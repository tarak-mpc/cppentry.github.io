---
layout:     post
title:      Kafka系列1----Kafka安装与相关命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u013983450/article/details/53507225				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div><strong><span style="font-size:15px;"><span><br></span></span></strong></div>
<div><span style="font-size:24px;"><span style="line-height:21px;"><strong>单节点KAFKA安装</strong></span></span></div>
<div><span><strong><span style="font-size:15px;">解压Kafka</span></strong></span></div>
<div><strong><span style="font-size:15px;"><br></span></strong></div>
<div><strong><span>运行zookeeper（必须）</span></strong></div>
<div>运行zookeeper</div>
<div><br></div>
<div><br></div>
<div><strong><span>修改kafka配置文件</span></strong></div>
<div><br></div>
<div style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;color:rgb(51,51,51);background-color:rgb(251,250,248);">
<div>broker.id=0</div>
<div>port=9092</div>
<div>log.dir=/tmp/kafka-logs-0</div>
<div>zookeeper.connect=192.168.60.81:2181,192.168.60.50:2181,192.168.60.82:2181</div>
</div>
<div><br></div>
<div><br></div>
<div>运行 kafka（必须）</div>
<div><br></div>
<div style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;color:rgb(51,51,51);background-color:rgb(251,250,248);">
./kafka-server-start.sh  ../config/server.properties</div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><span style="font-size:24px;"><strong>KAFKA集群安装</strong></span></div>
<div><strong><span><br></span></strong></div>
<div><span><strong>安装kafka集群</strong></span></div>
<div><br></div>
<div style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;color:rgb(51,51,51);background-color:rgb(251,250,248);">
<div>cp config/server.properties config/server-1.properties</div>
<div>cp config/server.properties config/server-2.properties</div>
</div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><span><strong>server-1.properties 修改如下内容</strong></span></div>
<div><br></div>
<div style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;color:rgb(51,51,51);background-color:rgb(251,250,248);">
<div>broker.id=1</div>
<div>port=9093</div>
<div>log.dir=/tmp/kafka-logs-1</div>
</div>
<div><br></div>
<div><span><strong>server-2.properties 修改如下内容</strong></span></div>
<div><br></div>
<div style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;color:rgb(51,51,51);background-color:rgb(251,250,248);">
<div>broker.id=2</div>
<div>port=9094</div>
<div>log.dir=/tmp/kafka-logs-2</div>
</div>
<div><br></div>
<div><strong><span>启动节点</span></strong></div>
<div><br></div>
<div style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;color:rgb(51,51,51);background-color:rgb(251,250,248);">
<div>kafka-server-start.sh config/server-1.properties &amp;</div>
<div>kafka-server-start.sh config/server-2.properties &amp;</div>
</div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><span style="font-size:19px;">测试集群环境中kafka副本与分区</span></div>
<div><br></div>
<div><strong><span>创建拥有三个副本的topic</span></strong></div>
<div><br></div>
<div style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;color:rgb(51,51,51);background-color:rgb(251,250,248);">
kafka-topics.sh --create --zookeeper 192.168.60.81:2181,192.168.60.50:2181,192.168.60.82:2181 --replication-factor 3 --partitions 3 --topic my-replicated</div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><span><strong>查看每个节点的信息</strong></span></div>
<div><br></div>
<div style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;color:rgb(51,51,51);background-color:rgb(251,250,248);">
<div>kafka-topics.sh --describe --zookeeper 192.168.60.81:2181,192.168.60.50:2181,192.168.60.82:2181:2181 --topic my-replicated</div>
<div><br></div>
<div>    Topic: my-replicated    Partition: 0    Leader: 2    Replicas: 2,0,1    Isr: 2,0,1</div>
<div>    Topic: my-replicated    Partition: 1    Leader: 0    Replicas: 0,1,2    Isr: 0,1,2</div>
<div>    Topic: my-replicated    Partition: 2    Leader: 1    Replicas: 1,2,0    Isr: 1,2,0</div>
<div>leader：负责处理消息的读和写，leader是从所有节点中随机选择的.</div>
<div>replicas：列出了所有的副本节点，不管节点是否在服务中.</div>
<div>isr：是正在服务中的节点.</div>
</div>
<div><br></div>
<div><strong><span>副本功能测试</span></strong></div>
<div>我们kill 掉.Broker 0 作为lead运行</div>
<div><br></div>
<div>另外一个节点被选做了leader,虽然最初负责续写消息的leader down掉了，但之前的消息还是可以消费的.</div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><span style="font-size:24px;"><strong>Kafka的相关操作</strong></span></div>
<div><span>创建topic</span></div>
<div><br></div>
<div style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;color:rgb(51,51,51);background-color:rgb(251,250,248);">
<div>./kafka-topics.sh --create  --zookeeper  192.168.60.81:2181,192.168.60.50:2181,192.168.60.82:2181 --replication-factor 1 --partitions 1 --topic gj</div>
<div><br></div>
<div>./kafka-topics.sh --create  --zookeeper  192.168.60.81:2181,192.168.60.50:2181,192.168.60.82:2181 --replication-factor 1 --partitions 1 --topic gj</div>
</div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><span>查看目前拥有的topic</span></div>
<div><br></div>
<div style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;color:rgb(51,51,51);background-color:rgb(251,250,248);">
<div>./kafka-topics.sh --list --zookeeper 192.168.60.81:2181,192.168.60.50:2181,192.168.60.82:2181</div>
<div><br></div>
<div>./kafka-topics.sh --describe --zookeeper 192.168.60.81:2181,192.168.60.50:2181,192.168.60.82:2181 --topic gj</div>
</div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><span>删除topic</span></div>
<div><br></div>
<div style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;color:rgb(51,51,51);background-color:rgb(251,250,248);">
<div>删除topic需要在配置文件加入   delete.topic.enable=true  否则只是对kafka的topic增加了delete标记,不会真正删除.</div>
<div>./kafka-topics.sh --delete --topic gj --zookeeper 192.168.60.81:2181,192.168.60.50:2181,192.168.60.82:2181</div>
</div>
<div><br></div>
<div><br></div>
<div><span>生产消息：</span></div>
<div><br></div>
<div style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;color:rgb(51,51,51);background-color:rgb(251,250,248);">
./kafka-console-producer.sh --broker-list 192.168.60.21:9092 --topic gj</div>
<div><br></div>
<div><br></div>
<div><span>消费消息：</span></div>
<div><br></div>
<div style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;color:rgb(51,51,51);background-color:rgb(251,250,248);">
./kafka-console-consumer.sh --zookeeper 192.168.60.81:2181,192.168.60.50:2181,192.168.60.82:2181 --topic gujian --from beginning</div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
            </div>
                </div>