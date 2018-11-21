---
layout:     post
title:      Spark Shuffle 核心组件BlockStoreShuffleReader内核原理深入剖析-Spark商业环境实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本套技术专栏是作者（秦凯新）平时工作的总结和升华，通过从真实商业环境抽取案例进行总结和分享，并给出商业应用的调优建议和集群环境容量规划等内容，请持续关注本套博客。版权声明：禁止转载，欢迎学习。QQ邮箱地址：1120746959@qq.com，如有任何商业交流，可随时联系。					https://blog.csdn.net/shenshouniu/article/details/83871017				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<ul>
<li><a href="https://juejin.im/post/5bd55b4de51d4579470247eb" rel="nofollow">Spark商业环境实战-Spark内置框架rpc通讯机制及RpcEnv基础设施 </a></li>
<li><a href="https://juejin.im/post/5bd583f0e51d452c296ef5dd" rel="nofollow">Spark商业环境实战-Spark事件监听总线流程分析</a></li>
<li><a href="https://juejin.im/post/5bd854955188255ca65da7ed" rel="nofollow">Spark商业环境实战-Spark存储体系底层架构剖析</a></li>
<li><a href="https://juejin.im/post/5bd8553af265da0adb311f58" rel="nofollow">Spark商业环境实战-Spark底层多个MessageLoop循环线程执行流程分析</a></li>
<li><a href="https://juejin.im/post/5bd88932f265da0ae5056199" rel="nofollow">Spark商业环境实战-Spark二级调度系统Stage划分算法和最佳任务调度细节剖析</a></li>
<li><a href="https://juejin.im/post/5bdc2676f265da611b57cd20" rel="nofollow">Spark商业环境实战-Spark任务延迟调度及调度池Pool架构剖析</a></li>
<li><a href="https://juejin.im/post/5bdd0a87f265da612859937d" rel="nofollow">Spark商业环境实战-Task粒度的缓存聚合排序结构AppendOnlyMap详细剖析</a></li>
<li><a href="https://juejin.im/post/5bdd3ac86fb9a049d7471f52" rel="nofollow">Spark商业环境实战-ExternalSorter 外部排序器在Spark Shuffle过程中设计思路剖析</a></li>
<li><a href="https://juejin.im/post/5be02cbfe51d4505551f961e" rel="nofollow">Spark商业环境实战-ShuffleExternalSorter外部排序器在Spark Shuffle过程中的设计思路剖析</a></li>
<li><a href="https://juejin.im/post/5be0329d6fb9a049b3476f32" rel="nofollow">Spark商业环境实战-Spark ShuffleManager内存缓冲器SortShuffleWriter设计思路剖析</a></li>
<li><a href="https://juejin.im/post/5be07638f265da61764a58df" rel="nofollow">Spark商业环境实战-Spark ShuffleManager内存缓冲器UnsafeShuffleWriter设计思路剖析</a></li>
<li><a href="https://juejin.im/post/5be06561e51d4505466cdfa2" rel="nofollow">Spark商业环境实战-Spark ShuffleManager内存缓冲器BypassMergeSortShuffleWriter设计思路剖析</a></li>
<li><a href="https://juejin.im/post/5be446e86fb9a049df238efa" rel="nofollow">Spark商业环境实战-Spark Shuffle 核心组件BlockStoreShuffleReader内核原理深入剖析</a></li>
<li><a href="https://juejin.im/post/5be429c751882516ba2913d3" rel="nofollow">Spark商业环境实战-Spark Shuffle 管理器SortShuffleManager内核原理深入剖析</a></li>
<li><a href="https://juejin.im/post/5bdb091ff265da392b2c51bf" rel="nofollow">Spark商业环境实战-StreamingContext启动流程及Dtream 模板源码剖析</a></li>
<li><a href="https://juejin.im/post/5bdb0e76e51d456c6f1cc45f" rel="nofollow">Spark商业环境实战-ReceiverTracker与BlockGenerator数据流接收过程剖析</a></li>
</ul>
<h2><a id="1_ShuffeManager_18"></a>1 从ShuffeManager讲起</h2>
<p>一张图我已经用过多次了，不要见怪，因为毕竟都是一个主题，有关shuffle的。英文注释已经很详细了，这里简单介绍一下：</p>
<ul>
<li>目前只有一个实现 SortShuffleManager。</li>
<li>SortShuffleManager依赖于ShuffleWriter提供服务，通过ShuffleWriter定义的规范，可以将MapTask的任务中间结果按照约束的规范持久化到磁盘。</li>
<li>SortShuffleManager总共有三个子类， UnsafeShuffleWriter，SortShuffleWriter ，BypassMergeSortShuffleWriter用于Shuffle的写。</li>
<li>SortShuffleManager使用BlockStoreShuffleReader用于Shuffle的读。</li>
<li>SortShuffleManager依赖于ShuffleHandle样例类，主要还是负责向Task传递Shuffle信息。一个是序列化，一个是确定何时绕开合并和排序的Shuffle路径。</li>
</ul>
<p>官方英文介绍如下：</p>
<pre><code>     * Pluggable interface for shuffle systems. A ShuffleManager is created in SparkEnv on the 
     * driver and on each executor, based on the spark.shuffle.manager setting. The driver 
     * registers shuffles with it, and executors (or tasks running locally in the driver) can ask * to read and write data.
     
     * NOTE: this will be instantiated by SparkEnv so its constructor can take a SparkConf and
     * boolean isDriver as parameters.
</code></pre>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/8/166f3ec1133c79b3?w=1219&amp;h=595&amp;f=png&amp;s=72288" alt=""></p>
<h2><a id="2__BlockStoreShuffleReader_read_40"></a>2  BlockStoreShuffleReader 的气吞山河（就一个read方法）</h2>
<p>reduce Task 最最核心的方法就是BlockStoreShuffleReader干嘛的呢？主要从MapTask输出的正式的唯一的Block文件中读取由起始分区和结束分区指定范围内的数据。开始之前，我们重点介绍一下成员变量，同时开启一段英文：</p>
<pre><code> * Fetches and reads the partitions in range [startPartition, endPartition) from a shuffle by
 * requesting them from other nodes' block stores。
</code></pre>
<ul>
<li>
<p>那么要想使用，构造器里面需要封装什么呢？</p>
<pre><code>  private[spark] class BlockStoreShuffleReader[K, C](
      handle: BaseShuffleHandle[K, _, C],
      startPartition: Int,
      endPartition: Int,
      context: TaskContext,
      serializerManager: SerializerManager = SparkEnv.get.serializerManager,
      blockManager: BlockManager = SparkEnv.get.blockManager,
      mapOutputTracker: MapOutputTracker = SparkEnv.get.mapOutputTracker)
    extends ShuffleReader[K, C] with Logging 
</code></pre>
</li>
<li>
<p>dep ：BaseShuffleHandle 通过样例类传入的handle.dependency,也即ShuffleDependency</p>
</li>
<li>
<p>read() 方法</p>
</li>
<li>
<p>mapOutputTracker : 即SparkEnv的子组件MapOuputTracker</p>
</li>
</ul>
<h2><a id="3_read__62"></a>3 read 方法核心思想讲解：</h2>
<ul>
<li>
<p>ShuffleBlockFetcherIterator：用于获取多个Block的迭代器，说白了就是调用 mapOutputTracker.getMapSizesByExecutorId(handle.shuffleId, startPartition, endPartition)的返回值 Seq[(BlockManagerId, Seq[(BlockId, Long)])]，传入地址序列后，经由该方法获取所有请求的数据迭代器。</p>
</li>
<li>
<p>getMapSizesByExecutorId：</p>
<pre><code> * Called from executors to get the server URIs and output sizes for each shuffle block that
 * needs to be read from a given range of map output partitions (startPartition is included but
 * endPartition is excluded from the range).
</code></pre>
</li>
<li>
<p>如果指定了dep.mapSideCombine,就会在ExternalOnlyMap中进行聚合，注意这个可不是AppendOnlyMap。ExternalOnlyMap则继承SizeTrackingAppendOnlyMap,所以没有排序输出迭代器的东西，只有聚合和缓冲的功能</p>
</li>
<li>
<p>wrappedStreams 表示获取的MapTask的Block数据<br>
override def read(): Iterator[Product2[K, C]] = {</p>
<pre><code>  val wrappedStreams = new ShuffleBlockFetcherIterator(
    context,
    blockManager.shuffleClient,
    blockManager,
    mapOutputTracker.getMapSizesByExecutorId(handle.shuffleId, startPartition, endPartition),
    serializerManager.wrapStream,
    // Note: we use getSizeAsMb when no suffix is provided for backwards compatibility
    SparkEnv.get.conf.getSizeAsMb("spark.reducer.maxSizeInFlight", "48m") * 1024 * 1024,
    SparkEnv.get.conf.getInt("spark.reducer.maxReqsInFlight", Int.MaxValue),
    SparkEnv.get.conf.get(config.REDUCER_MAX_BLOCKS_IN_FLIGHT_PER_ADDRESS),
    SparkEnv.get.conf.get(config.MAX_REMOTE_BLOCK_SIZE_FETCH_TO_MEM),
    SparkEnv.get.conf.getBoolean("spark.shuffle.detectCorrupt", true))

  val serializerInstance = dep.serializer.newInstance()

  // Create a key/value iterator for each stream
  val recordIter = wrappedStreams.flatMap { case (blockId, wrappedStream) =&gt;
    // Note: the asKeyValueIterator below wraps a key/value iterator inside of a
    // NextIterator. The NextIterator makes sure that close() is called on the
    // underlying InputStream when all records have been read.
    serializerInstance.deserializeStream(wrappedStream).asKeyValueIterator
  }

  // Update the context task metrics for each record read.
  val readMetrics = context.taskMetrics.createTempShuffleReadMetrics()
  val metricIter = CompletionIterator[(Any, Any), Iterator[(Any, Any)]](
    recordIter.map { record =&gt;
      readMetrics.incRecordsRead(1)
      record
    },
    context.taskMetrics().mergeShuffleReadMetrics())

  // An interruptible iterator must be used here in order to support task cancellation
  val interruptibleIter = new InterruptibleIterator[(Any, Any)](context, metricIter)



  -----------------------------------神来之笔（聚合或缓冲）-----------------------------------
  val aggregatedIter: Iterator[Product2[K, C]] = if (dep.aggregator.isDefined) {
    if (dep.mapSideCombine) {
      // We are reading values that are already combined
      val combinedKeyValuesIterator = interruptibleIter.asInstanceOf[Iterator[(K, C)]]
      dep.aggregator.get.combineCombinersByKey(combinedKeyValuesIterator, context)
    } else {
      // We don't know the value type, but also don't care -- the dependency *should*
      // have made sure its compatible w/ this aggregator, which will convert the value
      // type to the combined type C
      val keyValuesIterator = interruptibleIter.asInstanceOf[Iterator[(K, Nothing)]]
      dep.aggregator.get.combineValuesByKey(keyValuesIterator, context)
    }
--------------------------------------------------------------------------------------------   
    
  } else {
    require(!dep.mapSideCombine, "Map-side combine without Aggregator specified!")
    interruptibleIter.asInstanceOf[Iterator[Product2[K, C]]]
  }

  // Sort the output if there is a sort ordering defined.
  dep.keyOrdering match {
    case Some(keyOrd: Ordering[K]) =&gt;
      // Create an ExternalSorter to sort the data.
      val sorter =
        new ExternalSorter[K, C, C](context, ordering = Some(keyOrd), serializer = dep.serializer)
  
  
  ===========================如果需要全局排序调用sorter.insertAll=========================
    sorter.insertAll(aggregatedIter)  &lt;= 构造器没有聚合器传入，所以使用PartitionedPairBuffer做缓冲
  ========================================================================================
  
  
  
      context.taskMetrics().incMemoryBytesSpilled(sorter.memoryBytesSpilled)
      context.taskMetrics().incDiskBytesSpilled(sorter.diskBytesSpilled)
      context.taskMetrics().incPeakExecutionMemory(sorter.peakMemoryUsedBytes)
      
      
      
     ====================如果需要全局排序，直接给出排序迭代器=========================
      CompletionIterator[Product2[K, C], Iterator[Product2[K, C]]](sorter.iterator, sorter.stop())         &lt;=神来之笔，sorter.iterator直接给出排序迭代器=
     =========================================================================================

      
    case None =&gt;
     ====================如果不需要全局排序，直接给出缓冲区数据迭代器=========================
      aggregatedIter
     =========================================================================================
      
  }
}
</code></pre>
<p>}</p>
</li>
</ul>
<h2><a id="3_ExternalSorter_166"></a>3 深度剖析一下ExternalSorter</h2>
<ul>
<li>
<p>构造器变量（aggregator，ordering）：</p>
<pre><code>private[spark] class ExternalSorter[K, V, C](
  context: TaskContext,
  aggregator: Option[Aggregator[K, V, C]] = None,
  partitioner: Option[Partitioner] = None,
  ordering: Option[Ordering[K]] = None,
  serializer: Serializer = SparkEnv.get.serializer)
extends Spillable[WritablePartitionedPairCollection[K, C]](context.taskMemoryManager())
with Logging 
</code></pre>
</li>
<li>
<p>insertAll 方法，发现 aggregator.isDefined若没有定义，则会使用PartitionedPairBuffer做缓冲，另外注意的是插入操作最多只会聚合。所以插入会很快，因为没有排序。若要全局排序，就要调用iterator读数据时才会全局排序并给出迭代器。内部为<br>
groupByPartition(destructiveIterator(<br>
collection.partitionedDestructiveSortedIterator(Some(keyComparator))))</p>
</li>
</ul>
<h4><a id="collectionPartitionedAppendOnlyMapPartitionedPairBuffer_182"></a>注意：collection其实为PartitionedAppendOnlyMap或者为PartitionedPairBuffer</h4>
<pre><code>    def insertAll(records: Iterator[Product2[K, V]]): Unit = {
        // TODO: stop combining if we find that the reduction factor isn't high
        val shouldCombine = aggregator.isDefined
    
        if (shouldCombine) {
          // Combine values in-memory first using our AppendOnlyMap
          val mergeValue = aggregator.get.mergeValue
          val createCombiner = aggregator.get.createCombiner
          var kv: Product2[K, V] = null
          val update = (hadValue: Boolean, oldValue: C) =&gt; {
            if (hadValue) mergeValue(oldValue, kv._2) else createCombiner(kv._2)
          }
          while (records.hasNext) {
            addElementsRead()
            kv = records.next()
            map.changeValue((getPartition(kv._1), kv._1), update)
            maybeSpillCollection(usingMap = true)
          }
        } else {
          // Stick values into our buffer
          while (records.hasNext) {
            addElementsRead()
            val kv = records.next()
            buffer.insert(getPartition(kv._1), kv._1, kv._2.asInstanceOf[C])
            maybeSpillCollection(usingMap = false)
          }
        }
      }
</code></pre>
<ul>
<li>
<p>sorter.iterator ：进一步调用ExternalSorter.partitionedIterator方法</p>
<pre><code>  def iterator: Iterator[Product2[K, C]] = {
  isShuffleSort = false
  partitionedIterator.flatMap(pair =&gt; pair._2)
}
</code></pre>
</li>
<li>
<p>ExternalSorter.partitionedIterator：根据ordering.isDefined来最终调用ExternalSorter.groupByPartition， 最终实现了按照分区读数据时，按照Key排序输出。</p>
<pre><code>  def partitionedIterator: Iterator[(Int, Iterator[Product2[K, C]])] = {
      val usingMap = aggregator.isDefined
      val collection: WritablePartitionedPairCollection[K, C] = if (usingMap) map else buffer
      if (spills.isEmpty) {
        // Special case: if we have only in-memory data, we don't need to merge streams, and perhaps
        // we don't even need to sort by anything other than partition ID
        if (!ordering.isDefined) {
        
       ------------------------------神来之笔（无排序迭代器）----------------------------------
          // The user hasn't requested sorted keys, so only sort by partition ID, not key
          groupByPartition(destructiveIterator(collection.partitionedDestructiveSortedIterator(None)
          
          ))
        } else {
        
         ------------------------------神来之笔（排序并输出迭代器）--------------------------------
          // We do need to sort by both partition ID and key
          groupByPartition(destructiveIterator(
            collection.partitionedDestructiveSortedIterator(Some(keyComparator))))
        }
      } else {
        // Merge spilled and in-memory data
        merge(spills, destructiveIterator(
          collection.partitionedDestructiveSortedIterator(comparator)))
      }
    }
</code></pre>
</li>
</ul>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/9/166f429c20df816b?w=1497&amp;h=560&amp;f=png&amp;s=119792" alt=""></p>
<h2><a id="3_ShuffleBlockFetcherIterator_251"></a>3 ShuffleBlockFetcherIterator方法核心思想讲解：</h2>
<p>ShuffleBlockFetcherIterator会通过splitLocalRemoteBlocks划分数据的读取策略：如果在本地有，那么可以直接从BlockManager中获取数据；如果需要从其他的节点上获取，那么需要走网络。</p>
<h2><a id="31__254"></a>3.1 重量级成员</h2>
<p>localBlocks：本地<br>
remoteBlocks：远程<br>
results：请求成功或失败 SuccessFetchResult</p>
<pre><code>      /** Local blocks to fetch, excluding zero-sized blocks. */
      localBlocks：private[this] val 
      = new ArrayBuffer[BlockId]()
    
      /** Remote blocks to fetch, excluding zero-sized blocks. */
      private[this] val remoteBlocks = new HashSet[BlockId]()
    
      /**
       * A queue to hold our results. This turns the asynchronous model provided by
       * [[org.apache.spark.network.BlockTransferService]] into a synchronous model (iterator).
       */
      private[this] val results = new LinkedBlockingQueue[FetchResult]
</code></pre>
<h2><a id="32_initialize_ShuffleBlockFetcherIterator_273"></a>3.2 initialize() 在ShuffleBlockFetcherIterator初始化时执行</h2>
<p>可以看到核心方法有：</p>
<ul>
<li>
<p>splitLocalRemoteBlocks：划分本地和远程读取Block请求 ，本地的放在localBlocks</p>
</li>
<li>
<p>fetchUpToMaxBytes：发送sendRequest请求，远程拉取数据。</p>
</li>
<li>
<p>fetchLocalBlocks：调用本地的BlockManager来读取数据。</p>
<pre><code>private[this] def initialize(): Unit = {
  // Add a task completion callback (called in both success case and failure case) to cleanup.
  context.addTaskCompletionListener(_ =&gt; cleanup())

  // Split local and remote blocks.
  val remoteRequests = splitLocalRemoteBlocks()
  // Add the remote requests into our queue in a random order
  fetchRequests ++= Utils.randomize(remoteRequests)
  assert ((0 == reqsInFlight) == (0 == bytesInFlight),
    "expected reqsInFlight = 0 but found reqsInFlight = " + reqsInFlight +
    ", expected bytesInFlight = 0 but found bytesInFlight = " + bytesInFlight)

  // Send out initial requests for blocks, up to our maxBytesInFlight
  fetchUpToMaxBytes()

  val numFetches = remoteRequests.size - fetchRequests.size
  logInfo("Started " + numFetches + " remote fetches in" + Utils.getUsedTimeMs(startTime))

  // Get Local Blocks
  fetchLocalBlocks()
  logDebug("Got local blocks in " + Utils.getUsedTimeMs(startTime))
}
</code></pre>
</li>
</ul>
<h2><a id="33_splitLocalRemoteBlocks_303"></a>3.3 splitLocalRemoteBlocks</h2>
<pre><code>private[this] def splitLocalRemoteBlocks(): ArrayBuffer[FetchRequest] = {
    // Make remote requests at most maxBytesInFlight / 5 in length; the reason to keep them
    // smaller than maxBytesInFlight is to allow multiple, parallel fetches from up to 5
    // nodes, rather than blocking on reading output from one node.
    val targetRequestSize = math.max(maxBytesInFlight / 5, 1L)
    logDebug("maxBytesInFlight: " + maxBytesInFlight + ", targetRequestSize: " + targetRequestSize
      + ", maxBlocksInFlightPerAddress: " + maxBlocksInFlightPerAddress)

    // Split local and remote blocks. Remote blocks are further split into FetchRequests of size
    // at most maxBytesInFlight in order to limit the amount of data in flight.
    val remoteRequests = new ArrayBuffer[FetchRequest]

    // Tracks total number of blocks (including zero sized blocks)
    var totalBlocks = 0
    for ((address, blockInfos) &lt;- blocksByAddress) {
      totalBlocks += blockInfos.size
      if (address.executorId == blockManager.blockManagerId.executorId) {
        // Filter out zero-sized blocks
        localBlocks ++= blockInfos.filter(_._2 != 0).map(_._1)
        numBlocksToFetch += localBlocks.size
      } else {
        val iterator = blockInfos.iterator
        var curRequestSize = 0L
        var curBlocks = new ArrayBuffer[(BlockId, Long)]
        while (iterator.hasNext) {
          val (blockId, size) = iterator.next()
          // Skip empty blocks
          if (size &gt; 0) {
            curBlocks += ((blockId, size))
            remoteBlocks += blockId
            numBlocksToFetch += 1
            curRequestSize += size
          } else if (size &lt; 0) {
            throw new BlockException(blockId, "Negative block size " + size)
          }
          if (curRequestSize &gt;= targetRequestSize ||
              curBlocks.size &gt;= maxBlocksInFlightPerAddress) {
            // Add this FetchRequest
            remoteRequests += new FetchRequest(address, curBlocks)
            logDebug(s"Creating fetch request of $curRequestSize at $address "
              + s"with ${curBlocks.size} blocks")
            curBlocks = new ArrayBuffer[(BlockId, Long)]
            curRequestSize = 0
          }
        }
        // Add in the final request
        if (curBlocks.nonEmpty) {
          remoteRequests += new FetchRequest(address, curBlocks)
        }
      }
    }
    logInfo(s"Getting $numBlocksToFetch non-empty blocks out of $totalBlocks blocks")
    remoteRequests
  }
</code></pre>
<h2><a id="34_fetchUpToMaxBytes_360"></a>3.4 fetchUpToMaxBytes</h2>
<pre><code>private def fetchUpToMaxBytes(): Unit = {
    // Send fetch requests up to maxBytesInFlight. If you cannot fetch from a remote host
    // immediately, defer the request until the next time it can be processed.

    // Process any outstanding deferred fetch requests if possible.
    if (deferredFetchRequests.nonEmpty) {
      for ((remoteAddress, defReqQueue) &lt;- deferredFetchRequests) {
        while (isRemoteBlockFetchable(defReqQueue) &amp;&amp;
            !isRemoteAddressMaxedOut(remoteAddress, defReqQueue.front)) {
          val request = defReqQueue.dequeue()
          logDebug(s"Processing deferred fetch request for $remoteAddress with "
            + s"${request.blocks.length} blocks")
          send(remoteAddress, request)
          if (defReqQueue.isEmpty) {
            deferredFetchRequests -= remoteAddress
          }
        }
      }
    }
</code></pre>
<ul>
<li>
<p>发送远程读取读取shuffleClient.fetchBlocks请求读取数据</p>
<pre><code> private[this] def sendRequest(req: FetchRequest) {
     logDebug("Sending request for %d blocks (%s) from %s".format(
       req.blocks.size, Utils.bytesToString(req.size), req.address.hostPort))
     bytesInFlight += req.size
     reqsInFlight += 1
 
     // so we can look up the size of each blockID
     val sizeMap = req.blocks.map { case (blockId, size) =&gt; (blockId.toString, size) }.toMap
     val remainingBlocks = new HashSet[String]() ++= sizeMap.keys
     val blockIds = req.blocks.map(_._1.toString)
     val address = req.address
 
     val blockFetchingListener = new BlockFetchingListener {
       override def onBlockFetchSuccess(blockId: String, buf: ManagedBuffer): Unit = {
         // Only add the buffer to results queue if the iterator is not zombie,
         // i.e. cleanup() has not been called yet.
         ShuffleBlockFetcherIterator.this.synchronized {
           if (!isZombie) {
             // Increment the ref count because we need to pass this to a different thread.
             // This needs to be released after use.
             buf.retain()
             remainingBlocks -= blockId
             results.put(new SuccessFetchResult(BlockId(blockId), address, sizeMap(blockId), buf,
               remainingBlocks.isEmpty))
             logDebug("remainingBlocks: " + remainingBlocks)
           }
         }
         logTrace("Got remote block " + blockId + " after " + Utils.getUsedTimeMs(startTime))
       }
 
       override def onBlockFetchFailure(blockId: String, e: Throwable): Unit = {
         logError(s"Failed to get block(s) from ${req.address.host}:${req.address.port}", e)
         results.put(new FailureFetchResult(BlockId(blockId), address, e))
       }
     }
 
     // Fetch remote shuffle blocks to disk when the request is too large. Since the shuffle data is
     // already encrypted and compressed over the wire(w.r.t. the related configs), we can just fetch
     // the data and write it to file directly.
     if (req.size &gt; maxReqSizeShuffleToMem) {
       shuffleClient.fetchBlocks(address.host, address.port, address.executorId, blockIds.toArray,
         blockFetchingListener, this)
     } else {
       shuffleClient.fetchBlocks(address.host, address.port, address.executorId, blockIds.toArray,
         blockFetchingListener, null)
     }
   }
</code></pre>
</li>
</ul>
<h2><a id="35_fetchLocalBlocks_434"></a>3.5 fetchLocalBlocks()</h2>
<pre><code> private[this] def fetchLocalBlocks() {
    val iter = localBlocks.iterator
    while (iter.hasNext) {
      val blockId = iter.next()
      try {
        val buf = blockManager.getBlockData(blockId)
        shuffleMetrics.incLocalBlocksFetched(1)
        shuffleMetrics.incLocalBytesRead(buf.size)
        buf.retain()
        results.put(new SuccessFetchResult(blockId, blockManager.blockManagerId, 0, buf, false))
      } catch {
        case e: Exception =&gt;
          // If we see an exception, stop immediately.
          logError(s"Error occurred while fetching local blocks", e)
          results.put(new FailureFetchResult(blockId, blockManager.blockManagerId, e))
          return
      }
    }
  }
</code></pre>
<h2><a id="40__456"></a>4.0 总结</h2>
<ul>
<li>ShuffleBlockFetcherIterator 依托于BlockStoreShuffleReader，得到Maptask的所有Block块数据的迭代器，</li>
<li>写入ExternalOnlyMap，并进行了缓冲聚合。</li>
<li>然后写入map或者buffer的缓冲，最后根据使用全局排序，则使用Sorter.iterator得到最终有序数据。</li>
<li>没有使用全局排序直接返回ExternalOnlyMap的迭代器。</li>
</ul>
<p>秦凯新 于深圳</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>