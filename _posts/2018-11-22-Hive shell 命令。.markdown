---
layout:     post
title:      Hive shell 命令。
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <strong><span style="font-size:18pt;">Hive shell 命令。</span></strong>
<p>　　连接 hive shell</p>
<p>　　直接输入 hive</p>
<p>　<strong>　1、显示表</strong></p>
<p>　　hive&gt; show tables; </p>
<p>　　OK </p>
<p>　　test </p>
<p>　　Time taken: 0.17 seconds, Fetched: 1 row(s)</p>
<p>　<strong>　2、创建表</strong></p>
<p>　　hive&gt; create table test(key string); </p>
<p>　　OK </p>
<p>　　Time taken: 0.265 seconds</p>
<p><strong>　　3、创建分区表：</strong></p>
<p>　　hive&gt; create table logs(ts bigint,line string) partitioned by (dt String,country String);</p>
<p><strong>　　4、加载分区表数据：</strong></p>
<p>　　hive&gt; load data local inpath '/home/Hadoop/input/file1' into table logs partition (dt='2014-03-11',country='CN');</p>
<p>　<strong>　5、展示表中有多少分区：</strong></p>
<p>　　hive&gt; show partitions logs; </p>            </div>
                </div>