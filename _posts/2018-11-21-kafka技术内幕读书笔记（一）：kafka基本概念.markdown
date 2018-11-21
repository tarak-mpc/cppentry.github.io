---
layout:     post
title:      kafka技术内幕读书笔记（一）：kafka基本概念
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><em>前言：</em></p>

<p>消息队列技术广泛用于异步处理，应用解耦，流量削峰，日志处理等场景，对于一个热爱技术的程序员来说怎么能不去学习这一酷炫的技术呢？而为什么要选取kafka来进行学习？我认为kafka的中文学习资料是比较多的，而且kafka的国内开发者以及使用者比较多，最后是kafka是比较“重”的消息队列，我相信能把kafka搞定，其他的消息队列那也不成问题了！</p>

<p>kafka基本概念：</p>

<p><em>分区模型：</em></p>

<p>Kafka集群向多个消息代理服务器（ broker server ）组成，发布至Kafka集群的每条消息都有一个类别，用主题（ topic ）来表示。通常，不同应用产生不同类型的数据，可以设置不同的主题。一个主题一般会有多个消息的订阅者，当生产者发布消息到某个主题时，订阅了这个主题的消费者都可以接收到生产者写入的新消息。Kafka集群为每个主题维护了分布式的分区（ partition ）日志文件，物理意义上可以把主题看作分区的日志文件（ partitioned log ）。每个分区都是一个有序的、不可变的记录序列，新的消息会不断追加到提交日志（ commit log ）。分区中的每条消息都会按照时间顺序分配到一个单调递增的顺序编号， 叫<br>
作偏移盘（ offset ），这个偏移量能够唯一地定位当前分区中的每一条消息。</p>

<p><img alt="" class="has" height="273" src="https://img-blog.csdn.net/20180908085533749?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NjQyMzQw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="649"></p>

<p>消费模型：</p>

<p>Kafka采用拉取模型，由消费者向己记录消费状态，每个消费者五相独立地顺序读取每个分区的消息。如图1-4所示，有两个消费者（不同消费组）拉取同一个主题的消息，消费者A的消费进度是3,消费者B 的消费者进度是6 。消费者拉取的最大上限通过最高水位控制，生产者最新写入的消息如果还没有达到备份数量，对消费者是不可见的。这种由消费者控制偏移韭－的优点是： 消费者可以按照任意的顺序消费消息。比如，消费者可以重置到旧的偏移盏，重新处理之前已经消费过的消息； 或者直接跳到最近的位置，从当前时刻开始消费。</p>

<p><img alt="" class="has" height="236" src="https://img-blog.csdn.net/2018090808563512?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NjQyMzQw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="703"></p>

<p>分布式模型：</p>

<p>Kafka每个主题的多个分区日志分布式地存储在Kafka集群上，同时为了故障容错，每个分区都会以副本的方式复制到多个消息代理节点上。其中一个节点会作为主副本（ Leader ），其他节点作为备份副本（ Follower ，也叫作从副本）。主副本会负责所有的客户端读写操作，备份副本仅仅从主副本同步数据。当主副本出现故障时，备份副本中的一个副本会被选择为新的主副本。因为每个分区的副本中只有主副本接受读写，所以每个服务端都会作为某些分区的主副本，以及另外一些分区的备份副本，<br>
这样Kafka集群的所有服务端整体上对客户端是负载均衡的。</p>

<p>Kafka 的生产者和消费者相对于服务端而言都是客户端，生产者客户端发布消息到服务端的指定主题， 会指定消息所属的分区。生产者发布消息时根据消息是否有键， 采用不同的分区策略。消息没有键时，通过轮询方式进行客户端负载均衡消息有键时，根据分区语义确保相同键的消息总是发送到同一个分区。</p>

<p><br>
Kafka 的消费者通过订阅主题来消费消息并且每个消费者都会设置一个消费组名称。因为生产者发布到主题的每一条消息都只会发送给消费组的一个消费者。所以，如果要实现传统消息系统的“队列”模型，可以让每个消费者都拥有相同的消费组名称，这样消息就会负载均衡到所有的消费者；如果要实现“发布－订阅”模型， 则每个消费者的消费组名称都不相同，这样每条消息就会广播给所有的消费者。</p>

<p><img alt="" class="has" height="226" src="https://img-blog.csdn.net/20180908085845330?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NjQyMzQw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="808"></p>

<p>kafka零拷贝技术：</p>

<p>kafka使用“零拷贝技术”（ zero-copy ）只需将磁盘文件的数据复制到页面缓存中一次，然后将数据从页面缓存直接发送到网络中（发送给不同的使用者时，都可以重复使用同一个页面缓存），避免了重复的复制操作。这样，消息使用的速度基本上等同于网络连接的速度了。</p>

<p><img alt="" class="has" height="412" src="https://img-blog.csdn.net/20180908085959137?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NjQyMzQw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="808"></p>

<p>副本机制和容错处理：</p>

<p>Kafka的副本机制会在多个服务端节点（简称节点即消息代理节点）上对每个主题分区的日志进行复制。当集群中的某个节点出现故障时，访问故障节点的请求会被转移到其他正常节点的副本上。副本的单位是主题的分区， Kafka每个主题的每个分区都有一个主副本以及0个或多个备份副本。备份副本会保持和主副本的数据同步，用来在主副本失效时替换为主副本。</p>

<p>分布式系统处理故障容错时，需要明确地定义节点是否处于存活状态。kafka对节点的存活定义有两个条件：<br>
1.节点必须和ZK保持会话；<br>
2.如果这个节点是某个分区的备份副本，它必须对分区主副本的写操作进行复制，并且复制的<br>
进度不能落后太多。</p>

<p>满足这两个条件，叫作“正在同步中”（ in-sync ）。每个分区的主副本会跟踪正在同步中的备份副本节点（ In Sync Replicas ，即ISR ）。如果一个备份副本挂掉、没有响应或者落后太多，主副本就会将其从同步副本集合中移除。反之，如果备份副本重新赶上主副本，它就会加入到主副本的同步集合中。<br>
在Kafka 中， 一条消息只有被ISR集合的所有副本都运用到本地的日志文件，才会认为消息被成功提交了。任何时刻，只要ISR至少有一个副本是存活的， Kafka就可以保证“一条消息一旦被提交，就不会丢失” 。只有已经提交的消息才能被消费者消费，因此消费者不用担心会看到因为主副本失败而丢失的消息。</p>            </div>
                </div>