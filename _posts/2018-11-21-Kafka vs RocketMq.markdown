---
layout:     post
title:      Kafka vs RocketMq
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/LemonGirls/article/details/77184059				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="kafka-vs-rocketmq">Kafka vs RocketMq</h2>

<p>RocketMq相较kafka，做的最大的提升在于解决Kafka的劣势：低延迟和高可靠性。如RocketMq官方提到</p>

<blockquote>
  <p>Unfortunately, Kafka can not meet our requirements especially in terms of low latency and high reliability…we decided to invent a new messaging engine to handle a broader set of use cases…</p>
</blockquote>

<p>就低延迟和高可靠性分别比较下Kafka和RocketMq。</p>

<ul>
<li><p>低延迟</p>

<p>RocketMQ使用长轮询，Kafka在0.8以前是短轮询方式，但0.8以后版本也支持长轮询。 <br>
长轮询和短轮询的差异在于，短轮询的实时性就取决于轮询间隔。</p></li>
<li><p>高可靠性</p>

<p>RocketMq的高可靠性主要体现在消息重试，和事务性上。Kafka不具备上述两点功能。</p></li>
</ul>

<p>⭐️</p>

<p>另外，从分布式架构上来说，Kafka采取的是，每个broker在群集中的地位是一样的，一个broker既可作为Master，也可作为Slave，当Master挂掉之后，会从Slave中选举出新的Master。RocketMq中，一个broker只能要么是Master，要么是Slave。这个在初始的机器配置里面，就定了，Master挂掉之后，就挂掉了,不过只是影响不能写入，消费者会从Slave上进行消费。</p>

<p>Kafka集群管理的中间件使用的是zookeeper, Strom也使用了zookeeper。 <br>
RocketMq集群管理的是自己写的NameSrv。</p>

<p>⭐️</p>

<p>然后，从各种插件上来说，Kafka因为年长的优势，插件的内容和形式上来说Kafka就更占优势，例如confluent系列。支持各种语言的客户端，支持数据库交互等等..</p>

<p>不过，就功能上而言，Kafka的确更适合在日志系列领域，RocketMq更适合高实时，高事务性的领域。</p>

<p>⭐️</p>

<p>最后，就性能上而言，网上很多性能上比较的示例，大多都是阿里团队做的，结论是，在单机，高并发的环境下，RocketMq的性能胜于Kafka, 但分布式上来说，鹿死谁手呢，还需要更多的性能测试。</p>

<p>⭐️</p>

<p>以上结论，有任何措辞不适的问题，还请包容和纠正。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>