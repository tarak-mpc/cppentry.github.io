---
layout:     post
title:      kafka简介以及在Ubuntu环境下kafka的安装和实例介绍
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_37788081/article/details/80641817				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="kafka简介以及在ubuntu环境下kafka的安装和实例介绍">kafka简介以及在Ubuntu环境下kafka的安装和实例介绍</h1>



<h2 id="kafka的介绍">kafka的介绍：</h2>



<h3 id="kafka是什么有什么用">kafka是什么？有什么用？</h3>

<p>Kafka 是一种搞吞吐量的分布式发布订阅消息系统，他可以处理消费着规模的网站中的动作流数据。 <br>
 Kafka的目的是通过Hadoop和Spark 等的并行加载机制来统一线上和离线的消息处理。</p>



<h3 id="kafka的相关概念">kafka的相关概念</h3>

<ul>
<li>Broker ：Kafka集群包含一个或多个服务器，这些服务器称为Broker</li>
<li>Topic : 每条发布到Kafka集群的消息都有一个类别，这个类别称之为Topic,物理上不同Topic的消息分开存储，逻辑上一个Topic的消息虽然保存在一个或多个Broker上，但是用户只需要指定消息的Topic,即可生产或者消费数据，而不必关心数据存于何处</li>
<li>Partition :是物理上的概念，每个Topic包含一个或多个Partition</li>
<li>Producer: 负责发布消息到Kafka Broker</li>
<li>Consumer: 消息消费之，想Kafka Broker 读取消息的客户端</li>
<li>Consumer Group: 每个Consumer属于一个特定的Consumer Group，可以为每个Consumer指定group name , 若不指定则属于默认的group</li>
</ul>



<h2 id="安装kafka">安装kafka</h2>



<h3 id="kafka安装包的下载">Kafka安装包的下载</h3>

<ul>
<li>1.访问<a href="https://kafka.apache.org/downloads" rel="nofollow">Kafka官网</a>下载页面,下载Kafka稳定版的安装包kafka_2.10-0.10.1.0.tgz,</li>
<li>2.或者访问我以及准备好的github链接直接下载稳定版安装包<a href="https://github.com/2391134843/BigData/tree/master/Kafka" rel="nofollow">下载地址</a> <br>
<strong>两种安装方式都很方便，请大家自己选择推荐第二种</strong></li>
</ul>



<h3 id="kafka安装操作">kafka安装操作</h3>

<p><strong>文件下载之后需要解压，按照Linux的系统的规范，一般用户安装的软件都是存放在/usr/local 目录下面</strong> <br>
<strong>具体步骤如下：</strong></p>

<blockquote>
  <p>～ cd ~/下载 <br>
  ～ sudo tar -zxf kafka_2.10-0.10.1.0.tgz -C /usr/local <br>
  ～ cd /usr/local <br>
  ～ sudo mv kafka_2.10-0.10.1.0.tgz/ ./kafka <br>
  ～ sudo chown 777 ./kafka</p>
</blockquote>

<p>__(注意这里可能有人会问有些教程不是用 sudo chown -R hadoop ./kafka  吗？ <br>
 我之前也是用这个权限命令，但是这样会报错，有些文件的创建权限不够，如下图：)__ <br>
 <img src="http://p8i28834i.bkt.clouddn.com/kafuka-1.png" alt="" title=""></p>



<h2 id="实例介绍">实例介绍</h2>



<h3 id="第一个终端">第一个终端</h3>

<p>新建立一个Linux终端，执行如下命令启动Zookeeper:</p>

<blockquote>
  <p>～ cd /usr/local/kafka <br>
   ～ bin/zookeeper-server-start.sh config/zookeeper.properties</p>
</blockquote>

<p>执行结果： <br>
<img src="http://p8i28834i.bkt.clouddn.com/kafuka-%E7%AC%AC%E4%B8%80%E4%B8%AA%E7%BB%88%E7%AB%AF.png" alt="" title=""></p>

<p><strong>注意！！！ 执行了上面的命令之后会弹出一大堆信息，有两种情况，一种是刚刚在上面说了的那种权限不够的问题，还有一种是成功了，两种都会弹出一大堆信息，并且停住不动，没有回到shell命令提示符状态，这是正常的不要误以为死机，如果此时你讲这个终端关闭了则Zookeeper服务就停止了</strong></p>



<h3 id="第二个终端">第二个终端</h3>

<blockquote>
  <p>~ cd /usr/local/kafka <br>
   ~  ./bin/kafka-server-start.sh config/server.properties </p>
</blockquote>

<p>执行结果： <br>
<img src="http://p8i28834i.bkt.clouddn.com/kafuka-%E7%AC%AC%E4%BA%8C%E4%B8%AA%E7%BB%88%E7%AB%AF.png" alt="" title=""></p>



<h3 id="第三个个终端">第三个个终端</h3>

<p>同样的，我们需要重新在建立一个新的终端，不会同时建立多个终端的朋友请自行百度，Google</p>

<blockquote>
  <p>~ cd /usr/local/kafka <br>
   ~  ./bin/kafka-topics.sh –create –zookeeper localhost:2181 –replication-factor 1 –partitions 1 –topic dblab</p>
</blockquote>

<p>执行结果： <br>
<img src="http://p8i28834i.bkt.clouddn.com/kafuka-%E7%AC%AC%E4%B8%89%E4%B8%AA%E7%BB%88%E7%AB%AF.png" alt="" title=""></p>

<p><strong>我之前已经创建过了，所以显示以及存在，如果没有创建过则会显示Created topic XXX</strong></p>

<p><strong>topic 是用来发布消息的category,一单节点的配置方式创建一个名为dblab的topic。可以利用list命令列出所有创建了的topics,来产看刚才创建的topic是否存在，命令如下：</strong></p>

<blockquote>
  <p>~ cd /usr/local/kafka <br>
  ~ ./bin/kafka-topics.sh –list –zookeeper localhost:2181</p>
</blockquote>

<p>在已经知道创建了topic之后可以来用producer生产一些数据：</p>

<blockquote>
  <p>~ cd /usr/local/kafka <br>
  ~ ./bin/kafka-console-producer.sh –broker-list localhost:9092 –topic dblab <br>
  在该命令之心后，可以在终端中输入以下信息来测试 <br>
  hello hadoop <br>
  hello haha <br>
  hadoop world</p>
</blockquote>



<h3 id="打开第四个终端">打开第四个终端：</h3>

<p>使用consumer来接受刚刚写入的数据</p>

<blockquote>
  <p>~ cd /usr/local/kafka <br>
   ～  ./bin/kafka-console-consumer.sh –zookeeper localhost:2181 –topic dblab –from-beginning</p>
</blockquote>

<p>执行结果： <br>
<img src="http://p8i28834i.bkt.clouddn.com/kafuka-%E7%AC%AC%E5%9B%9B%E4%B8%AA%E7%BB%88%E7%AB%AF.png" alt="" title=""> <br>
 当显示出你输入的信息之后则说明kafuka安装完全成功 <br>
 ## 最终我们应该有四个中断： <br>
 <img src="http://p8i28834i.bkt.clouddn.com/kafuka-%E5%85%A8%E5%9B%9B%E4%B8%AA%E7%BB%88%E7%AB%AF.png" alt="" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>