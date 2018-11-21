---
layout:     post
title:      Hbase 之shell命令(二)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：小兄弟，慢慢熬吧......					https://blog.csdn.net/u013850277/article/details/73203809				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>hbase shell ： 进入hbase shell console</p>

<ol>
<li>创建一个表member ： （表名： ‘member’,  列族 ：’m_id’,’address’,’info’） <br>
在创建表只需要指定表名与列族便可</li>
</ol>



<pre class="prettyprint"><code class=" hljs livecodeserver"> hbase(main):<span class="hljs-number">025</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">create</span> <span class="hljs-string">'member'</span>,<span class="hljs-string">'m_id'</span>,<span class="hljs-string">'address'</span>,<span class="hljs-string">'info'</span></code></pre>

<p>2.list #查看所有表</p>



<pre class="prettyprint"><code class=" hljs fsharp">hbase(main):<span class="hljs-number">037</span>:<span class="hljs-number">0</span>&gt; list
TABLE                                                                                                                                                                                                            
blog                                                                                                                                                                                                             
<span class="hljs-keyword">member</span>                                                                                                                                                                                                           
table1                                                                                                                                                                                                           
<span class="hljs-number">3</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0090</span> seconds

=&gt; [<span class="hljs-string">"blog"</span>, <span class="hljs-string">"member"</span>, <span class="hljs-string">"table1"</span>]</code></pre>

<p>3.查看表描述 desc ‘tablename’</p>



<pre class="prettyprint"><code class=" hljs coffeescript">hbase(main):<span class="hljs-number">040</span>:<span class="hljs-number">0</span>* desc <span class="hljs-string">'member'</span>
Table member <span class="hljs-keyword">is</span> ENABLED                                                                                                                                                                                          
member                                                                                                                                                                                                           
COLUMN FAMILIES DESCRIPTION                                                                                                                                                                                      
{NAME<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'address'</span>, BLOOMFILTER<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'ROW'</span>, VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'1'</span>, IN_MEMORY<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'false'</span>, KEEP_DELETED_CELLS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FALSE'</span>, DATA_BLOCK_ENCODING<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, TTL<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FOREVER'</span>, COMPRESSION<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, MIN_VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, BLOC
KCACHE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'true'</span>, BLOCKSIZE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'65536'</span>, REPLICATION_SCOPE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>}                                                                                                                                                
{NAME<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'info'</span>, BLOOMFILTER<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'ROW'</span>, VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'1'</span>, IN_MEMORY<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'false'</span>, KEEP_DELETED_CELLS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FALSE'</span>, DATA_BLOCK_ENCODING<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, TTL<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FOREVER'</span>, COMPRESSION<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, MIN_VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, BLOCKCA
CHE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'true'</span>, BLOCKSIZE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'65536'</span>, REPLICATION_SCOPE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>}                                                                                                                                                   
{NAME<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'m_id'</span>, BLOOMFILTER<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'ROW'</span>, VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'1'</span>, IN_MEMORY<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'false'</span>, KEEP_DELETED_CELLS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FALSE'</span>, DATA_BLOCK_ENCODING<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, TTL<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FOREVER'</span>, COMPRESSION<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, MIN_VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, BLOCKCA
CHE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'true'</span>, BLOCKSIZE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'65536'</span>, REPLICATION_SCOPE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>}                                                                                                                                                   
<span class="hljs-number">3</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0250</span> seconds</code></pre>

<p>4、插入数据</p>

<pre><code> put命令比较简单，只有这一种用法：
 hbase&gt;put ‘t1′, ‘r1′, ‘c1′, ‘value', ts1
 t1指表名，r1指行键名，c1指列名，value指单元格值。ts1指时间戳，一般都省略掉了。
</code></pre>



<pre class="prettyprint"><code class=" hljs livecodeserver">     eg:   <span class="hljs-built_in">put</span><span class="hljs-string">'member'</span>,<span class="hljs-string">'scutshuxue'</span>,<span class="hljs-string">'info:age'</span>,<span class="hljs-string">'24'</span>
           <span class="hljs-built_in">put</span><span class="hljs-string">'member'</span>,<span class="hljs-string">'scutshuxue'</span>,<span class="hljs-string">'info:birthday'</span>,<span class="hljs-string">'1987-06-17'</span>
           <span class="hljs-built_in">put</span><span class="hljs-string">'member'</span>,<span class="hljs-string">'scutshuxue'</span>,<span class="hljs-string">'info:company'</span>,<span class="hljs-string">'alibaba'</span>
           <span class="hljs-built_in">put</span><span class="hljs-string">'member'</span>,<span class="hljs-string">'scutshuxue'</span>,<span class="hljs-string">'address:contry'</span>,<span class="hljs-string">'china'</span></code></pre>

<p>5、  scan浏览表中数据</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">048</span>:<span class="hljs-number">0</span>&gt;  scan <span class="hljs-string">'member'</span>,LIMIT=&gt;<span class="hljs-number">10</span>
ROW                                                   COLUMN+CELL                                                                                                                                                
 scutshuxue                                           column=address:contry, timestamp=<span class="hljs-number">1497409704325</span>, <span class="hljs-built_in">value</span>=china                                                                                                
 scutshuxue                                           column=info:age, timestamp=<span class="hljs-number">1497409677439</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">24</span>                                                                                                         
 scutshuxue                                           column=info:birthday, timestamp=<span class="hljs-number">1497409686575</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">1987</span>-<span class="hljs-number">06</span>-<span class="hljs-number">17</span>                                                                                            
 scutshuxue                                           column=info:company, timestamp=<span class="hljs-number">1497409696404</span>, <span class="hljs-built_in">value</span>=alibaba                                                                                                
<span class="hljs-number">1</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0320</span> <span class="hljs-built_in">seconds</span></code></pre>



<pre class="prettyprint"><code class=" hljs php">    也可以指定一些修饰词：TIMERANGE, FILTER, LIMIT,STARTROW, STOPROW, TIMESTAMP, MAXLENGTH,<span class="hljs-keyword">or</span> COLUMNS
    eg:hbase&gt;scan ‘t1′, {COLUMNS =&gt; ‘c1′, TIMERANGE =&gt; [<span class="hljs-number">1303668804</span>, <span class="hljs-number">1303668904</span>]}</code></pre>

<p>6、查询数据</p>



<pre class="prettyprint"><code class=" hljs cs">    获取一个id的所有数据
     hbase(main):<span class="hljs-number">050</span>:<span class="hljs-number">0</span>* <span class="hljs-keyword">get</span> <span class="hljs-string">'member'</span>,<span class="hljs-string">'scutshuxue'</span>
COLUMN                                                CELL                                                                                                                                                       
 address:contry                                       timestamp=<span class="hljs-number">1497409704325</span>, <span class="hljs-keyword">value</span>=china                                                                                                                       
 info:age                                             timestamp=<span class="hljs-number">1497409677439</span>, <span class="hljs-keyword">value</span>=<span class="hljs-number">24</span>                                                                                                                          
 info:birthday                                        timestamp=<span class="hljs-number">1497409686575</span>, <span class="hljs-keyword">value</span>=<span class="hljs-number">1987</span>-<span class="hljs-number">06</span>-<span class="hljs-number">17</span>                                                                                                                  
 info:company                                         timestamp=<span class="hljs-number">1497409696404</span>, <span class="hljs-keyword">value</span>=alibaba  

     获取一个id，一个列族的所有数据
     hbase(main):<span class="hljs-number">053</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-keyword">get</span> <span class="hljs-string">'member'</span>,<span class="hljs-string">'scutshuxue'</span>,<span class="hljs-string">'info'</span>
COLUMN                                                CELL                                                                                                                                                       
 info:age                                             timestamp=<span class="hljs-number">1497409677439</span>, <span class="hljs-keyword">value</span>=<span class="hljs-number">24</span>                                                                                                                          
 info:birthday                                        timestamp=<span class="hljs-number">1497409962987</span>, <span class="hljs-keyword">value</span>=<span class="hljs-number">1987</span>-<span class="hljs-number">06</span>-<span class="hljs-number">10</span>                                                                                                                  
 info:company                                         timestamp=<span class="hljs-number">1497409696404</span>, <span class="hljs-keyword">value</span>=alibaba 

     获取一个id，一个列族中一个列的所有数据     
hbase(main):<span class="hljs-number">088</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-keyword">get</span> <span class="hljs-string">'member'</span>,<span class="hljs-string">'scutshuxue'</span>,<span class="hljs-string">'info:age'</span>
COLUMN                                                CELL                                                                                                                                                       
 info:age                                             timestamp=<span class="hljs-number">1497410251265</span>, <span class="hljs-keyword">value</span>=<span class="hljs-number">99</span>   
     通过timestamp来获取两个版本的数据
     hbase(main):<span class="hljs-number">057</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-keyword">get</span> <span class="hljs-string">'member'</span>,<span class="hljs-string">'scutshuxue'</span>,{COLUMN=&gt;<span class="hljs-string">'info:age'</span>,TIMESTAMP=&gt;<span class="hljs-number">1497409677439</span>}
COLUMN                                                CELL                                                                                                                                                       
 info:age                                             timestamp=<span class="hljs-number">1497409677439</span>, <span class="hljs-keyword">value</span>=<span class="hljs-number">24</span> </code></pre>

<p>7、更新一条记录（其实更新记录跟插入新记录是一样的） <br>
     将scutshuxue的年龄改成99</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">059</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'member'</span>,<span class="hljs-string">'scutshuxue'</span>,<span class="hljs-string">'info:age'</span> ,<span class="hljs-string">'99'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0140</span> <span class="hljs-built_in">seconds</span></code></pre>



<pre class="prettyprint"><code class=" hljs cs">hbase(main):<span class="hljs-number">061</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-keyword">get</span> <span class="hljs-string">'member'</span>,<span class="hljs-string">'scutshuxue'</span>,<span class="hljs-string">'info:age'</span>
COLUMN                                                CELL                                                                                                                                                       
 info:age                                             timestamp=<span class="hljs-number">1497410251265</span>, <span class="hljs-keyword">value</span>=<span class="hljs-number">99</span></code></pre>

<p>8、删除一个列族m_id：先将表disable（如果表中之前有数据的话，会把数据清空） <br>
   删除一个列族m_id：先将表disable（如果表中之前有数据的话，会把数据清空）</p>



<pre class="prettyprint"><code class=" hljs php"> hbase(main):<span class="hljs-number">030</span>:<span class="hljs-number">0</span>&gt; disable <span class="hljs-string">'member'</span>
   hbase(main):<span class="hljs-number">033</span>:<span class="hljs-number">0</span>&gt; is_enabled <span class="hljs-string">'member'</span>
   hbase(main):<span class="hljs-number">034</span>:<span class="hljs-number">0</span>&gt; alter <span class="hljs-string">'member'</span>,{NAME=&gt;<span class="hljs-string">'m_id'</span>,METHOD=&gt;<span class="hljs-string">'delete'</span>}
   最后enable，否则不能进行其他操作
   hbase(main):<span class="hljs-number">038</span>:<span class="hljs-number">0</span>&gt; enable <span class="hljs-string">'member'</span></code></pre>

<p>9、删除指定数据</p>



<pre class="prettyprint"><code class=" hljs sql">从表删除特定单元格
使用 <span class="hljs-operator"><span class="hljs-keyword">delete</span> 命令，可以在一个表中删除特定单元格。 <span class="hljs-keyword">delete</span> 命令的语法如下：

<span class="hljs-keyword">delete</span> ‘&lt;<span class="hljs-keyword">table</span> name&gt;’, ‘&lt;<span class="hljs-keyword">row</span>&gt;’, ‘&lt;<span class="hljs-keyword">column</span> name &gt;’, ‘&lt;<span class="hljs-keyword">time</span> stamp&gt;’
下面是一个删除特定单元格和例子。在这里，我们删除salary

hbase(main):<span class="hljs-number">006</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-keyword">delete</span> <span class="hljs-string">'emp'</span>, <span class="hljs-string">'1'</span>, <span class="hljs-string">'personal data:city'</span>,
<span class="hljs-number">1417521848375</span>
<span class="hljs-number">0</span> <span class="hljs-keyword">row</span>(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0060</span> seconds
删除表的所有单元格
使用“deleteall”命令，可以删除一行中所有单元格。下面给出是 deleteall 命令的语法。

deleteall ‘&lt;<span class="hljs-keyword">table</span> name&gt;’, ‘&lt;<span class="hljs-keyword">row</span>&gt;’,
这里是使用“deleteall”命令删去 emp 表 row1 的所有单元的一个例子。

hbase(main):<span class="hljs-number">007</span>:<span class="hljs-number">0</span>&gt; deleteall <span class="hljs-string">'emp'</span>,<span class="hljs-string">'1'</span>
<span class="hljs-number">0</span> <span class="hljs-keyword">row</span>(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0240</span> seconds</span></code></pre>

<p>10、统计表数据量，count命令 <br>
最直接的方式是在hbase shell中执行count的命令可以统计行数。</p>

<pre class="prettyprint"><code class=" hljs php">hbase&gt; count ‘t1′  
hbase&gt; count ‘t1′, INTERVAL =&gt; <span class="hljs-number">100000</span>  
hbase&gt; count ‘t1′, CACHE =&gt; <span class="hljs-number">1000</span>  
hbase&gt; count ‘t1′, INTERVAL =&gt; <span class="hljs-number">10</span>, CACHE =&gt; <span class="hljs-number">1000</span>  </code></pre>

<p>其中，INTERVAL为统计的行数间隔，默认为1000，CACHE为统计的数据缓存。这种方式效率很低，如果表行数很大的话不建议采用这种方式。</p>

<hr>



<h2 id="查询操作">查询操作</h2>

<p>1.scan ‘表名’ #查看所有数据,或带上LIMIT=&gt;10查看随机的10条数据。</p>

<p>2.get是根据主键查看一列 <br>
get ‘表名’,’主键’[，’列簇名’][，’列簇名：列名’]</p>

<p>过滤查询表的内容 <br>
1.FirstKeyOnlyFilter() 得到每一行的列族info的第一个列</p>



<pre class="prettyprint"><code class=" hljs php">hbase(main):<span class="hljs-number">002</span>:<span class="hljs-number">0</span>&gt; scan <span class="hljs-string">'student'</span>,{COLUMNS=&gt;<span class="hljs-string">'info'</span>,FILTER=&gt;<span class="hljs-string">"(FirstKeyOnlyFilter())"</span>}</code></pre>

<p>2.查看列簇是info，LIMIT是显示的行数，STARTROW是开始行对应的是主键</p>



<pre class="prettyprint"><code class=" hljs php">hbase(main):<span class="hljs-number">008</span>:<span class="hljs-number">0</span>&gt; scan <span class="hljs-string">'student'</span>,{COLUMNS=&gt;[<span class="hljs-string">'info'</span>],LIMIT=&gt;<span class="hljs-number">4</span>,STARTROW=&gt;<span class="hljs-string">'200977100710'</span>}</code></pre>

<p>3.查看列簇是info，name</p>



<pre class="prettyprint"><code class=" hljs php">hbase(main):<span class="hljs-number">007</span>:<span class="hljs-number">0</span>&gt; scan <span class="hljs-string">'student'</span>,{COLUMNS=&gt;[<span class="hljs-string">'info'</span>,<span class="hljs-string">'name'</span>],LIMIT=&gt;<span class="hljs-number">4</span>,STARTROW=&gt;<span class="hljs-string">'200977100710'</span>}</code></pre>

<p>10、修改表结构</p>

<p>代码如下:</p>



<pre class="prettyprint"><code class=" hljs php">disable‘scores<span class="hljs-string">'
alter ‘scores'</span>,NAME=&gt;<span class="hljs-string">'info'</span>
enable‘scores<span class="hljs-string">'</span></code></pre>

<p>alter命令使用如下（如果无法成功的版本，需要先通用表disable）： <br>
a、改变或添加一个列族：</p>



<pre class="prettyprint"><code class=" hljs php">hbase&gt;alter ‘t1′, NAME =&gt; ‘f1′, VERSIONS =&gt; <span class="hljs-number">5</span></code></pre>

<p>b、删除一个列族： <br>
代码如下:</p>



<pre class="prettyprint"><code class=" hljs oxygene">hbase&gt;alter ‘t1′, NAME =&gt; ‘f1′, <span class="hljs-function"><span class="hljs-keyword">METHOD</span> =&gt; ‘<span class="hljs-title">delete</span>'
<span class="hljs-title">hbase</span>&gt;<span class="hljs-title">alter</span> ‘<span class="hljs-title">t1</span>′, ‘<span class="hljs-title">delete</span>' =&gt; ‘<span class="hljs-title">f1</span>′</span></code></pre>

<p>c、也可以修改表属性如MAX_FILESIZE</p>



<pre class="prettyprint"><code class=" hljs oxygene">MEMSTORE_FLUSHSIZE,<span class="hljs-keyword">READONLY</span>,和 DEFERRED_LOG_FLUSH：
hbase&gt;alter ‘t1′, <span class="hljs-function"><span class="hljs-keyword">METHOD</span> =&gt; ‘<span class="hljs-title">table_att</span>', <span class="hljs-title">MAX_FILESIZE</span> =&gt; '134217728′</span></code></pre>

<p>总结：HBase的shell操作，一个大概顺序就是操作关键词后跟表名，行名，列名这样的一个顺序，如果有其他条件再用花括号加上。</p>

<p>参考文档：<a href="http://www.yiibai.com/hbase/hbase_architecture.html" rel="nofollow">http://www.yiibai.com/hbase/hbase_architecture.html</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>