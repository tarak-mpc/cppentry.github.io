---
layout:     post
title:      大数据IMF传奇行动绝密课程第89课：SparkStreaming On Kafka之kafka解析和安装实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="sparkstreaming-on-kafka之kafka解析和安装实战">SparkStreaming On Kafka之kafka解析和安装实战</h1>

<p>1、Kafka解析 <br>
2、Kafka安装实战</p>

<p><img title="" alt="图89-1 Kafka数据不流向UserSpace" src="https://img-blog.csdn.net/20170403195310883?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdG9tXzg4OTlfbGk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast"> <br>
Kafka元数据被ZooKeeper管理 <br>
Kafka是Scala写的，所以需要安装Scala、Java</p>

<p>将slf4j-nop-1.7.6.jar拷贝到kafka的libs目录下，slf4j用于nohup <br>
配置集群中每台机器： <br>
1、配置.bashrc</p>

<pre class="prettyprint"><code class=" hljs autohotkey">export KAFK<span class="hljs-built_in">A_HOME</span>=/usr/local/kafk<span class="hljs-built_in">a_2</span>.<span class="hljs-number">10</span>-<span class="hljs-number">0.9</span>.<span class="hljs-number">0.10</span>-<span class="hljs-number">0</span>
PATH加入{$KAFK<span class="hljs-built_in">A_HOME</span>}/bin</code></pre>

<p>2、配置属性 <br>
kafka目录下server.properties <br>
broker.id=0(每台机器不同Worker1设置为1，Worker2设置为2)</p>



<pre class="prettyprint"><code class=" hljs fix"><span class="hljs-attribute">zookeeper.connect</span>=<span class="hljs-string">Master:2181,Worker1:2182,Worker2:2181(zookeeper client默认端口)</span></code></pre>

<p>3、启动（所有机器都要启动）</p>



<pre class="prettyprint"><code class=" hljs axapta">nohup ./kafka-<span class="hljs-keyword">server</span>-start.sh ../config/<span class="hljs-keyword">server</span>.properties &amp;</code></pre>

<p>创建topic</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-string">.</span><span class="hljs-comment">/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">create</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">Master:2181</span><span class="hljs-string">,</span><span class="hljs-comment">Worker1:2181</span><span class="hljs-string">,</span><span class="hljs-comment">Worker2:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">replication</span><span class="hljs-literal">-</span><span class="hljs-comment">factor</span> <span class="hljs-comment">3</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">partitions</span> <span class="hljs-comment">1</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">HelloKafka</span></code></pre>

<p>查看topic</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-string">.</span><span class="hljs-comment">/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">discribe</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">Master:2181</span><span class="hljs-string">,</span><span class="hljs-comment">Worker1:2181</span><span class="hljs-string">,</span><span class="hljs-comment">Worker2:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">HelloKafka</span></code></pre>

<p>配置生产者</p>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-built_in">.</span>/kafka<span class="hljs-attribute">-console</span><span class="hljs-attribute">-producer</span><span class="hljs-built_in">.</span>sh <span class="hljs-subst">--</span>broker<span class="hljs-attribute">-list</span> Master:<span class="hljs-number">9092</span>,Worker1:<span class="hljs-number">9092</span>,Worker2:<span class="hljs-number">9092</span> <span class="hljs-subst">--</span>topic HelloKafka</code></pre>

<p>在下面输入 <br>
This is DT_Spark! <br>
I’m Rocky! <br>
Life is short, you need Spark!</p>

<p>消费者：</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-string">.</span><span class="hljs-comment">/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">console</span><span class="hljs-literal">-</span><span class="hljs-comment">consumer</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">Master:2181</span><span class="hljs-string">,</span><span class="hljs-comment">Worker1:2181</span><span class="hljs-string">,</span><span class="hljs-comment">Worker2:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">from</span><span class="hljs-literal">-</span><span class="hljs-comment">beginning</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">HelloKafka</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>