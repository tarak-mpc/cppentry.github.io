---
layout:     post
title:      Kafka实战－Flume到Kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1>1.概述</h1>
<p>　　前面给大家介绍了整个Kafka项目的开发流程，今天给大家分享Kafka如何获取数据源，即Kafka生产数据。下面是今天要分享的目录：</p>
<ul><li>数据来源</li><li>Flume到Kafka</li><li>数据源加载</li><li>预览</li></ul><p>　　下面开始今天的分享内容。</p>
<h1>2.数据来源</h1>
<p>　　Kafka生产的数据，是由Flume的Sink提供的，这里我们需要用到Flume集群，通过Flume集群将Agent的日志收集分发到Kafka（供实时计算处理）和HDFS（离线计算处理）。关于Flume集群的Agent部署，这里就不多做赘述了，不清楚的同学可以参考《<a id="homepage1_HomePageDays_DaysList_ctl07_DayList_TitleUrl_0" class="postTitle2" href="http://www.cnblogs.com/smartloli/p/4468708.html" rel="nofollow">高可用Hadoop平台－Flume
 NG实战图解篇</a>》一文中的介绍，下面给大家介绍数据来源的流程图，如下图所示：</p>
<p><img src="http://images0.cnblogs.com/blog2015/666745/201507/021209089712474.png" alt=""></p>
<p>　　这里，我们使用Flume作为日志收集系统，将收集到的数据输送到Kafka中间件，以供Storm去实时消费计算，整个流程从各个Web节点上，通过Flume的Agent代理收集日志，然后汇总到Flume集群，在由Flume的Sink将日志输送到Kafka集群，完成数据的生产流程。</p>
<h1>3.Flume到Kafka</h1>
<p>　　从图，我们已经清楚了数据生产的流程，下面我们来看看如何实现Flume到Kafka的输送过程，下面我用一个简要的图来说明，如下图所示：</p>
<p><img src="http://images0.cnblogs.com/blog2015/666745/201507/021211417053445.png" alt=""></p>
<p>　　这个表达了从Flume到Kafka的输送工程，下面我们来看看如何实现这部分。</p>
<p>　　首先，在我们完成这部分流程时，需要我们将Flume集群和Kafka集群都部署完成，在完成部署相关集群后，我们来配置Flume的Sink数据流向，配置信息如下所示：</p>
<ul><li>首先是配置spooldir方式，内容如下所示：</li></ul><div class="cnblogs_code">
<pre>producer.sources.s.type =<span style="color:#000000;"> spooldir
producer.sources.s.spoolDir </span>= /home/hadoop/<span style="color:#0000ff;">dir</span>/logdfs</pre>
</div>
<ul><li>当然，Flume的数据发送方类型也是多种类型的，有：Console、Text、HDFS、RPC等，这里我们系统所使用的是Kafka中间件来接收，配置内容如下所示：</li></ul><div class="cnblogs_code">
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre>producer.sinks.r.type =<span style="color:#000000;"> org.apache.flume.plugins.KafkaSink
producer.sinks.r.metadata.broker.list</span>=dn1:<span style="color:#800080;">9092</span>,dn2:<span style="color:#800080;">9092</span>,dn3:<span style="color:#800080;">9092</span><span style="color:#000000;">
producer.sinks.r.partition.key</span>=<span style="color:#800080;">0</span><span style="color:#000000;">
producer.sinks.r.partitioner.class</span>=<span style="color:#000000;">org.apache.flume.plugins.SinglePartition
producer.sinks.r.serializer.class</span>=<span style="color:#000000;">kafka.serializer.StringEncoder
producer.sinks.r.request.required.acks</span>=<span style="color:#800080;">0</span><span style="color:#000000;">
producer.sinks.r.max.message.size</span>=<span style="color:#800080;">1000000</span><span style="color:#000000;">
producer.sinks.r.producer.type</span>=<span style="color:#0000ff;">sync</span><span style="color:#000000;">
producer.sinks.r.custom.encoding</span>=UTF-<span style="color:#800080;">8</span><span style="color:#000000;">
producer.sinks.r.custom.topic.name</span>=test</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
</div>
<p>　　这样，我们就在Flume的Sink端配置好了数据流向接受方。</p>
<h1>4.数据加载</h1>
<p>　　在完成配置后，接下来我们开始加载数据，首先我们在Flume的spooldir端生产日志，以供Flume去收集这些日志。然后，我们通过Kafka的KafkaOffsetMonitor监控工具，去监控数据生产的情况，下面我们开始加载。</p>
<ul><li>启动ZK集群，内容如下所示：</li></ul><div class="cnblogs_code">
<pre>zkServer.<span style="color:#0000ff;">sh</span> start</pre>
</div>
<p>　　注意：分别在ZK的节点上启动。</p>
<ul><li>启动Kafka集群</li></ul><div class="cnblogs_code">
<pre>kafka-server-start.<span style="color:#0000ff;">sh</span> config/server.properties &amp;</pre>
</div>
<p>　　在其他的Kafka节点输入同样的命令，完成启动。</p>
<ul><li>启动Kafka监控工具</li></ul><div class="cnblogs_code">
<pre>java -<span style="color:#0000ff;">cp</span> KafkaOffsetMonitor-assembly-<span style="color:#800080;">0.2</span>.<span style="color:#800080;">0</span><span style="color:#000000;">.jar \
 com.quantifind.kafka.offsetapp.OffsetGetterWeb \
 </span>--zk dn1:<span style="color:#800080;">2181</span>,dn2:<span style="color:#800080;">2181</span>,dn3:<span style="color:#800080;">2181</span><span style="color:#000000;"> \
 </span>--port <span style="color:#800080;">8089</span><span style="color:#000000;"> \
 </span>--refresh <span style="color:#800080;">10</span><span style="color:#000000;">.seconds \
 </span>--retain <span style="color:#800080;">1</span>.days</pre>
</div>
<ul><li>启动Flume集群</li></ul><div class="cnblogs_code">
<pre>flume-ng agent -n producer -c conf -f flume-kafka-sink.properties -Dflume.root.logger=ERROR,console</pre>
</div>
<p>　　然后，我在/home/hadoop/dir/logdfs目录下上传log日志，这里我只抽取了一少部分日志进行上传，如下图所示，表示日志上传成功。</p>
<p><img src="http://images0.cnblogs.com/blog2015/666745/201507/021401396551939.png" alt=""></p>
<h1>5.预览</h1>
<p>　　下面，我们通过Kafka的监控工具，来预览我们上传的日志记录，有没有在Kafka中产生消息数据，如下所示：</p>
<ul><li>启动Kafka集群，为生产消息截图预览</li></ul><p><img src="http://images0.cnblogs.com/blog2015/666745/201507/021403529647010.png" alt=""></p>
<ul><li>通过Flume上传日志，在Kafka中产生消息数据</li></ul><p><img src="http://images0.cnblogs.com/blog2015/666745/201507/021405485519095.png" alt=""></p>
<h1>6.总结</h1>
<p>　　本篇文章给大家讲述了Kafka的消息产生流程，后续会在Kafka实战系列中为大家讲述Kafka的消息消费流程等一整套流程，这里只是为后续的Kafka实战编码打下一个基础，让大家先对Kafka的消息生产有个整体的认识。</p>
            </div>
                </div>