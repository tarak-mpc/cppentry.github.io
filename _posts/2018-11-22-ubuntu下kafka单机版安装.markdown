---
layout:     post
title:      ubuntu下kafka单机版安装
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h3>相关环境：</h3>

<p>ubuntu版本：16.04</p>

<p>kafka版本：kafka_2.11-0.11.0.0</p>

<h2>1、安装步骤</h2>

<h3>1、将解压后的kafka文件夹复制到/usr/local下面</h3>

<h3><a name="t4"></a>2、进入到/usr/local/kafka/conf</h3>

<p>        cd /usr/local/kafka/conf</p>

<h3><a name="t5"></a>3、更改配置文件</h3>

<p>        vim server.properties</p>

<p>1、以下是是开启远程客户端访问kafka</p>

<p><img alt="" class="has" height="129" src="https://img-blog.csdn.net/20180926111515405?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1NTcxNTU0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="488"></p>

<p>2、修改与zookeeper服务器之间的配置信息，主要是端口号（默认是2180，我自己修改成了9999）</p>

<p><img alt="" class="has" height="133" src="https://img-blog.csdn.net/20180926104818739?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1NTcxNTU0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="425"></p>

<p>4、启动kafka（前提是启动了zookeeper服务器  zkServer.sh  start zoo.cfg）</p>

<p>      以下命令需要进入到/usr/local/kafka/目录下执行</p>

<p>  1、启动kafka</p>

<p>   bin/kafka-server-start.sh config/server.properties</p>

<p><img alt="" class="has" height="295" src="https://img-blog.csdn.net/20180926105236238?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1NTcxNTU0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1200"></p>

<p>  2、创建一个主题test</p>

<p>       bin/kafka-topics.sh --create --zookeeper localhost:9999 --replication-factor 1 --partitions 1 --topic test</p>

<p>  3、开启一个生产者，并发送数据</p>

<p>      bin/kafka-console-producer.sh --broker-list 132.232.28.164:9092 --topic test </p>

<p>    <img alt="" class="has" height="225" src="https://img-blog.csdn.net/20180926105406279?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1NTcxNTU0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="914"></p>

<p>   4、开启一个消费者</p>

<p>      bin/kafka-console-consumer.sh --zookeeper 132.232.28.164:9999 --topic test </p>

<p><img alt="" class="has" height="248" src="https://img-blog.csdn.net/2018092610543464?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1NTcxNTU0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1200"></p>

<p>     配置完成，可以通过客户端程序进行测试！</p>

<p> </p>            </div>
                </div>