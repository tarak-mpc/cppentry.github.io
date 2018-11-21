---
layout:     post
title:      HBase控制台Shell的命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_27470131/article/details/80160570				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3 id="hbase简单使用-1">HBase简单使用</h3>



<h4 id="hbase控制台终端操作-1">hbase控制台终端操作</h4>



<h5 id="启动hbase的shell-1">启动hbase的shell</h5>



<pre class="prettyprint"><code class="language-shell hljs ruby"><span class="hljs-variable">$ </span>bin/hbase shell</code></pre>



<h5 id="查看帮助命令-1">查看帮助命令</h5>



<pre class="prettyprint"><code class="language-shell hljs scss"><span class="hljs-function">hbase(main)</span>&gt; <span class="hljs-value">help</span></code></pre>



<h5 id="查看当前数据库中有哪些表-1">查看当前数据库中有哪些表</h5>



<pre class="prettyprint"><code class="language-shell hljs erlang"><span class="hljs-function"><span class="hljs-title">hbase</span><span class="hljs-params">(main)</span>&gt; <span class="hljs-title">list</span></span></code></pre>



<h4 id="表的操作-1">表的操作</h4>

<blockquote>
  <p>注意：在hbase shell中退格需要用ctrl+退格键</p>
</blockquote>



<h5 id="创建表-1">创建表</h5>



<pre class="prettyprint"><code class="language-sql hljs "> <span class="hljs-comment">-- create '表名，'列族（colum-family）'</span>
ex: hbase&gt; <span class="hljs-operator"><span class="hljs-keyword">create</span> <span class="hljs-string">'student'</span>,<span class="hljs-string">'info'</span></span></code></pre>



<h5 id="插入数据-1">插入数据</h5>



<pre class="prettyprint"><code class="language-shell hljs livecodeserver">hbase&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'表名'</span>，<span class="hljs-string">'rowkey'</span>,<span class="hljs-string">'列族名：列名1'</span>,<span class="hljs-string">'值'</span>
ex: hbase&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'student'</span>,<span class="hljs-string">'1001'</span>,<span class="hljs-string">'info:name'</span>,<span class="hljs-string">'daxiong'</span>
hbase(main) &gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'student'</span>,<span class="hljs-string">'1001'</span>,<span class="hljs-string">'info:name'</span>,<span class="hljs-string">'Thomas'</span>
hbase(main) &gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'student'</span>,<span class="hljs-string">'1001'</span>,<span class="hljs-string">'info:sex'</span>,<span class="hljs-string">'male'</span>
hbase(main) &gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'student'</span>,<span class="hljs-string">'1001'</span>,<span class="hljs-string">'info:age'</span>,<span class="hljs-string">'18'</span>
hbase(main) &gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'student'</span>,<span class="hljs-string">'1002'</span>,<span class="hljs-string">'info:name'</span>,<span class="hljs-string">'Janna'</span>
hbase(main) &gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'student'</span>,<span class="hljs-string">'1002'</span>,<span class="hljs-string">'info:sex'</span>,<span class="hljs-string">'female'</span>
hbase(main) &gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'student'</span>,<span class="hljs-string">'1002'</span>,<span class="hljs-string">'info:age'</span>,<span class="hljs-string">'20'</span>
</code></pre>



<h5 id="查看数据-1">查看数据</h5>



<pre class="prettyprint"><code class="language-sql hljs "><span class="hljs-comment">--查看全表</span>
hbase &gt; scan 'student'
<span class="hljs-comment">-- 查看指定范围</span>
hbase &gt; scan 'student',{STARTROW =&gt; '1001',STOPROW =&gt; '1002'}
<span class="hljs-comment">-- 查看指定行</span>
hbase &gt; get 'student','1001'
hbase &gt; get 'student','1001','info'
hbase &gt; get 'student','1001','info:name'</code></pre>

<blockquote>
  <p>上述操作的区间：前闭后开</p>
</blockquote>



<h5 id="查看表结构-1">查看表结构</h5>



<pre class="prettyprint"><code class="language-sql hljs ">hbase(main): describe 'student'</code></pre>



<h5 id="删除数据-1">删除数据</h5>



<pre class="prettyprint"><code class="language-sql hljs "><span class="hljs-comment">--删除某rowkey的全部数据</span>
hbase(main) &gt; deleteall 'student','1001'
<span class="hljs-comment">--删除某rowkey的全部数据</span>
hbase(main) &gt; deleteall 'student','1001','info:sex'</code></pre>



<h5 id="清空表-1">清空表</h5>



<pre class="prettyprint"><code class="language-sql hljs ">hbase(main) &gt; <span class="hljs-operator"><span class="hljs-keyword">truncate</span> <span class="hljs-string">'student'</span></span></code></pre>

<blockquote>
  <p>清空表的操作顺序为先disable，然后再truncating</p>
</blockquote>



<h5 id="删除表-1">删除表</h5>



<pre class="prettyprint"><code class="language-sql hljs "><span class="hljs-comment">--首先需要先让该表为disable状态</span>
hbase(main) &gt; disable 'student'
<span class="hljs-comment">--然后才能drop这个表</span>
hbase(main) &gt; <span class="hljs-operator"><span class="hljs-keyword">drop</span> <span class="hljs-string">'student'</span></span></code></pre>

<blockquote>
  <p>如果直接drop表，会报错：Drop the named table. Tablemust first be disabled</p>
  
  <p>ERROR: Table student is enabled. Disable itfirst.</p>
</blockquote>



<h5 id="统计表数据行数-1">统计表数据行数</h5>



<pre class="prettyprint"><code class="language-sql hljs ">hbase(main) &gt; count 'student'</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>