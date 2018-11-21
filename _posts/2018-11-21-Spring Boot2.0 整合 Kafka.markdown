---
layout:     post
title:      Spring Boot2.0 整合 Kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>Kafka 概述</p>

<p>Apache Kafka 是一个分布式流处理平台，用于构建实时的数据管道和流式的应用.它可以让你发布和订阅流式的记录，可以储存流式的记录，并且有较好的容错性，可以在流式记录产生时就进行处理。</p>

<p>Apache Kafka是分布式发布-订阅消息系统，在 kafka官网上对 Kafka 的定义：一个分布式发布-订阅消息传递系统。</p>

<p>Kafka 特性</p>

<ol><li>高吞吐量、低延迟：kafka每秒可以处理几十万条消息，它的延迟最低只有几毫秒，每个topic可以分多个partition, consumer group 对partition进行consume操作；</li>
	<li>可扩展性：kafka集群支持热扩展；</li>
	<li>持久性、可靠性：消息被持久化到本地磁盘，并且支持数据备份防止数据丢失；</li>
	<li>容错性：允许集群中节点失败（若副本数量为n,则允许n-1个节点失败）；</li>
	<li>高并发：支持数千个客户端同时读写；</li>
	<li>支持实时在线处理和离线处理：可以使用Storm这种实时流处理系统对消息进行实时进行处理，同时还可以使用Hadoop这种批处理系统进行离线处理；</li>
</ol><p>Kafka 使用场景</p>

<ol><li>日志收集：一个公司可以用Kafka可以收集各种服务的log，通过kafka以统一接口服务的方式开放给各种consumer，例如Hadoop、Hbase、Solr等；</li>
	<li>消息系统：解耦和生产者和消费者、缓存消息等；</li>
	<li>用户活动跟踪：Kafka经常被用来记录web用户或者app用户的各种活动，如浏览网页、搜索、点击等活动，这些活动信息被各个服务器发布到kafka的topic中，然后订阅者通过订阅这些topic来做实时的监控分析，或者装载到Hadoop、数据仓库中做离线分析和挖掘；</li>
	<li>运营指标：Kafka也经常用来记录运营监控数据。包括收集各种分布式应用的数据，生产各种操作的集中反馈，比如报警和报告；</li>
	<li>流式处理：比如spark streaming和storm；</li>
	<li>事件源；</li>
</ol><p>Spring Boot2.0 + Kafka</p>

<p>1，安装配置Kafka ,Zookeeper</p>

<p>安装和配置过程很简单，就不详细说了，参考官网：<a href="http://kafka.apache.org/quickstart" rel="nofollow">http://kafka.apache.org/quickstart</a></p>

<p>使用命令启动Kafka： <code>bin``/kafka-server-start``.sh config``/server``.properties</code></p>

<p>下面给出我的环境：</p>

<pre class="has">
<code>Centos 7.5,  Kafka 2.11, Zookeeper-3.4.13,  JDK1.8+     </code></pre>

<p>2，创建 Spring Boot 项目</p>

<p>注意版本：该项目使用Spring Boot 2.0 +，低版本可能不对</p>

<ol><li>pom.xml引用</li>
</ol><pre class="has">
<code>               &lt;dependency&gt;
                   &lt;groupId&gt;org.springframework.boot&lt;/groupId&gt;
                   &lt;artifactId&gt;spring-boot-starter&lt;/artifactId&gt;
               &lt;/dependency&gt;
               &lt;dependency&gt;
                   &lt;groupId&gt;org.springframework.kafka&lt;/groupId&gt;
                   &lt;artifactId&gt;spring-kafka&lt;/artifactId&gt;
               &lt;/dependency&gt;
               &lt;dependency&gt;
                   &lt;groupId&gt;com.alibaba&lt;/groupId&gt;
                   &lt;artifactId&gt;fastjson&lt;/artifactId&gt;
                   &lt;version&gt;1.2.47&lt;/version&gt;
               &lt;/dependency&gt;</code></pre>

<ol><li>定义消息生产者<br>
	直接使用 KafkaTemplate 发送消息 ，Spring Boot自动装配，不需要自己定义一个Kafka配置类，吐槽一下网站的文章，全都是互相抄，全都写一个 ProduceConfig Consumerconfig 类, Kafka 的参数配置 硬编码在代码中，简直无法直视。。<br>
	定义一个泛型类<code>KafkaSender&lt;T&gt;</code> T 就是你需要发送的消息 对象，序列化使用阿里的 fastjson</li>
</ol><p>消息发送后，可以在回调类里面处理自己的业务，<code>ListenableFutureCallback</code> 类有两个方法，分别是 <code>onFailureon</code> 和 <code>onSuccess</code> ，实际场景可以在这两个方法，处理自己的具体业务，这里不做实现。</p>

<pre class="has">
<code>
       /**
        * 消息生产者
        *
        * @author Jarvis
        * @date 2018/8/3
        */
       @Component
       public class KafkaSender&lt;T&gt; {
       
           private Logger logger = LoggerFactory.getLogger(KafkaSender.class);
       
           @Autowired
           private KafkaTemplate&lt;String, Object&gt; kafkaTemplate;
       
           /**
            * kafka 发送消息
            *
            * @param obj 消息对象
            */
           public void send(T obj) {
               String jsonObj = JSON.toJSONString(obj);
               logger.info("------------ message = {}", jsonObj);
       
               //发送消息
               ListenableFuture&lt;SendResult&lt;String, Object&gt;&gt; future = kafkaTemplate.send("kafka.tut", jsonObj);
               future.addCallback(new ListenableFutureCallback&lt;SendResult&lt;String, Object&gt;&gt;() {
                   @Override
                   public void onFailure(Throwable throwable) {
                       logger.info("Produce: The message failed to be sent:" + throwable.getMessage());
                   }
       
                   @Override
                   public void onSuccess(SendResult&lt;String, Object&gt; stringObjectSendResult) {
                       //TODO 业务处理
                       logger.info("Produce: The message was sent successfully:");
                       logger.info("Produce: _+_+_+_+_+_+_+ result: " + stringObjectSendResult.toString());
                   }
               });
           }
       }
</code></pre>

<ol><li>定义消息消费者<br>
	使用<code>@KafkaListener</code> 注解监听 topics 消息，此处的<code>topics</code> 必须和 send 函数中的 一致</li>
</ol><p><code>@Header(KafkaHeaders.RECEIVED_TOPI</code> 直接获取 topic</p>

<pre class="has">
<code>
    /**
     * 监听kafka.tut 的 topic
     *
     * @param record
     * @param topic  topic
     */
    @KafkaListener(id = "tut", topics = "kafka.tut")
    public void listen(ConsumerRecord&lt;?, ?&gt; record, @Header(KafkaHeaders.RECEIVED_TOPIC) String topic) {
        //判断是否NULL
        Optional&lt;?&gt; kafkaMessage = Optional.ofNullable(record.value());

        if (kafkaMessage.isPresent()) {
            //获取消息
            Object message = kafkaMessage.get();

            logger.info("Receive： +++++++++++++++ Topic:" + topic);
            logger.info("Receive： +++++++++++++++ Record:" + record);
            logger.info("Receive： +++++++++++++++ Message:" + message);
        }
    }
</code></pre>

<ol><li>配置文件 application.yml</li>
</ol><pre class="has">
<code>
       spring:
         application:
           name: kafka-tutorial
         kafka:
           # 指定kafka 代理地址，可以多个
           bootstrap-servers: 192.168.10.100:9092
           producer:
             retries: 0
             # 每次批量发送消息的数量
             batch-size: 16384
             # 缓存容量
             buffer-memory: 33554432
             # 指定消息key和消息体的编解码方式
             key-serializer: org.apache.kafka.common.serialization.StringSerializer
             value-serializer: org.apache.kafka.common.serialization.StringSerializer
           consumer:
             # 指定默认消费者group id
             group-id: consumer-tutorial
             auto-commit-interval: 100
             auto-offset-reset: earliest
             enable-auto-commit: true
             # 指定消息key和消息体的编解码方式
             key-deserializer: org.apache.kafka.common.serialization.StringDeserializer
             value-deserializer: org.apache.kafka.common.serialization.StringDeserializer
           # 指定listener 容器中的线程数，用于提高并发量
           listener:
             concurrency: 3</code></pre>

<ol><li>直接使用 @Autowired 对类 KafkaSender 自动装配，然后调用 send 方法发送消息即可，下面给出代码：</li>
</ol><pre class="has">
<code>           @Autowired
           private KafkaSender&lt;User&gt; kafkaSender;
       
           @Test
           public void kafkaSend() throws InterruptedException {
               //模拟发消息
               for (int i = 0; i &lt; 5; i++) {
       
                   User user = new User();
                   user.setId(System.currentTimeMillis());
                   user.setMsg(UUID.randomUUID().toString());
                   user.setSendTime(new Date());
       
                   kafkaSender.send(message);
                   Thread.sleep(3000);
       
               }
           }
</code></pre>

<p>控制台可以看到执行成功：<br><img alt="" class="has" src="https://images2018.cnblogs.com/blog/278758/201808/278758-20180803161103590-1060801605.png"></p>

<p>在服务器执行 <code>bin/kafka-topics.sh --list --zookeeper localhost:2181</code> 可以看到topic<br><img alt="" class="has" src="https://images2018.cnblogs.com/blog/278758/201808/278758-20180803161122421-591754640.png"></p>

<p>Kafka如何保证数据的不丢失</p>

<p>1.生产者数据的不丢失</p>

<ul><li>新版本的producer采用异步发送机制。KafkaProducer.send(ProducerRecord)方法仅仅是把这条消息放入一个缓存中(即RecordAccumulator，本质上使用了队列来缓存记录)，同时后台的IO线程会不断扫描该缓存区，将满足条件的消息封装到某个batch中然后发送出去。显然，这个过程中就有一个数据丢失的窗口：若IO线程发送之前client端挂掉了，累积在accumulator中的数据的确有可能会丢失。 kafka的ack机制：在kafka发送数据的时候，每次发送消息都会有一个确认反馈机制，确保消息正常的能够被收到。</li>
	<li>如果是同步模式：ack机制能够保证数据的不丢失，如果ack设置为0，风险很大，一般不建议设置为0<br>
	producer.type=sync<br>
	request.required.acks=1</li>
	<li>如果是异步模式：通过buffer来进行控制数据的发送，有两个值来进行控制，时间阈值与消息的数量阈值，如果buffer满了数据还没有发送出去，如果设置的是立即清理模式，风险很大，一定要设置为阻塞模式<br>
	producer.type=async<br>
	request.required.acks=1<br>
	queue.buffering.max.ms=5000<br>
	queue.buffering.max.messages=10000<br>
	queue.enqueue.timeout.ms = -1<br>
	batch.num.messages=200</li>
	<li>结论：producer有丢数据的可能，但是可以通过配置保证消息的不丢失<br>
	2.消费者数据的不丢失</li>
	<li>如果在消息处理完成前就提交了offset，那么就有可能造成数据的丢失。由于Kafka consumer默认是自动提交位移的，所以在后台提交位移前一定要保证消息被正常处理了，因此不建议采用很重的处理逻辑，如果处理耗时很长，则建议把逻辑放到另一个线程中去做。为了避免数据丢失，现给出两点建议：<br>
	enable.auto.commit=false 关闭自动提交位移<br>
	在消息被完整处理之后再手动提交位移</li>
	<li>如果使用了storm，要开启storm的ackfail机制；</li>
	<li>如果没有使用storm，确认数据被完成处理之后，再更新offset值。低级API中需要手动控制offset值。通过offset commit 来保证数据的不丢失，kafka自己记录了每次消费的offset数值，下次继续消费的时候，接着上次的offset进行消费即可。</li>
</ul><p>源码 github：<a href="https://github.com/jarvisqi/java-tutorial/tree/master/kafka-tutorial" rel="nofollow">https://github.com/jarvisqi/java-tutorial/tree/master/kafka-tutorial</a></p>

<p>参考：</p>

<ol><li><a href="http://kafka.apache.org/quickstart" rel="nofollow">http://kafka.apache.org/quickstart</a></li>
	<li><a href="https://docs.spring.io/spring-kafka/reference/htmlsingle/#kafka" rel="nofollow">https://docs.spring.io/spring-kafka/reference/htmlsingle/#kafka</a></li>
	<li><a href="https://blog.csdn.net/tzs_1041218129/article/details/78988439" rel="nofollow">https://blog.csdn.net/tzs_1041218129/article/details/78988439</a>
	<p><a href="http://mobile.zzchanghong110.com/" rel="nofollow"><span style="color:#ffffff;">郑州泌尿科医院</span> </a></p>

	<p><a href="http://mobile.ytsgnk.com/" rel="nofollow"><span style="color:#ffffff;">郑州看男科多少钱</span> </a></p>

	<p><a href="http://mobile.zztongjiyiyuan.com/" rel="nofollow"><span style="color:#ffffff;">郑州哪家医院看男科好</span> </a></p>

	<p><a href="http://mobile.zztj120.com/" rel="nofollow"><span style="color:#ffffff;">郑州专业妇科</span></a></p>
	</li>
</ol>            </div>
                </div>