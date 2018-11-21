---
layout:     post
title:      Apache Flume采集数据简单案例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_27252133/article/details/80760295				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="概述">概述</h1>

<p> Flume 是 Cloudera 提供的一个高可用的，高可靠的，分布式的海量日志采 <br>
集、聚合和传输的系统。Flume 支持定制各类数据发送方，用于收集各类型数据；同时，Flume 提供对数据进行简单处理，并写到各种数据接受方（可定制）的能 <br>
力。一般的采集需求，通过对 flume 的简单配置即可实现。针对特殊场景也具备 <br>
良好的自定义扩展能力。因此，flume 可以适用于大部分的日常数据采集场景。 <br>
  当前 Flume 有两个版本。Flume 0.9X 版本的统称 Flume OG（original <br>
generation），Flume1.X 版本的统称 Flume NG（next generation）。由于 Flume <br>
NG 经过核心组件、核心配置以及代码架构重构，与 Flume OG 有很大不同，使用 <br>
时请注意区分。改动的另一原因是将 Flume 纳入 apache 旗下，cloudera Flume <br>
改名为 Apache Flume。 <br>
  该博客介绍Flume版本为：<code>1.6.0</code>，运行环境为：<code>Centos 6.9</code></p>

<h1 id="运行机制">运行机制</h1>

<p>  Flume 的核心是把数据从数据源(source)收集过来，在将收集到的数据送到 <br>
指定的目的地(sink)。为了保证输送的过程一定成功，在送到目的地(sink)之前， <br>
会先缓存数据(channel),待数据真正到达目的地(sink)后，flume 在删除自己缓 <br>
存的数据。 <br>
  Flume 分布式系统中核心的角色是  agent，agent 本身是一个 Java 进程，一 <br>
般运行在日志收集节点。flume 采集系统就是由一个个 agent 所连接起来形成。 <br>
<img src="https://img-blog.csdn.net/20180621143940314?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3MjUyMTMz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="" title=""> <br>
每一个 agent 相当于一个数据传递员，内部有三个组件：</p>

<ul>
<li>Source：采集源，用于跟数据源对接，以获取数据</li>
<li>Sink：下沉地，采集数据的传送目的，用于往下一级 agent 传递数据或者往 <br>
最终存储系统传递数据</li>
<li>Channel：agent 内部的数据传输通道，用于从 source 将数据传递到 sink</li>
</ul>

<p>  在整个数据的传输的过程中，流动的是  event，它是 Flume 内部数据传输的 <br>
最基本单元。一个完整的 event 包括：event headers、event body、event 信息，其中event 信息就是 flume 收集到的日记记录。</p>

<h1 id="安装部署">安装部署</h1>

<ul>
<li>下载地址： <code>https://www.apache.org/dist/flume/1.6.0/apache-flume-1.6.0-bin.tar.gz</code></li>
<li>减压： <code>tar -zxvf apache-flume-1.6.0-bin.tar.gz</code></li>
<li>进入flume的目录修改conf下的<code>flume.env.sh</code>，在里面配置JAVA_HOME</li>
</ul>

<h3 id="测试flume是否能正常运行">测试Flume是否能正常运行</h3>

<ol>
<li>在conf目录下新建一个文件：vi netcat-logger.conf</li>
</ol>

<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor"># 定义这个 agent 中各组件的名字</span>
a1<span class="hljs-preprocessor">.sources</span> = <span class="hljs-built_in">r1</span>
a1<span class="hljs-preprocessor">.sinks</span> = k1
a1<span class="hljs-preprocessor">.channels</span> = c1
<span class="hljs-preprocessor"># 描述和配置 source 组件：r1</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.type</span> = netcat
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.bind</span> = localhost
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.port</span> = <span class="hljs-number">44444</span>
<span class="hljs-preprocessor"># 描述和配置 sink 组件：k1</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.type</span> = logger
<span class="hljs-preprocessor"># 描述和配置 channel 组件，此处使用是内存缓存的方式</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span> = memory
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">1000</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.transactionCapacity</span> = <span class="hljs-number">100</span>
<span class="hljs-preprocessor"># 描述和配置 source channel sink 之间的连接关系</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.channels</span> = c1
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.channel</span> = c1</code></pre>

<p>2.启动agent去采集数据 <br>
 在flume目录下执行</p>

<pre class="prettyprint"><code class=" hljs lasso">bin/flume<span class="hljs-attribute">-ng</span> agent <span class="hljs-attribute">-c</span> conf <span class="hljs-attribute">-f</span> conf/netcat<span class="hljs-attribute">-logger</span><span class="hljs-built_in">.</span>conf <span class="hljs-attribute">-n</span> a1 <span class="hljs-attribute">-Dflume</span><span class="hljs-built_in">.</span>root<span class="hljs-built_in">.</span>logger<span class="hljs-subst">=</span>INFO,console</code></pre>

<p>-c conf 指定 flume 自身的配置文件所在目录 <br>
-f conf/netcat-logger.con 指定我们所描述的采集方案 <br>
-n a1 指定我们这个 agent 的名字 <br>
3.测试 <br>
 先要往 agent 采集监听的端口上发送数据，让 agent 有数据可采。 <br>
随便在一个能跟 agent 节点联网的机器上：</p>



<pre class="prettyprint"><code class=" hljs ">telnet localhost 44444</code></pre>

<p>如果没有安装telnet先使用<code>yum -y install telnet</code> 安装 <br>
如果产生如下效果证明flume安装并启动成功 <br>
<img src="https://img-blog.csdn.net/20180621145951126?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3MjUyMTMz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
<img src="https://img-blog.csdn.net/20180621150001652?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI3MjUyMTMz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<h1 id="flume简单案例">Flume简单案例</h1>

<h3 id="采集目录到hdfs">采集目录到HDFS</h3>

<p>采集需求：服务器的某特定目录下，会不断产生新的文件，每当有新文件出现，就需要把文件采集到HDFS中去 <br>
根据需求，首先定义以下三大要素：</p>

<ul>
<li>采集源，即source：spooldir</li>
<li>下沉目标，即sink：hdfs sink</li>
<li>source和sink之间传递的通道-channel，可用file channel也可以用内存channel <br>
配置文件编写：</li>
</ul>

<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor"># Name the components on this agent</span>
a1<span class="hljs-preprocessor">.sources</span> = <span class="hljs-built_in">r1</span>
a1<span class="hljs-preprocessor">.sinks</span> = k1
a1<span class="hljs-preprocessor">.channels</span> = c1

<span class="hljs-preprocessor"># Describe/configure the source</span>
<span class="hljs-preprocessor">##注意：不能往监控目中重复丢同名文件</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.type</span> = spooldir
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.spoolDir</span> = /root/logs
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.fileHeader</span> = true

<span class="hljs-preprocessor"># Describe the sink</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.type</span> = hdfs
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.channel</span> = c1
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.path</span> = /flume/events/%<span class="hljs-built_in">y</span>-%m-%d/%H%M/
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.filePrefix</span> = events-
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.round</span> = true
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.roundValue</span> = <span class="hljs-number">10</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.roundUnit</span> = minute
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollInterval</span> = <span class="hljs-number">3</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollSize</span> = <span class="hljs-number">20</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollCount</span> = <span class="hljs-number">5</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.batchSize</span> = <span class="hljs-number">1</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.useLocalTimeStamp</span> = true
<span class="hljs-preprocessor">#生成的文件类型，默认是Sequencefile，可用DataStream，则为普通文本</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.fileType</span> = DataStream

<span class="hljs-preprocessor"># Use a channel which buffers events in memory</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span> = memory
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">1000</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.transactionCapacity</span> = <span class="hljs-number">100</span>

<span class="hljs-preprocessor"># Bind the source and sink to the channel</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.channels</span> = c1
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.channel</span> = c1</code></pre>

<p>测试： <br>
启动该agent和HDFS，向<code>/root/logs</code> 目录中创建一个文件，观察agent的输出，最终结果在HDFS的<code>/flume/events/</code> 下有文件产生说明测试成功</p>

<h3 id="采集文件到hdfs">采集文件到HDFS</h3>

<p>需求：业务系统使用 j log4j  生成的日志，日志内容不断增加，需要把追加到日志文件中的数据实时采集到 HDFS <br>
根据需求，首先定义一下3大要素：</p>

<ul>
<li>source : <code>exec tail -F file</code> </li>
<li>sink : <code>hdfs</code></li>
<li>channel : 这里我们使用<code>memory</code> <br>
编写配置文件：</li>
</ul>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor"># Name the components on this agent</span>
a1<span class="hljs-preprocessor">.sources</span> = <span class="hljs-built_in">r1</span>
a1<span class="hljs-preprocessor">.sinks</span> = k1
a1<span class="hljs-preprocessor">.channels</span> = c1

<span class="hljs-preprocessor"># Describe/configure the source</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.type</span> = exec
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.command</span> = tail -F /root/logs/test<span class="hljs-preprocessor">.log</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.channels</span> = c1

<span class="hljs-preprocessor"># Describe the sink</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.type</span> = hdfs
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.channel</span> = c1
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.path</span> = /flume/tailout/%<span class="hljs-built_in">y</span>-%m-%d/%H%M/
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.filePrefix</span> = events-
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.round</span> = true
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.roundValue</span> = <span class="hljs-number">10</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.roundUnit</span> = minute
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollInterval</span> = <span class="hljs-number">3</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollSize</span> = <span class="hljs-number">20</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollCount</span> = <span class="hljs-number">5</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.batchSize</span> = <span class="hljs-number">1</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.useLocalTimeStamp</span> = true
<span class="hljs-preprocessor">#生成的文件类型，默认是Sequencefile，可用DataStream，则为普通文本</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.fileType</span> = DataStream



<span class="hljs-preprocessor"># Use a channel which buffers events in memory</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span> = memory
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">1000</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.transactionCapacity</span> = <span class="hljs-number">100</span>

<span class="hljs-preprocessor"># Bind the source and sink to the channel</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.channels</span> = c1
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.channel</span> = c1
</code></pre>

<p>测试同采集目录到hfs</p>

<h1 id="日志的采集和汇总">日志的采集和汇总</h1>

<p>需求：把不同种类的日志采集汇总到hdfs的不同种类目录下。 <br>
access.log、nginx.log、web.log日志采集到hdfs中目录如下：</p>

<pre class="prettyprint"><code class=" hljs ruby">/source/logs/access/<span class="hljs-number">20160101</span>/**
<span class="hljs-regexp">/source/logs</span><span class="hljs-regexp">/nginx/</span><span class="hljs-number">20160101</span>/**
<span class="hljs-regexp">/source/logs</span><span class="hljs-regexp">/web/</span><span class="hljs-number">20160101</span>/**</code></pre>

<p>配置文件如下：</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor"># Name the components on this agent</span>
a1<span class="hljs-preprocessor">.sources</span> = <span class="hljs-built_in">r1</span> <span class="hljs-built_in">r2</span> <span class="hljs-built_in">r3</span>
a1<span class="hljs-preprocessor">.sinks</span> = k1
a1<span class="hljs-preprocessor">.channels</span> = c1

<span class="hljs-preprocessor"># Describe/configure the source</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.type</span> = exec
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.command</span> = tail -F /root/log/access<span class="hljs-preprocessor">.log</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.interceptors</span> = i1
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>1<span class="hljs-preprocessor">.type</span> = static
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>1<span class="hljs-preprocessor">.key</span> = type
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>1<span class="hljs-preprocessor">.value</span> = access

a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>2<span class="hljs-preprocessor">.type</span> = exec
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>2<span class="hljs-preprocessor">.command</span> = tail -F /root/log/nginx<span class="hljs-preprocessor">.log</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>2<span class="hljs-preprocessor">.interceptors</span> = i2
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>2<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>2<span class="hljs-preprocessor">.type</span> = static
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>2<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>2<span class="hljs-preprocessor">.key</span> = type
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>2<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>2<span class="hljs-preprocessor">.value</span> = nginx

a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>3<span class="hljs-preprocessor">.type</span> = exec
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>3<span class="hljs-preprocessor">.command</span> = tail -F /root/log/web<span class="hljs-preprocessor">.log</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>3<span class="hljs-preprocessor">.interceptors</span> = i3
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>3<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>3<span class="hljs-preprocessor">.type</span> = static
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>3<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>3<span class="hljs-preprocessor">.key</span> = type
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>3<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>3<span class="hljs-preprocessor">.value</span> = web

<span class="hljs-preprocessor"># Describe the sink</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.type</span> = avro
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hostname</span> = node2
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.port</span> = <span class="hljs-number">41414</span>

<span class="hljs-preprocessor"># Use a channel which buffers events in memory</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span> = memory
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">2000000</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.transactionCapacity</span> = <span class="hljs-number">100000</span>

<span class="hljs-preprocessor"># Bind the source and sink to the channel</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.channels</span> = c1
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>2<span class="hljs-preprocessor">.channels</span> = c1
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>3<span class="hljs-preprocessor">.channels</span> = c1
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.channel</span> = c1</code></pre>

<p>测试：</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-keyword">while</span> <span class="hljs-literal">true</span> ; <span class="hljs-keyword">do</span> <span class="hljs-built_in">echo</span> <span class="hljs-string">'access  access....'</span> &gt;&gt;/root/log/access.log;<span class="hljs-keyword">done</span>

<span class="hljs-keyword">while</span> <span class="hljs-literal">true</span> ; <span class="hljs-keyword">do</span> <span class="hljs-built_in">echo</span> <span class="hljs-string">'nginx  nginx....'</span> &gt;&gt;/root/log/nginx.log;<span class="hljs-keyword">done</span>

<span class="hljs-keyword">while</span> <span class="hljs-literal">true</span> ; <span class="hljs-keyword">do</span> <span class="hljs-built_in">echo</span> <span class="hljs-string">'web  web....'</span> &gt;&gt;/root/log/web.log;<span class="hljs-keyword">done</span></code></pre>

<p>分别向三种类型的文件写入数据，观察hdfs中是否有对应的目录生成。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>