---
layout:     post
title:      Spark Streaming 第一部分
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1> Spark 概述</h1>

<p>Spark Streaming是基于Spark core API的扩展，支持高吞吐，可扩展，容错的。</p>

<pre class="has">
<code>Spark Streaming is an extension of the core Spark API that enables scalable, high-throughput, fault-tolerant stream processing of live data streams. </code></pre>

<p> 特点如下图所示</p>

<p>一方面可以对接到多种数据源，另外一方面处理完的数据可以写到很多地方去</p>

<p><img alt="Spark Streaming" class="has" src="http://spark.apache.org/docs/latest/img/streaming-arch.png"></p>

<p>总结：</p>

<ul><li>将不同的数据源的数据经过~的处理后将结果输出到外部系统中</li>
	<li>低延时</li>
	<li>能能错误中高效的恢复，即容错性很高</li>
	<li>能够运行在成百上千的节点</li>
	<li>能够将批处理、机器学习、图计算等子框架和spark streaming综合使用</li>
</ul><p> </p>

<pre class="has">
<code>Internally, it works as follows. 
Spark Streaming receives live input data streams and divides the data into batches, 
which are then processed by the Spark engine to generate the final stream of results in batches.</code></pre>

<p> 事实上，他们是以从节点的方式工作的。Spark Streaming接收来自数据源的数据，将他们切分成一个一个的数据块后，传送给Spark engine进行处理，来得到最后处理的结果</p>

<p><img alt="Spark Streaming" class="has" src="http://spark.apache.org/docs/latest/img/streaming-flow.png"></p>

<p>Spark提供了一站式解决方案</p>

<p>One stack to rule them all:一站式解决</p>

<p><img alt="" class="has" height="396" src="https://img-blog.csdn.net/20180930094101307?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NpbmF0XzM3NTEzOTk4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="691"></p>

<p> </p>

<h1>集成Spark 生态系统的使用</h1>

<h2>Join data streams with static data sets</h2>

<h2>Combine machine learning with Streaming processing</h2>

<h2>Combine SQL with Streaming Processing Interactively query streaming data with sql</h2>

<h1>Spark Streaming的发展史</h1>

<h1>Spark 词频统计</h1>

<p>org.apache.spark.examples.streaming.NetworkWordCount</p>

<p>核心代码，可以看出，Spark SQL和Spark Streaming是可以进行无缝衔接的。</p>

<pre class="has">
<code>    val ssc = new StreamingContext(sparkConf, Seconds(1))    
    val lines = ssc.socketTextStream(args(0), args(1).toInt, StorageLevel.MEMORY_AND_DISK_SER)
    val words = lines.flatMap(_.split(" "))
    val wordCounts = words.map(x =&gt; (x, 1)).reduceByKey(_ + _)
    wordCounts.print()
    ssc.start()
    ssc.awaitTermination()</code></pre>

<p> 开启NC服务</p>

<pre class="has">
<code>[hadoop@hadoop000 ~]$ nc -lk 9999</code></pre>

<p>执行spark-submit </p>

<pre class="has">
<code>./spark-submit --master local[2] \
--class org.apache.spark.examples.streaming.NetworkWordCount \
--name NetworkWordCount \
/home/hadoop/app/spark-2.1.0-bin-2.6.0-cdh5.7.0/examples/jars/spark-examples_2.11-2.1.0.jar hadoop000 9999</code></pre>

<p>执行spark-shell</p>

<pre class="has">
<code>[hadoop@hadoop000 bin]$ ./spark-shell --master local[2]
</code></pre>

<pre class="has">
<code>import org.apache.spark.streaming.{Seconds, StreamingContext}
val ssc = new StreamingContext(sc, Seconds(1))    
val lines = ssc.socketTextStream("hadoop000", 9999)
val words = lines.flatMap(_.split(" "))
val wordCounts = words.map(x =&gt; (x, 1)).reduceByKey(_ + _)
wordCounts.print()
ssc.start()
ssc.awaitTermination()</code></pre>

<h1>Spark的工作原理</h1>

<p>Spark Streaming接收到数据后，按照设置的秒数来拆分，然后交由Spark引擎处理。产生processed result</p>

<p>运行Spark Streaming 的时候，会把数据拆分成一系列的小的确定的批次来进行处理，Spark Streaming会</p>

<p>把每个批次当作RDD来处理，然后交由spark引擎进行处理，最后分次分批返回。</p>

<p> </p>

<p>总结：Spark Streaming接收到实时数据流，会把数据流按照指定时间段切成一片片小的数据块，然后把小的数据块传给spark engine</p>

<p> </p>

<h2>细粒度</h2>

<p>对于应用程序在跑的时候，会生成StreamingContext和SparkContext<br>
 Driver会生成一个receivers用来跑在worder节点的Executors上<br>
 也可以看成是StreamingContext在Executore生成的Receivers的任务<br>
 <br>
 receiver起来后，等待数据源的输入，并且将数据源分离成一个个数据块，其实就是RDD<br>
 基于Executor的memory的存储备份（可以配置）<br>
 把Blocks的副本存储到其他的Executor上去</p>

<p><br>
 Executor将接收（保存到内存）中的数据的保存（block metastore信息地址）报告给StreamingContext<br>
 <br>
 StreamingContext对每个数据块按照RDD处理，将数据集给SparkContext按照一个个Job处理<br>
 <br>
 SparkContext跑executor memory中的数据跑一个个任务<br>
 <br>
 会不停、循环的处理</p>            </div>
                </div>