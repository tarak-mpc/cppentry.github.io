---
layout:     post
title:      springboot配置kafka与原生kafka配置
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u012466304/article/details/78729128				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;">       搭建kafka要注意版本问题，本教程使用的kafka版本是kafka_2.11-0.11.0.2.tgz；首先看下spring-boot链接kafka的使用。</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">      1. 添加pom依赖</span></p>
<p><span style="font-size:18px;"></span></p>
<pre><code class="language-java">&lt;dependencies&gt;
&lt;dependency&gt;
	&lt;groupId&gt;org.springframework.boot&lt;/groupId&gt;
	&lt;artifactId&gt;spring-boot-starter-web&lt;/artifactId&gt;
	&lt;!--&lt;version&gt;1.5.8.RELEASE&lt;/version&gt;--&gt;
&lt;/dependency&gt;
&lt;dependency&gt;
	&lt;groupId&gt;org.apache.kafka&lt;/groupId&gt;
	&lt;artifactId&gt;kafka-clients&lt;/artifactId&gt;
	&lt;version&gt;0.10.2.0&lt;/version&gt;
&lt;/dependency&gt;
&lt;dependency&gt;
	&lt;groupId&gt;org.springframework.boot&lt;/groupId&gt;
	&lt;artifactId&gt;spring-boot-starter&lt;/artifactId&gt;
&lt;/dependency&gt;
&lt;dependency&gt;
	&lt;groupId&gt;org.springframework.kafka&lt;/groupId&gt;
	&lt;artifactId&gt;spring-kafka&lt;/artifactId&gt;
&lt;/dependency&gt;
&lt;/dependencies&gt;</code></pre><br><span></span>2. application.properties配置
<p></p>
<p></p>
<pre><code class="language-javascript">kafka.consumer.zookeeper.connect=zookeeper-ip:2181
kafka.consumer.servers=kafka-ip:9092
kafka.consumer.enable.auto.commit=true
kafka.consumer.session.timeout=6000
kafka.consumer.auto.commit.interval=100
kafka.consumer.auto.offset.reset=latest
kafka.consumer.topic=test
kafka.consumer.group.id=test
kafka.consumer.concurrency=10

kafka.producer.servers=kafka-ip:9092
kafka.producer.retries=0
kafka.producer.batch.size=4096
kafka.producer.linger=1
kafka.producer.buffer.memory=40960</code></pre><br><br><p></p>
<p><span style="font-size:18px;">     3. 添加kafkaConsumer配置类：</span></p>
<p><span style="font-size:18px;">    </span></p>
<pre><code class="language-java">package com.databps.bigdaf.admin.config;

import com.databps.bigdaf.admin.manager.HomePageManager;
import com.databps.bigdaf.admin.vo.HomePageVo;
import org.apache.kafka.clients.consumer.ConsumerConfig;
import org.apache.kafka.common.serialization.StringDeserializer;
import org.springframework.beans.factory.annotation.Autowired;
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
 * @author haipeng
 * @create 17-11-2 上午11:39
 */
@Configuration
@EnableKafka
public class KafkaConsumerConfig {
    @Value("${kafka.consumer.servers}")
    private String servers;
    @Value("${kafka.consumer.enable.auto.commit}")
    private boolean enableAutoCommit;
    @Value("${kafka.consumer.session.timeout}")
    private String sessionTimeout;
    @Value("${kafka.consumer.auto.commit.interval}")
    private String autoCommitInterval;
    @Value("${kafka.consumer.group.id}")
    private String groupId;
    @Value("${kafka.consumer.auto.offset.reset}")
    private String autoOffsetReset;
    @Value("${kafka.consumer.concurrency}")
    private int concurrency;
    @Autowired
    private HomePageManager homePageManager;
    @Bean
    public KafkaListenerContainerFactory&lt;ConcurrentMessageListenerContainer&lt;String, String&gt;&gt; kafkaListenerContainerFactory() {
        ConcurrentKafkaListenerContainerFactory&lt;String, String&gt; factory = new ConcurrentKafkaListenerContainerFactory&lt;&gt;();
        factory.setConsumerFactory(consumerFactory());
        factory.setConcurrency(concurrency);
        factory.getContainerProperties().setPollTimeout(1500);
        return factory;
    }

    public ConsumerFactory&lt;String, String&gt; consumerFactory() {
        return new DefaultKafkaConsumerFactory&lt;&gt;(consumerConfigs());
    }


    public Map&lt;String, Object&gt; consumerConfigs() {
        Map&lt;String, Object&gt; propsMap = new HashMap&lt;&gt;();
//        propsMap.put("zookeeper.connect", "master1.hdp.com:2181,master2.hdp.com:2181,slave1.hdp.com:2181");
        propsMap.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, servers);
        propsMap.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, enableAutoCommit);
        propsMap.put(ConsumerConfig.AUTO_COMMIT_INTERVAL_MS_CONFIG, autoCommitInterval);
        propsMap.put(ConsumerConfig.SESSION_TIMEOUT_MS_CONFIG, sessionTimeout);
        propsMap.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
        propsMap.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
        propsMap.put(ConsumerConfig.GROUP_ID_CONFIG, groupId);
        propsMap.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, autoOffsetReset);
        return propsMap;
    }
}</code></pre><br><p></p>
<p><span style="font-size:18px;">     4. 添加kafkaProducer配置类</span></p>
<p><span style="font-size:18px;"></span></p>
<pre><code class="language-java">package com.databps.bigdaf.admin.config;


import java.util.HashMap;
import java.util.Map;

import org.apache.kafka.clients.producer.ProducerConfig;
import org.apache.kafka.common.serialization.StringSerializer;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.kafka.annotation.EnableKafka;
import org.springframework.kafka.core.DefaultKafkaProducerFactory;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.kafka.core.ProducerFactory;
/**
 * @author haipeng
 * @create 17-11-2 上午11:37
 */
@Configuration
@EnableKafka
public class KafkaProducerConfig {
    @Value("${kafka.producer.servers}")
    private String servers;
    @Value("${kafka.producer.retries}")
    private int retries;
    @Value("${kafka.producer.batch.size}")
    private int batchSize;
    @Value("${kafka.producer.linger}")
    private int linger;
    @Value("${kafka.producer.buffer.memory}")
    private int bufferMemory;


    public Map&lt;String, Object&gt; producerConfigs() {
        Map&lt;String, Object&gt; props = new HashMap&lt;&gt;();
        props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, servers);
        props.put(ProducerConfig.RETRIES_CONFIG, retries);
        props.put(ProducerConfig.BATCH_SIZE_CONFIG, batchSize);
        props.put(ProducerConfig.LINGER_MS_CONFIG, linger);
        props.put(ProducerConfig.BUFFER_MEMORY_CONFIG, bufferMemory);
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

}</code></pre><br>
    5. 生产者调用过程：
<p></p>
<p><span style="font-size:18px;">     （1）添加kafkaTemplate注入</span></p>
<p><span style="font-size:18px;"></span></p>
<pre><code class="language-java">  @Autowired
  private KafkaTemplate kafkaTemplate;</code></pre>     
<p></p>
<p><span style="font-size:18px;">    （2）将要传输数据转化为json发送，本实例通过Gson进行转换</span></p>
<p><span style="font-size:18px;"></span></p>
<pre><code class="language-java">    AuditVo auditVo=new AuditVo();
    long sortData=Long.parseLong(DateUtils.getNowDateTime());
    auditVo.setId("sdfdf");
    auditVo.setCmpyId(cmpyId);
    auditVo.setUser("whp");
    auditVo.setPluginIp("192.168.1.53");
    auditVo.setAccessTime(DateUtils.getNowDateTime());
    auditVo.setAccessType("WRITE");
    auditVo.setAction("write");
    auditVo.setAccessResult("success");
    auditVo.setServiceType("hbase");
    auditVo.setResourcePath("/whp");
    Gson gson=new Gson();
    kafkaTemplate.send("test", gson.toJson(auditVo));</code></pre>      
<p></p>
<p><span style="font-size:18px;">   （3）消费者类只要在方法上添加注解就可以了：@KafkaListener(topics = {"test"})<br></span></p>
<pre><code class="language-java">@Component
public class KafkaConsumer {
    @KafkaListener(topics = {"test"})
    public void processMessage(String content) {
        System.out.println("消息被消费"+content);
    }
}</code></pre><br><p></p>
<p><span style="font-size:18px;">    使用原生kafka的java API进行kafka测试方法如下：</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">    1. pom文件引入依赖</span></p>
<p><span style="font-size:18px;"></span></p>
<pre><code class="language-java"> &lt;mapr-storm-kafka.version&gt;1.0.1&lt;/mapr-storm-kafka.version&gt;
    &lt;scala.version&gt;2.11&lt;/scala.version&gt;
    &lt;kafka.version&gt;0.10.0.0&lt;/kafka.version&gt;

    &lt;dependency&gt;
      &lt;groupId&gt;org.apache.kafka&lt;/groupId&gt;
      &lt;artifactId&gt;kafka_${scala.version}&lt;/artifactId&gt;
      &lt;version&gt;${kafka.version}&lt;/version&gt;
    &lt;/dependency&gt;


    &lt;dependency&gt;
      &lt;groupId&gt;org.apache.storm&lt;/groupId&gt;
      &lt;artifactId&gt;storm-kafka&lt;/artifactId&gt;
      &lt;version&gt;${mapr-storm-kafka.version}&lt;/version&gt;
      &lt;exclusions&gt;
        &lt;exclusion&gt;
          &lt;groupId&gt;ch.qos.logback&lt;/groupId&gt;
          &lt;artifactId&gt;logback-classic&lt;/artifactId&gt;
        &lt;/exclusion&gt;
        &lt;exclusion&gt;
          &lt;groupId&gt;log4j&lt;/groupId&gt;
          &lt;artifactId&gt;log4j&lt;/artifactId&gt;
        &lt;/exclusion&gt;
        &lt;exclusion&gt;
          &lt;groupId&gt;org.slf4j&lt;/groupId&gt;
          &lt;artifactId&gt;log4j-over-slf4j&lt;/artifactId&gt;
        &lt;/exclusion&gt;
      &lt;/exclusions&gt;
    &lt;/dependency&gt;
</code></pre><br>
    2. 添加测试类
<p></p>
<p><span style="font-size:18px;"></span></p>
<pre><code class="language-java">package com.example.demo.kafka;

import java.util.Arrays;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.Properties;
import kafka.consumer.ConsumerConfig;
import kafka.consumer.ConsumerIterator;
import kafka.consumer.KafkaStream;
import kafka.javaapi.consumer.ConsumerConnector;
import kafka.serializer.StringDecoder;
import kafka.utils.VerifiableProperties;
import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.Producer;
import org.apache.kafka.clients.producer.ProducerRecord;
import org.junit.Test;


public class kafkaConsumer {

  private String topic="test";

  @Test
  public void Producer(){
    Properties props = new Properties();
    props.put("bootstrap.servers", "master1.hdp.com:6667");
    props.put("acks", "all"); //ack方式，all，会等所有的commit最慢的方式
    props.put("retries", 0); //失败是否重试，设置会有可能产生重复数据
    props.put("batch.size", 16384); //对于每个partition的batch buffer大小
    props.put("linger.ms", 1);  //等多久，如果buffer没满，比如设为1，即消息发送会多1ms的延迟，如果buffer没满
    props.put("buffer.memory", 33554432); //整个producer可以用于buffer的内存大小
    props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
    props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");
    Producer&lt;String, String&gt; producer = new KafkaProducer&lt;&gt;(props);
    producer.send(new ProducerRecord&lt;String, String&gt;(topic, "", Integer.toString(1)));


    producer.close();
  }

  private  ConsumerConnector consumer;

  @Test
  public  void kafkaConsumer() {

    Properties props = new Properties();

    // zookeeper 配置

    props.put("zookeeper.connect", "master1.hdp.com:2181,master2.hdp.com:2181,slave1.hdp.com:2181");

    // group 代表一个消费组

    props.put("group.id", "jd-group");

    // zk连接超时

    props.put("zookeeper.session.timeout.ms", "4000");

    props.put("zookeeper.sync.time.ms", "200");

    props.put("auto.commit.interval.ms", "1000");

    props.put("auto.offset.reset", "largest");

    // 序列化类

    props.put("serializer.class", "kafka.serializer.StringEncoder");

    ConsumerConfig config = new ConsumerConfig(props);

    consumer = (ConsumerConnector) kafka.consumer.Consumer.createJavaConsumerConnector(config);

    Map&lt;String, Integer&gt; topicCountMap = new HashMap&lt;String, Integer&gt;();

    topicCountMap.put("test", new Integer(1));

    StringDecoder keyDecoder = new StringDecoder(new VerifiableProperties());

    StringDecoder valueDecoder = new StringDecoder(
        new VerifiableProperties());
    Map&lt;String, List&lt;KafkaStream&lt;String, String&gt;&gt;&gt; consumerMap =
        consumer.createMessageStreams(topicCountMap, keyDecoder, valueDecoder);
    KafkaStream&lt;String, String&gt; stream = consumerMap.get(
        "test").get(0);
    ConsumerIterator&lt;String, String&gt; it = stream.iterator();
    while (it.hasNext())
      System.out.println(it.next().message());
  }
}</code></pre><br><p><br></p>
<p><span style="font-size:18px;">代码请参考个人git账号：</span><span style="font-size:18px;">https://github.com/whpHarper/springboot-kafka</span></p>
<p><a href="https://github.com/whpHarper/springboot-kafka" rel="nofollow">点击打开链接</a></p>
            </div>
                </div>