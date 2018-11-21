---
layout:     post
title:      常用hadoop dfs命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创或整理自网络，转载请注明出处。					https://blog.csdn.net/u011331383/article/details/79749277				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>创建目录</p>



<pre class="prettyprint"><code class=" hljs perl">hadoop dfs -<span class="hljs-keyword">mkdir</span> /home</code></pre>

<p>上传文件或目录到hdfs</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hadoop dfs -<span class="hljs-built_in">put</span> hello /
hadoop dfs -<span class="hljs-built_in">put</span> hellodir/ /</code></pre>

<p>查看目录</p>



<pre class="prettyprint"><code class=" hljs mel">hadoop dfs -<span class="hljs-keyword">ls</span> /</code></pre>

<p>创建一个空文件</p>



<pre class="prettyprint"><code class=" hljs lasso">hadoop dfs <span class="hljs-attribute">-touchz</span> /<span class="hljs-number">361</span>way</code></pre>

<p>删除一个文件</p>



<pre class="prettyprint"><code class=" hljs lasso">hadoop dfs <span class="hljs-attribute">-rm</span> /<span class="hljs-number">361</span>way</code></pre>

<p>删除一个目录</p>



<pre class="prettyprint"><code class=" hljs lasso">hadoop dfs <span class="hljs-attribute">-rmr</span> /home</code></pre>

<p>重命名</p>



<pre class="prettyprint"><code class=" hljs lasso">hadoop dfs <span class="hljs-attribute">-mv</span> /hello1 /hello2</code></pre>

<p>查看文件</p>



<pre class="prettyprint"><code class=" hljs lasso">hadoop dfs <span class="hljs-attribute">-cat</span> /hello</code></pre>

<p>将制定目录下的所有内容merge成一个文件，下载到本地</p>



<pre class="prettyprint"><code class=" hljs lasso">hadoop dfs <span class="hljs-attribute">-getmerge</span> /hellodir wa</code></pre>

<p>使用du文件和目录大小</p>



<pre class="prettyprint"><code class=" hljs lasso">hadoop dfs <span class="hljs-attribute">-du</span> <span class="hljs-subst">/</span></code></pre>

<p>将目录拷贝到本地</p>



<pre class="prettyprint"><code class=" hljs lasso">hadoop dfs <span class="hljs-attribute">-copyToLocal</span>  /home localdir</code></pre>

<p>查看dfs的情况</p>



<pre class="prettyprint"><code class=" hljs vhdl">hadoop dfsadmin -<span class="hljs-keyword">report</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>