---
layout:     post
title:      flume+ Elasticsearch +kibana环境搭建及讲解
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p align="center"><br></p>
<h2>1、软件介绍</h2>
<h3>1.1、flume</h3>
<h4>1.1.1、flume介绍</h4>
<p><strong>1）flume概念</strong></p>
<p>1、flume是一个分布式的日志收集系统，具有高可靠、高可用、事务管理、失败重启等功能。数据处理速度快，完全可以用于生产环境；</p>
<p>2、flume的核心是agent。agent是一个java进程，运行在日志收集端，通过agent接收日志，然后暂存起来，再发送到目的地；</p>
<p>3、agent里面包含3个核心组件：source、channel、sink，介绍如下：</p>
<p align="left"><strong>source</strong>组件是专用于收集日志的，可以处理各种类型各种格式的日志数据,包括avro、thrift、exec、jms、spooling directory、netcat、sequence generator、syslog、http、legacy、自定义等。source组件把数据收集来以后，临时存放在channel中。</p>
<p align="left"><strong>channel </strong>组件是在agent中专用于临时存储数据的，可以存放在memory、jdbc、file、自定义。channel中的数据只有在sink发送成功之后才会被删除。</p>
<p align="left"><strong>sink</strong>组件是用于把数据发送到目的地的组件，目的地包括hdfs、logger、avro、thrift、ipc、file、null、hbase、solr、Elasticsearch、自定义等。</p>
<p align="left">4、在整个数据传输过程中，流动的是事件event。事件event是Flume的基本数据单位，它携带日志数据(字节数组形式)并且携带有头信息，这些Event由Agent外部的Source生成；</p>
<p align="left">5、flume事务保证是在event级别；</p>
<p align="left">6、flume可以支持多级flume的agent ，支持扇入(fan-in)、扇出(fan-out)；</p>
<p> </p>
<p><strong>2）flume数据流模型</strong></p>
<p>Flume以agent为最小的独立运行单位。一个agent就是一个JVM。单agent由Source、Sink和Channel三大组件构成。</p>
<p>Flume支持用户建立多级流，也就是说，多个agent可以协同工作。常见模型如下：</p>
<p>1、单agent模型：</p>
<p></p>
<p> <img src="" alt=""></p>
<p>2、多agent模型：</p>
<p><img src="" alt=""></p>
<p align="center">图（一）</p>
<p> <img src="" alt=""></p>
<p></p>
<p align="center">图（二）</p>
<p> <img src="" alt=""></p>
<p></p>
<p align="center">图（三）</p>
<p align="center"> </p>
<p><strong>3）flume读取文件方式</strong></p>
<p>对于直接读取文件Source, 主要有两种方式：</p>
<p><strong>1、Exec source</strong></p>
<p>可通过写Unix command的方式组织数据，最常用的就是tail -F [file]。可以实现实时传输，但在flume不运行和脚本错误时，会丢数据，也不支持断点续传功能。因为没有记录上次文件读到的位置，从而没办法知道，下次再读时，从什么地方开始读。特别是在日志文件一直在增加的时候。flume的source挂了。等flume的source再次开启的这段时间内，增加的日志内容，就没办法被source读取到了。不过flume有一个execStream的扩展，可以自己写一个监控日志增加情况，把增加的日志，通过自己写的工具把增加的内容，传送给flume的node。再传送给sink的node。要是能在tail类的source中能支持，在node挂掉这段时间的内容，等下次node开启后在继续传送，那就更完美了。</p>
<p> </p>
<p><strong>2、Spooling Directory Source</strong></p>
<p>SpoolSource是监测配置的目录下新增的文件，并将文件中的数据读取出来，可实现准实时。需要注意两点：1、拷贝到spool目录下的文件不可以再打开编辑。2、spool目录下不可包含相应的子目录。在实际使用的过程中，可以结合log4j使用，使用log4j的时候，将log4j的文件分割机制设为1分钟一次，将文件拷贝到spool的监控目录。log4j有一个TimeRolling的插件，可以把log4j分割的文件到spool目录。基本实现了实时的监控。Flume在传完文件之后，将会修改文件的后缀，变为.COMPLETED（后缀也可以在配置文件中灵活指定）。</p>
<p> </p>
<p><strong>Exec与Spooling Directory比较：</strong></p>
<p>ExecSource可以实现对日志的实时收集，但是存在Flume不运行或者指令执行出错时，将无法收集到日志数据，无法保证日志数据的完整性。SpoolSource虽然无法实现实时的收集数据，但是可以使用以分钟的方式分割文件，趋近于实时。如果应用无法实现以分钟切割日志文件的话，可以两种收集方式结合使用。</p>
<p><strong>4）flume channel介绍</strong></p>
<p>Channel有多种方式：有MemoryChannel,JDBC Channel, MemoryRecoverChannel, FileChannel。MemoryChannel可以实现高速的吞吐，但是无法保证数据的完整性。MemoryRecoverChannel在官方文档的建议上已经建义使用FileChannel来替换。FileChannel保证数据的完整性与一致性。在具体配置FileChannel时，建议FileChannel设置的目录和程序日志文件保存的目录设成不同的磁盘，以便提高效率。</p>
<p> </p>
<p><strong>5）flume特性</strong></p>
<p>1、高可靠性</p>
<p>作为生产环境运行的软件，高可靠性是必须的。从单agent来看，Flume使用基于事务的数据传递方式来保证事件传递的可靠性。Source和Sink分别被封装进一个事务。事件被存放在Channel中直到该事件被处理，Channel中的事件才会被移除。这是Flume提供的点到点的可靠机制。</p>
<p>从多级流来看，前一个agent的sink和后一个agent的source同样有它们的事务来保障数据的可靠性。</p>
<p>事务流程图如下：</p>
<p></p>
<p> </p>
<p>2、可恢复性</p>
<p>还是靠Channel。推荐使用FileChannel，将事件持久化在本地文件系统里，但性能相对MemoryChannel较差。</p>
<p> </p>
<h4>1.1.2、flume配置说明</h4>
<p><strong>1、source常见配置（详细配置见官方文档）</strong></p>
<p>1）Avro source</p>
<p>flume可以监听avro-client发送过来的内容，然后进行处理。</p>
<p>配置文件内容如下：</p>
<div style="background:#E7E6E6;">
<p>a1.sources = r1</p>
<p>a1.sinks = k1</p>
<p>a1.channels = c1</p>
<p> </p>
<p># Describe/configure the source</p>
<p>a1.sources.r1.type = avro</p>
<p>a1.sources.r1.bind = 0.0.0.0</p>
<p>a1.sources.r1.port = 4141</p>
<p> </p>
<p># Describe the sink</p>
<p>a1.sinks.k1.type = logger</p>
<p> </p>
<p># Use a channel which buffers eventsin memory</p>
<p>a1.channels.c1.type = memory</p>
<p>a1.channels.c1.capacity = 1000</p>
<p>a1.channels.c1.transactionCapacity =100</p>
<p> </p>
<p># Bind the source and sink to thechannel</p>
<p>a1.sources.r1.channels = c1</p>
<p>a1.sinks.k1.channel = c1</p>
</div>
<p> </p>
<p>2）Spool source，</p>
<p>Spool监测配置的目录下新增的文件，并将文件中的数据读取出来。需要注意两点：</p>
<p>1、拷贝到spool目录下的文件不可以再打开编辑；</p>
<p>2、spool目录下不可包含相应的子目录；</p>
<p>配置文件内容如下：</p>
<div style="background:#E7E6E6;">
<p>a1.sources = r1</p>
<p>a1.sinks = k1</p>
<p>a1.channels = c1</p>
<p> </p>
<p># Describe/configure the source</p>
<p>a1.sources.r1.type = spooldir</p>
<p>a1.sources.r1.spoolDir = /home /flume-1.6.0-bin/logs</p>
<p>a1.sources.r1.fileHeader = true</p>
<p> </p>
<p># Describe the sink</p>
<p>a1.sinks.k1.type = logger</p>
<p> </p>
<p># Use a channel which buffers eventsin memory</p>
<p>a1.channels.c1.type = memory</p>
<p>a1.channels.c1.capacity = 1000</p>
<p>a1.channels.c1.transactionCapacity =100</p>
<p> </p>
<p># Bind the source and sink to thechannel</p>
<p>a1.sources.r1.channels = c1</p>
<p>a1.sinks.k1.channel = c1</p>
</div>
<p> </p>
<p>3）Exec source</p>
<p>EXEC执行一个给定的命令获得输出的源，如使用tail命令。</p>
<p>配置文件内容如下：</p>
<div style="background:#E7E6E6;">
<p>a1.sources = r1</p>
<p>a1.sinks = k1</p>
<p>a1.channels = c1</p>
<p> </p>
<p># Describe/configure the source</p>
<p>a1.sources.r1.type = exec</p>
<p>a1.sources.r1.command = tail -F /home/flume-1.6.0-bin/log_exec_tail</p>
<p> </p>
<p># Describe the sink</p>
<p>a1.sinks.k1.type = logger</p>
<p> </p>
<p># Use a channel which buffers eventsin memory</p>
<p>a1.channels.c1.type = memory</p>
<p>a1.channels.c1.capacity = 1000</p>
<p>a1.channels.c1.transactionCapacity =100</p>
<p> </p>
<p># Bind the source and sink to thechannel</p>
<p>a1.sources.r1.channels = c1</p>
<p>a1.sinks.k1.channel = c1</p>
</div>
<p> </p>
<p>4）Syslogtcp source</p>
<p>Syslogtcp监听TCP的端口做为数据源</p>
<p>配置文件内容如下：</p>
<div style="background:#E7E6E6;">
<p>a1.sources = r1</p>
<p>a1.sinks = k1</p>
<p>a1.channels = c1</p>
<p> </p>
<p># Describe/configure the source</p>
<p>a1.sources.r1.type = syslogtcp</p>
<p>a1.sources.r1.port = 5140</p>
<p>a1.sources.r1.host = localhost</p>
<p> </p>
<p># Describe the sink</p>
<p>a1.sinks.k1.type = logger</p>
<p> </p>
<p># Use a channel which buffers eventsin memory</p>
<p>a1.channels.c1.type = memory</p>
<p>a1.channels.c1.capacity = 1000</p>
<p>a1.channels.c1.transactionCapacity =100</p>
<p> </p>
<p># Bind the source and sink to thechannel</p>
<p>a1.sources.r1.channels = c1</p>
<p>a1.sinks.k1.channel = c1</p>
</div>
<p> </p>
<p><strong>2、sink常见配置（详细配置见官方文档）</strong></p>
<p>1）Hadoop sink</p>
<p>配置文件内容如下：</p>
<div style="background:#E7E6E6;">
<p>a1.sources = r1</p>
<p>a1.sinks = k1</p>
<p>a1.channels = c1</p>
<p> </p>
<p># Describe/configure the source</p>
<p>a1.sources.r1.type = syslogtcp</p>
<p>a1.sources.r1.port = 5140</p>
<p>a1.sources.r1.host = localhost</p>
<p> </p>
<p># Describe the sink</p>
<p>a1.sinks.k1.type = hdfs</p>
<p>a1.sinks.k1.hdfs.path =hdfs://m1:9000/user/flume/syslogtcp</p>
<p>a1.sinks.k1.hdfs.filePrefix = Syslog</p>
<p>a1.sinks.k1.hdfs.round = true</p>
<p>a1.sinks.k1.hdfs.roundValue = 10</p>
<p>a1.sinks.k1.hdfs.roundUnit = minute</p>
<p> </p>
<p># Use a channel which buffers eventsin memory</p>
<p>a1.channels.c1.type = memory</p>
<p>a1.channels.c1.capacity = 1000</p>
<p>a1.channels.c1.transactionCapacity =100</p>
<p> </p>
<p># Bind the source and sink to thechannel</p>
<p>a1.sources.r1.channels = c1</p>
<p>a1.sinks.k1.channel = c1</p>
</div>
<p> </p>
<p>2）File Roll Sink</p>
<p>配置文件内容如下：</p>
<div style="background:#E7E6E6;">
<p>a1.sources = r1</p>
<p>a1.sinks = k1</p>
<p>a1.channels = c1</p>
<p> </p>
<p># Describe/configure the source</p>
<p>a1.sources.r1.type = syslogtcp</p>
<p>a1.sources.r1.port = 5555</p>
<p>a1.sources.r1.host = localhost</p>
<p> </p>
<p># Describe the sink</p>
<p>a1.sinks.k1.type = file_roll</p>
<p>a1.sinks.k1.sink.directory = /home/flume-1.6.0-bin/logs</p>
<p> </p>
<p># Use a channel which buffers eventsin memory</p>
<p>a1.channels.c1.type = memory</p>
<p>a1.channels.c1.capacity = 1000</p>
<p>a1.channels.c1.transactionCapacity =100</p>
<p> </p>
<p># Bind the source and sink to thechannel</p>
<p>a1.sources.r1.channels = c1</p>
<p>a1.sinks.k1.channel = c1</p>
</div>
<p> </p>
<p>3）elasticsearch Sink</p>
<p>配置文件内容如下：</p>
<div style="background:#E7E6E6;">
<p># Name the components on this agent</p>
<p>a1.sources = r1</p>
<p>a1.sinks = k1</p>
<p>a1.channels = c1</p>
<p> </p>
<p># Describe/configure the source</p>
<p>a1.sources.r1.type = avro</p>
<p>a1.sources.r1.bind = localhost</p>
<p>a1.sources.r1.port = 44444</p>
<p> </p>
<p># Describe the sink</p>
<p>a1.sinks. k1.type = elasticsearch</p>
<p>a1.sinks. k1.hostNames =127.0.0.1:9300</p>
<p>a1.sinks. k1.indexName = test_index</p>
<p>a1.sinks. k1.indexType = test_type_1</p>
<p>a1.sinks. k1.clusterName =vie61_yanshi</p>
<p>a1.sinks. k1.batchSize = 10</p>
<p>a1.sinks. k1.ttl = 5d</p>
<p>a1.sinks. k1.serializer =org.apache.flume.sink.elasticsearch.ElasticSearchDynamicSerializer</p>
<p> </p>
<p># Use a channel which buffers eventsin memory</p>
<p>a1.channels.c1.type = memory</p>
<p>a1.channels.c1.capacity = 1000</p>
<p>a1.channels.c1.transactionCapacity =100</p>
<p> </p>
<p># Bind the source and sink to thechannel</p>
<p>a1.sources.r1.channels = c1</p>
<p>a1.sinks.k1.channel = c1</p>
</div>
<p> </p>
<p><strong>3、Flume Sink Processors常见配置（详细配置见官方文档）</strong></p>
<p>1）Failover SinkProcessor</p>
<p>将多个sink归为一个组，先向组中优先级高的sink发送消息，如果发送失败，则会向组中优先级低的sink发送消息。</p>
<p>配置文件内容如下：</p>
<div style="background:#E7E6E6;">
<p>a1.sinkgroups = g1</p>
<p>a1.sinkgroups.g1.sinks = k1 k2</p>
<p>a1.sinkgroups.g1.processor.type =failover</p>
<p>a1.sinkgroups.g1.processor.priority.k1= 5</p>
<p>a1.sinkgroups.g1.processor.priority.k2= 10</p>
<p>a1.sinkgroups.g1.processor.maxpenalty= 10000</p>
</div>
<p> </p>
<p>2）Load balancing SinkProcessor</p>
<p>将多个sink归为一个组，向组中sink负载均衡的发送消息。负载均衡的方式分为：循环遍历（round_robin）、随机（random）和自定义实现。</p>
<p>配置文件内容如下：</p>
<div style="background:#E7E6E6;">
<p>a1.sinkgroups = g1</p>
<p>a1.sinkgroups.g1.sinks = k1 k2</p>
<p>a1.sinkgroups.g1.processor.type =load_balance</p>
<p>a1.sinkgroups.g1.processor.backoff =true</p>
<p>a1.sinkgroups.g1.processor.selector =random</p>
</div>
<p> </p>
<p><strong>4、channel常见配置（详细配置见官方文档）</strong></p>
<p>1）Memory Channel</p>
<p>此种方式将event内容放在缓存中。优点是效率高，缺点是放在channel中的数据丢失后不可恢复。</p>
<p>配置文件内容如下：</p>
<div style="background:#E7E6E6;">
<p>a1.sources = r1</p>
<p>a1.sinks = k1</p>
<p>a1.channels = c1</p>
<p> </p>
<p># Describe/configure the source</p>
<p>a1.sources.r1.type = avro</p>
<p>a1.sources.r1.bind = 0.0.0.0</p>
<p>a1.sources.r1.port = 4141</p>
<p> </p>
<p># Describe the sink</p>
<p>a1.sinks.k1.type = logger</p>
<p> </p>
<p># Use a channel which buffers eventsin memory</p>
<p>a1.channels.c1.type = memory</p>
<p>a1.channels.c1.capacity = 100</p>
<p>a1.channels.c1.transactionCapacity =100</p>
<p> </p>
<p># Bind the source and sink to thechannel</p>
<p>a1.sources.r1.channels = c1</p>
<p>a1.sinks.k1.channel = c1</p>
</div>
<p> </p>
<p>2）File Channel</p>
<p>此种方式将event内容放在文件系统中。优点是数据达到channel后即使flume挂了，在flume重启后数据依然可以恢复发送。缺点是效率较低。</p>
<p>配置文件内容如下：</p>
<div style="background:#E7E6E6;">
<p>a1.sources = r1</p>
<p>a1.sinks = k1</p>
<p>a1.channels = c1</p>
<p> </p>
<p># Describe/configure the source</p>
<p>a1.sources.r1.type = avro</p>
<p>a1.sources.r1.bind = 0.0.0.0</p>
<p>a1.sources.r1.port = 4141</p>
<p> </p>
<p># Describe the sink</p>
<p>a1.sinks.k1.type = logger</p>
<p> </p>
<p># Use a channel which buffers eventsin memory</p>
<p>a1.channels.c1.type = file</p>
<p>a1.channels.c1.checkpointDir =/mnt/flume/checkpoint</p>
<p>a1.channels.c1.dataDirs =/mnt/flume/data</p>
<p> </p>
<p># Bind the source and sink to thechannel</p>
<p>a1.sources.r1.channels = c1</p>
<p>a1.sinks.k1.channel = c1</p>
</div>
<p> </p>
<p><strong>5、Fan Out常见配置（详细配置见官方文档）</strong></p>
<p>Fan Out（扇出）分为两种方式：replicating（复制）和multiplexing（复用）。</p>
<p>1）replicating（复制）</p>
<p>复制会将一个source的event内容发送给所有配置的channel。</p>
<p>配置文件内容如下：</p>
<div style="background:#E7E6E6;">
<p># List the sources, sinks andchannels for the agent</p>
<p>&lt;Agent&gt;.sources =&lt;Source1&gt;</p>
<p>&lt;Agent&gt;.sinks = &lt;Sink1&gt;&lt;Sink2&gt;</p>
<p>&lt;Agent&gt;.channels =&lt;Channel1&gt; &lt;Channel2&gt;</p>
<p> </p>
<p># set list of channels for source(separated by space)</p>
<p>&lt;Agent&gt;.sources.&lt;Source1&gt;.channels= &lt;Channel1&gt; &lt;Channel2&gt;</p>
<p> </p>
<p># set channel for sinks</p>
<p>&lt;Agent&gt;.sinks.&lt;Sink1&gt;.channel= &lt;Channel1&gt;</p>
<p>&lt;Agent&gt;.sinks.&lt;Sink2&gt;.channel= &lt;Channel2&gt;</p>
<p> </p>
<p>&lt;Agent&gt;.sources.&lt;Source1&gt;.selector.type= replicating</p>
</div>
<p> </p>
<p>2）multiplexing（复用）</p>
<p>复用会将一个source的event内容发送给匹配到结果的一个子集channels。</p>
<p>配置文件内容如下：</p>
<div style="background:#E7E6E6;">
<p># Mapping for multiplexing selector</p>
<p>&lt;Agent&gt;.sources.&lt;Source1&gt;.selector.type= multiplexing</p>
<p>&lt;Agent&gt;.sources.&lt;Source1&gt;.selector.header= &lt;someHeader&gt;</p>
<p>&lt;Agent&gt;.sources.&lt;Source1&gt;.selector.mapping.&lt;Value1&gt;= &lt;Channel1&gt;</p>
<p>&lt;Agent&gt;.sources.&lt;Source1&gt;.selector.mapping.&lt;Value2&gt;= &lt;Channel1&gt; &lt;Channel2&gt;</p>
<p>&lt;Agent&gt;.sources.&lt;Source1&gt;.selector.mapping.&lt;Value3&gt;= &lt;Channel2&gt;</p>
<p>#...</p>
<p>&lt;Agent&gt;.sources.&lt;Source1&gt;.selector.default= &lt;Channel2&gt;</p>
</div>
<p>具体示例：</p>
<div style="background:#E7E6E6;">
<p># list the sources, sinks andchannels in the agent</p>
<p>agent_foo.sources =avro-AppSrv-source1</p>
<p>agent_foo.sinks = hdfs-Cluster1-sink1avro-forward-sink2</p>
<p>agent_foo.channels = mem-channel-1file-channel-2</p>
<p> </p>
<p># set channels for source</p>
<p>agent_foo.sources.avro-AppSrv-source1.channels= mem-channel-1 file-channel-2</p>
<p> </p>
<p># set channel for sinks</p>
<p>agent_foo.sinks.hdfs-Cluster1-sink1.channel= mem-channel-1</p>
<p>agent_foo.sinks.avro-forward-sink2.channel= file-channel-2</p>
<p> </p>
<p># channel selector configuration</p>
<p>agent_foo.sources.avro-AppSrv-source1.selector.type= multiplexing</p>
<p>agent_foo.sources.avro-AppSrv-source1.selector.header= State</p>
<p>agent_foo.sources.avro-AppSrv-source1.selector.mapping.CA= mem-channel-1</p>
<p>agent_foo.sources.avro-AppSrv-source1.selector.mapping.AZ= file-channel-2</p>
<p>agent_foo.sources.avro-AppSrv-source1.selector.mapping.NY= mem-channel-1 file-channel-2</p>
<p>agent_foo.sources.avro-AppSrv-source1.selector.default= mem-channel-1</p>
</div>
<h4>1.1.3、flume扩展说明</h4>
<p>Flume对source、channel和sink提供了多种实现方式，但因为需求的复杂性，可能这些方式满足不了我们的需求。好在flume提供了灵活的扩展方式，我们通过实现flume提供的接口，就可以方便的实现自定义的source、sink等。具体实现请参考官方源码。</p>
<p>1、  source扩展</p>
<div style="background:#E7E6E6;">
<p>public class MySource extendsAbstractSource implements Configurable, PollableSource {</p>
<p> private String myProp;</p>
<p> </p>
<p> @Override</p>
<p> public void configure(Context context) {</p>
<p>   String myProp = context.getString("myProp","defaultValue");</p>
<p>   // Process the myProp value (e.g. validation, convert to another type,...)</p>
<p>   // Store myProp for later retrieval by process() method</p>
<p>   this.myProp = myProp;</p>
<p> }</p>
<p> </p>
<p> @Override</p>
<p> public void start() {</p>
<p>   // Initialize the connection to the external client</p>
<p> }</p>
<p> </p>
<p> @Override</p>
<p> public void stop () {</p>
<p>   // Disconnect from external client and do any additional cleanup</p>
<p>   // (e.g. releasing resources or nulling-out field values) ..</p>
<p>  }</p>
<p> </p>
<p> @Override</p>
<p> public Status process() throws EventDeliveryException {</p>
<p>   Status status = null;</p>
<p>   try {</p>
<p>     // This try clause includes whatever Channel/Event operations you wantto do</p>
<p>     // Receive new data</p>
<p>     Event e = getSomeData();</p>
<p>     // Store the Event into this Source's associated Channel(s)</p>
<p>     getChannelProcessor().processEvent(e);</p>
<p>     status = Status.READY;</p>
<p>   } catch (Throwable t) {</p>
<p>     // Log exception, handle individual exceptions as needed</p>
<p>     status = Status.BACKOFF;</p>
<p> </p>
<p>     // re-throw all Errors</p>
<p>     if (t instanceof Error) {</p>
<p>        throw (Error)t;</p>
<p>     }</p>
<p>   } finally {</p>
<p>     txn.close();</p>
<p>   }</p>
<p>   return status;</p>
<p> }</p>
<p>}</p>
</div>
<p> </p>
<p>2、  sink扩展</p>
<div style="background:#E7E6E6;">
<p>public class MySink extendsAbstractSink implements Configurable {</p>
<p> private String myProp;</p>
<p> </p>
<p> @Override</p>
<p> public void configure(Context context) {</p>
<p>   String myProp = context.getString("myProp","defaultValue");</p>
<p>   // Process the myProp value (e.g. validation)</p>
<p>   // Store myProp for later retrieval by process() method</p>
<p>   this.myProp = myProp;</p>
<p> }</p>
<p> </p>
<p> @Override</p>
<p> public void start() {</p>
<p>   // Initialize the connection to the external repository (e.g. HDFS) that</p>
<p>   // this Sink will forward Events to ..</p>
<p> }</p>
<p> </p>
<p> @Override</p>
<p> public void stop () {</p>
<p>   // Disconnect from the external respository and do any</p>
<p>   // additional cleanup (e.g. releasing resources or nulling-out</p>
<p>   // field values) ..</p>
<p> }</p>
<p> </p>
<p> @Override</p>
<p> public Status process() throws EventDeliveryException {</p>
<p>   Status status = null;</p>
<p>   // Start transaction</p>
<p>   Channel ch = getChannel();</p>
<p>   Transaction txn = ch.getTransaction();</p>
<p>   txn.begin();</p>
<p>   try {</p>
<p>     // This try clause includes whatever Channel operations you want to do</p>
<p>     Event event = ch.take();</p>
<p>     // Send the Event to the external repository.</p>
<p>     // storeSomeData(e);</p>
<p>     txn.commit();</p>
<p>     status = Status.READY;</p>
<p>   } catch (Throwable t) {</p>
<p>     txn.rollback();</p>
<p>     // Log exception, handle individual exceptions as needed</p>
<p>     status = Status.BACKOFF;</p>
<p>     // re-throw all Errors</p>
<p>     if (t instanceof Error) {</p>
<p>        throw (Error)t;</p>
<p>     }</p>
<p>   }</p>
<p>   return status;</p>
<p> }</p>
<p>}</p>
</div>
<p> </p>
<p>3、  Interceptor扩展</p>
<div style="background:#E7E6E6;">
<p>public class MyInterceptor implementsInterceptor {</p>
<p>           @Override</p>
<p>           public void initialize() {</p>
<p>           }</p>
<p> </p>
<p>           /**</p>
<p>            * Modifies events in-place.</p>
<p>            */</p>
<p>           @Override</p>
<p>           public Event intercept(Event event) {</p>
<p>           }</p>
<p>           </p>
<p>           /**</p>
<p>            * Delegates to {@link #intercept(Event)} ina loop.</p>
<p>            * @param events</p>
<p>            * @return</p>
<p>            */</p>
<p>           @Override</p>
<p>           public List&lt;Event&gt;intercept(List&lt;Event&gt; events) {</p>
<p>           }</p>
<p> </p>
<p>           @Override</p>
<p>           public void close() {</p>
<p>             // no-op</p>
<p>           }</p>
<p> </p>
<p>           /**</p>
<p>            * Builder which builds new instances of theHostInterceptor.</p>
<p>            */</p>
<p>           public static class Builder implementsInterceptor.Builder {                 </p>
<p>                   @Override</p>
<p>                   publicvoid configure(Context context) {</p>
<p>                   }</p>
<p> </p>
<p>                   @Override</p>
<p>                   publicInterceptor build() {</p>
<p>                            returnnew MyInterceptor();</p>
<p>                   }</p>
<p>           }</p>
<p>}</p>
</div>
<p> </p>
<p>4、  RPC clients – Avro</p>
<p>通过Avro client可以实现将日志信息直接发送到flume，前提是flume的source端收集日志方式需要配置为Avro方式。示例如下：</p>
<div style="background:#E7E6E6;">
<p>public class MyApp {</p>
<p> public static void main(String[] args) {</p>
<p>   MyRpcClientFacade client = new MyRpcClientFacade();</p>
<p>   // Initialize client with the remote Flume agent's host and port</p>
<p>   client.init("host.example.org", 41414);</p>
<p> </p>
<p>   // Send 10 events to the remote Flume agent. That agent should be</p>
<p>   // configured to listen with an AvroSource.</p>
<p>   String sampleData = "Hello Flume!";</p>
<p>   for (int i = 0; i &lt; 10; i++) {</p>
<p>     client.sendDataToFlume(sampleData);</p>
<p>   }</p>
<p> </p>
<p>   client.cleanUp();</p>
<p> }</p>
<p>}</p>
<p> </p>
<p>class MyRpcClientFacade {</p>
<p> private RpcClient client;</p>
<p> private String hostname;</p>
<p> private int port;</p>
<p> </p>
<p> public void init(String hostname, int port) {</p>
<p>   // Setup the RPC connection</p>
<p>   this.hostname = hostname;</p>
<p>   this.port = port;</p>
<p>   this.client = RpcClientFactory.getDefaultInstance(hostname, port);</p>
<p>   // Use the following method to create a thrift client (instead of theabove line):</p>
<p>   // this.client = RpcClientFactory.getThriftInstance(hostname, port);</p>
<p> }</p>
<p> </p>
<p> public void sendDataToFlume(String data) {</p>
<p>   // Create a Flume Event object that encapsulates the sample data</p>
<p>   Event event = EventBuilder.withBody(data,Charset.forName("UTF-8"));</p>
<p> </p>
<p>   // Send the event</p>
<p>   try {</p>
<p>     client.append(event);</p>
<p>   } catch (EventDeliveryException e) {</p>
<p>     // clean up and recreate the client</p>
<p>     client.close();</p>
<p>     client = null;</p>
<p>     client = RpcClientFactory.getDefaultInstance(hostname, port);</p>
<p>     // Use the following method to create a thrift client (instead of theabove line):</p>
<p>     // this.client = RpcClientFactory.getThriftInstance(hostname, port);</p>
<p>   }</p>
<p> }</p>
<p> </p>
<p> public void cleanUp() {</p>
<p>   // Close the RPC connection</p>
<p>   client.close();</p>
<p> }</p>
<p> </p>
<p>}</p>
</div>
<p> </p>
<h3>1.2、Elasticsearch</h3>
<h4>1.2.1、Elasticsearch介绍</h4>
<p>1）Elasticsearch介绍</p>
<p>Elasticsearch是一个实时的分布式搜索和分析引擎。它可以帮助你用前所未有的速度去处理大规模数据。它可以用于全文搜索，结构化搜索以及分析，当然你也可以将这三者进行组合。</p>
<p>Elasticsearch是一个建立在全文搜索引擎Apache Lucene™基础上的搜索引擎，可以说Lucene是当今最先进，最高效的全功能开源搜索引擎框架。</p>
<p>Elasticsearch使用Lucene作为内部引擎，但是在使用它做全文搜索时，只需要使用统一开发好的API即可，而不需要了解其背后复杂的Lucene的运行原理。</p>
<p>当然Elasticsearch并不仅仅是Lucene这么简单，它不但包括了全文搜索功能，还可以进行以下工作:</p>
<p>    1、分布式实时文件存储，并将每一个字段都编入索引，使其可以被搜索；</p>
<p>    2、实时分析的分布式搜索引擎；</p>
<p>    3、可以扩展到上百台服务器，处理PB级别的结构化或非结构化数据；</p>
<p>这么多的功能被集成到一台服务器上，你可以轻松地通过客户端或者任何你喜欢的程序语言与ES的RESTful API进行交流。</p>
<p> </p>
<p>2）Elasticsearch优点</p>
<p>1、 Elasticsearch是分布式的。不需要其他组件，分发是实时的，被叫做”Push replication”；</p>
<p>    2、Elasticsearch完全支持 Apache Lucene 的接近实时的搜索；</p>
<p>    3、处理多租户（multitenancy）不需要特殊配置，而Solr则需要更多的高级设置；</p>
<p>    4、Elasticsearch采用 Gateway 的概念，使得完备份更加简单；</p>
<p>    5、各节点组成对等的网络结构，某些节点出现故障时会自动分配其他节点代替其进行工作。</p>
<p> </p>
<p>3）Elasticsearch使用案例</p>
<p>    1、维基百科使用Elasticsearch来进行全文搜做并高亮显示关键词，以及提供search-as-you-type、did-you-mean等搜索建议功能；</p>
<p>    2、英国卫报使用Elasticsearch来处理访客日志，以便能将公众对不同文章的反应实时地反馈给各位编辑；</p>
<p>    3、StackOverflow将全文搜索与地理位置和相关信息进行结合，以提供more-like-this相关问题的展现；</p>
<p>    4、GitHub使用Elasticsearch来检索超过1300亿行代码；</p>
<p>    5、每天，GoldmanSachs使用它来处理5TB数据的索引，还有很多投行使用它来分析股票市场的变动。</p>
<p>但是Elasticsearch并不只是面向大型企业的，它还帮助了很多类似DataDog以及Klout的创业公司进行了功能的扩展。</p>
<h4>1.2.2、Elasticsearch配置说明</h4>
<p><strong>1、索引模板（Index templates）</strong></p>
<p>索引可使用预定义的模板进行创建,这个模板称作Indextemplates。模板设置包括settings和mappings，通过模式匹配的方式使得多个索引重用一个模板。</p>
<p>1）定义模板：</p>
<div style="background:#E7E6E6;">
<p>   curl -XPUT localhost:9200/_template/template_1 -d '  </p>
<p>   {  </p>
<p>        "template" :"te*",  </p>
<p>        "settings" : {  </p>
<p>            "number_of_shards" :5  </p>
<p>        }, </p>
<p>        "mappings" : {  </p>
<p>            "type1" : {  </p>
<p>                "_source" :{"enabled" : false },</p>
<p>                                     "properties": { }</p>
<p>            } </p>
<p>        }  </p>
<p>   }  </p>
<p>   '  </p>
</div>
<p>说明：上述定义的模板template_1将对用te开头的新索引都是有效。</p>
<p> </p>
<p>模板中也可以包含别别名的定义，如下：</p>
<div style="background:#E7E6E6;">
<p>   curl -XPUT localhost:9200/_template/template_1 -d '  </p>
<p>   {  </p>
<p>        "template" :"te*",  </p>
<p>        "settings" : {  </p>
<p>            "number_of_shards" :1  </p>
<p>        }, </p>
<p>        "aliases" : {  </p>
<p>            "alias1" : {},  </p>
<p>            "alias2" : {  </p>
<p>                "filter" : {  </p>
<p>                    "term":{"user" : "kimchy" } </p>
<p>                },  </p>
<p>                "routing":"kimchy"  </p>
<p>            }, </p>
<p>            "{index}-alias" : {}   </p>
<p>        } </p>
<p>}  </p>
</div>
<p> </p>
<p>2）删除模板</p>
<p>curl -XDELETElocalhost:9200/_template/template_1  </p>
<p> </p>
<p>3）查看模板</p>
<p>curl -XGET localhost:9200/_template/template_1  </p>
<p> </p>
<p>4）<strong>模板配置文件</strong></p>
<p>除了以上方式，索引模板也可以在文件中进行配置。索引模板的配置文件需要在每个主节点的config目录下，目录结构为：config/templates/template_1.json,</p>
<p>template_1.json的示例如下：</p>
<div style="background:#E7E6E6;">
<p>   {  </p>
<p>     "template-logstash" : { </p>
<p>        "template" :"logstash*",  </p>
<p>        "settings" : {  </p>
<p>          "index.number_of_shards" :5,  </p>
<p>          "number_of_replicas" :1,  </p>
<p>          "index" : {  </p>
<p>            "store" : {  </p>
<p>             "compress" :{  </p>
<p>                "stored" : true,  </p>
<p>                "tv": true  </p>
<p>              } </p>
<p>            } </p>
<p>          } </p>
<p>        }, </p>
<p>        "mappings" : {  </p>
<p>          "_default_" : {  </p>
<p>            "properties" : {  </p>
<p>              "dynamic" :"true",  </p>
<p>            }, </p>
<p>          }, </p>
<p>          "loadbalancer" : {  </p>
<p>            "_source" : {  </p>
<p>              "compress" : true,  </p>
<p>            }, </p>
<p>            "_ttl" : {  </p>
<p>              "enabled" : true,  </p>
<p>              "default" :"10d"  </p>
<p>            }, </p>
<p>            "_all" : {  </p>
<p>              "enabled" : false  </p>
<p>            }, </p>
<p>            "properties" : {  </p>
<p>              "@fields" : {  </p>
<p>                "dynamic" :"true",  </p>
<p>                "properties" : {  </p>
<p>                  "client" : {  </p>
<p>                    "type" :"string",  </p>
<p>                    "index" :"not_analyzed"  </p>
<p>                  },  </p>
<p>                  "domain" : {  </p>
<p>                    "type" :"string",  </p>
<p>                    "index" :"not_analyzed"  </p>
<p>                  },  </p>
<p>                  "oh" : {  </p>
<p>                    "type" :"string",  </p>
<p>                    "index" :"not_analyzed"  </p>
<p>                  },  </p>
<p>                  "responsetime" :{  </p>
<p>                    "type" :"double",  </p>
<p>                 },  </p>
<p>                  "size" : {  </p>
<p>                    "type" :"long",  </p>
<p>                    "index" :"not_analyzed"  </p>
<p>                  },  </p>
<p>                  "status" : {  </p>
<p>                    "type" :"string",  </p>
<p>                    "index" :"not_analyzed"  </p>
<p>                  },  </p>
<p>                  "upstreamtime" :{  </p>
<p>                    "type" :"double",  </p>
<p>                  },  </p>
<p>                  "url" : {  </p>
<p>                    "type" :"string",  </p>
<p>                    "index" :"not_analyzed"  </p>
<p>                  }  </p>
<p>                }  </p>
<p>              },  </p>
<p>              "@source" : {  </p>
<p>                "type" :"string",  </p>
<p>                "index" :"not_analyzed"  </p>
<p>              },  </p>
<p>              "@timestamp" : {  </p>
<p>                "type" :"date",  </p>
<p>                "format" :"dateOptionalTime"  </p>
<p>              },  </p>
<p>              "@type" : {  </p>
<p>                "type" :"string",  </p>
<p>                "index" :"not_analyzed",  </p>
<p>                "store" :"no"  </p>
<p>              } </p>
<p>            } </p>
<p>          } </p>
<p>        } </p>
<p>     }  </p>
<p>   }  </p>
</div>
<p> </p>
<p>5）_source字段说明</p>
<p>_source字段是自动生成的，以JSON格式存储索引文件。_source字段没有建索引，所以不可搜索。当执行“get”或者“search”操作时，默认会返回_source字段。</p>
<p> </p>
<p>_source字段消耗性能，所以可以屏蔽（disable）掉。例如：</p>
<div style="background:#E7E6E6;">
<p>{</p>
<p>"tweet":{</p>
<p>"_source":{"enabled":false}</p>
<p>}</p>
<p>}</p>
</div>
<p>enabale:false的情况下，默认检索只返回ID。</p>
<p>如果觉得enabale:true时，索引的膨涨率比较大的情况下可以通过下面一些辅助设置进行优化：</p>
<p>Compress:是否进行压缩，建议一般情况下将其设为true</p>
<p>“includes” : ["author", "name"],</p>
<p>“excludes” : ["sex"]</p>
<p>上面的includes和 excludes主要是针对默认情况下面_source一般是保存全部Bulk过去的数据，我们可以通过include,excludes在字段级别上做出一些限索。</p>
<p> </p>
<h2>2、环境搭建</h2>
<h3>2.1、flume安装</h3>
<p><strong>1）下载apache-flume-1.6.0-bin.tar.gz</strong></p>
<p>官网下载地址：http://flume.apache.org/download.html</p>
<p> </p>
<p><strong>2）安装flume</strong></p>
<p>1、将软件包拷贝到服务器上，如/root/yiran目录下；</p>
<p>2、解压软件包，命令：tar –xvf apache-flume-1.6.0-bin.tar.gz；</p>
<p>3、配置*.conf文件，如example.conf，放在/root/yiran/apache-flume-1.6.0-bin/conf目录下，内容示例如下：</p>
<div style="background:#E7E6E6;">
<p># Name the components on this agent</p>
<p>a1.sources = r1</p>
<p>a1.sinks = k1</p>
<p>a1.channels = c1</p>
<p> </p>
<p># Describe/configure the source</p>
<p>a1.sources.r1.type = netcat</p>
<p>a1.sources.r1.bind = localhost</p>
<p>a1.sources.r1.port = 44444</p>
<p> </p>
<p># Describe the sink</p>
<p>#a1.sinks.k1.type = logger</p>
<p>a1.sinks. k1.type = elasticsearch</p>
<p>a1.sinks. k1.hostNames =127.0.0.1:9300</p>
<p>a1.sinks. k1.indexName = test_index</p>
<p>a1.sinks. k1.indexType = test_type_1</p>
<p>a1.sinks. k1.clusterName = vie61_yanshi</p>
<p>a1.sinks. k1.batchSize = 10</p>
<p>a1.sinks. k1.ttl = 5d</p>
<p>a1.sinks. k1.serializer =org.apache.flume.sink.elasticsearch.ElasticSearchDynamicSerializer</p>
<p> </p>
<p># Use a channel which buffers eventsin memory</p>
<p>a1.channels.c1.type = memory</p>
<p>a1.channels.c1.capacity = 1000</p>
<p>a1.channels.c1.transactionCapacity =100</p>
<p> </p>
<p># Bind the source and sink to thechannel</p>
<p>a1.sources.r1.channels = c1</p>
<p>a1.sinks.k1.channel = c1</p>
</div>
<p>4、配置flume-env.sh文件，位于/root/yiran/apache-flume-1.6.0-bin/conf目录下，操作如下：</p>
<div style="background:#E7E6E6;">
<p>mv flume-env.sh.template flume-env.sh</p>
<p>vi flume-env.sh</p>
<p>修改JAVA_HOME</p>
<p>JAVA_HOME=/usr/java/jdk1.7.0_71</p>
<p>JAVA_OPTS="-Xms512m–Xmx1024m-Dcom.sun.management.jmxremote"</p>
</div>
<p>5、从elasticsearch安装包下将写入elasticsearch需要的核心包拷贝到/root/yiran/apache-flume-1.6.0-bin/lib（当收集的日志需要写入es时才进行第5步操作，否则不用进行第5步）；</p>
<div style="background:#E7E6E6;">
<p>elasticsearch-1.6.2.jar；</p>
<p>lucene-core-4.10.4.jar；</p>
</div>
<p> </p>
<p><strong>3）启动flume</strong></p>
<p>完成上述5个步骤后，则安装完成。然后可以启动flume，命令如下：</p>
<div style="background:#E7E6E6;">
<p>cd /root/yiran/apache-flume-1.6.0-bin;</p>
<p>./bin/flume-ng agent --conf conf--conf-file conf/example.conf --name a1 -Dflume.root.logger=INFO,console &amp;;</p>
</div>
<p>说明：--conf指明配置文件目录名称；--conf-file指明要运行的配置文件；--name指明agent名称，保持与*.conf配置文件里面命名一致；-Dflume.root.logger指明日志打印级别；</p>
<p> </p>
<h3>2.2、Elasticsearch安装</h3>
<p><strong>1） 下载elasticsearch-1.6.2.tar.gz</strong></p>
<p>官网下载地址：<a href="https://www.elastic.co/downloads/elasticsearch" rel="nofollow">https://www.elastic.co/downloads/elasticsearch</a>；</p>
<p> </p>
<p><strong>2）安装elasticsearch</strong></p>
<p>1、将软件包拷贝到服务器上，如/root/yiran目录下；</p>
<p>2、解压软件包，命令：tar –xvf elasticsearch-1.6.2.tar.gz；</p>
<p>3、修改/root/yiran/elasticsearch-1.6.2/config下elasticsearch.yml文件：</p>
<div style="background:#E7E6E6;">
<p>如将node.name的值设置为“test-node”，表示当前这个es服务节点名字为test-node；</p>
<p>修改cluster.name的值为vie61_yanshi；</p>
<p>修改network.host为本机ip；</p>
</div>
<p>4、  根据需要可配置/root/yiran/elasticsearch-1.6.2/bin目录下elasticsearch.in.sh文件；</p>
<p> </p>
<p><strong>3）启动elasticsearch</strong></p>
<p>完成上述4个步骤后，则安装完成。然后可以启动elasticsearch，命令如下：</p>
<p>nohup ./elasticsearch &amp;</p>
<p> </p>
<h3>2.3、kibana安装</h3>
<p><strong>1）下载kibana-4.1.10-linux-x64.tar.gz</strong></p>
<p>官网下载地址：https://www.elastic.co/downloads/past-releases</p>
<p><strong> </strong></p>
<p><strong>2）安装kibana</strong></p>
<p>1、将软件包拷贝到服务器上，如/root/yiran目录下；</p>
<p>2、解压软件包，命令：tar –xvf kibana-4.1.10-linux-x64.tar.gz；</p>
<p>3、修改/root/yiran/kibana-4.1.10-linux-x64/config目录下kibana.yml文件；</p>
<div style="background:#E7E6E6;">
<p>修改host为本机ip;</p>
<p>修改elasticsearch_url为要访问的elasticsearch的地址，如：<a href="http://localhost:9200/" rel="nofollow">http://localhost:9200</a>；</p>
</div>
<p> </p>
<p><strong>3）启动kibana</strong></p>
<p>完成上述3个步骤后，则安装完成。然后可以启动kibana，命令如下：</p>
<p>nohup ./kibana &amp;</p>
<p> </p>
<h2>3、实例演示</h2>
<h3>3.1、实例说明</h3>
<p>日志记录方式用logback，示例展示了将tomcat访问日志和用户行为日志收集到flume，然后由flume将日志信息写入到elasticsearch中，最后通过kibana对数据进行统计分析。架构图如下：</p>
<p></p>
<p>    <img src="" alt="">                  </p>
<h3>3.2、flume配置</h3>
<p><strong>环境说明：</strong></p>
<p>1）用logback记录日志；</p>
<p>2）  部署两套flume环境，本地flume环境命名为agent1（ip:192.168.27.73），远程flume环境命名为agent3（ip:192.168.77.113）；</p>
<p>3）  Agent1收集日志数据，然后将数据通过Avro方式传递到agent3，最后agent3将日志数据写入到elasticsearch；</p>
<p>4）  Source1收集用户行为日志，source2收集tomcat访问日志；</p>
<p>5）  安装步骤见“2.1节flume安装”，*.conf文件见下面配置说明。</p>
<p> </p>
<p><strong>配置说明：</strong></p>
<p>1）  agent3配置文件命名为sink_to_es.conf，内容如下：</p>
<div style="background:#E7E6E6;">
<p>#定义agent各组件名称</p>
<p>agent1.sources = source1 source2</p>
<p>agent1.sinks = sink1 sink2</p>
<p>agent1.channels = channel1 channel2</p>
<p> </p>
<p> </p>
<p>#定义source1配置信息，监听44444端口</p>
<p>agent1.sources.source1.type = avro</p>
<p>agent1.sources.source1.bind =192.168.77.113</p>
<p>agent1.sources.source1.port = 44444</p>
<p> </p>
<p>#定义source2配置信息，监听33333端口</p>
<p>agent1.sources.source2.type = avro</p>
<p>agent1.sources.source2.bind =192.168.77.113</p>
<p>agent1.sources.source2.port = 33333</p>
<p> </p>
<p> </p>
<p>#定义sink1的配置信息，将信息写入到es中，索引名为test_index，索引类型为test_type_1，集群名为vie61_yanshi</p>
<p>agent1.sinks.sink1.type =elasticsearch</p>
<p>agent1.sinks.sink1.hostNames =127.0.0.1:9300</p>
<p>agent1.sinks.sink1.indexName =test_index</p>
<p>agent1.sinks.sink1.indexType =test_type_1</p>
<p>agent1.sinks.sink1.clusterName =vie61_yanshi</p>
<p>agent1.sinks.sink1.batchSize = 10</p>
<p>agent1.sinks.sink1.ttl = 5d</p>
<p>agent1.sinks.sink1.serializer =org.apache.flume.sink.elasticsearch.ElasticSearchDynamicSerializer</p>
<p> </p>
<p>#定义sink2的配置信息，将信息写入到es中，索引名为test_index_tomcat，索引类型为test_type_1，集群名为vie61_yanshi</p>
<p>agent1.sinks.sink2.type =elasticsearch</p>
<p>agent1.sinks.sink2.hostNames =127.0.0.1:9300</p>
<p>agent1.sinks.sink2.indexName =test_index_tomcat</p>
<p>agent1.sinks.sink2.indexType =test_type_1</p>
<p>agent1.sinks.sink2.clusterName =vie61_yanshi</p>
<p>agent1.sinks.sink2.batchSize = 10</p>
<p>agent1.sinks.sink2.ttl = 5d</p>
<p>agent1.sinks.sink2.serializer =org.apache.flume.sink.elasticsearch.ElasticSearchDynamicSerializer</p>
<p> </p>
<p> </p>
<p>#定义channel1的配置信息，信息存储在缓存中</p>
<p>agent1.channels.channel1.type =memory</p>
<p>agent1.channels.channel1.capacity =1000</p>
<p>agent1.channels.channel1.transactionCapacity= 100</p>
<p> </p>
<p>#定义channel2的配置信息，信息存储在缓存中</p>
<p>agent1.channels.channel2.type =memory</p>
<p>agent1.channels.channel2.capacity =1000</p>
<p>agent1.channels.channel2.transactionCapacity= 100</p>
<p> </p>
<p> </p>
<p>#将source1 and sink1 绑定到 channel1</p>
<p>agent1.sources.source1.channels =channel1</p>
<p>agent1.sinks.sink1.channel = channel1</p>
<p> </p>
<p>#将source2 and sink2 绑定到 channel2</p>
<p>agent1.sources.source2.channels =channel2</p>
<p>agent1.sinks.sink2.channel = channel2</p>
</div>
<p><strong> </strong></p>
<p>2）  agent1配置文件命名为sink_to_agent.conf，提供两种实现方式，任选其中一种即可。</p>
<p align="left"><strong>第一种</strong>：通过读取生成的日志文件，将其中的内容收集到flume。假设tomcat访问日志路径为/root/yiran/apache-tomcat-8.0.36/logs/tomcat，用户操作日志路径为/root/yiran/apache-tomcat-8.0.36/logs/operator。内容如下：</p>
<div style="background:#E7E6E6;">
<p align="left">#定义agent各组件名称</p>
<p align="left">agent1.sources= source1 source2</p>
<p align="left">agent1.sinks= sink1 sink2</p>
<p align="left">agent1.channels= channel1 channel2</p>
<p align="left"> </p>
<p align="left"> </p>
<p align="left">#定义source1配置信息，读取operator文件夹下日志内容，只接受“^@@.*@@$”格式的消息，其他消息过滤掉</p>
<p align="left">agent1.sources.source1.type= org.apache.flume.source.SpoolDirectoryTailFileSource</p>
<p align="left">agent1.sources.source1.spoolDir= /root/yiran/apache-tomcat-8.0.36/logs/operator</p>
<p align="left">agent1.sources.source1.fileSuffix= .COMPLETED</p>
<p align="left">agent1.sources.source1.deletePolicy= never </p>
<p align="left">agent1.sources.source1.ignorePattern= ^$</p>
<p align="left">agent1.sources.source1.targetPattern= .*(\\d){4}-(\\d){2}-(\\d){2}.*</p>
<p align="left">agent1.sources.source1.targetFilename= yyyy-MM-dd</p>
<p align="left">agent1.sources.source1.trackerDir= .flumespooltail</p>
<p align="left">agent1.sources.source1.consumeOrder= oldest</p>
<p align="left">agent1.sources.source1.batchSize= 100</p>
<p align="left">agent1.sources.source1.inputCharset= UTF-8</p>
<p align="left">agent1.sources.source1.decodeErrorPolicy= REPLACE</p>
<p align="left">agent1.sources.source1.deserializer= LINE </p>
<p align="left">agent1.sources.source1.interceptors= i1 i2</p>
<p align="left">agent1.sources.source1.interceptors.i1.type= regex_filter</p>
<p align="left">agent1.sources.source1.interceptors.i1.regex= ^@@.*@@$</p>
<p align="left">agent1.sources.source1.interceptors.i2.type= org.apache.flume.interceptor.JsonParseInterceptor$Builder</p>
<p align="left">agent1.sources.source1.interceptors.i2.splitFlag=@@</p>
<p align="left"> </p>
<p align="left">#定义source2配置信息，读取tomcat文件夹下日志内容，只接受“^@@.*@@$”格式的消息，其他消息过滤掉</p>
<p align="left">agent1.sources.source2.type= org.apache.flume.source.SpoolDirectoryTailFileSource</p>
<p align="left">agent1.sources.source2.spoolDir= /root/yiran/apache-tomcat-8.0.36/logs/tomcat</p>
<p align="left">agent1.sources.source2.fileSuffix= .COMPLETED</p>
<p align="left">agent1.sources.source2.deletePolicy= never </p>
<p align="left">agent1.sources.source2.ignorePattern= ^$</p>
<p align="left">agent1.sources.source2.targetPattern= .*(\\d){4}-(\\d){2}-(\\d){2}.*</p>
<p align="left">agent1.sources.source2.targetFilename= yyyy-MM-dd</p>
<p align="left">agent1.sources.source2.trackerDir= .flumespooltail</p>
<p align="left">agent1.sources.source2.consumeOrder= oldest</p>
<p align="left">agent1.sources.source2.batchSize= 100</p>
<p align="left">agent1.sources.source2.inputCharset= UTF-8</p>
<p align="left">agent1.sources.source2.decodeErrorPolicy= REPLACE</p>
<p align="left">agent1.sources.source2.deserializer= LINE</p>
<p align="left">agent1.sources.source2.interceptors= i3 i4</p>
<p align="left">agent1.sources.source2.interceptors.i3.type= regex_filter</p>
<p align="left">agent1.sources.source2.interceptors.i3.regex= ^@@.*@@$</p>
<p align="left">agent1.sources.source2.interceptors.i4.type= org.apache.flume.interceptor.JsonParseInterceptor$Builder</p>
<p align="left">agent1.sources.source2.interceptors.i4.splitFlag=@@</p>
<p align="left"> </p>
<p align="left"> </p>
<p align="left">#定义sink1的配置信息，将信息写入到agent3中的source1中</p>
<p align="left">agent1.sinks.sink1.type= avro</p>
<p align="left">agent1.sinks.sink1.hostname= 192.168.77.113</p>
<p align="left">agent1.sinks.sink1.port= 44444</p>
<p align="left"> </p>
<p align="left">#定义sink2的配置信息，将信息写入到agent3中的source2中</p>
<p align="left">agent1.sinks.sink2.type= avro</p>
<p align="left">agent1.sinks.sink2.hostname= 192.168.77.113</p>
<p align="left">agent1.sinks.sink2.port= 33333</p>
<p align="left"> </p>
<p align="left"> </p>
<p align="left">#定义channel1的配置信息，信息存储在缓存中</p>
<p align="left">agent1.channels.channel1.type= memory</p>
<p align="left">agent1.channels.channel1.capacity= 1000</p>
<p align="left">agent1.channels.channel1.transactionCapacity= 100</p>
<p align="left"> </p>
<p align="left">#定义channel2的配置信息，信息存储在缓存中</p>
<p align="left">agent1.channels.channel2.type= memory</p>
<p align="left">agent1.channels.channel2.capacity= 1000</p>
<p align="left">agent1.channels.channel2.transactionCapacity= 100</p>
<p align="left"> </p>
<p align="left"> </p>
<p align="left">#将source1 andsink1 绑定到 channel1</p>
<p align="left">agent1.sources.source1.channels= channel1</p>
<p align="left">agent1.sinks.sink1.channel= channel1</p>
<p align="left"> </p>
<p align="left">#将source2 andsink2 绑定到 channel2</p>
<p align="left">agent1.sources.source2.channels= channel2</p>
<p align="left">agent1.sinks.sink2.channel= channel2</p>
</div>
<p align="left"><strong>说明：</strong></p>
<p align="left">1）agent1中定义了两个拦截器，第一个类型为“regex_filter”，是系统自带的拦截器，作用是只接受“regex”正则表达式定义格式的消息，如“^@@.*@@$”。第二个类型为org.apache.flume.interceptor.JsonParseInterceptor$Builder，是一个自定义的拦截器，作用是解析接收到的json格式的内容，其内容分割标志默认为：@@，可以根据格式自行定义。实现如下，详细内容见（flume-ng-core-extend工程）：</p>
<div style="background:#E7E6E6;">
<p align="left"><strong><span style="color:#7F0055;">public</span> <span style="color:#7F0055;">
class</span></strong>JsonParseInterceptor <strong><span style="color:#7F0055;">implements</span></strong> Interceptor {</p>
<p align="left">     <strong><span style="color:#7F0055;">private</span> <span style="color:#7F0055;">
static</span><span style="color:#7F0055;">final</span></strong>Logger <strong><em><span style="color:#0000C0;">logger</span></em></strong>= LoggerFactory.<em>getLogger</em>(JsonParseInterceptor.<strong><span style="color:#7F0055;">class</span></strong>);</p>
<p align="left">     </p>
<p align="left">     <strong><span style="color:#7F0055;">private</span> <span style="color:#7F0055;">
final</span></strong>String <span style="color:#0000C0;">splitFlag</span>;</p>
<p align="left">     </p>
<p align="left">     <span style="color:#3F5FBF;">/**</span></p>
<p align="left"><span style="color:#3F5FBF;">      * Only </span><span style="color:#3F3FBF;">{@link HostInterceptor.Builder}</span><span style="color:#3F5FBF;"> can build me</span></p>
<p align="left"><span style="color:#3F5FBF;">      */</span></p>
<p align="left">     <strong><span style="color:#7F0055;">private</span></strong> JsonParseInterceptor(String<span style="color:#6A3E3E;">splitFlag</span>) {</p>
<p align="left">        <strong><span style="color:#7F0055;">this</span></strong>.<span style="color:#0000C0;">splitFlag</span>=<span style="color:#6A3E3E;">splitFlag</span>;</p>
<p align="left">     }</p>
<p align="left"> </p>
<p align="left">     <span style="color:#646464;">@Override</span></p>
<p align="left">     <strong><span style="color:#7F0055;">public</span> <span style="color:#7F0055;">
void</span></strong>initialize() {</p>
<p align="left">     }</p>
<p align="left"> </p>
<p align="left">     <span style="color:#3F5FBF;">/**</span></p>
<p align="left"><span style="color:#3F5FBF;">      * Modifies events in</span><span style="color:#7F7F9F;">-</span><span style="color:#3F5FBF;">place.</span></p>
<p align="left"><span style="color:#3F5FBF;">      */</span></p>
<p align="left">     <span style="color:#646464;">@Override</span></p>
<p align="left">     <strong><span style="color:#7F0055;">public</span></strong> Event intercept(Event<span style="color:#6A3E3E;">event</span>) {</p>
<p align="left">      String<span style="color:#6A3E3E;">body</span>= <strong><span style="color:#7F0055;">new</span></strong>String(<span style="color:#6A3E3E;">event</span>.getBody(),Charsets.<strong><em><span style="color:#0000C0;">UTF_8</span></em></strong>);</p>
<p align="left">       String[] <span style="color:#6A3E3E;">bodyArray</span> = <span style="color:#6A3E3E;">
body</span>.split(<span style="color:#0000C0;">splitFlag</span>);</p>
<p align="left">       <strong><em><span style="color:#0000C0;">logger</span></em></strong>.info(<span style="color:#2A00FF;">"body content: "</span> +<span style="color:#6A3E3E;">body</span> +<span style="color:#2A00FF;">"  length:"</span> +<span style="color:#6A3E3E;">bodyArray</span>.<span style="color:#0000C0;">length</span>);</p>
<p align="left">       <strong><span style="color:#7F0055;">if</span></strong>(<span style="color:#6A3E3E;">bodyArray</span> !=<strong><span style="color:#7F0055;">null</span></strong>&amp;&amp;<span style="color:#6A3E3E;">bodyArray</span>.<span style="color:#0000C0;">length</span>
 &gt; 1){</p>
<p align="left">          String<span style="color:#6A3E3E;">content</span>= <span style="color:#6A3E3E;">
bodyArray</span>[1];  <span style="color:#3F7F5F;">//</span><span style="color:#3F7F5F;">取得</span><span style="color:#3F7F5F;">body</span><span style="color:#3F7F5F;">内容，为</span><u><span style="color:#3F7F5F;">json</span></u><span style="color:#3F7F5F;">格式数据</span></p>
<p align="left">          <strong><span style="color:#7F0055;">try</span></strong>{</p>
<p align="left">             Map&lt;String,String&gt; <span style="color:#6A3E3E;">jsonMap</span>=<u>(Map&lt;String, String&gt;) JSON.<em>parse</em>(<span style="color:#6A3E3E;">content</span>)</u>;</p>
<p align="left">             <span style="color:#6A3E3E;">event</span>.setHeaders(<span style="color:#6A3E3E;">jsonMap</span>);</p>
<p align="left">             <span style="color:#6A3E3E;">event</span>.setBody(<strong><span style="color:#7F0055;">null</span></strong>);</p>
<p align="left">             <strong><em><span style="color:#0000C0;">logger</span></em></strong>.info(<span style="color:#2A00FF;">"body content: "</span>+<span style="color:#6A3E3E;">jsonMap</span>);</p>
<p align="left">          }<strong><span style="color:#7F0055;">catch</span></strong>(Exception<span style="color:#6A3E3E;">e</span>){</p>
<p align="left">             <strong><em><span style="color:#0000C0;">logger</span></em></strong>.error(<span style="color:#2A00FF;">"Could not parse json data, "</span>,<span style="color:#6A3E3E;">e</span>);</p>
<p align="left">             <strong><span style="color:#7F0055;">return</span> <span style="color:#7F0055;">
null</span></strong>;</p>
<p align="left">          }</p>
<p align="left">       }<strong><span style="color:#7F0055;">else</span></strong>{</p>
<p align="left">          <strong><span style="color:#7F0055;">return</span> <span style="color:#7F0055;">
null</span></strong>;</p>
<p align="left">       }</p>
<p align="left">       <strong><span style="color:#7F0055;">return</span></strong><span style="color:#6A3E3E;">event</span>;</p>
<p align="left">     }</p>
<p align="left">     </p>
<p align="left">     <span style="color:#3F5FBF;">/**</span></p>
<p align="left"><span style="color:#3F5FBF;">      * Delegates to </span><span style="color:#3F3FBF;">{@link #intercept(Event)}</span><span style="color:#3F5FBF;"> in a loop.</span></p>
<p align="left"><span style="color:#3F5FBF;">      * </span><strong><span style="color:#7F9FBF;">@param</span></strong><span style="color:#3F5FBF;"> events</span></p>
<p align="left"><span style="color:#3F5FBF;">      * </span><strong><span style="color:#7F9FBF;">@return</span></strong></p>
<p align="left"><span style="color:#3F5FBF;">      */</span></p>
<p align="left">     <span style="color:#646464;">@Override</span></p>
<p align="left">     <strong><span style="color:#7F0055;">public</span></strong> List&lt;Event&gt; intercept(List&lt;Event&gt;<span style="color:#6A3E3E;">events</span>) {</p>
<p align="left">        List&lt;Event&gt; <span style="color:#6A3E3E;">out</span> = Lists.<em>newArrayList</em>();</p>
<p align="left">          <strong><span style="color:#7F0055;">for</span></strong> (Event<span style="color:#6A3E3E;">event</span> :<span style="color:#6A3E3E;">events</span>) {</p>
<p align="left">            Event <span style="color:#6A3E3E;">outEvent</span> = intercept(<span style="color:#6A3E3E;">event</span>);</p>
<p align="left">            <strong><span style="color:#7F0055;">if</span></strong> (<span style="color:#6A3E3E;">outEvent</span> !=<strong><span style="color:#7F0055;">null</span></strong>){</p>
<p align="left">               <span style="color:#6A3E3E;">out</span>.add(<span style="color:#6A3E3E;">outEvent</span>);</p>
<p align="left">            }</p>
<p align="left">          }</p>
<p align="left">          <strong><span style="color:#7F0055;">return</span></strong><span style="color:#6A3E3E;">out</span>;</p>
<p align="left">     }</p>
<p align="left"> </p>
<p align="left">     <span style="color:#646464;">@Override</span></p>
<p align="left">     <strong><span style="color:#7F0055;">public</span> <span style="color:#7F0055;">
void</span></strong>close() {</p>
<p align="left">       <span style="color:#3F7F5F;">// no-<u>op</u></span></p>
<p align="left">     }</p>
<p align="left"> </p>
<p align="left">     <span style="color:#3F5FBF;">/**</span></p>
<p align="left"><span style="color:#3F5FBF;">      * Builder which builds new instances of theHostInterceptor.</span></p>
<p align="left"><span style="color:#3F5FBF;">      */</span></p>
<p align="left">     <strong><span style="color:#7F0055;">public</span> <span style="color:#7F0055;">
static</span><span style="color:#7F0055;">class</span></strong>Builder <strong><span style="color:#7F0055;">implements</span></strong>Interceptor.Builder {</p>
<p align="left">      <strong><span style="color:#7F0055;">private</span></strong> String<span style="color:#0000C0;">splitFlag</span> =<strong><span style="color:#7F0055;">null</span></strong>;</p>
<p align="left">        </p>
<p align="left">      <span style="color:#646464;">@Override</span></p>
<p align="left">      <strong><span style="color:#7F0055;">public</span> <span style="color:#7F0055;">
void</span></strong> configure(Context <span style="color:#6A3E3E;">context</span>) {</p>
<p align="left">         <span style="color:#0000C0;">splitFlag</span> = <span style="color:#6A3E3E;">
context</span>.getString(<span style="color:#2A00FF;">"splitFlag"</span>,Constants.<em><span style="color:#0000C0;">DEFAULT_SPLITFLAG</span></em>);</p>
<p align="left">      }</p>
<p align="left"> </p>
<p align="left">      <span style="color:#646464;">@Override</span></p>
<p align="left">      <strong><span style="color:#7F0055;">public</span></strong> Interceptor build() {</p>
<p align="left">         <span style="color:#3F7F5F;">// </span><strong><span style="color:#7F9FBF;">TODO</span></strong><span style="color:#3F7F5F;"> Auto-generated method stub</span></p>
<p align="left">         <strong><span style="color:#7F0055;">return</span> <span style="color:#7F0055;">
new</span></strong> JsonParseInterceptor(<span style="color:#0000C0;">splitFlag</span>);</p>
<p align="left">      }</p>
<p align="left">     }</p>
<p align="left">     </p>
<p align="left">     <strong><span style="color:#7F0055;">public</span> <span style="color:#7F0055;">
static</span><span style="color:#7F0055;">class</span></strong>Constants {</p>
<p align="left">       <strong><span style="color:#7F0055;">public</span> <span style="color:#7F0055;">
static</span></strong> String <em><span style="color:#0000C0;">DEFAULT_SPLITFLAG</span></em> =<span style="color:#2A00FF;">"@@"</span>;</p>
<p align="left">     }</p>
<p align="left">}</p>
</div>
<p><strong> </strong></p>
<p>3）  agent1中使用了自定义的Source（SpoolDirectoryTailFileSource，详见flume-ng-core-extend工程），此source将exec和spoolingsource结合起来，可以实现如下效果：</p>
<p>l  监控指定文件夹下的日志文件；</p>
<p>l  实时读取日志文件的内容；</p>
<p>l  实现断点续传功能，即flume发生故障后，重启会从故障前的位置继续读取日志内容；</p>
<p><strong> </strong></p>
<p><strong>第二种：</strong>扩展logback接口，通过Avroclient将日志信息传递到flume。内容如下：</p>
<div style="background:#E7E6E6;">
<p>#定义agent各组件名称</p>
<p>agent1.sources = source1 source2</p>
<p>agent1.sinks = sink1 sink2</p>
<p>agent1.channels = channel1 channel2</p>
<p> </p>
<p> </p>
<p>#定义source1配置信息，监听44444端口，只接受“^@@.*@@$”格式的消息，其他消息过滤掉</p>
<p>agent1.sources.source1.type = avro</p>
<p>agent1.sources.source1.bind =192.168.77.113</p>
<p>agent1.sources.source1.port = 44444</p>
<p>agent1.sources.source1.interceptors =i1 i2</p>
<p>agent1.sources.source1.interceptors.i1.type= regex_filter</p>
<p>agent1.sources.source1.interceptors.i1.regex= ^@@.*@@$</p>
<p>agent1.sources.source1.interceptors.i2.type= org.apache.flume.interceptor.JsonParseInterceptor$Builder</p>
<p> </p>
<p>#定义source2配置信息，监听33333端口，只接受“^@@.*@@$”格式的消息，其他消息过滤掉</p>
<p>agent1.sources.source2.type = avro</p>
<p>agent1.sources.source2.bind =192.168.77.113</p>
<p>agent1.sources.source2.port = 33333</p>
<p>agent1.sources.source2.interceptors =i3 i4</p>
<p>agent1.sources.source2.interceptors.i3.type= regex_filter</p>
<p>agent1.sources.source2.interceptors.i3.regex= ^@@.*@@$</p>
<p>agent1.sources.source2.interceptors.i4.type= org.apache.flume.interceptor.JsonParseInterceptor$Builder</p>
<p> </p>
<p> </p>
<p>#定义sink1的配置信息，通过Avro方式将信息写入到agent3的source1中</p>
<p>agent1.sinks.sink1.type = avro</p>
<p>agent1.sinks.sink1.hostname =192.168.77.113</p>
<p>agent1.sinks.sink1.port = 44444</p>
<p> </p>
<p>#定义sink2的配置信息，通过Avro方式将信息写入到agent3的source2中</p>
<p>agent1.sinks.sink2.type = avro</p>
<p>agent1.sinks.sink2.hostname =192.168.77.113</p>
<p>agent1.sinks.sink2.port = 33333</p>
<p> </p>
<p> </p>
<p>#定义channel1的配置信息，信息存储在缓存中</p>
<p>agent1.channels.channel1.type =memory</p>
<p>agent1.channels.channel1.capacity =1000</p>
<p>agent1.channels.channel1.transactionCapacity= 100</p>
<p> </p>
<p>#定义channel2的配置信息，信息存储在缓存中</p>
<p>agent1.channels.channel2.type =memory</p>
<p>agent1.channels.channel2.capacity =1000</p>
<p>agent1.channels.channel2.transactionCapacity= 100</p>
<p> </p>
<p> </p>
<p>#将source1 and sink1 绑定到 channel1</p>
<p>agent1.sources.source1.channels =channel1</p>
<p>agent1.sinks.sink1.channel = channel1</p>
<p> </p>
<p>#将source2 and sink2 绑定到 channel2</p>
<p>agent1.sources.source2.channels =channel2</p>
<p>agent1.sinks.sink2.channel = channel2</p>
</div>
<p><strong>说明：</strong></p>
<p>1、拦截器的定义见第一种方式里面说明；</p>
<p>2、此种方式需要扩展logback，核心类如下，详细内容见（logback-monitor工程）：</p>
<div style="background:#E7E6E6;">
<p align="left"><strong><span style="color:#7F0055;">public</span> <span style="color:#7F0055;">
class</span></strong>FlumeRpcAppender&lt;E&gt; <strong><span style="color:#7F0055;">extends</span></strong> OutputStreamAppender&lt;E&gt; {</p>
<p align="left">   </p>
<p align="left">   <strong><span style="color:#7F0055;">protected</span></strong> String<span style="color:#0000C0;">configfile</span>;</p>
<p align="left">   <strong><span style="color:#7F0055;">public</span> <span style="color:#7F0055;">
static</span> <span style="color:#7F0055;">final</span></strong> String <strong><em><span style="color:#0000C0;">CLIENT_APPNAME</span></em></strong>=<span style="color:#2A00FF;">"client.appname"</span>;</p>
<p align="left">   <strong><span style="color:#7F0055;">public</span> <span style="color:#7F0055;">
static</span> <span style="color:#7F0055;">final</span></strong> String <strong><em><span style="color:#0000C0;">POOL_SIZE</span></em></strong>=<span style="color:#2A00FF;">"pool.size"</span>;</p>
<p align="left">   </p>
<p align="left">   <strong><span style="color:#7F0055;">private</span> <span style="color:#7F0055;">
final</span></strong> Properties <span style="color:#0000C0;">props</span> = <strong>
<span style="color:#7F0055;">new</span></strong> Properties();</p>
<p align="left">   RpcClient<span style="color:#0000C0;">client</span>;</p>
<p align="left">   </p>
<p align="left">   <span style="color:#646464;">@Override</span></p>
<p align="left">   <strong><span style="color:#7F0055;">public</span> <span style="color:#7F0055;">
void</span></strong> start() {</p>
<p align="left">      genProps();</p>
<p align="left">      <span style="color:#0000C0;">client</span> = RpcClientFactory.<em>getInstance</em>(<span style="color:#0000C0;">props</span>);</p>
<p align="left">      Map&lt;String,String&gt;<span style="color:#6A3E3E;">header</span>= configHeader(<span style="color:#0000C0;">props</span>);</p>
<p align="left">      <strong><span style="color:#7F0055;">int</span></strong> <span style="color:#6A3E3E;">
poolSize</span> = 10;</p>
<p align="left">      String<span style="color:#6A3E3E;">strPoolSize</span>= <span style="color:#0000C0;">
props</span>.getProperty(<strong><em><span style="color:#0000C0;">POOL_SIZE</span></em></strong>);</p>
<p align="left">      <strong><span style="color:#7F0055;">if</span></strong>(<span style="color:#6A3E3E;">strPoolSize</span> !=<strong><span style="color:#7F0055;">null</span></strong> &amp;&amp; !<span style="color:#6A3E3E;">strPoolSize</span>.trim().isEmpty() ){</p>
<p align="left">         <span style="color:#6A3E3E;">poolSize</span> = Integer.<em>parseInt</em>(<span style="color:#6A3E3E;">strPoolSize</span>.trim());</p>
<p align="left">      }</p>
<p align="left">      ExecutorService<span style="color:#6A3E3E;">executorService</span>= Executors.<em>newFixedThreadPool</em>(<span style="color:#6A3E3E;">poolSize</span>);</p>
<p align="left"> </p>
<p align="left">      setOutputStream(<strong><span style="color:#7F0055;">new</span></strong> FlumeRpcOutputStream(<span style="color:#0000C0;">client</span>,<span style="color:#6A3E3E;">header</span>,<span style="color:#6A3E3E;">executorService</span>));</p>
<p align="left">      <strong><span style="color:#7F0055;">super</span></strong>.start();</p>
<p align="left">   }</p>
<p align="left">   </p>
<p align="left">   <strong><span style="color:#7F0055;">private</span> <span style="color:#7F0055;">
void</span></strong> genProps(){</p>
<p align="left">      <strong><span style="color:#7F0055;">if</span></strong>(<strong><span style="color:#7F0055;">this</span></strong>.<span style="color:#0000C0;">configfile</span>!=<strong><span style="color:#7F0055;">null</span></strong>){</p>
<p align="left">         ClassLoader<span style="color:#6A3E3E;">cl</span> =FlumeRpcAppender.<strong><span style="color:#7F0055;">class</span></strong>.getClassLoader();</p>
<p align="left">         <strong><span style="color:#7F0055;">try</span></strong> {</p>
<p align="left">            <span style="color:#0000C0;">props</span>.load(<span style="color:#6A3E3E;">cl</span>.getResourceAsStream(getConfigfile()));</p>
<p align="left">         }<strong><span style="color:#7F0055;">catch</span></strong>(IOException<span style="color:#6A3E3E;">e</span>) {</p>
<p align="left">            <span style="color:#6A3E3E;">e</span>.printStackTrace();</p>
<p align="left">         }</p>
<p align="left">      }<strong><span style="color:#7F0055;">else</span></strong>{</p>
<p align="left">         <strong><span style="color:#7F0055;">throw</span> <span style="color:#7F0055;">
new</span></strong> RuntimeException(<span style="color:#2A00FF;">"configfile is null."</span>);</p>
<p align="left">      }</p>
<p align="left">   }</p>
<p align="left">   </p>
<p align="left">   <strong><span style="color:#7F0055;">private</span></strong>Map&lt;String,String&gt; configHeader(Properties<span style="color:#6A3E3E;">props</span>){</p>
<p align="left">      Map&lt;String,String&gt;<span style="color:#6A3E3E;">header</span>=<strong><span style="color:#7F0055;">new</span></strong>HashMap&lt;String,String&gt;();</p>
<p align="left">      <span style="color:#6A3E3E;">header</span>.put(<strong><em><span style="color:#0000C0;">CLIENT_APPNAME</span></em></strong>,(String)<span style="color:#6A3E3E;">props</span>.remove(<strong><em><span style="color:#0000C0;">CLIENT_APPNAME</span></em></strong>));</p>
<p align="left">      <strong><span style="color:#7F0055;">return</span></strong> <span style="color:#6A3E3E;">
header</span>;</p>
<p align="left">   }</p>
<p align="left"> </p>
<p align="left">   <span style="color:#646464;">@Override</span></p>
<p align="left">   <strong><span style="color:#7F0055;">public</span> <span style="color:#7F0055;">
void</span></strong> stop() {</p>
<p align="left">      <span style="color:#0000C0;">client</span>.close();</p>
<p align="left">      <strong><span style="color:#7F0055;">super</span></strong>.stop();</p>
<p align="left">   }</p>
<p align="left"> </p>
<p align="left">   <span style="color:#3F5FBF;">/**</span></p>
<p align="left"><span style="color:#3F5FBF;">    * </span><strong><span style="color:#7F9FBF;">@return</span></strong><span style="color:#3F5FBF;"> the<u>configfile</u></span></p>
<p align="left"><span style="color:#3F5FBF;">    */</span></p>
<p align="left">   <strong><span style="color:#7F0055;">public</span></strong> String getConfigfile(){</p>
<p align="left">      <strong><span style="color:#7F0055;">return</span></strong> <span style="color:#0000C0;">
configfile</span>;</p>
<p align="left">   }</p>
<p align="left"> </p>
<p align="left">   <span style="color:#3F5FBF;">/**</span></p>
<p align="left"><span style="color:#3F5FBF;">    * </span><strong><span style="color:#7F9FBF;">@param</span></strong><span style="color:#3F5FBF;"> configfile the<u>configfile</u> to set</span></p>
<p align="left"><span style="color:#3F5FBF;">    */</span></p>
<p align="left">   <strong><span style="color:#7F0055;">public</span> <span style="color:#7F0055;">
void</span></strong> setConfigfile(String <span style="color:#6A3E3E;">configfile</span>) {</p>
<p align="left">      <strong><span style="color:#7F0055;">this</span></strong>.<span style="color:#0000C0;">configfile</span> =<span style="color:#6A3E3E;">configfile</span>;</p>
<p align="left">   }</p>
<p>}</p>
</div>
<p>3、用户行为日志会通过Avro的方式发送到source1的44444端口，集成用户行为日志需要做如下配置：</p>
<p>1）在web应用中引入logback-monitor工程，通过maven方式或将logback-monitor打包为jar放入web应用lib目录下；</p>
<p>2）将logback.xml和logback-monitor.properties文件放到web应用class目录。具体内容如下：</p>
<p><strong>logback.xml内容：</strong></p>
<div style="background:#E7E6E6;">
<p>&lt;?xml version="1.0"encoding="UTF-8"?&gt;</p>
<p>&lt;configuration&gt;</p>
<p>         &lt;!--always a good activate OnConsoleStatusListener --&gt;</p>
<p>         &lt;statusListenerclass="ch.qos.logback.core.status.OnConsoleStatusListener" /&gt;</p>
<p> </p>
<p>         &lt;appendername="STDOUT"class="ch.qos.logback.core.ConsoleAppender"&gt;</p>
<p>                   &lt;encoder&gt;</p>
<p>                            &lt;pattern&gt;%d{HH:mm:ss.SSS}[%thread] %-5level %logger{56} - %msg%n&lt;/pattern&gt;</p>
<p>                   &lt;/encoder&gt;</p>
<p>         &lt;/appender&gt;</p>
<p> </p>
<p>         &lt;appendername="FILE"</p>
<p>                   class="ch.qos.logback.core.rolling.RollingFileAppender"&gt;</p>
<p>                   &lt;rollingPolicyclass="ch.qos.logback.core.rolling.TimeBasedRollingPolicy"&gt;</p>
<p>                            &lt;fileNamePattern&gt;/root/yiran/apache-tomcat-8.0.36/logs/operator/operator.%d{yyyy-MM-dd}.log&lt;/fileNamePattern&gt;</p>
<p>                            &lt;maxHistory&gt;30&lt;/maxHistory&gt;</p>
<p>                   &lt;/rollingPolicy&gt;</p>
<p> </p>
<p>                   &lt;encoder&gt;</p>
<p>                            &lt;pattern&gt;%msg&lt;/pattern&gt;</p>
<p>                   &lt;/encoder&gt;</p>
<p>         &lt;/appender&gt;</p>
<p>         </p>
<p>                   &lt;!--flume收集日志配置 --&gt;</p>
<p>         &lt;appendername="flumeRpcAppender"</p>
<p>                   class="com.logback.core.<strong>FlumeRpcAppender</strong>"&gt;</p>
<p>                   &lt;encoder&gt;</p>
<p>                            &lt;pattern&gt;<strong>%msg</strong>&lt;/pattern&gt;</p>
<p>                            &lt;charset&gt;UTF-8&lt;/charset&gt;</p>
<p>                   &lt;/encoder&gt;</p>
<p>                   &lt;configfile&gt;<strong>logback-monitor.properties</strong>&lt;/configfile&gt;</p>
<p>         &lt;/appender&gt;</p>
<p>         </p>
<p>         &lt;rootlevel="INFO"&gt;  </p>
<p>              &lt;appender-ref ref="FILE"/&gt;</p>
<p>              &lt;appender-refref="flumeRpcAppender"/&gt;  </p>
<p>          &lt;/root&gt; </p>
<p>&lt;/configuration&gt;</p>
</div>
<p align="left">说明：</p>
<p align="left">1）msg格式示例：@@{"userName":"王五", "sex": "女","age": "23","createDate":"2016-07-28T23:23:32.999+0800"}@@；</p>
<p align="left">2）应用程序在写入日志时要按照msg消息示例格式进行日志记录。</p>
<p align="left"> </p>
<p align="left"><strong>logback-monitor.properties内容：</strong></p>
<div style="background:#E7E6E6;">
<p>client.appname=something</p>
<p>client.type=DEFAULT_FAILOVER</p>
<p>hosts=h1</p>
<p>hosts.h1=127.0.0.1:44444</p>
<p>pool.size=20</p>
</div>
<p>说明：如有多个flume接收端，可以如下配置。通过配置client.type可以实现负载均衡（default_loadbalance）或失败重发（default_failover）。</p>
<div style="background:#E7E6E6;">
<p>host2=h1 h2 h3</p>
<p>hosts.h1=127.0.0.1:44444</p>
<p>hosts.h2=127.0.0.1:44444</p>
<p>hosts.h3=127.0.0.1:44444</p>
</div>
<p> </p>
<p>4、tomcat访问日志会通过Avro的方式发送到source2的33333端口，集成tomcat访问日志需要做如下配置：</p>
<p>1）  配置logback-monitor.properties文件，将其放入到D:\server2\apache-tomcat-8.0.36\lib目录下，内容如下：</p>
<div style="background:#E7E6E6;">
<p>client.appname=something</p>
<p>client.type=DEFAULT_FAILOVER</p>
<p>hosts=h1</p>
<p>hosts.h1=127.0.0.1:33333</p>
<p>pool.size=5</p>
</div>
<p>2）  配置logback-access.xml文件，将其放入到D:\server2\apache-tomcat-8.0.36\conf目录下，内容如下：</p>
<div style="background:#E7E6E6;">
<p>&lt;?xml version="1.0"encoding="UTF-8"?&gt;</p>
<p>&lt;configuration&gt;</p>
<p>         &lt;!--always a good activate OnConsoleStatusListener --&gt;</p>
<p>         &lt;statusListenerclass="ch.qos.logback.core.status.OnConsoleStatusListener" /&gt;</p>
<p> </p>
<p>         &lt;appendername="STDOUT" class="ch.qos.logback.core.ConsoleAppender"&gt;</p>
<p>         &lt;encoder&gt;</p>
<p align="left">          &lt;pattern&gt;@@{"createTime":"%t{yyyy-MM-ddHH:mm:ss.SSSZ}", "remoteIP":"%h","status":"%s", "contentLength":"%b","duration":"%D", "requestURL":"%r",</p>
<p align="left">"logType":"tomcatLog"}@@&lt;/pattern&gt;</p>
<p>         &lt;/encoder&gt;</p>
<p> &lt;/appender&gt;</p>
<p>         </p>
<p>                   &lt;!--flume收集日志配置 --&gt;</p>
<p>         &lt;appendername="flumeRpcAppender"</p>
<p>                   class="com.logback.core.<strong>FlumeRpcAppender</strong>"&gt;</p>
<p>                   &lt;encoder&gt;</p>
<p>                            &lt;pattern&gt;<strong>@@{"createTime":"%t{yyyy-MM-dd'T'HH:mm:ss.SSSZ}","remoteIP":"%h", "status":"%s","contentLength":"%b", "duration":"%D","requestURL":"%r","logType":"tomcatLog"}@@</strong>&lt;/pattern&gt;</p>
<p>                            &lt;charset&gt;UTF-8&lt;/charset&gt;</p>
<p>                   &lt;/encoder&gt;</p>
<p>                   &lt;configfile&gt;<strong>logback-monitor.properties</strong>&lt;/configfile&gt;</p>
<p>         &lt;/appender&gt;</p>
<p> </p>
<p>         &lt;appendername="FILE"</p>
<p>                   class="ch.qos.logback.core.rolling.RollingFileAppender"&gt;</p>
<p>                   &lt;rollingPolicyclass="ch.qos.logback.core.rolling.TimeBasedRollingPolicy"&gt;</p>
<p>                            &lt;fileNamePattern&gt;/root/yiran/apache-tomcat-8.0.36/logs/tomcat/tomcat_access.%d{yyyy-MM-dd}.log&lt;/fileNamePattern&gt;</p>
<p>                            &lt;maxHistory&gt;30&lt;/maxHistory&gt;</p>
<p>                   &lt;/rollingPolicy&gt;</p>
<p>                   &lt;encoder&gt;</p>
<p>                            &lt;pattern&gt;@@{"createTime":"%t{yyyy-MM-dd'T'HH:mm:ss.SSSZ}","remoteIP":"%h", "status":"%s","contentLength":"%b", "duration":"%D","requestURL":"%r","logType":"tomcatLog"}@@&lt;/pattern&gt;</p>
<p>                   &lt;/encoder&gt;</p>
<p>         &lt;/appender&gt;</p>
<p>         </p>
<p>          &lt;appender-refref="FILE"/&gt;  </p>
<p>          &lt;appender-ref ref="flumeRpcAppender"/&gt;           </p>
<p>&lt;/configuration&gt;</p>
</div>
<p> </p>
<p>3）  将[\部署配置文件\lib]目录下的jar包复制到D:\server2\apache-tomcat-8.0.36\lib目录下：</p>
<p></p>
<p>4）修改tomcat中server.xml下最后一行，添加如下配置：</p>
<div style="background:#E7E6E6;">
<p>&lt;ValveclassName="ch.qos.logback.access.tomcat.LogbackValve"quiet="true"/&gt;</p>
</div>
<p> </p>
<p></p>
<p> </p>
<p>通过上述步骤，则完成flume配置，然后启动agent1和agent3，命令如下：</p>
<p>启动agent1：</p>
<div style="background:#E7E6E6;">
<p>cd C:\Users\qxb-810\Desktop\flume_study\apache-flume-1.6.0-bin;</p>
<p>bin\flume-ng.cmd agent --conf conf--conf-file conf\sink_to_agent.conf --name agent1 &amp;</p>
</div>
<p> </p>
<p>启动agent3：</p>
<div style="background:#E7E6E6;">
<p>cd /root/yiran/apache-flume-1.6.0-bin;</p>
<p>./bin/flume-ng agent --conf conf --conf-fileconf/sink_to_es.conf --name agent1 &amp;</p>
</div>
<p> </p>
<h3>3.3、Elasticsearch配置</h3>
<p><strong>环境说明：</strong></p>
<p>1）  根据“2.2节Elasticsearch安装”说明完成安装；</p>
<p>2）  用crul方式创建索引模板，</p>
<p>用户操作日志模板示例如下：</p>
<div style="background:#E7E6E6;">
<p>curl -XPUTlocalhost:9200/_template/template_flume_log -d '  </p>
<p>{ </p>
<p>   "template" : "test_index-*",  </p>
<p>   "settings" : {  </p>
<p>        "number_of_shards" : 5  </p>
<p>   },  </p>
<p>   "mappings" : {  </p>
<p>        "test_type_1" : {  </p>
<p>            "_source" :{"enabled" : true },</p>
<p>         "properties": {</p>
<p>                   "createDate": {</p>
<p>                     "type" :   "date",</p>
<p>                     "date_format":"date_hour_minute_second_millis"</p>
<p>                   },</p>
<p>                   "sex": {</p>
<p>                     "type" :   "string"</p>
<p>                   },</p>
<p>                   "userName": {</p>
<p>                     "type" :   "string"</p>
<p>                   },</p>
<p>                   "age": {</p>
<p>                     "type" :   "long"</p>
<p>                   }</p>
<p>           }</p>
<p>        } </p>
<p>   }  </p>
<p>} </p>
<p>'</p>
</div>
<p> </p>
<p>Tomcat访问日志模板如下：</p>
<div style="background:#E7E6E6;">
<p>curl -XPUTlocalhost:9200/_template/template_flume_tomcat_log -d '  </p>
<p>{ </p>
<p>   "template" : "test_index_tomcat-*",  </p>
<p>   "settings" : {  </p>
<p>        "number_of_shards" : 5  </p>
<p>   },  </p>
<p>   "mappings" : {  </p>
<p>        "test_type_1" : {  </p>
<p>            "_source" : {"enabled": true },</p>
<p>         "properties": {</p>
<p>                   "createTime": {</p>
<p>                     "type" :   "date",</p>
<p>                     "date_format":"date_hour_minute_second_millis"</p>
<p>                   },</p>
<p>                   "remoteIP": {</p>
<p>                     "type" :   "string"</p>
<p>                   },</p>
<p>                   "status": {</p>
<p>                     "type" :   "long"</p>
<p>                   },</p>
<p>                   "contentLength": {</p>
<p>                     "type" :   "string"</p>
<p>                   },</p>
<p>                   "duration": {</p>
<p>                     "type" :   "long"</p>
<p>                   },</p>
<p>                   "requestURL": {</p>
<p>                     "type" :   "string"</p>
<p>                   },</p>
<p>                   "logType": {</p>
<p>                     "type" :   "string"</p>
<p>                   }</p>
<p>           }</p>
<p>        } </p>
<p>   }  </p>
<p>} </p>
<p>'</p>
</div>
<p> </p>
<h3>3.4、Kibana配置</h3>
<p><strong>环境说明：</strong></p>
<p>1）根据“2.3节Kibana安装”完成Kibana安装；</p>
<p>2）访问<a href="http://192.168.77.113:5601/" rel="nofollow">http://192.168.77.113:5601/</a>，完成kibana索引配置；</p>
<p> </p>
<h2>4、常见问题说明</h2>
<p>1、elasticsearch启动不成功</p>
<p>原因：很大可能是因为jdk版本不对</p>
<p>解决：更新jdk版本</p>
<p> </p>
<p>2、flume向elasticsearch写数据，flume启动报：No nodeavailable and cluster/nodes/info] request_id [0] timed out</p>
<p>原因：1、可能是节点配置信息有误，检查节点配置信息，确认没有问题重启es;</p>
<p>           2、检查elasticsearch的jdk版本与应用的jdk版本是否一致；</p>
<p> </p>
<p>3、tail 断点续传的问题</p>
<p>可以在 tail 传的时候记录行号，下次再传的时候，取上次记录的位置开始传输，类似：</p>
<p>agent1.sources.avro-source1.command =/usr/local/bin/tail  -n +$(tail -n1/home/storm/tmp/n) --max-unchanged-stats=600 -F  /home/storm/tmp/id.txt | awk'ARNGIND==1{i=$0;next}{i++; if($0~/文件已截断/)i=0; print i&gt;&gt; "/home/storm/tmp/n";print $1"---"i}' /home/storm/tmp/n-</p>
<p>需要注意如下几点：</p>
<p>1）文件被 rotation 的时候，需要同步更新你的断点记录“指针”；</p>
<p>2）需要按文件名来追踪文件；</p>
<p>3）flume 挂掉后需要累加断点续传“指针”；</p>
<p>4）flume 挂掉后，如果恰好文件被rotation，那么会有丢数据的风险</p>
<p>   只能监控尽快拉起或者加逻辑判断文件大小重置指针；</p>
<p>5）tail 注意你的版本，请更新coreutils 包到最新。</p>
<p> </p>
<p>4、flume 报错：java.lang.OutOfMemoryError:GC overhead limit exceeded</p>
<p>解决：Flume 启动时的最大堆内存大小默认是 20M，线上环境很容易 OOM，因此需要你在 flume-env.sh 中添加 JVM 启动参数:</p>
<div style="background:#E7E6E6;">
<p>JAVA_OPTS="-Xms8192m -Xmx8192m -Xss256k-Xmn2g -XX:+UseParNewGC -XX:+UseConcMarkSweepGC -XX:-UseGCOverheadLimit"</p>
</div>
<p>然后在启动 agent 的时候一定要带上 -c conf 选项，否则 flume-env.sh 里配置的环境变量不会被加载生效。</p>
<p> </p>
<p>5、关于elasticsearch和kibana的时区和日期问题</p>
<p>原因：elasticsearch原生支持date类型，json格式通过字符来表示date类型。date类型是包含时区信息的，如果我们没有在json代表日期的字符串中显式指定时区，对es来说没什么问题，但是如果通过kibana显示es里的数据时，就会出现问题，数据的时间会晚8个小时。因为kibana从es里读取的date类型数据，没有时区信息，kibana会默认当作0时区来解析，但是kibana在通过浏览器展示的时候，会通过js获取当前客户端机器所在的时区，也就是东八区，所以kibana会把从es得到的日期数据减去8小时。这里就会导致kibana经常遇到的“数据时间延迟8小时”的问题。</p>
<p>解决：在往es提交日期数据的时候，直接提交带有时区信息的日期字符串，如：“2016-07-15T12:58:17.136+0800”。</p>
<p> </p>
<h2>5、参考文档</h2>
<p>1）Kibana使用参考文档：http://kibana.logstash.es/content/kibana/index.html</p>
<p>2）Elasticsearch使用参考文档：<a href="http://es.xiaoleilu.com/030_Data/45_Partial_update.html" rel="nofollow">http://es.xiaoleilu.com/030_Data/45_Partial_update.html</a></p>
<p>3）flume用户使用参考文档：<a href="http://flume.apache.org/FlumeUserGuide.html" rel="nofollow">http://flume.apache.org/FlumeUserGuide.html</a></p>
<p>4）flume开发者参考文档：http://flume.apache.org/FlumeDeveloperGuide.html</p>
<p> </p>
<p> </p>
            </div>
                </div>