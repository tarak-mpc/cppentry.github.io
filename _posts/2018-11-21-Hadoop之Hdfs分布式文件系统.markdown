---
layout:     post
title:      Hadoop之Hdfs分布式文件系统
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 style="font-family:Arial;">
<span style="font-size:18px;">hdfs<span class="s1">介绍</span></span></h1>
<p class="p3" style="font-family:Arial;font-size:14px;">
<span class="s2"><a href="http://lib.csdn.net/base/hadoop" rel="nofollow" class="replace_word" title="Hadoop知识库" style="color:rgb(223,52,52);font-weight:bold;">Hadoop</a></span>的分布式文件系统<span class="s2">hdfs</span>是依据谷歌的<span class="s2">GFS</span>论文来实现的，它是一个被设计成为运行在廉价的通用硬件之上，具有容错性的文件系统。<span class="s2">hdfs</span>的设计使得它能够提供很高的吞吐量，很适合大规模的数据集合。</p>
<p class="p2" style="font-family:Arial;font-size:14px;">
<br></p>
<h1 style="font-family:Arial;">
<span style="font-size:18px;">hdfs<span class="s1"><a href="http://lib.csdn.net/base/architecture" rel="nofollow" class="replace_word" title="大型网站架构知识库" style="color:rgb(223,52,52);font-weight:bold;">架构</a></span></span></h1>
<p class="p3" style="font-family:Arial;font-size:14px;">
<span class="s2">hdfs</span>的设计如下图所示</p>
<p class="p2" style="font-family:Arial;font-size:14px;">
<br><img src="https://img-blog.csdn.net/20170409220134968?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2Fud2VueXVibG9n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;"><br></p>
<p class="p3">
<span style="font-family:'FangSong_GB2312';font-size:14px;"><span class="s2">hdfs</span>由<span class="s2">Namenode</span>和<span class="s2">Datanode</span>组成，<span class="s2">Namenode</span>和<span class="s2">Datanode</span>可以在任何异构的机器上运行。hdfs由<a href="http://lib.csdn.net/base/java" rel="nofollow" class="replace_word" title="Java 知识库" style="color:rgb(223,52,52);font-weight:bold;">Java</a>语言开发，这些机器上都安装jvm虚拟机，所以都可以很轻松的部署<span class="s2">Namenode</span>或<span class="s2">Datanode</span>。比较典型的部署场景是一台机器上只运行一个<span class="s2">Namenode</span>实例，而集群中的其它机器分别运行一个<span class="s2">Datanode</span>实例。这种架构并不排斥在一台机器上运行多个<span class="s2">Datanode</span>，只不过这样的情况比较少见。</span></p>
<p class="p3">
<span style="font-family:'FangSong_GB2312';font-size:14px;">集群中单一<span class="s2">Namenode</span>的结构大大简化了系统的架构。<span class="s2">Namenode</span>是所有<span class="s2">HDFS</span>元数据的仲裁者和管理者，这样，用户数据永远不会流过<span class="s2">Namenode</span>，极大的缓解了中心节点<span class="s2">namenode</span>的压力。</span></p>
<p class="p3">
<span style="font-family:'FangSong_GB2312';font-size:14px;"><span class="s2">hdfs</span>具有高可用的特点，即使一台机器宕机，存在<span class="s2">hdfs</span>里的数据也不会丢失，这就要归功于<span class="s2">hdfs</span>的副本的特点了。<span class="s2">HDFS</span>被设计成能够在一个大集群中跨机器可靠地存储超大文件。它将每个文件存储成一系列的数据块，除了最后一个，所有的数据块都是同样大小的。为了容错，文件的所有数据块都会有副本。每个文件的数据块大小和副本系数都是可配置的。应用程序可以指定某个文件的副本数目。副本系数可以在文件创建的时候指定，也可以在之后改变。</span></p>
<p class="p3">
<span style="font-family:'FangSong_GB2312';font-size:14px;"><span class="s2">Namenode</span>全权管理数据块的复制，它周期性地从集群中的每个<span class="s2">Datanode</span>接收心跳信号和块状态报告<span class="s2">(Blockreport)</span>。接收到心跳信号意味着该<span class="s2">Datanode</span>节点工作正常。块状态报告包含了一个该<span class="s2">Datanode</span>上所有数据块的列表。</span></p>
<p class="p3">
<span style="font-family:'FangSong_GB2312';font-size:14px;">如下图所示</span></p>
<p class="p2" style="font-family:Arial;font-size:14px;">
<img src="https://img-blog.csdn.net/20170409220219132?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2Fud2VueXVibG9n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;"><br></p>
<p class="p2" style="font-family:Arial;font-size:14px;">
<br></p>
<p class="p1" style="font-family:Arial;font-size:14px;">
hdfs<span class="s1">读写</span></p>
<p class="p3" style="font-family:Arial;font-size:14px;">
<span class="s2">hdfs</span>的读文件流程如下</p>
<p class="p2" style="font-family:Arial;font-size:14px;">
<img src="https://img-blog.csdn.net/20170409220330680?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2Fud2VueXVibG9n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;"><br></p>
<p class="p3" style="font-family:Arial;">
<span style="font-size:14px;">客户端首先访问<span class="s2">namenode</span>，得到文件的块的存储位置，然后去<span class="s2">datanode</span>读取对应的数据块，然后再告知<span class="s2">namenode</span>关闭文件。</span></p>
<p class="p3" style="font-family:Arial;">
<span style="font-size:14px;"><span class="s2">hdfs</span>写文件的流程如下</span></p>
<p class="p2" style="font-family:Arial;font-size:14px;">
<img src="https://img-blog.csdn.net/20170409220346009?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2Fud2VueXVibG9n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;"><br></p>
<p class="p3" style="font-family:Arial;">
<span style="font-size:14px;">客户端首先访问<span class="s2">namenode</span>，得到文件的分配到的块所在的<span class="s2">datanode</span>的服务器位置，然后写数据到第一个<span class="s2">datanode</span>，然后<span class="s2">datanode</span>依次向其他副本所在的<span class="s2">datanode</span>写入数据，等待所有的数据都写好了，九返回客户端写入成功，最终告知<span class="s2">namenode</span>关闭客户端。</span></p>
            </div>
                </div>