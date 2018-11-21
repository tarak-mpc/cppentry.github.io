---
layout:     post
title:      Flume(NG)架构设计要点及配置实践 	  Flume NG是一个分布式、可靠、可用的系统，它能够将不同数据源的海量日志数据进行高效收集
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 class="entry-title">Flume(NG)架构设计要点及配置实践</h1>
<div class="entry-meta"><span class="sep"></span><span class="byline"><span class="author vcard"></span></span></div>
<p>Flume NG是一个分布式、可靠、可用的系统，它能够将不同数据源的海量日志数据进行高效收集、聚合、移动，最后存储到一个中心化数据存储系统中。由原来的Flume OG到现在的Flume NG，进行了架构重构，并且现在NG版本完全不兼容原来的OG版本。经过架构重构后，Flume NG更像是一个轻量的小工具，非常简单，容易适应各种方式日志收集，并支持failover和负载均衡。</p>
<p><strong>架构设计要点</strong></p>
<p>Flume的架构主要有一下几个核心概念：</p>
<ul><li>Event：一个数据单元，带有一个可选的消息头</li><li>Flow：Event从源点到达目的点的迁移的抽象</li><li>Client：操作位于源点处的Event，将其发送到Flume Agent</li><li>Agent：一个独立的Flume进程，包含组件Source、Channel、Sink</li><li>Source：用来消费传递到该组件的Event</li><li>Channel：中转Event的一个临时存储，保存有Source组件传递过来的Event</li><li>Sink：从Channel中读取并移除Event，将Event传递到Flow Pipeline中的下一个Agent（如果有的话）</li></ul><p>Flume NG架构，如图所示：<br><img class="alignnone size-full wp-image-918" src="http://shiyanjuncn.b0.upaiyun.com/wp-content/uploads/2014/09/flume-ng-architecture.png" alt="flume-ng-architecture" width="646" height="252"><br>
外部系统产生日志，直接通过Flume的Agent的Source组件将事件（如日志行）发送到中间临时的channel组件，最后传递给Sink组件，HDFS Sink组件可以直接把数据存储到HDFS集群上。<br>
一个最基本Flow的配置，格式如下：</p>
<div id="highlighter_213793" class="syntaxhighlighter">
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>01</code></td>
<td class="content"><code class="plain"># list the sources, sinks and channels for the agent</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>02</code></td>
<td class="content"><code class="plain">&lt;Agent&gt;.sources = &lt;Source1&gt; &lt;Source2&gt;</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>03</code></td>
<td class="content"><code class="plain">&lt;Agent&gt;.sinks = &lt;Sink1&gt; &lt;Sink2&gt;</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>04</code></td>
<td class="content"><code class="plain">&lt;Agent&gt;.channels = &lt;Channel1&gt; &lt;Channel2&gt;</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>05</code></td>
<td class="content"> </td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>06</code></td>
<td class="content"><code class="plain"># set channel for source</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>07</code></td>
<td class="content"><code class="plain">&lt;Agent&gt;.sources.&lt;Source1&gt;.channels = &lt;Channel1&gt; &lt;Channel2&gt; ...</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>08</code></td>
<td class="content"><code class="plain">&lt;Agent&gt;.sources.&lt;Source2&gt;.channels = &lt;Channel1&gt; &lt;Channel2&gt; ...</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>09</code></td>
<td class="content"> </td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>10</code></td>
<td class="content"><code class="plain"># set channel for sink</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>11</code></td>
<td class="content"><code class="plain">&lt;Agent&gt;.sinks.&lt;Sink1&gt;.channel = &lt;Channel1&gt;</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>12</code></td>
<td class="content"><code class="plain">&lt;Agent&gt;.sinks.&lt;Sink2&gt;.channel = &lt;Channel2&gt;</code></td>
</tr></tbody></table></div>
</div>
</div>
<p>尖括号里面的，我们可以根据实际需求或业务来修改名称。下面详细说明：<br>
表示配置一个Agent的名称，一个Agent肯定有一个名称。与是Agent的Source组件的名称，消费传递过来的Event。与是Agent的Channel组件的名称。与是Agent的Sink组件的名称，从Channel中消费（移除）Event。<br>
上面配置内容中，第一组中配置Source、Sink、Channel，它们的值可以有1个或者多个；第二组中配置Source将把数据存储（Put）到哪一个Channel中，可以存储到1个或多个Channel中，同一个Source将数据存储到多个Channel中，实际上是Replication；第三组中配置Sink从哪一个Channel中取（Task）数据，一个Sink只能从一个Channel中取数据。<br>
下面，根据官网文档，我们展示几种Flow Pipeline，各自适应于什么样的应用场景：</p>
<ul><li>多个Agent顺序连接</li></ul><p><img class="alignnone size-full wp-image-919" src="http://shiyanjuncn.b0.upaiyun.com/wp-content/uploads/2014/09/flume-multiseq-agents.png" alt="flume-multiseq-agents" width="642" height="139"><br>
可以将多个Agent顺序连接起来，将最初的数据源经过收集，存储到最终的存储系统中。这是最简单的情况，一般情况下，应该控制这种顺序连接的Agent的数量，因为数据流经的路径变长了，如果不考虑failover的话，出现故障将影响整个Flow上的Agent收集服务。</p>
<ul><li>多个Agent的数据汇聚到同一个Agent</li></ul><p><img class="alignnone size-full wp-image-920" src="http://shiyanjuncn.b0.upaiyun.com/wp-content/uploads/2014/09/flume-join-agent.png" alt="flume-join-agent" width="644" height="444"><br>
这种情况应用的场景比较多，比如要收集Web网站的用户行为日志，Web网站为了可用性使用的负载均衡的集群模式，每个节点都产生用户行为日志，可以为每个节点都配置一个Agent来单独收集日志数据，然后多个Agent将数据最终汇聚到一个用来存储数据存储系统，如HDFS上。</p>
<ul><li>多路（Multiplexing）Agent</li></ul><p><img class="alignnone size-full wp-image-921" src="http://shiyanjuncn.b0.upaiyun.com/wp-content/uploads/2014/09/flume-multiplexing-agent.png" alt="flume-multiplexing-agent" width="649" height="388"><br>
这种模式，有两种方式，一种是用来复制（Replication），另一种是用来分流（Multiplexing）。Replication方式，可以将最前端的数据源复制多份，分别传递到多个channel中，每个channel接收到的数据都是相同的，配置格式，如下所示：</p>
<div id="highlighter_219259" class="syntaxhighlighter">
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>01</code></td>
<td class="content"><code class="plain"># List the sources, sinks and channels for the agent</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>02</code></td>
<td class="content"><code class="plain">&lt;Agent&gt;.sources = &lt;Source1&gt;</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>03</code></td>
<td class="content"><code class="plain">&lt;Agent&gt;.sinks = &lt;Sink1&gt; &lt;Sink2&gt;</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>04</code></td>
<td class="content"><code class="plain">&lt;Agent&gt;.channels = &lt;Channel1&gt; &lt;Channel2&gt;</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>05</code></td>
<td class="content"> </td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>06</code></td>
<td class="content"><code class="plain"># set list of channels for source (separated by space)</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>07</code></td>
<td class="content"><code class="plain">&lt;Agent&gt;.sources.&lt;Source1&gt;.channels = &lt;Channel1&gt; &lt;Channel2&gt;</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>08</code></td>
<td class="content"> </td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>09</code></td>
<td class="content"><code class="plain"># set channel for sinks</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>10</code></td>
<td class="content"><code class="plain">&lt;Agent&gt;.sinks.&lt;Sink1&gt;.channel = &lt;Channel1&gt;</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>11</code></td>
<td class="content"><code class="plain">&lt;Agent&gt;.sinks.&lt;Sink2&gt;.channel = &lt;Channel2&gt;</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>12</code></td>
<td class="content"> </td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>13</code></td>
<td class="content"><code class="plain">&lt;Agent&gt;.sources.&lt;Source1&gt;.selector.type = replicating</code></td>
</tr></tbody></table></div>
</div>
</div>
<p>上面指定了selector的type的值为replication，其他的配置没有指定，使用的Replication方式，Source1会将数据分别存储到Channel1和Channel2，这两个channel里面存储的数据是相同的，然后数据被传递到Sink1和Sink2。<br>
Multiplexing方式，selector可以根据header的值来确定数据传递到哪一个channel，配置格式，如下所示：</p>
<div id="highlighter_186622" class="syntaxhighlighter">
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>1</code></td>
<td class="content"><code class="plain"># Mapping for multiplexing selector</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>2</code></td>
<td class="content"><code class="plain">&lt;Agent&gt;.sources.&lt;Source1&gt;.selector.type = multiplexing</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>3</code></td>
<td class="content"><code class="plain">&lt;Agent&gt;.sources.&lt;Source1&gt;.selector.header = &lt;someHeader&gt;</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>4</code></td>
<td class="content"><code class="plain">&lt;Agent&gt;.sources.&lt;Source1&gt;.selector.mapping.&lt;Value1&gt; = &lt;Channel1&gt;</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>5</code></td>
<td class="content"><code class="plain">&lt;Agent&gt;.sources.&lt;Source1&gt;.selector.mapping.&lt;Value2&gt; = &lt;Channel1&gt; &lt;Channel2&gt;</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>6</code></td>
<td class="content"><code class="plain">&lt;Agent&gt;.sources.&lt;Source1&gt;.selector.mapping.&lt;Value3&gt; = &lt;Channel2&gt;</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>7</code></td>
<td class="content"><code class="plain">#...</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>8</code></td>
<td class="content"> </td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>9</code></td>
<td class="content"><code class="plain">&lt;Agent&gt;.sources.&lt;Source1&gt;.selector.default = &lt;Channel2&gt;</code></td>
</tr></tbody></table></div>
</div>
</div>
<p>上面selector的type的值为multiplexing，同时配置selector的header信息，还配置了多个selector的mapping的值，即header的值：如果header的值为Value1、Value2，数据从Source1路由到Channel1；如果header的值为Value2、Value3，数据从Source1路由到Channel2。</p>
<ul><li>实现load balance功能</li></ul><p><img class="alignnone size-full wp-image-922" src="http://shiyanjuncn.b0.upaiyun.com/wp-content/uploads/2014/09/flume-load-balance-agents.png" alt="flume-load-balance-agents" width="973" height="501"><br>
Load balancing Sink Processor能够实现load balance功能，上图Agent1是一个路由节点，负责将Channel暂存的Event均衡到对应的多个Sink组件上，而每个Sink组件分别连接到一个独立的Agent上，示例配置，如下所示：</p>
<div id="highlighter_486068" class="syntaxhighlighter">
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>1</code></td>
<td class="content"><code class="plain">a1.sinkgroups = g1</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>2</code></td>
<td class="content"><code class="plain">a1.sinkgroups.g1.sinks = k1 k2 k3</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>3</code></td>
<td class="content"><code class="plain">a1.sinkgroups.g1.processor.type = load_balance</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>4</code></td>
<td class="content"><code class="plain">a1.sinkgroups.g1.processor.backoff = true</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>5</code></td>
<td class="content"><code class="plain">a1.sinkgroups.g1.processor.selector = round_robin</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>6</code></td>
<td class="content"><code class="plain">a1.sinkgroups.g1.processor.selector.maxTimeOut=10000</code></td>
</tr></tbody></table></div>
</div>
</div>
<ul><li>实现failover能</li></ul><p>Failover Sink Processor能够实现failover功能，具体流程类似load balance，但是内部处理机制与load balance完全不同：Failover Sink Processor维护一个优先级Sink组件列表，只要有一个Sink组件可用，Event就被传递到下一个组件。如果一个Sink能够成功处理Event，则会加入到一个Pool中，否则会被移出Pool并计算失败次数，设置一个惩罚因子，示例配置如下所示：</p>
<div id="highlighter_280303" class="syntaxhighlighter">
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>1</code></td>
<td class="content"><code class="plain">a1.sinkgroups = g1</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>2</code></td>
<td class="content"><code class="plain">a1.sinkgroups.g1.sinks = k1 k2 k3</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>3</code></td>
<td class="content"><code class="plain">a1.sinkgroups.g1.processor.type = failover</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>4</code></td>
<td class="content"><code class="plain">a1.sinkgroups.g1.processor.priority.k1 = 5</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>5</code></td>
<td class="content"><code class="plain">a1.sinkgroups.g1.processor.priority.k2 = 7</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>6</code></td>
<td class="content"><code class="plain">a1.sinkgroups.g1.processor.priority.k3 = 6</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>7</code></td>
<td class="content"><code class="plain">a1.sinkgroups.g1.processor.maxpenalty = 20000</code></td>
</tr></tbody></table></div>
</div>
</div>
<p><strong>基本功能</strong></p>
<p>我们看一下，Flume NG都支持哪些功能（目前最新版本是1.5.0.1），了解它的功能集合，能够让我们在应用中更好地选择使用哪一种方案。说明Flume NG的功能，实际还是围绕着Agent的三个组件Source、Channel、Sink来看它能够支持哪些技术或协议。我们不再对各个组件支持的协议详细配置进行说明，通过列表的方式分别对三个组件进行概要说明：</p>
<ul><li>Flume Source</li></ul><table width="100%" cellspacing="0" cellpadding="2" border="1" style="border:solid thin #868686;"><tbody><tr><td valign="top" style="text-align:center;border:solid thin #868686;">
<strong>Source类型</strong></td>
<td valign="top" style="text-align:center;border:solid thin #868686;">
<strong>说明</strong></td>
</tr><tr><td valign="top" style="border:solid thin #868686;">Avro Source</td>
<td valign="top" style="border:solid thin #868686;">支持Avro协议（实际上是Avro RPC），内置支持</td>
</tr><tr><td valign="top" style="border:solid thin #868686;">Thrift Source</td>
<td valign="top" style="border:solid thin #868686;">支持Thrift协议，内置支持</td>
</tr><tr><td valign="top" style="border:solid thin #868686;">Exec Source</td>
<td valign="top" style="border:solid thin #868686;">基于Unix的command在标准输出上生产数据</td>
</tr><tr><td valign="top" style="border:solid thin #868686;">JMS Source</td>
<td valign="top" style="border:solid thin #868686;">从JMS系统（消息、主题）中读取数据，ActiveMQ已经测试过</td>
</tr><tr><td valign="top" style="border:solid thin #868686;">Spooling Directory Source</td>
<td valign="top" style="border:solid thin #868686;">监控指定目录内数据变更</td>
</tr><tr><td valign="top" style="border:solid thin #868686;">Twitter 1% firehose Source</td>
<td valign="top" style="border:solid thin #868686;">通过API持续下载Twitter数据，试验性质</td>
</tr><tr><td valign="top" style="border:solid thin #868686;">Netcat Source</td>
<td valign="top" style="border:solid thin #868686;">监控某个端口，将流经端口的每一个文本行数据作为Event输入</td>
</tr><tr><td valign="top" style="border:solid thin #868686;">Sequence Generator Source</td>
<td valign="top" style="border:solid thin #868686;">序列生成器数据源，生产序列数据</td>
</tr><tr><td valign="top" style="border:solid thin #868686;">Syslog Sources</td>
<td valign="top" style="border:solid thin #868686;">读取syslog数据，产生Event，支持UDP和TCP两种协议</td>
</tr><tr><td valign="top" style="border:solid thin #868686;">HTTP Source</td>
<td valign="top" style="border:solid thin #868686;">基于HTTP POST或GET方式的数据源，支持JSON、BLOB表示形式</td>
</tr><tr><td valign="top" style="border:solid thin #868686;">Legacy Sources</td>
<td valign="top" style="border:solid thin #868686;">兼容老的Flume OG中Source（0.9.x版本）</td>
</tr></tbody></table><ul><li>Flume Channel</li></ul><table width="100%" cellspacing="0" cellpadding="2" border="1" style="border:solid thin #868686;"><tbody><tr><td valign="top" style="text-align:center;border:solid thin #868686;">
<strong>Channel类型</strong></td>
<td valign="top" style="text-align:center;border:solid thin #868686;">
<strong>说明</strong></td>
</tr><tr><td valign="top" style="border:solid thin #868686;">Memory Channel</td>
<td valign="top" style="border:solid thin #868686;">Event数据存储在内存中</td>
</tr><tr><td valign="top" style="border:solid thin #868686;">JDBC Channel</td>
<td valign="top" style="border:solid thin #868686;">Event数据存储在持久化存储中，当前Flume Channel内置支持Derby</td>
</tr><tr><td valign="top" style="border:solid thin #868686;">File Channel</td>
<td valign="top" style="border:solid thin #868686;">Event数据存储在磁盘文件中</td>
</tr><tr><td valign="top" style="border:solid thin #868686;">Spillable Memory Channel</td>
<td valign="top" style="border:solid thin #868686;">Event数据存储在内存中和磁盘上，当内存队列满了，会持久化到磁盘文件（当前试验性的，不建议生产环境使用）</td>
</tr><tr><td valign="top" style="border:solid thin #868686;">Pseudo Transaction Channel</td>
<td valign="top" style="border:solid thin #868686;">测试用途</td>
</tr><tr><td valign="top" style="border:solid thin #868686;">Custom Channel</td>
<td valign="top" style="border:solid thin #868686;">自定义Channel实现</td>
</tr></tbody></table><ul><li>Flume Sink</li></ul><table width="100%" cellspacing="0" cellpadding="2" border="1" style="border:solid thin #868686;"><tbody><tr><td valign="top" style="text-align:center;border:solid thin #868686;">
<strong>Sink类型</strong></td>
<td valign="top" style="text-align:center;border:solid thin #868686;">
<strong>说明</strong></td>
</tr><tr><td valign="top" style="border:solid thin #868686;">HDFS Sink</td>
<td valign="top" style="border:solid thin #868686;">数据写入HDFS</td>
</tr><tr><td valign="top" style="border:solid thin #868686;">Logger Sink</td>
<td valign="top" style="border:solid thin #868686;">数据写入日志文件</td>
</tr><tr><td valign="top" style="border:solid thin #868686;">Avro Sink</td>
<td valign="top" style="border:solid thin #868686;">数据被转换成Avro Event，然后发送到配置的RPC端口上</td>
</tr><tr><td valign="top" style="border:solid thin #868686;">Thrift Sink</td>
<td valign="top" style="border:solid thin #868686;">数据被转换成Thrift Event，然后发送到配置的RPC端口上</td>
</tr><tr><td valign="top" style="border:solid thin #868686;">IRC Sink</td>
<td valign="top" style="border:solid thin #868686;">数据在IRC上进行回放</td>
</tr><tr><td valign="top" style="border:solid thin #868686;">File Roll Sink</td>
<td valign="top" style="border:solid thin #868686;">存储数据到本地文件系统</td>
</tr><tr><td valign="top" style="border:solid thin #868686;">Null Sink</td>
<td valign="top" style="border:solid thin #868686;">丢弃到所有数据</td>
</tr><tr><td valign="top" style="border:solid thin #868686;">HBase Sink</td>
<td valign="top" style="border:solid thin #868686;">数据写入HBase数据库</td>
</tr><tr><td valign="top" style="border:solid thin #868686;">Morphline Solr Sink</td>
<td valign="top" style="border:solid thin #868686;">数据发送到Solr搜索服务器（集群）</td>
</tr><tr><td valign="top" style="border:solid thin #868686;">ElasticSearch Sink</td>
<td valign="top" style="border:solid thin #868686;">数据发送到Elastic Search搜索服务器（集群）</td>
</tr><tr><td valign="top" style="border:solid thin #868686;">Kite Dataset Sink</td>
<td valign="top" style="border:solid thin #868686;">写数据到Kite Dataset，试验性质的</td>
</tr><tr><td valign="top" style="border:solid thin #868686;">Custom Sink</td>
<td valign="top" style="border:solid thin #868686;">自定义Sink实现</td>
</tr></tbody></table><p>另外还有Channel Selector、Sink Processor、Event Serializer、Interceptor等组件，可以参考官网提供的用户手册。</p>
<p><strong>应用实践</strong></p>
<p>安装Flume NG非常简单，我们使用最新的1.5.0.1版本，执行如下命令：</p>
<div id="highlighter_860255" class="syntaxhighlighter">
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>1</code></td>
<td class="content"><code class="plain">cd /usr/local</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>2</code></td>
<td class="content"><code class="plain">wget <a href="http://mirror.bit.edu.cn/apache/flume/1.5.0.1/apache-flume-1.5.0.1-bin.tar.gz" rel="nofollow">
http://mirror.bit.edu.cn/apache/flume/1.5.0.1/apache-flume-1.5.0.1-bin.tar.gz</a></code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>3</code></td>
<td class="content"><code class="plain">tar xvzf apache-flume-1.5.0.1-bin.tar.gz</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>4</code></td>
<td class="content"><code class="plain">cd apache-flume-1.5.0.1-bin</code></td>
</tr></tbody></table></div>
</div>
</div>
<p>如果需要使用到Hadoop集群，保证Hadoop相关的环境变量都已经正确配置，并且Hadoop集群可用。下面，通过一些实际的配置实例，来了解Flume的使用。为了简单期间，channel我们使用Memory类型的channel。</p>
<ul><li>Avro Source+Memory Channel+Logger Sink</li></ul><p>使用apache-flume-1.5.0.1自带的例子，使用Avro Source接收外部数据源，Logger作为sink，即通过Avro RPC调用，将数据缓存在channel中，然后通过Logger打印出调用发送的数据。<br>
配置Agent，修改配置文件conf/flume-conf.properties，内容如下：</p>
<div id="highlighter_911072" class="syntaxhighlighter">
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>01</code></td>
<td class="content"><code class="plain"># Define a memory channel called ch1 on agent1</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>02</code></td>
<td class="content"><code class="plain">agent1.channels.ch1.type = memory</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>03</code></td>
<td class="content"> </td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>04</code></td>
<td class="content"><code class="plain"># Define an Avro source called avro-source1 on agent1 and tell it</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>05</code></td>
<td class="content"><code class="plain"># to bind to 0.0.0.0:41414. Connect it to channel ch1.</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>06</code></td>
<td class="content"><code class="plain">agent1.sources.avro-source1.channels = ch1</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>07</code></td>
<td class="content"><code class="plain">agent1.sources.avro-source1.type = avro</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>08</code></td>
<td class="content"><code class="plain">agent1.sources.avro-source1.bind = 0.0.0.0</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>09</code></td>
<td class="content"><code class="plain">agent1.sources.avro-source1.port = 41414</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>10</code></td>
<td class="content"> </td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>11</code></td>
<td class="content"><code class="plain"># Define a logger sink that simply logs all events it receives</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>12</code></td>
<td class="content"><code class="plain"># and connect it to the other end of the same channel.</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>13</code></td>
<td class="content"><code class="plain">agent1.sinks.log-sink1.channel = ch1</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>14</code></td>
<td class="content"><code class="plain">agent1.sinks.log-sink1.type = logger</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>15</code></td>
<td class="content"> </td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>16</code></td>
<td class="content"><code class="plain"># Finally, now that we've defined all of our components, tell</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>17</code></td>
<td class="content"><code class="plain"># agent1 which ones we want to activate.</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>18</code></td>
<td class="content"><code class="plain">agent1.channels = ch1</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>19</code></td>
<td class="content"><code class="plain">agent1.channels.ch1.capacity = 1000</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>20</code></td>
<td class="content"><code class="plain">agent1.sources = avro-source1</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>21</code></td>
<td class="content"><code class="plain">agent1.sinks = log-sink1</code></td>
</tr></tbody></table></div>
</div>
</div>
<p>首先，启动Agent进程：</p>
<div id="highlighter_248448" class="syntaxhighlighter">
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>1</code></td>
<td class="content"><code class="plain">bin/flume-ng agent -c ./conf/ -f conf/flume-conf.properties -Dflume.root.logger=DEBUG,console -n agent1</code></td>
</tr></tbody></table></div>
</div>
</div>
<p>然后，启动Avro Client，发送数据：</p>
<div id="highlighter_466586" class="syntaxhighlighter">
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>1</code></td>
<td class="content"><code class="plain">bin/flume-ng avro-client -c ./conf/ -H 0.0.0.0 -p 41414 -F /usr/</code><code class="functions">local</code><code class="plain">/programs/logs/</code><code class="functions">sync</code><code class="plain">.log -Dflume.root.logger=DEBUG,console</code></td>
</tr></tbody></table></div>
</div>
</div>
<ul><li>Avro Source+Memory Channel+HDFS Sink</li></ul><p>配置Agent，修改配置文件conf/flume-conf-hdfs.properties，内容如下：</p>
<div id="highlighter_902517" class="syntaxhighlighter">
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>01</code></td>
<td class="content"><code class="plain"># Define a source, channel, sink</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>02</code></td>
<td class="content"><code class="plain">agent1.sources = avro-source1</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>03</code></td>
<td class="content"><code class="plain">agent1.channels = ch1</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>04</code></td>
<td class="content"><code class="plain">agent1.sinks = hdfs-sink</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>05</code></td>
<td class="content"> </td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>06</code></td>
<td class="content"><code class="plain"># Configure channel</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>07</code></td>
<td class="content"><code class="plain">agent1.channels.ch1.type = memory</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>08</code></td>
<td class="content"><code class="plain">agent1.channels.ch1.capacity = 1000000</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>09</code></td>
<td class="content"><code class="plain">agent1.channels.ch1.transactionCapacity = 500000</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>10</code></td>
<td class="content"> </td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>11</code></td>
<td class="content"><code class="plain"># Define an Avro source called avro-source1 on agent1 and tell it</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>12</code></td>
<td class="content"><code class="plain"># to bind to 0.0.0.0:41414. Connect it to channel ch1.</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>13</code></td>
<td class="content"><code class="plain">agent1.sources.avro-source1.channels = ch1</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>14</code></td>
<td class="content"><code class="plain">agent1.sources.avro-source1.type = avro</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>15</code></td>
<td class="content"><code class="plain">agent1.sources.avro-source1.bind = 0.0.0.0</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>16</code></td>
<td class="content"><code class="plain">agent1.sources.avro-source1.port = 41414</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>17</code></td>
<td class="content"> </td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>18</code></td>
<td class="content"><code class="plain"># Define a logger sink that simply logs all events it receives</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>19</code></td>
<td class="content"><code class="plain"># and connect it to the other end of the same channel.</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>20</code></td>
<td class="content"><code class="plain">agent1.sinks.hdfs-sink1.channel = ch1</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>21</code></td>
<td class="content"><code class="plain">agent1.sinks.hdfs-sink1.type = hdfs</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>22</code></td>
<td class="content"><code class="plain">agent1.sinks.hdfs-sink1.hdfs.path = <a href="" rel="nofollow">
hdfs://h1:8020/data/flume/</a></code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>23</code></td>
<td class="content"><code class="plain">agent1.sinks.hdfs-sink1.hdfs.filePrefix = sync_file</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>24</code></td>
<td class="content"><code class="plain">agent1.sinks.hdfs-sink1.hdfs.fileSuffix = .log</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>25</code></td>
<td class="content"><code class="plain">agent1.sinks.hdfs-sink1.hdfs.rollSize = 1048576</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>26</code></td>
<td class="content"><code class="plain">agent1.sinks.hdfs-sink1.rollInterval = 0</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>27</code></td>
<td class="content"><code class="plain">agent1.sinks.hdfs-sink1.hdfs.rollCount = 0</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>28</code></td>
<td class="content"><code class="plain">agent1.sinks.hdfs-sink1.hdfs.batchSize = 1500</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>29</code></td>
<td class="content"><code class="plain">agent1.sinks.hdfs-sink1.hdfs.round = true</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>30</code></td>
<td class="content"><code class="plain">agent1.sinks.hdfs-sink1.hdfs.roundUnit = minute</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>31</code></td>
<td class="content"><code class="plain">agent1.sinks.hdfs-sink1.hdfs.threadsPoolSize = 25</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>32</code></td>
<td class="content"><code class="plain">agent1.sinks.hdfs-sink1.hdfs.useLocalTimeStamp = true</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>33</code></td>
<td class="content"><code class="plain">agent1.sinks.hdfs-sink1.hdfs.minBlockReplicas = 1</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>34</code></td>
<td class="content"><code class="plain">agent1.sinks.hdfs-sink1.fileType = SequenceFile</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>35</code></td>
<td class="content"><code class="plain">agent1.sinks.hdfs-sink1.writeFormat = TEXT</code></td>
</tr></tbody></table></div>
</div>
</div>
<p>首先，启动Agent：</p>
<div id="highlighter_739170" class="syntaxhighlighter">
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>1</code></td>
<td class="content"><code class="plain">bin/flume-ng agent -c ./conf/ -f conf/flume-conf-hdfs.properties -Dflume.root.logger=INFO,console -n agent1</code></td>
</tr></tbody></table></div>
</div>
</div>
<p>然后，启动Avro Client，发送数据：</p>
<div id="highlighter_624148" class="syntaxhighlighter">
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>1</code></td>
<td class="content"><code class="plain">bin/flume-ng avro-client -c ./conf/ -H 0.0.0.0 -p 41414 -F /usr/</code><code class="functions">local</code><code class="plain">/programs/logs/</code><code class="functions">sync</code><code class="plain">.log -Dflume.root.logger=DEBUG,console</code></td>
</tr></tbody></table></div>
</div>
</div>
<p>可以查看同步到HDFS上的数据：</p>
<div id="highlighter_977064" class="syntaxhighlighter">
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>1</code></td>
<td class="content"><code class="plain">hdfs dfs -</code><code class="functions">ls</code><code class="plain">/data/flume</code></td>
</tr></tbody></table></div>
</div>
</div>
<p>结果示例，如下所示：</p>
<div id="highlighter_69385" class="syntaxhighlighter">
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>1</code></td>
<td class="content"><code class="plain">-rw-r--r--   3 shirdrn supergroup    1377617 2014-09-16 14:35 /data/flume/sync_file.1410849320761.log</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>2</code></td>
<td class="content"><code class="plain">-rw-r--r--   3 shirdrn supergroup    1378137 2014-09-16 14:35 /data/flume/sync_file.1410849320762.log</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>3</code></td>
<td class="content"><code class="plain">-rw-r--r--   3 shirdrn supergroup     259148 2014-09-16 14:35 /data/flume/sync_file.1410849320763.log</code></td>
</tr></tbody></table></div>
</div>
</div>
<ul><li>Spooling Directory Source+Memory Channel+HDFS Sink</li></ul><p>配置Agent，修改配置文件flume-conf-spool.properties，内容如下：</p>
<div id="highlighter_341835" class="syntaxhighlighter">
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>01</code></td>
<td class="content"><code class="plain"># Define source, channel, sink</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>02</code></td>
<td class="content"><code class="plain">agent1.sources = spool-source1</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>03</code></td>
<td class="content"><code class="plain">agent1.channels = ch1</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>04</code></td>
<td class="content"><code class="plain">agent1.sinks = hdfs-sink1</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>05</code></td>
<td class="content"> </td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>06</code></td>
<td class="content"><code class="plain"># Configure channel</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>07</code></td>
<td class="content"><code class="plain">agent1.channels.ch1.type = memory</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>08</code></td>
<td class="content"><code class="plain">agent1.channels.ch1.capacity = 1000000</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>09</code></td>
<td class="content"><code class="plain">agent1.channels.ch1.transactionCapacity = 500000</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>10</code></td>
<td class="content"> </td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>11</code></td>
<td class="content"><code class="plain"># Define and configure an Spool directory source</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>12</code></td>
<td class="content"><code class="plain">agent1.sources.spool-source1.channels = ch1</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>13</code></td>
<td class="content"><code class="plain">agent1.sources.spool-source1.type = spooldir</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>14</code></td>
<td class="content"><code class="plain">agent1.sources.spool-source1.spoolDir = /home/shirdrn/data/</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>15</code></td>
<td class="content"><code class="plain">agent1.sources.spool-source1.ignorePattern = event(_\d{4}\-\d{2}\-\d{2}_\d{2}_\d{2})?\.log(\.COMPLETED)?</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>16</code></td>
<td class="content"><code class="plain">agent1.sources.spool-source1.batchSize = 50</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>17</code></td>
<td class="content"><code class="plain">agent1.sources.spool-source1.inputCharset = UTF-8</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>18</code></td>
<td class="content"> </td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>19</code></td>
<td class="content"><code class="plain"># Define and configure a hdfs sink</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>20</code></td>
<td class="content"><code class="plain">agent1.sinks.hdfs-sink1.channel = ch1</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>21</code></td>
<td class="content"><code class="plain">agent1.sinks.hdfs-sink1.type = hdfs</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>22</code></td>
<td class="content"><code class="plain">agent1.sinks.hdfs-sink1.hdfs.path = <a href="" rel="nofollow">
hdfs://h1:8020/data/flume/</a></code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>23</code></td>
<td class="content"><code class="plain">agent1.sinks.hdfs-sink1.hdfs.filePrefix = event_%y-%m-%d_%H_%M_%S</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>24</code></td>
<td class="content"><code class="plain">agent1.sinks.hdfs-sink1.hdfs.fileSuffix = .log</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>25</code></td>
<td class="content"><code class="plain">agent1.sinks.hdfs-sink1.hdfs.rollSize = 1048576</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>26</code></td>
<td class="content"><code class="plain">agent1.sinks.hdfs-sink1.hdfs.rollCount = 0</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>27</code></td>
<td class="content"><code class="plain">agent1.sinks.hdfs-sink1.hdfs.batchSize = 1500</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>28</code></td>
<td class="content"><code class="plain">agent1.sinks.hdfs-sink1.hdfs.round = true</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>29</code></td>
<td class="content"><code class="plain">agent1.sinks.hdfs-sink1.hdfs.roundUnit = minute</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>30</code></td>
<td class="content"><code class="plain">agent1.sinks.hdfs-sink1.hdfs.threadsPoolSize = 25</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>31</code></td>
<td class="content"><code class="plain">agent1.sinks.hdfs-sink1.hdfs.useLocalTimeStamp = true</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>32</code></td>
<td class="content"><code class="plain">agent1.sinks.hdfs-sink1.hdfs.minBlockReplicas = 1</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>33</code></td>
<td class="content"><code class="plain">agent1.sinks.hdfs-sink1.fileType = SequenceFile</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>34</code></td>
<td class="content"><code class="plain">agent1.sinks.hdfs-sink1.writeFormat = TEXT</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>35</code></td>
<td class="content"><code class="plain">agent1.sinks.hdfs-sink1.rollInterval = 0</code></td>
</tr></tbody></table></div>
</div>
</div>
<p>启动Agent进程，执行如下命令：</p>
<div id="highlighter_967056" class="syntaxhighlighter">
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>1</code></td>
<td class="content"><code class="plain">bin/flume-ng agent -c ./conf/ -f conf/flume-conf-spool.properties -Dflume.root.logger=INFO,console -n agent1</code></td>
</tr></tbody></table></div>
</div>
</div>
<p>可以查看HDFS上同步过来的数据：</p>
<div id="highlighter_900058" class="syntaxhighlighter">
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>1</code></td>
<td class="content"><code class="plain">hdfs dfs -</code><code class="functions">ls</code><code class="plain">/data/flume</code></td>
</tr></tbody></table></div>
</div>
</div>
<p>结果示例，如下所示：</p>
<div id="highlighter_50057" class="syntaxhighlighter">
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>01</code></td>
<td class="content"><code class="plain">-rw-r--r--   3 shirdrn supergroup    1072265 2014-09-17 10:52 /data/flume/event_14-09-17_10_52_00.1410922355094.log</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>02</code></td>
<td class="content"><code class="plain">-rw-r--r--   3 shirdrn supergroup    1072265 2014-09-17 10:52 /data/flume/event_14-09-17_10_52_00.1410922355095.log</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>03</code></td>
<td class="content"><code class="plain">-rw-r--r--   3 shirdrn supergroup    1072265 2014-09-17 10:52 /data/flume/event_14-09-17_10_52_00.1410922355096.log</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>04</code></td>
<td class="content"><code class="plain">-rw-r--r--   3 shirdrn supergroup    1072265 2014-09-17 10:52 /data/flume/event_14-09-17_10_52_00.1410922355097.log</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>05</code></td>
<td class="content"><code class="plain">-rw-r--r--   3 shirdrn supergroup       1530 2014-09-17 10:53 /data/flume/event_14-09-17_10_52_00.1410922355098.log</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>06</code></td>
<td class="content"><code class="plain">-rw-r--r--   3 shirdrn supergroup    1072265 2014-09-17 10:53 /data/flume/event_14-09-17_10_53_00.1410922380386.log</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>07</code></td>
<td class="content"><code class="plain">-rw-r--r--   3 shirdrn supergroup    1072265 2014-09-17 10:53 /data/flume/event_14-09-17_10_53_00.1410922380387.log</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>08</code></td>
<td class="content"><code class="plain">-rw-r--r--   3 shirdrn supergroup    1072265 2014-09-17 10:53 /data/flume/event_14-09-17_10_53_00.1410922380388.log</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>09</code></td>
<td class="content"><code class="plain">-rw-r--r--   3 shirdrn supergroup    1072265 2014-09-17 10:53 /data/flume/event_14-09-17_10_53_00.1410922380389.log</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>10</code></td>
<td class="content"><code class="plain">-rw-r--r--   3 shirdrn supergroup    1072265 2014-09-17 10:53 /data/flume/event_14-09-17_10_53_00.1410922380390.log</code></td>
</tr></tbody></table></div>
</div>
</div>
<ul><li>Exec Source+Memory Channel+File Roll Sink</li></ul><p>配置Agent，修改配置文件flume-conf-file.properties，内容如下：</p>
<div id="highlighter_784257" class="syntaxhighlighter">
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>01</code></td>
<td class="content"><code class="plain"># Define source, channel, sink</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>02</code></td>
<td class="content"><code class="plain">agent1.sources = tail-source1</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>03</code></td>
<td class="content"><code class="plain">agent1.channels = ch1</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>04</code></td>
<td class="content"><code class="plain">agent1.sinks = file-sink1</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>05</code></td>
<td class="content"> </td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>06</code></td>
<td class="content"><code class="plain"># Configure channel</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>07</code></td>
<td class="content"><code class="plain">agent1.channels.ch1.type = memory</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>08</code></td>
<td class="content"><code class="plain">agent1.channels.ch1.capacity = 1000000</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>09</code></td>
<td class="content"><code class="plain">agent1.channels.ch1.transactionCapacity = 500000</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>10</code></td>
<td class="content"> </td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>11</code></td>
<td class="content"><code class="plain"># Define and configure an Exec source</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>12</code></td>
<td class="content"><code class="plain">agent1.sources.tail-source1.channels = ch1</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>13</code></td>
<td class="content"><code class="plain">agent1.sources.tail-source1.type = exec</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>14</code></td>
<td class="content"><code class="plain">agent1.sources.tail-source1.command = tail -F /home/shirdrn/data/event.log</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>15</code></td>
<td class="content"><code class="plain">agent1.sources.tail-source1.shell = /bin/sh -c</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>16</code></td>
<td class="content"><code class="plain">agent1.sources.tail-source1.batchSize = 50</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>17</code></td>
<td class="content"> </td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>18</code></td>
<td class="content"><code class="plain"># Define and configure a File roll sink</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>19</code></td>
<td class="content"><code class="plain"># and connect it to the other end of the same channel.</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>20</code></td>
<td class="content"><code class="plain">agent1.sinks.file-sink1.channel = ch1</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>21</code></td>
<td class="content"><code class="plain">agent1.sinks.file-sink1.type = file_roll</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>22</code></td>
<td class="content"><code class="plain">agent1.sinks.file-sink1.batchSize = 100</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>23</code></td>
<td class="content"><code class="plain">agent1.sinks.file-sink1.serializer = TEXT</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>24</code></td>
<td class="content"><code class="plain">agent1.sinks.file-sink1.sink.directory = /home/shirdrn/sink_data</code></td>
</tr></tbody></table></div>
</div>
</div>
<p>启动Agent进程，执行如下命令：</p>
<div id="highlighter_856663" class="syntaxhighlighter">
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>1</code></td>
<td class="content"><code class="plain">bin/flume-ng agent -c ./conf/ -f conf/flume-conf-</code><code class="functions">file</code><code class="plain">.properties -Dflume.root.logger=INFO,console -n agent1</code></td>
</tr></tbody></table></div>
</div>
</div>
<p>可以查看File Roll Sink对应的本地文件系统目录/home/shirdrn/sink_data下，示例如下所示：</p>
<div id="highlighter_84745" class="syntaxhighlighter">
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>1</code></td>
<td class="content"><code class="plain">-rw-rw-r-- 1 shirdrn shirdrn 13944825 Sep 17 11:36 1410924990039-1</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>2</code></td>
<td class="content"><code class="plain">-rw-rw-r-- 1 shirdrn shirdrn 11288870 Sep 17 11:37 1410924990039-2</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>3</code></td>
<td class="content"><code class="plain">-rw-rw-r-- 1 shirdrn shirdrn        0 Sep 17 11:37 1410924990039-3</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>4</code></td>
<td class="content"><code class="plain">-rw-rw-r-- 1 shirdrn shirdrn 20517500 Sep 17 11:38 1410924990039-4</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>5</code></td>
<td class="content"><code class="plain">-rw-rw-r-- 1 shirdrn shirdrn 16343250 Sep 17 11:38 1410924990039-5</code></td>
</tr></tbody></table></div>
</div>
</div>
<p>有关Flume NG更多配置及其说明，请参考官方用户手册，非常详细。</p>
<p><strong>参考链接</strong></p>
<ul><li><a href="http://flume.apache.org/FlumeUserGuide.html" rel="nofollow">http://flume.apache.org/FlumeUserGuide.html</a></li><li><a href="https://blogs.apache.org/flume/entry/flume_ng_architecture" rel="nofollow">https://blogs.apache.org/flume/entry/flume_ng_architecture</a></li></ul><p>转载自yangjun<br></p>
            </div>
                </div>