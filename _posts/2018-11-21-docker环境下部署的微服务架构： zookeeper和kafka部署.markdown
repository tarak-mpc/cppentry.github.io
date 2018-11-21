---
layout:     post
title:      docker环境下部署的微服务架构： zookeeper和kafka部署
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>转载自：http://www.jianshu.com/p/263164fdcac7</p>

<p> </p>

<p>kafka简单介绍<br><br><br>
Kafka 是 LinkedIn 开源的一种高吞吐量的分布式发布订阅消息系统，kafka的诞生就是为了处理海量日志数据，所以kafka处理消息的效率非常高，即使是非常普通的硬件也可以支持每秒数百万的消息。<br>
kafka 天然支持集群负载均衡，使用 zookeeper 进行分布式协调管理。不支持事务，有一定概率丢失消息。<br>
kafka 的特点，决定了使用场景：日志中间件。<br><br><br>
下载docker镜像<br><br><br>
zookeeker: docker pull zookeeper:latest<br>
kafka: docker pull wurstmeister/kafka:latest<br><br><br>
创建并启动容器<br><br><br>
先启动zookeeper:<br><br><br>
docker run -d --name zookeeper --publish 2181:2181 \<br>
--volume /etc/localtime:/etc/localtime \<br>
zookeeper:latest<br>
zookeeper启动完成后再启动kafka:<br><br><br>
docker run -d --name kafka --publish 9092:9092 \<br>
--link zookeeper \<br>
--env KAFKA_ZOOKEEPER_CONNECT=zookeeper:2181 \<br>
--env KAFKA_ADVERTISED_HOST_NAME=kafka所在宿主机的IP \<br>
--env KAFKA_ADVERTISED_PORT=9092 \<br>
--volume /etc/localtime:/etc/localtime \<br>
wurstmeister/kafka:latest<br>
向kafka发送测试消息<br><br><br>
运行 docker ps，找到kafka的 CONTAINER ID，运行 docker exec -it ${CONTAINER ID} /bin/bash，进入kafka容器。<br>
进入kafka默认目录 /opt/kafka_2.11-0.10.1.0，运行 bin/kafka-topics.sh --create --zookeeper zookeeper:2181 --replication-factor 1 --partitions 1 --topic test，创建一个 topic 名称为 test。<br>
运行 bin/kafka-topics.sh --list --zookeeper zookeeper:2181 查看当前的 topic 列表。<br>
运行一个消息生产者，指定 topic 为刚刚创建的 test ， bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test，输入一些测试消息。<br>
运行一个消息消费者，同样指定 topic 为 test， bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning，可以接收到生产者发送的消息。<br><br><br>
最后<br><br><br>
kafka 环境已经搭建完成，接下来我们要对之前的工程进行改造，使用 log4j2 的 kafka appender 把日志统一输出到 kafka 日志中间件。<br>
日志导入 kafka 之后，接下来的处理就比较灵活了，可以用不同功能的消费者订阅感兴趣的 topic，进行日志分析。例如：使用 kafka 作为 storm 的数据来源 spout，进行流式处理；订阅 kafka 中需要做离线统计处理的 topic，把数据保存到数据库，一般是 mongodb 或 hbase 这种数据结构松散的 nosql 数据库；又或者可以使用 flume 或 logstash 这种管道工具，把数据导入到其他的系统，比如 elasticsearch、 solr 等。<br>
kafka 作为最常用的日志中间件，可以把分散的日志集中到一处，并做缓冲处理，再和其他开源工具进行集成，对数据做进一步处理，是日志统计系统的基础组件。<br>
 </p>            </div>
                </div>