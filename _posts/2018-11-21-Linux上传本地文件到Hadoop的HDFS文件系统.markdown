---
layout:     post
title:      Linux上传本地文件到Hadoop的HDFS文件系统
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/zhangvalue/article/details/80671217				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>记录如何将本地文件上传至HDFS中</p><p>前提是已经启动了hadoop成功（nodedate都成功启动）</p><p>①先切换到HDFS用户</p><p>②创建一个input文件夹</p><pre><code class="language-delphi">zhangsf@hadoop1:~$ hdfs dfs -mkdir /input</code></pre><p>     查看创建的文件夹在 hadoop1:50070中查看（我自己的对应的是：  http://192.168.5.130:50070）</p><p><img src="https://img-blog.csdn.net/20180612211624123" alt=""><br></p><p>③将本地文件上传到HDFS，前面为 服务器中的文件路径，后面为HDFS中路径</p><pre><code class="language-delphi">zhangsf@hadoop1:~$ hdfs dfs -put /home/zhangsf/aaa.txt  /input
zhangsf@hadoop1:~$ hdfs dfs  -ls /input
Found 1 items
-rw-r--r--   2 zhangsf supergroup         89 2018-06-12 06:03 /input/aaa.txt</code></pre><p>      其中 aaa.txt 是本地文件</p><pre><code class="language-delphi">zhangsf@hadoop1:~$ ls
aaa.txt  data  hadoop  hadoop2  opt  package
zhangsf@hadoop1:~$ pwd
/home/zhangsf</code></pre><p>      查看自己上传成功了，也可以进入hadoop1:7077的</p><h1>   Browse Directory中查看</h1><p><img src="https://img-blog.csdn.net/20180612211933472" alt=""></p><p>查看</p><p><img src="https://img-blog.csdn.net/20180614142914591" alt=""><br></p><p>上传成功ok！</p><p>方法二： 使用moveFromLocal指令</p><pre><code class="language-delphi">zhangsf@hadoop1:~$ hadoop fs -moveFromLocal aaa.txt  /data
</code></pre><img src="https://img-blog.csdn.net/20180615095348914?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3poYW5ndmFsdWU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br><p><br></p>            </div>
                </div>