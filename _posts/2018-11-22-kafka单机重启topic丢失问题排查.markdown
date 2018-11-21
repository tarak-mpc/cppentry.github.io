---
layout:     post
title:      kafka单机重启topic丢失问题排查
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/zfszhangyuan/article/details/53389916				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>这个问题，在线上集群环境一般不容易出现，因为相关的日志文件参数都已经配置好了，而且经受住时间的的验证了。</p>
<p>作为新手，我在本地配置了一个单机kafka，用得是kafka自带的zookeeper服务。</p>
<p>kafka安装很简单如下：</p>
<p>1).下载kafka:<br><br><br>
wget http://apache.fayea.com/kafka/0.10.1.0/kafka_2.10-0.10.1.0.tgz<br>
2) 解压：<br><br><br>
tar -zxf kafka_2.10-0.10.1.0.tgz<br>
3）启动zookeeper，kafka<br><br><br><strong>&gt; bin/zookeeper-server-start.sh config/zookeeper.properties &amp;</strong><br>
[2013-04-22 15:01:37,495] INFO Reading configuration from: config/zookeeper.properties (org.apache.zookeeper.server.quorum.QuorumPeerConfig)<br>
...<br><br><br>
现在启动Kafka:<br><strong>&gt; bin/kafka-server-start.sh config/server.properties &amp;</strong><br>
[2013-04-22 15:01:47,028] INFO Verifying properties (kafka.utils.VerifiableProperties)<br>
[2013-04-22 15:01:47,051] INFO Property socket.send.buffer.bytes is overridden to 1048576 (kafka.utils.VerifiableProperties)<br>
...<br>
备注：要挂到后台使用：<br><br><br>
4)新建一个TOPIC<br><strong>&gt; bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 3 --partitions 1 --topic my-replicated-topic</strong><br><br><br>
5)查看创建的topic list<br><strong>&gt; bin/kafka-topics.sh --list --zookeeper localhost:2181</strong><br><br><br>
6)查看topic详情<br><strong>&gt; bin/kafka-topics.sh --describe --zookeeper localhost:2181 --topic my-replicated-topic</strong><br></p>
<p><br></p>
<p>这个时候我们给topic添加一点数据：</p>
<p><strong>&gt; bin/kafka-console-producer.sh --broker-list localhost:9092 --topic my-replicated-topic</strong></p>
<p>重启电脑后 启动zookeeper kafka后发现topic没了</p>
<p>这里简略一点：有两个地方需要配置一下 第一个是zookeeper的数据目录 和日志目录（主要是数据目录），另外一个就是kafka本事的server.properties</p>
<p><strong><span style="font-size:14px;">1.配置zookeeper的把目录从tmp目录修改成自定义目录</span></strong></p>
<p><img src="https://img-blog.csdn.net/20161129110610057?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p><strong><span style="font-size:14px;">2.kafka的日志目录重新配置不要放在tmp下面 ，另外设置一下数据 flush策略，保证数据已经flush到磁盘上</span></strong></p>
<p><img src="https://img-blog.csdn.net/20161129110743802?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>下面是flush策略的修改</p>
<p><img src="https://img-blog.csdn.net/20161129110759240?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p><br></p>
<p>本文对其他配置没有做过多介绍，只是把kafka数据落地的配置罗列了一下。</p>
<p>配置好，重启，问题解决。<br></p>
<div style="text-align:justify;line-height:1.6;font-size:14px;">
<br></div>
<p></p>
            </div>
                </div>