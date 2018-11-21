---
layout:     post
title:      Kafka总结（一）：Kafka概述
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p style="margin-left:0cm;"><a href="https://mp.csdn.net/postedit/81283142" rel="nofollow">Kafka总结（一）：Kafka概述</a></p>

<p><a href="https://mp.csdn.net/postedit/81283229" rel="nofollow">Kafka总结（二）：Kafka核心组件</a></p>

<p><a href="https://mp.csdn.net/postedit/81283286" rel="nofollow">Kafka总结（三）：Kafka核心流程分析</a></p>

<p><a href="https://mp.csdn.net/postedit/81283397" rel="nofollow">Kafka总结（四）：Kafka命令操作</a></p>

<p><a href="https://mp.csdn.net/postedit/81283460" rel="nofollow">Kafka总结（五）：API编程详解</a></p>

<p><a href="https://mp.csdn.net/postedit/81283491" rel="nofollow">Kafka总结（六）：Kafka Stream详解</a></p>

<p><a href="https://mp.csdn.net/postedit/81283546" rel="nofollow">Kafka总结（七）：数据采集应用</a></p>

<p><a href="https://mp.csdn.net/postedit/81283568" rel="nofollow">Kafka总结（八）：KafKa与ELK整合应用</a></p>

<p><a href="https://mp.csdn.net/postedit/81283606" rel="nofollow">Kafka总结（九）：KafKa 与Spark整合应用</a></p>

<h1 style="margin-left:0cm;">Kafka是什么？</h1>

<p style="margin-left:0cm;">KafKa是一个高吞吐量、分布式的发布——订阅消息系统。据KafKa官网介绍，当前的KafKa已经定位为一个分布式流式处理平台（a distributed streaming platform），它以可水平扩展和具有高吞吐量等特性而著称。越来越多的开源分布式处理系统（Flume、Apache Storm 、Spark、Flink等）支持与KafKa集成。</p>

<h1 style="margin-left:0cm;">1.Kafka简介</h1>

<h2>1.Kafka背景</h2>

<p style="margin-left:0cm;">计算机所存储的信息量正在呈爆炸式的增长，目前数据量已经进入大规模和超大规模的海量数据时代，如何高效的存储、分析、处理和挖掘海量数据已经成为技术研究的热点和难点问题。</p>

<p style="margin-left:0cm;">         当前出现的云存储、分布式存储系统、NoSQL数据库以及列存储等前沿技术在海量数据的驱使下，正在日新月异的向前发展，采用这些技术来处理大数据称为一种发展趋势。</p>

<p style="margin-left:0cm;">       而如何采集和运营管理、分析这些数据也是大数据处理中的一个至关重要的组成环节，这就需要相应的基础设施对其提供支持。</p>

<p style="margin-left:0cm;">针对这个需求，当前业界已有很多开源的消息系统应运而生，KafKa就是当前流行的一款非常优秀的消息系统；</p>

<p style="margin-left:0cm;">         KafKa是一款开源的、轻量级的、分布式、可分区和具有复制备份的（Replicated）、基于ZooKeeper的协调管理的分布式流平台的功能强大的消息系统。与传统的消息系统相比，KafKa能够很好的处理活跃的流数据，使得数据在各个子系统中高性能、低延迟地不停流转。</p>

<p style="margin-left:0cm;">         据KafKa官网介绍，KafKa定位就是一个分布式流处理平台，作为一个流式处理平台，必须具备以下三个特征：</p>

<ol><li>能够允许发布和订阅流数据。从这个角度来讲，平台更像是一个消息队列或者企业级的消息系统；</li>
	<li>存储流数据的时候提供相应的容错机制；</li>
	<li>当流数据达到的时候能够即时的被处理；</li>
</ol><p style="margin-left:0cm;">kafka能够很好的满足以上3个特性，通过kafka能够很好的建立实时流式数据通道，由该通道可靠地获取系统或者应用程序的数据，也可以通过kafka方便的构建实时流数据应用来转换或者是对流数据进行响应处理。特别是在0.10版本之后，KafKa推出KafKa Streams，这让KafKa对流数据处理变得更加的方便。</p>

<h2 style="margin-left:0cm;">2. KafKa基本结构</h2>

<p style="margin-left:0cm;">KafKa的结构需要解决如下的问题：</p>

<ul><li>生产者负责生产消息，如何写入KafKa集群。</li>
	<li>消费者如何从KafKa集群中拉取消息</li>
	<li>KafKa如何存储消息</li>
	<li>KafKa集群如何管理调度</li>
	<li>如何进行负载均衡</li>
	<li>各组件之间如何进行通信</li>
</ul><h2 style="margin-left:0cm;">3. KafKa基本概念</h2>

<p>【1】主题</p>

<p style="margin-left:0cm;">KafKa将一组消息抽象归纳为一个主题（Topic），也就是说，一个主题就是对消息的一个分类。生产者将消息发送到特定主题，消费者订阅主题或者主题的某些分区进行消费。</p>

<p>【2】消息</p>

<p style="margin-left:0cm;">消息是KafKa通信的基本单位，由一个固定长度的消息头和一个可变长度的消息体构成。在老版本中，每一条消息称为Message；在由Java重新实现的客户端中，每一条消息称为Record。</p>

<p>【3】分区和副本</p>

<p style="margin-left:0cm;">KafKa将一组消息归纳为一个主题，而每个主题又被分为一个或者多个分区（Partition）。每个分区有一系列有序、不可变的消息组成，是一个有序队列；</p>

<p style="margin-left:0cm;">         每个分区在物理上对应一个文件夹，分区命令规则：主题名称—分区编号</p>

<p style="margin-left:0cm;">分区编号从0开始。每个分区又有一至多个副本（Replica），分区的副本分布在集群的不同代理上，以提高可用性。<strong>从存储的角度分析，分区的每个副本在逻辑上抽象为一个日志（</strong><strong>Log</strong><strong>）对象，即分区的副本与日志对象是一一对应的关系</strong>。每个主题对应的分区数可以在KafKa启动的时候所加载的配置文件中配置，也可以在创建主题的时候指定。客户端还可以在创建主题之后修改主题的分区数；</p>

<p style="margin-left:0cm;">         分区使得KafKa在并发处理上变得更加简单，理论上来讲，分区数越多吞吐量越高，但是这需要根据集群实际的环境和业务场景来决定。同时分区也是KafKa保证消息被顺序消费以及对消息进行负载均衡的基础；</p>

<p>         注意：<u>KafKa只能保证一个分区之内的消息的有序性</u>，并不能保证跨分区消息的有</p>

<p style="margin-left:0cm;">序性。每条消息被追加到相应的分区中，是顺序写磁盘，效率非常高，这是KafKa高吞吐率的一个重要保证。</p>

<p style="margin-left:0cm;">         与传统消息不同的一点：KafKa并不会立即删除已经被消费的数据，KafKa提供了两种删除老数据的方式：</p>

<p style="margin-left:0cm;">第一种：基于消息已经存储的时间长度；</p>

<p style="margin-left:0cm;">第二种：基于分区的大小；</p>

<p style="margin-left:0cm;">以上两种策略都能够通过配置文件进行配置；</p>

<p style="margin-left:0cm;">【4】Leader副本和Follower副本</p>

<p style="margin-left:0cm;">由于KafKa副本的存在，就需要保证一个分区的多个副本之间数据的一致性，KafKa会选择该分区的一个副本作为Leader副本，而该分区其他副本作为Follower副本，只有Leader副本才负责处理客户端读/写请求，Follower副本从Leader副本同步数据。如果Leader副本失效，通过相应的选举算法将从其他Follower副本中选出新的Leader副本。</p>

<p>【5】偏移量</p>

<p style="margin-left:0cm;">任何发布到分区的消息会直接追加到日志文件（分区目录下以.log为文件名后缀的数据文件）的尾部，而每条消息在日志中的位置都会对应一个按顺序递增的偏移量。偏移量是一个分区下严格有序的逻辑值，它并不表示消息在磁盘上的物理位置。由于KafKa几乎不允许对消息进行随机读写，因此KafKa并没有提供额外的索引机制到存储偏移量，也就是说并不会给偏移量再提供索引。</p>

<p style="margin-left:0cm;">         消费者可以通过控制消息偏移量来对消息进行消费，如消费者可以指定消费的起始偏移量。为了保证消息顺序消费，消费者已经消费的信息对应的偏移量也需要保存。</p>

<p style="margin-left:0cm;">需要说明的是：消费者对消息偏移量的操作并不会影响消息本身的偏移量。旧版消费者将消费偏移量保存到ZooKeeper当中，而新版的消费者是将消费偏移量保存到KafKa内部的一个主题当中。</p>

<p style="margin-left:0cm;">当然消费者也可以自己在外部系统保存消费偏移量，而无需保存到KafKa中；</p>

<p style="margin-left:0cm;">【6】日志段</p>

<p style="margin-left:0cm;">一个日志又被划分为多个日志段（LogSegment），日志段是KafKa日志对象分片中的最小单位。与日志对象一样，日志段也是一个逻辑概念，<strong>一个日志段对应磁盘上的一个日志文件（</strong><strong>.log</strong><strong>）和两个索引文件</strong>：<strong>（</strong><strong>.index</strong><strong>）表示消息偏移量索引文件，（.timeindex</strong><strong>）表示消息时间戳索引文件。</strong></p>

<p style="margin-left:0cm;">【7】代理</p>

<p style="margin-left:0cm;">KafKa集群就是由一个或者多个KafKa实例构成，每一个KafKa实例称为代理（Broker），通常也称代理为KafKa服务器（KafKaServer）。在生产环境中KafKa集群一般包括一台或者多台机器，我们可以在一台机器上配置一个或者多个代理。每一个代理具有唯一的与该集群中其他代理都不同的非负整数的id，这个id就是代理的名字，也就是在启动代理的时候配置的broker.id对应的值。</p>

<p style="margin-left:0cm;">代理有很多相关的参数配置；</p>

<p>【8】生产者</p>

<p style="margin-left:0cm;">生产者（Producer）负责将消息发送给代理，也就是向KafKa代理发送消息的客户端；</p>

<p>【9】消费者和消费组</p>

<p style="margin-left:0cm;">消费者（Consumer）以拉取（Pull）方式拉取数据，它是消费的客户端。在KafKa中每一个消费者都属于一个特定的消费者组（ConsumerGroup），我们可以为每个消费者指定一个消费组，以groupId代表消费组名称，通过group.id配置设置。我们不指定消费组，则该消费者属于默认的消费组test-consumer-group。同时，每个消费者也有一个全局唯一的id，通过配置项client.id指定，如果客户端没有指定消费者的id，KafKa会自动为该消费者生成一个全局唯一的id，格式如下：</p>

<p style="margin-left:0cm;">${groupId}-${timestamp}-${UUID前8位字符}。</p>

<p style="margin-left:0cm;">         注意：同一个主题的一条消息只能被同一个消费组下某一个消费者消费，但是不同消费组的消费者可以同时消费该消息。消费组是KafKa用来实现对一个主题消息进行广播和单播的手段，实现消息广播只需要指定各个消费者均属于不同的消费组，消息单播则只需让各个消费者属于同一个消费组。</p>

<p>【10】ISR</p>

<p style="margin-left:0cm;">KafKa在ZooKeeper中动态维护了一个ISR（In-sync Replica）即保存同步的副本列表，该列表中保存的是与Leader副本保持消息同步的所有副本对应的代理节点id。</p>

<p style="margin-left:0cm;">如果一个Follower副本宕机（本书用宕机来特指某个代理失效的情景，包括但不咸鱼代理被关闭，如代理被人为关闭或是发生物理故障、心跳检测过期、网络延迟、进程崩溃等）或者落后太多，则该Follower副本节点将从ISR列表中移除；</p>

<p>【11】ZooKeeper</p>

<p style="margin-left:0cm;">这里我们并不打算介绍ZooKeeper的相关知识，只是简要的介绍ZooKeeper在KafKa中的作用。KafKa利用ZooKeeper保存相应元素据信息，KafKa<strong>元素据包括如代理节点信息、KafKa</strong><strong>集群信息、旧版消费者信息以及消费偏移量信息、主题信息、分区状态信息、分区副本分配方案信息、动态配置信息等</strong>。KafKa在启动或者运行过程中会在ZooKeeper上创建相关的节点来保存元数据信息，KafKa通过监听机制在这些节点注册相应监听器来监听节点元素据的变化，从而由ZooKeeper负责管理维护KafKa集群，同时通过ZooKeeper我们能够很方便的对KafKa集群进行水平扩展以及数据迁移；</p>

<h2 style="margin-left:0cm;">4. KafKa设计概述</h2>

<h3 style="margin-left:0cm;">1. KafKa设计动机</h3>

<p style="margin-left:0cm;">KafKa设计的初衷是是KafKa能够成为统一的、实时处理大规模平台数据。为了达到这个目标，KafKa必须支持以下的几个应用场景：</p>

<ol><li>具有高吞吐量来支持诸如实时的日志集这样的大规模事件流；</li>
	<li>能够很好的处理大量积压的数据，以便能够周期性的加载离线数据进行处理。</li>
	<li>能够低延迟的处理传统消息应用场景；</li>
	<li>能够支持分区、分布式、实时地处理消息，同时具有容错保障机制。</li>
</ol><p style="margin-left:0cm;">满足了以上功能的KafKa与传统的消息系统相比更像是一个数据库日志系统。</p>

<h3 style="margin-left:0cm;">2. KafKa特性</h3>

<p>【1】消息持久化</p>

<p style="margin-left:0cm;">KafKa高度依赖于文件系统来存储和缓存消息。说到文件系统，大家普遍任务磁盘读写慢，依赖于文件系统进行存储和缓存消息势必在性能上会大打折扣，其实文件系统存储速度快慢一定程度上也取决于我们对磁盘的用法。</p>

<p style="margin-left:0cm;">KafKa官网介绍：磁盘线性写的速度约是随机写的速度的6000多倍。由此看来，磁盘的快慢取决于我们是如何应用磁盘的。</p>

<p style="margin-left:0cm;">KafKa是基于JVM的，Java对象的增加会导致JVM的垃圾回收也越来越繁琐，这些都加大了内存的消耗。鉴于以上因素，使用文件系统和依赖于页缓存（page cache）的存储比维护一个内存的存储或者是应用其他结构存储消息更有优势，因此KafKa选择以文件系统来存储数据；</p>

<p style="margin-left:0cm;">因为是顺序追加，所以KafKa在设计上是采用的时间复杂度为O(1)的磁盘结构，它提供了常量的时间性能，即使是存储海量信息（TB级别）也是如此，性能和数据的大小关系也不大，同时KafKa将数据持久化到磁盘上，这样只要磁盘空间足够大数据就可以一直追加，而不会想消息系统在消息被消费之后就删除掉，KafKa提供了相关配置让用户决定消息需要保存多久，因此，KafKa能够在没有性能损失的情况下提供一般消息系统不具备的特性；</p>

<p style="margin-left:0cm;">正式因为KafKa将消息持久化，使得KafKa在机器重启之后，已存储的消息可以继续恢复使用。同时KafKa能够很好的支持在线或者离线的处理、与其他存储以及流处理框架集成；</p>

<p style="margin-left:0cm;">【2】高吞吐量</p>

<p style="margin-left:0cm;">高吞吐量是KafKa设计的主要目标，KafKa将数据写到磁盘，充分利用磁盘的顺序读写。同时KafKa在数据写入以及数据同步采用了零拷贝（zero-copy）技术，采用sendFile()函数调用，该函数是在两个文件描述符之间直接传递数据，完全在内核中操作，从而避免了内核缓冲区与用户缓冲区之间数据的拷贝，操作效率极高。KafKa还支持数据压缩以及批量发送，同时KafKa将每个主题划分为多个分区，这一系列的优化以及实现方法使得KafKa具有很高的吞吐量。</p>

<p style="margin-left:0cm;">KafKa支持每秒钟数百万级别的消息；</p>

<p>【3】扩展性</p>

<p style="margin-left:0cm;">KafKa依赖ZooKeeper来对集群进行协调管理，这样使得KafKa更加容易进行水平扩展。生产者、消费者和代理都为分布式，可以配置多个。同时在机器扩展的时候无需将整个集群停机，集群可以自动的感知，重新进行负载均衡以及数据复制；</p>

<p style="margin-left:0cm;">【4】多客户端支持</p>

<p style="margin-left:0cm;">KafKa支持多种连接器和多种语言接入，与当前大多数主流的数据框架都可以很好的集成；</p>

<p style="margin-left:0cm;">【5】KafKa Streams</p>

<p style="margin-left:0cm;"> KafKa Streams是一个用Java语言实现的用于流处理的jar文件；</p>

<p style="margin-left:0cm;">【6】安全机制</p>

<ol><li>通过SSL和SASL（Kerberos），SASL/PLAIN验证机制支持生产者、消费者与代理连接时的身份认证；</li>
	<li>支持代理与ZooKeeper连接身份验证；</li>
	<li>通信的时候数据加密；</li>
	<li>客户端读、写权限认证；</li>
	<li>KafKa支持与外部其他认证授权服务的集成；</li>
</ol><p>【7】数据备份</p>

<p style="margin-left:0cm;">KafKa可以为每个主题指定副本数，对数据进行持久化备份，这可以在一定的程度上方式数据的丢失，提高可用性；</p>

<p style="margin-left:0cm;">【8】轻量级</p>

<p style="margin-left:0cm;">KafKa的代理是无状态的，代理不记录消息是否被消费，消息偏移量的管理工作交由消费者自身或者组协调器来完成。同时集群本身不需要生产者和消费者的状态信息，这就使得KafKa非常的轻量级；</p>

<p style="margin-left:0cm;">【9】消息压缩</p>

<p style="margin-left:0cm;">KafKa支持Gzip、Snappy以及LZ4这三种压缩方式，通常把多条消息放在一起组成MessageSet，然后将MessageSet放到一条消息里面，从而提高压缩比率进而提高吞吐量；</p>

<h3 style="margin-left:0cm;">3. KafKa应用场景</h3>

<p style="margin-left:0cm;">消息系统或者是说消息队列中间件是当前处理大数据的一个非常重要的组件，用来解决应用解耦、异步通信、流量控制等问题，从而构建一个高效、灵活、消息同步和异步传输处理、存储转发、可伸缩和最终一致性的稳定系统。</p>

<p style="margin-left:0cm;">当前比较流行的消息中间件有：KafKa、RocketMQ、RabbitMQ、ZeroMQ、ActiveMQ、Redis等，这些消息中间件在性能以及功能上各有所长。如何选择一个消息中间件取决于我们的业务场景、系统运行环境、开发人员以及运维人员对消息中间件的情况等。</p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;">以下的场景可以选择使用KafKa：</p>

<p>(1)：消息系统：</p>

<p style="margin-left:0cm;">KafKa作为一个优秀的消息系统，具有高吞吐量、内置的分区、备份冗余分布式的特点，为大规模的消息处理提供了一种很好的解决方案；</p>

<p>(2)：应用监控：</p>

<p style="margin-left:0cm;">利用KafKa采集应用程序和服务器健康相关指标，如CPU占用率、IO、内存、连接数、TPS、QPS等，然后将指标信息进行处理，从而可以构建一个具有监控仪表盘、曲线图等可视化监控系统。例如：很多公司采用KafKa与ELK（Elasticsearch、Logstash和Kibana）真核构建应用服务监控系统；</p>

<p>(3)：网站用户行为追踪：</p>

<p style="margin-left:0cm;">为了更好的了解用户的行为，操作习惯，改善用户体验，进而对产品进行升级，将用户的操作轨迹、内容等信息发送到KafKa集群上，通过Hadoop、Spark或者Strom等进行数据分析处理，生成相应的统计报告，未推荐系统推荐对象提供数据源，进而为每个用户进行个性化推荐；</p>

<p>(4)：流处理：</p>

<p style="margin-left:0cm;">需要将已经收集的流数据提供给其他流式计算框架进行处理，KafKa已经提供了KafKa Streams支持对流数据的处理；</p>

<p>(5)：持久性日志：</p>

<p style="margin-left:0cm;">KafKa可以为外部系统提供一种持久性日志的分布式系统。日志可以在多个节点之间进行备份，KafKa为故障节点数据恢复提供了一种重新同步机制。同时，KafKa很方便的与HDFS和Flume进行整合，这样就可以方便的将KafKa采集的数据持久化到其他的外部系统；</p>

<p style="margin-left:0cm;"> </p>            </div>
                </div>