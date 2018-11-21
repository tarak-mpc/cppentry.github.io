---
layout:     post
title:      HBase replication
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>exportSnapshot：</p>
<p>nohup ./hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot sdsv1_2vd6_snapshot -mappers 9 -copy-to hdfs://azbjsrv-galaxy-ssd/hbase/azbjsrv-galaxy-ssd -copy-from hdfs://lgsrv-galaxy/hbase/lgsrv-galaxy &gt;2vd6.log 2&gt;&amp;1 &amp;</p>
<p>verifySnapshot：</p>
<p>./hbase org.apache.hadoop.hbase.mapreduce.replication.VerifyReplication --scanrate=20 1 sdsv1_3u7d<br></p>
            </div>
                </div>