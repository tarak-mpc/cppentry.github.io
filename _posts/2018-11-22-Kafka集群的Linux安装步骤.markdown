---
layout:     post
title:      Kafka集群的Linux安装步骤
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/gavinguo1987/article/details/77315134				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p> </p>
<p><strong>一、  环境准备</strong></p>
<p><span style="color:#333333;">Kafka</span><span style="color:#333333;">依赖ZooKeeper</span>，首先需要确保ZooKeeper服务已经搭建完成。</p>
<p><strong> </strong></p>
<p><strong>操作系统: </strong></p>
<p><strong>CentOS-7-x86_64-1611</strong></p>
<p><strong> </strong></p>
<p><strong>节点IP端口:</strong></p>
<p><strong>192.168.2.200:9092</strong></p>
<p><strong>192.168.2.201:9092</strong></p>
<p><strong>192.168.2.202:9092</strong></p>
<p align="left"><strong> </strong></p>
<p><strong>二、  Kafka安装</strong></p>
<p align="left"><strong> </strong></p>
<p align="left"><span style="color:#333333;">1. </span><span style="color:rgb(51,51,51);">下载Kafka</span></p>
<p><span style="color:rgb(51,51,51);">Kafka</span><span style="color:rgb(51,51,51);">官网</span><span style="color:rgb(51,51,51);">地址</span><span style="color:rgb(51,51,51);">：</span><a href="http://kafka.apache.org/" rel="nofollow">http://kafka.apache.org/</a></p>
<p> </p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p><strong>cd /usr/local </strong></p>
<p><strong>wget https://www.apache.org/dyn/closer.cgi?path=/kafka/0.11.0.0/kafka_2.11-0.11.0.0.tgz</strong></p>
</td>
</tr></tbody></table><p align="left"><strong> </strong></p>
<p align="left"><strong>2.  解压</strong></p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p><strong>cd /usr/local</strong></p>
<p><strong>tar -zxvf kafka_2.11-0.11.0.0.tgz</strong></p>
<p><strong>ls</strong></p>
</td>
</tr></tbody></table><p align="left"><strong> </strong></p>
<p><strong>三、  Kafka集群配置</strong></p>
<p><strong> </strong></p>
<p align="left"><strong>1.  修改server.properties配置文件</strong></p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p><strong>cd /usr/local/kafka_2.11-0.11.0.0/config/</strong></p>
<p><strong>vi server.properties</strong></p>
</td>
</tr></tbody></table><p><strong>server.properties修改后文件内容</strong></p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p><span style="color:#00B400;">############################# Server Basics #############################</span></p>
<p> </p>
<p><span style="color:#00B400;"># The id of the broker. This must be set to a unique integer for each broker.</span></p>
<p><span style="color:#000080;">broker.id</span><span style="color:#008000;">=</span><span style="color:#800000;">0</span></p>
<p> </p>
<p><span style="color:#00B400;"># Switch to enable topic deletion or not, default value is false</span></p>
<p><span style="color:#000080;">delete.topic.enable</span><span style="color:#008000;">=</span><span style="color:#800000;">true</span></p>
<p> </p>
<p><span style="color:#00B400;">############################# Socket Server Settings #############################</span></p>
<p> </p>
<p><span style="color:#00B400;"># The address the socket server listens on. It will get the value returned from
</span></p>
<p><span style="color:#00B400;"># java.net.InetAddress.getCanonicalHostName() if not configured.</span></p>
<p><span style="color:#00B400;">#   FORMAT:</span></p>
<p><span style="color:#00B400;">#     listeners = listener_name://host_name:port</span></p>
<p><span style="color:#00B400;">#   EXAMPLE:</span></p>
<p><span style="color:#00B400;">#     listeners = PLAINTEXT://your.host.name:9092</span></p>
<p><span style="color:#000080;">listeners</span><span style="color:#008000;">=</span><span style="color:#800000;">PLAINTEXT://192.168.2.200:9092</span></p>
<p> </p>
<p><span style="color:#00B400;"># Hostname and port the broker will advertise to producers and consumers. If not set,
</span></p>
<p><span style="color:#00B400;"># it uses the value for "listeners" if configured.  Otherwise, it will use the value</span></p>
<p><span style="color:#00B400;"># returned from java.net.InetAddress.getCanonicalHostName().</span></p>
<p><span style="color:#00B400;">#advertised.listeners=PLAINTEXT://your.host.name:9092</span></p>
<p> </p>
<p><span style="color:#00B400;"># Maps listener names to security protocols, the default is for them to be the same. See the config documentation for more details</span></p>
<p><span style="color:#00B400;">#listener.security.protocol.map=PLAINTEXT:PLAINTEXT,SSL:SSL,SASL_PLAINTEXT:SASL_PLAINTEXT,SASL_SSL:SASL_SSL</span></p>
<p> </p>
<p><span style="color:#00B400;"># The number of threads that the server uses for receiving requests from the network and sending responses to the network</span></p>
<p><span style="color:#000080;">num.network.threads</span><span style="color:#008000;">=</span><span style="color:#800000;">3</span></p>
<p> </p>
<p><span style="color:#00B400;"># The number of threads that the server uses for processing requests, which may include disk I/O</span></p>
<p><span style="color:#000080;">num.io.threads</span><span style="color:#008000;">=</span><span style="color:#800000;">8</span></p>
<p> </p>
<p><span style="color:#00B400;"># The send buffer (SO_SNDBUF) used by the socket server</span></p>
<p><span style="color:#000080;">socket.send.buffer.bytes</span><span style="color:#008000;">=</span><span style="color:#800000;">1048576</span></p>
<p> </p>
<p><span style="color:#00B400;"># The receive buffer (SO_RCVBUF) used by the socket server</span></p>
<p><span style="color:#000080;">socket.receive.buffer.bytes</span><span style="color:#008000;">=</span><span style="color:#800000;">1048576</span></p>
<p> </p>
<p><span style="color:#00B400;"># The maximum size of a request that the socket server will accept (protection against OOM)</span></p>
<p><span style="color:#000080;">socket.request.max.bytes</span><span style="color:#008000;">=</span><span style="color:#800000;">104857600</span></p>
<p> </p>
<p> </p>
<p><span style="color:#00B400;">############################# Log Basics #############################</span></p>
<p> </p>
<p><span style="color:#00B400;"># A comma seperated list of directories under which to store log files</span></p>
<p><span style="color:#000080;">log.dirs</span><span style="color:#008000;">=</span><span style="color:#800000;">/usr/local/kafka_2.11-0.11.0.0/kafka-logs</span></p>
<p> </p>
<p><span style="color:#00B400;"># The default number of log partitions per topic. More partitions allow greater</span></p>
<p><span style="color:#00B400;"># parallelism for consumption, but this will also result in more files across</span></p>
<p><span style="color:#00B400;"># the brokers.</span></p>
<p><span style="color:#000080;">num.partitions</span><span style="color:#008000;">=</span><span style="color:#800000;">1</span></p>
<p> </p>
<p><span style="color:#00B400;"># The number of threads per data directory to be used for log recovery at startup and flushing at shutdown.</span></p>
<p><span style="color:#00B400;"># This value is recommended to be increased for installations with data dirs located in RAID array.</span></p>
<p><span style="color:#000080;">num.recovery.threads.per.data.dir</span><span style="color:#008000;">=</span><span style="color:#800000;">1</span></p>
<p> </p>
<p><span style="color:#00B400;">############################# Internal Topic Settings  #############################</span></p>
<p><span style="color:#00B400;"># The replication factor for the group metadata internal topics "__consumer_offsets" and "__transaction_state"</span></p>
<p><span style="color:#00B400;"># For anything other than development testing, a value greater than 1 is recommended for to ensure availability such as 3.</span></p>
<p><span style="color:#000080;">offsets.topic.replication.factor</span><span style="color:#008000;">=</span><span style="color:#800000;">1</span></p>
<p><span style="color:#000080;">transaction.state.log.replication.factor</span><span style="color:#008000;">=</span><span style="color:#800000;">1</span></p>
<p><span style="color:#000080;">transaction.state.log.min.isr</span><span style="color:#008000;">=</span><span style="color:#800000;">1</span></p>
<p> </p>
<p><span style="color:#00B400;">############################# Log Flush Policy #############################</span></p>
<p> </p>
<p><span style="color:#00B400;"># Messages are immediately written to the filesystem but by default we only fsync() to sync</span></p>
<p><span style="color:#00B400;"># the OS cache lazily. The following configurations control the flush of data to disk.</span></p>
<p><span style="color:#00B400;"># There are a few important trade-offs here:</span></p>
<p><span style="color:#00B400;">#    1. Durability: Unflushed data may be lost if you are not using replication.</span></p>
<p><span style="color:#00B400;">#    2. Latency: Very large flush intervals may lead to latency spikes when the flush does occur as there will be a lot of data to flush.</span></p>
<p><span style="color:#00B400;">#    3. Throughput: The flush is generally the most expensive operation, and a small flush interval may lead to exceessive seeks.</span></p>
<p><span style="color:#00B400;"># The settings below allow one to configure the flush policy to flush data after a period of time or</span></p>
<p><span style="color:#00B400;"># every N messages (or both). This can be done globally and overridden on a per-topic basis.</span></p>
<p> </p>
<p><span style="color:#00B400;"># The number of messages to accept before forcing a flush of data to disk</span></p>
<p><span style="color:#000080;">log.flush.interval.messages</span><span style="color:#008000;">=</span><span style="color:#800000;">20000</span></p>
<p> </p>
<p><span style="color:#00B400;"># The maximum amount of time a message can sit in a log before we force a flush</span></p>
<p><span style="color:#000080;">log.flush.interval.ms</span><span style="color:#008000;">=</span><span style="color:#800000;">10000</span></p>
<p> </p>
<p><span style="color:#00B400;">############################# Log Retention Policy #############################</span></p>
<p> </p>
<p><span style="color:#00B400;"># The following configurations control the disposal of log segments. The policy can</span></p>
<p><span style="color:#00B400;"># be set to delete segments after a period of time, or after a given size has accumulated.</span></p>
<p><span style="color:#00B400;"># A segment will be deleted whenever *either* of these criteria are met. Deletion always happens</span></p>
<p><span style="color:#00B400;"># from the end of the log.</span></p>
<p> </p>
<p><span style="color:#00B400;"># The minimum age of a log file to be eligible for deletion due to age</span></p>
<p><span style="color:#000080;">log.retention.hours</span><span style="color:#008000;">=</span><span style="color:#800000;">168</span></p>
<p> </p>
<p><span style="color:#00B400;"># A size-based retention policy for logs. Segments are pruned from the log as long as the remaining</span></p>
<p><span style="color:#00B400;"># segments don't drop below log.retention.bytes. Functions independently of log.retention.hours.</span></p>
<p><span style="color:#00B400;">#log.retention.bytes=1073741824</span></p>
<p> </p>
<p><span style="color:#00B400;"># The maximum size of a log segment file. When this size is reached a new log segment will be created.</span></p>
<p><span style="color:#000080;">log.segment.bytes</span><span style="color:#008000;">=</span><span style="color:#800000;">1073741824</span></p>
<p> </p>
<p><span style="color:#00B400;"># The interval at which log segments are checked to see if they can be deleted according</span></p>
<p><span style="color:#00B400;"># to the retention policies</span></p>
<p><span style="color:#000080;">log.retention.check.interval.ms</span><span style="color:#008000;">=</span><span style="color:#800000;">300000</span></p>
<p> </p>
<p><span style="color:#00B400;">############################# Zookeeper #############################</span></p>
<p> </p>
<p><span style="color:#00B400;"># Zookeeper connection string (see zookeeper docs for details).</span></p>
<p><span style="color:#00B400;"># This is a comma separated host:port pairs, each corresponding to a zk</span></p>
<p><span style="color:#00B400;"># server. e.g. "127.0.0.1:3000,127.0.0.1:3001,127.0.0.1:3002".</span></p>
<p><span style="color:#00B400;"># You can also append an optional chroot string to the urls to specify the</span></p>
<p><span style="color:#00B400;"># root directory for all kafka znodes.</span></p>
<p><span style="color:#000080;">zookeeper.connect</span><span style="color:#008000;">=</span><span style="color:#800000;">192.168.2.200:2181,192.168.2.201:2181,192.168.2.202:2181,192.168.2.203:2181,192.168.2.204:2181</span></p>
<p> </p>
<p><span style="color:#00B400;"># Timeout in ms for connecting to zookeeper</span></p>
<p><span style="color:#000080;">zookeeper.connection.timeout.ms</span><span style="color:#008000;">=</span><span style="color:#800000;">6000</span></p>
<p> </p>
<p> </p>
<p><span style="color:#00B400;">############################# Group Coordinator Settings #############################</span></p>
<p> </p>
<p><span style="color:#00B400;"># The following configuration specifies the time, in milliseconds, that the GroupCoordinator will delay the initial consumer rebalance.</span></p>
<p><span style="color:#00B400;"># The rebalance will be further delayed by the value of group.initial.rebalance.delay.ms as new members join the group, up to a maximum of max.poll.interval.ms.</span></p>
<p><span style="color:#00B400;"># The default value for this is 3 seconds.</span></p>
<p><span style="color:#00B400;"># We override this to 0 here as it makes for a better out-of-the-box experience for development and testing.</span></p>
<p><span style="color:#00B400;"># However, in production environments the default value of 3 seconds is more suitable as this will help to avoid unnecessary, and potentially expensive, rebalances during application startup.</span></p>
<p><span style="color:#000080;">group.initial.rebalance.delay.ms</span><span style="color:#008000;">=</span><span style="color:#800000;">0</span><strong></strong></p>
</td>
</tr></tbody></table><p>      在集群中每个节点的broker.id不同，类似zookeeper的myid</p>
<p align="left"> </p>
<p align="left"><strong>2.  启动kafka集群</strong></p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p>cd /usr/local/kafka_2.11-0.11.0.0/ </p>
<p>nohup bin/kafka-server-start.sh config/server.properties &amp;</p>
</td>
</tr></tbody></table><p> </p>
<p align="left">3.  创建主题</p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p align="left">bin/kafka-topics.sh --create --zookeeper 192.168.2.200:2181,192.168.2.201:2181,192.168.2.202:2181,192.168.2.203:2181,192.168.2.204:2181 --replication-factor 3 --partitions 1 --topic CZ-ICBC-TOPIC</p>
</td>
</tr></tbody></table><p> </p>
<p align="left">4. 查看主题状态</p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p>bin/kafka-topics.sh --describe --zookeeper 192.168.2.200:2181,192.168.2.201:2181,192.168.2.202:2181,192.168.2.203:2181,192.168.2.204:2181 --topic CZ-ICBC-TOPIC</p>
</td>
</tr></tbody></table><p> </p>
<p></p>
<p> </p>
<p align="left">5. 生产者生产数据</p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p>bin/kafka-console-producer.sh --broker-list 192.168.2.200:9092,192.168.2.201:9092,192.168.2.202:9092 --topic CZ-ICBC-TOPIC</p>
<p>this is a message</p>
</td>
</tr></tbody></table><p> </p>
<p align="left">6. 消费者消费数据</p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p>bin/kafka-console-consumer.sh --bootstrap-server 192.168.2.200:9092,192.168.2.201:9092,192.168.2.202:9092 --from-beginning --topic CZ-ICBC-TOPIC</p>
</td>
</tr></tbody></table><p> </p>
<p></p>
<p> </p>
            </div>
                </div>