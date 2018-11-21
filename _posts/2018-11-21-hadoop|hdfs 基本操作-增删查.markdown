---
layout:     post
title:      hadoop|hdfs 基本操作-增删查
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：如未注明转载，则均为公子原创。原创不易，如果有用，请动动小手指顶一下，多谢多谢!					https://blog.csdn.net/mulangren1988/article/details/54411170				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="hdfs-增删查等基本操作">hdfs 增删查等基本操作</h2>



<h2 id="查看">查看</h2>



<pre class="prettyprint"><code class=" hljs coffeescript">hadoop fs -ls <span class="hljs-regexp">/user/hadoop/dw_realtime/config/</span></code></pre>



<h2 id="删除文件">删除文件</h2>



<pre class="prettyprint"><code class=" hljs objectivec">hadoop fs -rm /user/hadoop/dw_realtime/config/<span class="hljs-keyword">id</span><span class="hljs-variable">.properties</span></code></pre>



<h2 id="删除目录及文件">删除目录及文件</h2>



<pre class="prettyprint"><code class=" hljs coffeescript">hadoop fs -rmr <span class="hljs-regexp">/user/hadoop/dw_realtime/config/</span>
<span class="hljs-comment"># or</span>
hadoop fs -rm -r <span class="hljs-regexp">/user/hadoop/dw_realtime/config/</span></code></pre>



<h2 id="创建目录">创建目录</h2>



<pre class="prettyprint"><code class=" hljs perl">hadoop fs -<span class="hljs-keyword">mkdir</span> /user/hadoop/dw_realtime/config/</code></pre>



<h2 id="上传文件">上传文件</h2>



<pre class="prettyprint"><code class=" hljs applescript">hadoop fs -<span class="hljs-keyword">put</span> ./<span class="hljs-property">id</span>.properties /user/hadoop/dw_realtime/config/</code></pre>



<h2 id="查看文件全部内容">查看文件全部内容</h2>



<pre class="prettyprint"><code class=" hljs objectivec">hadoop fs -cat /user/hadoop/dw_realtime/config/<span class="hljs-keyword">id</span><span class="hljs-variable">.properties</span></code></pre>



<h2 id="查看文件结尾1000字符">查看文件结尾1000字符</h2>



<pre class="prettyprint"><code class=" hljs objectivec">hadoop fs -tail /user/hadoop/dw_realtime/config/<span class="hljs-keyword">id</span><span class="hljs-variable">.properties</span></code></pre>



<h2 id="将hdfs文件拉取到指定目录下">将hdfs文件拉取到指定目录下</h2>



<pre class="prettyprint"><code class=" hljs applescript">hadoop fs -<span class="hljs-keyword">get</span> /user/hadoop/dw_realtime/config/<span class="hljs-property">id</span>.properties ./</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>