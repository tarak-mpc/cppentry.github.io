---
layout:     post
title:      Apache Kafka系列(一)----简介
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u011781521/article/details/78284769				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:24px;"><strong>一、Kafka简介</strong></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;"><strong>(1)、<span style="font-size:18px;">什么是Kafka</span></strong></span></p>
<p><span style="font-size:18px;"><span style="font-size:18px;"><br></span></span></p>
<p><span style="font-size:18px;">Apache Kafka是一个基于分布式日志提交机制设计的发布订阅系统。数据在kafka中持久化，用户可以随时按需读取。另外数据以分布式的方式存储，提高容错性，易于扩展<br></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;"><strong>(2)、产生背景</strong></span></p>
<p><span style="font-size:18px;"><br>
    Kafka是一个消息系统，用作LinkedIn的活动流（Activity Stream）和运营数据处理管道（Pipeline）的基础。活动流数据是几乎所有站点在对其网站使用情况做报表时都要用到的数据中最常规的部分。活动数据包括页面访问量（Page View）、被查看内容方面的信息以及搜索情况等内容。这种数据通常的处理方式是先把各种活动以日志的形式写入某种文件，然后周期性地对这些文件进行统计分析。运营数据指的3是服务器的性能数据（CPU、IO使用率、请求时间、服务日志等等数据)。运营数据的统计方法种类繁多。</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:24px;"><strong>二、Kafka的特点</strong></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">(1)、同时为发布和订阅提供高吞吐量。据了解，Kafka每秒可以生产约25万消息（50 MB），每秒处理55万消息（110 MB）。</span></p>
<p><span style="font-size:18px;"><br>
(2)、可进行持久化操作。将消息持久化到磁盘，因此可用于批量消费，例如ETL，以及实时应用程序。通过将数据持久化到硬盘以及replication防止数据丢失。</span></p>
<p><span style="font-size:18px;"><br>
(3)、分布式系统，易于向外扩展。所有的producer、broker和consumer都会有多个，均为分布式的。无需停机即可扩展机器。</span></p>
<p><span style="font-size:18px;"><br>
(4)、消息被处理的状态是在consumer端维护，而不是由server端维护。当失败时能自动平衡。<br><br></span></p>
<p><span style="font-size:18px;">(5)、支持online和offline的场景。<br></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:24px;"><strong>三、Kafka的架构</strong></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20171019145947072" alt=""><br></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">一个典型的Kafka集群中包含若干Producer（可以是web前端产生的Page View，或者是服务器日志，系统CPU、Memory等），若干broker（Kafka支持水平扩展，一般broker数量越多，集群吞吐率越高），若干Consumer Group，以及一个Zookeeper集群。Kafka通过Zookeeper管理集群配置，选举leader，以及在Consumer Group发生变化时进行rebalance。Producer使用push模式将消息发布到broker，Consumer使用pull模式从broker订阅并消费消息。<br></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20171019150022306" alt=""><br></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">Kafka的整体架构非常简单，是显式分布式架构，producer、broker（kafka）和consumer都可以有多个。Producer，consumer实现Kafka注册的接口，数据从producer发送到broker，broker承担一个中间缓存和分发的作用。broker分发注册到系统中的consumer。broker的作用类似于缓存，即活跃的数据和离线处理系统之间的缓存。客户端和服务器端的通信，是基于简单，高性能，且与编程语言无关的TCP协议。几个基本概念：<br><br>
1、Topic：特指Kafka处理的消息源（feeds of messages）的不同分类。<br>
2、Partition：Topic物理上的分组，一个topic可以分为多个partition，每个partition是一个有序的队列。partition中的每条消息都会被分配一个有序的id（offset）。<br>
3、Message：消息，是通信的基本单位，每个producer可以向一个topic（主题）发布一些消息。<br>
4、Producers：消息和数据生产者，向Kafka的一个topic发布消息的过程叫做producers。<br>
5、Consumers：消息和数据消费者，订阅topics并处理其发布的消息的过程叫做consumers。<br>
6、Broker：缓存代理，Kafa集群中的一台或多台服务器统称为broker。<br></span></p>
<p><br></p>
<p><span style="font-size:24px;"><strong>四、Kafka相关概念</strong></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">1.producer：<br>
　　消息生产者，发布消息到 kafka 集群的终端或服务。</span></p>
<p><span style="font-size:18px;"><br>
2.broker：<br>
　　kafka 集群中包含的服务器。</span></p>
<p><span style="font-size:18px;"><br>
3.topic：<br>
　　每条发布到 kafka 集群的消息属于的类别，即 kafka 是面向 topic 的。</span></p>
<p><span style="font-size:18px;"><br>
4.partition：<br>
　　partition 是物理上的概念，每个 topic 包含一个或多个 partition。kafka 分配的单位是 partition。</span></p>
<p><span style="font-size:18px;"><br>
5.consumer：<br>
　　从 kafka 集群中消费消息的终端或服务。</span></p>
<p><span style="font-size:18px;"><br>
6.Consumer group：<br>
　　high-level consumer API 中，每个 consumer 都属于一个 consumer group，每条消息只能被 consumer group 中的一个 Consumer 消费，但可以被多个 consumer group 消费。</span></p>
<p><span style="font-size:18px;"><br>
7.replica：<br>
　　partition 的副本，保障 partition 的高可用。</span></p>
<p><span style="font-size:18px;"><br>
8.leader：<br>
　　replica 中的一个角色， producer 和 consumer 只跟 leader 交互。</span></p>
<p><span style="font-size:18px;"><br>
9.follower：<br>
　　replica 中的一个角色，从 leader 中复制数据。</span></p>
<p><span style="font-size:18px;"><br>
10.controller：<br>
　　kafka 集群中的其中一个服务器，用来进行 leader election 以及 各种 failover。</span></p>
<p><span style="font-size:18px;"><br>
12.zookeeper：<br>
　　kafka 通过 zookeeper 来存储集群的 meta 信息。<br></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:24px;"><strong>五、消息发送的流程</strong></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20171019150557070" alt=""><br></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">(1)、Producer根据指定的partition方法（round-robin、hash等），将消息发布到指定topic的partition里面。</span></p>
<p><span style="font-size:18px;"><br>
(2)、kafka集群接收到Producer发过来的消息后，将其持久化到硬盘，并保留消息指定时长（可配置），而不关注消息是否被消费。</span></p>
<p><span style="font-size:18px;"><br>
(3)、Consumer从kafka集群pull数据，并控制获取消息的offset。<br></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:24px;"><strong>六、KafKa原理</strong></span></p>
<p><br></p>
<p><span style="font-size:18px;"><strong>(1)、话题和日志 (Topic和Log)</strong></span></p>
<p><span style="font-size:18px;"><br>
每条发布到Kafka集群的消息都有一个类别，这个类别被称为Topic。对于每一个Topic，Kafka集群维护这一个分区的log，就像下图中的示例：</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20171019151710693" alt=""><br><br></span></p>
<p><span style="font-size:18px;">每一个分区都是一个顺序的、不可变的消息队列， 并且可以持续的添加。分区中的消息都被分了一个序列号，称之为偏移量(offset)，在每个分区中此偏移量都是唯一的。<br><br>
Kafka集群保持所有的消息，直到它们过期， 无论消息是否被消费了。 实际上消费者所持有的仅有的元数据就是这个偏移量，也就是消费者在这个log中的位置。 这个偏移量由消费者控制：正常情况当消费者消费消息的时候，偏移量也线性的的增加。但是实际偏移量由消费者控制，消费者可以将偏移量重置为更老的一个偏移量，重新读取消息。 可以看到这种设计对消费者来说操作自如， 一个消费者的操作不会影响其它消费者对此log的处理。 再说说分区。</span></p>
<p><span style="font-size:18px;">Kafka中采用分区的设计有几个目的。一是可以处理更多的消息，不受单台服务器的限制。Topic拥有多个分区意味着它可以不受限的处理更多的数据。第二，分区可以作为并行处理的单元，</span><br><br><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20171019152746737" alt=""></span><br><span style="font-size:18px;"><strong>(2)、Topics和Partitions(主题和分区)<br></strong></span><br><span style="font-size:18px;">消息都是以主题Topic的方式组织在一起，Topic也可以理解成传统数据库里的表，或者文件系统里的一个目录。一个主题由broker上的一个或者多个Partition分区组成。在Kafka中数据是以Log的方式存储，一个partition就是一个单独的Log。消息通过追加的方式写入日志文件，读取的时候则是从头开始按照顺序读取。注意，一个主题通常都是由多个分区组成的，每个分区内部保证消息的顺序行，分区之间是不保证顺序的。如果你想要kafka中的数据按照时间的先后顺序进行存储，那么可以设置分区数为1。如下图所示，一个主题由4个分区组成，数据都以追加的方式写入这四个文件。分区的方式为Kafka提供了良好的扩展性，每个分区都可以放在独立的服务器上，这样就相当于主题可以在多个机器间水平扩展，相对于单独的服务器，性能更好。</span><br><br><br><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20171019153153002" alt=""></span><br><br><span style="font-size:18px;">在Kafka这种数据系统中经常会提起stream流这个词，通常流被认为是一个主题中的数据，而忽略分区的概念。这就意味着数据流就是从producer到consumer。在很多框架中，比如kafka stream,apache samza,storm在操作实时数据的时候，都是这样理解数据流的。这种操作的模式跟离线系统处理数据的方式不同，如hadoop，是在某一个固定的时间处理一批的数据。</span><br><br><br><br><span style="font-size:18px;"><strong>(3)、分布式(Distribution)<br></strong></span><br><span style="font-size:18px;">Log的分区被分布到集群中的多个服务器上。每个服务器处理它分到的分区。 根据配置每个分区还可以复制到其它服务器作为备份容错。 每个分区有一个leader，零或多个follower。Leader处理此分区的所有的读写请求，而follower被动的复制数据。如果leader宕机，其它的一个follower会被推举为新的leader。 一台服务器可能同时是一个分区的leader，另一个分区的follower。 这样可以平衡负载，避免所有的请求都只让一台或者某几台服务器处理。</span><br><br><br><span style="font-size:18px;"><strong>(4)、生产者(Producers)<br></strong></span><br><span style="font-size:18px;">生产者往某个Topic上发布消息。生产者也负责选择发布到Topic上的哪一个分区。最简单的方式从分区列表中轮流选择。也可以根据某种算法依照权重选择分区。开发者负责如何选择分区的算法。</span><br><br><span style="font-size:18px;">Producer用来创建消息。在发布订阅系统中，他们也被叫做Publisher发布者或writer写作者。通常情况下，消息都会进入特定的主题。默认情况下，生产者不关系消息到底进入哪个分区，它会自动在多个分区间负载均衡。也有的时候，消息会进入特定的一个分区中。一般都是通过消息的key使用哈希的方式确定它进入哪一个分区。这就意味着如果所有的消息都给定相同的key，那么他们最终会进入同一个分区。生产者也可以使用自定义的分区器，这样消息可以进入特定的分区。<br></span><br><br><span style="font-size:18px;"><strong>(5)、消费者(Consumers)<br></strong></span></p>
<p><br></p>
<p><span style="font-size:18px;">通常来讲，消息模型可以分为两种， <span style="color:#ff0000;">队列和发布-订阅式</span>。 队列的处理方式是 一组消费者从服务器读取消息，一条消息只有其中的一个消费者来处理。在发布-订阅模型中，消息被广播给所有的消费者，接收到消息的消费者都可以处理此消息。Kafka为这两种模型提供了单一的消费者抽象模型： 消费者组 （consumer group）。 消费者用一个消费者组名标记自己。 一个发布在Topic上消息被分发给此消费者组中的一个消费者。
 假如所有的消费者都在一个组中，那么这就变成了queue模型。 假如所有的消费者都在不同的组中，那么就完全变成了发布-订阅模型。 更通用的， 我们可以创建一些消费者组作为逻辑上的订阅者。每个组包含数目不等的消费者， 一个组内多个消费者可以用来扩展性能和容错。正如下图所示：</span><br></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20171020095547175" alt=""><br></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;"><br>
2个kafka集群托管4个分区（P0-P3），2个消费者组，消费组A有2个消费者实例，消费组B有4个。<br><br>
正像传统的消息系统一样，Kafka保证消息的顺序不变。 再详细扯几句。传统的队列模型保持消息，并且保证它们的先后顺序不变。但是， 尽管服务器保证了消息的顺序，消息还是异步的发送给各个消费者，消费者收到消息的先后顺序不能保证了。这也意味着并行消费将不能保证消息的先后顺序。用过传统的消息系统的同学肯定清楚，消息的顺序处理很让人头痛。如果只让一个消费者处理消息，又违背了并行处理的初衷。 在这一点上Kafka做的更好，尽管并没有完全解决上述问题。 Kafka采用了一种分而治之的策略：分区。 因为Topic分区中消息只能由消费者组中的唯一一个消费者处理，所以消息肯定是按照先后顺序进行处理的。但是它也仅仅是保证Topic的一个分区顺序处理，不能保证跨分区的消息先后处理顺序。
 所以，如果你想要顺序的处理Topic的所有消息，那就只提供一个分区。<br></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">Consumer读取消息。在发布订阅系统中，也叫做subscriber订阅者或者reader阅读者。消费者订阅一个或者多个主题，然后按照顺序读取主题中的数据。消费者需要记录已经读取到消息的位置，这个位置也被叫做offset。每个消息在给定的分区中只有唯一固定的offset。通过存储最后消费的Offset，消费者应用在重启或者停止之后，还可以继续从之前的位置读取。保存的机制可以是zookeeper,或者kafka自己。<br><br>
消费者是以consumer group消费者组的方式工作，由一个或者多个消费者组成一个组，共同消费一个topic。每个分区在同一时间只能由group中的一个消费者读取，在下图中，有一个由三个消费者组成的grouop，有一个消费者读取主题中的两个分区，另外两个分别读取一个分区。某个消费者读取某个分区，也可以叫做某个消费者是某个分区的拥有者。<br><br>
在这种情况下，消费者可以通过水平扩展的方式同时读取大量的消息。另外，如果一个消费者失败了，那么其他的group成员会自动负载均衡读取之前失败的消费者读取的分区。<br></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20171020100254328" alt=""><br></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;"><strong>(6)、Brokers和Clusters</strong></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">单独的kafka服务器也叫做broker，Broker从生产者那里获取消息，分配offset，然后提交存储到磁盘年。他也会提供消费者，让消费者读取分区上的消息，并把存储的消息传给消费者。依赖于一些精简资源，单独的broker也可以轻松的支持每秒数千个分区和百万级的消息。<br><br>
Kafka的broker支持集群模式，在Broker组成的集群中，有一个节点也被叫做控制器（是在活跃的节点中自动选择的）。这个controller控制器负责管理整个集群的操作，包括分区的分配、失败节点的检测等。一个partition只能出现在一个broker节点上，并且这个Broker也被叫做分区的leader。一个分区可以分配多个Broker，这样可以做到多个机器之间备份的效果。这种多机备份在其中一个broker失败的时候，可以自动选举出其他的broker提供服务。然而，producer和consumer都必须连接leader才能正常工作。<br></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20171020100411834" alt=""><br></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">Kafka的一个重要特性就是支持数据的过期删除，数据可以在Broker上保留一段时间。Kafka的broker支持针对topic设置保存的机制，可以按照大小配置也可以按照时间配置。一旦达到其中的一个限制，可能是时间过期也可能是大小超过配置的数值，那么这部分的数据都会被清除掉。每个topic都可以配置它自己的过期配置，因此消息可以按照业务的需要进行持久化保留。比如，一个数据追踪分析的topic可以保留几天时间，一些应用的指标信息则只需要保存几个小时。topic支持日志数据的压缩，这样kafka仅仅会保留最后一条日志生成的key。这在修改日志类型的时候会非常有用。<br></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;"><strong>(7)、Multiple Cluster</strong></span></p>
<p><span style="font-size:18px;"><br>
随着Kafka部署环境的演变，有时候需要莉利用多集群的优势。使用多集群的原因如下：</span></p>
<p><span style="font-size:18px;"><br>
1 不同类型数据的分离<br>
2 安全隔离<br>
3 多数据中心（灾备）</span></p>
<p><span style="font-size:18px;"><br>
在使用多数据中心的时候，需要很清楚的理解消息是如何在她们之间传递的。一般情况下，用户可以在多个对外提供服务的网址，产生一些前端数据，然后利用kafka把他们统一的汇总到一起，进行分析监控告警。这种备份的机制一般都是应用于单个集群，而不是多集群。<br><br>
Kafka项目提供了一个叫做mirro maker的工具，它支持多机房的数据传输。它其实就是一个基于queu连接的consumer和producer。消息从kafka中消费，然后传输给另一个集群的kafka。如下图所示，就是使用mirror maker的一个例子，消息在两个集群的本地聚合，然后再传输给另一个集群进行分析。由于kafka设计的精简，可以在多机房中实现这种简单的管道处理机制。</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20171020100549603" alt=""><br></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">内容整理自以下文章:</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">http://orchome.com/5<br></span></p>
<p><span style="font-size:18px;">http://www.aboutyun.com/thread-21652-1-1.html<br></span></p>
<p><span style="font-size:18px;">http://www.cnblogs.com/cyfonly/p/5954614.html<br></span></p>
<p><span style="font-size:18px;"><br></span></p>
            </div>
                </div>