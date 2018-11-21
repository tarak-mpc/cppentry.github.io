---
layout:     post
title:      彷徨 | HDFS客户端基本shell操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：版权所有,转载请注明出处.谢谢					https://blog.csdn.net/weixin_35353187/article/details/81257187				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1># HDFS 的shell练习</h1>

<h3># hdfs version 查看HDFS版本</h3>

<p><img alt="" class="has" height="143" src="https://img-blog.csdn.net/20180728091104774?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1000"></p>

<h3># hadoop fs -ls /  查看HDFS根目录下的文件和目录</h3>

<p><img alt="" class="has" height="300" src="https://img-blog.csdn.net/2018072809141889?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="938"></p>

<p><img alt="" class="has" height="199" src="https://img-blog.csdn.net/20180728091354159?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1000"></p>

<h3># hdfs dfs -ls -R /这条会列出/目录下的左右文件，由于有-R参数，会在文件夹和子文件夹下执行ls操作 , 会递归</h3>

<p><img alt="" class="has" height="279" src="https://img-blog.csdn.net/20180728091536641?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1000"></p>

<h3># hadoop fs -mkdir /test 在根目录创建一个目录test</h3>

<p><img alt="" class="has" height="423" src="https://img-blog.csdn.net/20180728091903640?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1000"></p>

<h3># hdfs dfs -cat /<a>hdfs-mgmt.sh</a>  查看hdfs文件中的内容</h3>

<p><img alt="" class="has" height="396" src="https://img-blog.csdn.net/20180728092211228?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1000"></p>

<h3># hdfs dfs -copyToLocal  /<a>hdfs-mgmt.sh</a>  下载,即把文件复制到本地</h3>

<p><img alt="" class="has" height="322" src="https://img-blog.csdn.net/20180728092847512?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1000"></p>

<h3># hdfs dfs -rm -f /<a>hdfs-mgmt.sh</a>  删除文件</h3>

<p><img alt="" class="has" height="386" src="https://img-blog.csdn.net/20180728093217546?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1000"></p>

<h3># hadoop fs -put ./zhang / 或 # hadoop fs -copyFromLocal ./zhang /  上传文件</h3>

<p><img alt="" class="has" height="399" src="https://img-blog.csdn.net/20180728095508142?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1000"></p>

<h3># hadoop fs -get /zhang  下载文件</h3>

<p><img alt="" class="has" height="318" src="https://img-blog.csdn.net/20180728095821943?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1000"></p>

<h3># hadoop fs -rm /zhang</h3>

<h3># hadoop fs -mv /test/test.txt /test1</h3>

<h3># hadoop fs -rmr /test1</h3>

<h3>等等,更多的命令请看<a href="http://hadoop.apache.org/docs/r1.0.4/cn/hdfs_shell.html" rel="nofollow"><span style="color:#f33b45;">http://hadoop.apache.org/docs/r1.0.4/cn/hdfs_shell.html</span></a></h3>

<h1><a href="https://www.cnblogs.com/shellshell/p/6635753.html" rel="nofollow" id="cb_post_title_url">HDFS基本shell操作</a></h1>

<p>在客户端输入Hadoop fs，可以查看所有的，hadoop shell</p>

<p># -help [cmd] //显示命令的帮助信息，如： hadoop fs -help ls</p>

<p># -ls(r) &lt;path&gt;      //显示当前目录下所有文件，path是hadoop下的路径，如：/shikun/file</p>

<p># -du(s) &lt;path&gt;    //显示目录中所有文件大小</p>

<p># -count[-q] &lt;path&gt;     //显示目录中文件数量</p>

<p># -mv &lt;src&gt; &lt;dst&gt; //移动多个文件到目标目录</p>

<p># -cp &lt;src&gt; &lt;dst&gt;  //复制多个文件到目标目录</p>

<p># -rm(r)         //删除文件(夹)</p>

<p># -put &lt;localsrc&gt; &lt;dst&gt;       //本地文件复制到hdfs</p>

<p># -copyFromLocal //同put</p>

<p># -moveFromLocal       //从本地文件移动到hdfs</p>

<p># -get [-ignoreCrc] &lt;src&gt; &lt;localdst&gt;  //复制文件到本地，可以忽略crc校验</p>

<p># -getmerge &lt;src&gt; &lt;localdst&gt;             //将源目录中的所有文件排序合并到一个文件中</p>

<p># -cat &lt;src&gt;   //在终端显示文件内容</p>

<p># -text &lt;src&gt;  //在终端显示文件内容</p>

<p># -copyToLocal [-ignoreCrc] &lt;src&gt; &lt;localdst&gt;  //复制到本地</p>

<p># -moveToLocal &lt;src&gt; &lt;localdst&gt;</p>

<p># -mkdir &lt;path&gt;   //创建文件夹</p>

<p># -touchz &lt;path&gt;  //创建一个空文件</p>            </div>
                </div>