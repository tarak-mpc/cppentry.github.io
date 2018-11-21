---
layout:     post
title:      centos7安装kafka2.11
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <pre>
文件说明

    kafka.properties                kafka配置
    KafkaConsumerUtil.java          kafka消费者
    KafkaConsumerService.java       kafka service接口，需要实现
    KafkaProducerUtil.java          kafka生产者（demo）
    KafkaProperties.java            kafka配置文件
    KafkaUtil.java                  kafka工具类（包含发送消息（生产者）），(需注解扫描到)启动消费者线程

maven

    &lt;dependency&gt;
        &lt;groupId&gt;org.apache.kafka&lt;/groupId&gt;
        &lt;artifactId&gt;kafka-clients&lt;/artifactId&gt;
        &lt;version&gt;2.0.0&lt;/version&gt;
    &lt;/dependency&gt;
    
1、下载kafka，解压并进入kafka目录

2、启动服务器

    Kafka需要用zookeeper，所以你首先需要启动一个zookeeper服务器，如果您没有， 您可以使用方便脚本打包与kafka卡应急的单节点管理员实例
    
    bin/zookeeper-server-start.sh config/zookeeper.properties &amp;

3、修改kafka配置/config/server.properties

    设置外网可以访问：advertised.listeners=PLAINTEXT://ip地址:9092

4、启动kafka服务器
    
    bin/kafka-server-start.sh config/server.properties &amp;
    
5、创建一个topic
    让我们创建一个名为“test”的topic 与单个分区只有一个副本

    bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1     --partitions 1 --topic test

    我们现在可以看到topic ，如果我们运行topic 列表命令:
    
    bin/kafka-topics.sh --list --zookeeper localhost:2181
    
6、发送消息（生产者）

    bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test

7、创建一个消费者
    
    bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning
          </pre>            </div>
                </div>