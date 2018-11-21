---
layout:     post
title:      Kafka设计解析：Kafka Consumer解析
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="note" style="border:1px solid rgb(214,233,198);color:rgb(60,118,61);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;background-color:rgb(223,240,216);">
　　<a href="http://www.iteblog.com/archives/1305" rel="nofollow" title="" style="color:rgb(0,166,124);text-decoration:none;">《Kafka剖析：Kafka背景及架构介绍》</a><br>
　　<a href="http://www.iteblog.com/archives/1360" rel="nofollow" title="" style="color:rgb(0,166,124);text-decoration:none;">《Kafka设计解析：Kafka High Availability（上）》</a><br>
　　<a href="http://www.iteblog.com/archives/1383" rel="nofollow" title="" style="color:rgb(0,166,124);text-decoration:none;">《Kafka设计解析：Kafka High Availability （下）》</a><br>
　　<a href="http://www.iteblog.com/archives/1384" rel="nofollow" title="" style="color:rgb(0,166,124);text-decoration:none;">《Kafka设计解析：Replication工具》</a><br>
　　<a href="http://www.iteblog.com/archives/1501" rel="nofollow" title="" style="color:rgb(0,166,124);text-decoration:none;">《Kafka设计解析：Kafka Consumer解析》</a></div>
<div id="toc_container" class="no_bullets" style="border:1px solid rgb(170,170,170);width:558px;display:table;font-size:14.25px;font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:26px;background:rgb(249,249,249);">
<p class="toc_title" style="text-align:center;font-weight:700;">
文章目录 <span class="toc_toggle" style="font-weight:400;font-size:12.825px;">[<a href="http://www.iteblog.com/archives/1501#" rel="nofollow" title="" style="color:rgb(0,166,124);text-decoration:none;">hide</a>]</span></p>
<ul class="toc_list" style="list-style:none;background:0px 0px;"><li style="line-height:30px;font-size:13.8225px;list-style:none;background:0px 0px;">
<a href="http://www.iteblog.com/archives/1501#High_Level_Consumer" rel="nofollow" title="" style="color:rgb(0,166,124);text-decoration:none;"><span class="toc_number toc_depth_1">1</span> High Level Consumer</a>
<ul style="list-style:none;background:0px 0px;"><li style="font-size:13.4078px;list-style:none;background:0px 0px;">
<a href="http://www.iteblog.com/archives/1501#Consumer_Group" rel="nofollow" title="" style="color:rgb(0,166,124);text-decoration:none;"><span class="toc_number toc_depth_2">1.1</span> Consumer Group</a></li><li style="font-size:13.4078px;list-style:none;background:0px 0px;">
<a href="http://www.iteblog.com/archives/1501#High_Level_Consumer_Rebalance" rel="nofollow" title="" style="color:rgb(0,166,124);text-decoration:none;"><span class="toc_number toc_depth_2">1.2</span> High Level Consumer Rebalance</a></li></ul></li><li style="line-height:30px;font-size:13.8225px;list-style:none;background:0px 0px;">
<a href="http://www.iteblog.com/archives/1501#Low_Level_Consumer" rel="nofollow" title="" style="color:rgb(0,166,124);text-decoration:none;"><span class="toc_number toc_depth_1">2</span> Low Level Consumer</a></li><li style="line-height:30px;font-size:13.8225px;list-style:none;background:0px 0px;">
<a href="http://www.iteblog.com/archives/1501#Consumer" rel="nofollow" title="" style="color:rgb(0,166,124);text-decoration:none;"><span class="toc_number toc_depth_1">3</span> Consumer重新设计</a>
<ul style="list-style:none;background:0px 0px;"><li style="font-size:13.4078px;list-style:none;background:0px 0px;">
<a href="http://www.iteblog.com/archives/1501#i" rel="nofollow" title="" style="color:rgb(0,166,124);text-decoration:none;"><span class="toc_number toc_depth_2">3.1</span> 设计方向</a></li><li style="font-size:13.4078px;list-style:none;background:0px 0px;">
<a href="http://www.iteblog.com/archives/1501#CoordinatorRebalance" rel="nofollow" title="" style="color:rgb(0,166,124);text-decoration:none;"><span class="toc_number toc_depth_2">3.2</span> 如何通过中心Coordinator实现Rebalance</a></li></ul></li></ul></div>
<h2 style="line-height:18px;font-size:18px;border-left-width:4px;border-left-style:solid;border-left-color:rgb(0,166,124);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;background-color:rgb(121,211,211);">
<span id="High_Level_Consumer">High Level Consumer</span></h2>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　很多时候，客户程序只是希望从<span class="wp_keywordlink_affiliate"><a href="http://www.iteblog.com/archives/tag/kafka" rel="nofollow" title="" style="color:rgb(0,166,124);text-decoration:none;">Kafka</a></span>读取数据，不太关心消息offset的处理。同时也希望提供一些语义，例如同一条消息只被某一个Consumer消费（单播）或被所有Consumer消费（广播）。因此，<span class="wp_keywordlink_affiliate"><a href="http://www.iteblog.com/archives/tag/kafka" rel="nofollow" title="" style="color:rgb(0,166,124);text-decoration:none;">Kafka</a></span> High
 Level Consumer提供了一个从<span class="wp_keywordlink_affiliate"><a href="http://www.iteblog.com/archives/tag/kafka" rel="nofollow" title="" style="color:rgb(0,166,124);text-decoration:none;">Kafka</a></span>消费数据的高层抽象，从而屏蔽掉其中的细节并提供丰富的语义。</p>
<h3 style="line-height:35px;font-size:16px;font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;">
<span id="Consumer_Group">Consumer Group</span></h3>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　High Level Consumer将从某个Partition读取的最后一条消息的offset存于ZooKeeper中（Kafka从0.8.2版本开始同时支持将offset存于Zookeeper中与将offset存于专用的Kafka Topic中）。这个offset基于客户程序提供给Kafka的名字来保存，这个名字被称为Consumer Group。Consumer Group是整个Kafka集群全局的，而非某个Topic的。每一个High Level Consumer实例都属于一个Consumer Group，若不指定则属于默认的Group。ZooKeeper中Consumer相关节点如下图所示：</p>
<div class="wp-caption aligncenter" style="text-align:center;font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:22.2656px;color:#FF0000;">
<a href="http://source.iteblog.com/pic/kafka_info8.png-webp" rel="nofollow" title="" style="color:rgb(0,166,124);text-decoration:none;"><img alt="" class="b-loaded" src="http://source.iteblog.com/pic/kafka_info8.png-webp" style="vertical-align:middle;border:0px;"></a><br>
如果想及时了解Spark、Hadoop或者Hbase相关的文章，欢迎关注微信公共帐号：<strong>iteblog_hadoop</strong></div>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　很多传统的Message Queue都会在消息被消费完后将消息删除，一方面避免重复消费，另一方面可以保证Queue的长度比较短，提高效率。而如上文所述，Kafka并不删除已消费的消息，为了实现传统Message Queue消息只被消费一次的语义，Kafka保证每条消息在同一个Consumer Group里只会被某一个Consumer消费。与传统Message Queue不同的是，Kafka还允许不同Consumer Group同时消费同一条消息，这一特性可以为消息的多元化处理提供支持。</p>
<div class="wp-caption aligncenter" style="text-align:center;font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:22.2656px;color:#FF0000;">
<a href="http://source.iteblog.com/pic/kafka_info9.png-webp" rel="nofollow" title="" style="color:rgb(0,166,124);text-decoration:none;"><img alt="" class="b-loaded" src="http://source.iteblog.com/pic/kafka_info9.png-webp" style="vertical-align:middle;border:0px;"></a><br>
如果想及时了解Spark、Hadoop或者Hbase相关的文章，欢迎关注微信公共帐号：<strong>iteblog_hadoop</strong></div>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　实际上，Kafka的设计理念之一就是同时提供离线处理和实时处理。根据这一特性，可以使用Storm这种实时流处理系统对消息进行实时在线处理，同时使用 Hadoop这种批处理系统进行离线处理，还可以同时将数据实时备份到另一个数据中心，只需要保证这三个操作所使用的Consumer在不同的 Consumer Group即可。下图展示了Kafka在LinkedIn的一种简化部署模型。</p>
<div class="wp-caption aligncenter" style="text-align:center;font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:22.2656px;color:#FF0000;">
<a href="http://source.iteblog.com/pic/kafka_info10.png-webp" rel="nofollow" title="" style="color:rgb(0,166,124);text-decoration:none;"><img alt="" class="b-loaded" src="http://source.iteblog.com/pic/kafka_info10.png-webp" style="vertical-align:middle;border:0px;"></a><br>
如果想及时了解Spark、Hadoop或者Hbase相关的文章，欢迎关注微信公共帐号：<strong>iteblog_hadoop</strong></div>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　为了更清晰展示Kafka Consumer Group的特性，笔者进行了一项测试。创建一个Topic (名为topic1)，再创建一个属于group1的Consumer实例，并创建三个属于group2的Consumer实例，然后通过 Producer向topic1发送Key分别为1，2，3的消息。结果发现属于group1的Consumer收到了所有的这三条消息，同时 group2中的3个Consumer分别收到了Key为1，2，3的消息，如下图所示。(点击放大图像)</p>
<div class="wp-caption aligncenter" style="text-align:center;font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:22.2656px;color:#FF0000;">
<a href="http://source.iteblog.com/pic/kafka_info11.png-webp" rel="nofollow" title="" style="color:rgb(0,166,124);text-decoration:none;"><img alt="" class="b-loaded" src="http://source.iteblog.com/pic/kafka_info11.png-webp" style="vertical-align:middle;border:0px;"></a><br>
如果想及时了解Spark、Hadoop或者Hbase相关的文章，欢迎关注微信公共帐号：<strong>iteblog_hadoop</strong></div>
<div class="danger" style="border:1px solid rgb(235,204,209);color:rgb(169,68,66);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;background-color:rgb(242,222,222);">
　　注：上图中每个黑色区域代表一个Consumer实例，每个实例只创建一个MessageStream。实际上，本实验将Consumer应用程序打成jar包，并在4个不同的命令行终端中传入不同的参数运行。</div>
<h3 style="line-height:35px;font-size:16px;font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;">
<span id="High_Level_Consumer_Rebalance">High Level Consumer Rebalance</span></h3>
<div class="danger" style="border:1px solid rgb(235,204,209);color:rgb(169,68,66);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;background-color:rgb(242,222,222);">
　　注：本节所讲述Rebalance相关内容均基于Kafka High Level Consumer。</div>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　Kafka保证同一Consumer Group中只有一个Consumer会消费某条消息，实际上，Kafka保证的是稳定状态下每一个Consumer实例只会消费某一个或多个特定 Partition的数据，而某个Partition的数据只会被某一个特定的Consumer实例所消费。也就是说Kafka对消息的分配是以 Partition为单位分配的，而非以每一条消息作为分配单元。这样设计的劣势是无法保证同一个Consumer Group里的Consumer均匀消费数据，优势是每个Consumer不用都跟大量的Broker通信，减少通信开销，同时也降低了分配难度，实现也更简单。另外，因为同一个Partition里的数据是有序的，这种设计可以保证每个Partition里的数据可以被有序消费。</p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　如果某Consumer Group中Consumer（每个Consumer只创建1个MessageStream）数量少于Partition数量，则至少有一个 Consumer会消费多个Partition的数据，如果Consumer的数量与Partition数量相同，则正好一个Consumer消费一个 Partition的数据。而如果Consumer的数量多于Partition的数量时，会有部分Consumer无法消费该Topic下任何一条消息。</p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　如下例所示，如果topic1有0，1，2共三个Partition，当group1只有一个Consumer(名为consumer1)时，该 Consumer可消费这3个Partition的所有数据。</p>
<div class="wp-caption aligncenter" style="text-align:center;font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:22.2656px;color:#FF0000;">
<a href="http://source.iteblog.com/pic/kafka_info12.png-webp" rel="nofollow" title="" style="color:rgb(0,166,124);text-decoration:none;"><img alt="" class="b-loaded" src="http://source.iteblog.com/pic/kafka_info12.png-webp" style="vertical-align:middle;border:0px;"></a><br>
如果想及时了解Spark、Hadoop或者Hbase相关的文章，欢迎关注微信公共帐号：<strong>iteblog_hadoop</strong></div>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　增加一个Consumer（consumer2）后，其中一个Consumer（consumer1）可消费2个Partition的数据（Partition 0和Partition 1），另外一个Consumer(consumer2)可消费另外一个Partition（Partition 2）的数据。</p>
<div class="wp-caption aligncenter" style="text-align:center;font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:22.2656px;color:#FF0000;">
<a href="http://source.iteblog.com/pic/kafka_info13.png-webp" rel="nofollow" title="" style="color:rgb(0,166,124);text-decoration:none;"><img alt="" class="b-loaded" src="http://source.iteblog.com/pic/kafka_info13.png-webp" style="vertical-align:middle;border:0px;"></a><br>
如果想及时了解Spark、Hadoop或者Hbase相关的文章，欢迎关注微信公共帐号：<strong>iteblog_hadoop</strong></div>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　再增加一个Consumer（consumer3）后，每个Consumer可消费一个Partition的数据。consumer1消费partition0，consumer2消费partition1，consumer3消费partition2。</p>
<div class="wp-caption aligncenter" style="text-align:center;font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:22.2656px;color:#FF0000;">
<a href="http://source.iteblog.com/pic/kafka_info14.png-webp" rel="nofollow" title="" style="color:rgb(0,166,124);text-decoration:none;"><img alt="" class="b-loaded" src="http://source.iteblog.com/pic/kafka_info14.png-webp" style="vertical-align:middle;border:0px;"></a><br>
如果想及时了解Spark、Hadoop或者Hbase相关的文章，欢迎关注微信公共帐号：<strong>iteblog_hadoop</strong></div>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　再增加一个Consumer（consumer4）后，其中3个Consumer可分别消费一个Partition的数据，另外一个Consumer（consumer4）不能消费topic1的任何数据。</p>
<div class="wp-caption aligncenter" style="text-align:center;font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:22.2656px;color:#FF0000;">
<a href="http://source.iteblog.com/pic/kafka_info15.png-webp" rel="nofollow" title="" style="color:rgb(0,166,124);text-decoration:none;"><img alt="" class="b-loaded" src="http://source.iteblog.com/pic/kafka_info15.png-webp" style="vertical-align:middle;border:0px;"></a><br>
如果想及时了解Spark、Hadoop或者Hbase相关的文章，欢迎关注微信公共帐号：<strong>iteblog_hadoop</strong></div>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
此时关闭consumer1，其余3个Consumer可分别消费一个Partition的数据。</p>
<div class="wp-caption aligncenter" style="text-align:center;font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:22.2656px;color:#FF0000;">
<a href="http://source.iteblog.com/pic/kafka_info16.png-webp" rel="nofollow" title="" style="color:rgb(0,166,124);text-decoration:none;"><img alt="" class="b-loaded" src="http://source.iteblog.com/pic/kafka_info16.png-webp" style="vertical-align:middle;border:0px;"></a><br>
如果想及时了解Spark、Hadoop或者Hbase相关的文章，欢迎关注微信公共帐号：<strong>iteblog_hadoop</strong></div>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
接着关闭consumer2，consumer3可消费2个Partition，consumer4可消费1个Partition。</p>
<div class="wp-caption aligncenter" style="text-align:center;font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:22.2656px;color:#FF0000;">
<a href="http://source.iteblog.com/pic/kafka_info17.png-webp" rel="nofollow" title="" style="color:rgb(0,166,124);text-decoration:none;"><img alt="" class="b-loaded" src="http://source.iteblog.com/pic/kafka_info17.png-webp" style="vertical-align:middle;border:0px;"></a><br>
如果想及时了解Spark、Hadoop或者Hbase相关的文章，欢迎关注微信公共帐号：<strong>iteblog_hadoop</strong></div>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
再关闭consumer3，仅存的consumer4可同时消费topic1的3个Partition。</p>
<div class="wp-caption aligncenter" style="text-align:center;font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:22.2656px;color:#FF0000;">
<a href="http://source.iteblog.com/pic/kafka_info18.png-webp" rel="nofollow" title="" style="color:rgb(0,166,124);text-decoration:none;"><img alt="" class="b-loaded" src="http://source.iteblog.com/pic/kafka_info18.png-webp" style="vertical-align:middle;border:0px;"></a><br>
如果想及时了解Spark、Hadoop或者Hbase相关的文章，欢迎关注微信公共帐号：<strong>iteblog_hadoop</strong></div>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　Consumer Rebalance的算法如下：</p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　1、将目标Topic下的所有Partirtion排序，存于PT<br>
　　2、对某Consumer Group下所有Consumer排序，存于CG，第i个Consumer记为Ci<br>
　　3、N=size(PT)/size(CG)，向上取整<br>
　　4、解除Ci对原来分配的Partition的消费权（i从0开始）<br>
　　5、将第i∗N到（i+1）∗N−1个Partition分配给Ci</p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　目前，最新版（0.8.2.1）Kafka的Consumer Rebalance的控制策略是由每一个Consumer通过在Zookeeper上注册Watch完成的。每个Consumer被创建时会触发 Consumer Group的Rebalance，具体启动流程如下：<br>
　　1、High Level Consumer启动时将其ID注册到其Consumer Group下，在Zookeeper上的路径为/consumers/[consumer group]/ids/[consumer id]<br>
　　2、在/consumers/[consumer group]/ids上注册Watch<br>
　　3、在/brokers/ids上注册Watch<br>
　　4、如果Consumer通过Topic Filter创建消息流，则它会同时在/brokers/topics上也创建Watch<br>
　　5、强制自己在其Consumer Group内启动Rebalance流程</p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　在这种策略下，每一个Consumer或者Broker的增加或者减少都会触发 Consumer Rebalance。因为每个Consumer只负责调整自己所消费的Partition，为了保证整个Consumer Group的一致性，当一个Consumer触发了Rebalance时，该Consumer Group内的其它所有其它Consumer也应该同时触发Rebalance。</p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　<strong>该方式有如下缺陷：</strong></p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　1、<strong>Herd effect</strong>：任何Broker或者Consumer的增减都会触发所有的Consumer的Rebalance<br>
　　2、<strong>Split Brain</strong>：每个Consumer分别单独通过Zookeeper判断哪些Broker和Consumer 宕机了，那么不同Consumer在同一时刻从Zookeeper“看”到的View就可能不一样，这是由Zookeeper的特性决定的，这就会造成不正确的Reblance尝试。<br>
　　3、<strong>调整结果不可控</strong>：所有的Consumer都并不知道其它Consumer的Rebalance是否成功，这可能会导致Kafka工作在一个不正确的状态。</p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　根据Kafka社区wiki，Kafka作者正在考虑在还未发布的0.9.x版本中使用中心协调器(Coordinator)。大体思想是为所有Consumer Group的子集选举出一个Broker作为Coordinator，由它Watch Zookeeper，从而判断是否有Partition或者Consumer的增减，然后生成Rebalance命令，并检查是否这些Rebalance 在所有相关的Consumer中被执行成功，如果不成功则重试，若成功则认为此次Rebalance成功（这个过程跟Replication
 Controller非常类似）。具体方案将在后文中详细阐述。</p>
<h2 style="line-height:18px;font-size:18px;border-left-width:4px;border-left-style:solid;border-left-color:rgb(0,166,124);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;background-color:rgb(121,211,211);">
<span id="Low_Level_Consumer">Low Level Consumer</span></h2>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　使用Low Level Consumer (Simple Consumer)的主要原因是，用户希望比Consumer Group更好的控制数据的消费。比如：<br>
　　1、同一条消息读多次<br>
　　2、只读取某个Topic的部分Partition<br>
　　3、管理事务，从而确保每条消息被处理一次，且仅被处理一次</p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　与Consumer Group相比，Low Level Consumer要求用户做大量的额外工作。<br>
　　1、必须在应用程序中跟踪offset，从而确定下一条应该消费哪条消息<br>
　　2、应用程序需要通过程序获知每个Partition的Leader是谁<br>
　　3、必须处理Leader的变化</p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
使用Low Level Consumer的一般流程如下<br>
　　1、查找到一个“活着”的Broker，并且找出每个Partition的Leader<br>
　　2、找出每个Partition的Follower<br>
　　3、定义好请求，该请求应该能描述应用程序需要哪些数据<br>
　　4、Fetch数据<br>
　　5、识别Leader的变化，并对之作出必要的响应</p>
<h2 style="line-height:18px;font-size:18px;border-left-width:4px;border-left-style:solid;border-left-color:rgb(0,166,124);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;background-color:rgb(121,211,211);">
<span id="Consumer">Consumer重新设计</span></h2>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　根据社区社区wiki，Kafka在0.9.*版本中，重新设计Consumer可能是最重要的Feature之一。本节会根据社区wiki介绍Kafka 0.9.*中对Consumer可能的设计方向及思路。</p>
<h3 style="line-height:35px;font-size:16px;font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;">
<span id="i">设计方向</span></h3>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
<strong>简化消费者客户端</strong></p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　部分用户希望开发和使用non-java的客户端。现阶段使用non-java发SimpleConsumer比较方便，但想开发High Level Consumer并不容易。因为High Level Consumer需要实现一些复杂但必不可少的失败探测和Rebalance。如果能将消费者客户端更精简，使依赖最小化，将会极大的方便non- java用户实现自己的Consumer。</p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
<strong>中心Coordinator</strong></p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　如上文所述，当前版本的High Level Consumer存在Herd Effect和Split Brain的问题。如果将失败探测和Rebalance的逻辑放到一个高可用的中心Coordinator，那么这两个问题即可解决。同时还可大大减少 Zookeeper的负载，有利于Kafka Broker的Scale Out。</p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
<strong>允许手工管理offset</strong></p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　一些系统希望以特定的时间间隔在自定义的数据库中管理Offset。这就要求Consumer能获取到每条消息的metadata，例如 Topic，Partition，Offset，同时还需要在Consumer启动时得到每个Partition的Offset。实现这些，需要提供新的 Consumer API。同时有个问题不得不考虑，即是否允许Consumer手工管理部分Topic的Offset，而让Kafka自动通过Zookeeper管理其它 Topic的Offset。一个可能的选项是让每个Consumer只能选取1种Offset管理机制，这可极大的简化Consumer
 API的设计和实现。</p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
<strong>Rebalance后触发用户指定的回调</strong></p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　一些应用可能会在内存中为每个Partition维护一些状态，Rebalance时，它们可能需要将该状态持久化。因此该需求希望支持用户实现并指定一些可插拔的并在Rebalance时触发的回调。如果用户使用手动的Offset管理，那该需求可方便得由用户实现，而如果用户希望使用Kafka提供的自动Offset管理，则需要Kafka提供该回调机制。</p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
<strong>非阻塞式Consumer API</strong></p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　该需求源于那些实现高层流处理操作，如filter by， group by， join等，的系统。现阶段的阻塞式Consumer几乎不可能实现Join操作。</p>
<h3 style="line-height:35px;font-size:16px;font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;">
<span id="CoordinatorRebalance">如何通过中心Coordinator实现Rebalance</span></h3>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　成功Rebalance的结果是，被订阅的所有Topic的每一个Partition将会被Consumer Group内的一个（有且仅有一个）Consumer拥有。每一个Broker将被选举为某些Consumer Group的Coordinator。某个Cosnumer Group的Coordinator负责在该Consumer Group的成员变化或者所订阅的Topic的Partititon变化时协调Rebalance操作。</p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
<strong>Consumer</strong></p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　1) Consumer启动时，先向Broker列表中的任意一个Broker发送ConsumerMetadataRequest，并通过 ConsumerMetadataResponse获取它所在Group的Coordinator信息。ConsumerMetadataRequest 和ConsumerMetadataResponse的结构如下</p>
<div style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
<div id="highlighter_31988" class="syntaxhighlighter nogutter java" style="width:558.594px;font-size:13px !important;line-height:1.6 !important;background-color:rgb(247,247,247) !important;">
<table border="0" cellpadding="0" cellspacing="0" style="width:558px;border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;table-layout:auto !important;background:none !important;"><tbody style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;background:none !important;"><tr style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;background:none !important;"><td class="code" style="width:558px;border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;background:none !important;">
<div style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;background:none !important;">
<div class="line number1 index0 alt2" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">ConsumerMetadataRequest</code></div>
<div class="line number2 index1 alt1" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">{</code></div>
<div class="line number3 index2 alt2" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java spaces" style="color:rgb(51,51,51);font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">  </code><code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">GroupId               
 =&gt; String</code></div>
<div class="line number4 index3 alt1" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">}</code></div>
<div class="line number5 index4 alt2" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
 </div>
<div class="line number6 index5 alt1" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">ConsumerMetadataResponse</code></div>
<div class="line number7 index6 alt2" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">{</code></div>
<div class="line number8 index7 alt1" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java spaces" style="color:rgb(51,51,51);font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">  </code><code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">ErrorCode             
 =&gt; int16</code></div>
<div class="line number9 index8 alt2" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java spaces" style="color:rgb(51,51,51);font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">  </code><code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">Coordinator           
 =&gt; Broker</code></div>
<div class="line number10 index9 alt1" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">}</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　2）Consumer连接到Coordinator并发送 HeartbeatRequest，如果返回的HeartbeatResponse没有任何错误码，Consumer继续fetch数据。若其中包含 IllegalGeneration错误码，即说明Coordinator已经发起了Rebalance操作，此时Consumer停止fetch数据，commit offset，并发送JoinGroupRequest给它的Coordinator，并在JoinGroupResponse中获得它应该拥有的所有 Partition列表和它所属的Group的新的Generation
 ID。此时Rebalance完成，Consumer开始fetch数据。相应Request和Response结构如下</p>
<div style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
<div id="highlighter_895888" class="syntaxhighlighter nogutter java" style="width:558.594px;font-size:13px !important;line-height:1.6 !important;background-color:rgb(247,247,247) !important;">
<table border="0" cellpadding="0" cellspacing="0" style="width:558px;border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;table-layout:auto !important;background:none !important;"><tbody style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;background:none !important;"><tr style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;background:none !important;"><td class="code" style="width:558px;border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;background:none !important;">
<div style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;background:none !important;">
<div class="line number1 index0 alt2" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">HeartbeatRequest</code></div>
<div class="line number2 index1 alt1" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">{</code></div>
<div class="line number3 index2 alt2" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java spaces" style="color:rgb(51,51,51);font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">  </code><code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">GroupId               
 =&gt; String</code></div>
<div class="line number4 index3 alt1" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java spaces" style="color:rgb(51,51,51);font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">  </code><code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">GroupGenerationId     
 =&gt; int32</code></div>
<div class="line number5 index4 alt2" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java spaces" style="color:rgb(51,51,51);font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">  </code><code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">ConsumerId            
 =&gt; String</code></div>
<div class="line number6 index5 alt1" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">}</code></div>
<div class="line number7 index6 alt2" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
 </div>
<div class="line number8 index7 alt1" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">HeartbeatResponse</code></div>
<div class="line number9 index8 alt2" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">{</code></div>
<div class="line number10 index9 alt1" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java spaces" style="color:rgb(51,51,51);font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">  </code><code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">ErrorCode             
 =&gt; int16</code></div>
<div class="line number11 index10 alt2" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">}</code></div>
<div class="line number12 index11 alt1" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
 </div>
<div class="line number13 index12 alt2" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">JoinGroupRequest</code></div>
<div class="line number14 index13 alt1" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">{</code></div>
<div class="line number15 index14 alt2" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java spaces" style="color:rgb(51,51,51);font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">  </code><code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">GroupId                    
 =&gt; String</code></div>
<div class="line number16 index15 alt1" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java spaces" style="color:rgb(51,51,51);font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">  </code><code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">SessionTimeout             
 =&gt; int32</code></div>
<div class="line number17 index16 alt2" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java spaces" style="color:rgb(51,51,51);font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">  </code><code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">Topics                     
 =&gt; [String]</code></div>
<div class="line number18 index17 alt1" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java spaces" style="color:rgb(51,51,51);font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">  </code><code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">ConsumerId                 
 =&gt; String</code></div>
<div class="line number19 index18 alt2" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java spaces" style="color:rgb(51,51,51);font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">  </code><code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">PartitionAssignmentStrategy
 =&gt; String</code></div>
<div class="line number20 index19 alt1" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">}</code></div>
<div class="line number21 index20 alt2" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
 </div>
<div class="line number22 index21 alt1" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">JoinGroupResponse</code></div>
<div class="line number23 index22 alt2" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">{</code></div>
<div class="line number24 index23 alt1" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java spaces" style="color:rgb(51,51,51);font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">  </code><code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">ErrorCode             
 =&gt; int16</code></div>
<div class="line number25 index24 alt2" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java spaces" style="color:rgb(51,51,51);font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">  </code><code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">GroupGenerationId     
 =&gt; int32</code></div>
<div class="line number26 index25 alt1" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java spaces" style="color:rgb(51,51,51);font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">  </code><code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">ConsumerId            
 =&gt; String</code></div>
<div class="line number27 index26 alt2" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java spaces" style="color:rgb(51,51,51);font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">  </code><code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">PartitionsToOwn       
 =&gt; [TopicName [Partition]]</code></div>
<div class="line number28 index27 alt1" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">}</code></div>
<div class="line number29 index28 alt2" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">TopicName
 =&gt; String</code></div>
<div class="line number30 index29 alt1" style="border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;">
<code class="java plain" style="color:rgb(77,77,76) !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;border:0px !important;line-height:1.6 !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;display:inline !important;background:none !important;">Partition
 =&gt; int32</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
Consumer状态机</p>
<div class="wp-caption aligncenter" style="text-align:center;font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:22.2656px;color:#FF0000;">
<a href="http://source.iteblog.com/pic/kafka_info19.png-webp" rel="nofollow" title="" style="color:rgb(0,166,124);text-decoration:none;"><img alt="" class="b-loaded" src="http://source.iteblog.com/pic/kafka_info19.png-webp" style="vertical-align:middle;border:0px;"></a><br>
如果想及时了解Spark、Hadoop或者Hbase相关的文章，欢迎关注微信公共帐号：<strong>iteblog_hadoop</strong></div>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
Down：Consumer停止工作</p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　Start up &amp; discover coordinator：Consumer检测其所在Group的Coordinator。一旦它检测到Coordinator，即向其发送JoinGroupRequest。</p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　Part of a group：该状态下，Consumer已经是该Group的成员，并周期性发送HeartbeatRequest。如 HeartbeatResponse包含IllegalGeneration错误码，则转换到Stopped Consumption状态。若连接丢失，HeartbeatResponse包含NotCoordinatorForGroup错误码，则转换到 Rediscover coordinator状态。</p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　Rediscover coordinator：该状态下，Consumer不停止消费而是尝试通过发送ConsumerMetadataRequest来探测新的Coordinator，并且等待直到获得无错误码的响应。</p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　Stopped consumption：该状态下，Consumer停止消费并提交offset，直到它再次加入Group。</p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
故障检测机制</p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　Consumer成功加入Group后，Consumer和相应的Coordinator同时开始故障探测程序。Consumer向 Coordinator发起周期性的Heartbeat（HeartbeatRequest）并等待响应，该周期为 session.timeout.ms/heartbeat.frequency。若Consumer在session.timeout.ms内未收到 HeartbeatResponse，或者发现相应的Socket channel断开，它即认为Coordinator已宕机并启动Coordinator探测程序。若Coordinator在
 session.timeout.ms内没有收到一次HeartbeatRequest，则它将该Consumer标记为宕机状态并为其所在Group触发一次Rebalance操作。</p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　Coordinator Failover过程中，Consumer可能会在新的Coordinator完成Failover过程之前或之后发现新的Coordinator并向其发送HeatbeatRequest。对于后者，新的Cooodinator可能拒绝该请求，致使该Consumer重新探测Coordinator并发起新的连接请求。如果该Consumer向新的Coordinator发送连接请求太晚，新的Coordinator可能已经在此之前将其标记为宕机状态而将之视为新加入的Consumer并触发一次Rebalance操作。</p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
Coordinator</p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　1）稳定状态下，Coordinator通过上述故障探测机制跟踪其所管理的每个Group下的每个Consumer的健康状态。<br>
　　2）刚启动时或选举完成后，Coordinator从Zookeeper读取它所管理的Group列表及这些Group的成员列表。如果没有获取到Group成员信息，它不会做任何事情直到某个Group中有成员注册进来。<br>
　　3）在Coordinator完成加载其管理的Group列表及其相应的成员信息之前，它将为 HeartbeatRequest，OffsetCommitRequest和JoinGroupRequests返回 CoordinatorStartupNotComplete错误码。此时，Consumer会重新发送请求。<br>
　　4）Coordinator会跟踪被其所管理的任何Consumer Group注册的Topic的Partition的变化，并为该变化触发Rebalance操作。创建新的Topic也可能触发Rebalance，因为 Consumer可以在Topic被创建之前就已经订阅它了。</p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
Coordinator发起Rebalance操作流程如下所示:</p>
<div class="wp-caption aligncenter" style="text-align:center;font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:22.2656px;color:#FF0000;">
<a href="http://source.iteblog.com/pic/kafka_info20.png-webp" rel="nofollow" title="" style="color:rgb(0,166,124);text-decoration:none;"><img alt="" class="b-loaded" src="http://source.iteblog.com/pic/kafka_info20.png-webp" style="vertical-align:middle;border:0px;"></a><br>
如果想及时了解Spark、Hadoop或者Hbase相关的文章，欢迎关注微信公共帐号：<strong>iteblog_hadoop</strong></div>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
<strong>Coordinator状态机</strong></p>
<div class="wp-caption aligncenter" style="text-align:center;font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:22.2656px;color:#FF0000;">
<a href="http://source.iteblog.com/pic/kafka_info21.png-webp" rel="nofollow" title="" style="color:rgb(0,166,124);text-decoration:none;"><img alt="" class="b-loaded" src="http://source.iteblog.com/pic/kafka_info21.png-webp" style="vertical-align:middle;border:0px;"></a><br>
如果想及时了解Spark、Hadoop或者Hbase相关的文章，欢迎关注微信公共帐号：<strong>iteblog_hadoop</strong></div>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
Down：Coordinator不再担任之前负责的Consumer Group的Coordinator</p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
Catch up：该状态下，Coordinator竞选成功，但还未能做好服务相应请求的准备。</p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
Ready：该状态下，新竞选出来的Coordinator已经完成从Zookeeper中加载它所负责管理的所有Group的metadata，并可开始接收相应的请求。</p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
Prepare for rebalance：该状态下，Coordinator在所有HeartbeatResponse中返回IllegalGeneration错误码，并等待所有Consumer向其发送JoinGroupRequest后转到Rebalancing状态。</p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
Rebalancing：该状态下，Coordinator已经收到了JoinGroupRequest请求，并增加其Group Generation ID，分配Consumer ID，分配Partition。Rebalance成功后，它会等待接收包含新的Consumer Generation ID的HeartbeatRequest，并转至Ready状态。</p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
Coordinator Failover</p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
如前文所述，Rebalance操作需要经历如下几个阶段<br>
　　1）Topic/Partition的改变或者新Consumer的加入或者已有Consumer停止，触发Coordinator注册在Zookeeper上的watch，Coordinator收到通知准备发起Rebalance操作。<br>
　　2）Coordinator通过在HeartbeatResponse中返回IllegalGeneration错误码发起Rebalance操作。<br>
　　3）Consumer发送JoinGroupRequest<br>
　　4）Coordinator在Zookeeper中增加Group的Generation ID并将新的Partition分配情况写入Zookeeper<br>
　　5）Coordinator发送JoinGroupResponse</p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
在这个过程中的每个阶段，Coordinator都可能出现故障。下面给出Rebalance不同阶段中Coordinator的Failover处理方式。</p>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　1）如果Coordinator的故障发生在第一阶段，即它收到Notification并未来得及作出响应，则新的Coordinator将从 Zookeeper读取Group的metadata，包含这些Group订阅的Topic列表和之前的Partition分配。如果某个Group所订阅的Topic数或者某个Topic的Partition数与之前的Partition分配不一致，亦或者某个Group连接到新的 Coordinator的Consumer数与之前Partition分配中的不一致，新的Coordinator会发起Rebalance操作。<br>
　　2）如果失败发生在阶段2，它可能对部分而非全部Consumer发出带错误码的HeartbeatResponse。与第上面第一种情况一样，新的 Coordinator会检测到Rebalance的必要性并发起一次Rebalance操作。如果Rebalance是由Consumer的失败所触发并且Cosnumer在Coordinator的Failover完成前恢复，新的Coordinator不会为此发起新的Rebalance操作。<br>
　　3）如果Failure发生在阶段3，新的Coordinator可能只收到部分而非全部Consumer的JoinGroupRequest。 Failover完成后，它可能收到部分Consumer的HeartRequest及另外部分Consumer的JoinGroupRequest。与第1种情况类似，它将发起新一轮的Rebalance操作。<br>
　　4）如果Failure发生在阶段4，即它将新的Group Generation ID和Group成员信息写入Zookeeper后。新的Generation ID和Group成员信息以一个原子操作一次性写入Zookeeper。Failover完成后，Consumer会发送 HeartbeatRequest给新的Coordinator，并包含旧的Generation ID。此时新的Coordinator通过在HeartbeatResponse中返回IllegalGeneration错误码发起新的一轮 Rebalance。这也解释了为什么每次HeartbeatRequest中都需要包含Generation
 ID和Consumer ID。<br>
　　5）如果Failure发生在阶段5，旧的Coordinator可能只向Group中的部分Consumer发送了 JoinGroupResponse。收到JoinGroupResponse的Consumer在下次向已经失效的Coordinator发送 HeartbeatRequest或者提交Offset时会检测到它已经失败。此时，它将检测新的Coordinator并向其发送带有新的 Generation ID 的HeartbeatRequest。而未收到JoinGroupResponse的Consumer将检测新的Coordinator并向其发送
 JoinGroupRequest，这将促使新的Coordinator发起新一轮的Rebalance。</p>
<div class="note" style="border:1px solid rgb(214,233,198);color:rgb(60,118,61);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;background-color:rgb(223,240,216);">
　　本文转载自：http://www.infoq.com/cn/articles/kafka-analysis-part-4</div>
            </div>
                </div>