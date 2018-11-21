---
layout:     post
title:      Apache Spark大数据分析入门（一）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
Apache Spark的出现让普通人也具备了大数据及实时数据分析能力。鉴于此，本文通过动手实战操作演示带领大家快速地入门学习Spark。本文是Apache Spark入门系列教程（共四部分）的第一部分。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
全文共包括四个部分：</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
</p>
<ul style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;"><li style="list-style:disc;"><span>第一部分：Spark入门，介绍如何使用Shell及RDDs</span></li><li style="list-style:disc;"><span>第二部分：介绍Spark SQL、Dataframes及如何结合Spark与Cassandra一起使用</span></li><li style="list-style:disc;"><span>第三部分：介绍Spark MLlib和Spark Streaming</span></li><li style="list-style:disc;"><span>第四部分：介绍Spark Graphx图计算</span></li></ul><p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
本篇讲解的便是第一部分</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
关于全部摘要和提纲部分，请登录我们的网站 <a href="http://www.mammatustech.com/apache-spark-course-quick-start-real-time-data-analytics" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Apache Spark QuickStart for real-time data-analytics</a>进行访问。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
在网站上你可以找到更多这方面的文章和教程，例如： <a href="http://www.mammatustech.com/introduction-to-apache-spark" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Java Reactive Microservice Training</a>，<a href="http://www.mammatustech.com/consul-service-discovery-and-health-for-microservices-architecture-tutorial" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Microservices Architecture | Consul Service Discovery and Health For Microservices Architecture Tutorial</a>。还有更多的其它内容，感兴趣的可以去查看。</p>
<h3 style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;line-height:24px;">
<strong>Spark 概述</strong></h3>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
Apache Spark是一个正在快速成长的开源集群计算系统，正在快速的成长。Apache Spark生态系统中的包和框架日益丰富，使得Spark能够进行高级数据分析。Apache Spark的快速成功得益于它的强大功能和易于使用性。相比于传统的MapReduce大数据分析，Spark效率更高、运行时速度更快。Apache Spark 提供了内存中的分布式计算能力，具有Java、 Scala、Python、R四种编程语言的API编程接口。Spark生态系统如下图所示：</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;text-align:center;">
<img src="http://img.ptcms.csdn.net/article/201511/25/5655a8619e7c9.jpg" alt="" style="vertical-align:middle;border:none;"> <br></p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
<a href="http://2.bp.blogspot.com/-bJJ6nHF4r1o/VeFRpor-bfI/AAAAAAAAAUQ/WiFPHP4_pBA/s1600/Screen%2BShot%2B2015-08-28%2Bat%2B11.30.22%2BPM.png" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Display</a> -  <a href="https://docs.google.com/document/d/1rg5yaH1OHaEbJZxFAiAW7GXXek8F6KJ51pzr7U7ec1Y/edit?usp=sharing" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Edit</a></p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
整个生态系统构建在Spark内核引擎之上，内核使得Spark具备快速的内存计算能力，也使得其API支持Java、Scala,、Python、R四种编程语言。Streaming具备实时流数据的处理能力。Spark SQL使得用户使用他们最擅长的语言查询结构化数据，DataFrame位于Spark SQL的核心，DataFrame将数据保存为行的集合，对应行中的各列都被命名，通过使用DataFrame，可以非常方便地查询、绘制和过滤数据。MLlib为Spark中的机器学习框架。Graphx为图计算框架，提供结构化数据的图计算能力。以上便是整个生态系统的概况。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
<a href="http://www.mammatustech.com/java-reactive-microservice-training" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;"></a></p>
<h3 style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;line-height:24px;">
Apache Spark的发展历史</h3>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
</p>
<ul style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;"><li style="list-style:disc;"><span>最初由加州伯克利大学（UC Berkeley） AMP lab实验室开发并于2010年开源，目前已经成为阿帕奇软件基金会（Apache Software Foundation）的顶级项目。</span></li><li style="list-style:disc;"><span>已经有12,500次代码提交，这些提交来自630个源码贡献者（参见</span> <a href="https://github.com/apache/spark" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;"> Apache Spark Github repo</a><span>）</span></li><li style="list-style:disc;"><span>大部分代码使用</span> <a href="http://www.scala-lang.org/" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Scala</a><span>语言编写。</span></li><li style="list-style:disc;"><span>Apache Spark的Google兴趣搜索量（</span> <a href="http://www.csdn.net/article/2015-11-25/2826324#q=apache%20spark" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Google search interests</a><span>）最近呈井喷式的增长，这表明其关注度之高（Google广告词工具显示：仅七月就有多达108,000次搜索，比Microservices的搜索量多十倍）</span></li></ul><p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;text-align:center;">
<img src="http://img.ptcms.csdn.net/article/201511/25/5655a8cdca960.jpg" alt="" style="vertical-align:middle;border:none;"> <br></p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
</p>
<ul style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;"><li style="list-style:disc;"><span>部分Spark的源码贡献者（distributors）分别来自IBM、Oracle、DataStax、BlueData、Cloudera……</span></li><li style="list-style:disc;"><span>构建在Spark上的应用包括：Qlik、Talen、Tresata、atscale、platfora……</span></li><li style="list-style:disc;"><span>使用Spark的公司有：</span> <a href="http://www.meetup.com/Los-Angeles-Apache-Spark-Users-Group/events/207973922/" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Verizon</a> <a href="http://www.meetup.com/Los-Angeles-Apache-Spark-Users-Group/events/207973922/" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Verizon</a><span>、</span> <a href="https://databricks.com/blog/2014/09/24/apache-spark-improves-the-economics-of-video-distribution-at-nbc-universal.html" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">NBC</a><span>、Yahoo、</span> <a href="https://spark-summit.org/2014/talk/music-recommendations-at-scale-with-spark" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Spotify</a>……</li></ul><p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
大家对Apache Spark如此感兴趣的原因是它使得普通的开发具备Hadoop的数据处理能力。较之于Hadoop，Spark的集群配置比Hadoop集群的配置更简单，运行速度更快且更容易编程。Spark使得大多数的开发人员具备了大数据和实时数据分析能力。鉴于此，鉴于此，本文通过动手实战操作演示带领大家快速地入门学习Apache Spark。</p>
<h3 style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;line-height:24px;">
下载Spark并河演示如何使用交互式Shell命令行</h3>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
动手实验Apache Spark的最好方式是使用交互式Shell命令行，Spark目前有Python Shell和Scala Shell两种交互式命令行。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
可以从 <a href="http://spark.apache.org/downloads.html" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">这里</a>下载Apache Spark，下载时选择最近预编译好的版本以便能够立即运行shell。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
目前最新的Apache Spark版本是1.5.0，发布时间是2015年9月9日。</p>
<pre style="list-style:none;overflow:hidden;border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;line-height:24px;background:rgb(247,247,247);">tar -xvzf ~/spark-1.5.0-bin-hadoop2.4.tgz</pre>
<h4 style="list-style:none;color:rgb(51,51,51);font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;">
运行Python Shell</h4>
<pre style="list-style:none;overflow:hidden;border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;line-height:24px;background:rgb(247,247,247);">cd spark-1.5.0-bin-hadoop2.4
./bin/pyspark</pre>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
在本节中不会使用Python Shell进行演示。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
Scala交互式命令行由于运行在JVM上，能够使用java库。</p>
<h4 style="list-style:none;color:rgb(51,51,51);font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;">
运行Scala Shell</h4>
<pre style="list-style:none;overflow:hidden;border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;line-height:24px;background:rgb(247,247,247);">cd spark-1.5.0-bin-hadoop2.4
./bin/spark-shell</pre>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
执行完上述命令行，你可以看到下列输出：</p>
<h4 style="list-style:none;color:rgb(51,51,51);font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;">
Scala Shell欢迎信息</h4>
<pre style="list-style:none;overflow:hidden;border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;line-height:24px;background:rgb(247,247,247);">Welcome to
      ____              __
     / __/__  ___ _____/ /__
    _\ \/ _ \/ _ `/ __/  '_/
   /___/ .__/\_,_/_/ /_/\_\   version 1.5.0
      /_/
Using Scala version 2.10.4 (Java HotSpot(TM) 64-Bit Server VM, Java 1.8.0_25)
Type in expressions to have them evaluated.
Type :help for more information.
15/08/24 21:58:29 INFO SparkContext: Running Spark version 1.5.0</pre>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
下面是一些简单的练习以便帮助使用shell。也许你现在不能理解我们做的是什么，但在后面我们会对此进行详细分析。在Scala Shell中，执行下列操作：</p>
<h4 style="list-style:none;color:rgb(51,51,51);font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;">
在Spark中使用README 文件创建textFileRDD</h4>
<pre style="list-style:none;overflow:hidden;border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;line-height:24px;background:rgb(247,247,247);">val textFile = sc.textFile("README.md")</pre>
<h4 style="list-style:none;color:rgb(51,51,51);font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;">
获取textFile  RDD的第一个元素</h4>
<pre style="list-style:none;overflow:hidden;border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;line-height:24px;background:rgb(247,247,247);">textFile.first()
res3: String = # Apache Spark</pre>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
对textFile RDD中的数据进行过滤操作，返回所有包含“Spark”关键字的行，操作完成后会返回一个新的RDD，操作完成后可以对返回的RDD的行进行计数</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
<strong>筛选出包括Spark关键字的RDD然后进行行计数</strong></p>
<pre style="list-style:none;overflow:hidden;border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;line-height:24px;background:rgb(247,247,247);">val linesWithSpark = textFile.filter(line =&gt; line.contains("Spark"))
linesWithSpark.count()
res10: Long = 19</pre>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
要找出RDD linesWithSpark单词出现最多的行，可以使用下列操作。使用map方法，将RDD中的各行映射成一个数，然后再使用reduce方法找出包含单词数最多的行。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
<strong>找出RDD textFile 中包含单词数最多的行</strong></p>
<pre style="list-style:none;overflow:hidden;border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;line-height:24px;background:rgb(247,247,247);">textFile.map(line =&gt; line.split(" ").size)
.reduce((a, b) =&gt; if (a &gt; b) a else b)
res11: Int = 14</pre>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
返回结果表明第14行单词数最多。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
也可以引入其它java包，例如 Math.max()方法，因为map和reduce方法接受scala函数字面量作为参数。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
<strong>在scala shell中引入Java方法</strong></p>
<pre style="list-style:none;overflow:hidden;border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;line-height:24px;background:rgb(247,247,247);">import java.lang.Math
textFile.map(line =&gt; line.split(" ").size)
.reduce((a, b) =&gt; Math.max(a, b))
res12: Int = 14</pre>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
我们可以很容易地将数据缓存到内存当中。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
<strong>将RDD linesWithSpark 缓存，然后进行行计数</strong></p>
<pre style="list-style:none;overflow:hidden;border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;line-height:24px;background:rgb(247,247,247);">linesWithSpark.cache()
res13: linesWithSpark.type = 
MapPartitionsRDD[8] at filter at &lt;console&gt;:23
linesWithSpark.count()
res15: Long = 19</pre>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
上面简要地给大家演示的了如何使用Spark交互式命令行。</p>
<h3 style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;line-height:24px;">
弹性分布式数据集（RDDs）</h3>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
Spark在集群中可以并行地执行任务，并行度由Spark中的主要组件之一——RDD决定。弹性分布式数据集(Resilient distributed data, RDD)是一种数据表示方式，RDD中的数据被分区存储在集群中（碎片化的数据存储方式），正是由于数据的分区存储使得任务可以并行执行。分区数量越多，并行越高。下图给出了RDD的表示：</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;text-align:center;">
<img src="http://img.ptcms.csdn.net/article/201511/25/5655b0ea512a8.jpg" alt="" style="vertical-align:middle;border:none;"> <br></p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;text-align:center;">
<a href="http://3.bp.blogspot.com/-t_IupTFEy90/VdvqznUkOfI/AAAAAAAAAQ4/7-TNDWH3Xp4/s1600/Screen%2BShot%2B2015-08-24%2Bat%2B9.09.13%2BPM.png" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Display</a>-  <a href="https://docs.google.com/document/d/1rg5yaH1OHaEbJZxFAiAW7GXXek8F6KJ51pzr7U7ec1Y/edit?usp=sharing" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Edit</a></p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
想像每列均为一个分区（partition ），你可以非常方便地将分区数据分配给集群中的各个节点。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
为创建RDD，可以从外部存储中读取数据，例如从Cassandra、Amazon简单存储服务（Amazon Simple Storage Service）、HDFS或其它Hadoop支持的输入数据格式中读取。也可以通过读取文件、数组或JSON格式的数据来创建RDD。另一方面，如果对于应用来说，数据是本地化的，此时你仅需要使用parallelize方法便可以将Spark的特性作用于相应数据，并通过Apache Spark集群对数据进行并行化分析。为验证这一点，我们使用Scala Spark Shell进行演示：</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
<strong>通过单词列表集合创建RDD thingsRDD </strong></p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
</p>
<pre style="list-style:none;overflow:hidden;border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;line-height:24px;background:rgb(247,247,247);">val thingsRDD = sc.parallelize(List("spoon", "fork", "plate", "cup", "bottle"))
thingsRDD: org.apache.spark.rdd.RDD[String] = 
ParallelCollectionRDD[11] at parallelize at &lt;console&gt;:24</pre><span style="color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">计算RDD thingsRDD 中单的个数</span><span style="color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;"></span>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
</p>
<pre style="list-style:none;overflow:hidden;border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;line-height:24px;background:rgb(247,247,247);">thingsRDD.count()
res16: Long = 5</pre><span style="color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">运行Spark时，需要创建Spark Context。使用Spark Shell交互式命令行时，Spark Context会自动创建。当调用Spark Context 对象的parallelize 方法后，我们会得到一个经过分区的RDD，这些数据将被分发到集群的各个节点上。 </span><br style="color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;"><p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
</p>
<h3 style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;line-height:24px;">
<strong>使用RDD我们能够做什么？</strong></h3>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
对RDD，既可以进行数据转换，也可以对进行action操作。这意味着使用transformation可以改变数据格式、进行数据查询或数据过滤操作等，使用action操作，可以触发数据的改变、抽取数据、收集数据甚至进行计数。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
例如，我们可以使用Spark中的文本文件README.md创建一个RDD textFile，文件中包含了若干文本行，将该文本文件读入RDD textFile时，其中的文本行数据将被分区以便能够分发到集群中并被并行化操作。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
<strong>根据README.md文件创建RDD textFile</strong></p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
</p>
<pre style="list-style:none;overflow:hidden;border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;line-height:24px;background:rgb(247,247,247);">val textFile = sc.textFile("README.md")</pre><span style="color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">行计数</span><span style="color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;"></span>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
</p>
<pre style="list-style:none;overflow:hidden;border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;line-height:24px;background:rgb(247,247,247);">textFile.count()
res17: Long = 98</pre>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
README.md 文件中有98行数据。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
得到的结果如下图所示：</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;text-align:center;">
<img src="http://img.ptcms.csdn.net/article/201511/25/5655b111f09fa.jpg" alt="" style="vertical-align:middle;border:none;"> <br></p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;text-align:center;">
<a href="http://1.bp.blogspot.com/-pEGRBiyUXug/VdvqzqOz4cI/AAAAAAAAARA/lPAijbSTp-E/s640/Screen%2BShot%2B2015-08-24%2Bat%2B9.09.24%2BPM.png" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Display</a>-  <a href="https://docs.google.com/document/d/1rg5yaH1OHaEbJZxFAiAW7GXXek8F6KJ51pzr7U7ec1Y/edit?usp=sharing" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Edit</a></p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
然后，我们可以将所有包含Spark关键字的行筛选出来，完成操作后会生成一个新的RDDlinesWithSpark：</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
<strong>创建一个过滤后的RDD linesWithSpark</strong></p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
</p>
<pre style="list-style:none;overflow:hidden;border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;line-height:24px;background:rgb(247,247,247);">val linesWithSpark = textFile.filter(line =&gt; line.contains("Spark"))</pre>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
在前一幅图中，我们给出了 textFile RDD的表示，下面的图为RDD linesWithSpark的表示：</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;text-align:center;">
<img src="http://img.ptcms.csdn.net/article/201511/25/5655b14b5a371.jpg" alt="" style="vertical-align:middle;border:none;"> <br></p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;text-align:center;">
<a href="http://4.bp.blogspot.com/-QhoC7esU6_0/Vdvqzl4qS_I/AAAAAAAAAQ8/PDQhkFF5rpE/s640/Screen%2BShot%2B2015-08-24%2Bat%2B9.09.34%2BPM.png" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Display</a>- <a href="https://docs.google.com/document/d/1rg5yaH1OHaEbJZxFAiAW7GXXek8F6KJ51pzr7U7ec1Y/edit?usp=sharing" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Edit</a></p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
值得注意的是，Spark还存在键值对RDD（Pair RDD），这种RDD的数据格式为键/值对数据（key/value paired data）。例如下表中的数据，它表示水果与颜色的对应关系：</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;text-align:center;">
<img src="http://img.ptcms.csdn.net/article/201511/25/5655b20626790.jpg" alt="" style="vertical-align:middle;border:none;"> <br></p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;text-align:center;">
<a href="http://4.bp.blogspot.com/-nvVTePFraZ8/Vd1dwJ5KJWI/AAAAAAAAAR4/RjkhSWLuekQ/s400/Screen%2BShot%2B2015-08-25%2Bat%2B11.33.10%2BPM.png" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Display</a>- <a href="https://docs.google.com/document/d/1rg5yaH1OHaEbJZxFAiAW7GXXek8F6KJ51pzr7U7ec1Y/edit?usp=sharing" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Edit</a></p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
对表中的数据使用groupByKey()转换操作将得到下列结果：</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
<strong>groupByKey() 转换操作</strong></p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
</p>
<pre style="list-style:none;overflow:hidden;border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;line-height:24px;background:rgb(247,247,247);">pairRDD.groupByKey()
Banana [Yellow]
Apple [Red, Green]      
Kiwi [Green]
Figs [Black]</pre>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
该转换操作只将键为Apple，值为Red和Green的数据进行了分组。这些是到目前为止给出的转换操作例子。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
当得到一个经过过滤操作后的RDD，可以collect/materialize相应的数据并使其流向应用程序，这是action操作的例子。经过此操作后， RDD中所有数据将消失，但我们仍然可以在RDD的数据上进行某些操作，因为它们仍然在内存当中。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
<strong>Collect 或 materializelinesWithSpark RDD中的数据</strong></p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
</p>
<pre style="list-style:none;overflow:hidden;border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;line-height:24px;background:rgb(247,247,247);">linesWithSpark.collect()</pre>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
值得一提的是每次进行Spark action操作时，例如count() action操作，Spark将重新启动所有的转换操作，计算将运行到最后一个转换操作，然后count操作返回计算结果，这种运行方式速度会较慢。为解决该问题和提高程序运行速度，可以将RDD的数据缓存到内存当中，这种方式的话，当你反复运行action操作时，能够避免每次计算都从头开始，直接从缓存到内存中的RDD得到相应的结果。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
<strong>缓存RDDlinesWithSpark</strong></p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
</p>
<pre style="list-style:none;overflow:hidden;border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;line-height:24px;background:rgb(247,247,247);">linesWithSpark.cache()</pre>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
如果你想将RDD linesWithSpark从缓存中清除，可以使用unpersist()方法。 <br></p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
</p>
<h4 style="list-style:none;color:rgb(51,51,51);font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;">
将linesWithSpark从内存中删除</h4>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
</p>
<pre style="list-style:none;overflow:hidden;border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;line-height:24px;background:rgb(247,247,247);">linesWithSpark.unpersist()</pre>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
如果不手动删除的话，在内存空间紧张的情况下，Spark会采用最近最久未使用（least recently used logic，LRU)调度算法删除缓存在内存中最久的RDD。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
下面总结一下Spark从开始到结果的运行过程：</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
</p>
<ul style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;"><li style="list-style:disc;"><span>创建某种数据类型的RDD</span></li><li style="list-style:disc;"><span>对RDD中的数据进行转换操作，例如过滤操作</span></li><li style="list-style:disc;"><span>在需要重用的情况下，对转换后或过滤后的RDD进行缓存</span></li><li style="list-style:disc;"><span>在RDD上进行action操作，例如提取数据、计数、存储数据到Cassandra等。</span></li></ul><p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
下面给出的是RDD的部分转换操作清单：</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
</p>
<ul style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;"><li style="list-style:disc;"><span>filter()</span></li><li style="list-style:disc;"><span>map()</span></li><li style="list-style:disc;"><span>sample()</span></li><li style="list-style:disc;"><span>union()</span></li><li style="list-style:disc;"><span>groupbykey()</span></li><li style="list-style:disc;"><span>sortbykey()</span></li><li style="list-style:disc;"><span>combineByKey()</span></li><li style="list-style:disc;"><span>subtractByKey()</span></li><li style="list-style:disc;"><span>mapValues()</span></li><li style="list-style:disc;"><span>Keys()</span></li><li style="list-style:disc;"><span>Values()</span></li></ul><p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
下面给出的是RDD的部分action操作清单：</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
</p>
<ul style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;"><li style="list-style:disc;"><span>collect()</span></li><li style="list-style:disc;"><span>count()</span></li><li style="list-style:disc;"><span>first()</span></li><li style="list-style:disc;"><span>countbykey()</span></li><li style="list-style:disc;"><span>saveAsTextFile()</span></li><li style="list-style:disc;"><span>reduce()</span></li><li style="list-style:disc;"><span>take(n)</span></li><li style="list-style:disc;"><span>countBykey()</span></li><li style="list-style:disc;"><span>collectAsMap()</span></li><li style="list-style:disc;"><span>lookup(key)</span></li></ul><p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
关于RDD所有的操作清单和描述，可以参考 <a href="http://spark.apache.org/docs/latest/programming-guide.html" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Spark documentation</a></p>
<h3 style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;line-height:24px;">
结束语</h3>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
本文介绍了Apache Spark，一个正在快速成长、开源的集群计算系统。我们给大家展示了部分能够进行高级数据分析的Apache Spark库和框架。对 Apache Spark为什么会如此成功的原因进行了简要分析，具体表现为 Apache Spark的强大功能和易用性。给大家演示了 Apache Spark提供的内存、分布式计算环境，并演示了其易用性及易掌握性。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
在本系列教程的第二部分，我们对Spark进行更深入的介绍。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
<strong>相关链接：</strong></p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
</p>
<ul style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;"><li style="list-style:disc;"><a href="http://www.mammatustech.com/reactive-microservices" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Reactive Microservices</a></li><li style="list-style:disc;"><a href="http://www.mammatustech.com/high-speed-microservices" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">High-speed Microservices</a></li><li style="list-style:disc;"><a href="https://github.com/advantageous/qbit" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Real-time, fast-lane data analytics for Microservices metrics</a></li></ul><p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
<strong>原文地址：</strong> <a href="https://dzone.com/articles/introduction-to-bigdata-analytics-with-apache-spar-6" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Introduction to Big Data Analytics w/ Apache Spark Pt. 1</a>（译者/牛亚真
 审校/朱正贵 责编/仲浩）</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
<strong>译者简介：</strong>牛亚真，本科，2010年毕业于西南大学计算机与信息科学学院信息管理与信息系统专业；研究生，2013年毕业于中国科学院大学文献情报中心情报学专业，计算机信息处理与检索方向。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
转载：http://www.csdn.net/article/2015-11-25/2826324</p>
            </div>
                </div>