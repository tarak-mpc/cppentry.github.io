---
layout:     post
title:      Kafka_转 Kafka 各版本 提升特性
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>kafka 0.8–kafka 0.9–kafka 0.10 – kafka 1.0 各版本的新特性</p>

<p>最重要的是，就是consumer的机制。</p>

<p>consumer机制各版本的演化：</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180521203755354?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzIxNDM5Mzk1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>本图示来源于：https://blog.csdn.net/cymvp/article/details/69554569</p>

<p> </p>

<h1 id="kafka-082-新特性"><a name="t0"></a>kafka-0.8.2 新特性</h1>

<p>producer不再区分同步（sync）和异步方式（async），所有的请求以异步方式发送，这样提升了客户端效率。producer请求会返回一个应答对象，包括偏移量或者错误信。这种异步方地批量的发送消息到kafka broker节点，因而可以减少server端资源的开销。新的producer和所有的服务器网络通信都是异步地，在ack=-1模式下需要等待所有的replica副本完成复制时，可以大幅减少等待时间。<br>
　　在0.8.2之前，kafka删除topic的功能存在bug。<br>
　　在0.8.2之前，comsumer定期提交已经消费的kafka消息的offset位置到zookeeper中保存。对zookeeper而言，每次写操作代价是很昂贵的，而且zookeeper集群是不能扩展写能力的。在0.8.2开始，可以把comsumer提交的offset记录在compacted topic（__comsumer_offsets）中，该topic设置最高级别的持久化保证，即ack=-1。__consumer_offsets由一个三元组&lt; comsumer group, topic, partiotion&gt; 组成的key和offset值组成，在内存也维持一个最新的视图view，所以读取很快。<br>
　　kafka可以频繁的对offset做检查点checkpoint，即使每消费一条消息提交一次offset。<br>
　　在0.8.1中，已经实验性的加入这个功能，0.8.2中可以广泛使用。auto rebalancing的功能主要解决broker节点重启后，leader partition在broker节点上分布不均匀，比如会导致部分节点网卡流量过高，负载比其他节点高出很多。auto rebalancing主要配置如下，<br>
controlled.shutdown.enable ，是否在在关闭broker时主动迁移leader partition。基本思想是每次kafka接收到关闭broker进程请求时，主动把leader partition迁移到其存活节点上，即follow replica提升为新的leader partition。如果没有开启这个参数，集群等到replica会话超时，controller节点才会重现选择新的leader partition，这些leader partition在这段时间内也不可读写。如果集群非常大或者partition 很多，partition不可用的时间将会比较长。<br>
　　1）可以关闭unclean leader election，也就是不在ISR（IN-Sync Replica）列表中的replica，不会被提升为新的leader partition。unclean.leader.election=false时，kafka集群的持久化力大于可用性，如果ISR中没有其它的replica，会导致这个partition不能读写。<br>
　　2）设置min.isr（默认值1）和 producer使用ack=-1，提高数据写入的持久性。当producer设置了ack=-1，如果broker发现ISR中的replica个数小于min.isr的值，broker将会拒绝producer的写入请求。max.connections.per.ip限制每个客户端ip发起的连接数，避免broker节点文件句柄被耗光。</p>

<p> </p>

<p> </p>

<p> </p>

<h1 id="kafka-09-新特性"><a name="t1"></a>kafka-0.9 新特性</h1>

<p><br>
一、安全特性<br>
　　在0.9之前，Kafka安全方面的考虑几乎为0，在进行外网传输时，只好通过Linux的防火墙、或其他网络安全方面进行配置。相信这一点，让很多用户在考虑使用Kafka进行外网消息交互时有些担心。<br>
　　客户端连接borker使用SSL或SASL进行验证<br>
　　borker连接ZooKeeper进行权限管理<br>
　　数据传输进行加密（需要考虑性能方面的影响）<br>
　　客户端读、写操作可以进行授权管理<br>
　　可以对外部的可插拔模块的进行授权管理<br>
　　当然，安全配置方面是可选的，可以混合使用。如：做过安全配置的的borkers和没有进行安全配置的borkers放在同一集群，授权的客户端和没有授权的客户端，也可以在同一个集群等等。具体配置详见官方文档。<br>
二、Kafka Connect<br>
　　这个功能模块，也是之前版本没有的。可以从名称看出，它可以和外部系统、数据集建立一个数据流的连接，实现数据的输入、输出。有以下特性：<br>
　　使用了一个通用的框架，可以在这个框架上非常方面的开发、管理Kafka Connect接口<br>
　　支持分布式模式或单机模式进行运行<br>
　　支持REST接口，可以通过REST API提交、管理 Kafka Connect集群<br>
　　offset自动管理<br>
　　同时，官方文档中，也给出了例子。通过配置，往一个文本文件中输入数据，数据可以实时的传输到Topic中。在进行数据流或者批量传输时，是一个可选的解决方案。<br>
三、新的Comsumer API<br>
　　新的ComsumerAPI不再有high-level、low-level之分了，而是自己维护offset。这样做的好处是避免应用出现异常时，数据未消费成功，但Position已经提交，导致消息未消费的情况发生。通过查看API，新的Comsumer API有以下功能：<br>
　　Kafka可以自行维护Offset、消费者的Position。也可以开发者自己来维护Offset，实现相关的业务需求。<br>
　　消费时，可以只消费指定的Partitions<br>
　　可以使用外部存储记录Offset，如数据库之类的。<br>
　　自行控制Consumer消费消息的位置。<br>
　　可以使用多线程进行消费</p>

<p> </p>

<p> </p>

<h1 id="kafka-010-新特性"><a name="t2"></a>kafka-0.10 新特性</h1>

<p>　　Kafka已经内置了机架感知以便隔离副本，这使得Kafka保证副本可以跨越到多个机架或者是可用区域，显著提高了Kafka的弹性和可用性。这个功能是由Netflix提供的<br>
　　所有Kafka中的消息都包含了时间戳字段，这个时间就是这条消息产生的时间。这使得Kafka Streams能够处理基于事件时间的流处理；而且那些通过时间寻找消息以及那些基于事件时间戳的垃圾回收特性能为可能。<br>
　　Apache Kafka 0.9.0.0版本引入了新的安全特性，包括通过SASL支持Kerberos。ApacheKafka 0.10.0.0现在支持更多的SASL特性，包括外部授权服务器，在一台服务器上支持多种类型的SASL认证以及其他的改进。<br>
　　Kafka Connect得到了持续提升。在此之前，用户需要监控日志以便看到各个connectors以及他们task的状态，现在Kafka已经支持了获取的状态API这样使得监控变得更简单。同时也添加了控制相关的API，这使得用户可以在进行维护的时候停止一个connector；或者手动地重启那些失败的task。这些能够直观的在用户界面展示和管理connector目前可以在控制中心(Control Center)看到。<br>
　　Kafka Consumer Max Records，在Kafka0.9.0.0，开发者们在新consumer上使用poll()函数的时候是几乎无法控制返回消息的条数。不过值得高兴的是，此版本的Kafka引入了max.poll.records参数，允许开发者控制返回消息的条数。<br>
 </p>

<p>　　协议版本改进，Kafka brokers现在支持返回所有支持的协议版本的请求API，这个特点的好处就是以后将允许一个客户端支持多个broker版本。</p>

<p> </p>

<h1 id="kafka-011-新特性"><a name="t3"></a>kafka-0.11 新特性</h1>

<p>0.11版本是一个里程碑式的大版本，特别是Kafka从这个版本开始支持“exactly-once”语义(下称EOS， exactly-once semantics)。</p>

<p>一、修改unclean.leader.election.enabled默认值</p>

<p>Kafka社区终于下定决心要把这个参数的默认值改成false，即不再允许出现unclean leader选举的情况，在正确性和高可用性之间选择了前者。如果依然要启用它，用户需要显式地在server.properties中设置这个参数=true</p>

<p>二、确保offsets.topic.replication.factor参数被正确应用</p>

<p>__consumer_offsets这个topic是Kafka自动创建的，在创建的时候如果集群broker数 三、优化了对Snappy压缩的支持</p>

<p>之前由于源代码中硬编码了block size，使得producer使用Snappy时的表现比LZ4相差很多，但其实Snappy和LZ4两者之差距不应该很大。故此0.11版本中对Snappy的默认blocksize做了调整。不过这一点需要详尽的性能测试报告来证明此改动是有效的。</p>

<p>四、消息增加头部信息(Header)</p>

<p>Record增加了Header，每个header是一个KV存储。具体的header设计参见KIP-82</p>

<p>五、空消费者组延时rebalance</p>

<p>为了缩短多consumer首次rebalance的时间，增加了“group.initial.rebalance.delay.ms”用于设置group开启rebalance的延时时间。这段延时期间允许更多的consumer加入组，避免不必要的JoinGroup与SyncGroup之间的切换。当然凡事都是trade-off，引入这个必然带来消费延时。</p>

<p>六、消息格式变更</p>

<p>增加最新的magic值：2。增加了header信息。同时为了支持幂等producer和EOS，增加一些与事务相关的字段，使得单个record数据结构体积增加。但因为优化了RecordBatch使得整个batch所占体积反而减少，进一步降低了网络IO开销。</p>

<p>七、新的分配算法：StickyAssignor</p>

<p>比range和round-robin更加平衡的分配算法。指定partition.assignment.strategy= org.apache.kafka.clients.consumer.StickyAssignor可以尝尝鲜。不过根据我的经验，分配不均匀的情况通常发生在每个consumer订阅topic差别很大的时候。比如consumer1订阅topic1, topic2, topic4, consumer2订阅topic3, topic4这种情况</p>

<p>八、controller重设计</p>

<p>Controller原来的设计非常复杂，使得社区里面的人几乎不敢改动controller代码。老版本controller的主要问题在我看来有2个：1. controller需要执行1，2，3，4，5，6步操作，倘若第3步出错了，无法回滚前两步的操作;2. 多线程访问，多个线程同时访问Controller上下文信息。0.11版本部分重构了controller，采用了单线程+基于事件队列的方式。具体效果咱们拭目以待吧~~</p>

<p>九、支持EOS</p>

<p>0.11最重要的功能，没有之一!EOS是流式处理实现正确性的基石。主流的流式处理框架基本都支持EOS(如Storm Trident, Spark Streaming, Flink)，Kafka streams肯定也要支持的。0.11版本通过3个大的改动支持EOS：1.幂等的producer(这也是千呼万唤始出来的功能);2. 支持事务;3. 支持EOS的流式处理(保证读-处理-写全链路的EOS)</p>

<h1 id="kafka-100-新特性"><a name="t4"></a></h1>

<h1> </h1>

<h1>kafka-1.0.0 新特性</h1>

<p>apache kafka 于2017-11-1 推出全新版本1.0.0。</p>

<p>0.10.0 版本里开始引入的 Streams API 在 1.0.0 版本里继续演进，改进了 builder API（KIP-120），新增了用于查看运行时活跃任务的 API（KIP-130）和用于聚合分区的 cogroup API（KIP-150）。增强的 print() 和 writeAsText() 方法让调试变得更容易（KIP-160）。其他更多信息可以参考 Streams 文档。</p>

<p>改进了 Connect 的度量指标（KIP-196），新增了大量用于健康监测的度量指标（KIP-188），并提供了集群的 GloabalTopicCount 和 GlobalPartitionCount 度量指标（KIP-168）。</p>

<p>支持 Java 9，实现更快的 TLS 和 CRC32C，加快了加密速度，降低了计算开销。</p>

<p>调整了 SASL 认证模块的错误处理逻辑（KIP-152），原先的认证错误信息现在被清晰地记录到日志当中。</p>

<p>更好地支持磁盘容错（KIP-112），更优雅地处理磁盘错误，单个 JBOD 上的磁盘错误不会导致整个集群崩溃。</p>

<p>0.11.0 版本中引入的幂等性生产者需要将max.in.flight.requests.per.connection 参数设置为 1，这对吞吐量造成了一定的限制。而在 1.0.0 版本里，这个参数最大可以被设置为 5（KAFKA-5949），极大提升了吞吐量范围。</p>            </div>
                </div>