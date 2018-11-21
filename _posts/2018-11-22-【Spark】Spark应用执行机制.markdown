---
layout:     post
title:      【Spark】Spark应用执行机制
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2 id="Spark应用概念">Spark应用概念</h2>
<p>Spark应用（Application）是用户提交的应用程序。执行模式又Local、Standalone、YARN、Mesos。根据Spark Application的Driver Program是否在集群中运行，Spark应用的运行方式又可以分为Cluster模式和Client模式。<br>
下面是Spark应用涉及的一些基本概念：</p>
<blockquote>
<ul><li>Application：Spark 的应用程序，用户提交后，Spark为App分配资源，将程序转换并执行，其中Application包含一个Driver program和若干Executor</li><li>SparkContext：Spark 应用程序的入口，负责调度各个运算资源，协调各个 Worker<br>
Node 上的 Executor</li><li>Driver Program：运行Application的main()函数并且创建SparkContext</li><li>RDD Graph：RDD是Spark的核心结构， 可以通过一系列算子进行操作（主要有Transformation和Action操作）。当RDD遇到Action算子时，将之前的所有算子形成一个有向无环图（DAG）。再在Spark中转化为Job，提交到集群执行。一个App中可以包含多个Job</li><li>Executor：是为Application运行在Worker node上的一个进程，该进程负责运行Task，并且负责将数据存在内存或者磁盘上。每个Application都会申请各自的Executor来处理任务</li><li>Worker Node：集群中任何可以运行Application代码的节点，运行一个或多个Executor进程</li></ul></blockquote>
<p>下面介绍Spark Application运行过程中各个组件的概念：</p>
<blockquote>
<ul><li>Job：一个RDD Graph触发的作业，往往由Spark Action算子触发，在SparkContext中通过runJob方法向Spark提交Job</li><li>Stage：每个Job会根据RDD的宽依赖关系被切分很多Stage， 每个Stage中包含一组相同的Task， 这一组Task也叫TaskSet</li><li>Task：一个分区对应一个Task，Task执行RDD中对应Stage中包含的算子。Task被封装好后放入Executor的线程池中执行</li><li>DAGScheduler：根据Job构建基于Stage的DAG，并提交Stage给TaskScheduler</li><li>TaskScheduler：将Taskset提交给Worker node集群运行并返回结果</li></ul></blockquote>
<p><img src="http://7nj1qk.com1.z0.glb.clouddn.com/@/spark/internal/spark_application.jpg" alt=""></p>
<h2 id="Spark执行机制概况"><a href="http://blog.jasonding.top/2015/07/08/Spark/%E3%80%90Spark%E3%80%91Spark%E5%BA%94%E7%94%A8%E6%89%A7%E8%A1%8C%E6%9C%BA%E5%88%B6/#Spark%E6%89%A7%E8%A1%8C%E6%9C%BA%E5%88%B6%E6%A6%82%E5%86%B5" rel="nofollow" class="headerlink" title="Spark执行机制概况"></a>Spark执行机制概况</h2>
<h3 id="Spark应用转换"><a href="http://blog.jasonding.top/2015/07/08/Spark/%E3%80%90Spark%E3%80%91Spark%E5%BA%94%E7%94%A8%E6%89%A7%E8%A1%8C%E6%9C%BA%E5%88%B6/#Spark%E5%BA%94%E7%94%A8%E8%BD%AC%E6%8D%A2" rel="nofollow" class="headerlink" title="Spark应用转换"></a>Spark应用转换</h3>
<p>RDD的Action算子触发Job的提交，提交到Spark中的Job生成RDD DAG，由DAGScheduler转化为Stage DAG，每个Stage中产生相应的Task集合，TaskScheduler将任务分发到Executor执行。 每个任务对应相应的一个数据块，使用用户定义的函数处理数据块。<br><img src="http://7nj1qk.com1.z0.glb.clouddn.com/@/spark/internal/spark_execution1.jpg" alt=""></p>
<h3 id="Spark执行的底层实现"><a href="http://blog.jasonding.top/2015/07/08/Spark/%E3%80%90Spark%E3%80%91Spark%E5%BA%94%E7%94%A8%E6%89%A7%E8%A1%8C%E6%9C%BA%E5%88%B6/#Spark%E6%89%A7%E8%A1%8C%E7%9A%84%E5%BA%95%E5%B1%82%E5%AE%9E%E7%8E%B0" rel="nofollow" class="headerlink" title="Spark执行的底层实现"></a>Spark执行的底层实现</h3>
<p>在Spark的底层实现中，通过RDD进行数据的管理，RDD中有一组分布在不同节点的数据块，当Spark的应用在对这个RDD进行操作时，调度器将包含操作的任务分发到指定的机器上执行，在计算节点通过多线程的方式执行任务。一个操作执行完毕，RDD便转换为另一个RDD，这样，用户的操作依次执行。Spark为了系统的内存不至于快速用完，使用延迟执行的方式执行，即只有操作累计到Action（行动），算子才会触发整个操作序列的执行，中间结果不会单独再重新分配内存，而是在同一个数据块上进行流水线操作。</p>
<p>Spark实现了分布式计算和任务处理，并实现了任务的分发、跟踪、执行等工作，最终聚合结果，完成Spark应用的计算。<br>
对RDD的块管理通过BlockManger完成，BlockManager将数据抽象为数据块，在内存或者磁盘进行存储，如果数据不在本节点，则还可以通过远端节点复制到本机进行计算。<br>
在计算节点的执行器Executor中会创建线程池，这个执行器将需要执行的任务通过线程池并发执行。<br><img src="http://7nj1qk.com1.z0.glb.clouddn.com/@/spark/internal/spark_execution2.jpg" alt=""></p>
<h2 id="应用提交和执行方式"><a href="http://blog.jasonding.top/2015/07/08/Spark/%E3%80%90Spark%E3%80%91Spark%E5%BA%94%E7%94%A8%E6%89%A7%E8%A1%8C%E6%9C%BA%E5%88%B6/#%E5%BA%94%E7%94%A8%E6%8F%90%E4%BA%A4%E5%92%8C%E6%89%A7%E8%A1%8C%E6%96%B9%E5%BC%8F" rel="nofollow" class="headerlink" title="应用提交和执行方式"></a>应用提交和执行方式</h2>
<p>应用的提交包含以下两种方式：</p>
<ul><li>Driver进程运行在客户端，对应用进行管理监控</li><li>主节点指定某个Worker节点启动Driver，负责整个应用的监控</li></ul><p>Driver进程是应用的主控进程，负责应用的解析、切分Stage并调度Task到Executor执行，包含DAGScheduler等重要对象。<br>
下面是具体的介绍：</p>
<h3 id="Driver进程运行在客户端"><a href="http://blog.jasonding.top/2015/07/08/Spark/%E3%80%90Spark%E3%80%91Spark%E5%BA%94%E7%94%A8%E6%89%A7%E8%A1%8C%E6%9C%BA%E5%88%B6/#Driver%E8%BF%9B%E7%A8%8B%E8%BF%90%E8%A1%8C%E5%9C%A8%E5%AE%A2%E6%88%B7%E7%AB%AF" rel="nofollow" class="headerlink" title="Driver进程运行在客户端"></a>Driver进程运行在客户端</h3>
<p><img src="http://7nj1qk.com1.z0.glb.clouddn.com/@/spark/internal/app_submit1.jpg" alt=""><br>
该方式应用执行流程：</p>
<blockquote>
<ol><li>用户启动客户端，之后客户端运行用户程序，启动Driver进程。在Driver中启动或实例化DAGScheduler等组件。 客户端的Driver向Master注册。</li><li>Worker向Master注册，Master命令Worker启动Exeuctor。Worker通过创建ExecutorRunner线程，在ExecutorRunner线程内部启动ExecutorBackend进程。</li><li>ExecutorBackend启动后，向客户端Driver进程内的SchedulerBackend注册，这样Driver进程就能找到计算资源。Driver的DAGScheduler解析应用中的RDD DAG并生成相应的Stage，每个Stage包含的TaskSet通过TaskScheduler分配给Executor。 在Executor内部启动线程池并行化执行Task。</li></ol></blockquote>
<h3 id="Driver进程在Worker节点运行"><a href="http://blog.jasonding.top/2015/07/08/Spark/%E3%80%90Spark%E3%80%91Spark%E5%BA%94%E7%94%A8%E6%89%A7%E8%A1%8C%E6%9C%BA%E5%88%B6/#Driver%E8%BF%9B%E7%A8%8B%E5%9C%A8Worker%E8%8A%82%E7%82%B9%E8%BF%90%E8%A1%8C" rel="nofollow" class="headerlink" title="Driver进程在Worker节点运行"></a>Driver进程在Worker节点运行</h3>
<p><img src="http://7nj1qk.com1.z0.glb.clouddn.com/@/spark/internal/app_submit2.jpg" alt=""><br>
该方式应用执行流程：</p>
<blockquote>
<ol><li>用户启动客户端，客户端提交应用程序给Master。</li><li>Master调度应用，针对每个应用分发给指定的一个Worker启动Driver，即Scheduler-Backend。 Worker接收到Master命令后创建DriverRunner线程，在DriverRunner线程内创建SchedulerBackend进程。Driver充当整个作业的主控进程。Master会指定其他Worker启动Exeuctor，即ExecutorBackend进程，提供计算资源。流程和上面很相似，Worker创建ExecutorRunner线程，ExecutorRunner会启动ExecutorBackend进程。</li><li>ExecutorBackend启动后，向Driver的SchedulerBackend注册，这样Driver获取了计算资源就可以调度和将任务分发到计算节点执行。SchedulerBackend进程中包含DAGScheduler，它会根据RDD的DAG切分Stage，生成TaskSet，并调度和分发Task到Executor。对于每个Stage的TaskSet，都会被存放到TaskScheduler中。TaskScheduler将任务分发到Executor，执行多线程并行任务。</li></ol></blockquote>
<p><span>本文转自如下博客，所有权利归原作者所有。</span></p>
<p><span>转载请注明作者Jason Ding及其出处</span><br><a href="http://jasonding1354.gitcafe.io/" rel="nofollow">GitCafe博客主页(http://jasonding1354.gitcafe.io/)</a><br><a href="http://jasonding1354.github.io/" rel="nofollow">Github博客主页(http://jasonding1354.github.io/)</a><br><a href="http://blog.csdn.net/jasonding1354" rel="nofollow">CSDN博客(http://blog.csdn.net/jasonding1354)</a><br><a href="http://www.jianshu.com/users/2bd9b48f6ea8/latest_articles" rel="nofollow">简书主页(http://www.jianshu.com/users/2bd9b48f6ea8/latest_articles)</a><br><span>Google搜索jasonding1354进入我的博客主页</span></p>
            </div>
                </div>