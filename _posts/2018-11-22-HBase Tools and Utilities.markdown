---
layout:     post
title:      HBase Tools and Utilities
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/wl101yjx/article/details/52748370				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:14px;"><img src="https://img-blog.csdn.net/20161007104455454?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="font-size:14px;"><span style="font-size:14px;"><br></span></span></p>
<p><span style="font-size:14px;">hadoop jar /usr/local/fqlhadoop/hbase-1.1.2/lib/hbase-server-1.1.2.jar与<br></span></p>
<p><span style="font-size:14px;">hbase org.apache.hadoop.hbase.mapreduce.Driver等效。<br></span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;">hadoop /usr/local/fqlhadoop/hbase-1.1.2/lib/hbase-server-1.1.2.jar importtsv<br></span></p>
<p><span style="font-size:14px;">hbase org.apache.hadoop.hbase.mapreduce.Driver importtsv<br></span></p>
<p><span style="font-size:14px;">hbase org.apache.hadoop.hbase.mapreduce.ImportTsv</span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;"><img src="https://img-blog.csdn.net/20161007104507475?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;">####129. HBase Tools and Utilities BEGIN####</span></p>
<span style="font-size:14px;">#1<br>
hbase org.apache.hadoop.hbase.mapreduce.Driver<br>
##1.1<br>
hbase org.apache.hadoop.hbase.mapreduce.Driver verifyrep与hbase org.apache.hadoop.hbase.mapreduce.replication.VerifyReplication等效<br><br><br>
hbase org.apache.hadoop.hbase.mapreduce.replication.VerifyReplication --starttime=1475251200000 --endtime=1475424000000 1 hbase_test:fql_hbase_replication_ms<br><br><br>
hbase org.apache.hadoop.hbase.mapreduce.Driver verifyrep --starttime=1475251200000 --endtime=1475424000000 1 hbase_test:fql_hbase_replication_ms<br><br><br>
#1.2<br>
hbase org.apache.hadoop.hbase.mapreduce.Driver importtsv<br>
hbase org.apache.hadoop.hbase.mapreduce.ImportTsv<br><br><br>
#2 hbase wal<br>
wal             Write-ahead-log analyzer<br><br><br>
WAL Tools<br>
hbase org.apache.hadoop.hbase.regionserver.wal.FSHLog<br><br><br>
##get a textual dump of a WAL file<br>
hbase org.apache.hadoop.hbase.regionserver.wal.FSHLog --dump /hbase/WALs/11.hadoop.com,16020,1474628670626/11.hadoop.com%2C16020%2C1474628670626.default.1475741264910<br><br><br>
##force a split of a log file directory<br>
hbase org.apache.hadoop.hbase.regionserver.wal.FSHLog --split /hbase/WALs/5.hadoop.com,16020,1475304063995<br><br><br>
##print the contents of a WAL<br>
hbase wal /hbase/WALs/11.hadoop.com,16020,1474628670626/11.hadoop.com%2C16020%2C1474628670626.default.1475741613348<br>
hbase wal --printvals /hbase/WALs/11.hadoop.com,16020,1474628670626/11.hadoop.com%2C16020%2C1474628670626.default.1475741613348<br>
hbase wal --json --printvals /hbase/WALs/11.hadoop.com,16020,1474628670626/<span></span>11.hadoop.com%2C16020%2C1474628670626.default.1475742164406<br><br><br>
hbase org.apache.hadoop.hbase.wal.WALPrettyPrinter<br>
 <br>
旧版<br>
hbase hlog <br>
hbase org.apache.hadoop.hbase.regionserver.wal.HLogPrettyPrinter<br><br><br>
#3 hbase hbck<br>
hbck            Run the hbase 'fsck' tool<br><br><br>
hbase hbck<br>
hbase hbck<span> </span>hbase org.apache.hadoop.hbase.util.HBaseFsck<br><br><br>
hbase hbck -details<br>
hbase hbck -metaonly<br>
hbase hbck -fixAssignments<br>
hbase hbck -fix   <br>
hbase hbck -repairHoles<br><br><br>
#4 verify the compressor is available<br>
hbase org.apache.hadoop.hbase.util.CompressionTest /compressionTest snappy<br>
hbase org.apache.hadoop.hbase.util.CompressionTest /compressionTest lzo<br>
hbase org.apache.hadoop.hbase.util.CompressionTest /compressionTest gz<br><br><br>
#5 WALPlayer<br>
hbase org.apache.hadoop.hbase.mapreduce.WALPlayer<br><br><br>
#6.1 RowCounter <br>
hbase org.apache.hadoop.hbase.mapreduce.RowCounter -Dhbase.client.scanner.caching=100 PH_MONITOR.T_MONITOR_INTERFACE_LOG<br><br><br>
#6.2 RowCounter <br>
hbase org.apache.hadoop.hbase.mapreduce.CellCounter PH_MONITOR.T_MONITOR_FSOF_PROVIDER_SERVICE /test53 |<br><br><br>
&lt;property&gt;<br>
  &lt;name&gt;mapreduce.job.counters.limit&lt;/name&gt;<br>
  &lt;!--&lt;value&gt;120&lt;/value&gt;--&gt;<br>
  &lt;value&gt;20000&lt;/value&gt;<br>
  &lt;description&gt;Limit on the number of counters allowed per job. The default value is 200.&lt;/description&gt;<br>
&lt;/property&gt;<br><br><br>
#7<br>
hbase org.apache.hadoop.hbase.regionserver.CompactionTool<br><br><br>
#8<br>
hbase clean<br><br><br>
#9 hbase mapredcp<br>
hbase org.apache.hadoop.hbase.util.MapreduceDependencyClasspathTool<br><br><br>
#10 hbase pe<br>
hbase org.apache.hadoop.hbase.PerformanceEvaluation<br><br><br>
#11 hbase ltt<br>
hbase ltt<br>
hbase org.apache.hadoop.hbase.util.LoadTestTool<br><br><br>
其他<br>
hbase org.apache.flume.tools.GetJavaProperty<br>
hbase org.apache.hadoop.hbase.util.HBaseConfTool hbase.rootdir<br><br><br>
####129. HBase Tools and Utilities END####<br><br><br><br><br>
130. Region Management<br>
130.2. Merge<br>
hbase org.apache.hadoop.hbase.util.Merge &lt;tablename&gt; &lt;region1&gt; &lt;region2&gt;<br><br><br><br><br><br><br>
69.7.4. StoreFile (HFile)<br>
hbase hfile<br>
hbase org.apache.hadoop.hbase.io.hfile.HFile<br><br><br>
hfile           Store file analyzer<br><br><br>
HBase中表ns_monitor:t_monitor_memory的Region分区与HBase在HDFS中的路径/hbase/data/ns_monitor/t_monitor_memory/相对应。<br><br><br>
#to view the content of the file by HDFS file<br>
hbase hfile -v -f /hbase/data/ns_monitor/t_monitor_memory/e15236a1387c7148dfde23022884cde3/monitor_memory/e1f274ed5c6c44f0984ac95de30fa880<br>
hbase hfile -b -v -f /hbase/data/ns_monitor/t_monitor_memory/e15236a1387c7148dfde23022884cde3/monitor_memory/e1f274ed5c6c44f0984ac95de30fa880<br><br><br>
hbase hfile -m -v -f /hbase/data/ns_monitor/t_monitor_memory/e15236a1387c7148dfde23022884cde3/monitor_memory/e1f274ed5c6c44f0984ac95de30fa880<br>
hbase hfile -p -v -f /hbase/data/ns_monitor/t_monitor_memory/e15236a1387c7148dfde23022884cde3/monitor_memory/e1f274ed5c6c44f0984ac95de30fa880<br><br><br>
#to view the content of the file by HBase region name<br>
hbase hfile -p -v -r ns_monitor:t_monitor_memory,,1463673066515.07ded346fc2ee8ee07fdcd04eb644243.<br>
hbase hfile -s -v -r ns_monitor:t_monitor_memory,,1463673066515.07ded346fc2ee8ee07fdcd04eb644243.<br><br><br>
#to view the content by the RowKey of a record<br>
hbase hfile -p -v -w "2001-01-01|192.168.16.123|mem_cache"<br>
输出Scanned kv count -&gt; 0<br><br><br>
-b,--printblocks         Print block index meta data<br>
-e,--printkey            Print keys<br><br><br>
-f &lt;arg&gt;                 File to scan. Pass full-path<br>
-h,--printblockheaders   Print block headers for each block.<br><br><br>
(useful)-m,--printmeta           Print meta data of file<br>
(useful)-p,--printkv             Print key/value pairs<br>
 <br>
-r,--region &lt;arg&gt;        Region to scan. Pass region name<br>
-s,--stats               Print statistics<br><br><br>
-v,--verbose             Verbose output<br>
-w &lt;arg&gt;                 Seek to this row and print all the kvs for this row only<br>
 <br><br><br>
69.7.7. Compaction<br>
A StoreFile is a facade of HFile. In terms of compaction, use of StoreFile seems to have prevailed in the past.<br>
A Store is the same thing as a ColumnFamily. StoreFiles are related to a Store, or ColumnFamily.</span>
            </div>
                </div>