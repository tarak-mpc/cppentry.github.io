---
layout:     post
title:      SparkStreaming之JobGenerator周期性任务数据处理逻辑源码深度剖析-Spark商业环境实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本套技术专栏是作者（秦凯新）平时工作的总结和升华，通过从真实商业环境抽取案例进行总结和分享，并给出商业应用的调优建议和集群环境容量规划等内容，请持续关注本套博客。版权声明：禁止转载，欢迎学习。QQ邮箱地址：1120746959@qq.com，如有任何商业交流，可随时联系。					https://blog.csdn.net/shenshouniu/article/details/83931920				</div>
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
<h2><a id="1_JobGenerator_22"></a>1 JobGenerator的前世</h2>
<h3><a id="11_JobGeneratorReceiverTracker_24"></a>1.1 JobGenerator的难兄难弟ReceiverTracker</h3>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/10/166fc58eee067bf2?w=1273&amp;h=593&amp;f=png&amp;s=92689" alt=""></p>
<h3><a id="12_ReceiverTracker_JobGenerator_27"></a>1.2 ReceiverTracker 的难兄难弟JobGenerator</h3>
<p>JobGenerator周期性的不断产生Job，最终Job会在Executor上执行处理。</p>
<h3><a id="13_ReceiverTrackerreceivedBlockTracker__32"></a>1.3 ReceiverTracker与receivedBlockTracker 的相爱相杀</h3>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/10/166fda62f881fd70?w=1072&amp;h=465&amp;f=png&amp;s=77563" alt=""></p>
<ul>
<li>
<p>我们可以看到receivedBlockTracker包含在ReceiverTracker，最重要的是receivedBlockTracker内部维护了一个  streamIdToUnallocatedBlockQueues，用于追踪Executor上报上来的Block。</p>
<pre><code>  class ReceiverTracker(ssc: StreamingContext, skipReceiverLaunch: Boolean = false) extends Logging {
    private val receiverInputStreams = ssc.graph.getReceiverInputStreams()
    private val receiverInputStreamIds = receiverInputStreams.map { _.id }
    private val receivedBlockTracker = new ReceivedBlockTracker(
      ssc.sparkContext.conf,
      ssc.sparkContext.hadoopConfiguration,
      receiverInputStreamIds,
      ssc.scheduler.clock,
      ssc.isCheckpointPresent,
      Option(ssc.checkpointDir)
    )
</code></pre>
</li>
<li>
<p>receivedBlockTracker内部重要的元数据存储结构：</p>
<pre><code>  private val streamIdToUnallocatedBlockQueues = new mutable.HashMap[Int, ReceivedBlockQueue]
</code></pre>
</li>
</ul>
<h3><a id="14_StreamingContext_55"></a>1.4 StreamingContext如何双剑合璧</h3>
<p>JobScheduler里面包含核心的重量级成员，分别是：jobGenerator 和 receiverTracker。其中初始化如下：</p>
<h4><a id="jobGeneratorJobScheduler_58"></a>注意：jobGenerator中构造函数是JobScheduler</h4>
<pre><code>private val jobGenerator = new JobGenerator(this)

receiverTracker = new ReceiverTracker(ssc)
</code></pre>
<p><img src="https://img-blog.csdnimg.cn/20181111102758215.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoZW5zaG91bml1,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<h2><a id="2_JobGenerator_69"></a>2 JobGenerator的今生</h2>
<ul>
<li>
<h5><a id="JobGenerator_RecurringTimer_71"></a>JobGenerator 中重要成员RecurringTimer，负责用户定义时间窗的触发</h5>
<pre><code>  private val timer = new RecurringTimer(clock, ssc.graph.batchDuration.milliseconds,
      longTime =&gt; eventLoop.post(GenerateJobs(new Time(longTime))), "JobGenerator")
</code></pre>
</li>
<li>
<h5><a id="JobGenerator_StreamingContextstartFirstTime_76"></a>JobGenerator 的启动，通过StreamingContext来触发，最终调用startFirstTime</h5>
<pre><code>def start(): Unit = synchronized {
  if (eventLoop != null) return // generator has already been started
  // Call checkpointWriter here to initialize it before eventLoop uses it to avoid a deadlock.
  // See SPARK-10125
  checkpointWriter

  eventLoop = new EventLoop[JobGeneratorEvent]("JobGenerator") {
    override protected def onReceive(event: JobGeneratorEvent): Unit = processEvent(event)

    override protected def onError(e: Throwable): Unit = {
      jobScheduler.reportError("Error in job generator", e)
    }
  }
  eventLoop.start()

  if (ssc.isCheckpointPresent) {
    restart()
  } else {
    startFirstTime()
  }
}
</code></pre>
</li>
<li>
<h5><a id="JobGenerator_sscgraphtimer_100"></a>JobGenerator 最终启动ssc.graph和timer，因此整个处理逻辑开始启动了。</h5>
<pre><code>  private def startFirstTime() {
      val startTime = new Time(timer.getStartTime())
      graph.start(startTime - graph.batchDuration)
      timer.start(startTime.milliseconds)
      logInfo("Started JobGenerator at " + startTime)
    }
</code></pre>
</li>
</ul>
<h3><a id="21_JobGenerator4_110"></a>2.1 JobGenerator的4步核心处理逻辑</h3>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/10/166fdae50cfd821f?w=1032&amp;h=537&amp;f=png&amp;s=104550" alt=""></p>
<h3><a id="22_allocateBlocksToBatch_114"></a>2.2 第一步：allocateBlocksToBatch</h3>
<ul>
<li>
<p>JobGenerator持有jobScheduler的引用，jobScheduler持有receiverTracker的引用</p>
<pre><code>   jobScheduler.receiverTracker.allocateBlocksToBatch(time) // allocate received blocks to batch
</code></pre>
</li>
<li>
<p>receiverTracker持有receivedBlockTracker的引用</p>
</li>
<li>
<p>从streamIdToUnallocatedBlockQueues取出streamId对应的所有间隔为200ms（default）采集的block，并把它放到timeToAllocatedBlocks中。</p>
<pre><code>   * Allocate all unallocated blocks to the given batch.
   * This event will get written to the write ahead log (if enabled).
 
    def allocateBlocksToBatch(batchTime: Time): Unit = synchronized {
      if (lastAllocatedBatchTime == null || batchTime &gt; lastAllocatedBatchTime) {
      
      
      (首先按照用户设置的时间窗，从streamIdToUnallocatedBlockQueues取出所有的Block)
        val streamIdToBlocks = streamIds.map { streamId =&gt;
            (streamId, getReceivedBlockQueue(streamId).dequeueAll(x =&gt; true))
        }.toMap                                                   &lt;=点睛之笔
        
        （然后把未分配用户指定时间窗的block放进timeToAllocatedBlocks）
        val allocatedBlocks = AllocatedBlocks(streamIdToBlocks)    &lt;=点睛之笔
        
        
        if (writeToLog(BatchAllocationEvent(batchTime, allocatedBlocks))) {
          timeToAllocatedBlocks.put(batchTime, allocatedBlocks)
          lastAllocatedBatchTime = batchTime
        } else {
          logInfo(s"Possibly processed batch $batchTime needs to be processed again in WAL recovery")
        }
      } else {
        // This situation occurs when:
        // 1. WAL is ended with BatchAllocationEvent, but without BatchCleanupEvent,
        // possibly processed batch job or half-processed batch job need to be processed again,
        // so the batchTime will be equal to lastAllocatedBatchTime.
        // 2. Slow checkpointing makes recovered batch time older than WAL recovered
        // lastAllocatedBatchTime.
        // This situation will only occurs in recovery time.
        logInfo(s"Possibly processed batch $batchTime needs to be processed again in WAL recovery")
      }
    }
</code></pre>
</li>
<li>
<h5><a id="timeToAllocatedBlocks__receiverTrackerreceivedBlockTrackertimeToAllocatedBlocksgeneratedRDDs_159"></a>timeToAllocatedBlocks 是 receiverTracker（成员receivedBlockTracker）中包含的核心成员，反向迭代到调用链最顶端，根据timeToAllocatedBlocks来生成generatedRDDs</h5>
</li>
<li>
<h5><a id="streamIdToUnallocatedBlockQueues_Block_161"></a>streamIdToUnallocatedBlockQueues ：没有被分配批次的Block集合</h5>
</li>
<li>
<h5><a id="timeToAllocatedBlocks_block_162"></a>timeToAllocatedBlocks :已经被分配批次的block集合</h5>
</li>
<li>
<h5><a id="DStreamRDDgetOrComputeDStreamRDDgeneratedRDDs_163"></a>下面是DStream的模板代码，就是为了生成RDD来使用的，getOrCompute方法只有DStream有，所以上一级生成RDD后，就会放入generatedRDDs中。</h5>
</li>
<li>
<h5><a id="generatedRDDs_computeComputegetOrComputegetOrComputeComputeInputDStreamCompute_164"></a>generatedRDDs 中没有，就会调用compute，而Compute又会调用getOrCompute。getOrCompute又会调用Compute，反反复复进行一直回溯到InputDStream的Compute</h5>
<pre><code> * Get the RDD corresponding to the given time; either retrieve it from cache
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
        
        generatedRDDs.put(time, newRDD)                           &lt;=点睛之笔
        
      }
      rddOption
    } else {
      None
    }
  }
}
</code></pre>
</li>
<li>
<h5><a id="MapPartitionedDStreamcompute_210"></a>MapPartitionedDStream的compute方法</h5>
<pre><code>   override def compute(validTime: Time): Option[RDD[U]] = {
      parent.getOrCompute(validTime).map(_.mapPartitions[U](mapPartFunc, preservePartitioning))
    }
</code></pre>
</li>
<li>
<h5><a id="eceiverInputDstreamcompute_216"></a>eceiverInputDstream中的compute方法</h5>
<pre><code>   * Generates RDDs with blocks received by the receiver of this stream. 
   
        override def compute(validTime: Time): Option[RDD[T]] = {
          val blockRDD = {
  
        if (validTime &lt; graph.startTime) {
          // If this is called for any time before the start time of the context,
          // then this returns an empty RDD. This may happen when recovering from a
          // driver failure without any write ahead log to recover pre-failure data.
          new BlockRDD[T](ssc.sc, Array.empty)
        } else {
        
        
          // Otherwise, ask the tracker for all the blocks that have been allocated to this stream
          // for this batch
          
          （主要从timeToAllocatedBlocks中取出数据）
          val receiverTracker = ssc.scheduler.receiverTracker                    &lt;=点睛之笔
          val blockInfos = receiverTracker.getBlocksOfBatch(validTime).getOrElse(id, Seq.empty)    &lt;=点睛之笔
  
          // Register the input blocks information into InputInfoTracker
          val inputInfo = StreamInputInfo(id, blockInfos.flatMap(_.numRecords).sum)
          ssc.scheduler.inputInfoTracker.reportInfo(validTime, inputInfo)
  
         （主要从timeToAllocatedBlocks中取出数据，构建RDD，方便后续调用链使用generatedRDDs）
          // Create the BlockRDD 
          createBlockRDD(validTime, blockInfos)                       &lt;=点睛之笔                 
        }
      }
      Some(blockRDD)
    }
</code></pre>
</li>
</ul>
<p>可见 allocateBlocksToBatch的作用就是为了把对应窗的Block放进timeToAllocatedBlocks。方便调用链使用。</p>
<h3><a id="23_graphgenerateJobs_251"></a>2.3 第二步：graph.generateJobs</h3>
<ul>
<li>
<h5><a id="DStreamGraphoutputStreams_253"></a>DStreamGraph的核心作用是注册了outputStreams，那么是什么时候注册的呢？</h5>
</li>
<li>
<h5><a id="Action__print__foreachRDD__ForEachDStream__register__sscgraphaddOutputStreamthis_254"></a>Action函数  print -&gt; foreachRDD -&gt; ForEachDStream -&gt; register -&gt; ssc.graph.addOutputStream(this)</h5>
</li>
<li>
<h5><a id="DStreamGraphgenerateJobs_outputStreamgenerateJobtime_255"></a>DStreamGraph.generateJobs最终调用了 outputStream.generateJob(time)</h5>
<pre><code>  private val inputStreams = new ArrayBuffer[InputDStream[_]]()
  private val outputStreams = new ArrayBuffer[DStream[_]]()
 
  def generateJobs(time: Time): Seq[Job] = {
     logDebug("Generating jobs for time " + time)
     val jobs = this.synchronized {
       outputStreams.flatMap { outputStream =&gt;
         val jobOption = outputStream.generateJob(time)
         jobOption.foreach(_.setCallSite(outputStream.creationSite))
         jobOption
       }
     }
     logDebug("Generated " + jobs.length + " jobs for time " + time)
     jobs
   }
</code></pre>
</li>
<li>
<h5><a id="outputStreamgenerateJobjobFuncnew_Jobtime_jobFunc_273"></a>outputStream.generateJob定义了jobFunc，生成new Job(time, jobFunc)</h5>
<pre><code>   private[streaming] def generateJob(time: Time): Option[Job] = {
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
</li>
</ul>
<h3><a id="24__jobSchedulerinputInfoTrackergetInfotime_287"></a>2.4 第三步： jobScheduler.inputInfoTracker.getInfo(time)</h3>
<ul>
<li>
<h5><a id="Block_289"></a>就是为了对应批次Block的元数据信息</h5>
<p>// Map to track all the InputInfo related to specific batch time and input stream.<br>
private val batchTimeToInputInfos =<br>
new mutable.HashMap[Time, mutable.HashMap[Int, StreamInputInfo]]</p>
<pre><code> case class StreamInputInfo(
     inputStreamId: Int, numRecords: Long, metadata: Map[String, Any] = Map.empty) 
</code></pre>
</li>
</ul>
<h3><a id="25__jobSchedulersubmitJobSetJobSettime_jobs_streamIdToInputInfos_297"></a>2.5 第四步： jobScheduler.submitJobSet(JobSet(time, jobs, streamIdToInputInfos))</h3>
<ul>
<li>
<h5><a id="JobGenerator_JobSchedulerJobExecutor_299"></a>JobGenerator 持有JobScheduler的引用，最终会提交Job的并开始驱动Executor计算。</h5>
<pre><code>def submitJobSet(jobSet: JobSet) {
    if (jobSet.jobs.isEmpty) {
      logInfo("No jobs added for time " + jobSet.time)
    } else {
      listenerBus.post(StreamingListenerBatchSubmitted(jobSet.toBatchInfo))
      jobSets.put(jobSet.time, jobSet)
      jobSet.jobs.foreach(job =&gt; jobExecutor.execute(new JobHandler(job)))
      logInfo("Added jobs for time " + jobSet.time)
    }
  }
</code></pre>
<h3><a id="3__311"></a>3 总结</h3>
</li>
</ul>
<p>本文是作者花大量时间整理而成，请勿做伸手党，禁止转载，欢迎学习，有问题请留言。</p>
<p>秦凯新  于深圳 2018</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>