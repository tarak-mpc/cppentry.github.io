---
layout:     post
title:      flume-ng学习之spooling directory类型source配置深入及解析
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/Jiny_li/article/details/51769399				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div>﻿﻿</div>
<p><strong><span style="font-size:18px;">flume概述</span></strong></p>
<p><span style="font-size:14px;">Flume是一个分布式、可靠、高可用的海量日志聚合系统，支持在系统中定制各类数据发送方，用于收集数据；同时，Flume提供对数据的简单处理，并写到各种数据接收方的能力。<br>
Flume主要由3个重要的组件构成：Source、Sink、Channel。</span></p>
<p><span style="font-size:14px;">flume的核心是把数据从数据源收集过来，再送到目的地。为了保证输送一定成功，在送到目的地之前，会先缓存数据，待数据真正到达目的地后，删除自己缓存的数据。</span></p>
<p><span style="font-size:14px;">source可以接收外部源发送过来的数据。不同的source，可以接受不同的数据格式。比如有目录池(spooling directory)数据源，可以监控指定文件夹中的新文件变化，如果目录中有文件产生，就会立刻读取其内容。<br>
channel是一个存储地，接收source的输出，直到有sink消费掉channel中的数据。channel中的数据直到进入到下一个channel中或者进入终端才会被删除。当sink写入失败后，可以自动重启，不会造成数据丢失，因此很可靠。<br>
sink会消费channel中的数据，然后送给外部源或者其他source。如数据可以写入到HDFS或者HBase中。<br>
flume允许多个agent连在一起，形成前后相连的多级跳。</span></p>
<p><span style="font-size:14px;"><img alt="" src="https://img-blog.csdn.net/20160627170027264?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"></span></p>
<p></p>
<span style="font-size:14px;"></span>
<p><span style="font-family:'宋体';"></span></p>
<p><strong><span lang="en-us" style="font-family:Arial, 'sans-serif';font-size:13.5pt;" xml:lang="en-us">flume</span><span style="font-family:'宋体';font-size:13.5pt;">配置</span></strong></p>
<p><span style="font-family:'宋体';"></span></p>
<p><span style="font-size:14px;"><span style="font-family:'宋体';">使用</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">flume</span><span style="font-family:'宋体';">的核心是如何配置</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">agent</span><span style="font-family:'宋体';">文件。</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">agent</span><span style="font-family:'宋体';">的配置是一个普通文本文件，使用键值对形式存储配置信息，可以设置多个</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">agent</span><span style="font-family:'宋体';">信息。配置的内容包括</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">source</span><span style="font-family:'宋体';">、</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">channel</span><span style="font-family:'宋体';">、</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">sink</span><span style="font-family:'宋体';">等。组件</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">source</span><span style="font-family:'宋体';">、</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">channel</span><span style="font-family:'宋体';">、</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">sink</span><span style="font-family:'宋体';">都有名称、类型和很多个性化的属性配置。</span></span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us"><br></span><span style="font-family:'宋体';font-size:14px;">配置文件应该这么写</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us"><br><span style="font-size:14px;"># list the sources, sinks and channels for the agent<br>
&lt;Agent&gt;.sources = &lt;Source&gt;<br>
&lt;Agent&gt;.sinks = &lt;Sink&gt;<br>
&lt;Agent&gt;.channels = &lt;Channel1&gt; &lt;Channel2&gt;<br>
# set channel for source<br>
&lt;Agent&gt;.sources.&lt;Source&gt;.channels = &lt;Channel1&gt;&lt;Channel2&gt; ...<br>
# set channel for sink<br>
&lt;Agent&gt;.sinks.&lt;Sink&gt;.channel = &lt;Channel1&gt;<br>
# properties for sources<br>
&lt;Agent&gt;.sources.&lt;Source&gt;.&lt;someProperty&gt; = &lt;someValue&gt;<br>
# properties for channels<br>
&lt;Agent&gt;.channel.&lt;Channel&gt;.&lt;someProperty&gt; = &lt;someValue&gt;<br>
# properties for sinks<br>
&lt;Agent&gt;.sources.&lt;Sink&gt;.&lt;someProperty&gt; = &lt;someValue&gt;</span></span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p><span style="font-family:'宋体';font-size:14px;">示例：</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p><span style="font-size:14px;"><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">Spooling DirectorySource</span><span style="font-family:'宋体';">为例</span></span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p><span style="font-size:14px;"><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">              SpoolSource:</span><span style="font-family:'宋体';">是监测配置的目录下新增的文件，并将文件中的数据读取出来。需要注意两点：</span><span style="font-family:Arial, 'sans-serif';"></span></span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p><span style="font-size:14px;"><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">1)</span><span style="font-family:'宋体';">拷贝到</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">spool</span><span style="font-family:'宋体';">目录下的文件不可以再打开编辑。</span></span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p><span style="font-size:14px;"><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">2) spool</span><span style="font-family:'宋体';">目录下不可包含相应的子目录</span></span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p><span lang="en-us" style="font-family:Arial, 'sans-serif';font-size:14px;" xml:lang="en-us"> </span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p><span lang="en-us" style="font-family:Arial, 'sans-serif';font-size:14px;" xml:lang="en-us">agent1.sources =src1<br>
agent1.sinks = sink1 sink2<br>
agent1.channels = ch1 ch2</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p><span style="font-size:14px;"><span style="font-family:'宋体';">说明：</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">agent1</span><span style="font-family:'宋体';">是代理名称，对应有</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">source</span><span style="font-family:'宋体';">，名称是</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">src1,</span><span style="font-family:'宋体';">；有两个</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">sink</span><span style="font-family:'宋体';">，名称是</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">sink1</span><span style="font-family:'宋体';">和</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">sink2</span><span style="font-family:'宋体';">；有两个</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">channel</span><span style="font-family:'宋体';">，名称是</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">ch1</span><span style="font-family:'宋体';">和</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">ch2</span></span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p><span lang="en-us" style="font-family:Arial, 'sans-serif';font-size:14px;" xml:lang="en-us"> </span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p><span lang="en-us" style="font-family:Arial, 'sans-serif';font-size:14px;" xml:lang="en-us">agent1.sources.src1.type= spooldir</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p><span style="font-size:14px;"><span style="font-family:'宋体';">说明：</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">spooldir</span><span style="font-family:'宋体';">指源的类型，</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">flume</span><span style="font-family:'宋体';">工程根据此字符串走入监控目录相关代码，进而启动线程（代码逻辑在</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">flume-ng-core</span><span style="font-family:'宋体';">工程</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">SpoolDirectorySource.java</span><span style="font-family:'宋体';">）</span></span></p>
<p><span style="font-family:'宋体';"></span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us"><br><span style="font-size:14px;">agent1.sources.src1.spoolDir = /root/data/logs</span></span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p><span style="font-size:14px;"><span style="font-family:'宋体';">说明：所监控的目录</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">/root/data/logs</span><span style="font-family:'宋体';">，目前只支持监控一个目录，如有业务中需监控两个或以上的目录，可修改</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">ReliableSpoolingFileEventReader.java</span><span style="font-family:'宋体';">中私有构造器中相关代码或者配置两个或以上相同的源。</span></span></p>
<p><span style="font-family:'宋体';"></span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us"><br><span style="font-size:14px;">agent1.sources.src1.ignorePattern = .*\\.txt  </span>
</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p><span style="font-size:14px;"><span style="font-family:'宋体';">说明：该配置项默认是（</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">^$</span><span style="font-family:'宋体';">），官方解释：</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">Regular
 expressionspecifying which files to ignore (skip)</span><span style="font-family:'宋体';">。如果配置就是过滤的文件，示例中就是所监控的目录下以</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">.txt</span><span style="font-family:'宋体';">结尾的文件不进行处理，配置按照</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">Java</span><span style="font-family:'宋体';">中正则表达式进行配置，代码中是调用</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">Pattern.<em>compile</em>(ignorePattern)</span><span style="font-family:'宋体';">进行解析（</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">ReliableSpoolingFileEventReader|line174</span><span style="font-family:'宋体';">），解析后在</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">ReliableSpoolingFileEventReader.getNextFile()</span><span style="font-family:'宋体';">方法中与读取的文件名通过</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">ignorePattern.matcher(fileName).matches()</span><span style="font-family:'宋体';">匹配，如果匹配到返回</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">false</span><span style="font-family:'宋体';">，就过滤不处理。</span><span style="font-family:Arial, 'sans-serif';"></span><span style="font-family:'宋体';">注意：往监控目录中写文件时不能写以点（</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">.</span><span style="font-family:'宋体';">）开头的文件和自定义结尾名的文件。</span></span></p>
<p><span style="font-family:'宋体';"></span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us"><br><span style="font-size:14px;">agent1.sources.src1.deletePolicy = immediate</span></span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p><span style="font-size:14px;"><span style="font-family:'宋体';">说明：处理后文件的删除策略，默认是</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">never</span><span style="font-family:'宋体';">，不删除，处理后将文件重命名，默认在文件名追加</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">.COMPLETED</span><span style="font-family:'宋体';">（可自定义配置），配置</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">immediate</span><span style="font-family:'宋体';">就是立即删除。源代码中有延时（</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">delay</span><span style="font-family:'宋体';">）删除策略，不过目前没有实现，来了句（</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">implementdelay
 in the future</span><span style="font-family:'宋体';">）</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">ReliableSpoolingFileEventReader|line334</span><span style="font-family:'宋体';">。</span></span></p>
<p><span style="font-family:'宋体';"></span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us"><br><span style="font-size:14px;">agent1.sources.src1.fileSuffix=.COMPLETED</span></span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p><span style="font-size:14px;"><span style="font-family:'宋体';">说明：读取文件后默认给处理后文件追加的后缀名，默认的是（</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">.COMPLETED</span><span style="font-family:'宋体';">），和</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">deletePolicy=
 never</span><span style="font-family:'宋体';">配合使用</span></span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p><span lang="en-us" style="font-family:Arial, 'sans-serif';font-size:14px;" xml:lang="en-us">agent1.sources.src1.batchSize= 100</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p><span style="font-size:14px;"><span style="font-family:'宋体';">说明：批量读取文件的大小，不配置时默认是</span><span lang="en-us" style="font-family:Arial, 'sans-serif';" xml:lang="en-us">100</span></span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p><span lang="en-us" style="font-family:Arial, 'sans-serif';font-size:14px;" xml:lang="en-us">agent1.sources.src1.channels= ch1</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p><span lang="en-us" style="font-family:Arial, 'sans-serif';font-size:14px;" xml:lang="en-us">agent1.sources.src1.channels= ch2</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p><span lang="en-us" style="font-family:Arial, 'sans-serif';font-size:14px;" xml:lang="en-us"> </span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><span style="font-family:'Times New Roman', serif;font-size:12pt;">#</span><span style="font-family:'宋体';font-size:12pt;">配置</span><span style="font-family:'Times New Roman', serif;font-size:12pt;">memorychannel</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><span style="font-family:'Times New Roman', serif;font-size:12pt;">agent1.channels.ch1.type = memory</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><span style="font-family:'Times New Roman', serif;font-size:12pt;">agent1.channels.ch1.capacity = 10000</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><span style="font-family:'宋体';font-size:12pt;">说明：</span><span style="font-family:'Times New Roman', serif;font-size:12pt;">capacity</span><span style="font-family:'宋体';font-size:12pt;">属性指通道容量</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><span style="font-family:'Times New Roman', serif;font-size:12pt;">agent1.channels.ch1.transactionCapacity = 1000</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><span style="font-family:'宋体';font-size:12pt;">说明：</span><span style="font-family:'Times New Roman', serif;font-size:12pt;">transactionCapacity</span><span style="font-family:'宋体';font-size:12pt;">属性指通道传输能力</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><span style="font-family:'Times New Roman', serif;font-size:12pt;">agent1.channels.ch1.byteCapacityBufferPercentage = 20</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><span style="font-family:'Times New Roman', serif;font-size:12pt;">agent1.channels.ch1.byteCapacity = 800000</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><span style="font-family:'Times New Roman', serif;font-size:12pt;">#</span><span style="font-family:'宋体';font-size:12pt;">配置</span><span style="font-family:'Times New Roman', serif;font-size:12pt;">filechannel</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><span style="font-family:'Times New Roman', serif;font-size:12pt;">agent1.channels.ch2.type = file</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><span style="font-family:'Times New Roman', serif;font-size:12pt;">agent1.channels.ch2.checkpointDir =/root/flumechannel/checkpoint</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><span style="font-family:'Times New Roman', serif;font-size:12pt;">agent1.channels.ch2.dataDirs =/root/flumechannel/data</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><span style="font-family:'Times New Roman', serif;font-size:12pt;">#</span><span style="font-family:'宋体';font-size:12pt;">配置</span><span style="font-family:'Times New Roman', serif;font-size:12pt;">hdfssink</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><span style="font-family:'Times New Roman', serif;font-size:12pt;">agent1.sinks.sink1.type = hdfs</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><span style="font-family:'Times New Roman', serif;font-size:12pt;">agent1.sinks.sink1.channel = ch2</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><span style="font-family:'Times New Roman', serif;font-size:12pt;">agent1.sinks.sink1.hdfs.path =hdfs://hadoop0:9000/flume/%Y-%m-%d/</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><span style="font-family:'Times New Roman', serif;font-size:12pt;">agent1.sinks.sink1.hdfs.rollInterval=1</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><span style="font-family:'Times New Roman', serif;font-size:12pt;">agent1.sinks.sink1.hdfs.fileType = DataStream</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><span style="font-family:'Times New Roman', serif;font-size:12pt;">agent1.sinks.sink1.hdfs.writeFormat = Text</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><span style="font-family:'Times New Roman', serif;font-size:12pt;">#</span><span style="font-family:'宋体';font-size:12pt;">配置</span><span style="font-family:'Times New Roman', serif;font-size:12pt;">hbasesink</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><span style="font-family:'Times New Roman', serif;font-size:12pt;">#</span><span style="font-family:'宋体';font-size:12pt;">配置</span><span style="font-family:'Times New Roman', serif;font-size:12pt;">hbasesink2</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><span style="font-family:'Times New Roman', serif;font-size:12pt;">agent1.sinks.sink2.type = hbase</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><span style="font-family:'Times New Roman', serif;font-size:12pt;">agent1.sinks.sink2.channel = channel1</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><span style="font-family:'Times New Roman', serif;font-size:12pt;">agent1.sinks.sink2.table = hmbbs</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><span style="font-family:'Times New Roman', serif;font-size:12pt;">agent1.sinks.sink2.columnFamily = cf</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><span style="font-family:'Times New Roman', serif;font-size:12pt;">agent1.sinks.sink2.serializer =flume.HmbbsHbaseEventSerializer</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><span style="font-family:'Times New Roman', serif;font-size:12pt;">agent1.sinks.sink2.serializer.suffix = timestamp</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><span style="font-family:'Times New Roman', serif;font-size:12pt;">agent1.sinks.sink2.serializer =org.apache.flume.sink.hbase.SimpleHbaseEventSerializer</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p><span style="font-family:Arial, 'sans-serif';font-size:14px;"> </span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><strong><span style="font-family:'宋体';font-size:12pt;">启动代理的脚本</span></strong></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><span style="font-family:'宋体';font-size:12pt;">启动命令是</span><span style="font-family:'Times New Roman', serif;font-size:12pt;">flume-ng</span><span style="font-family:'宋体';font-size:12pt;">，命令在</span><span style="font-family:'Times New Roman', serif;font-size:12pt;">flume</span><span style="font-family:'宋体';font-size:12pt;">工程的</span><span style="font-family:'Times New Roman', serif;font-size:12pt;">bin</span><span style="font-family:'宋体';font-size:12pt;">目录下</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><span style="font-family:'Times New Roman', serif;font-size:12pt;">-n</span><span style="font-family:'宋体';font-size:12pt;">指定</span><span style="font-family:'Times New Roman', serif;font-size:12pt;">agent</span><span style="font-family:'宋体';font-size:12pt;">名称</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><span style="font-family:'Times New Roman', serif;font-size:12pt;">-c</span><span style="font-family:'宋体';font-size:12pt;">指定配置文件目录</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><span style="font-family:'Times New Roman', serif;font-size:12pt;">-f</span><span style="font-family:'宋体';font-size:12pt;">指定配置文件</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><span style="font-family:'Times New Roman', serif;font-size:12pt;">-Dflume.root.logger=INFO,console</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><span style="font-family:'宋体';font-size:12pt;">因此完整命令：</span></p>
<p><span style="font-family:'宋体';font-size:14px;"></span></p>
<p align="left"><span style="font-family:'Times New Roman', serif;font-size:12pt;">bin/flume-ng agent -c conf -f conf/example.conf -n a1-Dflume.root.logger=INFO,console</span></p>
<p><span style="font-family:'宋体';"></span></p>
<p align="left"><span style="font-family:'Times New Roman', serif;font-size:12pt;"> 官方配置指南：</span></p>
<p><a href="http://flume.apache.org/FlumeUserGuide.html" rel="nofollow">http://flume.apache.org/FlumeUserGuide.html</a><br></p>
<p></p>
<div>kafka  <a href="http://www.cnblogs.com/likehua/p/3999538.html" rel="nofollow">
http://www.cnblogs.com/likehua/p/3999538.html</a>﻿﻿</div>
<div>﻿﻿</div>
<div><a href="http://blog.jobbole.com/75328/" rel="nofollow">http://blog.jobbole.com/75328/</a>﻿﻿</div>
<div>﻿﻿</div>
            </div>
                </div>