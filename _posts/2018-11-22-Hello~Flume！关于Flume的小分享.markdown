---
layout:     post
title:      Hello~Flume！关于Flume的小分享
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-tomorrow-night-eighties">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<blockquote>
<p>Apache官网对Flume的描述是这样子的：</p>
<p>Flume is a distributed, reliable, and available service for efficiently collecting, aggregating, and moving large amounts of log data. It has a simple and flexible architecture based on streaming data flows. It is robust and fault tolerant with tunable reliability mechanisms and many failover and recovery mechanisms. It uses a simple extensible data model that allows for online analytic application.</p>
</blockquote>
<p>简单来说，Apache旗下的Flume，是一个分布式日志收集系统，支持多种数据来源，可以对日志信息进行简单处理，然后写出到数据存储系统中，也就是HDFS之类的了。</p>
<h2><a id="flume_9"></a>关于flume的几个概念</h2>
<p><img src="https://img-blog.csdnimg.cn/20181025220153132.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjcxMjg3Ng==,size_27,color_FFFFFF,t_70" alt="来自官网的flume介绍"></p>
<ol>
<li><strong>Event：</strong> 事件，数据的载体。Flume将一批日志信息包装成一个event进行传输处理，结构非常简单，就是json串。由一个headers和一个body组成，header里面是自定义内容或默认内容，body里面就是日志的本身。</li>
</ol>
<blockquote>
<p>{“headers”:infomation,”body”:infomation}</p>
</blockquote>
<ol start="2">
<li>
<p><strong>Agent：</strong> 代理，就是Flume里面包装、装载、运输event到目的地的过程。这个过程，包含了三个组件，分别是下面的source、channel、sink（敲黑板啦！）：</p>
</li>
<li>
<p><strong>Source：</strong> 数据源，接受日志并封装成Event，传输到Channel</p>
</li>
<li>
<p><strong>Channel：</strong> 传输通道，<strong>被动</strong>接受Source传来的数据，暂时存储，<strong>等待</strong> Sink消费</p>
</li>
<li>
<p><strong>Sink：</strong> 目的地传送槽，获取Agent里面的数据，即消费Channel中的数据，并传送到目的地。</p>
</li>
</ol>
<hr>
<h2><a id="72Flume_28"></a>懂得72变的Flume</h2>
<p>Flume的厉害之处在于它可以千变万化的结构，几乎适应任何的数据处理场景。</p>
<ol>
<li><strong>扇入扇出</strong> Consolidation</li>
</ol>
<blockquote>
<p>扇入：多个Event进入同一个Destination<br>
扇出：一个Event传送到多个Destination</p>
</blockquote>
<p><img src="https://img-blog.csdnimg.cn/20181025225241750.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjcxMjg3Ng==,size_27,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<ol start="2">
<li><strong>多级流动</strong> Setting multi-agent flow</li>
</ol>
<blockquote>
<p>同一个Event要经过多个Agent才到达特定的Destination</p>
</blockquote>
<p><img src="https://img-blog.csdnimg.cn/20181025225427503.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjcxMjg3Ng==,size_27,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<p>所以说，扇入扇出组合多级流动，别说是蜘蛛网，再复杂的网络Flume也能织出来。<br>
<img src="https://img-blog.csdnimg.cn/20181025225629586.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjcxMjg3Ng==,size_27,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<hr>
<h2><a id="Flume_47"></a>到底Flume能搞什么鬼</h2>
<p>搞什么鬼？就是Flume能传递什么呢？我们从Flume的Agent组件传输/存储类型里切入窥探下吧~</p>

<table>
<thead>
<tr>
<th>Source</th>
<th>Sink</th>
<th>Channel</th>
</tr>
</thead>
<tbody>
<tr>
<td><mark>Avro Source</mark></td>
<td><mark>HDFS Sink</mark></td>
<td><mark>Memory Channel</mark></td>
</tr>
<tr>
<td>Thrift Source</td>
<td>Hive Sink</td>
<td>JDBC Channel</td>
</tr>
<tr>
<td>Exec Source</td>
<td><mark>Logger Sink</mark></td>
<td>Kafka Channel</td>
</tr>
<tr>
<td>JMS Source</td>
<td><mark>Avro Sink</mark></td>
<td>File Channel</td>
</tr>
<tr>
<td><mark>Spooling Directory Source</mark></td>
<td>Thrift Sink</td>
<td>Spillable Memory Channel</td>
</tr>
<tr>
<td>Twitter 1% firehose Source (experimental)</td>
<td>IRC Sink</td>
<td>Pseudo Transaction Channel</td>
</tr>
<tr>
<td>Kafka Source</td>
<td>File Roll Sink</td>
<td>Custom Channel</td>
</tr>
<tr>
<td>NetCat TCP Source</td>
<td>Null Sink</td>
<td></td>
</tr>
<tr>
<td>NetCat UDP Source</td>
<td>HBaseSinks</td>
<td></td>
</tr>
<tr>
<td>Sequence Generator Source</td>
<td>MorphlineSolrSink</td>
<td></td>
</tr>
<tr>
<td>Syslog Sources</td>
<td>ElasticSearchSink</td>
<td></td>
</tr>
<tr>
<td><mark>HTTP Source</mark></td>
<td>Kite Dataset Sink</td>
<td></td>
</tr>
<tr>
<td>Stress Source</td>
<td>Kafka Sink</td>
<td></td>
</tr>
<tr>
<td>Legacy Sources</td>
<td>HTTP Sink</td>
<td></td>
</tr>
<tr>
<td>Avro Legacy Source</td>
<td>Custom Sink</td>
<td></td>
</tr>
<tr>
<td>Thrift Legacy Source</td>
<td></td>
<td></td>
</tr>
<tr>
<td>Custom Source</td>
<td></td>
<td></td>
</tr>
<tr>
<td>Scribe Source</td>
<td></td>
<td></td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
</tr>
</tbody>
</table><p>一大堆的英文，有木有引起你的密集恐惧症？<br>
划重点，以上标黄项为常用类型！</p>
<p>1.Source: Avro(序列化信息)、SpoolingDirectory(文件监听)、Http<br>
2.Channel: Memory(内存)<br>
3.Sink:HDFS(Hadoop的数据的存储技术)、Logger(日志)、Avro(序列化信息)</p>
<h6><a id="_78"></a>问题来了</h6>
<p>这些什么类型的，怎么玩？<br>
首先，你要启动Hadoop<br>
最后，你要启动Flume<br>
中间呢？当然是修改配置文件玩~<br>
打开Flume目录下的conf文件夹，找到flume.properties，配置！</p>
<blockquote>
<p>如果需要绑定多个组件，空格分开就好，例如a1.sources  =  r1 r2 r3</p>
</blockquote>
<pre><code>a1.sources  =  r1		#定义agent的数据源source，可以有多个。
a1.sinks  =  k1			#定义agent的数据出处，可以有多个。
a1.channels  =  c1		#定义agent的通道，一般有多少个sink就有多少个channel

a1.sources.r1.type  =  http			#【!】指定source的类型为http
a1.sources.r1.bind  =  0.0.0.0		#指定source的来源。一般为本机，被动接收。
a1.sources.r1.port  =  22222		#指定端口

a1.sinks.k1.type  =  avro						#【!】指定sink的类型为avro
a1.sinks.k1.hostname  =  111.111.111.111		#指定sink的目标节点IP
a1.sinks.k1.port  =  22222						#指定目标端口

a1.channels.c1.type  =  memory						#指定channel的类型为 内存
a1.channels.c1.capacity  =  1000					#指定存储容量，避免强制抢占内存影响其他进程的正常运行
a1.channels.c1.transactionCapacity  =  100			#指定事物容量

a1.sources.r1.channels  =  c1		#绑定source
a1.sinks.k1.channel  =  c1		#绑定sink

</code></pre>
<p>如果Sink为logger的时候，要进行如下修改：</p>
<pre><code>a1.sinks.k1.type  =  logger
</code></pre>
<p>如果Source是要做文件监听SpoolingDirectory，要这样（自定义的文件夹要存在哦）：</p>
<pre><code>a1.sources.r1.type  =  spooldir
a1.sources.r1.spoolDir = /usr/local/src/flume/bin/temp
</code></pre>
<h4><a id="_119"></a>什么！？你不会启动？玩我吗？</h4>
<p>谁叫我人这么好？好吧，大发慈悲告诉你！下次自己看官网啦~<br>
划重点！Agent的启动要在bin目录底下进行（或者指令指向bin）~</p>
<pre><code>./flume-ng agent -c ./ -f ./flume.properties -n a1 -Dflume.root.logger=INFO,console
</code></pre>
<ol>
<li>Source = Avro<br>
划重点！启动Agent后，要在bin目录底下执行以下代码~<br>
然后建个测试用的文件，conf/temp.txt</li>
</ol>
<pre><code>./flume-ng avro-client -c ../conf -H 0.0.0.0 -p 22222 -F temp.txt
</code></pre>
<ol start="2">
<li>Source = Http<br>
任意目录执行都行</li>
</ol>
<pre><code>curl -X POST -d '[{"headers":{"tester":"i am superman"},"body":"flume flume"}]' http://0.0.0.0:22222
</code></pre>
<ol start="3">
<li>Source = SpoolingDirectory<br>
启动好Flume就好了，然后往指定自定义文件夹内添加东西。</li>
</ol>
<blockquote>
<p><strong>划重点</strong><br>
只能监听到一级目录已下的文件变化，例如监听 temp文件夹，但是temp/aa/1.txt是监听不到的。为什么？因为监听到的文件会被改名字，加了个后缀识别监听状态，至于是什么字段，自己尝试下吧~</p>
</blockquote>
<hr>
<h2><a id="Flume_147"></a>【超级干货】那些关于Flume的那些<mark>面试题</mark></h2>
<blockquote>
<p>面试题？说是面试题，不如说是测测大家对Flume理解层度的题目<br>
反正做一做，肯定有益处~</p>
</blockquote>
<p><strong>1 flume管道内存，flume宕机了数据丢失怎么解决</strong></p>
<p>解答：</p>
<p>1、Flume的channel分为很多种，可以将数据写入到文件</p>
<p>2、防止非首个agent宕机的方法数可以做集群或者主备</p>
<p><strong>2 flume配置方式，flume集群（问的很详细）</strong></p>
<p>解答：</p>
<p>Flume的配置围绕着source、channel、sink叙述，flume的集群是做在agent上的，而非机器上。</p>
<p><strong>3 flume不采集Nginx日志，通过Logger4j采集日志，优缺点是什么？</strong></p>
<p>解答：</p>
<p>优点：Nginx的日志格式是固定的，但是缺少sessionid，通过logger4j采集的日志是带有sessionid的，而session可以通过redis共享，保证了集群日志中的同一session落到不同的tomcat时，sessionId还是一样的，而且logger4j的方式比较稳定，不会宕机。</p>
<p>缺点：不够灵活，logger4j的方式和项目结合过于紧密，而flume的方式比较灵活，拔插式比较好，不会影响项目性能。</p>
<p><strong>4 flume和kafka采集日志区别，采集日志时中间停了，怎么记录之前的日志。</strong></p>
<p>解答：</p>
<p>Flume采集日志是通过流的方式直接将日志收集到存储层，而kafka试讲日志缓存在kafka集群，待后期可以采集到存储层。</p>
<p>Flume采集中间停了，可以采用文件的方式记录之前的日志，而kafka是采用offset的方式记录之前的日志。</p>
<blockquote>
<p>以上四题转载自风远陌小哥哥的博文：<a href="https://blog.csdn.net/albg_boy/article/details/78424509" rel="nofollow">https://blog.csdn.net/albg_boy/article/details/78424509</a></p>
</blockquote>
<p><strong>5 Flume事务的处理</strong><br>
小编年纪尚轻，这个还不会回答，但估计是跟Channel有关，但是身边有小伙伴去面试被问到了。<br>
勉为其难翻了一下伟大的CSDN，找到了个比较好的博文，大家参考下。<br>
等小编发奋图强，一定把这东西嚼碎吐出来分享给大家…</p>
<blockquote>
<p>flume的事务实现<br>
作者：lilyjoke  (估计也是个好看的小哥哥)<br>
原文：<a href="https://blog.csdn.net/lilyjoke/article/details/81363906" rel="nofollow">https://blog.csdn.net/lilyjoke/article/details/81363906</a></p>
</blockquote>
<hr>
<p>完</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>