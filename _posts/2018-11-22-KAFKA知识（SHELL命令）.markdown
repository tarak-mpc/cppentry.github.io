---
layout:     post
title:      KAFKA知识（SHELL命令）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/yl3395017/article/details/73650250				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h3>Kafka启动与停止</h3>
<pre><code class="language-java">broker：9092，zookeeper：2181</code></pre>
<p>启动kafka命令：</p>
<p></p>
<pre><code class="language-plain">nohup /soc/kafka/bin/kafka-server-start.sh /soc/kafka/config/server.properties &gt;/dev/null 2&gt;&amp;1 &amp;</code></pre>
<p></p>
<p>停止kafka命令：</p>
<pre><code class="language-java">pkill -9 -f config/server.properties</code></pre>
<p><span style="color:#ff0000;">注意： server.properties为相对路径 根据实际情况修改</span></p>
<h3>Kafka基本操作</h3>
<span style="color:#ff0000;">注意：Logs为主题topic 根据实际情况修改</span><br>
消费监控命令：<pre><code class="language-java">/kafka/bin/kafka-console-consumer.sh --zookeeper 10.2.4.12:2181,10.2.4.13:2181,10.2.4.14:2181 --topic Logs </code></pre>生产监控命令：<pre><code class="language-java">/kafka/bin/kafka-console-producer.sh --broker-list 10.2.4.12:9092,10.2.4.13:9092,10.2.4.14:9092 --topic Logs</code></pre>创建topic命令：<br><pre><code class="language-java">/kafka/bin/kafka-topics.sh --create --zookeeper 10.2.4.12:2181,10.2.4.13:2181,10.2.4.14:2181 --replication-factor 1 --partitions 1 --topic 
Logs</code></pre><pre><code class="language-java">/kafka/bin/kafka-topics.sh --create --zookeeper 10.2.4.12:2181,10.2.4.13:2181,10.2.4.14:2181 --replication-factor 3 --partitions 3 --topic 
Logs</code></pre>
<p><span style="color:#ff0000;">这条命令会创建一个名为Logs的topic，有3个分区，每个分区需分配3个副本，replication-factor 为副本参数，partitions为分区参数。</span></p>
<p><br></p>
<h3>Kafka主题删除方案</h3>
方案一删除：<br><pre><code class="language-java">（1）/kafka/bin/kafka-run-class.sh kafka.admin.DeleteTopicCommand --topic Logs --zookeeper 10.2.4.12:2181,10.2.4.13:2181,10.2.4.14:2181</code></pre><pre><code class="language-java">（2）/kafka/bin/kafka-topics.sh --zookeeper 10.2.4.12:2181,10.2.4.13:2181,10.2.4.14:2181 --delete --topic adtest
</code></pre>
<p><span style="color:#ff0000;">kafka 删除topic 提示marked for deletion 没有被真正删除 如果需要真正删除 需要在server.properties 配置标签delete.topic.enable=true</span></p>
<p>方案二测底删除：</p>
<p>（1）登录zookeeper客户端：</p>
<pre><code class="language-java">./bin/zookeeper-client</code></pre>（2）找到topic所在的目录：<pre><code class="language-java">ls /brokers/topics</code></pre>（3）找到要删除的topic，执行命令：<pre><code class="language-java">rmr /brokers/topics/【topic name】</code></pre>即可，此时topic被彻底删除。<br><br><h3>Kafka查看主题命令</h3>
查看当前存在那些topic命令：<pre><code class="language-java">/kafka/bin/kafka-topics.sh --list --zookeeper 10.2.4.12:2181,10.2.4.13:2181,10.2.4.14:2181</code></pre>查看topic分区详情命令：<pre><code class="language-java">/kafka/bin/kafka-topics.sh --describe --zookeeper 10.2.4.12:2181,10.2.4.13:2181,10.2.4.14:2181 --topic Logs</code></pre><br>            </div>
                </div>