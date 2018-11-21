---
layout:     post
title:      Ubuntu下kafka使用总结
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>安装运行环境</p>
<pre><code>apt-get install openjdk-8-jre
pip3 install kafka
</code></pre>
<p>官网下载kafka 略，直接下从官网下就好了，不需要安装<br>
<a href="http://kafka.apache.org/downloads" rel="nofollow">官网下载地址</a><br>
启动kafka</p>
<pre><code>bin/zookeeper-server-start.sh config/zookeeper.properties
bin/kafka-server-start.sh config/server.properties
</code></pre>
<p>启动生产者：<br>
1.从终端启动</p>
<pre><code>bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test
#开启了一个名为test的topic
</code></pre>
<p>2.从python3启动</p>
<pre><code>&gt;&gt;&gt; from kafka import KafkaProducer
&gt;&gt;&gt; producer = KafkaProducer(bootstrap_servers='localhost:9092')
&gt;&gt;&gt; for _ in range(100):
...     producer.send('test',b'some_message_bytes')  #指定发送给名为test的topic
</code></pre>
<p>启动消费者：<br>
1.从终端启动</p>
<pre><code>bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning
</code></pre>
<p>2.从python3启动</p>
<pre><code>&gt;&gt;&gt; from kafka import KafkaConsumer
&gt;&gt;&gt; consumer=KafkaConsumer('test',bootstrap_servers=['localhost:9092'])
&gt;&gt;&gt; for msg in consumer:
...   recv = "%s:%d:%d: key=%s value=%s" %(msg.topic,msg.partition,msg.offset,msg.key,msg.value)
...   print(recv)
...
test:0:222: key=None value=b'some_message_bytes'
test:0:223: key=None value=b'some_message_bytes'
test:0:224: key=None value=b'some_message_bytes'
test:0:225: key=None value=b'some_message_bytes'
。。。。
</code></pre>
<p>测试可用，要先启动消费者再启动生产者，topic一定要一致<br>
<a href="https://blog.csdn.net/see_you_see_me/article/details/78468421" rel="nofollow">部分参考来源</a><br>
<a href="https://www.cnblogs.com/leaf-7/p/5310054.html" rel="nofollow">kafka与zookeeper简介</a><br>
<a href="https://www.cnblogs.com/tonglin0325/p/8810313.html" rel="nofollow">Kafka中bootstrap-server、broker-list和zookeeper的区别</a><br>
有空可以看一下极客学院的课程</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>