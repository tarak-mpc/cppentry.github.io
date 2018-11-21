---
layout:     post
title:      linux环境下安装kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>linux环境下安装kafka</p>

<p> </p>

<p>Kafka是一个分布式的、可分区的、可复制的消息系统。它提供了普通消息系统的功能，但具有自己独特的设计</p>

<p> </p>

<p>基本的消息系统术语：</p>

<p>•Kafka将消息以topic为单位进行归纳。</p>

<p>•将向Kafka topic发布消息的程序成为producers.</p>

<p>•将预订topics并消费消息的程序成为consumer.</p>

<p>•Kafka以集群的方式运行，可以由一个或多个服务组成，每个服务叫做一个broker.</p>

<p>producers通过网络将消息发送到Kafka集群，集群向消费者提供消息。客户端和服务端通过TCP协议通信。Kafka提供了Java客户端，并且对多种语言都提供了支持。</p>

<p><img alt="" class="has" height="179" src="https://img-blog.csdn.net/20180730165306165?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3h1ZWppbnlhbjEyMw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="340"></p>

<p></p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p>Kafka的安装需要java环境，cent os 7自带java1.6版本，可以不用重新安装</p>

<p>准备工作：</p>

<p>Kafka集群是把状态保存在Zookeeper中的，首先要搭建Zookeeper集群</p>

<p> </p>

<p>1、下载软</p>

<p>zookeeper压缩包、kafka压缩包</p>

<p>#下载ZooKeeper：<a href="http://pan.baidu.com/s/1pJlwbR9" rel="nofollow">http://pan.baidu.com/s/1pJlwbR9</a></p>

<p>wget  <a href="http://apache.opencas.org/kafka/0.9.0.1/kafka_2.10-0.10.1.0.tg" rel="nofollow">http://apache.opencas.org/kafka/0.9.0.1/kafka_2.10-0.10.1.0.tg</a>z</p>

<p> </p>

<p>#这里没有用到zookeeper软件包</p>

<p> </p>

<p>2、解压</p>

<p>#tar -zxvf zookeeper-3.4.5.tar.gz</p>

<p>tar -zxvf kafka_2.10-0.10.1.0.tgz</p>

<p> </p>

<p>#3、修改配置文件</p>

<p>#zookeeper配置相关文件</p>

<p>#<a href="https://www.cnblogs.com/mmzs/p/8072264.html" rel="nofollow">https://www.cnblogs.com/mmzs/p/8072264.html</a></p>

<p> </p>

<p>4、相关配置修改</p>

<p>（1）修改zookeeper相关配置</p>

<p>cd /kafka/config</p>

<p>vi zookeeper.properties</p>

<p>dataDir=/data1/xjy/kafka/zookeeper_data （zookeeper_data存放信息文件）</p>

<p>（2）修改kafka相关配置</p>

<p>cd /kafka/config</p>

<p>vi server.properties</p>

<p>listeners=PLAINTEXT://192.168.92.111:9092</p>

<p>advertised.listeners=PLAINTEXT://192.168.92.111:9092</p>

<p>log.dirs=/data1/xjy/kafka/logs</p>

<p> </p>

<p> </p>

<p>5、</p>

<p>启动zookeeper</p>

<p>bin/zookeeper-server-start.sh config/zookeeper.properties &amp;</p>

<p> </p>

<p>启动kafka</p>

<p>bin/kafka-server-start.sh config/server.properties &amp;</p>

<p> </p>

<p>关闭kafka</p>

<p>bin/kafka-server-stop.sh config/server.properties &amp;</p>

<p> </p>

<p>kafka创建topic</p>

<p>bin/kafka-topics.sh --create --zookeeper 192.168.92.111:2181 --replication-factor 1 --partitions 1 --topic test</p>

<p> </p>

<p>查看topic</p>

<p>bin/kafka-topics.sh --list --zookeeper 192.168.92.111:2181</p>

<p> </p>

<p>ksfka删除topic</p>

<p>bin/kafka-topics.sh --delete --zookeeper 192.168.92.111:2181 --topic test</p>

<p> </p>

<p>运行 producer：</p>

<p>bin/kafka-console-producer.sh --broker-list localhost:9092 --topic appreportdata_800021</p>

<p> </p>

<p>运行 consumer：</p>

<p>bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic appreportdata_800021 --from-beginning</p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p></p>            </div>
                </div>