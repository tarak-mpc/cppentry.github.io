---
layout:     post
title:      Flume和Kafka集成错误:Error reading field 'throttle_time_ms'
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>Flume和Kafka集成错误</p>

<p>1.问题描述：</p>

<p>flume向kafka写入数据的时候报错：</p>

<pre class="has">
<code>2018-09-15 00:10:08,502 (kafka-producer-network-thread | producer-1) [ERROR - org.apache.kafka.clients.producer.internals.Sender.run(Sender.java:130)] Uncaught error in kafka producer I/O thread: 
org.apache.kafka.common.protocol.types.SchemaException: Error reading field 'throttle_time_ms': java.nio.BufferUnderflowException
        at org.apache.kafka.common.protocol.types.Schema.read(Schema.java:71)
        at org.apache.kafka.clients.NetworkClient.handleCompletedReceives(NetworkClient.java:439)
        at org.apache.kafka.clients.NetworkClient.poll(NetworkClient.java:265)
        at org.apache.kafka.clients.producer.internals.Sender.run(Sender.java:216)
        at org.apache.kafka.clients.producer.internals.Sender.run(Sender.java:128)
        at java.lang.Thread.run(Thread.java:748)</code></pre>

<p> </p>

<p>2.原因：版本不兼容，使用flume1.7.0而kafka时0.8.2.1，flume的conf配置文件适合0.9+的版本的kafka，所以报错。</p>

<p> </p>

<p>3.解决：</p>

<p>(1)flume1.7.0和kafka_2.11-0.10.2.1是可以合成的。</p>

<p>(2)flume1.6.0和kafka_2.10-0.8.2.1是可以合成的。</p>

<p> </p>            </div>
                </div>