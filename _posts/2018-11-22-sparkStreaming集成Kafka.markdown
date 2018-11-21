---
layout:     post
title:      sparkStreaming集成Kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主编写文章，未经博主允许转载，转载请注明出处。					https://blog.csdn.net/u012373815/article/details/53454669				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>这几天看了spark集成Kafka，消费Kafka数据并向Kafka发送数据，仿照官方样例写了两个小例子。在此分享一下。</p>

<hr>

<ul>
<li>1.添加Kafka的repository</li>
<li>2.DirectKafkaWordCountDemo代码展示</li>
<li>3.kafkaProducer代码展示</li>
<li>4.从Kafka 集群中消费数据并处理后再存入Kafka代码展示 <br>
<hr></li>
</ul>

<p>本案例中使用的Kafka为三个broker一个zookeeper的Kafka集群。 <br>
本案例中原始消息体为“message: 我是第 n 条信息“</p>

<hr>

<h1 id="1添加kafka的repository">1.添加Kafka的repository</h1>

<p>spark中集成Kafka需要引入Kafka的repository，在pom文件中添加如下依赖</p>



<pre class="prettyprint"><code class=" hljs xml">  <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-streaming-kafka-0-8_${scala.version}<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>${spark.version}<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span></code></pre>

<hr>



<h1 id="2directkafka代码展示">2.DirectKafka代码展示</h1>

<p>该段代码，从 Kafka集群中消费原始“message:  我是第n条信息“并把数据进行截断，过滤处理，输出为“我是第n条信息“</p>

<pre class="prettyprint"><code class=" hljs scala"><span class="hljs-keyword">import</span> kafka.serializer.StringDecoder
<span class="hljs-keyword">import</span> org.apache.spark.SparkConf
<span class="hljs-keyword">import</span> org.apache.spark.streaming.kafka.KafkaUtils
<span class="hljs-keyword">import</span> org.apache.spark.streaming.{Seconds, StreamingContext}

<span class="hljs-javadoc">/**
  * Created by yangyibo on 16/12/1.
  */</span>
<span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">DirectKafkaWordCountDemo</span> {</span>
  <span class="hljs-keyword">def</span> main(args: Array[String]) {
    <span class="hljs-keyword">val</span> sprakConf = <span class="hljs-keyword">new</span> SparkConf().setAppName(<span class="hljs-string">"DirectKafkaWordCountDemo"</span>)
    <span class="hljs-comment">//此处在idea中运行时请保证local[2]核心数大于2</span>
    sprakConf.setMaster(<span class="hljs-string">"local[2]"</span>)
    <span class="hljs-keyword">val</span> ssc = <span class="hljs-keyword">new</span> StreamingContext(sprakConf, Seconds(<span class="hljs-number">3</span>))

    <span class="hljs-keyword">val</span> brokers = <span class="hljs-string">"192.168.100.41:9092,192.168.100.42:9092,192.168.100.43:9092"</span>;
    <span class="hljs-keyword">val</span> topics = <span class="hljs-string">"abel"</span>;
    <span class="hljs-keyword">val</span> topicSet = topics.split(<span class="hljs-string">","</span>).toSet
    <span class="hljs-keyword">val</span> kafkaParams = Map[String, String](<span class="hljs-string">"metadata.broker.list"</span> -&gt; brokers)
    <span class="hljs-keyword">val</span> messages = KafkaUtils.createDirectStream[String, String, StringDecoder, StringDecoder](ssc, kafkaParams, topicSet)

    <span class="hljs-keyword">val</span> lines = messages.map(_._2)
    <span class="hljs-keyword">val</span> words = lines.flatMap(_.split(<span class="hljs-string">" "</span>)).filter(!_.equals(<span class="hljs-string">"message:"</span>))
    <span class="hljs-keyword">val</span> wordCounts = words.map(x=&gt;(x, <span class="hljs-number">1</span>l)).reduceByKey(_ + _)
    wordCounts.print()
    ssc.start()
    ssc.awaitTermination()
  }

}
</code></pre>

<hr>



<h1 id="3kafkaproducer代码展示">3.kafkaProducer代码展示</h1>

<p>此段代码吧array 中的字符串当作数据发送到Kafka集群中。</p>

<pre class="prettyprint"><code class=" hljs scala"><span class="hljs-keyword">import</span> java.util.HashMap

<span class="hljs-keyword">import</span> org.apache.kafka.clients.producer.{KafkaProducer, ProducerConfig, ProducerRecord}
<span class="hljs-keyword">import</span> org.apache.spark.{SparkConf, SparkContext}

<span class="hljs-keyword">import</span> scala.collection.mutable.ArrayBuffer

<span class="hljs-javadoc">/**
  * Created by yangyibo on 16/11/29.
  */</span>
<span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">KafkaProducerDemo</span> {</span>
  <span class="hljs-keyword">def</span> main(args: Array[String]): Unit = {
    <span class="hljs-keyword">val</span> conf = <span class="hljs-keyword">new</span> SparkConf().setAppName(<span class="hljs-string">"KafkaProducer"</span>)
    <span class="hljs-keyword">val</span> sc = <span class="hljs-keyword">new</span> SparkContext(conf)
    <span class="hljs-keyword">val</span> array = ArrayBuffer(<span class="hljs-string">"one"</span>,<span class="hljs-string">"tow"</span>,<span class="hljs-string">"three"</span>)    
      kafkaProducer(array)
    }

  <span class="hljs-keyword">def</span> kafkaProducer(args: ArrayBuffer[String]) {
    <span class="hljs-keyword">if</span> (args != <span class="hljs-keyword">null</span>) {
      <span class="hljs-keyword">val</span> brokers = <span class="hljs-string">"192.168.100.41:9092,192.168.100.42:9092,192.168.100.43:9092"</span>
      <span class="hljs-comment">// Zookeeper connection properties</span>
      <span class="hljs-keyword">val</span> props = <span class="hljs-keyword">new</span> HashMap[String, Object]()
      props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, brokers)
      props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG,
        <span class="hljs-string">"org.apache.kafka.common.serialization.StringSerializer"</span>)
      props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG,
        <span class="hljs-string">"org.apache.kafka.common.serialization.StringSerializer"</span>)
      <span class="hljs-keyword">val</span> producer = <span class="hljs-keyword">new</span> KafkaProducer[String, String](props)
      <span class="hljs-keyword">val</span> topic = <span class="hljs-string">"abel2"</span>
      <span class="hljs-comment">// Send some messages</span>
        <span class="hljs-keyword">for</span> (arg &lt;- args) {
          println(arg + <span class="hljs-string">"----------我已经发送"</span>)
          <span class="hljs-keyword">val</span> message = <span class="hljs-keyword">new</span> ProducerRecord[String, String](topic, <span class="hljs-keyword">null</span>, arg)
          producer.send(message)
        }
        Thread.sleep(<span class="hljs-number">500</span>)
        producer.close()
    }
  }
}
</code></pre>

<hr>



<h1 id="4从kafka-集群中读取数据处理后再存入kafka代码展示">4.从Kafka 集群中读取数据处理后再存入Kafka代码展示</h1>

<p>此段代码是sparkStreaming集合Kafka的消费和发送数据。从Kafka集群中消费原始数据“message:  我是第n条信息“将原始数据处理后为“message:  我是第n条信息－－read“并将处理后的数据发送到Kafka集群的另外一个topic中。</p>

<pre class="prettyprint"><code class=" hljs scala"><span class="hljs-keyword">import</span> java.util.HashMap
<span class="hljs-keyword">import</span> org.apache.kafka.clients.producer.{KafkaProducer, ProducerConfig, ProducerRecord}
<span class="hljs-keyword">import</span> org.apache.spark.streaming._
<span class="hljs-keyword">import</span> org.apache.spark.streaming.kafka._
<span class="hljs-keyword">import</span> scala.collection.mutable.ArrayBuffer
<span class="hljs-keyword">import</span> org.apache.spark.{SparkConf}

<span class="hljs-javadoc">/**
  * Created by yangyibo on 16/11/28.
  */</span>
<span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">KafkaWordCountDemo</span> {</span>
  <span class="hljs-keyword">private</span> <span class="hljs-keyword">val</span> brokers = <span class="hljs-string">"192.168.100.41:9092,192.168.100.42:9092,192.168.100.43:9092"</span>
  <span class="hljs-comment">// Zookeeper connection properties</span>
  <span class="hljs-keyword">private</span> <span class="hljs-keyword">val</span> props = <span class="hljs-keyword">new</span> HashMap[String, Object]()
  props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, brokers)
  props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG,
    <span class="hljs-string">"org.apache.kafka.common.serialization.StringSerializer"</span>)
  props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG,
    <span class="hljs-string">"org.apache.kafka.common.serialization.StringSerializer"</span>)
  <span class="hljs-keyword">private</span> <span class="hljs-keyword">val</span> producer = <span class="hljs-keyword">new</span> KafkaProducer[String, String](<span class="hljs-keyword">this</span>.props)

  <span class="hljs-keyword">def</span> main(args: Array[String]): Unit = {
    run()
  }

  <span class="hljs-keyword">def</span> run(): Unit = {
    <span class="hljs-keyword">val</span> zkQuorum = <span class="hljs-string">"192.168.100.48:2181"</span>
    <span class="hljs-keyword">val</span> group = <span class="hljs-string">"spark-streaming-test"</span>
    <span class="hljs-keyword">val</span> topics = <span class="hljs-string">"abel"</span>
    <span class="hljs-keyword">val</span> numThreads = <span class="hljs-number">1</span>

    <span class="hljs-keyword">val</span> sparkConf = <span class="hljs-keyword">new</span> SparkConf().setAppName(<span class="hljs-string">"KafkaWordCountDemo"</span>)
    sparkConf.setMaster(<span class="hljs-string">"local[4]"</span>)
    <span class="hljs-keyword">val</span> ssc = <span class="hljs-keyword">new</span> StreamingContext(sparkConf, Seconds(<span class="hljs-number">2</span>))
    ssc.checkpoint(<span class="hljs-string">"checkpoint"</span>)


    <span class="hljs-keyword">val</span> topicMap = topics.split(<span class="hljs-string">","</span>).map((_, numThreads.toInt)).toMap
    <span class="hljs-keyword">val</span> lines = KafkaUtils.createStream(ssc, zkQuorum, group, topicMap).map(_._2)       
    <span class="hljs-keyword">val</span> array = ArrayBuffer[String]()
    lines.foreachRDD(rdd =&gt; {
      <span class="hljs-keyword">val</span> count = rdd.count().toInt;
      rdd.take(count + <span class="hljs-number">1</span>).take(count).foreach(x =&gt; {
        array += x + <span class="hljs-string">"--read"</span>
      })
      kafkaProducerSend(array)
      array.clear()
    })
    ssc.start()
    ssc.awaitTermination()
  }

  <span class="hljs-keyword">def</span> kafkaProducerSend(args: ArrayBuffer[String]) {
    <span class="hljs-keyword">if</span> (args != <span class="hljs-keyword">null</span>) {
      <span class="hljs-keyword">val</span> topic = <span class="hljs-string">"abel2"</span>
      <span class="hljs-comment">// Send some messages</span>
      <span class="hljs-keyword">for</span> (arg &lt;- args) {
        println(arg + <span class="hljs-string">"----------我已经读取"</span>)
        <span class="hljs-keyword">val</span> message = <span class="hljs-keyword">new</span> ProducerRecord[String, String](topic, <span class="hljs-keyword">null</span>, arg)
        producer.send(message)
      }
      Thread.sleep(<span class="hljs-number">500</span>)
    }
  }

}</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>