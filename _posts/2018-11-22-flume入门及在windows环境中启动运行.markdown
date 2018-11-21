---
layout:     post
title:      flume入门及在windows环境中启动运行
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;">最近公司项目需求，要研究flume技术，近段时间研究了一下，给它来做个小小滴总结！</span></p>
<p><span style="font-size:18px;">一、什么是Flume?<br>
　　flume 作为 cloudera 开发的实时日志收集系统，受到了业界的认可与广泛应用。Flume 初始的发行版本目前被统称为 Flume OG（original generation），属于 cloudera。但随着 FLume 功能的扩展，Flume OG 代码工程臃肿、核心组件设计不合理、核心配置不标准等缺点暴露出来，尤其是在 Flume OG 的最后一个发行版本 0.94.0 中，日志传输不稳定的现象尤为严重，为了解决这些问题，2011 年 10 月 22 号，cloudera 完成了 Flume-728，对
 Flume 进行了里程碑式的改动：重构核心组件、核心配置以及代码架构，重构后的版本统称为 Flume NG（next generation）；改动的另一原因是将 Flume 纳入 apache 旗下，cloudera Flume 改名为 Apache Flume。<br></span></p>
<p><span style="font-size:18px;">二、flume的特点：<br>
　　flume是一个分布式、可靠、和高可用的海量日志采集、聚合和传输的系统。支持在日志系统中定制各类数据发送方，用于收集数据;同时，Flume提供对数据进行简单处理，并写到各种数据接受方(比如文本、HDFS、Hbase等)的能力 。flume的数据流由事件(Event)贯穿始终。事件是Flume的基本数据单位，它携带日志数据(字节数组形式)并且携带有头信息，这些Event由Agent外部的Source生成，当Source捕获事件后会进行特定的格式化，然后Source会把事件推入(单个或多个)Channel中。你可以把Channel看作是一个缓冲区，它将保存事件直到Sink处理完该事件。Sink负责持久化日志或者把事件推向另一个Source。<br></span></p>
<p><span style="font-size:18px;"><span></span>flume的可靠性 <br>
　　当节点出现故障时，日志能够被传送到其他节点上而不会丢失。Flume提供了三种级别的可靠性保障，从强到弱依次分别为：end-to-end（收到数据agent首先将event写到磁盘上，当数据传送成功后，再删除；如果数据发送失败，可以重新发送。），Store on failure（这也是scribe采用的策略，当数据接收方crash时，将数据写到本地，待恢复后，继续发送），Besteffort（数据发送到接收方后，不会进行确认）。<br>
 <br>
       flume的可恢复性：<br>
　　还是靠Channel。推荐使用FileChannel，事件持久化在本地文件系统里(性能较差)。 <br>
 </span></p>
<p><span style="font-size:18px;">三、flume的一些核心概念：<br>
Agent使用JVM 运行Flume。每台机器运行一个agent，但是可以在一个agent中包含多个sources和sinks。<br>
Client生产数据，运行在一个独立的线程。Source从Client收集数据，传递给Channel。Sink从Channel收集数据，运行在一个独立线程。Channel连接 sources 和 sinks ，这个有点像一个队列。Events可以是日志记录、 avro 对象等。<br></span></p>
<p><span style="font-size:18px;"></span></p>
<p><span style="font-size:18px;">　Flume以agent为最小的独立运行单位。一个agent就是一个JVM。单agent由Source、Sink和Channel三大组件构成，如下图：</span></p>
<p><span style="font-size:18px;"><img alt="" src="http://files.jb51.net/file_images/article/201408/201408111056044.png"></span></p>
<p><span style="font-size:18px;">　　值得注意的是，Flume提供了大量内置的Source、Channel和Sink类型。不同类型的Source,Channel和Sink可以自由组合。组合方式基于用户设置的配置文件，非常灵活。比如：Channel可以把事件暂存在内存里，也可以持久化到本地硬盘上。Sink可以把日志写入HDFS, HBase，甚至是另外一个Source等等。Flume支持用户建立多级流，也就是说，多个agent可以协同工作，并且支持Fan-in、Fan-out、Contextual
 Routing、Backup Routes，这也正是NB之处。如下图所示:</span></p>
<p></p>
<span style="font-size:18px;"><img alt="" src="http://files.jb51.net/file_images/article/201408/201408111056045.png"><br></span>
<p></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">四、windows环境搭建</span></p>
<p><span style="font-size:18px;">1、准备flume的安装包，请在官网进行下载：<a href="http://flume.apache.org/download.html" rel="nofollow">http://flume.apache.org/download.html</a></span></p>
<p><span style="font-size:18px;">2、解压下载后的文件，进入conf目录，复制flume-conf.properties文件，并修改文件名称为：example.conf，编写内容如下：</span></p>
<p><span style="font-size:18px;"></span></p>
<pre><code class="language-html"><span style="font-size:18px;"># 指定Agent的组件名称
a1.sources = r1
a1.sinks = k1
a1.channels = c1

# 指定Flume source(要监听的路径)
a1.sources.r1.type = netcat
a1.sources.r1.bind = localhost
a1.sources.r1.port = 44444

# 指定Flume sink
a1.sinks.k1.type = logger

# 指定Flume channel
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# 绑定source和sink到channel上
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1</span></code></pre><span style="font-size:18px;"><br>
3、使用cmd命令进入flume的bin目录，在命令行输入如下命令启动flume：</span>
<p></p>
<p><span style="font-size:18px;"></span></p>
<div><span style="font-size:18px;"> flume-ng.cmd agent -conf conf -conf-file ../conf/example.conf -name a1 -Dflume.root.logger=INFO,console</span></div>
<span style="font-size:18px;"><img src="" alt=""><br></span>
<p></p>
<p><span style="font-size:18px;">发现启动后报错，提示-Dflume.root.logger=INFO,console该命令找不到，而在linux或mac上面就可以了，这个命令是所上面的sink输出设置：a1.sinks.k1.type=logger,表示我们在控制台输出，但是启动不了，所以我们不能使用上面的配置进行测试了，因为看不到输出的效果。</span></p>
<p><span style="font-size:18px;">我们来换一种配置吧，修改source的源是监听一个目录下面的文件，如果有文件，就把文件追加到另一个文件夹中，配置如下，文件名称为：mytest.conf</span></p>
<p><span style="font-size:18px;"></span></p>
<pre><code class="language-html"><span style="font-size:18px;"># 指定Agent的组件名称
a1.sources = r1
a1.sinks = k1
a1.channels = c1

# 指定Flume source(要监听的路径)
a1.sources.r1.type = spooldir
a1.sources.r1.spoolDir = D://flume-test//source

# 指定Flume sink
a1.sinks.k1.type = file_roll
a1.sinks.k1.sink.directory = D://flume-test//sink

# 指定Flume channel
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# 绑定source和sink到channel上
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1</span></code></pre><span style="font-size:18px;"><br>
相对于上面那个example.cof只是修改了source和sink的方式，注意source的spoolDir和sink的directory的路径经测试需要//，也就是上面那样写才能生效。当然要做测试的话，先在D盘目录下把下面的文件夹建立好，下面同样使用cmd命令进入到flume的bin目录，然后输入命令：</span>
<p></p>
<p><span style="font-size:18px;"><img src="" alt=""><img src="" alt="" style="font-size:18px;"><img src="https://img-blog.csdn.net/20170630085857346?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSGFyZGVyWGlu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="font-size:18px;">输出下面的信息，就表示flume启动成功，下面我们来进行测试，编写一个test.txt文件，放入D盘flume-test的source目录下，会发现我们的flume起作用了，该文件立刻被flume执行，然后变成test.txt.COMPLETED文件，表示已处理，接下来我们看看sink目录下，会发现我们放入source的文件已经被flume处理后到sink目录了：</span></p>
<p><span style="font-size:18px;"><img src="" alt=""><img src="" alt="" style="font-size:18px;"><img src="https://img-blog.csdn.net/20170630085909555?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSGFyZGVyWGlu/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="font-size:18px;">经过测试发现，我们的test.txt文件中不能包含中文或者中文的标点符号之类的，否则该文件处理不了，并在source目录的.flumespool中会出现一个.meta文件，应该是临时的备份文件，打开显示乱码，应该是还有什么配置没有设置，还在研究之中。</span></p>
<p><span style="font-size:18px;">到此，一个简单的flume环境总算是搭建起来了，总结一下，flume的主要作用是应该就是从各个数据源将数据发送到source，source经过channel，channel到sink，sink将数据发送到其他数据源，其中这个里面的数据源包括文件、内存、kafka消息队列、hdfs数据库、mysql....flume能够实时监听数据源数据的变化，定义的规则是编写我们的conf配置文件，只要把规则定义好了，然后重启flume，就能够实现我们的功能了！</span></p>
<p><span style="font-size:18px;">附录:flume官网API,里面包含了我们所有的数据源属性的定义和介绍：<a href="http://flume.apache.org/FlumeUserGuide.html" rel="nofollow">http://flume.apache.org/FlumeUserGuide.html</a></span></p>
            </div>
                </div>