---
layout:     post
title:      kafka环境搭建及demo
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<ul>
<li>概述</li>
<li>文件下载</li>
<li>系统环境搭建和配置</li>
<li>kafka demo</li>
<li><p>参考</p>

<ol><li><p>概述</p>

<ul><li>kafka是一个分布式的消息缓存系统</li>
<li>kafka集群中的服务器都叫做broker</li>
<li>kafka有两类客户端，一类叫producer（消息生产者），一类叫做consumer（消息消费者），客户端和broker服务器之间采用tcp协议连接</li>
<li>kafka中不同业务系统的消息可以通过topic进行区分，而且每一个消息topic都会被分区，以分担消息读写的负载</li>
<li>每一个分区都可以有多个副本，以防止数据的丢失</li>
<li>某一个分区中的数据如果需要更新，都必须通过该分区所有副本中的leader来更新</li>
<li>消费者可以分组，比如有两个消费者组A和B，共同消费一个topic：order_info,A和B所消费的消息不会重复 ，比如 order_info 中有100个消息，每个消息有一个id,编号从0-99，那么，如果A组消费0-49号，B组就消费50-99号</li>
<li>消费者在具体消费某个topic中的消息时，可以指定起始偏移量</li></ul></li>
<li><p>文件下载</p>

<ul><li>zookeeper下载 <br>
下载地址：<a href="http://apache.fayea.com/zookeeper/zookeeper-3.3.6/zookeeper-3.3.6.tar.gz" rel="nofollow" target="_blank">http://apache.fayea.com/zookeeper/zookeeper-3.3.6/zookeeper-3.3.6.tar.gz</a></li>
<li>kafka下载 <br>
地址：<a href="https://mirrors.tuna.tsinghua.edu.cn/apache/kafka/0.11.0.0/kafka_2.11-0.11.0.0.tgz" rel="nofollow" target="_blank">https://mirrors.tuna.tsinghua.edu.cn/apache/kafka/0.11.0.0/kafka_2.11-0.11.0.0.tgz</a></li></ul></li>
<li><p>系统环境搭建和配置</p>

<ul><li>java7配置省略</li>
<li>zookeeper配置（此处略，参考：<a href="http://blog.csdn.net/lxf20054658/article/details/74452755" rel="nofollow" target="_blank">http://blog.csdn.net/lxf20054658/article/details/74452755</a>）</li>
<li>kafka配置</li>
<li>上传kafka_2.11-0.11.0.0.tgz到centos服务器</li>
<li>解压，tar -zxvf kafka_2.11-0.11.0.0.tgz，mv kafka_2.11-0.11.0.0 /usr/lib/apache-kafka</li>
<li>配置，cd /usr/lib/apache-kafka/config，vi server.properties，配置示例（参考颜色标注的地方即可）：</li></ul></li></ol></li>
</ul>



<pre class="prettyprint"><code class=" hljs vhdl"># Licensed <span class="hljs-keyword">to</span> the Apache Software Foundation (ASF) under one <span class="hljs-keyword">or</span> more
# contributor license agreements.  See the NOTICE <span class="hljs-keyword">file</span> distributed <span class="hljs-keyword">with</span>
# this work <span class="hljs-keyword">for</span> additional information regarding copyright ownership.
# The ASF licenses this <span class="hljs-keyword">file</span> <span class="hljs-keyword">to</span> You under the Apache License, Version <span class="hljs-number">2.0</span>
# (the <span class="hljs-string">"License"</span>); you may <span class="hljs-keyword">not</span> <span class="hljs-keyword">use</span> this <span class="hljs-keyword">file</span> except <span class="hljs-keyword">in</span> compliance <span class="hljs-keyword">with</span>
# the License.  You may obtain a copy <span class="hljs-keyword">of</span> the License at
#
#    http://www.apache.org/licenses/LICENSE-<span class="hljs-number">2.0</span>
#
# Unless required by applicable law <span class="hljs-keyword">or</span> agreed <span class="hljs-keyword">to</span> <span class="hljs-keyword">in</span> writing, software
# distributed under the License <span class="hljs-keyword">is</span> distributed <span class="hljs-keyword">on</span> an <span class="hljs-string">"AS IS"</span> BASIS,
# WITHOUT WARRANTIES <span class="hljs-keyword">OR</span> CONDITIONS <span class="hljs-keyword">OF</span> ANY KIND, either express <span class="hljs-keyword">or</span> implied.
# See the License <span class="hljs-keyword">for</span> the specific language governing permissions <span class="hljs-keyword">and</span>
# limitations under the License.

# see kafka.server.KafkaConfig <span class="hljs-keyword">for</span> additional details <span class="hljs-keyword">and</span> defaults

############################# Server Basics #############################

# The id <span class="hljs-keyword">of</span> the broker. This must be set <span class="hljs-keyword">to</span> a unique <span class="hljs-typename">integer</span> <span class="hljs-keyword">for</span> each broker.
broker.id=<span class="hljs-number">1</span>

# Switch <span class="hljs-keyword">to</span> enable topic deletion <span class="hljs-keyword">or</span> <span class="hljs-keyword">not</span>, <span class="hljs-keyword">default</span> value <span class="hljs-keyword">is</span> false
#delete.topic.enable=true

############################# Socket Server Settings #############################

# The address the socket server listens <span class="hljs-keyword">on</span>. It will get the value returned from
# java.net.InetAddress.getCanonicalHostName() <span class="hljs-keyword">if</span> <span class="hljs-keyword">not</span> configured.
#   FORMAT:
#     listeners = listener_name://host_name:<span class="hljs-keyword">port</span>
#   EXAMPLE:
#     listeners = PLAINTEXT://your.host.name:<span class="hljs-number">9092</span>
#listeners=PLAINTEXT://:<span class="hljs-number">9092</span>

# Hostname <span class="hljs-keyword">and</span> <span class="hljs-keyword">port</span> the broker will advertise <span class="hljs-keyword">to</span> producers <span class="hljs-keyword">and</span> consumers. <span class="hljs-keyword">If</span> <span class="hljs-keyword">not</span> set,
# it uses the value <span class="hljs-keyword">for</span> <span class="hljs-string">"listeners"</span> <span class="hljs-keyword">if</span> configured.  Otherwise, it will <span class="hljs-keyword">use</span> the value
# returned from java.net.InetAddress.getCanonicalHostName().
#advertised.listeners=PLAINTEXT://your.host.name:<span class="hljs-number">9092</span>

# Maps listener names <span class="hljs-keyword">to</span> security protocols, the <span class="hljs-keyword">default</span> <span class="hljs-keyword">is</span> <span class="hljs-keyword">for</span> them <span class="hljs-keyword">to</span> be the same. See the config documentation <span class="hljs-keyword">for</span> more details
#listener.security.protocol.<span class="hljs-keyword">map</span>=PLAINTEXT:PLAINTEXT,SSL:SSL,SASL_PLAINTEXT:SASL_PLAINTEXT,SASL_SSL:SASL_SSL

# The number <span class="hljs-keyword">of</span> threads that the server uses <span class="hljs-keyword">for</span> receiving requests from the network <span class="hljs-keyword">and</span> sending responses <span class="hljs-keyword">to</span> the network
num.network.threads=<span class="hljs-number">3</span>

# The number <span class="hljs-keyword">of</span> threads that the server uses <span class="hljs-keyword">for</span> processing requests, which may include disk I/O
num.io.threads=<span class="hljs-number">8</span>
# The send <span class="hljs-keyword">buffer</span> (SO_SNDBUF) used by the socket server
socket.send.<span class="hljs-keyword">buffer</span>.bytes=<span class="hljs-number">102400</span>

# The receive <span class="hljs-keyword">buffer</span> (SO_RCVBUF) used by the socket server
socket.receive.<span class="hljs-keyword">buffer</span>.bytes=<span class="hljs-number">102400</span>

# The maximum size <span class="hljs-keyword">of</span> a request that the socket server will accept (protection against OOM)
socket.request.max.bytes=<span class="hljs-number">104857600</span>


############################# Log Basics #############################

# A comma seperated list <span class="hljs-keyword">of</span> directories under which <span class="hljs-keyword">to</span> store log files
log.dirs=/tmp/kafka-logs

# The <span class="hljs-keyword">default</span> number <span class="hljs-keyword">of</span> log partitions per topic. More partitions allow greater
# parallelism <span class="hljs-keyword">for</span> consumption, but this will also result <span class="hljs-keyword">in</span> more files across
# the brokers.
num.partitions=<span class="hljs-number">1</span>

# The number <span class="hljs-keyword">of</span> threads per data directory <span class="hljs-keyword">to</span> be used <span class="hljs-keyword">for</span> log recovery at startup <span class="hljs-keyword">and</span> flushing at shutdown.
# This value <span class="hljs-keyword">is</span> recommended <span class="hljs-keyword">to</span> be increased <span class="hljs-keyword">for</span> installations <span class="hljs-keyword">with</span> data dirs located <span class="hljs-keyword">in</span> RAID <span class="hljs-keyword">array</span>.
num.recovery.threads.per.data.dir=<span class="hljs-number">1</span>

############################# Internal Topic Settings  #############################
# The replication factor <span class="hljs-keyword">for</span> the <span class="hljs-keyword">group</span> metadata internal topics <span class="hljs-string">"__consumer_offsets"</span> <span class="hljs-keyword">and</span> <span class="hljs-string">"__transaction_state"</span>
# <span class="hljs-keyword">For</span> anything other than development testing, a value greater than <span class="hljs-number">1</span> <span class="hljs-keyword">is</span> recommended <span class="hljs-keyword">for</span> <span class="hljs-keyword">to</span> ensure availability such as <span class="hljs-number">3.</span>
offsets.topic.replication.factor=<span class="hljs-number">1</span>
transaction.state.log.replication.factor=<span class="hljs-number">1</span>
transaction.state.log.min.isr=<span class="hljs-number">1</span>

############################# Log Flush Policy #############################

# Messages are immediately written <span class="hljs-keyword">to</span> the filesystem but by <span class="hljs-keyword">default</span> we only fsync() <span class="hljs-keyword">to</span> sync
# the OS cache lazily. The following configurations control the flush <span class="hljs-keyword">of</span> data <span class="hljs-keyword">to</span> disk.
# There are a few important trade-offs here:
#    <span class="hljs-number">1.</span> Durability: Unflushed data may be lost <span class="hljs-keyword">if</span> you are <span class="hljs-keyword">not</span> using replication.
#    <span class="hljs-number">2.</span> Latency: Very large flush intervals may lead <span class="hljs-keyword">to</span> latency spikes <span class="hljs-keyword">when</span> the flush does occur as there will be a lot <span class="hljs-keyword">of</span> data <span class="hljs-keyword">to</span> flush.
#    <span class="hljs-number">3.</span> Throughput: The flush <span class="hljs-keyword">is</span> generally the most expensive operation, <span class="hljs-keyword">and</span> a small flush interval may lead <span class="hljs-keyword">to</span> exceessive seeks.
# The settings below allow one <span class="hljs-keyword">to</span> configure the flush policy <span class="hljs-keyword">to</span> flush data <span class="hljs-keyword">after</span> a period <span class="hljs-keyword">of</span> <span class="hljs-typename">time</span> <span class="hljs-keyword">or</span>
# every N messages (<span class="hljs-keyword">or</span> both). This can be done globally <span class="hljs-keyword">and</span> overridden <span class="hljs-keyword">on</span> a per-topic basis.

# The number <span class="hljs-keyword">of</span> messages <span class="hljs-keyword">to</span> accept before forcing a flush <span class="hljs-keyword">of</span> data <span class="hljs-keyword">to</span> disk
#log.flush.interval.messages=<span class="hljs-number">10000</span>

# The maximum amount <span class="hljs-keyword">of</span> <span class="hljs-typename">time</span> a message can sit <span class="hljs-keyword">in</span> a log before we <span class="hljs-keyword">force</span> a flush
#log.flush.interval.ms=<span class="hljs-number">1000</span>

############################# Log Retention Policy #############################

# The following configurations control the disposal <span class="hljs-keyword">of</span> log segments. The policy can
# be set <span class="hljs-keyword">to</span> delete segments <span class="hljs-keyword">after</span> a period <span class="hljs-keyword">of</span> <span class="hljs-typename">time</span>, <span class="hljs-keyword">or</span> <span class="hljs-keyword">after</span> a given size has accumulated.
# A segment will be deleted whenever *either* <span class="hljs-keyword">of</span> these criteria are met. Deletion always happens
# from the <span class="hljs-keyword">end</span> <span class="hljs-keyword">of</span> the log.

# The minimum age <span class="hljs-keyword">of</span> a log <span class="hljs-keyword">file</span> <span class="hljs-keyword">to</span> be eligible <span class="hljs-keyword">for</span> deletion due <span class="hljs-keyword">to</span> age
log.retention.hours=<span class="hljs-number">168</span>

# A size-based retention policy <span class="hljs-keyword">for</span> logs. Segments are pruned from the log as long as the remaining
# segments don<span class="hljs-attribute">'t</span> drop below log.retention.bytes. Functions independently <span class="hljs-keyword">of</span> log.retention.hours.
#log.retention.bytes=<span class="hljs-number">1073741824</span>

# The maximum size <span class="hljs-keyword">of</span> a log segment <span class="hljs-keyword">file</span>. <span class="hljs-keyword">When</span> this size <span class="hljs-keyword">is</span> reached a <span class="hljs-keyword">new</span> log segment will be created.
log.segment.bytes=<span class="hljs-number">1073741824</span>
# The interval at which log segments are checked <span class="hljs-keyword">to</span> see <span class="hljs-keyword">if</span> they can be deleted according
# <span class="hljs-keyword">to</span> the retention policies
log.retention.check.interval.ms=<span class="hljs-number">300000</span>

############################# Zookeeper #############################

# Zookeeper connection <span class="hljs-typename">string</span> (see zookeeper docs <span class="hljs-keyword">for</span> details).
# This <span class="hljs-keyword">is</span> a comma separated host:<span class="hljs-keyword">port</span> pairs, each corresponding <span class="hljs-keyword">to</span> a zk
# server. e.g. <span class="hljs-string">"127.0.0.1:3000,127.0.0.1:3001,127.0.0.1:3002"</span>.
# You can also append an optional chroot <span class="hljs-typename">string</span> <span class="hljs-keyword">to</span> the urls <span class="hljs-keyword">to</span> specify the
# root directory <span class="hljs-keyword">for</span> <span class="hljs-keyword">all</span> kafka znodes.
zookeeper.connect=hadooplearn:<span class="hljs-number">2181</span>

# Timeout <span class="hljs-keyword">in</span> ms <span class="hljs-keyword">for</span> connecting <span class="hljs-keyword">to</span> zookeeper
zookeeper.connection.timeout.ms=<span class="hljs-number">6000</span>


############################# <span class="hljs-keyword">Group</span> Coordinator Settings #############################

# The following <span class="hljs-keyword">configuration</span> specifies the <span class="hljs-typename">time</span>, <span class="hljs-keyword">in</span> milliseconds, that the GroupCoordinator will delay the initial consumer rebalance.
# The rebalance will be further delayed by the value <span class="hljs-keyword">of</span> <span class="hljs-keyword">group</span>.initial.rebalance.delay.ms as <span class="hljs-keyword">new</span> members join the <span class="hljs-keyword">group</span>, up <span class="hljs-keyword">to</span> a maximum <span class="hljs-keyword">of</span> max.poll.interval.ms.
# The <span class="hljs-keyword">default</span> value <span class="hljs-keyword">for</span> this <span class="hljs-keyword">is</span> <span class="hljs-number">3</span> seconds.
# We override this <span class="hljs-keyword">to</span> <span class="hljs-number">0</span> here as it makes <span class="hljs-keyword">for</span> a better <span class="hljs-keyword">out</span>-<span class="hljs-keyword">of</span>-the-box experience <span class="hljs-keyword">for</span> development <span class="hljs-keyword">and</span> testing.
# However, <span class="hljs-keyword">in</span> production environments the <span class="hljs-keyword">default</span> value <span class="hljs-keyword">of</span> <span class="hljs-number">3</span> seconds <span class="hljs-keyword">is</span> more suitable as this will help <span class="hljs-keyword">to</span> avoid unnecessary, <span class="hljs-keyword">and</span> potentially expensive, rebalances during application startup.
<span class="hljs-keyword">group</span>.initial.rebalance.delay.ms=<span class="hljs-number">0</span></code></pre>

<pre><code>    * 启动broker
</code></pre>

<p>bin/kafka-server-start.sh config/server.properties</p>

<pre><code>    * 在kafka集群中创建一个topic
</code></pre>

<p>bin/kafka-topics.sh –create –zookeeper hadooplearn:2181 –replication-factor 1 –partitions 1 –topic order</p>

<pre><code>    * 查看topic
</code></pre>

<p>bin/kafka-topics.sh –list –zookeeper hadooplearn:2181</p>

<pre><code>    * 用一个producer向某一个topic中写入消息（往话题order写消息）
</code></pre>

<p>bin/kafka-console-producer.sh –broker-list hadooplearn:9092 –topic order</p>

<pre><code>    * 用一个comsumer从某一个topic中读取信息
</code></pre>

<p>bin/kafka-console-consumer.sh –zookeeper hadooplearn:2181 –from-beginning –topic order</p>

<pre><code>    * 查看一个topic的分区及副本状态信息
</code></pre>

<p>bin/kafka-topics.sh –describe –zookeeper hadooplearn:2181 –topic order</p>

<pre><code>1. kafka demo
</code></pre>

<p>注：基于kafka_2.11-0.11.0.0.tgz包 <br>
生产者：</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">package</span> cn.itcast.kafka;
<span class="hljs-keyword">import</span> java.util.Properties;
<span class="hljs-keyword">import</span> org.apache.kafka.clients.producer.KafkaProducer;
<span class="hljs-keyword">import</span> org.apache.kafka.clients.producer.Producer;
<span class="hljs-keyword">import</span> org.apache.kafka.clients.producer.ProducerRecord;

<span class="hljs-javadoc">/**
 * see http://kafka.apache.org/090/javadoc/index.html?org/apache/kafka/clients/producer/KafkaProducer.html 
 *<span class="hljs-javadoctag"> @author</span> xfli
 *
 */</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">ProducerDemo</span> {</span>
      <span class="hljs-javadoc">/**
       *<span class="hljs-javadoctag"> @param</span> args
       */</span>
      <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) {
            Properties props = <span class="hljs-keyword">new</span> Properties();
            props.put(<span class="hljs-string">"bootstrap.servers"</span>, <span class="hljs-string">"hadooplearn:9092"</span>);
            props.put(<span class="hljs-string">"metadata.broker.list"</span>,<span class="hljs-string">"hadooplearn:9092"</span>);
            props.put(<span class="hljs-string">"key.serializer"</span>, <span class="hljs-string">"org.apache.kafka.common.serialization.StringSerializer"</span>);
            props.put(<span class="hljs-string">"value.serializer"</span>, <span class="hljs-string">"org.apache.kafka.common.serialization.StringSerializer"</span>);
            Producer&lt;String, String&gt; producer = <span class="hljs-keyword">new</span> KafkaProducer&lt;&gt;(props);
            <span class="hljs-comment">// 发送业务消息</span>
            <span class="hljs-comment">// 读取文件 读取内存数据库 读socket端口</span>
            <span class="hljs-keyword">for</span> (<span class="hljs-keyword">int</span> i = <span class="hljs-number">1</span>; i &lt;= <span class="hljs-number">100</span>; i++) {
                  <span class="hljs-keyword">try</span> {
                        Thread.sleep(<span class="hljs-number">500</span>);
                  } <span class="hljs-keyword">catch</span> (InterruptedException e) {
                        e.printStackTrace();
                  }
                  producer.send(<span class="hljs-keyword">new</span> ProducerRecord&lt;String, String&gt;(<span class="hljs-string">"wordcount"</span>,
                              i+<span class="hljs-string">" said "</span>+ i + <span class="hljs-string">" love you baby for "</span> + i + <span class="hljs-string">" times,will you have a nice day with me tomorrow"</span>));
            }
      }
}</code></pre>

<p>消费者：</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">package</span> cn.itcast.kafka;

<span class="hljs-keyword">import</span> java.util.Arrays;
<span class="hljs-keyword">import</span> java.util.Properties;
<span class="hljs-keyword">import</span> java.util.concurrent.atomic.AtomicBoolean;
<span class="hljs-keyword">import</span> org.apache.kafka.clients.consumer.ConsumerRecord;
<span class="hljs-keyword">import</span> org.apache.kafka.clients.consumer.ConsumerRecords;
<span class="hljs-keyword">import</span> org.apache.kafka.clients.consumer.KafkaConsumer;
<span class="hljs-keyword">import</span> org.apache.kafka.common.errors.WakeupException;

<span class="hljs-javadoc">/**
 * see http://kafka.apache.org/090/javadoc/index.html?org/apache/kafka/clients/consumer/KafkaConsumer.html
 * consumer/KafkaConsumer.html
 *
 *<span class="hljs-javadoctag"> @author</span> xfli
 *
 */</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">ConsumerDemo</span> <span class="hljs-keyword">implements</span> <span class="hljs-title">Runnable</span> {</span>
      <span class="hljs-keyword">private</span> <span class="hljs-keyword">final</span> AtomicBoolean closed = <span class="hljs-keyword">new</span> AtomicBoolean(<span class="hljs-keyword">false</span>);
      KafkaConsumer&lt;String, String&gt; consumer;<span class="hljs-comment">// = new KafkaConsumer&lt;&gt;(props);</span>

      <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">run</span>() {
            <span class="hljs-keyword">try</span> {
                  Properties props = <span class="hljs-keyword">new</span> Properties();
                  props.put(<span class="hljs-string">"bootstrap.servers"</span>, <span class="hljs-string">"hadooplearn:9092"</span>);
                  props.put(<span class="hljs-string">"group.id"</span>, <span class="hljs-string">"test"</span>);
                  props.put(<span class="hljs-string">"enable.auto.commit"</span>, <span class="hljs-string">"true"</span>);
                  props.put(<span class="hljs-string">"auto.commit.interval.ms"</span>, <span class="hljs-string">"1000"</span>);
                  props.put(<span class="hljs-string">"session.timeout.ms"</span>, <span class="hljs-string">"30000"</span>);
                  props.put(<span class="hljs-string">"key.deserializer"</span>,
                              <span class="hljs-string">"org.apache.kafka.common.serialization.StringDeserializer"</span>);
                  props.put(<span class="hljs-string">"value.deserializer"</span>,
                              <span class="hljs-string">"org.apache.kafka.common.serialization.StringDeserializer"</span>);
                  consumer = <span class="hljs-keyword">new</span> KafkaConsumer&lt;&gt;(props);
                  consumer.subscribe(Arrays.asList(<span class="hljs-string">"wordcount"</span>));
                  <span class="hljs-keyword">while</span> (!closed.get()) {
                        ConsumerRecords&lt;String, String&gt; records = consumer.poll(<span class="hljs-number">10000</span>);
                        <span class="hljs-comment">// Handle new records</span>
                        <span class="hljs-keyword">for</span> (<span class="hljs-keyword">final</span> ConsumerRecord&lt;String, String&gt; rc : records) {
                              System.out.println(<span class="hljs-string">"msg="</span> + rc.value());
                        }
                        <span class="hljs-keyword">try</span> {
                              Thread.sleep(<span class="hljs-number">500</span>);
                        } <span class="hljs-keyword">catch</span> (InterruptedException e) {
                              <span class="hljs-comment">// TODO Auto-generated catch block</span>
                              e.printStackTrace();
                        }
                  }
            } <span class="hljs-keyword">catch</span> (WakeupException e) {
                  <span class="hljs-comment">// Ignore exception if closing</span>
                  <span class="hljs-keyword">if</span> (!closed.get())
                        <span class="hljs-keyword">throw</span> e;
            } <span class="hljs-keyword">finally</span> {
                  consumer.close();
            }
      }

      <span class="hljs-comment">// Shutdown hook which can be called from a separate thread</span>
      <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">shutdown</span>() {
            closed.set(<span class="hljs-keyword">true</span>);
            consumer.wakeup();
      }

      <span class="hljs-javadoc">/**
       *<span class="hljs-javadoctag"> @param</span> args
       */</span>
      <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) {
            <span class="hljs-comment">// TODO Auto-generated method stub</span>
            ConsumerDemo sub1 = <span class="hljs-keyword">new</span> ConsumerDemo();
            Thread tsub1 = <span class="hljs-keyword">new</span> Thread(sub1);
            tsub1.start();
      }

}</code></pre>

<p>运行结果示例：</p>



<pre class="prettyprint"><code class=" hljs applescript">msg=<span class="hljs-number">56</span> said <span class="hljs-number">56</span> love you baby <span class="hljs-keyword">for</span> <span class="hljs-number">56</span> <span class="hljs-keyword">times</span>,will you have a nice <span class="hljs-property">day</span> <span class="hljs-keyword">with</span> <span class="hljs-keyword">me</span> tomorrow
msg=<span class="hljs-number">57</span> said <span class="hljs-number">57</span> love you baby <span class="hljs-keyword">for</span> <span class="hljs-number">57</span> <span class="hljs-keyword">times</span>,will you have a nice <span class="hljs-property">day</span> <span class="hljs-keyword">with</span> <span class="hljs-keyword">me</span> tomorrow
msg=<span class="hljs-number">58</span> said <span class="hljs-number">58</span> love you baby <span class="hljs-keyword">for</span> <span class="hljs-number">58</span> <span class="hljs-keyword">times</span>,will you have a nice <span class="hljs-property">day</span> <span class="hljs-keyword">with</span> <span class="hljs-keyword">me</span> tomorrow
msg=<span class="hljs-number">59</span> said <span class="hljs-number">59</span> love you baby <span class="hljs-keyword">for</span> <span class="hljs-number">59</span> <span class="hljs-keyword">times</span>,will you have a nice <span class="hljs-property">day</span> <span class="hljs-keyword">with</span> <span class="hljs-keyword">me</span> tomorrow
msg=<span class="hljs-number">60</span> said <span class="hljs-number">60</span> love you baby <span class="hljs-keyword">for</span> <span class="hljs-number">60</span> <span class="hljs-keyword">times</span>,will you have a nice <span class="hljs-property">day</span> <span class="hljs-keyword">with</span> <span class="hljs-keyword">me</span> tomorrow
msg=<span class="hljs-number">61</span> said <span class="hljs-number">61</span> love you baby <span class="hljs-keyword">for</span> <span class="hljs-number">61</span> <span class="hljs-keyword">times</span>,will you have a nice <span class="hljs-property">day</span> <span class="hljs-keyword">with</span> <span class="hljs-keyword">me</span> tomorrow
msg=<span class="hljs-number">62</span> said <span class="hljs-number">62</span> love you baby <span class="hljs-keyword">for</span> <span class="hljs-number">62</span> <span class="hljs-keyword">times</span>,will you have a nice <span class="hljs-property">day</span> <span class="hljs-keyword">with</span> <span class="hljs-keyword">me</span> tomorrow
msg=<span class="hljs-number">63</span> said <span class="hljs-number">63</span> love you baby <span class="hljs-keyword">for</span> <span class="hljs-number">63</span> <span class="hljs-keyword">times</span>,will you have a nice <span class="hljs-property">day</span> <span class="hljs-keyword">with</span> <span class="hljs-keyword">me</span> tomorrow
msg=<span class="hljs-number">64</span> said <span class="hljs-number">64</span> love you baby <span class="hljs-keyword">for</span> <span class="hljs-number">64</span> <span class="hljs-keyword">times</span>,will you have a nice <span class="hljs-property">day</span> <span class="hljs-keyword">with</span> <span class="hljs-keyword">me</span> tomorrow
msg=<span class="hljs-number">65</span> said <span class="hljs-number">65</span> love you baby <span class="hljs-keyword">for</span> <span class="hljs-number">65</span> <span class="hljs-keyword">times</span>,will you have a nice <span class="hljs-property">day</span> <span class="hljs-keyword">with</span> <span class="hljs-keyword">me</span> tomorrow</code></pre>

<p>参考： <br>
<a href="http://kafka.apache.org/090/javadoc/index.html?org/apache/kafka/clients/producer/KafkaProducer.html" rel="nofollow">http://kafka.apache.org/090/javadoc/index.html?org/apache/kafka/clients/producer/KafkaProducer.html</a> </p>

<p><a href="http://kafka.apache.org/090/javadoc/index.html?org/apache/kafka/clients/consumer/KafkaConsumer.html" rel="nofollow">http://kafka.apache.org/090/javadoc/index.html?org/apache/kafka/clients/consumer/KafkaConsumer.html</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>