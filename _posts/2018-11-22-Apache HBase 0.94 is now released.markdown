---
layout:     post
title:      Apache HBase 0.94 is now released
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:14px;line-height:20px;color:rgb(80,80,80);">
<span style="background-color:rgb(255,255,255);">Apache HBase 0.94.0 has been released! This is the first major release since the January 22nd HBase 0.92 release. In the HBase 0.94.0 release the main focuses were on performance enhancements and the addition
 of new features (Also, several major bug fixes).</span></p>
<h2 style="font-size:15px;line-height:17px;font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;">
<span style="background-color:rgb(255,255,255);">Performance Related JIRAs</span></h2>
<p style="font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:14px;line-height:20px;color:rgb(80,80,80);">
<span style="background-color:rgb(255,255,255);">Below are a few of the important performance related JIRAs:</span></p>
<ul style="font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:14px;line-height:20px;color:rgb(80,80,80);"><li title="HBASE-5074" style="list-style:disc;">
<span style="background-color:rgb(255,255,255);"><span style="font-style:inherit;color:rgb(47,43,44);">Read Caching improvements:</span> HDFS stores data in one block file and its corresponding metadata (checksum) in another block file. This means that every
 read into the HBase block cache may consume up to two disk ops, one to the datafile and one to the checksum file. <a title="HBASE-5074" href="https://issues.apache.org/jira/browse/HBASE-5074" rel="nofollow" style="text-decoration:none;color:rgb(1,126,197);">HBASE-5074</a>:
 “Support checksums in HBase block cache” adds a block level checksum in the HFile itself in order to avoid one disk op,  boosting up the read performance. This feature is <em>enabled</em> by default.</span></li><li style="list-style:disc;"><span style="background-color:rgb(255,255,255);"><span style="font-style:inherit;color:rgb(47,43,44);">Seek optimizations:</span> Till now, if there were several StoreFiles for a column family
 in a region, HBase would seek in each such files and merge the results, even if the row/column we are looking for is in the most recent file.  <a title="HBase-4465" href="https://issues.apache.org/jira/browse/HBASE-4465" rel="nofollow" style="text-decoration:none;color:rgb(1,126,197);">HBASE-4465</a>:
 “Lazy Seek optimization of StoreFile Scanners” optimizes scanner reads to read the<em> most recent</em> StoreFile first by <em>lazily seeking</em> the StoreFiles. This is achieved by introducing a fake keyvalue with its timestamp equal to the maximum timestamp
 present in the particular StoreFile. Thus, a disk seek is avoided until the KeyValueScanner for a StoreFile is bubbled up the heap, implying a need to do a real read operation.  This should provide a significant read performance boost, especially for IncrementColumnValue
 operations where we care only for latest value. This feature is <em>enabled</em> by default.</span></li><li style="list-style:disc;"><span style="background-color:rgb(255,255,255);"><span style="font-style:inherit;color:rgb(47,43,44);">Write to WAL optimizations: </span>HBase write throughput is upper bounded by the write rate
 of WAL where the log is replicated to a number of datanodes, depending on the replication factor. <a title="HBase-4608" href="https://issues.apache.org/jira/browse/HBASE-4608" rel="nofollow" style="text-decoration:none;color:rgb(1,126,197);">HBASE-4608</a>:
 “HLog Compression” adds a custom dictionary-based compression of HLogs for faster replication on HDFS datanodes, thus improving overall write rate for HBase. This feature is considered experimental and is<em>off</em> by default.</span></li></ul><h2 style="font-size:15px;line-height:17px;font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;">
<span style="background-color:rgb(255,255,255);">New Feature Related JIRAs</span></h2>
<p style="font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:14px;line-height:20px;color:rgb(80,80,80);">
<span style="background-color:rgb(255,255,255);">Here is a list of some of the important JIRAs related to adding new features:</span></p>
<ul style="font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:14px;line-height:20px;color:rgb(80,80,80);"><li style="list-style:disc;"><span style="background-color:rgb(255,255,255);"><span style="font-style:inherit;color:rgb(47,43,44);">More powerful first aid box:</span> The previous HBck tool did a good job of fixing inconsistencies
 related to region assignments but lacked some basic features like fixing orphaned regions, region holes, overlapping regions, etc. <a href="https://issues.apache.org/jira/browse/HBASE-5128" rel="nofollow" style="text-decoration:none;color:rgb(1,126,197);">HBASE-5128:
 “Uber hbck”</a>, adds these missing features to the first aid box.</span></li><li style="list-style:disc;"><span style="background-color:rgb(255,255,255);"><span style="font-style:inherit;color:rgb(47,43,44);">Simplified Region Sizing:</span> Deciding a region size is always tricky as it varies on
 a number of dynamic parameters such as data size, cluster size, workload, etc. <a title="HBase-4365" href="https://issues.apache.org/jira/browse/HBASE-4365" rel="nofollow" style="text-decoration:none;color:rgb(1,126,197);">HBASE-4365</a>: “Heuristic for Region
 size” adds a heuristic where it increases the split size threshold of a table region as the data grows, thus limiting the number of region splits.</span></li><li style="list-style:disc;"><span style="background-color:rgb(255,255,255);"><span style="font-style:inherit;color:rgb(47,43,44);">Smarter transaction semantics: </span>Though HBase supports single row level transaction,
 if there are a number of updates (Puts/Deletes) to an individual row, it will lock the row for each of these operations.<a title="HBase-3584" href="https://issues.apache.org/jira/browse/HBASE-3584" rel="nofollow" style="text-decoration:none;color:rgb(1,126,197);">HBASE-3584</a>:
 “Atomic Put &amp; Delete in a single transaction” enhances the HBase single row locking semantics by allowing Puts and Deletes on a row to be executed in a single call. This feature is <em>on</em> by default.</span></li></ul><p style="font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:14px;line-height:20px;color:rgb(80,80,80);">
<span style="background-color:rgb(255,255,255);">This major release has a number of new features and bug fixes; a total of <a title="397 resolved jiras" href="https://issues.apache.org/jira/secure/IssueNavigator.jspa?reset=true&amp;jqlQuery=project+%3D+HBASE+AND+fixVersion+%3D+%220.94.0%22+AND+resolution+%3D+Fixed+ORDER+BY+priority+DESC&amp;mode=hide" rel="nofollow" style="text-decoration:none;color:rgb(1,126,197);">397
 resolved JIRAs</a> with 140 enhancements and 180 bug fixes. It is compatible with 0.92. This opens up a window of opportunity to backport some of the cool features back in CDH4, which is based on the 0.92 branch.</span></p>
<h2 style="font-size:15px;line-height:17px;font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;">
<span style="background-color:rgb(255,255,255);">Acknowledgements</span></h2>
<p style="font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:14px;line-height:20px;color:rgb(80,80,80);">
<span style="background-color:rgb(255,255,255);">Thanks to everyone who contributed to this release and a hat tip to Lars Hofhansl of Salesforce for being the release manager.</span></p>
<p style="font-family:'Lucida Grande CY', 'Lucida Grande', 'Lucida Sans', Helvetica, Arial, sans-serif;font-size:14px;line-height:20px;color:rgb(80,80,80);">
<span style="background-color:rgb(255,255,255);">Ref: <a href="http://www.cloudera.com/blog/2012/05/apache-hbase-0-94-is-now-released/" rel="nofollow">http://www.cloudera.com/blog/2012/05/apache-hbase-0-94-is-now-released/</a></span></p>
            </div>
                </div>