---
layout:     post
title:      windows搭建flume：基于源码的二次开发
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主大壮原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_33792843/article/details/75387669				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>windows搭建flume：基于源码的二次开发</p>
<p></p>
<p style="color:rgb(51,51,51);font-family:Arial, 'Microsoft YaHei';font-size:18px;">
​</p>
<span></span><span></span><span></span><span> </span><img src="https://wx3.sinaimg.cn/large/005A3Hwygy1fhp7ey2rh2j30gw04ut8o.jpg" alt="" style="border:0px;display:block;">移除点击此处添加图片说明文字
<p>​先说一下flume的架构，这部分快速阅读，flume架构倒是不难，容易理解。</p>
<p>flume是分布式的，可靠的，高可用的，用于对不同来源的大量的日志数据进行有效收集、聚集和移动，并以集中式的数据存储的系统。</p>
<p>flume目前是apache的一个顶级项目。</p>
<p><br></p>
<h2>flume中的重要模型</h2>
<h2>1.flume Event：</h2>
<p>flume 事件，被定义为一个具有有效荷载的字节数据流和可选的字符串属性集。</p>
<h2>2.flume Agent：</h2>
<p>flume 代理，是一个进程承载从外部源事件流到下一个目的地的过程。包含source channel 和 sink。</p>
<h2>3.Source</h2>
<p>数据源，消耗外部传递给他的事件，外部源将数据按照flume Source 能识别的格式将Flume 事件发送给flume Source。</p>
<h2>4.Channel</h2>
<p>数据通道，是一个被动的存储，用来保持事件，直到由一个flume Sink消耗。</p>
<h2>5.Sink</h2>
<p>数据汇聚点，代表外部数据存放位置。发送flume event到指定的外部目标。</p>
<p><br></p>
<p>flume基本配置</p>
<p><span class="font" style="color:#9053b9;">＃example.conf：单节点Flume配置</span></p>
<p><span class="font" style="color:#9053b9;">＃命名Agenta1的组件</span></p>
<p><span class="font" style="color:#9053b9;">a1.sources=r1</span></p>
<p><span class="font" style="color:#9053b9;">a1.sinks=k1</span></p>
<p><span class="font" style="color:#9053b9;">a1.channels=c1</span></p>
<p><br></p>
<p><span class="font" style="color:#9053b9;">＃描述/配置Source</span></p>
<p><span class="font" style="color:#9053b9;">a1.sources.r1.type=netcat</span></p>
<p><span class="font" style="color:#9053b9;">a1.sources.r1.bind=0.0.0.0</span></p>
<p><span class="font" style="color:#9053b9;">a1.sources.r1.port=44444</span></p>
<p><br></p>
<p><span class="font" style="color:#9053b9;">＃描述Sink</span></p>
<p><span class="font" style="color:#9053b9;">a1.sinks.k1.type=logger</span></p>
<p><br></p>
<p><span class="font" style="color:#9053b9;">＃描述内存Channel</span></p>
<p><span class="font" style="color:#9053b9;">a1.channels.c1.type=memory</span></p>
<p><span class="font" style="color:#9053b9;">a1.channels.c1.capacity=1000</span></p>
<p><span class="font" style="color:#9053b9;">a1.channels.c1.transactionCapacity=100</span></p>
<p><br></p>
<p><span class="font" style="color:#9053b9;">＃为Channle绑定Source和Sink</span></p>
<p><span class="font" style="color:#9053b9;">a1.sources.r1.channels=c1</span></p>
<p><span class="font" style="color:#9053b9;">a1.sinks.k1.channel=c1</span></p>
<p><br></p>
<p><span class="bold" style="font-weight:bold;"><span class="italic" style="font-style:italic;font-weight:bold;"> 现在介绍几种比较重要的Source </span></span><br></p>
<h2>1.    Avro Source</h2>
<p>监听AVRO端口来接受来自外部AVRO客户端的事件流。利用Avro Source可以实现多级流动、扇出流、扇入流等效果。另外也可以接受通过flume提供的Avro客户端发送的日志信息。</p>
<p><span class="bold" style="font-weight:bold;">编写配置文件  修改上面给出的配置文件，除了Source部分配置不同，其余部分都一样。不同的地方如下：</span></p>
<p>＃描述/配置Source a1.sources.r1.type = avro a1.sources.r1.bind = 0.0.0.0 a1.sources.r1.port = 44444</p>
<h2>2.    Spooling Directory Source</h2>
<p>这个Source允许你将将要收集的数据放置到"自动搜集"目录中。这个Source将监视该目录，并将解析新文件的出现。事件处理逻辑是可插拔的，当一个文件被完全读入通道，它会被重命名或可选的直接删除。</p>
<p>要注意的是，放置到自动搜集目录下的文件不能修改，如果修改，则flume会报错。另外，也不能产生重名的文件，如果有重名的文件被放置进来，则flume会报错。</p>
<p><span class="bold" style="font-weight:bold;">编写配置文件  修改上面给出的配置文件，除了Source部分配置不同，其余部分都一样。不同的地方如下：</span></p>
<p>＃描述/配置Source a1.sources.r1.type = spooldir a1.sources.r1.spoolDir=/home/park/work/apache-flume-1.6.0-bin/mydata</p>
<h2>3.    NetCat Source</h2>
<p>一个NetCat Source用来监听一个指定端口，并将接收到的数据的每一行转换为一个事件。</p>
<h2>4.    HTTP Source</h2>
<p>HTTP Source接受HTTP的GET和POST请求作为Flume的事件,其中GET方式应该只用于试验。</p>
<p>该Source需要提供一个可插拔的"处理器"来将请求转换为事件对象，这个处理器必须实现HTTPSourceHandler接口，该处理器接受一个 HttpServletRequest对象，并返回一个Flume Envent对象集合。</p>
<p>从一个HTTP请求中得到的事件将在一个事务中提交到通道中。因此允许像文件通道那样对通道提高效率。</p>
<p>如果处理器抛出一个异常，Source将会返回一个400的HTTP状态码。</p>
<p>如果通道已满，无法再将Event加入Channel，则Source返回503的HTTP状态码，表示暂时不可用。</p>
<p><span class="bold" style="font-weight:bold;">编写配置文件  修改上面给出的配置文件，除了Source部分配置不同，其余部分都一样。不同的地方如下：</span></p>
<p>＃描述/配置Source a1.sources.r1.type = http a1.sources.r1.port = 66666</p>
<p>好的，我们引入svn代码，解决maven依赖。</p>
<span></span><span></span><span></span><span> </span><img src="https://wx2.sinaimg.cn/large/005A3Hwygy1fhp0lh1g98j30ml0crq5d.jpg" alt="" style="border:0px;display:block;">移除点击此处添加图片说明文字
<p>​用阿里云的中央仓库：</p>
<span></span><span></span><span></span><span> </span><img src="https://wx3.sinaimg.cn/large/005A3Hwygy1fhp0mb78xzj30m304fq31.jpg" alt="" style="border:0px;display:block;">移除点击此处添加图片说明文字
<p>​然后等待maven下载好的同时，分析导致flume数据丢失的环节，因为已经查看了flume的报错，得知两个原因可能导致：</p>
<p>1、flume报错超时timeout</p>
<p>2.因为一个文件输入超过了最大值，直接break了。</p>
<span></span><span></span><span></span><span> </span><img src="https://wx3.sinaimg.cn/large/005A3Hwygy1fhp0p2xmt8j30q20f7q4h.jpg" alt="" style="border:0px;display:block;"><p>​那现在就好玩了，恶心的是这种当时开发完了很爽的，结果留下了坑的，没办法痛苦的去读代码，</p>
<span></span><span></span><span></span><span> </span><img src="https://wx1.sinaimg.cn/large/005A3Hwygy1fhp0q7a0a8j30p70a1myn.jpg" alt="" style="border:0px;display:block;">移除点击此处添加图片说明文字
<p>​在这里，对了，这里有很多类任然在报错，不用着急，maven正在引入依赖jar包，稍等会儿即可。</p>
<p>这样，等了很久，也没见好转，这样，也不浪费时间了，自己搭建一个。windows版本的吧，容易调试。</p>
<p>首先环境变量不用多说了，其次：</p>
<p><span class="bold" style="font-weight:bold;">开始下载安装和配置：</span></p>
<p><span class="bold" style="font-weight:bold;">（1）官网：http://archive.apache.org/dist/flume/   下载：apache-flume-1.6.0-bin.tar.gz</span></p>
<p><span class="bold" style="font-weight:bold;">（2）在合适的位置解压</span></p>
<p><span class="bold" style="font-weight:bold;">（3）参照官网的user guide配置:</span></p>
<p><span class="bold" style="font-weight:bold;">  英文指南：http://flume.apache.org/releases/content/1.6.0/FlumeUserGuide.html</span></p>
<p><span class="bold" style="font-weight:bold;">  中文指南：http://www.jianshu.com/p/41de9a715273</span></p>
<p>安装的时候，报错：</p>
<span></span><span></span><span></span><span> </span><img src="https://wx2.sinaimg.cn/large/005A3Hwygy1fhp3impx0ij30ku071my1.jpg" alt="" style="border:0px;display:block;">移除点击此处添加图片说明文字
<p>​调整一下example.conf</p>
<p>输出日志如下：</p>
<span></span><span></span><span></span><span> </span><img src="https://wx3.sinaimg.cn/large/005A3Hwygy1fhp7d6s95pj30i4091gm3.jpg" alt="" style="border:0px;display:block;">移除点击此处添加图片说明文字
<p>​说明flume启动成功，windows的flume已经可以用了</p>
<p>将example.conf粘贴出来：</p>
<p><span class="italic" style="font-style:italic;font-weight:normal;"><span class="font" style="color:#9053b9;">a1.sources = r1</span></span></p>
<p><span class="italic" style="font-style:italic;font-weight:normal;"><span class="font" style="color:#9053b9;">a1.sinks = k1</span></span></p>
<p><span class="italic" style="font-style:italic;font-weight:normal;"><span class="font" style="color:#9053b9;">a1.channels = c1</span></span></p>
<p><br></p>
<p><span class="italic" style="font-style:italic;font-weight:normal;"><span class="font" style="color:#9053b9;"># Describe/configure the source</span></span></p>
<p><span class="italic" style="font-style:italic;font-weight:normal;"><span class="font" style="color:#9053b9;">a1.sources.r1.type = exec</span></span></p>
<p><span class="italic" style="font-style:italic;font-weight:normal;"><span class="font" style="color:#9053b9;">a1.sources.r1.shell = /bin/bash -c</span></span></p>
<p><span class="italic" style="font-style:italic;font-weight:normal;"><span class="font" style="color:#9053b9;">a1.sources.r1.channels = c1</span></span></p>
<p><span class="italic" style="font-style:italic;font-weight:normal;"><span class="font" style="color:#9053b9;">a1.sources.r1.command = tail -F /opt/apps/logs/tail4.log</span></span></p>
<p><br></p>
<p><br></p>
<p><span class="italic" style="font-style:italic;font-weight:normal;"><span class="font" style="color:#9053b9;"># Use a channel which buffers events in memory</span></span></p>
<p><span class="italic" style="font-style:italic;font-weight:normal;"><span class="font" style="color:#9053b9;">a1.channels.c1.type = memory</span></span></p>
<p><span class="italic" style="font-style:italic;font-weight:normal;"><span class="font" style="color:#9053b9;">a1.channels.c1.capacity = 1000</span></span></p>
<p><span class="italic" style="font-style:italic;font-weight:normal;"><span class="font" style="color:#9053b9;">a1.channels.c1.transactionCapacity = 100</span></span></p>
<p><br></p>
<p><span class="italic" style="font-style:italic;font-weight:normal;"><span class="font" style="color:#9053b9;"># sink</span></span></p>
<p><span class="italic" style="font-style:italic;font-weight:normal;"><span class="font" style="color:#9053b9;">a1.sinks.k1.type = file_roll</span></span></p>
<p><span class="italic" style="font-style:italic;font-weight:normal;"><span class="font" style="color:#9053b9;">a1.sinks.k1.channel = c1</span></span></p>
<p><span class="italic" style="font-style:italic;font-weight:normal;"><span class="font" style="color:#9053b9;">#a1.sinks.k1.sink.rollInterval=0</span></span></p>
<p><span class="italic" style="font-style:italic;font-weight:normal;"><span class="font" style="color:#9053b9;">a1.sinks.k1.sink.directory = /opt/apps/tmp</span></span></p>
<p><br></p>
<p>bat脚本代码粘贴</p>
<p><span class="italic" style="font-style:italic;font-weight:normal;"><span class="font" style="color:#9053b9;">@echo off</span></span></p>
<p><span class="italic" style="font-style:italic;font-weight:normal;"><span class="font" style="color:#9053b9;">title=flume搭建</span></span></p>
<p><span class="italic" style="font-style:italic;font-weight:normal;"><span class="font" style="color:#9053b9;">mode con cols=80 lines=40</span></span></p>
<p><span class="italic" style="font-style:italic;font-weight:normal;"><span class="font" style="color:#9053b9;">COLOR 3f</span></span></p>
<p><span class="italic" style="font-style:italic;font-weight:normal;"><span class="font" style="color:#9053b9;">echo ------------------------------------------</span></span></p>
<p><span class="italic" style="font-style:italic;font-weight:normal;"><span class="font" style="color:#9053b9;">cd F:\flume\flume-code\apache-flume-1.6.0-bin\bin</span></span></p>
<p><span class="italic" style="font-style:italic;font-weight:normal;"><span class="font" style="color:#9053b9;">echo flume-ng.cmd  agent  -conf  F:\flume\flume-code\apache-flume-1.6.0-bin\conf  -conf-file  F:\flume\flume-code\apache-flume-1.6.0-bin\conf
 example.conf  -name  a1</span></span></p>
<p><span class="italic" style="font-style:italic;font-weight:normal;"><span class="font" style="color:#9053b9;">flume-ng.cmd agent --conf ..\conf --conf-file ..\conf\example.conf --name a1</span></span></p>
<p><span class="italic" style="font-style:italic;font-weight:normal;"><span class="font" style="color:#9053b9;">pause</span></span></p>
<p><br></p>
<p>接下来准备down源码了，我们需要开发</p>
            </div>
                </div>