---
layout:     post
title:      springboot kafka集成&简单实现
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/fclwd/article/details/82422784				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>本文介绍如何在springboot项目中集成kafka收发message。</p>

<p><strong>1、kafka安装及下载</strong></p>

<p>kafka下载：<a href="http://kafka.apache.org/" rel="nofollow">http://kafka.apache.org/</a></p>

<p>spring boot kafka介绍和版本对应关系：<a href="https://spring.io/projects/spring-kafka" rel="nofollow">https://spring.io/projects/spring-kafka</a></p>

<p><strong>1、解决依赖</strong></p>

<p>springboot相关的依赖我们就不提了，和kafka相关的只依赖一个spring-kafka集成包</p>

<pre class="has">
<code class="language-java">    &lt;parent&gt;
		&lt;groupId&gt;org.springframework.boot&lt;/groupId&gt;
		&lt;artifactId&gt;spring-boot-starter-parent&lt;/artifactId&gt;
		&lt;version&gt;1.5.2.RELEASE&lt;/version&gt;
		&lt;relativePath/&gt;
	&lt;/parent&gt;

	&lt;properties&gt;
		&lt;project.build.sourceEncoding&gt;UTF-8&lt;/project.build.sourceEncoding&gt;
		&lt;project.reporting.outputEncoding&gt;UTF-8&lt;/project.reporting.outputEncoding&gt;
		&lt;java.version&gt;1.8&lt;/java.version&gt;
	&lt;/properties&gt;

	&lt;dependencies&gt;
		&lt;dependency&gt;
			&lt;groupId&gt;org.springframework.boot&lt;/groupId&gt;
			&lt;artifactId&gt;spring-boot-starter-web&lt;/artifactId&gt;
		&lt;/dependency&gt;

		&lt;dependency&gt;
			&lt;groupId&gt;org.springframework.boot&lt;/groupId&gt;
			&lt;artifactId&gt;spring-boot-starter-test&lt;/artifactId&gt;
			&lt;scope&gt;test&lt;/scope&gt;
		&lt;/dependency&gt;
		&lt;dependency&gt;
			&lt;groupId&gt;org.springframework.kafka&lt;/groupId&gt;
			&lt;artifactId&gt;spring-kafka&lt;/artifactId&gt;
			&lt;version&gt;1.1.0.RELEASE&lt;/version&gt;
		&lt;/dependency&gt;
	&lt;/dependencies&gt;</code></pre>

<p> 这里我们先把配置文件展示一下</p>

<p> </p>

<pre>
#============== kafka ===================application.yml
server:
  port: 8086
kafka:
  consumer:
    zookeeper.connect: 192.168.0.164:2181
    servers: 192.168.0.164:9092
    enable.auto.commit: true
    session.timeout: 6000
    auto.commit.interval: 100
    auto.offset.reset: latest
    topic: test
    group.id: test
    concurrency: 10
  producer:
    servers: 192.168.0.164:9092
    retries: 0
    batch.size: 40960
    linger: 1
    buffer.memory: 40960</pre>

<p><strong>2、Configuration：Kafka producer </strong></p>

<p>1）通过@Configuration、@EnableKafka，声明Config并且打开KafkaTemplate能力。</p>

<p>2）通过@Value注入application.properties配置文件中的kafka配置。</p>

<p>3）生成bean，@Bean</p>

<pre class="has">
<code class="language-java">package com.example.demo.kafka;

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
}</code></pre>

<p>实验我们的producer，写一个Controller。想topic=test，key=key，发送消息message</p>

<pre class="has">
<code class="language-java">package com.example.demo.controller;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.web.bind.annotation.*;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import static com.sun.xml.internal.ws.api.message.Packet.Status.Response;


@RestController
@RequestMapping("/kafka")
public class CollectController {
    @Autowired
    private KafkaTemplate kafkaTemplate;

    @RequestMapping(value = "/send", method = RequestMethod.GET)
    public String sendKafka(HttpServletRequest request, HttpServletResponse response) {
        try {
            String message = request.getParameter("message");
            System.out.println("kafka的消息={}"+ message);
            kafkaTemplate.send("test", "key", message);
            System.out.println("发送kafka成功.");
            return "success";
        } catch (Exception e) {
            System.out.println("发送kafka失败"+e.getMessage());
            return "error";
        }
    }

}</code></pre>

<p><strong>3、configuration：kafka consumer</strong></p>

<p>1）通过@Configuration、@EnableKafka，声明Config并且打开KafkaTemplate能力。</p>

<p>2）通过@Value注入application.properties配置文件中的kafka配置。</p>

<p>3）生成bean，@Bean</p>

<pre class="has">
<code class="language-java">package com.example.demo.kafka;

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
}</code></pre>

<p>new Listener()生成一个bean用来处理从kafka读取的数据。Listener简单的实现demo如下：只是简单的读取并打印key和message值</p>

<p>@KafkaListener中topics属性用于指定kafka topic名称，topic名称由消息生产者指定，也就是由kafkaTemplate在发送消息时指定。</p>

<pre class="has">
<code class="language-java">package com.example.demo.kafka;
import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.springframework.kafka.annotation.KafkaListener;

/**
 * Created by liuwd on 2018/9/5.
 */
public class ListenerKafka {
    @KafkaListener(topics = {"test"})
    public void listen(ConsumerRecord&lt;?, ?&gt; record) {
        System.out.println("kafka的key: " + record.key());
        System.out.println("kafka的value: " + record.value().toString());
    }
    //@KafkaListener(topics = {"test1"})可设置多个topics
}
</code></pre>

<p><strong>测试结果</strong></p>

<pre class="has">
<code class="language-java">
2018-09-05 12:39:18.933  INFO 13128 --- [nio-8086-exec-1] o.a.kafka.common.utils.AppInfoParser     : Kafka version : 0.10.0.1
2018-09-05 12:39:18.933  INFO 13128 --- [nio-8086-exec-1] o.a.kafka.common.utils.AppInfoParser     : Kafka commitId : a7a17cdec9eaa6c5
发送kafka成功.
kafka的key: key
kafka的value: lwd
kafka的消息={}lwdwwww
发送kafka成功.
kafka的key: key
kafka的value: lwdwwww
</code></pre>

<p><strong>tips:</strong></p>

<p>1）我没有介绍如何安装配置kafka，配置kafka时最好用完全bind网络ip的方式，而不是localhost或者127.0.0.1</p>

<p>2）最好不要使用kafka自带的zookeeper部署kafka，可能导致访问不通。</p>

<p>3）理论上consumer读取kafka应该是通过zookeeper，但是这里我们用的是kafkaserver的地址，为什么没有深究。</p>

<p>4）定义监听消息配置时，GROUP_ID_CONFIG配置项的值用于指定消费者组的名称，如果同组中存在多个监听器对象则只有一个监听器对象能收到消息。</p>

<p> </p>            </div>
                </div>