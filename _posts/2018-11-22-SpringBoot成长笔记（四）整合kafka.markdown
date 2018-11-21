---
layout:     post
title:      SpringBoot成长笔记（四）整合kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/mhm52368/article/details/83655835				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p></p><div class="toc"><h3>SpringBoot成长笔记（四）整合kafka</h3><ul><li><a href="#_2" rel="nofollow">目录</a></li><ul><li><a href="#_7" rel="nofollow">环境准备</a></li><li><a href="#windowkafka_14" rel="nofollow">window环境启动kafka</a></li><ul><li><a href="#zookeeper_17" rel="nofollow">启动zookeeper</a></li><li><a href="#kafka_19" rel="nofollow">启动kafka</a></li><li><a href="#topic_21" rel="nofollow">创建topic</a></li><li><a href="#_24" rel="nofollow">生成者和消费者测试</a></li></ul><li><a href="#_26" rel="nofollow">代码</a></li></ul><li><a href="#_210" rel="nofollow">自动提交修改为手动提交</a></li><ul><li><a href="#_243" rel="nofollow">源码</a></li></ul></ul></div><p></p>
<h1><a id="_2"></a>目录</h1>
<p>springboot作为微服务，集成kafka的场景导出可用，实现的方式也很多，可用使用kafka原生的代码，尤其是springboot新版本兼容老的kafka版本的时候，也可以使用集成kafka的方式。<br>
<img src="https://img-blog.csdnimg.cn/2018110215061114.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21obTUyMzY4,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<h2><a id="_7"></a>环境准备</h2>
<p>开发环境准备好，此处不多说：</p>
<ol>
<li><strong>springboot版本2.0.4.RELEASE</strong>，kafka我们使用kafka_2.12-1.0.1 ；</li>
<li>idea,jdk1.8</li>
<li>开始</li>
</ol>
<h2><a id="windowkafka_14"></a>window环境启动kafka</h2>
<p>现在kafka版本  [link] <a href="http://kafka.apache.org/" rel="nofollow">http://kafka.apache.org/</a><br>
<img src="https://img-blog.csdnimg.cn/20181102101230623.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21obTUyMzY4,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<h3><a id="zookeeper_17"></a>启动zookeeper</h3>
<p><img src="https://img-blog.csdnimg.cn/20181102101633775.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21obTUyMzY4,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<h3><a id="kafka_19"></a>启动kafka</h3>
<p><img src="https://img-blog.csdnimg.cn/20181102101748854.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21obTUyMzY4,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<h3><a id="topic_21"></a>创建topic</h3>
<p>kafka-topics.bat --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test<br>
不创建的话，kafka这个版本也会自动创建topic</p>
<h3><a id="_24"></a>生成者和消费者测试</h3>
<p><img src="https://img-blog.csdnimg.cn/20181102102135452.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21obTUyMzY4,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<h2><a id="_26"></a>代码</h2>
<p>配置文件</p>
<pre><code>#自定义属性
tcp.switch=true

#日志配置
logging.level.root=debug
#logging.path.classpth=logback-spring.xml.bak
logging.path.classpth=log4j2-spring.xml


#kafka生产者自定义配置
kafka.producer.servers=127.0.0.1:9092
kafka.producer.retries=0
kafka.producer.batch.size=4096
kafka.producer.linger=1
kafka.producer.buffer.memory=40960

#kafka消费者自定义配置
kafka.consumer.zookeeper.connect=127.0.0.1:2181
#bootstrap-servers
kafka.consumer.servers=127.0.0.1:9092
kafka.consumer.enable.auto.commit=true
kafka.consumer.session.timeout=6000
kafka.consumer.auto.commit.interval=100
# earliest
kafka.consumer.auto.offset.reset=latest
kafka.consumer.topic=test
kafka.consumer.group.id=test
kafka.consumer.concurrency=10
kafka.consumer.max.partition.fetch.bytes = 104857600
kafka.consumer.heartbeat.interval.ms=10000
</code></pre>
<p>生产者配置</p>
<pre><code>package com.mhm.kafka.producer;

import org.apache.kafka.clients.producer.ProducerConfig;
import org.apache.kafka.common.serialization.StringSerializer;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Primary;
import org.springframework.kafka.core.DefaultKafkaProducerFactory;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.kafka.core.ProducerFactory;

import java.util.HashMap;
import java.util.Map;

/**
 * Created by MHmon 2018/11/1.
 */
public class KafkaProducerConfig {
    @Value("${kafka.producer.servers}")
    private String servers;
    @Value("${kafka.producer.retries}")
    private String retries;
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
    @Primary
    public KafkaTemplate&lt;String, String&gt; kafkaTemplate() {
        return new KafkaTemplate&lt;String, String&gt;(producerFactory());
    }
    @Bean
    public KafkaProducer sender() {
        return new KafkaProducer();
    }
}

</code></pre>
<p>消费者配置</p>
<pre><code>package com.mhm.kafka.consumer;

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
 * Created by MHm on 2018/11/1.
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

    @Bean
    public KafkaConsumer listener() {
        return new KafkaConsumer();
    }

}

</code></pre>
<p>启动<br>
<img src="https://img-blog.csdnimg.cn/20181102105856932.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21obTUyMzY4,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<p>发送kafka消息<br>
<img src="https://img-blog.csdnimg.cn/20181102150408189.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21obTUyMzY4,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<p>控制台消息<br>
<img src="https://img-blog.csdnimg.cn/20181102150542839.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21obTUyMzY4,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<p>上面的方式，我们采用的是自动提交的方式，下面我采用手动提交的方式。</p>
<h1><a id="_210"></a>自动提交修改为手动提交</h1>
<p>kafka.consumer.enable.auto.commit=false</p>
<pre><code>package com.mhm.component;

import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Component;

/**
 * 手动提交的方式
 * Created by MHmon 2018/11/2.
 */
@Slf4j
@Component
public class TopicReceiver {

//    @KafkaListener(topics = "spring_boot_test", containerFactory = "kafkaListenerContainerFactory")
//    public void receive(String record, Acknowledgment ack) {
//        ack.acknowledge();
//        log.info("facebase----------------------:{}",record);
//    }
}

</code></pre>
<p>手动提交，配置项修改KafkaConsumerConfig.java</p>
<pre><code>//设置了手动提交enable.auto.commit必须为false        factory.getContainerProperties().setAckMode(AbstractMessageListenerContainer.AckMode.MANUAL);
</code></pre>
<p>我们可以根据不同topic订阅去消费处理不同的业务逻辑。</p>
<h2><a id="_243"></a>源码</h2>
<p><a href="https://github.com/289562326/springbootdemo22" rel="nofollow">源码：https://github.com/289562326/springbootdemo22</a></p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>