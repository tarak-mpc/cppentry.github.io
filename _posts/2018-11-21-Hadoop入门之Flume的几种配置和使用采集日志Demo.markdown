---
layout:     post
title:      Hadoop入门之Flume的几种配置和使用采集日志Demo
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/sqh201030412/article/details/78053219				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div>1.获取flume压缩包解压</div>
<div>   <span style="line-height:1.5;background-color:inherit;">wget </span><a href="http://archive.apache.org/dist/flume/1.6.0/apache-flume-1.6.0-bin.tar.gz" rel="nofollow" style="line-height:1.5;background-color:inherit;">http://archive.apache.org/dist/flume/1.6.0/apache-flume-1.6.0-bin.tar.gz</a></div>
<div>2.添加JAVA_HOME到flume配置文件flume_env.sh中</div>
<div>   <span style="line-height:1.5;background-color:inherit;"># Enviroment variables can be set here.</span></div>
<div>    export JAVA_HOME=/usr/local/jdk1.8.0_131</div>
<div>3.编写flume agent的配置文件启动flume服务.关键在于编写出自己需求的配置文件.</div>
<div>  下面是几个例子.</div>
<div>
<div style="background-color:inherit;">   官方文档 <a href="http://flume.apache.org/FlumeUserGuide.html" rel="nofollow" style="background-color:inherit;">http://flume.apache.org/FlumeUserGuide.html</a></div>
</div>
<div>   </div>
<div>   3.1 配置一个最简单的例子</div>
<div>3.1.1 在 flume/conf 添加新文件 </div>
<div> vim  netcat-logger.conf  配置如下:</div>
<div><br style="background-color:inherit;"></div>
<div> <span style="line-height:1.5;background-color:inherit;"># 定义这个agent中各组件的名字</span></div>
<div> a1.sources = r1</div>
<div> a1.sinks = k1</div>
<div> a1.channels = c1</div>
<div><br style="background-color:inherit;"></div>
<div>        # 描述和配置source组件：r1</div>
<div>a1.sources.r1.type = netcat</div>
<div>a1.sources.r1.bind = localhost</div>
<div>a1.sources.r1.port = 44444</div>
<div><br style="background-color:inherit;"></div>
<div># 描述和配置sink组件：k1</div>
<div>a1.sinks.k1.type = logger</div>
<div><br style="background-color:inherit;"></div>
<div># 描述和配置channel组件，此处使用是内存缓存的方式</div>
<div>a1.channels.c1.type = memory</div>
<div>a1.channels.c1.capacity = 1000</div>
<div>a1.channels.c1.transactionCapacity = 100</div>
<div><br style="background-color:inherit;"></div>
<div># 描述和配置source  channel   sink之间的连接关系</div>
<div>a1.sources.r1.channels = c1</div>
<div>a1.sinks.k1.channel = c1</div>
<div><br style="background-color:inherit;"></div>
<div>3.1.2 使用配置文件启动flume</div>
<div>  
<div style="background-color:inherit;"><span lang="en-us" style="font-family:Calibri, sans-serif;font-size:10.5pt;background-color:inherit;" xml:lang="en-us">   bin/flume-ng agent -c conf -f conf/netcat-logger.conf -n a1<span style="background-color:inherit;"> <span> </span></span>-Dflume.root.logger=INFO,console</span></div>
</div>
<div><br style="background-color:inherit;"></div>
<div>
<div style="background-color:inherit;">
<p style="background-color:inherit;"><span lang="en-us" style="background-color:inherit;" xml:lang="en-us">   -c conf<span style="background-color:inherit;">  <span> </span></span></span><span style="font-family:'宋体';background-color:inherit;">指定</span><span lang="en-us" style="background-color:inherit;" xml:lang="en-us">flume</span><span style="font-family:'宋体';background-color:inherit;">自身的配置文件所在目录</span></p>
<p style="background-color:inherit;"><span lang="en-us" style="background-color:inherit;" xml:lang="en-us">  -f conf/netcat-logger.con<span style="background-color:inherit;"> <span> </span></span></span><span style="font-family:'宋体';background-color:inherit;">指定我们所描述的采集方案</span></p>
<p style="background-color:inherit;"><span lang="en-us" style="background-color:inherit;" xml:lang="en-us">  -n a1<span style="background-color:inherit;"> <span> </span></span></span><span style="font-family:'宋体';background-color:inherit;">指定我们这个</span><span lang="en-us" style="background-color:inherit;" xml:lang="en-us">agent</span><span style="font-family:'宋体';background-color:inherit;">的名字</span></p>
</div>
<br style="background-color:inherit;"></div>
<div>3.1.3 验证是否能通过端口进行日志采集工作</div>
<div>     我配置的flume机器的内网域名为hadoop5,使用telnet进行连接测试</div>
<div><br style="background-color:inherit;"></div>
<div>
<div style="background-color:inherit;"><span lang="en-us" style="font-family:Calibri, sans-serif;font-size:10.5pt;background-color:inherit;" xml:lang="en-us">      telnet localhost 44444</span></div>
<div style="background-color:inherit;"><span lang="en-us" style="font-family:Calibri, sans-serif;font-size:10.5pt;background-color:inherit;" xml:lang="en-us"><img src="https://img-blog.csdn.net/20170921160915390?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3FoMjAxMDMwNDEy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></span></div>
<div style="background-color:inherit;"><span lang="en-us" style="font-family:Calibri, sans-serif;font-size:10.5pt;background-color:inherit;" xml:lang="en-us"><br></span></div>
<div style="background-color:inherit;"><span lang="en-us" style="font-family:Calibri, sans-serif;font-size:10.5pt;background-color:inherit;" xml:lang="en-us"><br></span></div>
<div style="background-color:inherit;"><span lang="en-us" style="font-family:Calibri, sans-serif;font-size:10.5pt;background-color:inherit;" xml:lang="en-us"><img src="https://img-blog.csdn.net/20170921160919540?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3FoMjAxMDMwNDEy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></span></div>
<div style="background-color:inherit;"><span lang="en-us" style="font-family:Calibri, sans-serif;font-size:10.5pt;background-color:inherit;" xml:lang="en-us"><br></span></div>
<div style="background-color:inherit;"><span lang="en-us" style="font-family:Calibri, sans-serif;font-size:10.5pt;background-color:inherit;" xml:lang="en-us"><br></span></div>
<div style="background-color:inherit;"><span lang="en-us" style="font-family:Calibri, sans-serif;font-size:10.5pt;background-color:inherit;" xml:lang="en-us"></span>
<div>3.2.1   <span style="font-family:'宋体';font-size:10.5pt;line-height:1.5;background-color:inherit;">服务器的某特定目录下，会不断产生新的文件，每当有新文件出现，就需要把文件采集到</span><span style="font-size:10.5pt;line-height:1.5;background-color:inherit;"><span style="font-family:Calibri, sans-serif;background-color:inherit;">logger</span></span></div>
<div><br style="background-color:inherit;"></div>
<div>
<div style="background-color:inherit;">      vim conf/spooldir-logger.conf</div>
</div>
<div>内容如下:</div>
<div><br style="background-color:inherit;"></div>
<div>
<div style="background-color:inherit;"># Name the components on this agent</div>
<div style="background-color:inherit;">a1.sources = r1</div>
<div style="background-color:inherit;">a1.sinks = k1</div>
<div style="background-color:inherit;">a1.channels = c1</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;"># Describe/configure the source</div>
<div style="background-color:inherit;">#监听目录,spoolDir指定目录, fileHeader要不要给文件夹前坠名</div>
<div style="background-color:inherit;">a1.sources.r1.type = spooldir</div>
<div style="background-color:inherit;">a1.sources.r1.spoolDir = /home/hadoop/flumespool</div>
<div style="background-color:inherit;">a1.sources.r1.fileHeader = true</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;"># Describe the sink</div>
<div style="background-color:inherit;">a1.sinks.k1.type = logger</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;"># Use a channel which buffers events in memory</div>
<div style="background-color:inherit;">a1.channels.c1.type = memory</div>
<div style="background-color:inherit;">a1.channels.c1.capacity = 1000</div>
<div style="background-color:inherit;">a1.channels.c1.transactionCapacity = 100</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;"># Bind the source and sink to the channel</div>
<div style="background-color:inherit;">a1.sources.r1.channels = c1</div>
<div style="background-color:inherit;">a1.sinks.k1.channel = c1</div>
</div>
<div><br style="background-color:inherit;"></div>
<div><br style="background-color:inherit;"></div>
<div>3.2.2 启动flume服务监控指定目录</div>
<div>
<div style="background-color:inherit;">bin/flume-ng agent -c ./conf -f ./conf/spooldir-logger.conf -n a1 -Dflume.root.logger=INFO,console</div>
</div>
<div><br style="background-color:inherit;"></div>
<div>3.2.3 移动一个文件到监控目录下</div>
<img src="https://img-blog.csdn.net/20170921160924640?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3FoMjAxMDMwNDEy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></div>
</div>
<p><span>效果</span><br></p>
<p><img src="https://img-blog.csdn.net/20170921160928790?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3FoMjAxMDMwNDEy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p><br></p>
<p></p>
<div>3.3.1   <span style="line-height:1.5;background-color:inherit;">用tail命令获取数据，下沉到hdfs</span></div>
<div><span style="line-height:1.5;background-color:inherit;"><br style="background-color:inherit;"></span></div>
<div><span style="line-height:1.5;background-color:inherit;"></span>
<div style="color:rgb(0,0,0);font-family:'微软雅黑';font-size:14px;font-style:normal;font-weight:normal;background-color:rgb(255,255,255);">
<div style="background-color:inherit;">      vim conf/tail-hdfs.conf</div>
</div>
<div style="color:rgb(0,0,0);font-family:'微软雅黑';font-size:14px;font-style:normal;font-weight:normal;background-color:rgb(255,255,255);">
内容如下:</div>
<br style="background-color:inherit;"></div>
<div>
<div style="background-color:inherit;"># Name the components on this agent</div>
<div style="background-color:inherit;">a1.sources = r1</div>
<div style="background-color:inherit;">a1.sinks = k1</div>
<div style="background-color:inherit;">a1.channels = c1</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;"># Describe/configure the source</div>
<div style="background-color:inherit;">a1.sources.r1.type = exec</div>
<div style="background-color:inherit;">a1.sources.r1.command = tail -F /home/hadoop/log/test.log</div>
<div style="background-color:inherit;">a1.sources.r1.channels = c1</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;"># Describe the sink</div>
<div style="background-color:inherit;">a1.sinks.k1.type = hdfs</div>
<div style="background-color:inherit;">a1.sinks.k1.channel = c1</div>
<div style="background-color:inherit;">a1.sinks.k1.hdfs.path = /flume/events/%y-%m-%d/%H%M/</div>
<div style="background-color:inherit;">a1.sinks.k1.hdfs.filePrefix = events-</div>
<div style="background-color:inherit;">a1.sinks.k1.hdfs.round = true</div>
<div style="background-color:inherit;">a1.sinks.k1.hdfs.roundValue = 10</div>
<div style="background-color:inherit;">a1.sinks.k1.hdfs.roundUnit = minute</div>
<div style="background-color:inherit;">a1.sinks.k1.hdfs.rollInterval = 3</div>
<div style="background-color:inherit;">a1.sinks.k1.hdfs.rollSize = 20</div>
<div style="background-color:inherit;">a1.sinks.k1.hdfs.rollCount = 5</div>
<div style="background-color:inherit;">a1.sinks.k1.hdfs.batchSize = 1</div>
<div style="background-color:inherit;">a1.sinks.k1.hdfs.useLocalTimeStamp = true</div>
<div style="background-color:inherit;">#生成的文件类型，默认是Sequencefile，可用DataStream，则为普通文本</div>
<div style="background-color:inherit;">a1.sinks.k1.hdfs.fileType = DataStream</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;"># Use a channel which buffers events in memory</div>
<div style="background-color:inherit;">a1.channels.c1.type = memory</div>
<div style="background-color:inherit;">a1.channels.c1.capacity = 1000</div>
<div style="background-color:inherit;">a1.channels.c1.transactionCapacity = 100</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;"># Bind the source and sink to the channel</div>
<div style="background-color:inherit;">a1.sources.r1.channels = c1</div>
<div style="background-color:inherit;">a1.sinks.k1.channel = c1</div>
</div>
<div><br style="background-color:inherit;"></div>
<div><br style="background-color:inherit;"></div>
<div>
<div style="color:rgb(0,0,0);font-family:'微软雅黑';font-size:14px;font-style:normal;font-weight:normal;background-color:rgb(255,255,255);">
3.3.2 启动flume服务监控指定目录</div>
<div style="color:rgb(0,0,0);font-family:'微软雅黑';font-size:14px;font-style:normal;font-weight:normal;background-color:rgb(255,255,255);">
<div style="background-color:inherit;">
<div style="background-color:inherit;">bin/flume-ng agent -c conf -f conf/tail-hdfs.conf -n a1</div>
</div>
</div>
<div style="color:rgb(0,0,0);font-family:'微软雅黑';font-size:14px;font-style:normal;font-weight:normal;background-color:rgb(255,255,255);">
<br style="background-color:inherit;"></div>
<div style="color:rgb(0,0,0);font-family:'微软雅黑';font-size:14px;font-style:normal;font-weight:normal;background-color:rgb(255,255,255);">
3.3.3 写个脚本循环写数据到test.log文件中</div>
<div style="color:rgb(0,0,0);font-family:'微软雅黑';font-size:14px;font-style:normal;font-weight:normal;background-color:rgb(255,255,255);">
<br style="background-color:inherit;"></div>
<div style="color:rgb(0,0,0);font-family:'微软雅黑';font-size:14px;font-style:normal;font-weight:normal;background-color:rgb(255,255,255);">
<div style="background-color:inherit;">#!/bin/sh</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;">for i in {1..1000}</div>
<div style="background-color:inherit;">do</div>
<div style="background-color:inherit;">   echo $i &gt;&gt; /home/hadoop/log/test.log</div>
<div style="background-color:inherit;">done</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;">查看hdfs中是否有相关日志上传了:</div>
</div>
</div>
<img src="https://img-blog.csdn.net/20170921160932674?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3FoMjAxMDMwNDEy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br><p></p>
<p><br></p>
<p></p>
<div>3.4.1  最后来一个例子,从一台机器上用flume采集日志发送到有Hadoop节点的flume上最后存入hdfs中</div>
<div><br style="background-color:inherit;"></div>
<div><br style="background-color:inherit;"></div>
<div>hadoop节点上接收flume配置:</div>
<div><br style="background-color:inherit;"></div>
<div>
<div style="background-color:inherit;"># Name the components on this agent</div>
<div style="background-color:inherit;">a1.sources = r1</div>
<div style="background-color:inherit;">a1.sinks = k1</div>
<div style="background-color:inherit;">a1.channels = c1</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;"># Describe/configure the source</div>
<div style="background-color:inherit;">a1.sources.r1.type = avro</div>
<div style="background-color:inherit;">a1.sources.r1.channels = c1</div>
<div style="background-color:inherit;">a1.sources.r1.bind = 0.0.0.0</div>
<div style="background-color:inherit;">a1.sources.r1.port = 4141</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;"># Describe the sink</div>
<div style="background-color:inherit;">a1.sinks.k1.type = hdfs</div>
<div style="background-color:inherit;">a1.sinks.k1.channel = c1</div>
<div style="background-color:inherit;">a1.sinks.k1.hdfs.path = /flume/flash/%y-%m-%d/</div>
<div style="background-color:inherit;">a1.sinks.k1.hdfs.filePrefix = flash</div>
<div style="background-color:inherit;">a1.sinks.k1.hdfs.round = true</div>
<div style="background-color:inherit;">a1.sinks.k1.hdfs.roundValue = 1</div>
<div style="background-color:inherit;">a1.sinks.k1.hdfs.roundUnit = hour</div>
<div style="background-color:inherit;">a1.sinks.k1.hdfs.rollInterval = 3</div>
<div style="background-color:inherit;">a1.sinks.k1.hdfs.rollSize = 134217728</div>
<div style="background-color:inherit;">a1.sinks.k1.hdfs.rollCount = 5</div>
<div style="background-color:inherit;">a1.sinks.k1.hdfs.batchSize = 1</div>
<div style="background-color:inherit;">a1.sinks.k1.hdfs.useLocalTimeStamp = true</div>
<div style="background-color:inherit;">#生成的文件类型，默认是Sequencefile，可用DataStream，则为普通文本</div>
<div style="background-color:inherit;">a1.sinks.k1.hdfs.fileType = DataStream</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;"># Use a channel which buffers events in memory</div>
<div style="background-color:inherit;">a1.channels.c1.type = memory</div>
<div style="background-color:inherit;">a1.channels.c1.capacity = 1000</div>
<div style="background-color:inherit;">a1.channels.c1.transactionCapacity = 100</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;"># Bind the source and sink to the channel</div>
<div style="background-color:inherit;">a1.sources.r1.channels = c1</div>
<div style="background-color:inherit;">a1.sinks.k1.channel = c1</div>
</div>
<div><br style="background-color:inherit;"></div>
<div>nohup启动该flume服务</div>
<div><br style="background-color:inherit;"></div>
<div>nohup  <span style="font-family:'微软雅黑';font-size:14px;color:#000000;font-style:normal;font-weight:normal;background-color:rgb(255,255,255);">bin/flume-ng agent -c conf -f conf/</span><span style="line-height:1.5;background-color:inherit;">avro-hdfs.conf</span><span style="line-height:1.5;background-color:rgb(255,255,255);"> -n a1
  <span style="background-color:inherit;">&gt; </span><span style="background-color:inherit;">/home/hadoop/log/flume.log</span><span style="background-color:inherit;"> 2&gt;&amp;1 &amp;</span></span></div>
<div><br style="background-color:inherit;"></div>
<div><br style="background-color:inherit;"></div>
<div>在Web服务器上配置如下:</div>
<div><br style="background-color:inherit;"></div>
<div>
<div style="background-color:inherit;">##################</div>
<div style="background-color:inherit;"># Name the components on this agent</div>
<div style="background-color:inherit;">a1.sources = r1</div>
<div style="background-color:inherit;">a1.sinks = k1</div>
<div style="background-color:inherit;">a1.channels = c1</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;"># Describe/configure the source</div>
<div style="background-color:inherit;">a1.sources.r1.type = exec</div>
<div style="background-color:inherit;">a1.sources.r1.command = tail -F /gomeo2o/gome-flash_7317/logs/eventlog.log</div>
<div style="background-color:inherit;">a1.sources.r1.channels = c1</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;"># Describe the sink</div>
<div style="background-color:inherit;">a1.sinks = k1</div>
<div style="background-color:inherit;">a1.sinks.k1.type = avro</div>
<div style="background-color:inherit;">a1.sinks.k1.channel = c1</div>
<div style="background-color:inherit;">a1.sinks.k1.hostname = 10.115.1.65</div>
<div style="background-color:inherit;">a1.sinks.k1.port = 4141</div>
<div style="background-color:inherit;">a1.sinks.k1.batch-size = 2</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;"># Use a channel which buffers events in memory</div>
<div style="background-color:inherit;">a1.channels.c1.type = memory</div>
<div style="background-color:inherit;">a1.channels.c1.capacity = 1000</div>
<div style="background-color:inherit;">a1.channels.c1.transactionCapacity = 100</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;"># Bind the source and sink to the channel</div>
<div style="background-color:inherit;">a1.sources.r1.channels = c1</div>
<div style="background-color:inherit;">a1.sinks.k1.channel = c1</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
</div>
<div><br style="background-color:inherit;"></div>
<div>后台启动flume监控日志上传到Hadoop节点上的flume中</div>
<div><br style="background-color:inherit;"></div>
<div>
<div style="background-color:inherit;">nohup  bin/flume-ng agent -c conf -f conf/tail-avro.conf -n a1  &gt; /root/log/flume.log 2&gt;&amp;1 &amp;</div>
</div>
<div><br style="background-color:inherit;"></div>
<div>最后看看效果:</div>
<br><img src="https://img-blog.csdn.net/20170921160936870?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3FoMjAxMDMwNDEy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><p></p>
<p></p>
<div>到这里flume的核心功能,已经大概了解了,其余就剩下flume的监控和故障恢复和根据自身业务来定制优化相关配置了.这里都要</div>
<div>自己实践了.</div>
<br><p></p>
<p><br></p>
            </div>
                </div>