---
layout:     post
title:      hadoop的HDFS文件操作命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><span style="font-size:medium;"><strong>Hadoop中操作HDFS的最常用的文件管理任务<br></strong>
</span>
</p>
<p><span style="font-size:medium;"><strong>1、添加文件和目录</strong>
</span>
</p>
<p><span style="font-size:medium;"><strong>添加目录:oldmo</strong>
</span>
</p>
<p><span style="font-size:medium;"><strong>hadoop fs -mkdir /user/oldmo</strong>
</span>
</p>
<p><span style="font-size:medium;"><strong>查看目录</strong>
</span>
</p>
<p><span style="font-size:medium;"><strong>hadoop fs -ls /</strong>
</span>
</p>
<p><span style="font-size:medium;"><strong>hadoop fs -lsr /</strong>
</span>
</p>
<p><span style="font-size:medium;"><strong>复制本地文件到HDFS</strong>
</span>
</p>
<p><span style="font-size:medium;"><strong>hadoop fs -put hadoop1.0.1/conf/*  /user/oldmo</strong>
</span>
</p>
<p><span style="font-size:medium;"><strong>2、获取文件</strong>
</span>
</p>
<p><span style="font-size:medium;"><strong>hadoop fs -get configuration.txt</strong>
</span>
</p>
<p><span style="font-size:medium;"><strong>显示文件</strong>
</span>
</p>
<p><span style="font-size:medium;"><strong>hadoop fs -cat configuration.txt</strong>
</span>
</p>
<p><span style="font-size:medium;"> </span>
</p>
<p><span style="font-size:medium;"><strong>3、删除文件</strong>
</span>
</p>
<p><span style="font-size:medium;"><strong>hadoop fs -rm configuration.txt</strong>
</span>
</p>            </div>
                </div>