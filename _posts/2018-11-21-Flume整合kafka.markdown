---
layout:     post
title:      Flume整合kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/daiyutage/article/details/52129897				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="color:#ff0000;">需要把flumeng-kafka-plugin的jar包拷贝到flume的lib目录下</span></p>
<p></p><pre><code class="language-html"># example.conf: A single-node Flume configuration

# Name the components on this agent
a1.sources = r1
a1.sinks = k1
a1.channels = c1

#源的方式使用netcat
# Describe/configure the source
a1.sources.r1.type = netcat
a1.sources.r1.bind = localhost
a1.sources.r1.port = 44444

# Use a channel which buffers events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1



#sink的方式为Kafka
# Each sink's type must be defined  
a1.sinks.k1.type = org.apache.flume.plugins.KafkaSink  
a1.sinks.k1.metadata.broker.list=master:9092  
a1.sinks.k1.partition.key=0  
a1.sinks.k1.partitioner.class=org.apache.flume.plugins.SinglePartition  
a1.sinks.k1.serializer.class=kafka.serializer.StringEncoder  
a1.sinks.k1.request.required.acks=0  
a1.sinks.k1.max.message.size=1000000  
a1.sinks.k1.producer.type=sync  
a1.sinks.k1.custom.encoding=UTF-8  
a1.sinks.k1.custom.topic.name=test 
</code></pre><br><br>            </div>
                </div>