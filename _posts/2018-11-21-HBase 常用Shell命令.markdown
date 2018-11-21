---
layout:     post
title:      HBase 常用Shell命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/helloxiaozhe/article/details/80278015				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <div class="blogpost-body" style="margin-bottom:20px;color:rgb(0,0,0);font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;background-color:rgb(255,255,255);"><p style="line-height:1.5;margin:10px auto;"><span style="text-align:left;white-space:pre-wrap;color:rgb(0,0,0);"><span style="font-size:16px;">HBase 常用Shell命令</span></span></p><p style="line-height:1.5;margin:10px auto;"><span style="text-align:left;white-space:pre-wrap;color:rgb(0,0,0);"></span></p><p style="margin:10px auto;list-style-type:none;list-style-image:none;color:rgb(68,68,68);font-family:Tahoma, Arial, Helvetica, sans-serif;background-color:rgb(255,255,255);"><span style="font-size:14px;"><strong>0、启动、关闭hbase<br></strong></span><span style="font-size:14px;">./bin/start-hbase.sh<br>./bin/stop-hbase.sh</span></p><p style="font-size:14px;margin:10px auto;list-style-type:none;list-style-image:none;color:rgb(68,68,68);font-family:Tahoma, Arial, Helvetica, sans-serif;background-color:rgb(255,255,255);">查询HBase版本<br>hbase(main):010:0&gt; version</p><div style="font-size:14px;white-space:pre-wrap;text-align:left;line-height:1.75;"><span style="font-size:13px;font-family:Verdana;background-color:rgb(255,255,255);"><strong>1、进入hbase shell console</strong></span></div><div style="font-size:14px;white-space:pre-wrap;text-align:left;line-height:1.75;">$HBASE_HOME/bin/hbase shell</div><div style="font-size:14px;white-space:pre-wrap;text-align:left;line-height:1.75;">如果有kerberos认证，需要事先使用相应的keytab进行一下认证（使用kinit命令），认证成功之后再使用hbase shell进入</div><div style="font-size:14px;white-space:pre-wrap;text-align:left;line-height:1.75;"><strong>2、可以使用whoami命令可查看当前用户</strong></div><pre><code class="language-plain">hbase(main):001:0&gt; whoami
a6 (auth:SIMPLE)
    groups: staff, com.apple.sharepoint.group.1, everyone, localaccounts, _appserverusr, admin, _appserveradm, _lpadmin, com.apple.sharepoint.group.2, _appstore, _lpoperator, _developer, _analyticsusers, com.apple.access_ftp, com.apple.access_screensharing, com.apple.access_ssh-disabled</code></pre><span style="font-size:13px;"><strong>3、表管理</strong></span><div style="font-size:14px;white-space:pre-wrap;text-align:left;line-height:1.75;">    1）查看有哪些表</div></div><pre><code class="language-plain">hbase(main):002:0&gt; list
TABLE
test
test001
test010
10 row(s) in 0.4870 seconds</code></pre><p><strong>    2）创建表<br></strong># 语法：create &lt;table&gt;, {NAME =&gt; &lt;family&gt;, VERSIONS =&gt; &lt;VERSIONS&gt;}<br># 例如：创建表t1，有两个family name：f1，f2，且版本数均为2<br>hbase(main)&gt; create 't1',{NAME =&gt; 'f1', VERSIONS =&gt; 2},{NAME =&gt; 'f2', VERSIONS =&gt; 2}<br></p><pre><code class="language-plain">hbase(main):003:0&gt; create 't1',{NAME =&gt; 'f1', VERSIONS =&gt; 2},{NAME =&gt; 'f2', VERSIONS =&gt; 2}
0 row(s) in 1.3840 seconds

=&gt; Hbase::Table - t1</code></pre><p style="text-align:left;"><span style="white-space:pre-wrap;"><strong><span style="font-size:14px;">    3）查看表结构</span></strong></span></p><p style="text-align:left;"><span style="font-size:14px;white-space:pre-wrap;"># 语法：describe &lt;table&gt;<br># 例如：查看表t1的结构<br>hbase(main)&gt; describe 't1'<br></span></p><pre><code class="language-plain">hbase(main):006:0&gt; desc 't1'
Table t1 is ENABLED
t1
COLUMN FAMILIES DESCRIPTION
{NAME =&gt; 'f1', BLOOMFILTER =&gt; 'ROW', VERSIONS =&gt; '2', IN_MEMORY =&gt; 'false', KEEP_DELETED_CELLS =&gt; 'FALSE', DATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; 'FOREVER', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', BLOCKCACHE =&gt; 'true', BLOCKSIZE =&gt; '65536', REPLICATION_SCOPE =&gt; '0'}
{NAME =&gt; 'f2', BLOOMFILTER =&gt; 'ROW', VERSIONS =&gt; '2', IN_MEMORY =&gt; 'false', KEEP_DELETED_CELLS =&gt; 'FALSE', DATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; 'FOREVER', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', BLOCKCACHE =&gt; 'true', BLOCKSIZE =&gt; '65536', REPLICATION_SCOPE =&gt; '0'}
2 row(s) in 0.2250 seconds</code></pre><strong>4）删除表<br></strong>分两步：首先disable，然后drop<br>例如：删除表t1<br>hbase(main)&gt; disable 't1'<br><p>hbase(main)&gt; drop 't1'</p><pre><code class="language-plain">hbase(main):006:0&gt; desc 't1'
Table t1 is ENABLED
t1
COLUMN FAMILIES DESCRIPTION
……
2 row(s) in 0.2250 seconds

hbase(main):007:0&gt; disable 't1'
0 row(s) in 2.3340 seconds

hbase(main):008:0&gt; desc 't1'
Table t1 is DISABLED
t1
COLUMN FAMILIES DESCRIPTION
……
2 row(s) in 0.0350 seconds

hbase(main):009:0&gt; drop 't1'
0 row(s) in 1.3390 seconds

hbase(main):010:0&gt; desc 't1'

ERROR: Unknown table t1!

Here is some help for this command:
Describe the named table. For example:
  hbase&gt; describe 't1'
  hbase&gt; describe 'ns1:t1'

Alternatively, you can use the abbreviated 'desc' for the same thing.
  hbase&gt; desc 't1'
  hbase&gt; desc 'ns1:t1'</code></pre><p style="font-weight:bold;font-size:13px;line-height:1.5;margin:10px auto;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;">5）<span style="background-color:rgb(255,255,255);color:rgb(68,68,68);font-family:Tahoma, Arial, Helvetica, sans-serif;font-size:14px;">清空表</span></p><p style="font-weight:bold;font-size:14px;color:rgb(68,68,68);margin:10px auto;list-style-type:none;list-style-image:none;font-family:Tahoma, Arial, Helvetica, sans-serif;background-color:rgb(255,255,255);">truncate ‘lmj_test’</p><p style="font-weight:bold;font-size:14px;color:rgb(68,68,68);margin:10px auto;list-style-type:none;list-style-image:none;font-family:Tahoma, Arial, Helvetica, sans-serif;background-color:rgb(255,255,255);">6）<span style="font-weight:bold;">表空间：</span></p><span style="color:rgb(68,68,68);font-family:Tahoma, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);"><strong>create_namespace 'name'</strong></span><br style="color:rgb(68,68,68);font-family:Tahoma, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);"><span style="color:rgb(68,68,68);font-family:Tahoma, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);"><strong>drop_namespace 'name'</strong></span><br style="color:rgb(68,68,68);font-family:Tahoma, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);"><span style="color:rgb(68,68,68);font-family:Tahoma, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);"><strong>列出所有表空间：list_namespace</strong></span><br style="color:rgb(68,68,68);font-family:Tahoma, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);"><span style="color:rgb(68,68,68);font-family:Tahoma, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);"><strong>查看表空间：describe_namespace 'name'</strong></span><p style="font-weight:bold;line-height:1.5;margin:10px auto;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;"><span style="font-size:14px;">7）向hbase表插入和获取数据</span></p><p style="font-weight:bold;font-size:14px;color:rgb(68,68,68);margin:10px auto;list-style-type:none;list-style-image:none;font-family:Tahoma, Arial, Helvetica, sans-serif;background-color:rgb(255,255,255);">使用put命令，可以插入行到一个表。它的语法如下：<br>put ’&lt;namespace:table name&gt;’,’row1’,’&lt;colfamily:colname&gt;’,’&lt;value&gt;’<br>(表空间名非必填)</p><p style="font-weight:bold;font-size:14px;color:rgb(68,68,68);margin:10px auto;list-style-type:none;list-style-image:none;font-family:Tahoma, Arial, Helvetica, sans-serif;background-color:rgb(255,255,255);">put 'emp','1','personal data:name','raju'</p><p style="font-weight:bold;font-size:14px;color:rgb(68,68,68);margin:10px auto;list-style-type:none;list-style-image:none;font-family:Tahoma, Arial, Helvetica, sans-serif;background-color:rgb(255,255,255);">使用get命令获取一行数据。它的语法如下：<br>get ’&lt;table name&gt;’,’row1’<br>get 'emp' , '1'</p><p style="font-weight:bold;font-size:14px;color:rgb(68,68,68);margin:10px auto;list-style-type:none;list-style-image:none;font-family:Tahoma, Arial, Helvetica, sans-serif;background-color:rgb(255,255,255);">8）使用get方法读取指定列:<br>hbase&gt;get 'table name', ‘rowid’, {COLUMN =&gt; ‘column family:column name ’}<br>get 'emp', '2', {COLUMN=&gt;'personal data:name'}</p><p style="color:rgb(68,68,68);margin:10px auto;list-style-type:none;list-style-image:none;font-family:Tahoma, Arial, Helvetica, sans-serif;background-color:rgb(255,255,255);"><span style="font-size:14px;"><strong>9）删除指定的数据<br></strong></span><span style="font-size:14px;"><strong>删除一行中所有单元格。下面给出是 deleteall 命令的语法。</strong></span><br><span style="font-size:14px;"><strong>deleteall ‘&lt;table name&gt;’, ‘&lt;row&gt;’</strong></span></p><p style="font-weight:bold;font-size:14px;color:rgb(68,68,68);margin:10px auto;list-style-type:none;list-style-image:none;font-family:Tahoma, Arial, Helvetica, sans-serif;background-color:rgb(255,255,255);">使用 delete 命令，可以在一个表中删除特定单元格。 delete 命令的语法如下：<br>delete ‘&lt;table name&gt;’, ‘&lt;row&gt;’, ‘&lt;column name &gt;’, ‘&lt;time stamp&gt;’</p><p><strong>10）修改表结构</strong></p>修改表结构必须先disable<br># 语法：alter 't1', {NAME =&gt; 'f1'}, {NAME =&gt; 'f2', METHOD =&gt; 'delete'}<br># 例如：修改表test1的cf的TTL为180天<br>hbase(main)&gt; disable 't1'<br>hbase(main)&gt; alter 't1',{NAME=&gt;'body',TTL=&gt;'15552000'},{NAME=&gt;'meta', TTL=&gt;'15552000'}<br><p>hbase(main)&gt; enable 't1'</p><pre><code class="language-plain">hbase(main):014:0&gt; alter 't1',{NAME=&gt;'body',TTL=&gt;'15552000'},{NAME=&gt;'meta', TTL=&gt;'15552000'}
Updating all regions with the new schema...
1/1 regions updated.
Done.
Updating all regions with the new schema...
1/1 regions updated.
Done.
0 row(s) in 3.8910 seconds

hbase(main):015:0&gt; desc 't1'
Table t1 is ENABLED
t1
COLUMN FAMILIES DESCRIPTION
{NAME =&gt; 'body', BLOOMFILTER =&gt; 'ROW', VERSIONS =&gt; '1', IN_MEMORY =&gt; 'false', KEEP_DELETED_CELLS =&gt; 'FALSE', DATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; '15552000 SECONDS (180 DAYS)', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', BLOCKCACHE =&gt; 'true', BLOCKSIZE =&gt; '65536', REPLIC
ATION_SCOPE =&gt; '0'}
{NAME =&gt; 'f1', BLOOMFILTER =&gt; 'ROW', VERSIONS =&gt; '2', IN_MEMORY =&gt; 'false', KEEP_DELETED_CELLS =&gt; 'FALSE', DATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; 'FOREVER', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', BLOCKCACHE =&gt; 'true', BLOCKSIZE =&gt; '65536', REPLICATION_SCOPE =&gt; '0'}
{NAME =&gt; 'f2', BLOOMFILTER =&gt; 'ROW', VERSIONS =&gt; '2', IN_MEMORY =&gt; 'false', KEEP_DELETED_CELLS =&gt; 'FALSE', DATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; 'FOREVER', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', BLOCKCACHE =&gt; 'true', BLOCKSIZE =&gt; '65536', REPLICATION_SCOPE =&gt; '0'}
{NAME =&gt; 'meta', BLOOMFILTER =&gt; 'ROW', VERSIONS =&gt; '1', IN_MEMORY =&gt; 'false', KEEP_DELETED_CELLS =&gt; 'FALSE', DATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; '15552000 SECONDS (180 DAYS)', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', BLOCKCACHE =&gt; 'true', BLOCKSIZE =&gt; '65536', REPLIC
ATION_SCOPE =&gt; '0'}
4 row(s) in 0.0540 seconds</code></pre><p><strong>11）统计表记录数<br>语法：count &lt;table&gt;, {INTERVAL =&gt; intervalNum, CACHE =&gt; cacheNum}<br></strong></p><p>INTERVAL设置多少行显示一次及对应的rowkey，默认1000；CACHE每次去取的缓存区大小，默认是10，调整该参数可提高查询速度</p><pre><code class="language-plain">hbase(main):020:0&gt; count 'User'
3 row(s) in 0.0360 seconds

=&gt; 3</code></pre><strong>4、权限管理</strong><strong>    1）分配权限<br></strong># 语法 : grant &lt;user&gt; &lt;permissions&gt; &lt;table&gt; &lt;column family&gt; &lt;column qualifier&gt; 参数后面用逗号分隔<br># 权限用五个字母表示： "RWXCA".<br># READ('R'), WRITE('W'), EXEC('X'), CREATE('C'), ADMIN('A')<br># 例如，给用户‘test'分配对表t1有读写的权限，<br>hbase(main)&gt; grant 'test','RW','t1'<br><strong>    2）查看权限<br></strong># 语法：user_permission &lt;table&gt;<br># 例如，查看表t1的权限列表<br>hbase(main)&gt; user_permission 't1'<br><strong>    3）收回权限<br></strong># 与分配权限类似，语法：revoke &lt;user&gt; &lt;table&gt; &lt;column family&gt; &lt;column qualifier&gt;<br># 例如，收回test用户在表t1上的权限<br>hbase(main)&gt; revoke 'test','t1'<br><strong>5、表数据的增删改查<br>    1）添加数据<br></strong># 语法：put &lt;table&gt;,&lt;rowkey&gt;,&lt;family:column&gt;,&lt;value&gt;,&lt;timestamp&gt;<br># 例如：给表t1的添加一行记录：rowkey是rowkey001，family name：f1，column name：col1，value：value01，timestamp：系统默认<br>hbase(main)&gt; put 't1','rowkey001','f1:col1','value01'<br>用法比较单一。<br><strong>    2）查询数据<br>        a）查询某行记录<br></strong># 语法：get &lt;table&gt;,&lt;rowkey&gt;,[&lt;family:column&gt;,....]<br># 例如：查询表t1，rowkey001中的f1下的col1的值<br>hbase(main)&gt; get 't1','rowkey001', 'f1:col1'<br># 或者：<br>hbase(main)&gt; get 't1','rowkey001', {COLUMN=&gt;'f1:col1'}<br># 查询表t1，rowke002中的f1下的所有列值<br>hbase(main)&gt; get 't1','rowkey001'<br><strong>        b）扫描表<br></strong># 语法：scan &lt;table&gt;, {COLUMNS =&gt; [ &lt;family:column&gt;,.... ], LIMIT =&gt; num}<br># 另外，还可以添加STARTROW、TIMERANGE和FITLER等高级功能<br># 例如：扫描表t1的前5条数据<br>hbase(main)&gt; scan 't1',{LIMIT=&gt;5}<br><strong>        c）查询表中的数据行数<br></strong># 语法：count &lt;table&gt;, {INTERVAL =&gt; intervalNum, CACHE =&gt; cacheNum}<br># INTERVAL设置多少行显示一次及对应的rowkey，默认1000；CACHE每次去取的缓存区大小，默认是10，调整该参数可提高查询速度<br># 例如，查询表t1中的行数，每100条显示一次，缓存区为500<br><p>hbase(main)&gt; count 't1', {INTERVAL =&gt; 100, CACHE =&gt; 500}</p><p></p><p><span style="font-weight:700;">        d）插入数据</span></p><p><span style="font-weight:700;"></span></p><p style="margin:10px auto;color:rgb(0,0,0);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:15px;text-align:left;background-color:rgb(255,255,255);">语法：put &lt;table&gt;,&lt;rowkey&gt;,&lt;family:column&gt;,&lt;value&gt;<br></p><pre><code class="language-plain">hbase(main):005:0&gt; put 'User', 'row1', 'info:name', 'xiaoming'
0 row(s) in 0.1200 seconds

hbase(main):006:0&gt; put 'User', 'row2', 'info:age', '18'
0 row(s) in 0.0170 seconds

hbase(main):007:0&gt; put 'User', 'row3', 'info:sex', 'man'
0 row(s) in 0.0030 seconds</code></pre><p><span style="font-weight:700;">        e）范围查询</span></p><pre><code class="language-plain">hbase(main):011:0&gt; scan 'User', {STARTROW =&gt; 'row2'}
ROW                                     COLUMN+CELL
 row2                                   column=info:age, timestamp=1502368069926, value=18
 row3                                   column=info:sex, timestamp=1502368093636, value=man
2 row(s) in 0.0170 seconds
hbase(main):012:0&gt; scan 'User', {STARTROW =&gt; 'row2', ENDROW =&gt; 'row2'}
ROW                                     COLUMN+CELL
 row2                                   column=info:age, timestamp=1502368069926, value=18
1 row(s) in 0.0110 seconds

hbase(main):013:0&gt; scan 'User', {STARTROW =&gt; 'row2', ENDROW =&gt; 'row3'}
ROW                                     COLUMN+CELL
 row2                                   column=info:age, timestamp=1502368069926, value=18
1 row(s) in 0.0120 seconds</code></pre><span style="font-weight:bold;">3）删除数据</span><strong>    a)删除行中的某个列值<br></strong># 语法：delete &lt;table&gt;, &lt;rowkey&gt;,  &lt;family:column&gt; , &lt;timestamp&gt;,必须指定列名<br># 例如：删除表t1，rowkey001中的f1:col1的数据<br>hbase(main)&gt; delete 't1','rowkey001','f1:col1'<br>注：将删除改行f1:col1列所有版本的数据<br><strong>    b)删除行<br></strong># 语法：deleteall &lt;table&gt;, &lt;rowkey&gt;,  &lt;family:column&gt; , &lt;timestamp&gt;，可以不指定列名，删除整行数据<br># 例如：删除表t1，rowk001的数据<br>hbase(main)&gt; deleteall 't1','rowkey001'<br><strong>   c）删除表中的所有数据<br></strong># 语法： truncate &lt;table&gt;<br># 其具体过程是：disable table -&gt; drop table -&gt; create table<br># 例如：删除表t1的所有数据<br>hbase(main)&gt; truncate 't1'<br><strong>6、Region管理<br>    1）移动region<br></strong># 语法：move 'encodeRegionName', 'ServerName'<br># encodeRegionName指的regioName后面的编码，ServerName指的是master-status的Region Servers列表<br># 示例<br>hbase(main)&gt;move '4343995a58be8e5bbc739af1e91cd72d', 'db-41.xxx.xxx.org,60020,1390274516739'<br><strong>    2）开启/关闭region<br></strong># 语法：balance_switch true|false<br>hbase(main)&gt; balance_switch<br><strong>    3）手动split<br></strong># 语法：split 'regionName', 'splitKey'<br><strong>    4）手动触发major compaction<br></strong>#语法：<br>#Compact all regions in a table:<br>#hbase&gt; major_compact 't1'<br>#Compact an entire region:<br>#hbase&gt; major_compact 'r1'<br>#Compact a single column family within a region:<br>#hbase&gt; major_compact 'r1', 'c1'<br>#Compact a single column family within a table:<br>#hbase&gt; major_compact 't1', 'c1'<br><strong>7、配置管理及节点重启<br>    1）修改hdfs配置<br></strong>hdfs配置位置：/etc/hadoop/conf<br># 同步hdfs配置<br>cat /home/hadoop/slaves|xargs -i -t scp /etc/hadoop/conf/hdfs-site.xml hadoop@{}:/etc/hadoop/conf/hdfs-site.xml<br>#关闭：<br>cat /home/hadoop/slaves|xargs -i -t ssh hadoop@{} "sudo /home/hadoop/cdh4/hadoop-2.0.0-cdh4.2.1/sbin/hadoop-daemon.sh --config /etc/hadoop/conf stop datanode"<br>#启动：<br>cat /home/hadoop/slaves|xargs -i -t ssh hadoop@{} "sudo /home/hadoop/cdh4/hadoop-2.0.0-cdh4.2.1/sbin/hadoop-daemon.sh --config /etc/hadoop/conf start datanode"<br><strong>    2）修改hbase配置<br></strong>hbase配置位置：<br><strong>    # 同步hbase配置<br></strong>cat /home/hadoop/hbase/conf/regionservers|xargs -i -t scp /home/hadoop/hbase/conf/hbase-site.xml hadoop@{}:/home/hadoop/hbase/conf/hbase-site.xml<br><strong>    # graceful重启<br></strong>cd ~/hbase<br><p>bin/graceful_stop.sh --restart --reload --debug inspurXXX.xxx.xxx.org</p>            </div>
                </div>