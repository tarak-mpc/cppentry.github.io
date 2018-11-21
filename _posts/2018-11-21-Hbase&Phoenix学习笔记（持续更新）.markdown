---
layout:     post
title:      Hbase&Phoenix学习笔记（持续更新）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/lijingjingchn/article/details/83015302				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h4><a id="1_HbaseHDFS_0"></a>1. 查看某张Hbase表在HDFS中的存储情况</h4>
<pre><code>```
hadoop fs -ls /apps/hbase/data/data/h3c/log_suspect_track_history_spark/hadoop fs -	ls /apps/hbase/data/data/h3c/log_suspect_track_history_spark/
```
</code></pre>
<h4><a id="2_HbaseROW_KEY_5"></a>2. 统计某张Hbase表中ROW_KEY的分布情况</h4>
<pre><code>```
./hbase org.apache.hadoop.hbase.io.hfile.HFile -m -f /apps/hbase/data/data/h3c/log_suspect_track_history_spark/1d2b17364b1a9c4318c3451ec3188e5d/0/b71e1f76942c42b3afdf97d78b9792c6
```
统计结果如下：

```
-sh-4.1$ ./hbase org.apache.hadoop.hbase.io.hfile.HFile -m -f 				/apps/hbase/data/data/h3c/log_suspect_track_history_spark/1d2b17364b1a9c4318c34	51ec3188e5d/0/b71e1f76942c42b3afdf97d78b9792c6
log4j:WARN No such property [maxBackupIndex] in org.apache.log4j.ConsoleAppender.
log4j:WARN No such property [maxFileSize] in org.apache.log4j.ConsoleAppender.
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/usr/hdp/2.3.4.0-3485/hbase/lib/slf4j-log4j12-1.7.10.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/usr/hdp/2.3.4.0-3485/hadoop/lib/slf4j-log4j12-1.7.10.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/usr/hdp/2.3.4.0-3485/zookeeper/lib/slf4j-log4j12-1.6.1.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [org.slf4j.impl.Log4jLoggerFactory]
2018-10-09 18:04:52,556 INFO  [main] hfile.CacheConfig: CacheConfig:disabled
2018-10-09 18:04:52,558 INFO  [main] hfile.CacheConfig: CacheConfig:disabled
Block index size as per heapsize: 2104
reader=/apps/hbase/data/data/h3c/log_suspect_track_history_spark/1d2b17364b1a9c4318c3451ec3188e5d/0/b71e1f76942c42b3afdf97d78b9792c6,
	compression=none,
	cacheConf=CacheConfig:disabled,    				firstKey=000170108991121015201810091547320201/0:alert_flag/1539071434002/Put,
lastKey=X95370502002231015201810091547350201/0:y_coordinate/1539071454636/Put,
	avgKeyLen=60,
	avgValueLen=10,
	entries=14300,
	length=397422
Trailer:
	fileinfoOffset=392595,
	loadOnOpenDataOffset=391379,
	dataIndexCount=18,
	metaIndexCount=0,
	totalUncomressedBytes=396087,
	entryCount=14300,
	compressionCodec=NONE,
	uncompressedDataIndexSize=1142,
	numDataIndexLevels=1,
	firstDataBlockOffset=0,
	lastDataBlockOffset=382814,
	comparatorClassName=org.apache.hadoop.hbase.KeyValue$KeyComparator,
	encryptionKey=NONE,
	majorVersion=3,
minorVersion=0
Fileinfo:
	BLOOM_FILTER_TYPE = ROW
	DATA_BLOCK_ENCODING = FAST_DIFF
	DELETE_FAMILY_COUNT = \x00\x00\x00\x00\x00\x00\x00\x00
	EARLIEST_PUT_TS = \x00\x00\x01fW\xB1OI
	KEY_VALUE_VERSION = \x00\x00\x00\x01
	LAST_BLOOM_KEY = X95370502002231015201810091547350201
	MAJOR_COMPACTION_KEY = \x00
	MAX_MEMSTORE_TS_KEY = \x00\x00\x00\x00\x00\x00\x00S
	MAX_SEQ_ID_KEY = 84
	TIMERANGE = 1539069529929....1539071454636
	hfile.AVG_KEY_LEN = 60
	hfile.AVG_VALUE_LEN = 10
	hfile.CREATE_TIME_TS = \x00\x00\x01fW\xE8\x892
	hfile.LASTKEY = 		\x00$X95370502002231015201810091547350201\x010y_coordinate\x00\x00\x01fW\xCE\xA	D\xAC\x04
Mid-key: 		\x00$555711114991000112201810091547280201\x010q\x7F\xFF\xFF\xFF\xFF\xFF\xFF\xFF\	xFF
Bloom filter:
	BloomSize: 2048
	No of Keys in bloom: 1100
	Max Keys for bloom: 1707
	Percentage filled: 64%
	Number of chunks: 1
	Comparator: RawBytesComparator
Delete Family Bloom filter:
	Not present

# -----------------------------------
# avgKeyLen=60,    avgValueLen=10, 说明 ROW_KEY 长度相对 value 较长
```
</code></pre>
<h4><a id="3_Phoenix_77"></a>3. 常用Phoenix操作语句</h4>
<pre><code>```
# 常用查询语句
# =========================================================================
# 模糊匹配
select * from "TEST" where "row_key" LIKE '%ff1f1c50a607c0f9174c39312a302a17%';

# 字符串截取
select * from "TEST" where substr("row_key",34,14) = '20181010155623';

# 范围查询
select * from "TEST" where substr("row_key",34,14) &gt; '20181010155620' and substr("row_key",34,14) &lt; '20181010155625';

# 索引操作语句
# =========================================================================
# 索引删除
drop index INDEX_TEST on TEST;

# 索引重建
alter index INDEX_TEST on TEST rebuild;

```
</code></pre>
<h4><a id="4_HbaseColumnFamily_101"></a>4. 关于Hbase的ColumnFamily</h4>
<p>每次Memstore Flush，会为每个CF都创建一个新的HFile。这样，不同CF中数据量的不均衡将会导致产生过多HFile：当其中一个CF的Memstore达到阈值flush时，所有其他CF的也会被flush。如上所述，太频繁的flush以及过多的HFile将会影响集群性能。<br>
&gt;<strong>注意：</strong><br>
很多情况下，一个CF是最好的设计。</p>
<h4><a id="5_HbaseRegion_106"></a>5. Hbase中的Region的分布</h4>
<p>Region在Hbase中是以负载均衡的方式分布在RegionServer上的。</p>
<h4><a id="6_Hbase_109"></a>6. Hbase中的列族</h4>
<p>每次Memstore Flush，会为每个CF都创建一个新的HFile。这样，不同CF中数据量的不均衡将会导致产生过多HFile：当其中一个CF的Memstore达到阈值flush时，所有其他CF的也会被flush。如上所述，太频繁的flush以及过多的HFile将会影响集群性能。</p>
<pre><code>&gt; 提示：很多情况下，一个CF是最好的设计。
</code></pre>
<h4><a id="7_Compression__Memstore_Flush_114"></a>7. Compression &amp; Memstore Flush</h4>
<p>HBase建议压缩存储在HDFS上的数据(比如HFiles)。除了节省硬盘空间，同样也会显著地减少硬盘和网络IO。使用压缩，当Memstore flush并将数据写入HDFS时候，数据会被压缩。压缩不会减慢多少flush的处理过程，却会大大减少以上所述问题，例如因为Memstore变大(超过 upper limit)而引起的“写阻塞”等等。</p>
<blockquote>
<p>提示：压缩库建议使用Snappy。</p>
</blockquote>
<h4><a id="8_phoenix_119"></a>8. phoenix中不会插入重复数据</h4>
<p>在phoenix中插入语句并不会像传统数据库一样存在重复数据。因为Phoenix是构建在HBase之上的，也就是必须存在一个主键。后面插入的会覆盖前面的，但是时间戳不一样。</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>