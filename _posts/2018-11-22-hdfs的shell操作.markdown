---
layout:     post
title:      hdfs的shell操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>1、hdfs  dfs的命令</p>



<pre class="prettyprint"><code class=" hljs r">hdfs dfs -appendToFile &lt;localsrc&gt; <span class="hljs-keyword">...</span> &lt;dst&gt; 
可同时上传多个文件到HDFS里面
hdfs dfs -cat URI [URI <span class="hljs-keyword">...</span>] 
查看文件内容
hdfs dfs -chgrp [-R] GROUP URI [URI <span class="hljs-keyword">...</span>] 
修改文件所属组
hdfs dfs -chmod [-R] &lt;MODE[,MODE]<span class="hljs-keyword">...</span> | OCTALMODE&gt; URI [URI <span class="hljs-keyword">...</span>] 
修改文件权限
hdfs dfs -chown [-R] [OWNER][:[GROUP]] URI [URI ]
修改文件所有者，文件所属组，其他用户的读、写、执行权限
hdfs dfs -copyFromLocal &lt;localsrc&gt; URI
复制文件到hdfs
hdfs dfs -copyToLocal [-ignorecrc] [-crc] URI &lt;localdst&gt; 
复制文件到本地
hdfs dfs -count [-q] &lt;paths&gt;
统计文件及文件夹数目
hdfs dfs -cp [-f] URI [URI <span class="hljs-keyword">...</span>] &lt;dest&gt;
Hadoop HDFS 文件系统间的文件复制
hdfs dfs -du [-s] [-h] URI [URI <span class="hljs-keyword">...</span>]
统计目录下的文件及大小
hdfs dfs -dus &lt;args&gt; 
汇总目录下的文件总大小
hdfs dfs -get [-ignorecrc] [-crc] &lt;src&gt; &lt;localdst&gt;
下载文件到本地
hdfs dfs -getmerge &lt;src&gt; &lt;localdst&gt; [addnl]
合并下载文件到本地
hdfs dfs -ls &lt;args&gt; 
查看目录
hdfs dfs -ls -R &lt;args&gt; 
循环列出目录、子目录及文件信息 
hdfs dfs -mkdir [-p] &lt;paths&gt; 
创建空白文件夹
dfs -moveFromLocal &lt;localsrc&gt; &lt;dst&gt; 
剪切文件到hdfs
hdfs dfs -moveToLocal [-crc] &lt;src&gt; &lt;dst&gt;
剪切文件到本地
hdfs dfs -mv URI [URI <span class="hljs-keyword">...</span>] &lt;dest&gt;
剪切hdfs文件
hdfs dfs -put &lt;localsrc&gt; <span class="hljs-keyword">...</span> &lt;dst&gt;
上传文件
hdfs dfs -rm [-skipTrash] URI [URI <span class="hljs-keyword">...</span>] 
删除文件/空白文件夹
hdfs dfs -rm -r [-skipTrash] URI [URI <span class="hljs-keyword">...</span>]
递归删除  删除文件及文件夹下的所有文件
hdfs dfs -setrep [-R] [-w] &lt;numReplicas&gt; &lt;path&gt;
修改副本数
hdfs dfs -stat URI [URI <span class="hljs-keyword">...</span>]
显示文件统计信息
hdfs dfs -tail [-f] URI
查看文件尾部信息
hdfs dfs -test -[ezd] URI
对PATH进行如下类型的检查： 
-e PATH是否存在，如果PATH存在，返回<span class="hljs-number">0</span>，否则返回<span class="hljs-number">1</span> 
-z 文件是否为空，如果长度为<span class="hljs-number">0</span>，返回<span class="hljs-number">0</span>，否则返回<span class="hljs-number">1</span> 
-d 是否为目录，如果PATH为目录，返回<span class="hljs-number">0</span>，否则返回<span class="hljs-number">1</span> 
hdfs dfs -text &lt;src&gt; 
查看文件内容
hdfs dfs -touchz URI [URI <span class="hljs-keyword">...</span>]
创建长度为<span class="hljs-number">0</span>的空文件

</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>