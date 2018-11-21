---
layout:     post
title:      flume一：flumeSource介绍
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
flume介绍<br><p>首先,flume是一个分布式、可靠和可用的系统,主要用来收集日志数据等。</p>
<p>flume主要有三个插件组成：source，channel，sink。</p>
<p><br></p>
<p>本文主要列举flume提供的source。</p>
<p>source fan-out:指的是一个source可以指向多个channel；<br><br>
一个source可以指向多个channel,<br>
一个sink只能被一个channel指向。<br></p>
<p>flume默认提供的source有以下这些：</p>
<p>Avro Source<br>
a1.sources = r1<br>
a1.channels = c1<br>
a1.sources.r1.type = avro<br>
a1.sources.r1.channels = c1<br>
a1.sources.r1.bind = 0.0.0.0<br>
a1.sources.r1.port = 4141<br><br><br>
Thrift Source<br>
a1.sources = r1<br>
a1.channels = c1<br>
a1.sources.r1.type = thrift<br>
a1.sources.r1.channels = c1<br>
a1.sources.r1.bind = 0.0.0.0<br>
a1.sources.r1.port = 4141<br><br><br>
Exec Source<br>
a1.sources = r1<br>
a1.channels = c1<br>
a1.sources.r1.type = exec<br>
a1.sources.r1.command = tail -F /var/log/secure<br>
a1.sources.r1.channels = c1<br><br><br>
JMS Source<br>
a1.sources = r1<br>
a1.channels = c1<br>
a1.sources.r1.type = jms<br>
a1.sources.r1.channels = c1<br>
a1.sources.r1.initialContextFactory = org.apache.activemq.jndi.ActiveMQInitialContextFactory<br>
a1.sources.r1.connectionFactory = GenericConnectionFactory<br>
a1.sources.r1.providerURL = tcp://mqserver:61616<br>
a1.sources.r1.destinationName = BUSINESS_DATA<br>
a1.sources.r1.destinationType = QUEUE<br><br><br>
Spooling Directory Source<br>
a1.channels = ch-1<br>
a1.sources = src-1<br>
a1.sources.src-1.type = spooldir<br>
a1.sources.src-1.channels = ch-1<br>
a1.sources.src-1.spoolDir = /var/log/apache/flumeSpool<br>
a1.sources.src-1.fileHeader = true<br><br><br>
Taildir Source<br>
a1.sources = r1<br>
a1.channels = c1<br>
a1.sources.r1.type = TAILDIR<br>
a1.sources.r1.channels = c1<br>
a1.sources.r1.positionFile = /var/log/flume/taildir_position.json<br>
a1.sources.r1.filegroups = f1 f2<br>
a1.sources.r1.filegroups.f1 = /var/log/test1/example.log<br>
a1.sources.r1.headers.f1.headerKey1 = value1<br>
a1.sources.r1.filegroups.f2 = /var/log/test2/.*log.*<br>
a1.sources.r1.headers.f2.headerKey1 = value2<br>
a1.sources.r1.headers.f2.headerKey2 = value2-2<br>
a1.sources.r1.fileHeader = true<br><br><br>
Twitter 1% firehose Source<br>
a1.sources = r1<br>
a1.channels = c1<br>
a1.sources.r1.type = org.apache.flume.source.twitter.TwitterSource<br>
a1.sources.r1.channels = c1<br>
a1.sources.r1.consumerKey = YOUR_TWITTER_CONSUMER_KEY<br>
a1.sources.r1.consumerSecret = YOUR_TWITTER_CONSUMER_SECRET<br>
a1.sources.r1.accessToken = YOUR_TWITTER_ACCESS_TOKEN<br>
a1.sources.r1.accessTokenSecret = YOUR_TWITTER_ACCESS_TOKEN_SECRET<br>
a1.sources.r1.maxBatchSize = 10<br>
a1.sources.r1.maxBatchDurationMillis = 200<br><br><br>
Kafka Source<br>
配置将主题订阅以逗号分隔的主题列表。<br>
tier1.sources.source1.type = org.apache.flume.source.kafka.KafkaSource<br>
tier1.sources.source1.channels = channel1<br>
tier1.sources.source1.batchSize = 5000<br>
tier1.sources.source1.batchDurationMillis = 2000<br>
tier1.sources.source1.kafka.bootstrap.servers = localhost:9092<br>
tier1.sources.source1.kafka.topics = test1, test2<br>
tier1.sources.source1.kafka.consumer.group.id = custom.g.id<br><br><br>
配置将主题订阅以正则表达式匹配的主题列表。<br>
tier1.sources.source1.type = org.apache.flume.source.kafka.KafkaSource<br>
tier1.sources.source1.channels = channel1<br>
tier1.sources.source1.kafka.bootstrap.servers = localhost:9092<br>
tier1.sources.source1.kafka.topics.regex = ^topic[0-9]$<br>
#the default kafka.consumer.group.id=flume is used<br><br><br>
配置服务器端身份验证和数据加密<br>
a1.channels.channel1.type = org.apache.flume.channel.kafka.KafkaChannel<br>
a1.channels.channel1.kafka.bootstrap.servers = kafka-1:9093,kafka-2:9093,kafka-3:9093<br>
a1.channels.channel1.kafka.topic = channel1<br>
a1.channels.channel1.kafka.consumer.group.id = flume-consumer<br>
a1.channels.channel1.kafka.consumer.security.protocol = SSL<br>
a1.channels.channel1.kafka.consumer.ssl.truststore.location=/path/to/truststore.jks<br>
a1.channels.channel1.kafka.consumer.ssl.truststore.password=&lt;password to access the truststore&gt;<br>
配置验证主机名<br>
a1.channels.channel1.kafka.consumer.ssl.endpoint.identification.algorithm=HTTPS<br><br><br>
NetCat Source<br>
a1.sources = r1<br>
a1.channels = c1<br>
a1.sources.r1.type = netcat<br>
a1.sources.r1.bind = 0.0.0.0<br>
a1.sources.r1.port = 6666<br>
a1.sources.r1.channels = c1<br><br><br>
Sequence Generator Source<br>
a1.sources = r1<br>
a1.channels = c1<br>
a1.sources.r1.type = seq<br>
a1.sources.r1.channels = c1<br><br><br>
Syslog Sources<br>
a1.sources = r1<br>
a1.channels = c1<br>
a1.sources.r1.type = syslogtcp<br>
a1.sources.r1.port = 5140<br>
a1.sources.r1.host = localhost<br>
a1.sources.r1.channels = c1<br><br><br>
Multiport Syslog TCP Source<br>
a1.sources = r1<br>
a1.channels = c1<br>
a1.sources.r1.type = multiport_syslogtcp<br>
a1.sources.r1.channels = c1<br>
a1.sources.r1.host = 0.0.0.0<br>
a1.sources.r1.ports = 10001 10002 10003<br>
a1.sources.r1.portHeader = port<br><br><br>
Syslog UDP Source<br>
a1.sources = r1<br>
a1.channels = c1<br>
a1.sources.r1.type = syslogudp<br>
a1.sources.r1.port = 5140<br>
a1.sources.r1.host = localhost<br>
a1.sources.r1.channels = c1<br><br><br>
HTTP Source<br>
a1.sources = r1<br>
a1.channels = c1<br>
a1.sources.r1.type = http<br>
a1.sources.r1.port = 5140<br>
a1.sources.r1.channels = c1<br>
a1.sources.r1.handler = org.example.rest.RestHandler<br>
a1.sources.r1.handler.nickname = random props<br><br><br>
Stress Source<br>
a1.sources = stresssource-1<br>
a1.channels = memoryChannel-1<br>
a1.sources.stresssource-1.type = org.apache.flume.source.StressSource<br>
a1.sources.stresssource-1.size = 10240<br>
a1.sources.stresssource-1.maxTotalEvents = 1000000<br>
a1.sources.stresssource-1.channels = memoryChannel-1<br><br><br>
Legacy Sources<br>
a1.sources = r1<br>
a1.channels = c1<br>
a1.sources.r1.type = org.apache.flume.source.avroLegacy.AvroLegacySource<br>
a1.sources.r1.host = 0.0.0.0<br>
a1.sources.r1.bind = 6666<br>
a1.sources.r1.channels = c1<br><br><br>
Thrift Legacy Source<br>
a1.sources = r1<br>
a1.channels = c1<br>
a1.sources.r1.type = org.apache.flume.source.thriftLegacy.ThriftLegacySource<br>
a1.sources.r1.host = 0.0.0.0<br>
a1.sources.r1.bind = 6666<br>
a1.sources.r1.channels = c1<br><br><br>
Custom Source<br>
自定义source,要继承Source接口<br><br><br>
Scribe Source<br>
a1.sources = r1<br>
a1.channels = c1<br>
a1.sources.r1.type = org.apache.flume.source.scribe.ScribeSource<br>
a1.sources.r1.port = 1463<br>
a1.sources.r1.workerThreads = 5<br>
a1.sources.r1.channels = c1<br><br><br></p>
<p><br></p>
<p><br></p>
            </div>
                </div>