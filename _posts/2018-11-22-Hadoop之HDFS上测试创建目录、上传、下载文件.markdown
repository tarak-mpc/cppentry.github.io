---
layout:     post
title:      Hadoop之HDFS上测试创建目录、上传、下载文件
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_20989105/article/details/79181854				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h4 id="1hdfs上创建目录">1、HDFS上创建目录</h4>



<pre class="prettyprint"><code class=" hljs perl"><span class="hljs-variable">${HADOOP_HOME}</span>/bin/hdfs dfs -<span class="hljs-keyword">mkdir</span> /demo1</code></pre>

<h4 id="2上传本地文件到hdfs上">2、上传本地文件到HDFS上</h4>

<pre class="prettyprint"><code class=" hljs lasso">${HADOOP_HOME}/bin/hdfs dfs <span class="hljs-attribute">-put</span> ${HADOOP_HOME}/etc/hadoop/core<span class="hljs-attribute">-site</span><span class="hljs-built_in">.</span><span class="hljs-built_in">xml</span> /demo1</code></pre>



<h4 id="3上传本地文件到hdfs上">3、上传本地文件到HDFS上</h4>

<pre class="prettyprint"><code class=" hljs lasso">${HADOOP_HOME}/bin/hdfs dfs <span class="hljs-attribute">-cat</span> /demo1/core<span class="hljs-attribute">-site</span><span class="hljs-built_in">.</span><span class="hljs-built_in">xml</span></code></pre>



<h4 id="4从hdfs上下载文件到本地">4、从HDFS上下载文件到本地</h4>

<pre class="prettyprint"><code class=" hljs lasso">${HADOOP_HOME}/bin/hdfs dfs <span class="hljs-attribute">-get</span> /demo1/core<span class="hljs-attribute">-site</span><span class="hljs-built_in">.</span><span class="hljs-built_in">xml</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>