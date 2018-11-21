---
layout:     post
title:      flume连接kafka报错 Excessively large list allocation request detected: 1818583411 items! Connection clos
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h3>1.问题描述：</h3>

<p>flume然后sinkTokafka，flume是1.6.0然后kafka是0.8.2.1。flume和kafka自测都没有问题，但是flumeTokafka就会：</p>

<pre class="has">
<code>2018-11-21 01:09:16,119 (lifecycleSupervisor-1-1) [INFO - kafka.utils.Logging$class.info(Logging.scala:68)] Property key.serializer.class is overridden to kafka.serializer.StringEncoder
2018-11-21 01:09:16,119 (lifecycleSupervisor-1-1) [INFO - kafka.utils.Logging$class.info(Logging.scala:68)] Property metadata.broker.list is overridden to hadoop:9092
2018-11-21 01:09:16,119 (lifecycleSupervisor-1-1) [INFO - kafka.utils.Logging$class.info(Logging.scala:68)] Property request.required.acks is overridden to 1
2018-11-21 01:09:16,119 (lifecycleSupervisor-1-1) [INFO - kafka.utils.Logging$class.info(Logging.scala:68)] Property serializer.class is overridden to kafka.serializer.DefaultEncoder
2018-11-21 01:09:16,188 (lifecycleSupervisor-1-1) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.register(MonitoredCounterGroup.java:120)] Monitored counter group for type: SINK, name: kafka-sink: Successfully registered new MBean.
2018-11-21 01:09:16,188 (lifecycleSupervisor-1-1) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.start(MonitoredCounterGroup.java:96)] Component type: SINK, name: kafka-sink started
2018-11-21 01:09:16,189 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.register(MonitoredCounterGroup.java:120)] Monitored counter group for type: SOURCE, name: avro-source: Successfully registered new MBean.
2018-11-21 01:09:16,189 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.start(MonitoredCounterGroup.java:96)] Component type: SOURCE, name: avro-source started
2018-11-21 01:09:16,189 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.source.AvroSource.start(AvroSource.java:253)] Avro source avro-source started.
2018-11-21 01:09:37,874 (New I/O server boss #9) [INFO - org.apache.avro.ipc.NettyServer$NettyServerAvroHandler.handleUpstream(NettyServer.java:171)] [id: 0x83134623, /172.16.0.147:55290 =&gt; /172.16.0.147:41414] OPEN
2018-11-21 01:09:37,875 (New I/O worker #1) [INFO - org.apache.avro.ipc.NettyServer$NettyServerAvroHandler.handleUpstream(NettyServer.java:171)] [id: 0x83134623, /172.16.0.147:55290 =&gt; /172.16.0.147:41414] BOUND: /172.16.0.147:41414
2018-11-21 01:09:37,875 (New I/O worker #1) [INFO - org.apache.avro.ipc.NettyServer$NettyServerAvroHandler.handleUpstream(NettyServer.java:171)] [id: 0x83134623, /172.16.0.147:55290 =&gt; /172.16.0.147:41414] CONNECTED: /172.16.0.147:55290
2018-11-21 01:09:44,010 (New I/O worker #1) [INFO - org.apache.avro.ipc.NettyServer$NettyServerAvroHandler.handleUpstream(NettyServer.java:171)] [id: 0x83134623, /172.16.0.147:55290 :&gt; /172.16.0.147:41414] DISCONNECTED
2018-11-21 01:09:44,010 (New I/O worker #1) [INFO - org.apache.avro.ipc.NettyServer$NettyServerAvroHandler.handleUpstream(NettyServer.java:171)] [id: 0x83134623, /172.16.0.147:55290 :&gt; /172.16.0.147:41414] UNBOUND
2018-11-21 01:09:44,010 (New I/O worker #1) [INFO - org.apache.avro.ipc.NettyServer$NettyServerAvroHandler.handleUpstream(NettyServer.java:171)] [id: 0x83134623, /172.16.0.147:55290 :&gt; /172.16.0.147:41414] CLOSED
2018-11-21 01:09:44,010 (New I/O worker #1) [INFO - org.apache.avro.ipc.NettyServer$NettyServerAvroHandler.channelClosed(NettyServer.java:209)] Connection to /172.16.0.147:55290 disconnected.
2018-11-21 01:09:44,012 (New I/O worker #1) [WARN - org.apache.avro.ipc.NettyServer$NettyServerAvroHandler.exceptionCaught(NettyServer.java:201)] Unexpected exception from downstream.
org.apache.avro.AvroRuntimeException: Excessively large list allocation request detected: 1818583411 items! Connection closed.
        at org.apache.avro.ipc.NettyTransportCodec$NettyFrameDecoder.decodePackHeader(NettyTransportCodec.java:167)
        at org.apache.avro.ipc.NettyTransportCodec$NettyFrameDecoder.decode(NettyTransportCodec.java:139)
        at org.jboss.netty.handler.codec.frame.FrameDecoder.callDecode(FrameDecoder.java:425)
        at org.jboss.netty.handler.codec.frame.FrameDecoder.messageReceived(FrameDecoder.java:303)
        at org.jboss.netty.channel.Channels.fireMessageReceived(Channels.java:268)
        at org.jboss.netty.channel.Channels.fireMessageReceived(Channels.java:255)
        at org.jboss.netty.channel.socket.nio.NioWorker.read(NioWorker.java:88)
        at org.jboss.netty.channel.socket.nio.AbstractNioWorker.process(AbstractNioWorker.java:107)
        at org.jboss.netty.channel.socket.nio.AbstractNioSelector.run(AbstractNioSelector.java:312)
        at org.jboss.netty.channel.socket.nio.AbstractNioWorker.run(AbstractNioWorker.java:88)
        at org.jboss.netty.channel.socket.nio.NioWorker.run(NioWorker.java:178)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
        at java.lang.Thread.run(Thread.java:748)</code></pre>

<h3>2.环境</h3>

<p>（1）flume的配置</p>

<pre class="has">
<code>avro-memory-kafka.sources = avro-source
avro-memory-kafka.sinks = kafka-sink
avro-memory-kafka.channels = memory-channel

avro-memory-kafka.sources.avro-source.type = avro
avro-memory-kafka.sources.avro-source.bind = hadoop
avro-memory-kafka.sources.avro-source.port = 41414

avro-memory-kafka.sinks.kafka-sink.type = org.apache.flume.sink.kafka.KafkaSink
avro-memory-kafka.sinks.kafka-sink.brokerList = hadoop:9092
avro-memory-kafka.sinks.kafka-sink.topic = hello2
avro-memory-kafka.sinks.kafka-sink.batchSize = 5
avro-memory-kafka.sinks.kafka-sink.requiredAcks =1 

avro-memory-kafka.channels.memory-channel.type = memory

avro-memory-kafka.sources.avro-source.channels = memory-channel
avro-memory-kafka.sinks.kafka-sink.channel = memory-channel
</code></pre>

<p>（2）测试</p>

<pre class="has">
<code>telnet hadoop 41414
Trying 172.16.0.147...
Connected to hadoop.
Escape character is '^]'.
ffff
rrrr
Connection closed by foreign host.</code></pre>

<h3>3.原因</h3>

<p>flume的avro sources和telnet的信息是不匹配的，这个是神坑爹的。</p>

<h3>4.解决</h3>

<p>flume的sources.type换成netcat即可！</p>

<p>收获就是需要仔细阅读文档。</p>

<p> </p>            </div>
                </div>