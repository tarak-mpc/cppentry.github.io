---
layout:     post
title:      flume学习一：flume基础知识
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u011066470/article/details/51535575				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:14px;">一．<span style="font-family:'Times New Roman';">Flume</span>使用的前提：</span></p>
<p><span style="font-size:14px;"> <span style="color:#333333;background:#FFFFFF;">Flume</span><span style="color:#333333;background:#FFFFFF;">使用</span><span style="color:#333333;background:#FFFFFF;"> java</span><span style="color:#333333;background:#FFFFFF;">编写，其需要运行在</span><span style="color:#333333;background:#FFFFFF;">
 Java1.6</span><span style="color:#333333;background:#FFFFFF;">或更高版本之上</span>。</span></p>
<p><span style="font-size:14px;">二．<span style="font-family:'Times New Roman';">Flume</span>的定义：</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"> Flume</span><span style="font-size:14px;">是一个分布式、可靠、高效可用的海量日志<span style="color:#FF0000;">采集、聚合和传输</span></span><span style="font-size:14px;">系统，支持在系统中</span><span style="color:#FF0000;"><span style="font-size:14px;">定制各类数据发送方，用于搜集数据</span></span><span style="font-size:14px;">；同时，</span><span style="font-family:'Times New Roman';font-size:14px;">flume</span><span style="font-size:14px;">提供<span style="color:#FF0000;">对数据进行加单处理</span></span><span style="font-size:14px;">，并</span><span style="color:#FF0000;"><span style="font-size:14px;">写到各种数据接受方</span></span><span style="font-size:14px;">（可定制）的能力。</span></p>
<p>核心一句话：<span style="color:#FF0000;background:#FFFFFF;">将数据从数据源收集过来，再送到目的地。为了保证输送一定成功，在送到目的地之前，会先缓冲数据，待数据真正到的目的地后，删除自己缓冲的数据。</span></p>
<p><span style="font-size:14px;">三．组件及作用：</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">1.source</span>：从<span style="font-family:'Times New Roman';">client</span>端收集数据，传递个<span style="font-family:'Times New Roman';">channel</span>。直接读取：文件<span style="font-family:'Times New Roman';">source</span>的有两种方式：</p>
<p><span style="font-family:'Times New Roman';font-size:14px;">   </span><span style="font-size:14px;">ExecuteSource:</span><span style="font-size:14px;">以运行<span style="font-family:'Times New Roman';">linux</span></span><span style="font-size:14px;">命令的方式，<span style="color:#FF0000;">持续的输出最新的数据</span></span><span style="font-size:14px;">，如</span><span style="font-family:'Times New Roman';font-size:14px;">tail
 –f </span>文件名指令，在这种方式下取的文件名必须是指定的。</p>
<p><span style="font-family:'Times New Roman';font-size:14px;">   </span><span style="font-size:14px;">SpoolSource</span><span style="font-size:14px;">：检测配置的目录<span style="color:#FF0000;">下新增的文件</span></span><span style="font-size:14px;">，</span><span style="color:#FF0000;"><span style="font-size:14px;">并将文件中的数据读取出</span></span><span style="font-size:14px;">来。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">2.channel</span>：缓冲数据，连接<span style="font-family:'Times New Roman';">sources</span>和<span style="font-family:'Times New Roman';">sinks</span>，有点像队列。类型有<span style="font-family:'Times New Roman';">MemoryChannel</span>，<span style="font-family:'Times New Roman';">JDBC
 Channel</span>，<span style="font-family:'Times New Roman';">FileChannel</span>等。</p>
<p><span style="font-family:'Times New Roman';font-size:14px;">3. sink</span>：从<span style="font-family:'Times New Roman';">channel</span>收集数据，运行一个独立线程。可以向文件系统、数据库、<span style="font-family:'Times New Roman';">hadoop</span>存储数据。</p>
<p><span style="font-family:'Times New Roman';font-size:14px;">   </span>在日志数据较少时，可以将数据存储在文件系统中，并且设定一定的时间间隔保存数据。</p>
<p><span style="font-size:14px;">  在日志数据较多时，可以将相应的日志存储到<span style="font-family:'Times New Roman';">hadoop</span>中，便于日后进行相应的数据分析。</span></p>
<p align="left"><span style="color:#333333;background:rgb(254,254,254);">组件名称</span><span style="color:#333333;background:rgb(254,254,254);">功能介绍</span></p>
<p> </p>
<table width="605" border="1" cellpadding="0" style="background:rgb(254,254,254);"><tbody><tr><td>
<p align="left"><span style="color:#333333;"><span style="font-size:14px;"> </span></span></p>
</td>
</tr><tr><td valign="top">
<p align="left"><span style="font-size:14px;"><span style="color:#333333;">Agent</span><span style="color:#333333;">代理</span></span></p>
</td>
<td valign="top">
<p align="left"><span style="font-size:14px;"><span style="color:#333333;">使用</span><span style="color:#333333;">JVM</span><span style="color:#333333;">运行</span><span style="color:#333333;">Flume</span><span style="color:#333333;">。每台机器运行一个</span><span style="color:#333333;">agent</span><span style="color:#333333;">，但是可以在一个</span><span style="color:#333333;">agent</span><span style="color:#333333;">中包含多个</span><span style="color:#333333;">sources</span><span style="color:#333333;">和</span><span style="color:#333333;">sinks</span><span style="color:#333333;">。</span></span></p>
</td>
</tr><tr><td valign="top">
<p align="left"><span style="font-size:14px;"><span style="color:#333333;">Client</span><span style="color:#333333;">客户端</span></span></p>
</td>
<td valign="top">
<p align="left"><span style="font-size:14px;"><span style="color:#333333;">生产数据，运行在一个独立的线程。</span></span></p>
</td>
</tr><tr><td valign="top">
<p align="left"><span style="font-size:14px;"><span style="color:#333333;">Source</span><span style="color:#333333;">源</span></span></p>
</td>
<td valign="top">
<p align="left"><span style="font-size:14px;"><span style="color:#333333;">从</span><span style="color:#333333;">Client</span><span style="color:#333333;">收集数据，传递给</span><span style="color:#333333;">Channel</span><span style="color:#333333;">。</span></span></p>
</td>
</tr><tr><td valign="top">
<p align="left"><span style="font-size:14px;"><span style="color:#333333;">Sink</span><span style="color:#333333;">接收器</span></span></p>
</td>
<td valign="top">
<p align="left"><span style="font-size:14px;"><span style="color:#333333;">从</span><span style="color:#333333;">Channel</span><span style="color:#333333;">收集数据，进行相关操作，运行在一个独立线程。</span></span></p>
</td>
</tr><tr><td valign="top">
<p align="left"><span style="font-size:14px;"><span style="color:#333333;">Channel</span><span style="color:#333333;">通道</span></span></p>
</td>
<td valign="top">
<p align="left"><span style="font-size:14px;"><span style="color:#333333;">连接</span><span style="color:#333333;"> sources</span><span style="color:#333333;">和</span><span style="color:#333333;"> sinks</span><span style="color:#333333;">，这个有点像一个队列。</span></span></p>
</td>
</tr><tr><td valign="top">
<p align="left"><span style="font-size:14px;"><span style="color:#333333;">Events</span><span style="color:#333333;">事件</span></span></p>
</td>
<td valign="top">
<p align="left"><span style="font-size:14px;"><span style="color:#333333;">传输的基本数据负载。</span></span></p>
</td>
</tr></tbody></table><p> </p>
<p><span style="font-size:14px;">四．<span style="font-family:'Times New Roman';">Flume</span>的整体构成图</span></p>
<p><img alt="" src="https://img-blog.csdn.net/20160530094312837?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">     <span lang="en-us" style="font-family:Helvetica;color:#333333;font-size:12pt;" xml:lang="en-us">
</span></span></p>
<p align="left" style="background:rgb(254,254,254);"><span style="color:#FF0000;">源将事件写到一个多或者多个通道中</span><span style="color:#333333;">。</span></p>
<p align="left" style="background:rgb(254,254,254);"><span style="color:#FF0000;">接收器只从一个通道接收事件</span><span style="color:#333333;">。</span></p>
<p align="left" style="background:rgb(254,254,254);"><span style="color:#FF0000;">代理可能会有多个源、通道与接收器</span></p>
<p><span style="font-size:14px;"><span style="color:#FF0000;background:#FFFFFF;"><span style="font-family:'Times New Roman';">Event</span></span><span style="color:#FF0000;background:#FFFFFF;">是</span><span style="color:#FF0000;background:#FFFFFF;"><span style="font-family:'Times New Roman';">flume</span></span><span style="color:#FF0000;background:#FFFFFF;">数据传输的基本单元</span><span style="color:#333333;background:#FFFFFF;">，</span><span style="color:#333333;background:#FFFFFF;"><span style="font-family:'Times New Roman';">flume</span></span><span style="color:#333333;background:#FFFFFF;">以</span><span style="color:#FF0000;background:#FFFFFF;">事件的形式</span><span style="color:#333333;background:#FFFFFF;">将数据从源头传送到目的地。</span></span></p>
<p><span style="font-size:14px;"><span style="color:#333333;background:#FFFFFF;">一个</span><span style="font-family:'Times New Roman';"><span style="color:#333333;background:#FFFFFF;"> </span><em><span style="color:#333333;">Event </span></em></span><span style="color:#333333;background:#FFFFFF;">由</span><span style="font-family:'Times New Roman';"><span style="color:#333333;background:#FFFFFF;"> </span><em><span style="color:#333333;">Map&lt;String,String&gt;Headers</span></em><span style="color:#333333;background:#FFFFFF;"> </span></span><span style="color:#333333;background:#FFFFFF;">和</span><span style="font-family:'Times New Roman';"><span style="color:#333333;background:#FFFFFF;"> </span><em><span style="color:#333333;">byte[]
 body</span></em><span style="color:#333333;background:#FFFFFF;"> </span></span><span style="color:#333333;background:#FFFFFF;">组成，其中</span><span style="font-family:'Times New Roman';"><span style="color:#333333;background:#FFFFFF;"> </span><em><span style="color:#333333;">Headers</span></em><span style="color:#333333;background:#FFFFFF;"> </span></span><span style="color:#333333;background:#FFFFFF;">保存了</span><span style="font-family:'Times New Roman';"><span style="color:#333333;background:#FFFFFF;"> </span><em><span style="color:#333333;">Event </span></em></span><span style="color:#333333;background:#FFFFFF;">的属性，</span><span style="font-family:'Times New Roman';"><em><span style="color:#333333;">body</span></em><span style="color:#333333;background:#FFFFFF;"> </span></span><span style="color:#333333;background:#FFFFFF;">保存了</span><span style="font-family:'Times New Roman';"><span style="color:#333333;background:#FFFFFF;"> </span><em><span style="color:#333333;">Event </span></em></span><span style="color:#333333;background:#FFFFFF;">的内容。</span></span></p>
<p><span style="font-size:14px;"><span style="color:#333333;background:#FFFFFF;">。</span></span></p>
<p><span style="font-size:14px;"><span style="color:#333333;background:#FFFFFF;"><span style="font-family:'Times New Roman';">Source:</span></span><span style="color:#333333;background:#FFFFFF;">负责接收</span><span style="color:#333333;background:#FFFFFF;"><span style="font-family:'Times New Roman';">events</span></span><span style="color:#333333;background:#FFFFFF;">或通过特殊机制产生</span><span style="color:#333333;background:#FFFFFF;"><span style="font-family:'Times New Roman';">events</span></span><span style="color:#333333;background:#FFFFFF;">，并将</span><span style="color:#333333;background:#FFFFFF;"><span style="font-family:'Times New Roman';">events</span></span><span style="color:#333333;background:#FFFFFF;">批量的放到一个或多个</span><span style="color:#333333;background:#FFFFFF;"><span style="font-family:'Times New Roman';">channels</span></span><span style="color:#333333;background:#FFFFFF;">。</span></span></p>
<p><span style="font-size:14px;"><span style="color:#333333;background:#FFFFFF;"><span style="font-family:'Times New Roman';">Channel</span></span><span style="color:#333333;background:#FFFFFF;">位于</span><span style="color:#333333;background:#FFFFFF;"><span style="font-family:'Times New Roman';">sources</span></span><span style="color:#333333;background:#FFFFFF;">和</span><span style="color:#333333;background:#FFFFFF;"><span style="font-family:'Times New Roman';">sinks</span></span><span style="color:#333333;background:#FFFFFF;">之间，用于缓存进来的</span><span style="color:#333333;background:#FFFFFF;"><span style="font-family:'Times New Roman';">events</span></span><span style="color:#333333;background:#FFFFFF;">，当</span><span style="color:#333333;background:#FFFFFF;"><span style="font-family:'Times New Roman';">sink</span></span><span style="color:#333333;background:#FFFFFF;">成功的将</span><span style="color:#333333;background:#FFFFFF;"><span style="font-family:'Times New Roman';">events</span></span><span style="color:#333333;background:#FFFFFF;">发送到下一跳的</span><span style="color:#333333;background:#FFFFFF;"><span style="font-family:'Times New Roman';">channel</span></span><span style="color:#333333;background:#FFFFFF;">或最终目的，</span><span style="color:#333333;background:#FFFFFF;"><span style="font-family:'Times New Roman';">events</span></span><span style="color:#333333;background:#FFFFFF;">从</span><span style="color:#333333;background:#FFFFFF;"><span style="font-family:'Times New Roman';">channel</span></span><span style="color:#333333;background:#FFFFFF;">移除。</span></span></p>
<p><span style="font-size:14px;"><span style="color:#333333;background:#FFFFFF;"><span style="font-family:'Times New Roman';">Sink</span></span><span style="color:#333333;background:#FFFFFF;">：负责将</span><span style="color:#333333;background:#FFFFFF;"><span style="font-family:'Times New Roman';">events</span></span><span style="color:#333333;background:#FFFFFF;">传输到吓一跳或最终目的，成功完成后将</span><span style="color:#333333;background:#FFFFFF;"><span style="font-family:'Times New Roman';">events</span></span><span style="color:#333333;background:#FFFFFF;">从</span><span style="color:#333333;background:#FFFFFF;"><span style="font-family:'Times New Roman';">channel</span></span><span style="color:#333333;background:#FFFFFF;">移除。</span></span></p>
<p><span style="font-size:14px;">五．常见错误：</span></p>
<p style="background:#FFFFFF;"><span style="color:#333333;background:#FFFFFF;">   </span>
Could not configure sink  sink1 due to:No channel configured for sink: sink1<br>
org.apache.flume.conf.ConfigurationException: No channel configured for sink:sink1</p>
<p style="background:#FFFFFF;"><span style="font-family:'宋体';"><span style="color:#333333;background:#FFFFFF;">则仔细查看配置文件，</span><span style="color:#FF0000;background:#FFFFFF;">一个</span></span><span style="color:#FF0000;background:#FFFFFF;">source</span><span style="color:#FF0000;background:#FFFFFF;"><span style="font-family:'宋体';">可以对应多个</span></span><span style="color:#FF0000;background:#FFFFFF;">channel</span><span style="color:#333333;background:#FFFFFF;"><span style="font-family:'宋体';">，所以是</span></span><span style="color:#FF0000;background:#FFFFFF;">channels</span><span style="color:#333333;background:#FFFFFF;"><span style="font-family:'宋体';">，</span></span><span style="color:#333333;background:#FFFFFF;">agent.</span><span style="color:#FF0000;background:#FFFFFF;">sources.source1.channels</span><span style="color:#333333;background:#FFFFFF;">
 = channel1</span><span style="font-family:'宋体';"><span style="color:#333333;background:#FFFFFF;">，</span></span></p>
<p style="background:#FFFFFF;"><span style="font-family:'宋体';"><span style="color:#333333;background:#FFFFFF;">但是</span><span style="color:#FF0000;background:#FFFFFF;">一个</span></span><span style="color:#FF0000;background:#FFFFFF;">sink</span><span style="color:#FF0000;background:#FFFFFF;"><span style="font-family:'宋体';">对应一个</span></span><span style="color:#FF0000;background:#FFFFFF;">channel</span><span style="color:#333333;background:#FFFFFF;"><span style="font-family:'宋体';">，所以是</span></span><span style="color:#333333;background:#FFFFFF;">channel</span><span style="color:#333333;background:#FFFFFF;"><span style="font-family:'宋体';">，</span></span><span style="color:#333333;background:#FFFFFF;">agent.sinks.</span><span style="color:#FF0000;background:#FFFFFF;">sink1.channel
 =channel1</span><span style="font-family:'宋体';"><span style="color:#333333;background:#FFFFFF;">，请注意。</span></span></p>
<p style="background:#FFFFFF;"><span style="font-family:'宋体';"><span style="color:#333333;background:#FFFFFF;">六．常见各组件支持的类型：</span></span></p>
<p align="left" style="background:#FFFFFF;"><span style="color:#333333;background:#FFFFFF;">  </span><span style="color:#333333;">Flume Source</span><span style="color:#333333;">支持的类型：</span></p>
<p style="background:#FFFFFF;"> </p>
<table border="1" cellspacing="0" cellpadding="0" style="background:#FFFFFF;"><tbody><tr><td valign="bottom" style="background:rgb(243,243,243);">
<p align="left"><span style="color:#444444;">Source</span><span style="color:#444444;">类型</span></p>
</td>
<td valign="bottom" style="background:rgb(243,243,243);">
<p align="left"><span style="color:#444444;">说明</span></p>
</td>
</tr><tr><td>
<p align="left"><span style="color:#444444;">Avro  Source</span></p>
</td>
<td>
<p align="left"><span style="color:#444444;">支持Avro</span>协议（实际上是Avro RPC），内置支持</p>
</td>
</tr><tr><td>
<p align="left"><span style="color:#444444;">Thrift  Source</span></p>
</td>
<td>
<p align="left"><span style="color:#444444;">支持Thrift</span>协议，内置支持</p>
</td>
</tr><tr><td>
<p align="left"><span style="color:#444444;"><span style="font-family:'宋体';font-size:14px;"> </span></span></p>
</td>
<td>
<p align="left"><span style="color:#444444;">Exec  Source</span></p>
</td>
</tr><tr><td>
<p align="left"><span style="color:#444444;">JMS  Source</span></p>
</td>
<td>
<p align="left"><span style="color:#444444;">从JMS</span>系统（消息、主题）中读取数据，ActiveMQ已经测试过</p>
</td>
</tr><tr><td>
<p align="left"><span style="color:#444444;">Spooling  Directory Source</span></p>
</td>
<td>
<p align="left"><span style="color:#444444;">监控指定目录内数据变更</span></p>
</td>
</tr><tr><td>
<p align="left"><span style="color:#444444;">Twitter  1% firehose Source</span></p>
</td>
<td>
<p align="left"><span style="color:#444444;">通过API</span>持续下载Twitter数据，试验性质</p>
</td>
</tr><tr><td>
<p align="left"><span style="color:#444444;">Netcat  Source</span></p>
</td>
<td>
<p align="left"><span style="color:#FF0000;">监控某个端口，将流经端口的每一个文本行数据作为Event</span>输入</p>
</td>
</tr><tr><td>
<p align="left"><span style="color:#444444;">Sequence  Generator Source</span></p>
</td>
<td>
<p align="left"><span style="color:#444444;">序列生成器数据源，生产序列数据</span></p>
</td>
</tr><tr><td>
<p align="left"><span style="color:#444444;">Syslog  Sources</span></p>
</td>
<td>
<p align="left"><span style="color:#444444;">读取syslog</span>数据，产生Event，支持UDP和TCP两种协议</p>
</td>
</tr><tr><td>
<p align="left"><span style="color:#444444;">HTTP  Source</span></p>
</td>
<td>
<p align="left"><span style="color:#444444;">基于HTTP POST</span>或GET方式的数据源，支持JSON、BLOB表示形式</p>
</td>
</tr><tr><td>
<p align="left"><span style="color:#444444;">Legacy  Sources</span></p>
</td>
<td>
<p align="left"><span style="color:#444444;">兼容老的Flume OG</span>中Source（0.9.x版本）</p>
</td>
</tr></tbody></table><p style="background:#FFFFFF;"> </p>
<p align="left" style="background:#FFFFFF;"><span style="color:#333333;">Flume Channel</span><span style="color:#333333;">支持的类型：</span></p>
<p style="background:#FFFFFF;"> </p>
<table border="1" cellspacing="0" cellpadding="0" style="background:#FFFFFF;"><tbody><tr><td valign="bottom" style="background:rgb(243,243,243);">
<p align="left"><span style="color:#444444;">Channel</span><span style="color:#444444;">类型</span></p>
</td>
<td valign="bottom" style="background:rgb(243,243,243);">
<p align="left"><span style="color:#444444;">说明</span></p>
</td>
</tr><tr><td>
<p align="left"><span style="color:#FF0000;">Memory  Channel</span></p>
</td>
<td>
<p align="left"><span style="color:#FF0000;">Event</span><span style="color:#FF0000;">数据存储在内存中</span></p>
</td>
</tr><tr><td>
<p align="left"><span style="color:#444444;">JDBC  Channel</span></p>
</td>
<td>
<p align="left"><span style="color:#444444;">Event</span><span style="color:#444444;">数据存储在持久化存储中，当前Flume Channel</span>内置支持Derby</p>
</td>
</tr><tr><td>
<p align="left"><span style="color:#444444;">File  Channel</span></p>
</td>
<td>
<p align="left"><span style="color:#444444;">Event</span><span style="color:#444444;">数据存储在磁盘文件中</span></p>
</td>
</tr><tr><td>
<p align="left"><span style="color:#444444;">Spillable  Memory Channel</span></p>
</td>
<td>
<p align="left"><span style="color:#444444;">Event</span><span style="color:#444444;">数据存储在内存中和磁盘上，当内存队列满了，会持久化到磁盘文件（当前试验性的，不建议生产环境使用）</span></p>
</td>
</tr><tr><td>
<p align="left"><span style="color:#444444;">Pseudo  Transaction Channel</span></p>
</td>
<td>
<p align="left"><span style="color:#444444;">测试用途</span></p>
</td>
</tr><tr><td>
<p align="left"><span style="color:#444444;">Custom  Channel</span></p>
</td>
<td>
<p align="left"><span style="color:#444444;">自定义Channel</span>实现</p>
</td>
</tr></tbody></table><p style="background:#FFFFFF;"> </p>
<p align="left" style="background:#FFFFFF;"><span style="color:#333333;">Flume Sink</span><span style="color:#333333;">支持的类型</span></p>
<p style="background:#FFFFFF;"> </p>
<table border="1" cellspacing="0" cellpadding="0" style="background:#FFFFFF;"><tbody><tr><td valign="bottom" style="background:rgb(243,243,243);">
<p align="left"><span style="color:#444444;">Sink</span><span style="color:#444444;">类型</span></p>
</td>
<td valign="bottom" style="background:rgb(243,243,243);">
<p align="left"><span style="color:#444444;">说明</span></p>
</td>
</tr><tr><td>
<p align="left"><span style="color:#444444;">HDFS  Sink</span></p>
</td>
<td>
<p align="left"><span style="color:#444444;">数据写入HDFS</span></p>
</td>
</tr><tr><td>
<p align="left"><span style="color:#FF0000;">Logger  Sink</span></p>
</td>
<td>
<p align="left"><span style="color:#FF0000;">数据写入日志文件</span></p>
</td>
</tr><tr><td>
<p align="left"><span style="color:#444444;">Avro  Sink</span></p>
</td>
<td>
<p align="left"><span style="color:#444444;">数据被转换成Avro Event</span>，然后发送到配置的RPC端口上</p>
</td>
</tr><tr><td>
<p align="left"><span style="color:#444444;">Thrift  Sink</span></p>
</td>
<td>
<p align="left"><span style="color:#444444;">数据被转换成Thrift Event</span>，然后发送到配置的RPC端口上</p>
</td>
</tr><tr><td>
<p align="left"><span style="color:#444444;">IRC  Sink</span></p>
</td>
<td>
<p align="left"><span style="color:#444444;">数据在IRC</span>上进行回放</p>
</td>
</tr><tr><td>
<p align="left"><span style="color:#444444;">File  Roll Sink</span></p>
</td>
<td>
<p align="left"><span style="color:#444444;">存储数据到本地文件系统</span></p>
</td>
</tr><tr><td>
<p align="left"><span style="color:#444444;">Null  Sink</span></p>
</td>
<td>
<p align="left"><span style="color:#444444;">丢弃到所有数据</span></p>
</td>
</tr><tr><td>
<p align="left"><span style="color:#444444;">HBase  Sink</span></p>
</td>
<td>
<p align="left"><span style="color:#444444;">数据写入HBase</span>数据库</p>
</td>
</tr><tr><td>
<p align="left"><span style="color:#444444;">Morphline  Solr Sink</span></p>
</td>
<td>
<p align="left"><span style="color:#444444;">数据发送到Solr</span>搜索服务器（集群）</p>
</td>
</tr><tr><td>
<p align="left"><span style="color:#444444;">ElasticSearch  Sink</span></p>
</td>
<td>
<p align="left"><span style="color:#444444;">数据发送到Elastic Search</span>搜索服务器（集群）</p>
</td>
</tr><tr><td>
<p align="left"><span style="color:#444444;">Kite  Dataset Sink</span></p>
</td>
<td>
<p align="left"><span style="color:#444444;">写数据到Kite Dataset</span>，试验性质的</p>
</td>
</tr><tr><td>
<p align="left"><span style="color:#444444;">Custom  Sink</span></p>
</td>
<td>
<p align="left"><span style="color:#444444;">自定义Sink</span>实现</p>
</td>
</tr></tbody></table><p style="background:#FFFFFF;"> </p>
<p style="background:#FFFFFF;"><span style="color:#333333;background:#FFFFFF;"><span style="font-family:'宋体';">七</span></span><span style="font-family:'宋体';"><span style="color:#333333;background:#FFFFFF;">各种组件的特性：</span></span></p>
<p align="left" style="background:rgb(254,254,254);"><span style="color:#333333;">(1)</span><span style="color:#333333;">可靠性</span></p>
<p align="left" style="background:rgb(254,254,254);"><span style="color:#333333;">当节点出现故障时，日志能够被传送到其他节点上而不会丢失。</span><span style="color:#333333;">Flume</span><span style="color:#333333;">提供了三种级别的可靠性保障，从强到弱依次分别为：</span><span style="color:#333333;">end-to-end</span><span style="color:#333333;">（收到数据</span><span style="color:#333333;">agent</span><span style="color:#333333;">首先将</span><span style="color:#333333;">event</span><span style="color:#333333;">写到磁盘上，当数据传送成功后，再删除；如果数据发送失败，可以重新发送。），</span><span style="color:#333333;">Store
 on failure</span><span style="color:#333333;">（这也是</span><span style="color:#333333;">scribe</span><span style="color:#333333;">采用的策略，当数据接收方</span><span style="color:#333333;">crash</span><span style="color:#333333;">时，将数据写到本地，待恢复后，继续发送），</span><span style="color:#333333;">Best
 effort</span><span style="color:#333333;">（数据发送到接收方后，不会进行确认）。</span></p>
<p align="left" style="background:rgb(254,254,254);"><span style="color:#333333;">(2)</span><span style="color:#333333;">可扩展性</span></p>
<p align="left" style="background:rgb(254,254,254);"><span style="color:#333333;">Flume</span><span style="color:#333333;">采用了三层架构，分别为</span><span style="color:#333333;">agent</span><span style="color:#333333;">，</span><span style="color:#333333;">collector</span><span style="color:#333333;">和</span><span style="color:#333333;">storage</span><span style="color:#333333;">，每一层均可以水平扩展。其中，所有</span><span style="color:#333333;">agent</span><span style="color:#333333;">和</span><span style="color:#333333;">collector</span><span style="color:#333333;">由</span><span style="color:#333333;">master</span><span style="color:#333333;">统一管理，这使得系统容易监控和维护，且</span><span style="color:#333333;">master</span><span style="color:#333333;">允许有多个（使用</span><span style="color:#333333;">ZooKeeper</span><span style="color:#333333;">进行管理和负载均衡），这就避免了单点故障问题。</span></p>
<p align="left" style="background:rgb(254,254,254);"><span style="color:#333333;">(3)</span><span style="color:#333333;">可管理性</span></p>
<p align="left" style="background:rgb(254,254,254);"><span style="color:#333333;">所有</span><span style="color:#333333;">agent</span><span style="color:#333333;">和</span><span style="color:#333333;">colletor</span><span style="color:#333333;">由</span><span style="color:#333333;">master</span><span style="color:#333333;">统一管理，这使得系统便于维护。多</span><span style="color:#333333;">master</span><span style="color:#333333;">情况，</span><span style="color:#333333;">Flume</span><span style="color:#333333;">利用</span><span style="color:#333333;">ZooKeeper</span><span style="color:#333333;">和</span><span style="color:#333333;">gossip</span><span style="color:#333333;">，保证动态配置数据的一致性。用户可以在</span><span style="color:#333333;">master</span><span style="color:#333333;">上查看各个数据源或者数据流执行情况，且可以对各个数据源配置和动态加载。</span><span style="color:#333333;">Flume</span><span style="color:#333333;">提供了</span><span style="color:#333333;">web</span><span style="color:#333333;">和</span><span style="color:#333333;">shell
 scriptcommand</span><span style="color:#333333;">两种形式对数据流进行管理。</span></p>
<p align="left" style="background:rgb(254,254,254);"><span style="color:#333333;">(4)</span><span style="color:#333333;">功能可扩展性</span></p>
<p align="left" style="background:rgb(254,254,254);"><span style="color:#333333;">用户可以根据需要添加自己的</span><span style="color:#333333;">agent</span><span style="color:#333333;">，</span><span style="color:#333333;">collector</span><span style="color:#333333;">或者</span><span style="color:#333333;">storage</span><span style="color:#333333;">。此外，</span><span style="color:#333333;">Flume</span><span style="color:#333333;">自带了很多组件，包括各种</span><span style="color:#333333;">agent</span><span style="color:#333333;">（</span><span style="color:#333333;">file</span><span style="color:#333333;">，</span><span style="color:#333333;">
 syslog</span><span style="color:#333333;">等），</span><span style="color:#333333;">collector</span><span style="color:#333333;">和</span><span style="color:#333333;">storage</span><span style="color:#333333;">（</span><span style="color:#333333;">file</span><span style="color:#333333;">，</span><span style="color:#333333;">HDFS</span><span style="color:#333333;">等）。</span></p>
<p style="background:#FFFFFF;"> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p><span style="font-size:14px;"></span> </p>
            </div>
                </div>