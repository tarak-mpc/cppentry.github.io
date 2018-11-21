---
layout:     post
title:      Hadoop学习之路（二）Hadoop发展背景
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><strong>目录</strong></p>

<ul><li><a href="http://www.cnblogs.com/qingyunzong/p/8494803.html#_label0" rel="nofollow">Hadoop产生的背景</a></li>
	<li><a href="http://www.cnblogs.com/qingyunzong/p/8494803.html#_label1" rel="nofollow">Hadoop是啥</a></li>
	<li><a href="http://www.cnblogs.com/qingyunzong/p/8494803.html#_label2" rel="nofollow">HADOOP在大数据、云计算中的位置和关系</a></li>
	<li><a href="http://www.cnblogs.com/qingyunzong/p/8494803.html#_label3" rel="nofollow">Hadoop的技术应用</a></li>
	<li><a href="http://www.cnblogs.com/qingyunzong/p/8494803.html#_label4" rel="nofollow">HADOOP生态圈以及各组成部分的简介</a></li>
	<li><a href="http://www.cnblogs.com/qingyunzong/p/8494803.html#_label5" rel="nofollow">获取数据的三种方式</a></li>
	<li><a href="http://www.cnblogs.com/qingyunzong/p/8494803.html#_label6" rel="nofollow">国内HADOOP的就业情况分析</a>
	<ul><li><a href="http://www.cnblogs.com/qingyunzong/p/8494803.html#_label6_0" rel="nofollow">1、HADOOP就业整体情况</a></li>
		<li><a href="http://www.cnblogs.com/qingyunzong/p/8494803.html#_label6_1" rel="nofollow">2、 HADOOP就业职位要求</a></li>
	</ul></li>
</ul><p> </p>

<p><strong>正文</strong></p>

<p><a href="http://www.cnblogs.com/qingyunzong/p/8494803.html#_labelTop" rel="nofollow">回到顶部</a><a name="_label0"></a></p>

<h2>Hadoop产生的背景</h2>

<p>1. HADOOP<strong>最早起源于Nutch</strong>。Nutch的设计目标是构建一个大型的全网搜索引擎，包括网页抓取、索引、查询等功能，但随着抓取网页数量的增加，<strong>遇到了严重的可扩展性问题——</strong>如何解决数十亿网页的存储和索引问题。</p>

<p>2. 2003年开始<strong>谷歌陆续发表的三篇论文为该问题提供了可行的解决方案</strong>。</p>

<p>——分布式文件系统（GFS），可用于处理海量网页的<strong>存储</strong></p>

<p>——分布式计算框架MAPREDUCE，可用于处理海量网页的<strong>索引计算</strong>问题。</p>

<p>——BigTable 数据库：OLTP 联机事务处理 Online Transaction Processing 增删改<br>
　　　　　　　　　　OLAP 联机分析处理 Online Analysis Processing 查询<br>
　　　　　　　　　　真正的作用：提供了一种可以在超大数据集中进行实时CRUD操作的功能</p>

<p>3.Nutch的开发人员完成了相应的<strong>开源实现HDFS和MAPREDUCE</strong>，并从Nutch中剥离成为独立项目HADOOP，到2008年1月，HADOOP成为Apache顶级项目，迎来了它的快速发展期。</p>

<p><a href="http://www.cnblogs.com/qingyunzong/p/8494803.html#_labelTop" rel="nofollow">回到顶部</a><a name="_label1"></a></p>

<h2>Hadoop是啥</h2>

<p>Hadoop的官网：<a href="http://hadoop.apache.org/" rel="nofollow">http://hadoop.apache.org/</a></p>

<p>1、Hadoop是Apache旗下的一套开源软件平台</p>

<p>2、Hadoop提供的功能：利用服务器集群，根据户自定义业逻辑对海量数进行分布式处理</p>

<p>3、Hadoop的核心组件：</p>

<p>　　1）<strong>Hadoop Common</strong>：支持其他Hadoop模块的常用工具。</p>

<p>　　2)  <strong>Hadoop分布式文件系统（HDFS™）</strong>：一种分布式文件系统，可提供对应用程序数据的高吞吐量访问。</p>

<p>　　3)  <strong>Hadoop YARN</strong>：作业调度和集群资源管理的框架。</p>

<p><strong>　　4)  Hadoop MapReduce</strong>：一种用于并行处理大型数据集的基于YARN的系统。</p>

<p>　　大数据的处理主要就是<strong>存储</strong>和<strong>计算</strong>。</p>

<p>如果说安装hadoop集群，其实就是安装了两个东西： 一个操作系统YARN 和 一个文件系统HDFS。其实MapReduce就是运行在YARN之上的应用。</p>

<p>操作系统 　　文件系统 　　应用程序<br>
win7 　　　　NTFS　　　  QQ，WeChat<br>
YARN 　　　 HDFS 　　    MapReduce</p>

<p>4、hadoop的概念：</p>

<p>　　狭义上： 就是apache的一个顶级项目：apahce hadoop</p>

<p>　　广义上: 就是指以hadoop为核心的整个大数据处理体系</p>

<p>5、Apache的其他Hadoop相关项目包括：</p>

<ul><li><a href="http://incubator.apache.org/ambari/" rel="nofollow"><strong>Ambari™</strong></a>：一种用于供应，管理和监控Apache Hadoop集群的基于Web的工具，其中包括对Hadoop HDFS，Hadoop MapReduce，Hive，HCatalog，HBase，ZooKeeper，Oozie，Pig和Sqoop的支持。Ambari还提供了一个用于查看群集运行状况的仪表板，例如热图和可以直观地查看MapReduce，Pig和Hive应用程序的功能，以及以用户友好的方式诊断其性能特征的功能。</li>
	<li><a href="http://avro.apache.org/" rel="nofollow"><strong>Avro™</strong></a>：数据序列化系统。</li>
	<li><a href="http://cassandra.apache.org/" rel="nofollow"><strong>Cassandra™</strong></a>：无单点故障的可扩展多主数据库。</li>
	<li><a href="http://incubator.apache.org/chukwa/" rel="nofollow"><strong>Chukwa™</strong></a>：管理大型分布式系统的数据收集系统。</li>
	<li><a href="http://hbase.apache.org/" rel="nofollow"><strong>HBase™</strong></a>：可扩展的分布式数据库，支持大型表格的结构化数据存储。</li>
	<li><a href="http://hive.apache.org/" rel="nofollow"><strong>Hive™</strong></a>：提供数据汇总和即席查询的数据仓库基础架构。</li>
	<li><a href="http://mahout.apache.org/" rel="nofollow"><strong>Mahout™</strong></a>：可扩展的机器学习和数据挖掘库。</li>
	<li><a href="http://pig.apache.org/" rel="nofollow"><strong>Pig™</strong></a>：用于并行计算的高级数据流语言和执行框架。</li>
	<li><a href="http://spark.incubator.apache.org/" rel="nofollow"><strong>Spark™</strong></a>：用于Hadoop数据的快速和通用计算引擎。Spark提供了一个简单而富有表现力的编程模型，它支持广泛的应用程序，包括ETL，机器学习，流处理和图计算。</li>
	<li><a href="http://tez.incubator.apache.org/" rel="nofollow"><strong>Tez™</strong></a>：一种基于Hadoop YARN的通用数据流编程框架，它提供了一个强大且灵活的引擎，可执行任意DAG任务来处理批处理和交互式用例的数据。Hado™，Pig™和Hadoop生态系统中的其他框架以及其他商业软件（例如ETL工具）正在采用Tez来替代Hadoop™MapReduce作为底层执行引擎。</li>
	<li><a href="http://zookeeper.apache.org/" rel="nofollow"><strong>ZooKeeper™</strong></a>：分布式应用程序的高性能协调服务。</li>
</ul><p><a href="http://www.cnblogs.com/qingyunzong/p/8494803.html#_labelTop" rel="nofollow">回到顶部</a><a name="_label2"></a></p>

<h2><strong>HADOOP在大数据、云计算中的位置和关系</strong></h2>

<p>1、云计算是分布式计算、并行计算、网格计算、多核计算、网络存储、虚拟化、负载均衡等传统计算机技术和互联网技术融合发展的产物。借助IaaS(基础设施即服务)、PaaS(平台即服务)、SaaS（软件即服务）等业务模式，把强大的计算能力提供给终端用户。1、</p>

<p>2、现阶段，云计算的<strong>两大底层支撑技术</strong>为“<strong>虚拟化</strong>”和“<strong>大数据技术</strong>”</p>

<p>3、 而HADOOP则是云计算的PaaS层的解决方案之一，并不等同于PaaS，更不等同于云计算本身。</p>

<p><a href="http://www.cnblogs.com/qingyunzong/p/8494803.html#_labelTop" rel="nofollow">回到顶部</a><a name="_label3"></a></p>

<h2>Hadoop的技术应用</h2>

<p><strong>HADOOP应用于数据服务基础平台建设</strong></p>

<p><strong><img alt="" class="has" src="https://images2018.cnblogs.com/blog/1228818/201803/1228818-20180302190557551-1606375842.png"></strong></p>

<p><strong>HADOOP用于用户画像</strong></p>

<p><img alt="" class="has" src="https://images2018.cnblogs.com/blog/1228818/201803/1228818-20180302190637930-1990174101.png"></p>

<p> </p>

<p> 该图是中国电信的用户画像标签体系。</p>

<p><strong>HADOOP用于网站点击流日志</strong><strong>数据</strong><strong>挖掘</strong></p>

<p><img alt="" class="has" src="https://images2018.cnblogs.com/blog/1228818/201803/1228818-20180302190852771-1457100418.png"></p>

<p><strong>总结：hadoop并不会跟某个具体的行业或者某个具体的业务挂钩，它只是一种用来做海量数据分析处理的工具。</strong></p>

<p><a href="http://www.cnblogs.com/qingyunzong/p/8494803.html#_labelTop" rel="nofollow">回到顶部</a><a name="_label4"></a></p>

<h2><strong>HADOOP生态圈以及各组成部分的简介</strong></h2>

<p><strong><img alt="" class="has" src="https://images2018.cnblogs.com/blog/1228818/201803/1228818-20180302191054664-57367875.png"></strong></p>

<p> </p>

<p><img alt="" class="has" src="https://images2018.cnblogs.com/blog/1228818/201803/1228818-20180302191105833-1985207815.png"></p>

<p>重点组件：</p>

<p>HDFS：Hadoop的分布式文件存储系统。</p>

<p>MapReduce：Hadoop的分布式程序运算框架，也可以叫做一种编程模型。</p>

<p>Hive：基于Hadoop的类SQL数据仓库工具</p>

<p>Hbase：基于Hadoop的列式分布式NoSQL数据库</p>

<p>ZooKeeper：分布式协调服务组件</p>

<p>Mahout：基于MapReduce/Flink/Spark等分布式运算框架的机器学习算法库</p>

<p>Oozie/Azkaban：工作流调度引擎</p>

<p>Sqoop：数据迁入迁出工具</p>

<p>Flume：日志采集工具</p>

<p><a href="http://www.cnblogs.com/qingyunzong/p/8494803.html#_labelTop" rel="nofollow">回到顶部</a><a name="_label5"></a></p>

<h2>获取数据的三种方式</h2>

<p>1、自己公司收集的数据--日志     或者     数据库中的数据</p>

<p>2、有一些数据可以通过爬虫从网络中进行爬取</p>

<p>3、从第三方机构购买</p>

<p><a href="http://www.cnblogs.com/qingyunzong/p/8494803.html#_labelTop" rel="nofollow">回到顶部</a><a name="_label6"></a></p>

<h2><strong>国内HADOOP的就业情况分析</strong></h2>

<p><a name="_label6_0"></a></p>

<h3>1、HADOOP就业整体情况</h3>

<p>A. 大数据产业已纳入<strong>国家十三五规划</strong></p>

<p>B. 各大城市都在进行<strong>智慧城市项目</strong>建设，而智慧城市的根基就是大数据综合平台</p>

<p>C. 互联网时代数据的种类，增长都呈现<strong>爆发式增长</strong>，各行业对数据的价值日益重视</p>

<p>D. 相对于传统JAVAEE技术领域来说，大数据领域的<strong>人才相对稀缺</strong></p>

<p>E. 随着现代社会的发展，数据处理和数据挖掘的重要性只会增不会减，因此，大数据技术是一个尚在蓬勃发展且具有<strong>长远前景的领域</strong></p>

<p><a name="_label6_1"></a></p>

<h3>2、 HADOOP就业职位要求</h3>

<p>大数据是个复合专业，包括应用开发、软件平台、算法、数据挖掘等，因此，<strong>大数据技术领域的就业选择是多样的</strong>，但就HADOOP而言，通常都需要具备以下技能或知识：</p>

<p>硬实力</p>

<p>A. HADOOP分布式集群的平台搭建</p>

<p>B. HADOOP分布式文件系统HDFS的原理理解及使用</p>

<p>C. HADOOP分布式运算框架MAPREDUCE的原理理解及编程</p>

<p>D. Hive数据仓库工具的熟练应用</p>

<p>E. Flume、sqoop、oozie等辅助工具的熟练使用</p>

<p>F. Shell/python等脚本语言的开发能力</p>

<p>软实力</p>

<p>A.  解决问题的能力（调试，阅读文档）</p>

<p>B.  沟通协调能力（寻求帮助）</p>

<p>C.  学习提升自己的能力（自我提高）</p>

<p>D.   组织管控能力（管理能力）</p>

<p><br>
转自<a href="http://www.cnblogs.com/qingyunzong/p/8494803.html" rel="nofollow">http://www.cnblogs.com/qingyunzong/p/8494803.html</a></p>            </div>
                </div>