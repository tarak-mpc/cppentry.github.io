---
layout:     post
title:      Karaf 实践指南 Kafka 安装 Karaf 学习 Kafka 帮助
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>公司很多产品有在使用Kafka进行数据的处理，因为各种原因，没有在产品中有用到这快，偶尔，自己去研究下，做个文档进行记录： <br>
    本文是在一个机器上进行的kafka集群，分为三个节点，并且测试peoducer、cunsumer在正常和非正常情况下的测试：</p>



<h2 id="1-下载和安装kafka">1. 下载和安装Kafka</h2>

<pre><code>    &gt; mkdir kafka
    &gt; cd kafka
    &gt; wget https://archive.apache.org/dist/kafka/0.8.0/kafka_2.8.0-0.8.0.tar.gz
    &gt; tar -xzvf kafka_2.8.0-0.8.0.tar.gz
    Ok，Kafka安装完成，比较简单。
</code></pre>



<h2 id="2-启动单节点的zookeeper">2. 启动单节点的zookeeper</h2>

<pre><code>    &gt; $ nohup bin/zookeeper-server-start.sh config/zookeeper.properties &amp;
</code></pre>



<h2 id="3-准备启动一个3个broker节点的kafka集群因此做如下配置">3. 准备启动一个3个broker节点的kafka集群，因此做如下配置</h2>

<pre><code>    &gt;cp config/server.properties config/server-1.properties 
    &gt;cp config/server.properties config/server-2.properties
 修改下面的如下参数：
</code></pre>



<pre class="prettyprint"><code class=" hljs vbscript">    <span class="hljs-built_in">server</span>-<span class="hljs-number">1.</span>properties 修改：
        config/<span class="hljs-built_in">server</span>-<span class="hljs-number">1.</span>properties:
        broker.id=<span class="hljs-number">1</span>
        port=<span class="hljs-number">9093</span>
        <span class="hljs-built_in">log</span>.dir=/tmp/kafka-logs-<span class="hljs-number">1</span>
    <span class="hljs-built_in">server</span>-<span class="hljs-number">2.</span>properties 修改：
        config/<span class="hljs-built_in">server</span>-<span class="hljs-number">2.</span>properties:
        broker.id=<span class="hljs-number">2</span>
        port=<span class="hljs-number">9094</span>
        <span class="hljs-built_in">log</span>.dir=/tmp/kafka-logs-<span class="hljs-number">2</span>
    顺便说明下：
        broker.id: broker节点的唯一标识
        port: broker节点使用端口号
        <span class="hljs-built_in">log</span>.dir: 消息目录位置
</code></pre>



<h2 id="4-启动3个broker-节点">4. 启动3个broker 节点</h2>

<pre><code>&gt;JMX_PORT=9997 bin/kafka-server-start.sh config/server-1.properties &amp;
&gt;JMX_PORT=9998 bin/kafka-server-start.sh config/server-2.properties &amp;
&gt;JMX_PORT=9999 bin/kafka-server-start.sh config/server.properties &amp;
</code></pre>

<p>5. 创建topic并查看</p>

<blockquote>
  <p>bin/kafka-create-topic.sh –zookeeper localhost:2181 –replica 3 –partition 1 –topic 3test <br>
  bin/kafka-list-topic.sh –zookeeper localhost:2181</p>
</blockquote>

<p><img src="https://img-blog.csdn.net/20150702204558701" alt="这里写图片描述" title=""></p>

<p>6.启动consumer &amp; producer，并在producer启动后的console输入一些信息</p>

<blockquote>
  <p>bin/kafka-console-consumer.sh –zookeeper localhost:2181 –from-beginning –topic 3test <br>
  messageA <br>
  messageB <br>
  messageC</p>
</blockquote>

<pre><code>&gt;bin/kafka-console-producer.sh --broker-list localhost:9092,localhost:9093,localhost:9094 --topic 3test
&gt;messageA
&gt;messageB
&gt;messageC

验证下，prodocer发送的数据在consumer中是不是能正常消费。
</code></pre>

<p>Ok，配置基本结束，你可以尝试在干掉（pkill -9 -f server-1.properties)，然后可以继续消费和生产，看看是不是能正常。 <br>
顺便说下，查看topic：</p>

<blockquote>
  <p>bin/kafka-list-topic.sh –zookeeper localhost:2181</p>
</blockquote>

<p><img src="https://img-blog.csdn.net/20150702204824508" alt="这里写图片描述" title=""></p>

<p><img src="https://img-blog.csdn.net/20150702205140657" alt="这里写图片描述" title=""></p>

<p><img src="https://img-blog.csdn.net/20150702205149970" alt="这里写图片描述" title=""> <br>
结束！QQ：154833488</p>

<p>如下是官网的介绍： <br>
    <a href="http://kafka.apache.org/07/quickstart.html" rel="nofollow">http://kafka.apache.org/07/quickstart.html</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>