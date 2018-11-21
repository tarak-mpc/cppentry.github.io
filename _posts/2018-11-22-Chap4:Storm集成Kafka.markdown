---
layout:     post
title:      Chap4:Storm集成Kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u012432611/article/details/47978845				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>本文讨论： <br>
•    Kafka概览 <br>
•    Setting up a single node and multinode Kafka cluster <br>
•    Producing data into a Kafka partition <br>
•    Using KafkaSpout in a Storm topology to consume messages from Kafka <br>
<img src="https://img-blog.csdn.net/20150825153500120" alt="kafka cluster" title=""> <br>
kafka中，消息由producer发出，称为topics，它是一个可以被多个消费者消费的队列。kafka topic可以有多个分区，可以在每个分区并行读写，一个topic的每个分区的数据存储在disk的不同目录下，这些目录可以在不同的disk上，允许我们跨越单个disk的I/O限制，一个topic的两个分区可以被不同的brokers定位，这增加了吞吐量，因为每个分区相互独立。每个分区的消息有唯一对应的称为offset的序列号。 <br>
<img src="https://img-blog.csdn.net/20150825154300214" alt="kafka消息分布" title=""></p>

<h2 id="复制">复制</h2>

<p>为了容错，kafka支持复制一个topic的分区，选取一个broker作为分区的leader所有的读写都必须到leader分区。</p>

<h2 id="消费者">消费者</h2>

<p>消费者从broker读取消息区间。每个消费者有一个groupID。有相同groupID的消费者作为一个逻辑消费者，topic的每个消息从一个消费者组(有相同的groupID)分发到消费者。对一个特定的topic的不同消费者组可以以他们自己的节奏处理消息（因为消息在没被处理之前不会被删除）。 <br>
<img src="https://img-blog.csdn.net/20150825155328458" alt="这里写图片描述" title=""></p>

<h2 id="brokers">Brokers</h2>

<p>Broker从producer接收消息(推机制)分发到消费者(拉机制)，broker管理持久化消息到disk。对每个topic,它将在disk上创建一个目录。</p>

<h2 id="数据滞留">数据滞留</h2>

<p>Kafka中的topic 有个滞留时间，在broker配置文件里通过log.retention.minutes指定。这是个删除操作，非常有效。</p>

<h2 id="单节点kafka">单节点kafka</h2>



<pre class="prettyprint"><code class=" hljs autohotkey">tar -xvzf kafk<span class="hljs-built_in">a_2</span>.<span class="hljs-number">8.0</span>-<span class="hljs-number">0.8</span>.<span class="hljs-number">1.1</span>.tgz
cd kafk<span class="hljs-built_in">a_2</span>.<span class="hljs-number">8.0</span>-<span class="hljs-number">0.8</span>.<span class="hljs-number">1.1</span></code></pre>

<p>配置环境变量 <br>
在$KAFKA_HOEM/config/server.properties里设置</p>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-keyword">log</span><span class="hljs-built_in">.</span>dirs<span class="hljs-subst">=</span>/<span class="hljs-built_in">var</span>/kafka<span class="hljs-attribute">-logs</span>
zookeeper<span class="hljs-built_in">.</span>connect<span class="hljs-subst">=</span>zoo1:<span class="hljs-number">2181</span>,zoo2:<span class="hljs-number">2181</span>,zoo3:<span class="hljs-number">2181</span></code></pre>

<h2 id="启动kafka">启动kafka</h2>



<pre class="prettyprint"><code class=" hljs axapta">./bin/kafka-<span class="hljs-keyword">server</span>-start.sh config/<span class="hljs-keyword">server</span>.properties</code></pre>

<p>创建topic</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-string">.</span><span class="hljs-comment">/bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">create</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">zoo1:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">partitions</span>
<span class="hljs-comment">1</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">replication</span><span class="hljs-literal">-</span><span class="hljs-comment">factor</span> <span class="hljs-comment">1</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">verification</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span></code></pre>

<p>列出topic</p>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-built_in">.</span>/bin/kafka<span class="hljs-attribute">-topics</span><span class="hljs-built_in">.</span>sh
<span class="hljs-subst">--</span>zookeeper zoo1:<span class="hljs-number">2181</span> <span class="hljs-subst">--</span><span class="hljs-built_in">list</span>
输出：verification<span class="hljs-attribute">-topic</span></code></pre>

<p>利用kafka的命令行写几条消息</p>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-built_in">.</span>/bin/kafka<span class="hljs-attribute">-console</span><span class="hljs-attribute">-producer</span><span class="hljs-built_in">.</span>sh <span class="hljs-subst">--</span>broker<span class="hljs-attribute">-list</span> localhost:<span class="hljs-number">9092</span>
<span class="hljs-subst">--</span>topic verification<span class="hljs-attribute">-topic</span>
输入：
Message <span class="hljs-number">1</span>
Test Message <span class="hljs-number">2</span>
Message <span class="hljs-number">3</span></code></pre>

<p>启动消费者窗口</p>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-built_in">.</span>/bin/kafka<span class="hljs-attribute">-console</span><span class="hljs-attribute">-consumer</span><span class="hljs-built_in">.</span>sh <span class="hljs-subst">--</span>zookeeper localhost:<span class="hljs-number">2181</span> <span class="hljs-subst">--</span>topic
verification<span class="hljs-attribute">-topic</span> <span class="hljs-subst">--</span>from<span class="hljs-attribute">-beginning</span>
输出：Message <span class="hljs-number">1</span>
Test Message <span class="hljs-number">2</span>
Message <span class="hljs-number">3</span></code></pre>

<h2 id="三个节点的kafka集群">三个节点的kafka集群</h2>

<p>和单节点的区别是server.properties:</p>



<pre class="prettyprint"><code class=" hljs avrasm">broker<span class="hljs-preprocessor">.id</span>=<span class="hljs-number">0</span>    //各broker要唯一
port=<span class="hljs-number">9092</span>
host<span class="hljs-preprocessor">.name</span>=kafka1
log<span class="hljs-preprocessor">.dirs</span>=/var/kafka-logs
zookeeper<span class="hljs-preprocessor">.connect</span>=zoo1:<span class="hljs-number">2181</span>,zoo2:<span class="hljs-number">2181</span>,zoo3:<span class="hljs-number">2181</span></code></pre>

<p>在三个节上启动kafka</p>



<pre class="prettyprint"><code class=" hljs axapta">./bin/kafka-<span class="hljs-keyword">server</span>-start.sh config/<span class="hljs-keyword">server</span>.properties</code></pre>

<p>确认 <br>
创建消息</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-string">.</span><span class="hljs-comment">/bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">create</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">zoo1:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">partitions</span>
<span class="hljs-comment">3</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">replication</span><span class="hljs-literal">-</span><span class="hljs-comment">factor</span> <span class="hljs-comment">1</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">verification</span></code></pre>

<p>列出消息</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-string">.</span><span class="hljs-comment">/bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">describe</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">zoo1:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span>
<span class="hljs-comment">verification</span></code></pre>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-label">Topic:</span>verification PartitionCount:<span class="hljs-number">3</span>
<span class="hljs-label">ReplicationFactor:</span><span class="hljs-number">1</span>
<span class="hljs-label">Configs:</span>
<span class="hljs-label">Topic:</span> verification Partition: <span class="hljs-number">0</span> Leader: <span class="hljs-number">0</span> Replicas: <span class="hljs-number">0</span> Isr: <span class="hljs-number">0</span>
<span class="hljs-label">Topic:</span> verification Partition: <span class="hljs-number">1</span> Leader: <span class="hljs-number">1</span> Replicas: <span class="hljs-number">0</span> Isr: <span class="hljs-number">0</span>
<span class="hljs-label">Topic:</span> verification Partition: <span class="hljs-number">2</span> Leader: <span class="hljs-number">2</span> Replicas: <span class="hljs-number">0</span> Isr: <span class="hljs-number">0</span></code></pre>

<p>核实kafka</p>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-built_in">.</span>/bin/kafka<span class="hljs-attribute">-console</span><span class="hljs-attribute">-producer</span><span class="hljs-built_in">.</span>sh <span class="hljs-subst">--</span>broker<span class="hljs-attribute">-list</span> kafka1:<span class="hljs-number">9092</span>,kafka2:<span class="hljs-number">9</span>
<span class="hljs-number">092</span>,kafka3:<span class="hljs-number">9092</span> <span class="hljs-subst">--</span>topic verification</code></pre>

<p>这里kafka1是broker的IP <br>
写入消息</p>



<pre class="prettyprint"><code class=" hljs mathematica"><span class="hljs-keyword">First</span>
Second
Third</code></pre>

<p>启动消费者</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-string">.</span><span class="hljs-comment">/bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">console</span><span class="hljs-literal">-</span><span class="hljs-comment">consumer</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">zoo1:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span>
<span class="hljs-comment">verification</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">from</span><span class="hljs-literal">-</span><span class="hljs-comment">beginning</span></code></pre>

<h2 id="当然也可以在单个节点上运行多个broker">当然也可以在单个节点上运行多个broker</h2>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>