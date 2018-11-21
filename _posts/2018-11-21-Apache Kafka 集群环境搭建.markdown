---
layout:     post
title:      Apache Kafka 集群环境搭建
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
Apache Kafka 是一个高吞吐量分布式消息系统，由LinkedIn开源。引用官网对kafka的介绍：“Apache Kafka is publish-subscribe messaging rethought as a distributed commit log.”  “publish-subscribe”是kafka设计的核心思想，也是kafka最具特色的地方。publish在kakfa中是一个producer的角色，subscribe是consumer，就像我们生活中的一样，生产商生产出来的产品，消费者一般不能够直接去工厂购买，还需要一个代理经销商，所以同样的在kafka的生态系统中，有一个broker的角色。所以kafka的生态系统大致可以表述如下：</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
"producer——&gt;broker&lt;——consumer"</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
大致的介绍就这么多，具体的大家可以移步官网：<a href="http://kafka.apache.org/" rel="nofollow" style="color:rgb(16,138,198);">http://kafka.apache.org/ </a></p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
接下来是老生常谈的问题：为什么要用kafka？kafka适用什么样的场景？我先和大家分享一下自己再项目中的使用总结，有其他想法的同学欢迎补充：</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
<strong> 使用kafka的理由：</strong></p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
1.分布式，高吞吐量，速度快（kafka是直接通过磁盘存储，线性读写，速度快：避免了数据在JVM内存和系统内存之间的复制，减少耗性能的对象创建和垃圾回收）</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
2.同时支持实时和离线两种解决方案（相信很多项目都有类似的需求，这也是Linkedin的官方架构，我们是一部分数据通过storm做实时计算处理，一部分到hadoop做离线分析）。</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
3.open source (open source 谁不喜欢呢)</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
4.源码由scala编写，可以运行在JVM上（笔者对scala很有好感，函数式语言一直都挺帅的，spark也是由scala写的，看来以后有空得刷刷scala）</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
<strong>使用场景：</strong></p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
笔者主要是用来做日志分析系统，其实Linkedin也是这么用的，可能是因为kafka对可靠性要求不是特别高，除了日志，网站的一些浏览数据应该也适用。（只要原始数据不需要直接存DB的都可以）</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
 </p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
下面就简单的介绍一下kafka集群的搭建过程：</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
 </p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
准备环境：至少3台的linux server（笔者是准备了5台redhat版本的cloud server）</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
 </p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
<strong>第一步:</strong> 安装JDK/JRE</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
<strong>第二步:</strong> 安装Zookeeper（kafka自带有zookeeper服务，但是建议大家最好单独建立一个zookeeper集群，可以和其他应用共享，也便于管理）</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
zookeeper的安装，大家可以参考我的另一篇博文：<a href="http://bigcat2013.iteye.com/blog/2175538" rel="nofollow" style="color:rgb(16,138,198);">http://bigcat2013.iteye.com/blog/2175538</a></p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
<strong>第三步</strong>：下载kafka ：<a href="http://kafka.apache.org/downloads.html" rel="nofollow" style="color:rgb(16,138,198);">http://kafka.apache.org/downloads.html</a>（最好下载scala预编译好的package，例如我下的是kafka_2.10-0.8.1.1.tgz，意思就是用scala 2.10预编译好的0.8.1.1版本）</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
<strong>第四步</strong>：上传安装包到服务器（可以通过WinSCP等）</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
<strong>第五步</strong>：使用 "<strong>tar -xzvf  kafka_2.10-0.8.1.1.tgz</strong> "来 解压安装包 ：</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
解压后的目录结构：</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
<img alt="" src="http://dl2.iteye.com/upload/attachment/0105/2635/5d7c3d54-c409-3a23-bae0-98f70fd94f50.png" style="border:0px;"><br><strong> 第六步</strong>：修改配置文件</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
简答配置的话修改/config/server.properties 就可以了</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
需要配置的属性有：broker.id(标示当前server在集群中的id，从0开始)，port，host.name(当前的server host name)，zookeeper.connect(连接的zookeeper集群)，log.dirs(log的存储目录，记得对应的去建立这个目录)等，其他的一些配置可以看相应的注释：</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
<img alt="" src="http://dl2.iteye.com/upload/attachment/0105/2648/726515bf-8e00-3adc-8fee-1787f0df0ccf.png" style="border:0px;"><br>
 <br><img alt="" src="http://dl2.iteye.com/upload/attachment/0105/2658/f8101492-7dc6-3da0-a06c-55f5aef64bdf.png" style="border:0px;"></p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
 </p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
<strong> 第七步</strong>：通过“scp -r ”把配置好的kafka目录copy到其他几台server上：</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
<img alt="" src="http://dl2.iteye.com/upload/attachment/0105/2664/cb4b35dc-a373-3b50-b904-191a9effd3a7.png" style="border:0px;"><br>
 </p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
<strong>第八步</strong>：修改每台server对应的配置文件，主要是修改其中的broker.id 和 host.name 属性：</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
broker.id从0开始递增，每台server必须唯一<br><img alt="" src="http://dl2.iteye.com/upload/attachment/0105/2666/fd80a847-8e85-302a-8246-5676d6cf6ee2.png" style="border:0px;"><br>
 </p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
<strong>第九步</strong>： 先启动zookeeper集群，再启动kakfa集群</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
kafka启动命令： sudo nohup ./bin/kafka-server-start.sh config/server.properties &amp;</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
 </p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
<strong>第十步</strong>：集群启动成功后，可以试着创建topic，在一台server上创建producer，另外一台创建consumer，从producer上发送信息，看consumer是否能接收到，以验证集群对否成功。</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
<strong> 创建topic</strong> ：  sudo ./bin/kafka-topics.sh -zookeeper server1:2181,server2:2181,server3:2181 -topic test -replication-factor 2 -partitions 5 -create</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
<strong> 查看topic：</strong>sudo ./bin/kafka-topics.sh -zookeeper server1:2181,server2:2181,server3:2181 -list</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
<strong> 创建producer：</strong>sudo ./bin/kafka-console-producer.sh -broker-list kafkaServer1:9092,kafkaServer2:9092,kafkaServer3:9092 -topic test</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
<strong> 创建consumer：</strong>sudo ./bin/kafka-console-consumer.sh -zookeeper server1:2181,server2:2181,server3:2181 - from-begining -topic test</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
 </p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.1875px;">
通过在创建好的producer控制台输入信息，在consumer的控制台检测输出来测试，如果可以同步接受到信息就说明简单的kakfa 集群搭好了，另外可以根据项目的实际需求进一步做配置。</p>
            </div>
                </div>