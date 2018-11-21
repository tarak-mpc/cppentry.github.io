---
layout:     post
title:      [喵咪KafKa(1)]KafKa的介绍以及使用场景
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u011142688/article/details/78490012				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="喵咪kafka1kafka的介绍以及使用场景">[喵咪KafKa(1)]KafKa的介绍以及使用场景</h1>

<p><img src="http://i.imgur.com/s39oCXG.jpg" alt="" title=""></p>

<h2 id="前言">前言</h2>

<p>哈喽!大家好呀,真是一坑未平一坑又起,otter还在继续更新的同时,笔者也为大家带来了关于kafka相关的一系列博客,要说到kafka就离不开现在特别火热的大数据技术,了解的童鞋可能只要一些大数据的带名词比如<strong>Hadoop,spark,storm</strong>,包括最近很火的微服务,kafka也是其中一员,但是不同的是kafka并不负责处理数据,要给kafka一个定义的话应该是一个<strong>分布式发布订阅消息系统</strong>可以说是一个数据通道保证数据稳定传输,要是感兴趣就开始和喵咪开始一场kafka的冒险吧!</p>

<p>附上:</p>

<p>喵了个咪的博客:<a href="w-blog.cn" rel="nofollow" target="_blank">w-blog.cn</a></p>

<p>KafKa官网地址:<a href="http://kafka.apache.org/" rel="nofollow" title="KafKa官网地址" target="_blank">http://kafka.apache.org/</a></p>

<p>Git地址:<a href="https://github.com/apache/kafka" rel="nofollow" title="Git地址" target="_blank">https://github.com/apache/kafka</a></p>



<h2 id="1-kafka简介">1. KafKa简介</h2>



<h3 id="初识kafka">初识KafKa</h3>

<p>笔者是怎么了解到KafKa的呢?其实这个也源于InfoQ大会的功劳(InfoQ大会是一个技术架构分享峰会),在之间有很多大家耳熟能详的公司来分享一些技术,大家在谈论大数据技术的时候使用到了本文开篇提的<strong>Hadoop,spark,storm</strong>此类技术,但是他们都要有一个共同点,就是大部分使用了<strong>KafKa</strong>作为了数据传输的通道,并且还有很多不需要使用到大数据的公司也在使用KafKa,为什么都要使用KafKa呢?KafKa能支撑大数据处理吗,KafKa还能做什么,笔者带着这些疑问,开始对KafKa进行了了解!</p>



<h3 id="kafka是什么">kafKa是什么</h3>

<p>KafKa是一款由Apache软件基金会开源,由Scala编写的一个<strong>分布式发布订阅消息系统</strong>,Kafka最初是由LinkedIn开发，并于2011年初开源(知道这个就够了),KafKa它最初的目的是为了解决,统一,高效低延时,高通量(同时能传输的数据量)并且高可用一个消息平台.</p>

<p>说的更简单易懂一点就是帮助程序之间互相传递消息,并且提供一些保证,所有的大数据处理也好,微服务也好他们都有一个共同的需求就是一个稳定的数据通道,KafKa刚好就是一个稳定高效通道最佳选择!</p>



<h3 id="kafka中的几个角色">KafKa中的几个角色</h3>

<ul>
<li>broker:对于KafKa集群来说,每一个KafKa实例都被称为一个broker</li>
<li>Topic(主题):在KafKa中每一条消息都所属一个Topic下,Topic之间是完全物理隔离的</li>
<li>Partitine(分区):一个Topic下面可以拥有一个到多个Partitine,Partitine也是物理层面的隔离</li>
<li>peoduker(生产者):向kafka的Topic发布消息</li>
<li>consumer(消费者):向Topic注册，并且接收发布到这些Topic的消息</li>
</ul>



<h3 id="kafka的特性">KafKa的特性</h3>

<p>笔者也是总结了一些关于KafKa的一些特性如下:</p>

<ul>
<li>kafka接收到的消息最终会以文件的形式存在本地保证了,只要消息接受成功理论上就不会丢失</li>
<li>KafKa通过append来实现消息的追加,保证消息都是有序的有先来后到的顺序</li>
<li>KafKa集群有良好的容灾机制,比如有N台服务器,可以承受N-1台服务器故障是保证<strong>提交的消息</strong>不会丢失</li>
<li>KafKa会更具Topic以及partition来进行消息在本地的物理划分</li>
<li>KafKa依赖zookeeper实现了offset,你不用关心到你获取了那些消息KafKa会知道并且在你下次获取时接着给你</li>
<li>你可以获取任意一个offset的记录</li>
<li>消息可以在KafKa内保存很长的时间也可以很短,KafKa基于文件系统能存储消息的容量取决于硬盘空间</li>
<li>KafKa的性能不会受到消息的数量影响</li>
</ul>



<h2 id="2-kafka的使用场景">2. KafKa的使用场景</h2>



<h3 id="消息队列mq">消息队列(MQ)</h3>

<p>KafKa可以代替传统的消息队列软件(阿里的队列软件RocketMQ就是基于KafKa实现的),在队列软件的选择上KafKa已经成了不二之选,使用KafKa来实现队列有如下优点</p>

<ul>
<li>KafKa的append来实现消息的追加,保证消息都是有序的有先来后到的顺序,</li>
<li>稳定性强队列在使用中最怕丢失数据,KafKa能做到理论上的写成功不丢失</li>
<li>分布式容灾好</li>
<li>容量大相对于内存队列,KafKa的容量受硬盘影响</li>
<li>数据量不会影响到KafKa的速度</li>
</ul>

<p>就以上几点和笔者之前使用的Redis来承载队列服务要优秀的多,在后续文章的比较中会一一说明</p>



<h3 id="分布式日志系统log">分布式日志系统(Log)</h3>

<p>在很多时候我们需要对一些庞大的数据进行存留,一些业务型公司可能永不上应为基本可以依靠数据库解决日志的问题,但是服务型公司比如jpush,云监控此类服务,日志存储这块会遇到巨大的问题,日志不能丢,日志存文件不好找,定位一条消息成本高(遍历当天日志文件),实时显示给用户难,这几类问题KafKa都能游刃有余</p>

<ul>
<li>KafKa的集群备份机制能做到n/2的可用,当n/2以下的机器宕机时存储的日志不会丢失</li>
<li>KafKa可以对消息进行分组分片,并且通过offset可以做到获取中间莫一条消息(通过算法很容易的到莫个时段的日志)</li>
<li>KafKa非常容易做到实时日志查询,可以从日志尾部获取需要显示给用户查询的资料即可</li>
</ul>



<h3 id="数据通道messaging">数据通道(Messaging)</h3>

<p>kafka特有的offset机制能够保证消息至少被获取一次,当程序在获取途中死亡这条消息会被认定为未被消费,下次会继续消费这条消息,此特性使得kafka可以作为一个保障数据传输的通道来使用,但是kafka并没有提供JMS中的”事务性”“消息传输担保(消息确认机制)”“消息分组”等企业级特性;所以kafka只能使用作为”常规”的消息系统</p>



<h2 id="3-总结">3. 总结</h2>

<p>本节简单介绍了一下kafka的一些相关知识,在后续的博文中喵咪将会从搭建单机到集群对KafKa进行优化测试等场景进行说明,那么今天的就到这里了,再次感谢大家的支持!</p>

<p>注:笔者能力有限有说的不对的地方希望大家能够指出,也希望多多交流!</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>