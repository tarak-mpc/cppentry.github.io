---
layout:     post
title:      Kafka的副本策略——《Learning Apache Kafka》学习笔记（原书第三章第4节）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>在学习Kafka的副本策略之前，首先回顾一下Kafkad的消息分片策略。</p>
<p>在Kafka中，消息的分片策略在kafka的broker服务器端应用，而如何将消息分片是由producer负责，broker只是将消息以相同的到达顺序进行存储。每个topic的分片数量可以在Kafka的broker端设置。</p>
<p>Kafka副本策略是其0.8版本之后的一个重要的特性。虽然Kafka是高可扩展的，为了消息达到更好的持久性以及Kafka集群的高可用性，即便是存在broker因各种原因造成宕机，副本策略仍保证了消息在被发布和消费时的可靠性。Kafka中的producer和consumer都是“副本感知(replication-aware)”的。下图展示的是kafka中的副本策略。</p>
<p><img src="https://img-blog.csdn.net/20151029152303279?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p></p>
<p>一个消息的每个分片可拥有n个副本，能够容忍n-1次失败来保证消息的交付。在n个分片中，其中一个作为 leader角色，其余n-1个作为follower角色。Zookeeper中保存着leader副本的信息，以及当前follower的ISR(in-sync replicas)同步副本。Leader中保存着所有ISR的follower列表。每个副本将它的消息及offset保存在它的本地log中并定期同步到磁盘。这个过程同时也保证了一条消息要么被写所有副本，要么不写。</p>
<p> </p>
<p>Kafka支持下列副本模式：</p>
<p>1.      Synchronous replication（同步模式）.在该模式中，一个producer首先从zookeeper中识别leader所在位置，并且向其发布消息。当消息发布完成，它被写入leader的日志中，而其他follower将从leader处“拉”副本数据。由于采用的单隧道，因此消息的到达顺序是有序的。每个follower在写各自本地日志后，向leader发送一个确认信息。一旦所有副本写入完成以及leader收到所有的确认信息，leader向producer发送一个确认信息。在consumer，所有的拉数据动作都是从leader处开始。</p>
<p>2.      Asynchronous replication（异步模式）.该模式中与同步模式唯一的不同是，当leader写本地日志后，它立即向producer发送一个确认信息，而不必等其他的follower都完成拉取和确认。但是，该模式并不保证当有broker发生宕机时消息的可靠性。</p>
<p> </p>
<p>当任何一个follower在一个设定的时间内发生失败时，leader将其从ISR列表里删除，写副本的操作将在ISR列表中未失败的follower上进行。当失败的follower重启之后，它首先截取到上一个checkpoint位置，然后从leader日志的相同checkpoint位置处之后拉取所有的消息。当该follower与leader都同步之后，leader将其加入当前ISR列表中。</p>
<p>当leader失败时(当写本地日志时，或者在发送确认信息给producer时)，消息的分片会被重新发送到一个新的leader broker。选举新leader的过程只涉及之前的leader在zookeeper中注册的ISR列表中包含的所有follower，选举策略是将最先注册的follower本成为leader。每个follower都向zookeeper监听来随时获取新leader的消息。当新leader选举后，所有follower从上个确认信息处截断，然后从新leader处同步信息。而新leader会一直等待所有存活的follower同步完成，然后将它们写入当前的
 ISR列表中并发送给Zookeeper，然后再开启消息的读写操作。</p>
<p> </p>
<p>Kafka的副本策略保证了它更强的持久性和高可用性，保证了任何成功发布的消息，在有broker宕机时，不会丢失并且被消费。</p>
<p> </p>
<p>--转载请注明出处--</p>
            </div>
                </div>