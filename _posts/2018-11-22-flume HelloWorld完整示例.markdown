---
layout:     post
title:      flume HelloWorld完整示例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>第一步。到http://flume.apache.org/download.html 下载flume 组件。将flume组件上传到linux。</p>
<p>第二步。如果你没有安装JDK  请安装JDK1.6+以上版本的。</p>
<p>第三步。解压flume压缩包。在conf目录下创建example.conf。</p>
<p># example.conf: A single-node Flume configuration<br>
# Name the components on this agent<br>
a1.sources = r1<br>
a1.sinks = k1<br>
a1.channels = c1<br>
# Describe/configure the source<br>
a1.sources.r1.type = netcat<br>
a1.sources.r1.bind = localhost<br>
a1.sources.r1.port = 44444<br>
# Describe the sink<br>
a1.sinks.k1.type = logger<br>
# Use a channel which buffers events in memory<br>
a1.channels.c1.type = memory<br>
# Bind the source and sink to the channel<br>
a1.sources.r1.channels = c1<br>
a1.sinks.k1.channel = c1<br></p>
<p><br></p>
<p>我来解释一下这个配置文件的意思   a1 就是agent的名称。r1、k1、c1分别是source、sinks、channels的别名。这里我们把type设置成netcat。代表打开一个socket监听事件。</p>
<p>需要2个参数  ，一个是绑定IP ，一个是端口号。 名为k1的接收器级别是logger。相当于log4j的INFO 级别的日志。  名为c1的是个内存通道，采用的是默认配置。</p>
<p><br></p>
<p>第四步。使用  bin/flume-ng agent -n a1 -c conf -f conf/example.conf  -Dflume.root.logger=INFO,console 命令启动flume</p>
<p>注意几个重要的参数  -n a1 这个a1 就是对应agent的别名  -c  必须加这个-c  不然log4j日志属性文件找不到 这个命令的作用就是把log4j.properties加入到classpath中。</p>
<p>conf/example.conf 就是配置文件对应的路径。</p>
<p><br></p>
<p>启动成功后会看到如下信息</p>
<p><br></p>
<p></p>
<div><img src="https://img-blog.csdn.net/20170410173255946?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzUxNTcyOTA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></div>
<div><br></div>
<div>第五步。用telnet来连接flume。命令为telnet localhost 44444</div>
<div><br></div>
<div><img src="" alt=""><br></div>
<div><br></div>
<div>现在就能打印日志了  服务端也能接收到日志啦 </div>
<br><p></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
            </div>
                </div>