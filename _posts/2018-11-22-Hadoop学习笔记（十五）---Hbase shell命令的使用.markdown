---
layout:     post
title:      Hadoop学习笔记（十五）---Hbase shell命令的使用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>1.启动hbase， 进入hbase文件夹下面的bin文件夹，然后执行命令：</p>

<pre class="prettyprint"><code class=" hljs livecodeserver">hbase <span class="hljs-built_in">shell</span></code></pre>



<pre class="prettyprint"><code class=" hljs vhdl">[root@hadoop0 bin]# hbase shell
HBase Shell; enter <span class="hljs-attribute">'help</span>&lt;<span class="hljs-keyword">RETURN</span>&gt;' <span class="hljs-keyword">for</span> list <span class="hljs-keyword">of</span> supported commands.
<span class="hljs-keyword">Type</span> <span class="hljs-string">"exit&lt;RETURN&gt;"</span> <span class="hljs-keyword">to</span> leave the HBase Shell
Version <span class="hljs-number">0.98</span><span class="hljs-number">.12</span><span class="hljs-number">.1</span>-hadoop1, rb00ec5da604d64a0bdc7d92452b1e0559f0f5d73, Sun May <span class="hljs-number">17</span> <span class="hljs-number">12</span>:<span class="hljs-number">22</span>:<span class="hljs-number">29</span> PDT <span class="hljs-number">2015</span>

hbase(main):<span class="hljs-number">001</span>:<span class="hljs-number">0</span>&gt; </code></pre>

<p>2。创建表：</p>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">create</span> <span class="hljs-string">'users'</span>,<span class="hljs-string">'user_id'</span>, <span class="hljs-string">'address'</span>, <span class="hljs-string">'info'</span></span></code></pre>

<p>表users,三个列簇：user_id, address, info</p>

<p>3.查看所有表：<code>list</code></p>

<pre class="prettyprint"><code class=" hljs css"><span class="hljs-tag">hbase</span>(<span class="hljs-tag">main</span>)<span class="hljs-pseudo">:003</span><span class="hljs-pseudo">:0</span>&gt; <span class="hljs-tag">list</span>
<span class="hljs-tag">TABLE</span>                                                                                                                                                                       
<span class="hljs-tag">users</span>                                                                                                                                                                       
1 <span class="hljs-tag">row</span>(<span class="hljs-tag">s</span>) <span class="hljs-tag">in</span> 0<span class="hljs-class">.0400</span> <span class="hljs-tag">seconds</span></code></pre>

<p>4.查看表结构：<code>describe 'users'</code></p>

<pre class="prettyprint"><code class=" hljs coffeescript">hbase(main):<span class="hljs-number">004</span>:<span class="hljs-number">0</span>&gt; describe <span class="hljs-string">'users'</span>
Table users <span class="hljs-keyword">is</span> ENABLED                                                                                                                                                      
users                                                                                                                                                                       
COLUMN FAMILIES DESCRIPTION                                                                                                                                                 
{NAME<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'address'</span>, DATA_BLOCK_ENCODING<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, BLOOMFILTER<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'ROW'</span>, REPLICATION_SCOPE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'1'</span>, COMPRESSION<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, MIN_VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, TTL<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FORE
VER'</span>, KEEP_DELETED_CELLS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FALSE'</span>, BLOCKSIZE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'65536'</span>, IN_MEMORY<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'false'</span>, BLOCKCACHE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'true'</span>}                                                                      
{NAME<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'info'</span>, DATA_BLOCK_ENCODING<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, BLOOMFILTER<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'ROW'</span>, REPLICATION_SCOPE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'1'</span>, COMPRESSION<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, MIN_VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, TTL<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FOREVER
'</span>, KEEP_DELETED_CELLS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FALSE'</span>, BLOCKSIZE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'65536'</span>, IN_MEMORY<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'false'</span>, BLOCKCACHE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'true'</span>}                                                                         
{NAME<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'user_id'</span>, DATA_BLOCK_ENCODING<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, BLOOMFILTER<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'ROW'</span>, REPLICATION_SCOPE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'1'</span>, COMPRESSION<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, MIN_VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, TTL<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FORE
VER'</span>, KEEP_DELETED_CELLS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FALSE'</span>, BLOCKSIZE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'65536'</span>, IN_MEMORY<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'false'</span>, BLOCKCACHE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'true'</span>}                                                                      
<span class="hljs-number">3</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.1000</span> seconds</code></pre>

<p>5.删除表：</p>

<p>表不能直接删除，需要先进行disable操作：</p>

<p>如果直接删除会报下面的错误：</p>

<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">007</span>:<span class="hljs-number">0</span>&gt; drop <span class="hljs-string">'user_tmp'</span>

ERROR: Table user_tmp is enabled. Disable <span class="hljs-keyword">it</span> <span class="hljs-keyword">first</span>.

Here is some help <span class="hljs-keyword">for</span> this <span class="hljs-command"><span class="hljs-keyword">command</span>:</span>
Drop <span class="hljs-operator">the</span> named table. Table must <span class="hljs-keyword">first</span> be disabled:
  hbase&gt; drop <span class="hljs-string">'t1'</span>
  hbase&gt; drop <span class="hljs-string">'ns1:t1'</span></code></pre>

<p>应该先disable：</p>

<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">008</span>:<span class="hljs-number">0</span>&gt; disable <span class="hljs-string">'user_tmp'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">1.4120</span> <span class="hljs-built_in">seconds</span></code></pre>

<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">010</span>:<span class="hljs-number">0</span>&gt; drop <span class="hljs-string">'user_tmp'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.2320</span> <span class="hljs-built_in">seconds</span></code></pre>

<p>6.查看表是否存在：</p>

<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">013</span>:<span class="hljs-number">0</span>&gt; exists <span class="hljs-string">'users'</span>
Table users does exist                                                                                                                                                      
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0330</span> <span class="hljs-built_in">seconds</span></code></pre>

<p>7.查看是否禁止：</p>

<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">016</span>:<span class="hljs-number">0</span>&gt; is_enabled <span class="hljs-string">'users'</span>
<span class="hljs-constant">true</span>                                                                                                                                                                        
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0410</span> <span class="hljs-built_in">seconds</span></code></pre>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">002</span>:<span class="hljs-number">0</span>&gt; is_disabled <span class="hljs-string">'users'</span>
<span class="hljs-constant">false</span>                                                                                                                                                                       
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0270</span> <span class="hljs-built_in">seconds</span></code></pre>

<p>7.插入数据：</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">001</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'users'</span>, <span class="hljs-string">'xiaoming'</span>,<span class="hljs-string">'info:company'</span>,<span class="hljs-string">'baidu'</span></code></pre>

<p>8.查看表中的数据：</p>

<pre class="prettyprint"><code class=" hljs bash">scan <span class="hljs-string">'users'</span></code></pre>

<p>9.获得小明的信息：</p>

<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">003</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'users'</span>,<span class="hljs-string">'xiaoming'</span>
COLUMN                                       CELL                                                                                                                           
 info:age                                    timestamp=<span class="hljs-number">1433712506712</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">22</span>                                                                                              
 info:company                                timestamp=<span class="hljs-number">1433712630400</span>, <span class="hljs-built_in">value</span>=baidu                                                                                           
<span class="hljs-number">2</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0560</span> <span class="hljs-built_in">seconds</span></code></pre>

<p>只查看年龄：</p>

<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">004</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'users'</span>,<span class="hljs-string">'xiaoming'</span>,<span class="hljs-string">'info:age'</span>
COLUMN                                       CELL                                                                                                                           
 info:age                                    timestamp=<span class="hljs-number">1433712506712</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">22</span>                                                                                              
<span class="hljs-number">1</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0280</span> <span class="hljs-built_in">seconds</span></code></pre>

<p>10.更新记录：</p>

<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">007</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'users'</span>,<span class="hljs-string">'xiaoming'</span>,<span class="hljs-string">'info:age'</span>,<span class="hljs-string">'24'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0140</span> <span class="hljs-built_in">seconds</span></code></pre>

<p>现在查看年龄就是最新的年龄：</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">009</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'users'</span>,<span class="hljs-string">'xiaoming'</span>,<span class="hljs-string">'info:age'</span>
COLUMN                                       CELL                                                                                                                           
 info:age                                    timestamp=<span class="hljs-number">1433713054227</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">24</span>                                                                                              
<span class="hljs-number">1</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0140</span> <span class="hljs-built_in">seconds</span></code></pre>

<p>怎么查看历史记录呢？：</p>

<p>查看上一个数据：</p>



<pre class="prettyprint"><code class=" hljs php">hbase(main):<span class="hljs-number">011</span>:<span class="hljs-number">0</span>&gt; get <span class="hljs-string">'users'</span>,<span class="hljs-string">'xiaoming'</span>,{COLUMN=&gt;<span class="hljs-string">'info:age'</span>,VERSIONS=&gt;<span class="hljs-number">2</span>}</code></pre>

<p>显示时间戳数据：</p>



<pre class="prettyprint"><code class=" hljs php">hbase(main):<span class="hljs-number">016</span>:<span class="hljs-number">1</span>&gt; get <span class="hljs-string">'users'</span>,<span class="hljs-string">'xiaoming'</span>,{COLUMN=&gt;<span class="hljs-string">'info:age'</span>,TIMESTAMP=&gt;<span class="hljs-number">1433713054227</span>}</code></pre>

<p>删除数据：</p>



<pre class="prettyprint"><code class=" hljs javascript"><span class="hljs-keyword">delete</span> <span class="hljs-string">'users'</span>,<span class="hljs-string">'xiaoming'</span>,<span class="hljs-string">'info:age'</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>