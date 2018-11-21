---
layout:     post
title:      HBase学习之路 （三）HBase集群Shell操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><strong>目录</strong></p>

<ul><li><a href="https://www.cnblogs.com/qingyunzong/p/8671153.html#_label0" rel="nofollow">进入HBase命令行</a></li>
	<li><a href="https://www.cnblogs.com/qingyunzong/p/8671153.html#_label1" rel="nofollow">HBase表的操作</a>
	<ul><li><a href="https://www.cnblogs.com/qingyunzong/p/8671153.html#_label1_0" rel="nofollow">创建create</a></li>
		<li><a href="https://www.cnblogs.com/qingyunzong/p/8671153.html#_label1_1" rel="nofollow">查看表列表list</a></li>
		<li><a href="https://www.cnblogs.com/qingyunzong/p/8671153.html#_label1_2" rel="nofollow">查看表的详细信息desc</a></li>
		<li><a href="https://www.cnblogs.com/qingyunzong/p/8671153.html#_label1_3" rel="nofollow">修改表的定义alter</a></li>
	</ul></li>
	<li><a href="https://www.cnblogs.com/qingyunzong/p/8671153.html#_label2" rel="nofollow">HBase表中数据的操作</a>
	<ul><li><a href="https://www.cnblogs.com/qingyunzong/p/8671153.html#_label2_0" rel="nofollow">增put</a></li>
		<li><a href="https://www.cnblogs.com/qingyunzong/p/8671153.html#_label2_1" rel="nofollow">查get + scan</a></li>
		<li><a href="https://www.cnblogs.com/qingyunzong/p/8671153.html#_label2_2" rel="nofollow">删delete</a></li>
	</ul></li>
</ul><p> </p>

<p><strong>正文</strong></p>

<p><a href="https://www.cnblogs.com/qingyunzong/p/8671153.html#_labelTop" rel="nofollow">回到顶部</a><a name="_label0"></a></p>

<h2>进入HBase命令行</h2>

<p>在你安装的随意台服务器节点上，执行命令：hbase shell，会进入到你的 hbase shell 客 户端</p>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<pre>
[hadoop@hadoop1 ~]$ <strong>hbase shell</strong>
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/home/hadoop/apps/hbase-1.2.6/lib/slf4j-log4j12-1.7.5.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/home/hadoop/apps/hadoop-2.7.5/share/hadoop/common/lib/slf4j-log4j12-1.7.10.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [org.slf4j.impl.Log4jLoggerFactory]
HBase Shell; enter 'help&lt;RETURN&gt;' for list of supported commands.
Type "exit&lt;RETURN&gt;" to leave the HBase Shell
Version 1.2.6, rUnknown, Mon May 29 02:25:32 CDT 2017

hbase(main):001:0&gt; </pre>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<p> 说明，先看一下提示。其实是不是有一句很重要的话：</p>

<pre>
HBase Shell; enter 'help&lt;RETURN&gt;' for list of supported commands.
Type "exit&lt;RETURN&gt;" to leave the HBase Shell</pre>

<p>讲述了怎么获得帮助，怎么退出客户端</p>

<p>help 获取帮助</p>

<p>　　help：获取所有命令提示</p>

<p>　　help "dml" ：获取一组命令的提示</p>

<p>　　help "put" ：获取一个单独命令的提示帮助</p>

<p>exit 退出 hbase shell 客户端</p>

<p><a href="https://www.cnblogs.com/qingyunzong/p/8671153.html#_labelTop" rel="nofollow">回到顶部</a><a name="_label1"></a></p>

<h2>HBase表的操作</h2>

<p> 关于表的操作包括（创建create，查看表列表list。查看表的详细信息desc，删除表drop，清空表truncate，修改表的定义alter）</p>

<p><a name="_label1_0"></a></p>

<h3>创建create</h3>

<p>可以输入以下命令进行查看帮助命令</p>

<pre>
hbase(main):001:0&gt; <strong>help 'create'</strong></pre>

<p><img alt="" class="has" id="code_img_opened_4a5ad8fc-9a1d-4539-9e55-977bcac25e6f" src="https://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif"></p>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<pre>
 1 hbase(main):001:0&gt; help 'create'
 2 Creates a table. Pass a table name, and a set of column family
 3 specifications (at least one), and, optionally, table configuration.
 4 Column specification can be a simple string (name), or a dictionary
 5 (dictionaries are described below in main help output), necessarily 
 6 including NAME attribute. 
 7 Examples:
 8 
 9 Create a table with namespace=ns1 and table qualifier=t1
10   hbase&gt; create 'ns1:t1', {NAME =&gt; 'f1', VERSIONS =&gt; 5}
11 
12 Create a table with namespace=default and table qualifier=t1
13   hbase&gt; create 't1', {NAME =&gt; 'f1'}, {NAME =&gt; 'f2'}, {NAME =&gt; 'f3'}
14   hbase&gt; # The above in shorthand would be the following:
15   hbase&gt; create 't1', 'f1', 'f2', 'f3'
16   hbase&gt; create 't1', {NAME =&gt; 'f1', VERSIONS =&gt; 1, TTL =&gt; 2592000, BLOCKCACHE =&gt; true}
17   hbase&gt; create 't1', {NAME =&gt; 'f1', CONFIGURATION =&gt; {'hbase.hstore.blockingStoreFiles' =&gt; '10'}}
18   
19 Table configuration options can be put at the end.
20 Examples:
21 
22   hbase&gt; create 'ns1:t1', 'f1', SPLITS =&gt; ['10', '20', '30', '40']
23   hbase&gt; create 't1', 'f1', SPLITS =&gt; ['10', '20', '30', '40']
24   hbase&gt; create 't1', 'f1', SPLITS_FILE =&gt; 'splits.txt', OWNER =&gt; 'johndoe'
25   hbase&gt; create 't1', {NAME =&gt; 'f1', VERSIONS =&gt; 5}, METADATA =&gt; { 'mykey' =&gt; 'myvalue' }
26   hbase&gt; # Optionally pre-split the table into NUMREGIONS, using
27   hbase&gt; # SPLITALGO ("HexStringSplit", "UniformSplit" or classname)
28   hbase&gt; create 't1', 'f1', {NUMREGIONS =&gt; 15, SPLITALGO =&gt; 'HexStringSplit'}
29   hbase&gt; create 't1', 'f1', {NUMREGIONS =&gt; 15, SPLITALGO =&gt; 'HexStringSplit', REGION_REPLICATION =&gt; 2, CONFIGURATION =&gt; {'hbase.hregion.scan.loadColumnFamiliesOnDemand' =&gt; 'true'}}
30   hbase&gt; create 't1', {NAME =&gt; 'f1', DFS_REPLICATION =&gt; 1}
31 
32 You can also keep around a reference to the created table:
33 
34   hbase&gt; t1 = create 't1', 'f1'
35 
36 Which gives you a reference to the table named 't1', on which you can then
37 call methods.
38 hbase(main):002:0&gt; </pre>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<p>可以看到其中一条提示</p>

<pre>
hbase&gt; create 't1', {NAME =&gt; 'f1'}, {NAME =&gt; 'f2'}, {NAME =&gt; 'f3'}</pre>

<p>其中t1是表名，f1,f2,f3是列簇的名，如：</p>

<pre>
hbase(main):002:0&gt; <strong>create 'myHbase',{NAME =&gt; 'myCard',VERSIONS =&gt; 5}
</strong>0 row(s) in 3.1270 seconds

=&gt; Hbase::Table - myHbase
hbase(main):003:0&gt; </pre>

<p>创建了一个名为myHbase的表，表里面有1个列簇，名为<strong>myCard</strong>，保留5个版本信息</p>

<p><a name="_label1_1"></a></p>

<h3>查看表列表list</h3>

<p>可以输入以下命令进行查看帮助命令</p>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<pre>
hbase(main):003:0&gt; <strong>help 'list'</strong>
List all tables in hbase. Optional regular expression parameter could
be used to filter the output. Examples:

  hbase&gt; list
  hbase&gt; list 'abc.*'
  hbase&gt; list 'ns:abc.*'
  hbase&gt; list 'ns:.*'
hbase(main):004:0&gt; </pre>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<p>直接输入list进行查看</p>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<pre>
hbase(main):004:0&gt;<strong> list</strong>
TABLE                                                                                                           
myHbase                                                                                                         
1 row(s) in 0.0650 seconds

=&gt; ["myHbase"]
hbase(main):005:0&gt; </pre>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<p>只有一条结果，就是刚刚创建的表myHbase</p>

<p><a name="_label1_2"></a></p>

<h3>查看表的详细信息desc</h3>

<p>一个大括号，就相当于一个列簇。</p>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<pre>
hbase(main):006:0&gt; <strong>desc 'myHbase'</strong>
Table myHbase is ENABLED                                                                                        
myHbase                                                                                                         
COLUMN FAMILIES DESCRIPTION                                                                                     
{NAME =&gt; 'myCard', BLOOMFILTER =&gt; 'ROW', VERSIONS =&gt; '5', IN_MEMORY =&gt; 'false', KEEP_DELETED_CELLS =&gt; 'FALSE', D
ATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; 'FOREVER', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', BLOCKCACHE =&gt; 'true'
, BLOCKSIZE =&gt; '65536', REPLICATION_SCOPE =&gt; '0'}                                                               
1 row(s) in 0.2160 seconds

hbase(main):007:0&gt; </pre>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<p><a name="_label1_3"></a></p>

<h3>修改表的定义alter</h3>

<p>添加一个列簇</p>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<p>hbase(main):007:0&gt; <strong>alter 'myHbase', NAME =&gt; 'myInfo'</strong><br>
Updating all regions with the new schema...<br>
1/1 regions updated.<br>
Done.<br>
0 row(s) in 2.0690 seconds</p>

<p>hbase(main):008:0&gt; <strong>desc 'myHbase'</strong><br>
Table myHbase is ENABLED<br>
myHbase<br>
COLUMN FAMILIES DESCRIPTION<br>
{NAME =&gt; 'myCard', BLOOMFILTER =&gt; 'ROW', VERSIONS =&gt; '5', IN_MEMORY =&gt; 'false', KEEP_DELETED_CELLS =&gt; 'FALSE', D<br>
ATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; 'FOREVER', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', BLOCKCACHE =&gt; 'true'<br>
, BLOCKSIZE =&gt; '65536', REPLICATION_SCOPE =&gt; '0'}<br>
{NAME =&gt; 'myInfo', BLOOMFILTER =&gt; 'ROW', VERSIONS =&gt; '1', IN_MEMORY =&gt; 'false', KEEP_DELETED_CELLS =&gt; 'FALSE', D<br>
ATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; 'FOREVER', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', BLOCKCACHE =&gt; 'true'<br>
, BLOCKSIZE =&gt; '65536', REPLICATION_SCOPE =&gt; '0'}<br>
2 row(s) in 0.0420 seconds</p>

<p>hbase(main):009:0&gt;</p>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<p>删除一个列簇</p>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<pre>
hbase(main):009:0&gt; <strong>alter 'myHbase', NAME =&gt; 'myCard', METHOD =&gt; 'delete'</strong>
Updating all regions with the new schema...
1/1 regions updated.
Done.
0 row(s) in 2.1920 seconds

hbase(main):010:0&gt; <strong>desc 'myHbase'</strong>
Table myHbase is ENABLED                                                                                        
myHbase                                                                                                         
COLUMN FAMILIES DESCRIPTION                                                                                     
{NAME =&gt; 'myInfo', BLOOMFILTER =&gt; 'ROW', VERSIONS =&gt; '1', IN_MEMORY =&gt; 'false', KEEP_DELETED_CELLS =&gt; 'FALSE', D
ATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; 'FOREVER', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', BLOCKCACHE =&gt; 'true'
, BLOCKSIZE =&gt; '65536', REPLICATION_SCOPE =&gt; '0'}                                                               
1 row(s) in 0.0290 seconds

hbase(main):011:0&gt; </pre>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<p>删除一个列簇也可以执行以下命令</p>

<pre>
<strong>alter 'myHbase', 'delete' =&gt; 'myCard'</strong></pre>

<p>添加列簇hehe同时删除列簇myInfo</p>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<pre>
hbase(main):011:0&gt; <strong>alter 'myHbase', {NAME =&gt; 'hehe'}, {NAME =&gt; 'myInfo', METHOD =&gt; 'delete'</strong><strong>}</strong>
Updating all regions with the new schema...
1/1 regions updated.
Done.
Updating all regions with the new schema...
1/1 regions updated.
Done.
0 row(s) in 3.8260 seconds

hbase(main):012:0&gt;<strong> desc 'myHbase'</strong>
Table myHbase is ENABLED                                                                                        
myHbase                                                                                                         
COLUMN FAMILIES DESCRIPTION                                                                                     
{NAME =&gt; 'hehe', BLOOMFILTER =&gt; 'ROW', VERSIONS =&gt; '1', IN_MEMORY =&gt; 'false', KEEP_DELETED_CELLS =&gt; 'FALSE', DAT
A_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; 'FOREVER', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', BLOCKCACHE =&gt; 'true', 
BLOCKSIZE =&gt; '65536', REPLICATION_SCOPE =&gt; '0'}                                                                 
1 row(s) in 0.0410 seconds

hbase(main):013:0&gt; </pre>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<p>清空表truncate                                                                                    </p>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<pre>
hbase(main):013:0&gt; truncate 'myHbase'
Truncating 'myHbase' table (it may take a while):
 - Disabling table...
 - Truncating table...
0 row(s) in 3.6760 seconds

hbase(main):014:0&gt; </pre>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<p>删除表drop</p>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<pre>
hbase(main):014:0&gt; <strong>drop 'myHbase'

ERROR: Table myHbase is</strong><strong> enabled. Disable it first.</strong>

Here is some help for this command:
Drop the named table. Table must first be disabled:
  hbase&gt; drop 't1'
  hbase&gt; drop 'ns1:t1'


hbase(main):015:0&gt; </pre>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<p>直接删除表会报错，根据提示需要先停用表</p>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<pre>
hbase(main):015:0&gt; <strong>disable 'myHbase'</strong>
0 row(s) in 2.2620 seconds

hbase(main):016:0&gt; <strong>drop 'myHbase'</strong>
0 row(s) in 1.2970 seconds

hbase(main):017:0&gt;<strong> list</strong>
TABLE                                                                                                           
0 row(s) in 0.0110 seconds

=&gt; []
hbase(main):018:0&gt; </pre>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<p><a href="https://www.cnblogs.com/qingyunzong/p/8671153.html#_labelTop" rel="nofollow">回到顶部</a><a name="_label2"></a></p>

<h2>HBase表中数据的操作</h2>

<p>关于数据的操作（增put，删delete，查get + scan,  改==变相的增加）</p>

<p>创建 user 表，包含 info、data 两个列簇</p>

<pre>
hbase(main):018:0&gt; <strong>create 'user_info',{NAME=&gt;'base_info',VERSIONS=&gt;3 },{NAME=&gt;'extra_info',VERSIONS=&gt;1 } 
</strong>0 row(s) in 4.2670 seconds

=&gt; Hbase::Table - user_info
hbase(main):019:0&gt; </pre>

<p><a name="_label2_0"></a></p>

<h3>增put</h3>

<p>查看帮助，需要传入表名，rowkey，列簇名、值等</p>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<pre>
hbase(main):019:0&gt; <strong>help 'put'</strong>
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
hbase(main):020:0&gt; </pre>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<p> 向 user 表中插入信息，row key 为 user0001，列簇 base_info 中添加 name 列标示符，值为 zhangsan1</p>

<pre>
hbase(main):020:0&gt; <strong>put 'user_info', 'user0001', 'base_info:name', 'zhangsan1'</strong>
0 row(s) in 0.2900 seconds

hbase(main):021:0&gt; </pre>

<p>此处可以多添加几条数据</p>

<p><img alt="" class="has" id="code_img_opened_e7713a30-8974-4691-b169-80910d442f77" src="https://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif"></p>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<pre>
put 'user_info', 'zhangsan_20150701_0001', 'base_info:name', 'zhangsan1'
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
put 'user_info', 'baiyc_20150716_0008', 'extra_info:Hobbies', 'sport'</pre>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<p><a name="_label2_1"></a></p>

<h3>查get + scan</h3>

<p>获取 user 表中 row key 为 user0001 的所有信息</p>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<pre>
hbase(main):022:0&gt; get 'user_info', 'user0001'
COLUMN                        CELL                                                                              
 base_info:name               timestamp=1522320801670, value=zhangsan1                                          
1 row(s) in 0.1310 seconds

hbase(main):023:0&gt; </pre>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<p> </p>

<p>获取user表中row key为rk0001，info列簇的所有信息</p>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<pre>
hbase(main):025:0&gt; get 'user_info', 'rk0001', 'base_info'
COLUMN                        CELL                                                                              
 base_info:name               timestamp=1522321247732, value=zhangsan                                           
1 row(s) in 0.0320 seconds

hbase(main):026:0&gt; </pre>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<p>查询user_info表中的所有信息</p>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<pre>
hbase(main):026:0&gt; scan 'user_info'
ROW                           COLUMN+CELL                                                                       
 rk0001                       column=base_info:name, timestamp=1522321247732, value=zhangsan                    
 user0001                     column=base_info:name, timestamp=1522320801670, value=zhangsan1                   
2 row(s) in 0.0970 seconds

hbase(main):027:0&gt; </pre>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<p>查询user_info表中列簇为base_info的信息</p>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<pre>
hbase(main):027:0&gt; scan 'user_info', {COLUMNS =&gt; 'base_info'}
ROW                           COLUMN+CELL                                                                       
 rk0001                       column=base_info:name, timestamp=1522321247732, value=zhangsan                    
 user0001                     column=base_info:name, timestamp=1522320801670, value=zhangsan1                   
2 row(s) in 0.0620 seconds

hbase(main):028:0&gt; </pre>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<p><a name="_label2_2"></a></p>

<h3>删delete</h3>

<p>删除user_info表row key为rk0001，列标示符为base_info:name的数据</p>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<pre>
hbase(main):028:0&gt;<strong> delete 'user_info', 'rk0001', 'base_info:name'</strong>
0 row(s) in 0.0780 seconds

hbase(main):029:0&gt; <strong>scan 'user_info', {COLUMNS =&gt; 'base_info'</strong><strong>}</strong>
ROW                           COLUMN+CELL                                                                       
 user0001                     column=base_info:name, timestamp=1522320801670, value=zhangsan1                   
1 row(s) in 0.0530 seconds

hbase(main):030:0&gt; </pre>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>            </div>
                </div>