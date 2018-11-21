---
layout:     post
title:      第89讲：Spark Streaming on Kafka解析和安装实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
</p>
<pre style="font-size:9pt;font-family:'宋体';background-color:rgb(255,255,255);"><span style="color:rgb(51,51,51);font-family:Arial, 'Microsoft YaHei';font-size:14px;line-height:14px;background-color:rgb(238,238,238);">有兴趣想学习国内整套Spark+Spark Streaming+Machine learning最</span><span style="color:rgb(51,51,51);font-family:Arial, 'Microsoft YaHei';font-size:14px;line-height:14px;background-color:rgb(238,238,238);">顶级</span><span style="color:rgb(51,51,51);font-family:Arial, 'Microsoft YaHei';font-size:14px;line-height:14px;background-color:rgb(238,238,238);">课程的，可加我qq  471186150。共享视频，性价比超高！</span></pre>
<br><p></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
本课分2部分讲解：</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
第一部分，讲解Kafka的概念、架构和用例场景；</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
第二部分，讲解Kafka的安装和实战。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
由于时间关系，今天的课程只讲到如何用官网的例子验证Kafka的安装是否成功。后续课程会接着讲解如何集成Spark Streaming和Kafka。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>一、Kafka的概念、架构和用例场景</span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<a href="http://kafka.apache.org/documentation.html#introdution" rel="nofollow" style="color:#000000;">http://kafka.apache.org/documentation.html#introdution</a></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>1、Kafka的概念</span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
Apache Kafka是分布式发布-订阅消息系统。它最初由LinkedIn公司开发，之后成为Apache项目的一部分。Kafka是一种快速、可扩展的、设计内在就是分布式的，分区的和可复制的提交日志服务。zookeeper和kafka和大数据是不只能用于大数据的，集群启不启动，它都可以使用，也可以用于java server普通的企业级平台上。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>什么是消息组件:</span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
以帅哥和美女聊天为例，帅哥如何和美女交流呢？这中间通常想到的是微信、QQ、电话、邮件等通信媒介，这些通信媒介就是消息组件，帅哥把聊天信息发送给消息组件、消息组件将消息推送给美女，这就是常说的生产者、消费者模型，kafka不仅仅说是生产者消费者模式中广播的概念，也可以实现队列的方式，kafka的消费者中有一个group的概念，group中可以有很多实体，也可以只有一个实体，group中只有一个实体的话，就是队列的方式，所以从消息驱动的角度讲，它是广播的方式和队列的方式的完美结合体。而且在发送信息时可以将内容进行分类，即所谓的Topic主题。Kafka就是这样的通信组件，将不同对象组件粘合起来的纽带，
 且是解耦合方式传递数据。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
完善的流处理系统的特点：</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
1）能在线的以非常低的延迟，来处理数据，而且是稳定可靠的</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
2）能对流进来的数据进行非常复杂的分析，而不是简单的仅仅统计的分析</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
3）不仅能处理当前在线的数据，也能处理过去一天，一周，一个月甚至一年的数据</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>Apache Kafka与传统消息系统相比，有以下不同的特点：</span></p>
<ul style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);"><li style="list-style:disc;">分布式系统，易于向外扩展；</li><li style="list-style:disc;">在线低延迟，同时为发布和订阅提供高吞吐量；</li><li style="list-style:disc;">流进来的数据一般处理完后就消失了，也可以将消息存储到磁盘，因此可以处理1天甚至1周前内容，所以kafka不仅是一个消息中间件，还是一个存储系统</li></ul><p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span><span lang="en-us" xml:lang="en-us">2</span>、<span lang="en-us" xml:lang="en-us">Kafka</span>的架构</span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span><img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160426001347205-1918052773.png" alt="" style="border:0px;"></span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
Kafka既然具备消息系统的基本功能，那么就必然会有组成消息系统的组件：</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
Topic，Producer和Consumer。Kafka还有其特殊的Kafka Cluster组件。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
Topic主题：</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
代表一种数据的类别或类型，工作、娱乐、生活有不同的Topic，生产者需要说明把说明数据分别放在那些Topic中，里面就是一个个小对象，并将数据数据推到Kafka，消费者获取数据是pull的过程。一组相同类型的消息数据流。这些消息在Kafka会被分区存放，并且有多个副本，以防数据丢失。每个分区的消息是顺序写入的，并且不可改写。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160426001507002-1927984098.png" alt="" style="border:0px;"></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       Producer（生产者）：把数据推到Kafka系统的任何对象。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
 </p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
- Kafka Cluster（Kafka集群）：把推到Kafka系统的消息保存起来的一组服务器，也叫Broker。因为Kafka集群用到了Zookeeper作为底层支持框架，所以由一个选出的服务器作为Leader来处理所有消息的读和写的请求，其他服务器作为Follower接受Leader的广播同步备份数据，以备灾难恢复时用。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
- Consumer（消费者）：从Kafka系统订阅消息的任何对象。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
消费者可以有多个，并且某些消费者还可以组成Consumer Group。多个Consumer Group之间组成消息广播的关系，所以各个Group可以拉相同的消息数据。在Consumer Group内部，各消费者之间对Consumer Group拉出来的消息数据是队列先进先出的关系，某个消息数据只能给该Group的一个消费者使用，同一个Group中的实体是互斥的，对一个消息，这样是避免重复消费。如果有多个group，每个group中只有一个实体，这就是队列的方式了，因为它是互斥的。如果不是一个实体，则是广播模式，如下图所示，广播只能广播给一个group中的一个消费实体</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160426001541642-2097443983.png" alt="" style="border:0px;"></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
kafka的数据传输是基于kernel（内核）级别的（传输速度接近0拷贝-ZeroCopy）、没有用户空间的参与。Linux本身是软件，软件启动时第一个启动进程叫init，在init进程启动后会进入用户空间；kafka是用java写的，是基于jvm虚拟机的。例如：在分布式系统中，机器A上的应用程序需要读取机器B上的Java服务数据，由于Java程序对应的JVM是用户空间级别而且数据在磁盘上，A上应用程序读取数据时会首先进入机器B上的内核空间再进入机器B的用户空间，读取用户空间的数据后，数据再经过B机器上的内核空间分发到网络中(之所以要再经过B的内核，因为要通过网络通信，不通过内核，哪里来的网络通信)，机器A网卡接收到传输过来的数据后再将数据写入A机器的内核空间，从而最终将数据传输给A的用户空间进行处理。如下图：网络本身是一种硬件，磁盘只是硬件的一种。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160426001602189-27650640.png" alt="" width="732" height="493" style="border:0px;"></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
正常情况下，外部系统从Java程序中读取数据，传输给内核空间并依赖网卡将数据写入到网络中，从而把数据传输出去。其实Java本身是内核的一层外衣，Java Socket编程，操作的各种数据都是在JVM的用户空间中进行的。而Kafka操作数据是放在内核空间的，通常内核空间处理数据的速度比用户空间快上万倍，因为没用用户态和内核态的切换，所以通过kafka可以实现高速读、写数据。只要磁盘空间足够大，可以无限量的存储数据，kafka的数据就是存储在磁盘中的，不是存在内核中的。而很多消息组件是把数据存内存中的。kafka用zookeeperg管理元数据，而且按顺序写数据，比随机写要快很多。又有副本！</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>3、Kafka的用例场景 </span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
类似微信，手机和邮箱等等这样大家熟悉的消息组件，Kafka也可以：</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       支持文字/图片</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       可以存储内容</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       分门别类</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
从内容消费的角度，Kafka把邮箱中的邮件类型看成是Topic。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>二、Kafka的安装和实战</span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<a href="http://kafka.apache.org/documentation.html#quickstart" rel="nofollow" style="color:#000000;">http://kafka.apache.org/documentation.html#quickstart</a></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>1、安装和配置Zookeeper</span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
Kafka集群模式需要提前安装好Zookeeper。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       提示：Kafka单例模式不需要安装额外的Zookeeper，可以使用内置的Zookeeper。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       Kafka集群模式需要至少3台服务器。本课实战用到的服务器Hostname：master，slave1，slave2。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       本课中用到的Zookeeper版本是Zookeeper-3.4.6。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="line-height:1.5;">1)    下载Zookeeper</span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
进入<a href="http://www.apache.org/dyn/closer.cgi/zookeeper/" rel="nofollow" style="color:#000000;">http://www.apache.org/dyn/closer.cgi/zookeeper/</a>，你可以选择其他镜像网址去下载，用官网推荐的镜像：<a href="http://mirror.bit.edu.cn/apache/zookeeper/" rel="nofollow" style="color:#000000;">http://mirror.bit.edu.cn/apache/zookeeper/</a>。提示：可以直接下载群里的Zookeeper安装文件。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160426001710830-728902693.png" alt="" style="border:0px;"></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160426001726283-174197864.png" alt="" width="557" height="528" style="border:0px;"></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
下载<span lang="en-us" xml:lang="en-us">zookeeper-3.4.6.tar.gz</span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span lang="en-us" xml:lang="en-us"><img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160426001753252-854065366.png" alt="" style="border:0px;"></span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
1)    安装Zookeeper</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
提示：下面的步骤发生在master服务器。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
以ubuntu14.04举例，把下载好的文件放到/root目录，用下面的命令解压：</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
cd /root</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
tar -zxvf zookeeper-3.4.6.tar.gz</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
解压后在/root目录会多出一个zookeeper-3.4.6的新目录，用下面的命令把它剪切到指定目录即安装好Zookeeper了：</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
cd /root</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
mv zookeeper-3.4.6 /usr/local/spark</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
之后在/usr/local/spark目录会多出一个zookeeper-3.4.6的新目录。下面我们讲如何配置安装好的Zookeeper。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
2)    配置Zookeeper</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
提示：下面的步骤发生在master服务器。</p>
<ol style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);"><li style="list-style:decimal;">配置.bashrc</li></ol><p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       打开文件：vi /root/.bashrc</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       在PATH配置行前添加：</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
export ZOOKEEPER_HOME=/usr/local/spark/zookeeper-3.4.6</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       最后修改PATH：</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
export PATH=${JAVA_HOME}/bin:${ZOOKEEPER_HOME}/bin:${HADOOP_HOME}/bin:${HADOOP_HOME}/sbin:${SCALA_HOME}/bin:${SPARK_HOME}/bin:${SPARK_HOME}/sbin:${HIVE_HOME}/bin:${KAFKA_HOME}/bin:$PATH</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       使配置的环境变量立即生效：source /root/.bashrc</p>
<ol style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);"><li style="list-style:decimal;">创建data目录</li></ol><p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       cd $ZOOKEEPER_HOME</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       mkdir data</p>
<ol style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);"><li style="list-style:decimal;">创建并打开zoo.cfg文件</li></ol><p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       cd $ZOOKEEPER_HOME/conf</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       cp zoo_sample.cfg zoo.cfg</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       vi zoo.cfg</p>
<ol style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);"><li style="list-style:decimal;">配置zoo.cfg</li></ol><p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
# 配置Zookeeper的日志和服务器身份证号等数据存放的目录。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
# 千万不要用默认的/tmp/zookeeper目录，因为/tmp目录的数据容易被意外删除。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
dataDir=../data</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
# Zookeeper与客户端连接的端口</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
clientPort=2181</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
# 在文件最后新增3行配置每个服务器的2个重要端口：Leader端口和选举端口</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
# server.A=B：C：D：其中 A 是一个数字，表示这个是第几号服务器；</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
# B 是这个服务器的hostname或ip地址；</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
# C 表示的是这个服务器与集群中的 Leader 服务器交换信息的端口；</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
# D 表示的是万一集群中的 Leader 服务器挂了，需要一个端口来重新进行选举，</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
# 选出一个新的 Leader，而这个端口就是用来执行选举时服务器相互通信的端口。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
# 如果是伪集群的配置方式，由于 B 都是一样，所以不同的 Zookeeper 实例通信</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
# 端口号不能一样，所以要给它们分配不同的端口号。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
server.1=master:2888:3888</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
server.2=slave1:2888:3888</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
server.3=slave2:2888:3888</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:#ff0000;">改成如下方式：</span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:#ff0000;">dataDir=/usr/local/spark/zookeeper-3.4.6/data<br>
dataLogDir=/usr/local/spark/zookeeper-3.4.6/logs<br>
clientPort=2181<br>
server.0=master1:2888:3888<br>
server.1=work1:2888:3888<br>
server.2=work2:2888:3888</span><br></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<br></p>
<ol style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);"><li style="list-style:decimal;">创建并打开myid文件</li></ol><p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       cd $ZOOKEEPER_HOME/data</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       touch myid</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       vi myid</p>
<ol style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);"><li style="list-style:decimal;">配置myid</li></ol><p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
按照zoo.cfg的配置，myid的内容就是1。要写成0，和上面zoo.cfg里面的配置server.0,server.1,server.2一致，所以下面work1中myid内容为1，work2中myid内容为2</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
3)    同步master的安装和配置到slave1和slave2</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       在master服务器上运行下面的命令</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
cd /root</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
scp ./.bashrc root@slave1:/root</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
scp ./.bashrc root@slave2:/root</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
cd /usr/local/spark</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
scp -r ./zookeeper-3.4.6 root@slave1:/usr/local/spark</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
scp -r ./zookeeper-3.4.6 root@slave2:/usr/local/spark</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       在slave1服务器上运行下面的命令</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
vi $ZOOKEEPER_HOME/data/myid</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
按照zoo.cfg的配置，myid的内容就是1。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       在slave2服务器上运行下面的命令</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
vi $ZOOKEEPER_HOME/data/myid</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
按照zoo.cfg的配置，myid的内容就是2。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
4)    启动Zookeeper服务</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       在master服务器上运行下面的命令</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
zkServer.sh start</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       在slave1服务器上运行下面的命令</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
source /root/.bashrc</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
zkServer.sh start</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       在slave1服务器上运行下面的命令</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
source /root/.bashrc</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
zkServer.sh start</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
5)    验证Zookeeper是否安装和启动成功</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       在master服务器上运行命令：jps和zkServer.sh status</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
root@master:/usr/local/spark/zookeeper-3.4.6/bin# jps</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
3844 QuorumPeerMain</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
4790 Jps</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
zkServer.sh status</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
root@master:/usr/local/spark/zookeeper-3.4.6/bin# zkServer.sh status</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
JMX enabled by default</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
Using config: /usr/local/spark/zookeeper-3.4.6/bin/../conf/zoo.cfg</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
Mode: follower</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       在slave1服务器上运行命令：jps和zkServer.sh status</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
source /root/.bashrc</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
root@slave1:/usr/local/spark/zookeeper-3.4.6/bin# jps</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
3462 QuorumPeerMain</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
4313 Jps</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
root@slave1:/usr/local/spark/zookeeper-3.4.6/bin# zkServer.sh status</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
JMX enabled by default</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
Using config: /usr/local/spark/zookeeper-3.4.6/bin/../conf/zoo.cfg</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
Mode: follower</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       在slave2服务器上运行命令：jps和zkServer.sh status</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
root@slave2:/usr/local/spark/zookeeper-3.4.6/bin# jps</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
4073 Jps</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
3277 QuorumPeerMain</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
root@slave2:/usr/local/spark/zookeeper-3.4.6/bin# zkServer.sh status</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
JMX enabled by default</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
Using config: /usr/local/spark/zookeeper-3.4.6/bin/../conf/zoo.cfg</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
Mode: leader</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
      至此，代表Zookeeper已经安装和配置成功。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>2、安装和配置Kafka</span></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
本课中用到的Kafka版本是Kafka-2.10-0.9.0.1。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
1)    下载Kafka </p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
进入<a href="http://kafka.apache.org/downloads.html" rel="nofollow" style="color:#000000;">http://kafka.apache.org/downloads.html</a>，左键单击kafka_2.10-0.9.0.1.tgz。提示：可以直接下载群里的Kafka安装文件。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160426001855955-1197546164.png" alt="" style="border:0px;"></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
下载kafka_2.10-0.9.0.1.tgz</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160426001919283-1079432798.png" alt="" style="border:0px;"></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
1)    安装Kafka</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
提示：下面的步骤发生在master服务器。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
以ubuntu14.04举例，把下载好的文件放到/root目录，用下面的命令解压：</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
cd /root</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
tar -zxvf kafka_2.10-0.9.0.1.tgz</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
解压后在/root目录会多出一个kafka_2.10-0.9.0.1的新目录，用下面的命令把它剪切到指定目录即安装好Kafka了：</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
cd /root</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
mv kafka_2.10-0.9.0.1 /usr/local</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
之后在/usr/local目录会多出一个kafka_2.10-0.9.0.1的新目录。下面我们讲如何配置安装好的Kafka。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
2)    配置Kafka</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
提示：下面的步骤发生在master服务器。</p>
<ol style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);"><li style="list-style:decimal;">配置.bashrc</li></ol><p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       打开文件：vi /root/.bashrc</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       在PATH配置行前添加：</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
export KAFKA_HOME=/usr/local/kafka_2.10-0.9.0.1</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       最后修改PATH：</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
export PATH=${JAVA_HOME}/bin:${ZOOKEEPER_HOME}/bin:${HADOOP_HOME}/bin:${HADOOP_HOME}/sbin:${SCALA_HOME}/bin:${SPARK_HOME}/bin:${SPARK_HOME}/sbin:${HIVE_HOME}/bin:${KAFKA_HOME}/bin:$PATH</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       使配置的环境变量立即生效：source /root/.bashrc</p>
<ol style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);"><li style="list-style:decimal;">打开server.properties</li></ol><p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       cd $ZOOKEEPER_HOME/config</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       vi server.properties</p>
<ol style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);"><li style="list-style:decimal;">配置server.properties</li></ol><p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
broker.id=0</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
port=9092</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
zookeeper.connect=master:2181,slave1:2181,slave2:2181</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
3)    同步master的安装和配置到slave1和slave2</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       在master服务器上运行下面的命令</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
cd /root</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
scp ./.bashrc root@slave1:/root</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
scp ./.bashrc root@slave2:/root</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
cd /usr/local</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
scp -r ./kafka_2.10-0.9.0.1 root@slave1:/usr/local</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
scp -r ./kafka_2.10-0.9.0.1 root@slave2:/usr/local</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       在slave1服务器上运行下面的命令</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
vi $KAFKA_HOME/config/server.properties</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
修改broker.id=1。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       在slave2服务器上运行下面的命令</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
vi $KAFKA_HOME/config/server.properties</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
修改broker.id=2。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
4)    启动Kafka服务</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       在master服务器上运行下面的命令,nohup,在集群上终端不输出启动日志</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
cd $KAFKA_HOME/bin</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
nohup ./kafka-server-start.sh ../config/server.properties &amp;</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       在slave1服务器上运行下面的命令</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
source /root/.bashrc</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
cd $KAFKA_HOME/bin</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
nohup ./kafka-server-start.sh ../config/server.properties &amp;</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       在slave2服务器上运行下面的命令</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
source /root/.bashrc</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
cd $KAFKA_HOME/bin</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
kafka-server-start.sh ../config/server.properties &amp;</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
5)    验证Kafka是否安装和启动成功</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       在任意服务器上运行命令创建Topic“HelloKafka”：</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
kafka-topics.sh --create --zookeeper master:2181,slave1:2181,slave2:2181 --replication-factor 3 --partitions 1 --topic HelloKafka</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       在任意服务器上运行命令为创建的Topic“HelloKafka”生产一些消息：</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
kafka-console-producer.sh --broker-list master:9092,slave1:9092,slave2:9092 --topic HelloKafka</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
输入下面的消息内容：</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
This is DT_Spark!</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
I’m Rocky!</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
Life is short, you need Spark!</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       在任意服务器上运行命令从指定的Topic“HelloKafka”上消费（拉取）消息：</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
kafka-console-consumer.sh --zookeeper master:2181,slave1:2181,slave2:2181 --from-beginning --topic HelloKafka</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
过一会儿，你会看到打印的消息内容：</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
This is DT_Spark!</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
I’m Rocky!</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
Life is short, you need Spark!</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       在任意服务器上运行命令查看所有的Topic名字：</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
kafka-topics.sh --list --zookeeper master:2181,slave1:2181,slave2:2181</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
-       在任意服务器上运行命令查看指定Topic的概况：</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
kafka-topics.sh --describe --zookeepermaster:2181,slave1:2181,slave2:2181 --topic HelloKafka</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
至此，代表Kafka已经安装和配置成功。</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>总结：</span></p>
<h3 align="center" style="font-size:16px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(238,238,238);">
使用Spark Streaming可以处理各种数据来源类型，如：数据库、HDFS，服务器log日志、网络流，其强大超越了你想象不到的场景，只是很多时候大家不会用，其真正原因是对Spark、spark streaming本身不了解。</h3>
            </div>
                </div>