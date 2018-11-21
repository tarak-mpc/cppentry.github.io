---
layout:     post
title:      window下Kafka最佳实践
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/chiyingluolei/article/details/53689322				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                Kafka的介绍和入门请看这里<a id="Header1_HeaderTitle" class="headermaintitle" href="http://www.cnblogs.com/likehua/" rel="nofollow"></a><a id="cb_post_title_url" style="font-size:1.5em;line-height:1.5;" href="http://www.cnblogs.com/likehua/p/3999538.html" rel="nofollow">kafka入门：简介、使用场景、设计原理、主要配置及集群搭建（转）</a>
<p>当前文章从实践的角度为大家规避window下使用的坑。</p>
<p>1.要求：</p>
<p>java 6+</p>
<p>2.下载kafka【注意，只用下载kafka】</p>
<p>http://mirrors.cnnic.cn/apache/kafka/0.9.0.1/kafka_2.11-0.9.0.1.tgz</p>
<p>3.解压，通过cmd进入kafka_2.11-0.9.0.1\bin\windows目录</p>
<p>4.启动zk</p>
<p>zookeeper-server-start ..\..\config\zookeeper.properties</p>
<p>测试连接一下</p>
<p>zookeeper-shell.bat 0.0.0.0:2181</p>
<p>5.启动Kafka</p>
<p>kafka-server-start.bat ..\..\config\server.properties</p>
<p><span style="line-height:1.5;">6)创建topic</span></p>
<div>kafka-topics.bat --create --zookeeper localhost:2181 --replication-factor 1 --partitions 3 --topic mytopic</div>
<div>
<div>
<div class="linenums">
<div class="L0"><span style="font-family:monospace;">参数的含义可以自查，当前无视</span></div>
<div class="L3"><code class="language-sh"><span class="typ">Created<span class="pln"> topic <span class="str">"mytopic"<span class="pun">.</span></span></span></span></code></div>
</div>
</div>
<div> </div>
</div>
<div>7)查看topic</div>
<div>kafka-topics.bat --describe --zookeeper localhost:2181 --topic mytopic</div>
<div>
<div>
<div class="linenums">
<div class="L0"><code class="language-sh"><span class="pln">----------------------------------------------------------</span></code></div>
<div class="L2"><code class="language-sh"><span class="typ">Topic<span class="pun">:<span class="pln">mytopic <span class="typ">PartitionCount<span class="pun">:<span class="lit">3<span class="pln"> <span class="typ">ReplicationFactor<span class="pun">:<span class="lit">1<span class="pln"> <span class="typ">Configs<span class="pun">:</span></span></span></span></span></span></span></span></span></span></span></span></span></code></div>
<div class="L3"><code class="language-sh"><span class="pln"> <span class="typ">Topic<span class="pun">:<span class="pln"> mytopic <span class="typ">Partition<span class="pun">:<span class="pln"> <span class="lit">0<span class="pln"> <span class="typ">Leader<span class="pun">:<span class="pln"> <span class="lit">0<span class="pln"> <span class="typ">Replicas<span class="pun">:<span class="pln"> <span class="lit">0<span class="pln"> <span class="typ">Isr<span class="pun">:<span class="pln"> <span class="lit">0</span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></code></div>
<div class="L4"><code class="language-sh"><span class="pln"> <span class="typ">Topic<span class="pun">:<span class="pln"> mytopic <span class="typ">Partition<span class="pun">:<span class="pln"> <span class="lit">1<span class="pln"> <span class="typ">Leader<span class="pun">:<span class="pln"> <span class="lit">0<span class="pln"> <span class="typ">Replicas<span class="pun">:<span class="pln"> <span class="lit">0<span class="pln"> <span class="typ">Isr<span class="pun">:<span class="pln"> <span class="lit">0</span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></code></div>
<div class="L5"><code class="language-sh"><span class="pln"> <span class="typ">Topic<span class="pun">:<span class="pln"> mytopic <span class="typ">Partition<span class="pun">:<span class="pln"> <span class="lit">2<span class="pln"> <span class="typ">Leader<span class="pun">:<span class="pln"> <span class="lit">0<span class="pln"> <span class="typ">Replicas<span class="pun">:<span class="pln"> <span class="lit">0<span class="pln"> <span class="typ">Isr<span class="pun">:<span class="pln"> <span class="lit">0</span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></code></div>
</div>
</div>
<div>8）发布/生产消息窗口</div>
</div>
<div>kafka-console-producer.bat --broker-list localhost:9092 --topic mytopic</div>
<div><span style="line-height:1.5;"> </span></div>
<div>9）消费消息窗口</div>
<div>
<div>kafka-console-consumer.bat --zookeeper localhost:2181 --from-beginning --topic mytopic</div>
<div> </div>
<div>10）在生产者producer窗口输入，可以在consumer窗口看到输出</div>
<div>
<div>
<div class="linenums">
<div class="L0"><code class="language-sh"><span class="pln"><span class="pun"><span class="pln"><span class="pun"><span class="lit"><span class="pun"><span class="lit"><span class="pun"><span class="lit"><span class="pln"><span class="pun"><span class="lit"><span class="pun"><span class="lit"><span class="pun"><span class="lit"><span class="pln">b<span class="kwd">in<span class="pln">\windows<span class="pun">&gt;<span class="pln">kafka<span class="pun">-<span class="pln">console</span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></code><code class="language-sh"><span class="pun">-<span class="pln">producer<span class="pun">.<span class="pln">bat <span class="pun">--<span class="pln">broker<span class="pun">-<span class="pln">list localhost<span class="pun">:<span class="lit">9092<span class="pln"> <span class="pun">--<span class="pln">topic mytopic</span></span></span></span></span></span></span></span></span></span></span></span></span></code></div>
<div class="L2">--------------------------------------------------------------------</div>
<div class="L4">
<p>你真坚决<br>niaho<br>heelo<br>我是中文啊</p>

</div>

</div>

</div>
<div>
<div class="linenums">
<div class="L0"><code class="language-sh"><span class="pln"><span class="pun"><span class="pln"><span class="pun"><span class="lit"><span class="pun"><span class="lit"><span class="pun"><span class="lit"><span class="pln"><span class="pun"><span class="lit"><span class="pun"><span class="lit"><span class="pun"><span class="lit"><span class="pln">b<span class="kwd">in<span class="pln">\windows<span class="pun">&gt;<span class="pln">kafka<span class="pun">-<span class="pln">console</span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></code><code class="language-sh"><span class="pun">-<span class="pln">consumer<span class="pun">.<span class="pln">bat <span class="pun">--<span class="pln">zookeeper localhost<span class="pun">:<span class="lit">2181<span class="pln"> <span class="pun">--<span class="pln">from<span class="pun">-<span class="pln">beginning <span class="pun">--<span class="pln">topic mytopic</span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></code></div>
<div class="L2">
<p>--------------------------------------------------------------------</p>
<p>你真坚决<br>niaho<br>heelo<br>我是中文啊</p>

</div>

</div>

</div>

</div>

</div>
<p>--------------------------------------------------------------------</p>
<p>以上内容参考这里：http://kafka.apache.org/documentation.html#quickstart</p>
<p>ok,很完美.</p>
<p>出错问题：Unrecognized VM option UseG1GC 这些优化参数其实都是新版的，所以要么用jdk1.7+，要么去掉。</p>
<p>解决办法：打开conf\kafka-run-class.bat，找到102行，修改如下</p>
<div class="cnblogs_code">
<pre>IF [<span style="color:#800000;">"</span><span style="color:#800000;">%KAFKA_JVM_PERFORMANCE_OPTS%</span><span style="color:#800000;">"</span>] EQU [<span style="color:#800000;">""</span><span style="color:#000000;">] (
    rem set KAFKA_JVM_PERFORMANCE_OPTS</span>=-server -XX:+UseG1GC -XX:MaxGCPauseMillis=<span style="color:#800080;">20</span> -XX:InitiatingHeapOccupancyPercent=<span style="color:#800080;">35</span> -XX:+DisableExplicitGC -Djava.awt.headless=<span style="color:#0000ff;">true</span><span style="color:#000000;">
    set KAFKA_JVM_PERFORMANCE_OPTS</span>=-server -XX:+DisableExplicitGC -Djava.awt.headless=<span style="color:#0000ff;">true</span><span style="color:#000000;">
)</span></pre>
</div>
<p> </p>
<p>使用java调用kafka，参考http://blog.csdn.net/hxpjava1/article/details/19160665。</p>
<p>Kafka与spring集成，参考http://colobu.com/2014/11/19/kafka-spring-integration-in-practice/</p>            </div>
                </div>