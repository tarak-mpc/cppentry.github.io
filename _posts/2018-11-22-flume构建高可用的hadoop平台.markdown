---
layout:     post
title:      flume构建高可用的hadoop平台
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h3 style="font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;line-height:1.6em;color:rgb(51,51,51);font-size:18px;text-indent:1em;background-color:rgb(254,254,254);">
1.概述</h3>
<p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
今天补充一篇关于Flume的博客，前面在讲解高可用的Hadoop平台的时候遗漏了这篇，本篇博客为大家讲述以下内容：</p>
<ul style="list-style-type:none;font-size:16px;line-height:27.2px;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);"><li style="line-height:1.7em;list-style-type:disc;">Flume NG简述</li><li style="line-height:1.7em;list-style-type:disc;">单点Flume NG搭建、运行</li><li style="line-height:1.7em;list-style-type:disc;">高可用Flume NG搭建</li><li style="line-height:1.7em;list-style-type:disc;">Failover测试</li><li style="line-height:1.7em;list-style-type:disc;">截图预览</li></ul><p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
下面开始今天的博客介绍。</p>
<h3 style="font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;line-height:1.6em;color:rgb(51,51,51);font-size:18px;text-indent:1em;background-color:rgb(254,254,254);">
2.Flume NG简述</h3>
<p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
Flume NG是一个分布式，高可用，可靠的系统，它能将不同的海量数据收集，移动并存储到一个数据存储系统中。轻量，配置简单，适用于各种日志收集，并支持 Failover和负载均衡。并且它拥有非常丰富的组件。Flume NG采用的是三层架构：Agent层，Collector层和Store层，每一层均可水平拓展。其中Agent包含Source，Channel和 Sink，三者组建了一个Agent。三者的职责如下所示：</p>
<ul style="list-style-type:none;font-size:16px;line-height:27.2px;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);"><li style="line-height:1.7em;list-style-type:disc;">Source：用来消费（收集）数据源到Channel组件中</li><li style="line-height:1.7em;list-style-type:disc;">Channel：中转临时存储，保存所有Source组件信息</li><li style="line-height:1.7em;list-style-type:disc;">Sink：从Channel中读取，读取成功后会删除Channel中的信息</li></ul><p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
下图是Flume NG的架构图，如下所示：</p>
<p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<img src="http://img2.tuicool.com/NN7Fvq.png!web" class="alignCenter" alt="" style="vertical-align:middle;border:0px none;text-align:center;display:block;"></p>
<p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
图中描述了，从外部系统（Web Server）中收集产生的日志，然后通过Flume的Agent的Source组件将数据发送到临时存储Channel组件，最后传递给Sink组件，Sink组件直接把数据存储到HDFS文件系统中。</p>
<h3 style="font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;line-height:1.6em;color:rgb(51,51,51);font-size:18px;text-indent:1em;background-color:rgb(254,254,254);">
3.单点Flume NG搭建、运行</h3>
<p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
我们在熟悉了Flume NG的架构后，我们先搭建一个单点Flume收集信息到HDFS集群中，由于资源有限，本次直接在之前的高可用Hadoop集群上搭建Flume。</p>
<p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
场景如下：在NNA节点上搭建一个Flume NG，将本地日志收集到HDFS集群。</p>
<h3 style="font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;line-height:1.6em;color:rgb(51,51,51);font-size:18px;text-indent:1em;background-color:rgb(254,254,254);">
3.1基础软件</h3>
<p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
在搭建Flume NG之前，我们需要准备必要的软件，具体下载地址如下所示：</p>
<ul style="list-style-type:none;font-size:16px;line-height:27.2px;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);"><li style="line-height:1.7em;list-style-type:disc;">Flume <a href="http://www.apache.org/dyn/closer.cgi/flume/1.5.2/apache-flume-1.5.2-bin.tar.gz" rel="nofollow" style="color:rgb(148,148,148);text-decoration:none;border-bottom-width:1px;border-bottom-style:dashed;border-bottom-color:rgb(148,148,148);font-style:italic;font-weight:bold;">《下载地址》</a></li></ul><p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
JDK由于之前在安装Hadoop集群时已经配置过，这里就不赘述了，若需要配置的同学，可参考《 <a href="http://www.cnblogs.com/smartloli/p/4298430.html" rel="nofollow" style="color:rgb(148,148,148);text-decoration:none;border-bottom-width:1px;border-bottom-style:dashed;border-bottom-color:rgb(148,148,148);font-style:italic;font-weight:bold;">配置高可用的Hadoop平台</a> 》。</p>
<h3 style="font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;line-height:1.6em;color:rgb(51,51,51);font-size:18px;text-indent:1em;background-color:rgb(254,254,254);">
3.2安装与配置</h3>
<ul style="list-style-type:none;font-size:16px;line-height:27.2px;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);"><li style="line-height:1.7em;list-style-type:disc;">安装</li></ul><p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
首先，我们解压flume安装包，命令如下所示：</p>
<pre><code class="language-ruby">[hadoop<span class="variable" style="color:rgb(0,128,128);">@nna</span> ~]<span class="variable" style="color:rgb(0,128,128);">$ </span><span style="color:rgb(0,0,255);">tar</span> -zxvf apache-flume-<span style="color:rgb(128,0,128);"><span class="number" style="color:rgb(0,153,153);">1.5</span></span>.<span style="color:rgb(128,0,128);"><span class="number" style="color:rgb(0,153,153);">2</span></span>-bin.<span style="color:rgb(0,0,255);">tar</span>.gz</code></pre>
<ul style="list-style-type:none;font-size:16px;line-height:27.2px;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);"><li style="line-height:1.7em;list-style-type:disc;">配置</li></ul><p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
环境变量配置内容如下所示：</p>
<pre class="prettyprint bash" style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;color:rgb(51,51,51);line-height:1.5em;background-color:rgb(246,246,246);">export FLUME_HOME=/home/hadoop/flume-<span style="color:rgb(128,0,128);">1.5</span>.<span style="color:rgb(128,0,128);">2</span><span style="color:rgb(0,0,0);">
export PATH</span>=<span class="variable" style="color:rgb(0,128,128);">$PATH</span>:<span class="variable" style="color:rgb(0,128,128);">$FLUME_HOME</span>/bin</pre>
<p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
flume-conf.properties</p>
<pre class="prettyprint perl" style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;color:rgb(51,51,51);line-height:1.5em;background-color:rgb(246,246,246);"><span style="color:rgb(0,0,0);"><span class="comment" style="color:rgb(153,153,136);font-style:italic;"><span class="comment">#agent1 name</span></span>
agent1.sources</span>=<span style="color:rgb(0,0,0);">source1
agent1.sinks</span>=<span style="color:rgb(0,0,0);">sink1
agent1.channels</span>=<span style="color:rgb(0,0,0);">channel1


<span class="comment" style="color:rgb(153,153,136);font-style:italic;"><span class="comment">#Spooling Directory</span></span>
<span class="comment" style="color:rgb(153,153,136);font-style:italic;"><span class="comment">#set source1</span></span>
agent1.sources.source1.type</span>=<span style="color:rgb(0,0,0);">spooldir
agent1.sources.source1.spoolDir</span>=<span class="regexp" style="color:rgb(0,153,38);">/home/hadoop</span><span class="regexp" style="color:rgb(0,153,38);"><span class="regexp">/<span style="color:rgb(0,0,255);">dir</span>/<span style="color:rgb(0,0,0);">logdfs</span></span><span style="color:rgb(0,0,0);"></span></span><span style="color:rgb(0,0,0);">
agent1.sources.source1.channels</span>=<span style="color:rgb(0,0,0);">channel1
agent1.sources.source1.fileHeader </span>= <span style="color:rgb(0,0,255);">false</span><span style="color:rgb(0,0,0);">
agent1.sources.source1.interceptors </span>=<span style="color:rgb(0,0,0);"> i1
agent1.sources.source1.interceptors.i1.type </span>=<span style="color:rgb(0,0,0);"> timestamp

<span class="comment" style="color:rgb(153,153,136);font-style:italic;"><span class="comment">#set sink1</span></span>
agent1.sinks.sink1.type</span>=<span style="color:rgb(0,0,0);">hdfs
agent1.sinks.sink1.hdfs.path</span>=<span class="regexp" style="color:rgb(0,153,38);">/home/hdfs</span><span class="regexp" style="color:rgb(0,153,38);"><span class="regexp">/flume/<span style="color:rgb(0,0,0);">logdfs</span></span><span style="color:rgb(0,0,0);"></span></span><span style="color:rgb(0,0,0);">
agent1.sinks.sink1.hdfs.fileType</span>=<span style="color:rgb(0,0,0);">DataStream
agent1.sinks.sink1.hdfs.writeFormat</span>=<span style="color:rgb(0,0,0);">TEXT
agent1.sinks.sink1.hdfs.rollInterval</span>=<span style="color:rgb(128,0,128);"><span class="number" style="color:rgb(0,153,153);">1</span></span><span style="color:rgb(0,0,0);">
agent1.sinks.sink1.channel</span>=<span style="color:rgb(0,0,0);">channel1
agent1.sinks.sink1.hdfs.filePrefix</span>=<span class="variable" style="color:rgb(0,128,128);">%Y</span>-<span class="variable" style="color:rgb(0,128,128);">%m</span>-<span class="variable" style="color:rgb(0,128,128);"><span class="variable">%<span style="color:rgb(0,0,0);">d</span></span><span style="color:rgb(0,0,0);"></span></span><span style="color:rgb(0,0,0);">

<span class="comment" style="color:rgb(153,153,136);font-style:italic;"><span class="comment">#set channel1</span></span>
agent1.channels.channel1.type</span>=<span style="color:rgb(0,0,255);">file</span><span style="color:rgb(0,0,0);">
agent1.channels.channel1.checkpointDir</span>=<span class="regexp" style="color:rgb(0,153,38);">/home/hadoop</span><span class="regexp" style="color:rgb(0,153,38);">/<span style="color:rgb(0,0,255);">dir</span>/logdfstmp</span><span class="regexp" style="color:rgb(0,153,38);">/<span style="color:rgb(0,0,0);">point
agent1.channels.channel1.dataDirs</span>=/home</span><span class="regexp" style="color:rgb(0,153,38);"><span class="regexp">/hadoop/<span style="color:rgb(0,0,255);">dir</span></span><span style="color:rgb(0,0,255);"></span></span><span class="regexp" style="color:rgb(0,153,38);">/logdfstmp</span></pre>
<p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
flume-env.sh</p>
<pre><code class="language-ruby"><span class="constant">JAVA_HOME</span>=<span class="regexp" style="color:rgb(0,153,38);">/usr/java</span><span class="regexp" style="color:rgb(0,153,38);"><span class="regexp">/jdk1.<span style="color:rgb(128,0,128);">7</span></span><span style="color:rgb(128,0,128);"></span></span></code></pre>
<p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
注：配置中的目录若不存在，需提前创建。</p>
<h3 style="font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;line-height:1.6em;color:rgb(51,51,51);font-size:18px;text-indent:1em;background-color:rgb(254,254,254);">
3.3启动</h3>
<p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
启动命令如下所示：</p>
<pre class="prettyprint undefined" style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;color:rgb(51,51,51);line-height:1.5em;background-color:rgb(246,246,246);">flume-ng agent -n agent1 -c conf -f flume-conf.properties -Dflume.root.logger=DEBUG,console</pre>
<p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
注：命令中的agent1表示配置文件中的Agent的Name，如配置文件中的agent1。flume-conf.properties表示配置文件所在配置，需填写准确的配置文件路径。</p>
<h3 style="font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;line-height:1.6em;color:rgb(51,51,51);font-size:18px;text-indent:1em;background-color:rgb(254,254,254);">
3.4效果预览</h3>
<p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<img src="http://img0.tuicool.com/ueiaIv.png!web" class="alignCenter" alt="" style="vertical-align:middle;border:0px none;text-align:center;display:block;"></p>
<p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<img src="http://img0.tuicool.com/Q7FRR3.png!web" class="alignCenter" alt="" style="vertical-align:middle;border:0px none;text-align:center;display:block;"></p>
<p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<img src="http://img2.tuicool.com/aQJv6fZ.png!web" class="alignCenter" alt="" style="vertical-align:middle;border:0px none;text-align:center;display:block;"></p>
<p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
之后，成功上传后本地目的会被标记完成。如下图所示：</p>
<p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<img src="http://img2.tuicool.com/IBVJNv.png!web" class="alignCenter" alt="" style="vertical-align:middle;border:0px none;text-align:center;display:block;"></p>
<h3 style="font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;line-height:1.6em;color:rgb(51,51,51);font-size:18px;text-indent:1em;background-color:rgb(254,254,254);">
4.高可用Flume NG搭建</h3>
<p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
在完成单点的Flume NG搭建后，下面我们搭建一个高可用的Flume NG集群，架构图如下所示：</p>
<p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<img src="http://img0.tuicool.com/Af6beaV.png!web" class="alignCenter" alt="" style="vertical-align:middle;border:0px none;text-align:center;display:block;"></p>
<p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
图中，我们可以看出，Flume的存储可以支持多种，这里只列举了HDFS和Kafka（如：存储最新的一周日志，并给Storm系统提供实时日志流）。</p>
<h3 style="font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;line-height:1.6em;color:rgb(51,51,51);font-size:18px;text-indent:1em;background-color:rgb(254,254,254);">
4.1节点分配</h3>
<p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
Flume的Agent和Collector分布如下表所示：</p>
<table class="table table-bordered" style="border-spacing:0px;width:605px;border-width:1px 1px 1px 0px;border-top-style:solid;border-right-style:solid;border-bottom-style:solid;border-top-color:rgb(221,221,221);border-right-color:rgb(221,221,221);border-bottom-color:rgb(221,221,221);font-size:14px;line-height:1.3em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);"><tbody><tr style="line-height:1.3em;"><td style="line-height:1.3em;vertical-align:top;border-top-width:0px;border-left-width:1px;border-left-style:solid;border-left-color:rgb(221,221,221);font-size:14px;">
名称</td>
<td style="line-height:1.3em;vertical-align:top;border-top-width:0px;border-left-width:1px;border-left-style:solid;border-left-color:rgb(221,221,221);font-size:14px;">
HOST</td>
<td style="line-height:1.3em;vertical-align:top;border-top-width:0px;border-left-width:1px;border-left-style:solid;border-left-color:rgb(221,221,221);font-size:14px;">
角色</td>
</tr><tr style="line-height:1.3em;"><td style="line-height:1.3em;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-left-width:1px;border-left-style:solid;border-left-color:rgb(221,221,221);font-size:14px;">
Agent1</td>
<td style="line-height:1.3em;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-left-width:1px;border-left-style:solid;border-left-color:rgb(221,221,221);font-size:14px;">
10.211.55.14</td>
<td style="line-height:1.3em;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-left-width:1px;border-left-style:solid;border-left-color:rgb(221,221,221);font-size:14px;">
Web Server</td>
</tr><tr style="line-height:1.3em;"><td style="line-height:1.3em;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-left-width:1px;border-left-style:solid;border-left-color:rgb(221,221,221);font-size:14px;">
Agent2</td>
<td style="line-height:1.3em;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-left-width:1px;border-left-style:solid;border-left-color:rgb(221,221,221);font-size:14px;">
10.211.55.15</td>
<td style="line-height:1.3em;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-left-width:1px;border-left-style:solid;border-left-color:rgb(221,221,221);font-size:14px;">
Web Server</td>
</tr><tr style="line-height:1.3em;"><td style="line-height:1.3em;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-left-width:1px;border-left-style:solid;border-left-color:rgb(221,221,221);font-size:14px;">
Agent3</td>
<td style="line-height:1.3em;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-left-width:1px;border-left-style:solid;border-left-color:rgb(221,221,221);font-size:14px;">
10.211.55.16</td>
<td style="line-height:1.3em;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-left-width:1px;border-left-style:solid;border-left-color:rgb(221,221,221);font-size:14px;">
Web Server</td>
</tr><tr style="line-height:1.3em;"><td style="line-height:1.3em;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-left-width:1px;border-left-style:solid;border-left-color:rgb(221,221,221);font-size:14px;">
Collector1</td>
<td style="line-height:1.3em;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-left-width:1px;border-left-style:solid;border-left-color:rgb(221,221,221);font-size:14px;">
10.211.55.18</td>
<td style="line-height:1.3em;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-left-width:1px;border-left-style:solid;border-left-color:rgb(221,221,221);font-size:14px;">
AgentMstr1</td>
</tr><tr style="line-height:1.3em;"><td style="line-height:1.3em;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-left-width:1px;border-left-style:solid;border-left-color:rgb(221,221,221);font-size:14px;">
Collector2</td>
<td style="line-height:1.3em;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-left-width:1px;border-left-style:solid;border-left-color:rgb(221,221,221);font-size:14px;">
10.211.55.19</td>
<td style="line-height:1.3em;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-left-width:1px;border-left-style:solid;border-left-color:rgb(221,221,221);font-size:14px;">
AgentMstr2</td>
</tr></tbody></table><p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
图中所示，Agent1，Agent2，Agent3数据分别流入到Collector1和Collector2，Flume NG本身提供了Failover机制，可以自动切换和恢复。在上图中，有3个产生日志服务器分布在不同的机房，要把所有的日志都收集到一个集群中存储。下 面我们开发配置Flume NG集群</p>
<h3 style="font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;line-height:1.6em;color:rgb(51,51,51);font-size:18px;text-indent:1em;background-color:rgb(254,254,254);">
4.2配置</h3>
<p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
在下面单点Flume中，基本配置都完成了，我们只需要新添加两个配置文件，它们是flume-client.properties和flume-server.properties，其配置内容如下所示：</p>
<ul style="list-style-type:none;font-size:16px;line-height:27.2px;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);"><li style="line-height:1.7em;list-style-type:disc;">flume-client.properties</li></ul><pre><code class="language-sql"><span style="color:rgb(0,0,0);">#agent1 name
agent1.channels </span>=<span style="color:rgb(0,0,0);"> c1
agent1.sources </span>=<span style="color:rgb(0,0,0);"> r1
agent1.sinks </span>=<span style="color:rgb(0,0,0);"> k1 k2

#<span class="operator"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;"><span class="operator"><span class="keyword">set</span></span></span><span class="operator"> gruop
agent1.sinkgroups </span></span><span class="operator"></span></span><span class="operator"><span class="operator">=<span style="color:rgb(0,0,0);"> g1 

#<span class="keyword" style="color:rgb(51,51,51);font-weight:bold;"><span class="keyword">set</span></span> channel
agent1.channels.c1.type </span>=<span style="color:rgb(0,0,0);"> memory
agent1.channels.c1.capacity </span>= <span style="color:rgb(128,0,128);"><span class="number" style="color:rgb(0,153,153);"><span class="number">1000</span></span></span><span style="color:rgb(0,0,0);">
agent1.channels.c1.transactionCapacity </span>= <span style="color:rgb(128,0,128);"><span class="number" style="color:rgb(0,153,153);"><span class="number">100</span></span></span><span style="color:rgb(0,0,0);">

agent1.sources.r1.channels </span>=<span style="color:rgb(0,0,0);"> c1
agent1.sources.r1.type </span>=<span style="color:rgb(0,0,0);"> <span class="keyword" style="color:rgb(51,51,51);font-weight:bold;"><span class="keyword">exec</span></span>
agent1.sources.r1.command </span>= <span style="color:rgb(0,0,255);">tail</span> -F /home/hadoop/<span style="color:rgb(0,0,255);">dir</span>/logdfs/<span style="color:rgb(0,0,0);">test.log

agent1.sources.r1.interceptors </span>=<span style="color:rgb(0,0,0);"> i1 i2
agent1.sources.r1.interceptors.i1.type </span>=<span style="color:rgb(0,0,0);"> static
agent1.sources.r1.interceptors.i1.<span class="keyword" style="color:rgb(51,51,51);font-weight:bold;"><span class="keyword">key</span></span> </span>=<span style="color:rgb(0,0,0);"> Type
agent1.sources.r1.interceptors.i1.<span class="keyword" style="color:rgb(51,51,51);font-weight:bold;"><span class="keyword">value</span></span> </span>=<span style="color:rgb(0,0,0);"> LOGIN
agent1.sources.r1.interceptors.i2.type </span>=<span style="color:rgb(0,0,0);"> <span class="keyword" style="color:rgb(51,51,51);font-weight:bold;"><span class="keyword">timestamp</span></span>

# <span class="keyword" style="color:rgb(51,51,51);font-weight:bold;"><span class="keyword">set</span></span> sink1
agent1.sinks.k1.channel </span>=<span style="color:rgb(0,0,0);"> c1
agent1.sinks.k1.type </span>=<span style="color:rgb(0,0,0);"> avro
agent1.sinks.k1.</span><span style="color:rgb(0,0,255);">hostname</span> =<span style="color:rgb(0,0,0);"> nna
agent1.sinks.k1.port </span>= <span style="color:rgb(128,0,128);"><span class="number" style="color:rgb(0,153,153);"><span class="number">52020</span></span></span><span style="color:rgb(0,0,0);">

# <span class="keyword" style="color:rgb(51,51,51);font-weight:bold;"><span class="keyword">set</span></span> sink2
agent1.sinks.k2.channel </span>=<span style="color:rgb(0,0,0);"> c1
agent1.sinks.k2.type </span>=<span style="color:rgb(0,0,0);"> avro
agent1.sinks.k2.</span><span style="color:rgb(0,0,255);">hostname</span> =<span style="color:rgb(0,0,0);"> nns
agent1.sinks.k2.port </span>= <span style="color:rgb(128,0,128);"><span class="number" style="color:rgb(0,153,153);"><span class="number">52020</span></span></span><span style="color:rgb(0,0,0);">

#<span class="keyword" style="color:rgb(51,51,51);font-weight:bold;"><span class="keyword">set</span></span> sink <span class="keyword" style="color:rgb(51,51,51);font-weight:bold;"><span class="keyword">group</span></span>
agent1.sinkgroups.g1.sinks </span>=<span style="color:rgb(0,0,0);"> k1 k2

#<span class="keyword" style="color:rgb(51,51,51);font-weight:bold;"><span class="keyword">set</span></span> failover
agent1.sinkgroups.g1.processor.type </span>=<span style="color:rgb(0,0,0);"> failover
agent1.sinkgroups.g1.processor.priority.k1 </span>= <span style="color:rgb(128,0,128);"><span class="number" style="color:rgb(0,153,153);"><span class="number">10</span></span></span><span style="color:rgb(0,0,0);">
agent1.sinkgroups.g1.processor.priority.k2 </span>= <span style="color:rgb(128,0,128);"><span class="number" style="color:rgb(0,153,153);"><span class="number">1</span></span></span><span style="color:rgb(0,0,0);">
agent1.sinkgroups.g1.processor.maxpenalty </span>= <span style="color:rgb(128,0,128);"><span class="number" style="color:rgb(0,153,153);"><span class="number">10000</span></span></span></span></span><span class="operator"><span style="color:rgb(128,0,128);"><span class="number" style="color:rgb(0,153,153);"></span></span></span></code></pre>
<p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
注：指定Collector的IP和Port。</p>
<ul style="list-style-type:none;font-size:16px;line-height:27.2px;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);"><li style="line-height:1.7em;list-style-type:disc;">flume-server.properties</li></ul><pre><code class="language-sql"><span style="color:rgb(0,0,0);">#<span class="operator"><span class="keyword" style="color:rgb(51,51,51);font-weight:bold;"><span class="operator"><span class="keyword">set</span></span></span><span class="operator"> Agent name
a1.sources </span></span><span class="operator"></span></span><span class="operator">=<span style="color:rgb(0,0,0);"> r1
a1.channels </span>=<span style="color:rgb(0,0,0);"> c1
a1.sinks </span>=<span style="color:rgb(0,0,0);"> k1

#<span class="keyword" style="color:rgb(51,51,51);font-weight:bold;"><span class="keyword">set</span></span> channel
a1.channels.c1.type </span>=<span style="color:rgb(0,0,0);"> memory
a1.channels.c1.capacity </span>= <span style="color:rgb(128,0,128);"><span class="number" style="color:rgb(0,153,153);"><span class="number">1000</span></span></span><span style="color:rgb(0,0,0);">
a1.channels.c1.transactionCapacity </span>= <span style="color:rgb(128,0,128);"><span class="number" style="color:rgb(0,153,153);"><span class="number">100</span></span></span><span style="color:rgb(0,0,0);">

# other node,nna <span class="keyword" style="color:rgb(51,51,51);font-weight:bold;"><span class="keyword">to</span></span> nns
a1.sources.r1.type </span>=<span style="color:rgb(0,0,0);"> avro
a1.sources.r1.bind </span>=<span style="color:rgb(0,0,0);"> nna
a1.sources.r1.port </span>= <span style="color:rgb(128,0,128);"><span class="number" style="color:rgb(0,153,153);"><span class="number">52020</span></span></span><span style="color:rgb(0,0,0);">
a1.sources.r1.interceptors </span>=<span style="color:rgb(0,0,0);"> i1
a1.sources.r1.interceptors.i1.type </span>=<span style="color:rgb(0,0,0);"> static
a1.sources.r1.interceptors.i1.<span class="keyword" style="color:rgb(51,51,51);font-weight:bold;"><span class="keyword">key</span></span> </span>=<span style="color:rgb(0,0,0);"> Collector
a1.sources.r1.interceptors.i1.<span class="keyword" style="color:rgb(51,51,51);font-weight:bold;"><span class="keyword">value</span></span> </span>=<span style="color:rgb(0,0,0);"> NNA
a1.sources.r1.channels </span>=<span style="color:rgb(0,0,0);"> c1

#<span class="keyword" style="color:rgb(51,51,51);font-weight:bold;"><span class="keyword">set</span></span> sink <span class="keyword" style="color:rgb(51,51,51);font-weight:bold;"><span class="keyword">to</span></span> hdfs
a1.sinks.k1.type</span>=<span style="color:rgb(0,0,0);">hdfs
a1.sinks.k1.hdfs.path</span>=/home/hdfs/flume/<span style="color:rgb(0,0,0);">logdfs
a1.sinks.k1.hdfs.fileType</span>=<span style="color:rgb(0,0,0);">DataStream
a1.sinks.k1.hdfs.writeFormat</span>=<span style="color:rgb(0,0,0);">TEXT
a1.sinks.k1.hdfs.rollInterval</span>=<span style="color:rgb(128,0,128);"><span class="number" style="color:rgb(0,153,153);"><span class="number">1</span></span></span><span style="color:rgb(0,0,0);">
a1.sinks.k1.channel</span>=<span style="color:rgb(0,0,0);">c1
a1.sinks.k1.hdfs.filePrefix</span>=%Y-%m-%d</span><span class="operator"></span></code></pre>
<p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
注：在另一台Collector节点上修改IP，如在NNS节点将绑定的对象有nna修改为nns。</p>
<h3 style="font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;line-height:1.6em;color:rgb(51,51,51);font-size:18px;text-indent:1em;background-color:rgb(254,254,254);">
4.3启动</h3>
<p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
在Agent节点上启动命令如下所示：</p>
<pre class="prettyprint undefined" style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;color:rgb(51,51,51);line-height:1.5em;background-color:rgb(246,246,246);">flume-ng agent -n agent1 -c conf -f flume-client.properties -Dflume.root.logger=DEBUG,console</pre>
<p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
注：命令中的agent1表示配置文件中的Agent的Name，如配置文件中的agent1。flume-client.properties表示配置文件所在配置，需填写准确的配置文件路径。</p>
<p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
在Collector节点上启动命令如下所示：</p>
<pre class="prettyprint undefined" style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;color:rgb(51,51,51);line-height:1.5em;background-color:rgb(246,246,246);">flume-ng agent -n a1 -c conf -f flume-server.properties -Dflume.root.logger=DEBUG,console</pre>
<p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
注：命令中的a1表示配置文件中的Agent的Name，如配置文件中的a1。flume-server.properties表示配置文件所在配置，需填写准确的配置文件路径。</p>
<h3 style="font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;line-height:1.6em;color:rgb(51,51,51);font-size:18px;text-indent:1em;background-color:rgb(254,254,254);">
5.Failover测试</h3>
<p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
下面我们来测试下Flume NG集群的高可用（故障转移）。场景如下：我们在Agent1节点上传文件，由于我们配置Collector1的权重比Collector2大，所以 Collector1优先采集并上传到存储系统。然后我们kill掉Collector1，此时有Collector2负责日志的采集上传工作，之后，我 们手动恢复Collector1节点的Flume服务，再次在Agent1上次文件，发现Collector1恢复优先级别的采集工作。具体截图如下所 示：</p>
<ul style="list-style-type:none;font-size:16px;line-height:27.2px;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);"><li style="line-height:1.7em;list-style-type:disc;">Collector1优先上传</li></ul><p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<img src="http://img2.tuicool.com/ieii2q.png!web" class="alignCenter" alt="" style="vertical-align:middle;border:0px none;text-align:center;display:block;"></p>
<ul style="list-style-type:none;font-size:16px;line-height:27.2px;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);"><li style="line-height:1.7em;list-style-type:disc;">HDFS集群中上传的log内容预览</li></ul><p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<img src="http://img2.tuicool.com/MJbQf2.png!web" class="alignCenter" alt="" style="vertical-align:middle;border:0px none;text-align:center;display:block;"></p>
<ul style="list-style-type:none;font-size:16px;line-height:27.2px;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);"><li style="line-height:1.7em;list-style-type:disc;">Collector1宕机，Collector2获取优先上传权限</li></ul><p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<img src="http://img1.tuicool.com/Ffea6j3.png!web" class="alignCenter" alt="" style="vertical-align:middle;border:0px none;text-align:center;display:block;"></p>
<ul style="list-style-type:none;font-size:16px;line-height:27.2px;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);"><li style="line-height:1.7em;list-style-type:disc;">重启Collector1服务，Collector1重新获得优先上传的权限</li></ul><p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<img src="http://img0.tuicool.com/3MBnyqm.png!web" class="alignCenter" alt="" style="vertical-align:middle;border:0px none;text-align:center;display:block;"></p>
<h3 style="font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;line-height:1.6em;color:rgb(51,51,51);font-size:18px;text-indent:1em;background-color:rgb(254,254,254);">
6.截图预览</h3>
<p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
下面为大家附上HDFS文件系统中的截图预览，如下图所示：</p>
<ul style="list-style-type:none;font-size:16px;line-height:27.2px;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);"><li style="line-height:1.7em;list-style-type:disc;">HDFS文件系统中的文件预览</li></ul><p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<img src="http://img1.tuicool.com/qMBrQ3.png!web" class="alignCenter" alt="" style="vertical-align:middle;border:0px none;text-align:center;display:block;"></p>
<ul style="list-style-type:none;font-size:16px;line-height:27.2px;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);"><li style="line-height:1.7em;list-style-type:disc;">上传的文件内容预览</li></ul><p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<img src="http://img0.tuicool.com/F3AJF3I.png!web" class="alignCenter" alt="" style="vertical-align:middle;border:0px none;text-align:center;display:block;"></p>
<h3 style="font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;line-height:1.6em;color:rgb(51,51,51);font-size:18px;text-indent:1em;background-color:rgb(254,254,254);">
7.总结</h3>
<p style="font-size:16px;line-height:27.2px;text-indent:1em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
在配置高可用的Flume NG时，需要注意一些事项。在Agent中需要绑定对应的Collector1和Collector2的IP和Port，另外，在配置Collector 节点时，需要修改当前Flume节点的配置文件，Bind的IP（或HostName）为当前节点的IP（或HostName），最后，在启动的时候，指 定配置文件中的Agent的Name和配置文件的路径，否则会出错。</p>
            </div>
                </div>