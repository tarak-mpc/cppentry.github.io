---
layout:     post
title:      Linux下Kafka环境的搭建
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/xuzhelin/article/details/71515208				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-family:Arial;font-size:18px;">        </span><span style="font-family:Arial;"><span style="font-size:14px;">本文对Kafka做个简单介绍（详细的介绍在后续文章中体现），着重介绍下Linux下Kafka环境的搭建（单机），后续会补充集群环境下的配置。</span></span></p>
<h2><span style="font-family:Arial;font-size:18px;">        Kafka简介</span></h2>
<div><span style="font-family:Arial;font-size:18px;">             </span><span style="font-family:Arial;"><span style="font-size:14px;">Apache Kafka是分布式发布-订阅消息系统。Kafka是一种快速、可扩展的、设计内在就是分布式的，分区的和可复制的提交日志服务。</span></span></div>
<div><span style="font-family:Arial;"><span style="font-size:14px;">                Apache Kafka与传统消息系统相比，有以下不同：<br>
                   它被设计为一个分布式系统，易于向外扩展；<br>
                   它同时为发布和订阅提供高吞吐量；<br>
                   它支持多订阅者，当失败时能自动平衡消费者；<br>
                   它将消息持久化到磁盘，因此可用于批量消费，例如ETL，以及实时应用程序。</span></span></div>
<div><span style="font-family:Arial;font-size:18px;">                                                    <img src="https://img-blog.csdn.net/20170510104925582?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHV6aGVsaW4=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></div>
<div><span style="font-family:Arial;"><span style="font-size:14px;">                                                                                       图片来自官网</span></span></div>
<h2><span style="font-family:Arial;font-size:18px;">        安装JDK</span></h2>
<div><span style="font-family:Arial;"><span style="color:rgb(85,85,85);"><span style="font-size:18px;">               </span><span style="font-size:14px;">Kafka需要依赖JAVA环境运行，</span></span><span style="font-size:14px;"><a href="http://blog.csdn.net/xuzhelin/article/details/52512331" rel="nofollow" style="color:rgb(12,137,207);">JDK的安装</a><span style="color:rgb(85,85,85);">在之前的文章有做详细描述，仅供参考。</span></span></span></div>
<h2><span style="font-family:Arial;font-size:18px;">       下载Kafka版本</span></h2>
<div><span style="font-family:Arial;font-size:18px;"></span>
<div style="color:rgb(85,85,85);"><span style="font-size:18px;">              </span><span style="font-size:14px;"> 本文下载的是<a href="http://mirror.bit.edu.cn/apache/kafka/0.10.1.1/kafka_2.10-0.10.1.1.tgz" rel="nofollow">kafka_2.10-0.10.1.1.tgz</a>版本，可以使用wget下载，也可以自行下载。</span><span style="font-size:14px;">下载地址：http://kafka.apache.org/downloads</span></div>
</div>
<h2><span style="font-family:Arial;">        安装和配置</span></h2>
<div><span style="font-family:Arial;font-size:18px;"></span>
<div style="color:rgb(85,85,85);"><span style="font-size:18px;">              </span>
<span style="font-size:14px;">1、将包下载到相关的目录，然后解压Zookeeper到指定目录；</span></div>
<div style="color:rgb(85,85,85);"><span style="font-size:14px;">                     #cd /opt;</span></div>
<div style="color:rgb(85,85,85);"><span style="font-size:14px;">                     #tar -zxvf kafka_2.10-0.10.1.1.tgz;</span></div>
<div style="color:rgb(85,85,85);"><span style="font-size:14px;">                  2、配置日志：进入到/opt/</span><span style="font-size:14px;">kafka_2.10-0.10.1.1目录创建日志目录；</span></div>
<div style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="font-size:14px;">                     #cd /opt/kafka_2.10-0.10.1.1；</span></span></div>
<div style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="font-size:14px;">                     #mkdir logs；</span></span></div>
<div style="color:rgb(85,85,85);"><span style="font-size:14px;">                  3、修改kafka配置文件；</span></div>
<div style="color:rgb(85,85,85);"><span style="font-size:14px;">                       进入到/opt/kafka_2.10-0.10.1.1/config目录，修改server.properties。</span></div>
<div style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="font-size:14px;">                       #cd <span style="color:rgb(85,85,85);">/opt/kafka_2.10-0.10.1.1/config；</span></span></span></div>
<div style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="font-size:14px;">                       #vi server.properties #编辑修改相应的参数</span></span></span></div>
<div style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="font-size:14px;">                          broker.id=0<br>
                          port=9092 #端口号<br>
                          host.name=127.0.0.1 #服务器IP地址，修改为自己的服务器IP<br>
                          log.dirs=/opt/kafka_2.10-0.10.1.1/logs #日志存放路径，上面创建的目录<br>
                          zookeeper.connect=localhost:2181 #zookeeper地址和端口，单机配置部署，localhost:2181<br>
                       #:wq! #保存退出</span></span></span></div>
<div style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="font-size:14px;">                  4、启动Zookeeper：</span></span></span></div>
<div style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="font-size:14px;">                       Kafka支持内置的Zookeeper和引用外部的Zookeeper，本文使用外部的Zookeeper。</span></span></span></div>
<div style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="font-size:14px;">                       启动Zookeeper可以参考前面文章： <a href="http://blog.csdn.net/xuzhelin/article/details/71514618" rel="nofollow">Linux下Zookeeper的（单机）安装与简单测试</a>。</span></span></span></div>
<div style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="font-size:14px;">                  5、Kafka操作。</span></span></span></div>
<div style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="font-size:14px;">                       <strong>（1）、启动kafka命令：</strong></span></span></span></div>
<div style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="font-size:14px;">                                #<span style="color:rgb(85,85,85);">cd </span><span style="color:rgb(85,85,85);">/opt/kafka_2.10-0.10.1.1/bin;</span></span></span></span></div>
<div style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="font-size:14px;">                                # ./kafka-server-start.sh /opt/kafka_2.10-0.10.1.1/config/server.properties &amp;;</span></span></span></div>
<div style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="font-size:14px;"><span style="color:rgb(85,85,85);">                       <strong>（2）、停止kafka命令：</strong></span><br></span></span></span></div>
<div style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="font-size:14px;"><span style="color:rgb(85,85,85);">                                #</span><span style="color:rgb(85,85,85);"> ./kafka-server-stop.sh</span></span></span></span></div>
<div style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="font-size:14px;">                       <strong>（3）、创建Topic：(创建一个名为test的topic，只有一个副本，一个分区。)</strong></span></span></span></div>
<div style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="font-size:14px;">                                #./kafka-topics.sh --create --zookeeper 127.0.0.1:2181 --replication-factor 1 --partitions 1 --topic
 test</span></span></span></div>
<div style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="font-size:14px;">                       <strong>（4）、列出所有Topic：</strong></span></span></span></div>
<div style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="font-size:14px;">                                #./kafka-topics.sh -list -zookeeper 127.0.0.1:2181                  </span></span></span></div>
<div style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="font-size:14px;">                       <strong>（5）、启动Producer并发送消息：</strong></span></span></span></div>
<div style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="font-size:14px;">                                #./kafka-console-producer.sh --broker-list localhost:9092 --topic test</span></span></span></div>
<div style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="font-size:14px;">                               （输入相应的消息，eg:hello kafka;按Ctrl+C结束）</span></span></span></div>
<div style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="color:rgb(85,85,85);"><span style="font-size:14px;">                        <strong>（6）、启动Consumer并接收消息：</strong></span></span></span></div>
<div style="color:rgb(85,85,85);"><span style="font-size:14px;">                                #./kafka-console-consumer.sh --zookeeper 127.0.0.1:2181 --topic test --from-beginning</span></div>
</div>
<div><br></div>
            </div>
                </div>