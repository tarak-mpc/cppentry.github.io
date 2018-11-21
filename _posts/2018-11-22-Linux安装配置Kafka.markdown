---
layout:     post
title:      Linux安装配置Kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/mvpboss1004/article/details/52883256				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<strong>1.安装Kafka</strong><br>
下载kafka_2.10-0.8.2.2.tgz<br>
tar -zxvf kafka_2.10-0.8.2.2.tgz<br>
mv kafka_2.10-0.8.2.2/ /usr/share/kafka<br><br><strong>2.启动zookeeper<br></strong>cd /usr/share/kafka<br>
bin/zookeeper-server-start.sh config/zookeeper.properties &amp;<br>
执行jps，可以看到QuorumPeerMain<br><br><strong>3.启动broker<br></strong>bin/kafka-server-start.sh config/server.properties &amp;<br>
执行jps，可以看到Kafka<br><br><strong>4.创建单Partition、单份的topic<br></strong>bin/kafka-topics.sh --create --replication-factor 1 --partitions 1 --zookeeper localhost:2181 --topic test<br>
bin/kafka-topics.sh --describe --zookeeper localhost:2181，可以看到刚刚新建的topic<br><br><strong>5.启动一个生产者<br></strong>新建一个窗口，bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test<br><br><strong>6.启动一个消费者<br></strong>新建一个窗口，bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic test --from-beginning<br><br><strong>7.测试<br></strong>生产者窗口的输入，会在消费者窗口回显<br><br><br>            </div>
                </div>