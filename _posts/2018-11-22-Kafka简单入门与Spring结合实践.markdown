---
layout:     post
title:      Kafka简单入门与Spring结合实践
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/caox_nazi/article/details/82147333				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <blockquote>
<h1><strong>Kafka简单入门与Spring结合实践</strong></h1>
</blockquote>

<h3><span style="color:#3399ea;"><strong>一、【安装部署kafka服务器环境（centos7.0）】: </strong></span></h3>

<p><strong><span style="color:#f33b45;">1.【注意】</span>新版的kafka已经内置了一个zookeeper环境</strong></p>

<p><span style="color:#3399ea;">2.【安装与运行】</span>：</p>

<p style="text-indent:50px;">可以在kafka官网 <a href="http://kafka.apache.org/downloads" rel="nofollow">http://kafka.apache.org/downloads</a>下载到最新的kafka安装包，选择下载二进制版本的tgz文件，本章用的是版本2.11_2.0.0，在centos7.0 直接解压即可</p>

<p>3.【运行命令】：</p>

<pre class="has">
<code class="language-java">./zookeeper-server-start.sh ../config/zookeeper.properties &amp;

./kafka-server-start.sh ../config/server.properties &amp;</code></pre>

<p><span style="color:#f33b45;">4.【注意】：</span></p>

<p><strong>【问题一】</strong>：Java端的消费者取不到消息，生产者消息也没发送成功，java通过kafka-client的API写的代码始终不能跟kafka通信：java producer的消息发不出去， java comsumer也收不到任何消息</p>

<p><strong>【解决办法】</strong>：修改kafka/config/server.properties 中的advertised.listeners 这个值改成自己虚拟机IP地址</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180828143930456?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Nhb3hfbmF6aQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p><strong>【问题二】</strong>：WARN [Consumer clientId=consumer-1, groupId=console-consumer-950] Connection to node -1 could not be established. Broker may not be available.</p>

<p><strong>【解决办法】</strong>：查看本机宿主机是否可以ping通centos7.0 虚拟机的IP地址，一般ping不通，虚拟机的IP地址改变了。</p>

<h3><span style="color:#3399ea;"> 二<strong>、【Java程序 + Spring -Kafka 运行实例】: </strong></span></h3>

<h3><span style="color:#3399ea;"><strong>参考博客：</strong></span><a href="https://www.cnblogs.com/hei12138/p/7805475.html" rel="nofollow">https://www.cnblogs.com/hei12138/p/7805475.html</a></h3>

<p><strong><span>1.【Java程序】 ：</span></strong></p>

<p>  pom.xml 依赖配置：<span style="color:#f33b45;">【注意】：其中版本号要与服务器端版本一致</span></p>

<pre class="has">
<code class="language-java">&lt;dependency&gt;
                &lt;groupId&gt;org.apache.kafka&lt;/groupId&gt;
                &lt;artifactId&gt;kafka-clients&lt;/artifactId&gt;
                &lt;version&gt;2.0.0&lt;/version&gt;
                &lt;exclusions&gt;
                    &lt;exclusion&gt;
                        &lt;groupId&gt;org.slf4j&lt;/groupId&gt;
                        &lt;artifactId&gt;slf4j-log4j12&lt;/artifactId&gt;
                    &lt;/exclusion&gt;
                &lt;/exclusions&gt;
            &lt;/dependency&gt;
            &lt;dependency&gt;
                &lt;groupId&gt;org.apache.kafka&lt;/groupId&gt;
                &lt;artifactId&gt;kafka-streams&lt;/artifactId&gt;
                &lt;version&gt;2.0.0&lt;/version&gt;
                &lt;exclusions&gt;
                    &lt;exclusion&gt;
                        &lt;groupId&gt;org.slf4j&lt;/groupId&gt;
                        &lt;artifactId&gt;slf4j-log4j12&lt;/artifactId&gt;
                    &lt;/exclusion&gt;
                &lt;/exclusions&gt;
            &lt;/dependency&gt;</code></pre>

<p> 【主题配置】：TopicMain.java</p>

<pre class="has">
<code class="language-java">package com.caox.kafka._01_topic;

import org.apache.kafka.clients.admin.AdminClient;
import org.apache.kafka.clients.admin.CreateTopicsResult;
import org.apache.kafka.clients.admin.NewTopic;

import java.util.ArrayList;
import java.util.Properties;
import java.util.concurrent.ExecutionException;

/**
 * Created by nazi on 2018/8/27.
 * @author nazi
 */
public class TopicMain {

    public static void main(String[] argv) throws Exception {
        //创建topic
        Properties props = new Properties();
        props.put("bootstrap.servers", "192.168.80.150:9092");
        AdminClient adminClient = AdminClient.create(props);
        ArrayList&lt;NewTopic&gt; topics = new ArrayList&lt;NewTopic&gt;();
        /**
         * NewTopic(String name, int numPartitions, short replicationFactor)
         * 的构造方法来创建了一个名为“topic-test”，分区数为1，复制因子为1的Topic.
         */
        NewTopic newTopic = new NewTopic("topic-test3", 1, (short) 1);
        topics.add(newTopic);
        CreateTopicsResult result = adminClient.createTopics(topics);
        try {
            result.all().get();
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
    }

}
</code></pre>

<p>【生产者】：ProducerMain.java</p>

<pre class="has">
<code class="language-java">package com.caox.kafka._01_topic;

import lombok.extern.slf4j.Slf4j;
import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.Producer;
import org.apache.kafka.clients.producer.ProducerRecord;

import java.util.Properties;

/**
 * Created by nazi on 2018/8/27.
 * @author nazi
 */
@Slf4j
public class ProducerMain {

    public static void main(String[] argv) throws Exception {
        Properties props = new Properties();
        props.put("bootstrap.servers", "192.168.80.149:9092");
        props.put("acks", "all");
        props.put("retries", 0);
        props.put("batch.size", 16384);
        props.put("linger.ms", 1);
        props.put("buffer.memory", 33554432);
        props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");

        Producer&lt;String,String&gt; producer = new KafkaProducer&lt;String, String&gt;(props);
        for (int i = 0; i &lt; 100; i++){
            producer.send(new ProducerRecord&lt;String, String&gt;("topic-test2", Integer.toString(i), Integer.toString(i)));
        }
        log.info("call SUCCESS");
        producer.close();
    }

}
</code></pre>

<p> 【消费者】：ConsumerMain.java</p>

<pre class="has">
<code class="language-java">package com.caox.kafka._01_topic;

import lombok.extern.slf4j.Slf4j;
import org.apache.kafka.clients.consumer.ConsumerRebalanceListener;
import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.KafkaConsumer;
import org.apache.kafka.common.TopicPartition;

import java.util.Arrays;
import java.util.Collection;
import java.util.Properties;

/**
 * Created by nazi on 2018/8/27.
 * @author nazi
 */
@Slf4j
public class ConsumerMain {

    public static void main(String[] argv) throws Exception {
        Properties props = new Properties();
        props.put("bootstrap.servers", "192.168.80.149:9092");
        props.put("group.id", "test");
        props.put("enable.auto.commit", "true");
        props.put("auto.commit.interval.ms", "1000");
        props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        final KafkaConsumer&lt;String, String&gt; consumer = new KafkaConsumer&lt;String,String&gt;(props);
        consumer.subscribe(Arrays.asList("topic-test2"),new ConsumerRebalanceListener() {
            public void onPartitionsRevoked(Collection&lt;TopicPartition&gt; collection) {
            }
            public void onPartitionsAssigned(Collection&lt;TopicPartition&gt; collection) {
                //将偏移设置到最开始
                consumer.seekToBeginning(collection);
            }
        });
        while (true) {
            ConsumerRecords&lt;String, String&gt; records = consumer.poll(100);
            for (ConsumerRecord&lt;String, String&gt; record : records){
                System.out.printf("offset = %d, key = %s, value = %s%n", record.offset(), record.key(), record.value());
            }
        }
    }

}
</code></pre>

<p> 2<strong><span>.【Spring-Kafka配置程序】 ：</span></strong></p>

<p><strong>【pom.xml】依赖配置：</strong></p>

<pre class="has">
<code class="language-java">&lt;dependency&gt;
	&lt;groupId&gt;org.springframework&lt;/groupId&gt;
	&lt;artifactId&gt;spring-context&lt;/artifactId&gt;
	&lt;version&gt;5.0.6.RELEASE&lt;/version&gt;
&lt;/dependency&gt;
&lt;dependency&gt;
	&lt;groupId&gt;org.springframework&lt;/groupId&gt;
	&lt;artifactId&gt;spring-core&lt;/artifactId&gt;
	&lt;version&gt;5.0.6.RELEASE&lt;/version&gt;
&lt;/dependency&gt;
&lt;dependency&gt;
	&lt;groupId&gt;org.springframework&lt;/groupId&gt;
	&lt;artifactId&gt;spring-beans&lt;/artifactId&gt;
	&lt;version&gt;5.0.6.RELEASE&lt;/version&gt;
&lt;/dependency&gt;
&lt;dependency&gt;
	&lt;groupId&gt;org.springframework.kafka&lt;/groupId&gt;
	&lt;artifactId&gt;spring-kafka&lt;/artifactId&gt;
	&lt;version&gt;2.1.6.RELEASE&lt;/version&gt;
&lt;/dependency&gt;</code></pre>

<p><strong><span>【kafka配置】：KafkaConfig.java：</span></strong></p>

<pre class="has">
<code class="language-java">package com.caox.kafka._02_spring_kafka;

import org.apache.kafka.clients.admin.AdminClientConfig;
import org.apache.kafka.clients.admin.NewTopic;
import org.apache.kafka.clients.producer.ProducerConfig;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.kafka.annotation.EnableKafka;
import org.springframework.kafka.config.ConcurrentKafkaListenerContainerFactory;
import org.springframework.kafka.core.*;

import java.util.HashMap;
import java.util.Map;

/**
 * Created by nazi on 2018/8/28.
 * @author nazi
 */
@Configuration
@EnableKafka
public class KafkaConfig {

    private static String BOOTSTRAP_SERVERS_CONFIG = "192.168.80.150:9092";

    /**
     * topic配置
     */
    /******************************************************************************************************************/
    @Bean
    public KafkaAdmin admin() {
        Map&lt;String, Object&gt; configs = new HashMap&lt;String, Object&gt;();
        configs.put(AdminClientConfig.BOOTSTRAP_SERVERS_CONFIG,BOOTSTRAP_SERVERS_CONFIG);
        return new KafkaAdmin(configs);
    }

    @Bean
    public NewTopic topic1() {
        return new NewTopic("foo", 10, (short) 1);
    }
    /******************************************************************************************************************/

    /**
     * 配置生产者Factory及Template
     */
    /******************************************************************************************************************/
    @Bean
    public ProducerFactory&lt;Integer, String&gt; producerFactory() {
        return new DefaultKafkaProducerFactory&lt;Integer,String&gt;(producerConfigs());
    }
    @Bean
    public Map&lt;String, Object&gt; producerConfigs() {
        Map&lt;String, Object&gt; props = new HashMap&lt;String,Object&gt;();
        props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, BOOTSTRAP_SERVERS_CONFIG);
        props.put("acks", "all");
        props.put("retries", 0);
        props.put("batch.size", 16384);
        props.put("linger.ms", 1);
        props.put("buffer.memory", 33554432);
        props.put("key.serializer", "org.apache.kafka.common.serialization.IntegerSerializer");
        props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        return props;
    }
    @Bean
    public KafkaTemplate&lt;Integer, String&gt; kafkaTemplate() {
        return new KafkaTemplate&lt;Integer, String&gt;(producerFactory());
    }
    /******************************************************************************************************************/

    /**
     * 配置ConsumerFactory
     */
    /******************************************************************************************************************/
    @Bean
    public ConcurrentKafkaListenerContainerFactory&lt;Integer,String&gt; kafkaListenerContainerFactory(){
        ConcurrentKafkaListenerContainerFactory&lt;Integer, String&gt; factory =
                new ConcurrentKafkaListenerContainerFactory&lt;Integer, String&gt;();
        factory.setConsumerFactory(consumerFactory());
        return factory;
    }

    @Bean
    public ConsumerFactory&lt;Integer,String&gt; consumerFactory(){
        return new DefaultKafkaConsumerFactory&lt;Integer, String&gt;(consumerConfigs());
    }


    @Bean
    public Map&lt;String,Object&gt; consumerConfigs(){
        HashMap&lt;String, Object&gt; props = new HashMap&lt;String, Object&gt;();
        props.put("bootstrap.servers", BOOTSTRAP_SERVERS_CONFIG);
        props.put("group.id", "test");
        props.put("enable.auto.commit", "true");
        props.put("auto.commit.interval.ms", "1000");
        props.put("key.deserializer", "org.apache.kafka.common.serialization.IntegerDeserializer");
        props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        return props;
    }
    /******************************************************************************************************************/

    /**
     * 默认spring-kafka会为每一个监听方法创建一个线程来向kafka服务器拉取消息
     */
    @Bean
    public SimpleConsumerListener simpleConsumerListener(){
        return new SimpleConsumerListener();
    }

}
</code></pre>

<p>【生产者配置】：ProducerMain.java</p>

<pre class="has">
<code class="language-java">package com.caox.kafka._02_spring_kafka;

import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.kafka.support.SendResult;
import org.springframework.util.concurrent.ListenableFuture;
import org.springframework.util.concurrent.ListenableFutureCallback;

/**
 * Created by nazi on 2018/8/28.
 * @author nazi
 */
public class ProducerMain {

    /**
     * 创建消息生产者
     * @param argv        参数
     * @throws Exception  异常
     */
    public static void main(String[] argv) throws Exception {
        AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(KafkaConfig.class);
        KafkaTemplate&lt;Integer, String&gt; kafkaTemplate = (KafkaTemplate&lt;Integer, String&gt;) ctx.getBean("kafkaTemplate");
        String data="this is a test message";
        ListenableFuture&lt;SendResult&lt;Integer, String&gt;&gt; send = kafkaTemplate.send("topic-test3", 1, data);
        send.addCallback(new ListenableFutureCallback&lt;SendResult&lt;Integer, String&gt;&gt;() {
            public void onFailure(Throwable throwable) {

            }

            public void onSuccess(SendResult&lt;Integer, String&gt; integerStringSendResult) {
                System.out.println("success to receive message !");
            }
        });
    }

}
</code></pre>

<p>【消费者配置】：消费者监听配置 SimpleConsumerListener.java：</p>

<pre class="has">
<code class="language-java">package com.caox.kafka._02_spring_kafka;

import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.kafka.annotation.KafkaListener;

import java.util.concurrent.CountDownLatch;

/**
 * Created by nazi on 2018/8/28.
 * @author nazi
 */
public class SimpleConsumerListener {
    private final static Logger logger = LoggerFactory.getLogger(SimpleConsumerListener.class);
    private final CountDownLatch latch1 = new CountDownLatch(1);

    @KafkaListener(id = "foo", topics = "topic-test3")
//    public void listen(byte[] records) {
//        //do something here
//        this.latch1.countDown();
//    }
    public void listen(ConsumerRecord record) {
        System.out.println("listen : " + " key:"+ record.key() + " value: " + record.value());
    }
}
</code></pre>

<p> </p>

<p> </p>

<p> </p>

<p> </p>            </div>
                </div>