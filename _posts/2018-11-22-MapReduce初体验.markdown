---
layout:     post
title:      MapReduce初体验
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，请在转载时注明出处					https://blog.csdn.net/mrbcy/article/details/55668136				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>首先跑一下Hadoop自己带的world count。</p>

<p>先得上传文件到HDFS。</p>

<pre><code>hadoop fs -mkdir -p /wordcount/input
hadoop fs -put GitHubLog.txt /wordcount/input
</code></pre>

<p>然后运行</p>

<pre><code>hadoop jar /root/apps/hadoop-2.7.3/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.3.jar wordcount /wordcount/input/ /wordcount/output
</code></pre>

<p>其中 /wordcount/output 是结果的输出目录，要求不存在。</p>

<p>在这一步还出了问题，结果是之前的集群没配好。之前那篇配置Hadoop的博客已经修正了。</p>

<p>使用下面的命令查看结果：</p>

<p><code>hadoop fs -cat /wordcount/output/part-r-00000</code></p>

<p>一部分结果是：</p>

<pre><code>extracted       4
failed. 2
failed: 2
fatal:  2
fetch   1
file    3
file;   2
filters 1
find    2
for     11
from    4
get     1
git     3
git.c:371       2
github  1
github.com      6
</code></pre>

<p>自己写MapReduce程序的部分的话，可以参考这一篇使用MapReduce计算Pi的文章。<a href="http://blog.csdn.net/mrbcy/article/details/61455917" rel="nofollow">http://blog.csdn.net/mrbcy/article/details/61455917</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>