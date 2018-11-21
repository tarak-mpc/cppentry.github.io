---
layout:     post
title:      Hadoop实践（四）---在HDFS中合并文件
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/Wee_Mita/article/details/52750052				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>HDFS的getMerge命令可以将HDFS中给定路径下的文件复制到本地文件系统的单个合并后的文件中</p>



<pre class="prettyprint"><code class=" hljs avrasm">bin/hadoopfs -getmerge /user/foo/demofiles mergrd<span class="hljs-preprocessor">.txt</span></code></pre>

<p>getmerge命令的语法</p>



<pre class="prettyprint"><code class=" hljs xml">hadoopfs -getmerge <span class="hljs-tag">&lt;<span class="hljs-title">src</span>&gt;</span> <span class="hljs-tag">&lt;<span class="hljs-title">localdst</span>&gt;</span> [addnl]</code></pre>

<p>第一个参数是HDFS的路径，它包含要连接文件的HDFS目录 <br>
第二个参数是合并文件的本地文件名 <br>
第三个参数是可选的，表示是否需要在每个合并后的结果文件末尾添加新行</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>