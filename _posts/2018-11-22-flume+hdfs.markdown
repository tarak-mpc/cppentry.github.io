---
layout:     post
title:      flume+hdfs
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>1、安装flume以及hadoop</p>
<p>2、配置flume配置文件，hdfs.conf</p>
<pre><code class="language-plain">#agent.sources.s1.type = spooldir
#agent.sources.s1.spoolDir = D:\\logs
#agent.sources.s1.fileHeader = false
#agent.sources.s1.channels=c1

agent.sources.s1.type=exec
agent.sources.s1.command=tail -F /tmp/cdz/log/test.log
agent.sources.s1.channels=c1


agent.channels.c1.type=memory
agent.channels.c1.capacity=10000
agent.channels.c1.transactionCapacity=100

agent.sinks.k1.type = hdfs
agent.sinks.k1.channel = c1
agent.sinks.k1.hdfs.path = /flume/events/%y-%m-%d/%H%M/%S
agent.sinks.k1.hdfs.filePrefix = events-
agent.sinks.k1.hdfs.round = true
agent.sinks.k1.hdfs.useLocalTimeStamp = true
agent.sinks.k1.hdfs.roundValue = 1
agent.sinks.k1.hdfs.roundUnit = minute
agent.sinks.k1.hdfs.fileType=DataStream


agent.channels = c1
agent.sources = s1
agent.sinks = k1

</code></pre>
<p><br>
3、运行</p>
<pre><code class="language-plain">sh bin/flume-ng agent --conf ./conf --conf-file ./conf/hdfs.conf --name agent -Dflume.root.logger=INFO,console</code></pre>
<p><br>
4、往/tmp/cdz/log/test.log写入数据，在hdfs的管理界面可以看到events-.*的文件，里面便是前面写入log文件的内容。</p>
            </div>
                </div>