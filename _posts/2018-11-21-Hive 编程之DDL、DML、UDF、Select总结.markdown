---
layout:     post
title:      Hive 编程之DDL、DML、UDF、Select总结
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主Huidoo_Yang的原创文章，未经博主允许不得转载。					https://blog.csdn.net/Huidoo_Yang/article/details/79516731				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<div id="cnblogs_post_body" class="blogpost-body"><p>　　Hive的基本理论与安装可参看作者上一篇博文《<a href="http://www.cnblogs.com/yangp/p/8529946.html" rel="nofollow" target="_blank">Apache Hive 基本理论与安装指南</a>》。</p>
<h2 id="scroller-1"><span>一、Hive命令行</span></h2>
<p>　　所有的hive命令都可以通过hive命令行去执行，hive命令行中仍有许多选项。使用$hive -H查看：</p>
<p><img src="https://images2018.cnblogs.com/blog/884765/201803/884765-20180310162800655-466430610.png" alt=""></p>
<p>　　-e 选项后面可以直接接一个hql语句，不用进入到hive命令行用户接口再输入hql语句。</p>
<p>　　-f 选项后面接一个hql语句的文件。</p>
<p>　　-i 选项在hql语句执行之前的初始化hql文件。例如添加、导入等操作都可以写在这个hql语句文件中。</p>
<p>　　在hive使用中，大多是写很多脚本，在hive命令行中输入是属于交互式的，这就必须有工作人员在工作，不适合做批量处理。可以使用-f或-i选项，一次性执行可以将所有的hql语句放在一个hql脚本文件中。例如：</p>
<div class="cnblogs_code">
<pre>$ <span>mkdir</span> -p /opt/shell/<span>sql
$ vim </span>/opt/shell/sql/test.sql</pre>
</div>

<div class="cnblogs_code">
<pre><span>select</span> <span>*</span> <span>from</span> table_name limit <span>5</span><span>;
</span><span>select</span> <span>*</span> <span>from</span> table_name <span>where</span> professional<span>=</span><span>"student";
</span><span>select</span> <span>count</span>(<span>*</span>) <span>from</span><span> table_name;
</span><span>select</span> <span>count</span>(<span>*</span>) <span>from</span> table_name <span>group</span> <span>by</span><span> professional;
</span><span>select</span> <span>*</span> <span>from</span> table_name <span>where</span> professional<span>=</span>"student" <span>order</span> <span>by</span> age;</pre>
</div>

<p>　　将脚本保存退出后，再批量执行：</p>

<div class="cnblogs_code">
<pre>$ hive -f /opt/shell/sql/test.sql</pre>
</div>

<h2 id="scroller-12"><span>二、Hive DDL（Data Definition Language）</span></h2>

<ul>
<li><span class="toc-item-body"><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-HiveDataDefinitionLanguage" rel="nofollow">Hive Data Definition Language</a></span></span>
<ul>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-Overview" rel="nofollow">Overview</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-Keywords,Non-reservedKeywordsandReservedKeywords" rel="nofollow">Keywords, Non-reserved Keywords and Reserved Keywords</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-Create/Drop/Alter/UseDatabase" rel="nofollow">Create/Drop/Alter/Use Database</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-CreateTableCreate/Drop/TruncateTable" rel="nofollow">Create/Drop/Truncate Table</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTable/Partition/Column" rel="nofollow">Alter Table/Partition/Column</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-Create/Drop/AlterView" rel="nofollow">Create/Drop/Alter View</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-Create/Drop/AlterIndex" rel="nofollow">Create/Drop/Alter Index</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-Create/DropMacro" rel="nofollow">Create/Drop Macro</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-Create/Drop/ReloadFunction" rel="nofollow">Create/Drop/Reload Function</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-Create/Drop/Grant/RevokeRolesandPrivileges" rel="nofollow">Create/Drop/Grant/Revoke Roles and Privileges</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-Show" rel="nofollow">Show</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-Describe" rel="nofollow">Describe</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-Abort" rel="nofollow">Abort</a></span></li>
</ul>

<p></p></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-HCatalogandWebHCatDDL" rel="nofollow">HCatalog and WebHCat DDL</a></span></li>
</ul>

<p>　　详见<a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-Create/Drop/TruncateTable" rel="nofollow" target="_blank">官方文档</a>。重点在于创建表和创建函数。</p>

<h3 id="scroller-15"><span>1. 建表语法</span></h3>

<ul>
<li><strong>建表语句</strong></li>
</ul>

<p>　　从官方文档的Create Table中可以查看hive建表的命令，如下：</p>

<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span>CREATE</span> <span>[</span><span>TEMPORARY</span><span>]</span> <span>[</span><span>EXTERNAL</span><span>]</span> <span>TABLE</span> <span>[</span><span>IF NOT EXISTS</span><span>]</span> <span>[</span><span>db_name.</span><span>]</span>table_name    <span>--</span><span> (Note: TEMPORARY available in Hive 0.14.0 and later)</span>
  <span>[</span><span>(col_name data_type [COMMENT col_comment</span><span>]</span>, ... <span>[</span><span>constraint_specification</span><span>]</span><span>)]
  </span><span>[</span><span>COMMENT table_comment</span><span>]</span>
  <span>[</span><span>PARTITIONED BY (col_name data_type [COMMENT col_comment</span><span>]</span><span>, ...)]
  </span><span>[</span><span>CLUSTERED BY (col_name, col_name, ...) [SORTED BY (col_name [ASC|DESC</span><span>]</span>, ...)] <span>INTO</span><span> num_buckets BUCKETS]
  </span><span>[</span><span>SKEWED BY (col_name, col_name, ...)                  -- (Note: Available in Hive 0.10.0 and later)</span><span>]</span>
     <span>ON</span><span> ((col_value, col_value, ...), (col_value, col_value, ...), ...)
     </span><span>[</span><span>STORED AS DIRECTORIES</span><span>]</span>
  <span>[</span><span>
   [ROW FORMAT row_format</span><span>]</span> 
   <span>[</span><span>STORED AS file_format</span><span>]</span>
     <span>|</span> STORED <span>BY</span> <span>'</span><span>storage.handler.class.name</span><span>'</span> <span>[</span><span>WITH SERDEPROPERTIES (...)</span><span>]</span>  <span>--</span><span> (Note: Available in Hive 0.6.0 and later)</span>
<span>  ]
  </span><span>[</span><span>LOCATION hdfs_path</span><span>]</span>
  <span>[</span><span>TBLPROPERTIES (property_name=property_value, ...)</span><span>]</span>   <span>--</span><span> (Note: Available in Hive 0.6.0 and later)</span>
  <span>[</span><span>AS select_statement</span><span>]</span>;   <span>--</span><span> (Note: Available in Hive 0.5.0 and later; not supported for external tables)</span>

<span>CREATE</span> <span>[</span><span>TEMPORARY</span><span>]</span> <span>[</span><span>EXTERNAL</span><span>]</span> <span>TABLE</span> <span>[</span><span>IF NOT EXISTS</span><span>]</span> <span>[</span><span>db_name.</span><span>]</span><span>table_name
  </span><span>LIKE</span><span> existing_table_or_view_name
  </span><span>[</span><span>LOCATION hdfs_path</span><span>]</span>;</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<ul>
<li><strong>数据类型</strong></li>
</ul>
<div class="cnblogs_code">
<pre><span>data_type
  : primitive_type
  </span><span>|</span><span> array_type
  </span><span>|</span><span> map_type
  </span><span>|</span><span> struct_type
  </span><span>|</span> union_type  <span>--</span><span> (Note: Available in Hive 0.7.0 and later)</span></pre>
</div>

<p>　　data_type，有两种类型：一种是复杂类型，例如array_type（ARRAY &lt; data_type &gt;）、map_type（MAP &lt; primitive_type, data_type &gt;）、struct_type（STRUCT &lt; col_name : data_type [COMMENT col_comment], …&gt;）、union_type（UNIONTYPE &lt; data_type, data_type, … &gt;）；简单类型有SMALLINT（占2个字节的整数）、INT（占4个字节的整数）、BIGINT（占8个字节的整数）、BOOLEAN、FLOAT、DOUBLE、STRING、BINARY、TIMESTAMP、DECIMAL、DATE、VARCHAR（0.12之后）、CHAR（字符，0.13之后）。</p>

<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span>primitive_type
  : </span><span>TINYINT</span>
  <span>|</span> <span>SMALLINT</span>
  <span>|</span> <span>INT</span>
  <span>|</span> <span>BIGINT</span>
  <span>|</span><span> BOOLEAN
  </span><span>|</span> <span>FLOAT</span>
  <span>|</span> <span>DOUBLE</span>
  <span>|</span> <span>DOUBLE</span> <span>PRECISION</span> <span>--</span><span> (Note: Available in Hive 2.2.0 and later)</span>
  <span>|</span><span> STRING
  </span><span>|</span> <span>BINARY</span>      <span>--</span><span> (Note: Available in Hive 0.8.0 and later)</span>
  <span>|</span> <span>TIMESTAMP</span>   <span>--</span><span> (Note: Available in Hive 0.8.0 and later)</span>
  <span>|</span> <span>DECIMAL</span>     <span>--</span><span> (Note: Available in Hive 0.11.0 and later)</span>
  <span>|</span> <span>DECIMAL</span>(<span>precision</span>, scale)  <span>--</span><span> (Note: Available in Hive 0.13.0 and later)</span>
  <span>|</span> DATE        <span>--</span><span> (Note: Available in Hive 0.12.0 and later)</span>
  <span>|</span> <span>VARCHAR</span>     <span>--</span><span> (Note: Available in Hive 0.12.0 and later)</span>
  <span>|</span> <span>CHAR</span>        <span>--</span><span> (Note: Available in Hive 0.13.0 and later)</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<ul>
<li><strong>row_format</strong></li>
</ul>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span>row_format
  : DELIMITED </span><span>[</span><span>FIELDS TERMINATED BY char [ESCAPED BY char</span><span>]</span>] <span>[</span><span>COLLECTION ITEMS TERMINATED BY char</span><span>]</span>
        <span>[</span><span>MAP KEYS TERMINATED BY char</span><span>]</span> <span>[</span><span>LINES TERMINATED BY char</span><span>]</span>
        <span>[</span><span>NULL DEFINED AS char</span><span>]</span>   <span>--</span><span> (Note: Available in Hive 0.13 and later)</span>
  <span>|</span> SERDE serde_name <span>[</span><span>WITH SERDEPROPERTIES (property_name=property_value, property_name=property_value, ...)</span><span>]</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>　　row_format：需要指出每一行是由什么隔开，并且要指出每一行中的每一个字段是用什么隔开的。</p>
<p>　　对于字段的隔开，复杂数据类型和简单数据类型不一样。例如数组类型是复杂类型，其隔开符[COLLECTION ITEMS TERMINATED BY char]，Map类型的要使用[MAP KEYS TERMINATED BY char]。[LINES TERMINATED BY char]可以设置行隔开符，如果不写，默认为回车换行。</p>
<ul>
<li><strong>file_format</strong></li>
</ul>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span>file_format:
  : SEQUENCEFILE
  </span><span>|</span> TEXTFILE    <span>--</span><span> (Default, depending on hive.default.fileformat configuration)</span>
  <span>|</span> RCFILE      <span>--</span><span> (Note: Available in Hive 0.6.0 and later)</span>
  <span>|</span> ORC         <span>--</span><span> (Note: Available in Hive 0.11.0 and later)</span>
  <span>|</span> PARQUET     <span>--</span><span> (Note: Available in Hive 0.13.0 and later)</span>
  <span>|</span> AVRO        <span>--</span><span> (Note: Available in Hive 0.14.0 and later)</span>
  <span>|</span> INPUTFORMAT input_format_classname OUTPUTFORMAT output_format_classname</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>　　SEQUENCEFILE表示序列压缩格式，以及RCFILE、ORC、PARQUET、AVRO都是压缩格式。TEXTFILE是默认的格式，文本格式。</p>
<h3 id="scroller-30"><span>2. 创建表案例一</span>　　</h3>
<ul>
<li><strong>查看数据</strong></li>
</ul>
<p>　　为举例说明，这里先生成数据文件：</p>
<div class="cnblogs_code">
<pre>vim /root/person.txt</pre>
</div>

<div class="cnblogs_code">
<pre><span>1</span>,zhangsan,<span>31</span>,books-sports-math,city:shanghai-street:lujiazui-zipcode:<span>313100</span>
<span>2</span>,lisi,<span>35</span>,books-music,city:hangzhou-street:xihu-zipcode:<span>222100</span>
<span>3</span>,wangwu,<span>25</span>,computer-games,city:guangzhou-street:baiyun-zipcode:<span>212300</span></pre>
</div>

<ul>
<li><strong>创建表</strong></li>
</ul>

<p>　　根据数据创建一个人类表，包含人的编号、姓名、年龄、爱好、地址五个字段。</p>

<div class="cnblogs_code">
<pre>vim <span>/</span>opt<span>/</span>shell<span>/</span>sql<span>/</span>t_person.sql</pre>
</div>

<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span>create</span> <span>table</span><span> t_person
(
    id </span><span>int</span><span>,
    name string,
    age </span><span>int</span><span>,
    likes array</span><span>&lt;</span>string<span>&gt;</span><span>,
    address map</span><span>&lt;</span>string,string<span>&gt;</span><span>
) row format delimited 
fields terminated </span><span>by</span> <span>'</span><span>,</span><span>'</span><span> 
collection items terminated </span><span>by</span> <span>'</span><span>-</span><span>'</span><span>
map keys terminated </span><span>by</span> <span>'</span><span>:</span><span>'</span><span> 
lines terminated </span><span>by</span> <span>'</span><span>\n</span><span>'</span><span> 
stored </span><span>as</span> textfile;</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>　　根据表结构，字段之间按,隔开；likes的类型是array，所以可以有多个爱好，元素之间按-隔开，以,作为数组的结束；地址是map类型，也是collection类型， key和value按:隔开，每对key和value之间用-隔开（和collection一致）；回车代表换行符。</p>
<p>　　执行创建表脚本：</p>
<div class="cnblogs_code">
<pre>$ hive -f /opt/shell/sql/t_person.sql</pre>
</div>

<ul>
<li><strong>将数据从本地导入到hive</strong></li>
</ul>

<div class="cnblogs_code">
<pre>hive<span>&gt;</span> <span>load</span> data local inpath <span>'</span><span>/root/person.txt</span><span>'</span> <span>into</span> <span>table</span> t_person;</pre>
</div>

<ul>
<li><strong>查看数据</strong></li>
</ul>

<p> <img src="https://images2018.cnblogs.com/blog/884765/201803/884765-20180311003756558-836928601.png" alt="" width="576" height="127"></p>

<h3 id="scroller-46"><span>3. 创建表案例二（分区表）</span></h3>

<p>　　当我们要在关系型数据库中查找某一数据时，一般需要匹配数据库中的所有数据，所以可以通过分区来提高查询效率。把常用的查询条件作为一种分区，例如要查某一天的数据，事实上就是可以只匹配当天的数据，其他数据不匹配。下面以建一个日志管理表为例：</p>

<ul>
<li><strong>数据文件</strong></li>
</ul>

<p>　　文件经常按照天、小时等进行分割。故在此创建多个文件如下：</p>

<div class="cnblogs_code">
<pre>vim /root/log_2016-<span>03</span>-<span>11</span>.txt</pre>
</div>

<div class="cnblogs_code">
<pre><span>111</span>,<span>1</span>,<span>200</span>,2016-03-11
<span>135</span>,<span>4</span>,<span>100</span>,2016-03-11
<span>211</span>,<span>3</span>,<span>300</span>,2016-03-11
<span>141</span>,<span>1</span>,<span>200</span>,2016-03-11
<span>131</span>,<span>2</span>,<span>400</span>,2016-03-11</pre>
</div>

<div class="cnblogs_code">
<pre>vim /root/log_2016-<span>03</span>-<span>12</span>.txt</pre>
</div>

<div class="cnblogs_code">
<pre><span>121</span>,<span>2</span>,<span>300</span>,2016-03-12
<span>125</span>,<span>3</span>,<span>400</span>,2016-03-12
<span>211</span>,<span>1</span>,<span>400</span>,2016-03-12
<span>161</span>,<span>3</span>,<span>100</span>,2016-03-12
<span>171</span>,<span>5</span>,<span>200</span>,2016-03-12</pre>
</div>

<ul>
<li><strong>创建表结构</strong></li>
</ul>

<div class="cnblogs_code">
<pre><span>create</span> <span>table</span> t_log (id string,type <span>int</span><span>,pid string,logtime string)
partitioned </span><span>by</span> (<span>day</span><span> string)
row format delimited 
fields terminated </span><span>by</span> <span>'</span><span>,</span><span>'</span><span>
stored </span><span>as</span> textfile;</pre>
</div>

<p>　　这里指定了按照天做分区。</p>

<ul>
<li><strong>装载数据</strong></li>
</ul>

<div class="cnblogs_code">
<pre>hive<span>&gt;</span> <span>load</span> data local inpath <span>'</span><span>/root/<span>log_2016-03-11</span>.txt</span><span>'</span> <span>into</span> <span>table</span> t_log partition (<span>day</span><span>=</span><span>'</span><span>2016-03-11</span><span>'</span><span>);
hive</span><span>&gt;</span> <span>load</span> data local inpath <span>'</span><span>/root/log_2016-03-12.txt</span><span>'</span> <span>into</span> <span>table</span> t_log partition (<span>day</span><span>=</span><span>'</span><span>2016-03-12</span><span>'</span>);</pre>
</div>

<ul>
<li><strong>查看数据</strong></li>
</ul>

<div class="cnblogs_code">
<pre>hive<span>&gt;</span> <span>select</span> <span>*</span> <span>from</span> t_log;</pre>
</div>

<p><img src="https://images2018.cnblogs.com/blog/884765/201803/884765-20180311011544824-709212847.png" alt=""></p>

<p>　　从上图可以看出，数据增加了一个字段，左边是logtime，右边是分区day。所以，添加一个分区可以理解为添加一个字段；但是又不同于添加一个字段，因为查询按照分区做限定（查询分区day=’2016-03-11’）时其他分区的数据不读取，而如果是字段的话，需要每一条数据进行匹配过滤。也可以从浏览器看出创建分区表的目录结构如下：</p>

<p><img src="https://images2018.cnblogs.com/blog/884765/201803/884765-20180311012030658-445318338.png" alt="" width="556" height="187"></p>

<p>　　创建分区，就会为每一个分区建一个目录，所以在查询时，只在限定的分区目录里查询。</p>

<p>　　<strong>查看某一天的数据量</strong></p>

<div class="cnblogs_code">
<pre>hive<span>&gt;</span> <span>select</span> <span>count</span>(<span>*</span>) <span>from</span> t_log <span>where</span> <span>day</span><span>=</span>'<span>2016</span><span>-</span><span>03</span><span>-</span><span>11'</span>;</pre>
</div>

<p><strong>　　查看某一个月的数据量</strong></p>

<div class="cnblogs_code">
<pre>hive<span>&gt;</span><span>select</span> <span>count</span>(<span>*</span>) <span>from</span> t_log <span>where</span> <span>day</span> <span>between</span> <span>'</span><span>xxx</span><span>'</span> <span>and</span> <span>'</span><span>xxx</span><span>'</span>;</pre>
</div>

<p>　　当然，这里需要先转换格式，略。</p>

<h2 id="scroller-70"><span>三、Hive DML（Data Manipulation Language）</span></h2>

<ul>
<li><span class="toc-item-body"><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-HiveDataManipulationLanguage" rel="nofollow">Hive Data Manipulation Language</a></span></span>
<ul>
<li><span class="toc-item-body"><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-Loadingfilesintotables" rel="nofollow">Loading files into tables</a></span></span>
<ul>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-Syntax" rel="nofollow">Syntax</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-Synopsis" rel="nofollow">Synopsis</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-Notes" rel="nofollow">Notes</a></span></li>
</ul>

<p></p></li>
<li><span class="toc-item-body"><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-InsertingdataintoHiveTablesfromqueries" rel="nofollow">Inserting data into Hive Tables from queries</a></span></span>

<ul>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-Syntax.1" rel="nofollow">Syntax</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-Synopsis.1" rel="nofollow">Synopsis</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-Notes.1" rel="nofollow">Notes</a></span></li>
<li><span class="toc-item-body"><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-DynamicPartitionInserts" rel="nofollow">Dynamic Partition Inserts</a></span></span>
<ul>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-Example" rel="nofollow">Example</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-AdditionalDocumentation" rel="nofollow">Additional Documentation</a></span></li>
</ul>

<p></p></li>
</ul> <br>
</li>
<li><span class="toc-item-body"><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-Writingdataintothefilesystemfromqueries" rel="nofollow">Writing data into the filesystem from queries</a></span></span>

<ul>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-Syntax.2" rel="nofollow">Syntax</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-Synopsis.2" rel="nofollow">Synopsis</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-Notes.2" rel="nofollow">Notes</a></span></li>
</ul>

<p></p></li>
<li><span class="toc-item-body"><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-InsertingvaluesintotablesfromSQL" rel="nofollow">Inserting values into tables from SQL</a></span></span>

<ul>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-Syntax.3" rel="nofollow">Syntax</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-Synopsis.3" rel="nofollow">Synopsis</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-Examples" rel="nofollow">Examples</a></span></li>
</ul>

<p></p></li>
<li><span class="toc-item-body"><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-Update" rel="nofollow">Update</a></span></span>

<ul>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-Syntax.4" rel="nofollow">Syntax</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-Synopsis.4" rel="nofollow">Synopsis</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-Notes.3" rel="nofollow">Notes</a></span></li>
</ul>

<p></p></li>
<li><span class="toc-item-body"><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-Delete" rel="nofollow">Delete</a></span></span>

<ul>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-Syntax.5" rel="nofollow">Syntax</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-Synopsis.5" rel="nofollow">Synopsis</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-Notes.4" rel="nofollow">Notes</a></span></li>
</ul>

<p></p></li>
<li><span class="toc-item-body"><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-Merge" rel="nofollow">Merge</a></span></span>

<ul>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-Syntax.6" rel="nofollow">Syntax</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-Synopsis.6" rel="nofollow">Synopsis</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-PerformanceNote" rel="nofollow">Performance Note</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-Notes.5" rel="nofollow">Notes</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-Examples.1" rel="nofollow">Examples</a></span></li>
</ul>

<p></p></li>
</ul> <br>
</li>
</ul>

<p>　　详见<a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML" rel="nofollow" target="_blank">官方文档</a>。DML是对数据进行增删改的脚本。但是数据在hdfs中，实际上不提供修改，只提供追加（insert），支持删除。</p>

<h3 id="scroller-73"><span>1. Loading files into tables</span></h3>

<ul>
<li><strong>语法</strong></li>
</ul>

<p>　　load file将数据导入到表中。其语法为：</p>

<div class="cnblogs_code">
<pre>hive<span>&gt;</span> <span>LOAD</span> DATA <span>[</span><span>LOCAL</span><span>]</span> INPATH <span>'</span><span>filepath</span><span>'</span> <span>[</span><span>OVERWRITE</span><span>]</span> <span>INTO</span> <span>TABLE</span> tablename <span>[</span><span>PARTITION (partcol1=val1, partcol2=val2 ...)</span><span>]</span></pre>
</div>

<p>　　local：如果导入的数据在Linux主机本地，就要添加[local]，如果数据已经在hdfs上就不能加local；overwrite：覆盖；partition：如果表有分区就要加partition。</p>

<ul>
<li><strong>实例</strong>
<ul>
<li><strong>将hdfs中的数据装载到hive</strong>　　</li>
</ul>

<p></p></li>
</ul>

<p>　　首先，上传Linux文件到hdfs</p>

<div class="cnblogs_code">
<pre>$ hdfs dfs -put /root/log_2016-<span>03</span>-<span>1</span>* /usr/input</pre>
</div>

<p>　　其次，在hive中执行导入</p>

<div class="cnblogs_code">
<pre>hive&gt;load data inpath <span>'</span><span>/usr/input/log_2016-03-12.txt</span><span>'</span> into table t_log partition (day=<span>'</span><span>2016-03-12</span><span>'</span><span>);
</span></pre>
</div>

<p><strong><img src="https://images2018.cnblogs.com/blog/884765/201803/884765-20180311020040656-1042767998.png" alt="" width="579" height="150"></strong></p>

<div class="cnblogs_code">
<pre>hive&gt;load data inpath <span>'</span><span>/usr/input/log_2016-03-12.txt</span><span>'</span> overwrite into table t_log partition (day=<span>'</span><span>2016-03-12</span><span>'</span>);</pre>
</div>

<p><strong>　　注意：</strong>导入的分区要存在，导入到hive表后原来hdfs路径下的文件就不复存在了，即存在hdfs的目录变了，实际就是元数据变了，block的位置没有变。如果使用overwrite，则原来该表中指定分区的数据将全部清空，然后上传该文件。如果load相同文件名名到表中时，会默认生成副本filename_copy_1。文件中的数据格式要和表的结构相吻合。</p>

<ul>
<li>
<ul>
<li><strong>将Linux本地的数据装载到hive</strong></li>
</ul>

<p></p></li>
</ul>

<div class="cnblogs_code">
<pre>hive&gt;load data local inpath <span>'</span><span>/root/log_2016-03-11.txt</span><span>'</span> into table t_log partition (day=<span>'</span><span>2016-03-11</span><span>'</span>);</pre>
</div>

<h3 id="scroller-88"><span>2. Inserting data into hive tables from queries</span></h3>

<ul>
<li><strong>语法</strong></li>
</ul>

<div class="cnblogs_code">
<pre>hive<span>&gt;</span> <span>INSERT</span> OVERWRITE <span>TABLE</span> tablename1 <span>[</span><span>PARTITION (partcol1=val1, partcol2=val2 ...) [IF NOT EXISTS</span><span>]</span>] select_statement1 <span>FROM</span> from_statement;<br>hive<span>&gt;</span> <span>INSERT</span> <span>INTO</span> <span>TABLE</span> tablename1 <span>[</span><span>PARTITION (partcol1=val1, partcol2=val2 ...)</span><span>]</span> select_statement1 <span>FROM</span> from_statement;</pre>
</div>

<p>　　其中的select_statement1 FROM from_statement;是一个sql语句。这是用于查询hive中已经存在的数据，将查询结果给到一张新表中。</p>

<div class="cnblogs_code">
<pre>hive<span>&gt;</span> <span>INSERT</span> <span>INTO</span> <span>TABLE</span> tablename <span>[</span><span>PARTITION (partcol1[=val1</span><span>]</span>, partcol2<span>[</span><span>=val2</span><span>]</span> ...)] <span>VALUES</span> values_row <span>[</span><span>, values_row ...</span><span>]</span> </pre>
</div>

<p>　　这个语句是和关系型数据库的insert语句用法是一样的。区别有三：一是，有partition；二是，可以一次插入多条数据；三是，这种语句是转成MapReduce去执行的。</p>

<p>　　因为hive是一个工具，其作用是查询和统计hdfs上的数据的，对于查询结果一般就在控制台上显示，所以这个insert语句作用就是将查询的结果保存下来，用于后期的展示。</p>

<ul>
<li><strong>实例</strong></li>
</ul>

<div class="cnblogs_code">
<pre><span>hive</span><span>&gt;</span> <span>insert</span> <span>into</span> <span>table</span> t_log partition (<span>day</span><span>=</span>'<span>2016</span><span>-</span><span>03</span><span>-</span><span>13</span>’) <span>values</span> ('<span>888'</span>,<span>2</span>,'<span>100</span>','<span>2016</span><span>-</span><span>03</span><span>-</span><span>13</span>’); </pre>
</div>

<p>　　这个会启动MapReduce，因为数据在文件中不存在，要插到文件中相当于上传，会生成一个新的文件。这个不常用。所以实际上inset into语句的实际意义在于将查询结果放到新的表中。</p>

<h3 id="scroller-98"><span>3. Delete</span></h3>

<p>　　删除数据一般可以不在hive中删除，直接在hdfs上删除文件。因为删数据实际上就是删除文件。例如要删除之前插入到hive的t_log表day=2016-03-11分区下的<span>log_2016-03-11.txt</span>，可以执行如下命令：</p>

<div class="cnblogs_code">
<pre>#hdfs dfs -<span>rm</span> /user/hive/warehouse/t_log/day=<span>2016</span>-<span>03</span>-<span>11</span>/t_log-<span>2016</span>-<span>03</span>-<span>11.txt</span></pre>
</div>

<h2 id="scroller-101"><span class="toc-link">四、Hive UDF（Operators and User-Defined functions）</span></h2>

<ul>
<li><span class="toc-item-body"><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-HiveOperatorsandUser-DefinedFunctions(UDFs)" rel="nofollow">Hive Operators and User-Defined Functions (UDFs)</a></span></span>
<ul>
<li><span class="toc-item-body"><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-Built-inOperators" rel="nofollow">Built-in Operators</a></span></span>
<ul>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-OperatorsprecedencesOperatorsPrecedencesOperatorsPrecedences" rel="nofollow">Operators Precedences</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-RelationalOperators" rel="nofollow">Relational Operators</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-ArithmeticOperators" rel="nofollow">Arithmetic Operators</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-LogicalOperators" rel="nofollow">Logical Operators</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-StringOperators" rel="nofollow">String Operators</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-ComplexTypeConstructors" rel="nofollow">Complex Type Constructors</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-OperatorsonComplexTypes" rel="nofollow">Operators on Complex Types</a></span></li>
</ul>

<p></p></li>
<li><span class="toc-item-body"><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-Built-inFunctions" rel="nofollow">Built-in Functions</a></span></span>

<ul>
<li><span class="toc-item-body"><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" rel="nofollow">Mathematical Functions</a></span></span>
<ul>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctionsandOperatorsforDecimalDatatypes" rel="nofollow">Mathematical Functions and Operators for Decimal Datatypes</a></span></li>
</ul>

<p></p></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-CollectionFunctions" rel="nofollow">Collection Functions</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-TypeConversionFunctions" rel="nofollow">Type Conversion Functions</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-DateFunctions" rel="nofollow">Date Functions</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-ConditionalFunctions" rel="nofollow">Conditional Functions</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-StringFunctions" rel="nofollow">String Functions</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-DataMaskingFunctions" rel="nofollow">Data Masking Functions</a></span></li>
<li><span class="toc-item-body"><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-Misc.Functions" rel="nofollow">Misc. Functions</a></span></span>

<ul>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-xpath" rel="nofollow">xpath</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object" rel="nofollow">get_json_object</a></span></li>
</ul>

<p></p></li>
</ul> <br>
</li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-Built-inAggregateFunctions(UDAF)" rel="nofollow">Built-in Aggregate Functions (UDAF)</a></span></li>
<li><span class="toc-item-body"><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-Built-inTable-GeneratingFunctions(UDTF)" rel="nofollow">Built-in Table-Generating Functions (UDTF)</a></span></span>

<ul>
<li><span class="toc-item-body"><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-UsageExamples" rel="nofollow">Usage Examples</a></span></span>
<ul>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-explode(array)" rel="nofollow">explode (array)</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-explode(map)" rel="nofollow">explode (map)</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-posexplode(array)" rel="nofollow">posexplode (array)</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-inline(arrayofstructs)" rel="nofollow">inline (array of structs)</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-stack(values)" rel="nofollow">stack (values)</a></span></li>
</ul>

<p></p></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-explode" rel="nofollow">explode</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-posexplode" rel="nofollow">posexplode</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple" rel="nofollow">json_tuple</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-parse_url_tuple" rel="nofollow">parse_url_tuple</a></span></li>
</ul> <br>
</li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-GROUPingandSORTingonf(column)" rel="nofollow">GROUPing and SORTing on f(column)</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-UDFinternals" rel="nofollow">UDF internals</a></span></li>
<li><span class="toc-item-body"><a class="toc-link" href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-CreatingCustomUDFs" rel="nofollow">Creating Custom UDFs</a></span></li>
</ul> <br>
</li>
</ul>

<p>　　详见<a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF" rel="nofollow" target="_blank">官方文档</a>。</p>

<h3 id="scroller-104"><span>1. 内置运算符</span></h3>

<ul>
<li><strong>关系运算符</strong></li>
</ul>

<p>　　见文档</p>

<ul>
<li><strong>算术运算符</strong></li>
</ul>

<p>　　见文档</p>

<ul>
<li><strong>逻辑运算符</strong></li>
</ul>

<p>　　见文档</p>

<ul>
<li><strong>复杂类型构造函数（Complex Type Constructors）</strong>
<ul>
<li><strong>复杂类型函数或对象的构建</strong></li>
</ul>

<p></p></li>
</ul>

<p>　　【1】map（构造map对象）</p>

<p>　　Creates a map with the given key/value pairs. (key1,value1,key2,value2,…)</p>

<p>　　【2】struct（创建架构数组）</p>

<p>　　Creates a struct with the given field values. (val1,val2,val3,…)。Struct的属性名和列名相对应。struct可以认为是面向对象的对象类型，是一个构造体，里面可以定义属性的值，field的名称是预先定义的，就像Java类的属性预先定义好了。</p>

<p>　　【3】named_struct</p>

<p>　　Creates a struct with the given field names and values. (name1,val1,name2,val2, …)。根据给定的Struct的属性名来创建构建数组</p>

<p>　　【4】array（创建数组对象）</p>

<p>　　Creates an array with the given elements. (val1,val2,val3,…)。把各（若干）个字段的值变成一个array。</p>

<p>　　【5】create_union</p>

<p>　　Creates a union type with value that is being pointes to by the tag parameter.</p>

<ul>
<li>
<ul>
<li><strong>复杂类型函数的运算符</strong></li>
</ul>

<p></p></li>
</ul>

<p>　　【1】A[n]</p>

<p>　　A是array类型，n是int类型。A[n]表示数组A的第n+1个元素。</p>

<p>　　【2】M[key]</p>

<p>　　M是map类型，key是map集合中键的类型。M[key]是取map集合中键为key的值。注意使用是key需要使用单引号。</p>

<p>　　【3】S.x</p>

<p>　　S是struct类型，x表示struct中的某个属性。S.x，将会返回S这个struct中定义的x属性型中存储的值。</p>

<h3 id="scroller-129"><span>2. 内置函数</span></h3>

<ul>
<li><strong>数学函数</strong></li>
</ul>

<p><img src="https://images2018.cnblogs.com/blog/884765/201803/884765-20180311023914161-470827370.png" alt=""></p>

<ul>
<li><strong>集合操作函数</strong></li>
</ul>

<p><img src="https://images2018.cnblogs.com/blog/884765/201803/884765-20180311023945074-288253095.png" alt=""></p>

<ul>
<li><strong>类型转换函数</strong></li>
</ul>

<p><img src="https://images2018.cnblogs.com/blog/884765/201803/884765-20180311024006703-398736808.png" alt=""></p>

<ul>
<li><strong>日期函数</strong></li>
</ul>

<p><img src="https://images2018.cnblogs.com/blog/884765/201803/884765-20180311024032638-1879984478.png" alt=""></p>

<ul>
<li><strong>条件/逻辑函数</strong></li>
</ul>

<p> 　　略。</p>

<ul>
<li><strong>字符串函数</strong></li>
</ul>

<p><img src="https://images2018.cnblogs.com/blog/884765/201803/884765-20180311024105695-1424161606.png" alt=""></p>

<h3 id="scroller-142"><span>3. UDAF</span></h3>

<p>　　UDAF和UDF的区别在于传给函数的参数不一样。UDF表示传给函数的参数只有一个值或一行值；UDAF是给函数传多个值或者多行值的函数，例如count(*)，sum(col)等都是多行的值。</p>

<p>　　UDAF(User- Defined Aggregation Funcation) 聚集函数，多进一出，例如count/max/min等。</p>

<p><img src="https://images2018.cnblogs.com/blog/884765/201803/884765-20180311024316476-80921776.png" alt=""></p>

<h3 id="scroller-146"><span>4. UDTF</span></h3>

<p>　　详见文档。</p>

<h3 id="scroller-148"><span>5. UDF</span></h3>

<p>　　HivePlugins，Creating Custom UDFs。自定义函数需要以下几个步骤：首先要自定义一个类，继承UDF；其次，打成jar包；第三，将jar包加载到hive的classpath；第四，创建自定义函数；第五，使用函数。</p>

<p>　　例如，要创建一个将字符串转成日期的函数。</p>

<ul>
<li><strong>自定义类</strong></li>
</ul>

<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span>package</span><span> com.huidoo.hive;

</span><span>import</span><span> java.text.SimpleDateFormat;
</span><span>import</span><span> java.util.Date;

</span><span>import</span><span> org.apache.hadoop.hive.ql.exec.UDF;
</span><span>import</span><span> org.apache.hadoop.hive.serde2.io.TimestampWritable;
</span><span>import</span><span> org.apache.hadoop.io.Text;

</span><span>public</span> <span>class</span> StringToDate <span>extends</span><span> UDF {
    </span><span>/**</span><span>
     * 定义evaluate方法。返回值类型定义为TimestampWritable
     * </span><span>@param</span><span> str
     * </span><span>@param</span><span> datefmt
     * </span><span>@return</span><span>
     * </span><span>@throws</span><span> Exception 
     </span><span>*/</span>
    <span>public</span> TimestampWritable evaluate(<span>final</span> Text str,<span>final</span> Text datefmt) <span>throws</span><span> Exception{
        SimpleDateFormat sdf</span>= <span>new</span> SimpleDateFormat(datefmt.toString()); <span>//date</span><span>fmt是传入的格式化字符串，需要创建SimpleDateFormat对象，Text类型要转成Java中的String类型。</span>
        Date date= sdf.parse(str.toString()); <span>//</span><span>将字符串str，按照格式sdf转成java.util的Date类型</span>
        TimestampWritable time=<span>new</span> TimestampWritable();<span>//</span><span>创建Hadoop的时间戳对象</span>
        time.setTime(date.getTime());<span>//</span><span>将date转成Timestamp类型，然后使用Hadoop时间戳对象设置成相应的时间。</span>
        <span>return</span><span> time;
    }
}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<ul>
<li><strong>打成jar包并上传到服务器</strong></li>
</ul>
<p>　　略。std.jar</p>
<ul>
<li><strong>将jar包加载到hive的classpath</strong></li>
</ul>
<div class="cnblogs_code">
<pre>hive<span>&gt;</span> <span>add</span> jar <span>/</span>path<span>/</span><span>to</span><span>/</span>std.jar;</pre>
</div>

<ul>
<li><strong> 创建函数</strong></li>
</ul>

<p>　　语法：create 函数名(不能与内置的一样) as ‘自定义类的全限定名’;</p>

<div class="cnblogs_code">
<pre>hive<span>&gt;</span> <span>create</span> <span>function</span> std <span>as</span> 'com.huidoo.hive.StringToDate';</pre>
</div>

<p>　　这就创建了一个函数，名为std。</p>

<ul>
<li><strong>使用函数</strong></li>
</ul>

<div class="cnblogs_code">
<pre>hive<span>&gt;</span> <span>select</span> std(<span>'</span><span>2016-02-06 01:19:30</span><span>'</span>,<span>'</span><span>yyyy-MM-dd HH:mm:ss</span><span>'</span>) <span>from</span> t_log limit <span>3</span>;</pre>
</div>

<h2 id="scroller-163"><span>五、Hive Select</span></h2>

<h3 id="scroller-164"><span>1. 简单查询和使用/切换数据库</span></h3>

<div class="cnblogs_code">
<pre>hive&gt; <span>select <span>* <span>from table_name;</span></span></span></pre>
</div>

<p>　　查看当前数据库：</p>

<div class="cnblogs_code">
<pre>hive<span>&gt;</span> <span>SELECT</span> current_database();</pre>
</div>

<p>　　“db_name.table_name” allows a query to access tables in different databases.可以查询不同数据库中的表。</p>

<p>　　使用某数据库：</p>

<div class="cnblogs_code">
<pre>hive<span>&gt;</span> <span>USE</span> database_name;</pre>
</div>

<h3 id="scroller-171"><span>2. where从句</span></h3>

<div class="cnblogs_code">
<pre>hive<span>&gt;</span><span>SELECT</span> <span>*</span> <span>FROM</span> sales <span>WHERE</span> amount <span>&gt;</span> <span>10</span> <span>AND</span> region <span>=</span> "US"</pre>
</div>

<h3 id="scroller-173"><span>3. ALL and DISTINCT从句</span></h3>

<p>　　使用distinct关键字去重查询，如下列出两个查询的区别：</p>

<div class="cnblogs_code">
<pre>hive<span>&gt; </span><span>SELECT</span> col1, col2 <span>FROM</span> t1</pre>
</div>

<p>    1 3</p>

<p>    1 3</p>

<p>    1 4</p>

<p>    2 5</p>

<div class="cnblogs_code">
<pre>hive<span>&gt;</span> <span>SELECT</span> <span>DISTINCT</span> col1, col2 <span>FROM</span> t1</pre>
</div>

<p>    1 3</p>

<p>    1 4</p>

<p>    2 5</p>

<h3 id="scroller-184"><span>4. Join从句</span></h3>

<p>　　join用于表连接。两个表即两个文件能连接起来就一定有相同点，这就是说其中一个文件中某域的值和另一个文件的某域的值相同，就可以连接。</p>

<div class="cnblogs_code">
<pre>hive<span>&gt;</span> <span>select</span> col1,col2,col3 <span>from</span> t1 <span>join</span> t2 <span>on</span><span>+</span>条件;</pre>
</div>

<h3 id="scroller-187"><span>5. 基于Partition的查询</span></h3>

<p>　　这种查询语法就只要把partition当成一个字段即可。如果查询时要引入外键关联表也可以使用JION…ON语句。</p>

<ul>
<li><strong>非关联查询</strong></li>
</ul>

<p>　　语法：</p>

<div class="cnblogs_code">
<pre>hive<span>&gt;</span> <span>select</span> table_name.<span>*</span> <span>from</span> table_name <span>where</span> table_name.partition_name <span>+</span>条件;</pre>
</div>

<p>　　案例：</p>

<div class="cnblogs_code">
<pre><span>SELECT</span> page_views.<span>*</span>
<span>FROM</span><span> page_views
</span><span>WHERE</span> page_views.date <span>&gt;=</span> <span>'</span><span>2016-03-01</span><span>'</span> <span>AND</span> page_views.date <span>&lt;= <span>'<span>2016-03-31<span>'</span></span></span></span>；</pre>
</div>

<ul>
<li><strong>关联查询</strong></li>
</ul>

<p>　　语法：</p>

<div class="cnblogs_code">
<pre>hive<span>&gt;</span> <span>select</span> table_name1.<span>*</span> <span>from</span> table_name1 <span>join</span> table_name2 <span>on</span> (table_name1.外键 <span>=</span> table_name2.主键 <span>And</span> table_name1.partiton_name <span>+</span>条件);</pre>
</div>

<p>　　案例：</p>

<div class="cnblogs_code">
<pre><span>SELECT</span> page_views.<span>*</span>
<span>FROM</span> page_views <span>JOIN</span><span> dim_users 
</span><span>ON</span> (page_views.<span>user_id</span> <span>=</span> dim_users.id <span>AND</span> page_views.date <span>&gt;=</span> <span>'</span><span>2016-03-01</span><span>'</span> <span>AND</span> page_views.date <span>&lt;=</span> <span>'</span><span>2016-03-31</span><span>'</span>);</pre>
</div>

<h3 id="scroller-199"><span>6. Group by从句</span></h3>

<div class="cnblogs_code">
<pre>hive<span>&gt;</span> <span>select</span> col<span>[</span><span>list</span><span>]</span> <span>from</span> table_name <span>group</span> <span>by</span> col;</pre>
</div>

<h3 id="scroller-201"><span>7. Order/Sort by从句</span></h3>

<p>　　sort by和order by的用法基本一致，都是做排序的，一般用order by。例如：</p>

<div class="cnblogs_code">
<pre>hive<span>&gt;</span> <span>select</span> <span>*</span> <span>from</span> t_log <span>order</span> <span>by</span> id;</pre>
</div>

<h3 id="scroller-204"><span>8. Having从句</span></h3>

<p>　　having从句必须依附于group by从句，在group从句后面接having从句，相当于增加一个条件。having后面可以接聚合函数，例如sum、avg等。</p>

<ul>
<li><strong>单纯的group by语句</strong></li>
</ul>

<div class="cnblogs_code">
<pre>hive<span>&gt;</span> <span>select</span> <span>day</span> <span>from</span> t_log <span>group</span> <span>by</span> <span>day</span>;</pre>
</div>

<p>　　其查询结果为：</p>

<p>　　2016-03-11</p>

<p>　　2016-03-12</p>

<ul>
<li><strong>添加having从句</strong></li>
</ul>

<div class="cnblogs_code">
<pre>hive<span>&gt;</span><span>select</span> <span>day</span> <span>from</span> t_log <span>group</span> <span>by</span> <span>day</span> <span>having</span> <span>avg</span>(id)<span>&gt;</span><span>150</span>;</pre>
</div>

<p>　　其查询结果为：</p>

<p>　　2016-03-12</p>

<ul>
<li><strong>hiving同效于子查询</strong></li>
</ul>

<div class="cnblogs_code">
<pre>hive<span>&gt;</span> <span>select</span> <span>day</span> <span>from</span> (<span>select</span> <span>day</span>, <span>avg</span>(id) <span>as</span> avgid <span>from</span> t_log <span>group</span> <span>by</span> <span>day</span>) table2 <span>where</span> table2.avgid<span>&gt;</span><span>150</span>;</pre>
</div>

<p>　　这里将t_log表通过select day, avg(id) as avgid from t_log group by day查询得到的结果作为表再查询的。和上述的hiving从句得到的结果一样。</p>

<h3 id="scroller-218"><span>9. Limit从句</span></h3>

<p>　　限定查询返回的行数。</p>

<div class="cnblogs_code">
<pre>hive<span>&gt;</span> <span>select</span> <span>*</span> <span>from</span> t_log limit <span>5</span>;</pre>
</div>

<p></p><p>　　原文发表于2018-03-11 11:20，原文地址：<a href="http://www.cnblogs.com/yangp/p/8541345.html" rel="nofollow">http://www.cnblogs.com/yangp/p/8541345.html</a></p></div>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>