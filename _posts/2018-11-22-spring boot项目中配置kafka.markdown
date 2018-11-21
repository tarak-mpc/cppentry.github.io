---
layout:     post
title:      spring boot项目中配置kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>1、首先配置文件配置kafka相关配置信息<br>
#kafka服务地址<br>
spring.kafka.bootstrap-servers=ip:9092</p>

<p>#kafka消费者配置<br>
spring.kafka.consumer.enable-auto-commit=true<br>
spring.kafka.consumer.group-id=test-consumer-group<br>
spring.kafka.consumer.auto-commit-interval=1000<br>
spring.kafka.consumer.session.timeout.ms=1500</p>

<p>#kafka生产者配置<br>
spring.kafka.producer.acks=all<br>
spring.kafka.producer.retries=0<br>
spring.kafka.producer.batch-size=16384<br>
spring.kafka.producer.linger.ms=1<br>
spring.kafka.producer.buffer-memory=33554432</p>

<p>2、新建kafka生产者</p>

<pre class="has">
<code class="language-java">package com.multi.store.manage.kafka.config;

import org.apache.kafka.clients.producer.ProducerConfig;
import org.apache.kafka.common.serialization.StringSerializer;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.kafka.annotation.EnableKafka;
import org.springframework.kafka.core.DefaultKafkaProducerFactory;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.kafka.core.ProducerFactory;

import java.util.HashMap;
import java.util.Map;

/**
 * kafka生产者相关配置
 * @date 2018/8/21 9:17.
 */
@Configuration
@EnableKafka
public class KafkaProducerConfig {

    @Value("${spring.kafka.bootstrap-servers}")
    private String servers;
    @Value("${spring.kafka.producer.acks}")
    private String acks;
    @Value("${spring.kafka.producer.retries}")
    private String retries;
    @Value("${spring.kafka.producer.batch-size}")
    private String batchSize;
    @Value("${spring.kafka.producer.linger.ms}")
    private String lingerMs;
    @Value("${spring.kafka.producer.buffer-memory}")
    private String bufferMemory;

    public Map&lt;String, Object&gt; producerConfigs() {
        Map&lt;String, Object&gt; props = new HashMap&lt;&gt;();
        props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, servers);
        props.put(ProducerConfig.ACKS_CONFIG,acks);
        props.put(ProducerConfig.RETRIES_CONFIG,retries);
        props.put(ProducerConfig.BATCH_SIZE_CONFIG,batchSize);
        props.put(ProducerConfig.LINGER_MS_CONFIG,lingerMs);
        props.put(ProducerConfig.BUFFER_MEMORY_CONFIG,bufferMemory);
        props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
        props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
        return props;
    }

    public ProducerFactory&lt;String, String&gt; producerFactory() {
        return new DefaultKafkaProducerFactory&lt;&gt;(producerConfigs());
    }

    @Bean
    public KafkaTemplate&lt;String, String&gt; kafkaTemplate() {
        return new KafkaTemplate&lt;String, String&gt;(producerFactory());
    }
}
</code></pre>

<p>3、新建kafka消费者</p>

<pre class="has">
<code class="language-java">package com.multi.store.manage.kafka.config;

import com.multi.store.manage.kafka.KafkaMessageListener;
import org.apache.kafka.clients.consumer.ConsumerConfig;
import org.apache.kafka.common.serialization.StringDeserializer;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.kafka.annotation.EnableKafka;
import org.springframework.kafka.config.ConcurrentKafkaListenerContainerFactory;
import org.springframework.kafka.config.KafkaListenerContainerFactory;
import org.springframework.kafka.core.ConsumerFactory;
import org.springframework.kafka.core.DefaultKafkaConsumerFactory;
import org.springframework.kafka.listener.ConcurrentMessageListenerContainer;

import java.util.HashMap;
import java.util.Map;


/**
 * kafka消费者相关配置
 * @date 2018/8/20 18:20.
 */
@Configuration
@EnableKafka
public class KafkaConsumerConfig {
    @Value("${spring.kafka.bootstrap-servers}")
    private String servers;
    @Value("${spring.kafka.consumer.group-id}")
    private String groupId;
    @Value("${spring.kafka.consumer.enable-auto-commit}")
    private String enableAutoCommit;
    @Value("${spring.kafka.consumer.auto-commit-interval}")
    private String autoCommitInterval;
    @Value("${spring.kafka.consumer.session.timeout.ms}")
    private String sessionTimeoutMs;

    @Bean
    public KafkaListenerContainerFactory&lt;ConcurrentMessageListenerContainer&lt;String, String&gt;&gt; kafkaListenerContainerFactory() {
        ConcurrentKafkaListenerContainerFactory&lt;String, String&gt; factory = new ConcurrentKafkaListenerContainerFactory&lt;&gt;();
        factory.setConsumerFactory(consumerFactory());
        factory.getContainerProperties().setPollTimeout(1500);
        return factory;
    }

    public ConsumerFactory&lt;String, String&gt; consumerFactory() {
        return new DefaultKafkaConsumerFactory&lt;&gt;(consumerConfigs());
    }


    public Map&lt;String, Object&gt; consumerConfigs() {
        Map&lt;String, Object&gt; propsMap = new HashMap&lt;&gt;();
        propsMap.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, servers);
        propsMap.put(ConsumerConfig.GROUP_ID_CONFIG, groupId);
        propsMap.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG,enableAutoCommit);
        propsMap.put(ConsumerConfig.AUTO_COMMIT_INTERVAL_MS_CONFIG,autoCommitInterval);
//        propsMap.put(ConsumerConfig.SESSION_TIMEOUT_MS_CONFIG,sessionTimeoutMs);
        propsMap.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
        propsMap.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
        return propsMap;
    }

    @Bean
    public KafkaMessageListener listener() {
        return new KafkaMessageListener();
    }
}
</code></pre>

<p>4、新建kafka监听</p>

<pre class="has">
<code class="language-java">package com.multi.store.manage.kafka;

import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.kafka.annotation.KafkaListener;

/**
 * kafka监听
 * @date 2018/8/20 18:31.
 */
public class KafkaMessageListener {
   private static final Logger LOGGER = LoggerFactory.getLogger(KafkaMessageListener.class);
   /**
    * 
    * @createDate:2018/8/20 18:40
    * @return
    */
   @KafkaListener(topics = {"test-topics"})
   public void linstenOtaStatus(ConsumerRecord&lt;?, ?&gt; record){
      LOGGER.info("test-topics --&gt;kafka监听到的值为：{}",record.value().toString());
   }
}
</code></pre>

<p>5、新建测试类</p>

<pre class="has">
<code class="language-java">package com.multi.store.manage.utils.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @date 2018/9/18 9:52.
 */
@RestController
@RequestMapping(value = "kafka")
public class KafkaController {
    @Autowired
    private KafkaTemplate kafkaTemplate;


    @RequestMapping(value = "sendMessage")
    public String sendMessage(@RequestBody String msgJson){
        kafkaTemplate.send("ota-topics",msgJson);
        return "成功";
    }
}
</code></pre>

<p> </p>            </div>
                </div>