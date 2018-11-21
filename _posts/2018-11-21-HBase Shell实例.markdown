---
layout:     post
title:      HBase Shell实例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p></p><div class="toc"><div class="toc">
<ul>
<li><a href="#hbase-shell-%E5%B8%B8%E7%94%A8%E6%93%8D%E4%BD%9C" rel="nofollow">HBase Shell 常用操作</a><ul>
<li><a href="#1-%E5%91%BD%E5%90%8D%E7%A9%BA%E9%97%B4" rel="nofollow">1. 命名空间</a></li>
<li><a href="#2-ddl%E8%AF%AD%E5%8F%A5" rel="nofollow">2. DDL语句</a></li>
<li><a href="#3-put%E4%B8%8Eget" rel="nofollow">3. put与get</a></li>
<li><a href="#4-%E5%85%B6%E4%BB%96dml%E8%AF%AD%E5%8F%A5" rel="nofollow">4. 其他DML语句</a></li>
<li><a href="#5-scan%E5%92%8Cfilter" rel="nofollow">5. scan和filter</a></li>
</ul>
</li>
</ul>
</div>
</div>




<h1 id="hbase-shell-常用操作">HBase Shell 常用操作</h1>

<p>HBase Shell是HBase的一个命令行工具，我们可以通过它对HBase进行维护操作。我们可以使用sudo -u hbase hbase shell来进入HBase shell。 <br>
在HBase shell中，可以使用status, version和whoami分别获得当前服务的状态、版本、登录用户和验证方式。</p>



<pre class="prettyprint"><code class="language-sql hljs ">&gt; status
3 servers, 1 dead, 1.3333 average <span class="hljs-operator"><span class="hljs-keyword">load</span>
&gt; version
<span class="hljs-number">0.98</span><span class="hljs-number">.6</span>-cdh5<span class="hljs-number">.3</span><span class="hljs-number">.1</span>, rUnknown, Tue Jan <span class="hljs-number">27</span> <span class="hljs-number">16</span>:<span class="hljs-number">43</span>:<span class="hljs-number">50</span> PST <span class="hljs-number">2015</span>
&gt; whoami
hbase (auth:SIMPLE)
groups: hbase</span></code></pre>

<p>HBase shell中的帮助命令非常强大，使用help获得全部命令的列表，使用help ‘command_name’获得某一个命令的详细信息。 例如：</p>



<pre class="prettyprint"><code class="language-sql hljs ">&gt; help 'list'
List all tables in hbase. Optional regular expression parameter could
be used to filter the output. Examples:
  hbase&gt; list
  hbase&gt; list 'abc.*'
  hbase&gt; list 'ns:abc.*'
  hbase&gt; list 'ns:.*'</code></pre>



<h2 id="1-命名空间">1. 命名空间</h2>

<p>在HBase系统中，命名空间namespace指的是一个HBase表的逻辑分组，同一个命名空间中的表有类似的用途，也用于配额和权限等设置进行安全管控。 <br>
HBase默认定义了两个系统内置的预定义命名空间：</p>

<ul>
<li>hbase：系统命名空间，用于包含hbase的内部表</li>
<li>default：所有未指定命名空间的表都自动进入该命名空间</li>
</ul>

<p>我们可以通过create_namespace命令来建立命名空间</p>



<pre class="prettyprint"><code class="language-sql hljs ">&gt; create_namespace 'debugo_ns'
0 row(s) in 2.0910 seconds</code></pre>

<p>通过drop_namespace来删除命名空间</p>



<pre class="prettyprint"><code class="language-sql hljs ">&gt; drop_namespace 'debugo_ns'
0 row(s) in 1.9540 seconds</code></pre>

<p>通过alter_namespac改变表的属性，其格式如下：</p>



<pre class="prettyprint"><code class="language-sql hljs ">alter_namespace 'my_ns', {METHOD =&gt; '<span class="hljs-operator"><span class="hljs-keyword">set</span><span class="hljs-string">', '</span>PROPERTY_NAME<span class="hljs-string">' =&gt; '</span>PROPERTY_VALUE<span class="hljs-string">'}</span></span></code></pre>

<p>显示命名空间以及设定的元信息：</p>



<pre class="prettyprint"><code class="language-sql hljs ">&gt; describe_namespace 'debugo_ns'
DESCRIPTION                                                            
{NAME =&gt; 'debugo_ns'}                                                  
1 row(s) in 1.9540 seconds</code></pre>

<p>显示所有命名空间</p>



<pre class="prettyprint"><code class="language-sql hljs ">&gt; list_namespace
NAMESPACE                                                              
debugo_ns                                                              
default                                                                
hbase                                                                  
3 row(s) in 0.0910 seconds</code></pre>

<p>在HBase下建表需要使用create table_name, column_family1, 这个命令:</p>



<pre class="prettyprint"><code class="language-sql hljs ">&gt; <span class="hljs-operator"><span class="hljs-keyword">create</span> <span class="hljs-string">'user'</span>,<span class="hljs-string">'info'</span>
<span class="hljs-number">0</span> <span class="hljs-keyword">row</span>(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.9030</span> seconds
=&gt; Hbse::<span class="hljs-keyword">Table</span> - <span class="hljs-keyword">user</span></span></code></pre>

<p>这个时候这个表是创建在default下面。如果需要在debugo_ns这个命名空间下面建表，则需要使用create namespace:table_name这种方式:</p>



<pre class="prettyprint"><code class="language-sql hljs ">&gt; create_namespace 'debugo_ns'
0 row(s) in 2.0910 seconds
<span class="hljs-operator"><span class="hljs-keyword">create</span> <span class="hljs-string">'debugo_ns:users'</span>, <span class="hljs-string">'info'</span>
<span class="hljs-number">0</span> <span class="hljs-keyword">row</span>(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.4640</span> seconds
=&gt; Hbase::<span class="hljs-keyword">Table</span> - debugo_ns:users</span></code></pre>

<p>List命令可以列出当前HBase实例中的所有表，支持使用正则表达式来匹配。</p>



<pre class="prettyprint"><code class="language-sql hljs ">&gt; list_namespace_tables 'debugo_ns'
TABLE                                                                  
users                                                                  
1 row(s) in 0.0400 seconds</code></pre>

<p>使用list_namespace_tables也可以直接输出某个命名空间下的所有表</p>



<pre class="prettyprint"><code class="language-sql hljs ">&gt; list_namespace_tables 'debugo_ns'
TABLE                                                                  
users                                                                  
1 row(s) in 0.0400 seconds</code></pre>



<h2 id="2-ddl语句">2. DDL语句</h2>

<p>首先是建立HBase表，上面我们已经用过create命令了。它后面的第一个参数是表名，然后是一系列列簇的列表。每个列簇中可以独立指定它使用的版本数，数据有效保存时间（TTL），是否开启块缓存等信息。</p>



<pre class="prettyprint"><code class="language-sql hljs ">&gt; <span class="hljs-operator"><span class="hljs-keyword">create</span> <span class="hljs-string">'t1'</span>, {NAME =&gt; <span class="hljs-string">'f1'</span>, VERSIONS =&gt; <span class="hljs-number">1</span>, TTL =&gt; <span class="hljs-number">2592000</span>, BLOCKCACHE =&gt; <span class="hljs-keyword">true</span>}, <span class="hljs-string">'f2'</span></span></code></pre>

<p>表也可以在创建时指定它预分割(pre-splitting)的region数和split方法。在表初始建立时，HBase只分配给这个表一个region。这就意味着当我们访问这个表数据时，我们只会访问一个region server，这样就不能充分利用集群资源。HBase提供了一个工具来管理表的region数，即org.apache.hadoop.hbase.util.RegionSplitter和HBase shell中create中的split的配置项。例如：</p>



<pre class="prettyprint"><code class="language-sql hljs ">&gt; <span class="hljs-operator"><span class="hljs-keyword">create</span> <span class="hljs-string">'t2'</span>, <span class="hljs-string">'f1'</span>, {NUMREGIONS =&gt; <span class="hljs-number">3</span>, SPLITALGO =&gt; <span class="hljs-string">'HexStringSplit'</span>}</span></code></pre>

<p>我们通过describe 来查看这个表中的元信息：</p>



<pre class="prettyprint"><code class=" hljs coffeescript">&gt; describe <span class="hljs-string">'t2'</span>
DESCRIPTION                                                 ENABLED
 <span class="hljs-string">'t2'</span>, {NAME<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'f1'</span>, DATA_BLOCK_ENCODING<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, BLOOMFILTER<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'ROW'</span>, REPLIC <span class="hljs-literal">true</span> ATION_SCOPE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'1'</span>, COMPRESSION<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, MIN_VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, TTL<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FOREVER'</span>, KEEP_DELETED_CELLS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'false'</span>, BLOCKSIZE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'65536'</span>, IN_MEMOR Y<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'false'</span>, BLOCKCACHE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'true'</span>}
<span class="hljs-number">1</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0690</span> seconds</code></pre>

<p>通过enable和disable来启用/禁用这个表,相应的可以通过is_enabled和is_disabled来检查表是否被禁用。</p>



<pre class="prettyprint"><code class="language-sql hljs ">&gt; disable 't2'
0 row(s) in 1.4410 seconds
&gt; enable 't2'
0 row(s) in 0.5940 seconds
&gt; is_enabled 't2'
true
0 row(s) in 0.0400 seconds
hbase(main):042:0&gt; is_disabled 't2'
false
0 row(s) in 0.0490 seconds</code></pre>

<p>使用exists来检查表是否存在</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">&gt; exists <span class="hljs-string">'t2'</span>
Table t2 does exist
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0590</span> <span class="hljs-built_in">seconds</span></code></pre>

<p>使用alter来改变表的属性，比如改变列簇的属性, 这涉及将信息更新到所有的region。在过去的版本中，alter操作需要先把table禁用，而在当前版本已经不需要。</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">&gt; alter <span class="hljs-string">'t1'</span>, {NAME =&gt; <span class="hljs-string">'f1'</span>, VERSIONS =&gt; <span class="hljs-number">5</span>}
Updating all regions <span class="hljs-operator">with</span> <span class="hljs-operator">the</span> <span class="hljs-built_in">new</span> schema...
<span class="hljs-number">0</span>/<span class="hljs-number">1</span> regions updated.
<span class="hljs-number">1</span>/<span class="hljs-number">1</span> regions updated.
Done.
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">2.3470</span> <span class="hljs-built_in">seconds</span></code></pre>

<p>另外一个非常常用的操作是添加和删除列簇：</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">&gt; alter <span class="hljs-string">'t1'</span>,<span class="hljs-string">'f3'</span>
Updating all regions <span class="hljs-operator">with</span> <span class="hljs-operator">the</span> <span class="hljs-built_in">new</span> schema...
<span class="hljs-number">0</span>/<span class="hljs-number">1</span> regions updated.
<span class="hljs-number">1</span>/<span class="hljs-number">1</span> regions updated.
Done.
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">2.3130</span> <span class="hljs-built_in">seconds</span>
&gt; alter <span class="hljs-string">'t1'</span>, <span class="hljs-string">'delete'</span> =&gt; <span class="hljs-string">'f3'</span></code></pre>

<p>或者：</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">&gt; alter <span class="hljs-string">'t1'</span>,{ NAME =&gt; <span class="hljs-string">'f3'</span>, METHOD =&gt; <span class="hljs-string">'delete'</span>}
Updating all regions <span class="hljs-operator">with</span> <span class="hljs-operator">the</span> <span class="hljs-built_in">new</span> schema...
<span class="hljs-number">0</span>/<span class="hljs-number">1</span> regions updated.
<span class="hljs-number">1</span>/<span class="hljs-number">1</span> regions updated.
Done.
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">2.2930</span> <span class="hljs-built_in">seconds</span></code></pre>

<p>删除表需要先将表disable。</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">&gt; disable <span class="hljs-string">'t1'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">1.4310</span> <span class="hljs-built_in">seconds</span>
&gt; drop <span class="hljs-string">'t1'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.2440</span> <span class="hljs-built_in">seconds</span></code></pre>



<h2 id="3-put与get">3. put与get</h2>

<p>在HBase shell中，我们可以通过put命令来插入数据。例如我们新创建一个表，它拥有id、address和info三个列簇，并插入一些数据。列簇下的列不需要提前创建，在需要时通过 <br>
: <br>
来指定即可。</p>



<pre class="prettyprint"><code class=" hljs coffeescript">&gt; create <span class="hljs-string">'member'</span>,<span class="hljs-string">'id'</span>,<span class="hljs-string">'address'</span>,<span class="hljs-string">'info'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.4570</span> seconds<span class="hljs-function">
=&gt;</span> <span class="hljs-attribute">Hbase</span>::Table – member
put <span class="hljs-string">'member'</span>, <span class="hljs-string">'debugo'</span>,<span class="hljs-string">'id'</span>,<span class="hljs-string">'11'</span>
put <span class="hljs-string">'member'</span>, <span class="hljs-string">'debugo'</span>,<span class="hljs-string">'info:age'</span>,<span class="hljs-string">'27'</span>
put <span class="hljs-string">'member'</span>, <span class="hljs-string">'debugo'</span>,<span class="hljs-string">'info:birthday'</span>,<span class="hljs-string">'1987-04-04'</span>
put <span class="hljs-string">'member'</span>, <span class="hljs-string">'debugo'</span>,<span class="hljs-string">'info:industry'</span>, <span class="hljs-string">'it'</span>
put <span class="hljs-string">'member'</span>, <span class="hljs-string">'debugo'</span>,<span class="hljs-string">'address:city'</span>,<span class="hljs-string">'beijing'</span>
put <span class="hljs-string">'member'</span>, <span class="hljs-string">'debugo'</span>,<span class="hljs-string">'address:country'</span>,<span class="hljs-string">'china'</span>
put <span class="hljs-string">'member'</span>, <span class="hljs-string">'Sariel'</span>, <span class="hljs-string">'id'</span>, <span class="hljs-string">'21'</span>
put <span class="hljs-string">'member'</span>, <span class="hljs-string">'Sariel'</span>,<span class="hljs-string">'info:age'</span>, <span class="hljs-string">'26'</span>
put <span class="hljs-string">'member'</span>, <span class="hljs-string">'Sariel'</span>,<span class="hljs-string">'info:birthday'</span>, <span class="hljs-string">'1988-05-09 '</span>
put <span class="hljs-string">'member'</span>, <span class="hljs-string">'Sariel'</span>,<span class="hljs-string">'info:industry'</span>, <span class="hljs-string">'it'</span>
put <span class="hljs-string">'member'</span>, <span class="hljs-string">'Sariel'</span>,<span class="hljs-string">'address:city'</span>, <span class="hljs-string">'beijing'</span>
put <span class="hljs-string">'member'</span>, <span class="hljs-string">'Sariel'</span>,<span class="hljs-string">'address:country'</span>, <span class="hljs-string">'china'</span>
put <span class="hljs-string">'member'</span>, <span class="hljs-string">'Elvis'</span>, <span class="hljs-string">'id'</span>, <span class="hljs-string">'22'</span>
put <span class="hljs-string">'member'</span>, <span class="hljs-string">'Elvis'</span>,<span class="hljs-string">'info:age'</span>, <span class="hljs-string">'26'</span>
put <span class="hljs-string">'member'</span>, <span class="hljs-string">'Elvis'</span>,<span class="hljs-string">'info:birthday'</span>, <span class="hljs-string">'1988-09-14 '</span>
put <span class="hljs-string">'member'</span>, <span class="hljs-string">'Elvis'</span>,<span class="hljs-string">'info:industry'</span>, <span class="hljs-string">'it'</span>
put <span class="hljs-string">'member'</span>, <span class="hljs-string">'Elvis'</span>,<span class="hljs-string">'address:city'</span>, <span class="hljs-string">'beijing'</span>
put <span class="hljs-string">'member'</span>, <span class="hljs-string">'Elvis'</span>,<span class="hljs-string">'address:country'</span>, <span class="hljs-string">'china'</span></code></pre>

<p>获取一个id的所有数据</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'member'</span>, <span class="hljs-string">'Sariel'</span>
COLUMN                           CELL                                                                                        
 address:city                    timestamp=<span class="hljs-number">1425871035382</span>, <span class="hljs-built_in">value</span>=beijing                                                      
 address:country                 timestamp=<span class="hljs-number">1425871035424</span>, <span class="hljs-built_in">value</span>=china                                                        
 id:                             timestamp=<span class="hljs-number">1425871035176</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">21</span>                                                           
 info:age                        timestamp=<span class="hljs-number">1425871035225</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">26</span>                                                           
 info:birthday                   timestamp=<span class="hljs-number">1425871035296</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">1988</span>-<span class="hljs-number">05</span>-<span class="hljs-number">09</span>                                                   
 info:industry                   timestamp=<span class="hljs-number">1425871035334</span>, <span class="hljs-built_in">value</span>=<span class="hljs-keyword">it</span>                                                           
<span class="hljs-number">6</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0530</span> <span class="hljs-built_in">seconds</span></code></pre>

<p>获得一个id，一个列簇（一个列）中的所有数据:</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'member'</span>, <span class="hljs-string">'Sariel'</span>, <span class="hljs-string">'info'</span>
COLUMN                           CELL                                                                                        
 info:age                        timestamp=<span class="hljs-number">1425871035225</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">26</span>                                                           
 info:birthday                   timestamp=<span class="hljs-number">1425871035296</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">1988</span>-<span class="hljs-number">05</span>-<span class="hljs-number">09</span>                                                   
 info:industry                   timestamp=<span class="hljs-number">1425871035334</span>, <span class="hljs-built_in">value</span>=<span class="hljs-keyword">it</span>                                                           
<span class="hljs-number">3</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0320</span> <span class="hljs-built_in">seconds</span>
&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'member'</span>, <span class="hljs-string">'Sariel'</span>, <span class="hljs-string">'info:age'</span>
COLUMN                           CELL                                                                                        
 info:age                        timestamp=<span class="hljs-number">1425871035225</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">26</span>                                                           
<span class="hljs-number">1</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0270</span> <span class="hljs-built_in">seconds</span></code></pre>

<p>通过describe ‘member’可以看到，默认情况下列簇只保存1个version。我们先将其修改到2,然后update一些信息。</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">&gt; alter <span class="hljs-string">'member'</span>, {NAME=&gt; <span class="hljs-string">'info'</span>, VERSIONS =&gt; <span class="hljs-number">2</span>}
Updating all regions <span class="hljs-operator">with</span> <span class="hljs-operator">the</span> <span class="hljs-built_in">new</span> schema...
<span class="hljs-number">0</span>/<span class="hljs-number">1</span> regions updated.
<span class="hljs-number">1</span>/<span class="hljs-number">1</span> regions updated.
Done.
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">2.2580</span> <span class="hljs-built_in">seconds</span>
&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'member'</span>, <span class="hljs-string">'debugo'</span>,<span class="hljs-string">'info:age'</span>,<span class="hljs-string">'29'</span>
&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'member'</span>, <span class="hljs-string">'debugo'</span>,<span class="hljs-string">'info:age'</span>,<span class="hljs-string">'28'</span>
&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'member'</span>, <span class="hljs-string">'debugo'</span>, {COLUMN=&gt;<span class="hljs-string">'info:age'</span>, VERSIONS=&gt;<span class="hljs-number">2</span>}
COLUMN                           CELL                                                                                        
 info:age                        timestamp=<span class="hljs-number">1425884510241</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">28</span>                                                           
 info:age                        timestamp=<span class="hljs-number">1425884510195</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">29</span>                                                           
<span class="hljs-number">2</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0400</span> <span class="hljs-built_in">seconds</span></code></pre>



<h2 id="4-其他dml语句">4. 其他DML语句</h2>

<p>通过delete命令，我们可以删除id为某个值的‘info:age’字段，接下来的get就无视了</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">&gt; <span class="hljs-built_in">delete</span> <span class="hljs-string">'member'</span>,<span class="hljs-string">'debugo'</span>,<span class="hljs-string">'info:age'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0420</span> <span class="hljs-built_in">seconds</span>
&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'member'</span>,<span class="hljs-string">'debugo'</span>,<span class="hljs-string">'info:age'</span>
COLUMN                           CELL
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.3270</span> <span class="hljs-built_in">seconds</span></code></pre>

<p>通过deleteall来删除整行</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">&gt; <span class="hljs-built_in">delete</span> <span class="hljs-string">'member'</span>,<span class="hljs-string">'debugo'</span>,<span class="hljs-string">'info:age'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0420</span> <span class="hljs-built_in">seconds</span>
&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'member'</span>,<span class="hljs-string">'debugo'</span>,<span class="hljs-string">'info:age'</span>
COLUMN                           CELL
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.3270</span> <span class="hljs-built_in">seconds</span></code></pre>

<p>给’Sariel’的’info:age’字段添加，并使用incr实现递增。但需要注意的是，这个value需要是一个数值，如果使用单引号标识的字符串就无法使用incr。在使用Java API开发时，我们可以使用toBytes函数讲数值转换成byte字节。在HBase shell中我们只能通过incr来初始化这个列，</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">&gt; <span class="hljs-built_in">delete</span> <span class="hljs-string">'member'</span>,<span class="hljs-string">'Sariel'</span>,<span class="hljs-string">'info:age'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0270</span> <span class="hljs-built_in">seconds</span>
&gt; incr <span class="hljs-string">'member'</span>,<span class="hljs-string">'Sariel'</span>,<span class="hljs-string">'info:age'</span>,<span class="hljs-number">26</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0290</span> <span class="hljs-built_in">seconds</span>
&gt; incr <span class="hljs-string">'member'</span>,<span class="hljs-string">'Sariel'</span>,<span class="hljs-string">'info:age'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0290</span> <span class="hljs-built_in">seconds</span>
&gt; incr <span class="hljs-string">'member'</span>,<span class="hljs-string">'Sariel'</span>,<span class="hljs-string">'info:age'</span>, -<span class="hljs-number">1</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0230</span> <span class="hljs-built_in">seconds</span>
&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'member'</span>,<span class="hljs-string">'Sariel'</span>,<span class="hljs-string">'info:age'</span>
COLUMN   CELL                                                                                        
 info:age   timestamp=<span class="hljs-number">1425890213341</span>, <span class="hljs-built_in">value</span>=\x00\x00\x00\x00\x00\x00\x00\x1A                             
<span class="hljs-number">1</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0280</span> <span class="hljs-built_in">seconds</span></code></pre>

<p>十六进制1A是26，通过上面增1再减1后得到的结果。下面通过count统计行数。</p>



<pre class="prettyprint"><code class=" hljs coffeescript">&gt; count <span class="hljs-string">'member'</span>
<span class="hljs-number">2</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0750</span> seconds<span class="hljs-function">
=&gt;</span> <span class="hljs-number">2</span></code></pre>

<p>通过truncate来截断表。hbase是先将掉disable掉，然后drop掉后重建表来实现truncate的功能的。</p>



<pre class="prettyprint"><code class=" hljs lua">hbase(main):<span class="hljs-number">010</span>:<span class="hljs-number">0</span>&gt; truncate <span class="hljs-string">'member'</span>
Truncating <span class="hljs-string">'member'</span> <span class="hljs-built_in">table</span> (it may take a <span class="hljs-keyword">while</span>):
 - Disabling <span class="hljs-built_in">table</span>...
 - Dropping <span class="hljs-built_in">table</span>...
 - Creating <span class="hljs-built_in">table</span>...
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">2.3260</span> seconds</code></pre>



<h2 id="5-scan和filter">5. scan和filter</h2>

<p>通过scan来对全表进行扫描。我们将之前put的数据恢复。</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">&gt; scan <span class="hljs-string">'member'</span>
ROW                COLUMN+CELL                                         
 Elvis             column=address:city, timestamp=<span class="hljs-number">1425891057211</span>, <span class="hljs-built_in">value</span>=
                   beijing                                             
 Elvis             column=address:country, timestamp=<span class="hljs-number">1425891057258</span>, val
                   ue=china                                            
 Elvis             column=id:, timestamp=<span class="hljs-number">1425891057038</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">22</span>       
 Elvis             column=info:age, timestamp=<span class="hljs-number">1425891057083</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">26</span>  
 Elvis             column=info:birthday, timestamp=<span class="hljs-number">1425891057129</span>, <span class="hljs-built_in">value</span>
                   =<span class="hljs-number">1988</span>-<span class="hljs-number">09</span>-<span class="hljs-number">14</span>                                         
 Elvis             column=info:industry, timestamp=<span class="hljs-number">1425891057172</span>, <span class="hljs-built_in">value</span>
                   =<span class="hljs-keyword">it</span>                                                 
 Sariel            column=address:city, timestamp=<span class="hljs-number">1425891056965</span>, <span class="hljs-built_in">value</span>=
                   beijing                                             
 Sariel            column=address:country, timestamp=<span class="hljs-number">1425891057003</span>, val
                   ue=china                                            
 Sariel            column=id:, timestamp=<span class="hljs-number">1425891056767</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">21</span>       
 Sariel            column=info:age, timestamp=<span class="hljs-number">1425891056808</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">26</span>  
 Sariel            column=info:birthday, timestamp=<span class="hljs-number">1425891056883</span>, <span class="hljs-built_in">value</span>
                   =<span class="hljs-number">1988</span>-<span class="hljs-number">05</span>-<span class="hljs-number">09</span>                                         
 Sariel            column=info:industry, timestamp=<span class="hljs-number">1425891056924</span>, <span class="hljs-built_in">value</span>
                   =<span class="hljs-keyword">it</span>                                                 
 debugo            column=address:city, timestamp=<span class="hljs-number">1425891056642</span>, <span class="hljs-built_in">value</span>=
                   beijing                                             
 debugo            column=address:country, timestamp=<span class="hljs-number">1425891056726</span>, val
                   ue=china                                            
 debugo            column=id:, timestamp=<span class="hljs-number">1425891056419</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">11</span>       
 debugo            column=info:age, timestamp=<span class="hljs-number">1425891056499</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">27</span>  
 debugo            column=info:birthday, timestamp=<span class="hljs-number">1425891056547</span>, <span class="hljs-built_in">value</span>
                   =<span class="hljs-number">1987</span>-<span class="hljs-number">04</span>-<span class="hljs-number">04</span>                                         
 debugo            column=info:industry, timestamp=<span class="hljs-number">1425891056597</span>, <span class="hljs-built_in">value</span>
                   =<span class="hljs-keyword">it</span>                                                 
<span class="hljs-number">3</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0660</span> seconds3 row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0590</span> <span class="hljs-built_in">seconds</span></code></pre>

<p>指定扫描其中的某个列：</p>



<pre class="prettyprint"><code class=" hljs php">&gt; scan <span class="hljs-string">'member'</span>, {COLUMNS=&gt; <span class="hljs-string">'info:birthday'</span>}</code></pre>

<p>或者整个列簇：</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">&gt; scan <span class="hljs-string">'member'</span>, {COLUMNS=&gt; <span class="hljs-string">'info'</span>}
ROW                              COLUMN+CELL                                                                                 
 Elvis                           column=info:age, timestamp=<span class="hljs-number">1425891057083</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">26</span>                                          
 Elvis                           column=info:birthday, timestamp=<span class="hljs-number">1425891057129</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">1988</span>-<span class="hljs-number">09</span>-<span class="hljs-number">14</span>                             
 Elvis                           column=info:industry, timestamp=<span class="hljs-number">1425891057172</span>, <span class="hljs-built_in">value</span>=<span class="hljs-keyword">it</span>                                     
 Sariel                          column=info:age, timestamp=<span class="hljs-number">1425891056808</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">26</span>                                          
 Sariel                          column=info:birthday, timestamp=<span class="hljs-number">1425891056883</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">1988</span>-<span class="hljs-number">05</span>-<span class="hljs-number">09</span>                             
 Sariel                          column=info:industry, timestamp=<span class="hljs-number">1425891056924</span>, <span class="hljs-built_in">value</span>=<span class="hljs-keyword">it</span>                                     
 debugo                          column=info:age, timestamp=<span class="hljs-number">1425891056499</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">27</span>                                          
 debugo                          column=info:birthday, timestamp=<span class="hljs-number">1425891056547</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">1987</span>-<span class="hljs-number">04</span>-<span class="hljs-number">04</span>                             
 debugo                          column=info:industry, timestamp=<span class="hljs-number">1425891056597</span>, <span class="hljs-built_in">value</span>=<span class="hljs-keyword">it</span>                                     
<span class="hljs-number">3</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0650</span> <span class="hljs-built_in">seconds</span></code></pre>

<p>除了列（COLUMNS）修饰词外，HBase还支持Limit（限制查询结果行数），STARTROW （ROWKEY起始行。会先根据这个key定位到region，再向后扫描）、STOPROW(结束行)、TIMERANGE（限定时间戳范围）、VERSIONS（版本数）、和FILTER（按条件过滤行）等。比如我们从Sariel这个rowkey开始，找下一个行的最新版本：</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">&gt; scan <span class="hljs-string">'member'</span>, { STARTROW =&gt; <span class="hljs-string">'Sariel'</span>, LIMIT=&gt;<span class="hljs-number">1</span>, VERSIONS=&gt;<span class="hljs-number">1</span>}
ROW    COLUMN+CELL
 Sariel   column=address:city, timestamp=<span class="hljs-number">1425891056965</span>, <span class="hljs-built_in">value</span>=beijing
 Sariel   column=address:country, timestamp=<span class="hljs-number">1425891057003</span>, <span class="hljs-built_in">value</span>=china
 Sariel   column=id:, timestamp=<span class="hljs-number">1425891056767</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">21</span>
 Sariel   column=info:age, timestamp=<span class="hljs-number">1425891056808</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">26</span>
 Sariel   column=info:birthday, timestamp=<span class="hljs-number">1425891056883</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">1988</span>-<span class="hljs-number">05</span>-<span class="hljs-number">09</span>
 Sariel   column=info:industry, timestamp=<span class="hljs-number">1425891056924</span>, <span class="hljs-built_in">value</span>=<span class="hljs-keyword">it</span>
<span class="hljs-number">1</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0410</span> <span class="hljs-built_in">seconds</span></code></pre>

<p>Filter是一个非常强大的修饰词，可以设定一系列条件来进行过滤。比如我们要限制某个列的值等于26：</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">&gt; scan <span class="hljs-string">'member'</span>, FILTER=&gt;<span class="hljs-string">"ValueFilter(=,'binary:26')"</span>
ROW    COLUMN+CELL
 Elvis    column=info:age, timestamp=<span class="hljs-number">1425891057083</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">26</span>
 Sariel   column=info:age, timestamp=<span class="hljs-number">1425891056808</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">26</span>
<span class="hljs-number">2</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0620</span> <span class="hljs-built_in">seconds</span></code></pre>

<p>值包含6这个值：</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">&gt; scan <span class="hljs-string">'member'</span>, FILTER=&gt;<span class="hljs-string">"ValueFilter(=,'substring:6')"</span>
Elvis    column=info:age, timestamp=<span class="hljs-number">1425891057083</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">26</span>
 Sariel   column=info:age, timestamp=<span class="hljs-number">1425891056808</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">26</span>
<span class="hljs-number">2</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0620</span> <span class="hljs-built_in">seconds</span></code></pre>

<p>列名中的前缀为birthday的：</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">&gt; scan <span class="hljs-string">'member'</span>, FILTER=&gt;<span class="hljs-string">"ColumnPrefixFilter('birth') "</span>
ROW                              COLUMN+CELL                                                                                 
 Elvis                           column=info:birthday, timestamp=<span class="hljs-number">1425891057129</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">1988</span>-<span class="hljs-number">09</span>-<span class="hljs-number">14</span>                             
 Sariel      column=info:birthday, timestamp=<span class="hljs-number">1425891056883</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">1988</span>-<span class="hljs-number">05</span>-<span class="hljs-number">09</span>
 debugo                          column=info:birthday, timestamp=<span class="hljs-number">1425891056547</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">1987</span>-<span class="hljs-number">04</span>-<span class="hljs-number">04</span>                             
<span class="hljs-number">3</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0450</span> <span class="hljs-built_in">seconds</span></code></pre>

<p>FILTER中支持多个过滤条件通过括号、AND和OR的条件组合。</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">&gt; scan <span class="hljs-string">'member'</span>, FILTER=&gt;<span class="hljs-string">"ColumnPrefixFilter('birth') AND ValueFilter ValueFilter(=,'substring:1987')"</span>
ROW       COLUMN+CELL                                                                                 
Debugo    column=info:birthday, timestamp=<span class="hljs-number">1425891056547</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">1987</span>-<span class="hljs-number">04</span>-<span class="hljs-number">04</span>
<span class="hljs-number">1</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0450</span> <span class="hljs-built_in">seconds</span></code></pre>

<p>同一个rowkey的同一个column有多个version，根据timestamp来区分。而每一个列簇有多个column。而FIRSTKEYONLY仅取出每个列簇的第一个column的第一个版本。而KEYONLY则是对于每一个column只去取出key，把VALUE的信息丢弃,一般和其他filter结合使用。例如：</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">&gt; scan <span class="hljs-string">'member'</span>, FILTER=&gt;<span class="hljs-string">"FirstKeyOnlyFilter()"</span>
ROW    COLUMN+CELL                                                                                 
 Elvis    column=address:city, timestamp=<span class="hljs-number">1425891057211</span>, <span class="hljs-built_in">value</span>=beijing                                 
 Sariel   column=address:city, timestamp=<span class="hljs-number">1425891056965</span>, <span class="hljs-built_in">value</span>=beijing                                 
 debugo column=address:city, timestamp=<span class="hljs-number">1425891056642</span>, <span class="hljs-built_in">value</span>=beijing                                 
<span class="hljs-number">3</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0230</span> <span class="hljs-built_in">seconds</span>
&gt; scan <span class="hljs-string">'member'</span>, FILTER=&gt;<span class="hljs-string">"KeyOnlyFilter()"</span>
hbase(main):<span class="hljs-number">055</span>:<span class="hljs-number">0</span>&gt; scan <span class="hljs-string">'member'</span>, FILTER=&gt;<span class="hljs-string">"KeyOnlyFilter()"</span>
ROW    COLUMN+CELL 
Elvis    column=address:city, timestamp=<span class="hljs-number">1425891057211</span>, <span class="hljs-built_in">value</span>=
Elvis    column=id:, timestamp=<span class="hljs-number">1425891057038</span>, <span class="hljs-built_in">value</span>= 
……</code></pre>

<p>PrefixFilter是对Rowkey的前缀进行判断,这是一个非常常用的功能。</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">&gt; scan <span class="hljs-string">'member'</span>, FILTER=&gt;<span class="hljs-string">"PrefixFilter('E')"</span>
ROW    COLUMN+CELL                                                                                 
Elvis    column=address:city, timestamp=<span class="hljs-number">1425891057211</span>, <span class="hljs-built_in">value</span>=beijing                                 
……
<span class="hljs-number">1</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0460</span> <span class="hljs-built_in">seconds</span></code></pre>

<p><strong>转载：<a href="http://debugo.com/hbase-shell-cmds/" rel="nofollow">http://debugo.com/hbase-shell-cmds/</a> </strong></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>