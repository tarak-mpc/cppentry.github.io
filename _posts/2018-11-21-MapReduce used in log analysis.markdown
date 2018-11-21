---
layout:     post
title:      MapReduce used in log analysis
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：鄙人不才，对于知识的理解难免有误，希望各位批评指正					https://blog.csdn.net/wedy542700927/article/details/72912102				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="hadoop">Hadoop</h2>

<p>Master/Slave model</p>

<ul>
<li><p>HDFS <br>
namenode/datanode</p>

<pre><code>hadoop fs -ls XXX / hls XXX
hadoop fs -rmr XXX
hadoop fs -cat XXX
hadoop fs -get XXX
hadoop job -list
hadoop job -kill XXX
</code></pre></li>
<li><p>Map/Reduce</p>

<p>jobtracker/tasktracker</p>

<p>Map =&gt; Shullfe =&gt; Reduce</p></li>
</ul>



<h2 id="pipe">Pipe</h2>

<p>stdout =&gt; stdin</p>

<pre><code>less XXX | ./map | ./reduce
</code></pre>



<h2 id="shullfe">Shullfe</h2>

<ul>
<li>copy</li>
<li>sort</li>
<li>merge</li>
</ul>

<p>same key in same reduce task <br>
<img src="http://dl.iteye.com/upload/attachment/456531/4df193f5-e56e-308f-9689-eac035dd8a2b.png" alt="这里写图片描述" title=""></p>

<h2 id="log-analysis">Log Analysis</h2>

<ol>
<li><p>out of memory???</p>

<ul><li>mapred.max.split.size</li>
<li>mapred.reduce.tasks</li></ul></li>
<li><p>how to balance time of map and reduce???</p></li>
</ol>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>