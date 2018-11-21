---
layout:     post
title:      Kafka Producer请求处理机制(五)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。如有疑问，请在下方评论或邮件本人,本人邮箱: kongtrio@sina.com					https://blog.csdn.net/u013332124/article/details/82778419				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p></p><div class="toc"><h3>文章目录</h3><ul><ul><li><a href="#Kafka_Producer_2" rel="nofollow">Kafka Producer请求处理机制</a></li><ul><li><a href="#KafkaApis_8" rel="nofollow">KafkaApis</a></li><li><a href="#ReplicaManager_57" rel="nofollow">ReplicaManager</a></li><li><a href="#Partition_181" rel="nofollow">Partition</a></li><li><a href="#Log_230" rel="nofollow">Log</a></li><li><a href="#LogSegment_339" rel="nofollow">LogSegment</a></li></ul></ul></ul></div><p></p>
<h2><a id="Kafka_Producer_2"></a>Kafka Producer请求处理机制</h2>
<p><img src="http://on-img.com/chart_image/5b6ae176e4b08d3622a4d775.png?_=1534296790647" alt=""></p>
<p>Kafka在处理Producer请求时，主要涉及的组件有KafkaApis、ReplicaManager、Partition、Log、LogSegment。</p>
<h3><a id="KafkaApis_8"></a>KafkaApis</h3>
<p>kafka接收到producer请求后，通过其网络模型，最终会交给KafkaApis组件处理。</p>
<pre class=" language-scala"><code class="prism  language-scala">def handleProducerRequest(request: RequestChannel.Request) {
    val produceRequest = request.body.asInstanceOf[ProduceRequest]
    val numBytesAppended = request.header.sizeOf + produceRequest.sizeOf
    //判断客户端是否有对这些topic的Describe权限以及这些topic是否有效，然后分别划分到两个集合中去
    val (existingAndAuthorizedForDescribeTopics, nonExistingOrUnauthorizedForDescribeTopics) =
      produceRequest.partitionRecordsOrFail.asScala.partition { case (tp, _) =&gt;
        authorize(request.session, Describe, new Resource(auth.Topic, tp.topic)) &amp;&amp; metadataCache.contains(tp.topic)
      }
    //继续判断客户端是否对上面那些经过筛选的topic有写权限，然后分到两个集合中去。
    //后面在发送响应时会把nonExistingOrUnauthorizedForDescribeTopics和unauthorizedForWriteRequestInfo还给客户端,告诉客户端错误详情
    val (authorizedRequestInfo, unauthorizedForWriteRequestInfo) = existingAndAuthorizedForDescribeTopics.partition {
      case (tp, _) =&gt; authorize(request.session, Write, new Resource(auth.Topic, tp.topic))
    }

    //方法内嵌函数，发送响应给客户端
    def sendResponseCallback(responseStatus: Map[TopicPartition, PartitionResponse]) {
		//...
    }
    //如果没有一个topic通过上面的筛选，就发送响应给客户端
    if (authorizedRequestInfo.isEmpty)
      sendResponseCallback(Map.empty)
    else {
      //根据客户端的clientId判断是否可以操作内部的一些topic。这些内部topic指的是kafka内置的一些topic，比如__consumer_offsets
      val internalTopicsAllowed = request.header.clientId == AdminUtils.AdminClientId

      // 调用replicaManager组件的方法添加消息
      replicaManager.appendRecords(
        produceRequest.timeout.toLong,
        produceRequest.acks,
        internalTopicsAllowed,
        authorizedRequestInfo,
        sendResponseCallback)

      //帮助GC回收内存
      produceRequest.clearPartitionRecords()
    }
  }
</code></pre>
<p>kafkaApis做的事情其实很简单:</p>
<ol>
<li>过滤掉那些该客户端没有权限的topic还有那些不存在的topic(可能已经被删除了)</li>
<li>将过滤后的所有消息交给replicaManager处理</li>
</ol>
<h3><a id="ReplicaManager_57"></a>ReplicaManager</h3>
<p>replicaManager是kafka管理副本的组件。维护目标broker上各个topic的副本信息。</p>
<pre class=" language-scala"><code class="prism  language-scala">def appendRecords(timeout: Long,
                    requiredAcks: Short,
                    internalTopicsAllowed: Boolean,
                    entriesPerPartition: Map[TopicPartition, MemoryRecords],
                    responseCallback: Map[TopicPartition, PartitionResponse] =&gt; Unit) {
    //判断acks是否合法,acks只能是[-1,0,1]其中的一个数
    if (isValidRequiredAcks(requiredAcks)) {
      val sTime = time.milliseconds
      //添加日志
      val localProduceResults = appendToLocalLog(internalTopicsAllowed, entriesPerPartition, requiredAcks)
      debug("Produce to local log in %d ms".format(time.milliseconds - sTime))
      //
      val produceStatus = localProduceResults.map { case (topicPartition, result) =&gt;
        topicPartition -&gt;
                ProducePartitionStatus(
                  result.info.lastOffset + 1, // required offset
                  new PartitionResponse(result.error, result.info.firstOffset, result.info.logAppendTime)) // response status
      }
      //判断是否需要延迟响应客户端。通常acks=-1时，需要通知其他replica复制消息，保证复制完了才会响应客户端，这时候就需要使用延迟操作
      if (delayedRequestRequired(requiredAcks, entriesPerPartition, localProduceResults)) {
        // create delayed produce operation
        val produceMetadata = ProduceMetadata(requiredAcks, produceStatus)
        //建立一个延迟操作对象。超过timeout时间后这个操作会被认定为超时，并立刻返回，发送响应给客户端
        val delayedProduce = new DelayedProduce(timeout, produceMetadata, this, responseCallback)

        //遍历有哪些topic需要检查延迟操作是否完成
        val producerRequestKeys = entriesPerPartition.keys.map(new TopicPartitionOperationKey(_)).toSeq

        //尝试完成这个操作
        delayedProducePurgatory.tryCompleteElseWatch(delayedProduce, producerRequestKeys)

      } else {
        //立刻发送响应给客户端
        val produceResponseStatus = produceStatus.mapValues(status =&gt; status.responseStatus)
        responseCallback(produceResponseStatus)
      }
    } else {
      // acks错误，发送响应给客户端
      val responseStatus = entriesPerPartition.map { case (topicPartition, _) =&gt;
        topicPartition -&gt; new PartitionResponse(Errors.INVALID_REQUIRED_ACKS,
          LogAppendInfo.UnknownLogAppendInfo.firstOffset, Record.NO_TIMESTAMP)
      }
      responseCallback(responseStatus)
    }
  }

//添加日志
private def appendToLocalLog(internalTopicsAllowed: Boolean,
                               entriesPerPartition: Map[TopicPartition, MemoryRecords],
                               requiredAcks: Short): Map[TopicPartition, LogAppendResult] = {
    trace("Append [%s] to local log ".format(entriesPerPartition))
    entriesPerPartition.map { case (topicPartition, records) =&gt;
      BrokerTopicStats.getBrokerTopicStats(topicPartition.topic).totalProduceRequestRate.mark()
      BrokerTopicStats.getBrokerAllTopicsStats().totalProduceRequestRate.mark()

      // 如果要写的topic是内部的topic，但是客户端没有内部写权限，就直接返回客户端错误信息
      if (Topic.isInternal(topicPartition.topic) &amp;&amp; !internalTopicsAllowed) {
        (topicPartition, LogAppendResult(
          LogAppendInfo.UnknownLogAppendInfo,
          Some(new InvalidTopicException(s"Cannot append to internal topic ${topicPartition.topic}"))))
      } else {
        try {
          //获取具体的partition
          val partitionOpt = getPartition(topicPartition)
          val info = partitionOpt match {
            case Some(partition) =&gt;
              //调用partition的方法写入消息
              partition.appendRecordsToLeader(records, requiredAcks)
            case None =&gt; throw new UnknownTopicOrPartitionException("Partition %s doesn't exist on %d"
              .format(topicPartition, localBrokerId))
          }
          //计算写入的消息数量
          val numAppendedMessages =
            if (info.firstOffset == -1L || info.lastOffset == -1L)
              0
            else
              info.lastOffset - info.firstOffset + 1

          // update stats for successfully appended bytes and messages as bytesInRate and messageInRate
          BrokerTopicStats.getBrokerTopicStats(topicPartition.topic).bytesInRate.mark(records.sizeInBytes)
          BrokerTopicStats.getBrokerAllTopicsStats.bytesInRate.mark(records.sizeInBytes)
          BrokerTopicStats.getBrokerTopicStats(topicPartition.topic).messagesInRate.mark(numAppendedMessages)
          BrokerTopicStats.getBrokerAllTopicsStats.messagesInRate.mark(numAppendedMessages)

          trace("%d bytes written to log %s-%d beginning at offset %d and ending at offset %d"
            .format(records.sizeInBytes, topicPartition.topic, topicPartition.partition, info.firstOffset, info.lastOffset))
          (topicPartition, LogAppendResult(info))
        } catch {
          // NOTE: Failed produce requests metric is not incremented for known exceptions
          // it is supposed to indicate un-expected failures of a broker in handling a produce request
          case e: KafkaStorageException =&gt;
            fatal("Halting due to unrecoverable I/O error while handling produce request: ", e)
            Runtime.getRuntime.halt(1)
            (topicPartition, null)
          case e@ (_: UnknownTopicOrPartitionException |
                   _: NotLeaderForPartitionException |
                   _: RecordTooLargeException |
                   _: RecordBatchTooLargeException |
                   _: CorruptRecordException |
                   _: InvalidTimestampException) =&gt;
            (topicPartition, LogAppendResult(LogAppendInfo.UnknownLogAppendInfo, Some(e)))
          case t: Throwable =&gt;
            BrokerTopicStats.getBrokerTopicStats(topicPartition.topic).failedProduceRequestRate.mark()
            BrokerTopicStats.getBrokerAllTopicsStats.failedProduceRequestRate.mark()
            error("Error processing append operation on partition %s".format(topicPartition), t)
            (topicPartition, LogAppendResult(LogAppendInfo.UnknownLogAppendInfo, Some(t)))
        }
      }
    }
  }
</code></pre>
<p>ReplicaManager拿到请求内容后，主要做了如下事情</p>
<ol>
<li>校验ack是否合法，即ack是否在[-1,0,1]中</li>
<li>如果要写的topic是内部topic，比如__consumer_offsets</li>
<li>之后获取到具体要写的partition，把消息交给partition组件处理</li>
<li>partition组件写入成功后,ReplicaManager组件还会判断是否需要立即响应客户端。如果acks=-1的话，就需要保证一定数量的replica复制完毕后才可以响应客户端。</li>
</ol>
<h3><a id="Partition_181"></a>Partition</h3>
<p>partiton组件是topic在某个broker上一个副本的抽象。每个partition对象都会维护一个Replica对象，Replica对象中又维护Log对象，也就是数据目录的抽象。</p>
<pre class=" language-scala"><code class="prism  language-scala">def appendRecordsToLeader(records: MemoryRecords, requiredAcks: Int = 0) = {
    val (info, leaderHWIncremented) = inReadLock(leaderIsrUpdateLock) {
      leaderReplicaIfLocal match {
          //写之前先判断该replica是否是leader，如果不是leader则没有写权限
        case Some(leaderReplica) =&gt;
          //获取该partition的log对象
          val log = leaderReplica.log.get
          //获取配置的minInSyncReplicas值
          val minIsr = log.config.minInSyncReplicas
          //当前ISR的数量
          val inSyncSize = inSyncReplicas.size

          // 如果请求的acks=-1，但是当前的ISR比配置的minInSyncReplicas还小，那要抛出错误，表示当前ISR不足
          if (inSyncSize &lt; minIsr &amp;&amp; requiredAcks == -1) {
            throw new NotEnoughReplicasException("Number of insync replicas for partition %s is [%d], below required minimum [%d]"
              .format(topicPartition, inSyncSize, minIsr))
          }
          //调用log的append方法写入消息
          val info = log.append(records, assignOffsets = true)
          // probably unblock some follower fetch requests since log end offset has been updated
          replicaManager.tryCompleteDelayedFetch(TopicPartitionOperationKey(this.topic, this.partitionId))
          // we may need to increment high watermark since ISR could be down to 1
          (info, maybeIncrementLeaderHW(leaderReplica))

        case None =&gt;
          throw new NotLeaderForPartitionException("Leader not local for partition %s on broker %d"
            .format(topicPartition, localBrokerId))
      }
    }

    // some delayed operations may be unblocked after HW changed
    if (leaderHWIncremented)
      tryCompleteDelayedRequests()

    info
  }
</code></pre>
<p>parititon组件从replicaManager拿到消息后</p>
<ol>
<li>先判断自己是否是leader，只有leader才可以接收producer请求然后写数据</li>
<li>判断当前的当前的ISR数量是否比minInSyncReplicas还小，如果ISR数量小于minInSyncReplicas就抛出异常</li>
<li>把消息交给自己管理的Log组件处理</li>
</ol>
<h3><a id="Log_230"></a>Log</h3>
<p>Log对象是对partition数据目录的抽象。管理着某个topic在某个broker的一个partition,它可能是一个leader，也可能是replica。同时，Log对象还同时管理着多个LogSegment，也就是日志的分段。</p>
<pre class=" language-scala"><code class="prism  language-scala">def append(records: MemoryRecords, assignOffsets: Boolean = true): LogAppendInfo = {
    //判断消息格式是否正确.分析消息的压缩格式
    val appendInfo = analyzeAndValidateRecords(records)

    //如果没有一条消息格式正确，直接返回
    if (appendInfo.shallowCount == 0)
      return appendInfo

    //裁剪一些错误的数据
    var validRecords = trimInvalidBytes(records, appendInfo)

    try {
      lock synchronized {
        //需要给消息分配offset的话
        if (assignOffsets) {
          // 计算第一条消息的offset
          val offset = new LongRef(nextOffsetMetadata.messageOffset)
          appendInfo.firstOffset = offset.value
          val now = time.milliseconds
          val validateAndOffsetAssignResult = try {
            //给每一条消息设置offset。并且找出maxTimestamp以及maxTimestamp对于的offset
            LogValidator.validateMessagesAndAssignOffsets(validRecords,
                                                          offset,
                                                          now,
                                                          appendInfo.sourceCodec,
                                                          appendInfo.targetCodec,
                                                          config.compact,
                                                          config.messageFormatVersion.messageFormatVersion,
                                                          config.messageTimestampType,
                                                          config.messageTimestampDifferenceMaxMs)
          } catch {
            case e: IOException =&gt; throw new KafkaException("Error in validating messages while appending to log '%s'".format(name), e)
          }
          //获取有效的记录，然后根据这些记录设置响应的返回内容
          validRecords = validateAndOffsetAssignResult.validatedRecords
          //消息的最大时间戳和配置的messageTimestampType有关系。当前获取消息maxTimestamp时间戳的方式有两种。
          //1. 根据消息的timestamp来设置时间戳
          //2. 根据消息的写入时间来设置时间戳,也就是当前时间
          appendInfo.maxTimestamp = validateAndOffsetAssignResult.maxTimestamp
          appendInfo.offsetOfMaxTimestamp = validateAndOffsetAssignResult.shallowOffsetOfMaxTimestamp
          appendInfo.lastOffset = offset.value - 1
          if (config.messageTimestampType == TimestampType.LOG_APPEND_TIME)
            appendInfo.logAppendTime = now

          // 由于前面的操作可能导致消息压缩格式改变以及消息格式改变，因此这里还需要重新检查一下当前的每条消息大小是否超过maxMessageSize的配置大小
          if (validateAndOffsetAssignResult.messageSizeMaybeChanged) {
            for (logEntry &lt;- validRecords.shallowEntries.asScala) {
              if (logEntry.sizeInBytes &gt; config.maxMessageSize) {
                // we record the original message set size instead of the trimmed size
                // to be consistent with pre-compression bytesRejectedRate recording
                BrokerTopicStats.getBrokerTopicStats(topicPartition.topic).bytesRejectedRate.mark(records.sizeInBytes)
                BrokerTopicStats.getBrokerAllTopicsStats.bytesRejectedRate.mark(records.sizeInBytes)
                throw new RecordTooLargeException("Message size is %d bytes which exceeds the maximum configured message size of %d."
                  .format(logEntry.sizeInBytes, config.maxMessageSize))
              }
            }
          }

        } else {
          // we are taking the offsets we are given
          if (!appendInfo.offsetsMonotonic || appendInfo.firstOffset &lt; nextOffsetMetadata.messageOffset)
            throw new IllegalArgumentException("Out of order offsets found in " + records.deepEntries.asScala.map(_.offset))
        }

        // 要写入的消息大小不能比一个segment的大小还大
        if (validRecords.sizeInBytes &gt; config.segmentSize) {
          throw new RecordBatchTooLargeException("Message set size is %d bytes which exceeds the maximum configured segment size of %d."
            .format(validRecords.sizeInBytes, config.segmentSize))
        }

        // 判断是否需要新建一个segment
        val segment = maybeRoll(messagesSize = validRecords.sizeInBytes,
          maxTimestampInMessages = appendInfo.maxTimestamp,
          maxOffsetInMessages = appendInfo.lastOffset)


        //调用segment的方法添加消息
        segment.append(firstOffset = appendInfo.firstOffset,
          largestOffset = appendInfo.lastOffset,
          largestTimestamp = appendInfo.maxTimestamp,
          shallowOffsetOfMaxTimestamp = appendInfo.offsetOfMaxTimestamp,
          records = validRecords)

        updateLogEndOffset(appendInfo.lastOffset + 1)

        trace("Appended message set to log %s with first offset: %d, next offset: %d, and messages: %s"
          .format(this.name, appendInfo.firstOffset, nextOffsetMetadata.messageOffset, validRecords))
        //判断是否需要将消息刷盘
        if (unflushedMessages &gt;= config.flushInterval)
          flush()

        appendInfo
      }
    } catch {
      case e: IOException =&gt; throw new KafkaStorageException("I/O exception in append to log '%s'".format(name), e)
    }
  }
</code></pre>
<ol>
<li>Log组件拿到消息后，对消息内容进行校验以及裁剪，然后设置每一条消息的offset。还会计算当前这批消息的最大时间戳是多少，即maxTimestamp，这个时间戳和日志保留时间有着密切关系</li>
<li>之后还会校验要写入的消息数量是否大于一个segment所能容纳的最大限制，这个限制和配置<code>segment.bytes</code>有关系</li>
<li>写入到segment之前还要判断如果把这些消息写入segment，会不会导致segment超出<code>segment.bytes</code>的大小，如果会的话，要新建一个新的segment用于日志写入</li>
<li>把消息传过最新活跃的LogSegment处理</li>
</ol>
<h3><a id="LogSegment_339"></a>LogSegment</h3>
<p>LogSegment是partition目录中数据段的抽象，kafka会将一个副本中日志根据配置分段。这个LogSegment对象维护数据文件以及索引文件的信息。</p>
<pre class=" language-scala"><code class="prism  language-scala">def append(firstOffset: Long, largestOffset: Long, largestTimestamp: Long, shallowOffsetOfMaxTimestamp: Long, records: MemoryRecords) {
    if (records.sizeInBytes &gt; 0) {
      trace("Inserting %d bytes at offset %d at position %d with largest timestamp %d at shallow offset %d"
        .format(records.sizeInBytes, firstOffset, log.sizeInBytes(), largestTimestamp, shallowOffsetOfMaxTimestamp))
      //记录要插入的第一条消息的物理地址，后面生成索引的时候会用到
      val physicalPosition = log.sizeInBytes()
      if (physicalPosition == 0)
        rollingBasedTimestamp = Some(largestTimestamp)
      //判断是否可以安全写入消息
      require(canConvertToRelativeOffset(largestOffset), "largest offset in message set can not be safely converted to relative offset.")
      //将消息写入到fileChannel
      val appendedBytes = log.append(records)
      trace(s"Appended $appendedBytes to ${log.file()} at offset $firstOffset")
      //随时更新segment的maxTimestampSoFar值。
      // 这个时间的获取和message.timestamp.type配置有关系。可能获取当前时间作为largestTimestamp，也可能获取日志的最大timestamp作为largestTimestamp
      if (largestTimestamp &gt; maxTimestampSoFar) {
        maxTimestampSoFar = largestTimestamp
        offsetOfMaxTimestamp = shallowOffsetOfMaxTimestamp
      }
      //判断是否需要追加一条索引记录。当bytesSinceLastIndexEntry大于配置的indexIntervalBytes值时会追加新的所有记录
      //bytesSinceLastIndexEntry会随着消息的写入不断增加，直到生成一条新的索引记录后重置为0
      //indexIntervalBytes和broker的配置有关
      if (bytesSinceLastIndexEntry &gt; indexIntervalBytes) {
        index.append(firstOffset, physicalPosition)
        timeIndex.maybeAppend(maxTimestampSoFar, offsetOfMaxTimestamp)
        bytesSinceLastIndexEntry = 0
      }
      bytesSinceLastIndexEntry += records.sizeInBytes
    }
  }
</code></pre>
<ol>
<li>logSegment底层使用了fileChannel写日志,写完之后还会判断是否要更新当前logSegment的最大时间戳</li>
<li>每当写入消息的大小积累到一定程度时，会新插入一条索引记录。这个积累的大小和配置<code>index.interval.bytes</code>有关系</li>
</ol>
<p>kafka底层的写数据是根据fileChannel来写的，它写的时候不会立刻刷盘，而是开启了一个定时任务根据策略去刷盘。但是在默认情况下，这个定时任务又是不刷盘的（刷盘策略都不满足），kafka把刷盘的时机交给操作系统来掌控。</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>