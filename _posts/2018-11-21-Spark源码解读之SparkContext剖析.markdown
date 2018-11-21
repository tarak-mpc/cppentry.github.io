---
layout:     post
title:      Spark源码解读之SparkContext剖析
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_37142346/article/details/81222299				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<blockquote>
  <p>最近一直在研究Spark，也自己练习做了几个项目，对于Spark这个框架特别的喜爱，尤其是其一站式的大数据解决方案，而且也对Spark MLlib中的机器学习算法很感兴趣，也学习过一段时间。但是在自己空闲下来回想，对于Spark的理解自己仅仅只是停留在表层，如果只是使用API来编写Spark程序，那么无疑将Spark与其他普通的框架混为一谈，发挥不了其作用，根本谈不上说熟悉Spark。因此，想花一段时间来研究Spark的内核架构与源码，常听大牛们说学习技术不仅要知道它怎么使用，更要知其所以然。那么，抱着这种心态来开启Spark的内核与源码的剖析之旅吧！</p>
</blockquote>

<p>转发请标明原文地址：<a href="https://blog.csdn.net/qq_37142346/article/details/81222299" rel="nofollow">原文链接</a></p>

<p>上篇文章<a href="https://blog.csdn.net/qq_37142346/article/details/81204921" rel="nofollow">Spark内核架构流程深度剖析</a>一文中我们剖析了Spark核心架构以及job提交执行的流程。这篇文章将会从源码的角度出发探究其内部的实现细节，首先从SparkContext的创建以及它执行的任务开始。</p>

<p>和以前一样，依旧使用图解的方式来剖析，下面是SparContext创建的流程图： <br>
<img src="https://img-blog.csdn.net/20180726150931844?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MTQyMzQ2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>在一个Spark应用程序中必定先创建SparkContext对象，在讲SparkContext对象之前，还有一个对象值得一提，在创建了SparkConf之后，会利用它来创建一个对象SparkEnv，那么这个对象的作用是什么呢？官方给出的解释是<code>Holds all the runtime environment objects for a running Spark instance (either master or worker)</code>，也就是拥有一个运行时Spark实例（mater，worker）的所有运行时环境的对象，这些环境变量主要包括下面内容：</p>

<pre class="prettyprint"><code class=" hljs fsharp"><span class="hljs-keyword">class</span> SparkEnv (
    <span class="hljs-keyword">val</span> executorId: String,
    <span class="hljs-keyword">val</span> actorSystem: ActorSystem,
    <span class="hljs-keyword">val</span> serializer: Serializer,
    <span class="hljs-keyword">val</span> closureSerializer: Serializer,
    <span class="hljs-keyword">val</span> cacheManager: CacheManager,         <span class="hljs-comment">//用于存储中间计算结果</span>
    <span class="hljs-keyword">val</span> mapOutputTracker: MapOutputTracker, <span class="hljs-comment">//用来缓存MapStatus信息，并提供从MapOutputMaster获取信息的功能</span>
    <span class="hljs-keyword">val</span> shuffleManager: ShuffleManager,     <span class="hljs-comment">//路由维护表</span>
    <span class="hljs-keyword">val</span> broadcastManager: BroadcastManager, <span class="hljs-comment">//广播</span>
    <span class="hljs-keyword">val</span> blockTransferService: BlockTransferService,
    <span class="hljs-keyword">val</span> blockManager: BlockManager,         <span class="hljs-comment">//块管理</span>
    <span class="hljs-keyword">val</span> securityManager: SecurityManager,   <span class="hljs-comment">//安全管理</span>
    <span class="hljs-keyword">val</span> httpFileServer: HttpFileServer,     <span class="hljs-comment">//文件存储目录</span>
    <span class="hljs-keyword">val</span> sparkFilesDir: String,              <span class="hljs-comment">//文件存储目录</span>
    <span class="hljs-keyword">val</span> metricsSystem: MetricsSystem,       <span class="hljs-comment">//测量</span>
    <span class="hljs-keyword">val</span> shuffleMemoryManager: ShuffleMemoryManager,
    <span class="hljs-keyword">val</span> outputCommitCoordinator: OutputCommitCoordinator,
    <span class="hljs-keyword">val</span> conf: SparkConf                     <span class="hljs-comment">//配置文件</span>
               )</code></pre>

<p>也就是说这个对象持有集群所有节点的运行时的环境配置，对于SparkEnv 的介绍就到这里，有兴趣的读者可以详细阅读相关源码。下面我们进入今天的重点SparkContext。</p>

<p>在SparkContext这个类中有这样一段代码：</p>



<pre class="prettyprint"><code class=" hljs java">  <span class="hljs-javadoc">/**
    * 1、创建TaskSchedulerImpl
    * 2、创建了底层调度器SparkDeploySchedulerBackend
    * 3、使用TaskSchedulerImpl.initial方法初始化了底层的调度池
    */</span>
<span class="hljs-keyword">private</span>[spark] <span class="hljs-title">var</span> (schedulerBackend, taskScheduler) = SparkContext.createTaskScheduler(<span class="hljs-keyword">this</span>, master)</code></pre>

<p>我们都知道在scala中，没有在任何方法或者代码块中代码在调用主构造函数的时候都会执行，而上面这段代码没有在任何方法或者代码块中，那么在执行<code>val sc=new SparkContext(conf)</code>代码的时候会调用上面的代码createTaskScheduler方法来创建TaskScheduler接口的实现类TaskSchedulerImpl。在这个方法中实际上一些匹配规则来匹配我们提交的模式，比如local模式提交作业，如下图所示： <br>
<img src="https://img-blog.csdn.net/20180726152456834?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MTQyMzQ2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>我们今天重点来研究standalone模式提交作业的工作流程，因此在这个方法中我们重点来看这段代码：</p>



<pre class="prettyprint"><code class=" hljs scala">      <span class="hljs-javadoc">/**
        * 这种方式就是spark常用的standalone模式
        */</span>
      <span class="hljs-keyword">case</span> SPARK_REGEX(sparkUrl) =&gt;
        <span class="hljs-keyword">val</span> scheduler = <span class="hljs-keyword">new</span> TaskSchedulerImpl(sc)
        <span class="hljs-keyword">val</span> masterUrls = sparkUrl.split(<span class="hljs-string">","</span>).map(<span class="hljs-string">"spark://"</span> + _)
        <span class="hljs-comment">//创建TaskSchedulerImpl完成，创建底层SparkDeploySchedulerBackend</span>
        <span class="hljs-keyword">val</span> backend = <span class="hljs-keyword">new</span> SparkDeploySchedulerBackend(scheduler, sc, masterUrls)
        <span class="hljs-comment">//初始化TaskSchedulerImpl</span>
        scheduler.initialize(backend)
        (backend, scheduler)</code></pre>

<p>首先会创建TaskSchedulerImpl。 <br>
TaskSchedulerImpl的主要作用是什么呢？在它内部完成了什么工作呢？</p>

<p>它的主要作用是：</p>

<ol>
<li>底层通过操作一个SchedulerBackend，来调度各种不同种类的cluster（比如standalone，yarn,mesos），来调度task</li>
<li>它也可以使用本地模式来启动一个task使用LocalBackend或者设置isLocal属性为true</li>
<li>它也负责一些通过的逻辑，比如任务的调度顺序（FIFO etc…），启动推测的task执行等等</li>
<li>客户端应该首先调用initialize()和start()方法，然后通过runTasks()方法来提交task set</li>
</ol>

<p>在创建完TaskSchedulerImpl之后，它会创建对象SparkDeploySchedulerBackend。 <br>
它的作用是控制TaskSchedulerImpl，实际上是负责Master的注册，Executor的反向注册，Task任务发送到Executor。因此这个类也是至关重要的。它内部的具体信息稍后会讲到，我们接着启动流程。</p>

<p>在SparkDeploySchedulerBackend创建完成之后，就会调用TaskSchedulerImpl的initialize方法来初始化TaskSchedulerImpl和SparkDeploySchedulerBackend，源码如下：</p>



<pre class="prettyprint"><code class=" hljs scala"><span class="hljs-javadoc">/**
    * TaskScheduler初始化，会创建一个调度池，这个调度池有不同的优先级调度策略，比如FIFO等
    * <span class="hljs-javadoctag">@param</span> backend
    */</span>
  <span class="hljs-keyword">def</span> initialize(backend: SchedulerBackend) {
    <span class="hljs-comment">//初始化SparkDeploySchedulerBackend</span>
    <span class="hljs-keyword">this</span>.backend = backend
    <span class="hljs-comment">// temporarily set rootPool name to empty</span>
    rootPool = <span class="hljs-keyword">new</span> Pool(<span class="hljs-string">""</span>, schedulingMode, <span class="hljs-number">0</span>, <span class="hljs-number">0</span>)
    schedulableBuilder = {
      <span class="hljs-comment">//不同的调度策略</span>
      schedulingMode <span class="hljs-keyword">match</span> {
        <span class="hljs-keyword">case</span> SchedulingMode.FIFO =&gt;
          <span class="hljs-keyword">new</span> FIFOSchedulableBuilder(rootPool)
        <span class="hljs-keyword">case</span> SchedulingMode.FAIR =&gt;
          <span class="hljs-keyword">new</span> FairSchedulableBuilder(rootPool, conf)
      }
    }
    schedulableBuilder.buildPools()
  }</code></pre>

<p>在除了初始化上面这两个对象之外，它还会创建一个调度池，比如FIFO，FAIR等等，用于任务调度策略。</p>

<p>到这里整个createTaskScheduler方法中的代码就全部执行完毕。 <br>
可以看到，在createTaskScheduler方法的standalone模式下会做三件事情：</p>

<ol>
<li>创建TaskSchedulerImpl</li>
<li>创建底层SparkDeploySchedulerBackend</li>
<li>初始化TaskSchedulerImpl，SparkDeploySchedulerBackend，创建调度池</li>
</ol>

<p>接着它会调用TaskSchedulerImpl类中的start方法进行启动：</p>



<pre class="prettyprint"><code class=" hljs java">  <span class="hljs-keyword">private</span>[spark] <span class="hljs-title">var</span> (schedulerBackend, taskScheduler) =
    SparkContext.createTaskScheduler(<span class="hljs-keyword">this</span>, master)
  <span class="hljs-keyword">private</span> val heartbeatReceiver = env.actorSystem.actorOf(
    Props(<span class="hljs-keyword">new</span> HeartbeatReceiver(<span class="hljs-keyword">this</span>, taskScheduler)), <span class="hljs-string">"HeartbeatReceiver"</span>)
  <span class="hljs-annotation">@volatile</span> <span class="hljs-keyword">private</span>[spark] var dagScheduler: DAGScheduler = _
  <span class="hljs-keyword">try</span> {
    <span class="hljs-javadoc">/**
      * 创建DAGScheduler
      */</span>
    dagScheduler = <span class="hljs-keyword">new</span> DAGScheduler(<span class="hljs-keyword">this</span>)
  } <span class="hljs-keyword">catch</span> {
    <span class="hljs-keyword">case</span> e: Exception =&gt; {
      <span class="hljs-keyword">try</span> {
        stop()
      } <span class="hljs-keyword">finally</span> {
        <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> SparkException(<span class="hljs-string">"Error while constructing DAGScheduler"</span>, e)
      }
    }
  }

  <span class="hljs-comment">// start TaskScheduler after taskScheduler sets DAGScheduler reference in DAGScheduler's</span>
  <span class="hljs-comment">// constructor</span>
  taskScheduler.start()</code></pre>

<p>在这中间它还会创建DAGScheduler，这个对象也是非常重要的，在稍后会详细介绍，在start方法内部会调用SparkDeploySchedulerBackend的start方法：</p>



<pre class="prettyprint"><code class=" hljs avrasm"> override def start() {
    backend<span class="hljs-preprocessor">.start</span>()

    if (!isLocal &amp;&amp; conf<span class="hljs-preprocessor">.getBoolean</span>(<span class="hljs-string">"spark.speculation"</span>, false)) {
      logInfo(<span class="hljs-string">"Starting speculative execution thread"</span>)
      import sc<span class="hljs-preprocessor">.env</span><span class="hljs-preprocessor">.actorSystem</span><span class="hljs-preprocessor">.dispatcher</span>
      sc<span class="hljs-preprocessor">.env</span><span class="hljs-preprocessor">.actorSystem</span><span class="hljs-preprocessor">.scheduler</span><span class="hljs-preprocessor">.schedule</span>(SPECULATION_INTERVAL milliseconds,
            SPECULATION_INTERVAL milliseconds) {
        Utils<span class="hljs-preprocessor">.tryOrExit</span> { checkSpeculatableTasks() }
      }
    }
  }</code></pre>

<p>在SparkDeploySchedulerBackend的start方法的内部：</p>



<pre class="prettyprint"><code class=" hljs avrasm"> override def start() {
    super<span class="hljs-preprocessor">.start</span>()

    // The endpoint for executors to talk to us
    val driverUrl = AkkaUtils<span class="hljs-preprocessor">.address</span>(
      AkkaUtils<span class="hljs-preprocessor">.protocol</span>(actorSystem),
      SparkEnv<span class="hljs-preprocessor">.driverActorSystemName</span>,
      conf<span class="hljs-preprocessor">.get</span>(<span class="hljs-string">"spark.driver.host"</span>),
      conf<span class="hljs-preprocessor">.get</span>(<span class="hljs-string">"spark.driver.port"</span>),
      CoarseGrainedSchedulerBackend<span class="hljs-preprocessor">.ACTOR</span>_NAME)
    val args = Seq(
      <span class="hljs-string">"--driver-url"</span>, driverUrl,
      <span class="hljs-string">"--executor-id"</span>, <span class="hljs-string">"{{EXECUTOR_ID}}"</span>,
      <span class="hljs-string">"--hostname"</span>, <span class="hljs-string">"{{HOSTNAME}}"</span>,
      <span class="hljs-string">"--cores"</span>, <span class="hljs-string">"{{CORES}}"</span>,
      <span class="hljs-string">"--app-id"</span>, <span class="hljs-string">"{{APP_ID}}"</span>,
      <span class="hljs-string">"--worker-url"</span>, <span class="hljs-string">"{{WORKER_URL}}"</span>)
    val extraJavaOpts = sc<span class="hljs-preprocessor">.conf</span><span class="hljs-preprocessor">.getOption</span>(<span class="hljs-string">"spark.executor.extraJavaOptions"</span>)
      <span class="hljs-preprocessor">.map</span>(Utils<span class="hljs-preprocessor">.splitCommandString</span>)<span class="hljs-preprocessor">.getOrElse</span>(Seq<span class="hljs-preprocessor">.empty</span>)
    val classPathEntries = sc<span class="hljs-preprocessor">.conf</span><span class="hljs-preprocessor">.getOption</span>(<span class="hljs-string">"spark.executor.extraClassPath"</span>)
      <span class="hljs-preprocessor">.map</span>(_<span class="hljs-preprocessor">.split</span>(java<span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.File</span><span class="hljs-preprocessor">.pathSeparator</span>)<span class="hljs-preprocessor">.toSeq</span>)<span class="hljs-preprocessor">.getOrElse</span>(Nil)
    val libraryPathEntries = sc<span class="hljs-preprocessor">.conf</span><span class="hljs-preprocessor">.getOption</span>(<span class="hljs-string">"spark.executor.extraLibraryPath"</span>)
      <span class="hljs-preprocessor">.map</span>(_<span class="hljs-preprocessor">.split</span>(java<span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.File</span><span class="hljs-preprocessor">.pathSeparator</span>)<span class="hljs-preprocessor">.toSeq</span>)<span class="hljs-preprocessor">.getOrElse</span>(Nil)

    // When testing, expose the parent class path to the child. This is processed by
    // compute-classpath.{cmd,sh} <span class="hljs-keyword">and</span> makes all needed jars available to child processes
    // when the assembly is built with the <span class="hljs-string">"*-provided"</span> profiles enabled.
    val testingClassPath =
      if (sys<span class="hljs-preprocessor">.props</span><span class="hljs-preprocessor">.contains</span>(<span class="hljs-string">"spark.testing"</span>)) {
        sys<span class="hljs-preprocessor">.props</span>(<span class="hljs-string">"java.class.path"</span>)<span class="hljs-preprocessor">.split</span>(java<span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.File</span><span class="hljs-preprocessor">.pathSeparator</span>)<span class="hljs-preprocessor">.toSeq</span>
      } else {
        Nil
      }

    // Start executors with a few necessary configs for registering with the scheduler
    val sparkJavaOpts = Utils<span class="hljs-preprocessor">.sparkJavaOpts</span>(conf, SparkConf<span class="hljs-preprocessor">.isExecutorStartupConf</span>)
    val javaOpts = sparkJavaOpts ++ extraJavaOpts
    val command = Command(<span class="hljs-string">"org.apache.spark.executor.CoarseGrainedExecutorBackend"</span>,
      args, sc<span class="hljs-preprocessor">.executorEnvs</span>, classPathEntries ++ testingClassPath, libraryPathEntries, javaOpts)
    val appUIAddress = sc<span class="hljs-preprocessor">.ui</span><span class="hljs-preprocessor">.map</span>(_<span class="hljs-preprocessor">.appUIAddress</span>)<span class="hljs-preprocessor">.getOrElse</span>(<span class="hljs-string">""</span>)
    <span class="hljs-comment">/**
      * application应用程序的一些详细描述信息，包括这个application最大需要的cpu core，每个slaves需要的内存大小
      */</span>
    val appDesc = new ApplicationDescription(sc<span class="hljs-preprocessor">.appName</span>, maxCores, sc<span class="hljs-preprocessor">.executorMemory</span>, command,
      appUIAddress, sc<span class="hljs-preprocessor">.eventLogDir</span>, sc<span class="hljs-preprocessor">.eventLogCodec</span>)
    //创建AppClient
    client = new AppClient(sc<span class="hljs-preprocessor">.env</span><span class="hljs-preprocessor">.actorSystem</span>, masters, appDesc, this, conf)
    client<span class="hljs-preprocessor">.start</span>()

    waitForRegistration()
  }</code></pre>

<p>可以看出实际上最为核心的组件是底层的SparkDeploySchedulerBackend，它初始化一系列配置信息，还会将application的详细信息封装成一个类ApplicationDescription，然后创建AppClient，然后将application的信息注册到Master上，下面来看它内部的详细源码。</p>

<p>它会创建ApplicationDescription对象，这个对象是应用程序的一系列配置信息，包括集群的CPU核数，每个节点的分配内存，集群的最大核数等信息：</p>



<pre class="prettyprint"><code class=" hljs fsharp"><span class="hljs-keyword">private</span>[spark] <span class="hljs-keyword">class</span> ApplicationDescription(
    <span class="hljs-keyword">val</span> name: String,
    <span class="hljs-keyword">val</span> maxCores: Option[Int],
    <span class="hljs-keyword">val</span> memoryPerSlave: Int,
    <span class="hljs-keyword">val</span> command: Command,
    var appUiUrl: String,
    <span class="hljs-keyword">val</span> eventLogDir: Option[URI] = None,
    <span class="hljs-comment">// short name of compression codec used when writing event logs, if any (e.g. lzf)</span>
    <span class="hljs-keyword">val</span> eventLogCodec: Option[String] = None)</code></pre>

<p>接着会利用ApplicationDescription创建一个AppClient对象，实际上AppClient是 一个接口，它主要负责接收一个master-url的参数和ApplicationDescription，和集群事件监听器和各种事件发生时的监听器。</p>

<p>对象创建完毕之后，会调用AppClient对象的start方法：</p>



<pre class="prettyprint"><code class=" hljs scala"> <span class="hljs-keyword">def</span> start() {
    <span class="hljs-comment">// Just launch an actor; it will call back into the listener.</span>
    <span class="hljs-comment">//akka模型，相当于一个线程</span>
    actor = actorSystem.actorOf(Props(<span class="hljs-keyword">new</span> ClientActor))
  }

  <span class="hljs-keyword">def</span> stop() {
    <span class="hljs-keyword">if</span> (actor != <span class="hljs-keyword">null</span>) {
      <span class="hljs-keyword">try</span> {
        <span class="hljs-keyword">val</span> timeout = AkkaUtils.askTimeout(conf)
        <span class="hljs-keyword">val</span> future = actor.ask(StopAppClient)(timeout)
        Await.result(future, timeout)
      } <span class="hljs-keyword">catch</span> {
        <span class="hljs-keyword">case</span> e: TimeoutException =&gt;
          logInfo(<span class="hljs-string">"Stop request to Master timed out; it may already be shut down."</span>)
      }
      actor = <span class="hljs-keyword">null</span>
    }
  }
}</code></pre>

<p>注意这块代码<code>actor = actorSystem.actorOf(Props(new ClientActor))</code>，它还会创建ClientActor对象，它是AppClient的内部类，这个内部类的部分源码如下，它主要实现了Actor 模型：</p>



<pre class="prettyprint"><code class=" hljs scala"> <span class="hljs-javadoc">/**
    * 内部类
    */</span>
  <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">ClientActor</span> <span class="hljs-keyword">extends</span> <span class="hljs-title">Actor</span> <span class="hljs-keyword">with</span> <span class="hljs-title">ActorLogReceive</span> <span class="hljs-keyword">with</span> <span class="hljs-title">Logging</span> {</span>
    <span class="hljs-keyword">var</span> master: ActorSelection = <span class="hljs-keyword">null</span>
    <span class="hljs-keyword">var</span> alreadyDisconnected = <span class="hljs-keyword">false</span>  <span class="hljs-comment">// To avoid calling listener.disconnected() multiple times</span>
    <span class="hljs-keyword">var</span> alreadyDead = <span class="hljs-keyword">false</span>  <span class="hljs-comment">// To avoid calling listener.dead() multiple times</span>
    <span class="hljs-keyword">var</span> registrationRetryTimer: Option[Cancellable] = None</code></pre>

<p>在这个内部类中有两个重要的方法用来向master注册信息：</p>



<pre class="prettyprint"><code class=" hljs scala"><span class="hljs-keyword">def</span> tryRegisterAllMasters() {
      <span class="hljs-keyword">for</span> (masterAkkaUrl &lt;- masterAkkaUrls) {
        logInfo(<span class="hljs-string">"Connecting to master "</span> + masterAkkaUrl + <span class="hljs-string">"..."</span>)
        <span class="hljs-keyword">val</span> actor = context.actorSelection(masterAkkaUrl)
        <span class="hljs-comment">//将ApplicationDescription封装在case class中发送到master中去注册</span>
        actor ! RegisterApplication(appDescription)
      }
    }

    <span class="hljs-keyword">def</span> registerWithMaster() {
      tryRegisterAllMasters()
      <span class="hljs-keyword">import</span> context.dispatcher
      <span class="hljs-keyword">var</span> retries = <span class="hljs-number">0</span>
      registrationRetryTimer = Some {
        context.system.scheduler.schedule(REGISTRATION_TIMEOUT, REGISTRATION_TIMEOUT) {
          Utils.tryOrExit {
            retries += <span class="hljs-number">1</span>
            <span class="hljs-keyword">if</span> (registered) {
              registrationRetryTimer.foreach(_.cancel())
            } <span class="hljs-keyword">else</span> <span class="hljs-keyword">if</span> (retries &gt;= REGISTRATION_RETRIES) {
              markDead(<span class="hljs-string">"All masters are unresponsive! Giving up."</span>)
            } <span class="hljs-keyword">else</span> {
              tryRegisterAllMasters()
            }
          }
        }
      }
    }</code></pre>

<p>核心代码块是下面的代码：</p>



<pre class="prettyprint"><code class=" hljs coffeescript"><span class="hljs-regexp">//</span>将ApplicationDescription封装在<span class="hljs-reserved">case</span> <span class="hljs-class"><span class="hljs-keyword">class</span>中发送到<span class="hljs-title">master</span>中去注册</span>
        actor ! RegisterApplication(appDescription)</code></pre>

<p>RegisterApplication实际上封装了ApplicationDescription信息的case class，然后将这些application的信息发送给master。（注意上面是代码scala的akka模型发送消息的方式，不理解的读者可以查阅相关资料）</p>



<pre class="prettyprint"><code class=" hljs scala"><span class="hljs-class"><span class="hljs-keyword">case</span> <span class="hljs-keyword">class</span> <span class="hljs-title">RegisterApplication</span><span class="hljs-params">(appDescription: ApplicationDescription)</span></span>
    <span class="hljs-keyword">extends</span> DeployMessage</code></pre>

<p>至此最为关键的TaskSchedulerImpl以及相关组件的创建，初始化我们已经详细介绍完毕，接下里看另外两个创建SparkContext时的重要组件DAGScheduler和SparkUI。</p>

<p>对于DAGScheduler官方详细的解释如下：</p>

<p> <br>
DAGScheduler是一个面向stage调度的高调度层次的工具，它会根据stage的每一个job会计算出一个DAG（有向无环图）。并且追踪每个RDD和stage的输出是否被物化（写入磁盘或者内存等地方），并且来寻找一个最优的调度策略来运行这个job，然后会提交stage封装在TaskSet到底层的TaskSchedulerImpl中将他们运行在集群上。除了处理stage的DAG的构建，这个类还负责每个运行task的最佳位置（RDD的本地性），基于当前的缓存状态，将这些提交到底层 的TaskChedulerImpl。此外，它还会因为shuffle输出的文件丢失而操作失败，在这种情况下旧的stage可能会被重新提交。如果提交失败不是由于shuffle文件丢失导致的，那么这种情况下会被TaskScheduler来处理，它会每隔很小的时间间隔重新提交每一个task，到一定情况下会取消整个stage。 <br>
 <br>
DAGScheduler核心就是构建DAG，关于DAGScheduler更加详细的信息读者可自行阅读相关源码。</p>

<p>对于SparkUI，只是简单提一下，不做过多解释，它实际上是启动一个jettty服务器，创建了一个web应用，启动了4040端口：</p>



<pre class="prettyprint"><code class=" hljs python">private[spark] object SparkUI {
  val DEFAULT_PORT = <span class="hljs-number">4040</span>
  val STATIC_RESOURCE_DIR = <span class="hljs-string">"org/apache/spark/ui/static"</span>

  <span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">getUIPort</span><span class="hljs-params">(conf: SparkConf)</span>:</span> Int = {
    conf.getInt(<span class="hljs-string">"spark.ui.port"</span>, SparkUI.DEFAULT_PORT)
  }</code></pre>

<p><strong>注意事项：本篇文章解读的源码是Spark1.3.0的源码（至于为什么选择1.3，机缘巧合φ(&gt;ω&lt;*) ）</strong></p>

<p>至此，关于SparkContext的剖析到这里就接近尾声了，阅读源码的目的不仅仅是更加了解Spark的内部结构，更可以给我们日常编写程序时可以更快定位bug以及优化程序。更为重要的还可以提高我们的编码的技能以及思维。在阅读源码的技巧上我会多多努力，当然，要想了解spark的内部架构不仅仅是通过阅读一篇博客可以搞懂，如果想要深入读者可以自行阅读源码。如果本篇文章有任何不对的地方，还望不吝赐教，谢谢阅读！！！</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>