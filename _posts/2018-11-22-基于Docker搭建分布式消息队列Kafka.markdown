---
layout:     post
title:      基于Docker搭建分布式消息队列Kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>本文基于Docker搭建一套单节点的Kafka消息队列，Kafka依赖Zookeeper为其管理集群信息，虽然本例不涉及集群，但是该有的组件都还是会有，典型的kafka分布式架构如下图所示。本例搭建的示例包含Zookeeper + Kafka + Kafka-manger</p>

<p><img alt="" class="has" src="//upload-images.jianshu.io/upload_images/8818451-7fa3bebabccb0c3e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/554/format/webp"></p>

<p>mark</p>

<p> </p>

<h1>获取镜像</h1>

<ul><li>zookeeper镜像：zookeeper:3.4.9</li>
	<li>kafka镜像：wurstmeister/kafka:0.10.2.0</li>
	<li>kafka-manager镜像：kafka-manager:latest</li>
</ul><h1>建立Zookeeper容器</h1>

<p>这里我们用最简单的方式创建一个独立的Zookeeper节点，如果要考虑zookeeper的高可用，可以将其做成一个集群，最好是能有多台机器。</p>

<pre class="has">
<code>docker run --name some-zookeeper \
--restart always \
-p 2181:2181 \
-d zookeeper
</code></pre>

<p>默认的，容器内配置文件在，/conf/zoo.cfg，数据和日志目录默认在/data 和 /datalog，需要的话可以将上述目录映射到宿主机的可靠文件目录下。详情参考<a href="https://link.jianshu.com?t=https://hub.docker.com/_/zookeeper/" rel="nofollow">Zookeeper官方镜像</a></p>

<h1>建立kafka节点</h1>

<p>这里同样只做一个简单的单点kafka</p>

<pre class="has">
<code>docker run --name kafka \
-p 9092:9092 \
-e KAFKA_ADVERTISED_HOST_NAME=kafka01 \
-e KAFKA_CREATE_TOPICS="test:1:1" \
-e KAFKA_ZOOKEEPER_CONNECT=100.100.16.231:2181 \
-d  wurstmeister/kafka  
</code></pre>

<p>详情参考<a href="https://link.jianshu.com?t=https://hub.docker.com/r/wurstmeister/kafka/" rel="nofollow">Kafka官方镜像</a></p>

<h1>创建Kafka管理节点</h1>

<p>kafka-manager有图形化UI，可以方便的监控集群状态，调整队列配置</p>

<pre class="has">
<code>docker run -itd \
--restart=always \
--name=kafka-manager \
-p 9000:9000 \
-e ZK_HOSTS="100.100.16.231:2181" \
sheepkiller/kafka-manager
</code></pre>

<p>容器启动以后访问主机的9000端口，<a href="https://link.jianshu.com?t=http://xxx:9000" rel="nofollow">http://xxx:9000</a></p>

<p><img alt="" class="has" src="//upload-images.jianshu.io/upload_images/8818451-7099cfe044147c28.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/554/format/webp"></p>

<p>mark</p>

<p>首次进入需要添加一个集群标识，如下图所示</p>

<p><img alt="" class="has" src="//upload-images.jianshu.io/upload_images/8818451-d4fdf7bd96e15bb1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/554/format/webp"></p>

<p>mark</p>

<p>配置好以后，通过Zookeeper该管理节点可以读取到整个Kafka集群的信息，并且我们可以通过JMX直接看到集群的健康状态</p>

<p> </p>

<p><img alt="" class="has" src="//upload-images.jianshu.io/upload_images/8818451-13c22b21e615916b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/554/format/webp"></p>

<p>mark</p>

<p>也可以看到主题的读写速度，偏移量等信息，如下图所示：</p>

<p> </p>

<p><img alt="" class="has" src="//upload-images.jianshu.io/upload_images/8818451-d4b9c15e66b8acfe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/554/format/webp"></p>

<p>mark</p>

<h1>读写验证</h1>

<p>读写验证的方法有很多，这里我们用kafka容器自带的工具来验证，首先进入到kafka容器的交互模式：</p>

<pre class="has">
<code>docker exec -it kafka /bin/bash

</code></pre>

<ul><li>创建一个主题：</li>
</ul><pre class="has">
<code>/opt/kafka/bin/kafka-topics.sh --create --zookeeper 100.100.16.231:2181 --replication-factor 1 --partitions 1 --topic my-test
</code></pre>

<ul><li>查看刚创建的主题：</li>
</ul><pre class="has">
<code>/opt/kafka/bin/kafka-topics.sh --list --zookeeper 100.100.16.231:2181
</code></pre>

<p><img alt="" class="has" src="//upload-images.jianshu.io/upload_images/8818451-c23390f73062a075.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/554/format/webp"></p>

<p>mark</p>

<ul><li>发送消息：</li>
</ul><pre class="has">
<code>/opt/kafka/bin/kafka-console-producer.sh --broker-list  100.100.16.231:9092 --topic my-test
</code></pre>

<p>This is a message<br>
This is another message</p>

<ul><li>读取消息：</li>
</ul><pre class="has">
<code>/opt/kafka/bin/kafka-console-consumer.sh --bootstrap-server 100.100.16.231:9092 --topic my-test --from-beginning
</code></pre>

<p>参考：<a href="https://link.jianshu.com?t=https://kafka.apache.org/quickstart" rel="nofollow">https://kafka.apache.org/quickstart</a></p>

<p>原文链接：<a href="https://link.jianshu.com?t=http://dearcharles.cn/2017/11/23/%E5%9F%BA%E4%BA%8EDocker%E6%90%AD%E5%BB%BA%E5%88%86%E5%B8%83%E5%BC%8F%E6%B6%88%E6%81%AF%E9%98%9F%E5%88%97Kafka/" rel="nofollow">《基于Docker搭建分布式消息队列Kafka》</a></p>

<p> </p>            </div>
                </div>