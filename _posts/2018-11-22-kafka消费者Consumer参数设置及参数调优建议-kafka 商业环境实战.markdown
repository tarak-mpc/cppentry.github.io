---
layout:     post
title:      kafka消费者Consumer参数设置及参数调优建议-kafka 商业环境实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本套技术专栏是作者（秦凯新）平时工作的总结和升华，通过从真实商业环境抽取案例进行总结和分享，并给出商业应用的调优建议和集群环境容量规划等内容，请持续关注本套博客。版权声明：禁止转载，欢迎学习。QQ邮箱地址：1120746959@qq.com，如有任何商业交流，可随时联系。					https://blog.csdn.net/shenshouniu/article/details/84075858				</div>
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
<li><a href="https://juejin.im/post/5be7e0556fb9a049a62c1792" rel="nofollow">kafka 商业环境实战-kafka之Consumer多种消费模式案例应用实战</a></li>
<li><a href="https://juejin.im/post/5bec10ca6fb9a049b13dc1e4" rel="nofollow">kafka 商业环境实战-kafka消费者Consumer参数设置及参数调优建议</a></li>
<li>[kafka 商业环境实战-kafka位移提交机制与消费者组的重平衡策略]</li>
<li>[kafka 商业环境实战-kafka消息Poll轮询机制原理深入剖析]</li>
<li>[kafka 商业环境实战-kafka副本与ISR同步机制原理深入剖析]</li>
<li>[kafka 商业环境实战-kafka精确一次语义EOS的原理深入剖析]</li>
<li>[kafka 商业环境实战-kafka消息的幂等性与事务支持机制深入剖析]</li>
<li>[kafka 商业环境实战-kafka集群Controller竞选与责任设计思路架构详解]</li>
<li>[kafka 商业环境实战-kafka集群消息格式之V1版本到V2版本的平滑过渡详解]</li>
<li>[kafka 商业环境实战-kafka集群水印与leader epoch对数据一致性保障的深入研究]</li>
<li>[kafka 商业环境实战-kafka调优过程在吞吐量，持久性，低延时，可用性等指标的折中选择研究]</li>
</ul>
<h2><a id="1__Consumer_Group_20"></a>1  消息的接收-&gt;基于Consumer Group</h2>
<p>Consumer Group 主要用于实现高伸缩性，高容错性的Consumer机制。因此，消息的接收是基于Consumer Group 的。组内多个Consumer实例可以同时读取Kafka消息，同一时刻一条消息只能被一个消费者消费，而且一旦某一个consumer “挂了”， Consumer Group 会立即将已经崩溃的Consumer负责的分区转交给其他Consumer来负责。从而保证 Consumer Group 能够正常工作。</p>
<h2><a id="2_Consumer_Group_23"></a>2 位移保存-&gt;基于Consumer Group</h2>
<p>说来奇怪，位移保存是基于Consumer Group，同时引入检查点模式，定期实现offset的持久化。</p>
<h2><a id="3_ZooKeeper_26"></a>3 位移提交-&gt;抛弃ZooKeeper</h2>
<p>Consumer会定期向kafka集群汇报自己消费数据的进度，这一过程叫做位移的提交。这一过程已经抛弃Zookeeper，因为Zookeeper只是一个协调服务组件，不能作为存储组件，高并发的读取势必造成Zk的压力。</p>
<ul>
<li>新版本位移提交是在kafka内部维护了一个内部Topic(_consumer_offsets)。</li>
<li>在kafka内部日志目录下面，总共有50个文件夹，每一个文件夹包含日志文件和索引文件。日志文件主要是K-V结构，（<a href="http://group.id" rel="nofollow">group.id</a>,topic,分区号）。</li>
<li>假设线上有很多的consumer和ConsumerGroup，通过对group.id做Hash求模运算，这50个文件夹就可以分散同时位移提交的压力。</li>
</ul>
<h2><a id="4__33"></a>4 官方案例</h2>
<h3><a id="41__35"></a>4.1 自动提交位移</h3>
<pre><code>     Properties props = new Properties();
     props.put("bootstrap.servers", "localhost:9092");
     props.put("group.id", "test");
     props.put("enable.auto.commit", "true");
     props.put("auto.commit.interval.ms", "1000");
     props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
     props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
     KafkaConsumer&lt;String, String&gt; consumer = new KafkaConsumer&lt;&gt;(props);
     consumer.subscribe(Arrays.asList("foo", "bar"));
     while (true) {
         ConsumerRecords&lt;String, String&gt; records = consumer.poll(100);
         for (ConsumerRecord&lt;String, String&gt; record : records)
             System.out.printf("offset = %d, key = %s, value = %s%n", record.offset(), record.key(), record.value());
     }
</code></pre>
<h3><a id="42__52"></a>4.2 手动提交位移</h3>
<pre><code>     Properties props = new Properties();
     props.put("bootstrap.servers", "localhost:9092");
     props.put("group.id", "test");
     props.put("enable.auto.commit", "false");
     props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
     props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
     KafkaConsumer&lt;String, String&gt; consumer = new KafkaConsumer&lt;&gt;(props);
     consumer.subscribe(Arrays.asList("foo", "bar"));
     final int minBatchSize = 200;
     List&lt;ConsumerRecord&lt;String, String&gt;&gt; buffer = new ArrayList&lt;&gt;();
     while (true) {
         ConsumerRecords&lt;String, String&gt; records = consumer.poll(100);
         for (ConsumerRecord&lt;String, String&gt; record : records) {
             buffer.add(record);
         }
         if (buffer.size() &gt;= minBatchSize) {
             insertIntoDb(buffer);
             consumer.commitSync();
             buffer.clear();
         }
     }
</code></pre>
<h2><a id="5_kafka_Consumer_76"></a>5 kafka Consumer参数设置</h2>
<hr>
<ul>
<li>consumer.poll(1000) 重要参数</li>
<li>新版本的Consumer的Poll方法使用了类似于Select I/O机制，因此所有相关事件（包括reblance，消息获取等）都发生在一个事件循环之中。</li>
<li>1000是一个超时时间，一旦拿到足够多的数据（参数设置），consumer.poll(1000)会立即返回 ConsumerRecords&lt;String, String&gt; records。</li>
<li>如果没有拿到足够多的数据，会阻塞1000ms，但不会超过1000ms就会返回。</li>
</ul>
<hr>
<ul>
<li>session. timeout. ms &lt;=  coordinator检测失败的时间</li>
<li>默认值是10s</li>
<li>该参数是 Consumer Group 主动检测 (组内成员comsummer)崩溃的时间间隔。若设置10min，那么Consumer Group的管理者（group coordinator）可能需要10分钟才能感受到。太漫长了是吧。</li>
</ul>
<hr>
<ul>
<li>max. poll. interval. ms &lt;= 处理逻辑最大时间</li>
<li>这个参数是0.10.1.0版本后新增的，可能很多地方看不到喔。这个参数需要根据实际业务处理时间进行设置，一旦Consumer处理不过来，就会被踢出Consumer Group<br>
。</li>
<li>注意：如果业务平均处理逻辑为1分钟，那么max. poll. interval. ms需要设置稍微大于1分钟即可，但是session. timeout. ms可以设置小一点（如10s），用于快速检测Consumer崩溃。</li>
</ul>
<hr>
<ul>
<li>auto.offset.reset</li>
<li>该属性指定了消费者在读取一个没有偏移量后者偏移量无效（消费者长时间失效当前的偏移量已经过时并且被删除了）的分区的情况下，应该作何处理，默认值是latest，也就是从最新记录读取数据（消费者启动之后生成的记录），另一个值是earliest，意思是在偏移量无效的情况下，消费者从起始位置开始读取数据。</li>
</ul>
<hr>
<ul>
<li>enable.auto.commit</li>
<li>对于精确到一次的语义，最好手动提交位移</li>
</ul>
<hr>
<ul>
<li>fetch.max.bytes</li>
<li>单次获取数据的最大消息数。</li>
</ul>
<hr>
<ul>
<li>max.poll.records  &lt;=  吞吐量</li>
<li>单次poll调用返回的最大消息数，如果处理逻辑很轻量，可以适当提高该值。</li>
<li>一次从kafka中poll出来的数据条数,max.poll.records条数据需要在在session.timeout.ms这个时间内处理完</li>
<li>默认值为500</li>
</ul>
<hr>
<ul>
<li>heartbeat. interval. ms &lt;= 居然拖家带口</li>
<li>heartbeat心跳主要用于沟通交流，及时返回请求响应。这个时间间隔真是越快越好。因为一旦出现reblance,那么就会将新的分配方案或者通知重新加入group的命令放进心跳响应中。</li>
</ul>
<hr>
<ul>
<li>connection. max. idle. ms &lt;= socket连接</li>
<li>kafka会定期的关闭空闲Socket连接。默认是9分钟。如果不在乎这些资源开销，推荐把这些参数值为-1，即不关闭这些空闲连接。</li>
</ul>
<hr>
<ul>
<li>request. timeout. ms</li>
<li>这个配置控制一次请求响应的最长等待时间。如果在超时时间内未得到响应，kafka要么重发这条消息，要么超过重试次数的情况下直接置为失败。</li>
<li>消息发送的最长等待时间.需大于session.timeout.ms这个时间</li>
</ul>
<hr>
<ul>
<li>fetch.min.bytes</li>
<li>server发送到消费端的最小数据，若是不满足这个数值则会等待直到满足指定大小。默认为1表示立即接收。</li>
</ul>
<hr>
<ul>
<li><a href="http://fetch.wait.max.ms" rel="nofollow">fetch.wait.max.ms</a></li>
<li>若是不满足fetch.min.bytes时，等待消费端请求的最长等待时间</li>
</ul>
<hr>
<ul>
<li><a href="http://group.initial.rebalance.delay.ms" rel="nofollow">group.initial.rebalance.delay.ms</a>  &lt;= 0.11 新功能</li>
<li>空消费组延时rebalance，主要在server.properties文件配置</li>
<li><a href="http://group.initial.rebalance.delay.ms" rel="nofollow">group.initial.rebalance.delay.ms</a>  &lt;=  牛逼了，我的kafka，防止成员加入请求后本应立即开启的rebalance</li>
<li>对于用户来说，这个改进最直接的效果就是新增了一个broker配置：<a href="http://group.initial.rebalance.delay.ms" rel="nofollow">group.initial.rebalance.delay.ms</a>，</li>
<li>默认是3秒钟。</li>
<li>主要作用是让coordinator推迟空消费组接收到成员加入请求后本应立即开启的rebalance。在实际使用时，假设你预估你的所有consumer组成员加入需要在10s内完成，那么你就可以设置该参数=10000。</li>
</ul>
<h2><a id="6__133"></a>6 线上采坑</h2>
<pre><code>org.apache.kafka.clients.consumer.CommitFailedException:
 Commit cannot be completed since the group has already rebalanced and assigned the partitions to another member. 
This means that the time between subsequent calls to poll() was longer than the configured session.timeout.ms, which typically implies that the poll loop is spending too much time message processing. 
You can address this either by increasing the session timeout or by reducing the maximum size of batches returned in poll() with max.poll.records. [com.bonc.framework.server.kafka.consumer.ConsumerLoop]
</code></pre>
<h6><a id="10session_timeout_ms_maxpollintervalms_140"></a>基于最新版本10，注意此版本session. timeout. ms 与max.poll.interval.ms进行功能分离了。</h6>
<ul>
<li>可以发现频繁reblance，并伴随者重复性消费，这是一个很严重的问题，就是处理逻辑过重,max.poll. <a href="http://interval.ms" rel="nofollow">interval.ms</a> 过小导致。发生的原因就是 poll（）的循环调用时间过长，出现了处理超时。此时只用调大max.poll. <a href="http://interval.ms" rel="nofollow">interval.ms</a> ，调小max.poll.records即可，同时要把request. timeout. ms设置大于max.poll. <a href="http://interval.ms" rel="nofollow">interval.ms</a></li>
</ul>
<h2><a id="7__144"></a>7 总结</h2>
<p>优化会继续，暂时把核心放在request. timeout. ms, max. poll. interval. ms，max.poll.records 上，避免因为处理逻辑过重，导致Consumer被频繁的踢出Consumer group。</p>
<p>秦凯新 于深圳</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>