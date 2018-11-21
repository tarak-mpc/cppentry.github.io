---
layout:     post
title:      flume 日志导入elasticsearch
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 id="flume-ng-elasticsearchKibana-Flume">Flume配置</h1>
<p>。</p>
<div><span style="font-size:18px;"><strong>flume生成的数据结构<br><br></strong></span></div>
<pre><code class="language-python">&lt;span style="font-size:18px;"&gt;"_index" : "logstash-2013.01.07",
"_type" : "tms_jboss_syslog",
"_id" : "a_M9X_0YSpmE7A_bEzIFiw",
"_score" : 1.0, "_source" : {"@source":"file://localhost.localdomain/tmp/logstash_test.log","@tags":[],"@fields":{},"@timestamp":"2013-01-07T10:53:50.941Z","@source_host":"localhost.localdomain","@source_path":"/tmp/logstash_test.log","@message":"[2013-01-05 11:02:19,969] packBoxNumber eq 00004000000044043412 createdOffice eq VIP_BJ:;null","@type":"tms_jboss_syslog"}&lt;/span&gt;</code></pre><span style="font-size:18px;"><br></span>flume配置文件
<p></p>
<p></p><pre><code class="language-python">agent.sources = tail

agent.channels = memoryChannel

agent.channels.memoryChannel.type = memory

agent.sources.tail.channels = memoryChannel

agent.sources.tail.type = exec

agent.sources.tail.command = tail -F /home/hadoop/flume/conf/es_log/es_log.log

agent.sources.tail.interceptors=i1 i2 i3

agent.sources.tail.interceptors.i1.type=regex_extractor

agent.sources.tail.interceptors.i1.regex = (\\w.*):(\\w.*):(\\w.*)\\s

agent.sources.tail.interceptors.i1.serializers = s1 s2 s3

agent.sources.tail.interceptors.i1.serializers.s1.name = source

agent.sources.tail.interceptors.i1.serializers.s2.name = type

agent.sources.tail.interceptors.i1.serializers.s3.name = src_path

agent.sources.tail.interceptors.i2.type=org.apache.flume.interceptor.TimestampInterceptor$Builder

agent.sources.tail.interceptors.i3.type=org.apache.flume.interceptor.HostInterceptor$Builder

agent.sources.tail.interceptors.i3.hostHeader = host

agent.sinks = elasticsearch

agent.sinks.elasticsearch.channel = memoryChannel

agent.sinks.elasticsearch.type=org.apache.flume.sink.elasticsearch.ElasticSearchSink

agent.sinks.elasticsearch.batchSize=100

agent.sinks.elasticsearch.hostNames=127.0.0.1:9300
agent.sinks.k1.indexType = bar_type
agent.sinks.elasticsearch.indexName=logstash
agent.sinks.elasticsearch.clusterName=elasticsearch
agent.sinks.elasticsearch.serializer=org.apache.flume.sink.elasticsearch.ElasticSearchLogStashEventSerializer
</code></pre>启动：
<p></p><pre><code class="language-python"> ../bin/flume-ng agent -c . -f es_log.conf -n agent  -Dflume.root.logger=INFO,console
</code></pre><br>
测试数据
<p></p><pre><code class="language-python">website:weblog:login_page weblog data1
website:weblog:profile_page weblog data2
website:weblog:transaction_page weblog data3
website:weblog:docs_page weblog data4
syslog:syslog:sysloggroup syslog data1
syslog:syslog:sysloggroup syslog data2
syslog:syslog:sysloggroup syslog data3
syslog:syslog:sysloggroup syslog data4
syslog:syslog:sysloggroup syslog data5
syslog:syslog:sysloggroup syslog data6</code></pre><img src="https://img-blog.csdn.net/20141009140854304?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3VuZmxvd2VyX2Nhbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><p>之后就可以在es集群上看到通过flume导入的数据了</p>
<p>这时候编辑log文件时候会被flume读入es集群中并实时生成索引  如下图所示：</p>
<p><img src="https://img-blog.csdn.net/20141009141223860?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3VuZmxvd2VyX2Nhbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p>这样就可以通过es对日志的实时检索了<br></p>
            </div>
                </div>