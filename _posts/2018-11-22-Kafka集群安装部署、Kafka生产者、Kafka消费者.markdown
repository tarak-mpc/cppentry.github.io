---
layout:     post
title:      Kafka集群安装部署、Kafka生产者、Kafka消费者
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>概念： <br>
Storm上游数据源之Kakfa <br>
1、  kafka是什么？ <br>
2、  JMS规范是什么？ <br>
3、  为什么需要消息队列？ <br>
4、  Kafka核心组件 <br>
5、  Kafka安装部署 <br>
6、  Kafka生产者Java API <br>
7、  Kafka消费者Java API <br>
1、Kafka是什么 <br>
在流式计算中，Kafka一般用来缓存数据，Storm通过消费Kafka的数据进行计算。</p>

<p>KAFKA + STORM +REDIS</p>

<p>   Apache Kafka是一个开源消息系统，由Scala写成。是由Apache软件基金会开发的一个开源消息系统项目。 <br>
   Kafka最初是由LinkedIn开发，并于2011年初开源。2012年10月从Apache Incubator毕业。该项目的目标是为处理实时数据提供一个统一、高通量、低等待的平台。 <br>
   Kafka是一个分布式消息队列：生产者、消费者的功能。它提供了类似于JMS的特性，但是在设计实现上完全不同，此外它并不是JMS规范的实现。 <br>
   Kafka对消息保存时根据Topic进行归类，发送消息者称为Producer,消息接受者称为Consumer,此外kafka集群有多个kafka实例组成，每个实例(server)成为broker。 <br>
   无论是kafka集群，还是producer和consumer都依赖于zookeeper集群保存一些meta信息，来保证系统可用性 <br>
2、JMS是什么 <br>
2.1、JMS的基础 <br>
    JMS是什么：JMS是Java提供的一套技术规范 <br>
JMS干什么用：用来异构系统 集成通信，缓解系统瓶颈，提高系统的伸缩性增强系统用户体验，使得系统模块化和组件化变得可行并更加灵活 <br>
通过什么方式：生产消费者模式（生产者、服务器、消费者）</p>

<p>jdk，kafka，activemq…… <br>
2.2、JMS消息传输模型 <br>
   点对点模式（一对一，消费者主动拉取数据，消息收到后消息清除） <br>
点对点模型通常是一个基于拉取或者轮询的消息传送模型，这种模型从队列中请求信息，而不是将消息推送到客户端。这个模型的特点是发送到队列的消息被一个且只有一个接收者接收处理，即使有多个消息监听者也是如此。 <br>
   发布/订阅模式（一对多，数据生产后，推送给所有订阅者） <br>
发布订阅模型则是一个基于推送的消息传送模型。发布订阅模型可以有多种不同的订阅者，临时订阅者只在主动监听主题时才接收消息，而持久订阅者则监听主题的所有消息，即时当前订阅者不可用，处于离线状态。</p>

<p>queue.put（object）  数据生产 <br>
queue.take(object)    数据消费 <br>
2.3、JMS核心组件 <br>
   Destination：消息发送的目的地，也就是前面说的Queue和Topic。 <br>
   Message ：从字面上就可以看出是被发送的消息。 <br>
   Producer： 消息的生产者，要发送一个消息，必须通过这个生产者来发送。 <br>
   MessageConsumer： 与生产者相对应，这是消息的消费者或接收者，通过它来接收一个消息。</p>

<p>通过与ConnectionFactory可以获得一个connection <br>
通过connection可以获得一个session会话。</p>

<p>2.4、常见的类JMS消息服务器 <br>
2.4.1、JMS消息服务器 ActiveMQ <br>
ActiveMQ 是Apache出品，最流行的，能力强劲的开源消息总线。ActiveMQ 是一个完全支持JMS1.1和J2EE 1.4规范的。 <br>
主要特点： <br>
   多种语言和协议编写客户端。语言: Java, C, C++, C#, Ruby, Perl, Python, PHP。应用协议: OpenWire,Stomp REST,WS Notification,XMPP,AMQP <br>
   完全支持JMS1.1和J2EE 1.4规范 (持久化,XA消息,事务) <br>
   对Spring的支持,ActiveMQ可以很容易内嵌到使用Spring的系统里面去,而且也支持Spring2.0的特性 <br>
   通过了常见J2EE服务器(如 Geronimo,JBoss 4, GlassFish,WebLogic)的测试,其中通过JCA 1.5 resource adaptors的配置,可以让ActiveMQ可以自动的部署到任何兼容J2EE 1.4 商业服务器上 <br>
   支持多种传送协议:in-VM,TCP,SSL,NIO,UDP,JGroups,JXTA <br>
   支持通过JDBC和journal提供高速的消息持久化 <br>
   从设计上保证了高性能的集群,客户端-服务器,点对点 <br>
   支持Ajax <br>
   支持与Axis的整合 <br>
   可以很容易得调用内嵌JMS provider,进行测试 <br>
2.4.2、分布式消息中间件 Metamorphosis <br>
Metamorphosis (MetaQ) 是一个高性能、高可用、可扩展的分布式消息中间件，类似于LinkedIn的Kafka，具有消息存储顺序写、吞吐量大和支持本地和XA事务等特性，适用于大吞吐量、顺序消息、广播和日志数据传输等场景，在淘宝和支付宝有着广泛的应用，现已开源。 <br>
主要特点： <br>
   生产者、服务器和消费者都可分布 <br>
   消息存储顺序写 <br>
   性能极高,吞吐量大 <br>
   支持消息顺序 <br>
   支持本地和XA事务 <br>
   客户端pull，随机读,利用sendfile系统调用，zero-copy ,批量拉数据 <br>
   支持消费端事务 <br>
   支持消息广播模式 <br>
   支持异步发送消息 <br>
   支持http协议 <br>
   支持消息重试和recover <br>
   数据迁移、扩容对用户透明 <br>
   消费状态保存在客户端 <br>
   支持同步和异步复制两种HA <br>
   支持group commit <br>
2.4.3、分布式消息中间件 RocketMQ <br>
RocketMQ 是一款分布式、队列模型的消息中间件，具有以下特点： <br>
   能够保证严格的消息顺序 <br>
   提供丰富的消息拉取模式 <br>
   高效的订阅者水平扩展能力 <br>
   实时的消息订阅机制 <br>
   亿级消息堆积能力 <br>
   Metaq3.0 版本改名，产品名称改为RocketMQ <br>
2.4.4、其他MQ <br>
   .NET消息中间件 DotNetMQ <br>
   基于HBase的消息队列 HQueue <br>
   Go 的 MQ 框架 KiteQ <br>
   AMQP消息服务器 RabbitMQ <br>
   MemcacheQ 是一个基于 MemcacheDB 的消息队列服务器。</p>

<p>3、为什么需要消息队列（重要） <br>
消息系统的核心作用就是三点：解耦，异步和并行 <br>
以用户注册的案列来说明消息系统的作用 <br>
3.1、用户注册的一般流程</p>

<p>问题：随着后端流程越来越多，每步流程都需要额外的耗费很多时间，从而会导致用户更长的等待延迟。 <br>
3.2、用户注册的并行执行</p>

<p>问题：系统并行的发起了4个请求，4个请求中，如果某一个环节执行1分钟，其他环节再快，用户也需要等待1分钟。如果其中一个环节异常之后，整个服务挂掉了。</p>

<p>3.3、用户注册的最终一致</p>

<p>1、  保证主流程的正常执行、执行成功之后，发送MQ消息出去。 <br>
2、  需要这个destination的其他系统通过消费数据再执行，最终一致。</p>

<p>4、Kafka核心组件 <br>
   Topic ：消息根据Topic进行归类 <br>
   Producer：发送消息者 <br>
   Consumer：消息接受者 <br>
   broker：每个kafka实例(server) <br>
   Zookeeper：依赖集群保存meta信息。</p>

<p>5、Kafka集群部署 <br>
5.1集群部署的基本流程 <br>
下载安装包、解压安装包、修改配置文件、分发安装包、启动集群 <br>
5.2集群部署的基础环境准备 <br>
安装前的准备工作（zk集群已经部署完毕） <br>
   关闭防火墙  <br>
chkconfig iptables off  &amp;&amp; setenforce 0 <br>
   创建用户 <br>
groupadd realtime &amp;&amp;　useradd realtime　&amp;&amp; usermod -a -G realtime realtime <br>
   创建工作目录并赋权 <br>
mkdir /export <br>
mkdir /export/servers <br>
chmod 755 -R /export <br>
   切换到realtime用户下 <br>
su realtime <br>
5.3 Kafka集群部署  <br>
5.3.1、下载安装包 <br>
<a href="http://kafka.apache.org/downloads.html" rel="nofollow">http://kafka.apache.org/downloads.html</a> <br>
在linux中使用wget命令下载安装包 <br>
    wget <a href="http://mirrors.hust.edu.cn/apache/kafka/0.8.2.2/kafka_2.11-0.8.2.2.tgz" rel="nofollow">http://mirrors.hust.edu.cn/apache/kafka/0.8.2.2/kafka_2.11-0.8.2.2.tgz</a> <br>
5.3.2、解压安装包 <br>
tar -zxvf /export/software/kafka_2.11-0.8.2.2.tgz -C /export/servers/ <br>
cd /export/servers/ <br>
ln -s kafka_2.11-0.8.2.2 kafka <br>
5.3.3、修改配置文件 <br>
cp   /export/servers/kafka/config/server.properties <br>
/export/servers/kafka/config/server.properties.bak <br>
vi  /export/servers/kafka/config/server.properties <br>
输入以下内容：</p>

<p>5.3.4、分发安装包 <br>
scp -r /export/servers/kafka_2.11-0.8.2.2 kafka02:/export/servers <br>
然后分别在各机器上创建软连 <br>
cd /export/servers/ <br>
ln -s kafka_2.11-0.8.2.2 kafka <br>
5.3.5、再次修改配置文件（重要） <br>
依次修改各服务器上配置文件的的broker.id，分别是0,1,2不得重复。 <br>
5.3.6、启动集群 <br>
依次在各节点上启动kafka <br>
/export/servers/kafka/bin/kafka-server-start.sh  /export/servers/kafka/config/server.properties</p>

<p>5.4、Kafka常用操作命令 <br>
   查看当前服务器中的所有topic <br>
bin/kafka-topics.sh –list –zookeeper  cosa:2181 <br>
   创建topic <br>
/export/servers/kafka/bin/kafka-topics.sh –create –zookeeper cosa:2181 –replication-factor 1 –partitions 3 –topic first <br>
   删除topic <br>
sh /export/servers/kafka/bin/kafka-topics.sh –delete –zookeeper cosa:2181 –topic test <br>
需要server.properties中设置delete.topic.enable=true否则只是标记删除或者直接重启。 <br>
   通过shell命令发送消息 <br>
kafka-console-producer.sh –broker-list cosa:9092 –topic itheima <br>
   通过shell消费消息 <br>
sh /export/servers/kafka/bin/kafka-console-consumer.sh –zookeeper cosa:2181 –from-beginning –topic test1 <br>
   查看消费位置 <br>
sh kafka-run-class.sh kafka.tools.ConsumerOffsetChecker –zookeeper cosa:2181 –group testGroup <br>
   查看某个Topic的详情 <br>
sh kafka-topics.sh –topic test –describe –zookeeper cosa:2181</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>