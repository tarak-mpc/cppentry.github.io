---
layout:     post
title:      hadoop hive sql
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
创建表<br>
hive&gt; CREATE TABLE pokes (foo INT, bar STRING); <br>
创建表并创建索引字段ds<br>
hive&gt; CREATE TABLE invites (foo INT, bar STRING) PARTITIONED BY (ds STRING); <br>
显示所有表<br>
hive&gt; SHOW TABLES;<br>
按正条件（正则表达式）显示表，<br>
hive&gt; SHOW TABLES '.*s';<br>
表添加一列 <br>
hive&gt; ALTER TABLE pokes ADD COLUMNS (new_col INT);<br>
添加一列并增加列字段注释<br>
hive&gt; ALTER TABLE invites ADD COLUMNS (new_col2 INT COMMENT 'a comment');<br>
更改表名<br>
hive&gt; ALTER TABLE events RENAME TO 3koobecaf;<br>
删除列<br>
hive&gt; DROP TABLE pokes;<br>
元数据存储<br>
将文件中的数据加载到表中<br>
hive&gt; LOAD DATA LOCAL INPATH './examples/files/kv1.txt' OVERWRITE INTO TABLE pokes;
<br>
加载本地数据，同时给定分区信息<br>
hive&gt; LOAD DATA LOCAL INPATH './examples/files/kv2.txt' OVERWRITE INTO TABLE invites PARTITION (ds='2008-08-15');<br>
加载DFS数据 ，同时给定分区信息<br>
hive&gt; LOAD DATA INPATH '/user/myname/kv2.txt' OVERWRITE INTO TABLE invites PARTITION (ds='2008-08-15');<br>
The above command will load data from an HDFS file/directory to the table. Note that loading data from HDFS will result in moving the file/directory. As a result, the operation is almost instantaneous.
<br>
SQL 操作<br>
按先件查询<br>
hive&gt; SELECT a.foo FROM invites a WHERE a.ds='&lt;DATE&gt;';<br>
将查询数据输出至目录<br>
hive&gt; INSERT OVERWRITE DIRECTORY '/tmp/hdfs_out' SELECT a.* FROM invites a WHERE a.ds='&lt;DATE&gt;';<br>
将查询结果输出至本地目录<br>
hive&gt; INSERT OVERWRITE LOCAL DIRECTORY '/tmp/local_out' SELECT a.* FROM pokes a;<br>
选择所有列到本地目录 <br>
hive&gt; INSERT OVERWRITE TABLE events SELECT a.* FROM profiles a;<br>
hive&gt; INSERT OVERWRITE TABLE events SELECT a.* FROM profiles a WHERE a.key &lt; 100;
<br>
hive&gt; INSERT OVERWRITE LOCAL DIRECTORY '/tmp/reg_3' SELECT a.* FROM events a;<br>
hive&gt; INSERT OVERWRITE DIRECTORY '/tmp/reg_4' select a.invites, a.pokes FROM profiles a;<br>
hive&gt; INSERT OVERWRITE DIRECTORY '/tmp/reg_5' SELECT COUNT(1) FROM invites a WHERE a.ds='&lt;DATE&gt;';<br>
hive&gt; INSERT OVERWRITE DIRECTORY '/tmp/reg_5' SELECT a.foo, a.bar FROM invites a;<br>
hive&gt; INSERT OVERWRITE LOCAL DIRECTORY '/tmp/sum' SELECT SUM(a.pc) FROM pc1 a;<br>
将一个表的统计结果插入另一个表中<br>
hive&gt; FROM invites a INSERT OVERWRITE TABLE events SELECT a.bar, count(1) WHERE a.foo &gt; 0 GROUP BY a.bar;<br>
hive&gt; INSERT OVERWRITE TABLE events SELECT a.bar, count(1) FROM invites a WHERE a.foo &gt; 0 GROUP BY a.bar;<br>
JOIN<br>
hive&gt; FROM pokes t1 JOIN invites t2 ON (t1.bar = t2.bar) INSERT OVERWRITE TABLE events SELECT t1.bar, t1.foo, t2.foo;<br>
将多表数据插入到同一表中<br>
FROM src<br>
INSERT OVERWRITE TABLE dest1 SELECT src.* WHERE src.key &lt; 100<br>
INSERT OVERWRITE TABLE dest2 SELECT src.key, src.value WHERE src.key &gt;= 100 and src.key &lt; 200<br>
INSERT OVERWRITE TABLE dest3 PARTITION(ds='2008-04-08', hr='12') SELECT src.key WHERE src.key &gt;= 200 and src.key &lt; 300<br>
INSERT OVERWRITE LOCAL DIRECTORY '/tmp/dest4.out' SELECT src.value WHERE src.key &gt;= 300;<br>
将文件流直接插入文件<br>
hive&gt; FROM invites a INSERT OVERWRITE TABLE events SELECT TRANSFORM(a.foo, a.bar) AS (oof, rab) USING '/bin/cat' WHERE a.ds &gt; '2008-08-09';<br>
This streams the data in the map phase through the script /bin/cat (like hadoop streaming). Similarly - streaming can be used on the reduce side (please see the Hive Tutorial or examples)
<br>
实际示例<br>
创建一个表<br>
CREATE TABLE u_data (<br>
userid INT,<br>
movieid INT,<br>
rating INT,<br>
unixtime STRING)<br>
ROW FORMAT DELIMITED<br>
FIELDS TERMINATED BY '\t'<br>
STORED AS TEXTFILE;<br>
下载示例数据文件，并解压缩<br>
wget <a href="http://www.grouplens.org/system/files/ml-data.tar__0.gz" rel="nofollow">
<span style="color:#336699;">http://www.grouplens.org/system/files/ml-data.tar__0.gz</span></a><br>
tar xvzf ml-data.tar__0.gz<br>
加载数据到表中<br>
LOAD DATA LOCAL INPATH 'ml-data/u.data'<br>
OVERWRITE INTO TABLE u_data;<br>
统计数据总量<br>
SELECT COUNT(1) FROM u_data;<br>
现在做一些复杂的数据分析<br>
创建一个 weekday_mapper.py: 文件，作为数据按周进行分割 <br>
import sys<br>
import datetime<br>
for line in sys.stdin:<br>
line = line.strip()<br>
userid, movieid, rating, unixtime = line.split('\t')<br>
生成数据的周信息<br>
weekday = datetime.datetime.fromtimestamp(float(unixtime)).isoweekday()<br>
print '\t'.join([userid, movieid, rating, str(weekday)])<br>
使用映射脚本<br>
//创建表，按分割符分割行中的字段值<br>
CREATE TABLE u_data_new (<br>
userid INT,<br>
movieid INT,<br>
rating INT,<br>
weekday INT)<br>
ROW FORMAT DELIMITED<br>
FIELDS TERMINATED BY '\t';<br>
//将python文件加载到系统<br>
add FILE weekday_mapper.py;<br>
将数据按周进行分割<br>
INSERT OVERWRITE TABLE u_data_new<br>
SELECT<br>
TRANSFORM (userid, movieid, rating, unixtime)<br>
USING 'python weekday_mapper.py'<br>
AS (userid, movieid, rating, weekday)<br>
FROM u_data;<br>
SELECT weekday, COUNT(1)<br>
FROM u_data_new<br>
GROUP BY weekday;<br>            </div>
                </div>