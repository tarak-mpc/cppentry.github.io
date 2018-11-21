---
layout:     post
title:      Hadoop对hdfs操作的命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span style="font-size:18px;">1.对hdfs操作的命令格式是hadoop fs <br><span></span>1.1 -ls<span> </span>
&lt;path&gt;<span> </span>表示对hdfs下一级目录的查看<br><span></span>1.2 -lsr<span> </span>
&lt;path&gt;<span> </span>表示对hdfs目录的递归查看<br><span></span>1.3<span> </span>-mkdir<span>
</span>&lt;path&gt;<span> </span>创建目录<br><span></span>1.4 -put<span> </span>
&lt;src&gt;<span> </span>&lt;des&gt;<span> </span>
从linux上传文件到hdfs<br><span></span>1.5 -get<span> </span>
&lt;src&gt;<span> </span>&lt;des&gt;<span> </span>
从hdfs下载文件到linux<br><span></span>1.6 -text<span> </span>
&lt;path&gt;<span> </span>查看文件内容<br><span></span>1.7 -rm<span> </span>
&lt;path&gt;<span> </span>表示删除文件<br><span></span>1.7 -rmr<span> </span>
&lt;path&gt;<span> </span>表示递归删除文件<br></span>
<p><span style="font-size:18px;">2.hdfs在对数据存储进行block划分时，如果文件大小超过block，那么按照block大小进行划分；不如block size的，划分为一个块，是实际数据大小。</span></p>
<p><span style="font-size:18px;">例如：</span></p>
<p><span style="font-size:18px;"> --查看命令<br>
hadoop fs -lsr /<br>
hadoop fs -put hello /<br>
hadoop fs -text /hello<br>
hadoop fs -rmr hdfs://centos:9000/*</span></p>
<p><span style="font-size:18px;"><span style="font-size:18px;">hadoop fs -put hello /操作的时候，可能说权限不够，离开安全模式的命令：</span><br></span></p>
<p><span style="font-size:18px;"><span style="font-size:18px;">hadoop dfsadmin -safemode leave</span></span></p>
<p><span style="font-size:18px;"><br></span></p>
            </div>
                </div>