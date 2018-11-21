---
layout:     post
title:      【Flume】Flume 简单理解及使用实例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/w1992wishes/article/details/81905023				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2><a id="Flume_0"></a>一、Flume简介</h2>
<p>**flume 是一个cloudera提供的 高可用高可靠，分布式的海量日志收集聚合传输系统。**原名是 <strong>Flume OG</strong> (original generation)，但随着 FLume 功能的扩展，Flume OG 代码工程臃肿、核心组件设计不合理、核心配置不标准等缺点暴露出来，尤其是在 Flume OG 的最后一个发行版本 0.94.0 中，日志传输不稳定的现象尤为严重，为了解决这些问题，2011 年 10 月 22 号，cloudera 完成了 Flume-728，对 Flume 进行了里程碑式的改动：重构核心组件、核心配置以及代码架构，重构后的版本统称为 <strong>Flume NG</strong>（next generation，改动的另一原因是将 Flume 纳入 apache 旗下，cloudera Flume 改名为 Apache Flume）。</p>
<h2><a id="Flume_OG__Flume_NG_4"></a>二、Flume OG 到 Flume NG</h2>
<p><strong>FLUM OG：</strong></p>
<ul>
<li>FLUM OG 有三种角色的节点：代理节点（agent）、收集节点（collector）、主节点（master）。</li>
<li>agent 从各个数据源收集日志数据，将收集到的数据集中到 Collector，然后由收集节点汇总存入 HDFS。master 负责管理 agent，collector 的活动。</li>
<li>agent、collector 都称为 node，node 的角色根据配置的不同分为 logical node（逻辑节点）、physical node（物理节点）。</li>
<li>agent、collector 由 source、sink 组成，代表在当前节点数据是从 source 传送到 sink。</li>
</ul>
<p>窃取网上一张图用以说明：</p>
<p><img src="https://img-blog.csdnimg.cn/20181105143126966.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3cxOTkyd2lzaGVz,size_16,color_FFFFFF,t_70" alt=""></p>
<p>Flume NG 取消了集中管理配置的 Master 和 Zookeeper，变为一个纯粹的传输工具。Flume NG 另一个主要的不同点是读入数据和写出数据现在由不同的工作线程处理（称为Runner）。在 Flume OG 中，读入线程同样做写出工作（除了故障重试）,如果写出慢的话（不是完全失败），它将阻塞 Flume 接收数据的能力。这种异步的设计使读入线程可以顺畅的工作而无需关注下游的任何问题。</p>
<p><strong>FLUME NG：</strong></p>
<ul>
<li>NG 只有一种角色的节点：代理节点（agent）。</li>
<li>没有 collector、master 节点，这是核心组件最核心的变化。</li>
<li>去除了 physical nodes、logical nodes 的概念和相关内容。</li>
<li>agent 节点的组成也发生了变化。Flume NG 的 agent 由 source、sink、Channel 组成。</li>
</ul>
<p>flume ng 节点组成图:</p>
<p><img src="https://img-blog.csdnimg.cn/20181105143135109.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3cxOTkyd2lzaGVz,size_16,color_FFFFFF,t_70" alt=""></p>
<p>多 Agent 并联下的架构图:</p>
<p><img src="https://img-blog.csdnimg.cn/2018110514314250.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3cxOTkyd2lzaGVz,size_16,color_FFFFFF,t_70" alt=""></p>
<h2><a id="Flume__34"></a>三、Flume 的特性</h2>
<p>flume 支持在日志系统中定制各类数据发送方，用于收集数据；同时支持对数据进行简单处理，并写到各种数据接受方(比如文本、HDFS、Hbase等)的能力 。</p>
<p>flume 的数据流由事件(Event)贯穿始终。事件是 Flume 的基本数据单位，它携带日志数据(字节数组形式)并且携带有头信息，这些 Event 由 Agent 外部的 Source 生成，当 Source 捕获事件后会进行特定的格式化，然后 Source 会把事件推入(单个或多个) Channel 中。可以把 Channel 看作是一个缓冲区，它将保存事件直到 Sink 处理完该事件。</p>
<p>Sink 负责持久化日志或者把事件推向另一个 Source。</p>
<p>flume 具备高可靠性：</p>
<p>当节点出现故障时，日志能够被传送到其他节点上而不会丢失。Flume提供了三种级别的可靠性保障，从强到弱依次分别为：</p>
<ul>
<li>end-to-end：收到数据agent首先将event写到磁盘上，当数据传送成功后，再删除；如果数据发送失败，可以重新发送。</li>
<li>Store on failure：这也是scribe采用的策略，当数据接收方crash崩溃时，将数据写到本地，待恢复后，继续发送。</li>
<li>Best effort：数据发送到接收方后，不会进行确认。</li>
</ul>
<h2><a id="Flume__50"></a>四、Flume 架构组成和核心概念</h2>
<ul>
<li><strong>client</strong>: 生产数据的地方，运行在一个独立的线程。</li>
<li><strong>event</strong>: 生产的数据，可以是日志记录、 avro 对象等，如果是文本文件通常是一行记录。</li>
<li><strong>Flow</strong>： Event从源点到达目的点的迁移的抽象。</li>
<li><strong>agent</strong>: flume 核心组件，flume 以 Agent 为最小的独立运行单位。一个 agent 就是一个 jvm， agent 又是由 source， channel， sink 等构建而成。</li>
</ul>
<p>agent 由 source， channel， sink 等构建而成：</p>
<h3><a id="41_Source_Client__Channel_59"></a>4.1 Source：从 Client 收集数据，传递给 Channel</h3>
<p>不同的 source，可以接受不同的数据格式，比如监视外部源–目录池(spooling directory)数据源，可以监控指定文件夹中的新文件变化，如果目录中有文件产生，就会立刻读取其内容。 source 组件可以处理各种格式的日志数据，eg:avro Sources、thrift Sources、exec、jms、spooling directory、netcat、sequence generator、syslog、http、legacy、自定义。</p>
<pre><code>Source类型                   说明 

Avro Source                  支持Avro协议（实际上是Avro RPC），内置支持| 
Thrift Source                支持Thrift协议，内置支持
Exec Source                  基于Unix的command在标准输出上生产数据
JMS Source                   从JMS系统（消息、主题）中读取数据
Spooling Directory Source    监控指定目录内数据变更
Twitter 1% firehose Source   通过API持续下载Twitter数据，试验性质
Netcat Source                监控某个端口，将流经端口的每一个文本行数据作为Event输入
Sequence Generator Source    序列生成器数据源，生产序列数据
Syslog Sources               读取syslog数据，产生Event，支持UDP和TCP两种协议
HTTP Source                  基于HTTP POST或GET方式的数据源，支持JSON、BLOB表示形式
Legacy Sources               兼容老的Flume OG中Source（0.9.x版本）
</code></pre>
<p>详细参考官网：<a href="http://flume.apache.org/FlumeUserGuide.html#flume-sources" rel="nofollow">http://flume.apache.org/FlumeUserGuide.html#flume-sources</a></p>
<h3><a id="42Channel_sources__sinks_79"></a>4.2、Channel：连接 sources 和 sinks</h3>
<p>有点像一个队列，是一个存储池，接收 source 的输出，直到有 sink 消费掉 channel 中的数据，channel 中的数据直到进入下一个 channel 或者进入 sink 才会被删除，当 sink 写入失败后，可以自动重启，不会造成数据丢失。临时存放的数据可以存放在memory Channel、jdbc Channel、file Channel、自定义。</p>
<pre><code>Channel类型                   说明

Memory Channel                Event数据存储在内存中
JDBC Channel                  Event数据存储在持久化存储中，当前Flume Channel内置支持Derby
File Channel                  Event数据存储在磁盘文件中
Spillable Memory Channel      Event数据存储在内存中和磁盘上，当内存队列满了，会持久化到磁盘文件
Pseudo Transaction Channel    测试用途
Custom Channel                自定义Channel实现
</code></pre>
<p>详细参考官网：<a href="http://flume.apache.org/FlumeUserGuide.html#flume-channels" rel="nofollow">http://flume.apache.org/FlumeUserGuide.html#flume-channels</a></p>
<h3><a id="43SinkChannel_94"></a>4.3、Sink：从Channel收集数据，运行在一个独立线程</h3>
<p>用于把数据发送到目的地的组件目的地包括 hdfs、logger、avro、thrift、ipc、file、null、hbase、solr、自定义。</p>
<p>详细参考官网：<a href="http://flume.apache.org/FlumeUserGuide.html#flume-sinks" rel="nofollow">http://flume.apache.org/FlumeUserGuide.html#flume-sinks</a></p>
<p>flume可以支持：</p>
<ul>
<li>多级 flume的 agent，(即多个 flume 可以连成串，上一个 flume 可以把数据写到下一个 flume 上)</li>
<li>支持扇入(fan-in)：source 可以接受多个输入</li>
<li>扇出(fan-out)：sink 可以输出到多个目的地</li>
</ul>
<h2><a id="Flume__107"></a>五、Flume 使用实例</h2>
<p>模拟使用 Flume 监听日志变化，并且把增量的日志文件写入到 hdfs 中。</p>
<p>根据需求，首先定义一下3大要素：</p>
<ul>
<li>Source：监控日志文件内容更新，Exec Source（tail -f “file”）</li>
<li>Sink：HDFS文件系统，hdfs sink</li>
<li>Channel：Source和sink之间的传递通道，可用 file channel 也可以用 Memory channel</li>
</ul>
<h3><a id="51_117"></a>5.1、编写配置文件</h3>
<p>确定好了 Source/Sink/Channel 之后，开始编写配置文件。</p>
<pre><code>gedit exec_tail.conf
</code></pre>
<p>内容如下：</p>
<pre><code># Name the components on this agent
a1.sources = r1
a1.sinks = k1
a1.channels = c1

# Describe/configure the source
## exec表示flume调用给的命令，然后从给的命令的结果中去拿数据
a1.sources.r1.type = exec
## 使用tail这个命令来读数据
a1.sources.r1.command = tail -F /home/hadoop/testdir/flumedata/test.log
a1.sources.r1.channels = c1

# Describe the sink
## 表示下沉到hdfs，类型决定了下面的参数
a1.sinks.k1.type = hdfs
## sinks.k1只能连接一个channel，source可以配置多个
a1.sinks.k1.channel = c1
## 下面的配置告诉用hdfs去写文件的时候写到什么位置，下面的表示不是写死的，而是可以动态的变化的。表示输出的目录名称是可变的
a1.sinks.k1.hdfs.path = hdfs://master:9000/flume/tailout/%y-%m-%d/%H%M/
##表示最后的文件的前缀
a1.sinks.k1.hdfs.filePrefix = events-
## 表示到了需要触发的时间时，是否要更新文件夹，true:表示要
a1.sinks.k1.hdfs.round = true
## 表示每隔1分钟改变一次
a1.sinks.k1.hdfs.roundValue = 1
## 切换文件的时候的时间单位是分钟
a1.sinks.k1.hdfs.roundUnit = minute
## 表示只要过了3秒钟，就切换生成一个新的文件
a1.sinks.k1.hdfs.rollInterval = 3
## 如果记录的文件大于20字节时切换一次
a1.sinks.k1.hdfs.rollSize = 20
## 当写了5个事件时触发
a1.sinks.k1.hdfs.rollCount = 5
## 收到了多少条消息往dfs中追加内容
a1.sinks.k1.hdfs.batchSize = 10
## 使用本地时间戳
a1.sinks.k1.hdfs.useLocalTimeStamp = true
#生成的文件类型，默认是Sequencefile，可用DataStream：为普通文本
a1.sinks.k1.hdfs.fileType = DataStream

# Use a channel which buffers events in memory
##使用内存的方式
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
</code></pre>
<h3><a id="52_flume__177"></a>5.2、启动 flume 监控</h3>
<pre><code>cd /opt/flume-1.8.0
bin/flume-ng agent -c conf -f conf/exec_tail.conf -n a1 -Dflume.root.logger=INFO,console
</code></pre>
<p>命令参数解释：</p>
<ul>
<li><code>agent</code>：运行一个 Flume Agent</li>
<li><code>--conf / -c &lt;conf&gt;</code>：在<code>&lt;conf&gt;</code>目录使用配置文件，指定配置文件放在什么目录</li>
<li><code>--conf-file / -f &lt;file&gt;</code>：指定配置文件，这个配置文件必须在全局选项的 --conf 参数定义的目录下</li>
<li><code>--name / -n &lt;name&gt;</code>: Agent 的名称，同配置文件中的相对应</li>
</ul>
<h3><a id="53_190"></a>5.3、模拟日志</h3>
<pre><code>hadoop@master:/opt/flume-1.8.0$ cd /home/hadoop/testdir/flumedata/

hadoop@master:~/testdir/flumedata$ while true
&gt; do
&gt; date &gt;&gt; test.log
&gt; sleep 2
&gt; done
</code></pre>
<p>查看日志变化：</p>
<pre><code>hadoop@master:~/testdir/flumedata$ tail -f test.log 
Mon Aug 20 20:09:31 PDT 2018
Mon Aug 20 20:09:33 PDT 2018
Mon Aug 20 20:09:35 PDT 2018
Mon Aug 20 20:09:37 PDT 2018
Mon Aug 20 20:09:39 PDT 2018
Mon Aug 20 20:09:41 PDT 2018
Mon Aug 20 20:09:43 PDT 2018
Mon Aug 20 20:09:45 PDT 2018
</code></pre>
<p>通过 tail 命令，可以看到test.log在不停的追加数据。</p>
<p>到hdfs中进行查看，效果如下：</p>
<p><img src="https://img-blog.csdnimg.cn/20181105143155761.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3cxOTkyd2lzaGVz,size_16,color_FFFFFF,t_70" alt=""></p>
<p>也可以到页面查看：</p>
<p><img src="https://img-blog.csdnimg.cn/20181105143204280.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3cxOTkyd2lzaGVz,size_16,color_FFFFFF,t_70" alt=""></p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>