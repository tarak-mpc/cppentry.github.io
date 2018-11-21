---
layout:     post
title:      Spark学习笔记一：Spark.core
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>一、Spark.core</p>

<p>Spark生态圈以Spark.core为核心，从HDFS、Hbase等持久层读取数据，以MESS、YARN和自身有的Standalone为资源管理器调度Job完成Spark应用程序的计算。这些应用程序可以来源于不同的Spark组件。如Spark shell 或spark commit 的批处理、Spark streaming的实时处理应用，Spark sql的查询，ML/MLIB的机器学习等。</p>

<p>Spark.core的内核架构：</p>

<ol><li>提供 了DAG有向无环图的分布式计算框架，并提供 了cache机制支持多次迭代或数据共享，大大减少了IO的开销，对需要多次迭代的数据挖掘和分析，性能有很大提升。</li>
	<li>引入了RDD数据集，它是分布在一组节点中的只读对象集合，这些集合是弹性的，如果数据集中一部分丢失，则可以根据“血统”（即数据产生的逻辑）对它们进行重建。另外RDD计算时可以通过Checkpoint设置检查点来实现容错。</li>
	<li>移动计算而不移动数据，RDD partition可以就近读取分布式系统中的数据到各自的节点内存中计算</li>
	<li>使用多线程池模型来减少tash启动的开销</li>
</ol><p> </p>

<p>Spark具体是如何工作的？总的来讲，当一个应用程序提交到Spark集群后，Spark会在集群中找一台不那么忙的机器，启动Driver进程开始工作，在action算子触发一个job后，DAGscheduler引擎把job分成数个Stage，这里划分Stage是以ShuffleDependency为边界进行划分，而Stage中又根据应用计算的不同，划分为多个task，这些task组成taskset集合给taskscheduler来进行管理调度，遵循“先进先出”原则。接下来就具体计算资源的申请了，可以用自身的standalone作为资源管理器，也可以使用其他如YARN。下面具体来讲讲</p>

<p><img alt="" class="has" height="490" src="https://img-blog.csdnimg.cn/20181105105945984.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0RhbnlZYW4=,size_16,color_FFFFFF,t_70" width="959"></p>

<p>Driver的任务提交过程</p>

<p><img alt="" class="has" height="268" src="https://img-blog.csdnimg.cn/20181105110348431.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0RhbnlZYW4=,size_16,color_FFFFFF,t_70" width="606"></p>

<ol><li>Driver程序的代码运行到action操作，触发了SparkContext的runJob方法。</li>
	<li>SparkContext调用DAGScheduler的runJob函数。</li>
	<li>DAGScheduler把Job划分stage，然后把stage转化为相应的Tasks，把Tasks交给TaskScheduler。</li>
	<li>通过TaskScheduler把Tasks添加到任务队列当中，交给SchedulerBackend进行资源分配和任务调度。</li>
	<li>调度器给Task分配执行Executor，ExecutorBackend负责执行Task。</li>
</ol><p>二、SPARK调度管理</p>

<p>上面我们可以知道SPARK主要以DAGScheduler和TaskScheduler来调度相关资源的。</p>

<p>TaskScheduler负责每个具体任务的实际物理调度，DAGScheduler负责将作业拆分成不同阶段的具有依赖关系的多批任务，可以理解为DAGScheduler负责任务的逻辑调度。</p>

<p><img alt="" class="has" height="450" src="https://img-blog.csdnimg.cn/20181105110742732.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0RhbnlZYW4=,size_16,color_FFFFFF,t_70" width="566"></p>

<p>这里讲一下有关的概念：</p>

<ul style="margin-left:.375in;"><li>Task（任务）：单个分区数据及上的最小处理流程单元。</li>
	<li>TaskSet（任务集）：由一组关联的，但互相之间没有Shuffle依赖关系的任务所组成的任务集。</li>
	<li>Stage（调度阶段）：一个任务集对应的调度阶段。</li>
	<li>Job（作业）：有一个RDD Action生成的一个或多个调度阶段所组成的一次计算作业。</li>
	<li>Application（应用程序）：Spark应用程序，由一个或多个作业组成。</li>
</ul><p>关系如图：<br><img alt="" class="has" height="366" src="https://img-blog.csdnimg.cn/20181105110916268.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0RhbnlZYW4=,size_16,color_FFFFFF,t_70" width="652"></p>

<ul><li>调度阶段的拆分STAGE</li>
</ul><p>一个Spark任务提交后，DAGScheduler从RDD依赖链末端的RDD出发，遍历整个RDD依赖链，将Job分解成具有前后依赖关系的多个stage。DAGScheduler是根据ShuffleDependency划分stage的，也就是说当某个RDD的运算需要将数据进行shuffle操作时，这个包含了shuffle依赖关系的RDD将被用来作为输入信息，构建一个新的调度阶段。以此为依据划分调度阶段，可以确保有依赖关系的数据能够按照正确的顺序得到处理和运算。</p>

<p><img alt="" class="has" height="449" src="https://img-blog.csdnimg.cn/20181105105849538.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0RhbnlZYW4=,size_16,color_FFFFFF,t_70" width="553"></p>

<p>那怎么划分STAGE呢？上图就是对应Spark应用程序代码生成的Stage。它的具体划分依据是根据RDD的依赖关系进行，在遇到宽依赖时将两个RDD划分为不同的Stage。那什么是宽依赖和窄依赖呢？从上图中可以看到，RDD G与RDD F间的依赖是宽依赖，所以RDD F与 RDD G被划分为不同的Stage，而RDD G 与 RDD 间为窄依赖，因此 RDD B 与 RDD G被划分为同一个Stage。通过这种递归的调用方式，将所有RDD进行划分。</p>

<p>由于Spark的算子构建一般都是链式的，这就涉及了要如何进行这些链式计算，Spark的策略是对这些算子，先划分Stage，然后在进行计算。由于数据是分布式的存储在各个节点上的，所以为了减少网络传输的开销，就必须最大化的追求数据本地性，所谓的数据本地性是指，在计算时，数据本身已经在内存中或者利用已有缓存无需计算的方式获取数据。这个也RDD的一个特性。</p>

<p><strong>Stage划分总结</strong><br><br>
(1)一个Job由多个Stage构成<br><br>
一个Job可以有一个或者多个Stage，Stage划分的依据就是宽依赖，产生宽依赖的算子：reduceByKey、groupByKey等等<br><br>
(2)根据依赖关系，从前往后依次执行多个Stage<br><br>
SparkApplication 中可以因为不同的Action触发众多的Job，也就是说一个Application中可以有很多的Job，每个Job是有一个或者多个Stage构成，后面的Stage依赖前面的Stage，也就是说只有前面的Stage计算完后，后面的Stage才会运行。<br><br>
(3)Stage的执行时Lazy级别的<br><br>
所有的Stage会形成一个DAG（有向无环图），由于RDD的Lazy特性，导致Stage也是Lazy级别的，只有遇到了Action才会真正发生作业的执行，在Action之前，Spark框架只是将要进行的计算记录下来，并没有真的执行。</p>

<ul><li>
	<p><strong><span style="color:#2e75b5;">调度阶段的提交</span></strong></p>
	</li>
</ul><p>在划分Stage的步骤中会得到一个或多个有依赖关系的Stage，其中直接触发作业的RDD关联的调度阶段被称为<strong>FinalStage</strong>，DAGScheduler从FinalStage开始生成一个Job。Job和Stage的关系存储在一个映射表中，用于在该调度阶段全部完成时做一些后续处理，如报告状态、清理作业相关数据等。</p>

<p>具体提交一个Stage时，首先判断其依赖的所有父Stage的结果是否可用。如果所有父Stage的结果都可用，则提交该Stage。如果有任何一个父Stage的结果不可用，则尝试迭代提交当前不可用的父Stage。在迭代过程中，父Stage还未运行的Stage都被放到等待队列中，等待将来被提交。</p>

<p><img alt="" class="has" height="381" src="https://img-blog.csdnimg.cn/20181105112115545.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0RhbnlZYW4=,size_16,color_FFFFFF,t_70" width="667"></p>

<p>上图是一个具有四个调度阶段的Job的Stage提交顺序</p>

<p>当一个属于中间过程调度阶段的任务（这种类型的任务所对应的类为ShuffleMapTask）完成后，DAGScheduler会检查对应调度阶段的所有任务是否都完成了。如果完成了，则DAGScheduler将重新扫描一次等待列表中所有的Stage，检查它们是否还有依赖的Stage没有完成。如果所有依赖的Stage都已执行完毕，则提交该Stage。</p>

<p>任务结果的获取</p>

<p>根据任务结果的大小不同，ResultTask返回的结果分为两中形式：</p>

<ul style="margin-left:.375in;"><li>如果结果足够小，则直接放在DirectTaskResult对象内。</li>
	<li>如果超过特定尺寸（默认约10MB），则在Executor端会将DirectTaskResult序列化，将序列化的结果作为一个数据块存放在BlockManager中，然后将BlockManager返回的BlockId放在IndirectTaskResult对象中返回给TaskScheduler，TaskScheduler进而调用TaskResultGetter将IndirectTaskResult中的BlockId取出并通过BlockManager最终取得对应的DirectTaskResult。</li>
</ul><p> </p>

<p> </p>

<ul><li>作业执行阶段</li>
</ul><p>当stage划分为多个task后，TaskScheduler开始对task进度。</p>

<p><img alt="" class="has" height="600" src="https://img-blog.csdnimg.cn/2018110511302946.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0RhbnlZYW4=,size_16,color_FFFFFF,t_70" width="806"></p>

<p>资源管理器会为应用程序启动一定数量的Executor进程，每个Executor进程都占有一定数量的内存和CPU core。然后开始执行所分配的task任务，一个stage的所有task都执行完毕之后，会在各个节点本地的磁盘文件中写入计算中间结果，然后Driver就会调度运行下一个stage。下一个stage的task的输入数据就是上一个stage输出的中间结果。如此循环往复，直到将我们自己编写的代码逻辑全部执行完，并且计算完所有的数据，得到我们想要的结果为止。</p>

<p> </p>

<p>以上就是spark的运行原理，要进行相关参数调优，理解其原理是前提。</p>

<p> </p>

<p>三、RDD</p>

<p>RDD是一个分布式数据集，顾名思义，其数据应该分部存储于多台机器上。事实上，每个RDD的数据都以Block的形式存储于多台机器上，下图是Spark的RDD存储架构图，其中每个Executor会启动一个BlockManagerSlave，并管理一部分Block；而Block的元数据由Driver节点的BlockManagerMaster保存。BlockManagerSlave生成Block后向BlockManagerMaster注册该Block，BlockManagerMaster管理RDD与Block的关系，当RDD不再需要存储的时候，将向BlockManagerSlave发送指令删除相应的Block。如下图</p>

<p><img alt="" class="has" height="297" src="https://img-blog.csdnimg.cn/20181105113617246.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0RhbnlZYW4=,size_16,color_FFFFFF,t_70" width="640"></p>

<p>RDD的五大特性</p>

<p>1.A list of partitions<br>
RDD是一个由多个partition（某个节点里的某一片连续的数据）组成的的list；将数据加载为RDD时，一般会遵循数据的本地性（一般一个hdfs里的block会加载为一个partition）。<br>
2.A function for computing each split<br>
RDD的每个partition上面都会有function，也就是函数应用，其作用是实现RDD之间partition的转换。<br>
3.A list of dependencies on other RDDs<br>
RDD会记录它的依赖 ，为了容错（重算，cache，checkpoint），也就是说在内存中的RDD操作时出错或丢失会进行重算。<br>
4.Optionally,a Partitioner for Key-value RDDs<br>
  可选项，如果RDD里面存的数据是key-value形式，则可以传递一个自定义的Partitioner进行重新分区，例如这里自定义的Partitioner是基于key进行分区，那则会将不同RDD里面的相同key的数据放到同一个partition里面<br>
5.Optionally, a list of preferred locations to compute each split on<br>
最优的位置去计算，也就是数据的本地性。     <br>
 </p>

<p><em>RDD cache的原理</em></p>

<p>RDD的转换过程中，并不是每个RDD都会存储，<strong>如果某个RDD会被重复使用，或者计算其代价很高</strong>，那么可以通过显示调用RDD提供的cache()方法，把该RDD存储下来。那RDD的cache是如何实现的呢？RDD中提供的cache()方法只是简单的把该RDD放到cache列表中。当RDD的iterator被调用时，通过CacheManager把RDD计算出来，并存储到BlockManager中，下次获取该RDD的数据时便可直接通过CacheManager从BlockManager读出。</p>

<p><em>RDD dependency与DAG</em></p>

<p>在讲DAG划分stage时我们就讲过 RDD之间的依赖关系分为两种：宽依赖ShuffleDependency与窄依赖 NarrowDependency  </p>

<p>其中ShuffleDependency为子RDD的每个Partition都依赖于父RDD的所有Partition，而NarrowDependency则只依赖一个或部分的Partition。下图的groupBy与join操作是ShuffleDependency，map和union是NarrowDependency。</p>

<p><em>RDD partitioner与并行度</em></p>

<p>    每个RDD都有Partitioner属性，它决定了该RDD如何分区，当然Partition的个数还将决定每个Stage的Task个数。当前Spark需要应用设置Stage的并行Task个数（配置项为：spark.default.parallelism），在未设置的情况下，子RDD会根据父RDD的Partition决定，如map操作下子RDD的Partition与父Partition完全一致，Union操作时子RDD的Partition个数为父Partition个数之和。 如何设置spark.default.parallelism对用户是一个挑战，它会很大程度上决定Spark程序的性能。</p>

<p>四、相关文档</p>

<p>官方说明文档：<a href="http://spark.apache.org/docs/latest/rdd-programming-guide.html#resilient-distributed-datasets-rdds" rel="nofollow">http://spark.apache.org/docs/latest/rdd-programming-guide.html#resilient-distributed-datasets-rdds</a></p>

<p>官方API：<a href="http://spark.apache.org/docs/latest/api/python/index.html" rel="nofollow">http://spark.apache.org/docs/latest/api/python/index.html</a></p>

<p>五、常用API与核心类（python）</p>

<p>SparkConf：</p>

<p>Configuration for a Spark application. Used to set various Spark parameters as key-value pairs.</p>

<p>配置Spark application</p>

<p><a href="http://spark.apache.org/docs/latest/api/python/pyspark.html#pyspark.SparkConf" rel="nofollow">http://spark.apache.org/docs/latest/api/python/pyspark.html#pyspark.SparkConf</a></p>

<p> </p>

<p>SparkContext：</p>

<p>Main entry point for Spark functionality. A SparkContext represents the connection to a Spark cluster, and can be used to create RDD and broadcast variables on that cluster.</p>

<p>Spark功能的主要入口点。 SparkContext表示与Spark群集的连接，可用于在该群集上创建RDD和广播变量。</p>

<p>链接：<a href="http://spark.apache.org/docs/latest/api/python/pyspark.html#pyspark.SparkContext" rel="nofollow">http://spark.apache.org/docs/latest/api/python/pyspark.html#pyspark.SparkContext</a></p>

<p>RDD:</p>

<p>A Resilient Distributed Dataset (RDD), the basic abstraction in Spark. Represents an immutable, partitioned collection of elements that can be operated on in parallel.</p>

<p>弹性分布式数据集（RDD），Spark中的基本抽象。 表示可以并行操作的不可变的分区元素集合。</p>

<p><a href="http://spark.apache.org/docs/latest/api/python/pyspark.html#pyspark.RDD" rel="nofollow">http://spark.apache.org/docs/latest/api/python/pyspark.html#pyspark.RDD</a></p>

<p>StorageLevel:</p>

<p>Flags for controlling the storage of an RDD. Each StorageLevel records whether to use memory</p>

<p>配置存储级别</p>

<p><a href="http://spark.apache.org/docs/latest/api/python/pyspark.html#pyspark.StorageLevel" rel="nofollow">http://spark.apache.org/docs/latest/api/python/pyspark.html#pyspark.StorageLevel</a></p>            </div>
                </div>