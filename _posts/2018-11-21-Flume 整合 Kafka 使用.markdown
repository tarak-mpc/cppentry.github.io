---
layout:     post
title:      Flume 整合 Kafka 使用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/HG_Harvey/article/details/79203243				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>实现需求：整合 Flume 和 Kafka 完成实时数据收集，即使用 Flume 中的 Kafka Sink 将 Flume 实时收集到的日志信息输出到 Kafka</p>

<p>笔者使用的Flume版本为1.6，Kafka版本为0.11.0.0，Flume 中的Kafka Sink的使用参见Flume官方文档： <br>
<a href="http://flume.apache.org/releases/content/1.6.0/FlumeUserGuide.html" rel="nofollow">http://flume.apache.org/releases/content/1.6.0/FlumeUserGuide.html</a></p>

<p><img src="https://img-blog.csdn.net/20180130104556687?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSEdfSGFydmV5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
<strong>注：上图中黑色加粗字体的项为必须要配置的，红色框中的官方给出了一个配置模板，下面我们参照这个模板来进行配置</strong></p>

<p>在博客<a href="http://blog.csdn.net/hg_harvey/article/details/78357556" rel="nofollow">使用Log4j将日志实时写入Flume</a>的基础上完成 Flume 整合 Kafka，实现Kafka不断消费Flume收集到的Log4j日志。</p>

<p>启动Kafka（先启动ZK）</p>



<pre class="prettyprint"><code class=" hljs lasso">kafka<span class="hljs-attribute">-server</span><span class="hljs-attribute">-start</span><span class="hljs-built_in">.</span>sh <span class="hljs-variable">$KAFKA_HOME</span>/config/server<span class="hljs-built_in">.</span>properties</code></pre>

<p>Kafka 中创建 topic</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-title">[</span><span class="hljs-comment">hadoop@Master</span> <span class="hljs-comment">~</span><span class="hljs-title">]</span><span class="hljs-comment">$</span> <span class="hljs-comment">kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">create</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">localhost:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">replication</span><span class="hljs-literal">-</span><span class="hljs-comment">factor</span> <span class="hljs-comment">1</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">partitions</span> <span class="hljs-comment">1</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">flume2kafka</span>
<span class="hljs-comment">Created</span> <span class="hljs-comment">topic</span> <span class="hljs-comment">"flume2kafka"</span><span class="hljs-string">.</span>
<span class="hljs-title">[</span><span class="hljs-comment">hadoop@Master</span> <span class="hljs-comment">~</span><span class="hljs-title">]</span><span class="hljs-comment">$</span> <span class="hljs-comment">kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">describe</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">localhost:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">flume2kafka</span>
<span class="hljs-comment">Topic:flume2kafka</span>   <span class="hljs-comment">PartitionCount:1</span>    <span class="hljs-comment">ReplicationFactor:1</span> <span class="hljs-comment">Configs:</span>
    <span class="hljs-comment">Topic:</span> <span class="hljs-comment">flume2kafka</span>  <span class="hljs-comment">Partition:</span> <span class="hljs-comment">0</span>    <span class="hljs-comment">Leader:</span> <span class="hljs-comment">0</span>   <span class="hljs-comment">Replicas:</span> <span class="hljs-comment">0</span> <span class="hljs-comment">Isr:</span> <span class="hljs-comment">0</span></code></pre>

<p>Flume 整合 Kafka Agent 配置文件编写</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor"># Name the components on this agent</span>
flume2kafka-agent<span class="hljs-preprocessor">.sources</span> = arvo-source
flume2kafka-agent<span class="hljs-preprocessor">.sinks</span> = kafka-sink
flume2kafka-agent<span class="hljs-preprocessor">.channels</span> = memory-channel

<span class="hljs-preprocessor"># Describe/configure the source</span>
flume2kafka-agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.arvo</span>-source<span class="hljs-preprocessor">.type</span> = avro
flume2kafka-agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.arvo</span>-source<span class="hljs-preprocessor">.bind</span> = Master
flume2kafka-agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.arvo</span>-source<span class="hljs-preprocessor">.port</span> = <span class="hljs-number">44444</span>

<span class="hljs-preprocessor"># Flume 整合 Kafka</span>
flume2kafka-agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.kafka</span>-sink<span class="hljs-preprocessor">.type</span> = org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.KafkaSink</span>
flume2kafka-agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.kafka</span>-sink<span class="hljs-preprocessor">.topic</span> = flume2kafka
flume2kafka-agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.kafka</span>-sink<span class="hljs-preprocessor">.brokerList</span> = Master:<span class="hljs-number">9092</span>
<span class="hljs-preprocessor"># 10 条记录写一次</span>
flume2kafka-agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.kafka</span>-sink<span class="hljs-preprocessor">.batchSize</span> = <span class="hljs-number">10</span>

<span class="hljs-preprocessor"># Use a channel which buffers events in memory</span>
flume2kafka-agent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.memory</span>-channel<span class="hljs-preprocessor">.type</span> = memory

<span class="hljs-preprocessor"># Bind the source and sink to the channel</span>
flume2kafka-agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.arvo</span>-source<span class="hljs-preprocessor">.channels</span> = memory-channel
flume2kafka-agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.kafka</span>-sink<span class="hljs-preprocessor">.channel</span> = memory-channel</code></pre>

<p>启动Agent</p>

<pre class="prettyprint"><code class=" hljs haml">flume-ng agent \
-<span class="ruby">-conf <span class="hljs-variable">$FLUME_HOME</span>/conf \
</span>-<span class="ruby">-conf-file <span class="hljs-variable">$FLUME_HOME</span>/config/flume2kafka-agent.conf \
</span>-<span class="ruby">-name flume2kafka-agent \
</span>-<span class="ruby"><span class="hljs-constant">Dflume</span>.root.logger=<span class="hljs-constant">INFO</span>,console</span></code></pre>

<p>Kafka 中启动消费者</p>

<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">console</span><span class="hljs-literal">-</span><span class="hljs-comment">consumer</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">localhost:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">flume2kafka</span></code></pre>

<p>运行项目，观察Kafka 消费者控制台</p>

<p><img src="https://img-blog.csdn.net/20180130105104589?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSEdfSGFydmV5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>