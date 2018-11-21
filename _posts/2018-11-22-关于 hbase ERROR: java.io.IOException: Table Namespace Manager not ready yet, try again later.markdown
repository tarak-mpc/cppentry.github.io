---
layout:     post
title:      关于 hbase ERROR: java.io.IOException: Table Namespace Manager not ready yet, try again later
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/Petershusheng/article/details/51607411				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>最近学习hbase时遇到这个问题,一开始安装hbase时create表是可以的,但不知为何后来就不行了,重装hbase但不重装hadoop也不行.说明问题出在hadoop内保存的hbase的数据上面.将hadoop内的hbase文件删除即可; <br>
首先使用该命令检查hadoop内的hbase文件夹: <br>
<img src="https://img-blog.csdn.net/20160607230815364" alt="这里写图片描述" title=""> <br>
然后使用下面的命令删除hadoop中的hbase文件: <br>
<img src="https://img-blog.csdn.net/20160607230957950" alt="这里写图片描述" title=""> <br>
然后再使用hadoop fs -ls / 命令查看文件夹是否已经删除. <br>
然后再重新进入hbase命令行即可建表.</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>