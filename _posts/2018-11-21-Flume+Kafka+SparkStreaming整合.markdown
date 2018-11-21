---
layout:     post
title:      Flume+Kafka+SparkStreaming整合
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
摘要：目次 1.Flume先容. 2 1.1 Flume数据源和输出体式格局. 2 1.2 Flume的焦点观点. 2 1.3 Flume布局. 2 1.4 Flume装置测试. 3 1.5 启动flume4 2.Kafka先容. 4 ]</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
目录</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
1.Flume介绍. 2</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
1.1 Flume数据源以及输出方式. 2</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
1.2 Flume的核心概念. 2</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
1.3 Flume结构. 2</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
1.4 Flume安装测试. 3</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
1.5 启动flume4</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
2.Kafka介绍. 4</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
2.1 Kafka产生背景. 4</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
2.2 Kafka部署结构. 4</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
2.3 Kafka集群架构. 4</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
2.4 Kafka基本概念. 5</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
2.5 Kafka安装测试. 5</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
3.Flume和Kafka整合. 6</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
3.1两者整合优势. 6</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
3.2 Flume和Kafka整合安装. 6</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
3.3 启动kafka flume相关服务. 7</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
3.4 Kafka和SparkStreaming整合. 8</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
 </p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<span style="font-weight:bold;">1. Flume介绍</span></p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<span style="color:rgb(68,68,68);">Flume</span><span style="color:rgb(68,68,68);">是Cloudera</span>提供的一个分布式、可靠、和高可用的海量日志采集、聚合和传输的日志收集系统，支持在日志系统中定制各类数据发送方，用于收集数据;同时，Flume提供对数据进行简单处理，并写到各种数据接受方(可定制)的能力。</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<span style="color:rgb(68,68,68);"> </span></p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<strong><span style="color:rgb(68,68,68);">1.1 Flume数据源以及输出方式</span></strong></p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<span style="color:rgb(68,68,68);">Flume</span><span style="color:rgb(68,68,68);">提供了从console(</span>控制台)、RPC(Thrift-RPC)、text(文件)、tail(UNIX tail)、syslog(syslog日志系统，支持TCP和UDP等2种模式)，exec(命令执行)等数据源上收集数据的能力,在我们的系统中目前使用exec方式进行日志采集。</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<span style="color:rgb(68,68,68);">Flume</span><span style="color:rgb(68,68,68);">的数据接受方，可以是console(</span>控制台)、text(文件)、dfs(HDFS文件)、RPC(Thrift-RPC)和syslogTCP(TCP syslog日志系统)等。本测试研究中由kafka来接收数据。</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<span style="color:rgb(68,68,68);"> </span></p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<strong><span style="color:rgb(68,68,68);">1.2 Flume</span><span style="color:rgb(68,68,68);">的核心概念</span></strong></p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<span style="color:rgb(68,68,68);">1.  </span><span style="color:rgb(68,68,68);">Agent</span><span style="color:rgb(68,68,68);">：使用JVM</span>运行Flume。每台机器运行一个agent，但是可以在一个agent中包含多个sources和sinks。</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<span style="color:rgb(68,68,68);">2.  </span><span style="color:rgb(68,68,68);">Client</span><span style="color:rgb(68,68,68);">：生产数据，运行在一个独立的线程。</span></p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<span style="color:rgb(68,68,68);">3.  </span><span style="color:rgb(68,68,68);">Source</span><span style="color:rgb(68,68,68);">：从Client</span>收集数据，传递给Channel。</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<span style="color:rgb(68,68,68);">4.  </span><span style="color:rgb(68,68,68);">Sink</span><span style="color:rgb(68,68,68);">：从Channel</span>收集数据，运行在一个独立线程。</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<span style="color:rgb(68,68,68);">5.  </span><span style="color:rgb(68,68,68);">Channel</span><span style="color:rgb(68,68,68);">：连接 sources</span>和 sinks ，这个有点像一个队列。</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<span style="color:rgb(68,68,68);">6.  </span><span style="color:rgb(68,68,68);">Events </span><span style="color:rgb(68,68,68);">：可以是日志记录、 avro</span>对象等。</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<strong><span style="color:rgb(68,68,68);">1.3 结构</span></strong></p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<span style="color:rgb(68,68,68);">Flume</span><span style="color:rgb(68,68,68);">以agent</span>为最小的独立运行单位。一个agent就是一个JVM。单agent由Source、Sink和Channel三大组件构成，如下图：</p>
<p align="center" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
 <img src="http://doc.ithao123.cn/d/f8/da/0b/f8da0bfbfbc602a869b5e622cf5a6e1a.jpg" alt="\" style="border:medium none;"></p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<span style="color:rgb(68,68,68);">Flume</span><span style="color:rgb(68,68,68);">提供了大量内置的Source</span>、Channel和Sink类型。不同类型的Source,Channel和Sink可以自由组合。组合方式基于用户设置的配置文件，非常灵活。比如：Channel可以把事件暂存在内存里，也可以持久化到本地硬盘上。Sink可以把日志写入HDFS, HBase，甚至是另外一个Source等等。Flume支持用户建立多级流，也就是说，多个agent可以协同工作，并且支持Fan-in、Fan-out、ContextualRouting、Backup
 Routes。如下图所示:</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<img src="http://doc.ithao123.cn/d/72/29/c8/7229c89d710c9180dc9eebe207b1423a.jpg" alt="\" style="border:medium none;"><br></p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<img src="http://www.ithao123.cn/content-10045818.html" alt="\" style="border:medium none;"><br></p>
<p align="center" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<strong><span style="color:rgb(68,68,68);">1.4 安装测试</span></strong></p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
解压apache-flume-1.6.0-bin.tar.gz：tar –zxvf apache-flume-1.6.0-bin.tar.gz</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
cp conf/flume-conf.properties.template conf/exec.conf</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
cp conf/flume-env.sh.template conf/flume-env.sh    配置JAVA_HOME</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
exec.conf配置如下:</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
a2.sources = r2</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
a2.sinks = k2</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
a2.channels = c2</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
# Describe/configure the source</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
a2.sources.r2.type = exec</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
a2.sources.r2.channels = c2</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
a2.sources.r2.command=tail -n +0 -F /usr/local/hadoop/flume/test.log</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
# Describe the sink</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
a2.sinks.k2.type = logger</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
# Use a channel which buffers events in memory</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
a2.channels.c2.type = memory</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
a2.channels.c2.capacity = 1000</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
a2.channels.c2.transactionCapacity = 100</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
# Bind the source and sink to the channel</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
a2.sources.r2.channels = c2</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
a2.sinks.k2.channel = c2</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
验证安装：flume-ng version</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<img src="http://doc.ithao123.cn/d/7a/86/92/7a8692d6e463b31642baa9f688f5971c.jpg" alt="\" style="border:medium none;"><br></p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<img src="http://www.ithao123.cn/content-10045818.html" alt="\" style="border:medium none;"><br></p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<strong><span style="color:rgb(68,68,68);">1.5 启动flume</span></strong></p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
flume-ng agent --conf ./flume/conf/ -f ./flume/conf/exec.conf-Dflume.root.logger=DEBUG,console -n a2</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<strong>发送数据和flume接收数据：</strong></p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<strong><img src="http://doc.ithao123.cn/d/73/81/10/7381107177d55ab15054a5f165530928.jpg" alt="\" style="border:medium none;"><br></strong></p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<img src="http://www.ithao123.cn/content-10045818.html" alt="\" style="border:medium none;"><img src="http://doc.ithao123.cn/d/c2/71/83/c2718360e6fbf396b88ff226a1f98eb0.jpg" alt="\" style="border:medium none;"></p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<img src="http://www.ithao123.cn/content-10045818.html" alt="\" style="border:medium none;"><br></p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<strong>2.Kafka介绍</strong></p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<span style="font-weight:bold;">2.1 </span><span style="color:rgb(68,68,68);"><strong>产生背景</strong></span></p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
Kafka 是分布式发布-订阅消息系统。它最初由 LinkedIn 公司开发，使用 Scala语言编写,之后成为 Apache 项目的一部分。Kafka是一个分布式的，可划分的，多订阅者,冗余备份的持久性的日志服务。它主要用于处理活跃的流式数据。</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
在大数据系统中，常常会碰到一个问题，整个大数据是由各个子系统组成，数据需要在各个子系统中高性能，低延迟的不停流转。传统的企业消息系统并不是非常适合大规模的数据处理。为了已在同时搞定在线应用（消息）和离线应用（数据文件，日志）Kafka 就出现了。Kafka 可以起到两个作用：</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
降低系统组网复杂度</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
降低编程复杂度，各个子系统不在是相互协商接口，各个子系统类似插口插在插座上，Kafka 承担高速数据总线的作用。</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
 </p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<strong><span style="color:rgb(68,68,68);">2.2 部署结构</span></strong></p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<img src="http://doc.ithao123.cn/d/fd/a3/fb/fda3fb2da910782cda686c1d07f54ea2.jpg" alt="\" style="border:medium none;"><strong></strong></p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<strong> <img src="http://www.ithao123.cn/content-10045818.html" alt="\" style="border:medium none;"></strong></p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<strong><span style="color:rgb(68,68,68);">2.3 集群架构</span></strong></p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<strong></strong></p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<span style="color:rgb(68,68,68);"><strong><img src="http://doc.ithao123.cn/d/2a/b6/fc/2ab6fc4daa98bd9ef5c09a5f8416ebc8.jpg" alt="\" style="border:medium none;"><br></strong></span></p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<span style="color:rgb(68,68,68);"><strong><br></strong></span></p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<strong><span style="color:rgb(68,68,68);"><img src="http://www.ithao123.cn/content-10045818.html" alt="\" style="border:medium none;"></span></strong></p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<strong><span style="color:rgb(68,68,68);">2.4 基本概念</span></strong></p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
Topic：特指 Kafka 处理的消息源（feeds of messages）的不同分类。</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
Partition：Topic 物理上的分组，一个topic 可以分为多个 partition，每个 partition 是一个有序的队列。partition 中的每条消息都会被分配一个有序的id（offset）。</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
Message：消息，是通信的基本单位，每个 producer 可以向一个 topic（主题）发布一些消息。</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
Producers：消息和数据生产者，向 Kafka的一个 topic 发布消息的过程叫做 producers。</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
Consumers：消息和数据消费者，订阅 topics 并处理其发布的消息的过程叫做 consumers。</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
Broker：缓存代理，Kafa 集群中的一台或多台服务器统称为broker。</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
 </p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<strong><span style="color:rgb(68,68,68);">2.5 安装测试</span></strong></p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
解压Kafka: tar -xzf kafka_2.10-0.8.1.1.tgz</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
启动ZK bin/zookeeper-server-start.shconfig/zookeeper.properties</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
启动服务bin/kafka-server-start.sh config/server.properties&gt;/dev/null 2&gt;&amp;1&amp;</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
创建主题 bin/kafka-topics.sh --create --zookeeperlocalhost:2181 --replication-factor 1 --partitions 1 --topic test</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
查看主题 bin/kafka-topics.sh --list --zookeeperlocalhost:2181</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
查看主题详情 bin/kafka-topics.sh--describe --zookeeper localhost:2181 --topic test</p>
<p style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
删除主题 bin/kafka-run-class.shkafka.admin.DeleteTopicCommand --topic test --zookeeper localhost:2181</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
创建生产者bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
创建消费者bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic test--from-beginning</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
 </p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<strong>3.Flume和Kafka整合</strong></p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<strong><span style="color:rgb(68,68,68);">3.1 两者整合优势</span></strong></p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
Flume更倾向于数据传输本身，Kakfa是典型的消息中间件用于解耦生产者消费者。</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
具体架构上，Agent并没把数据直接发送到Kafka，在Kafka前面有层由Flume构成的forward。这样做有两个原因：</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
Kafka的API对非JVM系的语言支持很不友好，forward对外提供更加通用的HTTP接口。</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
forward层可以做路由、Kafka topic和Kafkapartition key等逻辑，进一步减少Agent端的逻辑。</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
数据有数据源到flume再到Kafka时，数据一方面可以同步到HDFS做离线计算，另一方面可以做实时计算。本文实时计算采用SparkStreaming做测试。</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
 </p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<strong><span style="color:rgb(68,68,68);">3.2 Flume和Kafka</span>整合安装</strong></p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
Flume和Kafka插件包下载：https://github.com/beyondj2ee/flumeng-kafka-plugin</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<span style="color:rgb(68,68,68);">提取插件中的flume-conf.properties</span>文件：修改如下:flume源采用exec</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
producer.sources.s.type = exec</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
producer.sources.s.command=tail -f -n+1/usr/local/Hadoop/flume/test.log</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
producer.sources.s.channels = c</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
修改producer代理的topic为test</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
将配置放到flume/cong/producer.conf中</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
复制插件包中的jar包到flume/lib中：删除掉版本不同的相同jar包，这里需要删除scala-compiler-z.9.2.jar包，否则flume启动会出现问题。</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<img src="http://www.ithao123.cn/content-10045818.html" alt="\" style="border:medium none;"><img src="http://doc.ithao123.cn/d/4f/75/c3/4f75c312a38e61c30f93239ac5810d7b.jpg" alt="\" style="border:medium none;"><br></p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
复制kafka/libs中的jar包到flume/lib中。</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<strong>完整producer.conf:</strong></p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<strong></strong></p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
producer.conf:</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
#agentsection </p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
producer.sources= s </p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
producer.channels= c </p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
producer.sinks= r </p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
#sourcesection </p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
producer.sources.s.type= exec</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
#producer.sources.s.spoolDir= /usr/local/hadoop/flume/logs</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
#producer.sources.s.fileHeader= true</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
producer.sources.s.command= tail -f -n+1 /usr/local/hadoop/flume/aaa.log</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
producer.sources.s.channels= c </p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
# Eachsink's type must be defined </p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
producer.sinks.r.type= org.apache.flume.plugins.KafkaSink </p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
producer.sinks.r.metadata.broker.list=localhost:9092 </p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
producer.sinks.r.partition.key=0 </p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
producer.sinks.r.partitioner.class=org.apache.flume.plugins.SinglePartition </p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
producer.sinks.r.serializer.class=kafka.serializer.StringEncoder </p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
producer.sinks.r.request.required.acks=0 </p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
producer.sinks.r.max.message.size=1000000 </p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
producer.sinks.r.producer.type=sync </p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
producer.sinks.r.custom.encoding=UTF-8 </p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
producer.sinks.r.custom.topic.name=test </p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
#Specifythe channel the sink should use </p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
producer.sinks.r.channel= c </p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
# Eachchannel's type is defined. </p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
producer.channels.c.type= memory </p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
producer.channels.c.capacity= 1000</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
producer.channels.c.transactionCapacity= 100</p>
<br style="color:rgb(51,51,51);font-family:'microsoft yahei';font-size:16px;"><p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<strong><span style="color:rgb(68,68,68);">3.3 启动kafka flume</span>相关服务</strong></p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
启动ZKbin/zookeeper-server-start.sh config/zookeeper.properties</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
启动Kafka服务 bin/kafka-server-start.sh config/server.properties</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
创建消费者bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic test--from-beginning</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<strong>启动flume</strong></p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
flume-ng agent --conf./flume/conf/ -f ./flume/conf/producer.conf -Dflume.root.logger=DEBUG,console-n producer</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
 </p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
向flume发送数据：</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<img src="http://www.ithao123.cn/content-10045818.html" alt="\" style="border:medium none;"><img src="http://doc.ithao123.cn/d/b0/63/65/b06365660b8d2cfd0627ddf219aa88c8.jpg" alt="\" style="border:medium none;"><br></p>
<p align="center" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
Kafka消费者数据：</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<img src="http://www.ithao123.cn/content-10045818.html" alt="\" style="border:medium none;"><br></p>
<p align="center" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<img src="http://doc.ithao123.cn/d/7b/00/32/7b00321ea4bb28fc70fb95c5a320819f.jpg" alt="\" style="border:medium none;"><br></p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<strong><span style="color:rgb(68,68,68);">3.4 Kafka和SparkStreaming</span>整合</strong></p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<strong>核心代码：</strong></p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<strong></strong></p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
完整代码路径：</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
spark-1.4.0\examples\src\main\java\org\apache\spark\examples\streaming</p>
<img src="http://www.ithao123.cn/content-10045818.html" alt="\" style="border:medium none;color:rgb(51,51,51);font-family:'microsoft yahei';font-size:16px;"><br style="color:rgb(51,51,51);font-family:'microsoft yahei';font-size:16px;"><p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<strong><img src="http://www.ithao123.cn/content-10045818.html" alt="\" style="border:medium none;"><img src="http://doc.ithao123.cn/d/61/cc/40/61cc405877fb63e15b6249b2adbc5c4d.jpg" alt="\" style="border:medium none;"><br></strong></p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<strong></strong></p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<img src="http://doc.ithao123.cn/d/cb/99/66/cb996606098b9cf8bbdc5defb8854263.jpg" alt="\" style="border:medium none;"></p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<strong>执行参数：</strong></p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<strong><img src="http://www.ithao123.cn/content-10045818.html" alt="\" style="border:medium none;"><img src="http://doc.ithao123.cn/d/e7/03/4a/e7034a66c57fcc0cd855039a30f668a3.jpg" alt="\" style="border:medium none;"><br></strong></p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<strong>发送数据：</strong></p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
由于flume采用exec数据源的方式，因此flume会监听配置的相应的文件： tail -f -n+1 /usr/local/Hadoop/flume/aaa.log</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
当向该文件追加文件时，flume就会获取追加的数据：</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
writetoflume.py</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<img src="http://www.ithao123.cn/content-10045818.html" alt="\" style="border:medium none;"><img src="http://doc.ithao123.cn/d/88/83/7a/88837a212441a5e28535ff8530ebc1af.jpg" alt="\" style="border:medium none;"><br></p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
flume将获取的增量数据由sink发送给kafka,以下是kafka comsumer消费的数据</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<img src="http://www.ithao123.cn/content-10045818.html" alt="\" style="border:medium none;"><br></p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<img src="http://doc.ithao123.cn/d/b6/66/1b/b6661bc7d0ccca0dd47a29e862185deb.jpg" alt="\" style="border:medium none;"></p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<br></p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<strong>执行结果：</strong></p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
SparkStreaming订阅kafka的test主题的数据，将订阅的数据进行单词计数处理。</p>
<p align="left" style="font-size:18px;color:rgb(51,51,51);font-family:'microsoft yahei';">
<img src="http://www.ithao123.cn/content-10045818.html" alt="\" style="border:medium none;"><img src="http://doc.ithao123.cn/d/e3/1a/a4/e31aa435fa90c259acae120f6b6120b7.jpg" alt="\" style="border:medium none;"></p>
            </div>
                </div>