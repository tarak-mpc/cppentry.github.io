---
layout:     post
title:      Spark二级调度系统Stage划分算法和最佳任务调度细节剖析-Spark商业环境实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本套技术专栏是作者（秦凯新）平时工作的总结和升华，通过从真实商业环境抽取案例进行总结和分享，并给出商业应用的调优建议和集群环境容量规划等内容，请持续关注本套博客。版权声明：禁止转载，欢迎学习。QQ邮箱地址：1120746959@qq.com，如有任何商业交流，可随时联系。					https://blog.csdn.net/shenshouniu/article/details/83626157				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3><a id="Spark_1"></a>Spark商业环境实战及调优进阶系列</h3>
<ul>
<li><a href="https://juejin.im/post/5bd55b4de51d4579470247eb" rel="nofollow">Spark商业环境实战-Spark内置框架rpc通讯机制及RpcEnv基础设施 </a></li>
<li><a href="https://juejin.im/post/5bd583f0e51d452c296ef5dd" rel="nofollow">Spark商业环境实战-Spark事件监听总线流程分析</a></li>
<li><a href="https://juejin.im/post/5bd854955188255ca65da7ed" rel="nofollow">Spark商业环境实战-Spark存储体系底层架构剖析</a></li>
<li><a href="https://juejin.im/post/5bd8553af265da0adb311f58" rel="nofollow">Spark商业环境实战-Spark底层多个MessageLoop循环线程执行流程分析</a></li>
<li><a href="https://juejin.im/post/5bd88932f265da0ae5056199" rel="nofollow">Spark商业环境实战-Spark二级调度系统Stage划分算法和最佳任务调度细节剖析</a></li>
<li><a href="https://juejin.im/post/5bdc2676f265da611b57cd20" rel="nofollow">Spark商业环境实战-Spark任务延迟调度及调度池Pool架构剖析</a></li>
<li><a href="https://juejin.im/post/5bdd0a87f265da612859937d" rel="nofollow">Spark商业环境实战-Task粒度的缓存聚合排序结构AppendOnlyMap详细剖析</a></li>
<li><a href="https://juejin.im/post/5bdd3ac86fb9a049d7471f52" rel="nofollow">Spark商业环境实战-ExternalSorter 排序器在Spark Shuffle过程中设计思路剖析</a></li>
<li><a href="https://juejin.im/post/5bdb091ff265da392b2c51bf" rel="nofollow">Spark商业环境实战-StreamingContext启动流程及Dtream 模板源码剖析</a></li>
<li><a href="https://juejin.im/post/5bdb0e76e51d456c6f1cc45f" rel="nofollow">Spark商业环境实战-ReceiverTracker与BlockGenerator数据流接收过程剖析</a></li>
</ul>
<h2><a id="1_Spark_14"></a>1. Spark调度系统的组件关系</h2>
<h3><a id="11_SparkStandAlone_16"></a>1.1 Spark调度系统的组件(以StandAlone模式)</h3>
<ul>
<li>
<p>一级调度：Cluster Manger (YARN模式下为ResourceManger , Standalone 模式下为 Master )负责将资源分配给Application。这里的资源如：cpu核数，内存，磁盘空间等。</p>
</li>
<li>
<p>二级调度：Application 进一步将资源分配给Application的各个Task。DAG任务调度，延迟调度等。</p>
</li>
<li>
<p>任务(Task): Task分为ResultTask和ShuffleMapTask两种，每一个Stage会根据未完成的Partion的多少，创建零到多个Task，DAGScheduer最后将每个Stage中的Task以任务集合（TaskSet）的形式提交给TaskScheduler继续处理。</p>
</li>
<li>
<p>TaskSchedulerImpl ：手握StandaloneSchedulerBackend（StandaloneAppClient）和  CoarseGrainedSchedulerBackend（DriverEndpoint）两大通讯端点神器。操控了整个集群资源的汇报和资源调度。</p>
</li>
<li>
<p>CoarseGrainedSchedulerBackend：维护了一个executorDataMap，用于实时拿到最新的且活着的executor用于资源分配。</p>
</li>
<li>
<p>StandaloneSchedulerBackend： 持有TaskSchedulerImpl的引用，目前来看，就是为了初始化启动StandaloneAppClient和DriverEndpoint终端，通过接受消息，实际干活的还是TaskSchedulerImpl。</p>
</li>
<li>
<p>StandaloneAppClient：在Standalone模式下StandaloneSchedulerBackend在启动的时候构造AppClient实例并在该实例start的时候启动了ClientEndpoint这个消息循环体。ClientEndpoint在启动的时候会向Master注册当前程序。（Interface allowing applications to speak with a Spark standalone cluster manager.）</p>
<h5><a id="1_StandaloneSchedulerBackend__CoarseGrainedSchedulerBackend_TaskSchedulerImplStandaloneSchedulerBackend_28"></a>(1) StandaloneSchedulerBackend 与 CoarseGrainedSchedulerBackend 的前世，可以看到在TaskSchedulerImpl和StandaloneSchedulerBackend相互引用并启动：</h5>
<pre><code>  SparkContext -&gt; createTaskScheduler -&gt; new TaskSchedulerImpl(sc) -&gt;
  new StandaloneSchedulerBackend(scheduler, sc, masterUrls)-&gt; scheduler.initialize(backend)
  _taskScheduler.start()-&gt; backend.start()
</code></pre>
<h5><a id="2_StandaloneAppClient__DriverEndpoint__34"></a>(2) StandaloneAppClient 与 DriverEndpoint 的今生：</h5>
<pre><code>  StandaloneSchedulerBackend-&gt;start()
  
  -&gt; super.start() [CoarseGrainedSchedulerBackend]-&gt; 
  createDriverEndpointRef(properties)-&gt;  createDriverEndpoint(properties) -&gt;
  
  -&gt; new StandaloneAppClient(sc.env.rpcEnv, masters, appDesc, this, conf).start()
</code></pre>
<h5><a id="3_StandaloneAppClient__43"></a>(3) StandaloneAppClient 新官上任</h5>
<pre><code>  onStart -&gt; registerWithMaster -&gt; tryRegisterAllMasters -&gt;  rpcEnv.setupEndpointRef(masterAddress,
  Master.ENDPOINT_NAME) -&gt; masterRef.send(RegisterApplication(appDescription, self))
</code></pre>
<h5><a id="4StandaloneAppClient_48"></a>（4）StandaloneAppClient负责的功能如下：</h5>
<pre><code>      RegisteredApplication（启动时向Master注册）
      ApplicationRemoved
      ExecutorAdded
      ExecutorUpdated
      WorkerRemoved
      MasterChanged
</code></pre>
</li>
<li>
<p>DriverEndpoint：，而StandaloneSchedulerBackend的父类CoarseGrainedSchedulerBackend在start的时候会实例化类型为DriverEndpoint(这就是我们程序运行时候的经典对象的Driver)的消息循环体,StandaloneSchedulerBackend专门负责收集Worker上的资源信息， 当Worker端的ExecutorBackend启动的时候会发送RegisteredExecutor信息向DriverEndpoint注册，</p>
<h5><a id="StandaloneSchedulerBackendTaskSchedulerImplStandaloneSchedulerBackendTask_58"></a>此时StandaloneSchedulerBackend就掌握了当前应用程序拥有的计算资源。TaskSchedulerImpl就是通过StandaloneSchedulerBackend拥有的计算资源来具体运行Task。负责的功能如下：</h5>
<pre><code>  StatusUpdate
  ReviveOffers --&gt;
  KillTask
  KillExecutorsOnHost
  RemoveExecutor
  RegisterExecutor()
  StopDriver
  StopExecutors
  RemoveWorker
</code></pre>
</li>
</ul>
<p>Spark调度系统总体规律：在Standalone模式下StandaloneSchedulerBackend在启动的时候构造AppClient实例并在该实例start的时候启动了ClientEndpoint这个消息循环体。ClientEndpoint在启动的时候会向Master注册当前程序。而StandaloneSchedulerBackend的父类CoarseGrainedSchedulerBackend在start的时候会实例化类型为DriverEndpoint(这就是我们程序运行时候的经典对象的Driver)的消息循环体,StandaloneSchedulerBackend专门负责收集Worker上的资源信息,当ExecutorBackend启动的时候会发送RegisteredExecutor信息向DriverEndpoint注册,此时StandaloneSchedulerBackend就掌握了当前应用程序拥有的计算资源,TaskScheduler就是通过StandaloneSchedulerBackend拥有的计算资源来具体运行Task。</p>
<h2><a id="2_DAGScheduler__71"></a>2. DAGScheduler 核心调度系统</h2>
<h3><a id="21_DAGScheduler__73"></a>2.1 DAGScheduler 核心成员</h3>
<ul>
<li>
<p>TaskSchdulerImpl</p>
</li>
<li>
<p>LiveListenerBus</p>
</li>
<li>
<p>MapoutTrackerMaster</p>
</li>
<li>
<p>BlockManagerMaster</p>
</li>
<li>
<p>SparkEnv</p>
</li>
<li>
<p>cacheLocas：缓存每个RDD的所有分区的位置信息，最终建立分区号和位置信息序列映射。为什么是位置序列？ 这里着重讲解一下：每一个分区可能存在多个副本机制，因此RDD的每一个分区的BLock可能存在多个节点的BlockManager上，因此是序列。听懂了吗？？</p>
<pre><code>    new HashMap[Int, IndexedSeq[Seq[TaskLocation]]]
</code></pre>
</li>
<li>
<p>MessageScheduler：职责是对失败的Stage进行重试，如下面的执行线程代码段:</p>
<pre><code>private val messageScheduler =
 ThreadUtils.newDaemonSingleThreadScheduledExecutor("dag-scheduler-message")

  case FetchFailed -&gt; messageScheduler.schedule(new Runnable {
      override def run(): Unit = eventProcessLoop.post(ResubmitFailedStages)
    }, DAGScheduler.RESUBMIT_TIMEOUT, TimeUnit.MILLISECONDS)
  }
</code></pre>
</li>
<li>
<p>getPreferredLocs：重量级方法，分区最大偏好位置获取。最终把分区最佳偏好位置序列放在cacheLocas中，获取不到，调用rdd.preferredLocations方法获取。</p>
<pre><code>  getPreferredLocs 
  
  -&gt; getPreferredLocsInternal 
  
  -&gt; getCacheLocs(rdd)(partition) ---&gt; cacheLocs(rdd.id) = locs() -&gt; 返回 cached
             (取不到直接放进内存后，再返回偏好序列)
  
  -&gt; val rddPrefs = rdd.preferredLocations(rdd.partitions(partition)).toList
</code></pre>
<p>getCacheLocs 方法代码段：</p>
<pre><code>  def getCacheLocs(rdd: RDD[_]): IndexedSeq[Seq[TaskLocation]] =         
      cacheLocs.synchronized {
  if (!cacheLocs.contains(rdd.id)) {
  val locs: IndexedSeq[Seq[TaskLocation]] = if (rdd.getStorageLevel == StorageLevel.NONE) {
  IndexedSeq.fill(rdd.partitions.length)(Nil)
  } else {
  val blockIds =
  rdd.partitions.indices.map(index =&gt; RDDBlockId(rdd.id, index)).toArray[BlockId]
  blockManagerMaster.getLocations(blockIds).map { bms =&gt;
  bms.map(bm =&gt; TaskLocation(bm.host, bm.executorId))
  }
}
  cacheLocs(rdd.id) = locs
  }
  cacheLocs(rdd.id)
  }
</code></pre>
</li>
<li>
<p>eventProccessLoop：大名鼎鼎的DAGSchedulerEventProcessLoop，事件处理线程，负责处理各种事件，如：</p>
<pre><code>  private def doOnReceive(event: DAGSchedulerEvent): Unit = event match {
  
  case JobSubmitted(jobId, rdd, func, partitions, callSite, listener, properties) =&gt;
  dagScheduler.handleJobSubmitted(jobId, rdd, func, partitions, callSite, listener,
  properties)
  
  case MapStageSubmitted(jobId, dependency, callSite, listener, properties) =&gt;
  dagScheduler.handleMapStageSubmitted(jobId, dependency, callSite, listener, properties)

  case StageCancelled(stageId) =&gt;
  dagScheduler.handleStageCancellation(stageId)

  case JobCancelled(jobId) =&gt;
  dagScheduler.handleJobCancellation(jobId)

  case JobGroupCancelled(groupId) =&gt;
  dagScheduler.handleJobGroupCancelled(groupId)

  case AllJobsCancelled =&gt;
  dagScheduler.doCancelAllJobs()

  case ExecutorAdded(execId, host) =&gt;
  dagScheduler.handleExecutorAdded(execId, host)

  case ExecutorLost(execId) =&gt;
  dagScheduler.handleExecutorLost(execId, fetchFailed = false)

   case BeginEvent(task, taskInfo) =&gt;
   dagScheduler.handleBeginEvent(task, taskInfo)

  case GettingResultEvent(taskInfo) =&gt;
  dagScheduler.handleGetTaskResult(taskInfo)

  case completion: CompletionEvent =&gt;
  dagScheduler.handleTaskCompletion(completion)

  case TaskSetFailed(taskSet, reason, exception) =&gt;
  dagScheduler.handleTaskSetFailed(taskSet, reason, exception)

  case ResubmitFailedStages =&gt;
  dagScheduler.resubmitFailedStages()
</code></pre>
</li>
</ul>
<h3><a id="22_DAGScheduler__job__job_167"></a>2.2 DAGScheduler 与 job 的 牵手缘分，一个job的前世今生</h3>
<h5><a id="1_DAGjob_169"></a>(1) DAG的有向无环图切分及任务调度job提交流程(以下非代码堆积，从上往下看逻辑核心流程)：</h5>
<pre><code>    -&gt; DAGScheduler.runJob-&gt;submitJob(rdd, func, partitions, callSite, resultHandler,
       properties)
    
    -&gt; DAGScheduler.eventProcessLoop.post(JobSubmitted(
               jobId, rdd, func2, partitions.toArray, callSite, waiter,
               SerializationUtils.clone(properties))) 
    
    -&gt; DAGSchedulerEventProcessLoop-&gt; case JobSubmitted(jobId, dependency, callSite, listener, properties)-&gt;
    
    -&gt; DAGScheduler.handleJobSubmitted(jobId, rdd, func, partitions, callSite, listener, properties)
    
    -&gt; DAGScheduler.createResultStage(finalRDD, func, partitions, jobId, callSite) (广度优先算法，建立无环图)
    
    -&gt; DAGScheduler.submitStage(finalStage)
    
    -&gt; DAGScheduler.submitMissingTasks(stage, jobId.get) （提交最前面的Stage0）
    
    -&gt; taskScheduler.submitTasks(new TaskSet(
       tasks.toArray, stage.id, stage.latestInfo.attemptNumber, jobId, properties))
    
    -&gt; taskScheduler.submitTasks(new TaskSet(tasks.toArray, stage.id, stage.latestInfo.attemptNumber, jobId,
       properties)) 
       
    -&gt; createTaskSetManager(taskSet, maxTaskFailures) (TaskSchedulerImpl.submitTasks方法内)
    
    -&gt; taskScheduler -&gt;initialize -&gt; FIFOSchedulableBuilder -&gt;buildPools （TaskSchedulerImpl初始化构建）
    
    -&gt; schedulableBuilder.addTaskSetManager(manager, manager.taskSet.properties)
       (TaskSchedulerImpl.submitTasks方法内,  TaskSetManager pool ，遵循FIFO)
    
    -&gt; backend.reviveOffers()(CoarseGrainedSchedulerBackend)
    
    -&gt; driverEndpoint.send(ReviveOffers)
    
    -&gt; DriverEndpoint.receive  -&gt; case ReviveOffers  makeOffers() (makeOffers封装公共调度任务调度方法)
    
    -&gt; makeOffers(获得活着的Executor及executorHost和freeCores) -&gt; workOffers(CoarseGrainedSchedulerBackend内部)
    
    -&gt; TaskSchedulerImpl.resourceOffers(workOffers)（CoarseGrainedSchedulerBackend引用TaskSchedulerImpl）
    
    -&gt; val sortedTaskSets = TaskSchedulerImpl.rootPool.getSortedTaskSetQueue  (TaskSetManager上场)
    
    -&gt; TaskSchedulerImpl.resourceOfferSingleTaskSet(
                  taskSet, currentMaxLocality, shuffledOffers, availableCpus, tasks)t
    
    -&gt; TaskSetManager.resourceOffer(execId, host, maxLocality))
    
    -&gt; TaskSchedulerImpl.getAllowedLocalityLevel(curTime)  (延迟调度，期待我的下一篇精彩讲解)
    
    -&gt; TaskSetManager.dequeueTask(execId, host, allowedLocality)
    
    -&gt; sched.dagScheduler.taskStarted(task, info)
    
    -&gt; new TaskInfo(taskId, index, attemptNum, curTime, execId, host, taskLocality, speculative)
</code></pre>
<h3><a id="23_Job__Stage_Stage_226"></a>2.3 Job 与 Stage 的分分合合，Stage反向驱动与正向提交</h3>
<p>反向驱动：长江后浪推前浪，这里我发现一个奇怪的事情，Spark 2.0版本stage的反向驱动算法和Spark 2.3的居然不一样，这里以Spark 2.3为准:</p>
<ul>
<li>
<p>DAGScheduler进行submitStage提交后使命就结束了，最终实现submitStage正向提交任务集合即可：</p>
<pre><code> -&gt;  DAGScheduler. handleJobSubmitted 
 -&gt;  finalStage = createResultStage(finalRDD, func, partitions, jobId, callSite)
    （封装 finalRDD 和func为最后的Stage）
 -&gt;  submitStage(finalStage) -&gt; submitMissingTasks
</code></pre>
</li>
<li>
<p>但是createResultStage反向驱动算法精彩开始了：</p>
<pre><code>-&gt; createResultStage 
-&gt; getOrCreateParentStages -&gt; getShuffleDependencies  (获取父依赖)
-&gt; getOrCreateShuffleMapStage -&gt; getShuffleDependencies  (不断循环，形成递归)
-&gt; createShuffleMapStage 
</code></pre>
</li>
<li>
<p>以下代码段作用是获取父stage，有的话直接返回，没有就重新根据final RDD父依赖来创建Stage。注意这里会不断递归调用getOrCreateParentStages，最终建立Stage，也因此</p>
</li>
</ul>
<pre><code>private def getOrCreateParentStages(rdd: RDD[_], firstJobId: Int): List[Stage] = {
getShuffleDependencies(rdd).map { shuffleDep =&gt;
  getOrCreateShuffleMapStage(shuffleDep, firstJobId)
}.toList
</code></pre>
<p>}</p>
<ul>
<li>
<p>getOrCreateShuffleMapStage:创建依赖的依赖的所在Stage，有的话会直接获取，没有就优先创建   父Stage,然后执行子Stage.</p>
<pre><code>private def getOrCreateShuffleMapStage(
shuffleDep: ShuffleDependency[_, _, _],
firstJobId: Int): ShuffleMapStage = {
  shuffleIdToMapStage.get(shuffleDep.shuffleId) match {
case Some(stage) =&gt;
  stage

case None =&gt;
  // Create stages for all missing ancestor shuffle dependencies.
  getMissingAncestorShuffleDependencies(shuffleDep.rdd).foreach { dep =&gt;
    // Even though getMissingAncestorShuffleDependencies only returns shuffle dependencies
    // that were not already in shuffleIdToMapStage, it's possible that by the time we
    // get to a particular dependency in the foreach loop, it's been added to
    // shuffleIdToMapStage by the stage creation process for an earlier dependency. See
    // SPARK-13902 for more information.
    if (!shuffleIdToMapStage.contains(dep.shuffleId)) {
      createShuffleMapStage(dep, firstJobId)
    }
  }
  // Finally, create a stage for the given shuffle dependency.
  createShuffleMapStage(shuffleDep, firstJobId)
</code></pre>
<p>}<br>
}</p>
</li>
<li>
<p>createShuffleMapStage：最终落地方法，就是要返回需要的stage,注意阻塞点就在getOrCreateParentStages，从而一直递归到最顶层。</p>
<pre><code> def createShuffleMapStage(shuffleDep: ShuffleDependency[_, _, _], jobId: Int):      ShuffleMapStage = {
 val rdd = shuffleDep.rdd
 val numTasks = rdd.partitions.length
 val parents = getOrCreateParentStages(rdd, jobId)
 val id = nextStageId.getAndIncrement()
 val stage = new ShuffleMapStage(
 id, rdd, numTasks, parents, jobId, rdd.creationSite, shuffleDep,                mapOutputTracker)
 stageIdToStage(id) = stage
 shuffleIdToMapStage(shuffleDep.shuffleId) = stage
 updateJobIdStageIdMaps(jobId, stage)
 if (!mapOutputTracker.containsShuffle(shuffleDep.shuffleId)) {
 mapOutputTracker.registerShuffle(shuffleDep.shuffleId, rdd.partitions.length)
 }
 stage
 }
</code></pre>
</li>
</ul>
<h4><a id="24__296"></a>2.4 秦凯新原创总结：</h4>
<p>总流程是：先创建最顶层Satge，慢慢递归执行创建子stage，类似于堆栈模型。</p>
<ul>
<li>
<p>总流程为什么是这样呢？如无环图： G  -&gt; F A -&gt; H -&gt; M  L-&gt;N，因为由createShuffleMapStage做反向驱动，阻塞点在就在方法内的getOrCreateParentStages，因此先把创建(Final<br>
Stage的父stage的创建方法 F, A)放在堆栈底部，不断向上存放(F, A以上的父Stage创建方法)依次到堆栈，但却不执行Stage创建，直到最后最顶层Stage创建方法放到堆栈时，在得到rdd的顶级父亲时，开始执行最顶层Stage创建方法，也即createShuffleMapStage开始从阻塞点递归建立依赖关系，注册Shuffle，执行createShuffleMapStage方法体，然后依次从阻塞点递归向下执行。</p>
</li>
<li>
<p>注意 getOrCreateShuffleMapStage的缓存机制，即shuffleIdToMapStage，实现了依赖的直接获取，不用再重复执行，如F,A的父Stage的获取。</p>
<pre><code>  G  -&gt; F A -&gt; H -&gt; M  L-&gt;N

  1 先从 F A 的父依赖开始 开始递归构建stage
  
  2 进而开始建立H以上的stagey以及依赖关系。
  
  3 后建立 F A 与 H stage 的关系，注意H Stage是从shuffleIdToMapStage拿到的,
    最后返回F，A stage ,建立 final RDD(G)与F A的依赖关系
  
  4 最终提交submitStage(finalStage)

  最终stage构建顺序为： N -&gt; M  L -&gt; H-&gt; F A  -&gt; G  
</code></pre>
</li>
</ul>
<h2><a id="_316"></a>总结</h2>
<p>本节内容是作者投入大量时间优化后的内容，采用最平实的语言来剖析Spark的任务调度，现在时间为凌晨1:22，最后放张图DAGScheduler.handleTaskCompletion，正向提交任务，放飞吧，spark。</p>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/2/166d042dc075a448?w=1589&amp;h=1004&amp;f=png&amp;s=290649" alt=""></p>
<h3><a id="_20181102_322"></a>作者：秦凯新 20181102</h3>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>