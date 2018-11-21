---
layout:     post
title:      HBase Shell 基本的增删改查CRUD操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主John Lau原创文章，未经博主允许不得转载					https://blog.csdn.net/GreatElite/article/details/18133191				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20140111095132921?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvR3JlYXRFbGl0ZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></span></h2>
<h2><span style="font-size:18px;">1.   1 HBase 命令模式</span></h2>
<p><span style="font-size:18px;">HBase shell 命令提供了一个TAB键完成指令。</span></p>
<p><span style="font-size:18px;">$ <strong>hbase shell</strong></span></p>
<p><span style="font-size:18px;">进入shell 命令模式后，可以使用HBase shell 命令来操作数据。</span></p>
<p><span style="font-size:18px;">将一个hbase日志日期'08/08/16 20:56:29'转化为一个timestamp,可以这样得到:</span></p>
<p></p>
<pre><code class="language-plain"><span style="font-size:18px;">hbase(main):021:0&gt; import java.text.SimpleDateFormat
hbase(main):022:0&gt; import java.text.ParsePosition
hbase(main):023:0&gt;SimpleDateFormat.new("yy/MM/ddHH:mm:ss").parse("08/08/16 20:56:29",ParsePosition.new(0)).getTime()</span></code></pre><span style="font-size:18px;"><br><br></span>
<p></p>
<p></p>
<pre><code class="language-plain"><span style="font-size:18px;"> =&gt; 1218920189000</span></code></pre><span style="font-size:18px;"><br><br></span>
<p></p>
<p><span style="font-size:18px;">反之，通过类似操作也可以得到。</span></p>
<p></p>
<pre><code class="language-plain"><span style="font-size:18px;">hbase(main):021:0&gt; import java.util.Date
hbase(main):022:0&gt; Date.new(1218920189000).toString()</span></code></pre><span style="font-size:18px;"><br><br></span>
<p></p>
<p></p>
<pre><code class="language-plain">=&gt;"Sat Aug 16 20:56:29 UTC 2008"</code></pre><br><br><p></p>
<p><span style="font-size:18px;">1.2 常用的HBase Shell命令</span></p>
<p><span style="font-size:18px;">1.2.1创建表</span></p>
<p><span style="font-size:18px;">创建一个表car, 命名一个column family 为 exp(expensive 的缩写)。</span></p>
<p></p>
<pre><code class="language-plain"><span style="font-size:18px;">hbase(main):020:0&gt;create 'car', 'exp'</span></code></pre><span style="font-size:18px;"><br><br></span>
<p></p>
<p><span style="font-size:18px;">1.2.2插入数据</span></p>
<p><span style="font-size:18px;">插入三个数据，制造商、模型和生产日期。具体的命令如下：</span></p>
<p></p>
<pre><code class="language-plain"><span style="font-size:18px;">put 'car','row1','exp:make', 'bmw'
put 'car', 'row1', 'exp:model', ' 5 series'
put'car', 'row1', 'exp:year', '2012-12-20'
 
put 'car', 'row2', 'exp:make', 'benz'
put 'car', 'row2', 'exp:model', 'c200'
put 'car', 'row2', 'exp:year', '2013'</span></code></pre><span style="font-size:18px;"><br><br></span>
<p></p>
<p><span style="font-size:18px;">1.2.3查询</span></p>
<p><span style="font-size:18px;">    查询所有指定的结果，使用scan加上具体的修饰语句。</span></p>
<p></p>
<pre><code class="language-plain"><span style="font-size:18px;">             hbase(main):043:0&gt; scan 'car', {COLUMNS =&gt;['exp:make']}</span></code></pre><span style="font-size:18px;"><br><br></span>
<p></p>
<p><span style="font-size:18px;">如果需要限定只返回一个行的数据，可以这样实现：</span></p>
<p></p>
<pre><code class="language-plain"><span style="font-size:18px;">hbase(main):044:0&gt; scan 'car', {COLUMNS=&gt; ['exp:make'], LIMIT =&gt; 1}</span></code></pre><span style="font-size:18px;"><br><br></span>
<p></p>
<p><span style="font-size:18px;">NOTES： hbase shell 的命令区分大小写，如<span style="color:#0070C0;">COLUMNS</span><span style="color:#0070C0;">，</span><span style="color:#0070C0;"> LIMIT</span>等关键字。</span></p>
<p><span style="font-size:18px;">获取到某一行，使用get命令，具体指令如下：</span></p>
<p></p>
<pre><code class="language-plain"><span style="font-size:18px;">hbase(main):045:0&gt; get 'car', 'row1'
COLUMN                CELL                                                     
 exp:make             timestamp=1363919137232,value=bmw                       
 exp:model            timestamp=1363919183289, value= 5series                 
 exp:year             timestamp=1363919193544,value=2012-12-20</span></code></pre><span style="font-size:18px;"><br><br></span>
<p></p>
<p><span style="font-size:18px;">1.2.4删除</span></p>
<p><span style="font-size:18px;">删除cell数据，指定到具体的column名称。</span></p>
<p></p>
<pre><code class="language-plain"><span style="font-size:18px;">hbase(main):047:0&gt;delete 'car', 'row1', 'exp:year'</span></code></pre><span style="font-size:18px;"><br><br></span>
<p></p>
<p><span style="font-size:18px;">检测一下具体的数据内容，可以看到数据已经删除。</span></p>
<p></p>
<pre><code class="language-plain"><span style="font-size:18px;">hbase(main):048:0&gt; get 'car' , 'row1'
COLUMN                CELL                                                     
 exp:make             timestamp=1363919137232,value=bmw                       
 exp:model            timestamp=1363919183289, value= 5series</span></code></pre><span style="font-size:18px;"><br><br></span>
<p></p>
<p><span style="font-size:18px;">删除table， 需要运行如下命令：</span></p>
<p></p>
<pre><code class="language-plain"><span style="font-size:18px;">  hbase(main):050:0&gt;disable 'car'
0 row(s) in 2.0460 seconds
    hbase(main):052:0&gt; drop 'car'
0 row(s) in 1.0410 seconds</span></code></pre><span style="font-size:18px;"><br><br></span>
<p></p>
<p><span style="font-size:18px;">查看表单，发现确实删除了。</span></p>
<p></p>
<pre><code class="language-plain"><span style="font-size:18px;">hbase(main):053:0&gt; list
TABLE                                                                          
mytable                                                                        
1 row(s) in 0.0100 seconds</span></code></pre><span style="font-size:18px;"><br><br></span>
<p></p>
<p><span style="font-size:18px;">当不清楚某个命令是，使用help帮助命令.</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p>ＮＯＴＥＳ：<span style="font-family:Georgia, Utopia, 'Palatino Linotype', Palatino, serif;font-size:18px;line-height:26px;">转载请注明出处，谢谢！</span></p>
            </div>
                </div>