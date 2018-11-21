---
layout:     post
title:      STORM入门之（Flume Kafka集成架构）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/yl3395017/article/details/77581256				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h3>Flume</h3>
<p>Flume作用：收集日志</p>
<p>组件下载地址：http://archive.apache.org/dist/flume/1.6.0/ </p>
<p>版本：1.6.0以上集成Kafka</p>
<h4>Flume配置</h4>
<p></p>
<pre><code class="language-sql">a1.sources = r1
a1.sinks = k1
a1.channels = c1
 
a1.sources.r1.type = syslogudp
a1.sources.r1.port = 514
a1.sources.r1.host = 0.0.0.0

a1.sinks.k1.type = org.apache.flume.sink.kafka.KafkaSink
a1.sinks.k1.brokerList=10.2.4.12:9092,10.2.4.13:9092,10.2.4.14:9092
a1.sinks.k1.topic=test_rce_yjd
a1.sinks.k1.batchSize=20
 
a1.channels.c1.type = memory
a1.channels.c1.capacity = 10000
a1.channels.c1.transactionCapacity = 10000
 
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1</code></pre>
<h4>启动命令</h4>
<pre><code class="language-plain">/flume/apache-flume-1.6.0-bin/bin/flume-ng agent --conf conf --conf-file /flume/apache-flume-1.6.0-bin/conf/syslog.conf --name a1 -Dflume.root.logger=INFO,console</code></pre><br><p></p>
<h3>Kafka</h3>
<p>Kafka作用：缓存日志  </p>
<p>监听是否发送至集群中</p>
<p>（1）shell 传送门：http://blog.csdn.net/yl3395017/article/details/73650250</p>
<p>（2）消费 传送门：http://blog.csdn.net/yl3395017/article/details/77547881</p>
<p><br></p>
<h3>Storm</h3>
<p>Strom作用：流式处理</p>
<p>（1）消费Kafka集群信息 传送门：http://blog.csdn.net/yl3395017/article/details/77477716</p>
<p>效果</p>
<p><img src="https://img-blog.csdn.net/20170825170106935?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWwzMzk1MDE3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p><br></p>
            </div>
                </div>