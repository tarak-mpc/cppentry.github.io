---
layout:     post
title:      Hadoop学习笔记——Hadoop常用命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/google19890102/article/details/50962501				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>Hadoop下有一些常用的命令，通过这些命令可以很方便操作Hadoop上的文件。</p>

<p>1、查看指定目录下的内容</p>

<p>语法：</p>



<pre class="prettyprint"><code class=" hljs mel">hadoop fs -<span class="hljs-keyword">ls</span> 文件目录</code></pre>

<p>2、打开某个已存在的文件</p>

<p>语法：</p>



<pre class="prettyprint"><code class=" hljs css"><span class="hljs-tag">hadoop</span> <span class="hljs-tag">fs</span> <span class="hljs-tag">-cat</span> 文件地址 <span class="hljs-attr_selector">[ | more]</span></code></pre>

<blockquote>
  <p>[]表示的是可选命令</p>
</blockquote>

<p>3、将本地文件存到Hadoop</p>

<p>语法：</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hadoop fs -<span class="hljs-built_in">put</span> 本地文件地址 Hadoop目录</code></pre>

<p>4、将Hadoop上的文件下载到本地文件夹内</p>

<p>语法：</p>



<pre class="prettyprint"><code class=" hljs cs">hadoop fs -<span class="hljs-keyword">get</span> Hadoop目录 本地文件目录</code></pre>

<p>5、删除Hadoop上指定的文件</p>

<p>语法：</p>



<pre class="prettyprint"><code class=" hljs lasso">hadoop fs <span class="hljs-attribute">-rm</span> Hadoop文件地址</code></pre>

<p>6、删除Hadoop上指定的文件夹</p>

<p>语法：</p>



<pre class="prettyprint"><code class=" hljs lasso">hadoop fs <span class="hljs-attribute">-rmr</span> Hadoop文件目录</code></pre>

<p>7、在Hadoop指定目录下新建一个空目录</p>

<p>语法：</p>



<pre class="prettyprint"><code class=" hljs perl">hadoop fs -<span class="hljs-keyword">mkdir</span> Hadoop目录</code></pre>

<p>8、在Hadoop指定目录下新建一个空文件</p>

<p>语法：</p>



<pre class="prettyprint"><code class=" hljs lasso">hadoop fs <span class="hljs-attribute">-touchz</span> Hadoop文件</code></pre>

<p>9、将Hadoop上某个文件重命名</p>

<p>语法：</p>



<pre class="prettyprint"><code class=" hljs lasso">hadoop fs <span class="hljs-attribute">-mv</span> Hadoop原文件地址 Hadoop新文件地址</code></pre>

<p>10、将正在运行的Hadoop作业kill掉</p>

<p>语法：</p>



<pre class="prettyprint"><code class=" hljs lasso">hadoop job <span class="hljs-attribute">-kill</span> job<span class="hljs-attribute">-id</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>