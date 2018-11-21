---
layout:     post
title:      openTSDB详解之底层HBase表分析
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：如若转载，请联系作者。					https://blog.csdn.net/liu16659/article/details/83786486				</div>
								            <div id="content_views" class="markdown_views prism-dracula">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3><a id="openTSDBHBase_Updating_0"></a>openTSDB详解之底层HBase表分析 【Updating】</h3>
<ul>
<li>查看hbase的表负载情况</li>
</ul>
<pre><code>hbase(main):001:0&gt; status
1 active master, 0 backup masters, 3 servers, 0 dead, 2.3333 average load
</code></pre>
<ul>
<li>列出表名</li>
</ul>
<pre><code class="prism language-shell">hbase<span class="token punctuation">(</span>main<span class="token punctuation">)</span>:002:0<span class="token operator">&gt;</span> list
TABLE                                                                                                                                                                                                      
tsdb                                                                                                                                                                                                       
tsdb-meta                                                                                                                                                                                                  
tsdb-tree                                                                                                                                                                                                  
tsdb-uid                                                                                                                                                                                                   
4 row<span class="token punctuation">(</span>s<span class="token punctuation">)</span> <span class="token keyword">in</span> 0.1020 seconds
<span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token punctuation">[</span><span class="token string">"tsdb"</span>, <span class="token string">"tsdb-meta"</span>, <span class="token string">"tsdb-tree"</span>, <span class="token string">"tsdb-uid"</span><span class="token punctuation">]</span>
</code></pre>
<ul>
<li>查看tsdb表结构</li>
</ul>
<pre><code class="prism language-shell">hbase<span class="token punctuation">(</span>main<span class="token punctuation">)</span>:008:0<span class="token operator">&gt;</span> desc <span class="token string">'tsdb'</span>
Table tsdb is ENABLED                                                                                                                                                                                      
tsdb                                                                                                                                                                                                       
COLUMN FAMILIES DESCRIPTION                                                                                                                                                                                
<span class="token punctuation">{</span>NAME <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'t'</span>, BLOOMFILTER <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'ROW'</span>, VERSIONS <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'1'</span>, IN_MEMORY <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'false'</span>, KEEP_DELETED_CELLS <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'FALSE'</span>, DATA_BLOCK_ENCODING <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'NONE'</span>, TTL <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'FOREVER'</span>, COMPRESSION <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'NONE'</span>, MIN_VERSIONS <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'0'</span>, BLOC
KCACHE <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'true'</span>, BLOCKSIZE <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'65536'</span>, REPLICATION_SCOPE <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'0'</span><span class="token punctuation">}</span>                                                                                                                                          
1 row<span class="token punctuation">(</span>s<span class="token punctuation">)</span> <span class="token keyword">in</span> 0.0240 seconds
</code></pre>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>