---
layout:     post
title:      Kafka从入门到精通系列课第三章 kafka基础整理
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：所有内容原创，如需转载，请声明					https://blog.csdn.net/lovedopa/article/details/82823942				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>Kafka拓扑结构</p>

<p><img alt="è¿éåå¾çæè¿°" class="has" src="https://img-blog.csdn.net/20180124145530004?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmd5dXFpYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast"><br>
1.producer： <br>
　　消息生产者，发布消息到 kafka 集群的终端或服务。 <br>
2.broker： <br>
　　kafka 集群中包含的服务器。 <br>
3.topic： <br>
　　每条发布到 kafka 集群的消息属于的类别，即 kafka 是面向 topic 的。 <br>
4.partition： <br>
　　partition 是物理上的概念，每个 topic 包含一个或多个 partition。kafka 分配的单位是 partition。 <br>
5.consumer： <br>
　　从 kafka 集群中消费消息的终端或服务。 <br>
6.Consumer group： <br>
　　high-level consumer API 中，每个 consumer 都属于一个 consumer group，每条消息只能被 consumer group 中的一个 Consumer 消费，但可以被多个 consumer group 消费。 <br>
7.replica： <br>
　　partition 的副本，保障 partition 的高可用。 <br>
8.leader： <br>
　　replica 中的一个角色， producer 和 consumer 只跟 leader 交互。 <br>
9.follower： <br>
　　replica 中的一个角色，从 leader 中复制数据。 <br>
10.controller： <br>
　　kafka 集群中的其中一个服务器，用来进行 leader election 以及 各种 failover。 <br>
12.zookeeper： <br>
　　kafka 通过 zookeeper 来存储集群的 meta 信息。 <br>
　　</p>

<p>kafka 在 zookeeper 中的存储结构</p>

<p><img alt="è¿éåå¾çæè¿°" class="has" src="https://img-blog.csdn.net/20180124145631158?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmd5dXFpYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast"><br>
Consumer Group<br>
同一Topic的一条消息只能被同一个Consumer Group内的一个Consumer消费，但多个Consumer Group可同时消费这一消息。 <br>
一个Topic可以对应多个Consumer Group。如果需要实现广播，只要每个Consumer有一个独立的Group就可以了。要实现单播只要所有的Consumer在同一个Group里。用Consumer Group还可以将Consumer进行自由的分组而不需要多次发送消息到不同的Topic。 </p>

<p><img alt="è¿éåå¾çæè¿°" class="has" src="https://img-blog.csdn.net/20180124145953233?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmd5dXFpYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast"><br>
Kafka的设计理念之一就是同时提供离线处理和实时处理。根据这一特性，可以使用Storm这种实时流处理系统对消息进行实时在线处理，同时使用Hadoop这种批处理系统进行离线处理，还可以同时将数据实时备份到另一个数据中心，只需要保证这三个操作所使用的Consumer属于不同的Consumer Group即可。</p>

<p>消费方式<br>
Push vs. Pull <br>
作为一个消息系统，Kafka遵循了传统的方式，选择由Producer向broker push消息并由Consumer从broker pull消息。</p>

<p>consumer 采用 pull 模式从 broker 中读取数据。</p>

<p>push 模式很难适应消费速率不同的消费者，因为消息发送速率是由 broker 决定的。它的目标是尽可能以最快速度传递消息，但是这样很容易造成 consumer 来不及处理消息，典型的表现就是拒绝服务以及网络拥塞。而 pull 模式则可以根据 consumer 的消费能力以适当的速率消费消息。</p>

<p>对于 Kafka 而言，pull 模式更合适，它可简化 broker 的设计，consumer 可自主控制消费消息的速率，同时 consumer 可以自己控制消费方式——即可批量消费也可逐条消费，同时还能选择不同的提交方式从而实现不同的传输语义。</p>

<p> </p>            </div>
                </div>