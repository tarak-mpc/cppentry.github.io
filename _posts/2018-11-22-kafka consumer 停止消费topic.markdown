---
layout:     post
title:      kafka consumer 停止消费topic
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1>现象</h1><p>在kafka consumer (以 kafka1.0.0为例)消费 topic 时，常常会出现程序还在运行，但是已经不消费消息了(kafka producer正常生产消息)，使用kafka命令查看，kafka 已经没有consumer 的信息了。</p><p>实验用例</p><p>实验 kafka consumer 实现：<br></p><pre><code class="language-java">package com.muhao.kafka;

import java.util.Arrays;
import java.util.Properties;

import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.KafkaConsumer;

public class MyKafKaConsumer {
	public static void main(String[] args) throws InterruptedException {
		Properties props = new Properties();
		props.put("bootstrap.servers", "192.168.220.10:9092");
		props.put("group.id", "test");
		props.put("enable.auto.commit", "true");
		props.put("auto.commit.interval.ms", "1000");
		props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
		props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
		KafkaConsumer&lt;String, String&gt; consumer = new KafkaConsumer&lt;&gt;(props);
		consumer.subscribe(Arrays.asList("my-topic"));
		while (true) {
			ConsumerRecords&lt;String, String&gt; records = consumer.poll(100);
			for (ConsumerRecord&lt;String, String&gt; record : records) {
				// 一次停止10 秒钟，如果上一次得到超过30条消息，就会出现kafka consumer停止消费的现象
				Thread.sleep(10000L);
				System.out.printf("offset = %d, key = %s, value = %s%n", record.offset(), record.key(), record.value());
				
			}
		}

	}
}
</code></pre><p>实验 kafka producer 实现：</p><pre><code class="language-java">package com.muhao.kafka;

import java.util.Properties;

import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.Producer;
import org.apache.kafka.clients.producer.ProducerRecord;


public class MyKafkaProducer {
	public static void main(String[] args) {
		Properties props = new Properties();
		 props.put("bootstrap.servers", "192.168.220.10:9092");
		 props.put("acks", "all");
		 props.put("retries", 0);
		 props.put("batch.size", 16384);
		 props.put("linger.ms", 1);
		 props.put("buffer.memory", 33554432);
		 props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
		 props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");

		 Producer&lt;String, String&gt; producer = new KafkaProducer&lt;&gt;(props);
		 for (int i = 0; i &lt; 1000; i++)
		     producer.send(new ProducerRecord&lt;String, String&gt;("my-topic", Integer.toString(i), "hello message is number : "+Integer.toString(i)));

		 producer.close();
	}
}</code></pre><p>启动 kafka consumer ，成功运行后在 kafka命令行执行 </p><p>bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --describe  --group test<br></p><p><img src="https://img-blog.csdn.net/20180407121640929" alt=""><br></p><p>发现kafka consumer 已经注册到kafka集群中了。</p><p>kafka producer发送消息让kafka consumer 消费，但是consumer是阻塞的，等待5分钟时候，运行命令行</p><p><img src="https://img-blog.csdn.net/20180407121908336" alt=""><br></p><p>发现kafka集群已经没有了 consumer的消息，但是程序仍在运行。</p><p><img src="https://img-blog.csdn.net/20180407122001136" alt=""><br></p><h1>解决及建议</h1><p>这种现象也是纠结了好长时间，查看源码，终于明白了，原来是在 kafka consumer 运行时，要和kafka集群的协调节点做心跳交流，这也是kafka集群给consumer做负载均衡的条件。但是但是consumer内部也会有一个计时器，记录上一次向 kafka 集群 poll 的时间，另外心跳线程会检测该现在距上一次poll的时间，如果该时间差超过了设定时间(kafka consumer默认的是 5分钟)，就会想kafka集群发出leaveGroup，这时kafka集群会注销掉该consumer 的信息。</p><p>建议：kafka consumer 在消费消息时，不要使用阻塞方法，比如blockqueue、网络发送设置超时时间……<br>总得一句就是上下两次poll 的时间间隔不要超过5分钟(默认的时间)。<br></p>            </div>
                </div>