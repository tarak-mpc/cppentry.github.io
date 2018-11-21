---
layout:     post
title:      Hadoop-实例WordCount
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/sunyx1130/article/details/50856643				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>Hadoop必须要启动了才行哟，不要问我为什么会知道。<img alt="大笑" src="http://static.blog.csdn.net/xheditor/xheditor_emot/default/laugh.gif"></p>
<p>1.退出安全模式</p>
<p>/opt/hadoop/hadoop272/bin/hadoop dfsadmin -safemode leave<br></p>
<p><img src="https://img-blog.csdn.net/20160311160304436?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>启动就是 enter</p>
<p>2.上传数据到hadoop</p>
<p>2.1新建存放的目录</p>
<p>/opt/hadoop/hadoop272/bin/hadoop fs -mkdir /input</p>
<p><img src="https://img-blog.csdn.net/20160311160638266?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>2.2传数据到指定目录</p>
<p>/opt/hadoop/hadoop272/bin/hadoop fs -put /opt/hadoop/data/file1.txt /input</p>
<p><img src="https://img-blog.csdn.net/20160311160814782?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>3.启动jar程序</p>
<p>/opt/hadoop/hadoop272/bin/hadoop jar /opt/hadoop/hadoop272/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.2.jar wordcount /input /output<br></p>
<p><img src="https://img-blog.csdn.net/20160311161216631?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>4.查看结果</p>
<p>4.1查看/output下的目录结构</p>
<p>/opt/hadoop/hadoop272/bin/hadoop fs -ls /output</p>
<p><img src="https://img-blog.csdn.net/20160311161315508?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>4.2查看统计结果</p>
<p>/opt/hadoop/hadoop272/bin/hadoop fs cat /output/part-r-00000<br></p>
<p><img src="https://img-blog.csdn.net/20160311161415628?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>5.重新上传</p>
<p>5.1删除 /input中的file1</p>
<p>/opt/hadoop/hadoop272/bin/hadoop fs -rm /input/file1.txt<br></p>
<p><img src="https://img-blog.csdn.net/20160311161543621?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>5.2删除/output</p>
<p>/opt/hadoop/hadoop272/bin/hadoop fs -rm -r /output<br></p>
<p><img src="https://img-blog.csdn.net/20160311160427734?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>5.3跳到2.2开始执行</p>
            </div>
                </div>