---
layout:     post
title:      hadoop学习笔记(11)——hbase shell简单操作示例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/thinkpadshi/article/details/50579584				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="1-表设计">1) 表设计</h2>

<p><img src="https://img-blog.csdn.net/20160125135224665" alt="这里写图片描述" title=""></p>



<h2 id="2-建表scores具有两个列族grad和course">2) 建表scores，具有两个列族：grad和course</h2>



<pre class="prettyprint"><code class=" hljs coffeescript">[root<span class="hljs-property">@master</span> bin]<span class="hljs-comment"># hbase shell</span>
hbase(main):<span class="hljs-number">002</span>:<span class="hljs-number">0</span>&gt; create <span class="hljs-string">'scores'</span>,<span class="hljs-string">'grade'</span>,<span class="hljs-string">'course'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">7.6340</span> seconds<span class="hljs-function">
=&gt;</span> <span class="hljs-attribute">Hbase</span>::Table - scores</code></pre>



<h2 id="3-查看hasee中有哪些表">3) 查看Hasee中有哪些表</h2>



<pre class="prettyprint"><code class=" hljs php">hbase(main):<span class="hljs-number">003</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-keyword">list</span>
TABLE                                                                                                                                 
scores                                                                                                                                
<span class="hljs-number">1</span> row(s) in <span class="hljs-number">0.2190</span> seconds
=&gt; [<span class="hljs-string">"scores"</span>]</code></pre>



<h2 id="4-查看表结构">4) 查看表结构</h2>



<pre class="prettyprint"><code class=" hljs coffeescript">hbase(main):<span class="hljs-number">004</span>:<span class="hljs-number">0</span>&gt; describe <span class="hljs-string">'scores'</span>
Table scores <span class="hljs-keyword">is</span> ENABLED                                                                                                               
scores                                                                                                                                
COLUMN FAMILIES DESCRIPTION                                                                                                           
{NAME<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'course'</span>, DATA_BLOCK_ENCODING<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, BLOOMFILTER<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'ROW'</span>, REPLICATION_SCOPE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'1'</span>, COMPRESSION<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NON
E'</span>, MIN_VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, TTL<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FOREVER'</span>, KEEP_DELETED_CELLS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FALSE'</span>, BLOCKSIZE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'65536'</span>, IN_MEMORY<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'false'</span>, BLOCKCACHE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'t
rue'</span>}                                                                                                                                 
{NAME<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'grade'</span>, DATA_BLOCK_ENCODING<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, BLOOMFILTER<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'ROW'</span>, REPLICATION_SCOPE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'1'</span>, COMPRESSION<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE
'</span>, MIN_VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, TTL<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FOREVER'</span>, KEEP_DELETED_CELLS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FALSE'</span>, BLOCKSIZE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'65536'</span>, IN_MEMORY<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'false'</span>, BLOCKCACHE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'tr
ue'</span>}                                                                                                                                  
<span class="hljs-number">2</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.5300</span> seconds</code></pre>



<h2 id="5-添加数据">5) 添加数据</h2>

<p>a)  行名称为 Tom 列名为grad， 值为1</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">005</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'scores'</span>,<span class="hljs-string">'Tom'</span>,<span class="hljs-string">'grade:'</span>,<span class="hljs-string">'1'</span></code></pre>

<p>b)  给Tom这一行的数据的列族添加一列 </p>

<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">005</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'scores'</span>,<span class="hljs-string">'Tom'</span>,<span class="hljs-string">'course:math'</span>,<span class="hljs-string">'93'</span></code></pre>

<p>c)  给Tom这一行的数据的列族添加一列 </p>

<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">005</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'scores'</span>,<span class="hljs-string">'Tom'</span>,<span class="hljs-string">'course:art'</span>,<span class="hljs-string">'82'</span></code></pre>

<p>d)  行名称为 Sam 列名为grad， 值为2</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">005</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'scores'</span>,<span class="hljs-string">'Sam'</span>,<span class="hljs-string">'grade:'</span>,<span class="hljs-string">'2'</span></code></pre>

<p>e)  给Sam这一行的数据的列族添加一列 </p>

<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">005</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'scores'</span>,<span class="hljs-string">'Sam'</span>,<span class="hljs-string">'course:math'</span>,<span class="hljs-string">'97'</span></code></pre>

<p>f)  给Sam这一行的数据的列族添加一列 </p>

<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">005</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'scores'</span>,<span class="hljs-string">'Sam'</span>,<span class="hljs-string">'course:art'</span>,<span class="hljs-string">'76'</span></code></pre>



<h2 id="6-查看scores表中tom的相关数据">6) 查看scores表中Tom的相关数据</h2>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">014</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'scores'</span>,<span class="hljs-string">'Tom'</span>
COLUMN                             CELL                                                                                               
 course:art                        timestamp=<span class="hljs-number">1453694601377</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">76</span>                                                                  
 course:math                       timestamp=<span class="hljs-number">1453694113182</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">93</span>                                                                  
 grade:                            timestamp=<span class="hljs-number">1453694015658</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">1</span>                                                                   
<span class="hljs-number">3</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0190</span> <span class="hljs-built_in">seconds</span></code></pre>



<h2 id="7-查看scores表中所有数据">7) 查看scores表中所有数据</h2>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">015</span>:<span class="hljs-number">0</span>&gt; scan <span class="hljs-string">'scores'</span>
ROW                   COLUMN+CELL                                                                                        
 Sam        column=course:art, timestamp=<span class="hljs-number">1453694637826</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">82</span>                                               
 Sam        column=course:math, timestamp=<span class="hljs-number">1453694531258</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">97</span>                                              
 Sam        column=grade:, timestamp=<span class="hljs-number">1453694479156</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">2</span>                                                    
 Tom        column=course:art, timestamp=<span class="hljs-number">1453694601377</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">76</span>                                               
 Tom        column=course:math, timestamp=<span class="hljs-number">1453694113182</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">93</span>                                              
 Tom        column=grade:, timestamp=<span class="hljs-number">1453694015658</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">1</span>                                                    
<span class="hljs-number">2</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.1770</span> <span class="hljs-built_in">seconds</span></code></pre>



<h2 id="8-查看scores表中所有数据courses列族的所有数据">8) 查看scores表中所有数据courses列族的所有数据</h2>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">015</span>:<span class="hljs-number">0</span>&gt; scan <span class="hljs-string">"scores"</span>,{COLUMNS =&gt; [<span class="hljs-string">'course:math'</span>]}
hbase(main):<span class="hljs-number">015</span>:<span class="hljs-number">0</span>&gt; scan <span class="hljs-string">"scores"</span>,{COLUMNS =&gt; [<span class="hljs-string">'course'</span>]}
ROW       COLUMN+CELL                                                                                        
 Sam      column=course:art, timestamp=<span class="hljs-number">1453694637826</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">82</span>                                               
 Sam      column=course:math, timestamp=<span class="hljs-number">1453694531258</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">97</span>                                              
 Tom      column=course:art, timestamp=<span class="hljs-number">1453694601377</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">76</span>                                               
 Tom      column=course:math, timestamp=<span class="hljs-number">1453694113182</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">93</span>                                              
<span class="hljs-number">2</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0250</span> <span class="hljs-built_in">seconds</span></code></pre>

<h2 id="9-delete表记录">9) delete表记录</h2>



<pre class="prettyprint"><code class=" hljs javascript">hbase(main):<span class="hljs-number">015</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-keyword">delete</span> <span class="hljs-string">'scores'</span>,<span class="hljs-string">'Tom'</span>,<span class="hljs-string">'course:ctrl'</span></code></pre>



<h2 id="10-count表记录">10) count表记录</h2>



<pre class="prettyprint"><code class=" hljs coffeescript">hbase(main):<span class="hljs-number">025</span>:<span class="hljs-number">0</span>&gt; count <span class="hljs-string">'scores'</span>
<span class="hljs-number">2</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.1310</span> seconds<span class="hljs-function">
=&gt;</span> <span class="hljs-number">2</span></code></pre>



<h2 id="11-exists表">11) exists表</h2>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">027</span>:<span class="hljs-number">0</span>&gt; exists <span class="hljs-string">'scores'</span>
Table scores does exist                                                                                                               
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0120</span> <span class="hljs-built_in">seconds</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>