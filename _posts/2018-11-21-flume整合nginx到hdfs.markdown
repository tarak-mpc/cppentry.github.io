---
layout:     post
title:      flume整合nginx到hdfs
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>前提：</p>

<p>先装flume:</p>

<p>解压flume压缩文件</p>

<p>修改flume-env.sh 里面的java_home目录</p>

<p>添加环境变量(flume)</p>

<p>测试：</p>

<pre class="has">
<code>flume-ng version</code></pre>

<p> 1.进入${FLUME_HOME}的conf目录下</p>

<pre class="has">
<code>cp flume-conf.properties.tmplates flume-conf.properties</code></pre>

<p>2.修改配置文件flume-conf.properties ：</p>

<pre class="has">
<code># 配置Agent
a1.sources = r1
a1.sinks = k1
a1.channels = c1

# 配置Source
a1.sources.r1.type = exec
a1.sources.r1.channels = c1
a1.sources.r1.deserializer.outputCharset = UTF-8

# 配置需要监控的日志输出目录,也就是nginx日志存放目录
a1.sources.r1.command = tail -F /usr/local/nginx/log/access.log

# 配置Sink
a1.sinks.k1.type = hdfs
a1.sinks.k1.channel = c1
a1.sinks.k1.hdfs.useLocalTimeStamp = true

#hdfs地址
a1.sinks.k1.hdfs.path = hdfs://master:9000/flume/events/%Y-%m  
a1.sinks.k1.hdfs.filePrefix = %Y-%m-%d-%H
a1.sinks.k1.hdfs.fileSuffix = .log
a1.sinks.k1.hdfs.minBlockReplicas = 1
a1.sinks.k1.hdfs.fileType = DataStream
a1.sinks.k1.hdfs.writeFormat = Text
a1.sinks.k1.hdfs.rollInterval = 86400
a1.sinks.k1.hdfs.rollSize = 1000000
a1.sinks.k1.hdfs.rollCount = 10000
a1.sinks.k1.hdfs.idleTimeout = 0

# 配置Channel
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# 将三者连接
a1.sources.r1.channel = c1
a1.sinks.k1.channel = c1</code></pre>

<p>3.配置完成后运行：</p>

<pre class="has">
<code>#需注意运行时所在的目录，否则会报错,也就是在${FLUME_HOME}目录下
flume-ng agent --conf ./conf/ --conf-file ./conf/flume-conf.properties --name a1 -Dflume.root.logger=INFO,console</code></pre>

<p>4.开启hdfs,nginx，验证日志是否实时地通过flume写入到hdfs中。</p>            </div>
                </div>