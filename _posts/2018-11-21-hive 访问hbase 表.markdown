---
layout:     post
title:      hive 访问hbase 表
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/jjshouji/article/details/78434064				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>1.背景</p>
<p><span></span>大部分同学可能不会用hbase ，或者用hbase 不习惯，但是对sql 却很熟悉，比如要统计表的记录数，用sql 可能知道怎么写，用hbase 可能不知道，或者知道但是很慢，性能有问题等，这时我们可以通过hive 建立与hbase 表的关联关系没映射hbase 表到hive。</p>
<p><br></p>
<p>2.建hive表</p>
<p><br></p>
<p>2.1 hbase 表</p>
<p>  hbase 已存在product 表三个列簇EXT(id ,desc )，computer(name,price)，food (name ,price)没个列簇有多个列</p>
<p></p>
<pre><code class="language-plain">hbase(main):007:0&gt; desc 'product'
Table product is ENABLED
product
COLUMN FAMILIES DESCRIPTION
{NAME =&gt; 'EXT', BLOOMFILTER =&gt; 'ROW', VERSIONS =&gt; '1', IN_MEMORY =&gt; 'false', KEEP_DELETED_CELLS =&gt; 'FALSE', DATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; 'FOREVER', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', BLOCKCACHE =&gt; 'true', BLOCKSIZE =&gt; '65536', REPLICATION_SCOPE =&gt; '0'}
{NAME =&gt; 'computer', BLOOMFILTER =&gt; 'ROW', VERSIONS =&gt; '5', IN_MEMORY =&gt; 'false', KEEP_DELETED_CELLS =&gt; 'FALSE', DATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; 'FOREVER', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', BLOCKCACHE =&gt; 'true', BLOCKSIZE =&gt; '65536', REPLICATION_SCOPE =&gt; '0'}
{NAME =&gt; 'food', BLOOMFILTER =&gt; 'ROW', VERSIONS =&gt; '7', IN_MEMORY =&gt; 'false', KEEP_DELETED_CELLS =&gt; 'FALSE', DATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; 'FOREVER', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', BLOCKCACHE =&gt; 'true', BLOCKSIZE =&gt;'65536', REPLICATION_SCOPE =&gt; '0'}
3 row(s) in 0.3780 seconds</code></pre><br><br><p></p>
<p>有4条记录如下：</p>
<p></p>
<pre><code class="language-plain">hbase(main):009:0&gt; scan 'product'
ROW               COLUMN+CELL
 rowkey001        column=EXT:desc, timestamp=1509610518837, value=ext1
 rowkey001        column=EXT:id, timestamp=1509610507780, value=1
 rowkey001        column=computer:name, timestamp=1507369786304, value=ThinkPad E550
 rowkey001        column=computer:price, timestamp=1509609996387, value=4200
 rowkey001        column=food:name, timestamp=1507370035248, value=orange
 rowkey001        column=food:price, timestamp=1507371767465, value=10
 rowkey002        column=EXT:desc, timestamp=1509610896463, value=buy at 2017-11-03
 rowkey002        column=EXT:id, timestamp=1509610861714, value=0001
 rowkey002        column=computer:name, timestamp=1509610842778, value=ACER S6720
 rowkey002        column=computer:price, timestamp=1509610793222, value=4100
 rowkey002        column=food:name, timestamp=1509678372445, value=apple
 rowkey002        column=food:price, timestamp=1509610776529, value=12
 rowkey003        column=EXT:desc, timestamp=1509617309557, value=MAC product
 rowkey003        column=EXT:id, timestamp=1509617309613, value=00001
 rowkey003        column=computer:name, timestamp=1509617309642, value=MAC S6826
 rowkey003        column=computer:price, timestamp=1509617309706, value=6500
 rowkey003        column=food:name, timestamp=1509678262882, value=mangosteen
 rowkey003        column=food:price, timestamp=1509617309740, value=6.5
 rowkey004        column=EXT:desc, timestamp=1509618190303, value=ext0001
 rowkey004        column=EXT:id, timestamp=1509618190345, value=Pera
 rowkey004        column=computer:name, timestamp=1509618190371, value=AIGO S60
 rowkey004        column=computer:price, timestamp=1509618190401, value=2500
 rowkey004        column=food:name, timestamp=1509618192426, value=Pear
 rowkey004        column=food:price, timestamp=1509618190478, value=13.2
4 row(s) in 0.4020 seconds
</code></pre><br>
hive 建外部表
<p></p>
<p></p>
<pre><code class="language-plain">create external table product
(
rowkey varchar(100),
fname  varchar(100),
fprice float,
cname  varchar(100),
cprice float,
eid varchar(100),
edesc varchar(100)
) 
STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler' 
WITH SERDEPROPERTIES
("hbase.columns.mapping" = 
":key,food:name,food:price,computer:name,computer:price,EXT:id,EXT:desc")
TBLPROPERTIES("hbase.table.name" = "product");</code></pre><br><br><p></p>
<p></p>
说明：hbase.columns.mapping 字段与建表字段顺序一致。<br><br>
3.查看hive表
<p></p>
<p></p>
<pre><code class="language-plain">hive&gt; select * from product;
OK
rowkey001       orange  10.0    ThinkPad E550   4200.0  1       ext1
rowkey002       apple   12.0    ACER S6720      4100.0  0001    buy at 2017-11-03
rowkey003       mangosteen      6.5     MAC S6826       6500.0  00001   MAC product
rowkey004       Pear    13.2    AIGO S60        2500.0  Pera    ext0001
Time taken: 0.508 seconds, Fetched: 4 row(s)</code></pre><br><br><p></p>
<p><br></p>
4.变成本地表
<p></p>
<pre><code class="language-plain">hive&gt; create table  product_1 as  select * from product;
WARNING: Hive-on-MR is deprecated in Hive 2 and may not be available in the future versions. Consider using a different execution engine (i.e. spark, tez) or using Hive 1.X releases.
Query ID = hadoop_20171103112618_d1137b8c-7d4c-4bb5-b60f-1cc20c325581
Total jobs = 3
Launching Job 1 out of 3
Number of reduce tasks is set to 0 since there's no reduce operator
Starting Job = job_1509675916323_0003, Tracking URL = http://master:18088/proxy/application_1509675916323_0003/
Kill Command = /home/hadoop/hadoop-2.8.1//bin/hadoop job  -kill job_1509675916323_0003
Hadoop job information for Stage-1: number of mappers: 1; number of reducers: 0
2017-11-03 11:26:53,261 Stage-1 map = 0%,  reduce = 0%
2017-11-03 11:27:16,905 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 3.51 sec
MapReduce Total cumulative CPU time: 3 seconds 510 msec
Ended Job = job_1509675916323_0003
Stage-4 is selected by condition resolver.
Stage-3 is filtered out by condition resolver.
Stage-5 is filtered out by condition resolver.
Moving data to directory hdfs://master:9000/user/hive/warehouse/.hive-staging_hive_2017-11-03_11-26-18_130_15224692176553118-1/-ext-10002
Moving data to directory hdfs://master:9000/user/hive/warehouse/product_1
MapReduce Jobs Launched: 
Stage-Stage-1: Map: 1   Cumulative CPU: 3.51 sec   HDFS Read: 5736 HDFS Write: 296 SUCCESS
Total MapReduce CPU Time Spent: 3 seconds 510 msec
OK

</code></pre>
<p></p>
<pre></pre>
查看数据：
<p></p>
<p></p>
<pre><code class="language-plain">hive&gt; select * from product_1;
OK
rowkey001       orange  10.0    ThinkPad E550   4200.0  1       ext1
rowkey002       apple   12.0    ACER S6720      4100.0  0001    buy at 2017-11-03
rowkey003       mangosteen      6.5     MAC S6826       6500.0  00001   MAC product
rowkey004       Pear    13.2    AIGO S60        2500.0  Pera    ext0001
Time taken: 0.155 seconds, Fetched: 4 row(s)</code></pre><br><br><p></p>
<p>外部表的数据会跟随hbase 表中数据变化而变化，但是hive本地表不是，是固化的。</p>
<p></p>
<pre><code class="language-plain">hbase 修改rowkey002 food name为apple1;
--------------------------------------------
hbase(main):010:0&gt; put 'product','rowkey002','food:name','apple1'
0 row(s) in 0.3230 seconds

hive&gt; select * from product;
OK
rowkey001       orange  10.0    ThinkPad E550   4200.0  1       ext1
rowkey002       apple1  12.0    ACER S6720      4100.0  0001    buy at 2017-11-03
rowkey003       mangosteen      6.5     MAC S6826       6500.0  00001   MAC product
rowkey004       Pear    13.2    AIGO S60        2500.0  Pera    ext0001
Time taken: 0.728 seconds, Fetched: 4 row(s)
hive&gt; select * from product_1;
OK
rowkey001       orange  10.0    ThinkPad E550   4200.0  1       ext1
rowkey002       apple   12.0    ACER S6720      4100.0  0001    buy at 2017-11-03
rowkey003       mangosteen      6.5     MAC S6826       6500.0  00001   MAC product
rowkey004       Pear    13.2    AIGO S60        2500.0  Pera    ext0001
Time taken: 0.425 seconds, Fetched: 4 row(s)</code></pre>
            </div>
                </div>