---
layout:     post
title:      Apache Kafka(一)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1><span>Kafka讲解</span></h1>
<h2><span>介绍</span></h2>
<span>kafka是一个分布式的，分区的，可备份的日志提交服务。它提供了消息系统的功能，但是设计确实独一无二。<br>
这些意味着什么呢？<br>
首先我们介绍一些术语：<br>
1. Kafka获取的消息在类型上叫做topics<br>
2. 我们把生产消息到kafka的进程叫做producer（生产者）<br>
3. 我们称订阅topic并且处理kafka获得的消息的进程叫做consumer（消费者）<br>
4. kafka作为一个集群来运行，这个集群有一个或多个服务器组成，这些服务器每一个都被称为broker<br></span>
<p><span>所以从一个高的角度来讲，生产者生产数据通过网络到kafka集群上，为consumer准备数据，过程如下图所示：</span></p>
<p><img src="https://img-blog.csdn.net/20160805135404284?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<span><br>
在客户端与服务端之间的通信是使用一个简单、高可靠、与语言无关的TCP协议。我们对于kafka提供了Java的客户端。但是对于其他的语言也有相应的客户端。<br>
Topics和Logs<br>
我们首先研究kafka提供的高级抽象-topics<br>
topics是一个种类或者说消息发布的一个名字。对于每个topic来说，kafka集群维持一个分布式的日志如下：<br><img src="https://img-blog.csdn.net/20160805135420003?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span>
<p><span>每一个分区都是一个有序的、可变的消息序列，该序列被持续的加入提交的日志。在分区的中的消息每个都被分配一个有序的id号码，这个号码被称为offset，在一个分区中</span></p>
<p><span>与其他消息的id都是不同的。</span></p>
<p><span>Kafka集群会保留所有提交的消息-不管他们是否被消费-在你配置的这段时间内。比如，你把日志保留时间设置为两天，那么在这两天当该消息被发布之后，他是可以被消费</span></p>
<p><span>的，时间过去了两天之后，他将会被丢弃用来释放空间。Kafka的性能与数据的大小是不相关的，所以保留很大量的数据是没有任何问题的。</span></p>
<p><span>事实上在每个消费者基本信息中保存的都是消费者在log中的位置，称作：offset。这个offset被消费者所控制：一般情况下，消费者呈直线的增加他的offset，但是事实上，</span></p>
<p><span>offset是由消费者控制的，它能够消费数据以任何他想要的顺序。比如说，消费者可以重新获得一个更古老的位移来重新处理。</span></p>
<p><span>这种结合起来的特性意味着kafka消费者十分的cheap（我不知道这种情况下如何翻译），他们能够在集群或者其他的消费者上来去自如，并不会对后者产生多少影响。比如</span></p>
<p><span>说，你可以使用我们的命令行工具去tail任何topic的内容，而不会改变其他正在消费该topic的消费者。</span></p>
<p><span>日志服务中的分区有很多作用。第一：它允许日志超越一个单独服务器上的大小范围。每一个分区必须与包含它的服务器相匹配，但是一个topic可以含有很多分区，所以他</span></p>
<p><span>可以包含任意数量的数据。第二：他们运作起来像是一个平行单元，更多内容在下面章节。</span></p>
<h2><span>分布式</span></h2>
<span>日志的分区被分布在kafka集群上的服务器之中，每个服务器都处理数据，并且请求共享的分区。每个分区都根据配置进行备份从而进行容错处理。<br></span>
<p><span>每个分区有一个服务器，该服务器扮演着leader（主）的身份，其余的机器则扮演者follower（从）的身份。主服务器处理所有关于分区的读和写的请求，从服务器被动的复</span></p>
<p><span>制主的操作。如果主服务器挂了，从服务器中的一个将会自动成为新的主服务器。每个服务器对于他自己的分区来说扮演着主的角色，而对于其他的分区则扮演着从的身份，所</span></p>
<p><span>以在集群中load操作能够得到平衡。</span></p>
<h2><span>生产者</span></h2>
<p><span>生产者生产数据到他们指定的topic中，生产者负责决策哪个消息被保存在哪个topic中的哪个分区中。这个可以用循环的方式去做仅仅是为了平衡load，同样他也可以通过语</span></p>
<p><span>意分区来完成（也就是说基于消息中的key来完成）。</span></p>
<h2><span>消费者</span></h2>
<p><span>消息发送传统上有两种方式：队列方式和发布订阅方式。在队列方式中，一个池中的消费者们也许从服务器读取数据到他们之中的一个。在发布订阅模式中，消息被广播到</span></p>
<p><span>所有的消费者中。Kafka提供了一个单独的消费者抽象来包含了以上的两者-消费者组（consumer group）。</span></p>
<p><span>消费者使用一个消费者组名来标记自己，任何发布到topic中的消息都被发送给指定的消费者组中的一个消费者实例。消费者实例可以在不同的进程中或者在不同的机器上。</span></p>
<span>如果所有的消费者实例拥有同一个消费者组，这种工作方式类似于传统的消息队列式。<br>
如果所有的消费者实例拥有不同的消费者组，这种工作方式类似于发布订阅的类型，他会把消息广播到所有的消费者中。<br></span>
<p><span>更常见的是，我们发现topics有很少数量的消费者群，每个都有“逻辑订阅者”。每个组都包含很多消费者实例为了稳定性和容错机制。这不仅仅是发布订阅模式，因为kafka</span></p>
<p><span>发布订阅模式的订阅者是一个消费者集群而不是一个单独的进程。</span></p>
<span>Kafka相较于传统消息系统有着一个很强的顺序保障。<br></span>
<p><span>一个传统的消息队列在服务器上按照顺序保存消息。如果多个消费者从队列中消费数据，那么服务器会按照数据的存储顺序分发。然而，虽然服务器按照顺序分发消息，消</span></p>
<p><span>息异步的分发给消费者，所以对于不同的消费者来说，接收到消息的顺序可能是错乱的。这也就意味着在平行消费中消息丢失了它原有的顺序。</span></p>
<span>消息系统经常变通的通过独有的消费者线程来从队列中取数据。当然这也就意味着不能平行的进行计算。<br></span>
<p><span>Kafka做的更好，他有着平行的概念-分区-在topics中。Kafka有能力为消费者线程池中的消费者提供顺序保证和load平衡。这是通过声明topic中的消费者组中的消费者的分区</span></p>
<p><span>来完成的，从而使得每个分区被组中的一个消费者消费。通过这些我们确保了该消费者是该分区的唯一一个读取者，这样就可以按照顺序消费数据了。由于有很多的分区，这样</span></p>
<p><span>的方式同样采用平衡load到多个消费者实例中。注意，在消费者组中，消费者的数量不可以多于分区的数量。</span></p>
<p><span>在一个分区内，kafka对于消息仅仅提供一个整体的顺序，而不是在topic中的多个分区间。每个分区顺序通过key组合分区数据的能力对于大多数的应用来讲已经足够了。然</span></p>
<p><span>而，如果你需要关于消息的整体顺序的话，你可以设置该topic只含有唯一一个分区。虽然这意味着在一个消费者组中只有一个消费者进程。</span></p>
<p><img src="https://img-blog.csdn.net/20160805140142187?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<span><br></span>
<h2><span>保障</span></h2>
<span>在一个高层次来讲，kafka提供了如下的保障：<br></span>
<p><span>1. 发送往特定topic下分区的消息将会按照他们发送的顺序被添加。这就是说，如果消息M1被消费者作为消息M2发送，那么M1会被首先发送，接着M1相较于M2会有一个较小</span></p>
<p><span>的offset，在log里面显现的也会更早。</span></p>
<span>2. 一个消费者实例查看消息时按照消息保存在log的顺序来查看。<br>
3. 如果一个topic含有副本元素N，那么我们能够容忍N-1的服务器挂掉却不会丢失任何的数据。<br></span>
<h2><span>Messaging（发送消息）</span></h2>
<p><span>Kafka同样可以作为传统消息broker的替代品，消息brokers因为种种原因被使用。对比大多数消息系统，kafka拥有更好的吞吐量，内置分区，备份，和容错机制，这种种使</span></p>
<p><span>得kafka是那些拥有者大规模消息处理的应用的完美解决方案。</span></p>
<span>在我们的经验中，消息系统相比较而言一般都是小吞吐量的，但是也许需要点对点的低延迟并且依赖kafka提供的较强间隔保证。<br>
在这个方面kafka可以与传统消息系统相比较，例如：ActiveMQ或者RabbitMQ。<br></span>
<h2><span>Web Activity Tracking(网页事件追踪)</span></h2>
<p><span>原始的kafka用例能够重建用户事件追踪管道为一系列实时发布订阅信息。这意味着网站事件（网页浏览、搜索或者其他活动都能够获取）被发送到中心topic中，并且是每个</span></p>
<p><span>事件类型对应着一个topic。这些信息对于订阅者来说有着许多应用包括实时处理，实时监控，读入Hadoop或者仓库系统的离线数据用来离线处理或者报告。</span></p>
<span>事件追踪通常有非常高的容量，因为从每个用户的界面都会产生许多事件消息。<br></span>
<h2><span>度量</span></h2>
<span>kafka经常用作检测数据。这包含从不同的分布式应用中聚合统计结果去产生集中后的数据。<br></span>
<h2><span>日志收集</span></h2>
<p><span>很多人使用Kafka作为日志收集系统。日志收集系统本质上来讲是各个服务器上收集物理日志文件，把他们放入一个中心位置用来处理。Kafka抽象了文件的细节并给了一个</span></p>
<p><span>更明确的抽象就是消息流。这允许了多数据源和分布式处理时的低延迟和更简易的支持。相比较于以日志文中心的系统像是Scribe和Flume，kafka提供了同样好的性能，更健壮</span></p>
<p><span>的保证、更低的端对端的延迟。</span></p>
<span>流处理<br></span>
<p><span>很多实用kafka的用户处理数据用包含很多阶段的管道方式。方便后边的消费或者接下来的处理，来自于kafka中topics的数据被消费接着被组合、加工、或者直接被转化为了</span></p>
<p><span>新的topics。比如，一个用来推荐新闻文章的处理管道可能会从RSS上爬取信息并且把他发布为一个文章topic。那么接下来的工作可能就是处理这些爬取的内容然后发布清洗后的</span></p>
<p><span>数据为一个新的topic。最后的阶段可能就是尝试把他推荐给用户。这种处理管道基于每个topic创建了一个实时数据流。在0.10.0.0上开始，一个轻量级但是强力的流处理包Kafka</span></p>
<p><span>Streams在apache kafka中可以被使用，用来处理如上所述的数据。除了kafka streams以外，其他的开源流处理工具包括apache Storm和apache Samza</span></p>
<h2><span>Event Soucing（事件溯源）</span></h2>
<span>事件溯源是一种应用设计，在这种设计中，状态改变都会被记录为基于时间排序的序列记录。Kafka支持海量数据存储的特点使得他对于这种类型的应用是非常理想的选择。<br>
Commit Log(提交日志)<br></span>
<p><span>对于一个分布式系统来说，Kafka能作为一款外部的日志提交系统来提供服务。日志帮助在节点间复制数据，其表现的像是一个re-syncing 机制主要用来对那些失败的节点重</span></p>
<p><span>新保存数据。Kafka中日志压缩的属性支持这种用法，在这种用法中，Kafka 类似于Apache BookKeeper项目。</span></p>
<p><span></span>以上是关于kafka的概念性内容，下一次我将进行关于操作部分的内容讲解。感谢开源。</p>
<span><br><br><br></span>
            </div>
                </div>