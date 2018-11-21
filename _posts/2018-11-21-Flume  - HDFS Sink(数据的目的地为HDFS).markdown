---
layout:     post
title:      Flume  - HDFS Sink(数据的目的地为HDFS)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p style="color:rgb(35,35,35);font-family:Verdana, Arial, helvetica, 'sans-seriff';font-size:14px;background-color:rgb(255,255,255);"><strong><em>一：flume介绍</em></strong></p><p style="color:rgb(35,35,35);font-family:Verdana, Arial, helvetica, 'sans-seriff';font-size:14px;background-color:rgb(255,255,255);">        Flume是一个分布式、可靠、和<strong><em>高可用</em></strong>的海量日志聚合的系统，支持在系统中定制各类数据发送方，用于收集数据；同时，Flume提供对数据进行简单处理，并写到各种数据接受方（可定制）的能力。，Flume架构分为三个部分 源-Source，接收器-Sink，通道-Channel<strong>。</strong></p><p style="color:rgb(35,35,35);font-family:Verdana, Arial, helvetica, 'sans-seriff';font-size:14px;background-color:rgb(255,255,255);"><strong><br></strong></p><p style="color:rgb(35,35,35);font-family:Verdana, Arial, helvetica, 'sans-seriff';font-size:14px;background-color:rgb(255,255,255);"><strong>flume<br>---------------------<br><span>	</span>agent(主件)：<br><span>	</span>source<span>	</span>:收集数据,把它转成事件（一个事件就相当于一条记录）<br><span>	</span>channel(通道)<span>	</span>:临时存储事件（event）<br></strong></p><p><strong><span>	</span>sink<span>	</span>:向destination写入数据（hdfs）</strong></p><p><strong><br></strong></p><p><strong><span style="color:rgb(35,35,35);font-family:Verdana, Arial, helvetica, 'sans-seriff';font-size:14px;background-color:rgb(255,255,255);">二：</span><span style="color:rgb(35,35,35);font-family:Verdana, Arial, helvetica, 'sans-seriff';font-size:14px;background-color:rgb(255,255,255);">配置文件：hdfs-s.conf</span><br></strong></p><pre><code class="language-html">#comonents
a1.sources = r1
a1.sinks = k1
a1.channels = c1

#source
a1.sources.r1.type = netcat
a1.sources.r1.bind = localhost
a1.sources.r1.port = 8888

#sink
a1.sinks = k1
a1.sinks.k1.type = hdfs
a1.sinks.k1.channel = c1
a1.sinks.k1.hdfs.path = /hadoop/flume/%y-%m-%d/%H%M/%S
a1.sinks.k1.hdfs.filePrefix = events-
a1.sinks.k1.hdfs.round = true
a1.sinks.k1.hdfs.roundValue = 10
a1.sinks.k1.hdfs.roundUnit = minute
a1.sinks.k1.hdfs.useLocalTimeStamp=true

#channel
a1.channels.c1.type=memory

#bind
a1.sources.r1.channels=c1
a1.sinks.k1.channel=c1</code></pre><p>执行如下命令：</p><p>[hadoop@master conf]$ flume-ng agent --conf-file hdfs-s.conf --name a1 </p><p><br></p><p>[hadoop@master ~]$ nc localhost 8888<br></p><p>helloworld<br>thank you<br></p><p>nice too meet you</p><p>图示：</p><p><img src="https://img-blog.csdn.net/20180707230542260?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxMDI4OTU4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></p><p><br></p><p>查看HDSF</p><p><img src="https://img-blog.csdn.net/20180707230923544?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxMDI4OTU4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></p>            </div>
                </div>