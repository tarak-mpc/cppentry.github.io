---
layout:     post
title:      hadoop之MR Job案例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<ol>
<li>
</ol>
<p>Linux 文件存储系统</p>
<p>HDFS 分布式文件存储系统</p>
<p>hdfs是架在linux上的分布式文件存储系统，如果要操作hdfs,必须在linux命令前加<em>hdfs dfs</em></p>
<p>2.部署好hdfs，然后创建用户<br>
<img src="https://img-blog.csdn.net/20181012221000771?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0phc2Vyb2s=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<p>3.bin/hdfs dfs -put etc/hadoop input  （-put命令，把etc/hadoop这个目录放到input这个目录下，input就是指当前所在的目录即/uer/hadoop这个目录下）<img src="https://img-blog.csdn.net/20181012221630405?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0phc2Vyb2s=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<p>4.<img src="https://img-blog.csdn.net/2018101222224095?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0phc2Vyb2s=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<p>5.bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.6.0-cdh5.7.0.jar grep input output ‘fs[a-z.]+’<img src="https://img-blog.csdn.net/20181012222357545?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0phc2Vyb2s=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<p>6.map和reduce在跑的过程中，并不是一个跑完另一个再跑，可同时进行，如下图<img src="https://img-blog.csdn.net/2018101222305869?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0phc2Vyb2s=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"><img src="https://img-blog.csdn.net/20181012223116777?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0phc2Vyb2s=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>