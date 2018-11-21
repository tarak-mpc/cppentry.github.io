---
layout:     post
title:      Flume学习笔记：Flume的安装与基础应用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p id="main-toc"><strong>目录</strong></p>

<p id="Flume%E6%98%AF%E4%BB%80%E4%B9%88-toc" style="margin-left:0px;"><a href="#Flume%E6%98%AF%E4%BB%80%E4%B9%88" rel="nofollow">Flume是什么</a></p>

<p id="Flume%E5%9F%BA%E6%9C%AC%E6%9E%B6%E6%9E%84-toc" style="margin-left:0px;"><a href="#Flume%E5%9F%BA%E6%9C%AC%E6%9E%B6%E6%9E%84" rel="nofollow">Flume基本架构</a></p>

<p id="Event-toc" style="margin-left:40px;"><a href="#Event" rel="nofollow">Event</a></p>

<p id="Agent-toc" style="margin-left:40px;"><a href="#Agent" rel="nofollow">Agent</a></p>

<p id="Source-toc" style="margin-left:40px;"><a href="#Source" rel="nofollow">Source</a></p>

<p id="Channel-toc" style="margin-left:40px;"><a href="#Channel" rel="nofollow">Channel</a></p>

<p id="Sink-toc" style="margin-left:40px;"><a href="#Sink" rel="nofollow">Sink</a></p>

<p id="Flume%E7%9A%84%E5%AE%89%E8%A3%85-toc" style="margin-left:0px;"><a href="#Flume%E7%9A%84%E5%AE%89%E8%A3%85" rel="nofollow">Flume的安装</a></p>

<p id="%E6%B5%8B%E8%AF%95%E7%8E%AF%E5%A2%83-toc" style="margin-left:40px;"><a href="#%E6%B5%8B%E8%AF%95%E7%8E%AF%E5%A2%83" rel="nofollow">测试环境</a></p>

<hr id="hr-toc"><h1 id="Flume%E6%98%AF%E4%BB%80%E4%B9%88">Flume是什么</h1>

<p>Flume是Cloudera公司发布的开源的日志收集工具<br>
分布式、可靠和高可用的海量日志采集、聚合和传输的日志收集系统<br>
系统灵活性高：数据源可定制、可扩展；数据存储系统可定制、可扩展<br>
中间件：屏蔽了数据源和数据存储系统的异构性</p>

<h1 id="Flume%E5%9F%BA%E6%9C%AC%E6%9E%B6%E6%9E%84">Flume基本架构</h1>

<p style="text-align:center;"><img alt="" class="has" height="350" src="https://img-blog.csdn.net/20180824074520645?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xyeGNtd3ky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="687"></p>

<p style="text-align:center;"><img alt="" class="has" height="367" src="https://img-blog.csdn.net/20180824110038973?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xyeGNtd3ky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="688"></p>

<p> </p>

<h2 id="Event">Event</h2>

<p>Event是Flume数据传输的基本单元，Flume以Event的形式将数据从数据源传送到最终的目的地。<br>
Event由可选的header和载有数据的byte array构成，Header是容纳了kv字符串对的无序集合，key在集合里唯一。<br>
 </p>

<p style="text-align:center;"><img alt="" class="has" height="228" src="https://img-blog.csdn.net/20180824075842935?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xyeGNtd3ky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="457"></p>

<h2 id="Agent">Agent</h2>

<p>一个Agent包括了Source、Channel和Sink，Agent是Flume流的基础部分。<br>
Flume为这些组件提供了配置、生命周期管理、监控支持。<br>
Agent必须部署在采集的机器上。</p>

<h2 id="Source">Source</h2>

<p>Source负责接收Event或通过特殊机制产生Event，并将Event批量的放到一个或多个Channel<br>
包含Event驱动（即数据源将数据推到Source）和轮询（Source向数据源读数据）两种类型<br>
Source必须至少和一个Channel关联<br>
Source的不同类型：<br>
      与系统集成的Source：Syslog，Netcat，监测目录池<br>
      自动生成Event的Source：Exec<br>
      用于Agent和Agent之间通信的IPC Source：Avro、Thrift<br>
 </p>

<h2 id="Channel">Channel</h2>

<p>Channel位于Source和Sink之间，用于缓存从Source传进来的Event<br>
Channel成功缓存Event后，会将Event发送到Sink<br>
不同的Channel所提供的持久化水平是不同的<br>
       Memory Channel：volatile（不稳定的）<br>
       File Channel：基于WAL实现<br>
       JDBC Channel：基于嵌入式Database实现<br>
Channel可以和任意数量的Source、Sink工作</p>

<h2 id="Sink">Sink</h2>

<p>Sink负责将Event传输到下一个Source或者最终目的地，成功后该Event将从Channel中移除<br>
不同类型的silk<br>
       存储event到最终目的地终端sink,比如 HDFS,HBase<br>
       自动消耗的sink 比如 null sink<br>
       用于agent间通信的IPC：sink:Avro<br>
       必须作用于一个确切的channel</p>

<p>Interceptor：作用于Source，用于拦截、修改或者放弃Event。<br>
Channel Selector：允许Source基于预设的标准，从所有Channel中，选择一个或者多个Channel<br>
Sink Processor：多个Sink可以构成一个Sink Group，Sink Processor可以通过组中所有Sink实现负载均衡；也可以在一个Sink失败的时候转移到另一个</p>

<h1 id="Flume%E7%9A%84%E5%AE%89%E8%A3%85">Flume的安装</h1>

<p><a href="https://www.cnblogs.com/haozhengfei/p/2192231596ceb2ac4c22294dbd25a1ca.html" rel="nofollow">https://www.cnblogs.com/haozhengfei/p/2192231596ceb2ac4c22294dbd25a1ca.html</a><br>
这篇文章里讲述了五种情况下Flume的安装，在学习的过程中测试环境里只安装单个Flume。</p>

<h2 id="%E6%B5%8B%E8%AF%95%E7%8E%AF%E5%A2%83">测试环境</h2>

<p>hive-1.1.0-cdh5.14.0<br>
hbase-1.2.0-cdh5.14.0<br>
hadoop-2.6.0-cdh5.14.0<br>
zookeeper-3.4.5-cdh5.14.0<br>
jdk1.8.0_171<br>
apache-flume-1.6.0-cdh5.14.0-bin<br><br>
解压tar包后，将flume-env.sh-template改名为flume-env.sh，并更改JAVA_HOME<br><img alt="" class="has" height="482" src="https://img-blog.csdn.net/20180824102918184?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xyeGNtd3ky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1000"></p>

<p> 需求：监控文件夹/flumeagent，将新增加的文件进行采集并打印在console上<br>
写配置文件<br><img alt="" class="has" height="214" src="https://img-blog.csdn.net/20180824105408201?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xyeGNtd3ky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="506"></p>

<p>指定agent的名字为a1，然后指定对应source、channel、sink的名字，再对他们进行配置<br>
执行命令</p>

<pre class="has">
<code class="language-bash">flume-ng agent --conf $FLUME_HOME/conf/ --conf-file /agent.conf --name a1 -Dflume.root.logger=INFO,console</code></pre>

<p>会有相关提示<br><img alt="" class="has" height="482" src="https://img-blog.csdn.net/20180824105604483?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xyeGNtd3ky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1000"> 然后往文件夹里添加文件<br><img alt="" class="has" height="120" src="https://img-blog.csdn.net/20180824105711409?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xyeGNtd3ky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="586"><br>
Flume里也会采集到对应的文件<br><img alt="" class="has" height="96" src="https://img-blog.csdn.net/20180824105658758?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xyeGNtd3ky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1000"></p>

<p>进入文件夹可以看到文件被打上了Completed的标签，下次就不会重复采集了<br><img alt="" class="has" height="64" src="https://img-blog.csdn.net/20180824105808852?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xyeGNtd3ky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="442"></p>

<p> </p>            </div>
                </div>