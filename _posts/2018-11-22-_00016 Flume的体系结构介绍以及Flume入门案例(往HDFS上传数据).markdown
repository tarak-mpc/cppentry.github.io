---
layout:     post
title:      _00016 Flume的体系结构介绍以及Flume入门案例(往HDFS上传数据)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，转载请说明来源于 ApacheCN : 					https://blog.csdn.net/u012185296/article/details/34424139				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:20px;color:#FF0000;">博文作者：</span><a href="http://user.qzone.qq.com/1042658081" rel="nofollow" style="font-size:20px;color:#0000FF;">妳那伊抹微笑</a></p>
<p><span style="color:rgb(51,255,255);font-family:'微软雅黑';font-size:18px;line-height:21px;">itdog8 地址链接 : </span><a href="http://www.itdog8.com/" rel="nofollow" style="font-family:'微软雅黑';font-size:18px;line-height:21px;"><span style="color:#000099;">http://www.itdog8.com</span></a><span style="color:rgb(51,255,255);font-family:'微软雅黑';font-size:18px;line-height:21px;">（个人链接）</span><br></p>
<span style="font-size:20px;color:#FF0000;">博客地址：</span><a href="http://blog.csdn.net/u012185296" rel="nofollow" style="font-size:20px;color:#0000FF;">http://blog.csdn.net/u012185296</a><br><span style="font-size:20px;color:#FF0000;">博文标题：</span><a href="http://blog.csdn.net/u012185296/article/details/34424139" rel="nofollow" style="font-size:20px;color:#0000FF;">_00016 Flume的体系结构介绍以及Flume入门案例(往HDFS上传数据))</a><br><span style="font-size:20px;color:#FF0000;">个性签名：</span><span style="font-size:20px;">世界上最遥远的距离不是天涯，也不是海角，而是我站在妳的面前，妳却感觉不到我的存在</span><br><span style="font-size:20px;color:#FF0000;">技术方向：</span><span style="font-size:20px;">Flume+Kafka+Storm+Redis/Hbase+Hadoop+Hive+Mahout+Spark ... 云计算技术</span><br><span style="font-size:20px;color:#FF0000;">转载声明：</span><span style="font-size:20px;">可以转载, 但必须以超链接形式标明文章原始出处和作者信息及版权声明，谢谢合作！</span><br><span style="font-size:20px;color:#FF0000;">qq交流群：</span><span style="font-size:20px;">214293307  <a href="http://shang.qq.com/wpa/qunwpa?idkey=bf80524ac3630cb0951006dfcd8888187bdc020161d1586bb207d85577577a71" rel="nofollow"><img border="0" src="http://pub.idqqimg.com/wpa/images/group.png" alt="云计算之嫣然伊笑" title="云计算之嫣然伊笑">（期待与你一起学习，共同进步）</a></span><br><span style="font-size:20px;color:#FF0000;">软件版本：</span><span style="font-size:20px;">Apache Fluem-NG-1.5.0 （目前是最新版本）</span><br><br><br><h1># Flume的体系结构</h1>
<p></p>
<p><strong><span style="font-size:14px;"># 学习前言</span></strong></p>
<p>想学习一下Flume，网上找了好多文章基本上都说的很简单，只有一半什么的，简直就是坑爹，饿顿时怒火就上来了，学个东西真不容易，然后自己耐心的把这些零零碎碎的东西整理整理，各种搭环境实验之后才弄好的，也不容易啊，希望可以帮到想学Flume的你 、、、<span style="font-size:10px;">对云计算有兴趣的朋友可以加上面说的214293307这个群哦，一起学习，共同进步 ...</span></p>
<h2></h2>
<h2># Flume介绍</h2>
<p style="background:#FFFFFF;"><span style="color:#333333;">Flume</span><span style="color:#333333;">是</span><span style="color:#333333;">Cloudera</span><span style="color:#333333;">提供的一个</span><span style="color:#FF0000;">高可用的</span>，<span style="color:#FF0000;">高可靠的</span>，<span style="color:#FF0000;">分布式的</span><span style="color:#333333;">海量日志采集、聚合和传输的系统，</span><span style="color:#333333;">Flume</span><span style="color:#333333;">支持在日志系统中定制各类数据发送方，用于收集数据；同时，</span><span style="color:#333333;">Flume</span><span style="color:#333333;">提供对数据进行简单处理，并写到各种数据接受方（可定制）的能力。</span></p>
<h3><span style="background:#FFFFFF;"># </span><span style="background:#FFFFFF;">系统功能</span></h3>
<h4># 日志收集</h4>
<p style="background:#FFFFFF;"><span style="color:#333333;">Flume</span><span style="color:#333333;">最早是</span><span style="color:#333333;">Cloudera</span><span style="color:#333333;">提供的日志收集系统，目前是</span><span style="color:#333333;">Apache</span><span style="color:#333333;">下的一个孵化项目，</span><span style="color:#333333;">Flume</span><span style="color:#333333;">支持在日志系统中定制各类数据发送方，用于收集数据。</span></p>
<h4># 数据处理</h4>
<p style="background:#FFFFFF;"><span style="color:#333333;">Flume</span><span style="color:#333333;">提供对数据进行简单处理，并写到各种数据接受方（可定制）的能力</span><span style="color:#333333;">Flume</span><span style="color:#333333;">提供了从</span><span style="color:#333333;">console</span><span style="color:#333333;">（控制台）、</span><span style="color:#333333;">RPC</span><span style="color:#333333;">（</span><span style="color:#333333;">Thrift-RPC</span><span style="color:#333333;">）、</span><span style="color:#333333;">text</span><span style="color:#333333;">（文件）、</span><span style="color:#333333;">tail</span><span style="color:#333333;">（</span><span style="color:#333333;">UNIX
 tail</span><span style="color:#333333;">）、</span><span style="color:#333333;">syslog</span><span style="color:#333333;">（</span><span style="color:#333333;">syslog</span><span style="color:#333333;">日志系统，支持</span><span style="color:#333333;">TCP</span><span style="color:#333333;">和</span><span style="color:#333333;">UDP</span><span style="color:#333333;">等</span><span style="color:#333333;">2</span><span style="color:#333333;">种模式），</span><span style="color:#333333;">exec</span><span style="color:#333333;">（命令执行）等数据源上收集数据的能力。</span></p>
<h4># 工作方式 <span style="color:#ff0000;">（Flume-NG旧版本的概念，新版本已经丢弃）</span></h4>
<p style="background:#FFFFFF;"><span style="color:rgb(51,51,51);">Flume</span><span style="color:#333333;">采用了多</span><span style="color:#333333;">Master</span><span style="color:#333333;">的方式。为了保证配置数据的一致性，</span><span style="color:#333333;">Flume</span><span style="color:#333333;">引入了</span><span style="color:#333333;">ZooKeeper</span><span style="color:#333333;">，用于保存配置数据，</span><span style="color:#333333;">ZooKeeper</span><span style="color:#333333;">本身可保证配置数据的一致性和高可用，另外，在配置数据发生变化时，</span><span style="color:#333333;">ZooKeeper</span><span style="color:#333333;">可以通知</span><span style="color:#333333;">FlumeMaster</span><span style="color:#333333;">节点。</span><span style="color:#333333;">Flume
 Master</span><span style="color:#333333;">间使用</span><span style="color:#333333;">gossip</span><span style="color:#333333;">协议同步数据。</span></p>
<p style="background:#FFFFFF;"><span style="color:#333333;"></span></p>
<h2># Flume的设计目标（百度百科）</h2>
<h3># 可靠性 </h3>
<p></p>
<p style="background:#FFFFFF;"><span style="color:#333333;">当节点出现故障时，日志能够被传送到其他节点上而不会丢失。</span><span style="color:#333333;">Flume</span><span style="color:#333333;">提供了三种级别的可靠性保障，从强到弱依次分别为：</span><span style="color:#333333;">end-to-end</span><span style="color:#333333;">（收到数据</span><span style="color:#333333;">agent</span><span style="color:#333333;">首先将</span><span style="color:#333333;">event</span><span style="color:#333333;">写到磁盘上，当数据传送成功后，再删除；如果数据发送失败，可以重新发送。），</span><span style="color:#333333;">Store
 on failure</span><span style="color:#333333;">（这也是</span><span style="color:#333333;">scribe</span><span style="color:#333333;">采用的策略，当数据接收方</span><span style="color:#333333;">crash</span><span style="color:#333333;">时，将数据写到本地，待恢复后，继续发送），</span><span style="color:#333333;">Besteffort</span><span style="color:#333333;">（数据发送到接收方后，不会进行确认）。</span></p>
<h3># 可扩展性<span style="color:#ff0000;">（Flume-NG旧版本的概念，新版本已经丢弃）</span></h3>
<p style="background:#FFFFFF;"><span style="color:#333333;">Flume</span><span style="color:#333333;">采用了三层架构，分别为</span><span style="color:#333333;">agent</span><span style="color:#333333;">，</span><span style="color:#333333;">collector</span><span style="color:#333333;">和</span><span style="color:#333333;">storage</span><span style="color:#333333;">，每一层均可以水平扩展。其中，所有</span><span style="color:#333333;">agent</span><span style="color:#333333;">和</span><span style="color:#333333;">collector</span><span style="color:#333333;">由</span><span style="color:#333333;">master</span><span style="color:#333333;">统一管理，这使得系统容易监控和维护，且</span><span style="color:#333333;">master</span><span style="color:#333333;">允许有多个（使用</span><span style="color:#333333;">ZooKeeper</span><span style="color:#333333;">进行管理和负载均衡），这就避免了单点故障问题。</span></p>
<h3># 可管理性<span style="color:#ff0000;">（Flume-NG旧版本的概念，新版本已经丢弃）</span></h3>
<p style="background:#FFFFFF;"><span style="color:#333333;">所有</span><span style="color:#333333;">agent</span><span style="color:#333333;">和</span><span style="color:#333333;">colletor</span><span style="color:#333333;">由</span><span style="color:#333333;">master</span><span style="color:#333333;">统一管理，这使得系统便于维护。多</span><span style="color:#333333;">master</span><span style="color:#333333;">情况，</span><span style="color:#333333;">Flume</span><span style="color:#333333;">利用</span><span style="color:#333333;">ZooKeeper</span><span style="color:#333333;">和</span><span style="color:#333333;">gossip</span><span style="color:#333333;">，保证动态配置数据的一致性。用户可以在</span><span style="color:#333333;">master</span><span style="color:#333333;">上查看各个数据源或者数据流执行情况，且可以对各个数据源配置和动态加载。</span><span style="color:#333333;">Flume</span><span style="color:#333333;">提供了</span><span style="color:#333333;">web</span><span style="color:#333333;">和</span><span style="color:#333333;">shell
 script command</span><span style="color:#333333;">两种形式对数据流进行管理。</span></p>
<h3># 功能可扩展性</h3>
<p style="background:#FFFFFF;"><span style="color:#333333;">用户可以根据需要添加自己的</span><span style="color:#333333;">agent</span><span style="color:#333333;">，</span><span style="color:#333333;">collector</span><span style="color:#333333;">或者</span><span style="color:#333333;">storage</span><span style="color:#333333;">。此外，</span><span style="color:#333333;">Flume</span><span style="color:#333333;">自带了很多组件，包括各种</span><span style="color:#333333;">agent</span><span style="color:#333333;">（</span><span style="color:#333333;">file</span><span style="color:#333333;">，</span><span style="color:#333333;">
 syslog</span><span style="color:#333333;">等），</span><span style="color:#333333;">collector</span><span style="color:#333333;">和</span><span style="color:#333333;">storage</span><span style="color:#333333;">（</span><span style="color:#333333;">file</span><span style="color:#333333;">，</span><span style="color:#333333;">HDFS</span><span style="color:#333333;">等）。（这里看下面的</span><span style="color:#333333;">Flume</span><span style="color:#333333;">架构图你就明白了）</span></p>
<h2># Flume架构</h2>
<h4># Flume基础架构，如下图：</h4>
<p style="background:#FFFFFF;">  <img src="https://img-blog.csdn.net/20140625114928093?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjE4NTI5Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>这是一个flume-ng 最简单的图。flume-ng 是由一个个agent组成的。一个agent就像一个细胞一样。</p>
<h4># Flume的多agent架构，如下图：</h4>
<p>    <img src="https://img-blog.csdn.net/20140625114937015?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjE4NTI5Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>   上面是两个agent链接在一起的，再看看更多的......</p>
<h4># Flume的合并（合作）架构，如下图：</h4>
<div><img src="https://img-blog.csdn.net/20140625114948406?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjE4NTI5Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<p style="background:#FFFFFF;"></p>
<p style="background:#FFFFFF;">你是不是觉得这种设计是不是吊炸天了，可以随意组合，跟搭积木一样。跟Storm的设计思想是差不多的，何止吊炸天啊，简直就是吊炸天 、、、</p>
<h4># Flume的多路复用架构，如下图：</h4>
<p>      <img src="https://img-blog.csdn.net/20140625115000328?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjE4NTI5Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<h4># agent的构造</h4>
<p style="background:#FFFFFF;">每个agent里都有三部分构成：source、channel和sink。</p>
<p>就相当于source接收数据，通过channel传输数据，sink把数据写到下一端。这就完了，就这么简单。其中source有很多种可以选择，channel有很多种可以选择，sink也同样有多种可以选择，并且都支持自定义。饿靠！太灵活了。想怎么玩就怎么玩，这你妹的！</p>
<p>同时，如上上图所示，agent还支持选择器，就是一个source支持多个channel和多个sink，这样就完成了数据的分发。</p>
<p>这就完了，flume-ng就这么简单........</p>
<p>从看到最后用，一天足可以搞定。剩下的就是怎么组织你的agent的问题了。也就是搭积木的过程......</p>
<p>另外有一点需要强调的是，flume-ng提供了一种特殊的启动方式（不同于agent），那就是client启动。cilent是一个特殊的agent, 他的source是文件，channel是内存，sink是arvo。实际上是为了方便大家用，直接来传递文件的。具体可以看看官方使用手册。</p>
<p>估计到这儿，应该对flume-ng有了解了吧 、、、on my god、、、</p>
<br><h2># Flume的安装</h2>
<h3># 下载 flume（使用wget下载）</h3>
<p><span style="background:#D9D9D9;">[root@rs229 flume]# wget -c -P /root http://mirrors.cnnic.cn/apache/flume/1.5.0/apache-flume-1.5.0-bin.tar.gz</span></p>
<p># 安装</p>
<p><span style="background:#D9D9D9;">root@rs229 flume]# pwd</span></p>
<p><span style="background:#D9D9D9;">/usr/local/adsit/yting/apache/flume</span></p>
<p><span style="background:#D9D9D9;">[root@rs229 flume]# ll</span></p>
<p><span style="background:#D9D9D9;">total 4</span></p>
<p><span style="background:#D9D9D9;">drwxr-xr-x 3 root root 4096 Jun 24 17:25mirrors.cnnic.cn</span></p>
<p><span style="background:#D9D9D9;">[root@rs229 flume]# cpmirrors.cnnic.cn/apache/flume/1.5.0/apache-flume-1.5.0-bin.tar.gz .</span></p>
<p><span style="background:#D9D9D9;">[root@rs229 flume]# ll</span></p>
<p><span style="background:#D9D9D9;">total 25276</span></p>
<p><span style="background:#D9D9D9;">-rw-r--r-- 1 root root 25876246 Jun 24 17:27apache-flume-1.5.0-bin.tar.gz</span></p>
<p><span style="background:#D9D9D9;">drwxr-xr-x 3 root root     4096 Jun 24 17:25 mirrors.cnnic.cn</span></p>
<p><span style="background:#D9D9D9;">[root@rs229 flume]# tar -zxvfapache-flume-1.5.0-bin.tar.gz</span></p>
<p><span style="background:#D9D9D9;"> </span></p>
<p><span style="background:#D9D9D9;">[root@rs229 flume]# ll</span></p>
<p><span style="background:#D9D9D9;">total 25280</span></p>
<p><span style="background:#D9D9D9;">drwxr-xr-x 7 root root     4096 Jun 24 17:27 apache-flume-1.5.0-bin</span></p>
<p><span style="background:#D9D9D9;">-rw-r--r-- 1 root root 25876246 Jun 24 17:27apache-flume-1.5.0-bin.tar.gz</span></p>
<p><span style="background:#D9D9D9;">drwxr-xr-x 3 root root     4096 Jun 24 17:25 mirrors.cnnic.cn</span></p>
<p><span style="background:#D9D9D9;">[root@rs229 flume]# rm -rfapache-flume-1.5.0-bin.tar.gz</span></p>
<p><span style="background:#D9D9D9;">[root@rs229 flume]# rm -rf mirrors.cnnic.cn/</span></p>
<p><span style="background:#D9D9D9;">[root@rs229 flume]# ll</span></p>
<p><span style="background:#D9D9D9;">total 4</span></p>
<p><span style="background:#D9D9D9;">drwxr-xr-x 7 root root 4096 Jun 24 17:27apache-flume-1.5.0-bin</span></p>
<p><span style="background:#D9D9D9;">[root@rs229 flume]#</span></p>
<h3># 修改 flume-env.sh 配置文件</h3>
<p><span style="background:#D9D9D9;">[root@rs229 conf]# pwd</span></p>
<p><span style="background:#D9D9D9;">/usr/local/adsit/yting/apache/flume/apache-flume-1.5.0-bin/conf</span></p>
<p><span style="background:#D9D9D9;">[root@rs229 conf]# ll</span></p>
<p><span style="background:#D9D9D9;">total 12</span></p>
<p><span style="background:#D9D9D9;">-rw-r--r-- 1 501 games 1661 Mar 29 06:15flume-conf.properties.template</span></p>
<p><span style="background:#D9D9D9;">-rw-r--r-- 1 501 games 1197 Mar 29 06:15 flume-env.sh.template</span></p>
<p><span style="background:#D9D9D9;">-rw-r--r-- 1 501 games 3063 Mar 29 06:15log4j.properties</span></p>
<p><span style="background:#D9D9D9;">[root@rs229 conf]# cp flume-env.sh.templateflume-env.sh</span></p>
<p><span style="background:#D9D9D9;">[root@rs229 conf]# vi flume-env.sh</span></p>
<p><span style="background:#D9D9D9;"> </span></p>
<p># Enviroment variables can be sethere.</p>
<p>JAVA_HOME=/usr/local/adsit/yting/jdk/jdk1.7.0_60</p>
<h3># 修改 flume-site.xml 配置文件（貌似没有该步骤，貌似也可以修改，研究后再来弄吧！）</h3>
<h3># 验证 flume是否安装成功</h3>
<p><span style="background:#D9D9D9;">[root@rs229 conf]# ../bin/flume-ng version</span></p>
<p><span style="background:#D9D9D9;">Flume 1.5.0</span></p>
<p><span style="background:#D9D9D9;">Source code repository:https://git-wip-us.apache.org/repos/asf/flume.git</span></p>
<p><span style="background:#D9D9D9;">Revision: 8633220df808c4cd0c13d1cf0320454a94f1ea97</span></p>
<p><span style="background:#D9D9D9;">Compiled by hshreedharan on Wed May  7 14:49:18 PDT 2014</span></p>
<p><span style="background:#D9D9D9;">From source with checksuma01fe726e4380ba0c9f7a7d222db961f</span></p>
<p><span style="color:#FF0000;">出现这样的信息表示安装成功了</span></p>
<h2># Flume 入门案例</h2>
<h3># Flume监控指定目录下的日志信息，并将日志信息上传到HDFS中去</h3>
<h3># 在conf目录下新建example.conf配置文件</h3>
<p><span style="color:#FF0000;">新建文件：</span>在conf目录下新建一个example.conf文件（随便起什么名字），当然随便哪里都行</p>
<p><span style="color:#FF0000;">注意：</span>文件名最好跟配置中的名字一样，比如里面的agent1最好跟外面的文件名一样，<span style="color:#FF0000;">见名知意</span></p>
<p><span style="background:#D9D9D9;">[root@rs229 conf]# pwd</span></p>
<p><span style="background:#D9D9D9;">/usr/local/adsit/yting/apache/flume/apache-flume-1.5.0-bin/conf</span></p>
<p><span style="background:#D9D9D9;">[root@rs229 apache-flume-1.5.0-bin]# catconf/example.conf</span></p>
<p><span style="background:#D9D9D9;"># agent1 : yting first flume example </span></p>
<p><span style="background:#D9D9D9;">agent1.sources=source1</span></p>
<p><span style="background:#D9D9D9;">agent1.sinks=sink1</span></p>
<p><span style="background:#D9D9D9;">agent1.channels=channel1</span></p>
<p><span style="background:#D9D9D9;"> </span></p>
<p><span style="background:#D9D9D9;"># configure source1</span></p>
<p><span style="background:#D9D9D9;">agent1.sources.source1.type=spooldir</span></p>
<p><span style="background:#D9D9D9;">agent1.sources.source1.spoolDir=<span style="color:#FF0000;">/usr/local/yting/flume/tdata/tdir1</span></span></p>
<p><span style="background:#D9D9D9;">agent1.sources.source1.channels=channel1</span></p>
<p><span style="background:#D9D9D9;">agent1.sources.source1.fileHeader = false</span></p>
<p><span style="background:#D9D9D9;"> </span></p>
<p><span style="background:#D9D9D9;"># configure sink1</span></p>
<p><span style="background:#D9D9D9;">agent1.sinks.sink1.type=hdfs</span></p>
<p><span style="background:#D9D9D9;">agent1.sinks.sink1.hdfs.path=<span style="color:#FF0000;">hdfs://rs229:9000/yting/flumet</span></span></p>
<p><span style="background:#D9D9D9;">agent1.sinks.sink1.hdfs.fileType=DataStream</span></p>
<p><span style="background:#D9D9D9;">agent1.sinks.sink1.hdfs.writeFormat=TEXT</span></p>
<p><span style="background:#D9D9D9;">agent1.sinks.sink1.hdfs.rollInterval=4</span></p>
<p><span style="background:#D9D9D9;">agent1.sinks.sink1.channel=channel1</span></p>
<p><span style="background:#D9D9D9;"> </span></p>
<p><span style="background:#D9D9D9;"># configure channel1</span></p>
<p><span style="background:#D9D9D9;">agent1.channels.channel1.type=file</span></p>
<p><span style="background:#D9D9D9;">agent1.channels.channel1.checkpointDir=<span style="color:#FF0000;">/usr/local/yting/flume/checkpointdir/tcpdir/example_agent1_001</span></span></p>
<p><span style="background:#D9D9D9;">agent1.channels.channel1.dataDirs=<span style="color:#FF0000;">/usr/local/yting/flume/datadirs/tddirs/example_agent1_001</span></span></p>
<p><span style="color:#FF0000;">注意：</span>红色字体部分自己修改成自己对应的目录了</p>
<h3># 运行Flume 使用example.conf</h3>
<h4>#命令参数说明</h4>
<p><span style="color:#FF0000;">-c </span><span style="color:#333333;">conf </span><span style="color:#333333;">指定配置目录为</span><span style="color:#333333;">conf</span></p>
<p><span style="color:#FF0000;">-f</span><span style="color:#333333;"> conf/example.conf </span><span style="color:#333333;">指定配置文件为</span><span style="color:#333333;">conf/example.conf</span></p>
<p><span style="color:#FF0000;">-n</span><span style="color:#333333;"> agent1 </span><span style="color:#333333;">指定</span><span style="color:#333333;">agent</span><span style="color:#333333;">名字为</span><span style="color:#333333;">agent1,</span><span style="color:#333333;">需要与</span><span style="color:#333333;">example.conf</span><span style="color:#333333;">中的一致（这里不一致，可能会一直停在那里，请参考笔记中后面的错误全集Flume</span>部分，那里介绍了错误的分析，原因，解决）</p>
<p><span style="color:#FF0000;">-Dflume.root.logger=INFO,console</span><span style="color:#333333;"> </span><span style="color:#333333;">指定</span><span style="color:#333333;">DEBUF</span><span style="color:#333333;">模式在</span><span style="color:#333333;">console</span><span style="color:#333333;">输出</span><span style="color:#333333;">INFO</span><span style="color:#333333;">信息</span></p>
<p><span style="color:#333333;"><br></span></p>
<p><span style="background-color:rgb(204,204,204);">[root@rs229conf]# ./bin/flume-ng agent -c conf/ -f conf/example.conf<span style="color:#FF0000;">-n agent1</span>-Dflume.root.logger=INFO,console</span></p>
<p><span style="background-color:rgb(204,204,204);">-bash:./bin/flume-ng: No such file or directory</span></p>
<p><span style="background-color:rgb(204,204,204);">[root@rs229conf]# cd ..</span></p>
<p><span style="background-color:rgb(204,204,204);">[root@rs229apache-flume-1.5.0-bin]# ./bin/flume-ng agent -c conf/ -f conf/example.conf -nagent1 -Dflume.root.logger=INFO,console</span></p>
<p><span style="background-color:rgb(204,204,204);">Info: Sourcingenvironment configuration script/usr/local/adsit/yting/apache/flume/apache-flume-1.5.0-bin/conf/flume-env.sh</span></p>
<p><span style="background-color:rgb(204,204,204);">Info: IncludingHadoop libraries found via(/usr/local/adsit/yting/apache/hadoop/hadoop-2.2.0/bin/hadoop) for HDFS access</span></p>
<p><span style="background-color:rgb(204,204,204);">Info: Excluding/usr/local/adsit/yting/apache/hadoop/hadoop-2.2.0/share/hadoop/common/lib/slf4j-api-1.7.5.jarfrom classpath</span></p>
<p><span style="background-color:rgb(204,204,204);">Info: Excluding/usr/local/adsit/yting/apache/hadoop/hadoop-2.2.0/share/hadoop/common/lib/slf4j-log4j12-1.7.5.jarfrom classpath</span></p>
<p><span style="background-color:rgb(204,204,204);">Info: IncludingHBASE libraries found via(/usr/local/adsit/yting/apache/hbase/hbase-0.96.2-hadoop2/bin/hbase) for HBASEaccess</span></p>
<p><span style="background-color:rgb(204,204,204);">Info: Excluding/usr/local/adsit/yting/apache/hbase/hbase-0.96.2-hadoop2/bin/../lib/slf4j-api-1.6.4.jarfrom classpath</span></p>
<p><span style="background-color:rgb(204,204,204);">Info: Excluding/usr/local/adsit/yting/apache/hbase/hbase-0.96.2-hadoop2/bin/../lib/slf4j-log4j12-1.6.4.jarfrom classpath</span></p>
<p><span style="background-color:rgb(204,204,204);">Info: Excluding/usr/local/adsit/yting/apache/hadoop/hadoop-2.2.0/share/hadoop/common/lib/slf4j-api-1.7.5.jarfrom classpath</span></p>
<p><span style="background-color:rgb(204,204,204);">Info: Excluding/usr/local/adsit/yting/apache/hadoop/hadoop-2.2.0/share/hadoop/common/lib/slf4j-log4j12-1.7.5.jarfrom classpath</span></p>
<p><span style="background-color:rgb(204,204,204);">….capacity-scheduler/*.jar:/conf'-Djava.library.path=:/usr/local/adsit/yting/apache/hadoop/hadoop-2.2.0/lib:/usr/local/adsit/yting/apache/hadoop/hadoop-2.2.0//liborg.apache.flume.node.Application -f conf/example.conf
 -n agent1</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:45,763 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.node.PollingPropertiesFileConfigurationProvider.start(PollingPropertiesFileConfigurationProvider.java:61)]Configuration provider starting</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:45,772 (conf-file-poller-0) [INFO -org.apache.flume.node.PollingPropertiesFileConfigurationProvider$FileWatcherRunnable.run(PollingPropertiesFileConfigurationProvider.java:133)]Reloading configuration
 file:conf/example.conf</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:45,781 (conf-file-poller-0) [INFO -org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:1016)]Processing:sink1</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:45,783 (conf-file-poller-0) [INFO -org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:1016)]Processing:sink1</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:45,783 (conf-file-poller-0) [INFO -org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:930)]Added sinks: sink1 Agent: agent1</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:45,783 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:1016)]Processing:sink1</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:45,783 (conf-file-poller-0) [INFO -org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:1016)]Processing:sink1</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:45,783 (conf-file-poller-0) [INFO -org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:1016)]Processing:sink1</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:45,784 (conf-file-poller-0) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:1016)]Processing:sink1</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:45,809 (conf-file-poller-0) [INFO -org.apache.flume.conf.FlumeConfiguration.validateConfiguration(FlumeConfiguration.java:140)]Post-validation flume configuration contains configuration for
 agents: [agent1]</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:45,809 (conf-file-poller-0) [INFO -org.apache.flume.node.AbstractConfigurationProvider.loadChannels(AbstractConfigurationProvider.java:150)]Creating channels</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:45,823 (conf-file-poller-0) [INFO -org.apache.flume.channel.DefaultChannelFactory.create(DefaultChannelFactory.java:40)]Creating instance of channel channel1 type file</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:45,828 (conf-file-poller-0) [INFO - org.apache.flume.node.AbstractConfigurationProvider.loadChannels(AbstractConfigurationProvider.java:205)]Created channel channel1</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:45,829 (conf-file-poller-0) [INFO -org.apache.flume.source.DefaultSourceFactory.create(DefaultSourceFactory.java:39)]Creating instance of source source1, type spooldir</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:45,844 (conf-file-poller-0) [INFO -org.apache.flume.sink.DefaultSinkFactory.create(DefaultSinkFactory.java:40)]Creating instance of sink: sink1, type: hdfs</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:46,293 (conf-file-poller-0) [WARN -org.apache.hadoop.util.NativeCodeLoader.&lt;clinit&gt;(NativeCodeLoader.java:62)]Unable to load native-hadoop library for your platform... using builtin-javaclasses
 where applicable</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:46,572 (conf-file-poller-0) [INFO - org.apache.flume.sink.hdfs.HDFSEventSink.authenticate(HDFSEventSink.java:555)]Hadoop Security enabled: false</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:46,576 (conf-file-poller-0) [INFO -org.apache.flume.node.AbstractConfigurationProvider.getConfiguration(AbstractConfigurationProvider.java:119)]Channel channel1 connected to [source1, sink1]</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:46,587 (conf-file-poller-0) [INFO -org.apache.flume.node.Application.startAllComponents(Application.java:138)]Starting new configuration:{ sourceRunners:{source1=EventDrivenSourceRunner: {source:Spool
 Directory source source1: { spoolDir:/usr/local/yting/flume/tdata/tdir1 } }} sinkRunners:{sink1=SinkRunner: {policy:org.apache.flume.sink.DefaultSinkProcessor@7205c140 counterGroup:{name:null counters:{} } }} channels:{channel1=FileChannel channel1 { dataDirs:[/usr/local/yting/flume/datadirs/tddirs/example_agent1_001]
 }} }</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:46,593 (conf-file-poller-0) [INFO -org.apache.flume.node.Application.startAllComponents(Application.java:145)]Starting Channel channel1</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:46,593 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.FileChannel.start(FileChannel.java:259)] StartingFileChannel channel1 { dataDirs:[/usr/local/yting/flume/datadirs/tddirs/example_agent1_001]
 }...</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:46,617 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.Log.&lt;init&gt;(Log.java:328)] Encryption is notenabled</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:46,618 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.Log.replay(Log.java:373)] Replay started</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:46,620 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.Log.replay(Log.java:385)] Found NextFileID 0,from []</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:46,661 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.EventQueueBackingStoreFile.&lt;init&gt;(EventQueueBackingStoreFile.java:91)]Preallocated/usr/local/yting/flume/checkpointdir/tcpdir/example_agent1_001/checkpoint
 to8008232 for capacity 1000000</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:46,663 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.EventQueueBackingStoreFileV3.&lt;init&gt;(EventQueueBackingStoreFileV3.java:53)]Starting up with/usr/local/yting/flume/checkpointdir/tcpdir/example_agent1_001/checkpoint
 and/usr/local/yting/flume/checkpointdir/tcpdir/example_agent1_001/checkpoint.meta</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,095 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.FlumeEventQueue.&lt;init&gt;(FlumeEventQueue.java:114)]QueueSet population inserting 0 took 0</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,100 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.Log.replay(Log.java:423)] Last Checkpoint Wed Jun25 10:37:46 CST 2014, queue depth = 0</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,105 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.Log.doReplay(Log.java:507)] Replaying logs withv2 replay logic</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,109 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.ReplayHandler.replayLog(ReplayHandler.java:249)]Starting replay of []</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,109 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.ReplayHandler.replayLog(ReplayHandler.java:346)]read: 0, put: 0, take: 0, rollback: 0, commit: 0, skip: 0, eventCount:0</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,110 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.FlumeEventQueue.replayComplete(FlumeEventQueue.java:407)]Search Count = 0, Search Time = 0, Copy Count = 0, Copy Time =
 0</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,119 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.Log.replay(Log.java:470)] Rolling/usr/local/yting/flume/datadirs/tddirs/example_agent1_001</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,120 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.Log.roll(Log.java:932)] Roll start/usr/local/yting/flume/datadirs/tddirs/example_agent1_001</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,137 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.tools.DirectMemoryUtils.getDefaultDirectMemorySize(DirectMemoryUtils.java:113)]Unable to get maxDirectMemory from VM: NoSuchMethodException:sun.misc.VM.maxDirectMemory(null)</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,140 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.tools.DirectMemoryUtils.allocate(DirectMemoryUtils.java:47)]Direct Memory Allocation:  Allocation =1048576, Allocated = 0, MaxDirectMemorySize
 = 18874368, Remaining = 18874368</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,195 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.channel.file.LogFile$Writer.&lt;init&gt;(LogFile.java:214)]Opened /usr/local/yting/flume/datadirs/tddirs/example_agent1_001/log-1</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,208 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.Log.roll(Log.java:948)] Roll end</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,208 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.EventQueueBackingStoreFile.beginCheckpoint(EventQueueBackingStoreFile.java:214)]Start checkpoint for/usr/local/yting/flume/checkpointdir/tcpdir/example_agent1_001/checkpoint,elements
 to sync = 0</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,211 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.EventQueueBackingStoreFile.checkpoint(EventQueueBackingStoreFile.java:239)]Updating checkpoint metadata: logWriteOrderID:
 1403663867120, queueSize: 0,queueHead: 0</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,235 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.Log.writeCheckpoint(Log.java:1005)] Updatedcheckpoint for file:/usr/local/yting/flume/datadirs/tddirs/example_agent1_001/log-1
 position: 0logWriteOrderID: 1403663867120</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,235 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.channel.file.FileChannel.start(FileChannel.java:285)] QueueSize after replay: 0 [channel=channel1]</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,296 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.register(MonitoredCounterGroup.java:119)]Monitored counter group for type: CHANNEL, name: channel1:
 Successfullyregistered new MBean.</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,296 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.start(MonitoredCounterGroup.java:95)]Component type: CHANNEL, name: channel1 started</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,297 (conf-file-poller-0) [INFO -org.apache.flume.node.Application.startAllComponents(Application.java:173)] StartingSink sink1</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,297 (conf-file-poller-0) [INFO -org.apache.flume.node.Application.startAllComponents(Application.java:184)]Starting Source source1</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,298 (lifecycleSupervisor-1-0) [INFO - org.apache.flume.source.SpoolDirectorySource.start(SpoolDirectorySource.java:77)]SpoolDirectorySource source starting with directory:/usr/local/yting/flume/tdata/tdir1</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,300 (lifecycleSupervisor-1-1) [INFO -org.apache.flume.instrumentation.MonitoredCounterGroup.register(MonitoredCounterGroup.java:119)]Monitored counter group for type: SINK, name: sink1: Successfully
 registerednew MBean.</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,300 (lifecycleSupervisor-1-1) [INFO -org.apache.flume.instrumentation.MonitoredCounterGroup.start(MonitoredCounterGroup.java:95)]Component type: SINK, name: sink1 started</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,330 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.instrumentation.MonitoredCounterGroup.register(MonitoredCounterGroup.java:119)]Monitored counter group for type: SOURCE, name: source1:
 Successfullyregistered new MBean.</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,330 (lifecycleSupervisor-1-0) [INFO -org.apache.flume.instrumentation.MonitoredCounterGroup.start(MonitoredCounterGroup.java:95)]Component type: SOURCE, name: source1 started</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,331 (pool-6-thread-1) [INFO -org.apache.flume.source.SpoolDirectorySource$SpoolDirectoryRunnable.run(SpoolDirectorySource.java:254)]Spooling Directory Source runner has shutdown.</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:47,831 (pool-6-thread-1) [INFO - org.apache.flume.source.SpoolDirectorySource$SpoolDirectoryRunnable.run(SpoolDirectorySource.java:254)]Spooling Directory Source runner has shutdown.</span></p>
<p><span style="background-color:rgb(204,204,204);">2014-06-2510:37:48,332 (pool-6-thread-1) [INFO -org.apache.flume.source.SpoolDirectorySource$SpoolDirectoryRunnable.run(SpoolDirectorySource.java:254)]Spooling Directory Source runner has shutdown.</span></p>
<p><span style="color:#FF0000;">到了这里说明你的程序运行正常了，但是你的监视目录</span><span style="color:#FF0000;"> /usr/local/yting/flume/tdata/tdir1</span><span style="color:#FF0000;">下没有新文件的产生，所以会一直出现上面的那条信息</span></p>
<h3># 在/usr/local/yting/flume/tdata/tdir1这个flume监视目录下添加一个新文件<span style="color:#FF0000;">yting_flume_example_agent1_00001.log</span></h3>
<p><span style="background:#D9D9D9;">[root@rs229 hadoop-2.2.0]# ./bin/hadoop fs -ls /yting</span></p>
<p><span style="background:#D9D9D9;">14/06/25 10:48:00 WARN util.NativeCodeLoader: Unableto load native-hadoop library for your platform... using builtin-java classeswhere applicable</span></p>
<p><span style="background:#D9D9D9;">Found 1 items</span></p>
<p><span style="background:#D9D9D9;">-rw-r--r--   3root supergroup       4278 2014-06-1018:29 /yting/yarn-daemon.sh</span></p>
<p><span style="background:#D9D9D9;">[root@rs229 tdir1]# ll</span></p>
<p><span style="background:#D9D9D9;">total 0</span></p>
<p><span style="background:#D9D9D9;">[root@rs229 tdir1]# ll -a</span></p>
<p><span style="background:#D9D9D9;">total 12</span></p>
<p><span style="background:#D9D9D9;">drwxr-xr-x 3 root root 4096 Jun 25 10:37 .</span></p>
<p><span style="background:#D9D9D9;">drwxr-xr-x 3 root root 4096 Jun 24 22:25 ..</span></p>
<p><span style="background:#D9D9D9;">drwxr-xr-x 2 root root 4096 Jun 25 09:48 .flumespool</span><span style="background:#D9D9D9;">（隐藏文件）</span></p>
<p><span style="background:#D9D9D9;">[root@rs229 tdir1]# viyting_flume_example_agent1_00001.log</span></p>
<p><span style="background:#D9D9D9;">The you smile until forever .....................</span></p>
<p><span style="background:#D9D9D9;">[root@rs229 tdir1]# ll</span></p>
<p><span style="background:#D9D9D9;">total 4</span></p>
<p><span style="background:#D9D9D9;">-rw-r--r-- 1 root root 50 Jun 25 10:51 <a name="OLE_LINK122">
</a><a name="OLE_LINK121">yting_flume_example_agent1_00001.log.COMPLETED</a></span></p>
<h3># 文件名变成.COMPLETED结尾</h3>
<p><span style="background:#D9D9D9;">说明该文件</span><span style="background:#D9D9D9;">yting_flume_example_agent1_00001.log</span><span style="background:#D9D9D9;">已经被</span><span style="background:#D9D9D9;">flume</span><span style="background:#D9D9D9;">处理了，处理过后的文件名变成</span><span style="background:#D9D9D9;">yting_flume_example_agent1_00001.log.COMPLETED,</span><span style="background:#D9D9D9;">接下来看看</span><span style="background:#D9D9D9;">flume</span><span style="background:#D9D9D9;">那边的信息，应该发生变化了</span></p>
<h3># 查看flume shell的信息变化</h3>
<p><span style="background:#D9D9D9;">2014-06-25 10:51:00,530 (pool-6-thread-1) [INFO -org.apache.flume.source.SpoolDirectorySource$SpoolDirectoryRunnable.run(SpoolDirectorySource.java:254)]Spooling Directory Source runner has shutdown.</span></p>
<p><span style="background:#D9D9D9;">2014-06-25 10:51:01,434 (pool-6-thread-1) [INFO -org.apache.flume.client.avro.ReliableSpoolingFileEventReader.rollCurrentFile(ReliableSpoolingFileEventReader.java:332)]<span style="color:#FF0000;">Preparing to move file/usr/local/yting/flume/tdata/tdir1/yting_flume_example_agent1_00001.log
 to/usr/local/yting/flume/tdata/tdir1/yting_flume_example_agent1_00001.log.COMPLETED</span></span></p>
<p><span style="background:#D9D9D9;">2014-06-25 10:51:02,436 (pool-6-thread-1) [INFO -org.apache.flume.source.SpoolDirectorySource$SpoolDirectoryRunnable.run(SpoolDirectorySource.java:254)]Spooling Directory Source runner has shutdown.</span></p>
<p><span style="background:#D9D9D9;">2014-06-25 10:51:02,473(SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO -org.apache.flume.sink.hdfs.BucketWriter.open(BucketWriter.java:261)]<span style="color:#FF0000;">Creatinghdfs://rs229:9000/yting/flumet/FlumeData.1403664662360.tmp</span></span></p>
<p><span style="background:#D9D9D9;">2014-06-25 10:51:07,440 (pool-6-thread-1) [INFO -org.apache.flume.source.SpoolDirectorySource$SpoolDirectoryRunnable.run(SpoolDirectorySource.java:254)]Spooling Directory Source runner has shutdown.</span></p>
<p><span style="background:#D9D9D9;">2014-06-25 10:51:07,519 (hdfs-sink1-roll-timer-0)[INFO - org.apache.flume.sink.hdfs.BucketWriter.close(BucketWriter.java:409)]<span style="color:#FF0000;">Closinghdfs://rs229:9000/yting/flumet/FlumeData.1403664662360.tmp</span></span></p>
<p><span style="background:#D9D9D9;">2014-06-25 10:51:07,521 (hdfs-sink1-call-runner-3)[INFO - org.apache.flume.sink.hdfs.BucketWriter$3.call(BucketWriter.java:339)]<span style="color:#FF0000;">Close tries incremented</span></span></p>
<p><span style="background:#D9D9D9;">2014-06-25 10:51:07,549 (hdfs-sink1-call-runner-4)[INFO - org.apache.flume.sink.hdfs.BucketWriter$8.call(BucketWriter.java:669)]<span style="color:#FF0000;">Renaminghdfs://rs229:9000/yting/flumet/FlumeData.1403664662360.tmp tohdfs://rs229:9000/yting/flumet/FlumeData.1403664662360</span></span></p>
<p><span style="background:#D9D9D9;">2014-06-25 10:51:07,557 (hdfs-sink1-roll-timer-0)[INFO - org.apache.flume.sink.hdfs.HDFSEventSink$1.run(HDFSEventSink.java:402)]<span style="color:#FF0000;">Writer callback called.</span></span></p>
<p><span style="background:#D9D9D9;">2014-06-25 10:51:16,448 (pool-6-thread-1) [INFO -org.apache.flume.source.SpoolDirectorySource$SpoolDirectoryRunnable.run(SpoolDirectorySource.java:254)]Spooling Directory Source runner has shutdown.</span></p>
<p><span style="background:#D9D9D9;">2014-06-25 10:51:16,626 (Log-BackgroundWorker-channel1)[INFO -org.apache.flume.channel.file.EventQueueBackingStoreFile.beginCheckpoint(EventQueueBackingStoreFile.java:214)]Start checkpoint for/usr/local/yting/flume/checkpointdir/tcpdir/example_agent1_001/checkpoint,elements
 to sync = 1</span></p>
<p><span style="background:#D9D9D9;">2014-06-25 10:51:16,628(Log-BackgroundWorker-channel1) [INFO -org.apache.flume.channel.file.EventQueueBackingStoreFile.checkpoint(EventQueueBackingStoreFile.java:239)]Updating checkpoint metadata: logWriteOrderID: 1403663867125,
 queueSize: 0,queueHead: 0</span></p>
<p><span style="background:#D9D9D9;">2014-06-25 10:51:16,630(Log-BackgroundWorker-channel1) [INFO -org.apache.flume.channel.file.Log.writeCheckpoint(Log.java:1005)] Updatedcheckpoint for file:/usr/local/yting/flume/datadirs/tddirs/example_agent1_001/log-1 position:
 206logWriteOrderID: 1403663867125</span></p>
<p><span style="color:#FF0000;">注意：</span>这里的分析请看下面的分析整个过程</p>
<h3># 查看hdfs上flume是否上传了数据</h3>
<p><span style="background:#D9D9D9;">[root@rs229 tdir1]# hadoop fs -ls /yting</span></p>
<p><span style="background:#D9D9D9;">Found 2 items</span></p>
<p><span style="background:#D9D9D9;">drwxr-xr-x   -root supergroup          0 2014-06-2510:51 /yting/flumet</span></p>
<p><span style="background:#D9D9D9;">-rw-r--r--   3root supergroup       4278 2014-06-1018:29 /yting/yarn-daemon.sh</span></p>
<p><span style="background:#D9D9D9;">[root@rs229 tdir1]# hadoop fs -ls /yting/flumet</span></p>
<p><span style="background:#D9D9D9;">Found 1 items</span></p>
<p><span style="background:#D9D9D9;">-rw-r--r--   3root supergroup         50 2014-06-2510:51 /yting/flumet/FlumeData.1403664662360</span></p>
<p><span style="background:#D9D9D9;">[root@rs229 tdir1]# hadoop fs -cat /yting/flumet/FlumeData.1403664662360</span></p>
<p><span style="background:#D9D9D9;">The you smile until forever .....................</span><span style="background:#D9D9D9;">（日志信息以及被上传了，</span><span style="background:#D9D9D9;">OK</span><span style="background:#D9D9D9;">、、、）</span></p>
<p><span style="background:#D9D9D9;">[root@rs229 tdir1]#</span></p>
<p><span style="background:#D9D9D9;"><img src="https://img-blog.csdn.net/20140625115132718?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjE4NTI5Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<h3># 分析整个过程</h3>
<p>通过分析<span style="color:#FF0000;">flume shell</span><span style="color:#FF0000;">的日志信息</span>可以发现当我们在监视目录下新文件被创建保存的时候flume进行处理并且重命名该文件，在原文件命后面添加<span style="color:#FF0000;">.COMPLETE</span>,然后将文件中的数据上传到hdfs中并创建一个临时文件<span style="color:#FF0000;">filename.tmp</span>,上传成功后重命名hdfs上的临时文件，将文件后缀<span style="color:#FF0000;">.tmp</span>去掉就ok了，最后flume将本次操作写入自己的日志信息。</p>
<h3># 初学者注意的地方</h3>
<p># 配置文件的文件名命</p>
<p># 配置文件中的agent1与flume-ng 的-n 参数一直</p>
<p># 最好配置文件的文件名与配置文件内容的名字一样，这样-n参数就不会敲错了</p>
<p><strong><span style="font-size:14px;color:#ff0000;"># 最后的最后想吐槽一下csdn的这个编辑器，真心烂，自己写好的笔记放在word文档里面，复制过来的时候会出现些莫名其妙的东西，改都改半天 ,靠 、、、</span></strong></p>
<p align="right"><span style="background-color:rgb(102,255,255);"><span style="color:#FF0000;">时间：</span><span style="color:#000099;">2014-06-25 11:08:21</span></span></p>
<br>            </div>
                </div>