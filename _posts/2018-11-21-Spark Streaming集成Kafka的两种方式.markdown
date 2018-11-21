---
layout:     post
title:      Spark Streaming集成Kafka的两种方式
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_37142346/article/details/80300976				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="spark-streaming集成kafka">Spark Streaming集成Kafka</h2>

<p>转载请标明原文链接：<a href="https://blog.csdn.net/qq_37142346/article/details/80300976" rel="nofollow">原文链接</a></p>

<p>在实际开发中Spark Streaming经常会结合Flume以及Kafka来实时计算数据。这篇文章主要讲述如何集成Kafka，对于Kafka的集成有两种方式：</p>

<ol>
<li>Receiver-based Approach</li>
<li>Direct Approach (No Receivers)</li>
</ol>

<p>在集成Kafka之前，必须先安装kafka并且启动，不了解kafka怎么启动的读者可以参照笔者的笔记<a href="https://github.com/ljcan/jqBlogs/wiki/Kafka%E7%9A%84%E4%BD%BF%E7%94%A8%E4%BB%A5%E5%8F%8A%E5%8E%9F%E7%90%86" rel="nofollow">Kafka的使用</a>，在启动kafka后，下面来看看如何与Spark SStreaming集成。</p>

<p><strong>1.Receiver-based Approach（接受者模式）</strong></p>

<blockquote>
  <p>This approach uses a Receiver to receive the data. The Received is implemented using the Kafka high-level consumer API. As with all receivers, the data received from Kafka through a Receiver is stored in Spark executors, and then jobs launched by Spark Streaming processes the data.</p>
</blockquote>

<p>这种方式使用一个Receiver 来接收数据，它可以使用kafka API来消费数据并且进行统计，接收到的数据存储在Spark Executor 中的Receiver 上，然后使用Spark Streaming对数据进行分析，不过这种方式当节点发生错误的时候数据容易丢失。</p>

<p>1.1导入相关jar包：</p>

<pre class="prettyprint"><code class=" hljs avrasm">bin/spark-shell --master local[<span class="hljs-number">2</span>] --jars \
/opt/cdh-<span class="hljs-number">5.3</span><span class="hljs-number">.6</span>/spark-<span class="hljs-number">1.3</span><span class="hljs-number">.0</span>-bin-<span class="hljs-number">2.5</span><span class="hljs-number">.0</span>-cdh5<span class="hljs-number">.3</span><span class="hljs-number">.6</span>/externallibs/spark-streaming-kafka_2<span class="hljs-number">.10</span>-<span class="hljs-number">1.3</span><span class="hljs-number">.0</span><span class="hljs-preprocessor">.jar</span>,/opt/cdh-<span class="hljs-number">5.3</span><span class="hljs-number">.6</span>/spark-<span class="hljs-number">1.3</span><span class="hljs-number">.0</span>-bin-<span class="hljs-number">2.5</span><span class="hljs-number">.0</span>-cdh5<span class="hljs-number">.3</span><span class="hljs-number">.6</span>/externallibs/kafka_2<span class="hljs-number">.10</span>-<span class="hljs-number">0.8</span><span class="hljs-number">.2</span><span class="hljs-number">.1</span><span class="hljs-preprocessor">.jar</span>,/opt/cdh-<span class="hljs-number">5.3</span><span class="hljs-number">.6</span>/spark-<span class="hljs-number">1.3</span><span class="hljs-number">.0</span>-bin-<span class="hljs-number">2.5</span><span class="hljs-number">.0</span>-cdh5<span class="hljs-number">.3</span><span class="hljs-number">.6</span>/externallibs/kafka-clients-<span class="hljs-number">0.8</span><span class="hljs-number">.2</span><span class="hljs-number">.1</span><span class="hljs-preprocessor">.jar</span>,/opt/cdh-<span class="hljs-number">5.3</span><span class="hljs-number">.6</span>/spark-<span class="hljs-number">1.3</span><span class="hljs-number">.0</span>-bin-<span class="hljs-number">2.5</span><span class="hljs-number">.0</span>-cdh5<span class="hljs-number">.3</span><span class="hljs-number">.6</span>/externallibs/zkclient-<span class="hljs-number">0.3</span><span class="hljs-preprocessor">.jar</span>,/opt/cdh-<span class="hljs-number">5.3</span><span class="hljs-number">.6</span>/spark-<span class="hljs-number">1.3</span><span class="hljs-number">.0</span>-bin-<span class="hljs-number">2.5</span><span class="hljs-number">.0</span>-cdh5<span class="hljs-number">.3</span><span class="hljs-number">.6</span>/externallibs/metrics-core-<span class="hljs-number">2.2</span><span class="hljs-number">.0</span><span class="hljs-preprocessor">.jar</span></code></pre>

<p>1.2编写scala脚本（DirectKafkaWordCount.scala）：</p>



<pre class="prettyprint"><code class=" hljs avrasm">import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.HashMap</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span>._
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span>._
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span><span class="hljs-preprocessor">.StreamingContext</span>._ 
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span><span class="hljs-preprocessor">.kafka</span>._

val ssc = new StreamingContext(sc, Seconds(<span class="hljs-number">5</span>))

val topicMap = Map(<span class="hljs-string">"test"</span> -&gt; <span class="hljs-number">1</span>)

// read data
val lines = KafkaUtils<span class="hljs-preprocessor">.createStream</span>(ssc, <span class="hljs-string">"hadoop-senior.shinelon.com:2181"</span>, <span class="hljs-string">"testWordCountGroup"</span>, topicMap)<span class="hljs-preprocessor">.map</span>(_._2)

val words = lines<span class="hljs-preprocessor">.flatMap</span>(_<span class="hljs-preprocessor">.split</span>(<span class="hljs-string">" "</span>))
val wordCounts = words<span class="hljs-preprocessor">.map</span>(<span class="hljs-built_in">x</span> =&gt; (<span class="hljs-built_in">x</span>, <span class="hljs-number">1</span>))<span class="hljs-preprocessor">.reduceByKey</span>(_ + _)
wordCounts<span class="hljs-preprocessor">.print</span>()

ssc<span class="hljs-preprocessor">.start</span>()             // Start the computation
ssc<span class="hljs-preprocessor">.awaitTermination</span>()  // Wait for the computation to terminate</code></pre>

<p>1.3提交脚本(脚本的目录)：</p>



<pre class="prettyprint"><code class=" hljs ruby">&gt;scala <span class="hljs-symbol">:load</span> /opt/spark/<span class="hljs-constant">DirectKafkaWordCount</span>.scala</code></pre>

<p>这时Spark Streaming已经启动成功，上面是对kafka生产者产生的数据进行单词统计，当我们在kafka生产者中生产消息时，在Spark Streaming中就可以统计到单词数，运行结果如下图：</p>

<p>kafka中产生数据： <br>
<img src="https://img-blog.csdn.net/20180513170331835?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MTQyMzQ2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
Spark Streaming中接收数据并进行统计： <br>
<img src="https://img-blog.csdn.net/20180513170409704?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MTQyMzQ2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p><strong>2.Direct Approach (No Receivers)</strong></p>

<blockquote>
  <p>this approach periodically queries Kafka for the latest offsets in each topic+partition, and accordingly defines the offset ranges to process in each batch. When the jobs to process the data are launched, Kafka’s simple consumer API is used to read the defined ranges of offsets from Kafka (similar to read files from a file system). Note that this is an experimental feature in Spark 1.3 and is only available in the Scala and Java API.</p>
</blockquote>

<p>2.1，导入相关包： <br>
这种方式集成Kafka也需要导入相应的包，和上面导入方式一样。</p>

<p>2.1编写scala脚本：</p>



<pre class="prettyprint"><code class=" hljs avrasm">import kafka<span class="hljs-preprocessor">.serializer</span><span class="hljs-preprocessor">.StringDecoder</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span>._
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span>._
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span><span class="hljs-preprocessor">.StreamingContext</span>._ 
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span><span class="hljs-preprocessor">.kafka</span>._

val ssc = new StreamingContext(sc, Seconds(<span class="hljs-number">5</span>))
//需要制定kafka集群
val kafkaParams = Map[String, String](<span class="hljs-string">"metadata.broker.list"</span> -&gt; <span class="hljs-string">"hadoop-senior.shinelon.com:9092"</span>)
//制定topic
val topicsSet = <span class="hljs-keyword">Set</span>(<span class="hljs-string">"test"</span>)

// read data
val messages = KafkaUtils<span class="hljs-preprocessor">.createDirectStream</span>[String, String, StringDecoder, StringDecoder](ssc, kafkaParams, topicsSet)

val lines = messages<span class="hljs-preprocessor">.map</span>(_._2)
val words = lines<span class="hljs-preprocessor">.flatMap</span>(_<span class="hljs-preprocessor">.split</span>(<span class="hljs-string">" "</span>))
val wordCounts = words<span class="hljs-preprocessor">.map</span>(<span class="hljs-built_in">x</span> =&gt; (<span class="hljs-built_in">x</span>, <span class="hljs-number">1</span>))<span class="hljs-preprocessor">.reduceByKey</span>(_ + _)
wordCounts<span class="hljs-preprocessor">.print</span>()

ssc<span class="hljs-preprocessor">.start</span>()             // Start the computation
ssc<span class="hljs-preprocessor">.awaitTermination</span>()  // Wait for the computation to terminate</code></pre>

<p>2.3执行脚本：</p>



<pre class="prettyprint"><code class=" hljs ruby">&gt;scala <span class="hljs-symbol">:load</span> /opt/spark/<span class="hljs-constant">DirectKafkaWordCount</span>.scala</code></pre>

<p>运行结果如下所示：</p>

<p>kafka生产数据： <br>
<img src="https://img-blog.csdn.net/20180513171351416?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MTQyMzQ2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
Spark Streaming消费数据并且进行统计： <br>
<img src="https://img-blog.csdn.net/20180513171431656?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MTQyMzQ2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>上面是Spark Streaming集成kafka的两种方式，不过细心的读者会发现，上面的两种方式并不是对整个数据进行统计而是来一部分数据只统计当前产生的数据，在实际生产中，我们常常需要统计整个状态下数据，要对生产生产的所有数据进行统计，而不是某一次生产的数据。这里就需要使用Spark的一个transformation操作：updateStateByKey(func)函数 <br>
<img src="https://img-blog.csdn.net/20180513172119386?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MTQyMzQ2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>当我们翻阅spark的源码中的MappedDstream这个类时并不能发现它的updateStateByKey(func)函数，因为这里使用了scala的一个隐式转换，因此这个函数可以在Spark源码中的PairDStreamFunctions类找到，该函数源码如下：</p>



<pre class="prettyprint"><code class=" hljs applescript">/**
   * Return a new <span class="hljs-string">"state"</span> DStream <span class="hljs-keyword">where</span> <span class="hljs-keyword">the</span> state <span class="hljs-keyword">for</span> each key <span class="hljs-keyword">is</span> updated <span class="hljs-keyword">by</span> applying
   * <span class="hljs-keyword">the</span> <span class="hljs-keyword">given</span> function <span class="hljs-function_start"><span class="hljs-keyword">on</span></span> <span class="hljs-keyword">the</span> previous state <span class="hljs-keyword">of</span> <span class="hljs-keyword">the</span> key <span class="hljs-keyword">and</span> <span class="hljs-keyword">the</span> new values <span class="hljs-keyword">of</span> each key.
   * Hash partitioning <span class="hljs-keyword">is</span> used <span class="hljs-keyword">to</span> generate <span class="hljs-keyword">the</span> RDDs <span class="hljs-keyword">with</span> Spark's default <span class="hljs-type">number</span> <span class="hljs-keyword">of</span> partitions.
   * @param updateFunc State update function. If `this` function returns None, <span class="hljs-keyword">then</span>
   *                   corresponding state key-value pair will be eliminated.
   * @tparam S State type
   */
  def updateStateByKey[S: ClassTag](
      updateFunc: (Seq[V], Option[S]) =&gt; Option[S]
    ): DStream[(K, S)] = ssc.withScope {
    updateStateByKey(updateFunc, defaultPartitioner())
  }</code></pre>

<p>使用这个API需要定义一个函数，如下所示：</p>



<pre class="prettyprint"><code class=" hljs perl">val updateFunc = (<span class="hljs-keyword">values</span>: Se<span class="hljs-string">q[Int]</span>, <span class="hljs-keyword">state</span>: Option[Int]) =&gt; {
    val currentCount = <span class="hljs-keyword">values</span>.sum
    val previousCount = <span class="hljs-keyword">state</span>.getOrElse(<span class="hljs-number">0</span>)
    Some(currentCount + previousCount)
}</code></pre>

<p>这个函数就是将当前值与之前的数据累加起来返回。</p>

<p>完整的脚本如下所示：</p>



<pre class="prettyprint"><code class=" hljs markdown">import kafka.serializer.StringDecoder
import org.apache.spark._
import org.apache.spark.streaming._
import org.apache.spark.streaming.StreamingContext._ 
import org.apache.spark.streaming.kafka._

val ssc = new StreamingContext(sc, Seconds(5))
ssc.checkpoint(".")

val kafkaParams = Map[<span class="hljs-link_label">String, String</span>](<span class="hljs-link_url">"metadata.broker.list" -&gt; "hadoop-senior.shinelon.com:9092"</span>)
val topicsSet = Set("test")

val updateFunc = (values: Seq[Int], state: Option[Int]) =&gt; {
<span class="hljs-code">    val currentCount = values.sum</span>
<span class="hljs-code">    val previousCount = state.getOrElse(0)</span>
<span class="hljs-code">    Some(currentCount + previousCount)</span>
}

// read data
val messages = KafkaUtils.createDirectStream[<span class="hljs-link_label">String, String, StringDecoder, StringDecoder</span>](<span class="hljs-link_url">ssc, kafkaParams, topicsSet</span>)

val lines = messages.map(<span class="hljs-emphasis">_._</span>2)
val words = lines.flatMap(_.split(" "))
val wordDstream = words.map(x =&gt; (x, 1))

val stateDstream = wordDstream.updateStateByKey[<span class="hljs-link_label">Int</span>](<span class="hljs-link_url">updateFunc</span>)

stateDstream.print()

ssc.start()             // Start the computation
ssc.awaitTermination()  // Wait for the computation to terminate</code></pre>

<p>然后可以使用上面的load方式运行这个脚本就可以实时的统计整个状态下kafka生产者生产的数据，而不只是某一次产生的数据。</p>

<p>——————————————————-结束符——————————————————–</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>