---
layout:     post
title:      HDFS 常用shell命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：作者：sun_shang 出处：http://blog.csdn.net/sun_shang/ 文章版权归本人所有，欢迎转载，但未经作者同意必须保留此段声明，且在文章页面明显位置给出原文连接，否则保留追究法律责任的权利。					https://blog.csdn.net/sun_shang/article/details/78081179				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>hdfs命令：</p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p>sbin/start-dfs.sh</p>
</td>
<td valign="top">
<p>开启HDFS</p>
</td>
</tr><tr><td valign="top">
<p>bin/hdfs  bin/hdfs -help</p>
</td>
<td valign="top">
<p>查看hdfs帮助信息</p>
</td>
</tr><tr><td valign="top">
<p>bin/hdfs namenode  -h</p>
</td>
<td valign="top">
<p>查看namenode等帮助信息</p>
</td>
</tr><tr><td valign="top">
<p><span style="color:#FF0000;">bin/hdfs dfs -help cp</span></p>
</td>
<td valign="top">
<p>查看带有例子的帮助信息</p>
</td>
</tr><tr><td valign="top">
<p>bin/hdfs namenode -format</p>
</td>
<td valign="top">
<p>格式化namenode(勿用)</p>
</td>
</tr><tr><td valign="top">
<p>bin/hdfs dfs</p>
</td>
<td valign="top">
<p>查看dfs命令帮助信息</p>
</td>
</tr><tr><td valign="top">
<p>bin/hdfs dfs -ls</p>
</td>
<td valign="top">
<p>查看HDFS上的目录文件</p>
</td>
</tr><tr><td valign="top">
<p>bin/hdfs dfs -lsr</p>
</td>
<td valign="top">
<p>查看HDFS上的目录文件及其子文件</p>
</td>
</tr><tr><td valign="top">
<p>bin/hdfs dfs -cat /user/1.txt</p>
</td>
<td valign="top">
<p>查看文本文件内容</p>
</td>
</tr><tr><td valign="top">
<p>bin/hdfs dfs –text  /user/1.txt</p>
</td>
<td valign="top">
<p>查看所有文件内容(包括文本文件)</p>
</td>
</tr><tr><td valign="top">
<p>bin/hdfs dfs -mkdir -p /input/test</p>
</td>
<td valign="top">
<p>新建目录文件以及子文件</p>
</td>
</tr><tr><td valign="top">
<p>bin/hdfs dfs -rmdir /input/test</p>
</td>
<td valign="top">
<p>删除目录文件</p>
</td>
</tr><tr><td valign="top">
<p>bin/hdfs dfs –rm -rf /input/test</p>
</td>
<td valign="top">
<p>删除文件</p>
</td>
</tr><tr><td valign="top">
<p><span style="color:#FF0000;">bin/hdfs dfs  -put </span></p>
</td>
<td valign="top">
<p>上传文件</p>
</td>
</tr><tr><td valign="top">
<p><span style="color:#FF0000;">bin/dfs dfs -get /user/a.txt</span></p>
</td>
<td valign="top">
<p>下载文件</p>
</td>
</tr><tr><td valign="top">
<p><span style="color:#FF0000;">bin/hdfs fsck  /user</span></p>
</td>
<td valign="top">
<p><span style="color:#FF0000;">查看</span><span style="color:#FF0000;">user</span><span style="color:#FF0000;">文件健康</span></p>
</td>
</tr><tr><td valign="top">
<p><span style="color:#FF0000;">bin/hdfs dfsadmin -report</span></p>
</td>
<td valign="top">
<p><span style="color:#FF0000;">汇报集群信息：包括磁盘</span><span style="color:#FF0000;">大小，剩余内存</span></p>
</td>
</tr><tr><td valign="top">
<p><span style="color:#FF0000;">bin/hdfs dfsadmin -safemode get</span></p>
</td>
<td valign="top">
<p><span style="color:#FF0000;">查看安全模式</span><span style="color:#FF0000;">可选项有</span><span style="color:#FF0000;">enter/leave</span><span style="color:#FF0000;">不要轻易用</span></p>
</td>
</tr></tbody></table>            </div>
                </div>