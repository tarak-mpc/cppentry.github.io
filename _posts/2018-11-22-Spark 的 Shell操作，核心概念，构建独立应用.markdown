---
layout:     post
title:      Spark 的 Shell操作，核心概念，构建独立应用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：【http://thinkgamer.cn】					https://blog.csdn.net/Gamer_gyt/article/details/51699576				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-family:'Microsoft YaHei';font-size:14px;">1：Spark中的Python和Scala的shell</span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;">2：Spark核心概念简介<br></span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;">3：独立应用</span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;">4：Spark数据集<br></span></p>
<h1><span style="font-family:'Microsoft YaHei';font-size:14px;"><span style="font-size:24px;">一：Spark中的Python 和Scala  的shell</span><br></span></h1>
<h2><span style="font-family:'Microsoft YaHei';font-size:14px;">       <span style="font-size:18px;">
1：shell设置显示日志</span><br></span></h2>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;">       进入Spark的安装目录，启动spark的集群，输入bin/pyspark，但此时会伴有大量的日志信息，在这里想要缩减启动信息的显示，可以调整日志的级别来控制输出的信息量，在conf目目录下创建log4j.properties的文件来管理系统日志设置，Spark开发者默认在conf下已经加入了日志设置的模板，为 log4l,proper.template,赋值一份修改为 log4j.properties找到</span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;">       log4j.rootCategory=INFO,console</span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;">       然后通过下面的设定降低日志的优先级，只显示警告和更严重的信息</span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;">      log4j.rootCategory=WARN,console</span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;"><br></span></p>
<h2><span style="font-family:'Microsoft YaHei';font-size:14px;">      <span style="font-size:18px;">
2：利用python-shell进行行数统计</span></span></h2>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;"></span></p>
<pre><code class="language-python">&gt;&gt;&gt; lines = sc.textFile("file:///usr/local/hadoop/spark/README.md")             #创建一个lines的RDD
&gt;&gt;&gt; lines.count()                                                               #统计RDD中元素的个数
95
&gt;&gt;&gt; lines.first()                                                               #返回RDD中的第一个元素，也就是README.md的第一行
u'# Apache Spark'
&gt;&gt;&gt; 
</code></pre>      需要注意的是这里的文件路径，如果是本地文件，需要加上 file://       若是hdfs文件则路径为：hdfs://ip:9000/xxx/xxx
<p></p>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;"></span></p>
<p style="color:rgb(51,51,51);font-size:14px;line-height:26px;">      textFile的参数是一个path,这个path可以是：</p>
<p style="color:rgb(51,51,51);font-size:14px;line-height:26px;">      1. 一个文件路径，这时候只装载指定的文件</p>
<p style="color:rgb(51,51,51);font-size:14px;line-height:26px;">      2. 一个目录路径，这时候只装载指定目录下面的所有文件（<span>不包括子目录下面的文件</span>）</p>
<p style="color:rgb(51,51,51);font-size:14px;line-height:26px;">      3. 压缩文件读取，如 textFile(”/my/directory/*.gz“) 注意必须是gzip压缩的<br></p>
<p style="color:rgb(51,51,51);font-size:14px;line-height:26px;"><span>     <span>4</span>. 通过通配符的形式加载多个文件或者加载多个目录下面的所有文件</span></p>
<span style="font-family:'Microsoft YaHei';font-size:14px;"></span>
<p style="color:rgb(51,51,51);font-size:14px;line-height:26px;">      第四点是一个使用小技巧，现在假设我的<a href="http://lib.csdn.net/base/31" rel="nofollow" class="replace_word" title="算法与数据结构知识库" style="color:#df3434;font-weight:bold;">数据结构</a>为先按天分区，再按小时分区的，在hdfs上的目录结构类似于：</p>
<span style="font-family:'Microsoft YaHei';font-size:14px;"></span>
<p style="color:rgb(51,51,51);font-size:14px;line-height:26px;">      /user/hdfs/input/dt=20130728/hr=00/</p>
<p style="color:rgb(51,51,51);font-size:14px;line-height:26px;">      /user/hdfs/input/dt=20130728/hr=01/<br></p>
<p style="color:rgb(51,51,51);font-size:14px;line-height:26px;">      ...</p>
<p style="color:rgb(51,51,51);font-size:14px;line-height:26px;">      /user/hdfs/input/dt=20130728/hr=23/<br></p>
<p style="color:rgb(51,51,51);font-size:14px;line-height:26px;">      具体的数据都在hr等于某个时间的目录下面，现在我们要分析20130728这一天的数据，我们就必须把这个目录下面的所有hr=*的子目录下面的数据全部装载进RDD，于是我们可以这样写：sc.textFile("hdfs://n1:8020/user/hdfs/input/dt=20130728/hr=*/"),注意到hr=*,是一个模糊匹配的方式。</p>
<h2>    3：Scala进行函数统计</h2>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;"></span></p>
<pre><code class="language-python">scala&gt; val lines = sc.textFile("file:///usr/local/hadoop/spark/README.md")
lines: org.apache.spark.rdd.RDD[String] = file:///usr/local/hadoop/spark/README.md MapPartitionsRDD[1] at textFile at &lt;console&gt;:27

scala&gt; lines.count()
res0: Long = 95

scala&gt; lines.first()
res1: String = # Apache Spark

scala&gt; 
</code></pre>
<p></p>
<h1><span style="font-family:'Microsoft YaHei';font-size:24px;">二：Spark核心概念简介</span></h1>
<h2><span style="font-family:'Microsoft YaHei';font-size:14px;">       <span style="font-size:18px;">
1：RDD（弹性<span style="font-family:'Microsoft YaHei';">分布</span>数据集）</span></span></h2>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;"></span></p>
<div align="left">        RDD是Spark的最基本抽象,是对分布式内存的抽象使用，实现了以操作本地集合的方式来操作分布式数据集的抽象实现。RDD是Spark最核心的东西，它表示已被分区，不可变的并能够被并行操作的数据集合，不同的数据集格式对应不同的RDD实现。RDD必须是可序列化的。RDD可以cache到内存中，每次对RDD数据集的操作之后的结果，都可以存放到内存中，下一个操作可以直接从内存中输入，省去了MapReduce大量的磁盘IO操作。这对于迭代运算比较常见的机器学习算法,
 交互式数据挖掘来说，效率提升比较大。</div>
<div align="left">RDD的特点：</div>
<div align="left">　　(1). 它是在集群节点上的不可变的、已分区的集合对象。</div>
<div align="left">　　(2). 通过并行转换的方式来创建如(map, filter, join,etc)。</div>
<div align="left">　　(3). 失败自动重建。</div>
<div align="left">　　(4). 可以控制存储级别(内存、磁盘等)来进行重用。</div>
<div align="left">　　(5). 必须是可序列化的。</div>
<div align="left">        (6). 是静态类型的。</div>
       RDD创建的两种方式
<p></p>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;">        (1)：从Hadoop文件系统(或与Hadoop兼容的其它存储系统)输入(例如HDFS)创建</span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;">        (2)：<span style="font-family:'Microsoft YaHei';font-size:14px;">从父RDD转换得到新RDD</span></span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;"><span style="font-family:'Microsoft YaHei';font-size:14px;"></span><br></span></p>
<h2><span style="font-family:'Microsoft YaHei';font-size:14px;">       <span style="font-size:18px;">
2：驱动器程序（driver program）</span></span></h2>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;">       每个spark应用用驱动器程序发起集群上的各种并行操作，包含了应用的main函数，并定义了集群上的分布式数据集，还对这些分布式数据集应用了相关操作</span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;">       驱动器程序通过一个sparkContext对象来访问Spark，这个对象代表对计算机集群的一个连接，shell在启动时就已经创建了一个SparkContext对象，是一个sc变量，一旦有了SparkContext就可以用它来创建RDD（弹性<span style="font-family:'Microsoft YaHei';font-size:14px;">分布</span>数据集）</span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;">      <br></span></p>
<h2><span style="font-family:'Microsoft YaHei';font-size:14px;">       <span style="font-size:18px;">
3：执行器（executor）</span></span></h2>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;"><strong><span style="color:#ff0000;">      在Worker Node上为某Application启动一个<span style="color:#0000ff;">进程</span>，该进程负责运行任务，并且负责将数据存在硬盘或者内存中；</span><span style="color:#ff0000;">每个Application都有各自独立的executors</span></strong>；</span></p>
<div>       比如：应用A在一个Node上启动Executor，B应用也在同一个Node上启动Executor，他们各自的Executor是相互隔离的，运行在不同的JVM上。不同的应用对应不同的Executor；</div>
<p></p>
<h2>      <span style="font-family:'Microsoft YaHei';font-size:18px;">4：Application</span></h2>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;">       <strong><span style="color:#ff0000;">基于spark的用户程序，包含了一个Driver Program以及集群上中多个executor；</span></strong></span></p>
<div><span style="font-family:'Microsoft YaHei';font-size:14px;">   <strong>   <span style="color:#599ffe;">
spark中只要有一个sparkcontext就是一个application</span></strong>；</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:14px;">       启动一个spark-shell也是一个application，因为在启动shark-shell时就内置了一个sc(SparkContext的实例)；<br>
 <br></span>
<h2><span style="font-family:'Microsoft YaHei';font-size:14px;">     <span style="font-size:18px;">
5：Cluster Manager</span></span></h2>
<span style="font-family:'Microsoft YaHei';font-size:14px;">      <strong><span style="color:#ff0000;">在集群上获取资源的外部服务</span></strong>。如：standalone、yarn、mesos；</span>
<div><span style="font-family:'Microsoft YaHei';font-size:14px;">　  各种不同的集群的区别：只是任务调度的粗细粒度不同，对学习spark没有影响，自己在学习时使用standalone即可；</span></div>
<span style="font-family:'Microsoft YaHei';font-size:14px;"><br></span>
<h2><span style="font-family:'Microsoft YaHei';font-size:14px;">     <span style="font-size:18px;">
6：Worker Node</span></span></h2>
<div><span style="font-family:'Microsoft YaHei';font-size:14px;">      <span style="color:#ff0000;">
<strong>集群中任何一个可以运行Application代码的节点</strong></span>；</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:14px;">      可以在Worker Node启动Executor进程；</span></div>
<span style="font-family:'Microsoft YaHei';font-size:14px;"><br></span>
<h2><span style="font-family:'Microsoft YaHei';font-size:14px;">     <span style="font-size:18px;">
7：Job</span></span></h2>
<div><span style="font-family:'Microsoft YaHei';font-size:14px;">      包含很多task的并行计算，<span style="color:#ff0000;"><strong>spark中的一个action对应一个job</strong></span>，如：collect、count、saveAsTextFile；</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:14px;">      <span style="color:#ff0000;">
<strong>用户提交的Job会提交给DAGScheduler，Job会被分解成Stage(TaskSet) DAG</strong></span>；</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:14px;">      RDD的transformation只会记录对元数据的操作(map/filter)，而并不会真正执行，只有action触发时才会执行job；</span></div>
<span style="font-family:'Microsoft YaHei';font-size:14px;"><br></span></div>
<p></p>
<h2><span style="font-family:'Microsoft YaHei';font-size:14px;">     <span style="font-size:18px;">
8：Stage</span></span></h2>
<p></p>
<div><span style="font-family:'Microsoft YaHei';font-size:14px;">     <span style="color:#ff0000;">
<strong>每</strong></span><span style="color:#ff0000;"><strong>个Job会被拆分成多组任务，每组</strong></span><strong>任务</strong><span style="color:#ff0000;"><strong>被称为一个Stage,可称为TaskSet</strong></span>；</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:14px;">     <strong>一个stage的边界往往是从某个地方取数据开始(如：sc.readTextFile)，在shuffle时(如：join、reduceByKey等)终止</strong>；</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:14px;">     一个job的结束(如：count、saveAsTextFile等)往往也是一个stage的边界；</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:14px;">     有两种类型的Stage：shuffle和result；</span></div>
<span style="font-family:'Microsoft YaHei';font-size:14px;"><br></span>
<p></p>
<h2><span style="font-family:'Microsoft YaHei';font-size:14px;">    <span style="font-size:18px;">
9：Task</span></span></h2>
<p></p>
<div><span style="font-family:'Microsoft YaHei';font-size:14px;">     <span style="color:#ff0000;">
<strong>被送到executor上的工作单元</strong></span>；</span></div>
<div>
<div><span style="font-family:'Microsoft YaHei';font-size:14px;">     在Spark中有两类Task：shuffleMapTask和ResultTask，第一类Task的输出是shuffle所需数据，第二类task的输出是result；</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:14px;">　 stage的划分也以此为依据，shuffle之前的所有变换是一个stage，shuffle之后的操作是另一个stage；</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:14px;">     比如：rdd.parallize(1 to 10).foreach(println)这个操作没有shuffle，直接就输出了，那么它的task就是resulttask，stage也只有一个；</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:14px;">     如果rdd.map((x,1)).reduceByKey(_+_).foreach(println)，这个job因为有reduce，所以有个一shuffle过程，那么reduceByKey之前是一个stage，执行shuffleMapTask，输出shuffle所需要的数据，reduceByKey到最后是一个stage，直接就输出结果了。</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:14px;">     如果一个job中有多次shuffle，那么每个shuffle之前都是一个stage；</span></div>
</div>
<p></p>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;">          <br></span></p>
<h2><span style="font-family:'Microsoft YaHei';font-size:14px;">    <span style="font-size:18px;">
10：Partition</span></span></h2>
<p></p>
<div><span style="font-family:'Microsoft YaHei';font-size:14px;">     partition类似hadoop的split，计算是以partition为单位进行的</span></div>
<br><p></p>
<h1><span style="font-family:'Microsoft YaHei';font-size:24px;">三：独立应用</span></h1>
<h2><span style="font-family:'Microsoft YaHei';font-size:14px;">      1：Python中初始化Spark</span></h2>
<p><br><span style="font-family:'Microsoft YaHei';font-size:14px;"></span></p>
<pre><code class="language-python">from pyspark import SparkConf, SparkContext

conf = SparkConf().setMaster("local").setAppName("My App")
sc = SparkContext(conf = conf)                      #&lt;span style="font-family:Microsoft YaHei;"&gt;以上为初始化sc&lt;/span&gt;
line = sc.textFile("file:///usr/local/hadoop/spark/README.md")
print line.count()
print line.first()
</code></pre>       执行：bin/spark-submit my_script.py
<p></p>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;">       运行结果：</span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;">                     <img src="https://img-blog.csdn.net/20160617150917495?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<h2><span style="font-family:'Microsoft YaHei';font-size:14px;">       2：Scala中初始化Spark</span></h2>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;"></span></p>
<pre><code class="language-python">import org.apache.spark.SparkConf
import org.apache.spark.SparkContext
import org.apache.spark.SparkContext_

val conf = new SparkConf().setMaster("local").setAppName("My App")
val sc = new SparkContext(conf)
</code></pre>
<h2>      3：在Java中初始化Spark</h2>
<p></p>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;"></span></p>
<pre><code class="language-java">import org.apache.spark.SparkConf
import org.apache.spark.api.java.JavaSparkContext

SparkConf conf = new SparkConf().setMaster("local").setAppName("My App")
JavaSparkContext sc = new JavaSparkContext(conf)
</code></pre><br><h1>四：Spark数据集</h1>
<p></p>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;">       – Spark 可以将任何 hadoop 所支持存储资源转化成 RDD ,如本地文件、 HDFS 、 Cassandra 、 HBase, Amazon S3 等。<br>
       – Spark 支持 text files, SequenceFiles 和任何 Hadoop InputFormat 格式<br>
            ●使用 textFile() 方法可以将本地文件或 HDFS 文件转换成 RDD<br>
                  – 如果读取本地文件,各节点都要有该文件;或者使用网络共享文件 <br>
                  – 支持整个文件目录读取,如 textFile("/my/directory")<br>
                  – 压缩文件读取,如 textFile("/my/directory/*.gz")<br>
                  – 通配符文件读取,如 textFile("/my/directory/*.txt")<br>
                  – textFile() 有可选的第二个参数 slice ,默认情况下,为每个 block 创建一个分片,用户也可以通过 slice 指定更多的分片,</span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;">                      但不能使用少于 block 数的分片<br>
            ●使用 wholeTextFiles() 读取目录里面的小文件,返回 ( 文件名,内容 ) 对<br>
            ●使用 sequenceFile[K,V]() 方法可以将 SequenceFile 转换成 RDD<br>
            ●使用 hadoopRDD() 方法可以将其他任何 Hadoop 的输入类型转化成 RDD <br></span></p>
            </div>
                </div>