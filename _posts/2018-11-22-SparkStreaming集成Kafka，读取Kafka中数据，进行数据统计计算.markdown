---
layout:     post
title:      SparkStreaming集成Kafka，读取Kafka中数据，进行数据统计计算
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>简单了解一下Kafka：是一种高吞吐量的分布式发布订阅消息系统。依赖Zookeeper，因此搭建Kafka的时候需要事先搭建好Zookeeper。体系结构如下：</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180907210107662?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0phbnRlbG9wZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180907210217779?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0phbnRlbG9wZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>当SparkStreaming与Kafka做集成的时候Kafka成了Streaming的高级数据源，由于Spark Streaming和Kafka集成的时候，依赖的jar包比较多，而且还会产生冲突。<strong><span style="color:#ff0000;">强烈建议使用</span></strong><strong><span style="color:#ff0000;">Maven</span><span style="color:#ff0000;">的方式来搭建项目工程</span></strong></p>

<p><strong><span style="color:#ff0000;">pom文件如下：</span></strong></p>

<pre class="has">
<code class="language-html">&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"&gt;
    &lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;

    &lt;groupId&gt;ZDemo5&lt;/groupId&gt;
    &lt;artifactId&gt;ZDemo5&lt;/artifactId&gt;
    &lt;version&gt;1.0-SNAPSHOT&lt;/version&gt;

    &lt;properties&gt;
        &lt;spark.version&gt;2.1.0&lt;/spark.version&gt;
        &lt;scala.version&gt;2.11&lt;/scala.version&gt;
    &lt;/properties&gt;

    &lt;dependencies&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;org.apache.spark&lt;/groupId&gt;
            &lt;artifactId&gt;spark-core_${scala.version}&lt;/artifactId&gt;
            &lt;version&gt;${spark.version}&lt;/version&gt;
        &lt;/dependency&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;org.apache.spark&lt;/groupId&gt;
            &lt;artifactId&gt;spark-streaming_${scala.version}&lt;/artifactId&gt;
            &lt;version&gt;${spark.version}&lt;/version&gt;
        &lt;/dependency&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;org.apache.spark&lt;/groupId&gt;
            &lt;artifactId&gt;spark-sql_${scala.version}&lt;/artifactId&gt;
            &lt;version&gt;${spark.version}&lt;/version&gt;
        &lt;/dependency&gt;

        &lt;dependency&gt;
            &lt;groupId&gt;org.apache.spark&lt;/groupId&gt;
            &lt;artifactId&gt;spark-streaming-kafka-0-8_2.11&lt;/artifactId&gt;
            &lt;version&gt;2.1.1&lt;/version&gt;
        &lt;/dependency&gt;
    &lt;/dependencies&gt;

&lt;/project&gt;</code></pre>

<p>启动kafka：bin/kafka-server-start.sh config/server.properties &amp;       ---后台方式启动</p>

<p>创建topic：bin/kafka-topics.sh --create --zookeeper bigdata111:2181 -replication-factor 1 --partitions 3 --topic <span style="color:#f33b45;">mydemo2</span></p>

<p>发布消息：bin/kafka-console-producer.sh --broker-list bigdata111:9092 --topic mydemo2</p>

<p>SparkStreaming读取Kafka数据源由两种模式： </p>

<p>模式一：Receiver 的方式，Receivers的实现使用到Kafka高层次的消费者API。对于所有的Receivers，接收到的数据将会保存在Spark executors中，然后由Spark Streaming启动的Job来处理这些数据。</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180907210545846?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0phbnRlbG9wZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>这种方法吞吐量不高</p>

<p>代码实现：</p>

<pre class="has">
<code class="language-java">import org.apache.log4j.Logger
import org.apache.log4j.Level
import org.apache.spark.SparkConf
import org.apache.spark.streaming.StreamingContext
import org.apache.spark.streaming.Seconds
import org.apache.spark.streaming.kafka.KafkaUtils
import org.apache.spark.storage.StorageLevel

object KafkaRecciver {
  def main(args: Array[String]): Unit = {
    Logger.getLogger("org.apache.spark").setLevel(Level.ERROR)
    Logger.getLogger("org.eclipse.jetty.server").setLevel(Level.OFF)
    val conf = new SparkConf().setAppName("SparkFlumeNGWordCount").setMaster("local[2]")
    val ssc = new StreamingContext(conf, Seconds(5))
    ssc.checkpoint("hdfs://bigdata111:9000/checkpoint")
    //创建kafka对象   生产者 和消费者 
    //模式1 采取的是 receiver 方式  reciver 每次只能读取一条记录
    val topic = Map("mydemo2" -&gt; 1)
    //直接读取的方式  由于kafka 是分布式消息系统需要依赖Zookeeper
    val data = KafkaUtils.createStream(ssc, "192.168.128.111:2181", "mygroup", topic, StorageLevel.MEMORY_AND_DISK)
    //数据累计计算
    val updateFunc =(curVal:Seq[Int],preVal:Option[Int])=&gt;{
    //进行数据统计当前值加上之前的值
    var total = curVal.sum
    //最初的值应该是0
    var previous = preVal.getOrElse(0)
    //Some 代表最终的返回值
    Some(total+previous)
  }
   val result = data.map(_._2).flatMap(_.split(" ")).map(word=&gt;(word,1)).updateStateByKey(updateFunc).print()
   //启动ssc
   ssc.start()
   ssc.awaitTermination()
    
  }
}</code></pre>

<p>模式二：与基于Receiver接收数据不一样，这种方式定期地从Kafka的topic+partition中查询最新的偏移量，再根据定义的偏移量范围在每个batch里面处理数据。当作业需要处理的数据来临时，spark通过调用Kafka的简单消费者API读取一定范围的数据。这种模式可以有效提高系统的吞吐量</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180907211638501?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0phbnRlbG9wZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>代码实现：</p>

<pre class="has">
<code class="language-java">import org.apache.log4j.Logger
import org.apache.log4j.Level
import org.apache.spark.SparkConf
import org.apache.spark.streaming.StreamingContext
import org.apache.spark.streaming.Seconds
import org.apache.spark.streaming.kafka.KafkaUtils
import kafka.serializer.StringDecoder

object KafkaDirector {
  def main(args: Array[String]): Unit = {
    Logger.getLogger("org.apache.spark").setLevel(Level.ERROR)
    Logger.getLogger("org.eclipse.jetty.server").setLevel(Level.OFF)
    //构建conf ssc 对象
    val conf = new SparkConf().setAppName("Kafka_director").setMaster("local[2]")
    val ssc = new StreamingContext(conf,Seconds(3))
    //设置数据检查点进行累计统计单词
    ssc.checkpoint("hdfs://192.168.128.111:9000/checkpoint")
    //kafka 需要Zookeeper  需要消费者组
    val topics = Set("mydemo2")
    //                                     broker的原信息                                  ip地址以及端口号
    val kafkaPrams = Map[String,String]("metadata.broker.list" -&gt; "192.168.128.111:9092")
    //                                          数据的输入了类型    数据的解码类型
    val data = KafkaUtils.createDirectStream[String,String,StringDecoder,StringDecoder](ssc, kafkaPrams, topics)
    val updateFunc =(curVal:Seq[Int],preVal:Option[Int])=&gt;{
      //进行数据统计当前值加上之前的值
      var total = curVal.sum
      //最初的值应该是0
      var previous = preVal.getOrElse(0)
      //Some 代表最终的但会值
      Some(total+previous)
    }
    //统计结果
    val result = data.map(_._2).flatMap(_.split(" ")).map(word=&gt;(word,1)).updateStateByKey(updateFunc).print() 
    //启动程序
    ssc.start()
    ssc.awaitTermination()
    
  }
}</code></pre>

<p>结果展示：</p>

<pre class="has">
<code>-------------------------------------------
Time: 1536325032000 ms
-------------------------------------------
(love,1)
(Beijing,1)
(I,1)

-------------------------------------------
Time: 1536325035000 ms
-------------------------------------------
(love,2)
(Beijing,1)
(I,2)
(Shanghai,1)

-------------------------------------------
Time: 1536325038000 ms
-------------------------------------------
(love,2)
(Beijing,1)
(I,2)
(Shanghai,1)

-------------------------------------------
Time: 1536325041000 ms
-------------------------------------------
(love,2)
(Beijing,1)
(I,2)
(Shanghai,1)
</code></pre>

<p> </p>            </div>
                </div>