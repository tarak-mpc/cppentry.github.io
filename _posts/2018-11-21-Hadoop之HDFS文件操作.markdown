---
layout:     post
title:      Hadoop之HDFS文件操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，博主转载请附加原文链接并声明。					https://blog.csdn.net/u012102306/article/details/51036604				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="color:rgb(54,46,43);font-size:14px;line-height:26px;"><span style="font-family:'Microsoft YaHei';"><img src="https://img-blog.csdn.net/20140605055113328?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2FuZ2xvdmVhbGw=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;"></span></p>
<p style="color:rgb(54,46,43);font-size:14px;line-height:26px;"></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-family:'Microsoft YaHei';"><span style="font-weight:700;">摘要：</span>Hadoop之HDFS文件操作常有两种方式，命令行方式和JavaAPI方式。本文介绍如何利用这两种方式对HDFS文件进行操作。</span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-weight:700;"><span style="font-family:'Microsoft YaHei';">关键词：HDFS文件    命令行     Java API</span></span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-family:'Microsoft YaHei';">HDFS是一种分布式文件系统，为MapReduce这种框架下的海量数据分布式处理而设计。</span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-family:'Microsoft YaHei';">Hadoop之HDFS文件操作常有两种方式，一种是命令行方式，即Hadoop提供了一套与Linux文件命令类似的命令行工具；另一种是JavaAPI，即利用Hadoop的Java库，采用编程的方式操作HDFS的文件。</span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-family:'Microsoft YaHei';">方式一：命令行方式</span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-family:'Microsoft YaHei';">Hadoop文件操作命令形式为</span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-weight:700;"><span style="font-family:'Microsoft YaHei';">hadoop fs -cmd &lt;args&gt;</span></span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-family:'Microsoft YaHei';">说明：cmd是具体的文件操作命令，&lt;args&gt;是一组数目可变的参数。</span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-family:'Microsoft YaHei';">Hadoop最常用的文件操作命令，包括添加文件和目录、获取文件、删除文件等。</span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-weight:700;"><span style="font-family:'Microsoft YaHei';">1 添加文件和目录</span></span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-family:'Microsoft YaHei';">HDFS有一个默认工作目录/usr/$USER，其中$USER是你的登录用户名，作者的用户名是root。该目录不能自动创建，需要执行mkdir命令创建。</span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-weight:700;"><span style="font-family:'Microsoft YaHei';">hadoop fs -mkdir  /usr/root</span></span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-family:'Microsoft YaHei';">使用Hadoop的命令put将本地文件README.txt送到HDFS。</span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-weight:700;"><span style="font-family:'Microsoft YaHei';">hadoop fs -put README.txt  .</span></span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-family:'Microsoft YaHei';">注意上面这个命令最后一个参数是句点（<span style="font-weight:700;">.</span>），这意味着把本地文件放入到默认的工作目录，该命令等价于：</span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-weight:700;"><span style="font-family:'Microsoft YaHei';">hadoop fs -put README.txt     /user/root</span></span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-family:'Microsoft YaHei';">使用Hadoop的ls命令，即</span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-weight:700;"><span style="font-family:'Microsoft YaHei';">hadoop fs -ls</span></span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-family:'Microsoft YaHei';">显示结果如图1所示。</span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<a href="http://www.wangluqing.com/wp-content/uploads/2014/03/pic1.jpg" rel="nofollow" style="color:rgb(36,137,13);text-decoration:none;"><span style="font-family:'Microsoft YaHei';"><img class="alignnone size-medium wp-image-594" src="http://www.wangluqing.com/wp-content/uploads/2014/03/pic1-300x40.jpg" alt="pic1" width="300" height="40" style="border:none;vertical-align:middle;"></span></a></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-weight:700;"><span style="font-family:'Microsoft YaHei';">图1  hadoop 中 ls命令Demo</span></span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-weight:700;"><span style="font-family:'Microsoft YaHei';">2 获取文件</span></span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-family:'Microsoft YaHei';">获取文件包含两层意思，一是HDFS从本地文件中获取文件，即前面介绍的添加文件；二是本地文件从HDFS中获取文件，可以使用Hadoop的get命令。例如若本地文件没有README.txt文件，需要从HDFS中取回，可以执行如下命令。</span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-weight:700;"><span style="font-family:'Microsoft YaHei';">hadoop fs -get  README.txt  .</span></span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-family:'Microsoft YaHei';">或者</span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-weight:700;"><span style="font-family:'Microsoft YaHei';">hadoop fs -get README.txt  /usr/root/README.txt</span></span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-weight:700;"><span style="font-family:'Microsoft YaHei';">3 删除文件</span></span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-family:'Microsoft YaHei';">Hadoop删除文件命令为rm。例如要删除从本地文件上传的README.txt，可以执行如下命令。</span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-weight:700;"><span style="font-family:'Microsoft YaHei';">hadoop  fs -rm  README.txt</span></span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-weight:700;"><span style="font-family:'Microsoft YaHei';">4 检索文件</span></span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-family:'Microsoft YaHei';">检索文件即查阅HDFS中的文件内容，可以使用hadoop中的cat命令。例如要查阅README.txt的内容，可以执行如下命令。</span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-family:'Microsoft YaHei';">hadoop fs -cat README.txt</span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-family:'Microsoft YaHei';">部分显示结果如图2所示</span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<a href="http://www.wangluqing.com/wp-content/uploads/2014/03/pic2.jpg" rel="nofollow" style="color:rgb(36,137,13);text-decoration:none;"><span style="font-family:'Microsoft YaHei';"><img class="alignnone size-medium wp-image-596" src="http://www.wangluqing.com/wp-content/uploads/2014/03/pic2-300x128.jpg" alt="pic2" width="300" height="128" style="border:none;vertical-align:middle;"></span></a></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-weight:700;"><span style="font-family:'Microsoft YaHei';">图2 hadoop中cat命令Demo</span></span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-family:'Microsoft YaHei';">另外，hadoop的cat命令的输出也可以使用管道传递给Unix 命令的head：</span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-weight:700;"><span style="font-family:'Microsoft YaHei';">hadoop fs -cat README.txt | head</span></span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-family:'Microsoft YaHei';">Hadoop也支持tail命令查看最后一千字节。例如要查阅README.txt最后一千个字节，可以执行如下命令。</span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-weight:700;"><span style="font-family:'Microsoft YaHei';">hadoop fs -tail README.txt</span></span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-weight:700;"><span style="font-family:'Microsoft YaHei';">5查阅帮助</span></span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-family:'Microsoft YaHei';">查阅Hadoop命令帮助，可以让我们很好地掌握和使用Hadoop的 命令。我们可以执行hadoop fs 获取所用版本Hadoop的一个完整命令列别，也可以使用help来显示某个具体命令的用法及简短描述。</span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-family:'Microsoft YaHei';">例如，要了解ls命令，可执行如下命令。</span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-weight:700;"><span style="font-family:'Microsoft YaHei';">hadoop  fs -help ls</span></span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-family:'Microsoft YaHei';">关于hadoop命令ls的描述如图3所示。</span></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<a href="http://www.wangluqing.com/wp-content/uploads/2014/03/pic3.jpg" rel="nofollow" style="color:rgb(36,137,13);text-decoration:none;"><span style="font-family:'Microsoft YaHei';"><img class="alignnone size-medium wp-image-597" src="http://www.wangluqing.com/wp-content/uploads/2014/03/pic3-300x90.jpg" alt="pic3" width="300" height="90" style="border:none;vertical-align:middle;"></span></a></p>
<p style="color:rgb(43,43,43);font-size:16px;line-height:24px;">
<span style="font-weight:700;"><span style="font-family:'Microsoft YaHei';">图3 Hadoop命令ls的介绍 </span></span></p>
            </div>
                </div>