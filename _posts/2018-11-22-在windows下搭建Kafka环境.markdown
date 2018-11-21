---
layout:     post
title:      在windows下搭建Kafka环境
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1>在windows下搭建Kafka环境</h1>

<p>1.从官方网站上分别下载zookeeper和kafka的压缩包</p>

<p>kafka：<a href="http://kafka.apache.org/downloads" rel="nofollow">http://kafka.apache.org/downloads</a></p>

<p>zookeeper:<a href="http://kafka.apache.org/downloads.html" rel="nofollow">http://kafka.apache.org/downloads.html</a></p>

<p> </p>

<p>2.把下载下来的kafka和zookeeper分别解压缩到自己的盘符上这里我解压到了d盘：</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180719122856578?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1czQ2hoaGhoaA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p> </p>

<p>3.配置相关的环境变量，这里需要配置一下zookeeper的环境变量</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180719123034451?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1czQ2hoaGhoaA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>接着打开zookeeper的conf文件夹，将zoo_sample.cfg复制一份重命名为zoo.cfg,</p>

<p>并修改如下内容，保存：</p>

<pre class="has">
<code class="language-bash">dataDir=D:/zookeeper-3.4.12/data</code></pre>

<p>修改kafka的conf文件夹下面的server.properties文件：</p>

<pre class="has">
<code class="language-bash">log.dirs=D:/kafka_2.11-1.1.0/kafka-logs</code></pre>

<p> </p>

<p>4.打开控制台启动zookeeper的服务：</p>

<pre class="has">
<code class="language-bash">zkserver</code></pre>

<p> </p>

<p>启动kafka服务：</p>

<pre class="has">
<code>D:\kafka_2.11-1.1.0\bin\windows&gt;kafka-server-start.bat ../../config/server.properties</code></pre>

<p>5. 创建一个topic：</p>

<pre class="has">
<code>D:\kafka_2.11-1.1.0\bin\windows&gt;kafka-topics.bat --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test</code></pre>

<p>6.创建一个producer:</p>

<pre class="has">
<code>D:\kafka_2.11-1.1.0\bin\windows&gt;kafka-console-producer.bat --broker-list localhost:9092 --topic test</code></pre>

<p>7.创建一个customer：</p>

<pre class="has">
<code>D:\kafka_2.11-1.1.0\bin\windows&gt;kafka-console-consumer.bat --zookeeper localhost:2181 --topic test</code></pre>

<p> </p>

<p>8.测试kafka环境：</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180719124911751?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1czQ2hoaGhoaA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>            </div>
                </div>