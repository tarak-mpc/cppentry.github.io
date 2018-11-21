---
layout:     post
title:      hadoop hdfs常用命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u014542122/article/details/82531405				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h4 id="hdfs常用命令">hdfs常用命令</h4>



<h5 id="列出hdfs目录下的列表">* 列出hdfs目录下的列表</h5>

<p>语法：<code>hadoop fs -ls hdfs路径</code>  </p>



<h5 id="上传文件到hdfs中三种方式">* 上传文件到hdfs中（三种方式）</h5>

<ol>
<li><p>直接上传 <br>
语法：<code>hadoop fs -put 本地路径 hdfs路径</code>  </p></li>
<li><p>复制文件到hdfs中，和put等价 <br>
语法：<code>hadoop fs -copyFromLocal 本地文件  hdfs路径</code></p></li>
<li><p>移动文件到hdfs中 <br>
语法：<code>hadoop fs -moveFromLocal 本地文件  hdfs路径</code></p></li>
</ol>



<h5 id="下载hdfs文件到本地三种方式">* 下载hdfs文件到本地（三种方式）</h5>

<ol>
<li><p>直接下载 <br>
语法：<code>hadoop fs -get hdfs路径 本地路径</code></p></li>
<li><p>复制文件到本地，和get等价 <br>
语法：<code>hadoop fs -copyToLocal hdfs中路径 本地路径</code></p></li>
<li><p>移动文件到本地 <br>
语法：<code>hadoop fs -moveToLocal hdfs路径 本地路径</code></p></li>
</ol>



<h5 id="创建文件夹">* 创建文件夹</h5>

<p>语法：<code>hadoop fs -mkdir -p 文件夹</code></p>



<h5 id="删除文件夹">* 删除文件夹</h5>

<p>语法：<code>hadoop fs -rm -r 文件夹</code></p>



<h5 id="移动更名">* 移动（更名）</h5>

<p>语法：<code>hadoop fs -mv /hdfs路径 /hdfs的另一个路径</code></p>



<h5 id="显示文本文件的内容">* 显示文本文件的内容</h5>

<p>语法：<code>hadoop fs -cat /hdfs中的文件</code> <br>
语法：<code>hadoop fs -tail [-f] /hdfs中的文件</code></p>



<h5 id="追加内容到已存在的文件">* 追加内容到已存在的文件</h5>

<p>语法：<code>hadoop fs -appendToFile 本地文件 hdfs中的文件</code></p>



<h5 id="修改文件的权限">* 修改文件的权限</h5>

<p>语法：<code>hadoop fs -chmod 700 文件或目录</code></p>



<h5 id="修改文件的属主属组">* 修改文件的属主属组</h5>

<p>语法：<code>hadoop fs -chown user:group 文件或目录</code></p>



<h5 id="补充所有命令列表">* 补充所有命令列表</h5>



<pre class="prettyprint"><code class=" hljs r">Usage: hadoop fs [generic options]
    [-appendToFile &lt;localsrc&gt; <span class="hljs-keyword">...</span> &lt;dst&gt;]
    [-cat [-ignoreCrc] &lt;src&gt; <span class="hljs-keyword">...</span>]
    [-checksum &lt;src&gt; <span class="hljs-keyword">...</span>]
    [-chgrp [-R] GROUP PATH...]
    [-chmod [-R] &lt;MODE[,MODE]<span class="hljs-keyword">...</span> | OCTALMODE&gt; PATH...]
    [-chown [-R] [OWNER][:[GROUP]] PATH...]
    [-copyFromLocal [-f] [-p] [-l] [-d] &lt;localsrc&gt; <span class="hljs-keyword">...</span> &lt;dst&gt;]
    [-copyToLocal [-f] [-p] [-ignoreCrc] [-crc] &lt;src&gt; <span class="hljs-keyword">...</span> &lt;localdst&gt;]
    [-count [-q] [-h] [-v] [-t [&lt;storage type&gt;]] [-u] [-x] &lt;path&gt; <span class="hljs-keyword">...</span>]
    [-cp [-f] [-p | -p[topax]] [-d] &lt;src&gt; <span class="hljs-keyword">...</span> &lt;dst&gt;]
    [-createSnapshot &lt;snapshotDir&gt; [&lt;snapshotName&gt;]]
    [-deleteSnapshot &lt;snapshotDir&gt; &lt;snapshotName&gt;]
    [-df [-h] [&lt;path&gt; <span class="hljs-keyword">...</span>]]
    [-du [-s] [-h] [-x] &lt;path&gt; <span class="hljs-keyword">...</span>]
    [-expunge]
    [-find &lt;path&gt; <span class="hljs-keyword">...</span> &lt;expression&gt; <span class="hljs-keyword">...</span>]
    [-get [-f] [-p] [-ignoreCrc] [-crc] &lt;src&gt; <span class="hljs-keyword">...</span> &lt;localdst&gt;]
    [-getfacl [-R] &lt;path&gt;]
    [-getfattr [-R] {-n name | -d} [-e en] &lt;path&gt;]
    [-getmerge [-nl] [-skip-empty-file] &lt;src&gt; &lt;localdst&gt;]
    [-help [cmd <span class="hljs-keyword">...</span>]]
    [-ls [-C] [-d] [-h] [-q] [-R] [-t] [-S] [-r] [-u] [&lt;path&gt; <span class="hljs-keyword">...</span>]]
    [-mkdir [-p] &lt;path&gt; <span class="hljs-keyword">...</span>]
    [-moveFromLocal &lt;localsrc&gt; <span class="hljs-keyword">...</span> &lt;dst&gt;]
    [-moveToLocal &lt;src&gt; &lt;localdst&gt;]
    [-mv &lt;src&gt; <span class="hljs-keyword">...</span> &lt;dst&gt;]
    [-put [-f] [-p] [-l] [-d] &lt;localsrc&gt; <span class="hljs-keyword">...</span> &lt;dst&gt;]
    [-renameSnapshot &lt;snapshotDir&gt; &lt;oldName&gt; &lt;newName&gt;]
    [-rm [-f] [-r|-R] [-skipTrash] [-safely] &lt;src&gt; <span class="hljs-keyword">...</span>]
    [-rmdir [--ignore-fail-on-non-empty] &lt;dir&gt; <span class="hljs-keyword">...</span>]
    [-setfacl [-R] [{-b|-k} {-m|-x &lt;acl_spec&gt;} &lt;path&gt;]|[--set &lt;acl_spec&gt; &lt;path&gt;]]
    [-setfattr {-n name [-v value] | -x name} &lt;path&gt;]
    [-setrep [-R] [-w] &lt;rep&gt; &lt;path&gt; <span class="hljs-keyword">...</span>]
    [-stat [format] &lt;path&gt; <span class="hljs-keyword">...</span>]
    [-tail [-f] &lt;file&gt;]
    [-test -[defsz] &lt;path&gt;]
    [-text [-ignoreCrc] &lt;src&gt; <span class="hljs-keyword">...</span>]
    [-touchz &lt;path&gt; <span class="hljs-keyword">...</span>]
    [-truncate [-w] &lt;length&gt; &lt;path&gt; <span class="hljs-keyword">...</span>]
    [-usage [cmd <span class="hljs-keyword">...</span>]]</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>