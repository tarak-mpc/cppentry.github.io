---
layout:     post
title:      Hadoop入门之WordCount（windows+hadoop2.8.1）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，转载请注明出处，因个人水平有限，如有误处，请尽管批评斧正，十分感谢！					https://blog.csdn.net/lixibin0829/article/details/78283287				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>参考文章： <br>
    <a href="http://blog.csdn.net/lifeifei2010/article/details/69396332" rel="nofollow">http://blog.csdn.net/lifeifei2010/article/details/69396332</a> <br>
    <a href="http://blog.csdn.net/xiaoxiangzi222/article/details/52757168" rel="nofollow">http://blog.csdn.net/xiaoxiangzi222/article/details/52757168</a> <br>
    ……</p>

<p>首先开启Hadoop服务（参照上一篇博客） <br>
接下来：</p>

<pre><code>1.进入hadoop的bin目录 在hdfs上创建一个新文件夹

hdfs dfs -mkdir -p /data/input

2.将当前目录中的某个txt文件复制到hdfs中（我复制的LICENSE.txt，     注意要确保当前目录中有该文件）

hdfs dfs -put LICENSE.txt /data/input

3.查看hdfs中是否有我们刚复制进去的文件

hdfs dfs ls /data/input
</code></pre>

<p><img src="https://img-blog.csdn.net/20171019121132042?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl4aWJpbjA4Mjk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<pre><code>4.进入share的上层目录，提交单词统计任务

hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.8.1.jar wordcount /data/input /data/output/result


接着就会进行map和reduce

5.查看结果

hdfs dfs -cat /data/output/result/part-r-00000
</code></pre>

<p><img src="https://img-blog.csdn.net/20171019121109248?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl4aWJpbjA4Mjk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="如图" title=""></p>

<pre><code>OK，成功！
</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>