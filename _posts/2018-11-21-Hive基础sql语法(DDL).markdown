---
layout:     post
title:      Hive基础sql语法(DDL)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：					https://blog.csdn.net/wangkun_j/article/details/80714962				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><strong>前言：</strong></p>

<ul>
<li>经过前面的学习 我们了解到Hive可以使用关系型数据库来存储元数据，而且Hive提供了比较完整的SQL功能 ，这篇文章主要介绍Hive基本的sql语法。</li>
</ul>

<p>首先了解下Hive的数据存储结构，抽象图如下： <br>
<img src="https://upload-images.jianshu.io/upload_images/11096896-36185c3c7f16fcf0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" alt="Hive存储.png" title=""></p>

<ul>
<li>1.Database:Hive中包含了多个数据库，默认的数据库为default，对应于HDFS目录是/user/hadoop/hive/warehouse，可以通过hive.metastore.warehouse.dir参数进行配置（hive-site.xml中配置） </li>
<li>2.Table: Hive 中的表又分为内部表和外部表 ,Hive 中的每张表对应于HDFS上的一个目录，HDFS目录为：/user/hadoop/hive/warehouse/[databasename.db]/table </li>
<li>3.Partition:分区，每张表中可以加入一个分区或者多个，方便查询，提高效率；并且HDFS上会有对应的分区目录：  <br>
/user/hadoop/hive/warehouse/[databasename.db]/table</li>
<li>4.Bucket(桶)：暂且不讲</li>
</ul>



<h4 id="ddl操作data-definition-language">DDL操作(Data Definition Language)</h4>

<p><em>参考官方文档：</em> <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL" rel="nofollow">DDL文档</a> <br>
HiveQL DDL statements are documented here, including:</p>

<ul>
<li>CREATE DATABASE/SCHEMA, TABLE, VIEW, FUNCTION, INDEX</li>
<li>DROP DATABASE/SCHEMA, TABLE, VIEW, INDEX</li>
<li>TRUNCATE TABLE</li>
<li>ALTER DATABASE/SCHEMA, TABLE, VIEW</li>
<li>MSCK REPAIR TABLE (or ALTER TABLE RECOVER PARTITIONS)</li>
<li>SHOW DATABASES/SCHEMAS, TABLES, TBLPROPERTIES, VIEWS, PARTITIONS, FUNCTIONS, INDEX[ES], COLUMNS, CREATE TABLE</li>
<li>DESCRIBE DATABASE/SCHEMA, table_name, view_name</li>
</ul>



<h4 id="一基于数据库的ddl操作">一.基于数据库的DDL操作</h4>



<h5 id="1创建数据库create-database">1.创建数据库（Create Database）</h5>

<ul>
<li>下面是官网上为我们列出的语法：</li>
</ul>



<pre class="prettyprint"><code class="language-sql hljs "><span class="hljs-operator"><span class="hljs-keyword">Create</span> <span class="hljs-keyword">Database</span>
<span class="hljs-keyword">CREATE</span> (<span class="hljs-keyword">DATABASE</span>|<span class="hljs-keyword">SCHEMA</span>) [<span class="hljs-keyword">IF</span> <span class="hljs-keyword">NOT</span> <span class="hljs-keyword">EXISTS</span>] database_name
  [COMMENT database_comment]
  [LOCATION hdfs_path]
  [<span class="hljs-keyword">WITH</span> DBPROPERTIES (property_name=property_value, ...)];</span></code></pre>

<p><strong>IF NOT EXISTS</strong>：加上这句话代表判断数据库是否存在，不存在就会创建，存在就不会创建（生产环境建议使用）。  <br>
<strong>COMMENT</strong>：数据库的描述  <br>
<strong>LOCATION</strong>：创建数据库的地址，不加默认在/user/hive/warehouse/路径下  <br>
<strong>WITH DBPROPERTIES</strong>：数据库的属性</p>



<pre class="prettyprint"><code class="language-shell hljs markdown">hive&gt; CREATE DATABASE hive1;
OK
hive&gt; CREATE DATABASE IF NOT EXISTS hive2
<span class="hljs-code">    &gt; COMMENT "this is ruoze database"</span>
<span class="hljs-code">    &gt; WITH DBPROPERTIES ("creator"="ruoze", "date"="2018-08-08");</span>
OK
hive&gt; CREATE DATABASE hive3 LOCATION '/db_hive3';
OK
hive&gt; show databases;
OK
default
hive1
hive2
hive3

<span class="hljs-header"># 在HDFS中查看数据库文件夹</span>
[hadoop@hadoop000 ~]$ hadoop fs -ls /user/hive/warehouse
Found 2 items
drwxr-xr-x   - hadoop supergroup          0 2018-06-16 15:26 /user/hive/warehouse/hive1.db
drwxr-xr-x   - hadoop supergroup          0 2018-06-16 15:28 /user/hive/warehouse/hive2.db
[hadoop@hadoop000 ~]$ hadoop fs -ls /
Found 3 items
drwxr-xr-x   - hadoop supergroup          0 2018-06-16 15:29 /db_hive3
drwx-wx-wx   - hadoop supergroup          0 2018-06-03 15:57 /tmp
drwxr-xr-x   - hadoop supergroup          0 2018-06-03 16:43 /user

<span class="hljs-header"># 在RDBMS中查看数据库相关信息</span>
mysql&gt; select * from hive_meta.dbs\G;
<span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">** 1. row **</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span>
<span class="hljs-code">          DB_ID: 1</span>
<span class="hljs-code">           DESC: Default Hive database</span>
DB<span class="hljs-emphasis">_LOCATION_</span>URI: hdfs://hadoop000:9000/user/hive/warehouse
<span class="hljs-code">           NAME: default</span>
<span class="hljs-code">     OWNER_NAME: public</span>
<span class="hljs-code">     OWNER_TYPE: ROLE</span>
<span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">** 2. row **</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span>
<span class="hljs-code">          DB_ID: 6</span>
<span class="hljs-code">           DESC: NULL</span>
DB<span class="hljs-emphasis">_LOCATION_</span>URI: hdfs://hadoop000:9000/user/hive/warehouse/hive1.db
<span class="hljs-code">           NAME: hive1</span>
<span class="hljs-code">     OWNER_NAME: hadoop</span>
<span class="hljs-code">     OWNER_TYPE: USER</span>
<span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">** 3. row **</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span>
<span class="hljs-code">          DB_ID: 7</span>
<span class="hljs-code">           DESC: this is ruoze database</span>
DB<span class="hljs-emphasis">_LOCATION_</span>URI: hdfs://hadoop000:9000/user/hive/warehouse/hive2.db
<span class="hljs-code">           NAME: hive2</span>
<span class="hljs-code">     OWNER_NAME: hadoop</span>
<span class="hljs-code">     OWNER_TYPE: USER</span>
<span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">** 4. row **</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span>
<span class="hljs-code">          DB_ID: 8</span>
<span class="hljs-code">           DESC: NULL</span>
DB<span class="hljs-emphasis">_LOCATION_</span>URI: hdfs://hadoop000:9000/db_hive3
<span class="hljs-code">           NAME: hive3</span>
<span class="hljs-code">     OWNER_NAME: hadoop</span>
<span class="hljs-code">     OWNER_TYPE: USER</span>
4 rows in set (0.00 sec)</code></pre>



<h5 id="2查询数据库show-databases">2.查询数据库（Show Databases）</h5>

<ul>
<li>下面是官网上为我们列出的语法：</li>
</ul>



<pre class="prettyprint"><code class="language-sql hljs "><span class="hljs-operator"><span class="hljs-keyword">SHOW</span> (DATABASES|SCHEMAS) [<span class="hljs-keyword">LIKE</span> <span class="hljs-string">'identifier_with_wildcards'</span>];</span></code></pre>



<pre class="prettyprint"><code class="language-shell hljs vhdl">hive&gt; show databases;
OK
<span class="hljs-keyword">default</span>
hive1
hive2
hive3
<span class="hljs-typename">Time</span> taken: <span class="hljs-number">0.047</span> seconds, Fetched: <span class="hljs-number">4</span> row(s)
hive&gt; show databases like <span class="hljs-attribute">'hive1</span>';
OK
hive1
<span class="hljs-typename">Time</span> taken: <span class="hljs-number">0.035</span> seconds, Fetched: <span class="hljs-number">1</span> row(s)
hive&gt; show databases like <span class="hljs-attribute">'hive</span>*';
OK
hive1
hive2
hive3
<span class="hljs-typename">Time</span> taken: <span class="hljs-number">0.037</span> seconds, Fetched: <span class="hljs-number">3</span> row(s)</code></pre>



<h5 id="3查询数据库信息describe-database">3.查询数据库信息（Describe Database）</h5>

<ul>
<li>下面是官网上为我们列出的语法：</li>
</ul>



<pre class="prettyprint"><code class="language-sql hljs ">DESCRIBE DATABASE [EXTENDED] db_name;
<span class="hljs-comment">--describe 可简写为desc</span></code></pre>

<p><strong>DESCRIBE DATABASE db_name</strong>：查看数据库的描述信息和文件目录位置路径信息；  <br>
<strong>EXTENDED</strong>：加上数据库键值对的属性信息。</p>



<pre class="prettyprint"><code class="language-shell hljs axapta">hive&gt; <span class="hljs-keyword">desc</span> database hive1;
OK
hive1           hdfs:<span class="hljs-comment">//192.168.6.217:9000/user/hive/warehouse/hive1.db  hadoop  USER</span>
Time taken: <span class="hljs-number">0.039</span> seconds, Fetched: <span class="hljs-number">1</span> row(s)
hive&gt;  <span class="hljs-keyword">desc</span> database hive2;
OK
hive2   <span class="hljs-keyword">this</span> is ruoze database  hdfs:<span class="hljs-comment">//192.168.6.217:9000/user/hive/warehouse/hive2.db  hadoop  USER</span>
Time taken: <span class="hljs-number">0.041</span> seconds, Fetched: <span class="hljs-number">1</span> row(s)
hive&gt;  <span class="hljs-keyword">desc</span> database hive3;
OK
hive3           hdfs:<span class="hljs-comment">//192.168.6.217:9000/db_hive3      hadoop  USER</span>
Time taken: <span class="hljs-number">0.046</span> seconds, Fetched: <span class="hljs-number">1</span> row(s)
hive&gt;  <span class="hljs-keyword">desc</span> database extended  hive2;
OK
hive2   <span class="hljs-keyword">this</span> is ruoze database  hdfs:<span class="hljs-comment">//192.168.6.217:9000/user/hive/warehouse/hive2.db  hadoop  USER    {date=2018-08-08, creator=ruoze}</span>
Time taken: <span class="hljs-number">0.031</span> seconds, Fetched: <span class="hljs-number">1</span> row(s)</code></pre>



<h5 id="4删除数据库drop-database">4.删除数据库（Drop Database）</h5>

<ul>
<li>下面是官网上为我们列出的语法：</li>
</ul>



<pre class="prettyprint"><code class="language-sql hljs "><span class="hljs-operator"><span class="hljs-keyword">DROP</span> (<span class="hljs-keyword">DATABASE</span>|<span class="hljs-keyword">SCHEMA</span>) [<span class="hljs-keyword">IF</span> <span class="hljs-keyword">EXISTS</span>] database_name [<span class="hljs-keyword">RESTRICT</span>|<span class="hljs-keyword">CASCADE</span>];</span></code></pre>

<p><strong>RESTRICT</strong>：默认是restrict，如果该数据库还有表存在则报错；  <br>
<strong>CASCADE</strong>：级联删除数据库(当数据库还有表时，级联删除表后再删除数据库) –生产尽量不用。</p>



<pre class="prettyprint"><code class="language-shell hljs rust">hive&gt; <span class="hljs-keyword">drop</span> database test;
OK
Time taken: <span class="hljs-number">0.094</span> seconds</code></pre>



<h5 id="5修改数据库信息alter-database">5.修改数据库信息（Alter Database）</h5>

<ul>
<li>下面是官网上为我们列出的语法：</li>
</ul>



<pre class="prettyprint"><code class="language-sql hljs "><span class="hljs-operator"><span class="hljs-keyword">ALTER</span> (<span class="hljs-keyword">DATABASE</span>|<span class="hljs-keyword">SCHEMA</span>) database_name <span class="hljs-keyword">SET</span> DBPROPERTIES (property_name=property_value, ...);</span>   <span class="hljs-comment">-- (Note: SCHEMA added in Hive 0.14.0)</span>

<span class="hljs-operator"><span class="hljs-keyword">ALTER</span> (<span class="hljs-keyword">DATABASE</span>|<span class="hljs-keyword">SCHEMA</span>) database_name <span class="hljs-keyword">SET</span> OWNER [<span class="hljs-keyword">USER</span>|ROLE] user_or_role;</span>  
 <span class="hljs-comment">-- (Note: Hive 0.13.0 and later; SCHEMA added in Hive 0.14.0)</span>

<span class="hljs-operator"><span class="hljs-keyword">ALTER</span> (<span class="hljs-keyword">DATABASE</span>|<span class="hljs-keyword">SCHEMA</span>) database_name <span class="hljs-keyword">SET</span> LOCATION hdfs_path;</span> 
<span class="hljs-comment">-- (Note: Hive 2.2.1, 2.4.0 and later)</span></code></pre>

<p>（Note：表示对于版本进行的修改）</p>



<pre class="prettyprint"><code class="language-shell hljs cs">hive&gt; alter database hive2 <span class="hljs-keyword">set</span> dbproperties (<span class="hljs-string">"update"</span>=<span class="hljs-string">"jepson"</span>);
OK
Time taken: <span class="hljs-number">0.094</span> seconds
hive&gt; alter database hive2 <span class="hljs-keyword">set</span> owner user hive;
OK
Time taken: <span class="hljs-number">0.072</span> seconds

<span class="hljs-preprocessor"># 修改前</span>
hive&gt;  desc database extended  hive2;
OK
hive2   <span class="hljs-keyword">this</span> <span class="hljs-keyword">is</span> ruoze database  hdfs:<span class="hljs-comment">//192.168.6.217:9000/user/hive/warehouse/hive2.db  hadoop  USER    {date=2018-08-08, creator=ruoze}</span>
Time taken: <span class="hljs-number">0.031</span> seconds, Fetched: <span class="hljs-number">1</span> row(s)
<span class="hljs-preprocessor"># 修改后</span>
hive&gt; desc database extended  hive2;
OK
hive2   <span class="hljs-keyword">this</span> <span class="hljs-keyword">is</span> ruoze database  hdfs:<span class="hljs-comment">//192.168.6.217:9000/user/hive/warehouse/hive2.db  hive    USER    {update=jepson, date=2018-08-08, creator=ruoze}</span>
Time taken: <span class="hljs-number">0.034</span> seconds, Fetched: <span class="hljs-number">1</span> row(s)</code></pre>



<h5 id="6切换数据库use-database">6.切换数据库（Use Database）</h5>

<ul>
<li>下面是官网上为我们列出的语法：</li>
</ul>



<pre class="prettyprint"><code class="language-sql hljs ">USE database_name;</code></pre>



<pre class="prettyprint"><code class="language-shell hljs vhdl">hive&gt; <span class="hljs-keyword">use</span> hive1;
OK
<span class="hljs-typename">Time</span> taken: <span class="hljs-number">0.044</span> seconds
hive&gt; <span class="hljs-keyword">use</span> <span class="hljs-keyword">default</span>;
OK
<span class="hljs-typename">Time</span> taken: <span class="hljs-number">0.047</span> seconds</code></pre>



<h4 id="二基于表的ddl操作">二.基于表的DDL操作</h4>



<h5 id="1创建表create-table">1.创建表（Create Table）</h5>

<ul>
<li>下面是官网上为我们列出的语法：</li>
</ul>



<pre class="prettyprint"><code class="language-sql hljs "><span class="hljs-operator"><span class="hljs-keyword">CREATE</span> [<span class="hljs-keyword">TEMPORARY</span>] [<span class="hljs-keyword">EXTERNAL</span>] <span class="hljs-keyword">TABLE</span> [<span class="hljs-keyword">IF</span> <span class="hljs-keyword">NOT</span> <span class="hljs-keyword">EXISTS</span>] [db_name.]table_name    -- (Note: <span class="hljs-keyword">TEMPORARY</span> available <span class="hljs-keyword">in</span> Hive <span class="hljs-number">0.14</span><span class="hljs-number">.0</span> <span class="hljs-keyword">and</span> later)
  [(col_name data_type [COMMENT col_comment], ... [constraint_specification])]
  [COMMENT table_comment]
  [PARTITIONED <span class="hljs-keyword">BY</span> (col_name data_type [COMMENT col_comment], ...)]
  [CLUSTERED <span class="hljs-keyword">BY</span> (col_name, col_name, ...) [SORTED <span class="hljs-keyword">BY</span> (col_name [<span class="hljs-keyword">ASC</span>|<span class="hljs-keyword">DESC</span>], ...)] <span class="hljs-keyword">INTO</span> num_buckets BUCKETS]
  [SKEWED <span class="hljs-keyword">BY</span> (col_name, col_name, ...)                  -- (Note: Available <span class="hljs-keyword">in</span> Hive <span class="hljs-number">0.10</span><span class="hljs-number">.0</span> <span class="hljs-keyword">and</span> later)]
     <span class="hljs-keyword">ON</span> ((col_value, col_value, ...), (col_value, col_value, ...), ...)
     [STORED <span class="hljs-keyword">AS</span> DIRECTORIES]
  [
   [<span class="hljs-keyword">ROW</span> FORMAT row_format] 
   [STORED <span class="hljs-keyword">AS</span> file_format]
     | STORED <span class="hljs-keyword">BY</span> <span class="hljs-string">'storage.handler.class.name'</span> [<span class="hljs-keyword">WITH</span> SERDEPROPERTIES (...)]  -- (Note: Available <span class="hljs-keyword">in</span> Hive <span class="hljs-number">0.6</span><span class="hljs-number">.0</span> <span class="hljs-keyword">and</span> later)
  ]
  [LOCATION hdfs_path]
  [TBLPROPERTIES (property_name=property_value, ...)]   -- (Note: Available <span class="hljs-keyword">in</span> Hive <span class="hljs-number">0.6</span><span class="hljs-number">.0</span> <span class="hljs-keyword">and</span> later)
  [<span class="hljs-keyword">AS</span> select_statement];</span>   <span class="hljs-comment">-- (Note: Available in Hive 0.5.0 and later; not supported for external tables)</span></code></pre>



<h6 id="11temporary临时表">1.1.TEMPORARY（临时表）</h6>

<ul>
<li>Hive从0.14.0开始提供创建临时表的功能，表只对当前session有效，session退出后，表自动删除。 </li>
</ul>

<p>语法：</p>



<pre class="prettyprint"><code class="language-sql hljs "><span class="hljs-operator"><span class="hljs-keyword">CREATE</span> <span class="hljs-keyword">TEMPORARY</span> <span class="hljs-keyword">TABLE</span> ...</span></code></pre>

<p><strong>注意点：</strong>  <br>
1. 如果创建的临时表表名已存在，那么当前session引用到该表名时实际用的是临时表，只有drop或rename临时表名才能使用原始表；  <br>
2. 临时表限制：不支持分区字段和创建索引。</p>



<pre class="prettyprint"><code class="language-shell hljs vhdl">hive&gt; <span class="hljs-keyword">use</span> <span class="hljs-keyword">default</span>;
OK
<span class="hljs-typename">Time</span> taken: <span class="hljs-number">0.047</span> seconds
hive&gt; CREATE TEMPORARY TABLE temporary_table (
    &gt; id int,
    &gt; name <span class="hljs-typename">string</span>);
OK
<span class="hljs-typename">Time</span> taken: <span class="hljs-number">0.242</span> seconds
hive&gt; show tables;
OK
temporary_table
<span class="hljs-typename">Time</span> taken: <span class="hljs-number">0.044</span> seconds, Fetched: <span class="hljs-number">1</span> row(s)

# 退出重新进
hive&gt; <span class="hljs-keyword">use</span> <span class="hljs-keyword">default</span>;
OK
<span class="hljs-typename">Time</span> taken: <span class="hljs-number">1.054</span> seconds
hive&gt; show tables;
OK
<span class="hljs-typename">Time</span> taken: <span class="hljs-number">0.559</span> seconds</code></pre>



<h6 id="12managed-and-external-tables内部表和外部表">1.2.Managed and External Tables（内部表和外部表）</h6>

<ul>
<li>Hive上有两种类型的表，一种是Managed Table(默认的)，另一种是External Table（加上EXTERNAL关键字）。它俩的主要区别在于：当我们drop表时，Managed Table会同时删去data（存储在HDFS上）和meta data（存储在MySQL），而External Table只会删meta data。</li>
</ul>



<pre class="prettyprint"><code class="language-shell hljs markdown">hive&gt; use default;
OK
Time taken: 1.054 seconds
hive&gt; show tables;
OK
Time taken: 0.559 seconds
<span class="hljs-header"># 创建内部表和外部表</span>
hive&gt; create table managed_table(
<span class="hljs-code">    &gt; id int,</span>
<span class="hljs-code">    &gt; name string </span>
<span class="hljs-code">    &gt; );</span>
OK
Time taken: 0.677 seconds
hive&gt; create external table external_table(
<span class="hljs-code">    &gt; id int,</span>
<span class="hljs-code">    &gt; name string </span>
<span class="hljs-code">    &gt; );</span>
OK
Time taken: 0.146 seconds
hive&gt; show tables;
OK
external_table
managed_table
Time taken: 0.05 seconds, Fetched: 2 row(s)
<span class="hljs-header"># HDFS中查看</span>
[hadoop@hadoop000 ~]$ hadoop fs -ls /user/hive/warehouse
Found 4 items
drwxr-xr-x   - hadoop supergroup          0 2018-06-16 16:40 /user/hive/warehouse/external_table
drwxr-xr-x   - hadoop supergroup          0 2018-06-16 15:26 /user/hive/warehouse/hive1.db
drwxr-xr-x   - hadoop supergroup          0 2018-06-16 15:28 /user/hive/warehouse/hive2.db
drwxr-xr-x   - hadoop supergroup          0 2018-06-16 16:39 /user/hive/warehouse/managed_table
<span class="hljs-header"># MySQL中查看</span>
mysql&gt; select * from hive_meta.tbls\G;
<span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">** 1. row **</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span>
<span class="hljs-code">            TBL_ID: 11</span>
<span class="hljs-code">       CREATE_TIME: 1529138399</span>
<span class="hljs-code">             DB_ID: 1</span>
  LAST<span class="hljs-emphasis">_ACCESS_</span>TIME: 0
<span class="hljs-code">             OWNER: hadoop</span>
<span class="hljs-code">         RETENTION: 0</span>
<span class="hljs-code">             SD_ID: 11</span>
<span class="hljs-code">          TBL_NAME: managed_table</span>
<span class="hljs-code">          TBL_TYPE: MANAGED_TABLE</span>
VIEW<span class="hljs-emphasis">_EXPANDED_</span>TEXT: NULL
VIEW<span class="hljs-emphasis">_ORIGINAL_</span>TEXT: NULL
<span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">** 2. row **</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span><span class="hljs-strong">*****</span>
<span class="hljs-code">            TBL_ID: 12</span>
<span class="hljs-code">       CREATE_TIME: 1529138409</span>
<span class="hljs-code">             DB_ID: 1</span>
  LAST<span class="hljs-emphasis">_ACCESS_</span>TIME: 0
<span class="hljs-code">             OWNER: hadoop</span>
<span class="hljs-code">         RETENTION: 0</span>
<span class="hljs-code">             SD_ID: 12</span>
<span class="hljs-code">          TBL_NAME: external_table</span>
<span class="hljs-code">          TBL_TYPE: EXTERNAL_TABLE</span>
VIEW<span class="hljs-emphasis">_EXPANDED_</span>TEXT: NULL
VIEW<span class="hljs-emphasis">_ORIGINAL_</span>TEXT: NULL
2 rows in set (0.00 sec)

<span class="hljs-header"># 删除内部表和外部表</span>
hive&gt; drop table managed_table;
OK
Time taken: 1.143 seconds
hive&gt; drop table external_table;
OK
Time taken: 0.265 seconds
<span class="hljs-header"># 再次查看</span>
[hadoop@hadoop000 ~]$ hadoop fs -ls /user/hive/warehouse
Found 3 items
drwxr-xr-x   - hadoop supergroup          0 2018-06-16 16:40 /user/hive/warehouse/external_table
drwxr-xr-x   - hadoop supergroup          0 2018-06-16 15:26 /user/hive/warehouse/hive1.db
drwxr-xr-x   - hadoop supergroup          0 2018-06-16 15:28 /user/hive/warehouse/hive2.db
mysql&gt; select * from hive_meta.tbls\G;
Empty set (0.00 sec)

ERROR: 
No query specified</code></pre>



<h6 id="13commentrow-format等其他建表参数">1.3.COMMENT,ROW FORMAT等其他建表参数</h6>

<p><strong>COMMENT</strong> ：注释 可以给字段和表加注释</p>

<p>先看看官网对于ROW FORMAT的描述</p>



<pre class="prettyprint"><code class="language-sql hljs ">: DELIMITED 
[FIELDS TERMINATED BY char [ESCAPED BY char]]       [COLLECTION ITEMS TERMINATED BY char]
[MAP KEYS TERMINATED BY char] 
[LINES TERMINATED BY char]
[NULL DEFINED AS char]   
<span class="hljs-comment">-- (Note: Available in Hive 0.13 and later)</span>
  | SERDE serde_name [WITH SERDEPROPERTIES (property_name=property_value, property_name=property_value, ...)]</code></pre>

<ul>
<li><p>先看看官网给我们的解释：用户在建表的时候可以自定义 SerDe 或者使用自带的 SerDe。如果没有指定 ROW FORMAT 或者 ROW FORMAT DELIMITED，将会使用自带的 SerDe。在建表的时候，用户还需要为表指定列，用户在指定表的列的同时也会指定自定义的 SerDe，Hive 通过 SerDe 确定表的具体的列的数据。</p></li>
<li><p>那么问题又来了上面这句话又是什么意思呢？  <br>
让我们来一起看看到底是神马东东：  <br>
<strong>DELIMITED</strong>：分隔符（可以自定义分隔符）；  <br>
<strong>FIELDS TERMINATED BY char</strong>:每个字段之间使用的分割；  <br>
例：-FIELDS TERMINATED BY ‘\n’ 字段之间的分隔符为\n;  <br>
<strong>COLLECTION ITEMS TERMINATED BY char</strong>:集合中元素与元素（array）之间使用的分隔符（collection单例集合的跟接口）；  <br>
<strong>MAP KEYS TERMINATED BY char</strong>：字段是K-V形式指定的分隔符；  <br>
<strong>LINES TERMINATED BY char</strong>：每条数据之间由换行符分割（默认[ \n ]）。</p></li>
<li><p>一般情况下LINES TERMINATED BY char我们就使用默认的换行符\n，只需要指定FIELDS TERMINATED BY char。</p></li>
</ul>



<pre class="prettyprint"><code class="language-shell hljs vala">hive&gt;<span class="hljs-constant"> CREATE </span>TABLE hive_test
    &gt; (id <span class="hljs-keyword">int</span> comment <span class="hljs-string">'this is id'</span>, name <span class="hljs-keyword">string</span> comment <span class="hljs-string">'this is name'</span> )
    &gt; comment <span class="hljs-string">'this is hive_test'</span>
    &gt;<span class="hljs-constant"> ROW </span>FORMAT<span class="hljs-constant"> DELIMITED </span>
    &gt;<span class="hljs-constant"> FIELDS </span>TERMINATED<span class="hljs-constant"> BY </span><span class="hljs-string">'\t'</span> ;
OK
Time taken: <span class="hljs-number">0.174</span> seconds

<span class="hljs-preprocessor">#为了后面的测试我们创建一张emp表 并导入一些数据</span>
hive&gt; create table emp 
    &gt; (empno <span class="hljs-keyword">int</span>, ename <span class="hljs-keyword">string</span>, job <span class="hljs-keyword">string</span>, mgr <span class="hljs-keyword">int</span>, hiredate <span class="hljs-keyword">string</span>, salary <span class="hljs-keyword">double</span>, comm <span class="hljs-keyword">double</span>, deptno <span class="hljs-keyword">int</span>)
    &gt;<span class="hljs-constant"> ROW </span>FORMAT<span class="hljs-constant"> DELIMITED </span>
    &gt;<span class="hljs-constant"> FIELDS </span>TERMINATED<span class="hljs-constant"> BY </span><span class="hljs-string">'\t'</span> ;
OK
Time taken: <span class="hljs-number">0.651</span> seconds
hive&gt;<span class="hljs-constant"> LOAD </span>DATA<span class="hljs-constant"> LOCAL </span>INPATH <span class="hljs-string">'/home/hadoop/emp.txt'</span><span class="hljs-constant"> OVERWRITE </span>INTO<span class="hljs-constant"> TABLE </span>emp; 
Loading data to table <span class="hljs-keyword">default</span>.emp
Table <span class="hljs-keyword">default</span>.emp stats: [numFiles=<span class="hljs-number">1</span>, numRows=<span class="hljs-number">0</span>, totalSize=<span class="hljs-number">886</span>, rawDataSize=<span class="hljs-number">0</span>]
OK
Time taken: <span class="hljs-number">1.848</span> seconds</code></pre>



<h6 id="14create-table-as-select-ctas">1.4.Create Table As Select (CTAS)</h6>

<ul>
<li>创建表（拷贝表结构及数据，并且会运行MapReduce作业）</li>
</ul>



<pre class="prettyprint"><code class="language-shell hljs r"><span class="hljs-comment"># 复制整张表</span>
hive&gt; create table emp2 as select * from emp;
Query ID = hadoop_20180616171313_fbc318e8-bc70-4b63-84fa-3acd94e4ec3e
Total jobs = <span class="hljs-number">3</span>
<span class="hljs-keyword">...</span>
OK
Time taken: <span class="hljs-number">23.279</span> seconds
hive&gt; select * from emp2;
OK
<span class="hljs-number">7369</span>    SMITH   CLERK   <span class="hljs-number">7902</span>    <span class="hljs-number">1980</span>-<span class="hljs-number">12</span>-<span class="hljs-number">17</span>      <span class="hljs-number">800.0</span>   <span class="hljs-literal">NULL</span>    <span class="hljs-number">20</span>
<span class="hljs-number">7499</span>    ALLEN   SALESMAN        <span class="hljs-number">7698</span>    <span class="hljs-number">1981</span>-<span class="hljs-number">2</span>-<span class="hljs-number">20</span>       <span class="hljs-number">1600.0</span>  <span class="hljs-number">300.0</span>   <span class="hljs-number">30</span>
<span class="hljs-number">7521</span>    WARD    SALESMAN        <span class="hljs-number">7698</span>    <span class="hljs-number">1981</span>-<span class="hljs-number">2</span>-<span class="hljs-number">22</span>       <span class="hljs-number">1250.0</span>  <span class="hljs-number">500.0</span>   <span class="hljs-number">30</span>
<span class="hljs-number">7566</span>    JONES   MANAGER <span class="hljs-number">7839</span>    <span class="hljs-number">1981</span>-<span class="hljs-number">4</span>-<span class="hljs-number">2</span>        <span class="hljs-number">2975.0</span>  <span class="hljs-literal">NULL</span>    <span class="hljs-number">20</span>
<span class="hljs-number">7654</span>    MARTIN  SALESMAN        <span class="hljs-number">7698</span>    <span class="hljs-number">1981</span>-<span class="hljs-number">9</span>-<span class="hljs-number">28</span>       <span class="hljs-number">1250.0</span>  <span class="hljs-number">1400.0</span>  <span class="hljs-number">30</span>
<span class="hljs-number">7698</span>    BLAKE   MANAGER <span class="hljs-number">7839</span>    <span class="hljs-number">1981</span>-<span class="hljs-number">5</span>-<span class="hljs-number">1</span>        <span class="hljs-number">2850.0</span>  <span class="hljs-literal">NULL</span>    <span class="hljs-number">30</span>
<span class="hljs-number">7782</span>    CLARK   MANAGER <span class="hljs-number">7839</span>    <span class="hljs-number">1981</span>-<span class="hljs-number">6</span>-<span class="hljs-number">9</span>        <span class="hljs-number">2450.0</span>  <span class="hljs-literal">NULL</span>    <span class="hljs-number">10</span>
<span class="hljs-number">7788</span>    SCOTT   ANALYST <span class="hljs-number">7566</span>    <span class="hljs-number">1987</span>-<span class="hljs-number">4</span>-<span class="hljs-number">19</span>       <span class="hljs-number">3000.0</span>  <span class="hljs-literal">NULL</span>    <span class="hljs-number">20</span>
<span class="hljs-number">7839</span>    KING    PRESIDENT       <span class="hljs-literal">NULL</span>    <span class="hljs-number">1981</span>-<span class="hljs-number">11</span>-<span class="hljs-number">17</span>      <span class="hljs-number">5000.0</span>  <span class="hljs-literal">NULL</span>    <span class="hljs-number">10</span>
<span class="hljs-number">7844</span>    TURNER  SALESMAN        <span class="hljs-number">7698</span>    <span class="hljs-number">1981</span>-<span class="hljs-number">9</span>-<span class="hljs-number">8</span>        <span class="hljs-number">1500.0</span>  <span class="hljs-number">0.0</span>     <span class="hljs-number">30</span>
<span class="hljs-number">7876</span>    ADAMS   CLERK   <span class="hljs-number">7788</span>    <span class="hljs-number">1987</span>-<span class="hljs-number">5</span>-<span class="hljs-number">23</span>       <span class="hljs-number">1100.0</span>  <span class="hljs-literal">NULL</span>    <span class="hljs-number">20</span>
<span class="hljs-number">7900</span>    JAMES   CLERK   <span class="hljs-number">7698</span>    <span class="hljs-number">1981</span>-<span class="hljs-number">12</span>-<span class="hljs-number">3</span>       <span class="hljs-number">950.0</span>   <span class="hljs-literal">NULL</span>    <span class="hljs-number">30</span>
<span class="hljs-number">7902</span>    FORD    ANALYST <span class="hljs-number">7566</span>    <span class="hljs-number">1981</span>-<span class="hljs-number">12</span>-<span class="hljs-number">3</span>       <span class="hljs-number">3000.0</span>  <span class="hljs-literal">NULL</span>    <span class="hljs-number">20</span>
<span class="hljs-number">7934</span>    MILLER  CLERK   <span class="hljs-number">7782</span>    <span class="hljs-number">1982</span>-<span class="hljs-number">1</span>-<span class="hljs-number">23</span>       <span class="hljs-number">1300.0</span>  <span class="hljs-literal">NULL</span>    <span class="hljs-number">10</span>
Time taken: <span class="hljs-number">0.138</span> seconds, Fetched: <span class="hljs-number">14</span> row(s)

<span class="hljs-comment">#复制表中的一些字段</span>
hive&gt; create table emp3 as select empno,ename from emp;
Query ID = hadoop_20180616171313_fbc318e8-bc70-4b63-84fa-3acd94e4ec3e
Total jobs = <span class="hljs-number">3</span>
<span class="hljs-keyword">...</span>
OK
Time taken: <span class="hljs-number">16.143</span> seconds
hive&gt; select * from emp3;
OK
<span class="hljs-number">7369</span>    SMITH
<span class="hljs-number">7499</span>    ALLEN
<span class="hljs-number">7521</span>    WARD
<span class="hljs-number">7566</span>    JONES
<span class="hljs-number">7654</span>    MARTIN
<span class="hljs-number">7698</span>    BLAKE
<span class="hljs-number">7782</span>    CLARK
<span class="hljs-number">7788</span>    SCOTT
<span class="hljs-number">7839</span>    KING
<span class="hljs-number">7844</span>    TURNER
<span class="hljs-number">7876</span>    ADAMS
<span class="hljs-number">7900</span>    JAMES
<span class="hljs-number">7902</span>    FORD
<span class="hljs-number">7934</span>    MILLER
Time taken: <span class="hljs-number">0.159</span> seconds, Fetched: <span class="hljs-number">14</span> row(s)</code></pre>



<h6 id="15create-table-like">1.5.Create Table Like</h6>



<pre class="prettyprint"><code class="language-shell hljs vbnet"><span class="hljs-preprocessor"># Create Table Like 只拷贝表结构</span>
hive&gt; create table emp_like <span class="hljs-keyword">like</span> emp;
OK
Time taken: <span class="hljs-number">0.195</span> seconds
hive&gt; <span class="hljs-keyword">select</span> * <span class="hljs-keyword">from</span> emp_like;
OK
Time taken: <span class="hljs-number">0.131</span> seconds</code></pre>



<h5 id="2展示表-show-table与show-create-table">2.展示表 （Show Table与Show Create Table）</h5>

<ul>
<li>下面是官网上为我们列出的语法：</li>
</ul>



<pre class="prettyprint"><code class="language-sql hljs "><span class="hljs-operator"><span class="hljs-keyword">SHOW</span> TABLES [<span class="hljs-keyword">IN</span> database_name] [<span class="hljs-string">'identifier_with_wildcards'</span>];</span>
<span class="hljs-operator"><span class="hljs-keyword">SHOW</span> <span class="hljs-keyword">CREATE</span> <span class="hljs-keyword">TABLE</span> ([db_name.]table_name|view_name);</span></code></pre>



<pre class="prettyprint"><code class="language-shell hljs mel">hive&gt; show tables;
OK
emp
emp2
emp3
emp_like
hive_test
Time taken: <span class="hljs-number">0.042</span> seconds, Fetched: <span class="hljs-number">5</span> row(s)
hive&gt; show tables <span class="hljs-string">'emp*'</span>;
OK
emp
emp2
emp3
emp_like
Time taken: <span class="hljs-number">0.053</span> seconds, Fetched: <span class="hljs-number">4</span> row(s)
hive&gt; show create table emp;
OK
CREATE TABLE <span class="hljs-string">`emp`</span>(
  <span class="hljs-string">`empno`</span> <span class="hljs-keyword">int</span>, 
  <span class="hljs-string">`ename`</span> <span class="hljs-keyword">string</span>, 
  <span class="hljs-string">`job`</span> <span class="hljs-keyword">string</span>, 
  <span class="hljs-string">`mgr`</span> <span class="hljs-keyword">int</span>, 
  <span class="hljs-string">`hiredate`</span> <span class="hljs-keyword">string</span>, 
  <span class="hljs-string">`salary`</span> double, 
  <span class="hljs-string">`comm`</span> double, 
  <span class="hljs-string">`deptno`</span> <span class="hljs-keyword">int</span>)
ROW FORMAT DELIMITED 
  FIELDS TERMINATED BY <span class="hljs-string">'\t'</span> 
STORED AS INPUTFORMAT 
  <span class="hljs-string">'org.apache.hadoop.mapred.TextInputFormat'</span> 
OUTPUTFORMAT 
  <span class="hljs-string">'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'</span>
LOCATION
  <span class="hljs-string">'hdfs://192.168.6.217:9000/user/hive/warehouse/emp'</span>
TBLPROPERTIES (
  <span class="hljs-string">'COLUMN_STATS_ACCURATE'</span>=<span class="hljs-string">'true'</span>, 
  <span class="hljs-string">'numFiles'</span>=<span class="hljs-string">'1'</span>, 
  <span class="hljs-string">'numRows'</span>=<span class="hljs-string">'0'</span>, 
  <span class="hljs-string">'rawDataSize'</span>=<span class="hljs-string">'0'</span>, 
  <span class="hljs-string">'totalSize'</span>=<span class="hljs-string">'657'</span>, 
  <span class="hljs-string">'transient_lastDdlTime'</span>=<span class="hljs-string">'1529140756'</span>)
Time taken: <span class="hljs-number">0.245</span> seconds, Fetched: <span class="hljs-number">24</span> row(s)</code></pre>



<h5 id="3查询表信息describe-table">3.查询表信息（Describe Table）</h5>

<ul>
<li>下面是官网上为我们列出的语法：</li>
</ul>



<pre class="prettyprint"><code class="language-sql hljs ">DESCRIBE [EXTENDED|FORMATTED] 
  table_name[.col_name ( [.field_name] | [.'$elem$'] | [.'$key$'] | [.'$value$'] )* ];
                                        <span class="hljs-comment">-- (Note: Hive 1.x.x and 0.x.x only. See "Hive 2.0+: New Syntax" below)</span></code></pre>

<p>desc formatted table_name; 比较常用</p>



<pre class="prettyprint"><code class="language-shell hljs vala">hive&gt; desc emp;
OK
empno                   <span class="hljs-keyword">int</span>                                         
ename                   <span class="hljs-keyword">string</span>                                      
job                     <span class="hljs-keyword">string</span>                                      
mgr                     <span class="hljs-keyword">int</span>                                         
hiredate                <span class="hljs-keyword">string</span>                                      
salary                  <span class="hljs-keyword">double</span>                                      
comm                    <span class="hljs-keyword">double</span>                                      
deptno                  <span class="hljs-keyword">int</span>                                         
Time taken: <span class="hljs-number">0.213</span> seconds, Fetched: <span class="hljs-number">8</span> row(s)
hive&gt; desc formatted emp;
OK
<span class="hljs-preprocessor"># col_name              data_type               comment             </span>

empno                   <span class="hljs-keyword">int</span>                                         
ename                   <span class="hljs-keyword">string</span>                                      
job                     <span class="hljs-keyword">string</span>                                      
mgr                     <span class="hljs-keyword">int</span>                                         
hiredate                <span class="hljs-keyword">string</span>                                      
salary                  <span class="hljs-keyword">double</span>                                      
comm                    <span class="hljs-keyword">double</span>                                      
deptno                  <span class="hljs-keyword">int</span>                                         

<span class="hljs-preprocessor"># Detailed Table Information             </span>
Database:               <span class="hljs-keyword">default</span>                  
Owner:                  hadoop                   
CreateTime:             Sat Jun <span class="hljs-number">16</span> <span class="hljs-number">17</span>:<span class="hljs-number">13</span>:<span class="hljs-number">05</span><span class="hljs-constant"> CST </span><span class="hljs-number">2018</span>     
LastAccessTime:        <span class="hljs-constant"> UNKNOWN </span>                 
Protect Mode:           None                     
Retention:              <span class="hljs-number">0</span>                        
Location:               hdfs:<span class="hljs-comment">//192.168.6.217:9000/user/hive/warehouse/emp        </span>
Table Type:            <span class="hljs-constant"> MANAGED_TABLE </span>           
Table Parameters:                
       <span class="hljs-constant"> COLUMN_STATS_ACCURATE </span>  <span class="hljs-literal">true</span>                
        numFiles                <span class="hljs-number">1</span>                   
        numRows                 <span class="hljs-number">0</span>                   
        rawDataSize             <span class="hljs-number">0</span>                   
        totalSize               <span class="hljs-number">657</span>                 
        transient_lastDdlTime   <span class="hljs-number">1529140756</span>          

<span class="hljs-preprocessor"># Storage Information            </span>
SerDe Library:          org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe       
InputFormat:            org.apache.hadoop.mapred.TextInputFormat         
OutputFormat:           org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat       
Compressed:             No                       
Num Buckets:            -<span class="hljs-number">1</span>                       
Bucket Columns:         []                       
Sort Columns:           []                       
Storage Desc Params:             
        field.delim             \t                  
        serialization.format    \t                  
Time taken: <span class="hljs-number">0.214</span> seconds, Fetched: <span class="hljs-number">39</span> row(s)
hive&gt; desc<span class="hljs-constant"> EXTENDED </span>emp;
OK
empno                   <span class="hljs-keyword">int</span>                                         
ename                   <span class="hljs-keyword">string</span>                                      
job                     <span class="hljs-keyword">string</span>                                      
mgr                     <span class="hljs-keyword">int</span>                                         
hiredate                <span class="hljs-keyword">string</span>                                      
salary                  <span class="hljs-keyword">double</span>                                      
comm                    <span class="hljs-keyword">double</span>                                      
deptno                  <span class="hljs-keyword">int</span>                                         

Detailed Table Information      Table(tableName:emp, dbName:<span class="hljs-keyword">default</span>, owner:hadoop, createTime:<span class="hljs-number">1529140385</span>, lastAccessTime:<span class="hljs-number">0</span>, retention:<span class="hljs-number">0</span>, sd:StorageDescriptor(cols:[FieldSchema(name:empno, type:<span class="hljs-keyword">int</span>, comment:<span class="hljs-literal">null</span>), FieldSchema(name:ename, type:<span class="hljs-keyword">string</span>, comment:<span class="hljs-literal">null</span>), FieldSchema(name:job, type:<span class="hljs-keyword">string</span>, comment:<span class="hljs-literal">null</span>), FieldSchema(name:mgr, type:<span class="hljs-keyword">int</span>, comment:<span class="hljs-literal">null</span>), FieldSchema(name:hiredate, type:<span class="hljs-keyword">string</span>, comment:<span class="hljs-literal">null</span>), FieldSchema(name:salary, type:<span class="hljs-keyword">double</span>, comment:<span class="hljs-literal">null</span>), FieldSchema(name:comm, type:<span class="hljs-keyword">double</span>, comment:<span class="hljs-literal">null</span>), FieldSchema(name:deptno, type:<span class="hljs-keyword">int</span>, comment:<span class="hljs-literal">null</span>)], location:hdfs:<span class="hljs-comment">//192.168.6.217:9000/user/hive/warehouse/emp, inputFormat:org.apache.hadoop.mapred.TextInputFormat, outputFormat:org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat, compressed:false, numBuckets:-1, serdeInfo:SerDeInfo(name:null, serializationLib:org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe, parameters:{serialization.format=       , field.delim=</span>
Time taken: <span class="hljs-number">0.21</span> seconds, Fetched: <span class="hljs-number">10</span> row(s)</code></pre>



<h5 id="4修改表alter-table">4.修改表（Alter Table）</h5>

<ul>
<li>下面是官网上为我们列出的语法：</li>
</ul>



<pre class="prettyprint"><code class="language-sql hljs "><span class="hljs-operator"><span class="hljs-keyword">ALTER</span> <span class="hljs-keyword">TABLE</span> table_name RENAME <span class="hljs-keyword">TO</span> new_table_name;</span>
<span class="hljs-operator"><span class="hljs-keyword">ALTER</span> <span class="hljs-keyword">TABLE</span> table_name <span class="hljs-keyword">SET</span> TBLPROPERTIES table_properties;</span>
table_properties:
  : (property_name = property_value, property_name = property_value, ... )
<span class="hljs-operator"><span class="hljs-keyword">ALTER</span> <span class="hljs-keyword">TABLE</span> table_name <span class="hljs-keyword">SET</span> TBLPROPERTIES (<span class="hljs-string">'comment'</span> = new_comment);</span>
...</code></pre>



<pre class="prettyprint"><code class="language-shell hljs vala">hive&gt; alter table hive_test rename to new_hive_test;
OK
Time taken: <span class="hljs-number">0.262</span> seconds
hive&gt;<span class="hljs-constant"> ALTER </span>TABLE table_name<span class="hljs-constant"> SET </span>TBLPROPERTIES (<span class="hljs-string">"creator"</span>=<span class="hljs-string">"ruoze"</span>, <span class="hljs-string">"date"</span>=<span class="hljs-string">"2018-06-16"</span>);
FAILED: SemanticException [Error <span class="hljs-number">10001</span>]: Table not found <span class="hljs-keyword">default</span>.table_name
hive&gt;<span class="hljs-constant"> ALTER </span>TABLE new_hive_test<span class="hljs-constant"> SET </span>TBLPROPERTIES (<span class="hljs-string">"creator"</span>=<span class="hljs-string">"ruoze"</span>, <span class="hljs-string">"date"</span>=<span class="hljs-string">"2018-06-16"</span>);
OK
Time taken: <span class="hljs-number">0.246</span> seconds
hive&gt;<span class="hljs-constant"> ALTER </span>TABLE new_hive_test<span class="hljs-constant"> SET </span>TBLPROPERTIES (<span class="hljs-string">'comment'</span> = <span class="hljs-string">'This is new_hive_test Table'</span>);
<span class="hljs-preprocessor"># 再次查看表</span>
hive&gt; desc formatted new_hive_test;
OK
<span class="hljs-preprocessor"># col_name              data_type               comment             </span>

id                      <span class="hljs-keyword">int</span>                     <span class="hljs-keyword">this</span> is id          
name                    <span class="hljs-keyword">string</span>                  <span class="hljs-keyword">this</span> is name        

<span class="hljs-preprocessor"># Detailed Table Information             </span>
Database:               <span class="hljs-keyword">default</span>                  
Owner:                  hadoop                   
CreateTime:             Sat Jun <span class="hljs-number">16</span> <span class="hljs-number">17</span>:<span class="hljs-number">09</span>:<span class="hljs-number">19</span><span class="hljs-constant"> CST </span><span class="hljs-number">2018</span>     
LastAccessTime:        <span class="hljs-constant"> UNKNOWN </span>                 
Protect Mode:           None                     
Retention:              <span class="hljs-number">0</span>                        
Location:               hdfs:<span class="hljs-comment">//192.168.6.217:9000/user/hive/warehouse/new_hive_test      </span>
Table Type:            <span class="hljs-constant"> MANAGED_TABLE </span>           
Table Parameters:                
       <span class="hljs-constant"> COLUMN_STATS_ACCURATE </span>  <span class="hljs-literal">false</span>               
        comment                 This is new_hive_test Table
        creator                 ruoze               
        date                    <span class="hljs-number">2018</span>-<span class="hljs-number">06</span>-<span class="hljs-number">16</span>          
        last_modified_by        hadoop              
        last_modified_time      <span class="hljs-number">1529143021</span>          
        numFiles                <span class="hljs-number">0</span>                   
        numRows                 -<span class="hljs-number">1</span>                  
        rawDataSize             -<span class="hljs-number">1</span>                  
        totalSize               <span class="hljs-number">0</span>                   
        transient_lastDdlTime   <span class="hljs-number">1529143021</span>          

<span class="hljs-preprocessor"># Storage Information            </span>
SerDe Library:          org.apache.hadoop.hive.serde2.lazy.LazySimpleSerDe       
InputFormat:            org.apache.hadoop.mapred.TextInputFormat         
OutputFormat:           org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat       
Compressed:             No                       
Num Buckets:            -<span class="hljs-number">1</span>                       
Bucket Columns:         []                       
Sort Columns:           []                       
Storage Desc Params:             
        field.delim             \t                  
        serialization.format    \t                  
Time taken: <span class="hljs-number">0.188</span> seconds, Fetched: <span class="hljs-number">38</span> row(s)</code></pre>



<h5 id="5截断表truncate-table">5.截断表（Truncate Table）</h5>

<ul>
<li>下面是官网上为我们列出的语法：</li>
</ul>



<pre class="prettyprint"><code class="language-sql hljs "><span class="hljs-operator"><span class="hljs-keyword">TRUNCATE</span> <span class="hljs-keyword">TABLE</span> table_name [PARTITION partition_spec];</span>
partition_spec:
  : (partition_column = partition_col_value, partition_column = partition_col_value, ...)</code></pre>

<p>Truncate Table用处不多</p>



<pre class="prettyprint"><code class="language-shell hljs vhdl">hive&gt; <span class="hljs-keyword">select</span> * from emp3;
OK
<span class="hljs-number">7369</span>    SMITH
<span class="hljs-number">7499</span>    ALLEN
<span class="hljs-number">7521</span>    WARD
<span class="hljs-number">7566</span>    JONES
<span class="hljs-number">7654</span>    MARTIN
<span class="hljs-number">7698</span>    BLAKE
<span class="hljs-number">7782</span>    CLARK
<span class="hljs-number">7788</span>    SCOTT
<span class="hljs-number">7839</span>    KING
<span class="hljs-number">7844</span>    TURNER
<span class="hljs-number">7876</span>    ADAMS
<span class="hljs-number">7900</span>    JAMES
<span class="hljs-number">7902</span>    FORD
<span class="hljs-number">7934</span>    MILLER
<span class="hljs-typename">Time</span> taken: <span class="hljs-number">0.148</span> seconds, Fetched: <span class="hljs-number">14</span> row(s)
hive&gt; truncate table emp3;
OK
<span class="hljs-typename">Time</span> taken: <span class="hljs-number">0.241</span> seconds
hive&gt; <span class="hljs-keyword">select</span> * from emp3;
OK
<span class="hljs-typename">Time</span> taken: <span class="hljs-number">0.12</span> seconds</code></pre>



<h5 id="6删除表drop-table">6.删除表（Drop Table）</h5>

<ul>
<li>下面是官网上为我们列出的语法：</li>
</ul>



<pre class="prettyprint"><code class="language-sql hljs "><span class="hljs-operator"><span class="hljs-keyword">DROP</span> <span class="hljs-keyword">TABLE</span> [<span class="hljs-keyword">IF</span> <span class="hljs-keyword">EXISTS</span>] table_name [PURGE];</span>     <span class="hljs-comment">-- (Note: PURGE available in Hive 0.14.0 and later)</span></code></pre>

<p>1.指定PURGE后，数据不会放到回收箱，会直接删除。 <br>
2.DROP TABLE删除此表的元数据和数据。如果配置了垃圾箱（并且未指定PURGE），则实际将数据移至.Trash / Current目录。元数据完全丢失。 <br>
3.删除EXTERNAL表时，表中的数据不会从文件系统中删除。</p>



<pre class="prettyprint"><code class="language-shell hljs vhdl">hive&gt; drop table emp3;
OK
<span class="hljs-typename">Time</span> taken: <span class="hljs-number">0.866</span> seconds
hive&gt; show tables;
OK
emp
emp2
emp_like
new_hive_test
<span class="hljs-typename">Time</span> taken: <span class="hljs-number">0.036</span> seconds, Fetched: <span class="hljs-number">4</span> row(s)</code></pre>

<p><em>参考：</em><a href="https://blog.csdn.net/yu0_zhang0/article/details/78976021" rel="nofollow">https://blog.csdn.net/yu0_zhang0/article/details/78976021</a> <br>
<em>关于表的DDL操作还有很多，有关分区表的操作还没详解 后面会单独写一篇分区表</em></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>