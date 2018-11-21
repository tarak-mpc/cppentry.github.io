---
layout:     post
title:      Kafka负载均衡、Kafka自定义Partition、Kafk文件存储机制
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>1、Kafka整体结构图 <br>
Kafka名词解释和工作方式</p>

<p>   Producer ：消息生产者，就是向kafka broker发消息的客户端。 <br>
   Consumer ：消息消费者，向kafka broker取消息的客户端 <br>
   Topic ：咋们可以理解为一个队列。 <br>
   Consumer Group （CG）：这是kafka用来实现一个topic消息的广播（发给所有的consumer）和单播（发给任意一个consumer）的手段。一个topic可以有多个CG。topic的消息会复制（不是真的复制，是概念上的）到所有的CG，但每个partion只会把消息发给该CG中的一个consumer。如果需要实现广播，只要每个consumer有一个独立的CG就可以了。要实现单播只要所有的consumer在同一个CG。用CG还可以将consumer进行自由的分组而不需要多次发送消息到不同的topic。 <br>
   Broker ：一台kafka服务器就是一个broker。一个集群由多个broker组成。一个broker可以容纳多个topic。 <br>
   Partition：为了实现扩展性，一个非常大的topic可以分布到多个broker（即服务器）上，一个topic可以分为多个partition，每个partition是一个有序的队列。partition中的每条消息都会被分配一个有序的id（offset）。kafka只保证按一个partition中的顺序将消息发给consumer，不保证一个topic的整体（多个partition间）的顺序。 <br>
   Offset：kafka的存储文件都是按照offset.kafka来命名，用offset做名字的好处是方便查找。例如你想找位于2049的位置，只要找到2048.kafka的文件即可。当然the first offset就是00000000000.kafka <br>
2、Consumer与topic关系 <br>
本质上kafka只支持Topic； <br>
   每个group中可以有多个consumer，每个consumer属于一个consumer group； <br>
通常情况下，一个group中会包含多个consumer，这样不仅可以提高topic中消息的并发消费能力，而且还能提高”故障容错”性，如果group中的某个consumer失效那么其消费的partitions将会有其他consumer自动接管。 <br>
   对于Topic中的一条特定的消息，只会被订阅此Topic的每个group中的其中一个consumer消费，此消息不会发送给一个group的多个consumer； <br>
那么一个group中所有的consumer将会交错的消费整个Topic，每个group中consumer消息消费互相独立，我们可以认为一个group是一个”订阅”者。 <br>
   在kafka中,一个partition中的消息只会被group中的一个consumer消费(同一时刻)； <br>
一个Topic中的每个partions，只会被一个”订阅者”中的一个consumer消费，不过一个consumer可以同时消费多个partitions中的消息。 <br>
   kafka的设计原理决定,对于一个topic，同一个group中不能有多于partitions个数的consumer同时消费，否则将意味着某些consumer将无法得到消息。 <br>
kafka只能保证一个partition中的消息被某个consumer消费时是顺序的；事实上，从Topic角度来说,当有多个partitions时,消息仍不是全局有序的。 <br>
3、Kafka消息的分发 <br>
    Producer客户端负责消息的分发 <br>
   kafka集群中的任何一个broker都可以向producer提供metadata信息,这些metadata中包含”集群中存活的servers列表”/”partitions leader列表”等信息； <br>
   当producer获取到metadata信息之后, producer将会和Topic下所有partition leader保持socket连接； <br>
   消息由producer直接通过socket发送到broker，中间不会经过任何”路由层”，事实上，消息被路由到哪个partition上由producer客户端决定； <br>
比如可以采用”random”“key-hash”“轮询”等,如果一个topic中有多个partitions,那么在producer端实现”消息均衡分发”是必要的。 <br>
   在producer端的配置文件中,开发者可以指定partition路由的方式。</p>

<p>Producer消息发送的应答机制 <br>
设置发送数据是否需要服务端的反馈,有三个值0,1,-1 <br>
0: producer不会等待broker发送ack  <br>
1: 当leader接收到消息之后发送ack  <br>
-1: 当所有的follower都同步消息成功后发送ack <br>
    request.required.acks=0 <br>
4、Consumer的负载均衡 <br>
当一个group中,有consumer加入或者离开时,会触发partitions均衡.均衡的最终目的,是提升topic的并发消费能力，步骤如下： <br>
1、  假如topic1,具有如下partitions: P0,P1,P2,P3 <br>
2、  加入group中,有如下consumer: C1,C2 <br>
3、  首先根据partition索引号对partitions排序: P0,P1,P2,P3 <br>
4、  根据consumer.id排序: C0,C1 <br>
5、  计算倍数: M = [P0,P1,P2,P3].size / [C0,C1].size,本例值M=2(向上取整) <br>
6、  然后依次分配partitions: C0 = [P0,P1],C1=[P2,P3],即Ci = [P(i * M),P((i + 1) * M -1)]</p>

<p>5、kafka文件存储机制 <br>
5.1、Kafka文件存储基本结构 <br>
   在Kafka文件存储中，同一个topic下有多个不同partition，每个partition为一个目录，partiton命名规则为topic名称+有序序号，第一个partiton序号从0开始，序号最大值为partitions数量减1。 <br>
   每个partion(目录)相当于一个巨型文件被平均分配到多个大小相等segment(段)数据文件中。但每个段segment file消息数量不一定相等，这种特性方便old segment file快速被删除。默认保留7天的数据。</p>

<p>   每个partiton只需要支持顺序读写就行了，segment文件生命周期由服务端配置参数决定。（什么时候创建，什么时候删除）</p>

<p>数据有序的讨论？ <br>
    一个partition的数据是否是有序的？   间隔性有序，不连续 <br>
    针对一个topic里面的数据，只能做到partition内部有序，不能做到全局有序。 <br>
    特别加入消费者的场景后，如何保证消费者消费的数据全局有序的？伪命题。</p>

<p>只有一种情况下才能保证全局有序？就是只有一个partition。 <br>
5.2、Kafka Partition Segment <br>
   Segment file组成：由2大部分组成，分别为index file和data file，此2个文件一一对应，成对出现，后缀”.index”和“.log”分别表示为segment索引文件、数据文件。</p>

<p>   Segment文件命名规则：partion全局的第一个segment从0开始，后续每个segment文件名为上一个segment文件最后一条消息的offset值。数值最大为64位long大小，19位数字字符长度，没有数字用0填充。 <br>
   索引文件存储大量元数据，数据文件存储大量消息，索引文件中元数据指向对应数据文件中message的物理偏移地址。</p>

<p>3，497：当前log文件中的第几条信息，存放在磁盘上的那个地方</p>

<p>上述图中索引文件存储大量元数据，数据文件存储大量消息，索引文件中元数据指向对应数据文件中message的物理偏移地址。 <br>
其中以索引文件中元数据3,497为例，依次在数据文件中表示第3个message(在全局partiton表示第368772个message)、以及该消息的物理偏移地址为497。</p>

<p>   segment data file由许多message组成， qq物理结构如下： <br>
关键字 解释说明 <br>
8 byte offset   在parition(分区)内的每条消息都有一个有序的id号，这个id号被称为偏移(offset),它可以唯一确定每条消息在parition(分区)内的位置。即offset表示partiion的第多少message <br>
4 byte message size message大小 <br>
4 byte CRC32    用crc32校验message <br>
1 byte “magic”  表示本次发布Kafka服务程序协议版本号 <br>
1 byte “attributes” 表示为独立版本、或标识压缩类型、或编码类型。 <br>
4 byte key length   表示key的长度,当key为-1时，K byte key字段不填 <br>
K byte key  可选 <br>
value bytes payload 表示实际消息数据。</p>

<p>5.3、Kafka 查找message <br>
读取offset=368776的message，需要通过下面2个步骤查找。</p>

<p>5.3.1、查找segment file <br>
00000000000000000000.index表示最开始的文件，起始偏移量(offset)为0 <br>
00000000000000368769.index的消息量起始偏移量为368770 = 368769 + 1 <br>
00000000000000737337.index的起始偏移量为737338=737337 + 1 <br>
其他后续文件依次类推。 <br>
以起始偏移量命名并排序这些文件，只要根据offset <strong>二分查找</strong>文件列表，就可以快速定位到具体文件。当offset=368776时定位到00000000000000368769.index和对应log文件。 <br>
5.3.2、通过segment file查找message <br>
当offset=368776时，依次定位到00000000000000368769.index的元数据物理位置和00000000000000368769.log的物理偏移地址 <br>
然后再通过00000000000000368769.log顺序查找直到offset=368776为止。 <br>
6、Kafka自定义Partition <br>
    见代码</p>

<p>关于消息队列的使用—-ActiveMQ，RabbitMQ，ZeroMQ，Kafka，MetaMQ，RocketMQ <br>
一、消息队列概述 <br>
消息队列中间件是分布式系统中重要的组件，主要解决应用解耦，异步消息，流量削锋等问题，实现高性能，高可用，可伸缩和最终一致性架构。目前使用较多的消息队列有ActiveMQ，RabbitMQ，ZeroMQ，Kafka，MetaMQ，RocketMQ <br>
二、消息队列应用场景 <br>
以下介绍消息队列在实际应用中常用的使用场景。异步处理，应用解耦，流量削锋和消息通讯四个场景。 <br>
2.1异步处理 <br>
场景说明：用户注册后，需要发注册邮件和注册短信。传统的做法有两种 1.串行的方式；2.并行方式 <br>
a、串行方式：将注册信息写入数据库成功后，发送注册邮件，再发送注册短信。以上三个任务全部完成后，返回给客户端。</p>

<p>b、并行方式：将注册信息写入数据库成功后，发送注册邮件的同时，发送注册短信。以上三个任务完成后，返回给客户端。与串行的差别是，并行的方式可以提高处理的时间</p>

<p>假设三个业务节点每个使用50毫秒钟，不考虑网络等其他开销，则串行方式的时间是150毫秒，并行的时间可能是100毫秒。 <br>
因为CPU在单位时间内处理的请求数是一定的，假设CPU1秒内吞吐量是100次。则串行方式1秒内CPU可处理的请求量是7次（1000/150）。并行方式处理的请求量是10次（1000/100） <br>
小结：如以上案例描述，传统的方式系统的性能（并发量，吞吐量，响应时间）会有瓶颈。如何解决这个问题呢？ <br>
引入消息队列，将不是必须的业务逻辑，异步处理。改造后的架构如下：</p>

<p>按照以上约定，用户的响应时间相当于是注册信息写入数据库的时间，也就是50毫秒。注册邮件，发送短信写入消息队列后，直接返回，因此写入消息队列的速度很快，基本可以忽略，因此用户的响应时间可能是50毫秒。因此架构改变后，系统的吞吐量提高到每秒20 QPS。比串行提高了3倍，比并行提高了两倍。 <br>
2.2应用解耦 <br>
场景说明：用户下单后，订单系统需要通知库存系统。传统的做法是，订单系统调用库存系统的接口。如下图：</p>

<p>传统模式的缺点：假如库存系统无法访问，则订单减库存将失败，从而导致订单失败，订单系统与库存系统耦合 <br>
如何解决以上问题呢？引入应用消息队列后的方案，如下图：</p>

<p>订单系统：用户下单后，订单系统完成持久化处理，将消息写入消息队列，返回用户订单下单成功 <br>
库存系统：订阅下单的消息，采用拉/推的方式，获取下单信息，库存系统根据下单信息，进行库存操作 <br>
假如：在下单时库存系统不能正常使用。也不影响正常下单，因为下单后，订单系统写入消息队列就不再关心其他的后续操作了。实现订单系统与库存系统的应用解耦 <br>
2.3流量削锋 <br>
流量削锋也是消息队列中的常用场景，一般在秒杀或团抢活动中使用广泛。 <br>
应用场景：秒杀活动，一般会因为流量过大，导致流量暴增，应用挂掉。为解决这个问题，一般需要在应用前端加入消息队列。 <br>
a、可以控制活动的人数 <br>
b、可以缓解短时间内高流量压垮应用</p>

<p>用户的请求，服务器接收后，首先写入消息队列。假如消息队列长度超过最大数量，则直接抛弃用户请求或跳转到错误页面。 <br>
秒杀业务根据消息队列中的请求信息，再做后续处理 <br>
2.4日志处理 <br>
日志处理是指将消息队列用在日志处理中，比如Kafka的应用，解决大量日志传输的问题。架构简化如下</p>

<p>日志采集客户端，负责日志数据采集，定时写受写入Kafka队列 <br>
Kafka消息队列，负责日志数据的接收，存储和转发 <br>
日志处理应用：订阅并消费kafka队列中的日志数据  <br>
2.5消息通讯 <br>
消息通讯是指，消息队列一般都内置了高效的通信机制，因此也可以用在纯的消息通讯。比如实现点对点消息队列，或者聊天室等 <br>
点对点通讯：</p>

<p>客户端A和客户端B使用同一队列，进行消息通讯。 <br>
聊天室通讯：</p>

<p>客户端A，客户端B，客户端N订阅同一主题，进行消息发布和接收。实现类似聊天室效果。 <br>
以上实际是消息队列的两种消息模式，点对点或发布订阅模式。模型为示意图，供参考。 <br>
三、消息中间件示例  <br>
3.1电商系统</p>

<p>消息队列采用高可用，可持久化的消息中间件。比如Active MQ，Rabbit MQ，Rocket Mq。 <br>
（1）应用将主干逻辑处理完成后，写入消息队列。消息发送是否成功可以开启消息的确认模式。（消息队列返回消息接收成功状态后，应用再返回，这样保障消息的完整性） <br>
（2）扩展流程（发短信，配送处理）订阅队列消息。采用推或拉的方式获取消息并处理。 <br>
（3）消息将应用解耦的同时，带来了数据一致性问题，可以采用最终一致性方式解决。比如主数据写入数据库，扩展应用根据消息队列，并结合数据库方式实现基于消息队列的后续处理。 <br>
3.2日志收集系统</p>

<p>分为Zookeeper注册中心，日志收集客户端，Kafka集群和Storm集群（OtherApp）四部分组成。 <br>
Zookeeper注册中心，提出负载均衡和地址查找服务 <br>
日志收集客户端，用于采集应用系统的日志，并将数据推送到kafka队列 <br>
Kafka集群：接收，路由，存储，转发等消息处理 <br>
Storm集群：与OtherApp处于同一级别，采用拉的方式消费队列中的数据 <br>
四、JMS消息服务 <br>
讲消息队列就不得不提JMS 。JMS（Java Message Service，Java消息服务）API是一个消息服务的标准/规范，允许应用程序组件基于JavaEE平台创建、发送、接收和读取消息。它使分布式通信耦合度更低，消息服务更加可靠以及异步性。 <br>
在EJB架构中，有消息bean可以无缝的与JM消息服务集成。在J2EE架构模式中，有消息服务者模式，用于实现消息与应用直接的解耦。 <br>
4.1消息模型 <br>
在JMS标准中，有两种消息模型P2P（Point to Point）,Publish/Subscribe(Pub/Sub)。 <br>
4.1.1 P2P模式</p>

<p>P2P模式包含三个角色：消息队列（Queue），发送者(Sender)，接收者(Receiver)。每个消息都被发送到一个特定的队列，接收者从队列中获取消息。队列保留着消息，直到他们被消费或超时。 <br>
P2P的特点 <br>
每个消息只有一个消费者（Consumer）(即一旦被消费，消息就不再在消息队列中) <br>
发送者和接收者之间在时间上没有依赖性，也就是说当发送者发送了消息之后，不管接收者有没有正在运行，它不会影响到消息被发送到队列 <br>
接收者在成功接收消息之后需向队列应答成功  <br>
如果希望发送的每个消息都会被成功处理的话，那么需要P2P模式。 <br>
4.1.2 Pub/Sub模式</p>

<p>包含三个角色主题（Topic），发布者（Publisher），订阅者（Subscriber） 多个发布者将消息发送到Topic，系统将这些消息传递给多个订阅者。 <br>
Pub/Sub的特点 <br>
每个消息可以有多个消费者 <br>
发布者和订阅者之间有时间上的依赖性。针对某个主题（Topic）的订阅者，它必须创建一个订阅者之后，才能消费发布者的消息 <br>
为了消费消息，订阅者必须保持运行的状态 <br>
为了缓和这样严格的时间相关性，JMS允许订阅者创建一个可持久化的订阅。这样，即使订阅者没有被激活（运行），它也能接收到发布者的消息。 <br>
如果希望发送的消息可以不被做任何处理、或者只被一个消息者处理、或者可以被多个消费者处理的话，那么可以采用Pub/Sub模型。 <br>
4.2消息消费 <br>
在JMS中，消息的产生和消费都是异步的。对于消费来说，JMS的消息者可以通过两种方式来消费消息。 <br>
（1）同步 <br>
订阅者或接收者通过receive方法来接收消息，receive方法在接收到消息之前（或超时之前）将一直阻塞； <br>
（2）异步 <br>
订阅者或接收者可以注册为一个消息监听器。当消息到达之后，系统自动调用监听器的onMessage方法。 <br>
JNDI：Java命名和目录接口,是一种标准的Java命名系统接口。可以在网络上查找和访问服务。通过指定一个资源名称，该名称对应于数据库或命名服务中的一个记录，同时返回资源连接建立所必须的信息。 <br>
JNDI在JMS中起到查找和访问发送目标或消息来源的作用。 <br>
五、常用消息队列 <br>
一般商用的容器，比如WebLogic，JBoss，都支持JMS标准，开发上很方便。但免费的比如Tomcat，Jetty等则需要使用第三方的消息中间件。本部分内容介绍常用的消息中间件（Active MQ,Rabbit MQ，Zero MQ,Kafka）以及他们的特点。 <br>
5.1 ActiveMQ <br>
ActiveMQ 是Apache出品，最流行的，能力强劲的开源消息总线。ActiveMQ 是一个完全支持JMS1.1和J2EE 1.4规范的 JMS Provider实现，尽管JMS规范出台已经是很久的事情了，但是JMS在当今的J2EE应用中间仍然扮演着特殊的地位。 <br>
ActiveMQ特性如下： <br>
⒈ 多种语言和协议编写客户端。语言: Java,C,C++,C#,Ruby,Perl,Python,PHP。应用协议： OpenWire,Stomp REST,WS Notification,XMPP,AMQP <br>
⒉ 完全支持JMS1.1和J2EE 1.4规范 （持久化，XA消息，事务) <br>
⒊ 对spring的支持，ActiveMQ可以很容易内嵌到使用Spring的系统里面去，而且也支持Spring2.0的特性 <br>
⒋ 通过了常见J2EE服务器（如 Geronimo,JBoss 4,GlassFish,WebLogic)的测试，其中通过JCA 1.5 resource adaptors的配置，可以让ActiveMQ可以自动的部署到任何兼容J2EE 1.4 商业服务器上 <br>
⒌ 支持多种传送协议：in-VM,TCP,SSL,NIO,UDP,JGroups,JXTA <br>
⒍ 支持通过JDBC和journal提供高速的消息持久化 <br>
⒎ 从设计上保证了高性能的集群，客户端-服务器，点对点 <br>
⒏ 支持Ajax <br>
⒐ 支持与Axis的整合 <br>
⒑ 可以很容易得调用内嵌JMS provider，进行测试 <br>
5.2 Kafka <br>
Kafka是一种高吞吐量的分布式发布订阅消息系统，它可以处理消费者规模的网站中的所有动作流数据。 这种动作（网页浏览，搜索和其他用户的行动）是在现代网络上的许多社会功能的一个关键因素。 这些数据通常是由于吞吐量的要求而通过处理日志和日志聚合来解决。 对于像Hadoop的一样的日志数据和离线分析系统，但又要求实时处理的限制，这是一个可行的解决方案。Kafka的目的是通过hadoop的并行加载机制来统一线上和离线的消息处理，也是为了通过集群机来提供实时的消费。 <br>
Kafka是一种高吞吐量的分布式发布订阅消息系统，有如下特性： <br>
通过O(1)的磁盘数据结构提供消息的持久化，这种结构对于即使数以TB的消息存储也能够保持长时间的稳定性能。（文件追加的方式写入数据，过期的数据定期删除） <br>
高吞吐量：即使是非常普通的硬件Kafka也可以支持每秒数百万的消息 <br>
支持通过Kafka服务器和消费机集群来分区消息 <br>
支持Hadoop并行数据加载 <br>
Kafka相关概念 <br>
Broker <br>
Kafka集群包含一个或多个服务器，这种服务器被称为broker[5] <br>
Topic <br>
每条发布到Kafka集群的消息都有一个类别，这个类别被称为Topic。（物理上不同Topic的消息分开存储，逻辑上一个Topic的消息虽然保存于一个或多个broker上但用户只需指定消息的Topic即可生产或消费数据而不必关心数据存于何处） <br>
Partition <br>
Parition是物理上的概念，每个Topic包含一个或多个Partition. <br>
Producer <br>
负责发布消息到Kafka broker <br>
Consumer <br>
消息消费者，向Kafka broker读取消息的客户端。 <br>
Consumer Group <br>
每个Consumer属于一个特定的Consumer Group（可为每个Consumer指定group name，若不指定group name则属于默认的group）。 <br>
一般应用在大数据日志处理或对实时性（少量延迟），可靠性（少量丢数据）要求稍低的场景使用。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>