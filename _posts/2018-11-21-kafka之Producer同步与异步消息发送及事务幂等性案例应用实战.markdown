---
layout:     post
title:      kafka之Producer同步与异步消息发送及事务幂等性案例应用实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本套技术专栏是作者（秦凯新）平时工作的总结和升华，通过从真实商业环境抽取案例进行总结和分享，并给出商业应用的调优建议和集群环境容量规划等内容，请持续关注本套博客。版权声明：禁止转载，欢迎学习。QQ邮箱地址：1120746959@qq.com，如有任何商业交流，可随时联系。					https://blog.csdn.net/shenshouniu/article/details/83960012				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>本套系列博客从真实商业环境抽取案例进行总结和分享，并给出Spark商业应用实战指导，请持续关注本套博客。版权声明：本套Spark商业应用实战归作者（秦凯新）所有，禁止转载，欢迎学习。</p>
<ul>
<li><a href="https://blog.csdn.net/shenshouniu/article/details/83719129" rel="nofollow">秦凯新的技术社区-大数据商业实战系列全集目录  </a></li>
<li><a href="https://juejin.im/post/5bd464ccf265da0ac3735124" rel="nofollow">kafka 商业环境实战-kafka生产环境规划 </a></li>
<li><a href="https://juejin.im/post/5bd50b9451882529642b2581" rel="nofollow">kafka 商业环境实战-kafka生产者和消费者吞吐量测试 </a></li>
<li><a href="https://juejin.im/post/5bd51b32e51d457947024771" rel="nofollow">kafka 商业环境实战-kafka生产者Producer参数设置及参数调优建议  </a></li>
<li><a href="https://juejin.im/post/5be799f851882558512137f7" rel="nofollow">kafka 商业环境实战-kafka集群Broker端参数设置及调优准则建议</a></li>
<li><a href="https://juejin.im/post/5be7ded25188253b6e5c085c" rel="nofollow">kafka 商业环境实战-kafka之Producer同步与异步消息发送及事务幂等性案例应用实战</a></li>
<li><a href="https://juejin.im/post/5be7e0556fb9a049a62c1792" rel="nofollow">kafka 商业环境实战-kafka之Consumer多种消费模式案例应用实战</a></li>
</ul>
<h2><a id="1__13"></a>1 我很安全</h2>
<p>为何惊出此言？内心惶恐。kafka的Producer是线程安全的，用户可以非常非常放心的在多线程中使用。</p>
<p>但是官方建议：通常情况下，一个线程维护一个kafka 的producer的效率会更高。</p>
<h2><a id="2_Producer__19"></a>2 Producer 消息发送流程</h2>
<ul>
<li>第一步：封装ProducerRecord</li>
<li>第二步：分区器Partioner进行数据路由，选择某一个Topic分区。如果没有指定key，消息会被均匀的分配到所有分区。</li>
<li>第三步：确定好分区，就会找分区对应的leader，接下来就是副本同步机制。</li>
</ul>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/11/16701be5d47d9925?w=1056&amp;h=378&amp;f=png&amp;s=293194" alt=""></p>
<h2><a id="3_Producer_27"></a>3 Producer官方实例</h2>
<h3><a id="31_Fire_and_Fogret__29"></a>3.1 Fire and Fogret案例 （无所谓心态）</h3>
<ul>
<li>
<h5><a id="_31"></a>发送之后便不再理会发送结果</h5>
<pre><code>  Properties props = new Properties();
   props.put("bootstrap.servers", "localhost:9092");
   props.put("acks", "all");
   props.put("retries", 0);
   props.put("batch.size", 16384);
   props.put("linger.ms", 1);
   props.put("buffer.memory", 33554432);
   props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
   props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");
  
   Producer&lt;String, String&gt; producer = new KafkaProducer&lt;&gt;(props);
   for (int i = 0; i &lt; 100; i++)
       producer.send(new ProducerRecord&lt;String, String&gt;("my-topic", Integer.toString(i), Integer.toString(i)));
  
   producer.close();
</code></pre>
</li>
</ul>
<h3><a id="32___50"></a>3.2 异步回调官方案例 （不阻塞）</h3>
<ul>
<li>
<h5><a id="JavaProducersendJavaFuture_51"></a>JavaProducer的send方法会返回一个JavaFuture对象供用户稍后获取发送结果。这就是回调机制。</h5>
</li>
<li>
<p>Fully non-blocking usage can make use of the Callback parameter to provide a callback that will be invoked when the request is complete.</p>
</li>
<li>
<p>RecordMetadata 和 Exception 不可能同时为空，消息发送成功时，Exception为null，消息发送失败时，metadata为空。</p>
<pre><code>   ProducerRecord&lt;byte[],byte[]&gt; record = new ProducerRecord&lt;byte[],byte[]&gt;("the-topic", key, value);
   
   producer.send(myRecord,
                 new Callback() {
                     public void onCompletion(RecordMetadata metadata, Exception e) {
                         if(e != null) {
                            e.printStackTrace();
                         } else {
                            System.out.println("The offset of the record we just sent is: " + metadata.offset());
                         }
                     }
                 });
</code></pre>
</li>
</ul>
<h3><a id="33___68"></a>3.3 同步发送官方案例 （阻塞）</h3>
<ul>
<li>
<p>通过 producer.send（record)返回Future对象，通过调用Future.get()进行无限等待结果返回。</p>
<pre><code> producer.send（record).get()
</code></pre>
</li>
</ul>
<h3><a id="34____74"></a>3.4  基于事务发送官方案例 （原子性和幂等性）</h3>
<ul>
<li>
<p>From Kafka 0.11, the KafkaProducer supports two additional modes: the idempotent producer<br>
and the transactional producer. The idempotent producer strengthens Kafka’s delivery<br>
semantics from at least once to exactly once delivery. In particular producer retries will<br>
no longer introduce duplicates. The transactional producer allows an application to send<br>
messages to multiple partitions (and topics!) atomically.</p>
</li>
<li>
<p>To enable idempotence, the enable.idempotence configuration must be set to true. If set,<br>
the retries config will default to Integer.MAX_VALUE and the acks config will default to all.<br>
There are no API changes for the idempotent producer, so existing applications will not need to be modified to take advantage of this feature.</p>
<pre><code>Properties props = new Properties();
props.put("bootstrap.servers", "localhost:9092");
props.put("transactional.id", "my-transactional-id");
Producer&lt;String, String&gt; producer = new KafkaProducer&lt;&gt;(props, new StringSerializer(), 
new StringSerializer());

producer.initTransactions();

try {
    producer.beginTransaction();
    for (int i = 0; i &lt; 100; i++)
        producer.send(new ProducerRecord&lt;&gt;("my-topic", Integer.toString(i), Integer.toString(i)));
    producer.commitTransaction();
} catch (ProducerFencedException | OutOfOrderSequenceException | AuthorizationException e) {
    // We can't recover from these exceptions, so our only option is to close the producer and exit.
    producer.close();
} catch (KafkaException e) {
    // For all other exceptions, just abort the transaction and try again.
    producer.abortTransaction();
}
producer.close();
</code></pre>
</li>
<li>
<p>As is hinted at in the example, there can be only one open transaction per producer. All messages sent between the beginTransaction() and commitTransaction() calls will be part of a single transaction. When the <a href="http://transactional.id" rel="nofollow">transactional.id</a> is specified, all messages sent by the producer must be part of a transaction.</p>
</li>
</ul>
<h3><a id="35__RetriableException_110"></a>3.5  可重试异常（继承RetriableException）</h3>
<ul>
<li>LeaderNotAvailableException :分区的Leader副本不可用，这可能是换届选举导致的瞬时的异常，重试几次就可以恢复</li>
<li>NotControllerException:Controller主要是用来选择分区副本和每一个分区leader的副本信息，主要负责统一管理分区信息等，也可能是选举所致。</li>
<li>NetWorkerException :瞬时网络故障异常所致。</li>
</ul>
<h3><a id="36__116"></a>3.6 不可重试异常</h3>
<ul>
<li>SerializationException:序列化失败异常</li>
<li>RecordToolLargeException:消息尺寸过大导致。</li>
</ul>
<h3><a id="37__122"></a>3.7 异常的区别对待</h3>
<pre><code>     producer.send(myRecord,
                   new Callback() {
                       public void onCompletion(RecordMetadata metadata, Exception e) {
                           if(e ==null){
                               //正常处理逻辑
                               System.out.println("The offset of the record we just sent is: " + metadata.offset()); 
                               
                           }else{
                                   
                                 if(e instanceof RetriableException) {
                                    //处理可重试异常
                                    ......
                                 } else {
                                    //处理不可重试异常
                                    ......
                                 }
                           }
                       }
                   });
</code></pre>
<h3><a id="38_Producer_144"></a>3.8 Producer的绅士关闭</h3>
<ul>
<li>producer.close()：优先把消息处理完毕，优雅退出。</li>
<li>producer.close(timeout): 超时时，强制关闭。</li>
</ul>
<h2><a id="4__151"></a>4 总结</h2>
<p>为了能够证明技术就是一层窗户纸，我会把kafka剖析的体无完肤。</p>
<p>秦凯新 于深圳 2018</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>