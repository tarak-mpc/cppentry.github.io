---
layout:     post
title:      windows kafka 安装测试
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/weixin_34160718/article/details/81704849				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>kafka运行依赖zookeeper，所以预先安装好zookeeper</p>

<p>启动kafka</p>

<p>在安装目录下（kafka_2.12-2.0.0）cmd：    .\bin\windows\kafka-server-start.bat .\config\server.properties</p>

<p>1、创建主题</p>

<p> 进入Kafka安装目录D:\Kafka\kafka_2.12-2.0.0，按下Shift+右键，选择“打开命令窗口”选项，打开命令行，输入：</p>

<pre>
.\bin\windows\kafka-topics.bat --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test</pre>

<p>查看主题输入：</p>

<pre>
.\bin\windows\kafka-topics.bat --list --zookeeper localhost:2181</pre>

<p>2、创建生产者，进入Kafka安装目录D:\Kafka\kafka_2.12-2.0.0，按下Shift+右键，选择“打开命令窗口”选项，打开命令行，输入：</p>

<pre>
.\bin\windows\kafka-console-producer.bat --broker-list localhost:9092 --topic test</pre>

<p>3、 创建消费者，进入Kafka安装目录D:\Kafka\kafka_2.12-2.0.0，按下Shift+右键，选择“打开命令窗口”选项，打开命令行，输入：</p>

<pre>
.\bin\windows\kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic test --from-beginning</pre>            </div>
                </div>