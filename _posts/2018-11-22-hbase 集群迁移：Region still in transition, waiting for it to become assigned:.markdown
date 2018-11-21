---
layout:     post
title:      hbase 集群迁移：Region still in transition, waiting for it to become assigned:
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许随机转载。					https://blog.csdn.net/mtj66/article/details/77041879				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 style="font-family:Arial;"><span style="font-size:18px;">1. 在数据源集群执行 ：</span></h1>
<h1 style="font-family:Arial;"><span style="font-size:18px;"> hadoop distcp  /hbase/data/*  hdfs://172.16.4.81/hbase/data/<br></span></h1>
<div><strong><span style="font-size:18px;"><br></span></strong></div>
<div><strong><span style="font-size:18px;">2. 在目标集群执行 :</span></strong></div>
<div><strong><span style="font-size:18px;"><span></span>修改目标文件chown  hdfs  dfs -chown -R /hbase/data</span></strong></div>
<div><strong><span style="font-size:18px;">3. 之后执行 </span></strong></div>
<div><strong><span style="font-size:18px;"><br></span></strong></div>
<div><strong><span style="font-size:18px;">hbase hbck -repair<br></span></strong></div>
<div><strong><span style="font-size:18px;">hbase hbck -fixAssignments</span><br></strong></div>
<div><br></div>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;"><strong>执行过程中失败，Region still in transition, waiting for it to become assigned:</strong></span></div>
<div><span style="font-size:18px;">参照其他文章依然没有解决，然后自己发现，表的hbase:meta 已经有 regioninfo 信息，但是scan 时候报错，</span></div>
<div><span style="font-size:18px;">ERROR: No server address listed in hbase:meta for region <br></span></div>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;">参照第二步 ，修改文件所有者以及所有者组。成功解决该问题。</span></div>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;"><br></span></div>
<div><span style="font-size:18px;">排查过程中参考如下步骤</span></div>
<div><br></div>
<h1 style="font-family:Arial;">一、从源hbase集群中复制出HBase数据库表到本地目录</h1>
<p style="font-family:Arial;font-size:14px;">
http://abloz.com/2012/08/14/another-method-of-the-data-of-the-<a href="http://lib.csdn.net/base/hbase" rel="nofollow" class="replace_word" title="Hbase知识库" style="color:rgb(223,52,52);font-weight:bold;">Hbase</a>-cross-cluster-replication.html<br></p>
<p style="font-family:Arial;font-size:14px;">
最好停止<a href="http://lib.csdn.net/base/hbase" rel="nofollow" class="replace_word" title="Hbase知识库" style="color:rgb(223,52,52);font-weight:bold;">hbase</a>，否则可能会丢部分数据</p>
<pre style="font-size:14px;background-color:rgb(255,255,255);">[hbase@hadoop200 ~]$ hadoop fs -get /hbase/toplist_ware_total_1009_201232 toplist_ware_total_1009_201232
</pre>
<p style="font-family:Arial;font-size:14px;">
压缩</p>
<pre style="font-size:14px;background-color:rgb(255,255,255);">[hbase@hadoop200 ~]$ tar zcvf topl.tar.gz toplist_ware_total_1009_201232
</pre>
<p style="font-family:Arial;font-size:14px;">
远程复制到目标机器</p>
<pre style="font-size:14px;background-color:rgb(255,255,255);">[hbase@hadoop200 ~]$ scp topl.tar.gz zhouhh@h185:~/.
</pre>
<h1 style="font-family:Arial;"><a name="t1" style="color:rgb(255,153,0);"></a>二、目标HBase导入</h1>
<p style="font-family:Arial;font-size:14px;">
解压<br>
[zhouhh@h185 ~]$ tar zxvf topl.tar.gz</p>
<p style="font-family:Arial;font-size:14px;">
如果目标HBase里有这个表，需disable并drop掉。如果有该目录，则用<a href="http://lib.csdn.net/base/hadoop" rel="nofollow" class="replace_word" title="Hadoop知识库" style="color:rgb(223,52,52);font-weight:bold;">Hadoop</a> fs -rmr /hbase/table的方式删除，再往HDFS上复制。以免数据出错。</p>
<p style="font-family:Arial;font-size:14px;">
放到集群下面</p>
<pre style="font-size:14px;background-color:rgb(255,255,255);">[zhouhh@h185 ~]$ fs -put toplist_ware_total_1009_201232 /hbase
[zhouhh@h185 ~]$
</pre>
<p style="font-family:Arial;font-size:14px;">
此时可以list出来，但scan报错</p>
<pre style="font-size:14px;background-color:rgb(255,255,255);">hbase(main):055:0&gt; list 'toplist_ware_total_1009_201232'
TABLE
toplist_ware_total_1009_201232
1 row(s) in 0.0220 seconds

hbase(main):062:0&gt; scan 'toplist_ware_total_1009_201232'
ROW                                        COLUMN+CELL

ERROR: Unknown table toplist_ware_total_1009_201232!
</pre>
<p style="font-family:Arial;font-size:14px;">
.META.表里面没有相关记录<br>
hbase(main):064:0&gt; scan ‘.META.’<br>
里面没有toplist_ware_total_1009_201232 开头的行</p>
<h1 style="font-family:Arial;"><a name="t2" style="color:rgb(255,153,0);"></a>三、修复.META.表和重新分配数据到各RegionServer</h1>
<p style="font-family:Arial;font-size:14px;">
在.META.表没修复时执行重新分配，会报错</p>
<pre style="font-size:14px;background-color:rgb(255,255,255);">[zhouhh@h185 ~]$ hbase hbck -fixAssignments
...
ERROR: Region { meta =&gt; null, hdfs =&gt; hdfs://h185:54310/hbase/toplist_ware_total_1009_201232/0403552001eb2a31990e443dcae74ee8, deployed =&gt;  } on HDFS, but not listed in META or deployed on any region server
...
</pre>
<p style="font-family:Arial;font-size:14px;">
先修复.META.表</p>
<pre style="font-size:14px;background-color:rgb(255,255,255);">[zhouhh@h185 ~]$ hbase hbck -fixMeta
...
ERROR: Region { meta =&gt; null, hdfs =&gt; hdfs://h185:54310/hbase/toplist_ware_total_1009_201232/0403552001eb2a31990e443dcae74ee8, deployed =&gt;  } on HDFS, but not listed in META or deployed on any region server
12/08/14 18:25:15 INFO util.HBaseFsck: Patching .META. with .regioninfo: {NAME =&gt; 'toplist_ware_total_1009_201232,,1344187094829.0403552001eb2a31990e443dcae74ee8.', STARTKEY =&gt; '', ENDKEY =&gt; '', ENCODED =&gt; 0403552001eb2a31990e443dcae74ee8,}
...

此时.META.表已经有表的数据了，但scan还是失败
hbase(main):065:0&gt; scan '.META.'
ROW                                        COLUMN+CELL
...
toplist_ware_total_1009_201232,,134418709 column=info:regioninfo, timestamp=1344939930752, value={NAME =&gt; 'toplist_ware_total_1009_201232,,1344187094829.0403552001eb
 4829.0403552001eb2a31990e443dcae74ee8.    2a31990e443dcae74ee8.', STARTKEY =&gt; '', ENDKEY =&gt; '', ENCODED =&gt; 0403552001eb2a31990e443dcae74ee8,}
16 row(s) in 0.0550 seconds

scan还是失败
hbase(main):066:0&gt; scan 'toplist_ware_total_1009_201232'
ROW                                        COLUMN+CELL

ERROR: org.apache.hadoop.hbase.client.NoServerForRegionException: No server address listed in .META. for region toplist_ware_total_1009_201232,,1344187094829.0403552001eb2a31990e443dcae74ee8. containing row
</pre>
<p style="font-family:Arial;font-size:14px;">
重新分配到各分区服务器</p>
<pre style="font-size:14px;background-color:rgb(255,255,255);">[zhouhh@h185 ~]$ hbase hbck -fixAssignments
...
ERROR: Region { meta =&gt; toplist_ware_total_1009_201232,,1344187094829.0403552001eb2a31990e443dcae74ee8., hdfs =&gt; hdfs://h185:54310/hbase/toplist_ware_total_1009_201232/0403552001eb2a31990e443dcae74ee8, deployed =&gt;  } not deployed on any region server.
Trying to fix unassigned region...
12/08/14 18:28:01 INFO util.HBaseFsckRepair: Region still in transition, waiting for it to become assigned: {NAME =&gt; 'toplist_ware_total_1009_201232,,1344187094829.0403552001eb2a31990e443dcae74ee8.', STARTKEY =&gt; '', ENDKEY =&gt; '', ENCODED =&gt; 0403552001eb2a31990e443dcae74ee8,}
12/08/14 18:28:02 INFO util.HBaseFsckRepair: Region still in transition, waiting for it to become assigned: {NAME =&gt; 'toplist_ware_total_1009_201232,,1344187094829.0403552001eb2a31990e443dcae74ee8.', STARTKEY =&gt; '', ENDKEY =&gt; '', ENCODED =&gt; 0403552001eb2a31990e443dcae74ee8,}
12/08/14 18:28:04 INFO util.HBaseFsckRepair: Region still in transition, waiting for it to become assigned: {NAME =&gt; 'toplist_ware_total_1009_201232,,1344187094829.0403552001eb2a31990e443dcae74ee8.', STARTKEY =&gt; '', ENDKEY =&gt; '', ENCODED =&gt; 0403552001eb2a31990e443dcae74ee8,}
12/08/14 18:28:05 INFO util.HBaseFsckRepair: Region still in transition, waiting for it to become assigned: {NAME =&gt; 'toplist_ware_total_1009_201232,,1344187094829.0403552001eb2a31990e443dcae74ee8.', STARTKEY =&gt; '', ENDKEY =&gt; '', ENCODED =&gt; 0403552001eb2a31990e443dcae74ee8,}
...
</pre>
<p style="font-family:Arial;font-size:14px;">
scan成功!</p>
<pre style="font-size:14px;background-color:rgb(255,255,255);">hbase(main):067:0&gt; scan 'toplist_ware_total_1009_201232'
ROW                                        COLUMN+CELL
 0000000001                                column=info:loginid, timestamp=1344187147972, value=jjm167258611
 0000000001                                column=info:nick, timestamp=1344187147972, value=?\xE9\x97\xB4?\xE6\xB5\xA3?
 0000000001                                column=info:score, timestamp=1344187147972, value=200
 0000000001                                column=info:userid, timestamp=1344187147972, value=167258611
...
330 row(s) in 0.8630 seconds
</pre>
<p style="font-family:Arial;font-size:14px;">
如果目标集群是空的，则可以直接将源HBase的/hbase目录复制出来，然后在目标HBase系统上fs -rmr /hbase 或fs -mv /hbase /hbase1<br>
然后用<a href="http://lib.csdn.net/base/hadoop" rel="nofollow" class="replace_word" title="Hadoop知识库" style="color:rgb(223,52,52);font-weight:bold;">hadoop</a> fs -put hbase / 即可</p>
            </div>
                </div>