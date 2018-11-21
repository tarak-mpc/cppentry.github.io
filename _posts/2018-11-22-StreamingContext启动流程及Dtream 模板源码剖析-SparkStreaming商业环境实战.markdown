---
layout:     post
title:      StreamingContext启动流程及Dtream 模板源码剖析-SparkStreaming商业环境实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本套技术专栏是作者（秦凯新）平时工作的总结和升华，通过从真实商业环境抽取案例进行总结和分享，并给出商业应用的调优建议和集群环境容量规划等内容，请持续关注本套博客。版权声明：禁止转载，欢迎学习。QQ邮箱地址：1120746959@qq.com，如有任何商业交流，可随时联系。					https://blog.csdn.net/shenshouniu/article/details/83905007				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>本套系列博客从真实商业环境抽取案例进行总结和分享，并给出Spark源码解读及商业实战指导，请持续关注本套博客。版权声明：本套Spark源码解读及商业实战归作者（秦凯新）所有，禁止转载，欢迎学习。</p>
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
<li><a href="https://juejin.im/post/5bdb0e76e51d456c6f1cc45f" rel="nofollow">Spark商业环境实战-ReceiverTracker 启动过程及接收器 receiver RDD 任务提交机制源码剖析</a></li>
</ul>
<h2><a id="1_SparkStreaming_is_What_20"></a>1 SparkStreaming is What?</h2>
<p>SparkStreaming 是基于批处理的流式计算平台，目前默认是200ms的间隔。SparkStreaming 会把数据流封装成一个个批次，然后把多个批次的数据转换成RDD，并交由BlockManger管理，最终以任务的方式进行提交DAG有向无环图。</p>
<ul>
<li>离散流DStrem : DStrem可以认为是没有边界的集合，没有大小的限制。因为包含了时间的维度，因此可以看做是时空模型。当然DStrem也是一个逻辑的概念，因为SparkStreaming完全基于Dstream来构建算子体系，依赖关系。但是请注意，Dstream 又是抽象的，计算过程最终都会转化为RDD来实现，所以RDD作为最底层的基础，是具有物理意义的。</li>
<li>随着时间的流逝，Dstrem会不断的产生RDD，因此对Dstrem的操作就是在固定时间上对RDD的操作。</li>
</ul>
<h2><a id="2_StreamingContext_26"></a>2 StreamingContext入口类的真面目</h2>
<h3><a id="21_StreamingContext_28"></a>2.1 StreamingContext英文详细解释</h3>
<pre><code> * Main entry point for Spark Streaming functionality. It provides methods used to create
 * [[org.apache.spark.streaming.dstream.DStream]]s from various input sources. It can be either
 * created by providing a Spark master URL and an appName, or from a org.apache.spark.SparkConf
 * configuration (see core Spark documentation), or from an existing org.apache.spark.SparkContext.
 * The associated SparkContext can be accessed using `context.sparkContext`. After
 * creating and transforming DStreams, the streaming computation can be started and stopped
 * using `context.start()` and `context.stop()`, respectively.
 * `context.awaitTermination()` allows the current thread to wait for the termination
 * of the context by `stop()` or by an exception.
</code></pre>
<h3><a id="21_SparkStreaming_40"></a>2.1 SparkStreaming基本案例与深层次剖析</h3>
<ul>
<li>
<p>SparkStreaming基本案例</p>
<pre><code>  object NetworkWordCount {
    def main(args: Array[String]) {
      if (args.length &lt; 2) {
        System.err.println("Usage: NetworkWordCount &lt;hostname&gt; &lt;port&gt;")
        System.exit(1)
      }
  
      StreamingExamples.setStreamingLogLevels()
      
      // Create the context with a 1 second batch size
      val sparkConf = new SparkConf().setAppName("NetworkWordCount")
      val ssc = new StreamingContext(sparkConf, Seconds(1))
  
      // Create a socket stream on target ip:port and count the
      // words in input stream of \n delimited text (eg. generated by 'nc')
      // Note that no duplication in storage level only for running locally.
      // Replication necessary in distributed scenario for fault tolerance.
      val lines = ssc.socketTextStream(args(0), args(1).toInt, StorageLevel.MEMORY_AND_DISK_SER)
      val words = lines.flatMap(_.split(" "))
      val wordCounts = words.map(x =&gt; (x, 1)).reduceByKey(_ + _)
      wordCounts.print()
      ssc.start()
      ssc.awaitTermination()
    }
  }
</code></pre>
</li>
<li>
<p>StreamingContext到SparkContext转换</p>
<pre><code>   def this(conf: SparkConf, batchDuration: Duration) = {
      this(StreamingContext.createNewSparkContext(conf), null, batchDuration)
    }
    
  private[streaming] def createNewSparkContext(conf: SparkConf): SparkContext = {
      new SparkContext(conf)
    }
</code></pre>
</li>
<li>
<p>socketTextStream依赖关系</p>
</li>
</ul>
<p>（1）超级父类逻辑模板英文专业讲解：</p>
<pre><code>    * A Discretized Stream (DStream), the basic abstraction in Spark Streaming, is a continuous
     * sequence of RDDs (of the same type) representing a continuous stream of data (see
     * org.apache.spark.rdd.RDD in the Spark core documentation for more details on RDDs).
     * DStreams can either be created from live data (such as, data from TCP sockets, Kafka, Flume,
     * etc.) using a [[org.apache.spark.streaming.StreamingContext]] or it can be generated by
     * transforming existing DStreams using operations such as `map`,
     * `window` and `reduceByKeyAndWindow`. While a Spark Streaming program is running, each DStream
     * periodically generates a RDD, either from live data or by transforming the RDD generated by a
     * parent DStream.
     *
     * This class contains the basic operations available on all DStreams, such as `map`, `filter` and
     * `window`. In addition, [[org.apache.spark.streaming.dstream.PairDStreamFunctions]] contains
     * operations available only on DStreams of key-value pairs, such as `groupByKeyAndWindow` and
     * `join`. These operations are automatically available on any DStream of pairs
     * (e.g., DStream[(Int, Int)] through implicit conversions.
     *
     * A DStream internally is characterized by a few basic properties:
     *  - A list of other DStreams that the DStream depends on
     *  - A time interval at which the DStream generates an RDD
     *  - A function that is used to generate an RDD after each time interval
</code></pre>
<p>（2） Dstream源码段摘录</p>
<h6><a id="DstreamDstreamDstream_104"></a>除了第一个Dstream，后面的Dstream都要依赖前面的Dstream。</h6>
<h6><a id="DstreamintrvalRDD_105"></a>Dstream在每一个时间间隔（intrval）都会生成一个RDD</h6>
<pre><code>    abstract class DStream[T: ClassTag] (
        @transient private[streaming] var ssc: StreamingContext
      ) extends Serializable with Logging {
    
      validateAtInit()
    
      // =======================================================================
      // Methods that should be implemented by subclasses of DStream
      // =======================================================================
    
      /** Time interval after which the DStream generates an RDD */
      def slideDuration: Duration
    
      /** List of parent DStreams on which this DStream depends on */
      def dependencies: List[DStream[_]]
    
      /** Method that generates an RDD for the given time */
      def compute(validTime: Time): Option[RDD[T]]
    
      // =======================================================================
      // Methods and fields available on all DStreams
      // =======================================================================
</code></pre>
<h5><a id="generatedRDDDstreamRDD_129"></a>generatedRDD表示Dstream在每一个批次所相应生成的RDD</h5>
<pre><code>      // RDDs generated, marked as private[streaming] so that testsuites can access it
      @transient
      private[streaming] var generatedRDDs = new HashMap[Time, RDD[T]]()
    
      // Time zero for the DStream
      private[streaming] var zeroTime: Time = null
    
      // Duration for which the DStream will remember each RDD created
      private[streaming] var rememberDuration: Duration = null
    
      // Storage level of the RDDs in the stream
      private[streaming] var storageLevel: StorageLevel = StorageLevel.NONE
</code></pre>
<h5><a id="getOrCompute_145"></a>计算函数getOrCompute</h5>
<pre><code>   * Get the RDD corresponding to the given time; either retrieve it from cache
   * or compute-and-cache it.
      private[streaming] final def getOrCompute(time: Time): Option[RDD[T]] = {
        // If RDD was already generated, then retrieve it from HashMap,
        // or else compute the RDD
        generatedRDDs.get(time).orElse {
          // Compute the RDD if time is valid (e.g. correct time in a sliding window)
          // of RDD generation, else generate nothing.
          if (isTimeValid(time)) {
    
            val rddOption = createRDDWithLocalProperties(time, displayInnerRDDOps = false) {
              // Disable checks for existing output directories in jobs launched by the streaming
              // scheduler, since we may need to write output to an existing directory during checkpoint
              // recovery; see SPARK-4835 for more details. We need to have this call here because
              // compute() might cause Spark jobs to be launched.
              SparkHadoopWriterUtils.disableOutputSpecValidation.withValue(true) {
                compute(time)
              }
            }
            rddOption.foreach { case newRDD =&gt;
              // Register the generated RDD for caching and checkpointing
              if (storageLevel != StorageLevel.NONE) {
                newRDD.persist(storageLevel)
                logDebug(s"Persisting RDD ${newRDD.id} for time $time to $storageLevel")
              }
              if (checkpointDuration != null &amp;&amp; (time - zeroTime).isMultipleOf(checkpointDuration)) {
                newRDD.checkpoint()
                logInfo(s"Marking RDD ${newRDD.id} for time $time for checkpointing")
              }
              generatedRDDs.put(time, newRDD)
            }
            rddOption
          } else {
            None
          }
        }
      }
</code></pre>
<h5><a id="JobgenerateJob_186"></a>Job生成函数generateJob</h5>
<pre><code>   * Generate a SparkStreaming job for the given time. This is an internal method that
   * should not be called directly. This default implementation creates a job
   * that materializes the corresponding RDD. Subclasses of DStream may override this
   * to generate their own jobs.
   
  private[streaming] def generateJob(time: Time): Option[Job] = {
    getOrCompute(time) match {
      case Some(rdd) =&gt;
        val jobFunc = () =&gt; {
          val emptyFunc = { (iterator: Iterator[T]) =&gt; {} }
          context.sparkContext.runJob(rdd, emptyFunc)
        }
        Some(new Job(time, jobFunc))
      case None =&gt; None
    }
  }
</code></pre>
<h3><a id="22_socketTextStream_206"></a>2.2 socketTextStream依赖关系</h3>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/10/166f937fc729bb9d?w=724&amp;h=458&amp;f=png&amp;s=39856" alt=""></p>
<h3><a id="23_DStreamDstreamAction_210"></a>2.3 DStream及其所有子类及Dstream的Action触发</h3>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/10/166f94e51c572bc9?w=458&amp;h=457&amp;f=png&amp;s=72510" alt=""></p>
<p>目前Dstream的输出触发操作有：print，saveAsTextFiles，saveAsObjectFiles，saveAsHadoopFiles, foreachRDD。而这些输出触发操作会生成ForeachDStream对象。并注册到DStreamGraph的成员outputStreams中。</p>
<pre><code>  final private[streaming] class DStreamGraph extends Serializable with Logging {
  private val inputStreams = new ArrayBuffer[InputDStream[_]]()
  private val outputStreams = new ArrayBuffer[DStream[_]]()
</code></pre>
<p>而StreamingContext中，DStreamGraph 是重要的成员，专门负责action操作。</p>
<pre><code>  private[streaming] val graph: DStreamGraph = {
    if (isCheckpointPresent) {
      _cp.graph.setContext(this)
      _cp.graph.restoreCheckpointData()
      _cp.graph
    } else {
      require(_batchDur != null, "Batch duration for StreamingContext cannot be null")
      val newGraph = new DStreamGraph()
      newGraph.setBatchDuration(_batchDur)
      newGraph
    }
  }
</code></pre>
<h3><a id="24_DStreamGraph_236"></a>2.4 DStreamGraph的回溯</h3>
<p>各个Dstream对象的依赖关系和操作算子最终如何串成一条串呢？DStreamGraph会利用outputStreams进行回溯并生成Job，当StreamingContext启动的时候，才会真正执行算法链条。</p>
<h2><a id="3_ForeachDStream_ForeachRDD__239"></a>3 ForeachDStream 与ForeachRDD 生死相依</h2>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/10/166fb6572d89c2da?w=769&amp;h=488&amp;f=png&amp;s=69512" alt=""></p>
<p>可以看到Dstream抽象父类模板的print函数实际上会定义ForeachFuc 和 ForeachRDD ，ForeachRDD中包含了ForeachDstream，而这个ForeachDstream最终会注册到StreamingContext.</p>
<ul>
<li>
<p>print 函数</p>
<pre><code>   * Print the first num elements of each RDD generated in this DStream. This is an output
   * operator, so this DStream will be registered as an output stream and there materialized.
    def print(num: Int): Unit = ssc.withScope {
      def foreachFunc: (RDD[T], Time) =&gt; Unit = {
        (rdd: RDD[T], time: Time) =&gt; {
          val firstNum = rdd.take(num + 1)
          // scalastyle:off println
          println("-------------------------------------------")
          println(s"Time: $time")
          println("-------------------------------------------")
          firstNum.take(num).foreach(println)
          if (firstNum.length &gt; num) println("...")
          println()
          // scalastyle:on println
        }
      }
      foreachRDD(context.sparkContext.clean(foreachFunc), displayInnerRDDOps = false)
    }
</code></pre>
</li>
<li>
<p>saveAsTextFiles 函数</p>
<pre><code>   def saveAsTextFiles(prefix: String, suffix: String = ""): Unit = ssc.withScope {
      val saveFunc = (rdd: RDD[T], time: Time) =&gt; {
        val file = rddToFileName(prefix, suffix, time)
        rdd.saveAsTextFile(file)
      }
      this.foreachRDD(saveFunc, displayInnerRDDOps = false)
    }
</code></pre>
</li>
<li>
<p>register 函数</p>
<pre><code>   * Register this streaming as an output stream. This would ensure that RDDs of this
   * DStream will be generated.
    private[streaming] def register(): DStream[T] = {
      ssc.graph.addOutputStream(this)
      this
    }
</code></pre>
</li>
</ul>
<h2><a id="4__InputStream__OutputStream_286"></a>4 总结 InputStream 和 OutputStream</h2>
<ul>
<li>InputStream 定义了业务部分的数据源的处理逻辑。但是接收器Receiver才是最终的水流的开关，在Executor上Receiver接收流数据，然后缓冲起来，积累成块，然后交由BlockManager管理。最终分配给相应处理时间间隔的Job。</li>
<li>在Driver端需要有一个跟踪器ReceiverTracker，而这个跟踪器会不断监督Executor启动Receiver，(比如：发送Receiver  Rdd 给Executor, 注意是把Receiver作为RDD给Executor)，同时管理待分配给Job的数据Block的元数据。</li>
</ul>
<h2><a id="5_StreamingContext__291"></a>5 StreamingContext 启动秘籍</h2>
<h3><a id="51_StreamingContext_JobScheduler_293"></a>5.1 StreamingContext 启动使得JobScheduler联动（好基友）</h3>
<pre><code>private[streaming] val scheduler = new JobScheduler(this)

def start(): Unit = synchronized {
    state match {
      case INITIALIZED =&gt;
        startSite.set(DStream.getCreationSite())
        StreamingContext.ACTIVATION_LOCK.synchronized {
          StreamingContext.assertNoOtherContextIsActive()
          try {
            validate()
            // Start the streaming scheduler in a new thread, so that thread local properties
            // like call sites and job groups can be reset without affecting those of the
            // current thread.
            ThreadUtils.runInNewThread("streaming-start") {
              sparkContext.setCallSite(startSite.get)
              sparkContext.clearJobGroup()
              sparkContext.setLocalProperty(SparkContext.SPARK_JOB_INTERRUPT_ON_CANCEL, "false")
              savedProperties.set(SerializationUtils.clone(sparkContext.localProperties.get()))
              
              scheduler.start()     &lt;= 神来之笔
              
            }
            state = StreamingContextState.ACTIVE
            scheduler.listenerBus.post(
              StreamingListenerStreamingStarted(System.currentTimeMillis()))
          } catch {
            case NonFatal(e) =&gt;
              logError("Error starting the context, marking it as stopped", e)
              scheduler.stop(false)
              state = StreamingContextState.STOPPED
              throw e
          }
          StreamingContext.setActiveContext(this)
        }
        logDebug("Adding shutdown hook") // force eager creation of logger
        shutdownHookRef = ShutdownHookManager.addShutdownHook(
          StreamingContext.SHUTDOWN_HOOK_PRIORITY)(() =&gt; stopOnShutdown())
        // Registering Streaming Metrics at the start of the StreamingContext
        assert(env.metricsSystem != null)
        env.metricsSystem.registerSource(streamingSource)
        uiTab.foreach(_.attach())
        logInfo("StreamingContext started")
      case ACTIVE =&gt;
        logWarning("StreamingContext has already been started")
      case STOPPED =&gt;
        throw new IllegalStateException("StreamingContext has already been stopped")
    }
  }
</code></pre>
<h3><a id="52_JobScheduler_345"></a>5.2 JobScheduler联动后坐拥乾坤</h3>
<ul>
<li>
<p>Driver端：启动receiverTracker =&gt; 用于数据接收，数据缓存,Block生成</p>
</li>
<li>
<p>Driver端：启动jobGenerator =&gt; 用于DstreamGraph初始化，Dstream与RDD的转换，生成Job，提交执行</p>
<pre><code>  def start(): Unit = synchronized {
      if (eventLoop != null) return // scheduler has already been started
  
  logDebug("Starting JobScheduler")
  eventLoop = new EventLoop[JobSchedulerEvent]("JobScheduler") {
    override protected def onReceive(event: JobSchedulerEvent): Unit = processEvent(event)

    override protected def onError(e: Throwable): Unit = reportError("Error in job scheduler", e)
  }
  
  eventLoop.start()                                &lt;= 神来之笔，耳听八方

  // attach rate controllers of input streams to receive batch completion updates
  for { 
  
    inputDStream &lt;- ssc.graph.getInputStreams      &lt;= 神来之笔
   
    rateController &lt;- inputDStream.rateController
  } ssc.addStreamingListener(rateController)

  listenerBus.start()
  
  receiverTracker = new ReceiverTracker(ssc)        &lt;= 神来之笔
  
  inputInfoTracker = new InputInfoTracker(ssc)  &lt;= 神来之笔 用于管理所有的输入流以及输入的数据统计

  val executorAllocClient: ExecutorAllocationClient = ssc.sparkContext.schedulerBackend match {
    case b: ExecutorAllocationClient =&gt; b.asInstanceOf[ExecutorAllocationClient]
    case _ =&gt; null
  }

  executorAllocationManager = ExecutorAllocationManager.createIfEnabled(
    executorAllocClient,
    receiverTracker,
    ssc.conf,
    ssc.graph.batchDuration.milliseconds,
    clock)
  executorAllocationManager.foreach(ssc.addStreamingListener)
  
  receiverTracker.start()                         &lt;= 神来之笔
  jobGenerator.start()                            &lt;= 神来之笔
  
  executorAllocationManager.foreach(_.start())
  logInfo("Started JobScheduler")
}
</code></pre>
</li>
</ul>
<h3><a id="53_JobScheduler_395"></a>5.3 JobScheduler的耳听八方</h3>
<pre><code>    private var eventLoop: EventLoop[JobSchedulerEvent] = null

    eventLoop = new EventLoop[JobSchedulerEvent]("JobScheduler") {
      override protected def onReceive(event: JobSchedulerEvent): Unit = processEvent(event)

      override protected def onError(e: Throwable): Unit = reportError("Error in job scheduler", e)
    }
</code></pre>
<ul>
<li>
<p>JobStarted</p>
</li>
<li>
<p>JobCompleted</p>
</li>
<li>
<p>ErrorReported</p>
<pre><code>   private def processEvent(event: JobSchedulerEvent) {
      try {
        event match {
          case JobStarted(job, startTime) =&gt; handleJobStart(job, startTime)
          case JobCompleted(job, completedTime) =&gt; handleJobCompletion(job, completedTime)
          case ErrorReported(m, e) =&gt; handleError(m, e)
        }
      } catch {
        case e: Throwable =&gt;
          reportError("Error in job scheduler", e)
      }
    }
</code></pre>
</li>
</ul>
<h2><a id="6__422"></a>6 总结</h2>
<p>本文重点解剖了StreamingContext启动流程及Dtream 模板源码，没有参考任何网上博客，郑重声明为原创内容，禁止转载或用于商业用途。</p>
<p>秦凯新  于深圳   2018</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>