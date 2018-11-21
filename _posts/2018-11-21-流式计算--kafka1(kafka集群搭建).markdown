---
layout:     post
title:      流式计算--kafka1(kafka集群搭建)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h2 style="margin-left:0cm;">1、Kafka是什么</h2>

<p>        在流式计算中，Kafka一般用来缓存数据，Storm通过消费Kafka的数据进行计算。KAFKA + STORM +REDIS</p>

<ol><li><strong>Kafka</strong><strong>是一个分布式消息队列：生产者、消费者的功能。它提供了<span style="color:#ff0000;">类似于</span>JMS</strong><strong>的特性，<span style="color:#ff0000;">但是在设计实现上完全不同，此外它并不是</span><span style="color:#ff0000;">JMS规范的实现</span>。Kafka</strong>由Scala写成。</li>
	<li>Kafka对消息保存时根据Topic进行归类，发送消息者称为Producer,消息接受者称为Consumer,此外kafka集群有多个kafka实例组成，每个实例(server)成为broker。</li>
	<li>无论是kafka集群，还是producer和consumer都依赖于<strong><span style="color:#ff0000;">zookeeper</span></strong>集群保存一些meta信息，来保证系统可用性</li>
</ol><h2 style="margin-left:0cm;">2、JMS是什么</h2>

<p>    JMS是Java提供的一套技术规范，生产消费者模式（生产者、服务器、消费者）</p>

<p>    用来异构系统 集成通信，缓解系统瓶颈，提高系统的伸缩性增强系统用户体验，使得系统模块化和组件化变得可行并更加灵活    </p>

<p>    JMS消息传输模型：</p>

<ol><li>点对点模式<strong>（一对一，消费者主动<span style="color:#ff0000;">拉取</span>数据，消息收到后消息清除）</strong>点对点模型通常是一个基于拉取或者轮询的消息传送模型，这种模型从队列中请求信息，而不是将消息推送到客户端。这个模型的特点是发送到队列的消息被<strong><span style="color:#ff0000;">一个且只有一个接收者接收处理</span></strong>，即使有多个消息监听者也是如此。</li>
	<li>发布/订阅模式<strong>（一对多，数据生产后，<span style="color:#ff0000;">推送</span>给所有订阅者）</strong>发布订阅模型则是一个基于推送的消息传送模型。发布订阅模型可以有多种不同的订阅者，临时订阅者只在主动监听主题时才接收消息，而持久订阅者则监听主题的所有消息，<strong><span style="color:#ff0000;">即时当前订阅者不可用，处于离线状态</span></strong>。</li>
</ol><p style="margin-left:0cm;">     JMS核心组件：</p>

<ol><li>Destination：消息发送的目的地，也就是前面说的Queue和Topic。</li>
	<li><a>Message </a>：从字面上就可以看出是被发送的消息。</li>
	<li>
	<p style="margin-left:0cm;">Producer： 消息的生产者，要发送一个消息，必须通过这个生产者来发送。</p>
	</li>
	<li>MessageConsumer： 与生产者相对应，这是消息的消费者或接收者，通过它来接收一个消息。</li>
</ol><h3 style="margin-left:0cm;">3.常见的类JMS消息服务器</h3>

<p>     <img alt="" class="has" height="268" src="https://img-blog.csdnimg.cn/20181117161900596.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzY1MDQ1OA==,size_16,color_FFFFFF,t_70" width="573"></p>

<p> JMS消息服务器ActiveMQ（他支持事务，rocketMq也支持）主要特点： </p>

<ol><li>多种语言和协议编写客户端。语言: Java, C, C++, C#, Ruby, Perl, Python, PHP。应用协议: OpenWire,Stomp REST,WS Notification,XMPP,<span style="color:#ff0000;">AMQP</span>（高级消息队列协议）</li>
	<li>完全支持JMS1.1和J2EE 1.4规范 (持久化,XA消息,<span style="color:#ff0000;">事务</span>)</li>
	<li>对Spring的支持,ActiveMQ可以很容易内嵌到使用Spring的系统里面去,而且也支持Spring2.0的特性</li>
	<li>通过了常见J2EE服务器(如 Geronimo,JBoss 4, GlassFish,WebLogic)的测试,其中通过JCA 1.5 resource adaptors的配置,可以让ActiveMQ可以自动的部署到任何兼容J2EE 1.4 商业服务器上</li>
	<li>支持多种传送协议:in-VM,TCP,SSL,NIO,UDP,JGroups,JXTA</li>
	<li>支持通过JDBC和journal提供高速的消息持久化</li>
	<li>从设计上保证了高性能的集群,客户端-服务器,点对点</li>
	<li>支持Ajax</li>
	<li>支持与Axis的整合</li>
	<li>可以很容易得调用内嵌JMS provider,进行测试</li>
</ol><p> 分布式消息中间件 RocketMQ（分布式，亿级消息堆积能力）：</p>

<ol><li>能够保证严格的消息顺序</li>
	<li>提供丰富的消息拉取模式</li>
	<li>高效的订阅者水平扩展能力</li>
	<li>实时的消息订阅机制</li>
	<li><span style="color:#ff0000;">亿级消息堆积能力</span></li>
	<li><strong>Metaq3.0 </strong><strong>版本改名，产品名称改为RocketMQ</strong></li>
</ol><p>   kafka的主要的特点是<span style="color:#ff0000;">为了高吞吐量，支持动态扩容</span></p>

<p>  为什么需要消息队列：<span style="color:#ff0000;">解耦，异步和并行</span></p>

<h1><span style="color:#ff0000;">  </span><span style="color:#000000;">4.Kafka核心组件</span></h1>

<ol><li>Topic ：消息根据Topic进行归类</li>
	<li>Producer：发送消息者</li>
	<li>Consumer：消息接受者</li>
	<li>broker：每个kafka实例(server)</li>
	<li>Zookeeper：依赖集群保存meta信息。</li>
</ol><h1>  5.kafka集群搭建</h1>

<p>      首先本篇博客基于<a href="https://blog.csdn.net/weixin_37650458/article/details/84112280" rel="nofollow">Storm集群</a>上搭建kafka集群，所以机器的配置和Storm的一样</p>

<p>      所以首先起来zk：</p>

<p>      <img alt="" class="has" height="225" src="https://img-blog.csdnimg.cn/20181117163728410.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzY1MDQ1OA==,size_16,color_FFFFFF,t_70" width="1200"></p>

<p>   1.下载安装包    </p>

<p style="margin-left:0cm;">    <a href="http://kafka.apache.org/downloads.html" rel="nofollow">http://kafka.apache.org/downloads.html</a></p>

<p style="margin-left:0cm;">    在linux中使用wget命令下载安装包</p>

<p style="margin-left:0cm;">         Sudo yum -y install wget</p>

<p style="margin-left:0cm;">         wget http://mirror.bit.edu.cn/apache/kafka/0.10.2.1/kafka_2.12-0.10.2.1.tgz</p>

<p style="margin-left:0cm;">   2.解压安装包</p>

<p style="margin-left:0cm;">      tar -zxvf /export/software/kafka_2.11-0.8.2.2.tgz -C /export/servers/</p>

<p style="margin-left:0cm;">      cd /export/servers/</p>

<p style="margin-left:0cm;">    3. 配置环境变量：</p>

<pre class="has">
<code>　export KAFKA_HOME= /export/servers/kafka/kafka_2.12-0.10.2.1
　export PATH=$KAFKA_HOME/bin:$PATH
</code></pre>

<p>     4.<span style="color:#000000;">修改</span><span style="color:#000000;">kafka</span><span style="color:#000000;">配置文件：</span></p>

<pre class="has">
<code>#每台服务器的broker.id都不能相同
broker.id=1
host.name=192.168. 25.130 
advertised.host.name=192.168.25.130
advertised.port=9092 

delete.topic.enable=true
#用来监听链接的端口，生产者和消费者将在此端口建立链接
port=9092
#处理网络请求的线程数量
num.network.threads=3
#处理磁盘io的线程数量
num.io.threads=8
#发送套接字的缓冲区大小
socket.send.buffer.bytes=102400
#接收套接字缓冲区的大小
socket.receive.buffer.bytes=102400
#请求套接字的额缓冲区的大小
socket.request.max.bytes=104857600
#kafka运行日志的存放的路径
log.dirs=/export/servers/logs/kafka
#topic在当前broker上的数量
num.partitions=1
#用来恢复和清理data下数据的线程数量
num.recovery.threads.per.data.dir=1
#segment文件保留的最长时间，超时将被删除
log.retention.hours=168
message.max.byte=5242880
default.replication.factor=2
replica.fetch.max.bytes=5242880
zookeeper.connect=192.168.25.130:2181,192.168.25.131:2181,192.168.25.132:2181
</code></pre>

<p><span style="color:#000000;">    </span>分发到其他机器上，修改id，修改host.name,修改环境变量</p>

<p style="margin-left:0cm;">           sudo scp -r kafka/ storm@storm03:$pwd</p>

<p style="margin-left:0cm;">   5.依次启动kafka:</p>

<p style="margin-left:0cm;">          bin/kafka-server-start.sh  config/server.properties</p>

<p style="margin-left:0cm;">         启动报错修稿文件夹权限：</p>

<p style="margin-left:0cm;">         sudo chown -R storm:storm export/</p>

<p style="margin-left:0cm;">     <img alt="" class="has" height="183" src="https://img-blog.csdnimg.cn/20181117164146206.png" width="717"></p>

<p style="margin-left:0cm;">  6.kafka常用命令：</p>

<ol><li> 查看当前服务器中的所有topic：kafka-topics.sh --list --zookeeper  zk01:2181</li>
</ol><p>              <img alt="" class="has" height="117" src="https://img-blog.csdnimg.cn/20181117164753319.png" width="573"></p>

<p>      2创建topic：kafka-topics.sh --create --zookeeper zk01:2181 --replication-factor 2 --partitions 4 --topic <span style="color:#ff0000;">orderMq</span></p>

<p><span style="color:#ff0000;">      </span>3.通过shell来来生产kafka的消息：kafka-console-producer.sh --broker-list storm01:9092 --topic orderMq</p>

<p>             <img alt="" class="has" height="111" src="https://img-blog.csdnimg.cn/20181117164948695.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzY1MDQ1OA==,size_16,color_FFFFFF,t_70" width="491"></p>

<p>      4.通过shell来消费kafka的消息：kafka-console-consumer.sh --zookeeper zk01:2181 --from-beginning --topic orderMq</p>

<p>          <img alt="" class="has" height="196" src="https://img-blog.csdnimg.cn/20181117165036333.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzY1MDQ1OA==,size_16,color_FFFFFF,t_70" width="584"></p>

<p>      分布：</p>

<p>                <img alt="" class="has" height="500" src="https://img-blog.csdnimg.cn/20181117165130506.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzY1MDQ1OA==,size_16,color_FFFFFF,t_70" width="687"></p>

<p>    终极图片（Mr.Mao的图）：<img alt="" class="has" height="548" src="https://img-blog.csdnimg.cn/20181117165409424.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNzY1MDQ1OA==,size_16,color_FFFFFF,t_70" width="1182"></p>

<p>          </p>

<p>          </p>

<p><span style="color:#000000;">    接下来会讲解java操作kafka。。。。。。</span></p>

<p>   </p>

<p>   </p>

<p style="margin-left:0cm;"><br>
    </p>            </div>
                </div>