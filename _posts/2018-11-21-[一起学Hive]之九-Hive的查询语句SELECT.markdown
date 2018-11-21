---
layout:     post
title:      [一起学Hive]之九-Hive的查询语句SELECT
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>关键字：Hive SELECT、ORDER BY、SORT BY、DISTRIBUTE BY、CLUSTER BY、Hive子查询、Hive虚拟列</p>

<h2>八、Hive的查询语句SELECT</h2>

<p>在所有的数据库系统中，SELECT语句是使用最多，也最复杂的一块，Hive中的查询语句SELECT支持的语法当然也比较复杂，本文只能尽力去介绍。</p>

<h3>8.1 基础查询语法</h3>

<p>Hive中的SELECT基础语法和标准SQL语法基本一致，支持WHERE、DISTINCT、GROUP BY、ORDER BY、HAVING、LIMIT、子查询等；<br>
语法如下：</p>

<pre>

 </pre>

<ol><li>[WITH CommonTableExpression (, CommonTableExpression)*]</li>
	<li>SELECT [ALL | DISTINCT] select_expr, select_expr, ...</li>
	<li>FROM table_reference</li>
	<li>[WHERE where_condition]</li>
	<li>[GROUP BY col_list]</li>
	<li>[CLUSTER BY col_list</li>
	<li>| [DISTRIBUTE BY col_list] [SORT BY col_list]</li>
	<li>]</li>
	<li>[LIMIT number]</li>
</ol><p>下面介绍Hive中比较特殊的一些查询语法。</p>

<h3>8.2 ORDER BY和SORT BY</h3>

<p>ORDER BY用于全局排序，就是对指定的所有排序键进行全局排序，使用ORDER BY的查询语句，最后会用一个Reduce Task来完成全局排序。<br>
SORT BY用于分区内排序，即每个Reduce任务内排序。<br>
看下面的例子：<br>
原表数据为：</p>

<pre>

 </pre>

<ol><li>hive&gt; select * from lxw1234_com;</li>
	<li>OK</li>
	<li>5</li>
	<li>3</li>
	<li>6</li>
	<li>2</li>
	<li>9</li>
	<li>8</li>
	<li>1</li>
	<li> </li>
</ol><p>使用ORDER BY</p>

<pre>

 </pre>

<ol><li>hive&gt; select * from lxw1234_com order by id;</li>
	<li>1</li>
	<li>2</li>
	<li>3</li>
	<li>5</li>
	<li>6</li>
	<li>8</li>
	<li>9</li>
	<li> </li>
</ol><p>使用SORT BY</p>

<p>hive&gt; set mapred.reduce.tasks=2;<br>
hive&gt; select * from lxw1234_com sort by id;<br>
2<br>
5<br>
6<br>
9<br>
1<br>
3<br>
8</p>

<p>设定了2个reduce，从结果可以看出，每个reduce内做了排序。<br>
如果reduce数为1，那么ORDER BY和SORT BY的结果是一样的：</p>

<pre>

 </pre>

<ol><li>hive&gt; set mapred.reduce.tasks=1;</li>
	<li>hive&gt; select * from lxw1234_com sort by id;</li>
	<li>1</li>
	<li>2</li>
	<li>3</li>
	<li>5</li>
	<li>6</li>
	<li>8</li>
	<li>9</li>
	<li> </li>
</ol><p>真实业务环境中，我们的需求大多需要使用ORDER BY全局排序来完成。</p>

<h3>8.3 DISTRIBUTE BY和CLUSTER BY</h3>

<p>distribute by：按照指定的字段或表达式对数据进行划分，输出到对应的Reduce或者文件中。<br>
cluster by：除了兼具distribute by的功能，还兼具sort by的排序功能。</p>

<p>##distribute by</p>

<pre>

 </pre>

<ol><li>hive&gt; set mapred.reduce.tasks=2;</li>
	<li>hive&gt;INSERT overwrite LOCAL directory '/tmp/lxw1234/'</li>
	<li>SELECT id FROM lxw1234_com</li>
	<li>distribute BY id;</li>
</ol><p>执行后在本地的/tmp/lxw1234目录中生成了000000_0和000001_0两个文件：<br>
cat 000000_0<br>
8<br>
2<br>
6<br>
cat 000001_0 ##id%2=1的记录<br>
1<br>
9<br>
3<br>
5</p>

<p>##cluster by</p>

<pre>

 </pre>

<ol><li>hive&gt; set mapred.reduce.tasks=2;</li>
	<li>hive&gt; INSERT overwrite LOCAL directory '/tmp/lxw1234/'</li>
	<li>SELECT id FROM lxw1234_com</li>
	<li>CLUSTER BY id;</li>
	<li>cat 000000_0</li>
	<li>2</li>
	<li>6</li>
	<li>8</li>
	<li>cat 000001_0</li>
	<li>1</li>
	<li>3</li>
	<li>5</li>
	<li>9</li>
</ol><p>注意：使用cluster by之后，每个文件中的id都进行了排序，而distribute by没有。</p>

<h3>8.4 子查询</h3>

<p>子查询和标准SQL中的子查询语法和用法基本一致，需要注意的是，Hive中如果是从一个子查询进行SELECT查询，那么子查询必须设置一个别名。</p>

<pre>

 </pre>

<ol><li>SELECT col</li>
	<li>FROM (</li>
	<li>SELECT a+b AS col</li>
	<li>FROM t1</li>
	<li>) t2</li>
</ol><p>另外，从Hive0.13开始，在WHERE子句中也支持子查询，比如：</p>

<pre>

 </pre>

<ol><li>SELECT *</li>
	<li>FROM A</li>
	<li>WHERE A.a IN (SELECT foo FROM B);</li>
	<li> </li>
	<li>SELECT A</li>
	<li>FROM T1</li>
	<li>WHERE EXISTS (SELECT B FROM T2 WHERE T1.X = T2.Y)</li>
</ol><p>还有一种将子查询作为一个表的语法，叫做Common Table Expression（CTE）：</p>

<pre>

 </pre>

<ol><li>with q1 as (select * from src where key= '5'),</li>
	<li>q2 as (select * from src s2 where key = '4')</li>
	<li>select * from q1 union all select * from q2;</li>
	<li> </li>
	<li>with q1 as ( select key, value from src where key = '5')</li>
	<li>from q1</li>
	<li>insert overwrite table s1</li>
	<li>select *;</li>
</ol><h3>8.5 虚拟列</h3>

<p>Hive查询中有两个虚拟列：<br>
INPUT__FILE__NAME：数据对应的HDFS文件名；<br>
BLOCK__OFFSET__INSIDE__FILE：该行记录在文件中的偏移量；</p>

<pre>

 </pre>

<ol><li>hive&gt; select id,INPUT__FILE__NAME, BLOCK__OFFSET__INSIDE__FILE from lxw1234_com;</li>
	<li>5 hdfs://cdh5/tmp/lxw1234/1.txt 0</li>
	<li>3 hdfs://cdh5/tmp/lxw1234/1.txt 2</li>
	<li>6 hdfs://cdh5/tmp/lxw1234/1.txt 4</li>
	<li>2 hdfs://cdh5/tmp/lxw1234/1.txt 6</li>
	<li>9 hdfs://cdh5/tmp/lxw1234/1.txt 8</li>
	<li>8 hdfs://cdh5/tmp/lxw1234/1.txt 10</li>
	<li>1 hdfs://cdh5/tmp/lxw1234/1.txt 12</li>
</ol><p>Hive查询中还包括：<br>
Hive的内置操作和函数： <a href="http://lxw1234.com/archives/2015/06/251.htm" rel="nofollow">http://lxw1234.com/archives/2015/06/251.htm</a><br>
Hive窗口分析函数：<a href="http://lxw1234.com/archives/tag/hive-window-functions" rel="nofollow">http://lxw1234.com/archives/tag/hive-window-functions</a></p>            </div>
                </div>