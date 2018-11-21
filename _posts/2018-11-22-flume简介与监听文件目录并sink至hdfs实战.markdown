---
layout:     post
title:      flume简介与监听文件目录并sink至hdfs实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，转载请标明出处					https://blog.csdn.net/PENGYUCHENG32109/article/details/51568573				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3 id="场景">场景</h3>



<h4 id="1-flume是什么">1. flume是什么</h4>



<h4 id="11-背景">1.1 背景</h4>

<p>　　flume 作为 cloudera 开发的实时日志收集系统，受到了业界的认可与广泛应用。Flume 初始的发行版本目前被统称为 Flume OG（original generation），属于 cloudera。但随着 FLume 功能的扩展，Flume OG 代码工程臃肿、核心组件设计不合理、核心配置不标准等缺点暴露出来，尤其是在 Flume OG 的最后一个发行版本 0.94.0 中，日志传输不稳定的现象尤为严重，为了解决这些问题，2011 年 10 月 22 号，cloudera 完成了 Flume-728，对 Flume 进行了里程碑式的改动：重构核心组件、核心配置以及代码架构，重构后的版本统称为 Flume NG（next generation）；改动的另一原因是将 Flume 纳入 apache 旗下，cloudera Flume 改名为 Apache Flume。 <br>
　　flume是一个分布式、可靠、和高可用的海量日志采集、聚合和传输的系统。支持在日志系统中定制各类数据发送方，用于收集数据;同时，Flume提供对数据进行简单处理，并写到各种数据接受方(比如文本、HDFS、Hbase等)的能力 。 <br>
　　flume的数据流由事件(Event)贯穿始终。事件是Flume的基本数据单位，它携带日志数据(字节数组形式)并且携带有头信息，这些Event由Agent外部的Source生成，当Source捕获事件后会进行特定的格式化，然后Source会把事件推入(单个或多个)Channel中。你可以把Channel看作是一个缓冲区，它将保存事件直到Sink处理完该事件。Sink负责持久化日志或者把事件推向另一个Source。</p>



<h4 id="12-特点">1.2 特点</h4>

<ul>
<li><p>flume的可靠性</p>

<p>　当节点出现故障时，日志能够被传送到其他节点上而不会丢失。Flume提供了三种级别的可靠性保障，从强到弱依次分别为：end-to-end（收到数据agent首先将event写到磁盘上，当数据传送成功后，再删除；如果数据发送失败，可以重新发送。），Store on failure（这也是scribe采用的策略，当数据接收方crash时，将数据写到本地，待恢复后，继续发送），Besteffort（数据发送到接收方后，不会进行确认）。</p></li>
<li><p>可恢复性</p>

<p>　　还是靠Channel。推荐使用FileChannel，事件持久化在本地文件系统里(性能较差)。 </p></li>
<li><p>核心概念</p></li>
</ul>

<p>Agent ：使用JVM 运行Flume。每台机器运行一个agent，但是可以在一个agent中包含多个sources和sinks。 <br>
Client ：生产数据，运行在一个独立的线程。 <br>
Source：从Client收集数据，传递给Channel。 <br>
Sink  ：从Channel收集数据，运行在一个独立线程。 <br>
Channel ：连接 sources 和 sinks ，这个有点像一个队列。 <br>
Events ：可以是日志记录、 avro 对象等。</p>

<p>Flume以agent为最小的独立运行单位。一个agent就是一个JVM。单agent由Source、Sink和Channel三大组件构成，如下图：</p>

<p><img title="" alt="这里写图片描述" src="http://flume.apache.org/_images/DevGuide_image00.png"></p>

<p>　　值得注意的是，Flume提供了大量内置的Source、Channel和Sink类型。不同类型的Source,Channel和Sink可以自由组合。组合方式基于用户设置的配置文件，非常灵活。比如：Channel可以把事件暂存在内存里，也可以持久化到本地硬盘上。Sink可以把日志写入HDFS, HBase，甚至是另外一个Source等等。Flume支持用户建立多级流，也就是说，多个agent可以协同工作，并且支持Fan-in、Fan-out、Contextual Routing、Backup Routes，这也正是NB之处。如下图所示: <br>
　　</p>

<p><img title="" alt="这里写图片描述" src="http://flume.apache.org/_images/UserGuide_image01.png"></p>



<h4 id="13-如何配置flume">1.3 如何配置flume</h4>

<p>这里以flume监听目录并将该目录下的文件内容sink至hdfs上指定目录的配置为例加以说明，详见实验部分。</p>

<h4 id="实验">实验</h4>

<ul>
<li>配置 flume-env.sh文件 <br>
在文件末尾追加以下内容：</li>
</ul>

<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-keyword">export</span> FLUME_HOME=/home/hadoop/apache-flume-<span class="hljs-number">1.6</span>.<span class="hljs-number">0</span>-bin
<span class="hljs-keyword">export</span> FLUME_CONF_DIR=<span class="hljs-variable">$FLUME_HOME</span>/conf
<span class="hljs-keyword">export</span> PATH=.:<span class="hljs-variable">$PATH</span>::<span class="hljs-variable">$FLUME_HOME</span>/bin
</code></pre>

<ul>
<li>配置flume-conf.properties文件</li>
</ul>



<pre class="prettyprint"><code class=" hljs avrasm">agent1<span class="hljs-preprocessor">.sources</span> = spooldirSource
agent1<span class="hljs-preprocessor">.channels</span> = fileChannel
agent1<span class="hljs-preprocessor">.sinks</span> = hdfsSink

<span class="hljs-preprocessor">#配置sources，即被监听的源目录</span>
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.spooldirSource</span><span class="hljs-preprocessor">.type</span>=spooldir
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.spooldirSource</span><span class="hljs-preprocessor">.spoolDir</span>=/home/hadoop/flume
agent1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.spooldirSource</span><span class="hljs-preprocessor">.channels</span>=fileChannel

<span class="hljs-preprocessor">#配置sinks，即目的目录</span>
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hdfsSink</span><span class="hljs-preprocessor">.type</span>=hdfs
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hdfsSink</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.path</span>=hdfs://master:<span class="hljs-number">9000</span>/input/flume/%<span class="hljs-built_in">y</span>-%m-%d
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hdfsSink</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.filePrefix</span>=flume
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.round</span> = true
<span class="hljs-preprocessor"># Number of seconds to wait before rolling current file (0 = never roll based on time interval)</span>
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hdfsSink</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollInterval</span> = <span class="hljs-number">3600</span>
<span class="hljs-preprocessor"># File size to trigger roll, in bytes (0: never roll based on file size)</span>
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hdfsSink</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollSize</span> = <span class="hljs-number">128000000</span>
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hdfsSink</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollCount</span> = <span class="hljs-number">0</span>
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hdfsSink</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.batchSize</span> = <span class="hljs-number">1000</span>

<span class="hljs-preprocessor">#Rounded down to the highest multiple of this (in the unit configured using hdfs.roundUnit), less than current time.</span>
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hdfsSink</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.roundValue</span> = <span class="hljs-number">1</span>
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hdfsSink</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.roundUnit</span> = minute
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hdfsSink</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.useLocalTimeStamp</span> = true
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hdfsSink</span><span class="hljs-preprocessor">.channel</span>=fileChannel
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.hdfsSink</span><span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.fileType</span> = DataStream

<span class="hljs-preprocessor">#channels，通道目录配置：把文件事件持久化到本地硬盘上</span>
agent1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.fileChannel</span><span class="hljs-preprocessor">.type</span> = file
agent1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.fileChannel</span><span class="hljs-preprocessor">.checkpointDir</span>=/home/hadoop/apache-flume-<span class="hljs-number">1.6</span><span class="hljs-number">.0</span>-bin/checkpoint
agent1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.fileChannel</span><span class="hljs-preprocessor">.dataDirs</span>=/home/hadoop/apache-flume-<span class="hljs-number">1.6</span><span class="hljs-number">.0</span>-bin/dataDir</code></pre>

<ul>
<li>测试 <br>
1、flume环境测试与启动</li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">hadoop<span class="hljs-variable">@master</span><span class="hljs-symbol">:~</span><span class="hljs-variable">$ </span>flume-ng version
<span class="hljs-constant">Flume</span> <span class="hljs-number">1.6</span>.<span class="hljs-number">0</span>
<span class="hljs-constant">Source</span> code <span class="hljs-symbol">repository:</span> <span class="hljs-symbol">https:</span>/<span class="hljs-regexp">/git-wip-us.apache.org/repos</span><span class="hljs-regexp">/asf/flume</span>.git
<span class="hljs-constant">Revision</span><span class="hljs-symbol">:</span> <span class="hljs-number">2561</span>a23240a71ba20bf288c7c2cda88f443c208<span class="hljs-number">0</span>
<span class="hljs-constant">Compiled</span> by hshreedharan on <span class="hljs-constant">Mon</span> <span class="hljs-constant">May</span> <span class="hljs-number">11</span> <span class="hljs-number">11</span><span class="hljs-symbol">:</span><span class="hljs-number">15</span><span class="hljs-symbol">:</span><span class="hljs-number">44</span> <span class="hljs-constant">PDT</span> <span class="hljs-number">2015</span>
<span class="hljs-constant">From</span> source with checksum b29e416802ce9ece3269d34233baf43f
hadoop<span class="hljs-variable">@master</span><span class="hljs-symbol">:~</span><span class="hljs-variable">$ </span></code></pre>



<pre class="prettyprint"><code class=" hljs avrasm">hadoop@master:~$ ${FLUME_HOME}/bin/flume-ng agent --conf ./conf/ -f conf/flume-conf<span class="hljs-preprocessor">.properties</span> -Dflume<span class="hljs-preprocessor">.root</span><span class="hljs-preprocessor">.logger</span>=DEBUG,console -n agent1 &gt; log<span class="hljs-preprocessor">.log</span> <span class="hljs-number">2</span>&gt;&amp;<span class="hljs-number">1</span> &amp;
[<span class="hljs-number">2</span>] <span class="hljs-number">13370</span>
hadoop@master:~$ tailf ~/apache-flume-<span class="hljs-number">1.6</span><span class="hljs-number">.0</span>-bin/log<span class="hljs-preprocessor">.log</span>
<span class="hljs-number">2016</span>-<span class="hljs-number">06</span>-<span class="hljs-number">03</span> <span class="hljs-number">16</span>:<span class="hljs-number">32</span>:<span class="hljs-number">15</span>,<span class="hljs-number">007</span> (Log-BackgroundWorker-fileChannel) [DEBUG - org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.channel</span><span class="hljs-preprocessor">.file</span><span class="hljs-preprocessor">.FlumeEventQueue</span><span class="hljs-preprocessor">.checkpoint</span>(FlumeEventQueue<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">139</span>)] Checkpoint not required
<span class="hljs-number">2016</span>-<span class="hljs-number">06</span>-<span class="hljs-number">03</span> <span class="hljs-number">16</span>:<span class="hljs-number">32</span>:<span class="hljs-number">15</span>,<span class="hljs-number">828</span> (conf-file-poller-<span class="hljs-number">0</span>) [DEBUG - org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.node</span><span class="hljs-preprocessor">.PollingPropertiesFileConfigurationProvider</span>$FileWatcherRunnable<span class="hljs-preprocessor">.run</span>(PollingPropertiesFileConfigurationProvider<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">126</span>)] Checking file:conf/flume-conf<span class="hljs-preprocessor">.properties</span> for changes
</code></pre>

<p>2、向监听目录中添加文件</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-keyword">cp</span> ~/wordcount<span class="hljs-preprocessor">.txt</span> ~/flume/</code></pre>

<p>3、执行结果</p>

<p><img title="" alt="这里写图片描述" src="https://img-blog.csdn.net/20160603164104164"></p>



<h3 id="总结">总结</h3>

<p>　flume是一个分布式、可靠、和高可用的海量日志采集、聚合和传输的系统。支持在日志系统中定制各类数据发送方，用于收集数据;同时，Flume提供对数据进行简单处理，并写到各种数据接受方(比如文本、HDFS、Hbase等)的能力 。 <br>
　</p>



<h3 id="参考">参考</h3>

<p><a href="http://weibo.com/ilovepains" rel="nofollow">王家林DT大数据梦工厂-IMF-瓦力同学</a> <br>
<a href="http://flume.apache.org/FlumeUserGuide.html" rel="nofollow">flume官网</a> <br>
<a href="http://www.cnblogs.com/weiqiang-liu/p/3795149.html" rel="nofollow">flume on hdfs 配置</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>