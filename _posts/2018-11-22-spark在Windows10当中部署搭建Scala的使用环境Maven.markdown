---
layout:     post
title:      spark在Windows10当中部署搭建Scala的使用环境Maven
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/shiheyingzhe/article/details/80721752				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1 style="text-align:center;"><span style="font-size:16px;">在Windows10中搭建部署Scala编写spark的使用环境</span></h1><p><span style="font-size:16px;">在Scala中编写spark的程序，需要安装好Java、spark、hadoop、Scala这些环境才可以，spark、hadoop都是依赖Java的，spark的开发语言是Scala，支持用Java、Scala、python这些语言来编写spark程序，本文讲述配置Scala语言编写spark代码的过程，文中的Java版本是Java SE 1.8，spark版本是2.3.1，Scala版本用的是插件。为了方便编写和调试，这里安装的IDE是IntelliJ IDEA Community Edition。Scala编写spark程序有几种方式，一种是使用SBT环境，另一种是使用Maven环境，这里讲的是使用Maven环境。</span></p><p><span style="font-size:16px;">这里安装Java、spark、hadoop和另一篇文章是相同的步骤<a href="https://blog.csdn.net/shiheyingzhe/article/details/80714301" rel="nofollow">spark2.3在Windows10当中来搭建python3的使用环境pyspark</a></span></p><p><br></p><ul><li><strong><span style="font-size:16px;">首先需要安装Java</span></strong></li></ul><p><span style="font-size:16px;">到官网下载并安装<span style="background-color:rgb(255,255,255);color:rgb(51,51,51);font-family:'PingFang SC', 'Lantinghei SC', 'Microsoft YaHei', arial, '宋体', sans-serif, tahoma;white-space:pre-wrap;">Java Standard Edition即Java SE1.8.本，这里下载的是window64位版本JDK，<a href="http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html" rel="nofollow">点击打开链接</a>，</span></span></p><p><span style="background-color:rgb(255,255,255);color:rgb(51,51,51);font-family:'PingFang SC', 'Lantinghei SC', 'Microsoft YaHei', arial, '宋体', sans-serif, tahoma;white-space:pre-wrap;"><span style="font-size:16px;"><img src="https://img-blog.csdn.net/20180617225051572?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></span></span></p><p><span style="background-color:rgb(255,255,255);color:rgb(51,51,51);font-family:'PingFang SC', 'Lantinghei SC', 'Microsoft YaHei', arial, '宋体', sans-serif, tahoma;white-space:pre-wrap;"><strong><span style="font-size:16px;">设置环境变量</span></strong></span></p><p><span style="background-color:rgb(255,255,255);color:rgb(51,51,51);font-family:'PingFang SC', 'Lantinghei SC', 'Microsoft YaHei', arial, '宋体', sans-serif, tahoma;white-space:pre-wrap;"><span style="font-size:16px;">安装过程中按照默认配置就好，安装好以后，配置Java的环境变量，右键我的电脑，依次点击属性-高级系统设置-环境变量</span></span></p><p><span style="background-color:rgb(255,255,255);color:rgb(51,51,51);font-family:'PingFang SC', 'Lantinghei SC', 'Microsoft YaHei', arial, '宋体', sans-serif, tahoma;white-space:pre-wrap;"><span style="font-size:16px;">新建用户变量: JAVA_HOME；C:\Program Files\Java\jdk1.8.0_171</span></span></p><p><span style="background-color:rgb(255,255,255);color:rgb(51,51,51);font-family:'PingFang SC', 'Lantinghei SC', 'Microsoft YaHei', arial, '宋体', sans-serif, tahoma;white-space:pre-wrap;"><span style="font-size:16px;"><img src="https://img-blog.csdn.net/20180617225310258?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></span></span></p><p><span style="background-color:rgb(255,255,255);color:rgb(51,51,51);font-family:'PingFang SC', 'Lantinghei SC', 'Microsoft YaHei', arial, '宋体', sans-serif, tahoma;white-space:pre-wrap;"><span style="font-size:16px;">在系统变量中找到Path，点击按钮新建，然后添加文字%JAVA_HOME%\bin，最后按回车Enter，一直点击确定，就保存了更改，这样就将bin文件夹中的Java程序放到了系统变量中。</span></span></p><p><span style="background-color:rgb(255,255,255);color:rgb(51,51,51);font-family:'PingFang SC', 'Lantinghei SC', 'Microsoft YaHei', arial, '宋体', sans-serif, tahoma;white-space:pre-wrap;"><span style="font-size:16px;"><img src="https://img-blog.csdn.net/20180616223443474?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></span></span></p><span style="font-size:16px;"><span style="color:rgb(51,51,51);font-family:'PingFang SC', 'Lantinghei SC', 'Microsoft YaHei', arial, '宋体', sans-serif, tahoma;white-space:pre-wrap;background-color:rgb(255,255,255);">检查Java环境变量配置是否成功</span><span style="color:rgb(51,51,51);font-family:'PingFang SC', 'Lantinghei SC', 'Microsoft YaHei', arial, '宋体', sans-serif, tahoma;white-space:pre-wrap;background-color:rgb(255,255,255);">，使用快捷键Ctrl+R打开命令行，输入cmd打开命令行工具，输入java -version</span></span><p><span style="font-family:'Microsoft YaHei';"><span style="font-size:16px;">当出现图中所示时Java安装配置完成</span></span></p><p><span style="font-size:16px;"><img src="https://img-blog.csdn.net/20180617231839701?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></span></p><p><span style="background-color:rgb(255,255,255);color:rgb(51,51,51);font-family:'PingFang SC', 'Lantinghei SC', 'Microsoft YaHei', arial, '宋体', sans-serif, tahoma;white-space:pre-wrap;"><span style="font-size:16px;"><br></span></span></p><h2></h2><h2></h2><ul><li><strong><span style="font-size:16px;">下载安装并配置Spark</span></strong></li></ul><p><span style="font-size:16px;">从官方网站<a href="http://spark.apache.org/downloads.html" rel="nofollow">Download Apache Spark™</a>下载相应版本的spark，因为spark是基于hadoop的，需要下载对应版本的hadoop才行，这个页面有对hadoop的版本要求，点击<span style="color:rgb(85,85,85);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;background-color:rgb(255,255,255);">Download Spark:</span><span style="color:rgb(85,85,85);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;background-color:rgb(255,255,255);"> </span><span style="color:rgb(85,85,85);text-align:left;background-color:rgb(255,255,255);"><a href="https://www.apache.org/dyn/closer.lua/spark/spark-2.3.1/spark-2.3.1-bin-hadoop2.7.tgz" rel="nofollow" style="color:rgb(47,164,231);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;background:transparent;">spark-2.3.1-bin-hadoop2.7.tgz</a><span style="font-family:'Microsoft YaHei';">就可以下载压缩包了，对应的hadoop版本要在Hadoop2.7及其以后。</span></span></span></p><p></p><p><span style="font-size:16px;"><img src="https://img-blog.csdn.net/20180616175513608?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""></span></p><p><span style="font-size:16px;">这里解压到D:\spark-2.3.1-bin-hadoop2.7，为了后续操作简便，这里将解压以后的文件夹名称改为spark，这样解压的路径就是D:\spark</span></p><p><strong><span style="font-size:16px;">配置环境变量</span></strong></p><span style="font-size:16px;"><span style="background-color:rgb(255,255,255);color:rgb(51,51,51);font-family:'PingFang SC', 'Lantinghei SC', 'Microsoft YaHei', arial, '宋体', sans-serif, tahoma;white-space:pre-wrap;">右键我的电脑，依次点击属性-高级系统设置-环境变量</span><br></span><p><span style="font-size:16px;">新建用户变量 SPARK_HOME    D:\spark</span></p><p></p><p><span style="font-size:16px;"><img src="https://img-blog.csdn.net/20180616225553484?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></span></p><p><span style="font-size:16px;">找到系统变量Path  点击按钮新建，然后添加文本%SPARK_HOME%\bin，按回车enter，继续新建一个，添加文本%SPARK_HOME%\sbin，按键回车，<span style="color:rgb(51,51,51);font-family:'PingFang SC', 'Lantinghei SC', 'Microsoft YaHei', arial, '宋体', sans-serif, tahoma;white-space:pre-wrap;background-color:rgb(255,255,255);">一直点击确定，就保存了更改，这样就将bin、sbin文件夹中的程序放到了系统变量中</span></span></p><p><span style="font-size:16px;"><img src="https://img-blog.csdn.net/20180616225826213?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></span></p><p><span style="font-size:16px;"><strong>pyspark：</strong>到这里spark的配置完成了一部分，还有pyspark需要配置，pyspark等anaconda安装后在下文中讨论，pyspark的安装有几种方式，其中解压以后的spark文件夹中就有pyspark库，可以安装到python的库当中去；还可以不复制，pyspark可以通过pip单独安装，还有一种是单独下载pyspark的安装包，解压以后安装到python库当中去。</span></p><p><span style="font-size:16px;"><br></span></p><h2></h2><h2></h2><ul><li><strong><span style="font-size:16px;">安装并配置Hadoop</span></strong></li></ul><p><span style="font-size:16px;">上面安装spark的时候有对hadoop的版本要求，这里要求的是2.7及以后的版本，进入官方网站<a href="http://hadoop.apache.org/releases.html" rel="nofollow">Apache Hadoop Releases</a>下载2.7.6 binary版本，其中source版本是该版本hadoop的源代码，下载以后解压到D:\hadoop-2.7.6，为了后续操作方便，解压以后修改文件夹名称为hadoop，这样文件夹就是D:\hadoop</span></p><p><span style="font-size:16px;"><img src="https://img-blog.csdn.net/20180616231942836?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></span></p><p><strong><span style="font-size:16px;">配置环境变量：</span></strong></p><span style="font-size:16px;"><span style="background-color:rgb(255,255,255);color:rgb(51,51,51);font-family:'PingFang SC', 'Lantinghei SC', 'Microsoft YaHei', arial, '宋体', sans-serif, tahoma;white-space:pre-wrap;">右键我的电脑，依次点击属性-高级系统设置-环境变量</span><br></span><p><span style="background-color:rgb(255,255,255);white-space:pre-wrap;color:rgb(51,51,51);font-family:'PingFang SC', 'Lantinghei SC', 'Microsoft YaHei', arial, '宋体', sans-serif, tahoma;"><span style="font-size:16px;">新增用户变量 HADOOP_HOME    D:\hadoop</span></span></p><p><span style="background-color:rgb(255,255,255);white-space:pre-wrap;color:rgb(51,51,51);font-family:'PingFang SC', 'Lantinghei SC', 'Microsoft YaHei', arial, '宋体', sans-serif, tahoma;"><span style="font-size:16px;"><img src="https://img-blog.csdn.net/20180616232557971?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></span></span></p><p><span style="background-color:rgb(255,255,255);white-space:pre-wrap;color:rgb(51,51,51);font-family:'PingFang SC', 'Lantinghei SC', 'Microsoft YaHei', arial, '宋体', sans-serif, tahoma;"><span style="font-size:16px;">然后找到系统变量Path  点击按钮新建，然后添加文本%HADOOP%\bin，按回车enter，继续新建一个，添加文本%HADOOP%\sbin，按键回车，一直点击确定，就保存了更改，这样就将bin、sbin文件夹中的程序放到了系统变量中</span></span></p><p><span style="background-color:rgb(255,255,255);white-space:pre-wrap;color:rgb(51,51,51);font-family:'PingFang SC', 'Lantinghei SC', 'Microsoft YaHei', arial, '宋体', sans-serif, tahoma;"><span style="font-size:16px;"><img src="https://img-blog.csdn.net/20180617083609407?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></span></span></p><p><span style="font-family:'PingFang SC', 'Lantinghei SC', 'Microsoft YaHei', arial, '宋体', sans-serif, tahoma;color:#333333;"><span style="white-space:pre-wrap;background-color:rgb(255,255,255);"><span style="font-size:16px;">从网站中下载<a href="https://github.com/srccodes/hadoop-common-2.2.0-bin" rel="nofollow">点击打开链接</a>一个压缩包，然后解压出来，复制其中的winutils.exe和winutils.pdb到hadoop的安装文件夹中，复制目录为：D:\hadoop\bin，复制到这个目录中</span></span></span></p><p><span style="font-family:'PingFang SC', 'Lantinghei SC', 'Microsoft YaHei', arial, '宋体', sans-serif, tahoma;color:#333333;"><span style="white-space:pre-wrap;background-color:rgb(255,255,255);"><span style="font-size:16px;">当输入命令pyspark出现以下结果时表明spark安装配置完成了</span></span></span></p><p><span style="font-family:'PingFang SC', 'Lantinghei SC', 'Microsoft YaHei', arial, '宋体', sans-serif, tahoma;color:#333333;"><span style="white-space:pre-wrap;background-color:rgb(255,255,255);"><span style="font-size:16px;"><img src="https://img-blog.csdn.net/20180617183933845?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""></span></span></span></p><p><span style="font-family:'PingFang SC', 'Lantinghei SC', 'Microsoft YaHei', arial, '宋体', sans-serif, tahoma;color:#333333;"><span style="white-space:pre-wrap;background-color:rgb(255,255,255);"><span style="font-size:16px;"><br></span></span></span></p><ul><li><strong><span style="font-size:16px;">安装配置IntelliJ IDEA Community Edition</span></strong></li></ul><p></p><p><span style="font-size:16px;">进入官方网站<a href="http://www.jetbrains.com/idea/download/#section=windows" rel="nofollow">Download IntelliJ IDEA：The Java IDE</a>，下载社区版的，按照默认配置安装就好，如果是32位的电脑，就勾选32位，64位的勾选64位。打开以后安装插件Scala，这里没有安装的话，需要等进入界面以后再安装。这里是使用Maven来构建相应的Scala编写环境</span></p><p><span style="font-size:16px;"><img src="https://img-blog.csdn.net/20180617204944430?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></span></p><p><span style="font-size:16px;">下一步点击Configure里面的settings</span></p><p><span style="font-size:16px;"><img src="https://img-blog.csdn.net/20180617210345566?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></span></p><p><span style="font-size:16px;">点击插件Plugins，输入Scala搜索插件，点击右侧的install安装，之前已经安装了，就可以不点击，之后需要安装什么插件，都可以通过File-Default Settings-Plugins来查找安装</span></p><p><span style="font-size:16px;"><img src="https://img-blog.csdn.net/20180617210509324?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></span></p><p><span style="font-size:16px;">修改字体这些在Settings-Editor-Font当中</span></p><p><span style="font-size:16px;"><img src="https://img-blog.csdn.net/20180617210833383?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></span></p><p><span style="font-size:16px;">修改编码为utf-8</span></p><p><span style="font-size:16px;"><img src="https://img-blog.csdn.net/2018061800303469?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></span></p><p><span style="font-size:16px;"><br></span></p><p><strong><span style="font-size:16px;">新建一个项目点击Create New Project</span></strong></p><p><span style="font-size:16px;"><img src="https://img-blog.csdn.net/2018061721183837?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></span></p><p><span style="font-size:16px;">然后需要配置Java的环境，依次点击Maven-New...，然后进入Java的安装目录C:\Program Files\Java，选择jdk1.8.0_171以后，点击OK，然后点击Next</span></p><p><span style="font-size:16px;"><img src="https://img-blog.csdn.net/20180617232309275?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></span></p><p><span style="font-size:16px;">填入相应信息</span></p><p><span style="font-size:16px;"><img src="https://img-blog.csdn.net/20180617212946443?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></span></p><p><span style="font-size:16px;">选择好项目所在的位置然后点击OK</span></p><p><span style="font-size:16px;"><img src="https://img-blog.csdn.net/20180617213016212?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></span></p><p><strong><span style="font-size:16px;">这里需要注意的是，必须点击Enable Auto-Import，没有这个弹窗的话，需要自己点击最右下角的气泡标志，然后点击Enable Auto-Import</span></strong></p><p><span style="font-size:16px;"><img src="https://img-blog.csdn.net/20180617213132596?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="">   <img src="https://img-blog.csdn.net/20180617213250144?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></span></p><p><span style="font-size:16px;"><br></span></p><h2><span style="font-size:16px;">配置包和编译器Project Structure</span></h2><p><span style="font-size:16px;">点击File-Project Structure，也可以右键项目点击Open Module Settings进入，Project中的SDK要是选中的Java版本</span></p><p><span style="font-size:16px;"><img src="https://img-blog.csdn.net/20180617214033837?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="" style="margin-bottom:0px;">     <img src="https://img-blog.csdn.net/20180617232836641?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></span></p><p><span style="font-size:16px;">点击Libraries，然后点击+号，点击下拉链的Kotlin/JS，进入spark的安装目录D:\spark\jars中，选中jars目录中的所有jar包，可以先点击第一个，然后按住shift键，拖动下拉链，点击最后一个jar包，这样就全都选中了，然后一直点击OK，等待配置完成。</span></p><p><span style="font-size:16px;"><img src="https://img-blog.csdn.net/2018061800001296?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""> <img src="https://img-blog.csdn.net/20180618000626113?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></span></p><p><span style="font-size:16px;">配置好以后显示这个</span></p><p><span style="font-size:16px;"><img src="https://img-blog.csdn.net/20180618000844425?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></span></p><p><span style="font-size:16px;">接着依次点击Global Libraries-Scala SDK<br></span></p><p><span style="font-size:16px;"><img src="https://img-blog.csdn.net/20180617220623984?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></span></p><p><span style="font-size:16px;">然后选择Maven，一直点击OK</span></p><p><span style="font-size:16px;"><img src="https://img-blog.csdn.net/20180617220755842?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""></span></p><p><span style="font-size:16px;">右键新加入的SDK，点击Copy to Project Libraries</span></p><p><span style="font-size:16px;"><img src="https://img-blog.csdn.net/20180618083822641?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></span></p><p><span style="font-size:16px;"><br></span></p><p><span style="background-color:rgb(247,247,247);color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;"><strong><span style="font-size:16px;">创建WordCount文件夹，并作为Sources Root</span></strong></span></p><p><span style="font-size:16px;">然后在项目中右键src，依次点击New-Directory，新建一个目录命名为WordCount</span></p><p><span style="font-size:16px;"><img src="https://img-blog.csdn.net/20180617213412729?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></span></p><p><span style="font-size:16px;">右键新建立的目录，依次点击Make Directory as-Sources Root，就可以在这个目录中编辑源文件，设置好这一步以后，WordCount目录的颜色会变为蓝色。</span></p><p><span style="font-size:16px;"><img src="https://img-blog.csdn.net/20180617213635173?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></span></p><p><span style="font-size:16px;"><br></span></p><p><strong><span style="font-size:16px;">新建配置文件和Scala代码</span></strong></p><p><span style="font-size:16px;">右键WordCount文件夹，新建一个Scala class</span></p><p><span style="font-size:16px;"><img src="https://img-blog.csdn.net/20180617221459299?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></span></p><p><span style="font-size:16px;">类型要选择Object类型的才可以</span></p><p><span style="font-size:16px;"><img src="https://img-blog.csdn.net/20180617221538491?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></span></p><p><span style="font-size:16px;">然后点击右侧的Setup JDK点击OK就行</span></p><p><span style="font-size:16px;"><img src="https://img-blog.csdn.net/20180617221717929?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></span></p><p><span style="font-size:16px;">进入pom.xml的编辑状态，粘贴以下代码</span></p><p><span style="font-size:16px;"><img src="https://img-blog.csdn.net/20180617232642546?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""></span></p><pre><code class="language-python">&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"&gt;
&lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;

&lt;groupId&gt;dblab&lt;/groupId&gt;
&lt;artifactId&gt;WordCount&lt;/artifactId&gt;
&lt;version&gt;1.0-SNAPSHOT&lt;/version&gt;

&lt;properties&gt;
    &lt;spark.version&gt;2.3.1&lt;/spark.version&gt;
    &lt;scala.version&gt;2.11.8&lt;/scala.version&gt;
&lt;/properties&gt;


&lt;dependencies&gt;
    &lt;dependency&gt;
        &lt;groupId&gt;org.apache.spark&lt;/groupId&gt;
        &lt;artifactId&gt;spark-core_${scala.version}&lt;/artifactId&gt;
        &lt;version&gt;${spark.version}&lt;/version&gt;
    &lt;/dependency&gt;
    &lt;dependency&gt;
        &lt;groupId&gt;org.apache.spark&lt;/groupId&gt;
        &lt;artifactId&gt;spark-streaming_${scala.version}&lt;/artifactId&gt;
        &lt;version&gt;${spark.version}&lt;/version&gt;
    &lt;/dependency&gt;
    &lt;dependency&gt;
        &lt;groupId&gt;org.apache.spark&lt;/groupId&gt;
        &lt;artifactId&gt;spark-sql_${scala.version}&lt;/artifactId&gt;
        &lt;version&gt;${spark.version}&lt;/version&gt;
    &lt;/dependency&gt;
    &lt;dependency&gt;
        &lt;groupId&gt;org.apache.spark&lt;/groupId&gt;
        &lt;artifactId&gt;spark-hive_${scala.version}&lt;/artifactId&gt;
        &lt;version&gt;${spark.version}&lt;/version&gt;
    &lt;/dependency&gt;
    &lt;dependency&gt;
        &lt;groupId&gt;org.apache.spark&lt;/groupId&gt;
        &lt;artifactId&gt;spark-mllib_${scala.version}&lt;/artifactId&gt;
        &lt;version&gt;${spark.version}&lt;/version&gt;
    &lt;/dependency&gt;
    &lt;dependency&gt;
        &lt;groupId&gt;org.ansj&lt;/groupId&gt;
        &lt;artifactId&gt;ansj_seg&lt;/artifactId&gt;
        &lt;version&gt;5.1.6&lt;/version&gt;
    &lt;/dependency&gt;

&lt;/dependencies&gt;

&lt;build&gt;
    &lt;plugins&gt;

        &lt;plugin&gt;
            &lt;groupId&gt;org.scala-tools&lt;/groupId&gt;
            &lt;artifactId&gt;maven-scala-plugin&lt;/artifactId&gt;
            &lt;version&gt;2.15.2&lt;/version&gt;
            &lt;executions&gt;
                &lt;execution&gt;
                    &lt;goals&gt;
                        &lt;goal&gt;compile&lt;/goal&gt;
                        &lt;goal&gt;testCompile&lt;/goal&gt;
                    &lt;/goals&gt;
                &lt;/execution&gt;
            &lt;/executions&gt;
        &lt;/plugin&gt;

        &lt;plugin&gt;
            &lt;artifactId&gt;maven-compiler-plugin&lt;/artifactId&gt;
            &lt;version&gt;3.6.0&lt;/version&gt;
            &lt;configuration&gt;
                &lt;source&gt;1.8&lt;/source&gt;
                &lt;target&gt;1.8&lt;/target&gt;
            &lt;/configuration&gt;
        &lt;/plugin&gt;

        &lt;plugin&gt;
            &lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;
            &lt;artifactId&gt;maven-surefire-plugin&lt;/artifactId&gt;
            &lt;version&gt;2.19&lt;/version&gt;
            &lt;configuration&gt;
                &lt;skip&gt;true&lt;/skip&gt;
            &lt;/configuration&gt;
        &lt;/plugin&gt;

    &lt;/plugins&gt;
&lt;/build&gt;

&lt;/project&gt;</code></pre><p><span style="background-color:rgb(255,255,255);color:rgb(0,0,0);font-family:'Courier New';font-size:12pt;">黏贴好后，右键点击工程文件夹，更新一下文件，按下图操作，点击Generate Sources and Update Folders</span></p><p><span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;background-color:rgb(254,254,254);"><span style="font-size:16px;"><img src="https://img-blog.csdn.net/20180617233553335?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></span></span></p><p><span style="font-size:16px;">等待配置完成，右下角会有进度条显示，可以点击查看具体情况，如果有起泡标志，必须点击蓝色字体<span style="font-weight:bold;">Enable Auto-Import</span></span></p><h2><span style="font-weight:bold;"><span style="font-size:16px;">安装ansj中文分词库：</span></span></h2><p><span style="font-size:16px;">在项目官网中<a href="https://github.com/NLPchina/ansj_seg" rel="nofollow" style="font-weight:bold;">https://github.com/NLPchina/ansj_seg</a>下载最新版<a href="https://oss.sonatype.org/content/repositories/releases/org/ansj/ansj_seg/5.1.6/" rel="nofollow">ansj_seg-5.1.6.jar</a>，在网站下载最新版<a href="https://oss.sonatype.org/content/repositories/releases/org/nlpcn/nlp-lang/1.7.7/" rel="nofollow">nlp-lang-1.7.7.jar</a>，jar包都放在项目的目录中D:\IdeaProjects<br></span></p><p><span style="font-size:16px;"><img src="https://img-blog.csdn.net/20180623175508724?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></span></p><p>然后添加jar包到项目当中来，点击File-Project Structure，也可以右键项目点击Open Module Settings进入，点击Libraries，然后点击右边的+号，点击Kotlin/JS，进入项目目录，选中两个jar包，点击OK，然后点击JS file，最后一直点击OK，将jar包都导入进来。</p><p><img src="https://img-blog.csdn.net/20180623175739553?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="">     <img src="https://img-blog.csdn.net/20180623180148555?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="">     <img src="https://img-blog.csdn.net/20180623180333463?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NoaWhleWluZ3poZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""> </p><p><strong>编写Scala代码并运行：</strong></p><pre><code class="language-python">import org.apache.spark.{SparkContext,SparkConf}
import org.apache.spark.rdd.RDD
import scala.io.Source
import org.ansj.library.DicLibrary
import org.ansj.recognition.impl.StopRecognition
import org.nlpcn.commons.lang.tire.library
import org.ansj.splitWord.analysis.ToAnalysis
object WordCount {
  val False: Boolean = False
  def main(args: Array[String]) {
    System.setProperty("spark.ui.showConsoleProgress", "False")
    //----添加自定义词典----
    //    val dicfile = raw"/xxx/xxx/ExtendDic" //ExtendDic为一个文本文件的名字，里面每一行存放一个词
    //    for (word &lt;- Source.fromFile(dicfile).getLines) { DicLibrary.insert(DicLibrary.DEFAULT,word)} //逐行读入文本文件，将其添加到自定义词典中
    DicLibrary.insert(DicLibrary.DEFAULT, "小王子")
    val filter = new StopRecognition()
    filter.insertStopNatures("w") //过滤掉标点
    val file = Source.fromFile (raw"D:\中文停用词库.txt")
    for(x&lt;- file.getLines()){
      filter.insertStopWords(x.toString())
    }
    // ----构建spark对象----
    val conf = new SparkConf().setAppName("TextClassificationDemo").setMaster("local[2]")
    val sc = new SparkContext(conf)
    //----读入要分词的文件----
    val filename = raw"d:\WordCount.txt"
    val TXTFile = sc.textFile(filename) // 用sc读入文件，此时文件的数据是RDD结构，注意textFile只能读UTF-8编码
    val splited = TXTFile.map(x =&gt; ToAnalysis.parse(x).recognition(filter).toStringWithOutNature(" "))
    val wordCount = splited.flatMap(_.split(" ")).map((_, 1)).reduceByKey(_ + _).map(x =&gt; (x._2, x._1)).sortByKey(ascending = False)
    val choose=wordCount.take(100)
    choose.foreach(println)
  }
}</code></pre>使用Ctrl+Shift+F10快捷键run代码，可以得到输出结果：<br><pre><code class="language-python">(419,小王子)
(361,说)
(219,一个)
(202,没有)
(194,我的)
(134,说道)
(124,星星)
(118,会)
(118,花)
(114,画)
(104,星球)
(84,羊)
(80,地方)
(78,国王)
(73,中)
(72,你的)
(72,狐狸)
(68,知道)
(68,象)
(67,想)
(65,回答)
(63,一只)
(62,真)
(60,非常)</code></pre>参考内容：<br><a href="https://blog.csdn.net/bitcarmanlee/article/details/53607776" rel="nofollow">ansj分词史上最详细教程</a><br><a href="https://github.com/NLPchina/ansj_seg" rel="nofollow">https://github.com/NLPchina/ansj_seg</a><br><a href="http://dblab.xmu.edu.cn/blog/1327/" rel="nofollow">利用开发工具IntelliJ IDEA编写Spark应用程序（Scala+Maven）</a><br><a href="https://www.jianshu.com/p/27888ca5dcbb" rel="nofollow">spark应用开发-开发工具篇</a><br><a href="https://blog.csdn.net/guiying712/article/details/68947747#%E4%B8%8B%E8%BD%BDscala" rel="nofollow">学习大数据的第一步-搭建Scala开发环境，以及使用Intellij IDEA开发Scala程序</a><br><p><a href="https://jingyan.baidu.com/article/bad08e1ec8f6dd09c9512173.html" rel="nofollow">IntelliJ IDEA 配置maven</a></p>            </div>
                </div>