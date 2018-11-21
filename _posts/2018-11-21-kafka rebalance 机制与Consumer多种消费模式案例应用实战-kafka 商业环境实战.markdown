---
layout:     post
title:      kafka rebalance 机制与Consumer多种消费模式案例应用实战-kafka 商业环境实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本套技术专栏是作者（秦凯新）平时工作的总结和升华，通过从真实商业环境抽取案例进行总结和分享，并给出商业应用的调优建议和集群环境容量规划等内容，请持续关注本套博客。版权声明：禁止转载，欢迎学习。QQ邮箱地址：1120746959@qq.com，如有任何商业交流，可随时联系。					https://blog.csdn.net/shenshouniu/article/details/84260665				</div>
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
<h2><a id="1_rebalance__21"></a>1 rebalance 何时触发？到底干嘛？流程如何？</h2>
<h3><a id="11_reblance__23"></a>1.1 reblance 何时触发</h3>
<ul>
<li>组订阅发生变更，比如基于正则表达式订阅，当匹配到新的topic创建时，组的订阅就会发生变更。</li>
<li>组的topic分区数发生变更，通过命令行脚本增加了订阅topic的分区数。</li>
<li>组成员发生变更：新加入组以及离开组。</li>
</ul>
<h3><a id="12_reblance__27"></a>1.2 reblance 到底干嘛</h3>
<p>一句话：多个Consumer订阅了一个Topic时，根据分区策略进行消费者订阅分区的重分配</p>
<h3><a id="13_Coordinator_Broker_32"></a>1.3 Coordinator 到底在那个Broker</h3>
<p>找到Coordinator的算法 与 找到_consumer_offsets目标分区的算法是一致的。</p>
<ul>
<li>第一步：确定目标分区：Math.abs(groupId.hashCode)%50，假设是12。</li>
<li>第二步：找到_consumer_offsets分区为10的Leader副本所在的Broker，那么该broker即为Group Coordinator。</li>
</ul>
<h3><a id="14_reblance__39"></a>1.4 reblance 流程如何</h3>
<p>reblance 流程流程整体如下图所示，值得强调的几点如下：</p>
<ul>
<li>Coordinator的角色由Broker端担任。</li>
<li>Group Leader 的角色主要有Consumer担任。</li>
<li>加入组请求（JoinGroup）=&gt;作用在于选择Group Leader。</li>
<li>同步组请求（SyncGroup）=&gt;作用在于确定分区分配方案给Coordinator，把方案响应给所有Consumer。<br>
<img src="https://user-gold-cdn.xitu.io/2018/11/19/1672bc350b917fee?w=978&amp;h=337&amp;f=png&amp;s=45433" alt=""></li>
</ul>
<h3><a id="15_reblance__48"></a>1.5 reblance 机制的好处</h3>
<ul>
<li>分区分配权利下放给客户端consumer，因此系统不用重启，既可以实现分区策略的变更。</li>
<li>用户可以自行实现机架感知分配方案。</li>
</ul>
<h3><a id="16__reblance_generation__52"></a>1.6  reblance generation 过滤无用请求</h3>
<ul>
<li>kafka引入 reblance generation ，就是为了防止Consumer group的无效Offset提交。若因为某些原因，consumer延迟提交了Offset，而该consumer被踢出了消费组，那么该Consumer再次提交位移时，携带的就是旧的generation了。</li>
</ul>
<h2><a id="2_reblance__57"></a>2 reblance 监听器应用级别实战</h2>
<ul>
<li>
<p>reblance 监听器解决用户把位移提交到外部存储的情况，在监听器中实现位移保存和位移的重定向。</p>
</li>
<li>
<p>onPartitionsRevoked :  rebalance开启新一轮的重平衡前会调用，一般用于手动提交位移，及审计功能</p>
</li>
<li>
<p>onPartitionsAssigned ：rebalance在重平衡结束后会调用，一般用于消费逻辑处理</p>
<pre><code>   Properties props = new Properties();
   props.put("bootstrap.servers", "localhost:9092");
   props.put("group.id", "test");
   props.put("enable.auto.commit", "false");
   props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
   props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
   KafkaConsumer&lt;String, String&gt; consumer = new KafkaConsumer&lt;&gt;(props);
   
   统计rebalance总时长
   final AtomicLong totalRebalanceTimeMs =new AtomicLong(0L)
   
   统计rebalance开始时刻
   final AtomicLong rebalanceStart =new AtomicLong(0L)
   
   
   1 重平衡监听
   consumer.subscribe(Arrays.asList("test-topic"), new ConsumerRebalanceListener(){
       
   @Override
      public void onPartitionsRevoked(Collection&lt;TopicPartition&gt; partitions) {
          for(TopicPartition tp : partitions){
          
              1 保存到外部存储
              saveToExternalStore(consumer.position(tp)) 
              
              2 手动提交位移
              //consumer.commitSync(toCommit);
          }
          
          rebalanceStart.set(System.currentTimeMillis())
      }
  }

  @Override
  public void onPartitionsAssigned(Collection&lt;TopicPartition&gt; partitions) {

      totalRebalanceTimeMs.addAndGet(System.currentTimeMillis()-rebalanceStart.get())
      
      for (TopicPartition tp : partitions) {
             
             consumer.seek(tp，readFromExternalStore(tp))
      }
  }
   });

  2 消息处理
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
</li>
</ul>
<h2><a id="3_Consumer_120"></a>3 Consumer组内消息均衡实战</h2>
<h3><a id="31_Consumer__122"></a>3.1 Consumer 单线程封装，实现多个消费者来消费（浪费资源）</h3>
<p>实例主题：</p>
<ul>
<li>ConsumerGroup 实现组封装</li>
<li>ConsumerRunnable 每个线程维护私有的KafkaConsumer实例</li>
</ul>
<hr>
<pre><code>public class  Main {

    public static void main(String[] args) {
        String brokerList = "localhost:9092";
        String groupId = "testGroup1";
        String topic = "test-topic";
        int consumerNum = 3;

        核心对外封装
        ConsumerGroup consumerGroup = new ConsumerGroup(consumerNum, groupId, topic, brokerList);
        consumerGroup.execute();
    }
}
</code></pre>
<hr>
<pre><code>import java.util.ArrayList;
import java.util.List;
public class ConsumerGroup {    

    private List&lt;ConsumerRunnable&gt; consumers;
    
    public ConsumerGroup(int consumerNum, String groupId, String topic, String brokerList) {
        consumers = new ArrayList&lt;&gt;(consumerNum);
        for (int i = 0; i &lt; consumerNum; ++i) {
            ConsumerRunnable consumerThread = new ConsumerRunnable(brokerList, groupId, topic);
            consumers.add(consumerThread);
        }
    }
    public void execute() {
        for (ConsumerRunnable task : consumers) {
            new Thread(task).start();
        }
    }
}
</code></pre>
<hr>
<pre><code>import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.KafkaConsumer;
import java.util.Arrays;
import java.util.Properties;

public class ConsumerRunnable implements Runnable {

    private final KafkaConsumer&lt;String, String&gt; consumer;

    public ConsumerRunnable(String brokerList, String groupId, String topic) {
        Properties props = new Properties();
        props.put("bootstrap.servers", brokerList);
        props.put("group.id", groupId);
        props.put("enable.auto.commit", "true");        //本例使用自动提交位移
        props.put("auto.commit.interval.ms", "1000");
        props.put("session.timeout.ms", "30000");
        props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        this.consumer = new KafkaConsumer&lt;&gt;(props);
        consumer.subscribe(Arrays.asList(topic));   // 本例使用分区副本自动分配策略
    }

    @Override
    public void run() {
        while (true) {
            ConsumerRecords&lt;String, String&gt; records = consumer.poll(200);   
            for (ConsumerRecord&lt;String, String&gt; record : records) {
                System.out.println(Thread.currentThread().getName() + " consumed " + record.partition() +
                        "th message with offset: " + record.offset());
            }
        }
    }
}
</code></pre>
<hr>
<h3><a id="32_Consumerconsumer_201"></a>3.2 一个Consumer，内部实现多线程消费（consumer压力过大）</h3>
<p>实例主题：</p>
<ul>
<li>ConsumerHandler 单一的Consumer实例，poll后里面会跑一个线程池，执行多个Processor线程来处理</li>
<li>Processor 业务逻辑处理方法</li>
</ul>
<p>进一步优化建议；</p>
<ul>
<li>ConsumerHandler 设置手动提交位移，负责最终位移提交consumer.commitSync();。</li>
<li>ConsumerHandler设置一个全局的Map&lt;TopicPartion,OffsetAndMetadata&gt; offsets，来管理Processor消费的位移。</li>
<li>Processor 负责批处理完消息后，得到消息的最大位移，并更新offsets数组</li>
<li>ConsumerHandler 根据 offsets，位移提交后会清空offsets集合。</li>
<li>ConsumerHandler设置重平衡监听</li>
</ul>
<hr>
<pre><code>public class Main {
    public static void main(String[] args) {
    
        String brokerList = "localhost:9092,localhost:9093,localhost:9094";
        String groupId = "group2";
        String topic = "test-topic";
        int workerNum = 5;

        ConsumerHandler consumers = new ConsumerHandler(brokerList, groupId, topic);
        consumers.execute(workerNum);
        try {
            Thread.sleep(1000000);
        } catch (InterruptedException ignored) {}
        consumers.shutdown();
    }
}
</code></pre>
<hr>
<pre><code>import java.util.Arrays;
import java.util.Properties;
import java.util.concurrent.ArrayBlockingQueue;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.ThreadPoolExecutor;
import java.util.concurrent.TimeUnit;

public class ConsumerHandler {

    private final KafkaConsumer&lt;String, String&gt; consumer;
    private ExecutorService executors;

    public ConsumerHandler(String brokerList, String groupId, String topic) {
        Properties props = new Properties();
        props.put("bootstrap.servers", brokerList);
        props.put("group.id", groupId);
        props.put("enable.auto.commit", "true");
        props.put("auto.commit.interval.ms", "1000");
        props.put("session.timeout.ms", "30000");
        props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        consumer = new KafkaConsumer&lt;&gt;(props);
        consumer.subscribe(Arrays.asList(topic));
    }
    public void execute(int workerNum) {
    
        executors = new ThreadPoolExecutor(workerNum, workerNum, 0L, TimeUnit.MILLISECONDS,
                new ArrayBlockingQueue&lt;&gt;(1000), new ThreadPoolExecutor.CallerRunsPolicy());
                
        while (true) {
            ConsumerRecords&lt;String, String&gt; records = consumer.poll(200);
            
            for (final ConsumerRecord record : records) {
                executors.submit(new Processor(record));
            }
            
            
        }
    }
    public void shutdown() {
        if (consumer != null) {
            consumer.close();
        }
        if (executors != null) {
            executors.shutdown();
        }
        try {
            if (!executors.awaitTermination(10, TimeUnit.SECONDS)) {
                System.out.println("Timeout.... Ignore for this case");
            }
        } catch (InterruptedException ignored) {
            System.out.println("Other thread interrupted this shutdown, ignore for this case.");
            Thread.currentThread().interrupt();
        }
    }

}
</code></pre>
<hr>
<pre><code>import org.apache.kafka.clients.consumer.ConsumerRecord;
public class Processor implements Runnable {

    private ConsumerRecord&lt;String, String&gt; consumerRecord;

    public Processor(ConsumerRecord record) {
        this.consumerRecord = record;
    }

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName() + " consumed " + consumerRecord.partition()
            + "th message with offset: " + consumerRecord.offset());
    }
}   
</code></pre>
<h3><a id="33__307"></a>3.3 方案对比</h3>
<ul>
<li>第一种方案：建议采用 Consumer 单线程封装，实现多个消费者来消费（浪费资源），这样能很好地保证分区内消费的顺序，同时也没有线程切换的开销。</li>
<li>第二种方案：实现复杂，问题在于可能无法维护分区内的消息顺序，注意消息处理和消息接收解耦了。</li>
</ul>
<h3><a id="4__ConsumerStandalone__Consumer_312"></a>4  Consumer指定分区消费案例实战（Standalone  Consumer）</h3>
<ul>
<li>
<p>Standalone Consumer  assign 用于接收指定分区列表的消息和Subscribe是矛盾的。只能二选一。</p>
</li>
<li>
<p>多个 Consumer 实例消费一个 Topic 借助于 group reblance可谓是天作之合。</p>
</li>
<li>
<p>若要精准控制，assign逃不了。</p>
<pre><code>      poperties props = new Properties();
      props.put("bootstrap.servers", brokerList);
      props.put("group.id", groupId);
      props.put("enable.auto.commit", "false");
      props.put("auto.commit.interval.ms", "1000");
      props.put("session.timeout.ms", "30000");
      props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
      props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
      consumer = new KafkaConsumer&lt;&gt;(props);
      
      List&lt;TopicPartion&gt; partitions = new ArrayList&lt;&gt;();
      
      List&lt;PartitionInfo&gt;  allPartitions = consumer.partitionsFor("kaiXinTopic")
      
      if(allPartitions != null &amp;&amp; !allPartitions.isEmpty){
          for(PartitionInfo partitionInfo : allPartitions){
          
              partitions.add(new TopicPartition(partitionInfo.topic(),partitionInfo.partition()))
          }
          
          consumer.assign(partitions)
      }
      
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
</code></pre>
</li>
</ul>
<h3><a id="_354"></a>结语</h3>
<p>本文综合了多本Kafka实战书籍和博客，为了写好本文，参考了大量资料，进行了语言的重组，辛苦成文，各自珍惜！</p>
<p>秦凯新  2181119 2123</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>