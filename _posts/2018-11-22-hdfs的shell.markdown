---
layout:     post
title:      hdfs的shell
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u010916338/article/details/81102346				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>一,查看hdfs文件</p>



<pre class="prettyprint"><code class=" hljs mel">hadoop fs -<span class="hljs-keyword">ls</span> /                    查看HDFS根目录</code></pre>

<p>二,下载hdfs文件</p>



<pre class="prettyprint"><code class=" hljs cs">hadoop fs -<span class="hljs-keyword">get</span> /test/test.txt    /home/hbase</code></pre>

<p>三,删除hdfs文件或者文件夹</p>



<pre class="prettyprint"><code class=" hljs lasso">hadoop fs <span class="hljs-attribute">-rm</span> /test1/test<span class="hljs-built_in">.</span>txt
hadoop fs <span class="hljs-attribute">-rmr</span> /test1</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>