---
layout:     post
title:      使用flume问题总结3——一个使用flume拦截器和选择器的简单实例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/anjing900812/article/details/48999067				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h4>
</h4><h3>flume配置文件示例：</h3>
<div><pre><code class="language-java">//flume source选用syslog TCP source
producer.sources = syslogSource

//For each one of the sources, the type is defined
producer.sources.syslogSource.type = syslogtcp
producer.sources.syslogSource.bind = localhost
producer.sources.syslogSource.port = 5496
//官方文档中介绍选择器时未指明仍然需要配置这一项，但不配置时报错
producer.sources.syslogSource.channels = cbaidu csina cgoogle 

//类型为正则表达式提取拦截器(regex_extractor),
//即从event正文中提取信息加至header，配合下面的channel selector就可以进行event路由了
//the interceptors:domain name
producer.sources.syslogSource.interceptors=domainname
producer.sources.syslogSource.interceptors.domainname.type=regex_extractor
producer.sources.syslogSource.interceptors.domainname.regex = YM:(\\w+)
//从正文中提取域名，作为一个新的header
//key为domain_name，value为为对应的域名信息，添加至event
//only add one header
producer.sources.syslogSource.interceptors.domainname.serializers = s1 
producer.sources.syslogSource.interceptors.domainname.serializers.s1.name = domain_name
producer.sources.syslogSource.interceptors.domainname.serializers.s2.type = default

//类型：多路复用选择器multiplexing，即依据指定的header值，将event路由至不同的channel
//selector
producer.sources.syslogSource.selector.type = multiplexing
producer.sources.syslogSource.selector.header = domain_name//哪一个header
producer.sources.syslogSource.selector.mapping.baidu = cbaidu//不同的header值路由至不同的channel
producer.sources.syslogSource.selector.mapping.sina = csina
producer.sources.syslogSource.selector.mapping.google = cgoogle
producer.sources.syslogSource.selector.default = cbaidu


// Each channel's type is defined.
//3个channel均是内存
producer.channels = cbaidu csina cgoogle
producer.channels.cbaidu.type = memory
producer.channels.csina.type = memory
producer.channels.cgoogle.type = memory
producer.channels.cbaidu.capacity = 1000
producer.channels.csina.capacity = 1000
producer.channels.cgoogle.capacity = 1000

// Each sink's type must be defined
//3个不同的sink，其中两个为kafka，一个是File Roll
producer.sinks = sbaidu ssina sgoogle
producer.sinks.sbaidu.type = org.apache.flume.sink.kafka.KafkaSink
producer.sinks.sbaidu.channel = cbaidu
producer.sinks.sbaidu.topic = baidu
producer.sinks.sbaidu.brokerList = localhost:9797
producer.sinks.sbaidu.requiredAcks = 1
producer.sinks.sbaidu.batchSize = 20
producer.sinks.sbaidu.metadata.broker.list = localhost:9092
producer.sinks.sbaidu.producer.type=sync
producer.sinks.sbaidu.serializer.class=kafka.serializer.DefaultEncoder

producer.sinks.ssina.type = file_roll 
producer.sinks.ssina.channel = csina
producer.sinks.ssina.sink.directory = /usr/local/flume/result 
producer.sinks.ssina.sink.rollInterval = 0 
producer.sinks.ssina.sink.serializer = avro_event

producer.sinks.sgoogle.type = org.apache.flume.sink.kafka.KafkaSink
producer.sinks.sgoogle.channel = cgoogle
producer.sinks.sgoogle.topic = google
producer.sinks.sgoogle.brokerList = localhost:9898
producer.sinks.sgoogle.requiredAcks = 1
producer.sinks.sgoogle.batchSize = 20
producer.sinks.sgoogle.metadata.broker.list = localhost:9092
producer.sinks.sgoogle.producer.type=sync
producer.sinks.sgoogle.serializer.class=kafka.serializer.DefaultEncoder</code></pre>
<h5>说明：flume source：syslogtcp、3个memory channel、3个sink（一个file roll，两个kafka sink）<br><span></span>    提取event中的域名字段，生成新的header（domain_name），根据此header将消息路由至不同的sink<br><span></span>    event举例：YM:baidu YDIP:63.12.79.4<br></h5>
</div>
<pre></pre>
<pre></pre>

            </div>
                </div>