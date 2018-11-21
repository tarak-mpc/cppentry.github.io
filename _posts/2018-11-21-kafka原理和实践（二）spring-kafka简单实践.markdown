---
layout:     post
title:      kafka原理和实践（二）spring-kafka简单实践
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <div class="blogpost-body"><a></a><div><p style="font-size:18px;"><b>目录</b></p><ul><li><a href="#_label0" rel="nofollow">一、生产者</a><ul><li><a href="#_label0_0" rel="nofollow">1.1.配置</a></li><li><a href="#_label0_1" rel="nofollow">1.2  发送kafka消息</a></li></ul></li><li><a href="#_label1" rel="nofollow">二、消费者</a><ul><li><a href="#_label1_0" rel="nofollow">2.1配置</a></li><li><a href="#_label1_1" rel="nofollow">2.2消费kafka消息</a></li></ul></li></ul></div><p> </p><p style="font-size:18px;"><b>正文</b></p><p>系列目录</p>
<p><a class="singleposttitle" href="http://www.cnblogs.com/dennyzhangdd/p/7759869.html" rel="nofollow">kafka原理和实践（一）原理：10分钟入门</a></p>
<p><a href="http://www.cnblogs.com/dennyzhangdd/p/7759875.html" rel="nofollow">kafka原理和实践（二）spring-kafka简单实践</a></p>
<p><a class="singleposttitle" href="http://www.cnblogs.com/dennyzhangdd/p/7827564.html" rel="nofollow">kafka原理和实践（三）spring-kafka生产者源码</a></p>
<p><a href="http://www.cnblogs.com/dennyzhangdd/p/7759876.html" rel="nofollow">kafka原理和实践（四）spring-kafka消费者源码</a></p>
<p><a class="singleposttitle" href="http://www.cnblogs.com/dennyzhangdd/p/7834143.html" rel="nofollow">kafka原理和实践（五）spring-kafka配置详解</a></p>
<p><a href="http://www.cnblogs.com/dennyzhangdd/p/7759878.html" rel="nofollow">kafka原理和实践（六）总结升华</a></p>
<p> </p>
<p> </p>
<p>=========正文分割线====================</p>
<p>作为一个MQ做基本的功能自然就是消息的生产和消费，本章以XML配置的方式实现消息的生产和消费。</p>
<div style="text-align:right;"><a href="#_labelTop" rel="nofollow">回到顶部</a><a></a></div><h2>一、生产者</h2>
<a></a><h3><span class="header">1.1.配置<br></span></h3>
<p><span class="header">spring-kafka 提供了org</span>.<span class="header">springframework</span>.<span class="header">kafka</span>.<span class="header">core</span>.KafkaTemplate</p>
<p>xml配置如下，<span style="color:#ff0000;"><strong><span style="color:#ff0000;"><span style="color:#ff0000;">producerProperties</span>中的具体配置暂时不用在意，后面有一章专门讲xml配置</span></strong></span>：</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color:#008080;"> 1</span> &lt;?xml version="1.0" encoding="UTF-8"?&gt;
<span style="color:#008080;"> 2</span> &lt;beans xmlns="http://www.springframework.org/schema/beans"
<span style="color:#008080;"> 3</span>     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
<span style="color:#008080;"> 4</span>     xsi:schemaLocation="http://www.springframework.org/schema/beans  
<span style="color:#008080;"> 5</span>          http:<span style="color:#008000;">//</span><span style="color:#008000;">www.springframework.org/schema/beans/spring-beans-3.0.xsd  </span>
<span style="color:#008080;"> 6</span>          http:<span style="color:#008000;">//</span><span style="color:#008000;">www.springframework.org/schema/context  </span>
<span style="color:#008080;"> 7</span>          http:<span style="color:#008000;">//</span><span style="color:#008000;">www.springframework.org/schema/context/spring-context.xsd"&gt;</span>
<span style="color:#008080;"> 8</span>     &lt;context:property-placeholder location="classpath*:config/application.properties" /&gt;
<span style="color:#008080;"> 9</span>     &lt;!-- 定义producer的参数 --&gt;
<span style="color:#008080;">10</span>     &lt;bean id="<span style="color:#ff0000;">producerProperties</span>" <span style="color:#0000ff;">class</span>="java.util.HashMap"&gt;
<span style="color:#008080;">11</span>         &lt;constructor-arg&gt;
<span style="color:#008080;">12</span>             &lt;map&gt;
<span style="color:#008080;">13</span>                 &lt;entry key="bootstrap.servers" value="${bootstrap.servers}" /&gt;
<span style="color:#008080;">14</span>                 &lt;entry key="group.id" value="${group.id}" /&gt;
<span style="color:#008080;">15</span>                 &lt;entry key="retries" value="${retries}" /&gt;
<span style="color:#008080;">16</span>                 &lt;entry key="batch.size" value="${batch.size}" /&gt;
<span style="color:#008080;">17</span>                 &lt;entry key="linger.ms" value="${linger.ms}" /&gt;
<span style="color:#008080;">18</span>                 &lt;entry key="buffer.memory" value="${buffer.memory}" /&gt;            
<span style="color:#008080;">20</span>                 &lt;entry key="acks" value="${acks}" /&gt;              
<span style="color:#008080;">22</span>                 &lt;entry key="key.serializer"
<span style="color:#008080;">23</span>                     value="org.apache.kafka.common.serialization.StringSerializer" /&gt;
<span style="color:#008080;">24</span>                 &lt;entry key="value.serializer"
<span style="color:#008080;">25</span>                     value="org.apache.kafka.common.serialization.StringSerializer" /&gt;
<span style="color:#008080;">26</span>             &lt;/map&gt;
<span style="color:#008080;">27</span>         &lt;/constructor-arg&gt;
<span style="color:#008080;">28</span>     &lt;/bean&gt;
<span style="color:#008080;">29</span> 
<span style="color:#008080;">30</span>     &lt;!-- 创建kafkatemplate需要使用的producerfactory bean --&gt;
<span style="color:#008080;">31</span>     &lt;bean id="<span style="color:#ff0000;">producerFactory</span>"
<span style="color:#008080;">32</span>         <span style="color:#0000ff;">class</span>="org.springframework.kafka.core.DefaultKafkaProducerFactory"&gt;
<span style="color:#008080;">33</span>         &lt;constructor-arg&gt;
<span style="color:#008080;">34</span>             &lt;ref bean="producerProperties" /&gt;
<span style="color:#008080;">35</span>         &lt;/constructor-arg&gt;
<span style="color:#008080;">36</span>     &lt;/bean&gt;
<span style="color:#008080;">37</span> 
<span style="color:#008080;">38</span>     &lt;!-- 创建kafkatemplate bean，使用的时候，只需要注入这个bean，即可使用template的send消息方法 --&gt;
<span style="color:#008080;">39</span>     &lt;bean id="<span style="color:#ff0000;">kafkaTemplate</span>" <span style="color:#0000ff;">class</span>="org.springframework.kafka.core.KafkaTemplate"&gt;
<span style="color:#008080;">40</span>         &lt;constructor-arg ref="producerFactory" /&gt;
<span style="color:#008080;">41</span>         &lt;constructor-arg name="autoFlush" value="true" /&gt;
<span style="color:#008080;">42</span>         &lt;property name="defaultTopic" value="default" /&gt;
<span style="color:#008080;">43</span>     &lt;/bean&gt;
<span style="color:#008080;">46</span> &lt;/beans&gt;  </pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>如上图，xml主要配置了KafkaTemplate的构造参数producerFactory和autoFlush，对应了一个KafkaTemplate源码中的2参构造函数。</p>
<p><strong><span style="color:#ff0000;">producerProperties：设置生产者公产需要的配置</span></strong><br><strong><span style="color:#ff0000;">producerFactory：定义了生产者工厂构造方法</span></strong><br><strong><span style="color:#ff0000;">kafkaTemplate：定义了使用producerFactory和是否自动刷新，2个参数来构造kafka生产者模板类。</span></strong></p>
<a></a><h3><span style="color:#000000;">1.2  发送kafka消息</span></h3>
<p><span style="color:#000000;">1.根据topic、partition、key发送数据data。</span></p>
<p><span style="color:#000000;">2.接收ListenableFuture添加成功、失败回调函数。</span></p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color:#008080;"> 1</span> ListenableFuture&lt;SendResult&lt;String, String&gt;&gt; listenableFuture = <span style="color:#ff0000;">kafkaTemplate</span>.<span style="color:#ff0000;">send</span>("topic", "partition","key","data"<span style="color:#000000;">);
</span><span style="color:#008080;"> 2</span> <span style="color:#008000;">//</span><span style="color:#008000;">发送成功回调</span>
<span style="color:#008080;"> 3</span> SuccessCallback&lt;SendResult&lt;String, String&gt;&gt; successCallback = <span style="color:#0000ff;">new</span> SuccessCallback&lt;SendResult&lt;String, String&gt;&gt;<span style="color:#000000;">() {
</span><span style="color:#008080;"> 4</span> <span style="color:#000000;">    @Override
</span><span style="color:#008080;"> 5</span>     <span style="color:#0000ff;">public</span> <span style="color:#0000ff;">void</span> onSuccess(SendResult&lt;String, String&gt;<span style="color:#000000;"> result) {
</span><span style="color:#008080;"> 6</span> <span style="color:#000000;">       <span style="color:#008000;">//成功业务逻辑</span>
</span><span style="color:#008080;"> 7</span> <span style="color:#000000;">    }
</span><span style="color:#008080;"> 8</span> <span style="color:#000000;">}
</span><span style="color:#008080;"> 9</span> <span style="color:#008000;">//</span><span style="color:#008000;">发送失败回调</span>
<span style="color:#008080;">10</span> FailureCallback failureCallback = <span style="color:#0000ff;">new</span><span style="color:#000000;"> FailureCallback() {
</span><span style="color:#008080;">11</span> <span style="color:#000000;">    @Override
</span><span style="color:#008080;">12</span>     <span style="color:#0000ff;">public</span> <span style="color:#0000ff;">void</span><span style="color:#000000;"> onFailure(Throwable ex) {
</span><span style="color:#008080;">13</span> <span style="color:#000000;">        <span style="color:#000000;"><span style="color:#008000;">//失败业务逻辑</span></span>
</span><span style="color:#008080;">14</span> <span style="color:#000000;">    }
</span><span style="color:#008080;">15</span> <span style="color:#000000;">}
</span><span style="color:#008080;">16</span> listenableFuture.<span style="color:#ff0000;">addCallback</span>(successCallback, failureCallback);</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div style="text-align:right;"><a href="#_labelTop" rel="nofollow">回到顶部</a><a></a></div><h2>二、消费者</h2>
<a></a><h3>2.1配置</h3>
<pre><span style="color:#ff0000;"><strong><span style="color:#ff0000;">consumerProperties中的</span>具体配置暂时不用在意，后面有一章专门讲xml配置</strong></span></pre>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color:#008080;"> 1</span> &lt;!-- 1.定义consumer的参数 --&gt;
<span style="color:#008080;"> 2</span>     &lt;bean id="<span style="color:#ff0000;">consumerProperties</span>" <span style="color:#0000ff;">class</span>="java.util.HashMap"&gt;
<span style="color:#008080;"> 3</span>         &lt;constructor-arg&gt;
<span style="color:#008080;"> 4</span>             &lt;map&gt;
<span style="color:#008080;"> 5</span>                 &lt;entry key="bootstrap.servers" value="${bootstrap.servers}" /&gt;
<span style="color:#008080;"> 6</span>                 &lt;entry key="group.id" value="${group.id}" /&gt;
<span style="color:#008080;"> 7</span>                 &lt;entry key="enable.auto.commit" value="${enable.auto.commit}" /&gt;
<span style="color:#008080;"> 8</span>                 &lt;entry key="session.timeout.ms" value="${session.timeout.ms}" /&gt;
<span style="color:#008080;"> 9</span>                 &lt;entry key="key.deserializer"
<span style="color:#008080;">10</span>                     value="org.apache.kafka.common.serialization.StringDeserializer" /&gt;
<span style="color:#008080;">11</span>                 &lt;entry key="value.deserializer"
<span style="color:#008080;">12</span>                     value="org.apache.kafka.common.serialization.StringDeserializer" /&gt;
<span style="color:#008080;">13</span>             &lt;/map&gt;
<span style="color:#008080;">14</span>         &lt;/constructor-arg&gt;
<span style="color:#008080;">15</span>     &lt;/bean&gt;
<span style="color:#008080;">16</span> 
<span style="color:#008080;">17</span>     &lt;!-- 2.创建consumerFactory bean --&gt;
<span style="color:#008080;">18</span>     &lt;bean id="<span style="color:#ff0000;">consumerFactory</span>"
<span style="color:#008080;">19</span>         <span style="color:#0000ff;">class</span>="org.springframework.kafka.core.DefaultKafkaConsumerFactory" &gt;
<span style="color:#008080;">20</span>         &lt;constructor-arg&gt;
<span style="color:#008080;">21</span>             &lt;ref bean="consumerProperties" /&gt;
<span style="color:#008080;">22</span>         &lt;/constructor-arg&gt;
<span style="color:#008080;">23</span>     &lt;/bean&gt;
<span style="color:#008080;">24</span> 
<span style="color:#008080;">25</span>     &lt;!-- 3.定义消费实现类 --&gt;
<span style="color:#008080;">26</span>     &lt;bean id="<span style="color:#ff0000;">kafkaConsumerService</span>" <span style="color:#0000ff;">class</span>="xxx.service.impl.KafkaConsumerSerivceImpl" /&gt;
<span style="color:#008080;">27</span> 
<span style="color:#008080;">28</span>     &lt;!-- 4.消费者容器配置信息 --&gt;
<span style="color:#008080;">29</span>     &lt;bean id="<span style="color:#ff0000;">containerProperties</span>" <span style="color:#0000ff;">class</span>="org.springframework.kafka.listener.config.ContainerProperties"&gt;
<span style="color:#008080;">30</span>         &lt;!-- topic --&gt;
<span style="color:#008080;">31</span>         &lt;constructor-arg name="topics"&gt;
<span style="color:#008080;">32</span>             &lt;list&gt;
<span style="color:#008080;">33</span>                 &lt;value&gt;${kafka.consumer.topic.credit.<span style="color:#0000ff;">for</span>.lease}&lt;/value&gt;
<span style="color:#008080;">34</span>                 &lt;value&gt;${loan.application.feedback.topic}&lt;/value&gt;
<span style="color:#008080;">35</span>                 &lt;value&gt;${templar.agreement.feedback.topic}&lt;/value&gt;
<span style="color:#008080;">36</span>                 &lt;value&gt;${templar.aggrement.active.feedback.topic}&lt;/value&gt;
<span style="color:#008080;">37</span>                 &lt;value&gt;${templar.aggrement.agreementRepaid.topic}&lt;/value&gt;
<span style="color:#008080;">38</span>                 &lt;value&gt;${templar.aggrement.agreementWithhold.topic}&lt;/value&gt;
<span style="color:#008080;">39</span>                 &lt;value&gt;${templar.aggrement.agreementRepayRemind.topic}&lt;/value&gt;
<span style="color:#008080;">40</span>             &lt;/list&gt;
<span style="color:#008080;">41</span>         &lt;/constructor-arg&gt;
<span style="color:#008080;">42</span>         &lt;property name="<span style="color:#ff0000;">messageListener</span>" ref="kafkaConsumerService" /&gt;
<span style="color:#008080;">43</span>     &lt;/bean&gt;
<span style="color:#008080;">44</span>     &lt;!-- 5.消费者并发消息监听容器，执行doStart()方法 --&gt;
<span style="color:#008080;">45</span>     &lt;bean id="<span style="color:#ff0000;">messageListenerContainer</span>" <span style="color:#0000ff;">class</span>="org.springframework.kafka.listener.ConcurrentMessageListenerContainer" init-method="doStart" &gt;
<span style="color:#008080;">46</span>         &lt;constructor-arg ref="consumerFactory" /&gt;
<span style="color:#008080;">47</span>         &lt;constructor-arg ref="containerProperties" /&gt;
<span style="color:#008080;">48</span>         &lt;property name="concurrency" value="${concurrency}" /&gt;
<span style="color:#008080;">49</span>     &lt;/bean&gt;</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<pre><span style="color:#ff0000;"><strong>1.consumerProperties-》</strong></span><span style="color:#ff0000;"><strong>consumerFactory  载入配置构造消费者工厂</strong><br><strong>2.messageListener-》containerProperties  载入容器配置（topics）</strong><br><strong>3.consumerFactory+containerProperties-》messageListenerContainer 容器配置（topics）+消息监听器，构造一个并发消息监听容器，并执行初始化方法doStart</strong></span></pre>
<a></a><h3>2.2消费kafka消息</h3>
<p>方案1:直接实现<span style="color:#ff0000;">MessageListener</span>接口，复写<span style="color:#ff0000;">onMessage</span>方法，实现自定义消费业务逻辑。</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color:#008080;"> 1</span> <span style="color:#0000ff;">public</span> <span style="color:#0000ff;">class</span> KafkaConsumerSerivceImpl <span style="color:#0000ff;">implements</span> MessageListener&lt;String, String&gt;<span style="color:#000000;"> {
</span><span style="color:#008080;"> 2</span> <span style="color:#000000;">    @Override
</span><span style="color:#008080;"> 3</span>     <span style="color:#0000ff;">public</span> <span style="color:#0000ff;">void</span> <span style="color:#ff0000;">onMessage</span>(ConsumerRecord&lt;String, String&gt;<span style="color:#000000;"> data) {
</span><span style="color:#008080;"> 4</span>         <span style="color:#008000;">//</span><span style="color:#008000;">根据不同主题，消费</span>
<span style="color:#008080;"> 5</span>         <span style="color:#0000ff;">if</span>("主题1"<span style="color:#000000;">.equals(data.topic())){
</span><span style="color:#008080;"> 6</span>             <span style="color:#008000;">//</span><span style="color:#008000;">逻辑1</span>
<span style="color:#008080;"> 7</span>         }<span style="color:#0000ff;">else</span> <span style="color:#0000ff;">if</span>("主题2"<span style="color:#000000;">.equals(data.topic())){
</span><span style="color:#008080;"> 8</span>             <span style="color:#008000;">//</span><span style="color:#008000;">逻辑2</span>
<span style="color:#008080;"> 9</span> <span style="color:#000000;">        }
</span><span style="color:#008080;">10</span> <span style="color:#000000;">    }
</span><span style="color:#008080;">11</span> }</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>方案2：使用<span style="color:#ff0000;">@KafkaListener注解，并设置topic,支持SPEL表达式。这样方便拆分多个不同topic处理不同业务逻辑。（特别是有自己的事务的时候，尤其方便）</span></p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color:#008080;">1</span> <span style="color:#0000ff;">import</span><span style="color:#000000;"> org.springframework.kafka.annotation.KafkaListener;
</span><span style="color:#008080;">2</span> 
<span style="color:#008080;">3</span> <span style="color:#0000ff;">public</span> <span style="color:#0000ff;">class</span><span style="color:#000000;"> KafkaConsumerSerivceImpl {
</span><span style="color:#008080;">4</span>     <span style="color:#ff0000;">@KafkaListener</span>(topics = "${templar.aggrement.agreementWithhold.topic}"<span style="color:#000000;">)
</span><span style="color:#008080;">5</span>     <span style="color:#0000ff;">void</span> templarAgreementNoticewithhold(ConsumerRecord&lt;String, String&gt;<span style="color:#000000;"> data){
</span><span style="color:#008080;">6</span>        <span style="color:#008000;">//<span style="color:#008000;">消费业务逻辑</span></span>
<span style="color:#008080;">7</span> <span style="color:#000000;">    }
</span><span style="color:#008080;">8</span> }</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>三、总结</p>
<p>本章我们实现了一个简单的kafka生产、消费消息的实践。到这里我们已经会基本使用kafka了。是不是很简单...</p>
<p>下一章，我们从源码角度来深入分析spring-kafka。</p>
<p> </p></div>            </div>
                </div>