---
layout:     post
title:      flume+Kafka+Storm实时框架
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div align="left">Flume：Flume是Cloudera提供的一个分布式、可靠、和高可用的海量日志采集、聚合和传输的日志收集系统，支持在日志系统中定制各类数据发送方，用于收集数据;同时，Flume提供对数据进行简单处理，并写到各种数据接受方(可定制)的能力。</div>
<div align="left"><br></div>
<div align="left">Flume数据源以及输出方式:Flume提供了从console(控制台)、RPC(Thrift-RPC)、text(文件)、tail(UNIX tail)、syslog(syslog日志系统，支持TCP和UDP等2种模式)，exec(命令执行)等数据源上收集数据的能力,在我们的系统中目前使用exec方式进行日志采集。</div>
<div align="left"><br></div>
<div align="left">flume的特点：<br>
　　flume是一个分布式、可靠、和高可用的海量日志采集、聚合和传输的系统。支持在日志系统中定制各类数据发送方，用于收集数据;同时，Flume提供对数据进行简单处理，并写到各种数据接受方(比如文本、HDFS、Hbase等)的能力 。<br>
　　flume的数据流由事件(Event)贯穿始终。事件是Flume的基本数据单位，它携带日志数据(字节数组形式)并且携带有头信息，这些Event由Agent外部的Source生成，当Source捕获事件后会进行特定的格式化，然后Source会把事件推入(单个或多个)Channel中。你可以把Channel看作是一个缓冲区，它将保存事件直到Sink处理完该事件。Sink负责持久化日志或者把事件推向另一个Source。<br>
 <br>
flume的可靠性 <br>
　　当节点出现故障时，日志能够被传送到其他节点上而不会丢失。Flume提供了三种级别的可靠性保障，从强到弱依次分别为：end-to-end（收到数据agent首先将event写到磁盘上，当数据传送成功后，再删除；如果数据发送失败，可以重新发送。），Store on failure（这也是scribe采用的策略，当数据接收方crash时，将数据写到本地，待恢复后，继续发送），Besteffort（数据发送到接收方后，不会进行确认）。<br>
 <br>
flume的可恢复性：<br>
　　还是靠Channel。推荐使用FileChannel，事件持久化在本地文件系统里(性能较差)。 <br>
 </div>
<div align="left">flume的一些核心概念：<br><span></span><span></span>Agent使用JVM 运行Flume。每台机器运行一个agent，但是可以在一个agent中包含多个sources和sinks。<br><span></span><span></span>Client生产数据，运行在一个独立的线程。<br><span></span><span></span>Source从Client收集数据，传递给Channel。<br><span></span><span></span>Sink从Channel收集数据，运行在一个独立线程。<br><span></span><span></span>Channel连接 sources 和 sinks ，这个有点像一个队列。<br><span></span><span></span>Events可以是日志记录、 avro 对象等。<br></div>
<div align="left"><br></div>
<p>Flume的数据接受方，可以是console(控制台)、text(文件)、dfs(HDFS文件)、RPC(Thrift-RPC)和syslogTCP(TCP syslog日志系统)等。由kafka来接收。</p>
<p>Flume以agent为最小的独立运行单位。一个agent就是一个JVM。单agent由Source、Sink和Channel三大组件构成，如下图：<br></p>
<p><img src="http://files.jb51.net/file_images/article/201408/201408111056044.png" alt=""><br></p>
<p>值得注意的是，Flume提供了大量内置的Source、Channel和Sink类型。不同类型的Source,Channel和Sink可以自由组合。组合方式基于用户设置的配置文件，非常灵活。比如：Channel可以把事件暂存在内存里，也可以持久化到本地硬盘上。Sink可以把日志写入HDFS, HBase，甚至是另外一个Source等等。Flume支持用户建立多级流，也就是说，多个agent可以协同工作，并且支持Fan-in、Fan-out、Contextual Routing、Backup Routes，这也正是NB之处。如下图所示:<br></p>
<p><img src="http://files.jb51.net/file_images/article/201408/201408111056045.png" alt=""><br></p>
<br><div align="left">Flume下载及文档：http://flume.apache.org/</div>
<div align="left"><br></div>
<div align="left">Flume安装：$tar zxvf apache-flume-1.4.0-bin.tar.gz</div>
<div align="left">Flume启动命令：$bin/flume-ng agent --conf conf --conf-file conf/flume-conf.properties --name producer -Dflume.root.logger=INFO,console</div>
<div align="left">更多详细flume命令:http://www.jb51.net/article/53542.htm</div>
<div align="left"><br></div>
<div align="left">
<div align="left">kafka是一种高吞吐量的分布式发布订阅消息系统，有如下特性：</div>
<ul><li>通过O(1)的磁盘数据结构提供消息的持久化，这种结构对于即使数以TB的消息存储也能够保持长时间的稳定性能。</li><li>高吞吐量：即使是非常普通的硬件kafka也可以支持每秒数十万的消息。</li><li>支持通过kafka服务器和消费机集群来分区消息。</li><li>支持Hadoop并行数据加载。<br></li></ul><div align="left">kafka的目的是提供一个发布订阅解决方案，它可以处理消费者规模的网站中的所有动作流数据。 这种动作（网页浏览，搜索和其他用户的行动）是在现代网络上的许多社会功能的一个关键因素。 这些数据通常是由于吞吐量的要求而通过处理日志和日志聚合来解决。 对于像Hadoop的一样的日志数据和离线分析系统，但又要求实时处理的限制，这是一个可行的解决方案。kafka的目的是通过Hadoop的并行加载机制来统一线上和离线的消息处理，也是为了通过集群机来提供实时的消费。</div>
<br></div>
<div align="left">kafka分布式订阅架构如下图：--取自Kafka官网<br></div>
<div align="left"><img id="aimg_2547" src="http://www.aboutyun.com/data/attachment/forum/201402/10/150105je8xweaxjsassesa.png" class="zoom" width="258" alt=""><br></div>
<div align="left">                           官网图</div>
<div align="left"><img id="aimg_2548" src="http://www.aboutyun.com/data/attachment/forum/201402/10/150106wfh8u7d4hbmsnh7u.jpg" class="zoom" width="562" alt=""><br></div>
<div align="left">其实两者没有太大区别，官网的架构图只是把Kafka简洁的表示成一个Kafka Cluster，而上面架构图就相对详细一些；<br></div>
<div align="left"><br></div>
<div align="left">
<div align="left">Kafka版本：0.8.0</div>
<div align="left">Kafka下载及文档：<a href="http://kafka.apache.org/" rel="nofollow">http://kafka.apache.org/</a></div>
<div align="left">Kafka安装：</div>
<div align="left">
<div id="code_KO6">
<ol><li>&gt; tar xzf kafka-&lt;VERSION&gt;.tgz<br></li><li>&gt; cd kafka-&lt;VERSION&gt;<br></li><li>&gt; ./sbt update<br></li><li>&gt; ./sbt package<br></li><li>&gt; ./sbt assembly-package-dependency</li></ol></div>
启动及测试命令：<br></div>
<div align="left">
<div id="code_yl5">
<ol><li>&gt; bin/zookeeper-server-start.shconfig/zookeeper.properties<br></li><li>&gt; bin/kafka-server-start.shconfig/server.properties</li></ol></div>
这里是官网上的教程，kafka本身有内置zookeeper，但是在实际部署中是使用单独的zookeeper集群。 </div>
<div align="left">配置独立的zookeeper集群需要配置server.properties文件，讲zookeeper.connect修改为独立集群的IP和端口：zookeeper.connect=nutch1:2181</div>
<div align="left">Create a topic：<br></div>
<div align="left">
<div id="code_YFu">
<ol><li>&gt; bin/kafka-create-topic.sh --zookeeper localhost:2181 --replica 1 --partition 1 --topic test<br></li><li>&gt; bin/kafka-list-topic.sh --zookeeperlocalhost:2181</li></ol></div>
Send some messages：<br></div>
<div align="left"><span></span><span></span><span></span>1. &gt; bin/kafka-console-producer.sh--broker-list localhost:9092 --topic test<br></div>
<div align="left">Start a consumer:<br></div>
<div align="left"><span></span><span></span><span></span>1. &gt; bin/kafka-console-consumer.sh--zookeeper localhost:2181 --topic test --from-beginning<br></div>
<div align="left"><br></div>
<div align="left">Storm:这是一个分布式的、容错的实时计算系统，它被托管在GitHub上.<br></div>
<div align="left"><img id="aimg_2549" src="http://www.aboutyun.com/data/attachment/forum/201402/10/150107qor24znw2nrbgzru.jpg" class="zoom" width="600" alt=""><br></div>
<div align="left">
<div align="left">Storm的主要特点如下：</div>
<ul class="litype_1"><li>简单的编程模型。类似于MapReduce降低了并行批处理复杂性，Storm降低了进行实时处理的复杂性。</li><li>可以使用各种编程语言。你可以在Storm之上使用各种编程语言。默认支持Clojure、Java、Ruby和Python。要增加对其他语言的支持，只需实现一个简单的Storm通信协议即可。</li><li>容错性。Storm会管理工作进程和节点的故障。</li><li>水平扩展。计算是在多个线程、进程和服务器之间并行进行的。</li><li>可靠的消息处理。Storm保证每个消息至少能得到一次完整处理。任务失败时，它会负责从消息源重试消息。</li><li>快速。系统的设计保证了消息能得到快速的处理，使用ØMQ作为其底层消息队列。（0.9.0.1版本支持ØMQ和netty两种模式）</li><li>本地模式。Storm有一个“本地模式”，可以在处理过程中完全模拟Storm集群。这让你可以快速进行开发和单元测试。</li></ul>
Storm具体的安装步骤可以参考：<a href="http://www.aboutyun.com/thread-6854-1-1.html" rel="nofollow">Storm-0.9.0.1安装部署 指导</a><br></div>
</div>
<div align="left"><br></div>
            </div>
                </div>