---
layout:     post
title:      HBase笔记二：Hbase Shell详解
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div>﻿﻿</div>
<p><span style="font-family:'Courier New';background-color:inherit;"><span style="color:#4b4b4b;background-color:inherit;">HBase 为用户提供了一个非常方便的使用方式, 我们称之为“HBase Shell”。</span><br style="background-color:inherit;"><span style="color:#4b4b4b;background-color:inherit;">HBase Shell 提供了大多数的 HBase 命令, 通过 HBase Shell 用户可以方便地创建、删除及修改表, 还可以向表中添加数据、列出表中的相关信息等。</span><br style="background-color:inherit;"><span style="color:#4b4b4b;background-color:inherit;">在启动 HBase 之后,用户可以通过下面的命令进入 HBase Shell 之中,命令如下所示:</span></span></p>
<p><span style="color:rgb(75,75,75);line-height:16px;background-color:rgb(255,255,255);"><span style="font-family:'Courier New';background-color:inherit;"><br style="background-color:inherit;"></span></span></p>
<table border="1" cellspacing="0" cellpadding="2"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top">
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">[<a href="mailto:hadoop@hadoopmaster" rel="nofollow" style="background-color:inherit;">hadoop@hadoopmaster</a><span> </span>~]$ hbase shell<span> </span><br style="background-color:inherit;">
HBase Shell; enter 'help&lt;RETURN&gt;' for list of supported commands.<span> </span><br style="background-color:inherit;">
Type "exit&lt;RETURN&gt;" to leave the HBase Shell<span> </span><br style="background-color:inherit;">
Version 0.94.2, r1395367, Sun Oct 7 19:11:01 UTC 2012<span> </span><br style="background-color:inherit;"><br style="background-color:inherit;">
hbase(main):001:0&gt;</span></div>
</td>
</tr></tbody></table><p><span style="color:rgb(75,75,75);line-height:16px;background-color:rgb(255,255,255);"><span style="font-family:'Courier New';background-color:inherit;"><br style="background-color:inherit;"></span></span></p>
<p><span style="color:rgb(75,75,75);line-height:16px;background-color:rgb(255,255,255);"><span style="font-family:'Courier New';background-color:inherit;">具体的 HBase Shell 命令如下表 1.1-1 所示:</span></span></p>
<p><span style="font-family:'Courier New';background-color:inherit;"><img src="" alt="" style="display:inline-block;background-color:inherit;"></span></p>
<p><span style="font-family:'Courier New';background-color:inherit;">下面我们将以“一个学生成绩表”的例子来详细介绍常用的 HBase 命令及其使用方法。</span></p>
<p><span style="font-family:'Courier New';background-color:inherit;"><img src="" alt="" style="display:inline-block;background-color:inherit;"><br style="background-color:inherit;">
这里 grad 对于表来说是一个列,course 对于表来说是一个列族,这个列族由三个列组成 china、math 和 english,当然我们可以根据我们的需要在 course 中建立更多的列族,如computer,physics 等相应的列添加入 course 列族。(备注:列族下面的列也是可以没有名字的。)</span></p>
<span style="font-family:'Courier New';background-color:inherit;"></span>
<p><br style="background-color:inherit;"><span style="color:rgb(0,0,255);background-color:inherit;"><strong><span style="color:#000000;">1. create 命令（新建一张表）</span></strong></span></p>
<p><span style="color:rgb(0,0,255);background-color:inherit;"><span style="font-family:'Courier New';"></span></span><br style="background-color:inherit;">
创建一个具有两个列族“grad”和“course”的表“scores”。其中表名、行和列都要用单引号括起来,并以逗号隔开。<br style="background-color:inherit;"></p>
<table border="1" cellspacing="0" cellpadding="2"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top">
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';color:#e60013;background-color:inherit;"><strong style="background-color:inherit;">hbase(main):001:0&gt; create 'scores','name','grad','course'</strong></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">0 row(s) in 2.2190 seconds</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):002:0&gt; </span></div>
</td>
</tr></tbody></table><span style="font-family:'Courier New';background-color:inherit;"></span>
<p><br style="background-color:inherit;"><span style="color:rgb(0,0,255);background-color:inherit;"><strong><span style="color:#000000;">2. list 命令（显示数据库中的表）</span></strong></span></p>
<p><span style="color:rgb(0,0,255);background-color:inherit;"><span style="font-family:'Courier New';"></span></span><br style="background-color:inherit;">
查看当前 HBase 中具有哪些表。<br style="background-color:inherit;"></p>
<table border="1" cellspacing="0" cellpadding="2"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top">
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';color:#e60013;background-color:inherit;"><strong style="background-color:inherit;">hbase(main):002:0&gt; list</strong></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">TABLE</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"><strong style="background-color:inherit;"><span style="color:#e60013;background-color:inherit;">scores</span></strong></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">wordcount</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">wordcount1</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">3 row(s) in 0.1350 seconds</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):003:0&gt; </span></div>
</td>
</tr></tbody></table><span style="font-family:'Courier New';background-color:inherit;"></span>
<p><br style="background-color:inherit;"><span style="color:rgb(0,0,255);background-color:inherit;"><span style="color:#000000;"><strong>3. describe 命令（描述表结构）</strong></span></span></p>
<p><span style="color:rgb(0,0,255);background-color:inherit;"><span style="font-family:'Courier New';"></span></span><br style="background-color:inherit;">
查看表“scores”的构造。<br style="background-color:inherit;"></p>
<table border="1" cellspacing="0" cellpadding="2"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top">
<div style="min-width:2px;background-color:inherit;"><strong style="background-color:inherit;"><span style="font-family:'Courier New';color:#e60013;background-color:inherit;">hbase(main):004:0&gt; describe 'scores'</span></strong></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">DESCRIPTION ENABLED</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"> {NAME =&gt; 'scores', FAMILIES =&gt; [{NAME =&gt; 'course', ENCODE_ON_DISK =&gt; 'true', BLOOMFIL true</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"> TER =&gt; 'NONE', VERSIONS =&gt; '3', IN_MEMORY =&gt; 'false', KEEP_DELETED_CELLS =&gt; 'false',</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"> DATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; '2147483647', COMPRESSION =&gt; 'NONE', MIN_VERSIO</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"> NS =&gt; '0', BLOCKCACHE =&gt; 'true', BLOCKSIZE =&gt; '65536', REPLICATION_SCOPE =&gt; '0'}, {NA</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"> ME =&gt; 'grad', ENCODE_ON_DISK =&gt; 'true', BLOOMFILTER =&gt; 'NONE', VERSIONS =&gt; '3', IN_ME</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"> MORY =&gt; 'false', KEEP_DELETED_CELLS =&gt; 'false', DATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt;</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">  '2147483647', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', BLOCKCACHE =&gt; 'true', BLOC</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"> KSIZE =&gt; '65536', REPLICATION_SCOPE =&gt; '0'}, {NAME =&gt; 'name', ENCODE_ON_DISK =&gt; 'true</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"> ', BLOOMFILTER =&gt; 'NONE', VERSIONS =&gt; '3', IN_MEMORY =&gt; 'false', KEEP_DELETED_CELLS =</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"> &gt; 'false', DATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; '2147483647', COMPRESSION =&gt; 'NONE',</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">  MIN_VERSIONS =&gt; '0', BLOCKCACHE =&gt; 'true', BLOCKSIZE =&gt; '65536', REPLICATION_SCOPE =</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"> &gt; '0'}]}</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">1 row(s) in 0.0800 seconds</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):005:0&gt; </span></div>
</td>
</tr></tbody></table><span style="font-family:'Courier New';background-color:inherit;"></span>
<p><br style="background-color:inherit;"><span style="color:rgb(0,0,255);background-color:inherit;"><strong><span style="color:#000000;">4. put 命令（插入一条数据）</span></strong></span></p>
<p><span style="color:rgb(0,0,255);background-color:inherit;"><span style="font-family:'Courier New';"></span></span><br style="background-color:inherit;">
使用 put 命令向表中插入数据,参数分别为表名、行名、列名和值,其中列名前需要列族最为前缀,时间戳由系统自动生成。<br style="background-color:inherit;">
格式: put 表名,行名,列名([列族:列名]),值<br style="background-color:inherit;">
例子：<br style="background-color:inherit;">
a. 加入一行数据,行名称为“xiapi”,列族“grad”的列名为”(空字符串)”,值位 1。<br style="background-color:inherit;"></p>
<table border="1" cellspacing="0" cellpadding="2"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top">
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):006:0&gt;<span> </span><strong style="background-color:inherit;"><span style="color:#e60013;background-color:inherit;">put 'scores',
 'xiapi', 'grad:', '1'</span></strong></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">0 row(s) in 0.0220 seconds</span></div>
<div style="min-width:2px;background-color:inherit;"></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):007:0&gt; </span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):007:0&gt;<span> </span><span style="color:#e60013;background-color:inherit;"><strong style="background-color:inherit;">put 'scores',
 'xiapi', 'grad:', '2' --修改操作</strong></span></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">0 row(s) in 0.0270 seconds</span></div>
<div style="min-width:2px;background-color:inherit;"></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):008:0&gt; </span></div>
</td>
</tr></tbody></table><p><span style="font-family:'Courier New';background-color:inherit;"><br style="background-color:inherit;"></span></p>
<p><span style="font-family:'Courier New';background-color:inherit;">b. 给“xiapi”这一行的数据的列族“course”添加一列。<br style="background-color:inherit;"></span></p>
<table border="1" cellspacing="0" cellpadding="2"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top">
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):008:0&gt;<span> </span><span style="color:#e60013;background-color:inherit;"><strong style="background-color:inherit;">put 'scores',
 'xiapi', 'course:china', '97'</strong></span></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">0 row(s) in 0.0380 seconds</span></div>
<div style="min-width:2px;background-color:inherit;"></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):009:0&gt;<span> </span><span style="color:#e60013;background-color:inherit;"><strong style="background-color:inherit;">put 'scores',
 'xiapi', 'course:math', '128'</strong></span></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">0 row(s) in 0.0090 seconds</span></div>
<div style="min-width:2px;background-color:inherit;"></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):010:0&gt;<span> </span><span style="color:#e60013;background-color:inherit;"><strong style="background-color:inherit;">put 'scores',
 'xiapi', 'course:english', '85'</strong></span></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">0 row(s) in 0.0210 seconds</span></div>
<div style="min-width:2px;background-color:inherit;"></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):011:0&gt; </span></div>
</td>
</tr></tbody></table><span style="font-family:'Courier New';background-color:inherit;"></span>
<p><br style="background-color:inherit;"><span style="color:rgb(0,0,255);background-color:inherit;"><strong><span style="color:#000000;">5. get 命令（得到一条数据）</span></strong></span></p>
<p><span style="color:rgb(0,0,255);background-color:inherit;"><span style="font-family:'Courier New';"></span></span><br style="background-color:inherit;">
a.查看表“scores”中的行“xiapi”的相关数据。<br style="background-color:inherit;"></p>
<table border="1" cellspacing="0" cellpadding="2"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top">
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):011:0&gt;<span> </span><span style="color:#e60013;background-color:inherit;"><strong style="background-color:inherit;">get 'scores',
 'xiapi'</strong></span></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">COLUMN CELL</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"> course:china timestamp=1410191209388, value=97</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"> course:english timestamp=1410191240468, value=85</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"> course:math timestamp=1410191232069, value=128</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"> grad: timestamp=1410191119678, value=2</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">4 row(s) in 0.0710 seconds</span></div>
<div style="min-width:2px;background-color:inherit;"></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):012:0&gt; </span></div>
</td>
</tr></tbody></table><p><span style="font-family:'Courier New';background-color:inherit;">b.查看表“scores”中行“xiapi”列“course :math”的值。<br style="background-color:inherit;"></span></p>
<table border="1" cellspacing="0" cellpadding="2"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top">
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):013:0&gt;<span> </span><strong style="background-color:inherit;"><span style="color:#e60013;background-color:inherit;">get 'scores',
 'xiapi', 'course:math'</span></strong></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">COLUMN CELL</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"> course:math timestamp=1410191232069, value=128</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">1 row(s) in 0.0160 seconds</span></div>
<div style="min-width:2px;background-color:inherit;"></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):014:0&gt; </span></div>
</td>
</tr></tbody></table><p><span style="font-family:'Courier New';background-color:inherit;">或者<br style="background-color:inherit;"></span></p>
<table border="1" cellspacing="0" cellpadding="2"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top">
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):014:0&gt;<span> </span><strong style="background-color:inherit;">get 'scores', 'xiapi', {COLUMN=&gt;'course:math'}</strong></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">COLUMN CELL</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"> course:math timestamp=1410191232069, value=128</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">1 row(s) in 0.0170 seconds</span></div>
<div style="min-width:2px;background-color:inherit;"></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):015:0&gt;<span> </span><strong style="background-color:inherit;"><span style="color:#e60013;background-color:inherit;">get 'scores',
 'xiapi', {COLUMNS=&gt;'course:math'}</span></strong></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">COLUMN CELL</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"> course:math timestamp=1410191232069, value=128</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">1 row(s) in 0.0180 seconds</span></div>
<div style="min-width:2px;background-color:inherit;"></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):016:0&gt; </span></div>
</td>
</tr></tbody></table><p><span style="font-family:'Courier New';background-color:inherit;">备注:COLUMN 和 COLUMNS 是不同的,scan 操作中的 COLUMNS 指定的是表的列族, get操作中的 COLUMN 指定的是特定的列,COLUMNS 的值实质上为“列族:列修饰符”。<strong style="background-color:inherit;"><span style="color:rgb(255,0,0);background-color:inherit;">COLUMN
 和 COLUMNS 必须为大写。</span></strong><br style="background-color:inherit;"><br style="background-color:inherit;"><span style="color:rgb(0,0,255);background-color:inherit;"><strong><span style="color:#000000;">6. scan 命令（获取数据库表全部数据）</span></strong></span></span></p>
<p><span style="font-family:'Courier New';background-color:inherit;"><span style="color:rgb(0,0,255);background-color:inherit;"></span><br style="background-color:inherit;">
a. 查看表“scores”中的所有数据。<br style="background-color:inherit;"></span></p>
<table border="1" cellspacing="0" cellpadding="2"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top">
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):016:0&gt;<span> </span><strong style="background-color:inherit;"><span style="color:#e60013;background-color:inherit;">scan 'scores'</span></strong></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">ROW COLUMN+CELL</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"> songhn column=grad:math, timestamp=1410191032626, value=100</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"> xiapi column=course:china, timestamp=1410191209388, value=97</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"> xiapi column=course:english, timestamp=1410191240468, value=85</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"> xiapi column=course:math, timestamp=1410191232069, value=128</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"> xiapi column=grad:, timestamp=1410191119678, value=2</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">2 row(s) in 0.0650 seconds</span></div>
<div style="min-width:2px;background-color:inherit;"></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):017:0&gt; </span></div>
</td>
</tr></tbody></table><p><span style="font-family:'Courier New';background-color:inherit;">注意:<br style="background-color:inherit;">
scan 命令可以指定 startrow,stoprow 来 scan 多个 row。<br style="background-color:inherit;">
例如:<br style="background-color:inherit;">
scan 'user_test',{COLUMNS =&gt;'info:username',LIMIT =&gt;10, STARTROW =&gt; 'test', STOPROW=&gt;'test2'}<br style="background-color:inherit;">
b.查看表“scores”中列族“course”的所有数据。</span></p>
<table border="1" cellspacing="0" cellpadding="2"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top">
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):020:0&gt;</span><strong style="background-color:inherit;"><span style="font-family:'Courier New';color:#e60013;background-color:inherit;"><span> </span>scan
 'scores', {COLUMN =&gt; 'grad'} </span></strong></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">ROW COLUMN+CELL</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"> songhn column=grad:math, timestamp=1410191032626, value=100</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"> xiapi column=grad:, timestamp=1410191119678, value=2</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">2 row(s) in 0.0540 seconds</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):021:0&gt;</span><strong style="background-color:inherit;"><span style="font-family:'Courier New';color:#e60013;background-color:inherit;"><span> </span>scan
 'scores', {COLUMN=&gt;'course:math'} </span></strong></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">ROW COLUMN+CELL</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"> xiapi column=course:math, timestamp=1410191232069, value=128</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">1 row(s) in 0.0320 seconds</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):022:0&gt;</span><strong style="background-color:inherit;"><span style="font-family:'Courier New';color:#e60013;background-color:inherit;"><span> </span>scan
 'scores', {COLUMNS =&gt; 'course'} </span></strong></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">ROW COLUMN+CELL</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"> xiapi column=course:china, timestamp=1410191209388, value=97</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"> xiapi column=course:english, timestamp=1410191240468, value=85</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"> xiapi column=course:math, timestamp=1410191232069, value=128</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">1 row(s) in 0.0310 seconds</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):023:0&gt;</span></div>
</td>
</tr></tbody></table><p><span style="font-family:'Courier New';background-color:inherit;"><br style="background-color:inherit;"><span style="color:rgb(0,0,255);background-color:inherit;"><span style="color:#000000;"><strong>7. count 命令（统计数据）</strong></span></span></span></p>
<table border="1" cellspacing="0" cellpadding="2"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top">
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):024:0&gt;</span><strong style="background-color:inherit;"><span style="font-family:'Courier New';color:#e60013;background-color:inherit;"><span> </span>count
 'scores' </span></strong></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">2 row(s) in 0.0270 seconds</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):025:0&gt; </span></div>
</td>
</tr></tbody></table><p><span style="font-family:'Courier New';background-color:inherit;"><br style="background-color:inherit;"><span style="color:rgb(0,0,255);background-color:inherit;"><span style="color:#000000;"><strong>8. exists 命令（判别数据表是否存在）</strong></span></span></span></p>
<table border="1" cellspacing="0" cellpadding="2"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top">
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):025:0&gt;</span><strong style="background-color:inherit;"><span style="font-family:'Courier New';color:#e60013;background-color:inherit;"><span> </span>exists
 'scores' </span></strong></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">Table scores does exist</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">0 row(s) in 0.0810 seconds</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):026:0&gt; </span></div>
</td>
</tr></tbody></table><p><span style="font-family:'Courier New';background-color:inherit;"><br style="background-color:inherit;"><br style="background-color:inherit;"><span style="color:rgb(0,0,255);background-color:inherit;"><strong><span style="color:#000000;">9. delete 命令（删除表中的某一列）</span></strong></span><br style="background-color:inherit;">
删除表“scores”中行为“xiaoxue”, 列族“course”中的“math”。</span></p>
<table border="1" cellspacing="0" cellpadding="2"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top">
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):029:0&gt;</span><strong style="background-color:inherit;"><span style="font-family:'Courier New';color:#e60013;background-color:inherit;"><span> </span>delete
 'scores', 'xiapi', 'course:math' </span></strong></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">0 row(s) in 0.0170 seconds</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):030:0&gt; </span></div>
</td>
</tr></tbody></table><p><span style="font-family:'Courier New';background-color:inherit;"><br style="background-color:inherit;"><span style="color:rgb(0,0,255);background-color:inherit;"><span style="color:#000000;"><strong>10. truncate 命令（截断一张表）</strong></span></span><br style="background-color:inherit;"></span></p>
<table border="1" cellspacing="0" cellpadding="2"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top">
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):030:0&gt;</span><strong style="background-color:inherit;"><span style="font-family:'Courier New';color:#e60013;background-color:inherit;"><span> </span>truncate
 'scores' </span></strong></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">Truncating 'scores' table (it may take a while):</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"> - Disabling table...</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"> - Dropping table...</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"> - Creating table...</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">0 row(s) in 4.4990 seconds</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):031:0&gt;</span></div>
</td>
</tr></tbody></table><p><span style="font-family:'Courier New';background-color:inherit;"><br style="background-color:inherit;"><span style="color:rgb(0,0,255);background-color:inherit;"><span style="color:#000000;"><strong>11. disbale、drop 命令（删除表-先将表不可用，再删除）</strong></span></span><br style="background-color:inherit;">
通过“disable”和“drop”命令删除“scores”表。</span></p>
<table border="1" cellspacing="0" cellpadding="2"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top">
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):002:0&gt;</span><strong style="background-color:inherit;"><span style="font-family:'Courier New';color:#e60013;background-color:inherit;"><span> </span>disable
 'scores' </span></strong></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">0 row(s) in 3.3360 seconds</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):003:0&gt;</span><strong style="background-color:inherit;"><span style="font-family:'Courier New';color:#e60013;background-color:inherit;"><span> </span>drop
 'scores'</span></strong></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">0 row(s) in 1.1390 seconds</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):004:0&gt;</span><strong style="background-color:inherit;"><span style="font-family:'Courier New';color:#e60013;background-color:inherit;"><span> </span>exists
 'scores'</span></strong></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">Table scores does not exist</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">0 row(s) in 0.0760 seconds</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):005:0&gt; </span></div>
</td>
</tr></tbody></table><p><span style="font-family:'Courier New';background-color:inherit;"><br style="background-color:inherit;"><span style="color:rgb(0,0,255);background-color:inherit;"><span style="color:#000000;"><strong>12.  status命令（查看数据库当前运行状态）</strong></span></span></span></p>
<table border="1" cellspacing="0" cellpadding="2"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top">
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">ase(main):005:0&gt;</span><strong style="background-color:inherit;"><span style="font-family:'Courier New';color:#e60013;background-color:inherit;"><span> </span>status</span></strong></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">2 servers, 0 dead, 2.0000 average load</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):006:0&gt; </span></div>
</td>
</tr></tbody></table><p><span style="font-family:'Courier New';background-color:inherit;"><br style="background-color:inherit;"><span style="color:rgb(0,0,255);background-color:inherit;"><span style="color:#000000;"><strong>13.  version命令（查看数据库的版本信息）</strong></span></span></span></p>
<table border="1" cellspacing="0" cellpadding="2"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top">
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):006:0&gt;</span><strong style="background-color:inherit;"><span style="font-family:'Courier New';color:#e60013;background-color:inherit;"><span> </span>version</span></strong></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">0.94.2, r1395367, Sun Oct 7 19:11:01 UTC 2012</span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;"></span></div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'Courier New';background-color:inherit;">hbase(main):007:0&gt; </span></div>
</td>
</tr></tbody></table><p><span style="font-family:'Courier New';background-color:inherit;"><br style="background-color:inherit;"><strong><span style="color:#ff0000;">注意：在 shell 中,常量不需要用引号引起来,但二进制的值需要双引号引起来,而其他值则用单引号引起来。HBase Shell 的常量可以通过在 shell 中输入“Object.constants”。</span></strong></span></p>
            </div>
                </div>