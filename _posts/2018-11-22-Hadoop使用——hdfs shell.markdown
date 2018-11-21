---
layout:     post
title:      Hadoop使用——hdfs shell
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>当Hadoop搭建完毕之后，启动Hadoop就可以使用了，而Hadoop既然是个分布式存储系统，那么肯定需要将文件上传到服务器进行存储，这篇博客就此进行初步的hdfs shell使用<br>
既然都是搭建在Linux下的，因此，hdfs shell也可以看见Linux命令的影子。<br>
我们使用<code>hdfs dfs</code>、<code>hadoop fs</code>使用，根据个人爱好使用就可以，这里使用<code>hdfs dfs</code><br>
以下就是hdfs简单命令</p>
<ol>
<li>列出hdfs文件系统中的文件以及文件夹</li>
</ol>
<pre><code>hdfs dfs -ls /
查看hdfs根目录下的文件和文件夹，如果想递归显示，添加参数-R
</code></pre>
<ol start="2">
<li>上传文件</li>
</ol>
<pre><code>hdfs dfs -put 本地文件路径 hdfs路径
将一个本地文件上传到hdfs上，注意指明本地路径和hdfs路径
</code></pre>
<ol start="3">
<li>查看文件内容</li>
</ol>
<pre><code>hdfs dfs -cat hdfs文件
查看hdfs文件，需要注意hdfs路径是否正确
</code></pre>
<ol start="4">
<li>创建文件夹以及联级创建</li>
</ol>
<pre><code>hdfs dfs -mkdir hdfs路径
与Linux创建文件夹相同，联级创建在mkdir后边添加-p
</code></pre>
<ol start="5">
<li>删除目录或者文件</li>
</ol>
<pre><code>hdfs dfs -rm hdfs文件
如果想删除文件夹需要添加-r 
也可以直接hdfs dfs -rmr hdfs路径
</code></pre>
<ol start="6">
<li>复制系统内的文件</li>
</ol>
<pre><code>hdfs dfs -cp hdfs文件 hdfs复制文件
如果目标是文件夹同样添加-r
</code></pre>
<ol start="7">
<li>复制文件到本地文件系统</li>
</ol>
<pre><code>hdfs dfs -get hdfs文件 local路径
hdfs dfs copyToLocal hdfs文件 local 路径
将hdfs文件下载到本地，注意路径正确
</code></pre>
<ol start="8">
<li>将文件从一个地方剪切到另一个地方</li>
</ol>
<pre><code>hdfs dfs -mv hdfs文件 hdfs剪切文件
与cp相同
</code></pre>
<ol start="9">
<li>显示文件以及文件夹大小</li>
</ol>
<pre><code>hdfs dfs -du hdfs文件 
如果是显示当前文件夹的大小可以添加参数-s hdfs dfs -du -s hdfs文件夹
</code></pre>
<ol start="10">
<li>在本地和hdfs上进行文件移动</li>
</ol>
<pre><code>hdfs dfs -moveToLocal hdfs路径 local路径
hdfs dfs -moveFromLocal local路径 hdfs路径
</code></pre>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>