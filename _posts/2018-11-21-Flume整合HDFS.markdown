---
layout:     post
title:      Flume整合HDFS
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/daiyutage/article/details/52129830				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<pre><code class="language-html">#agent1表示代理名称  
agent1.sources=source1  
agent1.sinks=sink1  
agent1.channels=channel1  
  
#Spooling Directory是监控指定文件夹中新文件的变化，一旦新文件出现，就解析该文件内容，然后写入到channle。写入完成后，标记该文件已完成或者删除该文件。  
#配置source1  
agent1.sources.source1.type=spooldir  
agent1.sources.source1.spoolDir=/home/hadoop/flume/data 
agent1.sources.source1.channels=channel1  
agent1.sources.source1.fileHeader = false  
agent1.sources.source1.interceptors = i1  
agent1.sources.source1.interceptors.i1.type = timestamp  
  
#配置channel1  

# Use a channel which buffers events in memory
agent1.channels.channel1.type = memory
agent1.channels.channel1.capacity = 1000
agent1.channels.channel1.transactionCapacity = 100
  
#配置sink1  
agent1.sinks.sink1.type=hdfs  
agent1.sinks.sink1.hdfs.path=hdfs://master:9000/flume
agent1.sinks.sink1.hdfs.fileType=DataStream  
agent1.sinks.sink1.hdfs.writeFormat=TEXT  
agent1.sinks.sink1.hdfs.rollInterval=1  
agent1.sinks.sink1.channel=channel1  
agent1.sinks.sink1.hdfs.filePrefix=%Y-%m-%d  </code></pre>
<p><br></p>
<p>查看HDFS</p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20160805154649218?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
            </div>
                </div>