---
layout:     post
title:      Hbase&Phoenix学习笔记
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/sinat_36121406/article/details/82986350				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<ol>
<li>
<p>查看某张Hbase表在HDFS中的存储情况</p>
<pre><code>hadoop fs -ls /apps/hbase/data/data/h3c/log_suspect_track_history_spark/hadoop fs -	ls /apps/hbase/data/data/h3c/log_suspect_track_history_spark/
</code></pre>
</li>
<li>
<p>统计某张Hbase表中ROW_KEY的分布情况</p>
<pre><code>./hbase org.apache.hadoop.hbase.io.hfile.HFile -m -f /apps/hbase/data/data/h3c/log_suspect_track_history_spark/1d2b17364b1a9c4318c3451ec3188e5d/0/b71e1f76942c42b3afdf97d78b9792c6
</code></pre>
<p>统计结果如下：</p>
<pre><code>-sh-4.1$ ./hbase org.apache.hadoop.hbase.io.hfile.HFile -m -f 	/apps/hbase/data/data/h3c/log_suspect_track_history_spark/1d2b17364b1a9c4318c34	51ec3188e5d/0/b71e1f76942c42b3afdf97d78b9792c6
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
	cacheConf=CacheConfig:disabled,    		firstKey=000170108991121015201810091547320201/0:alert_flag/1539071434002/Put,
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
	hfile.LASTKEY = 	\x00$X95370502002231015201810091547350201\x010y_coordinate\x00\x00\x01fW\xCE\xAD\xAC\x04
Mid-key: 	\x00$555711114991000112201810091547280201\x010q\x7F\xFF\xFF\xFF\xFF\xFF\xFF\xFF\xFF
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

</code></pre>
</li>
<li>
<p>常用Phoenix查询语句</p>
<pre><code># 模糊匹配
select * from "TEST" where "row_key" LIKE '%ff1f1c50a607c0f9174c39312a302a17%';

# 字符串截取
select * from "TEST" where substr("row_key",34,14) = '20181010155623';

# 范围查询
select * from "TEST" where substr("row_key",34,14) &gt; '20181010155620' and substr("row_key",34,14) &lt; '20181010155625';
</code></pre>
</li>
</ol>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>