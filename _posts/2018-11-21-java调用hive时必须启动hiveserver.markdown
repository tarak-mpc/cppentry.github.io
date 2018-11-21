---
layout:     post
title:      java调用hive时必须启动hiveserver
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="postTitle" style="font-size:14.7px;font-weight:bold;color:rgb(75,75,75);font-family:Verdana, Arial, Helvetica, sans-serif;">
<h1 style="font-size:14.7px;color:rgb(102,102,102);"><a id="cb_post_title_url" class="postTitle2" href="http://www.cnblogs.com/sh91/archive/2012/08/03/2621911.html" rel="nofollow" style="text-decoration:none;color:rgb(52,104,164);">hiveserver启动方式</a></h1>
</div>
<div id="cnblogs_post_body" style="color:rgb(75,75,75);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:20.799999237060547px;">
<p style="font-size:12px;line-height:18px;font-family:Arial, Verdana, sans-serif;color:rgb(0,0,0);">
<span style="line-height:25px;font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:15px;">1， hive  命令行模式，直接输入/hive/bin/hive的执行程序，或者输入 hive –service cli</span></p>
<p style="font-size:12px;line-height:18px;font-family:Arial, Verdana, sans-serif;color:rgb(0,0,0);">
<span style="line-height:25px;font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:15px;">       用于linux平台命令行查询，查询语句基本跟mysql查询语句类似</span></p>
<p style="font-size:12px;line-height:18px;font-family:Arial, Verdana, sans-serif;color:rgb(0,0,0);">
<span style="font-size:15px;"><span style="line-height:25px;font-family:Helvetica, Tahoma, Arial, sans-serif;"> 2，</span><span style="line-height:25px;font-family:Helvetica, Tahoma, Arial, sans-serif;"> hive  web界面的启动方式，hive
 –service hwi  </span></span></p>
<p style="font-size:12px;line-height:18px;font-family:Arial, Verdana, sans-serif;color:rgb(0,0,0);">
<span style="line-height:25px;font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:15px;">      用于通过浏览器来访问hive，感觉没多大用途</span></p>
<p style="font-size:12px;line-height:18px;font-family:Arial, Verdana, sans-serif;color:rgb(0,0,0);">
<span style="line-height:25px;font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:15px;">3， hive  远程服务 (端口号10000) 启动方式， hive –service hiveserver  &amp; </span></p>
<p style="font-size:12px;line-height:18px;font-family:Arial, Verdana, sans-serif;color:rgb(0,0,0);">
<span style="line-height:25px;font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:15px;">      用java等程序实现通过jdbc等驱动的访问hive就用这种起动方式了，这个是程序员最需要的方式了</span></p>
<p style="font-size:12px;line-height:18px;font-family:Arial, Verdana, sans-serif;color:rgb(0,0,0);">
<span style="line-height:25px;font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:15px;"> 　 也可以自己指定端口 hive --service hiveserver -p 50000 &amp;  (&amp;表示后台运行)</span></p>
<p style="font-size:12px;line-height:18px;font-family:Arial, Verdana, sans-serif;color:rgb(0,0,0);">
<span style="line-height:25px;font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:15px;">　　输入完这些指令后终端就在运行hiveserver了，会卡住不动。其实已经在运行了，不用担心。</span></p>
</div>
            </div>
                </div>