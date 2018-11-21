---
layout:     post
title:      spring-boot Kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1>spring-boot Kafka</h1> <p>基于 springboot 和 kafka 开发消息通信系统，提供消息发送、接收功能。<br>本文需要对 kafka 有基础的了解，且存在可访问的 kafka 服务。如果缺乏相关知识，请先阅读<a href="https://hacpai.com/forward?goto=http%3A%2F%2Fkafka.apache.org%2Fquickstart" rel="nofollow">kafka 官方文档</a></p> <h2>环境工具</h2> <ul><li>jdk 1.8</li> <li>maven 3</li> <li>apache-kafka 2.11-0.10.1.0</li></ul><h2>项目依赖</h2> <ul><li>spring-boot 1.4.3.RELEASE</li> <li>spring-kafka 1.1.1.RELEASE</li></ul><h2>代码</h2> <h3>发送消息</h3> <p>利用<strong>spring-kafka</strong>提供的<strong>KafkaTemplate</strong>模板实现发送消息的功能，因为<strong>KafkaTemplate</strong>不是 springbean，所以在 Config 中手动创建实例 Bean。<br></p><p>看一下<strong>KafkaTemplate</strong>构造函数相关源码</p><pre><code class="language-java">/**
 * Create an instance using the supplied producer factory and autoFlush false.
 * @param producerFactory the producer factory.
 */
public KafkaTemplate(ProducerFactory&lt;K, V&gt; producerFactory) {
    this(producerFactory, false);
}

/**
 * Create an instance using the supplied producer factory and autoFlush setting.
 * Set autoFlush to true if you wish to synchronously interact with Kafaka, calling
 * {@link Future#get()} on the result.
 * @param producerFactory the producer factory.
 * @param autoFlush true to flush after each send.
 */
public KafkaTemplate(ProducerFactory&lt;K, V&gt; producerFactory, boolean autoFlush) {
    this.producerFactory = producerFactory;
    this.autoFlush = autoFlush;
}</code></pre> <p>构造函数需要参数<strong>ProducerFactory</strong>参数，<strong>ProducerFactory</strong>接口一个有一个默认实现<strong>DefaultKafkaProducerFactory</strong>。再看一下<strong>DefaultKafkaProducerFactory</strong>构造函数相关源码</p><pre><code class="language-java">public DefaultKafkaProducerFactory(Map&lt;String, Object&gt; configs) {
    this(configs, null, null);
}

public DefaultKafkaProducerFactory(Map&lt;String, Object&gt; configs, Serializer&lt;K&gt; keySerializer,
        Serializer&lt;V&gt; valueSerializer) {
    this.configs = new HashMap&lt;&gt;(configs);
    this.keySerializer = keySerializer;
    this.valueSerializer = valueSerializer;
}</code></pre><br><p>需要的 configs 参数，在创建<strong>KafkaProducer</strong>时用于初始化参数，所以可以再创建一个 configMap 用于配制<strong>KafkaProducer</strong>相关配置项</p><pre><code class="language-java">protected KafkaProducer&lt;K, V&gt; createKafkaProducer() {
    return new KafkaProducer&lt;K, V&gt;(this.configs, this.keySerializer, this.valueSerializer);
}</code></pre><br><p>相关参数项参考<strong>org.apache.kafka.clients.producer.ProducerConfig</strong>，这里只配置最必需参数，包括 kafka 地址、keyValue 序列化。<br></p><p>发送端配置类如下：</p><pre><code class="language-java">package pro.hemo.study.kafka;

import org.apache.kafka.clients.producer.ProducerConfig;
import org.apache.kafka.common.serialization.IntegerSerializer;
import org.apache.kafka.common.serialization.StringSerializer;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.kafka.core.DefaultKafkaProducerFactory;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.kafka.core.ProducerFactory;

import java.util.HashMap;
import java.util.Map;

@Configuration
public class KafkaProducerConfig {

    private Map&lt;String, Object&gt; configs() {
        Map&lt;String, Object&gt; configMap = new HashMap&lt;&gt;();
        configMap.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG,
                "localhost:9092");
        configMap.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, IntegerSerializer.class);
        configMap.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
        return configMap;
    }

    private ProducerFactory producerFactory() {
        return new DefaultKafkaProducerFactory(configs());
    }

    @Bean
    public KafkaTemplate kafkaTemplate() {
        return new KafkaTemplate(producerFactory());
    }
}
</code></pre><br><p>发送消息工具类方法</p><pre><code class="language-java">package pro.hemo.study.kafka.producer;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.stereotype.Component;

@Component
public class KafkaSendService {

    @Autowired
    private KafkaTemplate kafkaTemplate;

    public void sendMessage(String topic, String message) {
        kafkaTemplate.send(topic, message);
    }
}</code></pre><br><p>可以通过测试用例测试消息发送，通过命令行模式监听对应的 topic，查看是否能够接收消息。</p><pre><code class="language-java">package pro.hemo.study.kafka;

import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;
import pro.hemo.study.kafka.producer.KafkaSendService;

@RunWith(SpringRunner.class)
@SpringBootTest
public class StudyKafkaApplicationTests {


    @Autowired
    private KafkaSendService kafkaSendService;

    @Test
    public void testSendMessage() throws Exception {
        kafkaSendService.sendMessage("foo", "Hello SpringBoot Kafka!");
    }
}</code></pre><br><h3>接收消息</h3> <p>接收端的配置项代码和发送端类似，不同的事，接收端需要指定一个<strong>KafkaListenerContainerFactory</strong>，用于注册监听。先看代码</p><pre><code class="language-java">package pro.hemo.study.kafka;

import org.apache.kafka.clients.consumer.ConsumerConfig;
import org.apache.kafka.common.serialization.IntegerDeserializer;
import org.apache.kafka.common.serialization.StringDeserializer;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.kafka.annotation.EnableKafka;
import org.springframework.kafka.config.ConcurrentKafkaListenerContainerFactory;
import org.springframework.kafka.config.KafkaListenerContainerFactory;
import org.springframework.kafka.core.ConsumerFactory;
import org.springframework.kafka.core.DefaultKafkaConsumerFactory;

import java.util.HashMap;
import java.util.Map;

@Configuration
@EnableKafka
public class KafkaConsumerConfig {

    private Map&lt;String, Object&gt; configs() {
        Map&lt;String, Object&gt; configMap = new HashMap&lt;&gt;();
        configMap.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG,
                "localhost:9092");
        configMap.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG,
                IntegerDeserializer.class);
        configMap.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG,
                StringDeserializer.class);
        configMap.put(ConsumerConfig.GROUP_ID_CONFIG, "groupTest");

        return configMap;
    }

    private ConsumerFactory consumerFactory() {
        return new DefaultKafkaConsumerFactory&lt;&gt;(configs());
    }

    @Bean
    public KafkaListenerContainerFactory kafkaListenerContainerFactory() {
        ConcurrentKafkaListenerContainerFactory factory = new ConcurrentKafkaListenerContainerFactory&lt;&gt;();
        factory.setConsumerFactory(consumerFactory());
        return factory;
    }
}</code></pre><br><p>结合接收消息工具类一起看</p><pre><code class="language-java">package pro.hemo.study.kafka.consumer;

import org.springframework.kafka.annotation.KafkaHandler;
import org.springframework.kafka.annotation.KafkaListener;
import org.springframework.stereotype.Component;

@KafkaListener(topics = "topicTest")
@Component
public class KafkaReceiveService {

    @KafkaHandler
    public void receiveMessage(String message) {
        System.out.println("receive:" + message);
    }
}</code></pre><br><p>消息接收端代码核心在于注解<strong>@KafkaListener</strong>，查看源码，重点为两个属性<strong>containerFactory</strong>和<strong>topics</strong>。</p><pre><code class="language-java">package org.springframework.kafka.annotation;

import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Repeatable;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import org.springframework.kafka.listener.MessageListener;
import org.springframework.messaging.handler.annotation.MessageMapping;

@Target({ ElementType.TYPE, ElementType.METHOD, ElementType.ANNOTATION_TYPE })
@Retention(RetentionPolicy.RUNTIME)
@MessageMapping
@Documented
@Repeatable(KafkaListeners.class)
public @interface KafkaListener {

    /**
     * The unique identifier of the container managing for this endpoint.
     * &lt;p&gt;If none is specified an auto-generated one is provided.
     * @return the {@code id} for the container managing for this endpoint.
     * @see org.springframework.kafka.config.KafkaListenerEndpointRegistry#getListenerContainer(String)
     */
    String id() default "";

    /**
     * The bean name of the {@link org.springframework.kafka.config.KafkaListenerContainerFactory}
     * to use to create the message listener container responsible to serve this endpoint.
     * &lt;p&gt;If not specified, the default container factory is used, if any.
     * @return the container factory bean name.
     */
    String containerFactory() default "";

    /**
     * The topics for this listener.
     * The entries can be 'topic name', 'property-placeholder keys' or 'expressions'.
     * Expression must be resolved to the topic name.
     * Mutually exclusive with {@link #topicPattern()} and {@link #topicPartitions()}.
     * @return the topic names or expressions (SpEL) to listen to.
     */
    String[] topics() default {};

    /**
     * The topic pattern for this listener.
     * The entries can be 'topic name', 'property-placeholder keys' or 'expressions'.
     * Expression must be resolved to the topic pattern.
     * Mutually exclusive with {@link #topics()} and {@link #topicPartitions()}.
     * @return the topic pattern or expression (SpEL).
     */
    String topicPattern() default "";

    /**
     * The topicPartitions for this listener.
     * Mutually exclusive with {@link #topicPattern()} and {@link #topics()}.
     * @return the topic names or expressions (SpEL) to listen to.
     */
    TopicPartition[] topicPartitions() default {};

    /**
     * If provided, the listener container for this listener will be added to a bean
     * with this value as its name, of type {@code Collection&lt;MessageListenerContainer&gt;}.
     * This allows, for example, iteration over the collection to start/stop a subset
     * of containers.
     * @return the bean name for the group.
     */
    String group() default "";

}</code></pre><br><p>topics、topicPattern、topicPartitions 都是用于指定监听的 topic，而<strong>containerFactory</strong>需要指明监听仓库<strong>KafkaListenerContainerFactory</strong>，所以在<strong>KafkaConsumerConfig</strong>配置类中创建对应的 Bean 对象，并根据构造函数添加相应的参数配置。</p> <h4>@EnableKafka</h4> <p>用于扫描对应的 listener。</p> <h4>@KafkaHandler</h4> <p>指明消息接收处理方法。</p>转载自：https://hacpai.com/article/1498353443246            </div>
                </div>