---
layout:     post
title:      Hadoop之HDFS文件操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><img src="https://img-blog.csdn.net/20140605055113328?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2FuZ2xvdmVhbGw=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
<span style="font-weight:700;">摘要：</span>Hadoop之HDFS文件操作常有两种方式，命令行方式和JavaAPI方式。本文介绍如何利用这两种方式对HDFS文件进行操作。</p>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
<span style="font-weight:700;">关键词：HDFS文件    命令行     Java API</span></p>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
HDFS是一种分布式文件系统，为MapReduce这种框架下的海量数据分布式处理而设计。</p>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
Hadoop之HDFS文件操作常有两种方式，一种是命令行方式，即Hadoop提供了一套与Linux文件命令类似的命令行工具；另一种是JavaAPI，即利用Hadoop的Java库，采用编程的方式操作HDFS的文件。</p>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
方式一：命令行方式</p>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
Hadoop文件操作命令形式为</p>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
<span style="font-weight:700;">hadoop fs -cmd &lt;args&gt;</span></p>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
说明：cmd是具体的文件操作命令，&lt;args&gt;是一组数目可变的参数。</p>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
Hadoop最常用的文件操作命令，包括添加文件和目录、获取文件、删除文件等。</p>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
<span style="font-weight:700;">1 添加文件和目录</span></p>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
HDFS有一个默认工作目录/usr/$USER，其中$USER是你的登录用户名，作者的用户名是root。该目录不能自动创建，需要执行mkdir命令创建。</p>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
<span style="font-weight:700;">hadoop fs -mkdir  /usr/root</span></p>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
使用Hadoop的命令put将本地文件README.txt送到HDFS。</p>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
<span style="font-weight:700;">hadoop fs -put README.txt  .</span></p>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
注意上面这个命令最后一个参数是句点（<span style="font-weight:700;">.</span>），这意味着把本地文件放入到默认的工作目录，该命令等价于：</p>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
<span style="font-weight:700;">hadoop fs -put README.txt     /user/root</span></p>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
使用Hadoop的ls命令，即</p>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
<span style="font-weight:700;">hadoop fs -ls</span></p>
<br><p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
<span style="font-weight:700;">图1  hadoop 中 ls命令Demo</span></p>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
<span style="font-weight:700;">2 获取文件</span></p>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
获取文件包含两层意思，一是HDFS从本地文件中获取文件，即前面介绍的添加文件；二是本地文件从HDFS中获取文件，可以使用Hadoop的get命令。例如若本地文件没有README.txt文件，需要从HDFS中取回，可以执行如下命令。</p>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
<span style="font-weight:700;">hadoop fs -get  README.txt  .</span></p>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
或者</p>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
<span style="font-weight:700;">hadoop fs -get README.txt  /usr/root/README.txt</span></p>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
<span style="font-weight:700;">3 删除文件</span></p>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
Hadoop删除文件命令为rm。例如要删除从本地文件上传的README.txt，可以执行如下命令。</p>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
<span style="font-weight:700;">hadoop  fs -rm  README.txt</span></p>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
<span style="font-weight:700;">4 检索文件</span></p>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
检索文件即查阅HDFS中的文件内容，可以使用hadoop中的cat命令。例如要查阅README.txt的内容，可以执行如下命令。</p>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
hadoop fs -cat README.txt</p>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
<span style="font-weight:700;">图2 hadoop中cat命令Demo</span></p>
<span style="font-weight:700;"></span>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
Hadoop也支持tail命令查看最后一千字节。例如要查阅README.txt最后一千个字节，可以执行如下命令。</p>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
<span style="font-weight:700;">hadoop fs -tail README.txt</span></p>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
<span style="font-weight:700;">5查阅帮助</span></p>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
查阅Hadoop命令帮助，可以让我们很好地掌握和使用Hadoop的 命令。我们可以执行hadoop fs 获取所用版本Hadoop的一个完整命令列别，也可以使用help来显示某个具体命令的用法及简短描述。</p>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
例如，要了解ls命令，可执行如下命令。</p>
<p style="color:rgb(43,43,43);font-family:Lato, sans-serif;font-size:16px;line-height:24px;">
<span style="font-weight:700;">hadoop  fs -help ls</span></p>
<br>            </div>
                </div>