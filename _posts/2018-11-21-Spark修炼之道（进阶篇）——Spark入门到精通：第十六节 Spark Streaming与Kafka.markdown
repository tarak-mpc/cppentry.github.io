---
layout:     post
title:      Spark修炼之道（进阶篇）——Spark入门到精通：第十六节 Spark Streaming与Kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>作者：周志湖</p>

<h2 id="主要内容"><a name="t0"></a>主要内容</h2>

<ol><li>Spark Streaming与Kafka版的WordCount示例（一）</li>
	<li>Spark Streaming与Kafka版的WordCount示例（二）</li>
</ol><h2 id="1-spark-streaming与kafka版本的wordcount示例-一"><a name="t1"></a>1. Spark Streaming与Kafka版本的WordCount示例 （一）</h2>

<ol><li>启动kafka集群</li>
</ol><pre class="has">
<code>root@sparkslave02:/hadoopLearning/kafka_2.10-0.8.2.1# bin/kafka-server-start.sh config/server.properties 
root@sparkslave01:/hadoopLearning/kafka_2.10-0.8.2.1# bin/kafka-server-start.sh config/server.properties 
root@sparkmaster:/hadoopLearning/kafka_2.10-0.8.2.1# bin/kafka-server-start.sh config/server.properties </code></pre>

<ul><li> </li>
</ul><p>向kafka集群发送消息</p>

<pre class="has">
<code>root@sparkslave01:/hadoopLearning/kafka_2.10-0.8.2.1# bin/kafka-console-producer.sh --broker-list sparkslave01:9092 --sync --topic kafkatopictest</code></pre>

<ul><li> </li>
</ul><ol><li>编写如下程序</li>
</ol><pre class="has">
<code>import org.apache.kafka.clients.producer.{ProducerConfig, KafkaProducer, ProducerRecord}
import org.apache.log4j.{Level, Logger}

import org.apache.spark.streaming._
import org.apache.spark.streaming.kafka._
import org.apache.spark.{Logging, SparkConf}

object KafkaWordCount {
  def main(args: Array[String]) {
    if (args.length &lt; 4) {
      System.err.println("Usage: KafkaWordCount &lt;zkQuorum&gt; &lt;group&gt; &lt;topics&gt; &lt;numThreads&gt;")
      System.exit(1)
    }
    StreamingExamples.setStreamingLogLevels()

    val Array(zkQuorum, group, topics, numThreads) = args
    val sparkConf = new SparkConf().setAppName("KafkaWordCount").setMaster("local[4]")
    val ssc = new StreamingContext(sparkConf, Seconds(2))
    ssc.checkpoint("checkpoint")

    val topicMap = topics.split(",").map((_, numThreads.toInt)).toMap
    //创建ReceiverInputDStream
    val lines = KafkaUtils.createStream(ssc, zkQuorum, group, topicMap).map(_._2)
    val words = lines.flatMap(_.split(" "))
    val wordCounts = words.map(x =&gt; (x, 1L))
      .reduceByKeyAndWindow(_ + _, _ - _, Minutes(10), Seconds(2), 2)
    wordCounts.print()

    ssc.start()
    ssc.awaitTermination()
  }
}</code></pre>

<ul><li> </li>
</ul><p>配置运行参数： <br><img alt="这里写图片描述" class="has" src="https://img-blog.csdn.net/20151203231200487"><br>
具体如下：</p>

<pre class="has">
<code>sparkmaster:2181  test-consumer-group kafkatopictest 1</code></pre>

<ul><li> </li>
</ul><p>sparkmaster:2181，zookeeper监听地址 <br>
test-consumer-group， consumer-group的名称，必须和$KAFKA_HOME/config/consumer.properties中的group.id的配置内容一致 <br>
kafkatopictest，topic名称 <br>
1，线程数</p>

<p>运行KafkaWordCount 后，在producer中输入下列内容</p>

<pre class="has">
<code>root@sparkslave01:/hadoopLearning/kafka_2.10-0.8.2.1# bin/kafka-console-producer.sh --broker-list sparkslave01:9092 --sync --topic kafkatopictest
[2015-11-04 03:25:39,666] WARN Property topic is not valid (kafka.utils.VerifiableProperties)
Spark
Spark TEST
TEST Spark Streaming
</code></pre>

<ul><li> </li>
</ul><p><img alt="这里写图片描述" class="has" src="https://img-blog.csdn.net/20151203231618978"></p>

<p>得到结果如下： <br><img alt="这里写图片描述" class="has" src="https://img-blog.csdn.net/20151203231723359"></p>

<h2 id="2-spark-streaming与kafka版本的wordcount示例二"><a name="t2"></a>2. Spark Streaming与Kafka版本的WordCount示例（二）</h2>

<p>前面的例子中，producer是通过kafka的脚本生成的，本例中将给出通过编写程序生成的producer</p>

<pre class="has">
<code>// 随机生成1-100间的数字
object KafkaWordCountProducer {

  def main(args: Array[String]) {
    if (args.length &lt; 4) {
      System.err.println("Usage: KafkaWordCountProducer &lt;metadataBrokerList&gt; &lt;topic&gt; " +
        "&lt;messagesPerSec&gt; &lt;wordsPerMessage&gt;")
      System.exit(1)
    }

    val Array(brokers, topic, messagesPerSec, wordsPerMessage) = args

    // Zookeeper连接属性配置
    val props = new HashMap[String, Object]()
    props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, brokers)
    props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG,
      "org.apache.kafka.common.serialization.StringSerializer")
    props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG,
      "org.apache.kafka.common.serialization.StringSerializer")
    //创建KafkaProducer
    val producer = new KafkaProducer[String, String](props)

    // 向kafka集群发送消息
    while(true) {
      (1 to messagesPerSec.toInt).foreach { messageNum =&gt;
        val str = (1 to wordsPerMessage.toInt).map(x =&gt; scala.util.Random.nextInt(10).toString)
          .mkString(" ")

        val message = new ProducerRecord[String, String](topic, null, str)
        producer.send(message)
      }

      Thread.sleep(1000)
    }
  }

}</code></pre>

<ul><li> </li>
</ul><p>KafkaWordCountProducer 运行参数设置如下：</p>

<pre class="has">
<code>sparkmaster:9092 kafkatopictest 5 8</code></pre>

<ul><li> </li>
</ul><p>sparkmaster:9092，broker-list <br>
kafkatopictest，top名称 <br>
5表示每秒发多少条消息 <br>
8表示每条消息中有几个单词</p>

<p>先KafkaWordCountProducer，然后再运行KafkaWordCount ，得到的计算结果如下： <br><img alt="这里写图片描述" class="has" src="https://img-blog.csdn.net/20151203232434164"></p>            </div>
                </div>