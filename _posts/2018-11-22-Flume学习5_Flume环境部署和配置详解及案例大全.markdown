---
layout:     post
title:      Flume学习5_Flume环境部署和配置详解及案例大全
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="white-space:pre"></span>flume是一个分布式、可靠、和高可用的海量日志采集、聚合和传输的系统。支持在日志系统中定制各类数据发送方，用于收集数据;同时，Flume提供对数据进行简单处理，并写到各种数据接受方(比如文本、HDFS、Hbase等)的能力 。</p>
<p></p>
<p>一、什么是Flume?<br>
　　flume 作为 cloudera 开发的实时日志收集系统，受到了业界的认可与广泛应用。Flume 初始的发行版本目前被统称为 Flume OG（original generation），属于 cloudera。但随着 FLume 功能的扩展，Flume OG 代码工程臃肿、核心组件设计不合理、核心配置不标准等缺点暴露出来，尤其是在 Flume OG 的最后一个发行版本 0.94.0 中，日志传输不稳定的现象尤为严重，为了解决这些问题，2011 年 10 月 22 号，cloudera 完成了 Flume-728，对
 Flume 进行了里程碑式的改动：重构核心组件、核心配置以及代码架构，重构后的版本统称为 Flume NG（next generation）；改动的另一原因是将 Flume 纳入 apache 旗下，cloudera Flume 改名为 Apache Flume。<br>
 <br>
flume的特点：<br>
　　flume是一个分布式、可靠、和高可用的海量日志采集、聚合和传输的系统。支持在日志系统中定制各类数据发送方，用于收集数据;同时，Flume提供对数据进行简单处理，并写到各种数据接受方(比如文本、HDFS、Hbase等)的能力 。<br>
　　flume的数据流由事件(Event)贯穿始终。事件是Flume的基本数据单位，它携带日志数据(字节数组形式)并且携带有头信息，这些Event由Agent外部的Source生成，当Source捕获事件后会进行特定的格式化，然后Source会把事件推入(单个或多个)Channel中。你可以把Channel看作是一个缓冲区，它将保存事件直到Sink处理完该事件。Sink负责持久化日志或者把事件推向另一个Source。<br>
 <br>
flume的可靠性 <br>
　　当节点出现故障时，日志能够被传送到其他节点上而不会丢失。Flume提供了三种级别的可靠性保障，从强到弱依次分别为：end-to-end（收到数据agent首先将event写到磁盘上，当数据传送成功后，再删除；如果数据发送失败，可以重新发送。），Store on failure（这也是scribe采用的策略，当数据接收方crash时，将数据写到本地，待恢复后，继续发送），Besteffort（数据发送到接收方后，不会进行确认）。<br>
 <br>
flume的可恢复性：<br>
　　还是靠Channel。推荐使用FileChannel，事件持久化在本地文件系统里(性能较差)。 <br>
 <br>
　　flume的一些核心概念：<br>
Agent使用JVM 运行Flume。每台机器运行一个agent，但是可以在一个agent中包含多个sources和sinks。<br>
Client生产数据，运行在一个独立的线程。<br>
Source从Client收集数据，传递给Channel。<br>
Sink从Channel收集数据，运行在一个独立线程。<br>
Channel连接 sources 和 sinks ，这个有点像一个队列。<br>
Events可以是日志记录、 avro 对象等。<br>
 <br>
　　Flume以agent为最小的独立运行单位。一个agent就是一个JVM。单agent由Source、Sink和Channel三大组件构成。</p>
<p>　　值得注意的是，Flume提供了大量内置的Source、Channel和Sink类型。不同类型的Source,Channel和Sink可以自由组合。组合方式基于用户设置的配置文件，非常灵活。比如：Channel可以把事件暂存在内存里，也可以持久化到本地硬盘上。Sink可以把日志写入HDFS, HBase，甚至是另外一个Source等等。Flume支持用户建立多级流，也就是说，多个agent可以协同工作，并且支持Fan-in、Fan-out、Contextual Routing、Backup Routes，这也正是NB之处。</p>
<p>　　二、flume的官方网站在哪里？<br>
　　<a target="_blank" href="http://flume.apache.org/" rel="nofollow">http://flume.apache.org/</a></p>
<p>　　三、在哪里下载？</p>
<p>　　<a target="_blank" href="http://www.apache.org/dyn/closer.cgi/flume/1.5.0/apache-flume-1.5.0-bin.tar.gz" rel="nofollow">http://www.apache.org/dyn/closer.cgi/flume/1.5.0/apache-flume-1.5.0-bin.tar.gz</a></p>
<p>　　四、如何安装？<br>
　　　　1)将下载的flume包，解压到/home/hadoop目录中，你就已经完成了50%：）简单吧</p>
<p>　　　　2)修改 flume-env.sh 配置文件,主要是JAVA_HOME变量设置</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_873105">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
<div class="line number10 index9 alt1">10</div>
<div class="line number11 index10 alt2">11</div>
<div class="line number12 index11 alt1">12</div>
<div class="line number13 index12 alt2">13</div>
<div class="line number14 index13 alt1">14</div>
<div class="line number15 index14 alt2">15</div>
<div class="line number16 index15 alt1">16</div>
<div class="line number17 index16 alt2">17</div>
<div class="line number18 index17 alt1">18</div>
<div class="line number19 index18 alt2">19</div>
<div class="line number20 index19 alt1">20</div>
<div class="line number21 index20 alt2">21</div>
<div class="line number22 index21 alt1">22</div>
<div class="line number23 index22 alt2">23</div>
<div class="line number24 index23 alt1">24</div>
<div class="line number25 index24 alt2">25</div>
<div class="line number26 index25 alt1">26</div>
<div class="line number27 index26 alt2">27</div>
<div class="line number28 index27 alt1">28</div>
<div class="line number29 index28 alt2">29</div>
<div class="line number30 index29 alt1">30</div>
<div class="line number31 index30 alt2">31</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop/flume-1</code><code class="bash plain">.5.0-bin</code><code class="bash comments"># cp conf/flume-env.sh.template conf/flume-env.sh</code></div>
<div class="line number2 index1 alt1"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop/flume-1</code><code class="bash plain">.5.0-bin</code><code class="bash comments"># vi conf/flume-env.sh</code></div>
<div class="line number3 index2 alt2"><code class="bash comments"># Licensed to the Apache Software Foundation (ASF) under one</code></div>
<div class="line number4 index3 alt1"><code class="bash comments"># or more contributor license agreements. See the NOTICE file</code></div>
<div class="line number5 index4 alt2"><code class="bash comments"># distributed with this work for additional information</code></div>
<div class="line number6 index5 alt1"><code class="bash comments"># regarding copyright ownership. The ASF licenses this file</code></div>
<div class="line number7 index6 alt2"><code class="bash comments"># to you under the Apache License, Version 2.0 (the</code></div>
<div class="line number8 index7 alt1"><code class="bash comments"># "License"); you may not use this file except in compliance</code></div>
<div class="line number9 index8 alt2"><code class="bash comments"># with the License. You may obtain a copy of the License at</code></div>
<div class="line number10 index9 alt1"><code class="bash comments">#</code></div>
<div class="line number11 index10 alt2"><code class="bash comments">#   <a target="_blank" href="http://www.apache.org/licenses/LICENSE-2.0" rel="nofollow">
http://www.apache.org/licenses/LICENSE-2.0</a></code></div>
<div class="line number12 index11 alt1"><code class="bash comments">#</code></div>
<div class="line number13 index12 alt2"><code class="bash comments"># Unless required by applicable law or agreed to in writing, software</code></div>
<div class="line number14 index13 alt1"><code class="bash comments"># distributed under the License is distributed on an "AS IS" BASIS,</code></div>
<div class="line number15 index14 alt2"><code class="bash comments"># WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.</code></div>
<div class="line number16 index15 alt1"><code class="bash comments"># See the License for the specific language governing permissions and</code></div>
<div class="line number17 index16 alt2"><code class="bash comments"># limitations under the License.</code></div>
<div class="line number18 index17 alt1"><code class="bash spaces"> </code> </div>
<div class="line number19 index18 alt2"><code class="bash comments"># If this file is placed at FLUME_CONF_DIR/flume-env.sh, it will be sourced</code></div>
<div class="line number20 index19 alt1"><code class="bash comments"># during Flume startup.</code></div>
<div class="line number21 index20 alt2"><code class="bash spaces"> </code> </div>
<div class="line number22 index21 alt1"><code class="bash comments"># Enviroment variables can be set here.</code></div>
<div class="line number23 index22 alt2"><code class="bash spaces"> </code> </div>
<div class="line number24 index23 alt1"><code class="bash plain">JAVA_HOME=</code><code class="bash plain">/usr/lib/jvm/java-7-oracle</code></div>
<div class="line number25 index24 alt2"><code class="bash spaces"> </code> </div>
<div class="line number26 index25 alt1"><code class="bash comments"># Give Flume more memory and pre-allocate, enable remote monitoring via JMX</code></div>
<div class="line number27 index26 alt2"><code class="bash comments">#JAVA_OPTS="-Xms100m -Xmx200m -Dcom.sun.management.jmxremote"</code></div>
<div class="line number28 index27 alt1"><code class="bash spaces"> </code> </div>
<div class="line number29 index28 alt2"><code class="bash comments"># Note that the Flume conf directory is always included in the classpath.</code></div>
<div class="line number30 index29 alt1"><code class="bash comments">#FLUME_CLASSPATH=""</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　3)验证是否安装成功</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_908919">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># /home/hadoop/flume-1.5.0-bin/bin/flume-ng version</code></div>
<div class="line number2 index1 alt1"><code class="bash plain">Flume 1.5.0</code></div>
<div class="line number3 index2 alt2"><code class="bash plain">Source code repository: https:</code><code class="bash plain">//git-wip-us</code><code class="bash plain">.apache.org</code><code class="bash plain">/repos/asf/flume</code><code class="bash plain">.git</code></div>
<div class="line number4 index3 alt1"><code class="bash plain">Revision: 8633220df808c4cd0c13d1cf0320454a94f1ea97</code></div>
<div class="line number5 index4 alt2"><code class="bash plain">Compiled by hshreedharan on Wed May 7 14:49:18 PDT 2014</code></div>
<div class="line number6 index5 alt1"><code class="bash plain">From </code><code class="bash functions">source</code><code class="bash plain">with checksum a01fe726e4380ba0c9f7a7d222db961f</code></div>
<div class="line number7 index6 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments">#</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　出现上面的信息，表示安装成功了<br>
 <br>
 <br>
　　五、flume的案例<br>
　　　　1)案例1：Avro<br>
　　　　Avro可以发送一个给定的文件给Flume，Avro 源使用AVRO RPC机制。<br>
　　　　　　a)创建agent配置文件</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_39638">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
<div class="line number10 index9 alt1">10</div>
<div class="line number11 index10 alt2">11</div>
<div class="line number12 index11 alt1">12</div>
<div class="line number13 index12 alt2">13</div>
<div class="line number14 index13 alt1">14</div>
<div class="line number15 index14 alt2">15</div>
<div class="line number16 index15 alt1">16</div>
<div class="line number17 index16 alt2">17</div>
<div class="line number18 index17 alt1">18</div>
<div class="line number19 index18 alt2">19</div>
<div class="line number20 index19 alt1">20</div>
<div class="line number21 index20 alt2">21</div>
<div class="line number22 index21 alt1">22</div>
<div class="line number23 index22 alt2">23</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments">#vi /home/hadoop/flume-1.5.0-bin/conf/avro.conf</code></div>
<div class="line number2 index1 alt1"><code class="bash spaces"> </code> </div>
<div class="line number3 index2 alt2"><code class="bash plain">a1.sources = r1</code></div>
<div class="line number4 index3 alt1"><code class="bash plain">a1.sinks = k1</code></div>
<div class="line number5 index4 alt2"><code class="bash plain">a1.channels = c1</code></div>
<div class="line number6 index5 alt1"><code class="bash spaces"> </code> </div>
<div class="line number7 index6 alt2"><code class="bash comments"># Describe/configure the source</code></div>
<div class="line number8 index7 alt1"><code class="bash plain">a1.sources.r1.</code><code class="bash functions">type</code><code class="bash plain">= avro</code></div>
<div class="line number9 index8 alt2"><code class="bash plain">a1.sources.r1.channels = c1</code></div>
<div class="line number10 index9 alt1"><code class="bash plain">a1.sources.r1.bind = 0.0.0.0</code></div>
<div class="line number11 index10 alt2"><code class="bash plain">a1.sources.r1.port = 4141</code></div>
<div class="line number12 index11 alt1"><code class="bash spaces"> </code> </div>
<div class="line number13 index12 alt2"><code class="bash comments"># Describe the sink</code></div>
<div class="line number14 index13 alt1"><code class="bash plain">a1.sinks.k1.</code><code class="bash functions">type</code><code class="bash plain">= logger</code></div>
<div class="line number15 index14 alt2"><code class="bash spaces"> </code> </div>
<div class="line number16 index15 alt1"><code class="bash comments"># Use a channel which buffers events in memory</code></div>
<div class="line number17 index16 alt2"><code class="bash plain">a1.channels.c1.</code><code class="bash functions">type</code><code class="bash plain">= memory</code></div>
<div class="line number18 index17 alt1"><code class="bash plain">a1.channels.c1.capacity = 1000</code></div>
<div class="line number19 index18 alt2"><code class="bash plain">a1.channels.c1.transactionCapacity = 100</code></div>
<div class="line number20 index19 alt1"><code class="bash spaces"> </code> </div>
<div class="line number21 index20 alt2"><code class="bash comments"># Bind the source and sink to the channel</code></div>
<div class="line number22 index21 alt1"><code class="bash plain">a1.sources.r1.channels = c1</code></div>
<div class="line number23 index22 alt2"><code class="bash plain">a1.sinks.k1.channel = c1</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　b)启动flume agent a1</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_592455">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># /home/hadoop/flume-1.5.0-bin/bin/flume-ng agent -c . -f /home/hadoop/flume-1.5.0-bin/conf/avro.conf -n a1
 -Dflume.root.logger=INFO,console</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　c)创建指定文件</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_697291">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># echo "hello world" &gt; /home/hadoop/flume-1.5.0-bin/log.00</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　d)使用avro-client发送文件</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_504495">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># /home/hadoop/flume-1.5.0-bin/bin/flume-ng avro-client -c . -H m1 -p 4141 -F /home/hadoop/flume-1.5.0-bin/log.00</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　f)在m1的控制台，可以看到以下信息，注意最后一行：</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_965234">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
<div class="line number10 index9 alt1">10</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop/flume-1</code><code class="bash plain">.5.0-bin</code><code class="bash plain">/conf</code><code class="bash comments"># /home/hadoop/flume-1.5.0-bin/bin/flume-ng
 agent -c . -f /home/hadoop/flume-1.5.0-bin/conf/avro.conf -n a1 -Dflume.root.logger=INFO,console</code></div>
<div class="line number2 index1 alt1"><code class="bash plain">Info: Sourcing environment configuration script</code><code class="bash plain">/home/hadoop/flume-1</code><code class="bash plain">.5.0-bin</code><code class="bash plain">/conf/flume-env</code><code class="bash plain">.sh</code></div>
<div class="line number3 index2 alt2"><code class="bash plain">Info: Including Hadoop libraries found via (</code><code class="bash plain">/home/hadoop/hadoop-2</code><code class="bash plain">.2.0</code><code class="bash plain">/bin/hadoop</code><code class="bash plain">)</code><code class="bash keyword">for</code><code class="bash plain">HDFS
 access</code></div>
<div class="line number4 index3 alt1"><code class="bash plain">Info: Excluding </code>
<code class="bash plain">/home/hadoop/hadoop-2</code><code class="bash plain">.2.0</code><code class="bash plain">/share/hadoop/common/lib/slf4j-api-1</code><code class="bash plain">.7.5.jar from classpath</code></div>
<div class="line number5 index4 alt2"><code class="bash plain">Info: Excluding </code>
<code class="bash plain">/home/hadoop/hadoop-2</code><code class="bash plain">.2.0</code><code class="bash plain">/share/hadoop/common/lib/slf4j-log4j12-1</code><code class="bash plain">.7.5.jar from classpath</code></div>
<div class="line number6 index5 alt1"><code class="bash plain">...</code></div>
<div class="line number7 index6 alt2"><code class="bash plain">-08-10 10:43:25,112 (New I</code><code class="bash plain">/O</code><code class="bash plain">worker</code><code class="bash comments">#1) [INFO - org.apache.avro.ipc.NettyServer$NettyServerAvroHandler.handleUpstream(NettyServer.java:171)]
 [id: 0x92464c4f, /192.168.1.50:59850 :&gt; /192.168.1.50:4141] UNBOUND</code></div>
<div class="line number8 index7 alt1"><code class="bash plain">-08-10 10:43:25,112 (New I</code><code class="bash plain">/O</code><code class="bash plain">worker</code><code class="bash comments">#1) [INFO - org.apache.avro.ipc.NettyServer$NettyServerAvroHandler.handleUpstream(NettyServer.java:171)]
 [id: 0x92464c4f, /192.168.1.50:59850 :&gt; /192.168.1.50:4141] CLOSED</code></div>
<div class="line number9 index8 alt2"><code class="bash plain">-08-10 10:43:25,112 (New I</code><code class="bash plain">/O</code><code class="bash plain">worker</code><code class="bash comments">#1) [INFO - org.apache.avro.ipc.NettyServer$NettyServerAvroHandler.channelClosed(NettyServer.java:209)]
 Connection to /192.168.1.50:59850 disconnected.</code></div>
<div class="line number10 index9 alt1"><code class="bash plain">-08-10 10:43:26,718 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 68 65 6C 6C 6F 20 77 6F 72 6C
 64        hello world }</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　2)案例2：Spool<br>
　　　　Spool监测配置的目录下新增的文件，并将文件中的数据读取出来。需要注意两点：<br>
　　　　1) 拷贝到spool目录下的文件不可以再打开编辑。<br>
　　　　2) spool目录下不可包含相应的子目录<br>
　　　　　　a)创建agent配置文件<br>
</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_492934">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
<div class="line number10 index9 alt1">10</div>
<div class="line number11 index10 alt2">11</div>
<div class="line number12 index11 alt1">12</div>
<div class="line number13 index12 alt2">13</div>
<div class="line number14 index13 alt1">14</div>
<div class="line number15 index14 alt2">15</div>
<div class="line number16 index15 alt1">16</div>
<div class="line number17 index16 alt2">17</div>
<div class="line number18 index17 alt1">18</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># vi /home/hadoop/flume-1.5.0-bin/conf/spool.conf</code></div>
<div class="line number2 index1 alt1"><code class="bash plain">a1.sources = r1</code></div>
<div class="line number3 index2 alt2"><code class="bash plain">a1.sinks = k1</code></div>
<div class="line number4 index3 alt1"><code class="bash plain">a1.channels = c1</code></div>
<div class="line number5 index4 alt2"><code class="bash comments"># Describe/configure the source</code></div>
<div class="line number6 index5 alt1"><code class="bash plain">a1.sources.r1.</code><code class="bash functions">type</code><code class="bash plain">= spooldir</code></div>
<div class="line number7 index6 alt2"><code class="bash plain">a1.sources.r1.channels = c1</code></div>
<div class="line number8 index7 alt1"><code class="bash plain">a1.sources.r1.spoolDir =</code><code class="bash plain">/home/hadoop/flume-1</code><code class="bash plain">.5.0-bin</code><code class="bash plain">/logs</code></div>
<div class="line number9 index8 alt2"><code class="bash plain">a1.sources.r1.fileHeader =</code><code class="bash functions">true</code></div>
<div class="line number10 index9 alt1"><code class="bash comments"># Describe the sink</code></div>
<div class="line number11 index10 alt2"><code class="bash plain">a1.sinks.k1.</code><code class="bash functions">type</code><code class="bash plain">= logger</code></div>
<div class="line number12 index11 alt1"><code class="bash comments"># Use a channel which buffers events in memory</code></div>
<div class="line number13 index12 alt2"><code class="bash plain">a1.channels.c1.</code><code class="bash functions">type</code><code class="bash plain">= memory</code></div>
<div class="line number14 index13 alt1"><code class="bash plain">a1.channels.c1.capacity = 1000</code></div>
<div class="line number15 index14 alt2"><code class="bash plain">a1.channels.c1.transactionCapacity = 100</code></div>
<div class="line number16 index15 alt1"><code class="bash comments"># Bind the source and sink to the channel</code></div>
<div class="line number17 index16 alt2"><code class="bash plain">a1.sources.r1.channels = c1</code></div>
<div class="line number18 index17 alt1"><code class="bash plain">a1.sinks.k1.channel = c1</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　b)启动flume agent a1<br>
</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_450022">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># /home/hadoop/flume-1.5.0-bin/bin/flume-ng agent -c . -f /home/hadoop/flume-1.5.0-bin/conf/spool.conf -n a1
 -Dflume.root.logger=INFO,console</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　c)追加文件到/home/hadoop/flume-1.5.0-bin/logs目录<br>
</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_999619">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># echo "spool test1" &gt; /home/hadoop/flume-1.5.0-bin/logs/spool_text.log</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　d)在m1的控制台，可以看到以下相关信息：<br>
</p>
<div class="jb51code">
<div class="syntaxhighlighter  plain" id="highlighter_170556">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
<div class="line number10 index9 alt1">10</div>
<div class="line number11 index10 alt2">11</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="plain plain">/08/10 11:37:13 INFO source.SpoolDirectorySource: Spooling Directory Source runner has shutdown.</code></div>
<div class="line number2 index1 alt1"><code class="plain plain">/08/10 11:37:13 INFO source.SpoolDirectorySource: Spooling Directory Source runner has shutdown.</code></div>
<div class="line number3 index2 alt2"><code class="plain plain">/08/10 11:37:14 INFO avro.ReliableSpoolingFileEventReader: Preparing to move file /home/hadoop/flume-1.5.0-bin/logs/spool_text.log to /home/hadoop/flume-1.5.0-bin/logs/spool_text.log.COMPLETED</code></div>
<div class="line number4 index3 alt1"><code class="plain plain">/08/10 11:37:14 INFO source.SpoolDirectorySource: Spooling Directory Source runner has shutdown.</code></div>
<div class="line number5 index4 alt2"><code class="plain plain">/08/10 11:37:14 INFO source.SpoolDirectorySource: Spooling Directory Source runner has shutdown.</code></div>
<div class="line number6 index5 alt1"><code class="plain plain">/08/10 11:37:14 INFO sink.LoggerSink: Event: { headers:{file=/home/hadoop/flume-1.5.0-bin/logs/spool_text.log} body: 73 70 6F 6F 6C 20 74 65 73 74 31        spool test1 }</code></div>
<div class="line number7 index6 alt2"><code class="plain plain">/08/10 11:37:15 INFO source.SpoolDirectorySource: Spooling Directory Source runner has shutdown.</code></div>
<div class="line number8 index7 alt1"><code class="plain plain">/08/10 11:37:15 INFO source.SpoolDirectorySource: Spooling Directory Source runner has shutdown.</code></div>
<div class="line number9 index8 alt2"><code class="plain plain">/08/10 11:37:16 INFO source.SpoolDirectorySource: Spooling Directory Source runner has shutdown.</code></div>
<div class="line number10 index9 alt1"><code class="plain plain">/08/10 11:37:16 INFO source.SpoolDirectorySource: Spooling Directory Source runner has shutdown.</code></div>
<div class="line number11 index10 alt2"><code class="plain plain">/08/10 11:37:17 INFO source.SpoolDirectorySource: Spooling Directory Source runner has shutdown.</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　3)案例3：Exec<br>
　　　　EXEC执行一个给定的命令获得输出的源,如果要使用tail命令，必选使得file足够大才能看到输出内容<br>
　　　　　　a)创建agent配置文件<br>
</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_852184">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
<div class="line number10 index9 alt1">10</div>
<div class="line number11 index10 alt2">11</div>
<div class="line number12 index11 alt1">12</div>
<div class="line number13 index12 alt2">13</div>
<div class="line number14 index13 alt1">14</div>
<div class="line number15 index14 alt2">15</div>
<div class="line number16 index15 alt1">16</div>
<div class="line number17 index16 alt2">17</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># vi /home/hadoop/flume-1.5.0-bin/conf/exec_tail.conf</code></div>
<div class="line number2 index1 alt1"><code class="bash plain">a1.sources = r1</code></div>
<div class="line number3 index2 alt2"><code class="bash plain">a1.sinks = k1</code></div>
<div class="line number4 index3 alt1"><code class="bash plain">a1.channels = c1</code></div>
<div class="line number5 index4 alt2"><code class="bash comments"># Describe/configure the source</code></div>
<div class="line number6 index5 alt1"><code class="bash plain">a1.sources.r1.</code><code class="bash functions">type</code><code class="bash plain">=</code><code class="bash functions">exec</code></div>
<div class="line number7 index6 alt2"><code class="bash plain">a1.sources.r1.channels = c1</code></div>
<div class="line number8 index7 alt1"><code class="bash plain">a1.sources.r1.</code><code class="bash functions">command</code><code class="bash plain">=</code><code class="bash functions">tail</code><code class="bash plain">-F
</code><code class="bash plain">/home/hadoop/flume-1</code><code class="bash plain">.5.0-bin</code><code class="bash plain">/log_exec_tail</code></div>
<div class="line number9 index8 alt2"><code class="bash comments"># Describe the sink</code></div>
<div class="line number10 index9 alt1"><code class="bash plain">a1.sinks.k1.</code><code class="bash functions">type</code><code class="bash plain">= logger</code></div>
<div class="line number11 index10 alt2"><code class="bash comments"># Use a channel which buffers events in memory</code></div>
<div class="line number12 index11 alt1"><code class="bash plain">a1.channels.c1.</code><code class="bash functions">type</code><code class="bash plain">= memory</code></div>
<div class="line number13 index12 alt2"><code class="bash plain">a1.channels.c1.capacity = 1000</code></div>
<div class="line number14 index13 alt1"><code class="bash plain">a1.channels.c1.transactionCapacity = 100</code></div>
<div class="line number15 index14 alt2"><code class="bash comments"># Bind the source and sink to the channel</code></div>
<div class="line number16 index15 alt1"><code class="bash plain">a1.sources.r1.channels = c1</code></div>
<div class="line number17 index16 alt2"><code class="bash plain">a1.sinks.k1.channel = c1</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　b)启动flume agent a1<br>
</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_996217">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># /home/hadoop/flume-1.5.0-bin/bin/flume-ng agent -c . -f /home/hadoop/flume-1.5.0-bin/conf/exec_tail.conf
 -n a1 -Dflume.root.logger=INFO,console</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　c)生成足够多的内容在文件里<br>
</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_368131">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># for i in {1..100};do echo "exec tail$i" &gt;&gt; /home/hadoop/flume-1.5.0-bin/log_exec_tail;echo $i;sleep 0.1;done</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　e)在m1的控制台，可以看到以下信息：<br>
</p>
<div class="jb51code">
<div class="syntaxhighlighter  plain" id="highlighter_682769">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
<div class="line number10 index9 alt1">10</div>
<div class="line number11 index10 alt2">11</div>
<div class="line number12 index11 alt1">12</div>
<div class="line number13 index12 alt2">13</div>
<div class="line number14 index13 alt1">14</div>
<div class="line number15 index14 alt2">15</div>
<div class="line number16 index15 alt1">16</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="plain plain">-08-10 10:59:25,513 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 65 78 65 63 20 74 61 69 6C 20
 74 65 73 74    exec tail test }</code></div>
<div class="line number2 index1 alt1"><code class="plain plain">-08-10 10:59:34,535 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 65 78 65 63 20 74 61 69 6C 20
 74 65 73 74    exec tail test }</code></div>
<div class="line number3 index2 alt2"><code class="plain plain">-08-10 11:01:40,557 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 65 78 65 63 20 74 61 69 6C 31         
 exec tail1 }</code></div>
<div class="line number4 index3 alt1"><code class="plain plain">-08-10 11:01:41,180 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 65 78 65 63 20 74 61 69 6C 32         
 exec tail2 }</code></div>
<div class="line number5 index4 alt2"><code class="plain plain">-08-10 11:01:41,180 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 65 78 65 63 20 74 61 69 6C 33         
 exec tail3 }</code></div>
<div class="line number6 index5 alt1"><code class="plain plain">-08-10 11:01:41,181 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 65 78 65 63 20 74 61 69 6C 34         
 exec tail4 }</code></div>
<div class="line number7 index6 alt2"><code class="plain plain">-08-10 11:01:41,181 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 65 78 65 63 20 74 61 69 6C 35         
 exec tail5 }</code></div>
<div class="line number8 index7 alt1"><code class="plain plain">-08-10 11:01:41,181 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 65 78 65 63 20 74 61 69 6C 36         
 exec tail6 }</code></div>
<div class="line number9 index8 alt2"><code class="plain plain">....</code></div>
<div class="line number10 index9 alt1"><code class="plain plain">....</code></div>
<div class="line number11 index10 alt2"><code class="plain plain">....</code></div>
<div class="line number12 index11 alt1"><code class="plain plain">-08-10 11:01:51,550 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 65 78 65 63 20 74 61 69 6C
 39 36        exec tail96 }</code></div>
<div class="line number13 index12 alt2"><code class="plain plain">-08-10 11:01:51,550 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 65 78 65 63 20 74 61 69 6C
 39 37        exec tail97 }</code></div>
<div class="line number14 index13 alt1"><code class="plain plain">-08-10 11:01:51,551 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 65 78 65 63 20 74 61 69 6C
 39 38        exec tail98 }</code></div>
<div class="line number15 index14 alt2"><code class="plain plain">-08-10 11:01:51,551 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 65 78 65 63 20 74 61 69 6C
 39 39        exec tail99 }</code></div>
<div class="line number16 index15 alt1"><code class="plain plain">-08-10 11:01:51,551 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:70)] Event: { headers:{} body: 65 78 65 63 20 74 61 69 6C
 31 30 30       exec tail100 }</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　4)案例4：Syslogtcp<br>
　　　　Syslogtcp监听TCP的端口做为数据源<br>
　　　　　　a)创建agent配置文件<br>
</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_744701">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
<div class="line number10 index9 alt1">10</div>
<div class="line number11 index10 alt2">11</div>
<div class="line number12 index11 alt1">12</div>
<div class="line number13 index12 alt2">13</div>
<div class="line number14 index13 alt1">14</div>
<div class="line number15 index14 alt2">15</div>
<div class="line number16 index15 alt1">16</div>
<div class="line number17 index16 alt2">17</div>
<div class="line number18 index17 alt1">18</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># vi /home/hadoop/flume-1.5.0-bin/conf/syslog_tcp.conf</code></div>
<div class="line number2 index1 alt1"><code class="bash plain">a1.sources = r1</code></div>
<div class="line number3 index2 alt2"><code class="bash plain">a1.sinks = k1</code></div>
<div class="line number4 index3 alt1"><code class="bash plain">a1.channels = c1</code></div>
<div class="line number5 index4 alt2"><code class="bash comments"># Describe/configure the source</code></div>
<div class="line number6 index5 alt1"><code class="bash plain">a1.sources.r1.</code><code class="bash functions">type</code><code class="bash plain">= syslogtcp</code></div>
<div class="line number7 index6 alt2"><code class="bash plain">a1.sources.r1.port = 5140</code></div>
<div class="line number8 index7 alt1"><code class="bash plain">a1.sources.r1.host = localhost</code></div>
<div class="line number9 index8 alt2"><code class="bash plain">a1.sources.r1.channels = c1</code></div>
<div class="line number10 index9 alt1"><code class="bash comments"># Describe the sink</code></div>
<div class="line number11 index10 alt2"><code class="bash plain">a1.sinks.k1.</code><code class="bash functions">type</code><code class="bash plain">= logger</code></div>
<div class="line number12 index11 alt1"><code class="bash comments"># Use a channel which buffers events in memory</code></div>
<div class="line number13 index12 alt2"><code class="bash plain">a1.channels.c1.</code><code class="bash functions">type</code><code class="bash plain">= memory</code></div>
<div class="line number14 index13 alt1"><code class="bash plain">a1.channels.c1.capacity = 1000</code></div>
<div class="line number15 index14 alt2"><code class="bash plain">a1.channels.c1.transactionCapacity = 100</code></div>
<div class="line number16 index15 alt1"><code class="bash comments"># Bind the source and sink to the channel</code></div>
<div class="line number17 index16 alt2"><code class="bash plain">a1.sources.r1.channels = c1</code></div>
<div class="line number18 index17 alt1"><code class="bash plain">a1.sinks.k1.channel = c1</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　b)启动flume agent a1<br>
</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_486509">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># /home/hadoop/flume-1.5.0-bin/bin/flume-ng agent -c . -f /home/hadoop/flume-1.5.0-bin/conf/syslog_tcp.conf
 -n a1 -Dflume.root.logger=INFO,console</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　c)测试产生syslog<br>
</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_181831">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># echo "hello idoall.org syslog" | nc localhost 5140</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　d)在m1的控制台，可以看到以下信息：<br>
</p>
<div class="jb51code">
<div class="syntaxhighlighter  plain" id="highlighter_944717">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
<div class="line number10 index9 alt1">10</div>
<div class="line number11 index10 alt2">11</div>
<div class="line number12 index11 alt1">12</div>
<div class="line number13 index12 alt2">13</div>
<div class="line number14 index13 alt1">14</div>
<div class="line number15 index14 alt2">15</div>
<div class="line number16 index15 alt1">16</div>
<div class="line number17 index16 alt2">17</div>
<div class="line number18 index17 alt1">18</div>
<div class="line number19 index18 alt2">19</div>
<div class="line number20 index19 alt1">20</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="plain plain">/08/10 11:41:45 INFO node.PollingPropertiesFileConfigurationProvider: Reloading configuration file:/home/hadoop/flume-1.5.0-bin/conf/syslog_tcp.conf</code></div>
<div class="line number2 index1 alt1"><code class="plain plain">/08/10 11:41:45 INFO conf.FlumeConfiguration: Added sinks: k1 Agent: a1</code></div>
<div class="line number3 index2 alt2"><code class="plain plain">/08/10 11:41:45 INFO conf.FlumeConfiguration: Processing:k1</code></div>
<div class="line number4 index3 alt1"><code class="plain plain">/08/10 11:41:45 INFO conf.FlumeConfiguration: Processing:k1</code></div>
<div class="line number5 index4 alt2"><code class="plain plain">/08/10 11:41:45 INFO conf.FlumeConfiguration: Post-validation flume configuration contains configuration for agents: [a1]</code></div>
<div class="line number6 index5 alt1"><code class="plain plain">/08/10 11:41:45 INFO node.AbstractConfigurationProvider: Creating channels</code></div>
<div class="line number7 index6 alt2"><code class="plain plain">/08/10 11:41:45 INFO channel.DefaultChannelFactory: Creating instance of channel c1 type memory</code></div>
<div class="line number8 index7 alt1"><code class="plain plain">/08/10 11:41:45 INFO node.AbstractConfigurationProvider: Created channel c1</code></div>
<div class="line number9 index8 alt2"><code class="plain plain">/08/10 11:41:45 INFO source.DefaultSourceFactory: Creating instance of source r1, type syslogtcp</code></div>
<div class="line number10 index9 alt1"><code class="plain plain">/08/10 11:41:45 INFO sink.DefaultSinkFactory: Creating instance of sink: k1, type: logger</code></div>
<div class="line number11 index10 alt2"><code class="plain plain">/08/10 11:41:45 INFO node.AbstractConfigurationProvider: Channel c1 connected to [r1, k1]</code></div>
<div class="line number12 index11 alt1"><code class="plain plain">/08/10 11:41:45 INFO node.Application: Starting new configuration:{ sourceRunners:{r1=EventDrivenSourceRunner: { source:org.apache.flume.source.SyslogTcpSource{name:r1,state:IDLE} }} sinkRunners:{k1=SinkRunner:
 { policy:org.apache.flume.sink.DefaultSinkProcessor@6538b14 counterGroup:{ name:null counters:{} } }} channels:{c1=org.apache.flume.channel.MemoryChannel{name: c1}} }</code></div>
<div class="line number13 index12 alt2"><code class="plain plain">/08/10 11:41:45 INFO node.Application: Starting Channel c1</code></div>
<div class="line number14 index13 alt1"><code class="plain plain">/08/10 11:41:45 INFO instrumentation.MonitoredCounterGroup: Monitored counter group for type: CHANNEL, name: c1: Successfully registered new MBean.</code></div>
<div class="line number15 index14 alt2"><code class="plain plain">/08/10 11:41:45 INFO instrumentation.MonitoredCounterGroup: Component type: CHANNEL, name: c1 started</code></div>
<div class="line number16 index15 alt1"><code class="plain plain">/08/10 11:41:45 INFO node.Application: Starting Sink k1</code></div>
<div class="line number17 index16 alt2"><code class="plain plain">/08/10 11:41:45 INFO node.Application: Starting Source r1</code></div>
<div class="line number18 index17 alt1"><code class="plain plain">/08/10 11:41:45 INFO source.SyslogTcpSource: Syslog TCP Source starting...</code></div>
<div class="line number19 index18 alt2"><code class="plain plain">/08/10 11:42:15 WARN source.SyslogUtils: Event created from Invalid Syslog data.</code></div>
<div class="line number20 index19 alt1"><code class="plain plain">/08/10 11:42:15 INFO sink.LoggerSink: Event: { headers:{Severity=0, flume.syslog.status=Invalid, Facility=0} body: 68 65 6C 6C 6F 20 69 64 6F 61 6C 6C 2E 6F 72 67 hello idoall.org }</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　5)案例5：JSONHandler<br>
　　　　　　a)创建agent配置文件<br>
</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_422295">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
<div class="line number10 index9 alt1">10</div>
<div class="line number11 index10 alt2">11</div>
<div class="line number12 index11 alt1">12</div>
<div class="line number13 index12 alt2">13</div>
<div class="line number14 index13 alt1">14</div>
<div class="line number15 index14 alt2">15</div>
<div class="line number16 index15 alt1">16</div>
<div class="line number17 index16 alt2">17</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># vi /home/hadoop/flume-1.5.0-bin/conf/post_json.conf</code></div>
<div class="line number2 index1 alt1"><code class="bash plain">a1.sources = r1</code></div>
<div class="line number3 index2 alt2"><code class="bash plain">a1.sinks = k1</code></div>
<div class="line number4 index3 alt1"><code class="bash plain">a1.channels = c1</code></div>
<div class="line number5 index4 alt2"><code class="bash comments"># Describe/configure the source</code></div>
<div class="line number6 index5 alt1"><code class="bash plain">a1.sources.r1.</code><code class="bash functions">type</code><code class="bash plain">= org.apache.flume.</code><code class="bash functions">source</code><code class="bash plain">.http.HTTPSource</code></div>
<div class="line number7 index6 alt2"><code class="bash plain">a1.sources.r1.port = 8888</code></div>
<div class="line number8 index7 alt1"><code class="bash plain">a1.sources.r1.channels = c1</code></div>
<div class="line number9 index8 alt2"><code class="bash comments"># Describe the sink</code></div>
<div class="line number10 index9 alt1"><code class="bash plain">a1.sinks.k1.</code><code class="bash functions">type</code><code class="bash plain">= logger</code></div>
<div class="line number11 index10 alt2"><code class="bash comments"># Use a channel which buffers events in memory</code></div>
<div class="line number12 index11 alt1"><code class="bash plain">a1.channels.c1.</code><code class="bash functions">type</code><code class="bash plain">= memory</code></div>
<div class="line number13 index12 alt2"><code class="bash plain">a1.channels.c1.capacity = 1000</code></div>
<div class="line number14 index13 alt1"><code class="bash plain">a1.channels.c1.transactionCapacity = 100</code></div>
<div class="line number15 index14 alt2"><code class="bash comments"># Bind the source and sink to the channel</code></div>
<div class="line number16 index15 alt1"><code class="bash plain">a1.sources.r1.channels = c1</code></div>
<div class="line number17 index16 alt2"><code class="bash plain">a1.sinks.k1.channel = c1</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　b)启动flume agent a1<br>
</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_414365">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># /home/hadoop/flume-1.5.0-bin/bin/flume-ng agent -c . -f /home/hadoop/flume-1.5.0-bin/conf/post_json.conf
 -n a1 -Dflume.root.logger=INFO,console</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　c)生成JSON 格式的POST request<br>
</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_772775">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># curl -X POST -d '[{ "headers" :{"a" : "a1","b" : "b1"},"body" : "idoall.org_body"}]'<a target="_blank" href="http://localhost:8888/" rel="nofollow">http://localhost:8888</a></code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　d)在m1的控制台，可以看到以下信息：<br>
/</p>
<div class="jb51code">
<div class="syntaxhighlighter  plain" id="highlighter_748938">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
<div class="line number10 index9 alt1">10</div>
<div class="line number11 index10 alt2">11</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="plain plain">08/10 11:49:59 INFO node.Application: Starting Channel c1</code></div>
<div class="line number2 index1 alt1"><code class="plain plain">/08/10 11:49:59 INFO instrumentation.MonitoredCounterGroup: Monitored counter group for type: CHANNEL, name: c1: Successfully registered new MBean.</code></div>
<div class="line number3 index2 alt2"><code class="plain plain">/08/10 11:49:59 INFO instrumentation.MonitoredCounterGroup: Component type: CHANNEL, name: c1 started</code></div>
<div class="line number4 index3 alt1"><code class="plain plain">/08/10 11:49:59 INFO node.Application: Starting Sink k1</code></div>
<div class="line number5 index4 alt2"><code class="plain plain">/08/10 11:49:59 INFO node.Application: Starting Source r1</code></div>
<div class="line number6 index5 alt1"><code class="plain plain">/08/10 11:49:59 INFO mortbay.log: Logging to org.slf4j.impl.Log4jLoggerAdapter(org.mortbay.log) via org.mortbay.log.Slf4jLog</code></div>
<div class="line number7 index6 alt2"><code class="plain plain">/08/10 11:49:59 INFO mortbay.log: jetty-6.1.26</code></div>
<div class="line number8 index7 alt1"><code class="plain plain">/08/10 11:50:00 INFO mortbay.log: Started SelectChannelConnector@0.0.0.0:8888</code></div>
<div class="line number9 index8 alt2"><code class="plain plain">/08/10 11:50:00 INFO instrumentation.MonitoredCounterGroup: Monitored counter group for type: SOURCE, name: r1: Successfully registered new MBean.</code></div>
<div class="line number10 index9 alt1"><code class="plain plain">/08/10 11:50:00 INFO instrumentation.MonitoredCounterGroup: Component type: SOURCE, name: r1 started</code></div>
<div class="line number11 index10 alt2"><code class="plain plain">/08/10 12:14:32 INFO sink.LoggerSink: Event: { headers:{b=b1, a=a1} body: 69 64 6F 61 6C 6C 2E 6F 72 67 5F 62 6F 64 79  idoall.org_body }</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　6)案例6：Hadoop sink<br>
　　　　其中关于hadoop2.2.0部分的安装部署，请参考文章《ubuntu12.04+hadoop2.2.0+zookeeper3.4.5+hbase0.96.2+hive0.13.1分布式环境部署》<br>
　　　　　　a)创建agent配置文件<br>
</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_400145">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
<div class="line number10 index9 alt1">10</div>
<div class="line number11 index10 alt2">11</div>
<div class="line number12 index11 alt1">12</div>
<div class="line number13 index12 alt2">13</div>
<div class="line number14 index13 alt1">14</div>
<div class="line number15 index14 alt2">15</div>
<div class="line number16 index15 alt1">16</div>
<div class="line number17 index16 alt2">17</div>
<div class="line number18 index17 alt1">18</div>
<div class="line number19 index18 alt2">19</div>
<div class="line number20 index19 alt1">20</div>
<div class="line number21 index20 alt2">21</div>
<div class="line number22 index21 alt1">22</div>
<div class="line number23 index22 alt2">23</div>
<div class="line number24 index23 alt1">24</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># vi /home/hadoop/flume-1.5.0-bin/conf/hdfs_sink.conf</code></div>
<div class="line number2 index1 alt1"><code class="bash plain">a1.sources = r1</code></div>
<div class="line number3 index2 alt2"><code class="bash plain">a1.sinks = k1</code></div>
<div class="line number4 index3 alt1"><code class="bash plain">a1.channels = c1</code></div>
<div class="line number5 index4 alt2"><code class="bash comments"># Describe/configure the source</code></div>
<div class="line number6 index5 alt1"><code class="bash plain">a1.sources.r1.</code><code class="bash functions">type</code><code class="bash plain">= syslogtcp</code></div>
<div class="line number7 index6 alt2"><code class="bash plain">a1.sources.r1.port = 5140</code></div>
<div class="line number8 index7 alt1"><code class="bash plain">a1.sources.r1.host = localhost</code></div>
<div class="line number9 index8 alt2"><code class="bash plain">a1.sources.r1.channels = c1</code></div>
<div class="line number10 index9 alt1"><code class="bash comments"># Describe the sink</code></div>
<div class="line number11 index10 alt2"><code class="bash plain">a1.sinks.k1.</code><code class="bash functions">type</code><code class="bash plain">= hdfs</code></div>
<div class="line number12 index11 alt1"><code class="bash plain">a1.sinks.k1.channel = c1</code></div>
<div class="line number13 index12 alt2"><code class="bash plain">a1.sinks.k1.hdfs.path = hdfs:</code><code class="bash plain">//m1</code><code class="bash plain">:9000</code><code class="bash plain">/user/flume/syslogtcp</code></div>
<div class="line number14 index13 alt1"><code class="bash plain">a1.sinks.k1.hdfs.filePrefix = Syslog</code></div>
<div class="line number15 index14 alt2"><code class="bash plain">a1.sinks.k1.hdfs.round =</code><code class="bash functions">true</code></div>
<div class="line number16 index15 alt1"><code class="bash plain">a1.sinks.k1.hdfs.roundValue = 10</code></div>
<div class="line number17 index16 alt2"><code class="bash plain">a1.sinks.k1.hdfs.roundUnit = minute</code></div>
<div class="line number18 index17 alt1"><code class="bash comments"># Use a channel which buffers events in memory</code></div>
<div class="line number19 index18 alt2"><code class="bash plain">a1.channels.c1.</code><code class="bash functions">type</code><code class="bash plain">= memory</code></div>
<div class="line number20 index19 alt1"><code class="bash plain">a1.channels.c1.capacity = 1000</code></div>
<div class="line number21 index20 alt2"><code class="bash plain">a1.channels.c1.transactionCapacity = 100</code></div>
<div class="line number22 index21 alt1"><code class="bash comments"># Bind the source and sink to the channel</code></div>
<div class="line number23 index22 alt2"><code class="bash plain">a1.sources.r1.channels = c1</code></div>
<div class="line number24 index23 alt1"><code class="bash plain">a1.sinks.k1.channel = c1</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　b)启动flume agent a1<br>
</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_405914">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># /home/hadoop/flume-1.5.0-bin/bin/flume-ng agent -c . -f /home/hadoop/flume-1.5.0-bin/conf/hdfs_sink.conf
 -n a1 -Dflume.root.logger=INFO,console</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　c)测试产生syslog<br>
</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_64854">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># echo "hello idoall flume -&gt; hadoop testing one" | nc localhost 5140</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　d)在m1的控制台，可以看到以下信息：<br>
</p>
<div class="jb51code">
<div class="syntaxhighlighter  plain" id="highlighter_858935">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
<div class="line number10 index9 alt1">10</div>
<div class="line number11 index10 alt2">11</div>
<div class="line number12 index11 alt1">12</div>
<div class="line number13 index12 alt2">13</div>
<div class="line number14 index13 alt1">14</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="plain plain">/08/10 12:20:39 INFO instrumentation.MonitoredCounterGroup: Monitored counter group for type: CHANNEL, name: c1: Successfully registered new MBean.</code></div>
<div class="line number2 index1 alt1"><code class="plain plain">/08/10 12:20:39 INFO instrumentation.MonitoredCounterGroup: Component type: CHANNEL, name: c1 started</code></div>
<div class="line number3 index2 alt2"><code class="plain plain">/08/10 12:20:39 INFO node.Application: Starting Sink k1</code></div>
<div class="line number4 index3 alt1"><code class="plain plain">/08/10 12:20:39 INFO node.Application: Starting Source r1</code></div>
<div class="line number5 index4 alt2"><code class="plain plain">/08/10 12:20:39 INFO instrumentation.MonitoredCounterGroup: Monitored counter group for type: SINK, name: k1: Successfully registered new MBean.</code></div>
<div class="line number6 index5 alt1"><code class="plain plain">/08/10 12:20:39 INFO instrumentation.MonitoredCounterGroup: Component type: SINK, name: k1 started</code></div>
<div class="line number7 index6 alt2"><code class="plain plain">/08/10 12:20:39 INFO source.SyslogTcpSource: Syslog TCP Source starting...</code></div>
<div class="line number8 index7 alt1"><code class="plain plain">/08/10 12:21:46 WARN source.SyslogUtils: Event created from Invalid Syslog data.</code></div>
<div class="line number9 index8 alt2"><code class="plain plain">/08/10 12:21:49 INFO hdfs.HDFSSequenceFile: writeFormat = Writable, UseRawLocalFileSystem = false</code></div>
<div class="line number10 index9 alt1"><code class="plain plain">/08/10 12:21:49 INFO hdfs.BucketWriter: Creating<a target="_blank" href="" rel="nofollow">hdfs://m1:9000/user/flume/syslogtcp//Syslog.1407644509504.tmp</a></code></div>
<div class="line number11 index10 alt2"><code class="plain plain">/08/10 12:22:20 INFO hdfs.BucketWriter: Closing<a target="_blank" href="" rel="nofollow">hdfs://m1:9000/user/flume/syslogtcp//Syslog.1407644509504.tmp</a></code></div>
<div class="line number12 index11 alt1"><code class="plain plain">/08/10 12:22:20 INFO hdfs.BucketWriter: Close tries incremented</code></div>
<div class="line number13 index12 alt2"><code class="plain plain">/08/10 12:22:20 INFO hdfs.BucketWriter: Renaming<a target="_blank" href="" rel="nofollow">hdfs://m1:9000/user/flume/syslogtcp/Syslog.1407644509504.tmp</a> to<a target="_blank" href="" rel="nofollow">hdfs://m1:9000/user/flume/syslogtcp/Syslog.1407644509504</a></code></div>
<div class="line number14 index13 alt1"><code class="plain plain">/08/10 12:22:20 INFO hdfs.HDFSEventSink: Writer callback called.</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　e)在m1上再打开一个窗口，去hadoop上检查文件是否生成<br>
</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_72102">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># /home/hadoop/hadoop-2.2.0/bin/hadoop fs -ls /user/flume/syslogtcp</code></div>
<div class="line number2 index1 alt1"><code class="bash plain">Found 1 items</code></div>
<div class="line number3 index2 alt2"><code class="bash plain">-rw-r--r--  3 root supergroup    155 2014-08-10 12:22</code><code class="bash plain">/user/flume/syslogtcp/Syslog</code><code class="bash plain">.1407644509504</code></div>
<div class="line number4 index3 alt1"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># /home/hadoop/hadoop-2.2.0/bin/hadoop fs -cat /user/flume/syslogtcp/Syslog.1407644509504</code></div>
<div class="line number5 index4 alt2"><code class="bash plain">SEQ!org.apache.hadoop.io.LongWritable"org.apache.hadoop.io.BytesWritable^;&gt;Gv$hello idoall flume -&gt; hadoop testing one</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　7)案例7：File Roll Sink<br>
　　　　　　a)创建agent配置文件<br>
</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_435999">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
<div class="line number10 index9 alt1">10</div>
<div class="line number11 index10 alt2">11</div>
<div class="line number12 index11 alt1">12</div>
<div class="line number13 index12 alt2">13</div>
<div class="line number14 index13 alt1">14</div>
<div class="line number15 index14 alt2">15</div>
<div class="line number16 index15 alt1">16</div>
<div class="line number17 index16 alt2">17</div>
<div class="line number18 index17 alt1">18</div>
<div class="line number19 index18 alt2">19</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># vi /home/hadoop/flume-1.5.0-bin/conf/file_roll.conf</code></div>
<div class="line number2 index1 alt1"><code class="bash plain">a1.sources = r1</code></div>
<div class="line number3 index2 alt2"><code class="bash plain">a1.sinks = k1</code></div>
<div class="line number4 index3 alt1"><code class="bash plain">a1.channels = c1</code></div>
<div class="line number5 index4 alt2"><code class="bash comments"># Describe/configure the source</code></div>
<div class="line number6 index5 alt1"><code class="bash plain">a1.sources.r1.</code><code class="bash functions">type</code><code class="bash plain">= syslogtcp</code></div>
<div class="line number7 index6 alt2"><code class="bash plain">a1.sources.r1.port = 5555</code></div>
<div class="line number8 index7 alt1"><code class="bash plain">a1.sources.r1.host = localhost</code></div>
<div class="line number9 index8 alt2"><code class="bash plain">a1.sources.r1.channels = c1</code></div>
<div class="line number10 index9 alt1"><code class="bash comments"># Describe the sink</code></div>
<div class="line number11 index10 alt2"><code class="bash plain">a1.sinks.k1.</code><code class="bash functions">type</code><code class="bash plain">= file_roll</code></div>
<div class="line number12 index11 alt1"><code class="bash plain">a1.sinks.k1.sink.directory =</code><code class="bash plain">/home/hadoop/flume-1</code><code class="bash plain">.5.0-bin</code><code class="bash plain">/logs</code></div>
<div class="line number13 index12 alt2"><code class="bash comments"># Use a channel which buffers events in memory</code></div>
<div class="line number14 index13 alt1"><code class="bash plain">a1.channels.c1.</code><code class="bash functions">type</code><code class="bash plain">= memory</code></div>
<div class="line number15 index14 alt2"><code class="bash plain">a1.channels.c1.capacity = 1000</code></div>
<div class="line number16 index15 alt1"><code class="bash plain">a1.channels.c1.transactionCapacity = 100</code></div>
<div class="line number17 index16 alt2"><code class="bash comments"># Bind the source and sink to the channel</code></div>
<div class="line number18 index17 alt1"><code class="bash plain">a1.sources.r1.channels = c1</code></div>
<div class="line number19 index18 alt2"><code class="bash plain">a1.sinks.k1.channel = c1</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　b)启动flume agent a1<br>
</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_284075">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># /home/hadoop/flume-1.5.0-bin/bin/flume-ng agent -c . -f /home/hadoop/flume-1.5.0-bin/conf/file_roll.conf
 -n a1 -Dflume.root.logger=INFO,console</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　c)测试产生log<br>
</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_318333">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># echo "hello idoall.org syslog" | nc localhost 5555</code></div>
<div class="line number2 index1 alt1"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># echo "hello idoall.org syslog 2" | nc localhost 5555</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　d)查看/home/hadoop/flume-1.5.0-bin/logs下是否生成文件,默认每30秒生成一个新文件<br>
</p>
<div class="jb51code">
<div class="syntaxhighlighter  plain" id="highlighter_563151">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
<div class="line number10 index9 alt1">10</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="plain plain">root@m1:/home/hadoop# ll /home/hadoop/flume-1.5.0-bin/logs</code></div>
<div class="line number2 index1 alt1"><code class="plain plain">总用量 272</code></div>
<div class="line number3 index2 alt2"><code class="plain plain">drwxr-xr-x 3 root root  4096 Aug 10 12:50 ./</code></div>
<div class="line number4 index3 alt1"><code class="plain plain">drwxr-xr-x 9 root root  4096 Aug 10 10:59 ../</code></div>
<div class="line number5 index4 alt2"><code class="plain plain">-rw-r--r-- 1 root root   50 Aug 10 12:49 1407646164782-1</code></div>
<div class="line number6 index5 alt1"><code class="plain plain">-rw-r--r-- 1 root root   0 Aug 10 12:49 1407646164782-2</code></div>
<div class="line number7 index6 alt2"><code class="plain plain">-rw-r--r-- 1 root root   0 Aug 10 12:50 1407646164782-3</code></div>
<div class="line number8 index7 alt1"><code class="plain plain">root@m1:/home/hadoop# cat /home/hadoop/flume-1.5.0-bin/logs/1407646164782-1 /home/hadoop/flume-1.5.0-bin/logs/1407646164782-2</code></div>
<div class="line number9 index8 alt2"><code class="plain plain">hello idoall.org syslog</code></div>
<div class="line number10 index9 alt1"><code class="plain plain">hello idoall.org syslog 2</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　8)案例8：Replicating Channel Selector<br>
　　　　Flume支持Fan out流从一个源到多个通道。有两种模式的Fan out，分别是复制和复用。在复制的情况下，流的事件被发送到所有的配置通道。在复用的情况下，事件被发送到可用的渠道中的一个子集。Fan out流需要指定源和Fan out通道的规则。<br>
　　　　这次我们需要用到m1,m2两台机器<br>
　　　　　　a)在m1创建replicating_Channel_Selector配置文件</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_67279">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
<div class="line number10 index9 alt1">10</div>
<div class="line number11 index10 alt2">11</div>
<div class="line number12 index11 alt1">12</div>
<div class="line number13 index12 alt2">13</div>
<div class="line number14 index13 alt1">14</div>
<div class="line number15 index14 alt2">15</div>
<div class="line number16 index15 alt1">16</div>
<div class="line number17 index16 alt2">17</div>
<div class="line number18 index17 alt1">18</div>
<div class="line number19 index18 alt2">19</div>
<div class="line number20 index19 alt1">20</div>
<div class="line number21 index20 alt2">21</div>
<div class="line number22 index21 alt1">22</div>
<div class="line number23 index22 alt2">23</div>
<div class="line number24 index23 alt1">24</div>
<div class="line number25 index24 alt2">25</div>
<div class="line number26 index25 alt1">26</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># vi /home/hadoop/flume-1.5.0-bin/conf/replicating_Channel_Selector.conf</code></div>
<div class="line number2 index1 alt1"><code class="bash plain">a1.sources = r1</code></div>
<div class="line number3 index2 alt2"><code class="bash plain">a1.sinks = k1 k2</code></div>
<div class="line number4 index3 alt1"><code class="bash plain">a1.channels = c1 c2</code></div>
<div class="line number5 index4 alt2"><code class="bash comments"># Describe/configure the source</code></div>
<div class="line number6 index5 alt1"><code class="bash plain">a1.sources.r1.</code><code class="bash functions">type</code><code class="bash plain">= syslogtcp</code></div>
<div class="line number7 index6 alt2"><code class="bash plain">a1.sources.r1.port = 5140</code></div>
<div class="line number8 index7 alt1"><code class="bash plain">a1.sources.r1.host = localhost</code></div>
<div class="line number9 index8 alt2"><code class="bash plain">a1.sources.r1.channels = c1 c2</code></div>
<div class="line number10 index9 alt1"><code class="bash plain">a1.sources.r1.selector.</code><code class="bash functions">type</code><code class="bash plain">= replicating</code></div>
<div class="line number11 index10 alt2"><code class="bash comments"># Describe the sink</code></div>
<div class="line number12 index11 alt1"><code class="bash plain">a1.sinks.k1.</code><code class="bash functions">type</code><code class="bash plain">= avro</code></div>
<div class="line number13 index12 alt2"><code class="bash plain">a1.sinks.k1.channel = c1</code></div>
<div class="line number14 index13 alt1"><code class="bash plain">a1.sinks.k1.</code><code class="bash functions">hostname</code><code class="bash plain">= m1</code></div>
<div class="line number15 index14 alt2"><code class="bash plain">a1.sinks.k1.port = 5555</code></div>
<div class="line number16 index15 alt1"><code class="bash plain">a1.sinks.k2.</code><code class="bash functions">type</code><code class="bash plain">= avro</code></div>
<div class="line number17 index16 alt2"><code class="bash plain">a1.sinks.k2.channel = c2</code></div>
<div class="line number18 index17 alt1"><code class="bash plain">a1.sinks.k2.</code><code class="bash functions">hostname</code><code class="bash plain">= m2</code></div>
<div class="line number19 index18 alt2"><code class="bash plain">a1.sinks.k2.port = 5555</code></div>
<div class="line number20 index19 alt1"><code class="bash comments"># Use a channel which buffers events in memory</code></div>
<div class="line number21 index20 alt2"><code class="bash plain">a1.channels.c1.</code><code class="bash functions">type</code><code class="bash plain">= memory</code></div>
<div class="line number22 index21 alt1"><code class="bash plain">a1.channels.c1.capacity = 1000</code></div>
<div class="line number23 index22 alt2"><code class="bash plain">a1.channels.c1.transactionCapacity = 100</code></div>
<div class="line number24 index23 alt1"><code class="bash plain">a1.channels.c2.</code><code class="bash functions">type</code><code class="bash plain">= memory</code></div>
<div class="line number25 index24 alt2"><code class="bash plain">a1.channels.c2.capacity = 1000</code></div>
<div class="line number26 index25 alt1"><code class="bash plain">a1.channels.c2.transactionCapacity = 100</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　b)在m1创建replicating_Channel_Selector_avro配置文件<br>
</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_105520">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
<div class="line number10 index9 alt1">10</div>
<div class="line number11 index10 alt2">11</div>
<div class="line number12 index11 alt1">12</div>
<div class="line number13 index12 alt2">13</div>
<div class="line number14 index13 alt1">14</div>
<div class="line number15 index14 alt2">15</div>
<div class="line number16 index15 alt1">16</div>
<div class="line number17 index16 alt2">17</div>
<div class="line number18 index17 alt1">18</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># vi /home/hadoop/flume-1.5.0-bin/conf/replicating_Channel_Selector_avro.conf</code></div>
<div class="line number2 index1 alt1"><code class="bash plain">a1.sources = r1</code></div>
<div class="line number3 index2 alt2"><code class="bash plain">a1.sinks = k1</code></div>
<div class="line number4 index3 alt1"><code class="bash plain">a1.channels = c1</code></div>
<div class="line number5 index4 alt2"><code class="bash comments"># Describe/configure the source</code></div>
<div class="line number6 index5 alt1"><code class="bash plain">a1.sources.r1.</code><code class="bash functions">type</code><code class="bash plain">= avro</code></div>
<div class="line number7 index6 alt2"><code class="bash plain">a1.sources.r1.channels = c1</code></div>
<div class="line number8 index7 alt1"><code class="bash plain">a1.sources.r1.bind = 0.0.0.0</code></div>
<div class="line number9 index8 alt2"><code class="bash plain">a1.sources.r1.port = 5555</code></div>
<div class="line number10 index9 alt1"><code class="bash comments"># Describe the sink</code></div>
<div class="line number11 index10 alt2"><code class="bash plain">a1.sinks.k1.</code><code class="bash functions">type</code><code class="bash plain">= logger</code></div>
<div class="line number12 index11 alt1"><code class="bash comments"># Use a channel which buffers events in memory</code></div>
<div class="line number13 index12 alt2"><code class="bash plain">a1.channels.c1.</code><code class="bash functions">type</code><code class="bash plain">= memory</code></div>
<div class="line number14 index13 alt1"><code class="bash plain">a1.channels.c1.capacity = 1000</code></div>
<div class="line number15 index14 alt2"><code class="bash plain">a1.channels.c1.transactionCapacity = 100</code></div>
<div class="line number16 index15 alt1"><code class="bash comments"># Bind the source and sink to the channel</code></div>
<div class="line number17 index16 alt2"><code class="bash plain">a1.sources.r1.channels = c1</code></div>
<div class="line number18 index17 alt1"><code class="bash plain">a1.sinks.k1.channel = c1</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　c)在m1上将2个配置文件复制到m2上一份<br>
</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_555022">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop/flume-1</code><code class="bash plain">.5.0-bin</code><code class="bash comments"># scp -r /home/hadoop/flume-1.5.0-bin/conf/replicating_Channel_Selector.conf
 root@m2:/home/hadoop/flume-1.5.0-bin/conf/replicating_Channel_Selector.conf</code></div>
<div class="line number2 index1 alt1"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop/flume-1</code><code class="bash plain">.5.0-bin</code><code class="bash comments"># scp -r /home/hadoop/flume-1.5.0-bin/conf/replicating_Channel_Selector_avro.conf
 root@m2:/home/hadoop/flume-1.5.0-bin/conf/replicating_Channel_Selector_avro.conf&lt;br&gt;</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　d)打开4个窗口，在m1和m2上同时启动两个flume agent<br>
</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_203695">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># /home/hadoop/flume-1.5.0-bin/bin/flume-ng agent -c . -f /home/hadoop/flume-1.5.0-bin/conf/replicating_Channel_Selector_avro.conf
 -n a1 -Dflume.root.logger=INFO,console</code></div>
<div class="line number2 index1 alt1"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># /home/hadoop/flume-1.5.0-bin/bin/flume-ng agent -c . -f /home/hadoop/flume-1.5.0-bin/conf/replicating_Channel_Selector.conf
 -n a1 -Dflume.root.logger=INFO,console</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　e)然后在m1或m2的任意一台机器上，测试产生syslog<br>
</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_868786">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># echo "hello idoall.org syslog" | nc localhost 5140</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　f)在m1和m2的sink窗口，分别可以看到以下信息,这说明信息得到了同步：<br>
</p>
<div class="jb51code">
<div class="syntaxhighlighter  plain" id="highlighter_49909">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="plain plain">/08/10 14:08:18 INFO ipc.NettyServer: Connection to /192.168.1.51:46844 disconnected.</code></div>
<div class="line number2 index1 alt1"><code class="plain plain">/08/10 14:08:52 INFO ipc.NettyServer: [id: 0x90f8fe1f, /192.168.1.50:35873 =&gt; /192.168.1.50:5555] OPEN</code></div>
<div class="line number3 index2 alt2"><code class="plain plain">/08/10 14:08:52 INFO ipc.NettyServer: [id: 0x90f8fe1f, /192.168.1.50:35873 =&gt; /192.168.1.50:5555] BOUND: /192.168.1.50:5555</code></div>
<div class="line number4 index3 alt1"><code class="plain plain">/08/10 14:08:52 INFO ipc.NettyServer: [id: 0x90f8fe1f, /192.168.1.50:35873 =&gt; /192.168.1.50:5555] CONNECTED: /192.168.1.50:35873</code></div>
<div class="line number5 index4 alt2"><code class="plain plain">/08/10 14:08:59 INFO ipc.NettyServer: [id: 0xd6318635, /192.168.1.51:46858 =&gt; /192.168.1.50:5555] OPEN</code></div>
<div class="line number6 index5 alt1"><code class="plain plain">/08/10 14:08:59 INFO ipc.NettyServer: [id: 0xd6318635, /192.168.1.51:46858 =&gt; /192.168.1.50:5555] BOUND: /192.168.1.50:5555</code></div>
<div class="line number7 index6 alt2"><code class="plain plain">/08/10 14:08:59 INFO ipc.NettyServer: [id: 0xd6318635, /192.168.1.51:46858 =&gt; /192.168.1.50:5555] CONNECTED: /192.168.1.51:46858</code></div>
<div class="line number8 index7 alt1"><code class="plain plain">/08/10 14:09:20 INFO sink.LoggerSink: Event: { headers:{Severity=0, flume.syslog.status=Invalid, Facility=0} body: 68 65 6C 6C 6F 20 69 64 6F 61 6C 6C 2E 6F 72 67 hello idoall.org }</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　<br>
                 9)案例9：Multiplexing Channel Selector<br>
　　　　　　a)在m1创建Multiplexing_Channel_Selector配置文件</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_650061">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
<div class="line number10 index9 alt1">10</div>
<div class="line number11 index10 alt2">11</div>
<div class="line number12 index11 alt1">12</div>
<div class="line number13 index12 alt2">13</div>
<div class="line number14 index13 alt1">14</div>
<div class="line number15 index14 alt2">15</div>
<div class="line number16 index15 alt1">16</div>
<div class="line number17 index16 alt2">17</div>
<div class="line number18 index17 alt1">18</div>
<div class="line number19 index18 alt2">19</div>
<div class="line number20 index19 alt1">20</div>
<div class="line number21 index20 alt2">21</div>
<div class="line number22 index21 alt1">22</div>
<div class="line number23 index22 alt2">23</div>
<div class="line number24 index23 alt1">24</div>
<div class="line number25 index24 alt2">25</div>
<div class="line number26 index25 alt1">26</div>
<div class="line number27 index26 alt2">27</div>
<div class="line number28 index27 alt1">28</div>
<div class="line number29 index28 alt2">29</div>
<div class="line number30 index29 alt1">30</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># vi /home/hadoop/flume-1.5.0-bin/conf/Multiplexing_Channel_Selector.conf</code></div>
<div class="line number2 index1 alt1"><code class="bash plain">a1.sources = r1</code></div>
<div class="line number3 index2 alt2"><code class="bash plain">a1.sinks = k1 k2</code></div>
<div class="line number4 index3 alt1"><code class="bash plain">a1.channels = c1 c2</code></div>
<div class="line number5 index4 alt2"><code class="bash comments"># Describe/configure the source</code></div>
<div class="line number6 index5 alt1"><code class="bash plain">a1.sources.r1.</code><code class="bash functions">type</code><code class="bash plain">= org.apache.flume.</code><code class="bash functions">source</code><code class="bash plain">.http.HTTPSource</code></div>
<div class="line number7 index6 alt2"><code class="bash plain">a1.sources.r1.port = 5140</code></div>
<div class="line number8 index7 alt1"><code class="bash plain">a1.sources.r1.channels = c1 c2</code></div>
<div class="line number9 index8 alt2"><code class="bash plain">a1.sources.r1.selector.</code><code class="bash functions">type</code><code class="bash plain">= multiplexing</code></div>
<div class="line number10 index9 alt1"><code class="bash plain">a1.sources.r1.selector.header =</code><code class="bash functions">type</code></div>
<div class="line number11 index10 alt2"><code class="bash comments">#映射允许每个值通道可以重叠。默认值可以包含任意数量的通道。</code></div>
<div class="line number12 index11 alt1"><code class="bash plain">a1.sources.r1.selector.mapping.baidu = c1</code></div>
<div class="line number13 index12 alt2"><code class="bash plain">a1.sources.r1.selector.mapping.ali = c2</code></div>
<div class="line number14 index13 alt1"><code class="bash plain">a1.sources.r1.selector.default = c1</code></div>
<div class="line number15 index14 alt2"><code class="bash comments"># Describe the sink</code></div>
<div class="line number16 index15 alt1"><code class="bash plain">a1.sinks.k1.</code><code class="bash functions">type</code><code class="bash plain">= avro</code></div>
<div class="line number17 index16 alt2"><code class="bash plain">a1.sinks.k1.channel = c1</code></div>
<div class="line number18 index17 alt1"><code class="bash plain">a1.sinks.k1.</code><code class="bash functions">hostname</code><code class="bash plain">= m1</code></div>
<div class="line number19 index18 alt2"><code class="bash plain">a1.sinks.k1.port = 5555</code></div>
<div class="line number20 index19 alt1"><code class="bash plain">a1.sinks.k2.</code><code class="bash functions">type</code><code class="bash plain">= avro</code></div>
<div class="line number21 index20 alt2"><code class="bash plain">a1.sinks.k2.channel = c2</code></div>
<div class="line number22 index21 alt1"><code class="bash plain">a1.sinks.k2.</code><code class="bash functions">hostname</code><code class="bash plain">= m2</code></div>
<div class="line number23 index22 alt2"><code class="bash plain">a1.sinks.k2.port = 5555</code></div>
<div class="line number24 index23 alt1"><code class="bash comments"># Use a channel which buffers events in memory</code></div>
<div class="line number25 index24 alt2"><code class="bash plain">a1.channels.c1.</code><code class="bash functions">type</code><code class="bash plain">= memory</code></div>
<div class="line number26 index25 alt1"><code class="bash plain">a1.channels.c1.capacity = 1000</code></div>
<div class="line number27 index26 alt2"><code class="bash plain">a1.channels.c1.transactionCapacity = 100</code></div>
<div class="line number28 index27 alt1"><code class="bash plain">a1.channels.c2.</code><code class="bash functions">type</code><code class="bash plain">= memory</code></div>
<div class="line number29 index28 alt2"><code class="bash plain">a1.channels.c2.capacity = 1000</code></div>
<div class="line number30 index29 alt1"><code class="bash plain">a1.channels.c2.transactionCapacity = 100</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　b)在m1创建Multiplexing_Channel_Selector_avro配置文件<br>
</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_93360">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
<div class="line number10 index9 alt1">10</div>
<div class="line number11 index10 alt2">11</div>
<div class="line number12 index11 alt1">12</div>
<div class="line number13 index12 alt2">13</div>
<div class="line number14 index13 alt1">14</div>
<div class="line number15 index14 alt2">15</div>
<div class="line number16 index15 alt1">16</div>
<div class="line number17 index16 alt2">17</div>
<div class="line number18 index17 alt1">18</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># vi /home/hadoop/flume-1.5.0-bin/conf/Multiplexing_Channel_Selector_avro.conf</code></div>
<div class="line number2 index1 alt1"><code class="bash plain">a1.sources = r1</code></div>
<div class="line number3 index2 alt2"><code class="bash plain">a1.sinks = k1</code></div>
<div class="line number4 index3 alt1"><code class="bash plain">a1.channels = c1</code></div>
<div class="line number5 index4 alt2"><code class="bash comments"># Describe/configure the source</code></div>
<div class="line number6 index5 alt1"><code class="bash plain">a1.sources.r1.</code><code class="bash functions">type</code><code class="bash plain">= avro</code></div>
<div class="line number7 index6 alt2"><code class="bash plain">a1.sources.r1.channels = c1</code></div>
<div class="line number8 index7 alt1"><code class="bash plain">a1.sources.r1.bind = 0.0.0.0</code></div>
<div class="line number9 index8 alt2"><code class="bash plain">a1.sources.r1.port = 5555</code></div>
<div class="line number10 index9 alt1"><code class="bash comments"># Describe the sink</code></div>
<div class="line number11 index10 alt2"><code class="bash plain">a1.sinks.k1.</code><code class="bash functions">type</code><code class="bash plain">= logger</code></div>
<div class="line number12 index11 alt1"><code class="bash comments"># Use a channel which buffers events in memory</code></div>
<div class="line number13 index12 alt2"><code class="bash plain">a1.channels.c1.</code><code class="bash functions">type</code><code class="bash plain">= memory</code></div>
<div class="line number14 index13 alt1"><code class="bash plain">a1.channels.c1.capacity = 1000</code></div>
<div class="line number15 index14 alt2"><code class="bash plain">a1.channels.c1.transactionCapacity = 100</code></div>
<div class="line number16 index15 alt1"><code class="bash comments"># Bind the source and sink to the channel</code></div>
<div class="line number17 index16 alt2"><code class="bash plain">a1.sources.r1.channels = c1</code></div>
<div class="line number18 index17 alt1"><code class="bash plain">a1.sinks.k1.channel = c1</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　c)将2个配置文件复制到m2上一份<br>
</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_386165">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop/flume-1</code><code class="bash plain">.5.0-bin</code><code class="bash comments"># scp -r /home/hadoop/flume-1.5.0-bin/conf/Multiplexing_Channel_Selector.conf
 root@m2:/home/hadoop/flume-1.5.0-bin/conf/Multiplexing_Channel_Selector.conf</code></div>
<div class="line number2 index1 alt1"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop/flume-1</code><code class="bash plain">.5.0-bin</code><code class="bash comments"># scp -r /home/hadoop/flume-1.5.0-bin/conf/Multiplexing_Channel_Selector_avro.conf
 root@m2:/home/hadoop/flume-1.5.0-bin/conf/Multiplexing_Channel_Selector_avro.conf</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　d)打开4个窗口，在m1和m2上同时启动两个flume agent<br>
</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_103783">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># /home/hadoop/flume-1.5.0-bin/bin/flume-ng agent -c . -f /home/hadoop/flume-1.5.0-bin/conf/Multiplexing_Channel_Selector_avro.conf
 -n a1 -Dflume.root.logger=INFO,console</code></div>
<div class="line number2 index1 alt1"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># /home/hadoop/flume-1.5.0-bin/bin/flume-ng agent -c . -f /home/hadoop/flume-1.5.0-bin/conf/Multiplexing_Channel_Selector.conf
 -n a1 -Dflume.root.logger=INFO,console</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　e)然后在m1或m2的任意一台机器上，测试产生syslog</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_560069">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># curl -X POST -d '[{ "headers" :{"type" : "baidu"},"body" : "idoall_TEST1"}]'<a target="_blank" href="http://localhost:5140/" rel="nofollow">http://localhost:5140</a>
 &amp;&amp; curl -X POST -d '[{ "headers" :{"type" : "ali"},"body" : "idoall_TEST2"}]'<a target="_blank" href="http://localhost:5140/" rel="nofollow">http://localhost:5140</a> &amp;&amp; curl -X POST -d '[{ "headers" :{"type" : "qq"},"body" : "idoall_TEST3"}]'<a target="_blank" href="http://localhost:5140/" rel="nofollow">http://localhost:5140</a></code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　f)在m1的sink窗口，可以看到以下信息：</p>
<div class="jb51code">
<div class="syntaxhighlighter  plain" id="highlighter_711252">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
<div class="line number10 index9 alt1">10</div>
<div class="line number11 index10 alt2">11</div>
<div class="line number12 index11 alt1">12</div>
<div class="line number13 index12 alt2">13</div>
<div class="line number14 index13 alt1">14</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="plain plain">14/08/10 14:32:21 INFO node.Application: Starting Sink k1</code></div>
<div class="line number2 index1 alt1"><code class="plain plain">14/08/10 14:32:21 INFO node.Application: Starting Source r1</code></div>
<div class="line number3 index2 alt2"><code class="plain plain">14/08/10 14:32:21 INFO source.AvroSource: Starting Avro source r1: { bindAddress: 0.0.0.0, port: 5555 }...</code></div>
<div class="line number4 index3 alt1"><code class="plain plain">14/08/10 14:32:21 INFO instrumentation.MonitoredCounterGroup: Monitored counter group for type: SOURCE, name: r1: Successfully registered new MBean.</code></div>
<div class="line number5 index4 alt2"><code class="plain plain">14/08/10 14:32:21 INFO instrumentation.MonitoredCounterGroup: Component type: SOURCE, name: r1 started</code></div>
<div class="line number6 index5 alt1"><code class="plain plain">14/08/10 14:32:21 INFO source.AvroSource: Avro source r1 started.</code></div>
<div class="line number7 index6 alt2"><code class="plain plain">14/08/10 14:32:36 INFO ipc.NettyServer: [id: 0xcf00eea6, /192.168.1.50:35916 =&gt; /192.168.1.50:5555] OPEN</code></div>
<div class="line number8 index7 alt1"><code class="plain plain">14/08/10 14:32:36 INFO ipc.NettyServer: [id: 0xcf00eea6, /192.168.1.50:35916 =&gt; /192.168.1.50:5555] BOUND: /192.168.1.50:5555</code></div>
<div class="line number9 index8 alt2"><code class="plain plain">14/08/10 14:32:36 INFO ipc.NettyServer: [id: 0xcf00eea6, /192.168.1.50:35916 =&gt; /192.168.1.50:5555] CONNECTED: /192.168.1.50:35916</code></div>
<div class="line number10 index9 alt1"><code class="plain plain">14/08/10 14:32:44 INFO ipc.NettyServer: [id: 0x432f5468, /192.168.1.51:46945 =&gt; /192.168.1.50:5555] OPEN</code></div>
<div class="line number11 index10 alt2"><code class="plain plain">14/08/10 14:32:44 INFO ipc.NettyServer: [id: 0x432f5468, /192.168.1.51:46945 =&gt; /192.168.1.50:5555] BOUND: /192.168.1.50:5555</code></div>
<div class="line number12 index11 alt1"><code class="plain plain">14/08/10 14:32:44 INFO ipc.NettyServer: [id: 0x432f5468, /192.168.1.51:46945 =&gt; /192.168.1.50:5555] CONNECTED: /192.168.1.51:46945</code></div>
<div class="line number13 index12 alt2"><code class="plain plain">14/08/10 14:34:11 INFO sink.LoggerSink: Event: { headers:{type=baidu} body: 69 64 6F 61 6C 6C 5F 54 45 53 54 31       idoall_TEST1 }</code></div>
<div class="line number14 index13 alt1"><code class="plain plain">14/08/10 14:34:57 INFO sink.LoggerSink: Event: { headers:{type=qq} body: 69 64 6F 61 6C 6C 5F 54 45 53 54 33       idoall_TEST3 }</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　g)在m2的sink窗口，可以看到以下信息：</p>
<div class="jb51code">
<div class="syntaxhighlighter  plain" id="highlighter_907883">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
<div class="line number10 index9 alt1">10</div>
<div class="line number11 index10 alt2">11</div>
<div class="line number12 index11 alt1">12</div>
<div class="line number13 index12 alt2">13</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="plain plain">14/08/10 14:32:27 INFO node.Application: Starting Sink k1</code></div>
<div class="line number2 index1 alt1"><code class="plain plain">14/08/10 14:32:27 INFO node.Application: Starting Source r1</code></div>
<div class="line number3 index2 alt2"><code class="plain plain">14/08/10 14:32:27 INFO source.AvroSource: Starting Avro source r1: { bindAddress: 0.0.0.0, port: 5555 }...</code></div>
<div class="line number4 index3 alt1"><code class="plain plain">14/08/10 14:32:27 INFO instrumentation.MonitoredCounterGroup: Monitored counter group for type: SOURCE, name: r1: Successfully registered new MBean.</code></div>
<div class="line number5 index4 alt2"><code class="plain plain">14/08/10 14:32:27 INFO instrumentation.MonitoredCounterGroup: Component type: SOURCE, name: r1 started</code></div>
<div class="line number6 index5 alt1"><code class="plain plain">14/08/10 14:32:27 INFO source.AvroSource: Avro source r1 started.</code></div>
<div class="line number7 index6 alt2"><code class="plain plain">14/08/10 14:32:36 INFO ipc.NettyServer: [id: 0x7c2f0aec, /192.168.1.50:38104 =&gt; /192.168.1.51:5555] OPEN</code></div>
<div class="line number8 index7 alt1"><code class="plain plain">14/08/10 14:32:36 INFO ipc.NettyServer: [id: 0x7c2f0aec, /192.168.1.50:38104 =&gt; /192.168.1.51:5555] BOUND: /192.168.1.51:5555</code></div>
<div class="line number9 index8 alt2"><code class="plain plain">14/08/10 14:32:36 INFO ipc.NettyServer: [id: 0x7c2f0aec, /192.168.1.50:38104 =&gt; /192.168.1.51:5555] CONNECTED: /192.168.1.50:38104</code></div>
<div class="line number10 index9 alt1"><code class="plain plain">14/08/10 14:32:44 INFO ipc.NettyServer: [id: 0x3d36f553, /192.168.1.51:48599 =&gt; /192.168.1.51:5555] OPEN</code></div>
<div class="line number11 index10 alt2"><code class="plain plain">14/08/10 14:32:44 INFO ipc.NettyServer: [id: 0x3d36f553, /192.168.1.51:48599 =&gt; /192.168.1.51:5555] BOUND: /192.168.1.51:5555</code></div>
<div class="line number12 index11 alt1"><code class="plain plain">14/08/10 14:32:44 INFO ipc.NettyServer: [id: 0x3d36f553, /192.168.1.51:48599 =&gt; /192.168.1.51:5555] CONNECTED: /192.168.1.51:48599</code></div>
<div class="line number13 index12 alt2"><code class="plain plain">14/08/10 14:34:33 INFO sink.LoggerSink: Event: { headers:{type=ali} body: 69 64 6F 61 6C 6C 5F 54 45 53 54 32       idoall_TEST2 }</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　可以看到，根据header中不同的条件分布到不同的channel上<br>
 <br>
　　　　10)案例10：Flume Sink Processors<br>
　　　　failover的机器是一直发送给其中一个sink，当这个sink不可用的时候，自动发送到下一个sink。<br>
 <br>
　　　　　　a)在m1创建Flume_Sink_Processors配置文件</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_604715">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
<div class="line number10 index9 alt1">10</div>
<div class="line number11 index10 alt2">11</div>
<div class="line number12 index11 alt1">12</div>
<div class="line number13 index12 alt2">13</div>
<div class="line number14 index13 alt1">14</div>
<div class="line number15 index14 alt2">15</div>
<div class="line number16 index15 alt1">16</div>
<div class="line number17 index16 alt2">17</div>
<div class="line number18 index17 alt1">18</div>
<div class="line number19 index18 alt2">19</div>
<div class="line number20 index19 alt1">20</div>
<div class="line number21 index20 alt2">21</div>
<div class="line number22 index21 alt1">22</div>
<div class="line number23 index22 alt2">23</div>
<div class="line number24 index23 alt1">24</div>
<div class="line number25 index24 alt2">25</div>
<div class="line number26 index25 alt1">26</div>
<div class="line number27 index26 alt2">27</div>
<div class="line number28 index27 alt1">28</div>
<div class="line number29 index28 alt2">29</div>
<div class="line number30 index29 alt1">30</div>
<div class="line number31 index30 alt2">31</div>
<div class="line number32 index31 alt1">32</div>
<div class="line number33 index32 alt2">33</div>
<div class="line number34 index33 alt1">34</div>
<div class="line number35 index34 alt2">35</div>
<div class="line number36 index35 alt1">36</div>
<div class="line number37 index36 alt2">37</div>
<div class="line number38 index37 alt1">38</div>
<div class="line number39 index38 alt2">39</div>
<div class="line number40 index39 alt1">40</div>
<div class="line number41 index40 alt2">41</div>
<div class="line number42 index41 alt1">42</div>
<div class="line number43 index42 alt2">43</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># vi /home/hadoop/flume-1.5.0-bin/conf/Flume_Sink_Processors.conf</code></div>
<div class="line number2 index1 alt1"><code class="bash spaces"> </code> </div>
<div class="line number3 index2 alt2"><code class="bash plain">a1.sources = r1</code></div>
<div class="line number4 index3 alt1"><code class="bash plain">a1.sinks = k1 k2</code></div>
<div class="line number5 index4 alt2"><code class="bash plain">a1.channels = c1 c2</code></div>
<div class="line number6 index5 alt1"><code class="bash spaces"> </code> </div>
<div class="line number7 index6 alt2"><code class="bash comments">#这个是配置failover的关键，需要有一个sink group</code></div>
<div class="line number8 index7 alt1"><code class="bash plain">a1.sinkgroups = g1</code></div>
<div class="line number9 index8 alt2"><code class="bash plain">a1.sinkgroups.g1.sinks = k1 k2</code></div>
<div class="line number10 index9 alt1"><code class="bash comments">#处理的类型是failover</code></div>
<div class="line number11 index10 alt2"><code class="bash plain">a1.sinkgroups.g1.processor.</code><code class="bash functions">type</code><code class="bash plain">= failover</code></div>
<div class="line number12 index11 alt1"><code class="bash comments">#优先级，数字越大优先级越高，每个sink的优先级必须不相同</code></div>
<div class="line number13 index12 alt2"><code class="bash plain">a1.sinkgroups.g1.processor.priority.k1 = 5</code></div>
<div class="line number14 index13 alt1"><code class="bash plain">a1.sinkgroups.g1.processor.priority.k2 = 10</code></div>
<div class="line number15 index14 alt2"><code class="bash comments">#设置为10秒，当然可以根据你的实际状况更改成更快或者很慢</code></div>
<div class="line number16 index15 alt1"><code class="bash plain">a1.sinkgroups.g1.processor.maxpenalty = 10000</code></div>
<div class="line number17 index16 alt2"><code class="bash spaces"> </code> </div>
<div class="line number18 index17 alt1"><code class="bash comments"># Describe/configure the source</code></div>
<div class="line number19 index18 alt2"><code class="bash plain">a1.sources.r1.</code><code class="bash functions">type</code><code class="bash plain">= syslogtcp</code></div>
<div class="line number20 index19 alt1"><code class="bash plain">a1.sources.r1.port = 5140</code></div>
<div class="line number21 index20 alt2"><code class="bash plain">a1.sources.r1.channels = c1 c2</code></div>
<div class="line number22 index21 alt1"><code class="bash plain">a1.sources.r1.selector.</code><code class="bash functions">type</code><code class="bash plain">= replicating</code></div>
<div class="line number23 index22 alt2"><code class="bash spaces"> </code> </div>
<div class="line number24 index23 alt1"><code class="bash spaces"> </code> </div>
<div class="line number25 index24 alt2"><code class="bash comments"># Describe the sink</code></div>
<div class="line number26 index25 alt1"><code class="bash plain">a1.sinks.k1.</code><code class="bash functions">type</code><code class="bash plain">= avro</code></div>
<div class="line number27 index26 alt2"><code class="bash plain">a1.sinks.k1.channel = c1</code></div>
<div class="line number28 index27 alt1"><code class="bash plain">a1.sinks.k1.</code><code class="bash functions">hostname</code><code class="bash plain">= m1</code></div>
<div class="line number29 index28 alt2"><code class="bash plain">a1.sinks.k1.port = 5555</code></div>
<div class="line number30 index29 alt1"><code class="bash spaces"> </code> </div>
<div class="line number31 index30 alt2"><code class="bash plain">a1.sinks.k2.</code><code class="bash functions">type</code><code class="bash plain">= avro</code></div>
<div class="line number32 index31 alt1"><code class="bash plain">a1.sinks.k2.channel = c2</code></div>
<div class="line number33 index32 alt2"><code class="bash plain">a1.sinks.k2.</code><code class="bash functions">hostname</code><code class="bash plain">= m2</code></div>
<div class="line number34 index33 alt1"><code class="bash plain">a1.sinks.k2.port = 5555</code></div>
<div class="line number35 index34 alt2"><code class="bash spaces"> </code> </div>
<div class="line number36 index35 alt1"><code class="bash comments"># Use a channel which buffers events in memory</code></div>
<div class="line number37 index36 alt2"><code class="bash plain">a1.channels.c1.</code><code class="bash functions">type</code><code class="bash plain">= memory</code></div>
<div class="line number38 index37 alt1"><code class="bash plain">a1.channels.c1.capacity = 1000</code></div>
<div class="line number39 index38 alt2"><code class="bash plain">a1.channels.c1.transactionCapacity = 100</code></div>
<div class="line number40 index39 alt1"><code class="bash spaces"> </code> </div>
<div class="line number41 index40 alt2"><code class="bash plain">a1.channels.c2.</code><code class="bash functions">type</code><code class="bash plain">= memory</code></div>
<div class="line number42 index41 alt1"><code class="bash plain">a1.channels.c2.capacity = 1000</code></div>
<div class="line number43 index42 alt2"><code class="bash plain">a1.channels.c2.transactionCapacity = 100</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　b)在m1创建Flume_Sink_Processors_avro配置文件</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_657024">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
<div class="line number10 index9 alt1">10</div>
<div class="line number11 index10 alt2">11</div>
<div class="line number12 index11 alt1">12</div>
<div class="line number13 index12 alt2">13</div>
<div class="line number14 index13 alt1">14</div>
<div class="line number15 index14 alt2">15</div>
<div class="line number16 index15 alt1">16</div>
<div class="line number17 index16 alt2">17</div>
<div class="line number18 index17 alt1">18</div>
<div class="line number19 index18 alt2">19</div>
<div class="line number20 index19 alt1">20</div>
<div class="line number21 index20 alt2">21</div>
<div class="line number22 index21 alt1">22</div>
<div class="line number23 index22 alt2">23</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># vi /home/hadoop/flume-1.5.0-bin/conf/Flume_Sink_Processors_avro.conf</code></div>
<div class="line number2 index1 alt1"><code class="bash spaces"> </code> </div>
<div class="line number3 index2 alt2"><code class="bash plain">a1.sources = r1</code></div>
<div class="line number4 index3 alt1"><code class="bash plain">a1.sinks = k1</code></div>
<div class="line number5 index4 alt2"><code class="bash plain">a1.channels = c1</code></div>
<div class="line number6 index5 alt1"><code class="bash spaces"> </code> </div>
<div class="line number7 index6 alt2"><code class="bash comments"># Describe/configure the source</code></div>
<div class="line number8 index7 alt1"><code class="bash plain">a1.sources.r1.</code><code class="bash functions">type</code><code class="bash plain">= avro</code></div>
<div class="line number9 index8 alt2"><code class="bash plain">a1.sources.r1.channels = c1</code></div>
<div class="line number10 index9 alt1"><code class="bash plain">a1.sources.r1.bind = 0.0.0.0</code></div>
<div class="line number11 index10 alt2"><code class="bash plain">a1.sources.r1.port = 5555</code></div>
<div class="line number12 index11 alt1"><code class="bash spaces"> </code> </div>
<div class="line number13 index12 alt2"><code class="bash comments"># Describe the sink</code></div>
<div class="line number14 index13 alt1"><code class="bash plain">a1.sinks.k1.</code><code class="bash functions">type</code><code class="bash plain">= logger</code></div>
<div class="line number15 index14 alt2"><code class="bash spaces"> </code> </div>
<div class="line number16 index15 alt1"><code class="bash comments"># Use a channel which buffers events in memory</code></div>
<div class="line number17 index16 alt2"><code class="bash plain">a1.channels.c1.</code><code class="bash functions">type</code><code class="bash plain">= memory</code></div>
<div class="line number18 index17 alt1"><code class="bash plain">a1.channels.c1.capacity = 1000</code></div>
<div class="line number19 index18 alt2"><code class="bash plain">a1.channels.c1.transactionCapacity = 100</code></div>
<div class="line number20 index19 alt1"><code class="bash spaces"> </code> </div>
<div class="line number21 index20 alt2"><code class="bash comments"># Bind the source and sink to the channel</code></div>
<div class="line number22 index21 alt1"><code class="bash plain">a1.sources.r1.channels = c1</code></div>
<div class="line number23 index22 alt2"><code class="bash plain">a1.sinks.k1.channel = c1</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　c)将2个配置文件复制到m2上一份</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_425106">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop/flume-1</code><code class="bash plain">.5.0-bin</code><code class="bash comments"># scp -r /home/hadoop/flume-1.5.0-bin/conf/Flume_Sink_Processors.conf
 root@m2:/home/hadoop/flume-1.5.0-bin/conf/Flume_Sink_Processors.conf</code></div>
<div class="line number2 index1 alt1"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop/flume-1</code><code class="bash plain">.5.0-bin</code><code class="bash comments"># scp -r /home/hadoop/flume-1.5.0-bin/conf/Flume_Sink_Processors_avro.conf
 root@m2:/home/hadoop/flume-1.5.0-bin/conf/Flume_Sink_Processors_avro.conf</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　d)打开4个窗口，在m1和m2上同时启动两个flume agent</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_800022">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># /home/hadoop/flume-1.5.0-bin/bin/flume-ng agent -c . -f /home/hadoop/flume-1.5.0-bin/conf/Flume_Sink_Processors_avro.conf
 -n a1 -Dflume.root.logger=INFO,console</code></div>
<div class="line number2 index1 alt1"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># /home/hadoop/flume-1.5.0-bin/bin/flume-ng agent -c . -f /home/hadoop/flume-1.5.0-bin/conf/Flume_Sink_Processors.conf
 -n a1 -Dflume.root.logger=INFO,console</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　e)然后在m1或m2的任意一台机器上，测试产生log</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_406616">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># echo "idoall.org test1 failover" | nc localhost 5140</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　f)因为m2的优先级高，所以在m2的sink窗口，可以看到以下信息，而m1没有：</p>
<div class="jb51code">
<div class="syntaxhighlighter  plain" id="highlighter_680847">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="plain plain">14/08/10 15:02:46 INFO ipc.NettyServer: Connection to /192.168.1.51:48692 disconnected.</code></div>
<div class="line number2 index1 alt1"><code class="plain plain">14/08/10 15:03:12 INFO ipc.NettyServer: [id: 0x09a14036, /192.168.1.51:48704 =&gt; /192.168.1.51:5555] OPEN</code></div>
<div class="line number3 index2 alt2"><code class="plain plain">14/08/10 15:03:12 INFO ipc.NettyServer: [id: 0x09a14036, /192.168.1.51:48704 =&gt; /192.168.1.51:5555] BOUND: /192.168.1.51:5555</code></div>
<div class="line number4 index3 alt1"><code class="plain plain">14/08/10 15:03:12 INFO ipc.NettyServer: [id: 0x09a14036, /192.168.1.51:48704 =&gt; /192.168.1.51:5555] CONNECTED: /192.168.1.51:48704</code></div>
<div class="line number5 index4 alt2"><code class="plain plain">14/08/10 15:03:26 INFO sink.LoggerSink: Event: { headers:{Severity=0, flume.syslog.status=Invalid, Facility=0} body: 69 64 6F 61 6C 6C 2E 6F 72 67 20 74 65 73 74 31 idoall.org test1 }</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　g)这时我们停止掉m2机器上的sink(ctrl+c)，再次输出测试数据：</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_53550">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># echo "idoall.org test2 failover" | nc localhost 5140</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　h)可以在m1的sink窗口，看到读取到了刚才发送的两条测试数据：</p>
<div class="jb51code">
<div class="syntaxhighlighter  plain" id="highlighter_366843">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="plain plain">14/08/10 15:02:46 INFO ipc.NettyServer: Connection to /192.168.1.51:47036 disconnected.</code></div>
<div class="line number2 index1 alt1"><code class="plain plain">14/08/10 15:03:12 INFO ipc.NettyServer: [id: 0xbcf79851, /192.168.1.51:47048 =&gt; /192.168.1.50:5555] OPEN</code></div>
<div class="line number3 index2 alt2"><code class="plain plain">14/08/10 15:03:12 INFO ipc.NettyServer: [id: 0xbcf79851, /192.168.1.51:47048 =&gt; /192.168.1.50:5555] BOUND: /192.168.1.50:5555</code></div>
<div class="line number4 index3 alt1"><code class="plain plain">14/08/10 15:03:12 INFO ipc.NettyServer: [id: 0xbcf79851, /192.168.1.51:47048 =&gt; /192.168.1.50:5555] CONNECTED: /192.168.1.51:47048</code></div>
<div class="line number5 index4 alt2"><code class="plain plain">14/08/10 15:07:56 INFO sink.LoggerSink: Event: { headers:{Severity=0, flume.syslog.status=Invalid, Facility=0} body: 69 64 6F 61 6C 6C 2E 6F 72 67 20 74 65 73 74 31 idoall.org test1 }</code></div>
<div class="line number6 index5 alt1"><code class="plain plain">14/08/10 15:07:56 INFO sink.LoggerSink: Event: { headers:{Severity=0, flume.syslog.status=Invalid, Facility=0} body: 69 64 6F 61 6C 6C 2E 6F 72 67 20 74 65 73 74 32 idoall.org test2 }</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　i)我们再在m2的sink窗口中，启动sink：<br>
</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_220029">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># /home/hadoop/flume-1.5.0-bin/bin/flume-ng agent -c . -f /home/hadoop/flume-1.5.0-bin/conf/Flume_Sink_Processors_avro.conf
 -n a1 -Dflume.root.logger=INFO,console</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　j)输入两批测试数据：<br>
</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_87819">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># echo "idoall.org test3 failover" | nc localhost 5140 &amp;&amp; echo "idoall.org test4 failover" | nc localhost 5140</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　k)在m2的sink窗口，我们可以看到以下信息，因为优先级的关系，log消息会再次落到m2上：</p>
<div class="jb51code">
<div class="syntaxhighlighter  plain" id="highlighter_343684">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
<div class="line number10 index9 alt1">10</div>
<div class="line number11 index10 alt2">11</div>
<div class="line number12 index11 alt1">12</div>
<div class="line number13 index12 alt2">13</div>
<div class="line number14 index13 alt1">14</div>
<div class="line number15 index14 alt2">15</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="plain plain">14/08/10 15:09:47 INFO node.Application: Starting Sink k1</code></div>
<div class="line number2 index1 alt1"><code class="plain plain">14/08/10 15:09:47 INFO node.Application: Starting Source r1</code></div>
<div class="line number3 index2 alt2"><code class="plain plain">14/08/10 15:09:47 INFO source.AvroSource: Starting Avro source r1: { bindAddress: 0.0.0.0, port: 5555 }...</code></div>
<div class="line number4 index3 alt1"><code class="plain plain">14/08/10 15:09:47 INFO instrumentation.MonitoredCounterGroup: Monitored counter group for type: SOURCE, name: r1: Successfully registered new MBean.</code></div>
<div class="line number5 index4 alt2"><code class="plain plain">14/08/10 15:09:47 INFO instrumentation.MonitoredCounterGroup: Component type: SOURCE, name: r1 started</code></div>
<div class="line number6 index5 alt1"><code class="plain plain">14/08/10 15:09:47 INFO source.AvroSource: Avro source r1 started.</code></div>
<div class="line number7 index6 alt2"><code class="plain plain">14/08/10 15:09:54 INFO ipc.NettyServer: [id: 0x96615732, /192.168.1.51:48741 =&gt; /192.168.1.51:5555] OPEN</code></div>
<div class="line number8 index7 alt1"><code class="plain plain">14/08/10 15:09:54 INFO ipc.NettyServer: [id: 0x96615732, /192.168.1.51:48741 =&gt; /192.168.1.51:5555] BOUND: /192.168.1.51:5555</code></div>
<div class="line number9 index8 alt2"><code class="plain plain">14/08/10 15:09:54 INFO ipc.NettyServer: [id: 0x96615732, /192.168.1.51:48741 =&gt; /192.168.1.51:5555] CONNECTED: /192.168.1.51:48741</code></div>
<div class="line number10 index9 alt1"><code class="plain plain">14/08/10 15:09:57 INFO sink.LoggerSink: Event: { headers:{Severity=0, flume.syslog.status=Invalid, Facility=0} body: 69 64 6F 61 6C 6C 2E 6F 72 67 20 74 65 73 74 32 idoall.org test2 }</code></div>
<div class="line number11 index10 alt2"><code class="plain plain">14/08/10 15:10:43 INFO ipc.NettyServer: [id: 0x12621f9a, /192.168.1.50:38166 =&gt; /192.168.1.51:5555] OPEN</code></div>
<div class="line number12 index11 alt1"><code class="plain plain">14/08/10 15:10:43 INFO ipc.NettyServer: [id: 0x12621f9a, /192.168.1.50:38166 =&gt; /192.168.1.51:5555] BOUND: /192.168.1.51:5555</code></div>
<div class="line number13 index12 alt2"><code class="plain plain">14/08/10 15:10:43 INFO ipc.NettyServer: [id: 0x12621f9a, /192.168.1.50:38166 =&gt; /192.168.1.51:5555] CONNECTED: /192.168.1.50:38166</code></div>
<div class="line number14 index13 alt1"><code class="plain plain">14/08/10 15:10:43 INFO sink.LoggerSink: Event: { headers:{Severity=0, flume.syslog.status=Invalid, Facility=0} body: 69 64 6F 61 6C 6C 2E 6F 72 67 20 74 65 73 74 33 idoall.org test3 }</code></div>
<div class="line number15 index14 alt2"><code class="plain plain">14/08/10 15:10:43 INFO sink.LoggerSink: Event: { headers:{Severity=0, flume.syslog.status=Invalid, Facility=0} body: 69 64 6F 61 6C 6C 2E 6F 72 67 20 74 65 73 74 34 idoall.org test4 }</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p> <br>
　　　　11)案例11：Load balancing Sink Processor<br>
　　　　load balance type和failover不同的地方是，load balance有两个配置，一个是轮询，一个是随机。两种情况下如果被选择的sink不可用，就会自动尝试发送到下一个可用的sink上面。<br>
 <br>
　　　　　　a)在m1创建Load_balancing_Sink_Processors配置文件</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_35126">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
<div class="line number10 index9 alt1">10</div>
<div class="line number11 index10 alt2">11</div>
<div class="line number12 index11 alt1">12</div>
<div class="line number13 index12 alt2">13</div>
<div class="line number14 index13 alt1">14</div>
<div class="line number15 index14 alt2">15</div>
<div class="line number16 index15 alt1">16</div>
<div class="line number17 index16 alt2">17</div>
<div class="line number18 index17 alt1">18</div>
<div class="line number19 index18 alt2">19</div>
<div class="line number20 index19 alt1">20</div>
<div class="line number21 index20 alt2">21</div>
<div class="line number22 index21 alt1">22</div>
<div class="line number23 index22 alt2">23</div>
<div class="line number24 index23 alt1">24</div>
<div class="line number25 index24 alt2">25</div>
<div class="line number26 index25 alt1">26</div>
<div class="line number27 index26 alt2">27</div>
<div class="line number28 index27 alt1">28</div>
<div class="line number29 index28 alt2">29</div>
<div class="line number30 index29 alt1">30</div>
<div class="line number31 index30 alt2">31</div>
<div class="line number32 index31 alt1">32</div>
<div class="line number33 index32 alt2">33</div>
<div class="line number34 index33 alt1">34</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># vi /home/hadoop/flume-1.5.0-bin/conf/Load_balancing_Sink_Processors.conf</code></div>
<div class="line number2 index1 alt1"><code class="bash spaces"> </code> </div>
<div class="line number3 index2 alt2"><code class="bash plain">a1.sources = r1</code></div>
<div class="line number4 index3 alt1"><code class="bash plain">a1.sinks = k1 k2</code></div>
<div class="line number5 index4 alt2"><code class="bash plain">a1.channels = c1</code></div>
<div class="line number6 index5 alt1"><code class="bash spaces"> </code> </div>
<div class="line number7 index6 alt2"><code class="bash comments">#这个是配置Load balancing的关键，需要有一个sink group</code></div>
<div class="line number8 index7 alt1"><code class="bash plain">a1.sinkgroups = g1</code></div>
<div class="line number9 index8 alt2"><code class="bash plain">a1.sinkgroups.g1.sinks = k1 k2</code></div>
<div class="line number10 index9 alt1"><code class="bash plain">a1.sinkgroups.g1.processor.</code><code class="bash functions">type</code><code class="bash plain">= load_balance</code></div>
<div class="line number11 index10 alt2"><code class="bash plain">a1.sinkgroups.g1.processor.backoff =</code><code class="bash functions">true</code></div>
<div class="line number12 index11 alt1"><code class="bash plain">a1.sinkgroups.g1.processor.selector = round_robin</code></div>
<div class="line number13 index12 alt2"><code class="bash spaces"> </code> </div>
<div class="line number14 index13 alt1"><code class="bash comments"># Describe/configure the source</code></div>
<div class="line number15 index14 alt2"><code class="bash plain">a1.sources.r1.</code><code class="bash functions">type</code><code class="bash plain">= syslogtcp</code></div>
<div class="line number16 index15 alt1"><code class="bash plain">a1.sources.r1.port = 5140</code></div>
<div class="line number17 index16 alt2"><code class="bash plain">a1.sources.r1.channels = c1</code></div>
<div class="line number18 index17 alt1"><code class="bash spaces"> </code> </div>
<div class="line number19 index18 alt2"><code class="bash spaces"> </code> </div>
<div class="line number20 index19 alt1"><code class="bash comments"># Describe the sink</code></div>
<div class="line number21 index20 alt2"><code class="bash plain">a1.sinks.k1.</code><code class="bash functions">type</code><code class="bash plain">= avro</code></div>
<div class="line number22 index21 alt1"><code class="bash plain">a1.sinks.k1.channel = c1</code></div>
<div class="line number23 index22 alt2"><code class="bash plain">a1.sinks.k1.</code><code class="bash functions">hostname</code><code class="bash plain">= m1</code></div>
<div class="line number24 index23 alt1"><code class="bash plain">a1.sinks.k1.port = 5555</code></div>
<div class="line number25 index24 alt2"><code class="bash spaces"> </code> </div>
<div class="line number26 index25 alt1"><code class="bash plain">a1.sinks.k2.</code><code class="bash functions">type</code><code class="bash plain">= avro</code></div>
<div class="line number27 index26 alt2"><code class="bash plain">a1.sinks.k2.channel = c1</code></div>
<div class="line number28 index27 alt1"><code class="bash plain">a1.sinks.k2.</code><code class="bash functions">hostname</code><code class="bash plain">= m2</code></div>
<div class="line number29 index28 alt2"><code class="bash plain">a1.sinks.k2.port = 5555</code></div>
<div class="line number30 index29 alt1"><code class="bash spaces"> </code> </div>
<div class="line number31 index30 alt2"><code class="bash comments"># Use a channel which buffers events in memory</code></div>
<div class="line number32 index31 alt1"><code class="bash plain">a1.channels.c1.</code><code class="bash functions">type</code><code class="bash plain">= memory</code></div>
<div class="line number33 index32 alt2"><code class="bash plain">a1.channels.c1.capacity = 1000</code></div>
<div class="line number34 index33 alt1"><code class="bash plain">a1.channels.c1.transactionCapacity = 100</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　b)在m1创建Load_balancing_Sink_Processors_avro配置文件</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_808509">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
<div class="line number10 index9 alt1">10</div>
<div class="line number11 index10 alt2">11</div>
<div class="line number12 index11 alt1">12</div>
<div class="line number13 index12 alt2">13</div>
<div class="line number14 index13 alt1">14</div>
<div class="line number15 index14 alt2">15</div>
<div class="line number16 index15 alt1">16</div>
<div class="line number17 index16 alt2">17</div>
<div class="line number18 index17 alt1">18</div>
<div class="line number19 index18 alt2">19</div>
<div class="line number20 index19 alt1">20</div>
<div class="line number21 index20 alt2">21</div>
<div class="line number22 index21 alt1">22</div>
<div class="line number23 index22 alt2">23</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># vi /home/hadoop/flume-1.5.0-bin/conf/Load_balancing_Sink_Processors_avro.conf</code></div>
<div class="line number2 index1 alt1"><code class="bash spaces"> </code> </div>
<div class="line number3 index2 alt2"><code class="bash plain">a1.sources = r1</code></div>
<div class="line number4 index3 alt1"><code class="bash plain">a1.sinks = k1</code></div>
<div class="line number5 index4 alt2"><code class="bash plain">a1.channels = c1</code></div>
<div class="line number6 index5 alt1"><code class="bash spaces"> </code> </div>
<div class="line number7 index6 alt2"><code class="bash comments"># Describe/configure the source</code></div>
<div class="line number8 index7 alt1"><code class="bash plain">a1.sources.r1.</code><code class="bash functions">type</code><code class="bash plain">= avro</code></div>
<div class="line number9 index8 alt2"><code class="bash plain">a1.sources.r1.channels = c1</code></div>
<div class="line number10 index9 alt1"><code class="bash plain">a1.sources.r1.bind = 0.0.0.0</code></div>
<div class="line number11 index10 alt2"><code class="bash plain">a1.sources.r1.port = 5555</code></div>
<div class="line number12 index11 alt1"><code class="bash spaces"> </code> </div>
<div class="line number13 index12 alt2"><code class="bash comments"># Describe the sink</code></div>
<div class="line number14 index13 alt1"><code class="bash plain">a1.sinks.k1.</code><code class="bash functions">type</code><code class="bash plain">= logger</code></div>
<div class="line number15 index14 alt2"><code class="bash spaces"> </code> </div>
<div class="line number16 index15 alt1"><code class="bash comments"># Use a channel which buffers events in memory</code></div>
<div class="line number17 index16 alt2"><code class="bash plain">a1.channels.c1.</code><code class="bash functions">type</code><code class="bash plain">= memory</code></div>
<div class="line number18 index17 alt1"><code class="bash plain">a1.channels.c1.capacity = 1000</code></div>
<div class="line number19 index18 alt2"><code class="bash plain">a1.channels.c1.transactionCapacity = 100</code></div>
<div class="line number20 index19 alt1"><code class="bash spaces"> </code> </div>
<div class="line number21 index20 alt2"><code class="bash comments"># Bind the source and sink to the channel</code></div>
<div class="line number22 index21 alt1"><code class="bash plain">a1.sources.r1.channels = c1</code></div>
<div class="line number23 index22 alt2"><code class="bash plain">a1.sinks.k1.channel = c1</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　c)将2个配置文件复制到m2上一份</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_821851">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop/flume-1</code><code class="bash plain">.5.0-bin</code><code class="bash comments"># scp -r /home/hadoop/flume-1.5.0-bin/conf/Load_balancing_Sink_Processors.conf
 root@m2:/home/hadoop/flume-1.5.0-bin/conf/Load_balancing_Sink_Processors.conf</code></div>
<div class="line number2 index1 alt1"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop/flume-1</code><code class="bash plain">.5.0-bin</code><code class="bash comments"># scp -r /home/hadoop/flume-1.5.0-bin/conf/Load_balancing_Sink_Processors_avro.conf
 root@m2:/home/hadoop/flume-1.5.0-bin/conf/Load_balancing_Sink_Processors_avro.conf</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　d)打开4个窗口，在m1和m2上同时启动两个flume agent</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_340446">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># /home/hadoop/flume-1.5.0-bin/bin/flume-ng agent -c . -f /home/hadoop/flume-1.5.0-bin/conf/Load_balancing_Sink_Processors_avro.conf
 -n a1 -Dflume.root.logger=INFO,console</code></div>
<div class="line number2 index1 alt1"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># /home/hadoop/flume-1.5.0-bin/bin/flume-ng agent -c . -f /home/hadoop/flume-1.5.0-bin/conf/Load_balancing_Sink_Processors.conf
 -n a1 -Dflume.root.logger=INFO,console</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　e)然后在m1或m2的任意一台机器上，测试产生log，一行一行输入，输入太快，容易落到一台机器上</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_157352">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># echo "idoall.org test1" | nc localhost 5140</code></div>
<div class="line number2 index1 alt1"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># echo "idoall.org test2" | nc localhost 5140</code></div>
<div class="line number3 index2 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># echo "idoall.org test3" | nc localhost 5140</code></div>
<div class="line number4 index3 alt1"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># echo "idoall.org test4" | nc localhost 5140</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　f)在m1的sink窗口，可以看到以下信息：</p>
<div class="jb51code">
<div class="syntaxhighlighter  plain" id="highlighter_414506">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="plain plain">14/08/10 15:35:29 INFO sink.LoggerSink: Event: { headers:{Severity=0, flume.syslog.status=Invalid, Facility=0} body: 69 64 6F 61 6C 6C 2E 6F 72 67 20 74 65 73 74 32 idoall.org test2 }</code></div>
<div class="line number2 index1 alt1"><code class="plain plain">14/08/10 15:35:33 INFO sink.LoggerSink: Event: { headers:{Severity=0, flume.syslog.status=Invalid, Facility=0} body: 69 64 6F 61 6C 6C 2E 6F 72 67 20 74 65 73 74 34 idoall.org test4 }</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　g)在m2的sink窗口，可以看到以下信息：</p>
<div class="jb51code">
<div class="syntaxhighlighter  plain" id="highlighter_735805">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="plain plain">14/08/10 15:35:27 INFO sink.LoggerSink: Event: { headers:{Severity=0, flume.syslog.status=Invalid, Facility=0} body: 69 64 6F 61 6C 6C 2E 6F 72 67 20 74 65 73 74 31 idoall.org test1 }</code></div>
<div class="line number2 index1 alt1"><code class="plain plain">14/08/10 15:35:29 INFO sink.LoggerSink: Event: { headers:{Severity=0, flume.syslog.status=Invalid, Facility=0} body: 69 64 6F 61 6C 6C 2E 6F 72 67 20 74 65 73 74 33 idoall.org test3 }</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　说明轮询模式起到了作用。<br>
 <br>
　　　　12)案例12：Hbase sink<br>
 <br>
　　　　　　a)在测试之前，请先参考《ubuntu12.04+hadoop2.2.0+zookeeper3.4.5+hbase0.96.2+hive0.13.1分布式环境部署》将hbase启动<br>
 <br>
　　　　　　b)然后将以下文件复制到flume中：</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_144747">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash functions">cp</code> <code class="bash plain">
/home/hadoop/hbase-0</code><code class="bash plain">.96.2-hadoop2</code><code class="bash plain">/lib/protobuf-java-2</code><code class="bash plain">.5.0.jar</code><code class="bash plain">/home/hadoop/flume-1</code><code class="bash plain">.5.0-bin</code><code class="bash plain">/lib</code></div>
<div class="line number2 index1 alt1"><code class="bash functions">cp</code> <code class="bash plain">
/home/hadoop/hbase-0</code><code class="bash plain">.96.2-hadoop2</code><code class="bash plain">/lib/hbase-client-0</code><code class="bash plain">.96.2-hadoop2.jar</code><code class="bash plain">/home/hadoop/flume-1</code><code class="bash plain">.5.0-bin</code><code class="bash plain">/lib</code></div>
<div class="line number3 index2 alt2"><code class="bash functions">cp</code> <code class="bash plain">
/home/hadoop/hbase-0</code><code class="bash plain">.96.2-hadoop2</code><code class="bash plain">/lib/hbase-common-0</code><code class="bash plain">.96.2-hadoop2.jar</code><code class="bash plain">/home/hadoop/flume-1</code><code class="bash plain">.5.0-bin</code><code class="bash plain">/lib</code></div>
<div class="line number4 index3 alt1"><code class="bash functions">cp</code> <code class="bash plain">
/home/hadoop/hbase-0</code><code class="bash plain">.96.2-hadoop2</code><code class="bash plain">/lib/hbase-protocol-0</code><code class="bash plain">.96.2-hadoop2.jar</code><code class="bash plain">/home/hadoop/flume-1</code><code class="bash plain">.5.0-bin</code><code class="bash plain">/lib</code></div>
<div class="line number5 index4 alt2"><code class="bash functions">cp</code> <code class="bash plain">
/home/hadoop/hbase-0</code><code class="bash plain">.96.2-hadoop2</code><code class="bash plain">/lib/hbase-server-0</code><code class="bash plain">.96.2-hadoop2.jar</code><code class="bash plain">/home/hadoop/flume-1</code><code class="bash plain">.5.0-bin</code><code class="bash plain">/lib</code></div>
<div class="line number6 index5 alt1"><code class="bash functions">cp</code> <code class="bash plain">
/home/hadoop/hbase-0</code><code class="bash plain">.96.2-hadoop2</code><code class="bash plain">/lib/hbase-hadoop2-compat-0</code><code class="bash plain">.96.2-hadoop2.jar</code><code class="bash plain">/home/hadoop/flume-1</code><code class="bash plain">.5.0-bin</code><code class="bash plain">/lib</code></div>
<div class="line number7 index6 alt2"><code class="bash functions">cp</code> <code class="bash plain">
/home/hadoop/hbase-0</code><code class="bash plain">.96.2-hadoop2</code><code class="bash plain">/lib/hbase-hadoop-compat-0</code><code class="bash plain">.96.2-hadoop2.jar</code><code class="bash plain">/home/hadoop/flume-1</code><code class="bash plain">.5.0-bin</code><code class="bash plain">/lib</code><code class="bash plain">@@@</code></div>
<div class="line number8 index7 alt1"><code class="bash functions">cp</code> <code class="bash plain">
/home/hadoop/hbase-0</code><code class="bash plain">.96.2-hadoop2</code><code class="bash plain">/lib/htrace-core-2</code><code class="bash plain">.04.jar</code><code class="bash plain">/home/hadoop/flume-1</code><code class="bash plain">.5.0-bin</code><code class="bash plain">/lib</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　c)确保test_idoall_org表在hbase中已经存在，test_idoall_org表的格式以及字段请参考《ubuntu12.04+hadoop2.2.0+zookeeper3.4.5+hbase0.96.2+hive0.13.1分布式环境部署》中关于hbase部分的建表代码。<br>
 <br>
　　　　　　d)在m1创建hbase_simple配置文件</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_882133">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
<div class="line number10 index9 alt1">10</div>
<div class="line number11 index10 alt2">11</div>
<div class="line number12 index11 alt1">12</div>
<div class="line number13 index12 alt2">13</div>
<div class="line number14 index13 alt1">14</div>
<div class="line number15 index14 alt2">15</div>
<div class="line number16 index15 alt1">16</div>
<div class="line number17 index16 alt2">17</div>
<div class="line number18 index17 alt1">18</div>
<div class="line number19 index18 alt2">19</div>
<div class="line number20 index19 alt1">20</div>
<div class="line number21 index20 alt2">21</div>
<div class="line number22 index21 alt1">22</div>
<div class="line number23 index22 alt2">23</div>
<div class="line number24 index23 alt1">24</div>
<div class="line number25 index24 alt2">25</div>
<div class="line number26 index25 alt1">26</div>
<div class="line number27 index26 alt2">27</div>
<div class="line number28 index27 alt1">28</div>
<div class="line number29 index28 alt2">29</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># vi /home/hadoop/flume-1.5.0-bin/conf/hbase_simple.conf</code></div>
<div class="line number2 index1 alt1"><code class="bash spaces"> </code> </div>
<div class="line number3 index2 alt2"><code class="bash plain">a1.sources = r1</code></div>
<div class="line number4 index3 alt1"><code class="bash plain">a1.sinks = k1</code></div>
<div class="line number5 index4 alt2"><code class="bash plain">a1.channels = c1</code></div>
<div class="line number6 index5 alt1"><code class="bash spaces"> </code> </div>
<div class="line number7 index6 alt2"><code class="bash comments"># Describe/configure the source</code></div>
<div class="line number8 index7 alt1"><code class="bash plain">a1.sources.r1.</code><code class="bash functions">type</code><code class="bash plain">= syslogtcp</code></div>
<div class="line number9 index8 alt2"><code class="bash plain">a1.sources.r1.port = 5140</code></div>
<div class="line number10 index9 alt1"><code class="bash plain">a1.sources.r1.host = localhost</code></div>
<div class="line number11 index10 alt2"><code class="bash plain">a1.sources.r1.channels = c1</code></div>
<div class="line number12 index11 alt1"><code class="bash spaces"> </code> </div>
<div class="line number13 index12 alt2"><code class="bash comments"># Describe the sink</code></div>
<div class="line number14 index13 alt1"><code class="bash plain">a1.sinks.k1.</code><code class="bash functions">type</code><code class="bash plain">= logger</code></div>
<div class="line number15 index14 alt2"><code class="bash plain">a1.sinks.k1.</code><code class="bash functions">type</code><code class="bash plain">= hbase</code></div>
<div class="line number16 index15 alt1"><code class="bash plain">a1.sinks.k1.table = test_idoall_org</code></div>
<div class="line number17 index16 alt2"><code class="bash plain">a1.sinks.k1.columnFamily = name</code></div>
<div class="line number18 index17 alt1"><code class="bash plain">a1.sinks.k1.column = idoall</code></div>
<div class="line number19 index18 alt2"><code class="bash plain">a1.sinks.k1.serializer = org.apache.flume.sink.hbase.RegexHbaseEventSerializer</code></div>
<div class="line number20 index19 alt1"><code class="bash plain">a1.sinks.k1.channel = memoryChannel</code></div>
<div class="line number21 index20 alt2"><code class="bash spaces"> </code> </div>
<div class="line number22 index21 alt1"><code class="bash comments"># Use a channel which buffers events in memory</code></div>
<div class="line number23 index22 alt2"><code class="bash plain">a1.channels.c1.</code><code class="bash functions">type</code><code class="bash plain">= memory</code></div>
<div class="line number24 index23 alt1"><code class="bash plain">a1.channels.c1.capacity = 1000</code></div>
<div class="line number25 index24 alt2"><code class="bash plain">a1.channels.c1.transactionCapacity = 100</code></div>
<div class="line number26 index25 alt1"><code class="bash spaces"> </code> </div>
<div class="line number27 index26 alt2"><code class="bash comments"># Bind the source and sink to the channel</code></div>
<div class="line number28 index27 alt1"><code class="bash plain">a1.sources.r1.channels = c1</code></div>
<div class="line number29 index28 alt2"><code class="bash plain">a1.sinks.k1.channel = c1</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　e)启动flume agent</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_386838">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">/home/hadoop/flume-1</code><code class="bash plain">.5.0-bin</code><code class="bash plain">/bin/flume-ng</code><code class="bash plain">agent -c . -f</code><code class="bash plain">/home/hadoop/flume-1</code><code class="bash plain">.5.0-bin</code><code class="bash plain">/conf/hbase_simple</code><code class="bash plain">.conf
 -n a1 -Dflume.root.logger=INFO,console</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　f)测试产生syslog</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_246518">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># echo "hello idoall.org from flume" | nc localhost 5140</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　　　g)这时登录到hbase中，可以发现新数据已经插入</p>
<div class="jb51code">
<div class="syntaxhighlighter  bash" id="highlighter_484118">
<div class="toolbar"><a target="_blank" class="toolbar_item command_help help" href="http://www.jb51.net/article/53542.htm#" rel="nofollow">?</a></div>
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
<div class="line number10 index9 alt1">10</div>
<div class="line number11 index10 alt2">11</div>
<div class="line number12 index11 alt1">12</div>
<div class="line number13 index12 alt2">13</div>
<div class="line number14 index13 alt1">14</div>
<div class="line number15 index14 alt2">15</div>
<div class="line number16 index15 alt1">16</div>
<div class="line number17 index16 alt2">17</div>
<div class="line number18 index17 alt1">18</div>
<div class="line number19 index18 alt2">19</div>
<div class="line number20 index19 alt1">20</div>
<div class="line number21 index20 alt2">21</div>
<div class="line number22 index21 alt1">22</div>
<div class="line number23 index22 alt2">23</div>
<div class="line number24 index23 alt1">24</div>
<div class="line number25 index24 alt2">25</div>
<div class="line number26 index25 alt1">26</div>
<div class="line number27 index26 alt2">27</div>
<div class="line number28 index27 alt1">28</div>
<div class="line number29 index28 alt2">29</div>
<div class="line number30 index29 alt1">30</div>
</td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="bash plain">root@m1:</code><code class="bash plain">/home/hadoop</code><code class="bash comments"># /home/hadoop/hbase-0.96.2-hadoop2/bin/hbase shell</code></div>
<div class="line number2 index1 alt1"><code class="bash plain">2014-08-10 16:09:48,984 INFO [main] Configuration.deprecation: hadoop.native.lib is deprecated. Instead, use io.native.lib.available</code></div>
<div class="line number3 index2 alt2"><code class="bash plain">HBase Shell; enter</code><code class="bash string">'help&lt;RETURN&gt;'</code><code class="bash keyword">for</code><code class="bash plain">list of supported commands.</code></div>
<div class="line number4 index3 alt1"><code class="bash plain">Type </code><code class="bash string">"exit&lt;RETURN&gt;"</code><code class="bash plain">to leave the HBase Shell</code></div>
<div class="line number5 index4 alt2"><code class="bash plain">Version 0.96.2-hadoop2, r1581096, Mon Mar 24 16:03:18 PDT 2014</code></div>
<div class="line number6 index5 alt1"><code class="bash spaces"> </code> </div>
<div class="line number7 index6 alt2"><code class="bash plain">hbase(main):001:0&gt; list</code></div>
<div class="line number8 index7 alt1"><code class="bash plain">TABLE                                                                                                        </code></div>
<div class="line number9 index8 alt2"><code class="bash plain">SLF4J: Class path contains multiple SLF4J bindings.</code></div>
<div class="line number10 index9 alt1"><code class="bash plain">SLF4J: Found binding</code><code class="bash keyword">in</code><code class="bash plain">[jar:</code><code class="bash functions">file</code><code class="bash plain">:</code><code class="bash plain">/home/hadoop/hbase-0</code><code class="bash plain">.96.2-hadoop2</code><code class="bash plain">/lib/slf4j-log4j12-1</code><code class="bash plain">.6.4.jar!</code><code class="bash plain">/org/slf4j/impl/StaticLoggerBinder</code><code class="bash plain">.class]</code></div>
<div class="line number11 index10 alt2"><code class="bash plain">SLF4J: Found binding</code><code class="bash keyword">in</code><code class="bash plain">[jar:</code><code class="bash functions">file</code><code class="bash plain">:</code><code class="bash plain">/home/hadoop/hadoop-2</code><code class="bash plain">.2.0</code><code class="bash plain">/share/hadoop/common/lib/slf4j-log4j12-1</code><code class="bash plain">.7.5.jar!</code><code class="bash plain">/org/slf4j/impl/StaticLoggerBinder</code><code class="bash plain">.class]</code></div>
<div class="line number12 index11 alt1"><code class="bash plain">SLF4J: See http:</code><code class="bash plain">//www</code><code class="bash plain">.slf4j.org</code><code class="bash plain">/codes</code><code class="bash plain">.html</code><code class="bash comments">#multiple_bindings
 for an explanation.</code></div>
<div class="line number13 index12 alt2"><code class="bash plain">hbase2hive_idoall                                                                                                  </code></div>
<div class="line number14 index13 alt1"><code class="bash plain">hive2hbase_idoall                                                                                                  </code></div>
<div class="line number15 index14 alt2"><code class="bash plain">test_idoall_org                                                                                                   </code></div>
<div class="line number16 index15 alt1"><code class="bash plain">3 row(s) </code>
<code class="bash keyword">in</code> <code class="bash plain">2.6880 seconds</code></div>
<div class="line number17 index16 alt2"><code class="bash spaces"> </code> </div>
<div class="line number18 index17 alt1"><code class="bash plain">=&gt; [</code><code class="bash string">"hbase2hive_idoall"</code><code class="bash plain">,</code><code class="bash string">"hive2hbase_idoall"</code><code class="bash plain">,</code><code class="bash string">"test_idoall_org"</code><code class="bash plain">]</code></div>
<div class="line number19 index18 alt2"><code class="bash plain">hbase(main):002:0&gt; scan</code><code class="bash string">"test_idoall_org"</code></div>
<div class="line number20 index19 alt1"><code class="bash plain">ROW                          COLUMN+CELL                                                                          </code></div>
<div class="line number21 index20 alt2"><code class="bash spaces"> </code><code class="bash plain">10086                         column=name:idoall, timestamp=1406424831473, value=idoallvalue                                                </code></div>
<div class="line number22 index21 alt1"><code class="bash plain">1 row(s) </code>
<code class="bash keyword">in</code> <code class="bash plain">0.0550 seconds</code></div>
<div class="line number23 index22 alt2"><code class="bash spaces"> </code> </div>
<div class="line number24 index23 alt1"><code class="bash plain">hbase(main):003:0&gt; scan</code><code class="bash string">"test_idoall_org"</code></div>
<div class="line number25 index24 alt2"><code class="bash plain">ROW                          COLUMN+CELL                                                                          </code></div>
<div class="line number26 index25 alt1"><code class="bash spaces"> </code><code class="bash plain">10086                         column=name:idoall, timestamp=1406424831473, value=idoallvalue                                                </code></div>
<div class="line number27 index26 alt2"><code class="bash spaces"> </code><code class="bash plain">1407658495588-XbQCOZrKK8-0              column=name:payload, timestamp=1407658498203, value=hello idoall.org from flume                                        </code></div>
<div class="line number28 index27 alt1"><code class="bash plain">2 row(s) </code>
<code class="bash keyword">in</code> <code class="bash plain">0.0200 seconds</code></div>
<div class="line number29 index28 alt2"><code class="bash spaces"> </code> </div>
<div class="line number30 index29 alt1"><code class="bash plain">hbase(main):004:0&gt; quit</code></div>
</div>
</td>
</tr>
</tbody>
</table>
</div>
</div>
<p>　　　　经过这么多flume的例子测试，如果你全部做完后，会发现flume的功能真的很强大，可以进行各种搭配来完成你想要的工作，俗话说师傅领进门，修行在个人，如何能够结合你的产品业务，将flume更好的应用起来，快去动手实践吧。<br>
 </p>
<p><br>
</p>
<p><br>
</p>
<p>原文来自：http://www.jb51.net/article/53542.htm</p>
<br>
            </div>
                </div>