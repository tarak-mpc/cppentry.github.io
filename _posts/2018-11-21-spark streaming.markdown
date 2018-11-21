---
layout:     post
title:      spark streaming
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1 style="margin-left:21.25pt;">Spark Streaming</h1>

<ol><li><a name="_Toc10262"> 课程目标</a>

	<ol><li>掌握Spark Streaming的原理</li>
		<li>熟练使用Spark Streaming完成流式计算任务</li>
	</ol></li>
	<li>Spark Streaming介绍
	<ol><li>Spark Streaming概述
		<ol><li>什么是Spark Streaming</li>
		</ol></li>
	</ol></li>
</ol><p style="margin-left:0cm;"><img alt="" class="has" height="189" src="https://img-blog.csdn.net/20180801125100593?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI1NDYwMjI3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="554"></p>

<p style="margin-left:0cm;">Spark Streaming类似于Apache Storm，用于流式数据的处理。根据其官方文档介绍，Spark Streaming有高吞吐量和容错能力强等特点。Spark Streaming支持的数据输入源很多，例如：Kafka、Flume、Twitter、ZeroMQ和简单的TCP套接字等等。数据输入后可以用Spark的高度抽象原语如：map、reduce、join、window等进行运算。而结果也能保存在很多地方，如HDFS，数据库等。另外Spark Streaming也能和MLlib（机器学习）以及Graphx完美融合。</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="207" src="https://img-blog.csdn.net/20180801125107997?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI1NDYwMjI3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="554"></p>

<ol><li>
	<ol><li>
		<ol><li>为什么要学习Spark Streaming</li>
		</ol></li>
	</ol></li>
</ol><p style="margin-left:0cm;"> </p>

<ol><li>易用</li>
</ol><p style="margin-left:0cm;"><img alt="" class="has" height="126" src="https://img-blog.csdn.net/20180801125114752?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI1NDYwMjI3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="554"></p>

<ol><li>容错</li>
</ol><p style="margin-left:0cm;"><img alt="" class="has" height="135" src="https://img-blog.csdn.net/20180801125123445?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI1NDYwMjI3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="554"></p>

<ol><li>易整合到Spark体系</li>
</ol><p style="margin-left:0cm;"><img alt="" class="has" height="137" src="https://img-blog.csdn.net/20180801125130590?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI1NDYwMjI3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="553"></p>

<ol><li>
	<ol><li>
		<ol><li>Spark与Storm的对比</li>
		</ol></li>
	</ol></li>
</ol><table border="1" cellspacing="0"><tbody><tr><td style="vertical-align:top;width:213.05pt;">
			<p style="margin-left:0cm;"><strong>Spark</strong></p>
			</td>
			<td style="vertical-align:top;width:213.05pt;">
			<p style="margin-left:0cm;"><strong>Storm</strong></p>
			</td>
		</tr><tr><td style="vertical-align:top;width:213.05pt;">
			<p style="margin-left:0cm;"><img alt="" class="has" height="152" src="https://img-blog.csdn.net/2018080112514584?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI1NDYwMjI3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="269"></p>
			</td>
			<td style="vertical-align:top;width:213.05pt;">
			<p style="margin-left:0cm;"><img alt="" class="has" height="151" src="https://img-blog.csdn.net/20180801125149844?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI1NDYwMjI3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="269"></p>
			</td>
		</tr><tr><td style="vertical-align:top;width:213.05pt;">
			<p style="margin-left:0cm;">开发语言：Scala</p>
			</td>
			<td style="vertical-align:top;width:213.05pt;">
			<p style="margin-left:0cm;">开发语言：Clojure</p>
			</td>
		</tr><tr><td style="vertical-align:top;width:213.05pt;">
			<p style="margin-left:0cm;">编程模型：DStream</p>
			</td>
			<td style="vertical-align:top;width:213.05pt;">
			<p style="margin-left:0cm;">编程模型：Spout/Bolt</p>
			</td>
		</tr><tr><td style="vertical-align:top;width:213.05pt;">
			<p style="margin-left:0cm;"><img alt="" class="has" height="212" src="https://img-blog.csdn.net/20180801125156296?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI1NDYwMjI3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="270"></p>
			</td>
			<td style="vertical-align:top;width:213.05pt;">
			<p style="margin-left:0cm;"><img alt="" class="has" height="220" src="https://img-blog.csdn.net/20180801125201311?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI1NDYwMjI3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="270"></p>
			</td>
		</tr></tbody></table><p style="margin-left:0cm;"> </p>

<ol><li>DStream
	<ol><li>什么是DStream</li>
	</ol></li>
</ol><p style="margin-left:0cm;">Discretized Stream是Spark Streaming的基础抽象，代表持续性的数据流和经过各种Spark原语操作后的结果数据流。在内部实现上，<span style="color:#ff0000;">DStream</span><span style="color:#ff0000;">是一系列连续的</span><span style="color:#ff0000;">RDD</span><span style="color:#ff0000;">来表示</span>。每个RDD含有一段时间间隔内的数据，如下图：</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="121" src="https://img-blog.csdn.net/20180801125210465?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI1NDYwMjI3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="554"></p>

<p style="margin-left:0cm;">对数据的操作也是按照RDD为单位来进行的</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="197" src="https://img-blog.csdn.net/20180801125214794?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI1NDYwMjI3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="554"></p>

<p style="margin-left:0cm;">计算过程由Spark engine来完成</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="123" src="https://img-blog.csdn.net/20180801125219240?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI1NDYwMjI3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="553"></p>

<ol><li>
	<ol><li>DStream相关操作</li>
	</ol></li>
</ol><p style="margin-left:0cm;">DStream上的原语与RDD的类似，分为Transformations（转换）和Output Operations（输出）两种，此外转换操作中还有一些比较特殊的原语，如：updateStateByKey()、transform()以及各种Window相关的原语。</p>

<p style="margin-left:0cm;"> </p>

<ol><li>
	<ol><li>
		<ol><li>Transformations on DStreams</li>
		</ol></li>
	</ol></li>
</ol><table border="1" cellspacing="0"><tbody><tr><td style="vertical-align:top;width:160.55pt;">
			<p style="margin-left:0cm;"><strong>Transformation</strong></p>
			</td>
			<td style="vertical-align:top;width:265.55pt;">
			<p style="margin-left:0cm;"><strong>Meaning</strong></p>
			</td>
		</tr><tr><td style="vertical-align:top;width:160.55pt;">
			<p style="margin-left:0cm;">map(func)</p>
			</td>
			<td style="vertical-align:top;width:265.55pt;">
			<p style="margin-left:0cm;">Return a new DStream by passing each element of the source DStream through a function func.</p>
			</td>
		</tr><tr><td style="vertical-align:top;width:160.55pt;">
			<p style="margin-left:0cm;">flatMap(func)</p>
			</td>
			<td style="vertical-align:top;width:265.55pt;">
			<p style="margin-left:0cm;">Similar to map, but each input item can be mapped to 0 or more output items.</p>
			</td>
		</tr><tr><td style="vertical-align:top;width:160.55pt;">
			<p style="margin-left:0cm;">filter(func)</p>
			</td>
			<td style="vertical-align:top;width:265.55pt;">
			<p style="margin-left:0cm;">Return a new DStream by selecting only the records of the source DStream on which func returns true.</p>
			</td>
		</tr><tr><td style="vertical-align:top;width:160.55pt;">
			<p style="margin-left:0cm;">repartition(numPartitions)</p>
			</td>
			<td style="vertical-align:top;width:265.55pt;">
			<p style="margin-left:0cm;">Changes the level of parallelism in this DStream by creating more or fewer partitions.</p>
			</td>
		</tr><tr><td style="vertical-align:top;width:160.55pt;">
			<p style="margin-left:0cm;">union(otherStream)</p>
			</td>
			<td style="vertical-align:top;width:265.55pt;">
			<p style="margin-left:0cm;">Return a new DStream that contains the union of the elements in the source DStream and otherDStream.</p>
			</td>
		</tr><tr><td style="vertical-align:top;width:160.55pt;">
			<p style="margin-left:0cm;">count()</p>
			</td>
			<td style="vertical-align:top;width:265.55pt;">
			<p style="margin-left:0cm;">Return a new DStream of single-element RDDs by counting the number of elements in each RDD of the source DStream.</p>
			</td>
		</tr><tr><td style="vertical-align:top;width:160.55pt;">
			<p style="margin-left:0cm;">reduce(func)</p>
			</td>
			<td style="vertical-align:top;width:265.55pt;">
			<p style="margin-left:0cm;">Return a new DStream of single-element RDDs by aggregating the elements in each RDD of the source DStream using a function func (which takes two arguments and returns one). The function should be associative so that it can be computed in parallel.</p>
			</td>
		</tr><tr><td style="vertical-align:top;width:160.55pt;">
			<p style="margin-left:0cm;">countByValue()</p>
			</td>
			<td style="vertical-align:top;width:265.55pt;">
			<p style="margin-left:0cm;">When called on a DStream of elements of type K, return a new DStream of (K, Long) pairs where the value of each key is its frequency in each RDD of the source DStream.</p>
			</td>
		</tr><tr><td style="vertical-align:top;width:160.55pt;">
			<p style="margin-left:0cm;">reduceByKey(func, [numTasks])   </p>
			</td>
			<td style="vertical-align:top;width:265.55pt;">
			<p style="margin-left:0cm;">When called on a DStream of (K, V) pairs, return a new DStream of (K, V) pairs where the values for each key are aggregated using the given reduce function. Note: By default, this uses Spark's default number of parallel tasks (2 for local mode, and in cluster mode the number is determined by the config property spark.default.parallelism) to do the grouping. You can pass an optional numTasks argument to set a different number of tasks.</p>
			</td>
		</tr><tr><td style="vertical-align:top;width:160.55pt;">
			<p style="margin-left:0cm;">join(otherStream, [numTasks])</p>
			</td>
			<td style="vertical-align:top;width:265.55pt;">
			<p style="margin-left:0cm;">When called on two DStreams of (K, V) and (K, W) pairs, return a new DStream of (K, (V, W)) pairs with all pairs of elements for each key.</p>
			</td>
		</tr><tr><td style="vertical-align:top;width:160.55pt;">
			<p style="margin-left:0cm;">cogroup(otherStream, [numTasks])</p>
			</td>
			<td style="vertical-align:top;width:265.55pt;">
			<p style="margin-left:0cm;">When called on a DStream of (K, V) and (K, W) pairs, return a new DStream of (K, Seq[V], Seq[W]) tuples.</p>
			</td>
		</tr><tr><td style="vertical-align:top;width:160.55pt;">
			<p style="margin-left:0cm;">transform(func)     </p>
			</td>
			<td style="vertical-align:top;width:265.55pt;">
			<p style="margin-left:0cm;">Return a new DStream by applying a RDD-to-RDD function to every RDD of the source DStream. This can be used to do arbitrary RDD operations on the DStream.</p>
			</td>
		</tr><tr><td style="vertical-align:top;width:160.55pt;">
			<p style="margin-left:0cm;">updateStateByKey(func)</p>
			</td>
			<td style="vertical-align:top;width:265.55pt;">
			<p style="margin-left:0cm;">Return a new "state" DStream where the state for each key is updated by applying the given function on the previous state of the key and the new values for the key. This can be used to maintain arbitrary state data for each key.</p>
			</td>
		</tr></tbody></table><p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;"><strong>特殊的Transformations</strong></p>

<p style="margin-left:0cm;"> </p>

<ol><li><strong>UpdateStateByKey Operation</strong></li>
</ol><p style="margin-left:0cm;">UpdateStateByKey原语用于记录历史记录，上文中Word Count示例中就用到了该特性。若不用UpdateStateByKey来更新状态，那么每次数据进来后分析完成后，结果输出后将不再保存</p>

<p style="margin-left:0cm;"> </p>

<ol><li><strong>Transform Operation</strong></li>
</ol><p style="margin-left:0cm;">Transform原语允许DStream上执行任意的RDD-to-RDD函数。通过该函数可以方便的扩展Spark API。此外，MLlib（机器学习）以及Graphx也是通过本函数来进行结合的。</p>

<p style="margin-left:0cm;"> </p>

<ol><li><strong>Window Operations</strong></li>
</ol><p style="margin-left:0cm;">Window Operations有点类似于Storm中的State，可以设置窗口的大小和滑动窗口的间隔来动态的获取当前Steaming的允许状态</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="216" src="https://img-blog.csdn.net/20180801125242747?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI1NDYwMjI3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="553"></p>

<ol><li>
	<ol><li>
		<ol><li>Output Operations on DStreams</li>
		</ol></li>
	</ol></li>
</ol><p style="margin-left:0cm;">Output Operations可以将DStream的数据输出到外部的数据库或文件系统，当某个Output Operations原语被调用时（与RDD的Action相同），streaming程序才会开始真正的计算过程。</p>

<table border="1" cellspacing="0"><tbody><tr><td style="vertical-align:top;width:165.05pt;">
			<p style="margin-left:0cm;">Output Operation</p>
			</td>
			<td style="vertical-align:top;width:261.05pt;">
			<p style="margin-left:0cm;">Meaning</p>
			</td>
		</tr><tr><td style="vertical-align:top;width:165.05pt;">
			<p style="margin-left:0cm;">print()</p>
			</td>
			<td style="vertical-align:top;width:261.05pt;">
			<p style="margin-left:0cm;">Prints the first ten elements of every batch of data in a DStream on the driver node running the streaming application. This is useful for development and debugging.</p>
			</td>
		</tr><tr><td style="vertical-align:top;width:165.05pt;">
			<p style="margin-left:0cm;">saveAsTextFiles(prefix, [suffix])</p>
			</td>
			<td style="vertical-align:top;width:261.05pt;">
			<p style="margin-left:0cm;">Save this DStream's contents as text files. The file name at each batch interval is generated based on prefix and suffix: "prefix-TIME_IN_MS[.suffix]".</p>
			</td>
		</tr><tr><td style="vertical-align:top;width:165.05pt;">
			<p style="margin-left:0cm;">saveAsObjectFiles(prefix, [suffix])</p>
			</td>
			<td style="vertical-align:top;width:261.05pt;">
			<p style="margin-left:0cm;">Save this DStream's contents as SequenceFiles of serialized Java objects. The file name at each batch interval is generated based on prefix and suffix: "prefix-TIME_IN_MS[.suffix]".</p>
			</td>
		</tr><tr><td style="vertical-align:top;width:165.05pt;">
			<p style="margin-left:0cm;">saveAsHadoopFiles(prefix, [suffix])</p>
			</td>
			<td style="vertical-align:top;width:261.05pt;">
			<p style="margin-left:0cm;">Save this DStream's contents as Hadoop files. The file name at each batch interval is generated based on prefix and suffix: "prefix-TIME_IN_MS[.suffix]".</p>
			</td>
		</tr><tr><td style="vertical-align:top;width:165.05pt;">
			<p style="margin-left:0cm;">foreachRDD(func)</p>
			</td>
			<td style="vertical-align:top;width:261.05pt;">
			<p style="margin-left:0cm;">The most generic output operator that applies a function, func, to each RDD generated from the stream. This function should push the data in each RDD to an external system, such as saving the RDD to files, or writing it over the network to a database. Note that the function func is executed in the driver process running the streaming application, and will usually have RDD actions in it that will force the computation of the streaming RDDs.</p>
			</td>
		</tr></tbody></table><ol><li>实战
	<ol><li>用Spark Streaming实现实时WordCount</li>
	</ol></li>
</ol><p style="margin-left:0cm;">架构图：</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="216" src="https://img-blog.csdn.net/20180801125254200?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI1NDYwMjI3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="553"></p>

<ol><li>安装并启动生产者</li>
</ol><p style="margin-left:0cm;">首先在一台Linux（ip：192.168.10.101）上用YUM安装nc工具</p>

<p style="margin-left:0cm;"><span style="color:#333333;">yum install -y nc</span></p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;">启动一个服务端并监听9999端口</p>

<p style="margin-left:0cm;"><span style="color:#333333;">nc -lk 9999</span></p>

<p style="margin-left:0cm;"> </p>

<ol><li>编写Spark Streaming程序</li>
</ol><table border="1" cellspacing="0"><tbody><tr><td style="vertical-align:top;width:426.1pt;">
			<pre style="margin-left:0cm;">
<code class="language-html hljs">package com.qf.spark.streaming

  

  import com.qf.spark.util.LoggerLevel

  import org.apache.spark.SparkConf

  import org.apache.spark.streaming.{Seconds, StreamingContext}

  

  object NetworkWordCount {

  def main(args: Array[String]) {

    //设置日志级别

    LoggerLevel.setStreamingLogLevels()

    //创建SparkConf并设置为本地模式运行

    //注意local[2]代表开两个线程

    val conf = new SparkConf().setMaster("local[2]").setAppName("NetworkWordCount")

    //设置DStream批次时间间隔为2秒

    val ssc = new StreamingContext(conf, Seconds(2))

    //通过网络读取数据

    val lines = ssc.socketTextStream("192.168.10.101", 9999)

    //将读到的数据用空格切成单词

    val words = lines.flatMap(_.split(" "))

    //将单词和1组成一个pair

    val pairs = words.map(word =&gt; (word, 1))

    //按单词进行分组求相同单词出现的次数

    val wordCounts = pairs.reduceByKey(_ + _)

    //打印结果到控制台

    wordCounts.print()

    //开始计算

    ssc.start()

    //等待停止

    ssc.awaitTermination()

  }

}</code></pre>
			</td>
		</tr></tbody></table><p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;">3.启动Spark Streaming程序：由于使用的是本地模式<strong><span style="color:#008000;">"local[2]"</span></strong>所以可以直接在本地运行该程序</p>

<p style="margin-left:0cm;"><span style="color:#ff0000;">注意：</span>要指定并行度，如在本地运行设置setMaster("local[2]")，相当于启动两个线程，一个给receiver，一个给computer。如果是在集群中运行，必须要求集群中可用core数大于1</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="132" src="https://img-blog.csdn.net/20180801125310995?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI1NDYwMjI3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="553"></p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;">4.在Linux端命令行中输入单词</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="45" src="https://img-blog.csdn.net/20180801125316800?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI1NDYwMjI3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="554"></p>

<p style="margin-left:0cm;"> </p>

<p>5.在IDEA控制台中查看结果</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="148" src="https://img-blog.csdn.net/20180801125322529?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI1NDYwMjI3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="553"></p>

<p style="margin-left:0cm;"><span style="color:#ff0000;">问题：</span>结果每次在Linux段输入的单词次数都被正确的统计出来，但是结果不能累加！如果需要累加需要使用updateStateByKey(func)来更新状态，下面给出一个例子：</p>

<table border="1" cellspacing="0"><tbody><tr><td style="vertical-align:top;width:426.1pt;">
			<pre style="margin-left:0cm;">
<code class="language-html hljs">package com.qf.spark.streaming

  

  import com.qf.spark.util.LoggerLevel

  import org.apache.spark.{HashPartitioner, SparkConf}

  import org.apache.spark.streaming.{StreamingContext, Seconds}

  

  object NetworkUpdateStateWordCount {

  /**

    * String : 单词 hello

    * Seq[Int] ：单词在当前批次出现的次数

    * Option[Int] ： 历史结果

    */

  val updateFunc = (iter: Iterator[(String, Seq[Int], Option[Int])]) =&gt; {

    //iter.flatMap(it=&gt;Some(it._2.sum + it._3.getOrElse(0)).map(x=&gt;(it._1,x)))

    iter.flatMap{case(x,y,z)=&gt;Some(y.sum + z.getOrElse(0)).map(m=&gt;(x, m))}

  }

  

  def main(args: Array[String]) {

    LoggerLevel.setStreamingLogLevels()

    val conf = new SparkConf().setMaster("local[2]").setAppName("NetworkUpdateStateWordCount")

    val ssc = new StreamingContext(conf, Seconds(5))

    //做checkpoint 写入共享存储中

    ssc.checkpoint("c://aaa")

    val lines = ssc.socketTextStream("192.168.10.100", 9999)

    //reduceByKey 结果不累加

    //val result = lines.flatMap(_.split(" ")).map((_, 1)).reduceByKey(_+_)

    //updateStateByKey结果可以累加但是需要传入一个自定义的累加函数：updateFunc

    val results = lines.flatMap(_.split(" ")).map((_,1)).updateStateByKey(updateFunc, new HashPartitioner(ssc.sparkContext.defaultParallelism), true)

    results.print()

    ssc.start()

    ssc.awaitTermination()

  }

}</code></pre>

			<pre style="margin-left:0cm;">

<code class="language-html hljs"> </code></pre>
			</td>
		</tr></tbody></table><p>4.2Spark Streaming整合Kafka完成网站点击流实时统计</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="159" src="https://img-blog.csdn.net/20180801125357157?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI1NDYwMjI3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="553"></p>

<ol><li>安装并配置zk</li>
	<li>安装并配置Kafka</li>
	<li>启动zk</li>
	<li>启动Kafka</li>
	<li>创建topic</li>
</ol><p style="margin-left:0cm;"><span style="color:#333333;">bin/kafka-topics.sh --create --zookeeper node01:2181,node02:2181 \</span></p>

<p style="margin-left:0cm;"><span style="color:#333333;">--replication-factor 3 --partitions 3 --topic urlcount</span></p>

<p>6.编写Spark Streaming应用程序</p>

<table border="1" cellspacing="0"><tbody><tr><td style="vertical-align:top;width:426.1pt;">
			<pre style="margin-left:0cm;">
<code class="language-html hljs">package com.qf.spark.streaming

  

  package com.qf.spark

  

  import org.apache.spark.{HashPartitioner, SparkConf}

  import org.apache.spark.storage.StorageLevel

  import org.apache.spark.streaming.kafka.KafkaUtils

  import org.apache.spark.streaming.{Seconds, StreamingContext}

  

  object UrlCount {

  val updateFunc = (iterator: Iterator[(String, Seq[Int], Option[Int])]) =&gt; {

    iterator.flatMap{case(x,y,z)=&gt; Some(y.sum + z.getOrElse(0)).map(n=&gt;(x, n))}

  }

  

  def main(args: Array[String]) {

    //接收命令行中的参数

    val Array(zkQuorum, groupId, topics, numThreads, hdfs) = args

    //创建SparkConf并设置AppName

    val conf = new SparkConf().setAppName("UrlCount")

    //创建StreamingContext

    val ssc = new StreamingContext(conf, Seconds(2))

    //设置检查点

    ssc.checkpoint(hdfs)

    //设置topic信息

    val topicMap = topics.split(",").map((_, numThreads.toInt)).toMap

    //重Kafka中拉取数据创建DStream

    val lines = KafkaUtils.createStream(ssc, zkQuorum ,groupId, topicMap, StorageLevel.MEMORY_AND_DISK).map(_._2)

    //切分数据，截取用户点击的url

    val urls = lines.map(x=&gt;(x.split(" ")(6), 1))

    //统计URL点击量

    val result = urls.updateStateByKey(updateFunc, new HashPartitioner(ssc.sparkContext.defaultParallelism), true)

    //将结果打印到控制台

    result.print()

    ssc.start()

    ssc.awaitTermination()

  }

}</code></pre>
			</td>
		</tr></tbody></table><p style="margin-left:0cm;"> </p>            </div>
                </div>