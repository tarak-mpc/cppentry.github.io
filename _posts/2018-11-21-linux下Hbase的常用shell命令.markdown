---
layout:     post
title:      linux下Hbase的常用shell命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qy20115549/article/details/53001478				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>本文作者：合肥工业大学 管理学院 钱洋 email：1563178220@qq.com</p>

<p>目录</p>

<p></p><div class="toc"><div class="toc">
<ul>
<li><a href="#linux%E4%B8%8B%E6%9F%A5%E7%9C%8Bhbase%E7%9A%84%E5%AE%89%E8%A3%85%E8%B7%AF%E5%BE%84" rel="nofollow">linux下查看hbase的安装路径</a></li>
<li><a href="#hbase-shell%E5%92%8Chbase%E4%BA%A4%E4%BA%92" rel="nofollow">HBase Shell和HBase交互</a></li>
<li><a href="#hbase%E5%B8%B8%E7%94%A8shell%E8%AF%AD%E5%8F%A5" rel="nofollow">HBase常用shell语句</a><ul>
<li><a href="#%E5%88%9B%E5%BB%BA%E8%A1%A8" rel="nofollow">创建表</a></li>
<li><a href="#%E6%B7%BB%E5%8A%A0%E8%AE%B0%E5%BD%95" rel="nofollow">添加记录</a></li>
<li><a href="#%E6%9F%A5%E7%9C%8B%E6%89%80%E6%9C%89%E8%AE%B0%E5%BD%95" rel="nofollow">查看所有记录</a></li>
<li><a href="#%E6%9F%A5%E7%9C%8B%E8%A1%A8%E4%B8%AD%E8%AE%B0%E5%BD%95%E6%95%B0" rel="nofollow">查看表中记录数</a></li>
<li><a href="#%E5%88%A0%E9%99%A4%E8%AE%B0%E5%BD%95" rel="nofollow">删除记录</a></li>
<li><a href="#%E5%88%A0%E9%99%A4%E8%A1%A8" rel="nofollow">删除表</a></li>
</ul>
</li>
</ul>
</div>
</div>




<h1 id="linux下查看hbase的安装路径">linux下查看hbase的安装路径</h1>



<pre class="prettyprint"><code class="language-shell hljs applescript">find / -<span class="hljs-property">name</span> hbase</code></pre>

<p><img src="https://img-blog.csdn.net/20161101212356222" alt="这里写图片描述" title=""></p>



<h1 id="hbase-shell和hbase交互">HBase Shell和HBase交互</h1>



<pre class="prettyprint"><code class="language-shell hljs ">/usr/hdp/2.3.4.0-3485/hbase/bin/hbase</code></pre>

<p><img src="https://img-blog.csdn.net/20161101212537381" alt="这里写图片描述" title=""></p>

<p>上面的图片可以看到hbase的命令。 <br>
<img src="https://img-blog.csdn.net/20161101212814241" alt="这里写图片描述" title=""></p>



<h1 id="hbase常用shell语句">HBase常用shell语句</h1>

<p>如下表例举了HBase常用的shell语句。</p>

<p><img src="https://img-blog.csdn.net/20161101213022778" alt="这里写图片描述" title=""></p>

<p>下面一一对shell语句进行演示。</p>



<h2 id="创建表">创建表</h2>

<p><img src="https://img-blog.csdn.net/20161101213624777" alt="这里写图片描述" title=""></p>



<h2 id="添加记录">添加记录</h2>

<p><img src="https://img-blog.csdn.net/20161101214408937" alt="这里写图片描述" title=""></p>

<h2 id="查看所有记录">查看所有记录</h2>

<p><img src="https://img-blog.csdn.net/20161101214526156" alt="这里写图片描述" title=""></p>

<h2 id="查看表中记录数">查看表中记录数</h2>

<p><img src="https://img-blog.csdn.net/20161101214717768" alt="这里写图片描述" title=""></p>



<h2 id="删除记录">删除记录</h2>

<p><img src="https://img-blog.csdn.net/20161101215139346" alt="这里写图片描述" title=""></p>



<h2 id="删除表">删除表</h2>

<p><img src="https://img-blog.csdn.net/20161101215332715" alt="这里写图片描述" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>