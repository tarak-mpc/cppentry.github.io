---
layout:     post
title:      Spark Streaming  +Kafka 使用底层API直接读取Kafka的Partition数据，手动更新Offset到Zookeeper集群
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章,转载必须标明出处.尊重他人就是尊重自己!					https://blog.csdn.net/Dax1n/article/details/53413111				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p></p>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);">      Spark Streaming  +Kafka 使用底层API直接读取Kafka的Partition数据，正常Offset存储在CheckPoint中。但是这样无法实现Kafka监控工具对Kafka的监控，所以手动更新Offset到Zookeeper集群中</pre>
<br><p></p>
<p><br></p>
<p></p>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre><p>相关源码简单介绍：</p><p>1:TopicAndPartition是对 topic和partition的id的封装的一个样例类</p>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre><pre><code class="language-plain">  case class TopicAndPartition(topic: String, partitionId: Int)</code></pre><br>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);">2：OffsetRange 是对topic name，partition id，fromOffset（当前消费的开始偏移），untilOffset（当前消费的结束偏移）的封装。所以<span style="font-size:15pt;">OffsetRange 包含信息有：topic名字，分区Id，开始偏移，结束偏移。</span></pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre><pre><code class="language-sql"> /**
   *
   * @param topic Kafka topic name
   * @param partition Kafka partition id
   * @param fromOffset inclusive starting offset
   * @param untilOffset exclusive ending offset
   */
final class OffsetRange private(val topic: String, val partition: Int, val fromOffset: Long, val untilOffset: Long) extends Serializable</code></pre><br><br>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);">3：代码实现：</pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre><pre><code class="language-plain">object Iteblog {

  val brokerAddress = "http://www.iteblog.com:9092"

  val groupID="testGroup"

  val kafkaParams = Map[String, String](
    "metadata.broker.list" -&gt; brokerAddress,
    "group.id" -&gt; "iteblog")

  def main(args: Array[String]) {

    val sparkConf = new SparkConf().setAppName("Test")
    sparkConf.set("spark.kryo.registrator", "utils.CpcKryoSerializer")
    val sc = new SparkContext(sparkConf)


    val ssc = new StreamingContext(sc, Seconds(2))
    val topicsSet = Set("iteblog")

    val messages = KafkaUtils.createDirectStream[String, String, StringDecoder, StringDecoder](ssc, kafkaParams, topicsSet)
    messages.foreachRDD(rdd =&gt; {
      // 把RDD转成HasOffsetRanges类型（KafkaRDD extends HasOffsetRanges）
      // OffsetRange 说明：Represents a range of offsets from a single Kafka TopicAndPartition.
      // OffsetRange 说明： Instances of this class can be created with `OffsetRange.create()`.
      val offsetsList: Array[OffsetRange] = rdd.asInstanceOf[HasOffsetRanges].offsetRanges
//     offsetRanges 的实现代码（KafkaRDD中）：tp：TopicAndPartition,fo:fromOffset
//      val offsetRanges = fromOffsets.map { case (tp, fo) =&gt;
//        val uo = untilOffsets(tp)
//        OffsetRange(tp.topic, tp.partition, fo, uo.offset)
//      }.toArray

      val kc = new KafkaCluster(kafkaParams)
      for (offsets &lt;- offsetsList) {
       //TopicAndPartition 主构造参数第一个是topic，第二个是 partition id
        val topicAndPartition = TopicAndPartition("iteblog", offsets.partition) //offsets.partition表示的是Kafka partition id
        val o = kc.setConsumerOffsets(groupID, Map((topicAndPartition, offsets.untilOffset)))//offsets.untilOffset:是
        if (o.isLeft) {
          println(s"Error updating the offset to Kafka cluster: ${o.left.get}")
        }
      }
    })

    ssc.start()
    ssc.awaitTermination()
    ssc.stop()
  }
}</code></pre><br>核心代码讲解：
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);">使用KafkaUtils的createDirectStream方法，调用底层API直接消费Kafka Partition的数据（Kafka Partition和RDD Partition 一一对应）。<span style="font-size:15pt;">createDirectStream返回值是DStream，底层是RDD。</span></pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre><pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre><pre><code class="language-plain">val messages = KafkaUtils.createDirectStream[String, String, StringDecoder, StringDecoder](ssc, kafkaParams, topicsSet)</code></pre><br><pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre><pre><code class="language-plain">messages.foreachRDD 是对messages底层RDD计算其偏移范围。</code></pre><pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre><pre style="font-size:15pt;font-family:Consolas;background-color:rgb(255,255,255);">KafkaRDD和HasOffsetRanges关系(构造参数和泛型省略，具体见源码)：</pre><pre><code class="language-plain">KafkaRDD extends RDD[R](sc, Nil) with Logging with HasOffsetRanges</code></pre><br><pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);">//rdd是messages.foreachRDD中的变量，rdd其类型是KafkaRDD，但是由于多态的原因rdd实际上不是KafkaRDD类型，而是RDD类型，所以需要向下转型为HasOffsetRanges，调用offsetRanges方法。（回忆OffsetRange是对什么的封装？答案：<span style="font-size:15pt;">topic名字，分区Id，开始偏移，结束偏移。</span><span style="font-size:15pt;">）</span></pre><pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre><pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre><pre><code class="language-plain">val offsetsList: Array[OffsetRange] = rdd.asInstanceOf[HasOffsetRanges].offsetRanges
</code></pre><pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre>offsetRanges 的实现代码（KafkaRDD中）：tp：TopicAndPartition,fo:fromOffset<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre><pre><code class="language-plain">val offsetRanges = fromOffsets.map { case (tp, fo) =&gt;
   val uo = untilOffsets(tp)
   OffsetRange(tp.topic, tp.partition, fo, uo.offset)
}.toArray</code></pre><br><pre style="font-size:15pt;font-family:Consolas;background-color:rgb(255,255,255);"></pre><pre style="font-size:15pt;font-family:Consolas;background-color:rgb(255,255,255);">KafkaCluster 完成对更新偏移到zookeeper集群的封装工具类。</pre><pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre><pre><code class="language-plain">val kc = new KafkaCluster(kafkaParams)</code></pre><br>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);">对<span style="font-size:15pt;">offsetRanges数组遍历：setConsumerOffsets是</span><span style="font-size:15pt;">KafkaCluster </span></pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"><span style="font-size:15pt;"></span></pre><pre style="font-size:15pt;font-family:Consolas;background-color:rgb(255,255,255);">强调：OffsetRange是对什么的封装？答案：<span style="font-size:15pt;">topic名字，分区Id，开始偏移，结束偏移</span></pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre><pre><code class="language-plain">for (offsets &lt;- offsetsList) {</code></pre><pre><code class="language-plain">    //offsets.untilOffset是结束偏移量</code></pre>   val o = kc.setConsumerOffsets(groupID, Map((topicAndPartition, offsets.untilOffset)))        if (o.isLeft) {          println(s"Error updating the offset to Kafka cluster: ${o.left.get}")        } }
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre>
以上是完成对<span style="font-size:15pt;">createDirectStream创建的DStream中的一个底层RDD完成偏移的更新</span><span style="font-size:15pt;">到</span><span style="font-size:15pt;">zookeeper集群，通过foreachRDD完成对所有RDD的更新！！！至此已经完成RDD偏移的计算以及更新，但是具体的更新方法？在</span><span style="font-size:15pt;">KafkaCluster中。接下来看</span><span style="font-size:15pt;">KafkaCluster代码。</span>
<pre></pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre><pre></pre><pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"><span style="font-size:15pt;">直接上代码：</span></pre><pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"><span style="font-size:15pt;">
</span></pre><pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"><span style="font-size:15pt;"></span></pre><pre><code class="language-plain">package org.apache.spark.streaming.kafka

//使用org.apache.spark.streaming.kafka的原因：  private[spark] object SimpleConsumerConfig限制只在spark包中使用！

import kafka.api.OffsetCommitRequest
import kafka.common.{ErrorMapping, OffsetMetadataAndError, TopicAndPartition}
import kafka.consumer.SimpleConsumer
import org.apache.spark.SparkException
import org.apache.spark.streaming.kafka.KafkaCluster.SimpleConsumerConfig
import scala.collection.mutable.ArrayBuffer
import scala.util.Random
import scala.util.control.NonFatal

class KafkaCluster(val kafkaParams: Map[String, String]) extends Serializable {                 //<span style="font-family:Consolas;">Err是类型</span><span style="font-family:Consolas;">ArrayBuffer[Throwable]别名</span><span style="font-family:Consolas;">
</span>   type Err = ArrayBuffer[Throwable]

   @transient private var _config: SimpleConsumerConfig = null

   def config: SimpleConsumerConfig = this.synchronized {
     if (_config == null) {
       //SimpleConsumerConfig的apply方法部分代码：
       //val brokers = kafkaParams.get("metadata.broker.list").orElse(kafkaParams.get("bootstrap.servers"))
       //所以kafkaParams必须包含key=metadata.broker.list或者bootstrap.servers对应的Value
       _config = SimpleConsumerConfig(kafkaParams)
     }
     _config
   }

  /**
    *
    * @param groupId: String
    * @param offsets: Map[TopicAndPartition, Long]
    * @return
    */
   def setConsumerOffsets(groupId: String,
                          offsets: Map[TopicAndPartition, Long]
                           ): Either[Err, Map[TopicAndPartition, Short]] = {
     setConsumerOffsetMetadata(groupId, offsets.map { kv =&gt;
       kv._1 -&gt; OffsetMetadataAndError(kv._2)
     })
   }

   def setConsumerOffsetMetadata(groupId: String,
                                 metadata: Map[TopicAndPartition, OffsetMetadataAndError]
                                  ): Either[Err, Map[TopicAndPartition, Short]] = {
     var result = Map[TopicAndPartition, Short]()
     val req = OffsetCommitRequest(groupId, metadata)
     val errs = new Err
     val topicAndPartitions = metadata.keySet
     withBrokers(Random.shuffle(config.seedBrokers), errs) { consumer =&gt;
       val resp = consumer.commitOffsets(req)
       val respMap = resp.requestInfo
       val needed = topicAndPartitions.diff(result.keySet)
       needed.foreach { tp: TopicAndPartition =&gt;
         respMap.get(tp).foreach { err: Short =&gt;
           if (err == ErrorMapping.NoError) {
             result += tp -&gt; err
           } else {
             errs.append(ErrorMapping.exceptionFor(err))
           }
         }
       }
       if (result.keys.size == topicAndPartitions.size) {
         return Right(result)
       }
     }
     val missing = topicAndPartitions.diff(result.keySet)
     errs.append(new SparkException(s"Couldn't set offsets for ${missing}"))
     Left(errs)
   }

   private def withBrokers(brokers: Iterable[(String, Int)], errs: Err)
                          (fn: SimpleConsumer =&gt; Any): Unit = {
     brokers.foreach { hp =&gt;
       var consumer: SimpleConsumer = null
       try {
         consumer = connect(hp._1, hp._2)
         fn(consumer)
       } catch {
         case NonFatal(e) =&gt;
           errs.append(e)
       } finally {
         if (consumer != null) {
           consumer.close()
         }
       }
     }
   }

   def connect(host: String, port: Int): SimpleConsumer =
     new SimpleConsumer(host, port, config.socketTimeoutMs,
       config.socketReceiveBufferBytes, config.clientId)
 }
</code></pre><br><br><pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre><pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);">小拓展：</pre><pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre><pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre><pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);">Map的连续获取值用法：</pre><pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre><pre><code class="language-plain">object MapDemo extends App {
  val map = Map("1" -&gt; "11", "2" -&gt; "22", "3" -&gt; "33")
  map.map {
    case (a, b) =&gt;
      println(a + " " + b)
  }

//语法：先获取key=4的value，如果存在返回否则获取key=5的value，如果存在返回，不存在的话直接异常。
  val result = map.get("4").orElse(map.get("5")).getOrElse(throw new Exception("exception"))
  println(result)

}</code></pre><br><br><pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre><pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre><pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);">核心代码讲解之SimpleConsumerConfig的apply方法：</pre><pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre><pre><code class="language-plain">    /**
     * Make a consumer config without requiring group.id or zookeeper.connect,
     * since communicating with brokers also needs common settings such as timeout
     */
    def apply(kafkaParams: Map[String, String]): SimpleConsumerConfig = {
// These keys are from other pre-existing kafka configs for specifying brokers,accept either     // map连续获取值用法见上面示例
      val brokers = kafkaParams.get("metadata.broker.list")
        .orElse(kafkaParams.get("bootstrap.servers"))
        .getOrElse(throw new SparkException(
          "Must specify metadata.broker.list or bootstrap.servers"))

      val props = new Properties()
      kafkaParams.foreach { case (key, value) =&gt;
        // prevent warnings on parameters ConsumerConfig doesn't know about
        if (key != "metadata.broker.list" &amp;&amp; key != "bootstrap.servers") {
          props.put(key, value)
        }
      }
     //如果没有<span style="font-family:Consolas;">zookeeper.connect和</span><span style="font-family:Consolas;">group.id，设置其value为空字符串。</span><span style="font-family:Consolas;">
</span>      Seq("zookeeper.connect", "group.id").foreach { s =&gt;
        if (!props.contains(s)) {
          props.setProperty(s, "")
        }
      }

      new SimpleConsumerConfig(brokers, props)
    }</code></pre><pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre><pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);">OffsetMetadataAndError样例类：</pre><pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre><pre><code class="language-plain">case class OffsetMetadataAndError(offsetMetadata: OffsetMetadata, error: Short = Errors.NONE.code) {
  def offset = offsetMetadata.offset

  def metadata = offsetMetadata.metadata

  override def toString = "[%s,ErrorCode %d]".format(offsetMetadata, error)
}</code></pre><br><br><br>TopicAndPartition是对 topic和partition的id的封装的一个样例类<br><pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre><pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre><pre><code class="language-plain"> /**
    *
    * @param groupId: String
    * @param offsets: Map[TopicAndPartition, Long]</code></pre><pre><code class="language-plain">    * Map中key和value含义TopicAndPartition 是topic和partition id封装<span style="font-family:Consolas;">long是消费的结束偏移</span></code></pre>    * @return    */ def setConsumerOffsets(groupId: String,offsets: Map[TopicAndPartition, Long]): Either[Err,  Map[TopicAndPartition, Short]] = {     setConsumerOffsetMetadata(groupId, offsets.map { kv =&gt;       kv._1 -&gt; OffsetMetadataAndError(kv._2)     })   }<br><br>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);">
</pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);">具体更多细节见代码注释吧，写的很详细。就不在重复贴在博客了。</pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);">github地址：https://github.com/Dax1n/spark-kafka-directstream-zookeeper
</pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);">
</pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);">
</pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre>
<pre style="font-family:Consolas;font-size:15pt;background-color:rgb(255,255,255);"></pre>
<pre></pre>
<pre></pre>
<pre></pre>
            </div>
                </div>