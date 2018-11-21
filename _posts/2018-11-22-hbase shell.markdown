---
layout:     post
title:      hbase shell
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
1.进入HBase Shell 命令：</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
<span style="line-height:1.5;color:rgb(0,102,0);">$ ${HBASE_HOME}/bin/hbase shell</span></p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
 </p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
2.获得命令列表：</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
<span style="line-height:1.5;color:rgb(0,102,0);">hbase&gt; help</span></p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
 </p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
3.alter： </p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
1) 表't1'中，增加或修改一个column family=&gt; 'f1'并保持其最大版本数为5：</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
<span style="line-height:1.5;color:rgb(0,102,0);">hbase&gt; alter 't1', {NAME =&gt; 'f1', VERSIONS =&gt; 5}</span></p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
2)删除表't1'中值为'f1'的column family：</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
<span style="line-height:1.5;color:rgb(0,102,0);">hbase&gt; alter 't1', {NAME =&gt; 'f1', METHOD =&gt; 'delete'}</span></p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
3)你也可以修改表属性，比如：MAX_FILESIZE，MEMSTORE_FLUSHSIZE和READONLY：</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
<span style="line-height:1.5;color:rgb(0,102,0);">hbase&gt; alter 't1', {METHOD =&gt; 'table_att', MAX_FILESIZE =&gt; '134217728'}</span></p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
 </p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
4.count：</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
返回表的全部行数，这种操作会花费很长时间（run counting mapreduce job），默认每1000行显示一次，当然这个是可以自己配置的。</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
<span style="line-height:1.5;"><span style="line-height:1.5;color:rgb(0,102,0);">hbase&gt; count 't1'<br></span></span><span style="line-height:1.5;color:rgb(0,102,0);">hbase&gt; count 't1', 100000</span></p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
<span style="line-height:1.5;"><span style="line-height:1.5;color:rgb(0,102,0);"> </span></span></p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
5.create：</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
<span style="line-height:1.5;color:rgb(0,102,0);">hbase&gt; create 't1', {NAME =&gt; 'f1', VERSIONS =&gt; 5}</span><br><span style="line-height:1.5;color:rgb(0,102,0);">hbase&gt; create 't1', {NAME =&gt; 'f1'}, {NAME =&gt; 'f2'}, {NAME =&gt; 'f3'}</span><br><span style="line-height:1.5;color:rgb(0,102,0);">hbase&gt; # The above in shorthand would be the following:</span><br><span style="line-height:1.5;color:rgb(0,102,0);">hbase&gt; create 't1', 'f1', 'f2', 'f3'</span><br><span style="line-height:1.5;color:rgb(0,102,0);">hbase&gt; create 't1', {NAME =&gt; 'f1', VERSIONS =&gt; 1, TTL =&gt; 2592000, \</span></p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
<span style="line-height:1.5;color:rgb(0,102,0);">             BLOCKCACHE =&gt; true}</span></p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
TTL为此column family的生命周期，单位为秒。</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
 </p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
6.describe：</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
获得表的属性。</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
<span style="line-height:1.5;"><span style="line-height:1.5;color:rgb(0,102,0);">hbase&gt; describe 't1'</span></span></p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
<span style="line-height:1.5;"><span style="line-height:1.5;color:rgb(0,102,0);"> </span></span></p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
7.delete：</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
删除一个cell，需要用timestamp定位。</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
<span style="line-height:1.5;"><span style="line-height:1.5;color:rgb(0,102,0);">hbase&gt; delete 't1', 'r1', 'f1:c1', ts1</span></span></p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
<span style="line-height:1.5;"><span style="line-height:1.5;color:rgb(0,102,0);"> </span></span></p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
8.deleteall</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
根据指定条件批量删除cell。</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
<span style="line-height:1.5;color:rgb(0,102,0);">hbase&gt; deleteall 't1', 'r1'<br>
hbase&gt; deleteall 't1', 'r1', 'f1:c1'<br>
hbase&gt; deleteall 't1', 'r1', 'f1:c1', ts1</span></p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
<span style="line-height:1.5;color:rgb(0,102,0);"> </span></p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
9.disable</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
使一个指定表失效。</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
<span style="line-height:1.5;"><span style="line-height:1.5;color:rgb(0,102,0);">hbase&gt; exists 't1'</span></span></p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
<span style="line-height:1.5;"><span style="line-height:1.5;color:rgb(0,102,0);"> </span></span></p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
10.drop</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
删除一张表，在删除之前必须先disable此表，如果这个表有多个region，先运行：</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
<span style="line-height:1.5;"><span style="line-height:1.5;color:rgb(0,102,0);">hbase&gt; major_compact ".META."</span></span></p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
<span style="line-height:1.5;"> </span></p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
11.enable</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
与disable相对应。</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
 </p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
12.exists</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
判断表是否存在。</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
<span style="line-height:1.5;"><span style="line-height:1.5;color:rgb(0,102,0);">hbase&gt; exists 't1'</span></span></p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
<span style="line-height:1.5;"> </span></p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
13.exit</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
离开hbase shell。</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
 </p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
14.get</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
根据指定条件返回查询结果，必须指定row。</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
<span style="line-height:1.5;"><span style="line-height:1.5;color:rgb(0,102,0);">hbase&gt; get 't1', 'r1'hbase&gt; get 't1', 'r1', {COLUMN =&gt; 'f1:c1'}hbase&gt; get 't1', 'r1', {COLUMN =&gt; ['f1:c1', 'f1:c2',
 'f1:c3']}hbase&gt; get 't1', 'r1', {COLUMN =&gt; 'f1:c1', TIMESTAMP =&gt; ts1}hbase&gt; get 't1', 'r1', {COLUMN =&gt; 'f1:c1', TIMESTAMP =&gt; ts1, \       VERSIONS =&gt; 4}</span></span></p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
<span style="line-height:1.5;"><span style="line-height:1.5;color:rgb(0,102,0);"> </span></span></p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
15.list</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
获得hbase中所有表名。</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
<br></p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
16.put</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
新增一个cell。</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
<span style="line-height:1.5;color:rgb(0,102,0);">hbase&gt; put 't1', 'r1', 'f1:c1', 'value', ts1</span></p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
 </p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
17.tools</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
获得server端的一些操作命令，如close_regin，flush等，如以后遇到，会另作具体详解。</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
 </p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
18.scan</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
根据指定条件扫描指定表，条件包括LIMIT，STARTROW，STOPROW，TIMESTAMP，COLUMNS。如果在COLUMNS中未指定具体column family则会扫描全部column family。</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
<span style="line-height:1.5;"><span style="line-height:1.5;color:rgb(0,102,0);">hbase&gt; scan '.META.'hbase&gt; scan '.META.', {COLUMNS =&gt; 'info:regioninfo'}hbase&gt; scan 't1', {COLUMNS =&gt; ['c1', 'c2'],
 LIMIT =&gt; 10, \              STARTROW =&gt; 'xyz'}</span></span></p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
<span style="line-height:1.5;">在扫描的时候也可以自动设置缓存的开启和关闭状态，默认为开启。</span></p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
<span style="line-height:1.5;"><span style="line-height:1.5;color:rgb(0,102,0);">hbase&gt; scan 't1', {COLUMNS =&gt; ['c1', 'c2'], CACHE_BLOCKS =&gt; false}</span></span></p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
 </p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
<span style="line-height:1.5;">1</span>9.status</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
显示hbase中列簇状态，条件可以为：summary，simple，detailed，默认为summary。</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
<span style="line-height:1.5;"><span style="line-height:1.5;color:rgb(0,102,0);">hbase&gt; statushbase&gt; status 'simple'hbase&gt; status 'summary'<br></span></span><span style="line-height:1.5;color:rgb(0,102,0);">hbase&gt; status 'detailed'</span></p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
 </p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
20.shutdown,truncate,version</p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
转自：http://www.cnblogs.com/raylzhang/archive/2012/06/28/2567240.html</p>
            </div>
                </div>