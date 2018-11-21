---
layout:     post
title:      HBASE的shell使用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>也来hbase的shell吧</p>
<p>首先是登录hbase 了：</p>
<p>hbase(main):001:0&gt; create 'tbname','row','column1','column2'<br>
0 row(s) in 1.0730 seconds</p>
<p><br></p>
<p>hbase(main):004:0&gt; list 'tbname'<br>
TABLE <br>
tbname <br>
1 row(s) in 0.0290 seconds<br><span style="font-size:14px;"><br><strong style="background-color:rgb(255,255,255);">Put操作：</strong></span><br></p>
hbase(main):005:0&gt; put 'tbname','row','column1:a','a:value'<br>
0 row(s) in 0.2400 seconds<br><br>
hbase(main):006:0&gt; put 'tbname','row','column2:ff','ff:value'<br>
0 row(s) in 0.0080 seconds<br><br>
hbase(main):007:0&gt; put 'tbname','row','column1:b','b:value' <br>
0 row(s) in 0.0310 seconds<br><br>
hbase(main):008:0&gt; put 'tbname','row','column1:c','c:value'<br>
0 row(s) in 0.0080 seconds<br><br>
hbase(main):009:0&gt; put 'tbname','row','column2:gg','gg:value'<br><p>0 row(s) in 0.0080 seconds</p>
<p><span style="font-size:14px;color:#000000;background-color:rgb(255,255,255);"><strong>SCAN操作</strong></span><br></p>
hbase(main):011:0&gt; scan 'tbname'<br>
ROW COLUMN+CELL <br>
row column=column1:a, timestamp=1354556840011, value=a:value <br>
row column=column1:b, timestamp=1354556861963, value=b:value <br>
row column=column1:c, timestamp=1354556868862, value=c:value <br>
row column=column2:ff, timestamp=1354556852040, value=ff:value<br>
row column=column2:gg, timestamp=1354556885196, value=gg:value<br>
1 row(s) in 0.1500 seconds<br><br><p><span style="font-size:14px;color:#000000;"><strong>GET操作</strong>，</span><span style="font-size:14px;color:#000000;"><strong>获取表中的指定行</strong></span><br></p>
<p>hbase(main):012:0&gt; get 'tbname','row'</p>
COLUMN CELL <br>
column1:a timestamp=1354556840011, value=a:value <br>
column1:b timestamp=1354556861963, value=b:value <br>
column1:c timestamp=1354556868862, value=c:value <br>
column2:ff timestamp=1354556852040, value=ff:value <br>
column2:gg timestamp=1354556885196, value=gg:value <br>
5 row(s) in 0.0230 seconds<br><p><span style="font-size:14px;"><strong style="background-color:rgb(255,255,255);">GET操作，获取指定行列</strong></span><br></p>
<p>hbase(main):014:0&gt; get 'tbname','row','column1:a'</p>
COLUMN CELL <br>
column1:a timestamp=1354556840011, value=a:value <br>
1 row(s) in 0.0150 seconds<br><br><p>参考：http://www.yankay.com/wp-content/hbase/book.html#shell_exercises</p>
<p>创建一个名为 <code class="varname">test</code> 的表，<span style="color:#ff0000;">这个表只有一个 column family 为<code class="varname">cf</code>。</span>可以列出所有的表来检查创建情况，然后插入些值。</p>
<pre class="programlisting">hbase(main):003:0&gt; create 'test', 'cf'
0 row(s) in 1.2200 seconds
hbase(main):003:0&gt; list 'table'
test
1 row(s) in 0.0550 seconds
hbase(main):004:0&gt; put 'test', 'row1', 'cf:a', 'value1'
0 row(s) in 0.0560 seconds
hbase(main):005:0&gt; put 'test', 'row2', 'cf:b', 'value2'
0 row(s) in 0.0370 seconds
hbase(main):006:0&gt; put 'test', 'row3', 'cf:c', 'value3'
0 row(s) in 0.0450 seconds</pre>
<p>以上我们分别插入了3行。第一个行key为<code class="varname">row1</code>, 列为 <code class="varname">
cf:a</code>， 值是 <code class="varname">value1</code>。<span style="color:#ff0000;">Hbase中的列是由 column family前缀和列的名字组成的，以冒号间隔。</span>例如这一行的列名就是<code class="varname">a</code>.</p>
<p>检查插入情况.</p>
<p>Scan这个表，操作如下</p>
<pre class="programlisting">hbase(main):007:0&gt; scan 'test'
ROW        COLUMN+CELL
row1       column=cf:a, timestamp=1288380727188, value=value1
row2       column=cf:b, timestamp=1288380738440, value=value2
row3       column=cf:c, timestamp=1288380747365, value=value3
3 row(s) in 0.0590 seconds</pre>
<p>Get一行，操作如下</p>
<pre class="programlisting">hbase(main):008:0&gt; get 'test', 'row1'
COLUMN      CELL
cf:a        timestamp=1288380727188, value=value1
1 row(s) in 0.0400 seconds</pre>
<p><br></p>
<h3><span class="link_title"><a href="http://blog.csdn.net/scutshuxue/article/details/6988348" rel="nofollow"><span style="font-size:24px;"><strong>以下转自：http://blog.csdn.net/scutshuxue/article/details/6988348</strong></span></a></span></h3>
<h3><span class="link_title"><a href="http://blog.csdn.net/scutshuxue/article/details/6988348" rel="nofollow">Hbase shell 常用命令（1）
</a></span></h3>
<div class="article_manage">
<span class="link_postdate">2011-11-18 14:28</span> <span class="link_view" title="阅读次数">
4044人阅读</span> <span class="link_comments" title="评论次数"><a href="http://blog.csdn.net/scutshuxue/article/details/6988348#comments" rel="nofollow">评论</a>(0)</span>
<span class="link_collect"><a href="" rel="nofollow" title="收藏">收藏</a></span> <span class="link_report">
<a href="http://blog.csdn.net/scutshuxue/article/details/6988348#report" rel="nofollow" title="举报">举报</a></span>
</div>
<p>下面我们看看HBase Shell的一些基本操作命令，我列出了几个常用的HBase Shell命令，如下：</p>
<div>
<table border="1" cellpadding="0" cellspacing="0"><tbody><tr><td>
<p>名称</p>
</td>
<td>
<p>命令表达式</p>
</td>
</tr><tr><td>
<p>创建表</p>
</td>
<td>
<p>create '表名称', '列名称1','列名称2','列名称N'</p>
</td>
</tr><tr><td>
<p>添加记录 </p>
</td>
<td>
<p>put '表名称', '行名称', '列名称:', '值'</p>
</td>
</tr><tr><td>
<p>查看记录</p>
</td>
<td>
<p>get '表名称', '行名称'</p>
</td>
</tr><tr><td>
<p>查看表中的记录总数</p>
</td>
<td>
<p>count '表名称'</p>
</td>
</tr><tr><td>
<p>删除记录</p>
</td>
<td>
<p>delete '表名' ,'行名称' , '列名称'</p>
</td>
</tr><tr><td>
<p>删除一张表</p>
</td>
<td>
<p>先要屏蔽该表，才能对该表进行删除，第一步 disable '表名称' 第二步 drop '表名称'</p>
</td>
</tr><tr><td>
<p>查看所有记录</p>
</td>
<td>
<p>scan "表名称"</p>
</td>
</tr><tr><td>
<p>查看某个表某个列中所有数据</p>
</td>
<td>
<p>scan "表名称" , ['列名称:']</p>
</td>
</tr><tr><td>
<p>更新记录</p>
</td>
<td>
<p>就是重写一遍进行覆盖</p>
</td>
</tr></tbody></table></div>
<p></p>
<p>下面是一些常见命令的说明，在hbaseshell中输入help的帮助信息，在本文中，我们先介绍前3个，后面2个，将在下一篇博文中介绍。</p>
<p>COMMAND GROUPS:</p>
<p> Group name: general</p>
<p> Commands: status, version</p>
<p></p>
<p> Group name: ddl</p>
<p> Commands: alter, create, describe, disable,drop, enable, exists, is_disabled, is_enabled, list</p>
<p></p>
<p> Group name: dml</p>
<p> Commands: count, delete, deleteall, get,get_counter, incr, put, scan, truncate</p>
<p></p>
<p> Group name: tools</p>
<p> Commands: assign, balance_switch, balancer,close_region, compact, flush, major_compact, move, split, unassign, zk_dump</p>
<p></p>
<p> Group name: replication</p>
<p> Commands: add_peer, disable_peer,enable_peer, remove_peer, start_replication, stop_replication</p>
<p> <br></p>
<p><span style="font-size:32px;"><strong>一、一般操作</strong></span></p>
<p>1.查询服务器状态</p>
<p>hbase(main):024:0&gt;status</p>
<p>3 servers, 0 dead,1.0000 average load</p>
<p></p>
<p>2.查询hive版本</p>
<p></p>
<p>hbase(main):025:0&gt;version</p>
<p>0.90.4, r1150278,Sun Jul 24 15:53:29 PDT 2011</p>
<p></p>
<p><strong><span style="font-size:32px;">二、DDL操作</span></strong></p>
<p></p>
<p>1.创建一个表</p>
<p>hbase(main):011:0&gt;create 'member','member_id','address','info'</p>
<p>0 row(s) in 1.2210seconds</p>
<p></p>
<p>2.获得表的描述</p>
<p>hbase(main):012:0&gt;list</p>
<p>TABLE</p>
<p>member</p>
<p>1 row(s) in 0.0160seconds</p>
<p>hbase(main):006:0&gt;describe 'member'</p>
<p>DESCRIPTIONENABLED</p>
<p>{NAME =&gt; 'member', FAMILIES =&gt; [{NAME=&gt; 'address', BLOOMFILTER =&gt; 'NONE', REPLICATION_SCOPE =&gt; '0', true</p>
<p> VERSIONS =&gt; '3', COMPRESSION =&gt; 'NONE',TTL =&gt; '2147483647', BLOCKSIZE =&gt; '65536', IN_MEMORY =&gt; 'fa</p>
<p>lse', BLOCKCACHE =&gt; 'true'}, {NAME =&gt;'info', BLOOMFILTER =&gt; 'NONE', REPLICATION_SCOPE =&gt; '0', VERSI</p>
<p>ONS =&gt; '3', COMPRESSION =&gt; 'NONE', TTL=&gt; '2147483647', BLOCKSIZE =&gt; '65536', IN_MEMORY =&gt; 'false',</p>
<p>BLOCKCACHE =&gt; 'true'}]}</p>
<p>1 row(s) in 0.0230seconds</p>
<p></p>
<p>3.删除一个列族，alter，disable，enable</p>
<p>我们之前建了3个列族，但是发现member_id这个列族是多余的，因为他就是主键，所以我们要将其删除。</p>
<p>hbase(main):003:0&gt;alter 'member',{NAME=&gt;'member_id',METHOD=&gt;'delete'} </p>
<p></p>
<p>ERROR: Table memberis enabled. Disable it first before altering.</p>
<p></p>
<p>报错，删除列族的时候必须先将表给disable掉。</p>
<p>hbase(main):004:0&gt;disable 'member' </p>
<p>0 row(s) in 2.0390seconds</p>
<p>hbase(main):005:0&gt;alter'member',{NAME=&gt;'member_id',METHOD=&gt;'delete'}</p>
<p>0 row(s) in 0.0560seconds</p>
<p>hbase(main):006:0&gt;describe 'member'</p>
<p>DESCRIPTIONENABLED</p>
<p>{NAME =&gt; 'member', FAMILIES =&gt; [{NAME=&gt; 'address', BLOOMFILTER =&gt; 'NONE', REPLICATION_SCOPE =&gt; '0',false</p>
<p> VERSIONS =&gt; '3', COMPRESSION =&gt; 'NONE',TTL =&gt; '2147483647', BLOCKSIZE =&gt; '65536', IN_MEMORY =&gt; 'fa</p>
<p>lse', BLOCKCACHE =&gt; 'true'}, {NAME =&gt;'info', BLOOMFILTER =&gt; 'NONE', REPLICATION_SCOPE =&gt; '0', VERSI</p>
<p>ONS =&gt; '3', COMPRESSION =&gt; 'NONE', TTL=&gt; '2147483647', BLOCKSIZE =&gt; '65536', IN_MEMORY =&gt; 'false',</p>
<p>BLOCKCACHE =&gt; 'true'}]}</p>
<p>1 row(s) in 0.0230seconds</p>
<p>该列族已经删除，我们继续将表enable</p>
<p>hbase(main):008:0&gt; enable 'member'</p>
<p>0 row(s) in 2.0420seconds</p>
<p></p>
<p>4.列出所有的表</p>
<p>hbase(main):028:0&gt;list</p>
<p>TABLE</p>
<p>member</p>
<p>temp_table</p>
<p>2 row(s) in 0.0150seconds</p>
<p></p>
<p></p>
<p>5.drop一个表</p>
<p>hbase(main):029:0&gt;disable 'temp_table' </p>
<p>0 row(s) in 2.0590seconds</p>
<p></p>
<p>hbase(main):030:0&gt;drop 'temp_table'</p>
<p>0 row(s) in 1.1070seconds</p>
<p></p>
<p></p>
<p>6.查询表是否存在</p>
<p>hbase(main):021:0&gt;exists 'member'</p>
<p>Table member doesexist</p>
<p>0 row(s) in 0.1610seconds</p>
<p></p>
<p>7.判断表是否enable</p>
<p>hbase(main):034:0&gt;is_enabled 'member' </p>
<p>true</p>
<p>0 row(s) in 0.0110seconds</p>
<p></p>
<p>8.判断表是否disable</p>
<p>hbase(main):032:0&gt;is_disabled 'member'</p>
<p>false</p>
<p>0 row(s) in 0.0110seconds</p>
<p></p>
<p></p>
<p></p>
<p><span style="font-size:32px;"><strong>三、DML操作</strong></span></p>
<p></p>
<p></p>
<p>1.插入几条记录</p>
<p>put'member','scutshuxue','info:age','24'</p>
<p>put'member','scutshuxue','info:birthday','1987-06-17'</p>
<p>put'member','scutshuxue','info:company','alibaba'</p>
<p>put'member','scutshuxue','address:contry','china' </p>
<p>put'member','scutshuxue','address:province','zhejiang' </p>
<p>put'member','scutshuxue','address:city','hangzhou' </p>
<p></p>
<p></p>
<p>put'member','xiaofeng','info:birthday','1987-4-17'</p>
<p>put'member','xiaofeng','info:favorite','movie'</p>
<p>put'member','xiaofeng','info:company','alibaba'</p>
<p>put'member','xiaofeng','address:contry','china' </p>
<p>put'member','xiaofeng','address:province','guangdong' </p>
<p>put'member','xiaofeng','address:city','jieyang' </p>
<p>put'member','xiaofeng','address:town','xianqiao' </p>
<p></p>
<p></p>
<p></p>
<p>2.获取一条数据</p>
<p>获取一个id的所有数据</p>
<p>hbase(main):001:0&gt;get 'member','scutshuxue'</p>
<p>COLUMN CELL</p>
<p>address:citytimestamp=1321586240244, value=hangzhou</p>
<p>address:contrytimestamp=1321586239126, value=china</p>
<p>address:provincetimestamp=1321586239197, value=zhejiang</p>
<p>info:agetimestamp=1321586238965, value=24</p>
<p>info:birthdaytimestamp=1321586239015, value=1987-06-17</p>
<p>info:companytimestamp=1321586239071, value=alibaba</p>
<p>6 row(s) in 0.4720seconds</p>
<p></p>
<p>获取一个id，一个列族的所有数据</p>
<p>hbase(main):002:0&gt;get 'member','scutshuxue','info'</p>
<p>COLUMN CELL</p>
<p>info:agetimestamp=1321586238965, value=24</p>
<p>info:birthdaytimestamp=1321586239015, value=1987-06-17</p>
<p>info:companytimestamp=1321586239071, value=alibaba</p>
<p>3 row(s) in 0.0210seconds</p>
<p></p>
<p>获取一个id，一个列族中一个列的所有数据</p>
<p>hbase(main):002:0&gt;get 'member','scutshuxue','info:age' </p>
<p>COLUMN CELL</p>
<p>info:agetimestamp=1321586238965, value=24</p>
<p>1 row(s) in 0.0320seconds</p>
<p></p>
<p></p>
<p>6.更新一条记录</p>
<p>将scutshuxue的年龄改成99</p>
<p>hbase(main):004:0&gt;put 'member','scutshuxue','info:age' ,'99'</p>
<p>0 row(s) in 0.0210seconds</p>
<p></p>
<p>hbase(main):005:0&gt;get 'member','scutshuxue','info:age' </p>
<p>COLUMN CELL</p>
<p>info:agetimestamp=1321586571843, value=99</p>
<p>1 row(s) in 0.0180seconds</p>
<p></p>
<p></p>
<p>3.通过timestamp来获取两个版本的数据</p>
<p>hbase(main):010:0&gt;get 'member','scutshuxue',{COLUMN=&gt;'info:age',TIMESTAMP=&gt;1321586238965}</p>
<p>COLUMN CELL</p>
<p>info:agetimestamp=1321586238965, value=24</p>
<p>1 row(s) in 0.0140seconds</p>
<p></p>
<p>hbase(main):011:0&gt;get 'member','scutshuxue',{COLUMN=&gt;'info:age',TIMESTAMP=&gt;1321586571843}</p>
<p>COLUMN CELL</p>
<p>info:agetimestamp=1321586571843, value=99</p>
<p>1 row(s) in 0.0180seconds</p>
<p></p>
<p></p>
<p>4.全表扫描：</p>
<p>hbase(main):013:0&gt;scan 'member'</p>
<p>ROWCOLUMN+CELL</p>
<p>scutshuxuecolumn=address:city, timestamp=1321586240244, value=hangzhou</p>
<p>scutshuxuecolumn=address:contry, timestamp=1321586239126, value=china</p>
<p>scutshuxuecolumn=address:province, timestamp=1321586239197, value=zhejiang</p>
<p>scutshuxue column=info:age,timestamp=1321586571843, value=99</p>
<p>scutshuxuecolumn=info:birthday, timestamp=1321586239015, value=1987-06-17</p>
<p>scutshuxuecolumn=info:company, timestamp=1321586239071, value=alibaba</p>
<p>tempcolumn=info:age, timestamp=1321589609775, value=59</p>
<p>xiaofengcolumn=address:city, timestamp=1321586248400, value=jieyang</p>
<p>xiaofengcolumn=address:contry, timestamp=1321586248316, value=china</p>
<p>xiaofengcolumn=address:province, timestamp=1321586248355, value=guangdong</p>
<p>xiaofengcolumn=address:town, timestamp=1321586249564, value=xianqiao</p>
<p>xiaofengcolumn=info:birthday, timestamp=1321586248202, value=1987-4-17</p>
<p>xiaofengcolumn=info:company, timestamp=1321586248277, value=alibaba</p>
<p>xiaofengcolumn=info:favorite, timestamp=1321586248241, value=movie</p>
<p>3 row(s) in 0.0570seconds</p>
<p></p>
<p>5.删除id为temp的值的‘info:age’字段</p>
<p>hbase(main):016:0&gt;delete 'member','temp','info:age'</p>
<p>0 row(s) in 0.0150seconds</p>
<p>hbase(main):018:0&gt;get 'member','temp'</p>
<p>COLUMN CELL</p>
<p>0 row(s) in 0.0150seconds</p>
<p></p>
<p></p>
<p>6.删除整行</p>
<p></p>
<p>hbase(main):001:0&gt;deleteall 'member','xiaofeng'</p>
<p>0 row(s) in 0.3990seconds</p>
<p></p>
<p>7.查询表中有多少行：</p>
<p>hbase(main):019:0&gt;count 'member' </p>
<p>2 row(s) in 0.0160seconds</p>
<p></p>
<p>8.给‘xiaofeng’这个id增加'info:age'字段，并使用counter实现递增</p>
<p>hbase(main):057:0*incr 'member','xiaofeng','info:age' </p>
<p>COUNTER VALUE = 1</p>
<p></p>
<p>hbase(main):058:0&gt;get 'member','xiaofeng','info:age' </p>
<p>COLUMN CELL</p>
<p>info:agetimestamp=1321590997648, value=\x00\x00\x00\x00\x00\x00\x00\x01</p>
<p>1 row(s) in 0.0140seconds</p>
<p></p>
<p>hbase(main):059:0&gt;incr 'member','xiaofeng','info:age'</p>
<p>COUNTER VALUE = 2</p>
<p></p>
<p>hbase(main):060:0&gt;get 'member','xiaofeng','info:age' </p>
<p>COLUMN CELL</p>
<p>info:agetimestamp=1321591025110, value=\x00\x00\x00\x00\x00\x00\x00\x02</p>
<p>1 row(s) in 0.0160seconds</p>
<p></p>
<p>获取当前count的值</p>
<p>hbase(main):069:0&gt;get_counter 'member','xiaofeng','info:age'</p>
<p>COUNTER VALUE = 2</p>
<p> <br></p>
<p>9.将整张表清空：</p>
<p>hbase(main):035:0&gt;truncate 'member'</p>
<p>Truncating 'member'table (it may take a while):</p>
<p>- Disabling table...</p>
<p>- Dropping table...</p>
<p>- Creating table...</p>
<p>0 row(s) in 4.3430seconds</p>
<p>可以看出，hbase是先将掉disable掉，然后drop掉后重建表来实现truncate的功能的。</p>
<p></p>
<p></p>
<br>            </div>
                </div>