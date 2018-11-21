---
layout:     post
title:      Flume根据文件中每行开始的日期进行文件夹分类导入HDFS
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>这里使用Flume只需要创建一个新的flume配置文件就可以了，我这里将配置文件取名为access_hdfs.properties，下面是配置文件内容：</p>

<pre class="has">
<code>#-----------------access_hdfs.properties---------------#
#--------------------Edit by Cheengvho-----------------#
agent1.sources = src1
agent1.channels = ch1
agent1.sinks = sink1

agent1.sources.src1.interceptors = i1
agent1.sources.src1.interceptors.i1.type = regex_extractor
agent1.sources.src1.interceptors.i1.regex = ^(?:\\n)?(\\d\\d\\d\\d-\\d\\d-\\d\\d\\s\\d\\d:\\d\\d)
agent1.sources.src1.interceptors.i1.serializers = s1
agent1.sources.src1.interceptors.i1.serializers.s1.type = org.apache.flume.interceptor.RegexExtractorInterceptorMillisSerializer
agent1.sources.src1.interceptors.i1.serializers.s1.name = timestamp
agent1.sources.src1.interceptors.i1.serializers.s1.pattern = yyyy-MM-dd HH:mm


agent1.sources.src1.type = spooldir
agent1.sources.src1.spoolDir = /var/log/flume
agent1.sources.src1.channels = ch1

agent1.sinks.sink1.type = hdfs
agent1.sinks.sink1.hdfs.path = /flume/events/cheengvho/%Y%m%d
agent1.sinks.sink1.hdfs.filePrefix = %Y-%m-%d
agent1.sinks.sink1.hdfs.fileSuffix = .log
agent1.sinks.sink1.channel = ch1

agent1.sinks.sink1.hdfs.rollInterval = 0
agent1.sinks.sink1.hdfs.rollSize = 4000000
agent1.sinks.sink1.hdfs.rollCount = 0
agent1.sinks.sink1.hdfs.fileType = DataStream

agent1.channels.ch1.type = memory
agent1.channels.ch1.capacity = 1000
agent1.channels.ch1.transactionCapacity = 1000
</code></pre>

<p>用这个配置文件启动flume</p>

<pre class="has">
<code>$ flume-ng agent --conf /etc/flume-ng/conf --conf--file /etc/flume-ng/conf/access_hdfs.properties \
--name agent1 -Dflume.root.logger=INFO,console</code></pre>

<p> </p>            </div>
                </div>