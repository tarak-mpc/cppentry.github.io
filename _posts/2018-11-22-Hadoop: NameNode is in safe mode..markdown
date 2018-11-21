---
layout:     post
title:      Hadoop: NameNode is in safe mode.
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<blockquote>
  <p>safe mode： 只读状态</p>
</blockquote>



<h2 id="关闭">关闭</h2>

<blockquote>
  <p>hadoop dfsadmin -safemode leave</p>
</blockquote>



<h2 id="开启">开启</h2>

<blockquote>
  <p>hadoop dfsadmin -safemode enter</p>
</blockquote>



<h2 id="查询">查询</h2>

<blockquote>
  <p>hadoop dfsadmin -safemode get</p>
</blockquote>



<h2 id="阻塞直到安全模式解除">阻塞直到安全模式解除</h2>

<blockquote>
  <p>hadoop dfsadmin -safemode wait</p>
</blockquote>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>