---
layout:     post
title:      HBase  Shell使用记录
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_23146763/article/details/80452659				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="1以非交互式运行hbase-shell">1.以非交互式运行hbase shell</h2>

<p>用法：hbase shell -n <br>
务必转义HBase命令中的字符，否则shell将会解释这些字符</p>

<p>例子</p>

<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-built_in">echo</span> <span class="hljs-string">"describe 'test'"</span>|hbase shell -n</code></pre>



<h2 id="2文件方式运行hbase-shell命令">2.文件方式运行hbase shell命令</h2>

<pre class="prettyprint"><code class=" hljs livecodeserver">hbase <span class="hljs-built_in">shell</span> filename</code></pre>

<p>将路径作为命令的唯一参数传递给命令文件hbase shell。每个命令都会执行并显示其输出。如果您未exit在脚本中包含该命令，则会返回到HBase shell提示符。没有办法以编程方式检查每个单独的命令是否成功或失败。此外，尽管您看到每个命令的输出，但命令本身并未回显到屏幕，因此可能难以将命令与其输出对齐</p>



<h2 id="3将vm选项传递给shell">3.将VM选项传递给Shell</h2>

<p>通过使用环境变量HBASE_SHELL_OPTS</p>

<pre class="prettyprint"><code class=" hljs bash">HBASE_SHELL_OPTS=<span class="hljs-string">"-verbose:gc -XX:+PrintGCApplicationStoppedTime -XX:+PrintGCDateStamps -XX:+PrintGCDetails -Xloggc:<span class="hljs-variable">$HBASE_HOME</span>/logs/gc-hbase.log"</span> ./bin/hbase shell</code></pre>



<h2 id="4表变量">4.表变量</h2>

<p>HBase 0.95引入了将表分配给jruby变量的功能。表引用可以用来执行数据读写操作，比如放入，扫描，以及管理功能，如禁用，删除，描述表等。</p>

<p>将该表分配给一个变量并在jruby shell代码中使用变量操作</p>

<pre class="prettyprint"><code class=" hljs coffeescript">hbase(main):<span class="hljs-number">001</span>:<span class="hljs-number">0</span>&gt; t=create <span class="hljs-string">'lishengping_test2'</span>,<span class="hljs-string">'base_info'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">1.3820</span> seconds<span class="hljs-function">

=&gt;</span> <span class="hljs-attribute">Hbase</span>::Table - lishengping_test2
hbase(main):<span class="hljs-number">002</span>:<span class="hljs-number">0</span>&gt; t.put <span class="hljs-string">'20180525'</span>,<span class="hljs-string">'base_info:address'</span>,<span class="hljs-string">'touna'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.2270</span> seconds
hbase(main):<span class="hljs-number">003</span>:<span class="hljs-number">0</span>&gt; t.scan
ROW                                          COLUMN+CELL                                                                                                                    
 <span class="hljs-number">20180525</span>                                    column=<span class="hljs-attribute">base_info</span>:address, timestamp=<span class="hljs-number">1527232907078</span>, value=touna                                                                 
<span class="hljs-number">1</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0730</span> seconds
hbase(main):<span class="hljs-number">004</span>:<span class="hljs-number">0</span>&gt; t.describe
Table lishengping_test2 <span class="hljs-keyword">is</span> ENABLED                                                                                                                                          
lishengping_test2                                                                                                                                                           
COLUMN FAMILIES DESCRIPTION                                                                                                                                                 
{NAME<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'base_info'</span>, BLOOMFILTER<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'ROW'</span>, VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'1'</span>, IN_MEMORY<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'false'</span>, KEEP_DELETED_CELLS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FALSE'</span>, DATA_BLOCK_ENCODING<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, TTL<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FOREVER'</span>, COMPRESSI
ON<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, MIN_VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, BLOCKCACHE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'true'</span>, BLOCKSIZE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'65536'</span>, REPLICATION_SCOPE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>}                                                                    
<span class="hljs-number">1</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0420</span> seconds
hbase(main):<span class="hljs-number">005</span>:<span class="hljs-number">0</span>&gt; t.disable
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">2.3950</span> seconds
hbase(main):<span class="hljs-number">006</span>:<span class="hljs-number">0</span>&gt; t.drop
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">1.2860</span> seconds
</code></pre>

<p>如果该表已被创建，则可以使用get_table方法将一个表分配给一个变量：</p>



<pre class="prettyprint"><code class=" hljs scss"><span class="hljs-function">hbase(main)</span><span class="hljs-value">:<span class="hljs-number">008</span>:<span class="hljs-number">0</span>&gt; t=get_table <span class="hljs-string">'lishengping_test'</span></span></code></pre>



<h2 id="5调试">5.调试</h2>

<p><strong>shell调试开关</strong> <br>
您可以在shell中设置一个调试开关，以查看更多输出 - 例如，运行命令时出现更多的异常堆栈跟踪：</p>

<pre class="prettyprint"><code class=" hljs lua">hbase&gt; <span class="hljs-built_in">debug</span> &lt;RETURN&gt;</code></pre>

<p><strong>DEBUG日志级别</strong> <br>
要在shell中启用DEBUG级日志记录，请使用该-d选项启动它。</p>



<pre class="prettyprint"><code class=" hljs bash">$ ./bin/hbase shell <span class="hljs-operator">-d</span></code></pre>



<h2 id="计数">计数</h2>

<p>Count命令返回表中的行数。配置正确的CACHE时速度非常快</p>



<pre class="prettyprint"><code class=" hljs php">hbase&gt; count <span class="hljs-string">'&lt;tablename&gt;'</span>, CACHE =&gt; <span class="hljs-number">1000</span></code></pre>

<p>上述计数一次取1000行。如果行很大，请将CACHE设置为较低。默认是每次读取一行。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>