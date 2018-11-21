---
layout:     post
title:      wang-----Flume NG 简介及配置实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>文章来源：http://blog.csdn.net/haydenwang8287/article/details/45150583</p>
<p><a href="http://www.csdn.net/tag/%E5%A4%A7%E6%95%B0%E6%8D%AE" rel="nofollow" style="color:rgb(51,102,153);text-decoration:none;display:inline-block;line-height:12px;border:1px solid rgb(238,238,238);font-family:Arial, Console, Verdana, 'Courier New';font-size:12px;background-color:rgb(238,238,238);">大数据</a><a href="http://www.csdn.net/tag/flume" rel="nofollow" style="color:rgb(51,102,153);text-decoration:none;display:inline-block;line-height:12px;border:1px solid rgb(238,238,238);font-family:Arial, Console, Verdana, 'Courier New';font-size:12px;background-color:rgb(238,238,238);">flume</a><a href="http://www.csdn.net/tag/cloudera" rel="nofollow" style="color:rgb(51,102,153);text-decoration:none;display:inline-block;line-height:12px;border:1px solid rgb(238,238,238);font-family:Arial, Console, Verdana, 'Courier New';font-size:12px;background-color:rgb(238,238,238);">cloudera</a><br></p>
<p></p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
Flume 作为 cloudera 开发的实时日志收集系统，受到了业界的认可与广泛应用。Flume 初始的发行版本目前被统称为 Flume OG（original generation），属于 cloudera。但随着 FLume 功能的扩展，Flume OG 代码工程臃肿、核心组件设计不合理、核心配置不标准等缺点暴露出来，尤其是在 Flume OG 的最后一个发行版本 0.94.0 中，日志传输不稳定的现象尤为严重，为了解决这些问题，2011 年 10 月 22 号，cloudera 完成了 Flume-728，对
 Flume 进行了里程碑式的改动：重构核心组件、核心配置以及代码架构，重构后的版本统称为 Flume NG（next generation）；改动的另一原因是将 Flume 纳入 apache 旗下，cloudera Flume 改名为 Apache Flume。IBM 的这篇文章：《 <a href="http://www.ibm.com/developerworks/cn/data/library/bd-1404flumerevolution/index.html" rel="nofollow" style="color:rgb(51,102,153);text-decoration:none;">Flume
 NG：Flume 发展史上的第一次革命</a> 》，从基本组件以及用户体验的角度阐述 Flume OG 到 Flume NG 发生的革命性变化。本文就不再赘述各种细枝末节了，不过这里还是简要提下 Flume NG （1.x.x）的主要变化：</p>
<ul style="color:rgb(51,51,51);list-style-type:none;font-size:16px;line-height:27.2000007629395px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);"><li style="line-height:1.7em;list-style-type:disc;">sources和sinks 使用channels 进行链接</li><li style="line-height:1.7em;list-style-type:disc;">两个主要channel 。1，  in-memory channel  非持久性支持，速度快。2 ， JDBC-based channel 持久性支持。</li><li style="line-height:1.7em;list-style-type:disc;">不再区分逻辑和物理node，所有物理节点统称为 “agents”,每个agents 都能运行0个或多个sources 和sinks</li><li style="line-height:1.7em;list-style-type:disc;">不再需要master节点和对zookeeper的依赖，配置文件简单化。</li><li style="line-height:1.7em;list-style-type:disc;">插件化，一部分面对用户，工具或系统开发人员。</li><li style="line-height:1.7em;list-style-type:disc;">使用Thrift、Avro Flume sources 可以从flume0.9.4 发送 events  到flume 1.x</li></ul><p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
注：本文所使用的 Flume 版本为 flume-1.4.0-cdh4.7.0，不需要额外的安装过程，解压缩即可用。 </p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
</p>
<h2 style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-weight:normal;line-height:22px;font-size:20px;text-indent:1em;background-color:rgb(254,254,254);">
<a name="t0" style="color:rgb(51,102,153);"></a>1、Flume 的 一些核心概念：</h2>
<table class="table table-bordered" style="color:rgb(51,51,51);font-size:14px;border-spacing:0px;width:605px;border-width:1px 1px 1px 0px;border-top-style:solid;border-right-style:solid;border-bottom-style:solid;border-top-color:rgb(221,221,221);border-right-color:rgb(221,221,221);border-bottom-color:rgb(221,221,221);line-height:1.3em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);"><tbody><tr style="line-height:1.3em;"><th style="line-height:1.5em;vertical-align:top;border-top-width:0px;border-left-width:1px;border-left-style:solid;border-left-color:rgb(221,221,221);">
组件</th>
<th style="line-height:1.5em;vertical-align:top;border-top-width:0px;border-left-width:1px;border-left-style:solid;border-left-color:rgb(221,221,221);">
功能</th>
</tr><tr style="line-height:1.3em;"><td style="line-height:1.3em;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-left-width:1px;border-left-style:solid;border-left-color:rgb(221,221,221);font-size:14px;">
Agent</td>
<td style="line-height:1.3em;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-left-width:1px;border-left-style:solid;border-left-color:rgb(221,221,221);font-size:14px;">
使用JVM 运行Flume。每台机器运行一个agent，但是可以在一个agent中包含多个sources和sinks。</td>
</tr><tr style="line-height:1.3em;"><td style="line-height:1.3em;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-left-width:1px;border-left-style:solid;border-left-color:rgb(221,221,221);font-size:14px;">
Client</td>
<td style="line-height:1.3em;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-left-width:1px;border-left-style:solid;border-left-color:rgb(221,221,221);font-size:14px;">
生产数据，运行在一个独立的线程。</td>
</tr><tr style="line-height:1.3em;"><td style="line-height:1.3em;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-left-width:1px;border-left-style:solid;border-left-color:rgb(221,221,221);font-size:14px;">
Source</td>
<td style="line-height:1.3em;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-left-width:1px;border-left-style:solid;border-left-color:rgb(221,221,221);font-size:14px;">
从Client收集数据，传递给Channel。</td>
</tr><tr style="line-height:1.3em;"><td style="line-height:1.3em;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-left-width:1px;border-left-style:solid;border-left-color:rgb(221,221,221);font-size:14px;">
Sink</td>
<td style="line-height:1.3em;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-left-width:1px;border-left-style:solid;border-left-color:rgb(221,221,221);font-size:14px;">
从Channel收集数据，运行在一个独立线程。</td>
</tr><tr style="line-height:1.3em;"><td style="line-height:1.3em;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-left-width:1px;border-left-style:solid;border-left-color:rgb(221,221,221);font-size:14px;">
Channel</td>
<td style="line-height:1.3em;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-left-width:1px;border-left-style:solid;border-left-color:rgb(221,221,221);font-size:14px;">
连接 sources 和 sinks ，这个有点像一个队列。</td>
</tr><tr style="line-height:1.3em;"><td style="line-height:1.3em;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-left-width:1px;border-left-style:solid;border-left-color:rgb(221,221,221);font-size:14px;">
Events</td>
<td style="line-height:1.3em;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-left-width:1px;border-left-style:solid;border-left-color:rgb(221,221,221);font-size:14px;">
可以是日志记录、 avro 对象等。</td>
</tr></tbody></table><h2 style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-weight:normal;line-height:22px;font-size:20px;text-indent:1em;background-color:rgb(254,254,254);">
<a name="t1" style="color:rgb(51,102,153);"></a>1.1 数 据流模型</h2>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
Flume以agent为最小的独立运行单位。一个agent就是一个JVM。单agent由Source、Sink和Channel三大组件构成，如下图：</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<img src="http://img0.tuicool.com/ueima2.png" alt="Agent component diagram" class="alignCenter" style="border:0px none;vertical-align:middle;text-align:center;display:block;">  图一</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
Flume的数据流由事件(Event)贯穿始终。事件是Flume的基本数据单位，它携带日志数据(字节数组形式)并且携带有头信息，这些Event由Agent外部的Source，比如上图中的Web Server生成。当Source捕获事件后会进行特定的格式化，然后Source会把事件推入(单个或多个)Channel中。你可以把Channel看作是一个缓冲区，它将保存事件直到Sink处理完该事件。Sink负责持久化日志或者把事件推向另一个Source。</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
很直白的设计，其中值得注意的是，Flume提供了大量内置的Source、Channel和Sink类型。不同类型的Source,Channel和Sink可以自由组合。组合方式基于用户设置的配置文件，非常灵活。比如：Channel可以把事件暂存在内存里，也可以持久化到本地硬盘上。Sink可以把日志写入HDFS, HBase，甚至是另外一个Source等等。</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
如果你以为Flume就这些能耐那就大错特错了。Flume支持用户建立多级流，也就是说，多个agent可以协同工作，并且支持Fan-in、Fan-out、Contextual Routing、Backup Routes。如下图所示：</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<img src="http://img2.tuicool.com/i6FvAn.png" alt="A fan-out flow using a (multiplexing) channel selector" class="alignCenter" style="border:0px none;vertical-align:middle;text-align:center;display:block;"></p>
<h2 style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-weight:normal;line-height:22px;font-size:20px;text-indent:1em;background-color:rgb(254,254,254);">
<a name="t2" style="color:rgb(51,102,153);"></a>1.2 高可靠性</h2>
<span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-size:16px;line-height:27.2000007629395px;background-color:rgb(254,254,254);"></span><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"></span>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:1.7em;text-indent:1em;">
作为生产环境运行的软件，高可靠性是必须的。</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:1.7em;text-indent:1em;">
从单agent来看，Flume使用基于事务的数据传递方式来保证事件传递的可靠性。Source和Sink被封装进一个事务。事件被存放在Channel中直到该事件被处理，Channel中的事件才会被移除。这是Flume提供的点到点的可靠机制。</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:1.7em;text-indent:1em;">
从多级流来看，前一个agent的sink和后一个agent的source同样有它们的事务来保障数据的可靠性。</p>
<span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-size:16px;line-height:27.2000007629395px;background-color:rgb(254,254,254);"></span><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"></span>
<h2 style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-weight:normal;line-height:22px;font-size:20px;text-indent:1em;background-color:rgb(254,254,254);">
<a name="t3" style="color:rgb(51,102,153);"></a>1.3 可恢复性</h2>
<span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-size:16px;line-height:27.2000007629395px;background-color:rgb(254,254,254);"></span><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"></span>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:1.7em;text-indent:1em;">
还是靠Channel。推荐使用FileChannel，事件持久化在本地文件系统里(性能较差)。</p>
<span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-size:16px;line-height:27.2000007629395px;background-color:rgb(254,254,254);"></span><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"></span>
<h2 style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-weight:normal;line-height:22px;font-size:20px;text-indent:1em;background-color:rgb(254,254,254);">
<a name="t4" style="color:rgb(51,102,153);"></a>2、Flume 整体架构介绍</h2>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
Flume架构整体上看就是  source --&gt;c hannel --&gt; sink  的三层架构（参见最上面的 图一），类似生成者和消费者的架构，他们之间通过queue（channel）传输，解耦。</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
Source:完成对日志数据的收集，分成 transtion 和 event 打入到channel之中。 </p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
Channel:主要提供一个队列的功能，对source提供中的数据进行简单的缓存。 </p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
Sink:取出Channel中的数据，进行相应的存储文件系统，数据库，或者提交到远程服务器。 </p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
对现有程序改动最小的使用方式是使用是直接读取程序原来记录的日志文件，基本可以实现无缝接入，不需要对现有程序进行任何改动。 </p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
对于直接读取文件Source, 主要有两种方式： </p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
</p>
<h2 style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-weight:normal;line-height:22px;font-size:20px;text-indent:1em;background-color:rgb(254,254,254);">
<a name="t5" style="color:rgb(51,102,153);"></a>2.1 Exec source</h2>
<span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-size:16px;line-height:27.2000007629395px;background-color:rgb(254,254,254);"></span><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"></span>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:1.7em;text-indent:1em;">
可通过写Unix command的方式组织数据，最常用的就是tail -F [file]。</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:1.7em;text-indent:1em;">
可以实现实时传输，但在flume不运行和脚本错误时，会丢数据，也不支持断点续传功能。因为没有记录上次文件读到的位置，从而没办法知道，下次再读时，从什么地方开始读。特别是在日志文件一直在增加的时候。flume的source挂了。等flume的source再次开启的这段时间内，增加的日志内容，就没办法被source读取到了。不过flume有一个execStream的扩展，可以自己写一个监控日志增加情况，把增加的日志，通过自己写的工具把增加的内容，传送给flume的node。再传送给sink的node。要是能在tail类的source中能支持，在node挂掉这段时间的内容，等下次node开启后在继续传送，那就更完美了。</p>
<span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-size:16px;line-height:27.2000007629395px;background-color:rgb(254,254,254);"></span><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"></span>
<h2 style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-weight:normal;line-height:22px;font-size:20px;text-indent:1em;background-color:rgb(254,254,254);">
<a name="t6" style="color:rgb(51,102,153);"></a>2.2 Spooling Directory Source</h2>
<span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-size:16px;line-height:27.2000007629395px;background-color:rgb(254,254,254);"></span><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"></span>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:1.7em;text-indent:1em;">
SpoolSource:是监测配置的目录下新增的文件，并将文件中的数据读取出来，可实现准实时。需要注意两点：1、拷贝到spool目录下的文件不可以再打开编辑。2、spool目录下不可包含相应的子目录。在实际使用的过程中，可以结合log4j使用，使用log4j的时候，将log4j的文件分割机制设为1分钟一次，将文件拷贝到spool的监控目录。log4j有一个TimeRolling的插件，可以把log4j分割的文件到spool目录。基本实现了实时的监控。Flume在传完文件之后，将会修改文件的后缀，变为.COMPLETED（后缀也可以在配置文件中灵活指定） </p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:1.7em;text-indent:1em;">
ExecSource，SpoolSource对比：ExecSource可以实现对日志的实时收集，但是存在Flume不运行或者指令执行出错时，将无法收集到日志数据，无法何证日志数据的完整性。SpoolSource虽然无法实现实时的收集数据，但是可以使用以分钟的方式分割文件，趋近于实时。如果应用无法实现以分钟切割日志文件的话，可以两种收集方式结合使用。 </p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:1.7em;text-indent:1em;">
Channel有多种方式：有MemoryChannel, JDBC Channel, MemoryRecoverChannel, FileChannel。MemoryChannel可以实现高速的吞吐，但是无法保证数据的完整性。MemoryRecoverChannel在官方文档的建议上已经建义使用FileChannel来替换。FileChannel保证数据的完整性与一致性。在具体配置FileChannel时，建议FileChannel设置的目录和程序日志文件保存的目录设成不同的磁盘，以便提高效率。 </p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:1.7em;text-indent:1em;">
Sink在设置存储数据时，可以向文件系统中，数据库中，hadoop中储数据，在日志数据较少时，可以将数据存储在文件系中，并且设定一定的时间间隔保存数据。在日志数据较多时，可以将相应的日志数据存储到Hadoop中，便于日后进行相应的数据分析。 </p>
<span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-size:16px;line-height:27.2000007629395px;background-color:rgb(254,254,254);"></span><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"></span>
<h2 style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-weight:normal;line-height:22px;font-size:20px;text-indent:1em;background-color:rgb(254,254,254);">
<a name="t7" style="color:rgb(51,102,153);"></a>3、常用架构、功能配 置示例</h2>
<h2 style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-weight:normal;line-height:22px;font-size:20px;text-indent:1em;background-color:rgb(254,254,254);">
<a name="t8" style="color:rgb(51,102,153);"></a>3.1 先来个简 单的：单节点 Flume 配置</h2>
<pre class="prettyprint perl" style="color:rgb(51,51,51);font-size:14px;font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;line-height:1.5em;background-color:rgb(246,246,246);">&lt;span class="comment" style="color: rgb(153, 153, 136); font-style: italic;"&gt;# example.conf: A single-node Flume configuration&lt;/span&gt;

&lt;span class="comment" style="color: rgb(153, 153, 136); font-style: italic;"&gt;# Name the components on this agent&lt;/span&gt;
a1.sources = r1
a1.sinks = k1
a1.channels = c1

&lt;span class="comment" style="color: rgb(153, 153, 136); font-style: italic;"&gt;# Describe/configure the source&lt;/span&gt;
a1.sources.r1.type = netcat
a1.sources.r1.&lt;span class="keyword" style="font-weight: bold;"&gt;bind&lt;/span&gt; = localhost
a1.sources.r1.port = &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;44444&lt;/span&gt;

&lt;span class="comment" style="color: rgb(153, 153, 136); font-style: italic;"&gt;# Describe the sink&lt;/span&gt;
a1.sinks.k1.type = logger

&lt;span class="comment" style="color: rgb(153, 153, 136); font-style: italic;"&gt;# Use a channel which buffers events in memory&lt;/span&gt;
a1.channels.c1.type = memory
a1.channels.c1.capacity = &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;1000&lt;/span&gt;
a1.channels.c1.transactionCapacity = &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;100&lt;/span&gt;

&lt;span class="comment" style="color: rgb(153, 153, 136); font-style: italic;"&gt;# Bind the source and sink to the channel&lt;/span&gt;
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1</pre><span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-size:16px;line-height:27.2000007629395px;background-color:rgb(254,254,254);">将上述配置存为：example.conf</span><span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-size:16px;line-height:27.2000007629395px;background-color:rgb(254,254,254);"></span><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"></span>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
然后我们就可以启动 Flume 了：</p>
<pre><code class="language-sql">bin/flume-ng agent &lt;span class="comment" style="color: rgb(153, 153, 136); font-style: italic;"&gt;--conf conf --conf-file example.conf --name a1 -Dflume.root.logger=INFO,console&lt;/span&gt;</code></pre>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
PS：-Dflume.root.logger=INFO,console 仅为 debug 使用，请勿生产环境生搬硬套，否则大量的日志会返回到终端。。。</p>
<span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-size:16px;line-height:27.2000007629395px;background-color:rgb(254,254,254);">然后我们再开一个 shell 终端窗口，telnet 上配置中侦听的端口，就可以发消息看到效果了：</span><span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-size:16px;line-height:27.2000007629395px;background-color:rgb(254,254,254);"></span><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"></span><pre><code class="language-javascript">$ telnet localhost &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;44444&lt;/span&gt;
Trying &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;127.0&lt;/span&gt;&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;.0&lt;/span&gt;&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;.1&lt;/span&gt;...
Connected to localhost.localdomain (&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;127.0&lt;/span&gt;&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;.0&lt;/span&gt;&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;.1&lt;/span&gt;).
Escape character is &lt;span class="string" style="color: rgb(221, 17, 68);"&gt;'^]'&lt;/span&gt;.
Hello world! &lt;span class="xml"&gt;&lt;span class="tag" style="color: rgb(0, 0, 128);"&gt;&lt;&lt;span class="title"&gt;ENTER&lt;/span&gt;&gt;&lt;/span&gt;
OK&lt;/span&gt;</code></pre><span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-size:16px;line-height:27.2000007629395px;background-color:rgb(254,254,254);">Flume
 终端窗口此时会打印出如下信息，就表示成功了：</span><span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-size:16px;line-height:27.2000007629395px;background-color:rgb(254,254,254);"></span><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"></span><pre><code class="language-cpp">&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;12&lt;/span&gt;/&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;06&lt;/span&gt;/&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;19&lt;/span&gt; &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;15&lt;/span&gt;:&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;32&lt;/span&gt;:&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;19&lt;/span&gt; INFO source.NetcatSource: Source starting
&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;12&lt;/span&gt;/&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;06&lt;/span&gt;/&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;19&lt;/span&gt; &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;15&lt;/span&gt;:&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;32&lt;/span&gt;:&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;19&lt;/span&gt; INFO source.NetcatSource: Created serverSocket:sun.nio.ch.ServerSocketChannelImpl[/&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;127.0&lt;/span&gt;&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;.0&lt;/span&gt;&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;.1&lt;/span&gt;:&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;44444&lt;/span&gt;]
&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;12&lt;/span&gt;/&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;06&lt;/span&gt;/&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;19&lt;/span&gt; &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;15&lt;/span&gt;:&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;32&lt;/span&gt;:&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;34&lt;/span&gt; INFO sink.LoggerSink: Event: { headers:{} body: &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;48&lt;/span&gt; &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;65&lt;/span&gt; &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;6&lt;/span&gt;C &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;6&lt;/span&gt;C &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;6F&lt;/span&gt; &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;20&lt;/span&gt; &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;77&lt;/span&gt; &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;6F&lt;/span&gt; &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;72&lt;/span&gt; &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;6&lt;/span&gt;C &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;64&lt;/span&gt; &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;21&lt;/span&gt; &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;0&lt;/span&gt;D          Hello world!. }</code></pre><span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-size:16px;line-height:27.2000007629395px;background-color:rgb(254,254,254);">自此，咱们的第一个
 Flume Agent 算是部署成功了！</span><span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-size:16px;line-height:27.2000007629395px;background-color:rgb(254,254,254);"></span><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"></span>
<h2 style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-weight:normal;line-height:22px;font-size:20px;text-indent:1em;background-color:rgb(254,254,254);">
<a name="t9" style="color:rgb(51,102,153);"></a>3.2 单节点 Flume 直接写入 HDFS</h2>
<pre class="prettyprint perl" style="color:rgb(51,51,51);font-size:14px;font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;line-height:1.5em;background-color:rgb(246,246,246);">&lt;span class="comment" style="color: rgb(153, 153, 136); font-style: italic;"&gt;# Define a memory channel called ch1 on agent1&lt;/span&gt;
agent1.channels.ch1.type = memory
agent1.channels.ch1.capacity = &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;100000&lt;/span&gt;
agent1.channels.ch1.transactionCapacity = &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;100000&lt;/span&gt;
agent1.channels.ch1.keep-alive = &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;30&lt;/span&gt;

&lt;span class="comment" style="color: rgb(153, 153, 136); font-style: italic;"&gt;# Define an Avro source called avro-source1 on agent1 and tell it&lt;/span&gt;
&lt;span class="comment" style="color: rgb(153, 153, 136); font-style: italic;"&gt;# to bind to 0.0.0.0:41414. Connect it to channel ch1.&lt;/span&gt;
&lt;span class="comment" style="color: rgb(153, 153, 136); font-style: italic;"&gt;#agent1.sources.avro-source1.channels = ch1&lt;/span&gt;
&lt;span class="comment" style="color: rgb(153, 153, 136); font-style: italic;"&gt;#agent1.sources.avro-source1.type = avro&lt;/span&gt;
&lt;span class="comment" style="color: rgb(153, 153, 136); font-style: italic;"&gt;#agent1.sources.avro-source1.bind = 0.0.0.0&lt;/span&gt;
&lt;span class="comment" style="color: rgb(153, 153, 136); font-style: italic;"&gt;#agent1.sources.avro-source1.port = 41414&lt;/span&gt;
&lt;span class="comment" style="color: rgb(153, 153, 136); font-style: italic;"&gt;#agent1.sources.avro-source1.threads = 5&lt;/span&gt;

&lt;span class="comment" style="color: rgb(153, 153, 136); font-style: italic;"&gt;#define source monitor a file&lt;/span&gt;
agent1.sources.avro-source1.type = &lt;span class="keyword" style="font-weight: bold;"&gt;exec&lt;/span&gt;
agent1.sources.avro-source1.shell = &lt;span class="regexp" style="color: rgb(0, 153, 38);"&gt;/bin/bash&lt;/span&gt; -c
agent1.sources.avro-source1.command = tail -n +&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;0&lt;/span&gt; -F /home/storm/tmp/id.txt
agent1.sources.avro-source1.channels = ch1
agent1.sources.avro-source1.threads = &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;5&lt;/span&gt;

&lt;span class="comment" style="color: rgb(153, 153, 136); font-style: italic;"&gt;# Define a logger sink that simply logs all events it receives&lt;/span&gt;
&lt;span class="comment" style="color: rgb(153, 153, 136); font-style: italic;"&gt;# and connect it to the other end of the same channel.&lt;/span&gt;
agent1.sinks.&lt;span class="keyword" style="font-weight: bold;"&gt;log&lt;/span&gt;-sink1.channel = ch1
agent1.sinks.&lt;span class="keyword" style="font-weight: bold;"&gt;log&lt;/span&gt;-sink1.type = hdfs
agent1.sinks.&lt;span class="keyword" style="font-weight: bold;"&gt;log&lt;/span&gt;-sink1.hdfs.path = hdfs:&lt;span class="regexp" style="color: rgb(0, 153, 38);"&gt;//&lt;/span&gt;&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;192.168&lt;/span&gt;.&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;1.111&lt;/span&gt;:&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;8020&lt;/span&gt;/flumeTest
agent1.sinks.&lt;span class="keyword" style="font-weight: bold;"&gt;log&lt;/span&gt;-sink1.hdfs.writeFormat = Text
agent1.sinks.&lt;span class="keyword" style="font-weight: bold;"&gt;log&lt;/span&gt;-sink1.hdfs.fileType = DataStream
agent1.sinks.&lt;span class="keyword" style="font-weight: bold;"&gt;log&lt;/span&gt;-sink1.hdfs.rollInterval = &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;0&lt;/span&gt;
agent1.sinks.&lt;span class="keyword" style="font-weight: bold;"&gt;log&lt;/span&gt;-sink1.hdfs.rollSize = &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;1000000&lt;/span&gt;
agent1.sinks.&lt;span class="keyword" style="font-weight: bold;"&gt;log&lt;/span&gt;-sink1.hdfs.rollCount = &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;0&lt;/span&gt;
agent1.sinks.&lt;span class="keyword" style="font-weight: bold;"&gt;log&lt;/span&gt;-sink1.hdfs.batchSize = &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;1000&lt;/span&gt;
agent1.sinks.&lt;span class="keyword" style="font-weight: bold;"&gt;log&lt;/span&gt;-sink1.hdfs.txnEventMax = &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;1000&lt;/span&gt;
agent1.sinks.&lt;span class="keyword" style="font-weight: bold;"&gt;log&lt;/span&gt;-sink1.hdfs.callTimeout = &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;60000&lt;/span&gt;
agent1.sinks.&lt;span class="keyword" style="font-weight: bold;"&gt;log&lt;/span&gt;-sink1.hdfs.appendTimeout = &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;60000&lt;/span&gt;

&lt;span class="comment" style="color: rgb(153, 153, 136); font-style: italic;"&gt;# Finally, now that we've defined all of our components, tell&lt;/span&gt;
&lt;span class="comment" style="color: rgb(153, 153, 136); font-style: italic;"&gt;# agent1 which ones we want to activate.&lt;/span&gt;
agent1.channels = ch1
agent1.sources = avro-source1
agent1.sinks = &lt;span class="keyword" style="font-weight: bold;"&gt;log&lt;/span&gt;-sink1</pre><span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-size:16px;line-height:27.2000007629395px;background-color:rgb(254,254,254);">启动如下命令，就可以在 hdfs 上看到效果了。</span><span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-size:16px;line-height:27.2000007629395px;background-color:rgb(254,254,254);"></span><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"></span>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
../bin/flume-ng agent --conf ../conf/ -f flume_directHDFS.conf -n agent1 -Dflume.root.logger=INFO,console</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
PS：实际环境中有这样的需求，通过在多个agent端tail日志，发送给collector，collector再把数据收集，统一发送给HDFS存储起来，当HDFS文件大小超过一定的大小或者超过在规定的时间间隔会生成一个文件。</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
Flume 实现了两个Trigger，分别为SizeTriger（在调用HDFS输出流写的同时，count该流已经写入的大小总和，若超过一定大小，则创建新的文件和输出流，写入操作指向新的输出流，同时close以前的输出流）和TimeTriger（开启定时器，当到达该点时，自动创建新的文件和输出流，新的写入重定向到该流中，同时close以前的输出流）。</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
</p>
<h2 style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-weight:normal;line-height:22px;font-size:20px;text-indent:1em;background-color:rgb(254,254,254);">
<a name="t10" style="color:rgb(51,102,153);"></a>3.3 来一个常见架 构：多 agent 汇聚写入 HDFS</h2>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<img src="http://img0.tuicool.com/2aIj6z.png" alt="A fan-in flow using Avro RPC to consolidate events in one place" class="alignCenter" style="border:0px none;vertical-align:middle;text-align:center;display:block;"></p>
<h3 style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-weight:normal;line-height:20px;font-size:18px;text-indent:1em;background-color:rgb(254,254,254);">
<a name="t11" style="color:rgb(51,102,153);"></a>3.3.1 在各个webserv日志机上配置 Flume Client</h3>
<pre class="prettyprint cs" style="color:rgb(51,51,51);font-size:14px;font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;line-height:1.5em;background-color:rgb(246,246,246);">&lt;span class="preprocessor" style="color: rgb(153, 153, 153); font-weight: bold;"&gt;# clientMainAgent&lt;/span&gt;
clientMainAgent.channels = c1
clientMainAgent.sources  = s1
clientMainAgent.sinks    = k1 k2
&lt;span class="preprocessor" style="color: rgb(153, 153, 153); font-weight: bold;"&gt;# clientMainAgent sinks group&lt;/span&gt;
clientMainAgent.sinkgroups = g1
&lt;span class="preprocessor" style="color: rgb(153, 153, 153); font-weight: bold;"&gt;# clientMainAgent Spooling Directory Source&lt;/span&gt;
clientMainAgent.sources.s1.type = spooldir
clientMainAgent.sources.s1.spoolDir  =/dsap/rawdata/
clientMainAgent.sources.s1.fileHeader = &lt;span class="keyword" style="font-weight: bold;"&gt;true&lt;/span&gt;
clientMainAgent.sources.s1.deletePolicy =immediate
clientMainAgent.sources.s1.batchSize =&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;1000&lt;/span&gt;
clientMainAgent.sources.s1.channels =c1
clientMainAgent.sources.s1.deserializer.maxLineLength =&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;1048576&lt;/span&gt;
&lt;span class="preprocessor" style="color: rgb(153, 153, 153); font-weight: bold;"&gt;# clientMainAgent FileChannel&lt;/span&gt;
clientMainAgent.channels.c1.type = file
clientMainAgent.channels.c1.checkpointDir = /&lt;span class="keyword" style="font-weight: bold;"&gt;var&lt;/span&gt;/flume/fchannel/spool/checkpoint
clientMainAgent.channels.c1.dataDirs = /&lt;span class="keyword" style="font-weight: bold;"&gt;var&lt;/span&gt;/flume/fchannel/spool/data
clientMainAgent.channels.c1.capacity = &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;200000000&lt;/span&gt;
clientMainAgent.channels.c1.keep-alive = &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;30&lt;/span&gt;
clientMainAgent.channels.c1.write-timeout = &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;30&lt;/span&gt;
clientMainAgent.channels.c1.checkpoint-timeout=&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;600&lt;/span&gt;
&lt;span class="preprocessor" style="color: rgb(153, 153, 153); font-weight: bold;"&gt;# clientMainAgent Sinks&lt;/span&gt;
&lt;span class="preprocessor" style="color: rgb(153, 153, 153); font-weight: bold;"&gt;# k1 sink&lt;/span&gt;
clientMainAgent.sinks.k1.channel = c1
clientMainAgent.sinks.k1.type = avro
&lt;span class="preprocessor" style="color: rgb(153, 153, 153); font-weight: bold;"&gt;# connect to CollectorMainAgent&lt;/span&gt;
clientMainAgent.sinks.k1.hostname = flume115
clientMainAgent.sinks.k1.port = &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;41415&lt;/span&gt; 
&lt;span class="preprocessor" style="color: rgb(153, 153, 153); font-weight: bold;"&gt;# k2 sink&lt;/span&gt;
clientMainAgent.sinks.k2.channel = c1
clientMainAgent.sinks.k2.type = avro
&lt;span class="preprocessor" style="color: rgb(153, 153, 153); font-weight: bold;"&gt;# connect to CollectorBackupAgent&lt;/span&gt;
clientMainAgent.sinks.k2.hostname = flume116
clientMainAgent.sinks.k2.port = &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;41415&lt;/span&gt;
&lt;span class="preprocessor" style="color: rgb(153, 153, 153); font-weight: bold;"&gt;# clientMainAgent sinks group&lt;/span&gt;
clientMainAgent.sinkgroups.g1.sinks = k1 k2
&lt;span class="preprocessor" style="color: rgb(153, 153, 153); font-weight: bold;"&gt;# load_balance type&lt;/span&gt;
clientMainAgent.sinkgroups.g1.processor.type = load_balance
clientMainAgent.sinkgroups.g1.processor.backoff   = &lt;span class="keyword" style="font-weight: bold;"&gt;true&lt;/span&gt;
clientMainAgent.sinkgroups.g1.processor.selector  = random</pre>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
../bin/flume-ng agent --conf ../conf/ -f flume_Consolidation.conf -n clientMainAgent -Dflume.root.logger=DEBUG,console</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
</p>
<h3 style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-weight:normal;line-height:20px;font-size:18px;text-indent:1em;background-color:rgb(254,254,254);">
<a name="t12" style="color:rgb(51,102,153);"></a>3.3.2 在汇聚节点配置 Flume server</h3>
<pre class="prettyprint cs" style="color:rgb(51,51,51);font-size:14px;font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;line-height:1.5em;background-color:rgb(246,246,246);">&lt;span class="preprocessor" style="color: rgb(153, 153, 153); font-weight: bold;"&gt;# collectorMainAgent&lt;/span&gt;
collectorMainAgent.channels = c2
collectorMainAgent.sources  = s2
collectorMainAgent.sinks    =k1 k2
&lt;span class="preprocessor" style="color: rgb(153, 153, 153); font-weight: bold;"&gt;# collectorMainAgent AvroSource&lt;/span&gt;
&lt;span class="preprocessor" style="color: rgb(153, 153, 153); font-weight: bold;"&gt;#&lt;/span&gt;
collectorMainAgent.sources.s2.type = avro
collectorMainAgent.sources.s2.bind = flume115
collectorMainAgent.sources.s2.port = &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;41415&lt;/span&gt;
collectorMainAgent.sources.s2.channels = c2

&lt;span class="preprocessor" style="color: rgb(153, 153, 153); font-weight: bold;"&gt;# collectorMainAgent FileChannel&lt;/span&gt;
&lt;span class="preprocessor" style="color: rgb(153, 153, 153); font-weight: bold;"&gt;#&lt;/span&gt;
collectorMainAgent.channels.c2.type = file
collectorMainAgent.channels.c2.checkpointDir =/opt/&lt;span class="keyword" style="font-weight: bold;"&gt;var&lt;/span&gt;/flume/fchannel/spool/checkpoint
collectorMainAgent.channels.c2.dataDirs = /opt/&lt;span class="keyword" style="font-weight: bold;"&gt;var&lt;/span&gt;/flume/fchannel/spool/data,/work/flume/fchannel/spool/data
collectorMainAgent.channels.c2.capacity = &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;200000000&lt;/span&gt;
collectorMainAgent.channels.c2.transactionCapacity=&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;6000&lt;/span&gt;
collectorMainAgent.channels.c2.checkpointInterval=&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;60000&lt;/span&gt;
&lt;span class="preprocessor" style="color: rgb(153, 153, 153); font-weight: bold;"&gt;# collectorMainAgent hdfsSink&lt;/span&gt;
collectorMainAgent.sinks.k2.type = hdfs
collectorMainAgent.sinks.k2.channel = c2
collectorMainAgent.sinks.k2.hdfs.path = hdfs:&lt;span class="comment" style="color: rgb(153, 153, 136); font-style: italic;"&gt;//db-cdh-cluster/flume%{dir}&lt;/span&gt;
collectorMainAgent.sinks.k2.hdfs.filePrefix =k2_%{file}
collectorMainAgent.sinks.k2.hdfs.inUsePrefix =_
collectorMainAgent.sinks.k2.hdfs.inUseSuffix =.tmp
collectorMainAgent.sinks.k2.hdfs.rollSize = &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;0&lt;/span&gt;
collectorMainAgent.sinks.k2.hdfs.rollCount = &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;0&lt;/span&gt;
collectorMainAgent.sinks.k2.hdfs.rollInterval = &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;240&lt;/span&gt;
collectorMainAgent.sinks.k2.hdfs.writeFormat = Text
collectorMainAgent.sinks.k2.hdfs.fileType = DataStream
collectorMainAgent.sinks.k2.hdfs.batchSize = &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;6000&lt;/span&gt;
collectorMainAgent.sinks.k2.hdfs.callTimeout = &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;60000&lt;/span&gt;
collectorMainAgent.sinks.k1.type = hdfs
collectorMainAgent.sinks.k1.channel = c2
collectorMainAgent.sinks.k1.hdfs.path = hdfs:&lt;span class="comment" style="color: rgb(153, 153, 136); font-style: italic;"&gt;//db-cdh-cluster/flume%{dir}&lt;/span&gt;
collectorMainAgent.sinks.k1.hdfs.filePrefix =k1_%{file}
collectorMainAgent.sinks.k1.hdfs.inUsePrefix =_
collectorMainAgent.sinks.k1.hdfs.inUseSuffix =.tmp
collectorMainAgent.sinks.k1.hdfs.rollSize = &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;0&lt;/span&gt;
collectorMainAgent.sinks.k1.hdfs.rollCount = &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;0&lt;/span&gt;
collectorMainAgent.sinks.k1.hdfs.rollInterval = &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;240&lt;/span&gt;
collectorMainAgent.sinks.k1.hdfs.writeFormat = Text
collectorMainAgent.sinks.k1.hdfs.fileType = DataStream
collectorMainAgent.sinks.k1.hdfs.batchSize = &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;6000&lt;/span&gt;
collectorMainAgent.sinks.k1.hdfs.callTimeout = &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;60000&lt;/span&gt;</pre>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
../bin/flume-ng agent --conf ../conf/ -f flume_Consolidation.conf -n collectorMainAgent -Dflume.root.logger=DEBUG,console</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
</p>
<span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-size:16px;line-height:27.2000007629395px;background-color:rgb(254,254,254);">上面采用的就是类似 cs 架构，各个 flume agent 节点先将各台机器的日志汇总到 Consolidation
 节点，然后再由这些节点统一写入 HDFS，并且采用了负载均衡的方式，你还可以配置高可用的模式等等。</span><span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-size:16px;line-height:27.2000007629395px;background-color:rgb(254,254,254);"></span><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"></span>
<h2 style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-weight:normal;line-height:22px;font-size:20px;text-indent:1em;background-color:rgb(254,254,254);">
<a name="t13" style="color:rgb(51,102,153);"></a>4、可能遇到的问题：</h2>
<h2 style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-weight:normal;line-height:22px;font-size:20px;text-indent:1em;background-color:rgb(254,254,254);">
<a name="t14" style="color:rgb(51,102,153);"></a>4.1 OOM 问题：</h2>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
flume 报错 java.lang.OutOfMemoryError: GC overhead limit exceeded</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
或者：java.lang.OutOfMemoryError: Java heap space</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
Exception in thread "SinkRunner-PollingRunner-DefaultSinkProcessor" java.lang.OutOfMemoryError: Java heap space</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
试试在 .bashrc 或 env.sh 中添加启动参数: </p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
export JAVA_OPTS="-Xms8192m -Xmx8192m -Xss256k -Xmn2g -XX:+UseParNewGC -XX:+UseConcMarkSweepGC -XX:-UseGCOverheadLimit"</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
具体参见：</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<a href="http://stackoverflow.com/questions/1393486/error-java-lang-outofmemoryerror-gc-overhead-limit-exceeded" rel="nofollow" style="color:rgb(51,102,153);text-decoration:none;">http://stackoverflow.com/questions/1393486/error-java-lang-outofmemoryerror-gc-overhead-limit-exceeded</a></p>
<h2 style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-weight:normal;line-height:22px;font-size:20px;text-indent:1em;background-color:rgb(254,254,254);">
<a name="t15" style="color:rgb(51,102,153);"></a>4.2 JDK 版本不兼容问题：</h2>
<pre><code class="language-php">&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;2014&lt;/span&gt;-&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;07&lt;/span&gt;-&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;07&lt;/span&gt; &lt;span class="number" style="color: rgb(0, 153, 153);"&gt;14&lt;/span&gt;:&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;44&lt;/span&gt;:&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;17&lt;/span&gt;,&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;902&lt;/span&gt; (agent-shutdown-hook) [WARN - org.apache.flume.sink.hdfs.HDFSEventSink.stop(HDFSEventSink.java:&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;504&lt;/span&gt;)] &lt;span class="keyword" style="font-weight: bold;"&gt;Exception&lt;/span&gt; &lt;span class="keyword" style="font-weight: bold;"&gt;while&lt;/span&gt; closing hdfs:&lt;span class="comment" style="color: rgb(153, 153, 136); font-style: italic;"&gt;//192.168.1.111:8020/flumeTest/FlumeData. Exception follows.&lt;/span&gt;
java.lang.UnsupportedOperationException: &lt;span class="keyword" style="font-weight: bold;"&gt;This&lt;/span&gt; is supposed to be overridden by subclasses.
&lt;span class="indent"&gt;  &lt;/span&gt;at com.google.protobuf.GeneratedMessage.getUnknownFields(GeneratedMessage.java:&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;180&lt;/span&gt;)
&lt;span class="indent"&gt;  &lt;/span&gt;at org.apache.hadoop.hdfs.protocol.proto.ClientNamenodeProtocolProtos&lt;span class="variable" style="color: rgb(0, 128, 128);"&gt;$GetFileInfoRequestProto&lt;/span&gt;.getSerializedSize(ClientNamenodeProtocolProtos.java:&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;30108&lt;/span&gt;)
&lt;span class="indent"&gt;  &lt;/span&gt;at com.google.protobuf.AbstractMessageLite.toByteString(AbstractMessageLite.java:&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;49&lt;/span&gt;)
&lt;span class="indent"&gt;  &lt;/span&gt;at org.apache.hadoop.ipc.ProtobufRpcEngine&lt;span class="variable" style="color: rgb(0, 128, 128);"&gt;$Invoker&lt;/span&gt;.constructRpcRequest(ProtobufRpcEngine.java:&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;149&lt;/span&gt;)
&lt;span class="indent"&gt;  &lt;/span&gt;at org.apache.hadoop.ipc.ProtobufRpcEngine&lt;span class="variable" style="color: rgb(0, 128, 128);"&gt;$Invoker&lt;/span&gt;.invoke(ProtobufRpcEngine.java:&lt;span class="number" style="color: rgb(0, 153, 153);"&gt;193&lt;/span&gt;)</code></pre><span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-size:16px;line-height:27.2000007629395px;background-color:rgb(254,254,254);">把你的 jdk7 换成 jdk6 试试。</span><span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-size:16px;line-height:27.2000007629395px;background-color:rgb(254,254,254);"></span><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"></span>
<h2 style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-weight:normal;line-height:22px;font-size:20px;text-indent:1em;background-color:rgb(254,254,254);">
<a name="t16" style="color:rgb(51,102,153);"></a>5、Refe r：</h2>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
（1） scribe、chukwa、kafka、flume日志系统对比  </p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<a href="http://www.ttlsa.com/log-system/scribe-chukwa-kafka-flume-log-system-contrast/" rel="nofollow" style="color:rgb(51,102,153);text-decoration:none;">http://www.ttlsa.com/log-system/scribe-chukwa-kafka-flume-log-system-contrast/</a></p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
（2） 关于Flume-ng那些事   <a href="http://www.ttlsa.com/?s=flume" rel="nofollow" style="color:rgb(51,102,153);text-decoration:none;">http://www.ttlsa.com/?s=flume</a></p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
          关于Flume-ng那些事（三）：常见架构测试   <a href="http://www.ttlsa.com/log-system/about-flume-ng-3/" rel="nofollow" style="color:rgb(51,102,153);text-decoration:none;">http://www.ttlsa.com/log-system/about-flume-ng-3/</a></p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
（3） Flume 1.4.0 User Guide</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<a href="http://archive.cloudera.com/cdh4/cdh/4/flume-ng-1.4.0-cdh4.7.0/FlumeUserGuide.html" rel="nofollow" style="color:rgb(51,102,153);text-decoration:none;">http://archive.cloudera.com/cdh4/cdh/4/flume-ng-1.4.0-cdh4.7.0/FlumeUserGuide.html</a></p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
（4） flume日志采集   <a href="http://blog.csdn.net/sunmeng_007/article/details/9762507" rel="nofollow" style="color:rgb(51,102,153);text-decoration:none;">http://blog.csdn.net/sunmeng_007/article/details/9762507</a></p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
（5） Flume-NG + HDFS + HIVE 日志收集分析</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<a href="http://eyelublog.wordpress.com/2013/01/13/flume-ng-hdfs-hive-%E6%97%A5%E5%BF%97%E6%94%B6%E9%9B%86%E5%88%86%E6%9E%90/" rel="nofollow" style="color:rgb(51,102,153);text-decoration:none;">http://eyelublog.wordpress.com/2013/01/13/flume-ng-hdfs-hive-%E6%97%A5%E5%BF%97%E6%94%B6%E9%9B%86%E5%88%86%E6%9E%90/</a></p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
（6） 【Twitter Storm系列】flume-ng+Kafka+Storm+HDFS 实时系统搭建</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<a href="http://blog.csdn.net/weijonathan/article/details/18301321" rel="nofollow" style="color:rgb(51,102,153);text-decoration:none;">http://blog.csdn.net/weijonathan/article/details/18301321</a></p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
（7） Flume-NG + HDFS + PIG 日志收集分析</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<a href="http://hi.baidu.com/life_to_you/item/a98e2ec3367486dbef183b5e" rel="nofollow" style="color:rgb(51,102,153);text-decoration:none;">http://hi.baidu.com/life_to_you/item/a98e2ec3367486dbef183b5e</a></p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
flume 示例一收集tomcat日志   <a href="http://my.oschina.net/88sys/blog/71529" rel="nofollow" style="color:rgb(51,102,153);text-decoration:none;">http://my.oschina.net/88sys/blog/71529</a></p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
flume-ng 多节点集群示例   <a href="http://my.oschina.net/u/1401580/blog/204052" rel="nofollow" style="color:rgb(51,102,153);text-decoration:none;">http://my.oschina.net/u/1401580/blog/204052</a></p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
</p>
<p style="color:rgb(51,51,51);font-size:16px;line-height:27.2000007629395px;text-indent:1em;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
试用flume-ng 1.1    <a href="http://heipark.iteye.com/blog/1617995" rel="nofollow" style="color:rgb(51,102,153);text-decoration:none;">http://heipark.iteye.com/blog/1617995</a></p>
<br>            </div>
                </div>