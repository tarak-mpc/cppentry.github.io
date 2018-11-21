---
layout:     post
title:      kafka本地环境的搭建，以及本地java测试的调用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p align="center"><span style="font-size:32px;">kafka本地环境搭建与本地java代码测试</span></p>
<span style="font-size:18px;"></span>
<p><span style="font-size:14px;">1、下载kafka</span></p>
<p><a href="https://www.apache.org/dyn/closer.cgi?path=/kafka/0.8.2.1/kafka_2.9.2-0.8.2.1.tgz" rel="nofollow"><span style="font-size:14px;">https://www.apache.org/dyn/closer.cgi?path=/kafka/0.8.2.1/kafka_2.9.2-0.8.2.1.tgz</span></a><br><span style="font-size:14px;"></span></p>
<p><span style="font-size:14px;">2、解压</span></p>
<p><span style="font-size:14px;">tar -zxf kafka_2.9.2-0.8.2.1.tgz</span></p>
<p align="left"><span style="font-size:14px;"><br></span></p>
<p align="left"><span style="font-size:14px;">3.修改配置文件：</span><span style="font-size:14px;">%kafka_home/config目录下:</span></p>
<p align="left"><span style="font-size:14px;">vim server.properties<br>
(</span></p>
<p><span style="font-size:14px;"></span></p>
<p><span style="font-size:14px;">############################# Server Basics #############################</span></p>
<p><span style="font-size:14px;"># The id of the broker. This must be set to a unique integer for each broker.<br>
broker.id=0</span></p>
<p><span style="font-size:14px;">############################# Socket Server Settings #############################</span></p>
<p><span style="font-size:14px;"># The port the socket server listens on<br>
port=9092</span></p>
<p><span style="font-size:14px;"># Hostname the broker will bind to. If not set, the server will bind to all interfaces<br>
# 修改为主机ip,不然服务器返回给客户端的是主机的hostname,客户端并不一定能够识别<br>
host.name=192.168.52.129</span></p>
<p><span style="font-size:14px;">############################# Log Basics #############################</span></p>
<p><span style="font-size:14px;"># A comma seperated list of directories under which to store log files<br>
# 日志目录<br>
log.dirs=/var/log/kafka</span></p>
<p><span style="font-size:14px;">#zookeeper地址和端口<br>
zookeeper.connect=192.168.52.129:2181</span></p>
<p><span style="font-size:14px;">(2)zookeeper配置</span></p>
<p><span style="font-size:14px;">vim zookeeper.properties<br>
# the directory where the snapshot is stored.<br>
dataDir=/var/zookeeper<br>
dataLogDir=/var/log/zookeeper<br>
# the port at which the clients will connect<br>
clientPort=2181<br>
# disable the per-ip limit on the number of connections since this is a non-production config<br>
maxClientCnxns=100</span></p>
<p><span style="font-size:14px;"># The number of milliseconds of each tick<br>
tickTime=2000<br>
# The number of ticks that the initial <br>
# synchronization phase can take<br>
initLimit=10<br>
# The number of ticks that can pass between <br>
# sending a request and getting an acknowledgement<br>
syncLimit=5</span></p>
<p><span style="font-size:14px;">4、启动zookeeper和kafka,编写了个脚本--startall.sh；<br></span></p>
<p><span style="font-size:14px;">#!/bin/sh<br>
#start zookeeper<br>
sh zookeeper-server-start.sh ../config/zookeeper.properties &gt; /tmp/log/zookeeper/zk-server-start.log &amp;<br>
#sleep 3 seconds<br>
sleep 3</span></p>
<p><span style="font-size:14px;">#start kafka<br>
sh kafka-server-start.sh ../config/server.properties &gt; /tmp/log/kafka/kafka-server-start.log &amp;</span></p>
<p><span style="font-size:14px;"></span></p>
<p><span style="font-size:14px;">5、使用kafka</span></p>
<p><span style="font-size:14px;">Step 3: Create a topic（创建主题:test）</span></p>
<p><span style="font-size:14px;">Let's create a topic named "test" with a single partition and only one replica:<br>
&gt; bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test<br>
We can now see that topic if we run the list topic command:<br>
&gt; bin/kafka-topics.sh --list --zookeeper localhost:2181<br>
test<br>
Alternatively, instead of manually creating topics you can also configure your brokers to auto-create topics when a non-existent topic is published to.<br>
Step 4: Send some messages</span></p>
<p><span style="font-size:14px;">Kafka comes with a command line client that will take input from a file or from standard input and send it out as messages to the Kafka cluster. By default each line will be sent as a separate message.<br>
Run the producer and then type a few messages into the console to send to the server.</span></p>
<p><span style="font-size:14px;">&gt; bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test<br>
This is a message<br>
This is another message<br>
Step 5: Start a consumer</span></p>
<p><span style="font-size:14px;">Kafka also has a command line consumer that will dump out messages to standard output.<br>
&gt; bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic test --from-beginning<br>
This is a message<br>
This is another message<br>
心得总结：<br>
1.produce启动的时候参数使用的是kafka的端口而consumer启动的时候使用的是zookeeper的端口；<br>
2.必须先创建topic才能使用；<br>
3.topic本质是以文件的形式储存在zookeeper上的。</span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;">二.JAVA编写代码测试</span></p>
<p><span style="font-size:14px;">1.相关的依赖包</span></p>
<p><span style="font-size:14px;"><a href="http://download.csdn.net/detail/alexander_zhou/9192011" rel="nofollow">http://download.csdn.net/detail/alexander_zhou/9192011</a><br></span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;">2.生产者：</span></p>
<p><span style="font-size:14px;">package cn.com.kafka.test;</span></p>
<p><span style="font-size:14px;">import java.util.Properties;</span></p>
<p><span style="font-size:14px;">import kafka.javaapi.producer.Producer;<br>
import kafka.producer.KeyedMessage;<br>
import kafka.producer.ProducerConfig;</span></p>
<p><span style="font-size:14px;">public class KafkaProducer {<br>
 private final Producer&lt;String, String&gt; producer;<br>
 public final static String TOPIC = "test";</span></p>
<p><span style="font-size:14px;"> private KafkaProducer() {<br>
  Properties props = new Properties();<br>
  // 此处配置的是kafka的端口<br>
  props.put("metadata.broker.list", "192.168.52.129:9092");</span></p>
<p><span style="font-size:14px;">  // 配置value的序列化类<br>
  props.put("serializer.class", "kafka.serializer.StringEncoder");<br>
  // 配置key的序列化类<br>
  props.put("key.serializer.class", "kafka.serializer.StringEncoder");</span></p>
<p><span style="font-size:14px;">  // request.required.acks<br>
  // 0, which means that the producer never waits for an acknowledgement<br>
  // from the broker (the same behavior as 0.7). This option provides the<br>
  // lowest latency but the weakest durability guarantees (some data will<br>
  // be lost when a server fails).<br>
  // 1, which means that the producer gets an acknowledgement after the<br>
  // leader replica has received the data. This option provides better<br>
  // durability as the client waits until the server acknowledges the<br>
  // request as successful (only messages that were written to the<br>
  // now-dead leader but not yet replicated will be lost).<br>
  // -1, which means that the producer gets an acknowledgement after all<br>
  // in-sync replicas have received the data. This option provides the<br>
  // best durability, we guarantee that no messages will be lost as long<br>
  // as at least one in sync replica remains.<br>
  props.put("request.required.acks", "-1");</span></p>
<p><span style="font-size:14px;">  producer = new Producer&lt;String, String&gt;(new ProducerConfig(props));<br>
 }</span></p>
<p><span style="font-size:14px;"> void produce() {<br>
  int messageNo = 1000;<br>
  final int COUNT = 2000;</span></p>
<p><span style="font-size:14px;">  while (messageNo &lt; COUNT) {<br>
   String key = String.valueOf(messageNo);<br>
   String data = "hello kafka message " + key;<br>
   producer.send(new KeyedMessage&lt;String, String&gt;(TOPIC, key, data));<br>
   System.out.println(data);<br>
   messageNo++;<br>
  }<br>
 }</span></p>
<p><span style="font-size:14px;"> public static void main(String[] args) {<br>
  new KafkaProducer().produce();<br>
 }<br>
}</span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;">3.消费者</span></p>
<p><span style="font-size:14px;">package cn.com.kafka.test;</span></p>
<p><span style="font-size:14px;">import java.util.HashMap;<br>
import java.util.List;<br>
import java.util.Map;<br>
import java.util.Properties;</span></p>
<p><span style="font-size:14px;">import kafka.consumer.ConsumerConfig;<br>
import kafka.consumer.ConsumerIterator;<br>
import kafka.consumer.KafkaStream;<br>
import kafka.javaapi.consumer.ConsumerConnector;<br>
import kafka.serializer.StringDecoder;<br>
import kafka.utils.VerifiableProperties;</span></p>
<p><span style="font-size:14px;">public class KafkaConsumer {<br>
 private final ConsumerConnector consumer;</span></p>
<p><span style="font-size:14px;"> private KafkaConsumer() {<br>
  // Properties props = new Properties();<br>
  // // zookeeper 配置<br>
  // props.put("zookeeper.connect", "192.168.52.129:2181");<br>
  //<br>
  // // group 代表一个消费组<br>
  // props.put("group.id", "jd-group");<br>
  //<br>
  // // zk连接超时<br>
  // props.put("zookeeper.session.timeout.ms", "4000");<br>
  // props.put("zookeeper.sync.time.ms", "200");<br>
  // props.put("auto.commit.interval.ms", "1000");<br>
  // props.put("auto.offset.reset", "smallest");<br>
  // // 序列化类<br>
  // props.put("serializer.class", "kafka.serializer.StringEncoder");</span></p>
<p><span style="font-size:14px;">  Properties props = new Properties();<br>
  props.put("zookeeper.connect", "192.168.52.129:2181");<br>
  props.put("group.id", "group1");<br>
  props.put("zookeeper.session.timeout.ms", "10000");     //设置超时的时间要比较大<br>
  props.put("zookeeper.sync.time.ms", "200");<br>
  props.put("auto.commit.interval.ms", "1000");</span></p>
<p><span style="font-size:14px;">  ConsumerConfig config = new ConsumerConfig(props);</span></p>
<p><span style="font-size:14px;">  consumer = kafka.consumer.Consumer.createJavaConsumerConnector(config);<br>
 }</span></p>
<p><span style="font-size:14px;"> void consume() {<br>
  Map&lt;String, Integer&gt; topicCountMap = new HashMap&lt;String, Integer&gt;();<br>
  topicCountMap.put(KafkaProducer.TOPIC, new Integer(1));</span></p>
<p><span style="font-size:14px;">  StringDecoder keyDecoder = new StringDecoder(new VerifiableProperties());<br>
  StringDecoder valueDecoder = new StringDecoder(<br>
    new VerifiableProperties());</span></p>
<p><span style="font-size:14px;">  Map&lt;String, List&lt;KafkaStream&lt;String, String&gt;&gt;&gt; consumerMap = consumer<br>
    .createMessageStreams(topicCountMap, keyDecoder, valueDecoder);<br>
  KafkaStream&lt;String, String&gt; stream = consumerMap.get(<br>
    KafkaProducer.TOPIC).get(0);<br>
  ConsumerIterator&lt;String, String&gt; it = stream.iterator();<br>
  while (it.hasNext())<br>
   System.out.println("consumer is:"+it.next().message());<br>
 }</span></p>
<p><span style="font-size:14px;"> public static void main(String[] args) {<br>
  new KafkaConsumer().consume();<br>
 }</span></p>
<p><span style="font-size:14px;">}</span></p>
<p><span style="font-size:14px;"></span><br></p>
<p>备注：连接Zookeeper时比较耗时间也说不定，于是将timeout值加大</p>
<p><br></p>
<p>参照网址:</p>
<p><a href="http://blog.sina.com.cn/s/blog_3fe961ae01011o4z.html" rel="nofollow">http://blog.sina.com.cn/s/blog_3fe961ae01011o4z.html</a></p>
<p><a href="http://www.open-open.com/lib/view/open1412991579999.html" rel="nofollow">http://www.open-open.com/lib/view/open1412991579999.html</a></p>
<p><a href="http://www.zhixing123.cn/ubuntu/49444.html" rel="nofollow">http://www.zhixing123.cn/ubuntu/49444.html</a></p>
<p><br></p>
<div>﻿﻿</div>
<div>﻿﻿</div>
<div>﻿﻿</div>
            </div>
                </div>