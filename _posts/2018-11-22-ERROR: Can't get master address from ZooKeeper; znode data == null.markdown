---
layout:     post
title:      ERROR: Can't get master address from ZooKeeper; znode data == null
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/xiewendong93/article/details/51338380				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3 id="异常起因">异常起因</h3>

<blockquote>
  <p>Hbase创建表产生</p>
</blockquote>

<h3 id="异常原因">异常原因</h3>

<blockquote>
  <p>虚拟机经常挂起的状态，使用hbase的命令出现下面错误，可能是hbase的稳定性造成的</p>
</blockquote>

<h3 id="解决方案">解决方案</h3>

<blockquote>
  <p>重启hbase <br>
  stop-hbase.sh <br>
  start-hbase.sh</p>
</blockquote>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>