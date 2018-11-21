---
layout:     post
title:      HDFS shell常用命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/hongchenlingtian/article/details/53239310				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span></span><span style="font-size:14px;">1.0查看帮助</span></p>
<p><span style="font-size:14px;"><span></span>hadoop fs -help &lt;cmd&gt;</span></p>
<p><span style="font-size:14px;"><br><span></span>1.1上传</span></p>
<p><span style="font-size:14px;"><span></span>hadoop fs -put &lt;linux上文件&gt; &lt;hdfs上的路径&gt;<br><br>
           例如：hadoop fs -put /home/hadoop/jdk-8u45-linux-x64.tar.gz hdfs://hadoop01:9000/jdk<br><br><span></span>1.2查看文件内容<br><span></span>hadoop fs -cat &lt;hdfs上的路径&gt;<br><br>
          例如：hadoop fs -cat hdfs://hadoop01:9000/love.txt<br><br><span></span>1.3查看文件列表<br><span></span>hadoop fs -ls /<br><span></span>1.4下载文件<br><span></span>hadoop fs -get &lt;hdfs上的路径&gt; &lt;linux上文件&gt;<br><br>
            例如：hadoop fs -get hdfs://hadoop01:9000/love.txt /home/hadoop/Downloads/jdk1.8</span></p>
<span style="font-size:14px;"><span></span>hadoop fs -help &lt;cmd&gt;<br><span></span>1.1上传<br><span></span>hadoop fs -put &lt;linux上文件&gt; &lt;hdfs上的路径&gt;<br><br>
           例如：hadoop fs -put /home/hadoop/jdk-8u45-linux-x64.tar.gz hdfs://hadoop01:9000/jdk<br><br><span></span>1.2查看文件内容<br><span></span>hadoop fs -cat &lt;hdfs上的路径&gt;<br><br>
          例如：hadoop fs -cat hdfs://hadoop01:9000/love.txt<br><br><span></span>1.3查看文件列表<br><span></span>hadoop fs -ls /<br><span></span>1.4下载文件<br><span></span>hadoop fs -get &lt;hdfs上的路径&gt; &lt;linux上文件&gt;<br><br>
            例如：hadoop fs -get hdfs://hadoop01:9000/love.txt /home/hadoop/Downloads/jdk1.8</span>
            </div>
                </div>