---
layout:     post
title:      Start Hbase
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
 
<p><strong>Some Research of HBase </strong></p>
<p><strong> </strong></p>
<p><strong>1.    The Architecture of HBase</strong></p>
<p> </p>
<p>There are three major components of the HBase architecture:</p>
<p>1.         The HBaseMaster (analogous to the Bigtable master server)</p>
<p>2.         The HRegionServer (analogous to the Bigtable tablet server)</p>
<p>3.         The HBase client, defined by org.apache.hadoop.hbase.client.HTable</p>
<p align="center">The Architecture of HBase</p>
<p> </p>
<p><strong>1)     HBase Master duties:</strong></p>
<p>l         Cluster initialization </p>
<p>l         Assigning/unassigning regions to/from HRegionServers (unassigning is for load balance)</p>
<p>l         Monitor the health and load of each HRegionServer </p>
<p>l         Changes to the table schema and handling table administrative functions</p>
<p>l         Data localization </p>
<p> </p>
<p><strong>2)     HBase Region Server duties:</strong></p>
<p>l         Serving HRegions assigned to HRegionServer </p>
<p>l         Handling client read and write requests </p>
<p>l         Flushing cache to HDFS </p>
<p>l         Keeping HLog </p>
<p>l         Compactions </p>
<p>l         Region Splits </p>
<p> </p>
<p><strong>3)      HBase Client</strong></p>
<p>HBase is a Heavy Client System. Each client manages its own connection to appropriate server</p>
<p><strong>2.    The process of commit and update data.</strong></p>
<p> <img src="http://hi.csdn.net/attachment/201108/24/0_1314172846i0uC.gif" alt=""></p>
<p align="center">The process of commit and update data.</p>
<p> </p>
<p><strong>Write Requests</strong></p>
<p><strong> </strong></p>
<p>When a write request is received, it is first written to a write-ahead log called a HLog. All write requests for every region the region server is serving are written to the same log. Once the request has been written to the HLog, it is stored in an in-memory
 cache called the Memcache. There is one Memcache for each HStore.</p>
<p> </p>
<p><strong>Read Requests</strong></p>
<p><strong> </strong></p>
<p>Reads are handled by first checking the Memcache and if the requested data is not found, the MapFiles are searched for results.</p>
<p> </p>
<p><strong>Cache Flushes</strong></p>
<p> </p>
<p>When the Memcache reaches a configurable size, it is flushed to disk, creating a new MapFile and a marker is written to the HLog, so that when it is replayed, log entries before the last flush can be skipped. A flush may also be triggered to relieve memory
 pressure on the region server.</p>
<p> </p>
<p>Cache flushes happen concurrently with the region server processing read and write requests. Just before the new MapFile is moved into place, reads and writes are suspended until the MapFile has been added to the list of active MapFiles for the HStore.</p>
<p> </p>
<p> </p>
<p> </p>
<p><strong>3.    Basic Operation of HBase Table</strong></p>
<p> </p>
<table cellspacing="0" cellpadding="0" border="1"><tbody><tr><td valign="top">
<p>Operation</p>
</td>
<td valign="top">
<p>HBase shell Command</p>
</td>
</tr><tr><td valign="top">
<p>Create Table</p>
</td>
<td valign="top">
<p>create 't1', {NAME =&gt; 'f1', VERSIONS =&gt; 5}</p>
</td>
</tr><tr><td valign="top">
<p>Add the column family</p>
</td>
<td valign="top">
<p>alter 't1', {NAME =&gt; 'f1', VERSIONS =&gt; 5}</p>
</td>
</tr><tr><td valign="top">
<p>Delete the column family</p>
</td>
<td valign="top">
<p>alter 't1', {NAME =&gt; 'f1', METHOD =&gt; 'delete'}</p>
</td>
</tr><tr><td valign="top">
<p>Get row</p>
</td>
<td valign="top">
<p>get 't1', 'r1'</p>
</td>
</tr><tr><td valign="top">
<p>Get cell content</p>
</td>
<td valign="top">
<p>get 't1', 'r1', {COLUMN =&gt; 'c1', TIMESTAMP =&gt; ts1}</p>
</td>
</tr><tr><td valign="top">
<p>List all tables</p>
</td>
<td valign="top">
<p>list</p>
</td>
</tr><tr><td valign="top">
<p>Count the table</p>
</td>
<td valign="top">
<p>count ‘t1’</p>
</td>
</tr><tr><td valign="top">
<p>scan</p>
</td>
<td valign="top">
<p>Scanner specifications may include one or more of the following: LIMIT, STARTROW, STOPROW, TIMESTAMP, or COLUMNS:</p>
<p>1.      scan '.META.', {COLUMNS =&gt; 'info:regioninfo'}</p>
<p>2.      scan 't1', {COLUMNS =&gt; ['c1', 'c2'], LIMIT =&gt; 10, \</p>
<p>             STARTROW =&gt; 'xyz'}</p>
</td>
</tr></tbody></table><p> </p>
<p> </p>
<p><strong>4.    Check the status:</strong></p>
<p>1)      Check the DFS namenode status: <a href="/dfshealth.jsp" rel="nofollow">
http://{DFSNameNodeIP}:50070/dfshealth.jsp</a></p>
<p>2)      Check the HBase status:  http://{masterServerIP}:60010/master.jsp </p>
<p> </p>
<p><strong>5.    Meet some error and the solve method:</strong></p>
<table cellspacing="0" cellpadding="0" align="left" border="1"><tbody><tr><td valign="top">
<p>Error</p>
</td>
<td valign="top">
<p>Root</p>
</td>
<td valign="top">
<p>Resolve</p>
</td>
</tr><tr><td valign="top">
<p>org.apache.hadoop.hdfs.server.datanode.DataNode: java.io.IOException: Incompatible namespaceIDs in /tmp/hadoop-root/dfs/data: namenode namespaceID = 1743665947; datanode namespaceID = 352063137</p>
</td>
<td valign="top">
<p>Incompatible namespaceIDs</p>
</td>
<td valign="top">
<p>rm –rf /tmp/*</p>
</td>
</tr><tr><td valign="top">
<p>NativeException: org.apache.hadoop.hbase.TableNotDisabledException: org.apache.hadoop.hbase.TableNotDisabledException: t1</p>
</td>
<td valign="top">
<p>Before alter the column family , you need to disable the table first</p>
</td>
<td valign="top">
<p>Hbase&gt;disable ‘t1’</p>
</td>
</tr></tbody></table><p><strong> </strong></p>
<p><strong>6.    How to deploy the HBase</strong></p>
<p><a title="blocked::http://hi.baidu.com/webcell/blog/item/83ee17303e7d2391a8018e5d.html/cmtid/1ae33bfa2d7bfe14a9d311b5" href="http://hi.baidu.com/webcell/blog/item/83ee17303e7d2391a8018e5d.html/cmtid/1ae33bfa2d7bfe14a9d311b5" rel="nofollow">http://hi.baidu.com/webcell/blog/item/83ee17303e7d2391a8018e5d.html/cmtid/1ae33bfa2d7bfe14a9d311b5</a></p>
<p><strong> </strong></p>
<p><strong>7.    How to turning the HBase Performance</strong></p>
<p><a href="http://wiki.apache.org/hadoop/PerformanceTuning" rel="nofollow">http://wiki.apache.org/hadoop/PerformanceTuning</a></p>
<p><strong> </strong></p>
            </div>
                </div>