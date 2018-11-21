---
layout:     post
title:      Flume的体系结构介绍以及Flume入门案例(往HDFS上传数据)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<br><br><h1 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
# Flume的体系结构</h1>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
</p>
<p><span style="color:rgb(51,51,51);background:rgb(242,242,242);">对<span style="font-family:Arial;">java</span><span style="font-family:'宋体';">有兴趣的朋友可以加上面说的</span><span style="font-family:Arial;">553175249</span><span style="font-family:'宋体';">这个群哦，一起学习，共同进步
</span><span style="font-family:Arial;">.</span></span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
</p>
<h2 style="color:rgb(51,51,51);"><a name="t2" style="color:rgb(51,102,153);"></a># Flume介绍</h2>
<p></p>
<p style="color:rgb(51,51,51);">Flume是Cloudera提供的一个<span style="color:#FF0000;">高可用的</span>，<span style="color:#FF0000;">高可靠的</span>，<span style="color:#FF0000;">分布式的</span>海量日志采集、聚合和传输的系统，Flume支持在日志系统中定制各类数据发送方，用于收集数据；同时，Flume提供对数据进行简单处理，并写到各种数据接受方（可定制）的能力。</p>
<h3 style="color:rgb(51,51,51);"><a name="t3" style="color:rgb(51,102,153);"></a># 系统功能</h3>
<h4 style="color:rgb(51,51,51);"><a name="t4" style="color:rgb(51,102,153);"></a># 日志收集</h4>
<p style="color:rgb(51,51,51);">Flume最早是Cloudera提供的日志收集系统，目前是Apache下的一个孵化项目，Flume支持在日志系统中定制各类数据发送方，用于收集数据。</p>
<h4 style="color:rgb(51,51,51);"><a name="t5" style="color:rgb(51,102,153);"></a># 数据处理</h4>
<p style="color:rgb(51,51,51);">Flume提供对数据进行简单处理，并写到各种数据接受方（可定制）的能力Flume提供了从console（控制台）、RPC（Thrift-RPC）、text（文件）、tail（UNIX tail）、syslog（syslog日志系统，支持TCP和UDP等2种模式），exec（命令执行）等数据源上收集数据的能力。</p>
<h4 style="color:rgb(51,51,51);"><a name="t6" style="color:rgb(51,102,153);"></a># 工作方式 <span style="color:rgb(255,0,0);">（Flume-NG旧版本的概念，新版本已经丢弃）</span></h4>
<p style="color:rgb(51,51,51);">Flume采用了多Master的方式。为了保证配置数据的一致性，Flume引入了ZooKeeper，用于保存配置数据，ZooKeeper本身可保证配置数据的一致性和高可用，另外，在配置数据发生变化时，ZooKeeper可以通知FlumeMaster节点。Flume Master间使用gossip协议同步数据。</p>
<h2 style="color:rgb(51,51,51);"><a name="t7" style="color:rgb(51,102,153);"></a># Flume的设计目标（百度百科）</h2>
<h3 style="color:rgb(51,51,51);"><a name="t8" style="color:rgb(51,102,153);"></a># 可靠性 </h3>
<p style="color:rgb(51,51,51);">当节点出现故障时，日志能够被传送到其他节点上而不会丢失。Flume提供了三种级别的可靠性保障，从强到弱依次分别为：end-to-end（收到数据agent首先将event写到磁盘上，当数据传送成功后，再删除；如果数据发送失败，可以重新发送。），Store on failure（这也是scribe采用的策略，当数据接收方crash时，将数据写到本地，待恢复后，继续发送），Besteffort（数据发送到接收方后，不会进行确认）。</p>
<h3 style="color:rgb(51,51,51);"><a name="t9" style="color:rgb(51,102,153);"></a># 可扩展性<span style="color:rgb(255,0,0);">（Flume-NG旧版本的概念，新版本已经丢弃）</span></h3>
<p style="color:rgb(51,51,51);">Flume采用了三层<a href="http://lib.csdn.net/base/architecture" rel="nofollow" title="大型网站架构知识库" style="color:rgb(223,52,52);">架构</a>，分别为agent，collector和storage，每一层均可以水平扩展。其中，所有agent和collector由master统一管理，这使得系统容易监控和维护，且master允许有多个（使用ZooKeeper进行管理和负载均衡），这就避免了单点故障问题。</p>
<h3 style="color:rgb(51,51,51);"><a name="t10" style="color:rgb(51,102,153);"></a># 可管理性<span style="color:rgb(255,0,0);">（Flume-NG旧版本的概念，新版本已经丢弃）</span></h3>
<p style="color:rgb(51,51,51);">所有agent和colletor由master统一管理，这使得系统便于维护。多master情况，Flume利用ZooKeeper和gossip，保证动态配置数据的一致性。用户可以在master上查看各个数据源或者数据流执行情况，且可以对各个数据源配置和动态加载。Flume提供了web和shell script command两种形式对数据流进行管理。</p>
<h3 style="color:rgb(51,51,51);"><a name="t11" style="color:rgb(51,102,153);"></a># 功能可扩展性</h3>
<p style="color:rgb(51,51,51);">用户可以根据需要添加自己的agent，collector或者storage。此外，Flume自带了很多组件，包括各种agent（file，syslog等），collector和storage（file，HDFS等）。（这里看下面的Flume架构图你就明白了）</p>
<h2 style="color:rgb(51,51,51);"><a name="t12" style="color:rgb(51,102,153);"></a># Flume架构</h2>
<h4 style="color:rgb(51,51,51);"><a name="t13" style="color:rgb(51,102,153);"></a># Flume基础架构，如下图：</h4>
<p style="color:rgb(51,51,51);">  <img src="https://img-blog.csdn.net/20140625114928093?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjE4NTI5Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p style="color:rgb(51,51,51);">这是一个flume-ng 最简单的图。flume-ng 是由一个个agent组成的。一个agent就像一个细胞一样。</p>
<h4 style="color:rgb(51,51,51);"><a name="t14" style="color:rgb(51,102,153);"></a># Flume的多agent架构，如下图：</h4>
<p style="color:rgb(51,51,51);">    <img src="https://img-blog.csdn.net/20140625114937015?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjE4NTI5Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p style="color:rgb(51,51,51);">   上面是两个agent链接在一起的，再看看更多的......</p>
<h4 style="color:rgb(51,51,51);"><a name="t15" style="color:rgb(51,102,153);"></a># Flume的合并（合作）架构，如下图：</h4>
<div style="color:rgb(51,51,51);"><img src="https://img-blog.csdn.net/20140625114948406?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjE4NTI5Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<p style="color:rgb(51,51,51);">你是不是觉得这种设计是不是吊炸天了，可以随意组合，跟搭积木一样。跟Storm的设计思想是差不多的，何止吊炸天啊，简直就是吊炸天 、、、</p>
<h4 style="color:rgb(51,51,51);"><a name="t16" style="color:rgb(51,102,153);"></a># Flume的多路复用架构，如下图：</h4>
<p style="color:rgb(51,51,51);">      <img src="https://img-blog.csdn.net/20140625115000328?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjE4NTI5Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<h4 style="color:rgb(51,51,51);"><a name="t17" style="color:rgb(51,102,153);"></a># agent的构造</h4>
<p style="color:rgb(51,51,51);">每个agent里都有三部分构成：source、channel和sink。</p>
<p style="color:rgb(51,51,51);">就相当于source接收数据，通过channel传输数据，sink把数据写到下一端。这就完了，就这么简单。其中source有很多种可以选择，channel有很多种可以选择，sink也同样有多种可以选择，并且都支持自定义。饿靠！太灵活了。想怎么玩就怎么玩，这你妹的！</p>
<p style="color:rgb(51,51,51);">同时，如上上图所示，agent还支持选择器，就是一个source支持多个channel和多个sink，这样就完成了数据的分发。</p>
<p style="color:rgb(51,51,51);">这就完了，flume-ng就这么简单........</p>
<p style="color:rgb(51,51,51);">从看到最后用，一天足可以搞定。剩下的就是怎么组织你的agent的问题了。也就是搭积木的过程......</p>
<p style="color:rgb(51,51,51);">另外有一点需要强调的是，flume-ng提供了一种特殊的启动方式（不同于agent），那就是client启动。cilent是一个特殊的agent, 他的source是文件，channel是内存，sink是arvo。实际上是为了方便大家用，直接来传递文件的。具体可以看看官方使用手册。</p>
<p style="color:rgb(51,51,51);">估计到这儿，应该对flume-ng有了解了吧 、、、on my god、、、</p>
<br style="color:rgb(51,51,51);"><h2 style="color:rgb(51,51,51);"><a name="t18" style="color:rgb(51,102,153);"></a># Flume的安装</h2>
<h3 style="color:rgb(51,51,51);"><a name="t19" style="color:rgb(51,102,153);"></a># 下载 flume（使用wget下载）</h3>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">[root@rs229 flume]# wget -c -P /root http://mirrors.cnnic.cn/apache/flume/1.5.0/apache-flume-1.5.0-bin.tar.gz</span></p>
<p style="color:rgb(51,51,51);"># 安装</p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">root@rs229 flume]# pwd</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">/usr/local/adsit/yting/apache/flume</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">[root@rs229 flume]# ll</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">total 4</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">drwxr-xr-x 3 root root 4096 Jun 24 17:25mirrors.cnnic.cn</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">[root@rs229 flume]# cpmirrors.cnnic.cn/apache/flume/1.5.0/apache-flume-1.5.0-bin.tar.gz .</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">[root@rs229 flume]# ll</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">total 25276</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">-rw-r--r-- 1 root root 25876246 Jun 24 17:27apache-flume-1.5.0-bin.tar.gz</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">drwxr-xr-x 3 root root     4096 Jun 24 17:25 mirrors.cnnic.cn</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">[root@rs229 flume]# tar -zxvfapache-flume-1.5.0-bin.tar.gz</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);"> </span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">[root@rs229 flume]# ll</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">total 25280</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">drwxr-xr-x 7 root root     4096 Jun 24 17:27 apache-flume-1.5.0-bin</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">-rw-r--r-- 1 root root 25876246 Jun 24 17:27apache-flume-1.5.0-bin.tar.gz</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">drwxr-xr-x 3 root root     4096 Jun 24 17:25 mirrors.cnnic.cn</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">[root@rs229 flume]# rm -rfapache-flume-1.5.0-bin.tar.gz</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">[root@rs229 flume]# rm -rf mirrors.cnnic.cn/</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">[root@rs229 flume]# ll</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">total 4</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">drwxr-xr-x 7 root root 4096 Jun 24 17:27apache-flume-1.5.0-bin</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">[root@rs229 flume]#</span></p>
<h3 style="color:rgb(51,51,51);"><a name="t20" style="color:rgb(51,102,153);"></a># 修改 flume-env.sh 配置文件</h3>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">[root@rs229 conf]# pwd</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">/usr/local/adsit/yting/apache/flume/apache-flume-1.5.0-bin/conf</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">[root@rs229 conf]# ll</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">total 12</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">-rw-r--r-- 1 501 games 1661 Mar 29 06:15flume-conf.properties.template</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">-rw-r--r-- 1 501 games 1197 Mar 29 06:15 flume-env.sh.template</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">-rw-r--r-- 1 501 games 3063 Mar 29 06:15log4j.properties</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">[root@rs229 conf]# cp flume-env.sh.templateflume-env.sh</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">[root@rs229 conf]# vi flume-env.sh</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);"> </span></p>
<p style="color:rgb(51,51,51);"># Enviroment variables can be sethere.</p>
<p style="color:rgb(51,51,51);">JAVA_HOME=/usr/local/adsit/yting/jdk/jdk1.7.0_60</p>
<h3 style="color:rgb(51,51,51);"><a name="t21" style="color:rgb(51,102,153);"></a># 修改 flume-site.xml 配置文件（貌似没有该步骤，貌似也可以修改，研究后再来弄吧！）</h3>
<h3 style="color:rgb(51,51,51);"><a name="t22" style="color:rgb(51,102,153);"></a># 验证 flume是否安装成功</h3>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">[root@rs229 conf]# ../bin/flume-ng version</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">Flume 1.5.0</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">Source code repository:https://<a href="http://lib.csdn.net/base/git" rel="nofollow" title="Git知识库" style="color:rgb(223,52,52);">Git</a>-wip-us.apache.org/repos/asf/flume.git</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">Revision: 8633220df808c4cd0c13d1cf0320454a94f1ea97</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">Compiled by hshreedharan on Wed May  7 14:49:18 PDT 2014</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">From source with checksuma01fe726e4380ba0c9f7a7d222db961f</span></p>
<p style="color:rgb(51,51,51);"><span style="color:#FF0000;">出现这样的信息表示安装成功了</span></p>
<h2 style="color:rgb(51,51,51);"><a name="t23" style="color:rgb(51,102,153);"></a># Flume 入门案例</h2>
<h3 style="color:rgb(51,51,51);"><a name="t24" style="color:rgb(51,102,153);"></a># Flume监控指定目录下的日志信息，并将日志信息上传到HDFS中去</h3>
<h3 style="color:rgb(51,51,51);"><a name="t25" style="color:rgb(51,102,153);"></a># 在conf目录下新建example.conf配置文件</h3>
<p style="color:rgb(51,51,51);"><span style="color:#FF0000;">新建文件：</span>在conf目录下新建一个example.conf文件（随便起什么名字），当然随便哪里都行</p>
<p style="color:rgb(51,51,51);"><span style="color:#FF0000;">注意：</span>文件名最好跟配置中的名字一样，比如里面的agent1最好跟外面的文件名一样，<span style="color:#FF0000;">见名知意</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">[root@rs229 conf]# pwd</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">/usr/local/adsit/yting/apache/flume/apache-flume-1.5.0-bin/conf</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">[root@rs229 apache-flume-1.5.0-bin]# catconf/example.conf</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);"># agent1 : yting first flume example</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">agent1.sources=source1</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">agent1.sinks=sink1</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">agent1.channels=channel1</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);"> </span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);"># configure source1</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">agent1.sources.source1.type=spooldir</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">agent1.sources.source1.spoolDir=<span style="color:#FF0000;">/usr/local/yting/flume/tdata/tdir1</span></span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">agent1.sources.source1.channels=channel1</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">agent1.sources.source1.fileHeader = false</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);"> </span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);"># configure sink1</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">agent1.sinks.sink1.type=hdfs</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">agent1.sinks.sink1.hdfs.path=<span style="color:#FF0000;">hdfs://rs229:9000/yting/flumet</span></span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">agent1.sinks.sink1.hdfs.fileType=DataStream</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">agent1.sinks.sink1.hdfs.writeFormat=TEXT</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">agent1.sinks.sink1.hdfs.rollInterval=4</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">agent1.sinks.sink1.channel=channel1</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);"> </span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);"># configure channel1</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">agent1.channels.channel1.type=file</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">agent1.channels.channel1.checkpointDir=<span style="color:#FF0000;">/usr/local/yting/flume/checkpointdir/tcpdir/example_agent1_001</span></span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">agent1.channels.channel1.dataDirs=<span style="color:#FF0000;">/usr/local/yting/flume/datadirs/tddirs/example_agent1_001</span></span></p>
<p style="color:rgb(51,51,51);"><span style="color:#FF0000;">注意：</span>红色字体部分自己修改成自己对应的目录了</p>
<h3 style="color:rgb(51,51,51);"><a name="t26" style="color:rgb(51,102,153);"></a># 运行Flume 使用example.conf</h3>
<h4 style="color:rgb(51,51,51);"><a name="t27" style="color:rgb(51,102,153);"></a>#命令参数说明</h4>
<p style="color:rgb(51,51,51);"><span style="color:#FF0000;">-c </span>conf 指定配置目录为conf</p>
<p style="color:rgb(51,51,51);"><span style="color:#FF0000;">-f</span> conf/example.conf 指定配置文件为conf/example.conf</p>
<p style="color:rgb(51,51,51);"><span style="color:#FF0000;">-n</span> agent1 指定agent名字为agent1,需要与example.conf中的一致（这里不一致，可能会一直停在那里，请参考笔记中后面的错误全集Flume部分，那里介绍了错误的分析，原因，解决）</p>
<p style="color:rgb(51,51,51);"><span style="color:#FF0000;">-Dflume.root.logger=INFO,console</span> 指定DEBUF模式在console输出INFO信息</p>
<p style="color:rgb(51,51,51);"><br></p>
<p style="color:rgb(51,51,51);">[root@rs229conf]# ./bin/flume-ng agent -c conf/ -f conf/example.conf<span style="color:#FF0000;">-n agent1</span>-Dflume.root.logger=INFO,console</p>
<p style="color:rgb(51,51,51);">-bash:./bin/flume-ng: No such file or directory</p>
<p style="color:rgb(51,51,51);">[root@rs229conf]# cd ..</p>
<p style="color:rgb(51,51,51);">[root@rs229apache-flume-1.5.0-bin]# ./bin/flume-ng agent -c conf/ -f conf/example.conf -nagent1 -Dflume.root.logger=INFO,console</p>
<p style="color:rgb(51,51,51);">Info: Sourcingenvironment configuration script/usr/local/adsit/yting/apache/flume/apache-flume-1.5.0-bin/conf/flume-env.sh</p>
<p style="color:rgb(51,51,51);">Info: IncludingHadoop libraries found via(/usr/local/adsit/yting/apache/<a href="http://lib.csdn.net/base/hadoop" rel="nofollow" title="Hadoop知识库" style="color:rgb(223,52,52);">Hadoop</a>/hadoop-2.2.0/bin/hadoop)
 for HDFS access</p>
<p style="color:rgb(51,51,51);">Info: Excluding/usr/local/adsit/yting/apache/hadoop/hadoop-2.2.0/share/hadoop/common/lib/slf4j-api-1.7.5.jarfrom classpath</p>
<p style="color:rgb(51,51,51);">Info: Excluding/usr/local/adsit/yting/apache/hadoop/hadoop-2.2.0/share/hadoop/common/lib/slf4j-log4j12-1.7.5.jarfrom classpath</p>
<p style="color:rgb(51,51,51);">Info: IncludingHBASE libraries found via(/usr/local/adsit/yting/apache/hbase/hbase-0.96.2-hadoop2/bin/hbase) for HBASEaccess</p>
<p style="color:rgb(51,51,51);">Info: Excluding/usr/local/adsit/yting/apache/hbase/hbase-0.96.2-hadoop2/bin/../lib/slf4j-api-1.6.4.jarfrom classpath</p>
<p style="color:rgb(51,51,51);">Info: Excluding/usr/local/adsit/yting/apache/hbase/hbase-0.96.2-hadoop2/bin/../lib/slf4j-log4j12-1.6.4.jarfrom classpath</p>
<p style="color:rgb(51,51,51);">Info: Excluding/usr/local/adsit/yting/apache/hadoop/hadoop-2.2.0/share/hadoop/common/lib/slf4j-api-1.7.5.jarfrom classpath</p>
<p style="color:rgb(51,51,51);">Info: Excluding/usr/local/adsit/yting/apache/hadoop/hadoop-2.2.0/share/hadoop/common/lib/slf4j-log4j12-1.7.5.jarfrom classpath</p>
<p style="color:rgb(51,51,51);">….capacity-scheduler/*.jar:/conf'-Djava.library.path=:/usr/local/adsit/yting/apache/hadoop/hadoop-2.2.0/lib:/usr/local/adsit/yting/apache/hadoop/hadoop-2.2.0//liborg.apache.flume.node.Application -f conf/example.conf -n agent1</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:45,763 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.node.PollingPropertiesFileConfigurationProvider.start(PollingPropertiesFileConfigurationProvider.<a href="http://lib.csdn.net/base/javaee" rel="nofollow" title="Java EE知识库" style="color:rgb(223,52,52);">Java</a>:61)]Configuration
 provider starting</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:45,772 (conf-file-poller-0) [INFO -org.apache.flume.node.PollingPropertiesFileConfigurationProvider$FileWatcherRunnable.run(PollingPropertiesFileConfigurationProvider.java:133)]Reloading configuration file:conf/example.conf</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:45,781 (conf-file-poller-0) [INFO -org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:1016)]Processing:sink1</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:45,783 (conf-file-poller-0) [INFO -org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:1016)]Processing:sink1</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:45,783 (conf-file-poller-0) [INFO -org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:930)]Added sinks: sink1 Agent: agent1</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:45,783 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:1016)]Processing:sink1</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:45,783 (conf-file-poller-0) [INFO -org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:1016)]Processing:sink1</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:45,783 (conf-file-poller-0) [INFO -org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:1016)]Processing:sink1</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:45,784 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:1016)]Processing:sink1</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:45,809 (conf-file-poller-0) [INFO -org.apache.flume.conf.FlumeConfiguration.validateConfiguration(FlumeConfiguration.java:140)]Post-validation flume configuration contains configuration for agents: [agent1]</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:45,809 (conf-file-poller-0) [INFO -org.apache.flume.node.AbstractConfigurationProvider.loadChannels(AbstractConfigurationProvider.java:150)]Creating channels</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:45,823 (conf-file-poller-0) [INFO -org.apache.flume.channel.DefaultChannelFactory.create(DefaultChannelFactory.java:40)]Creating instance of channel channel1 type file</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:45,828 (conf-file-poller-0) [INFO - org.apache.flume.node.AbstractConfigurationProvider.loadChannels(AbstractConfigurationProvider.java:205)]Created channel channel1</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:45,829 (conf-file-poller-0) [INFO -org.apache.flume.source.DefaultSourceFactory.create(DefaultSourceFactory.java:39)]Creating instance of source source1, type spooldir</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:45,844 (conf-file-poller-0) [INFO -org.apache.flume.sink.DefaultSinkFactory.create(DefaultSinkFactory.java:40)]Creating instance of sink: sink1, type: hdfs</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:46,293 (conf-file-poller-0) [WARN -org.apache.hadoop.util.NativeCodeLoader.&lt;clinit&gt;(NativeCodeLoader.java:62)]Unable to load native-hadoop library for your platform... using builtin-javaclasses where applicable</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:46,572 (conf-file-poller-0) [INFO - org.apache.flume.sink.hdfs.HDFSEventSink.authenticate(HDFSEventSink.java:555)]Hadoop Security enabled: false</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:46,576 (conf-file-poller-0) [INFO -org.apache.flume.node.AbstractConfigurationProvider.getConfiguration(AbstractConfigurationProvider.java:119)]Channel channel1 connected to [source1, sink1]</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:46,587 (conf-file-poller-0) [INFO -org.apache.flume.node.Application.startAllComponents(Application.java:138)]Starting new configuration:{ sourceRunners:{source1=EventDrivenSourceRunner: {source:Spool Directory
 source source1: { spoolDir:/usr/local/yting/flume/tdata/tdir1 } }} sinkRunners:{sink1=SinkRunner: {policy:org.apache.flume.sink.DefaultSinkProcessor@7205c140 counterGroup:{name:null counters:{} } }} channels:{channel1=FileChannel channel1 { dataDirs:[/usr/local/yting/flume/datadirs/tddirs/example_agent1_001]
 }} }</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:46,593 (conf-file-poller-0) [INFO -org.apache.flume.node.Application.startAllComponents(Application.java:145)]Starting Channel channel1</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:46,593 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.FileChannel.start(FileChannel.java:259)] StartingFileChannel channel1 { dataDirs:[/usr/local/yting/flume/datadirs/tddirs/example_agent1_001]
 }...</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:46,617 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.Log.&lt;init&gt;(Log.java:328)] Encryption is notenabled</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:46,618 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.Log.replay(Log.java:373)] Replay started</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:46,620 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.Log.replay(Log.java:385)] Found NextFileID 0,from []</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:46,661 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.EventQueueBackingStoreFile.&lt;init&gt;(EventQueueBackingStoreFile.java:91)]Preallocated/usr/local/yting/flume/checkpointdir/tcpdir/example_agent1_001/checkpoint
 to8008232 for capacity 1000000</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:46,663 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.EventQueueBackingStoreFileV3.&lt;init&gt;(EventQueueBackingStoreFileV3.java:53)]Starting up with/usr/local/yting/flume/checkpointdir/tcpdir/example_agent1_001/checkpoint
 and/usr/local/yting/flume/checkpointdir/tcpdir/example_agent1_001/checkpoint.meta</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:47,095 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.FlumeEventQueue.&lt;init&gt;(FlumeEventQueue.java:114)]QueueSet population inserting 0 took 0</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:47,100 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.Log.replay(Log.java:423)] Last Checkpoint Wed Jun25 10:37:46 CST 2014, queue depth = 0</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:47,105 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.Log.doReplay(Log.java:507)] Replaying logs withv2 replay logic</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:47,109 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.ReplayHandler.replayLog(ReplayHandler.java:249)]Starting replay of []</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:47,109 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.ReplayHandler.replayLog(ReplayHandler.java:346)]read: 0, put: 0, take: 0, rollback: 0, commit: 0, skip: 0, eventCount:0</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:47,110 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.FlumeEventQueue.replayComplete(FlumeEventQueue.java:407)]Search Count = 0, Search Time = 0, Copy Count = 0, Copy Time = 0</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:47,119 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.Log.replay(Log.java:470)] Rolling/usr/local/yting/flume/datadirs/tddirs/example_agent1_001</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:47,120 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.Log.roll(Log.java:932)] Roll start/usr/local/yting/flume/datadirs/tddirs/example_agent1_001</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:47,137 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.tools.DirectMemoryUtils.getDefaultDirectMemorySize(DirectMemoryUtils.java:113)]Unable to get maxDirectMemory from VM: NoSuchMethodException:sun.misc.VM.maxDirectMemory(null)</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:47,140 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.tools.DirectMemoryUtils.allocate(DirectMemoryUtils.java:47)]Direct Memory Allocation:  Allocation =1048576, Allocated = 0, MaxDirectMemorySize = 18874368,
 Remaining = 18874368</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:47,195 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.channel.file.LogFile$Writer.&lt;init&gt;(LogFile.java:214)]Opened /usr/local/yting/flume/datadirs/tddirs/example_agent1_001/log-1</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:47,208 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.Log.roll(Log.java:948)] Roll end</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:47,208 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.EventQueueBackingStoreFile.beginCheckpoint(EventQueueBackingStoreFile.java:214)]Start checkpoint for/usr/local/yting/flume/checkpointdir/tcpdir/example_agent1_001/checkpoint,elements
 to sync = 0</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:47,211 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.EventQueueBackingStoreFile.checkpoint(EventQueueBackingStoreFile.java:239)]Updating checkpoint metadata: logWriteOrderID: 1403663867120, queueSize:
 0,queueHead: 0</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:47,235 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.Log.writeCheckpoint(Log.java:1005)] Updatedcheckpoint for file:/usr/local/yting/flume/datadirs/tddirs/example_agent1_001/log-1 position: 0logWriteOrderID:
 1403663867120</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:47,235 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.FileChannel.start(FileChannel.java:285)] QueueSize after replay: 0 [channel=channel1]</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:47,296 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.register(MonitoredCounterGroup.java:119)]Monitored counter group for type: CHANNEL, name: channel1: Successfullyregistered
 new MBean.</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:47,296 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.start(MonitoredCounterGroup.java:95)]Component type: CHANNEL, name: channel1 started</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:47,297 (conf-file-poller-0) [INFO -org.apache.flume.node.Application.startAllComponents(Application.java:173)] StartingSink sink1</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:47,297 (conf-file-poller-0) [INFO -org.apache.flume.node.Application.startAllComponents(Application.java:184)]Starting Source source1</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:47,298 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.source.SpoolDirectorySource.start(SpoolDirectorySource.java:77)]SpoolDirectorySource source starting with directory:/usr/local/yting/flume/tdata/tdir1</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:47,300 (lifecycleSupervisor-1-1) [INFO -org.apache.flume.instrumentation.MonitoredCounterGroup.register(MonitoredCounterGroup.java:119)]Monitored counter group for type: SINK, name: sink1: Successfully registerednew
 MBean.</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:47,300 (lifecycleSupervisor-1-1) [INFO -org.apache.flume.instrumentation.MonitoredCounterGroup.start(MonitoredCounterGroup.java:95)]Component type: SINK, name: sink1 started</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:47,330 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.instrumentation.MonitoredCounterGroup.register(MonitoredCounterGroup.java:119)]Monitored counter group for type: SOURCE, name: source1: Successfullyregistered
 new MBean.</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:47,330 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.instrumentation.MonitoredCounterGroup.start(MonitoredCounterGroup.java:95)]Component type: SOURCE, name: source1 started</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:47,331 (pool-6-thread-1) [INFO -org.apache.flume.source.SpoolDirectorySource$SpoolDirectoryRunnable.run(SpoolDirectorySource.java:254)]Spooling Directory Source runner has shutdown.</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:47,831 (pool-6-thread-1) [INFO - org.apache.flume.source.SpoolDirectorySource$SpoolDirectoryRunnable.run(SpoolDirectorySource.java:254)]Spooling Directory Source runner has shutdown.</p>
<p style="color:rgb(51,51,51);">2014-06-2510:37:48,332 (pool-6-thread-1) [INFO -org.apache.flume.source.SpoolDirectorySource$SpoolDirectoryRunnable.run(SpoolDirectorySource.java:254)]Spooling Directory Source runner has shutdown.</p>
<p style="color:rgb(51,51,51);"><span style="color:#FF0000;">到了这里说明你的程序运行正常了，但是你的监视目录</span><span style="color:#FF0000;"> /usr/local/yting/flume/tdata/tdir1</span><span style="color:#FF0000;">下没有新文件的产生，所以会一直出现上面的那条信息</span></p>
<h3 style="color:rgb(51,51,51);"><a name="t28" style="color:rgb(51,102,153);"></a># 在/usr/local/yting/flume/tdata/tdir1这个flume监视目录下添加一个新文件<span style="color:#FF0000;">yting_flume_example_agent1_00001.log</span></h3>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">[root@rs229 hadoop-2.2.0]# ./bin/hadoop fs -ls /yting</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">14/06/25 10:48:00 WARN util.NativeCodeLoader: Unableto load native-hadoop library for your platform... using builtin-java classeswhere applicable</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">Found 1 items</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">-rw-r--r--   3root supergroup       4278 2014-06-1018:29 /yting/yarn-daemon.sh</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">[root@rs229 tdir1]# ll</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">total 0</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">[root@rs229 tdir1]# ll -a</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">total 12</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">drwxr-xr-x 3 root root 4096 Jun 25 10:37 .</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">drwxr-xr-x 3 root root 4096 Jun 24 22:25 ..</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">drwxr-xr-x 2 root root 4096 Jun 25 09:48 .flumespool</span><span style="background:rgb(217,217,217);">（隐藏文件）</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">[root@rs229 tdir1]# viyting_flume_example_agent1_00001.log</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">The you smile until forever .....................</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">[root@rs229 tdir1]# ll</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">total 4</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">-rw-r--r-- 1 root root 50 Jun 25 10:51 <a name="OLE_LINK122" style="color:rgb(51,102,153);"></a><a name="OLE_LINK121" style="color:rgb(51,102,153);">yting_flume_example_agent1_00001.log.COMPLETED</a></span></p>
<h3 style="color:rgb(51,51,51);"><a name="t29" style="color:rgb(51,102,153);"></a># 文件名变成.COMPLETED结尾</h3>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">说明该文件</span><span style="background:rgb(217,217,217);">yting_flume_example_agent1_00001.log</span><span style="background:rgb(217,217,217);">已经被</span><span style="background:rgb(217,217,217);">flume</span><span style="background:rgb(217,217,217);">处理了，处理过后的文件名变成</span><span style="background:rgb(217,217,217);">yting_flume_example_agent1_00001.log.COMPLETED,</span><span style="background:rgb(217,217,217);">接下来看看</span><span style="background:rgb(217,217,217);">flume</span><span style="background:rgb(217,217,217);">那边的信息，应该发生变化了</span></p>
<h3 style="color:rgb(51,51,51);"><a name="t30" style="color:rgb(51,102,153);"></a># 查看flume shell的信息变化</h3>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">2014-06-25 10:51:00,530 (pool-6-thread-1) [INFO -org.apache.flume.source.SpoolDirectorySource$SpoolDirectoryRunnable.run(SpoolDirectorySource.java:254)]Spooling Directory Source runner
 has shutdown.</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">2014-06-25 10:51:01,434 (pool-6-thread-1) [INFO -org.apache.flume.client.avro.ReliableSpoolingFileEventReader.rollCurrentFile(ReliableSpoolingFileEventReader.java:332)]<span style="color:#FF0000;">Preparing
 to move file/usr/local/yting/flume/tdata/tdir1/yting_flume_example_agent1_00001.log to/usr/local/yting/flume/tdata/tdir1/yting_flume_example_agent1_00001.log.COMPLETED</span></span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">2014-06-25 10:51:02,436 (pool-6-thread-1) [INFO -org.apache.flume.source.SpoolDirectorySource$SpoolDirectoryRunnable.run(SpoolDirectorySource.java:254)]Spooling Directory Source runner
 has shutdown.</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">2014-06-25 10:51:02,473(SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO -org.apache.flume.sink.hdfs.BucketWriter.open(BucketWriter.java:261)]<span style="color:#FF0000;">Creatinghdfs://rs229:9000/yting/flumet/FlumeData.1403664662360.tmp</span></span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">2014-06-25 10:51:07,440 (pool-6-thread-1) [INFO -org.apache.flume.source.SpoolDirectorySource$SpoolDirectoryRunnable.run(SpoolDirectorySource.java:254)]Spooling Directory Source runner
 has shutdown.</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">2014-06-25 10:51:07,519 (hdfs-sink1-roll-timer-0)[INFO - org.apache.flume.sink.hdfs.BucketWriter.close(BucketWriter.java:409)]<span style="color:#FF0000;">Closinghdfs://rs229:9000/yting/flumet/FlumeData.1403664662360.tmp</span></span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">2014-06-25 10:51:07,521 (hdfs-sink1-call-runner-3)[INFO - org.apache.flume.sink.hdfs.BucketWriter$3.call(BucketWriter.java:339)]<span style="color:#FF0000;">Close tries incremented</span></span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">2014-06-25 10:51:07,549 (hdfs-sink1-call-runner-4)[INFO - org.apache.flume.sink.hdfs.BucketWriter$8.call(BucketWriter.java:669)]<span style="color:#FF0000;">Renaminghdfs://rs229:9000/yting/flumet/FlumeData.1403664662360.tmp
 tohdfs://rs229:9000/yting/flumet/FlumeData.1403664662360</span></span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">2014-06-25 10:51:07,557 (hdfs-sink1-roll-timer-0)[INFO - org.apache.flume.sink.hdfs.HDFSEventSink$1.run(HDFSEventSink.java:402)]<span style="color:#FF0000;">Writer callback called.</span></span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">2014-06-25 10:51:16,448 (pool-6-thread-1) [INFO -org.apache.flume.source.SpoolDirectorySource$SpoolDirectoryRunnable.run(SpoolDirectorySource.java:254)]Spooling Directory Source runner
 has shutdown.</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">2014-06-25 10:51:16,626 (Log-BackgroundWorker-channel1)[INFO -org.apache.flume.channel.file.EventQueueBackingStoreFile.beginCheckpoint(EventQueueBackingStoreFile.java:214)]Start checkpoint
 for/usr/local/yting/flume/checkpointdir/tcpdir/example_agent1_001/checkpoint,elements to sync = 1</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">2014-06-25 10:51:16,628(Log-BackgroundWorker-channel1) [INFO -org.apache.flume.channel.file.EventQueueBackingStoreFile.checkpoint(EventQueueBackingStoreFile.java:239)]Updating checkpoint
 metadata: logWriteOrderID: 1403663867125, queueSize: 0,queueHead: 0</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">2014-06-25 10:51:16,630(Log-BackgroundWorker-channel1) [INFO -org.apache.flume.channel.file.Log.writeCheckpoint(Log.java:1005)] Updatedcheckpoint for file:/usr/local/yting/flume/datadirs/tddirs/example_agent1_001/log-1
 position: 206logWriteOrderID: 1403663867125</span></p>
<p style="color:rgb(51,51,51);"><span style="color:#FF0000;">注意：</span>这里的分析请看下面的分析整个过程</p>
<h3 style="color:rgb(51,51,51);"><a name="t31" style="color:rgb(51,102,153);"></a># 查看hdfs上flume是否上传了数据</h3>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">[root@rs229 tdir1]# hadoop fs -ls /yting</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">Found 2 items</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">drwxr-xr-x   -root supergroup          0 2014-06-2510:51 /yting/flumet</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">-rw-r--r--   3root supergroup       4278 2014-06-1018:29 /yting/yarn-daemon.sh</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">[root@rs229 tdir1]# hadoop fs -ls /yting/flumet</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">Found 1 items</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">-rw-r--r--   3root supergroup         50 2014-06-2510:51 /yting/flumet/FlumeData.1403664662360</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">[root@rs229 tdir1]# hadoop fs -cat /yting/flumet/FlumeData.1403664662360</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">The you smile until forever .....................</span><span style="background:rgb(217,217,217);">（日志信息以及被上传了，</span><span style="background:rgb(217,217,217);">OK</span><span style="background:rgb(217,217,217);">、、、）</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);">[root@rs229 tdir1]#</span></p>
<p style="color:rgb(51,51,51);"><span style="background:rgb(217,217,217);"><img src="https://img-blog.csdn.net/20140625115132718?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjE4NTI5Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<h3 style="color:rgb(51,51,51);"><a name="t32" style="color:rgb(51,102,153);"></a># 分析整个过程</h3>
<p style="color:rgb(51,51,51);">通过分析<span style="color:#FF0000;">flume shell</span><span style="color:#FF0000;">的日志信息</span>可以发现当我们在监视目录下新文件被创建保存的时候flume进行处理并且重命名该文件，在原文件命后面添加<span style="color:#FF0000;">.COMPLETE</span>,然后将文件中的数据上传到hdfs中并创建一个临时文件<span style="color:#FF0000;">filename.tmp</span>,上传成功后重命名hdfs上的临时文件，将文件后缀<span style="color:#FF0000;">.tmp</span>去掉就ok了，最后flume将本次操作写入自己的日志信息。</p>
<h3 style="color:rgb(51,51,51);"><a name="t33" style="color:rgb(51,102,153);"></a># 初学者注意的地方</h3>
<p style="color:rgb(51,51,51);"># 配置文件的文件名命</p>
<p style="color:rgb(51,51,51);"># 配置文件中的agent1与flume-ng 的-n 参数一直</p>
<p style="color:rgb(51,51,51);"># 最好配置文件的文件名与配置文件内容的名字一样，这样-n参数就不会敲错了</p>
<br><p><span style="color:rgb(51,51,51);background:rgb(242,242,242);">对<span style="font-family:Arial;">java</span><span style="font-family:'宋体';">有兴趣的朋友可以加上面说的</span><span style="font-family:Arial;">553175249</span><span style="font-family:'宋体';">这个群哦，一起学习，共同进步
</span><span style="font-family:Arial;">.</span></span></p>
<span style="font-size:10px;">对<a href="http://lib.csdn.net/base/hadoop" rel="nofollow" class="replace_word" title="Hadoop知识库" style="color:rgb(223,52,52);text-decoration:none;font-weight:bold;">云计算</a>有兴趣的朋友可以加上面说的214293307这个群哦，一起学习，共同进步 ...</span>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t1" style="color:rgb(51,102,153);"></a></h2>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a style="color:rgb(51,102,153);"></a># Flume介绍</h2>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
Flume是Cloudera提供的一个<span style="color:#FF0000;">高可用的</span>，<span style="color:#FF0000;">高可靠的</span>，<span style="color:#FF0000;">分布式的</span>海量日志采集、聚合和传输的系统，Flume支持在日志系统中定制各类数据发送方，用于收集数据；同时，Flume提供对数据进行简单处理，并写到各种数据接受方（可定制）的能力。</p>
<h3 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a style="color:rgb(51,102,153);"></a><span># </span><span>系统功能</span></h3>
<h4 style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);"></a># 日志收集</h4>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
Flume最早是Cloudera提供的日志收集系统，目前是Apache下的一个孵化项目，Flume支持在日志系统中定制各类数据发送方，用于收集数据。</p>
<h4 style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);"></a># 数据处理</h4>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
Flume提供对数据进行简单处理，并写到各种数据接受方（可定制）的能力Flume提供了从console（控制台）、RPC（Thrift-RPC）、text（文件）、tail（UNIX tail）、syslog（syslog日志系统，支持TCP和UDP等2种模式），exec（命令执行）等数据源上收集数据的能力。</p>
<h4 style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);"></a># 工作方式 <span style="color:rgb(255,0,0);">（Flume-NG旧版本的概念，新版本已经丢弃）</span></h4>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
Flume采用了多Master的方式。为了保证配置数据的一致性，Flume引入了ZooKeeper，用于保存配置数据，ZooKeeper本身可保证配置数据的一致性和高可用，另外，在配置数据发生变化时，ZooKeeper可以通知FlumeMaster节点。Flume Master间使用gossip协议同步数据。</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
</p>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a style="color:rgb(51,102,153);"></a># Flume的设计目标（百度百科）</h2>
<h3 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a style="color:rgb(51,102,153);"></a># 可靠性 </h3>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
当节点出现故障时，日志能够被传送到其他节点上而不会丢失。Flume提供了三种级别的可靠性保障，从强到弱依次分别为：end-to-end（收到数据agent首先将event写到磁盘上，当数据传送成功后，再删除；如果数据发送失败，可以重新发送。），Store on failure（这也是scribe采用的策略，当数据接收方crash时，将数据写到本地，待恢复后，继续发送），Besteffort（数据发送到接收方后，不会进行确认）。</p>
<h3 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a style="color:rgb(51,102,153);"></a># 可扩展性<span style="color:rgb(255,0,0);">（Flume-NG旧版本的概念，新版本已经丢弃）</span></h3>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
Flume采用了三层<a href="http://lib.csdn.net/base/architecture" rel="nofollow" class="replace_word" title="大型网站架构知识库" style="color:rgb(223,52,52);text-decoration:none;font-weight:bold;">架构</a>，分别为agent，collector和storage，每一层均可以水平扩展。其中，所有agent和collector由master统一管理，这使得系统容易监控和维护，且master允许有多个（使用ZooKeeper进行管理和负载均衡），这就避免了单点故障问题。</p>
<h3 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a style="color:rgb(51,102,153);"></a># 可管理性<span style="color:rgb(255,0,0);">（Flume-NG旧版本的概念，新版本已经丢弃）</span></h3>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
所有agent和colletor由master统一管理，这使得系统便于维护。多master情况，Flume利用ZooKeeper和gossip，保证动态配置数据的一致性。用户可以在master上查看各个数据源或者数据流执行情况，且可以对各个数据源配置和动态加载。Flume提供了web和shell script command两种形式对数据流进行管理。</p>
<h3 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a style="color:rgb(51,102,153);"></a># 功能可扩展性</h3>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
用户可以根据需要添加自己的agent，collector或者storage。此外，Flume自带了很多组件，包括各种agent（file，syslog等），collector和storage（file，HDFS等）。（这里看下面的Flume架构图你就明白了）</p>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a style="color:rgb(51,102,153);"></a># Flume架构</h2>
<h4 style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);"></a># Flume基础架构，如下图：</h4>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
  <img src="https://img-blog.csdn.net/20140625114928093?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjE4NTI5Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;"></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
这是一个flume-ng 最简单的图。flume-ng 是由一个个agent组成的。一个agent就像一个细胞一样。</p>
<h4 style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);"></a># Flume的多agent架构，如下图：</h4>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
    <img src="https://img-blog.csdn.net/20140625114937015?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjE4NTI5Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;"></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
   上面是两个agent链接在一起的，再看看更多的......</p>
<h4 style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);"></a># Flume的合并（合作）架构，如下图：</h4>
<div style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<img src="https://img-blog.csdn.net/20140625114948406?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjE4NTI5Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;"><br></div>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
你是不是觉得这种设计是不是吊炸天了，可以随意组合，跟搭积木一样。跟Storm的设计思想是差不多的，何止吊炸天啊，简直就是吊炸天 、、、</p>
<h4 style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);"></a># Flume的多路复用架构，如下图：</h4>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
      <img src="https://img-blog.csdn.net/20140625115000328?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjE4NTI5Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;"></p>
<h4 style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);"></a># agent的构造</h4>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
每个agent里都有三部分构成：source、channel和sink。</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
就相当于source接收数据，通过channel传输数据，sink把数据写到下一端。这就完了，就这么简单。其中source有很多种可以选择，channel有很多种可以选择，sink也同样有多种可以选择，并且都支持自定义。饿靠！太灵活了。想怎么玩就怎么玩，这你妹的！</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
同时，如上上图所示，agent还支持选择器，就是一个source支持多个channel和多个sink，这样就完成了数据的分发。</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
这就完了，flume-ng就这么简单........</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
从看到最后用，一天足可以搞定。剩下的就是怎么组织你的agent的问题了。也就是搭积木的过程......</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
另外有一点需要强调的是，flume-ng提供了一种特殊的启动方式（不同于agent），那就是client启动。cilent是一个特殊的agent, 他的source是文件，channel是内存，sink是arvo。实际上是为了方便大家用，直接来传递文件的。具体可以看看官方使用手册。</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
估计到这儿，应该对flume-ng有了解了吧 、、、on my god、、、</p>
<br style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a style="color:rgb(51,102,153);"></a># Flume的安装</h2>
<h3 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a style="color:rgb(51,102,153);"></a># 下载 flume（使用wget下载）</h3>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">[root@rs229 flume]# wget -c -P /root http://mirrors.cnnic.cn/apache/flume/1.5.0/apache-flume-1.5.0-bin.tar.gz</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
# 安装</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">root@rs229 flume]# pwd</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">/usr/local/adsit/yting/apache/flume</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">[root@rs229 flume]# ll</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">total 4</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">drwxr-xr-x 3 root root 4096 Jun 24 17:25mirrors.cnnic.cn</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">[root@rs229 flume]# cpmirrors.cnnic.cn/apache/flume/1.5.0/apache-flume-1.5.0-bin.tar.gz .</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">[root@rs229 flume]# ll</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">total 25276</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">-rw-r--r-- 1 root root 25876246 Jun 24 17:27apache-flume-1.5.0-bin.tar.gz</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">drwxr-xr-x 3 root root     4096 Jun 24 17:25 mirrors.cnnic.cn</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">[root@rs229 flume]# tar -zxvfapache-flume-1.5.0-bin.tar.gz</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);"> </span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">[root@rs229 flume]# ll</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">total 25280</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">drwxr-xr-x 7 root root     4096 Jun 24 17:27 apache-flume-1.5.0-bin</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">-rw-r--r-- 1 root root 25876246 Jun 24 17:27apache-flume-1.5.0-bin.tar.gz</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">drwxr-xr-x 3 root root     4096 Jun 24 17:25 mirrors.cnnic.cn</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">[root@rs229 flume]# rm -rfapache-flume-1.5.0-bin.tar.gz</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">[root@rs229 flume]# rm -rf mirrors.cnnic.cn/</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">[root@rs229 flume]# ll</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">total 4</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">drwxr-xr-x 7 root root 4096 Jun 24 17:27apache-flume-1.5.0-bin</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">[root@rs229 flume]#</span></p>
<h3 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a style="color:rgb(51,102,153);"></a># 修改 flume-env.sh 配置文件</h3>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">[root@rs229 conf]# pwd</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">/usr/local/adsit/yting/apache/flume/apache-flume-1.5.0-bin/conf</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">[root@rs229 conf]# ll</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">total 12</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">-rw-r--r-- 1 501 games 1661 Mar 29 06:15flume-conf.properties.template</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">-rw-r--r-- 1 501 games 1197 Mar 29 06:15 flume-env.sh.template</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">-rw-r--r-- 1 501 games 3063 Mar 29 06:15log4j.properties</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">[root@rs229 conf]# cp flume-env.sh.templateflume-env.sh</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">[root@rs229 conf]# vi flume-env.sh</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);"> </span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
# Enviroment variables can be sethere.</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
JAVA_HOME=/usr/local/adsit/yting/jdk/jdk1.7.0_60</p>
<h3 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a style="color:rgb(51,102,153);"></a># 修改 flume-site.xml 配置文件（貌似没有该步骤，貌似也可以修改，研究后再来弄吧！）</h3>
<h3 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a style="color:rgb(51,102,153);"></a># 验证 flume是否安装成功</h3>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">[root@rs229 conf]# ../bin/flume-ng version</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">Flume 1.5.0</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">Source code repository:https://<a href="http://lib.csdn.net/base/git" rel="nofollow" class="replace_word" title="Git知识库" style="color:rgb(223,52,52);text-decoration:none;font-weight:bold;">Git</a>-wip-us.apache.org/repos/asf/flume.git</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">Revision: 8633220df808c4cd0c13d1cf0320454a94f1ea97</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">Compiled by hshreedharan on Wed May  7 14:49:18 PDT 2014</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">From source with checksuma01fe726e4380ba0c9f7a7d222db961f</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="color:#FF0000;">出现这样的信息表示安装成功了</span></p>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a style="color:rgb(51,102,153);"></a># Flume 入门案例</h2>
<h3 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a style="color:rgb(51,102,153);"></a># Flume监控指定目录下的日志信息，并将日志信息上传到HDFS中去</h3>
<h3 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a style="color:rgb(51,102,153);"></a># 在conf目录下新建example.conf配置文件</h3>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="color:#FF0000;">新建文件：</span>在conf目录下新建一个example.conf文件（随便起什么名字），当然随便哪里都行</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="color:#FF0000;">注意：</span>文件名最好跟配置中的名字一样，比如里面的agent1最好跟外面的文件名一样，<span style="color:#FF0000;">见名知意</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">[root@rs229 conf]# pwd</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">/usr/local/adsit/yting/apache/flume/apache-flume-1.5.0-bin/conf</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">[root@rs229 apache-flume-1.5.0-bin]# catconf/example.conf</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);"># agent1 : yting first flume example</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">agent1.sources=source1</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">agent1.sinks=sink1</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">agent1.channels=channel1</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);"> </span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);"># configure source1</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">agent1.sources.source1.type=spooldir</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">agent1.sources.source1.spoolDir=<span style="color:#FF0000;">/usr/local/yting/flume/tdata/tdir1</span></span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">agent1.sources.source1.channels=channel1</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">agent1.sources.source1.fileHeader = false</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);"> </span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);"># configure sink1</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">agent1.sinks.sink1.type=hdfs</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">agent1.sinks.sink1.hdfs.path=<span style="color:#FF0000;">hdfs://rs229:9000/yting/flumet</span></span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">agent1.sinks.sink1.hdfs.fileType=DataStream</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">agent1.sinks.sink1.hdfs.writeFormat=TEXT</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">agent1.sinks.sink1.hdfs.rollInterval=4</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">agent1.sinks.sink1.channel=channel1</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);"> </span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);"># configure channel1</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">agent1.channels.channel1.type=file</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">agent1.channels.channel1.checkpointDir=<span style="color:#FF0000;">/usr/local/yting/flume/checkpointdir/tcpdir/example_agent1_001</span></span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">agent1.channels.channel1.dataDirs=<span style="color:#FF0000;">/usr/local/yting/flume/datadirs/tddirs/example_agent1_001</span></span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="color:#FF0000;">注意：</span>红色字体部分自己修改成自己对应的目录了</p>
<h3 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a style="color:rgb(51,102,153);"></a># 运行Flume 使用example.conf</h3>
<h4 style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);"></a>#命令参数说明</h4>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="color:#FF0000;">-c </span>conf 指定配置目录为conf</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="color:#FF0000;">-f</span> conf/example.conf 指定配置文件为conf/example.conf</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="color:#FF0000;">-n</span> agent1 指定agent名字为agent1,需要与example.conf中的一致（这里不一致，可能会一直停在那里，请参考笔记中后面的错误全集Flume部分，那里介绍了错误的分析，原因，解决）</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="color:#FF0000;">-Dflume.root.logger=INFO,console</span> 指定DEBUF模式在console输出INFO信息</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<br></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">[root@rs229conf]# ./bin/flume-ng agent -c conf/ -f conf/example.conf<span style="color:#FF0000;">-n agent1</span>-Dflume.root.logger=INFO,console</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">-bash:./bin/flume-ng: No such file or directory</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">[root@rs229conf]# cd ..</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">[root@rs229apache-flume-1.5.0-bin]# ./bin/flume-ng agent -c conf/ -f conf/example.conf -nagent1 -Dflume.root.logger=INFO,console</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">Info: Sourcingenvironment configuration script/usr/local/adsit/yting/apache/flume/apache-flume-1.5.0-bin/conf/flume-env.sh</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">Info: IncludingHadoop libraries found via(/usr/local/adsit/yting/apache/<a href="http://lib.csdn.net/base/hadoop" rel="nofollow" class="replace_word" title="Hadoop知识库" style="color:rgb(223,52,52);text-decoration:none;font-weight:bold;">Hadoop</a>/hadoop-2.2.0/bin/hadoop)
 for HDFS access</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">Info: Excluding/usr/local/adsit/yting/apache/hadoop/hadoop-2.2.0/share/hadoop/common/lib/slf4j-api-1.7.5.jarfrom classpath</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">Info: Excluding/usr/local/adsit/yting/apache/hadoop/hadoop-2.2.0/share/hadoop/common/lib/slf4j-log4j12-1.7.5.jarfrom classpath</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">Info: IncludingHBASE libraries found via(/usr/local/adsit/yting/apache/hbase/hbase-0.96.2-hadoop2/bin/hbase) for HBASEaccess</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">Info: Excluding/usr/local/adsit/yting/apache/hbase/hbase-0.96.2-hadoop2/bin/../lib/slf4j-api-1.6.4.jarfrom classpath</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">Info: Excluding/usr/local/adsit/yting/apache/hbase/hbase-0.96.2-hadoop2/bin/../lib/slf4j-log4j12-1.6.4.jarfrom classpath</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">Info: Excluding/usr/local/adsit/yting/apache/hadoop/hadoop-2.2.0/share/hadoop/common/lib/slf4j-api-1.7.5.jarfrom classpath</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">Info: Excluding/usr/local/adsit/yting/apache/hadoop/hadoop-2.2.0/share/hadoop/common/lib/slf4j-log4j12-1.7.5.jarfrom classpath</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">….capacity-scheduler/*.jar:/conf'-Djava.library.path=:/usr/local/adsit/yting/apache/hadoop/hadoop-2.2.0/lib:/usr/local/adsit/yting/apache/hadoop/hadoop-2.2.0//liborg.apache.flume.node.Application -f conf/example.conf
 -n agent1</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:45,763 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.node.PollingPropertiesFileConfigurationProvider.start(PollingPropertiesFileConfigurationProvider.<a href="http://lib.csdn.net/base/javaee" rel="nofollow" class="replace_word" title="Java EE知识库" style="color:rgb(223,52,52);text-decoration:none;font-weight:bold;">Java</a>:61)]Configuration
 provider starting</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:45,772 (conf-file-poller-0) [INFO -org.apache.flume.node.PollingPropertiesFileConfigurationProvider$FileWatcherRunnable.run(PollingPropertiesFileConfigurationProvider.java:133)]Reloading configuration
 file:conf/example.conf</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:45,781 (conf-file-poller-0) [INFO -org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:1016)]Processing:sink1</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:45,783 (conf-file-poller-0) [INFO -org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:1016)]Processing:sink1</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:45,783 (conf-file-poller-0) [INFO -org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:930)]Added sinks: sink1 Agent: agent1</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:45,783 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:1016)]Processing:sink1</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:45,783 (conf-file-poller-0) [INFO -org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:1016)]Processing:sink1</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:45,783 (conf-file-poller-0) [INFO -org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:1016)]Processing:sink1</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:45,784 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:1016)]Processing:sink1</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:45,809 (conf-file-poller-0) [INFO -org.apache.flume.conf.FlumeConfiguration.validateConfiguration(FlumeConfiguration.java:140)]Post-validation flume configuration contains configuration for agents:
 [agent1]</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:45,809 (conf-file-poller-0) [INFO -org.apache.flume.node.AbstractConfigurationProvider.loadChannels(AbstractConfigurationProvider.java:150)]Creating channels</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:45,823 (conf-file-poller-0) [INFO -org.apache.flume.channel.DefaultChannelFactory.create(DefaultChannelFactory.java:40)]Creating instance of channel channel1 type file</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:45,828 (conf-file-poller-0) [INFO - org.apache.flume.node.AbstractConfigurationProvider.loadChannels(AbstractConfigurationProvider.java:205)]Created channel channel1</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:45,829 (conf-file-poller-0) [INFO -org.apache.flume.source.DefaultSourceFactory.create(DefaultSourceFactory.java:39)]Creating instance of source source1, type spooldir</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:45,844 (conf-file-poller-0) [INFO -org.apache.flume.sink.DefaultSinkFactory.create(DefaultSinkFactory.java:40)]Creating instance of sink: sink1, type: hdfs</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:46,293 (conf-file-poller-0) [WARN -org.apache.hadoop.util.NativeCodeLoader.&lt;clinit&gt;(NativeCodeLoader.java:62)]Unable to load native-hadoop library for your platform... using builtin-javaclasses
 where applicable</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:46,572 (conf-file-poller-0) [INFO - org.apache.flume.sink.hdfs.HDFSEventSink.authenticate(HDFSEventSink.java:555)]Hadoop Security enabled: false</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:46,576 (conf-file-poller-0) [INFO -org.apache.flume.node.AbstractConfigurationProvider.getConfiguration(AbstractConfigurationProvider.java:119)]Channel channel1 connected to [source1, sink1]</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:46,587 (conf-file-poller-0) [INFO -org.apache.flume.node.Application.startAllComponents(Application.java:138)]Starting new configuration:{ sourceRunners:{source1=EventDrivenSourceRunner: {source:Spool
 Directory source source1: { spoolDir:/usr/local/yting/flume/tdata/tdir1 } }} sinkRunners:{sink1=SinkRunner: {policy:org.apache.flume.sink.DefaultSinkProcessor@7205c140 counterGroup:{name:null counters:{} } }} channels:{channel1=FileChannel channel1 { dataDirs:[/usr/local/yting/flume/datadirs/tddirs/example_agent1_001]
 }} }</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:46,593 (conf-file-poller-0) [INFO -org.apache.flume.node.Application.startAllComponents(Application.java:145)]Starting Channel channel1</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:46,593 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.FileChannel.start(FileChannel.java:259)] StartingFileChannel channel1 { dataDirs:[/usr/local/yting/flume/datadirs/tddirs/example_agent1_001]
 }...</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:46,617 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.Log.&lt;init&gt;(Log.java:328)] Encryption is notenabled</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:46,618 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.Log.replay(Log.java:373)] Replay started</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:46,620 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.Log.replay(Log.java:385)] Found NextFileID 0,from []</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:46,661 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.EventQueueBackingStoreFile.&lt;init&gt;(EventQueueBackingStoreFile.java:91)]Preallocated/usr/local/yting/flume/checkpointdir/tcpdir/example_agent1_001/checkpoint
 to8008232 for capacity 1000000</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:46,663 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.EventQueueBackingStoreFileV3.&lt;init&gt;(EventQueueBackingStoreFileV3.java:53)]Starting up with/usr/local/yting/flume/checkpointdir/tcpdir/example_agent1_001/checkpoint
 and/usr/local/yting/flume/checkpointdir/tcpdir/example_agent1_001/checkpoint.meta</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,095 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.FlumeEventQueue.&lt;init&gt;(FlumeEventQueue.java:114)]QueueSet population inserting 0 took 0</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,100 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.Log.replay(Log.java:423)] Last Checkpoint Wed Jun25 10:37:46 CST 2014, queue depth = 0</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,105 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.Log.doReplay(Log.java:507)] Replaying logs withv2 replay logic</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,109 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.ReplayHandler.replayLog(ReplayHandler.java:249)]Starting replay of []</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,109 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.ReplayHandler.replayLog(ReplayHandler.java:346)]read: 0, put: 0, take: 0, rollback: 0, commit: 0, skip: 0, eventCount:0</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,110 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.FlumeEventQueue.replayComplete(FlumeEventQueue.java:407)]Search Count = 0, Search Time = 0, Copy Count = 0, Copy Time = 0</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,119 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.Log.replay(Log.java:470)] Rolling/usr/local/yting/flume/datadirs/tddirs/example_agent1_001</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,120 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.Log.roll(Log.java:932)] Roll start/usr/local/yting/flume/datadirs/tddirs/example_agent1_001</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,137 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.tools.DirectMemoryUtils.getDefaultDirectMemorySize(DirectMemoryUtils.java:113)]Unable to get maxDirectMemory from VM: NoSuchMethodException:sun.misc.VM.maxDirectMemory(null)</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,140 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.tools.DirectMemoryUtils.allocate(DirectMemoryUtils.java:47)]Direct Memory Allocation:  Allocation =1048576, Allocated = 0, MaxDirectMemorySize
 = 18874368, Remaining = 18874368</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,195 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.channel.file.LogFile$Writer.&lt;init&gt;(LogFile.java:214)]Opened /usr/local/yting/flume/datadirs/tddirs/example_agent1_001/log-1</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,208 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.Log.roll(Log.java:948)] Roll end</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,208 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.EventQueueBackingStoreFile.beginCheckpoint(EventQueueBackingStoreFile.java:214)]Start checkpoint for/usr/local/yting/flume/checkpointdir/tcpdir/example_agent1_001/checkpoint,elements
 to sync = 0</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,211 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.EventQueueBackingStoreFile.checkpoint(EventQueueBackingStoreFile.java:239)]Updating checkpoint metadata: logWriteOrderID: 1403663867120,
 queueSize: 0,queueHead: 0</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,235 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.Log.writeCheckpoint(Log.java:1005)] Updatedcheckpoint for file:/usr/local/yting/flume/datadirs/tddirs/example_agent1_001/log-1
 position: 0logWriteOrderID: 1403663867120</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,235 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.FileChannel.start(FileChannel.java:285)] QueueSize after replay: 0 [channel=channel1]</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,296 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.register(MonitoredCounterGroup.java:119)]Monitored counter group for type: CHANNEL, name: channel1:
 Successfullyregistered new MBean.</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,296 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.start(MonitoredCounterGroup.java:95)]Component type: CHANNEL, name: channel1 started</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,297 (conf-file-poller-0) [INFO -org.apache.flume.node.Application.startAllComponents(Application.java:173)] StartingSink sink1</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,297 (conf-file-poller-0) [INFO -org.apache.flume.node.Application.startAllComponents(Application.java:184)]Starting Source source1</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,298 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.source.SpoolDirectorySource.start(SpoolDirectorySource.java:77)]SpoolDirectorySource source starting with directory:/usr/local/yting/flume/tdata/tdir1</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,300 (lifecycleSupervisor-1-1) [INFO -org.apache.flume.instrumentation.MonitoredCounterGroup.register(MonitoredCounterGroup.java:119)]Monitored counter group for type: SINK, name: sink1: Successfully
 registerednew MBean.</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,300 (lifecycleSupervisor-1-1) [INFO -org.apache.flume.instrumentation.MonitoredCounterGroup.start(MonitoredCounterGroup.java:95)]Component type: SINK, name: sink1 started</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,330 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.instrumentation.MonitoredCounterGroup.register(MonitoredCounterGroup.java:119)]Monitored counter group for type: SOURCE, name: source1:
 Successfullyregistered new MBean.</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,330 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.instrumentation.MonitoredCounterGroup.start(MonitoredCounterGroup.java:95)]Component type: SOURCE, name: source1 started</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,331 (pool-6-thread-1) [INFO -org.apache.flume.source.SpoolDirectorySource$SpoolDirectoryRunnable.run(SpoolDirectorySource.java:254)]Spooling Directory Source runner has shutdown.</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,831 (pool-6-thread-1) [INFO - org.apache.flume.source.SpoolDirectorySource$SpoolDirectoryRunnable.run(SpoolDirectorySource.java:254)]Spooling Directory Source runner has shutdown.</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background-color:rgb(204,204,204);">2014-06-2510:37:48,332 (pool-6-thread-1) [INFO -org.apache.flume.source.SpoolDirectorySource$SpoolDirectoryRunnable.run(SpoolDirectorySource.java:254)]Spooling Directory Source runner has shutdown.</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="color:#FF0000;">到了这里说明你的程序运行正常了，但是你的监视目录</span><span style="color:#FF0000;"> /usr/local/yting/flume/tdata/tdir1</span><span style="color:#FF0000;">下没有新文件的产生，所以会一直出现上面的那条信息</span></p>
<h3 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a style="color:rgb(51,102,153);"></a># 在/usr/local/yting/flume/tdata/tdir1这个flume监视目录下添加一个新文件<span style="color:#FF0000;">yting_flume_example_agent1_00001.log</span></h3>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">[root@rs229 hadoop-2.2.0]# ./bin/hadoop fs -ls /yting</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">14/06/25 10:48:00 WARN util.NativeCodeLoader: Unableto load native-hadoop library for your platform... using builtin-java classeswhere applicable</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">Found 1 items</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">-rw-r--r--   3root supergroup       4278 2014-06-1018:29 /yting/yarn-daemon.sh</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">[root@rs229 tdir1]# ll</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">total 0</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">[root@rs229 tdir1]# ll -a</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">total 12</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">drwxr-xr-x 3 root root 4096 Jun 25 10:37 .</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">drwxr-xr-x 3 root root 4096 Jun 24 22:25 ..</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">drwxr-xr-x 2 root root 4096 Jun 25 09:48 .flumespool</span><span style="background:rgb(217,217,217);">（隐藏文件）</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">[root@rs229 tdir1]# viyting_flume_example_agent1_00001.log</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">The you smile until forever .....................</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">[root@rs229 tdir1]# ll</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">total 4</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">-rw-r--r-- 1 root root 50 Jun 25 10:51 <a style="color:rgb(51,102,153);"></a><a style="color:rgb(51,102,153);">yting_flume_example_agent1_00001.log.COMPLETED</a></span></p>
<h3 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a style="color:rgb(51,102,153);"></a># 文件名变成.COMPLETED结尾</h3>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">说明该文件</span><span style="background:rgb(217,217,217);">yting_flume_example_agent1_00001.log</span><span style="background:rgb(217,217,217);">已经被</span><span style="background:rgb(217,217,217);">flume</span><span style="background:rgb(217,217,217);">处理了，处理过后的文件名变成</span><span style="background:rgb(217,217,217);">yting_flume_example_agent1_00001.log.COMPLETED,</span><span style="background:rgb(217,217,217);">接下来看看</span><span style="background:rgb(217,217,217);">flume</span><span style="background:rgb(217,217,217);">那边的信息，应该发生变化了</span></p>
<h3 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a style="color:rgb(51,102,153);"></a># 查看flume shell的信息变化</h3>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">2014-06-25 10:51:00,530 (pool-6-thread-1) [INFO -org.apache.flume.source.SpoolDirectorySource$SpoolDirectoryRunnable.run(SpoolDirectorySource.java:254)]Spooling Directory Source runner has shutdown.</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">2014-06-25 10:51:01,434 (pool-6-thread-1) [INFO -org.apache.flume.client.avro.ReliableSpoolingFileEventReader.rollCurrentFile(ReliableSpoolingFileEventReader.java:332)]<span style="color:#FF0000;">Preparing to move file/usr/local/yting/flume/tdata/tdir1/yting_flume_example_agent1_00001.log
 to/usr/local/yting/flume/tdata/tdir1/yting_flume_example_agent1_00001.log.COMPLETED</span></span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">2014-06-25 10:51:02,436 (pool-6-thread-1) [INFO -org.apache.flume.source.SpoolDirectorySource$SpoolDirectoryRunnable.run(SpoolDirectorySource.java:254)]Spooling Directory Source runner has shutdown.</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">2014-06-25 10:51:02,473(SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO -org.apache.flume.sink.hdfs.BucketWriter.open(BucketWriter.java:261)]<span style="color:#FF0000;">Creatinghdfs://rs229:9000/yting/flumet/FlumeData.1403664662360.tmp</span></span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">2014-06-25 10:51:07,440 (pool-6-thread-1) [INFO -org.apache.flume.source.SpoolDirectorySource$SpoolDirectoryRunnable.run(SpoolDirectorySource.java:254)]Spooling Directory Source runner has shutdown.</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">2014-06-25 10:51:07,519 (hdfs-sink1-roll-timer-0)[INFO - org.apache.flume.sink.hdfs.BucketWriter.close(BucketWriter.java:409)]<span style="color:#FF0000;">Closinghdfs://rs229:9000/yting/flumet/FlumeData.1403664662360.tmp</span></span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">2014-06-25 10:51:07,521 (hdfs-sink1-call-runner-3)[INFO - org.apache.flume.sink.hdfs.BucketWriter$3.call(BucketWriter.java:339)]<span style="color:#FF0000;">Close tries incremented</span></span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">2014-06-25 10:51:07,549 (hdfs-sink1-call-runner-4)[INFO - org.apache.flume.sink.hdfs.BucketWriter$8.call(BucketWriter.java:669)]<span style="color:#FF0000;">Renaminghdfs://rs229:9000/yting/flumet/FlumeData.1403664662360.tmp
 tohdfs://rs229:9000/yting/flumet/FlumeData.1403664662360</span></span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">2014-06-25 10:51:07,557 (hdfs-sink1-roll-timer-0)[INFO - org.apache.flume.sink.hdfs.HDFSEventSink$1.run(HDFSEventSink.java:402)]<span style="color:#FF0000;">Writer callback called.</span></span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">2014-06-25 10:51:16,448 (pool-6-thread-1) [INFO -org.apache.flume.source.SpoolDirectorySource$SpoolDirectoryRunnable.run(SpoolDirectorySource.java:254)]Spooling Directory Source runner has shutdown.</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">2014-06-25 10:51:16,626 (Log-BackgroundWorker-channel1)[INFO -org.apache.flume.channel.file.EventQueueBackingStoreFile.beginCheckpoint(EventQueueBackingStoreFile.java:214)]Start checkpoint for/usr/local/yting/flume/checkpointdir/tcpdir/example_agent1_001/checkpoint,elements
 to sync = 1</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">2014-06-25 10:51:16,628(Log-BackgroundWorker-channel1) [INFO -org.apache.flume.channel.file.EventQueueBackingStoreFile.checkpoint(EventQueueBackingStoreFile.java:239)]Updating checkpoint metadata: logWriteOrderID: 1403663867125,
 queueSize: 0,queueHead: 0</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">2014-06-25 10:51:16,630(Log-BackgroundWorker-channel1) [INFO -org.apache.flume.channel.file.Log.writeCheckpoint(Log.java:1005)] Updatedcheckpoint for file:/usr/local/yting/flume/datadirs/tddirs/example_agent1_001/log-1
 position: 206logWriteOrderID: 1403663867125</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="color:#FF0000;">注意：</span>这里的分析请看下面的分析整个过程</p>
<h3 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a style="color:rgb(51,102,153);"></a># 查看hdfs上flume是否上传了数据</h3>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">[root@rs229 tdir1]# hadoop fs -ls /yting</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">Found 2 items</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">drwxr-xr-x   -root supergroup          0 2014-06-2510:51 /yting/flumet</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">-rw-r--r--   3root supergroup       4278 2014-06-1018:29 /yting/yarn-daemon.sh</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">[root@rs229 tdir1]# hadoop fs -ls /yting/flumet</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">Found 1 items</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">-rw-r--r--   3root supergroup         50 2014-06-2510:51 /yting/flumet/FlumeData.1403664662360</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">[root@rs229 tdir1]# hadoop fs -cat /yting/flumet/FlumeData.1403664662360</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">The you smile until forever .....................</span><span style="background:rgb(217,217,217);">（日志信息以及被上传了，</span><span style="background:rgb(217,217,217);">OK</span><span style="background:rgb(217,217,217);">、、、）</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);">[root@rs229 tdir1]#</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="background:rgb(217,217,217);"><img src="https://img-blog.csdn.net/20140625115132718?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjE4NTI5Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;"><br></span></p>
<h3 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a style="color:rgb(51,102,153);"></a># 分析整个过程</h3>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
通过分析<span style="color:#FF0000;">flume shell</span><span style="color:#FF0000;">的日志信息</span>可以发现当我们在监视目录下新文件被创建保存的时候flume进行处理并且重命名该文件，在原文件命后面添加<span style="color:#FF0000;">.COMPLETE</span>,然后将文件中的数据上传到hdfs中并创建一个临时文件<span style="color:#FF0000;">filename.tmp</span>,上传成功后重命名hdfs上的临时文件，将文件后缀<span style="color:#FF0000;">.tmp</span>去掉就ok了，最后flume将本次操作写入自己的日志信息。</p>
<h3 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a style="color:rgb(51,102,153);"></a># 初学者注意的地方</h3>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
# 配置文件的文件名命</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
# 配置文件中的agent1与flume-ng 的-n 参数一直</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
# 最好配置文件的文件名与配置文件内容的名字一样，这样-n参数就不会敲错了</p>
            </div>
                </div>