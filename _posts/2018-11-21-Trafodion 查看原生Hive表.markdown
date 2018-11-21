---
layout:     post
title:      Trafodion 查看原生Hive表
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，如需转载，请注明出处。					https://blog.csdn.net/Post_Yuan/article/details/54630220				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>Trafodion中的get命令可以用来查看Hive中有哪些表，具体方法如下， <br>
关于get命令的详细用法，请参考官方文档：<a href="http://trafodion.incubator.apache.org/docs/sql_reference/index.html#get_statement" rel="nofollow">http://trafodion.incubator.apache.org/docs/sql_reference/index.html#get_statement</a></p>

<p>1 hive命令查看hive中的表</p>



<pre class="prettyprint"><code class=" hljs haskell"><span class="hljs-title">hive</span>&gt; show tables <span class="hljs-keyword">in</span> <span class="hljs-default"><span class="hljs-keyword">default</span>;</span>
<span class="hljs-type">OK</span>
<span class="hljs-title">aa</span>
<span class="hljs-title">eboxdata</span>
<span class="hljs-title">eboxdata30w</span>
<span class="hljs-title">eboxdata_30m</span>
<span class="hljs-title">eboxdata_30m_tmp</span>
<span class="hljs-title">native_zhiyi</span>
<span class="hljs-type">Time</span> taken: <span class="hljs-number">0.024</span> seconds, <span class="hljs-type">Fetched</span>: <span class="hljs-number">6</span> row(s)

<span class="hljs-title">hive</span>&gt; show schemas;
<span class="hljs-type">OK</span>
<span class="hljs-default"><span class="hljs-keyword">default</span></span>
<span class="hljs-title">pksaas</span>
<span class="hljs-type">Time</span> taken: <span class="hljs-number">1.814</span> seconds, <span class="hljs-type">Fetched</span>: <span class="hljs-number">2</span> row(s)</code></pre>

<p>2 Trafodion查看hive中的表</p>



<pre class="prettyprint"><code class=" hljs applescript">[trafodion@n12 ~]$ trafci

Welcome <span class="hljs-keyword">to</span> EsgynDB Enterprise Command Interface
Copyright (c) <span class="hljs-number">2015</span>-<span class="hljs-number">2016</span> Esgyn Corporation

Host Name/IP Address: n12:<span class="hljs-number">23400</span>
User Name: zz

Connected <span class="hljs-keyword">to</span> EsgynDB Advanced

SQL&gt;<span class="hljs-keyword">get</span> tables <span class="hljs-keyword">in</span> schema hive.hive;

aa
eboxdata
eboxdata_30m
eboxdata_30m_tmp
eboxdata30w
native_zhiyi

<span class="hljs-comment">--- SQL operation complete.</span>

SQL&gt;<span class="hljs-keyword">get</span> tables <span class="hljs-keyword">in</span> schema hive.pksaas;

eboxdata

<span class="hljs-comment">--- SQL operation complete.</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>