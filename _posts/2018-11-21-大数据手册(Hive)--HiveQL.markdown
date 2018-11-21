---
layout:     post
title:      大数据手册(Hive)--HiveQL
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p></p><div class="toc"><h3>文章目录</h3><ul><li><a href="#Hive_10" rel="nofollow">Hive交互</a></li><li><a href="#Hive_39" rel="nofollow">Hive内置运算符</a></li><li><a href="#Hive_82" rel="nofollow">Hive数据类型</a></li><li><a href="#Hive_97" rel="nofollow">Hive的数据存储</a></li><li><a href="#HiveQL_105" rel="nofollow">HiveQL常用语句</a></li><ul><ul><li><a href="#_112" rel="nofollow">创建/删除数据库</a></li><li><a href="#_117" rel="nofollow">创建/删除表</a></li><li><a href="#_156" rel="nofollow">修改表结构</a></li><li><a href="#_176" rel="nofollow">导入导出数据</a></li><li><a href="#_199" rel="nofollow">插入数据</a></li><li><a href="#_215" rel="nofollow">查询语句</a></li><li><a href="#_245" rel="nofollow">其他常用命令</a></li></ul></ul><li><a href="#Hive_260" rel="nofollow">Hive常用内置函数</a></li><li><a href="#Hive_308" rel="nofollow">Hive视图和索引</a></li><li><a href="#Hive_339" rel="nofollow">Hive性能调优</a></li><li><a href="#Hadoop_344" rel="nofollow">部分Hadoop命令</a></li></ul></div><p></p>
<hr>
<p>Hive是一个数据仓库基础工具在Hadoop中用来处理结构化数据。它架构在Hadoop之上，总归为大数据，并使得查询和分析方便。</p>
<h1><a id="Hive_10"></a>Hive交互</h1>
<p><strong>hive启动</strong></p>
<pre><code class="prism language-bash">hive <span class="token punctuation">[</span>--database dbname<span class="token punctuation">]</span>  <span class="token comment">##进入CLI交互界面，默认进入default数据库</span>
hive -v  <span class="token comment">##冗余verbose模式，额外打印出执行的HQL语句</span>
hive -S  <span class="token comment">##静默Slient模式，不显示转化MR-Job的信息，只显示最终结果</span>
hive -i <span class="token string">'hive-init.sql'</span>  <span class="token comment">##进入Hive交互Shell时候先执行脚本中的HQL语句</span>

<span class="token comment">#-----web管理</span>
hive --service serviceName    <span class="token comment">##启动web服务</span>
<span class="token comment">#打开web页面</span>

<span class="token comment">#-----hive 远程服务</span>
hive --serve hiveserve <span class="token operator">&amp;</span>
</code></pre>
<p><strong>hive命令行交互模式</strong></p>
<pre><code class="prism language-bash">hive<span class="token operator">&gt;</span> <span class="token keyword">select</span> * from dummy   <span class="token comment">#执行HiveQL语句</span>
hive<span class="token operator">&gt;</span> <span class="token function">source</span> ./hive-script.sql  <span class="token comment">##执行HQL脚本</span>
hive<span class="token operator">&gt;</span> <span class="token operator">!</span>echo <span class="token string">'hello hive'</span>      <span class="token comment">##执行shell命令，前面加 ！即可</span>
</code></pre>
<p><strong>shell窗口执行hive语句</strong></p>
<pre><code class="prism language-bash">hive -e <span class="token string">'show tables;'</span>  <span class="token comment">##从命令行执行指定的HQL</span>
hive -f <span class="token string">'hive-script.sql'</span>  <span class="token comment">##执行HQL脚本</span>
hive -S -e <span class="token string">'show tables;'</span>  <span class="token comment">##静默模式</span>
</code></pre>
<h1><a id="Hive_39"></a>Hive内置运算符</h1>

<table>
<thead>
<tr>
<th align="left">关系运算符</th>
<th align="left">说明</th>
</tr>
</thead>
<tbody>
<tr>
<td align="left">A = B</td>
<td align="left">等于</td>
</tr>
<tr>
<td align="left">A != B</td>
<td align="left">不等于</td>
</tr>
<tr>
<td align="left">A &lt; B</td>
<td align="left">小于</td>
</tr>
<tr>
<td align="left">A &lt;= B</td>
<td align="left">大于等于</td>
</tr>
<tr>
<td align="left">A &gt; B</td>
<td align="left">大于</td>
</tr>
<tr>
<td align="left">A &gt;= B</td>
<td align="left">大于等于</td>
</tr>
<tr>
<td align="left">A IS NULL</td>
<td align="left">空值</td>
</tr>
<tr>
<td align="left">A IS NOT NULL</td>
<td align="left">非空</td>
</tr>
<tr>
<td align="left">A LIKE B</td>
<td align="left">如果字符串模式A匹配到B，使用通配符，例如<code>%</code></td>
</tr>
<tr>
<td align="left">A RLIKE B</td>
<td align="left">如果A任何子字符串匹配正则表达式B</td>
</tr>
<tr>
<td align="left">A REGEXP B</td>
<td align="left">字符串</td>
</tr>
</tbody>
</table>
<table>
<thead>
<tr>
<th>算数运算符</th>
<th>描述</th>
</tr>
</thead>
<tbody>
<tr>
<td>A + B</td>
<td></td>
</tr>
<tr>
<td>A - B</td>
<td></td>
</tr>
<tr>
<td>A * B</td>
<td></td>
</tr>
<tr>
<td>A / B</td>
<td></td>
</tr>
<tr>
<td>A % B</td>
<td>余数</td>
</tr>
<tr>
<td>A &amp; B</td>
<td>A和B的按位与结果</td>
</tr>
<tr>
<td>A | B</td>
<td>A和B的按位或结果</td>
</tr>
<tr>
<td>A ^ B</td>
<td>A和B的按位异或结果</td>
</tr>
<tr>
<td>~A</td>
<td>A按位非的结果</td>
</tr>
</tbody>
</table>
<table>
<thead>
<tr>
<th>逻辑运算符</th>
<th>描述</th>
</tr>
</thead>
<tbody>
<tr>
<td>A AND B</td>
<td></td>
</tr>
<tr>
<td>A &amp;&amp; B</td>
<td>类似于 A AND B.</td>
</tr>
<tr>
<td>A OR B</td>
<td></td>
</tr>
<tr>
<td>A || B</td>
<td>类似于 A OR B.</td>
</tr>
<tr>
<td>NOT A</td>
<td></td>
</tr>
<tr>
<td>!A</td>
<td>类似于 NOT A.</td>
</tr>
</tbody>
</table>
<table>
<thead>
<tr>
<th>复杂运算符</th>
<th>操作</th>
<th>描述</th>
</tr>
</thead>
<tbody>
<tr>
<td>A[n]</td>
<td>A是一个数组，n是一个int</td>
<td>它返回数组A的第n个元素，第一个元素的索引0。</td>
</tr>
<tr>
<td>M[key]</td>
<td>M 是一个 Map&lt;K, V&gt; 并 key 的类型为K</td>
<td>它返回对应于映射中关键字的值。</td>
</tr>
<tr>
<td>S.x</td>
<td>S 是一个结构</td>
<td>它返回S的s字段</td>
</tr>
</tbody>
</table><h1><a id="Hive_82"></a>Hive数据类型</h1>

<table>
<thead>
<tr>
<th align="left">数据类型</th>
<th align="left">说明</th>
</tr>
</thead>
<tbody>
<tr>
<td align="left">tinyint/smallint/int/bigint</td>
<td align="left">整数类型</td>
</tr>
<tr>
<td align="left">float/double</td>
<td align="left">浮点型</td>
</tr>
<tr>
<td align="left">Boolean</td>
<td align="left">布尔类型</td>
</tr>
<tr>
<td align="left">string<br>varchar(n)/char(n)</td>
<td align="left">字符串类型</td>
</tr>
<tr>
<td align="left"><code>array&lt;data_type&gt;</code></td>
<td align="left">数组类型,同一类型<code>[val1,val2,...]</code></td>
</tr>
<tr>
<td align="left"><code>map&lt;key_type,value_type&gt;</code></td>
<td align="left">集合类型,<code>&lt;key,value&gt;</code></td>
</tr>
<tr>
<td align="left"><code>struct&lt;name1:type1,name2:type2,...&gt;</code></td>
<td align="left">结构类型,包含不同类型</td>
</tr>
<tr>
<td align="left"><strong>时间类型</strong></td>
<td align="left"></td>
</tr>
<tr>
<td align="left">date</td>
<td align="left">只有日期部分</td>
</tr>
<tr>
<td align="left">timestamp</td>
<td align="left">时间戳</td>
</tr>
</tbody>
</table><h1><a id="Hive_97"></a>Hive的数据存储</h1>
<ul>
<li>基于HDFS</li>
<li>内部表 table：我们删除表的时候在hdfs上对应的目录及数据文件一同被删除了</li>
<li>分区表 partition：对于数据库中的超大型表，可以通过把它的数据分成若干个小表，从而简化数据库的管理活动</li>
<li>外部表 external：受控表在删除一个表的时候,会把hdfs中的目录给删除掉,外部表是你在删除这个表的时候只删除了表定义,对于h</li>
<li>桶表 bucket：经过hash运算后方在不同的桶中</li>
</ul>
<h1><a id="HiveQL_105"></a>HiveQL常用语句</h1>
<p>HiveQL是Hive支持的类似于SQL的查询语言。HiveQL大体可以分成下面两种类型</p>
<ul>
<li>DDL(data definition language)  比如创建数据库(create database),创建表(create table),数据库和表的删除</li>
<li>DML(data manipulation language) 数据的添加，查询</li>
<li>UDF(user defined function) Hive还支持用户自定义查询函数</li>
</ul>
<h3><a id="_112"></a>创建/删除数据库</h3>
<pre><code class="prism language-sql"><span class="token keyword">CREATE</span> <span class="token keyword">DATABASE</span> <span class="token punctuation">[</span><span class="token keyword">IF</span> <span class="token operator">NOT</span> <span class="token keyword">EXISTS</span><span class="token punctuation">]</span> userdb<span class="token punctuation">;</span>
<span class="token keyword">DROP</span> <span class="token keyword">DATABASE</span> <span class="token punctuation">[</span><span class="token keyword">IF</span> <span class="token keyword">EXISTS</span><span class="token punctuation">]</span> userdb<span class="token punctuation">;</span>
</code></pre>
<h3><a id="_117"></a>创建/删除表</h3>
<pre><code class="prism language-sql"><span class="token comment">-------创建表</span>
<span class="token keyword">CREATE</span> <span class="token punctuation">[</span>EXTERNAL<span class="token punctuation">]</span> <span class="token keyword">TABLE</span> <span class="token punctuation">[</span><span class="token keyword">IF</span> <span class="token operator">NOT</span> <span class="token keyword">EXISTS</span><span class="token punctuation">]</span> userdb<span class="token punctuation">.</span>table_name <span class="token punctuation">(</span>
id string <span class="token keyword">COMMENT</span> <span class="token string">'主键'</span><span class="token punctuation">,</span>
name string <span class="token keyword">COMMENT</span> <span class="token string">'字符'</span><span class="token punctuation">,</span>
age <span class="token keyword">int</span> <span class="token keyword">COMMENT</span> <span class="token string">'整数'</span><span class="token punctuation">,</span>
married <span class="token keyword">boolean</span> <span class="token keyword">COMMENT</span> <span class="token string">'布尔类型'</span><span class="token punctuation">,</span> 
arr array<span class="token operator">&lt;</span>map<span class="token operator">&lt;</span>string<span class="token punctuation">,</span><span class="token keyword">int</span><span class="token operator">&gt;&gt;</span> <span class="token keyword">COMMENT</span> <span class="token string">'map数组'</span><span class="token punctuation">,</span>
info struct<span class="token operator">&lt;</span>addr:string<span class="token punctuation">,</span>height:<span class="token keyword">double</span><span class="token punctuation">,</span>birth:<span class="token keyword">date</span><span class="token operator">&gt;</span> <span class="token keyword">COMMENT</span> <span class="token string">'结构类型'</span><span class="token punctuation">,</span>
<span class="token punctuation">)</span>
<span class="token keyword">COMMENT</span> <span class="token string">'表注释'</span>
PARTITIONED <span class="token keyword">BY</span><span class="token punctuation">(</span>
<span class="token keyword">date</span> STRING <span class="token keyword">COMMENT</span> <span class="token string">'日期分区'</span><span class="token punctuation">,</span> 
sex STRING <span class="token keyword">COMMENT</span> <span class="token string">'性别分区'</span>
<span class="token punctuation">)</span>
<span class="token keyword">CLUSTERED</span> <span class="token keyword">BY</span><span class="token punctuation">(</span>id<span class="token punctuation">)</span> SORTED <span class="token keyword">BY</span><span class="token punctuation">(</span>num<span class="token punctuation">)</span> <span class="token keyword">INTO</span> <span class="token number">32</span> BUCKETS
<span class="token keyword">ROW</span> FORMAT DELIMITED <span class="token string">'\t'</span>
   <span class="token keyword">FIELDS</span> <span class="token keyword">TERMINATED</span> <span class="token keyword">BY</span> <span class="token string">'\t'</span>
STORED <span class="token keyword">AS</span> SEQUENCEFILE<span class="token punctuation">;</span>
LOCATION <span class="token string">'hdfs_path'</span><span class="token punctuation">;</span>

<span class="token comment">--加上EXTERNAL时，建立外部表</span>
<span class="token comment">--PARTITIONED BY 关键字建分区表</span>
<span class="token comment">--CLUSTERED BY 关键字建Bucket表</span>
<span class="token comment">--STORED AS 存储类型{SEQUENCEFILE:需要压缩,TEXTFILE:纯文本,RCFILE}</span>
<span class="token comment">--LOCATION添加外部表存储路径</span>

<span class="token comment">--------创建空表，复制表结构</span>
<span class="token keyword">CREATE</span> <span class="token keyword">TABLE</span> <span class="token punctuation">[</span><span class="token keyword">IF</span> <span class="token operator">NOT</span> <span class="token keyword">EXISTS</span><span class="token punctuation">]</span> empty_tablename
<span class="token operator">LIKE</span> other_tablename<span class="token punctuation">;</span>
<span class="token comment">--------直接导入数据</span>
<span class="token keyword">CREATE</span> <span class="token keyword">TABLE</span> <span class="token punctuation">[</span><span class="token keyword">IF</span> <span class="token operator">NOT</span> <span class="token keyword">EXISTS</span><span class="token punctuation">]</span> table_name
<span class="token keyword">AS</span>
<span class="token keyword">SELECT</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">;</span>
<span class="token comment">--------删除表</span>
<span class="token keyword">DROP</span> <span class="token keyword">TABLE</span> <span class="token punctuation">[</span><span class="token keyword">IF</span> <span class="token keyword">EXISTS</span><span class="token punctuation">]</span> table_name<span class="token punctuation">;</span>
</code></pre>
<h3><a id="_156"></a>修改表结构</h3>
<pre><code class="prism language-sql"><span class="token keyword">ALTER</span> <span class="token keyword">TABLE</span> table_name
<span class="token keyword">ADD</span> <span class="token keyword">COLUMNS</span> <span class="token punctuation">(</span>column_name <span class="token punctuation">[</span>string <span class="token keyword">COMMENT</span> <span class="token string">'新列'</span><span class="token punctuation">]</span><span class="token punctuation">,</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span><span class="token punctuation">,</span>
<span class="token keyword">DROP</span> <span class="token punctuation">[</span><span class="token keyword">COLUMN</span><span class="token punctuation">]</span> column_name<span class="token punctuation">,</span>
<span class="token keyword">RENAME</span> <span class="token keyword">TO</span> new_name<span class="token punctuation">,</span>                      <span class="token comment">--重命名表名</span>
CHANGE column_name new_name new_type<span class="token punctuation">,</span>
<span class="token keyword">REPLACE</span> <span class="token keyword">COLUMNS</span> <span class="token punctuation">(</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span><span class="token punctuation">;</span>                   <span class="token comment">--替换表中所有字段</span>

<span class="token comment">--添加/删除分区</span>
<span class="token keyword">ALTER</span> <span class="token keyword">TABLE</span> table_name
<span class="token keyword">ADD</span> <span class="token punctuation">[</span><span class="token keyword">IF</span> <span class="token operator">NOT</span> <span class="token keyword">EXISTS</span><span class="token punctuation">]</span> <span class="token keyword">PARTITION</span> <span class="token punctuation">(</span>y<span class="token operator">=</span><span class="token string">'2018'</span><span class="token punctuation">,</span>m<span class="token operator">=</span><span class="token string">'07'</span><span class="token punctuation">)</span><span class="token punctuation">,</span>
<span class="token keyword">DROP</span> <span class="token punctuation">[</span><span class="token keyword">IF</span> <span class="token keyword">EXISTS</span><span class="token punctuation">]</span> <span class="token keyword">PARTITION</span> <span class="token punctuation">(</span>y<span class="token operator">=</span><span class="token string">'2018'</span><span class="token punctuation">,</span>m<span class="token operator">=</span><span class="token string">'07'</span><span class="token punctuation">)</span>；

<span class="token comment">--重命名分区</span>
<span class="token keyword">ALTER</span> <span class="token keyword">TABLE</span> table_name <span class="token keyword">PARTITION</span> <span class="token punctuation">(</span>y<span class="token operator">=</span><span class="token string">'2018'</span><span class="token punctuation">,</span>m<span class="token operator">=</span><span class="token string">'07'</span><span class="token punctuation">)</span> 
<span class="token keyword">RENAME</span> <span class="token keyword">TO</span> <span class="token keyword">PARTITION</span> <span class="token punctuation">(</span>y<span class="token operator">=</span><span class="token string">'18'</span><span class="token punctuation">,</span>m<span class="token operator">=</span><span class="token string">'07'</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

</code></pre>
<h3><a id="_176"></a>导入导出数据</h3>
<pre><code class="prism language-sql"><span class="token comment">--LOAD命令可以导入本地数据或者hdfs数据</span>
<span class="token keyword">LOAD</span> <span class="token keyword">DATA</span> <span class="token punctuation">[</span><span class="token keyword">LOCAL</span><span class="token punctuation">]</span> INPATH <span class="token string">'filepath'</span> <span class="token punctuation">[</span>OVERWRITE<span class="token punctuation">]</span> <span class="token keyword">INTO</span> <span class="token keyword">TABLE</span> table_name 
<span class="token keyword">PARTITION</span> <span class="token punctuation">(</span>
<span class="token keyword">date</span><span class="token operator">=</span><span class="token string">'2018-07-03'</span><span class="token punctuation">,</span> 
sex<span class="token operator">=</span><span class="token string">'M'</span>
<span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token comment">--LOCAL是标识符指定本地路径。它是可选的</span>
<span class="token comment">--OVERWRITE是可选的，覆盖表中的数据</span>
<span class="token comment">--PARTITION分区</span>

<span class="token comment">--将hive数据导出到本地</span>
<span class="token keyword">INSERT</span> OVERWRITE <span class="token keyword">LOCAL</span> DIRECTORY <span class="token string">'hdfs_path'</span>  
<span class="token keyword">row</span> format delimited <span class="token keyword">fields</span> <span class="token keyword">terminated</span> <span class="token keyword">by</span> <span class="token string">'\t'</span> 
<span class="token keyword">select</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">;</span>

<span class="token comment">--将hive数据放到hdfs文件系统中</span>
<span class="token keyword">INSERT</span> OVERWRITE  DIRECTORY <span class="token string">'hdfs_path'</span> 
<span class="token keyword">SELECT</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">;</span>
</code></pre>
<h3><a id="_199"></a>插入数据</h3>
<pre><code class="prism language-sql">
<span class="token comment">--将查询结果插入Hive表</span>
<span class="token keyword">INSERT</span> OVERWRITE<span class="token operator">|</span><span class="token keyword">INTO</span>  <span class="token keyword">table</span> table_name 
<span class="token keyword">PARTITION</span> <span class="token punctuation">(</span>y<span class="token operator">=</span><span class="token string">'2018'</span><span class="token punctuation">,</span>m<span class="token operator">=</span><span class="token string">'07'</span><span class="token punctuation">)</span>
<span class="token keyword">SELECT</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">;</span>

<span class="token comment">--插入单条数据 （Hive 已支持单条插入）</span>
<span class="token keyword">INSERT</span> <span class="token keyword">INTO</span> table_name  <span class="token keyword">VALUES</span><span class="token punctuation">(</span><span class="token string">'num'</span><span class="token punctuation">,</span><span class="token number">10086</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

<span class="token comment">--将查询结果写入HDFS文件系统</span>
<span class="token keyword">INSERT</span> OVERWRITE DIRECTORY <span class="token string">'hdfs_path'</span> 
<span class="token keyword">SELECT</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">;</span>
</code></pre>
<h3><a id="_215"></a>查询语句</h3>
<p>select查询支持正则表达式（select `patt`)</p>
<pre><code class="prism language-sql"><span class="token keyword">SELECT</span> <span class="token punctuation">[</span><span class="token keyword">ALL</span> <span class="token operator">|</span> <span class="token keyword">DISTINCT</span><span class="token punctuation">]</span> select_expr<span class="token punctuation">,</span> select_expr<span class="token punctuation">,</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span> 
<span class="token keyword">FROM</span> table_reference 
<span class="token punctuation">[</span><span class="token keyword">WHERE</span> where_condition<span class="token punctuation">]</span>
<span class="token punctuation">[</span><span class="token keyword">GROUP</span> <span class="token keyword">BY</span> col_list<span class="token punctuation">]</span>
<span class="token punctuation">[</span><span class="token keyword">HAVING</span> having_condition<span class="token punctuation">]</span> <span class="token comment">--可筛选聚合后结果</span>
<span class="token punctuation">[</span>CLUSTER <span class="token keyword">BY</span> col_list <span class="token operator">|</span> <span class="token punctuation">[</span>DISTRIBUTE <span class="token keyword">BY</span> col_list<span class="token punctuation">]</span> <span class="token punctuation">[</span>SORT <span class="token keyword">BY</span> col_list<span class="token punctuation">]</span><span class="token punctuation">]</span> 
<span class="token punctuation">[</span><span class="token keyword">ORDER</span> <span class="token keyword">BY</span> col_list<span class="token punctuation">]</span>
<span class="token punctuation">[</span><span class="token keyword">LIMIT</span> number<span class="token punctuation">]</span><span class="token punctuation">;</span>

<span class="token comment">--join语句,只支持等值连接</span>
<span class="token keyword">SELECT</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span>
<span class="token keyword">FROM</span> table_name 
<span class="token keyword">JOIN</span> table1 <span class="token keyword">ON</span> join_condition
{<span class="token keyword">LEFT</span><span class="token operator">|</span><span class="token keyword">RIGHT</span><span class="token operator">|</span><span class="token keyword">FULL</span>} <span class="token punctuation">[</span><span class="token keyword">OUTER</span><span class="token punctuation">]</span> <span class="token keyword">JOIN</span> table1 <span class="token keyword">ON</span> join_condition
<span class="token keyword">LEFT</span> SEMI <span class="token keyword">JOIN</span> table3 <span class="token keyword">ON</span> join_condition
<span class="token keyword">CROSS</span> <span class="token keyword">JOIN</span> table4 <span class="token keyword">ON</span> join_condition<span class="token punctuation">;</span>

<span class="token comment">--联合语句（加上all不会进行去重）</span>
select_statement 
<span class="token keyword">UNION</span> <span class="token punctuation">[</span><span class="token keyword">ALL</span><span class="token punctuation">]</span> select_statement 
<span class="token keyword">UNION</span> <span class="token punctuation">[</span><span class="token keyword">ALL</span><span class="token punctuation">]</span> select_statement<span class="token punctuation">;</span>

<span class="token comment">--分组排序</span>
<span class="token keyword">select</span> row_number<span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">over</span><span class="token punctuation">(</span>distribute <span class="token keyword">by</span> col1 sort <span class="token keyword">by</span> col2 <span class="token punctuation">[</span><span class="token keyword">desc</span><span class="token punctuation">]</span><span class="token punctuation">)</span> <span class="token keyword">as</span> rn
</code></pre>
<h3><a id="_245"></a>其他常用命令</h3>
<pre><code class="prism language-sql"><span class="token keyword">SHOW</span> <span class="token keyword">DATABASES</span><span class="token operator">/</span><span class="token keyword">TABLES</span><span class="token punctuation">;</span>    <span class="token comment">--查看数据库/表</span>
<span class="token keyword">SHOW</span> <span class="token keyword">DATABASES</span><span class="token operator">/</span><span class="token keyword">TABLES</span> <span class="token operator">LIKE</span> <span class="token string">"*keyword*"</span>；
<span class="token keyword">USE</span> db_name<span class="token punctuation">;</span>             <span class="token comment">--连接数据库db_name</span>
<span class="token keyword">SHOW</span> PARTITIONS test_table<span class="token punctuation">;</span>   <span class="token comment">--查看分区</span>
<span class="token keyword">SHOW</span> FUNCTIONS<span class="token punctuation">;</span>             <span class="token comment">--查看内置函数</span>
<span class="token keyword">SHOW</span> <span class="token keyword">CREATE</span> <span class="token keyword">TABLE</span> table_name<span class="token punctuation">;</span>  <span class="token comment">-- 查看建表语句</span>
<span class="token keyword">DESC</span> table_name<span class="token punctuation">;</span>           <span class="token comment">-- 表结构</span>
<span class="token keyword">DESC</span> formatted table_name  <span class="token comment">--查看表更新时间</span>
<span class="token keyword">DESC</span> formatted table_name <span class="token keyword">partition</span><span class="token punctuation">(</span>name<span class="token operator">=</span><span class="token keyword">value</span><span class="token punctuation">)</span> <span class="token comment">--查看分区更新时间</span>
<span class="token keyword">EXPLAIN</span> <span class="token keyword">select</span> <span class="token operator">*</span> <span class="token keyword">from</span> dual<span class="token punctuation">;</span>    <span class="token comment">-- 解释语句</span>
<span class="token keyword">TRUNCATE</span> <span class="token keyword">TABLE</span> table_name<span class="token punctuation">;</span>     <span class="token comment">-- 清空表</span>
</code></pre>
<h1><a id="Hive_260"></a>Hive常用内置函数</h1>
<blockquote>
<p><a href="https://www.cnblogs.com/MOBIN/p/5618747.html" rel="nofollow">Hive2.0函数大全(中文版)</a></p>
</blockquote>

<table>
<thead>
<tr>
<th align="left">常用函数</th>
<th align="left">描述</th>
</tr>
</thead>
<tbody>
<tr>
<td align="left">count/sum/max/min/avg</td>
<td align="left">聚合函数</td>
</tr>
<tr>
<td align="left">round(num,n)</td>
<td align="left">四舍五入</td>
</tr>
<tr>
<td align="left">floor(num)</td>
<td align="left">地板</td>
</tr>
<tr>
<td align="left">ceil(num)</td>
<td align="left">天花板</td>
</tr>
<tr>
<td align="left">rand(), rand(int,seed)</td>
<td align="left">随机数</td>
</tr>
<tr>
<td align="left">corr(col1, col2)</td>
<td align="left">相关系数</td>
</tr>
<tr>
<td align="left">length(string)</td>
<td align="left">字符串长度</td>
</tr>
<tr>
<td align="left">concat(string A, string B,…)</td>
<td align="left">它返回从A后串联B产生的字符串</td>
</tr>
<tr>
<td align="left">substr(string A, int start, int length)</td>
<td align="left">取子集</td>
</tr>
<tr>
<td align="left">upper(string A)/lower</td>
<td align="left"></td>
</tr>
<tr>
<td align="left">trim(string A)</td>
<td align="left">删除两侧空格</td>
</tr>
<tr>
<td align="left">ltrim(string A)/rtrim(string A)</td>
<td align="left">删除左/右空格</td>
</tr>
<tr>
<td align="left">regexp_replace(string A, string B, string C)</td>
<td align="left">用C替换B</td>
</tr>
<tr>
<td align="left">split(str,pat)</td>
<td align="left">分割字符串，返回array</td>
</tr>
<tr>
<td align="left">percentile_approx(col, p，B)</td>
<td align="left">百分位数</td>
</tr>
<tr>
<td align="left">collect_set(col)<br>collect_list(col)</td>
<td align="left">行转数组(去重)<code>select user,collect_set(movie) from douban</code><br>行转数组(不去重)</td>
</tr>
<tr>
<td align="left"><code>array_contains(Array&lt;T&gt;, value)</code></td>
<td align="left">数组中是否包含value</td>
</tr>
<tr>
<td align="left">explode(ARRAY)</td>
<td align="left">数组转行</td>
</tr>
<tr>
<td align="left"><code>size(Map&lt;K.V&gt;)</code></td>
<td align="left">它返回在映射类型的元素的数量。</td>
</tr>
<tr>
<td align="left"><code>size(Array&lt;T&gt;)</code></td>
<td align="left">它返回在数组类型元素的数量。</td>
</tr>
<tr>
<td align="left"><code>cast(&lt;expr&gt; as &lt;type&gt;)</code></td>
<td align="left">将表达式的结果转换类型</td>
</tr>
<tr>
<td align="left">case when … then…else…end</td>
<td align="left"></td>
</tr>
<tr>
<td align="left">between…and…</td>
<td align="left"></td>
</tr>
<tr>
<td align="left"><strong>日期时间函数</strong></td>
<td align="left"></td>
</tr>
<tr>
<td align="left">from_unixtime(int unixtime)</td>
<td align="left">转换的秒数从Unix纪元(1970-01-0100:00:00 UTC)</td>
</tr>
<tr>
<td align="left">to_date(string timestamp)</td>
<td align="left">返回一个字符串时间戳的日期部分</td>
</tr>
<tr>
<td align="left">date_format(date,format)</td>
<td align="left">按制定格式返回日期</td>
</tr>
<tr>
<td align="left">year(string date)</td>
<td align="left"></td>
</tr>
<tr>
<td align="left">quarter(string date)</td>
<td align="left"></td>
</tr>
<tr>
<td align="left">month(string date)</td>
<td align="left"></td>
</tr>
<tr>
<td align="left">day(string date)</td>
<td align="left"></td>
</tr>
<tr>
<td align="left">current_date()</td>
<td align="left">当前日期</td>
</tr>
<tr>
<td align="left">weekofyear(string date)</td>
<td align="left"></td>
</tr>
<tr>
<td align="left">datediff(start，end)</td>
<td align="left">返回相差天数</td>
</tr>
<tr>
<td align="left">months_between(date,date)</td>
<td align="left">相差月份</td>
</tr>
<tr>
<td align="left">date_add()/date_sub()</td>
<td align="left">日期加/减若干天数</td>
</tr>
<tr>
<td align="left">add_months(date,int)</td>
<td align="left">日期加若干月</td>
</tr>
<tr>
<td align="left">last_day(date)</td>
<td align="left">这个月最后一天</td>
</tr>
<tr>
<td align="left">trunc(date,format)</td>
<td align="left">年（“YY”）或月（“MM”）的第一天</td>
</tr>
<tr>
<td align="left">next_day(date,string day_of_week)</td>
<td align="left">下个周X对应的日期</td>
</tr>
<tr>
<td align="left">get_json_object(string json_string, string path)</td>
<td align="left"></td>
</tr>
</tbody>
</table><h1><a id="Hive_308"></a>Hive视图和索引</h1>
<ul>
<li>**视图：**逻辑表，不存储实际数据</li>
<li><strong>索引：</strong></li>
</ul>
<pre><code class="prism language-sql"><span class="token comment">--创建视图</span>
<span class="token keyword">CREATE</span> <span class="token keyword">VIEW</span> <span class="token punctuation">[</span><span class="token keyword">IF</span> <span class="token operator">NOT</span> <span class="token keyword">EXISTS</span><span class="token punctuation">]</span> view_name 
<span class="token punctuation">(</span>column_name <span class="token keyword">COMMENT</span> <span class="token string">'列说明'</span><span class="token punctuation">,</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span>
TBLPROPERTIES <span class="token punctuation">(</span>property_name <span class="token operator">=</span> property_value<span class="token punctuation">,</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span>
<span class="token keyword">COMMENT</span> <span class="token string">'视图注释'</span>
<span class="token keyword">AS</span> <span class="token keyword">SELECT</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">;</span>
<span class="token comment">--删除试图</span>
<span class="token keyword">DROP</span> <span class="token keyword">VIEW</span> view_name<span class="token punctuation">;</span>

<span class="token comment">--创建索引</span>
<span class="token keyword">CREATE</span> <span class="token keyword">INDEX</span> index_name
<span class="token keyword">ON</span> <span class="token keyword">TABLE</span> base_table_name <span class="token punctuation">(</span>col_name<span class="token punctuation">,</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span>
<span class="token keyword">AS</span> <span class="token string">'index.handler.class.name'</span>
<span class="token punctuation">[</span><span class="token keyword">WITH</span> DEFERRED REBUILD<span class="token punctuation">]</span>
<span class="token punctuation">[</span>IDXPROPERTIES <span class="token punctuation">(</span>property_name<span class="token operator">=</span>property_value<span class="token punctuation">,</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span><span class="token punctuation">]</span>
<span class="token punctuation">[</span><span class="token operator">IN</span> <span class="token keyword">TABLE</span> index_table_name<span class="token punctuation">]</span>
<span class="token punctuation">[</span>PARTITIONED <span class="token keyword">BY</span> <span class="token punctuation">(</span>col_name<span class="token punctuation">,</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span><span class="token punctuation">]</span>
<span class="token punctuation">[</span> <span class="token keyword">ROW</span> FORMAT <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">]</span> <span class="token punctuation">[</span>STORED <span class="token keyword">AS</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token operator">|</span> STORED <span class="token keyword">BY</span> <span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">]</span>
<span class="token punctuation">[</span>LOCATION hdfs_path<span class="token punctuation">]</span>
<span class="token punctuation">[</span>TBLPROPERTIES <span class="token punctuation">(</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">.</span><span class="token punctuation">)</span><span class="token punctuation">]</span><span class="token punctuation">;</span>
<span class="token comment">--删除索引</span>
<span class="token keyword">DROP</span> <span class="token keyword">INDEX</span> <span class="token operator">&lt;</span>index_name<span class="token operator">&gt;</span> <span class="token keyword">ON</span> <span class="token operator">&lt;</span>table_name<span class="token operator">&gt;</span>

</code></pre>
<h1><a id="Hive_339"></a>Hive性能调优</h1>
<p><a href="https://blog.csdn.net/myproudcodelife/article/details/45372467" rel="nofollow">https://blog.csdn.net/myproudcodelife/article/details/45372467</a></p>
<h1><a id="Hadoop_344"></a>部分Hadoop命令</h1>
<p><a href="https://blog.csdn.net/m0_38003171/article/details/79086780" rel="nofollow">https://blog.csdn.net/m0_38003171/article/details/79086780</a></p>
<hr>
<p>参考链接<br>
<a href="https://www.yiibai.com/hive" rel="nofollow">https://www.yiibai.com/hive</a><br>
<a href="https://blog.csdn.net/u012386109/article/details/78214894" rel="nofollow">https://blog.csdn.net/u012386109/article/details/78214894</a><br>
<a href="https://www.cnblogs.com/skyl/p/4736129.html" rel="nofollow">https://www.cnblogs.com/skyl/p/4736129.html</a></p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>