---
layout:     post
title:      【hadoop】 1009-hdfs的shell命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div><span style="font-family:'微软雅黑';font-size:14px;">本章重点讲解hdfs shell ：通过shell命令操作hdfs</span>
<div style="font-family:'微软雅黑';font-size:14px;"><br></div>
<div style="font-family:'微软雅黑';font-size:14px;"><br></div>
<div style="font-family:'微软雅黑';font-size:14px;"><strong>从以下三点说明一下hdfs shell即可</strong></div>
<div style="font-family:'微软雅黑';font-size:14px;">1、hdfs</div>
<div style="font-family:'微软雅黑';font-size:14px;">2、hdfs dfs</div>
<div style="font-family:'微软雅黑';font-size:14px;">3、hdfs dfs -help   hdfsshell命令</div>
<div style="font-family:'微软雅黑';font-size:14px;">[hadoop@cloud01 hadoop-2.4.1]$ hdfs<br></div>
<div style="font-family:'微软雅黑';font-size:14px;">hadoop@cloud01 hadoop-2.4.1]$ hdfs dfs<br></div>
<div style="font-family:'微软雅黑';font-size:14px;">[hadoop@cloud01 hadoop-2.4.1]$ hdfs dfs -help ls<br></div>
<div style="font-family:'微软雅黑';font-size:14px;"><strong><br></strong></div>
<div style="font-family:'微软雅黑';font-size:14px;"><strong><br></strong></div>
<div style="font-family:'微软雅黑';font-size:14px;"><strong>1.HDFS shell</strong><br>
     1.0查看帮助<br>
          hadoop fs -help &lt;cmd&gt;<br>
     1.1上传<br>
          hadoop fs -put &lt;linux上文件&gt; &lt;hdfs上的路径&gt;<br>
         <br>
          [root@hadoop ~]# hadoop fs -put /tmp/jdk-7u65-linux-i586.tar.gz  hdfs://hadoop:9000/jdk<br><br>
     1.2查看文件内容<br>
          hadoop fs -cat &lt;hdfs上的路径&gt;<br>
     1.3查看文件列表<br>
          hadoop fs -ls /<br>
     1.4下载文件<br>
          hadoop fs -get &lt;hdfs上的路径&gt; &lt;linux上文件&gt;<br>
          [root@hadoop ~]# hadoop fs -get hdfs://hadoop:9000/jdk jdk1.7</div>
<div style="font-family:'微软雅黑';font-size:14px;"><br></div>
<div style="font-family:'微软雅黑';font-size:14px;"><br></div>
<div style="font-family:'微软雅黑';font-size:14px;"><br></div>
<div style="font-family:'微软雅黑';font-size:14px;">在2.x 版本，一般使用  <strong>hdfs dfs  </strong> 替换 <strong>hadoop fs</strong> .     这两个命令在2.x 系统都生效。</div>
<div style="font-family:'微软雅黑';font-size:14px;"><br></div>
<div style="font-family:'微软雅黑';font-size:14px;"><br></div>
<div style="font-family:'微软雅黑';font-size:14px;"><br><strong>2.使用java接口操作HDFS</strong><br>
     见eclipse工程下的demo</div>
</div>
            </div>
                </div>