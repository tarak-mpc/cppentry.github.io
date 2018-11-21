---
layout:     post
title:      Spark学习1_【Spark六十一】Spark Streaming组合Flume、Kafka进行日志分析
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span style="font-family:Tahoma;font-size:14px;line-height:24px;">【Spark六十一】Spark Streaming结合Flume、Kafka进行日志分析</span><br style="font-family:Tahoma;font-size:14px;line-height:24px;"><div class="iteye-blog-content-contain" style="font-family:Tahoma;font-size:14px;line-height:24px;">
<p>
第一步，Flume和Kakfa对接，Flume抓取日志，写到Kafka中</p>
<p>
第二部，Spark Streaming读取Kafka中的数据，进行实时分析</p>
<p>
 </p>
<p>
本文首先使用Kakfa自带的消息处理（脚本）来获取消息，走通Flume和Kafka的对接</p>
<h1>1. Flume配置</h1>
<p>
1. 下载Flume和Kafka集成的插件，下载地址：https://github.com/beyondj2ee/flumeng-kafka-plugin。将package目录中的flumeng-kafka-plugin.jar拷贝到Flume安装目录的lib目录下</p>
<p>
2. 将Kakfa安装目录libs目录下的如下jar包拷贝到Flume安装目录的lib目录下</p>
<p>
kafka_2.10-0.8.1.1.jar</p>
<p>
scala-library-2.10.1.jar</p>
<p>
metrics-core-2.2.0.jar</p>
<p>
3.添加agent配置</p>
<pre><code class="language-java">producer.sources = s
producer.channels = c
producer.sinks = r

#source section    
#producer.sources.s.type = seq    
producer.sources.s.type = netcat
producer.sources.s.bind = localhost
producer.sources.s.port = 44444
producer.sources.s.channels = c

# Each sink's type must be defined    
producer.sinks.r.type = org.apache.flume.plugins.KafkaSink
producer.sinks.r.metadata.broker.list=127.0.0.1:9092
producer.sinks.r.partition.key=0
producer.sinks.r.partitioner.class=org.apache.flume.plugins.SinglePartition
producer.sinks.r.serializer.class=kafka.serializer.StringEncoder
producer.sinks.r.request.required.acks=0
producer.sinks.r.max.message.size=1000000
producer.sinks.r.producer.type=sync
producer.sinks.r.custom.encoding=UTF-8
producer.sinks.r.custom.topic.name=test

#Specify the channel the sink should use    
producer.sinks.r.channel = c

# Each channel's type is defined.    
producer.channels.c.type = memory
producer.channels.c.capacity = 1000</code></pre>
<p>
3.1 上面指定了sink的类型为KafkaSink，目的是将日志送往Kafka消息队列，分区类为SinglePartition</p>
<p>
3.2  指定topic的名字为test</p>
<p>
3.3 指定Flume的消息源来自于netcat，(localhost,44444)</p>
<p>
 </p>
<p>
4. 启动Flume</p>
<p>
 </p>
<pre><code class="language-java">./flume-ng agent -f ../conf/kafka.conf  -c . -n producer</code></pre>
<p>
 指定配置文件和agent的名字</p>
<p>
 </p>
<h1>Kafka配置</h1>
<p>
 </p>
<p>
5. 启动Kafka</p>
<pre><code class="language-java">./kafka-server-start.sh ../config/server.properties</code></pre>
<p>
5.1 启动Kafka依赖的Zookeeper，添加topic名字为test，详见</p>
<p>
5.2 启动Kakfa的消息接收进程</p>
<pre><code class="language-java">bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic test --from-beginning</code></pre>
<p>
 </p>
<p>
6.启动telnet，输入netcat接受的数据</p>
<p>
 </p>
<pre><code class="language-java">telnet localhost 44444</code></pre>
<p>
 </p>
<h1>数据流转过程</h1>
<p>
1. 在telnet终端输入数据，被Flume的source接受</p>
<p>
2. Flume将数据写入到Kafka消息队列中，在Flume_Kafka的插件中有向Kafka发送消息的逻辑</p>
<p>
3. Kafka消息消费者，监听到Kafka队列中来了消息，那么就在Kakfa的消息接收端看到控制台上有输出</p>
<p>
 </p>
<h1>问题：</h1>
<p>
1. 此处Kafka使用SinglePartition的方式接收消息，如果是Kafka集群，那么Flume如何写入消息到一个topic的多个partition中</p>
<p>
2. Flume的消息源是监听端口44444实现的，如何监听日志文件呢？日志文件可以自动增长，另外也会自动的创建新的日志文件，这用Kafka如何处理？</p>
<p>
 </p>
<p>
 </p>
<h1>关于Kafka的Partition</h1>
<p>
 1. 第一个问题，SinglePartition的实现</p>
<pre><code class="language-java">package org.apache.flume.plugins;
import kafka.producer.Partitioner;
import kafka.utils.VerifiableProperties;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
public class SinglePartition implements Partitioner&lt;String&gt; {

public SinglePartition(VerifiableProperties props) {
}
@Override
public int partition(String key, int numberOfPartions) {
  return 0;
}
}</code></pre>
<p>
 可见，只要把partition方法实现为 key.hashCode()%numberOfPartitions即可</p>
<p>
 </p>
<p>
2. 第二个问题，如何设置Kafka的一个topic几个partition？</p>
<p>
在创建topic时，就需要指定partition的个数</p>
<pre><code class="language-java">bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test</code></pre>
<p>
建立一个分区数为17，复制因为为3的topic，看看zk上记录了哪些信息，</p>
<p>
 </p>
<pre><code class="language-java">bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 3 --partitions 17 --topic test</code></pre>
<p>
 </p>
<p>
2.1. 报错：也就是说，复制因子不能比brokers的个数大</p>
<pre><code class="language-java">[hadoop@hadoop kafka_2.10-0.8.1.1]$ bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 3 --partitions 17 --topic test_many_partitions
Error while executing topic command replication factor: 3 larger than available brokers: 1
kafka.admin.AdminOperationException: replication factor: 3 larger than available brokers: 1
	at kafka.admin.AdminUtils$.assignReplicasToBrokers(AdminUtils.scala:70)
	at kafka.admin.AdminUtils$.createTopic(AdminUtils.scala:155)
	at kafka.admin.TopicCommand$.createTopic(TopicCommand.scala:86)
	at kafka.admin.TopicCommand$.main(TopicCommand.scala:50)
	at kafka.admin.TopicCommand.main(TopicCommand.scala)</code></pre>
<p>
 </p>
<p>
2.2 新建了topic后，Kafka server日志显示</p>
<pre><code class="language-java">[2015-02-14 02:53:53,526] INFO Completed load of log test_many_partitions-4 with log end offset 0 (kafka.log.Log)
[2015-02-14 02:53:53,526] INFO Created log for partition [test_many_partitions,4] in /tmp/kafka-logs with properties {segment.index.bytes -&gt; 10485760, file.delete.delay.ms -&gt; 60000, segment.bytes -&gt; 536870912, flush.ms -&gt; 9223372036854775807, delete.retention.ms -&gt; 86400000, index.interval.bytes -&gt; 4096, retention.bytes -&gt; -1, cleanup.policy -&gt; delete, segment.ms -&gt; 604800000, max.message.bytes -&gt; 1000012, flush.messages -&gt; 9223372036854775807, min.cleanable.dirty.ratio -&gt; 0.5, retention.ms -&gt; 604800000}. (kafka.log.LogManager)
[2015-02-14 02:53:53,527] WARN Partition [test_many_partitions,4] on broker 0: No checkpointed highwatermark is found for partition [test_many_partitions,4] (kafka.cluster.Partition)
[2015-02-14 02:53:53,540] INFO Completed load of log test_many_partitions-13 with log end offset 0 (kafka.log.Log)
[2015-02-14 02:53:53,541] INFO Created log for partition [test_many_partitions,13] in /tmp/kafka-logs with properties {segment.index.bytes -&gt; 10485760, file.delete.delay.ms -&gt; 60000, segment.bytes -&gt; 536870912, flush.ms -&gt; 9223372036854775807, delete.retention.ms -&gt; 86400000, index.interval.bytes -&gt; 4096, retention.bytes -&gt; -1, cleanup.policy -&gt; delete, segment.ms -&gt; 604800000, max.message.bytes -&gt; 1000012, flush.messages -&gt; 9223372036854775807, min.cleanable.dirty.ratio -&gt; 0.5, retention.ms -&gt; 604800000}. (kafka.log.LogManager)
[2015-02-14 02:53:53,541] WARN Partition [test_many_partitions,13] on broker 0: No checkpointed highwatermark is found for partition [test_many_partitions,13] (kafka.cluster.Partition)
[2015-02-14 02:53:53,554] INFO Completed load of log test_many_partitions-1 with log end offset 0 (kafka.log.Log)
[2015-02-14 02:53:53,555] INFO Created log for partition [test_many_partitions,1] in /tmp/kafka-logs with properties {segment.index.bytes -&gt; 10485760, file.delete.delay.ms -&gt; 60000, segment.bytes -&gt; 536870912, flush.ms -&gt; 9223372036854775807, delete.retention.ms -&gt; 86400000, index.interval.bytes -&gt; 4096, retention.bytes -&gt; -1, cleanup.policy -&gt; delete, segment.ms -&gt; 604800000, max.message.bytes -&gt; 1000012, flush.messages -&gt; 9223372036854775807, min.cleanable.dirty.ratio -&gt; 0.5, retention.ms -&gt; 604800000}. (kafka.log.LogManager)
[2015-02-14 02:53:53,555] WARN Partition [test_many_partitions,1] on broker 0: No checkpointed highwatermark is found for partition [test_many_partitions,1] (kafka.cluster.Partition)</code></pre>
<p>
 3.3 查看zk上关于具有多partition的topic，结果如下：</p>
<p>
 </p>
<p>
17个partition</p>
<pre><code class="language-java">[zk: localhost:2181(CONNECTED) 26] ls /brokers/topics
[test_many_partitions, test]
[zk: localhost:2181(CONNECTED) 27] ls /brokers/topics/test_many_partitions
[partitions]
[zk: localhost:2181(CONNECTED) 28] ls /brokers/topics/test_many_partitions/partitions
[15, 16, 13, 14, 11, 12, 3, 2, 1, 10, 0, 7, 6, 5, 4, 9, 8]
[zk: localhost:2181(CONNECTED) 29] </code></pre>
<p>
 </p>
<p>
1个partition</p>
<pre><code class="language-java">[zk: localhost:2181(CONNECTED) 30] ls /brokers/topics/test 
[partitions]
[zk: localhost:2181(CONNECTED) 31] ls /brokers/topics/test/partitions
[0]</code></pre>
<p>
 </p>
<p>
参考：</p>
<p>
https://github.com/beyondj2ee/flumeng-kafka-plugin</p>
<p>
http://blog.csdn.net/weijonathan/article/details/18301321</p>
<p>
http://liyonghui160com.iteye.com/blog/2173235</p>
<p>
<br></p>
<p>
原文来自：</p>
<p>
http://www.myexception.cn/open-source/1850714.html<br></p>
</div>
            </div>
                </div>