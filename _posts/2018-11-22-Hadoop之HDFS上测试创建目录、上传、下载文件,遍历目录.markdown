---
layout:     post
title:      Hadoop之HDFS上测试创建目录、上传、下载文件,遍历目录
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/JQ_AK47/article/details/82114114				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="遍历">遍历</h1>



<h2 id="循环遍历根目录慎重">循环遍历根目录，慎重</h2>



<pre class="prettyprint"><code class=" hljs lasso">hdfs dfs <span class="hljs-attribute">-ls</span> <span class="hljs-attribute">-R</span> <span class="hljs-subst">/</span></code></pre>



<h2 id="遍历根目录文件夹和文件">遍历根目录文件夹和文件</h2>



<pre class="prettyprint"><code class=" hljs mel">hdfs dfs -<span class="hljs-keyword">ls</span>  /   </code></pre>



<h1 id="上传文件-到hdfs已经存在的目录下">上传文件 到HDFS已经存在的目录下</h1>



<pre class="prettyprint"><code class=" hljs lasso">上传文件到 <span class="hljs-subst">/</span> 跟目录下
hdfs dfs <span class="hljs-attribute">-put</span> /home/vc/dev/hadoop/hadoop<span class="hljs-subst">-</span><span class="hljs-number">2.7</span><span class="hljs-number">.7</span>/etc/hadoop/hdfs<span class="hljs-attribute">-site</span><span class="hljs-built_in">.</span><span class="hljs-built_in">xml</span>  <span class="hljs-subst">/</span></code></pre>



<h1 id="查看刚才上传文件">查看刚才上传文件</h1>



<pre class="prettyprint"><code class=" hljs lasso">hdfs dfs <span class="hljs-attribute">-cat</span> /hdfs<span class="hljs-attribute">-site</span><span class="hljs-built_in">.</span><span class="hljs-built_in">xml</span></code></pre>



<h1 id="下载文件">下载文件</h1>

<p>将hdfs 文件系统中<code>/hdfs-site.xml</code>下载到服务器当前执行命令所在目录</p>



<pre class="prettyprint"><code class=" hljs lasso">hdfs dfs <span class="hljs-attribute">-get</span> /hdfs<span class="hljs-attribute">-site</span><span class="hljs-built_in">.</span><span class="hljs-built_in">xml</span></code></pre>



<h1 id="删除文件和目录">删除文件和目录</h1>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-comment">//删除目录，相比删除文件多一个参数 -r</span>
 hdfs dfs <span class="hljs-attribute">-rm</span> <span class="hljs-attribute">-r</span> /demo1
 <span class="hljs-comment">//删除文件,好像加入 -r 参数也可以删除，不会报错。</span>
 hdfs dfs <span class="hljs-attribute">-rm</span>  <span class="hljs-attribute">-r</span> /hdfs<span class="hljs-attribute">-site</span><span class="hljs-built_in">.</span><span class="hljs-built_in">xml</span>
  hdfs dfs <span class="hljs-attribute">-rm</span> /testone/core<span class="hljs-attribute">-site</span><span class="hljs-built_in">.</span><span class="hljs-built_in">xml</span></code></pre>



<h1 id="创建目录">创建目录</h1>

<p>在根目录下新建 demo1 目录</p>



<pre class="prettyprint"><code class=" hljs perl">hdfs dfs -<span class="hljs-keyword">mkdir</span> /demo1</code></pre>



<h1 id="管理命令">管理命令</h1>



<h2 id="dfsadmin">dfsadmin</h2>

<p>查看文件系统的基本信息和统计信息。</p>



<pre class="prettyprint"><code class=" hljs vhdl"> hdfs dfsadmin -<span class="hljs-keyword">report</span></code></pre>

<p>参考：</p>

<p><a href="https://blog.csdn.net/tanqu9315/article/details/79065594" rel="nofollow">Linux上传本地文件到HDFS</a></p>

<p><a href="https://blog.csdn.net/qq_20989105/article/details/79181854" rel="nofollow">Hadoop之HDFS上测试创建目录、上传、下载文件</a></p>

<p><a href="https://blog.csdn.net/sunshingheavy/article/details/53227581" rel="nofollow">Hadoop Hdfs常用命令</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>