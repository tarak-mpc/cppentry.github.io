---
layout:     post
title:      springboot集成kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>1.安装kafka  连接：<span style="color:#66cccc;">https://blog.csdn.net/qq_37497275/article/details/80507293</span></p><p><span style="color:#000000;">2.安装完成后：</span></p><p><span style="color:#000000;">通过springboot来调用和集成kafka</span></p><p><span style="color:#000000;">一、配置写在yml中</span></p><p><span style="color:#000000;">1.就是在yml中配置自己的kafka配置</span></p><pre><code class="language-plain">#============== kafka ===================
# 指定kafka 代理地址，可以多个
spring.kafka.bootstrap-servers=localhost:9092 
#=============== provider  =======================
spring.kafka.producer.retries=0
# 每次批量发送消息的数量
spring.kafka.producer.batch-size=16384
spring.kafka.producer.buffer-memory=33554432

# 指定消息key和消息体的编解码方式
spring.kafka.producer.key-serializer=org.apache.kafka.common.serialization.StringSerializer
spring.kafka.producer.value-serializer=org.apache.kafka.common.serialization.StringSerializer

#=============== consumer  =======================
# 指定默认消费者group id
spring.kafka.consumer.group-id=test-consumer-group

spring.kafka.consumer.auto-offset-reset=earliest
spring.kafka.consumer.enable-auto-commit=true
spring.kafka.consumer.auto-commit-interval=100

# 指定消息key和消息体的编解码方式
spring.kafka.consumer.key-deserializer=org.apache.kafka.common.serialization.StringDeserializer
spring.kafka.consumer.value-deserializer=org.apache.kafka.common.serialization.StringDeserializer</code></pre><p>2.创建生产者</p><pre><code class="language-plain">import java.util.Date;
import java.util.UUID;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.stereotype.Component;

import com.example.kafka.entity.Message;
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;

import lombok.extern.slf4j.Slf4j;

@Component
public class KafkaSender {

    @Autowired
    private KafkaTemplate&lt;String, String&gt; kafkaTemplate;
    
    private Logger log = LoggerFactory.getLogger(KafkaSender.class);

    private Gson gson = new GsonBuilder().create();

    //发送消息方法
    public void send() {
        Message message = new Message();
        message.setId(System.currentTimeMillis());
        message.setMsg(UUID.randomUUID().toString());
        message.setSendTime(new Date());
        log.info("+++++++++++++++++++++  message = {}", gson.toJson(message));
        kafkaTemplate.send("zhisheng", gson.toJson(message));
    }
}</code></pre><p>3.消费者的配置</p><pre><code class="language-plain">import java.util.Optional;

import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.kafka.annotation.KafkaListener;
import org.springframework.stereotype.Component;

import com.example.kafka.product.KafkaSender;

@Component
public class KafkaReceiver {

	
	 private Logger log = LoggerFactory.getLogger(KafkaSender.class);

	
    @KafkaListener(topics = {"zhisheng"})
    public void listen(ConsumerRecord&lt;?, ?&gt; record) {

    	//创建Listener 处理消息，使用@KafkaListener，用户监听topic
        Optional&lt;?&gt; kafkaMessage = Optional.ofNullable(record.value());

        if (kafkaMessage.isPresent()) {

            Object message = kafkaMessage.get();

            log.info("----------------- record =" + record);
            log.info("------------------ message =" + message);
        }

    }
}</code></pre><pre><code class="language-plain">@Controller
public class FeignController {

@Autowired
private KafkaSender ks;

    @GetMapping("/kafka/send")
    @ResponseBody
    public void testkafka() {

        ks.send();
       
    }
}</code></pre><p><br></p><p>二、配置卸载代码中</p><p>1.配置文件</p><pre><code class="language-plain">spring.kafka.consumer.group-id=cwenao-group
spring.kafka.bootstrap-servers=localhost:9092 
spring.kafka.template.default-topic=testtopic</code></pre><p>2.写一个生生产者的配置文件</p><pre><code class="language-java">import org.apache.kafka.clients.producer.ProducerConfig;
import org.apache.kafka.common.serialization.StringSerializer;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.kafka.core.DefaultKafkaProducerFactory;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.kafka.core.ProducerFactory;
 
import java.util.HashMap;
import java.util.Map;
 
@Configuration
public class KafkaProducersConfig {
 
  @Value("${spring.kafka.bootstrap-servers}")
  private String bootstrapServers;
 
  @Bean
  public Map&lt;String,Object&gt; producerConfigs() {
    Map&lt;String,Object&gt; properties = new HashMap&lt;&gt;();
    properties.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, this.bootstrapServers);
    properties.put(ProducerConfig.RETRIES_CONFIG, 0);
    properties.put(ProducerConfig.BATCH_SIZE_CONFIG, 16384);
    properties.put(ProducerConfig.BUFFER_MEMORY_CONFIG, 33554432);
    properties.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
    properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class);

    
    return properties;
  }
 
  @Bean
  public ProducerFactory&lt;String,String&gt; producerFactory() {
    return new DefaultKafkaProducerFactory&lt;&gt;(producerConfigs());
  }
 
  @Bean
  public KafkaTemplate&lt;String,String&gt; kafkaTemplate() {
    return new KafkaTemplate&lt;String, String&gt;(producerFactory());
  }
 
  @Bean
  public Sender sender() {
    return new Sender();
  }
}</code></pre><p>3.重写Sender方法</p><pre><code class="language-java">import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.kafka.core.KafkaTemplate;

 
public class Sender {
 
	Logger log = LoggerFactory.getLogger(Sender.class);
	
  @Autowired
  private KafkaTemplate&lt;String,String&gt; kafkaTemplate;
 
  public void send(String topic,String payload) {
    log.info("sending payload='{}' to topic='{}'",payload,topic);
    this.kafkaTemplate.send(topic,payload);
  }
}</code></pre><p>4.创建消费者</p><pre><code class="language-java">import org.apache.kafka.clients.consumer.ConsumerConfig;
import org.apache.kafka.common.serialization.StringDeserializer;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.kafka.annotation.EnableKafka;
import org.springframework.kafka.config.ConcurrentKafkaListenerContainerFactory;
import org.springframework.kafka.core.ConsumerFactory;
import org.springframework.kafka.core.DefaultKafkaConsumerFactory;
 
import java.util.HashMap;
import java.util.Map;
 
@Configuration
@EnableKafka
public class KafkaConsumerConfig {
 
  @Value("${spring.kafka.bootstrap-servers}")
  private String bootstrapServers;
  
  @Value("${spring.kafka.consumer.group-id}")
  private String group;
 
  public Map&lt;String,Object&gt; consumerConfigs() {
    Map&lt;String,Object&gt; props = new HashMap&lt;&gt;();
    
    props.put(ConsumerConfig.GROUP_ID_CONFIG, group);
    props.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, "earliest");
    props.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, true);
    props.put(ConsumerConfig.AUTO_COMMIT_INTERVAL_MS_CONFIG, "100");
    props.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapServers);
    props.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
    props.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);

    return props;
  }
 
  @Bean
  public ConsumerFactory&lt;String,String&gt; consumerFactory() {
    return new DefaultKafkaConsumerFactory&lt;&gt;(consumerConfigs());
  }
 
  @Bean
  public ConcurrentKafkaListenerContainerFactory&lt;String,String&gt; kafkaListenerContainerFactory() {
    ConcurrentKafkaListenerContainerFactory&lt;String,String&gt; factory =
        new ConcurrentKafkaListenerContainerFactory&lt;&gt;();
    factory.setConsumerFactory(consumerFactory());
    return factory;
  }
 
  @Bean
  public Receiver receiver() {
    return new Receiver();
  }
 
}</code></pre><p>5.重写Receiver方法</p><pre><code class="language-java">import java.util.concurrent.CountDownLatch;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.kafka.annotation.KafkaListener;


public class Receiver {

    Logger log = LoggerFactory.getLogger(Receiver.class);
	private CountDownLatch latch = new CountDownLatch(1);
	 
	  public CountDownLatch getLatch() {
	    return latch;
	  }
	 
	  @KafkaListener(topics = "${spring.kafka.template.default-topic}")
	  public void receive(String payload) {
	    log.info("received payload='{}'",payload);
	    latch.countDown();
	  }
	
}

</code></pre><p>6.controller</p><pre><code class="language-plain">import java.util.Date;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

import com.example.kafka.config.Sender;
import com.example.kafka.entity.Message;
import com.google.gson.Gson;


@Controller
public class FeignController {

	@Autowired
	Sender sender;

    @GetMapping("/kafka/send")
    @ResponseBody
    public void testkafka() {
    	Message bean = new Message();
        bean.setId(110L);
        bean.setMsg("222");
        bean.setSendTime(new Date());
        Gson gson = new Gson();
       this.sender.send("testtopic",gson.toJson(bean));
    }
}

</code></pre><pre><code class="language-plain">
</code></pre><p>注意：如果说出现错误：org.apache.kafka.common.errors.TimeoutException: Failed to update metadata after 60000 ms</p><p>解决方法：</p><p>在con.cfg中最后加上</p><p><span style="font-family:'-apple-system', 'SF UI Text', Arial, 'PingFang SC', 'Hiragino Sans GB', 'Microsoft YaHei', 'WenQuanYi Micro Hei', sans-serif;background-color:rgb(255,255,255);"><span style="color:#ff0000;">原因是kafka客户端连接到broker是成功的，但连接到集群后更新回来的集群meta信息是错误的即是会返回的是节点的hostname，解决办法就是手动配置advertised.host.name和advertised.port，2个参数都必须配置，重启问题解决</span></span><span style="color:rgb(47,47,47);font-family:'-apple-system', 'SF UI Text', Arial, 'PingFang SC', 'Hiragino Sans GB', 'Microsoft YaHei', 'WenQuanYi Micro Hei', sans-serif;background-color:rgb(255,255,255);">：</span><br></p><pre><code class="language-plain">advertised.host.name=localhost
advertised.port=9092</code></pre><br><p><br></p>            </div>
                </div>