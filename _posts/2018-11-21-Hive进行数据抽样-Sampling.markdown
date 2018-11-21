---
layout:     post
title:      Hive进行数据抽样-Sampling
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
原文链接：http://superlxw1234.iteye.com/blog/2233160?utm_source=tuicool&amp;utm_medium=referral</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
关键字：Hive数据取样、Hive Sampling、Hive TABLESAMPLE.</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
在Hive中提供了数据取样（SAMPLING）的功能，用来从Hive表中根据一定的规则进行数据取样，Hive中的数据取样支持分桶表取样和数据块取样。</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
 </p>
<h2 style="line-height:1.5em;font-family:Helvetica, Tahoma, Arial, sans-serif;">
16.1 数据块取样（Block Sampling）</h2>
<ul style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;"><li><span style="color:rgb(255,0,255);"><strong>block_sample: TABLESAMPLE (n PERCENT)</strong></span></li></ul><p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
根据输入的inputSize，取样n%。</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
比如：输入大小为1G，TABLESAMPLE (50 PERCENT)将会取样512M的数据；</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
看例子：</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
表lxw1总大小约为64816816，总记录数为：2750714</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
<img height="334" alt="Hive sampling" width="498" src="http://7xipth.com1.z0.glb.clouddn.com/0806-1.jpg" class="aligncenter" style="border:0px;"></p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
使用下面的语句，从表lxw111中取样50%的数据，创建一个新表：</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
<strong>CREATE TABLE lxw1234 AS</strong></p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
<strong>SELECT * FROM lxw1 <span style="color:rgb(255,0,0);">TABLESAMPLE (50 PERCENT)</span>;</strong></p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
完成后看看表lxw1234的记录数和大小：</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
<img height="342" alt="Hive sampling" width="499" src="http://7xipth.com1.z0.glb.clouddn.com/0806-2.jpg" class="aligncenter" style="border:0px;"></p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
结果表记录数：1376390，总大小：32432626，基本上是原表的50%。</p>
<ul style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;"><li><span style="color:rgb(255,0,255);"><strong>block_sample: TABLESAMPLE (nM)</strong></span></li></ul><p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
这种方式指定取样数据的大小，单位为M。</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
比如，下面的语句：</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
<strong>CREATE TABLE lxw1234_2 AS</strong></p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
<strong>SELECT * FROM lxw1 <span style="color:rgb(255,0,0);">TABLESAMPLE (30M)</span>;</strong></p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
将会从表lxw1中取样30M的数据：</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
<img height="359" alt="Hive sampling" width="526" src="http://7xipth.com1.z0.glb.clouddn.com/0806-3.jpg" class="aligncenter" style="border:0px;"></p>
<ul style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;"><li><span style="color:rgb(255,0,255);"><strong>block_sample: TABLESAMPLE (n ROWS)</strong></span></li></ul><p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
这种方式可以根据行数来取样，但要特别注意：这里指定的行数，是在每个InputSplit中取样的行数，也就是，每个Map中都取样n ROWS。</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
下面的语句：<br><strong>SELECT COUNT(1) FROM (SELECT * FROM lxw1 <span style="color:rgb(255,0,0);">TABLESAMPLE (200 ROWS)</span>) x;</strong></p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
<img height="365" alt="Hive sampling" width="534" src="http://7xipth.com1.z0.glb.clouddn.com/0806-4.jpg" class="aligncenter" style="border:0px;"></p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
有5个Map Task（InputSplit），每个取样200行，一共1000行。</p>
<h2 style="line-height:1.5em;font-family:Helvetica, Tahoma, Arial, sans-serif;">
16.2 分桶表取样（Sampling Bucketized Table）</h2>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
关于Hive中的分桶表（Bucket Table），在以后的文章中将会介绍，其实就是根据某一个字段Hash取模，放入指定数据的桶中，比如将表lxw1234按照ID分成100个桶，其算 法是hash(id) % 100，这样，hash(id) % 100 = 0的数据被放到第一个桶中，hash(id) % 100 = 1的记录被放到第二个桶中。分桶表在创建时候使用CLUSTER BY语句创建。</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
 </p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
Hive中分桶表取样的语法是：</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
<strong>table_sample: TABLESAMPLE (BUCKET x OUT OF y [ON colname])</strong></p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
其中x是要抽样的桶编号，桶编号从1开始，colname表示抽样的列，y表示桶的数量。</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
<span style="color:rgb(255,0,0);"><strong>例子1：</strong></span></p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
<strong>SELECT COUNT(1)</strong></p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
<strong>FROM lxw1 <span style="color:rgb(255,0,255);">TABLESAMPLE (BUCKET 1 OUT OF 10 ON rand())</span>;</strong></p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
该语句表示将表lxw1随机分成10个桶，抽样第一个桶的数据；</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
前面介绍过，表lxw1总大小约为<strong>64816816</strong>，总记录数为：<strong>2750714</strong></p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
<img height="455" alt="Hive sampling" width="542" src="http://7xipth.com1.z0.glb.clouddn.com/0806-5.jpg" class="aligncenter" style="border:0px;"></p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
出来的结果基本上是原表的十分之一，注意：这个结果每次运行是不一样的，因为是按照随机数进行分桶取样的。</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
<span style="color:rgb(255,0,0);"><strong>例子2</strong>：</span></p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
如果基于一个已经分桶表进行取样，将会更有效率。</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
执行下面的语句，创建一个分桶表，并插入数据：</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
<strong>CREATE TABLE lxw1_bucketed (pcid STRING)</strong></p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
<strong>CLUSTERED BY(pcid) INTO 10 BUCKETS;</strong></p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
 </p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
<strong>INSERT overwrite TABLE lxw1_bucketed</strong></p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
<strong>SELECT pcid FROM lxw1;</strong></p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
表lxw1_bucketed按照pcid字段分成10个桶，下面的语句表示从10个桶中抽样第一个桶的数据：</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
SELECT COUNT(1) FROM lxw1_bucketed TABLESAMPLE(<span style="color:rgb(255,0,255);"><strong>BUCKET 1 OUT OF 10</strong></span> ON pcid);</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
很好理解。</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
再看这个：</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
SELECT COUNT(1) FROM lxw1_bucketed TABLESAMPLE(<span style="color:rgb(255,0,255);"><strong>BUCKET 1 OUT OF 20</strong></span> ON pcid)</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
表只有10个桶，如果指定20，看结果：</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
<img height="348" alt="Hive sampling" width="541" src="http://7xipth.com1.z0.glb.clouddn.com/0806-6.jpg" class="aligncenter" style="border:0px;"></p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
结果差不多是源表记录的1/20，Hive在运行时候，会在第一个桶中抽样一半的数据。</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
<strong>还有一点：</strong></p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
如果从源表中直接分桶抽样，也能达到一样的效果，比如：</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
SELECT COUNT(1) FROM lxw1 TABLESAMPLE(BUCKET 1 OUT OF 20 ON pcid);</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
区别在于基于已经分桶的表抽样，查询只会扫描相应桶中的数据，而基于未分桶表的抽样，查询时候需要扫描整表数据，先分桶，再抽样。</p>
<p style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:25.2px;">
其它更详细的用法请参考Hive的官方文档说明。</p>
            </div>
                </div>