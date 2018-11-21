---
layout:     post
title:      SparkStreaming数据流从currentBuffer到Block定时转化过程源码深度剖析-Spark商业环境实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本套技术专栏是作者（秦凯新）平时工作的总结和升华，通过从真实商业环境抽取案例进行总结和分享，并给出商业应用的调优建议和集群环境容量规划等内容，请持续关注本套博客。版权声明：禁止转载，欢迎学习。QQ邮箱地址：1120746959@qq.com，如有任何商业交流，可随时联系。					https://blog.csdn.net/shenshouniu/article/details/83929263				</div>
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
<h2><a id="1_ReceiverTracker__22"></a>1 ReceiverTracker 以一发牵动全身</h2>
<ul>
<li>
<p>下图深度剖析了ReceiverTracker中如何实现 receiver RDD 的Job提交流程，右侧黄色底面为本节重点要讲的ReceiverSupervisorImpl。实现了receiver的启动，以及Block的生成等过程。</p>
</li>
<li>
<p>下图可以清晰的看到supervisor作为父类，在StartReceiverFunc，启动了两个start函数：</p>
<pre><code>    /** Start the supervisor */
    def start() {
      onStart()
      startReceiver()
    }
</code></pre>
<p>（1）第一个启动了 ReceiverSupervisorImpl的 onStart()方法，从而启动了registeredBlockGenerators，开启了数据batch的生成和管理。</p>
<pre><code>  override protected def onStart() {
      registeredBlockGenerators.asScala.foreach { _.start() }
    }
</code></pre>
<p>（2）第二个 startReceiver，先调用startReceiver，进一步会调用ReceiverSupervisorImpl的onReceiverStart方法来判断是否成功注册到ReceiverTracker中，若成功则会启动receiver</p>
<pre><code>supervisor的startReceiver方法

def startReceiver(): Unit = synchronized {
  try {
    if (onReceiverStart()) {           &lt;=神来之笔（端点通讯注册Receiver）
    
      logInfo(s"Starting receiver $streamId")
      receiverState = Started
      
      receiver.onStart()                &lt;=神来之笔
      
      logInfo(s"Called receiver $streamId onStart")
    } else {
      // The driver refused us
      stop("Registered unsuccessfully because Driver refused to start receiver " + streamId, None)
    }
  } catch {
    case NonFatal(t) =&gt;
      stop("Error starting receiver " + streamId, Some(t))
  }
}


  ReceiverSupervisorImpl的onReceiverStart方法
   
  override protected def onReceiverStart(): Boolean = {
      val msg = RegisterReceiver(
        streamId, receiver.getClass.getSimpleName, host, executorId, endpoint)
      trackerEndpoint.askSync[Boolean](msg)
    }
</code></pre>
<p>（3）ReceiverTracker的receiver注册请求管理</p>
<pre><code>override def receiveAndReply(context: RpcCallContext): PartialFunction[Any, Unit] = {
  
    // Remote messages
    case RegisterReceiver(streamId, typ, host, executorId, receiverEndpoint) =&gt;
      val successful =
        registerReceiver(streamId, typ, host, executorId, receiverEndpoint, context.senderAddress)                 &lt;=神来之笔 （eceiverTrackingInfos的管理） 
        
      context.reply(successful)
      
    case AddBlock(receivedBlockInfo) =&gt;
      if (WriteAheadLogUtils.isBatchingEnabled(ssc.conf, isDriver = true)) {
        walBatchingThreadPool.execute(new Runnable {
          override def run(): Unit = Utils.tryLogNonFatalError {
            if (active) {
              context.reply(addBlock(receivedBlockInfo))
            } else {
              throw new IllegalStateException("ReceiverTracker RpcEndpoint shut down.")
            }
          }
        })
      } else {
        context.reply(addBlock(receivedBlockInfo))
      }
      
    case DeregisterReceiver(streamId, message, error) =&gt;
      deregisterReceiver(streamId, message, error)
      context.reply(true)

    // Local messages
    case AllReceiverIds =&gt;
      context.reply(receiverTrackingInfos.filter(_._2.state != ReceiverState.INACTIVE).keys.toSeq)
      
    case GetAllReceiverInfo =&gt;
      context.reply(receiverTrackingInfos.toMap)
      
    case StopAllReceivers =&gt;
      assert(isTrackerStopping || isTrackerStopped)
      stopReceivers()
      context.reply(true)
  }
</code></pre>
</li>
</ul>
<p>（4）registerReceiver中如何实现receiverTrackingInfos的管理</p>
<pre><code>     /** Register a receiver */
      private def registerReceiver(
          streamId: Int,
          typ: String,
          host: String,
          executorId: String,
          receiverEndpoint: RpcEndpointRef,
          senderAddress: RpcAddress
        ): Boolean = {
        if (!receiverInputStreamIds.contains(streamId)) {
          throw new SparkException("Register received for unexpected id " + streamId)
        }
    
        if (isTrackerStopping || isTrackerStopped) {
          return false
        }
    
        val scheduledLocations = receiverTrackingInfos(streamId).scheduledLocations
        val acceptableExecutors = if (scheduledLocations.nonEmpty) {
            // This receiver is registering and it's scheduled by
            // ReceiverSchedulingPolicy.scheduleReceivers. So use "scheduledLocations" to check it.
            scheduledLocations.get
          } else {
            // This receiver is scheduled by "ReceiverSchedulingPolicy.rescheduleReceiver", so calling
            // "ReceiverSchedulingPolicy.rescheduleReceiver" again to check it.
            scheduleReceiver(streamId)
          }
    
        def isAcceptable: Boolean = acceptableExecutors.exists {
          case loc: ExecutorCacheTaskLocation =&gt; loc.executorId == executorId
          case loc: TaskLocation =&gt; loc.host == host
        }
    
        if (!isAcceptable) {
          // Refuse it since it's scheduled to a wrong executor
          false
        } else {
          val name = s"${typ}-${streamId}"
          val receiverTrackingInfo = ReceiverTrackingInfo(
            streamId,
            ReceiverState.ACTIVE,
            scheduledLocations = None,
            runningExecutor = Some(ExecutorCacheTaskLocation(host, executorId)),
            name = Some(name),
            endpoint = Some(receiverEndpoint))
            
          receiverTrackingInfos.put(streamId, receiverTrackingInfo)               &lt;=神来之笔
          
          listenerBus.post(StreamingListenerReceiverStarted(receiverTrackingInfo.toReceiverInfo))
          logInfo("Registered receiver for stream " + streamId + " from " + senderAddress)
          true
        }
      }
</code></pre>
<ul>
<li>ReceiverSupervisorImpl总体架构图如下：<br>
<img src="https://user-gold-cdn.xitu.io/2018/11/10/166fc58eee067bf2?w=1273&amp;h=593&amp;f=png&amp;s=92689" alt=""></li>
</ul>
<h2><a id="2_BlockGenerator_174"></a>2 BlockGenerator的深度剖析</h2>
<h3><a id="21_SocketInputDStream__176"></a>2.1 SocketInputDStream 数据流的接收存储过程</h3>
<ul>
<li>
<p>依赖于ReceiverSupervisor</p>
<pre><code>   /** Create a socket connection and receive data until receiver is stopped */
   def receive() {
     try {
       val iterator = bytesToObjects(socket.getInputStream())
       while(!isStopped &amp;&amp; iterator.hasNext) {
       
         store(iterator.next())                  &lt;= 神来之笔
         
       }
       if (!isStopped()) {
         restart("Socket data stream had no more data")
       } else {
         logInfo("Stopped receiving")
       }
     } catch {
       case NonFatal(e) =&gt;
         logWarning("Error receiving data", e)
         restart("Error receiving data", e)
     } finally {
       onStop()
     }
   }
</code></pre>
</li>
<li>
<p>依赖于ReceiverSupervisor的pushSingle方法</p>
<pre><code> * Store a single item of received data to Spark's memory.
 * These single items will be aggregated together into data blocks before
 * being pushed into Spark's memory.

 def store(dataItem: T) {
   supervisor.pushSingle(dataItem)             &lt;= 神来之笔
 }
</code></pre>
</li>
<li>
<p>依赖于ReceiverSupervisor的内部的defaultBlockGenerator</p>
<pre><code>/* Push a single record of received data into block generator. 
def pushSingle(data: Any) {
  defaultBlockGenerator.addData(data)           &lt;= 神来之笔
}
</code></pre>
</li>
</ul>
<h3><a id="22_BlockGenerator_220"></a>2.2 BlockGenerator的重剑无锋</h3>
<ul>
<li>兄弟1：blockIntervalTimer</li>
<li>兄弟2：blockPushingThread</li>
</ul>
<p>BlockGenerator的仗剑走天涯，诗酒趁年华。两大线程解决block存储和管理问题：</p>
<pre><code>private val blockIntervalMs = conf.getTimeAsMs("spark.streaming.blockInterval", "200ms")

一大线程：
private val blockIntervalTimer =
new RecurringTimer(clock, blockIntervalMs, updateCurrentBuffer, "BlockGenerator")

二大线程：
private val blockPushingThread = new Thread() { override def run() { keepPushingBlocks() } }
</code></pre>
<ul>
<li>
<p>俩兄弟上吧</p>
<pre><code>  def start(): Unit = synchronized {
     if (state == Initialized) {
       state = Active

       blockIntervalTimer.start()            &lt;= 神来之笔
       blockPushingThread.start()            &lt;= 神来之笔
       
       logInfo("Started BlockGenerator")
     } else {
       throw new SparkException(
         s"Cannot start BlockGenerator as its not in the Initialized state [state = $state]")
     }
   }    
</code></pre>
</li>
</ul>
<h3><a id="23_BlockGenerator_252"></a>2.3 BlockGenerator的厚积薄发</h3>
<ul>
<li>
<p>积水缓冲缸（存储离散的数据流）（currentBuffer）</p>
<p>@volatile private var currentBuffer = new ArrayBuffer[Any]</p>
</li>
<li>
<p>桶装水（积水缓冲缸的水聚集成桶）（blocksForPushing）</p>
<pre><code>  private val blocksForPushing = new ArrayBlockingQueue[Block](blockQueueSize)
</code></pre>
</li>
<li>
<p>积水缓冲缸通过InputDStream蓄水</p>
<pre><code>* Push a single data item into the buffer.
def addData(data: Any): Unit = {
  if (state == Active) {
    waitToPush()
    synchronized {
      if (state == Active) {
        currentBuffer += data
      } else {
        throw new SparkException(
          "Cannot add data as BlockGenerator has not been started or has been stopped")
      }
    }
  } else {
    throw new SparkException(
      "Cannot add data as BlockGenerator has not been started or has been stopped")
  }
}
</code></pre>
</li>
<li>
<p>blockIntervalTimer把积水缓冲缸转换为桶装水，管理起来</p>
<pre><code>   /** Change the buffer to which single records are added to. */
    private def updateCurrentBuffer(time: Long): Unit = {
      try {
        var newBlock: Block = null
        synchronized {
          if (currentBuffer.nonEmpty) {
            val newBlockBuffer = currentBuffer
            currentBuffer = new ArrayBuffer[Any]
            
            val blockId = StreamBlockId(receiverId, time - blockIntervalMs) &lt;= 神来之笔
            
            listener.onGenerateBlock(blockId)
            
            newBlock = new Block(blockId, newBlockBuffer)         &lt;= 神来之笔   
            
          }
        }
  
        if (newBlock != null) {
        
          blocksForPushing.put(newBlock)  // put is blocking when queue is full    &lt;= 神来之笔
        }
      } catch {
        case ie: InterruptedException =&gt;
          logInfo("Block updating timer thread was interrupted")
        case e: Exception =&gt;
          reportError("Error in block updating thread", e)
      }
    }
</code></pre>
</li>
<li>
<p>keepPushingBlocks 看我搅动风云</p>
</li>
</ul>
<h3><a id="23_BlockGeneratorkeepPushingBlocks_317"></a>2.3 BlockGenerator内部keepPushingBlocks的搅动风云</h3>
<ul>
<li>blockPushingThread线程调用defaultBlockGeneratorListener,通过blockManager.putBytes来存储Block，同时告诉Driver端ReceiverTracker的 AddBlock(blockInfo）信息添加成功。</li>
<li>ReceiverTracker通过上面的端点通信掌握了Block的存储元数据信息。</li>
<li>根据是否开启预读写日志，出现WriteAheadLogBasedBlockHandler和BlockManagerBasedBlockHandler，参数可以通过conf.getBoolean(RECEIVER_WAL_ENABLE_CONF_KEY, false)来配置。</li>
<li>一切的一切都是ReceiverSupervisorImpl来主导的，因为ReceiverSupervisorImpl内部成员包含了registeredBlockGenerators，defaultBlockGeneratorListener，trackerEndpoint等等所有给力干将。所以从receiver启动到block生成，到block的管理，再到上报给ReceiverTracker，真正实现了覆盖一切的角色</li>
</ul>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/10/166fce5e2c971f47?w=1299&amp;h=1052&amp;f=png&amp;s=127032" alt=""></p>
<h2><a id="3__325"></a>3 总结</h2>
<p>我们发现看了一场ReceiverSupervisorImpl的世纪大戏，自导自演解决了端到端的问题。</p>
<p>秦凯新 于深圳  2018</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>