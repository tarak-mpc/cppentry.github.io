---
layout:     post
title:      Spring Kafka 教程 –  spring读取和发送kakfa消息
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/russle/article/details/80296006				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>Apache Kafka, 分布式消息系统， 非常流行。Spring是非常流行的Java快速开发框架。将两者无缝平滑结合起来可以快速实现很多功能。本文件简要介绍Spring Kafka，如何使用 KafkaTemplate发送消息到kafka的broker上， 如何使用“listener container“接收Kafka消息。</p>

<p><strong>1，Spring Kafka的组成</strong> <br>
这一节我们首先介绍Spring Kafka的各个组成部分。 <br>
1.1  发送消息 <br>
与 JmsTemplate 或者JdbcTemplate类似，Spring Kafka提供了 KafkaTemplate.  该模板封装了Kafka消息生产者并提供各种消息发送方法。 <br>
消息发送的各种方法。</p>



<pre class="prettyprint"><code class=" hljs mathematica">ListenableFuture&lt;SendResult&lt;<span class="hljs-keyword">K</span>, V&gt;&gt; send(<span class="hljs-keyword">String</span> topic, V data);

ListenableFuture&lt;SendResult&lt;<span class="hljs-keyword">K</span>, V&gt;&gt; send(<span class="hljs-keyword">String</span> topic, <span class="hljs-keyword">K</span> key, V data);

ListenableFuture&lt;SendResult&lt;<span class="hljs-keyword">K</span>, V&gt;&gt; send(<span class="hljs-keyword">String</span> topic, int partition, V data);

ListenableFuture&lt;SendResult&lt;<span class="hljs-keyword">K</span>, V&gt;&gt; send(<span class="hljs-keyword">String</span> topic, int partition, <span class="hljs-keyword">K</span> key, V data);

ListenableFuture&lt;SendResult&lt;<span class="hljs-keyword">K</span>, V&gt;&gt; send(<span class="hljs-keyword">Message</span>&lt;?&gt; message);</code></pre>

<p>1.2 接收消息 <br>
要接收消息，我们需要配置MessageListenerContainer并提供一个Message Listener，或者使用 @KafkaListener注解。</p>

<p>MessageListenserContainer <br>
 MessageListenserContainer 有以下两个实现类：</p>

<pre><code>KafkaMessageListenerContainer
ConcurrentMessageListenerContainer
</code></pre>

<p>KafkaMessageListenerContainer可以让我们使用单线程消费Kafka topic的消息，而ConcurrentMessageListenerContainer 可以让我们多线程消费消息。</p>

<p>@KafkaListener 注解 <br>
Spring Kafka提供的 @KafkaListener注解，可以让我们监听某个topic或者topicPattern的消息。 </p>

<p>监听符合topicPattern = “topic.*”的所有topic的消息</p>

<pre class="prettyprint"><code class=" hljs cs">@Component
@Slf4j
<span class="hljs-keyword">public</span> <span class="hljs-keyword">class</span> CmdReceiver {
    @KafkaListener(topicPattern = <span class="hljs-string">"topic.*"</span>)
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">listen</span>(ConsumerRecord&lt;?, ?&gt; record, @<span class="hljs-title">Header</span>(KafkaHeaders.RECEIVED_TOPIC) String topic) {
        Optional&lt;?&gt; kafkaMessage = Optional.ofNullable(record.<span class="hljs-keyword">value</span>());
        <span class="hljs-keyword">if</span> (kafkaMessage.isPresent()) {
            Object message = kafkaMessage.<span class="hljs-keyword">get</span>();
            log.info(<span class="hljs-string">"----------------- record =topic："</span>  + topic+ <span class="hljs-string">", "</span> + record);
            log.info(<span class="hljs-string">"------------------ message =topic："</span> + topic+ <span class="hljs-string">", "</span> + message);
        }
    }
}</code></pre>



<pre class="prettyprint"><code class=" hljs cs">
监听某个topic的消息    
<span class="hljs-keyword">public</span> <span class="hljs-keyword">class</span> Listener {

    @KafkaListener(id = <span class="hljs-string">"id01"</span>, topics = <span class="hljs-string">"Topic1"</span>)
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">listen</span>(String data) {

    }
}</code></pre>

<p><strong>2， Spring Kafka 例子</strong></p>

<p>下面我们介绍一个具体的例子， 这个例会发送和接收指定topic的消息。 <br>
<strong>准备工作</strong>： <br>
    kafka_2.11-1.1.0.tgz和zookeeper-3.4.10.tar.gz <br>
    JDK jdk-8u171-linux-x64.tar.gz <br>
    IDE (Eclipse or IntelliJ) <br>
    Build tool (Maven  or Gradle) <br>
本文不涉及安装Kafka的介绍，请自行搜索，或者看官方文档。</p>

<p><strong>pom文件</strong>： <br>
也就是我们的依赖包. 这是笔者使用的依赖版本，仅供参考。</p>

<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-pi">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">project</span> <span class="hljs-attribute">xmlns</span>=<span class="hljs-value">"http://maven.apache.org/POM/4.0.0"</span>
         <span class="hljs-attribute">xmlns:xsi</span>=<span class="hljs-value">"http://www.w3.org/2001/XMLSchema-instance"</span>
         <span class="hljs-attribute">xsi:schemaLocation</span>=<span class="hljs-value">"http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">modelVersion</span>&gt;</span>4.0.0<span class="hljs-tag">&lt;/<span class="hljs-title">modelVersion</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>com.yq<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>kafkademo<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.0-SNAPSHOT<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">parent</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.springframework.boot<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spring-boot-starter-parent<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.5.12.RELEASE<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">relativePath</span>/&gt;</span> <span class="hljs-comment">&lt;!-- lookup parent from repository --&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">parent</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">properties</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">project.build.sourceEncoding</span>&gt;</span>UTF-8<span class="hljs-tag">&lt;/<span class="hljs-title">project.build.sourceEncoding</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">project.reporting.outputEncoding</span>&gt;</span>UTF-8<span class="hljs-tag">&lt;/<span class="hljs-title">project.reporting.outputEncoding</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">java.version</span>&gt;</span>1.8<span class="hljs-tag">&lt;/<span class="hljs-title">java.version</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">properties</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">dependencies</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.springframework.boot<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spring-boot-starter-web<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.projectlombok<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>lombok<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">optional</span>&gt;</span>true<span class="hljs-tag">&lt;/<span class="hljs-title">optional</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.springframework.boot<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spring-boot-starter-test<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">scope</span>&gt;</span>test<span class="hljs-tag">&lt;/<span class="hljs-title">scope</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.springframework.kafka<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spring-kafka<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.1.8.RELEASE<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>com.google.code.gson<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>gson<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>2.8.2<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.kafka<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>kafka-clients<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>0.10.1.1<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>io.springfox<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>springfox-swagger2<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>2.7.0<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>io.springfox<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>springfox-swagger-ui<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>2.7.0<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>io.springfox<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>springfox-spring-web<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>2.7.0<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-comment">&lt;!-- fastjson--&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>com.alibaba<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>fastjson<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.1.33<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">dependencies</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">build</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">plugins</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">plugin</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.springframework.boot<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spring-boot-maven-plugin<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">plugin</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">plugins</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">build</span>&gt;</span>

<span class="hljs-tag">&lt;/<span class="hljs-title">project</span>&gt;</span></code></pre>

<p><strong><em>*KafkaDemoApplication*</em></strong> <br>
我们使用springboot的框架，这是我们程序的入口点。</p>

<pre class="prettyprint"><code class=" hljs java"><span class="hljs-annotation">@SpringBootApplication</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">KafkaDemoApplication</span> {</span>
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> Logger logger = LoggerFactory.getLogger(KafkaDemoApplication.class);
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) {
        ConfigurableApplicationContext context = SpringApplication.run(KafkaDemoApplication.class, args);
        logger.info(<span class="hljs-string">"Done start Spring boot"</span>);
    }
}</code></pre>

<p><strong>ProducerConfig</strong> <br>
其实我们可以可以不用编写KafkaProducerConfig，直接使用KafkaTemplate（当然前提是我们要设置好producer需要的配置项，例如spring.kafka.bootstrap-servers, spring.kafka.producer.key-serializer, spring.kafka.producer.retries等等）</p>

<pre class="prettyprint"><code class=" hljs java"><span class="hljs-annotation">@Configuration</span>
<span class="hljs-annotation">@EnableKafka</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">KafkaProducerConfig</span> {</span>
    <span class="hljs-annotation">@Bean</span>
    <span class="hljs-keyword">public</span> ProducerFactory&lt;String, String&gt; <span class="hljs-title">producerFactory</span>() {
        <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> DefaultKafkaProducerFactory&lt;&gt;(producerConfigs());
    }

    <span class="hljs-annotation">@Bean</span>
    <span class="hljs-keyword">public</span> Map&lt;String, Object&gt; <span class="hljs-title">producerConfigs</span>() {
        Map&lt;String, Object&gt; props = <span class="hljs-keyword">new</span> HashMap&lt;&gt;();
        props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, <span class="hljs-string">"localhost:9092(根据实际情况修改)"</span>);
        props.put(ProducerConfig.RETRIES_CONFIG, <span class="hljs-number">0</span>);
        props.put(ProducerConfig.BATCH_SIZE_CONFIG, <span class="hljs-number">16384</span>);
        props.put(ProducerConfig.LINGER_MS_CONFIG, <span class="hljs-number">1</span>);
        props.put(ProducerConfig.BUFFER_MEMORY_CONFIG, <span class="hljs-number">33554432</span>);
        props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
        props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
        <span class="hljs-keyword">return</span> props;
    }

    <span class="hljs-annotation">@Bean</span>
    <span class="hljs-keyword">public</span> KafkaTemplate&lt;String, String&gt; <span class="hljs-title">kafkaTemplate</span>() {
        <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> KafkaTemplate&lt;String, String&gt;(producerFactory());
    }
}</code></pre>

<p><strong>KafkaConsumerConfig</strong> <br>
同理，其实我们可以可以不用编写KafkaConsumerConfig，直接使用 @KafkaListener（当然前提是我们要设置好consumer需要的配置项，例如spring.kafka.bootstrap-servers, spring.kafka.consumer.key-deserializer, spring.kafka.consumer.group-id、spring.kafka.consumer.auto-offset-reset等等）</p>

<pre class="prettyprint"><code class=" hljs java"><span class="hljs-annotation">@Configuration</span>
<span class="hljs-annotation">@EnableKafka</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">KafkaConsumerConfig</span> {</span>
    <span class="hljs-annotation">@Bean</span>
    KafkaListenerContainerFactory&lt;ConcurrentMessageListenerContainer&lt;String, String&gt;&gt; kafkaListenerContainerFactory() {
        ConcurrentKafkaListenerContainerFactory&lt;String, String&gt; factory = <span class="hljs-keyword">new</span> ConcurrentKafkaListenerContainerFactory&lt;&gt;();
        factory.setConsumerFactory(consumerFactory());
        factory.setConcurrency(<span class="hljs-number">3</span>);
        factory.getContainerProperties().setPollTimeout(<span class="hljs-number">3000</span>);
        <span class="hljs-keyword">return</span> factory;
    }

    <span class="hljs-annotation">@Bean</span>
    <span class="hljs-keyword">public</span> ConsumerFactory&lt;String, String&gt; <span class="hljs-title">consumerFactory</span>() {
        <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> DefaultKafkaConsumerFactory&lt;&gt;(consumerConfigs());
    }

    <span class="hljs-annotation">@Bean</span>
    <span class="hljs-keyword">public</span> Map&lt;String, Object&gt; <span class="hljs-title">consumerConfigs</span>() {
        Map&lt;String, Object&gt; propsMap = <span class="hljs-keyword">new</span> HashMap&lt;&gt;();
        propsMap.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, <span class="hljs-string">"localhost:9092(根据实际情况修改)"</span>);
        propsMap.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, <span class="hljs-keyword">false</span>);
        propsMap.put(ConsumerConfig.AUTO_COMMIT_INTERVAL_MS_CONFIG, <span class="hljs-string">"100"</span>);
        propsMap.put(ConsumerConfig.SESSION_TIMEOUT_MS_CONFIG, <span class="hljs-string">"15000"</span>);
        propsMap.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
        propsMap.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
        propsMap.put(ConsumerConfig.GROUP_ID_CONFIG, <span class="hljs-string">"group1"</span>);
        propsMap.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, <span class="hljs-string">"earliest"</span>);
        <span class="hljs-keyword">return</span> propsMap;
    }

    <span class="hljs-annotation">@Bean</span>
    <span class="hljs-keyword">public</span> MyListener <span class="hljs-title">listener</span>() {
        <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> MyListener();
    }
}</code></pre>

<p>定义了ProducerConfig和ConsumerConfig后我们需要实现具体的生产者和消费者。</p>

<p>本文的KafkaListenerContainerFactory 中使用了ConcurrentKafkaListenerContainer， 我们将使用多线程消费消息。 </p>

<p>注意消息代理的地址是localhost:9092， 需要根据实际情况修改。需要特别注意的是，我在windows运行程序，kafka在我的linux虚拟机， 我需要配置windows的hosts文件，配置虚拟机hostname和ip的映射，例如192.168.119.131        ubuntu01</p>

<p><strong>开发Listener</strong></p>

<p>我们来开发自己的Listener监听具体的topic， 这里例子中我们监听以topic开头的主题，不做其他业务，只是打印出来。</p>

<pre class="prettyprint"><code class=" hljs cs">@Component
@Slf4j
<span class="hljs-keyword">public</span> <span class="hljs-keyword">class</span> MyListener{
    @KafkaListener(topicPattern = <span class="hljs-string">"topic.*"</span>)
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">listen</span>(ConsumerRecord&lt;?, ?&gt; record, @<span class="hljs-title">Header</span>(KafkaHeaders.RECEIVED_TOPIC) String topic) {
        Optional&lt;?&gt; kafkaMessage = Optional.ofNullable(record.<span class="hljs-keyword">value</span>());
        <span class="hljs-keyword">if</span> (kafkaMessage.isPresent()) {
            Object message = kafkaMessage.<span class="hljs-keyword">get</span>();
            log.info(<span class="hljs-string">"------------------ message =topic："</span> + topic+ <span class="hljs-string">", "</span> + message);
        }
    }
}</code></pre>

<p><strong>开发producer</strong> <br>
我在程序中增加了controller，这样我们可以通过controller给topic发消息。consumer一直在监听，只要有消息发送过去，就会打印出来。controller中调用了ProducerServiceImpl ， 具体代码比较简单就不再罗列。</p>

<p>我们producerServiceImpl主要是有这句， 通过KafkaTemplate 发送消息。 <br>
@Autowired <br>
    private KafkaTemplate template;</p>

<pre class="prettyprint"><code class=" hljs java"><span class="hljs-annotation">@Service</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">ProducerServiceImpl</span> <span class="hljs-keyword">implements</span> <span class="hljs-title">ProducerService</span> {</span>
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> Logger logger = LoggerFactory.getLogger(ProducerServiceImpl.class);

    <span class="hljs-keyword">private</span> Gson gson = <span class="hljs-keyword">new</span> GsonBuilder().create();

    <span class="hljs-annotation">@Autowired</span>
    <span class="hljs-keyword">private</span> KafkaTemplate template;

    <span class="hljs-comment">//发送消息方法</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">sendJson</span>(String topic, String json) {
        JSONObject jsonObj = JSON.parseObject(json);

        jsonObj.put(<span class="hljs-string">"topic"</span>, topic);
        jsonObj.put(<span class="hljs-string">"ts"</span>, System.currentTimeMillis() + <span class="hljs-string">""</span>);

        logger.info(<span class="hljs-string">"json+++++++++++++++++++++  message = {}"</span>, jsonObj.toJSONString());

        ListenableFuture&lt;SendResult&lt;String, String&gt;&gt; future = template.send(topic, jsonObj.toJSONString());
        future.addCallback(<span class="hljs-keyword">new</span> ListenableFutureCallback&lt;SendResult&lt;String, String&gt;&gt;() {
            <span class="hljs-annotation">@Override</span>
            <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">onSuccess</span>(SendResult&lt;String, String&gt; result) {
                System.out.println(<span class="hljs-string">"msg OK."</span> + result.toString());
            }

            <span class="hljs-annotation">@Override</span>
            <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">onFailure</span>(Throwable ex) {
                System.out.println(<span class="hljs-string">"msg send failed: "</span>);
            }
        });
    }</code></pre>

<p><strong>运行程序</strong> </p>

<p>运行第一步，确保Kafka broker配置正确，笔者的程序在Windows10机器上，Kafka在虚拟机上，因为我的地址是192.168.119.129:9092, 而不是localhost:9092.</p>

<p>运行第二步骤，在IDEA中选中KafkaDemoApplication ,  单击鼠标右键，选择 Run KafkaDemoApplication </p>

<p>效果图 <br>
<img src="https://img-blog.csdn.net/20180513121333348?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3J1c3NsZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>kafka段命令行接收到的消息</p>

<p><img src="https://img-blog.csdn.net/20180513121424969?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3J1c3NsZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p><strong>3，总结</strong> <br>
Spring Kafka提供了很好的集成，我们只需配置properties文件，就可以直接使用KafkaTemplate发送消息，使用@KafkaListener监听消息。</p>

<p>参考文档： <br>
<a href="https://docs.spring.io/spring-kafka/docs/2.1.6.RELEASE/reference/html/_reference.html#kafka-template" rel="nofollow">https://docs.spring.io/spring-kafka/docs/2.1.6.RELEASE/reference/html/_reference.html#kafka-template</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>