---
layout:     post
title:      Spark学习（一）Spark介绍
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h3>一、什么是spark</h3>

<p>spark是基于内存计算的大数据并行计算框架，也是hadoop中的mapreduce的替代方案，但和mapreduce又有许多不同。      </p>

<p>Spark包含了大数据领域常见的各种计算框架：比如Spark Core用于离线计算，Spark SQL用于交互式查询，Spark Streaming用于实时流式计算，Spark MLlib用于机器学习，Spark GraphX用于图计算。<br>
      Spark主要用于大数据的计算，而Hadoop以后主要用于大数据的存储（比如HDFS、Hive、HBase等），以及资源调度（Yarn）。<br>
      Spark+Hadoop的组合，是未来大数据领域最热门的组合，也是最有前景的组合！</p>

<h3>二、对比hadoop</h3>

<p>1、最明显的一点，spark要比mapreduce快的多，官网的数据是，spark在硬盘上运行要比mapreduce快上十倍以上，而在内存中则是快到百倍，主要原因有以下几点（个人总结，也许不够全面）</p>

<ul><li>spark是基于内存计算，而hadoop基于磁盘。很显然spark速度更快且更适合迭代运算。</li>
	<li>Spark对分散的数据集进行抽样，创新地提出RDD(ResilientDistributedDataset)的概念，所有的统计分析任务被翻译成对RDD的基本操作组成的有向无环图(DAG)。RDD可以被驻留在RAM中，往后的任务可以直接读取RAM中的数据；同时分析DAG中任务之间的依赖性可以把相邻的任务合并，从而减少了大量不准确的结果输出，极大减少了HarddiskI/O，使复杂数据分析任务更高效。从这个推算，如果任务够复杂，Spark比Map/Reduce快一到两倍。</li>
	<li>mr基于进程，每个task的启动和结束都会开启或关闭进程，耗费了大量的时间。而spark基于线程，每次只有启动excutor的时候会启动进程，task都是在线程中进行。</li>
	<li>spark采用DAG（有向无环图，后面会介绍），避免了冗余的mr操作。</li>
</ul><p>2、hadoop的底层是java编写的，而spark是用scale编写的，虽然两者都提供了丰富的api可以支持不同的语言，但想深入学习一下spark最好还是使用scale。</p>

<p>3、相比hadoop 的一个mr程序需要写map，reduce，driver等一大堆类和方法，spark程序则要精简的多，拿wordcount来说：</p>

<pre class="has">
<code>object WordCount {
  
  def main(args: Array[String]) {
    val conf = new SparkConf().setAppName("WordCount");
    val wordCounts= new SparkContext(conf)
                    .textFile("hdfs://spark1:9000/spark.txt", 1)
                    .flatMap { line =&gt; line.split(" ") }   
                    .map { word =&gt; (word, 1) }   
                    .reduceByKey { _ + _ }
                    .foreach(wordCount =&gt; println(wordCount._1 + " appeared " + wordCount._2 + " times."))  
  }
  
}</code></pre>

<p>只需两行代码（虽然第二行略长了一些），就完成了一个wordCount，要知道写一个mr的wordcount得多麻烦。而这也是因为spark的RDD对许多操作进行了抽象，使得我们更方便使用。</p>

<p>4、相比hadoop，spark较为不稳定，由于大量数据被缓存在内存中，Java回收垃圾缓慢的情况严重，导致Spark性能不稳定，在复杂场景中SQL的性能甚至不如现有的Map/Reduce。也是因为数据存在内存中的原因，当数据量过大超出内存空间时会产生非常可怕的后果。所以在处理大量数据方面还是使用hadoop较为合理</p>

<h3>三、生态系统</h3>

<p>（参考自：<a href="https://blog.csdn.net/zhang123456456/article/details/78207184" rel="nofollow">https://blog.csdn.net/zhang123456456/article/details/78207184</a>）</p>

<p>Spark生态圈也称为BDAS（伯克利数据分析栈），是伯克利APMLab实验室打造的，力图在算法（Algorithms）、机器（Machines）、人（People）之间通过大规模集成来展现大数据应用的一个平台。伯克利AMPLab运用大数据、云计算、通信等各种资源以及各种灵活的技术方案，对海量不透明的数据进行甄别并转化为有用的信息，以供人们更好的理解世界。该生态圈已经涉及到机器学习、数据挖掘、数据库、信息检索、自然语言处理和语音识别等多个领域。</p>

<p>Spark生态圈以Spark Core为核心，从HDFS、Amazon S3和HBase等持久层读取数据，以MESS、YARN和自身携带的Standalone为资源管理器调度Job完成Spark应用程序的计算。 这些应用程序可以来自于不同的组件，如Spark Shell/Spark Submit的批处理、Spark Streaming的实时处理应用、Spark SQL的即时查询、BlinkDB的权衡查询、MLlib/MLbase的机器学习、GraphX的图处理和SparkR的数学计算等等。</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20160709170911217?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"></p>

<p> </p>

<p><a name="t8"></a>1 Spark Core</p>

<p>前面介绍了Spark Core的基本情况，以下总结一下Spark内核架构：</p>

<p>l  提供了有向无环图（DAG）的分布式并行计算框架，并提供Cache机制来支持多次迭代计算或者数据共享，大大减少迭代计算之间读取数据局的开销，这对于需要进行多次迭代的数据挖掘和分析性能有很大提升</p>

<p>l  在Spark中引入了RDD (Resilient Distributed Dataset) 的抽象，它是分布在一组节点中的只读对象集合，这些集合是弹性的，如果数据集一部分丢失，则可以根据“血统”对它们进行重建，保证了数据的高容错性；</p>

<p>l  移动计算而非移动数据，RDD Partition可以就近读取分布式文件系统中的数据块到各个节点内存中进行计算</p>

<p>l  使用多线程池模型来减少task启动开稍</p>

<p>l  采用容错的、高可伸缩性的akka作为通讯框架</p>

<h2><a name="t9"></a> <a></a><a name="_Toc426403393"></a>2.2 SparkStreaming</h2>

<p>SparkStreaming是一个对实时数据流进行高通量、容错处理的流式处理系统，可以对多种数据源（如Kdfka、Flume、Twitter、Zero和TCP 套接字）进行类似Map、Reduce和Join等复杂操作，并将结果保存到外部文件系统、数据库或应用到实时仪表盘。</p>

<p><strong>Spark Streaming构架</strong></p>

<p>l计算流程：Spark Streaming是将流式计算分解成一系列短小的批处理作业。这里的批处理引擎是Spark Core，也就是把Spark Streaming的输入数据按照batch size（如1秒）分成一段一段的数据（Discretized Stream），每一段数据都转换成Spark中的RDD（Resilient Distributed Dataset），然后将Spark Streaming中对DStream的Transformation操作变为针对Spark中对RDD的Transformation操作，将RDD经过操作变成中间结果保存在内存中。整个流式计算根据业务的需求可以对中间的结果进行叠加或者存储到外部设备。下图显示了Spark Streaming的整个流程。</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20160709170937616?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"></p>

<p> </p>

<p> </p>

<p>l容错性：对于流式计算来说，容错性至关重要。首先我们要明确一下Spark中RDD的容错机制。每一个RDD都是一个不可变的分布式可重算的数据集，其记录着确定性的操作继承关系（lineage），所以只要输入数据是可容错的，那么任意一个RDD的分区（Partition）出错或不可用，都是可以利用原始输入数据通过转换操作而重新算出的。  </p>

<p>对于Spark Streaming来说，其RDD的传承关系如下图所示，图中的每一个椭圆形表示一个RDD，椭圆形中的每个圆形代表一个RDD中的一个Partition，图中的每一列的多个RDD表示一个DStream（图中有三个DStream），而每一行最后一个RDD则表示每一个Batch Size所产生的中间结果RDD。我们可以看到图中的每一个RDD都是通过lineage相连接的，由于Spark Streaming输入数据可以来自于磁盘，例如HDFS（多份拷贝）或是来自于网络的数据流（Spark Streaming会将网络输入数据的每一个数据流拷贝两份到其他的机器）都能保证容错性，所以RDD中任意的Partition出错，都可以并行地在其他机器上将缺失的Partition计算出来。这个容错恢复方式比连续计算模型（如Storm）的效率更高。</p>

<p> </p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20160709171027007?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"></p>

<p> </p>

<p> </p>

<p>l实时性：对于实时性的讨论，会牵涉到流式处理框架的应用场景。Spark Streaming将流式计算分解成多个Spark Job，对于每一段数据的处理都会经过Spark DAG图分解以及Spark的任务集的调度过程。对于目前版本的Spark Streaming而言，其最小的Batch Size的选取在0.5~2秒钟之间（Storm目前最小的延迟是100ms左右），所以Spark Streaming能够满足除对实时性要求非常高（如高频实时交易）之外的所有流式准实时计算场景。</p>

<p>l扩展性与吞吐量：Spark目前在EC2上已能够线性扩展到100个节点（每个节点4Core），可以以数秒的延迟处理6GB/s的数据量（60M records/s），其吞吐量也比流行的Storm高2～5倍，图4是Berkeley利用WordCount和Grep两个用例所做的测试，在Grep这个测试中，Spark Streaming中的每个节点的吞吐量是670k records/s，而Storm是115k records/s。</p>

<p> </p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20160709171059141?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"></p>

<p> </p>

<h2><a name="t10"></a> <a></a><a name="_Toc426403394"></a>2.3 Spark SQL</h2>

<p> </p>

<p>Shark是SparkSQL的前身，它发布于3年前，那个时候Hive可以说是SQL on Hadoop的唯一选择，负责将SQL编译成可扩展的MapReduce作业，鉴于Hive的性能以及与Spark的兼容，Shark项目由此而生。</p>

<p>Shark即Hive on Spark，本质上是通过Hive的HQL解析，把HQL翻译成Spark上的RDD操作，然后通过Hive的metadata获取数据库里的表信息，实际HDFS上的数据和文件，会由Shark获取并放到Spark上运算。Shark的最大特性就是快和与Hive的完全兼容，且可以在shell模式下使用rdd2sql()这样的API，把HQL得到的结果集，继续在scala环境下运算，支持自己编写简单的机器学习或简单分析处理函数，对HQL结果进一步分析计算。</p>

<p>在2014年7月1日的Spark Summit上，Databricks宣布终止对Shark的开发，将重点放到Spark SQL上。Databricks表示，Spark SQL将涵盖Shark的所有特性，用户可以从Shark 0.9进行无缝的升级。在会议上，Databricks表示，Shark更多是对Hive的改造，替换了Hive的物理执行引擎，因此会有一个很快的速度。然而，不容忽视的是，Shark继承了大量的Hive代码，因此给优化和维护带来了大量的麻烦。随着性能优化和先进分析整合的进一步加深，基于MapReduce设计的部分无疑成为了整个项目的瓶颈。因此，为了更好的发展，给用户提供一个更好的体验，Databricks宣布终止Shark项目，从而将更多的精力放到Spark SQL上。</p>

<p>Spark SQL允许开发人员直接处理RDD，同时也可查询例如在 Apache Hive上存在的外部数据。Spark SQL的一个重要特点是其能够统一处理关系表和RDD，使得开发人员可以轻松地使用SQL命令进行外部查询，同时进行更复杂的数据分析。除了Spark SQL外，Michael还谈到Catalyst优化框架，它允许Spark SQL自动修改查询方案，使SQL更有效地执行。</p>

<p>还有Shark的作者是来自中国的博士生辛湜（Reynold Xin），也是Spark的核心成员，具体信息可以看他的专访 http://www.csdn.net/article/2013-04-26/2815057-Spark-Reynold</p>

<p>Spark SQL的特点:</p>

<p>l引入了新的RDD类型SchemaRDD，可以象传统数据库定义表一样来定义SchemaRDD，SchemaRDD由定义了列数据类型的行对象构成。SchemaRDD可以从RDD转换过来，也可以从Parquet文件读入，也可以使用HiveQL从Hive中获取。</p>

<p>l内嵌了Catalyst查询优化框架，在把SQL解析成逻辑执行计划之后，利用Catalyst包里的一些类和接口，执行了一些简单的执行计划优化，最后变成RDD的计算</p>

<p>l在应用程序中可以混合使用不同来源的数据，如可以将来自HiveQL的数据和来自SQL的数据进行Join操作。</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20160709171141517?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"></p>

<p> </p>

<p> </p>

<p>Shark的出现使得SQL-on-Hadoop的性能比Hive有了10-100倍的提高，  那么，摆脱了Hive的限制，SparkSQL的性能又有怎么样的表现呢？虽然没有Shark相对于Hive那样瞩目地性能提升，但也表现得非常优异，如下图所示：</p>

<p> </p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20160709171208956?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"></p>

<p> </p>

<p>为什么sparkSQL的性能会得到怎么大的提升呢？主要sparkSQL在下面几点做了优化：</p>

<p>1. <strong>内存列存储（In-Memory Columnar Storage）</strong> sparkSQL的表数据在内存中存储不是采用原生态的JVM对象存储方式，而是采用内存列存储；</p>

<p>2. <strong>字节码生成技术（Bytecode Generation）</strong> Spark1.1.0在Catalyst模块的expressions增加了codegen模块，使用动态字节码生成技术，对匹配的表达式采用特定的代码动态编译。另外对SQL表达式都作了CG优化， CG优化的实现主要还是依靠Scala2.10的运行时放射机制（runtime reflection）；</p>

<p>3. <strong>Scala代码优化</strong> SparkSQL在使用Scala编写代码的时候，尽量避免低效的、容易GC的代码；尽管增加了编写代码的难度，但对于用户来说接口统一。</p>

<h2><a name="t11"></a> <a></a><a name="_Toc426403395"></a>2.4 BlinkDB</h2>

<p>BlinkDB 是一个用于在海量数据上运行交互式 SQL 查询的大规模并行查询引擎，它允许用户通过权衡数据精度来提升查询响应时间，其数据的精度被控制在允许的误差范围内。为了达到这个目标，BlinkDB 使用两个核心思想:</p>

<p>l一个自适应优化框架，从原始数据随着时间的推移建立并维护一组多维样本；</p>

<p>l一个动态样本选择策略，选择一个适当大小的示例基于查询的准确性和（或）响应时间需求。</p>

<p>和传统关系型数据库不同，BlinkDB是一个很有意思的交互式查询系统，就像一个跷跷板，用户需要在查询精度和查询时间上做一权衡；如果用户想更快地获取查询结果，那么将牺牲查询结果的精度；同样的，用户如果想获取更高精度的查询结果，就需要牺牲查询响应时间。用户可以在查询的时候定义一个失误边界。</p>

<p> </p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20160709171233253?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"></p>

<p> </p>

<h2><a name="t12"></a> <a></a><a name="_Toc426403396"></a>2.5  MLBase/MLlib</h2>

<p> </p>

<p>MLBase是Spark生态圈的一部分专注于机器学习，让机器学习的门槛更低，让一些可能并不了解机器学习的用户也能方便地使用MLbase。MLBase分为四部分：MLlib、MLI、ML Optimizer和MLRuntime。</p>

<p>l  ML Optimizer会选择它认为最适合的已经在内部实现好了的机器学习算法和相关参数，来处理用户输入的数据，并返回模型或别的帮助分析的结果；</p>

<p>l  MLI 是一个进行特征抽取和高级ML编程抽象的算法实现的API或平台；</p>

<p>l MLlib是Spark实现一些常见的机器学习算法和实用程序，包括分类、回归、聚类、协同过滤、降维以及底层优化，该算法可以进行可扩充； MLRuntime 基于Spark计算框架，将Spark的分布式计算应用到机器学习领域。</p>

<p> </p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20160709171256566?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"></p>

<p> </p>

<p>总的来说，MLBase的核心是他的优化器，把声明式的Task转化成复杂的学习计划，产出最优的模型和计算结果。与其他机器学习Weka和Mahout不同的是：</p>

<p>l  MLBase是分布式的，Weka是一个单机的系统；</p>

<p>l  MLBase是自动化的，Weka和Mahout都需要使用者具备机器学习技能，来选择自己想要的算法和参数来做处理；</p>

<p>l  MLBase提供了不同抽象程度的接口，让算法可以扩充</p>

<p>l  MLBase基于Spark这个平台</p>

<h2><a name="t13"></a> <a></a><a name="_Toc426403397"></a>2.6 GraphX</h2>

<p>GraphX是Spark中用于图(e.g., Web-Graphs and Social Networks)和图并行计算(e.g., PageRank and Collaborative Filtering)的API,可以认为是GraphLab(C++)和Pregel(C++)在Spark(Scala)上的重写及优化，跟其他分布式图计算框架相比，GraphX最大的贡献是，在Spark之上提供一栈式数据解决方案，可以方便且高效地完成图计算的一整套流水作业。GraphX最先是伯克利AMPLAB的一个分布式图计算框架项目，后来整合到Spark中成为一个核心组件。</p>

<p>GraphX的核心抽象是Resilient Distributed Property Graph，一种点和边都带属性的有向多重图。它扩展了Spark RDD的抽象，有Table和Graph两种视图，而只需要一份物理存储。两种视图都有自己独有的操作符，从而获得了灵活操作和执行效率。如同Spark，GraphX的代码非常简洁。GraphX的核心代码只有3千多行，而在此之上实现的Pregel模型，只要短短的20多行。GraphX的代码结构整体下图所示，其中大部分的实现，都是围绕Partition的优化进行的。这在某种程度上说明了点分割的存储和相应的计算优化的确是图计算框架的重点和难点。</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20160709171325493?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"></p>

<p> </p>

<p>GraphX的底层设计有以下几个关键点。</p>

<p>1.对Graph视图的所有操作，最终都会转换成其关联的Table视图的RDD操作来完成。这样对一个图的计算，最终在逻辑上，等价于一系列RDD的转换过程。因此，Graph最终具备了RDD的3个关键特性：Immutable、Distributed和Fault-Tolerant。其中最关键的是Immutable（不变性）。逻辑上，所有图的转换和操作都产生了一个新图；物理上，GraphX会有一定程度的不变顶点和边的复用优化，对用户透明。</p>

<p>2.两种视图底层共用的物理数据，由RDD[Vertex-Partition]和RDD[EdgePartition]这两个RDD组成。点和边实际都不是以表Collection[tuple]的形式存储的，而是由VertexPartition/EdgePartition在内部存储一个带索引结构的分片数据块，以加速不同视图下的遍历速度。不变的索引结构在RDD转换过程中是共用的，降低了计算和存储开销。</p>

<p>3.图的分布式存储采用点分割模式，而且使用partitionBy方法，由用户指定不同的划分策略（PartitionStrategy）。划分策略会将边分配到各个EdgePartition，顶点Master分配到各个VertexPartition，EdgePartition也会缓存本地边关联点的Ghost副本。划分策略的不同会影响到所需要缓存的Ghost副本数量，以及每个EdgePartition分配的边的均衡程度，需要根据图的结构特征选取最佳策略。目前有EdgePartition2d、EdgePartition1d、RandomVertexCut和CanonicalRandomVertexCut这四种策略。在淘宝大部分场景下，EdgePartition2d效果最好。</p>

<h2><a name="t14"></a> <a></a><a name="_Toc426403398"></a>2.7 SparkR</h2>

<p>SparkR是AMPLab发布的一个R开发包，使得R摆脱单机运行的命运，可以作为Spark的job运行在集群上，极大得扩展了R的数据处理能力。</p>

<p>SparkR的几个特性：</p>

<p>l  提供了Spark中弹性分布式数据集（RDD）的API，用户可以在集群上通过R shell交互性的运行Spark job。</p>

<p>l  支持序化闭包功能，可以将用户定义函数中所引用到的变量自动序化发送到集群中其他的机器上。</p>

<p>l  SparkR还可以很容易地调用R开发包，只需要在集群上执行操作前用includePackage读取R开发包就可以了，当然集群上要安装R开发包。</p>

<p> </p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20160709171353180?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"></p>

<p> </p>

<h2><a name="t15"></a><a></a><a name="_Toc426403399"></a>2.8  Tachyon</h2>

<p>Tachyon是一个高容错的分布式文件系统，允许文件以内存的速度在集群框架中进行可靠的共享，就像Spark和 MapReduce那样。通过利用信息继承，内存侵入，Tachyon获得了高性能。Tachyon工作集文件缓存在内存中，并且让不同的 Jobs/Queries以及框架都能内存的速度来访问缓存文件”。因此，Tachyon可以减少那些需要经常使用的数据集通过访问磁盘来获得的次数。Tachyon兼容Hadoop，现有的Spark和MR程序不需要任何修改而运行。</p>

<p>在2013年4月，AMPLab共享了其Tachyon 0.2.0 Alpha版本的Tachyon，其宣称性能为HDFS的300倍，继而受到了极大的关注。Tachyon的几个特性如下：</p>

<p>l<strong>JAVA-Like File API</strong></p>

<p>Tachyon提供类似JAVA File类的API,</p>

<p>l<strong>兼容性</strong></p>

<p>Tachyon实现了HDFS接口，所以Spark和MR程序不需要任何修改即可运行。</p>

<p>l<strong>可插拔的底层文件系统</strong></p>

<p>Tachyon是一个可插拔的底层文件系统，提供容错功能。tachyon将内存数据记录在底层文件系统。它有一个通用的接口，使得可以很容易的插入到不同的底层文件系统。目前支持HDFS，S3，GlusterFS和单节点的本地文件系统，以后将支持更多的文件系统。</p>            </div>
                </div>