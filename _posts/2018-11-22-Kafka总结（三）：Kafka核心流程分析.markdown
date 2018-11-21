---
layout:     post
title:      Kafka总结（三）：Kafka核心流程分析
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><a href="https://mp.csdn.net/postedit/81283142" rel="nofollow">Kafka总结（一）：Kafka概述</a></p>

<p><a href="https://mp.csdn.net/postedit/81283229" rel="nofollow">Kafka总结（二）：Kafka核心组件</a></p>

<p><a href="https://mp.csdn.net/postedit/81283286" rel="nofollow">Kafka总结（三）：Kafka核心流程分析</a></p>

<p><a href="https://mp.csdn.net/postedit/81283397" rel="nofollow">Kafka总结（四）：Kafka命令操作</a></p>

<p><a href="https://mp.csdn.net/postedit/81283460" rel="nofollow">Kafka总结（五）：API编程详解</a></p>

<p><a href="https://mp.csdn.net/postedit/81283491" rel="nofollow">Kafka总结（六）：Kafka Stream详解</a></p>

<p><a href="https://mp.csdn.net/postedit/81283546" rel="nofollow">Kafka总结（七）：数据采集应用</a></p>

<p><a href="https://mp.csdn.net/postedit/81283568" rel="nofollow">Kafka总结（八）：KafKa与ELK整合应用</a></p>

<p><a href="https://mp.csdn.net/postedit/81283606" rel="nofollow">Kafka总结（九）：KafKa 与Spark整合应用</a></p>

<h3> </h3>

<h2 style="margin-left:0cm;">1. KafKaServer启动流程分析</h2>

<p style="margin-left:0cm;">KafKaServer的启动流程是由KafkaServer.startup()方法完成的，在该方法中会完成相应的组件，并启动这些组件。这些主件主要包括：</p>

<p style="margin-left:0cm;">任务调度器：KafkaScheduler</p>

<p style="margin-left:0cm;">日志管理器：LogManager</p>

<p style="margin-left:0cm;">网络通信服务器：SocketServer</p>

<p style="margin-left:0cm;">副本管理器：ReplicaManager</p>

<p style="margin-left:0cm;">控制器：KafKaController</p>

<p style="margin-left:0cm;">组协调器：GroupCoordinator</p>

<p style="margin-left:0cm;">动态配置管理器：DynamicConfigManager</p>

<p style="margin-left:0cm;">KafKa健康状态检测：KafkaHealthcheck</p>

<h2 style="margin-left:0cm;">2.创建主题流程分析</h2>

<p style="margin-left:0cm;">创建主题的流程包括两个阶段，第一阶段是客户端将主题元数据写入到ZooKeeper，可以称为客户端创建主题，第二阶段是控制器负责管理主题的创建，我们称其为服务端创建主题</p>

<h3 style="margin-left:0cm;">1.客户端创建主题</h3>

<p style="margin-left:0cm;">客户端创建主题可以使用命令行调用 kafka-topics.sh脚本来创建一个主题，或者通过调用相应的API来创建一个主题；</p>

<p style="margin-left:0cm;">无论是通过API还是命令行，底层都是通过调用TopicCommand.createTopic方法创建主题；</p>

<h3 style="margin-left:0cm;">2.分区副本分配</h3>

<p style="margin-left:0cm;">在创建主题的时候可以指定分区副本分配方案，也可以采用KafKa默认的分区副本分配策略创建副本分配方案。</p>

<p style="margin-left:0cm;">在0.10版本之后，KafKa支持指定代理机架信息，如果指定了机架信息在副本分配的时候会尽可能的让分区副本分布到不同的机架上。</p>

<p style="margin-left:0cm;">副本分配列表展示该主题拥有的分区以及各个分区的副本列表AR，AR中的第一个副本称为优先副本。</p>

<p style="margin-left:0cm;">KafKa保证优先副本会被均匀的分布到集群中所有的代理节点上，刚创建的主题一般会选择优先副本作为分区的Leader，这样一个主题的所有分区的Leader被均匀的分布到集群之中，而Leader负责所有的读写操作，这样就能够保证不会由于Leader分布过于集中而导致集群负载不均衡的问题；</p>

<h2 style="margin-left:0cm;">3.生产者</h2>

<p>【1】acks配置项    request.required.acks</p>

<p style="margin-left:0cm;">KafKa为生产者提供了三种消息确认机制（acks），用于配置代理接收到消息之后向生产者发送确认信号，以便生产者根据acks进行相应的处理，该机制通过属性request.required.acks设置，取值可以是0、-1、1（默认）</p>

<ul><li>acks = 0</li>
</ul><p style="margin-left:0cm;">生产者不用等待代理返回确认信息，而连续发送消息，尽管加快了消息投递的速度，但是无法保证消息是否已经被代理接收，有可能存在丢失数据的风险；</p>

<ul><li>acks = 1</li>
</ul><p style="margin-left:0cm;">生产者需要等待Leader副本已经成功将消息写入到日志文件中；这种方式仍然无法保证数据一定不会丢失——如果Flower副本还没有来得及同步Leader的数据，Leader分区宕机了，此时Flower取代Leader，但是这部分还没有来得及的数据就丢失了</p>

<ul><li>acks = -1</li>
</ul><p style="margin-left:0cm;">Leader副本和所有ISR列表中的副本都完成数据存储的时候才会向生产者发送确认消息，这种策略保证只要Leader副本和Follower副本至少一个存活，数据就不会丢失；</p>

<p style="margin-left:0cm;">但是这种方式同时也影响了生产者发送消息的速度以及吞吐量；</p>

<ol><li>batch.num.messages配置项 </li>
</ol><p style="margin-left:0cm;">KafKa支持消息批量（Batch）向dialing特定分区发送消息，批量的大小由属性batch.num.messages设置，表示每次批量发送消息的最大消息数，当生产者采用同步方式发送的时候该配置项将失效；</p>

<table border="1" cellspacing="0" style="margin-left:21pt;"><tbody><tr><td style="background-color:#5b9bd5;width:121.55pt;">
			<p style="margin-left:0cm;">属性名</p>
			</td>
			<td style="background-color:#5b9bd5;width:34.4pt;">
			<p style="margin-left:0cm;">默认</p>
			</td>
			<td style="background-color:#5b9bd5;width:237.85pt;">
			<p style="margin-left:0cm;">描述</p>
			</td>
		</tr><tr><td style="vertical-align:top;width:121.55pt;">
			<p style="margin-left:0cm;">message.send.max.retries</p>
			</td>
			<td style="vertical-align:top;width:34.4pt;">
			<p style="margin-left:0cm;">3</p>
			</td>
			<td style="vertical-align:top;width:237.85pt;">
			<p style="margin-left:0cm;"> </p>
			</td>
		</tr><tr><td style="vertical-align:top;width:121.55pt;">
			<p style="margin-left:0cm;">retry.backoff.ms</p>
			</td>
			<td style="vertical-align:top;width:34.4pt;">
			<p style="margin-left:0cm;">100</p>
			</td>
			<td style="vertical-align:top;width:237.85pt;">
			<p style="margin-left:0cm;"> </p>
			</td>
		</tr><tr><td style="vertical-align:top;width:121.55pt;">
			<p style="margin-left:0cm;">queue.buffering.max.ms</p>
			</td>
			<td style="vertical-align:top;width:34.4pt;">
			<p style="margin-left:0cm;">1000</p>
			</td>
			<td style="vertical-align:top;width:237.85pt;">
			<p style="margin-left:0cm;"> </p>
			</td>
		</tr><tr><td style="vertical-align:top;width:121.55pt;">
			<p style="margin-left:0cm;">queue.buffering.max.messages</p>
			</td>
			<td style="vertical-align:top;width:34.4pt;">
			<p style="margin-left:0cm;">10000</p>
			</td>
			<td style="vertical-align:top;width:237.85pt;">
			<p style="margin-left:0cm;"> </p>
			</td>
		</tr><tr><td style="vertical-align:top;width:121.55pt;">
			<p style="margin-left:0cm;">batch.num.messages</p>
			</td>
			<td style="vertical-align:top;width:34.4pt;">
			<p style="margin-left:0cm;">200</p>
			</td>
			<td style="vertical-align:top;width:237.85pt;">
			<p style="margin-left:0cm;"> </p>
			</td>
		</tr><tr><td style="vertical-align:top;width:121.55pt;">
			<p style="margin-left:0cm;">request.timeour.ms</p>
			</td>
			<td style="vertical-align:top;width:34.4pt;">
			<p style="margin-left:0cm;">1500</p>
			</td>
			<td style="vertical-align:top;width:237.85pt;">
			<p style="margin-left:0cm;"> </p>
			</td>
		</tr><tr><td style="vertical-align:top;width:121.55pt;">
			<p style="margin-left:0cm;">send.buffer.bytes</p>
			</td>
			<td style="vertical-align:top;width:34.4pt;">
			<p style="margin-left:0cm;">100kb</p>
			</td>
			<td style="vertical-align:top;width:237.85pt;">
			<p style="margin-left:0cm;"> </p>
			</td>
		</tr><tr><td style="vertical-align:top;width:121.55pt;">
			<p style="margin-left:0cm;">topic.metadata.refresh</p>
			</td>
			<td style="vertical-align:top;width:34.4pt;">
			<p style="margin-left:0cm;">5min</p>
			</td>
			<td style="vertical-align:top;width:237.85pt;">
			<p style="margin-left:0cm;"> </p>
			</td>
		</tr><tr><td style="vertical-align:top;width:121.55pt;">
			<p style="margin-left:0cm;">interval.ms</p>
			</td>
			<td style="vertical-align:top;width:34.4pt;">
			<p style="margin-left:0cm;"> </p>
			</td>
			<td style="vertical-align:top;width:237.85pt;">
			<p style="margin-left:0cm;"> </p>
			</td>
		</tr><tr><td style="vertical-align:top;width:121.55pt;">
			<p style="margin-left:0cm;">client.id</p>
			</td>
			<td style="vertical-align:top;width:34.4pt;">
			<p style="margin-left:0cm;">console-producer</p>
			</td>
			<td style="vertical-align:top;width:237.85pt;">
			<p style="margin-left:0cm;"> </p>
			</td>
		</tr><tr><td style="vertical-align:top;width:121.55pt;">
			<p style="margin-left:0cm;">queue.enqueue.timeout.ms</p>
			</td>
			<td style="vertical-align:top;width:34.4pt;">
			<p style="margin-left:0cm;">2147483647</p>
			</td>
			<td style="vertical-align:top;width:237.85pt;">
			<p style="margin-left:0cm;"> </p>
			</td>
		</tr><tr><td style="vertical-align:top;width:121.55pt;">
			<p style="margin-left:0cm;"> </p>
			</td>
			<td style="vertical-align:top;width:34.4pt;">
			<p style="margin-left:0cm;"> </p>
			</td>
			<td style="vertical-align:top;width:237.85pt;">
			<p style="margin-left:0cm;"> </p>
			</td>
		</tr></tbody></table><p style="margin-left:0cm;"> </p>

<h3 style="margin-left:0cm;">OldProducer执行流程</h3>

<p style="margin-left:0cm;">OldProducer是Scala版本的生产者，支持同步模式和异步模式，通过属性producer.type进行配置；</p>

<p style="margin-left:0cm;">同步方式发送消息：</p>

<p style="margin-left:0cm;">                  同步模式会调用EventHandler对消息进行处理之后，从ProducerPool中取出发送消息的SyncProducer将消息发送到代理</p>

<p style="margin-left:0cm;">异步方式发送消息：</p>

<p style="margin-left:0cm;">         首先会把消息插入到消息队列LinkedBlockingQueue中，当距离上一次SyncProducer将消息发往代理时间间隔不小于${queue.buffering.max.ms}或者队列中缓存的消息数量不小于${batch.num.messages}的时候才将消息发送到代理；</p>

<h3 style="margin-left:0cm;">KafKadProducer实现原理</h3>

<p style="margin-left:0cm;">用Java语言实现的KafkaProducer与老版本的生产者异步模式的设计思想很类似，只不过在实现细节上采用的数据结构有所不同，同时老版本的生产者同步模式和异步模式是分开实现的，而KafKaProducer的同步模式是通过异步模式来实现的；</p>

<p style="margin-left:0cm;">【KafkaProducer实例化过程】</p>

<p style="margin-left:0cm;">KafkaProducer在实例化的时候首先会加载和解析生产者相关的配置信息并封装成ProducerConfig对象，然后根据配置项主要完成以下的对象或者数据结构的实例化；</p>

<ol><li>从配置项中解析出clientId，客户端指定该配置项的值以便追踪程序运行的情况，在同一个进程内，当有多个KafkaProducer的时候，如果没有配置client.id则clientId以前缀producer-后面加一个从1递增的整数；</li>
	<li>根据配置项创建和注册用于KafKa metrics指标收集的相关对象，用于对KafKa集群相关的指标追踪；</li>
	<li>实例化分区器；</li>
</ol><p style="margin-left:0cm;">分区器用于为消息指定分区，客户端可以通过实现Partitioner接口自定义消息分配分区的规则。</p>

<p>     4.实例化消息Key和Value进行序列化操作的Serializer。</p>

<p>     5.根据配置实例化一组拦截器（ProducerInterceptor），用户可以指定多个拦截器。如果我们希望在消息发送前、消息发送到代理并ack，消息还未到达代理而失败或者调用send()方法失败这几种情况下进行相应的处理操作，就可以通过自定义拦截器实现该接口中相应的方法，多个拦截器会被顺序调用执行；</p>

<p>     6.实例化用于消息发送相关元数据信息的MetaData对象。MetaData对象是被客户端共享的，是线程安全的。MetaData的主要数据有两部分组成，一类是用于控制MetaData进行更新操作的相关配置信息，另一类就是集群信息Cluster。Cluster保存了集群中所有的主题以及所有主题对应的分区信息列表、可用分区列表、集群的代理列表等信息，在KafKaProducer实例化过程中会根据指定的代理列表初始化Cluster，并第一次更新MetaData；</p>

<p>    7.实例化用于存储消息的RecordAccumulator。其作用类似于一个队列，这里称之为消息累加器。KafkaProducer发送的消息都先被追加到消息累加器的一个双端队列Deque中，在消息累加器内部每一个主题的每一个分区TopicPartition对应一个双端队列，队列中的元素是RecordBatch，而RecordBatch是由同一个主题发往同一个分区的多条消息Record组成，并将结果以每个TopicPartition作为key，该TopicPartition所对应的双端队列作为Value保存到一个ConcurrentHashMap类型的batches中。</p>

<p style="margin-left:0cm;">采用双端队列是为了当消息发送失败之后需要重试的时候，将消息优先插入到队列的头部，而最新的消息总是插入到队列的尾部，只有需要重试发送的时候才在队列的头部插入；发送消息是从队列头部获取RecordBatch，这样就实现了对发送消息进行重试发送。</p>

<p>    8.根据指定的安全协议${security.protocol}创建一个ChannelBuilder，KafKa目前支持PLAINTEXT 、SSL 、 SASL_PLAINTEXT 、 SASL_SSL 、 TRACE这五种协议；然后创建NetworkClient实例，这个对象的底层是通过维持一个Socket连接来进行TCP通信的，用于生产者和各个代理进行Socket通信。由NetworkClient对象构造一个用于数据发送的Sender实例sender线程，最后通过sender创建一个KafkaThread线程，启动该线程，该线程是一个守护线程，在后台不断地轮询，将消息发送给代理；</p>

<p style="margin-left:0cm;">【send过程分析】</p>

<p style="margin-left:0cm;">在KafkaProducer实例化之后，调用KafkaProducer.send()发送消息</p>

<p style="margin-left:0cm;"><span style="color:#000088;">public</span><span style="color:#000000;"> Future&lt;RecordMetadata&gt; </span><span style="color:#009900;">send</span><span style="color:#000000;">(ProducerRecord&lt;K,V&gt; record); </span></p>

<p style="margin-left:0cm;"><span style="color:#000088;">public</span><span style="color:#000000;"> Future&lt;RecordMetadata&gt; </span><span style="color:#009900;">send</span><span style="color:#000000;">(ProducerRecord&lt;K,V&gt; record,Callback callback)</span></p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;"><strong>第一步：KafkaProducer</strong><strong>发送Record</strong><strong>将Record</strong><strong>写入消息缓冲区</strong></p>

<p style="margin-left:0cm;">首先，如果客户端指定了拦截器链ProducerInterceptors（由一个或者多个ProducerInterceptor构成的List），则ProducerRecord会被拦截器链中的每个ProducerInterceptor调用其onSend方法进行处理；</p>

<p style="margin-left:0cm;"><strong>第二步：由sender</strong><strong>线程从消息累加器中取出Record</strong><strong>将请求发送到相应的KafKa</strong><strong>节点</strong></p>

<p style="margin-left:0cm;"><strong>Sender</strong>将消息发送到KafKa相应代理节点主要依赖于KafkaClient、RecordAccumlator以及MetaData这三个对象。其中KafKaClient是一个接口，该接口定义了KafkaClient与网络交互的方式，其唯一的实现类是NetworkClient；</p>

<p style="margin-left:0cm;">首先从MetaData中获取集群信息，然后从RecordAccumlator中取出以满足发送条件的BatchRecord并构造相关网络层请求交由NetworkClient去执行。这个过程中需要取出每个TopicPartition所对应的分区Leader，而有可能某个TopicPartition的Leader不存在，则会触发请求MetaData更新操作；</p>

<p style="margin-left:0cm;">KafkaProducer的send操作没有发起网络请求，只是将消息发送到消息缓冲区，而网络请求是由KafkaProducer实例化的时候创建的Sender线程来完成的。后台线程Sender不断地额循环，把消息发送给KafKa集群。</p>

<p style="margin-left:0cm;">KafkaProducer发送的消息Record会根据TopicPartition分组保存到RecordAccumlator中，也就是会根据主题和分区进行分组。</p>

<p style="margin-left:0cm;">在KafKaProducer实例化的时候，创建了一个KafKaThread线程对象，该对象包装了一个Sender线程，在KafkaProducer实例化的时候启动该线程即启动了Sender。Sender在后台负责将RecordAccumlator中存储的Record发送到KafKa。</p>

<h2 style="margin-left:0cm;">4.消费者</h2>

<h3 style="margin-left:0cm;">1.旧版消费者</h3>

<p style="margin-left:0cm;">旧版消费者属于KafKa核心模块的一部分，分别为SimpleConsumer（习惯称之为低级消费者）和ZooKeeperConsumerConnector（习惯称之为高级消费者）；</p>

<p style="margin-left:0cm;">通过Java语言实现的KafkaConsumer称之为新版消费者；</p>

<h3 style="margin-left:0cm;">2. KafKaConsumer初始化（新版消费者）</h3>

<p style="margin-left:0cm;">KafkaProducer是线程安全的，但是KafKaConsumer是非线程安全的。</p>

<p style="margin-left:0cm;">KafKaConsumer定义了一个acquire()用来检测每个方法的调用是否只有一个线程在操作</p>

<p style="margin-left:0cm;">acquire()方法和release()方法成对出现与锁的Lock和unlock用法类似；</p>

<p style="margin-left:0cm;">订阅主题：subscribe()</p>

<p style="margin-left:0cm;">订阅主题的某些分区：assign()</p>

<p style="margin-left:0cm;">拉取消息：poll()</p>

<p style="margin-left:0cm;">指定消费起始位置：seek()  seekToBeginning()   seekToEnd()</p>

<p style="margin-left:0cm;">同步方式提交消息偏移量：commitSync()</p>

<p style="margin-left:0cm;">异步方式提交消息偏移量：commitAsync()</p>

<p style="margin-left:0cm;">获取分区分配关系方法：assignment()</p>

<p style="margin-left:0cm;">下一次消费消息位置方法：position()</p>

<p style="margin-left:0cm;">对分区消费控制方法：pause()  resume()</p>

<p style="margin-left:0cm;">【KafKaConsumer实例化过程】</p>

<p style="margin-left:0cm;">KafKaConsumer实例化过程就是从ConsumerConfig中提取相应的消费者级别配置实例化相应的组件；</p>

<p style="margin-left:0cm;">重要配置：</p>

<ol><li>group.id</li>
	<li>client.id</li>
	<li>key.deserializer</li>
	<li>value.deserializer</li>
	<li>enable.auto.commit</li>
	<li>max.poll.records</li>
	<li>max.poll.interval.ms</li>
	<li>send.buffer.bytes</li>
	<li>recive.buffer.bytes</li>
	<li>fetch.min.bytes</li>
	<li>fetch.max.bytes</li>
	<li>session.timeout.ms</li>
	<li>request.timeout.ms</li>
	<li>heartbeat.interval.ms</li>
	<li>auto.commit.interval.ms</li>
	<li>fetch.max.wait.ms</li>
</ol><p style="margin-left:0cm;">【KafKaConsumer实例化的主要组件】</p>

<ol><li>ConsumerConfig：</li>
</ol><p style="margin-left:0cm;">消费者级别的配置，将相应的配置传递给其他组件</p>

<p>       2.SubscribeState：</p>

<p style="margin-left:0cm;">维护了消费者订阅和消费消息的情况。该类定义了一系列用于保存订阅信息的字段；</p>

<h3 style="margin-left:0cm;">3.消费订阅</h3>

<p style="margin-left:0cm;">KafKaConsumer提供了两种订阅消息的方式；</p>

<ol><li>第一种：</li>
</ol><p style="margin-left:0cm;">通过KafkaConsumer.subscribe()方法指定消息对应的主题，支持以正则表达式方式指定主题；</p>

<p style="margin-left:0cm;">由同一个消费组的Leader消费者根据各消费者都支持的分区分配策略为消费者分配分区。同时在订阅主题的时候可以指定一个ConsumerRelalanceListener，在消费者发生平衡操作的时候回调处理；</p>

<ol><li>第二种：</li>
</ol><p style="margin-left:0cm;">通过KafKaConsumer.assign()方法指定需要消费的分区。</p>

<p style="margin-left:0cm;">该方式客户端直接指定了消费者与分区的对应关系；</p>

<p style="margin-left:0cm;">KafKaConsumer两类订阅方式是互斥的，客户端只能选择一种订阅方式，subscribe方法由KafKa自动进行分区分配</p>

<h3 style="margin-left:0cm;">4.消费消息</h3>

<p style="margin-left:0cm;">KafKaConsumer提供了一个poll方法，用于从服务端拉取消息，该方法通过Fetcher类来完成消息拉取以及更新消费偏移量；</p>

<p style="margin-left:0cm;">Fetcher类的主要功能是负责构造拉取消息的FetchRequest请求，然后通过ConsumerNetworkClient发送FetchRequest请求，最后对返回的结果进行处理并更新缓存中记录的消费位置。</p>

<h3 style="margin-left:0cm;">5.消息偏移量提交</h3>

<p style="margin-left:0cm;">旧版消费者将消息偏移量提交到ZooKeeper的/consumers/${group.id}/offsets/${topicName}/${partitionId}节点中，然而ZooKeeper并不适合频繁的进行读写操作，因此新版消费者进行了改进，将消息偏移量保存到KafKa的一个内部主题“_consumer_offsets”中，消费偏移量如同普通消息一样追加到该主题相应的分区当中。</p>

<p style="margin-left:0cm;">KafKa提供了两种提交消息偏移量的方式：KafkaConsumer自动提交和客户端调用KafKaConsumer相应的API提交，后者提交偏移量的方式通常也称为手动提交；</p>

<p style="margin-left:0cm;">【手动提交消息偏移量】</p>

<p style="margin-left:0cm;">手动提交需要设置enable.auto.commit配置项为false</p>

<p style="margin-left:0cm;">Kafka提供了</p>

<p style="margin-left:0cm;">同步提交commitSync和异步提交commitAsync方法供客户端提交消费偏移量；</p>

<p style="margin-left:0cm;">同步提交commitSync：</p>

<p style="margin-left:0cm;">         只有成功提交之后，才会进行下一次拉取消息操作</p>

<p style="margin-left:0cm;">异步提交commitAsync：</p>

<p style="margin-left:0cm;">         异步提交的时候KafkaConsumer不会被阻塞，当提交发生异常的时候可能发生重复消费的问题，但是异步方式可以提高消费吞吐量；</p>

<p style="margin-left:0cm;">【自动提交消息偏移量】</p>

<p style="margin-left:0cm;">设置enable.auto.commit配置项为true，通过设置auto.commit.interval.ms来设置提交操作的时间间隔；</p>

<h3 style="margin-left:0cm;">6.心跳检测</h3>

<p style="margin-left:0cm;">KafkaConsumer启动之后会定期向服务端组协调器GroupCoordinator发送心跳探测HeartbeatRequest请求，通过心跳探测通信双方相互感知对方是否存在并进行相应的处理。实现心跳探测功能的核心组件是HeartbeatThread线程，在GroupCoordinator实例化的时候，会创建一个守护线程HeartbeatThread，该线程通过计算当前时间与上次发送心跳时间之差判断是否发送心跳检测请求；</p>

<h3 style="margin-left:0cm;">7.分区数与消费者线程的关系</h3>

<p style="margin-left:0cm;">Kafka提供了配置项partition.assignment.strategy用来配置消费者线程与分区映射关系，Kafka提供了如下两种分配策略，默认是range分配的策略；</p>

<ol><li>round-robin分配策略</li>
</ol><p style="margin-left:0cm;">首先将订阅的主题分区以及消费者线程进行排序，然后通过轮询方式逐个将分区一次分给消费者线程；</p>

<p>      2.range分配策略：</p>

<p style="margin-left:0cm;">range策略即按照线程总数与分区总数进行整除运算计算一个跨度，然后将分区按照跨度进行平均分配，以保证分区尽可能的均衡的分配给所有的消费者线程。；具体逻辑如下：</p>

<p style="margin-left:0cm;">首先对线程集合按照字段顺序进行排序，然后通过分区总数和消费者线程总数进行整除运算计算每个线程平均分配的分区数，最后遍历线程集合为每个线程分配分区</p>

<h3 style="margin-left:0cm;">8.消费平衡过程</h3>

<p style="margin-left:0cm;">Kafka消费者平衡是指消费者重新加入消费组，并重新分配分区给消费者的过程。</p>

<p style="margin-left:0cm;">如下几种情况会引发消费者平衡操作：</p>

<ol><li>新的消费者加入消费组；</li>
	<li>当前消费者从消费组退出，这里的退出包括异常退出和正常关闭；</li>
	<li>消费者取消对某个主题的订阅</li>
	<li>订阅主题的分区增加；</li>
	<li>代理宕机新的协调器当选；</li>
	<li>当消费者在${session.timeout.ms}毫秒内还没有发送心跳请求，组协调器认为消费者已经退出；</li>
</ol><p style="margin-left:0cm;">在平衡过程中，因为要给消费者重新分配分区，所以会出现在一个短时间内消费者不能拉取消息。消费者平衡操作过程就是消费者重新加入消费组，然后GroupCoordinator选出一个Leader消费者，有Leader消费者根据各消费者支持的分区分配策略指定分区分配方案，然后在SyncGroupRequest请求的时候Leader消费者将分区分配方案上传给GroupCoordinator，Follower消费者在SyncGroupRequest请求响应的时候会收到GroupCoordinator转发的分区分配方案，这样各消费者就会得到自己应该消费的分区；</p>            </div>
                </div>