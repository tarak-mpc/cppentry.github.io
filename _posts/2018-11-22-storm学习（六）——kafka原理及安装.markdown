---
layout:     post
title:      storm学习（六）——kafka原理及安装
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p style="margin-left:0cm;">首先回顾一下storm的内容：</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="647" src="https://img-blog.csdn.net/20180902232249579?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdmFuMTIzNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1200"></p>

<p style="margin-left:0cm;">Storm的数据源一方面来自kafka，kafka的数据送给storm中的spout，供storm使用，下面详细介绍kafka。</p>

<p style="margin-left:0cm;">Kafka介绍：https://blog.csdn.net/ychenfeng/article/details/74980531</p>

<h2 style="margin-left:0cm;"><strong>1、Kafka是什么</strong></h2>

<p style="margin-left:0cm;">在流式计算中，Kafka一般用来缓存数据，Storm通过消费Kafka的数据进行计算。</p>

<p style="margin-left:0cm;">KAFKA + STORM +REDIS</p>

<ol><li>Apache Kafka是一个开源<strong><span style="color:#ff0000;">消息</span></strong>系统，由Scala写成。是由Apache软件基金会开发的一个开源消息系统项目。</li>
	<li>Kafka最初是由LinkedIn开发，并于2011年初开源。2012年10月从Apache Incubator毕业。该项目的目标是为处理实时数据提供一个统一、高通量、低等待的平台。</li>
	<li><strong>Kafka</strong><strong>是一个分布式消息队列：生产者、消费者的功能。它提供了<span style="color:#ff0000;">类似于</span>JMS</strong><strong>的特性，<span style="color:#ff0000;">但是在设计实现上完全不同，此外它并不是</span><span style="color:#ff0000;">JMS</span></strong><strong><span style="color:#ff0000;">规范的实现</span></strong><strong>。</strong></li>
	<li>Kafka对消息保存时根据Topic进行归类，发送消息者称为Producer,消息接受者称为Consumer,此外kafka集群有多个kafka实例组成，每个实例(server)成为broker。</li>
	<li>无论是kafka集群，还是producer和consumer都依赖于<strong><span style="color:#ff0000;">zookeeper</span></strong>集群保存一些meta信息，来保证系统可用性</li>
</ol><h2 style="margin-left:0cm;"><strong>2、JMS是什么</strong></h2>

<h3 style="margin-left:0cm;"><strong>2.1、JMS的基础</strong></h3>

<p style="margin-left:0cm;">         JMS是什么：JMS是Java提供的一套技术规范</p>

<p style="margin-left:0cm;">JMS干什么用：用来异构系统 集成通信，缓解系统瓶颈，提高系统的伸缩性增强系统用户体验，使得系统模块化和组件化变得可行并更加灵活</p>

<p style="margin-left:0cm;">通过什么方式：生产消费者模式（生产者、服务器、消费者）</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="240" src="https://img-blog.csdn.net/20180902232249432?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdmFuMTIzNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="676"></p>

<p style="margin-left:0cm;">jdk，kafka，activemq……</p>

<h3 style="margin-left:0cm;"><strong>2.2、JMS消息传输模型</strong></h3>

<p>1、点对点模式<strong>（一对一，消费者主动<span style="color:#ff0000;">拉取</span>数据，消息收到后消息清除）</strong></p>

<p style="margin-left:0cm;">点对点模型通常是一个基于拉取或者轮询的消息传送模型，这种模型从队列中请求信息，而不是将消息推送到客户端。这个模型的特点是发送到队列的消息被<strong><span style="color:#ff0000;">一个且只有一个接收者接收处理</span></strong>，即使有多个消息监听者也是如此。</p>

<p>2、发布/订阅模式<strong>（一对多，数据生产后，<span style="color:#ff0000;">推送</span>给所有订阅者）</strong></p>

<p>发布订阅模型则是一个基于推送的消息传送模型。发布订阅模型可以有多种不同的订阅者，临时订阅者只在主动监听主题时才接收消息，而持久订阅者则监听主题的所有消息，<strong><span style="color:#ff0000;">即时当前订阅者不可用，处于离线状态</span></strong>。</p>

<p style="margin-left:42.05pt;"><img alt="" class="has" height="264" src="https://img-blog.csdn.net/20180902232249444?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdmFuMTIzNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="558"></p>

<p style="margin-left:42.05pt;">queue.put（object）  数据生产</p>

<p style="margin-left:42.05pt;">queue.take(object)    数据消费</p>

<h3 style="margin-left:0cm;"><strong>2.3、JMS核心组件</strong></h3>

<p>Destination：消息发送的目的地，也就是前面说的Queue和Topic。</p>

<p>Message ：从字面上就可以看出是被发送的消息。</p>

<p style="margin-left:0cm;">StreamMessage：Java 数据流消息，用标准流操作来顺序的填充和读取。</p>

<p style="margin-left:0cm;">MapMessage：一个Map类型的消息；名称为 string 类型，而值为 Java 的基本类型。</p>

<p style="margin-left:0cm;">TextMessage：普通字符串消息，包含一个String。</p>

<p style="margin-left:0cm;">ObjectMessage：对象消息，包含一个可序列化的Java 对象</p>

<p style="margin-left:0cm;">BytesMessage：二进制数组消息，包含一个byte[]。</p>

<p style="margin-left:0cm;">XMLMessage:  一个XML类型的消息。</p>

<p><u><strong>最常用的是TextMessage和ObjectMessage。</strong></u></p>

<p>Producer： 消息的生产者，要发送一个消息，必须通过这个生产者来发送。</p>

<p>MessageConsumer： 与生产者相对应，这是消息的消费者或接收者，通过它来接收一个消息。</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="394" src="https://img-blog.csdn.net/20180902232249437?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdmFuMTIzNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="676"></p>

<p style="margin-left:0cm;">通过与ConnectionFactory可以获得一个connection</p>

<p style="margin-left:0cm;">通过connection可以获得一个session会话。</p>

<p style="margin-left:0cm;">Destimation即为消息队列queue。</p>

<h3 style="margin-left:0cm;"><strong>2.4、常见的类JMS消息服务器</strong></h3>

<p><strong>2.4.1、JMS消息服务器 ActiveMQ</strong></p>

<p style="margin-left:0cm;">ActiveMQ 是Apache出品，最流行的，能力强劲的开源消息总线。ActiveMQ 是一个完全支持JMS1.1和J2EE 1.4规范的。</p>

<p style="margin-left:0cm;">主要特点：</p>

<ol><li>多种语言和协议编写客户端。语言: Java, C, C++, C#, Ruby, Perl, Python, PHP。应用协议: OpenWire,Stomp REST,WS Notification,XMPP,AMQP</li>
	<li>完全支持JMS1.1和J2EE 1.4规范 (持久化,XA消息,事务)</li>
	<li>对Spring的支持,ActiveMQ可以很容易内嵌到使用Spring的系统里面去,而且也支持Spring2.0的特性</li>
	<li>通过了常见J2EE服务器(如 Geronimo,JBoss 4, GlassFish,WebLogic)的测试,其中通过JCA 1.5 resource adaptors的配置,可以让ActiveMQ可以自动的部署到任何兼容J2EE 1.4 商业服务器上</li>
	<li>支持多种传送协议:in-VM,TCP,SSL,NIO,UDP,JGroups,JXTA</li>
	<li>支持通过JDBC和journal提供高速的消息持久化</li>
	<li>从设计上保证了高性能的集群,客户端-服务器,点对点</li>
	<li>支持Ajax</li>
	<li>支持与Axis的整合</li>
	<li>可以很容易得调用内嵌JMS provider,进行测试</li>
</ol><p><strong>2.4.2、分布式消息中间件 Metamorphosis</strong></p>

<p style="margin-left:0cm;">Metamorphosis (MetaQ) 是一个高性能、高可用、可扩展的分布式消息中间件，类似于LinkedIn的Kafka，具有消息存储顺序写、吞吐量大和支持本地和XA事务等特性，适用于大吞吐量、顺序消息、广播和日志数据传输等场景，在淘宝和支付宝有着广泛的应用，现已开源。</p>

<p style="margin-left:0cm;">主要特点：</p>

<ol><li>生产者、服务器和消费者都可分布</li>
	<li>消息存储顺序写</li>
	<li>性能极高,吞吐量大</li>
	<li>支持消息顺序</li>
	<li>支持本地和XA事务</li>
	<li>客户端pull，随机读,利用sendfile系统调用，zero-copy ,批量拉数据</li>
	<li>支持消费端事务</li>
	<li>支持消息广播模式</li>
	<li>支持异步发送消息</li>
	<li>支持http协议</li>
	<li>支持消息重试和recover</li>
	<li>数据迁移、扩容对用户透明</li>
	<li>消费状态保存在客户端</li>
	<li>支持同步和异步复制两种HA</li>
	<li>支持group commit</li>
</ol><p><strong>2.4.3、分布式消息中间件 RocketMQ</strong></p>

<p style="margin-left:0cm;">RocketMQ 是一款分布式、队列模型的消息中间件，具有以下特点：</p>

<ol><li>能够保证严格的消息顺序</li>
	<li>提供丰富的消息拉取模式</li>
	<li>高效的订阅者水平扩展能力</li>
	<li>实时的消息订阅机制</li>
	<li>亿级消息堆积能力</li>
	<li><strong>Metaq3.0 </strong><strong>版本改名，产品名称改为RocketMQ</strong></li>
</ol><p><strong>2.4.4、其他MQ</strong></p>

<ol><li>.NET消息中间件 DotNetMQ</li>
	<li>基于HBase的消息队列 HQueue</li>
	<li>Go 的 MQ 框架 KiteQ</li>
	<li>AMQP消息服务器 RabbitMQ</li>
	<li>MemcacheQ 是一个基于 MemcacheDB 的消息队列服务器。</li>
</ol><h2 style="margin-left:0cm;"><strong>3、为什么需要消息队列（重要）</strong></h2>

<p style="margin-left:0cm;">消息系统的核心作用就是三点：解耦，异步和并行。</p>

<p style="margin-left:0cm;">以用户注册的案列来说明消息系统的作用</p>

<h3 style="margin-left:0cm;"><strong>3.1、用户注册的一般流程</strong></h3>

<p style="margin-left:0cm;"><img alt="" class="has" height="124" src="https://img-blog.csdn.net/20180902232249470?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdmFuMTIzNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="540"></p>

<p style="margin-left:0cm;"><strong><span style="color:#ff0000;">问题</span></strong>：随着后端流程越来越多，每步流程都需要额外的耗费很多时间，从而会导致用户更长的等待延迟。</p>

<h3 style="margin-left:0cm;"><strong>3.2、用户注册的并行执行</strong></h3>

<p style="margin-left:0cm;"><img alt="" class="has" height="408" src="https://img-blog.csdn.net/20180902232249530?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdmFuMTIzNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="540"></p>

<p style="margin-left:0cm;"><strong><span style="color:#ff0000;">问题</span></strong>：系统并行的发起了4个请求，4个请求中，如果某一个环节执行1分钟，其他环节再快，用户也需要等待1分钟。如果其中一个环节异常之后，整个服务挂掉了。</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="350" src="https://img-blog.csdn.net/20180902232249710?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdmFuMTIzNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="655"></p>

<h3 style="margin-left:0cm;"><strong>3.3、用户注册的最终一致</strong></h3>

<p style="margin-left:0cm;"><img alt="" class="has" height="214" src="https://img-blog.csdn.net/20180902232249771?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdmFuMTIzNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="540"></p>

<ol><li><strong><span style="color:#ff0000;">保证主流程的正常执行</span></strong>、执行成功之后，发送MQ消息出去。</li>
	<li>需要这个destination的其他系统通过消费数据再执行，最终一致。</li>
</ol><p style="margin-left:0cm;"><img alt="" class="has" height="244" src="https://img-blog.csdn.net/20180902232249777?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdmFuMTIzNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="336"></p>

<h2 style="margin-left:0cm;"><strong>4、Kafka核心组件</strong></h2>

<ol><li>Topic ：消息根据Topic进行归类</li>
	<li>Producer：发送消息者</li>
	<li>Consumer：消息接受者</li>
	<li>broker：每个kafka实例(server)</li>
	<li>Zookeeper：依赖集群保存meta信息。</li>
</ol><p style="margin-left:0cm;"><img alt="" class="has" height="180" src="https://img-blog.csdn.net/20180902232249821?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdmFuMTIzNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="258"></p>

<h2 style="margin-left:0cm;"><strong>5、Kafka集群部署</strong></h2>

<h3 style="margin-left:0cm;"><strong>5.1、集群部署的基本流程</strong></h3>

<p style="margin-left:0cm;">下载安装包、解压安装包、修改配置文件、分发安装包、启动集群</p>

<h3 style="margin-left:0cm;"><strong>5.2、集群部署的基础环境准备</strong></h3>

<p style="margin-left:0cm;">安装前的准备工作（zk集群已经部署完毕）</p>

<p>1、关闭防火墙</p>

<p style="margin-left:0cm;">chkconfig iptables off  &amp;&amp; setenforce 0</p>

<p>2、创建用户</p>

<p style="margin-left:0cm;">groupadd realtime &amp;&amp;　useradd realtime　&amp;&amp; usermod -a -G realtime realtime</p>

<p>3、创建工作目录并赋权</p>

<p style="margin-left:0cm;">mkdir /export</p>

<p style="margin-left:0cm;">mkdir /export/servers</p>

<p style="margin-left:0cm;">chmod 755 -R /export</p>

<p>4、切换到realtime用户下</p>

<p style="margin-left:0cm;">su realtime</p>

<h3 style="margin-left:0cm;"><strong>5.3 Kafka集群部署 </strong></h3>

<p><strong>5.3.1、下载安装包</strong></p>

<p style="margin-left:0cm;"><a href="http://kafka.apache.org/downloads.html" rel="nofollow">http://kafka.apache.org/downloads.html</a></p>

<p style="margin-left:0cm;">在linux中使用wget命令下载安装包</p>

<p style="margin-left:0cm;">        wget http://mirrors.hust.edu.cn/apache/kafka/0.8.2.2/kafka_2.11-0.8.2.2.tgz</p>

<p><strong>5.3.2、解压安装包</strong></p>

<p style="margin-left:0cm;">tar -zxvf /usr/apps/kafka/kafka_2.11-0.8.2.2.tgz -C /usr/apps/kafka/</p>

<p style="margin-left:0cm;">cd  /usr/apps/kafka/</p>

<p style="margin-left:0cm;">ln -s kafka_2.11-0.8.2.2 kafka</p>

<p><strong>5.3.3、修改配置文件</strong></p>

<p style="margin-left:0cm;"><strong><u>修改配置文件：</u></strong></p>

<p style="margin-left:0cm;">export KAFKA_HOME=/usr/apps/kafka/kafka_2.12-2.0.0</p>

<p style="margin-left:0cm;">export PATH=$PATH:$KAFKA_HOME/bin</p>

<p style="margin-left:0cm;"><strong><u>修改config</u></strong><strong><u>文件：</u></strong></p>

<p style="margin-left:0cm;">cp   /usr/apps/kafka/kafka_2.12-2.0.0/config/server.properties</p>

<p style="margin-left:0cm;">/usr/apps/kafka/kafka_2.12-2.0.0/config/server.properties.bak</p>

<p style="margin-left:0cm;">vi   /usr/apps/kafka/kafka_2.12-2.0.0/config/server.properties</p>

<p style="margin-left:0cm;">输入以下内容：</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="562" src="https://img-blog.csdn.net/20180902232249883?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdmFuMTIzNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="612"></p>

<p><strong>5.3.4、分发安装包</strong></p>

<p style="margin-left:0cm;">scp -r /usr/apps/kafka hdp-node-2:/export/servers</p>

<p style="margin-left:0cm;">scp -r /usr/apps/kafka hdp-node-3:/export/servers</p>

<p style="margin-left:0cm;">然后分别在各机器上创建软连</p>

<p style="margin-left:0cm;">cd /usr/apps/kafka/</p>

<p style="margin-left:0cm;">ln -s kafka_2.11-0.8.2.2 kafka</p>

<p><strong>5.3.5、再次修改配置文件（重要）</strong></p>

<p style="margin-left:0cm;"><strong><span style="color:#ff0000;">依次修改各服务器上配置文件的的</span><span style="color:#ff0000;">broker.id</span></strong><strong><span style="color:#ff0000;">，分别是</span><span style="color:#ff0000;">0,1,2</span></strong><strong><span style="color:#ff0000;">不得重复。</span></strong></p>

<p><strong>5.3.6、启动集群</strong></p>

<p style="margin-left:0cm;">依次在各节点上启动kafka</p>

<p style="margin-left:0cm;">./kafka-server-start.sh /usr/apps/kafka/kafka_2.12-2.0.0/config/server.properties &amp;</p>

<h3 style="margin-left:0cm;"><strong>5.4、Kafka常用操作命令</strong></h3>

<p>1、查看当前服务器中的所有topic</p>

<p style="margin-left:0cm;">./kafka-topics.sh --list --zookeeper hdp-node-1:2181</p>

<p>2、创建topic</p>

<p style="margin-left:0cm;">./kafka-topics.sh --create --zookeeper hdp-node-1:2181 --replication-factor 1 --partitions 3 --topic first</p>

<p style="margin-left:0cm;">解释：</p>

<p style="margin-left:0cm;">--partitions：分区数；</p>

<p style="margin-left:0cm;">--replication-factor：备份的副本数；</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="143" src="https://img-blog.csdn.net/2018090223225082?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdmFuMTIzNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="912"></p>

<p>3、删除topic</p>

<p style="margin-left:0cm;">./kafka-topics.sh --delete --zookeeper hdp-node-1:2181 --topic first</p>

<p style="margin-left:42pt;">需要server.properties中设置delete.topic.enable=true否则只是标记删除或者直接重启。</p>

<p>4、通过shell命令发送消息</p>

<p style="margin-left:0cm;">./kafka-console-producer.sh --broker-list hdp-node-1:9092 --topic first</p>

<p>5、通过shell消费消息</p>

<p style="margin-left:0cm;">./<span style="color:#4f4f4f;">kafka-console-consumer.sh --bootstrap-server </span>hdp-node-1<span style="color:#4f4f4f;">:9092 --from-beginning --topic </span>first</p>

<p>6、查看消费位置</p>

<p style="margin-left:0cm;">./kafka-run-class.sh kafka.tools.ConsumerOffsetChecker --zookeeper hdp-node-1:2181 --group testGroup</p>

<p>7、查看某个Topic的详情</p>

<p style="margin-left:0cm;">./kafka-topics.sh --topic test --describe --zookeeper hdp-node-1:2181</p>

<h2 style="margin-left:0cm;"><strong>6、Kafka生产者Java API</strong></h2>

<p style="margin-left:0cm;"><img alt="" class="has" height="525" src="https://img-blog.csdn.net/20180902232250168?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdmFuMTIzNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="940"></p>

<p style="margin-left:0cm;"><strong><em><u>Kafka</u></em></strong><strong><em><u>设置的文件为：</u></em></strong></p>

<p style="margin-left:0cm;">主题是test；</p>

<p style="margin-left:0cm;">Serizlizer.class，kafka.serializer.stringEncoder为序列化类，如果写了自定义的序列化，可以替换掉第二个；</p>

<p style="margin-left:0cm;">Broker.list中定义了kafka对应的broker对应的服务器list；</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="271" src="https://img-blog.csdn.net/20180902232250207?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdmFuMTIzNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="592"></p>

<p style="margin-left:0cm;">另外，配置中还有其他的可选数据配置：</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="137" src="https://img-blog.csdn.net/20180902232250186?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdmFuMTIzNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="546"></p>

<h2 style="margin-left:0cm;"><strong>7、Kafka消费者Java API</strong></h2>

<p style="margin-left:0cm;"><img alt="" class="has" height="569" src="https://img-blog.csdn.net/20180902232250192?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdmFuMTIzNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="971"></p>

<p style="margin-left:0cm;">消费者可以通过建立线程池，线程池中通过迭代器取出一个个线程，然后运行。</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="240" src="https://img-blog.csdn.net/20180902232250274?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdmFuMTIzNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="792"></p>

<h3 style="margin-left:0cm;"><strong>8、Kafka整体结构图</strong></h3>

<p style="margin-left:0cm;">Kafka名词解释和工作方式</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="690" src="https://img-blog.csdn.net/20180902232251117?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdmFuMTIzNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1200"></p>

<p style="margin-left:0cm;">1）由于每天系统产生的数据比较多，因此需要对数据进行分组：partition0、1、2、3，这几个partition分布在几个不同的服务器上；</p>

<p style="margin-left:0cm;">2）生产者的数据根据生产者规则，将数据分到不同的partition上，一般会有两种方式：取模和随机；</p>

<p style="margin-left:0cm;">各个组件的介绍为：</p>

<ol><li>Producer ：消息生产者，就是向kafka broker发消息的客户端。<span style="color:#444444;">Producer</span><span style="color:#444444;">将消息发布到它指定的</span><span style="color:#444444;">topic</span><span style="color:#444444;">中，并负责决定发布到哪个分区。通常简单的由负载均衡机制随机选择分区，但也可以通过特定的分区函数选择分区。使用的更多的是第二种。</span>。</li>
	<li>Consumer ：消息消费者，向kafka broker取消息的客户端</li>
	<li>Topic ：咋们可以理解为一个队列。</li>
	<li>Consumer Group （CG）：这是kafka用来实现一个topic消息的广播（发给所有的consumer）和单播（发给任意一个consumer）的手段。一个topic可以有多个CG。topic的消息会复制（不是真的复制，是概念上的）到所有的CG，但每个partion只会把消息发给该CG中的一个consumer。如果需要实现广播，只要每个consumer有一个独立的CG就可以了。要实现单播只要所有的consumer在同一个CG。用CG还可以将consumer进行自由的分组而不需要多次发送消息到不同的topic。</li>
	<li>Broker ：一台kafka服务器就是一个broker。一个集群由多个broker组成。一个broker可以容纳多个topic。</li>
	<li>Partition：为了实现扩展性，一个非常大的topic可以分布到多个broker（即服务器）上，一个topic可以分为多个partition，每个partition是一个有序的队列。partition中的每条消息都会被分配一个有序的id（offset）。kafka只保证按一个partition中的顺序将消息发给consumer，不保证一个topic的整体（多个partition间）的顺序。</li>
	<li>Offset：kafka的存储文件都是按照offset.kafka来命名，用offset做名字的好处是方便查找。例如你想找位于2049的位置，只要找到2048.kafka的文件即可。当然the first offset就是00000000000.kafka</li>
</ol><h2 style="margin-left:0cm;"><strong>9、Consumer与topic关系</strong></h2>

<p style="margin-left:0cm;">本质上kafka只支持Topic；</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="638" src="https://img-blog.csdn.net/2018090223225388?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdmFuMTIzNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="986"></p>

<p>1、每个group中可以有多个consumer，每个consumer属于一个consumer group；</p>

<p style="margin-left:0cm;">通常情况下，一个group中会包含多个consumer，这样不仅可以提高topic中消息的并发消费能力，而且还能提高"故障容错"性，如果group中的某个consumer失效那么其消费的partitions将会有其他consumer自动接管。</p>

<p>2、对于Topic中的一条特定的消息，只会被订阅此Topic的每个group中的其中一个consumer消费，此消息不会发送给一个group的多个consumer；</p>

<p style="margin-left:0cm;">那么一个group中所有的consumer将会交错的消费整个Topic，每个group中consumer消息消费互相独立，我们可以认为一个group是一个"订阅"者。</p>

<p>3、在kafka中,一个partition中的消息只会被group中的一个consumer消费<strong><span style="color:#ff0000;">(</span></strong><strong><span style="color:#ff0000;">同一时刻</span><span style="color:#ff0000;">)</span></strong>；</p>

<p style="margin-left:0cm;">一个Topic中的每个partitions，只会被一个"订阅者"中的一个consumer消费，不过一个consumer可以同时消费多个partitions中的消息。</p>

<p>4、kafka的设计原理决定,对于一个topic，同一个group中不能有多于partitions个数的consumer同时消费，否则将意味着某些consumer将无法得到消息。</p>

<p style="margin-left:0cm;"><strong><span style="color:#ff0000;">kafka</span></strong><strong><span style="color:#ff0000;">只能保证一个</span><span style="color:#ff0000;">partition</span></strong><strong><span style="color:#ff0000;">中的消息被某个</span><span style="color:#ff0000;">consumer</span></strong><strong><span style="color:#ff0000;">消费时是顺序的；事实上，从</span><span style="color:#ff0000;">Topic</span></strong><strong><span style="color:#ff0000;">角度来说</span><span style="color:#ff0000;">,</span></strong><strong><span style="color:#ff0000;">当有多个</span><span style="color:#ff0000;">partitions</span></strong><strong><span style="color:#ff0000;">时</span><span style="color:#ff0000;">,</span></strong><strong><span style="color:#ff0000;">消息仍不是全局有序的。</span></strong></p>

<p style="margin-left:0cm;"><strong><span style="color:#ff0000;">kafka</span></strong><strong><span style="color:#ff0000;">如何保证数据的完全生产</span></strong></p>

<p style="margin-left:0cm;"><strong><span style="color:#ff0000;">ack</span></strong><strong><span style="color:#ff0000;">机制：</span><span style="color:#ff0000;">broker</span></strong><strong><span style="color:#ff0000;">表示发来的数据已确认接收无误，表示数据已经保存到磁盘。</span></strong></p>

<p style="margin-left:0cm;"><strong><span style="color:#ff0000;">0</span></strong><strong><span style="color:#ff0000;">：不等待</span><span style="color:#ff0000;">broker</span></strong><strong><span style="color:#ff0000;">返回确认消息</span></strong></p>

<p style="margin-left:0cm;"><strong><span style="color:#ff0000;">1</span></strong><strong><span style="color:#ff0000;">：等待</span><span style="color:#ff0000;">topic</span></strong><strong><span style="color:#ff0000;">中某个</span><span style="color:#ff0000;">partition leader</span></strong><strong><span style="color:#ff0000;">保存成功的状态反馈</span></strong></p>

<p style="margin-left:0cm;"><strong><span style="color:#ff0000;">-1</span></strong><strong><span style="color:#ff0000;">：等待</span><span style="color:#ff0000;">topic</span></strong><strong><span style="color:#ff0000;">中某个</span><span style="color:#ff0000;">partition </span></strong><strong><span style="color:#ff0000;">所有副本都保存成功的状态反馈</span></strong></p>

<p style="margin-left:0cm;"><strong><span style="color:#ff0000;">broker</span></strong><strong><span style="color:#ff0000;">如何保存数据</span></strong></p>

<p style="margin-left:0cm;"><strong><span style="color:#ff0000;">在理论环境下，</span><span style="color:#ff0000;">broker</span></strong><strong><span style="color:#ff0000;">按照顺序读写的机制，可以每秒保存</span><span style="color:#ff0000;">600M</span></strong><strong><span style="color:#ff0000;">的数据。主要通过</span><span style="color:#ff0000;">pagecache</span></strong><strong><span style="color:#ff0000;">机制，尽可能的利用当前物理机器上的空闲内存来做缓存。</span></strong></p>

<p style="margin-left:0cm;"><strong><span style="color:#ff0000;">当前</span><span style="color:#ff0000;">topic</span></strong><strong><span style="color:#ff0000;">所属的</span><span style="color:#ff0000;">broker</span></strong><strong><span style="color:#ff0000;">，必定有一个该</span><span style="color:#ff0000;">topic</span></strong><strong><span style="color:#ff0000;">的</span><span style="color:#ff0000;">partition</span></strong><strong><span style="color:#ff0000;">，</span><span style="color:#ff0000;">partition</span></strong><strong><span style="color:#ff0000;">是一个磁盘目录。</span><span style="color:#ff0000;">partition</span></strong><strong><span style="color:#ff0000;">的目录中有多个</span><span style="color:#ff0000;">segment</span></strong><strong><span style="color:#ff0000;">组合</span><span style="color:#ff0000;">(index,log)</span></strong></p>

<h2 style="margin-left:0cm;"><strong>10、Kafka消息的分发</strong></h2>

<p style="margin-left:0cm;"><strong><span style="color:#ff0000;">Producer</span></strong><strong><span style="color:#ff0000;">客户端负责消息的分发</span></strong></p>

<ol><li>kafka集群中的任何一个broker都可以向producer提供metadata信息,这些metadata中包含"集群中存活的servers列表"/"partitions leader列表"等信息；</li>
	<li>当producer获取到metadata信息之后, producer将会和Topic下所有partition leader保持socket连接；</li>
	<li>消息由producer直接通过socket发送到broker，中间不会经过任何"路由层"，事实上，消息被路由到哪个partition上由producer客户端决定；</li>
</ol><p style="margin-left:0cm;">比如可以采用"random""key-hash""轮询"等,<strong><span style="color:#ff0000;">如果一个</span><span style="color:#ff0000;">topic</span></strong><strong><span style="color:#ff0000;">中有多个</span><span style="color:#ff0000;">partitions,</span></strong><strong><span style="color:#ff0000;">那么在</span><span style="color:#ff0000;">producer</span></strong><strong><span style="color:#ff0000;">端实现</span><span style="color:#ff0000;">"</span></strong><strong><span style="color:#ff0000;">消息均衡分发</span><span style="color:#ff0000;">"</span></strong><strong><span style="color:#ff0000;">是必要的。</span></strong></p>

<p>在producer端的配置文件中,开发者可以指定partition路由的方式。</p>

<p style="margin-left:0cm;">Producer消息发送的应答机制</p>

<p style="margin-left:0cm;">设置发送数据是否需要服务端的反馈,有三个值0,1,-1；</p>

<p style="margin-left:0cm;">0: producer不会等待broker发送ack；</p>

<p style="margin-left:0cm;">1: 当leader接收到消息之后发送ack；</p>

<p style="margin-left:0cm;">-1: 当所有的follower都同步消息成功后发送ack；</p>

<p style="margin-left:0cm;">         request.required.acks=0</p>

<h2 style="margin-left:0cm;"><strong><strong><span style="color:#333333;">11</span></strong><strong><span style="color:#333333;">、</span></strong><strong><span style="color:#333333;">Consumer</span></strong><strong><span style="color:#333333;">的负载均衡</span></strong></strong></h2>

<p style="margin-left:0cm;">当一个group中,有consumer加入或者离开时,会触发partitions均衡.均衡的最终目的,是提升topic的并发消费能力，步骤如下：</p>

<ol><li>假如topic1,具有如下partitions: P0,P1,P2,P3</li>
	<li>加入group中,有如下consumer: C1,C2</li>
	<li>首先根据partition索引号对partitions排序: P0,P1,P2,P3</li>
	<li>根据consumer.id排序: C0,C1</li>
	<li>计算倍数: M = [P0,P1,P2,P3].size / [C0,C1].size，本例值M=2(向上取整)</li>
	<li>然后依次分配partitions: C0 = [P0,P1],C1=[P2,P3],即Ci = [P(i * M),P((i + 1) * M -1)]</li>
</ol><p style="margin-left:0cm;"><img alt="" class="has" height="535" src="https://img-blog.csdn.net/20180902232251822?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdmFuMTIzNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1200"></p>

<p style="margin-left:0cm;">由上面的计算，C的数量需要小于等于P的数量，如果C的数量多于P，多出来的C的个数是空闲的，主要是分配时难以分配partition给到C。</p>

<p style="margin-left:0cm;"><strong><span style="color:#ff0000;">partition</span></strong><strong><span style="color:#ff0000;">如何分布在不同的</span><span style="color:#ff0000;">broker</span></strong><strong><span style="color:#ff0000;">上：</span></strong></p>

<p style="margin-left:0cm;"><strong><span style="color:#ff0000;">int i = 0</span></strong></p>

<p style="margin-left:0cm;"><strong><span style="color:#ff0000;">list{kafka01,kafka02,kafka03}</span></strong></p>

<p style="margin-left:0cm;"><strong><span style="color:#ff0000;">for(int i=0;i&lt;5;i++){</span></strong></p>

<p style="margin-left:0cm;"><strong><span style="color:#ff0000;">         brIndex = i%broker;</span></strong></p>

<p style="margin-left:0cm;"><strong><span style="color:#ff0000;">         hostName = list.get(brIndex)</span></strong></p>

<p style="margin-left:0cm;"><strong><span style="color:#ff0000;">}</span></strong></p>

<p style="margin-left:0cm;"><span style="color:#444444;">发布消息通常有两种模式：队列模式（</span><span style="color:#444444;">queuing</span><span style="color:#444444;">）和发布</span><span style="color:#444444;">-</span><span style="color:#444444;">订阅模式</span><span style="color:#444444;">(publish-subscribe)</span><span style="color:#444444;">。队列模式中，</span><span style="color:#444444;">consumers</span><span style="color:#444444;">可以同时从服务端读取消息，每个消息只被其中一个</span><span style="color:#444444;">consumer</span><span style="color:#444444;">读到；发布</span><span style="color:#444444;">-</span><span style="color:#444444;">订阅模式中消息被广播到所有的</span><span style="color:#444444;">consumer</span><span style="color:#444444;">中。</span><span style="color:#444444;">Consumers</span><span style="color:#444444;">可以加入一个</span><span style="color:#444444;">consumer </span><span style="color:#444444;">组，共同竞争一个</span><span style="color:#444444;">topic</span><span style="color:#444444;">，</span><span style="color:#444444;">topic</span><span style="color:#444444;">中的消息将被分发到组中的一个成员中。同一组中的</span><span style="color:#444444;">consumer</span><span style="color:#444444;">可以在不同的程序中，也可以在不同的机器上。如果所有的</span><span style="color:#444444;">consumer</span><span style="color:#444444;">都在一个组中，这就成为了传统的队列模式，在各</span><span style="color:#444444;">consumer</span><span style="color:#444444;">中实现负载均衡。如果所有的</span><span style="color:#444444;">consumer</span><span style="color:#444444;">都不在不同的组中，这就成为了发布</span><span style="color:#444444;">-</span><span style="color:#444444;">订阅模式，所有的消息都被分发到所有的</span><span style="color:#444444;">consumer</span><span style="color:#444444;">中。更常见的是，每个</span><span style="color:#444444;">topic</span><span style="color:#444444;">都有若干数量的</span><span style="color:#444444;">consumer</span><span style="color:#444444;">组，每个组都是一个逻辑上的</span><span style="color:#444444;">“</span><span style="color:#444444;">订阅者</span><span style="color:#444444;">”</span><span style="color:#444444;">，为了容错和更好的稳定性，每个组由若干</span><span style="color:#444444;">consumer</span><span style="color:#444444;">组成。这其实就是一个发布</span><span style="color:#444444;">-</span><span style="color:#444444;">订阅模式，只不过订阅者是个组而不是单个</span><span style="color:#444444;">consumer</span><span style="color:#444444;">。</span></p>

<h2 style="margin-left:0cm;"><strong><span style="color:#4f4f4f;">12、</span><span style="color:#4f4f4f;">Kafka</span><span style="color:#4f4f4f;">核心特性</span></strong></h2>

<h3 style="margin-left:0cm;"><strong><a name="t15"></a><span style="color:#4f4f4f;">12.1、</span><span style="color:#4f4f4f;">压缩</span></strong></h3>

<p style="margin-left:0cm;"><span style="color:#444444;">我们上面已经知道了</span><span style="color:#444444;">Kafka</span><span style="color:#444444;">支持以集合（</span><span style="color:#444444;">batch</span><span style="color:#444444;">）为单位发送消息，在此基础上，</span><span style="color:#444444;">Kafka</span><span style="color:#444444;">还支持对消息集合进行压缩，</span><span style="color:#444444;">Producer</span><span style="color:#444444;">端可以通过</span><span style="color:#444444;">GZIP</span><span style="color:#444444;">或</span><span style="color:#444444;">Snappy</span><span style="color:#444444;">格式对消息集合进行压缩。</span><span style="color:#444444;">Producer</span><span style="color:#444444;">端进行压缩之后，在</span><span style="color:#444444;">Consumer</span><span style="color:#444444;">端需进行解压。压缩的好处就是减少传输的数据量，减轻对网络传输的压力，在对大数据处理上，瓶颈往往体现在网络上而不是</span><span style="color:#444444;">CPU</span><span style="color:#444444;">（压缩和解压会耗掉部分</span><span style="color:#444444;">CPU</span><span style="color:#444444;">资源）。</span> <br><span style="color:#444444;">那么如何区分消息是压缩的还是未压缩的呢，</span><span style="color:#444444;">Kafka</span><span style="color:#444444;">在消息头部添加了一个描述压缩属性字节，这个字节的后两位表示消息的压缩采用的编码，如果后两位为</span><span style="color:#444444;">0</span><span style="color:#444444;">，则表示消息未被压缩。</span></p>

<h3 style="margin-left:0cm;"><strong><a name="t16"></a><span style="color:#4f4f4f;">12.2、</span><span style="color:#4f4f4f;">消息可靠性</span></strong></h3>

<p style="margin-left:0cm;"><span style="color:#444444;">在消息系统中，保证消息在生产和消费过程中的可靠性是十分重要的，在实际消息传递过程中，可能会出现如下三中情况：</span></p>

<p style="margin-left:0cm;"><span style="color:#444444;">一个消息发送失败</span></p>

<p style="margin-left:0cm;"><span style="color:#444444;">一个消息被发送多次</span></p>

<p style="margin-left:0cm;"><span style="color:#444444;">最理想的情况：</span><span style="color:#444444;">exactly-once ,</span><span style="color:#444444;">一个消息发送成功且仅发送了一次</span></p>

<p style="margin-left:0cm;"><span style="color:#444444;">有许多系统声称它们实现了</span><span style="color:#444444;">exactly-once</span><span style="color:#444444;">，但是它们其实忽略了生产者或消费者在生产和消费过程中有可能失败的情况。比如虽然一个</span><span style="color:#444444;">Producer</span><span style="color:#444444;">成功发送一个消息，但是消息在发送途中丢失，或者成功发送到</span><span style="color:#444444;">broker</span><span style="color:#444444;">，也被</span><span style="color:#444444;">consumer</span><span style="color:#444444;">成功取走，但是这个</span><span style="color:#444444;">consumer</span><span style="color:#444444;">在处理取过来的消息时失败了。</span> <br><span style="color:#444444;">从</span><span style="color:#444444;">Producer</span><span style="color:#444444;">端看：</span><span style="color:#444444;">Kafka</span><span style="color:#444444;">是这么处理的，当一个消息被发送后，</span><span style="color:#444444;">Producer</span><span style="color:#444444;">会等待</span><span style="color:#444444;">broker</span><span style="color:#444444;">成功接收到消息的反馈（可通过参数控制等待时间），如果消息在途中丢失或是其中一个</span><span style="color:#444444;">broker</span><span style="color:#444444;">挂掉，</span><span style="color:#444444;">Producer</span><span style="color:#444444;">会重新发送（我们知道</span><span style="color:#444444;">Kafka</span><span style="color:#444444;">有备份机制，可以通过参数控制是否等待所有备份节点都收到消息）。</span> <br><span style="color:#444444;">从</span><span style="color:#444444;">Consumer</span><span style="color:#444444;">端看：前面讲到过</span><span style="color:#444444;">partition</span><span style="color:#444444;">，</span><span style="color:#444444;">broker</span><span style="color:#444444;">端记录了</span><span style="color:#444444;">partition</span><span style="color:#444444;">中的一个</span><span style="color:#444444;">offset</span><span style="color:#444444;">值，这个值指向</span><span style="color:#444444;">Consumer</span><span style="color:#444444;">下一个即将消费</span><span style="color:#444444;">message</span><span style="color:#444444;">。当</span><span style="color:#444444;">Consumer</span><span style="color:#444444;">收到了消息，但却在处理过程中挂掉，此时</span><span style="color:#444444;">Consumer</span><span style="color:#444444;">可以通过这个</span><span style="color:#444444;">offset</span><span style="color:#444444;">值重新找到上一个消息再进行处理。</span><span style="color:#444444;">Consumer</span><span style="color:#444444;">还有权限控制这个</span><span style="color:#444444;">offset</span><span style="color:#444444;">值，对持久化到</span><span style="color:#444444;">broker</span><span style="color:#444444;">端的消息做任意处理。</span></p>

<h3 style="margin-left:0cm;"><strong><a name="t17"></a><span style="color:#4f4f4f;">12.3、</span><span style="color:#4f4f4f;">备份机制</span></strong></h3>

<p style="margin-left:0cm;"><span style="color:#444444;">备份机制是</span><span style="color:#444444;">Kafka0.8</span><span style="color:#444444;">版本的新特性，备份机制的出现大大提高了</span><span style="color:#444444;">Kafka</span><span style="color:#444444;">集群的可靠性、稳定性。有了备份机制后，</span><span style="color:#444444;">Kafka</span><span style="color:#444444;">允许集群中的节点挂掉后而不影响整个集群工作。一个备份数量为</span><span style="color:#444444;">n</span><span style="color:#444444;">的集群允许</span><span style="color:#444444;">n-1</span><span style="color:#444444;">个节点失败。在所有备份节点中，有一个节点作为</span><span style="color:#444444;">lead</span><span style="color:#444444;">节点，这个节点保存了其它备份节点列表，并维持各个备份间的状体同步。下面这幅图解释了</span><span style="color:#444444;">Kafka</span><span style="color:#444444;">的备份机制</span><span style="color:#444444;">:</span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="465" src="https://img-blog.csdn.net/20180902232251704?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdmFuMTIzNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="562"></p>

<h3 style="margin-left:0cm;"><strong><a name="t18"></a><span style="color:#4f4f4f;">12.4、Kafka</span><span style="color:#4f4f4f;">高效性相关设计</span></strong></h3>

<p><strong><span style="color:#4f4f4f;">12.4.1 </span><span style="color:#4f4f4f;">消息的持久化</span></strong></p>

<p style="margin-left:0cm;"><span style="color:#444444;">Kafka</span><span style="color:#444444;">高度依赖文件系统来存储和缓存消息，一般的人认为磁盘是缓慢的，这导致人们对持久化结构具有竞争性持怀疑态度。其实，磁盘远比你想象的要快或者慢，这决定于我们如何使用磁盘。</span> <br><span style="color:#444444;">一个和磁盘性能有关的关键事实是：磁盘驱动器的吞吐量跟寻到延迟是相背离的，也就是所，线性写的速度远远大于随机写。比如：在一个</span><span style="color:#444444;">6 7200rpm SATA RAID-5 </span><span style="color:#444444;">的磁盘阵列上线性写的速度大概是</span><span style="color:#444444;">600M/</span><span style="color:#444444;">秒，但是随机写的速度只有</span><span style="color:#444444;">100K/</span><span style="color:#444444;">秒，两者相差将近</span><span style="color:#444444;">6000</span><span style="color:#444444;">倍。线性读写在大多数应用场景下是可以预测的，因此，操作系统利用</span><span style="color:#444444;">read-ahead</span><span style="color:#444444;">和</span><span style="color:#444444;">write-behind</span><span style="color:#444444;">技术来从大的数据块中预取数据，或者将多个逻辑上的写操作组合成一个大写物理写操作中。更多的讨论可以在</span><span style="color:#444444;"><a href="http://queue.acm.org/detail.cfm?id=1563874" rel="nofollow"><span style="color:#444444;">ACMQueueArtical</span></a></span><span style="color:#444444;">中找到，他们发现，对磁盘的线性读在有些情况下可以比内存的随机访问要快一些。</span> <br><span style="color:#444444;">为了补偿这个性能上的分歧，现代操作系统都会把空闲的内存用作磁盘缓存，尽管在内存回收的时候会有一点性能上的代价。所有的磁盘读写操作会在这个统一的缓存上进行。</span> <br><span style="color:#444444;">此外，如果我们是在</span><span style="color:#444444;">JVM</span><span style="color:#444444;">的基础上构建的，熟悉</span><span style="color:#444444;">java</span><span style="color:#444444;">内存应用管理的人应该清楚以下两件事情：</span></p>

<p style="margin-left:0cm;"><span style="color:#444444;">一个对象的内存消耗是非常高的，经常是所存数据的两倍或者更多。</span></p>

<p style="margin-left:0cm;"><span style="color:#444444;">随着堆内数据的增多，</span><span style="color:#444444;">Java</span><span style="color:#444444;">的垃圾回收会变得非常昂贵。</span></p>

<p style="margin-left:0cm;"><span style="color:#444444;">基于这些事实，利用文件系统并且依靠页缓存比维护一个内存缓存或者其他结构要好</span><span style="color:#444444;">——</span><span style="color:#444444;">我们至少要使得可用的缓存加倍，通过自动访问可用内存，并且通过存储更紧凑的字节结构而不是一个对象，这将有可能再次加倍。这么做的结果就是在一台</span><span style="color:#444444;">32GB</span><span style="color:#444444;">的机器上，如果不考虑</span><span style="color:#444444;">GC</span><span style="color:#444444;">惩罚，将最多有</span><span style="color:#444444;">28-30GB</span><span style="color:#444444;">的缓存。此外，这些缓存将会一直存在即使服务重启，然而进程内缓存需要在内存中重构（</span><span style="color:#444444;">10GB</span><span style="color:#444444;">缓存需要花费</span><span style="color:#444444;">10</span><span style="color:#444444;">分钟）或者它需要一个完全冷缓存启动（非常差的初始化性能）。它同时也简化了代码，因为现在所有的维护缓存和文件系统之间内聚的逻辑都在操作系统内部了，这使得这样做比</span><span style="color:#444444;">one-off in-process attempts</span><span style="color:#444444;">更加高效与准确。如果你的磁盘应用更加倾向于顺序读取，那么</span><span style="color:#444444;">read-ahead</span><span style="color:#444444;">在每次磁盘读取中实际上获取到这人缓存中的有用数据。</span> <br><span style="color:#444444;">以上这些建议了一个简单的设计：不同于维护尽可能多的内存缓存并且在需要的时候刷新到文件系统中，我们换一种思路。所有的数据不需要调用刷新程序，而是立刻将它写到一个持久化的日志中。事实上，这仅仅意味着，数据将被传输到内核页缓存中并稍后被刷新。我们可以增加一个配置项以让系统的用户来控制数据在什么时候被刷新到物理硬盘上。</span></p>

<p><strong><span style="color:#4f4f4f;">12.4.2 </span><span style="color:#4f4f4f;">常数时间性能保证</span></strong></p>

<p style="margin-left:0cm;"><span style="color:#444444;">消息系统中持久化数据结构的设计通常是维护者一个和消费队列有关的</span><span style="color:#444444;">B</span><span style="color:#444444;">树或者其它能够随机存取结构的元数据信息。</span><span style="color:#444444;">B</span><span style="color:#444444;">树是一个很好的结构，可以用在事务型与非事务型的语义中。但是它需要一个很高的花费，尽管</span><span style="color:#444444;">B</span><span style="color:#444444;">树的操作需要</span><span style="color:#444444;">O(logN)</span><span style="color:#444444;">。通常情况下，这被认为与常数时间等价，但这对磁盘操作来说是不对的。磁盘寻道一次需要</span><span style="color:#444444;">10ms</span><span style="color:#444444;">，并且一次只能寻一个，因此并行化是受限的。</span> <br><span style="color:#444444;">直觉上来讲，一个持久化的队列可以构建在对一个文件的读和追加上，就像一般情况下的日志解决方案。尽管和</span><span style="color:#444444;">B</span><span style="color:#444444;">树相比，这种结构不能支持丰富的语义，但是它有一个优点，所有的操作都是常数时间，并且读写之间不会相互阻塞。这种设计具有极大的性能优势：最终系统性能和数据大小完全无关，服务器可以充分利用廉价的硬盘来提供高效的消息服务。</span> <br><span style="color:#444444;">事实上还有一点，磁盘空间的无限增大而不影响性能这点，意味着我们可以提供一般消息系统无法提供的特性。比如说，消息被消费后不是立马被删除，我们可以将这些消息保留一段相对比较长的时间（比如一个星期）。</span></p>

<p><strong><span style="color:#4f4f4f;">12.4.3</span><span style="color:#4f4f4f;">、</span><span style="color:#4f4f4f;">Kafka</span><span style="color:#4f4f4f;">高性能吞吐揭秘：</span></strong></p>

<p style="margin-left:0cm;"><span style="color:#444444;">Kafka</span><span style="color:#444444;">作为时下最流行的开源消息系统，被广泛地应用在数据缓冲、异步通信、汇集日志、系统解耦等方面。相比较于</span><span style="color:#444444;">RocketMQ</span><span style="color:#444444;">等其他常见消息系统，</span><span style="color:#444444;">Kafka</span><span style="color:#444444;">在保障了大部分功能特性的同时，还提供了超一流的读写性能。</span></p>

<p style="margin-left:0cm;"><span style="color:#444444;">不同于</span><span style="color:#444444;">Redis</span><span style="color:#444444;">和</span><span style="color:#444444;">MemcacheQ</span><span style="color:#444444;">等内存消息队列，</span><span style="color:#444444;">Kafka</span><span style="color:#444444;">的设计是把所有的</span><span style="color:#444444;">Message</span><span style="color:#444444;">都要写入速度低容量大的硬盘，以此来换取更强的存储能力。实际上，</span><span style="color:#444444;">Kafka</span><span style="color:#444444;">使用硬盘并没有带来过多的性能损失，“规规矩矩”的抄了一条“近道”。</span></p>

<p style="margin-left:0cm;"><span style="color:#444444;">首先，说“规规矩矩”是因为</span><span style="color:#444444;">Kafka</span><span style="color:#444444;">在磁盘上只做</span><span style="color:#444444;">Sequence I/O</span><span style="color:#444444;">，由于消息系统读写的特殊性，这并不存在什么问题。关于磁盘</span><span style="color:#444444;">I/O</span><span style="color:#444444;">的性能，引用一组</span><span style="color:#444444;">Kafka</span><span style="color:#444444;">官方给出的测试数据</span><span style="color:#444444;">(Raid-5</span><span style="color:#444444;">，</span><span style="color:#444444;">7200rpm)</span><span style="color:#444444;">：</span></p>

<p style="margin-left:0cm;"><span style="color:#444444;">Sequence I/O: 600MB/s</span></p>

<p style="margin-left:0cm;"><span style="color:#444444;">Random I/O: 100KB/s</span></p>

<p style="margin-left:0cm;"><span style="color:#444444;">所以通过只做</span><span style="color:#444444;">Sequence I/O</span><span style="color:#444444;">的限制，规避了磁盘访问速度低下对性能可能造成的影响。</span></p>

<p style="margin-left:0cm;"><span style="color:#444444;">接下来我们再聊一聊</span><span style="color:#444444;">Kafka</span><span style="color:#444444;">是如何“抄近道的”。</span></p>

<p style="margin-left:0cm;"><span style="color:#444444;">首先，</span><span style="color:#444444;">Kafka</span><span style="color:#444444;">重度依赖底层操作系统提供的</span><span style="color:#444444;">PageCache</span><span style="color:#444444;">功能。当上层有写操作时，操作系统只是将数据写入</span><span style="color:#444444;">PageCache</span><span style="color:#444444;">，同时标记</span><span style="color:#444444;">Page</span><span style="color:#444444;">属性为</span><span style="color:#444444;">Dirty</span><span style="color:#444444;">。当读操作发生时，先从</span><span style="color:#444444;">PageCache</span><span style="color:#444444;">中查找，如果发生缺页才进行磁盘调度，最终返回需要的数据。实际上</span><span style="color:#444444;">PageCache</span><span style="color:#444444;">是把尽可能多的空闲内存都当做了磁盘缓存来使用。同时如果有其他进程申请内存，回收</span><span style="color:#444444;">PageCache</span><span style="color:#444444;">的代价又很小，所以现代的</span><span style="color:#444444;">OS</span><span style="color:#444444;">都支持</span><span style="color:#444444;">PageCache</span><span style="color:#444444;">。</span></p>

<p style="margin-left:0cm;"><span style="color:#444444;">使用</span><span style="color:#444444;">PageCache</span><span style="color:#444444;">功能同时可以避免在</span><span style="color:#444444;">JVM</span><span style="color:#444444;">内部缓存数据，</span><span style="color:#444444;">JVM</span><span style="color:#444444;">为我们提供了强大的</span><span style="color:#444444;">GC</span><span style="color:#444444;">能力，同时也引入了一些问题不适用与</span><span style="color:#444444;">Kafka</span><span style="color:#444444;">的设计。</span></p>

<ul><li><span style="color:#444444;">如果在</span><span style="color:#444444;">Heap</span><span style="color:#444444;">内管理缓存，</span><span style="color:#444444;">JVM</span><span style="color:#444444;">的</span><span style="color:#444444;">GC</span><span style="color:#444444;">线程会频繁扫描</span><span style="color:#444444;">Heap</span><span style="color:#444444;">空间，带来不必要的开销。如果</span><span style="color:#444444;">Heap</span><span style="color:#444444;">过大，执行一次</span><span style="color:#444444;">Full GC</span><span style="color:#444444;">对系统的可用性来说将是极大的挑战。</span></li>
	<li><span style="color:#444444;">所有在</span><span style="color:#444444;">JVM</span><span style="color:#444444;">内的对象都不免带有一个</span><span style="color:#444444;">Object Overhead(</span><span style="color:#444444;">千万不可小视</span><span style="color:#444444;">)</span><span style="color:#444444;">，内存的有效空间利用率会因此降低。</span></li>
	<li><span style="color:#444444;">所有的</span><span style="color:#444444;">In-Process Cache</span><span style="color:#444444;">在</span><span style="color:#444444;">OS</span><span style="color:#444444;">中都有一份同样的</span><span style="color:#444444;">PageCache</span><span style="color:#444444;">。所以通过将缓存只放在</span><span style="color:#444444;">PageCache</span><span style="color:#444444;">，可以至少让可用缓存空间翻倍。</span></li>
	<li><span style="color:#444444;">如果</span><span style="color:#444444;">Kafka</span><span style="color:#444444;">重启，所有的</span><span style="color:#444444;">In-Process Cache</span><span style="color:#444444;">都会失效，而</span><span style="color:#444444;">OS</span><span style="color:#444444;">管理的</span><span style="color:#444444;">PageCache</span><span style="color:#444444;">依然可以继续使用。</span></li>
</ul><p style="margin-left:0cm;"><span style="color:#444444;">PageCache</span><span style="color:#444444;">还只是第一步，</span><span style="color:#444444;">Kafka</span><span style="color:#444444;">为了进一步的优化性能还采用了</span><span style="color:#444444;">Sendfile</span><span style="color:#444444;">技术。在解释</span><span style="color:#444444;">Sendfile</span><span style="color:#444444;">之前，首先介绍一下传统的网络</span><span style="color:#444444;">I/O</span><span style="color:#444444;">操作流程，大体上分为以下</span><span style="color:#444444;">4</span><span style="color:#444444;">步。</span></p>

<ol><li><span style="color:#444444;">OS </span><span style="color:#444444;">从硬盘把数据读到内核区的</span><span style="color:#444444;">PageCache</span><span style="color:#444444;">。</span></li>
	<li><span style="color:#444444;">用户进程把数据从内核区</span><span style="color:#444444;">Copy</span><span style="color:#444444;">到用户区。</span></li>
	<li><span style="color:#444444;">然后用户进程再把数据写入到</span><span style="color:#444444;">Socket</span><span style="color:#444444;">，数据流入内核区的</span><span style="color:#444444;">Socket Buffer</span><span style="color:#444444;">上。</span></li>
	<li><span style="color:#444444;">OS </span><span style="color:#444444;">再把数据从</span><span style="color:#444444;">Buffer</span><span style="color:#444444;">中</span><span style="color:#444444;">Copy</span><span style="color:#444444;">到网卡的</span><span style="color:#444444;">Buffer</span><span style="color:#444444;">上，这样完成一次发送。</span></li>
</ol><p style="margin-left:0cm;"><img alt="" class="has" height="339" src="https://img-blog.csdn.net/20180902232251737?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdmFuMTIzNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="444"></p>

<p style="margin-left:0cm;"><span style="color:#444444;">整个过程共经历两次</span><span style="color:#444444;">Context Switch</span><span style="color:#444444;">，四次</span><span style="color:#444444;">System Call</span><span style="color:#444444;">。同一份数据在内核</span><span style="color:#444444;">Buffer</span><span style="color:#444444;">与用户</span><span style="color:#444444;">Buffer</span><span style="color:#444444;">之间重复拷贝，效率低下。其中</span><span style="color:#444444;">2</span><span style="color:#444444;">、</span><span style="color:#444444;">3</span><span style="color:#444444;">两步没有必要，完全可以直接在内核区完成数据拷贝。这也正是</span><span style="color:#444444;">Sendfile</span><span style="color:#444444;">所解决的问题，经过</span><span style="color:#444444;">Sendfile</span><span style="color:#444444;">优化后，整个</span><span style="color:#444444;">I/O</span><span style="color:#444444;">过程就变成了下面这个样子。</span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="371" src="https://img-blog.csdn.net/20180902232251572?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdmFuMTIzNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="432"></p>

<p style="margin-left:0cm;"><span style="color:#444444;">通过以上的介绍不难看出，</span><span style="color:#444444;">Kafka</span><span style="color:#444444;">的设计初衷是尽一切努力在内存中完成数据交换，无论是对外作为一整个消息系统，或是内部同底层操作系统的交互。如果</span><span style="color:#444444;">Producer</span><span style="color:#444444;">和</span><span style="color:#444444;">Consumer</span><span style="color:#444444;">之间生产和消费进度上配合得当，完全可以实现数据交换零</span><span style="color:#444444;">I/O</span><span style="color:#444444;">。这也就是我为什么说</span><span style="color:#444444;">Kafka</span><span style="color:#444444;">使用“硬盘”并没有带来过多性能损失的原因。下面是我在生产环境中采到的一些指标。</span></p>

<p style="margin-left:0cm;"><span style="color:#444444;">(20 Brokers, 75 Partitions per Broker, 110k msg/s)</span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="693" src="https://img-blog.csdn.net/20180902232253645?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdmFuMTIzNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="833"></p>

<p style="margin-left:0cm;"><span style="color:#444444;">此时的集群只有写，没有读操作。</span><span style="color:#444444;">10M/s</span><span style="color:#444444;">左右的</span><span style="color:#444444;">Send</span><span style="color:#444444;">的流量是</span><span style="color:#444444;">Partition</span><span style="color:#444444;">之间进行</span><span style="color:#444444;">Replicate</span><span style="color:#444444;">而产生的。从</span><span style="color:#444444;">recv</span><span style="color:#444444;">和</span><span style="color:#444444;">writ</span><span style="color:#444444;">的速率比较可以看出，写盘是使用</span><span style="color:#444444;">Asynchronous+Batch</span><span style="color:#444444;">的方式，底层</span><span style="color:#444444;">OS</span><span style="color:#444444;">可能还会进行磁盘写顺序优化。而在有</span><span style="color:#444444;">Read Request</span><span style="color:#444444;">进来的时候分为两种情况，第一种是内存中完成数据交换。</span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="553" src="https://img-blog.csdn.net/20180902232253538?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdmFuMTIzNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="830"><span style="color:#444444;">Send流量从平均</span><span style="color:#444444;">10M/s</span><span style="color:#444444;">增加到了到平均</span><span style="color:#444444;">60M/s</span><span style="color:#444444;">，而磁盘</span><span style="color:#444444;">Read</span><span style="color:#444444;">只有不超过</span><span style="color:#444444;">50KB/s</span><span style="color:#444444;">。</span><span style="color:#444444;">PageCache</span><span style="color:#444444;">降低磁盘</span><span style="color:#444444;">I/O</span><span style="color:#444444;">效果非常明显。</span></p>

<p style="margin-left:0cm;"><span style="color:#444444;">接下来是读一些收到了一段时间，已经从内存中被换出刷写到磁盘上的老数据。</span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="234" src="https://img-blog.csdn.net/20180902232253354?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdmFuMTIzNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="831"></p>

<p style="margin-left:0cm;"><span style="color:#444444;">其他指标还是老样子，而磁盘</span><span style="color:#444444;">Read</span><span style="color:#444444;">已经飚高到</span><span style="color:#444444;">40+MB/s</span><span style="color:#444444;">。此时全部的数据都已经是走硬盘了</span><span style="color:#444444;">(</span><span style="color:#444444;">对硬盘的顺序读取</span><span style="color:#444444;">OS</span><span style="color:#444444;">层会进行</span><span style="color:#444444;">Prefill PageCache</span><span style="color:#444444;">的优化</span><span style="color:#444444;">)</span><span style="color:#444444;">。依然没有任何性能问题。</span></p>

<p style="margin-left:0cm;"><strong><em><u><span style="color:#444444;">Tips</span></u></em></strong><strong><em><u><span style="color:#444444;">：</span></u></em></strong></p>

<ol><li><span style="color:#444444;">Kafka</span><span style="color:#444444;">官方并不建议通过</span><span style="color:#444444;">Broker</span><span style="color:#444444;">端的</span><span style="color:#444444;">log.flush.interval.messages</span><span style="color:#444444;">和</span><span style="color:#444444;">log.flush.interval.ms</span><span style="color:#444444;">来强制写盘，认为数据的可靠性应该通过</span><span style="color:#444444;">Replica</span><span style="color:#444444;">来保证，而强制</span><span style="color:#444444;">Flush</span><span style="color:#444444;">数据到磁盘会对整体性能产生影响。</span></li>
	<li><span style="color:#444444;">可以通过调整</span><span style="color:#444444;">/proc/sys/vm/dirty_background_ratio</span><span style="color:#444444;">和</span><span style="color:#444444;">/proc/sys/vm/dirty_ratio</span><span style="color:#444444;">来调优性能。</span></li>
	<li><span style="color:#444444;">脏页率超过第一个指标会启动</span><span style="color:#444444;">pdflush</span><span style="color:#444444;">开始</span><span style="color:#444444;">Flush Dirty PageCache</span><span style="color:#444444;">。</span></li>
	<li><span style="color:#444444;">脏页率超过第二个指标会阻塞所有的写操作来进行</span><span style="color:#444444;">Flush</span><span style="color:#444444;">。</span></li>
	<li><span style="color:#444444;">根据不同的业务需求可以适当的降低</span><span style="color:#444444;">dirty_background_ratio</span><span style="color:#444444;">和提高</span><span style="color:#444444;">dirty_ratio</span><span style="color:#444444;">。</span></li>
</ol><p style="margin-left:0cm;"><strong><em><u><span style="color:#444444;">Partition</span></u></em></strong><strong><em><u><span style="color:#444444;">：</span></u></em></strong></p>

<p style="margin-left:0cm;"><span style="color:#444444;">Partition</span><span style="color:#444444;">是</span><span style="color:#444444;">Kafka</span><span style="color:#444444;">可以很好的横向扩展和提供高并发处理以及实现</span><span style="color:#444444;">Replication</span><span style="color:#444444;">的基础。</span></p>

<p style="margin-left:0cm;"><strong><u><span style="color:#444444;">1</span></u></strong><strong><u><span style="color:#444444;">）扩展性方面：</span></u></strong><span style="color:#444444;">首先，</span><span style="color:#444444;">Kafka</span><span style="color:#444444;">允许</span><span style="color:#444444;">Partition</span><span style="color:#444444;">在集群内的</span><span style="color:#444444;">Broker</span><span style="color:#444444;">之间任意移动，以此来均衡可能存在的数据倾斜问题。其次，</span><span style="color:#444444;">Partition</span><span style="color:#444444;">支持自定义的分区算法，例如可以将同一个</span><span style="color:#444444;">Key</span><span style="color:#444444;">的所有消息都路由到同一个</span><span style="color:#444444;">Partition</span><span style="color:#444444;">上去。</span> <span style="color:#444444;">同时</span><span style="color:#444444;">Leader</span><span style="color:#444444;">也可以在</span><span style="color:#444444;">In-Sync</span><span style="color:#444444;">的</span><span style="color:#444444;">Replica</span><span style="color:#444444;">中迁移。由于针对某一个</span><span style="color:#444444;">Partition</span><span style="color:#444444;">的所有读写请求都是只由</span><span style="color:#444444;">Leader</span><span style="color:#444444;">来处理，所以</span><span style="color:#444444;">Kafka</span><span style="color:#444444;">会尽量把</span><span style="color:#444444;">Leader</span><span style="color:#444444;">均匀的分散到集群的各个节点上，以免造成网络流量过于集中。</span></p>

<p style="margin-left:0cm;"><strong><u><span style="color:#444444;">2</span></u></strong><strong><u><span style="color:#444444;">）并发方面：</span></u></strong><span style="color:#444444;">任意</span><span style="color:#444444;">Partition</span><span style="color:#444444;">在某一个时刻只能被一个</span><span style="color:#444444;">Consumer Group</span><span style="color:#444444;">内的一个</span><span style="color:#444444;">Consumer</span><span style="color:#444444;">消费</span><span style="color:#444444;">(</span><span style="color:#444444;">反过来一个</span><span style="color:#444444;">Consumer</span><span style="color:#444444;">则可以同时消费多个</span><span style="color:#444444;">Partition)</span><span style="color:#444444;">，</span><span style="color:#444444;">Kafka</span><span style="color:#444444;">非常简洁的</span><span style="color:#444444;">Offset</span><span style="color:#444444;">机制最小化了</span><span style="color:#444444;">Broker</span><span style="color:#444444;">和</span><span style="color:#444444;">Consumer</span><span style="color:#444444;">之间的交互，这使</span><span style="color:#444444;">Kafka</span><span style="color:#444444;">并不会像同类其他消息队列一样，随着下游</span><span style="color:#444444;">Consumer</span><span style="color:#444444;">数目的增加而成比例的降低性能。此外，如果多个</span><span style="color:#444444;">Consumer</span><span style="color:#444444;">恰巧都是消费时间序上很相近的数据，可以达到很高的</span><span style="color:#444444;">PageCache</span><span style="color:#444444;">命中率，因而</span><span style="color:#444444;">Kafka</span><span style="color:#444444;">可以非常高效的支持高并发读操作，实践中基本可以达到单机网卡上限。</span></p>

<p style="margin-left:0cm;"><span style="color:#444444;">不过，</span><span style="color:#444444;">Partition</span><span style="color:#444444;">的数量并不是越多越好，</span><span style="color:#444444;">Partition</span><span style="color:#444444;">的数量越多，平均到每一个</span><span style="color:#444444;">Broker</span><span style="color:#444444;">上的数量也就越多。考虑到</span><span style="color:#444444;">Broker</span><span style="color:#444444;">宕机</span><span style="color:#444444;">(Network Failure, Full GC)</span><span style="color:#444444;">的情况下，需要由</span><span style="color:#444444;">Controller</span><span style="color:#444444;">来为所有宕机的</span><span style="color:#444444;">Broker</span><span style="color:#444444;">上的所有</span><span style="color:#444444;">Partition</span><span style="color:#444444;">重新选举</span><span style="color:#444444;">Leader</span><span style="color:#444444;">，假设每个</span><span style="color:#444444;">Partition</span><span style="color:#444444;">的选举消耗</span><span style="color:#444444;">10ms</span><span style="color:#444444;">，如果</span><span style="color:#444444;">Broker</span><span style="color:#444444;">上有</span><span style="color:#444444;">500</span><span style="color:#444444;">个</span><span style="color:#444444;">Partition</span><span style="color:#444444;">，那么在进行选举的</span><span style="color:#444444;">5s</span><span style="color:#444444;">的时间里，对上述</span><span style="color:#444444;">Partition</span><span style="color:#444444;">的读写操作都会触发</span><span style="color:#444444;">LeaderNotAvailableException</span><span style="color:#444444;">。</span></p>

<p style="margin-left:0cm;"><span style="color:#444444;">再进一步，如果挂掉的</span><span style="color:#444444;">Broker</span><span style="color:#444444;">是整个集群的</span><span style="color:#444444;">Controller</span><span style="color:#444444;">，那么首先要进行的是重新任命一个</span><span style="color:#444444;">Broker</span><span style="color:#444444;">作为</span><span style="color:#444444;">Controller</span><span style="color:#444444;">。新任命的</span><span style="color:#444444;">Controller</span><span style="color:#444444;">要从</span><span style="color:#444444;">Zookeeper</span><span style="color:#444444;">上获取所有</span><span style="color:#444444;">Partition</span><span style="color:#444444;">的</span><span style="color:#444444;">Meta</span><span style="color:#444444;">信息，获取每个信息大概</span><span style="color:#444444;">3-5ms</span><span style="color:#444444;">，那么如果有</span><span style="color:#444444;">10000</span><span style="color:#444444;">个</span><span style="color:#444444;">Partition</span><span style="color:#444444;">这个时间就会达到</span><span style="color:#444444;">30s-50s</span><span style="color:#444444;">。而且不要忘记这只是重新启动一个</span><span style="color:#444444;">Controller</span><span style="color:#444444;">花费的时间，在这基础上还要再加上前面说的选举</span><span style="color:#444444;">Leader</span><span style="color:#444444;">的时间</span><span style="color:#444444;"> -_-!!!!!!</span></p>

<p style="margin-left:0cm;"><span style="color:#444444;">此外，在</span><span style="color:#444444;">Broker</span><span style="color:#444444;">端，对</span><span style="color:#444444;">Producer</span><span style="color:#444444;">和</span><span style="color:#444444;">Consumer</span><span style="color:#444444;">都使用了</span><span style="color:#444444;">Buffer</span><span style="color:#444444;">机制。其中</span><span style="color:#444444;">Buffer</span><span style="color:#444444;">的大小是统一配置的，数量则与</span><span style="color:#444444;">Partition</span><span style="color:#444444;">个数相同。如果</span><span style="color:#444444;">Partition</span><span style="color:#444444;">个数过多，会导致</span><span style="color:#444444;">Producer</span><span style="color:#444444;">和</span><span style="color:#444444;">Consumer</span><span style="color:#444444;">的</span><span style="color:#444444;">Buffer</span><span style="color:#444444;">内存占用过大。</span></p>

<p style="margin-left:0cm;"><strong><em><u><span style="color:#444444;">Tips</span></u></em></strong></p>

<ol><li><span style="color:#444444;">Partition</span><span style="color:#444444;">的数量尽量提前预分配，虽然可以在后期动态增加</span><span style="color:#444444;">Partition</span><span style="color:#444444;">，但是会冒着可能破坏</span><span style="color:#444444;">Message Key</span><span style="color:#444444;">和</span><span style="color:#444444;">Partition</span><span style="color:#444444;">之间对应关系的风险。</span></li>
	<li><span style="color:#444444;">Replica</span><span style="color:#444444;">的数量不要过多，如果条件允许尽量把</span><span style="color:#444444;">Replica</span><span style="color:#444444;">集合内的</span><span style="color:#444444;">Partition</span><span style="color:#444444;">分别调整到不同的</span><span style="color:#444444;">Rack</span><span style="color:#444444;">。</span></li>
	<li><span style="color:#444444;">尽一切努力保证每次停</span><span style="color:#444444;">Broker</span><span style="color:#444444;">时都可以</span><span style="color:#444444;">Clean Shutdown</span><span style="color:#444444;">，否则问题就不仅仅是恢复服务所需时间长，还可能出现数据损坏或其他很诡异的问题。</span></li>
</ol><p style="margin-left:0cm;"><strong><em><u><span style="color:#444444;">Producer</span></u></em></strong></p>

<p style="margin-left:0cm;"><span style="color:#444444;">Kafka</span><span style="color:#444444;">的研发团队表示在</span><span style="color:#444444;">0.8</span><span style="color:#444444;">版本里用</span><span style="color:#444444;">Java</span><span style="color:#444444;">重写了整个</span><span style="color:#444444;">Producer</span><span style="color:#444444;">，据说性能有了很大提升。我还没有亲自对比试用过，这里就不做数据对比了。本文结尾的扩展阅读里提到了一套我认为比较好的对照组，有兴趣的同学可以尝试一下。</span></p>

<p style="margin-left:0cm;"><span style="color:#444444;">其实在</span><span style="color:#444444;">Producer</span><span style="color:#444444;">端的优化大部分消息系统采取的方式都比较单一，无非也就化零为整、同步变异步这么几种。</span></p>

<p style="margin-left:0cm;"><span style="color:#444444;">Kafka</span><span style="color:#444444;">系统默认支持</span><span style="color:#444444;">MessageSet</span><span style="color:#444444;">，把多条</span><span style="color:#444444;">Message</span><span style="color:#444444;">自动地打成一个</span><span style="color:#444444;">Group</span><span style="color:#444444;">后发送出去，均摊后拉低了每次通信的</span><span style="color:#444444;">RTT</span><span style="color:#444444;">。而且在组织</span><span style="color:#444444;">MessageSet</span><span style="color:#444444;">的同时，还可以把数据重新排序，从爆发流式的随机写入优化成较为平稳的线性写入。</span></p>

<p style="margin-left:0cm;"><span style="color:#444444;">此外，还要着重介绍的一点是，</span><span style="color:#444444;">Producer</span><span style="color:#444444;">支持</span><span style="color:#444444;">End-to-End</span><span style="color:#444444;">的压缩。数据在本地压缩后放到网络上传输，在</span><span style="color:#444444;">Broker</span><span style="color:#444444;">一般不解压</span><span style="color:#444444;">(</span><span style="color:#444444;">除非指定要</span><span style="color:#444444;">Deep-Iteration)</span><span style="color:#444444;">，直至消息被</span><span style="color:#444444;">Consume</span><span style="color:#444444;">之后在客户端解压。</span></p>

<p style="margin-left:0cm;"><span style="color:#444444;">当然用户也可以选择自己在应用层上做压缩和解压的工作</span><span style="color:#444444;">(</span><span style="color:#444444;">毕竟</span><span style="color:#444444;">Kafka</span><span style="color:#444444;">目前支持的压缩算法有限，只有</span><span style="color:#444444;">GZIP</span><span style="color:#444444;">和</span><span style="color:#444444;">Snappy)</span><span style="color:#444444;">，不过这样做反而会意外的降低效率！！！！</span><span style="color:#444444;"> Kafka</span><span style="color:#444444;">的</span><span style="color:#444444;">End-to-End</span><span style="color:#444444;">压缩与</span><span style="color:#444444;">MessageSet</span><span style="color:#444444;">配合在一起工作效果最佳，上面的做法直接割裂了两者间联系。至于道理其实很简单，压缩算法中一条基本的原理“重复的数据量越多，压缩比越高”。无关于消息体的内容，无关于消息体的数量，大多数情况下输入数据量大一些会取得更好的压缩比。</span></p>

<p style="margin-left:0cm;"><span style="color:#444444;">不过</span><span style="color:#444444;">Kafka</span><span style="color:#444444;">采用</span><span style="color:#444444;">MessageSet</span><span style="color:#444444;">也导致在可用性上一定程度的妥协。每次发送数据时，</span><span style="color:#444444;">Producer</span><span style="color:#444444;">都是</span><span style="color:#444444;">send()</span><span style="color:#444444;">之后就认为已经发送出去了，但其实大多数情况下消息还在内存的</span><span style="color:#444444;">MessageSet</span><span style="color:#444444;">当中，尚未发送到网络，这时候如果</span><span style="color:#444444;">Producer</span><span style="color:#444444;">挂掉，那就会出现丢数据的情况。</span></p>

<p style="margin-left:0cm;"><span style="color:#444444;">为了解决这个问题，</span><span style="color:#444444;">Kafka</span><span style="color:#444444;">在</span><span style="color:#444444;">0.8</span><span style="color:#444444;">版本的设计借鉴了网络当中的</span><span style="color:#444444;">ack</span><span style="color:#444444;">机制。如果对性能要求较高，又能在一定程度上允许</span><span style="color:#444444;">Message</span><span style="color:#444444;">的丢失，那就可以设置</span><span style="color:#444444;">request.required.acks=0 </span><span style="color:#444444;">来关闭</span><span style="color:#444444;">ack</span><span style="color:#444444;">，以全速发送。如果需要对发送的消息进行确认，就需要设置</span><span style="color:#444444;">request.required.acks</span><span style="color:#444444;">为</span><span style="color:#444444;">1</span><span style="color:#444444;">或</span><span style="color:#444444;">-1</span><span style="color:#444444;">，那么</span><span style="color:#444444;">1</span><span style="color:#444444;">和</span><span style="color:#444444;">-1</span><span style="color:#444444;">又有什么区别呢？这里又要提到前面聊的有关</span><span style="color:#444444;">Replica</span><span style="color:#444444;">数量问题。如果配置为</span><span style="color:#444444;">1</span><span style="color:#444444;">，表示消息只需要被</span><span style="color:#444444;">Leader</span><span style="color:#444444;">接收并确认即可，其他的</span><span style="color:#444444;">Replica</span><span style="color:#444444;">可以进行异步拉取无需立即进行确认，在保证可靠性的同时又不会把效率拉得很低。如果设置为</span><span style="color:#444444;">-1</span><span style="color:#444444;">，表示消息要</span><span style="color:#444444;">Commit</span><span style="color:#444444;">到该</span><span style="color:#444444;">Partition</span><span style="color:#444444;">的</span><span style="color:#444444;">ISR</span><span style="color:#444444;">集合中的所有</span><span style="color:#444444;">Replica</span><span style="color:#444444;">后，才可以返回</span><span style="color:#444444;">ack</span><span style="color:#444444;">，消息的发送会更安全，而整个过程的延迟会随着</span><span style="color:#444444;">Replica</span><span style="color:#444444;">的数量正比增长，这里就需要根据不同的需求做相应的优化。</span></p>

<p style="margin-left:0cm;"><strong><em><u><span style="color:#444444;">Tips</span></u></em></strong></p>

<ol><li><span style="color:#444444;">Producer</span><span style="color:#444444;">的线程不要配置过多，尤其是在</span><span style="color:#444444;">Mirror</span><span style="color:#444444;">或者</span><span style="color:#444444;">Migration</span><span style="color:#444444;">中使用的时候，会加剧目标集群</span><span style="color:#444444;">Partition</span><span style="color:#444444;">消息乱序的情况</span><span style="color:#444444;">(</span><span style="color:#444444;">如果你的应用场景对消息顺序很敏感的话</span><span style="color:#444444;">)</span><span style="color:#444444;">。</span></li>
	<li><span style="color:#444444;">0.8</span><span style="color:#444444;">版本的</span><span style="color:#444444;">request.required.acks</span><span style="color:#444444;">默认是</span><span style="color:#444444;">0(</span><span style="color:#444444;">同</span><span style="color:#444444;">0.7)</span><span style="color:#444444;">。</span></li>
</ol><p style="margin-left:0cm;"><strong><em><u><span style="color:#444444;">Consumer</span></u></em></strong></p>

<p style="margin-left:0cm;"><span style="color:#444444;">Consumer</span><span style="color:#444444;">端的设计大体上还算是比较常规的。</span></p>

<ul><li><span style="color:#444444;">通过</span><span style="color:#444444;">Consumer Group</span><span style="color:#444444;">，可以支持生产者消费者和队列访问两种模式。</span></li>
	<li><span style="color:#444444;">Consumer API</span><span style="color:#444444;">分为</span><span style="color:#444444;">High level</span><span style="color:#444444;">和</span><span style="color:#444444;">Low level</span><span style="color:#444444;">两种。前一种重度依赖</span><span style="color:#444444;">Zookeeper</span><span style="color:#444444;">，所以性能差一些且不自由，但是超省心。第二种不依赖</span><span style="color:#444444;">Zookeeper</span><span style="color:#444444;">服务，无论从自由度和性能上都有更好的表现，但是所有的异常</span><span style="color:#444444;">(Leader</span><span style="color:#444444;">迁移、</span><span style="color:#444444;">Offset</span><span style="color:#444444;">越界、</span><span style="color:#444444;">Broker</span><span style="color:#444444;">宕机等</span><span style="color:#444444;">)</span><span style="color:#444444;">和</span><span style="color:#444444;">Offset</span><span style="color:#444444;">的维护都需要自行处理。</span></li>
	<li><span style="color:#444444;">大家可以关注下不日发布的</span><span style="color:#444444;">0.9 Release</span><span style="color:#444444;">。开发人员又用</span><span style="color:#444444;">Java</span><span style="color:#444444;">重写了一套</span><span style="color:#444444;">Consumer</span><span style="color:#444444;">。把两套</span><span style="color:#444444;">API</span><span style="color:#444444;">合并在一起，同时去掉了对</span><span style="color:#444444;">Zookeeper</span><span style="color:#444444;">的依赖。据说性能有大幅度提升哦</span></li>
</ul><h2 style="margin-left:0cm;"><strong><span style="color:#000000;">13</span><span style="color:#000000;">、</span><span style="color:#000000;">kafka</span><span style="color:#000000;">文件存储机制</span></strong></h2>

<h3 style="margin-left:0cm;"><strong>13.1、Kafka文件存储基本结构</strong></h3>

<p style="margin-left:0cm;">Kafka的存储结构为：</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="183" src="https://img-blog.csdn.net/20180902232252118?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdmFuMTIzNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="755"></p>

<p style="margin-left:0cm;">现阶段这些数据会自动存储在多个服务器上。</p>

<ol><li>在Kafka文件存储中，同一个topic下有多个不同partition，每个partition为一个目录，partiton命名规则为topic名称+有序序号，第一个partiton序号从0开始，序号最大值为partitions数量减1。</li>
	<li>每个partition(目录，partition的数量是指一个服务器上有多少个文件分区)相当于一个巨型文件被平均分配到多个大小相等segment(段)数据文件中。<strong><span style="color:#ff0000;">但每个段</span></strong><strong><span style="color:#ff0000;">segment file</span></strong><strong><span style="color:#ff0000;">消息数量不一定相等</span></strong>，这种特性方便old segment file快速被删除。默认保留7天的数据。</li>
</ol><p style="margin-left:0cm;"><img alt="" class="has" height="375" src="https://img-blog.csdn.net/20180902232254465?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdmFuMTIzNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="774"></p>

<p>每个partiton只需要支持顺序读写就行了，segment文件生命周期由服务端配置参数决定。（什么时候创建，什么时候删除）</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="219" src="https://img-blog.csdn.net/20180902233021165?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdmFuMTIzNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="428"></p>

<p style="margin-left:0cm;">数据有序的讨论？</p>

<p style="margin-left:0cm;">         一个partition的数据是否是有序的？          间隔性有序，不连续</p>

<p style="margin-left:0cm;">         针对一个topic里面的数据，只能做到partition内部有序，不能做到全局有序。</p>

<p style="margin-left:0cm;">         特别加入消费者的场景后，如何保证消费者消费的数据全局有序的？伪命题。</p>

<p style="margin-left:0cm;">只有一种情况下才能保证全局有序？就是只有一个partition。</p>

<h3 style="margin-left:0cm;"><strong>13.2、Kafka Partition <span style="color:#000000;">Segment</span></strong></h3>

<p><span style="color:#000000;">Segment</span><span style="color:#000000;"> file</span><span style="color:#000000;">组成：由2大部分组成，分别为index file和data file，此2个文件一一对应，成对出现，后缀".index"和“.log”分别表示为segment索引文件、数据文件。</span></p>

<p style="margin-left:21pt;"><img alt="" class="has" height="205" src="https://img-blog.csdn.net/2018090223225497?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdmFuMTIzNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="326"><span style="color:#000000;"> </span></p>

<p><span style="color:#000000;">Segment</span><span style="color:#000000;">文件命名规则：</span><span style="color:#000000;">partion</span><span style="color:#000000;">全局的第一个</span><span style="color:#000000;">segment</span><span style="color:#000000;">从</span><span style="color:#000000;">0</span><span style="color:#000000;">开始，后续每个</span><span style="color:#000000;">segment</span><span style="color:#000000;">文件名为上一个</span><span style="color:#000000;">segment</span><span style="color:#000000;">文件最后一条消息的</span><span style="color:#000000;">offset</span><span style="color:#000000;">值。数值最大为</span><span style="color:#000000;">64</span><span style="color:#000000;">位</span><span style="color:#000000;">long</span><span style="color:#000000;">大小，</span><span style="color:#000000;">19</span><span style="color:#000000;">位数字字符长度，没有数字用</span><span style="color:#000000;">0</span><span style="color:#000000;">填充。</span></p>

<p><span style="color:#000000;">索引文件存储大量元数据，数据文件存储大量消息，索引文件中元数据指向对应数据文件中</span><span style="color:#000000;">message</span><span style="color:#000000;">的物理偏移地址。</span></p>

<p style="margin-left:21pt;"><img alt="" class="has" height="398" src="https://img-blog.csdn.net/20180902232254252?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdmFuMTIzNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="661"><span style="color:#3e3e3e;"> </span></p>

<ol><li><span style="color:#000000;">3</span><span style="color:#000000;">，</span><span style="color:#000000;">497</span><span style="color:#000000;">：当前</span><span style="color:#000000;">log</span><span style="color:#000000;">文件中的第几条信息，存放在磁盘上的那个地方</span></li>
	<li><span style="color:#000000;">上述图中索引文件存储大量元数据，数据文件存储大量消息，索引文件中元数据指向对应数据文件中</span><span style="color:#000000;">message</span><span style="color:#000000;">的物理偏移地址。</span></li>
	<li><span style="color:#000000;">其中以索引文件中元数据</span><span style="color:#000000;">3,497</span><span style="color:#000000;">为例，依次在数据文件中表示第</span><span style="color:#000000;">3</span><span style="color:#000000;">个</span><span style="color:#000000;">message(</span><span style="color:#000000;">在全局</span><span style="color:#000000;">partiton</span><span style="color:#000000;">表示第</span><span style="color:#000000;">368772</span><span style="color:#000000;">个</span><span style="color:#000000;">message)</span><span style="color:#000000;">、以及该消息的物理偏移地址为</span><span style="color:#000000;">497</span><span style="color:#000000;">。</span></li>
	<li><span style="color:#000000;">segment data file</span><span style="color:#000000;">由许多</span><span style="color:#000000;">message</span><span style="color:#000000;">组成，</span><span style="color:#000000;"> qq物理结构如下：</span></li>
</ol><h3 style="margin-left:0cm;"><img alt="" class="has" height="467" src="https://img-blog.csdn.net/20180902233210434?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdmFuMTIzNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="687"></h3>

<h3 style="margin-left:0cm;"><strong>13.3、Kafka <span style="color:#000000;">查找</span><span style="color:#000000;">message</span></strong></h3>

<p>读取offset=368776的message，需要通过下面2个步骤查找。</p>

<p style="margin-left:0cm;">         <img alt="" class="has" height="205" src="https://img-blog.csdn.net/20180902232253965?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdmFuMTIzNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="326"></p>

<p><strong>13.3.1、查找segment file</strong></p>

<p style="margin-left:0cm;">00000000000000000000.index表示最开始的文件，起始偏移量(offset)为0</p>

<p style="margin-left:0cm;">00000000000000368769.index的消息量起始偏移量为368770 = 368769 + 1</p>

<p style="margin-left:0cm;">00000000000000737337.index的起始偏移量为737338=737337 + 1</p>

<p style="margin-left:0cm;">其他后续文件依次类推。</p>

<p style="margin-left:0cm;">以起始偏移量命名并排序这些文件，只要根据offset **二分查找**文件列表，就可以快速定位到具体文件。当offset=368776时定位到00000000000000368769.index和对应log文件。</p>

<p><strong>13.3.2、通过segment file查找message </strong></p>

<p style="margin-left:0cm;">当offset=368776时，依次定位到00000000000000368769.index的元数据物理位置和00000000000000368769.log的物理偏移地址</p>

<p style="margin-left:0cm;">然后再通过00000000000000368769.log顺序查找直到offset=368776为止。</p>

<h2 style="margin-left:0cm;"><strong><span style="color:#000000;">14</span><span style="color:#000000;">、</span><span style="color:#000000;">Kafka</span><span style="color:#000000;">自定义</span><span style="color:#000000;">Partition</span></strong></h2>

<p style="margin-left:0cm;"><strong> <code>package cn.itcast;</code></strong></p>

<p style="margin-left:0cm;"><strong><code>import kafka.javaapi.producer.Producer;</code></strong></p>

<p><strong><code>import kafka.producer.KeyedMessage;</code></strong></p>

<p><strong><code>import kafka.producer.ProducerConfig;</code></strong></p>

<p><strong><code>import java.util.Properties;</code></strong></p>

<p><strong><code>public class MyKafkaProducer {</code></strong></p>

<p><strong><code>  public static void main(String[] args) {</code></strong></p>

<p><strong><code>    Properties properties = new Properties();</code></strong></p>

<p><strong><code>    properties.put("metadata.broker.list","mini1:9092");</code></strong></p>

<p><strong><code>    // 默认的序列化为byte改为string</code></strong></p>

<p><strong><code>    properties.put("serializer.class","kafka.serializer.StringEncoder");</code></strong></p>

<p><strong><code>   /**</code></strong></p>

<p><strong><code>   * 自定义parition的基本步骤</code></strong></p>

<p><strong><code>   * 1、实现partition类</code></strong></p>

<p><strong><code>   * 2、加一个构造器，MyPartitioner(VerifiableProperties properties)</code></strong></p>

<p><strong><code>   * 3、将自定义的parititoner加入到properties中</code></strong></p>

<p><strong><code>   * properties.put("partitioner.class","cn.itcast.MyPartitioner")</code></strong></p>

<p><strong><code>   * 4、producer.send方法中必须指定一个paritionKey</code></strong></p>

<p><strong><code>   */</code></strong></p>

<p><strong><code>   properties.put("partitioner.class","cn.itcast.MyPartitioner");</code></strong></p>

<p><strong><code>   Producer producer = new Producer(new ProducerConfig(properties));</code></strong></p>

<p><strong><code>   while (true){</code></strong></p>

<p><strong><code>      producer.send(new KeyedMessage("order4","zhang","我爱我的祖国"));</code></strong></p>

<p><strong><code>      // producer.send(new KeyedMessage("order","我爱我的祖国"));</code></strong></p>

<p><strong><code>   }</code></strong></p>

<p><strong><code>  }</code></strong></p>

<p><strong><code>}</code></strong></p>

<p><strong><code>package cn.itcast;</code></strong></p>

<p><strong><code>import kafka.producer.Partitioner;</code></strong></p>

<p><strong><code>import kafka.utils.VerifiableProperties;</code></strong></p>

<p><strong><code>/**</code></strong></p>

<p><strong><code>* http://www.cnblogs.com/wxd0108/p/6519973.html</code></strong></p>

<p><strong><code>*/</code></strong></p>

<p><strong><code>public class MyPartitioner implements Partitioner {</code></strong></p>

<p><strong><code>  public MyPartitioner(VerifiableProperties properties) {</code></strong></p>

<p><strong><code> }</code></strong></p>

<p><strong><code>  public int partition(Object key, int numPartitions) {</code></strong></p>

<p><strong><code>  return 2;</code></strong></p>

<p><strong><code> }</code></strong></p>

<p><strong><code>}</code></strong></p>            </div>
                </div>