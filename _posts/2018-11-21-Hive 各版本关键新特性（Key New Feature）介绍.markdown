---
layout:     post
title:      Hive 各版本关键新特性（Key New Feature）介绍
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>转：<a href="http://my.oschina.net/leejun2005/blog/272188" rel="nofollow">http://my.oschina.net/leejun2005/blog/272188</a></p>
<p> </p>
<div class="BlogAnchor">
<p style="color:rgb(21,162,48);font-size:18px;"><em title="点击收起目录" class="corner" id="AnchorContentToggle" style="background:rgb(92,194,111);color:rgb(255,255,255);line-height:20px;font-size:13px;font-style:normal;font-weight:normal;display:inline-block;">目录[-]</em></p>
<div class="AnchorContent" id="AnchorContent">

<a href="http://my.oschina.net/leejun2005/blog/272188#OSC_h3_1" rel="nofollow" style="color:rgb(68,102,187);text-decoration:none;">1、Hive 0.8.0</a>
<a href="http://my.oschina.net/leejun2005/blog/272188#OSC_h3_2" rel="nofollow" style="color:rgb(68,102,187);text-decoration:none;">2、Hive 0.9.0</a>
<a href="http://my.oschina.net/leejun2005/blog/272188#OSC_h3_3" rel="nofollow" style="color:rgb(68,102,187);text-decoration:none;">3、Hive 0.10.0</a>
<a href="http://my.oschina.net/leejun2005/blog/272188#OSC_h3_4" rel="nofollow" style="color:rgb(68,102,187);text-decoration:none;">4、Hive 0.11.0</a>
<a href="http://my.oschina.net/leejun2005/blog/272188#OSC_h3_5" rel="nofollow" style="color:rgb(68,102,187);text-decoration:none;">5、Hive 0.12.0</a>
<a href="http://my.oschina.net/leejun2005/blog/272188#OSC_h3_6" rel="nofollow" style="color:rgb(68,102,187);text-decoration:none;">6、Hive 0.13.0</a>
<a href="http://my.oschina.net/leejun2005/blog/272188#OSC_h3_7" rel="nofollow" style="color:rgb(68,102,187);text-decoration:none;">7、Hive 0.14.0</a>
<a href="http://my.oschina.net/leejun2005/blog/272188#OSC_h3_8" rel="nofollow" style="color:rgb(68,102,187);text-decoration:none;">8、hive 1.0</a>
<a href="http://my.oschina.net/leejun2005/blog/272188#OSC_h3_9" rel="nofollow" style="color:rgb(68,102,187);text-decoration:none;">9、hive 1.1</a>
<a href="http://my.oschina.net/leejun2005/blog/272188#OSC_h3_10" rel="nofollow" style="color:rgb(68,102,187);text-decoration:none;">10、hive 1.2</a>
<a href="http://my.oschina.net/leejun2005/blog/272188#OSC_h3_11" rel="nofollow" style="color:rgb(68,102,187);text-decoration:none;">11、Hive 2.0</a>
<a href="http://my.oschina.net/leejun2005/blog/272188#OSC_h3_12" rel="nofollow" style="color:rgb(68,102,187);text-decoration:none;">Refer：</a></div>
</div>
<p> </p>
<div class="BlogContent">
<p><span style="font-family:'Microsoft YaHei';font-size:16px;">开源世界里的代码受社区推动和极客文化的影响，变化一直都很快。这点在 hadoop 生态圈里表现尤为突出，不过这也与 hadoop 得到业界的广泛应用以及各种需求推动密不可分（近几年大数据、云计算被炒烂的节奏 哈哈~）。生态圈里各个组件各种 bug、改进、新特性满天飞，刚看到下面某同学整理的 hadoop 版本变迁图之后，感觉也有必要整理下
 hive 的新特性演进史，以备忘。</span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:16px;"><a href="http://static.oschina.net/uploads/space/2016/0219/024632_RHIb_568818.jpg" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><img src="http://static.oschina.net/uploads/space/2016/0219/024632_RHIb_568818.jpg" alt="" style="background:rgb(244,247,249);border:1px solid rgb(221,221,221);"></a></span></p>
<span id="OSC_h3_1"></span>
<h3 style="line-height:28.79px;font-size:16px;font-weight:600;">
<span style="font-family:'Microsoft YaHei';font-size:16px;background-color:rgb(255,229,0);">1、Hive 0.8.0</span></h3>
<p><span style="font-family:'Microsoft YaHei';font-size:16px;">添加 Bitmap Indexes、TIMESTAMP datatype、Plugin Developer Kit、JDBC Driver Improvements 等新特性</span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:16px;">该版本年代久远了，就不详述了~</span></p>
<p><span style="line-height:1.5;font-size:12.5px;"><span style="font-family:'Microsoft YaHei';color:#e53333;font-size:16px;">具体请参考：</span><a href="http://blog.cloudera.com/blog/2011/11/coming-attractions-apache-hive-0-8-0/" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><span style="font-family:'Microsoft YaHei';font-size:16px;">http://blog.cloudera.com/blog/2011/11/coming-attractions-apache-hive-0-8-0/</span></a><span style="font-family:'Microsoft YaHei';font-size:16px;"></span></span></p>
<p><span style="font-family:'Microsoft YaHei';line-height:1.5;font-size:16px;"><a href="https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&amp;version=12316178" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&amp;version=12316178</a></span></p>
<span id="OSC_h3_2"></span>
<h3 style="line-height:28.79px;font-size:16px;font-weight:600;">
<span style="font-family:'Microsoft YaHei';line-height:1.5;font-size:16px;background-color:rgb(255,229,0);">2、Hive 0.9.0</span></h3>
<p><span style="font-family:'Microsoft YaHei';line-height:1.5;font-size:16px;">1. 支持CREATE OR REPLACE VIEW<br>
2. 增加错误提示<br>
3. 支持NOT IN 和 NOT LIKE<br>
4. Ctrl+c将会提交kill命令，kill掉当前运行的query job,并且不会退出hive cli<br>
5. 输出map数和reduce数<br>
6. 提升"select xx,xx from xxx LIMIT xxx"性能<br>
7. 支持BETWEEN操作<br>
8. PRINTF()函数<br>
9. COALESCE/UNION ALL操作时候对数据类型宽限<br>
10. 增加TIMESTAMP数据类型<br>
11. 增加"INSERT OVERWRITE TABLE X PARTITION (a=b, c=d) IF NOT EXISTS ..."操作，如果分区存在，则不会动.<br>
12. 提升hive任务提交后任务编译和启动的性能。<br><span style="color:#e53333;">具体请参考：</span>Whats new in Apache Hive 0.9.0</span></p>
<p><span style="font-family:'Microsoft YaHei';line-height:1.5;font-size:16px;"><a href="https://cwiki.apache.org/confluence/download/attachments/27362054/WhatsNewInHive090HadoopSummit2012BoF.pdf?version=1&amp;modificationDate=1339872131000" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">https://cwiki.apache.org/confluence/download/attachments/27362054/WhatsNewInHive090HadoopSummit2012BoF.pdf?version=1&amp;modificationDate=1339872131000</a><br></span></p>
<p><span style="font-family:'Microsoft YaHei';line-height:1.5;font-size:16px;"><a href="https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&amp;version=12317742" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&amp;version=12317742</a><br></span></p>
<span id="OSC_h3_3"></span>
<h3 style="line-height:28.79px;font-size:16px;font-weight:600;">
<span style="font-family:'Microsoft YaHei';line-height:1.5;font-size:16px;background-color:rgb(255,229,0);">3、Hive 0.10.0</span></h3>
<p><span style="font-family:'Microsoft YaHei';line-height:1.5;font-size:16px;"></span></p>
<p><a href="https://cwiki.apache.org/confluence/display/Hive/Enhanced+Aggregation,+Cube,+Grouping+and+Rollup" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><span style="font-family:'Microsoft YaHei';">Cube
 and Rollup</span></a><span style="font-family:'Microsoft YaHei';">: Hive now has support for creating cubes with rollups. Thanks to Namit!</span></p>
<p><a href="https://cwiki.apache.org/confluence/display/Hive/ListBucketing" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><span style="font-family:'Microsoft YaHei';">List
 Bucketing</span></a><span style="font-family:'Microsoft YaHei';">: This is an optimization that lets you better handle skew in your tables. Thanks to Gang!</span></p>
<p><a href="https://issues.apache.org/jira/issues/?jql=project%20%3D%20HIVE%20AND%20fixVersion%20%3D%20%220.10.0%22%20AND%20status%20%3D%20Closed%20AND%20text%20~%20%22windows%22" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><span style="font-family:'Microsoft YaHei';">Better
 Windows Support</span></a><span style="font-family:'Microsoft YaHei';">: Several Hive 0.10.0 fixes support running Hive natively on Windows. There is no more cygwin dependency. Thanks to Kanna!</span></p>
<p><span style="font-family:'Microsoft YaHei';">‘</span><a href="https://issues.apache.org/jira/browse/HIVE-3610" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><span style="font-family:'Microsoft YaHei';">Explain’
 Adds More Info</span></a><span style="font-family:'Microsoft YaHei';">: Now you can do an explain dependency and the explain plan will contain all the tables and partitions touched upon by the query. Thanks to Sambavi!</span></p>
<p><a href="https://issues.apache.org/jira/browse/HIVE-3705" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><span style="font-family:'Microsoft YaHei';">Improved
 Authorization</span></a><span style="font-family:'Microsoft YaHei';">: The metastore can now optionally do authorization checks on the server side instead of on the client, providing you with a better security profile. Thanks to Sushanth!</span></p>
<p><a href="https://issues.apache.org/jira/browse/HIVE-2910" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><span style="font-family:'Microsoft YaHei';">Faster
 Simple Queries</span></a><span style="font-family:'Microsoft YaHei';">: Some simple queries that don’t require aggregations, and therefore MapReduce jobs, can now run faster.Thanks to Navis!</span></p>
<p><span style="text-decoration:underline;"><a href="https://issues.apache.org/jira/issues/?jql=project%20%3D%20HIVE%20AND%20fixVersion%20%3D%20%220.10.0%22%20AND%20status%20%3D%20Closed%20AND%20text%20~%20%2223%22" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><span style="font-family:'Microsoft YaHei';">Better
 YARN Support</span></a></span><span style="font-family:'Microsoft YaHei';">: This release contains additional work aimed at making Hive work well with Hadoop YARN. While not all test cases are passing yet, there has been a lot of good
 progress made with this release. Thanks to Zhenxiao!</span></p>
<p><a href="https://cwiki.apache.org/confluence/display/Hive/Union+Optimization" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><span style="font-family:'Microsoft YaHei';">Union
 Optimization</span></a><span style="font-family:'Microsoft YaHei';">: Hive queries with unions will now result in a lower number of MapReduce jobs under certain conditions. Thanks to Namit!</span></p>
<p><a href="https://issues.apache.org/jira/browse/HIVE-3068" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><span style="font-family:'Microsoft YaHei';">Undo
 Your Drop Table</span></a><span style="font-family:'Microsoft YaHei';">: While not really truly ‘undo’, you can now reinstate your table after dropping it. Thanks to Andrew!</span></p>
<p><a href="https://issues.apache.org/jira/browse/HIVE-967" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><span style="font-family:'Microsoft YaHei';">Show
 Create Table</span></a><span style="font-family:'Microsoft YaHei';">: The lets you see how you created your table. Thanks to Feng!</span></p>
<p><a href="https://issues.apache.org/jira/browse/HIVE-895" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><span style="font-family:'Microsoft YaHei';">Support
 for Avro Data</span></a><span style="font-family:'Microsoft YaHei';">: Hive now has built-in support for reading/writing Avro data. Thanks to Jakob!</span></p>
<p><a href="https://cwiki.apache.org/confluence/display/Hive/Skewed+Join+Optimization" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><span style="font-family:'Microsoft YaHei';">Skewed
 Joins</span></a><span style="font-family:'Microsoft YaHei';">: Hive’s support for joins involving skewed data is now improved. Thanks to Namit!</span></p>
<p><a href="https://issues.apache.org/jira/issues/?jql=project%20%3D%20HIVE%20AND%20fixVersion%20%3D%20%220.10.0%22%20AND%20status%20%3D%20Closed%20AND%20text%20~%20%22metastore%20retries%22" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><span style="font-family:'Microsoft YaHei';">Robust
 Connection Handling at the Metastore Layer</span></a><span style="font-family:'Microsoft YaHei';">: Connection handling between a metastore client and server  and also between a metastore server and the database layer has been improved.
 Thanks to Bhushan and Jean!</span></p>
<p><a href="https://cwiki.apache.org/confluence/display/Hive/Column+Statistics+in+Hive" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><span style="font-family:'Microsoft YaHei';">More
 Statistics</span></a><span style="font-family:'Microsoft YaHei';">: Its now possible to collect and store scalar-valued statistics for your tables and partitions. This will enable better query planning in upcoming releases. Thanks to
 Shreepadma!</span></p>
<p><a href="https://issues.apache.org/jira/browse/HIVE-2910" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><span style="font-family:'Microsoft YaHei';">Better-Looking
 HWI</span></a><span style="font-family:'Microsoft YaHei';"> : HWI now uses a bootstrap javascript library. It looks really slick.</span></p>
<p><span style="color:#e53333;">具体请参考：</span><span> </span><a href="http://zh.hortonworks.com/blog/apache-hive-0-10-0-is-now-available/" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">http://zh.hortonworks.com/blog/apache-hive-0-10-0-is-now-available/</a></p>
<p><span style="font-family:'Microsoft YaHei';line-height:1.5;font-size:16px;"><a href="https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12320745&amp;styleName=Text&amp;projectId=12310843" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12320745&amp;styleName=Text&amp;projectId=12310843</a></span></p>
<p><span style="line-height:1.5;font-size:12.5px;"><a href="https://cwiki.apache.org/confluence/display/Hive/Enhanced+Aggregation,+Cube,+Grouping+and+Rollup" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">https://cwiki.apache.org/confluence/display/Hive/Enhanced+Aggregation,+Cube,+Grouping+and+Rollup</a><br></span></p>
<span id="OSC_h3_4"></span>
<h3 style="line-height:28.79px;font-size:16px;font-weight:600;">
<span style="font-family:'Microsoft YaHei';line-height:1.5;font-size:16px;background-color:rgb(255,229,0);">4、Hive 0.11.0</span></h3>
<p><span style="font-family:'Microsoft YaHei';line-height:1.5;font-size:16px;"></span></p>
<ul style="line-height:22.5px;list-style-type:disc;"><li>
<p><strong>ORCFile.  It’s Optimized.<br></strong>The ORC File (Optimized RC File) presents key new features that speed access of data Apache Hive as it adds meta information at the file and block data level so that queries can be more intelligent and use meta data to optimize access.  Further, with
 the ORC file, only the bytes from the required columns are read from HDFS which minimizes I/O and speeds the query chain.  These are major advances for improved performance in Hive.</p>
</li><li>
<p><strong>Improved Data Types<br></strong>As Apache Hive marches towards full SQL-compatibility, an update to the decimal data type was made more usable.</p>
</li><li>
<p><strong>Analytic Functions<br></strong>Hive 0.11 introduces windowing functions for RANK, LEAD/LAG, ROW_NUMBER, FIRST_VALUE, LAST_VALUE and more. It also introduces aggregate OVER functions with PARTITION BY and ORDER BY</p>
</li><li>
<p><strong>Joins improved in Hive 0.11<br></strong>Both the broadcast join and the SMB join were improved considerably in Hive 0.11.  Both joins work without user hints, so that the Hive optimizer now picks the correct join rather than depending on the user to do so. More broadcast joins are now packed
 into a single MapReduce job, making star join queries much more efficient.</p>
</li><li>
<p><strong>Implement HiveServer2</strong><br></p>
</li><li>
<p><strong><a href="https://issues.apache.org/jira/browse/HIVE-3682" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><span style="color:#4c33e5;">when
 output hive table to file,users should could have a separator of their own choice</span></a></strong></p>
</li></ul><p><span style="font-family:'Microsoft YaHei';line-height:1.5;font-size:16px;"><span style="color:#e53333;">具体请参考：</span><a href="http://zh.hortonworks.com/blog/apache-hive-0-11-stinger-phase-1-delivered/" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">http://zh.hortonworks.com/blog/apache-hive-0-11-stinger-phase-1-delivered/</a></span></p>
<p><span style="font-family:'Microsoft YaHei';line-height:1.5;font-size:16px;"><a href="https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12323587&amp;styleName=Text&amp;projectId=12310843" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12323587&amp;styleName=Text&amp;projectId=12310843</a></span></p>
<span id="OSC_h3_5"></span>
<h3 style="line-height:28.79px;font-size:16px;font-weight:600;">
<span style="font-family:'Microsoft YaHei';line-height:1.5;font-size:16px;background-color:rgb(255,229,0);">5、Hive 0.12.0</span></h3>
<p><span style="font-family:'Microsoft YaHei';line-height:1.5;font-size:16px;background-color:rgb(255,229,0);"><a href="http://static.oschina.net/uploads/img/201406/04025732_B38U.png" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><img alt="Hive12deux" src="http://static.oschina.net/uploads/img/201406/04025732_B38U.png" style="background:rgb(244,247,249);border:1px solid rgb(221,221,221);"></a><br></span></p>
<p><span style="font-family:'Microsoft YaHei';line-height:1.5;font-size:16px;"></span></p>
<p><span style="font-family:'Microsoft YaHei';line-height:1.5;font-size:16px;"><span style="color:#e53333;">具体请参考：</span><a href="http://zh.hortonworks.com/blog/announcing-apache-hive-0-12/" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">http://zh.hortonworks.com/blog/announcing-apache-hive-0-12/</a></span></p>
<p><span style="font-family:'Microsoft YaHei';line-height:1.5;font-size:16px;"><a href="https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324312&amp;styleName=Text&amp;projectId=12310843" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324312&amp;styleName=Text&amp;projectId=12310843</a></span></p>
<span id="OSC_h3_6"></span>
<h3 style="line-height:28.79px;font-size:16px;font-weight:600;">
<span style="font-family:'Microsoft YaHei';line-height:1.5;font-size:16px;background-color:rgb(255,229,0);">6、Hive 0.13.0</span></h3>
<p><span style="font-family:'Microsoft YaHei';line-height:1.5;font-size:16px;"><a href="http://static.oschina.net/uploads/img/201406/04025616_NRup.png" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><img alt="hivesidebar" src="http://static.oschina.net/uploads/img/201406/04025616_NRup.png" style="background:rgb(244,247,249);border:1px solid rgb(221,221,221);"></a></span></p>
<p><span style="font-family:'Microsoft YaHei';line-height:1.5;font-size:16px;"><span style="color:#e53333;">具体请参考：</span><a href="http://zh.hortonworks.com/blog/announcing-apache-hive-0-13-completion-stinger-initiative/" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">http://zh.hortonworks.com/blog/announcing-apache-hive-0-13-completion-stinger-initiative/</a></span></p>
<p><span style="font-family:'Microsoft YaHei';line-height:1.5;font-size:16px;"><a href="https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324986&amp;styleName=Text&amp;projectId=12310843" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324986&amp;styleName=Text&amp;projectId=12310843</a></span></p>
<span id="OSC_h3_7"></span>
<h3 style="line-height:28.79px;font-size:16px;font-weight:600;">
<span style="line-height:1.5;font-size:16px;background-color:rgb(255,255,0);">7、Hive 0.14.0</span></h3>
<p><span style="line-height:1.5;font-size:12.5px;"></span></p>
<p>[HIVE-5317] - Implement insert, update, and delete in Hive with full ACID support</p>
<p>[HIVE-5775] - Introduce Cost Based Optimizer to Hive</p>
<p>[HIVE-5823] - Support for DECIMAL primitive type in AvroSerDe</p>
<p>[HIVE-6455] - Scalable dynamic partitioning and bucketing optimization</p>
<p>[HIVE-6469] - skipTrash option in hive command line</p>
<p>[HIVE-6806] - CREATE TABLE should support STORED AS AVRO</p>
<p>[HIVE-7036] - get_json_object bug when extract list of list with index</p>
<p>[HIVE-7054] - Support ELT UDF in vectorized mode</p>
<p>[HIVE-7068] - Integrate AccumuloStorageHandler</p>
<p>[HIVE-7090] - Support session-level temporary tables in Hive</p>
<p>[HIVE-7158] - Use Tez auto-parallelism in Hive</p>
<p>[HIVE-7203] - Optimize limit 0</p>
<p>[HIVE-7255] - Allow partial partition spec in analyze command</p>
<p>[HIVE-7299] - Enable metadata only optimization on Tez</p>
<p>[HIVE-7341] - Support for Table replication across HCatalog instances</p>
<p>[HIVE-7390] - Make single quote character optional and configurable in BeeLine CSV/TSV output</p>
<p>[HIVE-7416] - provide context information to authorization checkPrivileges api call</p>
<p>[HIVE-7430] - Implement SMB join in tez</p>
<p>[HIVE-7446] - Add support to ALTER TABLE .. ADD COLUMN to Avro backed tables</p>
<p>[HIVE-7506] - MetadataUpdater: provide a mechanism to edit the statistics of a column in a table (or a partition of a table)</p>
<p>[HIVE-7509] - Fast stripe level merging for ORC</p>
<p>[HIVE-7547] - Add ipAddress and userName to ExecHook</p>
<p>[HIVE-7587] - Fetch aggregated stats from MetaStore</p>
<p>[HIVE-7654] - A method to extrapolate columnStats for partitions of a table</p>
<p>[HIVE-7826] - Dynamic partition pruning on Tez</p>
<p>[HIVE-8531] - Fold is not null filter if there are other comparison filter present on same column</p>
<p><a href="https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12326450&amp;styleName=Text&amp;projectId=12310843" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12326450&amp;styleName=Text&amp;projectId=12310843</a><br></p>
<span id="OSC_h3_8"></span>
<h3 style="line-height:28.79px;font-size:16px;font-weight:600;">
<span style="line-height:1.5;font-size:16px;background-color:rgb(255,255,0);">8、hive 1.0</span></h3>
<p><span style="line-height:1.5;font-size:12.5px;">该版本无新特性</span></p>
<span id="OSC_h3_9"></span>
<h3 style="line-height:28.79px;font-size:16px;font-weight:600;">
<span style="line-height:1.5;font-size:16px;background-color:rgb(255,255,0);">9、hive 1.1</span></h3>
<p><span style="line-height:1.5;font-size:12.5px;"></span></p>
<p>[HIVE-3405] - UDF initcap to obtain a string with the first letter of each word in uppercase other letters in lowercase</p>
<p>[HIVE-7122] - Storage format for create like table</p>
<p>[HIVE-8435] - Add identity project remover optimization</p>
<p><a href="https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&amp;styleName=Text&amp;version=12329363" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&amp;styleName=Text&amp;version=12329363</a></p>
<span id="OSC_h3_10"></span>
<h3 style="line-height:28.79px;font-size:16px;font-weight:600;">
<span style="font-size:16px;background-color:rgb(255,255,0);">10、hive 1.2</span></h3>
<p><span style="font-size:12.5px;"></span></p>
<p>[HIVE-7998] - Enhance JDBC Driver to not require class specification</p>
<p>[HIVE-9039] - Support Union Distinct</p>
<p>[HIVE-9188] - BloomFilter support in ORC</p>
<p>[HIVE-9277] - Hybrid Hybrid Grace Hash Join</p>
<p>[HIVE-9302] - Beeline add commands to register local jdbc driver names and jars</p>
<p>[HIVE-9780] - Add another level of explain for RDBMS audience</p>
<p>[HIVE-10038] - Add Calcite's ProjectMergeRule.</p>
<p>[HIVE-10099] - Enable constant folding for Decimal</p>
<p>[HIVE-10591] - Support limited integer type promotion in ORC</p>
<p><a href="https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12329345&amp;styleName=Text&amp;projectId=12310843" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12329345&amp;styleName=Text&amp;projectId=12310843</a></p>
<span id="OSC_h3_11"></span>
<h3 style="line-height:28.79px;font-size:16px;font-weight:600;">
<span style="font-size:16px;background-color:rgb(255,255,0);">11、Hive 2.0</span></h3>
<ul style="line-height:22.5px;list-style-type:disc;"><li>
<p>[<a href="https://issues.apache.org/jira/browse/HIVE-686" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">HIVE-686</a>] - add UDF substring_index</p>
</li><li>
<p>[<a href="https://issues.apache.org/jira/browse/HIVE-3404" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">HIVE-3404</a>] - Create quarter UDF</p>
</li><li>
<p>[<a href="https://issues.apache.org/jira/browse/HIVE-7926" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">HIVE-7926</a>] - long-lived daemons for query fragment execution, I/O
 and caching</p>
</li><li>
<p>[<a href="https://issues.apache.org/jira/browse/HIVE-10591" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">HIVE-10591</a>] - Support limited integer type promotion in ORC</p>
</li><li>
<p>[<a href="https://issues.apache.org/jira/browse/HIVE-10592" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">HIVE-10592</a>] - ORC file dump in JSON format</p>
</li><li>
<p>[<a href="https://issues.apache.org/jira/browse/HIVE-10673" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">HIVE-10673</a>] - Dynamically partitioned hash join for Tez</p>
</li><li>
<p>[<a href="https://issues.apache.org/jira/browse/HIVE-10761" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">HIVE-10761</a>] - Create codahale-based metrics system for Hive</p>
</li><li>
<p>[<a href="https://issues.apache.org/jira/browse/HIVE-10785" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">HIVE-10785</a>] - Support aggregate push down through joins</p>
</li><li>
<p>[<a href="https://issues.apache.org/jira/browse/HIVE-11103" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">HIVE-11103</a>] - Add banker's rounding BROUND UDF</p>
</li><li>
<p>[<a href="https://issues.apache.org/jira/browse/HIVE-11461" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">HIVE-11461</a>] - Transform flat AND/OR into IN struct clause</p>
</li><li>
<p>[<a href="https://issues.apache.org/jira/browse/HIVE-11488" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">HIVE-11488</a>] - Add sessionId and queryId info to HS2 log</p>
</li><li>
<p>[<a href="https://issues.apache.org/jira/browse/HIVE-11593" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">HIVE-11593</a>] - Add aes_encrypt and aes_decrypt UDFs</p>
</li><li>
<p>[<a href="https://issues.apache.org/jira/browse/HIVE-11600" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">HIVE-11600</a>] - Hive Parser to Support multi col in clause (x,y..)
 in ((..),..., ())</p>
</li><li>
<p>[<a href="https://issues.apache.org/jira/browse/HIVE-11684" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">HIVE-11684</a>] - Implement limit pushdown through outer join in CBO</p>
</li><li>
<p>[<a href="https://issues.apache.org/jira/browse/HIVE-11699" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">HIVE-11699</a>] - Support special characters in quoted table names</p>
</li><li>
<p>[<a href="https://issues.apache.org/jira/browse/HIVE-11706" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">HIVE-11706</a>] - Implement "show create database"</p>
</li><li>
<p>[<a href="https://issues.apache.org/jira/browse/HIVE-11775" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">HIVE-11775</a>] - Implement limit push down through union all in CBO</p>
</li><li>
<p>[<a href="https://issues.apache.org/jira/browse/HIVE-11785" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">HIVE-11785</a>] - Support escaping carriage return and new line for LazySimpleSerDe</p>
</li><li>
<p>[<a href="https://issues.apache.org/jira/browse/HIVE-11976" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">HIVE-11976</a>] - Extend CBO rules to being able to apply rules only
 once on a given operator</p>
</li><li>
<p>[<a href="https://issues.apache.org/jira/browse/HIVE-12080" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">HIVE-12080</a>] - Support auto type widening (int-&gt;bigint &amp; float-&gt;double)
 for Parquet table</p>
</li></ul><p><span style="font-family:'Microsoft YaHei';font-size:12px;line-height:1.5;background-color:rgb(255,255,255);"><a href="https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332641&amp;styleName=Text&amp;projectId=12310843" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;">https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332641&amp;styleName=Text&amp;projectId=12310843</a><br></span></p>
<span id="OSC_h3_12"></span>
<h3 style="line-height:28.79px;font-size:16px;font-weight:600;">
<span style="font-family:'Microsoft YaHei';line-height:1.5;font-size:16px;background-color:rgb(255,229,0);">Refer：</span></h3>
<p><span style="font-family:'Microsoft YaHei';line-height:1.5;font-size:16px;"><span style="font-size:14px;">[1] hive0.80, 0.90新特性  </span><a href="http://superlxw1234.iteye.com/blog/1564461" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><span style="font-size:14px;">http://superlxw1234.iteye.com/blog/1564461</span></a></span></p>
<p><span style="font-family:'Microsoft YaHei';line-height:1.5;font-size:16px;"><span style="font-size:14px;">[2] hive 0.10 0.11新增特性综述  </span><a href="http://blog.csdn.net/lalaguozhe/article/details/11730817" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><span style="font-size:14px;">http://blog.csdn.net/lalaguozhe/article/details/11730817</span></a></span></p>
<p><span style="font-family:'Microsoft YaHei';line-height:1.5;font-size:16px;"><span style="font-size:14px;">[3] </span><a href="http://hive.apache.org/downloads.html" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><span style="font-size:14px;">http://hive.apache.org/downloads.html</span></a></span></p>
<p><span style="line-height:1.5;font-size:12.5px;"><span style="font-size:14px;">[4] Hive未来两年的路线图  </span><a href="http://www.infoq.com/cn/news/2014/09/hive" rel="nofollow" style="color:rgb(255,131,115);font-size:12px;"><span style="font-size:14px;">http://www.infoq.com/cn/news/2014/09/hive</span></a><span style="font-size:14px;"></span></span></p>
<p><span style="line-height:1.5;font-size:12.5px;"><span style="font-family:'Microsoft YaHei';font-size:14px;">(1)支持ACID事务——用户将可以插入、更新和删除现有数据。Hive将由传统的一次写入、频繁读取的系统发展为一个支持变化数据分析的系统。</span><br><span style="font-family:'Microsoft YaHei';font-size:14px;">(2)实现亚秒级查询——用户可以将Hive用于像交互式仪表板和探究性分析这样对响应时间有更高要求的应用场景。</span><br><span style="font-family:'Microsoft YaHei';font-size:14px;">(3)全面支持SQL：2011 Analytics——用户可以使用标准SQL在Hive上部署复杂的报表，而且更快捷、更简便、更可靠。而基于成本的、功能强大的优化器可以确保工具生成的查询和复杂查询的运行速度。届时，Hive将在Hadoop上提供企业级SQL用户所享有的全部表达能力。它将在支持窗口函数、用户自定义函数、子查询、Rollup、Cube、标准聚集、内连接、外连接、半连接和交叉连接的基础上，增加对不等连接、集合函数（并、交、差）、时间间隔类型等的支持。</span><br><span style="font-family:'Microsoft YaHei';font-size:14px;">Stinger.next计划用时18个月，将分三个阶段交付。事务支持将于2014年底发布，亚秒级查询将在2015年上半年推出，而对SQL：2011 Analytics的全面支持则将于2015年底完成。</span><br><span style="font-family:'Microsoft YaHei';font-size:14px;">此外，Hive还将与机器学习框架Spark集成，使用户可以通过Hive运行机器学习模型。</span><br></span></p>
</div>
<p> </p>
            </div>
                </div>