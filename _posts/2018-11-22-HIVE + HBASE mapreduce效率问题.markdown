---
layout:     post
title:      HIVE + HBASE mapreduce效率问题
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>HADOOP 0.20.203</p>
<p>HBASE 0.90.3</p>
<p>HIVE 0.7.1</p>
<p> </p>
<p>3 DATA node </p>
<p> </p>
<p>200万数据，比较HIVE+ HDFS 与 HBASE +HDFS +HIVE的统计。</p>
<p>hive在HBASE中作统计时间长很多，10倍了。</p>
<p> </p>
<p>hive已调整:</p>
<p>&lt;property&gt;<br>
      &lt;name&gt;hive.mapjoin.bucket.cache.size&lt;/name&gt;<br>
      &lt;value&gt;10000&lt;/value&gt;</p>
<p> </p>
<p> </p>
<p>研究中</p>            </div>
                </div>