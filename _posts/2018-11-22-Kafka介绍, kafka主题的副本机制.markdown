---
layout:     post
title:      Kafka介绍, kafka主题的副本机制
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：欢迎给我留言，多提意见。互相交流、共同进步！					https://blog.csdn.net/qq_31598113/article/details/70893878				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><strong><span style="font-size:18px;">1、Kafka概览</span></strong></p>
<p><span style="font-size:14px;">Apache下的项目Kafka(卡夫卡)是一个分布式流处理平台，它的流行是因为卡夫卡系统的设计和操作简单，能充分利用磁盘的顺序读写特性。kafka每秒钟能有百万条消息的吞吐量，因此很适合实时的数据流处理。例如kafka在线日志收集系统可作为flume的实时消息sink端，再通过kafka的消费者将消息实时写入hbase数据库中。</span></p>
<p><span style="font-size:14px;">卡夫卡以topic分类对记录进行存储，每个记录包含key-value和timestamp。</span></p>
<p><span style="font-size:14px;"> </span></p>
<p><strong><span style="font-size:14px;">1.1卡夫卡系统的组件、角色</span></strong></p>
<p><span style="font-size:14px;">broker: 每个正在运行的kafka节点</span></p>
<p><span style="font-size:14px;">producer：消息生产者</span></p>
<p><span style="font-size:14px;">consumer：消息的消费者</span></p>
<p><span style="font-size:14px;">consumer group：消费者组，同一个消费者组只能有一个consumer能消费消息</span></p>
<p><span style="font-size:14px;">kafka server ：也叫作broker, 已部署kafka的服务器, 以broker.id来区分不同的服务器</span></p>
<p><span style="font-size:14px;">topic：主题, 主题中的每条消息包括key-value和timestamp。可以定义多个topic，每个topic又可以划分为多个分区</span></p>
<p><span style="font-size:14px;">partition：topic下的消息分区，通过key取哈希后把消息映射分发到一个指定的分区，每个分区都映射到broker上的一个目录。一般每个分区存储在一个broker上</span></p>
<p><span style="font-size:14px;">replica：副本， 每个分区按照生产者的消息到达顺序存放。分区的多个副本中有且只有一个leader角色的副本.</span></p>
<p><span style="font-size:14px;">leader replica：leader角色的分区副本，leader角色的分区处理消息的读写请求. Leader和follower位于不同的broker.</span></p>
<p><span style="font-size:14px;">follower replica：follower角色的分区副本，负责从Leader拉取数据到本地，实现分区副本的创建</span></p>
<p><span style="font-size:14px;">zookeeper：严格来说这不是kafka的组件。但是在Kafka集群中, 很有必要通过Zookeeper管理kafka集群的配置、选举leader，以及在Consumer Group发生变化时进行rebalance。下面说一下kafka的哪些组件需要注册到zookeeper——</span></p>
<p><span style="font-size:14px;">为什么要注册到zk集群？<br><span></span>1，Kafka集群通过Zookeeper来管理kafka的配置，选举leader；<br><span></span>2，在Consumer Group发生变化时进行rebalance<br><span></span>3，所有的topic与broker的对应关系都由zk维护</span></p>
<p><span style="font-size:14px;"><img src="https://img-blog.csdn.net/20171228170323608?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzE1OTgxMTM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""></span><br><strong><span style="font-size:18px;"><br></span></strong></p>
<p><strong><span style="font-size:18px;"><br></span></strong></p>
<p><strong><span style="font-size:18px;">2、kafka的哪些组件需要注册到zookeeper？</span></strong><br><span style="font-size:14px;">（注意：producer不注册到zk）</span><br></p>
<p><span style="font-size:14px;">（1）Broker注册到zk</span><br><span style="font-size:14px;">每个broker启动时，都会注册到zk中，把自身的broker.id通知给zk。待zk创建此节点后，kafka会把这个broker的主机名和端口号记录到此节点</span><br><br><span style="font-size:14px;">（2）Topic注册到zk</span><br><span style="font-size:14px;">当broker启动时，会到对应topic节点下注册自己的broker.id到对应分区的isr列表中；当broker退出时，zk会自动更新其对应的topic分区的ISR列表，并决定是否需要做消费者的rebalance</span><br><br><span style="font-size:14px;">（3）Consumer注册到zk</span><br><span style="font-size:14px;">一旦有新的消费者组注册到zk，zk会创建专用的节点来保存相关信息。如果zk发现消费者增加或减少，会自动触发消费者的负载均衡。</span><br><em><br><br><strong><span style="font-size:18px;">3、消息如何被消费的？</span></strong></em></p>
<p><span style="font-size:14px;"><em>Producer使用push模式将消息发布到broker，Consumer使用pull模式从broker订阅并消费消息；producer通过联系zk获取leader角色的消息分区码，把消息写到leader</em></span></p>
<p><em><span style="font-size:14px;">Producer使用push模式将消息发布到broker</span></em></p>
<p><span style="font-size:14px;">+————+<br>
| broker     |<br>
+————+<br>
   |  |<br>
    \/<br>
   PULL<br>
   |  |<br>
    \/<br><em>Consumer使用pull模式从broker订阅并消费消息</em></span></p>
<p><span style="font-size:14px;"> </span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><strong><span style="font-size:18px;">4、卡夫卡的副本机制简介</span></strong></p>
<p><span style="font-size:14px;"><strong>4.1 简介</strong></span></p>
<p><span style="font-size:14px;">    由于Producer和Consumer都只会与Leader角色的分区副本相连，所以kafka需要以集群的组织形式提供主题下的消息高可用。kafka支持主备复制，所以消息具备高可用和持久性。</span></p>
<p><span style="font-size:14px;">    一个分区可以有多个副本，这些副本保存在不同的broker上。每个分区的副本中都会有一个作为Leader。当一个broker失败时，Leader在这台broker上的分区都会变得不可用，kafka会自动移除Leader，再其他副本中选一个作为新的Leader。</span></p>
<p><span style="font-size:14px;">在通常情况下，增加分区可以提供kafka集群的吞吐量。然而，也应该意识到集群的总分区数或是单台服务器上的分区数过多，会增加不可用及延迟的风险。</span></p>
<p><span style="font-size:14px;"><img src="https://img-blog.csdn.net/20170428113508818" alt=""></span></p>
<p align="right"><span style="font-size:14px;">（更正：图中Broker1中的<span style="background-color:rgb(51,204,0);">topic1-part1</span>和Broker2中的<span style="background-color:rgb(51,204,0);">topic1-part1</span>都是从<span style="background-color:rgb(255,102,102);">topic1-part2</span>复制过来的,所以要改成<span style="background-color:rgb(51,204,0);">topic1-part2</span><span style="background-color:rgb(255,255,255);"> </span><span style="background-color:rgb(255,255,255);">）</span></span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;"><strong>4.2 卡夫卡创建副本的2种模式——同步复制和异步复制</strong></span></p>
<p><span style="font-size:14px;">    Kafka动态维护了一个同步状态的副本的集合（a set of In-Sync Replicas），简称ISR，在这个集合中的节点都是和leader保持高度一致的，任何一条消息只有被这个集合中的每个节点读取并追加到日志中，才会向外部通知说“这个消息已经被提交”。</span></p>
<p><span style="font-size:14px;">    只有当消息被所有的副本加入到日志中时，才算是“committed”，只有committed的消息才会发送给consumer，这样就不用担心一旦leader down掉了消息会丢失。</span></p>
<p><span style="font-size:14px;">    消息从leader复制到follower, 我们可以通过决定Producer是否等待消息被提交的通知(ack)来区分同步复制和异步复制。</span><span style="font-size:14px;">同步复制流程：</span></p>
<p><span style="font-size:14px;">              1.producer联系zk识别leader</span></p>
<p><span style="font-size:14px;">              2.向leader发送消息</span></p>
<p><span style="font-size:14px;">              3.leadr收到消息写入到本地log</span></p>
<p><span style="font-size:14px;">              4.follower从leader pull消息</span></p>
<p><span style="font-size:14px;">              5.follower向本地写入log</span></p>
<p><span style="font-size:14px;">              6.follower向leader发送ack消息</span></p>
<p><span style="font-size:14px;">              7.leader收到所有follower的ack消息</span></p>
<p><span style="font-size:14px;">              8.leader向producer回传ack</span></p>
<p><span style="font-size:14px;">       异步复制流程：</span></p>
<p><span style="font-size:14px;">              和同步复制的区别在于，leader写入本地log之后，</span></p>
<p><span style="font-size:14px;">              直接向client回传ack消息，不需要等待所有follower复制完成。</span></p>
<p><span style="font-size:14px;">卡夫卡支持副本模式，那么其中一个Broker里的挂掉，一个新的leader就能通过ISR机制推选出来，继续处理读写请求。</span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;"><strong>4.3 卡夫卡判断一个broker节点是否存活，依据2个条件：</strong></span></p>
<p><span style="font-size:14px;">    1.节点必须可以维护和ZooKeeper的连接，Zookeeper通过心跳机制检查每个节点的连接。</span></p>
<p><span style="font-size:14px;">    2. 如果节点是个follower,他必须能及时的同步leader的写操作，延时不能太久。</span><span style="font-size:14px;">Leader会追踪所有“同步中”的节点，一旦一个down掉了，或是卡住了，或是延时太久，leader就会把它移除</span></p>
<p><span style="font-size:14px;"> </span></p>
            </div>
                </div>