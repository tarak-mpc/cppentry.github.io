---
layout:     post
title:      hdfs的shell命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/lovelovelovelovelo/article/details/51865204				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>列出hdfs上的文件 <br>
hadoop fs -ls  -R (递归查看)  hdfs://ubuntu:9000/ <br>
可以简写为hadoop fs -ls /</p>

<p>查看hdfs文件内容 <br>
hadoop fs -cat /xx</p>

<p>从本地拷贝文件到hdfs <br>
hadoop fs -copyFromLocal  /root/xxx(linux上文件) /(hdfs的根) <br>
同-put命令</p>

<p>从hdfs拷贝文件到本地 <br>
hadoop fs - copyToLocal …. <br>
同-get命令</p>

<p>统计 <br>
hadoop -fs -count / <br>
文件夹数  文件数 占用字节数   </p>

<p>删除hdfs上文件 <br>
hadoop fs -rm </p>

<p>hdfs上的文件拷贝 <br>
hadoop fs -cp ……</p>

<p>新建文件夹 <br>
hadoop fs -mkdir ….</p>

<p>从本地剪切文件到hdfs <br>
hadoop fs -moveFromLocal</p>

<p>从hdfs剪切文件到本地 <br>
hadoop fs -moveToLocal</p>

<p>查看文件的尾部 <br>
hadoop fs -tail -f …..</p>

<p>改变权限 <br>
hadoop fs -chmod -R -x  /out 删除out及其下面文件的x权限</p>

<p>改变所有者 <br>
hadoop fs -chown supergroup /file.txt  改变file.txt的所有者为supergroup</p>

<p>改变组 <br>
hadoop fs -chgrp group /file.txt</p>

<p>同时改变用户和组 <br>
hadoop fs -chown owner:group /file.txt</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>