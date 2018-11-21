---
layout:     post
title:      hadoop的hdfs的shell命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_21909689/article/details/50782577				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>hadoop 查看命令及其参数 <br>
hadoop fs -ls / 查看hadoop下的文件及目录 <br>
hadoop fs -put [local] [hadoop] 将本地的文件上传到hdfs上，尽量用绝对地址 <br>
hadoop fs -get [hadoop] [local] 将hdfs上的文件下载到本地 <br>
hadoop fs -rm [hadoop] 删除文件或文件夹-rmr <br>
echo -e ‘hadoop second lesson’&gt;test.txt 将”中的字符串写入文件，如果没有此文件则创建，有则覆盖 <br>
vi [local] 创建文件 <br>
jps 查看hadoop的java进程</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>