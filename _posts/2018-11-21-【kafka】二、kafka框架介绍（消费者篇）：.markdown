---
layout:     post
title:      【kafka】二、kafka框架介绍（消费者篇）：
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1>本人菜鸡，正在努力学习，记录知识以备后患！</h1>

<p style="text-indent:0;">该文章承接上文（kafka系列），暂有两篇：</p>

<p style="text-indent:0;">【kafka】一、kafka框架介绍（生产者篇）：<a href="https://blog.csdn.net/lsr40/article/details/84029034" rel="nofollow">https://blog.csdn.net/lsr40/article/details/84029034</a></p>

<p style="text-indent:0;">【kafka】二、kafka框架介绍（消费者篇）：<a href="https://blog.csdn.net/lsr40/article/details/84034394" rel="nofollow">https://blog.csdn.net/lsr40/article/details/84034394</a></p>

<p style="text-indent:0;">另一篇解决报错的文章：</p>

<p style="text-indent:0;">【kafka】报错：advertised.listeners参数的重要性（外部访问局域网kafka）<a href="https://blog.csdn.net/lsr40/article/details/84135959" rel="nofollow">https://blog.csdn.net/lsr40/article/details/84135959</a></p>

<p>消费者有两个配置，一个是<a href="http://kafka.apache.org/documentation/#newconsumerconfigs" rel="nofollow" id="newconsumerconfigs">New Consumer Configs</a>（让kafka自己管理偏移量），一个是<a href="http://kafka.apache.org/documentation/#oldconsumerconfigs" rel="nofollow" id="oldconsumerconfigs">Old Consumer Configs</a>（使用默认zk管理偏移量），具体使用的时候，请认准自己的kafka的版本</p>

<p>官网：<a href="http://kafka.apache.org/documentation/#consumerconfigs" rel="nofollow">http://kafka.apache.org/documentation/#consumerconfigs</a></p>

<p><span style="color:#7c79e5;"><strong>（消费者偏移量记录）</strong></span>当然，在说参数前，我要说说消费者的一些事情：</p>

<p>消费者消费数据的时候，必须记录每个分区消费到哪了，也就是说，如果消费者A读取的那个topic有10个分区的话，必须记录10个值，这10个值分别表示每个分区被消费到哪里（通过offset偏移量来记录），因此就有以下的概念，这些消费者的消费记录保存在哪里？</p>

<ol><li><span style="color:#e579b6;">手动管理</span>：通过api，获取拿到的该条数据的偏移量和分区值，保存在存储系统中（可以是数据库，可以是文件等方式），当然自己管理是最麻烦的一种方式，但也是可控的东西最多的。关于这个我看到了有一个系列的文章（感谢作者：葬月魔帝），大家有兴趣可以关注下！

	<table align="center" border="1" cellpadding="1" cellspacing="1"><tbody><tr><td style="text-align:left;vertical-align:middle;">            如何管理Spark Streaming消费Kafka的偏移量（一）</td>
				<td style="text-align:left;vertical-align:middle;"><a href="https://blog.csdn.net/u010454030/article/details/78535003" rel="nofollow">https://blog.csdn.net/u010454030/article/details/78535003</a></td>
			</tr><tr><td style="text-align:left;vertical-align:middle;">
				<p style="text-indent:50px;">如何管理Spark Streaming消费Kafka的偏移量（二）</p>
				</td>
				<td style="text-align:left;vertical-align:middle;"><a href="https://blog.csdn.net/u010454030/article/details/78554643" rel="nofollow">https://blog.csdn.net/u010454030/article/details/78554643</a></td>
			</tr><tr><td style="text-align:left;vertical-align:middle;">            如何管理Spark Streaming消费Kafka的偏移量（三）</td>
				<td style="text-align:left;vertical-align:middle;"><a href="https://blog.csdn.net/u010454030/article/details/78660643" rel="nofollow">https://blog.csdn.net/u010454030/article/details/78660643</a></td>
			</tr></tbody></table></li>
	<li><span style="color:#e579b6;">zookeeper管理</span>：配置，offsets.storage=zookeeper，auto.commit.enable=true（开启自动提交偏移量）接着auto.commit.interval.ms=60*1000（默认值：1分钟提交一次），缺点：提交之后程序又运行了30s之后挂掉了，下次消费的时候就会重复消费那30s的数据，反复的跟zk交互，影响性能。</li>
	<li><span style="color:#e579b6;">kafka管理</span>：offsets.storage=kafka，enable.auto.commit=true，auto.commit.interval.ms=5000（默认是5s提交一次），实现原理是kafka自己创建一个名为__consumer_offsets的topic，将offset存储在该topic下，推荐采用该方式，关于如何读取这个topic的信息：（感谢作者：huxihx）<a href="https://www.cnblogs.com/huxi2b/p/6061110.html" rel="nofollow">https://www.cnblogs.com/huxi2b/p/6061110.html</a>，当然也可以使用其他的监控工具，例如kafka-manager之类的别人写好的框架，通过前端页面查看topic的数据情况，和消费者偏移量情况。</li>
	<li><span style="color:#e579b6;">偏移量的管理涉及到三种语义</span>
	<table border="1" cellpadding="1" cellspacing="1"><tbody><tr><td style="text-align:center;vertical-align:middle;">至少一次</td>
				<td style="text-align:center;vertical-align:middle;">生产者同步发送消息（将acks设置为1或者-1/all）</td>
			</tr><tr><td style="text-align:center;vertical-align:middle;">至多一次</td>
				<td style="text-align:center;vertical-align:middle;">生产者异步发送消息，无论接受到与否</td>
			</tr><tr><td style="text-align:center;vertical-align:middle;">仅此一次</td>
				<td style="text-align:center;vertical-align:middle;">手动管理偏移量，做到处理一条保存一次偏移量，或者批量处理一批（处理未结束失败了要记得回滚），保存一次</td>
			</tr></tbody></table>
	kafka有给出java版api：<a href="http://kafka.apache.org/0110/javadoc/index.html?org/apache/kafka/clients/consumer/KafkaConsumer.html" rel="nofollow">http://kafka.apache.org/0110/javadoc/index.html?org/apache/kafka/clients/consumer/KafkaConsumer.html</a>，在<a name="rebalancecallback">Storing Offsets Outside Kafka</a>该模块中有介绍。<img alt="" class="has" height="349" src="https://img-blog.csdnimg.cn/20181115170555898.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xzcjQw,size_16,color_FFFFFF,t_70" width="898"></li>
</ol><p><strong><span style="color:#f33b45;">-1.group.id</span></strong>：消费者的组id（非常重要），他涉及到了<span style="color:#e579b6;">发布订阅和队列模式</span></p>

<ul><li>发布订阅模式：不同groupid的消费者之间消费数据互不影响，也就是说可以同时消费同一份数据。举个例子：groupid就好像家庭id，家庭A定了一份人民日报（topic1），家庭B也定了一份人民日报（也是topic1），那么当家庭A在看他们家买的人民日报的时候，丝毫不影响家庭B，家庭B可以同时看相同内容，也可以看不相同</li>
	<li>队列模式：相同group_id的消费者之间不可能消费同一份数据，举个例子：家庭A定了一份人民日报（topic1），人民日报里面有3个板块（3个分区），当爸爸（消费者1）在看第一个板块的时候（分区1），其他家庭成员就不能再看相同的板块，只能去看板块2（分区2）或者板块3（分区3）</li>
</ul><p><strong><span style="color:#f33b45;">-2.Rebalance</span></strong>：通过zookeeper实现的消费者负载均衡机制，在队列模式下，如果多个消费者的group_id相同，那么他们就会触发负载均衡机制。<strong><span style="color:#86ca5e;">举个例子：</span></strong><span style="color:#f33b45;">topicA有<strong>3</strong>个分区</span>，我启动了一个<strong><span style="color:#7c79e5;">消费者x</span></strong>（默认这里的消费者都只有一个线程），那么消费者x就会独自消费3个分区的数据。这时，我又启动了<strong><span style="color:#7c79e5;">消费者y</span></strong>（group_id和x相同），那么就会马上触发负载均衡机制，一个消费者消费2个分区数据，另一个消费1个分区，这时候我又启动了一个<strong><span style="color:#7c79e5;">消费者z</span></strong>（group_id和x和y相同），这样每个消费者都会消费1个分区的数据。也就是<span style="color:#7c79e5;"><strong>说当消费者的总个数（总线程数）和topicA的分区数一样的时候，性能最好</strong>。</span>如果这时候再增加消费者，那么就会出现有一个消费者空转（消耗资源，但接受不到数据），导致资源浪费。</p>

<p><span style="color:#f33b45;"><strong>-3.fetch.min.bytes</strong></span>：设置最少一次拉取多少数据，默认是一有数据就拉，可以调大，但是数据就会有延迟，但是请求的次数就降低，提升吞吐量（一般默认就可以了）</p>

<p>其他的基本上不需要太大的调整（当然如果有特殊需求的话，不同集群情况下，参数最优肯定都是不同的，还需要具体测试），为了提升性能，有时候还需要调整broker和topic的配置，这两部分本人暂时没有做太深入的研究，就不在这里瞎***吹了！</p>

<p>最后kafka自带消费者：</p>

<pre class="has">
<code class="language-bash">bin/kafka-console-consumer.sh --topic topic名称 --zookeeper zk1:端口1,zk2:端口2,zk3:端口3/zk上kafka的路径</code></pre>

<h3><span style="color:#3399ea;">总结：考虑偏移量如何管理，考虑发布订阅还是队列模式，考虑消费模式是否达到最优，是否设置拉数据的延迟，是否可以优化消费端的代码来减少数据延迟</span></h3>

<h1>kafka系列文章暂时到这，主要就介绍了对于集群使用者的相关参数（生产者和消费者），对于搭建集群（topic和broker）的一些参数，并没有相关的介绍，原谅本人才疏学浅。如果有什么问题或者笔误的地方，欢迎可以留言评论~</h1>

<p> </p>            </div>
                </div>