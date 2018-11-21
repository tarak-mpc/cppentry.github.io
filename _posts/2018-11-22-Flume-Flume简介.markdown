---
layout:     post
title:      Flume-Flume简介
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/zhangdong2012/article/details/53065254				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3 id="flume概述">Flume概述</h3>

<p>Flume是一个可靠的、高可用的海量日志收集、聚合、传输系统，支持在日志系统中定制各种数据发送方，用于数据采集，同时，Flume还可以对数据进行简单处理，并且写入各种数据接收方，例如Hadoop、HBase、JDBC等等。</p>



<h3 id="flume的组成">Flume的组成</h3>

<p>Flume中的数据流是由Event组成的，Event是 Flume中数据流的基本单位，它携带日志数据和头信息，事件是由数据发送方生成的，Source接收到事件后，会把他推送Chanel中，Channel可以理解为一个缓冲区，它会一直持有事件直到事件被Sink处理完，Sink负责持久化日志数据或将事件推送到另一个Source中。 <br>
<img src="https://img-blog.csdn.net/20161107104920107" alt="这里写图片描述" title=""></p>

<h3 id="flume的可靠性">Flume的可靠性</h3>

<p>当Flume节点出现故障时，会将数据发送到其他的节点上而不会造成数据丢失。Flume提供了三种级别的可靠性保障，从强到弱依次为：</p>

<ol>
<li>end-to-end：接收到数据的agent首先会将Event写入磁盘，只有当 <br>
数据传送成功之后才删除。 <br>
2.store on failure：当数据接收方crash时，会将事件写入本地，待恢 <br>
复后重新发送。  <br>
3.best effort：数据发送的接收方后，不会进行确认。</li>
</ol>



<h3 id="flume的可恢复性">Flume的可恢复性</h3>

<p>Flume的可靠性是由Channel来实现的，可以使用FileChannel，将事件持久化到本地文件系统。</p>



<h3 id="flume中的核心概念">Flume中的核心概念</h3>

<p>Agent：是由JVM运行的Flume节点，每台机器运行一个Agent，在一个Agent中可以包含多个Source、Channel和Sink <br>
Client：一个用于收集数据，生成Event的线程 <br>
Source：从Client收集数据，发送给Channel <br>
Channel：连接source和sink <br>
Sink：从Channel中收集数据，进行持久化或传递给下一个接收点 <br>
Event：Flume数据流的基本组成单位</p>



<h3 id="flume架构">Flume架构</h3>

<p>Flume以Agent作为最小的独立运行单位，一个Agent就是一个JVM，一个Agent’是由Sourcee、Channel、Sink三大组件构成。</p>

<p><img src="https://img-blog.csdn.net/20161107124052373" alt="这里写图片描述" title=""></p>

<p>值得注意的是Flume内部提供了大量内置的Source、Channel、Sink类型，不同类型的Source、Channl、Sink可以自由组合，组合方式基于用户的配置文件。比如Channel可以把事件暂存到内存中，也可以持久化到本地磁盘，Sink可以把事件写入HDFS、HBase，或者另一个Source。</p>

<p>Flume支持用户建立多级流，也就是说多个Agent可以协同工作。并且支持Fau-in，Fau-out，Contextual route和Backup route。 <br>
<img src="https://img-blog.csdn.net/20161107125253161" alt="这里写图片描述" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>