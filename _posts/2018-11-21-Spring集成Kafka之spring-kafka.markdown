---
layout:     post
title:      Spring集成Kafka之spring-kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>配置文件的方式实现spring整和kafka:</p><p>    此文主要讲述的内容:<br></p><p>     1,连接kafka服务器的配置</p><p>    2,kafka-customer:消费者配置<br></p><p>    3,kafka-provider:提供者配置<br></p><p>    4,KfkaUtils:根据topic发送消息<br></p><p>    5,消费者根据topic处理消息<br></p><p>一，安装kafka<br></p><p>    <span style="font-size:14px;background-color:rgb(255,255,255);">  网上百度一下,一大堆,在这里我就不赘述了（新版本的kafka集成了zookeeper插件，所以只需配置kafka安装包下的zookeeper.properties即可,）,下载地址：</span><a href="http://kafka.apache.org/downloads" rel="nofollow" style="font-size:14px;background-color:rgb(255,255,255);">http://kafka.apache.org/downloads</a><br></p><p>注意在启动kafka-service之前需启动zookeeper插件</p><p><img src="https://img-blog.csdn.net/20180608170613643?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2tlaG9uZ2hn/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></p><p>二，配置maven仓库</p><p>   由于是基于spring集成的kafka的方式 ，所以在添加spring依赖的同时，还需   </p><pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:Consolas;font-size:11.3pt;"><span style="color:#e8bf6a;">&lt;dependency&gt;
</span><span style="color:#e8bf6a;">  &lt;groupId&gt;</span>org.springframework.kafka<span style="color:#e8bf6a;">&lt;/groupId&gt;
</span><span style="color:#e8bf6a;">  &lt;artifactId&gt;</span>spring-kafka<span style="color:#e8bf6a;">&lt;/artifactId&gt;
</span><span style="color:#e8bf6a;">  &lt;version&gt;</span>2.1.4.RELEASE<span style="color:#e8bf6a;">&lt;/version&gt;
</span><span style="color:#e8bf6a;">&lt;/dependency&gt;
</span><span style="color:#e8bf6a;">&lt;dependency&gt;
</span><span style="color:#e8bf6a;">  &lt;groupId&gt;</span>org.apache.kafka<span style="color:#e8bf6a;">&lt;/groupId&gt;
</span><span style="color:#e8bf6a;">  &lt;artifactId&gt;</span>kafka-clients<span style="color:#e8bf6a;">&lt;/artifactId&gt;
</span><span style="color:#e8bf6a;">  &lt;version&gt;</span>1.0.0<span style="color:#e8bf6a;">&lt;/version&gt;
</span><span style="color:#e8bf6a;">&lt;/dependency&gt;</span></pre><p>三，kafka服务器配置</p><p></p><pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:Consolas;font-size:11.3pt;"><span style="color:#808080;">#brokers</span><span style="color:#808080;font-family:'宋体';">集群
</span><span style="color:#cc7832;">bootstrap.servers</span><span style="color:#808080;">=</span><span style="color:#6a8759;">192.168.11.38:9092,192.168.11.40:9092
</span><span style="color:#808080;">#</span><span style="color:#808080;font-family:'宋体';">即所有副本都同步到数据时</span><span style="color:#808080;">send</span><span style="color:#808080;font-family:'宋体';">方法才返回</span><span style="color:#808080;">, </span><span style="color:#808080;font-family:'宋体';">以此来完全判断数据是否发送成功</span><span style="color:#808080;">, </span><span style="color:#808080;font-family:'宋体';">理论上来讲数据不会丢失</span><span style="color:#808080;">.
</span><span style="color:#cc7832;">acks</span><span style="color:#808080;">=</span><span style="color:#6a8759;">all
</span><span style="color:#808080;">#</span><span style="color:#808080;font-family:'宋体';">发送失败重试次数
</span><span style="color:#cc7832;">retries</span><span style="color:#808080;">=</span><span style="color:#6a8759;">10
</span><span style="color:#808080;">#</span><span style="color:#808080;font-family:'宋体';">批处理条数：当多个记录被发送到同一个分区时，生产者会尝试将记录合并到更少的请求中。这有助于客户端和服务器的性能。
</span><span style="color:#cc7832;">batch.size</span><span style="color:#808080;">=</span><span style="color:#6a8759;">1638
</span><span style="color:#808080;">#</span><span style="color:#808080;font-family:'宋体';">批处理延迟时间上限：即</span><span style="color:#808080;">1ms</span><span style="color:#808080;font-family:'宋体';">过后，不管是否达到批处理数，都直接发送一次请求
</span><span style="color:#cc7832;">linger.ms</span><span style="color:#808080;">=</span><span style="color:#6a8759;">1
</span><span style="color:#808080;">#</span><span style="color:#808080;font-family:'宋体';">即</span><span style="color:#808080;">32MB</span><span style="color:#808080;font-family:'宋体';">的批处理缓冲区
</span><span style="color:#cc7832;">buffer.memory</span><span style="color:#808080;">=</span><span style="color:#6a8759;">33554432
</span><span style="color:#808080;">#</span><span style="color:#808080;font-family:'宋体';">消费者群组</span><span style="color:#808080;">ID</span><span style="color:#808080;font-family:'宋体';">，发布</span><span style="color:#808080;">-</span><span style="color:#808080;font-family:'宋体';">订阅模式，即如果一个生产者，多个消费者都要消费，那么需要定义自己的群组，同一群组内的消费者只有一个能消费到消息
</span><span style="color:#cc7832;">group.id</span><span style="color:#808080;">=</span><span style="color:#6a8759;">order-beta
</span><span style="color:#808080;">#</span><span style="color:#808080;font-family:'宋体';">如果为</span><span style="color:#808080;">true</span><span style="color:#808080;font-family:'宋体';">，消费者的偏移量将在后台定期提交。
</span><span style="color:#cc7832;">enable.auto.commit</span><span style="color:#808080;">=</span><span style="color:#6a8759;">true
</span><span style="color:#808080;">#</span><span style="color:#808080;font-family:'宋体';">如何设置为自动提交（</span><span style="color:#808080;">enable.auto.commit=true</span><span style="color:#808080;font-family:'宋体';">），这里设置自动提交周期
</span><span style="color:#cc7832;">auto.commit.interval.ms</span><span style="color:#808080;">=</span><span style="color:#6a8759;">1000
</span><span style="color:#808080;">#</span><span style="color:#808080;font-family:'宋体';">在使用</span><span style="color:#808080;">Kafka</span><span style="color:#808080;font-family:'宋体';">的组管理时，用于检测消费者故障的超时
</span><span style="color:#cc7832;">session.timeout.ms</span><span style="color:#808080;">=</span><span style="color:#6a8759;">15000
</span><span style="color:#808080;">#</span><span style="color:#808080;font-family:'宋体';">消费监听器容器并发数
</span><span style="color:#cc7832;">concurrency </span><span style="color:#808080;">= </span><span style="color:#6a8759;">3
</span></pre><p>四，customer配置<br></p><p></p><pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:Consolas;font-size:11.3pt;"><span style="color:#e8bf6a;">&lt;?</span><span style="color:#bababa;">xml version</span><span style="color:#6a8759;">="1.0" </span><span style="color:#bababa;">encoding</span><span style="color:#6a8759;">="UTF-8"</span><span style="color:#e8bf6a;">?&gt;
</span><span style="color:#e8bf6a;">&lt;beans </span><span style="color:#bababa;">xmlns</span><span style="color:#6a8759;">="http://www.springframework.org/schema/beans"
</span><span style="color:#6a8759;">       </span><span style="color:#bababa;">xmlns:</span><span style="color:#9876aa;">xsi</span><span style="color:#6a8759;">="http://www.w3.org/2001/XMLSchema-instance" </span><span style="color:#bababa;">xmlns:</span><span style="color:#9876aa;">context</span><span style="color:#6a8759;">="http://www.springframework.org/schema/context"
</span><span style="color:#6a8759;">       </span><span style="color:#9876aa;">xsi</span><span style="color:#bababa;">:schemaLocation</span><span style="color:#6a8759;">="http://www.springframework.org/schema/beans
</span><span style="color:#6a8759;">         http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
</span><span style="color:#6a8759;">         http://www.springframework.org/schema/context
</span><span style="color:#6a8759;">         http://www.springframework.org/schema/context/spring-context.xsd"</span><span style="color:#e8bf6a;">&gt;
</span><span style="color:#e8bf6a;">    &lt;</span><span style="color:#9876aa;">context</span><span style="color:#e8bf6a;">:property-placeholder </span><span style="color:#bababa;">location</span><span style="color:#6a8759;">="classpath*:kafka.properties " </span><span style="color:#e8bf6a;">/&gt;
</span><span style="color:#e8bf6a;">    </span><span style="color:#808080;">&lt;!-- 1.</span><span style="color:#808080;font-family:'宋体';">定义</span><span style="color:#808080;">consumer</span><span style="color:#808080;font-family:'宋体';">的参数 </span><span style="color:#808080;">--&gt;
</span><span style="color:#808080;">    </span><span style="color:#e8bf6a;">&lt;bean </span><span style="color:#bababa;">id</span><span style="color:#6a8759;">="consumerProperties" </span><span style="color:#bababa;">class</span><span style="color:#6a8759;">="java.util.HashMap"</span><span style="color:#e8bf6a;">&gt;
</span><span style="color:#e8bf6a;">        &lt;constructor-arg&gt;
</span><span style="color:#e8bf6a;">            &lt;map&gt;
</span><span style="color:#e8bf6a;">                &lt;entry </span><span style="color:#bababa;">key</span><span style="color:#6a8759;">="bootstrap.servers" </span><span style="color:#bababa;">value</span><span style="color:#6a8759;">="${bootstrap.servers}" </span><span style="color:#e8bf6a;">/&gt;
</span><span style="color:#e8bf6a;">                &lt;entry </span><span style="color:#bababa;">key</span><span style="color:#6a8759;">="group.id" </span><span style="color:#bababa;">value</span><span style="color:#6a8759;">="${group.id}" </span><span style="color:#e8bf6a;">/&gt;
</span><span style="color:#e8bf6a;">                &lt;entry </span><span style="color:#bababa;">key</span><span style="color:#6a8759;">="enable.auto.commit" </span><span style="color:#bababa;">value</span><span style="color:#6a8759;">="${enable.auto.commit}" </span><span style="color:#e8bf6a;">/&gt;
</span><span style="color:#e8bf6a;">                &lt;entry </span><span style="color:#bababa;">key</span><span style="color:#6a8759;">="session.timeout.ms" </span><span style="color:#bababa;">value</span><span style="color:#6a8759;">="${session.timeout.ms}" </span><span style="color:#e8bf6a;">/&gt;
</span><span style="color:#e8bf6a;">                &lt;entry </span><span style="color:#bababa;">key</span><span style="color:#6a8759;">="key.deserializer"
</span><span style="color:#6a8759;">                       </span><span style="color:#bababa;">value</span><span style="color:#6a8759;">="org.apache.kafka.common.serialization.StringDeserializer" </span><span style="color:#e8bf6a;">/&gt;
</span><span style="color:#e8bf6a;">                &lt;entry </span><span style="color:#bababa;">key</span><span style="color:#6a8759;">="value.deserializer"
</span><span style="color:#6a8759;">                       </span><span style="color:#bababa;">value</span><span style="color:#6a8759;">="org.apache.kafka.common.serialization.StringDeserializer" </span><span style="color:#e8bf6a;">/&gt;
</span><span style="color:#e8bf6a;">            &lt;/map&gt;
</span><span style="color:#e8bf6a;">        &lt;/constructor-arg&gt;
</span><span style="color:#e8bf6a;">    &lt;/bean&gt;
</span><span style="color:#e8bf6a;">
</span><span style="color:#e8bf6a;">    </span><span style="color:#808080;">&lt;!-- 2.</span><span style="color:#808080;font-family:'宋体';">创建</span><span style="color:#808080;">consumerFactory bean --&gt;
</span><span style="color:#808080;">    </span><span style="color:#e8bf6a;">&lt;bean </span><span style="color:#bababa;">id</span><span style="color:#6a8759;">="consumerFactory"
</span><span style="color:#6a8759;">          </span><span style="color:#bababa;">class</span><span style="color:#6a8759;">="org.springframework.kafka.core.DefaultKafkaConsumerFactory" </span><span style="color:#e8bf6a;">&gt;
</span><span style="color:#e8bf6a;">        &lt;constructor-arg&gt;
</span><span style="color:#e8bf6a;">            &lt;ref </span><span style="color:#bababa;">bean</span><span style="color:#6a8759;">="consumerProperties" </span><span style="color:#e8bf6a;">/&gt;
</span><span style="color:#e8bf6a;">        &lt;/constructor-arg&gt;
</span><span style="color:#e8bf6a;">    &lt;/bean&gt;
</span><span style="color:#e8bf6a;">
</span><span style="color:#e8bf6a;">    </span><span style="color:#808080;">&lt;!-- 3.</span><span style="color:#808080;font-family:'宋体';">定义消费实现类 </span><span style="color:#808080;">--&gt;
</span><span style="color:#808080;">    </span><span style="color:#e8bf6a;">&lt;bean </span><span style="color:#bababa;">id</span><span style="color:#6a8759;">="kafkaConsumerService" </span><span style="color:#bababa;">class</span><span style="color:#6a8759;">="KafkaSendMessageServiceImpl" </span><span style="color:#e8bf6a;">/&gt;
</span><span style="color:#e8bf6a;">
</span><span style="color:#e8bf6a;">    </span><span style="color:#808080;">&lt;!-- 4.</span><span style="color:#808080;font-family:'宋体';">消费者容器配置信息 </span><span style="color:#808080;">--&gt;
</span><span style="color:#808080;">    </span><span style="color:#e8bf6a;">&lt;bean </span><span style="color:#bababa;">id</span><span style="color:#6a8759;">="containerProperties" </span><span style="color:#bababa;">class</span><span style="color:#6a8759;">="org.springframework.kafka.listener.config.ContainerProperties"</span><span style="color:#e8bf6a;">&gt;
</span><span style="color:#e8bf6a;">        </span><span style="color:#808080;">&lt;!-- topic --&gt;
</span><span style="color:#808080;">        </span><span style="color:#e8bf6a;">&lt;constructor-arg </span><span style="color:#bababa;">name</span><span style="color:#6a8759;">="topics"</span><span style="color:#e8bf6a;">&gt;
</span><span style="color:#e8bf6a;">            &lt;list&gt;
</span><span style="color:#e8bf6a;">                &lt;value&gt;</span>topic1<span style="color:#e8bf6a;">&lt;/value&gt;
</span><span style="color:#e8bf6a;">                &lt;value&gt;</span>topic2<span style="color:#e8bf6a;">&lt;/value&gt;
</span><span style="color:#e8bf6a;">               </span><span style="color:#808080;">&lt;!-- &lt;value&gt;${templar.agreement.feedback.topic}&lt;/value&gt;
</span><span style="color:#808080;">                &lt;value&gt;${templar.aggrement.active.feedback.topic}&lt;/value&gt;
</span><span style="color:#808080;">                &lt;value&gt;${templar.aggrement.agreementRepaid.topic}&lt;/value&gt;
</span><span style="color:#808080;">                &lt;value&gt;${templar.aggrement.agreementWithhold.topic}&lt;/value&gt;
</span><span style="color:#808080;">                &lt;value&gt;${templar.aggrement.agreementRepayRemind.topic}&lt;/value&gt;--&gt;
</span><span style="color:#808080;">            </span><span style="color:#e8bf6a;">&lt;/list&gt;
</span><span style="color:#e8bf6a;">        &lt;/constructor-arg&gt;
</span><span style="color:#e8bf6a;">        &lt;property </span><span style="color:#bababa;">name</span><span style="color:#6a8759;">="messageListener" </span><span style="color:#bababa;">ref</span><span style="color:#6a8759;">="kafkaConsumerService" </span><span style="color:#e8bf6a;">/&gt;
</span><span style="color:#e8bf6a;">    &lt;/bean&gt;
</span><span style="color:#e8bf6a;">    </span><span style="color:#808080;">&lt;!-- 5.</span><span style="color:#808080;font-family:'宋体';">消费者并发消息监听容器，执行</span><span style="color:#808080;">doStart()</span><span style="color:#808080;font-family:'宋体';">方法 </span><span style="color:#808080;">--&gt;
</span><span style="color:#808080;">    </span><span style="color:#e8bf6a;">&lt;bean </span><span style="color:#bababa;">id</span><span style="color:#6a8759;">="messageListenerContainer" </span><span style="color:#bababa;">class</span><span style="color:#6a8759;">="org.springframework.kafka.listener.ConcurrentMessageListenerContainer" </span><span style="color:#bababa;">init-method</span><span style="color:#6a8759;">="doStart" </span><span style="color:#e8bf6a;">&gt;
</span><span style="color:#e8bf6a;">        &lt;constructor-arg </span><span style="color:#bababa;">ref</span><span style="color:#6a8759;">="consumerFactory" </span><span style="color:#e8bf6a;">/&gt;
</span><span style="color:#e8bf6a;">        &lt;constructor-arg </span><span style="color:#bababa;">ref</span><span style="color:#6a8759;">="containerProperties" </span><span style="color:#e8bf6a;">/&gt;
</span><span style="color:#e8bf6a;">        &lt;property </span><span style="color:#bababa;">name</span><span style="color:#6a8759;">="concurrency" </span><span style="color:#bababa;">value</span><span style="color:#6a8759;">="${concurrency}" </span><span style="color:#e8bf6a;">/&gt;
</span><span style="color:#e8bf6a;">    &lt;/bean&gt;
</span><span style="color:#e8bf6a;">&lt;/beans&gt;</span>    </pre><p>五，provider配置<br></p><p></p><pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:Consolas;font-size:11.3pt;"><span style="color:#e8bf6a;">&lt;?</span><span style="color:#bababa;">xml version</span><span style="color:#6a8759;">="1.0" </span><span style="color:#bababa;">encoding</span><span style="color:#6a8759;">="UTF-8"</span><span style="color:#e8bf6a;">?&gt;
</span><span style="color:#e8bf6a;">&lt;beans </span><span style="color:#bababa;">xmlns</span><span style="color:#6a8759;">="http://www.springframework.org/schema/beans"
</span><span style="color:#6a8759;">       </span><span style="color:#bababa;">xmlns:</span><span style="color:#9876aa;">xsi</span><span style="color:#6a8759;">="http://www.w3.org/2001/XMLSchema-instance" </span><span style="color:#bababa;">xmlns:</span><span style="color:#9876aa;">context</span><span style="color:#6a8759;">="http://www.springframework.org/schema/context"
</span><span style="color:#6a8759;">       </span><span style="color:#9876aa;">xsi</span><span style="color:#bababa;">:schemaLocation</span><span style="color:#6a8759;">="http://www.springframework.org/schema/beans
</span><span style="color:#6a8759;">         http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
</span><span style="color:#6a8759;">         http://www.springframework.org/schema/context
</span><span style="color:#6a8759;">         http://www.springframework.org/schema/context/spring-context.xsd"</span><span style="color:#e8bf6a;">&gt;
</span><span style="color:#e8bf6a;">    &lt;</span><span style="color:#9876aa;">context</span><span style="color:#e8bf6a;">:property-placeholder </span><span style="color:#bababa;">location</span><span style="color:#6a8759;">="classpath*:kafka.properties " </span><span style="color:#e8bf6a;">/&gt;
</span><span style="color:#e8bf6a;">    </span><span style="color:#808080;">&lt;!-- </span><span style="color:#808080;font-family:'宋体';">定义</span><span style="color:#808080;">producer</span><span style="color:#808080;font-family:'宋体';">的参数 </span><span style="color:#808080;">--&gt;
</span><span style="color:#808080;">    </span><span style="color:#e8bf6a;">&lt;bean </span><span style="color:#bababa;">id</span><span style="color:#6a8759;">="producerProperties" </span><span style="color:#bababa;">class</span><span style="color:#6a8759;">="java.util.HashMap"</span><span style="color:#e8bf6a;">&gt;
</span><span style="color:#e8bf6a;">        &lt;constructor-arg&gt;
</span><span style="color:#e8bf6a;">            &lt;map&gt;
</span><span style="color:#e8bf6a;">                &lt;entry </span><span style="color:#bababa;">key</span><span style="color:#6a8759;">="bootstrap.servers" </span><span style="color:#bababa;">value</span><span style="color:#6a8759;">="${bootstrap.servers}" </span><span style="color:#e8bf6a;">/&gt;
</span><span style="color:#e8bf6a;">                &lt;entry </span><span style="color:#bababa;">key</span><span style="color:#6a8759;">="group.id" </span><span style="color:#bababa;">value</span><span style="color:#6a8759;">="${group.id}" </span><span style="color:#e8bf6a;">/&gt;
</span><span style="color:#e8bf6a;">                &lt;entry </span><span style="color:#bababa;">key</span><span style="color:#6a8759;">="retries" </span><span style="color:#bababa;">value</span><span style="color:#6a8759;">="${retries}" </span><span style="color:#e8bf6a;">/&gt;
</span><span style="color:#e8bf6a;">                &lt;entry </span><span style="color:#bababa;">key</span><span style="color:#6a8759;">="batch.size" </span><span style="color:#bababa;">value</span><span style="color:#6a8759;">="${batch.size}" </span><span style="color:#e8bf6a;">/&gt;
</span><span style="color:#e8bf6a;">                &lt;entry </span><span style="color:#bababa;">key</span><span style="color:#6a8759;">="linger.ms" </span><span style="color:#bababa;">value</span><span style="color:#6a8759;">="${linger.ms}" </span><span style="color:#e8bf6a;">/&gt;
</span><span style="color:#e8bf6a;">                &lt;entry </span><span style="color:#bababa;">key</span><span style="color:#6a8759;">="buffer.memory" </span><span style="color:#bababa;">value</span><span style="color:#6a8759;">="${buffer.memory}" </span><span style="color:#e8bf6a;">/&gt;
</span><span style="color:#e8bf6a;">                &lt;entry </span><span style="color:#bababa;">key</span><span style="color:#6a8759;">="acks" </span><span style="color:#bababa;">value</span><span style="color:#6a8759;">="${acks}" </span><span style="color:#e8bf6a;">/&gt;
</span><span style="color:#e8bf6a;">                &lt;entry </span><span style="color:#bababa;">key</span><span style="color:#6a8759;">="key.serializer"
</span><span style="color:#6a8759;">                       </span><span style="color:#bababa;">value</span><span style="color:#6a8759;">="org.apache.kafka.common.serialization.StringSerializer" </span><span style="color:#e8bf6a;">/&gt;
</span><span style="color:#e8bf6a;">                &lt;entry </span><span style="color:#bababa;">key</span><span style="color:#6a8759;">="value.serializer"
</span><span style="color:#6a8759;">                       </span><span style="color:#bababa;">value</span><span style="color:#6a8759;">="org.apache.kafka.common.serialization.StringSerializer" </span><span style="color:#e8bf6a;">/&gt;
</span><span style="color:#e8bf6a;">            &lt;/map&gt;
</span><span style="color:#e8bf6a;">        &lt;/constructor-arg&gt;
</span><span style="color:#e8bf6a;">    &lt;/bean&gt;
</span><span style="color:#e8bf6a;">
</span><span style="color:#e8bf6a;">    </span><span style="color:#808080;">&lt;!-- </span><span style="color:#808080;font-family:'宋体';">创建</span><span style="color:#808080;">kafkatemplate</span><span style="color:#808080;font-family:'宋体';">需要使用的</span><span style="color:#808080;">producerfactory bean --&gt;
</span><span style="color:#808080;">    </span><span style="color:#e8bf6a;">&lt;bean </span><span style="color:#bababa;">id</span><span style="color:#6a8759;">="producerFactory"
</span><span style="color:#6a8759;">          </span><span style="color:#bababa;">class</span><span style="color:#6a8759;">="org.springframework.kafka.core.DefaultKafkaProducerFactory"</span><span style="color:#e8bf6a;">&gt;
</span><span style="color:#e8bf6a;">        &lt;constructor-arg&gt;
</span><span style="color:#e8bf6a;">            &lt;ref </span><span style="color:#bababa;">bean</span><span style="color:#6a8759;">="producerProperties" </span><span style="color:#e8bf6a;">/&gt;
</span><span style="color:#e8bf6a;">        &lt;/constructor-arg&gt;
</span><span style="color:#e8bf6a;">    &lt;/bean&gt;
</span><span style="color:#e8bf6a;">
</span><span style="color:#e8bf6a;">    </span><span style="color:#808080;">&lt;!-- </span><span style="color:#808080;font-family:'宋体';">创建</span><span style="color:#808080;">kafkatemplate bean</span><span style="color:#808080;font-family:'宋体';">，使用的时候，只需要注入这个</span><span style="color:#808080;">bean</span><span style="color:#808080;font-family:'宋体';">，即可使用</span><span style="color:#808080;">template</span><span style="color:#808080;font-family:'宋体';">的</span><span style="color:#808080;">send</span><span style="color:#808080;font-family:'宋体';">消息方法 </span><span style="color:#808080;">--&gt;
</span><span style="color:#808080;">    </span><span style="color:#e8bf6a;">&lt;bean </span><span style="color:#bababa;">id</span><span style="color:#6a8759;">="kafkaTemplate" </span><span style="color:#bababa;">class</span><span style="color:#6a8759;">="org.springframework.kafka.core.KafkaTemplate"</span><span style="color:#e8bf6a;">&gt;
</span><span style="color:#e8bf6a;">        &lt;constructor-arg </span><span style="color:#bababa;">ref</span><span style="color:#6a8759;">="producerFactory" </span><span style="color:#e8bf6a;">/&gt;
</span><span style="color:#e8bf6a;">        &lt;constructor-arg </span><span style="color:#bababa;">name</span><span style="color:#6a8759;">="autoFlush" </span><span style="color:#bababa;">value</span><span style="color:#6a8759;">="true" </span><span style="color:#e8bf6a;">/&gt;
</span><span style="color:#e8bf6a;">        &lt;property </span><span style="color:#bababa;">name</span><span style="color:#6a8759;">="defaultTopic" </span><span style="color:#bababa;">value</span><span style="color:#6a8759;">="default" </span><span style="color:#e8bf6a;">/&gt;
</span><span style="color:#e8bf6a;">    &lt;/bean&gt;
</span><span style="color:#e8bf6a;">&lt;/beans&gt;</span></pre><p>六，简单的KafkaUtils，发送kafka主题消息<br></p><p></p><pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:Consolas;font-size:11.3pt;"><span style="color:#cc7832;">import </span>org.springframework.beans.factory.annotation.<span style="color:#bbb529;">Autowired</span><span style="color:#cc7832;">;
</span><span style="color:#cc7832;">import </span>org.springframework.context.support.ClassPathXmlApplicationContext<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">import </span>org.springframework.kafka.core.KafkaTemplate<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">import </span>org.springframework.kafka.support.SendResult<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">import </span>org.springframework.util.concurrent.FailureCallback<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">import </span>org.springframework.util.concurrent.ListenableFuture<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">import </span>org.springframework.util.concurrent.SuccessCallback<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">public class </span>KafkaSendMsgUtils {
    <span style="color:#cc7832;">public static final  </span>ClassPathXmlApplicationContext <span style="color:#9876aa;"><em>CONTEXT </em></span>= <span style="color:#cc7832;">new </span>ClassPathXmlApplicationContext(<span style="color:#6a8759;">"/kafka-provider.xml"</span>)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">    public static </span>&lt;<span style="color:#507874;">K</span><span style="color:#cc7832;">,</span><span style="color:#507874;">T</span>&gt;<span style="color:#cc7832;">void </span><span style="color:#ffc66d;">sendMessage</span>(String topic<span style="color:#cc7832;">, </span>Integer partition<span style="color:#cc7832;">, </span>Long timestamp<span style="color:#cc7832;">,  </span><span style="color:#507874;">K </span>key<span style="color:#cc7832;">, </span><span style="color:#507874;">T </span>data) {
        KafkaTemplate&lt;<span style="color:#507874;">K</span><span style="color:#cc7832;">, </span><span style="color:#507874;">T</span>&gt; kafkaTemplate = (KafkaTemplate&lt;<span style="color:#507874;">K</span><span style="color:#cc7832;">, </span><span style="color:#507874;">T</span>&gt;) <span style="color:#9876aa;"><em>CONTEXT</em></span>.getBean(<span style="color:#6a8759;">"kafkaTemplate"</span>)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">        </span>ListenableFuture&lt;SendResult&lt;<span style="color:#507874;">K</span><span style="color:#cc7832;">, </span><span style="color:#507874;">T</span>&gt;&gt; listenableFuture = <span style="color:#cc7832;">null;
</span><span style="color:#cc7832;">        if </span>(kafkaTemplate.getDefaultTopic().equals(topic)) {
            listenableFuture =  kafkaTemplate.sendDefault(partition<span style="color:#cc7832;">,</span>timestamp<span style="color:#cc7832;">,</span>key<span style="color:#cc7832;">,</span>data)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">        </span>}<span style="color:#cc7832;">else </span>{
            listenableFuture = kafkaTemplate.send(topic<span style="color:#cc7832;">, </span>partition<span style="color:#cc7832;">, </span>timestamp<span style="color:#cc7832;">, </span>key<span style="color:#cc7832;">, </span>data)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">        </span>}
        <span style="color:#808080;">//</span><span style="color:#808080;font-family:'宋体';">发送成功回调
</span><span style="color:#808080;font-family:'宋体';">        </span>SuccessCallback&lt;SendResult&lt;<span style="color:#507874;">K</span><span style="color:#cc7832;">, </span><span style="color:#507874;">T</span>&gt;&gt; successCallback = <span style="color:#cc7832;">new </span>SuccessCallback&lt;SendResult&lt;<span style="color:#507874;">K</span><span style="color:#cc7832;">, </span><span style="color:#507874;">T</span>&gt;&gt;() {
            <span style="color:#bbb529;">@Override
</span><span style="color:#bbb529;">            </span><span style="color:#cc7832;">public void </span><span style="color:#ffc66d;">onSuccess</span>(SendResult&lt;<span style="color:#507874;">K</span><span style="color:#cc7832;">, </span><span style="color:#507874;">T</span>&gt; result) {
                <span style="color:#808080;">//</span><span style="color:#808080;font-family:'宋体';">成功业务逻辑
</span><span style="color:#808080;font-family:'宋体';">                </span>System.<span style="color:#9876aa;"><em>out</em></span>.println(<span style="color:#6a8759;">"</span><span style="color:#6a8759;font-family:'宋体';">成功</span><span style="color:#6a8759;">"</span>)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">            </span>}
        }<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">        </span><span style="color:#808080;">//</span><span style="color:#808080;font-family:'宋体';">发送失败回调
</span><span style="color:#808080;font-family:'宋体';">        </span>FailureCallback failureCallback = <span style="color:#cc7832;">new </span>FailureCallback() {
            <span style="color:#bbb529;">@Override
</span><span style="color:#bbb529;">            </span><span style="color:#cc7832;">public void </span><span style="color:#ffc66d;">onFailure</span>(Throwable ex) {
                <span style="color:#808080;">//</span><span style="color:#808080;font-family:'宋体';">失败业务逻辑
</span><span style="color:#808080;font-family:'宋体';">                </span><span style="color:#cc7832;">throw new </span>RuntimeException(ex)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">            </span>}
        }<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">        </span>listenableFuture.addCallback(successCallback<span style="color:#cc7832;">, </span>failureCallback)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">    </span>}

    <span style="color:#cc7832;">public static void </span><span style="color:#ffc66d;">main</span>(String[] args) {
        <span style="color:#cc7832;">for </span>(<span style="color:#cc7832;">int </span>i = <span style="color:#6897bb;">0</span><span style="color:#cc7832;">; </span>i &lt; <span style="color:#6897bb;">5</span><span style="color:#cc7832;">; </span>i++) {
            <span style="color:#cc7832;">try </span>{
                Thread.<span style="font-style:italic;">sleep</span>(<span style="color:#6897bb;">10000</span>)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">            </span>} <span style="color:#cc7832;">catch </span>(InterruptedException e) {
                e.printStackTrace()<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">            </span>}
            <span style="color:#808080;">//KafkaTemplate&lt;String, String&gt; kafkaTemplate = (KafkaTemplate&lt;String, String&gt;) CONTEXT.getBean("kafkaTemplate");
</span><span style="color:#808080;">            </span>KafkaSendMsgUtils.<span style="font-style:italic;">sendMessage</span>(<span style="color:#6a8759;">"topic1"</span><span style="color:#cc7832;">,</span><span style="color:#6897bb;">0</span><span style="color:#cc7832;">,null,</span><span style="color:#6a8759;">"key"</span><span style="color:#cc7832;">,</span><span style="color:#6a8759;">"kafka-test"</span>)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">        </span>}
    }
}
</pre><p>七,消费者接受消息<br></p><p></p><pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:Consolas;font-size:11.3pt;"><span style="color:#cc7832;">import </span>org.apache.kafka.clients.consumer.ConsumerRecord<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">import </span>org.springframework.kafka.listener.MessageListener<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">public class </span>KafkaSendMessageServiceImpl <span style="color:#cc7832;">implements </span>MessageListener&lt;String<span style="color:#cc7832;">,</span>String&gt; {

    <span style="color:#bbb529;">@Override
</span><span style="color:#bbb529;">    </span><span style="color:#cc7832;">public void </span><span style="color:#ffc66d;">onMessage</span>(ConsumerRecord&lt;String<span style="color:#cc7832;">, </span>String&gt; data) {
            <span style="color:#808080;">//</span><span style="color:#808080;font-family:'宋体';">根据不同主题，消费
</span><span style="color:#808080;font-family:'宋体';">            </span>System.<span style="color:#9876aa;"><em>out</em></span>.println(<span style="color:#6a8759;">"========"</span>)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">            if</span>(<span style="color:#6a8759;">"topic1"</span>.equals(data.topic())){
                <span style="color:#808080;">//</span><span style="color:#808080;font-family:'宋体';">逻辑</span><span style="color:#808080;">1
</span><span style="color:#808080;">                </span>System.<span style="color:#9876aa;"><em>out</em></span>.println(data.value()+<span style="color:#6a8759;">"</span><span style="color:#6a8759;font-family:'宋体';">被消费</span><span style="color:#6a8759;">"</span>)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">            </span>}<span style="color:#cc7832;">else if</span>(<span style="color:#6a8759;">"topic2"</span>.equals(data.topic())){
                <span style="color:#808080;">//</span><span style="color:#808080;font-family:'宋体';">逻辑</span><span style="color:#808080;">2
</span><span style="color:#808080;">                </span>System.<span style="color:#9876aa;"><em>out</em></span>.println(data.value()+<span style="color:#6a8759;">"</span><span style="color:#6a8759;font-family:'宋体';">主题</span><span style="color:#6a8759;">2 </span><span style="color:#6a8759;font-family:'宋体';">被消费</span><span style="color:#6a8759;">"</span>)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">            </span>}
        }
}
</pre>            </div>
                </div>