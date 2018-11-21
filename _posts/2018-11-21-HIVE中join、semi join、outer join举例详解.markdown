---
layout:     post
title:      HIVE中join、semi join、outer join举例详解
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="postTitle" style="font-size:14px;font-weight:bold;">
<a id="cb_post_title_url" class="postTitle2" href="http://www.cnblogs.com/xd502djj/archive/2013/01/18/2866662.html" rel="nofollow" style="color:rgb(255,51,102);">HIVE中join、semi join、outer join举例详解</a></div>
<div id="cnblogs_post_body" style="line-height:1.8;">
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
举例子：</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
hive&gt; select * from zz0; <br>
111111 <br>
222222 <br>
888888 <br>
hive&gt; select * from zz1; <br>
111111 <br>
333333 <br>
444444 <br>
888888</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
hive&gt; select * from zz0 join zz1 on zz0.uid = zz1.uid; <br>
111111  111111 <br>
888888  888888 <br>
hive&gt; select * from zz0 left outer join zz1 on zz0.uid = zz1.uid; <br>
111111  111111 <br>
222222  NULL <br>
888888  888888 <br>
hive&gt; select * from zz0 right outer join zz1 on zz0.uid = zz1.uid; <br>
NULL <br>
111111  111111 <br>
NULL    333333 <br>
NULL    444444 <br>
888888  888888 <br>
hive&gt; select * from zz0 full outer join zz1 on zz0.uid = zz1.uid; <br>
NULL <br>
111111  111111 <br>
222222  NULL <br>
NULL    333333 <br>
NULL    444444 <br>
888888  888888 <br>
hive&gt; select * from zz0 left semi join zz1 on zz0.uid = zz1.uid; <br>
111111  111111 <br>
888888  888888</p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<p style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
 </p>
<h1 class="entry-title" style="font-size:28px;line-height:1.5;font-family:verdana, Arial, Helvetica, sans-serif;">
写好Hive 程序的五个提示</h1>
<div class="entry-content" style="font-family:verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<p>使用<span lang="en-us" xml:lang="en-us">Hive</span>可以高效而又快速地编写复杂的<span lang="en-us" xml:lang="en-us">MapReduce</span>查询逻辑。但是某些情况下，因为不熟悉数据特性，或没有遵循<span lang="en-us" xml:lang="en-us">Hive</span>的优化约定，<span lang="en-us" xml:lang="en-us">Hive</span>计算任务会变得非常低效，甚至无法得到结果。一个”好”的<span lang="en-us" xml:lang="en-us">Hive</span>程序仍然需要对<span lang="en-us" xml:lang="en-us">Hive</span>运行机制有深入的了解。</p>
<p>有一些大家比较熟悉的优化约定包括：<span lang="en-us" xml:lang="en-us">Join</span>中需要将大表写在靠右的位置；尽量使用<span lang="en-us" xml:lang="en-us">UDF</span>而不是<span lang="en-us" xml:lang="en-us">transfrom</span>……诸如此类。下面讨论<span lang="en-us" xml:lang="en-us">5</span>个性能和逻辑相关的问题，帮助你写出更好的<span lang="en-us" xml:lang="en-us">Hive</span>程序。</p>
<h2 style="font-size:21px;line-height:1.5;"><span id="more-622"></span>全排序</h2>
<p><span lang="en-us" xml:lang="en-us">Hive</span>的排序关键字是<span lang="en-us" xml:lang="en-us">SORT BY</span>，它有意区别于传统数据库的<span lang="en-us" xml:lang="en-us">ORDER BY</span>也是为了强调两者的区别–<span lang="en-us" xml:lang="en-us">SORT BY</span>只能在单机范围内排序。考虑以下表定义：</p>
<pre><span lang="en-us" xml:lang="en-us">CREATE TABLE if not exists t_order(</span>

<span lang="en-us" xml:lang="en-us">id int, --</span> 订单编号

<span lang="en-us" xml:lang="en-us">sale_id int, --</span> 销售<span lang="en-us" xml:lang="en-us">ID</span>

<span lang="en-us" xml:lang="en-us">customer_id int, --</span> 客户<span lang="en-us" xml:lang="en-us">ID</span>

<span lang="en-us" xml:lang="en-us">product _id int, --</span> 产品<span lang="en-us" xml:lang="en-us">ID</span>

<span lang="en-us" xml:lang="en-us">amount int --</span> 数量

<span lang="en-us" xml:lang="en-us">) PARTITIONED BY (ds STRING);</span></pre>
<p>在表中查询所有销售记录，并按照销售<span lang="en-us" xml:lang="en-us">ID</span>和数量排序：</p>
<pre><span lang="en-us" xml:lang="en-us">set mapred.reduce.tasks=2;</span>

<span lang="en-us" xml:lang="en-us">Select sale_id, amount from t_order</span>

<span lang="en-us" xml:lang="en-us">Sort by sale_id, amount;</span></pre>
<p>这一查询可能得到非期望的排序。指定的<span lang="en-us" xml:lang="en-us">2</span>个<span lang="en-us" xml:lang="en-us">reducer</span>分发到的数据可能是（各自排序）：</p>
<p><span lang="en-us" xml:lang="en-us">Reducer1</span>：</p>
<pre><span lang="en-us" xml:lang="en-us">Sale_id | amount</span>

<span lang="en-us" xml:lang="en-us">0 | 100</span>

<span lang="en-us" xml:lang="en-us">1 | 30</span>

<span lang="en-us" xml:lang="en-us">1 | 50</span>

<span lang="en-us" xml:lang="en-us">2 | 20</span></pre>
<p><span lang="en-us" xml:lang="en-us">Reducer2</span>：</p>
<pre><span lang="en-us" xml:lang="en-us">Sale_id | amount</span>

<span lang="en-us" xml:lang="en-us">0 | 110</span>

<span lang="en-us" xml:lang="en-us">0 | 120</span>

<span lang="en-us" xml:lang="en-us">3 | 50</span>

<span lang="en-us" xml:lang="en-us">4 | 20</span></pre>
<p>因为上述查询没有<span lang="en-us" xml:lang="en-us">reduce key</span>，<span lang="en-us" xml:lang="en-us">hive</span>会生成随机数作为<span lang="en-us" xml:lang="en-us">reduce key</span>。这样的话输入记录也随机地被分发到不同<span lang="en-us" xml:lang="en-us">reducer</span>机器上去了。为了保证<span lang="en-us" xml:lang="en-us">reducer</span>之间没有重复的<span lang="en-us" xml:lang="en-us">sale_id</span>记录，可以使用<span lang="en-us" xml:lang="en-us">DISTRIBUTE
 BY</span>关键字指定分发<span lang="en-us" xml:lang="en-us">key</span>为<span lang="en-us" xml:lang="en-us">sale_id</span>。改造后的<span lang="en-us" xml:lang="en-us">HQL</span>如下：</p>
<pre><span lang="en-us" xml:lang="en-us">set mapred.reduce.tasks=2;</span>

<span lang="en-us" xml:lang="en-us">Select sale_id, amount from t_order</span>

<span lang="en-us" xml:lang="en-us">Distribute by sale_id</span>

<span lang="en-us" xml:lang="en-us">Sort by sale_id, amount;</span></pre>
<p>这样能够保证查询的销售记录集合中，销售<span lang="en-us" xml:lang="en-us">ID</span>对应的数量是正确排序的，但是销售<span lang="en-us" xml:lang="en-us">ID</span>不能正确排序，原因是<span lang="en-us" xml:lang="en-us">hive</span>使用<span lang="en-us" xml:lang="en-us">hadoop</span>默认的<span lang="en-us" xml:lang="en-us">HashPartitioner</span>分发数据。</p>
<p>这就涉及到一个全排序的问题。解决的办法无外乎两种：</p>
<p><span lang="en-us" xml:lang="en-us">1.)</span> 不分发数据，使用单个<span lang="en-us" xml:lang="en-us">reducer</span>：</p>
<div>
<pre><span lang="en-us" xml:lang="en-us">set mapred.reduce.tasks=1;</span></pre>
</div>
<p>这一方法的缺陷在于<span lang="en-us" xml:lang="en-us">reduce</span>端成为了性能瓶颈，而且在数据量大的情况下一般都无法得到结果。但是实践中这仍然是最常用的方法，原因是通常排序的查询是为了得到排名靠前的若干结果，因此可以用<span lang="en-us" xml:lang="en-us">limit</span>子句大大减少数据量。使用<span lang="en-us" xml:lang="en-us">limit n</span>后，传输到<span lang="en-us" xml:lang="en-us">reduce</span>端（单机）的数据记录数就减少到<span lang="en-us" xml:lang="en-us">n*</span> （<span lang="en-us" xml:lang="en-us">map</span>个数）。</p>
<p><span lang="en-us" xml:lang="en-us">2.)</span> 修改<span lang="en-us" xml:lang="en-us">Partitioner</span>，这种方法可以做到全排序。这里可以使用<span lang="en-us" xml:lang="en-us">Hadoop</span>自带的<span lang="en-us" xml:lang="en-us">TotalOrderPartitioner</span>（来自于<span lang="en-us" xml:lang="en-us">Yahoo!</span>的<span lang="en-us" xml:lang="en-us">TeraSort</span>项目），这是一个为了支持跨<span lang="en-us" xml:lang="en-us">reducer</span>分发有序数据开发的<span lang="en-us" xml:lang="en-us">Partitioner</span>，它需要一个<span lang="en-us" xml:lang="en-us">SequenceFile</span>格式的文件指定分发的数据区间。如果我们已经生成了这一文件（存储在<span lang="en-us" xml:lang="en-us">/tmp/range_key_list</span>，分成<span lang="en-us" xml:lang="en-us">100</span>个<span lang="en-us" xml:lang="en-us">reducer</span>），可以将上述查询改写为</p>
<pre><span lang="en-us" xml:lang="en-us">set mapred.reduce.tasks=100;</span>

<span lang="en-us" xml:lang="en-us">set hive.mapred.partitioner=org.apache.hadoop.mapred.lib.TotalOrderPartitioner;</span>

<span lang="en-us" xml:lang="en-us">set total.order.partitioner.path=/tmp/ range_key_list;</span>

<span lang="en-us" xml:lang="en-us">Select sale_id, amount from t_order</span>

<span lang="en-us" xml:lang="en-us">Cluster by sale_id</span>

<span lang="en-us" xml:lang="en-us">Sort by amount;</span></pre>
<p>有很多种方法生成这一区间文件（例如<span lang="en-us" xml:lang="en-us">hadoop</span>自带的<span lang="en-us" xml:lang="en-us">o.a.h.mapreduce.lib.partition.InputSampler</span>工具）。这里介绍用<span lang="en-us" xml:lang="en-us">Hive</span>生成的方法，例如有一个按<span lang="en-us" xml:lang="en-us">id</span>有序的<span lang="en-us" xml:lang="en-us">t_sale</span>表：</p>
<pre><span lang="en-us" xml:lang="en-us">CREATE TABLE if not exists t_sale (</span>

<span lang="en-us" xml:lang="en-us">id int,</span>

<span lang="en-us" xml:lang="en-us">name string,</span>

<span lang="en-us" xml:lang="en-us">loc string</span>

<span lang="en-us" xml:lang="en-us">);</span></pre>
<p>则生成按<span lang="en-us" xml:lang="en-us">sale_id</span>分发的区间文件的方法是：</p>
<pre><span lang="en-us" xml:lang="en-us">create external table range_keys(sale_id int)</span>

<span lang="en-us" xml:lang="en-us">row format serde</span>

<span lang="en-us" xml:lang="en-us">'org.apache.hadoop.hive.serde2.binarysortable.BinarySortableSerDe'</span>

<span lang="en-us" xml:lang="en-us">stored as</span>

<span lang="en-us" xml:lang="en-us">inputformat</span>

<span lang="en-us" xml:lang="en-us">'org.apache.hadoop.mapred.TextInputFormat'</span>

<span lang="en-us" xml:lang="en-us">outputformat</span>

<span lang="en-us" xml:lang="en-us">'org.apache.hadoop.hive.ql.io.HiveNullValueSequenceFileOutputFormat'</span>

<span lang="en-us" xml:lang="en-us">location '/tmp/range_key_list';</span>

<span lang="en-us" xml:lang="en-us"> </span>

<span lang="en-us" xml:lang="en-us">insert overwrite table range_keys</span>

<span lang="en-us" xml:lang="en-us">select distinct sale_id</span>

<span lang="en-us" xml:lang="en-us">from source t_sale sampletable(BUCKET 100 OUT OF 100 ON rand()) s</span>

<span lang="en-us" xml:lang="en-us">sort by sale_id;</span></pre>
<p>生成的文件（<span lang="en-us" xml:lang="en-us">/tmp/range_key_list</span>目录下）可以让<span lang="en-us" xml:lang="en-us">TotalOrderPartitioner</span>按<span lang="en-us" xml:lang="en-us">sale_id</span>有序地分发<span lang="en-us" xml:lang="en-us">reduce</span>处理的数据。区间文件需要考虑的主要问题是数据分发的均衡性，这有赖于对数据深入的理解。</p>
<h2 style="font-size:21px;line-height:1.5;">怎样做笛卡尔积？</h2>
<p>当<span lang="en-us" xml:lang="en-us">Hive</span>设定为严格模式（<span lang="en-us" xml:lang="en-us">hive.mapred.mode=strict</span>）时，不允许在<span lang="en-us" xml:lang="en-us">HQL</span>语句中出现笛卡尔积，这实际说明了<span lang="en-us" xml:lang="en-us">Hive</span>对笛卡尔积支持较弱。因为找不到<span lang="en-us" xml:lang="en-us">Join key</span>，<span lang="en-us" xml:lang="en-us">Hive</span>只能使用<span lang="en-us" xml:lang="en-us">1</span>个<span lang="en-us" xml:lang="en-us">reducer</span>来完成笛卡尔积。</p>
<p>当然也可以用上面说的<span lang="en-us" xml:lang="en-us">limit</span>的办法来减少某个表参与<span lang="en-us" xml:lang="en-us">join</span>的数据量，但对于需要笛卡尔积语义的需求来说，经常是一个大表和一个小表的<span lang="en-us" xml:lang="en-us">Join</span>操作，结果仍然很大（以至于无法用单机处理），这时<span lang="en-us" xml:lang="en-us">MapJoin</span>才是最好的解决办法。</p>
<p><span lang="en-us" xml:lang="en-us">MapJoin</span>，顾名思义，会在<span lang="en-us" xml:lang="en-us">Map</span>端完成<span lang="en-us" xml:lang="en-us">Join</span>操作。这需要将<span lang="en-us" xml:lang="en-us">Join</span>操作的一个或多个表完全读入内存。</p>
<p><span lang="en-us" xml:lang="en-us">MapJoin</span>的用法是在查询<span lang="en-us" xml:lang="en-us">/</span>子查询的<span lang="en-us" xml:lang="en-us">SELECT</span>关键字后面添加<span lang="en-us" xml:lang="en-us">/*+ MAPJOIN(tablelist) */</span>提示优化器转化为<span lang="en-us" xml:lang="en-us">MapJoin</span>（目前<span lang="en-us" xml:lang="en-us">Hive</span>的优化器不能自动优化<span lang="en-us" xml:lang="en-us">MapJoin</span>）。其中<span lang="en-us" xml:lang="en-us">tablelist</span>可以是一个表，或以逗号连接的表的列表。<span lang="en-us" xml:lang="en-us">tablelist</span>中的表将会读入内存，应该将小表写在这里。</p>
<p><span lang="en-us" xml:lang="en-us">PS</span>：有用户说<span lang="en-us" xml:lang="en-us">MapJoin</span>在子查询中可能出现未知<span lang="en-us" xml:lang="en-us">BUG</span>。在大表和小表做笛卡尔积时，规避笛卡尔积的方法是，给<span lang="en-us" xml:lang="en-us">Join</span>添加一个<span lang="en-us" xml:lang="en-us">Join key</span>，原理很简单：将小表扩充一列<span lang="en-us" xml:lang="en-us">join
 key</span>，并将小表的条目复制数倍，<span lang="en-us" xml:lang="en-us">join key</span>各不相同；将大表扩充一列<span lang="en-us" xml:lang="en-us">join key</span>为随机数。</p>
<h2 style="font-size:21px;line-height:1.5;">怎样写<span lang="en-us" xml:lang="en-us">exist in</span>子句？</h2>
<p><span lang="en-us" xml:lang="en-us">Hive</span>不支持<span lang="en-us" xml:lang="en-us">where</span>子句中的子查询，<span lang="en-us" xml:lang="en-us">SQL</span>常用的<span lang="en-us" xml:lang="en-us">exist in</span>子句需要改写。这一改写相对简单。考虑以下<span lang="en-us" xml:lang="en-us">SQL</span>查询语句：</p>
<pre><span lang="en-us" xml:lang="en-us">SELECT a.key, a.value</span>

<span lang="en-us" xml:lang="en-us">FROM a</span>

<span lang="en-us" xml:lang="en-us">WHERE a.key in</span>

<span lang="en-us" xml:lang="en-us">(SELECT b.key</span>

<span lang="en-us" xml:lang="en-us">FROM B);</span></pre>
<p>可以改写为</p>
<pre><span lang="en-us" xml:lang="en-us">SELECT a.key, a.value</span>

<span lang="en-us" xml:lang="en-us">FROM a LEFT OUTER JOIN b ON (a.key = b.key)</span>

<span lang="en-us" xml:lang="en-us">WHERE b.key &lt;&gt; NULL;</span></pre>
<p>一个更高效的实现是利用<span lang="en-us" xml:lang="en-us">left semi join</span>改写为：</p>
<pre><span lang="en-us" xml:lang="en-us">SELECT a.key, a.val</span>

<span lang="en-us" xml:lang="en-us">FROM a LEFT SEMI JOIN b on (a.key = b.key);</span></pre>
<p><span lang="en-us" xml:lang="en-us">left semi join</span>是<span lang="en-us" xml:lang="en-us">0.5.0</span>以上版本的特性。</p>
<h2 style="font-size:21px;line-height:1.5;"><span lang="en-us" xml:lang="en-us">Hive</span>怎样决定<span lang="en-us" xml:lang="en-us">reducer</span>个数？</h2>
<p><span lang="en-us" xml:lang="en-us">Hadoop MapReduce</span>程序中，<span lang="en-us" xml:lang="en-us">reducer</span>个数的设定极大影响执行效率，这使得<span lang="en-us" xml:lang="en-us">Hive</span>怎样决定<span lang="en-us" xml:lang="en-us">reducer</span>个数成为一个关键问题。遗憾的是<span lang="en-us" xml:lang="en-us">Hive</span>的估计机制很弱，不指定<span lang="en-us" xml:lang="en-us">reducer</span>个数的情况下，<span lang="en-us" xml:lang="en-us">Hive</span>会猜测确定一个<span lang="en-us" xml:lang="en-us">reducer</span>个数，基于以下两个设定：</p>
<p><span lang="en-us" xml:lang="en-us">1. hive.exec.reducers.bytes.per.reducer</span>（默认为<span lang="en-us" xml:lang="en-us">1000^3</span>）</p>
<p><span lang="en-us" xml:lang="en-us">2. hive.exec.reducers.max</span>（默认为<span lang="en-us" xml:lang="en-us">999</span>）</p>
<p>计算<span lang="en-us" xml:lang="en-us">reducer</span>数的公式很简单：</p>
<div>
<pre><span lang="en-us" xml:lang="en-us">N=min(</span>参数<span lang="en-us" xml:lang="en-us">2</span>，总输入数据量<span lang="en-us" xml:lang="en-us">/</span>参数<span lang="en-us" xml:lang="en-us">1)</span></pre>
</div>
<p>通常情况下，有必要手动指定<span lang="en-us" xml:lang="en-us">reducer</span>个数。考虑到<span lang="en-us" xml:lang="en-us">map</span>阶段的输出数据量通常会比输入有大幅减少，因此即使不设定<span lang="en-us" xml:lang="en-us">reducer</span>个数，重设参数<span lang="en-us" xml:lang="en-us">2</span>还是必要的。依据<span lang="en-us" xml:lang="en-us">Hadoop</span>的经验，可以将参数<span lang="en-us" xml:lang="en-us">2</span>设定为<span lang="en-us" xml:lang="en-us">0.95*(</span>集群中<span lang="en-us" xml:lang="en-us">TaskTracker</span>个数<span lang="en-us" xml:lang="en-us">)</span>。</p>
<p> </p>
<h2 style="font-size:21px;line-height:1.5;">合并<span lang="en-us" xml:lang="en-us">MapReduce</span>操作</h2>
<p><strong><span lang="en-us" xml:lang="en-us">Multi-group by</span></strong></p>
<p><span lang="en-us" xml:lang="en-us">Multi-group by</span>是<span lang="en-us" xml:lang="en-us">Hive</span>的一个非常好的特性，它使得<span lang="en-us" xml:lang="en-us">Hive</span>中利用中间结果变得非常方便。例如，</p>
<pre>FROM (SELECT a.status, b.school, b.gender

FROM status_updates a JOIN profiles b

ON (a.userid = b.userid and

a.ds='2009-03-20' )

) subq1

INSERT OVERWRITE TABLE gender_summary

PARTITION(ds='2009-03-20')

SELECT subq1.gender, COUNT(1) GROUP BY subq1.gender

INSERT OVERWRITE TABLE school_summary

PARTITION(ds='2009-03-20')

SELECT subq1.school, COUNT(1) GROUP BY subq1.school
</pre>
<p>上述查询语句使用了<span lang="en-us" xml:lang="en-us">Multi-group by</span>特性连续<span lang="en-us" xml:lang="en-us">group by</span>了<span lang="en-us" xml:lang="en-us">2</span>次数据，使用不同的<span lang="en-us" xml:lang="en-us">group by key</span>。这一特性可以减少一次<span lang="en-us" xml:lang="en-us">MapReduce</span>操作。</p>
<p> </p>
<p><strong><span lang="en-us" xml:lang="en-us">Multi-distinct</span></strong></p>
<p><span lang="en-us" xml:lang="en-us">Multi-distinct</span>是淘宝开发的另一个<span lang="en-us" xml:lang="en-us">multi-xxx</span>特性，使用<span lang="en-us" xml:lang="en-us">Multi-distinct</span>可以在同一查询<span lang="en-us" xml:lang="en-us">/</span>子查询中使用多个<span lang="en-us" xml:lang="en-us">distinct</span>，这同样减少了多次<span lang="en-us" xml:lang="en-us">MapReduce</span>操作。</p>
<p>摘自：<a href="http://www.alidata.org/archives/622" rel="nofollow" style="color:rgb(255,51,102);">http://www.alidata.org/archives/622</a></p>
<div><br></div>
</div>
</div>
            </div>
                </div>