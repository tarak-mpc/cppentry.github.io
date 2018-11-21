---
layout:     post
title:      Kafka整合Flume
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/daerzei/article/details/82182235				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>大数据进行流式数据处理的时候Flume采集数据，Kafka消费数据，<code>Spark Streaming</code>处理数据是一种非常常见的架构，这里记录一下<code>Kafka</code>整合<code>Flume</code>的不过，以备后用</p>
<p>这里默认已经安装好了<code>Kafka</code>和<code>Flume</code>，不再介绍，大家可以自行去网上找下</p>
<p>其实最主要的就是为Flume创建kafka的配置文件<code>kafka-conf.properties</code>：</p>
<pre><code># The configuration file needs to define the sources,
# the channels and the sinks.
# Sources, channels and sinks are defined per agent,
# in this case called 'agent'

agent.sources = r1
agent.channels = c1
agent.sinks = s1

# Flume中Source相关的配置
# 指定要监控的类型，比如说端口号，目录
agent.sources.r1.type = spooldir
# 指定要监控的目录
agent.sources.r1.spoolDir = /opt/shortcut/flume/logs/data
agent.sources.r1.fileHeader = true

# Flume中Sink相关的配置
# 指定输出结果到Kafka
agent.sinks.s1.type = org.apache.flume.sink.kafka.KafkaSink
# 指定输出结果到Kafka指定的Topic为test
agent.sinks.s1.topic = test
# 指定Kafka的brokerList为cm02.spark.com:9092
agent.sinks.s1.brokerList = cm02.spark.com:9092
agent.sinks.s1.requiredAcks = 1
agent.sinks.s1.batchSize = 2

# Flume中Channel相关的配置
agent.channels.c1.type = memory
agent.channels.c1.capacity = 100
agent.sources.r1.channels = c1
agent.sinks.s1.channel = c1
</code></pre>
<p>启动Flume：</p>
<pre class=" language-shell"><code class="prism  language-shell">bin/flume-ng agent --conf conf -f ./conf/kafka-conf.properties -n agent -Dflume.root.logger<span class="token operator">=</span>INFO,console
</code></pre>
<p><img src="https://img-blog.csdn.net/20180829140134474?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2RhZXJ6ZWk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="Kafka整合Flume_01.png"><br>
<img src="https://img-blog.csdn.net/20180829140146627?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2RhZXJ6ZWk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="Kafka整合Flume_02.png"><br>
启动Kafka消费者：</p>
<pre class=" language-shell"><code class="prism  language-shell">bin/kafka-console-consumer.sh --zookeeper cm01.spark.com:2181,cm02.spark.com:2181,cm03.spark.com:2181 --topic <span class="token function">test</span> --from-beginning
</code></pre>
<p>上传一个文本文件到Flume监听的目录：</p>
<p>在Kafka的消费者中查看消费情况：<br>
<img src="https://img-blog.csdn.net/2018082914020791?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2RhZXJ6ZWk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="Kafka整合Flume_03.png"><br>
<img src="https://img-blog.csdn.net/20180829140217951?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2RhZXJ6ZWk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="Kafka整合Flume_04.png"></p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>