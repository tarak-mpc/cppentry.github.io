---
layout:     post
title:      flume学习（一）——安装和一个简单的netcat程序
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/wild46cat/article/details/54970353				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1>flume学习（一）——安装和一个简单的netcat程序</h1>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">本文主要参考：</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">http://flume.apache.org/FlumeUserGuide.html#configuring-individual-components<br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"><br></span></div>
<div><span style="font-family:'Microsoft YaHei';"><span style="font-size:18px;"></span><span style="font-size:24px;">首先介绍一下flume的主要作用</span></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"><img src="https://img-blog.csdn.net/20170210144527449?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2lsZDQ2Y2F0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">apache flume 是一个分布式，可靠的，可用的系统，主要用于高效的数据收集，从不同源中移动大量日志数据到中央存储仓库中。</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">apache flume不是严格的只能收集日志数据。但是数据源是自定义的，flume能够用于传递大量的事件数据，包括但不限于网络传输的数据，社交媒体产生的数据，email信息和更多可能的数据源。</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">总结说来，flume（斜槽）的作用就是把数据高效稳定的传输到中央存储仓库中（例如：hdfs）。</span></div>
<div><span style="font-family:'Microsoft YaHei';"><span style="font-size:18px;"></span><span style="font-size:24px;">flume的组成</span></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"><img src="https://img-blog.csdn.net/20170210150617202?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2lsZDQ2Y2F0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">flume的事件由两部分组成，分别是含有byte的数据流——payload和可选的（可以有也可以没有的）字符串属性集合组成。flume代理是一个jvm进程。它通过决定事件从外部的源到哪一个目的地来管理组件（它来管理数据的流向，从哪里到哪里）。</span></div>
<div><span style="font-family:'Microsoft YaHei';"><span style="font-size:18px;"></span><span style="font-size:24px;">flume的agent由三部分组成：source、channel、sink</span></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">需要介绍的概念是</span><span style="font-family:'Microsoft YaHei';font-size:24px;">fan-in</span><span style="font-family:'Microsoft YaHei';font-size:18px;">和</span><span style="font-family:'Microsoft YaHei';font-size:24px;">fan-out</span><span style="font-family:'Microsoft YaHei';font-size:18px;">。</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">fan-in和fan-out可以理解为散入和散出。都是针对source和channel来说的。其中fan-in（散入）是指从多个source进入一个channel中，fan-out（散出）是指从一个source进入多个channel中。</span></div>
<div><span style="font-family:'Microsoft YaHei';"><span style="font-size:18px;"></span><span style="font-size:24px;">下面下载并安装flume</span></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">1、到官网上下载最新的flume。</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">2、tar开到执行的目录 tar -zxvf flume-x.x.x.x</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">3、添加环境变量，修改/etc/profile 中的内容，添加/home/flume/bin到path中</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"><img src="https://img-blog.csdn.net/20170210145412742?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2lsZDQ2Y2F0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div><br></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">4、复制flume-env.sh.template 和flume-conf.properties.template文件</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">cp flume-env.sh.template flume-env.sh</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">cp flume-conf.properties.template flume-conf.properties</span></div>
<div><span style="font-family:'Microsoft YaHei';"><span style="font-size:18px;"></span><span style="font-size:24px;">这样flume的安装就可以了，现在尝试运行一个netcat的例子</span></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">5、修改flume-conf.properties文件</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"></span><pre><code class="language-plain"># Name the components on this agent
a1.sources = r1
a1.sinks = k1
a1.channels = c1

# Describe/configure the source
a1.sources.r1.type = netcat
a1.sources.r1.bind = localhost
a1.sources.r1.port = 44444

# Describe the sink
a1.sinks.k1.type = logger

# Use a channel which buffers events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1</code></pre>6、启动名字为a1的agent。</div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"><span></span></span><pre><code class="language-html">flume-ng agent -n a1 -c conf -f /home/flume/conf/flume-conf.properties -Dflume.root.logger=INFO,console</code></pre><br><img src="https://img-blog.csdn.net/20170210150039859?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2lsZDQ2Y2F0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"><span><br></span></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"><span>7</span>、测试能否正常监听：</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">nc localhost 444444</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">然后输入内容，查看flume中a1的情况。</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"><img src="https://img-blog.csdn.net/20170210150351560?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2lsZDQ2Y2F0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;">现在，基本的flume代理就已经能够正常的运行了。</span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"><br></span></div>
            </div>
                </div>