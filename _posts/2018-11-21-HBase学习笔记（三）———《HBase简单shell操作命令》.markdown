---
layout:     post
title:      HBase学习笔记（三）———《HBase简单shell操作命令》
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/lcj369387335/article/details/48268773				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="text-align:center;"><span style="font-family:'Times New Roman';"><span style="line-height:26px;"><strong><span style="font-size:48px;">HBase简单shell操作命令</span><br></strong></span></span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"><span style="line-height:26px;"><span style="color:rgb(255,0,0);"><strong>1、Hbase Shell 练习</strong></span></span><br></span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"><span style="line-height:26px;"></span></span></p>
<pre><code class="language-plain">$ /bin/hbase shell 
HBase Shell; enter 'help&lt;RETURN&gt;' for list of supported commands.
Type "exit&lt;RETURN&gt;" to leave the HBase Shell
Version 0.94.27, rfb434617716493eac82b55180b0bbd653beb90bf, Thu Mar 19 06:17:55 UTC 2015
hbase(main):001:0&gt;</code></pre>
<p></p>
<pre></pre>
<pre></pre>
<p></p>
<p><span style="line-height:26px;"><span style="font-family:'Times New Roman';font-size:14px;">输入<strong><span style="color:rgb(255,0,0);">help</span></strong> 然后 按回车键，可以看到一列shell命令。这里的帮助很详细，要注意的是表名，行和列需要加引号。<br></span></span></p>
<pre><code class="language-plain">hbase(main):001:0&gt; help
HBase Shell, version 0.94.27, rfb434617716493eac82b55180b0bbd653beb90bf, Thu Mar 19 06:17:55 UTC 2015
Type 'help "COMMAND"', (e.g. 'help "get"' -- the quotes are necessary) for help on a specific command.
Commands are grouped. Type 'help "COMMAND_GROUP"', (e.g. 'help "general"') for help on a command group.

COMMAND GROUPS:
  Group name: general
  Commands: status, version, whoami

  Group name: ddl
  Commands: alter, alter_async, alter_status, create, describe, disable, disable_all, drop, drop_all, enable, enable_all, exists, is_disabled, is_enabled, list, show_filters

  Group name: dml
  Commands: count, delete, deleteall, get, get_counter, incr, put, scan, truncate, truncate_preserve

  Group name: tools
  Commands: assign, balance_switch, balancer, close_region, compact, flush, hlog_roll, major_compact, move, split, unassign, zk_dump

  Group name: replication
  Commands: add_peer, disable_peer, enable_peer, list_peers, list_replicated_tables, remove_peer, start_replication, stop_replication

  Group name: snapshot
  Commands: clone_snapshot, delete_snapshot, list_snapshots, restore_snapshot, snapshot

  Group name: security
  Commands: grant, revoke, user_permission

SHELL USAGE:
Quote all names in HBase Shell such as table and column names.  Commas delimit
command parameters.  Type &lt;RETURN&gt; after entering a command to run it.
Dictionaries of configuration used in the creation and alteration of tables are
Ruby Hashes. They look like this:

  {'key1' =&gt; 'value1', 'key2' =&gt; 'value2', ...}

and are opened and closed with curley-braces.  Key/values are delimited by the
'=&gt;' character combination.  Usually keys are predefined constants such as
NAME, VERSIONS, COMPRESSION, etc.  Constants do not need to be quoted.  Type
'Object.constants' to see a (messy) list of all constants in the environment.

If you are using binary keys or values and need to enter them in the shell, use
double-quote'd hexadecimal representation. For example:

  hbase&gt; get 't1', "key\x03\x3f\xcd"
  hbase&gt; get 't1', "key\003\023\011"
  hbase&gt; put 't1', "test\xef\xff", 'f1:', "\x01\x33\x40"

The HBase shell is the (J)Ruby IRB with the above HBase-specific commands added.
For more on the HBase Shell, see http://hbase.apache.org/docs/current/book.html
hbase(main):002:0&gt;</code></pre>
<p><span style="font-family:'Times New Roman';font-size:14px;">以网上的一个学生成绩表的例子来演示hbase的用法。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"><img src="https://img-blog.csdn.net/20150419182823686" width="350" height="150" alt=""><br>
这里grad对于表来说是一个只有它自己的列族,course对于表来说是一个有两个列的列族,这个列族由两个列组成math和art,当然我们可以根据我们的需要在course中建立更多的列族,如computer,physics等相应的列添加入course列族。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"><span style="color:rgb(255,0,0);"><strong>create</strong></span>命令用法：</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">create 'table_name', 'column_family_name1', 'column_family_name2', ....</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">其中，table_name指表名，column_family_name1为第一个列族名，column_family_name2为第二个列族名，可以加多个列族名</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">用<span style="color:rgb(255,0,0);"><strong>create</strong></span>创建一个名为 scores的表，这个表有两个列族(column family),分别为grad和courese，操作如下：</span></p>
<p></p>
<pre><code class="language-plain">hbase(main):002:0&gt; create 'scores', 'grade', 'course'
0 row(s) in 4.1860 seconds

=&gt; Hbase::Table - scores</code></pre>
<pre></pre>
<p><span style="font-family:'Times New Roman';font-size:14px;"><span style="color:rgb(255,0,0);"><strong>list</strong></span>命令用法：</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">1、list</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">列出所有的表</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">2、list 'table_name'</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">列出表名为table_name的表</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">用<strong><span style="color:rgb(255,0,0);">list</span></strong>列出所有的表来检查创建情况，操作如下：</span></p>
<p></p>
<p></p>
<pre><code class="language-plain">hbase(main):003:0&gt; list
TABLE
scores
1 row(s) in 0.0090 seconds

=&gt; ["scores"]</code></pre>
<pre></pre>
<p></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"><span style="color:rgb(255,0,0);"><strong>put</strong></span>命令比较简单，只有一种用法：</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">put 'table_name', 'row_value', 'column_name', 'value', timestamp</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">其中，table_name指表名，row_value指行键值，column_name指列名（具体到某个列族下的某个列），value指单元格的值，timestamp指时间戳，一般省略</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">按设计的表结构用<strong><span style="color:rgb(255,0,0);">put</span></strong>插入值，操作如下：</span></p>
<pre><code class="language-plain">hbase(main):004:0&gt; put 'scores', 'Tom', 'grade', '5'
0 row(s) in 0.0770 seconds

hbase(main):005:0&gt; put 'scores', 'Tom', 'course:math', '97'
0 row(s) in 0.0040 seconds

hbase(main):006:0&gt; put 'scores', 'Tom', 'course:art', '87'
0 row(s) in 0.0040 seconds

hbase(main):007:0&gt; put 'scores', 'Jim', 'grade', '4'
0 row(s) in 0.0050 seconds

hbase(main):008:0&gt; put 'scores', 'Jim', 'course:math', '89'
0 row(s) in 0.0100 seconds

hbase(main):009:0&gt; put 'scores', 'Jim', 'course:art', '80'
0 row(s) in 0.0090 seconds</code></pre><span style="font-family:'Times New Roman';font-size:14px;"><br></span>
<p><span style="font-family:'Times New Roman';font-size:14px;">scan命令用法：</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">1、scan <span style="line-height:26px;">'table_name'</span></span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"><span style="line-height:26px;">扫描表名为'table_name'的表</span></span></p>
<span style="font-family:'Times New Roman';font-size:14px;">用<span style="color:rgb(255,0,0);">scan</span>扫描表名为scores的表，操作如下：</span>
<p><span style="line-height:26px;"></span></p>
<pre><code class="language-plain">hbase(main):010:0&gt; scan 'scores'
ROW                   COLUMN+CELL
 Jim                  column=course:art, timestamp=1429439736957, value=80
 Jim                  column=course:math, timestamp=1429439727558, value=89
 Jim                  column=grade:, timestamp=1429439693821, value=4
 Tom                  column=course:art, timestamp=1429439680468, value=87
 Tom                  column=course:math, timestamp=1429439671589, value=97
 Tom                  column=grade:, timestamp=1429439628765, value=5
2 row(s) in 0.0190 seconds</code></pre>
<p><span style="font-family:'Times New Roman';font-size:14px;">2、scan <span style="line-height:26px;">'table_name'，{COLUMNS =&gt; 'column_name'，LIMIT =&gt; 10, STARTROW =&gt; 'row_value'}</span></span></p>
<p><span style="font-family:'Times New Roman';"><span style="line-height:26px;"><span style="font-size:14px;">扫描表名为'table_name'的表,列名为column_name的，行键值以row_value开始的10条数据<br></span></span></span></p>
<p><span style="font-family:'Times New Roman';"><span style="line-height:26px;"><span style="font-size:14px;">用<span style="color:rgb(255,0,0);">scan</span>扫描表名为scores的表，列名为course:math的数据，操作如下：</span></span></span></p>
<p><span style="line-height:26px;"></span></p>
<pre><code class="language-plain">hbase(main):011:0&gt; scan 'scores', {COLUMNS =&gt; 'course:math'}
ROW                     COLUMN+CELL
 Jim                    column=course:math, timestamp=1429439727558, value=89
 Tom                    column=course:math, timestamp=1429439671589, value=97
2 row(s) in 0.0070 seconds</code></pre>
<p></p>
<p>3、scan <span style="line-height:26px;">'table_name'，{COLUMNS =&gt; ['column_name1','column_name2']，LIMIT =&gt; 10, STARTROW =&gt; 'row_value'}</span><span style="line-height:26px;"></span></p>
<p><span style="line-height:26px;">扫描表名为</span><span style="line-height:26px;">'table_name'</span><span style="line-height:26px;">的表,列名为column_name1和column_name2的，行键值以row_value开始的10条数据</span><br></p>
<span style="font-size:12px;">用<span style="color:rgb(255,0,0);">scan</span>扫描表名为scores的表，操作如下：</span>
<p><span style="font-family:'Times New Roman';"><span style="line-height:26px;"></span></span></p>
<pre><code class="language-plain">hbase(main):008:0&gt; scan 'scores', {COLUMNS =&gt; ['course:math','course:art']}
ROW                     COLUMN+CELL
 Jim                    column=course:art, timestamp=1429439736957, value=80
 Jim                    column=course:math, timestamp=1429439727558, value=89
 Tom                    column=course:art, timestamp=1429439680468, value=87
 Tom                    column=course:math, timestamp=1429439671589, value=97
2 row(s) in 0.0080 seconds</code></pre>
<p><span style="line-height:26px;"><span style="font-size:12px;">4、scan <span style="line-height:26px;">'table_name'，{COLUMNS =&gt; ['column_name1','column_name2']，LIMIT =&gt; 10, STARTROW =&gt; 'row_value'， VERSIONS =&gt; 'n'}</span><br></span></span></p>
<p><span style="line-height:26px;"><span style="font-size:12px;"><span style="line-height:26px;"><span style="font-size:12px;line-height:26px;">scan操作默认只扫描最新版本的数据，如果想扫描多版本的数据，</span><span style="font-size:12px;line-height:26px;">操作如下：</span></span></span></span></p>
<p><span style="line-height:26px;"><span style="font-size:12px;"><span style="line-height:26px;"><span style="font-size:12px;line-height:26px;"></span></span></span></span></p><pre><code class="language-html">hbase(main):009:0&gt; scan 'scores', {COLUMNS =&gt; 'course:math', VERSIONS =&gt; 2]}
ROW COLUMN+CELL
Jim column=course:math, timestamp=1429439736957, value=80
Jim column=course:math, timestamp=1429439727558, value=89
Tom column=course:math, timestamp=1429439680468, value=87
Tom column=course:math, timestamp=1429439671589, value=97
2 row(s) in 0.0080 seconds</code></pre><br><br><p></p>
<p><span style="line-height:26px;"><span style="font-size:14px;">用<span style="color:rgb(255,0,0);"><strong>get</strong></span>查询表名为scores，行键值为Jim的数据，操作如下：</span></span></p>
<p></p>
<p><span style="line-height:26px;"></span></p>
<pre><code class="language-plain">hbase(main):011:0&gt; get 'scores', 'Jim'
COLUMN                CELL
 course:art           timestamp=1429439736957, value=80
 course:math          timestamp=1429439727558, value=89
 grade:               timestamp=1429439693821, value=4
3 row(s) in 0.0160 seconds</code></pre>
<p><span style="font-family:'Times New Roman';font-size:14px;"><span style="color:rgb(255,0,0);"><strong>get</strong></span>命令用法：</span></p>
<p><span style="line-height:26px;"><span style="font-family:'Times New Roman';font-size:14px;">1、get 'table_name', 'row_value'</span></span></p>
<p><span style="line-height:26px;"></span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">查询表名为table_name，行键值为row_value的数据</span></p>
<span style="font-family:'Times New Roman';font-size:14px;">用<span style="color:rgb(255,0,0);"><strong>get</strong></span>查询表名为scores，行键值为Jim的数据，操作如下：<br></span>
<p><span style="line-height:26px;"><span style="font-family:'Times New Roman';font-size:14px;"></span></span></p>
<pre><code class="language-plain">hbase(main):011:0&gt; get 'scores', 'Jim'
COLUMN                CELL
 course:art           timestamp=1429439736957, value=80
 course:math          timestamp=1429439727558, value=89
 grade:               timestamp=1429439693821, value=4
3 row(s) in 0.0160 seconds
</code></pre><span style="font-family:'Times New Roman';font-size:14px;"><span style="line-height:26px;"></span></span>
<p><span style="font-family:'Times New Roman';font-size:14px;">2、get 'table_name', 'row_value'，{COLUMN =&gt; 'column_name'}</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"><span style="line-height:26px;">      get 'table_name', 'row_value'，'</span><span style="line-height:26px;">column_name'</span><br></span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">上述两种方法相同，查询表名为table_name，行键值为row_value，列名为column_name的数据</span></p>
<span style="font-family:'Times New Roman';font-size:14px;">用<span style="line-height:26px;color:rgb(255,0,0);"><strong>get</strong></span><span style="line-height:26px;">查询表名为scores，行键值为Jim，列名为course:art的数据，操作如下：</span><br></span>
<p></p>
<pre><code class="language-plain">hbase(main):012:0&gt; get 'scores', 'Jim', {COLUMN =&gt; 'course:art'}
COLUMN                CELL
 course:art           timestamp=1429439736957, value=80
1 row(s) in 0.0040 seconds</code></pre>
<p></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"><span style="line-height:26px;">3、get 'table_name', 'row_value', {COLUMN =&gt; ['</span><span style="line-height:26px;">column_name1', 'column_name2']}</span></span></p>
<p><span style="line-height:26px;"><span style="font-family:'Times New Roman';font-size:14px;">      get 'table_name', 'row_value',  ['column_name1', 'column_name2']<br></span></span></p>
<p><span style="line-height:26px;"><span style="font-family:'Times New Roman';font-size:14px;">      get 'table_name', 'row_value',  'column_name1', 'column_name2'</span></span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"><span style="line-height:26px;"></span></span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"><span style="line-height:26px;">上述三种方法相同，</span>查询表名为table_name，行键值为row_value，列名为column_name1和column_name2的数据</span></p>
<span style="font-family:'Times New Roman';font-size:14px;">用<span style="line-height:26px;color:rgb(255,0,0);"><strong>get</strong></span><span style="line-height:26px;">查询表名为scores，行键值为Jim，列名为course:art和course:math的数据，操作如下：</span><br></span>
<p></p>
<pre><code class="language-plain">hbase(main):013:0&gt; get 'scores', 'Jim', 'course:art','course:math'
COLUMN                CELL
 course:art           timestamp=1429439736957, value=80
1 row(s) in 0.0040 seconds</code></pre>
<p><span style="font-family:'Times New Roman';font-size:14px;"><br></span></p>
<p><span style="font-family:'Times New Roman';"><span style="font-size:14px;color:rgb(255,0,0);">delete</span><span style="font-size:14px;">命令用法：</span></span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">1、delete 'table_name', 'row_value'，{COLUMN =&gt; 'column_name'}</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">      delete 'table_name', 'row_value'，'column_name'</span></p>
<p><span style="line-height:26px;"><span style="font-family:'Times New Roman';font-size:14px;">用<span style="color:rgb(255,0,0);"><strong>delete</strong></span>删除表名为scores，行键值为Jim,列名为course:math的单元格，操作如下：</span></span></p>
<p><span style="line-height:26px;"><span style="font-family:'Times New Roman';font-size:14px;"></span></span></p>
<pre><code class="language-plain">hbase(main):009:0&gt; delete 'scores', 'Jim', 'course:math'
0 row(s) in 0.0340 seconds</code></pre>
<p></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">检查一下数据是否被删除，<span style="line-height:26px;">操作如下：</span></span></p>
<p></p>
<pre><code class="language-plain">hbase(main):010:0&gt; scan 'scores'
ROW                     COLUMN+CELL
 Jim                    column=course:art, timestamp=1429439736957, value=80
 Jim                    column=grade:, timestamp=1429439693821, value=4
 Tom                    column=course:art, timestamp=1429439680468, value=87
 Tom                    column=course:math, timestamp=1429439671589, value=97&lt;/span&gt;</code></pre>
<p><span style="font-family:'Times New Roman';font-size:14px;"><br></span></p>
<p></p>
<p></p>
<p><span style="line-height:26px;"></span></p>
<p></p>
<p>检查一下数据是否被删除，<span style="line-height:26px;">操作如下：</span></p>
<p></p>
<pre><code class="language-plain">hbase(main):010:0&gt; scan 'scores'
ROW                     COLUMN+CELL
 Jim                    column=course:art, timestamp=1429439736957, value=80
 Jim                    column=grade:, timestamp=1429439693821, value=4
 Tom                    column=course:art, timestamp=1429439680468, value=87
 Tom                    column=course:math, timestamp=1429439671589, value=97</code></pre><span style="color:rgb(255,0,0);">deleteall</span>命令用法：
<p><span style="font-family:'Times New Roman';font-size:14px;"><span style="line-height:26px;">deleteall 'table_name', 'row_value'</span></span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"><span style="line-height:26px;">删除表名为table_name，行键值为row_value的整行数据</span></span></p>
<p style="line-height:26px;"><span style="font-family:'Times New Roman';font-size:14px;">用<strong><span style="color:rgb(255,0,0);">deleteall</span></strong>删除表名为scores，行键值为Tom的整行数据，操作如下：</span></p>
<p style="line-height:26px;"></p>
<pre><code class="language-plain">hbase(main):018:0&gt; deleteall 'scores', 'Tom'
0 row(s) in 0.0040 seconds</code></pre>
<p style="line-height:26px;"></p>
<p style="line-height:26px;"><span style="font-family:'Times New Roman';font-size:14px;">检查一下数据是否被删除，操作如下：</span></p>
<p style="line-height:26px;"><span style="font-family:'Times New Roman';font-size:14px;"></span></p><pre><code class="language-html">hbase(main):019:0&gt; scan 'scores'
ROW                     COLUMN+CELL
 Jim                    column=course:art, timestamp=1429439736957, value=80
 Jim                    column=grade:, timestamp=1429439693821, value=4
1 row(s) in 0.0110 seconds</code></pre><br><pre></pre>
<p><span style="line-height:26px;"><span style="font-family:'Times New Roman';font-size:14px;"><span style="color:rgb(255,0,0);">disable</span>命令用法：</span></span></p>
<p><span style="line-height:26px;"><span style="font-family:'Times New Roman';font-size:14px;">disable 'table_name'</span></span></p>
<p><span style="line-height:26px;"><span style="font-family:'Times New Roman';font-size:14px;">禁用表名为'table_name'的表</span></span></p>
<p><span style="line-height:26px;"><span style="font-family:'Times New Roman';font-size:14px;">用<span style="color:rgb(255,0,0);">disable</span>先禁用表名为scores的表，操作如下：</span></span></p>
<p><span style="line-height:26px;"><span style="font-family:'Times New Roman';font-size:14px;"></span></span></p>
<pre><code class="language-plain">hbase(main):020:0&gt; disable 'scores'
0 row(s) in 1.2230 seconds</code></pre>
<p><span style="line-height:26px;"><span style="font-family:'Times New Roman';font-size:14px;">alter命令用法：</span></span></p>
<p><span style="line-height:26px;"><span style="font-family:'Times New Roman';font-size:14px;">1、alter 'table_name'，NAME =&gt; 'column_family_name'</span></span></p>
<p><span style="line-height:26px;"></span></p>
<p><span style="line-height:26px;">2、alter  'table_name', NAME =&gt; 'column_family_name'，METHOD =&gt; 'delete'</span></p>
<p><span style="line-height:26px;">      删除表名为'table_name'， 列族名为'column_family_name'的列族</span></p>
<p><span style="line-height:26px;"></span></p>
<p><span style="line-height:26px;">删除表名为scores，列族名为grade的列族，操作如下：</span></p>
<p><span style="line-height:26px;"></span></p>
<pre><code class="language-plain">hbase(main):025:0&gt; alter 'scores', NAME =&gt; 'grade', METHOD =&gt; 'delete'
Updating all regions with the new schema...
0/1 regions updated.
1/1 regions updated.
Done.
0 row(s) in 2.1000 seconds</code></pre>
<p></p>
<p></p>
<p><span style="color:rgb(255,0,0);">count</span>命令操作：</p>
<p>count 'table_name'</p>
<p>统计表名为‘table_name'的行数</p>
<pre><code class="language-plain">hbase(main):027:0&gt; count 'scores'
1 row(s) in 0.0200 seconds

=&gt; 1</code></pre><br><span style="color:rgb(255,0,0);">drop</span>命令操作：
<p><span style="line-height:26px;"><span style="font-family:'Times New Roman';font-size:14px;">drop 'table_name'</span></span></p>
<p><span style="line-height:26px;"><span style="font-family:'Times New Roman';font-size:14px;">删除表名为table_name的表。PS:要先用disable命令停用表后才可以删除</span></span></p>
<p><span style="line-height:26px;"><span style="font-family:'Times New Roman';font-size:14px;">用<span style="color:rgb(255,0,0);"> drop</span>删除表，操作如下：</span></span></p>
<p><span style="background-color:rgb(240,240,240);"></span></p>
<pre><code class="language-plain">hbase(main):035:0&gt; drop 'scores'
0 row(s) in 0.1470 seconds</code></pre><span style="font-family:'Times New Roman';font-size:14px;"><br></span>
<p><span style="line-height:26px;"><span style="font-family:'Times New Roman';font-size:14px;">关闭shell<br></span></span></p>
<p><span style="line-height:26px;"><span style="font-family:'Times New Roman';font-size:14px;"></span></span></p>
<pre><code class="language-plain">hbase(main):013:0&gt; exit</code></pre>
<p></p>
<p><span style="line-height:26px;"><span style="color:rgb(255,0,0);"><strong><span style="font-family:'Times New Roman';font-size:14px;">5、停止Hbase Shell</span></strong></span></span></p>
<p><span style="line-height:26px;"><span style="font-family:'Times New Roman';font-size:14px;">运行停止脚本来停止HBase.<br></span></span></p>
<p><span style="line-height:26px;"><span style="font-family:'Times New Roman';font-size:14px;"></span></span></p>
<pre><code class="language-plain">$ ./bin/stop-hbase.sh
stopping hbase..............</code></pre>
            </div>
                </div>