---
layout:     post
title:      Kafka使用Java客户端进行访问
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>卡夫卡中间件安装<br>
    find / -name kafka<br>
    /opt/cloudera/parcels/KAFKA-3.0.0-1.3.0.0.p0.40/etc/default/kafka<br>
    /opt/cloudera/parcels/KAFKA-3.0.0-1.3.0.0.p0.40/etc/kafka<br>
    /opt/cloudera/parcels/KAFKA-3.0.0-1.3.0.0.p0.40/lib/kafka<br>
    /var/lib/kafka<br>
    /var/log/kafka<br>
    /var/local/kafka<br>
    /etc/kafka<br>
    find: “/proc/12696”: 没有那个文件或目录<br>
    find: “/proc/12697”: 没有那个文件或目录</p>

<p><br>
cd /opt/cloudera/parcels/KAFKA-3.0.0-1.3.0.0.p0.40/lib/kafka<br>
执行ls显示：<br>
bin  cloudera  config  libs  LICENSE  NOTICE  site-docs</p>

<p>执行以下命令查看版本<br>
find ./libs/ -name \*kafka_\* | head -1 | grep -o '\kafka[^\n]*'<br>
显示：<br>
kafka_2.11-0.11.0-kafka-3.0.0.jar</p>

<p>创建topic<br>
bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test</p>

<p>展示topic<br>
bin/kafka-topics.sh --list --zookeeper localhost:2181</p>

<p>描述topic<br>
bin/kafka-topics.sh --describe --zookeeper localhost:2181 --topic my-replicated-topic</p>

<p>生产者：<br>
bin/kafka-console-producer.sh --broker-list 130.51.23.95:9092 --topic my-replicated-topic</p>

<p>消费者：<br>
bin/kafka-console-consumer.sh --zookeeper 130.51.23.95:2181 --topic test --from-beginnin</p>

<p>复制代码</p>

<p> </p>

<p>删除topicbin/kafka-topics.sh --delete --zookeeper 130.51.23.95:2181 --topic topicname删除topic中存储的内容在config/server.properties中找到如下的位置<br>
   </p>

<p>删除log.dirs指定的文件目录，然后重新启动就可以了<br>
   <br>
   <br>
   <br>
启动命令行生产者（需要注意带端口）<br>
kafka-console-producer --broker-list node2.cdh.mulhyac.com:9092,node3.cdh.mulhyac.com:9092,node1.cdh.mulhyac.com:9092 --topic test<br>
kafka-console-producer --zookeeper node2.cdh.mulhyac.com,node3.cdh.mulhyac.com,node1.cdh.mulhyac.com --topic test<br>
kafka-console-consumer --zookeeper node2.cdh.mulhyac.com,node3.cdh.mulhyac.com,node1.cdh.mulhyac.com --topic test</p>

<p>1. 添加maven依赖包<br>
&lt;dependency&gt;<br>
    &lt;groupId&gt;org.apache.kafka&lt;/groupId&gt;<br>
    &lt;artifactId&gt;kafka-clients&lt;/artifactId&gt;<br>
    &lt;version&gt;0.9.0.1&lt;/version&gt;<br>
&lt;/dependency&gt;</p>

<p>2. 生产者代码<br>
public class KafkaProducerExample {<br>
    public static void main(String[] args) {<br>
        Properties props = new Properties();<br>
        props.put("bootstrap.servers", "master:9092");<br>
        props.put("acks", "all");<br>
        props.put("retries", 0);<br>
        props.put("batch.size", 16384);<br>
        props.put("linger.ms", 1);<br>
        props.put("buffer.memory", 33554432);<br>
        props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");<br>
        props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");</p>

<p>        Producer&lt;String, String&gt; producer = new KafkaProducer&lt;&gt;(props);<br>
        for(int i = 0; i &lt; 100; i++)<br>
            producer.send(new ProducerRecord&lt;&gt;("topic1", Integer.toString(i), Integer.toString(i)));</p>

<p>        producer.close();<br>
    }<br>
}<br>
3.消费者代码</p>

<p>import org.apache.kafka.clients.consumer.ConsumerRecord;<br>
import org.apache.kafka.clients.consumer.ConsumerRecords;<br>
import org.apache.kafka.clients.consumer.KafkaConsumer;</p>

<p>import java.util.Arrays;<br>
import java.util.Properties;</p>

<p>public class KafkaConsumerExample {<br>
    public static void main(String[] args) {<br>
        Properties props = new Properties();<br>
        props.put("bootstrap.servers", "master:9092");<br>
        props.put("group.id", "test");<br>
        props.put("enable.auto.commit", "true");<br>
        props.put("auto.commit.interval.ms", "1000");<br>
        props.put("session.timeout.ms", "30000");<br>
        props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");<br>
        props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");<br>
        KafkaConsumer&lt;String, String&gt; consumer = new KafkaConsumer&lt;&gt;(props);<br>
        consumer.subscribe(Arrays.asList("topic1"));<br>
        while (true) {<br>
            ConsumerRecords&lt;String, String&gt; records = consumer.poll(100);<br>
            for (ConsumerRecord&lt;String, String&gt; record : records)<br>
                System.out.printf("offset = %d, key = %s, value = %s\n", record.offset(), record.key(), record.value());<br>
        }<br>
    }<br>
}<br>
4. 执行程序</p>

<p>lib底下需要有：kafka-clients-0.9.0.1.jar log4j-1.2.17.jar slf4j-api-1.7.6.jar slf4j-log4j12-1.7.6.jar<br>
生产者:<br>
java -classpath learn-kafka-1.0.0.jar:lib/* com.mulhyac.learn.kafka.demo.KafkaProducerExample</p>

<p>消费者:<br>
java -classpath learn-kafka-1.0.0.jar:lib/* com.mulhyac.learn.kafka.demo.KafkaConsumerExample</p>            </div>
                </div>