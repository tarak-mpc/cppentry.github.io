---
layout:     post
title:      Spark一级资源调度Shedule机制及SpreadOut模式源码深入剖析-Spark商业环境实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本套技术专栏是作者（秦凯新）平时工作的总结和升华，通过从真实商业环境抽取案例进行总结和分享，并给出商业应用的调优建议和集群环境容量规划等内容，请持续关注本套博客。版权声明：禁止转载，欢迎学习。QQ邮箱地址：1120746959@qq.com，如有任何商业交流，可随时联系。					https://blog.csdn.net/shenshouniu/article/details/83962930				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>本套系列博客从真实商业环境抽取案例进行总结和分享，并给出Spark源码解读及商业实战指导，请持续关注本套博客。版权声明：本套Spark源码解读及商业实战归作者（秦凯新）所有，禁止转载，欢迎学习。</p>
<ul>
<li><a href="https://juejin.im/post/5bd55b4de51d4579470247eb" rel="nofollow">Spark商业环境实战-Spark内置框架rpc通讯机制及RpcEnv基础设施 </a></li>
<li><a href="https://juejin.im/post/5bd583f0e51d452c296ef5dd" rel="nofollow">Spark商业环境实战-Spark事件监听总线流程分析</a></li>
<li><a href="https://juejin.im/post/5bd854955188255ca65da7ed" rel="nofollow">Spark商业环境实战-Spark存储体系底层架构剖析</a></li>
<li><a href="https://juejin.im/post/5bd8553af265da0adb311f58" rel="nofollow">Spark商业环境实战-Spark底层多个MessageLoop循环线程执行流程分析</a></li>
<li><a href="https://juejin.im/post/5be82b75f265da614a3a03b7" rel="nofollow">Spark商业环境实战-Spark一级资源调度Shedule机制及SpreadOut模式源码深入剖析</a></li>
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
<li><a href="https://juejin.im/post/5be677946fb9a049ca36db6f" rel="nofollow">Spark商业环境实战-SparkStreaming数据流从Batch到Block定时转化过程源码深度剖析</a></li>
<li><a href="https://juejin.im/post/5be6ac236fb9a049a42e8e37" rel="nofollow">Spark商业环境实战-SparkStreaming之JobGenerator周期性任务数据处理逻辑源码深度剖析</a></li>
<li>[Spark商业环境实战-SparkStreaming Graph 处理链迭代过程源码深度剖析]</li>
<li>[Spark商业环境实战-JobGenerator 数据清理流程源码深度剖析]</li>
<li>[Spark商业环境实战-SparkStreaming 容错机制源码深度剖析]</li>
<li>[Spark商业环境实战-SparkStreaming 之No Receiver方式基于Kafka 拉取内幕源码深度剖析]</li>
<li>[Spark商业环境实战-SparkStreaming 反压机制控制消费速率内幕源码深度剖析]</li>
</ul>
<h2><a id="1_Shedule__29"></a>1 Shedule 在哪里？干什么？</h2>
<p>Shedule()发生在Master中，那么Master都负责什么呢？可以看到只要发生以下任何事件，就会重新执行Shedule()</p>
<ul>
<li>RegisterWorker</li>
<li>RegisterApplication</li>
<li>ExecutorStateChanged</li>
<li>RequestSubmitDriver</li>
<li>completeRecovery</li>
<li>relaunchDriver</li>
<li>removeApplication</li>
<li>handleRequestExecutors</li>
<li>handleKillExecutors</li>
<li>removeDriver</li>
</ul>
<h5><a id="LocalclusterStandaloneMasterDriverApplicationExecutor_44"></a>因此，可以看出所谓的一级资源调度，在Local-cluster部署模式和Standalone部署模式中，其实就是基于Master实现的资源调度，更确切的说是对Driver的资源调度和对Application(参数指定数量的Executor)的资源调度。</h5>
<h2><a id="2_MasterYarnK8s_46"></a>2 Master的天子王权（除Yarn资源和K8s容器编排）</h2>
<p>Master是Local-cluster部署模式和Standalone部署模式中，最为核心的管理组件。Master将直接决定整个集群的可用性，容错性，可用性。可谓位于整个Spark集群中最重要，最核心的位置。职责如下：</p>
<ul>
<li>Worker的管理</li>
<li>Application的管理</li>
<li>Driver的管理</li>
<li>统一管理和分配集群中的资源（如内存和cpu）</li>
<li>接收各个Worker的注册，状态更新，心跳</li>
<li>Driver和Application的注册</li>
</ul>
<h2><a id="3_Driver___57"></a>3 Driver  的前世今生？是什么？如何纳管？</h2>
<ul>
<li>
<p>Driver的诞生来源于Master接收到RequestSubmitDriver请求，那么RequestSubmitDriver来源于何处，这又要从SparkSubmit类说起，先上代码段，看看STANDALONE_CLUSTER_SUBMIT_CLASS，就从这里开始：</p>
<pre><code>    private[deploy] val YARN_CLUSTER_SUBMIT_CLASS = "org.apache.spark.deploy.yarn.YarnClusterApplication"   
    private[deploy] val REST_CLUSTER_SUBMIT_CLASS = classOf[RestSubmissionClientApp].getName()
    private[deploy] val STANDALONE_CLUSTER_SUBMIT_CLASS = classOf[ClientApp].getName()
    private[deploy] val KUBERNETES_CLUSTER_SUBMIT_CLASS ="org.apache.spark.deploy.k8s.submit.KubernetesClientApplication"
</code></pre>
</li>
<li>
<p>这里开始封装Spark-submit提交的各个参数，同时在StandAlone模式下，我们开始关注ClientEndpoint它是一个终端.</p>
<pre><code>  // In standalone cluster mode, use the REST client to submit the application (Spark 1.3+).
  // All Spark parameters are expected to be passed to the client through system properties.
  if (args.isStandaloneCluster) {
    if (args.useRest) {
      childMainClass = REST_CLUSTER_SUBMIT_CLASS
      childArgs += (args.primaryResource, args.mainClass)
    } else {
    
    
      // In legacy standalone cluster mode, use Client as a wrapper around the user class
      childMainClass = STANDALONE_CLUSTER_SUBMIT_CLASS   &lt;= 神来之笔ClientApp
      
      
      if (args.supervise) { childArgs += "--supervise" }
      Option(args.driverMemory).foreach { m =&gt; childArgs += ("--memory", m) }
      Option(args.driverCores).foreach { c =&gt; childArgs += ("--cores", c) }
      childArgs += "launch"
      childArgs += (args.master, args.primaryResource, args.mainClass)
    }
    if (args.childArgs != null) {
      childArgs ++= args.childArgs
    }
  }
</code></pre>
</li>
<li>
<p>ClientApp （ClientEndpoint） 开始向Master异步发送RequestSubmitDriver请求，也就是说：一次Spark-Submit提交，就会发送一次RequestSubmitDriver请求，进而生成一个资源申请的Driver。</p>
<pre><code> val driverDescription = new DriverDescription(
       driverArgs.jarUrl,
       driverArgs.memory,
       driverArgs.cores,
       driverArgs.supervise,
       command)
     asyncSendToMasterAndForwardReply[SubmitDriverResponse](
       RequestSubmitDriver(driverDescription))
</code></pre>
</li>
<li>
<p>Master接收到提交的资源申请，开始向自己的成员变量drivers中放入一个Driver，也即每一次任务提交的的资源申请驱动。</p>
<pre><code>   case RequestSubmitDriver(description) =&gt;
        if (state != RecoveryState.ALIVE) {
          val msg = s"${Utils.BACKUP_STANDALONE_MASTER_PREFIX}: $state. " +
            "Can only accept driver submissions in ALIVE state."
          context.reply(SubmitDriverResponse(self, false, None, msg))
        } else {
          logInfo("Driver submitted " + description.command.mainClass)
          val driver = createDriver(description)
          persistenceEngine.addDriver(driver)
          waitingDrivers += driver
          drivers.add(driver)
          schedule()
  
          // TODO: It might be good to instead have the submission client poll the master to determine
          //       the current status of the driver. For now it's simply "fire and forget".
  
          context.reply(SubmitDriverResponse(self, true, Some(driver.id),
            s"Driver successfully submitted as ${driver.id}"))
        }
</code></pre>
</li>
<li>
<p>封装资源申请实体DriverInfo</p>
<pre><code>private def createDriver(desc: DriverDescription): DriverInfo = {
  val now = System.currentTimeMillis()
  val date = new Date(now)
  new DriverInfo(now, newDriverId(date), desc, date)
}
</code></pre>
</li>
</ul>
<h6><a id="MasterreceiveAndReplyClientEndpointRequestSubmitDriverDriverwaitingDriversDriver_133"></a>总结：Master的receiveAndReply方法接收ClientEndpoint发送的消息RequestSubmitDriver，将收到的Driver注册到waitingDrivers。基于此，才会有后面的基于Driver的一级资源调度。</h6>
<pre><code>  RequestSubmitDriver详情请参考这篇博客，比我的更详细。https://blog.csdn.net/u011564172/article/details/68496848
</code></pre>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/11/167032ef7ea88fe3?w=1045&amp;h=411&amp;f=png&amp;s=68777" alt=""></p>
<h2><a id="4_Application_Driver_139"></a>4 Application 的前世今生？和Driver渊源？如何纳管？</h2>
<ul>
<li>
<p>差一点就疯了，Application和Driver完全不是一个概念。Driver的诞生发生在Spark-submit阶段。而Application的诞生发生在DAG调度阶段，也即SparkContext实例化阶段。拼了非讲清不可。</p>
</li>
<li>
<p>Master 最终会根据Application的资源申请，把appDesc放入apps队列中，并对Application进行资源调度。</p>
<pre><code>  val appDesc = ApplicationDescription(sc.appName, maxCores, sc.executorMemory, command,
       webUrl, sc.eventLogDir, sc.eventLogCodec, coresPerExecutor, initialExecutorLimit)
      
  SparkContext：
  
  SparkContext -&gt; StandaloneSchedulerBackend -&gt; StandaloneAppClient.start() 
  -&gt; registerWithMaster -&gt; masterRef.send(RegisterApplication(appDescription, self)) -&gt; 
  
  Master:
  
  -&gt; apps += app -&gt;
  shedule()[Driver启动后，调用startExecutorsOnWorkers()-&gt;allocateWorkerResourceToExecutors]
</code></pre>
</li>
<li>
<p>Master端点registerApplication</p>
<p>private def registerApplication(app: ApplicationInfo): Unit = {<br>
val appAddress = app.driver.address<br>
if (addressToApp.contains(appAddress)) {<br>
logInfo("Attempted to re-register application at same address: " + appAddress)<br>
return<br>
}</p>
<pre><code>  applicationMetricsSystem.registerSource(app.appSource)
  apps += app
  idToApp(app.id) = app
  endpointToApp(app.driver) = app
  addressToApp(appAddress) = app
  waitingApps += app
}
</code></pre>
</li>
</ul>
<h2><a id="5_Master_175"></a>5 Master的职责再讲</h2>
<ul>
<li>首先集群启动之后，Worker会向Master注册，同时携带身份标识和资源情况（如ID,host，port,cpu核数，内存大小），那么这些资源交由Master纳管后，Master会按照一定的资源调度策略分配给Driver和Application。</li>
<li>Master给Driver分配完资源后，将会向Worker发送启动Driver命令，Worker接收到命令后，开始启动Driver。</li>
<li>Master给Application分配完资源后，将向Worker发送启动Executor命令，Worker接收到命令后，开始启动Executor。</li>
</ul>
<h2><a id="6_Shedule_180"></a>6 Shedule()神秘面纱</h2>
<h3><a id="61_Shedule__182"></a>6.1 Shedule 核心思想</h3>
<ul>
<li>
<p>打乱洗牌存活的Worker,在Worker资源满足的情况下，启动Executor。</p>
</li>
<li>
<p>神来之笔（Driver资源调度）==&gt; launchDriver(worker, driver)</p>
</li>
<li>
<p>神来之笔（Executor调度）==&gt;  startExecutorsOnWorkers()</p>
<pre><code>    * Schedule the currently available resources among waiting apps. This method will be called
    * every time a new app joins or resource availability changes.
    
    private def schedule(): Unit = {
      if (state != RecoveryState.ALIVE) {
        return
      }
      // Drivers take strict precedence over executors
      val shuffledAliveWorkers = Random.shuffle(workers.toSeq.filter(_.state == WorkerState.ALIVE))
      val numWorkersAlive = shuffledAliveWorkers.size
      var curPos = 0
      for (driver &lt;- waitingDrivers.toList) { // iterate over a copy of waitingDrivers
        // We assign workers to each waiting driver in a round-robin fashion. For each driver, we
        // start from the last worker that was assigned a driver, and continue onwards until we have
        // explored all alive workers.
        var launched = false
        var numWorkersVisited = 0
        while (numWorkersVisited &lt; numWorkersAlive &amp;&amp; !launched) {
          val worker = shuffledAliveWorkers(curPos)
          numWorkersVisited += 1
          if (worker.memoryFree &gt;= driver.desc.mem &amp;&amp; worker.coresFree &gt;= driver.desc.cores) {
          
            launchDriver(worker, driver)      &lt;= 神来之笔（Driver资源调度）
            
            waitingDrivers -= driver
            launched = true
          }
          curPos = (curPos + 1) % numWorkersAlive
        }
      }
      
      
      startExecutorsOnWorkers()               &lt;= 神来之笔（Executor调度）
      
    }
</code></pre>
</li>
</ul>
<h3><a id="62_launchDriver__224"></a>6.2 launchDriver 挖一挖</h3>
<ul>
<li>
<p>发送到Worker开始启动driver</p>
<pre><code>private def launchDriver(worker: WorkerInfo, driver: DriverInfo) {
  logInfo("Launching driver " + driver.id + " on worker " + worker.id)
  worker.addDriver(driver)
  driver.worker = Some(worker)
  worker.endpoint.send(LaunchDriver(driver.id, driver.desc))
  driver.state = DriverState.RUNNING
}
</code></pre>
</li>
<li>
<p>Worker端的回馈</p>
<pre><code>   case LaunchDriver(driverId, driverDesc) =&gt;
        logInfo(s"Asked to launch driver $driverId")
        val driver = new DriverRunner(
          conf,
          driverId,
          workDir,
          sparkHome,
          driverDesc.copy(command = Worker.maybeUpdateSSLSettings(driverDesc.command, conf)),
          self,
          workerUri,
          securityMgr)
        drivers(driverId) = driver
        driver.start()
  
        coresUsed += driverDesc.cores
        memoryUsed += driverDesc.mem
</code></pre>
</li>
</ul>
<h3><a id="63_startExecutorsOnWorkers__254"></a>6.3 startExecutorsOnWorkers 钻一钻</h3>
<ul>
<li>
<p>coresPerExecutor：参数设置的每一个Executor所使用的内核数，默认为1。</p>
</li>
<li>
<p>app.desc.memoryPerExecutorMB ：参数设置的ExecutorMemory。</p>
</li>
<li>
<p>scheduleExecutorsOnWorkers ：返回各个Worker上分配的内核数</p>
</li>
<li>
<p>allocateWorkerResourceToExecutors：</p>
<pre><code>  private def startExecutorsOnWorkers(): Unit = {
      // Right now this is a very simple FIFO scheduler. We keep trying to fit in the first app
      // in the queue, then the second app, etc.
      
      for (app &lt;- waitingApps) {
      
        val coresPerExecutor = app.desc.coresPerExecutor.getOrElse(1) &lt;= 神来之笔（Worker资源情况判断）
        
        // If the cores left is less than the coresPerExecutor,the cores left will not be allocated
        if (app.coresLeft &gt;= coresPerExecutor) {
          // Filter out workers that don't have enough resources to launch an executor
          
          val usableWorkers = workers.toArray.filter(_.state == WorkerState.ALIVE)
            .filter(worker =&gt; worker.memoryFree &gt;= app.desc.memoryPerExecutorMB &amp;&amp;
              worker.coresFree &gt;= coresPerExecutor)
            .sortBy(_.coresFree).reverse                 &lt;= 神来之笔（Worker资源情况判断）
            
          val assignedCores = scheduleExecutorsOnWorkers(app, usableWorkers, spreadOutApps) &lt;= 神来之笔
  
          // Now that we've decided how many cores to allocate on each worker, let's allocate them
          for (pos &lt;- 0 until usableWorkers.length if assignedCores(pos) &gt; 0) {
          
            allocateWorkerResourceToExecutors(
              app, assignedCores(pos), app.desc.coresPerExecutor, usableWorkers(pos)) &lt;= 神来之笔
          }
        }
      }
    }
</code></pre>
</li>
</ul>
<h3><a id="63_scheduleExecutorsOnWorkers__291"></a>6.3 scheduleExecutorsOnWorkers 较较真</h3>
<pre><code>   * Schedule executors to be launched on the workers.
   * Returns an array containing number of cores assigned to each worker.
   *
   * There are two modes of launching executors. The first attempts to spread out an application's
   * executors on as many workers as possible, while the second does the opposite (i.e. launch them
   * on as few workers as possible). The former is usually better for data locality purposes and is
   * the default.
   *
   * The number of cores assigned to each executor is configurable. When this is explicitly set,
   * multiple executors from the same application may be launched on the same worker if the worker
   * has enough cores and memory. Otherwise, each executor grabs all the cores available on the
   * worker by default, in which case only one executor per application may be launched on each
   * worker during one single schedule iteration.
   * Note that when `spark.executor.cores` is not set, we may still launch multiple executors from
   * the same application on the same worker. Consider appA and appB both have one executor running
   * on worker1, and appA.coresLeft &gt; 0, then appB is finished and release all its cores on worker1,
   * thus for the next schedule iteration, appA launches a new executor that grabs all the free
   * cores on worker1, therefore we get multiple executors from appA running on worker1.
   *
   * It is important to allocate coresPerExecutor on each worker at a time (instead of 1 core
   * at a time). Consider the following example: cluster has 4 workers with 16 cores each.
   * User requests 3 executors (spark.cores.max = 48, spark.executor.cores = 16). If 1 core is
   * allocated at a time, 12 cores from each worker would be assigned to each executor.
   * Since 12 &lt; 16, no executors would launch [SPARK-8881].
</code></pre>
<ul>
<li>
<p>spreadOutApps 决定了Executor的分配是集中的，还是按照顺序分散的。</p>
</li>
<li>
<p>oneExecutorPerWorker ：如果没有指定coresPerExecutor，那么就说明每一个Worker上只有一个Executor，否则就是多个</p>
</li>
<li>
<p>assignedCores(pos)是返回的数组，其中freeWorkers就是索引0,1,2。对应的可分配的Cores就会是指定Worker上能够分配的。</p>
</li>
<li>
<p>allocateWorkerResourceToExecutors:就是根据打散后的Worker索引，进行Executor的启动，玄机在于每一个Worker是否需要启动多个Executor</p>
<pre><code>  private def scheduleExecutorsOnWorkers(
       app: ApplicationInfo,
       usableWorkers: Array[WorkerInfo],
       spreadOutApps: Boolean): Array[Int] = {
     val coresPerExecutor = app.desc.coresPerExecutor
     val minCoresPerExecutor = coresPerExecutor.getOrElse(1)
     val oneExecutorPerWorker = coresPerExecutor.isEmpty
     val memoryPerExecutor = app.desc.memoryPerExecutorMB
     val numUsable = usableWorkers.length
     val assignedCores = new Array[Int](numUsable) // Number of cores to give to each worker
     val assignedExecutors = new Array[Int](numUsable) // Number of new executors on each worker
     var coresToAssign = math.min(app.coresLeft, usableWorkers.map(_.coresFree).sum)
 
     /** Return whether the specified worker can launch an executor for this app. */
     def canLaunchExecutor(pos: Int): Boolean = {
       val keepScheduling = coresToAssign &gt;= minCoresPerExecutor
       val enoughCores = usableWorkers(pos).coresFree - assignedCores(pos) &gt;= minCoresPerExecutor
 
       // If we allow multiple executors per worker, then we can always launch new executors.
       // Otherwise, if there is already an executor on this worker, just give it more cores.
       val launchingNewExecutor = !oneExecutorPerWorker || assignedExecutors(pos) == 0
       if (launchingNewExecutor) {
         val assignedMemory = assignedExecutors(pos) * memoryPerExecutor
         val enoughMemory = usableWorkers(pos).memoryFree - assignedMemory &gt;= memoryPerExecutor
         val underLimit = assignedExecutors.sum + app.executors.size &lt; app.executorLimit
         keepScheduling &amp;&amp; enoughCores &amp;&amp; enoughMemory &amp;&amp; underLimit
       } else {
         // We're adding cores to an existing executor, so no need
         // to check memory and executor limits
         keepScheduling &amp;&amp; enoughCores
       }
     }
 
     // Keep launching executors until no more workers can accommodate any
     // more executors, or if we have reached this application's limits
     var freeWorkers = (0 until numUsable).filter(canLaunchExecutor)
     while (freeWorkers.nonEmpty) {
       freeWorkers.foreach { pos =&gt;
         var keepScheduling = true
         while (keepScheduling &amp;&amp; canLaunchExecutor(pos)) {
           coresToAssign -= minCoresPerExecutor
           assignedCores(pos) += minCoresPerExecutor
 
           // If we are launching one executor per worker, then every iteration assigns 1 core
           // to the executor. Otherwise, every iteration assigns cores to a new executor.
           if (oneExecutorPerWorker) {
             assignedExecutors(pos) = 1
           } else {
             assignedExecutors(pos) += 1
           }
 
           // Spreading out an application means spreading out its executors across as
           // many workers as possible. If we are not spreading out, then we should keep
           // scheduling executors on this worker until we use all of its resources.
           // Otherwise, just move on to the next worker.
           if (spreadOutApps) {
             keepScheduling = false
           }
         }
       }
       freeWorkers = freeWorkers.filter(canLaunchExecutor)
     }
     assignedCores
   }
</code></pre>
</li>
</ul>
<h3><a id="63_allocateWorkerResourceToExecutors__388"></a>6.3 allocateWorkerResourceToExecutors 探究竟</h3>
<ul>
<li>
<p>通知Worker根据Application的要求，也即根据应用提交时的要求，开始启动Executor。</p>
<pre><code>  private def allocateWorkerResourceToExecutors(
        app: ApplicationInfo,
        assignedCores: Int,
        coresPerExecutor: Option[Int],
        worker: WorkerInfo): Unit = {
      // If the number of cores per executor is specified, we divide the cores assigned
      // to this worker evenly among the executors with no remainder.
      // Otherwise, we launch a single executor that grabs all the assignedCores on this worker.
      val numExecutors = coresPerExecutor.map { assignedCores / _ }.getOrElse(1)
      val coresToAssign = coresPerExecutor.getOrElse(assignedCores)
      for (i &lt;- 1 to numExecutors) {
        val exec = app.addExecutor(worker, coresToAssign)
        launchExecutor(worker, exec)
        app.state = ApplicationState.RUNNING
      }
}
</code></pre>
</li>
<li>
<p>Master 终端发送 LaunchExecutor</p>
<pre><code>  private def launchExecutor(worker: WorkerInfo, exec: ExecutorDesc): Unit = {
      logInfo("Launching executor " + exec.fullId + " on worker " + worker.id)
      worker.addExecutor(exec)
      worker.endpoint.send(LaunchExecutor(masterUrl,
        exec.application.id, exec.id, exec.application.desc, exec.cores, exec.memory))
      exec.application.driver.send(
        ExecutorAdded(exec.id, worker.id, worker.hostPort, exec.cores, exec.memory))
    }
</code></pre>
</li>
</ul>
<h3><a id="7__419"></a>7 总结</h3>
<p>至此，一级资源调度Shedule机制剖析完毕，真的是剖析的体无完肤啊。贴一张图，该休息了。因为已经0:18了。</p>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/12/1670392505e0b4c9?w=2288&amp;h=1080&amp;f=png&amp;s=2354970" alt=""></p>
<p>秦凯新 于深圳  香港太平山全景 人定胜天</p>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/12/1670393198403ebb?w=598&amp;h=596&amp;f=png&amp;s=213411" alt=""></p>
<h3><a id="_2018_11_12_429"></a>再见 2018 11 12</h3>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>