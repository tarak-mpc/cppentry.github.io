---
layout:     post
title:      Flume架构、Flume工作原理、Flume应用场景
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h2>一、Flume概述</h2>

<p>Flume是由 Cloudera 提供的一个分布式、高可靠、高可用的服务，用于分布式的海量日志的高效收集、聚合、移动系统。简单来说，Flume 就是一个针对日志数据进行采集和汇总的一个工具（把日志从A地方移动到B地方）</p>

<p>Flume 官网：<a href="http://flume.apache.org/" rel="nofollow">http://flume.apache.org/</a> </p>

<p>Flume 官方文档：<a href="http://flume.apache.org/FlumeUserGuide.html" rel="nofollow">http://flume.apache.org/FlumeUserGuide.html</a></p>

<h3>Flume 特点：</h3>

<p>(1)、可靠性：当节点出现故障时，日志能够被传送到其他节点上而不会丢失。Flume提供了三种级别的可靠性保障，从强到弱依次分别为：</p>

<p>①.end-to-end（收到数据agent首先将event写到磁盘上，当数据传送成功后，再删除；如果数据发送失败，可以重新发送）； </p>

<p>②.Store on failure（这也是scribe采用的策略，当数据接收方crash时，将数据写到本地，待恢复后，继续发送）； </p>

<p>③.Best effort（数据发送到接收方后，不会进行确认）。</p>

<p>(2)、可扩展性：Flume采用了三层架构，分别为agent，collector和storage，每一层均可以水平扩展所有agent和collector由master统一管理，这使得系统容易监控和维护，且master允许有多个（使用ZooKeeper进行管理和负载均衡），这就避免了单点故障问题。</p>

<p>(3)、可管理性 </p>

<p>①.所有agent和colletor由master统一管理，这使得系统便于维护。 </p>

<p>②.多master情况，Flume利用ZooKeeper和gossip，保证动态配置数据的一致性。 </p>

<p>③.用户可以在master上查看各个数据源或者数据流执行情况，且可以对各个数据源配置和动态加载。 </p>

<p>④.Flume提供了web 和shell script command两种形式对数据流进行管理。①</p>

<p>(4)、功能可扩展性 </p>

<p>①.用户可以根据需要添加自己的agent，collector或者storage。 </p>

<p>②.此外，Flume自带了很多组件，包括各种agent（file， syslog等），collector和storage（file，HDFS等）。</p>

<p>(5)、文档丰富，社区活跃 </p>

<p>Flume 是Apache下的一个顶级项目，已经成为 Hadoop 生态系统的标配，它的文档比较丰富，社区比较活跃，方便我们学习。</p>

<p> </p>

<h3>主要的核心概念：</h3>

<p>Event：flume最基本的数据单元，带有一个可选的消息头（headers）。如果是文本，event通常是一行记录，event也是事务的基本单位。</p>

<p>Flow：Event从源点到达目的点的迁移的抽象。</p>

<p>Client：操作位于源点处的Event，将其发送到Flume Agent。</p>

<p>Agent：一个独立的Flume进程，包含组件Source、Channel、Sink。</p>

<p>Source：用来消费传递到该组件的Event，完成对数据的收集，分成transtion和event打入到channel之中。不同的 source，可以接受不同的数据格式。</p>

<p>Channel:主要提供一个队列的功能，对source提供中的数据进行简单缓存，作用是保证source到sink的数据传输过程一定能成功。</p>

<p>Sink：取出Channel中的数据，进行相应的存储文件系统、数据库等。</p>

<p> </p>

<p> </p>

<h2>二、Flume架构</h2>

<p>     <img alt="" class="has" src="https://img-blog.csdnimg.cn/20181107215539166.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pqaF83NDYxNDAxMjk=,size_16,color_FFFFFF,t_70"></p>

<p> </p>

<h2>三、Flume工作原理</h2>

<p>         Flume的核心就是一个agent，这个agent对外有两个进行交互的地方，一个是接受数据的输入——source，一个是数据的输出sink，sink负责将数据发送到外部指定的目的地。source接收到数据之后，将数据发送给channel，chanel作为一个数据缓冲区会临时存放这些数据，随后sink会将channel中的数据发送到指定的地方—-例如HDFS等，注意：只有在sink将channel中的数据成功发送出去之后，channel才会将临时数据进行删除，这种机制保证了数据传输的可靠性与安全性。</p>

<p></p>

<p> </p>

<h2>四、Flume应用场景</h2>

<p> </p>            </div>
                </div>