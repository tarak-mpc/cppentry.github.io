---
layout:     post
title:      Hbase学习（三）shell操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_41851454/article/details/79770418				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p></p><p style="color:rgb(0,0,0);font-family:'微软雅黑', PTSans, Arial, sans-serif;background-color:rgb(255,255,255);font-size:18px;"><span>目录</span></p><ul style="margin-left:40px;color:rgb(0,0,0);font-family:'微软雅黑', PTSans, Arial, sans-serif;font-size:15px;background-color:rgb(255,255,255);"><li style="margin-left:0px;"><a href="http://www.cnblogs.com/qingyunzong/p/8671153.html#_label0" rel="nofollow" style="color:rgb(0,0,0);"><strong>进入HBase命令行</strong></a></li><li style="margin-left:0px;"><a href="http://www.cnblogs.com/qingyunzong/p/8671153.html#_label1" rel="nofollow" style="color:rgb(0,0,0);"><strong>HBase表的操作</strong></a><ul style="margin-left:40px;"><li style="margin-left:0px;list-style-type:circle;"><a href="http://www.cnblogs.com/qingyunzong/p/8671153.html#_label1_0" rel="nofollow" style="color:rgb(0,0,0);"><strong>创建create</strong></a></li><li style="margin-left:0px;list-style-type:circle;"><a href="http://www.cnblogs.com/qingyunzong/p/8671153.html#_label1_1" rel="nofollow" style="color:rgb(0,0,0);"><strong>查看表列表list</strong></a></li><li style="margin-left:0px;list-style-type:circle;"><a href="http://www.cnblogs.com/qingyunzong/p/8671153.html#_label1_2" rel="nofollow" style="color:rgb(0,0,0);"><strong>查看表的详细信息desc</strong></a></li><li style="margin-left:0px;list-style-type:circle;"><a href="http://www.cnblogs.com/qingyunzong/p/8671153.html#_label1_3" rel="nofollow" style="color:rgb(0,0,0);"><strong>修改表的定义alter</strong></a></li></ul></li><li style="margin-left:0px;"><a href="http://www.cnblogs.com/qingyunzong/p/8671153.html#_label2" rel="nofollow" style="color:rgb(0,0,0);"><strong>HBase表中数据的操作</strong></a><ul style="margin-left:40px;"><li style="margin-left:0px;list-style-type:circle;"><a href="http://www.cnblogs.com/qingyunzong/p/8671153.html#_label2_0" rel="nofollow" style="color:rgb(0,0,0);"><strong>增put</strong></a></li><li style="margin-left:0px;list-style-type:circle;"><a href="http://www.cnblogs.com/qingyunzong/p/8671153.html#_label2_1" rel="nofollow" style="color:rgb(0,0,0);"><strong>查get + scan</strong></a></li><li style="margin-left:0px;list-style-type:circle;"><a href="http://www.cnblogs.com/qingyunzong/p/8671153.html#_label2_2" rel="nofollow" style="color:rgb(0,0,0);"><strong>删delete</strong></a></li></ul></li></ul><br><h2>正文</h2><h3>进入HBase命令行：</h3><p>在你安装的随意台服务器节点上，执行命令：hbase shell，会进入到你的 hbase shell 客户端<br></p><pre><code class="language-java">[potter@potter2 ~]$ hbase shell
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/home/potter/apps/hbase-1.2.6/lib/slf4j-log4j12-1.7.5.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/home/potter/apps/hadoop-2.7.5/share/hadoop/common/lib/slf4j-log4j12-1.7.10.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [org.slf4j.impl.Log4jLoggerFactory]
HBase Shell; enter 'help&lt;RETURN&gt;' for list of supported commands.
Type "exit&lt;RETURN&gt;" to leave the HBase Shell
Version 1.2.6, rUnknown, Mon May 29 02:25:32 CDT 2017

hbase(main):001:0&gt; </code></pre><p> 说明，先看一下提示。其实是不是有一句很重要的话：<br></p><pre><code class="language-java">HBase Shell; enter 'help&lt;RETURN&gt;' for list of supported commands.
Type "exit&lt;RETURN&gt;" to leave the HBase Shell
Version 1.2.6, rUnknown, Mon May 29 02:25:32 CDT 2017</code></pre><p>讲述了怎么获得帮助，怎么退出客户端<br></p><h3>学会使用help命令：</h3><pre><code class="language-java">hbase(main):001:0&gt; help
HBase Shell, version 1.2.6, rUnknown, Mon May 29 02:25:32 CDT 2017
Type 'help "COMMAND"', (e.g. 'help "get"' -- the quotes are necessary) for help on a specific command.
Commands are grouped. Type 'help "COMMAND_GROUP"', (e.g. 'help "general"') for help on a command group.

COMMAND GROUPS:
  Group name: general
  Commands: status, table_help, version, whoami

  Group name: ddl
  Commands: alter, alter_async, alter_status, create, describe, disable, disable_all, drop, drop_all, enable, enable_all, exists, get_table, is_disabled, is_enabled, list, locate_region, show_filters

  Group name: namespace
  Commands: alter_namespace, create_namespace, describe_namespace, drop_namespace, list_namespace, list_namespace_tables

  Group name: dml
  Commands: append, count, delete, deleteall, get, get_counter, get_splits, incr, put, scan, truncate, truncate_preserve

  Group name: tools
  Commands: assign, balance_switch, balancer, balancer_enabled, catalogjanitor_enabled, catalogjanitor_run, catalogjanitor_switch, close_region, compact, compact_rs, flush, major_compact, merge_region, move, normalize, normalizer_enabled, normalizer_switch, split, trace, unassign, wal_roll, zk_dump

  Group name: replication
  Commands: add_peer, append_peer_tableCFs, disable_peer, disable_table_replication, enable_peer, enable_table_replication, list_peers, list_replicated_tables, remove_peer, remove_peer_tableCFs, set_peer_tableCFs, show_peer_tableCFs

  Group name: snapshots
  Commands: clone_snapshot, delete_all_snapshot, delete_snapshot, list_snapshots, restore_snapshot, snapshot

  Group name: configuration
  Commands: update_all_config, update_config

  Group name: quotas
  Commands: list_quotas, set_quota

  Group name: security
  Commands: grant, list_security_capabilities, revoke, user_permission

  Group name: procedures
  Commands: abort_procedure, list_procedures

  Group name: visibility labels
  Commands: add_labels, clear_auths, get_auths, list_labels, set_auths, set_visibility

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
For more on the HBase Shell, see http://hbase.apache.org/book.html
hbase(main):002:0&gt; </code></pre><p>help获取帮助</p><p>　　help：获取所有命令提示</p><p>　　help "dml" ：获取一组命令的提示</p><p>　　help "put" ：获取一个单独命令的提示帮助</p><p>exit 退出 hbase shell 客户端</p><h3>HBase表的操作：</h3><p> 关于表的操作包括（创建create，查看表列表list。查看表的详细信息desc，删除表drop，清空表truncate，修改表的定义alter）<br></p><h4>general：普通命令组 </h4><p>status 查看集群状态 <br></p><p><img src="https://img-blog.csdn.net/20180331162425520?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxODUxNDU0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></p><h4>创建（creat）</h4><pre><code class="language-java">hbase(main):001:0&gt; help 'create'</code></pre><p>可以看到其中一条提示<br></p><pre><code class="language-java">hbase&gt; create 't1', {NAME =&gt; 'f1'}, {NAME =&gt; 'f2'}, {NAME =&gt; 'f3'}</code></pre><pre><code class="language-java">其中t1是表名，f1,f2,f3是列簇的名，如：</code></pre><pre><code class="language-java">hbase(main):002:0&gt; create 'myHbase',{NAME =&gt; 'myCard',VERSIONS =&gt; 5}
0 row(s) in 3.1270 seconds

=&gt; Hbase::Table - myHbase
hbase(main):003:0&gt; </code></pre>创建了一个名为myHbase的表，表里面有1个列簇，名为myCard，保留5个版本信息<br><br><p><img src="https://img-blog.csdn.net/20180331162252365?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxODUxNDU0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""></p><h4>查看表列表list</h4><p><span style="color:rgb(0,0,0);font-family:'微软雅黑', PTSans, Arial, sans-serif;font-size:15px;background-color:rgb(255,255,255);">可以输入以下命令进行查看帮助命令</span><br></p><pre><code class="language-java">hbase(main):006:0&gt; help 'list'
List all tables in hbase. Optional regular expression parameter could
be used to filter the output. Examples:

  hbase&gt; list
  hbase&gt; list 'abc.*'
  hbase&gt; list 'ns:abc.*'
  hbase&gt; list 'ns:.*'
hbase(main):007:0&gt; </code></pre><p>直接输入list查看</p><pre><code class="language-java">hbase(main):007:0&gt; list
TABLE                                                                                                   
blog                                                                                                    
stu                                                                                                     
user_info                                                                                               
3 row(s) in 0.0340 seconds

=&gt; ["blog", "stu", "user_info"]
hbase(main):008:0&gt; </code></pre><p>中括号里面是表的信息</p><h4>查看表的详细信息desc</h4><p>一个大括号，就相当于一个列簇</p><pre><code class="language-java">hbase(main):008:0&gt; desc 'stu'
Table stu is ENABLED                                                                                    
stu                                                                                                     
COLUMN FAMILIES DESCRIPTION                                                                             
{NAME =&gt; 'cf1', BLOOMFILTER =&gt; 'ROW', VERSIONS =&gt; '1', IN_MEMORY =&gt; 'false', KEEP_DELETED_CELLS =&gt; 'FALS
E', DATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; 'FOREVER', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', BLOCKCA
CHE =&gt; 'true', BLOCKSIZE =&gt; '65536', REPLICATION_SCOPE =&gt; '0'}                                          
{NAME =&gt; 'cf2', BLOOMFILTER =&gt; 'ROW', VERSIONS =&gt; '1', IN_MEMORY =&gt; 'false', KEEP_DELETED_CELLS =&gt; 'FALS
E', DATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; 'FOREVER', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', BLOCKCA
CHE =&gt; 'true', BLOCKSIZE =&gt; '65536', REPLICATION_SCOPE =&gt; '0'}                                          
{NAME =&gt; 'cf3', BLOOMFILTER =&gt; 'ROW', VERSIONS =&gt; '1', IN_MEMORY =&gt; 'false', KEEP_DELETED_CELLS =&gt; 'FALS
E', DATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; 'FOREVER', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', BLOCKCA
CHE =&gt; 'true', BLOCKSIZE =&gt; '65536', REPLICATION_SCOPE =&gt; '0'}                                          
3 row(s) in 0.6760 seconds

hbase(main):009:0&gt; </code></pre><h4>修改表的定义alter</h4><h5>添加一个列簇</h5><pre><code class="language-java">hbase(main):009:0&gt; alter 'stu', NAME =&gt; 'myInfo'
Updating all regions with the new schema...
0/1 regions updated.
0/1 regions updated.
1/1 regions updated.
Done.
0 row(s) in 8.1090 seconds

hbase(main):010:0&gt;</code></pre><p>查看添加的列簇</p><pre><code class="language-java">hbase(main):010:0&gt; desc 'stu'
Table stu is ENABLED                                                                                    
stu                                                                                                     
COLUMN FAMILIES DESCRIPTION                                                                             
{NAME =&gt; 'cf1', BLOOMFILTER =&gt; 'ROW', VERSIONS =&gt; '1', IN_MEMORY =&gt; 'false', KEEP_DELETED_CELLS =&gt; 'FALS
E', DATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; 'FOREVER', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', BLOCKCA
CHE =&gt; 'true', BLOCKSIZE =&gt; '65536', REPLICATION_SCOPE =&gt; '0'}                                          
{NAME =&gt; 'cf2', BLOOMFILTER =&gt; 'ROW', VERSIONS =&gt; '1', IN_MEMORY =&gt; 'false', KEEP_DELETED_CELLS =&gt; 'FALS
E', DATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; 'FOREVER', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', BLOCKCA
CHE =&gt; 'true', BLOCKSIZE =&gt; '65536', REPLICATION_SCOPE =&gt; '0'}                                          
{NAME =&gt; 'cf3', BLOOMFILTER =&gt; 'ROW', VERSIONS =&gt; '1', IN_MEMORY =&gt; 'false', KEEP_DELETED_CELLS =&gt; 'FALS
E', DATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; 'FOREVER', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', BLOCKCA
CHE =&gt; 'true', BLOCKSIZE =&gt; '65536', REPLICATION_SCOPE =&gt; '0'}                                          
{NAME =&gt; 'myInfo', BLOOMFILTER =&gt; 'ROW', VERSIONS =&gt; '1', IN_MEMORY =&gt; 'false', KEEP_DELETED_CELLS =&gt; 'F
ALSE', DATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; 'FOREVER', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', BLOC
KCACHE =&gt; 'true', BLOCKSIZE =&gt; '65536', REPLICATION_SCOPE =&gt; '0'}                                       
4 row(s) in 0.1550 seconds

hbase(main):011:0&gt; </code></pre><h5>删除一个列簇</h5><pre><code class="language-java">hbase(main):011:0&gt; alter 'stu',NAME =&gt; 'myInfo', METHOD =&gt; 'delete'
Updating all regions with the new schema...
0/1 regions updated.
1/1 regions updated.
Done.
0 row(s) in 3.6470 seconds

hbase(main):012:0&gt;</code></pre><p>查看删除的列簇还有没有</p><pre><code class="language-java">hbase(main):012:0&gt; desc 'stu'
Table stu is ENABLED                                                                                    
stu                                                                                                     
COLUMN FAMILIES DESCRIPTION                                                                             
{NAME =&gt; 'cf1', BLOOMFILTER =&gt; 'ROW', VERSIONS =&gt; '1', IN_MEMORY =&gt; 'false', KEEP_DELETED_CELLS =&gt; 'FALS
E', DATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; 'FOREVER', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', BLOCKCA
CHE =&gt; 'true', BLOCKSIZE =&gt; '65536', REPLICATION_SCOPE =&gt; '0'}                                          
{NAME =&gt; 'cf2', BLOOMFILTER =&gt; 'ROW', VERSIONS =&gt; '1', IN_MEMORY =&gt; 'false', KEEP_DELETED_CELLS =&gt; 'FALS
E', DATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; 'FOREVER', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', BLOCKCA
CHE =&gt; 'true', BLOCKSIZE =&gt; '65536', REPLICATION_SCOPE =&gt; '0'}                                          
{NAME =&gt; 'cf3', BLOOMFILTER =&gt; 'ROW', VERSIONS =&gt; '1', IN_MEMORY =&gt; 'false', KEEP_DELETED_CELLS =&gt; 'FALS
E', DATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; 'FOREVER', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', BLOCKCA
CHE =&gt; 'true', BLOCKSIZE =&gt; '65536', REPLICATION_SCOPE =&gt; '0'}                                          
3 row(s) in 0.0580 seconds

hbase(main):013:0&gt;</code></pre><p>删除一个列簇也可以执行以下命令</p><pre><code class="language-java">alter 'myHbase', 'delete' =&gt; 'myInfo'</code></pre><p>添加列簇hehe，同时删除列簇cf1</p><pre><code class="language-java">hbase(main):013:0&gt; alter 'stu', {NAME =&gt; 'hehe'}, {NAME =&gt; 'cf1', METHOD =&gt; 'delete'}
Updating all regions with the new schema...
0/1 regions updated.
1/1 regions updated.
Done.
Updating all regions with the new schema...
0/1 regions updated.
1/1 regions updated.
Done.
0 row(s) in 7.3360 seconds

hbase(main):014:0&gt;</code></pre><p>查看状态</p><pre><code class="language-java">hbase(main):014:0&gt; desc 'stu'
Table stu is ENABLED                                                                                    
stu                                                                                                     
COLUMN FAMILIES DESCRIPTION                                                                             
{NAME =&gt; 'cf2', BLOOMFILTER =&gt; 'ROW', VERSIONS =&gt; '1', IN_MEMORY =&gt; 'false', KEEP_DELETED_CELLS =&gt; 'FALS
E', DATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; 'FOREVER', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', BLOCKCA
CHE =&gt; 'true', BLOCKSIZE =&gt; '65536', REPLICATION_SCOPE =&gt; '0'}                                          
{NAME =&gt; 'cf3', BLOOMFILTER =&gt; 'ROW', VERSIONS =&gt; '1', IN_MEMORY =&gt; 'false', KEEP_DELETED_CELLS =&gt; 'FALS
E', DATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; 'FOREVER', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', BLOCKCA
CHE =&gt; 'true', BLOCKSIZE =&gt; '65536', REPLICATION_SCOPE =&gt; '0'}                                          
{NAME =&gt; 'hehe', BLOOMFILTER =&gt; 'ROW', VERSIONS =&gt; '1', IN_MEMORY =&gt; 'false', KEEP_DELETED_CELLS =&gt; 'FAL
SE', DATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; 'FOREVER', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', BLOCKC
ACHE =&gt; 'true', BLOCKSIZE =&gt; '65536', REPLICATION_SCOPE =&gt; '0'}                                         
3 row(s) in 0.0610 seconds

hbase(main):015:0&gt; </code></pre><h5>清空表truncate</h5><pre><code class="language-java">hbase(main):015:0&gt; truncate 'stu'
Truncating 'stu' table (it may take a while):
 - Disabling table...
 - Truncating table...
0 row(s) in 5.5020 seconds

hbase(main):016:0&gt;</code></pre><h5>删除表drop</h5><pre><code class="language-java">hbase(main):018:0&gt; drop 'stu'

ERROR: Table stu is enabled. Disable it first.

Here is some help for this command:
Drop the named table. Table must first be disabled:
  hbase&gt; drop 't1'
  hbase&gt; drop 'ns1:t1'


hbase(main):019:0&gt;</code></pre><p>直接删除表会报错，根据提示需要先停用表</p><pre><code class="language-java">hbase(main):020:0&gt; disable 'stu'
0 row(s) in 2.3340 seconds

hbase(main):021:0&gt; drop 'stu'
0 row(s) in 1.2920 seconds

hbase(main):022:0&gt; list
TABLE                                                                                                   
blog                                                                                                    
user_info                                                                                               
2 row(s) in 0.0180 seconds

=&gt; ["blog", "user_info"]
hbase(main):023:0&gt; </code></pre><h3>HBase表中数据的操作</h3><p></p><p>关于数据的操作（增put，删delete，查get + scan,  改==变相的增加）</p>创建 user 表，包含 info、data 两个列簇<br><pre><code class="language-java">hbase(main):023:0&gt;  create 'user',{NAME=&gt;'base_info',VERSIONS=&gt;3 },{NAME=&gt;'extra_info',VERSIONS=&gt;1 }
0 row(s) in 2.5440 seconds

=&gt; Hbase::Table - user
hbase(main):024:0&gt; list
TABLE                                                                                                   
blog                                                                                                    
user                                                                                                    
user_info                                                                                               
3 row(s) in 0.1120 seconds

=&gt; ["blog", "user", "user_info"]
hbase(main):025:0&gt;</code></pre><h3>增put</h3><p>查看帮助，需要传入表名，rowkey，列簇名、值等。</p><pre><code class="language-java">hbase(main):026:0&gt; help 'put'
Put a cell 'value' at specified table/row/column and optionally
timestamp coordinates.  To put a cell value into table 'ns1:t1' or 't1'
at row 'r1' under column 'c1' marked with the time 'ts1', do:

  hbase&gt; put 'ns1:t1', 'r1', 'c1', 'value'
  hbase&gt; put 't1', 'r1', 'c1', 'value'
  hbase&gt; put 't1', 'r1', 'c1', 'value', ts1
  hbase&gt; put 't1', 'r1', 'c1', 'value', {ATTRIBUTES=&gt;{'mykey'=&gt;'myvalue'}}
  hbase&gt; put 't1', 'r1', 'c1', 'value', ts1, {ATTRIBUTES=&gt;{'mykey'=&gt;'myvalue'}}
  hbase&gt; put 't1', 'r1', 'c1', 'value', ts1, {VISIBILITY=&gt;'PRIVATE|SECRET'}

The same commands also can be run on a table reference. Suppose you had a reference
t to table 't1', the corresponding command would be:

  hbase&gt; t.put 'r1', 'c1', 'value', ts1, {ATTRIBUTES=&gt;{'mykey'=&gt;'myvalue'}}
hbase(main):027:0&gt; </code></pre>向user表中插入信息，rowkey为 user0001， 列簇base中添加name列标示符，值为zhangsan<br><pre><code class="language-html">hbase(main):029:0&gt; put 'user', 'user0001', 'base_info:name', 'zhangsan'
0 row(s) in 0.0340 seconds

hbase(main):030:0&gt; </code></pre><p>此处可以多添加几条数据</p><pre><code class="language-java">put 'user_info', 'zhangsan_20150701_0001', 'base_info:name', 'zhangsan1'
put 'user_info', 'zhangsan_20150701_0002', 'base_info:name', 'zhangsan2'
put 'user_info', 'zhangsan_20150701_0003', 'base_info:name', 'zhangsan3'
put 'user_info', 'zhangsan_20150701_0004', 'base_info:name', 'zhangsan4'
put 'user_info', 'zhangsan_20150701_0005', 'base_info:name', 'zhangsan5'
put 'user_info', 'zhangsan_20150701_0006', 'base_info:name', 'zhangsan6'
put 'user_info', 'zhangsan_20150701_0007', 'base_info:name', 'zhangsan7'
put 'user_info', 'zhangsan_20150701_0008', 'base_info:name', 'zhangsan8'

put 'user_info', 'zhangsan_20150701_0001', 'base_info:age', '21'
put 'user_info', 'zhangsan_20150701_0002', 'base_info:age', '22'
put 'user_info', 'zhangsan_20150701_0003', 'base_info:age', '23'
put 'user_info', 'zhangsan_20150701_0004', 'base_info:age', '24'
put 'user_info', 'zhangsan_20150701_0005', 'base_info:age', '25'
put 'user_info', 'zhangsan_20150701_0006', 'base_info:age', '26'
put 'user_info', 'zhangsan_20150701_0007', 'base_info:age', '27'
put 'user_info', 'zhangsan_20150701_0008', 'base_info:age', '28'

put 'user_info', 'zhangsan_20150701_0001', 'extra_info:Hobbies', 'music'
put 'user_info', 'zhangsan_20150701_0002', 'extra_info:Hobbies', 'sport'
put 'user_info', 'zhangsan_20150701_0003', 'extra_info:Hobbies', 'music'
put 'user_info', 'zhangsan_20150701_0004', 'extra_info:Hobbies', 'sport'
put 'user_info', 'zhangsan_20150701_0005', 'extra_info:Hobbies', 'music'
put 'user_info', 'zhangsan_20150701_0006', 'extra_info:Hobbies', 'sport'
put 'user_info', 'zhangsan_20150701_0007', 'extra_info:Hobbies', 'music'

put 'user_info', 'baiyc_20150716_0001', 'base_info:name', 'baiyc1'
put 'user_info', 'baiyc_20150716_0002', 'base_info:name', 'baiyc2'
put 'user_info', 'baiyc_20150716_0003', 'base_info:name', 'baiyc3'
put 'user_info', 'baiyc_20150716_0004', 'base_info:name', 'baiyc4'
put 'user_info', 'baiyc_20150716_0005', 'base_info:name', 'baiyc5'
put 'user_info', 'baiyc_20150716_0006', 'base_info:name', 'baiyc6'
put 'user_info', 'baiyc_20150716_0007', 'base_info:name', 'baiyc7'
put 'user_info', 'baiyc_20150716_0008', 'base_info:name', 'baiyc8'

put 'user_info', 'baiyc_20150716_0001', 'base_info:age', '21'
put 'user_info', 'baiyc_20150716_0002', 'base_info:age', '22'
put 'user_info', 'baiyc_20150716_0003', 'base_info:age', '23'
put 'user_info', 'baiyc_20150716_0004', 'base_info:age', '24'
put 'user_info', 'baiyc_20150716_0005', 'base_info:age', '25'
put 'user_info', 'baiyc_20150716_0006', 'base_info:age', '26'
put 'user_info', 'baiyc_20150716_0007', 'base_info:age', '27'
put 'user_info', 'baiyc_20150716_0008', 'base_info:age', '28'

put 'user_info', 'baiyc_20150716_0001', 'extra_info:Hobbies', 'music'
put 'user_info', 'baiyc_20150716_0002', 'extra_info:Hobbies', 'sport'
put 'user_info', 'baiyc_20150716_0003', 'extra_info:Hobbies', 'music'
put 'user_info', 'baiyc_20150716_0004', 'extra_info:Hobbies', 'sport'
put 'user_info', 'baiyc_20150716_0005', 'extra_info:Hobbies', 'music'
put 'user_info', 'baiyc_20150716_0006', 'extra_info:Hobbies', 'sport'
put 'user_info', 'baiyc_20150716_0007', 'extra_info:Hobbies', 'music'
put 'user_info', 'baiyc_20150716_0008', 'extra_info:Hobbies', 'sport'</code></pre><h5>查get+scan</h5><p>获取user表中rowkey为user0001的所有信息</p><pre><code class="language-java">hbase(main):083:0&gt; get 'user', 'user0001'
COLUMN                      CELL                                                                        
 base_info:name             timestamp=1522486949513, value=zhangsan                                     
1 row(s) in 0.0840 seconds

hbase(main):084:0&gt; </code></pre>获取user表中rowkey为rk0001，info列簇的所有信息<br><pre><code class="language-java">hbase(main):025:0&gt; get 'user', 'rk0001', 'base_info'
COLUMN                        CELL                                                                              
 base_info:name               timestamp=1522321247732, value=zhangsan                                           
row(s) in 0.0320 seconds

hbase(main):026:0&gt;</code></pre><p>查询user表中所有信息</p><pre><code class="language-java">hbase(main):026:0&gt; scan 'user'
ROW                           COLUMN+CELL                                                                       
 rk0001                       column=base_info:name, timestamp=1522321247732, value=zhangsan                    
 user0001                     column=base_info:name, timestamp=1522320801670, value=zhangsan1                   
row(s) in 0.0970 seconds

hbase(main):027:0&gt;</code></pre><p><span style="color:rgb(0,0,0);font-family:'微软雅黑', PTSans, Arial, sans-serif;font-size:15px;background-color:rgb(255,255,255);">查询user_info表中列簇为base_info的信息</span></p><pre><code class="language-java">hbase(main):027:0&gt; scan 'user', {COLUMNS =&gt; 'base_info'}
ROW                           COLUMN+CELL                                                                       
 rk0001                       column=base_info:name, timestamp=1522321247732, value=zhangsan                    
 user0001                     column=base_info:name, timestamp=1522320801670, value=zhangsan1                   
row(s) in 0.0620 seconds

hbase(main):028:0&gt;</code></pre><h3>删delete</h3><p><span style="color:rgb(0,0,0);font-family:'微软雅黑', PTSans, Arial, sans-serif;font-size:15px;background-color:rgb(255,255,255);">删除user_info表row key为rk0001，列标示符为base_info:name的数据</span><br></p><pre><code class="language-java">hbase(main):028:0&gt; delete 'user_info', 'rk0001', 'base_info:name'
row(s) in 0.0780 seconds

hbase(main):029:0&gt; scan 'user_info', {COLUMNS =&gt; 'base_info'}
ROW                           COLUMN+CELL                                                                       
 user0001                     column=base_info:name, timestamp=1522320801670, value=zhangsan1                   
row(s) in 0.0530 seconds

hbase(main):030:0&gt;</code></pre><p><br></p>            </div>
                </div>