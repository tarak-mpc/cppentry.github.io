---
layout:     post
title:      windows系统flume数据传给kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>1、安装zookeeper</p>

<p>更改flume配置为kafka</p>

<p># Describe the sink<br>
# a1.sinks.k1.type = logger<br>
 # 指定Flume sink<br>
a1.sinks.k1.channel = c1<br>
a1.sinks.k1.type = org.apache.flume.sink.kafka.KafkaSink<br>
a1.sinks.k1.kafka.topic = test<br>
a1.sinks.k1.kafka.bootstrap.servers =172.22.20.59:9092<br>
a1.sinks.k1.kafka.flumeBatchSize = 100<br>
a1.sinks.k1.kafka.producer.acks = 1<br>
a1.sinks.k1.kafka.producer.linger.ms = 1<br>
a1.sinks.k1.kafka.producer.compression.type = snappy<br>
 a.系统变量中添加ZOOKEEPER_HOME=E:\dashuju\zookeeper-3.5.4-beta</p>

<p> b.编辑系统变量中的path变量，增加%ZOOKEEPER_HOME%\bin</p>

<p>conf 添加文件 zoo.cfg</p>

<p># The number of milliseconds of each tick  心跳间隔 毫秒每次</p>

<p>tickTime=2000</p>

<p># The number of ticks that the initial</p>

<p># synchronization phase can take</p>

<p>initLimit=10</p>

<p># The number of ticks that can pass between</p>

<p># sending a request and getting anacknowledgement</p>

<p>syncLimit=5</p>

<p># the directory where the snapshot isstored.  //镜像数据位置</p>

<p>dataDir=E:\\data\\zookeeper</p>

<p>#日志位置</p>

<p>dataLogDir=E:\\zooker\\zookeeper</p>

<p># the port at which the clients willconnect  客户端连接的端口</p>

<p>clientPort=2181</p>

<p><br>
运行zookeeper<br>
zkserver<br>
2、运行kafka<br>
cd D:\kafka_2.11-1.1.0<br>
在命令行中输入：<br>
.\bin\windows\kafka-server-start.bat .\config\server.properties   <br>
3、建立topic<br>
 D:\kafka_2.11-1.1.0\bin\windows\kafka-topics.bat --create --zookeeper 172.22.20.59:2181 --replication-factor 1 -partitions 1 --topic test created topic "test"<br>
3.1、 查看topic<br>
 D:\kafka_2.11-1.1.0\bin\windows\kafka-topics.bat --list  --zookeeper  172.22.20.59:2181<br>
4、启动生产者<br>
 D:\kafka_2.11-1.1.0\bin\windows\kafka-console-producer.bat --broker-list 172.22.20.59:9092 --topic test<br>
 <br>
5、启动消费消息<br>
 <br>
 D:\kafka_2.11-1.1.0\bin\windows\kafka-console-consumer.bat --zookeeper 172.22.20.59:2181 --from-beginning --topic test<br>
 </p>            </div>
                </div>