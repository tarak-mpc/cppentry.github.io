---
layout:     post
title:      kafka集群Broker端参数设置及调优准则建议-kafka 商业环境实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本套技术专栏是作者（秦凯新）平时工作的总结和升华，通过从真实商业环境抽取案例进行总结和分享，并给出商业应用的调优建议和集群环境容量规划等内容，请持续关注本套博客。版权声明：禁止转载，欢迎学习。QQ邮箱地址：1120746959@qq.com，如有任何商业交流，可随时联系。					https://blog.csdn.net/shenshouniu/article/details/83957355				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>本套系列博客从真实商业环境抽取案例进行总结和分享，并给出Spark商业应用实战指导，请持续关注本套博客。版权声明：本套Spark商业应用实战归作者（秦凯新）所有，禁止转载，欢迎学习。</p>
<ul>
<li><a href="https://blog.csdn.net/shenshouniu/article/details/83719129" rel="nofollow">秦凯新的技术社区-大数据商业实战系列全集目录  </a></li>
<li><a href="https://juejin.im/post/5bd464ccf265da0ac3735124" rel="nofollow">kafka 商业环境实战-kafka生产环境规划 </a></li>
<li><a href="https://juejin.im/post/5bd50b9451882529642b2581" rel="nofollow">kafka 商业环境实战-kafka生产者和消费者吞吐量测试 </a></li>
<li><a href="https://juejin.im/post/5bd51b32e51d457947024771" rel="nofollow">kafka 商业环境实战-kafka生产者Producer参数设置及参数调优建议  </a></li>
<li><a href="https://juejin.im/post/5be799f851882558512137f7" rel="nofollow">kafka 商业环境实战-kafka集群Broker端参数设置及调优准则建议</a></li>
<li><a href="https://juejin.im/post/5be7ded25188253b6e5c085c" rel="nofollow">kafka 商业环境实战-kafka之Producer同步与异步消息发送官方案例应用实战</a></li>
<li><a href="https://juejin.im/post/5be7e0556fb9a049a62c1792" rel="nofollow">kafka 商业环境实战-kafka之Consumer多种消费模式官方案例应用实战</a></li>
</ul>
<h2><a id="1__Distributed_streaming_platform_11"></a>1  Distributed streaming platform</h2>
<pre><code>Apache Kafka® is a distributed streaming platform. What exactly does that mean?
A streaming platform has three key capabilities:
   -  Publish and subscribe to streams of records, similar to a message queue or enterprise messaging system.
   -  Store streams of records in a fault-tolerant durable way.
   -  Process streams of records as they occur.

Kafka is generally used for two broad classes of applications:
- Building real-time streaming data pipelines that reliably get data between systems or applications
- Building real-time streaming applications that transform or react to the streams of data

To understand how Kafka does these things, let's dive in and explore Kafka's capabilities from the bottom up.
First a few concepts:

- Kafka is run as a cluster on one or more servers that can span multiple datacenters.
- The Kafka cluster stores streams of records in categories called topics.
- Each record consists of a key, a value, and a timestamp.
</code></pre>
<h2><a id="2_Kafka_as_a_Storage_System_30"></a>2 Kafka as a Storage System</h2>
<pre><code>Any message queue that allows publishing messages decoupled from consuming them 
is effectively acting as a storage system for the in-flight messages. What is 
different about Kafka is that it is a very good storage system.

- Data written to Kafka is written to disk and replicated for fault-tolerance. 
Kafka allows producers to wait on acknowledgement so that a write isn't considered
complete until it is fully replicated and guaranteed to persist even if the server 
written to fails.

- The disk structures Kafka uses scale well，Kafka will perform the same whether you 
have 50 KB or 50 TB of persistent data on the server.

- As a result of taking storage seriously and allowing the clients to control 
their read position, you can think of Kafka as a kind of special purpose 
distributed filesystem dedicated to high-performance, low-latency commit 
log storage, replication, and propagation.
</code></pre>
<h2><a id="3_kafka_49"></a>3 kafka实现高吞吐率的秘密</h2>
<ul>
<li>一个用户程序要把文件内容发送到网络这个用户程序是工作在用户空间，文件和网络socket属于硬件资源，两者之间有一个内核空间。因此在操作系统内部，整个过程为：<br>
<img src="https://user-gold-cdn.xitu.io/2018/11/11/16700d9a29e96786?w=497&amp;h=374&amp;f=png&amp;s=26634" alt=""></li>
</ul>
<p>在Linux kernel2.2 之后出现了一种叫做"零拷贝(zero-copy)"系统调用机制，就是跳过“用户缓冲区”的拷贝，建立一个磁盘空间和内存的直接映射，数据不再复制到“用户态缓冲区”</p>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/11/16700dc01a895659?w=476&amp;h=342&amp;f=png&amp;s=19965" alt=" "></p>
<ul>
<li>kafka的队列topic被分为了多个区partition，每个partition又分为多个段segment，所以一个队列中的消息实际上是保存在N多个片段文件中，通过分段的方式，每次文件操作都是对一个小文件的操作，增加了并行处理能力</li>
</ul>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/11/16700dcf0bbb1f06?w=630&amp;h=300&amp;f=png&amp;s=24617" alt=""></p>
<ul>
<li>kafka允许进行批量发送消息，先将消息缓存在内存中，然后通过一次请求批量把消息发送出去，比如：可以指定缓存的消息达到某个量的时候就发出去，或者缓存了固定的时间后就发送出去，如100条消息就发送，或者每5秒发送一次这种策略将大大减少服务端的I/O次数。</li>
<li>kafka还支持对消息集合进行压缩，Producer可以通过GZIP或Snappy格式或LZ4对消息集合进行压缩,压缩的好处就是减少传输的数据量，减轻对网络传输的压力。</li>
</ul>
<h2><a id="4_kafkaBroker_66"></a>4 kafka集群Broker端全局参数设置</h2>
<h2><a id="httpsusergoldcdnxituio2018111116701be5d47d9925w1056h378fpngs293194_68"></a><img src="https://user-gold-cdn.xitu.io/2018/11/11/16701be5d47d9925?w=1056&amp;h=378&amp;f=png&amp;s=293194" alt=""></h2>
<ul>
<li>broker. id</li>
</ul>
<p>唯一的整数来标识每个broker，不能与其他broker冲突，建议从0开始。</p>
<hr>
<ul>
<li>log.dirs    &lt;= 吞吐量提升</li>
</ul>
<p>确保该目录有比较大的硬盘空间。如果需要指定多个目录，以逗号分隔即可，比如/xin/kafka1,/xin/kafka2。这样做的好处是Kafka会力求均匀地在多个目录下存放分区(partition)数据。如果挂载多块磁盘，那么会有多个磁头同时执行写操作。对吞吐量具有非常强的提升。</p>
<hr>
<ul>
<li>zookeeper.connect</li>
</ul>
<p>该参数则完全没有默认值，必须要配置。这个参数也可以是一个逗号分隔值的列表，比如zk1:2181,zk2:2181,zk3:2181/kafka。注意结尾的/kafka，它是zookeeper的chroot，是可选的配置，如果不指定的话就默认使用zookeeper的根路径。</p>
<hr>
<ul>
<li>listeners</li>
</ul>
<p>协议配置包括PLAINTEXT，SSL, SASL_SSL等，格式是[协议]😕/[主机名]:[端口],[[协议]😕/[主机名]:[端口]]，该参数是Brocker端开发给clients的监听端口。建议配置：</p>
<pre><code>    PLAINTEXT://hostname:port（未启用安全认证）
    SSL://hostname:port（启用安全认证）
</code></pre>
<hr>
<ul>
<li>unclean.leader.election.enable  &lt;= 数据的完整性保证</li>
</ul>
<p>解决ISR所有副本为空，leader又出现宕机的情况。此时leader该如何选择呢？截止kafka 1.0.0版本，该参数默认为false，表示不允许选择非ISR副本集之外的broker。因为高可用性与数据的完整性，kafka官方选择了后者。</p>
<hr>
<ul>
<li>delete.topic.enable</li>
</ul>
<p>不多说，是否允许删除topic，鉴于0.9.0.0新增了ACL机制权限机制，误操作基本是不存在的。</p>
<hr>
<ul>
<li>log.retention.{hours|minutes|ms}  &lt;=时间维度</li>
</ul>
<p>优先选取ms的配置，minutes次之，hours最后，默认留存机制是7天。如何判断：</p>
<p>新版本：基于消息中的时间戳来进行判断。<br>
老版本：根据日志文件的最新修改时间进行比较.</p>
<hr>
<ul>
<li>log.retention.bytes  &lt;=空间维度</li>
</ul>
<p>Kafka会定期删除那些大小超过该参数值的日志文件。默认值是-1，表示Kafka永远不会根据大小来删除日志</p>
<hr>
<ul>
<li>min.insync.replicas &lt;=     与acks=-1 搭配使用</li>
</ul>
<p>持久化级别，用于最少需要多少副本同步。在acks=all(或-1) 时才有意义。min.insync.replicas指定了必须要应答写请求的最小数量的副本数。如果不能满足，producer将会抛出NotEnoughReplicas或NotEnoughReplicasAfterAppend异常。该参数用于实现更好的消息持久性。</p>
<p>举例如下：</p>
<p>5台broker  ack =-1  min.insync.replicas = 3</p>
<p>上述表示最少需要3个副本同步后，Broker才能够对外提供服务,否则将会抛出异常。若3台Broker宕机，即使剩余2台全部同步结束，满足了 ack =-1也要报错。</p>
<hr>
<ul>
<li>num.network.threads  &lt;= 请求转发线程数量</li>
</ul>
<p>默认值为3，主要负责转发来自broker和clients发送过来的各种请求。强调这里只是转发，真实环境下，需要监听<br>
NetWorkerProcessorAvgIdlePercent JMX指标，若指标低于0.3，则建议调高该值。</p>
<hr>
<ul>
<li>num.io.threads  &lt;= 实际处理线程数量</li>
</ul>
<p>默认是8，也即broker后端有8个线程以轮询的方式不停的监听转发过来的网络请求并进行实时处理。</p>
<hr>
<ul>
<li>message.max.bytes</li>
</ul>
<p>broker能够接收的最大消息大小，默认是977KB。因此注意，生产环境应该调高该值。</p>
<h2><a id="4_kafkaBrokerTopic_143"></a>4 kafka集群Broker端Topic级别参数设置</h2>
<ul>
<li>delete.topic.enable</li>
<li>message.max.bytes</li>
<li>log.retention.bytes</li>
</ul>
<h2><a id="5__149"></a>5 操作系统参数设置</h2>
<ul>
<li>OS页缓存刷盘flush时间  &lt;= 提升吞吐量</li>
</ul>
<p>默认是5秒，间隔实在太短，适当增加该值可以很高的提行OS物理写入操作的性能。LinkedIn设置为2分钟来提升吞吐量。</p>
<ul>
<li>文件系统选择</li>
</ul>
<p>官方测试XFS文件系统写入时间为160秒，Ext4大约是250毫秒。建议生产环境最好使用XFS文件系统。</p>
<ul>
<li>OS文件描述符限制</li>
</ul>
<p>OS系统最大打开的文件描述符是有上限的，举例：一个kafka集群主要有3个副本，50个分区，若每一个分区文件大小为10G，而分区内日志段大小为1GB，则一个Broker需要维护1500个左右的文件描述符。因此根据需要设置：</p>
<pre><code>ulimit -n 100000
</code></pre>
<ul>
<li>OS 操作系统缓冲区设置 （尚不确定）</li>
</ul>
<h2><a id="_166"></a>总结</h2>
<p>本文立足于Broker进行参数详细讲解，有问题，欢迎留言。</p>
<p>秦凯新  于深圳 2018</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>