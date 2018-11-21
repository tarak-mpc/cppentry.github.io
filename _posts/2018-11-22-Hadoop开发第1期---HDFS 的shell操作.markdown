---
layout:     post
title:      Hadoop开发第1期---HDFS 的shell操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2 style="font-size:14px;font-family:Verdana, Arial, Helvetica, sans-serif;line-height:25.2000007629395px;">
<a id="cb_post_title_url" href="http://www.cnblogs.com/sunddenly/p/3981583.html" rel="nofollow">Hadoop开发第期---HDFS 的shell操作</a></h2>
<div class="postbody" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25.2000007629395px;">
<div id="cnblogs_post_body">
<h1 style="font-size:23px;clear:both;color:#FFFFFF;letter-spacing:1px;font-family:'Microsoft Yahei';text-indent:10px;background:rgb(32,55,95) none repeat scroll 0px 0px;">
一、HDFS的shell命令简介</h1>
<blockquote style="border:1px dashed rgb(0,0,0);color:rgb(51,51,51);">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
我们都知道HDFS 是存取数据的分布式文件系统，那么对HDFS 的操作，就是文件系统的基本操作，比如文件的创建、修改、删除、修改权限等，文件夹的创建、删除、重命名等。对HDFS 的操作命令类似于lLinux 的shell 对文件的操作，如ls、mkdir、rm 等。<br>
我们在执行HDFS的shell操作的时候，<span style="text-decoration:underline;">一定要确定hadoop 是正常运行的</span>，我们可以使用使用jps 命令确保看到各个hadoop 进程。<br>
我们可以执行命令hadoop fs，来查看HDFS的shell操作的所有命令如下。<br>
[root@hadoop ~]# hadoop fs<br>
Usage: java FsShell<br>
           [-ls &lt;path&gt;]<br>
           [-lsr &lt;path&gt;]<br>
           [-du &lt;path&gt;]<br>
           [-dus &lt;path&gt;]<br>
           [-count[-q] &lt;path&gt;]<br>
           [-mv &lt;src&gt; &lt;dst&gt;]<br>
           [-cp &lt;src&gt; &lt;dst&gt;]<br>
           [-rm [-skipTrash] &lt;path&gt;]<br>
           [-rmr [-skipTrash] &lt;path&gt;]<br>
           [-expunge]<br>
           [-put &lt;localsrc&gt; ... &lt;dst&gt;]<br>
           [-copyFromLocal &lt;localsrc&gt; ... &lt;dst&gt;]<br>
           [-moveFromLocal &lt;localsrc&gt; ... &lt;dst&gt;]<br>
           [-get [-ignoreCrc] [-crc] &lt;src&gt; &lt;localdst&gt;]<br>
           [-getmerge &lt;src&gt; &lt;localdst&gt; [addnl]]<br>
           [-cat &lt;src&gt;]<br>
           [-text &lt;src&gt;]<br>
           [-copyToLocal [-ignoreCrc] [-crc] &lt;src&gt; &lt;localdst&gt;]<br>
           [-moveToLocal [-crc] &lt;src&gt; &lt;localdst&gt;]<br>
           [-mkdir &lt;path&gt;]<br>
           [-setrep [-R] [-w] &lt;rep&gt; &lt;path/file&gt;]<br>
           [-touchz &lt;path&gt;]<br>
           [-test -[ezd] &lt;path&gt;]<br>
           [-stat [format] &lt;path&gt;]<br>
           [-tail [-f] &lt;file&gt;]<br>
           [-chmod [-R] &lt;MODE[,MODE]... | OCTALMODE&gt; PATH...]<br>
           [-chown [-R] [OWNER][:[GROUP]] PATH...]<br>
           [-chgrp [-R] GROUP PATH...]<br>
           [-help [cmd]]</p>
</blockquote>
<h1 style="font-size:23px;clear:both;color:#FFFFFF;letter-spacing:1px;font-family:'Microsoft Yahei';text-indent:10px;background:rgb(32,55,95) none repeat scroll 0px 0px;">
二、HDFS的shell操作命令选项</h1>
<table border="1" cellspacing="0" cellpadding="0" style="border-width:1px;border-style:solid;border-color:rgb(72,61,139) #C0C0C0;border-collapse:collapse;"><tbody><tr><td width="115" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
选项名称</p>
</td>
<td width="403" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
使用格式</p>
</td>
<td width="163" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
含义</p>
</td>
</tr><tr><td valign="top" width="115" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-ls</p>
</td>
<td valign="top" width="403" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-ls &lt;路径&gt;</p>
</td>
<td valign="top" width="163" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
查看指定路径的当前目录结构</p>
</td>
</tr><tr><td valign="top" width="115" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-lsr</p>
</td>
<td valign="top" width="403" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-lsr &lt;路径&gt;</p>
</td>
<td valign="top" width="163" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
递归查看指定路径的目录结构</p>
</td>
</tr><tr><td valign="top" width="115" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-du</p>
</td>
<td valign="top" width="403" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-du &lt;路径&gt;</p>
</td>
<td valign="top" width="163" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
统计目录下各文件大小</p>
</td>
</tr><tr><td valign="top" width="115" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-dus</p>
</td>
<td valign="top" width="403" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-dus &lt;路径&gt;</p>
</td>
<td valign="top" width="163" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
汇总统计目录下文件(夹)大小</p>
</td>
</tr><tr><td valign="top" width="115" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-count</p>
</td>
<td valign="top" width="403" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-count [-q] &lt;路径&gt;</p>
</td>
<td valign="top" width="163" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
统计文件(夹)数量</p>
</td>
</tr><tr><td valign="top" width="115" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-mv</p>
</td>
<td valign="top" width="403" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-mv &lt;源路径&gt; &lt;目的路径&gt;</p>
</td>
<td valign="top" width="163" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
移动</p>
</td>
</tr><tr><td valign="top" width="115" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-cp</p>
</td>
<td valign="top" width="403" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-cp &lt;源路径&gt; &lt;目的路径&gt;</p>
</td>
<td valign="top" width="163" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
复制</p>
</td>
</tr><tr><td valign="top" width="115" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-rm</p>
</td>
<td valign="top" width="403" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-rm [-skipTrash] &lt;路径&gt;</p>
</td>
<td valign="top" width="163" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
删除文件/空白文件夹</p>
</td>
</tr><tr><td valign="top" width="115" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-rmr</p>
</td>
<td valign="top" width="403" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-rmr [-skipTrash] &lt;路径&gt;</p>
</td>
<td valign="top" width="163" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
递归删除</p>
</td>
</tr><tr><td valign="top" width="115" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-put</p>
</td>
<td valign="top" width="403" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-put &lt;多个linux上的文件&gt; &lt;hdfs 路径&gt;</p>
</td>
<td valign="top" width="163" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
上传文件</p>
</td>
</tr><tr><td valign="top" width="115" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-copyFromLocal</p>
</td>
<td valign="top" width="403" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-copyFromLocal &lt;多个linux 上的文件&gt; &lt;hdfs 路径&gt;</p>
</td>
<td valign="top" width="163" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
从本地复制</p>
</td>
</tr><tr><td valign="top" width="115" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-moveFromLocal</p>
</td>
<td valign="top" width="403" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-moveFromLocal &lt;多个linux 上的文件&gt; &lt;hdfs 路径&gt;</p>
</td>
<td valign="top" width="163" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
从本地移动</p>
</td>
</tr><tr><td valign="top" width="115" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-getmerge</p>
</td>
<td valign="top" width="403" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-getmerge &lt;源路径&gt; &lt;linux 路径&gt;</p>
</td>
<td valign="top" width="163" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
合并到本地</p>
</td>
</tr><tr><td valign="top" width="115" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-cat</p>
</td>
<td valign="top" width="403" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-cat &lt;hdfs 路径&gt;</p>
</td>
<td valign="top" width="163" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
查看文件内容</p>
</td>
</tr><tr><td valign="top" width="115" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-text</p>
</td>
<td valign="top" width="403" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-text &lt;hdfs 路径&gt;</p>
</td>
<td valign="top" width="163" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
查看文件内容</p>
</td>
</tr><tr><td valign="top" width="115" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-copyToLocal</p>
</td>
<td valign="top" width="403" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-copyToLocal [-ignoreCrc] [-crc] [hdfs 源路径] [linux 目的路径]</p>
</td>
<td valign="top" width="163" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
复制到本地</p>
</td>
</tr><tr><td valign="top" width="115" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-moveToLocal</p>
</td>
<td valign="top" width="403" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-moveToLocal [-crc] &lt;hdfs 源路径&gt; &lt;linux目的路径&gt;</p>
</td>
<td valign="top" width="163" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
移动到本地</p>
</td>
</tr><tr><td valign="top" width="115" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-setrep</p>
</td>
<td valign="top" width="403" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-setrep [-R] [-w] &lt;副本数&gt; &lt;路径&gt;</p>
</td>
<td valign="top" width="163" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
修改副本数量</p>
</td>
</tr><tr><td valign="top" width="115" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-mkdir</p>
</td>
<td valign="top" width="403" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-mkdir &lt;hdfs 路径&gt;</p>
</td>
<td valign="top" width="163" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
创建空白文件夹</p>
</td>
</tr><tr><td valign="top" width="115" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-touchz</p>
</td>
<td valign="top" width="403" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-touchz &lt;文件路径&gt;</p>
</td>
<td valign="top" width="163" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
创建空白文件</p>
</td>
</tr><tr><td valign="top" width="115" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-stat</p>
</td>
<td valign="top" width="403" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-stat [format] &lt;路径&gt;</p>
</td>
<td valign="top" width="163" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
显示文件统计信息</p>
</td>
</tr><tr><td valign="top" width="115" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-tail</p>
</td>
<td valign="top" width="403" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-tail [-f] &lt;文件&gt;</p>
</td>
<td valign="top" width="163" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
查看文件尾部信息</p>
</td>
</tr><tr><td valign="top" width="115" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-chmod</p>
</td>
<td valign="top" width="403" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-chmod [-R] &lt;权限模式&gt; [路径]</p>
</td>
<td valign="top" width="163" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
修改权限</p>
</td>
</tr><tr><td valign="top" width="115" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-chown</p>
</td>
<td valign="top" width="403" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-chown [-R] [属主][:[属组]]路径</p>
</td>
<td valign="top" width="163" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
修改属主</p>
</td>
</tr><tr><td valign="top" width="115" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-chgrp</p>
</td>
<td valign="top" width="403" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-chgrp [-R] 属组名称 路径</p>
</td>
<td valign="top" width="163" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
修改属组</p>
</td>
</tr><tr><td valign="top" width="115" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-help</p>
</td>
<td valign="top" width="403" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
-help -help [命令选项]</p>
</td>
<td valign="top" width="163" style="font-size:14px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
帮助</p>
</td>
</tr></tbody></table><p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
 </p>
<h1 style="font-size:23px;clear:both;color:#FFFFFF;letter-spacing:1px;font-family:'Microsoft Yahei';text-indent:10px;background:rgb(32,55,95) none repeat scroll 0px 0px;">
三、各命令选项的用法</h1>
<h2 style="font-size:22px;clear:both;color:#FFFFFF;letter-spacing:1px;font-family:'Microsoft Yahei';text-indent:15px;margin-left:0px !important;background:rgb(32,55,95) none repeat scroll 0px 0px;">
1. ls 显示当前目录结构</h2>
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<strong>(1)</strong> 该命令选项表示查看指定路径的当前目录结构，后面跟hdfs 路径，如果图3.1所示。</p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<img title="image" src="http://images.cnitblog.com/blog/671563/201409/191531457537947.png" alt="image" width="646" height="119" border="0" style="border:0px;display:inline;"></p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
图 3.1</p>
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
我们解释一下每一行的<span style="color:rgb(0,0,255);">内容格式：</span></p>
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
① 首字母表示文件夹(如果是“d”)还是文件（如果是“-”）；<br>
② 后面的9 位字符表示权限；<br>
③ 后面的数字或者“-”表示副本数。如果是文件，使用数字表示副本数；文件夹没有副本；<br>
④ 后面的“root”表示属主；<br>
⑤ 后面的“supergroup”表示属组；<br>
⑥ 后面的“0”、“ 84927175”表示文件大小，单位是字节；<br>
⑦ 后面的时间表示修改时间，格式是年月日时分；<br>
⑧ 最后一项表示文件路径。<br>
可见根目录下面有1个文件夹、1个文件。</p>
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<strong>(2)</strong> 如果该命<span style="color:rgb(255,0,0);">令选项后面没有路径</span>，那么就会访问<span style="color:rgb(0,0,255);">/user/&lt;当前用户&gt;</span>目录。我们使用root用户登录，因此会访问hdfs 的<span style="color:rgb(0,0,255);">/user/root </span>目录，但是如果<span style="color:rgb(255,0,0);">没有</span>这个目录/user/root，会提示文件不存在的错误如图3.2所示，添加该目录后再次执行该命令如图3.3,3.4所示。</p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<img title="image" src="http://images.cnitblog.com/blog/671563/201409/191531466596505.png" alt="image" width="658" height="98" border="0" style="border:0px;display:inline;"></p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
图 3.2</p>
<p align="center" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<img title="image" src="http://images.cnitblog.com/blog/671563/201409/191531473317133.png" alt="image" width="658" height="115" border="0" style="border:0px none;display:block;margin-left:auto;"></p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
图 3.3</p>
<p align="center" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<img title="image" src="http://images.cnitblog.com/blog/671563/201409/191531481905004.png" alt="image" width="656" height="123" border="0" style="border:0px none;display:block;margin-left:auto;"></p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
图 3.4</p>
<h2 align="left" style="font-size:22px;clear:both;color:#FFFFFF;letter-spacing:1px;font-family:'Microsoft Yahei';text-indent:15px;margin-left:0px !important;background:rgb(32,55,95) none repeat scroll 0px 0px;">
2. -lsr 递归显示目录结构</h2>
<p align="left" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
该命令选项表示递归显示当前路径的目录结构，后面跟hdfs 路径。如图3.5 所示。</p>
<p align="center" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<img title="image" src="http://images.cnitblog.com/blog/671563/201409/191531490036888.png" alt="image" width="650" height="104" border="0" style="border:0px none;display:block;margin-left:auto;"></p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
图 3.5</p>
<p align="left" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
显示/user 目录下有个root 目录，root 目录下有文件hello</p>
<h2 align="left" style="font-size:22px;clear:both;color:#FFFFFF;letter-spacing:1px;font-family:'Microsoft Yahei';text-indent:15px;margin-left:0px !important;background:rgb(32,55,95) none repeat scroll 0px 0px;">
3. -du 统计目录下各文件大小</h2>
<p align="left" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
该命令选项显示指定路径下的文件大小，单位是字节，如图3.6所示。</p>
<p align="center" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<img title="image" src="http://images.cnitblog.com/blog/671563/201409/191531498154474.png" alt="image" width="648" height="165" border="0" style="border:0px none;display:block;margin-left:auto;"></p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
图 3.6</p>
<h2 align="left" style="font-size:22px;clear:both;color:#FFFFFF;letter-spacing:1px;font-family:'Microsoft Yahei';text-indent:15px;margin-left:0px !important;background:rgb(32,55,95) none repeat scroll 0px 0px;">
4. -dus 汇总统计目录下文件大小</h2>
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
该命令选项显示指定路径的文件大小，单位是字节，如图3.7所示。</p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<img title="image" src="http://images.cnitblog.com/blog/671563/201409/191531506593817.png" alt="image" width="650" height="136" border="0" style="border:0px none;display:inline;"></p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
图 3.7</p>
<p align="left" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
请读者比较图3.6与图3.7的区别，体会两个命令选项的不同含义。</p>
<h2 align="left" style="font-size:22px;clear:both;color:#FFFFFF;letter-spacing:1px;font-family:'Microsoft Yahei';text-indent:15px;margin-left:0px !important;background:rgb(32,55,95) none repeat scroll 0px 0px;">
5. count 统计文件(夹)数量</h2>
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
该命令选项显示指定路径下的文件夹数量、文件数量、文件总大小信息，如图3.8所示.。</p>
<p align="center" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<img title="image" src="http://images.cnitblog.com/blog/671563/201409/191531516597644.png" alt="image" width="644" height="258" border="0" style="border:0px none;display:block;margin-left:auto;"></p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
图 3.8</p>
<p align="left" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
在图4-6 中有两条命令，下面的命令是为了佐证上面命令的正确性的。</p>
<h2 style="font-size:22px;clear:both;color:#FFFFFF;letter-spacing:1px;font-family:'Microsoft Yahei';text-indent:15px;margin-left:0px !important;background:rgb(32,55,95) none repeat scroll 0px 0px;">
6.  mv 移动</h2>
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
　　该命令选项表示移动hdfs 的文件到指定的hdfs 目录中。后面跟两个路径，第一个表示源文件，第二个表示目的目录。如图3.9所示。</p>
<p align="center" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<img title="image" src="http://images.cnitblog.com/blog/671563/201409/191531528622943.png" alt="image" width="654" height="177" border="0" style="border:0px none;display:block;margin-left:auto;"></p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
图 3.9</p>
<p align="left" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
在图3.9 中有三条命令，是为了体现移动前后的变化情况。</p>
<h2 align="left" style="font-size:22px;clear:both;color:#FFFFFF;letter-spacing:1px;font-family:'Microsoft Yahei';text-indent:15px;margin-left:0px !important;background:rgb(32,55,95) none repeat scroll 0px 0px;">
7. cp 复制</h2>
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
该命令选项表示复制hdfs 指定的文件到指定的hdfs 目录中。后面跟两个路径，第一个是被复制的文件，第二个是目的地。，如图3.10所示。.</p>
<p align="center" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<img title="image" src="http://images.cnitblog.com/blog/671563/201409/191531539092757.png" alt="image" width="642" height="225" border="0" style="border:0px none;display:block;margin-left:auto;"></p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
图 3.10</p>
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
在图3.10中有三条命令，是为了体现复制前后的变化情况。</p>
<h2 style="font-size:22px;clear:both;color:#FFFFFF;letter-spacing:1px;font-family:'Microsoft Yahei';text-indent:15px;margin-left:0px !important;background:rgb(32,55,95) none repeat scroll 0px 0px;">
8. rm 删除文件/空白文件夹</h2>
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
该命令选项表示删除指定的文件或者空目录，如图3.11 所示。.</p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<img title="image" src="http://images.cnitblog.com/blog/671563/201409/191531552682541.png" alt="image" width="646" height="255" border="0" style="border:0px;display:inline;"></p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
图 3.11</p>
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
在图3.11 中，前三条命令是为了体现执行前后的变化情况。第四条命令是删除非空的“/user”目录，操作失败，表明不能删除非空目录。</p>
<h2 style="font-size:22px;clear:both;color:#FFFFFF;letter-spacing:1px;font-family:'Microsoft Yahei';text-indent:15px;margin-left:0px !important;background:rgb(32,55,95) none repeat scroll 0px 0px;">
9.  rmr 递归删除</h2>
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
该命令选项表示递归删除指定目录下的所有子目录和文件，如图3.12 所示。</p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<img title="image" src="http://images.cnitblog.com/blog/671563/201409/191531562062856.png" alt="image" width="646" height="207" border="0" style="border:0px;display:inline;"></p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
图 3.12</p>
<h2 style="font-size:22px;clear:both;color:#FFFFFF;letter-spacing:1px;font-family:'Microsoft Yahei';text-indent:15px;margin-left:0px !important;background:rgb(32,55,95) none repeat scroll 0px 0px;">
10. put 上传文件</h2>
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
该命令选项表示把linux 上的文件复制到hdfs 中，如图3.12所示。.</p>
<p align="center" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<img title="image" src="http://images.cnitblog.com/blog/671563/201409/191531570659725.png" alt="image" width="646" height="153" border="0" style="border:0px none;display:block;margin-left:auto;"></p>
<p align="center" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
图 3.12</p>
<h2 align="left" style="font-size:22px;clear:both;color:#FFFFFF;letter-spacing:1px;font-family:'Microsoft Yahei';text-indent:15px;margin-left:0px !important;background:rgb(32,55,95) none repeat scroll 0px 0px;">
11. copyFromLocal 从本地复制到hdfs</h2>
<p align="left" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
操作与-put 一致，不再举例。</p>
<h2 align="left" style="font-size:22px;clear:both;color:#FFFFFF;letter-spacing:1px;font-family:'Microsoft Yahei';text-indent:15px;margin-left:0px !important;background:rgb(32,55,95) none repeat scroll 0px 0px;">
12. moveFromLocal 从本地移动到hdfs</h2>
<p align="left" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
该命令表示把文件从linux 上移动到hdfs 中，如图3.13所示。</p>
<p align="center" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<img title="image" src="http://images.cnitblog.com/blog/671563/201409/191531580188567.png" alt="image" width="648" height="210" border="0" style="border:0px none;display:block;margin-left:auto;"></p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
图 3.13</p>
<h2 align="left" style="font-size:22px;clear:both;color:#FFFFFF;letter-spacing:1px;font-family:'Microsoft Yahei';text-indent:15px;margin-left:0px !important;background:rgb(32,55,95) none repeat scroll 0px 0px;">
13.  getmerge 合并到本地</h2>
<p align="left" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
该命令选项的含义是把hdfs 指定目录下的所有文件内容合并到本地linux 的文件中，如图3.14所示。</p>
<p align="center" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<img title="image" src="http://images.cnitblog.com/blog/671563/201409/191531587063425.png" alt="image" width="644" height="179" border="0" style="border:0px none;display:block;margin-left:auto;"></p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
图 3.14</p>
<h2 align="left" style="font-size:22px;clear:both;color:#FFFFFF;letter-spacing:1px;font-family:'Microsoft Yahei';text-indent:15px;margin-left:0px !important;background:rgb(32,55,95) none repeat scroll 0px 0px;">
14.  cat 查看文件内容</h2>
<p align="left" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
该命令选项是查看文件内容，如图3.15所示。</p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<img title="image" src="http://images.cnitblog.com/blog/671563/201409/191531594561796.png" alt="image" width="644" height="140" border="0" style="border:0px;display:inline;"></p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
图 3.15</p>
<h2 align="left" style="font-size:22px;clear:both;color:#FFFFFF;letter-spacing:1px;font-family:'Microsoft Yahei';text-indent:15px;margin-left:0px !important;background:rgb(32,55,95) none repeat scroll 0px 0px;">
 15. text 查看文件内容</h2>
<p align="left" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
该命令选项可以认为作用和用法与-cat 相同，此处略。</p>
<h2 align="left" style="font-size:22px;clear:both;color:#FFFFFF;letter-spacing:1px;font-family:'Microsoft Yahei';text-indent:15px;margin-left:0px !important;background:rgb(32,55,95) none repeat scroll 0px 0px;">
16. mkdir 创建空白文件夹</h2>
<p align="left" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
该命令选项表示创建文件夹，后面跟的路径是在hdfs 将要创建的文件夹，如图3.16 所示。</p>
<p align="center" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<img title="image" src="http://images.cnitblog.com/blog/671563/201409/191532002371924.png" alt="image" width="650" height="253" border="0" style="border:0px none;display:block;margin-left:auto;"></p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
图 3.16</p>
<h2 align="left" style="font-size:22px;clear:both;color:#FFFFFF;letter-spacing:1px;font-family:'Microsoft Yahei';text-indent:15px;margin-left:0px !important;background:rgb(32,55,95) none repeat scroll 0px 0px;">
17. setrep 设置副本数量</h2>
<p align="left" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<strong>(1)</strong> 该命令选项是修改已保存文件的副本数量，后面跟副本数量，再跟文件路径，如图3.17所示。</p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<img title="image" src="http://images.cnitblog.com/blog/671563/201409/191532010657037.png" alt="image" width="650" height="226" border="0" style="border:0px none;display:block;margin-left:auto;"></p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
图 3.17</p>
<p align="left" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
　　在图3.17 中，我们修改了文件/file1的副本数，由1 修改为2，意味着多了一个副本，HDFS 会自动执行文件的复制工作，产生新的副本。</p>
<p align="left" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<strong>(2)</strong> 如果最后的路径表示文件夹，那么需要跟选项-R，表示对文件夹中的所有文件都修改副<br>
本，如图3.18 所示</p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<img title="image" src="http://images.cnitblog.com/blog/671563/201409/191532019408136.png" alt="image" width="650" height="216" border="0" style="border:0px;display:inline;"></p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
图 3.18</p>
<p align="left" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
在图3.18中，我们对/user/root 文件夹进行的操作，使用了选项-R，那么/user/root 下的文件file2,file1 的副本数发生了改变。</p>
<p style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<strong>(3)</strong> 还有一个选项是-w，表示等待副本操作结束才退出命令，如图3.19所示。</p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<img title="image" src="http://images.cnitblog.com/blog/671563/201409/191532028159235.png" alt="image" width="652" height="134" border="0" style="border:0px;display:inline;"></p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
图 3.19</p>
<h2 align="left" style="font-size:22px;clear:both;color:#FFFFFF;letter-spacing:1px;font-family:'Microsoft Yahei';text-indent:15px;margin-left:0px !important;background:rgb(32,55,95) none repeat scroll 0px 0px;">
18.  touchz 创建空白文件</h2>
<p align="left" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
该命令选项是在hdfs 中创建空白文件，如图3.20 所示。</p>
<p align="center" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<img title="image" src="http://images.cnitblog.com/blog/671563/201409/191532036123593.png" alt="image" width="646" height="220" border="0" style="border:0px none;display:block;margin-left:auto;"></p>
<p align="center" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
图 3.20</p>
<h2 align="left" style="font-size:22px;clear:both;color:#FFFFFF;letter-spacing:1px;font-family:'Microsoft Yahei';text-indent:15px;margin-left:0px !important;background:rgb(32,55,95) none repeat scroll 0px 0px;">
19. stat 显示文件的统计信息</h2>
<p align="left" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
该命令选项显示文件的一些统计信息，如图3.21所示。</p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<img title="image" src="http://images.cnitblog.com/blog/671563/201409/191532043467734.png" alt="image" width="648" height="151" border="0" style="border:0px;display:inline;"></p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
图 3.21</p>
<p align="left" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
在图3.21 中，命令选项后面可以有格式，使用引号表示。示例中的格式<span style="color:rgb(0,0,255);">“%b %n %o%r %Y”</span>依次表示<span style="color:rgb(255,0,0);">文件大小、文件名称、块大小、副本数、访问时间.</span></p>
<h2 style="font-size:22px;clear:both;color:#FFFFFF;letter-spacing:1px;font-family:'Microsoft Yahei';text-indent:15px;margin-left:0px !important;background:rgb(32,55,95) none repeat scroll 0px 0px;">
 20. tail 查看文件尾部内容</h2>
<p align="left" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
该命令选项显示文件最后<span style="color:rgb(0,0,255);">1K 字节的内容</span>。一般用于查看日志。如果带有<span style="color:rgb(255,0,0);">选项-f</span>，那么当文件内容变化时，也会自动显示。如图3.22 所示。</p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<img title="image" src="http://images.cnitblog.com/blog/671563/201409/191532052531590.png" alt="image" width="640" height="357" border="0" style="border:0px none;display:inline;"></p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
图 3.22</p>
<h2 style="font-size:22px;clear:both;color:#FFFFFF;letter-spacing:1px;font-family:'Microsoft Yahei';text-indent:15px;margin-left:0px !important;background:rgb(32,55,95) none repeat scroll 0px 0px;">
 21.  chmod 修改文件权限</h2>
<p align="left" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<strong>(1)</strong> 该命令选项的使用类似于linux 的shell 中的chmod 用法，作用是修改文件的权限，如图3.23 所示。.</p>
<p align="center" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<img title="image" src="http://images.cnitblog.com/blog/671563/201409/191532067681561.png" alt="image" width="646" height="315" border="0" style="border:0px none;display:block;margin-left:auto;"></p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
图 3.23</p>
<p align="left" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<strong>(2)</strong> 在图3.23中，修改了文件/emptyfile 的权限。如果加上选项-R，可以对文件夹中的所有文件修改权限，如图3.24 所示。.</p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<img title="image" src="http://images.cnitblog.com/blog/671563/201409/191532077685389.png" alt="image" width="644" height="218" border="0" style="border:0px;display:inline;"></p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
图 3.24</p>
<h2 align="left" style="font-size:22px;clear:both;color:#FFFFFF;letter-spacing:1px;font-family:'Microsoft Yahei';text-indent:15px;margin-left:0px !important;background:rgb(32,55,95) none repeat scroll 0px 0px;">
22. chown 修改属主</h2>
<p align="left" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
该命令选项表示修改文件的属主，如图3.25所示。</p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<img title="image" src="http://images.cnitblog.com/blog/671563/201409/191532091126645.png" alt="image" width="646" height="198" border="0" style="border:0px;display:inline;"></p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
图 3.25</p>
<p align="left" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<strong>(2)</strong> 上图中把文件/emptyfile 的属主由root 修改为sunddenly。也可以同时修改属组，如图3.26 所示。</p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<img title="image" src="http://images.cnitblog.com/blog/671563/201409/191532099714516.png" alt="image" width="640" height="111" border="0" style="border:0px;display:inline;"></p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
图 3.26</p>
<p align="left" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
在图3.26中，把文件/emptyfile 的属主和属组都修改为itcast，如果只修改属组，可以使用“：sunddenly”。如果带有选项-R，意味着可以递归修改文件夹中的所有文件的属主、属组信息。</p>
<h2 align="left" style="font-size:22px;clear:both;color:#FFFFFF;letter-spacing:1px;font-family:'Microsoft Yahei';text-indent:15px;margin-left:0px !important;background:rgb(32,55,95) none repeat scroll 0px 0px;">
23. chgrp 修改属组</h2>
<p align="left" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
该命令的作用是修改文件的属组，该命令相当于“chown :属组”的用法，如图3.27所示。</p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<img title="image" src="http://images.cnitblog.com/blog/671563/201409/191532109094830.png" alt="image" width="644" height="167" border="0" style="border:0px;display:inline;"></p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
图 3.27</p>
<h2 align="left" style="font-size:22px;clear:both;color:#FFFFFF;letter-spacing:1px;font-family:'Microsoft Yahei';text-indent:15px;margin-left:0px !important;background:rgb(32,55,95) none repeat scroll 0px 0px;">
24. help 帮助</h2>
<p align="left" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
该命令选项会显示帮助信息，后面跟上需要查询的命令选项即可，如图4-27 所示。</p>
<p align="center" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
<img title="image" src="http://images.cnitblog.com/blog/671563/201409/191532116285742.png" alt="image" width="665" height="117" border="0" style="border:0px;margin-left:auto;display:block;"></p>
<p align="center" style="letter-spacing:1px;text-align:center;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
图 3.28</p>
<p align="left" style="letter-spacing:1px;text-align:justify;line-height:1.6;font-size:12pt;font-family:'Microsoft YaHei';">
在图3.28 中，查询的rm 的用法。</p>
</div>
</div>
            </div>
                </div>