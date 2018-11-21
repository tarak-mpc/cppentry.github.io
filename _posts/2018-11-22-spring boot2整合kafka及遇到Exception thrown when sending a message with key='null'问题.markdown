---
layout:     post
title:      spring boot2整合kafka及遇到Exception thrown when sending a message with key='null'问题
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/zcc_heu/article/details/80632581				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="spring-boot2整合kafka及遇到exception-thrown-when-sending-a-message-with-keynull问题">spring boot2整合kafka及遇到Exception thrown when sending a message with key=’null’问题</h2>

<ol>
<li><p>最近在学习spring boot2和kafka。就用学着使用spring boot2与kafka集成。项目环境</p>

<ul><li>开发工具：IDEA</li>
<li>spring kafka ：2.1.6.RELEASE</li>
<li>spring boot2：2.0.2.RELEASE</li>
<li>Apache kafka：2.11-1.0.0</li></ul></li>
</ol>

<p>项目的github地址：<a href="https://github.com/sweetcczhang/springkafka" rel="nofollow">https://github.com/sweetcczhang/springkafka</a></p>

<ol>
<li><p>项目目录 <br>
<img src="https://img-blog.csdn.net/20180609141636458?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pjY19oZXU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p></li>
<li><p>application.properties文件</p>

<pre class="prettyprint"><code class="language-yaml hljs avrasm">
<span class="hljs-preprocessor">#kafka server address</span>

spring<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.bootstrap</span>-servers=<span class="hljs-number">10.108</span><span class="hljs-number">.208</span><span class="hljs-number">.51</span>:<span class="hljs-number">9092</span>


<span class="hljs-preprocessor"># Provider</span>

spring<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.producer</span><span class="hljs-preprocessor">.retries</span>=<span class="hljs-number">0</span>
spring<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.producer</span><span class="hljs-preprocessor">.batch</span>-size=<span class="hljs-number">16384</span>

<span class="hljs-preprocessor"># 指生产者的key和value的编码方式</span>

spring<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.producer</span><span class="hljs-preprocessor">.key</span>-serializer=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.common</span><span class="hljs-preprocessor">.serialization</span><span class="hljs-preprocessor">.StringSerializer</span>
spring<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.producer</span><span class="hljs-preprocessor">.value</span>-serializer=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.common</span><span class="hljs-preprocessor">.serialization</span><span class="hljs-preprocessor">.StringSerializer</span>


<span class="hljs-preprocessor"># Consumer</span>


<span class="hljs-preprocessor">#消费者组</span>

spring<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.group</span>-id=test-consumer-group
spring<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.auto</span>-offset-reset=earliest

<span class="hljs-preprocessor"># 指定消费者的解码方式</span>

spring<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.key</span>-deserializer=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.common</span><span class="hljs-preprocessor">.serialization</span><span class="hljs-preprocessor">.StringDeserializer</span>
spring<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.value</span>-deserializer=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.common</span><span class="hljs-preprocessor">.serialization</span><span class="hljs-preprocessor">.StringDeserializer</span>


<span class="hljs-preprocessor"># 日志</span>

spring<span class="hljs-preprocessor">.output</span><span class="hljs-preprocessor">.ansi</span><span class="hljs-preprocessor">.enabled</span>=DETECT
debug=true</code></pre></li>
<li><p>生产者配置文件KafkaProviderConfig.java</p>

<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-annotation">@Configuration</span>
<span class="hljs-annotation">@EnableKafka</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">KafkaProviderConfig</span> {</span>

   <span class="hljs-annotation">@Value</span>(<span class="hljs-string">"${spring.kafka.bootstrap-servers}"</span>)
   <span class="hljs-keyword">private</span> String bootstrapServers;

   <span class="hljs-annotation">@Value</span>(<span class="hljs-string">"${spring.kafka.producer.key-serializer}"</span>)
   <span class="hljs-keyword">private</span> String keySerializer;

   <span class="hljs-annotation">@Value</span>(<span class="hljs-string">"${spring.kafka.producer.value-serializer}"</span>)
   <span class="hljs-keyword">private</span> String valueSerializer;

   <span class="hljs-annotation">@Bean</span>
   <span class="hljs-keyword">public</span> Map&lt;String,Object&gt; <span class="hljs-title">producerConfig</span>(){
       Map&lt;String,Object&gt; props = <span class="hljs-keyword">new</span> HashMap&lt;&gt;();
       props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapServers);
       props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, keySerializer);
       props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, valueSerializer);
       <span class="hljs-keyword">return</span> props;
   }

   <span class="hljs-annotation">@Bean</span>
   <span class="hljs-keyword">public</span> ProducerFactory&lt;String,String&gt; <span class="hljs-title">producerFactory</span>(){
       <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> DefaultKafkaProducerFactory&lt;&gt;(producerConfig());
   }

   <span class="hljs-annotation">@Bean</span>
   <span class="hljs-keyword">public</span> KafkaTemplate&lt;String,String&gt; <span class="hljs-title">kafkaTemplate</span>(){
       <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> KafkaTemplate&lt;&gt;(producerFactory());
   }
}</code></pre></li>
<li><p>生产者产生消息发送到kafka</p>

<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-annotation">@Component</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">KafkaSender</span> {</span>
   <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> Logger logger = LoggerFactory.getLogger(KafkaSender.class);

   <span class="hljs-annotation">@Autowired</span>
   <span class="hljs-keyword">private</span> KafkaTemplate&lt;String,String&gt; kafkaTemplate;

   <span class="hljs-keyword">private</span> Gson gson = <span class="hljs-keyword">new</span> GsonBuilder().create();

   <span class="hljs-comment">//发送消息的方法</span>
   <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">send</span>(){
       Message message = <span class="hljs-keyword">new</span> Message();
       message.setId(System.currentTimeMillis());
       message.setMsg(UUID.randomUUID().toString());
       message.setSendTime(<span class="hljs-keyword">new</span> Date());
       logger.info(<span class="hljs-string">"+++++++++++++++++++ message = {}"</span>, gson.toJson(message));
       kafkaTemplate.send(<span class="hljs-string">"sweetzcc"</span>,gson.toJson(message);
   }
}</code></pre></li>
<li><p>消费者配置</p>

<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-annotation">@Configuration</span>
<span class="hljs-annotation">@EnableKafka</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">KafkaConsumerConfig</span> {</span>

   <span class="hljs-annotation">@Value</span>(<span class="hljs-string">"${spring.kafka.bootstrap-servers}"</span>)
   <span class="hljs-keyword">private</span> String bootstrapServer;

   <span class="hljs-annotation">@Value</span>(<span class="hljs-string">"${spring.kafka.consumer.key-deserializer}"</span>)
   <span class="hljs-keyword">private</span> String keySerializer;

   <span class="hljs-annotation">@Value</span>(<span class="hljs-string">"${spring.kafka.consumer.value-deserializer}"</span>)
   <span class="hljs-keyword">private</span> String valueSerializer;

   <span class="hljs-annotation">@Value</span>(<span class="hljs-string">"${spring.kafka.consumer.group-id}"</span>)
   <span class="hljs-keyword">private</span> String groupId;

   <span class="hljs-annotation">@Value</span>(<span class="hljs-string">"${spring.kafka.consumer.auto-offset-reset}"</span>)
   <span class="hljs-keyword">private</span> String autoOffsetReset;

   <span class="hljs-annotation">@Bean</span>
   <span class="hljs-keyword">public</span> Map&lt;String,Object&gt; <span class="hljs-title">consumerConfig</span>(){
       Map&lt;String,Object&gt; props = <span class="hljs-keyword">new</span> HashMap&lt;&gt;();
       props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapServer);
       props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, keySerializer);
       props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, valueSerializer);
       props.put(ConsumerConfig.GROUP_ID_CONFIG, groupId);
       props.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, autoOffsetReset);
       <span class="hljs-keyword">return</span> props;
   }

   <span class="hljs-annotation">@Bean</span>
   <span class="hljs-keyword">public</span> ConsumerFactory&lt;String,String&gt; <span class="hljs-title">consumerFactory</span>(){
       <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> DefaultKafkaConsumerFactory&lt;&gt;(consumerConfig());
   }

   <span class="hljs-annotation">@Bean</span>
   <span class="hljs-keyword">public</span> KafkaListenerContainerFactory&lt;ConcurrentMessageListenerContainer&lt;String, String&gt;&gt; <span class="hljs-title">kafkaListenerContainerFactory</span>(){
       ConcurrentKafkaListenerContainerFactory&lt;String,String&gt; factory = <span class="hljs-keyword">new</span> ConcurrentKafkaListenerContainerFactory&lt;&gt;();
       factory.setConsumerFactory(consumerFactory());
       <span class="hljs-keyword">return</span> factory;
   }
}</code></pre></li>
<li><p>消费者</p>

<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-annotation">@Component</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">KafkaReceiver</span> {</span>
   <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> Logger logger = LoggerFactory.getLogger(KafkaReceiver.class);

   <span class="hljs-annotation">@KafkaListener</span>(topics = <span class="hljs-string">"sweetzcc"</span>)
   <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">listen</span>(@Payload String message){
       logger.info(<span class="hljs-string">"received message={}"</span>,message);
   }
}</code></pre></li>
<li><p>运行测试</p>

<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-annotation">@EnableKafka</span>
<span class="hljs-annotation">@SpringBootApplication</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">SpringKafkaApplication</span> <span class="hljs-keyword">implements</span> <span class="hljs-title">CommandLineRunner</span> {</span>

<span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) {
    SpringApplication.run(SpringKafkaApplication.class, args);
}

<span class="hljs-annotation">@Autowired</span>
<span class="hljs-keyword">private</span> KafkaSender kafkaSender;

<span class="hljs-annotation">@Override</span>
<span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">run</span>(String... strings) <span class="hljs-keyword">throws</span> Exception {
    <span class="hljs-keyword">for</span> (<span class="hljs-keyword">int</span> i=<span class="hljs-number">0</span>;i&lt;<span class="hljs-number">10</span>;i++){
        kafkaSender.send();
    }
}
}</code></pre>

<p>运行结果： <br>
<img src="https://img-blog.csdn.net/20180609141721968?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pjY19oZXU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p></li>
<li><p>在开发中遇到的问题。</p>

<p>我在服务上搭建完一个单节点的kafka服务后，在服务器山启动命令行消费者和服务者进行测试是成功的但是总是报如下错误。</p>

<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-number">2018</span>-<span class="hljs-number">06</span>-<span class="hljs-number">09</span> <span class="hljs-number">14</span>:<span class="hljs-number">04</span>:<span class="hljs-number">13.490</span>  INFO <span class="hljs-number">6268</span> --- [ntainer#<span class="hljs-number">0</span>-<span class="hljs-number">0</span>-C-<span class="hljs-number">1</span>] o.a.k.c.c.internals.AbstractCoordinator  : [Consumer clientId=consumer-<span class="hljs-number">1</span>, groupId=test-consumer-group] Marking the coordinator node2:<span class="hljs-number">9092</span> (id: <span class="hljs-number">2147483646</span> rack: <span class="hljs-keyword">null</span>) dead
<span class="hljs-number">2018</span>-<span class="hljs-number">06</span>-<span class="hljs-number">09</span> <span class="hljs-number">14</span>:<span class="hljs-number">04</span>:<span class="hljs-number">41.305</span> ERROR <span class="hljs-number">6268</span> --- [ad | producer-<span class="hljs-number">1</span>] o.s.k.support.LoggingProducerListener    : Exception thrown when sending a message with key=<span class="hljs-string">'null'</span> and payload=<span class="hljs-string">'{"id":1528524250944,"msg":"056aa258-09ca-49e9-be25-d72383f96e50","sendTime":"Jun 9, 2018 2:04:10 PM"...'</span> to topic sweetzcc:

org.apache.kafka.common.errors.TimeoutException: Expiring <span class="hljs-number">10</span> record(s) <span class="hljs-keyword">for</span> sweetzcc-<span class="hljs-number">0</span>: <span class="hljs-number">30037</span> ms has passed since batch creation plus linger time</code></pre>

<p>网上的回答比较奇怪。说是在服务器上配置配置host和名称相对应。我查看服务器，发现已经配置完成。，有的说重启kafka服务就可以了，但是都不行。后来发现：错误中有：Marking the coordinator node2:9092 (id: 2147483646 rack: null) dead。发现是通过节点名去查找服务器的。但是我在本地的hosts中并没有配置10.108.208.51 node2。配置完成后问题就解决了。</p></li>
</ol>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>