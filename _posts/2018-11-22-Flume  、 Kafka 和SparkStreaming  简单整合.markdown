---
layout:     post
title:      Flume  、 Kafka 和SparkStreaming  简单整合
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/yulei_qq/article/details/82717653				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>flume 传递数据给Kafka ，然后Spark 从Kafka 中接收数据进行处理.</p>

<p>本文使用netcat 工具作为flume 的输入源 , 话不多说，直接贴代码.</p>

<p><strong>1、flume </strong></p>

<p>配置文件配置：</p>

<pre class="has">
<code class="language-java">     
                         
        a1.sources = r1
		a1.sinks = k1
		a1.channels = c1

		a1.sources.r1.type=netcat
		a1.sources.r1.bind=localhost
		a1.sources.r1.port=8888

		a1.sinks.k1.type = org.apache.flume.sink.kafka.KafkaSink
		a1.sinks.k1.kafka.topic = test3
		a1.sinks.k1.kafka.bootstrap.servers = big-data-2:6667
		a1.sinks.k1.kafka.flumeBatchSize = 20
		a1.sinks.k1.kafka.producer.acks = 1

		a1.channels.c1.type=memory

		a1.sources.r1.channels = c1
		a1.sinks.k1.channel = c1</code></pre>

<p>客户端使用  <strong> nc   localhost  8888</strong>  ,  就可以给flume 发送消息了。</p>

<p><strong>2、Kafka </strong></p>

<p>监听的端口是6667 (使用Ambari 工具，默认的端口就是这个).</p>

<p>创建一个topic </p>

<pre class="has">
<code>./kafka-topics.sh --create --zookeeper big-data-2:2181 --replication-factor 3 --partitions 3 --topic test3</code></pre>

<p>然后用如下命令可以查看创建的topic </p>

<pre class="has">
<code> ./kafka-topics.sh --list --zookeeper big-data-2:2181</code></pre>

<p> </p>

<p><strong>3、SparkStreaming</strong></p>

<pre class="has">
<code class="language-bash">package com.it18zhang.spark.java;

import java.util.*;

import org.apache.spark.SparkConf;
import org.apache.spark.TaskContext;
import org.apache.spark.api.java.*;
import org.apache.spark.api.java.function.*;
import org.apache.spark.streaming.Seconds;
import org.apache.spark.streaming.api.java.*;
import org.apache.spark.streaming.kafka010.*;
import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.apache.kafka.common.TopicPartition;
import org.apache.kafka.common.serialization.StringDeserializer;
import scala.Tuple2;

/**
 */
public class KafkaSparkStreamingDemo {
    public static void main(String[] args) throws InterruptedException {

        SparkConf conf = new SparkConf();
        conf.setAppName("kafkaSpark");
        conf.setMaster("local[4]");
        //创建Spark流应用上下文
        JavaStreamingContext streamingContext = new JavaStreamingContext(conf, Seconds.apply(5));

        Map&lt;String, Object&gt; kafkaParams = new HashMap&lt;&gt;();
        kafkaParams.put("bootstrap.servers", "big-data-2:6667");
        kafkaParams.put("key.deserializer", StringDeserializer.class);
        kafkaParams.put("value.deserializer", StringDeserializer.class);
        kafkaParams.put("group.id", "g6");
        kafkaParams.put("auto.offset.reset", "latest");
        kafkaParams.put("enable.auto.commit", false);

        Collection&lt;String&gt; topics = Arrays.asList("test3");

        final JavaInputDStream&lt;ConsumerRecord&lt;String, String&gt;&gt; stream =
                KafkaUtils.createDirectStream(
                        streamingContext,
                        LocationStrategies.PreferConsistent(),
                        ConsumerStrategies.&lt;String, String&gt;Subscribe(topics, kafkaParams)
                );


        //压扁
        JavaDStream&lt;String&gt; wordsDS = stream.flatMap(new FlatMapFunction&lt;ConsumerRecord&lt;String,String&gt;, String&gt;() {
            public Iterator&lt;String&gt; call(ConsumerRecord&lt;String, String&gt; r) throws Exception {
                String value = r.value();
                List&lt;String&gt; list = new ArrayList&lt;String&gt;();
                String[] arr = value.split(" ");
                for (String s : arr) {
                    list.add(s);
                }
                return list.iterator();
            }
        });

        //映射成元组
        JavaPairDStream&lt;String, Integer&gt; pairDS = wordsDS.mapToPair(new PairFunction&lt;String, String, Integer&gt;() {
            public Tuple2&lt;String, Integer&gt; call(String s) throws Exception {
                return new Tuple2&lt;String, Integer&gt;(s, 1);
            }
        });

        //聚合
        JavaPairDStream&lt;String, Integer&gt; countDS = pairDS.reduceByKey(new Function2&lt;Integer, Integer, Integer&gt;() {
            public Integer call(Integer v1, Integer v2) throws Exception {
                return v1 + v2;
            }
        });
        //打印
        countDS.print();

        streamingContext.start();

        streamingContext.awaitTermination();
    }
}
</code></pre>

<p> </p>

<p>附录：</p>

<p>flume 利用 spooldir 监控目录 ，传递数据到Kafka 和hdfs</p>

<pre class="has">
<code class="language-bash">
a1.sources = r1
a1.sinks = k1 k2
a1.channels = c1

# properties of r1
a1.sources.r1.type = spooldir
a1.sources.r1.spoolDir =/data/flow_data
a1.sources.r1.inputCharset=utf-8



# properties of c1
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100



# properties of k1
a1.sinks.k1.type = hdfs
a1.sinks.k1.hdfs.path = hdfs://mycluster/flume/events/%y-%m-%d/%H/%M/%S

a1.sinks.k1.hdfs.filePrefix = events-
a1.sinks.k1.hdfs.round = true
a1.sinks.k1.hdfs.roundValue = 10
a1.sinks.k1.hdfs.roundUnit = minute
 
a1.sinks.k1.hdfs.useLocalTimeStamp=true

a1.sinks.k1.hdfs.rollSize=0
a1.sinks.k1.hdfs.rollCount=0
#a1.sinks.k1.hdfs.rollInterval=30

# a1-Sink  Kafka
a1.sinks.k2.type = org.apache.flume.sink.kafka.KafkaSink  
a1.sinks.k2.topic = DBFile
a1.sinks.k2.brokerList = big-data-2:6667,big-data-3:6667,big-data-4:6667
a1.sinks.k2.requiredAcks = 1  
a1.sinks.k2.batchSize = 20  


# set channel for sources, sinks
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
a1.sinks.k2.channel = c1</code></pre>

<p> </p>

<p> </p>

<p> </p>            </div>
                </div>