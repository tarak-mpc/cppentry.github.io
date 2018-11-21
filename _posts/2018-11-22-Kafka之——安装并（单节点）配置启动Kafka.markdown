---
layout:     post
title:      Kafka之——安装并（单节点）配置启动Kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/l1028386804/article/details/78348367				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><a href="http://blog.csdn.net/l1028386804/article/details/78348367" rel="nofollow">转载请注明出处：http://blog.csdn.net/l1028386804/article/details/78348367</a><br></p>
<h3>1. 下载Kafka<br></h3>
<p>从<a href="http://kafka.apache.org/" rel="nofollow" title="">Kafka官网</a>下载最新的Kafka，目前我下载的版本为kafka_2.10-0.8.1.1.tgz</p>
<h3>2、解压Kafka</h3>
<p></p>
<pre><code class="language-plain">tar -zxvf kafka_2.10-0.8.1.1.tgz</code></pre>
<p></p>
<h3>3. 修改Zookeeper服务器配置，并启动</h3>
<p></p>
<pre><code class="language-plain">cd kafka_2.10-0.8.1.1
vi config/zookeeper.properties 
 
#修改ZooKeeper的数据目录
dataDir=/usr/local/db/zookeeper
 
#配置host.name和advertised.host.name为IP地址，防止通过Java客户端连接时解析为localhost
host.name=192.168.209.121
 
advertised.host.name=192.168.209.121
 
#启动Zookeeper服务器
./zookeeper-server-start.sh /usr/local/kafka_2.10-0.8.1.1/config/zookeeper.properties</code></pre>
<p>后台启动Zookeeper</p>
<p></p><pre><code class="language-plain">./zookeeper-server-start.sh  -daemon /usr/local/kafka_2.10-0.8.1.1/config/zookeeper.properties</code></pre>
<h3>4.修改Kafka配置，并启动Kafka服务器</h3>
<p></p>
<pre><code class="language-plain">vi config/server.properties 
 
log.dirs=/usr/local/kafka/kafka-logs
 
#启动Kafka服务器
./kafka-server-start.sh /usr/local/kafka_2.10-0.8.1.1/config/server.properties</code></pre>
<p>后台启动Kafka</p>
<p></p><pre><code class="language-plain">./kafka-server-start.sh  -daemon /usr/local/kafka_2.10-0.8.1.1/config/server.properties</code></pre>
<h3>5.创建并查看Topic</h3>
<p></p>
<pre><code class="language-plain">cd /usr/local/kafka_2.10-0.8.1.1/bin/
./kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test
Created topic "test".
 
#查看刚才的topic
./kafka-topics.sh --list --zookeeper localhost:2181
test</code></pre>
<p></p>
<h3>6. 使用生产者发送消息，每行是一条独立的消息</h3>
<p></p>
<pre><code class="language-plain">./kafka-console-producer.sh --broker-list localhost:9092 --topic test
This is a message
This is My mesage</code></pre>
<p></p>
<h3>7. 使用消费者接收消息</h3>
<p></p>
<pre><code class="language-plain">./kafka-console-consumer.sh --zookeeper localhost:2181 --topic test --from-beginning
This is a message
This is My me</code></pre>
<p></p>
<p><br></p>
            </div>
                </div>