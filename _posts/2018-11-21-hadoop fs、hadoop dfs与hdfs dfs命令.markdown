---
layout:     post
title:      hadoop fs、hadoop dfs与hdfs dfs命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">
<span style="font-size:24px;"><span style="color:rgb(255,0,0);">hadoop fs</span>：使用面最广，可以操作任何文件系统。</span></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">
<span style="font-size:24px;">hadoop dfs与<span style="color:rgb(255,0,0);">hdfs dfs</span>：<strong>只能操作HDFS文件系统相关</strong></span></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">
</p>
<ol style="color:rgb(69,69,69);font-family:Arial, 'Liberation Sans', 'DejaVu Sans', sans-serif;background:transparent;border:0px;vertical-align:baseline;"><li style="list-style:decimal;border:0px;vertical-align:baseline;background:transparent;">
<span style="border:0px;vertical-align:baseline;background:transparent;"><span style="font-size:24px;">hadoop fs {args}</span></span></li><li style="list-style:decimal;border:0px;vertical-align:baseline;background:transparent;">
<span style="border:0px;vertical-align:baseline;background:transparent;"><span style="font-size:24px;">hadoop dfs {args}</span></span></li><li style="list-style:decimal;border:0px;vertical-align:baseline;background:transparent;">
<p style="border:0px;vertical-align:baseline;clear:both;background:transparent;">
<span style="border:0px;vertical-align:baseline;background:transparent;"><span style="font-size:24px;">hdfs dfs {args}</span></span></p>
</li></ol><br><p></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">
</p>
<h3 class="h4" style="font-family:'Trebuchet MS', verdana, arial, helvetica, sans-serif;font-size:16.64px;">
cat</h3>
<p></p>
<p style="line-height:15.36px;font-family:Verdana, Helvetica, sans-serif;font-size:12.8px;">
<span class="codefrag" style="font-family:'Courier New', Courier, monospace;font-size:14.08px;">使用方法：hadoop fs -cat URI</span></p>
<h3 class="h4" style="font-family:'Trebuchet MS', verdana, arial, helvetica, sans-serif;font-size:16.64px;">
chgrp</h3>
<p style="line-height:15.36px;font-family:Verdana, Helvetica, sans-serif;font-size:12.8px;">
<span class="codefrag" style="font-family:'Courier New', Courier, monospace;font-size:14.08px;">使用方法：hadoop fs -chgrp [-R] GROUP URI</span></p>
<h3 class="h4" style="font-family:'Trebuchet MS', verdana, arial, helvetica, sans-serif;font-size:16.64px;">
copyFromLocal</h3>
<p style="line-height:15.36px;font-family:Verdana, Helvetica, sans-serif;font-size:12.8px;">
<span class="codefrag" style="font-family:'Courier New', Courier, monospace;font-size:14.08px;">使用方法：hadoop fs -copyFromLocal &lt;localsrc&gt; URI</span></p>
<p style="line-height:15.36px;font-family:Verdana, Helvetica, sans-serif;font-size:12.8px;">
除了限定源路径是一个本地文件外，和<a href="http://hadoop.apache.org/docs/r1.0.4/cn/hdfs_shell.html#putlink" rel="nofollow" style="color:rgb(0,153,153);"><strong>put</strong></a>命令相似。</p>
<h3 class="h4" style="font-family:'Trebuchet MS', verdana, arial, helvetica, sans-serif;font-size:16.64px;">
copyToLocal</h3>
<p style="line-height:15.36px;font-family:Verdana, Helvetica, sans-serif;font-size:12.8px;">
<span class="codefrag" style="font-family:'Courier New', Courier, monospace;font-size:14.08px;">使用方法：hadoop fs -copyToLocal [-ignorecrc] [-crc] URI &lt;localdst&gt;</span></p>
<p style="line-height:15.36px;font-family:Verdana, Helvetica, sans-serif;font-size:12.8px;">
除了限定目标路径是一个本地文件外，和<a href="http://hadoop.apache.org/docs/r1.0.4/cn/hdfs_shell.html#getlink" rel="nofollow" style="color:rgb(0,153,153);"><strong>get</strong></a>命令类似。</p>
<h3 class="h4" style="font-family:'Trebuchet MS', verdana, arial, helvetica, sans-serif;font-size:16.64px;">
du</h3>
<p style="line-height:15.36px;font-family:Verdana, Helvetica, sans-serif;font-size:12.8px;">
<span class="codefrag" style="font-family:'Courier New', Courier, monospace;font-size:14.08px;">使用方法：hadoop fs -du URI [URI …]</span></p>
<p style="line-height:15.36px;font-family:Verdana, Helvetica, sans-serif;font-size:12.8px;">
显示目录中所有文件的大小，或者当只指定一个文件时，显示此文件的大小。</p>
<h3 class="h4" style="font-family:'Trebuchet MS', verdana, arial, helvetica, sans-serif;font-size:16.64px;">
expunge</h3>
<p style="line-height:15.36px;font-family:Verdana, Helvetica, sans-serif;font-size:12.8px;">
<span class="codefrag" style="font-family:'Courier New', Courier, monospace;font-size:14.08px;">使用方法：hadoop fs -expunge</span></p>
<p style="line-height:15.36px;font-family:Verdana, Helvetica, sans-serif;font-size:12.8px;">
清空回收站。请参考<a href="http://hadoop.apache.org/docs/r1.0.4/cn/hdfs_design.html" rel="nofollow" style="color:rgb(0,153,153);">HDFS设计</a>文档以获取更多关于回收站特性的信息。</p>
<br>            </div>
                </div>