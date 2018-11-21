---
layout:     post
title:      HBase（二）hbase shell常用语法
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u013963380/article/details/74936645				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>HBase Shell是HBase的一个命令行工具，我们可以通过它对HBase表进行维护操作。我们可以使用命令hbase shell来进入HBase Shell。</p>

<p><strong>1.基本操作</strong></p>

<p>在HBase shell中，可以使用status, version和whoami分别获得当前服务的状态、版本、登录用户和验证方式。</p>



<pre class="prettyprint"><code class=" hljs css"><span class="hljs-tag">hbase</span>(<span class="hljs-tag">main</span>)<span class="hljs-pseudo">:001</span><span class="hljs-pseudo">:0</span>&gt; <span class="hljs-tag">status</span>
1 <span class="hljs-tag">active</span> <span class="hljs-tag">master</span>, 0 <span class="hljs-tag">backup</span> <span class="hljs-tag">masters</span>, 3 <span class="hljs-tag">servers</span>, 0 <span class="hljs-tag">dead</span>, 1<span class="hljs-class">.6667</span> <span class="hljs-tag">average</span> <span class="hljs-tag">load</span>

<span class="hljs-tag">hbase</span>(<span class="hljs-tag">main</span>)<span class="hljs-pseudo">:002</span><span class="hljs-pseudo">:0</span>&gt; <span class="hljs-tag">version</span>
1<span class="hljs-class">.2</span><span class="hljs-class">.0-cdh5</span><span class="hljs-class">.9</span><span class="hljs-class">.2</span>, <span class="hljs-tag">rUnknown</span>, <span class="hljs-tag">Tue</span> <span class="hljs-tag">Apr</span>  4 01<span class="hljs-pseudo">:53</span><span class="hljs-pseudo">:51</span> <span class="hljs-tag">PDT</span> 2017

<span class="hljs-tag">hbase</span>(<span class="hljs-tag">main</span>)<span class="hljs-pseudo">:003</span><span class="hljs-pseudo">:0</span>&gt; <span class="hljs-tag">whoami</span>
<span class="hljs-tag">root</span> (<span class="hljs-tag">auth</span><span class="hljs-pseudo">:SIMPLE)</span>
    <span class="hljs-tag">groups</span>: <span class="hljs-tag">root</span>
</code></pre>

<p>HBase shell中的帮助命令非常强大，使用help获得全部命令的列表，使用help ‘command_name’获得某一个命令的详细信息。 例如：</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">004</span>:<span class="hljs-number">0</span>&gt; help <span class="hljs-string">'create'</span>
Creates <span class="hljs-operator">a</span> table. Pass <span class="hljs-operator">a</span> table name, <span class="hljs-operator">and</span> <span class="hljs-operator">a</span> <span class="hljs-built_in">set</span> <span class="hljs-operator">of</span> column family
specifications (<span class="hljs-keyword">at</span> least <span class="hljs-constant">one</span>), <span class="hljs-operator">and</span>, optionally, table configuration.
Column specification can be <span class="hljs-operator">a</span> simple <span class="hljs-keyword">string</span> (name), <span class="hljs-operator">or</span> <span class="hljs-operator">a</span> dictionary
(dictionaries are described below <span class="hljs-operator">in</span> main help output), necessarily 
including NAME attribute. 
Examples:

Create <span class="hljs-operator">a</span> table <span class="hljs-operator">with</span> namespace=ns1 <span class="hljs-operator">and</span> table qualifier=t1
  hbase&gt; <span class="hljs-built_in">create</span> <span class="hljs-string">'ns1:t1'</span>, {NAME =&gt; <span class="hljs-string">'f1'</span>, VERSIONS =&gt; <span class="hljs-number">5</span>}

Create <span class="hljs-operator">a</span> table <span class="hljs-operator">with</span> namespace=default <span class="hljs-operator">and</span> table qualifier=t1
  hbase&gt; <span class="hljs-built_in">create</span> <span class="hljs-string">'t1'</span>, {NAME =&gt; <span class="hljs-string">'f1'</span>}, {NAME =&gt; <span class="hljs-string">'f2'</span>}, {NAME =&gt; <span class="hljs-string">'f3'</span>}
  hbase&gt; <span class="hljs-comment"># The above in shorthand would be the following:</span>
  hbase&gt; <span class="hljs-built_in">create</span> <span class="hljs-string">'t1'</span>, <span class="hljs-string">'f1'</span>, <span class="hljs-string">'f2'</span>, <span class="hljs-string">'f3'</span>
  hbase&gt; <span class="hljs-built_in">create</span> <span class="hljs-string">'t1'</span>, {NAME =&gt; <span class="hljs-string">'f1'</span>, VERSIONS =&gt; <span class="hljs-number">1</span>, TTL =&gt; <span class="hljs-number">2592000</span>, BLOCKCACHE =&gt; <span class="hljs-constant">true</span>}
  hbase&gt; <span class="hljs-built_in">create</span> <span class="hljs-string">'t1'</span>, {NAME =&gt; <span class="hljs-string">'f1'</span>, CONFIGURATION =&gt; {<span class="hljs-string">'hbase.hstore.blockingStoreFiles'</span> =&gt; <span class="hljs-string">'10'</span>}}
</code></pre>

<p><strong>2.命名空间</strong></p>

<p>在HBase系统中，命名空间namespace指的是一个HBase表的逻辑分组，同一个命名空间中的表有类似的用途，也用于配额和权限等设置进行安全管控。你也可以理解在关系型数据库中的一个数据库名。 <br>
HBase默认定义了两个系统内置的预定义命名空间： <br>
• hbase：系统命名空间，用于包含hbase的内部表 <br>
• default：所有未指定命名空间的表都自动进入该命名空间 <br>
我们可以通过create_namespace命令来建立命名空间</p>



<pre class="prettyprint"><code class=" hljs scss"><span class="hljs-function">hbase(main)</span><span class="hljs-value">:<span class="hljs-number">005</span>:<span class="hljs-number">0</span>&gt;create_namespace <span class="hljs-string">'xudong'</span></span></code></pre>

<p>通过drop_namespace来删除命名空间</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">008</span>:<span class="hljs-number">0</span>&gt; drop_namespace <span class="hljs-string">'xudong'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0620</span> <span class="hljs-built_in">seconds</span></code></pre>

<p>通过describe_namespace来显示命名空间设定的元信息</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">010</span>:<span class="hljs-number">0</span>&gt; describe_namespace <span class="hljs-string">'xudong'</span>
DESCRIPTION                                                                                                                           
{NAME =&gt; <span class="hljs-string">'xudong'</span>}                                                                                                                    
<span class="hljs-number">1</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0200</span> <span class="hljs-built_in">seconds</span></code></pre>

<p>通过命令list_namespace可以显示所有的命名空间</p>



<pre class="prettyprint"><code class=" hljs haskell"><span class="hljs-title">hbase</span>(main):<span class="hljs-number">011</span>:<span class="hljs-number">0</span>&gt; list_namespace
<span class="hljs-type">NAMESPACE</span>                                                                                                                             
<span class="hljs-default"><span class="hljs-keyword">default</span>                                                                                                                               </span>
<span class="hljs-title">hbase</span>                                                                                                                                 
<span class="hljs-title">xudong</span>                                                                                                                                
<span class="hljs-number">3</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0170</span> seconds

注：如果只是用list命令，则显示的是<span class="hljs-default"><span class="hljs-keyword">default</span>中的所有表<span class="hljs-container">(支持正则表达式)</span></span></code></pre>

<p>默认建表语句create table_name, column_family1 这个命令是将表创建在default下面，如果需要在自己设定的命名空间下建表，则需要用create namespace:table_name命令：</p>



<pre class="prettyprint"><code class=" hljs coffeescript">hbase(main):<span class="hljs-number">013</span>:<span class="hljs-number">0</span>&gt; create <span class="hljs-string">'xudong:userTest'</span>,<span class="hljs-string">'info'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">1.3280</span> seconds<span class="hljs-function">
=&gt;</span> <span class="hljs-attribute">Hbase</span>::Table - <span class="hljs-attribute">xudong</span>:userTest</code></pre>

<p>通过命令list_namespace_tables可以列出某个命名空间下所有的表</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">015</span>:<span class="hljs-number">0</span>&gt; list_namespace_tables <span class="hljs-string">'xudong'</span>
TABLE                                                                                                                                 
userTest                                                                                                                              
<span class="hljs-number">1</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0280</span> <span class="hljs-built_in">seconds</span></code></pre>

<p><strong>3.DDL语句</strong> <br>
创建HBase表，create命令，第一个参数是表名，然后后面是一系列列族的列表。每个列族可以独立指定使用的版本数，数据有效保存时间，是否启动缓存等信息。</p>



<pre class="prettyprint"><code class=" hljs php">hbase(main):<span class="hljs-number">025</span>:<span class="hljs-number">0</span>&gt; create <span class="hljs-string">'test1'</span>,{NAME =&gt; <span class="hljs-string">'f1'</span>,VERSION =&gt; <span class="hljs-number">1</span>},{NAME =&gt; <span class="hljs-string">'f2'</span>,VERSION =&gt; <span class="hljs-number">1</span>}</code></pre>

<p>可以通过describe来查看这个表中的元信息</p>



<pre class="prettyprint"><code class=" hljs coffeescript">hbase(main):<span class="hljs-number">027</span>:<span class="hljs-number">0</span>&gt; describe <span class="hljs-string">'test1'</span>
Table test1 <span class="hljs-keyword">is</span> ENABLED                                                                                                                
test1                                                                                                                                 
COLUMN FAMILIES DESCRIPTION                                                                                                           
{NAME<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'f1'</span>, BLOOMFILTER<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'ROW'</span>, VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'1'</span>, IN_MEMORY<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'false'</span>, KEEP_DELETED_CELLS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FALSE'</span>, DATA_BLOCK_ENCODING<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NON
E'</span>, TTL<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FOREVER'</span>, COMPRESSION<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, MIN_VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, BLOCKCACHE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'true'</span>, BLOCKSIZE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'65536'</span>, REPLICATION_SCOPE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>
}                                                                                                                                     
{NAME<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'f2'</span>, BLOOMFILTER<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'ROW'</span>, VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'1'</span>, IN_MEMORY<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'false'</span>, KEEP_DELETED_CELLS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FALSE'</span>, DATA_BLOCK_ENCODING<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NON
E'</span>, TTL<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FOREVER'</span>, COMPRESSION<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, MIN_VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, BLOCKCACHE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'true'</span>, BLOCKSIZE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'65536'</span>, REPLICATION_SCOPE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>
}                                                                                                                                     
<span class="hljs-number">2</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0460</span> seconds
</code></pre>

<p>还可以通过enable和disable来启用/禁用这个表，相应的可以通过is——enabled和is_disabled来检查表是否禁用。 <br>
使用exists来检查表是否存在。</p>

<p>使用alter来改变表的属性，比如改变列族的属性等。常用的添加和删除一个列族。</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">028</span>:<span class="hljs-number">0</span>&gt; alter <span class="hljs-string">'test1'</span>,<span class="hljs-string">'f3'</span>
Updating all regions <span class="hljs-operator">with</span> <span class="hljs-operator">the</span> <span class="hljs-built_in">new</span> schema...
<span class="hljs-number">1</span>/<span class="hljs-number">1</span> regions updated.
Done.
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">2.0380</span> <span class="hljs-built_in">seconds</span></code></pre>



<pre class="prettyprint"><code class=" hljs coffeescript">hbase(main):<span class="hljs-number">030</span>:<span class="hljs-number">0</span>&gt; alter <span class="hljs-string">'test1'</span>,<span class="hljs-string">'delete'</span><span class="hljs-function"> =&gt;</span> <span class="hljs-string">'f3'</span>
Updating all regions <span class="hljs-reserved">with</span> the <span class="hljs-keyword">new</span> schema...
<span class="hljs-number">1</span>/<span class="hljs-number">1</span> regions updated.
Done.
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">1.9510</span> seconds
也可以使用下面的语句：
alter <span class="hljs-string">'t1'</span>,{ NAME<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'f3'</span>, METHOD<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'delete'</span>}</code></pre>

<p>可以通过describe来检查者两条语句是否运行成功。</p>

<p><strong>4.添加和查询（put &amp; get）</strong> <br>
首先我们创建一个表test2，它有name、baseinfo、extinfo三个列族，并插入一些数据。列族下的列不需要提前创建。</p>



<pre class="prettyprint"><code class=" hljs coffeescript">hbase(main):<span class="hljs-number">031</span>:<span class="hljs-number">0</span>&gt; create <span class="hljs-string">'test2'</span>,<span class="hljs-string">'name'</span>,<span class="hljs-string">'baseinfo'</span>,<span class="hljs-string">'extinfo'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">1.2640</span> seconds<span class="hljs-function">

=&gt;</span> <span class="hljs-attribute">Hbase</span>::Table - test2
hbase(main):<span class="hljs-number">032</span>:<span class="hljs-number">0</span>&gt; put <span class="hljs-string">'test2'</span>,<span class="hljs-string">'xudong'</span>,<span class="hljs-string">'baseinfo:age'</span>,<span class="hljs-string">'20'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.1990</span> seconds

hbase(main):<span class="hljs-number">033</span>:<span class="hljs-number">0</span>&gt; put <span class="hljs-string">'test2'</span>,<span class="hljs-string">'xudong'</span>,<span class="hljs-string">'baseinfo:edu'</span>,<span class="hljs-string">'hfut'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0190</span> seconds

hbase(main):<span class="hljs-number">034</span>:<span class="hljs-number">0</span>&gt; put <span class="hljs-string">'test2'</span>,<span class="hljs-string">'michael'</span>,<span class="hljs-string">'baseinfo:age'</span>,<span class="hljs-string">'26'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0100</span> seconds

hbase(main):<span class="hljs-number">035</span>:<span class="hljs-number">0</span>&gt; put <span class="hljs-string">'test2'</span>,<span class="hljs-string">'michael'</span>,<span class="hljs-string">'baseinfo:career'</span>,<span class="hljs-string">'it'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0160</span> seconds

hbase(main):<span class="hljs-number">036</span>:<span class="hljs-number">0</span>&gt; put <span class="hljs-string">'test2'</span>,<span class="hljs-string">'michael'</span>,<span class="hljs-string">'extinfo:hobby'</span>,<span class="hljs-string">'code'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0100</span> seconds</code></pre>

<p>根据name获取所有的数据</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">037</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'test2'</span>,<span class="hljs-string">'xudong'</span>
COLUMN                             CELL                                                                                               
baseinfo:age                      timestamp=<span class="hljs-number">1499694539376</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">20</span>                                                                  
baseinfo:edu                      timestamp=<span class="hljs-number">1499694574274</span>, <span class="hljs-built_in">value</span>=hfut                                                                
<span class="hljs-number">2</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0320</span> <span class="hljs-built_in">seconds</span></code></pre>

<p>还可以根据name和一个列族获取所有数据</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">038</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'test2'</span>,<span class="hljs-string">'michael'</span>,<span class="hljs-string">'baseinfo'</span>
COLUMN                             CELL                                                                                               
 baseinfo:age                      timestamp=<span class="hljs-number">1499694608816</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">26</span>                                                                  
 baseinfo:career                   timestamp=<span class="hljs-number">1499694672665</span>, <span class="hljs-built_in">value</span>=<span class="hljs-keyword">it</span>                                                                  
<span class="hljs-number">2</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0300</span> <span class="hljs-built_in">seconds</span></code></pre>

<p><strong>5.DML语句</strong></p>

<p>通过delete命令，可以删除name为某个值下的字段（发现整个列族下的baseinfo列的所有字段都删除了，age和career，但是通过scan发现所有的字段还在，只不过get的时候无视掉删除的字段）</p>

<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">005</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">delete</span> <span class="hljs-string">'test2'</span>,<span class="hljs-string">'michael'</span>,<span class="hljs-string">'baseinfo:age'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0660</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">006</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'test2'</span>,<span class="hljs-string">'micahel'</span>,<span class="hljs-string">'baseinfo'</span>
COLUMN                             CELL                                                                                               
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0110</span> <span class="hljs-built_in">seconds</span></code></pre>

<p><strong>6.Scan</strong></p>

<p>通过scan来对全表进行扫描。</p>

<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">010</span>:<span class="hljs-number">0</span>&gt; scan <span class="hljs-string">'test2'</span>
ROW                                COLUMN+CELL                                                                                        
 bye                               column=baseinfo:age, timestamp=<span class="hljs-number">1499778403437</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">29</span>                                             
 michael                           column=baseinfo:career, timestamp=<span class="hljs-number">1499694672665</span>, <span class="hljs-built_in">value</span>=<span class="hljs-keyword">it</span>                                          
 michael                           column=extinfo:hobby, timestamp=<span class="hljs-number">1499694732650</span>, <span class="hljs-built_in">value</span>=code                                          
 xudong                            column=baseinfo:age, timestamp=<span class="hljs-number">1499694539376</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">20</span>                                             
 xudong                            column=baseinfo:edu, timestamp=<span class="hljs-number">1499694574274</span>, <span class="hljs-built_in">value</span>=hfut                                           
<span class="hljs-number">3</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0750</span> <span class="hljs-built_in">seconds</span>
</code></pre>

<p>还可以扫描指定的某个列或者列族</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">012</span>:<span class="hljs-number">0</span>&gt; scan <span class="hljs-string">'test2'</span>,{COLUMNS =&gt; <span class="hljs-string">'baseinfo:age'</span>}
ROW                                COLUMN+CELL                                                                                        
 bye                               column=baseinfo:age, timestamp=<span class="hljs-number">1499778403437</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">29</span>                                             
 xudong                            column=baseinfo:age, timestamp=<span class="hljs-number">1499694539376</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">20</span>                                             
<span class="hljs-number">2</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0190</span> <span class="hljs-built_in">seconds</span>
</code></pre>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">014</span>:<span class="hljs-number">0</span>&gt; scan <span class="hljs-string">'test2'</span>,{COLUMNS =&gt;<span class="hljs-string">'baseinfo'</span>}
ROW                                COLUMN+CELL                                                                                        
 bye                               column=baseinfo:age, timestamp=<span class="hljs-number">1499778403437</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">29</span>                                             
 michael                           column=baseinfo:career, timestamp=<span class="hljs-number">1499694672665</span>, <span class="hljs-built_in">value</span>=<span class="hljs-keyword">it</span>                                          
 xudong                            column=baseinfo:age, timestamp=<span class="hljs-number">1499694539376</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">20</span>                                             
 xudong                            column=baseinfo:edu, timestamp=<span class="hljs-number">1499694574274</span>, <span class="hljs-built_in">value</span>=hfut                                           
<span class="hljs-number">3</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0280</span> <span class="hljs-built_in">seconds</span>
</code></pre>

<p>除了列（COLUMNS）修饰词外，HBase还支持Limit（限制查询结果行数），STARTROW （ROWKEY起始行。会先根据这个key定位到region，再向后扫描）、STOPROW(结束行)、TIMERANGE（限定时间戳范围）、VERSIONS（版本数）、和FILTER（按条件过滤行）等。</p>

<p><strong>7.Filter</strong> <br>
Filter是一个非常强大的修饰词，可以设定一系列条件来进行过滤。比如我们要限制某个列的值等于29</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">016</span>:<span class="hljs-number">0</span>&gt; scan <span class="hljs-string">'test2'</span>,FILTER =&gt; <span class="hljs-string">"ValueFilter(=,'binary:29')"</span>
ROW                                COLUMN+CELL                                                                                        
 bye                               column=baseinfo:age, timestamp=<span class="hljs-number">1499778403437</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">29</span>                                             
<span class="hljs-number">1</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0610</span> <span class="hljs-built_in">seconds</span>
</code></pre>

<p>或者值包含2这个值</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">017</span>:<span class="hljs-number">0</span>&gt; scan <span class="hljs-string">'test2'</span>,FILTER =&gt; <span class="hljs-string">"ValueFilter(=,'substring:2')"</span>
ROW                                COLUMN+CELL                                                                                        
 bye                               column=baseinfo:age, timestamp=<span class="hljs-number">1499778403437</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">29</span>                                             
 xudong                            column=baseinfo:age, timestamp=<span class="hljs-number">1499694539376</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">20</span>                                             
<span class="hljs-number">2</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0260</span> <span class="hljs-built_in">seconds</span>
</code></pre>

<p>FILTER中支持多个过滤条件通过括号、AND和OR的条件组合。有兴趣的可以查看官网api。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>