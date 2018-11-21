---
layout:     post
title:      高可用Hadoop平台－Flume NG实战图解篇
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<table cellspacing="0" cellpadding="0" style="border-collapse:collapse;table-layout:fixed;color:rgb(68,68,68);font-family:Tahoma, Helvetica, SimSun, sans-serif;font-size:12px;line-height:18px;"><tbody><tr><td class="t_f" id="postmessage_367" style="font-size:14px;">
<br><span style="color:rgb(0,0,0);"><span style="font-family:Verdana, Arial, Helvetica, sans-serif;"><span style="font-weight:700;">1.概述</span></span></span>
<div align="left">　　今天补充一篇关于Flume的博客，前面在讲解高可用的Hadoop平台的时候遗漏了这篇，本篇博客为大家讲述以下内容：</div>
<ul><li style="list-style:disc;">
Flume NG简述</li><li style="list-style:disc;">
单点Flume NG搭建、运行</li><li style="list-style:disc;">
高可用Flume NG搭建</li><li style="list-style:disc;">
Failover测试</li><li style="list-style:disc;">
截图预览<br></li></ul><div align="left">　　下面开始今天的博客介绍。</div>
<span style="font-weight:700;">2.Flume NG简述</span>
<div align="left">　　Flume NG是一个分布式，高可用，可靠的系统，它能将不同的海量数据收集，移动并存储到一个数据存储系统中。轻量，配置简单，适用于各种日志收集，并支持Failover和负载均衡。并且它拥有非常丰富的组件。Flume NG采用的是三层架构：Agent层，Collector层和Store层，每一层均可水平拓展。其中Agent包含Source，Channel和Sink，三者组建了一个Agent。三者的职责如下所示：</div>
<ul><li style="list-style:disc;">
Source：用来消费（收集）数据源到Channel组件中</li><li style="list-style:disc;">
Channel：中转临时存储，保存所有Source组件信息</li><li style="list-style:disc;">
Sink：从Channel中读取，读取成功后会删除Channel中的信息<br></li></ul><div align="left">　　下图是Flume NG的架构图，如下所示：</div>
<div align="left"><img id="aimg_1301" src="http://www.kekeyun.org/data/attachment/forum/201504/30/225257p7ysa9k181q3pcss.jpg" class="zoom" width="600" alt=""></div>
<div align="left">　　图中描述了，从外部系统（Web Server）中收集产生的日志，然后通过Flume的Agent的Source组件将数据发送到临时存储Channel组件，最后传递给Sink组件，Sink组件直接把数据存储到HDFS文件系统中。</div>
<span style="font-weight:700;">3.单点Flume NG搭建、运行</span>
<div align="left">　　我们在熟悉了Flume NG的架构后，我们先搭建一个单点Flume收集信息到HDFS集群中，由于资源有限，本次直接在之前的高可用Hadoop集群上搭建Flume。</div>
<div align="left">　　场景如下：在NNA节点上搭建一个Flume NG，将本地日志收集到HDFS集群。</div>
<span style="font-weight:700;">3.1基础软件</span>
<div align="left">　　在搭建Flume NG之前，我们需要准备必要的软件，具体下载地址如下所示：</div>
<ul><li style="list-style:disc;">
Flume　　<span style="color:#000000;"><a href="http://www.apache.org/dyn/closer.cgi/flume/1.5.2/apache-flume-1.5.2-bin.tar.gz" rel="nofollow" style="color:rgb(51,102,153);">《下载地址》</a></span><br></li></ul><div align="left">　　JDK由于之前在安装Hadoop集群时已经配置过，这里就不赘述了，若需要配置的同学，可参考《<span style="color:#000000;"><a href="http://www.cnblogs.com/smartloli/p/4298430.html" rel="nofollow" style="color:rgb(51,102,153);">配置高可用的Hadoop平台</a></span>》。</div>
<span style="font-weight:700;">3.2安装与配置</span>
<ul><li style="list-style:disc;">
安装<br></li></ul><div align="left">　　首先，我们解压flume安装包，命令如下所示：</div>
<div class="blockcode" style="overflow:hidden;color:rgb(102,102,102);border:1px solid rgb(204,204,204);">
<div id="code_V1M">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
[hadoop@nna ~]$ tar -zxvf apache-flume-1.5.2-bin.tar.gz</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br><ul><li style="list-style:disc;">
配置<br></li></ul><div align="left">　　环境变量配置内容如下所示：</div>
<div class="blockcode" style="overflow:hidden;color:rgb(102,102,102);border:1px solid rgb(204,204,204);">
<div id="code_Qh6">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
export FLUME_HOME=/home/hadoop/flume-1.5.2<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
export PATH=$PATH:$FLUME_HOME/bin</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br><div align="left">flume-conf.properties</div>
<div class="blockcode" style="overflow:hidden;color:rgb(102,102,102);border:1px solid rgb(204,204,204);">
<div id="code_gts">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
#agent1 name<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sources=source1<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sinks=sink1<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.channels=channel1<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
#Spooling Directory<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
#set source1<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sources.source1.type=spooldir<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sources.source1.spoolDir=/home/hadoop/dir/logdfs<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sources.source1.channels=channel1<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sources.source1.fileHeader = false<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sources.source1.interceptors = i1<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sources.source1.interceptors.i1.type = timestamp<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
#set sink1<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sinks.sink1.type=hdfs<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sinks.sink1.hdfs.path=/home/hdfs/flume/logdfs<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sinks.sink1.hdfs.fileType=DataStream<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sinks.sink1.hdfs.writeFormat=TEXT<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sinks.sink1.hdfs.rollInterval=1<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sinks.sink1.channel=channel1<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sinks.sink1.hdfs.filePrefix=%Y-%m-%d<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
#set channel1<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.channels.channel1.type=file<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.channels.channel1.checkpointDir=/home/hadoop/dir/logdfstmp/point<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.channels.channel1.dataDirs=/home/hadoop/dir/logdfstmp</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br><div align="left">　　flume-env.sh</div>
<span style="background-color:rgb(245,245,245);">JAVA_HOME=/usr/java/jdk1.<span>7</span></span><br><div align="left">　　注：配置中的目录若不存在，需提前创建。</div>
<span style="font-weight:700;">3.3启动</span>
<div align="left">　　启动命令如下所示：</div>
<span style="background-color:rgb(245,245,245);">flume-ng agent -n agent1 -c conf -f flume-conf.properties -Dflume.root.logger=DEBUG,console</span><br><div align="left">　　注：命令中的agent1表示配置文件中的Agent的Name，如配置文件中的agent1。flume-conf.properties表示配置文件所在配置，需填写准确的配置文件路径。</div>
<span style="font-weight:700;">3.4效果预览</span>
<div align="left"><img id="aimg_1302" src="http://www.kekeyun.org/data/attachment/forum/201504/30/225257p0m97n7nen5pz0uw.jpg" class="zoom" width="600" alt=""></div>
<div align="left"><img id="aimg_1303" src="http://www.kekeyun.org/data/attachment/forum/201504/30/225258q5f5duf85uuf3u5j.jpg" class="zoom" width="600" alt=""></div>
<div align="left"><img id="aimg_1304" src="http://www.kekeyun.org/data/attachment/forum/201504/30/225258zd6z66z5xzrm5xd6.jpg" class="zoom" width="600" alt=""></div>
<div align="left">　　之后，成功上传后本地目的会被标记完成。如下图所示：</div>
<div align="left"><img id="aimg_1305" src="http://www.kekeyun.org/data/attachment/forum/201504/30/225258sg669ar0mjrjj2m8.jpg" class="zoom" width="600" alt=""></div>
<span style="font-weight:700;">4.高可用Flume NG搭建</span>
<div align="left">　　在完成单点的Flume NG搭建后，下面我们搭建一个高可用的Flume NG集群，架构图如下所示：</div>
<div align="left"><img id="aimg_1306" src="http://www.kekeyun.org/data/attachment/forum/201504/30/225259ncttzzmuqkfh3tww.jpg" class="zoom" width="600" alt=""></div>
<div align="left">　　图中，我们可以看出，Flume的存储可以支持多种，这里只列举了HDFS和Kafka（如：存储最新的一周日志，并给Storm系统提供实时日志流）。</div>
<span style="font-weight:700;">4.1节点分配</span>
<div align="left">　　Flume的Agent和Collector分布如下表所示：</div>
<table cellspacing="0" class="t_table" style="border-collapse:collapse;border:1px solid rgb(227,237,245);table-layout:auto;"><tbody><tr><td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
名称</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
HOST</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
角色</td>
</tr><tr><td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
Agent1</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
10.211.55.14</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
Web Server</td>
</tr><tr><td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
Agent2</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
10.211.55.15</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
Web Server</td>
</tr><tr><td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
Agent3</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
10.211.55.16</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
Web Server</td>
</tr><tr><td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
Collector1</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
10.211.55.18</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
AgentMstr1</td>
</tr><tr><td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
Collector2</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
10.211.55.19</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
AgentMstr2</td>
</tr></tbody></table><br><div align="left">　　图中所示，Agent1，Agent2，Agent3数据分别流入到Collector1和Collector2，Flume NG本身提供了Failover机制，可以自动切换和恢复。在上图中，有3个产生日志服务器分布在不同的机房，要把所有的日志都收集到一个集群中存储。下面我们开发配置Flume NG集群</div>
<span style="font-weight:700;">4.2配置</span>
<div align="left">　　在下面单点Flume中，基本配置都完成了，我们只需要新添加两个配置文件，它们是flume-client.properties和flume-server.properties，其配置内容如下所示：</div>
<ul><li style="list-style:disc;">
flume-client.properties</li><li style="list-style:disc;">
<div class="blockcode" style="overflow:hidden;color:rgb(102,102,102);border:1px solid rgb(204,204,204);">
<div id="code_S0f">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
#agent1 name<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.channels = c1<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sources = r1<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sinks = k1 k2<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
#set gruop<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sinkgroups = g1 <br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
#set channel<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.channels.c1.type = memory<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.channels.c1.capacity = 1000<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.channels.c1.transactionCapacity = 100<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sources.r1.channels = c1<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sources.r1.type = exec<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sources.r1.command = tail -F /home/hadoop/dir/logdfs/test.log<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sources.r1.interceptors = i1 i2<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sources.r1.interceptors.i1.type = static<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sources.r1.interceptors.i1.key = Type<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sources.r1.interceptors.i1.value = LOGIN<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sources.r1.interceptors.i2.type = timestamp<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
# set sink1<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sinks.k1.channel = c1<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sinks.k1.type = avro<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sinks.k1.hostname = nna<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sinks.k1.port = 52020<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
# set sink2<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sinks.k2.channel = c1<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sinks.k2.type = avro<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sinks.k2.hostname = nns<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sinks.k2.port = 52020<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
#set sink group<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sinkgroups.g1.sinks = k1 k2<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
#set failover<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sinkgroups.g1.processor.type = failover<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sinkgroups.g1.processor.priority.k1 = 10<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sinkgroups.g1.processor.priority.k2 = 1<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent1.sinkgroups.g1.processor.maxpenalty = 10000</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br>
注：指定Collector的IP和Port。<br></li></ul><ul><li style="list-style:disc;">
flume-server.properties
<div class="blockcode" style="overflow:hidden;color:rgb(102,102,102);border:1px solid rgb(204,204,204);">
<div id="code_x3H">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
#set Agent name<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
a1.sources = r1<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
a1.channels = c1<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
a1.sinks = k1<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
#set channel<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
a1.channels.c1.type = memory<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
a1.channels.c1.capacity = 1000<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
a1.channels.c1.transactionCapacity = 100<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
# other node,nna to nns<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
a1.sources.r1.type = avro<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
a1.sources.r1.bind = nna<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
a1.sources.r1.port = 52020<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
a1.sources.r1.interceptors = i1<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
a1.sources.r1.interceptors.i1.type = static<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
a1.sources.r1.interceptors.i1.key = Collector<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
a1.sources.r1.interceptors.i1.value = NNA<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
a1.sources.r1.channels = c1<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
#set sink to hdfs<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
a1.sinks.k1.type=hdfs<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
a1.sinks.k1.hdfs.path=/home/hdfs/flume/logdfs<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
a1.sinks.k1.hdfs.fileType=DataStream<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
a1.sinks.k1.hdfs.writeFormat=TEXT<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
a1.sinks.k1.hdfs.rollInterval=1<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
a1.sinks.k1.channel=c1<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
a1.sinks.k1.hdfs.filePrefix=%Y-%m-%d</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br></li></ul><div align="left">　　注：在另一台Collector节点上修改IP，如在NNS节点将绑定的对象有nna修改为nns。</div>
<span style="font-weight:700;">4.3启动</span>
<div align="left">　　在Agent节点上启动命令如下所示：</div>
<div class="blockcode" style="overflow:hidden;color:rgb(102,102,102);border:1px solid rgb(204,204,204);">
<div id="code_gBz">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
flume-ng agent -n agent1 -c conf -f flume-client.properties -Dflume.root.logger=DEBUG,console</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br><div align="left">注：命令中的agent1表示配置文件中的Agent的Name，如配置文件中的agent1。flume-client.properties表示配置文件所在配置，需填写准确的配置文件路径。</div>
<div align="left">　　在Collector节点上启动命令如下所示：</div>
<div class="blockcode" style="overflow:hidden;color:rgb(102,102,102);border:1px solid rgb(204,204,204);">
<div id="code_mJu">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
flume-ng agent -n a1 -c conf -f flume-server.properties -Dflume.root.logger=DEBUG,console</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br><div align="left">注：命令中的a1表示配置文件中的Agent的Name，如配置文件中的a1。flume-server.properties表示配置文件所在配置，需填写准确的配置文件路径。</div>
<span style="font-weight:700;">5.Failover测试</span>
<div align="left">　　下面我们来测试下Flume NG集群的高可用（故障转移）。场景如下：我们在Agent1节点上传文件，由于我们配置Collector1的权重比Collector2大，所以Collector1优先采集并上传到存储系统。然后我们kill掉Collector1，此时有Collector2负责日志的采集上传工作，之后，我们手动恢复Collector1节点的Flume服务，再次在Agent1上次文件，发现Collector1恢复优先级别的采集工作。具体截图如下所示：</div>
<ul><li style="list-style:disc;">
Collector1优先上传<br></li></ul><div align="left"><img id="aimg_1307" src="http://www.kekeyun.org/data/attachment/forum/201504/30/225259ck22fq2sd275crfs.jpg" class="zoom" width="600" alt=""></div>
<ul><li style="list-style:disc;">
HDFS集群中上传的log内容预览<br></li></ul><div align="left"><img id="aimg_1308" src="http://www.kekeyun.org/data/attachment/forum/201504/30/225259v26aiuuw24qwqzkw.jpg" class="zoom" width="600" alt=""></div>
<ul><li style="list-style:disc;">
Collector1宕机，Collector2获取优先上传权限<br></li></ul><div align="left"><img id="aimg_1309" src="http://www.kekeyun.org/data/attachment/forum/201504/30/225300hk0lhe07hueuaidl.jpg" class="zoom" width="600" alt=""></div>
<ul><li style="list-style:disc;">
重启Collector1服务，Collector1重新获得优先上传的权限<br></li></ul><div align="left"><img id="aimg_1310" src="http://www.kekeyun.org/data/attachment/forum/201504/30/225300wyrjemjv7mwnw91f.jpg" class="zoom" width="600" alt=""></div>
<span style="font-weight:700;">6.截图预览</span>
<div align="left">　　下面为大家附上HDFS文件系统中的截图预览，如下图所示：</div>
<ul><li style="list-style:disc;">
HDFS文件系统中的文件预览<br></li></ul><div align="left"><img id="aimg_1311" src="http://www.kekeyun.org/data/attachment/forum/201504/30/225301pmobivgibt4o4ozt.jpg" class="zoom" width="600" alt=""></div>
<ul><li style="list-style:disc;">
上传的文件内容预览<br></li></ul><div align="left"><img id="aimg_1312" src="http://www.kekeyun.org/data/attachment/forum/201504/30/225301gi3bg3zumig0jy4z.jpg" class="zoom" width="600" alt=""></div>
<span style="font-weight:700;">7.总结</span>
<div align="left">　　在配置高可用的Flume NG时，需要注意一些事项。在Agent中需要绑定对应的Collector1和Collector2的IP和Port，另外，在配置Collector节点时，需要修改当前Flume节点的配置文件，Bind的IP（或HostName）为当前节点的IP（或HostName），最后，在启动的时候，指定配置文件中的Agent的Name和配置文件的路径，否则会出错。</div>
<span style="font-weight:700;">8.结束语</span>
<div align="left">　　这篇博客就和大家分享到这里，如果大家在研究学习的过程当中有什么问题，可以加群进行讨论或发送邮件给我，我会尽我所能为您解答，与君共勉！</div>
<br></td>
</tr></tbody></table>            </div>
                </div>