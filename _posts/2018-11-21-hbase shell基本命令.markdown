---
layout:     post
title:      hbase shell基本命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>1.进入hbase shell</p>
<p>hadoop@namenode:~/hbase-0.98.8-hadoop1/conf$ hbase shell<br>
HBase Shell; enter 'help&lt;RETURN&gt;' for list of supported commands.<br>
Type "exit&lt;RETURN&gt;" to leave the HBase Shell<br>
Version 0.98.8-hadoop1, r6cfc8d064754251365e070a10a82eb169956d5fe, Fri Nov 14 17:14:19 PST 2014<br>
hbase(main):001:0&gt; <br></p>
<p><br></p>
<p>hbase不适合复杂的查询</p>
<p><br></p>
<p>2.以下是操作</p>
<p>hbase(main):007:0&gt; create 'user','age','name','gender','address'<br>
0 row(s) in 0.7400 seconds<br>
=&gt; Hbase::Table - user</p>
<p><br></p>
<p><br>
hbase(main):008:0&gt; list<br>
TABLE                                                                                                                        <br>
uesr                                                                                                                         <br>
user                                                                                                                         <br>
2 row(s) in 0.0080 seconds<br></p>
<p><br></p>
<p>hbase(main):009:0&gt; describe 'user'<br>
Table user is ENABLED                                                                                                        <br>
COLUMN FAMILIES DESCRIPTION                                                                                                  <br>
{NAME =&gt; 'address', DATA_BLOCK_ENCODING =&gt; 'NONE', BLOOMFILTER =&gt; 'ROW', REPLICATION_SCOPE =&gt; '0', VERSIONS =&gt; '1', COMPRESSI<br>
ON =&gt; 'NONE', MIN_VERSIONS =&gt; '0', TTL =&gt; 'FOREVER', KEEP_DELETED_CELLS =&gt; 'FALSE', BLOCKSIZE =&gt; '65536', IN_MEMORY =&gt; 'false<br>
', BLOCKCACHE =&gt; 'true'}                                                                                                     <br>
{NAME =&gt; 'age', DATA_BLOCK_ENCODING =&gt; 'NONE', BLOOMFILTER =&gt; 'ROW', REPLICATION_SCOPE =&gt; '0', VERSIONS =&gt; '1', COMPRESSION =<br>
&gt; 'NONE', MIN_VERSIONS =&gt; '0', TTL =&gt; 'FOREVER', KEEP_DELETED_CELLS =&gt; 'FALSE', BLOCKSIZE =&gt; '65536', IN_MEMORY =&gt; 'false', B<br>
LOCKCACHE =&gt; 'true'}                                                                                                         <br>
{NAME =&gt; 'gender', DATA_BLOCK_ENCODING =&gt; 'NONE', BLOOMFILTER =&gt; 'ROW', REPLICATION_SCOPE =&gt; '0', VERSIONS =&gt; '1', COMPRESSIO<br>
N =&gt; 'NONE', MIN_VERSIONS =&gt; '0', TTL =&gt; 'FOREVER', KEEP_DELETED_CELLS =&gt; 'FALSE', BLOCKSIZE =&gt; '65536', IN_MEMORY =&gt; 'false'<br>
, BLOCKCACHE =&gt; 'true'}                                                                                                      <br>
{NAME =&gt; 'name', DATA_BLOCK_ENCODING =&gt; 'NONE', BLOOMFILTER =&gt; 'ROW', REPLICATION_SCOPE =&gt; '0', VERSIONS =&gt; '1', COMPRESSION <br>
=&gt; 'NONE', MIN_VERSIONS =&gt; '0', TTL =&gt; 'FOREVER', KEEP_DELETED_CELLS =&gt; 'FALSE', BLOCKSIZE =&gt; '65536', IN_MEMORY =&gt; 'false', <br>
BLOCKCACHE =&gt; 'true'}                                                                                                        <br>
4 row(s) in 0.0580 seconds<br></p>
<p><br></p>
<p><br></p>
<p>删除表之前必须先disable掉表</p>
<p>hbase(main):010:0&gt; disable 'uesr'<br>
0 row(s) in 1.3560 seconds<br></p>
<p>hbase(main):012:0&gt; drop 'uesr'<br>
0 row(s) in 0.2450 seconds<br></p>
<p>hbase(main):013:0&gt; list<br>
TABLE                                                                                                                        <br>
user                                                                                                                         <br>
1 row(s) in 0.0080 seconds<br></p>
<p><br></p>
<p><br></p>
<p>新增数据（‘表名’，‘row key’,'列族:列','值'）</p>
<p>hbase(main):016:0*&gt; put 'user','xiaoming','address:city','newYork'<br>
0 row(s) in 0.0250 seconds<br></p>
<p><br></p>
<p>查询数据（‘表名’，‘row key’）</p>
<p>hbase(main):002:0&gt; get 'user','xiaoming'<br>
COLUMN                           CELL                                                                                        <br>
 address:city                    timestamp=1417013862751, value=newYork                                                      <br>
 age:real                        timestamp=1417273133703, value=14          <br></p>
<p><br></p>
<p>hbase(main):003:0&gt; get 'user','xiaoming','address'<br>
COLUMN                           CELL                                                                                        <br>
 address:city                    timestamp=1417013862751, value=newYork        </p>
<p><br></p>
<p>hbase(main):003:0&gt; get 'user','xiaoming','address:city'<br>
COLUMN                           CELL                                                                                        <br>
 address:city                    timestamp=1417013862751, value=newYork          <br></p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20141129231516413?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDQxNDA2Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20141129231759015?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDQxNDA2Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""> <br></p>
<p><br></p>
<p>hbase(main):011:0&gt; scan 'user'<br>
ROW                                   COLUMN+CELL                                                                                                 <br>
 xiaoming                             column=address:city, timestamp=1417013862751, value=newYork                                                 <br>
 xiaoming                             column=age:real, timestamp=1417273547325, value=16            <br></p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20141129232122093?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDQxNDA2Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><img src="https://img-blog.csdn.net/20141129232335031?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDQxNDA2Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><img src="https://img-blog.csdn.net/20141129232432257?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDQxNDA2Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><img src="https://img-blog.csdn.net/20141129232512365?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDQxNDA2Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
            </div>
                </div>