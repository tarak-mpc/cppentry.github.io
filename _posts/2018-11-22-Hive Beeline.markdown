---
layout:     post
title:      Hive Beeline
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，如需转载，请注明出处。					https://blog.csdn.net/Post_Yuan/article/details/61197727				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>Beeline是Hive 0.11引入的新的交互式CLI，基于SQLLine，可以作为Hive JDBC Client端访问Hive Server2，启动一个Beeline就是维护一个session。 <br>
Hive Server1中我们习惯了用Hive CLI，方式如下，</p>



<pre class="prettyprint"><code class=" hljs vhdl">[trafodion@n12 udr]$ hive
OpenJDK <span class="hljs-number">64</span>-<span class="hljs-typename">Bit</span> Server VM warning: ignoring option MaxPermSize=<span class="hljs-number">512</span>M; support was removed <span class="hljs-keyword">in</span> <span class="hljs-number">8.0</span>
OpenJDK <span class="hljs-number">64</span>-<span class="hljs-typename">Bit</span> Server VM warning: Using incremental CMS <span class="hljs-keyword">is</span> deprecated <span class="hljs-keyword">and</span> will likely be removed <span class="hljs-keyword">in</span> a future <span class="hljs-keyword">release</span>
<span class="hljs-number">17</span>/<span class="hljs-number">03</span>/<span class="hljs-number">10</span> <span class="hljs-number">15</span>:<span class="hljs-number">20</span>:<span class="hljs-number">22</span> WARN mapreduce.TableMapReduceUtil: The hbase-prefix-tree module jar containing PrefixTreeCodec <span class="hljs-keyword">is</span> <span class="hljs-keyword">not</span> present.  Continuing without it.
OpenJDK <span class="hljs-number">64</span>-<span class="hljs-typename">Bit</span> Server VM warning: ignoring option MaxPermSize=<span class="hljs-number">512</span>M; support was removed <span class="hljs-keyword">in</span> <span class="hljs-number">8.0</span>

Logging initialized using <span class="hljs-keyword">configuration</span> <span class="hljs-keyword">in</span> jar:<span class="hljs-keyword">file</span>:/opt/cloudera/parcels/CDH-<span class="hljs-number">5.7</span><span class="hljs-number">.2</span>-<span class="hljs-number">1.</span>cdh5<span class="hljs-number">.7</span><span class="hljs-number">.2</span>.p0<span class="hljs-number">.18</span>/jars/hive-common-<span class="hljs-number">1.1</span><span class="hljs-number">.0</span>-cdh5<span class="hljs-number">.7</span><span class="hljs-number">.2</span>.jar!/hive-log4j.properties
WARNING: Hive CLI <span class="hljs-keyword">is</span> deprecated <span class="hljs-keyword">and</span> migration <span class="hljs-keyword">to</span> Beeline <span class="hljs-keyword">is</span> recommended.
hive&gt;
</code></pre>

<p>而用Beeline连接Hive则是以下这样，</p>



<pre class="prettyprint"><code class=" hljs oxygene">[trafodion@n12 udr]$ beeline
OpenJDK <span class="hljs-number">64</span>-Bit Server VM warning: ignoring option MaxPermSize=<span class="hljs-number">512</span>M; support was removed <span class="hljs-keyword">in</span> <span class="hljs-number">8.0</span>
OpenJDK <span class="hljs-number">64</span>-Bit Server VM warning: <span class="hljs-keyword">Using</span> incremental CMS <span class="hljs-keyword">is</span> <span class="hljs-keyword">deprecated</span> <span class="hljs-keyword">and</span> will likely be removed <span class="hljs-keyword">in</span> a <span class="hljs-keyword">future</span> release
<span class="hljs-number">17</span>/<span class="hljs-number">03</span>/<span class="hljs-number">10</span> <span class="hljs-number">15</span>:<span class="hljs-number">14</span>:<span class="hljs-number">30</span> WARN mapreduce.TableMapReduceUtil: The hbase-prefix-tree <span class="hljs-keyword">module</span> jar containing PrefixTreeCodec <span class="hljs-keyword">is</span> <span class="hljs-keyword">not</span> present.  Continuing without it.
OpenJDK <span class="hljs-number">64</span>-Bit Server VM warning: ignoring option MaxPermSize=<span class="hljs-number">512</span>M; support was removed <span class="hljs-keyword">in</span> <span class="hljs-number">8.0</span>
Beeline version <span class="hljs-number">1.1</span>.<span class="hljs-number">0</span>-cdh5.<span class="hljs-number">7.2</span> <span class="hljs-keyword">by</span> Apache Hive
beeline&gt; !connect jdbc:hive2:<span class="hljs-comment">//n11:10000/default</span>
scan complete <span class="hljs-keyword">in</span> <span class="hljs-number">3</span>ms
Connecting <span class="hljs-keyword">to</span> jdbc:hive2:<span class="hljs-comment">//n11:10000/default</span>
Enter username <span class="hljs-keyword">for</span> jdbc:hive2:<span class="hljs-comment">//n11:10000/default: hive</span>
Enter password <span class="hljs-keyword">for</span> jdbc:hive2:<span class="hljs-comment">//n11:10000/default: ****</span>
Connected <span class="hljs-keyword">to</span>: Apache Hive (version <span class="hljs-number">1.1</span>.<span class="hljs-number">0</span>-cdh5.<span class="hljs-number">7.2</span>)
Driver: Hive JDBC (version <span class="hljs-number">1.1</span>.<span class="hljs-number">0</span>-cdh5.<span class="hljs-number">7.2</span>)
Transaction isolation: TRANSACTION_REPEATABLE_READ
<span class="hljs-number">0</span>: jdbc:hive2:<span class="hljs-comment">//n11:10000/default&gt;</span>
</code></pre>

<p>上面的jdbc:hive2://n11:10000/default，即是Hive Connection URL，n11为Hive Server2节点的IP，10000为Hive Server2默认的端口号，default为Hive默认的数据库名称。 <br>
关于Beeline命令行的使用，请参考：<a href="https://cwiki.apache.org//confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-BeelineExample" rel="nofollow">https://cwiki.apache.org//confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-BeelineExample</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>