---
layout:     post
title:      HDFS Shell命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：转载请附此链接https://blog.csdn.net/qq_42393859					https://blog.csdn.net/qq_42393859/article/details/82767354				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>1. 查看hdfs目录命令<br>
bin/hdfs dfs -ls /<br>
bin/hdfs dfs -ls /suns<br>
2. 创建目录的命令<br>
bin/hdfs dfs -mkdir 目录名字<br>
bin/hdfs dfs -mkdir /suns<br>
bin/hdfs dfs -mkdir -p /xiaohei/xiaohua<br>
3. 本地文件上传到hdfs<br>
bin/hdfs dfs -put 本地的文件路径  hdfs文件路径<br>
bin/hdfs dfs -put /root/data/data1 /suns<br>
4. 查看hdfs文件<br>
# HDFS 分布式大数据文件系统 存文本文件<br>
bin/hdfs dfs -text /suns/data1<br>
bin/hdfs dfs -cat /suns/data1<br>
5. 从hdfs中下载文件到本地<br>
bin/hdfs dfs -get hdfs文件路径  本地路径<br>
bin/hdfs dfs -get /suns/data1 /root/data/data2<br>
6. 从hdfs中删除文件或者文件夹<br>
bin/hdfs dfs -rm -r /suns/data1<br>
#删除的细节<br>
#修改垃圾箱的文件保存时间  默认 0  分钟 不保存<br>
core-site.xml<br>
 &lt;property&gt;<br>
    &lt;name&gt;fs.trash.interval&lt;/name&gt;<br>
    &lt;value&gt;10&lt;/value&gt;<br>
  &lt;/property&gt;<br>
#垃圾箱的位置<br>
/user/root/.Trash/180918031000/suns/data1<br>
#HDFS操作系统 也是有权限<br>
hdfs-site.xml<br>
&lt;property&gt;<br>
   &lt;name&gt;dfs.permissions.enabled&lt;/name&gt;<br>
   &lt;value&gt;false&lt;/value&gt;<br>
&lt;/property&gt;<br>
7. cp命令赋值<br>
bin/hdfs dfs -cp 原始位置 目标位置<br>
8. mv命令 移动 或者 改名<br>
bin/hdfs dfs -mv 原始位置 目标位置</p>            </div>
                </div>