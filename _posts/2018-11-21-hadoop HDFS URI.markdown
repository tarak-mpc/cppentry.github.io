---
layout:     post
title:      hadoop HDFS URI
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="line-height:19.5px;color:rgb(75,75,75);font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;font-size:13px;">
<strong>1、hdfs基本命令:</strong><br>
hadoop fs -cmd &lt;args&gt;<br>
选项：<br>
cmd: 具体的操作，基本上与UNIX的命令行相同<br>
args: 参数</p>
<p style="line-height:19.5px;color:rgb(75,75,75);font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;font-size:13px;">
<strong>2、hdfs资源uri格式：</strong><br>
用法：scheme://authority/path<br>
选项：<br>
scheme–&gt;协议名，file或hdfs<br>
authority–&gt;namenode主机名<br>
path–&gt;路径<br>
范例：hdfs://localhost:54310/user/hadoop/test.txt<br>
假设已经在/home/hadoop/hadoop-1.1.1/conf/core-site.xml里配置了fs.default.name=hdfs://localhost:54310，则仅使用/user/hadoop/test.txt即可。hdfs默认工作目录为/user/$USER，$USER是当前的登录用户名。</p>
<p style="line-height:19.5px;color:rgb(75,75,75);font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;font-size:13px;">
如在core-site.xml中配置有：</p>
<p style="line-height:19.5px;color:rgb(75,75,75);font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;font-size:13px;">
&lt;configuration&gt;<br>
&lt;property&gt;<br>
&lt;name&gt;fs.default.name&lt;/name&gt;<br>
&lt;value&gt;hdfs://hadoop0:9000&lt;/value&gt;<br>
&lt;/property&gt;<br>
&lt;/configuration&gt;<br></p>
<p style="line-height:19.5px;color:rgb(75,75,75);font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;font-size:13px;">
相应hdfs目录有：hadoop fs -ls /wq/有若干文件；其中hadoop0已配置为本机ip , </p>
<p style="line-height:19.5px;color:rgb(75,75,75);font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;font-size:13px;">
通过hdfs://hadoop0:9000/wq/.......访问；</p>
<p style="line-height:19.5px;color:rgb(75,75,75);font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;font-size:13px;">
<strong>3、hdfs命令范例</strong><br>
hadoop fs -mkdir /user/hadoop<br>
hadoop fs -ls /user<br>
hadoop fs -lsr /user(递归的)<br>
hadoop fs -put test.txt /user/hadoop(复制到hdfs://localhost:54310/user/hadoop目录下，首先要创建当前目录)<br>
hadoop fs -get /user/hadoop/test.txt .(复制test.txt文件到本地当前目录下)<br>
hadoop fs -cat /user/hadoop/test.txt<br>
hadoop fs -tail /user/hadoop/test.txt(查看最后1000字节)<br>
hadoop fs -rm /user/hadoop/test.txt<br>
hadoop fs -help ls(查看ls命令的帮助文档)</p>
<p style="line-height:19.5px;color:rgb(75,75,75);font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;font-size:13px;">
<strong>4、在put时遇到的问题</strong><br>
异常信息: org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create file/user/hadoopadmin. Name node is in safe mode.<br>
解决办法：hadoop dfsadmin -safemode leave</p>
            </div>
                </div>