---
layout:     post
title:      hadoop集群配置flume
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>1.先从官网下载flume的jar包。我们下载最新的Apache Flume binary (tar.gz) 1.8版本</p>
<p>地址：http://www.apache.org/dyn/closer.lua/flume/1.8.0/apache-flume-1.8.0-bin.tar.gz</p>
<p>2.之后再hadoop中解压</p>
<p>tar -zxvf apache-flume-1.8.0-bin.tar.gz</p>
<p>然后我们可以改变文件名，这样好操作（可以不改）</p>
<p>mv apache-flume-1.8.0-bin.tar.gz flume</p>
<p>3.之后前往/etc/profile配置环境变量（不配也可以，配了之后命令好操作）</p>
<p>export ZOOKEEPER_HOME=/home/zookeeper<br>
export PATH=$PATH:$ZOOKEEPER_HOME/bin:/home/flume/bin<br>
4.之后创建一个文件test</p>
<p></p>
<pre><span class="c"># example.conf: A single-node Flume configuration</span>

<span class="c"># Name the components on this agent</span>
<span class="na">a1.sources</span> <span class="o">=</span> <span class="s">r1</span>
<span class="na">a1.sinks</span> <span class="o">=</span> <span class="s">k1</span>
<span class="na">a1.channels</span> <span class="o">=</span> <span class="s">c1</span>

<span class="c"># Describe/configure the source</span>
<span class="na">a1.sources.r1.type</span> <span class="o">=</span> <span class="s">netcat</span>
<span class="na">a1.sources.r1.bind</span> <span class="o">=</span> node1
<span class="na">a1.sources.r1.port</span> <span class="o">=</span> <span class="s">44444</span>

<span class="c"># Describe the sink</span>
<span class="na">a1.sinks.k1.type</span> <span class="o">=</span> <span class="s">logger</span>

<span class="c"># Use a channel which buffers events in memory</span>
<span class="na">a1.channels.c1.type</span> <span class="o">=</span> <span class="s">memory</span>
<span class="na">a1.channels.c1.capacity</span> <span class="o">=</span> <span class="s">1000</span>
<span class="na">a1.channels.c1.transactionCapacity</span> <span class="o">=</span> <span class="s">100</span>

<span class="c"># Bind the source and sink to the channel</span>
<span class="na">a1.sources.r1.channels</span> <span class="o">=</span> <span class="s">c1</span>
<span class="na">a1.sinks.k1.channel</span> <span class="o">=</span> <span class="s">c1

</span></pre>
<p></p>
<p>注意：<span class="na">a1.sources.r1.bind</span> <span class="o">=</span> <span class="s">
localhost（改成你的主机地址）</span></p>
<p><span class="s">5.运行</span></p>
<p><span class="s"></span></p>
<pre>$ bin/flume-ng agent --conf conf --conf-file example.conf --name a1 -Dflume.root.logger=INFO,console</pre>
注意：由于设置source设置的是netcat，是tcp  所以我们要使用tcp协议的输入
<p></p>
<p><span class="s">我们可以使用telnet 192.168.56.101 44444来检测日志是否生成。</span></p>
<p><span class="s">最后我们<span class="s"></span></span></p>
<pre><span class="na">a1.sinks.k1.type</span> <span class="o">=</span> <span class="s">logger
我们sink输出的是logger所以就在node1节点打印输出
</span></pre>
<span class="s"></span><span class="s"></span><span class="s"></span><span class="s"></span><span class="s">6.上面的netcat是被动输出日志的，我</span><span class="s"></span><span class="s">们还可以使用一种主动地生成日志</span>
<p></p>
<p><span class="s"># example.conf: A single-node Flume configuration<br><br>
# Name the components on this agent<br>
a1.sources = r1<br>
a1.sinks = k1<br>
a1.channels = c1<br><br>
# Describe/configure the source<br>
a1.sources.r1.type = spooldir<br>
a1.sources.r1.spoolDir = /opt/flume<br><br>
# Describe the sink<br>
a1.sinks.k1.type = hdfs<br>
a1.sinks.k1.hdfs.path = hdfs://192.168.56.101:8020/flume/%Y-%m-%d/%H%M<br>
a1.sinks.k1.hdfs.rollCount = 0<br>
a1.sinks.k1.hdfs.rollInterval = 60<br>
a1.sinks.k1.hdfs.rollSize = 10240<br>
a1.sinks.k1.hdfs.idleTimeout = 3<br>
a1.sinks.k1.hdfs.fileType = DataStream<br>
a1.sinks.k1.hdfs.useLocalTimeStamp = true<br>
a1.sinks.k1.hdfs.round = true<br>
a1.sinks.k1.hdfs.roundValue = 5<br>
a1.sinks.k1.hdfs.roundUnit =  minute<br><br><br>
# Use a channel which buffers events in memory<br>
a1.channels.c1.type = memory<br>
a1.channels.c1.capacity = 1000<br>
a1.channels.c1.transactionCapacity = 100<br><br>
# Bind the source and sink to the channel<br>
a1.sources.r1.channels = c1<br>
a1.sinks.k1.channel = c1</span></p>
<p><span class="s"><span class="s">7.spooldir</span> 是一种主动生成日志的方式<br>
之后我们往<span class="s">/opt/flume</span>传入一个文件 发现日志文件在hadoop集群上面主动生成</span></p>
<p><span class="s"><br></span></p>
<p><span class="s">如果遇到此错误</span></p>
<p><span class="s">2017-11-22 21:22:26,047 (pool-3-thread-1) [ERROR - org.apache.flume.source.SpoolDirectorySource$SpoolDirectoryRunnable.run(SpoolDirectorySource.java:280)] FATAL: Spool Directory source r1: { spoolDir: /opt/flume }: Uncaught exception in SpoolDirectorySource
 thread. Restart or reconfigure Flume to continue processing.<br>
java.nio.charset.MalformedInputException: Input length = 1<br>
    at java.nio.charset.CoderResult.throwException(CoderResult.java:281)<br>
    at org.apache.flume.serialization.ResettableFileInputStream.readChar(ResettableFileInputStream.java:283)<br>
    at org.apache.flume.serialization.LineDeserializer.readLine(LineDeserializer.java:132)<br>
    at org.apache.flume.serialization.LineDeserializer.readEvent(LineDeserializer.java:70)<br>
    at org.apache.flume.serialization.LineDeserializer.readEvents(LineDeserializer.java:89)<br>
    at org.apache.flume.client.avro.ReliableSpoolingFileEventReader.readDeserializerEvents(ReliableSpoolingFileEventReader.java:343)<br>
    at org.apache.flume.client.avro.ReliableSpoolingFileEventReader.readEvents(ReliableSpoolingFileEventReader.java:318)<br>
    at org.apache.flume.source.SpoolDirectorySource$SpoolDirectoryRunnable.run(SpoolDirectorySource.java:250)<br>
    at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:511)<br>
    at java.util.concurrent.FutureTask.runAndReset(FutureTask.java:308)<br>
    at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.access$301(ScheduledThreadPoolExecutor.java:180)<br>
    at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.run(ScheduledThreadPoolExecutor.java:294)<br>
    at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)<br>
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)<br>
    at java.lang.Thread.run(Thread.java:748)<br></span></p>
<p><span class="s"></span>是因为你传入的文件有特殊符号，请更改就没错误了，至此单点的flume创建完成。/<br></p>
            </div>
                </div>