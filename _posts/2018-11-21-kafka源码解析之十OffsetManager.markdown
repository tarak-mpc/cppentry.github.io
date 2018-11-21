---
layout:     post
title:      kafka源码解析之十OffsetManager
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/wl044090432/article/details/51038600				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>OffsetManager主要提供对offset的保存和读取，kafka管理topic的偏移量有2种方式：1）zookeeper，即把偏移量提交至zk上；2）kafka，即把偏移量提交至kafka内部，主要由offsets.storage参数决定，默认为zookeeper。也就是说如果配置offsets.storage= kafka，则kafka会把这种offsetcommit请求转变为一种Producer，保存至topic为“__consumer_offsets”的log里面。</p>
<pre style="background:#FFFFFF;">查看OffsetManager类：</pre>
<pre style="background:#FFFFFF;"></pre><pre><code class="language-plain">class OffsetManager(val config: OffsetManagerConfig,
                    replicaManager: ReplicaManager,
                    zkClient: ZkClient,
                    scheduler: Scheduler) extends Logging with KafkaMetricsGroup {

  /* offsets and metadata cache */
//通过offsetsCache提供对GroupTopicPartition的查询
  private val offsetsCache = new Pool[GroupTopicPartition, OffsetAndMetadata]
  //把过时的偏移量刷入磁盘，因为这些偏移量长时间没有被更新，意味着消费者可能不再消费了，也就不需要了，因此刷入到磁盘
  scheduler.schedule(name = "offsets-cache-compactor",
                     fun = compact,
                     period = config.offsetsRetentionCheckIntervalMs,
                     unit = TimeUnit.MILLISECONDS)
……
}</code></pre><br><pre style="background:#FFFFFF;">主要完成2件事情：</pre><pre style="background:#FFFFFF;">           1）提供对topic偏移量的查询</pre><pre style="background:#FFFFFF;">           2）将偏移量消息刷入到以__consumer_offsets命名的topic的log中</pre><h2>10.1 offsetsCache的更新机制</h2><div><pre style="background:#FFFFFF;">那么offsetsCache是如何生成的呢？是通过producer端发送消息给leader，然后leader不断更新此偏移量。Leader更新此偏移量分3种情况：</pre><pre style="background:#FFFFFF;">1）当produceRequest.requiredAcks == 0时，即不需要ack，则立刻调用putOffsets更新偏移量</pre><pre style="background:#FFFFFF;">2）当produceRequest.requiredAcks == 1时，即需要立即返回response时，则立刻调用putOffsets更新偏移量</pre><pre style="background:#FFFFFF;">3）当produceRequest.requiredAcks == -1时，即只有此批消息达到最小副本数的时候，通过ProducerRequestPurgatory<span style="background-color:rgb(240,240,240);font-family:Arial, Helvetica, sans-serif;">触发调用putOffsets更新偏移量 （ProducerRequestPurgatory之后的章节会讲）</span></pre><img src="https://img-blog.csdn.net/20160401234447203?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div><h2>10.2 compact机制</h2><div><p>那么compact是如何工作的呢？</p><br></div><div><pre><code class="language-plain">//去除offsetsCache过时的OffsetAndMetadata，并把偏移量刷入磁盘
private def compact() {
  debug("Compacting offsets cache.")
  val startMs = SystemTime.milliseconds
//过滤出长时间没有被更新的offset
  val staleOffsets = offsetsCache.filter(startMs - _._2.timestamp &gt; config.offsetsRetentionMs)
  debug("Found %d stale offsets (older than %d ms).".format(staleOffsets.size, config.offsetsRetentionMs))
  // delete the stale offsets from the table and generate tombstone messages to remove them from the log
  val tombstonesForPartition = staleOffsets.map { case(groupTopicAndPartition, offsetAndMetadata) =&gt;
    val offsetsPartition = partitionFor(groupTopicAndPartition.group)
    trace("Removing stale offset and metadata for %s: %s".format(groupTopicAndPartition, offsetAndMetadata))
    offsetsCache.remove(groupTopicAndPartition)
    val commitKey = OffsetManager.offsetCommitKey(groupTopicAndPartition.group,
      groupTopicAndPartition.topicPartition.topic, groupTopicAndPartition.topicPartition.partition)
    (offsetsPartition, new Message(bytes = null, key = commitKey))
  }.groupBy{ case (partition, tombstone) =&gt; partition }
  // Append the tombstone messages to the offset partitions. It is okay if the replicas don't receive these (say,
  // if we crash or leaders move) since the new leaders will get rid of stale offsets during their own purge cycles.
  val numRemoved = tombstonesForPartition.flatMap { case(offsetsPartition, tombstones) =&gt;
    val partitionOpt = replicaManager.getPartition(OffsetManager.OffsetsTopicName, offsetsPartition)
    partitionOpt.map { partition =&gt;
      val appendPartition = TopicAndPartition(OffsetManager.OffsetsTopicName, offsetsPartition)
      val messages = tombstones.map(_._2).toSeq
      trace("Marked %d offsets in %s for deletion.".format(messages.size, appendPartition))
      try {
//把偏移量刷入磁盘，供kafka重启的时候读取，即loadOffsetsFromLog
        partition.appendMessagesToLeader(new ByteBufferMessageSet(config.offsetsTopicCompressionCodec, messages:_*))
        tombstones.size
      }
      catch {
        case t: Throwable =&gt;
          error("Failed to mark %d stale offsets for deletion in %s.".format(messages.size, appendPartition), t)
          // ignore and continue
          0
      }
    }
  }.sum
  debug("Removed %d stale offsets in %d milliseconds.".format(numRemoved, SystemTime.milliseconds - startMs))
}</code></pre><br><p>其实就是把不再有消息发送的topic的偏移量刷入到磁盘，并且leader在重启的时候可以调用loadOffsetsFromLog从磁盘加载偏移量。</p></div>
            </div>
                </div>