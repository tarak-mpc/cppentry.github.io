---
layout:     post
title:      Hive基础二（join原理和机制，join的几种类型，数据倾斜简单处理）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/login_sonata/article/details/75000766				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>相关链接： <br>
<a href="http://blog.csdn.net/login_sonata/article/details/74894685" rel="nofollow">Hive基础一（数据库，表，分区表，视图，导入导出数据）</a> <br>
<a href="http://blog.csdn.net/login_sonata/article/details/75000766" rel="nofollow">Hive基础二（join原理和机制，join的几种类型，数据倾斜简单处理）</a> <br>
 <a href="http://blog.csdn.net/login_sonata/article/details/75092590" rel="nofollow">Hive基础三（查询中常用的语法）</a></p>

<p><strong>【注意】有些语句会报错，这是因为hive版本问题，比如有些join中的outer不能省略，等等。</strong></p>

<h2 id="一hive中join的原理和机制">一，Hive中join的原理和机制</h2>

<p>Hive中的Join可分为Common Join（Reduce阶段完成join）和Map Join（Map阶段完成join）。</p>

<ol>
<li><p><strong>Hive Common Join</strong>  <br>
如果不指定MapJoin或者不符合MapJoin的条件，那么Hive解析器会默认把执行Common Join，即在Reduce阶段完成join。整个过程包含Map、Shuffle、Reduce阶段。</p>

<ul><li><strong>Map阶段</strong> <br>
读取源表的数据，Map输出时候以Join on条件中的列为key，如果Join有多个关联键，则以这些关联键的组合作为key；Map输出的value为join之后所关心的(select或者where中需要用到的)列，同时在value中还会包含表的Tag信息，用于标明此value对应哪个表。</li>
<li><strong>Shuffle阶段</strong> <br>
根据key的值进行hash，并将key/value按照hash值推送至不同的reduce中，这样确保两个表中相同的key位于同一个reduce中。</li>
<li><strong>Reduce阶段</strong>  <br>
根据key的值完成join操作，期间通过Tag来识别不同表中的数据。 </li></ul>

<p>以下面的HQL为例，图解其过程：</p>

<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">SELECT</span> a.id,a.dept,b.age 
<span class="hljs-keyword">FROM</span> a <span class="hljs-keyword">join</span> b 
<span class="hljs-keyword">ON</span> (a.id = b.id);</span></code></pre>

<p><img src="https://img-blog.csdn.net/20170711233618078?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbG9naW5fc29uYXRh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="join原理" title=""></p></li>
<li><p><strong>Hive Map Join</strong> <br>
MapJoin通常用于一个很小的表和一个大表进行join的场景，具体小表有多小，由参数hive.mapjoin.smalltable.filesize来决定，默认值为25M。满足条件的话Hive在执行时候会自动转化为MapJoin，或使用hint提示 /*+ mapjoin(table) */执行MapJoin。 <br>
<img src="https://img-blog.csdn.net/20170711234033872?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbG9naW5fc29uYXRh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="mapjoin" title=""></p>

<p>如上图中的流程，<strong>首先Task A</strong>在客户端本地执行，负责扫描小表b的数据，将其转换成一个HashTable的数据结构，并写入本地的文件中，之后将该文件加载到DistributeCache中。 <br>
<strong>接下来的Task B</strong>任务是一个没有Reduce的MapReduce，启动MapTasks扫描大表a，在Map阶段，根据a的每一条记录去和DistributeCache中b表对应的HashTable关联，并直接输出结果，因为没有Reduce，所以有多少个Map Task，就有多少个结果文件。 <br>
<strong>注意</strong>：Map JOIN不适合FULL/RIGHT OUTER JOIN。</p></li>
</ol>



<h2 id="二join的几种类型">二，join的几种类型</h2>

<p>Hive中除了支持和传统数据库中一样的内关联（JOIN）、左关联（LEFT JOIN）、右关联（RIGHT JOIN）、全关联（FULL JOIN），还支持左半关联（LEFT SEMI JOIN）。 <br>
<strong>注意</strong>：Hive中Join的关联键必须在ON()中指定，不能在Where中指定。 <br>
数据准备：</p>



<pre class="prettyprint"><code class=" hljs vbscript">hive&gt; desc lxw1234_a;
OK
id                      <span class="hljs-built_in">string</span>                                      
name                    <span class="hljs-built_in">string</span>                                      
<span class="hljs-built_in">Time</span> taken: <span class="hljs-number">0.094</span> seconds, Fetched: <span class="hljs-number">2</span> row(s)
hive&gt; <span class="hljs-keyword">select</span> * from lxw1234_a;
OK
<span class="hljs-number">1</span>       zhangsan
<span class="hljs-number">2</span>       lisi
<span class="hljs-number">3</span>       wangwu
<span class="hljs-built_in">Time</span> taken: <span class="hljs-number">0.116</span> seconds, Fetched: <span class="hljs-number">3</span> row(s)
hive&gt; desc lxw1234_b;
OK
id                      <span class="hljs-built_in">string</span>                                      
age                     <span class="hljs-built_in">int</span>                                         
<span class="hljs-built_in">Time</span> taken: <span class="hljs-number">0.159</span> seconds, Fetched: <span class="hljs-number">2</span> row(s)
hive&gt; <span class="hljs-keyword">select</span> * from lxw1234_b;
OK
<span class="hljs-number">1</span>       <span class="hljs-number">30</span>
<span class="hljs-number">2</span>       <span class="hljs-number">29</span>
<span class="hljs-number">4</span>       <span class="hljs-number">21</span>
<span class="hljs-built_in">Time</span> taken: <span class="hljs-number">0.09</span> seconds, Fetched: <span class="hljs-number">3</span> row(s)
</code></pre>

<ol>
<li><p><strong>内关联（JOIN）</strong> <br>
常规join，类似交集，只显示关联成功的行。</p>

<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">SELECT</span> a.id, a.name, b.age 
<span class="hljs-keyword">FROM</span> lxw1234_a a 
<span class="hljs-keyword">join</span> lxw1234_b b 
<span class="hljs-keyword">ON</span> (a.id = b.id);</span>
<span class="hljs-comment">--执行结果</span>
1       zhangsan        30
2       lisi        29
</code></pre></li>
<li><p><strong>左外关联（LEFT [OUTER] JOIN）</strong> <br>
以LEFT [OUTER] JOIN关键字前面的表作为主表，和其他表进行关联，返回记录和主表的记录数一致，关联不上的字段置为NULL。 <br>
是否指定OUTER关键字，貌似对查询结果无影响。</p>

<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">SELECT</span> a.id, a.name, b.age 
<span class="hljs-keyword">FROM</span> lxw1234_a a 
<span class="hljs-keyword">left</span> <span class="hljs-keyword">join</span> lxw1234_b b 
<span class="hljs-keyword">ON</span> (a.id = b.id);</span>
<span class="hljs-comment">--执行结果：</span>
1   zhangsan   30
2   lisi        29
3   wangwu    NULL
</code></pre></li>
<li><p><strong>右外关联（RIGHT [OUTER] JOIN）</strong> <br>
和左外关联相反，以RIGTH [OUTER] JOIN关键词后面的表作为主表，和前面的表做关联，返回记录数和主表一致，关联不上的字段为NULL。 <br>
是否指定OUTER关键字，貌似对查询结果无影响。</p>

<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">SELECT</span> a.id, a.name, b.age 
<span class="hljs-keyword">FROM</span> lxw1234_a a 
<span class="hljs-keyword">RIGHT</span> <span class="hljs-keyword">OUTER</span> <span class="hljs-keyword">JOIN</span> lxw1234_b b 
<span class="hljs-keyword">ON</span> (a.id = b.id);</span>
<span class="hljs-comment">--执行结果：</span>
1          zhangsan        30
2          lisi        29
NULL       NULL        21</code></pre></li>
<li><p><strong>全外关联（FULL [OUTER] JOIN）</strong> <br>
以两个表的记录为基准，返回两个表的所有记录，类似并集，关联不上的字段为NULL。 <br>
是否指定OUTER关键字，貌似对查询结果无影响。</p>

<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">SELECT</span> a.id, a.name, b.age 
<span class="hljs-keyword">FROM</span> lxw1234_a a 
<span class="hljs-keyword">FULL</span> <span class="hljs-keyword">OUTER</span> <span class="hljs-keyword">JOIN</span> lxw1234_b b 
<span class="hljs-keyword">ON</span> (a.id = b.id);</span>
<span class="hljs-comment">--执行结果：</span>
1       zhangsan            30
2       lisi            29
3       wangwu          NULL
NULL    NULL            21
</code></pre></li>
<li><p><strong>左半连接（LEFT SEMI JOIN）</strong> <br>
以LEFT SEMI JOIN关键字前面的表为主表，返回主表的KEY也在副表中的记录。相当于IN或EXISTS的作用。左半连接的<strong>限制</strong>是右侧的表只能出现在ON子句中，不能出现在WHERE或者SELECT子句中。 <br>
<strong>注意</strong>，left semi join和join并不是等价的，比如当左表1条数据可以关联右表2条数据时，此时left semi join只显示1条数据（因为只要左表的key在右表存在就行，不用管几条），join却显示两条数据（关联到的都显示），好好体会一下。</p>

<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">SELECT</span> a.id, a.name 
<span class="hljs-keyword">FROM</span> lxw1234_a a 
<span class="hljs-keyword">LEFT</span> SEMI <span class="hljs-keyword">JOIN</span> lxw1234_b b 
<span class="hljs-keyword">ON</span> (a.id = b.id);</span>
<span class="hljs-comment">--执行结果：</span>
1       zhangsan
2       lisi
<span class="hljs-comment">--等价于：</span>
<span class="hljs-operator"><span class="hljs-keyword">SELECT</span> a.id, a.name 
<span class="hljs-keyword">FROM</span> lxw1234_a a 
<span class="hljs-keyword">WHERE</span> a.id <span class="hljs-keyword">IN</span> (<span class="hljs-keyword">SELECT</span> id <span class="hljs-keyword">FROM</span> lxw1234_b);</span></code></pre></li>
</ol>



<h2 id="三join语句需要注意的地方">三，join语句需要注意的地方</h2>

<ol>
<li><p>首先是Hive中的连接查询<strong>只支持相等连接</strong>而不支持不等连接查询：</p>

<pre class="prettyprint"><code class=" hljs oxygene"><span class="hljs-comment">//有效的连接查询，相等连接查询  </span>
<span class="hljs-keyword">SELECT</span> a.* <span class="hljs-keyword">FROM</span> a <span class="hljs-keyword">JOIN</span> b <span class="hljs-keyword">ON</span> (a.id = b.id <span class="hljs-keyword">AND</span> a.department = b.department)  
<span class="hljs-comment">//无效的连接查询，Hive不支持不等连接查询  </span>
<span class="hljs-keyword">SELECT</span> a.* <span class="hljs-keyword">FROM</span> a <span class="hljs-keyword">JOIN</span> b <span class="hljs-keyword">ON</span> (a.id &lt;&gt; b.id)   </code></pre></li>
<li><p>如果每个表都只使用<strong>相同</strong>的列join连接，Hive将只生成一个map/reduce作业；如果一个表使用了<strong>两个以上</strong>的字段，则会生成2个以上的mr任务：</p>

<pre class="prettyprint"><code class=" hljs sql">//由于join子句中只使用了表b的key1列，该查询转换为一个作业  
<span class="hljs-operator"><span class="hljs-keyword">SELECT</span> a.val, b.val, c.val <span class="hljs-keyword">FROM</span> a <span class="hljs-keyword">JOIN</span> b <span class="hljs-keyword">ON</span> (a.<span class="hljs-keyword">key</span> = b.key1) <span class="hljs-keyword">JOIN</span> c <span class="hljs-keyword">ON</span> (c.<span class="hljs-keyword">key</span> = b.key1)  
//由于表b的key1列用在第一个<span class="hljs-keyword">join</span>子句中，key2列用在第二个<span class="hljs-keyword">join</span>子句中，该查询被转换为两个作业，
//第一个作业执行表a和b的连接查询，第二个作业将第一个作业的结果与第二个<span class="hljs-keyword">join</span>子句进行连接查询  
<span class="hljs-keyword">SELECT</span> a.val, b.val, c.val <span class="hljs-keyword">FROM</span> a <span class="hljs-keyword">JOIN</span> b <span class="hljs-keyword">ON</span> (a.<span class="hljs-keyword">key</span> = b.key1) <span class="hljs-keyword">JOIN</span> c <span class="hljs-keyword">ON</span> (c.<span class="hljs-keyword">key</span> = b.key2)  </span></code></pre></li>
<li><p>Join连接的顺序是<strong>不可以</strong>交换的，无论是LEFT还是RIGHT连接都是左结合的。</p></li>
</ol>



<h2 id="四hive数据倾斜的简单处理">四，hive数据倾斜的简单处理</h2>

<blockquote>
  <p>• 数据倾斜症状：  <br>
  任务长时间维持在99%（或100%）;  <br>
  查看任务监控页面，发现只有少量（1个或几个）reduce子任务未完成； <br>
  本地读写数据量很大。  <br>
  • 原因：  <br>
  key分布不均匀；  <br>
  业务数据本身特点。 <br>
  • 导致数据倾斜的操作：  <br>
  GROUP BY, COUNT DISTINCT()，join</p>
</blockquote>

<p>这里列出几个常用解决办法：</p>

<ol>
<li><p><strong>参数hive.groupby.skewindata = true</strong> <br>
似乎是解决数据倾斜的万能钥匙，查询计划会有两个 MR Job，<strong>第一个</strong> MR Job 中，Map 的输出结果集合会<strong>随机</strong>分布到 Reduce 中，每个Reduce做部分聚合操作，并输出结果，这样处理的结果是相同的 Group By Key 有可能被分发到不同的 Reduce中，从而达到负载均衡的目的；<strong>第二个</strong> MR Job 再根据预处理的数据结果按照 Group By Key 分布到 Reduce中（这个过程可以保证相同的 Group By Key 被分布到同一个 Reduce 中），最后完成最终的聚合操作。</p></li>
<li><p><strong>使用COUNT DISTINCT造成的数据倾斜</strong></p>

<ul><li><p>如果<strong>某一个值的记录特别多</strong>，可以先把该值过滤掉，在最后单独处理。比如某一天的IMEI值为’lxw1234’的特别多，当我要统计总的IMEI数，可以先统计不为’lxw1234’的，之后再加1。</p>

<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">SELECT</span> <span class="hljs-keyword">CAST</span>(<span class="hljs-aggregate">COUNT</span>(<span class="hljs-keyword">DISTINCT</span> imei)+<span class="hljs-number">1</span> <span class="hljs-keyword">AS</span> bigint)
<span class="hljs-keyword">FROM</span> lxw1234 <span class="hljs-keyword">where</span> pt = <span class="hljs-string">'2012-05-28'</span>
<span class="hljs-keyword">AND</span> imei &lt;&gt; <span class="hljs-string">'lxw1234'</span> ;</span></code></pre></li>
<li><p>数据量大的情况下，由于COUNT DISTINCT操作需要用一个Reduce Task来完成，这一个Reduce需要处理的数据量太大，就会导致整个Job很难完成，一般COUNT DISTINCT使用<strong>先GROUP BY再COUNT</strong>的方式替换：</p>

<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">SELECT</span> <span class="hljs-keyword">day</span>, <span class="hljs-aggregate">COUNT</span>(<span class="hljs-keyword">DISTINCT</span> id) <span class="hljs-keyword">AS</span> uv
<span class="hljs-keyword">FROM</span> lxw1234
<span class="hljs-keyword">GROUP</span> <span class="hljs-keyword">BY</span> <span class="hljs-keyword">day</span>;</span>
<span class="hljs-comment">--可以转换成：</span>
<span class="hljs-operator"><span class="hljs-keyword">SELECT</span> <span class="hljs-keyword">day</span>, <span class="hljs-aggregate">COUNT</span>(id) <span class="hljs-keyword">AS</span> uv
<span class="hljs-keyword">FROM</span> (<span class="hljs-keyword">SELECT</span> <span class="hljs-keyword">day</span>,id <span class="hljs-keyword">FROM</span> lxw1234 <span class="hljs-keyword">GROUP</span> <span class="hljs-keyword">BY</span> <span class="hljs-keyword">day</span>,id) a
<span class="hljs-keyword">GROUP</span> <span class="hljs-keyword">BY</span> <span class="hljs-keyword">day</span>;</span></code></pre>

<p>虽然会多用一个Job来完成，但在数据量大的情况下，这个绝对是值得的。</p></li></ul></li>
<li><p><strong>使用JOIN引起的数据倾斜</strong></p>

<ul><li><strong>表连接顺序优化</strong>： <br>
多表连接时，尽量小表在前，大表在后。因为JOIN前一阶段生成的数据会存在于Reducer的buffer中，小表数据量小，内存开销就小，与后面的大表进行连接时，只需要从buffer中读取缓存的Key，与大表中的指定Key进行连接，速度会更快，也可能避免内存缓冲区溢出。</li>
<li><p><strong>where优化</strong>： <br>
Join出现在WHERR子句之前。因此如果想在join前进行过滤，限制条件应该出现在JOIN子句中，而不是where中：</p>

<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">SELECT</span> a.val, b.val <span class="hljs-keyword">FROM</span> a <span class="hljs-keyword">LEFT</span> <span class="hljs-keyword">OUTER</span> <span class="hljs-keyword">JOIN</span> b 
<span class="hljs-keyword">ON</span> (a.<span class="hljs-keyword">key</span>=b.<span class="hljs-keyword">key</span>)  
<span class="hljs-keyword">WHERE</span> a.ds=<span class="hljs-string">'2009-07-07'</span> <span class="hljs-keyword">AND</span> b.ds=<span class="hljs-string">'2009-07-07'</span>;</span>
//当该左外连接在a中发现key而在b中没有发现key时，b中的列将为null，包括分区列ds，后边的where就没有用了。
//下面的语句将会提前根据条件过滤：
<span class="hljs-operator"><span class="hljs-keyword">SELECT</span> a.val, b.val <span class="hljs-keyword">FROM</span> a <span class="hljs-keyword">LEFT</span> <span class="hljs-keyword">OUTER</span> <span class="hljs-keyword">JOIN</span> b  
<span class="hljs-keyword">ON</span> (a.<span class="hljs-keyword">key</span>=b.<span class="hljs-keyword">key</span> <span class="hljs-keyword">AND</span> b.ds=<span class="hljs-string">'2009-07-07'</span> <span class="hljs-keyword">AND</span> a.ds=<span class="hljs-string">'2009-07-07'</span>);</span></code></pre></li>
<li><p><strong>关联键存在大量空值</strong>： <br>
在SQL标准中，任何对NULL的操作(如数值比较，字符串操作等)结果都为NULL。Hive对NULL值的处理与其基本一致，除了JOIN时的<strong>特殊逻辑</strong>：Hive的JOIN中作为JOIN Key的字段比较，NULL=NULL是有意义的，且返回值为True。 <br>
所以需要改写查询手动过滤NULL值的情况，操作如下：</p>

<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">SELECT</span> <span class="hljs-keyword">user</span>.uid,<span class="hljs-aggregate">count</span>(<span class="hljs-keyword">user</span>.uid) <span class="hljs-keyword">FROM</span> class <span class="hljs-keyword">JOIN</span> <span class="hljs-keyword">user</span> 
<span class="hljs-keyword">ON</span>(class.uid = <span class="hljs-keyword">user</span>.uid <span class="hljs-keyword">and</span> class.uid <span class="hljs-keyword">IS</span> <span class="hljs-keyword">NOT</span> <span class="hljs-keyword">NULL</span> <span class="hljs-keyword">and</span> <span class="hljs-keyword">user</span>.uid <span class="hljs-keyword">IS</span> <span class="hljs-keyword">NOT</span> <span class="hljs-keyword">NULL</span>) 
<span class="hljs-keyword">GROUP</span> <span class="hljs-keyword">BY</span> <span class="hljs-keyword">user</span>.uid;</span></code></pre></li>
<li><p><strong>不同数据类型的字段关联</strong>： <br>
转换为同一数据类型之后再做关联。</p></li></ul></li>
</ol>

<p>原文来自：<a href="http://lxw1234.com/archives/2015/07/365.htm" rel="nofollow" target="_blank">http://lxw1234.com/archives/2015/07/365.htm</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>