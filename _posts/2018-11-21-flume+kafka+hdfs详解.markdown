---
layout:     post
title:      flume+kafka+hdfs详解
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/jethai/article/details/52345280				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>flume架构图</p><p><a href="http://s3.51cto.com/wyfs02/M00/74/0A/wKiom1YPrdjguqxiAAJR5GnVzeg068.jpg" rel="nofollow"><img src="http://s3.51cto.com/wyfs02/M00/74/0A/wKiom1YPrdjguqxiAAJR5GnVzeg068.jpg" title="课时23：实战案例_flume和kafka安装.avi_20151003_183018.077.jpg" alt="wKiom1YPrdjguqxiAAJR5GnVzeg068.jpg"></a></p><p><span style="color:rgb(255,0,0);font-size:18px;"><strong>单节点flume配置</strong></span><br></p><p>flume-1.4.0  启动flume</p><pre class="brush:bash;toolbar:false">bin/flume-ng agent --conf ./conf  -f conf/flume-conf.properties -Dflume.root.logger=DEBUG,console -n agent</pre><p>-n表示配置文件中agent的名字</p><pre><code class="language-java">agent.sources = r1
agent.sinks = s1
agent.channels = c1
agent.sources.r1.channels = c1
agent.sinks.s1.channel = c1

#Describe/configure the sources
agent.sources.r1.type = exec
agent.sources.r1.command = tail -F /home/flume/loginfo
#Use a channel which buffers events in memory
agent.channels.c1.type = memory
agent.channels.c1.capacity = 1000  #Event
agent.channels.c1.transactionCapacity = 100
agent.sinks.s1.type = logger</code></pre><p><br></p><p><a href="http://s3.51cto.com/wyfs02/M02/74/08/wKioL1YPs5XgkmmhAAIZ3G0tbb0587.jpg" rel="nofollow"><img src="http://s3.51cto.com/wyfs02/M02/74/08/wKioL1YPs5XgkmmhAAIZ3G0tbb0587.jpg" title="课时24：结合flume_kafka_hdfs_hive实现日志处理.avi_20151003_185520.258.jpg" alt="wKioL1YPs5XgkmmhAAIZ3G0tbb0587.jpg"></a></p><p><br></p><p><br></p><p><a href="http://s3.51cto.com/wyfs02/M00/74/0A/wKiom1YPs5jyA5Q9AANI-ME5zqU247.jpg" rel="nofollow"><img src="http://s3.51cto.com/wyfs02/M00/74/0A/wKiom1YPs5jyA5Q9AANI-ME5zqU247.jpg" title="课时23：实战案例_flume和kafka安装.avi_20151003_184118.351.jpg" alt="wKiom1YPs5jyA5Q9AANI-ME5zqU247.jpg"></a></p><p><span style="color:rgb(255,0,0);font-size:20px;"><strong><br></strong></span></p><p><span style="color:rgb(255,0,0);font-size:20px;"><strong>flume-1.4.0 + kafka-0.7.2+hdfs   flume配置</strong></span><br></p><p><br></p><pre class="brush:xml;toolbar:false">agent.sources = r1
agent.sinks = s_kafka s_hdfs
agent.channels = c_kafka c_hdfs
agent.sources.r1.channels = c_kafka c_hdfs

agent.sources.r1.type = exec
#下面这个脚本tail某个日志
agent.sources.r1.command = tail -F /home/flume/loginfo
agent.channels.c_kafka.type = memory
agent.channels.c_hdfs.type = memory

agent.sinks.s_kafka.type = com.sink.FirstkafkaSink
agent.sinks.s_kafka.channel = c_kafka
#kafka需要连接zk，写入broker数据
agent.sinks.s_kafka.zkconnect = localhost:2181
agent.sinks.s_kafka.topic = test
agent.sinks.s_kafka.serializer.class = kafka.serializer.StringEncoder
agent.sinks.s_kafka.metadata.broker.list = localhost:9092 #配置文件server.properties
agent.sinks.s_kafka.custom.encoding = UTF-8


agent.sinks.s_hdfs.type = hdfs
agent.sinks.s_hdfs.channel = c_hdfs
#默认端口8020
agent.sinks.s_hdfs.hdfs.path = hdfs://localhost:9000/root/source
agent.sinks.s_hdfs.hdfs.filePrefix = events-
agent.sinks.s_hdfs.hdfs.fileType = DataStream
agent.sinks.s_hdfs.hdfs.writeFormat = Text
agent.sinks.s_hdfs.hdfs.rollCount = 30 #达到某一数值记录生成文件
agent.sinks.s_hdfs.hdfs.rollSize = 0
agent.sinks.s_hdfs.hdfs.rollInterval = 0
agent.sinks.s_hdfs.hdfs.useLocalTimeStamp = true
agent.sinks.s_hdfs.hdfs.idleTimeout = 51
agent.sinks.s_hdfs.hdfs.threadsPoolSize = 2</pre><p><br></p><h1 class="article-title"><span style="font-size:16px;">Flume内置channel,source,sink汇总</span><a style="font-size:20px;text-decoration:underline;" href="http://www.iteblog.com/archives/948" rel="nofollow"><span style="font-size:20px;"></span></a></h1><h1 class="article-title"><a style="font-size:20px;text-decoration:underline;" href="http://www.iteblog.com/archives/948" rel="nofollow"><span style="font-size:20px;">http://www.iteblog.com/archives/948</span></a></h1><p><a style="font-size:20px;text-decoration:underline;" href="http://www.iteblog.com/archives/948" rel="nofollow"><span style="font-size:20px;"></span></a><br></p><p><br></p><p>本文出自 “<a href="http://tianxingzhe.blog.51cto.com" rel="nofollow">点滴积累</a>” 博客，请务必保留此出处<a href="http://tianxingzhe.blog.51cto.com/3390077/1700049" rel="nofollow">http://tianxingzhe.blog.51cto.com/3390077/1700049</a></p>            </div>
                </div>