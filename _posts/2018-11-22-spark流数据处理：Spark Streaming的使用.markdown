---
layout:     post
title:      spark流数据处理：Spark Streaming的使用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>本文讲解Spark流数据处理之Spark Streaming。本文的写作时值Spark 1.6.2发布之际，Spark 2.0预览版也已发布，Spark发展如此迅速，请随时关注<a href="http://spark.apache.org/docs/latest/streaming-programming-guide.html" rel="nofollow">Spark Streaming官方文档</a>以了解最新信息。</p>

<p>文中对Spark Streaming的讲解主要使用到Scala语言，其他语言请参考官方文档，这点请知晓。</p>

<h1 id="u6982_u8FF0"><a name="t0"></a>概述</h1>

<p>Spark Streaming是Spark核心API的扩展，用于可伸缩、高吞吐量、可容错地处理在线流数据。Spark Streaming可以从很多数据源获取数据，比如：Kafka、Flume、Twitter、ZeroMQ、Kinesis或TCP连接等，并可以用很多高层算子(map/reduce/join/window等)来方便地处理这些数据。最后处理过的数据还可以推送到文件系统、数据库和在线监控页面等。实际上，你也可以在数据流上使用Spark的机器学习和图计算算法。</p>

<p>Spark Streaming内部工作机制概图如下所示。Spark Streaming接收在线数据流并将其划分成批(batch)，然后通过Spark引擎处理并最终得到由一批一批数据构成的结果流。<br><a href="http://smallx.me/2016/07/03/spark%E6%B5%81%E6%95%B0%E6%8D%AE%E5%A4%84%E7%90%86%EF%BC%9ASpark-Streaming%E7%9A%84%E4%BD%BF%E7%94%A8/Spark-Streaming%E6%B5%81%E7%A8%8B%E6%A6%82%E5%9B%BE.png" rel="nofollow"><img alt="Spark Streaming流程概图" class="has" src="http://smallx.me/2016/07/03/spark%E6%B5%81%E6%95%B0%E6%8D%AE%E5%A4%84%E7%90%86%EF%BC%9ASpark-Streaming%E7%9A%84%E4%BD%BF%E7%94%A8/Spark-Streaming%E6%B5%81%E7%A8%8B%E6%A6%82%E5%9B%BE.png"></a></p>

<p>Spark Streaming将流数据抽象为离散化流(discretized stream)，即<code>DStream</code>。DStream可以从输入数据流创建也可以从其他的DStream转换而来。DStream在内部被表示为一个连续的RDD序列。</p>

<h1 id="u5FEB_u901F_u793A_u4F8B"><a name="t1"></a>快速示例</h1>

<p>首先以一个简单的示例开始：用Spark Streaming对从TCP连接中接收的文本进行单词计数。</p>

<table><tbody><tr><td>
			<pre>
/**
 * 功能：用spark streaming实现的针对流式数据进行单词计数的程序。
 *      该程序只是对数据流中的每一批数据进行单独的计数，而没有进行增量计数。
 * 环境：spark 1.6.1, scala 2.10.4
 */

// 引入相关类库
import org.apache.spark._
import org.apache.spark.streaming._

object NetworkWordCount {
  def main(args: Array[String]) {
    // Spark Streaming程序以StreamingContext为起点，其内部维持了一个SparkContext的实例。
    // 这里我们创建一个带有两个本地线程的StreamingContext，并设置批处理间隔为1秒。
    val conf = new SparkConf().setMaster("local[2]").setAppName("NetworkWordCount")
    val ssc = new StreamingContext(conf, Seconds(1))
    // 在一个Spark应用中默认只允许有一个SparkContext，默认地spark-shell已经为我们创建好了
    // SparkContext，名为sc。因此在spark-shell中应该以下述方式创建StreamingContext，以
    // 避免创建再次创建SparkContext而引起错误：
    // val ssc = new StreamingContext(sc, Seconds(1))

    // 创建一个从TCP连接获取流数据的DStream，其每条记录是一行文本
    val lines = ssc.socketTextStream("localhost", 9999)

    // 对DStream进行转换，最终得到计算结果
    val res = lines.flatMap(_.split(" ")).map((_, 1)).reduceByKey(_ + _)

    // 打印该DStream中每个RDD中的前十个元素
    res.print()

    // 执行完上面代码，Spark Streaming并没有真正开始处理数据，而只是记录需在数据上执行的操作。
    // 当我们设置好所有需要在数据上执行的操作以后，我们就可以开始真正地处理数据了。如下：
    ssc.start()                     // 开始计算
    ssc.awaitTermination()          // 等待计算终止
  }
}
</pre>
			</td>
		</tr></tbody></table><p>为了测试程序，我们得有TCP数据源作为输入，这可以使用Netcat(一般linux系统中都有，如果是windows系统，则推荐你使用<a href="https://nmap.org/ncat/" rel="nofollow">Ncat</a>，Ncat是一个改进版的Netcat)。如下使用Netcat监听指定本地端口：</p>

<table><tbody><tr><td>
			<pre>
nc -lk 9999
</pre>
			</td>
		</tr></tbody></table><p>如果是使用Ncat，则对应命令如下：</p>

<table><tbody><tr><td>
			<pre>
ncat -lk 9999
</pre>
			</td>
		</tr></tbody></table><p>在IntelliJ IDEA或Eclipse中可以本地运行测试上述Spark Streaming程序，该程序会连接到Netcat(或Ncat)监听的端口，你可以在运行Netcat(或Ncat)的终端中输入东东并回车，然后就可以看到该Spark Streaming程序会马上输出处理结果，并且这个处理是不停的、流式的。</p>

<blockquote>
<p>注意：上述示例只是对数据流中的每一批数据进行单独的计数，而没有进行增量计数。</p>
</blockquote>

<p><a id="more"></a></p>

<h1 id="u57FA_u672C_u6982_u5FF5"><a name="t2"></a>基本概念</h1>

<h2 id="StreamingContext"><a name="t3"></a>StreamingContext</h2>

<p><code>StreamingContext</code>是Spark Streaming程序的入口点，正如SparkContext是Spark程序的入口点一样。</p>

<p>StreamingContext中维护了一个SparkContext实例，你可以通过<code>ssc.sparkContext</code>来访问它。该SparkContext实例要么在创建StreamingContext时被传入，要么在StreamingContext内部根据传入的SparkConf进行创建，这取决于你所使用的StreamingContext构造函数，请观看API文档。</p>

<h2 id="u5173_u4E8EDStream"><a name="t4"></a>关于DStream</h2>

<p>Spark Streaming将流数据抽象为离散化流(discretized stream)，即<code>DStream</code>。DStream在内部被表示为一个连续的RDD序列，每一个RDD包含了一个固定时间间隔内数据源所产生的数据，如下图所示。</p>

<p><a href="http://smallx.me/2016/07/03/spark%E6%B5%81%E6%95%B0%E6%8D%AE%E5%A4%84%E7%90%86%EF%BC%9ASpark-Streaming%E7%9A%84%E4%BD%BF%E7%94%A8/DStream%E4%B8%8ERDD.png" rel="nofollow"><img alt="DStream与RDD" class="has" src="http://smallx.me/2016/07/03/spark%E6%B5%81%E6%95%B0%E6%8D%AE%E5%A4%84%E7%90%86%EF%BC%9ASpark-Streaming%E7%9A%84%E4%BD%BF%E7%94%A8/DStream%E4%B8%8ERDD.png"></a></p>

<p>对DStream所进行的操作将被转换为对底层RDD的操作。例如，在前面的流数据单词计数示例程序中，<code>lines.flatMap(_.split(" "))</code>语句中的<code>flatMap</code>算子就被应用到lines DStream中的RDD以生成words DStream中的RDD，如下图所示。</p>

<p><a href="http://smallx.me/2016/07/03/spark%E6%B5%81%E6%95%B0%E6%8D%AE%E5%A4%84%E7%90%86%EF%BC%9ASpark-Streaming%E7%9A%84%E4%BD%BF%E7%94%A8/%E5%9C%A8DStream%E4%B9%8B%E4%B8%8A%E7%9A%84%E6%93%8D%E4%BD%9C.png" rel="nofollow"><img alt="在DStream之上的操作" class="has" src="http://smallx.me/2016/07/03/spark%E6%B5%81%E6%95%B0%E6%8D%AE%E5%A4%84%E7%90%86%EF%BC%9ASpark-Streaming%E7%9A%84%E4%BD%BF%E7%94%A8/%E5%9C%A8DStream%E4%B9%8B%E4%B8%8A%E7%9A%84%E6%93%8D%E4%BD%9C.png"></a></p>

<h3 id="InputDStream_u548CReceiver"><a name="t5"></a>InputDStream和Receiver</h3>

<p>InputDStream代表输入数据流，除了file stream和queue RDD stream，其他的输入流都和一个Receiver相关联(具体地是对应ReceiverInputDStream类，其内部会启动一个Receiver)，Receiver工作在一个worker节点上，用于接收相应数据源的流数据并将其存储在内存中(取决于创建StreamingContext时指定的存储级别)以供处理。</p>

<p>我们也可以创建多个InputDStream来连接多个数据源，其中的ReceiverInputDStream都将启动Receiver来接收数据。<strong>一个Spark Streaming应用程序应该分配足够多的核心(local模式下是线程)去运行receiver(s)并处理其接收的数据。当我们以本地模式运行Spark Streaming程序时，master URL不能指定为<code>local</code>或者<code>local[1]</code>(Spark Streaming会启动一个线程运行receiver，只有一个线程将导致没有线程来处理数据)，而应该是<code>local[n]</code>，这个n应该大于receiver的个数。在集群中运行Spark Streaming程序时，同样道理，也需要分配大于receiver的个数的核心数。</strong></p>

<h3 id="u57FA_u672C_u6570_u636E_u6E90"><a name="t6"></a>基本数据源</h3>

<p>Spark Streaming提供了从很多数据源获取流数据的方法，一些基本的数据源可以通过StreamingContext API直接使用，主要包括：文件系统、网络连接、Akka actors等。</p>

<p>文件数据流</p>

<p>StreamingContext提供了从兼容于HDFS API的所有文件系统中创建文件数据输入流的方法，如下：</p>

<table><tbody><tr><td>
			<pre>
ssc.fileStream[KeyClass, ValueClass, InputFormatClass](dataDirectory)
</pre>
			</td>
		</tr></tbody></table><p>文件流没有receiver。Spark Streaming将监控对应目录(但不支持嵌套目录)，并处理在该目录中创建的任何文件(以<code>.</code>开头的将被忽略)。监控目录中的文件必须有相同的数据格式。监控目录中的文件如果被修改(比如以append方式写入)，这些修改将不会被读取，因此正确的方式应该是先在其他目录中写好这些文件并将其移动或者重命名到该监控目录。</p>

<p>对于简单的文本文件，可以使用更简单的方法，如下：</p>

<table><tbody><tr><td>
			<pre>
ssc.textFileStream(dataDirectory)
</pre>
			</td>
		</tr></tbody></table><p>网络数据流</p>

<p>网络连接流可以使用<code>ssc.socketStream()</code>或<code>ssc.socketTextStream()</code>创建，详情参见API文档。</p>

<p>Akka Actor流</p>

<p>可以通过<code>ssc.actorStream()</code>创建一个从Akka actor接收数据流的ReceiverInputDStream。更多参见API文档和<a href="http://spark.apache.org/docs/latest/streaming-custom-receivers.html" rel="nofollow">自定义Receiver指南</a>。</p>

<p>RDD序列流</p>

<p>我们也可以用<code>ssc.queueStream()</code>创建一个基于RDD序列的InputDStream。序列中的每一个RDD将被作为DStream中的一个数据批，这通常在测试你的Spark Streaming程序时非常有用。</p>

<h3 id="u9AD8_u7EA7_u6570_u636E_u6E90"><a name="t7"></a>高级数据源</h3>

<p>对于Kafka、Flume、Kinesis、Twitter等这些高级数据源，则需要添加外部依赖，关于依赖参见<a href="http://spark.apache.org/docs/latest/streaming-programming-guide.html#linking" rel="nofollow">这里</a>。</p>

<p>下面给出一些关于高级数据源集成方法的参考链接：</p>

<ul><li><strong>Kafka</strong>: <a href="http://spark.apache.org/docs/latest/streaming-kafka-integration.html" rel="nofollow">Kafka Integration Guide</a></li>
	<li><strong>Flume</strong>: <a href="http://spark.apache.org/docs/latest/streaming-flume-integration.html" rel="nofollow">Flume Integration Guide</a></li>
	<li><strong>Kinesis</strong>: <a href="http://spark.apache.org/docs/latest/streaming-kinesis-integration.html" rel="nofollow">Kinesis Integration Guide</a></li>
</ul><h3 id="u81EA_u5B9A_u4E49_u6570_u636E_u6E90"><a name="t8"></a>自定义数据源</h3>

<p>你也可以自定义数据源，只需要实现一个自己的receiver从自定义数据源接收数据并将其推送到Spark。详情参见：<a href="http://spark.apache.org/docs/latest/streaming-custom-receivers.html" rel="nofollow">Custom Receiver Guide</a>。</p>

<h3 id="Receiver_u53EF_u9760_u6027"><a name="t9"></a>Receiver可靠性</h3>

<p>依据可靠性可将Receiver分为两类。<strong>可靠Receiver</strong>带有传输确认机制(ACK机制)，可以确保数据在传输过程中不会丢失，Kafka和Flume等在ACK机制开启的情况下就是可靠的。<strong>不可靠Receiver</strong>不带有传输确认机制，包括不支持ACK机制和支持ACK但关闭的情形。</p>

<h3 id="DStream_u8F6C_u6362_u7B97_u5B50"><a name="t10"></a>DStream转换算子</h3>

<p>DStream支持很多和RDD类似的转换算子(transformation)(<strong>这些转换算子和RDD中的一样，都是lazy的</strong>)，完整的算子列表参见API文档中的<code>DStream</code>和<code>PairDStreamFunctions</code>，下面列出一些常用的：</p>

<table><thead><tr><th>Transformation</th>
			<th>Meaning</th>
		</tr></thead><tbody><tr><td><strong>map</strong>(<em>func</em>)</td>
			<td>Return a new DStream by passing each element of the source DStream through a function <em>func</em>.</td>
		</tr><tr><td><strong>flatMap</strong>(<em>func</em>)</td>
			<td>Similar to map, but each input item can be mapped to 0 or more output items.</td>
		</tr><tr><td><strong>filter</strong>(<em>func</em>)</td>
			<td>Return a new DStream by selecting only the records of the source DStream on which <em>func</em> returns true.</td>
		</tr><tr><td><strong>repartition</strong>(<em>numPartitions</em>)</td>
			<td>Changes the level of parallelism in this DStream by creating more or fewer partitions.</td>
		</tr><tr><td><strong>union</strong>(<em>otherStream</em>)</td>
			<td>Return a new DStream that contains the union of the elements in the source DStream and <em>otherDStream</em>.</td>
		</tr><tr><td><strong>count</strong>()</td>
			<td>Return a new DStream of single-element RDDs by counting the number of elements in each RDD of the source DStream.</td>
		</tr><tr><td><strong>reduce</strong>(<em>func</em>)</td>
			<td>Return a new DStream of single-element RDDs by aggregating the elements in each RDD of the source DStream using a function <em>func</em> (which takes two arguments and returns one). The function should be associative so that it can be computed in parallel.</td>
		</tr><tr><td><strong>countByValue</strong>()</td>
			<td>When called on a DStream of elements of type K, return a new DStream of (K, Long) pairs where the value of each key is its frequency in each RDD of the source DStream.</td>
		</tr><tr><td><strong>reduceByKey</strong>(<em>func</em>, [<em>numTasks</em>])</td>
			<td>When called on a DStream of (K, V) pairs, return a new DStream of (K, V) pairs where the values for each key are aggregated using the given reduce function. <strong>Note:</strong> By default, this uses Spark’s default number of parallel tasks (2 for local mode, and in cluster mode the number is determined by the config property <code>spark.default.parallelism</code>) to do the grouping. You can pass an optional <code>numTasks</code> argument to set a different number of tasks.</td>
		</tr><tr><td><strong>join</strong>(<em>otherStream</em>, [<em>numTasks</em>])</td>
			<td>When called on two DStreams of (K, V) and (K, W) pairs, return a new DStream of (K, (V, W)) pairs with all pairs of elements for each key.</td>
		</tr><tr><td><strong>cogroup</strong>(<em>otherStream</em>, [<em>numTasks</em>])</td>
			<td>When called on a DStream of (K, V) and (K, W) pairs, return a new DStream of (K, Seq[V], Seq[W]) tuples.</td>
		</tr><tr><td><strong>transform</strong>(<em>func</em>)</td>
			<td>Return a new DStream by applying a RDD-to-RDD function to every RDD of the source DStream. This can be used to do arbitrary RDD operations on the DStream.</td>
		</tr><tr><td><strong>updateStateByKey</strong>(<em>func</em>)</td>
			<td>Return a new “state” DStream where the state for each key is updated by applying the given function on the previous state of the key and the new values for the key. This can be used to maintain arbitrary state data for each key.</td>
		</tr></tbody></table><p>下面对其中一些操作进行更详细的说明。</p>

<p>updateStateByKey</p>

<p>updateStateByKey操作允许你用持续不断的新信息来更新你所维护的某些状态信息。为了使用这个算子，通常你需要如下两个步骤：</p>

<ol><li>定义状态：状态可以是任意数据类型。</li>
	<li>定义状态更新函数：根据先前的状态数据和流数据中的新数据如何更新状态数据。</li>
</ol><p>在每一批数据中，Spark都将在所有已经存在的key上应用状态更新函数，即使在这批数据中没有某些key对应的数据。如果状态更新函数返回<code>None</code>，那么对应的key-value对将被移除。</p>

<p>在快速示例中，我们只是对每一批数据进行单独的单词计数，在这里我们就可以通过updateStateByKey算子进行增量计数了。</p>

<p>需要注意的是使用updateStateByKey算子要求已经配置了检查点目录，参见检查点部分。</p>

<p>transform</p>

<p>transform操作允许在DStream上应用任意RDD-to-RDD函数，这样你就可以方便地使用在DStream API中没有的却在RDD API中存在的算子来转换DStream中的每一个RDD了。例如，在DStream API中不存在将数据流中的每一批数据(一个RDD)与其他数据集进行join的操作，此时就可以通过transform算子+RDD的join算子来实现。</p>

<p>需要注意的是传入transform的函数每次应用在一批数据(一个RDD)上，这意味着你可以根据时间变化在不同的RDD上做不同的处理，也就是说RDD操作、RDD分区数、广播变量等在不同的批之间都可以改变。</p>

<p>window</p>

<p>Spark Streaming也提供了基于窗口的计算，它允许你在一个滑动窗口上使用转换操作，滑动窗口如下图所示。</p>

<p><a href="http://smallx.me/2016/07/03/spark%E6%B5%81%E6%95%B0%E6%8D%AE%E5%A4%84%E7%90%86%EF%BC%9ASpark-Streaming%E7%9A%84%E4%BD%BF%E7%94%A8/DStream%E6%BB%91%E5%8A%A8%E7%AA%97%E5%8F%A3.png" rel="nofollow"><img alt="DStream滑动窗口" class="has" src="http://smallx.me/2016/07/03/spark%E6%B5%81%E6%95%B0%E6%8D%AE%E5%A4%84%E7%90%86%EF%BC%9ASpark-Streaming%E7%9A%84%E4%BD%BF%E7%94%A8/DStream%E6%BB%91%E5%8A%A8%E7%AA%97%E5%8F%A3.png"></a></p>

<p>窗口是基于时间滑动的，窗口操作新形成的DStream中的每一个RDD包含了某一滑动窗口中的所有数据。任何窗口操作都需要指定如下两个参数：</p>

<ul><li>窗口长度：它必须是源DStream批处理间隔的整数倍。</li>
	<li>滑动间隔：它必须是源DStream批处理间隔的整数倍。</li>
</ul><p>一些常用的窗口操作算子如下：</p>

<table><thead><tr><th>Transformation</th>
			<th>Meaning</th>
		</tr></thead><tbody><tr><td><strong>window</strong>( <em>windowLength</em>, <em>slideInterval</em>)</td>
			<td>Return a new DStream which is computed based on windowed batches of the source DStream.</td>
		</tr><tr><td><strong>countByWindow</strong>( <em>windowLength</em>, <em>slideInterval</em>)</td>
			<td>Return a sliding window count of elements in the stream.</td>
		</tr><tr><td><strong>reduceByWindow</strong>( <em>func</em>, <em>windowLength</em>, <em>slideInterval</em>)</td>
			<td>Return a new single-element stream, created by aggregating elements in the stream over a sliding interval using <em>func</em>. The function should be associative so that it can be computed correctly in parallel.</td>
		</tr><tr><td><strong>reduceByKeyAndWindow</strong>( <em>func</em>, <em>windowLength</em>, <em>slideInterval</em>, [<em>numTasks</em>])</td>
			<td>When called on a DStream of (K, V) pairs, returns a new DStream of (K, V) pairs where the values for each key are aggregated using the given reduce function <em>func</em> over batches in a sliding window. <strong>Note:</strong> By default, this uses Spark’s default number of parallel tasks (2 for local mode, and in cluster mode the number is determined by the config property<code>spark.default.parallelism</code>) to do the grouping. You can pass an optional <code>numTasks</code>argument to set a different number of tasks.</td>
		</tr><tr><td><strong>reduceByKeyAndWindow</strong>( <em>func</em>, <em>invFunc</em>, <em>windowLength</em>, <em>slideInterval</em>, [<em>numTasks</em>])</td>
			<td>A more efficient version of the above <code>reduceByKeyAndWindow()</code> where the reduce value of each window is calculated incrementally using the reduce values of the previous window. This is done by reducing the new data that enters the sliding window, and “inverse reducing” the old data that leaves the window. An example would be that of “adding” and “subtracting” counts of keys as the window slides. However, it is applicable only to “invertible reduce functions”, that is, those reduce functions which have a corresponding “inverse reduce” function (taken as parameter <em>invFunc</em>). Like in <code>reduceByKeyAndWindow</code>, the number of reduce tasks is configurable through an optional argument. Note that <a href="http://spark.apache.org/docs/latest/streaming-programming-guide.html#checkpointing" rel="nofollow">checkpointing</a> must be enabled for using this operation.</td>
		</tr><tr><td><strong>countByValueAndWindow</strong>( <em>windowLength</em>, <em>slideInterval</em>, [<em>numTasks</em>])</td>
			<td>When called on a DStream of (K, V) pairs, returns a new DStream of (K, Long) pairs where the value of each key is its frequency within a sliding window. Like in <code>reduceByKeyAndWindow</code>, the number of reduce tasks is configurable through an optional argument.</td>
		</tr></tbody></table><p>需要强调的是，上述某些操作(如<code>reduceByWindow</code>和<code>reduceByKeyAndWindow</code>等)有一些特殊形式，通过只考虑新进入窗口的数据和离开窗口的数据，让Spark增量计算归约结果。这种特殊形式需要额外提供一个规约函数的逆函数，比如<code>+</code>对应的逆函数为<code>-</code>。对于较大的窗口，提供逆函数可以大大提高执行效率。</p>

<p>join</p>

<p><strong>Stream-stream joins：</strong></p>

<table><tbody><tr><td>
			<pre>
val stream1: DStream[String, String] = ...
val stream2: DStream[String, String] = ...
val joinedStream = stream1.join(stream2)
</pre>
			</td>
		</tr></tbody></table><p>上述代码对两个DStream进行<code>join</code>操作，在每一个批处理间隔，stream1产生的一个RDD将和stream2产生的一个RDD进行join操作。另外，还有其他一些join操作：<code>leftOuterJoin</code>、<code>rightOuterJoin</code>和<code>fullOuterJoin</code>。也可以进行基于窗口的join操作，如下：</p>

<table><tbody><tr><td>
			<pre>
val windowedStream1 = stream1.window(Seconds(20))
val windowedStream2 = stream2.window(Minutes(1))
val joinedStream = windowedStream1.join(windowedStream2)
</pre>
			</td>
		</tr></tbody></table><p><strong>Stream-dataset joins：</strong></p>

<p>这种类型的join可以通过<code>transform()</code>来实现。如下代码将一个分窗的stream和一个数据集进行join：</p>

<table><tbody><tr><td>
			<pre>
val dataset: RDD[String, String] = ...
val windowedStream = stream.window(Seconds(20))...
val joinedStream = windowedStream.transform { rdd =&gt; rdd.join(dataset) }
</pre>
			</td>
		</tr></tbody></table><h3 id="DStream_u8F93_u51FA_u64CD_u4F5C"><a name="t11"></a>DStream输出操作</h3>

<p>输出操作允许将DStream中的数据推送到外部系统，比如数据库和文件系统。和RDD的action算子类似，DStream的输出操作用来触发所有转换操作的执行。下面列出主要的输出操作：</p>

<table><thead><tr><th>Output Operation</th>
			<th>Meaning</th>
		</tr></thead><tbody><tr><td><strong>print</strong>()</td>
			<td>Prints the first ten elements of every batch of data in a DStream on the driver node running the streaming application. This is useful for development and debugging.</td>
		</tr><tr><td><strong>saveAsTextFiles</strong>( <em>prefix</em>, [<em>suffix</em>])</td>
			<td>Save this DStream’s contents as text files. The file name at each batch interval is generated based on <em>prefix</em> and <em>suffix</em>:<em>“prefix-TIME_IN_MS[.suffix]”</em>.</td>
		</tr><tr><td><strong>saveAsObjectFiles</strong>( <em>prefix</em>, [<em>suffix</em>])</td>
			<td>Save this DStream’s contents as<code>SequenceFiles</code> of serialized Java objects. The file name at each batch interval is generated based on <em>prefix</em> and <em>suffix</em>:<em>“prefix-TIME_IN_MS[.suffix]”</em>.</td>
		</tr><tr><td><strong>saveAsHadoopFiles</strong>( <em>prefix</em>, [<em>suffix</em>])</td>
			<td>Save this DStream’s contents as Hadoop files. The file name at each batch interval is generated based on <em>prefix</em> and <em>suffix</em>:<em>“prefix-TIME_IN_MS[.suffix]”</em>.</td>
		</tr><tr><td><strong>foreachRDD</strong>(<em>func</em>)</td>
			<td>The most generic output operator that applies a function, <em>func</em>, to each RDD generated from the stream. This function should push the data in each RDD to an external system, such as saving the RDD to files, or writing it over the network to a database. Note that the function <em>func</em> is executed in the driver process running the streaming application, and will usually have RDD actions in it that will force the computation of the streaming RDDs.</td>
		</tr></tbody></table><p>foreachRDD使用注意事项</p>

<p>通常，将数据写到外部系统需要创建一个网络连接。不经意间，你很可能在driver节点创建一个连接对象，然后在试着在executor节点使用这个连接，如下：</p>

<table><tbody><tr><td>
			<pre>
dstream.foreachRDD { rdd =&gt;
  val connection = createNewConnection()  // 在driver节点执行
  rdd.foreach { record =&gt;
    connection.send(record) // 在executor节点执行
  }
}
</pre>
			</td>
		</tr></tbody></table><p>上述代码是错误的，因为创建的连接对象将被序列化然后传输到worker节点，而连接通常是不能在机器之间传递的。这个错误可能显示为序列化错误(连接对象不可序列化)、初始化错误(连接对象需要在worker节点初始化)等等。</p>

<p>改进的办法是在worker节点创建连接对象。然而，这可能导致另一个常见错误——为每一条记录创建一个连接，如下：</p>

<table><tbody><tr><td>
			<pre>
dstream.foreachRDD { rdd =&gt;
  rdd.foreach { record =&gt;
    val connection = createNewConnection()
    connection.send(record)
    connection.close()
  }
}
</pre>
			</td>
		</tr></tbody></table><p>为每一条记录创建一个连接将消耗掉大量系统资源，极大地降低了系统效率。一个更好的方法是使用<code>rdd.foreachPartition()</code>为每一个RDD分区创建一个连接，如下：</p>

<table><tbody><tr><td>
			<pre>
dstream.foreachRDD { rdd =&gt;
  rdd.foreachPartition { partitionOfRecords =&gt;
    val connection = createNewConnection()
    partitionOfRecords.foreach(record =&gt; connection.send(record))
    connection.close()
  }
}
</pre>
			</td>
		</tr></tbody></table><p>最后，还可以进一步优化——在多个批数据(RDD)之间重用连接对象。我们可以通过一个静态的lazy的连接池来保存连接，以供在多批数据之间共用连接对象，如下：</p>

<table><tbody><tr><td>
			<pre>
dstream.foreachRDD { rdd =&gt;
  rdd.foreachPartition { partitionOfRecords =&gt;
    // 连接池需是静态的，并且初始化需是lazy的
    val connection = ConnectionPool.getConnection()
    partitionOfRecords.foreach(record =&gt; connection.send(record))
    ConnectionPool.returnConnection(connection)  // 将连接归还，以便将来重用
  }
}
</pre>
			</td>
		</tr></tbody></table><p>注意，上述连接池中的连接应该是按需创建的(lazy的)，并且最好将长期不用的连接关闭(超时机制)。</p>

<blockquote>
<ul><li>DStream的转换操作是lazy的，输出操作触发实质的计算。具体地说是DStream输出操作内部的RDD行动操作强制处理接收到的数据。因此，如果一个程序没有任何输出操作，或者有像foreachRDD()这样的输出操作但其中没有任何RDD行动操作，那么该程序就不会执行任何计算，它将简单地接收数据然后丢弃掉。</li>
	<li>缺省情况下，输出操作一次执行一个，并且是按照应用程序定义的前后顺序执行的。</li>
</ul></blockquote>

<h2 id="u7D2F_u52A0_u5668_u548C_u5E7F_u64AD_u53D8_u91CF"><a name="t12"></a>累加器和广播变量</h2>

<p><strong>在Spark Streaming中，累加器(Accumulator)和广播变量(Broadcast)不能从检查点(checkpoint)中恢复。如果你采用检查点机制(检查点将切断RDD依赖)并且也用了累加器或广播变量，为了在突发异常并重启driver节点之后累加器和广播变量可以被重新实例化，你应该为它们创建lazy实例化的单例对象。</strong>示例如下：</p>

<table><tbody><tr><td>
			<pre>
object WordBlacklist {

  @volatile private var instance: Broadcast[Seq[String]] = null

  def getInstance(sc: SparkContext): Broadcast[Seq[String]] = {
    if (instance == null) {
      synchronized {
        if (instance == null) {
          val wordBlacklist = Seq("a", "b", "c")
          instance = sc.broadcast(wordBlacklist)
        }
      }
    }
    instance
  }
}

object DroppedWordsCounter {

  @volatile private var instance: Accumulator[Long] = null

  def getInstance(sc: SparkContext): Accumulator[Long] = {
    if (instance == null) {
      synchronized {
        if (instance == null) {
          instance = sc.accumulator(0L, "WordsInBlacklistCounter")
        }
      }
    }
    instance
  }
}

wordCounts.foreachRDD((rdd: RDD[(String, Int)], time: Time) =&gt; {
  // 获取或注册blacklist广播变量
  val blacklist = WordBlacklist.getInstance(rdd.sparkContext)
  // 获取或注册droppedWordsCounter累加器
  val droppedWordsCounter = DroppedWordsCounter.getInstance(rdd.sparkContext)
  // 根据blacklist来移除words，并用droppedWordsCounter来计数
  val counts = rdd.filter { case (word, count) =&gt;
    if (blacklist.value.contains(word)) {
      droppedWordsCounter += count
      false
    } else {
      true
    }
  }.collect()
  val output = "Counts at time " + time + " " + counts
})
</pre>
			</td>
		</tr></tbody></table><h2 id="DataFrame_u548CSQL_u64CD_u4F5C"><a name="t13"></a>DataFrame和SQL操作</h2>

<p>在流数据处理中也可以使用DataFrame和SQL。此时你必须用StreamingContext正在使用的SparkContext实例来创建一个SQLContext。为了使程序可以在driver故障重启之后可以继续运行，我们应该创建一个lazy实例化的SQLContext的单例对象。示例如下：</p>

<table><tbody><tr><td>
			<pre>
/** 在Spark Streaming程序中进行DataFrame操作 */

val words: DStream[String] = ...

words.foreachRDD { rdd =&gt;

  // 获取SQLContext的单例对象
  val sqlContext = SQLContext.getOrCreate(rdd.sparkContext)
  import sqlContext.implicits._

  val wordsDataFrame = rdd.toDF("word")
  wordsDataFrame.registerTempTable("words")
  val wordCountsDataFrame = 
    sqlContext.sql("select word, count(*) as total from words group by word")
  wordCountsDataFrame.show()
}
</pre>
			</td>
		</tr></tbody></table><h2 id="MLlib_u64CD_u4F5C"><a name="t14"></a>MLlib操作</h2>

<p>你也可以在流数据中使用由MLlib提供的机器学习算法。首先你要知道的是，有一些流式机器学习算法(例如Streaming Linear Regression、Streaming KMeans等)，它们可以从流数据中学习得到模型，也可以将学到的模型应用到流数据中。除此之外，对于大量的机器学习算法，你可以通过历史数据离线地学习得到一个模型，并将模型应用到在线的流数据中。</p>

<h2 id="u6301_u4E45_u5316_28_u7F13_u5B58_29"><a name="t15"></a>持久化(缓存)</h2>

<p>和RDD相似，DStream也允许将流数据持久化，简单地在DStream上调用<code>persist()</code>将自动地将其代表的每一个RDD缓存下来。如果同一个DStream中的数据要被使用多次，将DStream缓存下来将是非常有益的。</p>

<p>对于window-based操作(如reduceByWindow、reduceByKeyAndWindow等)和state-based操作(如updateStateByKey)，DStream将被隐式地持久化，因此你就不必自己手动调用<code>persist()</code>了哦。</p>

<p>对于从网络获取数据的情况(如TCP连接、Kafka、Flume等)，出于容错的考虑，缺省的持久化级别是将数据复制到两个节点。</p>

<p>注意：和RDD不同的是，DStream的缺省持久化级别是将数据序列化并存储到内存中。</p>

<h2 id="u68C0_u67E5_u70B9"><a name="t16"></a>检查点</h2>

<p>流处理程序通常是7*24小时不间断运行的，因此必须是可以从故障中恢复的。为了可以从故障中恢复，Spark Streaming需要在可容错的存储系统中checkpoint足够的信息。有两类数据被checkpoint。</p>

<ul><li><strong>Metadata checkpointing</strong>: 将定义流计算的信息保存到可容错的存储系统中，如HDFS。这被用作Spark Streaming driver节点从故障中恢复。Metadata包括：Streaming程序的配置信息、作用在DStream上的操作集合、还未处理完的batch。</li>
	<li><strong>Data checkpointing</strong>: 将产生的RDD保存到可靠的存储系统，如HDFS。这对于一些stateful的转换操作(如updateStateByKey、reduceByKeyAndWindow等)是必须的，这些操作需要结合多个batch的数据来进行处理，新产生的RDD依赖于先前几个batch对应的RDD，这将导致依赖链随着时间持续变长。为了避免无边界的增长，stateful转换操作的中间结果(RDD)会被周期性地checkpoint到可靠的存储系统中，这样就可以切断依赖链了。</li>
</ul><p><strong>何时启用Checkpointing：</strong></p>

<ul><li><strong>使用了stateful转换操作</strong>：这种情况下必须配置checkpoint目录，以便周期性的RDD checkpointing。</li>
	<li><strong>driver节点从故障恢复</strong>：如果你想使driver节点可从故障恢复，就要配置checkpoint目录。如果无所谓故障恢复，checkpoint目录就不是必须的，此时如果程序中没有stateful转换操作，那么就无需配置checkpoint目录。</li>
</ul><p><strong>怎样配置Checkpointing：</strong></p>

<p>可以使用<code>ssc.checkpoint()</code>来设置checkpoint目录，这样你就可以在程序中使用stateful的转换操作了，如果你想使程序可以从driver节点的故障中恢复，你应该重写你的程序以支持以下行为：</p>

<ul><li>当程序首次启动，创建一个StreamingContext，并在设置一些东东之后调用<code>start()</code>。</li>
	<li>当程序从故障中恢复，从checkpoint数据中重建一个StreamingContext。</li>
</ul><p>上述行为可以通过<code>ssc.getOrCreate()</code>来辅助实现，示例如下：</p>

<table><tbody><tr><td>
			<pre>
// 创建并设置一个新的StreamingContext对象
def functionToCreateContext(): StreamingContext = {
    val ssc = new StreamingContext(...)
    val lines = ssc.socketTextStream(...)
    ...
    ssc.checkpoint(checkpointDirectory)
    ssc
}

// 如果检查点存在，则根据检查点创建一个StreamingContext；
// 否则，根据提供的函数创建一个新的StreamingContext。
val context = 
  StreamingContext.getOrCreate(checkpointDirectory, functionToCreateContext _)

// 在这里做一些额外的设置。这里的设置和首次启动还是故障恢复无关。
context. ...

// 开始运行
context.start()
context.awaitTermination()
</pre>
			</td>
		</tr></tbody></table><p>需要注意的是，checkpoint的时间间隔需要仔细考虑，过小或过大的时间间隔都可能导致问题。通常，checkpoint的时间间隔最好是DStream的批处理时间间隔的5-10倍。<code>dstream.checkpoint()</code>可用来设置checkpoint的时间间隔，同时对于那些没有默认地进行checkpointing的DStream(非stateful转换操作生成的DStream)，这也将引起周期性地checkpoint该DStream中的RDD。</p>

<h2 id="u76D1_u63A7_u5E94_u7528"><a name="t17"></a>监控应用</h2>

<p>除了Spark提供的一些监控能力外，Spark Streaming还提供了一些额外的监控能力。当一个Spark Streaming程序运行时，Spark应用程序监控页面(通常是 <a href="http://master:4040/" rel="nofollow">http://master:4040</a> )将多出一个<code>Streaming</code>选项卡，其中展示了receiver和已完成的batch的统计信息。其中有两个信息非常有用：</p>

<ul><li><strong>Processing Time</strong>: 一批数据的处理时间。</li>
	<li><strong>Scheduling Delay</strong>: 一批数据在队列中等待的时间。</li>
</ul><p>如果处理一批数据的时间持续高出批处理间隔，或者等待时间持续增加，通常意味着你的系统的处理速度跟不上数据产生的速度。此时，你可以考虑削减批数据的处理时间，参见性能调优部分。</p>

<p>你也可以通过<code>StreamingListener</code>接口来监听Spark Streaming程序的执行状况，包括：receiver状态、处理时间等等。</p>

<h1 id="u6027_u80FD_u8C03_u4F18"><a name="t18"></a>性能调优</h1>

<h2 id="u524A_u51CF_u6279_u6570_u636E_u5904_u7406_u65F6_u95F4"><a name="t19"></a>削减批数据处理时间</h2>

<h3 id="u6570_u636E_u63A5_u6536_u7684_u5E76_u884C_u5EA6"><a name="t20"></a>数据接收的并行度</h3>

<p>通过网络接收的数据(Kafka、Flume、socket等)需要经过解序列化然后存储在Spark中。如果数据接收成为瓶颈，你就需要考虑增加数据接收的并行度。注意每一个Input DStream只会创建一个receiver(运行在worker节点)用于接收一个数据流。你可以创建多个Input DStream并配置它们来接收数据源的不同部分以增加数据接收并行度。</p>

<p>例如，如果一个Kafka input DStream接收两个主题的数据导致系统瓶颈的话，可以将Kafka输入流划分为两个，然后创建两个Input DStream，每一个接收一个主题的数据流。这样的话数据接收就可以并行进行了，从而增加了系统的吞吐量。这两个DStream可以被union成为一个单一的DStream，后续的转换操作将作用在统一的数据流之上。示例如下：</p>

<table><tbody><tr><td>
			<pre>
val numStreams = 5
val kafkaStreams = (1 to numStreams).map { i =&gt; KafkaUtils.createStream(...) }
val unifiedStream = streamingContext.union(kafkaStreams)
unifiedStream.print()
</pre>
			</td>
		</tr></tbody></table><h3 id="u6570_u636E_u5904_u7406_u7684_u5E76_u884C_u5EA6"><a name="t21"></a>数据处理的并行度</h3>

<p>另一个需要考虑的是receiver的数据块划分间隔，这可以通过<code>spark.streaming.blockInterval</code>进行设置。receiver会将接收到的数据合并为数据块然后存储到Spark内存中。每一批数据中数据块的数量决定了Task的数量(通常大约是：批处理间隔/块间隔)。过少的Task数将导致集群资源利用率降低，如果出现这种情况，你应该试着去减小块划分间隔，我们推荐的块划分间隔的最小值是大约50ms，过小的话也将导致一些问题。</p>

<p>另一个增加并行度的方法是在处理数据之前，使用<code>inputStream.repartition()</code>明确地将Input DStream重新分区，这样新形成的DStream中的RDD都将有指定数量的分区。</p>

<p>对于一些操作，如<code>reduceByKey()</code>和<code>reduceByKeyAndWindow()</code>等，也可以传递一个分区数参数来控制计算的并行度。</p>

<h3 id="u6570_u636E_u5E8F_u5217_u5316_u4F18_u5316"><a name="t22"></a>数据序列化优化</h3>

<p>在Spark Streaming程序中，输入数据和持久化的数据默认都经过序列化处理并缓存在内存中。</p>

<p>序列化优化可以明显提高程序的运行效率，参见我的<a href="http://smallx.me/2016/06/07/spark%E4%BD%BF%E7%94%A8%E6%80%BB%E7%BB%93/" rel="nofollow">Spark使用总结</a>一文的序列化部分。</p>

<p>在一些特殊情况下，需要保存在内存中的流数据可能不是很大，这时可以设置存储级别以非序列化的形式存储在内存中，如果这不会引起过大的GC开销，那么将提高程序的性能。</p>

<h2 id="u8BBE_u7F6E_u6B63_u786E_u7684_u6279_u5904_u7406_u95F4_u9694"><a name="t23"></a>设置正确的批处理间隔</h2>

<p>批处理间隔的设置对流处理程序是非常关键的，这可能影响到输入流能否被迅速地流畅地持续地处理。</p>

<p>恰当的批处理间隔通常和数据产生速度以及集群计算能力相关。通常来说，如果我们想了解一个流处理程序的处理速度能否跟得上数据产生的速度，可以查看Spark应用监控页面( <a href="http://master:4040/" rel="nofollow">http://master:4040</a> )。对于一个稳定的流处理程序来说，批处理时间(Processing Time)应该小于设置的批处理间隔时间(Batch Interval)，并且Batch的调度延迟时间(Scheduling Delay)是相对平稳的(持续增加就意味着跟不上数据产生速度了，但瞬时的增加并不意味着什么)。</p>

<p>已经证实，对于大多数应用来说，500ms是比较好的最小批处理间隔。</p>

<h2 id="u5185_u5B58_u8C03_u4F18"><a name="t24"></a>内存调优</h2>

<p>内存优化对于一个Spark Streaming程序来说也很重要，这主要包括：内存使用优化以及GC优化。关于内存调优参见<a href="http://spark.apache.org/docs/latest/streaming-programming-guide.html#memory-tuning" rel="nofollow">Spark Streaming官方文档内存调优部分</a>。</p>            </div>
                </div>