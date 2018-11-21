---
layout:     post
title:      hadoop hdfs 常用命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_27404929/article/details/76257155				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-weight:bold;"><span style="font-size:18px;"><span style="color:rgb(255,0,0);background-color:rgb(255,255,255);"><span></span>hadoop 执行jar</span>  ：</span><span style="font-size:18px;">hadoop jar *.jar *.properties </span></span></p>
<p><span style="font-size:18px;color:#ff0000;"><strong>查看hdfs 上的文件目录 </strong></span><span style="font-size:18px;"><strong>：</strong></span><strong></strong><span style="font-size:18px;"><strong>hadoop fs -ls /</strong></span></p>
<p><strong><span style="font-size:18px;"><span style="color:#33ff33;">hdfs查看文件大小</span>：hadoop fs -du -h /data/*.txt</span></strong></p>
<p><strong><span style="font-size:18px;">删除hdfs上的目录：hadoop fs -rmr /data</span></strong></p>
<p><span style="font-size:18px;"><strong>拷贝hdfs文件到本地：hadoop fs -get hdfs://namenode/data/part-r-00000 /home/data</strong></span></p>
<p><span style="font-size:18px;"><strong>改变hdfs文件所属用户: su hdfs---&gt;hdfs  dfs  -chown -R root hdfs://namenode/data</strong></span></p>
<p><strong style="font-size:18px;">hdfs创建目录：  hadoop fs -mkdir /count</strong></p>
            </div>
                </div>