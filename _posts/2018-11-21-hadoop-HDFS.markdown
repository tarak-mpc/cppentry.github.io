---
layout:     post
title:      hadoop-HDFS
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>输入hadoop可显示所有command，输入hadoop fs可显示此command的具体所有命令；输入hadoop fs -help ls显示ls命令的意思和具体简介（可看到默认路径为/user/root）。</p>
<p>1、使用hadoop用户登陆，执行hadoop命令（并不是linux的命令）；</p>
<p>显示文件：比如hadoop fs -ls / 显示hdfs根目录下的内容，可用hadoop fs -lsr / 递归显示根目录以及其下属目录。hadoop fs -ls /file用来查看hdfs上根目录下files文件夹下的内容。</p>
<p>创建文件夹：hadoop fs -mkdir /file在hdfs根目录上创建了file文件夹</p>
<p>2、上传文件：hadoop fs -put README.txt /file上传linux文件到hdfs上的file文件夹下，如果file不存在，则把file作为一个文件名来把README.txt数据上传到此文件；</p>
<p>3、下载文件：hadoop fs -get /file/README.txt .把文件下载到linux当前目录；（hdfs直接查看文件内容为hadoop fs -text /file/README.txt ）</p>
<p><br></p>
            </div>
                </div>