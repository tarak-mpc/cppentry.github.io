---
layout:     post
title:      hdfs和spark笔记
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="hdfs的使用类似于linux命令本身只是在前面加入hdfs-dfs-正常的linux命令即hdfs-dfs-ls-hwhdfs中路径">hdfs的使用类似于Linux命令本身，只是在前面加入./hdfs dfs + 正常的Linux命令，即./hdfs dfs -ls /hw(hdfs中路径)</h2>

<p>1.list all files</p>

<p>[rucer@yun0 bin]$ ./hdfs dfs -ls /</p>

<p>drwxr-xr-x - rucer users 0 2017-11-13 13:11 /default_tests_files</p>

<p>2.hdfs导入导出文件</p>

<p>导入命令：./hdfs dfs -copyFromLocal /source/file.txt /destination directory</p>

<p>到出命令（参数）：copyToLocal</p>

<p>./hdfs dfs -copyToLocal /source/file.txt /destination directory</p>

<p>更多命令详解见 <a href="http://www.cnblogs.com/cl1234/p/3566923.html" rel="nofollow">http://www.cnblogs.com/cl1234/p/3566923.html</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>