---
layout:     post
title:      mkdir: Permission denied: user=root, access=WRITE
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/github_38358734/article/details/77534663				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>原因：root用户没有写入hdfs的权限 <br>
解决：</p>

<p>1、切换hdfs用户后，重新执行mkdir命令</p>

<p>2、修改advanced.permissions.enabled =false <br>
重启hdfs即可。</p>

<p>不过，最好切换用户执行命令。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>