---
layout:     post
title:      docker环境下的zookeeper和kafka部署
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h3>kafka简单介绍</h3>

<p><strong>Kafka</strong> 是 <em>LinkedIn</em> 开源的一种高吞吐量的分布式发布订阅消息系统，kafka的诞生就是为了处理海量日志数据，所以kafka处理消息的效率非常高，即使是非常普通的硬件也可以支持每秒数百万的消息。<br><strong>kafka</strong> 天然支持集群负载均衡，使用 <em>zookeeper</em> 进行分布式协调管理。不支持事务，有一定概率丢失消息。<br><strong>kafka</strong> 的特点，决定了使用场景：日志中间件。</p>

<h3>下载docker镜像</h3>

<p>zookeeker: <code>docker pull zookeeper:latest</code><br>
kafka: <code>docker pull wurstmeister/kafka:latest</code></p>

<h3>创建并启动容器</h3>

<p>先启动zookeeper:</p>

<pre class="has">
<code>docker run -d --name zookeeper --publish 2181:2181 \
--volume /etc/localtime:/etc/localtime \
zookeeper:latest
</code></pre>

<p>zookeeper启动完成后再启动kafka:</p>

<pre class="has">
<code>docker run -d --name kafka --publish 9092:9092 \
--link zookeeper \
--env KAFKA_ZOOKEEPER_CONNECT=zookeeper:2181 \
--env KAFKA_ADVERTISED_HOST_NAME=kafka所在宿主机的IP \
--env KAFKA_ADVERTISED_PORT=9092 \
--volume /etc/localtime:/etc/localtime \
wurstmeister/kafka:latest
</code></pre>

<h3>向kafka发送测试消息</h3>

<p>运行 <code>docker ps</code>，找到kafka的 <em>CONTAINER ID</em>，运行 <code>docker exec -it ${CONTAINER ID} /bin/bash</code>，进入kafka容器。<br>
进入kafka默认目录 <code>/opt/kafka_2.11-0.10.1.0</code>，运行 <code>bin/kafka-topics.sh --create --zookeeper zookeeper:2181 --replication-factor 1 --partitions 1 --topic test</code>，创建一个 <em>topic</em> 名称为 <em>test</em>。<br>
运行 <code>bin/kafka-topics.sh --list --zookeeper zookeeper:2181</code> 查看当前的 <em>topic</em> 列表。<br>
运行一个消息生产者，指定 <em>topic</em> 为刚刚创建的 <em>test</em> ， <code>bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test</code>，输入一些测试消息。<br>
运行一个消息消费者，同样指定 <em>topic</em> 为 <em>test</em>， <code>bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning</code>，可以接收到生产者发送的消息。</p>

<h3>最后</h3>

<p><strong>kafka</strong> 环境已经搭建完成，接下来我们要对之前的工程进行改造，使用 <em>log4j2</em> 的 <em>kafka appender</em> 把日志统一输出到 <strong>kafka</strong> 日志中间件。<br>
日志导入 <strong>kafka</strong> 之后，接下来的处理就比较灵活了，可以用不同功能的消费者订阅感兴趣的 <em>topic</em>，进行日志分析。例如：使用 <em>kafka</em> 作为 <em>storm</em> 的数据来源 <em>spout</em>，进行流式处理；订阅 <em>kafka</em> 中需要做离线统计处理的 <em>topic</em>，把数据保存到数据库，一般是 <em>mongodb</em> 或 <em>hbase</em> 这种数据结构松散的 <em>nosql</em> 数据库；又或者可以使用 <em>flume</em> 或 <em>logstash</em> 这种管道工具，把数据导入到其他的系统，比如 <em>elasticsearch</em>、 <em>solr</em> 等。<br><strong>kafka</strong> 作为最常用的日志中间件，可以把分散的日志集中到一处，并做缓冲处理，再和其他开源工具进行集成，对数据做进一步处理，是日志统计系统的基础组件。</p>

<p> </p>            </div>
                </div>