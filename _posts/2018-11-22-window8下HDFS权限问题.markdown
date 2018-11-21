---
layout:     post
title:      window8下HDFS权限问题
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
1、修改hadoop的配置文件hdfs-site.xml，添加配置，取消权限限制<br>
&lt;property&gt;<br>
&lt;name&gt;dfs.permissions&lt;/name&gt;<br>
&lt;value&gt;false&lt;/value&gt;<br>
&lt;/property&gt;<br>
重启下hadoop的进程生效<br><br><br>
2、修改HDFS的目录权限<br>
$ ./hadoop dfs -chmod -R 777 /
            </div>
                </div>