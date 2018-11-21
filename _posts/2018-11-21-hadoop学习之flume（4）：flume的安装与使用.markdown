---
layout:     post
title:      hadoop学习之flume（4）：flume的安装与使用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_32166627/article/details/53545439				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>flume是一个海量日志采集，聚合，传输的系统</p>
<p>一，安装</p>
<p>首先下载解压。</p>
<p>然后将flume/conf下，cp -a flume-env.sh.template flume-env.sh</p>
<p>修改JAVA_HOME为本机的jdk安装目录即可。</p>
<p>二，flume使用</p>
<p>flume通过读取按照一定规则写好的配置文件，来启动agent，进行传输，每一行数据被封装成一个event</p>
<p>举例介绍配置文件的写法</p>
<p>1，监听一个文件夹，并将文件夹中新增内容输出到控制台（屏幕）</p>
<p># vim spooldir-logger.conf</p>
<p></p>
<pre><code class="language-html">a1.sources = r1
a1.sinks = k1
a1.channels = c1

# Describe/configure the source
#监听目录,spoolDir指定目录, fileHeader要不要给文件夹前坠名
a1.sources.r1.type = spooldir
a1.sources.r1.spoolDir = /root/data_hadoop/data_flume
a1.sources.r1.fileHeader = true

# Describe the sink
a1.sinks.k1.type = logger

# Use a channel which buffers events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
</code></pre><br>
保存退出。然后启动agent：
<p></p>
<p></p>
<pre><code class="language-html">$ bin/flume-ng agent -c conf -f conf/spooldir-logger.conf -n a1 -Dflume.root.logger=INFO,console
</code></pre><br>
参数 -Dflume.root.logger=INFO,console 指定结果输出到控制台。<br><p></p>
<p>这样一来，你在/root/data_hadoop/data_flume文件夹下添加文件，都会将信息显示在屏幕上。</p>
<p><br></p>
<p>2，监听一个文件夹，将其下新增文件上传到hdfs上。</p>
<p>vim spooldir-hdfs.conf</p>
<p></p>
<pre><code class="language-html">a1.sources = r1
a1.sinks = k1
a1.channels = c1

# Describe/configure the source
#监听目录,spoolDir指定目录, fileHeader要不要给文件夹前坠名
a1.sources.r1.type = spooldir
a1.sources.r1.spoolDir = /root/data_hadoop/data_flume
a1.sources.r1.fileHeader = true

# Describe the sink
a1.sinks.k1.type = hdfs
a1.sinks.k1.channel = c1
a1.sinks.k1.hdfs.path = /flume/events/%y-%m-%d/%H%M/
a1.sinks.k1.hdfs.filePrefix = events-
a1.sinks.k1.hdfs.round = true
a1.sinks.k1.hdfs.roundValue = 10
a1.sinks.k1.hdfs.roundUnit = minute
a1.sinks.k1.hdfs.rollCount = 2
a1.sinks.k1.hdfs.batchSize = 1
a1.sinks.k1.hdfs.useLocalTimeStamp = true
a1.sinks.k1.hdfs.fileType = DataStream

# Use a channel which buffers events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
</code></pre>保存退出，然后启动agent:
<p></p>
<p></p>
<pre><code class="language-sql">bin/flume-ng agent -c conf -f conf/spooldir-hdfs.conf -n a1</code></pre><br>
然后可以在本地文件夹下新增文件，flume就按照指定的规则上传到hdfs上。<br><br><p></p>
            </div>
                </div>