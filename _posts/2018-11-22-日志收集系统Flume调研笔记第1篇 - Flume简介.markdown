---
layout:     post
title:      日志收集系统Flume调研笔记第1篇 - Flume简介
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div><span style="font-family:SimSun;font-size:14px;">用户行为数据的收集无疑是构建推荐系统的先决条件，而Apache基金会下的Flume项目正是为分布式的日志收集量身打造的，本文是Flume调研笔记的第1篇，主要介绍Flume的基本架构，下篇笔记将会以实例说明Flume的部署和使用步骤。</span></div>
<div><span style="font-family:SimSun;font-size:14px;">本文所用的Flume版本为目前最新版的ver1.5.2，它属于Flume-NG，在系统架构上与Flume-OG有所区别，二者的不同可以参考<a href="https://cwiki.apache.org/confluence/display/FLUME/Getting+Started" rel="nofollow">FlumeWiki文档</a>的说明。</span></div>
<h1><strong><span style="font-family:SimSun;font-size:14px;">1. Flume是什么</span></strong></h1>
<div><span style="font-family:SimSun;font-size:14px;">Flume是Apache基金会下的一个开源项目，它实现了一套分布式的、可靠且高可用系统，用于高效地收集、聚合或移动来自不同源的大量日志数据（典型如来自多台WebServer的日志），并支持将这些数据统一写入通用存储系统（典型如HDFS）。</span></div>
<h1><strong><span style="font-family:SimSun;font-size:14px;">2. Flume的适用场景</span></strong></h1>
<div><span style="font-family:SimSun;font-size:14px;">Flume最典型的应用场景就是聚合来自不同源的日志数据并将这些数据集中写入统一的存储系统（如HDFS或Kafka）。在涉及到流式计算的应用中（如实时推荐系统），我们经常会看到Flume的身影。</span></div>
<div><span style="font-family:SimSun;font-size:14px;">Flume接受的输入被称为"<strong>event data</strong>"。对于Flume来说，"event"就是一堆字节流（<strong>To Flume, an event is just a generic blob of bytes</strong>. 出自官方文档<a href="https://flume.apache.org/FlumeUserGuide.html#is-flume-a-good-fit-for-your-problem" rel="nofollow">Flume
 User Guide</a>），所以，除纯文本数据外，"event data"还支持2进制数据，如图片、网络流（network traffic）等。</span></div>
<div><span style="font-family:SimSun;font-size:14px;">但需要注意的是，<span style="background-color:rgb(255,255,51);">Flume支持的event数据有大小限制，单个event data不能大于部署Flume系统的机器的内存或磁盘</span>，好在正常模块产生的单条数据应该不会超过这个物理限制。</span></div>
<div><span style="font-family:SimSun;font-size:14px;">根据官方推荐的<a href="https://cwiki.apache.org/confluence/display/FLUME/Flume+NG+Performance+Measurements" rel="nofollow">Flume NG性能测试文档</a>，单台机器起多个Flume Agent进程同时向同一个HDFS写数据，保持event-size=300bytes的发送压力，在不丢数据的前提下，单机至少可处理4w+
 events/sec，上限可达7w+ events/sec。当然，确切的数据与机器硬件配置有关，但我们可以通过该结果评估Flume是否能满足实际业务的性能要求。此外，文中的结果表明单机最大吞吐量与Flume Agent的并发数有关，最优的并发数与机器CPU核数一致，细节可以阅读源文档。</span></div>
<div><span style="font-family:SimSun;font-size:14px;">总之，<strong>在合理配置的情况下，Flume可以适用于有日志收集和聚合需求的绝大多数分布式应用场合</strong>。</span></div>
<div><span style="font-family:SimSun;font-size:14px;"></span>
<h1><span style="font-family:SimSun;font-size:14px;">3. Flume典型系统架构</span></h1>
<div><span style="font-family:SimSun;font-size:14px;">根据Flume User Guild文档的说明，典型的Flume数据流模型如下所示：<br><img src="https://img-blog.csdn.net/20150429183439216?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2x2aGVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span>
<div><span style="font-family:SimSun;font-size:14px;">上图中方框圈起来的部分就是Flume的系统架构，被抽象为Agent，它在物理上表现为一个flume-agent进程，该进程实际上是个JVM。</span></div>
<div><span style="font-family:SimSun;font-size:14px;">每个agent由3类组件构成（注意不是3个，比如根据业务需求可以通过Flume的配置文件实现单个agent进程的逻辑拓扑中包含多个sources），下面按数据流从前向后的顺序分别进行说明。</span></div>
<p><span style="font-family:SimSun;font-size:14px;"><strong>3.1 Source<br></strong>Source负责接收并解析（如反序列化）来自外部源的events数据，并将解析后的数据发送给与它连接的一个或多个channel(s)。<br><strong>几点说明如下：<br>
1)</strong> 外部源发往Source的数据格式必须与Flume配置文件中指定的Source Type保持一致。比如，若配置Flume的Source类型为thrift，则发来的数据必须按thrift协议打包。<br><strong>2)</strong> 目前Source支持的接收外部源数据的方式包括RPC（如将Source配置为Avro方式时，可通过Avro客户端以RPC方式向Flume发送数据）、Thrift源、HTTP源、Exec源、JMS源、Seq源（类似于计数生成器，它会持续生成event，主要用于测试），等等。具体支持的Source列表及使用实例，可参考官方文档<a href="https://flume.apache.org/FlumeUserGuide.html#flume-sources" rel="nofollow">Flume
 Sources</a>的说明。<br><strong>3)</strong> 在同一个agent进程中，若source配置了多个channels，此时，根据业务需求，可为source配置不同的event路由策略，常见的channel selector包括replicating和multiplexing两种，其中，前者为默认策略，表示来自source的events会同时发往与它连接的所有channels（<span style="background-color:rgb(255,255,51);">显然这种情况会更耗内存或磁盘</span>）；而后者表示source的events只会发送到特定的channel(s)，具体而言，source通过其配置项selector.header指定路由决策字段的key，通过配置项selector.mapping.&lt;hdr-value&gt;指定与hdr-value匹配的events将要发往的channel，其中，&lt;hdr-value&gt;是与决策字段的key关联的value。具体实例可参考<a href="https://flume.apache.org/FlumeUserGuide.html#flume-channel-selectors" rel="nofollow">Flume
 Channel Selectors</a>文档的说明。<br><strong>4)</strong> 可以借助source. Interceptors修改或过滤event，细节可参考文档<a href="https://flume.apache.org/FlumeUserGuide.html#flume-interceptors" rel="nofollow">Flume Interceptors</a>。<br><strong>5)</strong> 自定义实现的Source也可以作为plugin集成到Flume中。</span></p>
<p><span style="font-family:SimSun;font-size:14px;"><strong>3.2 Channel<br></strong>Channel是个被动的存储组件，它会维护一个内存队列或磁盘文件来保存Source发来的event直到该event被Sink消费。也即，它像队列一样连接了sources和sinks。<br>
最常见的Channel类型是Memory Channel和File Channel，前者通过在内存队列中维护events来提高性能，但机器故障或进程退出时会丢失未被Sink消费的数据；而后者通过磁盘文件维护events，可以避免意外情况情况下的数据丢失，但显然性能会打折扣。<br>
除Memory Channel和File Channel外，Flume还支持JBDC Channel及其它的Channel类型，细节可以查看<a href="https://flume.apache.org/FlumeUserGuide.html#flume-channels" rel="nofollow">Flume Channels</a>文档。<br><strong>几点说明如下：<br>
1)</strong> 在使用memory类型的channel时，要意识到最大容量（capacity）问题，如果source生产events的速度超过sink的消费速度，则可能会导致channel缓冲区打满从而抛出异常。这种情况下，若向Source写数据的外部应用程序没有异常处理逻辑（ExecSource最容易出现这种情况），则数据会丢失。<br><strong>2)</strong> 在使用file类型的channel并配置了多个file channels时，最好为每个channel明确配置存放events的、各自独立的文件路径，因为若采用默认的配置路径，则多个channel会竞争同一个文件锁，最终导致只有1个channel能初始化成功。<br><strong>3)</strong> 可以配置memory和file混合的channel类型Spillable Memory Channel，优缺点可以查看文档，这里不赘述。<br><strong>4)</strong> 自定义实现的Channel接口可以作为plugin集成到Flume中。</span></p>
<div><strong><span style="font-family:SimSun;font-size:14px;">3.3 Sink</span></strong></div>
<div><span style="font-family:SimSun;font-size:14px;">Sink负责从channel中消费events，并根据配置的sink类型将数据写入外部的存储系统。</span></div>
<div><span style="font-family:SimSun;font-size:14px;">常见的sink类型包括HDFS Sink、Logger Sink（输出到终端以方便调试）、Avro Sink（如Flume级联的情况）、Thrift Sink、ElasticSearchSink、HBase Sink，等等。此外，从Flume v1.6开始，Flume增加了Kafka Sink。</span></div>
<div><strong><span style="font-family:SimSun;font-size:14px;">几点说明如下：</span></strong></div>
<div><span style="font-family:SimSun;font-size:14px;"><strong>1)</strong> 同一个channel可以连多个sinks，但同一个sink只能从1个channel消费数据。</span></div>
<div><span style="font-family:SimSun;font-size:14px;"><strong>2)</strong> 同一个agent进程可以对sinks做分组，同一个sink group可以根据processor.type配置项实现sink间的failover和load_balance。</span></div>
<div><span style="font-family:SimSun;font-size:14px;"><strong>3)</strong> 自定义实现的Sink接口可以作为plugin集成到Flume中，此外还可以自定义Sink Processor接口。</span></div>
</div>
<div>
<h1><strong><span style="font-family:SimSun;font-size:14px;">4. Flume级联</span></strong></h1>
<div><span style="font-family:SimSun;font-size:14px;">除source-channel-sink一对一的对应关系外，Flume还支持其它形式的系统结构。</span></div>
<div><strong><span style="font-family:SimSun;font-size:14px;">1) 多agent级联</span></strong></div>
</div>
<div><span style="font-family:SimSun;font-size:14px;"><img src="https://img-blog.csdn.net/20150429184438165?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2x2aGVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><strong>2) 多agent聚合级联</strong><br></span></div>
<div><span style="font-family:SimSun;font-size:14px;"><img src="https://img-blog.csdn.net/20150429184707736?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2x2aGVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><strong>3) 多路分流</strong></span></div>
<div><span style="font-family:SimSun;font-size:14px;"><img src="https://img-blog.csdn.net/20150429184717691?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2x2aGVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br>
上图中，source的events可以根据配置分配到不同的channel中，这种方法在上文介绍source的要点说明中曾经描述过，这里不再赘述。<br></span></div>
<div><span style="font-family:SimSun;font-size:14px;"><br></span></div>
<div><span style="font-family:SimSun;font-size:14px;">【参考资料】</span></div>
<div><span style="font-family:SimSun;font-size:14px;">1. <a href="https://flume.apache.org/" rel="nofollow">Apache Flume</a></span></div>
<div><span style="font-family:SimSun;font-size:14px;">2. FlumeWiki - <a href="https://cwiki.apache.org/confluence/display/FLUME/Getting+Started" rel="nofollow">
Getting Started</a><br>
3. Flume1.5.2 User Guide - <a href="https://flume.apache.org/FlumeUserGuide.html#is-flume-a-good-fit-for-your-problem" rel="nofollow">
Is Flume a good fit for your problem?</a><br>
4. </span><span style="font-family:SimSun;font-size:14px;"><a href="https://cwiki.apache.org/confluence/display/FLUME/Flume+NG+Performance+Measurements" rel="nofollow">Flume NG PerformanceMeasurements</a><br>
5. Apache Flume - <a href="https://blogs.apache.org/flume/entry/flume_ng_architecture" rel="nofollow">
Architecture of Flume NG</a><br><br></span></div>
<div><span style="font-family:SimSun;font-size:14px;">======================== EOF ========================= </span></div>
<div><span style="font-family:SimSun;font-size:14px;"><br></span></div>
</div>
<div><br></div>
            </div>
                </div>