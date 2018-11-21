---
layout:     post
title:      hadoop 自学指南八之Hive(1)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/ice_grey/article/details/48608547				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1>一、前言</h1>
<p>什么是hive？Hive 数据仓库工具，可以把hadoop下原始结构化数据变成hive中表看成sql-&gt;Map-Reduce的映射器，提供shell，jdbc/odbc接口<br></p>
<p>他为数据仓库的管理提供了多功能：数据ETL工具，数据存储管理和大型数据集查询和分析能力</p>
<h1>二、Hive 数据存储</h1>
<p><span></span>hive 的数据存储建立在hadoop 的hdfs 基础上,hive 的每个对应的分区对应 的数据库中的相应分区表的一个索引，一个分区对应表下的一个目录，相应数据存储在对应的目录中。hive 的表和外部表(指向hdfs中存在的数据)有一定的差别。</p>
<p><span></span>表的创建在数据加载过程中，实际数据会移动到数据仓库目录中，删除表时，表的数据和元数据将会被同时删除</p>
<p><span></span>外部表创建在加载过程中，实际数据并不会移动到数据仓库中，删除外部表仅删除元数据，表中的数据不会删除</p>
<p><br></p>
<h1>三、安装hive</h1>
<p><span></span>内嵌模式：元数据保持在内嵌的derby模式，只允许一个会话连接</p>
<p><span></span>本地狡辩模式：在本地安装mysql，把元数据放到mysql内</p>
<p><span></span>远程模式：元数据放置在远程的mysql数据库</p>
<p><span></span></p>
<p>下载：wget  http://apache.fayea.com/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz</p>
<p>设置环境变量：</p>
<p><span><img src="https://img-blog.csdn.net/20150921171815586?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p>配置文件：</p>
<p><span></span>hive-env.sh</p>
<p><span></span>hive-site.xml</p>
<p>启动hive：</p>
<p><span></span>cd bin  ;  ./hive</p>
<p>测试：show table</p>
<p><img src="https://img-blog.csdn.net/20150921171815586?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<h1>四、Hive QL</h1>
<h2>4.1、创建表</h2>
<p></p>
<pre><code class="language-java">CREATE [EXTERNAL] TABLE [IF NOT EXISTS] table_name
  [(col_name data_type [COMMENT col_comment], ...)]
  [COMMENT table_comment]
  [PARTITIONED BY (col_name data_type [COMMENT col_comment], ...)]
  [CLUSTERED BY (col_name, col_name, ...) [SORTED BY (col_name [ASC|DESC], ...)] INTO num_buckets BUCKETS]
  [
   [ROW FORMAT row_format] [STORED AS file_format]
   | STORED BY 'storage.handler.class.name' [ WITH SERDEPROPERTIES (...) ]  (Note:  only available starting with 0.6.0)
  ]
  [LOCATION hdfs_path]
  [TBLPROPERTIES (property_name=property_value, ...)]  (Note:  only available starting with 0.6.0)
  [AS select_statement]  (Note: this feature is only available starting with 0.5.0.)

CREATE [EXTERNAL] TABLE [IF NOT EXISTS] table_name
  LIKE existing_table_name
  [LOCATION hdfs_path]

data_type
  : primitive_type
  | array_type
  | map_type
  | struct_type

primitive_type
  : TINYINT
  | SMALLINT
  | INT
  | BIGINT
  | BOOLEAN
  | FLOAT
  | DOUBLE
  | STRING

array_type
  : ARRAY &lt; data_type &gt;

map_type
  : MAP &lt; primitive_type, data_type &gt;

struct_type
  : STRUCT &lt; col_name : data_type [COMMENT col_comment], ...&gt;

row_format
  : DELIMITED [FIELDS TERMINATED BY char] [COLLECTION ITEMS TERMINATED BY char]
        [MAP KEYS TERMINATED BY char] [LINES TERMINATED BY char]
  | SERDE serde_name [WITH SERDEPROPERTIES (property_name=property_value, property_name=property_value, ...)]

file_format:
  : SEQUENCEFILE
  | TEXTFILE
  | RCFILE     (Note:  only available starting with 0.6.0)
  | INPUTFORMAT input_format_classname OUTPUTFORMAT output_format_classname</code></pre>
<p></p>
<p><br></p>
eg:测试数据
<p>1<span> </span>chen<span> </span>
xxxxxxxxx1<br>
2<span> </span>xiaoming<span> </span>
xxxxx2<br>
3<span> </span>zhangsan<span> </span>
xxxxx3<span> </span><br></p>
<p>创建表：</p>
<p></p>
<pre><code class="language-java">create table myuser(id int, name string, address string) row format delimited fields terminated by '\005' stored as textfile</code></pre>
<p></p>
<p>我们的常用间隔符一般是Ascii码5，Ascii码7等。在hive中Ascii码5用’\005’表示， Ascii码7用’\007’表示，依此类推。</p>
装载数据：
<p></p>
<pre><code class="language-java">load data inpath '/input/hive_myuser.txt' overwrite into table myuser</code></pre>
<p></p>
<p> </p>
<p>外部表：</p>
<p>create external table myuser_external(id int,name string,address string) row format delimited fields terminated by '\005'  stored  as textfile location '/input/hive_myuser.txt'<br></p>
<br><p>删除表：drop table myuser;</p>
<h2>4.2、分区</h2>
<p></p>
<p>HIVE的分区通过在创建表时启用partition by实现，用来partition的维度并不是实际数据的某一列，具体分区的标志是由插入内容时给定的。</p>
<p></p>
<pre><code class="language-java">CREATE TABLE page_view(viewTime INT, userid BIGINT,
     page_url STRING, referrer_url STRING,
     ip STRING COMMENT 'IP Address of the User')
 COMMENT 'This is the page view table'
 PARTITIONED BY(dt STRING, country STRING)
 CLUSTERED BY(userid) SORTED BY(viewTime) INTO 32 BUCKETS
 ROW FORMAT DELIMITED
   FIELDS TERMINATED BY '\001'
   COLLECTION ITEMS TERMINATED BY '\002'
   MAP KEYS TERMINATED BY '\003'
 STORED AS SEQUENCEFILE;</code></pre><br><br><p></p>
create table myuser_partition(id int, name string, address string) partitioned by(dt string) row format delimited fields terminated by '\005' stored as textfile;
<p></p>
<p>外部表可以指定location位置</p>
<p></p>
<pre><code class="language-java">REATE EXTERNAL TABLE page_view(viewTime INT, userid BIGINT,
     page_url STRING, referrer_url STRING,
     ip STRING COMMENT 'IP Address of the User',
     country STRING COMMENT 'country of origination')
 COMMENT 'This is the staging page view table'
 ROW FORMAT DELIMITED FIELDS TERMINATED BY '\054'
 STORED AS TEXTFILE
 LOCATION '&lt;hdfs_location&gt;';</code></pre><br><h2>4.3、修改表alter table</h2>
<h3>4.3.1、Add partition</h3>
<p></p>
<pre><code class="language-java">ALTER TABLE table_name ADD [IF NOT EXISTS] partition_spec [ LOCATION 'location1' ] partition_spec [ LOCATION 'location2' ] ...

partition_spec:
  : PARTITION (partition_col = partition_col_value, partition_col = partiton_col_value, ...)</code></pre>
<p></p>
<p>eg:</p>
<p></p>
<pre><code class="language-java">Eg:
ALTER TABLE c02_clickstat_fatdt1 ADD 
PARTITION (dt='20101202') location '/user/hive/warehouse/c02_clickstat_fatdt1/part20101202' 
PARTITION (dt='20101203') location '/user/hive/warehouse/c02_clickstat_fatdt1/part20101203';</code></pre><br><p></p>
<h3>4.3.2、Rename table</h3>
<p></p>
<p>ALTER TABLE table_name RENAME TO new_table_name</p>
<p>这个命令可以让用户为表更名。数据所在的位置和分区名并不改变。换而言之，老的表名并未“释放”，对老表的更改会改变新表的数据。</p>
<h3>4.3.3、Change Column</h3>
<p>ALTER TABLE table_name CHANGE [COLUMN] col_old_name col_new_name column_type [COMMENT col_comment] [FIRST|AFTER column_name]</p>
<p>这个命令可以允许改变列名、数据类型、注释、列位置或者它们的任意组合</p>
<h3>4.3.4、 add/Replace columns</h3>
<p>ALTER TABLE table_name ADD|REPLACE COLUMNS (col_name data_type [COMMENT col_comment], ...)</p>
<p> </p>
<p>ADD是代表新增一字段，字段位置在所有列后面(partition列前);REPLACE则是表示替换表中所有字段。</p>
<p>Eg:</p>
<p>hive&gt; desc xi;</p>
<p>OK</p>
<p>id      int</p>
<p>cont    string</p>
<p>dw_ins_date     string</p>
<p>Time taken: 0.061 seconds</p>
<p>hive&gt; create table xibak like xi;                         </p>
<p>OK</p>
<p>Time taken: 0.157 seconds</p>
<p>hive&gt; alter table xibak replace columns (ins_date string);   </p>
<p>OK</p>
<p>Time taken: 0.109 seconds</p>
<p>hive&gt; desc xibak;</p>
<p>OK</p>
<p>ins_date        string</p>
<h3>4.3.5、create view</h3>
<p></p>
<pre><code class="language-java">CREATE VIEW [IF NOT EXISTS] view_name [ (column_name [COMMENT column_comment], ...) ]
[COMMENT view_comment]
[TBLPROPERTIES (property_name = property_value, ...)]
AS SELECT ...</code></pre>
<h3>4.3.6、show</h3>
<p>show tables;<span> </span>查看表名</p>
<p>show partitions;<span> </span>查看某表分区</p>
<p>describe tables;<span> </span>查看某表结构</p>
<p></p>
<p>查看分区内容</p>
<p></p>
<p style="background:rgb(243,245,247);">SELECT a.foo FROM invites a WHERE a.ds='2008-08-15';</p>
<p>查看有限行内容，同Greenplum，用limit关键词</p>
<p style="background:rgb(243,245,247);">SELECT a.foo FROM invites a limit 3;</p>
<br><p>查看表分区定义</p>
<p style="background:rgb(243,245,247);">DESCRIBE EXTENDED page_view PARTITION (ds='2008-08-08');</p>
<br><h2>4.6、加载数据Load</h2>
<p>语法：</p>
<p></p>
<pre><code class="language-java">LOAD DATA [LOCAL] INPATH 'filepath' [OVERWRITE] INTO TABLE tablename [PARTITION (partcol1=val1, partcol2=val2 ...)]</code></pre>Load 操作只是单纯的复制/移动操作，将数据文件移动到hive的相应的位置
<p></p>
<p></p>
<p>从本地导入数据到表格并追加原表</p>
<pre><code class="language-java">LOAD DATA LOCAL INPATH `/tmp/pv_2008-06-08_us.txt` INTO TABLE c02 PARTITION(date='2008-06-08', country='US')
</code></pre><pre><code class="language-java">&lt;/pre&gt;从hdfs 导入数据到表格并覆盖原表&lt;p&gt;&lt;/p&gt;&lt;pre name="code" class="java"&gt;LOAD DATA INPATH '/user/admin/SqlldrDat/CnClickstat/20101101/18/clickstat_gp_fatdt0/0' INTO table c02_clickstat_fatdt1 OVERWRITE PARTITION (dt='20101201');</code></pre>
<p></p>
<p><br></p>
<h2>4.7、Insert</h2>
<pre><code class="language-java">Standard syntax:
INSERT OVERWRITE [LOCAL] DIRECTORY directory1 SELECT ... FROM ...

Hive extension (multiple inserts):
FROM from_statement
INSERT OVERWRITE [LOCAL] DIRECTORY directory1 select_statement1
[INSERT OVERWRITE [LOCAL] DIRECTORY directory2 select_statement2] ...</code></pre>
<p></p>
<p>导出文件到本地</p>
<p></p>
<pre><code class="language-java">INSERT OVERWRITE LOCAL DIRECTORY '/tmp/local_out' SELECT a.* FROM pokes a;</code></pre><br>
导出文件到HDFS
<p></p>
<p></p>
<pre><code class="language-java">INSERT OVERWRITE DIRECTORY '/user/admin/SqlldrDat/CnClickstat/20101101/19/clickstat_gp_fatdt0/0' SELECT a.* FROM c02_clickstat_fatdt1 a WHERE dt=’20101201’;</code></pre>
<p></p>
<p><br></p>
<p></p>
<p>一个源可以同时插入到多个目标表或目标文件，多目标insert可以用一句话来完成</p>
<pre><code class="language-java">FROM src
  INSERT OVERWRITE TABLE dest1 SELECT src.* WHERE src.key &lt; 100
  INSERT OVERWRITE TABLE dest2 SELECT src.key, src.value WHERE src.key &gt;= 100 and src.key &lt; 200
  INSERT OVERWRITE TABLE dest3 PARTITION(ds='2008-04-08', hr='12') SELECT src.key WHERE src.key &gt;= 200 and src.key &lt; 300
  INSERT OVERWRITE LOCAL DIRECTORY '/tmp/dest4.out' SELECT src.value WHERE src.key &gt;= 300;</code></pre><br><br>
tip:
<p></p>
<p>Hive<span style="font-family:'宋体';">不支持一条一条的用</span><span style="font-family:Calibri;">insert</span><span style="font-family:'宋体';">语句进行插入操作，也不支持</span><span style="font-family:Calibri;">update</span><span style="font-family:'宋体';">的操作。数据是以</span><span style="font-family:Calibri;">load</span><span style="font-family:'宋体';">的方式，加载到建立好的表中。数据一旦导入，则不可修改。要么</span><span style="font-family:Calibri;">drop</span><span style="font-family:'宋体';">掉整个表，要么建立新的表，导入新的数据。</span></p>
<h2>4.8、Hive Select </h2>
<p></p>
<pre><code class="language-java">SELECT [ALL | DISTINCT] select_expr, select_expr, ...
FROM table_reference
[WHERE where_condition] 
[GROUP BY col_list]
[   CLUSTER BY col_list
  | [DISTRIBUTE BY col_list] [SORT BY col_list]
]
[LIMIT number]</code></pre>
<p></p>
<p><br></p>
eg:select a.address from myuser a;
<h3>4.8.1、order by</h3>
<p></p>
<pre><code class="language-java">colOrder: ( ASC | DESC )
orderBy: ORDER BY colName colOrder? (',' colName colOrder?)*
query: SELECT expression (',' expression)* FROM src orderBy</code></pre>
<p></p>
<p><br></p>
<h3>4.8.2、Join</h3>
<p></p>
<pre><code class="language-java">join_table:
    table_reference JOIN table_factor [join_condition]
  | table_reference {LEFT|RIGHT|FULL} [OUTER] JOIN table_reference join_condition
  | table_reference LEFT SEMI JOIN[] table_reference join_condition

table_reference:
    table_factor
  | join_table

table_factor:
    tbl_name [alias]
  | table_subquery alias
  | ( table_references )

join_condition:
    ON equality_expression ( AND equality_expression )*

equality_expression:
    expression = expression</code></pre>
<p></p>
<p><br></p>
1、只支持等值的join
<p>所以你能这样写</p>
<p><span> select a.*,b.* from myuser a join myuser b on (a.id = b.id);</span></p>
<p>但不能这样写：</p>
<p><span></span>select a.*,b.* from myuser a, myuser b where a.id = b.id;</p>
<p>2、join 可以多于2个表</p>
<p>3、join 通过MapReduce 任务，将大表放到条件最后，有减少缓存的数量</p>
<p></p>
<p> SELECT a.val, b.val, c.val FROM a</p>
<p>    JOIN b ON (a.key = b.key1) JOIN c ON (c.key = b.key1)</p>
<p>第一次map是a.key= b.key2,第一次结束后，会缓存结果和c进行map/reduece</p>
<p>4、Left semi join 可以替代IN/Exists</p>
<p><br></p>
<h1>五、Hive UDF</h1>
<p></p>
<pre><code class="language-java">SHOW FUNCTIONS;
DESCRIBE FUNCTION &lt;function_name&gt;;</code></pre><br><img src="https://img-blog.csdn.net/20150922180934289?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><p></p>
<br><p>UDF函数的限制</p>
<p>1、select 里面不能有其他字段</p>
<p></p>
<p>SELECT pageid, explode(adid_list) AS myCol.</p>
<br><p>2、不能嵌套</p>
<p></p>
<p>ELECT explode(explode(adid_list)) AS myCol...不支持</p>
3、不支持GROUP BY / CLUSTER BY / DISTRIBUTE BY / SORT BY
<p></p>
<p>SELECT explode(adid_list) AS myCol ... GROUP BY myCol </p>
eg:UDF  函数
<p>Explode 将数组进行转置</p>
<p><br></p>
<h1>六、Hive tip </h1>
<p>1、hadoop 和hive 是使用utf-8 编码</p>
<p>2、count(distinct)    </p>
<p>当前的 Hive 不支持在一条查询语句中有多 Distinct</p>
<p>3、join 只支持等值连接 </p>
<p>4、dml   只支持<span style="font-family:Calibri;">INSERT/LOAD</span><span style="font-family:'宋体';">操作，无</span><span style="font-family:Calibri;">UPDATE</span><span style="font-family:'宋体';">和</span><span style="font-family:Calibri;">DELTE</span></p>
<p><span style="font-family:Calibri;">5、不支持having</span></p>
<p><span style="font-family:Calibri;">6、Join 中null是有意义的</span></p>
<p><span style="font-family:Calibri;">7、</span><span style="font-family:Calibri;">不支持</span><span style="font-family:Calibri;">HAVING</span><span style="font-family:'宋体';">操作。如果需要这个功能要嵌套一个子查询用</span><span style="font-family:Calibri;">where</span><span style="font-family:'宋体';">限制</span></p>
<p><span style="font-family:'宋体';">8、如何插入一个数据并且不删除原来的数据</span></p>
<p><span style="font-family:'宋体';"><br></span></p>
<p><span style="font-family:'宋体';"></span></p>
<pre><code class="language-java">INSERT OVERWRITE TABLE xiaojun1 
SELECT id, value FROM (
SELECT id, value FROM xiaojun1 
UNION ALL 
SELECT 4 AS id, 5 AS value FROM xiaojun1 limit 1
) u;</code></pre>9、类型一致<br><p></p>
<p>INSERT OVERWRITE TABLE<span style="font-family:'宋体';">在插入数据时</span><span style="font-family:Calibri;">, </span><span style="font-family:'宋体';">后面的字段的初始值应注意与表定义中的一致性</span><span style="font-family:Calibri;">. </span><span style="font-family:'宋体';">例如</span><span style="font-family:Calibri;">, </span><span style="font-family:'宋体';">当为一个</span><span style="font-family:Calibri;">STRING</span><span style="font-family:'宋体';">类型字段初始为</span><span style="font-family:Calibri;">NULL</span><span style="font-family:'宋体';">时</span><span style="font-family:Calibri;">: </span></p>
<p>NULL AS field_name // <span style="font-family:'宋体';">这可能会被提示定义类型为</span><span style="font-family:Calibri;">STRING, </span><span style="font-family:'宋体';">但这里是</span><span style="font-family:Calibri;">void </span></p>
<p>CAST(NULL AS STRING) AS field_name // <span style="font-family:'宋体';">这样是正确的 </span></p>
<p>又如<span style="font-family:Calibri;">, </span><span style="font-family:'宋体';">为一个</span><span style="font-family:Calibri;">BIGINT</span><span style="font-family:'宋体';">类型的字段初始为</span><span style="font-family:Calibri;">0</span><span style="font-family:'宋体';">时</span><span style="font-family:Calibri;">: </span></p>
<p>CAST(0 AS BIGINT) AS field_name </p>
<br><p><br><br></p>
<p><br></p>
<p><br><br></p>
<p><br></p>
<p><br><br><br></p>
<p><br></p>
<p><br><br><br></p>
<p><br></p>
<p><br></p>
<p><br><br><br></p>
<p><span></span></p>
            </div>
                </div>