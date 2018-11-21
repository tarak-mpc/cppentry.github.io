---
layout:     post
title:      RabbiMQ 和 Kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';">
<span style="font-size:14px;">Kafka</span></p>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';">
<span style="font-size:14px;"></span></p>
<h2 style="font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-weight:100;line-height:1.1;color:rgb(69,69,69);font-size:2.15em;">
基础概念</h2>
<p></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">
</p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;border:0px;vertical-align:baseline;line-height:1.5em;">
Producer：消息生产者，负责发布消息到 Kafka broker<br>
Consumer：消息消费者，向 Kafka broker 读取消息<br>
Broker：Kafka 集群包含一个或多个服务器，这种服务器被称为 broker。一个broker上可能有很多topic的partition。<br>
Topic：每条发布到 Kafka 集群的消息都有一个类别，这个类别被称为 Topic。<span style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;">一个topic可以分为多个partition，每个partition是一个有序的队列。partition中的每条消息都会被分配一个有序的id（offset）。kafka只保证按一个partition中的顺序将消息发给consumer，不保证一个topic的整体（多个partition间）的顺序。</span><br>
Partition：分区，每个 Topic 包含一个或多个 Partition。</p>
<br><p style="color:rgb(85,85,85);font-family:'microsoft yahei';">
<span style="font-size:14px;"></span></p>
<h2 style="font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-weight:100;line-height:1.1;color:rgb(69,69,69);font-size:2.15em;">
架构图</h2>
<p></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;border:0px;vertical-align:baseline;line-height:1.5em;">
<img src="https://img-blog.csdn.net/20161103201451499?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:0px;vertical-align:middle;"></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;border:0px;vertical-align:baseline;line-height:1.5em;">
从网上找到的一个比对表格</p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;border:0px;vertical-align:baseline;line-height:1.5em;">
<span style="color:rgb(0,0,102);font-family:Arial, Verdana, sans-serif;font-size:14px;font-weight:700;"></span>
</p><table border="1" cellspacing="1" cellpadding="1" style="border-color:rgb(192,192,192);color:rgb(0,0,102);font-family:verdana, arial, helvetica, sans-serif;font-size:13px;"><tbody><tr><th width="100" style="border-color:rgb(192,192,192);border-collapse:collapse;">
 </th>
<th width="100" style="border-color:rgb(192,192,192);border-collapse:collapse;">
ActiveMq</th>
<th width="150" style="border-color:rgb(192,192,192);border-collapse:collapse;">
RabbitMq</th>
<th width="150" style="border-color:rgb(192,192,192);border-collapse:collapse;">
Kafka</th>
</tr><tr><td width="100" style="border-color:rgb(192,192,192);border-collapse:collapse;">
<strong>producer容错，是否会丢数据</strong></td>
<td width="150" style="border-color:rgb(192,192,192);border-collapse:collapse;">
 </td>
<td width="150" style="border-color:rgb(192,192,192);border-collapse:collapse;">
有ack模型，也有事务模型，保证至少不会丢数据。ack模型可能会有重复消息，事务模型则保证完全一致</td>
<td width="150" style="border-color:rgb(192,192,192);border-collapse:collapse;">
批量形式下，可能会丢数据。 非批量形式下， 1. 使用同步模式，可能会有重复数据。 2. 异步模式，则可能会丢数据。</td>
</tr><tr><td width="100" style="border-color:rgb(192,192,192);border-collapse:collapse;">
<strong>consumer容错，是否会丢数据</strong></td>
<td width="150" style="border-color:rgb(192,192,192);border-collapse:collapse;">
 </td>
<td width="150" style="border-color:rgb(192,192,192);border-collapse:collapse;">
有ack模型，数据不会丢，但可能会重复处理数据。</td>
<td width="150" style="border-color:rgb(192,192,192);border-collapse:collapse;">
批量形式下，可能会丢数据。非批量形式下，可能会重复处理数据。（ZK写offset是异步的）</td>
</tr><tr><td width="100" style="border-color:rgb(192,192,192);border-collapse:collapse;">
<strong>架构模型</strong></td>
<td width="150" style="border-color:rgb(192,192,192);border-collapse:collapse;">
基于JMS协议</td>
<td width="150" style="border-color:rgb(192,192,192);border-collapse:collapse;">
基于AMQP模型，比较成熟，但更新超慢。RabbitMQ的broker由Exchange,Binding,queue组成，其中exchange和binding组成了消息的路由键；客户端Producer通过连接channel和server进行通信，Consumer从queue获取消息进行消费（长连接，queue有消息会推送到consumer端，consumer循环从输入流读取数据）。rabbitMQ以broker为中心；有消息的确认机制</td>
<td width="150" style="border-color:rgb(192,192,192);border-collapse:collapse;">
producer，broker，consumer，以consumer为中心，消息的消费信息保存的客户端consumer上，consumer根据消费的点，从broker上批量pull数据；无消息确认机制。</td>
</tr><tr><td width="100" style="border-color:rgb(192,192,192);border-collapse:collapse;">
<strong>吞吐量</strong></td>
<td width="150" style="border-color:rgb(192,192,192);border-collapse:collapse;">
 </td>
<td width="150" style="border-color:rgb(192,192,192);border-collapse:collapse;">
rabbitMQ在吞吐量方面稍逊于kafka，他们的出发点不一样，rabbitMQ支持对消息的可靠的传递，支持事务，不支持批量的操作；基于存储的可靠性的要求存储可以采用内存或者硬盘。</td>
<td width="150" style="border-color:rgb(192,192,192);border-collapse:collapse;">
kafka具有高的吞吐量，内部采用消息的批量处理，zero-copy机制，数据的存储和获取是本地磁盘顺序批量操作，具有O(1)的复杂度，消息处理的效率很高</td>
</tr><tr><td width="100" style="border-color:rgb(192,192,192);border-collapse:collapse;">
<strong>可用性</strong></td>
<td width="150" style="border-color:rgb(192,192,192);border-collapse:collapse;">
 </td>
<td width="150" style="border-color:rgb(192,192,192);border-collapse:collapse;">
rabbitMQ支持miror的queue，主queue失效，miror queue接管</td>
<td width="150" style="border-color:rgb(192,192,192);border-collapse:collapse;">
kafka的broker支持主备模式</td>
</tr><tr><td width="100" style="border-color:rgb(192,192,192);border-collapse:collapse;">
<strong>集群负载均衡</strong></td>
<td width="150" style="border-color:rgb(192,192,192);border-collapse:collapse;">
 </td>
<td width="150" style="border-color:rgb(192,192,192);border-collapse:collapse;">
rabbitMQ的负载均衡需要单独的loadbalancer进行支持</td>
<td width="150" style="border-color:rgb(192,192,192);border-collapse:collapse;">
kafka采用zookeeper对集群中的broker、consumer进行管理，可以注册topic到zookeeper上；通过zookeeper的协调机制，producer保存对应topic的broker信息，可以随机或者轮询发送到broker上；并且producer可以基于语义指定分片，消息发送到broker的某分片上</td>
</tr></tbody></table><p style="color:rgb(0,0,102);font-family:verdana, arial, helvetica, sans-serif;font-size:13px;">
参考：<a href="http://www.liaoqiqi.com/post/227" rel="nofollow" style="background:transparent;color:rgb(0,107,173);">http://www.liaoqiqi.com/post/227</a></p>
<br><br>            </div>
                </div>