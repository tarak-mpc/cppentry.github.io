---
layout:     post
title:      第86讲：SparkStreaming数据源Flume实际案例分享
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span></span></p>
<pre style="font-size:9pt;font-family:'宋体';background-color:rgb(255,255,255);"><span style="color:rgb(51,51,51);font-family:Arial, 'Microsoft YaHei';font-size:14px;line-height:14px;background-color:rgb(238,238,238);">有兴趣想学习国内整套Spark+Spark Streaming+Machine learning最</span><span style="color:rgb(51,51,51);font-family:Arial, 'Microsoft YaHei';font-size:14px;line-height:14px;background-color:rgb(238,238,238);">顶级</span><span style="color:rgb(51,51,51);font-family:Arial, 'Microsoft YaHei';font-size:14px;line-height:14px;background-color:rgb(238,238,238);">课程的，可加我qq  471186150。共享视频，性价比超高！</span></pre>
<br><p></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>一、什么是Flume?</span><br>
　　flume 作为 cloudera 开发的实时日志收集系统，受到了业界的认可与广泛应用。Flume 初始的发行版本目前被统称为 Flume OG（original generation），属于 cloudera。但随着 FLume 功能的扩展，Flume OG 代码工程臃肿、核心组件设计不合理、核心配置不标准等缺点暴露出来，尤其是在 Flume OG 的最后一个发行版本 0.94.0 中，日志传输不稳定的现象尤为严重，为了解决这些问题，2011 年 10 月 22 号，cloudera 完成了 Flume-728，对
 Flume 进行了里程碑式的改动：重构核心组件、核心配置以及代码架构，重构后的版本统称为 Flume NG（next generation）；改动的另一原因是将 Flume 纳入 apache 旗下，cloudera Flume 改名为 Apache Flume。<br><br><span>   flume的特点：</span><br>
　　flume是一个分布式、可靠、和高可用的海量日志采集、聚合和传输的系统。支持在日志系统中定制各类数据发送方，用于收集数据;同时，Flume提供对数据进行简单处理，并写到各种数据接受方(比如文本、HDFS、Hbase等)的能力 。<br>
　　flume的数据流由事件(Event)贯穿始终。事件是Flume的基本数据单位，它携带日志数据(字节数组形式)并且携带有头信息，这些Event由Agent外部的Source生成，当Source捕获事件后会进行特定的格式化，然后Source会把事件推入(单个或多个)Channel中。你可以把Channel看作是一个缓冲区，它将保存事件直到Sink处理完该事件。Sink负责持久化日志或者把事件推向另一个Source。<br><span> flume的可靠性 </span><br>
　　当节点出现故障时，日志能够被传送到其他节点上而不会丢失。Flume提供了三种级别的可靠性保障，从强到弱依次分别为：end-to-end（收到数据agent首先将event写到磁盘上，当数据传送成功后，再删除；如果数据发送失败，可以重新发送。），Store on failure（这也是scribe采用的策略，当数据接收方crash时，将数据写到本地，待恢复后，继续发送），Besteffort（数据发送到接收方后，不会进行确认）。<br><span>flume的可恢复性：</span><br>
　　还是靠Channel。推荐使用FileChannel，事件持久化在本地文件系统里(性能较差)。 <br><span>flume的一些核心概念：</span><br>
Agent        使用JVM 运行Flume。每台机器运行一个agent，但是可以在一个agent中包含多个sources和sinks。</p>
<ol style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);"><li style="list-style:decimal;">Client        生产数据，运行在一个独立的线程。</li><li style="list-style:decimal;">Source        从Client收集数据，传递给Channel。</li><li style="list-style:decimal;">Sink        从Channel收集数据，运行在一个独立线程。</li><li style="list-style:decimal;">Channel        连接 sources 和 sinks ，这个有点像一个队列。</li><li style="list-style:decimal;">Events        可以是日志记录、 avro 对象等。</li></ol><p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
　Flume以agent为最小的独立运行单位。一个agent就是一个JVM。单agent由Source、Sink和Channel三大组件构成，如下图：</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160425235100298-737692625.png" alt="" style="border:0px;"></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
　　值得注意的是，Flume提供了大量内置的Source、Channel和Sink类型。不同类型的Source,Channel和Sink可以自由组合。组合方式基于用户设置的配置文件，非常灵活。比如：Channel可以把事件暂存在内存里，也可以持久化到本地硬盘上。Sink可以把日志写入HDFS, HBase，甚至是另外一个Source等等。Flume支持用户建立多级流，也就是说，多个agent可以协同工作，并且支持Fan-in、Fan-out、Contextual Routing、Backup Routes，这也正是NB之处。如下图所示:</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160425235122845-59089704.png" alt="" style="border:0px;"></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>二、Flume+Kafka+Spark Streaming应用场景：</span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
1、Flume集群采集外部系统的业务信息，将采集后的信息发生到Kafka集群，最终提供Spark Streaming流框架计算处理，流处理完成后再将最终结果发送给Kafka存储，架构如下图：</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160425235141142-595835993.png" alt="" style="border:0px;"></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
2、Flume集群采集外部系统的业务信息，将采集后的信息发生到Kafka集群，最终提供Spark Streaming流框架计算处理，流处理完成后再将最终结果发送给Kafka存储，同时将最终结果通过Ganglia监控工具进行图形化展示，架构如下图：</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160425235203080-1298406775.png" alt="" style="border:0px;"></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
3、我们要做：Spark streaming 交互式的360度的可视化，Spark streaming 交互式3D可视化UI；Flume集群采集外部系统的业务信息，将采集后的信息发生到Kafka集群，最终提供Spark Streaming流框架计算处理，流处理完成后再将最终结果发送给Kafka存储，将最终结果同时存储在数据库（Mysql）、内存中间件（Redis、MemSQL）中，同时将最终结果通过Ganglia监控工具进行图形化展示，架构如下图：</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160425235248752-1463890087.png" alt="" style="border:0px;"></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>三、Kafka数据写入Spark Streaming有二种方式：</span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
一种是Receivers，这个方法使用了Receivers来接收数据，Receivers的实现使用到Kafka高层次的消费者API，对于所有的Receivers，接收到的数据将会保存在<a href="http://www.iteblog.com/archives/tag/spark" rel="nofollow" style="color:#000000;">Spark</a> 分布式的executors中，然后由Spark Streaming启动的Job来处理这些数据；然而，在默认的配置下，这种方法在失败的情况下会丢失数据，为了保证零数据丢失，你可以在Spark
 Streaming中使用WAL日志功能，这使得我们可以将接收到的数据保存到WAL中（WAL日志可以存储在HDFS上），所以在失败的时候，我们可以从WAL中恢复，而不至于丢失数据。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
另一种是DirectAPI，产生数据和处理数据的时候是在两台机器上？其实是在同一台数据上，由于在一台机器上有Driver和Executor,所以这台机器要足够强悍。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
Flume集群将采集的数据放到Kafka集群中，Spark Streaming会实时在线的从Kafka集群中通过DirectAPI拿数据，可以通过Kafka中的topic+partition查询最新的偏移量（offset）来读取每个batch的数据，即使读取失败也可再根据偏移量来读取失败的数据，保证应用运行的稳定性和数据可靠性。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
 </p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>温馨提示：</span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
1、Flume集群数据写入Kafka集群时可能会导致数据存放不均衡，即有些Kafka节点数据量很大、有些不大，后续会对分发数据进行自定义算法来解决数据存放不均衡问题。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
2、个人强烈推荐在生产环境下用DirectAPI，但是我们的发行版，会对DirectAPI进行优化，降低其延迟。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="line-height:1.5;">总结：</span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
　　实际生产环境下，搜集分布式的日志以Kafka为核心。</p>
<h3 align="center" style="font-size:16px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(238,238,238);">
使用Spark Streaming可以处理各种数据来源类型，如：数据库、HDFS，服务器log日志、网络流，其强大超越了你想象不到的场景，只是很多时候大家不会用，其真正原因是对Spark、spark streaming本身不了解。</h3>
            </div>
                </div>