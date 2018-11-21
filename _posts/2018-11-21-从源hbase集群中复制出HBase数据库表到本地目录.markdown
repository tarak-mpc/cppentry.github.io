---
layout:     post
title:      从源hbase集群中复制出HBase数据库表到本地目录
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 style="color:rgb(54,46,43);font-family:Arial;line-height:26px;">
一、从源hbase集群中复制出HBase数据库表到本地目录</h1>
<p style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">
最好停止HBase，否则可能会丢部分数据</p>
<pre style="color:rgb(54,46,43);font-size:14px;line-height:26px;background-color:rgb(255,255,255);">[hbase@hadoop200 ~]$ hadoop fs -get /hbase/toplist_ware_total_1009_201232 toplist_ware_total_1009_201232
</pre>
<p style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">
压缩</p>
<pre style="color:rgb(54,46,43);font-size:14px;line-height:26px;background-color:rgb(255,255,255);">[hbase@hadoop200 ~]$ tar zcvf topl.tar.gz toplist_ware_total_1009_201232
</pre>
<p style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">
远程复制到目标机器</p>
<pre style="color:rgb(54,46,43);font-size:14px;line-height:26px;background-color:rgb(255,255,255);">[hbase@hadoop200 ~]$ scp topl.tar.gz zhouhh@h185:~/.
</pre>
<h1 style="color:rgb(54,46,43);font-family:Arial;line-height:26px;">
<a name="t1" style="color:rgb(106,57,6);"></a>二、目标HBase导入</h1>
<p style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">
解压<br>
[zhouhh@h185 ~]$ tar zxvf topl.tar.gz</p>
<p style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">
如果目标HBase里有这个表，需disable并drop掉。如果有该目录，则用hadoop fs -rmr /hbase/table的方式删除，再往HDFS上复制。以免数据出错。</p>
<p style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">
放到集群下面</p>
<pre style="color:rgb(54,46,43);font-size:14px;line-height:26px;background-color:rgb(255,255,255);">[zhouhh@h185 ~]$ fs -put toplist_ware_total_1009_201232 /hbase
[zhouhh@h185 ~]$
</pre>
<p style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">
此时可以list出来，但scan报错</p>
<pre style="color:rgb(54,46,43);font-size:14px;line-height:26px;background-color:rgb(255,255,255);">hbase(main):055:0&gt; list 'toplist_ware_total_1009_201232'
TABLE
toplist_ware_total_1009_201232
1 row(s) in 0.0220 seconds

hbase(main):062:0&gt; scan 'toplist_ware_total_1009_201232'
ROW                                        COLUMN+CELL

ERROR: Unknown table toplist_ware_total_1009_201232!
</pre>
<p style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">
.META.表里面没有相关记录<br>
hbase(main):064:0&gt; scan ‘.META.’<br>
里面没有toplist_ware_total_1009_201232 开头的行</p>
<h1 style="color:rgb(54,46,43);font-family:Arial;line-height:26px;">
<a name="t2" style="color:rgb(106,57,6);"></a>三、修复.META.表和重新分配数据到各RegionServer</h1>
<p style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">
在.META.表没修复时执行重新分配，会报错</p>
<pre style="color:rgb(54,46,43);font-size:14px;line-height:26px;background-color:rgb(255,255,255);">[zhouhh@h185 ~]$ hbase hbck -fixAssignments
...
ERROR: Region { meta =&gt; null, hdfs =&gt; hdfs://h185:54310/hbase/toplist_ware_total_1009_201232/0403552001eb2a31990e443dcae74ee8, deployed =&gt;  } on HDFS, but not listed in META or deployed on any region server
...
</pre>
<p style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">
先修复.META.表</p>
<pre style="color:rgb(54,46,43);font-size:14px;line-height:26px;background-color:rgb(255,255,255);">[zhouhh@h185 ~]$ hbase hbck -fixMeta
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
<p style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">
重新分配到各分区服务器</p>
<pre style="color:rgb(54,46,43);font-size:14px;line-height:26px;background-color:rgb(255,255,255);">[zhouhh@h185 ~]$ hbase hbck -fixAssignments
...
ERROR: Region { meta =&gt; toplist_ware_total_1009_201232,,1344187094829.0403552001eb2a31990e443dcae74ee8., hdfs =&gt; hdfs://h185:54310/hbase/toplist_ware_total_1009_201232/0403552001eb2a31990e443dcae74ee8, deployed =&gt;  } not deployed on any region server.
Trying to fix unassigned region...
12/08/14 18:28:01 INFO util.HBaseFsckRepair: Region still in transition, waiting for it to become assigned: {NAME =&gt; 'toplist_ware_total_1009_201232,,1344187094829.0403552001eb2a31990e443dcae74ee8.', STARTKEY =&gt; '', ENDKEY =&gt; '', ENCODED =&gt; 0403552001eb2a31990e443dcae74ee8,}
12/08/14 18:28:02 INFO util.HBaseFsckRepair: Region still in transition, waiting for it to become assigned: {NAME =&gt; 'toplist_ware_total_1009_201232,,1344187094829.0403552001eb2a31990e443dcae74ee8.', STARTKEY =&gt; '', ENDKEY =&gt; '', ENCODED =&gt; 0403552001eb2a31990e443dcae74ee8,}
12/08/14 18:28:04 INFO util.HBaseFsckRepair: Region still in transition, waiting for it to become assigned: {NAME =&gt; 'toplist_ware_total_1009_201232,,1344187094829.0403552001eb2a31990e443dcae74ee8.', STARTKEY =&gt; '', ENDKEY =&gt; '', ENCODED =&gt; 0403552001eb2a31990e443dcae74ee8,}
12/08/14 18:28:05 INFO util.HBaseFsckRepair: Region still in transition, waiting for it to become assigned: {NAME =&gt; 'toplist_ware_total_1009_201232,,1344187094829.0403552001eb2a31990e443dcae74ee8.', STARTKEY =&gt; '', ENDKEY =&gt; '', ENCODED =&gt; 0403552001eb2a31990e443dcae74ee8,}
...
</pre>
<p style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">
scan成功!</p>
<pre style="color:rgb(54,46,43);font-size:14px;line-height:26px;background-color:rgb(255,255,255);">hbase(main):067:0&gt; scan 'toplist_ware_total_1009_201232'
ROW                                        COLUMN+CELL
 0000000001                                column=info:loginid, timestamp=1344187147972, value=jjm167258611
 0000000001                                column=info:nick, timestamp=1344187147972, value=?\xE9\x97\xB4?\xE6\xB5\xA3?
 0000000001                                column=info:score, timestamp=1344187147972, value=200
 0000000001                                column=info:userid, timestamp=1344187147972, value=167258611
...
330 row(s) in 0.8630 seconds
</pre>
<p style="color:rgb(54,46,43);font-family:Arial;font-size:14px;line-height:26px;">
如果目标集群是空的，则可以直接将源HBase的/hbase目录复制出来，然后在目标HBase系统上fs -rmr /hbase 或fs -mv /hbase /hbase1<br>
然后用hadoop fs -put hbase / 即可</p>
            </div>
                </div>