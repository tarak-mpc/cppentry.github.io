---
layout:     post
title:      Spring-Boot集成Kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>这篇文章不讲Kafka相关概念，只是实战。具体了解请参考：</p>

<p>《分布式消息中间件实践》</p>

<p>《Kafka权威指南》</p>

<p>《spring-kafka-reference》spring集成kafka官方文档。</p>

<p>以为SpringBoot集成Kafka</p>

<h3>pom.xml</h3>

<pre class="has">
<code class="language-java">&lt;project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"&gt;
	&lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;

	&lt;groupId&gt;org.niugang&lt;/groupId&gt;
	&lt;artifactId&gt;kafka-spring-boot&lt;/artifactId&gt;
	&lt;version&gt;0.0.1-SNAPSHOT&lt;/version&gt;
	&lt;packaging&gt;jar&lt;/packaging&gt;
	&lt;url&gt;http://maven.apache.org&lt;/url&gt;
	&lt;properties&gt;
		&lt;project.build.sourceEncoding&gt;UTF-8&lt;/project.build.sourceEncoding&gt;
	&lt;/properties&gt;
	&lt;!-- 继承父包 --&gt;
	&lt;parent&gt;
		&lt;groupId&gt;org.springframework.boot&lt;/groupId&gt;
		&lt;artifactId&gt;spring-boot-starter-parent&lt;/artifactId&gt;
		&lt;version&gt;1.5.10.RELEASE&lt;/version&gt;
		&lt;relativePath&gt;&lt;/relativePath&gt;
	&lt;/parent&gt;
	&lt;dependencies&gt;
		&lt;dependency&gt;
			&lt;groupId&gt;org.springframework.boot&lt;/groupId&gt;
			&lt;artifactId&gt;spring-boot-starter-web&lt;/artifactId&gt;
		&lt;/dependency&gt;
		&lt;dependency&gt;
			&lt;groupId&gt;org.springframework.boot&lt;/groupId&gt;
			&lt;artifactId&gt;spring-boot-starter-test&lt;/artifactId&gt;
		&lt;/dependency&gt;
		&lt;dependency&gt;
			&lt;groupId&gt;org.springframework.kafka&lt;/groupId&gt;
			&lt;artifactId&gt;spring-kafka&lt;/artifactId&gt;
			&lt;/dependency&gt;
	&lt;/dependencies&gt;
	&lt;!--maven的插件 --&gt;
	&lt;build&gt;
		&lt;plugins&gt;
			&lt;plugin&gt;
				&lt;groupId&gt;org.springframework.boot&lt;/groupId&gt;
				&lt;artifactId&gt;spring-boot-maven-plugin&lt;/artifactId&gt;
			&lt;/plugin&gt;
		&lt;/plugins&gt;
	&lt;/build&gt;
&lt;/project&gt;
</code></pre>

<h3>application.properties</h3>

<pre class="has">
<code class="language-java">server.port=8086
###########################################kafka about config #######################################################
spring.kafka.bootstrap-servers=localhost:9092
##########################producer about config##############################
spring.kafka.producer.acks=1
spring.kafka.producer.batch-size=16384
spring.kafka.producer.retries=0
spring.kafka.producer.buffer-memory=33554432
#spring.kafka.producer.key-serializer=org.apache.kafka.common.serialization.StringSerializer.class
#spring.kafka.producer.value-serializer=org.apache.kafka.common.serialization.StringSerializer.class
##########################consumer about config##############################
spring.kafka.consumer.enable-auto-commit=true
spring.kafka.consumer.group-id=kafka_group_2
spring.kafka.consumer.auto-commit-interval=100
#spring.kafka.consumer.key-deserializer=org.apache.kafka.common.serialization.StringDeserializer.class
#spring.kafka.consumer.value-deserializer=org.apache.kafka.common.serialization.StringDeserializer.class</code></pre>

<h3>发送消息</h3>

<pre class="has">
<code class="language-java">package com.niugang.controller;

import java.util.concurrent.ExecutionException;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.kafka.core.KafkaTemplate;
import org.springframework.kafka.support.SendResult;
import org.springframework.util.concurrent.ListenableFuture;
import org.springframework.util.concurrent.ListenableFutureCallback;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
/**
 * 
 * @ClassName:  SenderConttoller   
 * @Description:验证发送消息
 * @author: niugang
 * @date:   2018年11月3日 上午9:58:19   
 * @Copyright: 863263957@qq.com. All rights reserved. 
 *
 */
@RestController
public class SenderConttoller {
	private Logger logger = LoggerFactory.getLogger(SenderConttoller.class);

	@Autowired
	private KafkaTemplate&lt;String, String&gt; template;

	/**
	 * 同步发送
	 * 
	 * @return
	 * @throws ExecutionException
	 * @throws InterruptedException
	 */
	@RequestMapping("syncSendMessage")
	public String syncSendMessage() {
		for (int i = 0; i &lt; 100; i++) {
			try {
				template.send("kafka-boot", "0", "foo" + i).get();
			} catch (InterruptedException e) {
				logger.error("sync send message fail [{}]", e.getMessage()); 
				e.printStackTrace();
			} catch (ExecutionException e) {
				logger.error("sync send message fail [{}]", e.getMessage());
				e.printStackTrace();
			}
		}
		return "success";
	}

	/**
	 * 异步发送
	 * 
	 * @return
	 */
	@RequestMapping("asyncSendMessage")
	public String sendMessageAsync() {
		for (int i = 0; i &lt; 100; i++) {
			/**
			 * &lt;p&gt;
			 * SendResult:如果消息成功写入kafka就会返回一个RecordMetaData对象;result.
			 * getRecordMetadata() 他包含主题信息和分区信息，以及集成在分区里的偏移量。
			 * 查看RecordMetaData属性字段就知道了
			 * &lt;/p&gt;
			 *
			 */
			ListenableFuture&lt;SendResult&lt;String, String&gt;&gt; send = template.send("kafka-boot", "0", "foo" + i);
			send.addCallback(new ListenableFutureCallback&lt;SendResult&lt;String, String&gt;&gt;() {

				@Override
				public void onSuccess(SendResult&lt;String, String&gt; result) {
					logger.info("async send message success partition [{}]", result.getRecordMetadata().partition());
					logger.info("async send message success offest[{}]", result.getRecordMetadata().offset());
				}

				@Override
				public void onFailure(Throwable ex) {
					logger.error("async send message fail [{}]", ex.getMessage());

				}
			});
		}
		return "success";
	}
}
</code></pre>

<h3>消费者</h3>

<pre class="has">
<code class="language-java">package com.niugang.config;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.kafka.annotation.KafkaListener;
import org.springframework.stereotype.Component;


/**
 * 
 * @ClassName: ConsumerListener
 * @Description:消费者监听
 * @author: niugang
 * @date: 2018年10月21日 下午2:05:21
 * @Copyright: 863263957@qq.com. All rights reserved.
 *
 */
@Component
public class ConsumerListener {
	private Logger logger = LoggerFactory.getLogger(ConsumerListener.class);

	@KafkaListener(id = "foo", topics = "kafka-boot")
	public void listen1(String foo) {
		logger.info("message content [{}]", foo);
	}
}</code></pre>

<h3>启动类</h3>

<pre class="has">
<code class="language-java">package com.niugang;


import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.web.support.SpringBootServletInitializer;


/**
 * 
 * @ClassName:  KafkaApplication   
 * @Description：启动类  
 * @author: niugang
 * @date:   2018年10月20日 下午7:55:38   
 * @Copyright: 863263957@qq.com. All rights reserved. 
 *
 */
@SpringBootApplication
public class KafkaApplication extends SpringBootServletInitializer {

	public static void main(String[] args) {
		SpringApplication.run(KafkaApplication.class, args);
	}

}
</code></pre>

<p>源码：<a href="https://gitee.com/niugangxy/kafka/tree/master/kafka-spring-boot" rel="nofollow">https://gitee.com/niugangxy/kafka/tree/master/kafka-spring-boot</a></p>

<p>                                                                        <strong>       微信公众号</strong></p>

<p>                                               <img alt="" class="has" src="https://img-blog.csdn.net/20180403143416927"></p>

<p>                                                                             JAVA程序猿成长之路</p>

<p>                                                       分享学习资源，学习方法，记录程序员生活。</p>

<p> </p>

<p> </p>            </div>
                </div>