---
layout:     post
title:      GIS+=地理信息+云计算技术——SPARK for IntelliJ IDEA 开发环境部署
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明： 专注于"GIS+"前沿技术的研究与交流，将云计算技术、大数据技术、容器技术、物联网与GIS进行深度融合，探讨"GIS+"技术和行业解决方案；文章允许转载，但必须以链接方式注明源地址，否则追究法律责任!					https://blog.csdn.net/chinagissoft/article/details/50687171				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p align="center"><span style="font-weight:bold;"><span lang="en-us" style="font-family:'微软雅黑', sans-serif;font-size:18pt;" xml:lang="en-us"></span></span></p>
<p style="text-align:left;font-family:Arial;color:rgb(85,85,85);font-size:14px;line-height:26px;">
</p>
<p style="font-size:14px;color:rgb(85,85,85);line-height:26px;font-family:Arial;">
<span style="font-size:32px;color:rgb(51,204,0);">--------------------------------------------------------------------------------------</span></p>
<p style="font-size:14px;color:rgb(85,85,85);line-height:26px;font-family:Arial;">
</p>
<p style="font-size:14px;color:rgb(85,85,85);line-height:26px;font-family:Arial;">
<span style="font-family:'Microsoft YaHei';"><span style="color:rgb(255,0,0);">版权所有：超图研究所（<a href="http://write.blog.csdn.net/www.supermap.com" rel="nofollow">www.supermap.com</a>)<br></span></span></p>
<p style="font-size:14px;color:rgb(85,85,85);line-height:26px;font-family:Arial;">
<span style="font-family:'Microsoft YaHei';"><span style="color:rgb(255,0,0);">Blog:    <a href="http://blog.csdn.net/chinagissoft" rel="nofollow">http://blog.csdn.net/chinagissoft</a></span></span></p>
<p style="font-size:14px;color:rgb(85,85,85);line-height:26px;font-family:Arial;">
<span style="font-family:'Microsoft YaHei';"><span style="color:rgb(255,0,0);">QQ群：<a href="http://shang.qq.com/wpa/qunwpa?idkey=db34317167632c01ab4750de87c000ae63cf173cf6fcbbd724ae60213272da91" rel="nofollow">16403743</a></span></span></p>
<p style="font-size:14px;color:rgb(85,85,85);font-family:'Microsoft YaHei';line-height:26px;">
<span style="color:rgb(255,0,0);">宗旨：专注于"GIS+"前沿技术的研究与交流，将云计算技术、大数据技术、容器技术、物联网与GIS进行深度融合，探讨"GIS+"技术和行业解决方案</span></p>
<p style="font-size:14px;color:rgb(85,85,85);line-height:26px;font-family:Arial;">
<span style="font-family:'Microsoft YaHei';"><span style="color:rgb(255,0,0);">转载说明：</span></span><span style="color:rgb(255,0,0);font-family:'Microsoft YaHei';">文章允许转载，但必须以链接方式注明源地址，否则追究法律责任!</span></p>
<p style="font-size:14px;color:rgb(85,85,85);line-height:26px;font-family:Arial;">
</p>
<p style="font-size:14px;color:rgb(85,85,85);line-height:26px;font-family:Arial;">
<span style="font-size:32px;color:rgb(51,204,0);">--------------------------------------------------------------------------------------</span></p>
<p style="line-height:26px;">
</p>
<p align="center"><strong><span lang="en-us" style="font-family:'微软雅黑', sans-serif;font-size:18pt;" xml:lang="en-us">SPARK</span><span style="font-family:'微软雅黑', sans-serif;font-size:18pt;">开发环境部署</span></strong></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<h1><span style="line-height:240%;font-size:16pt;"><span style="font-family:'微软雅黑';">前言</span></span></h1>
<span style="font-family:'宋体';font-size:14px;"></span>
<p><span style="font-family:'微软雅黑';"><span lang="en-us" style="font-size:14pt;" xml:lang="en-us"><span>    </span></span><span style="font-size:14pt;">在之前的文章中《<span lang="en-us" xml:lang="en-us">Spark</span>集群部署》中已经在<span lang="en-us" xml:lang="en-us">OpenStack</span>虚拟机环境下部署了由三个虚拟主机组成的<span lang="en-us" xml:lang="en-us">Spark</span>集群。在此基础上支持通过<span lang="en-us" xml:lang="en-us">Spark
 Shell</span>、<span lang="en-us" xml:lang="en-us">Spark Submit</span>、<span lang="en-us" xml:lang="en-us">pyspark</span>等命令执行<span lang="en-us" xml:lang="en-us">Spark</span>自带的示范程序。为了更方便的熟悉<span lang="en-us" xml:lang="en-us">OpenStack</span>的开发，并通过代码调试来测试<span lang="en-us" xml:lang="en-us">Spark</span>的功能，我们在进一步探究<span lang="en-us" xml:lang="en-us">Spark</span>功能之前，首先对<span lang="en-us" xml:lang="en-us">Spark</span>的开发环境进行了部署，下面将部署开发环境的过程做一介绍。</span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<h1><span style="line-height:240%;font-size:16pt;"><span style="font-family:'微软雅黑';">开发环境部署</span></span></h1>
<span style="font-family:'宋体';font-size:14px;"></span>
<p><span style="font-family:'微软雅黑';"><span lang="en-us" style="font-size:16pt;" xml:lang="en-us"><span>   </span></span><span style="font-size:14pt;">以往的<span lang="en-us" xml:lang="en-us">Java</span>开发，我们习惯于在<span lang="en-us" xml:lang="en-us">Windows</span>上开发或者编译，然后把程序编译的工程或者<span lang="en-us" xml:lang="en-us">Jar</span>包拷贝到<span lang="en-us" xml:lang="en-us">Linux</span>主机上，在<span lang="en-us" xml:lang="en-us">Linux</span>环境下部署并运行。因此在初学<span lang="en-us" xml:lang="en-us">Spark</span>开发的时候也考虑采用这种方式，但经过一段时间的实践，虽然也找到了一些在<span lang="en-us" xml:lang="en-us">windows</span>环境下搭建开发环境的方法<span lang="en-us" xml:lang="en-us">(</span>有兴趣可以参考：<span lang="en-us" xml:lang="en-us">http://ju.outofmemory.cn/entry/94851)</span>，但最终还是放弃了，主要原因是为了能更方便部署和调试，毕竟<span lang="en-us" xml:lang="en-us">Windows</span>和<span lang="en-us" xml:lang="en-us">Linux</span>系统的环境配置、文件目录以及各个节点之间的访问都不是很方便，而将最终的环境是建立在之前部署的三个节点的<span lang="en-us" xml:lang="en-us">Spark</span>集群的基础上，额外配置了一个带有图形界面的<span lang="en-us" xml:lang="en-us">Ubuntu</span>虚拟机，作为开发和调试的节点。系统部署结构如下：</span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p align="center" style="text-align:center;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'宋体';font-size:14px;"><img src="https://img-blog.csdn.net/20160218171420797?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p><span style="font-family:'微软雅黑';"><span lang="en-us" style="font-size:14pt;" xml:lang="en-us">Master</span><span style="font-size:14pt;">，<span lang="en-us" xml:lang="en-us">Slave1</span>，<span lang="en-us" xml:lang="en-us">Slave2</span>是在之前博客<u>《<span lang="en-us" xml:lang="en-us">Spark</span>集群部署》</u>介绍过的<span lang="en-us" xml:lang="en-us">Spark</span>集群节点。这三台节点是部署在<span lang="en-us" xml:lang="en-us">OpenStack</span>虚拟机环境中的，物理硬件是高性能服务器。<span lang="en-us" xml:lang="en-us">vmmaster</span>是用于自己开发的虚拟机，部署在<span lang="en-us" xml:lang="en-us">VMware
 10</span>上，物理硬件是个人的笔记本电脑。四个节点都统一使用<span lang="en-us" xml:lang="en-us">Ubuntu</span>，并通过局域网相互链接。</span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p><span style="font-size:14pt;"><span style="font-family:'微软雅黑';">确定了部署结构之后，就可以专注开发环境的搭建了，开发节点<span lang="en-us" xml:lang="en-us">vmmaster</span>上安装的软件主要是<span lang="en-us" xml:lang="en-us">IntelliJ IDEA</span>。</span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p><span style="font-size:14pt;"><span style="font-family:'微软雅黑';">下载地址：<span lang="en-us" xml:lang="en-us"><a href="https://download.jetbrains.com/idea/ideaIU-15.0.3.tar.gz" rel="nofollow"><span style="font-size:16pt;"><span style="color:#0563c1;">https://download.jetbrains.com/idea/ideaIU-15.0.3.tar.gz</span></span></a></span></span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p><span style="font-size:14pt;"><span style="font-family:'微软雅黑';">破解方法网上有很多就不介绍了。</span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p><span style="font-size:14pt;"><span style="font-family:'微软雅黑';">具体操作：</span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p style="text-indent:21pt;"><span style="font-family:'微软雅黑';"><span lang="en-us" style="font-size:14pt;" xml:lang="en-us">1.</span><span style="font-size:14pt;">安装<span lang="en-us" xml:lang="en-us">JDK7</span></span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p style="text-indent:21pt;"><span style="font-family:'微软雅黑';"><span lang="en-us" style="font-size:14pt;" xml:lang="en-us">2.</span><span style="font-size:14pt;">安装<span lang="en-us" xml:lang="en-us">Scala2.10.x</span>（目前推荐<span lang="en-us" xml:lang="en-us">2.10</span>，先不要用<span lang="en-us" xml:lang="en-us">2.11</span>，最后解释）</span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p style="text-indent:21pt;"><span style="font-family:'微软雅黑';"><span lang="en-us" style="font-size:14pt;" xml:lang="en-us">3.</span><span style="font-size:14pt;">下载解压并破解<span lang="en-us" xml:lang="en-us">IDEA</span></span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p style="text-indent:21pt;"><span style="font-family:'微软雅黑';"><span lang="en-us" style="font-size:14pt;" xml:lang="en-us">4.</span><span style="font-size:14pt;">运行<span lang="en-us" xml:lang="en-us">IDEA</span>，并安装插件，依次选择“<span lang="en-us" xml:lang="en-us">Configure</span>”–<span lang="en-us" xml:lang="en-us">&gt;</span>“<span lang="en-us" xml:lang="en-us">Plugins</span>”–<span lang="en-us" xml:lang="en-us">&gt;</span>“<span lang="en-us" xml:lang="en-us">Browse
 repositories</span>”，输入<span lang="en-us" xml:lang="en-us">scala</span>，然后安装即可</span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p align="center" style="text-align:center;text-indent:28pt;">
<span lang="en-us" xml:lang="en-us"><span style="font-family:'宋体';font-size:14px;"><img src="https://img-blog.csdn.net/20160218172124187?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p style="text-indent:28pt;"><span lang="en-us" style="font-size:14pt;" xml:lang="en-us"><span style="font-family:'微软雅黑';"> </span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p align="center" style="text-align:center;text-indent:28pt;">
<span lang="en-us" xml:lang="en-us"><span style="font-family:'宋体';font-size:14px;"><img src="https://img-blog.csdn.net/20160218172142197?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p style="text-indent:28pt;"><span style="font-size:14pt;"><span style="font-family:'微软雅黑';">这里已经安装过了，否则会提示<span lang="en-us" xml:lang="en-us">install</span>的选项。</span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p style="text-indent:21pt;"><span style="font-family:'微软雅黑';"><span lang="en-us" style="font-size:14pt;" xml:lang="en-us">5.</span><span style="font-size:14pt;">配置开发环境</span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p style="text-indent:28pt;"><span style="font-size:14pt;"><span style="font-family:'微软雅黑';">在<span lang="en-us" xml:lang="en-us">intelliJ IDEA</span>中创建<span lang="en-us" xml:lang="en-us">scala project</span>，名称为<span lang="en-us" xml:lang="en-us">test</span>。</span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p align="center" style="text-align:center;text-indent:28pt;">
<span lang="en-us" xml:lang="en-us"><span style="font-family:'宋体';font-size:14px;"><img src="https://img-blog.csdn.net/20160218172201698?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p style="text-indent:21pt;"><span style="font-size:14pt;"><span style="font-family:'微软雅黑';">并依次选择“<span lang="en-us" xml:lang="en-us">File</span>”–<span lang="en-us" xml:lang="en-us">&gt;</span>“<span lang="en-us" xml:lang="en-us">project structure</span>” –<span lang="en-us" xml:lang="en-us">&gt;</span>“<span lang="en-us" xml:lang="en-us">Libraries</span>”，选择“<span lang="en-us" xml:lang="en-us">+</span>”，将<span lang="en-us" xml:lang="en-us">spark-hadoop</span>对应的包导入，比如导入<span lang="en-us" xml:lang="en-us">spark-assembly-1.5.2-hadoop2.6.0.jar</span>（只需导入该<span lang="en-us" xml:lang="en-us">jar</span>包，其他不需要）。</span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p align="center" style="text-align:center;text-indent:28pt;">
<span lang="en-us" xml:lang="en-us"><span style="font-family:'宋体';font-size:14px;"><img src="https://img-blog.csdn.net/20160218172219964?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p style="text-indent:28pt;"><span style="font-size:14pt;"><span style="font-family:'微软雅黑';">如果<span lang="en-us" xml:lang="en-us">IDE</span>没有识别<span lang="en-us" xml:lang="en-us">scala</span>库，则需要以同样方式将<span lang="en-us" xml:lang="en-us">scala</span>库导入。同时还要确认<span lang="en-us" xml:lang="en-us">jdk</span>的配置是否正确，可以在以下界面中查看<span lang="en-us" xml:lang="en-us">jdk</span>和<span lang="en-us" xml:lang="en-us">scala</span>的配置。</span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p align="center" style="text-align:center;text-indent:28pt;">
<span lang="en-us" xml:lang="en-us"><span style="font-family:'宋体';font-size:14px;"><img src="https://img-blog.csdn.net/20160218172232922?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p align="center" style="text-align:center;text-indent:28pt;">
<span lang="en-us" xml:lang="en-us"><span style="font-family:'宋体';font-size:14px;"><img src="https://img-blog.csdn.net/20160218172246594?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p style="text-indent:21pt;"><span style="font-family:'微软雅黑';"><span lang="en-us" style="font-size:14pt;" xml:lang="en-us">6.</span><span style="font-size:14pt;">开发</span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p style="text-indent:21pt;"><span style="font-size:14pt;"><span style="font-family:'微软雅黑';">下面开始开发，这里就不用<span lang="en-us" xml:lang="en-us">helloworld</span>做事示范了，而是直接使用示范程序。</span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p><span style="font-size:14pt;"><span style="font-family:'微软雅黑';">直接将<span lang="en-us" xml:lang="en-us">spark</span>的示范程序<span lang="en-us" xml:lang="en-us">SprkPi.scala</span>和<span lang="en-us" xml:lang="en-us">RDDRelation.scala</span>文件拷贝到工程目录<span lang="en-us" xml:lang="en-us">src</span>下。</span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p align="center" style="text-align:center;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'宋体';font-size:14px;"><img src="https://img-blog.csdn.net/20160218172259106?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p><span style="font-family:'微软雅黑';"><span lang="en-us" style="font-size:14pt;" xml:lang="en-us"><span>    </span></span><span style="font-size:14pt;">为了测试程序在<span lang="en-us" xml:lang="en-us">Spark</span>集群环境下的分布式计算的效果，需要把程序打成<span lang="en-us" xml:lang="en-us">jar</span>包，才能运行在<span lang="en-us" xml:lang="en-us">spark</span>集群中，可以按照以下步骤操作：依次选择“<span lang="en-us" xml:lang="en-us">File</span>”–<span lang="en-us" xml:lang="en-us">&gt;</span>“<span lang="en-us" xml:lang="en-us">ProjectStructure</span>”
 –<span lang="en-us" xml:lang="en-us">&gt; </span>“<span lang="en-us" xml:lang="en-us">Artifact</span>”，选择“<span lang="en-us" xml:lang="en-us">+</span>”–<span lang="en-us" xml:lang="en-us">&gt;</span>“<span lang="en-us" xml:lang="en-us">Jar</span>”–<span lang="en-us" xml:lang="en-us">&gt;</span>“<span lang="en-us" xml:lang="en-us">From Modules with dependencies</span>”，选择<span lang="en-us" xml:lang="en-us">main</span>函数，并在弹出框中选择输出<span lang="en-us" xml:lang="en-us">jar</span>位置，并选择“<span lang="en-us" xml:lang="en-us">OK</span>”。</span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p align="center" style="text-align:center;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'宋体';font-size:14px;"><img src="https://img-blog.csdn.net/20160218172310965?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p align="center" style="text-align:center;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'宋体';font-size:14px;"><img src="https://img-blog.csdn.net/20160218172321673?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p><span style="font-family:'微软雅黑';"><span lang="en-us" style="font-size:14pt;" xml:lang="en-us"><span>    </span></span><span style="font-size:14pt;">最后依次选择“<span lang="en-us" xml:lang="en-us">Build</span>”–<span lang="en-us" xml:lang="en-us">&gt;</span>“<span lang="en-us" xml:lang="en-us">Build
 Artifact</span>”编译生成<span lang="en-us" xml:lang="en-us">jar</span>包。具体如下图所示。</span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p align="center" style="text-align:center;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'宋体';font-size:14px;"><img src="https://img-blog.csdn.net/20160218172333095?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p><span style="font-family:'微软雅黑';"><span lang="en-us" style="font-size:14pt;" xml:lang="en-us"><span>    </span></span><span style="font-size:14pt;">修改代码，注意要加入红框中的两行代码才能使程序在集群环境中运行。因为示范程序默认是本地执行，只有设置下面两行代码才能让程序运行在集群环境中，并通过<span lang="en-us" xml:lang="en-us">addJar</span>方法可以让<span lang="en-us" xml:lang="en-us">worker</span>找到依赖的<span lang="en-us" xml:lang="en-us">.jar</span>文件。</span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p align="center" style="text-align:center;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'宋体';font-size:14px;"><img src="https://img-blog.csdn.net/20160218172345688?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p><span style="font-family:'微软雅黑';"><span lang="en-us" style="font-size:14pt;" xml:lang="en-us"><span>    </span></span><span style="font-size:14pt;">运行并查看结果</span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p align="center" style="text-align:center;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'宋体';font-size:14px;"><img src="https://img-blog.csdn.net/20160218172357857?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<h1><span style="line-height:240%;font-size:16pt;"><span style="font-family:'微软雅黑';">总结</span></span></h1>
<span style="font-family:'宋体';font-size:14px;"></span>
<p><span style="font-family:'微软雅黑';"><span lang="en-us" style="font-size:14pt;" xml:lang="en-us"><span>    </span></span><span style="font-size:14pt;">由于开发环境是笔记本的虚拟机，网络为普通办公网络，编译生成的<span lang="en-us" xml:lang="en-us">test.jar</span>有<span lang="en-us" xml:lang="en-us">180m</span>，在执行过程中可能会产生比较大的网络<span lang="en-us" xml:lang="en-us">io</span>导致性能有所影响。同样的环境下在<span lang="en-us" xml:lang="en-us">master</span>上直接运行<span lang="en-us" xml:lang="en-us">SparkPi.scala</span>时间在<span lang="en-us" xml:lang="en-us">3s</span>。所以后面会实际物理环境中测试来体现<span lang="en-us" xml:lang="en-us">Spark</span>在分布式多节点状态下的性能优势，在此大家只要了解如何部署开发环境和进行代码运行调试的过程就可以了。</span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p><span style="font-family:'微软雅黑';"><span lang="en-us" style="font-size:14pt;" xml:lang="en-us"><span>    </span></span><span style="font-size:14pt;">另外，对于前面所说的<span lang="en-us" xml:lang="en-us">scala</span>版本的问题，由于已经下载使用了<span lang="en-us" xml:lang="en-us">Spark 1.5.2</span>，而在下载<span lang="en-us" xml:lang="en-us">Scala</span>时选择了最新的<span lang="en-us" xml:lang="en-us">2.11</span>。在开发运行过程中会出现以下错误。通过研究发现是<span lang="en-us" xml:lang="en-us">scala</span>版本兼容问题，解决办法是重新下载<span lang="en-us" xml:lang="en-us">scala2.10</span>，然后删除<span lang="en-us" xml:lang="en-us">scala
 2.11</span>目录，修改环境配置以及<span lang="en-us" xml:lang="en-us">scala</span>工程中对<span lang="en-us" xml:lang="en-us">scala</span>版本的引用，重新编译运行之后问题解决了。<span lang="en-us" xml:lang="en-us">Spark 1.5</span>支持<span lang="en-us" xml:lang="en-us">scala 2.11</span>但需要用户自己下载<span lang="en-us" xml:lang="en-us">Spark</span>源码再基于<span lang="en-us" xml:lang="en-us">Scala 2.11</span>重新编译，由于这里测试使用的是预编译版本，所以会造成版本不一致导致的问题。</span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p align="center" style="text-align:center;"><span lang="en-us" xml:lang="en-us"><span style="font-family:'宋体';font-size:14px;"><img src="https://img-blog.csdn.net/20160218172409954?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
<p><span lang="en-us" style="font-size:14pt;" xml:lang="en-us"><span style="font-family:'微软雅黑';"> </span></span></p>
<span style="font-family:'宋体';font-size:14px;"></span>
            </div>
                </div>