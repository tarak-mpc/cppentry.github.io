---
layout:     post
title:      大数据IMF传奇行动绝密课程第86课：SparkStreaming数据源Flume实际案例分享
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="sparkstreaming数据源flume实际案例分享">SparkStreaming数据源Flume实际案例分享</h1>

<p>1、Flume简要介绍 <br>
2、Spark Streaming on Flume案例介绍</p>

<p><img title="" alt="图86-1 Flume简要介绍" src="https://img-blog.csdn.net/20170403192735950?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdG9tXzg4OTlfbGk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast"></p>

<p><img title="" alt="图86-2 Flume常用数据流" src="https://img-blog.csdn.net/20170403192754950?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdG9tXzg4OTlfbGk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast"></p>

<p>一、什么是Flume? <br>
　　flume 作为 cloudera 开发的实时日志收集系统，受到了业界的认可与广泛应用。Flume 初始的发行版本目前被统称为 Flume OG（original generation），属于 cloudera。但随着 FLume 功能的扩展，Flume OG 代码工程臃肿、核心组件设计不合理、核心配置不标准等缺点暴露出来，尤其是在 Flume OG 的最后一个发行版本 0.94.0 中，日志传输不稳定的现象尤为严重，为了解决这些问题，2011 年 10 月 22 号，cloudera 完成了 Flume-728，对 Flume 进行了里程碑式的改动：重构核心组件、核心配置以及代码架构，重构后的版本统称为 Flume NG（next generation）；改动的另一原因是将 Flume 纳入 apache 旗下，cloudera Flume 改名为 Apache Flume。</p>

<p>flume的特点： <br>
　　flume是一个分布式、可靠、和高可用的海量日志采集、聚合和传输的系统。支持在日志系统中定制各类数据发送方，用于收集数据;同时，Flume提供对数据进行简单处理，并写到各种数据接受方(比如文本、HDFS、Hbase等)的能力 。 <br>
　　flume的数据流由事件(Event)贯穿始终。事件是Flume的基本数据单位，它携带日志数据(字节数组形式)并且携带有头信息，这些Event由Agent外部的Source生成，当Source捕获事件后会进行特定的格式化，然后Source会把事件推入(单个或多个)Channel中。你可以把Channel看作是一个缓冲区，它将保存事件直到Sink处理完该事件。Sink负责持久化日志或者把事件推向另一个Source。</p>

<p>flume的可靠性  <br>
　　当节点出现故障时，日志能够被传送到其他节点上而不会丢失。Flume提供了三种级别的可靠性保障，从强到弱依次分别为：end-to-end（收到数据agent首先将event写到磁盘上，当数据传送成功后，再删除；如果数据发送失败，可以重新发送。），Store on failure（这也是scribe采用的策略，当数据接收方crash时，将数据写到本地，待恢复后，继续发送），Besteffort（数据发送到接收方后，不会进行确认）。</p>

<p>flume的可恢复性： <br>
　　还是靠Channel。推荐使用FileChannel，事件持久化在本地文件系统里(性能较差)。 </p>

<p>flume的一些核心概念：</p>

<ol>
<li>Agent        使用JVM 运行Flume。每台机器运行一个agent，但是可以在一个agent中包含多个sources和sinks。</li>
<li>Client        生产数据，运行在一个独立的线程。</li>
<li>Source        从Client收集数据，传递给Channel。</li>
<li>Sink        从Channel收集数据，运行在一个独立线程。</li>
<li>Channel        连接 sources 和 sinks ，这个有点像一个队列。</li>
<li>Events        可以是日志记录、 avro 对象等。</li>
</ol>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>