---
layout:     post
title:      Hbase的常用shell操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_20641565/article/details/54923851				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<ul>
<li><strong>1.进入hbase的shell界面：</strong></li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@lijie</span> bin]<span class="hljs-comment"># ./hbase shell</span></code></pre>

<ul>
<li><strong>2.创建表</strong></li>
</ul>



<pre class="prettyprint"><code class=" hljs coffeescript">hbase(main):<span class="hljs-number">002</span>:<span class="hljs-number">0</span>&gt; create <span class="hljs-string">'tablename'</span>,<span class="hljs-string">'cf1'</span>,<span class="hljs-string">'cf2'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.5830</span> seconds<span class="hljs-function">

=&gt;</span> <span class="hljs-attribute">Hbase</span>::Table - tablename</code></pre>

<ul>
<li><strong>3.列出所有表</strong></li>
</ul>



<pre class="prettyprint"><code class=" hljs haml">hbase(main):003:0&gt; list
TABLE                                                                                                                                                              
stu                                                                                                                                                                
t1                                                                                                                                                                 
tablename                                                                                                                                                          
3 row(s) in 0.0100 seconds

=<span class="ruby">&gt; [<span class="hljs-string">"stu"</span>, <span class="hljs-string">"t1"</span>, <span class="hljs-string">"tablename"</span>]
</span></code></pre>

<ul>
<li><strong>4.插入数据</strong></li>
</ul>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">005</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'tablename'</span>,<span class="hljs-string">'rowkey01'</span>,<span class="hljs-string">'cf1:name'</span>,<span class="hljs-string">'lijie'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0570</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">006</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'tablename'</span>,<span class="hljs-string">'rowkey01'</span>,<span class="hljs-string">'cf1:addr'</span>,<span class="hljs-string">'chongqing'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0070</span> <span class="hljs-built_in">seconds</span>
</code></pre>

<ul>
<li><strong>5.查看表的所有数据</strong></li>
</ul>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">012</span>:<span class="hljs-number">0</span>&gt; scan <span class="hljs-string">'tablename'</span>
ROW                                       COLUMN+CELL                                                                                                              
 rowkey01                                 column=cf1:addr, timestamp=<span class="hljs-number">1486547683853</span>, <span class="hljs-built_in">value</span>=chongqing                                                                
 rowkey01                                 column=cf1:name, timestamp=<span class="hljs-number">1486547607486</span>, <span class="hljs-built_in">value</span>=lijie                                                                    
 rowkey02                                 column=cf1:addr, timestamp=<span class="hljs-number">1486547771954</span>, <span class="hljs-built_in">value</span>=shenzhen                                                                 
 rowkey02                                 column=cf1:name, timestamp=<span class="hljs-number">1486547737573</span>, <span class="hljs-built_in">value</span>=zhangsan                                                                 
<span class="hljs-number">2</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0280</span> <span class="hljs-built_in">seconds</span>
</code></pre>

<ul>
<li><strong>6.根据rowkey查看数据</strong></li>
</ul>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">015</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'tablename'</span>,<span class="hljs-string">'rowkey01'</span>
COLUMN                                    CELL                                                                                                                     
 cf1:addr                                 timestamp=<span class="hljs-number">1486547683853</span>, <span class="hljs-built_in">value</span>=chongqing                                                                                 
 cf1:name                                 timestamp=<span class="hljs-number">1486547607486</span>, <span class="hljs-built_in">value</span>=lijie                                                                                     
<span class="hljs-number">2</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0250</span> <span class="hljs-built_in">seconds</span>
</code></pre>

<ul>
<li><strong>7.查询数据的条数</strong></li>
</ul>



<pre class="prettyprint"><code class=" hljs coffeescript">hbase(main):<span class="hljs-number">016</span>:<span class="hljs-number">0</span>&gt; count <span class="hljs-string">'tablename'</span>
<span class="hljs-number">2</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0410</span> seconds<span class="hljs-function">

=&gt;</span> <span class="hljs-number">2</span>
</code></pre>

<ul>
<li><strong>8.获取一行指定列族的数据</strong></li>
</ul>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">017</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'tablename'</span>,<span class="hljs-string">'rowkey01'</span>,<span class="hljs-string">'cf1'</span>
COLUMN                                    CELL                                                                                                                     
 cf1:addr                                 timestamp=<span class="hljs-number">1486547683853</span>, <span class="hljs-built_in">value</span>=chongqing                                                                                 
 cf1:name                                 timestamp=<span class="hljs-number">1486547607486</span>, <span class="hljs-built_in">value</span>=lijie                                                                                     
<span class="hljs-number">2</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0120</span> <span class="hljs-built_in">seconds</span>
</code></pre>

<ul>
<li><strong>9.获取一行指定列族某列数据</strong></li>
</ul>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">021</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'tablename'</span>,<span class="hljs-string">'rowkey01'</span>,<span class="hljs-string">'cf1:name'</span>
COLUMN                                    CELL                                                                                                                     
 cf1:name                                 timestamp=<span class="hljs-number">1486547607486</span>, <span class="hljs-built_in">value</span>=lijie                                                                                     
<span class="hljs-number">1</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0080</span> <span class="hljs-built_in">seconds</span></code></pre>

<ul>
<li><strong>10.删除列族</strong></li>
</ul>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">027</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">delete</span> <span class="hljs-string">'tablename'</span>,<span class="hljs-string">'rowkey01'</span>,<span class="hljs-string">'cf1:addr'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0030</span> <span class="hljs-built_in">seconds</span>
</code></pre>

<ul>
<li><strong>11.删除一整行数据</strong></li>
</ul>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">029</span>:<span class="hljs-number">0</span>&gt; deleteall <span class="hljs-string">'tablename'</span>,<span class="hljs-string">'rowkey01'</span> 
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0090</span> <span class="hljs-built_in">seconds</span></code></pre>

<ul>
<li><strong>12.更新一条数据</strong></li>
</ul>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">032</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'tablename'</span>,<span class="hljs-string">'rowkey02'</span>,<span class="hljs-string">'cf1:addr'</span>,<span class="hljs-string">'shanghai'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0040</span> <span class="hljs-built_in">seconds</span>
</code></pre>

<ul>
<li><strong>13.是否enable或者disable</strong></li>
</ul>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">034</span>:<span class="hljs-number">0</span>&gt; is_enabled <span class="hljs-string">'tablename'</span>
<span class="hljs-constant">true</span>                                                                                                                                                               
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0070</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">035</span>:<span class="hljs-number">0</span>&gt; is_disabled <span class="hljs-string">'tablename'</span>
<span class="hljs-constant">false</span>                                                                                                                                                              
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0050</span> <span class="hljs-built_in">seconds</span>
</code></pre>

<ul>
<li><strong>14.查看表是否存在</strong></li>
</ul>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">036</span>:<span class="hljs-number">0</span>&gt; exists <span class="hljs-string">'tablename'</span>
Table tablename does exist                                                                                                                                         
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0050</span> <span class="hljs-built_in">seconds</span>
</code></pre>

<ul>
<li><strong>15.服务器状态</strong></li>
</ul>



<pre class="prettyprint"><code class=" hljs css"><span class="hljs-tag">hbase</span>(<span class="hljs-tag">main</span>)<span class="hljs-pseudo">:037</span><span class="hljs-pseudo">:0</span>&gt; <span class="hljs-tag">status</span>
1 <span class="hljs-tag">servers</span>, 0 <span class="hljs-tag">dead</span>, 5<span class="hljs-class">.0000</span> <span class="hljs-tag">average</span> <span class="hljs-tag">load</span>
</code></pre>

<ul>
<li><strong>16.hbase版本</strong></li>
</ul>



<pre class="prettyprint"><code class=" hljs css"><span class="hljs-tag">hbase</span>(<span class="hljs-tag">main</span>)<span class="hljs-pseudo">:039</span><span class="hljs-pseudo">:0</span>&gt; <span class="hljs-tag">version</span>
1<span class="hljs-class">.0</span><span class="hljs-class">.3</span>, <span class="hljs-tag">rf1e1312f9790a7c40f6a4b5a1bab2ea1dd559890</span>, <span class="hljs-tag">Tue</span> <span class="hljs-tag">Jan</span> 19 19<span class="hljs-pseudo">:26</span><span class="hljs-pseudo">:53</span> <span class="hljs-tag">PST</span> 2016
</code></pre>

<ul>
<li><strong>17.删除一个列族</strong></li>
</ul>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">040</span>:<span class="hljs-number">0</span>&gt; disable <span class="hljs-string">'tablename'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">1.2310</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">041</span>:<span class="hljs-number">0</span>&gt; alter <span class="hljs-string">'tablename'</span>,{NAME =&gt; <span class="hljs-string">'cf1'</span>,METHOD =&gt; <span class="hljs-string">'delete'</span>} 
Updating all regions <span class="hljs-operator">with</span> <span class="hljs-operator">the</span> <span class="hljs-built_in">new</span> schema...
<span class="hljs-number">1</span>/<span class="hljs-number">1</span> regions updated.
Done.
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">1.1460</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">042</span>:<span class="hljs-number">0</span>&gt; enable <span class="hljs-string">'tablename'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.1550</span> <span class="hljs-built_in">seconds</span></code></pre>

<ul>
<li><strong>18.获得表的描述</strong></li>
</ul>

<pre class="prettyprint"><code class=" hljs coffeescript">hbase(main):<span class="hljs-number">044</span>:<span class="hljs-number">0</span>&gt; describe <span class="hljs-string">'tablename'</span>
Table tablename <span class="hljs-keyword">is</span> ENABLED                                                                                                                                         
tablename                                                                                                                                                          
COLUMN FAMILIES DESCRIPTION                                                                                                                                        
{NAME<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'cf2'</span>, DATA_BLOCK_ENCODING<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, BLOOMFILTER<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'ROW'</span>, REPLICATION_SCOPE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'1'</span>, COMPRESSION<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, MIN_VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, TTL<span class="hljs-function"> =&gt;</span> 
<span class="hljs-string">'FOREVER'</span>, KEEP_DELETED_CELLS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FALSE'</span>, BLOCKSIZE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'65536'</span>, IN_MEMORY<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'false'</span>, BLOCKCACHE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'true'</span>}                                                        
<span class="hljs-number">1</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0150</span> seconds

hbase(main):<span class="hljs-number">045</span>:<span class="hljs-number">0</span>&gt; desc <span class="hljs-string">'tablename'</span>
Table tablename <span class="hljs-keyword">is</span> ENABLED                                                                                                                                         
tablename                                                                                                                                                          
COLUMN FAMILIES DESCRIPTION                                                                                                                                        
{NAME<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'cf2'</span>, DATA_BLOCK_ENCODING<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, BLOOMFILTER<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'ROW'</span>, REPLICATION_SCOPE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'1'</span>, COMPRESSION<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, MIN_VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, TTL<span class="hljs-function"> =&gt;</span> 
<span class="hljs-string">'FOREVER'</span>, KEEP_DELETED_CELLS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FALSE'</span>, BLOCKSIZE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'65536'</span>, IN_MEMORY<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'false'</span>, BLOCKCACHE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'true'</span>}                                                        
<span class="hljs-number">1</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0240</span> seconds</code></pre>

<ul>
<li><strong>19.清空整个表</strong></li>
</ul>

<pre class="prettyprint"><code class=" hljs lua">hbase(main):<span class="hljs-number">046</span>:<span class="hljs-number">0</span>&gt; truncate <span class="hljs-string">'tablename'</span>
Truncating <span class="hljs-string">'tablename'</span> <span class="hljs-built_in">table</span> (it may take a <span class="hljs-keyword">while</span>):
 - Disabling <span class="hljs-built_in">table</span>...
 - Truncating <span class="hljs-built_in">table</span>...
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">1.3600</span> seconds
</code></pre>

<ul>
<li><strong>20.删除整个表</strong></li>
</ul>

<pre class="prettyprint"><code class=" hljs ocaml">hbase(main):<span class="hljs-number">048</span>:<span class="hljs-number">0</span>&gt; disable <span class="hljs-string">'tablename'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">1.1720</span> seconds

hbase(main):<span class="hljs-number">049</span>:<span class="hljs-number">0</span>&gt; drop <span class="hljs-string">'tablename'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.1720</span> seconds

hbase(main):<span class="hljs-number">050</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">list</span>
TABLE                                                                                                                                                              
stu                                                                                                                                                                
t1                                                                                                                                                                 
<span class="hljs-number">2</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0100</span> seconds

=&gt; [<span class="hljs-string">"stu"</span>, <span class="hljs-string">"t1"</span>]
</code></pre>

<p>下次更新filter操作，未完待续。。。。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>