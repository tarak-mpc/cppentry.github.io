---
layout:     post
title:      JEESZ-kafka集群安装
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="color:rgb(47,47,47);font-size:16px;">
1. 在根目录创建kafka文件夹（service1、service2、service3都创建）<br></p>
<p style="color:rgb(47,47,47);font-size:16px;">
[root@localhost /]# mkdir kafka</p>
<p style="color:rgb(47,47,47);font-size:16px;">
2.通过Xshell上传文件到service1服务器：上传kafka_2.9.2-0.8.1.1.tgz到/software文件夹</p>
<p style="color:rgb(47,47,47);font-size:16px;">
3.远程copy将service1下的/software/kafka_2.9.2-0.8.1.1.tgz到service2、service3</p>
<p style="color:rgb(47,47,47);font-size:16px;">
[root@localhost software]# scp -r /software/kafka_2.9.2-0.8.1.1.tgz root@192.168.2.212:/software/</p>
<p style="color:rgb(47,47,47);font-size:16px;">
[root@localhost software]# scp -r /software/kafka_2.9.2-0.8.1.1.tgz root@192.168.2.213:/software/</p>
<p style="color:rgb(47,47,47);font-size:16px;">
3.copy /software/kafka_2.9.2-0.8.1.1.tgz到/kafka/目录（service1、service2、service3都执行）</p>
<p style="color:rgb(47,47,47);font-size:16px;">
[root@localhost software]# cp /software/kafka_2.9.2-0.8.1.1.tgz /kafka/</p>
<p style="color:rgb(47,47,47);font-size:16px;">
4.安装解压kafka_2.9.2-0.8.1.1.tgz（service1、service2、service3都执行）</p>
<p style="color:rgb(47,47,47);font-size:16px;">
[root@localhost /]# cd /kafka/</p>
<p style="color:rgb(47,47,47);font-size:16px;">
[root@localhost kafka]# tar -zxvf kafka_2.9.2-0.8.1.1.tgz</p>
<p style="color:rgb(47,47,47);font-size:16px;">
5.创建kafka消息目录（service1,service2,service3都要创建）</p>
<p style="color:rgb(47,47,47);font-size:16px;">
[root@localhost kafka]# mkdir kafkaLogs</p>
<p style="color:rgb(47,47,47);font-size:16px;">
6. 修改kafka的配置文件（service1,service2,service3都要配置）</p>
<p style="color:rgb(47,47,47);font-size:16px;">
[root@localhost /]# cd /kafka/kafka_2.9.2-0.8.1.1/</p>
<p style="color:rgb(47,47,47);font-size:16px;">
[root@localhost kafka_2.9.2-0.8.1.1]# cd config/</p>
<p style="color:rgb(47,47,47);font-size:16px;">
[root@localhost config]# ls</p>
<p style="color:rgb(47,47,47);font-size:16px;">
consumer.properties  log4j.properties  producer.properties  server.properties  test-log4j.properties  tools-log4j.properties  zookeeper.properties</p>
<p style="color:rgb(47,47,47);font-size:16px;">
[root@localhost config]# vi server.properties</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# Licensed to the Apache Software Foundation (ASF) under one or more</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# contributor license agreements.  See the NOTICE file distributed with</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# this work for additional information regarding copyright ownership.</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# The ASF licenses this file to You under the Apache License, Version 2.0</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# (the "License"); you may not use this file except in compliance with</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# the License.  You may obtain a copy of the License at</p>
<p style="color:rgb(47,47,47);font-size:16px;">
#</p>
<p style="color:rgb(47,47,47);font-size:16px;">
#<a href="http://www.apache.org/licenses/LICENSE-2.0" rel="nofollow" style="color:rgb(49,148,208);">http://www.apache.org/licenses/LICENSE-2.0</a></p>
<p style="color:rgb(47,47,47);font-size:16px;">
#</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# Unless required by applicable law or agreed to in writing, software</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# distributed under the License is distributed on an "AS IS" BASIS,</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# See the License for the specific language governing permissions and</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# limitations under the License.</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# see kafka.server.KafkaConfig for additional details and defaults</p>
<p style="color:rgb(47,47,47);font-size:16px;">
############################# Server Basics #############################</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# The id of the broker. This must be set to a unique integer for each broker.</p>
<p style="color:rgb(47,47,47);font-size:16px;">
broker.id=0  ---唯一标识</p>
<p style="color:rgb(47,47,47);font-size:16px;">
############################# Socket Server Settings #############################</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# The port the socket server listens on</p>
<p style="color:rgb(47,47,47);font-size:16px;">
port=19092  --当前broker对外提供的TCP端口,默认9092</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# Hostname the broker will bind to. If not set, the server will bind to all interfaces</p>
<p style="color:rgb(47,47,47);font-size:16px;">
host.name=192.168.2.213  --一般是关闭状态，我们要将它打开，如果dns解析失败，会出现文件句柄泄露，不要小看dns解析失败率，如果dns解析失败率为万分之一，由于kafka的性能非常高，每个topic的每个分区，每秒可以处理十万多条的数据，即使万分之一的失败率，每秒也要泄露10个文件句柄，很快句柄数就会泄露完毕，就会超过linux打开文件的数，就会出现异常，所以我们配置ip，就不会进行dns解析</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# Hostname the broker will advertise to producers and consumers. If not set, it uses the</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# value for "host.name" if configured.  Otherwise, it will use the value returned from</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# java.net.InetAddress.getCanonicalHostName().</p>
<p style="color:rgb(47,47,47);font-size:16px;">
#advertised.host.name=</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# The port to publish to ZooKeeper for clients to use. If this is not set,</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# it will publish the same port that the broker binds to.</p>
<p style="color:rgb(47,47,47);font-size:16px;">
#advertised.port=</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# The number of threads handling network requests</p>
<p style="color:rgb(47,47,47);font-size:16px;">
num.network.threads=2   --broker网络处理的线程数，一般不做处理</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# The number of threads doing disk I/O</p>
<p style="color:rgb(47,47,47);font-size:16px;">
num.io.threads=8  --broker io处理的线程数，这个数量一定要比log.dirs的目录数要大</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# The send buffer (SO_SNDBUF) used by the socket server</p>
<p style="color:rgb(47,47,47);font-size:16px;">
socket.send.buffer.bytes=1048576  --将发送的消息先放到缓冲区，当到达一定量的时候再一次性发出</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# The receive buffer (SO_RCVBUF) used by the socket server</p>
<p style="color:rgb(47,47,47);font-size:16px;">
socket.receive.buffer.bytes=1048576  --kafka接受消息的缓冲区，当接受的数量达到一定量的时候再写入磁盘</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# The maximum size of a request that the socket server will accept (protection against OOM)</p>
<p style="color:rgb(47,47,47);font-size:16px;">
socket.request.max.bytes=104857600   --像kafka发送或者请求消息的最大数，此设置不能超过java堆栈大小</p>
<p style="color:rgb(47,47,47);font-size:16px;">
############################# Log Basics #############################</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# A comma seperated list of directories under which to store log files</p>
<p style="color:rgb(47,47,47);font-size:16px;">
log.dirs=/kafka/kafkaLogs  --多个目录可以用，隔开</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# The default number of log partitions per topic. More partitions allow greater</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# parallelism for consumption, but this will also result in more files across</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# the brokers.</p>
<p style="color:rgb(47,47,47);font-size:16px;">
num.partitions=2  --一个topic默认分区数</p>
<p style="color:rgb(47,47,47);font-size:16px;">
############################# Log Flush Policy #############################</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# Messages are immediately written to the filesystem but by default we only fsync() to sync</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# the OS cache lazily. The following configurations control the flush of data to disk.</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# There are a few important trade-offs here:</p>
<p style="color:rgb(47,47,47);font-size:16px;">
#    1. Durability: Unflushed data may be lost if you are not using replication.</p>
<p style="color:rgb(47,47,47);font-size:16px;">
#    2. Latency: Very large flush intervals may lead to latency spikes when the flush does occur as there will be a lot of data to flush.</p>
<p style="color:rgb(47,47,47);font-size:16px;">
#    3. Throughput: The flush is generally the most expensive operation, and a small flush interval may lead to exceessive seeks.</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# The settings below allow one to configure the flush policy to flush data after a period of time or</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# every N messages (or both). This can be done globally and overridden on a per-topic basis.</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# The number of messages to accept before forcing a flush of data to disk</p>
<p style="color:rgb(47,47,47);font-size:16px;">
#log.flush.interval.messages=10000</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# The maximum amount of time a message can sit in a log before we force a flush</p>
<p style="color:rgb(47,47,47);font-size:16px;">
#log.flush.interval.ms=1000</p>
<p style="color:rgb(47,47,47);font-size:16px;">
############################# Log Retention Policy #############################</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# The following configurations control the disposal of log segments. The policy can</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# be set to delete segments after a period of time, or after a given size has accumulated.</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# A segment will be deleted whenever *either* of these criteria are met. Deletion always happens</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# from the end of the log.</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# The minimum age of a log file to be eligible for deletion</p>
<p style="color:rgb(47,47,47);font-size:16px;">
log.retention.hours=168</p>
<p style="color:rgb(47,47,47);font-size:16px;">
message.max.byte=5048576   --kafka每条消息容纳的最大大小</p>
<p style="color:rgb(47,47,47);font-size:16px;">
default.replication.factor=2  --默认的复制因子，默认消息只有一个副本，不太安全，所以设置为2，如果某个分区的消息失败了，我们可以使用另一个分区的消息服务</p>
<p style="color:rgb(47,47,47);font-size:16px;">
replica.fetch.max.byte=5048576 --kafka每条消息容纳的最大大小</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# A size-based retention policy for logs. Segments are pruned from the log as long as the remaining</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# segments don't drop below log.retention.bytes.</p>
<p style="color:rgb(47,47,47);font-size:16px;">
#log.retention.bytes=1073741824</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# The maximum size of a log segment file. When this size is reached a new log segment will be created.</p>
<p style="color:rgb(47,47,47);font-size:16px;">
log.segment.bytes=536870912  --消息持久化的最大大小</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# The interval at which log segments are checked to see if they can be deleted according</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# to the retention policies</p>
<p style="color:rgb(47,47,47);font-size:16px;">
log.retention.check.interval.ms=60000</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# By default the log cleaner is disabled and the log retention policy will default to just delete segments after their retention expires.</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# If log.cleaner.enable=true is set the cleaner will be enabled and individual logs can then be marked for log compaction.</p>
<p style="color:rgb(47,47,47);font-size:16px;">
log.cleaner.enable=false  --不使用log压缩</p>
<p style="color:rgb(47,47,47);font-size:16px;">
############################# Zookeeper #############################</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# Zookeeper connection string (see zookeeper docs for details).</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# This is a comma separated host:port pairs, each corresponding to a zk</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# server. e.g. "127.0.0.1:3000,127.0.0.1:3001,127.0.0.1:3002".</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# You can also append an optional chroot string to the urls to specify the</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# root directory for all kafka znodes.</p>
<p style="color:rgb(47,47,47);font-size:16px;">
zookeeper.connect=192.168.2.211:2181,192.168.2.212:2181,192.168.2.213:2181   --zk地址</p>
<p style="color:rgb(47,47,47);font-size:16px;">
# Timeout in ms for connecting to zookeeper</p>
<p style="color:rgb(47,47,47);font-size:16px;">
zookeeper.connection.timeout.ms=1000000</p>
<p style="color:rgb(47,47,47);font-size:16px;">
7.启动kafka服务</p>
<p style="color:rgb(47,47,47);font-size:16px;">
[root@localhost bin]# ./kafka-server-start.sh -daemon ../config/server.properties</p>
<p style="color:rgb(47,47,47);font-size:16px;">
[root@localhost bin]# jps</p>
<p style="color:rgb(47,47,47);font-size:16px;">
27413 Kafka</p>
<p style="color:rgb(47,47,47);font-size:16px;">
27450 Jps</p>
<p style="color:rgb(47,47,47);font-size:16px;">
17884 QuorumPeerMain</p>
<p style="color:rgb(47,47,47);font-size:16px;">
8.验证kafka集群</p>
<p style="color:rgb(47,47,47);font-size:16px;">
[root@localhost bin]# ./kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 2 --partitions 1 --topic test</p>
<p style="color:rgb(47,47,47);font-size:16px;">
Created topic "test".</p>
<p style="color:rgb(47,47,47);font-size:16px;">
9.在service1上开启producer程序</p>
<p style="color:rgb(47,47,47);font-size:16px;">
./kafka-console-producer.sh --broker-list 192.168.2.211:9092 --topic test</p>
<p style="color:rgb(47,47,47);font-size:16px;">
[root@localhost bin]# ./kafka-console-producer.sh --broker-list 192.168.2.211:9092 --topic test</p>
<p style="color:rgb(47,47,47);font-size:16px;">
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".</p>
<p style="color:rgb(47,47,47);font-size:16px;">
SLF4J: Defaulting to no-operation (NOP) logger implementation</p>
<p style="color:rgb(47,47,47);font-size:16px;">
SLF4J: See<a href="http://www.slf4j.org/codes.html#StaticLoggerBinder" rel="nofollow" style="color:rgb(49,148,208);">http://www.slf4j.org/codes.html#StaticLoggerBinder</a>for further details.</p>
<p style="color:rgb(47,47,47);font-size:16px;">
10. 在service2上开启consumer程序</p>
<p style="color:rgb(47,47,47);font-size:16px;">
[root@localhost bin]# ./kafka-console-consumer.sh --zookeeper localhost:2181 --topic test --from-beginning</p>
<p style="color:rgb(47,47,47);font-size:16px;">
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".</p>
<p style="color:rgb(47,47,47);font-size:16px;">
SLF4J: Defaulting to no-operation (NOP) logger implementation</p>
<p style="color:rgb(47,47,47);font-size:16px;">
SLF4J: See<a href="http://www.slf4j.org/codes.html#StaticLoggerBinder" rel="nofollow" style="color:rgb(49,148,208);">http://www.slf4j.org/codes.html#StaticLoggerBinder</a>for further details.</p>
<p style="color:rgb(47,47,47);font-size:16px;">
11.在producer中发送消息：hello jeesz</p>
<p style="color:rgb(47,47,47);font-size:16px;">
[root@localhost bin]# ./kafka-console-consumer.sh --zookeeper localhost:2181 --topic test --from-beginning</p>
<p style="color:rgb(47,47,47);font-size:16px;">
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".</p>
<p style="color:rgb(47,47,47);font-size:16px;">
SLF4J: Defaulting to no-operation (NOP) logger implementation</p>
<p style="color:rgb(47,47,47);font-size:16px;">
SLF4J: See<a href="http://www.slf4j.org/codes.html#StaticLoggerBinder" rel="nofollow" style="color:rgb(49,148,208);">http://www.slf4j.org/codes.html#StaticLoggerBinder</a>for further details.</p>
<p style="color:rgb(47,47,47);font-size:16px;">
hello jeesz</p>
<p style="color:rgb(47,47,47);font-size:16px;">
12. 在consumer中接受到消息</p>
<p style="color:rgb(47,47,47);font-size:16px;">
[root@localhost bin]# ./kafka-console-consumer.sh --zookeeper localhost:2181 --topic test --from-beginning</p>
<p style="color:rgb(47,47,47);font-size:16px;">
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".</p>
<p style="color:rgb(47,47,47);font-size:16px;">
SLF4J: Defaulting to no-operation (NOP) logger implementation</p>
<p style="color:rgb(47,47,47);font-size:16px;">
SLF4J: See<a href="http://www.slf4j.org/codes.html#StaticLoggerBinder" rel="nofollow" style="color:rgb(49,148,208);">http://www.slf4j.org/codes.html#StaticLoggerBinder</a>for further details.</p>
<p style="color:rgb(47,47,47);font-size:16px;">
hello jeesz</p>
<p style="color:rgb(47,47,47);font-size:16px;">
<strong>愿意了解框架技术或者源码的朋友直接加求求（企鹅）：2042849237</strong></p>
<p style="color:rgb(47,47,47);font-size:16px;">
<strong>更多详细源码参考来源：<a href="http://minglisoft.cn/technology" rel="nofollow" style="color:rgb(49,148,208);">http://minglisoft.cn/technology</a></strong></p>
            </div>
                </div>