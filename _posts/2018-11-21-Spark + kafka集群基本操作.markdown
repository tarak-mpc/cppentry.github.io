---
layout:     post
title:      Spark + kafka集群基本操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>Spark + kafka集群：kafka集群操作过程</p>
<p>启动Zookeeper服务：<br>
bin/zookeeper-server-start.sh config/zookeeper.properties &amp;<br>
启动kafka集群：<br>
./bin/kafka-server-start.sh -daemon conf/server.properties<br><br><br>
kafka使用测试：<br>
1、创建topic：<br>
bin$:./kafka-topics.sh --create --zookeeper 172.22.109.132:2181 --replication-factor 3 --partitions 1 --topic test<br><br><br>
2、查看topic:<br>
bin$:./kafka-topics.sh --describe --zookeeper 172.22.109.132:2181<br><br><br>
3、生产者测试:<br>
bin$:./kafka-console-producer.sh --broker-list 172.22.109.132:9092 --topic 【topic名字】<br>
输入<br>
输入<br>
。。。。<br><br><br>
4、消费者测试：<br>
bin$:./kafka-console-consumer.sh --zookeeper 172.22.109.132:2181 --from-beginning --topic test<br>
接收输入<br>
接收输入<br>
。。。<br><br><br><br><br>
在搭建过程中出现的拒绝连接原因：<br>
在zookeeper配置文件中的地址中写错了 172.22.109.134 ==》172.22.109.135<br></p>
            </div>
                </div>