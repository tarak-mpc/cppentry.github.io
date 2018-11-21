---
layout:     post
title:      windows上使用eclipse远程连接hadoop进行程序开发没有权限访问问题
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/hjtlovelife/article/details/52502663				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
使用命令查看hadoop是否处于安全模式<br>
hadoop dfsadmin -safemode get <br><br><br>
hadoop离开安全模式<br>
hadoop dfsadmin -safemode leave<br><br><br>
解决方案一：取消Hadoop权限验证<br>
打开conf/hdfs-site.xml，找到dfs.permissions属性修改为false（默认为true）OK了。<br>
&lt;property&gt;<br>
    &lt;name&gt;dfs.permissions&lt;/name&gt;<br>
    &lt;value&gt;false&lt;/value&gt;<br>
&lt;/property&gt;<br>
改完需要重启HDFS；<br><br><br>
解决方案二：创建于windows主机同名的用户且具有权限<br><br><br>
hadoop version 查看hadoop版本<br><br><br>
删除目录与目录下所有文件<br><p>hadoop dfs -rmr /user/cl/temp</p>
<p><br></p>
eclipse运行Map/Reduce任务报异常：org.apache.hadoop.io.nativeio.NativeIO$Windows.access0(Ljava/lang/String;I)Z<br>
参考这个网址解决问题http://f.dataguru.cn/thread-619943-1-1.html<br>
hadoopbin.zp下载网址http://download.csdn.net/download/fingthinking/9520395<br><br><br><br><br><br><br><br><br><br><br><br>            </div>
                </div>