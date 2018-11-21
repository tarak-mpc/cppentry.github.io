---
layout:     post
title:      springboot 1.5.2 集成kafka 简单例子
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Springboot1.5.2以后和Kafka进行无缝集成</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<br></p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
添加依赖</p>
<div class="cnblogs_code" style="background-color:rgb(245,245,245);border:1px solid rgb(204,204,204);overflow:auto;font-family:'Courier New' !important;">
<pre style="font-family:'Courier New' !important;">compile("org.springframework.kafka:spring-kafka:1.1.2.RELEASE")</pre>
</div>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
添加application.properties</p>
<div class="cnblogs_code" style="background-color:rgb(245,245,245);border:1px solid rgb(204,204,204);overflow:auto;font-family:'Courier New' !important;">
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy" style="line-height:1.5 !important;"><a title="复制代码" style="border:none !important;"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none !important;"></a></span></div>
<pre style="font-family:'Courier New' !important;"><span style="line-height:1.5 !important;">#kafka
# 指定kafka 代理地址，可以多个
spring.kafka.bootstrap-servers=192.168.59.130:9092,192.168.59.131:9092,192.168.59.132:9092
# 指定默认消费者group id
spring.kafka.consumer.group-id=myGroup
# 指定默认topic id
spring.kafka.template.default-topic= my-replicated-topic
# 指定listener 容器中的线程数，用于提高并发量
spring.kafka.listener.concurrency= 3
# 每次批量发送消息的数量
spring.kafka.producer.batch-size= 1000</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy" style="line-height:1.5 !important;"><a title="复制代码" style="border:none !important;"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none !important;"></a></span></div>
</div>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
configuration 启用kafka</p>
<div class="cnblogs_code" style="background-color:rgb(245,245,245);border:1px solid rgb(204,204,204);overflow:auto;font-family:'Courier New' !important;">
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy" style="line-height:1.5 !important;"><a title="复制代码" style="border:none !important;"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none !important;"></a></span></div>
<pre style="font-family:'Courier New' !important;"><span style="color:rgb(0,0,255);line-height:1.5 !important;">package</span><span style="line-height:1.5 !important;"> cn.xiaojf.today.data.kafka.configuration;

</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.springframework.context.annotation.Configuration;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.springframework.kafka.annotation.EnableKafka;

</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">/**</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">
 * kafka 配置
 * </span><span style="color:rgb(128,128,128);line-height:1.5 !important;">@author</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> xiaojf 2017/3/24 14:09
 </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">*/</span><span style="line-height:1.5 !important;">
@Configuration
@EnableKafka
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">class</span><span style="line-height:1.5 !important;"> KafkaConfiguration {


}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy" style="line-height:1.5 !important;"><a title="复制代码" style="border:none !important;"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none !important;"></a></span></div>
</div>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
消息生产者</p>
<div class="cnblogs_code" style="background-color:rgb(245,245,245);border:1px solid rgb(204,204,204);overflow:auto;font-family:'Courier New' !important;">
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy" style="line-height:1.5 !important;"><a title="复制代码" style="border:none !important;"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none !important;"></a></span></div>
<pre style="font-family:'Courier New' !important;"><span style="color:rgb(0,0,255);line-height:1.5 !important;">package</span><span style="line-height:1.5 !important;"> cn.xiaojf.today.data.kafka.producer;

</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.kafka.clients.producer.Producer;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.kafka.clients.producer.RecordMetadata;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.springframework.beans.factory.annotation.Autowired;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.springframework.kafka.core.KafkaOperations;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.springframework.kafka.core.KafkaTemplate;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.springframework.kafka.support.ProducerListener;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.springframework.stereotype.Component;

</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">/**</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">
 * 消息生产者
 * </span><span style="color:rgb(128,128,128);line-height:1.5 !important;">@author</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> xiaojf 2017/3/24 14:36
 </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">*/</span><span style="line-height:1.5 !important;">
@Component
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">class</span><span style="line-height:1.5 !important;"> MsgProducer {
    @Autowired
    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">private</span> KafkaTemplate&lt;String,String&gt;<span style="line-height:1.5 !important;"> kafkaTemplate;

    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">void</span><span style="line-height:1.5 !important;"> send() {
        kafkaTemplate.send(</span>"my-replicated-topic","xiaojf"<span style="line-height:1.5 !important;">);
        kafkaTemplate.send(</span>"my-replicated-topic","xiaojf"<span style="line-height:1.5 !important;">);

        kafkaTemplate.metrics();

        kafkaTemplate.execute(</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> KafkaOperations.ProducerCallback&lt;String, String, Object&gt;<span style="line-height:1.5 !important;">() {
            @Override
            </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> Object doInKafka(Producer&lt;String, String&gt;<span style="line-height:1.5 !important;"> producer) {
                </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">这里可以编写kafka原生的api操作</span>
                <span style="color:rgb(0,0,255);line-height:1.5 !important;">return</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">null</span><span style="line-height:1.5 !important;">;
            }
        });

        </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">消息发送的监听器，用于回调返回信息</span>
        kafkaTemplate.setProducerListener(<span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> ProducerListener&lt;String, String&gt;<span style="line-height:1.5 !important;">() {
            @Override
            </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">void</span><span style="line-height:1.5 !important;"> onSuccess(String topic, Integer partition, String key, String value, RecordMetadata recordMetadata) {

            }

            @Override
            </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">void</span><span style="line-height:1.5 !important;"> onError(String topic, Integer partition, String key, String value, Exception exception) {

            }

            @Override
            </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">boolean</span><span style="line-height:1.5 !important;"> isInterestedInSuccess() {
                </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">return</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">false</span><span style="line-height:1.5 !important;">;
            }
        });
    }
}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy" style="line-height:1.5 !important;"><a title="复制代码" style="border:none !important;"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none !important;"></a></span></div>
</div>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
消息消费者</p>
<div class="cnblogs_code" style="background-color:rgb(245,245,245);border:1px solid rgb(204,204,204);overflow:auto;font-family:'Courier New' !important;">
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy" style="line-height:1.5 !important;"><a title="复制代码" style="border:none !important;"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none !important;"></a></span></div>
<pre style="font-family:'Courier New' !important;"><span style="color:rgb(0,0,255);line-height:1.5 !important;">package</span><span style="line-height:1.5 !important;"> cn.xiaojf.today.data.kafka.consumer;

</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.springframework.kafka.annotation.KafkaListener;
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.springframework.stereotype.Component;

</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">/**</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">
 * 消息消费者
 * </span><span style="color:rgb(128,128,128);line-height:1.5 !important;">@author</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> xiaojf 2017/3/24 14:36
 </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">*/</span><span style="line-height:1.5 !important;">
@Component
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">class</span><span style="line-height:1.5 !important;"> MsgConsumer {
    @KafkaListener(topics </span>= {"my-replicated-topic","my-replicated-topic2"<span style="line-height:1.5 !important;">})
    </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">public</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">void</span><span style="line-height:1.5 !important;"> processMessage(String content) {
        System.out.println(content);
    }


}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy" style="line-height:1.5 !important;"><a title="复制代码" style="border:none !important;"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码" style="border:none !important;"></a></span></div>
</div>
            </div>
                </div>