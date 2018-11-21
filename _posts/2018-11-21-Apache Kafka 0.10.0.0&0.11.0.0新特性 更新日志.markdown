---
layout:     post
title:      Apache Kafka 0.10.0.0&0.11.0.0新特性 更新日志
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="一about-01000">一、About 0.10.0.0</h1>

<p>Apache Kafka 0.10.0.0于美国时间2016年5月24日正式发布。Apache Kafka 0.10.0.0是Apache Kafka的主要版本。以下是新特性：</p>



<h3 id="1kafka-streams">1、Kafka Streams</h3>

<p>Kafka Streams由Confluent Platform首先在其平台的技术预览中行提出，目前已经在Apache Kafka 0.10.0.0上可用了。Kafka Streams其实是一套类库，它使得Apache Kafka可以拥有流处理的能力。Kafka Streams包含了一整套描述常见流操作的高级语言API（比如 joining, filtering以及aggregating records），这使得开发者可以快速开发强大的流处理应用程序。Kafka Streams提供了状态和无状态的处理能力，并且可以部署在很多系统之上： Kafka Streams应用程序可以运行在YARN、Mesos、Docker containers上，甚至直接嵌入到现有的Java应用程序中。</p>



<h3 id="2机架感知rack-awareness">2、机架感知(Rack Awareness)</h3>

<p>现在Kafka已经内置了机架感知以便隔离副本，这使得Kafka保证副本可以跨越到多个机架或者是可用区域，显著提高了Kafka的弹性和可用性。这个功能是由Netflix提供的。</p>



<h3 id="3消息的时间戳">3、消息的时间戳</h3>

<p>现在所有Kafka中的消息都包含了时间戳字段，这个时间就是这条消息产生的时间。这使得Kafka Streams能够处理基于事件时间的流处理；而且那些通过时间寻找消息以及那些基于事件时间戳的垃圾回收特性能为可能。</p>



<h3 id="4sasl改进">4、SASL改进</h3>

<p>　　Apache Kafka 0.9.0.0版本引入了新的安全特性，包括通过SASL支持Kerberos。Apache Kafka 0.10.0.0现在支持更多的SASL特性，包括外部授权服务器，在一台服务器上支持多种类型的SASL认证以及其他的改进。</p>



<h3 id="5显示所有支持的connectors和连接状态控制的rest-api">5、显示所有支持的Connectors和连接状态/控制的REST API</h3>

<p>　　在Kafka 0.10.0.0中，Kafka Connect得到了持续提升。在此之前，用户需要监控日志以便看到各个connectors以及他们task的状态，现在Kafka已经支持了获取的状态API这样使得监控变得更简单。同时也添加了控制相关的API，这使得用户可以在进行维护的时候停止一个connector；或者手动地重启那些失败的task。这些能够直观的在用户界面展示和管理connector目前可以在控制中心(Control Center)看到。</p>



<h3 id="6kafka-consumer-max-records">6、Kafka Consumer Max Records</h3>

<p>　　在Kafka 0.9.0.0，开发者们在新consumer上使用poll()函数的时候是几乎无法控制返回消息的条数。不过值得高兴的是，此版本的Kafka引入了max.poll.records参数，允许开发者控制返回消息的条数。</p>



<h3 id="7协议版本改进protocol-version-improvements">7、协议版本改进(Protocol Version Improvements)</h3>

<p>　　Kafka brokers现在支持返回所有支持的协议版本的请求API，这个特点的好处就是以后将允许一个客户端支持多个broker版本。</p>



<h1 id="二about-01100">二、About 0.11.0.0</h1>

<p>Apache Kafka近日推出0.11版本。这是一个里程碑式的大版本，特别是Kafka从这个版本开始支持“exactly-once”语义(下称EOS， exactly-once semantics)。本文简要介绍一下0.11版本主要的功能变更，下面中的每一项都值得专门写篇文章好好聊聊。</p>



<h3 id="一修改uncleanleaderelectionenabled默认值">一、修改unclean.leader.election.enabled默认值</h3>

<p>Kafka社区终于下定决心要把这个参数的默认值改成false，即不再允许出现unclean leader选举的情况，在正确性和高可用性之间选择了前者。如果依然要启用它，用户需要显式地在server.properties中设置这个参数=true</p>



<h3 id="二确保offsetstopicreplicationfactor参数被正确应用">二、确保offsets.topic.replication.factor参数被正确应用</h3>

<p>__consumer_offsets这个topic是Kafka自动创建的，在创建的时候如果集群broker数offsets.topic.replication.factor，原先的版本取其小者，但这会违背用户设置该参数的初衷。因此在0.11版本中这个参数会被强制遵守，如果不满足该参数设定的值，会抛出GROUP_COORDINATOR_NOT_AVAILABLE。</p>



<h3 id="三优化了对snappy压缩的支持">三、优化了对Snappy压缩的支持</h3>

<p>之前由于源代码中硬编码了block size，使得producer使用Snappy时的表现比LZ4相差很多，但其实Snappy和LZ4两者之差距不应该很大。故此0.11版本中对Snappy的默认block size做了调整。不过这一点需要详尽的性能测试报告来证明此改动是有效的。</p>



<h3 id="四消息增加头部信息header">四、消息增加头部信息(Header)</h3>

<p>Record增加了Header，每个header是一个KV存储。</p>



<h3 id="五空消费者组延时rebalance">五、空消费者组延时rebalance</h3>

<p>为了缩短多consumer首次rebalance的时间，增加了“group.initial.rebalance.delay.ms”用于设置group开启rebalance的延时时间。这段延时期间允许更多的consumer加入组，避免不必要的JoinGroup与SyncGroup之间的切换。当然凡事都是trade-off，引入这个必然带来消费延时。</p>



<h3 id="六消息格式变更">六、消息格式变更</h3>

<p>增加最新的magic值：2，还增加了header信息。同时为了支持幂等producer和EOS，增加一些与事务相关的字段，使得单个record数据结构体积增加。但因为优化了RecordBatch使得整个batch所占体积反而减少，进一步降低了网络IO开销。</p>



<h3 id="七新的分配算法stickyassignor">七、新的分配算法：StickyAssignor</h3>

<p>比range和round-robin更加平衡的分配算法。指定partition.assignment.strategy = org.apache.kafka.clients.consumer.StickyAssignor可以尝尝鲜。不过根据我的经验，分配不均匀的情况通常发生在每个consumer订阅topic差别很大的时候。比如consumer1订阅topic1, topic2, topic4, consumer2订阅topic3, topic4这种情况</p>



<h3 id="八controller重设计">八、controller重设计</h3>

<p>Controller原来的设计非常复杂，使得社区里面的人几乎不敢改动controller代码。老版本controller的主要问题在我看来有2个：1.  controller需要执行1，2，3，4，5，6步操作，倘若第3步出错了，无法回滚前两步的操作；2. 多线程访问，多个线程同时访问Controller上下文信息。0.11版本部分重构了controller，采用了单线程+基于事件队列的方式。具体效果咱们拭目以待吧~~</p>



<h3 id="九支持eos">九、支持EOS</h3>

<p>0.11最重要的功能，没有之一！EOS是流式处理实现正确性的基石。主流的流式处理框架基本都支持EOS（如Storm Trident, Spark Streaming, Flink），Kafka streams肯定也要支持的。0.11版本通过3个大的改动支持EOS：1.幂等的producer（这也是千呼万唤始出来的功能）；2. 支持事务；3. 支持EOS的流式处理(保证读-处理-写全链路的EOS)</p>

<p>来源：weixin <br>
作者：huxihx <br>
原文链接：<a href="https://mp.weixin.qq.com/s/e0IPpM4QbPZNzt8zMJe7DQ" rel="nofollow">https://mp.weixin.qq.com/s/e0IPpM4QbPZNzt8zMJe7DQ</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>