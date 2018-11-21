---
layout:     post
title:      Spark Streaming实时流处理项目实战笔记
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-tomorrow-night">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2><a id="_Flume_0"></a>第二章 分布式日志收集框架Flume</h2>
<p><strong>课程目录</strong><br>
业务现状分析=&gt;flume概述=&gt;flume架构及核心组件=&gt;flume环境部署=&gt;flume实战</p>
<p><strong>1、业务现状分析</strong></p>
<ul>
<li>WebServer/ApplicationServer分散在各个机器上</li>
<li>大数据平台Hadoop进行统计分析</li>
<li>日志如何收集到Hadoop平台上</li>
<li>解决方案及存在问题</li>
</ul>
<p>传统从Server到Hadoop处理上存在的问题<br>
1.难以监控<br>
2.IO的读写开销大<br>
3.容错率高，负载均衡差<br>
4.高延时，需隔一段时间启动</p>
<p><strong>2、flume概述</strong></p>
<p>flume官网：<a href="http://flume.apache.org/" rel="nofollow">http://flume.apache.org/</a></p>
<p>Flume is a distributed（分布式的）, reliable（高可靠的）, and available service（高可用的服务） for efficiently collecting（海量收集）, aggregating（聚合）, and moving（移动系统） large amounts of log data.<br>
<strong>Flume是由Cloudera提供的一个分布式、高可靠、高可用的服务，用于分布式的海量日志的高效收集、聚合、移动系统</strong></p>
<p><strong>设计目标</strong><br>
可靠性<br>
扩展性<br>
管理性</p>
<p><strong>业界同类产品的对比</strong><br>
<strong>Flume</strong>： Cloudera/Apache Java<br>
Scribe： Facebook C/C++ 不再维护<br>
Chukwa： Yahoo/Apache Java 不再维护<br>
Kafka：<br>
Fluentd： Ruby<br>
<strong>Logstash</strong>: ELK(ElasticSearch,Kibana)</p>
<p><strong>Flume发展史</strong><br>
Cloudera 0.9.2 Flume-OG<br>
flume-728 Flume-NG ==&gt; Apache<br>
2012.7 1.0<br>
2015.5 1.6<br>
~ 1.7</p>
<p><strong>Flume架构及核心组件</strong></p>
<ol>
<li>Source 收集</li>
<li>Channel 聚集</li>
<li>Sink 输出<br>
<img alt="在这里插入图片描述" src="https://img-blog.csdnimg.cn/20181112131418846.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L215c19teXM=,size_16,color_FFFFFF,t_70"><br>
<strong>Flume安装前置条件</strong><br>
1.Java Runtime Environment - Java 1.8 or later<br>
2.Memory - Sufficient memory for configurations used by sources, channels or sinks<br>
3.Disk Space - Sufficient disk space for configurations used by channels or sinks<br>
4.Directory Permissions - Read/Write permissions for directories used by agent</li>
</ol>
<p><strong>安装jdk</strong><br>
下载<br>
解压到~/app<br>
将java配置系统环境变量中: vi ~/.bash_profile<br>
export JAVA_HOME=/home/hadoop/app/jdk1.8.0_144<br>
export PATH=<span class="katex--inline"><span class="katex"><span class="katex-mathml"><math><semantics><mrow><mi>J</mi><mi>A</mi><mi>V</mi><msub><mi>A</mi><mi>H</mi></msub><mi>O</mi><mi>M</mi><mi>E</mi><mi mathvariant="normal">/</mi><mi>b</mi><mi>i</mi><mi>n</mi><mo>:</mo></mrow><annotation encoding="application/x-tex">JAVA_HOME/bin:</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height: 1em; vertical-align: -0.25em;"></span><span class="mord mathit" style="margin-right: 0.09em;">J</span><span class="mord mathit">A</span><span class="mord mathit" style="margin-right: 0.22em;">V</span><span class="mord"><span class="mord mathit">A</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height: 0.32em;"><span style="top: -2.55em; margin-right: 0.05em; margin-left: 0em;"><span class="pstrut" style="height: 2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mathit mtight" style="margin-right: 0.08em;">H</span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height: 0.15em;"><span></span></span></span></span></span></span><span class="mord mathit" style="margin-right: 0.02em;">O</span><span class="mord mathit" style="margin-right: 0.1em;">M</span><span class="mord mathit" style="margin-right: 0.05em;">E</span><span class="mord">/</span><span class="mord mathit">b</span><span class="mord mathit">i</span><span class="mord mathit">n</span><span class="mspace" style="margin-right: 0.27em;"></span><span class="mrel">:</span></span></span></span></span>PATH<br>
source下让其配置生效：source ~/.bash_profile<br>
检测: java -version</p>
<p><strong>安装Flume</strong><br>
下载<br>
解压到~/app<br>
将java配置系统环境变量中: vi ~/.bash_profile<br>
export FLUME_HOME=/home/hadoop/app/apache-flume-1.6.0-cdh5.7.0-bin<br>
export PATH=<span class="katex--inline"><span class="katex"><span class="katex-mathml"><math><semantics><mrow><mi>F</mi><mi>L</mi><mi>U</mi><mi>M</mi><msub><mi>E</mi><mi>H</mi></msub><mi>O</mi><mi>M</mi><mi>E</mi><mi mathvariant="normal">/</mi><mi>b</mi><mi>i</mi><mi>n</mi><mo>:</mo></mrow><annotation encoding="application/x-tex">FLUME_HOME/bin:</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height: 1em; vertical-align: -0.25em;"></span><span class="mord mathit" style="margin-right: 0.13em;">F</span><span class="mord mathit">L</span><span class="mord mathit" style="margin-right: 0.1em;">U</span><span class="mord mathit" style="margin-right: 0.1em;">M</span><span class="mord"><span class="mord mathit" style="margin-right: 0.05em;">E</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height: 0.32em;"><span style="top: -2.55em; margin-right: 0.05em; margin-left: -0.05em;"><span class="pstrut" style="height: 2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mathit mtight" style="margin-right: 0.08em;">H</span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height: 0.15em;"><span></span></span></span></span></span></span><span class="mord mathit" style="margin-right: 0.02em;">O</span><span class="mord mathit" style="margin-right: 0.1em;">M</span><span class="mord mathit" style="margin-right: 0.05em;">E</span><span class="mord">/</span><span class="mord mathit">b</span><span class="mord mathit">i</span><span class="mord mathit">n</span><span class="mspace" style="margin-right: 0.27em;"></span><span class="mrel">:</span></span></span></span></span>PATH<br>
source下让其配置生效 ：source ~/.bash_profile<br>
flume-env.sh的配置：export JAVA_HOME=/home/hadoop/app/jdk1.8.0_144<br>
检测: flume-ng version</p>
<p><strong>Flume架构及核心组件</strong></p>
<h1><a id="Flume_77"></a>Flume实战：</h1>
<h2><a id="_78"></a>需求一：从指定网络端口采集数据输出到控制台</h2>
<p><img alt="在这里插入图片描述" src="https://img-blog.csdnimg.cn/20181112131649940.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L215c19teXM=,size_16,color_FFFFFF,t_70"></p>
<p><strong>使用Flume的关键就是写配置文件</strong><br>
A） 配置Source<br>
B） 配置Channel<br>
C） 配置Sink<br>
D） 把以上三个组件串起来</p>
<p>a1: agent名称<br>
r1: source的名称<br>
k1: sink的名称<br>
c1: channel的名称</p>
<pre><code># example.conf: A single-node Flume configuration

# Name the components on this agent
a1.sources = r1
a1.sinks = k1
a1.channels = c1

# Describe/configure the source
a1.sources.r1.type = netcat
a1.sources.r1.bind = hadoop000
a1.sources.r1.port = 44444

# Describe the sink
a1.sinks.k1.type = logger

# Use a channel which buffers events in memory
a1.channels.c1.type = memory

# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
</code></pre>
<p><strong>查询官网文档</strong><br>
<a href="http://flume.apache.org/FlumeUserGuide.html#avro-legacy-source" rel="nofollow">http://flume.apache.org/FlumeUserGuide.html#avro-legacy-source</a></p>
<pre><code> a1.sources.r1.type = netcat
    a1.sources.r1.bind = hadoop000
    a1.sources.r1.port = 44444
</code></pre>
<p>type：The component type name, needs to be org.apache.flume.source.avroLegacy.AvroLegacySource<br>
host：The hostname or IP address to bind to<br>
port：The port # to listen on</p>
<pre><code>a1.sinks.k1.type = logger
</code></pre>
<p>type：The component type name, needs to be logger</p>
<pre><code>a1.channels.c1.type = memory
</code></pre>
<p>type:The component type name, needs to be memory</p>
<pre><code>a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
</code></pre>
<p>注意：一个source可以输出到多个channel，因此上面是channels；而一此只能从channel输出一个到sink,因此下面是channel</p>
<p><strong>步骤：</strong><br>
1.写配置文件<br>
在conf目录下：vi example.conf<br>
将上面代码写入其中<br>
2.启动agent</p>
<pre><code>flume-ng agent \
--name a1 \
--conf $FLUME_HOME/conf \
--conf-file $FLUME_HOME/conf/example.conf \
-Dflume.root.logger=INFO,console
</code></pre>
<p>3.使用telnet进行测试： <code>telnet hadoop000 44444</code></p>
<h2><a id="_152"></a>需求二：监控一个文件实时采集新增的数据输出到控制台</h2>
<pre><code># example.conf: A single-node Flume configuration

# Name the components on this agent
a1.sources = r1
a1.sinks = k1
a1.channels = c1

# Describe/configure the source
a1.sources.r1.type = exec
a1.sources.r1.command = tail -F /home/hadoop/data/data.log
a1.sources.r1.shell = /bin/sh -c

# Describe the sink
a1.sinks.k1.type = logger

# Use a channel which buffers events in memory
a1.channels.c1.type = memory

# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
</code></pre>
<p><strong>channels</strong><br>
type:The component type name, needs to be <strong>exec</strong><br>
command:The command to execute<br>
shell:A shell invocation used to run the command. e.g. <strong>/bin/sh -c</strong>. Required only for commands relying on shell features like wildcards, back ticks, pipes etc.</p>
<p><strong>步骤：</strong><br>
1.写配置文件<br>
在conf目录下：vi exec-memory-logger.conf<br>
将上面代码写入其中<br>
2.启动agent</p>
<pre><code>flume-ng agent \
--name a1 \
--conf $FLUME_HOME/conf \
--conf-file $FLUME_HOME/conf/exec-memory-logger.conf \
-Dflume.root.logger=INFO,console
</code></pre>
<p>3.测试：</p>
<p>新打开一个窗口：输入以下内容</p>
<pre><code>  [hadoop@hadoop001 data]$ echo hello &gt;&gt; data.log 
    [hadoop@hadoop001 data]$ echo world &gt;&gt; data.log 
    [hadoop@hadoop001 data]$ echo welcome &gt;&gt; data.log 
</code></pre>
<p>原窗口会出现以下变化：</p>
<pre><code>Event: { headers:{} body: 68 65 6C 6C 6F  				hello }
Event: { headers:{} body: 77 6F 72 6C 64  				world }
Event: { headers:{} body: 77 65 6C 63 6F 6D 65			welcome }
</code></pre>
<h2><a id="AB_209"></a>需求三：将A服务器上的日志实时采集到B服务器端</h2>
<p>技术选项：</p>
<pre><code>exec source + memory channel + avro sink
avro source + memory channel + logger sink
</code></pre>
<p>两个配置文件:</p>
<p><strong>exec-memory-avro.conf</strong></p>
<pre><code># Name the components on this agent
exec-memory-avro.sources = exec-source
exec-memory-avro.sinks = avro-sink
exec-memory-avro.channels = memory-channel

# Describe/configure the source
exec-memory-avro.sources.exec-source.type = exec
exec-memory-avro.sources.exec-source.command = tail -F /home/hadoop/data/data.log
exec-memory-avro.sources.exec-source.shell = /bin/sh -c

# Describe the sink
exec-memory-avro.sinks.avro-sink.type = avro
exec-memory-avro.sinks.avro-sink.bind = hadoo000
exec-memory-avro.sinks.avro-sink.port = 4444

# Use a channel which buffers events in memory
exec-memory-avro.channels.exec-source.type = memory

# Bind the source and sink to the channel
exec-memory-avro.sources.exec-source.channels = memory-channel
exec-memory-avro.sinks.avro-sink.channel = memory-channel
</code></pre>
<p><strong>avro-memory-logger.conf</strong></p>
<pre><code># Name the components on this agent
avro-memory-logger.sources = avro source
avro-memory-logger.sinks = logger sink
avro-memory-logger.channels = memory-channel

# Describe/configure the source
avro-memory-logger.sources.avro source.type = avro
avro-memory-logger.sources.avro source.bind = hadoop000
avro-memory-logger.sources.avro source.port = 44444 

# Describe the sink
avro-memory-logger.logger sink.type = logger

# Use a channel which buffers events in memory
avro-memory-logger.channels.avro source.type = memory

# Bind the source and sink to the channel
avro-memory-logger.sources.avro source.channels = memory-channel
avro-memory-logger.sinks.logger sink.channel = memory-channel
</code></pre>
<p>先启动avro-memory-logger</p>
<pre><code>  flume-ng agent \
    --name avro-memory-logger \
    --conf $FLUME_HOME/conf \
    --conf-file $FLUME_HOME/conf/avro-memory-logger.conf \
    -Dflume.root.logger=INFO,console
</code></pre>
<p>再启动exec-memory-avro</p>
<pre><code> flume-ng agent \
    --name exec-memory-avro \
    --conf $FLUME_HOME/conf \
    --conf-file $FLUME_HOME/conf/exec-memory-avro.conf \
    -Dflume.root.logger=INFO,console
</code></pre>
<p>测试：<br>
新打开一个窗口：输入以下内容</p>
<pre><code> [hadoop@hadoop001 data]$ echo hello spark &gt;&gt; data.log 
 [hadoop@hadoop001 data]$ echo hello hadoop &gt;&gt; data.log  
</code></pre>
<p>原窗口会出现以下变化：</p>
<pre><code>Event: { headers:{} body: 68 65 6C 6C 6F 20 73 70 61 72 6B                hello spark }
Event: { headers:{} body: 68 65 6C 6C 6F 20 68 61 64 6F 6F 70             hello hadoop }
</code></pre>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>