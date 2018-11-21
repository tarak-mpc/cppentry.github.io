---
layout:     post
title:      Ceontos7下安装Flume及问题
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/weixin_40678969/article/details/79211678				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>      Flume是一个强大的采集日志信息的工具，它适用大部分的日志采集场景。它的安装配置也非常简单，下面就一起来看看吧！</p>
<p>      一、安装Flume</p>
<p></p>
<p>1、Flume<span style="font-family:'宋体';">的安装非常简单，只需要解压即可，但是必须先配置hadoop环境，关于hadoop的配置这里就不多说了。</span></p>
<p>上传flume的安装包然后解压  tar -zxvf apache-flume-1.6.0-bin.tar.gz</p>
<p><span style="font-family:'宋体';">然后进入</span>flume<span style="font-family:'宋体';">的目录，修改</span><span style="font-family:Calibri;">conf</span><span style="font-family:'宋体';">下的</span><span style="font-family:Calibri;">flume-env.sh</span><span style="font-family:'宋体';">，在里面配置</span><span style="font-family:Calibri;">JAVA_HOME</span></p>
<p>2<span style="font-family:'宋体';">、根据数据采集的</span>需求<strong>配置采集方案</strong>，描述在配置文件中(<span style="font-family:'宋体';">文件名可任意自定义</span><span style="font-family:Calibri;">)，根据不同的采集方案设置，配置文件会有一点区别，我们后面再介绍。</span></p>
<p>3<span style="font-family:'宋体';">、</span><strong>指定采集方案配置文件</strong><span style="font-family:'宋体';">，在相应的节点上启动</span>flume agent</p>
<p> 先用一个最简单的例子来测试一下程序环境是否正常</p>
<p>1、<span style="font-family:'宋体';">先在</span>flume<span style="font-family:'宋体';">的</span><span style="font-family:Calibri;">conf</span><span style="font-family:'宋体';">目录下新建一个文件</span></p>
<p>vi   netcat-logger.conf</p>
<table><tbody><tr><td valign="top">
<p># <span style="font-family:'宋体';">定义这个</span><span style="font-family:Calibri;">agent</span><span style="font-family:'宋体';">中各组件的名字</span></p>
<p>a1.sources = r1</p>
<p>a1.sinks = k1</p>
<p>a1.channels = c1</p>
<p> </p>
<p># <span style="font-family:'宋体';">描述和配置</span><span style="font-family:Calibri;">source</span><span style="font-family:'宋体';">组件：</span><span style="font-family:Calibri;">r1</span></p>
<p>a1.sources.r1.type = netcat</p>
<p>a1.sources.r1.bind = localhost</p>
<p>a1.sources.r1.port = 44444</p>
<p> </p>
<p># <span style="font-family:'宋体';">描述和配置</span><span style="font-family:Calibri;">sink</span><span style="font-family:'宋体';">组件：</span><span style="font-family:Calibri;">k1</span></p>
<p>a1.sinks.k1.type = logger</p>
<p> </p>
<p># <span style="font-family:'宋体';">描述和配置</span><span style="font-family:Calibri;">channel</span><span style="font-family:'宋体';">组件，此处使用是内存缓存的方式</span></p>
<p>a1.channels.c1.type = memory</p>
<p>a1.channels.c1.capacity = 1000</p>
<p>a1.channels.c1.transactionCapacity = 100</p>
<p> </p>
<p># <span style="font-family:'宋体';">描述和配置</span><span style="font-family:Calibri;">source  channel   sink</span><span style="font-family:'宋体';">之间的连接关系</span></p>
<p>a1.sources.r1.channels = c1</p>
<p>a1.sinks.k1.channel = c1</p>
</td>
</tr></tbody></table><p> </p>
<p>2、<span style="font-family:'宋体';">启动</span>agent<span style="font-family:'宋体';">去采集数据</span></p>
<table><tbody><tr><td valign="top">
<p>bin/flume-ng agent -c conf -f conf/netcat-logger.conf -n a1  -Dflume.root.logger=INFO,console</p>
</td>
</tr></tbody></table><p>-c conf   <span style="font-family:'宋体';">指定</span><span style="font-family:Calibri;">flume</span><span style="font-family:'宋体';">自身的配置文件所在目录</span></p>
<p>-f conf/netcat-logger.con  <span style="font-family:'宋体';">指定我们所描述的采集方案</span></p>
<p>-n a1  <span style="font-family:'宋体';">指定我们这个</span><span style="font-family:Calibri;">agent</span><span style="font-family:'宋体';">的名字</span></p>
<p>3、测试</p>
<p><span style="font-family:'宋体';">先要往</span>agent<span style="font-family:'宋体';">采集监听的端口上发送数据，让</span><span style="font-family:Calibri;">agent</span><span style="font-family:'宋体';">有数据可采</span></p>
<p><span style="font-family:'宋体';">随便在一个能跟</span>agent<span style="font-family:'宋体';">节点联网的机器上</span></p>
<p>telnet anget-hostname  port   <span style="font-family:'宋体';">（</span><span style="font-family:Calibri;">telnet localhost 44444</span><span style="font-family:'宋体';">）
</span></p>
<p> </p>
<p> </p>
<br>            </div>
                </div>