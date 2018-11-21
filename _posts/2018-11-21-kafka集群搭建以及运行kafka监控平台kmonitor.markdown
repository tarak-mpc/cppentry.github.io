---
layout:     post
title:      kafka集群搭建以及运行kafka监控平台kmonitor
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：欢迎转载					https://blog.csdn.net/feinifi/article/details/78413066				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>kafka集群搭建</p><p>kafka依赖zookeeper运行，在搭建kafka系统之前需要搭建zookeeper集群，这里先略过zookeeper集群搭建的过程。另外，kafka需要运行在jdk环境中，需要安装jdk,这里也不细说了。</p><p>kafka集群搭建很简单，只需在各自机器上开启各自的实例即可，稍微需要注意一点的就是配置文件中指定broker-id，两个broker-id需要不一样。</p><p>vi config/server.properties </p><pre><code class="language-plain">#设置broker-id
broker.id=2
#设置kafka日志位置
log.dirs=/data/kafka/kafka-logs
#设置zookeeperconnect
zookeeper.connect=linux-node1:2181,linux-node2:2181,linux-node3:2181</code></pre><p></p><p>另一台的配置就是修改一下broker.id=3即可。</p><p>启动zookeeper(略)</p><p>启动kafka(每台机器上运行以下命令)</p><pre><code class="language-plain">bin/kafka-server-start config/server.properties</code></pre><p>启动成功，可以通过zookeeper客户端工具登录，查看brokers会有两个节点。</p><p><img src="https://img-blog.csdn.net/20171102233111264?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZmVpbmlmaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""></p><p>方便后面测试，创建一个test的topic</p><pre><code class="language-plain">[root@linux-node3 kafka]# bin/kafka-topics.sh --create --zookeeper linux-node1:2181,linux-node2:2181,linux-node3:2181 --replication-factor 1 --partitions 1 --topic test
Created topic "test".</code></pre><p style="background:#ddd;"></p><p>kafka监控平台kmonitor搭建</p><p>需要下载KafkaOffsetMonitor-assembly-0.2.0.jar,启动该监控程序需要指定一些参数，比如zk连接zookeeper的信息，port服务开启之后监听的端口。为了方便启动，这里设置一个启动脚本，每次只需运行该脚本即可。</p><p>设置启动脚本</p><p>vi startup.sh</p><pre><code class="language-plain">#!/bin/sh
nohup java -cp KafkaOffsetMonitor-assembly-0.2.0.jar \
com.quantifind.kafka.offsetapp.OffsetGetterWeb \
--zk linux-node1:2181,linux-node2:2181,linux-node3:2181 \
--port 8090 \
--refresh 10.seconds \
--retain 1.days &gt; /dev/null &amp;</code></pre><p>启动</p><pre><code class="language-plain">./startup.sh</code></pre><p>访问url:http://192.168.61.21:8090/</p><p><img src="https://img-blog.csdn.net/20171102234940080?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZmVpbmlmaQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""></p>            </div>
                </div>