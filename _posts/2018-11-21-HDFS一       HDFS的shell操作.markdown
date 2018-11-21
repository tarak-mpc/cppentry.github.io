---
layout:     post
title:      HDFS一       HDFS的shell操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u013792289/article/details/49785819				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<pre><code class="language-plain">#hadoop fs -ls /  查看HDFS根目录
#hadoop fs -mkdir /test 在根目录创建一个目录test
#hadoop fs -mkdir /test1 在根目录创建一个目录test1
#echo -e 'hadoop second lesson' &gt;test.txt
#hadoop fs -put ./test.txt /test　
或#hadoop fs -copyFromLocal ./test.txt /test
#cd ..
#hadoop fs -get /test/test.txt .
或#hadoop fs -getToLocal /test/test.txt
#hadoop fs -cp /test/test.txt /test1
#hadoop fs -rm /test1/test.txt
#hadoop fs -mv /test/test.txt /test1
#hadoop fs -rmr /test1   
#hadoop fs -appendToFile</code></pre>
<p>-ls -R(递归显示)  文件夹名</p>
<p>-put                       上传</p>
<p>例如把Linux上的RE.txt上传到根目录：bin/hdfs dfs -put  RE.txt /</p>
<p>-get                       获取</p>
<p>例如把RE.txt下载到本地：bin/hdfs dfs -get /RE.txt</p>
<p>-text 后面接文件名来查看文件</p>
<p>-appendToFile    追加</p>
<p>bin/hdfs dfs -appendToFile hello /hello<br></p>
            </div>
                </div>