---
layout:     post
title:      Flume + Kafka学习——整合Demo
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_32815807/article/details/79593653				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="flume-kafka学习整合demo">Flume + Kafka学习——整合Demo</h1>



<h2 id="环境">环境</h2>

<ol>
<li>Flume 1.6.0-cdh5.7.0</li>
<li>Kafka 0.9.0.0</li>
</ol>

<p><strong>FLume官方文档</strong> <br>
<a href="http://archive.cloudera.com/cdh5/cdh/5/flume-ng-1.6.0-cdh5.7.0/FlumeUserGuide.html#memory-channel" rel="nofollow">Flume1.6.0 User Guide</a> <br>
<strong>Kafka官网文档</strong> <br>
<a href="http://kafka.apache.org/090/documentation.html#introduction" rel="nofollow">Kafka Documentation</a></p>



<h2 id="flume">Flume</h2>

<blockquote>
  <p>Apache Flume is a distributed, reliable, and available system for efficiently collecting, aggregating and moving large amounts of log data from many different sources to a centralized data store.</p>
</blockquote>

<p>Flume是分布式的日志收集系统，它将各个服务器中的数据收集起来并送到目的地</p>



<h3 id="flume数据流模型">Flume数据流模型</h3>

<p>Flume就是将数据从数据源(source)收集过来，Flume会先缓存数据(channel),再将收集到的数据送到指定的目的地(sink)，最后Flume在删除自己缓存的数据</p>

<p>这样就是一个Event ，被定义为具有字节数组和可选字符串属性的数据流单元。包括 event headers、event body、event信息 <br>
<img src="http://archive.cloudera.com/cdh5/cdh/5/flume-ng-1.6.0-cdh5.7.0/_images/UserGuide_image00.png" alt="数据流模型" title=""></p>

<p>Flume Agent 是一个（JVM）进程，用于承载Event从外部源流向下一个目标</p>

<p>Agent 由三个核心组成 <br>
1. Source  source组件是专门用来收集数据的,类似生产者。消耗由外部源（如Web服务器）传递给它的Event。外部源以Flume源能识别的格式向Flume发送Event <br>
2. Channel source组件把数据收集来以后临时存放在channel中，类似仓库 <br>
3. Sink    sink组件是用于把数据发送到目的地，类似消费者</p>



<h2 id="kafka">Kafka</h2>

<blockquote>
  <p>Kafka® is a distributed, partitioned, replicated commit log service. It provides the functionality of a messaging system, but with a unique design.</p>
</blockquote>

<p>Kafka是最初由Linkedin公司开发，是一个分布式、支持分区的（partition）、多副本的（replica），基于zookeeper协调的分布式消息系统，它的最大的特性就是可以实时的处理大量数据以满足各种需求场景</p>

<ol>
<li>producer：消息生产者，发布消息到 kafka 集群的终端或服务。</li>
<li>broker：kafka 集群中包含的服务器。</li>
<li>topic：每条发布到 kafka 集群的消息属于的类别，即 kafka 是面向 topic 的。</li>
<li>partition：partition 是物理上的概念，每个 topic 包含一个或多个 partition。kafka 分配的单位是 partition。</li>
<li>consumer：从 kafka 集群中消费消息的终端或服务。</li>
<li>consumer group：high-level consumer API 中，每个 consumer 都属于一个 consumer group，每条消息只能被 consumer group 中的一个 Consumer 消费，但可以被多个 consumer group 消费。</li>
<li>replica：partition 的副本，保障 partition 的高可用。</li>
<li>leader：replica 中的一个角色， producer 和 consumer 只跟 leader 交互。</li>
<li>follower：replica 中的一个角色，从 leader 中复制数据。</li>
<li>controller：kafka 集群中的其中一个服务器，用来进行 leader election 以及 各种 failover。</li>
<li>zookeeper：kafka 通过 zookeeper 来存储集群的 meta 信息。</li>
</ol>



<h3 id="kafka原理">Kafka原理</h3>

<p>通常来讲，消息模型可以分为两种：队列和发布-订阅式。队列的处理方式是一组消费者从服务器读取消息，一条消息只有其中的一个消费者来处理。在发布-订阅模型中，消息被广播给所有的消费者，接收到消息的消费者都可以处理此消息。Kafka为这两种模型提供了单一的消费者抽象模型： 消费者组(consumer group)。消费者用一个消费者组名标记自己。</p>

<p>一个发布在Topic上消息被分发给此消费者组中的一个消费者。假如所有的消费者都在一个组中，那么这就变成了queue模型。假如所有的消费者都在不同的组中，那么就完全变成了发布-订阅模型。更通用的， 我们可以创建一些消费者组作为逻辑上的订阅者。每个组包含数目不等的消费者，一个组内多个消费者可以用来扩展性能和容错。       </p>

<p>并且，kafka能够保证生产者发送到一个特定的Topic的分区上，消息将会按照它们发送的顺序依次加入，也就是说，如果一个消息M1和M2使用相同的producer发送，M1先发送，那么M1将比M2的offset低，并且优先的出现在日志中。消费者收到的消息也是此顺序。如果一个Topic配置了复制因子（replication facto）为N,那么可以允许N-1服务器宕机而不丢失任何已经提交（committed）的消息。此特性说明kafka有比传统的消息系统更强的顺序保证。但是，相同的消费者组中不能有比分区更多的消费者，否则多出的消费者一直处于空等待，不会收到消息。</p>



<h2 id="demo">Demo</h2>

<p>监控一个文件实时采集新增的数据输出到Kafka <br>
FLume采用 exec source + memory channel+ kafka sink</p>

<p><img src="//img-blog.csdn.net/20180317165803252?watermark/2/text/Ly9ibG9nLmNzZG4ubmV0L3FxXzMyODE1ODA3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<ol>
<li>设置agent</li>
</ol>

<p>Flume agent配置存储在本地配置文件中。配置文件包含代理中每个source，sink和channel的属性以及它们如何连接在一起以形成数据流</p>

<p><strong>下文<span class="MathJax_Preview" style="color: inherit; display: none;"></span><span class="MathJax" id="MathJax-Element-5-Frame" tabindex="0" style="position: relative;" data-mathml='&lt;math xmlns="http://www.w3.org/1998/Math/MathML"&gt;&lt;mi&gt;F&lt;/mi&gt;&lt;mi&gt;L&lt;/mi&gt;&lt;mi&gt;U&lt;/mi&gt;&lt;mi&gt;M&lt;/mi&gt;&lt;msub&gt;&lt;mi&gt;E&lt;/mi&gt;&lt;mi&gt;H&lt;/mi&gt;&lt;/msub&gt;&lt;mi&gt;O&lt;/mi&gt;&lt;mi&gt;M&lt;/mi&gt;&lt;mi&gt;E&lt;/mi&gt;&lt;mrow class="MJX-TeXAtom-ORD"&gt;&lt;mo&gt;&amp;#xFF0C;&lt;/mo&gt;&lt;/mrow&gt;&lt;/math&gt;' role="presentation"><nobr aria-hidden="true"><span class="math" id="MathJax-Span-74" style="width: 8.648em; display: inline-block;"><span style="display: inline-block; position: relative; width: 7.19em; height: 0px; font-size: 120%;"><span style="position: absolute; clip: rect(1.565em, 1007.19em, 2.867em, -999.997em); top: -2.497em; left: 0em;"><span class="mrow" id="MathJax-Span-75"><span class="mi" id="MathJax-Span-76" style="font-family: STIXGeneral-Italic;">F<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.055em;"></span></span><span class="mi" id="MathJax-Span-77" style="font-family: STIXGeneral-Italic;">L<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span class="mi" id="MathJax-Span-78" style="font-family: STIXGeneral-Italic;">U<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.055em;"></span></span><span class="mi" id="MathJax-Span-79" style="font-family: STIXGeneral-Italic;">M<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.055em;"></span></span><span class="msubsup" id="MathJax-Span-80"><span style="display: inline-block; position: relative; width: 1.253em; height: 0px;"><span style="position: absolute; clip: rect(3.18em, 1000.63em, 4.169em, -999.997em); top: -4.008em; left: 0em;"><span class="mi" id="MathJax-Span-81" style="font-family: STIXGeneral-Italic;">E<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span style="display: inline-block; width: 0px; height: 4.013em;"></span></span><span style="position: absolute; top: -3.852em; left: 0.628em;"><span class="mi" id="MathJax-Span-82" style="font-size: 70.7%; font-family: STIXGeneral-Italic;">H<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.055em;"></span></span><span style="display: inline-block; width: 0px; height: 4.013em;"></span></span></span></span><span class="mi" id="MathJax-Span-83" style="font-family: STIXGeneral-Italic;">O</span><span class="mi" id="MathJax-Span-84" style="font-family: STIXGeneral-Italic;">M<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.055em;"></span></span><span class="mi" id="MathJax-Span-85" style="font-family: STIXGeneral-Italic;">E<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span class="texatom" id="MathJax-Span-86"><span class="mrow" id="MathJax-Span-87"><span class="mo" id="MathJax-Span-88"><span style='font-family: STIXGeneral, "Arial Unicode MS", serif; font-size: 83%; font-style: normal; font-weight: normal;'>，</span></span></span></span></span><span style="display: inline-block; width: 0px; height: 2.503em;"></span></span></span><span style="display: inline-block; overflow: hidden; vertical-align: -0.309em; border-left: 0px solid; width: 0px; height: 1.316em;"></span></span></nobr><span class="MJX_Assistive_MathML" role="presentation"><math xmlns="http://www.w3.org/1998/Math/MathML"><mi>F</mi><mi>L</mi><mi>U</mi><mi>M</mi><msub><mi>E</mi><mi>H</mi></msub><mi>O</mi><mi>M</mi><mi>E</mi><mrow class="MJX-TeXAtom-ORD"><mo>，</mo></mrow></math></span></span><script type="math/tex" id="MathJax-Element-5">FLUME_HOME，</script>KAFKA_HOME指FLUME,KAFKA安装目录</strong></p>

<p>进入$FLUME_HOME/conf 创建exec-memory-kafca.conf并配置</p>

<h3 id="exec-source配置">Exec Source配置</h3>

<p>type: <br>
    The component type name, needs to be exec</p>

<p>command: <br>
    The command to execute</p>



<h3 id="kafka-sink-配置">Kafka Sink 配置</h3>

<p>type:</p>

<pre><code>Must be set to org.apache.flume.sink.kafka.KafkaSink
</code></pre>

<p>brokerList:</p>

<pre><code>List of brokers Kafka-Sink will connect to, to get the list of topic partitions This can be a partial list of brokers, but we recommend at least two for HA. The format is comma separated list of hostname:port
</code></pre>

<p>topic:</p>

<pre><code>default-flume-topic The topic in Kafka to which the messages will be published. If this parameter is configured, messages will be published to this topic. If the event header contains a “topic” field, the event will be published to that topic overriding the topic configured here.
</code></pre>

<p>batchSize: <br>
    How many messages to process in one batch. Larger batches improve throughput while adding latency. <br>
requiredAcks: <br>
    How many replicas must acknowledge a message before its considered successfully written. Accepted values are 0 (Never wait for acknowledgement), 1 (wait for leader only), -1 (wait for all replicas) Set this to -1 to avoid data loss in some cases of leader failure.</p>

<p><strong>详细配置见官方文档</strong></p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor"># 给agent命名</span>
a1<span class="hljs-preprocessor">.sources</span> = <span class="hljs-built_in">r1</span>
a1<span class="hljs-preprocessor">.sinks</span> = k1
a1<span class="hljs-preprocessor">.channels</span> = c1

<span class="hljs-preprocessor"># 配置source</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.type</span> = exec  
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.command</span> = tail -F /Users/null/data/flume-test<span class="hljs-preprocessor">.log</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.channels</span> = c1

<span class="hljs-preprocessor"># 配置sink</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.type</span> = org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.kafca</span><span class="hljs-preprocessor">.KafkaSink</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.topic</span> = kafka-test
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.brokerList</span> = localhost:<span class="hljs-number">9092</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.requiredAcks</span> = <span class="hljs-number">1</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.batchSize</span> = <span class="hljs-number">2</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.channel</span> = c1
<span class="hljs-preprocessor"># 配置memory channel</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span> = memory
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">1000</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.transactionCapacity</span> = <span class="hljs-number">100</span></code></pre>

<ol>
<li>启动zookeeper <br>
Kafka需要zookeeper环境 <br>
<code> <br>
bin/zkServer.sh start <br>
</code></li>
<li>启动Kafka <br>
<code> <br>
bin/kafka-server-start.sh config/server.properties <br>
</code></li>
<li><p>创建话题</p>

<p>kafka-test 为topic 名称与flume 配置中a1.sinks.k1.topic 对应</p>

<pre class="prettyprint"><code class=" hljs lasso">kafka<span class="hljs-attribute">-topics</span><span class="hljs-built_in">.</span>sh <span class="hljs-subst">--</span><span class="hljs-built_in">list</span> <span class="hljs-subst">--</span>zookeeper localhost:<span class="hljs-number">2181</span> kafka<span class="hljs-attribute">-test</span></code></pre></li>
<li><p>启动一个Consumer</p>

<pre class="prettyprint"><code class=" hljs lasso">kafka<span class="hljs-attribute">-console</span><span class="hljs-attribute">-consumer</span><span class="hljs-built_in">.</span>sh <span class="hljs-subst">--</span>zookeeper localhost:<span class="hljs-number">2181</span> <span class="hljs-subst">--</span>topic kafka<span class="hljs-attribute">-test</span> <span class="hljs-subst">--</span>from<span class="hljs-attribute">-beginning</span>
</code></pre></li>
<li><p>启动FLume</p>

<p>–conf 指定配置文件所在位置 –conf-file为指定配置文件 –name 为agent的名称</p>

<pre class="prettyprint"><code class=" hljs lasso">flume<span class="hljs-attribute">-ng</span> agent <span class="hljs-subst">--</span>conf <span class="hljs-variable">$FLUME_HOME</span>/conf <span class="hljs-subst">--</span>conf<span class="hljs-attribute">-file</span> <span class="hljs-variable">$FLUME_HOME</span>/conf/exec<span class="hljs-attribute">-memory</span><span class="hljs-attribute">-kafca</span><span class="hljs-built_in">.</span>conf <span class="hljs-subst">--</span>name a1 <span class="hljs-attribute">-Dflume</span><span class="hljs-built_in">.</span>root<span class="hljs-built_in">.</span>logger<span class="hljs-subst">=</span>INFO,console</code></pre></li>
<li><p>在监控的文件中添加内容，可以查看到kafka发出并消费</p></li>
</ol>

<p>参考： <br>
1.<a href="https://www.cnblogs.com/yangxiaoyi/p/7359236.html" rel="nofollow">Kafka基本知识整理</a> <br>
2.<a href="http://blog.csdn.net/a2011480169/article/details/51544664" rel="nofollow">FLume架构以及应用</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>