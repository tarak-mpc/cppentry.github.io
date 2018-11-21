---
layout:     post
title:      Nginx=>Flume=>Kafka 流程总结
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/liuge36/article/details/83791470				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-light">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1><a id="nginxflumekafka_1"></a>nginx=&gt;flume=&gt;kafka</h1>
<blockquote>
<ol>
<li>编写flume 日志收集文件</li>
</ol>
</blockquote>
<p>nginx日志<br>
access.log====&gt;flume</p>
<pre><code>a1.sources = r1
a1.sinks = k1
a1.channels = c1

a1.sources.r1.type = exec
a1.sources.r1.command = tail -F /root/logs/access.log
a1.sources.r1.shell = /bin/sh -c

a1.channels.c1.type = memory

a1.sinks.k1.type = org.apache.flume.sink.kafka.KafkaSink
# 这个地址是kafka的监听地址
a1.sinks.k1.brokerList = spark001:9092
# 注意这里的topic是zk的topic
a1.sinks.k1.topic = test
a1.sinks.k1.batchSize = 5
a1.sinks.k1.requiredAcks =1

#a1.sinks.k1.type = logger

a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
</code></pre>
<blockquote>
<ol start="2">
<li>关于Kafka的部署</li>
</ol>
</blockquote>
<p>Step 1: Start the zookeeper           #得到QuorumPeerMain进程</p>
<blockquote>
<p><a href="http://zkServer.sh" rel="nofollow">zkServer.sh</a> start</p>
</blockquote>
<p>[hadoop@hadoop000 conf]$ vim zoo.cfg<br>
#修改数据的临时存放目录，默认是在tmp目录下的<br>
dataDir=/home/hadoop/app/tmp/zk<br>
#:wq 保存退出</p>
<p>Step 2: Start the server           得到==》kafka进程</p>
<blockquote>
<p><a href="http://kafka-server-start.sh" rel="nofollow">kafka-server-start.sh</a> -deamon $KAFKA_HOME/config/server.properties</p>
</blockquote>
<p>配置，修改解压目录下config配置文件</p>
<p>配置server.properties[需要注意的地方]<br>
broker.id=0  解释：是kafka，一个broker<br>
listeners      解释：监听的端口<br>
<a href="http://host.name" rel="nofollow">host.name</a>    解释：当前机器<br>
log.dirs       解释：kafka的日志存放目录<br>
zookeeper.connect  zk的地址</p>
<p>修改好之后，保存退出</p>
<p>Step 3: Create a topic           # 注意和之前flume的topic对应起来</p>
<blockquote>
<p><a href="http://kafka-topics.sh" rel="nofollow">kafka-topics.sh</a> -create -zookeeper spark001:2181 -replication-factor 1 -partitions 1 -topic test</p>
</blockquote>
<p>ps：<a href="http://kafka-topics.sh" rel="nofollow">kafka-topics.sh</a> -list -zookeeper spark001:2181</p>
<p>Step 4: 开启之前的agent                    （作为生产者）</p>
<blockquote>
<p>flume-ng agent --name a1 --conf . --conf-file ./lamda_imooc.conf -Dflume.root.logger=INFO,console</p>
</blockquote>
<p>步骤4:扩展：控制台作为生产者</p>
<blockquote>
<p><a href="http://kafka-console-producer.sh" rel="nofollow">kafka-console-producer.sh</a> --broker-list spark001:9092 --topic test</p>
</blockquote>
<p>Step 5: Start a consumer                         （消费者）</p>
<blockquote>
<p><a href="http://kafka-console-consumer.sh" rel="nofollow">kafka-console-consumer.sh</a> --zookeeper spark001:2181 --topic test</p>
</blockquote>
<p>最后 确保flume监听的文件在不断产生新的日志<br>
文件位置 /root/logs/access.log</p>
<pre><code>#!/bin/bash
   i=1;
   j=0;
   while (test $i -le 6170967 )
     do
       j=`expr $i + 9`	
       sed -n $i,$j'p' /root/logs/all.log &gt;&gt; /root/logs/access.log
       i=`expr $i + 10`
       sleep 5
      # echo $i
     done
  #新建这个shell文件，把这个shell文件执行起来
</code></pre>
<p>上面的操作执行之后，就会收到刷屏的结果，哈哈哈！！<br>
nginx=&gt;flume=&gt;kafka<br>
补充：<br>
head -n 100 大日志文件.log 100_access.log<br>
wc -l 100_access.log</p>
<pre><code>spark-submit --master local[5] \
--jars $(echo /root/apps/hbase-1.2.0-cdh5.7.0/lib/*.jar | tr ' ' ',') \
--class com.csylh.spark.project.spark.ImoocStatStreamingApp \
--packages org.apache.spark:spark-streaming-kafka-0-8_2.11:2.2.0 \
/root/jars/streaming-1.0-SNAPSHOT.jar \
spark001:2181 test test 1

</code></pre>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>