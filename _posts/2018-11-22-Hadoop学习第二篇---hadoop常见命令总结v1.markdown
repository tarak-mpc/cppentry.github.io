---
layout:     post
title:      Hadoop学习第二篇---hadoop常见命令总结v1
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/Tiger_lin1/article/details/83684915				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>1、查看指定目录下内容<br>
hdfs dfs -ls [文件目录]<br>
例：hdfs dfs -ls  /</p>
<p>hdfs hdfs dfs -ls  -R / 显示目录结构</p>
<p>2、 打开一个已存在文件<br>
hdfs dfs –cat [file_path]<br>
hdfs dfs -cat /kmeans_data.txt</p>
<p>3、将本地文件存储到Hadoop<br>
hdfs dfs –put [本地地址] [hadoop目录]<br>
例：<br>
hdfs dfs -put 1.log /</p>
<p>4.将本地文件夹存储至hadoop<br>
hdfs dfs –put [本地目录] [hadoop目录]<br>
例：<br>
hadoop dfs -put /lw /lw</p>
<p>5、将hadoop上某个文件down至本地已有目录下<br>
hadoop dfs -get [文件目录] [本地目录]<br>
例：<br>
hadoop dfs -get /lw /lw1</p>
<p>6、删除Hadoop 上指定文件<br>
hdfs  dfs –rm [文件地址]<br>
例：<br>
hdfs dfs -rm /1.log</p>
<p>删除前：</p>
<p>删除后：</p>
<p>7、删除hadoop上指定文件夹（包含子目录等）</p>
<pre><code> hdfs dfs –rm [目录地址]
</code></pre>
<p>例：<br>
hdfs dfs -rmr /lw</p>
<p>删除前：</p>
<p>删除后：</p>
<p>8、在hadoop指定目录内创建新目录</p>
<p>hdfs dfs -mkdir /lin1</p>
<p>hdfs dfs -mkdir -p /lin2/lin1</p>
<p>9、在hadoop指定目录下新建一个空文件</p>
<pre><code>使用touchz命令：
</code></pre>
<p>hdfs dfs  -touchz  /lin1/new.txt</p>
<p>10、将hadoop上某个文件重命名</p>
<p>使用mv命令：</p>
<p>hdfs dfs –mv /lin1/new.txt /lin1/ok.txt （将new.txt重命名为ok.txt）</p>
<p>11、将hadoop指定目录下所有内容保存为一个文件，同时down至本地<br>
hdfs dfs -getmerge /lin1 /lin1</p>
<p>12、查看帮助<br>
hdfs dfs -help</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>