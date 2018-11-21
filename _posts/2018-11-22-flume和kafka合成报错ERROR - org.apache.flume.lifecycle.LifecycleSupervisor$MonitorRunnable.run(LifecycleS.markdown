---
layout:     post
title:      flume和kafka合成报错ERROR - org.apache.flume.lifecycle.LifecycleSupervisor$MonitorRunnable.run(LifecycleS
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1>0.环境：</h1>

<p>flume1.6.0和kafka_2.10-0.8.2.1</p>

<p> </p>

<h1>1.问题描述：</h1>

<p>flume和kafka集成，flume输出到kafka：</p>

<h2>(1)flume配置文件：</h2>

<pre class="has">
<code>agent1.sources=avro-source
agent1.channels=logger-channel
agent1.sinks=kafka-sink

#define source
agent1.sources.avro-source.type=avro
agent1.sources.avro-source.bind=0.0.0.0
agent1.sources.avro-source.port=41414

#define channel
agent1.channels.logger-channel.type=memory

#define sink
agent1.sinks.kafka-sink.type=org.apache.flume.sink.kafka.KafkaSink
agent1.sinks.kafka-sink.topic = streamingtopic
agent1.sinks.kafka-sink.brokerList = bigdata.ibeifeng.com:9092
agent1.sinks.kafka-sink.requiredAcks = 1
agent1.sinks.kafka-sink.batchSize = 20

agent1.sources.avro-source.channels=logger-channel
agent1.sinks.kafka-sink.channel=logger-channel
</code></pre>

<h2>(2)开启flume后报错：</h2>

<pre class="has">
<code>bin/flume-ng agent --conf conf --conf-file conf/streaming2.conf --name agent1 -Dflume.root.logger=INFO,console</code></pre>

<p>报错信息，</p>

<pre class="has">
<code>2018-09-23 16:14:38,931 (lifecycleSupervisor-1-1) [ERROR - org.apache.flume.lifecycle.LifecycleSupervisor$MonitorRunnable.run(LifecycleSupervisor.java:253)] Unable to start SinkRunner: { policy:org.apache.flume.sink.DefaultSinkProcessor@27b9f530 counterGroup:{ name:null counters:{} } } - Exception follows.
java.lang.NoClassDefFoundError: scala/collection/GenTraversableOnce$class
        at kafka.utils.Pool.&lt;init&gt;(Pool.scala:28)
        at kafka.producer.ProducerStatsRegistry$.&lt;init&gt;(ProducerStats.scala:34)
        at kafka.producer.ProducerStatsRegistry$.&lt;clinit&gt;(ProducerStats.scala)
        at kafka.producer.async.DefaultEventHandler.&lt;init&gt;(DefaultEventHandler.scala:48)
        at kafka.producer.Producer.&lt;init&gt;(Producer.scala:59)
        at kafka.javaapi.producer.Producer.&lt;init&gt;(Producer.scala:26)
        at org.apache.flume.sink.kafka.KafkaSink.start(KafkaSink.java:164)
        at org.apache.flume.sink.DefaultSinkProcessor.start(DefaultSinkProcessor.java:46)
        at org.apache.flume.SinkRunner.start(SinkRunner.java:79)
        at org.apache.flume.lifecycle.LifecycleSupervisor$MonitorRunnable.run(LifecycleSupervisor.java:251)
        at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:511)
        at java.util.concurrent.FutureTask.runAndReset(FutureTask.java:308)
        at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.access$301(ScheduledThreadPoolExecutor.java:180)
        at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.run(ScheduledThreadPoolExecutor.java:294)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
        at java.lang.Thread.run(Thread.java:748)
Caused by: java.lang.ClassNotFoundException: scala.collection.GenTraversableOnce$class
        at java.net.URLClassLoader.findClass(URLClassLoader.java:381)
        at java.lang.ClassLoader.loadClass(ClassLoader.java:424)
        at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:335)
        at java.lang.ClassLoader.loadClass(ClassLoader.java:357)
        ... 17 more
2018-09-23 16:14:38,940 (lifecycleSupervisor-1-1) [ERROR - org.apache.flume.lifecycle.LifecycleSupervisor$MonitorRunnable.run(LifecycleSupervisor.java:264)] Unsuccessful attempt to shutdown component: {} due to missing dependencies. Please shutdown the agentor disable this component, or the agent will bein an undefined state.
java.lang.NullPointerException
        at org.apache.flume.sink.kafka.KafkaSink.stop(KafkaSink.java:171)
        at org.apache.flume.sink.DefaultSinkProcessor.stop(DefaultSinkProcessor.java:53)
        at org.apache.flume.SinkRunner.stop(SinkRunner.java:115)
        at org.apache.flume.lifecycle.LifecycleSupervisor$MonitorRunnable.run(LifecycleSupervisor.java:259)
        at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:511)
        at java.util.concurrent.FutureTask.runAndReset(FutureTask.java:308)
        at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.access$301(ScheduledThreadPoolExecutor.java:180)
        at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.run(ScheduledThreadPoolExecutor.java:294)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
        at java.lang.Thread.run(Thread.java:748)
2018-09-23 16:14:39,023 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.register(MonitoredCounterGroup.java:120)] Monitored counter group for type: SOURCE, name: avro-source: Successfully registered new MBean.
2018-09-23 16:14:39,023 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.start(MonitoredCounterGroup.java:96)] Component type: SOURCE, name: avro-source started
2018-09-23 16:14:39,030 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.source.AvroSource.start(AvroSource.java:253)] Avro source avro-source started.

</code></pre>

<h1>2.原因：</h1>

<p>flume的libs的jar包兼容性问题，之前scala-library-2.10.1.jar的自己在streaming的时候，更改为scala-library-2.11.8.jar，导致兼容性问题，所以出现报错</p>

<p> </p>

<h1>3.解决：</h1>

<p>将scala-library的jar包，替换为原来的scala-library-2.10.1.jar，即可！</p>

<p> </p>

<h1>（如果本帖对大家有些许帮助，希望大家点赞~）</h1>            </div>
                </div>