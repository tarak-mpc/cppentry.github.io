---
layout:     post
title:      Flume的Source--KafkaSource
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>以前多是用kafka来消费flume中的数据，今天突然要用flume消费kafka中的数据时，有点懵，赶紧查一查Apache的官宣~~~~~~</p>
<h1><a id="flumekafka_1"></a>flume从kafka中消费数据</h1>
<h2><a id="kafkaSource_3"></a>一、kafkaSource</h2>
<p>Kafka Source is an Apache Kafka consumer that reads messages from Kafka topics. If you have multiple Kafka sources running, you can configure them with the same Consumer Group so each will read a unique set of partitions for the topics.<br>
kafkaSource是一个从kafka的topic中读取消息的Apache kafka的消费者；如果运行着许多/多样的kafka Source，可以使用相同的消费者组来配置它们，来保证它们读到topics中唯一分区集中的数据。</p>
<h2><a id="kafka_8"></a>二、kafka的配置项</h2>
<p><strong>加粗的为必配置项</strong></p>

<table>
<thead>
<tr>
<th align="center">Property Name</th>
<th align="center">Default</th>
<th align="center">Description</th>
</tr>
</thead>
<tbody>
<tr>
<td align="center"><strong>channels</strong></td>
<td align="center">-</td>
<td align="center"></td>
</tr>
<tr>
<td align="center"><strong>type</strong></td>
<td align="center">-</td>
<td align="center">此组件的类型名称需要写为org.apache.flume.source.kafka.KafkaSource</td>
</tr>
<tr>
<td align="center"><strong>kafka.bootstrap.servers(kafka服务器)</strong></td>
<td align="center">-</td>
<td align="center">List of brokers in the Kafka cluster used by the source(此kafka集群中用来做source的brokers的列表)</td>
</tr>
<tr>
<td align="center"><a href="http://kafka.consumer.group.id" rel="nofollow">kafka.consumer.group.id</a>(kafka的消费者组id)</td>
<td align="center">flume</td>
<td align="center">Unique identified of consumer group. Setting the same id in multiple sources or agents indicates that they are part of the same consumer group(唯一标识消费者组)</td>
</tr>
<tr>
<td align="center"><strong>kafka.topics</strong></td>
<td align="center">-</td>
<td align="center">Comma-separated list of topics the kafka consumer will read messages from.(kafka consumer将要读取的，以逗号分隔的topics的列表)</td>
</tr>
<tr>
<td align="center"><strong>kafka.topics.regex</strong></td>
<td align="center">-</td>
<td align="center">Regex that defines set of topics the source is subscribed on. This property has higher priority than kafka.topics and overrides kafka.topics if exists.(被source订阅的，用来定义topics集的正则表达式)</td>
</tr>
<tr>
<td align="center">batchSize(批大小)</td>
<td align="center">1000</td>
<td align="center">Maximum number of messages written to Channel in one batch(一批数据包含消息的最大条数)</td>
</tr>
<tr>
<td align="center">batchDurationMillis(batch持续时间)</td>
<td align="center">1000</td>
<td align="center">Maximum time (in ms) before a batch will be written to Channel The batch will be written whenever the first of size and time will be reached.(批写入channel之前的最大时间)</td>
</tr>
<tr>
<td align="center">backoffSleepIncrement</td>
<td align="center">1000</td>
<td align="center">Initial and incremental wait time that is triggered when a Kafka Topic appears to be empty. Wait period will reduce aggressive pinging of an empty Kafka Topic. One second is ideal for ingestion use cases but a lower value may be required for low latency operations with interceptors.</td>
</tr>
<tr>
<td align="center">maxBackoffSleep</td>
<td align="center">5000</td>
<td align="center">Maximum wait time that is triggered when a Kafka Topic appears to be empty. Five seconds is ideal for ingestion use cases but a lower value may be required for low latency operations with interceptors.</td>
</tr>
<tr>
<td align="center">useFlumeEventFormat</td>
<td align="center">false</td>
<td align="center">By default events are taken as bytes from the Kafka topic directly into the event body. Set to true to read events as the Flume Avro binary format. Used in conjunction with the same property on the KafkaSink or with the parseAsFlumeEvent property on the Kafka Channel this will preserve any Flume headers sent on the producing side.</td>
</tr>
<tr>
<td align="center">setTopicHeader</td>
<td align="center">true</td>
<td align="center">When set to true, stores the topic of the retrieved message into a header, defined by the topicHeader property.</td>
</tr>
<tr>
<td align="center">topicHeader</td>
<td align="center">topic</td>
<td align="center">Defines the name of the header in which to store the name of the topic the message was received from, if the setTopicHeader property is set to true. Care should be taken if combining with the Kafka Sink topicHeader property so as to avoid sending the message back to the same topic in a loop.</td>
</tr>
<tr>
<td align="center">migrateZookeeperOffsets</td>
<td align="center">true</td>
<td align="center">When no Kafka stored offset is found, look up the offsets in Zookeeper and commit them to Kafka. This should be true to support seamless Kafka client migration from older versions of Flume. Once migrated this can be set to false, though that should generally not be required. If no Zookeeper offset is found, the Kafka configuration kafka.consumer.auto.offset.reset defines how offsets are handled. Check Kafka documentation for details</td>
</tr>
<tr>
<td align="center">kafka.consumer.security.protocol</td>
<td align="center">PLAINTEXT</td>
<td align="center">Set to SASL_PLAINTEXT, SASL_SSL or SSL if writing to Kafka using some level of security. See below for additional info on secure setup.</td>
</tr>
<tr>
<td align="center">more consumer security props</td>
<td align="center"></td>
<td align="center">If using SASL_PLAINTEXT, SASL_SSL or SSL refer to Kafka security for additional properties that need to be set on consumer.</td>
</tr>
<tr>
<td align="center">Other Kafka Consumer Properties</td>
<td align="center">–</td>
<td align="center">These properties are used to configure the Kafka Consumer. Any consumer property supported by Kafka can be used. The only requirement is to prepend the property name with the prefix kafka.consumer. For example: kafka.consumer.auto.offset.reset</td>
</tr>
</tbody>
</table><h6><a id="_31"></a><strong>弃用的属性</strong></h6>

<table>
<thead>
<tr>
<th align="center">Property Name</th>
<th align="center">Default</th>
<th align="center">Description</th>
</tr>
</thead>
<tbody>
<tr>
<td align="center">topic</td>
<td align="center">-</td>
<td align="center">Use kafka.topics</td>
</tr>
<tr>
<td align="center">groupId</td>
<td align="center">flume</td>
<td align="center">Use <a href="http://kafka.consumer.group.id" rel="nofollow">kafka.consumer.group.id</a></td>
</tr>
<tr>
<td align="center">zookeeperConnect</td>
<td align="center">-</td>
<td align="center">0.9.x.版本之后kafka消费者客户端不再支持，使用kafka.bootstrap.servers来与kafka集群创建连接)</td>
</tr>
</tbody>
</table><h4><a id="topictopic__39"></a>topic订阅实例（通过逗号分隔的topic 列表）</h4>
<p>Example for topic subscription by comma-separated topic list.</p>
<pre><code>tier1.sources.source1.type = org.apache.flume.source.kafka.KafkaSource
tier1.sources.source1.channels = channel1
tier1.sources.source1.batchSize = 5000
tier1.sources.source1.batchDurationMillis = 2000
tier1.sources.source1.kafka.bootstrap.servers = localhost:9092
tier1.sources.source1.kafka.topics = test1, test2
tier1.sources.source1.kafka.consumer.group.id = custom.g.id
</code></pre>
<h4><a id="topic_50"></a>通过正则进行topic订阅的实例</h4>
<p>Example for topic subscription by regex</p>
<pre><code>tier1.sources.source1.type = org.apache.flume.source.kafka.KafkaSource
tier1.sources.source1.channels = channel1
tier1.sources.source1.kafka.bootstrap.servers = localhost:9092
tier1.sources.source1.kafka.topics.regex = ^topic[0-9]$
# the default kafka.consumer.group.id=flume is used
</code></pre>
<h2><a id="kafkaSource_59"></a>三、安全性和kafkaSource</h2>
<p>Security and Kafka Source<br>
Secure authentication as well as data encryption is supported on the communication channel between Flume and Kafka. For secure authentication SASL/GSSAPI (Kerberos V5) or SSL (even though the parameter is named SSL, the actual protocol is a TLS implementation) can be used from Kafka version 0.9.0.<br>
<strong>在flume以及kafka之间的通信信道中支持安全验证以及数据加密。对于安全验证SASL/GSSAPI或者SSL在kafka0.9.0版本之后可以使用。</strong><br>
As of now data encryption is solely provided by SSL/TLS.<br>
<strong>目前数据安全仅支持SSL/TLS</strong><br>
Setting kafka.consumer.security.protocol to any of the following value means:<br>
SASL_PLAINTEXT - Kerberos or plaintext authentication with no data encryption<br>
SASL_SSL - Kerberos or plaintext authentication with data encryption<br>
SSL - TLS based encryption with optional authentication.</p>
<h2><a id="TLS_and_Kafka_Source_71"></a>四、TLS and Kafka Source:</h2>
<p>Please read the steps described in Configuring Kafka Clients SSL to learn about additional configuration settings for fine tuning for example any of the following: security provider, cipher suites, enabled protocols, truststore or keystore types.</p>
<h4><a id="_74"></a>服务端验证和数据加密的配置实例</h4>
<p>Example configuration with server side authentication and data encryption</p>
<pre><code>a1.sources.source1.type = org.apache.flume.source.kafka.KafkaSource
a1.sources.source1.kafka.bootstrap.servers = kafka-1:9093,kafka-2:9093,kafka-3:9093
a1.sources.source1.kafka.topics = mytopic
a1.sources.source1.kafka.consumer.group.id = flume-consumer
a1.sources.source1.kafka.consumer.security.protocol = SSL
a1.sources.source1.kafka.consumer.ssl.truststore.location=/path/to/truststore.jks
a1.sources.source1.kafka.consumer.ssl.truststore.password=&lt;password to access the truststore&gt;
</code></pre>
<p>Note: By default the property ssl.endpoint.identification.algorithm is not defined, so hostname verification is not performed. In order to enable hostname verification, set the following properties<br>
注意：默认ssl.endpoint.identification.algorithm没有定义，所以主机名验证没有执行。为了开启主机验证需要进行如下配置</p>
<pre><code>a1.sources.source1.kafka.consumer.ssl.endpoint.identification.algorithm=HTTPS
</code></pre>
<p>Once enabled, clients will verify the server’s fully qualified domain name (FQDN) against one of the following two fields:<br>
一旦开启，客户端将验证服务器的全程域名(FQDN)来防备以下两个字段中的一个<br>
1、Common Name (CN) <a href="https://tools.ietf.org/html/rfc6125#section-2.3" rel="nofollow">https://tools.ietf.org/html/rfc6125#section-2.3</a><br>
2、Subject Alternative Name (SAN) <a href="https://tools.ietf.org/html/rfc5280#section-4.2.1.6" rel="nofollow">https://tools.ietf.org/html/rfc5280#section-4.2.1.6</a></p>
<p>If client side authentication is also required then additionally the following should be added to Flume agent configuration. Each Flume agent has to have its client certificate which has to be trusted by Kafka brokers either individually or by their signature chain. Common example is to sign each client certificate by a single Root CA which in turn is trusted by Kafka brokers.<br>
如果客户端仍然需要验证，需要在flume的agent配置中增加如下配置。每个flume agent必须有它客户端的客户端证书，此客户端证书必须要么被kafka的brokers信任，要么被它们的签名链信任。</p>
<pre><code>a1.sources.source1.kafka.consumer.ssl.keystore.location=/path/to/client.keystore.jks
a1.sources.source1.kafka.consumer.ssl.keystore.password=&lt;password to access the keystore&gt;
</code></pre>
<p>If keystore and key use different password protection then ssl.key.password property will provide the required additional secret for both consumer keystores:</p>
<pre><code>a1.sources.source1.kafka.consumer.ssl.key.password=&lt;password to access the key&gt;
</code></pre>
<h2><a id="Kerberoskafka_Source_105"></a>五、Kerberos(麻省理工学院安全认证系统)和kafka Source</h2>
<p>Kerberos and Kafka Source:</p>
<p>To use Kafka source with a Kafka cluster secured with Kerberos, set the consumer.security.protocol properties noted above for consumer. The Kerberos keytab and principal to be used with Kafka brokers is specified in a JAAS file’s “KafkaClient” section. “Client” section describes the Zookeeper connection if needed. See Kafka doc for information on the JAAS file contents. The location of this JAAS file and optionally the system wide kerberos configuration can be specified via JAVA_OPTS in <a href="http://flume-env.sh" rel="nofollow">flume-env.sh</a>:</p>
<pre><code>JAVA_OPTS="$JAVA_OPTS -Djava.security.krb5.conf=/path/to/krb5.conf"
JAVA_OPTS="$JAVA_OPTS -Djava.security.auth.login.config=/path/to/flume_jaas.conf"
</code></pre>
<h4><a id="Example_secure_configuration_using_SASL_PLAINTEXT_114"></a>Example secure configuration using SASL_PLAINTEXT:</h4>
<pre><code>a1.sources.source1.type = org.apache.flume.source.kafka.KafkaSource
a1.sources.source1.kafka.bootstrap.servers = kafka-1:9093,kafka-2:9093,kafka-3:9093
a1.sources.source1.kafka.topics = mytopic
a1.sources.source1.kafka.consumer.group.id = flume-consumer
a1.sources.source1.kafka.consumer.security.protocol = SASL_PLAINTEXT
a1.sources.source1.kafka.consumer.sasl.mechanism = GSSAPI
a1.sources.source1.kafka.consumer.sasl.kerberos.service.name = kafka
</code></pre>
<h4><a id="Example_secure_configuration_using_SASL_SSL_124"></a>Example secure configuration using SASL_SSL:</h4>
<pre><code>a1.sources.source1.type = org.apache.flume.source.kafka.KafkaSource
a1.sources.source1.kafka.bootstrap.servers = kafka-1:9093,kafka-2:9093,kafka-3:9093
a1.sources.source1.kafka.topics = mytopic
a1.sources.source1.kafka.consumer.group.id = flume-consumer
a1.sources.source1.kafka.consumer.security.protocol = SASL_SSL
a1.sources.source1.kafka.consumer.sasl.mechanism = GSSAPI
a1.sources.source1.kafka.consumer.sasl.kerberos.service.name = kafka
a1.sources.source1.kafka.consumer.ssl.truststore.location=/path/to/truststore.jks
a1.sources.source1.kafka.consumer.ssl.truststore.password=&lt;password to access the truststore&gt;
</code></pre>
<p>Sample JAAS file. For reference of its content please see client config sections of the desired authentication mechanism (GSSAPI/PLAIN) in Kafka documentation of SASL configuration. Since the Kafka Source may also connect to Zookeeper for offset migration, the “Client” section was also added to this example. This won’t be needed unless you require offset migration, or you require this section for other secure components. Also please make sure that the operating system user of the Flume processes has read privileges on the jaas and keytab files.</p>
<pre><code>Client {
  com.sun.security.auth.module.Krb5LoginModule required
  useKeyTab=true
  storeKey=true
  keyTab="/path/to/keytabs/flume.keytab"
  principal="flume/flumehost1.example.com@YOURKERBEROSREALM";
};

KafkaClient {
  com.sun.security.auth.module.Krb5LoginModule required
  useKeyTab=true
  storeKey=true
  keyTab="/path/to/keytabs/flume.keytab"
  principal="flume/flumehost1.example.com@YOURKERBEROSREALM";
};
</code></pre>
<p>在网上找了一个flume中kafka中消费数据，再存入hdfs中的例子，和官网的有些出入，使用了0.9.0弃用了的属性来配置，先记下来，以备不时之需</p>
<pre><code>#source的名字
agent.sources = kafkaSource
# channels的名字，建议按照type来命名
agent.channels = memoryChannel
# sink的名字，建议按照目标来命名
agent.sinks = hdfsSink

# 指定source使用的channel名字
agent.sources.kafkaSource.channels = memoryChannel
# 指定sink需要使用的channel的名字,注意这里是channel
agent.sinks.hdfsSink.channel = memoryChannel

#-------- kafkaSource相关配置-----------------
# 定义消息源类型
agent.sources.kafkaSource.type = org.apache.flume.source.kafka.KafkaSource
# 定义kafka所在zk的地址
#
# 这里特别注意: 是kafka的zookeeper的地址
#
agent.sources.kafkaSource.zookeeperConnect = 127.0.0.1:2181
# 配置消费的kafka topic
#agent.sources.kafkaSource.topic = testtopic# 配置消费者组的id
agent.sources.kafkaSource.groupId = flume
# 消费超时时间,参照如下写法可以配置其他所有kafka的consumer选项。注意格式从kafka.xxx开始是consumer的配置属性
agent.sources.kafkaSource.kafka.consumer.timeout.ms = 100



#------- memoryChannel相关配置-------------------------
# channel类型
agent.channels.memoryChannel.type = memory
# channel存储的事件容量
agent.channels.memoryChannel.capacity=10000
# 事务容量
agent.channels.memoryChannel.transactionCapacity=1000

#---------hdfsSink 相关配置------------------
agent.sinks.hdfsSink.type = hdfs
# 注意, 我们输出到下面一个子文件夹datax中
agent.sinks.hdfsSink.hdfs.path = hdfs://lenovo:9000/user/hive/warehouse/test/%Y%m%d%H
agent.sinks.hdfsSink.hdfs.writeFormat = Text
agent.sinks.hdfsSink.hdfs.fileType = DataStream


agent.sinks.hdfsSink.hdfs.rollSize = 1024
agent.sinks.hdfsSink.hdfs.rollCount = 0
agent.sinks.hdfsSink.hdfs.rollInterval = 60

#配置前缀和后缀
agent.sinks.hdfsSink.hdfs.filePrefix=test
agent.sinks.hdfsSink.hdfs.fileSuffix=.data

#避免文件在关闭前使用临时文件
agent.sinks.hdfsSink.hdfs.inUserPrefix=_
agent.sinks.hdfsSink.hdfs.inUserSuffix=

#自定义拦截器
agent.sources.kafkaSource.interceptors=i1
agent.sources.kafkaSource.interceptors.i1.type=com.hadoop.flume.FormatInterceptor$Builder
</code></pre>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>