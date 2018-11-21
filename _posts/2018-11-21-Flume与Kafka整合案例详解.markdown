---
layout:     post
title:      Flume与Kafka整合案例详解
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_26840065/article/details/70174094				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3 id="环境配置">环境配置</h3>

<table>
<thead>
<tr>
  <th>名称</th>
  <th>版本</th>
  <th>下载地址</th>
</tr>
</thead>
<tbody><tr>
  <td>Centos 7.0</td>
  <td>64x</td>
  <td><a href="http://www.baidu.com/" rel="nofollow">百度</a></td>
</tr>
<tr>
  <td>Zookeeper</td>
  <td>3.4.5</td>
  <td></td>
</tr>
<tr>
  <td>Flume</td>
  <td>1.6.0</td>
  <td></td>
</tr>
<tr>
  <td>Kafka</td>
  <td>2.1.0</td>
  <td></td>
</tr>
</tbody></table>




<h3 id="配置flume">配置Flume</h3>

<p>这里就不介绍了<code>零基础出门右转看Flume的文章</code></p>

<p><a href="http://blog.csdn.net/qq_26840065/article/details/51099141" rel="nofollow">flume笔记</a></p>

<p>直接贴配置文件</p>

<pre><code>[root@zero239 kafka_2.10-0.10.1.1]# cat /opt/hadoop/apache-flume-1.6.0-bin/conf/kafka-conf.properties 
# The configuration file needs to define the sources,
# the channels and the sinks.
# Sources, channels and sinks are defined per agent,
# in this case called 'agent'

agent.sources = r1
agent.channels = c1
agent.sinks = s1

# For each one of the sources, the type is defined
#agent.sources.r1.type = spooldir
#agent.sources.r1.command = /opt/test/logs/data
#agent.sources.r1.fileHeader = true
#agent.sources.r1.channels = c1

agent.sources.r1.type = spooldir
agent.sources.r1.spoolDir = /opt/test/logs/data
agent.sources.r1.fileHeader = true

# Each sink's type must be defined

#agent.sinks.s1.type = logger

agent.sinks.s1.type = org.apache.flume.sink.kafka.KafkaSink
agent.sinks.s1.topic = logstest
agent.sinks.s1.brokerList = zero230:9092
agent.sinks.s1.requiredAcks = 1
agent.sinks.s1.batchSize = 2


# Each channel's type is defined.
agent.channels.c1.type = memory
agent.channels.c1.capacity = 100
agent.sources.r1.channels = c1
agent.sinks.s1.channel = c1
</code></pre>



<h3 id="配置kafka">配置Kafka</h3>

<pre><code># Licensed to the Apache Software Foundation (ASF) under one or more
# contributor license agreements.  See the NOTICE file distributed with
# this work for additional information regarding copyright ownership.
# The ASF licenses this file to You under the Apache License, Version 2.0
# (the "License"); you may not use this file except in compliance with
# the License.  You may obtain a copy of the License at
#
#    http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

# see kafka.server.KafkaConfig for additional details and defaults

############################# Server Basics #############################

# The id of the broker. This must be set to a unique integer for each broker.
broker.id=2

# Switch to enable topic deletion or not, default value is false
#delete.topic.enable=true

############################# Socket Server Settings #############################

# The address the socket server listens on. It will get the value returned from 
# java.net.InetAddress.getCanonicalHostName() if not configured.
#   FORMAT:
#     listeners = security_protocol://host_name:port
#   EXAMPLE:
#     listeners = PLAINTEXT://your.host.name:9092
#listeners=PLAINTEXT://:9092

# Hostname and port the broker will advertise to producers and consumers. If not set, 
# it uses the value for "listeners" if configured.  Otherwise, it will use the value
# returned from java.net.InetAddress.getCanonicalHostName().
#advertised.listeners=PLAINTEXT://your.host.name:9092

# The number of threads handling network requests
num.network.threads=3

# The number of threads doing disk I/O
num.io.threads=8

# The send buffer (SO_SNDBUF) used by the socket server
socket.send.buffer.bytes=102400

# The receive buffer (SO_RCVBUF) used by the socket server
socket.receive.buffer.bytes=102400

# The maximum size of a request that the socket server will accept (protection against OOM)
socket.request.max.bytes=104857600


############################# Log Basics #############################

# A comma seperated list of directories under which to store log files
log.dirs=/opt/hadoop/kafka_2.10-0.10.1.1/logs/tmp

# The default number of log partitions per topic. More partitions allow greater
# parallelism for consumption, but this will also result in more files across
# the brokers.
num.partitions=1

# The number of threads per data directory to be used for log recovery at startup and flushing at shutdown.
# This value is recommended to be increased for installations with data dirs located in RAID array.
num.recovery.threads.per.data.dir=1

############################# Log Flush Policy #############################

# Messages are immediately written to the filesystem but by default we only fsync() to sync
# the OS cache lazily. The following configurations control the flush of data to disk.
# There are a few important trade-offs here:
#    1. Durability: Unflushed data may be lost if you are not using replication.
#    2. Latency: Very large flush intervals may lead to latency spikes when the flush does occur as there will be a lot of data to flush.
#    3. Throughput: The flush is generally the most expensive operation, and a small flush interval may lead to exceessive seeks.
# The settings below allow one to configure the flush policy to flush data after a period of time or
# every N messages (or both). This can be done globally and overridden on a per-topic basis.

# The number of messages to accept before forcing a flush of data to disk
#log.flush.interval.messages=10000

# The maximum amount of time a message can sit in a log before we force a flush
#log.flush.interval.ms=1000

############################# Log Retention Policy #############################

# The following configurations control the disposal of log segments. The policy can
# be set to delete segments after a period of time, or after a given size has accumulated.
# A segment will be deleted whenever *either* of these criteria are met. Deletion always happens
# from the end of the log.

# The minimum age of a log file to be eligible for deletion
log.retention.hours=168

# A size-based retention policy for logs. Segments are pruned from the log as long as the remaining
# segments don't drop below log.retention.bytes.
#log.retention.bytes=1073741824

# The maximum size of a log segment file. When this size is reached a new log segment will be created.
log.segment.bytes=1073741824

# The interval at which log segments are checked to see if they can be deleted according
# to the retention policies
log.retention.check.interval.ms=300000

############################# Zookeeper #############################

# Zookeeper connection string (see zookeeper docs for details).
# This is a comma separated host:port pairs, each corresponding to a zk
# server. e.g. "127.0.0.1:3000,127.0.0.1:3001,127.0.0.1:3002".
# You can also append an optional chroot string to the urls to specify the
# root directory for all kafka znodes.
zookeeper.connect=zero230:2181,zero231:2181,zero239:2181

# Timeout in ms for connecting to zookeeper
zookeeper.connection.timeout.ms=6000
</code></pre>

<p><strong>我已经配置了集群Zookeeper所以在这里我指定是我配置的Zookeeper地址如果你没有配置的话可以直接使用<code>Kafka</code>内置的Zokeeper</strong></p>

<p><strong><a href="http://blog.csdn.net/qq_26840065/article/category/6150055" rel="nofollow">Zookeeper集群搭建配置</a></strong></p>



<h3 id="启动kafka验证是否成功">启动Kafka验证是否成功</h3>

<ol>
<li>启动Zookeeper <code>如果没有配置集群的这一步跳过</code></li>
<li><p>启动Kafka内置Zookeeper</p>

<p>bin/zookeeper-server-start.sh config/zookeeper.properties</p></li>
</ol>

<p>3.启动Kafka</p>

<pre><code>server1.properties 为刚刚自己编辑的名称
bin/kafka-server-start.sh config/server1.properties
</code></pre>

<p><img src="http://i.imgur.com/aWn93v0.png" alt="" title=""></p>

<p>4.创建一个名为<code>logstest</code>的<code>topic</code></p>

<pre><code>./bin/kafka-topics.sh --create --zookeeper zero230:2181 --replication-factor 1 --partitions 1 --topic logstest
</code></pre>

<p>5.查看Topic是否创建成功</p>

<pre><code>./bin/kafka-topics.sh --list --zookeeper localhost:2181
</code></pre>

<p>6.创建一个生产端(相当于是一个已经数据产生的用户吧)这样容易理解</p>

<pre><code>bin/kafka-console-producer.sh --broker-list zero230:9092 --topic logstest
</code></pre>

<p>7.创建一个消费端(意思就是可以看到<code>生产者</code>意思就是生产出来的数据可以看到<code>输出</code>)</p>

<pre><code>bin/kafka-console-consumer.sh --zookeeper zero230:2181 --topic logstest --from-beginning
</code></pre>



<h3 id="启动验证flume是否能与kafka对接">启动验证Flume是否能与Kafka对接</h3>

<pre><code>[root@zero239 apache-flume-1.6.0-bin]# ./bin/flume-ng agent --conf conf -f ./conf/kafka-conf.properties -n agent -Dflume.root.logger=INFO,console
</code></pre>

<p><strong>对接成功截图</strong> </p>

<p><img src="http://i.imgur.com/E5A0GIQ.png" alt="" title=""></p>

<p>各位同学可以看到在Flume<code>sinks</code>配置中我设置的是Kafka意思就是输出到<code>Kafka</code>中</p>

<pre><code>agent.sinks.s1.type = org.apache.flume.sink.kafka.KafkaSink
agent.sinks.s1.topic = logstest 刚刚创建的Topic名称
agent.sinks.s1.brokerList = zero230:9092 创建生产的机
</code></pre>

<p>在这里Flume与Kafka已经整合完毕了。</p>



<h3 id="下节剧透">下节剧透</h3>

<p><strong>JAVA实现Kafka读取</strong></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>