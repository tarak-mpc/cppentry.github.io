---
layout:     post
title:      Spark PersistenceEngine持久化引擎与领导选举代理机制内核原理深入剖析-Spark商业环境实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本套技术专栏是作者（秦凯新）平时工作的总结和升华，通过从真实商业环境抽取案例进行总结和分享，并给出商业应用的调优建议和集群环境容量规划等内容，请持续关注本套博客。版权声明：禁止转载，欢迎学习。QQ邮箱地址：1120746959@qq.com，如有任何商业交流，可随时联系。					https://blog.csdn.net/shenshouniu/article/details/83999932				</div>
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
<li><a href="https://juejin.im/post/5be96a2fe51d454e0a1cc3a2" rel="nofollow">Spark商业环境实战-Spark PersistenceEngine持久化引擎高可用机制内核原理深入剖析</a></li>
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
<h2><a id="1_PersistenceEngine_28"></a>1 PersistenceEngine持久化引擎</h2>
<h3><a id="11_PersistenceEngine_30"></a>1.1 PersistenceEngine的启动</h3>
<ul>
<li>
<p>选择故障恢复机制，主要有ZOOKEEPER 和 FILESYSTEM 。</p>
<pre><code>private val RECOVERY_MODE = conf.get("spark.deploy.recoveryMode", "NONE")
</code></pre>
</li>
<li>
<p>PersistenceEngine 的初始化是放在Master的onStart()方法中，用于初始化持久化引擎。</p>
<pre><code>val serializer = new JavaSerializer(conf)

  val (persistenceEngine_, leaderElectionAgent_) = RECOVERY_MODE match {
    case "ZOOKEEPER" =&gt;
      logInfo("Persisting recovery state to ZooKeeper")
      
      val zkFactory =
        new ZooKeeperRecoveryModeFactory(conf, serializer)
      (zkFactory.createPersistenceEngine(), zkFactory.createLeaderElectionAgent(this))
      
    case "FILESYSTEM" =&gt;
    
      val fsFactory =
        new FileSystemRecoveryModeFactory(conf, serializer)
      (fsFactory.createPersistenceEngine(), fsFactory.createLeaderElectionAgent(this))
      
    case "CUSTOM" =&gt;
    
      val clazz = Utils.classForName(conf.get("spark.deploy.recoveryMode.factory"))
      val factory = clazz.getConstructor(classOf[SparkConf], classOf[Serializer])
        .newInstance(conf, serializer)
        .asInstanceOf[StandaloneRecoveryModeFactory]
      (factory.createPersistenceEngine(), factory.createLeaderElectionAgent(this))
      
    case _ =&gt;
      (new BlackHolePersistenceEngine(), new MonarchyLeaderAgent(this))
  }
  persistenceEngine = persistenceEngine_
  leaderElectionAgent = leaderElectionAgent_
</code></pre>
</li>
</ul>
<h3><a id="12_PersistenceEngine_69"></a>1.2 PersistenceEngine的功能</h3>
<ul>
<li>PersistenceEngine主要用于当Master发生故障时，来读取持久化的Application，Worker，Driver的详细信息。</li>
<li>PersistenceEngine同样负责写入持久化Application，Worker，Driver的详细信息。</li>
</ul>
<p>（1）PersistenceEngine 的调用时机：</p>
<ul>
<li>在新的Application注册之前。</li>
<li>在新的Worker注册之前。</li>
<li>在removeApplication和removeWorker方法被调用的时候</li>
</ul>
<p>举例如下：</p>
<pre><code>   persistenceEngine.removeWorker(worker)
</code></pre>
<h3><a id="13_PersistenceEngine_84"></a>1.3 PersistenceEngine的抽象模板，也即调用时机</h3>
<pre><code>abstract class PersistenceEngine {

  /**
   * Defines how the object is serialized and persisted. Implementation will
   * depend on the store used.
   */
  def persist(name: String, obj: Object): Unit

  /**
   * Defines how the object referred by its name is removed from the store.
   */
  def unpersist(name: String): Unit

  /**
   * Gives all objects, matching a prefix. This defines how objects are
   * read/deserialized back.
   */
  def read[T: ClassTag](prefix: String): Seq[T]

  final def addApplication(app: ApplicationInfo): Unit = {
    persist("app_" + app.id, app)
  }

  final def removeApplication(app: ApplicationInfo): Unit = {
    unpersist("app_" + app.id)
  }

  final def addWorker(worker: WorkerInfo): Unit = {
    persist("worker_" + worker.id, worker)
  }

  final def removeWorker(worker: WorkerInfo): Unit = {
    unpersist("worker_" + worker.id)
  }

  final def addDriver(driver: DriverInfo): Unit = {
    persist("driver_" + driver.id, driver)
  }

  final def removeDriver(driver: DriverInfo): Unit = {
    unpersist("driver_" + driver.id)
  }

  /**
   * Returns the persisted data sorted by their respective ids (which implies that they're
   * sorted by time of creation).
   */
  final def readPersistedData(
      rpcEnv: RpcEnv): (Seq[ApplicationInfo], Seq[DriverInfo], Seq[WorkerInfo]) = {
    rpcEnv.deserialize { () =&gt;
      (read[ApplicationInfo]("app_"), read[DriverInfo]("driver_"), read[WorkerInfo]("worker_"))
    }
  }

  def close() {}
}
</code></pre>
<h3><a id="14__PersistenceEngine_Zookeeper_144"></a>1.4  PersistenceEngine 的基于文件系统持久化和基于Zookeeper的持久化</h3>
<ul>
<li>
<p>基于文件系统持久化FileSystemPersistenceEngine</p>
<pre><code>   private def serializeIntoFile(file: File, value: AnyRef) {
      val created = file.createNewFile()
      if (!created) { throw new IllegalStateException("Could not create file: " + file) }
      val fileOut = new FileOutputStream(file)
      var out: SerializationStream = null
      Utils.tryWithSafeFinally {
        out = serializer.newInstance().serializeStream(fileOut)
        out.writeObject(value)
      } {
        fileOut.close()
        if (out != null) {
          out.close()
        }
      }
    }
</code></pre>
</li>
<li>
<p>基于Zookeeper的持久化ZooKeeperPersistenceEngine</p>
<p>Curator是Netflix公司开源的Zookeeper客户端，注意这里会把ApplicationInfo，WorkerInfo，DriverInfo等数据通过ZooKeeperPersistenceEngine将数据存储到Zookeeper的不同Znode节点上。</p>
<p>这里Zookeeper能撑得住吗？？疑问</p>
<pre><code>private val WORKING_DIR = conf.get("spark.deploy.zookeeper.dir", "/spark") + "/master_status"
private val zk: CuratorFramework = SparkCuratorUtil.newClient(conf)

  private def serializeIntoFile(path: String, value: AnyRef) {
  
      val serialized = serializer.newInstance().serialize(value)
      val bytes = new Array[Byte](serialized.remaining())
      serialized.get(bytes)
      zk.create().withMode(CreateMode.PERSISTENT).forPath(path, bytes)
   }
</code></pre>
</li>
</ul>
<h2><a id="2__182"></a>2 领导选举机制</h2>
<p>所谓选举机制就是注册监听机制，一旦监听到Master挂了，就会进行回调监听。</p>
<p>主要有：</p>
<ul>
<li>ZooKeeperLeaderElectionAgent</li>
<li>MonarchyLeaderAgent</li>
</ul>
<p>接下来主要以ZooKeeperLeaderElectionAgent为例:</p>
<h2><a id="21__192"></a>2.1 借鸡生蛋的道理</h2>
<ul>
<li>
<p>通过/leader_election这个目录进行监听：</p>
<pre><code>val WORKING_DIR = conf.get("spark.deploy.zookeeper.dir", "/spark") + "/leader_election"

private def start() {
    logInfo("Starting ZooKeeper LeaderElection agent")
    zk = SparkCuratorUtil.newClient(conf)
    leaderLatch = new LeaderLatch(zk, WORKING_DIR)
    leaderLatch.addListener(this)
    leaderLatch.start()
  }

  private def updateLeadershipStatus(isLeader: Boolean) {
    if (isLeader &amp;&amp; status == LeadershipStatus.NOT_LEADER) {
      status = LeadershipStatus.LEADER
      masterInstance.electedLeader()
    } else if (!isLeader &amp;&amp; status == LeadershipStatus.LEADER) {
      status = LeadershipStatus.NOT_LEADER
      masterInstance.revokedLeadership()
    }
  }
</code></pre>
</li>
<li>
<p>通过监听/leader_election对应目录来进行选举</p>
<pre><code>  override def isLeader() {
      synchronized {
        // could have lost leadership by now.
        if (!leaderLatch.hasLeadership) {
          return
        }
  
        logInfo("We have gained leadership")
        updateLeadershipStatus(true)
      }
    }
  
    override def notLeader() {
      synchronized {
        // could have gained leadership by now.
        if (leaderLatch.hasLeadership) {
          return
        }
  
        logInfo("We have lost leadership")
        updateLeadershipStatus(false)
      }
    }
</code></pre>
</li>
</ul>
<h2><a id="3_Master__242"></a>3 Master 在选举中要做什么</h2>
<p>Master自己给自己发送消息，开始进行恢复操作：</p>
<ul>
<li>
<p>Master继承了LeaderElectable，因此实现了electedLeader方法：</p>
<pre><code>override def electedLeader() {
  self.send(ElectedLeader)
}
</code></pre>
</li>
<li>
<p>Master 的行动beginRecovery和CompleteRecovery</p>
<pre><code>   override def receive: PartialFunction[Any, Unit] = {
      case ElectedLeader =&gt;
        val (storedApps, storedDrivers, storedWorkers) = persistenceEngine.readPersistedData(rpcEnv)
        state = if (storedApps.isEmpty &amp;&amp; storedDrivers.isEmpty &amp;&amp; storedWorkers.isEmpty) {
          RecoveryState.ALIVE
        } else {
          RecoveryState.RECOVERING
        }
        logInfo("I have been elected leader! New state: " + state)
        if (state == RecoveryState.RECOVERING) {

          beginRecovery(storedApps, storedDrivers, storedWorkers)      &lt;=神来之笔
          
          recoveryCompletionTask = forwardMessageThread.schedule(new Runnable {
            override def run(): Unit = Utils.tryLogNonFatalError {
              self.send(CompleteRecovery)   &lt;=神来之笔
            }
          }, WORKER_TIMEOUT_MS, TimeUnit.MILLISECONDS)
        }
</code></pre>
</li>
<li>
<p>Master 的行动beginRecovery</p>
<pre><code>  private def beginRecovery(storedApps: Seq[ApplicationInfo], storedDrivers: Seq[DriverInfo],
        storedWorkers: Seq[WorkerInfo]) {
      for (app &lt;- storedApps) {
        logInfo("Trying to recover app: " + app.id)
        try {
          registerApplication(app)
          app.state = ApplicationState.UNKNOWN
          app.driver.send(MasterChanged(self, masterWebUiUrl))
        } catch {
          case e: Exception =&gt; logInfo("App " + app.id + " had exception on reconnect")
        }
      }
</code></pre>
</li>
<li>
<p>Master 的行动completeRecovery</p>
<pre><code>  private def completeRecovery() {
      // Ensure "only-once" recovery semantics using a short synchronization period.
      if (state != RecoveryState.RECOVERING) { return }
      state = RecoveryState.COMPLETING_RECOVERY
  
      // Kill off any workers and apps that didn't respond to us.
      workers.filter(_.state == WorkerState.UNKNOWN).foreach(
        removeWorker(_, "Not responding for recovery"))
      apps.filter(_.state == ApplicationState.UNKNOWN).foreach(finishApplication)
  
      // Update the state of recovered apps to RUNNING
      apps.filter(_.state == ApplicationState.WAITING).foreach(_.state = ApplicationState.RUNNING)
  
      // Reschedule drivers which were not claimed by any workers
      drivers.filter(_.worker.isEmpty).foreach { d =&gt;
        logWarning(s"Driver ${d.id} was not found after master recovery")
        if (d.desc.supervise) {
          logWarning(s"Re-launching ${d.id}")
          relaunchDriver(d)
        } else {
          removeDriver(d.id, DriverState.ERROR, None)
          logWarning(s"Did not re-launch ${d.id} because it was not supervised")
        }
      }
</code></pre>
</li>
</ul>
<h2><a id="4__315"></a>4 总结</h2>
<p>秦凯新 于深圳</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>