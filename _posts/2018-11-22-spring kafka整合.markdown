---
layout:     post
title:      spring kafka整合
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>spring kafka</p>

<p><a href="https://docs.spring.io/spring-kafka/docs/1.1.7.RELEASE/reference/htmlsingle/" rel="nofollow">https://docs.spring.io/spring-kafka/docs/1.1.7.RELEASE/reference/htmlsingle/</a></p>

<h1 id="h1_1">kafka学习（四）---- Kafka整合SpringMVC实例（二）</h1>

<p><a href="https://my.oschina.net/zhengweishan/blog/736716" rel="nofollow">https://my.oschina.net/zhengweishan/blog/736716</a></p>

<p><a href="https://github.com/wesley5201314/Kafka_study_demo" rel="nofollow">https://github.com/wesley5201314/Kafka_study_demo</a></p>

<p> </p>

<h1>kafka分布式消息队列使用(springboot和springmvc)</h1>

<p>转载<a href="https://blog.csdn.net/trntaken/article/details/77924283" rel="nofollow">https://blog.csdn.net/trntaken/article/details/77924283</a></p>

<p>Kafak版本：kafka_2.12-0.10.2.0</p>

<p>Zookeeper版本：zookeeper-3.4.9</p>

<p>kafka是一个高并发的基于发布订阅模式的分布式消息队列系统。kafka具有更好的吞吐量、内置的分区、复制和容错能力，这使它成为大型消息处理应用程序的一个很好的解决方案。</p>

<p> 安装和配置<br>
kafka依赖于zookeeper，首先要下载安装zookeeper。这个其它文章有介绍安装方法。</p>

<p>下载kafka_2.12-0.10.2.0.tar.gz 解压。修改config目录下 server.properties配置：</p>

<p>zookeeper.connect=localhost:2181 # zookeeper 注册中心<br>
log.dirs=/opt/kafka/kafka-logs #log目录<br>
num.partitions=2 #主题默认分区个数<br>
listeners=PLAINTEXT://192.168.0.130:9092<br>
broker.id=0  #id标识，在集群中，必须是唯一的整形数字</p>

<p>操作指令<br>
启动：bin/kafka-server-start.sh  config/server.properties  。如果集群的话，只需复制粘贴server.properties 重命名 server1.properties，修改broker.id和log.dirs。启动脚本替换使用该配置即可。</p>

<p>创建topic：bin/kafka-topics.sh  --zookeeper localhost:2181 --create --topic test --partitions 2 --replication-factor 2</p>

<p>查看所有topic：bin/kafka-topics.sh  --list --zookeeper localhost:2181</p>

<p>查看topic ： bin/kafka-topics.sh  --describe --zookeeper localhost:2181 --topic test</p>

<p>修改topic ：bin/kafka-topics.sh --zookeeper zk_host:port/chroot --alter --topic my_topic_name   --partitions 40</p>

<p>使用<br>
springboot使用<br>
springboot使用起来很简单，在springboot学习笔记一种已经将配置和依赖添加好了，这里在简单的把代码贴出来:</p>

<p>gradle或maven加入以下依赖（以gralde为例）：</p>

<p>compile('org.springframework.kafka:spring-kafka')<br>
application.yml加入以下配置：</p>

<pre class="has">
<code>spring:
   kafka:
      bootstrap-servers: 192.168.0.130:9092,192.168.0.130:9293
      template.default-topic: bootkafka
      listener:
         concurrency: 10   #并发数
      producer:
         bootstrap-servers: 192.168.0.130:9092,192.168.0.130:9293  #中间件ip：port
         #key指定key 和value序列化方式
         key-serializer: org.apache.kafka.common.serialization.StringSerializer 
         value-serializer: org.apache.kafka.common.serialization.StringSerializer
         #发送消息失败情况下，尝试放送消息的次数
         retries: 3
         batch-size: 16384
         acks: 1
         linger:
            ms: 1
      consumer:
         bootstrap-servers: 192.168.0.130:9092,192.168.0.130:9293
         key-serializer: org.apache.kafka.common.serialization.StringSerializer
         #消费者组，默认
         group-id: boot</code></pre>

<p><br>
这样一个就配置好了，springboot配置比springmvc简单的多。</p>

<p>producer发布消息：</p>

<pre class="has">
<code class="language-java">private @Autowired KafkaTemplate&lt;String, String&gt; kafkaTemplate; 
 
   @RequestMapping(value = "/home", method = RequestMethod.GET)
     public String home() {
        try {
            Map&lt;String,Object&gt; message=new HashMap&lt;String,Object&gt;();
            message.put("description","kafka 消息测试");
            message.put("topic","主题是 bootkafka");
            message.put("timestamp",System.currentTimeMillis()/1000);
            String stringValue = JSONObject.toJSONString(message);
            kafkaTemplate.sendMessage("bootkafka",stringValue);//主题，消息
        
         } catch (Exception e) {
            e.printStackTrace();
        }</code></pre>

<p><br>
consumer消费消息，这里使用spring注解方式，比较简单：</p>

<pre class="has">
<code class="language-java">@Component
public class KafkaConsumer {
 
    private Logger logger = LoggerFactory.getLogger(getClass());
 
    @KafkaListener(topics = {"bootkafka" })
    public void listen(String data) {
        logger.info("收到kafka消息" + data);
    }}</code></pre>

<p><br>
springmvc使用<br>
springmvc的配置就比较多一点</p>

<p>gralde或maven加入以下依赖：</p>

<pre class="has">
<code>compile 'org.apache.kafka:kafka-clients:0.10.1.0'
 
compile 'org.springframework.kafka:spring-kafka:1.1.1.RELEASE'</code></pre>

<p><br>
application.properties:</p>

<pre class="has">
<code>#bootstrap.servers=192.168.0.130:9092,192.168.0.130:9093,192.168.0.130:9091
kafka.bootstrap.servers=192.168.0.130:9092,192.168.0.130:9093
kafka.group.id=0
kafka.retries=1
kafka.batch.size=16384
kafka.linger.ms=1
kafka.buffer.memory=33554432
kafka.key.serializer=org.apache.kafka.common.serialization.StringSerializer
kafka.value.serializer=org.apache.kafka.common.serialization.StringSerializer</code></pre>

<p>创建配置文件：spring-kafka.xml:</p>

<pre class="has">
<code>&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p" xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
       xmlns:cache="http://www.springframework.org/schema/cache" xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/aop
        http://www.springframework.org/schema/aop/spring-aop.xsd
        http://www.springframework.org/schema/tx
        http://www.springframework.org/schema/tx/spring-tx.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd"&gt;
 
    &lt;!-- 定义producer的参数 --&gt;
        &lt;bean id="producerProperties" class="java.util.HashMap"&gt;
            &lt;constructor-arg&gt;
                &lt;map&gt;
                    &lt;!--连接到kafka集群--&gt;
                    &lt;entry key="bootstrap.servers" value="${bootstrap.servers}" /&gt;
                    &lt;entry key="group.id" value="${kafka.group.id}" /&gt;
                    &lt;!--发送失败了，重新尝试次数--&gt;
                    &lt;entry key="retries" value="${kafka.retries}" /&gt;
                    &lt;!--批量记录的最大量--&gt;
                    &lt;entry key="batch.size" value="16384" /&gt;
                    &lt;!--消息延迟发送到broker--&gt;
                    &lt;entry key="linger.ms" value="1" /&gt;
                    &lt;entry key="buffer.memory" value="33554432" /&gt;
                    &lt;!--序列化key实现的接口--&gt;
                    &lt;entry key="key.serializer"
                    value="${kafka.key.serializer}" /&gt;
                    &lt;entry key="value.serializer"
                    value="${kafka.value.serializer}" /&gt;
                &lt;/map&gt;
            &lt;/constructor-arg&gt;
        &lt;/bean&gt;
     &lt;context:property-placeholder location="classpath*:config/config.properties"/&gt;
 
    &lt;!-- 创建kafkatemplate需要使用的producerfactory bean --&gt;
    &lt;bean id="producerFactory" class="org.springframework.kafka.core.DefaultKafkaProducerFactory"&gt;
        &lt;constructor-arg&gt;
            &lt;ref bean="producerProperties"/&gt;
        &lt;/constructor-arg&gt;
    &lt;/bean&gt;
 
    &lt;!-- 创建kafkatemplate bean，使用的时候，只需要注入这个bean，即可使用template的send消息方法 --&gt;
    &lt;bean id="KafkaTemplate" class="org.springframework.kafka.core.KafkaTemplate"&gt;
        &lt;constructor-arg ref="producerFactory"/&gt;
        &lt;constructor-arg name="autoFlush" value="true"/&gt;
        &lt;property name="defaultTopic" value="mhb-test"/&gt;
        &lt;property name="producerListener" ref="producerListener"/&gt;
    &lt;/bean&gt;
&lt;!-- 定义producer监听器，如果发送消息，会触发这个类 --&gt;
    &lt;bean id="producerListener" class="com.test.myspring.kafka.kafkaProducerListener" /&gt;
 
 
      &lt; 定义消费者consumer的参数 --&gt;
       &lt;bean id="consumerProperties" class="java.util.HashMap"&gt;
           &lt;constructor-arg&gt;
               &lt;map&gt;
                   &lt;entry key="bootstrap.servers" value="${bootstrap.servers}"/&gt;
                   &lt;entry key="group.id" value="0"/&gt;
                   &lt;entry key="enable.auto.commit" value="false"/&gt;
                   &lt;entry key="auto.commit.interval.ms" value="1000"/&gt;
                   &lt;entry key="session.timeout.ms" value="15000"/&gt;
                   &lt;entry key="key.deserializer" value="org.apache.kafka.common.serialization.StringDeserializer"/&gt;
                   &lt;entry key="value.deserializer" value="org.apache.kafka.common.serialization.StringDeserializer"/&gt;
               &lt;/map&gt;
           &lt;/constructor-arg&gt;
       &lt;/bean&gt;
 
      &lt;!--  创建消费者工厂consumerFactory bean --&gt;;
       &lt;bean id="consumerFactory" class="org.springframework.kafka.core.DefaultKafkaConsumerFactory"&gt;
           &lt;constructor-arg&gt;
               &lt;ref bean="consumerProperties"/&gt;
           &lt;/constructor-arg&gt;
       &lt;/bean&gt;
 
       &lt;!-- 如果采用和上面所述注解的方式创建消费者bean，就不需要下面这些配置 --&gt;
       &lt;bean id="messageListernerConsumerService" class="com.test.myspring.kafka.KafkaConsumerServer"/&gt;
 
       &lt;!-- 消费者容器配置信息 --&gt;
       &lt;bean id="containerProperties_trade" class="org.springframework.kafka.listener.config.ContainerProperties"&gt;
           &lt;constructor-arg value="mhb-test"/&gt;
           &lt;property name="messageListener" ref="messageListernerConsumerService"/&gt;
       &lt;/bean&gt;
       &lt;bean id="containerProperties_other" class="org.springframecck.kafka.listener.config.ContainerProperties"&gt;
           &lt;constructor-arg value="other_test_topic"/&gt;
           &lt;property name="messageListener" ref="messageListernerConsumerService"/&gt;
       &lt;/bean&gt;
 
      
            
&lt;/beans&gt;</code></pre>

<p><br>
web.xml 加上：</p>

<pre class="has">
<code>&lt;param-value&gt;classpath*:/config/spring-kafka.xml&lt;/param-value&gt;

</code></pre>

<p><br>
如果不想创建spring-kafka.xml 配置文件。我们可以自己创建KakfaFactory bean来初始化kafka：</p>

<pre class="has">
<code class="language-java">package com.wtsd.myspring.kafka;
 
import org.apache.commons.lang3.StringUtils;
import org.apache.log4j.Logger;
import org.springframework.core.io.ClassPathResource;
import org.springframework.core.io.Resource;
import org.springframework.kafka.core.DefaultKafkaProducerFactory;
import org.springframework.kafka.core.KafkaTemplate;
 
import java.io.IOException;
import java.io.InputStream;
import java.util.*;
 
/**
 * 生成kafka工厂类
 *
 * @author test
 * @date 2017/4/4 11:27
 * @Package com.myspring.kafka
 * @Version v1.0
 */
public class KakfaFactory {
    private static final Logger logger = Logger.getLogger(KakfaFactory.class);
 
    public static DefaultKafkaProducerFactory&lt;String, String&gt; kafkaProducerFactory;
 
    public static volatile  KafkaTemplate&lt;String, String&gt; kafkaTemplate;
 
    /**
     * 创建factory
     *
     * @param []
     * @return
     * @throws
     * @date 2017/4/4 19:04
     */
    public static DefaultKafkaProducerFactory&lt;String, String&gt; getKafkaFactory() {
     
        return   new DefaultKafkaProducerFactory&lt;String, String&gt;(getConfigs());
    }
 
    /**
     * 读取*.properties中文件
     *
     * @param []
     * @return
     * @throws
     * @date 2017/4/4 19:04
     */
    public static Map&lt;String, Object&gt; getConfigs() {
 
        logger.info("&gt;&gt;&gt;&gt; 加载kafka配置参数 &lt;&lt;&lt;&lt;&lt;");
        Resource resource = new ClassPathResource("config/application.properties");
        Map&lt;String, Object&gt; config = new HashMap&lt;String, Object&gt;();
        try {
            InputStream inputStream = resource.getInputStream();
            Properties properties = new Properties();
            properties.load(inputStream);
            inputStream.close();
            config = getKafakProperteis(properties);
        } catch (IOException e) {
            logger.error("加载kafka配置参数", e);
        }
        logger.info("&gt;&gt;&gt;配置参数：" + config);
        return config;
    }
 
    /**
     * 获取kafka的配置参数
     *
     * @param [properties]
     * @return
     * @throws
     * @date 2017/4/4 13:02
     */
    public static Map&lt;String, Object&gt; getKafakProperteis(Properties properties) {
        HashMap&lt;String, Object&gt; config;
        Set&lt;String&gt; set = new HashSet&lt;String&gt;();
        if (null != properties) {
            config = new HashMap&lt;String, Object&gt;();
            set = properties.stringPropertyNames();
            for (String s : set) {
                if (StringUtils.contains(s, "kafka") &amp;&amp; StringUtils.isNotBlank(properties.getProperty(s))) {
                    config.put(s.replace("kafka.", ""), properties.getProperty(s));
                }
            }
            return config;
        }
        return null;
    }
 
    public static KafkaTemplate&lt;String, String&gt; getKafkaTemplate() {
      
 
        if (kafkaTemplate == null) {
          logger.info("开始获取kafkaTemplae");
 
          synchronized (kafkaTemplate ) {
          if (kafkaTemplate == null) {
      
            kafkaTemplate = new KafkaTemplate&lt;String, String&gt;(getKafkaFactory(), true);
 
           }
        }
        return kafkaTemplate;
 
    }
 
}

</code></pre>

<p><br>
生产者发送消息和消费者消费消息和springboot使用方法一样，这里就不介绍了。<br>
--------------------- <br>
作者：trntaken <br>
来源：CSDN <br>
原文：https://blog.csdn.net/trntaken/article/details/77924283 <br>
版权声明：本文为博主原创文章，转载请附上博文链接！</p>            </div>
                </div>