---
layout:     post
title:      HBase shell详细操作指南
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p> </p>

<p>转载：原文链接 <a href="http://blog.itpub.net/15498/viewspace-2134477/" rel="nofollow">http://blog.itpub.net/15498/viewspace-2134477/</a></p>

<p>进入hbase Shell<br>
[root@test31 ~]# hbase shell<br>
HBase Shell; enter 'help' for list of supported commands.<br>
Type "exit" to leave the HBase Shell<br>
Version 0.92.1, r1298924, Fri Mar  9 16:58:34 UTC 2012<br><br><br><strong>1.查看服务器状态</strong></p>

<pre class="has">
<code>hbase(main):001:0&gt; status
1 servers, 0 dead, 3.0000 average load</code></pre>

<p><br><strong>2.查看HBase版本</strong></p>

<pre class="has">
<code>hbase(main):002:0&gt; version
0.92.1, r1298924, Fri Mar  9 16:58:34 UTC 2012</code></pre>

<p><br><strong>3.DDL操作</strong><br>
Data Definition Language 数据库定义语言<br>
如 create 之类</p>

<pre class="has">
<code>1).create命令
创建一个具有三个列族"member_id"和"address"即"info"的表"member"，其中表名、行和列都要用单引号括起来，并以逗号隔开。
hbase(main):003:0&gt; create 'member','member_id','address','info'
0 row(s) in 1.1770 seconds

2).list命令
查看当前HBase中具有哪些表
hbase(main):004:0&gt; list
TABLE
member
test
2 row(s) in 0.0170 seconds

3).describe 命令
查看表的描述信息
hbase(main):006:0&gt; describe 'member'
DESCRIPTION                                                                                 ENABLED
 {NAME =&gt; 'member', FAMILIES =&gt; [{NAME =&gt; 'address', BLOOMFILTER =&gt; 'NONE', REPLICATION_SCO true
 PE =&gt; '0', VERSIONS =&gt; '3', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', TTL =&gt; '2147483647
 ', BLOCKSIZE =&gt; '65536', IN_MEMORY =&gt; 'false', BLOCKCACHE =&gt; 'true'}, {NAME =&gt; 'info', BLO
 OMFILTER =&gt; 'NONE', REPLICATION_SCOPE =&gt; '0', VERSIONS =&gt; '3', COMPRESSION =&gt; 'NONE', MIN_
 VERSIONS =&gt; '0', TTL =&gt; '2147483647', BLOCKSIZE =&gt; '65536', IN_MEMORY =&gt; 'false', BLOCKCAC
 HE =&gt; 'true'}, {NAME =&gt; 'member_id', BLOOMFILTER =&gt; 'NONE', REPLICATION_SCOPE =&gt; '0', VERS
 IONS =&gt; '3', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', TTL =&gt; '2147483647', BLOCKSIZE =&gt;
  '65536', IN_MEMORY =&gt; 'false', BLOCKCACHE =&gt; 'true'}]}
1 row(s) in 0.0320 seconds


4).删除一个列族：disable，alter，enable
修改表结构必须先disable
hbase(main):004:0&gt; disable member
NameError: undefined local variable or method `member' for #

hbase(main):005:0&gt; disable 'member'
0 row(s) in 2.1560 seconds

删除member表的info列
hbase(main):006:0&gt; alter 'member', 'delete' =&gt; 'info'
Updating all regions with the new schema...
1/1 regions updated.
Done.
0 row(s) in 1.3370 seconds

记得enable表:
hbase(main):009:0&gt; enable 'member'
0 row(s) in 2.2330 seconds


5).删除一个表test（disable，drop）
hbase(main):025:0&gt; disable 'test'
0 row(s) in 2.2100 seconds


hbase(main):026:0&gt; drop 'test'
0 row(s) in 1.2350 seconds

6).查询表是否存在

hbase(main):033:0&gt; exists 'test'
Table test does not exist
0 row(s) in 0.1820 seconds


7).查看表是否enable
hbase(main):032:0&gt; is_enabled 'member'
true
0 row(s) in 0.0070 seconds
</code></pre>

<p><br><br><strong>4.DML操作</strong></p>

<p>Data Manipulation Language 数据操纵语言</p>

<pre class="has">
<code>1).插入数据
hbase(main):005:0&gt; put 'member','scutshuxue','info:age','24'
0 row(s) in 0.0790 seconds


hbase(main):005:0&gt; put 'member','duansf','info:age','37'
0 row(s) in 0.0790 seconds


hbase(main):001:0&gt; put 'member','scutshuxue','info:company','alibaba'
0 row(s) in 0.6520 seconds


hbase(main):002:0&gt; put 'member','xiaofeng','address:contry','china'
0 row(s) in 0.0090 seconds


hbase(main):007:0&gt; put 'member','xiaofeng','info:birthday','1987-4-17'
0 row(s) in 0.0120 seconds


注：插入语句后面一定不要有空格。


2).获取一条数据


hbase(main):012:0* get 'member','xiaofeng'
COLUMN                               CELL
 address:contry                      timestamp=1488307463293, value=china
 info:birthday                       timestamp=1488307533852, value=1987-4-17
2 row(s) in 0.0130 seconds


3).获取一条记录中某个列族的信息
hbase(main):013:0&gt; get 'member','xiaofeng','info'
COLUMN                               CELL
 info:birthday                       timestamp=1488307533852, value=1987-4-17
1 row(s) in 0.0100 seconds


4).获取一条记录的某个列族中的某个列的信息


hbase(main):020:0&gt; get 'member','xiaofeng','info:birthday'
COLUMN                               CELL
 info:birthday                       timestamp=1488307533852, value=1987-4-17
1 row(s) in 0.0090 seconds




5).更新一条记录
把duansf的年龄改为38岁
hbase(main):022:0* get 'member','duansf','info:age'
COLUMN                               CELL
 info:age                            timestamp=1488308172957, value=37
1 row(s) in 0.0090 seconds




put 'member','duansf','info:age','38'




6).删除member表中某个列
hbase(main):032:0* delete 'member','scutshuxue','info:age'
0 row(s) in 0.0080 seconds






7).member表全表扫描(类似于关系型数据库select *)
hbase(main):002:0&gt; scan 'member'
ROW                                  COLUMN+CELL
 scutshuxue                          column=info:age, timestamp=1488306890710, value=24
 scutshuxue                          column=info:company, timestamp=1488307003752, value=alibaba
 xiaofeng                            column=address:contry, timestamp=1488307463293, value=china
 xiaofeng                            column=info:birthday, timestamp=1488307533852, value=1987-4-17
2 row(s) in 0.9780 seconds


8).删除整行
hbase(main):036:0&gt; deleteall 'member','scutshuxue'
0 row(s) in 0.0090 seconds


9).查询member表中有多少行
hbase(main):041:0&gt; count 'member'
2 row(s) in 0.0240 seconds


10).清空整张表
hbase(main):045:0* truncate 'member'
Truncating 'member' table (it may take a while):
 - Disabling table...
 - Dropping table...
 - Creating table...
0 row(s) in 4.8640 seconds
</code></pre>

<p><br><br>
附图:<br>
HBase Shell命令解释<br><img alt="" class="has" height="609" src="http://img.blog.itpub.net/blog/attachment/201703/1/15498_1488337304CjR7.png?x-oss-process=style/bb" width="628"></p>            </div>
                </div>