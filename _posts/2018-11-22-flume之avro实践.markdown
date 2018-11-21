---
layout:     post
title:      flume之avro实践
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：转载请注明出处。					https://blog.csdn.net/tiantang_1986/article/details/49997153				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>flume接收端配置：</p>
<p></p><pre><code class="language-plain">a1.channels = c1
a1.sources = r1
a1.sinks = k1
a1.channels.c1.type = memory
a1.sources.r1.channels = c1

a1.sources.r1.type = avro
a1.sources.r1.bind = 0.0.0.0
a1.sources.r1.port = 41414
a1.sources.r1.threads = 5

a1.sinks.k1.channel = c1
a1.sinks.k1.type = FILE_ROLL
a1.sinks.k1.sink.directory = Z:/flume/flume/logs/relog
a1.sinks.k1.r.max.message.size = 100000</code></pre><br>
flume发送端：
<p></p><pre><code class="language-plain">a1.sources = r1
a1.sinks = k1
a1.channels = c1
a1.sources.r1.type = spooldir
a1.sources.r1.spoolDir = F:/flume_simple_send/logs/relog
a1.sources.r1.fileHeader = true
a1.sources.r1.channels = c1
# Describe the sink
a1.sinks.k1.type = avro
a1.sinks.k1.channel = c1
a1.sinks.k1.hostname = localhost
a1.sinks.k1.port = 41414
a1.sinks.k1.threads = 5

a1.channels.c1.type = memory
a1.channels.c1.capacity = 100000
a1.channels.c1.transactionCapacity = 10000</code></pre>
<p><br></p>
<p>启动之后即可把F:/flume_simple_send/logs/relog的文件发送到本地端口 41414，然后接收端会从41414端口拿到数据写文件到<span style="font-family:'lucida Grande', Verdana;font-size:14px;">Z:/flume/flume/logs/relog</span></p>
<br><br>            </div>
                </div>