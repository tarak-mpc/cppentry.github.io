---
layout:     post
title:      大数据----【Flume、常用组件、load-balance、failover、日志采集汇总、Flume中小文件频繁滚动注意事项】
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1><a id="Flume_0"></a>Flume</h1>
<h2><a id="1__2"></a>1 概述</h2>
<p>Flume 是 Cloudera 提供的一个<code>高可用的，高可靠的</code>，<code>分布式的海量日志采集、聚合和传输</code>的<strong>软件</strong>。</p>
<p>Flume核心 :</p>
<ul>
<li>数据源(source)</li>
<li>目的地(sink)</li>
<li>数据传输通道(channel)</li>
</ul>
<p>Flume版本 :</p>
<ul>
<li>FlumeOG(0.9X版本的统称) , 老版本</li>
<li><code>FlumeNG</code>(1.X版本的统称) , 该版本常用</li>
</ul>
<h2><a id="2___17"></a>2 . 运行机制</h2>
<p>Flume 系统中核心的角色是 <code>agent</code>，agent 本身是一个 Java 进程，一般运行在日志收集节点。</p>
<p><img src="https://img-blog.csdnimg.cn/20181119210757180.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0NvZGVyQm9vbQ==,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<p>每一个 agent 相当于一个数据传递员，内部有三个组件：<br>
<code>Source</code>：采集源，用于跟数据源对接，以获取数据；<br>
<code>Sink</code>：下沉地，采集数据的传送目的，用于往下一级 agent 传递数据或者往最终存储系统传递数据；<br>
<code>Channel</code>：agent 内部的数据传输通道，用于从 source 将数据传递到 sink；在整个数据的传输的过程中，流动的是 <code>event</code>，它是 Flume 内部数据传输的<code>最基本单元</code></p>
<p>一个完整的 event 包括：<strong>event headers、event body、event 信息</strong>，其中event 信息就是 flume 收集到的日记记录。</p>
<h2><a id="3_Flume_30"></a>3. Flume采集结构图</h2>
<h4><a id="31_agent_32"></a>3.1 简单结构(单个agent)</h4>
<p><img src="https://img-blog.csdnimg.cn/20181119210803605.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0NvZGVyQm9vbQ==,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<h4><a id="32_agent_36"></a>3.2 复杂结构(多级agent串联)</h4>
<p><img src="https://img-blog.csdnimg.cn/20181119210810315.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0NvZGVyQm9vbQ==,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<h2><a id="4_Flume_42"></a>4. Flume的安装部署</h2>
<ul>
<li>将安装包放到/export/servers下 , 解压到当前文件夹</li>
<li>配置环境变量JAVA_HOME , 将配置文件中注释掉的JAVA_HOME放开并修改即可<code>export JAVA_HOME=/export/servers/jdk1.8.0_65</code><br>
<strong>测试 :</strong></li>
<li>1 、 先在 flume 的 的 conf 目录下新建一个文件<code>vi netcat-logger.conf</code></li>
</ul>
<pre><code class="prism language-shell"><span class="token comment">#从网络端口接收数据，下沉到logger</span>
<span class="token comment">#采集配置文件，netcat-logger.conf</span>

<span class="token comment"># Name the components on this agent</span>
a1.sources <span class="token operator">=</span> r1
a1.sinks <span class="token operator">=</span> k1
a1.channels <span class="token operator">=</span> c1

<span class="token comment"># Describe/configure the source</span>
a1.sources.r1.type <span class="token operator">=</span> netcat
a1.sources.r1.bind <span class="token operator">=</span> localhost
a1.sources.r1.port <span class="token operator">=</span> 44444

<span class="token comment"># Describe the sink</span>
a1.sinks.k1.type <span class="token operator">=</span> logger

<span class="token comment"># Use a channel which buffers events in memory</span>
a1.channels.c1.type <span class="token operator">=</span> memory
a1.channels.c1.capacity <span class="token operator">=</span> 1000
a1.channels.c1.transactionCapacity <span class="token operator">=</span> 100

<span class="token comment"># Bind the source and sink to the channel</span>
a1.sources.r1.channels <span class="token operator">=</span> c1
a1.sinks.k1.channel <span class="token operator">=</span> c1
启动命令：
bin/flume-ng agent --conf conf --conf-file conf/netcat-logger.conf --name a1 -Dflume.root.logger<span class="token operator">=</span>INFO,console

传入数据：
telnet localhost 44444
如果没有telnet , 下载即可: yum -y <span class="token function">install</span> telnet
Trying 127.0.0.1<span class="token punctuation">..</span>.
Connected to localhost.localdomain <span class="token punctuation">(</span>127.0.0.1<span class="token punctuation">)</span>.
Escape character is <span class="token string">'^]'</span><span class="token keyword">.</span>
Hello world<span class="token operator">!</span> <span class="token operator">&lt;</span>ENTER<span class="token operator">&gt;</span>
OK
看到返回OK说明配置成功
</code></pre>
<ul>
<li>
<p>2 、 启动 agent 去采集数据</p>
<p>首先切换到flume目录下  , 然后执行下列命令</p>
<p><code>bin/flume-ng agent -c conf -f conf/netcat-logger.conf -n a1 -Dflume.root.logger=INFO,console</code></p>
<ul>
<li><code>--conf/-c</code> : 指定flume自身的配置文件所在目录</li>
<li><code>--conf-file/-f</code> : 指定我们所描述的采集方案</li>
<li><code>--name/-n</code> : 指定我们这个agent的名字</li>
<li><code>-Dflume.root.logger=INFO,console</code>  开启日志记录功能</li>
</ul>
</li>
<li>
<p>3 、 测试</p>
</li>
</ul>
<p>先要往 agent 采集监听的端口上发送数据，让 agent 有数据可采。</p>
<p>随便在一个能跟 agent 节点联网的机器上：</p>
<p><strong>telnet anget-hostname port</strong> （例子 :<code>telnet localhost 44444</code>）</p>
<h2><a id="5_Flume_108"></a>5. Flume中常用组件</h2>
<h3><a id="51_DHFS_110"></a>5.1 采集目录到DHFS</h3>
<ul>
<li><strong><code>spooldir source</code></strong></li>
</ul>
<p>需求 : 服务器的某特定目录下，会不断产生新的文件，每当有新文件出现，就需要把文件采集到HDFS中去</p>
<p>思路 : 根据需求定义一下3大要素</p>
<ul>
<li>采集源，即 source——监控文件目录 : <strong>spooldir</strong></li>
<li>下沉目标，即 sink——HDFS 文件系统 : <strong>hdfs sink</strong></li>
<li>source和sink之间的传递通道——channel , 可用 file channel 也可以用内存 channel</li>
</ul>
<p>**作用 : **监控一个指定的目录  该目录只要有新文件产生 就把新文件采集<code>该目录一定不能有重名文件产生 否则报错 且罢工</code> , 将数据采集传输到hdfs</p>
<p>配置文件编写<code>vi spooldir-hdfs.conf</code></p>
<pre><code class="prism language-properties"># Name the components on this agent
a1.sources = r1
a1.sinks = k1
a1.channels = c1

# Describe/configure the source
##注意：不能往监控目中重复丢同名文件
a1.sources.r1.type = spooldir
a1.sources.r1.spoolDir = /root/logs2
a1.sources.r1.fileHeader = true

# Describe the sink
a1.sinks.k1.type = hdfs
a1.sinks.k1.channel = c1
a1.sinks.k1.hdfs.path = /flume/events/%y-%m-%d/%H%M/
a1.sinks.k1.hdfs.filePrefix = events-
a1.sinks.k1.hdfs.round = true
a1.sinks.k1.hdfs.roundValue = 10
a1.sinks.k1.hdfs.roundUnit = minute
a1.sinks.k1.hdfs.rollInterval = 3
a1.sinks.k1.hdfs.rollSize = 20
a1.sinks.k1.hdfs.rollCount = 5
a1.sinks.k1.hdfs.batchSize = 1
a1.sinks.k1.hdfs.useLocalTimeStamp = true
#生成的文件类型，默认是Sequencefile，可用DataStream，则为普通文本
a1.sinks.k1.hdfs.fileType = DataStream

# Use a channel which buffers events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
</code></pre>
<blockquote>
<p>监视文件夹 <code>/root/logs2</code><br>
启动命令： <code>bin/flume-ng agent -c ./conf -f ./conf/spool-hdfs.conf -n a1 -Dflume.root.logger=INFO,console</code></p>
<p>capacity：默认该通道中最大的可以存储的 event 数量</p>
<p>trasactionCapacity:每次最大可以从source中拿到或者送到sink中的event数量</p>
<p>测试： 往/root/logs2放文件（mv ././xxxFile /home/hadoop/flumeSpool），但是不要在里面生成文件</p>
</blockquote>
<p>扩展 :</p>
<blockquote>
<p>即控制了文件夹的滚动方式</p>
<p>a1.sinks.k1.hdfs.round = true   表示是否开启时间上的舍弃 , 默认false<br>
a1.sinks.k1.hdfs.roundValue = 10  舍弃的值  默认1<br>
a1.sinks.k1.hdfs.roundUnit = minute  默认seconds<br>
控制了文件夹的滚动 如果为false 不滚动  如果为true 滚动<br>
该目录每 10 分钟新生成一个   17:30:00  17:40:00</p>
<p>控制了文件的滚动方式<br>
a1.sinks.k1.hdfs.rollInterval = 3    时间间隔 默认30秒<br>
a1.sinks.k1.hdfs.rollSize = 20       大小 默认1024bytes<br>
a1.sinks.k1.hdfs.rollCount = 5       event数量 默认10个<br>
如果三个属性都存在  谁先满足就触发<br>
如果把某个属性设置为0 意味着不以该属性为标准</p>
</blockquote>
<h3><a id="52_HDFS_192"></a>5.2 采集文件到HDFS</h3>
<ul>
<li><strong><code>exec source</code></strong></li>
</ul>
<p>需求 : 比如业务系统使用 j log4j 生成的日志，日志内容不断增加，需要把追加到日志文件中的数据实时采集到 hdfs</p>
<p>思路 : 根据需求定义一下3大要素</p>
<ul>
<li>采集源，即 source——监控文件内容更新 : <code>exec ‘tail -F file’</code></li>
<li>下沉目标，即 sink——HDFS 文件系统 : hdfs sink</li>
<li>Source和sink之间的传递通道——channel,可用 file channel 也可以用内存 channel</li>
</ul>
<p>可以执行linux命令把命令执行的结果作为数据采集</p>
<p>模拟一个文件不断变化</p>
<pre><code class="prism language-shell"><span class="token keyword">while</span> <span class="token boolean">true</span><span class="token punctuation">;</span><span class="token keyword">do</span> <span class="token function">date</span> <span class="token operator">&gt;&gt;</span> /root/logs/test.log<span class="token punctuation">;</span><span class="token function">sleep</span> 0.5<span class="token punctuation">;</span><span class="token keyword">done</span>
或者
<span class="token comment">#！/bin/bash</span>
<span class="token keyword">while</span> <span class="token boolean">true</span>
<span class="token keyword">do</span>
	<span class="token function">date</span> <span class="token operator">&gt;&gt;</span> /root/logs/test.log
	<span class="token function">sleep</span> 0.5
<span class="token keyword">done</span>
</code></pre>
<p>配置文件的编写 : <code>vi tail-hdfs.conf</code></p>
<pre><code class="prism language-properties"># Name the components on this agent
a1.sources = r1
a1.sinks = k1
a1.channels = c1

# Describe/configure the source
a1.sources.r1.type = exec
a1.sources.r1.command = tail -F /root/logs/test.log
a1.sources.r1.channels = c1

# Describe the sink
a1.sinks.k1.type = hdfs
a1.sinks.k1.channel = c1
a1.sinks.k1.hdfs.path = /flume/tailout/%y-%m-%d/%H-%M/
a1.sinks.k1.hdfs.filePrefix = events-
a1.sinks.k1.hdfs.round = true
a1.sinks.k1.hdfs.roundValue = 10
a1.sinks.k1.hdfs.roundUnit = minute
a1.sinks.k1.hdfs.rollInterval = 3
a1.sinks.k1.hdfs.rollSize = 20
a1.sinks.k1.hdfs.rollCount = 5
a1.sinks.k1.hdfs.batchSize = 1
a1.sinks.k1.hdfs.useLocalTimeStamp = true
#生成的文件类型，默认是Sequencefile，可用DataStream，则为普通文本
a1.sinks.k1.hdfs.fileType = DataStream



# Use a channel which buffers events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
</code></pre>
<blockquote>
<p><code>启动命令前先启动hdfs服务</code></p>
<p>启动命令<code>bin/flume-ng agent -c conf -f conf/tail-hdfs.conf -n a1 -Dflume.root.logger=INFO,console</code></p>
<p>测试 : <code>while true;do date &gt;&gt; /root/logs/test.log;sleep 0.5;done</code></p>
<p>如果没有此文件夹创建即可</p>
</blockquote>
<h2><a id="6_Flumeloadbalancefailover_270"></a>6. Flume的load-balance(负载均衡)、failover(容错)</h2>
<p>负载均衡是用于解决一台机器(一个进程)无法解决所有请求而产生的一种算法。</p>
<p>解决了一台机器或者服务处理不了 多个一起处理的问题  但是不能重复处理</p>
<p>常见的负载均衡规则：轮询（round_robin）  random随机   权重  等</p>
<p>Load balancing Sink Processor 能够实现 load balance 功能，如下图Agent1 是一个路由节点，负责将 Channel 暂存的 Event 均衡到对应的多个 Sink组件上，而每个 Sink 组件分别连接到一个独立的 Agent 上，示例配置，如下所示：</p>
<p><img src="https://img-blog.csdnimg.cn/20181119210900155.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0NvZGVyQm9vbQ==,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<ul>
<li>avro source  / avro sink</li>
</ul>
<p>当涉及两级flume之间数据传递的时候   使用avro即可  指定传递数据的ip 和端口</p>
<pre><code>agent1.sinks.k2.hostname = node-3
agent1.sinks.k2.port = 52020
</code></pre>
<p>当涉及多级flume串联的时候 <code>优先启动远离数据源</code>的那级</p>
<p>需求 : 解决一台机器或者服务处理不了 , 用多个解决即可</p>
<p>node-1上编写<code>vi exec-avro.conf</code></p>
<pre><code class="prism language-properties">#agent1 name
agent1.channels = c1
agent1.sources = r1
agent1.sinks = k1 k2

#设置组名
agent1.sinkgroups = g1

#set channel
agent1.channels.c1.type = memory
agent1.channels.c1.capacity = 1000
agent1.channels.c1.transactionCapacity = 100

agent1.sources.r1.channels = c1
agent1.sources.r1.type = exec
agent1.sources.r1.command = tail -F /root/logs/123.log

# 设置下沉1
agent1.sinks.k1.channel = c1
agent1.sinks.k1.type = avro
agent1.sinks.k1.hostname = node-2
agent1.sinks.k1.port = 52020

# set sink2
agent1.sinks.k2.channel = c1
agent1.sinks.k2.type = avro
agent1.sinks.k2.hostname = node-3
agent1.sinks.k2.port = 52020

#设置下沉组
agent1.sinkgroups.g1.sinks = k1 k2

#设置为负载均衡
agent1.sinkgroups.g1.processor.type = load_balance
#如果开启 , 即将失败的sink放入黑名单
agent1.sinkgroups.g1.processor.backoff = true
#设置为轮询 , 还支持random
agent1.sinkgroups.g1.processor.selector = round_robin
#在黑名单放置的超时时间 , 超时结束时 , 若仍然无法接收 ,则超时时间呈指数增长
agent1.sinkgroups.g1.processor.selector.maxTimeOut=10000
</code></pre>
<blockquote>
<p>启动命令 : <code>bin/flume-ng agent -c conf -f conf/exec-avro.conf -n agent1 -Dflume.root.logger=INFO,console</code></p>
</blockquote>
<p>node-2上编写<code>vi avro-logger.conf</code></p>
<pre><code class="prism language-properties"># Name the components on this agent
a1.sources = r1
a1.sinks = k1
a1.channels = c1

# Describe/configure the source
a1.sources.r1.type = avro
a1.sources.r1.channels = c1
a1.sources.r1.bind = node-2
a1.sources.r1.port = 52020

# Describe the sink
a1.sinks.k1.type = logger

# Use a channel which buffers events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
</code></pre>
<blockquote>
<p>启动 命令: <code>bin/flume-ng agent -c conf -f conf/avro-logger.conf -n a1 -Dflume.root.logger=INFO,console</code></p>
</blockquote>
<p>node-3 上编写<code>vi avro-logger.conf</code></p>
<pre><code class="prism language-properties"># Name the components on this agent
a1.sources = r1
a1.sinks = k1
a1.channels = c1

# Describe/configure the source
#描述轮询资源参数
a1.sources.r1.type = avro
a1.sources.r1.channels = c1
a1.sources.r1.bind = node-3
a1.sources.r1.port = 52020

# Describe the sink
a1.sinks.k1.type = logger

# Use a channel which buffers events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
</code></pre>
<blockquote>
<p>启动命令 : <code>bin/flume-ng agent -c conf -f conf/avro-logger.conf -n a1 -Dflume.root.logger=INFO,console</code></p>
</blockquote>
<blockquote>
<p>测试 : <code>while true;do date &gt;&gt; /root/logs/123.log;sleep 0.5;done</code></p>
</blockquote>
<p><img src="https://img-blog.csdnimg.cn/20181119210923622.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0NvZGVyQm9vbQ==,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<p><img src="https://img-blog.csdnimg.cn/201811192109310.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0NvZGVyQm9vbQ==,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<p>两台机器轮询输出</p>
<h4><a id="failover_413"></a>failover具体流程</h4>
<p>Failover Sink Processor 维护一个优先级 Sink 组件列表，只要有一个 Sink组件可用，Event 就被传递到下一个组件。故障转移机制的作用是将失败的 Sink降级到一个池，在这些池中它们被分配一个冷却时间，随着故障的连续，在重试之前冷却时间增加。一旦 Sink 成功发送一个事件，它将恢复到活动池。 Sink 具有与之相关的优先级，数量越大，优先级越高。</p>
<p>主要解决单点故障问题 , 备份越多 , 容错能力越强 , 同一时刻只能有一个对外提供服务</p>
<p>当正在干活挂了 备用的才会顶上去  从而保证服务连续可靠 即所谓的容忍了错误的发生</p>
<p>重写node-1中的<code>vi exec-avro.conf</code></p>
<pre><code class="prism language-properties">#agent1 name
agent1.channels = c1
agent1.sources = r1
agent1.sinks = k1 k2

#set gruop
agent1.sinkgroups = g1

#set channel
agent1.channels.c1.type = memory
agent1.channels.c1.capacity = 1000
agent1.channels.c1.transactionCapacity = 100

agent1.sources.r1.channels = c1
agent1.sources.r1.type = exec
#监视文件
agent1.sources.r1.command = tail -F /root/logs/456.log

# set sink1
agent1.sinks.k1.channel = c1
agent1.sinks.k1.type = avro
agent1.sinks.k1.hostname = node-2
agent1.sinks.k1.port = 52020

# set sink2
agent1.sinks.k2.channel = c1
agent1.sinks.k2.type = avro
agent1.sinks.k2.hostname = node-3
agent1.sinks.k2.port = 52020

#set sink group
agent1.sinkgroups.g1.sinks = k1 k2

#set failover
agent1.sinkgroups.g1.processor.type = failover
#设置等级
agent1.sinkgroups.g1.processor.priority.k1 = 10
agent1.sinkgroups.g1.processor.priority.k2 = 1
agent1.sinkgroups.g1.processor.maxpenalty = 10000
</code></pre>
<blockquote>
<p>同样是先启动原理数据源的那级 , 即node-2 , node-3</p>
<p><code>bin/flume-ng agent -c conf -f conf/avro-logger.conf -n a1 -Dflume.root.logger=INFO,console</code></p>
<p>在启动node-1</p>
<p><code>bin/flume-ng agent -c conf -f conf/exec-avro.conf -n agent1 -Dflume.root.logger=INFO,console</code></p>
<p>测试代码 : <code>while true;do date &gt;&gt; /root/logs/456.log;sleep 0.5;done</code></p>
<p>我们发现当我们停止node-2时 , node-1会弹出信息 , 然后再去连接node-3保持继续工作</p>
</blockquote>
<h2><a id="7_Flume_479"></a>7. Flume实战案例</h2>
<p>Flume拦截器</p>
<pre><code>没有拦截器默认采集的数据的形式
Event: { headers:{} body:20 31 37 3A 35 32 Sat Nov 17 17:52 }

使用拦截器之后如下：在event heander中可以插入自定义kv对
Event: { headers:{type=access} body:20 31 37 3A 35 32 Sat Nov 17 17:52 }
Event: { headers:{type=web} body:20 31 37 3A 35 32 Sat Nov 17 17:52 }
Event: { headers:{type=nginx} body:20 31 37 3A 35 32 Sat Nov 17 17:52 }

%{type}可以提取key对应的value值
</code></pre>
<h3><a id="___497"></a>案例一 : 日志的采集和汇总</h3>
<p>案例场景 : A、B 两台日志服务机器实时生产日志主要类型为 access.log、nginx.log、web.log</p>
<p>要求 : 把 A、B 机器中的 access.log、nginx.log、web.log 采集汇总到 C 机器上然后统一收集到 hdfs 中。<br>
但是在 hdfs 中要求的目录为：<br>
/source/logs/access/20160101/**<br>
/source/logs/nginx/20160101/**<br>
/source/logs/web/20160101/**</p>
<p><strong>场景分析</strong></p>
<p><img src="https://img-blog.csdnimg.cn/20181119210953257.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0NvZGVyQm9vbQ==,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<p><img src="https://img-blog.csdnimg.cn/20181119210958813.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0NvZGVyQm9vbQ==,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<p><strong>流程处理分析</strong></p>
<p><img src="https://img-blog.csdnimg.cn/20181119211006580.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0NvZGVyQm9vbQ==,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<p><strong>功能实现</strong></p>
<ol>
<li>
<p>在服务器 A (node-1)和服务器 B(node-2) 上创建配置文件</p>
<p>在node-1 上编写 <code>vi exec_source_avro_sink.conf</code></p>
</li>
</ol>
<pre><code class="prism language-properties"># Name the components on this agent
a1.sources = r1 r2 r3
a1.sinks = k1
a1.channels = c1

# Describe/configure the source
#资源状态
a1.sources.r1.type = exec
#监测的文件
a1.sources.r1.command = tail -F /root/logs1/access.log
#拦截器名字
a1.sources.r1.interceptors = i1
a1.sources.r1.interceptors.i1.type = static
a1.sources.r1.interceptors.i1.key = type
a1.sources.r1.interceptors.i1.value = access

a1.sources.r2.type = exec
#监测的文件
a1.sources.r2.command = tail -F /root/logs1/nginx.log
#拦截器名字
a1.sources.r2.interceptors = i2
a1.sources.r2.interceptors.i2.type = static
a1.sources.r2.interceptors.i2.key = type
a1.sources.r2.interceptors.i2.value = nginx

a1.sources.r3.type = exec
#监测的文件
a1.sources.r3.command = tail -F /root/logs1/web.log
#拦截器名字
a1.sources.r3.interceptors = i3
a1.sources.r3.interceptors.i3.type = static
#static 拦截器的功能就是往采集到的数据的 header 中插入自
#己定义的 key-value 对
a1.sources.r3.interceptors.i3.key = type
a1.sources.r3.interceptors.i3.value = web

# Describe the sink
a1.sinks.k1.type = avro
a1.sinks.k1.hostname = node-2
a1.sinks.k1.port = 41414

# Use a channel which buffers events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sources.r2.channels = c1
a1.sources.r3.channels = c1
a1.sinks.k1.channel = c1
</code></pre>
<blockquote>
<p>启动命令 : <code>后启动</code></p>
<p><code>bin/flume-ng agent -c conf -f conf/exec_source_avro_sink.conf -name a1 -Dflume.root.logger=DEBUG,console</code></p>
</blockquote>
<p>node-2 上编写<code>vi avro_source_hdfs_sink.conf</code></p>
<pre><code class="prism language-properties">#定义agent名， source、channel、sink的名称
a1.sources = r1
a1.sinks = k1
a1.channels = c1


#定义source
a1.sources.r1.type = avro
a1.sources.r1.bind = node-2
a1.sources.r1.port =41414

#添加时间拦截器
a1.sources.r1.interceptors = i1
a1.sources.r1.interceptors.i1.type = org.apache.flume.interceptor.TimestampInterceptor$Builder


#定义channels
a1.channels.c1.type = memory
a1.channels.c1.capacity = 2000
a1.channels.c1.transactionCapacity = 1000

#定义sink
a1.sinks.k1.type = hdfs
a1.sinks.k1.hdfs.path=hdfs://node-1:9000/source/logs/%{type}/%Y%m%d
a1.sinks.k1.hdfs.filePrefix =events
a1.sinks.k1.hdfs.fileType = DataStream
a1.sinks.k1.hdfs.writeFormat = Text
#时间类型
#a1.sinks.k1.hdfs.useLocalTimeStamp = true
a1.sinks.k1.hdfs.rollCount = 0
a1.sinks.k1.hdfs.rollInterval = 10
a1.sinks.k1.hdfs.rollSize =0

a1.sinks.k1.hdfs.round = true
a1.sinks.k1.hdfs.roundValue = 10
a1.sinks.k1.hdfs.roundUnit = minute

#批量写入hdfs的个数
a1.sinks.k1.hdfs.batchSize = 1
#flume操作hdfs的线程数（包括新建，写入等）
a1.sinks.k1.hdfs.threadsPoolSize=10
#操作hdfs超时时间
a1.sinks.k1.hdfs.callTimeout=30000

#组装source、channel、sink
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
</code></pre>
<blockquote>
<p>启动命令 : 先启动</p>
<p><code>bin/flume-ng agent -c conf -f conf/avro_source_hdfs_sink.conf -name a1 -Dflume.root.logger=DEBUG,console</code></p>
<p>测试命令 :</p>
<p><code>while true;do echo "access access" &gt;&gt; /root/logs1/access.log;sleep 0.5;done</code><br>
<code>while true;do echo "nginx nginx" &gt;&gt; /root/logs1/nginx.log;sleep 0.5;done</code><br>
<code>while true;do echo "web web" &gt;&gt; /root/logs1/web.log;sleep 0.5;done</code></p>
</blockquote>
<p>扩展 :</p>
<p><strong>Flume中遇到小文件频繁滚动注意事项</strong></p>
<p>hdfs.minBlockReplicas是为了让flume感知不到hdfs的块复制，这样滚动方式配置才不会受影响。</p>
<p>假如hdfs的副本为3.那么配置的滚动时间为10秒，那么在第二秒的时候，flume检测到hdfs在复制块，那么这时候flume就会滚动，这样导致flume的滚动方式受到影响。所以配置<strong>flume hdfs.minBlockReplicas配置为1</strong>，就检测不到副本的复制了。但是hdfs的副本还是3</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>