---
layout:     post
title:      SPARK STREAMING之1：编程指南（翻译v1.4.1）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，转载请注明来自http://blog.csdn.net/jediael_lu/					https://blog.csdn.net/jediael_lu/article/details/77043575				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="spark-streaming之1编程指南翻译v141">SPARK STREAMING之1：编程指南（翻译v1.4.1）</h1>

<p>@(SPARK)[spark, 大数据]</p>

<p></p><div class="toc">
<ul>
<li><a href="#spark-streaming%E4%B9%8B1%E7%BC%96%E7%A8%8B%E6%8C%87%E5%8D%97%E7%BF%BB%E8%AF%91v141" rel="nofollow" target="_blank">SPARK STREAMING之1编程指南翻译v141</a><ul>
<li><a href="#%E6%A6%82%E8%BF%B0" rel="nofollow" target="_blank">概述</a></li>
<li><a href="#%E5%BF%AB%E9%80%9F%E5%85%A5%E9%97%A8%E4%BE%8B%E5%AD%90" rel="nofollow" target="_blank">快速入门例子</a></li>
<li><a href="#%E5%9F%BA%E6%9C%AC%E6%A6%82%E5%BF%B5" rel="nofollow" target="_blank">基本概念</a><ul>
<li><a href="#linking" rel="nofollow" target="_blank">Linking</a></li>
</ul>
</li>
</ul>
</li>
</ul>
</div>




<h2 id="概述">概述</h2>

<p>Spark Streaming是Spark核心API的一个扩展，它使得spark可扩展、高吞吐、可容错的对实时数据流进行处理。可以通过集成外部系统获取数据来源，如 Kafka, Flume, Twitter, ZeroMQ, Kinesis, 或者 TCP sockets，还可以使用高度抽象的复杂算法，如map、reduce、join、window等。最后，处理完的数据可以直接发送到文件系统、数据库或者一些仪表盘。事实上，你还可以spark的机器学习和图处理算法直接运用于数据流中。</p>

<p><img src="https://github.com/lujinhong/lujinhong.github.io/raw/master/pic/1.jpg" alt="Alt text" title=""></p>

<p>它的内部实现原理如下图: Spark Streaming收到实时输入数据，然后将它切分为多个批次，这些批次被spark引擎处理后同样以批次的形式输出最终的数据流。（注：也就是说Spark Streaming不是真正的实时系统，而是一个准实时系统，它会等待收集到一定量的数据后汇总成一个批次，然后再统一处理）</p>

<p><img src="https://github.com/lujinhong/lujinhong.github.io/raw/master/pic/2.jpg" alt="Alt text" title=""></p>

<p>Spark Streaming有一个高度抽象概念叫做discretized stream or DStream（分离式流），它表示一个持续的数据流。DStream可以通过集成外部系统来获取（如kafka,flume等），或者通过对其它DStream操作后生成新的Dstream。在spark内部，DStream以一系列的RDD表示。</p>

<p>本文介绍了如何编写Spark Streaming的程序。你可以使用scala、java、python（1.2版本后）编写程序，这些内容都将在本文中介绍。在本文中，你可以通过点击标签选择查看不同语言所实现的代码。</p>

<p>注意：Spark Streaming的python API在1.2版本后引入的，它包括了scala/java所有的DStream transformation操作，以及大部分的输出操作。但是，只它支持基本的数据源（如文本文件，socket的文本数据等），集成kafka，flume的API将在以后的版本中实现。</p>



<h2 id="快速入门例子">快速入门例子</h2>

<p>在详细介绍如何编写Spark Streaming程序前，我们先看一个简单的例子，在这个例子中，我们对从TCP scoket中收到的文本数据进行单词统计。步骤如下：</p>

<p>首先，我们导入Spark Streaming的相关类，以及StreamingContext（1.3版本后不再需要）。StreamingContext是所有streaming功能的进入点。在这个例子中，我们创建了一个local StreamingContext，它有2个执行线程，每1秒形成一个批次。</p>

<pre><code>import org.apache.spark._
import org.apache.spark.streaming._
import org.apache.spark.streaming.StreamingContext._ // not necessary since Spark 1.3

// Create a local StreamingContext with two working thread and batch interval of 1 second.
// The master requires 2 cores to prevent from a starvation scenario.

val conf = new SparkConf().setMaster("local[2]").setAppName("NetworkWordCount")
val ssc = new StreamingContext(conf, Seconds(1))
</code></pre>

<p>使用这个context，我们创建一个从TCP socket获取数据的Dstream，它指定了主机名（localhost）以及端口号(9999)。</p>

<pre><code>// Create a DStream that will connect to hostname:port, like localhost:9999
val lines = ssc.socketTextStream("localhost", 9999)
</code></pre>

<p>变量lines表示从数据服务器收到的数据流，DStream中的每一个数据记录表示一行。然后，我们将这些行以空格切分成单词。（注：事实上，lines代表的是每秒钟收到的数据，而在map操作中以行作为单位进行输入）</p>

<pre><code>// Split each line into words
val words = lines.flatMap(_.split(" "))
</code></pre>

<p>flatMap是一个一对多的DStream操作，它从源DStream中读取一个记录，然后生成多个记录并发送到新的DStream中。在这个例子中，每一行内容都将切分为多个单词，这个单词流以words这个DStream表示。接下来，我们统计这些单词。</p>

<pre><code>import org.apache.spark.streaming.StreamingContext._ // not necessary since Spark 1.3
// Count each word in each batch
val pairs = words.map(word =&gt; (word, 1))
val wordCounts = pairs.reduceByKey(_ + _)

// Print the first ten elements of each RDD generated in this DStream to the console
wordCounts.print()
</code></pre>

<p>words这个DStream经过map操作后转换为一个(word,1)对的新DStream，然后统计每个单词的出现频率。最后，wordCouns.print()会每秒钟输出一些统计结果。</p>

<p>记住到这里为止，Spark Streaming只是定义了它会进行什么处理，但这些处理还没启动。你可以通过以下代码启动真正的处理进程：</p>

<pre><code>ssc.start()             // Start the computation
ssc.awaitTermination()  // Wait for the computation to terminate
</code></pre>

<p>完整代码请见 Spark Streaming example NetworkWordCount. </p>

<p>如果你已经下载了spark，你可以按照下面的步骤运行。首先你需要运行netcat（一个在大多数类unix系统中都能找到的小工具）作为一个数据服务器：</p>

<pre><code>$ nc -lk 9999
</code></pre>

<p>然后，在另一个终端中，你可以这样启动上面的例子：</p>

<pre><code>$ ./bin/run-example streaming.NetworkWordCount localhost 9999
</code></pre>

<p>此时，你在nc终端中输入的每一行文本都将被统计并输出结果。如下：</p>

<pre><code># TERMINAL 1:
# Running Netcat
$ nc -lk 9999
hello world


# TERMINAL 2: RUNNING NetworkWordCount

$ ./bin/run-example streaming.NetworkWordCount localhost 9999
...
-------------------------------------------
Time: 1357008430000 ms
-------------------------------------------
(hello,1)
(world,1)
...
</code></pre>



<h2 id="基本概念">基本概念</h2>

<p>下面，我们开始详细描述Spark Streaming的基本概念。</p>



<h3 id="linking">Linking</h3>

<p>与spark 类似，spark streaming可以通过maven的中央仓库获取。开发Spark Streaming程序前，你需要将以下依赖关系放到你的maven或者sbt项目中。</p>

<pre><code> &lt;dependency&gt;
      &lt;groupId&gt;org.apache.spark&lt;/groupId&gt;
      &lt;artifactId&gt;spark-streaming_2.10&lt;/artifactId&gt;
      &lt;version&gt;1.4.1&lt;/version&gt;
 &lt;/dependency&gt;    
</code></pre>

<p>如果需要集成kafka, flume,kinesis等未在Spark Streaming 核心API中包含的数据源，你需要在依赖中添加类似 spark-streaming-xyz_2.10的依赖。一引起常用的如下：</p>

<pre><code>Source  Artifact
Kafka   spark-streaming-kafka_2.10
Flume   spark-streaming-flume_2.10
Kinesis
spark-streaming-kinesis-asl_2.10 [Amazon Software License]
Twitter spark-streaming-twitter_2.10
ZeroMQ  spark-streaming-zeromq_2.10
MQTT    spark-streaming-mqtt_2.10
</code></pre>

<p>请参考maven的中央仓库以获得最新最全的依赖关系。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>