---
layout:     post
title:      Kafka The Definitive Guide - 安装Kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/Geek_ymv/article/details/81262842				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>这一章将介绍如何安装和运行Kafka，包括如何设置Zookeeper（Kafka使用Zookeeper保存Broker的元数据），还会介绍Kafka的基本配置，以及如何为Kafka选择合适的硬件，最后介绍如何在一个集群中安装多个Kafka broker，以及把Kafka应用到生产环境要注意的事项。</p>



<h3 id="21-安装jdk和zookeeper">2.1 安装JDK和Zookeeper</h3>

<p>安装Kafka之前，需要先安装JDK和Zookeeper，可以参考我的另一篇文章<a href="https://blog.csdn.net/Geek_ymv/article/details/52003735" rel="nofollow">https://blog.csdn.net/Geek_ymv/article/details/52003735</a></p>



<h3 id="22-安装kafka-broker">2.2 安装Kafka Broker</h3>

<p>可以从<a href="http://kafka.apache.org/downloads" rel="nofollow">http://kafka.apache.org/downloads</a>选择合适的版本安装，本系列文章使用的是kafka_2.10-0.9.0.0版本，Kafka的版本是0.9.0.0，对应Scala版本是2.10。 <br>
将Kafka安装在/usr/local/software目录下。</p>

<ul>
<li>tar -zxvf kafka_2.10-0.9.0.0.tgz -C /usr/local/software/</li>
<li>mkdir -p /opt/data/kafka</li>
<li>cd kafka_2.10-0.9.0.0</li>
<li>vim config/server.properties</li>
<li>bin/kafka-server-start.sh -daemon ./config/server.properties</li>
</ul>

<pre class="prettyprint"><code class="language-java hljs "># Licensed to the Apache Software Foundation (ASF) under one or more
# contributor license agreements.  See the NOTICE file distributed with
# <span class="hljs-keyword">this</span> work <span class="hljs-keyword">for</span> additional information regarding copyright ownership.
# The ASF licenses <span class="hljs-keyword">this</span> file to You under the Apache License, Version <span class="hljs-number">2.0</span>
# (the <span class="hljs-string">"License"</span>); you may not use <span class="hljs-keyword">this</span> file except in compliance with
# the License.  You may obtain a copy of the License at
# 
#    http:<span class="hljs-comment">//www.apache.org/licenses/LICENSE-2.0</span>
# 
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an <span class="hljs-string">"AS IS"</span> BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License <span class="hljs-keyword">for</span> the specific language governing permissions and
# limitations under the License.
# see kafka.server.KafkaConfig <span class="hljs-keyword">for</span> additional details and defaults

############################# Server Basics #############################

# The id of the broker. This must be set to a unique integer <span class="hljs-keyword">for</span> each broker.
broker.id=<span class="hljs-number">0</span>

############################# Socket Server Settings #############################

listeners=PLAINTEXT:<span class="hljs-comment">//:9092</span>

# The port the socket server listens on
port=<span class="hljs-number">9092</span>

# Hostname the broker will bind to. If not set, the server will bind to all interfaces
#host.name=localhost

# Hostname the broker will advertise to producers and consumers. If not set, it uses the
# value <span class="hljs-keyword">for</span> <span class="hljs-string">"host.name"</span> <span class="hljs-keyword">if</span> configured.  Otherwise, it will use the value returned from
# java.net.InetAddress.getCanonicalHostName().
#advertised.host.name=&lt;hostname routable by clients&gt;

# The port to publish to ZooKeeper <span class="hljs-keyword">for</span> clients to use. If <span class="hljs-keyword">this</span> is not set,
# it will publish the same port that the broker binds to.
#advertised.port=&lt;port accessible by clients&gt;

# The number of threads handling network requests
num.network.threads=<span class="hljs-number">3</span>

# The number of threads doing disk I/O
num.io.threads=<span class="hljs-number">8</span>

# The send buffer (SO_SNDBUF) used by the socket server
socket.send.buffer.bytes=<span class="hljs-number">102400</span>

# The receive buffer (SO_RCVBUF) used by the socket server
socket.receive.buffer.bytes=<span class="hljs-number">102400</span>

# The maximum size of a request that the socket server will accept (protection against OOM)
socket.request.max.bytes=<span class="hljs-number">104857600</span>


############################# Log Basics #############################

# A comma seperated list of directories under which to store log files
log.dirs=/opt/data/kafka/kafka-logs

# The <span class="hljs-keyword">default</span> number of log partitions per topic. More partitions allow greater
# parallelism <span class="hljs-keyword">for</span> consumption, but <span class="hljs-keyword">this</span> will also result in more files across
# the brokers.
num.partitions=<span class="hljs-number">1</span>

# The number of threads per data directory to be used <span class="hljs-keyword">for</span> log recovery at startup and flushing at shutdown.
# This value is recommended to be increased <span class="hljs-keyword">for</span> installations with data dirs located in RAID array.
num.recovery.threads.per.data.dir=<span class="hljs-number">1</span>

############################# Log Flush Policy #############################

# Messages are immediately written to the filesystem but by <span class="hljs-keyword">default</span> we only fsync() to sync
# the OS cache lazily. The following configurations control the flush of data to disk. 
# There are a few important trade-offs here:
#    <span class="hljs-number">1.</span> Durability: Unflushed data may be lost <span class="hljs-keyword">if</span> you are not using replication.
#    <span class="hljs-number">2.</span> Latency: Very large flush intervals may lead to latency spikes when the flush does occur as there will be a lot of data to flush.
#    <span class="hljs-number">3.</span> Throughput: The flush is generally the most expensive operation, and a small flush interval may lead to exceessive seeks. 
# The settings below allow one to configure the flush policy to flush data after a period of time or
# every N messages (or both). This can be done globally and overridden on a per-topic basis.

# The number of messages to accept before forcing a flush of data to disk
#log.flush.interval.messages=<span class="hljs-number">10000</span>

# The maximum amount of time a message can sit in a log before we force a flush
#log.flush.interval.ms=<span class="hljs-number">1000</span>

############################# Log Retention Policy #############################

# The following configurations control the disposal of log segments. The policy can
# be set to delete segments after a period of time, or after a given size has accumulated.
# A segment will be deleted whenever *either* of these criteria are met. Deletion always happens
# from the end of the log.

# The minimum age of a log file to be eligible <span class="hljs-keyword">for</span> deletion
log.retention.hours=<span class="hljs-number">168</span>

# A size-based retention policy <span class="hljs-keyword">for</span> logs. Segments are pruned from the log as <span class="hljs-keyword">long</span> as the remaining
# segments don<span class="hljs-string">'t drop below log.retention.bytes.
#log.retention.bytes=1073741824

# The maximum size of a log segment file. When this size is reached a new log segment will be created.
log.segment.bytes=1073741824

# The interval at which log segments are checked to see if they can be deleted according 
# to the retention policies
log.retention.check.interval.ms=300000

# By default the log cleaner is disabled and the log retention policy will default to just delete segments after their retention expires.
# If log.cleaner.enable=true is set the cleaner will be enabled and individual logs can then be marked for log compaction.
log.cleaner.enable=false

############################# Zookeeper #############################

# Zookeeper connection string (see zookeeper docs for details).
# This is a comma separated host:port pairs, each corresponding to a zk
# server. e.g. "127.0.0.1:3000,127.0.0.1:3001,127.0.0.1:3002".
# You can also append an optional chroot string to the urls to specify the
# root directory for all kafka znodes.
zookeeper.connect=node01:2181,node02:2181,node03:2181/kafka

# Timeout in ms for connecting to zookeeper
zookeeper.connection.timeout.ms=6000</span></code></pre>

<p>主要修改了 <br>
log.dirs=/opt/data/kafka/kafka-logs <br>
zookeeper.connect=node01:2181,node02:2181,node03:2181/kafka</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>