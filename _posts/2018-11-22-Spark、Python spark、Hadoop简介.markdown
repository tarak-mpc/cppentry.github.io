---
layout:     post
title:      Spark、Python spark、Hadoop简介
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="sparkpython-sparkhadoop简介">Spark、Python spark、Hadoop简介</h1>

<hr>



<h2 id="spark简介">Spark简介</h2>

<p><strong>1、Spark简介及功能模块</strong></p>

<p>Spark是一个弹性的分布式运算框架，作为一个用途广泛的大数据运算平台，Spark允许用户将数据加载到cluster集群的内存中储存，并多次重复运算，非常适合用于机器学习算法。</p>

<p>Spark的核心是RDD(Resilient Distributed Dataset)弹性分布式数据集，RDD能与其他系统兼容，也可以导入外部储存系统的数据集，例如HDFS、HBase或其他Hadoop数据源。</p>

<p>Spark在运算时，将中间产生的数据暂存在内存中，因此可以加快运行速度，Spark在内存中运行命令比Hapdoop MapReduce命令运行速度快100倍（硬盘上快10倍）。</p>

<p>Spark的主要功能模块有：Spark SQL DataFrame、Spark Streaming、Mllib(machine learning)、GraphX</p>

<ul>
<li><strong>Spark SQL DataFrame</strong>：Spark SQL 兼容SQL查询语言，DataFrame具有Schema(定义字段名与数据类型)，可使用类SQL方法，例如select()，使用上比RDD更方便</li>
<li><strong>Spark Streaming</strong>：可实现实时的数据串流的处理，具有大数据量、容错性、可扩充性等特点。</li>
<li><strong>GraphX</strong>：分布式图形处理，可用于图表计算</li>
<li><strong>Spark MLlib</strong>：可扩充性机器学习库。</li>
</ul>

<p><strong>2、Spark数据处理</strong> </p>

<p>Spark数据处理的方式主要有3种：RDD、DataFrame、Spark SQL</p>

<p>三者主要的差异在于是否定义Scheme</p>

<ul>
<li>RDD 的数据未定义Scheme（也就是未定义字段名及数据类型），使用上必须有Map/Reduce的概念，需要高级程序设计，同时功能最强，能完成所有Spark功能。</li>
<li>Spark DataFrame 建立时必须定义Schema（也就是定义字段名及数据类型）。</li>
<li>Spark SQL 是由DataFrame衍生出来的，必须先建立DataFrame，然后通过登录Spark SQL temp table，就可以使用Spark SQL语法了。</li>
</ul>

<p>易使用度：Spark SQL &gt; DataFrame &gt; RDD</p>

<p>DataFrame 与 Spark SQL 比RDD更快速</p>

<ul>
<li>DataFrame与Spark SQL通过Cataly进行最优化，可以大幅度提高执行效率。基于Python使用RDD速度慢于基于Scala使用RDD。Spark Python 使用DataFrame执行性能与Spark Python相当，且二者运行速度都比RDD快。</li>
</ul>

<p><strong>3、为什么使用Python结合Spark机器学习及大数据开发</strong></p>

<p>目前Spark支持Scala，Python，Java，R：</p>

<ul>
<li>Scala是Spark开发语言，与Spark兼容性最好，执行效率也最高，但数据分析使用Scala较少，学习门槛较Python高。</li>
<li>Java作为常用的程序设计语言，运用很广泛；但使用Java开发Spark相同功能，语句较长，数据分析师不熟悉Java。</li>
<li>R语言是数据分析很常用的语言，但目前Spark的R语言功能不完整，由于R专注于数据分析，没有其他程序设计模块（爬虫等）。</li>
<li>Python同样是数据分析很常用的语言，代码简洁易学、高生产力，同时也是面向对象、函数式的动态语言，并且有很多数据分析相关的强大的功能库和模块。</li>
</ul>

<p><strong>4、Python Spark机器学习</strong></p>

<p>Python目前有许多机器学习库以及基于Python的机器学习框架Scikit-learn、Tensorflow等，而面对海量大数据处理时，就必须结合分布式存储以及分布式计算。</p>

<p>Python结合Spark，可以使用HDFS分布式存储大量数据，还可以在集群（Spark stand alone、Hadoop YARN、Mesos）上执行分布式计算。</p>

<p>Spark机器学习主要有两个API：</p>

<p><strong>(1) Spark MLlib: RDD-based 机器学习 API</strong></p>

<ul>
<li>以RDD为基础的机器学习模块，优点是可以发挥in-memory与分布式运算，大幅度提升需要迭代的机器学习模块的执行效率，功能强大。</li>
</ul>

<p><strong>(2) Spark ML pipeline: Dataframes-based 机器学习 API</strong></p>

<ul>
<li>Dataframe：Spark 受Pandas程序包启发所设计的数据处理架构。</li>
<li>Spark ML Pipeline：Spark受Scikit-learn程序包启发设计的机器学习架构。</li>
<li>Spark Dataframe 与 Pandas  Dataframe 可以互相转换，带来极大方便：例如：数据读取-&gt;Spark  Dataframe -&gt; Spark ML Pipeline 机器学习-&gt;返回Spark  Dataframe -&gt; 转化为Pandas  Dataframe-&gt;使用Python其他模块（matplotlib）进行数据分析。</li>
<li>Spark  Dataframe 提供的API可以轻松读取大数据的各种数据源：Hadoop、Parquet、JSON等，还可以通过JDBC读取关系数据库，例MySQL等。 <br>
<img src="https://img-blog.csdn.net/2018090516381259" alt="这里写图片描述" title=""></li>
</ul>

<p><strong>5、Spark 2.0的介绍</strong></p>

<p>（1）提升执行性能</p>

<ul>
<li>Spark SQL 在Spark 2.0可以执行所有99 TPC-DS查询，能够执行SQL：2003标准的新功能，支持子查询。</li>
<li>SparkSession：新增SparkSession，同时具备了SQLContext与HiveContext功能。</li>
</ul>

<p>（2）Spark ML pipeline 机器学习程序包</p>

<ul>
<li>以DataFrame 为基础的机器学习程序包Spark ML pipeline 将成为主要的机器学习架构</li>
<li>Spark ML pipeline 程序包可以存储、加载训练完成模型。</li>
</ul>

<p>（3）Structured Streaming APIs</p>

<ul>
<li>Spark 2.0 的Structured Streaming APIs是新的结构化数据流处理方式，可以使用DataFrame/Dataset API 以Catalyst优化提升性能，并整合了streaming数据流处理、interactive互动查询与batch queries批次查询。</li>
</ul>

<p>（4）DataSet API、Accumulator API等</p>

<hr>

<h2 id="hadoop简介">Hadoop简介</h2>

<p><strong>1、 Hadoop及其特性</strong></p>

<p>Hadoop是存储与处理大数据的平台。具有可扩展性（Scalable）、经济性（Economical）、弹性（Flexible）及可靠性（Reliable）：</p>

<ul>
<li>可扩展性（Scalable）：Hadoop采用分布式计算与存储，当扩充容量或运算时，不需要更换整个系统，只需要增加新的数据节点服务器即可。</li>
<li>经济性（Economical）：Hadoop采用分布式计算与存储，不必使用昂贵高端的服务器，只需一般等级的服务器就可架构出高性能、高容量的集群。</li>
<li>弹性（Flexible）：传统关系型数据库存储数据时必须有数据表结构schema（各个数据对象的集合），Hadoop存储的数据是非结构化（schema-less）的，可以存储各种形式、不同数据源的数据。</li>
<li>可靠性（Reliable）：Hadoop采用分布式架构，当某台服务器（甚至整个机架）坏掉，HDFS仍可正常运行，数据有两个另外的副本。</li>
</ul>

<p>Hadoop采用分布式文件系统（Hadoop Distributed File System），可以由单台服务器扩充到数千台服务器。</p>

<p><strong>2、HDFS分布式文件系统</strong></p>

<p>HDFS分布式文件系统中，NameNode服务器负责管理与维护HDFS目录系统并控制文件的读写操作。多个DataNode服务器负责存储数据。</p>

<p><strong>HDFS设计的前提与目标</strong>：</p>

<p>（1）硬件故障是常态而不是异常（Hardware Failure）</p>

<ul>
<li>HDFS是设计运行在低成本的普通服务器上，硬件故障是常态，而不是异常，所以HDFS被设计成具有高度容错能力、能够实时检测错误并且自动恢复，这是HDFS核心的设计目标。</li>
</ul>

<p>（2）Streaming流式数据存取（Streaming Data Access）</p>

<ul>
<li>运行在HDFS上的应用程序会通过Streaming存取数据集。HDFS的主要设计是批处理，而不是实时互动处理，优点是可以提高存取大量数据的能力，但是牺牲了响应时间。</li>
</ul>

<p>（3）大数据集（Large Data Sets）</p>

<ul>
<li>为了存储大数据集，HDFS提供了cluster集群架构，用于存储大数据文件，集群可扩充数百个节点。</li>
</ul>

<p>（4）简单一致性模型（Simple Coherency Model）</p>

<ul>
<li>HDFS 的存取模式是一次写入多次读取，一个文件被创建后就不会再修改。该设计优点是提高存储大量数据的能力，并简化数据一致性问题。</li>
</ul>

<p>（5）移动“计算”比移动“数据”成本更低（Moving Computation is Cheaper than Moving Data）</p>

<ul>
<li>搬移数据耗费大量时间成本，因此需要计算数据时，将计算功能在接近数据服务器中运行而不是搬移数据。</li>
</ul>

<p>（6）跨硬件与软件平台</p>

<ul>
<li>HDFS在设计时就考虑到平台的可移植性。</li>
</ul>

<p><strong>HDFS文件存储架构</strong>： <br>
<img src="https://img-blog.csdn.net/20180905172503655" alt="这里写图片描述" title=""></p>

<p><strong>文件分割</strong>：</p>

<p>当用户以HDFS命令要求存储文件时，系统会将文件切割为多个区块（Block），每个区块为64MB，上图文件被分割为A、B、C共3个区块。</p>

<p>区块副本策略：</p>

<ul>
<li>一个文件区块默认会复制成3份，也可以设置文件区块创建几个副本。</li>
<li>文件区块损坏时，NameNode会自动寻找位于其他DataNode上的副本来恢复数据，维持3份的副本策略。</li>
</ul>

<p>机架感知：</p>

<ul>
<li>上图，有Rack1，Rack2，Rack3共3个机架，每个机架都有4台DataNode服务器。</li>
<li>HDFS具有机架感知功能，以Block C为例：第一份副本放在Rack1机架的节点，第二份放在同机架Rack1的不同节点，最后一份放在不同机架Rack3的不同节点</li>
<li>这样设计的好处是防止数据遗失，有任何机架出现故障时，仍可以保证恢复数据，提高网络性能。</li>
</ul>

<p><strong>3、Hadoop Map Reduce的介绍</strong></p>

<p>利用大数据进行数据分析处理时，数据量庞大，所需运算量也巨大。Hadoop MapReduce 的做法是采用分布式计算的技术：</p>

<ul>
<li>Map将任务分割成更小任务，由每台服务器分别运行。</li>
<li>Reduce将所有服务器运算结果汇总整理，返回最后的结果。</li>
</ul>

<p>通过MapReduce方式，可以在上千台机器上并行处理巨量的数据，大大减少数据处理的时间。</p>

<p>（1）MapReduce 版本2.0 YARN</p>

<ul>
<li>Hadoop的MapReduce架构称为YARN（Yet Another Resource Negotiator，另一种资源协调者）是效率更高的资源管理核心。<a href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html" rel="nofollow">YARN架构图</a> <br>
<img src="https://img-blog.csdn.net/201809051759058" alt="这里写图片描述" title=""></li>
<li>上图中，在Client客户端，用户向Resource Manager请求执行运算（或执行任务）。</li>
<li>在NameNode会有Resource Manager统筹管理运算的请求。</li>
<li>在其他DataNode 会有Node Manageer负责运行，以及监督每一个任务（task），并且向Resource Manager汇报状态。</li>
</ul>

<p>（2）Hadoop MapReduce的计算框架</p>

<ul>
<li>Hadoop MapReduce 在运算时需要将中间产生的数据储存在硬盘中，由于磁盘I/O性能问题，因此会有读写数据延迟的问题。</li>
<li>而Spark是基于内存的计算框架，不会有磁盘I/O读写数据延迟的问题，可以大幅度提升性能。</li>
</ul>

<hr>

<h2 id="参考资料">参考资料</h2>

<blockquote>
  <p>林大贵：Python+Spark+Hadoop机器学习与大数据实战</p>
</blockquote>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>