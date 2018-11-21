---
layout:     post
title:      Hbase shell
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
HBASE SHELL简介<br><br>
作为入门简要介绍下HBASE SHELL命令，通过这些命令大概可以知道HBASE的存储结构和如何操作HBASE的表。本篇先介绍DDL和DML的简单命令的使用案例，相关的原理和底层逻辑不再介绍之列。<br>
TOOLS、REPLICATION和GENERAL信息待对HBASE的架构有深入了解后再整理汇总。<br>
HBASE SHELL汇总：<br><span style="color:#ff0000;">DDL</span><br>
alter, create, describe, disable, drop, enable, exists, is_disabled, is_enabled, list<br><span style="color:#ff0000;">DML</span><br>
count, delete, deleteall, get, get_counter, incr, put, scan, truncate<br><span style="color:#ff0000;">TOOLS</span><br>
assign, balance_switch, balancer, close_region, compact, flush, major_compact, move, split, unassign, zk_dump<br><span style="color:#ff0000;">REPLICATION</span><br>
add_peer, disable_peer, enable_peer, remove_peer, start_replication, stop_replication<br><span style="color:#ff0000;">GENERAL</span><br><p>status, version</p>
<p><span style="color:#ff0000;"><br></span></p>
<span style="color:#ff0000;"><strong>DDL:</strong></span><br>
exists:<br>
功能：判断指定的表是否存在<br>
exists 'tb1'<br>
hbase(main):015:0&gt; exists 'wp_test'<br>
Table wp_test does exist<br>
0 row(s) in 0.0250 seconds<br>
-------------------------------------<br>
hbase(main):016:0&gt; exists 'wp_test1'<br>
Table wp_test1 does not exist<br>
0 row(s) in 0.0240 seconds<br>
例子：<br>
list:<br>
功能：罗列出hbase中所有的表，支持正则匹配(同HIVE的show tables)<br>
hbase(main):020:0&gt; list<br>
TABLE<br>
member<br>
member3<br>
test_regions<br>
testtable<br>
wp_test<br>
x2<br>
6 row(s) in 0.0300 seconds<br>
-------------------------------------<br>
hbase(main):022:0&gt; list 'mem*'<br>
TABLE<br>
member<br>
member3<br>
2 row(s) in 0.0260 seconds<br>
例子：<br>
describe:<br>
功能：展示制定的表的具体描述信息，包括表名和column families的信息，没有包含row的信息(功能同HIVE的describe)<br>
hbase(main):009:0&gt; describe 'wp_test'<br>
DESCRIPTION ENABLED<br>
{NAME =&gt; 'wp_test', FAMILIES =&gt; [{NAME =&gt; 'id', BLOOMFILTER =&gt; 'NONE', REPLICATION_SCOPE =&gt; '0', COMPRESSION =&gt; ' false<br>
NONE', VERSIONS =&gt; '3', TTL =&gt; '2147483647', BLOCKSIZE =&gt; '65536', IN_MEMORY =&gt; 'false', BLOCKCACHE =&gt; 'true'}, {<br>
NAME =&gt; 'name', BLOOMFILTER =&gt; 'NONE', REPLICATION_SCOPE =&gt; '0', COMPRESSION =&gt; 'NONE', VERSIONS =&gt; '3', TTL =&gt; '<br>
2147483647', BLOCKSIZE =&gt; '65536', IN_MEMORY =&gt; 'false', BLOCKCACHE =&gt; 'true'}]}<br>
1 row(s) in 0.0340 seconds<br>
这里的'ENABLED'字段没有任何参考价值，就算disable一张表，describe后还是enabled。<br>
例子：<br>
create:<br>
官方例子：<br>
hbase&gt; create 't1', {NAME =&gt; 'f1', VERSIONS =&gt; 5}<br>
hbase&gt; create 't1', {NAME =&gt; 'f1'}, {NAME =&gt; 'f2'}, {NAME =&gt; 'f3'}<br>
功能：创建HBASE的表，必须传入的参数为：表名+column famliy名。column family除了指定名称，一些配置参数也可以在建表时指定：BLOOMFILTER, REPLICATION_SCOPE,COMPRESSION,VERSIONS,TTL,BLOCKSIZE,IN_MEMORY,BLOCKCACHE(这些参数还没研究，不做详细说明)<br>
2011年12月31日<br>
16:14<br>
分区日常工作的第1 页<br>
hbase&gt; create 't1', {NAME =&gt; 'f1'}, {NAME =&gt; 'f2'}, {NAME =&gt; 'f3'}<br>
hbase&gt; # The above in shorthand would be the following:<br>
hbase&gt; create 't1', 'f1', 'f2', 'f3'<br>
hbase&gt; create 't1', {NAME =&gt; 'f1', VERSIONS =&gt; 1, TTL =&gt; 2592000, BLOCKCACHE =&gt; true}<br>
hbase(main):014:0&gt; create 'wp_test','id','name'<br>
0 row(s) in 1.1770 seconds<br>
这样就创建了一张hbase的表wp_test，表里有两个family: id; name。family里具体列的信息这里没有指定。想看表结构就可以直接describe 'wp_test'<br>
例子：<br>
disable:<br>
功能：使指定的表失效。目前只知道drop前必须disable，待研究HBASE的data model后disable应该会有更多的作用。<br>
hbase(main):017:0&gt; disable 'wp_test'<br>
0 row(s) in 2.0400 seconds<br>
disable成功了，如果不确定表是否被disable过，可以用is_disabled命令<br>
例子：<br>
is_disabled:<br>
功能：判断制定的表是否失效，如果失效返回true，否则返回false(当表不存在时，也返回false)，此命令和is_enabled对应，此命令返回true，彼命令就返回false，反之亦然<br>
hbase(main):018:0&gt; is_disabled 'wp_test'<br>
true<br>
0 row(s) in 0.0140 seconds<br>
例子：<br>
drop:<br>
功能：删除指定的表。drop前必须要先使表disabled，即执行drop前要执行disable<br>
------------------没有disable的结果------------------------------<br>
hbase(main):028:0&gt; is_disabled 'wp_test'<br>
false<br>
0 row(s) in 0.0130 seconds<br>
hbase(main):029:0&gt; drop 'wp_test'<br>
ERROR: Table wp_test is enabled. Disable it first.'<br>
------------------没有disable的结果------------------------------<br>
hbase(main):030:0&gt; disable 'wp_test'<br>
0 row(s) in 2.0390 seconds<br>
hbase(main):031:0&gt; is_disabled 'wp_test'<br>
true<br>
0 row(s) in 0.0150 seconds<br>
hbase(main):032:0&gt; drop 'wp_test'<br>
0 row(s) in 1.1050 seconds<br>
hbase(main):033:0&gt; exists 'wp_test'<br>
Table wp_test does not exist<br>
0 row(s) in 0.0250 seconds<br>
例子：<br>
enable:<br>
功能：使失效的表有效。<br>
hbase(main):042:0&gt; is_enabled 'wp_test'<br>
false<br>
0 row(s) in 0.0140 seconds<br>
hbase(main):043:0&gt; enable 'wp_test'<br>
0 row(s) in 2.0420 seconds<br>
hbase(main):044:0&gt; is_enabled 'wp_test'<br>
true<br>
0 row(s) in 0.0140 seconds<br>
------------------已有效的表可以重复使用此命令------------------------------<br>
hbase(main):044:0&gt; is_enabled 'wp_test'<br>
true<br>
0 row(s) in 0.0140 seconds<br>
hbase(main):045:0&gt; enable 'wp_test'<br>
0 row(s) in 0.0260 seconds<br>
hbase(main):046:0&gt; is_enabled 'wp_test'<br>
true<br>
0 row(s) in 0.0140 seconds<br>
例子：<br>
is_enabled:<br>
功能：判断制定的表是否有效，如果有效返回true，否则返回false(当表不存在时，返回true)，此命令和is_disabled对应，此命令返回true，彼命令就返回false，反之亦然<br>
hbase(main):053:0&gt; is_enabled 'wp_test'<br>
true<br>
0 row(s) in 0.0140 seconds<br>
因为当表不存在时，is_enabled也返回true，所以不能盲目的认为返回true，就代表表有效<br>
hbase(main):056:0&gt; exists 'wp_test'<br>
Table wp_test does not exist<br>
0 row(s) in 0.0310 seconds<br>
hbase(main):057:0&gt; is_enabled 'wp_test'<br>
true<br>
0 row(s) in 0.0140 seconds<br>
例子：<br>
alter:(等真正体验过alter每个元素的效果后补充)<br>
官方例子：<br>
To change or add the 'f1' column family in table 't1' from defaults<br>
功能：修改column family的信息。<br>
分区日常工作的第2 页<br>
To change or add the 'f1' column family in table 't1' from defaults<br>
to instead keep a maximum of 5 cell VERSIONS, do:<br>
hbase&gt; alter 't1', NAME =&gt; 'f1', VERSIONS =&gt; 5<br>
To delete the 'f1' column family in table 't1', do:<br>
hbase&gt; alter 't1', NAME =&gt; 'f1', METHOD =&gt; 'delete'<br>
or a shorter version:<br>
hbase&gt; alter 't1', 'delete' =&gt; 'f1'<br>
You can also change table-scope attributes like MAX_FILESIZE<br>
MEMSTORE_FLUSHSIZE, READONLY, and DEFERRED_LOG_FLUSH.<br>
For example, to change the max size of a family to 128MB, do:<br>
hbase&gt; alter 't1', METHOD =&gt; 'table_att', MAX_FILESIZE =&gt; '134217728'<br>
There could be more than one alteration in one command:<br>
hbase&gt; alter 't1', {NAME =&gt; 'f1'}, {NAME =&gt; 'f2', METHOD =&gt; 'delete'}<br><p>例子：</p>
<p><br></p>
<strong style="background-color:rgb(255,255,255);"><span style="color:#ff0000;">DML:</span></strong><br>
put:<br>
功能：向HBASE表中插入数据。语法：put table_name,row_key,column_family1,value1[,column_family2,value2,...][,timestamp]<br>
hbase(main):006:0&gt; put 'wp_test','1','id','001'<br>
0 row(s) in 0.1630 seconds<br>
hbase(main):007:0&gt; put 'wp_test','1','name','hugh.wangp'<br>
0 row(s) in 0.0200 seconds<br>
hbase(main):008:0&gt; scan 'wp_test'<br>
ROW COLUMN+CELL<br>
1 column=id:, timestamp=1325298164708, value=001<br>
1 column=name:, timestamp=1325298179649, value=hugh.wangp<br>
1 row(s) in 0.0600 seconds<br>
-------------------------如上还没体现出family的概念---------------------<br>
hbase(main):009:0&gt; put 'wp_test','1','name:first','hugh'<br>
0 row(s) in 0.0170 seconds<br>
hbase(main):010:0&gt; put 'wp_test','1','name:last','wangp'<br>
0 row(s) in 0.0200 seconds<br>
hbase(main):011:0&gt; scan 'wp_test'<br>
ROW COLUMN+CELL<br>
1 column=id:, timestamp=1325298164708, value=001<br>
1 column=name:, timestamp=1325298179649, value=hugh.wangp<br>
1 column=name:first, timestamp=1325298260659, value=hugh<br>
1 column=name:last, timestamp=1325298276137, value=wangp<br>
1 row(s) in 0.0270 seconds<br>
如上的就是把name作为column family来看待，name有两个列：first，last，可以独立分别操作first和last<br>
例子：<br>
get:<br>
官方例子：<br>
hbase&gt; get 't1', 'r1'<br>
hbase&gt; get 't1', 'r1', {TIMERANGE =&gt; [ts1, ts2]}<br>
hbase&gt; get 't1', 'r1', {COLUMN =&gt; 'c1'}<br>
hbase&gt; get 't1', 'r1', {COLUMN =&gt; ['c1', 'c2', 'c3']}<br>
hbase&gt; get 't1', 'r1', {COLUMN =&gt; 'c1', TIMESTAMP =&gt; ts1}<br>
hbase&gt; get 't1', 'r1', {COLUMN =&gt; 'c1', TIMERANGE =&gt; [ts1, ts2], VERSIONS =&gt; 4}<br>
hbase&gt; get 't1', 'r1', {COLUMN =&gt; 'c1', TIMESTAMP =&gt; ts1, VERSIONS =&gt; 4}<br>
hbase&gt; get 't1', 'r1', 'c1'<br>
hbase&gt; get 't1', 'r1', 'c1', 'c2'<br>
hbase&gt; get 't1', 'r1', ['c1', 'c2']<br>
功能：获取column或者cell的内容。必须传入的参数:表名+rowkey，可选column families、timestamp、timerange、versions<br>
-------------------------获取rowkey='1'的所有column和cell的信息---------------------<br>
hbase(main):029:0&gt; get 'wp_test','1'<br>
COLUMN CELL<br>
id: timestamp=1325298164708, value=001<br>
name: timestamp=1325298179649, value=hugh.wangp<br>
name:first timestamp=1325298260659, value=hugh<br>
name:last timestamp=1325298276137, value=wangp<br>
-------------------------获取rowkey='1'的特定列族的信息------------------------------<br>
hbase(main):041:0&gt; get 'wp_test','1',{COLUMN =&gt; 'id'}<br>
COLUMN CELL<br>
id: timestamp=1325298164708, value=001<br>
1 row(s) in 0.0170 seconds<br>
简单方式<br>
hbase(main):042:0&gt; get 'wp_test','1','id'<br>
COLUMN CELL<br>
id: timestamp=1325298164708, value=001<br>
1 row(s) in 0.0190 seconds<br>
-------------------------获取rowkey='1'的多列族的信息--------------------------------<br>
hbase(main):043:0&gt; get 'wp_test','1',{COLUMN =&gt; ['id','name:first']}<br>
COLUMN CELL<br>
id: timestamp=1325298164708, value=001<br>
name:first timestamp=1325298260659, value=hugh<br>
2 row(s) in 0.0190 seconds<br>
简单方式<br>
hbase(main):044:0&gt; get 'wp_test','1','id','name:first'<br>
COLUMN CELL<br>
id: timestamp=1325298164708, value=001<br>
例子：<br>
分区日常工作的第3 页<br>
id: timestamp=1325298164708, value=001<br>
name:first timestamp=1325298260659, value=hugh<br>
2 row(s) in 0.0180 seconds<br>
incr:<br>
获取incr列的value必须用get_counter获得，因为incr的列是二进制编码，get得到的不是十进制展示的结果，get_counter可以展示十进制的结果。<br>
功能：对table/row/column做递增/递减操作，可以解决事务问题。incr的列不能使用put来操作，put过的列也不能用incr，因为put和incr的存储方式不一致。<br>
hbase(main):096:0&gt; incr 'wp_test','1','id:num2'<br>
COUNTER VALUE = 1<br>
hbase(main):100:0&gt; get 'wp_test','1','id:num2'<br>
COLUMN CELL<br>
id:num2 timestamp=1325308653284, value=\x00\x00\x00\x00\x00\x00\x00\x01<br>
1 row(s) in 0.0180 seconds<br>
hbase(main):102:0&gt; get_counter 'wp_test','1','id:num2'<br>
COUNTER VALUE = 1<br>
例子：<br>
get_counter:<br>
功能：返回incr列的值。不能返回非incr的值，即时反悔了非incr的值，但是这个值没有任何参考意义<br>
hbase(main):102:0&gt; get_counter 'wp_test','1','id:num2'<br>
COUNTER VALUE = 1<br>
--------------------------非incr的列的异常值-------------------------------<br>
hbase(main):104:0&gt; get_counter 'wp_test','1','id'<br>
ERROR: java.lang.IllegalArgumentException: offset (0) + length (8) exceed the capacity of the array: 3<br>
Here is some help for this command:<br>
Return a counter cell value at specified table/row/column coordinates.<br>
A cell cell should be managed with atomic increment function oh HBase<br>
and the data should be binary encoded. Example:<br>
hbase&gt; get_counter 't1', 'r1', 'c1'<br>
hbase(main):105:0&gt; get_counter 'wp_test','1','name'<br>
COUNTER VALUE = 7527036049373487470<br>
例子：<br>
scan:<br>
官方例子：<br>
hbase&gt; scan '.META.'<br>
hbase&gt; scan '.META.', {COLUMNS =&gt; 'info:regioninfo'}<br>
hbase&gt; scan 't1', {COLUMNS =&gt; ['c1', 'c2'], LIMIT =&gt; 10, STARTROW =&gt; 'xyz'}<br>
hbase&gt; scan 't1', {FILTER =&gt; org.apache.hadoop.hbase.filter.ColumnPaginationFilter.new(1, 0)}<br>
hbase&gt; scan 't1', {COLUMNS =&gt; 'c1', TIMERANGE =&gt; [1303668804, 1303668904]}<br>
hbase&gt; scan 't1', {COLUMNS =&gt; ['c1', 'c2'], CACHE_BLOCKS =&gt; false}<br>
功能：扫描表。必须指定表名。可选参数：COLUMN、STARTROW、STOPROW、LIMIT、TIMESTAMP、TIMERANGE、MAXLENGTH、FILTER、CACHE_BLOCKS<br>
--------------------------wp_test全表数据-------------------------------<br>
hbase(main):138:0&gt; scan 'wp_test'<br>
ROW COLUMN+CELL<br>
1 column=id:, timestamp=1325300968316, value=500<br>
1 column=id:num, timestamp=1325301193197, value=10<br>
1 column=id:num1, timestamp=1325308303837, value=20<br>
1 column=id:num2, timestamp=1325308653284, value=\x00\x00\x00\x00\x00\x00\x00\x01<br>
1 column=name:, timestamp=1325298179649, value=hugh.wangp<br>
1 column=name:first, timestamp=1325298260659, value=hugh<br>
1 column=name:last, timestamp=1325298276137, value=wangp<br>
2 column=id:, timestamp=1325309756816, value=502<br>
2 column=name:first, timestamp=1325309767489, value=hugh<br>
2 column=name:last, timestamp=1325309777355, value=wang<br>
2 row(s) in 0.0310 seconds<br>
--------------------------wp_test只取一个rowkey的结果-------------------------------<br>
hbase(main):140:0&gt; scan 'wp_test',{LIMIT=&gt;1}<br>
ROW COLUMN+CELL<br>
1 column=id:, timestamp=1325300968316, value=500<br>
1 column=id:num, timestamp=1325301193197, value=10<br>
1 column=id:num1, timestamp=1325308303837, value=20<br>
1 column=id:num2, timestamp=1325308653284, value=\x00\x00\x00\x00\x00\x00\x00\x01<br>
1 column=name:, timestamp=1325298179649, value=hugh.wangp<br>
1 column=name:first, timestamp=1325298260659, value=hugh<br>
1 column=name:last, timestamp=1325298276137, value=wangp<br>
1 row(s) in 0.0280 seconds<br>
--------------------------wp_test从rowkey='1'开始取所有name的数据-------------------------------<br>
hbase(main):142:0&gt; scan 'wp_test',{COLUMN=&gt;'name',STARTROW=&gt;'1'}<br>
ROW COLUMN+CELL<br>
1 column=name:, timestamp=1325298179649, value=hugh.wangp<br>
1 column=name:first, timestamp=1325298260659, value=hugh<br>
1 column=name:last, timestamp=1325298276137, value=wangp<br>
2 column=name:first, timestamp=1325309767489, value=hugh<br>
2 column=name:last, timestamp=1325309777355, value=wang<br>
2 row(s) in 0.0260 seconds<br>
-----------------------wp_test从rowkey='1'开始取1个row所有name的数据----------------------------<br>
ROW COLUMN+CELL<br>
1 column=name:, timestamp=1325298179649, value=hugh.wangp<br>
1 column=name:first, timestamp=1325298260659, value=hugh<br>
1 column=name:last, timestamp=1325298276137, value=wangp<br>
1 row(s) in 0.0220 seconds<br>
----------------wp_test截止到rowkey='2'(不包含rowkey='2')取所有name的数据---------------------<br>
hbase(main):144:0&gt;scan 'wp_test',{COLUMN=&gt;'name',STOPROW=&gt;'2'}<br>
ROW COLUMN+CELL<br>
1 column=name:, timestamp=1325298179649, value=hugh.wangp<br>
例子：涉及参数：COLUMN、STARTROW、STOPROW、LIMIT<br>
分区日常工作的第4 页<br>
1 column=name:, timestamp=1325298179649, value=hugh.wangp<br>
1 column=name:first, timestamp=1325298260659, value=hugh<br>
1 column=name:last, timestamp=1325298276137, value=wangp<br>
1 row(s) in 0.0230 seconds<br>
count:<br>
官方例子：<br>
hbase&gt; count 't1'<br>
hbase&gt; count 't1', INTERVAL =&gt; 100000<br>
hbase&gt; count 't1', CACHE =&gt; 1000<br>
hbase&gt; count 't1', INTERVAL =&gt; 10, CACHE =&gt; 1000<br>
功能：返回HBASE表中的rowkey的数量。<br>
hbase(main):150:0&gt; count 'wp_test'<br>
2 row(s) in 0.0330 seconds<br>
INTERVAL和CACHE参数还没测试，等有大数据量后测试补充这部分信息。<br>
例子：<br>
delete:<br>
官方例子：<br>
hbase&gt; delete 't1', 'r1', 'c1', ts1<br>
功能：删除一张table特定row的特定column，可指定tiemstamp<br>
----------------删除表wp_test中rowkey='1'的列为id_num1的数据---------------------<br>
hbase(main):156:0&gt; scan 'wp_test'<br>
ROW COLUMN+CELL<br>
1 column=id:, timestamp=1325300968316, value=500<br>
1 column=id:num, timestamp=1325301193197, value=10<br>
1 column=id:num1, timestamp=1325308303837, value=20<br>
1 column=id:num2, timestamp=1325308653284, value=\x00\x00\x00\x00\x00\x00\x00\x01<br>
1 column=name:, timestamp=1325298179649, value=hugh.wangp<br>
1 column=name:first, timestamp=1325298260659, value=hugh<br>
1 column=name:last, timestamp=1325298276137, value=wangp<br>
2 column=id:, timestamp=1325309756816, value=502<br>
2 column=name:first, timestamp=1325309767489, value=hugh<br>
2 column=name:last, timestamp=1325309777355, value=wang<br>
2 row(s) in 0.0340 seconds<br>
hbase(main):157:0&gt; delete 'wp_test','1','id:num1'<br>
0 row(s) in 0.0180 seconds<br>
hbase(main):158:0&gt; scan 'wp_test'<br>
ROW COLUMN+CELL<br>
1 column=id:, timestamp=1325300968316, value=500<br>
1 column=id:num, timestamp=1325301193197, value=10<br>
1 column=id:num2, timestamp=1325308653284, value=\x00\x00\x00\x00\x00\x00\x00\x01<br>
1 column=name:, timestamp=1325298179649, value=hugh.wangp<br>
1 column=name:first, timestamp=1325298260659, value=hugh<br>
1 column=name:last, timestamp=1325298276137, value=wangp<br>
2 column=id:, timestamp=1325309756816, value=502<br>
2 column=name:first, timestamp=1325309767489, value=hugh<br>
2 column=name:last, timestamp=1325309777355, value=wang<br>
2 row(s) in 0.0290 seconds<br>
例子：<br>
deleteall:<br>
官方例子：<br>
hbase&gt; deleteall 't1', 'r1'<br>
hbase&gt; deleteall 't1', 'r1', 'c1'<br>
hbase&gt; deleteall 't1', 'r1', 'c1', ts1<br>
功能：删除HBASE表的rowkey的所有数据、rowkey的列的所有数据、rowkey的列的一个timestamp的所有数据<br>
----------------删除表wp_test中rowkey='1'的所有数据---------------------<br>
hbase(main):162:0&gt; scan 'wp_test'<br>
ROW COLUMN+CELL<br>
1 column=id:, timestamp=1325300968316, value=500<br>
1 column=id:num, timestamp=1325301193197, value=10<br>
1 column=id:num2, timestamp=1325308653284, value=\x00\x00\x00\x00\x00\x00\x00\x01<br>
1 column=name:, timestamp=1325298179649, value=hugh.wangp<br>
1 column=name:first, timestamp=1325298260659, value=hugh<br>
1 column=name:last, timestamp=1325298276137, value=wangp<br>
2 column=id:, timestamp=1325309756816, value=502<br>
2 column=name:first, timestamp=1325309767489, value=hugh<br>
2 column=name:last, timestamp=1325309777355, value=wang<br>
2 row(s) in 0.0310 seconds<br>
hbase(main):168:0&gt; deleteall 'wp_test','2'<br>
0 row(s) in 0.0150 seconds<br>
hbase(main):169:0&gt; scan 'wp_test'<br>
ROW COLUMN+CELL<br>
1 column=id:, timestamp=1325300968316, value=500<br>
1 column=id:num, timestamp=1325301193197, value=10<br>
1 column=id:num2, timestamp=1325308653284, value=\x00\x00\x00\x00\x00\x00\x00\x01<br>
1 column=name:, timestamp=1325298179649, value=hugh.wangp<br>
1 column=name:first, timestamp=1325298260659, value=hugh<br>
1 column=name:last, timestamp=1325298276137, value=wangp<br>
1 row(s) in 0.0250 seconds<br>
例子：<br>
truncate:<br>
功能：是disable+drop+create的组合，先disable表、然后drop表、最后recreate表(实现的功能同RAC的trucnate table)<br>
hbase(main):171:0&gt; scan 'wp_test'<br>
ROW COLUMN+CELL<br>
1 column=id:, timestamp=1325300968316, value=500<br>
1 column=id:num, timestamp=1325301193197, value=10<br>
例子：<br>
分区日常工作的第5 页<br>
1 column=id:num, timestamp=1325301193197, value=10<br>
1 column=id:num2, timestamp=1325308653284, value=\x00\x00\x00\x00\x00\x00\x00\x01<br>
1 column=name:, timestamp=1325298179649, value=hugh.wangp<br>
1 column=name:first, timestamp=1325298260659, value=hugh<br>
1 column=name:last, timestamp=1325298276137, value=wangp<br>
1 row(s) in 0.0280 seconds<br>
hbase(main):172:0&gt; truncate 'wp_test'<br>
Truncating 'wp_test' table (it may take a while):<br>
-Disabling table...<br>
-Dropping table...<br>
-Creating table...<br>
0 row(s) in 4.3920 seconds<br>
hbase(main):173:0&gt; scan 'wp_test'<br>
ROW COLUMN+CELL<br>
0 row(s) in 1.0330 seconds<br>
分区日常工作的第6 页<br>            </div>
                </div>