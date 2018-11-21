---
layout:     post
title:      Hadoop初次运行与WordCountd
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1>对HDFS的操作</h1>
<div>查看根目录的列表</div>
<div><em>hdfs dfs -ls /</em><br></div>
<div><img src="https://img-blog.csdn.net/20140808102309438?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdGhlZGF5XzIwMDk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div><br></div>
<div>data文件夹为资源文件</div>
<div>user为你登录后的文件，一般里面都是输出的结果</div>
<div><br></div>
<div><em>hdfs dfs -copyFromLocal /root/stong.txt /data</em><br></div>
<div>拷贝外部的文件至HDFS目录，进行分析或者处理</div>
<div><em><br></em></div>
<div><em>hadoop jar /root/hadoop-2.4.1/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.4.1.jar wordcount /data out   （也可设置文件夹）</em><br></div>
<div>执行某个jar的类，输出至out</div>
<div><img src="https://img-blog.csdn.net/20140808102752438?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdGhlZGF5XzIwMDk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div><em>hadoop dfs -cat /user/root/outsheng/part-r-00000</em><br></div>
<div>输出及查看某个结果</div>
            </div>
                </div>