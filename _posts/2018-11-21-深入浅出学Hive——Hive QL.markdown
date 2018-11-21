---
layout:     post
title:      深入浅出学Hive——Hive QL
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div style="border-width:0px;overflow:hidden;font-family:verdana, arial, helvetica, sans-serif;line-height:21px;background-color:rgb(250,250,250);">
<p style="font-size:14px;line-height:1.5em;">
<span class="bold" style="font-weight:bold;line-height:1.5em;"></span></p>
<p style="font-size:14px;font-family:verdana, arial, helvetica, sans-serif;line-height:21px;">
<span class="bold" style="font-weight:bold;line-height:1.5em;">目录：</span></p>
<p style="font-size:14px;font-family:verdana, arial, helvetica, sans-serif;line-height:21px;">
<span style="color:rgb(0,94,167);"><span style="line-height:1.5em;"><a href="http://blog.csdn.net/guohecang/article/details/51576755" rel="nofollow">初始Hive</a></span></span></p>
<p style="font-size:14px;font-family:verdana, arial, helvetica, sans-serif;line-height:21px;">
<span style="color:rgb(0,94,167);"><span style="line-height:1.5em;"><a href="http://blog.csdn.net/guohecang/article/details/51576829" rel="nofollow">Hive安装与配置</a></span></span></p>
<p style="font-size:14px;font-family:verdana, arial, helvetica, sans-serif;line-height:21px;">
<span style="color:rgb(0,94,167);"><span style="line-height:1.5em;"><a href="http://blog.csdn.net/guohecang/article/details/51576891" rel="nofollow">Hive内建操作符与函数开发</a></span></span></p>
<p style="font-size:14px;font-family:verdana, arial, helvetica, sans-serif;line-height:21px;">
<span style="color:rgb(0,94,167);"><span style="line-height:1.5em;"><a href="http://blog.csdn.net/guohecang/article/details/51576917" rel="nofollow">Hive JDBC</a></span></span></p>
<p style="font-size:14px;font-family:verdana, arial, helvetica, sans-serif;line-height:21px;">
<a href="http://blog.csdn.net/guohecang/article/details/51576933" rel="nofollow">Hive参数</a><br></p>
<p style="font-size:14px;font-family:verdana, arial, helvetica, sans-serif;line-height:21px;">
<span style="color:rgb(0,94,167);"><span style="line-height:1.5em;"><a href="http://blog.csdn.net/guohecang/article/details/51576957" rel="nofollow">Hive高级编程</a></span></span></p>
<p style="font-size:14px;font-family:verdana, arial, helvetica, sans-serif;line-height:21px;">
<span style="color:rgb(0,94,167);"><span style="line-height:1.5em;"><a href="http://blog.csdn.net/guohecang/article/details/51576975" rel="nofollow">Hive QL</a></span></span></p>
<p style="font-size:14px;font-family:verdana, arial, helvetica, sans-serif;line-height:21px;">
<span style="color:rgb(0,94,167);"><span style="line-height:1.5em;"><a href="http://blog.csdn.net/guohecang/article/details/51576993" rel="nofollow">Hive Shell基本操作</a></span></span></p>
<p style="font-size:14px;font-family:verdana, arial, helvetica, sans-serif;line-height:21px;">
<a href="http://blog.csdn.net/guohecang/article/details/51577027" rel="nofollow">Hive优化</a><a href="http://blog.csdn.net/guohecang/article/details/51577027" rel="nofollow"><span style="color:rgb(0,94,167);"><span style="line-height:1.5em;"></span></span></a></p>
<p style="font-size:14px;font-family:verdana, arial, helvetica, sans-serif;line-height:21px;">
<span style="color:rgb(0,94,167);"><span style="line-height:1.5em;"><a href="http://blog.csdn.net/guohecang/article/details/51577040" rel="nofollow">Hive体系结构</a></span></span></p>
<p style="font-size:14px;font-family:verdana, arial, helvetica, sans-serif;line-height:21px;">
<span style="color:rgb(0,94,167);"><span style="line-height:1.5em;"><a href="http://blog.csdn.net/guohecang/article/details/51577052" rel="nofollow">Hive原理</a></span></span></p>
<p style="font-size:14px;line-height:1.5em;">
<a href="http://sishuok.com/forum/blogPost/list/0/6232.html" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;line-height:1.5em;"></a></p>
<p style="font-size:14px;line-height:1.5em;">
<br></p>
<p style="font-size:14px;line-height:1.5em;">
<br></p>
<span class="bold" style="font-size:16px;font-weight:bold;">第一部分：ＤＤＬ</span></div>
<div style="border-width:0px;overflow:hidden;font-family:verdana, arial, helvetica, sans-serif;line-height:21px;background-color:rgb(250,250,250);">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" lang="en-us" style="font-weight:bold;" xml:lang="en-us">DDL</span></div>
</div>
<div style="border-width:0px;overflow:hidden;font-family:verdana, arial, helvetica, sans-serif;line-height:21px;background-color:rgb(250,250,250);">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">•建表</div>
<div style="border-width:0px;overflow:hidden;">•删除表</div>
<div style="border-width:0px;overflow:hidden;">•修改表结构</div>
<div style="border-width:0px;overflow:hidden;">•创建／删除视图</div>
<div style="border-width:0px;overflow:hidden;">•创建数据库</div>
<div style="border-width:0px;overflow:hidden;">•显示命令</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;">建表</span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">CREATE [EXTERNAL] TABLE [IF NOT EXISTS] table_name <br>
  [(col_name data_type [COMMENT col_comment], ...)] <br>
  [COMMENT table_comment] <br>
  [PARTITIONED BY (col_name data_type [COMMENT col_comment], ...)] <br>
  [CLUSTERED BY (col_name, col_name, ...) <br>
  [SORTED BY (col_name [ASC|DESC], ...)] INTO num_buckets BUCKETS] <br>
  [ROW FORMAT row_format] <br>
  [STORED AS file_format] <br>
  [LOCATION hdfs_path]</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">•CREATE TABLE 创建一个指定名字的表。如果相同名字的表已经存在，则抛出异常；用户可以用 IF NOT EXIST 选项来忽略这个异常</div>
<div style="border-width:0px;overflow:hidden;">•EXTERNAL 关键字可以让用户创建一个外部表，在建表的同时指定一个指向实际数据的路径（LOCATION）</div>
<div style="border-width:0px;overflow:hidden;">•LIKE 允许用户复制现有的表结构，但是不复制数据</div>
<div style="border-width:0px;overflow:hidden;">•COMMENT可以为表与字段增加描述</div>
<div style="border-width:0px;overflow:hidden;"> </div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">•ROW FORMAT</div>
<div style="border-width:0px;overflow:hidden;">    DELIMITED [FIELDS TERMINATED BY char] [COLLECTION ITEMS TERMINATED BY char]</div>
<div style="border-width:0px;overflow:hidden;">        [MAP KEYS TERMINATED BY char] [LINES TERMINATED BY char]</div>
<div style="border-width:0px;overflow:hidden;">   | SERDE serde_name [WITH SERDEPROPERTIES (property_name=property_value, property_name=property_value, ...)]</div>
<div style="border-width:0px;overflow:hidden;">         用户在建表的时候可以自定义 SerDe 或者使用自带的 SerDe。如果没有指定 ROW FORMAT 或者 ROW FORMAT DELIMITED，将会使用自带的 SerDe。在建表的时候，用户还需要为表指定列，用户在指定表的列的同时也会指定自定义的 SerDe，Hive 通过 SerDe 确定表的具体的列的数据。</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">•STORED AS</div>
<div style="border-width:0px;overflow:hidden;">            SEQUENCEFILE</div>
<div style="border-width:0px;overflow:hidden;">            | TEXTFILE</div>
<div style="border-width:0px;overflow:hidden;">            | RCFILE    </div>
<div style="border-width:0px;overflow:hidden;">            | INPUTFORMAT input_format_classname OUTPUTFORMAT             output_format_classname</div>
<div style="border-width:0px;overflow:hidden;">       如果文件数据是纯文本，可以使用 STORED AS TEXTFILE。如果数据需要压缩，使用 STORED AS SEQUENCE 。</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;">建立外部表</span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">CREATE EXTERNAL TABLE page_view(viewTime INT, userid BIGINT,</div>
<div style="border-width:0px;overflow:hidden;">     page_url STRING, referrer_url STRING,</div>
<div style="border-width:0px;overflow:hidden;">     ip STRING COMMENT 'IP Address of the User',</div>
<div style="border-width:0px;overflow:hidden;">     country STRING COMMENT 'country of origination')</div>
<div style="border-width:0px;overflow:hidden;"> COMMENT 'This is the staging page view table'</div>
<div style="border-width:0px;overflow:hidden;"> ROW FORMAT DELIMITED FIELDS TERMINATED BY '\054'</div>
<div style="border-width:0px;overflow:hidden;"> STORED AS TEXTFILE</div>
<div style="border-width:0px;overflow:hidden;"> LOCATION '&lt;hdfs_location&gt;';</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;">建分区表</span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">CREATE TABLE par_table(viewTime INT, userid BIGINT,</div>
<div style="border-width:0px;overflow:hidden;">     page_url STRING, referrer_url STRING,</div>
<div style="border-width:0px;overflow:hidden;">     ip STRING COMMENT 'IP Address of the User')</div>
<div style="border-width:0px;overflow:hidden;"> COMMENT 'This is the page view table'</div>
<div style="border-width:0px;overflow:hidden;"> PARTITIONED BY(date STRING, pos STRING)</div>
<div style="border-width:0px;overflow:hidden;">ROW FORMAT DELIMITED ‘\t’</div>
<div style="border-width:0px;overflow:hidden;">   FIELDS TERMINATED BY '\n'</div>
<div style="border-width:0px;overflow:hidden;">STORED AS SEQUENCEFILE;</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;">建Bucket表</span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">CREATE TABLE par_table(viewTime INT, userid BIGINT,</div>
<div style="border-width:0px;overflow:hidden;">     page_url STRING, referrer_url STRING,</div>
<div style="border-width:0px;overflow:hidden;">     ip STRING COMMENT 'IP Address of the User')</div>
<div style="border-width:0px;overflow:hidden;"> COMMENT 'This is the page view table'</div>
<div style="border-width:0px;overflow:hidden;"> PARTITIONED BY(date STRING, pos STRING)</div>
<div style="border-width:0px;overflow:hidden;"> CLUSTERED BY(userid) SORTED BY(viewTime) INTO 32 BUCKETS</div>
<div style="border-width:0px;overflow:hidden;"> ROW FORMAT DELIMITED ‘\t’</div>
<div style="border-width:0px;overflow:hidden;">   FIELDS TERMINATED BY '\n'</div>
<div style="border-width:0px;overflow:hidden;">STORED AS SEQUENCEFILE;</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;">复制一个空表</span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us">CREATE TABLE empty_key_value_store</span></div>
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us">LIKE key_value_store;</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span lang="en-us" xml:lang="en-us"><br></span><span><span class="bold" style="font-weight:bold;">删除表</span></span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><span lang="en-us" xml:lang="en-us">DROP TABLE table_name</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span lang="en-us" xml:lang="en-us"><br></span><span><span class="bold" style="font-weight:bold;">修改表结构</span></span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">•增加分区、删除分区</div>
<div style="border-width:0px;overflow:hidden;">•重命名表</div>
<div style="border-width:0px;overflow:hidden;">•修改列的名字、类型、位置、注释</div>
<div style="border-width:0px;overflow:hidden;">•增加/更新列</div>
<div style="border-width:0px;overflow:hidden;">•增加表的元数据信息</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span lang="en-us" xml:lang="en-us"><br></span><span><span class="bold" style="font-weight:bold;">增加、删除分区</span><br></span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•增加</div>
<div class="O" style="border-width:0px;overflow:hidden;">
ALTER TABLE table_name ADD [IF NOT EXISTS] partition_spec [ LOCATION 'location1' ] partition_spec [ LOCATION 'location2' ] ...</div>
<div class="O" style="border-width:0px;overflow:hidden;">
      partition_spec:</div>
<div class="O" style="border-width:0px;overflow:hidden;">
  : PARTITION (partition_col = partition_col_value, partition_col = partiton_col_value, ...)</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•删除</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
ALTER TABLE table_name DROP partition_spec, partition_spec,...</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span><span class="bold" style="font-weight:bold;">重命名表</span><br></span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">•ALTER TABLE table_name RENAME TO new_table_name </div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span><span class="bold" style="font-weight:bold;">修改列的名字、类型、位置、注释</span></span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">•ALTER TABLE table_name CHANGE [COLUMN] col_old_name col_new_name column_type [COMMENT col_comment] [FIRST|AFTER column_name]</div>
<div style="border-width:0px;overflow:hidden;">•这个命令可以允许改变列名、数据类型、注释、列位置或者它们的任意组合</div>
<div style="border-width:0px;overflow:hidden;"> </div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;">增加<span lang="en-us" xml:lang="en-us">/</span>更新列</span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">•ALTER TABLE table_name ADD|REPLACE COLUMNS (col_name data_type [COMMENT col_comment], ...)  </div>
<div style="border-width:0px;overflow:hidden;">    </div>
<div style="border-width:0px;overflow:hidden;">• ADD是代表新增一字段，字段位置在所有列后面(partition列前)</div>
<div style="border-width:0px;overflow:hidden;">     REPLACE则是表示替换表中所有字段。</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;">增加表的元数据信息</span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">•ALTER TABLE table_name SET TBLPROPERTIES table_properties table_properties:</div>
<div style="border-width:0px;overflow:hidden;">         :[property_name = property_value…..]</div>
<div style="border-width:0px;overflow:hidden;"> </div>
<div style="border-width:0px;overflow:hidden;">•用户可以用这个命令向表中增加metadata</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span><span class="bold" style="font-weight:bold;">改变表文件格式与组织</span></span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">•ALTER TABLE table_name SET FILEFORMAT file_format</div>
<div style="border-width:0px;overflow:hidden;">•ALTER TABLE table_name CLUSTERED BY(userid) SORTED BY(viewTime) INTO num_buckets BUCKETS</div>
<div style="border-width:0px;overflow:hidden;"> </div>
<div style="border-width:0px;overflow:hidden;">•这个命令修改了表的物理存储属性</div>
</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;">创建／删除视图</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•CREATE VIEW [IF NOT EXISTS] view_name [ (column_name [COMMENT column_comment], ...) ][COMMENT view_comment][TBLPROPERTIES (property_name = property_value, ...)] AS SELECT</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•增加视图</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•如果没有提供表名，视图列的名字将由定义的SELECT表达式自动生成</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•如果修改基本表的属性，视图中不会体现，无效查询将会失败</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•视图是只读的，不能用LOAD/INSERT/ALTER</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•DROP VIEW view_name</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•删除视图</div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span><span class="bold" style="font-weight:bold;">创建数据库</span></span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">•CREATE DATABASE name</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span><span class="bold" style="font-weight:bold;">显示命令</span><br></span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">•show tables;</div>
<div style="border-width:0px;overflow:hidden;">•show databases;</div>
<div style="border-width:0px;overflow:hidden;">•show partitions ;</div>
<div style="border-width:0px;overflow:hidden;">•show functions</div>
<div style="border-width:0px;overflow:hidden;">•describe extended table_name dot col_name</div>
<div style="border-width:0px;overflow:hidden;"> </div>
</div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><span class="bold" style="font-size:16px;font-weight:bold;">第二部分：ＤＭＬ</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" lang="en-us" style="font-weight:bold;" xml:lang="en-us">DML</span></div>
</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">•向数据表内加载文件</div>
<div style="border-width:0px;overflow:hidden;">•将查询结果插入到Hive表中</div>
<div style="border-width:0px;overflow:hidden;">•0.8新特性 insert into</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;">向数据表内加载文件</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•LOAD DATA [LOCAL] INPATH 'filepath' [OVERWRITE] INTO TABLE tablename [PARTITION (partcol1=val1, partcol2=val2 ...)]</div>
<div class="O" style="border-width:0px;overflow:hidden;">
 </div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•Load 操作只是单纯的复制/移动操作，将数据文件移动到 Hive 表对应的位置。</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•filepath</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•相对路径，例如：project/data1</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•绝对路径，例如： /user/hive/project/data1</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•包含模式的完整 URI，例如：hdfs://namenode:9000/user/hive/project/data1</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;">向数据表内加载文件</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•加载的目标可以是一个表或者分区。如果表包含分区，必须指定每一个分区的分区名</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•filepath 可以引用一个文件（这种情况下，Hive 会将文件移动到表所对应的目录中）或者是一个目录（在这种情况下，Hive 会将目录中的所有文件移动至表所对应的目录中）</div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" lang="en-us" style="font-weight:bold;" xml:lang="en-us">LOCAL关键字</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•指定了LOCAL</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•load 命令会去查找本地文件系统中的 filepath。如果发现是相对路径，则路径会被解释为相对于当前用户的当前路径。用户也可以为本地文件指定一个完整的 URI，比如：file:///user/hive/project/data1.</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•load 命令会将 filepath 中的文件复制到目标文件系统中。目标文件系统由表的位置属性决定。被复制的数据文件移动到表的数据对应的位置</div>
<div class="O" style="border-width:0px;overflow:hidden;">
• 没有指定LOCAL</div>
<div class="O" style="border-width:0px;overflow:hidden;">
         如果 filepath 指向的是一个完整的 URI，hive 会直接使用这个 URI。 否则</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•如果没有指定 schema 或者 authority，Hive 会使用在 hadoop 配置文件中定义的 schema 和 authority，fs.default.name 指定了 Namenode 的 URI</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•如果路径不是绝对的，Hive 相对于 /user/ 进行解释。 Hive 会将 filepath 中指定的文件内容移动到 table （或者 partition）所指定的路径中</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
 </div>
<div class="O1" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" lang="en-us" style="font-weight:bold;" xml:lang="en-us">OVERWRITE</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•指定了OVERWRITE</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•目标表（或者分区）中的内容（如果有）会被删除，然后再将 filepath 指向的文件/目录中的内容添加到表/分区中。</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
 </div>
<div class="O1" style="border-width:0px;overflow:hidden;">
 </div>
<div class="O" style="border-width:0px;overflow:hidden;">
•如果目标表（分区）已经有一个文件，并且文件名和 filepath 中的文件名冲突，那么现有的文件会被新文件所替代。</div>
<div class="O" style="border-width:0px;overflow:hidden;">
 </div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;">将查询结果插入Hive表</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•将查询结果插入Hive表</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•将查询结果写入HDFS文件系统</div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•基本模式</div>
<div class="O" style="border-width:0px;overflow:hidden;">
     INSERT OVERWRITE TABLE tablename1 [PARTITION (partcol1=val1, partcol2=val2 ...)] select_statement1 FROM from_statement</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•多插入模式</div>
<div class="O" style="border-width:0px;overflow:hidden;">
 FROM from_statement</div>
<div class="O" style="border-width:0px;overflow:hidden;">
INSERT OVERWRITE TABLE tablename1 [PARTITION (partcol1=val1, partcol2=val2 ...)] select_statement1</div>
<div class="O" style="border-width:0px;overflow:hidden;">
[INSERT OVERWRITE TABLE tablename2 [PARTITION ...] select_statement2] ...</div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•自动分区模式</div>
<div class="O" style="border-width:0px;overflow:hidden;">
 INSERT OVERWRITE TABLE tablename PARTITION (partcol1[=val1], partcol2[=val2] ...) select_statement FROM from_statement</div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;">将查询结果写入<span lang="en-us" xml:lang="en-us">HDFS</span>文件系统</span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">•INSERT OVERWRITE [LOCAL] DIRECTORY directory1 SELECT ... FROM ...</div>
<div style="border-width:0px;overflow:hidden;">        FROM from_statement</div>
<div style="border-width:0px;overflow:hidden;">        INSERT OVERWRITE [LOCAL] DIRECTORY directory1 select_statement1</div>
<div style="border-width:0px;overflow:hidden;">     [INSERT OVERWRITE [LOCAL] DIRECTORY directory2 select_statement2]</div>
<div style="border-width:0px;overflow:hidden;">•</div>
<div style="border-width:0px;overflow:hidden;">•数据写入文件系统时进行文本序列化，且每列用^A 来区分，\n换行</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">INSERT </span><span lang="en-us" xml:lang="en-us">INTO </span></span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">•INSERT INTO  TABLE tablename1 [PARTITION (partcol1=val1, partcol2=val2 ...)] select_statement1 FROM from_statement</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><span class="bold" style="font-size:16px;font-weight:bold;">第三部分：HiveQL 查询操作</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;">SQL操作</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•基本的Select 操作</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•基于Partition的查询</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•Join</div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-size:16px;font-weight:bold;">基本的<span lang="en-us" style="font-size:12px;" xml:lang="en-us">Select </span>操作</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
SELECT [ALL | DISTINCT] select_expr, select_expr, ...</div>
<div class="O" style="border-width:0px;overflow:hidden;">
FROM table_reference</div>
<div class="O" style="border-width:0px;overflow:hidden;">
[WHERE where_condition]</div>
<div class="O" style="border-width:0px;overflow:hidden;">
[GROUP BY col_list [HAVING condition]]</div>
<div class="O" style="border-width:0px;overflow:hidden;">
[   CLUSTER BY col_list</div>
<div class="O" style="border-width:0px;overflow:hidden;">
  | [DISTRIBUTE BY col_list] [SORT BY| ORDER BY col_list]</div>
<div class="O" style="border-width:0px;overflow:hidden;">
]</div>
<div class="O" style="border-width:0px;overflow:hidden;">
[LIMIT number]</div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•使用ALL和DISTINCT选项区分对重复记录的处理。默认是ALL，表示查询所有记录。DISTINCT表示去掉重复的记录</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•Where 条件</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•类似我们传统SQL的where 条件</div>
<div class="O2" style="border-width:0px;overflow:hidden;">
•目前支持 AND,OR ,0.9版本支持between</div>
<div class="O2" style="border-width:0px;overflow:hidden;">
•IN, NOT IN</div>
<div class="O2" style="border-width:0px;overflow:hidden;">
•不支持EXIST ,NOT EXIST</div>
<div class="O2" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">ORDER BY</span>与<span lang="en-us" xml:lang="en-us">SORT BY</span>的不同</span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">•ORDER BY 全局排序，只有一个Reduce任务</div>
<div style="border-width:0px;overflow:hidden;">•SORT BY 只在本机做排序</div>
<div style="border-width:0px;overflow:hidden;"> </div>
</div>
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" lang="en-us" style="font-weight:bold;" xml:lang="en-us">Limit</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•Limit 可以限制查询的记录数</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
SELECT * FROM t1 LIMIT 5</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•实现Top k 查询</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•下面的查询语句查询销售记录最大的 5 个销售代表。</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
SET mapred.reduce.tasks = 1 <br>
  SELECT * FROM test SORT BY amount DESC LIMIT 5</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•REGEX Column Specification</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
SELECT 语句可以使用正则表达式做列选择，下面的语句查询除了 ds 和 hr 之外的所有列：</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
SELECT `(ds|hr)?+.+` FROM test</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;">基于<span lang="en-us" xml:lang="en-us">Partition</span>的查询</span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">•一般 SELECT 查询会扫描整个表，使用 PARTITIONED BY 子句建表，查询就可以利用分区剪枝（input pruning）的特性</div>
<div style="border-width:0px;overflow:hidden;">•Hive 当前的实现是，只有分区断言出现在离 FROM 子句最近的那个WHERE 子句中，才会启用分区剪枝</div>
<div style="border-width:0px;overflow:hidden;"> </div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" lang="en-us" style="font-weight:bold;" xml:lang="en-us">Join</span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">Syntax</div>
<div style="border-width:0px;overflow:hidden;">join_table: <br>
   table_reference JOIN table_factor [join_condition] <br>
  | table_reference {LEFT|RIGHT|FULL} [OUTER] JOIN table_reference join_condition <br>
  | table_reference LEFT SEMI JOIN table_reference join_condition <br><br>
table_reference: <br>
    table_factor <br>
  | join_table <br><br>
table_factor: <br>
    tbl_name [alias] <br>
  | table_subquery alias <br>
  | ( table_references ) <br><br>
join_condition: <br>
    ON equality_expression ( AND equality_expression )* <br><br>
equality_expression: <br>
    expression = expression</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">•Hive 只支持等值连接（equality joins）、外连接（outer joins）和（left semi joins）。Hive 不支持所有非等值的连接，因为非等值连接非常难转化到 map/reduce 任务</div>
<div style="border-width:0px;overflow:hidden;"> </div>
<div style="border-width:0px;overflow:hidden;">•LEFT，RIGHT和FULL OUTER关键字用于处理join中空记录的情况</div>
<div style="border-width:0px;overflow:hidden;">•LEFT SEMI JOIN 是 IN/EXISTS 子查询的一种更高效的实现</div>
<div style="border-width:0px;overflow:hidden;">•join 时，每次 map/reduce 任务的逻辑是这样的：reducer 会缓存 join 序列中除了最后一个表的所有表的记录，再通过最后一个表将结果序列化到文件系统</div>
<div style="border-width:0px;overflow:hidden;">•实践中，应该把最大的那个表写在最后</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">join </span>查询时，需要注意几个关键点</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•只支持等值join</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•SELECT a.* FROM a JOIN b ON (a.id = b.id)</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•SELECT a.* FROM a JOIN b <br>
    ON (a.id = b.id AND a.department = b.department)</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•可以 join 多于 2 个表，例如</div>
<div class="O" style="border-width:0px;overflow:hidden;">
  SELECT a.val, b.val, c.val FROM a JOIN b <br>
    ON (a.key = b.key1) JOIN c ON (c.key = b.key2)</div>
<div class="O" style="border-width:0px;overflow:hidden;">
 </div>
<div class="O" style="border-width:0px;overflow:hidden;">
•如果join中多个表的 join key 是同一个，则 join 会被转化为单个 map/reduce 任务</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">LEFT</span>，<span lang="en-us" xml:lang="en-us">RIGHT</span>和<span lang="en-us" xml:lang="en-us">FULL OUTER</span></span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•例子</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•SELECT a.val, b.val FROM a LEFT OUTER JOIN b ON (a.key=b.key)</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
 </div>
<div class="O" style="border-width:0px;overflow:hidden;">
•如果你想限制 join 的输出，应该在 WHERE 子句中写过滤条件——或是在 join 子句中写</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•容易混淆的问题是表分区的情况</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
• SELECT c.val, d.val FROM c LEFT OUTER JOIN d ON (c.key=d.key) <br>
  WHERE a.ds='2010-07-07' AND b.ds='2010-07-07‘</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•如果 d 表中找不到对应 c 表的记录，d 表的所有列都会列出 NULL，包括 ds 列。也就是说，join 会过滤 d 表中不能找到匹配 c 表 join key 的所有记录。这样的话，LEFT OUTER 就使得查询结果与 WHERE 子句无关</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•解决办法</div>
<div class="O2" style="border-width:0px;overflow:hidden;">
•SELECT c.val, d.val FROM c LEFT OUTER JOIN d <br>
  ON (c.key=d.key AND d.ds='2009-07-07' AND c.ds='2009-07-07')</div>
<div class="O2" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" lang="en-us" style="font-weight:bold;" xml:lang="en-us">LEFT SEMI JOIN</span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">•LEFT SEMI JOIN 的限制是， JOIN 子句中右边的表只能在 ON 子句中设置过滤条件，在 WHERE 子句、SELECT 子句或其他地方过滤都不行</div>
<div style="border-width:0px;overflow:hidden;">•</div>
<div style="border-width:0px;overflow:hidden;">•SELECT a.key, a.value <br>
  FROM a <br>
  WHERE a.key in <br>
   (SELECT b.key <br>
    FROM B);</div>
<div style="border-width:0px;overflow:hidden;">       可以被重写为：</div>
<div style="border-width:0px;overflow:hidden;">      SELECT a.key, a.val <br>
   FROM a LEFT SEMI JOIN b on (a.key = b.key)</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" lang="en-us" style="font-weight:bold;" xml:lang="en-us">UNION ALL</span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">•用来合并多个select的查询结果，需要保证select中字段须一致</div>
<div style="border-width:0px;overflow:hidden;">•select_statement UNION ALL select_statement UNION ALL select_statement ...</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><span class="bold" style="font-size:16px;font-weight:bold;">第四部分：从SQL到HiveQL应该转变的几个习惯</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">Hive</span>不支持等值连接</span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•SQL中对两表内联可以写成：</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•select * from dual a,dual b where a.key = b.key;</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•Hive中应为</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•select * from dual a join dual b on a.key = b.key; </div>
<div class="O" style="border-width:0px;overflow:hidden;">
 
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;">分号字符</span></div>
</div>
</div>
</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•分号是SQL语句结束标记，在HiveQL中也是，但是在HiveQL中，对分号的识别没有那么智慧，例如：</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•select concat(key,concat(';',key)) from dual;</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•但HiveQL在解析语句时提示：</div>
<div class="O" style="border-width:0px;overflow:hidden;">
        FAILED: Parse Error: line 0:-1 mismatched input '&lt;EOF&gt;' expecting ) in function specification</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•解决的办法是，使用分号的八进制的ASCII码进行转义，那么上述语句应写成：</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•select concat(key,concat('\073',key)) from dual;</div>
<div class="O" style="border-width:0px;overflow:hidden;">
 </div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span lang="en-us" xml:lang="en-us"><span>IS [NOT] NULL</span></span></div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">•SQL中null代表空值, 值得警惕的是, 在HiveQL中String类型的字段若是空(empty)字符串, 即长度为0, 那么对它进行IS NULL的判断结果是False.</div>
<div style="border-width:0px;overflow:hidden;"> </div>
<div style="border-width:0px;overflow:hidden;">转载请注明【 <a href="http://sishuok.com/forum/blogPost/list/6227.html" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;">http://sishuok.com/forum/blogPost/list/6227.html</a>】</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
            </div>
                </div>