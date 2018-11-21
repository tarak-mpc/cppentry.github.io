---
layout:     post
title:      Kafka教程（二）---------------Kafka架构初识
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1><span style="font-size:24px;"><strong>一. 消息队列</strong></span></h1>
<div><span style="font-size:18px;"><strong>1. 存在价值</strong></span></div>
<div><span style="font-size:18px;"><strong><br></strong></span></div>
<h2></h2>
<h3>
</h3><ul><li><span style="font-size:18px;"><strong>解耦</strong></span></li></ul>
<p><span style="font-size:18px;">解耦消息产出组件与接收消息组件，如果两者强关联，那么一边有修改，另一边就需要跟着修改</span></p>
<p><span style="font-size:18px;">解耦之后，消息产出组件与接收消息组件可以自己独立修改，并且还能横向扩展</span></p>
<p><span style="font-size:18px;">消息队列</span></p>
<p><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20171117092451257?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2lsdmlha2Fma2E=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" width="650" alt=""><br></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p></p>
<p style="font-size:18px;"></p>
<h3></h3>
<h3>
</h3><ul><li><span style="font-size:18px;"><strong>数据备份</strong></span></li></ul>
<p></p>
<p style="font-size:18px;">消息会持久化或者其他方式保存一段时间，<strong><span style="color:rgb(0,0,255);">两端出现任何问题不会导致数据丢失</span></strong>，两端服务在启动之后可以恢复数据</p>
<p style="font-size:18px;"><br></p>
<p style="font-size:18px;"><span style="font-family:'FangSong_GB2312';"></span></p>
<ul style="font-size:21.06px;"><li><span style="font-size:18px;"><strong>异步通信</strong></span></li></ul><p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">之前的方式，接收方必须<strong><span style="color:rgb(0,0,255);">及时接收</span></strong>消息并进行存储。引入消息系统后消息消费方可以自己控制何时去处理消息数据</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p></p>
<p style="font-size:18px;"> </p>
<h2><span style="font-size:18px;"><strong>2. 常见消息队列</strong></span></h2>
<p style="font-size:18px;"><span style="font-family:'FangSong_GB2312';">应用在分布式系统中的消息队列叫做分布式消息队列，常见的分布式消息队列有：ActiveMQ,RabbitMQ,kafka</span></p>
<p style="font-size:18px;"><br></p>
<p></p>
<h1><span style="font-size:24px;"><strong>二. Kafka shell操作</strong></span></h1>
<p></p>
<p><span style="font-family:'FangSong_GB2312';"></span></p>
<p></p>
<p><span style="font-size:18px;">首先可以通过一系列操作对kafka有个直观的认识，里面的有些概念可能不明白，后面将会详细讲解</span></p>
<p><strong><span style="font-size:18px;">注意：下面操作中zkhost、kafkahost请各位都替换为自己的ip或者hostname</span></strong></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;"><span style="color:rgb(68,68,68);">1) </span><strong><span style="color:rgb(68,68,68);">创建topic</span></strong></span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;">这个topic叫做test，只有一个分区，一个副本。</span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;"> bin/kafka-topics.sh --create --zookeeper zkhost:2181 --replication-factor 1 --partitions 1 --topic test</span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;"><span style="color:rgb(68,68,68);">2) </span><strong><span style="color:rgb(68,68,68);">查看所有topic</span></strong></span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;">bin/kafka-topics.sh --list --zookeeper zkhost:2181</span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;"><span style="color:rgb(68,68,68);">3) </span><strong><span style="color:rgb(68,68,68);">向某个发送消息</span></strong></span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;">bin/kafka-console-producer.sh --broker-list kafkahost:9092 --topic test</span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;">即可发送消息了</span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;"><img src="https://img-blog.csdn.net/20171117154917391?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2lsdmlha2Fma2E=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;"></span></p>
<p><span style="font-size:18px;"><span style="color:rgb(68,68,68);">4) </span><strong><span style="color:rgb(68,68,68);"><span style="font-family:Tahoma;">接收消息</span></span></strong></span></p>
<p><span style="font-size:18px;">bin/kafka-console-consumer.sh --zookeeper zkhost:2181 --topic test --from-beginning</span></p>
<p><img src="https://img-blog.csdn.net/20171117154928951?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2lsdmlha2Fma2E=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;"></span></p>
<p><span style="font-size:18px;"><span style="color:rgb(68,68,68);">5) </span><strong><span style="color:rgb(68,68,68);"><span style="font-family:Tahoma;">查看</span>topic详情</span></strong></span></p>
<p><span style="font-size:18px;">bin/kafka-topics.sh --describe --zookeeper zkhost:2181 --topic test</span></p>
<p><img src="https://img-blog.csdn.net/20171117154938218?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2lsdmlha2Fma2E=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p></p>
<p><span style="font-size:18px;"><span style="font-family:'FangSong_GB2312';"><span style="color:rgb(68,68,68);">6) </span><strong><span style="color:rgb(68,68,68);">删除topic</span></strong></span></span></p>
<p><span style="font-size:18px;"><span style="font-family:'FangSong_GB2312';"> ./bin/kafka-topics  --delete --zookeeper  zkhost:2181  --topic test</span></span></p>
<p><span style="font-size:18px;"><span style="font-family:'FangSong_GB2312';">前提是server.properties中：  delete.topic.enable=true</span></span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;"><span style="color:rgb(68,68,68);">7) </span><strong><span style="color:rgb(68,68,68);">查询topic的offset的范围</span></strong></span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;">查询topic名字为test的offset的最小值：</span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;">bin/kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list kafkahost:9092 -topic test --time -2</span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;">输出</span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;">test:0:1288</span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;">查询offset的最大值：</span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;">bin/kafka-run-class.sh kafka.tools.GetOffsetShell --broker-list kafkahost:9092 -topic test --time -1</span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;">输出</span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;">test:0:7885</span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;">从上面的输出可以看出topic:test只有一个partition:0 offset范围为:[1288,7885]</span></p>
<span style="font-family:'FangSong_GB2312';font-size:18px;"><br></span>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;"></span></p>
<h1><span style="font-size:24px;"><strong>三. Kafka中的重要概念</strong></span></h1>
<p></p>
<p></p>
<ul><li><span style="font-family:'FangSong_GB2312';font-size:18px;"><strong>Broker</strong></span></li></ul><p></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;">Kafka是分布式的，在kafka集群中，每一个<strong>kafka服务进程</strong>都可以称为一个<strong>broker</strong></span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;">（可以和第一节的broker id进行对比）</span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;"> </span></p>
<p></p>
<ul><li><span style="font-family:'FangSong_GB2312';font-size:18px;"><strong>Topic</strong></span></li></ul><p></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;">也就是消息的名字，是业务上对消息的分组与区分。比如：告警的消息队列、错误信息的消息队列、问候信息的消息队列。</span></p>
<p><strong><span style="font-family:'FangSong_GB2312';font-size:18px;"> </span></strong></p>
<p></p>
<ul><li><span style="font-family:'FangSong_GB2312';font-size:18px;"><strong> Partition</strong></span></li></ul><p></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;">对于同一个topic，可以分为多个partition，比如topic1分为3个partition，partition0~partition2。相当于把<strong><span style="color:rgb(0,0,255);">一个整体分为3个部分</span></strong>，这样就实现了负载均衡</span></p>
<p><img src="https://img-blog.csdn.net/20171117155211672?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2lsdmlha2Fma2E=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" width="700" alt=""></p>
<p></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;">所以可以看到，每个partition上的数据虽然不一定相邻，但一定是<strong>有序的</strong></span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;">至于每一条数据<strong>如何分配到不同的broker</strong>的，这个后一节实现细节中会详细讲解</span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:18px;"></span></p>
<p><strong><br></strong></p>
<p></p>
<ul><li><span style="font-size:18px;"><strong>replication</strong></span></li></ul><p></p>
<p>为了保证数据的容错性，通常会设置replication。这个就是<strong>数据备份</strong>的功能。</p>
<p>比如说设置为replication=2，那么每个partition都会进行备份</p>
<img src="https://img-blog.csdn.net/20171117155341696?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2lsdmlha2Fma2E=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" width="700"><br><br><br>            </div>
                </div>