---
layout:     post
title:      Kafka（二）：Kafka安装与使用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，转载请注明出处http://blog.csdn.net/saytime					https://blog.csdn.net/saytime/article/details/79950599				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h4 id="一安装kafka">一、安装Kafka</h4>

<p>官方快速安装文档：<a href="http://kafka.apache.org/quickstart" rel="nofollow">http://kafka.apache.org/quickstart</a></p>



<h5 id="安装kafka">安装kafka</h5>



<pre class="prettyprint"><code class=" hljs ruby">下载最新版本kafka
[root<span class="hljs-variable">@Basic</span> apps]<span class="hljs-comment"># wget http://mirrors.shu.edu.cn/apache/kafka/1.1.0/kafka_2.11-1.1.0.tgz</span>
[root<span class="hljs-variable">@Basic</span> apps]<span class="hljs-comment"># tar -zxvf kafka_2.11-1.1.0.tgz </span></code></pre>



<h5 id="启动zookeeper服务">启动zookeeper服务</h5>



<pre class="prettyprint"><code class=" hljs avrasm">[root@Basic apps]<span class="hljs-preprocessor"># cd kafka_2.11-1.1.0</span>

如果本地有zookeeper，可以启动本地安装的zookeeper，如果没有就使用kafka自带的zookeeper
[root@Basic kafka_2<span class="hljs-number">.11</span>-<span class="hljs-number">1.1</span><span class="hljs-number">.0</span>]<span class="hljs-preprocessor"># bin/zookeeper-server-start.sh config/zookeeper.properties</span>


[<span class="hljs-number">2018</span>-<span class="hljs-number">02</span>-<span class="hljs-number">27</span> <span class="hljs-number">05</span>:<span class="hljs-number">43</span>:<span class="hljs-number">09</span>,<span class="hljs-number">969</span>] INFO tickTime <span class="hljs-keyword">set</span> to <span class="hljs-number">3000</span> (org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.zookeeper</span><span class="hljs-preprocessor">.server</span><span class="hljs-preprocessor">.ZooKeeperServer</span>)
[<span class="hljs-number">2018</span>-<span class="hljs-number">02</span>-<span class="hljs-number">27</span> <span class="hljs-number">05</span>:<span class="hljs-number">43</span>:<span class="hljs-number">09</span>,<span class="hljs-number">970</span>] INFO minSessionTimeout <span class="hljs-keyword">set</span> to -<span class="hljs-number">1</span> (org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.zookeeper</span><span class="hljs-preprocessor">.server</span><span class="hljs-preprocessor">.ZooKeeperServer</span>)
[<span class="hljs-number">2018</span>-<span class="hljs-number">02</span>-<span class="hljs-number">27</span> <span class="hljs-number">05</span>:<span class="hljs-number">43</span>:<span class="hljs-number">09</span>,<span class="hljs-number">971</span>] INFO maxSessionTimeout <span class="hljs-keyword">set</span> to -<span class="hljs-number">1</span> (org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.zookeeper</span><span class="hljs-preprocessor">.server</span><span class="hljs-preprocessor">.ZooKeeperServer</span>)
[<span class="hljs-number">2018</span>-<span class="hljs-number">02</span>-<span class="hljs-number">27</span> <span class="hljs-number">05</span>:<span class="hljs-number">43</span>:<span class="hljs-number">10</span>,<span class="hljs-number">060</span>] INFO binding to port <span class="hljs-number">0.0</span><span class="hljs-number">.0</span><span class="hljs-number">.0</span>/<span class="hljs-number">0.0</span><span class="hljs-number">.0</span><span class="hljs-number">.0</span>:<span class="hljs-number">2181</span> (org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.zookeeper</span><span class="hljs-preprocessor">.server</span><span class="hljs-preprocessor">.NIOServerCnxnFactory</span>)
</code></pre>



<h5 id="启动kafka服务">启动kafka服务</h5>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@Basic</span> kafka_2.<span class="hljs-number">11</span>-<span class="hljs-number">1.1</span>.<span class="hljs-number">0</span>]<span class="hljs-comment"># bin/kafka-server-start.sh config/server.properties</span></code></pre>



<h4 id="二kafka操作">二、Kafka操作</h4>



<h5 id="1-创建topic">1. 创建Topic</h5>

<blockquote>
  <p>创建一个名叫kafka_test， 一个复制因子一个分区的Topic</p>
</blockquote>



<pre class="prettyprint"><code class=" hljs vhdl">[root@Basic kafka_2<span class="hljs-number">.11</span>-<span class="hljs-number">1.1</span><span class="hljs-number">.0</span>]# bin/kafka-topics.sh <span class="hljs-comment">--create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic kafka_test</span>
WARNING: Due <span class="hljs-keyword">to</span> limitations <span class="hljs-keyword">in</span> metric names, topics <span class="hljs-keyword">with</span> a period ('.') <span class="hljs-keyword">or</span> underscore ('_') could collide. <span class="hljs-keyword">To</span> avoid issues it <span class="hljs-keyword">is</span> best <span class="hljs-keyword">to</span> <span class="hljs-keyword">use</span> either, but <span class="hljs-keyword">not</span> both.
Created topic <span class="hljs-string">"kafka_test"</span>.</code></pre>



<h5 id="2-列出所有topic">2. 列出所有Topic</h5>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-title">[</span><span class="hljs-comment">root@Basic</span> <span class="hljs-comment">kafka_2</span><span class="hljs-string">.</span><span class="hljs-comment">11</span><span class="hljs-literal">-</span><span class="hljs-comment">1</span><span class="hljs-string">.</span><span class="hljs-comment">1</span><span class="hljs-string">.</span><span class="hljs-comment">0</span><span class="hljs-title">]</span><span class="hljs-comment">#</span> <span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">list</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">localhost:2181</span>
<span class="hljs-comment">kafka_test</span></code></pre>



<h5 id="3-连接生产者发送消息">3. 连接生产者，发送消息</h5>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-title">[</span><span class="hljs-comment">root@Basic</span> <span class="hljs-comment">kafka_2</span><span class="hljs-string">.</span><span class="hljs-comment">11</span><span class="hljs-literal">-</span><span class="hljs-comment">1</span><span class="hljs-string">.</span><span class="hljs-comment">1</span><span class="hljs-string">.</span><span class="hljs-comment">0</span><span class="hljs-title">]</span><span class="hljs-comment">#</span> <span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">console</span><span class="hljs-literal">-</span><span class="hljs-comment">producer</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">broker</span><span class="hljs-literal">-</span><span class="hljs-comment">list</span> <span class="hljs-comment">localhost:9092</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">kafka_test</span>

&gt; <span class="hljs-comment">hello</span><span class="hljs-string">,</span> <span class="hljs-comment">kafka</span></code></pre>



<h5 id="4-创建一个消费者接收消息">4. 创建一个消费者，接收消息</h5>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-title">[</span><span class="hljs-comment">root@Basic</span> <span class="hljs-comment">kafka_2</span><span class="hljs-string">.</span><span class="hljs-comment">11</span><span class="hljs-literal">-</span><span class="hljs-comment">1</span><span class="hljs-string">.</span><span class="hljs-comment">1</span><span class="hljs-string">.</span><span class="hljs-comment">0</span><span class="hljs-title">]</span><span class="hljs-comment">#</span> <span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">console</span><span class="hljs-literal">-</span><span class="hljs-comment">consumer</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">bootstrap</span><span class="hljs-literal">-</span><span class="hljs-comment">server</span> <span class="hljs-comment">localhost:9092</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">kafka_test</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">from</span><span class="hljs-literal">-</span><span class="hljs-comment">beginning</span>

<span class="hljs-comment">hello</span><span class="hljs-string">,</span> <span class="hljs-comment">kafka</span>


<span class="hljs-comment">旧版本过时方式：</span>
<span class="hljs-title">[</span><span class="hljs-comment">root@Basic</span> <span class="hljs-comment">kafka_2</span><span class="hljs-string">.</span><span class="hljs-comment">11</span><span class="hljs-literal">-</span><span class="hljs-comment">1</span><span class="hljs-string">.</span><span class="hljs-comment">1</span><span class="hljs-string">.</span><span class="hljs-comment">0</span><span class="hljs-title">]</span><span class="hljs-comment">#</span> <span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">console</span><span class="hljs-literal">-</span><span class="hljs-comment">consumer</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">localhost:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">kafka_test</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">from</span><span class="hljs-literal">-</span><span class="hljs-comment">beginning</span>
</code></pre>

<blockquote>
  <p>from-beginning 表示从第一条记录开始读，即使已经生产者发送过很早条消息，并且已经有消费者读了很多条了， 这个消费者也能从第一条重新开始读，并且不影响其他消费者读的偏移量。</p>
</blockquote>



<h5 id="5-查看topic状态">5. 查看Topic状态</h5>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-title">[</span><span class="hljs-comment">root@Basic</span> <span class="hljs-comment">kafka_2</span><span class="hljs-string">.</span><span class="hljs-comment">11</span><span class="hljs-literal">-</span><span class="hljs-comment">1</span><span class="hljs-string">.</span><span class="hljs-comment">1</span><span class="hljs-string">.</span><span class="hljs-comment">0</span><span class="hljs-title">]</span><span class="hljs-comment">#</span> <span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">describe</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">localhost:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">kafka_test</span>
<span class="hljs-comment">Topic:kafka_test</span>        <span class="hljs-comment">PartitionCount:1</span>        <span class="hljs-comment">ReplicationFactor:1</span>     <span class="hljs-comment">Configs:</span>
        <span class="hljs-comment">Topic:</span> <span class="hljs-comment">kafka_test</span>       <span class="hljs-comment">Partition:</span> <span class="hljs-comment">0</span>    <span class="hljs-comment">Leader:</span> <span class="hljs-comment">0</span>       <span class="hljs-comment">Replicas:</span> <span class="hljs-comment">0</span>     <span class="hljs-comment">Isr:</span> <span class="hljs-comment">0</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>