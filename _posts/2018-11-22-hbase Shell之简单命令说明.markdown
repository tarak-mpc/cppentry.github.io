---
layout:     post
title:      hbase Shell之简单命令说明
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>[hadoop@master hbase]$ bin/hbase shell</p>
<p>进入shell</p>
<p><br></p>
<p>hbase(main):001:0&gt; help</p>
<p>******（Shell命令说明）<br></p>
<p><br></p>
<p>hbase(main):002:0&gt; status</p>
<p>1 servers, 0 dead, 4.0000 average load （服务器，dead，负荷）<br></p>
<p><br></p>
<p>hbase(main):003:0&gt; version</p>
<p>0.94.2, r1395367, Sun Oct  7 19:11:01 UTC 2012 （版本）</p>
<p><br></p>
<p>hbase(main):001:0&gt; create 'member','member_id','address','info'</p>
<p>0 row(s) in 5.3700 seconds （创建表，成功）</p>
<p><br></p>
<p>hbase(main):002:0&gt; list<br>
TABLE                                                                                          <br>
htest                                                                                          <br>
member                                                                                         <br>
user                                                                                           <br>
3 row(s) in 0.3400 seconds （查看TABLE信息）</p>
<p><br></p>
<p>hbase(main):003:0&gt; describe 'member'<br>
DESCRIPTION                                                   ENABLED                          <br>
 {NAME =&gt; 'member', FAMILIES =&gt; [{NAME =&gt; 'address', DATA_BLO true                             <br>
 CK_ENCODING =&gt; 'NONE', BLOOMFILTER =&gt; 'NONE', REPLICATION_SC                                  <br>
 OPE =&gt; '0', VERSIONS =&gt; '3', COMPRESSION =&gt; 'NONE', MIN_VERS                                  <br>
 IONS =&gt; '0', TTL =&gt; '2147483647', KEEP_DELETED_CELLS =&gt; 'fal                                  <br>
 se', BLOCKSIZE =&gt; '65536', IN_MEMORY =&gt; 'false', ENCODE_ON_D                                  <br>
 ISK =&gt; 'true', BLOCKCACHE =&gt; 'true'}, {NAME =&gt; 'info', DATA_                                  <br>
 BLOCK_ENCODING =&gt; 'NONE', BLOOMFILTER =&gt; 'NONE', REPLICATION                                  <br>
 _SCOPE =&gt; '0', VERSIONS =&gt; '3', COMPRESSION =&gt; 'NONE', MIN_V                                  <br>
 ERSIONS =&gt; '0', TTL =&gt; '2147483647', KEEP_DELETED_CELLS =&gt; '                                  <br>
 false', BLOCKSIZE =&gt; '65536', IN_MEMORY =&gt; 'false', ENCODE_O                                  <br>
 N_DISK =&gt; 'true', BLOCKCACHE =&gt; 'true'}, {NAME =&gt; 'member_id                                  <br>
 ', DATA_BLOCK_ENCODING =&gt; 'NONE', BLOOMFILTER =&gt; 'NONE', REP                                  <br>
 LICATION_SCOPE =&gt; '0', VERSIONS =&gt; '3', COMPRESSION =&gt; 'NONE                                  <br>
 ', MIN_VERSIONS =&gt; '0', TTL =&gt; '2147483647', KEEP_DELETED_CE                                  <br>
 LLS =&gt; 'false', BLOCKSIZE =&gt; '65536', IN_MEMORY =&gt; 'false',                                   <br>
 ENCODE_ON_DISK =&gt; 'true', BLOCKCACHE =&gt; 'true'}]}                                             <br>
1 row(s) in 0.0340 seconds   （查看表）<br></p>
<p><br></p>
<p>hbase(main):002:0&gt; alter 'member',{NAME=&gt;'member_id',METHOD=&gt;'delete'}<br><span style="color:#FF0000;">ERROR: org.apache.hadoop.hbase.TableNotDisabledException: org.apache.hadoop.hbase.TableNotDisabledException: member</span></p>
<p>（不可直接修改，必须先把表离线，然后修改）</p>
<p><br></p>
<p>hbase(main):003:0&gt; disable 'member'<br>
0 row(s) in 2.2260 seconds （disable表，先设为不可用）</p>
<p></p>
hbase(main):004:0&gt; alter 'member',{NAME=&gt;'member_id',METHOD=&gt;'delete'}<br>
Updating all regions with the new schema...<br>
1/1 regions updated.<br>
Done.<br>
0 row(s) in 2.5010 seconds  （修改成功）<br><p><br></p>
<p>hbase(main):005:0&gt; enable 'member'<br>
0 row(s) in 2.2380 seconds   （然后启动表）</p>
<p><br></p>
<p>hbase(main):009:0&gt; disable 'member'<br>
0 row(s) in 2.1470 seconds  （删除表也需要先把表设为不可用）</p>
<p><br></p>
<p>hbase(main):010:0&gt; drop 'member'<br>
0 row(s) in 1.3690 seconds  （删除成功）</p>
<p><br></p>
<p>hbase(main):011:0&gt; exists 'member'<br>
Table member does not exist                                                                    <br>
0 row(s) in 0.1170 seconds   （查看是否存在表）</p>
<p><br></p>
<p>hbase(main):015:0&gt; is_enabled 'member'<br>
true                                                                                           <br>
0 row(s) in 0.0030 seconds   （查看表是否可用）</p>
<p><br></p>
<p>hbase(main):016:0&gt; is_disabled 'member'<br>
false                                                                                          <br>
0 row(s) in 0.0040 seconds  （查看表是否不可用）</p>
<p><br></p>
<p>（插入记录）<br></p>
<p>hbase(main):017:0&gt; put 'member','scutshuxue','info:age','24'</p>
<p>hbase(main):018:0&gt; put 'member','scutshuxue','info:birthday','1987-06-17'</p>
<p>hbase(main):020:0&gt; put 'member','scutshuxue','info:company','alibaba'</p>
<p>hbase(main):025:0&gt; put 'member','scutshuxue','address:province','zhejiang'<br><br>
hbase(main):026:0&gt; put 'member','scutshuxue','address:city','hangzhou'<br><br>
hbase(main):027:0&gt; put 'member','xiaofeng','info:birthday','1987-4-17'<br><br>
hbase(main):028:0&gt; put 'member','xiaofeng','info:company','alibaba'<br><br>
hbase(main):029:0&gt; put 'member','xiaofeng','address:contry','china'<br><br>
hbase(main):030:0&gt; put 'member','xiaofeng','address:province','guangdong'<br><br>
hbase(main):031:0&gt; put 'member','xiaofeng','address:city','jieyang'<br><br>
hbase(main):032:0&gt; put 'member','xiaofeng','address:town','xianqiao'</p>
<p><br></p>
<p>（查询记录：行键查询）</p>
<p>hbase(main):001:0&gt; get 'member','scutshuxue'<br>
COLUMN                   CELL                                                                  <br>
 address:city            timestamp=1365791240949, value=hangzhou                               <br>
 address:province        timestamp=1365791217245, value=zhejiang                               <br>
 info:age                timestamp=1365790872766, value=24                                     <br>
 info:birthday           timestamp=1365790910608, value=1987-06-17                             <br>
 info:company            timestamp=1365790948776, value=alibaba                                <br>
5 row(s) in 2.8340 seconds</p>
<p><br></p>
<p>（查询记录：行键、一个列簇的所有信息）<br></p>
<p>hbase(main):002:0&gt; get 'member','scutshuxue','info'<br>
COLUMN                   CELL                                                                  <br>
 info:age                timestamp=1365790872766, value=24                                     <br>
 info:birthday           timestamp=1365790910608, value=1987-06-17                             <br>
 info:company            timestamp=1365790948776, value=alibaba                                <br>
3 row(s) in 0.0220 seconds</p>
<p><br></p>
<p>（查询记录：行键、列簇的某列信息）<br></p>
<p>hbase(main):003:0&gt; get 'member','scutshuxue','info:age'<br>
COLUMN                   CELL                                                                  <br>
 info:age                timestamp=1365790872766, value=24                                     <br>
1 row(s) in 0.0130 seconds</p>
<p><br></p>
<p>（更新一条记录）<br></p>
<p>hbase(main):004:0&gt; put 'member','scutshuxue','info:age','99'<br>
0 row(s) in 0.0940 seconds<br><br>
hbase(main):005:0&gt; get'member','scutshuxue','info:age'<br>
COLUMN                   CELL                                                                  <br>
 info:age                timestamp=1365792994491, value=99                                     <br>
1 row(s) in 0.0170 seconds</p>
<p><br></p>
<p>（通过时间戳查询记录）<br></p>
<p>hbase(main):008:0&gt; get'member','scutshuxue',{COLUMN=&gt;'info:age',TIMESTAMP=&gt;1365792994491}<br>
COLUMN                   CELL                                                                  <br>
 info:age                timestamp=1365792994491, value=99                                     <br>
1 row(s) in 0.0110 seconds</p>
<p><br></p>
<p>（全表扫描）<br></p>
<p>hbase(main):009:0&gt; scan 'member'<br>
ROW                      COLUMN+CELL                                                           <br>
 scutshuxue              column=address:city, timestamp=1365791240949, value=hangzhou          <br>
 scutshuxue              column=address:province, timestamp=1365791217245, value=zhejiang      <br>
 scutshuxue              column=info:age, timestamp=1365792994491, value=99                    <br>
 scutshuxue              column=info:birthday, timestamp=1365790910608, value=1987-06-17       <br>
 scutshuxue              column=info:company, timestamp=1365790948776, value=alibaba           <br>
 xiaofeng                column=address:city, timestamp=1365791360361, value=jieyang           <br>
 xiaofeng                column=address:contry, timestamp=1365791327027, value=china           <br>
 xiaofeng                column=address:province, timestamp=1365791339666, value=guangdong     <br>
 xiaofeng                column=address:town, timestamp=1365791380287, value=xianqiao          <br>
 xiaofeng                column=info:birthday, timestamp=1365791274037, value=1987-4-17        <br>
 xiaofeng                column=info:company, timestamp=1365791292933, value=alibaba           <br>
2 row(s) in 0.1540 seconds</p>
<p><br></p>
<p>（删除制定行键的字段）<br>
hbase(main):010:0&gt; delete 'member','temp','info:age'<br>
0 row(s) in 0.0500 seconds<br><br>
hbase(main):011:0&gt; get'member','temp'<br>
COLUMN                   CELL                                                                  <br>
0 row(s) in 0.0050 seconds</p>
<p><br></p>
<p>（删除整行）<br></p>
<p>hbase(main):013:0&gt; deleteall'member','xiaofeng'<br>
0 row(s) in 0.0070 seconds</p>
<p><br></p>
<p>（查询行）<br></p>
<p>hbase(main):014:0&gt; count'member'<br>
1 row(s) in 0.0670 seconds</p>
<p><br></p>
<p>（清空表）<br></p>
<p>hbase(main):015:0&gt; truncate'member'<br>
Truncating 'member' table (it may take a while):<br>
 - Disabling table...<br>
 - Dropping table...<br>
 - Creating table...<br>
0 row(s) in 3.8280 seconds</p>
<p><br></p>
<p>Hbase API</p>
<p>《Hbase权威指南》第3-5章<br></p>
<p>http://hbase.apache.org/apidocs/index.html<br></p>
            </div>
                </div>