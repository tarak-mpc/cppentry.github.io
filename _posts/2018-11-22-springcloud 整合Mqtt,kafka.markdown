---
layout:     post
title:      springcloud 整合Mqtt,kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/study4034/article/details/84140644				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1>springcloud 整合Mqtt,kafka (rabbit与kafka整和方式相同)</h1>

<h2>原理说明：</h2>

<p>springcloud整合mqtt主要是使用spring-boot-starter-integration、spring-integration-mqtt，springcloud整合kafka、rabbitmq主要是使用spring-cloud-starter-stream-kafka</p>

<h2>springcloud整合mqtt</h2>

<h3>依赖：</h3>

<pre>
&lt;!--mqtt--&gt;
&lt;dependency&gt;
    &lt;groupId&gt;org.springframework.boot&lt;/groupId&gt;
    &lt;artifactId&gt;spring-boot-starter-integration&lt;/artifactId&gt;
&lt;/dependency&gt;
&lt;dependency&gt;
    &lt;groupId&gt;org.springframework.integration&lt;/groupId&gt;
    &lt;artifactId&gt;spring-integration-stream&lt;/artifactId&gt;
&lt;/dependency&gt;
&lt;dependency&gt;
    &lt;groupId&gt;org.springframework.integration&lt;/groupId&gt;
    &lt;artifactId&gt;spring-integration-mqtt&lt;/artifactId&gt;
&lt;/dependency&gt;</pre>

<h3>整合代码：</h3>

<pre>
import cn.enncloud.iot.iotmqtttransferkafkahps.constant.AdapterProperties;
import cn.enncloud.iot.iotmqtttransferkafkahps.process.MessageMqttProcessHandler;
import lombok.extern.slf4j.Slf4j;
import org.eclipse.paho.client.mqttv3.MqttConnectOptions;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Primary;
import org.springframework.integration.annotation.MessagingGateway;
import org.springframework.integration.channel.DirectChannel;
import org.springframework.integration.dsl.IntegrationFlow;
import org.springframework.integration.dsl.IntegrationFlows;
import org.springframework.integration.endpoint.MessageProducerSupport;
import org.springframework.integration.handler.LoggingHandler;
import org.springframework.integration.mqtt.core.DefaultMqttPahoClientFactory;
import org.springframework.integration.mqtt.core.MqttPahoClientFactory;
import org.springframework.integration.mqtt.inbound.MqttPahoMessageDrivenChannelAdapter;
import org.springframework.integration.mqtt.outbound.MqttPahoMessageHandler;
import org.springframework.integration.mqtt.support.DefaultPahoMessageConverter;
import org.springframework.messaging.Message;
import org.springframework.messaging.MessageChannel;
import org.springframework.messaging.MessageHandler;
import org.springframework.messaging.MessagingException;

import java.util.Optional;

@Slf4j
@Configuration
public class MqttConfig {
    @Autowired
    private MessageMqttProcessHandler messageProcess;
    @Autowired
    private AdapterProperties adapterProperties;
    @Bean
    public MqttPahoClientFactory mqttClientFactory() {
        DefaultMqttPahoClientFactory factory = new DefaultMqttPahoClientFactory();
        MqttConnectOptions options = new MqttConnectOptions();
        options.setServerURIs(new String[] { adapterProperties.getHost() });
        options.setUserName(adapterProperties.getUsername());
        options.setPassword(adapterProperties.getPassword().toCharArray());
        options.setCleanSession(adapterProperties.isCleanSession());
        factory.setConnectionOptions(options);
        return factory;
    }

    // publisher

    @Bean
    public IntegrationFlow mqttOutFlow() {
        //console input
//        return IntegrationFlows.from(CharacterStreamReadingMessageSource.stdin(),
//                e -&gt; e.poller(Pollers.fixedDelay(1000)))
//                .transform(p -&gt; p + " sent to MQTT")
//                .handle(mqttOutbound())
//                .get();
        return IntegrationFlows.from(outChannelMqtt())
                .handle(mqttOutbound())
                .get();
    }

    @Primary
    @Bean
    public MessageChannel outChannelMqtt() {
        return new DirectChannel();
    }

    @Bean
    public MessageHandler mqttOutbound() {
        MqttPahoMessageHandler messageHandler = new MqttPahoMessageHandler(adapterProperties.getPublisher(), mqttClientFactory());
        messageHandler.setAsync(true);
        messageHandler.setDefaultTopic(adapterProperties.getTopic());
        return messageHandler;
    }

    // consumer

    @Bean
    public IntegrationFlow mqttInFlow() {
        return IntegrationFlows.from(mqttInbound())
//                .transform(p -&gt; p + ", received from MQTT")
//                .handle(logger())
                .handle(accephandler())
                .get();
    }
    @Bean
//   这里注入处理逻辑服务
    public MessageHandler accephandler() {
        return new MessageHandler() {
            @Override
            public void handleMessage(Message&lt;?&gt; message) throws MessagingException {
                Optional optional = Optional.ofNullable(message.getPayload());
                if(optional.isPresent()){
                    messageProcess.doProcess(message);
                }else{
                    log.info("my"+message.getHeaders());
                }

            }

        };
    }


    private LoggingHandler logger() {
        LoggingHandler loggingHandler = new LoggingHandler("INFO");
        loggingHandler.setLoggerName("siSample");
        return loggingHandler;
    }

    @Bean
    public MessageProducerSupport mqttInbound() {
        MqttPahoMessageDrivenChannelAdapter adapter = new MqttPahoMessageDrivenChannelAdapter(adapterProperties.getConsumer(),
                mqttClientFactory(), adapterProperties.getTopic());
        adapter.setCompletionTimeout(adapterProperties.getTimeout());
        adapter.setConverter(new DefaultPahoMessageConverter());
        adapter.setQos(adapterProperties.getQos());
        return adapter;
    }
    @MessagingGateway(defaultRequestChannel = "outChannelMqtt")
    public interface MessageWriter{
        void write(String data);
    }



}</pre>

<h3>写一个发送消息demo</h3>

<pre>
import cn.enncloud.iot.iotmqtttransferkafkahps.configuration.MqttConfig;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@Slf4j
@RestController
public class SendMessageController {

    @Autowired
    MqttConfig.MessageWriter messageWriter;


    @RequestMapping(value = "send",method = RequestMethod.GET)
    public void send(@RequestParam(value = "message") String message){
        log.info("收到meseage"+message);
        messageWriter.write(message);

    }

}</pre>

<h3>yml中配置：</h3>

<pre>
enn:
  mqtt:
    # todo
    host: tcp://**:1883
    publisher: samplePublisher
    # todo
    consumer: dampleConsumerprod2
    # 共享订阅
    #    topic: $share/group1/allInOne
    # 本地共享订阅
    topic: $local/$share/group1/allInOne
    username: %%
    password: %%
    timeout: 5000
    qos: 2
    cleanSession: false</pre>

<h2>springcloud整合kafka(rabbitmq相同)</h2>

<h3>依赖：</h3>

<pre>
&lt;dependency&gt;
    &lt;groupId&gt;org.springframework.cloud&lt;/groupId&gt;
    &lt;artifactId&gt;spring-cloud-stream&lt;/artifactId&gt;
&lt;/dependency&gt;
&lt;!--&lt;dependency&gt;--&gt;
    &lt;!--&lt;groupId&gt;org.springframework.cloud&lt;/groupId&gt;--&gt;
    &lt;!--&lt;artifactId&gt;spring-cloud-stream-binder-kafka&lt;/artifactId&gt;--&gt;
&lt;!--&lt;/dependency&gt;--&gt;
&lt;dependency&gt;
    &lt;groupId&gt;org.springframework.cloud&lt;/groupId&gt;
    &lt;artifactId&gt;spring-cloud-starter-stream-kafka&lt;/artifactId&gt;
&lt;/dependency&gt;</pre>

<h3>整合代码（已经包含了使用逻辑）：</h3>

<pre>
import org.springframework.cloud.stream.annotation.Output;
import org.springframework.messaging.MessageChannel;

public interface KafkaChannel {
    /**
     * 发消息的通道名称
     */
    String CIM_OUTPUT = "cim_output";

    /**
     * 消息的订阅通道名称
     */
//    String CIM_INPUT = "cim_input";

    /**
     * 发消息的通道
     *
     * @return
     */
    @Output(CIM_OUTPUT)
    MessageChannel sendCIMMessage();

    /**
     * 收消息的通道
     *
     * @return
     */
//    @Input(CIM_INPUT)
//    SubscribableChannel recieveCIMMessage();
}</pre>

<pre>
@EnableBinding(value = Sink.class)
public class MessageKafkaCimHandler {
   
    @Autowired
    private MessageKafkaOutHandler messageKafkaOutHandler;
    

    private static final String CHARSET = "UTF-8";
    @StreamListener(Sink.INPUT)
    public void doProcess(Message&lt;byte[]&gt; message){           messageKafkaOutHandler.doProcess(MessageBuilder.withPayload(kafkaData).build());
}
}</pre>

<pre>
import cn.enncloud.iot.iotmqtttransferkafkahps.configuration.KafkaChannel;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.cloud.stream.annotation.EnableBinding;
import org.springframework.messaging.Message;
import org.springframework.messaging.MessageChannel;
import org.springframework.stereotype.Component;

@Component
@Slf4j
@EnableBinding(value = KafkaChannel.class)
public class MessageKafkaOutHandler {
    @Qualifier(value = KafkaChannel.CIM_OUTPUT)
    @Autowired
    private MessageChannel cimOutput;
    public boolean doProcess(Message&lt;?&gt; message) throws Exception{
        return this.cimOutput.send(message);
    }
}</pre>

<h3>yml配置文件：</h3>

<pre>
spring:
  cloud:
    stream:
      default-binder: kafka
      bindings:
        #cim_output自定义配置的，上看代码中的接口KafkaChannel .output,input不需要配置接口，默认提供，直接使用即可。
        cim_output:
          destination: topic1
          content-type: application/json
          binder: kafka
          producer:
            headerMode: raw
        output:
          destination: topic2
          content-type: application/json
          binder: kafka
          producer:
            headerMode: raw
        input:
          destination: topic2
          content-type: application/json
          binder: kafka
          group: hpsgroup
          consumer:
            headerMode: raw
      kafka:
        binder:
          brokers: *.*.*.1:9092,1.*.*.*:9092,1.*.*.*:9092
          zk-nodes: *.*.*.1:2181,1.*.*.*:2181,1.*.*.*:2181
  kafka:
    producer:
      client-id: mqtttransferkafka</pre>

<h2>最后提醒：</h2>

<p>复制要注意把配置复制全了，都是血泪调试出来的必需品，少了可能就要开始汤坑了。</p>            </div>
                </div>