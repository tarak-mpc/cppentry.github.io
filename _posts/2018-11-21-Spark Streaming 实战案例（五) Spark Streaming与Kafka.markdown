---
layout:     post
title:      Spark Streaming 实战案例（五) Spark Streaming与Kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2 id="主要内容">主要内容</h2>
<ol><li>Spark Streaming与Kafka版的WordCount示例（一）</li><li>Spark Streaming与Kafka版的WordCount示例（二）</li></ol><h2 id="1-spark-streaming与kafka版本的wordcount示例-一"><a name="t1"></a>1. Spark Streaming与Kafka版本的WordCount示例 （一）</h2>
<ol><li>启动kafka集群</li></ol><pre class="prettyprint"><code class="hljs ruby has-numbering">root<span class="hljs-variable">@sparkslave02</span><span class="hljs-symbol">:/hadoopLearning/kafka_2</span>.<span class="hljs-number">10</span>-<span class="hljs-number">0</span>.<span class="hljs-number">8.2</span>.<span class="hljs-number">1</span><span class="hljs-comment"># bin/kafka-server-start.sh config/server.properties </span>
root<span class="hljs-variable">@sparkslave01</span><span class="hljs-symbol">:/hadoopLearning/kafka_2</span>.<span class="hljs-number">10</span>-<span class="hljs-number">0</span>.<span class="hljs-number">8.2</span>.<span class="hljs-number">1</span><span class="hljs-comment"># bin/kafka-server-start.sh config/server.properties </span>
root<span class="hljs-variable">@sparkmaster</span><span class="hljs-symbol">:/hadoopLearning/kafka_2</span>.<span class="hljs-number">10</span>-<span class="hljs-number">0</span>.<span class="hljs-number">8.2</span>.<span class="hljs-number">1</span><span class="hljs-comment"># bin/kafka-server-start.sh config/server.properties </span></code></pre>
<p>向kafka集群发送消息</p>
<pre class="prettyprint"><code class="hljs ruby has-numbering">root<span class="hljs-variable">@sparkslave01</span><span class="hljs-symbol">:/hadoopLearning/kafka_2</span>.<span class="hljs-number">10</span>-<span class="hljs-number">0</span>.<span class="hljs-number">8.2</span>.<span class="hljs-number">1</span><span class="hljs-comment"># bin/kafka-console-producer.sh --broker-list sparkslave01:9092 --sync --topic kafkatopictest</span></code></pre>
<ol><li>编写如下程序</li></ol><pre class="prettyprint"><code class="hljs avrasm has-numbering">import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.clients</span><span class="hljs-preprocessor">.producer</span>.{ProducerConfig, KafkaProducer, ProducerRecord}
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.log</span>4j.{Level, Logger}

import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span>._
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span><span class="hljs-preprocessor">.kafka</span>._
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span>.{Logging, SparkConf}

object KafkaWordCount {
  def main(args: Array[String]) {
    if (args<span class="hljs-preprocessor">.length</span> &lt; <span class="hljs-number">4</span>) {
      System<span class="hljs-preprocessor">.err</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"Usage: KafkaWordCount &lt;zkQuorum&gt; &lt;group&gt; &lt;topics&gt; &lt;numThreads&gt;"</span>)
      System<span class="hljs-preprocessor">.exit</span>(<span class="hljs-number">1</span>)
    }
    StreamingExamples<span class="hljs-preprocessor">.setStreamingLogLevels</span>()

    val Array(zkQuorum, group, topics, numThreads) = args
    val sparkConf = new SparkConf()<span class="hljs-preprocessor">.setAppName</span>(<span class="hljs-string">"KafkaWordCount"</span>)<span class="hljs-preprocessor">.setMaster</span>(<span class="hljs-string">"local[4]"</span>)
    val ssc = new StreamingContext(sparkConf, Seconds(<span class="hljs-number">2</span>))
    ssc<span class="hljs-preprocessor">.checkpoint</span>(<span class="hljs-string">"checkpoint"</span>)

    val topicMap = topics<span class="hljs-preprocessor">.split</span>(<span class="hljs-string">","</span>)<span class="hljs-preprocessor">.map</span>((_, numThreads<span class="hljs-preprocessor">.toInt</span>))<span class="hljs-preprocessor">.toMap</span>
    //创建ReceiverInputDStream
    val lines = KafkaUtils<span class="hljs-preprocessor">.createStream</span>(ssc, zkQuorum, group, topicMap)<span class="hljs-preprocessor">.map</span>(_._2)
    val words = lines<span class="hljs-preprocessor">.flatMap</span>(_<span class="hljs-preprocessor">.split</span>(<span class="hljs-string">" "</span>))
    val wordCounts = words<span class="hljs-preprocessor">.map</span>(<span class="hljs-built_in">x</span> =&gt; (<span class="hljs-built_in">x</span>, <span class="hljs-number">1</span>L))
      <span class="hljs-preprocessor">.reduceByKeyAndWindow</span>(_ + _, _ - _, Minutes(<span class="hljs-number">10</span>), Seconds(<span class="hljs-number">2</span>), <span class="hljs-number">2</span>)
    wordCounts<span class="hljs-preprocessor">.print</span>()

    ssc<span class="hljs-preprocessor">.start</span>()
    ssc<span class="hljs-preprocessor">.awaitTermination</span>()
  }
}</code></pre>
<p>配置运行参数： <br><img src="https://img-blog.csdn.net/20151203231200487" alt="这里写图片描述" title=""><br>
具体如下：</p>
<pre class="prettyprint"><code class="hljs lasso has-numbering">sparkmaster:<span class="hljs-number">2181</span>  test<span class="hljs-attribute">-consumer</span><span class="hljs-attribute">-group</span> kafkatopictest <span class="hljs-number">1</span></code></pre>
<p>sparkmaster:2181，zookeeper监听地址 <br>
test-consumer-group， consumer-group的名称，必须和$KAFKA_HOME/config/consumer.properties中的group.id的配置内容一致
<br>
kafkatopictest，topic名称 <br>
1，线程数</p>
<p>运行KafkaWordCount 后，在producer中输入下列内容</p>
<pre class="prettyprint"><code class="hljs ruby has-numbering">root<span class="hljs-variable">@sparkslave01</span><span class="hljs-symbol">:/hadoopLearning/kafka_2</span>.<span class="hljs-number">10</span>-<span class="hljs-number">0</span>.<span class="hljs-number">8.2</span>.<span class="hljs-number">1</span><span class="hljs-comment"># bin/kafka-console-producer.sh --broker-list sparkslave01:9092 --sync --topic kafkatopictest</span>
[<span class="hljs-number">2015</span>-<span class="hljs-number">11</span>-<span class="hljs-number">04</span> <span class="hljs-number">03</span><span class="hljs-symbol">:</span><span class="hljs-number">25</span><span class="hljs-symbol">:</span><span class="hljs-number">39</span>,<span class="hljs-number">666</span>] <span class="hljs-constant">WARN</span> <span class="hljs-constant">Property</span> topic is <span class="hljs-keyword">not</span> valid (kafka.utils.<span class="hljs-constant">VerifiableProperties</span>)
<span class="hljs-constant">Spark</span>
<span class="hljs-constant">Spark</span> <span class="hljs-constant">TEST</span>
<span class="hljs-constant">TEST</span> <span class="hljs-constant">Spark</span> <span class="hljs-constant">Streaming</span>
</code></pre>
<p><img src="https://img-blog.csdn.net/20151203231618978" alt="这里写图片描述" title=""></p>
<p>得到结果如下： <br><img src="https://img-blog.csdn.net/20151203231723359" alt="这里写图片描述" title=""></p>
<h2 id="2-spark-streaming与kafka版本的wordcount示例二"><a name="t2"></a>2. Spark Streaming与Kafka版本的WordCount示例（二）</h2>
<p>前面的例子中，producer是通过kafka的脚本生成的，本例中将给出通过编写程序生成的producer</p>
<pre class="prettyprint"><code class="hljs avrasm has-numbering">// 随机生成<span class="hljs-number">1</span>-<span class="hljs-number">100</span>间的数字
object KafkaWordCountProducer {

  def main(args: Array[String]) {
    if (args<span class="hljs-preprocessor">.length</span> &lt; <span class="hljs-number">4</span>) {
      System<span class="hljs-preprocessor">.err</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"Usage: KafkaWordCountProducer &lt;metadataBrokerList&gt; &lt;topic&gt; "</span> +
        <span class="hljs-string">"&lt;messagesPerSec&gt; &lt;wordsPerMessage&gt;"</span>)
      System<span class="hljs-preprocessor">.exit</span>(<span class="hljs-number">1</span>)
    }

    val Array(brokers, topic, messagesPerSec, wordsPerMessage) = args

    // Zookeeper连接属性配置
    val props = new HashMap[String, Object]()
    props<span class="hljs-preprocessor">.put</span>(ProducerConfig<span class="hljs-preprocessor">.BOOTSTRAP</span>_SERVERS_CONFIG, brokers)
    props<span class="hljs-preprocessor">.put</span>(ProducerConfig<span class="hljs-preprocessor">.VALUE</span>_SERIALIZER_CLASS_CONFIG,
      <span class="hljs-string">"org.apache.kafka.common.serialization.StringSerializer"</span>)
    props<span class="hljs-preprocessor">.put</span>(ProducerConfig<span class="hljs-preprocessor">.KEY</span>_SERIALIZER_CLASS_CONFIG,
      <span class="hljs-string">"org.apache.kafka.common.serialization.StringSerializer"</span>)
    //创建KafkaProducer
    val producer = new KafkaProducer[String, String](props)

    // 向kafka集群发送消息
    while(true) {
      (<span class="hljs-number">1</span> to messagesPerSec<span class="hljs-preprocessor">.toInt</span>)<span class="hljs-preprocessor">.foreach</span> { messageNum =&gt;
        val str = (<span class="hljs-number">1</span> to wordsPerMessage<span class="hljs-preprocessor">.toInt</span>)<span class="hljs-preprocessor">.map</span>(<span class="hljs-built_in">x</span> =&gt; scala<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Random</span><span class="hljs-preprocessor">.nextInt</span>(<span class="hljs-number">10</span>)<span class="hljs-preprocessor">.toString</span>)
          <span class="hljs-preprocessor">.mkString</span>(<span class="hljs-string">" "</span>)

        val message = new ProducerRecord[String, String](topic, null, str)
        producer<span class="hljs-preprocessor">.send</span>(message)
      }

      Thread<span class="hljs-preprocessor">.sleep</span>(<span class="hljs-number">1000</span>)
    }
  }

}</code></pre>
<p>KafkaWordCountProducer 运行参数设置如下：</p>
<pre class="prettyprint"><code class="hljs css has-numbering"><span class="hljs-tag">sparkmaster</span><span class="hljs-pseudo">:9092</span> <span class="hljs-tag">kafkatopictest</span> 5 8</code></pre>
<p>sparkmaster:9092，broker-list <br>
kafkatopictest，top名称 <br>
5表示每秒发多少条消息 <br>
8表示每条消息中有几个单词</p>
<p>先KafkaWordCountProducer，然后再运行KafkaWordCount ，得到的计算结果如下： <br><img src="https://img-blog.csdn.net/20151203232434164" alt="这里写图片描述" title=""></p>
            </div>
                </div>