---
layout:     post
title:      flume、kafka、avro组成的消息系统
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>利用apache flume和Apache kafka（依赖zookeeper）完成一个消息系统，具体消息传递：kafka--&gt;flume A--&gt;flume B--&gt;kafka。将数据通过flume进程A从kafka集群中读取，通过avro sink将数据发送到flume进程B ,最后将数据kafkasink 到kafka集群中。</p>

<p>测试过程两端kafka可选不同topic，例如：将topic  test3中的数据经过消息系统发送到topic  test2 中。</p>

<p>如图：</p>

<p><img alt="" class="has" height="288" src="https://img-blog.csdnimg.cn/20181106091816426.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L05vbm9yb3lhX1pvcm8=,size_16,color_FFFFFF,t_70" width="617"></p>

<h1>一、搭建环境</h1>

<p>Apache-flume-1.8.0</p>

<p>Zookeeper-3.4.10</p>

<p>kafka_2.10-0.10.2.1</p>

<p>kafka-manager-1.3.3.7（可选）</p>

<p>该文验证仅使用单机环境，集群环境可另外搭建。</p>

<h1>二、安装</h1>

<h2>1、zookeeper配置（kafka依赖它，来保存meta元数据消息，保证系统可用性）</h2>

<p>解压下载的压缩包后，将conf目录下的zoo_sample.cfg改名为zoo.cfg，并主要修改里面内容，修改数据存储目录并打开日志清理：</p>

<pre class="has">
<code class="language-bash">dataDir=../data
autopurge.snapRetainCount=3
autopurge.purgeInterval=1
</code></pre>

<h2>2、kafka配置</h2>

<p>server.properties内容：</p>

<pre class="has">
<code class="language-bash">broker.id=1
host.name=192.168.79.129
port=9092
log.dirs=/home/mywork/kafka/kafka-logs
zookeeper.connect=192.168.79.129:2181
num.partitions=1
#是否可以通过管理工具删除topic，默认是false
delete.topic.enable=true
#是否允许自动创建Topic，若是false，就需要通过命令创建Topic
auto.create.topics.enable=false
#消息日志备份因子，默认是1
default.replication.factor=2
#__consumer_offsets的分区数，默认50
offsets.topic.num.partitions=50
#__consumer_offsets副本因子，默认1
offsets.topic.replication.factor=2
# 连接ZooKeeper的超时时间
zookeeper.connection.timeout.ms=45000
#当Producer设置request.required.acks为-1时，指定需要写入成功的副本的最小数目
min.insync.replicas=1
#Broker允许接收单条消息的最大字节数。单位：字节
message.max.bytes=1048576
#如果一个副本中没有同步的消息条数超过这个数值，Leader会认为该副本已经失效，并将其从ISR中移除
replica.lag.max.messages=204800000
######################网络/IO相关配置##################
#Broker用来处理网络请求的线程数目
num.network.threads=4
#Broker用来处理磁盘I/O的线程数目，这个线程数目建议至少等于硬盘的个数
num.io.threads=8
#服务端发送缓冲区大小。单位：字节
socket.send.buffer.bytes=1048576
#服务端接收缓冲区大小。单位：字节
socket.receive.buffer.bytes=1048576
#服务端接收请求的最大大小。此配置可以有效避免Server端内存溢出，并且应小于KAFKA_HEAP_OPTS设置的大小。单位：字节
socket.request.max.bytes=104857600
#在网络请求处理线程停止读取新请求之前，可以排队等待I/O线程处理的最大请求个数
queued.max.requests=100
#当日志过期时（超过了要保存的时间），采用的清除策略，删除或者压缩
log.cleanup.policy=delete
#指定强制进行日志数据落盘的时间间隔。单位：毫秒。
#log.flush.interval.ms=9223372036854775807
#log.flush.interval.messages=9223372036854775807
#log.flush.scheduler.interval.ms=9223372036854775807
######################数据复制/恢复##################
# 每个数据目录用来数据恢复的线程数目
num.recovery.threads.per.data.dir=10
#副本向Leader请求同步数据的线程数，增大这个数值会增加副本的I/O并发度
num.replica.fetchers=4
######################日志相关配置##################
#  日志数据文件保留的最长时间
log.retention.hours=72
#指定创建新日志数据分段文件的时间间隔，即使文件大小没有达到log.segment.bytes，也会创建。单位：小时
log.roll.hours=72
# 指定每个Partition上的日志数据所能达到的最大字节。默认情况下无限制
log.retention.bytes=-1
# 指定日志数据中分段文件的最大字节数。单位：字节. 超过这个大小创建新segment file
log.segment.bytes=1073741824
# 检查日志数据的分段文件的间隔时间，以确定文件属性是否达到删除要求。单位：毫秒。
log.retention.check.interval.ms=300000
######################请求清除间隔条数##################
# 同步请求的清除fetch  purgatory 间隔消息条数
fetch.purgatory.purge.interval.requests=1000
#Producer请求清除producer purgatory 间隔消息条数
producer.purgatory.purge.interval.requests=1000
root@Master:/home/mywork/kafka/config# vi server.properties 
broker.id=1
host.name=192.168.79.129
port=9092
log.dirs=/home/mywork/kafka/kafka-logs
zookeeper.connect=192.168.79.129:2181
num.partitions=1
#是否可以通过管理工具删除topic，默认是false
delete.topic.enable=true
#是否允许自动创建Topic，若是false，就需要通过命令创建Topic
auto.create.topics.enable=false
#消息日志备份因子，默认是1
default.replication.factor=2
#__consumer_offsets的分区数，默认50
offsets.topic.num.partitions=50
#__consumer_offsets副本因子，默认1
offsets.topic.replication.factor=2
# 连接ZooKeeper的超时时间
zookeeper.connection.timeout.ms=45000
#当Producer设置request.required.acks为-1时，指定需要写&gt;入成功的副本的最小数目
min.insync.replicas=1
#Broker允许接收单条消息的最大字节数。单位：字节
message.max.bytes=1048576
#如果一个副本中没有同步的消息条数超过这个数值，Leader会
认为该副本已经失效，并将其从ISR中移除
replica.lag.max.messages=204800000
######################网络/IO相关配置##################
#Broker用来处理网络请求的线程数目
num.network.threads=4
#Broker用来处理磁盘I/O的线程数目，这个线程数目建议至少&gt;等于硬盘的个数
num.io.threads=8
#服务端发送缓冲区大小。单位：字节
socket.send.buffer.bytes=1048576
#服务端接收缓冲区大小。单位：字节
socket.receive.buffer.bytes=1048576
#服务端接收请求的最大大小。此配置可以有效避免Server端内
存溢出，并且应小于KAFKA_HEAP_OPTS设置的大小。单位：字节
socket.request.max.bytes=104857600
#在网络请求处理线程停止读取新请求之前，可以排队等待I/O&gt;线程处理的最大请求个数
queued.max.requests=100
@</code></pre>

<p>之后有必要的话还可以修改生产者配置文件和消费者配置文件相关内容。（本文不做修改）</p>

<h2>3、kafka-manager配置</h2>

<p style="margin-left:0cm;"><span style="color:#000000;">vi kafka-manager-1.3.3.7/conf/application.conf</span></p>

<p style="margin-left:0cm;"><span style="color:#000000;">将</span><span style="color:#000000;">kafka-manager.zkhosts="localhost:2181"</span><span style="color:#000000;">配置项，修改为实际的</span><span style="color:#000000;"> zookeeper</span><span style="color:#000000;">连接地址，例如以本次安装的</span><span style="color:#000000;">kafka</span><span style="color:#000000;">集群为例修改为</span></p>

<p style="margin-left:0cm;"><span style="color:#000000;">kafka-manager.zkhosts="192.168.79.129:2181"</span></p>

<h2 style="margin-left:0cm;"><strong><span style="color:#000000;">4、flume配置</span></strong></h2>

<p><span style="color:#000000;">修改conf目录下flume-env.sh ，将JAVA_HOME修改为本地的jdk路径。</span></p>

<pre class="has">
<code class="language-bash"># Enviroment variables can be set here.

export JAVA_HOME=/usr/java/jdk1.8.0_171</code></pre>

<p style="margin-left:0cm;"> </p>

<h1 style="margin-left:0cm;">三、flume进程配置文件</h1>

<p>flume进程B的配置文件avro-memory-kafka.conf，负责监听avro客户端连接，avro源来自连接方的sink，通道使用file，防止数据丢失，内容如下：</p>

<pre class="has">
<code class="language-bash">#agent avro-memory-kafka
 
avro-memory-kafka.sources = avro-source
avro-memory-kafka.sinks = kafka-sink
avro-memory-kafka.channels = memory-channel
 
avro-memory-kafka.sources.avro-source.type = avro
avro-memory-kafka.sources.avro-source.bind = 192.168.79.129
avro-memory-kafka.sources.avro-source.port = 44444
 
avro-memory-kafka.sinks.kafka-sink.type = org.apache.flume.sink.kafka.KafkaSink
avro-memory-kafka.sinks.kafka-sink.brokerList = 192.168.79.129:9092
avro-memory-kafka.sinks.kafka-sink.batchSize = 5
avro-memory-kafka.sinks.kafka-sink.requiredAcks = 1

avro-memory-kafka.channels.memory-channel.type = file
avro-memory-kafka.channels.memory-channel.checkpointDir=/home/mywork/tmp/flume-file-checkpoint
avro-memory-kafka.channels.memory-channel.dataDirs=/home/mywork/tmp/flume-file-data
 
avro-memory-kafka.sources.avro-source.channels = memory-channel
avro-memory-kafka.sinks.kafka-sink.channel = memory-channel
</code></pre>

<p>flume进程A 的配置文件kafka-memory-avro.conf,负责从kafka 源读取数据并将数据avro sink到目的方。设置了拦截器，详细可见注意事项。</p>

<pre class="has">
<code class="language-bash">kafka-memory-avro.sources = s1
kafka-memory-avro.channels = c1
kafka-memory-avro.sinks = k1

kafka-memory-avro.sources.s1.type=org.apache.flume.source.kafka.KafkaSource
kafka-memory-avro.sources.s1.batchSize = 20
kafka-memory-avro.sources.s1.kafka.bootstrap.servers=192.168.79.129:9092
kafka-memory-avro.sources.s1.kafka.topics = test3
kafka-memory-avro.sources.s1.kafka.consumer.group.id = test-consumer-group

#拦截器
kafka-memory-avro.sources.s1.interceptors = i1
kafka-memory-avro.sources.s1.interceptors.i1.type = static
kafka-memory-avro.sources.s1.interceptors.i1.key = topic
kafka-memory-avro.sources.s1.interceptors.i1.preserveExisting = false
kafka-memory-avro.sources.s1.interceptors.i1.value=test2

kafka-memory-avro.sinks.k1.type = avro
kafka-memory-avro.sinks.k1.hostname = 192.168.79.129
kafka-memory-avro.sinks.k1.port = 44444

kafka-memory-avro.channels.c1.type = file
kafka-memory-avro.channels.c1.checkpontDir=/home/mywork/tmp/flume-file-checkpoint
kafka-memory-avro.channels.c1.dataDirs=/home/mywork/tmp/flme-file/data

kafka-memory-avro.sources.s1.channels=c1
kafka-memory-avro.sinks.k1.channel = c1
</code></pre>

<h1>四、启动</h1>

<h2>1、启动zookeeper</h2>

<pre class="has">
<code class="language-bash">bin/zkServer.sh start conf/zoo.cfg</code></pre>

<h2>2、启动kafka</h2>

<pre class="has">
<code>bin/kafka-server-start.sh config/server.properties</code></pre>

<h2>3、启动kafka-manager（可选，方便查看）</h2>

<pre class="has">
<code>bin/kafka-manager -Dconfig.file=conf/application.conf -Dapplication.home=./</code></pre>

<h2>4、启动flume B</h2>

<pre class="has">
<code class="language-bash">bin/flume-ng agent --name avro-memory-kafka -c conf -f conf/avro-memory-kafka.conf -Dflume.root.logger=INFO,console</code></pre>

<h2>5、启动flume A</h2>

<pre class="has">
<code class="language-bash">bin/flume-ng agent --name kafka-memory-avro -c conf/ -f conf/kafka-memory-avro.conf -Dflume.root.logger=DEBUG,console</code></pre>

<p>下图为启动成功：</p>

<p><img alt="" class="has" height="236" src="https://img-blog.csdnimg.cn/20181106125421840.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L05vbm9yb3lhX1pvcm8=,size_16,color_FFFFFF,t_70" width="803"></p>

<h1>五、运行结果</h1>

<p>向kafka中topic为test3中生产数据，开启一个消费者终端，消费topic为test2的数据。</p>

<p><img alt="" class="has" height="243" src="https://img-blog.csdnimg.cn/20181106130331120.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L05vbm9yb3lhX1pvcm8=,size_16,color_FFFFFF,t_70" width="851"></p>

<h1>六、注意事项</h1>

<p style="margin-left:0cm;"><img alt="" class="has" height="346" src="https://img-blog.csdnimg.cn/20181106133906547.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L05vbm9yb3lhX1pvcm8=,size_16,color_FFFFFF,t_70" width="788"></p>

<p style="margin-left:0cm;">不要配置kafka源以将数据发送到kafka接收器。如果这样做，kafka源会在事件头中设置主题，覆盖sink配置并创建无限循环，在源和sink之前来回发送消息。</p>

<p style="margin-left:0cm;">如果需要同时使用源和接收器，请使用<span style="color:#FF0000;">拦截器</span>（interceptor）修改事件头并设置不同的主题。</p>

<p style="margin-left:0cm;">在flume A或flume B配置文件<span style="color:#FF0000;">任意一个节点</span>配置拦截器都可以解决sink topic覆盖问题。</p>

<p style="margin-left:0cm;">在<span style="color:#FF0000;">flume-1.8</span>版本中，可以不设置拦截器，设置<span style="color:#FF0000;">avro-memory-kafka.sinks.kafka-sink.allowTopicOverride = false</span> 即可实现topic的不覆盖。</p>            </div>
                </div>