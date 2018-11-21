---
layout:     post
title:      kafka集群管理重要操作指令运维兵书-kafka 商业环境实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本套技术专栏是作者（秦凯新）平时工作的总结和升华，通过从真实商业环境抽取案例进行总结和分享，并给出商业应用的调优建议和集群环境容量规划等内容，请持续关注本套博客。版权声明：禁止转载，欢迎学习。QQ邮箱地址：1120746959@qq.com，如有任何商业交流，可随时联系。					https://blog.csdn.net/shenshouniu/article/details/83721845				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>本套系列博客从真实商业环境抽取案例进行总结和分享，并给出Spark商业应用实战指导，请持续关注本套博客。版权声明：本套Spark商业应用实战归作者（秦凯新）所有，禁止转载，欢迎学习。</p>
<ul>
<li><a href="https://juejin.im/post/5bd464ccf265da0ac3735124" rel="nofollow">kafka 商业环境实战-kafka生产环境规划 </a></li>
<li><a href="https://juejin.im/post/5bd50b9451882529642b2581" rel="nofollow">kafka 商业环境实战-kafka生产者和消费者吞吐量测试 </a></li>
<li><a href="https://juejin.im/post/5bd51b32e51d457947024771" rel="nofollow">kafka 商业环境实战-kafka生产者Producer参数设置及参数调优建议  </a></li>
<li><a href="https://juejin.im/post/5bdf04c0e51d452c846ea051" rel="nofollow">kafka 商业环境实战-kafka集群管理重要操作指令运维兵书  </a></li>
</ul>
<h2><a id="1_kafka_8"></a>1 kafka数据分发测试指令</h2>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/4/166df31a6d1ed365?w=1027&amp;h=330&amp;f=png&amp;s=44304" alt=""></p>
<h2><a id="2_kafka_12"></a>2 kafka启动指令</h2>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/4/166df32c12ecd301?w=941&amp;h=459&amp;f=png&amp;s=47557" alt=""></p>
<h2><a id="3_Kafka_010_16"></a>3 Kafka 版本0.10指令汇总</h2>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/4/166df33ffbe8bac2?w=956&amp;h=745&amp;f=png&amp;s=116948" alt=""></p>
<h2><a id="3_Kafka__21"></a>3 Kafka 运维管理集锦</h2>
<ul>
<li>
<p>查看kafka topic列表，使用–list参数</p>
<pre><code>      bin/kafka-topics.sh --zookeeper 127.0.0.1:2181 --list
      __consumer_offsets
      lx_test_topic
      test
</code></pre>
</li>
<li>
<p>查看kafka特定topic的详情，使用–topic与–describe参数</p>
<pre><code>  bin/kafka-topics.sh --zookeeper 127.0.0.1:2181 --topic lx_test_topic --describe
  
  Topic:lx_test_topic     PartitionCount:1        ReplicationFactor:1     Configs:
      Topic: lx_test_topic    Partition: 0    Leader: 0       Replicas: 0     Isr: 0
</code></pre>
</li>
<li>
<p>查看consumer group列表，使用–list参数</p>
<p>同样根据新/旧版本的consumer</p>
<pre><code>  bin/kafka-consumer-groups.sh --new-consumer --bootstrap-server 127.0.0.1:9292 --list
  lx_test

  bin/kafka-consumer-groups.sh --zookeeper 127.0.0.1:2181 --list
  console-consumer-86845
  console-consumer-11967
</code></pre>
</li>
<li>
<p>查看特定consumer group 详情，使用–group与–describe参数</p>
<p>同样根据新/旧版本的consumer，分别指定bootstrap-server与zookeeper参数:</p>
<pre><code>  bin/kafka-consumer-groups.sh --new-consumer --bootstrap-server 127.0.0.1:9292 --group lx_test --describe
  GROUP                          TOPIC                          PARTITION  CURRENT-OFFSET  LOG-END-OFFSET  LAG             OWNER
  lx_test                        lx_test_topic             0          465             465             0               kafka-python-1.3.1_/127.0.0.1


  bin/kafka-consumer-groups.sh --zookeeper 127.0.0.1:2181 --group console-consumer-11967 --describe
  GROUP                          TOPIC                          PARTITION  CURRENT-OFFSET  LOG-END-OFFSET  LAG             OWNER
  Could not fetch offset from zookeeper for group console-consumer-11967 partition [lx_test_topic,0] due to missing offset data in zookeeper.
  console-consumer-11967         lx_test_topic             0          unknown         465             unknown         console-consumer-11967_aws-lx-1513787888172-d3a91f05-0
</code></pre>
</li>
<li>
<p>管理</p>
<pre><code>  创建主题（4个分区，2个副本）
  bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 2 --partitions 4 --topic test
</code></pre>
</li>
<li>
<p>查询</p>
<pre><code>  ## 查询集群描述
  bin/kafka-topics.sh --describe --zookeeper 
  
  ## 消费者列表查询
  bin/kafka-topics.sh --zookeeper 127.0.0.1:2181 --list
  
  ## 新消费者列表查询（支持0.9版本+）
  bin/kafka-consumer-groups.sh --new-consumer --bootstrap-server localhost:9092 --list
  
  ## 显示某个消费组的消费详情（仅支持offset存储在zookeeper上的）
  bin/kafka-run-class.sh kafka.tools.ConsumerOffsetChecker --zookeeper localhost:2181 --group test
  
  ## 显示某个消费组的消费详情（支持0.9版本+）
  bin/kafka-consumer-groups.sh --new-consumer --bootstrap-server localhost:9092 --describe --group test-consumer-group
</code></pre>
</li>
<li>
<p>发送和消费</p>
<pre><code>  ## 生产者
  bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test
  
  ## 消费者
  bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic test
  
  ## 新生产者（支持0.9版本+）
  bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test --producer.config config/producer.properties
  
  ## 新消费者（支持0.9版本+）
  bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --new-consumer --from-beginning --consumer.config config/consumer.properties
  
  ## 高级点的用法
  bin/kafka-simple-consumer-shell.sh --brist localhost:9092 --topic test --partition 0 --offset 1234  --max-messages 10
</code></pre>
</li>
<li>
<p>平衡leader</p>
<pre><code>  bin/kafka-preferred-replica-election.sh --zookeeper zk_host:port/chroot
</code></pre>
</li>
<li>
<p>增加副本</p>
<pre><code>  cat &gt; increase-replication-factor.json &lt;&lt;EOF
  {"version":1, "partitions":[
  {"topic":"__consumer_offsets","partition":0,"replicas":[0,1]},
  {"topic":"__consumer_offsets","partition":1,"replicas":[0,1]},
  {"topic":"__consumer_offsets","partition":2,"replicas":[0,1]},
  
bin/kafka-reassign-partitions.sh --zookeeper localhost:2181 --reassignment-json-file increase-replication-factor.json --execute

bin/kafka-reassign-partitions.sh --zookeeper localhost:2181 --reassignment-json-file increase-replication-factor.json --verify
</code></pre>
</li>
<li>
<p><a href="http://kafka-consumer-groups.sh" rel="nofollow">kafka-consumer-groups.sh</a> 在2.0增加的新功能（查看组信息与状态）</p>
<pre><code>  --简约信息
  bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092,localhost:9093,localhost:9094 
  --describe --group test-group --members
  
  CONSUMER-ID                                     HOST            CLIENT-ID       #PARTITIONS     

  consumer-1-aa3f2e15-d577-4c51-acd5-aa0d488cc131 /127.0.0.1      consumer-1      8               
  
  consumer-1-f3b11334-b9eb-4d4f-80d1-766446c77ee9 /127.0.0.1      consumer-1      8               
  
  consumer-1-658e4d7b-a561-4430-bbdf-c3ab59a18f3a /127.0.0.1      consumer-1      9 
  
  
  --详细信息
  bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092,localhost:9093,localhost:9094 
  --describe --group test-group --members --verbose 
  
  CONSUMER-ID                                     HOST            CLIENT-ID       #PARTITIONS     ASSIGNMENT

  consumer-1-aa3f2e15-d577-4c51-acd5-aa0d488cc131 /127.0.0.1      consumer-1      8               test-topic(9,10,11,12,13,14,15,16)
  
  consumer-1-f3b11334-b9eb-4d4f-80d1-766446c77ee9 /127.0.0.1      consumer-1      8               test-topic(17,18,19,20,21,22,23,24)
  
  consumer-1-658e4d7b-a561-4430-bbdf-c3ab59a18f3a /127.0.0.1      consumer-1      9               test-topic(0,1,2,3,4,5,6,7,8) 
  
  -- 组员状态信息
  bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092,localhost:9093,localhost:9094 
  --describe --group test-group --state
  
  COORDINATOR (ID)          ASSIGNMENT-STRATEGY       STATE                #MEMBERS
  
  localhost:9092 (0)        range                     Stable               3
</code></pre>
</li>
</ul>
<h2><a id="4_Kafka__151"></a>4 Kafka 测试指令汇总</h2>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/4/166df34e43bbd282?w=949&amp;h=695&amp;f=png&amp;s=92579" alt=""></p>
<p>上面命令中比较关键的参数包括：</p>
<ul>
<li>–num-records：  指定这次测试要发送的总消息数。笔者建议每次执行kafka-producer-perf-test脚本至少要跑够5分钟的时间，这样得出的测试结果才比较准确，因此这个参数最好设置大一些</li>
<li>–record-size：模拟消息的大小。这个参数最好和你生产环境中的平均消息大小保持一致，这样测试结果才能更准确地反映实际使用场景。假设你的业务中消息的平均大小是1MB，那么设置成该参数为1024就可以了。</li>
<li>–throughput：吞吐量上限阈值。如果没有TPS的限制，设置成-1就可以了。这里也是同样的原则：和你真实业务场景中的TPS目标保持一致。如果没有明确的预期，可以先设置成-1看下当前能够达到的最大TPS是多少。</li>
<li>–compression.type：设置消息压缩类型。生产环境中应该没有不启动压缩的吧？根据笔者的经验，目前Kafka与LZ4的组合效果最好，可以应用到生产环境。另外，Kafka社区已经在考虑加入对ZStandard的支持。ZStandard是Facebook开源的压缩算法，据称完胜目前其他所有压缩算法，让我们拭目以待吧。</li>
</ul>
<h2><a id="4__162"></a>4 总结</h2>
<p>本节内容主要探讨了kafka集群管理重要操作指令运维，可能部分截图来自github公开源码，部分是我的测试案例，如有雷同某位大神私有内容，请直接留言于我，我来重新修正案例。</p>
<p>秦凯新 于深圳</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>