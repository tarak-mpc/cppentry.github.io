---
layout:     post
title:      flume六：Event Serializers介绍
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>flume默认提供以下 Serializers：</p>
<p>Body Text Serializer：</p>
a1.sinks = k1<br>
a1.sinks.k1.type = file_roll<br>
a1.sinks.k1.channel = c1<br>
a1.sinks.k1.sink.directory = /var/log/flume<br>
a1.sinks.k1.sink.serializer = text<br>
a1.sinks.k1.sink.serializer.appendNewline = false<br><br><br>
“Flume Event” Avro Event Serializer：<br>
a1.sinks.k1.type = hdfs<br>
a1.sinks.k1.channel = c1<br>
a1.sinks.k1.hdfs.path = /flume/events/%y-%m-%d/%H%M/%S<br>
a1.sinks.k1.serializer = avro_event<br>
a1.sinks.k1.serializer.compressionCodec = snappy<br><br><br>
Avro Event Serializer：<br>
a1.sinks.k1.type = hdfs<br>
a1.sinks.k1.channel = c1<br>
a1.sinks.k1.hdfs.path = /flume/events/%y-%m-%d/%H%M/%S<br>
a1.sinks.k1.serializer = org.apache.flume.sink.hdfs.AvroEventSerializer$Builder<br>
a1.sinks.k1.serializer.compressionCodec = snappy<br>
a1.sinks.k1.serializer.schemaURL = hdfs://namenode/path/to/schema.avsc
            </div>
                </div>