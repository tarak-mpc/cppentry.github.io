---
layout:     post
title:      linux 搭建 kafka集群
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2><span style="font-family:'Microsoft YaHei';">1. 下载kafka<br></span></h2>
<p><span style="font-family:'Microsoft YaHei';">wget <a href="http://mirror.bit.edu.cn/apache/kafka/0.10.2.0/kafka_2.11-0.10.2.0.tgz" rel="nofollow">
<strong>http://mirror.bit.edu.cn/apache/kafka/0.10.2.0/kafka_2.11-0.10.2.0.tgz</strong></a><br></span></p>
<p><span style="font-family:'Microsoft YaHei';"><br></span></p>
<h2><span style="font-family:'Microsoft YaHei';">2.解压缩</span></h2>
<p><span style="font-family:'Microsoft YaHei';">tar -zxvf kafka_2.11-0.10.2.0.tgz</span></p>
<p><span style="font-family:'Microsoft YaHei';"><br></span></p>
<h2><span style="font-family:'Microsoft YaHei';">3.创建项目目录和kafka消息目录</span></h2>
<p><span style="font-family:'Microsoft YaHei';">mkdir kafka-logs</span></p>
<br><p><span style="font-family:'Microsoft YaHei';"><br></span></p>
<h2><span style="font-family:'Microsoft YaHei';">4.配置文件修改</span></h2>
<p><span style="font-family:'Microsoft YaHei';">cd soft-kafka/kafka_2.11-0.10.2.0/config</span></p>
<p><span style="font-family:'Microsoft YaHei';">vi server.properties</span></p>
<p><span style="font-family:'Microsoft YaHei';">修改</span></p>
<p><span style="font-family:'Microsoft YaHei';">#broker.id=0  注释掉，每台服务器的broker.id都不能相同,</span></p>
<span style="font-family:'Microsoft YaHei';">也可以自己自定义，不能相同</span><br><p><span style="font-family:'Microsoft YaHei';"><br>
#在log.retention.hours=168 下面新增下面三项     <br>
message.max.byte=5242880 <br>
default.replication.factor=2 <br>
replica.fetch.max.bytes=5242880<br><br>
#设置zookeeper的连接端口<br><br>
zookeeper.connect=192.168.0.15:12181,192.168.0.16:12181:12181,192.168.0.17:12181</span></p>
<p><span style="font-family:'Microsoft YaHei';"><br></span></p>
<h2><span style="font-family:'Microsoft YaHei';">5.启动zookeeper和kafka server</span></h2>
<p><span style="font-family:'Microsoft YaHei';">./bin/zookeeper-server-start.sh config/zookeeper.properties &amp;<br></span></p>
<p><span style="font-family:'Microsoft YaHei';">./bin/kafka-server-start.sh config/server.properties</span></p>
<p><span style="font-family:'Microsoft YaHei';"><br></span></p>
<h2><span style="font-family:'Microsoft YaHei';">6.测试生产者和消费者</span></h2>
<h4><span style="font-family:'Microsoft YaHei';">6.1 创建topic</span></h4>
<p><span style="font-family:'Microsoft YaHei';">     </span></p>
<pre><code class="language-html">bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test</code></pre><br><p></p>
<h4><span style="font-family:'Microsoft YaHei';">6.2 查看topic</span></h4>
<p><span style="font-family:'Microsoft YaHei';"></span></p>
<pre><code class="language-html">bin/kafka-topics.sh --list --zookeeper localhost:2181</code></pre><br><p></p>
<h4><span style="font-family:'Microsoft YaHei';">6.3 启动生产者</span></h4>
<p><span style="font-family:'Microsoft YaHei';"></span></p>
<pre><code class="language-html">bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test</code></pre>
<p></p>
<h4><span style="font-family:'Microsoft YaHei';">6.4 启动消费者</span></h4>
<p><span style="font-family:'Microsoft YaHei';"></span></p>
<pre><code class="language-html">bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning</code></pre><br><h2>7.注意事项</h2>
<p>出现下面异常，请将localhost换成具体ip<br></p>
<p></p><pre><code class="language-html">[2017-06-06 14:03:17,516] ERROR Error when sending message to topic test with key: null, value: 4 bytes with error: (org.apache.kafka.clients.producer.internals.ErrorLoggingCallback)
org.apache.kafka.common.errors.TimeoutException: Failed to update metadata after 60000 ms.</code></pre><br><br><p></p>
            </div>
                </div>