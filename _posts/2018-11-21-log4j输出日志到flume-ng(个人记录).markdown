---
layout:     post
title:      log4j输出日志到flume-ng(个人记录)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<pre><code class="language-html">1. flume 配置文件text.conf</code></pre><pre><code class="language-html">
</code></pre><pre><code class="language-html"># A single-node Flume configuration

# Name the components on this agent
agent1.sources = source1
agent1.sinks = sink1
agent1.channels = channel1

# Describe/configure source1
agent1.sources.source1.type = avro
agent1.sources.source1.bind = 0.0.0.0
agent1.sources.source1.port = 4444

# Describe sink1
#文件的方式输出
agent1.sinks.sink1.type = file_roll
#目录
agent1.sinks.sink1.sink.directory=/home/valsong/log

# Use a channel which buffers events in memory
agent1.channels.channel1.type = memory
agent1.channels.channel1.capacity = 1000
agent1.channels.channel1.transactionCapactiy = 100

# Bind the source and sink to the channel
agent1.sources.source1.channels = channel1
agent1.sinks.sink1.channel = channel1</code></pre>
<p><br></p>
<p>2. log4j.properties</p>
<p></p><pre><code class="language-html">log4j.appender.console=org.apache.log4j.ConsoleAppender
log4j.appender.console.target=System.out
log4j.appender.console.layout=org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern="%d{yyyy-MM-dd HH:mm:ss} %p [%c:%L] - %m%n
 
log4j.appender.flume = org.apache.flume.clients.log4jappender.Log4jAppender
#IP
log4j.appender.flume.Hostname = localhost
#端口号
log4j.appender.flume.Port = 4444
log4j.appender.flume.UnsafeMode = true
log4j.appender.flume.layout=org.apache.log4j.PatternLayout
log4j.appender.flume.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss} %p [%c:%L] - %m%n

log4j.rootLogger=INFO,flume</code></pre><br><br><p><br></p>
            </div>
                </div>