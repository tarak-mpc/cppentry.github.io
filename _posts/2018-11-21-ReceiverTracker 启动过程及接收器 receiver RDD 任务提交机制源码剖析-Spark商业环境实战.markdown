---
layout:     post
title:      ReceiverTracker 启动过程及接收器 receiver RDD 任务提交机制源码剖析-Spark商业环境实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本套技术专栏是作者（秦凯新）平时工作的总结和升华，通过从真实商业环境抽取案例进行总结和分享，并给出商业应用的调优建议和集群环境容量规划等内容，请持续关注本套博客。版权声明：禁止转载，欢迎学习。QQ邮箱地址：1120746959@qq.com，如有任何商业交流，可随时联系。					https://blog.csdn.net/shenshouniu/article/details/83927984				</div>
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
<li><a href="https://juejin.im/post/5bdb0e76e51d456c6f1cc45f" rel="nofollow">Spark商业环境实战-ReceiverTracker 启动过程及接收器 receiver RDD 任务提交机制源码剖析</a></li>
<li><a href="https://juejin.im/post/5be677946fb9a049ca36db6f" rel="nofollow">Spark商业环境实战-SparkStreaming数据流从currentBuffer到Block定时转化过程源码深度剖析</a></li>
<li><a href="https://juejin.im/post/5be6ac236fb9a049a42e8e37" rel="nofollow">Spark商业环境实战-SparkStreaming之JobGenerator周期性任务数据处理逻辑源码深度剖析</a></li>
<li>[Spark商业环境实战-SparkStreaming Graph 处理链迭代过程源码深度剖析]</li>
</ul>
<h2><a id="1_ReceiverTracker_is_What_21"></a>1 ReceiverTracker is What?</h2>
<ul>
<li>在Driver端需要有一个跟踪器ReceiverTracker，而这个跟踪器会不断监督Executor启动Receiver，(比如：发送Receiver  Rdd 给Executor, 注意是把Receiver作为RDD给Executor)，同时管理待分配给Job的数据Block的元数据。</li>
<li>ReceiverTracker只是一个全局管理者，负责Block元数据的管理。</li>
<li>因为在Executor上启动的是Receiver，Receiver的启动是连续不断地，通过把离散数据转化为batch，再进一步转化为Block，最终经由存储体系BlockManager纳管起来。最后通知Driver端ReceiverTracker已保存的Blockd的元数据信息。</li>
</ul>
<h2><a id="1_Receiver_is_What_28"></a>1 Receiver is What?</h2>
<pre><code> * Abstract class of a receiver that can be run on worker nodes to receive external data. A
 * custom receiver can be defined by defining the functions `onStart()` and `onStop()`. `onStart()`
 * should define the setup steps necessary to start receiving data,
 * and `onStop()` should define the cleanup steps necessary to stop receiving data.
 * Exceptions while receiving can be handled either by restarting the receiver with `restart(...)`
 * or stopped completely by `stop(...)`.
</code></pre>
<h3><a id="11_Receiver_37"></a>1.1 Receiver超级模板</h3>
<ul>
<li>
<p>_supervisor ：ReceiverSupervisor 启动时威力无穷，坐拥乾坤，主要管理Executor端的数据存储。（下一节重点剖析）</p>
</li>
<li>
<p>store() :顾名思义，就是数据流batch 存储</p>
<pre><code>  @DeveloperApi
  abstract class Receiver[T](val storageLevel: StorageLevel) extends Serializable {
  
    /**
     * This method is called by the system when the receiver is started. This function
     * must initialize all resources (threads, buffers, etc.) necessary for receiving data.
     * This function must be non-blocking, so receiving the data must occur on a different
     * thread. Received data can be stored with Spark by calling `store(data)`.
     *
     * If there are errors in threads started here, then following options can be done
     * (i) `reportError(...)` can be called to report the error to the driver.
     * The receiving of data will continue uninterrupted.
     * (ii) `stop(...)` can be called to stop receiving data. This will call `onStop()` to
     * clear up all resources allocated (threads, buffers, etc.) during `onStart()`.
     * (iii) `restart(...)` can be called to restart the receiver. This will call `onStop()`
     * immediately, and then `onStart()` after a delay.
     */
    def onStart(): Unit
  
    /**
     * This method is called by the system when the receiver is stopped. All resources
     * (threads, buffers, etc.) set up in `onStart()` must be cleaned up in this method.
     */
    def onStop(): Unit
  
    /** Override this to specify a preferred location (hostname). */
    def preferredLocation: Option[String] = None
  
    /**
     * Store a single item of received data to Spark's memory.
     * These single items will be aggregated together into data blocks before
     * being pushed into Spark's memory.
     */
    def store(dataItem: T) {
      supervisor.pushSingle(dataItem)
    }
  
    /** Store an ArrayBuffer of received data as a data block into Spark's memory. */
    def store(dataBuffer: ArrayBuffer[T]) {
      supervisor.pushArrayBuffer(dataBuffer, None, None)
    }
  
    /**
     * Store an ArrayBuffer of received data as a data block into Spark's memory.
     * The metadata will be associated with this block of data
     * for being used in the corresponding InputDStream.
     */
    def store(dataBuffer: ArrayBuffer[T], metadata: Any) {
      supervisor.pushArrayBuffer(dataBuffer, Some(metadata), None)
    }
  
    /** Store an iterator of received data as a data block into Spark's memory. */
    def store(dataIterator: Iterator[T]) {
      supervisor.pushIterator(dataIterator, None, None)
    }
  
    /**
     * Store an iterator of received data as a data block into Spark's memory.
     * The metadata will be associated with this block of data
     * for being used in the corresponding InputDStream.
     */
    def store(dataIterator: java.util.Iterator[T], metadata: Any) {
      supervisor.pushIterator(dataIterator.asScala, Some(metadata), None)
    }
  
    /** Store an iterator of received data as a data block into Spark's memory. */
    def store(dataIterator: java.util.Iterator[T]) {
      supervisor.pushIterator(dataIterator.asScala, None, None)
    }
  
    /**
     * Store an iterator of received data as a data block into Spark's memory.
     * The metadata will be associated with this block of data
     * for being used in the corresponding InputDStream.
     */
    def store(dataIterator: Iterator[T], metadata: Any) {
      supervisor.pushIterator(dataIterator, Some(metadata), None)
    }
  
    /**
     * Store the bytes of received data as a data block into Spark's memory. Note
     * that the data in the ByteBuffer must be serialized using the same serializer
     * that Spark is configured to use.
     */
    def store(bytes: ByteBuffer) {
      supervisor.pushBytes(bytes, None, None)
    }
  
    /**
     * Store the bytes of received data as a data block into Spark's memory.
     * The metadata will be associated with this block of data
     * for being used in the corresponding InputDStream.
     */
    def store(bytes: ByteBuffer, metadata: Any) {
      supervisor.pushBytes(bytes, Some(metadata), None)
    }
  
    /** Report exceptions in receiving data. */
    def reportError(message: String, throwable: Throwable) {
      supervisor.reportError(message, throwable)
    }
  
    /**
     * Restart the receiver. This method schedules the restart and returns
     * immediately. The stopping and subsequent starting of the receiver
     * (by calling `onStop()` and `onStart()`) is performed asynchronously
     * in a background thread. The delay between the stopping and the starting
     * is defined by the Spark configuration `spark.streaming.receiverRestartDelay`.
     * The `message` will be reported to the driver.
     */
    def restart(message: String) {
      supervisor.restartReceiver(message)
    }
  
    /**
     * Restart the receiver. This method schedules the restart and returns
     * immediately. The stopping and subsequent starting of the receiver
     * (by calling `onStop()` and `onStart()`) is performed asynchronously
     * in a background thread. The delay between the stopping and the starting
     * is defined by the Spark configuration `spark.streaming.receiverRestartDelay`.
     * The `message` and `exception` will be reported to the driver.
     */
    def restart(message: String, error: Throwable) {
      supervisor.restartReceiver(message, Some(error))
    }
  
    /**
     * Restart the receiver. This method schedules the restart and returns
     * immediately. The stopping and subsequent starting of the receiver
     * (by calling `onStop()` and `onStart()`) is performed asynchronously
     * in a background thread.
     */
    def restart(message: String, error: Throwable, millisecond: Int) {
      supervisor.restartReceiver(message, Some(error), millisecond)
    }
  
    /** Stop the receiver completely. */
    def stop(message: String) {
      supervisor.stop(message, None)
    }
  
    /** Stop the receiver completely due to an exception */
    def stop(message: String, error: Throwable) {
      supervisor.stop(message, Some(error))
    }
  
    /** Check if the receiver has started or not. */
    def isStarted(): Boolean = {
      supervisor.isReceiverStarted()
    }
  
    /**
     * Check if receiver has been marked for stopping. Use this to identify when
     * the receiving of data should be stopped.
     */
    def isStopped(): Boolean = {
      supervisor.isReceiverStopped()
    }
  
    /**
     * Get the unique identifier the receiver input stream that this
     * receiver is associated with.
     */
    def streamId: Int = id
  
    /*
     * =================
     * Private methods
     * =================
     */
  
    /** Identifier of the stream this receiver is associated with. */
    private var id: Int = -1
  
    /** Handler object that runs the receiver. This is instantiated lazily in the worker. */
    @transient private var _supervisor: ReceiverSupervisor = null
  
    /** Set the ID of the DStream that this receiver is associated with. */
    private[streaming] def setReceiverId(_id: Int) {
      id = _id
    }
  
    /** Attach Network Receiver executor to this receiver. */
    private[streaming] def attachSupervisor(exec: ReceiverSupervisor) {
      assert(_supervisor == null)
      _supervisor = exec
    }
  
    /** Get the attached supervisor. */
    private[streaming] def supervisor: ReceiverSupervisor = {
      assert(_supervisor != null,
        "A ReceiverSupervisor has not been attached to the receiver yet. Maybe you are starting " +
          "some computation in the receiver before the Receiver.onStart() has been called.")
      _supervisor
    }
</code></pre>
</li>
</ul>
<h3><a id="12_Receiver_238"></a>1.2 Receiver的继承关系</h3>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/10/166fba6da0521ef8?w=631&amp;h=303&amp;f=png&amp;s=51321" alt=""></p>
<h2><a id="2__ReceiverTracker__242"></a>2  ReceiverTracker 深度剖析</h2>
<h3><a id="21_ReceiverTracker__244"></a>2.1 ReceiverTracker 的启动流程</h3>
<ul>
<li>
<p>建立通讯终端，方便整个Streaming状态监控。</p>
</li>
<li>
<p>launchReceivers() ：自己给自己 endpoint 终端发送StartAllReceivers，然后启动Receiver RDD，把它作为Job，然后由SparkContext提交Job，最后Receiver 就可以在Executor上启动。</p>
<pre><code>   /** Start the endpoint and receiver execution thread. */
   def start(): Unit = synchronized {
     if (isTrackerStarted) {
       throw new SparkException("ReceiverTracker already started")
     }
 
     if (!receiverInputStreams.isEmpty) {
     
       endpoint = ssc.env.rpcEnv.setupEndpoint(
         "ReceiverTracker", new ReceiverTrackerEndpoint(ssc.env.rpcEnv))
         
       if (!skipReceiverLaunch) launchReceivers()      &lt;= 神来之笔
       
       logInfo("ReceiverTracker started")
       trackerState = Started
     }
   }
</code></pre>
</li>
</ul>
<h3><a id="22_ReceiverTrackerlaunchReceivers__267"></a>2.2 ReceiverTracker.launchReceivers 是一个信使</h3>
<p>作为信使，我就是要发StartAllReceivers(receivers)消息。</p>
<ul>
<li>Get the receivers from the ReceiverInputDStreams, distributes them to the</li>
<li>worker nodes as a parallel collection, and runs them.<br>
private def launchReceivers(): Unit = {</li>
</ul>
<pre><code>val receivers = receiverInputStreams.map { nis =&gt;
  val rcvr = nis.getReceiver()
  rcvr.setReceiverId(nis.id)
  rcvr
}                                                          &lt;= 神来之笔

runDummySparkJob()

logInfo("Starting " + receivers.length + " receivers")

endpoint.send(StartAllReceivers(receivers))                &lt;= 神来之笔
</code></pre>
<p>}</p>
<h3><a id="22_ReceiverTracker__288"></a>2.2 ReceiverTracker 接收自己的消息并处理</h3>
<pre><code>    /** RpcEndpoint to receive messages from the receivers. */
      private class ReceiverTrackerEndpoint(override val rpcEnv: RpcEnv) extends ThreadSafeRpcEndpoint {
    
        private val walBatchingThreadPool = ExecutionContext.fromExecutorService(
          ThreadUtils.newDaemonCachedThreadPool("wal-batching-thread-pool"))
    
        @volatile private var active: Boolean = true
    
        override def receive: PartialFunction[Any, Unit] = {
        
          // Local messages
          case StartAllReceivers(receivers) =&gt;
          
            val scheduledLocations = schedulingPolicy.scheduleReceivers(receivers, getExecutors) 
               &lt;= 神来之笔（以最优位置推荐，确定receiver在哪个Executor上启动）
          
            
            for (receiver &lt;- receivers) {
            
              val executors = scheduledLocations(receiver.streamId)          &lt;= 神来之笔
              updateReceiverScheduledExecutors(receiver.streamId, executors)
              receiverPreferredLocations(receiver.streamId) = receiver.preferredLocation
              
              
              startReceiver(receiver, executors)        &lt;= 神来之笔（循环所有receiver以最优位置启动）
              
              
            }
          case RestartReceiver(receiver) =&gt;
            // Old scheduled executors minus the ones that are not active any more
            val oldScheduledExecutors = getStoredScheduledExecutors(receiver.streamId)
            val scheduledLocations = if (oldScheduledExecutors.nonEmpty) {
                // Try global scheduling again
                oldScheduledExecutors
              } else {
                val oldReceiverInfo = receiverTrackingInfos(receiver.streamId)
                // Clear "scheduledLocations" to indicate we are going to do local scheduling
                val newReceiverInfo = oldReceiverInfo.copy(
                  state = ReceiverState.INACTIVE, scheduledLocations = None)
                receiverTrackingInfos(receiver.streamId) = newReceiverInfo
                schedulingPolicy.rescheduleReceiver(
                  receiver.streamId,
                  receiver.preferredLocation,
                  receiverTrackingInfos,
                  getExecutors)
              }
            // Assume there is one receiver restarting at one time, so we don't need to update
            // receiverTrackingInfos
            startReceiver(receiver, scheduledLocations)
          case c: CleanupOldBlocks =&gt;
            receiverTrackingInfos.values.flatMap(_.endpoint).foreach(_.send(c))
          case UpdateReceiverRateLimit(streamUID, newRate) =&gt;
            for (info &lt;- receiverTrackingInfos.get(streamUID); eP &lt;- info.endpoint) {
              eP.send(UpdateRateLimit(newRate))
            }
          // Remote messages
          case ReportError(streamId, message, error) =&gt;
            reportError(streamId, message, error)
        }
</code></pre>
<h3><a id="22_ReceiverTrackerreceiverExecutor_352"></a>2.2 ReceiverTracker发射receiver到Executor</h3>
<ul>
<li>
<p>startReceiverFunc ：receiverRDD的func 函数，主要执行在Executor中，startReceiverFunc方法体中包含需要启动的 ReceiverSupervisorImpl ，ReceiverSupervisorImpl是具体的数据接收执行者。内部封装了BlockGenerator：</p>
<pre><code>       private val registeredBlockGenerators = new ConcurrentLinkedQueue[BlockGenerator]()
</code></pre>
</li>
<li>
<p>BlockGenerator 中的 blockIntervalTimer 和 blockPushingThread 负责了整个实时数据流的batch到Block的过程。</p>
</li>
<li>
<p>startReceiver 核心代码段，从receiverRDD到Job提交，最终executor端的ReceiverSupervisorImpl.start()</p>
<pre><code>   * Start a receiver along with its scheduled executors
    private def startReceiver(
      receiver: Receiver[_],
      scheduledLocations: Seq[TaskLocation]): Unit = {
      
    def shouldStartReceiver: Boolean = {
      // It's okay to start when trackerState is Initialized or Started
      !(isTrackerStopping || isTrackerStopped)
    }

    val receiverId = receiver.streamId
    if (!shouldStartReceiver) {
      onReceiverJobFinish(receiverId)
      return
    }

    val checkpointDirOption = Option(ssc.checkpointDir)
    val serializableHadoopConf =
      new SerializableConfiguration(ssc.sparkContext.hadoopConfiguration)

    // Function to start the receiver on the worker node
    val startReceiverFunc: Iterator[Receiver[_]] =&gt; Unit =
      (iterator: Iterator[Receiver[_]]) =&gt; {
        if (!iterator.hasNext) {
          throw new SparkException(
            "Could not start receiver as object not found.")
        }
        if (TaskContext.get().attemptNumber() == 0) {
          val receiver = iterator.next()
          assert(iterator.hasNext == false)
          val supervisor = new ReceiverSupervisorImpl(
            receiver, SparkEnv.get, serializableHadoopConf.value, checkpointDirOption)
          supervisor.start()
          supervisor.awaitTermination()
        } else {
          // It's restarted by TaskScheduler, but we want to reschedule it again. So exit it.
        }
      }


    // Create the RDD using the scheduledLocations to run the receiver in a Spark job
    val receiverRDD: RDD[Receiver[_]] =
      if (scheduledLocations.isEmpty) {
        ssc.sc.makeRDD(Seq(receiver), 1)
      } else {
        val preferredLocations = scheduledLocations.map(_.toString).distinct
        ssc.sc.makeRDD(Seq(receiver -&gt; preferredLocations))
      }
    receiverRDD.setName(s"Receiver $receiverId")
    ssc.sparkContext.setJobDescription(s"Streaming job running receiver $receiverId")
    ssc.sparkContext.setCallSite(Option(ssc.getStartSite()).getOrElse(Utils.getCallSite()))

    val future = ssc.sparkContext.submitJob[Receiver[_], Unit, Unit](
      receiverRDD, startReceiverFunc, Seq(0), (_, _) =&gt; Unit, ())
    // We will keep restarting the receiver job until ReceiverTracker is stopped
    future.onComplete {
      case Success(_) =&gt;
        if (!shouldStartReceiver) {
          onReceiverJobFinish(receiverId)
        } else {
          logInfo(s"Restarting Receiver $receiverId")
          self.send(RestartReceiver(receiver))
        }
      case Failure(e) =&gt;
        if (!shouldStartReceiver) {
          onReceiverJobFinish(receiverId)
        } else {
          logError("Receiver has been stopped. Try to restart it.", e)
          logInfo(s"Restarting Receiver $receiverId")
          self.send(RestartReceiver(receiver))
        }
    }(ThreadUtils.sameThread)
    logInfo(s"Receiver ${receiver.streamId} started")
  }
</code></pre>
</li>
<li>
<p>ReceiverTracker 内部 receiver RDD的架构图</p>
</li>
</ul>
<p>下图深度剖析了ReceiverTracker中如何实现 receiver RDD 的Job提交流程<br>
<img src="https://user-gold-cdn.xitu.io/2018/11/10/166fc33fdf92a45f?w=1204&amp;h=499&amp;f=png&amp;s=73323" alt=""></p>
<h2><a id="3__444"></a>3 总结</h2>
<p>最终Driver端的ReceiverTracker 负责把receiver在不同的Executor中启动，从而使得receiver可以不间断的进行数据的批处理存储及BlcokManger管理。</p>
<p>秦凯新 于深圳 2018</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>