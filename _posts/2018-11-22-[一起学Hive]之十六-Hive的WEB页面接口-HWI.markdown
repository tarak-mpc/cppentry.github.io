---
layout:     post
title:      [一起学Hive]之十六-Hive的WEB页面接口-HWI
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
关键字：Hive WEB接口、Hive HWI</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
Hive提供给用户的交互接口一般有三种：Hive命令行(Hive Command Line)、JDBC/ODBC以及Hive WEB Interface（HWI）。对于开发者来说，最常用的是Hive命令行。如果是将Hive开放给数据分析或数据运营人员做即席查询，那么使用Hive WEB页面接口(HWI)应该是比较方便的。</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
本章就介绍一下Hive WEB页面接口(HWI)的配置和使用。以Hive0.13.1为例。</p>
<h2 style="line-height:18px;color:rgb(85,85,85);font-size:18px;border-left-width:4px;border-left-style:solid;border-left-color:rgb(0,166,124);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;text-indent:30px;background-color:rgb(251,251,251);">
15.1 配置和启动HWI</h2>
<ul style="list-style:none;color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;"><li style="line-height:30px;text-indent:0px;"><strong>配置</strong></li></ul><p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
HWI的运行需要依赖两个包：hive-hwi-0.13.1.jar和hive-hwi-0.13.1.war，这两个包应该都部署在$HIVE_HOME/lib目录下。</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
但Hive0.13.1的安装包lib目录下没有提供hive-hwi-0.13.1.war，解决办法很简单，下载Hive0.13.1的源码，进入源码的\hwi\web\目录下，将该目录下的文件夹和文件压缩成zip包，再重命名为hive-hwi-0.13.1.war，放到$HIVE_HOME/lib目录下即可。</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
<img class="aligncenter" src="http://7xipth.com1.z0.glb.clouddn.com/0720-1.jpg" alt="hive hwi" width="369" height="300" style="vertical-align:middle;border:0px;display:block;text-align:center;"></p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
配置$HIVE_HOME/conf/hive-site.xml</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
&lt;property&gt;</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
       &lt;name&gt;<strong>hive.hwi.listen.host</strong>&lt;/name&gt;</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
       &lt;value&gt;172.16.213.12&lt;/value&gt;</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
       &lt;description&gt;HWI服务运行的主机 &lt;/description&gt;</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
&lt;/property&gt;</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
&lt;property&gt;</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
       &lt;name&gt;<strong>hive.hwi.listen.port</strong>&lt;/name&gt;</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
       &lt;value&gt;9999&lt;/value&gt;</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
       &lt;description&gt;HWI服务运行的端口 &lt;/description&gt;</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
&lt;/property&gt;</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
&lt;property&gt;</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
       &lt;name&gt;<strong>hive.hwi.war.file</strong>&lt;/name&gt;</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
       &lt;value&gt;lib/hive-hwi-0.13.1.war&lt;/value&gt;</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
       &lt;description&gt;HWI服务运行的WAR包路径 &lt;/description&gt;</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
&lt;/property&gt;</p>
<ul style="list-style:none;color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;"><li style="line-height:30px;text-indent:0px;"><strong>启动</strong></li></ul><p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
进入$HIVE_HOME/bin/</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
运行：<span style="color:rgb(255,0,0);"><strong>nohup ./hive –service hwi &gt;&gt; /tmp/hive-hwi.log 2&gt;&amp;1 &amp;</strong></span></p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
HWI服务的日志被记录在tmp/hive-hwi.log中。</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
查看该日志文件：</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
<img class="aligncenter" src="http://7xipth.com1.z0.glb.clouddn.com/0720-2.jpg" alt="hive hwi" width="642" height="158" style="vertical-align:middle;border:0px;display:block;text-align:center;"></p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
日志中提示 Started SocketConnector@172.16.213.12:9999 ，说明hwi服务正常启动。</p>
<h2 style="line-height:18px;color:rgb(85,85,85);font-size:18px;border-left-width:4px;border-left-style:solid;border-left-color:rgb(0,166,124);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;text-indent:30px;background-color:rgb(251,251,251);">
15.2 使用HWI</h2>
<ul style="list-style:none;color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;"><li style="line-height:30px;text-indent:0px;"><strong>用户认证信息</strong></li></ul><p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
  使用浏览器打开：http://172.16.213.12:9999/hwi/</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
         首先会提示填入用户的信息，即用户名和用户组，填入之后点击Submit，提示认证完成。</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
<img class="aligncenter" src="http://7xipth.com1.z0.glb.clouddn.com/0720-3.jpg" alt="hive hwi" width="573" height="264" style="vertical-align:middle;border:0px;display:block;text-align:center;"></p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
认证信息即参数hadoop.job.ugi的值，这种认证方式很不安全，如果是真实环境使用的话，需要封装。</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
 </p>
<ul style="list-style:none;color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;"><li style="line-height:30px;text-indent:0px;"><strong>创建会话</strong></li></ul><p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
点击左侧的Create Session，创建一个Hive的会话：填入会员名之后，点击Submit，进入会话页：</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
<img class="aligncenter" src="http://7xipth.com1.z0.glb.clouddn.com/0720-4.jpg" alt="hive hwi" width="590" height="436" style="vertical-align:middle;border:0px;display:block;text-align:center;"></p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
 </p>
<ul style="list-style:none;color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;"><li style="line-height:30px;text-indent:0px;"><strong>执行查询</strong></li></ul><p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
进入会话管理页面：</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
在Result File中填入结果保存文件；注意：这个文件必须存在。</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
在Query中填入要执行的HQL语句；</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
Start Query选择 YES；</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
点击Submit开始执行HQL语句。</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
<img class="aligncenter" src="http://7xipth.com1.z0.glb.clouddn.com/0720-5.jpg" alt="hive hwi" width="481" height="500" style="vertical-align:middle;border:0px;display:block;text-align:center;"></p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
上面会提示会话处于 QUERY_RUNNING 的状态，这个状态下，不能做修改。</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
点击左侧的List Sessions，会显示每个Session的当前状态：</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
<img class="aligncenter" src="http://7xipth.com1.z0.glb.clouddn.com/0720-6.jpg" alt="hive hwi" width="499" height="157" style="vertical-align:middle;border:0px;display:block;text-align:center;"></p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
当Status为READY状态时候，表示前面的查询已经执行完，点击Manager进入会话管理页面，再点击Result File后面的 View File，查看执行结果：</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
<img class="aligncenter" src="http://7xipth.com1.z0.glb.clouddn.com/0720-7.jpg" alt="hive hwi" width="247" height="99" style="vertical-align:middle;border:0px;display:block;text-align:center;"></p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
 </p>
<ul style="list-style:none;color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;"><li style="line-height:30px;text-indent:0px;"><strong>浏览数据库</strong></li></ul><p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
点击左侧的Browse Schema，浏览Hive中所有的数据库：</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
<img class="aligncenter" src="http://7xipth.com1.z0.glb.clouddn.com/0720-8.jpg" alt="hive hwi" width="331" height="288" style="vertical-align:middle;border:0px;display:block;text-align:center;"></p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
点击一个数据库，可浏览该数据库下所有的表：</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
<img class="aligncenter" src="http://7xipth.com1.z0.glb.clouddn.com/0720-9.jpg" alt="hive hwi" width="333" height="215" style="vertical-align:middle;border:0px;display:block;text-align:center;"></p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
点击某一个表，可查看该表的元数据：</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
<img class="aligncenter" src="http://7xipth.com1.z0.glb.clouddn.com/0720-10.jpg" alt="hive hwi" width="356" height="351" style="vertical-align:middle;border:0px;display:block;text-align:center;"></p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
我觉得如果将HWI直接用于生产环境还是有问题的，比如：安全性，易用性等等。</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
在其上做一些封装的话，还是可行的。</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;text-indent:30px;">
 </p>
            </div>
                </div>