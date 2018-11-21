---
layout:     post
title:      【Strom篇】Flume+Kafaka+Strom整合完成信息记录
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/chenshi_2753/article/details/80216640				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><font color="red"><strong>第一部分：流程分析</strong></font>  <br>
<img src="https://img-blog.csdn.net/20180506172251128?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NoZW5zaGlfMjc1Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
由flume收集客户端发送的信息，经过kafka集群消费者收集，然后给storm集群数据处理（数据清洗），最后再发给kafka集群收集。</p>

<p><font color="red"><strong>第二部分：集群搭建</strong></font>  <br>
<strong>flume+kafka</strong> <br>
一、配合flume <br>
修改flume-env.sh里的JAVA_HOME的位置，（flume仅修改此处） <br>
（若查看flume的版本通过：./bin flume-ng version） <br>
二、创建fk.conf （flume和kafka整合配置项） </p>

<blockquote>
  <p>a1.sources = r1 <br>
  a1.sinks = k1 <br>
  a1.channels = c1 <br>
  // Describe/configure the source <br>
  a1.sources.r1.type = avro <br>
  a1.sources.r1.bind = node02 //注意修改  <br>
  a1.sources.r1.port = 41414 <br>
  //Describe the sink <br>
  a1.sinks.k1.type = org.apache.flume.sink.kafka.KafkaSink <br>
  a1.sinks.k1.topic = testflume <br>
  a1.sinks.k1.brokerList = node02:9092,node03:9092,node04:9092 <br>
  a1.sinks.k1.requiredAcks = 1 <br>
  a1.sinks.k1.batchSize = 20 <br>
  a1.sinks.k1.channel = c1 <br>
  //Use a channel which buffers events in memory <br>
  a1.channels.c1.type = memory <br>
  a1.channels.c1.capacity = 1000000 <br>
  a1.channels.c1.transactionCapacity = 10000 <br>
  //Bind the source and sink to the channel <br>
  a1.sources.r1.channels = c1 <br>
  a1.sinks.k1.channel = c1</p>
</blockquote>

<p>三、启动Flume + Kafka <br>
1.启动zk集群 <br>
<code>zkServer.sh start zkServer.sh status</code> <br>
2.启动Kafka集群。 <br>
<code>bin/kafka-server-start.sh config/server.properties</code> <br>
3.配置Flume集群，并启动Flume集群。 <br>
<code>bin/flume-ng agent -n a1 -c conf -f conf/fk.conf -Dflume.root.logger=DEBUG,console</code> <br>
4.启动消费者： <br>
bin/kafka-console-consumer.sh –zookeeper node06:2181,node07:2181,node08:2181 –from-beginning –topic testflume <br>
5.启动生产者 <br>
bin/kafka-topics.sh –zookeeper node06:2181,node07:2181,node08:2181 –create –replication-factor 2 –partitions 1 –topic LogError <br>
6.测试客户端到flume过程 <br>
运行“<a href="http://flume.apache.org/FlumeDeveloperGuide.html" rel="nofollow">RpcClientDemo</a>”代码，通过rpc请求发送数据到Flume集群。 <br>
<code>bin/kafka-console-consumer.sh --zookeeper node02:2181,node03:2181,node04:2181 --from-beginning --topic testflume</code></p>

<p><strong>Storm + Kafka</strong> <br>
一、配置kafka <br>
修改server.properties里的 <br>
<code>zookeeper.connect=node02:2181,node03:2181,node04:2181</code> <br>
二、测试storm和kafka之间的连接 <br>
kafka创建消费者： <br>
<code>bin/kafka-topics.sh --zookeeper node06:2181,node07:2181,node08:2181 --create --replication-factor 2 --partitions 1 --topic LogError</code> <br>
LogFilterTopology.java启动后，观察<code>bin/kafka-console-consumer.sh --zookeeper node02:2181,node03:2181,node04:2181 --from-beginning --topic LogError</code> <br>
联测，flume+storm+kafka <br>
先启动LogFilterTopology.java，再启动RpcClientDemo.java 观察LogError的消费者的输出即可。</p>

<p>备： <br>
附完整版Flume+Kafka+Storm架构设计图 <br>
<img src="https://img-blog.csdn.net/20180506180127610?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NoZW5zaGlfMjc1Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>