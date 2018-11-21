---
layout:     post
title:      kafka和flume整合
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/Anonymous_cx/article/details/51456913				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="kafka作为source">Kafka作为source：</h2>

<p>配置文件：</p>



<pre class="prettyprint"><code class=" hljs avrasm">**<span class="hljs-preprocessor">#定义各个模块**</span>
a1<span class="hljs-preprocessor">.sources</span> = kafka 
a1<span class="hljs-preprocessor">.sinks</span> = log
a1<span class="hljs-preprocessor">.channels</span> = c1

<span class="hljs-preprocessor">#配置kafka source</span>
<span class="hljs-preprocessor">#source的类型为kafkaSource</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.type</span> = org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.source</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.KafkaSource</span>
<span class="hljs-preprocessor">#消费者连接的zk集群地址</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.zookeeperConnect</span> = crxy155:<span class="hljs-number">2181</span>,crxy156:<span class="hljs-number">2181</span>,crxy162:<span class="hljs-number">2181</span>
<span class="hljs-preprocessor">#消费者消费的topic，只能是一个。</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.topic</span> = hello
<span class="hljs-preprocessor">#kafka的组id</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.groupId</span> = flume
<span class="hljs-preprocessor">#kafka的消费者连接超时时间单位毫秒</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.timeout</span><span class="hljs-preprocessor">.ms</span> = <span class="hljs-number">3000</span>


<span class="hljs-preprocessor"># 配置logger sink</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.log</span><span class="hljs-preprocessor">.type</span> = logger

<span class="hljs-preprocessor"># 配置 memory channel</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span> = memory
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">1000</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.transactionCapacity</span> = <span class="hljs-number">100</span>

<span class="hljs-preprocessor"># 绑定三种组件的关系</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.channels</span> = c1
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.log</span><span class="hljs-preprocessor">.channel</span> = c1</code></pre>

<h2 id="kafka作为sink">Kafka作为sink：</h2>

<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor">############### 注意要查看下kafka的lib中的一些依赖包到flume的lib下####################</span>
<span class="hljs-preprocessor">#定义各个模块</span>
a1<span class="hljs-preprocessor">.sources</span> = netcat 
a1<span class="hljs-preprocessor">.sinks</span> = kfk
a1<span class="hljs-preprocessor">.channels</span> = c1

<span class="hljs-preprocessor">#配置netcat source</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.netcat</span><span class="hljs-preprocessor">.type</span> = netcat
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.netcat</span><span class="hljs-preprocessor">.bind</span> = <span class="hljs-number">0.0</span><span class="hljs-number">.0</span><span class="hljs-number">.0</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.netcat</span><span class="hljs-preprocessor">.port</span> = <span class="hljs-number">44444</span>

<span class="hljs-preprocessor"># 配置 kafka sink</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.kfk</span><span class="hljs-preprocessor">.type</span> = org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.KafkaSink</span>
<span class="hljs-preprocessor">#topic 如果header里有“topic”字段，会使用header里topic对应的值。</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.kfk</span><span class="hljs-preprocessor">.topic</span> = hello
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.kfk</span><span class="hljs-preprocessor">.brokerList</span> = crxy155:<span class="hljs-number">9092</span>,crxy156:<span class="hljs-number">9092</span>,crxy162:<span class="hljs-number">9092</span>
<span class="hljs-preprocessor">#如果header里含有key这个header则会根据key进行分区。</span>


<span class="hljs-preprocessor"># 配置 memory channel</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span> = memory
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">1000</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.transactionCapacity</span> = <span class="hljs-number">100</span>

<span class="hljs-preprocessor"># 绑定三种组件的关系</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.netcat</span><span class="hljs-preprocessor">.channels</span> = c1
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.kfk</span><span class="hljs-preprocessor">.channel</span> = c1</code></pre>

<h2 id="kafka作为channel">Kafka作为channel：</h2>

<p>**Kafka作为channel：三种方式： <br>
With Flume source and sink <br>
With Flume source and interceptor but no sink <br>
With Flume sink, but no source**</p>

<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor">#定义各个模块</span>
a1<span class="hljs-preprocessor">.sources</span> = netcat
a1<span class="hljs-preprocessor">.channels</span> = kafka

<span class="hljs-preprocessor">#配置netcat source</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.netcat</span><span class="hljs-preprocessor">.type</span> = netcat
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.netcat</span><span class="hljs-preprocessor">.bind</span> = <span class="hljs-number">0.0</span><span class="hljs-number">.0</span><span class="hljs-number">.0</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.netcat</span><span class="hljs-preprocessor">.port</span> = <span class="hljs-number">44444</span>

<span class="hljs-preprocessor"># 配置 kafka channel</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.type</span> = org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.channel</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.KafkaChannel</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">10000</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.transactionCapacity</span> = <span class="hljs-number">1000</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.zookeeperConnect</span>= crxy155:<span class="hljs-number">2181</span>,crxy156:<span class="hljs-number">2181</span>,crxy162:<span class="hljs-number">2181</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.brokerList</span>=crxy155:<span class="hljs-number">9092</span>,crxy156:<span class="hljs-number">9092</span>,crxy162:<span class="hljs-number">9092</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.topic</span>=hello
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.groupId</span>=flume

<span class="hljs-preprocessor"># 绑定组件的关系</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.netcat</span><span class="hljs-preprocessor">.channels</span> = kafka</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>