---
layout:     post
title:      HFDS 常用命令 fsck 查看文件信息和block位置信息
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;text-indent:30px;">
在HDFS中，提供了fsck命令，用于检查HDFS上文件和目录的健康状态、获取文件的block信息和位置信息等。</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;text-indent:30px;">
fsck命令必须由HDFS超级用户来执行，普通用户无权限。</p>
<p style="color:rgb(85,85,85);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;text-indent:30px;">
<br></p>
<p style="color:rgb(85,85,85);text-indent:30px;">
<span style="font-family:'Microsoft YaHei';font-size:18px;background-color:rgb(153,255,153);">[root@minit1 hadoop-2.7.2]# bin/hdfs fsck <br><span></span> Usage: hdfs fsck &lt;path&gt; [-list-corruptfileblocks | [-move | -delete | -openforwrite] [-files [-blocks [-<span></span> locations | -racks]]]] [-<span></span>includeSnapshots]
 [-            storagepolicies] [-blockId &lt;blk_Id&gt;]<br>
        &lt;path&gt;  start checking from this path<br>
        -move   move corrupted files to /lost+found<br>
        -delete delete corrupted files<br>
        -files  print out files being checked<br>
        -openforwrite   print out files opened for write<br>
        -includeSnapshots       include snapshot data if the given path indicates a snapshottable directory or<span></span> there are<span></span>snapshottable directories under           it<br>
        -list-corruptfileblocks print out list of missing blocks and files they belong to<br>
        -blocks print out block report<br>
        -locations      print out locations for every block<br>
        -racks  print out network topology for data-node locations<br>
        -storagepolicies        print out storage policy summary for the blocks<br>
        -blockId        print out which file this blockId belongs to, locations (nodes, racks) of this block, and<span></span>other diagnostics info<span></span>(under replicated,
<span></span>corrupted or not, etc)</span><br></p>
<p style="text-indent:30px;"><span style="font-family:'Microsoft YaHei';font-size:18px;"></span></p>
<h2 style="color:rgb(85,85,85);background-color:rgb(251,251,251);line-height:18px;font-size:18px;border-left:4px solid rgb(0,166,124);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;text-indent:30px;">
    查看文件中损坏的块（-list-corruptfileblocks）</h2>
<div style="color:rgb(85,85,85);"><span style="background-color:rgb(153,255,153);">   [root@minit1 hadoop-2.7.2]# bin/hdfs fsck  /user/mapreduce/wordcount/output5/part-r-00000 -list-<span></span>corruptfileblocks<br><span></span>Connecting to namenode via http://minit1:50070/fsck?<span></span>ugi=root&amp;listcorruptfileblocks=1&amp;path=%2Fuser%2Fmapreduce%2Fwordcount%2Foutput5%2Fpart-<span></span>r-00000<br><span></span>The filesystem under path '/user/mapreduce/wordcount/output5/part-r-00000' has 0 CORRUPT files</span></div>
<span style="color:#555555;">      </span>
<h2 style="color:rgb(85,85,85);line-height:18px;font-size:18px;border-left:4px solid rgb(0,166,124);background-color:rgb(251,251,251);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;text-indent:30px;">
   将损坏的文件移动至/lost+found目录（-move）</h2>
<div><span><span style="color:#555555;"><br></span><span style="color:#99ff99;"></span><span style="background-color:rgb(153,255,153);"><span style="color:#333333;">[root@minit1 hadoop-2.7.2]# bin/hdfs fsck  /user/mapreduce/wordcount/output5/part-r-00000 -move<br><span></span>Connecting to namenode via http://minit1:50070/fsck?ugi=root&amp;move=1&amp;path=%2Fuser%2Fmapreduce%2Fwordcount%2Foutput5%2Fpart-r-00000<br><span></span>FSCK started by root (auth:SIMPLE) from /192.168.65.145 for path /user/mapreduce/wordcount/output5/part-r-00000 at Fri Dec 22 17:00:05 CST 2017<br><span></span>.Status: HEALTHY<br>
 <span> </span>Total size:    172 B<br>
 <span> </span>Total dirs:    0<br>
 <span> </span>Total files:   1<br>
 <span> </span>Total symlinks:                0<br>
 <span> </span>Total blocks (validated):      1 (avg. block size 172 B)<br>
 <span> </span>Minimally replicated blocks:   1 (100.0 %)<br>
 <span> </span>Over-replicated blocks:        0 (0.0 %)<br>
 <span> </span>Under-replicated blocks:       0 (0.0 %)<br>
 <span> </span>Mis-replicated blocks:         0 (0.0 %)<br>
 <span> </span>Default replication factor:    3<br>
 <span> </span>Average block replication:     3.0<br><span></span> Corrupt blocks:                0<br>
 <span> </span>Missing replicas:              0 (0.0 %)<br>
 <span> </span>Number of data-nodes:          3<br>
 <span> </span>Number of racks:               1<br><span></span>FSCK ended at Fri Dec 22 17:00:05 CST 2017 in 1 milliseconds</span></span></span><span style="color:rgb(51,51,51);background-color:rgb(153,255,153);"></span></div>
<div>
<h2 style="line-height:18px;color:rgb(85,85,85);font-size:18px;border-left:4px solid rgb(0,166,124);background-color:rgb(251,251,251);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;text-indent:30px;">
 删除损坏的文件（-delete）</h2>
<div><span style="background-color:rgb(153,255,153);">[root@minit1 hadoop-2.7.2]# bin/hdfs fsck  /user/mapreduce/wordcount/output5/part-r-00000 -delete<br><span></span>Connecting to namenode via http://minit1:50070/fsck?<span></span>ugi=root&amp;delete=1&amp;path=%2Fuser%2Fmapreduce%2Fwordcount%2Foutput5%2Fpart-r-00000<br><span></span>FSCK started by root (auth:SIMPLE) from /192.168.65.145 for path<span></span>/user/mapreduce/wordcount/output5/part-r-00000 at Fri Dec 22 17:03:59 CST 2017<br><span></span>.Status: HEALTHY<br>
 <span> </span> Total size:    172 B<br><span></span> Total dirs:    0<br><span></span> Total files:   1<br><span></span> Total symlinks:                0<br><span></span> Total blocks (validated):      1 (avg. block size 172 B)<br><span></span> Minimally replicated blocks:   1 (100.0 %)<br><span></span> Over-replicated blocks:        0 (0.0 %)<br><span></span> Under-replicated blocks:       0 (0.0 %)<br><span></span> Mis-replicated blocks:         0 (0.0 %)<br><span></span> Default replication factor:    3<br>
 <span> </span>Average block replication:     3.0<br><span></span> Corrupt blocks:                0<br><span></span> Missing replicas:              0 (0.0 %)<br><span></span> Number of data-nodes:          3<br><span></span> Number of racks:               1<br><span></span>FSCK ended at Fri Dec 22 17:03:59 CST 2017 in 0 milliseconds</span><br></div>
       
<h2 style="line-height:18px;color:rgb(85,85,85);font-size:18px;border-left:4px solid rgb(0,166,124);background-color:rgb(251,251,251);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;text-indent:30px;">
   检查并列出所有文件状态（-files）</h2>
<div><span style="background-color:rgb(153,255,153);"> [root@minit1 hadoop-2.7.2]# bin/hdfs fsck  /user/mapreduce/wordcount/output5/part-r-00000 -files<br><span></span>Connecting to namenode via http://minit1:50070/fsck?<span></span>ugi=root&amp;files=1&amp;path=%2Fuser%2Fmapreduce%2Fwordcount%2Foutput5%2Fpart-r-00000<br><span></span>FSCK started by root (auth:SIMPLE) from /192.168.65.145 for path /user<span></span>/mapreduce/wordcount/output5/part-r-00000 at Fri Dec 22 17:05:47 CST 2017<br><span></span>/user/mapreduce/wordcount/output5/part-r-00000 172 bytes, 1 block(s):  OK<br><span></span>Status: HEALTHY<br><span></span> Total size:    172 B<br><span></span> Total dirs:    0<br><span></span> Total files:   1<br><span></span> Total symlinks:                0<br><span></span> Total blocks (validated):      1 (avg. block size 172 B)<br>
 <span> </span>Minimally replicated blocks:   1 (100.0 %)<br>
 <span> </span>Over-replicated blocks:        0 (0.0 %)<br>
 <span> </span>Under-replicated blocks:       0 (0.0 %)<br>
 <span> </span>Mis-replicated blocks:         0 (0.0 %)<br>
 <span> </span>Default replication factor:    3<br>
 <span> </span>Average block replication:     3.0<br>
 <span> </span>Corrupt blocks:                0<br>
 <span> </span>Missing replicas:              0 (0.0 %)<br>
 <span> </span>Number of data-nodes:          3<br>
 <span> </span>Number of racks:               1<br><span></span>FSCK ended at Fri Dec 22 17:05:47 CST 2017 in 1 milliseconds<span></span></span></div>
<div>
<h2 style="line-height:18px;color:rgb(85,85,85);font-size:18px;border-left:4px solid rgb(0,166,124);background-color:rgb(251,251,251);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;text-indent:30px;">
   检查并打印正在被打开执行写操作的文件（-openforwrite）</h2>
<div><span style="background-color:rgb(153,255,153);">[root@minit1 hadoop-2.7.2]# bin/hdfs fsck  /user/mapreduce/wordcount/output5/part-r-00000 -openforwrite<br>
Connecting to namenode via http://minit1:50070/fsck?ugi=root&amp;openforwrite=1&amp;path=%2Fuser%2Fmapreduce%2Fwordcount%2Foutput5%2Fpart-r-00000<br>
FSCK started by root (auth:SIMPLE) from /192.168.65.145 for path /user/mapreduce/wordcount/output5/part-r-00000 at Fri Dec 22 17:08:21 CST 2017<br>
.Status: HEALTHY<br>
 Total size:    172 B<br>
 Total dirs:    0<br>
 Total files:   1<br>
 Total symlinks:                0<br>
 Total blocks (validated):      1 (avg. block size 172 B)<br>
 Minimally replicated blocks:   1 (100.0 %)<br>
 Over-replicated blocks:        0 (0.0 %)<br>
 Under-replicated blocks:       0 (0.0 %)<br>
 Mis-replicated blocks:         0 (0.0 %)<br>
 Default replication factor:    3<br>
 Average block replication:     3.0<br>
 Corrupt blocks:                0<br>
 Missing replicas:              0 (0.0 %)<br>
 Number of data-nodes:          3<br>
 Number of racks:               1<br>
FSCK ended at Fri Dec 22 17:08:21 CST 2017 in 0 milliseconds<br></span></div>
<span style="background-color:rgb(153,255,153);"> </span></div>
<div><span style="background-color:rgb(153,255,153);"></span>
<h2 style="line-height:18px;color:rgb(85,85,85);font-size:18px;border-left:4px solid rgb(0,166,124);background-color:rgb(251,251,251);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;text-indent:30px;">
打印文件的Block报告（-blocks）</h2>
  <span style="background-color:rgb(153,255,153);"> [root@minit1 hadoop-2.7.2]# bin/hdfs fsck  /user/mapreduce/wordcount/output5/part-r-00000 -files -blocks<br>
Connecting to namenode via http://minit1:50070/fsck?ugi=root&amp;files=1&amp;blocks=1&amp;path=%2Fuser%2Fmapreduce%2Fwordcount%2Foutput5%2Fpart-r-00000<br>
FSCK started by root (auth:SIMPLE) from /192.168.65.145 for path /user/mapreduce/wordcount/output5/part-r-00000 at Fri Dec 22 17:09:33 CST 2017<br>
/user/mapreduce/wordcount/output5/part-r-00000 172 bytes, 1 block(s):  OK<br>
0. BP-1296710928-192.168.65.145-1513844600367:blk_1073741891_1067 len=172 repl=3<br><br><br>
Status: HEALTHY<br>
 Total size:    172 B<br>
 Total dirs:    0<br>
 Total files:   1<br>
 Total symlinks:                0<br>
 Total blocks (validated):      1 (avg. block size 172 B)<br>
 Minimally replicated blocks:   1 (100.0 %)<br>
 Over-replicated blocks:        0 (0.0 %)<br>
 Under-replicated blocks:       0 (0.0 %)<br>
 Mis-replicated blocks:         0 (0.0 %)<br>
 Default replication factor:    3<br>
 Average block replication:     3.0<br>
 Corrupt blocks:                0<br>
 Missing replicas:              0 (0.0 %)<br>
 Number of data-nodes:          3<br>
 Number of racks:               1<br>
FSCK ended at Fri Dec 22 17:09:33 CST 2017 in 0 milliseconds</span></div>
<div>
<h2 style="background-color:rgb(251,251,251);line-height:18px;color:rgb(85,85,85);font-size:18px;border-left:4px solid rgb(0,166,124);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;text-indent:30px;">
打印文件块的位置信息（-locations）</h2>
<div><span style="background-color:rgb(153,255,153);">[root@minit1 hadoop-2.7.2]# bin/hdfs fsck  /user/mapreduce/wordcount/output5/part-r-00000 -files -blocks -locations<br>
Connecting to namenode via http://minit1:50070/fsck?ugi=root&amp;files=1&amp;blocks=1&amp;locations=1&amp;path=%2Fuser%2Fmapreduce%2Fwordcount%2Foutput5%2Fpart-r-00000<br>
FSCK started by root (auth:SIMPLE) from /192.168.65.145 for path /user/mapreduce/wordcount/output5/part-r-00000 at Fri Dec 22 17:10:33 CST 2017<br>
/user/mapreduce/wordcount/output5/part-r-00000 172 bytes, 1 block(s):  OK<br>
0. BP-1296710928-192.168.65.145-1513844600367:blk_1073741891_1067 len=172 repl=3 [DatanodeInfoWithStorage[192.168.65.146:50010,DS-0838f58c-6908-4651-a027-e36acc3d5cf8,DISK], DatanodeInfoWithStorage[192.168.65.145:50010,DS-658fbbe4-88c7-4815-a09d-5d69bb63dfe8,DISK],
 DatanodeInfoWithStorage[192.168.65.147:50010,DS-6b378552-8cac-4312-bfb2-858755402788,DISK]]<br><br><br>
Status: HEALTHY<br>
 Total size:    172 B<br>
 Total dirs:    0<br>
 Total files:   1<br>
 Total symlinks:                0<br>
 Total blocks (validated):      1 (avg. block size 172 B)<br>
 Minimally replicated blocks:   1 (100.0 %)<br>
 Over-replicated blocks:        0 (0.0 %)<br>
 Under-replicated blocks:       0 (0.0 %)<br>
 Mis-replicated blocks:         0 (0.0 %)<br>
 Default replication factor:    3<br>
 Average block replication:     3.0<br>
 Corrupt blocks:                0<br>
 Missing replicas:              0 (0.0 %)<br>
 Number of data-nodes:          3<br>
 Number of racks:               1<br>
FSCK ended at Fri Dec 22 17:10:33 CST 2017 in 1 milliseconds<br></span></div>
<div>
<h2 style="background-color:rgb(251,251,251);line-height:18px;color:rgb(85,85,85);font-size:18px;border-left:4px solid rgb(0,166,124);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;text-indent:30px;">
打印文件块位置所在的机架信息（-racks）</h2>
<div><span style="background-color:rgb(153,255,153);">[root@minit1 hadoop-2.7.2]# bin/hdfs fsck  /user/mapreduce/wordcount/output5/part-r-00000 -files -blocks -locations -racks<br>
Connecting to namenode via http://minit1:50070/fsck?ugi=root&amp;files=1&amp;blocks=1&amp;locations=1&amp;racks=1&amp;path=%2Fuser%2Fmapreduce%2Fwordcount%2Foutput5%2Fpart-r-00000<br>
FSCK started by root (auth:SIMPLE) from /192.168.65.145 for path /user/mapreduce/wordcount/output5/part-r-00000 at Fri Dec 22 17:12:27 CST 2017<br>
/user/mapreduce/wordcount/output5/part-r-00000 172 bytes, 1 block(s):  OK<br>
0. BP-1296710928-192.168.65.145-1513844600367:blk_1073741891_1067 len=172 repl=3 [/default-rack/192.168.65.146:50010, /default-rack/192.168.65.145:50010, /default-rack/192.168.65.147:50010]<br><br><br>
Status: HEALTHY<br>
 Total size:    172 B<br>
 Total dirs:    0<br>
 Total files:   1<br>
 Total symlinks:                0<br>
 Total blocks (validated):      1 (avg. block size 172 B)<br>
 Minimally replicated blocks:   1 (100.0 %)<br>
 Over-replicated blocks:        0 (0.0 %)<br>
 Under-replicated blocks:       0 (0.0 %)<br>
 Mis-replicated blocks:         0 (0.0 %)<br>
 Default replication factor:    3<br>
 Average block replication:     3.0<br>
 Corrupt blocks:                0<br>
 Missing replicas:              0 (0.0 %)<br>
 Number of data-nodes:          3<br>
 Number of racks:               1<br>
FSCK ended at Fri Dec 22 17:12:27 CST 2017 in 1 milliseconds<br></span></div>
<span style="background-color:rgb(153,255,153);"></span></div>
<br></div>
</div>
<p></p>
            </div>
                </div>