---
layout:     post
title:      Kafka 学习笔记（1）—— Kafka ,JMS 介绍
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u012292754/article/details/82837697				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1><a id="1_Kafka_0"></a>1 Kafka介绍</h1>
<p>在流式计算中，Kafka一般用来缓存数据，Storm通过消费Kafka的数据进行计算。</p>
<ul>
<li>KAFKA + STORM +REDIS</li>
<li>Apache Kafka是一个开源消息系统，由Scala写成。是由Apache软件基金会开发的一个开源消息系统项目。</li>
<li>Kafka最初是由LinkedIn开发，并于2011年初开源。2012年10月从Apache Incubator毕业。该项目的目标是为处理实时数据提供一个统一、高通量、低等待的平台。</li>
<li><strong>Kafka是一个分布式消息队列：生产者、消费者的功能</strong>。它提供了类似于JMS的特性，但是在设计实现上完全不同，此外它并不是JMS规范的实现。</li>
<li>Kafka对消息保存时根据Topic进行归类，发送消息者称为Producer,消息接受者称为Consumer,此外kafka集群有多个kafka实例组成，每个实例(server)成为broker。</li>
<li>无论是kafka集群，还是producer和consumer都依赖于zookeeper集群保存一些meta信息，来保证系统可用性</li>
</ul>
<h2><a id="11__Kafka__8"></a>1.1  Kafka 概念</h2>
<p>消费者可以有多个，并且主动拉取数据。</p>
<ul>
<li>Producer：发送消息者</li>
<li>Consumer：消息接受者</li>
<li>数据保存的进程，broker,每台服务器都有独立的 broker 进程。每个kafka实例(server)</li>
<li>数据目的（分类、主题）：destination,topic</li>
<li>数据分配，partition,有多个，有副本</li>
</ul>
<h2><a id="12_Kafka__15"></a>1.2 Kafka 核心组件</h2>
<ul>
<li>Topic ：消息根据Topic进行归类</li>
<li>Producer：发送消息者</li>
<li>Consumer：消息接受者</li>
<li>broker：每个kafka实例(server)</li>
<li>Zookeeper：依赖集群保存meta信息。</li>
</ul>
<h1><a id="2_JMS_22"></a>2 JMS</h1>
<h2><a id="21_JMS__23"></a>2.1 JMS 介绍</h2>
<p>JMS是什么：JMS是Java提供的一套技术规范(JMS:Java Message Standard java消息规范)<br>
JMS干什么用：用来异构系统集成通信，缓解系统瓶颈，提高系统的伸缩性增强系统用户体验，使得系统模块化和组件化变得可行并更加灵活<br>
通过什么方式：生产消费者模式（生产者、服务器、消费者）<br>
<img src="https://img-blog.csdn.net/20180925141734402?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIyOTI3NTQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<h2><a id="22_JMS_28"></a>2.2 JMS消息传输模型</h2>
<p><img src="https://img-blog.csdn.net/2018092514203080?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIyOTI3NTQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<h3><a id="221__30"></a>2.2.1 点对点模式（一对一，消费者主动拉取数据，消息收到后消息清除）</h3>
<p>点对点模型通常是一个基于拉取或者轮询的消息传送模型，这种模型从队列中请求信息，而不是将消息推送到客户端。这个模型的特点是发送到队列的消息被一个且只有一个接收者接收处理，即使有多个消息监听者也是如此。</p>
<h3><a id="222_	_32"></a>2.2.2 	发布/订阅模式（一对多，数据生产后，推送给所有订阅者）</h3>
<p>发布订阅模型则是一个基于推送的消息传送模型。发布订阅模型可以有多种不同的订阅者，临时订阅者只在主动监听主题时才接收消息，而持久订阅者则监听主题的所有消息，即使当前订阅者不可用，处于离线状态。</p>
<h2><a id="23_JMS_34"></a>2.3 JMS核心组件</h2>
<ul>
<li>Destination：消息发送的目的地，也就是前面说的Queue和Topic。</li>
<li>Message ：从字面上就可以看出是被发送的消息。</li>
<li>Producer： 消息的生产者，要发送一个消息，必须通过这个生产者来发送。</li>
<li>MessageConsumer： 与生产者相对应，这是消息的消费者或接收者，通过它来接收一个消息。<br>
<img src="https://img-blog.csdn.net/20180925142224199?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIyOTI3NTQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"><br>
通过与ConnectionFactory可以获得一个connection<br>
通过connection可以获得一个session会话。</li>
</ul>
<h2><a id="24__JMS_42"></a>2.4  常见的类JMS消息服务器</h2>
<h3><a id="241_JMS_ActiveMQ_43"></a>2.4.1 JMS消息服务器 ActiveMQ</h3>
<p>ActiveMQ 是Apache出品，最流行的，能力强劲的开源消息总线。ActiveMQ 是一个完全支持JMS1.1和J2EE 1.4规范的。</p>
<ul>
<li>多种语言和协议编写客户端。语言: Java, C, C++, C#, Ruby, Perl, Python, PHP。应用协议: OpenWire,Stomp REST,WS Notification,XMPP,AMQP</li>
<li>完全支持JMS1.1和J2EE 1.4规范 (持久化,XA消息,事务)</li>
<li>对Spring的支持,ActiveMQ可以很容易内嵌到使用Spring的系统里面去,而且也支持Spring2.0的特性</li>
<li>通过了常见J2EE服务器(如 Geronimo,JBoss 4, GlassFish,WebLogic)的测试,其中通过JCA 1.5 resource adaptors的配置,可以让ActiveMQ可以自动的部署到任何兼容J2EE 1.4 商业服务器上</li>
<li>支持多种传送协议:in-VM,TCP,SSL,NIO,UDP,JGroups,JXTA</li>
<li>支持通过JDBC和journal提供高速的消息持久化</li>
<li>从设计上保证了高性能的集群,客户端-服务器,点对点</li>
<li>支持Ajax</li>
<li>支持与Axis的整合</li>
<li>可以很容易得调用内嵌JMS provider,进行测试</li>
</ul>
<h3><a id="242__Metamorphosis_55"></a>2.4.2 分布式消息中间件 Metamorphosis</h3>
<p>Metamorphosis (MetaQ) 是一个高性能、高可用、可扩展的分布式消息中间件，类似于LinkedIn的Kafka，具有消息存储顺序写、吞吐量大和支持本地和XA事务等特性，适用于大吞吐量、顺序消息、广播和日志数据传输等场景，在淘宝和支付宝有着广泛的应用，现已开源。</p>
<ul>
<li>生产者、服务器和消费者都可分布</li>
<li>消息存储顺序写</li>
<li>性能极高,吞吐量大</li>
<li>支持消息顺序，支持本地和XA事务</li>
<li>客户端pull，随机读,利用sendfile系统调用，zero-copy ,批量拉数据</li>
<li>支持消费端事务，支持消息广播模式，支持异步发送消息，支持http协议，支持消息重试和recover</li>
<li>数据迁移、扩容对用户透明。消费状态保存在客户端</li>
<li>支持同步和异步复制两种HA。	支持group commit</li>
</ul>
<h3><a id="243__RocketMQ_65"></a>2.4.3 分布式消息中间件 RocketMQ</h3>
<p>RocketMQ 是一款分布式、队列模型的消息中间件</p>
<ul>
<li>能够保证严格的消息顺序</li>
<li>提供丰富的消息拉取模式</li>
<li>高效的订阅者水平扩展能力</li>
<li>实时的消息订阅机制</li>
<li>亿级消息堆积能力</li>
<li>Metaq3.0 版本改名，产品名称改为RocketMQ</li>
</ul>
<h3><a id="244_MQ_73"></a>2.4.4 其他MQ</h3>
<ul>
<li>.NET消息中间件 DotNetMQ</li>
<li>基于HBase的消息队列 HQueue</li>
<li>Go 的 MQ 框架 KiteQ</li>
<li>AMQP消息服务器 RabbitMQ</li>
<li>MemcacheQ 是一个基于 MemcacheDB 的消息队列服务器。</li>
</ul>
<h1><a id="3__79"></a>3 为什么需要消息队列</h1>
<p>消息系统的核心作用就是三点：<strong>解耦，异步和并行</strong><br>
以用户注册的案列来说明消息系统的作用</p>
<h2><a id="31__82"></a>3.1 用户注册流程</h2>
<p><img src="https://img-blog.csdn.net/20180925143425842?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIyOTI3NTQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"><br>
问题：随着后端流程越来越多，每步流程都需要额外的耗费很多时间，从而会导致用户更长的等待延迟。</p>
<h2><a id="32__85"></a>3.2 用户注册的并行执行</h2>
<p><img src="https://img-blog.csdn.net/20180925143515803?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIyOTI3NTQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"><br>
问题：系统并行的发起了4个请求，4个请求中，如果某一个环节执行1分钟，其他环节再快，用户也需要等待1分钟。如果其中一个环节异常之后，整个服务挂掉了。</p>
<h2><a id="33__88"></a>3.3 用户注册的最终一致</h2>
<p><img src="https://img-blog.csdn.net/20180925143559101?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIyOTI3NTQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<ol>
<li>保证主流程的正常执行、执行成功之后，发送MQ消息出去</li>
<li>需要这个destination的其他系统通过消费数据再执行，最终一致。<br>
<img src="https://img-blog.csdn.net/20180925143639866?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIyOTI3NTQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></li>
</ol>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>