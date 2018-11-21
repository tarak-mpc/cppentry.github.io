---
layout:     post
title:      hdfs shell常用操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>hadoop fs -ls /</p><p>hadoop fs -mkdir /test/</p><p>hadoop fs -mkdir -p /a/b</p><p>hadoop fs -ls -R /</p><p><br></p><p>hadoop fs -put hdfs.cmd(local) /test/</p><p>hadoop fs -cat /test/hdfs.cmd</p><p>hadoop fs -text /test/hdfs.cmd</p><p>hadoop fs -cpoyFromLocal src dst</p><p>hadoop fs -get /test/hdfs.cmd a_tmp </p><p>ls -l</p><p><br></p><p>hadoop fs -rm /test/hdfs.cmd</p><p>hadoop fs -rm -r /a</p><p><br></p><p><br></p>            </div>
                </div>