---
layout:     post
title:      kafka(三)-kafka安装
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>前面几篇介绍了kafka的原理，本文主要讲解kafka的安装：</p>

<p><a href="https://blog.csdn.net/haoxin963/article/details/83245632" rel="nofollow">kafka(一)-为什么使用kafka</a></p>

<p><a href="https://blog.csdn.net/haoxin963/article/details/83245678" rel="nofollow">kafka(二)-kafka原理及介绍</a></p>

<p>现在我们准备三台机器，都是centos7.2</p>

<table><thead><tr><th>IP</th>
			<th>broker.id</th>
		</tr></thead><tbody><tr><td>192.168.10.1</td>
			<td>1</td>
		</tr><tr><td>192.168.10.2</td>
			<td>2</td>
		</tr><tr><td>192.168.10.3</td>
			<td>3</td>
		</tr></tbody></table><p><strong>1.下载安装压缩包：</strong></p>

<p>下载地址：<a href="http://mirrors.cnnic.cn/apache/kafka" rel="nofollow">http://mirrors.cnnic.cn/apache/kafka</a>  我这个下载的是kafka_2.12-1.1.0.tg，然后将压缩包传到三台服务器上，同时先安装好JDK1.8，JDK具体安装我的博客里有。</p>

<p><strong>2.在三个机器上解压程序，创建数据库目录</strong></p>

<pre class="has">
<code>tar xf kafka_2.12-1.1.0.tgz -C /usr/local/

mkdir /data/kafka/</code></pre>

<p><strong>3.修改三个节点上修改配置文件server.properties：</strong></p>

<pre class="has">
<code>cd /usr/local/kafka_2.12-1.1.0/

 vi config/server.properties</code></pre>

<p>192.168.10.1节点</p>

<pre class="has">
<code>broker.id=1
advertised.listeners=PLAINTEXT://192.168.10.1:9092
log.dirs=/data/kafka
zookeeper.connect=0.0.0.0:2181,192.168.10.2:2181,192.168.10.3:2181</code></pre>

<p>192.168.10.2节点</p>

<pre class="has">
<code>broker.id=2
advertised.listeners=PLAINTEXT://192.168.10.2:9092
log.dirs=/data/kafka
zookeeper.connect=192.168.10.1:2181,0.0.0.0:2181,192.168.10.3:2181</code></pre>

<p>192.168.10.3节点</p>

<pre class="has">
<code>broker.id=3
advertised.listeners=PLAINTEXT://192.168.10.3:9092
log.dirs=/data/kafka
zookeeper.connect=192.168.10.1:2181,192.168.10.2:2181,0.0.0.0:2181</code></pre>

<p><strong>4.启动服务&amp;查看log</strong> </p>

<pre class="has">
<code>./bin/kafka-server-start.sh -daemon config/server.properties  #启动

tailf logs/server.log #查看日志
</code></pre>

<p><strong>5.解决启动时内存不足</strong></p>

<p>## There is insufficient memory for the Java Runtime Environment to continue.</p>

<p># Native memory allocation (malloc) failed to allocate 986513408 bytes for committing reserved memory.</p>

<p># An error report file with more information is saved as:</p>

<p># //hs_err_pid6500.logOpenJDK 64-Bit Server VM warning: INFO: os::commit_memory(0x00000000bad30000, 986513408, 0) failed; error='Cannot allocate memory' (errno=12)</p>

<p>==========================================================</p>

<p>原因：kafka启动脚本kafka-server-start.sh中指定了kafka启动时需要的最小内存，默认为1G</p>

<p>export KAFKA_HEAP_OPTS="-Xmx1G -Xms1G"</p>

<p>虚拟机分配的虚拟内存在1G以下时就会出现该错误。</p>

<p> </p>

<p><strong>6.简单操作：</strong></p>

<p>1、新建一个主题topic: <code>kafka-topics.sh</code> <br>
创建一个只有一个分区和一个备份名为“test”的Topic</p>

<pre class="has">
<code># ./bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test
Created topic "test".</code></pre>

<p>2、查看主题： <br>
运行list topic命令，查看该主题：</p>

<pre class="has">
<code># ./bin/kafka-topics.sh --list --zookeeper localhost:2181
test    </code></pre>

<p>同时也可以在zookeeper中查看：</p>

<pre class="has">
<code># ./bin/zkCli.sh -server localhost:2181 
...
[zk: localhost:2181(CONNECTED) 1] ls /brokers
[ids, topics, seqid]
[zk: localhost:2181(CONNECTED) 2] ls /brokers/topics
[test]</code></pre>

<p>3、发送消息：<code>kafka-console-producer.sh</code> <br>
Kafka带有一个命令行客户端，它将从文件或标准输入中获取输入，并将其作为消息发送到Kafka集群。默认情况下，每行将作为单独的消息发送。 <br>
运行<code>producer</code>生产者,然后在控制台输入几条消息到服务器。</p>

<pre class="has">
<code># ./bin/kafka-console-producer.sh --broker-list 192.168.20.201:9092 --topic test
&gt;111
&gt;this is a message
&gt;this is anothet message
&gt;hehe
</code></pre>

<p>4、消费消息： <code>kafka-console-consumer.sh</code> <br>
Kafka也提供了一个消费消息的命令行工具，将存储的信息输出出来 <br>
在另一台机器上或者不同终端上执行此命令，这样就能将消息键入生产者终端，并将它们显示在消费者终端</p>

<pre class="has">
<code># ./bin/kafka-console-consumer.sh --bootstrap-server 192.168.20.203:9092 --topic test --from-beginning
111
this is a message
this is anothet message
hehe</code></pre>

<p>5、查看topic详细信息 <code>kafka-topics.sh --describe</code></p>

<pre class="has">
<code># ./bin/kafka-topics.sh --describe --zookeeper localhost:2181 --topic test
Topic:test      PartitionCount:1        ReplicationFactor:1     Configs:
        Topic: test     Partition: 0    Leader: 3       Replicas: 3     Isr: 3  


Topic:     主题名称
Partition: 分片编号
Leader:    该分区的leader节点
Replicas:  该副本存在于哪个broker节点
Isr:       活跃状态的broker</code></pre>            </div>
                </div>