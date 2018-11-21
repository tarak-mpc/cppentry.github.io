---
layout:     post
title:      Hbase基本shell操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/aryoyo/article/details/81136318				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>环境：Habse version 1.2.6.1 on redhat6.4 64bit</p>

<p>1.进入hbase命令行： ./hbase shell</p>

<pre class="has">
<code>[root@rhel64-64bit bin]# ./hbase shell
HBase Shell; enter 'help&lt;RETURN&gt;' for list of supported commands.
Type "exit&lt;RETURN&gt;" to leave the HBase Shell
Version 1.2.6.1, rUnknown, Sun Jun  3 23:19:26 CDT 2018

hbase(main):001:0&gt; </code></pre>

<p>2.hbase help帮助命令:<br>
help          --- 获取所有命令提示<br>
help 'ddl'   ---获取一组命令的提示<br>
help 'put'   ---获取某个单独命令的提示帮助</p>

<p>3.general command</p>

<pre class="has">
<code>hbase(main):007:0&gt; status   ---查看服务器状态
1 active master, 0 backup masters, 0 servers, 1 dead, NaN average load

hbase(main):008:0&gt; version  ---查看版本
1.2.6.1, rUnknown, Sun Jun  3 23:19:26 CDT 2018</code></pre>

<p>4.DDL command对表进行操作</p>

<pre class="has">
<code>1》create 创建表
create 'test2',{NAME=&gt;'info',VERSIONS=&gt;3 },{NAME=&gt;'info2',VERSIONS=&gt;1 }
hbase(main):013:0&gt; create 'test2', 'info'
0 row(s) in 1.3700 seconds

=&gt; Hbase::Table - test2

2》list 列出表
hbase(main):014:0&gt; list
TABLE                                                                                                                                                                 
test                                                                                                                                                                  
test2                                                                                                                                                                 
2 row(s) in 0.0190 seconds

=&gt; ["test", "test2"]

3》describe  查看表结构信息
hbase(main):015:0&gt; describe 'test2'
Table test2 is ENABLED                                                                                                                                                
test2                                                                                                                                                                 
COLUMN FAMILIES DESCRIPTION                                                                                                                                           
{NAME =&gt; 'info', BLOOMFILTER =&gt; 'ROW', VERSIONS =&gt; '1', IN_MEMORY =&gt; 'false', KEEP_DELETED_CELLS =&gt; 'FALSE', DATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; 'FOREVER', COMPRESS
ION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', BLOCKCACHE =&gt; 'true', BLOCKSIZE =&gt; '65536', REPLICATION_SCOPE =&gt; '0'}                                                             
1 row(s) in 0.1630 seconds

4》alter 修改表结构
alter 'test2', NAME =&gt; 'info2'    ----增加info2列簇
alter 'test2', {NAME =&gt; 'info2', METHOD =&gt; 'delete'}   ---删除info2列簇
alter 'test2', {NAME=&gt;'info',VERSIONS=&gt;3}    ---修改column family保留版本数

5》disable,drop 先disable表再能删除表
hbase(main):022:0&gt; disable 'test'
0 row(s) in 2.3720 seconds

hbase(main):023:0&gt; drop 'test'
0 row(s) in 1.3350 seconds

hbase(main):024:0&gt; list
TABLE                                                                                                                                                                 
test2                                                                                                                                                                 
1 row(s) in 0.0130 seconds
=&gt; ["test2"]</code></pre>

<p>5.DML comand 对表数据进行操作</p>

<pre class="has">
<code>1》put 添加数据
put 'test2', 'user1', 'info:name', 'yo1'
put 'test2', 'user2', 'info:name', 'yo2'
put 'test2', 'user1', 'info:age', '18'
put 'test2', 'user1', 'info:score', '96'

hbase(main):028:0&gt; put 'test2', 'user1', 'info:score', '96'
0 row(s) in 0.0100 seconds

2》scan 查看表数据
hbase(main):029:0&gt; scan 'test2'
ROW                                         COLUMN+CELL                                                                                                                 
 user1                                      column=info:age, timestamp=1532076813178, value=18                                                                          
 user1                                      column=info:name, timestamp=1532076778014, value=yo1                                                                        
 user1                                      column=info:score, timestamp=1532076822923, value=96                                                                        
 user2                                      column=info:name, timestamp=1532076804216, value=yo2                                                                        
2 row(s) in 0.0620 seconds

hbase(main):032:0&gt; scan 'test2',{COLUMNS =&gt;'info:name' } ---只查看某一列
ROW                                         COLUMN+CELL                                                                                                                 
 user1                                      column=info:name, timestamp=1532076778014, value=yo1                                                                        
 user2                                      column=info:name, timestamp=1532076804216, value=yo2                                                                        
2 row(s) in 0.0270 seconds

alter 'test2', {NAME=&gt;'info',VERSIONS=&gt;3} 
scan 'test2',{COLUMNS =&gt;'info:age', VERSIONS =&gt; 3 }
---查看列簇的所有版本信息
hbase(main):039:0&gt; scan 'test2',{COLUMNS =&gt;'info', VERSIONS =&gt; 3 }  
ROW                                         COLUMN+CELL                                                                                                                 
 user1                                      column=info:age, timestamp=1532077259255, value=22                                                                          
 user1                                      column=info:age, timestamp=1532077253480, value=20                                                                          
 user1                                      column=info:age, timestamp=1532076813178, value=18                                                                          
 user1                                      column=info:name, timestamp=1532076778014, value=yo1                                                                        
 user1                                      column=info:score, timestamp=1532076822923, value=96                                                                        
 user2                                      column=info:name, timestamp=1532076804216, value=yo2                                                                        
2 row(s) in 0.0120 seconds


3》get 查看某个rowkey的数据
base(main):033:0&gt; get 'test2','user1'
COLUMN                                      CELL                                                                                                                        
 info:age                                   timestamp=1532076813178, value=18                                                                                           
 info:name                                  timestamp=1532076778014, value=yo1                                                                                          
 info:score                                 timestamp=1532076822923, value=96                                                                                           
3 row(s) in 0.0790 seconds

hbase(main):034:0&gt; get 'test2','user1','info:age'
COLUMN                                      CELL                                                                                                                        
 info:age                                   timestamp=1532076813178, value=18                                                                                           
1 row(s) in 0.0070 seconds

4》delete 删除表数据
delete &lt;table&gt;,&lt;rowkey&gt;,  &lt;family:column&gt; , &lt;timestamp&gt;,
delete 'test2','user1','info:age',1532077872555

----删除最老的一个版本时间戳为1532077872555，如果删除的不是最老版本，
假如是第二的版本，那么scan则不能看到比删除版本老的版本
具体见help 'delete'说明： When scanning, a delete cell suppresses older versions

hbase(main):007:0&gt; delete 'test2','user1','info:age',1532077872555   
0 row(s) in 0.0350 seconds

hbase(main):008:0&gt; scan 'test2',{COLUMNS =&gt;'info', VERSIONS =&gt; 3 }
ROW                                         COLUMN+CELL                                                                                                                 
 user1                                      column=info:age, timestamp=1532078108418, value=19                                                                          
 user1                                      column=info:age, timestamp=1532078105258, value=18                                                                          
 user1                                      column=info:name, timestamp=1532076778014, value=yo1                                                                        
 user1                                      column=info:score, timestamp=1532076822923, value=96                                                                        
 user2                                      column=info:name, timestamp=1532076804216, value=yo2  

5》truncate
hbase(main):009:0&gt; truncate 'test2'
Truncating 'test2' table (it may take a while):
 - Disabling table...
 - Truncating table...
0 row(s) in 3.9790 seconds

hbase(main):010:0&gt; scan 'test2'
ROW                                         COLUMN+CELL                                                                                                                 
0 row(s) in 0.1550 seconds

hbase(main):011:0&gt; </code></pre>

<p> </p>            </div>
                </div>