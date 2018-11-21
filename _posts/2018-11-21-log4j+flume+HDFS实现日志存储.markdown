---
layout:     post
title:      log4j+flume+HDFS实现日志存储
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>参考：<a href="http://blog.csdn.net/sum__mer/article/details/52474443" rel="nofollow">http://blog.csdn.net/sum__mer/article/details/52474443</a> <br>
 376  hadoop dfs -chown -R hadoop:hadoop  /flume <br>
  377  hdfs dfs -chown -R hadoop:hadoop  /flume <br>
  378  hdfs dfs -chown -R 777  /flume <br>
  379  hdfs dfs -chmod -R 777  /flume <br>
前言 <br>
在一个完整的大数据处理系统中，除了hdfs+mapreduce+hive组成分析系统的核心之外，还需要数据采集、结果数据导出、任务调度等不可或缺的辅助系统，而这些辅助工具在hadoop生态体系中都有便捷的开源框架，如图所示：</p>

<p><img src="https://img-blog.csdn.net/20170806170729632?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxNDAzMzIxOA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<ol>
<li>日志采集框架Flume <br>
1.1 Flume介绍 <br>
1.1.1 概述 <br>
官方文档：<a href="http://flume.apache.org/" rel="nofollow">http://flume.apache.org/</a> <br>
Flume is a distributed, reliable, and available service for efficiently collecting, aggregating, and moving large amounts of log data. It has a simple and flexible architecture based on streaming data flows. It is robust and fault tolerant with tunable reliability mechanisms and many failover and recovery mechanisms. It uses a simple extensible data model that allows for online analytic application.</li>
</ol>

<p>   Flume是一个分布式、可靠、和高可用的海量日志采集、聚合和传输的系统。 <br>
   Flume可以采集文件，socket数据包等各种形式源数据，又可以将采集到的数据输出到HDFS、hbase、hive、kafka等众多外部存储系统中 <br>
   一般的采集需求，通过对flume的简单配置即可实现 <br>
   Flume针对特殊场景也具备良好的自定义扩展能力，因此，flume可以适用于大部分的日常数据采集场景</p>

<p>1.1.2 运行机制 <br>
1、  Flume分布式系统中最核心的角色是agent，flume采集系统就是由一个个agent所连接起来形成 <br>
2、  每一个agent相当于一个数据传递员，内部有三个组件： <br>
a)  Source：采集源，用于跟数据源对接，以获取数据 <br>
b)  Sink：下沉地，采集数据的传送目的，用于往下一级agent传递数据或者往最终存储系统传递数据 <br>
c)  Channel：angent内部的数据传输通道，用于从source将数据传递到sink</p>

<p>  <br>
1.1.4 Flume采集系统结构图</p>

<ol>
<li><p>简单结构 <br>
单个agent采集数据</p></li>
<li><p>复杂结构 <br>
多级agent之间串联</p></li>
</ol>

<p><img src="https://img-blog.csdn.net/20170806170822398?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxNDAzMzIxOA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>1.2 Flume实战案例 <br>
1.2.1 Flume的安装部署 <br>
1、Flume的安装非常简单，只需要解压即可，当然，前提是已有hadoop环境 <br>
上传安装包到数据源所在节点上 <br>
然后解压  tar -zxvf apache-flume-1.6.0-bin.tar.gz <br>
然后进入flume的目录，修改conf下的flume-env.sh，在里面配置JAVA_HOME</p>

<p>2、根据数据采集的需求配置采集方案，描述在配置文件中(文件名可任意自定义) <br>
3、指定采集方案配置文件，在相应的节点上启动flume agent</p>

<p>先用一个最简单的例子来测试一下程序环境是否正常 <br>
1、先在flume的conf目录下新建一个文件 <br>
vi   netcat-logger.conf <br>
netcat又称nc工具-建立连接并返回两个数据流</p>

<h1 id="定义这个agent中各组件的名字">定义这个agent中各组件的名字</h1>

<p>a1.sources = r1 <br>
a1.sinks = k1 <br>
a1.channels = c1</p>



<h1 id="描述和配置source组件r1">描述和配置source组件：r1</h1>

<p>a1.sources.r1.type = netcat <br>
a1.sources.r1.bind = localhost <br>
a1.sources.r1.port = 44444</p>



<h1 id="描述和配置sink组件k1">描述和配置sink组件：k1</h1>

<p>a1.sinks.k1.type = logger</p>



<h1 id="描述和配置channel组件此处使用是内存缓存的方式">描述和配置channel组件，此处使用是内存缓存的方式</h1>

<p>a1.channels.c1.type = memory <br>
a1.channels.c1.capacity = 1000 <br>
a1.channels.c1.transactionCapacity = 100</p>



<h1 id="描述和配置source-channel-sink之间的连接关系">描述和配置source  channel   sink之间的连接关系</h1>

<p>a1.sources.r1.channels = c1 <br>
a1.sinks.k1.channel = c1</p>

<p>2、启动agent去采集数据 <br>
bin/flume-ng agent -c conf -f conf/netcat-logger.conf -n a1  -Dflume.root.logger=INFO,console <br>
-c conf   指定flume自身的配置文件所在目录 <br>
-f conf/netcat-logger.con  指定我们所描述的采集方案 <br>
-n a1  指定我们这个agent的名字 <br>
3、测试 <br>
先要往agent采集监听的端口上发送数据，让agent有数据可采 <br>
随便在一个能跟agent节点联网的机器上 <br>
telnet anget-hostname  port   （telnet localhost 44444） </p>

<p>1.2.2 采集案例 <br>
1、采集目录到HDFS <br>
采集需求：某服务器的某特定目录下，会不断产生新的文件，每当有新文件出现，就需要把文件采集到HDFS中去 <br>
根据需求，首先定义以下3大要素 <br>
   采集源，即source——监控文件目录 :  spooldir <br>
   下沉目标，即sink——HDFS文件系统  :  hdfs sink <br>
   source和sink之间的传递通道——channel，可用file channel 也可以用内存channel</p>

<p>配置文件编写：</p>



<h1 id="定义三大组件的名称">定义三大组件的名称</h1>

<p>agent1.sources = source1 <br>
agent1.sinks = sink1 <br>
agent1.channels = channel1</p>



<h1 id="配置source组件">配置source组件</h1>

<p>agent1.sources.source1.type = spooldir <br>
agent1.sources.source1.spoolDir = /home/hadoop/logs/ <br>
agent1.sources.source1.fileHeader = false</p>



<h1 id="配置拦截器">配置拦截器</h1>

<p>agent1.sources.source1.interceptors = i1 <br>
agent1.sources.source1.interceptors.i1.type = host <br>
agent1.sources.source1.interceptors.i1.hostHeader = hostname</p>



<h1 id="配置sink组件">配置sink组件</h1>

<p>agent1.sinks.sink1.type = hdfs <br>
agent1.sinks.sink1.hdfs.path =hdfs://hdp-node-01:9000/weblog/flume-collection/%y-%m-%d/%H-%M <br>
agent1.sinks.sink1.hdfs.filePrefix = access_log <br>
agent1.sinks.sink1.hdfs.maxOpenFiles = 5000 <br>
agent1.sinks.sink1.hdfs.batchSize= 100 <br>
agent1.sinks.sink1.hdfs.fileType = DataStream <br>
agent1.sinks.sink1.hdfs.writeFormat =Text <br>
agent1.sinks.sink1.hdfs.rollSize = 102400 <br>
agent1.sinks.sink1.hdfs.rollCount = 1000000 <br>
agent1.sinks.sink1.hdfs.rollInterval = 60</p>



<h1 id="agent1sinkssink1hdfsround-true">agent1.sinks.sink1.hdfs.round = true</h1>



<h1 id="agent1sinkssink1hdfsroundvalue-10">agent1.sinks.sink1.hdfs.roundValue = 10</h1>



<h1 id="agent1sinkssink1hdfsroundunit-minute">agent1.sinks.sink1.hdfs.roundUnit = minute</h1>

<p>agent1.sinks.sink1.hdfs.useLocalTimeStamp = true</p>



<h1 id="use-a-channel-which-buffers-events-in-memory">Use a channel which buffers events in memory</h1>

<p>agent1.channels.channel1.type = memory <br>
agent1.channels.channel1.keep-alive = 120 <br>
agent1.channels.channel1.capacity = 500000 <br>
agent1.channels.channel1.transactionCapacity = 600</p>



<h1 id="bind-the-source-and-sink-to-the-channel">Bind the source and sink to the channel</h1>

<p>agent1.sources.source1.channels = channel1 <br>
agent1.sinks.sink1.channel = channel1</p>

<p>Channel参数解释： <br>
capacity：默认该通道中最大的可以存储的event数量 <br>
trasactionCapacity：每次最大可以从source中拿到或者送到sink中的event数量 <br>
keep-alive：event添加到通道中或者移出的允许时间</p>

<p>2、采集文件到HDFS <br>
采集需求：比如业务系统使用log4j生成的日志，日志内容不断增加，需要把追加到日志文件中的数据实时采集到hdfs</p>

<p>根据需求，首先定义以下3大要素 <br>
   采集源，即source——监控文件内容更新 :  exec  ‘tail -F file’ <br>
   下沉目标，即sink——HDFS文件系统  :  hdfs sink <br>
   Source和sink之间的传递通道——channel，可用file channel 也可以用 内存channel</p>

<p>配置文件编写： <br>
agent1.sources = source1 <br>
agent1.sinks = sink1 <br>
agent1.channels = channel1</p>



<h1 id="describeconfigure-tail-f-source1">Describe/configure tail -F source1</h1>

<p>agent1.sources.source1.type = exec <br>
agent1.sources.source1.command = tail -F /home/hadoop/logs/access_log <br>
agent1.sources.source1.channels = channel1</p>



<h1 id="configure-host-for-source">configure host for source</h1>

<p>agent1.sources.source1.interceptors = i1 <br>
agent1.sources.source1.interceptors.i1.type = host <br>
agent1.sources.source1.interceptors.i1.hostHeader = hostname</p>



<h1 id="describe-sink1">Describe sink1</h1>

<p>agent1.sinks.sink1.type = hdfs</p>



<h1 id="a1sinksk1channel-c1">a1.sinks.k1.channel = c1</h1>

<p>agent1.sinks.sink1.hdfs.path =hdfs://hdp-node-01:9000/weblog/flume-collection/%y-%m-%d/%H-%M <br>
agent1.sinks.sink1.hdfs.filePrefix = access_log <br>
agent1.sinks.sink1.hdfs.maxOpenFiles = 5000 <br>
agent1.sinks.sink1.hdfs.batchSize= 100 <br>
agent1.sinks.sink1.hdfs.fileType = DataStream <br>
agent1.sinks.sink1.hdfs.writeFormat =Text <br>
agent1.sinks.sink1.hdfs.rollSize = 102400 <br>
agent1.sinks.sink1.hdfs.rollCount = 1000000 <br>
agent1.sinks.sink1.hdfs.rollInterval = 60 <br>
agent1.sinks.sink1.hdfs.round = true <br>
agent1.sinks.sink1.hdfs.roundValue = 10 <br>
agent1.sinks.sink1.hdfs.roundUnit = minute <br>
agent1.sinks.sink1.hdfs.useLocalTimeStamp = true</p>



<h1 id="use-a-channel-which-buffers-events-in-memory-1">Use a channel which buffers events in memory</h1>

<p>agent1.channels.channel1.type = memory <br>
agent1.channels.channel1.keep-alive = 120 <br>
agent1.channels.channel1.capacity = 500000 <br>
agent1.channels.channel1.transactionCapacity = 600</p>



<h1 id="bind-the-source-and-sink-to-the-channel-1">Bind the source and sink to the channel</h1>

<p>agent1.sources.source1.channels = channel1 <br>
agent1.sinks.sink1.channel = channel1</p>

<p>1.3 更多source和sink组件 <br>
Flume支持众多的source和sink类型，详细手册可参考官方文档 <br>
<a href="http://flume.apache.org/FlumeUserGuide.html" rel="nofollow" target="_blank">http://flume.apache.org/FlumeUserGuide.html</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>