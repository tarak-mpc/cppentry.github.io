---
layout:     post
title:      Spark Core笔记
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="spark-core笔记">Spark Core笔记</h1>

<p>Spark</p>

<hr>



<h2 id="一what-is-spark">一、What is Spark</h2>

<p><strong>Apache Spark is a fast and general engine for large-scale data processing</strong></p>



<h2 id="二spark框架优势">二、Spark框架优势</h2>

<p><strong>数据结构：RDD，用于存储和管理数据</strong></p>

<ul>
<li>DAG 调度 <br>
Spark 中每个Job运行的调度都是DAG调度 <br>
DAG：有向无环图</li>
<li>计算过程中中间结果临时数据（RDD） <br>
保存在内存中（除去产生Shuffle的RDD）</li>
</ul>



<h2 id="三ways-to-run-spark">三、Ways to run Spark</h2>

<ul>
<li>Local Mode</li>
<li>Spark Standalone</li>
<li>Hadoop Yarn</li>
<li>Apache Mesos</li>
</ul>



<h3 id="1大家喜欢spark编程-如下四个方面">1、大家喜欢Spark编程 ，如下四个方面：</h3>

<ul>
<li>代码： <br>
<ul><li>很少、很少，在业务逻辑上 函数式编程，简洁</li></ul></li>
<li>开发： <br>
<ul><li>开发测试很简单</li>
<li>由于Spark框架使用Scala语言编写，提供Scala Shell交互式命令，此外也支持Python语言，也提供命令行 <br>
在Windows系统上直接开发测试，不需要配置任何插件</li></ul></li>
<li>监控： <br>
运行每一个程序，都有自己的一个监控页面，端口号4040</li>
<li>运速快： <br>
比MapReduce快很多</li>
</ul>



<h3 id="2runs-everywhere">2、Runs Everywhere</h3>

<ul>
<li>a. Spark 框架开发的程序运行在哪里？？？ <br>
如果使用Java/Scala语言编程，打成 JAR包  <br>
<ul><li>本地模式 <br>
Local Mode   - 用于开发测试</li>
<li>集群模式 <br>
Hadoop YARN、Apache Mesos、Spark Standalone</li></ul></li>
<li>b. Spark 处理数据在哪里？？？ <br>
HDFS、Hive、HBase、ORC、Parquet、CSV、TSV、JDBC、Redis、MongoDB、ES、。。。。。</li>
</ul>

<p>词频统计演示</p>



<pre class="prettyprint"><code class=" hljs avrasm">val inputRDD = sc<span class="hljs-preprocessor">.textFile</span>(<span class="hljs-string">"/datas/wordcount.data"</span>)
val wordCountRDD = inputRDD<span class="hljs-preprocessor">.flatMap</span>(_<span class="hljs-preprocessor">.split</span>(<span class="hljs-string">"\\s+"</span>)
    <span class="hljs-preprocessor">.map</span>(_<span class="hljs-preprocessor">.trim</span>))
    <span class="hljs-preprocessor">.map</span>(word =&gt; (word, <span class="hljs-number">1</span>))<span class="hljs-preprocessor">.reduceByKey</span>(_ + _)
wordCountRDD<span class="hljs-preprocessor">.saveAsTextFile</span>(<span class="hljs-string">"/datas/spark-wc-output"</span>)
wordCountRDD<span class="hljs-preprocessor">.foreach</span>(println)</code></pre>



<h3 id="3spark-standalone-cluster">3、Spark Standalone Cluster</h3>

<ul>
<li>Master <br>
<ul><li>接收Worker的注册请求，统筹记录所有Worker的CPU、Memory等资源，并跟踪Worker节点的活动状态</li>
<li>接受Driver中App的注册请求（这个请求有Driver端的client发出），为App在Worker上分配CPU、Memory等资源，生成后台Executor进程，之后跟踪Executor和App的活动状态</li></ul></li>
<li>Worker <br>
<ul><li>负责接收Master的指示，为App创建Executor进程。Worker在Master和Executor之间起着桥梁作用，实际不会参与计算工作</li></ul></li>
<li>Driver <br>
<ul><li>负责用户侧逻辑处理</li></ul></li>
<li>Executor <br>
<ul><li>负责计算，接收并执行由App划分的Task任务，并将结果缓存在本地内存或磁盘</li></ul></li>
</ul>



<h2 id="四rddresilient-distributed-dataset-弹性分布式数据集">四、RDD（Resilient Distributed Dataset 弹性分布式数据集）</h2>



<h3 id="1就是一个集合">1、就是一个集合</h3>

<ul>
<li>从使用的角度来看 <br>
<ul><li>当做Scala语言集合类中的列表List</li></ul></li>
<li>实质 <br>
<ul><li>分布式、存储数据、集合</li>
<li>Represents an immutable，partitioned collection of elements that can be operated on parallel（将集合中的数据划分为很多分区（partition），不同分区的数据存储在不同的机器上，每个分区的数据可以被一个Task进行处理）</li></ul></li>
<li>如果从HDFS上读取数据，Spark程序运行在集群模式下，一个block数据对应一个分区Partition数据</li>
<li>RDD 五个主要特征（特征） <br>
<ul><li>A list of partitions <br>
<ul><li><code>RDD = List&lt;Partition&gt;</code></li></ul></li>
<li>A function for computing each split <br>
<ul><li><code>split = partition</code></li>
<li>每个分区的数据都可以应用函数进行计算</li></ul></li>
<li>A list of dependencies on other RDDs <br>
<ul><li>List</li></ul></li></ul></li>
</ul>



<h3 id="2创建rdd">2、创建RDD</h3>

<p>将要处理的数据转换为RDD</p>

<ul>
<li>从HDFS/LocalFS 读取数据 <br>
sc.textFile(“..”)/spark.read.textFile(“…”)</li>
<li>并行化Scala中集合 <br>
<code>def parallelize[T: ClassTag]( <br>
seq: Seq[T], <br>
numSlices: Int = defaultParallelism <br>
): RDD[T]</code></li>
<li>应用RDD中的Transformation转换函数 <br>
<ul><li>将一个RDD转换为另外一个RDD</li></ul></li>
</ul>

<p>示例（Spark开发经典案例WordCount）：</p>



<pre class="prettyprint"><code class="language-spark hljs scala"><span class="hljs-keyword">package</span> com.erongda.bigdata.spark.core

<span class="hljs-keyword">import</span> org.apache.spark.rdd.RDD
<span class="hljs-keyword">import</span> org.apache.spark.{SparkConf, SparkContext}

<span class="hljs-javadoc">/**
  * Spark 开发大数据经典案例，词频统计Wordcount
  */</span>
<span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">SparkWordCount</span> {</span>

  <span class="hljs-keyword">def</span> main(args: Array[String]): Unit = {

    <span class="hljs-comment">// Spark Application运行时的相关配置信息，比如AppName，Master</span>
    <span class="hljs-keyword">val</span> sparkConf = <span class="hljs-keyword">new</span> SparkConf()
      .setAppName(<span class="hljs-string">"Spark Application"</span>)
      <span class="hljs-comment">//设置应用运行在哪里，是本地模式还是集群（具体制定的地址）</span>
      .setMaster(<span class="hljs-string">"local[2]"</span>)

    <span class="hljs-comment">// 创建SparkContext对象，主要用于读取处理的数据，封装在集合RDD中，调度Job</span>
    <span class="hljs-keyword">val</span> sc = <span class="hljs-keyword">new</span> SparkContext(sparkConf)
    sc.setLogLevel(<span class="hljs-string">"WARN"</span>)

    <span class="hljs-javadoc">/**
      * 第一步 数据的读取（输入input）
      */</span>
    <span class="hljs-keyword">val</span> inputRDD: RDD[String] = sc.textFile(<span class="hljs-string">"/datas/wordcount.data"</span>)
    <span class="hljs-comment">//查看样本数据</span>
    println(s<span class="hljs-string">"count = ${inputRDD.count()}"</span>)
    println(s<span class="hljs-string">"first = \n\t ${inputRDD.first()}"</span>)

    <span class="hljs-javadoc">/**
      * 第二步 数据的处理
      */</span>
    <span class="hljs-comment">// 内功</span>
    <span class="hljs-keyword">val</span> wordCountRDD: RDD[(String, Int)] = inputRDD.flatMap(_.split(<span class="hljs-string">"\\s+"</span>)).map((_, <span class="hljs-number">1</span>)).reduceByKey(_ + _)

    <span class="hljs-comment">// 基本</span>
    inputRDD
      .flatMap(line =&gt; line.split(<span class="hljs-string">"\\s+"</span>))
      .map(word =&gt; (word, <span class="hljs-number">1</span>))
      .reduceByKey((tmp, item) =&gt; tmp + item)

    <span class="hljs-comment">// 按照统计词频count进行降序排序</span>
    <span class="hljs-javadoc">/**
      * def sortByKey(ascending: Boolean = true, numPartitions: Int = self.partitions.length)
      *     从函数的名称sortByKey：
      *       按照Key进行排序的
      *     第一个参数“
      *       ascending = true 默认值，表示暗战升序排序
      */</span>
    println(<span class="hljs-string">"======== sortByKey ========"</span>)
    <span class="hljs-keyword">val</span>  sortedWordCountRDD = wordCountRDD.map(_.swap).sortByKey(ascending = <span class="hljs-keyword">false</span>)
    sortedWordCountRDD.take(<span class="hljs-number">3</span>).foreach(println)

    println(<span class="hljs-string">"========= top ============"</span>)
    <span class="hljs-javadoc">/**
      * def top(num: Int)(implicit ord: Ordering[T]: Array[T])
      *     takeOrdered(num)(ord.reverse)
      */</span>
    wordCountRDD.top(<span class="hljs-number">3</span>)(OrderUtils.SecondValueOrdering).foreach(println)

    <span class="hljs-javadoc">/**
      * TODO:
      *     在企业中使用SparkCore RDD 来分析数据
      *       -a. 如果获取最大的前几个TopKey
      *         rdd#top
      *       -b. 如果获取最小的前几个BottomKey
      *         rdd#takeOrdered()
      */</span>

    <span class="hljs-javadoc">/**
      * 第三步 数据的保存（输出output）
      */</span>
    <span class="hljs-comment">// 查看处理后的数据</span>
    println(<span class="hljs-string">"=========== 原始数据 ============"</span>)
    wordCountRDD.foreach(println)

    Thread.sleep(<span class="hljs-number">1000000</span>)
    <span class="hljs-comment">// 关闭资源</span>
    sc.stop()
  }
}

<span class="hljs-javadoc">/**
  * 自定义的排序规则，依据实际需求定义
  */</span>
<span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">OrderUtils</span>{</span>
  <span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">SecondValueOrdering</span> <span class="hljs-keyword">extends</span> <span class="hljs-title">scala</span>.<span class="hljs-title">math</span>.<span class="hljs-title">Ordering</span>[<span class="hljs-params">(String, Int)</span>]{</span>
    <span class="hljs-keyword">override</span> <span class="hljs-keyword">def</span> compare(x: (String, Int), y: (String, Int)): Int = {
      x._2 - y._2
    }
  }
}</code></pre>



<h2 id="五rdd集合类中的函数80多种可以分为三类">五、RDD集合类中的函数（80多种，可以分为三类）</h2>



<h3 id="1转换函数transformations重点">1、转换函数（Transformations）重点</h3>

<p><strong>特点</strong></p>

<ul>
<li>return a new RDD</li>
<li>一个RDD调用函数以后转换为另外一个RDD</li>
</ul>



<h3 id="2action函数">2、Action函数</h3>

<p><strong>特点</strong></p>

<ul>
<li>launch a job to return a value to the user program</li>
<li>当一个RDD调用函数以后，就会触发一个Job的执行，不会转换为RDD</li>
</ul>

<blockquote>
  <p>如： <br>
  count -&gt; Long, first -&gt; 集合中第一条数据 <br>
  take -&gt; 获取集合中前N条数据 <br>
  foreach -&gt; 对每条进行操作，比如答应数据，无返回值</p>
</blockquote>



<h3 id="3persistence">3、Persistence（）</h3>

<p><strong>持久化函数：将RDD集合中的数据缓存到内存或磁盘中</strong></p>

<p>如：</p>



<pre class="prettyprint"><code class="language-spark hljs python">
<span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">cache</span><span class="hljs-params">()</span>:</span> this.type = persist()
<span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">persist</span><span class="hljs-params">()</span>:</span> this.type = persist(StorageLevel.MEMORY_ONLY)
StorageLevel：
    表示的将RDD数据存储的地方（存储级别）
<span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">StorageLevel</span> <span class="hljs-title">private</span><span class="hljs-params">(
    // 将数据存储到磁盘中
    private var _useDisk: Boolean,
    // 将数据存储到内存中
    private var _useMemory: Boolean,
    // JVM 内存中，Alluxio
    private var _useOffHeap: Boolean,
    // 数据存储的时候是否反序列化
    private var _deserialized: Boolean,
    // 持久化数据的副本数，默认值为<span class="hljs-number">1</span>
    private var _replication: Int = <span class="hljs-number">1</span>
)</span></span></code></pre>

<ul>
<li><p>Alluxio：分布式内存文件系统 <br>
类似于HDFS分布式文件系统，不过Ta将数据存储在内存中</p></li>
<li><p>在什么情况 ，将RDD数据持久化呢？？？</p>

<ul><li>某个RDD数据，被使用多次重复使用</li>
<li>某个RDD的数据来之不易，使用超多一次,经过复杂处理获取的RDD</li>
<li>通常选择缓存数据策略 <br>
<ul><li>MEMORY_ONLY_2     如果集群内存足够充分</li>
<li>MEMORY_AND_DISK_SER_2  先内存后磁盘，2副本数</li></ul></li></ul></li>
<li><p>释放存储数据</p>

<ul><li><code>def unpersist(blocking: Boolean = true): this.type</code></li></ul></li>
<li><p>RDD 中Aciton函数</p>

<ul><li>调用函数以后，返回一个非RDD的值Driver <br>
 count/first/take/collect </li>
<li>调用函数以后，没有返回值  <br>
<ul><li>打印、存储外部系统 foreach： <br>
<ul><li>针对RDD中每条数据进行操作的</li></ul></li>
<li>但是我们建议使用foreachPartition： <br>
<ul><li>针对RDD中每个分区的数据进行操作的</li></ul></li></ul></li></ul></li>
<li><p>在RDD中调整分区数的大小函数：</p>

<ul><li>coalesce：调整分区数，不进行Shuffle，性能较好 <br>
最原始的，源码如下： <br>
<code>def coalesce( <br>
numPartitions: Int,  <br>
shuffle: Boolean = false <br>
): RDD[T]</code></li>
<li>repartition：进行Shuffle操作，性能所有消耗，不建议使用 <br>
<code>def repartition(numPartitions: Int)(implicit ord: Ordering[T] = null): RDD[T]</code> <br>
<ul><li>底层：<code>`coalesce(numPartitions, shuffle = true)</code></li></ul></li></ul></li>
</ul>

<p>示例（SparkSessionWordCount）：</p>



<pre class="prettyprint"><code class="language-spark hljs scala"><span class="hljs-keyword">package</span> com.erongda.bigdata.spark.core

<span class="hljs-keyword">import</span> org.apache.spark.rdd.RDD
<span class="hljs-keyword">import</span> org.apache.spark.sql.SparkSession

<span class="hljs-javadoc">/**
  * Created by Administrator on 2018/7/16.
  */</span>
<span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">SparkSessionWordCount</span> {</span>

  <span class="hljs-keyword">def</span> main(args: Array[String]): Unit = {
    <span class="hljs-comment">// 创建SparkSession实例对象</span>
    <span class="hljs-keyword">val</span> spark: SparkSession = SparkSession.builder()
      .appName(<span class="hljs-string">"SparkSessionWordCount"</span>)
      .master(<span class="hljs-string">"local[2]"</span>)
      .getOrCreate()

    <span class="hljs-comment">// 设置日志级别</span>
    spark.sparkContext.setLogLevel(<span class="hljs-string">"WARN"</span>)

    <span class="hljs-javadoc">/**
      * 使用SparkSession读取数据
      */</span>
    <span class="hljs-keyword">val</span> inputRDD: RDD[String] = spark.read.textFile(<span class="hljs-string">"/datas/wordcount.data"</span>).rdd
    <span class="hljs-comment">// inputRDD.cache()  数据缓存</span>
    <span class="hljs-comment">// inputRDD.unpersist() 释放缓存</span>
    <span class="hljs-comment">// 数据处理</span>
    <span class="hljs-keyword">val</span> wordCount: RDD[(String, Int)] = inputRDD.flatMap(_.split(<span class="hljs-string">"\\s+"</span>).map((_, <span class="hljs-number">1</span>)))  <span class="hljs-comment">// 性能高</span>
      .reduceByKey(_ + _)

    wordCount.foreach(println)

    <span class="hljs-comment">// 为了4040</span>
    Thread.sleep(<span class="hljs-number">1000000</span>)

    <span class="hljs-comment">// 关闭资源</span>
    spark.close()

  }

}</code></pre>

<p>示例（SessionTimeCountSpark）：</p>



<pre class="prettyprint"><code class="language-spark hljs scala"><span class="hljs-keyword">package</span> com.erongda.bigdata.spark.core

<span class="hljs-keyword">import</span> org.apache.spark.rdd.RDD
<span class="hljs-keyword">import</span> org.apache.spark.{SparkConf, SparkContext}

<span class="hljs-javadoc">/**
  * Created by Administrator on 2018/7/16.
  * 分析用户 Session会话时长， 各个时段占比
  */</span>
<span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">SessionTimeCountSpark</span> {</span>

  <span class="hljs-comment">// 会话时长：0 - 10 秒</span>
  <span class="hljs-keyword">val</span> TIME_LENGTH_0010: String = <span class="hljs-string">"0-10"</span>
  <span class="hljs-comment">// 会话时长： 11 - 20 秒</span>
  <span class="hljs-keyword">val</span> TIME_LENGTH_1120: String = <span class="hljs-string">"11-20"</span>
  <span class="hljs-comment">// 会话时长： 21 - 30 秒</span>
  <span class="hljs-keyword">val</span> TIME_LENGTH_2130: String = <span class="hljs-string">"21-30"</span>
  <span class="hljs-comment">// 会话时长： 30+ 秒</span>
  <span class="hljs-keyword">val</span> TIME_LENGTH_3000: String = <span class="hljs-string">"30+"</span>

  <span class="hljs-keyword">def</span> main(args: Array[String]): Unit = {

    <span class="hljs-comment">// Spark Application运行时的相关配置信息，比如AppName, Master</span>
    <span class="hljs-keyword">val</span> sparkConf = <span class="hljs-keyword">new</span> SparkConf()
      .setAppName(<span class="hljs-string">"SessionTimeCountSpark"</span>)
      .setMaster(<span class="hljs-string">"local[2]"</span>)

    <span class="hljs-comment">// 创建SparkContext实例对象，主要用于读取处理的数据，封装集合到RDD中，调度Job</span>
    <span class="hljs-keyword">val</span> sc = <span class="hljs-keyword">new</span> SparkContext(sparkConf)
    sc.setLogLevel(<span class="hljs-string">"WARN"</span>)

    <span class="hljs-comment">// TODO: 1. 读取数据，从本地系统LocalFileSystem读取数据</span>
    <span class="hljs-keyword">val</span> pageViewRDD = sc.textFile(<span class="hljs-string">"file:///C:/spark-learning/datas/page_views.data"</span>)

    <span class="hljs-comment">// 将RDD集合的数据存储到内存中，属于lazy方法，需要一个Action函数触发，才会将数据真正的缓存</span>
    pageViewRDD.cache()

    <span class="hljs-comment">// 采用数据，获取第一条数据和总的条目数</span>
    println(s<span class="hljs-string">"count = ${pageViewRDD.count()} \nFirst: \n\t${pageViewRDD.first()} "</span>)

    <span class="hljs-javadoc">/**
      * 2.需求分析：
      *   统计 各个会话时长段 0-10,11-20,21-30,30+
      * 关键点：
      *   1. 统计各个会话时长
      *     按照session_id进行分组，得到每个会话中的所有的track_time, 使用最后一个track_time - 第一个track_time获取时长
      *   2. 判断会话时长属于哪个 时间段
      */</span>
    <span class="hljs-keyword">val</span> timeLengthCountRDD: RDD[(String, Int)] = pageViewRDD
      .map(line =&gt; {
        <span class="hljs-comment">//分割单词</span>
        <span class="hljs-keyword">val</span> arr = line.split(<span class="hljs-string">"\t"</span>)
        <span class="hljs-comment">// 将track_time日期类型的数据(2013-05-19 13:00:00)转换为Long类型，以便后续操作</span>
        <span class="hljs-keyword">import</span> java.text.SimpleDateFormat
        <span class="hljs-keyword">val</span> time = <span class="hljs-keyword">new</span> SimpleDateFormat(<span class="hljs-string">"yyyy-MM-dd HH:mm:ss"</span>).parse(arr(<span class="hljs-number">0</span>)).getTime
        <span class="hljs-comment">//返回</span>
        (arr(<span class="hljs-number">2</span>), time)
      })
      <span class="hljs-comment">// TODO: b. 按照session_id进行分组，得到每个session会话中的所有页面的访问时间</span>
      .groupByKey()  <span class="hljs-comment">// RDD[(String, Iterable[Long])]</span>
      <span class="hljs-comment">// TODO: c.计算每个会话的时长</span>
      .map{<span class="hljs-keyword">case</span> (session_id: String, iter: Iterable[Long]) =&gt;
          <span class="hljs-comment">// 最大的和最小的track_time</span>
        <span class="hljs-keyword">val</span> maxTrackTime = iter.max
        <span class="hljs-keyword">val</span> minTrackTime = iter.min
        <span class="hljs-comment">// 计算会话时长</span>
        <span class="hljs-keyword">val</span> timeLength = (maxTrackTime - minTrackTime) / <span class="hljs-number">1000.0</span>

        <span class="hljs-comment">// 判断时长属于哪个时长段</span>
        <span class="hljs-keyword">if</span> (<span class="hljs-number">30</span> &lt; timeLength){
          (TIME_LENGTH_3000, <span class="hljs-number">1</span>)
        }<span class="hljs-keyword">else</span> <span class="hljs-keyword">if</span>(<span class="hljs-number">20</span> &lt; timeLength){
          (TIME_LENGTH_2130, <span class="hljs-number">1</span>)
        }<span class="hljs-keyword">else</span> <span class="hljs-keyword">if</span>(<span class="hljs-number">10</span> &lt; timeLength){
          (TIME_LENGTH_1120, <span class="hljs-number">1</span>)
        }<span class="hljs-keyword">else</span>{
          (TIME_LENGTH_0010, <span class="hljs-number">1</span>)
        }
      }
      <span class="hljs-comment">// TODO: d. 聚合统计，计算出各个时长段的会话个数</span>
      .reduceByKey(_ + _)

    <span class="hljs-comment">// 显示结果</span>
    timeLengthCountRDD.foreach(println)

    <span class="hljs-comment">// 将RDD的数据从缓存中释放出来</span>
    pageViewRDD.unpersist()

    <span class="hljs-comment">// 为了开发测试 ，对每Application运行左监控，所以当前线程休眠</span>
    Thread.sleep(<span class="hljs-number">100000</span>)

    <span class="hljs-comment">// 关闭资源</span>
    sc.stop()

  }
}</code></pre>

<p>示例（SparkWordCountToMySQL）：</p>



<pre class="prettyprint"><code class="language-spark hljs scala"><span class="hljs-keyword">package</span> com.erongda.bigdata.spark.core

<span class="hljs-keyword">import</span> java.sql.{Connection, DriverManager}

<span class="hljs-keyword">import</span> org.apache.spark.rdd.RDD
<span class="hljs-keyword">import</span> org.apache.spark.{SparkConf, SparkContext}

<span class="hljs-javadoc">/**
  * Spark 开发大数据经典案例，词频统计Wordcount
  */</span>
<span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">SparkWordCountToMySQL</span> {</span>

  <span class="hljs-keyword">def</span> main(args: Array[String]): Unit = {

    <span class="hljs-comment">// Spark Application运行时的相关配置信息，比如AppName，Master</span>
    <span class="hljs-keyword">val</span> sparkConf = <span class="hljs-keyword">new</span> SparkConf()
      .setAppName(<span class="hljs-string">"Spark Application"</span>)
      <span class="hljs-comment">//设置应用运行在哪里，是本地模式还是集群（具体制定的地址）</span>
      .setMaster(<span class="hljs-string">"local[2]"</span>)

    <span class="hljs-comment">// 创建SparkContext对象，主要用于读取处理的数据，封装在集合RDD中，调度Job</span>
    <span class="hljs-keyword">val</span> sc = <span class="hljs-keyword">new</span> SparkContext(sparkConf)
    sc.setLogLevel(<span class="hljs-string">"WARN"</span>)

    <span class="hljs-javadoc">/**
      * 第一步 数据的读取（输入input）
      */</span>
    <span class="hljs-keyword">val</span> inputRDD: RDD[String] = sc.textFile(<span class="hljs-string">"/datas/wordcount.data"</span>)

    <span class="hljs-keyword">val</span> wordCount: RDD[(String, Int)] = inputRDD.flatMap(_.split(<span class="hljs-string">"\\s+"</span>).map((_, <span class="hljs-number">1</span>)))  <span class="hljs-comment">// 性能高</span>
      .reduceByKey(_ + _)

    wordCount.coalesce(<span class="hljs-number">1</span>)
      .foreachPartition(iter =&gt; {
        Class.forName(<span class="hljs-string">"com.mysql.jdbc.Driver"</span>)

        <span class="hljs-keyword">val</span> url =<span class="hljs-string">"jdbc:mysql://bigdata-training01.erongda.com:3306/test"</span>
        <span class="hljs-keyword">val</span> username = <span class="hljs-string">"root"</span>
        <span class="hljs-keyword">val</span> password = <span class="hljs-string">"123456"</span>

        <span class="hljs-keyword">var</span> conn: Connection = <span class="hljs-keyword">null</span>
        <span class="hljs-keyword">try</span>{
          conn = DriverManager.getConnection(url, username, password)

          <span class="hljs-keyword">val</span> sql = <span class="hljs-string">"INSERT INTO rdd_word_count (word, count) values(?,?)"</span>
          <span class="hljs-keyword">val</span> pst = conn.prepareStatement(sql)

          iter.foreach{
            <span class="hljs-keyword">case</span> (word: String, count: Int) =&gt;
              println(s<span class="hljs-string">"word = $word, count = $count"</span>)

              pst.setString(<span class="hljs-number">1</span>, word)
              pst.setInt(<span class="hljs-number">2</span>, count)

              pst.executeUpdate()
          }

        }<span class="hljs-keyword">catch</span>{
          <span class="hljs-keyword">case</span> e: Exception =&gt; e.printStackTrace()
        }<span class="hljs-keyword">finally</span> {
          <span class="hljs-keyword">if</span>(conn != <span class="hljs-keyword">null</span>) conn.close()
        }
      })

    Thread.sleep(<span class="hljs-number">1000000</span>)
    <span class="hljs-comment">// 关闭资源</span>
    sc.stop()
  }
}</code></pre>



<h2 id="六spark-standalone">六、Spark Standalone</h2>

<p>Spark框架自身带的分布式集群资源管理和任务调度的框架，类似Hadoop YARN框架</p>



<h3 id="1如何配置spark-standalone-cluster-伪分布式">1、如何配置Spark Standalone Cluster （伪分布式）</h3>

<ul>
<li>配置 <br>
conf/spark-env.sh 增加如下内容：</li>
</ul>



<pre class="prettyprint"><code class=" hljs avrasm">        SPARK_MASTER_HOST=bigdata-training01<span class="hljs-preprocessor">.erongda</span><span class="hljs-preprocessor">.com</span>
        SPARK_MASTER_PORT=<span class="hljs-number">7077</span>
        SPARK_MASTER_WEBUI_PORT=<span class="hljs-number">8080</span>
        SPARK_WORKER_CORES=<span class="hljs-number">2</span>
        SPARK_WORKER_MEMORY=<span class="hljs-number">2</span>g
        SPARK_WORKER_PORT=<span class="hljs-number">7078</span>
        SPARK_WORKER_WEBUI_PORT=<span class="hljs-number">8081</span></code></pre>

<ul>
<li>配置从节点Workers <br>
conf/slaves 内容： <br>
<code>bigdata-training01.erongda.com</code></li>
<li>启动服务 <br>
<ul><li>启动主节点Master服务 <br>
<code>$ sbin/start-master.sh</code></li>
<li>启动从节点Workers服务 - 启动所有的Workers服务 <br>
<code>$ start-slaves.sh</code> <br>
<ul><li>注意： <br>
此命令必须在Master主节点上执行，并且需要配置Master节点到所有Worker节点SSH无秘钥登录 <br>
<code>$ ssh-keygen -r rsa</code> <br>
<code>$ ssh-copy-id bigdata-training01.erongda.com</code></li></ul></li></ul></li>
<li>测试 <br>
运行一个spark-shell <br>
    到SparkStandalone Cluster上运行 <br>
<code>bin/spark-shell --master spark://bigdata-training01.erongda.com:7077</code></li>
</ul>



<h2 id="七大数据集群架构和数据规模">七、大数据集群架构和数据规模</h2>



<h3 id="1集群规模">1、集群规模</h3>

<ul>
<li>数据量：每天增量 <br>
<ul><li>记录数： <br>
    千万级别以上：单个访客访问 <br>
        亿级别记录数 <br>
    百万级别： <br>
        千万级别记录数 <br>
    几十万： <br>
        一千万级别</li>
<li>大小值： <br>
    记录数*每条记录的大小</li></ul></li>
<li><p>设计集群规模 <br>
    每天增量*365*3*3 / 12 = 从节点个数 <br>
    中小型规模：20台 <br>
    中型规模：50台 <br>
    大型规模：100台以上</p></li>
<li><p>硬件选型</p>

<ul><li>主节点： <br>
<ul><li>CPU：32核</li>
<li>内存：128GB</li>
<li>硬盘：SATA，SCSI，推荐SAS/SSD <br>
<ul><li>RAID0： <br>
    机器：disk1 2T   disk2 2T <br>
    系统：disk 4T <br>
        写数据时，随机写入两块硬盘</li>
<li>RAID1 <br>
    机器：disk1 2T   disk2 2T <br>
    系统：disk 2T <br>
        写数据时，写两份</li>
<li>JBOD：不属于raid <br>
    机器：disk1 2T   disk2 2T <br>
    系统：disk 4T</li></ul></li></ul></li></ul></li>
</ul>

<blockquote>
  <p>写数据时，先写第一块硬盘，直到第一块满了，才写第二块</p>
</blockquote>



<h3 id="2大数据运维部署">2、大数据运维部署</h3>

<ul>
<li><p>部署运维</p>

<ul><li><p>手动分发 <br>
    同步服务：rsync</p></li>
<li><p>CM：cloudera Manager</p>

<ul><li>分布式架构：主从</li>
<li>安装cm <br>
<ul><li>安装一台机器的cm</li>
<li>分发给其他机器</li>
<li>启动cm</li>
<li>勾选当前哪些机器构建集群</li>
<li>勾选需要安装的程序</li>
<li>选择每个进程在那台机器上</li>
<li>下一步，根据用户的选择，自动实现安装</li></ul></li></ul></li>
<li>技术选型 <br>
<ul><li>因素： <br>
<ul><li>业务需求</li>
<li>参考</li></ul></li>
<li>业务需求 <br>
<ul><li>架构 <br>
<ul><li>离线批处理</li>
<li>实时</li></ul></li>
<li>应用 <br>
<ul><li>数据分析</li>
<li>用户画像</li>
<li>推荐系统</li>
<li>推荐预测</li>
<li>数据挖掘</li>
<li>机器学习</li></ul></li>
<li>业务流程 <br>
<ul><li>数据采集</li>
<li>数据存储： <br>
<ul><li>持久性存储</li>
<li>缓存</li>
<li>数据仓库              </li></ul></li>
<li>数据处理 <br>
<ul><li>离线批处理</li>
<li>实时处理</li>
<li>交互式处理：spark SQL 、hive、impala、presto、kylin</li></ul></li></ul></li></ul></li></ul></li></ul></li>
</ul>



<h2 id="八spark-application应用在集群上">八、Spark Application应用在集群上</h2>



<h3 id="1driver-program-jvm-process">1、Driver Program  -  JVM Process</h3>

<blockquote>
  <p>相当于AppMaster，应用管理者，主要调度Job执行 <br>
  就是每个程序的main方法，必须创建SparkContext实例对象 <br>
  端口号：4040  ，提供应用的监控</p>
</blockquote>



<h3 id="2executors">2、Executors</h3>

<blockquote>
  <p>每个Executor是一个JVM Process（JVM 进程，相当于线程池），包含Memory和CPU Core，运行Tasks。 <br>
  Spark中Task是以线程Thread方式运行的，每个Task执行需要1 Core CPU。</p>
</blockquote>



<h2 id="九spark-application与mapreduce-application-区别">九、Spark Application与MapReduce Application 区别</h2>

<p><strong>每个Job（作业）都有很多Task（任务）进行计算</strong></p>



<h3 id="1对于mapreduce-application来说">1、对于MapReduce Application来说</h3>

<ul>
<li>bin/yarn jar …运行的一个MapReduce程序，其实就是一个MapReduce Application在运行。一个MR App就是一个MR Job（一个应用只有一个Job)。</li>
<li>无论是MapTask还是ReduceTask运行在JVM Process。</li>
</ul>



<h3 id="2对于spark-application来说">2、对于Spark Application来说</h3>

<ul>
<li>一个Application中有很多Job。</li>
<li>每个Task运行在一个线程Thread中，都需要1 Core CPU。</li>
</ul>



<h2 id="十rdd-中数据持久化操作属于lazy操作">十、RDD 中数据持久化操作(属于lazy操作)</h2>



<h3 id="需要rddaction函数调用job-触发才能进行持久化操作">需要RDD#Action函数调用(Job 触发)才能进行持久化操作</h3>

<blockquote>
  <p>注意： <br>
          Action函数必须对RDD中所有分区的数据进行操作，才能将RDD中所有分区的数据进行缓存。 <br>
          -a. first()函数仅仅获取RDD中某个分区的一条数据，所以仅仅 将一个分区中的数据进行缓存 <br>
          -b. 可以使用count()函数对RDD中所有的分区数据进行统计个数，来进行缓存数据（触发缓存）</p>
</blockquote>



<h2 id="十一对于rdd中某些函数的使用注意优化">十一、对于RDD中某些函数的使用注意：（优化）</h2>

<ul>
<li>能不使用groupByKey函数就不要使用，除非不得已 <br>
<ul><li>知道： <br>
<ul><li>groupByKey + map = reduceByKey: combiner</li></ul></li>
<li>reduceByKey： <br>
<ul><li>包含分组，在聚合的时候，先进行本地聚合，然后在进行分组中的聚合。</li></ul></li></ul></li>
<li>尽量使用xxParition函数代替xx函数 <br>
<ul><li>比如： <br>
<ul><li>foreach 与 foreachPartition</li>
<li>map 与 mapPartition <br>
<code>def map[U: ClassTag](f: T =&gt; U): RDD[U]  <br>
f -&gt;  针对RDD中每个元素进行操作 <br>
def mapPartitions[U: ClassTag](f: Iterator[T] =&gt; Iterator[U]): RDD[U] <br>
f -&gt; 针对RDD中每个分区中的元素进行操作的</code></li></ul></li></ul></li>
<li>适当调整RDD的分区数 <br>
<ul><li>在Spark 程序的运行中，一个Thread线程运行一个Task任务，一个Task处理一个分区Parition的数据。 <br>
<ul><li>Thread = Task = Partition</li></ul></li>
<li>方式一： <br>
    在读取数据的时候，就可以调整分区，通常加大分区数目 <br>
    sc.textFile(“”, minPartitions = number) <br>
    sc.parallelize(seq, numSlices = number)</li>
<li>方式二： <br>
    可以通过RDD直接调整分区数 <br>
    rdd.coalesce(numPartitions = number)</li>
<li>关键点： <br>
    对结果RDD或者数据预处理以后的RDD降低分区数</li></ul></li>
<li>数据多次使用，进行缓存</li>
</ul>



<h2 id="十二数据倾斜">十二、数据倾斜</h2>



<h3 id="1何为数据倾斜">1、何为数据倾斜</h3>

<ul>
<li>某些Task分析数据过多，某个Task分析数据过少，导致处理过程中运行的速度和效率是不一样，或者出现内存不足溢出情况。</li>
</ul>



<h3 id="2回顾hive中数据倾斜的处理">2、回顾Hive中数据倾斜的处理：</h3>

<ul>
<li>Hive 中某个MapReduce Job运行的发生数据倾斜方案： <br>
<ul><li>将一个MapReduce Job程序变成两个MapReduce Job程序来执行</li>
<li>具体方案： <br>
<a href="http://www.cnblogs.com/ggjucheng/archive/2013/01/03/2842860.html" rel="nofollow">http://www.cnblogs.com/ggjucheng/archive/2013/01/03/2842860.html</a></li>
<li>分阶段聚合： <br>
<ul><li>set hive.groupby.skewindata = true;</li>
<li>第一个MapReduce Job进行 局部聚合 <br>
给Key加上 指定范围 的随机数</li>
<li>第二MapReduce Job进行全局聚合 <br>
将Key前缀随机数去除，全局聚合</li></ul></li></ul></li>
</ul>



<h3 id="3rdd中四大聚合函数源码分析基本参数">3、RDD中四大聚合函数，源码分析，基本参数</h3>

<ul>
<li><p>第一个”聚合”函数：</p>

<ul><li><code>def groupByKey(): RDD[(K, Iterable[V])]</code>  - 643 行代码 <br>
<ul><li><code>def combineByKeyWithClassTag[C]</code> - 74行</li></ul></li></ul></li>
<li><p>第二个”聚合”函数：</p>

<ul><li><p><code>def reduceByKey(func: (V, V) =&gt; V): RDD[(K, V)]</code> - 328</p>

<ul><li><code>def combineByKeyWithClassTag[C]</code> - 74</li></ul></li>
<li><p>“聚合”函数 , 类似Scala中Listfold聚合函数  -243</p>

<ul><li><code>def foldByKey(zeroValue: V)(func: (V, V) =&gt; V): RDD[(K, V)]</code> <br>
<ul><li><code>def combineByKeyWithClassTag[C]</code> - 74</li></ul></li></ul></li></ul></li>
<li><p>第三个”聚合”函数：</p>

<ul><li><code>def aggregateByKey[U: ClassTag](zeroValue: U)  - 204 <br>
(zeroValue: U)  // 聚合中间临时变量初始化 <br>
( <br>
// 每个分区的聚合操作 <br>
seqOp: (U, V) =&gt; U, <br>
// 合并所有分区的聚合结果 <br>
combOp: (U, U) =&gt; U <br>
): RDD[(K, U)]</code> <br>
<ul><li><code>def combineByKeyWithClassTag[C]</code> - 74</li></ul></li></ul></li>
<li><p>第四个”聚合”函数： </p>

<ul><li><code>def combineByKey[C]   - 613 行 <br>
( <br>
createCombiner: V =&gt; C, <br>
mergeValue: (C, V) =&gt; C, <br>
mergeCombiners: (C, C) =&gt; C <br>
): RDD[(K, C)]</code> <br>
<ul><li><code>def combineByKeyWithClassTag[C]</code> - 74</li></ul></li></ul></li>
</ul>



<h3 id="4最最底层聚合函数的含义">4、最最底层”聚合”函数的含义</h3>

<p><code>def combineByKeyWithClassTag[C] <br>
( <br>
    // 创建  合并器：确定如何聚合合并Value的值, 初始化操作，聚合中间临时变量的数据类型 <br>
    createCombiner: V =&gt; C, <br>
    // 针对每个分区中V的值进行聚合操作 <br>
    mergeValue: (C, V) =&gt; C, <br>
    // 合并 各个分区聚合的结果 <br>
    mergeCombiners: (C, C) =&gt; C, <br>
    partitioner: Partitioner, // 指定分区器 <br>
    mapSideCombine: Boolean = true, // 默认不问 <br>
    serializer: Serializer = null   // 默认值，不问 <br>
) <br>
(implicit ct: ClassTag[C]): RDD[(K, C)]</code></p>



<h2 id="十三如何提交运行spark-application呢">十三、如何提交运行Spark Application呢？？？</h2>

<p>${SPARK_HOME}/bin/spark-submit   脚本运行提交所有的应用</p>

<ul>
<li>如何使用spark-sumbit： <br>
<code>spark-submit [options] &lt;app jar | python file&gt; [app arguments]</code> <br>
<ul><li>第一点： <br>
如果是Scala语言或者Python语言开发的程序，需要打成JAR包运行；如果是Python语言开发，直接指定脚本运行即可。</li>
<li>第二点： <br>
<ul><li>options可选项，主要用于配置应用运行时相关参数设置，如 <br>
<ul><li>a. 应用运行在哪里？？？？ <br>
<code>--master MASTER_URL</code> <br>
<ul><li>值：local[2], spark://…:7077, yarn,…</li></ul></li>
<li>b. 指定Driver Program  <br>
<ul><li>无论程序是运行在本地模式还是运行在集群上，都有Driver，指定资源（Memory和CPU Core）</li></ul></li>
<li>c. 如果运行在集群上，指定Executor相关信息 <br>
Executor个数，每个Executor资源配置</li></ul></li></ul></li>
<li>第三点： <br>
app arguments，表示的是指定应用程序运行时需要传递的参数</li></ul></li>
</ul>



<h2 id="十四spark-框架中的历史服务器">十四、Spark 框架中的历史服务器</h2>



<h3 id="1监控运行完成的spark-application">1、监控运行完成的Spark Application</h3>



<h3 id="2针对spark-historyserver来说如何配置">2、针对Spark HistoryServer来说，如何配置</h3>

<ul>
<li>将SparkApplication运行的EventLog存储到HDFS上 <br>
<a href="http://spark.apache.org/docs/2.2.0/configuration.html#spark-ui" rel="nofollow">http://spark.apache.org/docs/2.2.0/configuration.html#spark-ui</a> <br>
<ul><li>spark.eventLog.enabled： <br>
表示是否存储EventLog，设置为true</li>
<li>spark.eventLog.compress: <br>
表示是否进行压缩存储的EventLog，使用压缩算法lz4</li>
<li>spark.eventLog.dir: <br>
表示存储EventLog目录 <br>
hdfs://bigdata-training01.erongda.com:8020/datas/spark/eventLogs/ <br>
<ul><li>备注： <br>
/datas/spark/eventLogs/必须创建出来</li></ul></li>
<li>配置以上三种属性有多种方式 <br>
<ul><li>针对某个应用来配置属性 <br>
<ul><li>通过提交应用参数属性–命令行配置</li>
<li><code>--conf spark.eventLog.enabled=true \ <br>
--conf spark.eventLog.compress=true \ <br>
--conf spark.eventLog.dir=hdfs://bigdata-training01.erongda.com:8020/datas/spark/eventLogs/ \</code></li>
<li>程序编码配置</li>
<li><code>sparkConf.set("spark.eventLog.enabled", "true") <br>
sparkConf.set("spark.eventLog.compress", "true") <br>
sparkConf.set("spark.eventLog.dir", "hdfs://bigdata-training01.erongda.com:8020/datas/spark/eventLogs/")</code></li></ul></li>
<li>针对所有应用配置属性 <br>
<ul><li>只要运行SparkApplication，就会自动保存EventLog <br>
<code>${SPARK_HOME}/spark-default.conf</code></li></ul></li></ul></li></ul></li>
<li>启动HistoryServer历史服务  <br>
<ul><li><code>${SPARK_HOME}/conf/spark-env.sh</code> 中配置： <br>
<code>SPARK_HISTORY_OPTS="-Dspark.history.fs.logDirectory=hdfs://bigdata-training01.erongda.com:8020/datas/spark/eventLogs/ -Dspark.history.fs.cleaner.enabled=true"</code></li></ul></li>
</ul>



<h2 id="十五spark-application运行在yarn集群上">十五、Spark Application运行在YARN集群上</h2>

<p><a href="http://spark.apache.org/docs/2.2.0/running-on-yarn.html" rel="nofollow">http://spark.apache.org/docs/2.2.0/running-on-yarn.html</a></p>



<h3 id="1运行在yarn集群上">1、运行在YARN集群上</h3>

<ul>
<li>启动Hadoop YARN集群 <br>
<ul><li>ResourceManager/NodeManagers</li>
<li>向YARN集群提交应用，找到的RM服务（端口8032），告知RM所在主机</li></ul></li>
<li>配置Spark <br>
<ul><li><code>conf/spark-env.sh</code></li>
<li>JAVA_HOME</li>
<li>SCALA_HOME</li>
<li>HADOOP_CONF_DIR: HADOOP框架配置文件所在的目录 <br>
<ul><li>提交应用的时候，将会读取该目录下所有的属性文件 <br>
<ul><li>HDFS集群位置，读物HDFS上数据 <br>
<code>hdfs-site.xml core-site.xml</code></li>
<li>YARN集群位置（RM），提交应用到YARN上运行 <br>
<code>yarn-site.xml</code></li></ul></li></ul></li></ul></li>
<li>提交应用<code>${SPARK_HOME}/bin/spark-submit</code> <br>
<ul><li>以最简单的方式来演示：提交spark-shell运行在yarn上</li>
<li><code>SPARK_HOME=/opt/cdh-5.7.6/spark-2.2.0-bin-2.6.0-cdh5.7.6 <br>
${SPARK_HOME}/bin/spark-shell \ <br>
--master yarn \ <br>
--driver-memory 512M \ <br>
--executor-memory  1g \ <br>
--executor-cores 2 \ <br>
--num-executors 3</code></li></ul></li>
</ul>



<h3 id="2额外回顾yarn知识">2、额外回顾YARN知识</h3>

<p><strong>对于YARN集群来说，以下两点清楚</strong></p>

<ul>
<li>默认情况下每个NodeManager节点服务管理的资源 <br>
    <code>yarn-site.xml</code> <br>
<ul><li>内存大小：默认值 8G = 8192M <br>
<code>yarn.nodemanager.resource.memory-mb</code></li>
<li>CPU Core核数：默认值8核 <br>
<code>yarn.nodemanager.resource.cpu-vcores</code> <br>
<ul><li>虚拟核数考虑： <br>
    2 Core(i7) = 3 Core(i5)</li></ul></li></ul></li>
<li>YARN资源调度方式 <br>
<ul><li>按照队列方式进行资源调度</li>
<li>将集群资源划分为很多队列Queue方式进行调度，用户提交应用的时候，将应用提交到对应队列上，然后进行资源调度</li></ul></li>
</ul>



<h2 id="十六deploy-mode-deploymode">十六、deploy-mode DEPLOY_MODE</h2>

<p>表示的是Driver Program（JVM Process）运行在地方，如果运行在本地Localy称为client，也可以运行在集群的从节点（Worker节点或者NodeManager节点）上的某台机器上。 <br>
<strong>注意两点</strong></p>

<ul>
<li>在企业的实际开发环境中，Spark Application提交运行，采用的是“cluster”模式运行。</li>
<li>spark-shell不能运行在Cluster Mode下 <br>
<ul><li>Driver就是REPL（SCALA）交互式的命令行</li></ul></li>
</ul>



<h3 id="1spark-on-yarn">1、Spark on YARN:</h3>

<ul>
<li>Spark Application <br>
<ul><li>Driver Program  <br>
    应用的管理者（获取资源和调度Job执行)</li>
<li>Executors  <br>
    运行Tasks任务和Cache缓存数据</li>
<li>YARN Application  <br>
<ul><li>框架设计：如果一个应用运行在YARN上，首先给每个应用分区一个AppMaster（应用管理者）：获取资源和调度job执行</li>
<li>Container容器：JVM Process </li></ul></li>
<li>如何设计Spark Application运行在YARN上呢？？ <br>
<ul><li>Driver 运行在本地Client <br>
<ul><li>申请资源 <br>
driver -&gt; AppMaster -&gt; RM </li></ul></li>
<li>Driver 运行在集群上Cluster <br>
将AppMaster与Driver合并在一起 <br>
AppMaster(Driver) -&gt; RM</li></ul></li></ul></li>
</ul>



<h3 id="2企业中实际问题">2、企业中实际问题：</h3>

<p><strong>CDH 版本的HADOOP 框架使用的JDK 1.7，将Spark 2.x开发应用运行在YARN上，如何处理解决JDK 版本问题呢？？？</strong></p>

<p><strong>解决方案：</strong></p>

<ul>
<li>在提交应用运行的时候，指定Driver和Executor使用的JDK版本 <br>
<ul><li>针对AppMaster(Driver) <br>
<code>spark.yarn.appMasterEnv.JAVA_HOME=/opt/modules/jdk1.8.0_91</code></li>
<li>针对Executors <br>
<code>spark.executorEnv.JAVA_HOME=/opt/modules/jdk1.8.0_91</code></li></ul></li>
</ul>



<h2 id="十七spark-如何与hbase进行交互">十七、Spark 如何与HBase进行交互</h2>



<h3 id="1sparkcore如何读写hbase表中的数据">1、SparkCore如何读写HBase表中的数据？？？？</h3>

<p>RDD  &lt;-&gt; HFile（StoreFile）</p>

<p>返璞归真：</p>

<ul>
<li>SparkCore如何从HDFS上读取文本文件数据的？？ <br>
<code>sc.textFile("/datas/wordcount")</code> -&gt; hadoopFile <br>
<ul><li>底层：调用MapReduce框架中如何从HDFS上读取数据的API <br>
<ul><li>TextInputFormat <br>
<ul><li>LongWritable</li>
<li>Text</li></ul></li></ul></li></ul></li>
<li>SparkCore如何将RDD保存到HDFS文件系统上呢？？ <br>
<code>rdd.saveAsTextFile("/datas/xxx")</code> -&gt; saveAsHadoopFile <br>
<ul><li>底层： <br>
<ul><li>TextOutputFormat <br>
<ul><li>NullWritable</li>
<li>Text</li></ul></li></ul></li></ul></li>
<li>触发思考： <br>
<ul><li>Spark读写HBase表中的数据，本质：MapReduce从HBase表中读写数据的</li></ul></li>
</ul>



<h3 id="2mapreduce-读写hbase表中的数据使用底层类">2、MapReduce 读写HBase表中的数据，使用底层类</h3>

<ul>
<li>pom.xml加入依赖 <br>
<code>&lt;dependency&gt; <br>
    &lt;groupId&gt;org.apache.hbase&lt;/groupId&gt; <br>
    &lt;artifactId&gt;hbase-server&lt;/artifactId&gt; <br>
    &lt;version&gt;1.2.0-cdh5.7.6&lt;/version&gt; <br>
&lt;/dependency&gt; <br>
&lt;dependency&gt; <br>
    &lt;groupId&gt;org.apache.hbase&lt;/groupId&gt; <br>
    &lt;artifactId&gt;hbase-hadoop2-compat&lt;/artifactId&gt; <br>
    &lt;version&gt;1.2.0-cdh5.7.6&lt;/version&gt; <br>
&lt;/dependency&gt; <br>
&lt;dependency&gt; <br>
    &lt;groupId&gt;org.apache.hbase&lt;/groupId&gt; <br>
    &lt;artifactId&gt;hbase-client&lt;/artifactId&gt; <br>
    &lt;version&gt;1.2.0-cdh5.7.6&lt;/version&gt; <br>
&lt;/dependency&gt;</code></li>
</ul>



<h3 id="3读取数据">3、读取数据</h3>

<ul>
<li>一条一条的读取数据，每条数据变为KeyValue对 <br>
<ul><li>TableInputFormat <br>
<ul><li>ImmutableBytesWritable</li>
<li>Result</li></ul></li></ul></li>
<li>对于Spark来说： <br>
<code>def newAPIHadoopRDD[K, V, F &lt;: NewInputFormat[K, V]]( <br>
  conf: Configuration = hadoopConfiguration, <br>
  fClass: Class[F], <br>
  kClass: Class[K], <br>
  vClass: Class[V]): RDD[(K, V)]</code></li>
</ul>

<p>示例：</p>



<pre class="prettyprint"><code class="language-spark hljs scala"><span class="hljs-keyword">package</span> com.erongda.bigdata.spark.hbase

<span class="hljs-keyword">import</span> org.apache.hadoop.conf.Configuration
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.{CellUtil, HBaseConfiguration}
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.client.Result
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.io.ImmutableBytesWritable
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.mapreduce.TableInputFormat
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.util.Bytes
<span class="hljs-keyword">import</span> org.apache.spark.rdd.RDD
<span class="hljs-keyword">import</span> org.apache.spark.{SparkConf, SparkContext}

<span class="hljs-javadoc">/**
  * Spark Core从HBase表中读取数据：
  *     表的名称: ns1:sale_orders
  */</span>
<span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">ReadSaleOrdersSpark</span> {</span>

  <span class="hljs-keyword">def</span> main(args: Array[String]): Unit = {

    <span class="hljs-comment">// Spark Application运行时的相关配置信息，比如AppName, Master</span>
    <span class="hljs-keyword">val</span> sparkConf = <span class="hljs-keyword">new</span> SparkConf()
      .setAppName(<span class="hljs-string">"ReadSaleOrdersSpark"</span>)
      <span class="hljs-comment">// 设置应用运行在哪里，是本地模式还是集群（具体指定的地址）</span>
      .setMaster(<span class="hljs-string">"local[5]"</span>)

    <span class="hljs-javadoc">/**
      * 设置Spark Application 序列化方式使用Kryo
      *   默认情况下，对simple types, arrays of simple types, or string type 使用Kryo方式序列化
      */</span>
    sparkConf.set(<span class="hljs-string">"spark.serializer"</span>, <span class="hljs-string">"org.apache.spark.serializer.KryoSerializer"</span>)
    <span class="hljs-comment">// 告知哪些类型进行序列化</span>
    sparkConf.registerKryoClasses(Array(classOf[ImmutableBytesWritable], classOf[Result]))

    <span class="hljs-comment">// 创建SparkContext实例对象, 主要用于读取处理的数据，封装集合RDD中，调度Job</span>
    <span class="hljs-comment">// val sc = new SparkContext(sparkConf)</span>

    <span class="hljs-keyword">val</span> sc: SparkContext = SparkContext.getOrCreate(sparkConf)
    <span class="hljs-javadoc">/**
      * def newAPIHadoopRDD[K, V, F &lt;: NewInputFormat[K, V]](
          conf: Configuration = hadoopConfiguration,
          fClass: Class[F],
          kClass: Class[K],
          vClass: Class[V]
        ): RDD[(K, V)]
      */</span>
    <span class="hljs-comment">// a. 读取配置信息</span>
    <span class="hljs-keyword">val</span> conf: Configuration = HBaseConfiguration.create()

    <span class="hljs-comment">// b. 设置从HBase哪张表读取数据</span>
    conf.set(TableInputFormat.INPUT_TABLE, <span class="hljs-string">"ns1:sale_orders"</span>)

    <span class="hljs-comment">// c. 调用SparkContext中newAPIHadoopRDD读取HBase表中的数据</span>
    <span class="hljs-keyword">val</span> resultRDD: RDD[(ImmutableBytesWritable, Result)] = sc.newAPIHadoopRDD(
      conf, <span class="hljs-comment">// Configuration</span>
      classOf[TableInputFormat], <span class="hljs-comment">// storage format of the data to be read</span>
      classOf[ImmutableBytesWritable], <span class="hljs-comment">//</span>
      classOf[Result]
    )

    <span class="hljs-comment">// 测试获取的数据</span>
    println(s<span class="hljs-string">"Count = ${resultRDD.count()}"</span>)

    <span class="hljs-javadoc">/**
      * 当使用RDD.take(3).foreach() 报如下异常：ImmutableBytesWritable不能进行序列化
      *   java.io.NotSerializableException: org.apache.hadoop.hbase.io.ImmutableBytesWritable
      * Serialization stack:
      * 原因在于：
      *     RDD.take(N) 将数据从Executor中返回给Driver端，需要经过网络传输，所以需要对数据进行序列化，然而
      *   ImmutableBytesWritable 和 Result 类型都没有实现Java中序列化接口Serializable，所以出错。
      * 如何解决问题呢？？？？
      *   Spark 大数据分析计算框架，默认情况下使用Java Serializable对数据进行序列化，设置其他序列化方式。
      */</span>
    <span class="hljs-comment">//</span>
    resultRDD.take(<span class="hljs-number">3</span>).foreach{ <span class="hljs-keyword">case</span> (key, result) =&gt;
      <span class="hljs-comment">// 获取RowKey</span>
      <span class="hljs-keyword">val</span> rowKey = Bytes.toString(key.get)
      println(s<span class="hljs-string">"RowKey = $rowKey"</span>)

      <span class="hljs-comment">// 获取每条数据</span>
      <span class="hljs-keyword">for</span>(cell &lt;- result.rawCells()){
        <span class="hljs-comment">// 获取列簇</span>
        <span class="hljs-keyword">val</span> cf = Bytes.toString(CellUtil.cloneFamily(cell))
        <span class="hljs-comment">// 获取列</span>
        <span class="hljs-keyword">val</span> column = Bytes.toString(CellUtil.cloneQualifier(cell))
        <span class="hljs-comment">// 获取值</span>
        <span class="hljs-keyword">val</span> value = Bytes.toString(CellUtil.cloneValue(cell))
        <span class="hljs-comment">// 打印</span>
        println(s<span class="hljs-string">"\t $cf:$column = $value -&gt; ${cell.getTimestamp}"</span>)
      }
    }

    <span class="hljs-comment">// 为了开发测试，对每个Application运行做监控，所以当前线程休眠</span>
    Thread.sleep(<span class="hljs-number">10000000</span>)

    <span class="hljs-comment">// 关闭资源</span>
    sc.stop()
  }

}
</code></pre>



<h3 id="4写入数据">4、写入数据</h3>

<ul>
<li>TableOutputFormat <br>
<ul><li>ImmutableBytesWritable</li>
<li>Put</li></ul></li>
<li>对于Spark来说： <br>
<code>def saveAsNewAPIHadoopDataset(conf: Configuration): Unit</code></li>
</ul>

<p>示例：</p>



<pre class="prettyprint"><code class="language-spark hljs scala"><span class="hljs-keyword">package</span> com.erongda.bigdata.spark.hbase

<span class="hljs-keyword">import</span> org.apache.hadoop.conf.Configuration
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.HBaseConfiguration
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.client.Put
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.io.ImmutableBytesWritable
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.mapreduce.TableOutputFormat
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.util.Bytes
<span class="hljs-keyword">import</span> org.apache.spark.rdd.RDD
<span class="hljs-keyword">import</span> org.apache.spark.{SparkConf, SparkContext}

<span class="hljs-javadoc">/**
  * 将RDD数据存储到HBase表中，使用SparkCore中API完成
  */</span>
<span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">WriteDataToHBaseSpark</span> {</span>

  <span class="hljs-keyword">def</span> main(args: Array[String]): Unit = {

    <span class="hljs-comment">// Spark Application运行时的相关配置信息，比如AppName, Master</span>
    <span class="hljs-keyword">val</span> sparkConf = <span class="hljs-keyword">new</span> SparkConf()
      .setAppName(<span class="hljs-string">"SparkAppModule"</span>)
      <span class="hljs-comment">// 设置应用运行在哪里，是本地模式还是集群（具体指定的地址）</span>
      .setMaster(<span class="hljs-string">"local[2]"</span>)

    <span class="hljs-comment">// 创建SparkContext实例对象, 主要用于读取处理的数据，封装集合RDD中，调度Job</span>
    <span class="hljs-keyword">val</span> sc = <span class="hljs-keyword">new</span> SparkContext(sparkConf)


    <span class="hljs-javadoc">/**
      * 模拟数据
      *   将词频统计的结果RDD存储到HBase表中
      *   设计表：
      *      表的名称：ht_wordcount
      *      RowKey： word
      *      列簇：info
      *      列名：count
      */</span>
    <span class="hljs-comment">// 创建Scala中集合类列表 List</span>
    <span class="hljs-keyword">val</span> list = List((<span class="hljs-string">"hadoop"</span>, <span class="hljs-number">234</span>), (<span class="hljs-string">"spark"</span>, <span class="hljs-number">4356</span>), (<span class="hljs-string">"ml"</span>, <span class="hljs-number">456</span>), (<span class="hljs-string">"SQL"</span>, <span class="hljs-number">1235</span>))
    <span class="hljs-comment">// 通过并行化集合创建RDD</span>
    <span class="hljs-keyword">val</span> wordCountRDD: RDD[(String, Int)] = sc.parallelize(list, numSlices = <span class="hljs-number">1</span>)


    <span class="hljs-javadoc">/**
      * TableOutputFormat 向HBase表中写入数据，要求(Key, Value), 所以需要将RDD中的数据数据类型转换为
      *     RDD[(ImmutableBytesWritable, Put)]
      */</span>
    <span class="hljs-keyword">val</span> putsRDD: RDD[(ImmutableBytesWritable, Put)] = wordCountRDD
      <span class="hljs-comment">// 数据类型转换</span>
      .map{ <span class="hljs-keyword">case</span>(word, count) =&gt;
        <span class="hljs-comment">// RowKey</span>
        <span class="hljs-keyword">val</span> rowKey: ImmutableBytesWritable = <span class="hljs-keyword">new</span> ImmutableBytesWritable(Bytes.toBytes(word))
        <span class="hljs-comment">// 创建Put对象</span>
        <span class="hljs-keyword">val</span> put: Put = <span class="hljs-keyword">new</span> Put(rowKey.get())
        <span class="hljs-comment">// 增加列</span>
        put.addColumn(
          Bytes.toBytes(<span class="hljs-string">"info"</span>), <span class="hljs-comment">// cf</span>
          Bytes.toBytes(<span class="hljs-string">"count"</span>), <span class="hljs-comment">// column</span>
          Bytes.toBytes(count.toString)
        )
        <span class="hljs-comment">// 返回二元组</span>
        (rowKey, put)
      }

    <span class="hljs-comment">// TODO: 读取配置信息</span>
    <span class="hljs-keyword">val</span> conf: Configuration = HBaseConfiguration.create()


    <span class="hljs-comment">// a. 设置数据保存表的名称</span>
    conf.set(TableOutputFormat.OUTPUT_TABLE, <span class="hljs-string">"ht_wordcount"</span>)
    <span class="hljs-comment">// b.  设置OutputFormat</span>
    conf.set(<span class="hljs-string">"mapreduce.job.outputformat.class"</span>,
      <span class="hljs-string">"org.apache.hadoop.hbase.mapreduce.TableOutputFormat"</span>)
    <span class="hljs-comment">// c. 设置输出目录</span>
    conf.set(<span class="hljs-string">"mapreduce.output.fileoutputformat.outputdir"</span>,
      <span class="hljs-string">"/datas/spark/hbase/htwc-"</span> + System.currentTimeMillis())

    <span class="hljs-comment">// TODO: 调用RDD中方法，将数据保存到HBase表中</span>
    putsRDD.saveAsNewAPIHadoopDataset(conf)


    <span class="hljs-comment">// 为了开发测试，对每个Application运行做监控，所以当前线程休眠</span>
    Thread.sleep(<span class="hljs-number">10000000</span>)

    <span class="hljs-comment">// 关闭资源</span>
    sc.stop()
  }

}
</code></pre>



<h3 id="5对于mapreduce框架来说新旧api以hadoop-0200版本">5、对于MapReduce框架来说，新旧API，以hadoop-0.20.0版本</h3>

<ul>
<li>旧API <br>
Mapper、Reducer都是接口 <br>
<code>org.apache.hadoop.mapred</code></li>
<li>新API <br>
Mapper、Reducer都是类 <br>
<code>org.apache.hadoop.mapreduce</code></li>
</ul>



<h2 id="十八基于spark电商用户行为分析驴妈妈">十八、基于Spark电商用户行为分析（驴妈妈）</h2>



<h3 id="1基于hadoop生态系统框架离线数据分析">1、基于HADOOP生态系统框架离线数据分析</h3>

<ul>
<li>项目架构：分为三层架构 <br>
<ul><li>基于CDH 5.x版本HADOOP生态组件，8台机器</li></ul></li>
<li>数据收集 <br>
<ul><li>定制化收集数据，自定义SDK（Java SDK、JsSDK或Ios SDK、Android SDK）</li>
<li>SDK -&gt; Nginx -&gt; Flume -&gt; HDFS</li>
<li>每天一个日志文件，针对每台Nginx服务器，15GB</li></ul></li>
<li>数据处理 <br>
<ul><li>数据预处理 - 数据ETL  <br>
<ul><li>过滤清洗垃圾数据</li>
<li>转换数据格式</li>
<li>hdfs -&gt; mapreduce/spark -&gt; hbase</li></ul></li>
<li>数据分析 <br>
<ul><li>基于MR/Spark  分析  -&gt; 75% <br>
<ul><li>hbase -&gt; mr/spark -&gt; mysql</li></ul></li>
<li>基于Hive/SparkSQL分析  -&gt; 25% <br>
<ul><li>hbase -&gt; hive -&gt; HiveQL -&gt; Sqoop -&gt; MySQL</li>
<li>hbase -&gt; SparkSQL -&gt; MySQL</li></ul></li></ul></li></ul></li>
<li>分析结果展示 <br>
<ul><li>SSM + MySQL + Maven + HighCharts(ECharts)</li></ul></li>
</ul>



<h3 id="2hbase-数据库">2、HBase 数据库</h3>

<ul>
<li>基于HDFS之上NoSQL、面向列存储的、多版本的海量数据存的数据库 <br>
<ul><li>数十亿行数据 * 数百万列</li></ul></li>
<li>两个功能 <br>
<ul><li>存储数据 <br>
<ul><li>每条数据字段类型不一样，数据量很大</li></ul></li>
<li>检索数据 <br>
<ul><li>RowKey检索</li></ul></li></ul></li>
<li>关键点 <br>
<ul><li>RowKey 设计与业务</li></ul></li>
</ul>



<h3 id="3项目准备工作">3、项目准备工作</h3>

<ul>
<li>日志数据存储到HDFS <br>
<ul><li>HDFS 服务启动起来</li></ul></li>
<li>ETL数据存储到HBase表中 <br>
<ul><li>Zookeeper 启动</li>
<li>HBase 服务启动 <br>
<ul><li>Java API -&gt; 批量读数据、写数据到表中（MapReduce）</li></ul></li>
<li>游戏公司，往往使用Python语言开发代码 <br>
<ul><li>将游戏数据写入到HBase表中或从HBase表中读取数据</li>
<li>bin/hbase-daemon.sh start thrift</li></ul></li></ul></li>
<li>Spark 本地开发测试</li>
</ul>



<h3 id="4在spark程序运行中每个executor内存的使用">4、在Spark程序运行中每个Executor内存的使用</h3>

<p><a href="http://spark.apache.org/docs/2.2.0/tuning.html#memory-management-overview" rel="nofollow">http://spark.apache.org/docs/2.2.0/tuning.html#memory-management-overview</a></p>

<ul>
<li>内存：计算数据 <br>
<ul><li>Tasks运行需要内存</li></ul></li>
<li>内存：存数据 <br>
<ul><li>RDD缓存、广播变量缓存</li></ul></li>
</ul>

<blockquote>
  <p>比例的划分，在不同的应用运行的时候，适当考虑分配。</p>
</blockquote>

<p>比如： <br>
(默认情况下： 属于值的比例限定)</p>

<ul>
<li>计算数据的内存： <br>
<ul><li>spark.memory.fraction      = 0.6   -&gt; 6G</li></ul></li>
<li>存储数据的内存： <br>
<ul><li>spark.memory.storageFraction = 0.5  -&gt; 5G</li></ul></li>
</ul>

<p>代码（ETL）：</p>



<pre class="prettyprint"><code class="language-spark hljs scala">
<span class="hljs-keyword">package</span> com.erongda.bigdata.project.etl

<span class="hljs-keyword">import</span> java.util
<span class="hljs-keyword">import</span> java.util.zip.CRC32

<span class="hljs-keyword">import</span> com.erongda.bigdata.project.common.EventLogConstants
<span class="hljs-keyword">import</span> com.erongda.bigdata.project.common.EventLogConstants.EventEnum
<span class="hljs-keyword">import</span> com.erongda.bigdata.project.util.{LogParser, TimeUtil}
<span class="hljs-keyword">import</span> org.apache.commons.lang.StringUtils
<span class="hljs-keyword">import</span> org.apache.hadoop.conf.Configuration
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.{HBaseConfiguration, HColumnDescriptor, HTableDescriptor, TableName}
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.client.{Connection, ConnectionFactory, HBaseAdmin, Put}
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.io.ImmutableBytesWritable
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.io.compress.Compression
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.mapreduce.TableOutputFormat
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.util.Bytes
<span class="hljs-keyword">import</span> org.apache.spark.broadcast.Broadcast
<span class="hljs-keyword">import</span> org.apache.spark.rdd.RDD
<span class="hljs-keyword">import</span> org.apache.spark.{SparkConf, SparkContext}

<span class="hljs-javadoc">/**
  * 基于Spark框架读取HDFS上日志文件数据，进行ETL操作，最终将数据插入到HBase表中
  *   -1. 为什么选择ETL数据到HBase表中？？
  *     采集的数据包含很多Event类型的数据，不同Event时间类型的数据字段不一样，数据量相当比较大
  *   -2. HBase 表的设计？？？
  *     -a. 每天的日志数据，ETL到一张表中
  *         本系统，主要针对日志数据进行分析的，基本上每天的数据分析一次，为了分析更快，加载更少的数据
  *     -b. 每次ETL数据的时候，创建一张表
  *         -i. 创建表的预分区，使用的数据在不同Region中，减少些热点，避免Region Split
  *         -ii. 可以考虑表中数据的压缩，使用snappy压缩或lz4压缩
  *     -v. RowKey设计原则：
  *         - 唯一性（不重复）
  *         - 结合业务考虑
  *           某个EventType数据的分析，某个时间段数据的分析
  *         RowKey = 采用服务器时间戳  +  crc32(用户ID、会员ID、事件名称)
  */</span>
<span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">EtlToHBaseSpark</span> {</span>


  <span class="hljs-javadoc">/**
    * RowKey 创建
    * <span class="hljs-javadoctag">@param</span> time
    *             服务器时间
    * <span class="hljs-javadoctag">@param</span> uUID
    *             用户ID
    * <span class="hljs-javadoctag">@param</span> uMD
    *            用户会员ID
    * <span class="hljs-javadoctag">@param</span> eventAlias
    *                   事件Event名称
    * <span class="hljs-javadoctag">@return</span>
    */</span>
  <span class="hljs-keyword">def</span> createRowKey(time: Long, uUID: String, uMD: String, eventAlias: String): String = {
    <span class="hljs-comment">// 创建StringBuilder实例对象，用于拼接字符串</span>
    <span class="hljs-keyword">val</span> sBuilder = <span class="hljs-keyword">new</span> StringBuilder()
    sBuilder.append(time + <span class="hljs-string">"_"</span>)

    <span class="hljs-comment">// 创建CRC32实例对象，进行字符串编码，将字符串转换为Logn类型数字</span>
    <span class="hljs-keyword">val</span> crc32 = <span class="hljs-keyword">new</span> CRC32()
    <span class="hljs-comment">// 重置</span>
    crc32.reset()
    <span class="hljs-keyword">if</span>(StringUtils.isNotBlank(uUID)){
      crc32.update(Bytes.toBytes(uUID))
    }
    <span class="hljs-keyword">if</span>(StringUtils.isNotBlank(uMD)){
      crc32.update(Bytes.toBytes(uMD))
    }
    <span class="hljs-keyword">if</span>(StringUtils.isNotBlank(eventAlias)){
      crc32.update(Bytes.toBytes(eventAlias))
    }

    sBuilder.append(crc32.getValue % <span class="hljs-number">100000000</span>L)
    <span class="hljs-comment">// return</span>
    sBuilder.toString()
  }

  <span class="hljs-javadoc">/**
    * 创建HBase表，创建的时候判断是否已经存在，存在的话先删除后创建
    * <span class="hljs-javadoctag">@param</span> processDate
    *                    要处理哪天数据的日期，格式：2018-07-22
    * <span class="hljs-javadoctag">@param</span> conf
    *             HBase Client 要读取的配置信息
    * <span class="hljs-javadoctag">@return</span>
    *         表的名称
    */</span>
  <span class="hljs-keyword">def</span> createHBaseTable(processDate: String, conf: Configuration): String = {
    <span class="hljs-comment">// create 'event_logs20151220', 'info'</span>
    <span class="hljs-keyword">val</span> time = TimeUtil.parseString2Long(processDate)
    <span class="hljs-keyword">val</span> date = TimeUtil.parseLong2String(time, <span class="hljs-string">"yyyyMMdd"</span>)

    <span class="hljs-comment">// table name</span>
    <span class="hljs-keyword">val</span> tableName = EventLogConstants.HBASE_NAME_EVENT_LOGS + date

    <span class="hljs-comment">// 创建表，先判断是否存在</span>
    <span class="hljs-keyword">var</span> conn: Connection = <span class="hljs-keyword">null</span>
    <span class="hljs-keyword">var</span> admin: HBaseAdmin = <span class="hljs-keyword">null</span>

    <span class="hljs-keyword">try</span>{
      <span class="hljs-comment">// 获取连接</span>
      conn = ConnectionFactory.createConnection(conf)
      <span class="hljs-comment">// 获取HBaseAdmin实例对象</span>
      admin = conn.getAdmin.asInstanceOf[HBaseAdmin]

      <span class="hljs-comment">// 判断表是否存在</span>
      <span class="hljs-keyword">if</span>(admin.tableExists(tableName)){
        <span class="hljs-comment">// 表存在，先禁用，后删除</span>
        admin.disableTable(tableName)
        admin.deleteTable(tableName)
      }

      <span class="hljs-comment">// 创建TableDesc描述符</span>
      <span class="hljs-keyword">val</span> desc = <span class="hljs-keyword">new</span> HTableDescriptor(TableName.valueOf(tableName))
      <span class="hljs-comment">// 创建表的列簇描述符</span>
      <span class="hljs-keyword">val</span> familyDesc = <span class="hljs-keyword">new</span> HColumnDescriptor(EventLogConstants.BYTES_EVENT_LOGS_FAMILY_NAME)

      <span class="hljs-javadoc">/**
        * 针对列簇设置属性值
        */</span>
      <span class="hljs-comment">// 设置数据压缩</span>
      familyDesc.setCompressionType(Compression.Algorithm.SNAPPY)
      <span class="hljs-comment">// 设置读取数据不缓存</span>
      familyDesc.setBlockCacheEnabled(<span class="hljs-keyword">false</span>)
      <span class="hljs-comment">// 向表中添加列簇</span>
      desc.addFamily(familyDesc)

      <span class="hljs-comment">// 设置表的预分区，针对整个表来说的，不是针对某个列簇</span>
      <span class="hljs-comment">// TODO: createTable(desc: HTableDescriptor, splitKeys: Array[Array[Byte]])</span>
      admin.createTable(desc,
        Array(
          Bytes.toBytes(<span class="hljs-string">"1450570500000_"</span>), Bytes.toBytes(<span class="hljs-string">"1450571100000_"</span>),
          Bytes.toBytes(<span class="hljs-string">"1450571700000_"</span>)
        )
      )
    }<span class="hljs-keyword">catch</span> {
      <span class="hljs-keyword">case</span> e: Exception =&gt; e.printStackTrace()
    }<span class="hljs-keyword">finally</span> {
      <span class="hljs-keyword">if</span>(<span class="hljs-keyword">null</span> != admin) admin.close()
      <span class="hljs-keyword">if</span>(<span class="hljs-keyword">null</span> != conn) conn.close()
    }
    <span class="hljs-comment">// 返回表的名称</span>
    tableName
  }

  <span class="hljs-javadoc">/**
    * Spark Application 运行的入口，就是Driver Program
    * <span class="hljs-javadoctag">@param</span> args
    *             程序的参数，实际业务需要传递 处理哪天你的数据(processDate)
    */</span>
  <span class="hljs-keyword">def</span> main(args: Array[String]): Unit = {

    <span class="hljs-comment">// 需求传递一个参数，表明ETL处理的数据时哪一天的</span>
    <span class="hljs-keyword">if</span>(args.length &lt; <span class="hljs-number">1</span>){
      println(<span class="hljs-string">"Usage: EtlToHBaseSpark process_date"</span>)
      System.exit(<span class="hljs-number">1</span>)
    }

    <span class="hljs-javadoc">/**
      * 1. 创建SparkContext实例对象，读取数据，调度Job
      */</span>
    <span class="hljs-keyword">val</span> sparkConf = <span class="hljs-keyword">new</span> SparkConf()
      .setMaster(<span class="hljs-string">"local[3]"</span>).setAppName(<span class="hljs-string">"EtlToHBaseSpark Application"</span>)
      <span class="hljs-comment">// 设置使用kryo序列化</span>
      .set(<span class="hljs-string">"spark.serializer"</span>, <span class="hljs-string">"org.apache.spark.serializer.KryoSerializer"</span>)
      <span class="hljs-comment">// 告知序列化数据类型，看RDD中数据类型，除了simple types, arrays of simple types, or string type</span>
      .registerKryoClasses(Array(classOf[ImmutableBytesWritable], classOf[Put]))

    <span class="hljs-comment">// 创建SparkContext</span>
    <span class="hljs-keyword">val</span> sc = SparkContext.getOrCreate(sparkConf)
    sc.setLogLevel(<span class="hljs-string">"WARN"</span>)


    <span class="hljs-javadoc">/**
      * TODO: a. 读取日志数据，从本地读取
      */</span>
    <span class="hljs-keyword">val</span> eventLogsRDD: RDD[String] = sc
      .textFile(<span class="hljs-string">"file:///C:/spark-learning/datas/20151220.log"</span>, minPartitions = <span class="hljs-number">3</span>)

    <span class="hljs-comment">// println(s"Count = ${eventLogsRDD.count()}")</span>
    <span class="hljs-comment">// println(eventLogsRDD.first())</span>

    <span class="hljs-javadoc">/**
      * TODO：b. 解析每条日志数据
      */</span>
    <span class="hljs-keyword">val</span> parseEventLogsRDD: RDD[(String, util.Map[String, String])] = eventLogsRDD
      <span class="hljs-comment">// 通过解析工具类解析每条数据</span>
      .map(line =&gt; {
        <span class="hljs-comment">// 调用工具类进行解析得到Map集合</span>
        <span class="hljs-keyword">val</span> logInfo: util.Map[String, String] = <span class="hljs-keyword">new</span> LogParser().handleLogParser(line)
        <span class="hljs-comment">// 获取事件的类型</span>
        <span class="hljs-keyword">val</span> eventAlias = logInfo.get(EventLogConstants.LOG_COLUMN_NAME_EVENT_NAME)
        <span class="hljs-comment">// 以二元组的形式返回</span>
        (eventAlias, logInfo)
      })
    <span class="hljs-comment">// println(parseEventLogsRDD.first())</span>

    <span class="hljs-comment">// 存储事件EventType类型</span>
    <span class="hljs-keyword">val</span> eventTypeList = List(EventEnum.LAUNCH, EventEnum.PAGEVIEW, EventEnum.CHARGEREQUEST,
      EventEnum.CHARGESUCCESS, EventEnum.CHARGEREFUND, EventEnum.EVENT)
    <span class="hljs-comment">// TODO: 定义广播变量，将事件类型列表广播出去，广播给所有Executor</span>
    <span class="hljs-keyword">val</span> eventTypeListBroadcast: Broadcast[List[EventEnum]] = sc.broadcast(eventTypeList)

    <span class="hljs-javadoc">/**
      * TODO：c. 过滤数据
      */</span>
    <span class="hljs-keyword">val</span> eventPutsRDD: RDD[(ImmutableBytesWritable, Put)] = parseEventLogsRDD
      <span class="hljs-comment">// 过滤条件：解析Map集合不能为空; 事件类型EventType必须存在</span>
      <span class="hljs-comment">// TODO: 性能优化点：将集合列表 拷贝到每个Executor中一份数据，而不是每个Task中一份数据</span>
      .filter{ <span class="hljs-keyword">case</span>(eventAlias, logInfo) =&gt;
        <span class="hljs-comment">// logInfo.size() != 0 &amp;&amp; eventTypeList.contains(EventEnum.valueOfAlias(eventAlias))</span>
        logInfo.size() != <span class="hljs-number">0</span> &amp;&amp; eventTypeListBroadcast.value.contains(EventEnum.valueOfAlias(eventAlias))
      }
      <span class="hljs-comment">// 数据转换，准备RDD数据库，将数据保存到HBASE表中RDD[(ImmtableBytesWritable, Put)]</span>
      .map{ <span class="hljs-keyword">case</span>(eventAlias, logInfo) =&gt;
        <span class="hljs-comment">// -i. RowKey 表的主键</span>
        <span class="hljs-keyword">val</span> rowKey = createRowKey(
          TimeUtil.parseNginxServerTime2Long(logInfo.get(EventLogConstants.LOG_COLUMN_NAME_SERVER_TIME)),
          logInfo.get(EventLogConstants.LOG_COLUMN_NAME_UUID), <span class="hljs-comment">// 用户ID</span>
          logInfo.get(EventLogConstants.LOG_COLUMN_NAME_MEMBER_ID), <span class="hljs-comment">// 会员ID</span>
          eventAlias <span class="hljs-comment">// 事件类型别名</span>
        )

        <span class="hljs-comment">// -ii. 创建Put对象</span>
        <span class="hljs-keyword">val</span> put = <span class="hljs-keyword">new</span> Put(Bytes.toBytes(rowKey))
        <span class="hljs-comment">// add columns</span>
        <span class="hljs-comment">// TODO:  注意此处需要将Java中Map集合转换为Scala中Map集合，方能进行操作</span>
        <span class="hljs-keyword">import</span> scala.collection.JavaConverters._
        <span class="hljs-keyword">for</span>((key, value) &lt;- logInfo.asScala){
          put.addColumn(
            EventLogConstants.BYTES_EVENT_LOGS_FAMILY_NAME , <span class="hljs-comment">// cf</span>
            Bytes.toBytes(key), <span class="hljs-comment">// column</span>
            Bytes.toBytes(value)
          )
        }

        <span class="hljs-comment">// iii. 返回二元组</span>
        (<span class="hljs-keyword">new</span> ImmutableBytesWritable(put.getRow), put)
    }

    <span class="hljs-javadoc">/**
      * TODO: d. 将RDD保存到HBase表中
      */</span>
    <span class="hljs-comment">// d.1 获取配置信息, 需要将hbase-site.xml放入CLASSPATH下面</span>
    <span class="hljs-keyword">val</span> conf = HBaseConfiguration.create()

    <span class="hljs-javadoc">/**
      * 由于ETL每天执行一次（ETL失败，再次执行），对原始的数据进行处理，将每天的数据存储HBase表中
      *     表的名称：
      *       create 'event_logs20151220', 'info'
      */</span>
    <span class="hljs-keyword">val</span> tableName = createHBaseTable(args(<span class="hljs-number">0</span>), conf)

    <span class="hljs-comment">// d.2 设置表的名称</span>
    conf.set(TableOutputFormat.OUTPUT_TABLE, tableName)

    <span class="hljs-comment">// d.3 设置OutputFort</span>
    conf.set(<span class="hljs-string">"mapreduce.job.outputformat.class"</span>,
      <span class="hljs-string">"org.apache.hadoop.hbase.mapreduce.TableOutputFormat"</span>)

    <span class="hljs-comment">// d.4. 设置输出目录</span>
    conf.set(<span class="hljs-string">"mapreduce.output.fileoutputformat.outputdir"</span>,
      <span class="hljs-string">"/datas/spark/hbase/etl-"</span> + System.currentTimeMillis())

    <span class="hljs-javadoc">/**
      * TODO: 真正的保存数据到HBase表中
      */</span>
    eventPutsRDD.saveAsNewAPIHadoopDataset(conf)

    <span class="hljs-comment">// println(eventPutsRDD.count())</span>
    <span class="hljs-comment">// println(eventPutsRDD.first())</span>

    <span class="hljs-comment">// 为了开发测试，线程休眠, WEB UI监控查看</span>
    Thread.sleep(<span class="hljs-number">1000000</span>)

    <span class="hljs-comment">// 关闭资源</span>
    sc.stop()
  }
}
</code></pre>



<h3 id="5etl-日志数据到hbase表中程序代码优化点">5、ETL 日志数据到HBase表中，程序代码优化点</h3>

<ul>
<li>创建表的时候 <br>
<ul><li>设置表的数据压缩</li>
<li>创建预分区 <br>
<ul><li>最好分区数多一点，均匀一点，减少Region分割</li></ul></li>
<li>设置读取表中的数据不缓存 <br>
<ul><li>cache block<code>familyDesc.setBlockCacheEnabled(false)</code></li></ul></li></ul></li>
<li>事件类型EventType过滤优化 <br>
<ul><li>使用广播变量 ，将集合列表广播出去 ，将数据发送到每个Executor一份，而不是每个Task一份数据</li>
<li><code>val eventTypeListBroadcast: Broadcast[List[EventEnum]] = sc.broadcast(eventTypeList)</code></li>
<li><code>eventTypeListBroadcast.value</code></li></ul></li>
<li>使用HFileOutputFormat <br>
<ul><li>向HBase表中存储数据的时候，方式 <br>
<ul><li>Put方式 <br>
<ul><li>PutData -&gt; WAL -&gt; MemStore -&gt; StoreFile(HFile)</li></ul></li>
<li>HFile方式 <br>
<ul><li>Data -&gt; HFile -&gt; Load Table</li></ul></li></ul></li></ul></li>
</ul>



<h3 id="6分析需求">6、分析需求</h3>

<p><strong>新增用户 统计分析</strong></p>

<ul>
<li>第一次访问网站（这一天）就是一个新增用户  <br>
<ul><li>触发一个事件Event： launch事件 en=e_l</li></ul></li>
</ul>

<p><strong>分析指标：需要结合维度来分析</strong></p>

<ul>
<li>时间维度 <br>
<ul><li>每天进行统计</li></ul></li>
<li>平台维度 <br>
<ul><li>浏览网站所使用的的平台(网站、iOS APP，Android App等)</li></ul></li>
<li>浏览器维度 <br>
<ul><li>使用浏览器的类型</li></ul></li>
<li>定义维度 <br>
<ul><li>基本维度分析 <br>
<ul><li>时间维度 + 平台维度 </li></ul></li>
<li>基本维度 + 浏览器维度 <br>
<ul><li>时间维度 + 平台维度 + 浏览器维度 </li></ul></li></ul></li>
</ul>



<h3 id="7shared-variables-共享变量">7、shared variables （共享变量）</h3>

<ul>
<li>broadcast variables <br>
<ul><li>广播变量，变量的值是不可变，被存储到Executor中，以便Task使用的。</li></ul></li>
<li>accumulators <br>
<ul><li>累加器：类似MapReduce程序中的计数器Counters，此变量的值只能增加，不能其他操作</li></ul></li>
</ul>

<p>累加器示例：</p>



<pre class="prettyprint"><code class="language-spark hljs avrasm">val accum = sc<span class="hljs-preprocessor">.longAccumulator</span>(<span class="hljs-string">"Input Lines Accumulator"</span>)
val inputRDD = sc<span class="hljs-preprocessor">.textFile</span>(<span class="hljs-string">"/datas/wordcount.data"</span>)
val filterRDD = inputRDD<span class="hljs-preprocessor">.filter</span>(line =&gt; {
    accum<span class="hljs-preprocessor">.add</span>(<span class="hljs-number">1</span>L)
    line<span class="hljs-preprocessor">.trim</span><span class="hljs-preprocessor">.length</span> &gt; <span class="hljs-number">0</span>
})
filterRDD<span class="hljs-preprocessor">.count</span>()</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>