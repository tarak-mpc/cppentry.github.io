---
layout:     post
title:      Kafka Producer相关代码分析
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="markdown-here-wrapper">
<h1 id="kafka-producer-" style="font-weight:bold;font-size:1.6em;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(221,221,221);">
Kafka Producer相关代码分析</h1>
<p>标签（空格分隔）： kafka</p>
<hr><p>Kafka Producer将用户的消息发送到Kafka集群（准确讲是发送到Broker）。本文将分析Producer相关的代码实现。</p>
<h2 id="-kafka-producer-producer" style="font-weight:bold;font-size:1.4em;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(238,238,238);">
类kafka.producer.Producer</h2>
<p>如果你自己实现Kafka客户端来发送消息的话，你就是用到这个类提供的接口来发送消息。（如果你对如何利用Producer API来发送消息还不是很熟悉的话，可以参看<a href="https://cwiki.apache.org/confluence/display/KAFKA/0.8.0+Producer+Example" rel="nofollow">官方的例子</a>）。这个类提供了同步和异步两种方式来发送消息。</p>
<p>异步发送消息是基于同步发送消息的接口来实现的。异步发送消息的实现很简单，客户端消息发送过来以后，先放入到一个队列中然后就返回了。Producer再开启一个线程（ProducerSendThread）不断从队列中取出消息，然后调用同步发送消息的接口将消息发送给Broker。</p>
<p>Producer发送同步消息是委托给EventHandler做的，EventHandler是个接口，具体实现为DefaultEventHandler。它们的简化类图如下：<br><img src="https://img-blog.csdn.net/20150117104211338?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamV3ZXM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="Producer类图"></p>
<p>可以看到，Producer类中的成员producerSendThread和queue是为了发送异步消息的，eventHandler是为了发送同步消息的，当然异步消息也需要它。KeyedMessage是封装了用户发送的消息。Seq是Scala中的序列，可以看成是Java中的List。</p>
<p>KeyedMessage的简化类图如下：<br><img src="https://img-blog.csdn.net/20150117171318335" alt="KeyMessage类图"></p>
<h2 id="-defaulteventhandler" style="font-weight:bold;font-size:1.4em;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(238,238,238);">
类DefaultEventHandler</h2>
<p>DefaultEventHandler是接口EventHandler唯一的实现。从上一节可以看到Producer发送给EventHandler的消息格式为KeyedMessage。我们来看看在将KeyedMessage发送给Broker之前需要做哪些工作。</p>
<h3 id="-" style="font-weight:bold;font-size:1.3em;">
序列化</h3>
<p>KeyedMessage中的KV是由用户指定的自定义类型，而在发送给broker的时候是以二进制流来发送，因此还需要将用户自定类型的数据转换为二进制流。在初始化Producer的时候需要配置serializer.class，它就是用来处理这个事情的。此外，还要把多条Message组合成MessageSet并按照用户指定的压缩方式进行压缩。</p>
<h3 id="-broker" style="font-weight:bold;font-size:1.3em;">
找到对应的broker</h3>
<p>KeyedMessage中指定了该Message的topic，而一个topic可以有多个partition，每个partition有多个replica，由多个Broker来管理，这些Broker中有一个leader。只有leader broker能够响应客户端的读写请求。</p>
<p>由此可见，在将KeyedMessage发送给broker之前，必须找到该条Message对应的leader broker，具体步骤为：</p>
<ol><li>找出该topic的所有partition，</li><li>找出该KeyedMessage应该发送到的那个partition，在初始化Producer的时候配置partitioner.class就是用来对Message进行分区的</li><li>找出对应partition所在的leader broker。</li></ol><p>最后，DefaultEventHandler将序列化后的Message封装成ProducerRequest，它自身并没有将ProducerRequest发送给broker的逻辑，而是将其交给SyncProducer来继续后面发送的流程。</p>
<h2 id="producerpool-syncproducer-blockingchannel" style="font-weight:bold;font-size:1.4em;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(238,238,238);">
ProducerPool, SyncProducer和BlockingChannel</h2>
<p>它们在一起是完成最后的数据发送任务。先来看它们的类图：<br><img src="https://img-blog.csdn.net/20150117180331698" alt="ProducerPool等类图"><br>
ProducerPool中有一个HashMap，其key为brokerid，value为连接到这个broker的SyncProducer。因此ProducerPool的更准确名字应该为SyncProducerPool。</p>
<p>BlockingChannel可以看成是一个Socket客户端，它有两个成员变量分别是机器名和端口号。它的connect方法会打开到对应机器的socket。它的send方法可以发送RequestOrResponse，它是真正发送数据的地方。</p>
<p>SyncProducer提供了两个send方法，分别用来发送ProducerRequest和TopicMetadataRequest。它内部是调用了blockingChannel来发送数据的。</p>
<h1 style="font-weight:bold;font-size:1.6em;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(221,221,221);">
小结</h1>
<p>Producer发送数据的简化序列图如下：<img src="https://img-blog.csdn.net/20150117200747890?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamV3ZXM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="序列图"><br>
从图中可见，各个类的职责明确，BlockingChannel负责最底层的数据发送，SyncProducer负责将Request发送到一个指定的Broker那里，DefaultEventHandler负责数据转换和选择正确的Broker，直接给客户端使用的Producer则在此基础上提供了同步和异步两种发送方式。</p>
<div title="'MDH:IyBLYWZrYSBQcm9kdWNlcuebuOWFs+S7o+eggeWIhuaekDxicj48YnI+PGJyPuagh+etvu+8iOep" style="width:0;overflow:hidden;font-size:0em;">
​</div>
</div>
            </div>
                </div>