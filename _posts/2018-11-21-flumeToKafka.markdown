---
layout:     post
title:      flumeToKafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<ol>
<li>数据采集<br>
思路：<br>
a) 配置kafka，启动zookeeper和kafka集群；<br>
b) 创建kafka主题；<br>
c) 启动kafka控制台消费者（此消费者只用于测试使用）；<br>
d) 配置flume，监控日志文件；<br>
e) 启动flume监控任务；<br>
f) 运行日志生产脚本；<br>
g) 观察测试。<br>
1）启动zookeeper，kafka集群<br>
$/opt/module/kafka/bin/kafka-server-start.sh /opt/module/kafka/config/server.properties<br>
2）创建kafka主题<br>
$ /opt/module/kafka/bin/kafka-topics.sh --zookeeper hadoop102:2181 --topic calllog –-create --replication-factor 1 --partitions 3<br>
检查一下是否创建主题成功：<br>
$ /opt/module/kafka/bin/kafka-topics.sh --zookeeper hadoop102:2181 --list<br>
3）启动kafka控制台消费者，等待flume信息的输入<br>
$ /opt/module/kafka/bin/kafka-console-consumer.sh --zookeeper hadoop102:2181 –topic<br>
calllog --from-beginning<br>
4）配置flume(flume2kafka.conf)</li>
</ol>
<h3><a id="define_19"></a>define</h3>
<p>a1.sources = r1<br>
a1.sinks = k1<br>
a1.channels = c1</p>
<p>###source<br>
a1.sources.r1.type = exec<br>
a1.sources.r1.command = tail -F -c +0 /opt/module/calllog/calllog.csv<br>
a1.sources.r1.shell = /bin/bash -c</p>
<h3><a id="sink_29"></a>sink</h3>
<p>a1.sinks.k1.type = org.apache.flume.sink.kafka.KafkaSink<br>
a1.sinks.k1.kafka.bootstrap.servers = hadoop101:9092,hadoop102:9092,hadoop103:9092<br>
a1.sinks.k1.kafka.topic = calllog<br>
a1.sinks.k1.kafka.flumeBatchSize = 20<br>
a1.sinks.k1.kafka.producer.acks = 1<br>
<a href="http://a1.sinks.k1.kafka.producer.linger.ms" rel="nofollow">a1.sinks.k1.kafka.producer.linger.ms</a> = 1</p>
<h3><a id="channel_37"></a>channel</h3>
<p>a1.channels.c1.type = memory<br>
a1.channels.c1.capacity = 1000<br>
a1.channels.c1.transactionCapacity = 100</p>
<h3><a id="bind_42"></a>bind</h3>
<p>a1.sources.r1.channels = c1<br>
a1.sinks.k1.channel = c1<br>
5）启动flume<br>
$ /opt/module/flume/bin/flume-ng agent --conf /opt/module/flume/conf/ --name a1 --conf-file /home/atguigu/calllog/flume2kafka.conf<br>
6）运行生产日志的任务脚本，观察kafka控制台消费者是否成功显示产生的数据<br>
$ sh /home/atguigu/calllog/productlog.sh</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>