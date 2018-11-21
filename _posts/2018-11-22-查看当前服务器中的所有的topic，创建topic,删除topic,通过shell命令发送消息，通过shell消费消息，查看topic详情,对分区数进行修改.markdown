---
layout:     post
title:      查看当前服务器中的所有的topic，创建topic,删除topic,通过shell命令发送消息，通过shell消费消息，查看topic详情,对分区数进行修改
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/toto1297488504/article/details/73432256				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="一-kafka常用操作命令">一、 Kafka常用操作命令</h1>

<p><strong> 查看当前服务器中的所有topic</strong></p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@hadoop3</span> kafka]<span class="hljs-comment"># bin/kafka-topics.sh --list --zookeeper hadoop11:2181</span>
[root<span class="hljs-variable">@hadoop3</span> kafka]<span class="hljs-comment">#</span></code></pre>

<p><strong>信息写入到</strong> <br>
<strong> 创建topic</strong></p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-title">[</span><span class="hljs-comment">root@hadoop3</span> <span class="hljs-comment">kafka</span><span class="hljs-title">]</span><span class="hljs-comment">#</span> <span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">create</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">hadoop11:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">replication</span><span class="hljs-literal">-</span><span class="hljs-comment">factor</span> <span class="hljs-comment">1</span> <span class="hljs-literal">-</span><span class="hljs-comment">partitions</span> <span class="hljs-comment">1</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">test</span>
<span class="hljs-comment">Created</span> <span class="hljs-comment">topic</span> <span class="hljs-comment">"test"</span><span class="hljs-string">.</span>
<span class="hljs-title">[</span><span class="hljs-comment">root@hadoop3</span> <span class="hljs-comment">kafka</span><span class="hljs-title">]</span><span class="hljs-comment">#</span> <span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">list</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">hadoop11:2181</span>
<span class="hljs-comment">test</span></code></pre>

<p>通过上面，可以看到已经创建了一个test的topic</p>

<p><strong> 删除topic</strong></p>



<pre class="prettyprint"><code class=" hljs coffeescript">[root<span class="hljs-property">@hadoop2</span> kafka]<span class="hljs-comment"># bin/kafka-topics.sh --create --zookeeper hadoop11:2181 --replication-factor 1 -partitions 1 --topic test2</span>
Created topic <span class="hljs-string">"test2"</span>.
[root<span class="hljs-property">@hadoop2</span> kafka]<span class="hljs-comment"># bin/kafka-topics.sh --list --zookeeper hadoop11:2181</span>
itheima
test
test2
[root<span class="hljs-property">@hadoop2</span> kafka]<span class="hljs-comment"># bin/kafka-topics.sh --delete --zookeeper hadoop11:2181 --topic test2</span>
Topic test2 <span class="hljs-keyword">is</span> marked <span class="hljs-keyword">for</span> deletion.
<span class="hljs-attribute">Note</span>: This will have <span class="hljs-literal">no</span> impact <span class="hljs-keyword">if</span> <span class="hljs-keyword">delete</span>.topic.enable <span class="hljs-keyword">is</span> <span class="hljs-keyword">not</span> set to <span class="hljs-literal">true</span>.
[root<span class="hljs-property">@hadoop2</span> kafka]<span class="hljs-comment"># bin/kafka-topics.sh --list --zookeeper hadoop11:2181</span>
itheima
test
[root<span class="hljs-property">@hadoop2</span> kafka]<span class="hljs-comment">#</span></code></pre>

<p>需要server.properties中设置delete.topic.enable=true否则只是标记删除或者直接重启。 <br>
<strong> 通过shell命令发送消息</strong> <br>
要注意的是要指定topic，表示要在哪个topic中生产消息，这里的topic需要时上面创建的topic</p>

<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-title">[</span><span class="hljs-comment">root@hadoop3</span> <span class="hljs-comment">kafka</span><span class="hljs-title">]</span><span class="hljs-comment">#</span> <span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">console</span><span class="hljs-literal">-</span><span class="hljs-comment">producer</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">broker</span><span class="hljs-literal">-</span><span class="hljs-comment">list</span> <span class="hljs-comment">hadoop1:9092</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">test</span>
<span class="hljs-comment">asdfasdfasd</span>
<span class="hljs-comment">asdfasdf</span>
<span class="hljs-comment">asdfasdf</span>
<span class="hljs-comment">toto</span> <span class="hljs-comment">test</span>
<span class="hljs-comment">tuto</span> <span class="hljs-comment">test2</span></code></pre>

<p>注意命令中指定的–block-list <strong>hadoop1:9092</strong>,当改成<strong>hadoop2:9092</strong>时，也可以。</p>

<p><strong> 通过shell消费消息</strong> <br>
要指明的是要使用哪个topic中的数据，这里的topic需要时上面创建的topic：</p>

<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-title">[</span><span class="hljs-comment">root@hadoop3</span> <span class="hljs-comment">kafka</span><span class="hljs-title">]</span><span class="hljs-comment">#</span> <span class="hljs-comment">sh</span> <span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">console</span><span class="hljs-literal">-</span><span class="hljs-comment">consumer</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">hadoop11:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">from</span><span class="hljs-literal">-</span><span class="hljs-comment">beginning</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">test</span>
<span class="hljs-comment">asdfasdfasd</span>
<span class="hljs-comment">asdfasdf</span>
<span class="hljs-comment">asdfasdf</span>
<span class="hljs-comment">toto</span> <span class="hljs-comment">test</span>
<span class="hljs-comment">tuto</span> <span class="hljs-comment">test2</span></code></pre>

<p>注意：这里要指定消费那个topic，这里使用的是test.</p>

<p><strong> 查看消费位置</strong></p>

<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-title">[</span><span class="hljs-comment">root@hadoop3</span> <span class="hljs-comment">kafka</span><span class="hljs-title">]</span><span class="hljs-comment">#</span> <span class="hljs-comment">sh</span> <span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">run</span><span class="hljs-literal">-</span><span class="hljs-comment">class</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-comment">kafka</span><span class="hljs-string">.</span><span class="hljs-comment">tools</span><span class="hljs-string">.</span><span class="hljs-comment">ConsumerOffsetChecker</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">hadoop11:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">group</span> <span class="hljs-comment">testGroup</span></code></pre>

<p><strong> 查看某个Topic的详情</strong></p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@hadoop3</span> kafka]<span class="hljs-comment"># sh bin/kafka-topics.sh --topic test --describe --zookeeper hadoop11:2181</span>
<span class="hljs-constant">Topic</span><span class="hljs-symbol">:test</span>  <span class="hljs-constant">PartitionCount</span><span class="hljs-symbol">:</span><span class="hljs-number">1</span>    <span class="hljs-constant">ReplicationFactor</span><span class="hljs-symbol">:</span><span class="hljs-number">1</span> <span class="hljs-constant">Configs</span><span class="hljs-symbol">:</span>
    <span class="hljs-constant">Topic</span><span class="hljs-symbol">:</span> test <span class="hljs-constant">Partition</span><span class="hljs-symbol">:</span> <span class="hljs-number">0</span>    <span class="hljs-constant">Leader</span><span class="hljs-symbol">:</span> <span class="hljs-number">0</span>   <span class="hljs-constant">Replicas</span><span class="hljs-symbol">:</span> <span class="hljs-number">0</span> <span class="hljs-constant">Isr</span><span class="hljs-symbol">:</span> <span class="hljs-number">0</span>
[root<span class="hljs-variable">@hadoop3</span> kafka]<span class="hljs-comment">#</span></code></pre>

<p><strong> 对分区数进行修改</strong></p>



<pre class="prettyprint"><code class=" hljs asciidoc">[root@hadoop3 kafka]# bin/kafka-topics.sh --zookeeper hadoop11:2181 -alter --partitions 15 --topic test
<span class="hljs-label">WARNING: </span>If partitions are increased for a topic that has a key, the partition logic or ordering of the messages will be affected
Adding partitions succeeded!
[root@hadoop3 kafka]#</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>