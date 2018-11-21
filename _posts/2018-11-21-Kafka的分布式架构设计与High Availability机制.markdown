---
layout:     post
title:      Kafka的分布式架构设计与High Availability机制
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
作者:Wang, Josh</p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span>一、Kafka的基本概述</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span>1、Kafka是什么？</span></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
Kafka官网上对Kafka的定义叫：Adistributed publish-subscribe messaging system。publish-subscribe是发布和订阅的意思，所以准确的说Kafka是一个消息订阅和发布的系统。最初，Kafka实际上是LinkedIn用于日志处理的分布式消息队列，LinkedIn的日志数据容量大，但对可靠性要求不高，其日志数据主要包括用户行为（登录、浏览、点击、分享、喜欢）以及系统运行日志（CPU、内存、磁盘、网络、系统及进程状态）。</p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
 </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span>2、Kafka能做什么？</span></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
现今，Kafka主要用于处理活跃的流式数据，如分析用户的行为，包括用户的pageview（页面浏览），以便能够设计出更好的广告位，对用户搜索关键词进行统计以便分析出当前的流行趋势，比如经济学上著名的长裙理论：如果长裙的销量高了，说明经济不景气了，因为姑娘们没钱买各种丝袜了。当然还有些业务数据，如果存数据库浪费，而直接用传统的存硬盘方式效率又低下，这个时候，也可以使用Kafka的分布式进行存储。</p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span>3、Kafka中的相关概念</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
·        <span>Broker</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
Kafka集群包含一个或多个服务器，这种服务器被称为broker。一台Kafka服务器就是一个broker，一个集群由多个broker组成，一个broker可以容纳多个topic。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
·        <span>Topic</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
每条发布到Kafka集群的消息都有一个类别，这个类别被称为Topic，Kafka中Topic可以理解为一个存储消息的队列。物理上不同Topic的消息分开存储，逻辑上一个Topic的消息虽然保存在一个或多个broker上但用户只需指定消息的Topic即可生产或消费数据而不必关心数据存于何处。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
·        <span>Partition</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
Partition是物理上的概念，Kafka物理上把Topic分成一个或多个Partition，每个Partition在物理上对应一个文件夹，该文件夹下存储这个Partition的所有消息和索引文件。如创建topic1和topic2两个topic，且分别有13个和19个Partition分区，则整个集群上相应会生成32个文件夹。为了实现扩展性，一个非常大的topic可以分布到多个broker上，但Kafka只保证按一个partition中的顺序将消息发给consumer，不保证一个topic的整体（多个partition间）的顺序。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
·        <span>Producer</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
负责发布消息到Kafkabroker。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
·        <span>Consumer</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
消息消费者，向Kafkabroker读取消息的客户端。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
·        <span>Consumer Group（CG）<br></span>这是Kafka用来实现一个topic消息的广播（发给所有的consumer）和单播（发给任意一个consumer）的手段。一个 topic可以属于多个CG。topic的消息会复制（不是真的复制，是概念上的）到所有的CG，但每个CG只会把消息发给该CG中的一个consumer。如果需要实现广播，只要每个consumer有一个独立的CG就可以了。要实现单播只要所有的consumer在同一个CG。用CG还可以将consumer进行自由的分组而不需要多次发送消息到不同的topic。每个consumer属于一个特定的Consumer
 Group, Kafka允许为每个consumer指定group name，若不指定group name则属于默认的group。</p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span>4、Kafka的特性：</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
1）数据在磁盘上存取代价为O(1)，而一般数据在磁盘上是使用BTree存储的，存取代价为O（lgn）。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
   2）高吞吐率：即使在普通的节点（非常普通的硬件）上每秒钟也能处理成百上千的message。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
   3）显式分布式：即所有的producer、broker和consumer都会有多个，均匀分布并支持通过Kafka服务器和消费机集群来分区消息。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
   4）支持数据并行加载到Hadoop中。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
   5) 支持Broker间的消息分区及分布式消费，同时保证每个partition内的消息顺序传输。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
   6）同时支持离线数据处理和实时数据处理：当前很多的消息队列服务提供可靠交付保证，并默认是即时消费（不适合离线），而Kafka通过构建分布式的集群，允许消息在系统中累积，使得Kafka同时支持离线和在线日志处理。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
7）Scale out：支持在线水平扩展。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
二、<span>Kafka的架构设计</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span>1、 最简单的Kafka部署图</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
如果将消息的发布（publish）称作producer，将消息的订阅（subscribe）表述为consumer，将中间的存储阵列称作broker，这样可以得到一个最简单的消息发布与订阅模型：</p>
<img src="https://img-blog.csdn.net/20150618164135019?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZWJheQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:0px;vertical-align:middle;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><br style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span> 2、Kafka的拓扑图</span>：</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
Kafka是显示的分布式消息发布和订阅系统，除了有多个producer, broker，consumer外，还有一个zookeeper集群用于管理producer，broker和consumer之间的协同调用。</p>
<img src="https://img-blog.csdn.net/20150618164203372?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZWJheQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:0px;vertical-align:middle;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><br style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
从图中可以看出，一个典型的Kafka集群中包含若干Producer（可以是web前端产生的PageView，或者是服务器日志，系统CPU、Memory等），若干broker（Kafka支持水平扩展，一般broker数量越多，集群吞吐率越高），若干Consumer Group，以及一个<a href="http://zookeeper.apache.org/" rel="nofollow" style="background:transparent;color:rgb(202,12,22);"><span style="color:rgb(0,0,0);">Zookeeper</span></a>集群。Kafka通过Zookeeper管理集群配置，选举leader，以及在Consumer
 Group发生变化时进行rebalance。Producer使用push模式将消息发布到broker，Consumer使用pull模式从broker订阅并消费消息。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
图上有个细节需要注意，Producer到Broker的过程是push，也就是有数据就推送到Broker，而Consumer到Broker的过程是pull，是通过Consumer主动去拉数据的，而不是Broker把数据主动发送到Consumer端的。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
 </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span>3、Zookeeper和Producer、Broker、Consumer的协同工作</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
为了便于理解，假定此时Kafka集群中有两台Producer，但只有一台Kafka的Broker、Zookeeper和Consumer。如下图所示的部署集群。</p>
<img src="https://img-blog.csdn.net/20150618164231221?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZWJheQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:0px;vertical-align:middle;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><br style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
 1、 Kafka Broker其实就是Kafka的server，Broker主要做存储用，每个Broker启动后会在Zookeeper上注册一个临时的broker registry，包含Broker的IP地址和端口号，所存储的topics和partitions信息。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
 2、Zookeeper，可以把Zookeeper想象成它维持了一张表，记录了各个节点的IP、端口等配置信息。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
 3、Producer1,Producer2,Consumer的共同之处就是都配置了zkClient，更明确的说，就是运行前必须配置Zookeeper的地址，道理也很简单，因为他们之间的连接都是需要Zookeeper来进行分发的。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
 4、Kafka Broker和Zookeeper可以放在一台机器上，也可以分开放，此外Zookeeper也可以配集群,这样就不会出现单点故障。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
5、 每个Consumer启动后会在Zookeeper上注册一个临时的consumer registry：包含Consumer所属的Consumer Group以及订阅的topics。每个Consumer Group关联一个临时的owner registry和一个持久的offset registry。对于被订阅的每个partition包含一个owner registry，内容为订阅这个partition的consumer id，同时包含一个offset registry，内容为上一次订阅的offset。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span>整个系统运行的顺序可简单归纳为：</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
1、启动Zookeeper的server。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
2、启动Kafka的server。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
3、Producer如果生产了数据， 会先通过Zookeeper找到Broker，然后将数据存放进Broker。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
4、Consumer如果要消费数据，会先通过Zookeeper找对应的Broker，然后消费。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span>Producer代码实例：</span></p>
<table border="1" cellspacing="0" cellpadding="0" style="border-collapse:collapse;border-spacing:0px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><tbody><tr><td valign="top">
<p align="left">
<span>producer</span> = <span><span style="font-weight:400;color:rgb(127,0,85);">new</span></span> Producer(<span>...</span>)<span>;</span></p>
<p align="left">
message  = <span><span style="font-weight:400;color:rgb(127,0,85);">new</span></span> Message(<span style="color:rgb(42,0,255);">"Hello Ebay"</span>.getBytes());</p>
<p align="left">
set      = <span><span style="font-weight:400;color:rgb(127,0,85);">new</span></span> MessageSet(message);</p>
<p align="left">
producer.send(<span style="color:rgb(42,0,255);">"topic1"</span>, set);<span></span></p>
</td>
</tr></tbody></table><p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
发布消息时，Producer先构造一条消息，将消息加入到消息集set中（Kafka支持批量发布，可以往消息集合中添加多条消息，然后一次性发布), send消息时，client需指定消息所属的topic。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
 <span>Consumer代码实例：</span></p>
<table border="1" cellspacing="0" cellpadding="0" style="border-collapse:collapse;border-spacing:0px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><tbody><tr><td valign="top">
<p align="left">
streams[<span>]</span> = Consumer.createMessageStreams(<span style="color:rgb(42,0,255);">"topic1"</span>, 1);</p>
<p align="left">
<span><span style="font-weight:400;color:rgb(127,0,85);">for</span></span> (<span>message</span> : streams) {</p>
<p align="left">
    <span>bytes</span> = <span>message</span>.payload();</p>
<p align="left">
    <span style="color:rgb(63,127,95);">// do something with the bytes</span></p>
<p align="left">
}</p>
</td>
</tr></tbody></table><p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
订阅消息时,Consumer需指定topic以及partition num（每个partition对应一个逻辑日志流，如topic代表某个产品线，partition代表产品线的日志按天切分的结果），client订阅后，就可迭代读取消息，如果没有消息，client会阻塞直到有新的消息发布。Consumer可以累积确认接收到的消息，当其确认了某个offset的消息，意味着之前的消息也都已成功接收到，此时Broker会更新Zookeeper上的offset registry。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
那么怎样记录每个Consumer处理的信息的状态呢？其实在Kafka中仅保存了每个Consumer已经处理数据的offset。这样有两个好处：一是保存的数据量少，二是当Consumer出错时，重新启动Consumer处理数据时，只需从最近的offset开始处理数据即可。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
 </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span>4、Kafka的存储策略</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
Kafka broker主要是用于做存储使用，每个broker上可存储很多的topic信息。</p>
<img src="https://img-blog.csdn.net/20150618164331955?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZWJheQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:0px;vertical-align:middle;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><br style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span>4、Kafka的存储策略</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
Kafka broker主要是用于做存储使用，每个broker上可存储很多的topic信息。</p>
<img src="https://img-blog.csdn.net/20150618164400067?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZWJheQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:0px;vertical-align:middle;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><br style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
 2、每个segment中存储多条消息，消息id由其逻辑位置决定，即从消息id可直接定位到消息的存储位置，避免id到位置的额外映射。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
 3、每个partition在内存中对应一个index，记录每个segment中的第一条消息偏移。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
 4、Producer发到某个topic的消息会被均匀的分布到多个part上（随机或根据用户指定的回调函数进行分布），broker收到发布消息往对应part的最后一个segment上添加该消息，当某个segment上的消息条数达到配置值或消息发布时间超过阈值时，segment上的消息会被flush到磁盘，只有flush到磁盘上的消息订阅者才能订阅到，segment达到一定的大小后将不会再往该segment写数据，broker会创建新的segment。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
 </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span>5、Kafka的deliveryguarantee</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
在Kafka上，有两个原因可能导致低效：太多的网络请求和过多的字节拷贝。为了提高网络利用率，Kafka把message分成一组一组的，每次请求会把一组message发给相应的consumer。 此外，为了减少字节拷贝，采用sendfile系统调用， sendfile比传统的利用socket发送文件进行拷贝高效得多。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
Kafka支持有这几种delivery guarantee：</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
·        Atmost once 消息可能会丢，但绝不会重复传输</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
·        Atleast one 消息绝不会丢，但可能会重复传输</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
·        Exactlyonce 每条消息肯定会被传输一次且仅传输一次，很多时候这是用户所想要的。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span>6、Kafka多数据中心的数据流拓扑结构</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
实际设计中，有时由于安全性问题并不想让一个单个的Kafka集群系统跨越多个数据中心（数据中心之间可以进行通信），而是想让Kafka支持多数据中心的数据流拓扑结构。这可通过在集群之间进行镜像或“同步”实现。这个功能非常简单，镜像集群只是作为源集群的数据使用者（Consumer）的角色运行。这意味着，一个单个的集群就能够将来自多个数据中心的数据集中到一个位置。下面所示是可用于支持批量装载（batch loads）的多数据中心拓扑结构的一个例子：</p>
<br style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<img src="https://img-blog.csdn.net/20150618164433072?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZWJheQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:0px;vertical-align:middle;"><br></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
请注意，在图中上方的两个集群之间不存在通信连接，两者可能大小不同，具有不同数量的节点，而下面部分中的这个单个的集群可以镜像任意数量的源集群。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
 </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
三、<span>Kafka的High Availability机制</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span>   1、概述：</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
Kafka在0.8版本以后，开始提供High Availability机制。在此之前一旦一个或多个Broker宕机，则宕机期间其上所有Partition的数据将不可被消费，Producer也不能再将数据存储于这些Partition中。若该Broker永远不能再恢复，或者产生磁盘故障，则其上数据将丢失；此时无论Producer使用同步模式还是异步模式发送数据，都会造成整个系统的可用性降低，因为如果Producer使用同步模式，则Producer会在尝试重新发送message.send.max.retries（默认值为3）次后抛出Exception，用户可以选择停止发送后续数据或者选择继续选择发送。选择继续发送会造成数据的阻塞，选择停止发送会造成本应发往该Broker的数据的丢失；而如果Producer使用异步模式，Producer会尝试重新发送message.send.max.retries（默认值为3）次后记录该异常并继续发送后续数据，这会造成数据丢失并且用户只能通过日志发现该问题，更为悲惨的是当前Kafka的Producer并未对异步模式提供callback接口。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
Kafka的HA机制主要是通过Data Replication和Leader Election来保证的,Data Replication指的是每一个Partition都可能会有1个或者多个Replica，其中有一个Replica会被推选为Leader节点，其余落选的为Follower节点，其中Leader将会跟踪与其保持同步的Replica列表，该列表称为ISR（即in-sync Replica）。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span>2、Data Replication</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span>2.1、Kafka分配Replica的算法：</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
为了更好的做负载均衡，Kafka尽量将所有的Partition均匀分配到整个集群上。一个典型的部署方式是一个Topic的Partition数量大于Broker的数量。同时为了提高Kafka的容错能力，也需要将同一个Partition的Replica尽量分散到不同的机器。实际上，如果所有的 Replica都在同一个Broker上，那一旦该Broker宕机，该Partition的所有Replica都无法工作，也就达不到HA的效果。同时，如果某个Broker宕机了，需要保证它上面的负载可以被均匀的分配到其它幸存的所有Broker上。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
Kafka分配Replica的算法如下（假设总共有n个broker）：</p>
<ol start="1" type="1" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><li style="list-style:decimal;">将所有Broker和待分配的Partition排序</li><li style="list-style:decimal;">将第i个Partition分配到第（i mod n）个Broker上</li><li style="list-style:decimal;">将第i个Partition的第j个Replica分配到第（(i + j) mode n）个Broker上</li></ol><p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span>2.2、Kafka传播（Propagate）消息与ACK的过程</span></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
Producer在发布消息到某个Partition时，先通过ZooKeeper找到该Partition的Leader，然后无论该Topic的ReplicationFactor为多少（也即该Partition有多少个Replica），Producer只将该消息发送到该Partition的Leader，Leader会将该消息写入其本地Log。每个Follower都从Leaderpull数据。这种方式上，Follower存储的数据顺序与Leader保持一致。Follower在收到该消息并写入其Log后，向Leader发送
 ACK。一旦Leader收到了ISR中的所有Replica的ACK，该消息就被认为已经commit了，Leader将增加HW（高水位：表示最近一次提交消息的偏移位置）并且向 Producer发送ACK。</p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
为了提高性能，每个Follower在接收到数据后就立马向Leader发送ACK，而非等到数据写入Log中。因此，对于已经commit的消息，Kafka只能保证它被存于多个Replica的内存中，而不能保证它们被持久化到磁盘中，也就不能完全保证异常发生后该条消息一定能被 Consumer消费。但考虑到这种场景非常少见，可以认为这种方式是在性能和数据持久化上做了一个比较好的平衡。Consumer读消息也是从Leader读取，只有被commit过的消息（offset低于HW的消息）才会暴露给Consumer。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<a name="OLE_LINK2" style="background:transparent;color:rgb(79,161,219);"></a><a name="OLE_LINK1" style="background:transparent;color:rgb(79,161,219);"><span>2.3</span></a><span>、Partition
 Follower和Leader间的ISR复制机制</span></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
和大部分分布式系统一样，Kafka处理失败需要明确定义一个Broker是否“活着”。对于Kafka而言，Kafka存活包含两个条件，一是它必须维护与ZooKeeper的session（这个通过ZooKeeper的Heartbeat机制来实现）；二是Follower必须能够及时将 Leader的消息复制过来，不能“落后太多”。Leader会跟踪与其保持同步的Replica列表，即上面说到的ISR，如果一个Follower宕机，或者落后太多，Leader将把它从ISR中移除。这里所描述的“落后太多”指Follower复制的消息落后于Leader后的条数超过预定值（该值可在$KAFKA_HOME/config/server.properties中通过<code style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;font-size:14.4px;color:rgb(199,37,78);background-color:rgb(249,242,244);">replica.lag.max.messages</code>配置，其默认值是4000）或者Follower超过一定时间（该值可在$KAFKA_HOME/config/server.properties中通过<code style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;font-size:14.4px;color:rgb(199,37,78);background-color:rgb(249,242,244);">replica.lag.time.max.ms</code>来配置，其默认值是10000）未向Leader发送fetch请求。</p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
Kafka的复制机制既不是完全的同步复制，也不是单纯的异步复制。事实上，完全同步复制要求所有能工作的Follower都复制完，这条消息才会被认为commit，这种复制方式极大的影响了吞吐率,而异步复制方式下，Follower异步的从 Leader复制数据，数据只要被Leader写入log就被认为已经commit，这种情况下如果Follower复制都落后于Leader，而如果Leader突然宕机，则会丢失数据。而Kafka的这种使用ISR的方式则很好的均衡了确保数据不丢失以及吞吐率。Follower可以批量的从
 Leader复制数据，这样极大的提高复制性能（批量写磁盘），极大减少了Follower与Leader的差距。</p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
需要说明的是，Kafka只解决fail/recover，不处理“Byzantine”（“拜占庭”）问题。一条消息只有被ISR里的<span>所有</span> Follower都从Leader复制过去才会被认为已提交（不需要写到Follower本身的log）。这样就避免了部分数据被写进了Leader，还没来得及被任何Follower复制就宕机了，而造成数据丢失（Consumer无法消费这些数据）。而对于Producer而言，它可以选择是否等待消息commit，这可以通过<code style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;font-size:14.4px;color:rgb(199,37,78);background-color:rgb(249,242,244);">request.required.acks</code>来设置,这种机制确保了只要ISR有一个或以上的Follower，一条被commit的消息就不会丢失。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span>2.4、Follower从Leader Fetch数据</span></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
Follower通过向Leader发送FetchRequest获取消息，FetchRequest结构如下</p>
<img src="https://img-blog.csdn.net/20150618164513423?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZWJheQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:0px;vertical-align:middle;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><br style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
</p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
从FetchRequest的结构可以看出，每个Fetch请求都要指定最大等待时间和最小获取字节数，以及由TopicAndPartition和PartitionFetchInfo构成的Map。实际上，Follower从Leader Fetch数据和Consumer从Broker Fetch数据，都是通过FetchRequest请求完成，所以在FetchRequest结构中，其中一个字段是clientID，并且其默认值是ConsumerConfig.DefaultClientId。Leader收到Fetch请求后，Kafka通过KafkaApis.handleFetchRequest响应该请求，响应过程如下：</p>
<ol start="1" type="1" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><li style="list-style:decimal;">replicaManager根据请求读出数据存入dataRead中。</li><li style="list-style:decimal;">如果该请求来自Follower则更新其相应的LEO（log end offset）以及相应Partition的High Watermark（offset）。</li><li style="list-style:decimal;">根据dataRead算出可读消息长度（单位为字节）并存入bytesReadable中。</li><li style="list-style:decimal;">满足下面4个条件中的1个，则立即将相应的数据返回
<ul type="circle" style="list-style:disc;"><li style="list-style:decimal;">Fetch请求不希望等待，即fetchRequest.macWait &lt;= 0</li><li style="list-style:decimal;">Fetch请求不要求一定能取到消息，即fetchRequest.numPartitions &lt;= 0，也即requestInfo为空</li><li style="list-style:decimal;">有足够的数据可供返回，即bytesReadable &gt;= fetchRequest.minBytes</li><li style="list-style:decimal;">读取数据时发生异常</li></ul></li><li style="list-style:decimal;">若不满足以上4个条件，FetchRequest将不会立即返回，并将该请求封装成DelayedFetch。检查该DeplayedFetch是否满足，若满足则返回请求，否则将该请求加入Watch列表。</li></ol><p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
Leader通过以FetchResponse的形式将消息返回给Follower，FetchResponse结构如下</p>
<img src="https://img-blog.csdn.net/20150618164544258?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZWJheQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:0px;vertical-align:middle;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><br style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<br></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<a name="OLE_LINK3" style="background:transparent;color:rgb(79,161,219);"><span>2.5</span></a><span>、Partition的重新分配</span></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
管理工具或者client发出重新分配Partition请求后，会将相应信息写到<code style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;font-size:14.4px;color:rgb(199,37,78);background-color:rgb(249,242,244);">/admin/reassign_partitions</code>上，而该操作会触发ReassignedPartitionsIsrChangeListener，从而通过执行回调函数KafkaController.onPartitionReassignment来完成Partition的重新分配，主要过程是将ZooKeeper中的AR（CurrentAssigned
 Replicas）更新为OAR（Originallist of replicas for partition） + RAR（Reassigned replicas）。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span>2.6、Replication工具</span></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<code style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;font-size:14.4px;color:rgb(199,37,78);background-color:rgb(249,242,244);">1、Topic Tool</code></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<code style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;font-size:14.4px;color:rgb(199,37,78);background-color:rgb(249,242,244);">在$KAFKA_HOME/bin/Kafka-topics.sh</code>，该工具可用于创建、删除、修改、查看某个Topic的相关配置信息，也可用于列出所有Topic。</p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
2、Replica Verification Tool</p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<code style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;font-size:14.4px;color:rgb(199,37,78);background-color:rgb(249,242,244);">在$KAFKA_HOME/bin/Kafka-replica-verification.sh</code>，该工具用来验证所指定的一个或多个Topic下每个Partition对应的所有Replica是否都同步。可通过<code style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;font-size:14.4px;color:rgb(199,37,78);background-color:rgb(249,242,244);">topic-white-list</code>这一参数指定所需要验证的所有Topic，支持正则表达式。</p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
3、Kafka Reassign Partitions Tool</p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
该工具的设计目标与后面即将提到的PreferredReplica Leader Election Tool有些类似，都旨在促进Kafka集群的负载均衡。不同的是，Preferred Replica Leader Election只能在Partition的AR范围内调整其Leader，使Leader分布均匀，而该工具还可以调整Partition的AR，Follower需要从Leader Fetch数据以保持与Leader同步，所以仅仅保持Leader分布的平衡对整个集群的负载均衡来说是不够的。另外，生产环境下，随着负载的增大，可能需要给Kafka集群扩容，向Kafka集群中增加Broker非常简单方便，但是对于已有的Topic，并不会自动将其Partition迁移到新加入的Broker上，此时可用该工具达到此目的。</p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
某些场景下，实际负载可能远小于最初预期负载，此时可用该工具将分布在整个集群上的Partition重装分配到某些机器上，然后可以停止不需要的Broker从而实现节约资源的目的。需要说明的是，该工具不仅可以调整Partition的AR位置，还可调整其AR数量，即改变该Topic的replication factor。该工具有三种使用模式：generate模式，给定需要重新分配的Topic，自动生成reassign plan（并不执行）；execute模式，根据指定的reassign plan重新分配Partition；verify模式，验证重新分配。</p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
Kafka Replication的数据流如下图所示： </p>
<img src="https://img-blog.csdn.net/20150618164612414?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZWJheQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:0px;vertical-align:middle;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><br style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<br></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span>3、Leader Election</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span>3.1、概述</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
引入Replication之后，同一个Partition可能会有多个Replica，这时实际上需要从这些Replica中推选出一个Leader，如果没有一个Leader，所有Replica都可同时读/写数据，那就需要保证多个Replica之间互相（N×N条通路）同步数据，数据的一致性和有序性非常难保证，大大增加了Replication实现的复杂性，同时也增加了出现异常的几率，而引入Leader后，只有Leader负责数据读写（即Leader负责与Producer和Consumer交互），而Follower负责向Leader顺序Fetch数据（N条通路），这样的设计使得系统更加简单且高效。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span>3.2、Kafka借助Zookeeper和ISR选择Leader</span></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
Kafka中，一个简单直观的选择Leader的方案是所有Follower都在ZooKeeper上设置一个Watch，一旦Leader宕机，其对应的ephemeral znode会自动删除，此时所有Follower都尝试创建该节点，而创建成功者（ZooKeeper保证只有一个能创建成功）即是新的Leader，其它Replica即为Follower，但由于Follower可能落后Leader许多或者crash了，所以必须确保选择“最新”的Follower作为新的Leader。一个基本的原则就是，如果Leader不在了，新的Leader必须拥有原来的Leader
 commit过的所有消息。这就需要作一个折衷，如果Leader在标明一条消息被commit前等待更多的Follower确认，那在它宕机之后就有更多的Follower可以作为新的Leader，但这也会造成吞吐率的下降。 </p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
一种非常常用的选举leader的方式是“Majority Vote”（“少数服从多数”），但Kafka并未采用这种方式。这种模式下，如果我们有2n+1个Replica（包含Leader和Follower），那在commit之前必须保证有n+1个Replica复制完消息，为了保证正确选出新的Leader，fail的Replica不能超过n个。因为在剩下的任意n+1个Replica里，至少有一个Replica包含有最新的所有消息。这种方式有个很大的优势，系统的latency只取决于最快的几个Broker，而非最慢那个。但Majority
 Vote也有一些劣势，为了保证Leader Election的正常进行，它所能容忍的fail的follower个数比较少。如果要容忍2个follower挂掉，必须要有5个以上的Replica，如果要容忍3个Follower挂掉，必须要有7个以上的Replica。换句话说，在生产环境下为了保证较高的容错程度，必须要有大量的Replica，而大量的Replica又会在大数据量下导致性能的急剧下降。这就是这种算法更多用在<a href="http://zookeeper.apache.org/" rel="nofollow" style="background:transparent;color:rgb(202,12,22);"><span style="color:rgb(0,0,0);">ZooKeeper</span></a>这种共享集群配置的系统中而很少在需要存储大量数据的系统中使用的原因。例如HDFS的HA
 Feature是基于<a href="http://blog.cloudera.com/blog/2012/10/quorum-based-journaling-in-cdh4-1" rel="nofollow" style="background:transparent;color:rgb(202,12,22);"><span style="color:rgb(0,0,0);">majority-vote-based
 journal</span></a>算法，但是它的数据存储并没有使用这种方式。</p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
Kafka0.8以后增加了一个Controller概念，它在所有broker中选出一个controller，所有Partition的Leader选举都由controller决定，controller会将Leader的改变直接通过RPC的方式（比ZooKeeper Queue的方式更高效）通知需为此作为响应的Broker。同时controller也负责增删Topic以及Replica的重新分配。前面提到Kafka在ZooKeeper中动态维护了一个ISR（in-sync replicas），这个ISR里的所有Replica都跟上了leader，只有ISR里的成员才有被选为Leader的可能。在这种模式下，对于n+1个Replica，一个Partition能在保证不丢失已经commit的消息的前提下容忍n个Replica的失败。在大多数使用场景中，这种模式是非常有利的。事实上，为了容忍n个Replica的失败，MajorityVote和ISR在commit前需要等待的Replica数量是一样的，但是ISR需要的总的Replica的个数几乎是Majority
 Vote的一半。虽然Majority Vote与ISR相比有不需等待最慢的Broker这一优势，但是Kafka官方认为Kafka可以通过Producer选择是否被commit阻塞来改善这一问题，并且节省下来的Replica和磁盘使得ISR模式仍然值得。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span>3.3、Preferred Replica LeaderElection Tool</span></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
有了Replication机制后，每个Partition可能有多个备份。某个Partition的Replica列表叫作 AR（AssignedReplicas），AR中的第一个Replica即为“Preferred Replica“。创建一个新的Topic或者给已有Topic增加Partition时，Kafka保证Preferred Replica被均匀分布到集群中的所有Broker上。理想情况下，Preferred Replica会被选为Leader。以上两点保证了所有Partition的Leader被均匀分布到了集群当中，这一点非常重要，因为所有的读写操作都由Leader完成，若Leader分布过于集中，会造成集群负载不均衡。但是，随着集群的运行，该平衡可能会因为Broker的宕机而被打破，该工具就是用来帮助恢复Leader分配的平衡。</p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
事实上，每个Topic从失败中恢复过来后，它默认会被设置为Follower角色，除非某个Partition的Replica全部宕机，而当前 Broker是该Partition的AR中第一个恢复回来的Replica。因此，某个Partition的Leader（Preferred Replica）宕机并恢复后，它很可能不再是该Partition的Leader，但仍然是Preferred Replica。</p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
除了手动运行该工具使Leader分配均匀外，Kafka允许通过设置auto.leader.rebalance.enable=true来开启提供自动平衡Leader分配的功能，从而周期性检查Leader分配是否平衡，若不平衡度超过一定阈值则自动由Controller尝试将各Partition的Leader设置为其Preferred Replica,其中检查周期由leader.imbalance.check.interval.seconds指定，不平衡度阈值由leader.imbalance.per.broker.percentage指定。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span>总结：</span>从以上可以的分析可以看出：Kafka的主要设计理念就是同时提供离线处理和实时处理。根据这一特性，可以使用Storm这种实时流处理系统对消息进行实时在线处理，同时使用Hadoop这种批处理系统进行离线处理；而Kafka的HA机制在数据完整性和吞吐率之间做了一个很好的权衡，基于ISR的sync机制，提供了高吞吐率，高数据完整性的分布式消息发布和订阅系统，同时借助KafKa，用户还可以同时将数据实时备份到另一个数据中心，只需要保证这三个操作所使用的Consumer属于不同的Consumer
 Group即可，因为分属不同Consumer Group的Consumer都将收到Producer发布到Kafka broker的信息。</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
 </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
参考资料：</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<a href="http://kafka.apache.org/documentation.html" rel="nofollow" style="background:transparent;color:rgb(202,12,22);">http://kafka.apache.org/documentation.html</a></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<a href="http://www.infoq.com/cn/articles/kafka-analysis-part-2" rel="nofollow" style="background:transparent;color:rgb(202,12,22);">http://www.infoq.com/cn/articles/kafka-analysis-part-2</a></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<a href="http://www.open-open.com/lib/view/open1354277579741.html" rel="nofollow" style="background:transparent;color:rgb(202,12,22);">http://www.open-open.com/lib/view/open1354277579741.html</a></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<br></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<br></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<br></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<br></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
http://blog.csdn.net/ebay/article/details/46549661<br></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
 </p>
            </div>
                </div>