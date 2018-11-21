---
layout:     post
title:      Flume与Kafka整合之把flume中的数据sink到Kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/liubiaoxin/article/details/49231731				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>一、说明</p>
<p><span></span>版本介绍：apache-flume-1.6.0-bin + kafka_2.10-0.8.2.0</p>
<p><span></span>场景说明：把flume中的数据sink到Kafka</p>
<p><span></span>集群说明：</p>
<p></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span></span>192.168.215.90     broker、consumer、zookeeper 、flume<br></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span></span>192.168.215.110   broker<span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">、</span><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">zookeeper</span><br></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span></span>192.168.215.120   broker<span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">、</span><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">zookeeper</span></p>
<br><p>二、分别到三个节点启动zookepper节点</p>
<p><span></span>zkServer.sh  start</p>
<p><span></span>使用命令zkServer.sh  status检查zookepper节点启动情况<br></p>
<p><br></p>
<p>三、下载整合插件JAR包 flumeng-kafka-plugin.jar</p>
<p><span></span>下载地址--http://yunpan.cn/cF33J42EsRaDH （提取码：23bf）</p>
<p><br></p>
<p>四、启动Kafka集群</p>
<p><span></span>   三个节点执行命令，启动kafka服务    <span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">bin/kafka-server-start.sh config/server.properties &gt;/dev/null 2&gt;&amp;1 &amp;</span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;"><span></span>在90节点上执行：</span></span></p>
<p><span style="font-family:Arial;"><span style="font-size:14px;line-height:26px;"></span></span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span></span>创建消息主题topic     bin/kafka-topics.sh --create --zookeeper 192.168.215.90:2181 --replication-factor 2 --partitions 4 --topic <span style="color:rgb(255,0,0);">test</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span></span>新开窗口，创建消费者consumer      bin/kafka-console-consumer.sh --zookeeper 192.168.215.90:2181 --topic  <span style="color:rgb(255,0,0);">test  </span>--from-beginning</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
五、配置flume</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span></span>cd /usr/local/apache-flume-1.5.2-bin</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span></span>vi conf/producer.conf ，加入如下内容：</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span><img src="https://img-blog.csdn.net/20151018220121287?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span><span></span>具体</span>配置，如下所示：</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span>#agent<br>
producer.sources = s<br>
producer.channels = c<br>
producer.sinks = r<br>
#source<br>
producer.sources.s.type = spooldir<br>
producer.sources.s.channels = c<br>
producer.sources.s.spoolDir = /root/hmbbs<br>
producer.sources.s.fileHeader = true<br><br>
producer.sinks.r.type = org.apache.flume.plugins.KafkaSink<br>
producer.sinks.r.metadata.broker.list=192.168.215.90:9092<br>
producer.sinks.r.partitioner.class=org.apache.flume.plugins.SinglePartition<br>
producer.sinks.r.partition.key=1<br>
producer.sinks.r.serializer.class=kafka.serializer.StringEncoder<br>
producer.sinks.r.request.required.acks=0<br>
producer.sinks.r.max.message.size=1000000<br>
producer.sinks.r.producer.type=async<br>
producer.sinks.r.custom.encoding=UTF-8<br>
producer.sinks.r.custom.topic.name=test<br></span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span>#Specify the channel for the sink<br>
producer.sinks.r.channel = c<br>
producer.channels.c.type = memory<br>
producer.channels.c.capacity = 1000</span></p>
<p style="font-family:Arial;font-size:14px;line-height:26px;"><span style="color:#cc0000;">附注：需要在/root目录下创建hmbbs目录</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">六、启动agent服务</span><br></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span></span>在flume的home目录下执行命令   flume-ng agent -n producer conf -f conf/producer.conf -Dflume.root.logger=DEBUG,console<br></span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
七、检验</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span></span>在hmbbs中新建一个文件，并输入“this is  a  test”，在90节点的消费者窗口可见<span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">“this is  a  test”，说明整合成功！</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><br></span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
八、接收流数据</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
在flume目录下修改<span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">producer.conf文件即可</span>，修改如下：</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">vi conf/producer.conf</span><br></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<img src="https://img-blog.csdn.net/20151018223603147?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<br></p>
            </div>
                </div>