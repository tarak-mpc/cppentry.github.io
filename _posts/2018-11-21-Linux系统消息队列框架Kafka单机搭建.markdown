---
layout:     post
title:      Linux系统消息队列框架Kafka单机搭建
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u014427391/article/details/72151545				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-light">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p></p><div class="toc"><h3>文章目录</h3><ul><ul><ul><li><a href="#3JDK_88" rel="nofollow">3、安装JDK</a></li></ul></ul></ul></div><br>
##前言<br>
###Kafka简介<br>
Kafka是由LinkedIn开发的一个分布式的消息系统，使用Scala编写，它以可水平扩展和高吞吐率而被广泛使用。目前越来越多的开源分布式处理系统如Cloudera、Apache Storm、Spark都支持与Kafka集成。<br>
###Kafka优点<br>
Kafka好处：转自<a href="http://www.infoq.com/cn/articles/kafka-analysis-part-1" rel="nofollow">InfoQ：Kafka剖析</a><p></p>
<ul>
<li>
<p>解耦<br>
在项目启动之初来预测将来项目会碰到什么需求，是极其困难的。消息系统在处理过程中间插入了一个隐含的、基于数据的接口层，两边的处理过程都要实现这一接口。这允许你独立的扩展或修改两边的处理过程，只要确保它们遵守同样的接口约束。</p>
</li>
<li>
<p>冗余<br>
有些情况下，处理数据的过程会失败。除非数据被持久化，否则将造成丢失。消息队列把数据进行持久化直到它们已经被完全处理，通过这一方式规避了数据丢失风险。许多消息队列所采用的"插入-获取-删除"范式中，在把一个消息从队列中删除之前，需要你的处理系统明确的指出该消息已经被处理完毕，从而确保你的数据被安全的保存直到你使用完毕。</p>
</li>
<li>
<p>扩展性<br>
因为消息队列解耦了你的处理过程，所以增大消息入队和处理的频率是很容易的，只要另外增加处理过程即可。不需要改变代码、不需要调节参数。扩展就像调大电力按钮一样简单。</p>
</li>
<li>
<p>灵活性 &amp; 峰值处理能力<br>
在访问量剧增的情况下，应用仍然需要继续发挥作用，但是这样的突发流量并不常见；如果为以能处理这类峰值访问为标准来投入资源随时待命无疑是巨大的浪费。使用消息队列能够使关键组件顶住突发的访问压力，而不会因为突发的超负荷的请求而完全崩溃。</p>
</li>
<li>
<p>可恢复性<br>
系统的一部分组件失效时，不会影响到整个系统。消息队列降低了进程间的耦合度，所以即使一个处理消息的进程挂掉，加入队列中的消息仍然可以在系统恢复后被处理。</p>
</li>
<li>
<p>顺序保证<br>
在大多使用场景下，数据处理的顺序都很重要。大部分消息队列本来就是排序的，并且能保证数据会按照特定的顺序来处理。Kafka保证一个Partition内的消息的有序性。</p>
</li>
<li>
<p>缓冲<br>
在任何重要的系统中，都会有需要不同的处理时间的元素。例如，加载一张图片比应用过滤器花费更少的时间。消息队列通过一个缓冲层来帮助任务最高效率的执行———写入队列的处理会尽可能的快速。该缓冲有助于控制和优化数据流经过系统的速度。</p>
</li>
<li>
<p>异步通信<br>
很多时候，用户不想也不需要立即处理消息。消息队列提供了异步处理机制，允许用户把一个消息放入队列，但并不立即处理它。想向队列中放入多少消息就放多少，然后在需要的时候再去处理它们。</p>
</li>
</ul>
<p>##安装教程<br>
###1、关闭SELINUX</p>
<pre><code>vi /etc/selinux/config
</code></pre>
<p>对配置文件进行修改，然后按ESC键，:wq保存退出</p>
<pre><code>#SELINUX=enforcing 

#SELINUXTYPE=targeted

SELINUX=disabled #增加

:wq! #保存退出
</code></pre>
<p>###2、配置防火墙</p>
<pre><code>vi /etc/sysconfig/iptables
</code></pre>
<p>可以看到配置文件，然后按I键，insert一行</p>
<pre><code>-A INPUT -m state --state NEW -m tcp -p tcp --dport 9092 -j ACCEPT
</code></pre>
<p>然后按ESC键，:wq保存退出</p>
<pre><code># Firewall configuration written by system-config-firewall

# Manual customization of this file is not recommended.

*filter

:INPUT ACCEPT [0:0]

:FORWARD ACCEPT [0:0]

:OUTPUT ACCEPT [0:0]

-A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

-A INPUT -p icmp -j ACCEPT

-A INPUT -i lo -j ACCEPT

-A INPUT -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT

-A INPUT -m state --state NEW -m tcp -p tcp --dport 9092 -j ACCEPT

-A INPUT -j REJECT --reject-with icmp-host-prohibited

-A FORWARD -j REJECT --reject-with icmp-host-prohibited

COMMIT

:wq! #保存退出
</code></pre>
<h3><a id="3JDK_88"></a>3、安装JDK</h3>
<p>如果有安装yum的话，一般可以使用yum安装，下面给出网上一篇很不错的jdk安装教程，建议Linux安装的可以去linux公社找找教程<br>
<a href="http://www.linuxidc.com/Linux/2013-04/83520.htm" rel="nofollow">CentOS6安装JDK</a></p>
<p>###4、下载安装Kafka<br>
cd进入相应文件夹，一般安装到/usr/local/src</p>
<pre><code>cd /usr/local/src
</code></pre>
<p>wget下载文件</p>
<pre><code>wget http://archive.apache.org/dist/kafka/0.8.2.1/kafka_2.11-0.8.2.1.tgz
</code></pre>
<p>解压</p>
<pre><code>tar -xzvf kafka_2.11-0.8.2.1.tgz
</code></pre>
<p>移动到安装目录</p>
<pre><code>mv kafka_2.11-0.8.2.1 /usr/local/kafka
</code></pre>
<p>###5、配置Kafka</p>
<p>创建Kafka日志文件存放文件夹</p>
<pre><code>mkdir /usr/local/kafka/logs/kafka
</code></pre>
<p>cd配置文件目录</p>
<pre><code>cd /usr/local/kafka/config
</code></pre>
<p>VI编辑</p>
<pre><code>vi server.properties
</code></pre>
<p>修改配置</p>
<pre><code>broker.id=0

port=9092 #端口号

host.name=127.0.0.1 #服务器IP地址，修改为自己的服务器IP

log.dirs=/usr/local/kafka/logs/kafka #日志存放路径，上面创建的目录

zookeeper.connect=localhost:2181 #zookeeper地址和端口，单机配置部署，localhost:2181

:wq! #保存退出
</code></pre>
<p>###6、Zookeeper配置<br>
创建一个目录安装</p>
<pre><code>mkdir /usr/local/kafka/zookeeper
</code></pre>
<p>创建一个Zookeeper日志存放目录</p>
<pre><code>mkdir /usr/local/kafka/logs/zookeeper
</code></pre>
<p>配置文件</p>
<pre><code>cd /usr/local/kafka/config
</code></pre>
<pre><code>vi zookeeper.properties
</code></pre>
<pre><code>dataDir=/usr/local/kafka/zookeeper #zookeeper数据目录

dataLogDir=/usr/local/kafka/log/zookeeper #zookeeper日志目录

clientPort=2181

maxClientCnxns=100

tickTime=2000

initLimit=10

syncLimit=5

:wq! #保存退出
</code></pre>
<p>###7、脚本编写</p>
<p>编写kafka的start脚本</p>
<pre><code>cd /usr/local/kafka
</code></pre>
<p>使用vi创建脚本</p>
<pre><code>vi kafkastart.sh 
</code></pre>
<p>加入脚本代码，&amp;符号表示在后台执行，然后:wq保存退出</p>
<pre><code>#!/bin/sh

#启动zookeeper

/usr/local/kafka/bin/zookeeper-server-start.sh /usr/local/kafka/config/zookeeper.properties &amp;

sleep 3 #等3秒后执行

#启动kafka

/usr/local/kafka/bin/kafka-server-start.sh /usr/local/kafka/config/server.properties &amp;

</code></pre>
<p>编写kafka的stop脚本</p>
<pre><code>vi kafkastop.sh
</code></pre>
<p>脚本代码如，同样是:wq保存退出</p>
<pre><code>#!/bin/sh

#关闭zookeeper

/usr/local/kafka/bin/zookeeper-server-stop.sh /usr/local/kafka/config/zookeeper.properties &amp;

sleep 3 #等3秒后执行

#关闭kafka

/usr/local/kafka/bin/kafka-server-stop.sh /usr/local/kafka/config/server.properties &amp;
</code></pre>
<p>添加脚本执行权限</p>
<pre><code>chmod +x kafkastart.sh

chmod +x kafkastop.sh
</code></pre>
<p>###8、配置脚本开机自行启动</p>
<pre><code>vi /etc/rc.d/rc.local
</code></pre>
<p>设置开机时脚本在后台执行，使用&amp;符号<br>
将如下代码添加到rc.local里，同样使用VI编辑器</p>
<pre><code>sh /usr/local/kafka/kafkastart.sh &amp;
</code></pre>
<p>：wq保存退出</p>
<p>###9、启动kafka</p>
<pre><code>sh /usr/local/kafka/kafkastart.sh #启动kafka
</code></pre>
<p>###10、创建topic</p>
<pre><code>usr/local/kafka/bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test

/usr/local/kafka/bin/kafka-topics.sh --list --zookeeper localhost:2181 test

/usr/local/kafka/bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test --from-beginning
</code></pre>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>