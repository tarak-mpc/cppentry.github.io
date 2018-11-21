---
layout:     post
title:      HBase 相关API操练(一)：Shell操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，欢迎大家转载交流，但必须在博文明显位置标明转载地址。否则维权必究！					https://blog.csdn.net/py_123456/article/details/80570056				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>HBase 为用户提供了一个非常方便的使用方式, 我们称之为“HBase Shell”。</p>

<p>HBase Shell 提供了大多数的 HBase 命令, 通过 HBase Shell 用户可以方便地创建、删除及修改表, 还可以向表中添加数据、列出表中的相关信息等。</p>

<p><em>备注:写错 HBase Shell 命令时用键盘上的“Delete”进行删除,“Backspace”不起作用。</em></p>

<p>在启动 HBase 之后,用户可以通过下面的命令进入 HBase Shell 之中,命令如下所示:</p>



<pre class="prettyprint"><code class=" hljs vhdl">[hadoop@master hbase]$ bin/hbase shell
<span class="hljs-number">2018</span>-<span class="hljs-number">06</span>-<span class="hljs-number">04</span> <span class="hljs-number">16</span>:<span class="hljs-number">35</span>:<span class="hljs-number">46</span>,<span class="hljs-number">762</span> INFO  [main] <span class="hljs-keyword">Configuration</span>.deprecation: hadoop.native.lib <span class="hljs-keyword">is</span> deprecated. Instead, <span class="hljs-keyword">use</span> io.native.lib.available
HBase Shell; enter <span class="hljs-attribute">'help</span>&lt;<span class="hljs-keyword">RETURN</span>&gt;' <span class="hljs-keyword">for</span> list <span class="hljs-keyword">of</span> supported commands.
<span class="hljs-keyword">Type</span> <span class="hljs-string">"exit&lt;RETURN&gt;"</span> <span class="hljs-keyword">to</span> leave the HBase Shell
Version <span class="hljs-number">0.98</span><span class="hljs-number">.19</span>-hadoop2, r1e527e73bc539a04ba0fa4ed3c0a82c7e9dd7d15, Fri Apr <span class="hljs-number">22</span> <span class="hljs-number">19</span>:<span class="hljs-number">07</span>:<span class="hljs-number">24</span> PDT <span class="hljs-number">2016</span>

hbase(main):<span class="hljs-number">001</span>:<span class="hljs-number">0</span>&gt; </code></pre>

<p>输入 help 可以看到命令分组。</p>

<p>Group Name  Commands <br>
general status,version <br>
ddl alter,create,describe,disable,drop,enable,exists,is_disable,is_enable,list <br>
dml count,delete,deleteall,get,get_counter,incr,put,scan,truncate <br>
tools   assign,balance_switch,balancer,close_region,compact,flush,major_compact,move,split,unassign,zk_dump <br>
replication add_peer,disable_peer,enable_peer,remove_peer,start_replication,stop_replication <br>
部分命令清单。</p>

<p><img src="https://img-blog.csdn.net/20180604171026180?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3B5XzEyMzQ1Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
        下边分组举例 Shell 的各种操作。</p>



<h2 id="general操作">general操作</h2>

<p>查询 HBase 服务器状态 status。</p>



<pre class="prettyprint"><code class=" hljs avrasm">hbase(main):<span class="hljs-number">002</span>:<span class="hljs-number">0</span>&gt; status
<span class="hljs-number">2018</span>-<span class="hljs-number">06</span>-<span class="hljs-number">04</span> <span class="hljs-number">15</span>:<span class="hljs-number">49</span>:<span class="hljs-number">42</span>,<span class="hljs-number">011</span> WARN  [main] util<span class="hljs-preprocessor">.NativeCodeLoader</span>: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
<span class="hljs-label">SLF4J:</span> Class path contains multiple SLF4J bindings.
<span class="hljs-label">SLF4J:</span> Found binding <span class="hljs-keyword">in</span> [jar:file:/home/hadoop/app/hbase-<span class="hljs-number">0.98</span><span class="hljs-number">.19</span>/lib/phoenix-<span class="hljs-number">4.8</span><span class="hljs-number">.2</span>-HBase-<span class="hljs-number">0.98</span>-client<span class="hljs-preprocessor">.jar</span>!/org/slf4j/impl/StaticLoggerBinder<span class="hljs-preprocessor">.class</span>]
<span class="hljs-label">SLF4J:</span> Found binding <span class="hljs-keyword">in</span> [jar:file:/home/hadoop/app/hbase-<span class="hljs-number">0.98</span><span class="hljs-number">.19</span>/lib/slf4j-log4j12-<span class="hljs-number">1.6</span><span class="hljs-number">.4</span><span class="hljs-preprocessor">.jar</span>!/org/slf4j/impl/StaticLoggerBinder<span class="hljs-preprocessor">.class</span>]
<span class="hljs-label">SLF4J:</span> Found binding <span class="hljs-keyword">in</span> [jar:file:/home/hadoop/app/hadoop-<span class="hljs-number">2.6</span><span class="hljs-number">.0</span>/share/hadoop/common/lib/slf4j-log4j12-<span class="hljs-number">1.7</span><span class="hljs-number">.5</span><span class="hljs-preprocessor">.jar</span>!/org/slf4j/impl/StaticLoggerBinder<span class="hljs-preprocessor">.class</span>]
<span class="hljs-label">SLF4J:</span> See http://www<span class="hljs-preprocessor">.slf</span>4j<span class="hljs-preprocessor">.org</span>/codes<span class="hljs-preprocessor">.html</span><span class="hljs-preprocessor">#multiple_bindings for an explanation.</span>
<span class="hljs-number">1</span> active master, <span class="hljs-number">0</span> backup masters, <span class="hljs-number">1</span> servers, <span class="hljs-number">0</span> dead, <span class="hljs-number">7.0000</span> average load</code></pre>

<p>查询hbase版本 version</p>



<pre class="prettyprint"><code class=" hljs css"><span class="hljs-tag">hbase</span>(<span class="hljs-tag">main</span>)<span class="hljs-pseudo">:003</span><span class="hljs-pseudo">:0</span>&gt; <span class="hljs-tag">version</span>
0<span class="hljs-class">.98</span><span class="hljs-class">.19-hadoop2</span>, <span class="hljs-tag">r1e527e73bc539a04ba0fa4ed3c0a82c7e9dd7d15</span>, <span class="hljs-tag">Fri</span> <span class="hljs-tag">Apr</span> 22 19<span class="hljs-pseudo">:07</span><span class="hljs-pseudo">:24</span> <span class="hljs-tag">PDT</span> 2016</code></pre>



<h2 id="ddl操作">ddl操作</h2>

<p><strong>1、 创建一个表</strong></p>



<pre class="prettyprint"><code class=" hljs coffeescript">hbase(main):<span class="hljs-number">004</span>:<span class="hljs-number">0</span>&gt; create <span class="hljs-string">'member'</span>,<span class="hljs-string">'memeber_id'</span>,<span class="hljs-string">'address'</span>,<span class="hljs-string">'info'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.5980</span> seconds<span class="hljs-function">

=&gt;</span> <span class="hljs-attribute">Hbase</span>::Table – member </code></pre>

<p><strong>2、 列出所有的表</strong></p>



<pre class="prettyprint"><code class=" hljs avrasm">hbase(main):<span class="hljs-number">005</span>:<span class="hljs-number">0</span>&gt; list
TABLE                                                                                                                                                        
SYSTEM<span class="hljs-preprocessor">.CATALOG</span>                                                                                                                                               
SYSTEM<span class="hljs-preprocessor">.FUNCTION</span>                                                                                                                                              
SYSTEM<span class="hljs-preprocessor">.SEQUENCE</span>                                                                                                                                              
SYSTEM<span class="hljs-preprocessor">.STATS</span>                                                                                                                                                 
member                                                                                                                                                       
tvcount                                                                                                                                                      
<span class="hljs-number">6</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0290</span> seconds

=&gt; [<span class="hljs-string">"SYSTEM.CATALOG"</span>, <span class="hljs-string">"SYSTEM.FUNCTION"</span>, <span class="hljs-string">"SYSTEM.SEQUENCE"</span>, <span class="hljs-string">"SYSTEM.STATS"</span>, <span class="hljs-string">"member"</span>, <span class="hljs-string">"tvcount"</span>]</code></pre>

<p><strong>3、 获得表的描述</strong></p>



<pre class="prettyprint"><code class=" hljs coffeescript">hbase(main):<span class="hljs-number">008</span>:<span class="hljs-number">0</span>&gt; describe <span class="hljs-string">'member'</span>
Table member <span class="hljs-keyword">is</span> ENABLED                                                                                                                                      
member                                                                                                                                                       
COLUMN FAMILIES DESCRIPTION                                                                                                                                  
{NAME<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'address'</span>, DATA_BLOCK_ENCODING<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, BLOOMFILTER<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'ROW'</span>, REPLICATION_SCOPE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'1'</span>, COMPRESSION<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, MIN_VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0
'</span>, TTL<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FOREVER'</span>, KEEP_DELETED_CELLS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FALSE'</span>, BLOCKSIZE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'65536'</span>, IN_MEMORY<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'false'</span>, BLOCKCACHE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'true'</span>}                                        
{NAME<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'info'</span>, DATA_BLOCK_ENCODING<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, BLOOMFILTER<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'ROW'</span>, REPLICATION_SCOPE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'1'</span>, COMPRESSION<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, MIN_VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, 
TTL<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FOREVER'</span>, KEEP_DELETED_CELLS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FALSE'</span>, BLOCKSIZE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'65536'</span>, IN_MEMORY<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'false'</span>, BLOCKCACHE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'true'</span>}                                           
{NAME<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'member_id'</span>, DATA_BLOCK_ENCODING<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, BLOOMFILTER<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'ROW'</span>, REPLICATION_SCOPE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'1'</span>, COMPRESSION<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, MIN_VERSIONS<span class="hljs-function"> =&gt;</span>
 <span class="hljs-string">'0'</span>, TTL<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FOREVER'</span>, KEEP_DELETED_CELLS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FALSE'</span>, BLOCKSIZE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'65536'</span>, IN_MEMORY<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'false'</span>, BLOCKCACHE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'true'</span>}                                     
<span class="hljs-number">3</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0490</span> seconds</code></pre>

<p><strong>4、 删除一个列族： disable -&gt; alter -&gt; enable</strong></p>



<pre class="prettyprint"><code class=" hljs coffeescript">hbase(main):<span class="hljs-number">013</span>:<span class="hljs-number">0</span>&gt; disable <span class="hljs-string">'member'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.6500</span> seconds

hbase(main):<span class="hljs-number">011</span>:<span class="hljs-number">0</span>&gt; alter <span class="hljs-string">'member'</span>,{NAME=&gt;<span class="hljs-string">'member_id'</span>,METHOD=&gt;<span class="hljs-string">'delete'</span>}
Updating all regions <span class="hljs-reserved">with</span> the <span class="hljs-keyword">new</span> schema...
<span class="hljs-number">1</span>/<span class="hljs-number">1</span> regions updated.
Done.
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">1.2610</span> seconds

hbase(main):<span class="hljs-number">012</span>:<span class="hljs-number">0</span>&gt; describe <span class="hljs-string">'member'</span>
Table member <span class="hljs-keyword">is</span> DISABLED                                                                                                                                    
member                                                                                                                                                       
COLUMN FAMILIES DESCRIPTION                                                                                                                                 

{NAME<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'address'</span>, DATA_BLOCK_ENCODING<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, BLOOMFILTER<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'ROW'</span>, REPLICATION_SCOPE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'1'</span>, COMPRESSION<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, MIN_VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0
'</span>, TTL<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FOREVER'</span>, KEEP_DELETED_CELLS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FALSE'</span>, BLOCKSIZE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'65536'</span>, IN_MEMORY<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'false'</span>, BLOCKCACHE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'true'</span>}                                       

{NAME<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'info'</span>, DATA_BLOCK_ENCODING<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, BLOOMFILTER<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'ROW'</span>, REPLICATION_SCOPE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'1'</span>, COMPRESSION<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, MIN_VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>,
TTL<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FOREVER'</span>, KEEP_DELETED_CELLS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FALSE'</span>, BLOCKSIZE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'65536'</span>, IN_MEMORY<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'false'</span>, BLOCKCACHE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'true'</span>}                                          

<span class="hljs-number">2</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0370</span> seconds

hbase(main):<span class="hljs-number">013</span>:<span class="hljs-number">0</span>&gt; enable <span class="hljs-string">'member'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.6500</span> seconds

hbase(main):<span class="hljs-number">014</span>:<span class="hljs-number">0</span>&gt; describe <span class="hljs-string">'member'</span>
Table member <span class="hljs-keyword">is</span> ENABLED                                                                                                                                      
member                                                                                                                                                       
COLUMN FAMILIES DESCRIPTION                                                                                                                                 

{NAME<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'address'</span>, DATA_BLOCK_ENCODING<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, BLOOMFILTER<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'ROW'</span>, REPLICATION_SCOPE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'1'</span>, COMPRESSION<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, MIN_VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0
'</span>, TTL<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FOREVER'</span>, KEEP_DELETED_CELLS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FALSE'</span>, BLOCKSIZE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'65536'</span>, IN_MEMORY<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'false'</span>, BLOCKCACHE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'true'</span>}                                       

{NAME<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'info'</span>, DATA_BLOCK_ENCODING<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, BLOOMFILTER<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'ROW'</span>, REPLICATION_SCOPE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'1'</span>, COMPRESSION<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, MIN_VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>,
TTL<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FOREVER'</span>, KEEP_DELETED_CELLS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FALSE'</span>, BLOCKSIZE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'65536'</span>, IN_MEMORY<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'false'</span>, BLOCKCACHE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'true'</span>}                                          

<span class="hljs-number">2</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0300</span> seconds
</code></pre>

<p><strong>5、 查看表是否存在</strong></p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">015</span>:<span class="hljs-number">0</span>&gt; exists <span class="hljs-string">'member'</span>
<span class="hljs-constant">true</span>                                                                                                                                                         
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0305</span> <span class="hljs-built_in">seconds</span> </code></pre>

<p><strong>6、 判断表是否为‘enable’和‘disable’</strong></p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">018</span>:<span class="hljs-number">0</span>&gt; is_enabled <span class="hljs-string">'member'</span>

 <span class="hljs-constant">true</span>                                                                                                                                              

 <span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0200</span> <span class="hljs-built_in">seconds</span>

 hbase(main):<span class="hljs-number">021</span>:<span class="hljs-number">0</span>&gt; is_disabled <span class="hljs-string">'member'</span>

 <span class="hljs-constant">false</span>                                                                                                                                               <span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0210</span> <span class="hljs-built_in">seconds</span>
</code></pre>

<p><strong>7、 删除一个表</strong></p>



<pre class="prettyprint"><code class=" hljs vhdl">hbase(main):<span class="hljs-number">013</span>:<span class="hljs-number">0</span>&gt; disable <span class="hljs-attribute">'member</span>'
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.6500</span> seconds

hbase(main):<span class="hljs-number">026</span>:<span class="hljs-number">0</span>&gt; drop <span class="hljs-attribute">'member</span>'
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.2570</span> seconds

hbase(main):<span class="hljs-number">027</span>:<span class="hljs-number">0</span>&gt; list
TABLE                                                                                                                                                        
SYSTEM.CATALOG                                                                                                                                               
SYSTEM.<span class="hljs-keyword">FUNCTION</span>                                                                                                                                              
SYSTEM.<span class="hljs-keyword">SEQUENCE</span>                                                                                                                                              
SYSTEM.STATS                                                                                                                                                 
tvcount                                                                                                                                                      
<span class="hljs-number">5</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0060</span> seconds

=&gt; [<span class="hljs-string">"SYSTEM.CATALOG"</span>, <span class="hljs-string">"SYSTEM.FUNCTION"</span>, <span class="hljs-string">"SYSTEM.SEQUENCE"</span>, <span class="hljs-string">"SYSTEM.STATS"</span>, <span class="hljs-string">"tvcount"</span>]</code></pre>



<h2 id="dml操作">dml操作</h2>

<p>首先新建一个表 </p>



<pre class="prettyprint"><code class=" hljs coffeescript">hbase(main):<span class="hljs-number">028</span>:<span class="hljs-number">0</span>&gt; create <span class="hljs-string">'member'</span>,<span class="hljs-string">'address'</span>,<span class="hljs-string">'info'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.1550</span> seconds<span class="hljs-function">

=&gt;</span> <span class="hljs-attribute">Hbase</span>::Table - member
hbase(main):<span class="hljs-number">029</span>:<span class="hljs-number">0</span>&gt; describe <span class="hljs-string">'member'</span>

Table member <span class="hljs-keyword">is</span> ENABLED                                                                                                                                      
member                                                                                                                                                       
COLUMN FAMILIES DESCRIPTION                                                                                                                                  
{NAME<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'address'</span>, DATA_BLOCK_ENCODING<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, BLOOMFILTER<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'ROW'</span>, REPLICATION_SCOPE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'1'</span>, COMPRESSION<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, MIN_VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0
'</span>, TTL<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FOREVER'</span>, KEEP_DELETED_CELLS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FALSE'</span>, BLOCKSIZE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'65536'</span>, IN_MEMORY<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'false'</span>, BLOCKCACHE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'true'</span>}                                        
{NAME<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'info'</span>, DATA_BLOCK_ENCODING<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, BLOOMFILTER<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'ROW'</span>, REPLICATION_SCOPE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'1'</span>, COMPRESSION<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, MIN_VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, 
TTL<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FOREVER'</span>, KEEP_DELETED_CELLS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FALSE'</span>, BLOCKSIZE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'65536'</span>, IN_MEMORY<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'false'</span>, BLOCKCACHE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'true'</span>}                                           
<span class="hljs-number">2</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0370</span> seconds</code></pre>

<p><strong>1、 插入几条记录</strong></p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">030</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'member'</span>,<span class="hljs-string">'zimo'</span>,<span class="hljs-string">'info:age'</span>,<span class="hljs-string">'28'</span> 
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0750</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">031</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'member'</span>,<span class="hljs-string">'zimo'</span>,<span class="hljs-string">'info:birthday'</span>,<span class="hljs-string">'1994-07-17'</span> 
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0080</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">032</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'member'</span>,<span class="hljs-string">'zimo'</span>,<span class="hljs-string">'info:company'</span>,<span class="hljs-string">'luoji'</span> 
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0060</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">033</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'member'</span>,<span class="hljs-string">'zimo'</span>,<span class="hljs-string">'address:country'</span>,<span class="hljs-string">'china'</span> 
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0100</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">034</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'member'</span>,<span class="hljs-string">'zimo'</span>,<span class="hljs-string">'address:province'</span>,<span class="hljs-string">'beijing'</span> 
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0060</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">035</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'member'</span>,<span class="hljs-string">'zimo'</span>,<span class="hljs-string">'address:city'</span>,<span class="hljs-string">'beijing'</span> 
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0050</span> <span class="hljs-built_in">seconds</span></code></pre>

<p>put命令比较简单，只有这一种用法：</p>

<p>hbase&gt; put ‘t1′, ‘r1′, ‘c1′, ‘value’, ts1  <br>
t1指表名，r1指行键名，c1指列名，value指单元格值。ts1指时间戳，一般都省略掉了。</p>

<p><strong>2、 全表扫描 scan</strong></p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">036</span>:<span class="hljs-number">0</span>&gt; scan <span class="hljs-string">'member'</span>
ROW                                      COLUMN+CELL                                                                                                         
 zimo                                    column=address:city, timestamp=<span class="hljs-number">1528100333405</span>, <span class="hljs-built_in">value</span>=beijing                                                         
 zimo                                    column=address:country, timestamp=<span class="hljs-number">1528100280333</span>, <span class="hljs-built_in">value</span>=china                                                        
 zimo                                    column=address:province, timestamp=<span class="hljs-number">1528100323616</span>, <span class="hljs-built_in">value</span>=beijing                                                     
 zimo                                    column=info:age, timestamp=<span class="hljs-number">1528100120269</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">28</span>                                                                  
 zimo                                    column=info:birthday, timestamp=<span class="hljs-number">1528100169722</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">1994</span>-<span class="hljs-number">07</span>-<span class="hljs-number">17</span>                                                     
 zimo                                    column=info:company, timestamp=<span class="hljs-number">1528100217095</span>, <span class="hljs-built_in">value</span>=luoji                                                           
<span class="hljs-number">1</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0510</span> <span class="hljs-built_in">seconds</span>
</code></pre>

<p><strong>3、 获得数据 get</strong></p>

<pre><code>    1) 获得一行的所有数据
</code></pre>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">037</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'member'</span>,<span class="hljs-string">'zimo'</span> 
COLUMN                                   CELL                                                                                                                
 address:city                            timestamp=<span class="hljs-number">1528100333405</span>, <span class="hljs-built_in">value</span>=beijing                                                                              
 address:country                         timestamp=<span class="hljs-number">1528100280333</span>, <span class="hljs-built_in">value</span>=china                                                                                
 address:province                        timestamp=<span class="hljs-number">1528100323616</span>, <span class="hljs-built_in">value</span>=beijing                                                                              
 info:age                                timestamp=<span class="hljs-number">1528100120269</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">28</span>                                                                                   
 info:birthday                           timestamp=<span class="hljs-number">1528100169722</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">1994</span>-<span class="hljs-number">07</span>-<span class="hljs-number">17</span>                                                                           
 info:company                            timestamp=<span class="hljs-number">1528100217095</span>, <span class="hljs-built_in">value</span>=luoji                                                                                
<span class="hljs-number">6</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0160</span> <span class="hljs-built_in">seconds</span></code></pre>

<pre><code>    2) 获得某行，某列族的所有数据
</code></pre>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">038</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'member'</span>,<span class="hljs-string">'zimo'</span>,<span class="hljs-string">'info'</span>
COLUMN                                   CELL                                                                                                                
 info:age                                timestamp=<span class="hljs-number">1528100120269</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">28</span>                                                                                   
 info:birthday                           timestamp=<span class="hljs-number">1528100169722</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">1994</span>-<span class="hljs-number">07</span>-<span class="hljs-number">17</span>                                                                           
 info:company                            timestamp=<span class="hljs-number">1528100217095</span>, <span class="hljs-built_in">value</span>=luoji                                                                                
<span class="hljs-number">3</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0130</span> <span class="hljs-built_in">seconds</span></code></pre>

<pre><code>    3) 获得某行，某列族，某列的所有数据
</code></pre>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">039</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'member'</span>,<span class="hljs-string">'zimo'</span>,<span class="hljs-string">'info:company'</span>
COLUMN                                   CELL                                                                                                                
 info:company                            timestamp=<span class="hljs-number">1528100217095</span>, <span class="hljs-built_in">value</span>=luoji                                                                                
<span class="hljs-number">1</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0100</span> <span class="hljs-built_in">seconds</span></code></pre>

<p><strong>4、 更新一条记录 put（把scutshuxue年龄改为99）</strong></p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">040</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'member'</span>,<span class="hljs-string">'zimo'</span>,<span class="hljs-string">'info:age'</span>,<span class="hljs-string">'24'</span> 
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0070</span> <span class="hljs-built_in">seconds</span> </code></pre>

<p><strong>5、 删除 delete、 deleteall</strong></p>

<pre><code>    1) 删除行'zimo', 列族为‘info' 中age的值
</code></pre>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">042</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">delete</span> <span class="hljs-string">'member'</span>,<span class="hljs-string">'zimo'</span>,<span class="hljs-string">'info:age'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0280</span> <span class="hljs-built_in">seconds</span> 
hbase(main):<span class="hljs-number">043</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'member'</span>,<span class="hljs-string">'zimo'</span>

COLUMN                                   CELL                                                                                                               

 address:city                            timestamp=<span class="hljs-number">1528100333405</span>, <span class="hljs-built_in">value</span>=beijing                                                                              
 address:country                         timestamp=<span class="hljs-number">1528100280333</span>, <span class="hljs-built_in">value</span>=china                                                                               
 address:province                        timestamp=<span class="hljs-number">1528100323616</span>, <span class="hljs-built_in">value</span>=beijing                                                                             
 info:birthday                           timestamp=<span class="hljs-number">1528100169722</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">1994</span>-<span class="hljs-number">07</span>-<span class="hljs-number">17</span>                                                                          
 info:company                            timestamp=<span class="hljs-number">1528100217095</span>, <span class="hljs-built_in">value</span>=luoji                                                                                

<span class="hljs-number">5</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0180</span> <span class="hljs-built_in">seconds</span></code></pre>

<pre><code>    2) 删除整行
</code></pre>



<pre class="prettyprint"><code class=" hljs bash">deleteall <span class="hljs-string">'member'</span>, <span class="hljs-string">'zimo'</span></code></pre>

<p><strong>6、 查询表中有多少行</strong></p>



<pre class="prettyprint"><code class=" hljs coffeescript">hbase(main):<span class="hljs-number">044</span>:<span class="hljs-number">0</span>&gt; count <span class="hljs-string">'member'</span>
<span class="hljs-number">1</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0190</span> seconds<span class="hljs-function">

=&gt;</span> <span class="hljs-number">1</span></code></pre>

<p><strong>7、 将整个表清空</strong></p>



<pre class="prettyprint"><code class=" hljs lua">hbase(main):<span class="hljs-number">046</span>:<span class="hljs-number">0</span>&gt; truncate <span class="hljs-string">'member'</span>
Truncating <span class="hljs-string">'member'</span> <span class="hljs-built_in">table</span> (it may take a <span class="hljs-keyword">while</span>):
 - Disabling <span class="hljs-built_in">table</span>...
 - Truncating <span class="hljs-built_in">table</span>...
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">1.5320</span> seconds</code></pre>

<p>可以看出，HBase 是通过先对表执行 disable，然后再执行 drop 操作后重建表来实现 truncate 的功能的。</p>

<p><strong>以上就是博主为大家介绍的这一板块的主要内容，这都是博主自己的学习过程，希望能给大家带来一定的指导作用，有用的还望大家点个支持，如果对你没用也望包涵，有错误烦请指出。如有期待可关注博主以第一时间获取更新哦，谢谢！</strong>　</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>