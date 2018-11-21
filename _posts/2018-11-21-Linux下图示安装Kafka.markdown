---
layout:     post
title:      Linux下图示安装Kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/HcJsJqJSSM/article/details/83894009				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h3>一：Kafka的下载安装.</h3>

<h3>       1. <a href="https://www.apache.org" rel="nofollow">Kafka官网</a>.</h3>

<h3>       2. 官网下载.</h3>

<p>       <img alt="" class="has" height="702" src="https://img-blog.csdnimg.cn/20181109103955648.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hjSnNKcUpTU00=,size_16,color_FFFFFF,t_70" width="1200"></p>

<p><img alt="" class="has" height="700" src="https://img-blog.csdnimg.cn/20181109104052128.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hjSnNKcUpTU00=,size_16,color_FFFFFF,t_70" width="1200"></p>

<p><img alt="" class="has" height="705" src="https://img-blog.csdnimg.cn/20181109104134658.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hjSnNKcUpTU00=,size_16,color_FFFFFF,t_70" width="1200"></p>

<h3>    3. Linux下wget+url即可.</h3>

<h3>     wget http://mirror.bit.edu.cn/apache/kafka/2.0.0/kafka_2.12-2.0.0.tgz</h3>

<p>      <img alt="" class="has" height="312" src="https://img-blog.csdnimg.cn/20181109104813390.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hjSnNKcUpTU00=,size_16,color_FFFFFF,t_70" width="1127"></p>

<p><img alt="" class="has" height="428" src="https://img-blog.csdnimg.cn/20181109104845725.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hjSnNKcUpTU00=,size_16,color_FFFFFF,t_70" width="1124"></p>

<h3>   4. 解压到指定的目录.</h3>

<h3>    tar -zxvf kafka_2.12-2.0.0.tgz -C /usr/local</h3>

<p>   <img alt="" class="has" height="565" src="https://img-blog.csdnimg.cn/20181109105147324.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hjSnNKcUpTU00=,size_16,color_FFFFFF,t_70" width="1128"></p>

<p><img alt="" class="has" height="585" src="https://img-blog.csdnimg.cn/20181109105227665.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hjSnNKcUpTU00=,size_16,color_FFFFFF,t_70" width="1128"></p>

<h3>Kafka的目录.</h3>

<p><img alt="" class="has" height="243" src="https://img-blog.csdnimg.cn/20181109105609412.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hjSnNKcUpTU00=,size_16,color_FFFFFF,t_70" width="1125"></p>

<h3>配置文件的目录.(config)</h3>

<p><img alt="" class="has" height="368" src="https://img-blog.csdnimg.cn/20181109105740720.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hjSnNKcUpTU00=,size_16,color_FFFFFF,t_70" width="1126"></p>

<h3>二 . Kafka的配置启动.</h3>

<h3>      2.1 vim server.properties.</h3>

<h3>      broker.id=0(broker的全局唯一编号,不能重复).</h3>

<h3>      log.dirs=/tmp/kafka-logs</h3>

<h3>      zookeeper.connect=192.168.217.134:2181(ZooKeeper集群配置多个).</h3>

<h3>      zookeeper.connection.timeout.ms=6000</h3>

<h3>      2.2 启动Zookeeper. <img alt="" class="has" height="287" src="https://img-blog.csdnimg.cn/20181109114240684.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hjSnNKcUpTU00=,size_16,color_FFFFFF,t_70" width="1124"></h3>

<h3>     2.3 启动Kafka.</h3>

<h3>       kafka目录下：sh bin/kafka-server-start.sh config/server.properties</h3>

<p><img alt="" class="has" height="612" src="https://img-blog.csdnimg.cn/2018110911500421.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hjSnNKcUpTU00=,size_16,color_FFFFFF,t_70" width="1127"></p>

<h3>         jps(另外一个窗口执行命令)</h3>

<p><img alt="" class="has" height="96" src="https://img-blog.csdnimg.cn/20181109115039395.png" width="1135"></p>

<h3> 启动生产者：(生产三条消息.Hello,Kafka,Test).</h3>

<h3> sh kafka-console-producer.sh --broker-list localhost:9092 --topic testkafka</h3>

<p> <img alt="" class="has" height="289" src="https://img-blog.csdnimg.cn/20181109120614471.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hjSnNKcUpTU00=,size_16,color_FFFFFF,t_70" width="1126"></p>

<h3>启动消费者：</h3>

<h3>0.9以下的版本使用这个--zookeeper参数.</h3>

<p><img alt="" class="has" height="332" src="https://img-blog.csdnimg.cn/20181109120933357.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hjSnNKcUpTU00=,size_16,color_FFFFFF,t_70" width="1130"></p>

<h3>0.9以上的版本使用如下的方式启动消费者.</h3>

<h3>sh kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic testkafka --from-beginning</h3>

<p><img alt="" class="has" height="156" src="https://img-blog.csdnimg.cn/20181109121223211.png" width="1137"></p>

<h3>输出三条消息.</h3>

<h3>启动三个终端.（启动一个kafka Server一个终端,生产者一个终端,消费者一个终端）.</h3>

<p><img alt="" class="has" height="608" src="https://img-blog.csdnimg.cn/20181109121543791.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hjSnNKcUpTU00=,size_16,color_FFFFFF,t_70" width="1131"></p>

<p><img alt="" class="has" height="56" src="https://img-blog.csdnimg.cn/20181109121617942.png" width="1129"></p>

<p>关闭（Ctrl+C）.</p>

<p><img alt="" class="has" height="611" src="https://img-blog.csdnimg.cn/20181109121844839.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hjSnNKcUpTU00=,size_16,color_FFFFFF,t_70" width="1125"></p>            </div>
                </div>