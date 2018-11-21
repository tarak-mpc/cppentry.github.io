---
layout:     post
title:      Flume+Kafka整合
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">1.下载flume-kafka-plus:</span><a href="https://github.com/beyondj2ee/flumeng-kafka-plugin" rel="nofollow" style="color:rgb(51,102,153);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">https://github.com/beyondj2ee/flumeng-kafka-plugin</a></p>
<p><span style="color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">在该项目中提取以下jar包放入环境中flume的lib下</span><br></p>
<p><span style="color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;"><span style="color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">还有package目录的flumeng-kafka-plugin.jar包一并放到flume的lib目录下</span><br></span></p>
<p><span style="color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;"><span style="color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">flume配置文件：</span></span></p>
<p><span style="color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;"><span style="color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;"></span></span></p>
<pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:'Source Code Pro';font-size:10.5pt;">#agent
producer.sources = s
producer.channels = c
producer.sinks = r

#source
producer.sources.s.type = spooldir
producer.sources.s.spoolDir = /data/logs/data-schedule
producer.sources.s.channels = c

producer.sinks.r.type = org.apache.flume.plugins.KafkaSink
producer.sinks.r.metadata.broker.list=127.0.0.1:9092
producer.sinks.r.partition.key=0
producer.sinks.r.partitioner.class=org.apache.flume.plugins.SinglePartition
producer.sinks.r.serializer.class=kafka.serializer.StringEncoder
producer.sinks.r.request.required.acks=0
producer.sinks.r.max.message.size=1000000
producer.sinks.r.producer.type=sync
producer.sinks.r.custom.encoding=UTF-8
producer.sinks.r.custom.topic.name=test

#Specify the channel for the sink
producer.sinks.r.channel = c

producer.channels.c.type = memory
producer.channels.c.capacity = 1000</pre>
<p></p>
<p><span style="color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;"><span style="color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">启动flume：</span></span></p>
<p><span style="color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;"><span style="color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">./flume-ng agent --conf ../conf
 --conf-file ../conf/flume2.conf --name producer -Dflume.root.logger=INFO,console<br></span></span></p>
            </div>
                </div>