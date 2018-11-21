---
layout:     post
title:      Hadoop之常用shell命令整理
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/mmd0308/article/details/74156874				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>Hadoop之常用shell命令整理：</p>
<p></p>
<ul><li>查看根目录：</li></ul><div><pre><code class="language-plain">hadoop fs -ls hdfs://hzq:9000/</code></pre>        或</div>
<div><pre><code class="language-plain">hadoop fs -ls /</code></pre>
<div>
<ul><li>查看/demo下的目录结构：</li></ul></div>
<div><pre><code class="language-plain">hadoop fs -ls /demo</code></pre>
<ul><li>将“/home/hzq/jdk1.8.tar.gz”上传到hdfs“/java”目录下：</li></ul><div><pre><code class="language-plain"> hadoop fs -copyFromLocal /home/hzq/jdk1.8.tar.gz  /java</code></pre>       或</div>
<div><pre><code class="language-plain"> hadoop fs -put /home/hzq/jdk1.8.tar.gz  /java</code></pre>
<ul><li>将“jdk1.8.tar.gz“文件从hdfs上下载到本地</li></ul><pre><code class="language-plain">hadoop fs -get /java/jdk1.8.tar.gz /home/hzq/</code></pre>       或</div>
<div><pre><code class="language-plain">hadoop fs -copyToLocal /java/jdk1.8.tar.gz /home/hzq/</code></pre>
<ul><li>将hdfs上/java/jdk1.8.tar.gz文件移动到hdfs文件系统的/demo文件夹中</li></ul><pre><code class="language-plain"> hadoop fs -mv /java/jdk1.8.tar.gz /demo</code></pre>
<ul><li>在根目录下创建demo文件夹：</li></ul><pre><code class="language-plain">hadoop fs -mkdir /demo</code></pre>
<ul><li>在目录/demo下创建一个test.txt空文件</li></ul><pre><code class="language-plain">hadoop fs -touchz /demo/test.txt</code></pre>
<ul><li>删除/java/jdk1.8.tar.gz文件</li></ul><pre><code class="language-plain">hadoop fs -rm /java/jdk1.8.tar.gz</code></pre>
<ul><li>删除/demo文件及其下面的所有文件</li></ul><pre><code class="language-plain">hadoop fs -rm -r /demo</code></pre>
<ul><li>查看文件/a.txt的内容</li></ul><pre><code class="language-plain">hadoop fs -cat /a.txt</code></pre>       或</div>
<div><pre><code class="language-plain">hadoop fs -text /a.txt</code></pre>
<ul><li>tail命令动态的查看文件内容，支持-f，行为跟Linux中一致</li></ul><pre><code class="language-plain">hadoop fs -tail -f /a.txt</code></pre>
<ul><li>统计/java目录下个文件的大小</li></ul><pre><code class="language-plain">hadoop fs -du -h /java</code></pre>
<ul><li>统计/目录总共占用的资源</li></ul><div><pre><code class="language-plain">hadoop fs -df -h /</code></pre>
<ul><li>将/a.txt文件权限修改成所有人都可读可写可执行</li></ul><div><pre><code class="language-plain">hadoop fs -chmod 777  /a.txt</code></pre></div>
<ul><li>将/a.txt文件的所属组改为hzq<br></li></ul><div><pre><code class="language-plain">hadoop fs -chgrp hzq /a.txt</code></pre>
<ul><li>将/a.txt文件的所属主改为zhangsan（本机无zhangsn这个用户）</li></ul><pre><code class="language-plain">hadoop fs -chown zhangsan  /a.txt</code></pre><br>
总结：整理以下hdfs的常用shell命令，以便以后用到，可以直接参考，提高开发效率。</div>
</div>
</div>
</div>
</div>
<p></p>
            </div>
                </div>