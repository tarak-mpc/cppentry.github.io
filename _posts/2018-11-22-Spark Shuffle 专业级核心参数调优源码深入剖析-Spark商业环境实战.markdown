---
layout:     post
title:      Spark Shuffle 专业级核心参数调优源码深入剖析-Spark商业环境实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本套技术专栏是作者（秦凯新）平时工作的总结和升华，通过从真实商业环境抽取案例进行总结和分享，并给出商业应用的调优建议和集群环境容量规划等内容，请持续关注本套博客。版权声明：禁止转载，欢迎学习。QQ邮箱地址：1120746959@qq.com，如有任何商业交流，可随时联系。					https://blog.csdn.net/shenshouniu/article/details/84037500				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>本套系列博客从真实商业环境抽取案例进行总结和分享，并给出Spark商业应用实战指导，请持续关注本套博客。版权声明：本套Spark商业应用实战归作者（秦凯新）所有，禁止转载，欢迎学习。</p>
<ul>
<li><a href="https://juejin.im/post/5bd55b4de51d4579470247eb" rel="nofollow">Spark商业环境实战-Spark内置框架rpc通讯机制及RpcEnv基础设施 </a></li>
<li><a href="https://juejin.im/post/5bd583f0e51d452c296ef5dd" rel="nofollow">Spark商业环境实战-Spark事件监听总线流程分析</a></li>
<li><a href="https://juejin.im/post/5bd854955188255ca65da7ed" rel="nofollow">Spark商业环境实战-Spark存储体系底层架构剖析</a></li>
<li><a href="https://juejin.im/post/5bd8553af265da0adb311f58" rel="nofollow">Spark商业环境实战-Spark底层多个MessageLoop循环线程执行流程分析</a></li>
<li><a href="https://blog.csdn.net/shenshouniu/article/details/83962930" rel="nofollow">Spark商业环境实战-Spark一级资源调度Shedule机制及SpreadOut模式源码深入剖析</a></li>
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
<li><a href="https://juejin.im/post/5be96a2fe51d454e0a1cc3a2" rel="nofollow">Spark商业环境实战-Spark PersistenceEngine持久化引擎与领导选举代理机制内核原理深入剖析</a></li>
<li><a href="https://juejin.im/post/5beab66ce51d4547e432dd5c" rel="nofollow">Spark商业环境实战-Spark Shuffle专业级核心参数调优源码深入剖析 </a></li>
<li>[Spark商业环境实战-Spark 内存管理体系UnifiedMemoryManager源码深入剖析 ]</li>
<li>[Spark商业环境实战-Spark 内存管理体系StaticMemoryManager源码深入剖析 ]</li>
<li>[Spark商业环境实战-Spark 基于JVM 统一内存使用内幕源码深入剖析 ]</li>
<li>[Spark商业环境实战-Spark 基于Tungsten内存分配器的管理机制内幕源码深入剖析 ]</li>
<li>[Spark商业环境实战-Spark 基于Task粒度的内存管理器及内存消费者源码深入剖析]</li>
<li>[Spark商业环境实战-Spark Shuffle  Block 索引与数据解析组件IndexShuffleBlocakResolver源码深入剖析 ]</li>
<li>[Spark商业环境实战-Spark Block数据传输服务NettyBlockTransferService 源码深入剖析 ]</li>
<li>[Spark商业环境实战-Spark 基于Yarn的资源申请过程源码深入剖析 ]</li>
<li>[Spark商业环境实战-Spark 基于K8s的资源申请流程源码深入剖析 ]</li>
</ul>
<h2><a id="1_Spark_30"></a>1 Spark运行资源优化配置</h2>
<pre><code>    ./bin/spark-submit \  
    --master yarn-cluster \  
    --num-executors 100 \  
    --executor-memory 6G \ 
    --executor-cores 4 \
    --driver-memory 1G \
    --conf spark.default.parallelism=1000 \
    --conf spark.storage.memoryFraction=0.5 \  
    --conf spark.shuffle.memoryFraction=0.3 \
</code></pre>
<h2><a id="2_Spark_41"></a>2 Spark运行资源优化配置</h2>
<hr>
<ul>
<li>
<p>spark.reducer.maxSizeInFlight</p>
</li>
<li>
<p>默认值：48m</p>
</li>
<li>
<p>参数说明：该参数用于设置shuffle read task的buffer缓冲大小，而这个buffer缓冲决定了每次能够拉取多少数据。</p>
</li>
<li>
<p>调优建议：如果作业可用的内存资源较为充足的话，可以适当增加这个参数的大小（比如96m），从而减少拉取数据的次数，也就可以减少网络传输的次数，进而提升性能。在实践中发现，合理调节该参数，性能会有1%~5%的提升。</p>
<pre><code>   * Fetches and reads the partitions in range [startPartition, endPartition) from a shuffle by
   * requesting them from other nodes' block stores.

  private[spark] class BlockStoreShuffleReader[K, C](
      handle: BaseShuffleHandle[K, _, C],
      startPartition: Int,
      endPartition: Int,
      context: TaskContext,
      serializerManager: SerializerManager = SparkEnv.get.serializerManager,
      blockManager: BlockManager = SparkEnv.get.blockManager,
      mapOutputTracker: MapOutputTracker = SparkEnv.get.mapOutputTracker)
    extends ShuffleReader[K, C] with Logging {
  
    private val dep = handle.dependency
  
    /** Read the combined key-values for this reduce task */
    override def read(): Iterator[Product2[K, C]] = {
    
      val wrappedStreams = new ShuffleBlockFetcherIterator(
        context,
        blockManager.shuffleClient,
        blockManager,
        mapOutputTracker.getMapSizesByExecutorId(handle.shuffleId, startPartition, endPartition),
        serializerManager.wrapStream,
        // Note: we use getSizeAsMb when no suffix is provided for backwards compatibility
        
        SparkEnv.get.conf.getSizeAsMb("spark.reducer.maxSizeInFlight", "48m") * 1024 * 1024,       &lt;=神来之笔
        
        SparkEnv.get.conf.getInt("spark.reducer.maxReqsInFlight", Int.MaxValue),
        SparkEnv.get.conf.get(config.REDUCER_MAX_BLOCKS_IN_FLIGHT_PER_ADDRESS),
        SparkEnv.get.conf.get(config.MAX_REMOTE_BLOCK_SIZE_FETCH_TO_MEM),
        SparkEnv.get.conf.getBoolean("spark.shuffle.detectCorrupt", true))
</code></pre>
</li>
</ul>
<hr>
<ul>
<li>
<p>spark.shuffle.io.maxRetries</p>
</li>
<li>
<p>默认值：3</p>
</li>
<li>
<p>参数说明：shuffle read task从shuffle write task所在节点拉取属于自己的数据时，如果因为网络异常导致拉取失败，是会自动进行重试的。该参数就代表了可以重试的最大次数。如果在指定次数之内拉取还是没有成功，就可能会导致作业执行失败。</p>
</li>
<li>
<p>调优建议：对于那些包含了特别耗时的shuffle操作的作业，建议增加重试最大次数（比如60次），以避免由于JVM的full gc或者网络不稳定等因素导致的数据拉取失败。在实践中发现，对于针对超大数据量（数十亿~上百亿）的shuffle过程，调节该参数可以大幅度提升稳定性。</p>
<pre><code>public TransportConf(String module, ConfigProvider conf) {
  this.module = module;
  this.conf = conf;
  SPARK_NETWORK_IO_MODE_KEY = getConfKey("io.mode");
  SPARK_NETWORK_IO_PREFERDIRECTBUFS_KEY = getConfKey("io.preferDirectBufs");
  SPARK_NETWORK_IO_CONNECTIONTIMEOUT_KEY = getConfKey("io.connectionTimeout");
  SPARK_NETWORK_IO_BACKLOG_KEY = getConfKey("io.backLog");
  SPARK_NETWORK_IO_NUMCONNECTIONSPERPEER_KEY =  getConfKey("io.numConnectionsPerPeer");
  SPARK_NETWORK_IO_SERVERTHREADS_KEY = getConfKey("io.serverThreads");
  SPARK_NETWORK_IO_CLIENTTHREADS_KEY = getConfKey("io.clientThreads");
  SPARK_NETWORK_IO_RECEIVEBUFFER_KEY = getConfKey("io.receiveBuffer");
  SPARK_NETWORK_IO_SENDBUFFER_KEY = getConfKey("io.sendBuffer");
  SPARK_NETWORK_SASL_TIMEOUT_KEY = getConfKey("sasl.timeout");
  SPARK_NETWORK_IO_MAXRETRIES_KEY = getConfKey("io.maxRetries");
  SPARK_NETWORK_IO_RETRYWAIT_KEY = getConfKey("io.retryWait");
  SPARK_NETWORK_IO_LAZYFD_KEY = getConfKey("io.lazyFD");
  SPARK_NETWORK_VERBOSE_METRICS = getConfKey("io.enableVerboseMetrics");
}
</code></pre>
</li>
</ul>
<hr>
<ul>
<li>spark.shuffle.io.retryWait</li>
<li>默认值：5s</li>
<li>参数说明： shuffle read task从shuffle write task所在节点拉取属于自己的数据时，如果因为网络异常导致拉取失败，是会自动进行重试的，该参数代表了每次重试拉取数据的等待间隔，默认是5s。</li>
<li>调优建议：建议加大间隔时长（比如60s），以增加shuffle操作的稳定性。</li>
</ul>
<hr>
<ul>
<li>spark.shuffle.memoryFraction</li>
<li>默认值：0.2</li>
<li>参数说明：该参数代表了Executor内存中，分配给shuffle read task进行聚合操作的内存比例，默认是20%。</li>
<li>调优建议：在资源参数调优中讲解过这个参数。如果内存充足，而且很少使用持久化操作，建议调高这个比例，给shuffle read的聚合操作更多内存，以避免由于内存不足导致聚合过程中频繁读写磁盘。在实践中发现，合理调节该参数可以将性能提升10%左右。</li>
</ul>
<h3><a id="_125"></a>在这里好好唱一出戏：</h3>
<h4><a id="1_StaticMemoryManager__127"></a>（1） StaticMemoryManager 静态内存分配</h4>
<p><img src="https://img-blog.csdnimg.cn/20181113231746457.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoZW5zaG91bml1,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<pre><code>  private def getMaxStorageMemory(conf: SparkConf): Long = {
  
    val systemMaxMemory = conf.getLong("spark.testing.memory", Runtime.getRuntime.maxMemory)
    
    val memoryFraction = conf.getDouble("spark.storage.memoryFraction", 0.6)          &lt;=神来之笔
    val safetyFraction = conf.getDouble("spark.storage.safetyFraction", 0.9)          &lt;=神来之笔
    (systemMaxMemory * memoryFraction * safetyFraction).toLong                        &lt;=神来之笔
  }


    private def getMaxExecutionMemory(conf: SparkConf): Long = {
    
    val systemMaxMemory = conf.getLong("spark.testing.memory", Runtime.getRuntime.maxMemory)

    if (systemMaxMemory &lt; MIN_MEMORY_BYTES) {
      throw new IllegalArgumentException(s"System memory $systemMaxMemory must " +
        s"be at least $MIN_MEMORY_BYTES. Please increase heap size using the --driver-memory " +
        s"option or spark.driver.memory in Spark configuration.")
    }
    if (conf.contains("spark.executor.memory")) {
      val executorMemory = conf.getSizeAsBytes("spark.executor.memory")
      if (executorMemory &lt; MIN_MEMORY_BYTES) {
        throw new IllegalArgumentException(s"Executor memory $executorMemory must be at least " +
          s"$MIN_MEMORY_BYTES. Please increase executor memory using the " +
          s"--executor-memory option or spark.executor.memory in Spark configuration.")
      }
    }
    val memoryFraction = conf.getDouble("spark.shuffle.memoryFraction", 0.2)              &lt;=神来之笔
    val safetyFraction = conf.getDouble("spark.shuffle.safetyFraction", 0.8)              &lt;=神来之笔
    (systemMaxMemory * memoryFraction * safetyFraction).toLong                            &lt;=神来之笔
  }



  private val RESERVED_SYSTEM_MEMORY_BYTES = 300 * 1024 * 1024                            &lt;=神来之笔

  def apply(conf: SparkConf, numCores: Int): UnifiedMemoryManager = {
    val maxMemory = getMaxMemory(conf)
    new UnifiedMemoryManager(
      conf,
      maxHeapMemory = maxMemory,
      onHeapStorageRegionSize =
        (maxMemory * conf.getDouble("spark.memory.storageFraction", 0.5)).toLong,        &lt;=神来之笔
      numCores = numCores)
    }
</code></pre>
<h4><a id="2__UnifiedMemoryManager__180"></a>(2)  UnifiedMemoryManager 统一内存分配</h4>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/13/1670d76c442a2f6b?w=966&amp;h=598&amp;f=png&amp;s=70602" alt=""></p>
<pre><code>    /**
       * Return the total amount of memory shared between execution and storage, in bytes.
       */
      private def getMaxMemory(conf: SparkConf): Long = {
      
        val systemMemory = conf.getLong("spark.testing.memory", Runtime.getRuntime.maxMemory)    &lt;=神来之笔
        val reservedMemory = conf.getLong("spark.testing.reservedMemory",                        &lt;=神来之笔
        
          if (conf.contains("spark.testing")) 0 else RESERVED_SYSTEM_MEMORY_BYTES)
        val minSystemMemory = (reservedMemory * 1.5).ceil.toLong
        
        if (systemMemory &lt; minSystemMemory) {
          throw new IllegalArgumentException(s"System memory $systemMemory must " +
            s"be at least $minSystemMemory. Please increase heap size using the --driver-memory " +
            s"option or spark.driver.memory in Spark configuration.")
        }
        // SPARK-12759 Check executor memory to fail fast if memory is insufficient
        if (conf.contains("spark.executor.memory")) {
          val executorMemory = conf.getSizeAsBytes("spark.executor.memory")
          if (executorMemory &lt; minSystemMemory) {
            throw new IllegalArgumentException(s"Executor memory $executorMemory must be at least " +
              s"$minSystemMemory. Please increase executor memory using the " +
              s"--executor-memory option or spark.executor.memory in Spark configuration.")
          }
        }
        val usableMemory = systemMemory - reservedMemory
        
        val memoryFraction = conf.getDouble("spark.memory.fraction", 0.6)                       &lt;=神来之笔
        (usableMemory * memoryFraction).toLong
      }


      def apply(conf: SparkConf, numCores: Int): UnifiedMemoryManager = {
        val maxMemory = getMaxMemory(conf)
        new UnifiedMemoryManager(
          conf,
          maxHeapMemory = maxMemory,
          onHeapStorageRegionSize =
            (maxMemory * conf.getDouble("spark.memory.storageFraction", 0.5)).toLong,          &lt;=神来之笔    
          numCores = numCores)
      }
</code></pre>
<hr>
<ul>
<li>
<p>spark.shuffle.manager</p>
</li>
<li>
<p>默认值：sort</p>
</li>
<li>
<p>参数说明：该参数用于设置ShuffleManager的类型。Spark 1.5以后，有三个可选项：hash、sort和tungsten-sort。HashShuffleManager是Spark 1.2以前的默认选项，但是Spark 1.2以及之后的版本默认都是SortShuffleManager了。tungsten-sort与sort类似，但是使用了tungsten计划中的堆外内存管理机制，内存使用效率更高。</p>
</li>
<li>
<p>调优建议：由于SortShuffleManager默认会对数据进行排序，因此如果你的业务逻辑中需要该排序机制的话，则使用默认的SortShuffleManager就可以；而如果你的业务逻辑不需要对数据进行排序，那么建议参考后面的几个参数调优，通过bypass机制或优化的HashShuffleManager来避免排序操作，同时提供较好的磁盘读写性能。这里要注意的是，tungsten-sort要慎用，因为之前发现了一些相应的bug。</p>
</li>
<li>
<p>配置参数：spark.shuffle.manager，默认是sort。</p>
<pre><code> val shortShuffleMgrNames = Map(
    "sort" -&gt; classOf[org.apache.spark.shuffle.sort.SortShuffleManager].getName,
    "tungsten-sort" -&gt; classOf[org.apache.spark.shuffle.sort.SortShuffleManager].getName)
  val shuffleMgrName = conf.get("spark.shuffle.manager", "sort")
</code></pre>
</li>
</ul>
<hr>
<ul>
<li>spark.shuffle.sort.bypassMergeThreshold</li>
<li>默认值：200</li>
<li>参数说明：当ShuffleManager为SortShuffleManager时，如果shuffle read task的数量小于这个阈值（默认是200），则shuffle write过程中不会进行排序操作，而是直接按照未经优化的HashShuffleManager的方式去写数据，但是最后会将每个task产生的所有临时磁盘文件都合并成一个文件，并会创建单独的索引文件。</li>
<li>调优建议：当你使用SortShuffleManager时，如果的确不需要排序操作，那么建议将这个参数调大一些，大于shuffle read task的数量。那么此时就会自动启用bypass机制，map-side就不会进行排序了，减少了排序的性能开销。但是这种方式下，依然会产生大量的磁盘文件，因此shuffle write性能有待提高。</li>
</ul>
<hr>
<ul>
<li>spark.shuffle.consolidateFiles</li>
<li>默认值：false</li>
<li>参数说明：如果使用HashShuffleManager，该参数有效。如果设置为true，那么就会开启consolidate机制，会大幅度合并shuffle write的输出文件，对于shuffle read task数量特别多的情况下，这种方法可以极大地减少磁盘IO开销，提升性能。</li>
<li>调优建议：如果的确不需要SortShuffleManager的排序机制，那么除了使用bypass机制，还可以尝试将spark.shffle.manager参数手动指定为hash，使用HashShuffleManager，同时开启consolidate机制。在实践中尝试过，发现其性能比开启了bypass机制的SortShuffleManager要高出10%~30%。</li>
</ul>
<hr>
<ul>
<li>Spark.Shuffle.blockTransferService</li>
<li>默认值：Netty</li>
<li>实现在Executor之间传递Shuffle缓存块，有Netty和Nio两种可用的实现。</li>
</ul>
<hr>
<ul>
<li>
<p>Spark.Shuffle.compress</p>
</li>
<li>
<p>默认是true</p>
</li>
<li>
<p>判断是否对mapper端的聚合输出进行压缩，表示每一个shuffle过程都会对mapper端的输出进行压缩。举例如下：如果有几千台或者上万台的机器进行汇聚计算，数据量和网络传输会非常大，这样会造成大连好的内存消耗，磁盘I/O消耗，以及网络I/O消耗。如果在Mapper端进行压缩，就会减少shuffle过程中下一个Stage向上一个Stage抓数据的网络开销。</p>
<pre><code>    * Merge zero or more spill files together, choosing the fastest merging strategy based on the
    * number of spills and the IO compression codec.
    * @return the partition lengths in the merged file.
 
   private long[] mergeSpills(SpillInfo[] spills, File outputFile) throws IOException {

     final boolean compressionEnabled = sparkConf.getBoolean("spark.shuffle.compress", true);   &lt;=神来之笔
     
     final CompressionCodec compressionCodec = CompressionCodec$.MODULE$.createCodec(sparkConf);
     
     final boolean fastMergeEnabled =
       sparkConf.getBoolean("spark.shuffle.unsafe.fastMergeEnabled", true);                    &lt;=神来之笔
       
     final boolean fastMergeIsSupported = !compressionEnabled ||
       CompressionCodec$.MODULE$.supportsConcatenationOfSerializedStreams(compressionCodec);    &lt;=神来之笔
       
     final boolean encryptionEnabled = blockManager.serializerManager().encryptionEnabled();
     try {
       if (spills.length == 0) {
         new FileOutputStream(outputFile).close(); // Create an empty file
         return new long[partitioner.numPartitions()];
       } else if (spills.length == 1) {
         // Here, we don't need to perform any metrics updates because the bytes written to this
         // output file would have already been counted as shuffle bytes written.
         Files.move(spills[0].file, outputFile);
         return spills[0].partitionLengths;
       } else {
         final long[] partitionLengths;
         // There are multiple spills to merge, so none of these spill files' lengths were counted
         // towards our shuffle write count or shuffle write time. If we use the slow merge path,
         // then the final output file's size won't necessarily be equal to the sum of the spill
         // files' sizes. To guard against this case, we look at the output file's actual size when
         // computing shuffle bytes written.
         //
         // We allow the individual merge methods to report their own IO times since different merge
         // strategies use different IO techniques.  We count IO during merge towards the shuffle
         // shuffle write time, which appears to be consistent with the "not bypassing merge-sort"
         // branch in ExternalSorter.
         
         if (fastMergeEnabled &amp;&amp; fastMergeIsSupported) {
         
           // Compression is disabled or we are using an IO compression codec that supports
           // decompression of concatenated compressed streams, so we can perform a fast spill merge
           // that doesn't need to interpret the spilled bytes.
           if (transferToEnabled &amp;&amp; !encryptionEnabled) {
             logger.debug("Using transferTo-based fast merge");
             partitionLengths = mergeSpillsWithTransferTo(spills, outputFile);
           } else {
             logger.debug("Using fileStream-based fast merge");
             partitionLengths = mergeSpillsWithFileStream(spills, outputFile, null);
           }
         } else {
           logger.debug("Using slow merge");
           partitionLengths = mergeSpillsWithFileStream(spills, outputFile, compressionCodec);
         }
         // When closing an UnsafeShuffleExternalSorter that has already spilled once but also has
         // in-memory records, we write out the in-memory records to a file but do not count that
         // final write as bytes spilled (instead, it's accounted as shuffle write). The merge needs
         // to be counted as shuffle write, but this will lead to double-counting of the final
         // SpillInfo's bytes.
         writeMetrics.decBytesWritten(spills[spills.length - 1].file.length());
         writeMetrics.incBytesWritten(outputFile.length());
         return partitionLengths;
       }
     } catch (IOException e) {
       if (outputFile.exists() &amp;&amp; !outputFile.delete()) {
         logger.error("Unable to delete output file {}", outputFile.getPath());
       }
       throw e;
     }
   }
</code></pre>
</li>
</ul>
<hr>
<ul>
<li>
<p>spark.io.compression.codec</p>
</li>
<li>
<p>该参数用来压缩内部数据，如：RDD分区，广播变量和shuffle输出的数据等，所采用的压缩有LZ4，Lzf，Snappy等三种选择，默认是Snappy，但是和Snappy相比较，Lzf的压缩率较高。建议在大量Shuffle过程中，可以选择Lzf4。</p>
</li>
<li>
<p>默认是Snappy</p>
<pre><code>  private[spark] object CompressionCodec {
    private val configKey = "spark.io.compression.codec"
    private[spark] def supportsConcatenationOfSerializedStreams(codec: CompressionCodec): Boolean = {
      (codec.isInstanceOf[SnappyCompressionCodec] || codec.isInstanceOf[LZFCompressionCodec]
        || codec.isInstanceOf[LZ4CompressionCodec] || codec.isInstanceOf[ZStdCompressionCodec])
    }
  
    private val shortCompressionCodecNames = Map(
      "lz4" -&gt; classOf[LZ4CompressionCodec].getName,
      "lzf" -&gt; classOf[LZFCompressionCodec].getName,
      "snappy" -&gt; classOf[SnappyCompressionCodec].getName,
      "zstd" -&gt; classOf[ZStdCompressionCodec].getName)
</code></pre>
</li>
</ul>
<hr>
<ul>
<li>
<p>spark.shuffle.file.buffer</p>
</li>
<li>
<p>默认值：32k(考虑最小硬件下都能成功)</p>
</li>
<li>
<p>参数说明：该参数用于设置shuffle write task的BufferedOutputStream的buffer缓冲大小。将数据写到磁盘文件之前，会先写入buffer缓冲中，待缓冲写满之后，才会溢写到磁盘。</p>
</li>
<li>
<p>调优建议：如果作业可用的内存资源较为充足的话，可以适当增加这个参数的大小（比如64k），从而减少shuffle write过程中溢写磁盘文件的次数，也就可以减少磁盘IO次数，进而提升性能。在实践中发现，合理调节该参数，性能会有1%~5%的提升。</p>
<pre><code>   private[spark] val SHUFFLE_FILE_BUFFER_SIZE =
      ConfigBuilder("spark.shuffle.file.buffer")
        .doc("Size of the in-memory buffer for each shuffle file output stream, in KiB unless " +
          "otherwise specified. These buffers reduce the number of disk seeks and system calls " +
          "made in creating intermediate shuffle files.")
        .bytesConf(ByteUnit.KiB)
        .checkValue(v =&gt; v &gt; 0 &amp;&amp; v &lt;= Int.MaxValue / 1024,
          s"The file buffer size must be greater than 0 and less than ${Int.MaxValue / 1024}.")
        .createWithDefaultString("32k")



  final class ShuffleExternalSorter extends MemoryConsumer {
  
    private static final Logger logger = LoggerFactory.getLogger(ShuffleExternalSorter.class);
  
    @VisibleForTesting
    static final int DISK_WRITE_BUFFER_SIZE = 1024 * 1024;
  
    private final int numPartitions;
    private final TaskMemoryManager taskMemoryManager;
    private final BlockManager blockManager;
    private final TaskContext taskContext;
    private final ShuffleWriteMetrics writeMetrics;
  
    /**
     * Force this sorter to spill when there are this many elements in memory.
     */
    private final int numElementsForSpillThreshold;
  
    /** The buffer size to use when writing spills using DiskBlockObjectWriter */
    private final int fileBufferSizeBytes;
  
    /** The buffer size to use when writing the sorted records to an on-disk file */
    private final int diskWriteBufferSize;
  
    /**
     * Memory pages that hold the records being sorted. The pages in this list are freed when
     * spilling, although in principle we could recycle these pages across spills (on the other hand,
     * this might not be necessary if we maintained a pool of re-usable pages in the TaskMemoryManager
     * itself).
     */
    private final LinkedList&lt;MemoryBlock&gt; allocatedPages = new LinkedList&lt;&gt;();
  
    private final LinkedList&lt;SpillInfo&gt; spills = new LinkedList&lt;&gt;();
  
    /** Peak memory used by this sorter so far, in bytes. **/
    private long peakMemoryUsedBytes;
  
    // These variables are reset after spilling:
    @Nullable private ShuffleInMemorySorter inMemSorter;
    @Nullable private MemoryBlock currentPage = null;
    private long pageCursor = -1;
  
    ShuffleExternalSorter(
        TaskMemoryManager memoryManager,
        BlockManager blockManager,
        TaskContext taskContext,
        int initialSize,
        int numPartitions,
        SparkConf conf,
        ShuffleWriteMetrics writeMetrics) {
      super(memoryManager,
        (int) Math.min(PackedRecordPointer.MAXIMUM_PAGE_SIZE_BYTES, memoryManager.pageSizeBytes()),
        memoryManager.getTungstenMemoryMode());
      this.taskMemoryManager = memoryManager;
      this.blockManager = blockManager;
      this.taskContext = taskContext;
      this.numPartitions = numPartitions;
      
      // Use getSizeAsKb (not bytes) to maintain backwards compatibility if no units are provided
      this.fileBufferSizeBytes =
          (int) (long) conf.get(package$.MODULE$.SHUFFLE_FILE_BUFFER_SIZE()) * 1024;            &lt;=神来之笔
          
      this.numElementsForSpillThreshold =
          (int) conf.get(package$.MODULE$.SHUFFLE_SPILL_NUM_ELEMENTS_FORCE_SPILL_THRESHOLD());
          
      this.writeMetrics = writeMetrics;
      
      this.inMemSorter = new ShuffleInMemorySorter(
        this, initialSize, conf.getBoolean("spark.shuffle.sort.useRadixSort", true));
        
      this.peakMemoryUsedBytes = getMemoryUsage();
      this.diskWriteBufferSize =
          (int) (long) conf.get(package$.MODULE$.SHUFFLE_DISK_WRITE_BUFFER_SIZE());
    }
</code></pre>
</li>
</ul>
<hr>
<ul>
<li>
<p>spark.shuffle.io.numConnectionsPerPeer</p>
</li>
<li>
<p>仅Netty使用，复用主机之间的连接，以减少大型集群的连接建立，</p>
</li>
<li>
<p>默认是1</p>
<pre><code>    TransportConf ：
    Number of concurrent connections between two nodes for fetching data.

    public int numConnectionsPerPeer() {
      return conf.getInt(SPARK_NETWORK_IO_NUMCONNECTIONSPERPEER_KEY, 1);
    }
</code></pre>
</li>
</ul>
<hr>
<ul>
<li>
<p>Spark.Shuffle.io.preferDirectBufs</p>
</li>
<li>
<p>仅限Netty使用，堆外缓存可以有效减少垃圾回收和缓存复制。对于堆外内存紧张的用户来说，可以考虑禁用这个选项，从而迫使Netty所有的内存都分配到堆上，默认是true。</p>
<p>TransportConf:</p>
<pre><code>  /** If true, we will prefer allocating off-heap byte buffers within Netty. */
    public boolean preferDirectBufs() {
      return conf.getBoolean(SPARK_NETWORK_IO_PREFERDIRECTBUFS_KEY, true);
    }
</code></pre>
</li>
</ul>
<hr>
<ul>
<li>
<p>spark.shuffle.service.enabled</p>
</li>
<li>
<p>默认为false，如果配置成true，BlocakManager实例生成时，需要读取Spark.Shuffle.service.port配置的端口，注意此时BlockManager的ShuffleClient不再是默认的BlocakTransferSerice实例，而是ExternalShuffleClient。</p>
</li>
<li>
<p>启用外部的Shuffle Service , NodeManager中会长期运行一个辅助任务，用于提升Shuffle计算性能。</p>
<pre><code>  private[spark] class BlockManager(
      executorId: String,
      rpcEnv: RpcEnv,
      val master: BlockManagerMaster,
      val serializerManager: SerializerManager,
      val conf: SparkConf,
      memoryManager: MemoryManager,
      mapOutputTracker: MapOutputTracker,
      shuffleManager: ShuffleManager,
      val blockTransferService: BlockTransferService,
      securityManager: SecurityManager,
      numUsableCores: Int)
    extends BlockDataManager with BlockEvictionHandler with Logging {
  
    private[spark] val externalShuffleServiceEnabled =
      conf.getBoolean("spark.shuffle.service.enabled", false)

  // Port used by the external shuffle service. In Yarn mode, this may be already be
    // set through the Hadoop configuration as the server is launched in the Yarn NM.
    private val externalShuffleServicePort = {
      val tmpPort = Utils.getSparkOrYarnConfig(conf, "spark.shuffle.service.port", "7337").toInt
      if (tmpPort == 0) {
        // for testing, we set "spark.shuffle.service.port" to 0 in the yarn config, so yarn finds
        // an open port.  But we still need to tell our spark apps the right port to use.  So
        // only if the yarn config has the port set to 0, we prefer the value in the spark config
        conf.get("spark.shuffle.service.port").toInt
      } else {
        tmpPort
      }
    }

    // Client to read other executors' shuffle files. This is either an external service, or just the
    // standard BlockTransferService to directly connect to other Executors.
    private[spark] val shuffleClient = if (externalShuffleServiceEnabled) {
      val transConf = SparkTransportConf.fromSparkConf(conf, "shuffle", numUsableCores)
      new ExternalShuffleClient(transConf, securityManager,
        securityManager.isAuthenticationEnabled(), conf.get(config.SHUFFLE_REGISTRATION_TIMEOUT))
    } else {
      blockTransferService
    }
</code></pre>
</li>
</ul>
<p>基于Yarn的动态资源分配配置如下：</p>
<pre><code>首先需要对YARN的NodeManager进行配置，使其支持Spark的Shuffle Service。
（1）修改每台NodeManager上的yarn-site.xml：
    ##修改
    &lt;property&gt;
    &lt;name&gt;yarn.nodemanager.aux-services&lt;/name&gt;
    &lt;value&gt;mapreduce_shuffle,spark_shuffle&lt;/value&gt;
    &lt;/property&gt;
    ##增加
    &lt;property&gt;
    &lt;name&gt;yarn.nodemanager.aux-services.spark_shuffle.class&lt;/name&gt;
    &lt;value&gt;org.apache.spark.network.yarn.YarnShuffleService&lt;/value&gt;
    &lt;/property&gt;
    &lt;property&gt;
    &lt;name&gt;spark.shuffle.service.port&lt;/name&gt;
    &lt;value&gt;7337&lt;/value&gt;
    &lt;/property&gt;

（2）将$SPARK_HOME/lib/spark-1.5.0-yarn-shuffle.jar拷贝到每台NodeManager的${HADOOP_HOME}/share/hadoop/yarn/lib/下。
（3）重启所有NodeManager。
</code></pre>
<hr>
<ul>
<li>
<p>Spark.shuffle.Sort.bypassMergeThreshold</p>
</li>
<li>
<p>默认值为200</p>
</li>
<li>
<p>场景如下：如果Shuffle Read Task 的数量小于这个阈值（默认是200），那么Shuffle Write的过程中不会进行排序操作，而是直接按照未经优化的HashShuffleManager方式去写数据，最终还是会将每一个Task所产生的所有临时磁盘文件合并成一个文件，并创建单独索引。</p>
<pre><code>  private[spark] object SortShuffleWriter {
    def shouldBypassMergeSort(conf: SparkConf, dep: ShuffleDependency[_, _, _]): Boolean = {
      // We cannot bypass sorting if we need to do map-side aggregation.
      if (dep.mapSideCombine) {
        require(dep.aggregator.isDefined, "Map-side combine without Aggregator specified!")
        false
      } else {
        val bypassMergeThreshold: Int = conf.getInt("spark.shuffle.sort.bypassMergeThreshold", 200)
        dep.partitioner.numPartitions &lt;= bypassMergeThreshold
      }
    }
</code></pre>
</li>
</ul>
<hr>
<ul>
<li>
<p>Spark.Shuffle.spill</p>
</li>
<li>
<p>默认是True</p>
</li>
<li>
<p>即允许溢出到磁盘。</p>
<pre><code>  private[spark] class SortShuffleManager(conf: SparkConf) extends ShuffleManager with Logging {
    if (!conf.getBoolean("spark.shuffle.spill", true)) {
      logWarning(
        "spark.shuffle.spill was set to false, but this configuration is ignored as of Spark 1.6+." +
          " Shuffle will continue to spill to disk when necessary.")
    }
</code></pre>
</li>
</ul>
<hr>
<ul>
<li>
<p>spark.shuffle.spill.compress</p>
</li>
<li>
<p>设置为True是合理的，因为网络带宽往往最容易成为瓶颈</p>
</li>
<li>
<p>建议综合考虑cpu ,磁盘，网络的实际能力。</p>
<pre><code>   * Component which configures serialization, compression and encryption for various Spark
   * components, including automatic selection of which [[Serializer]] to use for shuffles.
   */
  private[spark] class SerializerManager(
      defaultSerializer: Serializer,
      conf: SparkConf,
      encryptionKey: Option[Array[Byte]]) {

   // Whether to compress broadcast variables that are stored
    private[this] val compressBroadcast = conf.getBoolean("spark.broadcast.compress", true)
    // Whether to compress shuffle output that are stored
    private[this] val compressShuffle = conf.getBoolean("spark.shuffle.compress", true)
    // Whether to compress RDD partitions that are stored serialized
    private[this] val compressRdds = conf.getBoolean("spark.rdd.compress", false)
    // Whether to compress shuffle output temporarily spilled to disk
    private[this] val compressShuffleSpill = conf.getBoolean("spark.shuffle.spill.compress", true)
</code></pre>
</li>
</ul>
<h2><a id="3__590"></a>3 总结</h2>
<p>本文综合Spark的核心参数配置，花大量时间，阅读源码并找到参数调优的位置和条件，一份好文实属不易，禁止转载，欢迎学习</p>
<p>秦凯新 于深圳</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>