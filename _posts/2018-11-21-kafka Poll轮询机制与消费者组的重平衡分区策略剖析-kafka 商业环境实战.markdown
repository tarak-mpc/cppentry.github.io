---
layout:     post
title:      kafka Poll轮询机制与消费者组的重平衡分区策略剖析-kafka 商业环境实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本套技术专栏是作者（秦凯新）平时工作的总结和升华，通过从真实商业环境抽取案例进行总结和分享，并给出商业应用的调优建议和集群环境容量规划等内容，请持续关注本套博客。版权声明：禁止转载，欢迎学习。QQ邮箱地址：1120746959@qq.com，如有任何商业交流，可随时联系。					https://blog.csdn.net/shenshouniu/article/details/84076930				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>本套系列博客从真实商业环境抽取案例进行总结和分享，并给出Spark商业应用实战指导，请持续关注本套博客。版权声明：本套Spark商业应用实战归作者（秦凯新）所有，禁止转载，欢迎学习。</p>
<ul>
<li><a href="https://juejin.im/post/5bd464ccf265da0ac3735124" rel="nofollow">kafka 商业环境实战-kafka生产环境规划 </a></li>
<li><a href="https://juejin.im/post/5bd50b9451882529642b2581" rel="nofollow">kafka 商业环境实战-kafka生产者和消费者吞吐量测试 </a></li>
<li><a href="https://juejin.im/post/5bd51b32e51d457947024771" rel="nofollow">kafka 商业环境实战-kafka生产者Producer参数设置及参数调优建议  </a></li>
<li><a href="https://juejin.im/post/5bdf04c0e51d452c846ea051" rel="nofollow">kafka 商业环境实战-kafka集群管理重要操作指令运维兵书  </a></li>
<li><a href="https://blog.csdn.net/shenshouniu/article/details/83957355" rel="nofollow">kafka 商业环境实战-kafka集群Broker端参数设置及调优准则建议</a></li>
<li><a href="https://blog.csdn.net/shenshouniu/article/details/83960012" rel="nofollow">kafka 商业环境实战-kafka之Producer同步与异步消息发送及事务幂等性案例应用实战</a></li>
<li><a href="https://blog.csdn.net/shenshouniu/article/details/84076930" rel="nofollow">kafka 商业环境实战-kafka Poll轮询机制与消费者组的重平衡分区策略剖析</a></li>
<li><a href="https://juejin.im/post/5be7e0556fb9a049a62c1792" rel="nofollow">kafka 商业环境实战-kafka rebalance 机制与Consumer多种消费模式案例应用实战</a></li>
<li>[kafka 商业环境实战-kafka副本与ISR同步机制原理深入剖析]</li>
<li>[kafka 商业环境实战-kafka精确一次语义EOS的原理深入剖析]</li>
<li>[kafka 商业环境实战-kafka消息的幂等性与事务支持机制深入剖析]</li>
<li>[kafka 商业环境实战-kafka集群Controller竞选与责任设计思路架构详解]</li>
<li>[kafka 商业环境实战-kafka集群消息格式之V1版本到V2版本的平滑过渡详解]</li>
<li>[kafka 商业环境实战-kafka集群水印与leader epoch对数据一致性保障的深入研究]</li>
<li>[kafka 商业环境实战-kafka集群日志文件系统设计与留存机制及Compact深入研究]</li>
<li>[kafka 商业环境实战-kafka集群Consumer group状态机及Coordinaor管理机制深入剖析]</li>
<li>[kafka 商业环境实战-kafka调优过程在吞吐量，持久性，低延时，可用性等指标的折中选择研究]</li>
</ul>
<h2><a id="1_Consumer_20"></a>1 Consumer在单线程下一手遮天</h2>
<p>注意本文采用最新版本进行Kafka的内核原理剖析，新版本每一个Consumer通过独立的线程，来管理多个Socket连接，即同时与多个broker通信实现消息的并行读取。这就是新版的技术革新。类似于Linux I/O模型或者Select NIO 模型。</p>
<h2><a id="2_Poll_23"></a>2 Poll为什么要设置一个超时参数</h2>
<ul>
<li>条件：</li>
<li>1：获取足够多的可用数据</li>
<li>2：等待时间超过指定的超时时间。</li>
<li>目的在于让Consumer主线程定期的""苏醒"去做其他事情。比如：定期的执行常规任务，（比如写日志，写库等）。</li>
<li>获取消息，然后执行业务逻辑。</li>
</ul>
<h2><a id="3__31"></a>3 位移精度</h2>
<ul>
<li>最少一次 -&gt;  消息会被重复处理</li>
<li>最多一次 -&gt;  消息会丢失，但不会被重复处理。</li>
<li>精确一次 -&gt; 一定会被处理，且也只会处理一次。</li>
</ul>
<h2><a id="4___HW__LEO_36"></a>4 位移角色 &lt;- (留一节专门剖析HW 与 LEO)</h2>
<ul>
<li>上次提交位移 ：last committed offset</li>
<li>当前位置 ：current position</li>
<li>水位 ： High watermark</li>
<li>日志终端位移： （Log End Offset）</li>
</ul>
<h2><a id="5__43"></a>5 位移管理</h2>
<p>consumer的位移提交最终会向group coordinator来提交，不过这里重点需要重新说明一下：组协调者coordinator负责管理所有的Consumer实例。而且coordinator运行在broker上（通过选举出某个broker），不过请注意新版本coordinator只负责做组管理。</p>
<h4><a id="reblanceConsumer_46"></a>但是具体的reblance分区分配策略目前已经交由Consumer客户端。这样就解耦了组管理和分区分配。</h4>
<p>权利下放的优势：</p>
<ul>
<li>如果需要分配就貌似需要重启整个kafka集群。</li>
<li>在Consumer端可以定制分区分配策略。</li>
<li>每一个consumer位移提交时，都会向_consumer_offsets对应的分区上追加写入一条消息。如果某一个consumer为同一个group的同一个topic同一个分区提交多次位移，很显然我们只关心最新一次提交的位移。</li>
</ul>
<h2><a id="6_reblance_52"></a>6 reblance的触发条件</h2>
<ul>
<li>组订阅发生变更，比如基于正则表达式订阅，当匹配到新的topic创建时，组的订阅就会发生变更。</li>
<li>组的topic分区数发生变更，通过命令行脚本增加了订阅topic的分区数。</li>
<li>组成员发生变更：新加入组以及离开组。</li>
</ul>
<h2><a id="7_reblance__59"></a>7 reblance 分配策略</h2>
<h3><a id="71_range_61"></a>7.1 range分区分配策略</h3>
<p>举例如下：<br>
一个拥有十个分区（0,1,2…,9）的topic，相同group拥有三个consumerid为a,b,c的消费者：</p>
<ul>
<li>
<p>consumer a分配对应的分区号为[0,4),即0，1，2，3前面四个分区</p>
</li>
<li>
<p>consumer b 分配对应分区4，5，6中间三个分区</p>
</li>
<li>
<p>consumer c 分配对应分区7，8，9最后三个分区。</p>
<p>class RangeAssignor() extends PartitionAssignor with Logging {</p>
<pre><code>def assign(ctx: AssignmentContext) = {
  val valueFactory = (topic: String) =&gt; new mutable.HashMap[TopicAndPartition, ConsumerThreadId]
  val partitionAssignment =
    new Pool[String, mutable.Map[TopicAndPartition, ConsumerThreadId]](Some(valueFactory))
  for (topic &lt;- ctx.myTopicThreadIds.keySet) {
    val curConsumers = ctx.consumersForTopic(topic)
    val curPartitions: Seq[Int] = ctx.partitionsForTopic(topic)

    val nPartsPerConsumer = curPartitions.size / curConsumers.size
    val nConsumersWithExtraPart = curPartitions.size % curConsumers.size

    info("Consumer " + ctx.consumerId + " rebalancing the following partitions: " + curPartitions +
      " for topic " + topic + " with consumers: " + curConsumers)

    for (consumerThreadId &lt;- curConsumers) {
      val myConsumerPosition = curConsumers.indexOf(consumerThreadId)
      assert(myConsumerPosition &gt;= 0)
      val startPart = nPartsPerConsumer * myConsumerPosition + myConsumerPosition.min(nConsumersWithExtraPart)
      val nParts = nPartsPerConsumer + (if (myConsumerPosition + 1 &gt; nConsumersWithExtraPart) 0 else 1)

      /**
       *   Range-partition the sorted partitions to consumers for better locality.
       *  The first few consumers pick up an extra partition, if any.
       */
      if (nParts &lt;= 0)
        warn("No broker partitions consumed by consumer thread " + consumerThreadId + " for topic " + topic)
      else {
        for (i &lt;- startPart until startPart + nParts) {
          val partition = curPartitions(i)
          info(consumerThreadId + " attempting to claim partition " + partition)
          // record the partition ownership decision
          val assignmentForConsumer = partitionAssignment.getAndMaybePut(consumerThreadId.consumer)
          assignmentForConsumer += (TopicAndPartition(topic, partition) -&gt; consumerThreadId)
        }
      }
    }
  }
</code></pre>
<p>源码剖析如下：</p>
<pre><code>     curConsumers=(a,b,c)
     curPartitions=(0,1,2,3,4,5,6,7,8,9)
     nPartsPerConsumer=10/3  =3
     nConsumersWithExtraPart=10%3  =1
     
     a:
     myConsumerPosition= curConsumers.indexof(a) =0
     startPart= 3*0+0.min(1) = 0
     nParts = 3+(if (0 + 1 &gt; 1) 0 else 1)=3+1=4
     b:
     myConsumerPosition=1
     c:
     myConsumerPosition
</code></pre>
</li>
</ul>
<h3><a id="72_roundrobin_129"></a>7.2 round-robin分区分配策略</h3>
<p>如果同一个消费组内所有的消费者的订阅信息都是相同的，那么RoundRobinAssignor策略的分区分配会是均匀的。<br>
举例如下：<br>
假设消费组中有2个消费者C0和C1，都订阅了主题topic0 和 topic1，并且每个主题都有3个分区，进行hashCode 排序 后，顺序为：topic0_0、topic0_1、topic0_2、topic1_0、topic1_1、topic1_2。最终的分配结果为：</p>
<p>消费者consumer0：topic0_0、topic0_2 、 topic1_1<br>
消费者consumer1：topic0_1、topic1_0、 topic1_2</p>
<p>使用RoundRobin策略有两个前提条件必须满足：</p>
<ul>
<li>同一个Consumer Group里面的所有消费者的num.streams必须相等；</li>
<li>每个消费者订阅的主题必须相同。</li>
</ul>
<p>所以这里假设前面提到的2个消费者的num.streams = 2。RoundRobin策略的工作原理：将所有主题的分区组成 TopicAndPartition 列表，然后对 TopicAndPartition 列表按照 hashCode 进行排序，最后按照round-robin风格将分区分别分配给不同的消费者线程。</p>
<pre><code>val allTopicPartitions = ctx.partitionsForTopic.flatMap { case(topic, partitions) =&gt;
	  info("Consumer %s rebalancing the following partitions for topic %s: %s"
	       .format(ctx.consumerId, topic, partitions))
	  partitions.map(partition =&gt; {
	    TopicAndPartition(topic, partition)
	  })
	}.toSeq.sortWith((topicPartition1, topicPartition2) =&gt; {
	  /*
	   * Randomize the order by taking the hashcode to reduce the likelihood of all partitions of a given topic ending
	   * up on one consumer (if it has a high enough stream count).
	   */
	  topicPartition1.toString.hashCode &lt; topicPartition2.toString.hashCode
	})
</code></pre>
<h3><a id="73_StickyAssignor_158"></a>7.3 StickyAssignor分区分配策略（摘录）</h3>
<ul>
<li>分区的分配要尽可能的均匀；</li>
<li>分区的分配尽可能的与上次分配的保持相同。<br>
当两者发生冲突时，第一个目标优先于第二个目标。鉴于这两个目标，StickyAssignor策略的具体实现要比RangeAssignor和RoundRobinAssignor这两种分配策略要复杂很多。</li>
</ul>
<p>假设消费组内有3个消费者：C0、C1和C2，它们都订阅了4个主题：t0、t1、t2、t3，并且每个主题有2个分区，也就是说整个消费组订阅了t0p0、t0p1、t1p0、t1p1、t2p0、t2p1、t3p0、t3p1这8个分区。最终的分配结果如下：</p>
<pre><code>消费者C0：t0p0、t1p1、t3p0
消费者C1：t0p1、t2p0、t3p1
消费者C2：t1p0、t2p1
</code></pre>
<p>假设此时消费者C1脱离了消费组，那么消费组就会执行再平衡操作，进而消费分区会重新分配。如果采用RoundRobinAssignor策略，那么此时的分配结果如下：</p>
<pre><code>消费者C0：t0p0、t1p0、t2p0、t3p0
消费者C2：t0p1、t1p1、t2p1、t3p1
</code></pre>
<p>RoundRobinAssignor策略会按照消费者C0和C2进行重新轮询分配。而如果此时使用的是StickyAssignor策略，那么分配结果为：</p>
<pre><code>消费者C0：t0p0、t1p1、t3p0、t2p0
消费者C2：t1p0、t2p1、t0p1、t3p1
</code></pre>
<p>可以看到分配结果中保留了上一次分配中对于消费者C0和C2的所有分配结果，并将原来消费者C1的“负担”分配给了剩余的两个消费者C0和C2，最终C0和C2的分配仍然保持了均衡。</p>
<p>如果发生分区重分配，那么对于同一个分区而言有可能之前的消费者和新指派的消费者不是同一个，对于之前消费者进行到一半的处理还要在新指派的消费者中再次复现一遍，这显然很浪费系统资源。StickyAssignor策略如同其名称中的“sticky”一样，让分配策略具备一定的“粘性”，尽可能地让前后两次分配相同，进而减少系统资源的损耗以及其它异常情况的发生。</p>
<h2><a id="7_reblance_generation__184"></a>7 reblance generation （代代不同）</h2>
<p>主要作用在于防止无效的offset提交，原因在于若上一届的consumer成员因为某些原因延迟提交了offset,同时被踢出group组，那么新一届的group组成员分区分配结束后，老一届的consumer再次提交老的offset就会出问题。因此采用reblance generation ，老的请求就会被拒绝。</p>
<h2><a id="8_reblance__187"></a>8 reblance 扫尾工作</h2>
<p>每一次reblance操作之前，都会检查用户是否设置了自动提交位移，如果是，则帮助用户提交。如没有设置，会在监听器中回调用户的提交程序。</p>
<h2><a id="9__190"></a>9 总结</h2>
<p>本文翻阅大量资料，极少程度，耗费大量时间，实属不易，作者一直坚持原创，当然也借鉴了经典案例。</p>
<p>秦凯新 于深圳  2018 1:30</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>