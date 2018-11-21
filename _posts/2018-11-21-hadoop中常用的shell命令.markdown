---
layout:     post
title:      hadoop中常用的shell命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span style="font-family:SimSun;font-size:14px;">hadoop fs -mkdir /wc  创建目录<br>
hadoop fs -mkdir /wc/srcdata   创建多目录<br>
hadoop fs -put 文件 /wc/srcdata  将文件上传到hadoop服务器指定的目录下<br>
hadoop fs -ls /wc/output    列出hadoop指定目录下的文件们<br>
hadoop fs -cat /wc/output/文件   编辑hadoop指定目录下的文件<br>
hadoop fs -rm -r/wc/output    删除hadoop指定目录的文件（保存文件夹）<br><br><br>
stop-yarn.sh 停止yarn服务</span>
            </div>
                </div>