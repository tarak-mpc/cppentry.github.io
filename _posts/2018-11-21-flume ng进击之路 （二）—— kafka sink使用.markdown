---
layout:     post
title:      flume ng进击之路 （二）—— kafka sink使用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="概述">概述</h1>

<p>随着kafka的越来越流行，把kafka作为消息（特别是日志）的传输消息队列的越来越多，同时其他各种开源工具都开始跟kafka对接。 <br>
在（一）中我们可以了解到，flume是常用语日志采集的工具，而kafka又常用于日志传输，因此对flume支持kafka的需求也很多，而官网也在提供flume的kafka sink，但貌似支持的配置不好，所有我们可以借助广大网页的力量，来实现kafka sink的功能。</p>



<h1 id="使用">使用</h1>

<ol>
<li>下载 <br>
地址：<a href="https://github.com/beyondj2ee/flumeng-kafka-plugin" rel="nofollow">https://github.com/beyondj2ee/flumeng-kafka-plugin</a> <br>
这是github上网友提供的flume kafka的插件，我在生产环境中也使用到很多场景，还是比较稳定。</li>
<li>解压处理 <br>
下载后解压，解压目录为：flumeng-kafka-plugin-master，文件夹下flumeng-kafka-plugin目录结构如下： <br>
<ul><li>conf</li>
<li>libs</li>
<li>package</li>
<li>src</li>
<li>pom.xml</li></ul></li>
</ol>

<p>其中，libs和package包有我们需要的引入的jar包，conf下有示例的flume配置。 <br>
3. 配置 <br>
将libs和package下的所有jar包都放入flume目录的lib目录下； <br>
配置flume-conf.properties <br>
<strong>flume kafka sink配置</strong></p>

<pre class="prettyprint"><code class="language-java hljs ">############################################
#  producer config
###########################################

#agent section
producer.sources = s
producer.channels = c
producer.sinks = r

#source section
producer.sources.s.type = exec
producer.sources.s.command = tail -f /home/user/xxx/xxx.log
producer.sources.s.channels = c

# Each sink type must be defined
producer.sinks.r.type = org.apache.flume.plugins.KafkaSink
producer.sinks.r.metadata.broker.list=<span class="hljs-number">127.0</span><span class="hljs-number">.0</span><span class="hljs-number">.1</span>:<span class="hljs-number">9092</span>
producer.sinks.r.partition.key=<span class="hljs-number">0</span>
producer.sinks.r.partitioner.class=org.apache.flume.plugins.SinglePartition
producer.sinks.r.serializer.class=kafka.serializer.StringEncoder
producer.sinks.r.request.required.acks=<span class="hljs-number">0</span>
producer.sinks.r.max.message.size=<span class="hljs-number">1000000</span>
producer.sinks.r.producer.type=sync
producer.sinks.r.custom.encoding=UTF-<span class="hljs-number">8</span>
producer.sinks.r.custom.topic.name=test

#Specify the channel the sink should use
producer.sinks.r.channel = c

# Each channels type is defined.
producer.channels.c.type = memory
producer.channels.c.capacity = <span class="hljs-number">1000</span></code></pre>

<p><strong>flume kafka source配置</strong></p>

<pre class="prettyprint"><code class="language-java hljs ">############################################
#   consumer config
###########################################

consumer.sources = s
consumer.channels = c
consumer.sinks = r

consumer.sources.s.type = seq
consumer.sources.s.channels = c
consumer.sinks.r.type = logger

consumer.sinks.r.channel = c
consumer.channels.c.type = memory
consumer.channels.c.capacity = <span class="hljs-number">100</span>

consumer.sources.s.type = org.apache.flume.plugins.KafkaSource
consumer.sources.s.zookeeper.connect=<span class="hljs-number">127.0</span><span class="hljs-number">.0</span><span class="hljs-number">.1</span>:<span class="hljs-number">2181</span>
consumer.sources.s.group.id=testGroup
consumer.sources.s.zookeeper.session.timeout.ms=<span class="hljs-number">400</span>
consumer.sources.s.zookeeper.sync.time.ms=<span class="hljs-number">200</span>
consumer.sources.s.auto.commit.interval.ms=<span class="hljs-number">1000</span>
consumer.sources.s.custom.topic.name=test
consumer.sources.s.custom.thread.per.consumer=<span class="hljs-number">4</span></code></pre>

<ol>
<li>启动 <br>
kafka sink启动命令./bin/flume-ng agent -n producer –conf conf -f conf/flume-conf.properties</li>
</ol>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>