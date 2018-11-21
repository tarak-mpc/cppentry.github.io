---
layout:     post
title:      flume使用（四）：taildirSource多文件监控实时采集
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：					https://blog.csdn.net/maoyuanming0806/article/details/79391010				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><span class="content">                                    <a href="http://blog.csdn.net/maoyuanming0806/article/details/79371123" rel="nofollow">flume使用（一）：入门demo</a>                                                                    <br></span></p><p><span class="content">                                    <a href="http://blog.csdn.net/maoyuanming0806/article/details/79376600" rel="nofollow">flume使用（二）：采集远程日志数据到MySql数据库</a></span></p><p><span class="content">                                    <a href="http://blog.csdn.net/maoyuanming0806/article/details/79380826" rel="nofollow">flume使用（三）：实时log4j日志通过flume输出到MySql数据库</a>                                    <em class="tags"></em>                                </span><br></p><p>本文在【flume使用（二）：采集远程日志数据到MySql数据库】一文基础之上进行测试操作。本文使用到的：</p><p>flume版本、jdk版本、mysql、数据库表、javaBean、自定义的mysqlSink、以及多agent配置均同【flume使用（二）：采集远程日志数据到MySql数据库】</p><h1>一、需求说明<br></h1><p>实时<strong>监控指定文件目录下的日志文件并实现实时采集</strong>到本机上进行数据库持久化。</p><p></p><h1>二、设计</h1><p>flume1.7.0加入了taildirSource作为agent的source。可以说是spooling directory source+execSource的结合体。可以可以监控多个目录，并且使用正则表达式匹配该目录中的文件名进行实时收集。</p><p>相对于【<span class="content"><a href="http://blog.csdn.net/maoyuanming0806/article/details/79376600" rel="nofollow">flume使用（二）：采集远程日志数据到MySql数据库</a></span><span class="content"><a href="http://blog.csdn.net/maoyuanming0806/article/details/79380826" rel="nofollow"><span class="content"></span></a></span>】一文中设计方案只是变换了taildirSource，其他内容都可以参考该文章</p><p></p><p>数据流：</p><p>（1）<span style="background-color:rgb(255,255,153);">日志文件</span></p><p><span style="background-color:rgb(255,153,102);">----[通过taildirSource监控并采集]-------&gt;</span>（2）<span style="background-color:rgb(255,255,153);">一个agent</span>（source:taildirSource; channel:memory; sink:avro）</p><p><span style="background-color:rgb(255,204,153);">----[通过avro协议传输]------&gt;</span>（3）<span style="background-color:rgb(255,255,153);">一个agent</span>（source:avro; channel:memory; sink:自定义mysqlSink）</p><p><span style="background-color:rgb(255,204,153);">---[mysqlSink进行jdbc持久化]-----&gt;</span>（4）<span style="background-color:rgb(255,255,153);">mysql数据库 <br></span></p><p><span style="background-color:rgb(255,255,153);"><span style="background-color:rgb(255,255,255);"><br></span></span></p><p><span style="background-color:rgb(255,255,153);"><span style="background-color:rgb(255,255,255);">本测试为了方便一些：</span></span></p><p><span style="background-color:rgb(255,255,153);"><span style="background-color:rgb(255,255,255);">（1）<span style="background-color:rgb(255,255,153);"><span style="background-color:rgb(255,255,255);">是linux机器202；</span></span></span></span></p><p><span style="background-color:rgb(255,255,153);"><span style="background-color:rgb(255,255,255);">（2）是linux机器202；</span></span></p><p><span style="background-color:rgb(255,255,153);"><span style="background-color:rgb(255,255,255);">（3）是linux机器201；</span></span></p><p><span style="background-color:rgb(255,255,153);"><span style="background-color:rgb(255,255,255);">（4）数据库在linux机器201上</span></span></p><p><span style="background-color:rgb(255,255,153);"><span style="background-color:rgb(255,255,255);"><br></span></span></p><h1><span style="background-color:rgb(255,255,153);"><span style="background-color:rgb(255,255,255);">三、实施<br></span></span></h1><h2>（1）编写监控并采集指定目录日志文件的flume的配置文件</h2><p>使用taildirSource组件：taildir-avro.conf</p><pre><code class="language-plain">agent1.sources=source1  
agent1.channels=channel1  
agent1.sinks=avroSink  
  
# describe/configure source1  
agent1.sources.source1.type = TAILDIR
agent1.sources.source1.channels = channel1
agent1.sources.source1.channels.skipToEnd = True
# throught JSON format to record the inode, the absolute path and the last position of each tailing file.For to continual work
agent1.sources.source1.positionFile = ./taildir_position.json
# throught Space-separated list file dir which will been tail
agent1.sources.source1.filegroups = f1 f2
# define f1 info.
agent1.sources.source1.filegroups.f1 = /usr/local/tomcat/logs/ac/ac.log.*
agent1.sources.source1.headers.f1.headerKey1 = value1
agent1.sources.source1.filegroups.f2 = /usr/local/tomcat/logs/gi/gi.log.*
agent1.sources.source1.headers.f2.headerKey1 = value2
agent1.sources.source1.headers.f2.headerKey2 = value2-2
agent1.sources.source1.fileHeader = true
  
# use a channel which buffers events in memory  
# type:memory or file is to temporary to save buffer data which is sink using  
agent1.channels.channel1.type=memory  
agent1.channels.channel1.capacity = 1000
agent1.channels.channel1.transactionCapacity = 1000

agent1.sinks.avroSink.type=avro
agent1.sinks.avroSink.channel=channel1
agent1.sinks.avroSink.hostname=192.168.216.201
agent1.sinks.avroSink.port=4545
agent1.sinks.avroSink.batch-size=5</code></pre><p>此配置文件放到日志生产者机器上，也就是我的linux机器202上</p><p><br></p><h1>四、测试<br></h1><p>启动两个linux机器上的flume服务</p><p>都进入flume中的bin目录下执行命令</p><p>1.启动linux机器201<span><span><br></span></span></p><pre><code class="language-plain">./flume-ng agent -c ../conf -f ../conf/avro-mysql.conf -n agent1 -Dflume.root.logger=INFO,console  </code></pre><p>2.启动linux机器202（注意启动的配置文件）<span><span><br></span></span></p><pre><code class="language-plain">./flume-ng agent -c ../conf -f ../conf/taildir-avro.conf -n agent1 -Dflume.root.logger=INFO,console</code></pre><p>3.在linux机器202上模拟日志实时产生，执行命令：</p><pre><code class="language-plain">for i in {1..100};do echo "exec tail-name-mini2-ac-$i,$i" &gt;&gt; /usr/local/tomcat/logs/ac/ac.log.1;sleep 1;done;</code></pre><p>此时刷新mysql数据库表</p><p><img src="https://img-blog.csdn.net/2018022717404926?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbWFveXVhbm1pbmcwODA2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""></p><p><br></p><p>测试断点续传：当flume由于未知错误停止运行后，日志仍然在不断增长，此时手动重新启动flume，flume可以根据记录的采集点接续上次位置继续采集。</p><p>根据配置信息：</p><pre><code class="language-plain">agent1.sources.source1.positionFile = ./taildir_position.json</code></pre><p>断点续传的保存内容，是以JSON格式保存在taildir_position.json文件中</p><p><br></p><p>讨论，taildirSource是有两个可能的问题：</p><p>（1）log4j的日志文件肯定是会根据规则进行滚动的：当*.log满了就会滚动把前文件更名为*.log.1,然后重新进行*.log文件打印。这样flume就会把*.log.1文件当作新文件，又重新读取一遍，导致重复。</p><p>（2）当flume监控的日志文件被移走或删除，flume仍然在监控中，并没有释放资源，当然，在一定时间后会自动释放，这个时间根据官方文档设置默认值是120000ms。</p><p>解决：<span class="content">                                    <a href="http://blog.csdn.net/maoyuanming0806/article/details/79391657" rel="nofollow">flume使用（五）：taildirSource重复获取数据和不释放资源解决办法</a>                                    <em class="tags"></em>                                </span><br></p><p><br></p><p></p><p></p><p></p><br><p></p><h4><br></h4><br>            </div>
                </div>