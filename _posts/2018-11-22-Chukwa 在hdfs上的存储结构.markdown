---
layout:     post
title:      Chukwa 在hdfs上的存储结构
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="section" style="color:rgb(64,64,64);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13.333333015441895px;line-height:18px;">
<h2 style="border:0px;font-size:24px;line-height:36px;font-family:inherit;">
Overview<a name="Overview" style="border:0px;font-weight:inherit;line-height:inherit;font-family:inherit;color:rgb(0,105,214);"></a></h2>
<p style="border:0px;font-family:inherit;">
This document describes how Chukwa data is stored in HDFS and the processes that act on it.</p>
</div>
<div class="section" style="color:rgb(64,64,64);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13.333333015441895px;line-height:18px;">
<h2 style="border:0px;font-size:24px;line-height:36px;font-family:inherit;">
HDFS File System Structure<a name="HDFS_File_System_Structure" style="border:0px;font-weight:inherit;line-height:inherit;font-family:inherit;color:rgb(0,105,214);"></a></h2>
<p style="border:0px;font-family:inherit;">
The general layout of the Chukwa filesystem is as follows.</p>
<div>
<pre style="font-size:12px;font-family:Monaco, 'Andale Mono', 'Courier New', monospace;background-color:rgb(245,245,245);">/chukwa/
   archivesProcessing/
   dataSinkArchives/
   demuxProcessing/
   finalArchives/
   logs/
   postProcess/
   repos/
   rolling/
   temp/</pre>
</div>
</div>
<div class="section" style="color:rgb(64,64,64);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13.333333015441895px;line-height:18px;">
<h2 style="border:0px;font-size:24px;line-height:36px;font-family:inherit;">
Raw Log Collection and Aggregation Workflow<a name="Raw_Log_Collection_and_Aggregation_Workflow" style="border:0px;font-weight:inherit;line-height:inherit;font-family:inherit;color:rgb(0,105,214);"></a></h2>
<p style="border:0px;font-family:inherit;">
What data is stored where is best described by stepping through the Chukwa workflow.</p>
<ol style="border:0px;font-size:13.333333015441895px;line-height:1;font-family:inherit;"><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
Collectors write chunks to <em>logs/*.chukwa</em> files until a 64MB chunk size is reached or a given time interval has passed.
<ul style="border:0px;font-size:13.333333015441895px;line-height:1;font-family:inherit;list-style:disc;"><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
<em>logs/*.chukwa</em></li></ul></li><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
Collectors close chunks and rename them to <em>*.done</em>
<ul style="border:0px;font-size:13.333333015441895px;line-height:1;font-family:inherit;list-style:disc;"><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
from <em>logs/*.chukwa</em></li><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
to <em>logs/*.done</em></li></ul></li><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
DemuxManager checks for <em>*.done</em> files every 20 seconds.
<ol style="border:0px;font-size:13.333333015441895px;line-height:1;font-family:inherit;"><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
If <em>*.done</em> files exist, moves files in place for demux processing:
<ul style="border:0px;font-size:13.333333015441895px;line-height:1;font-family:inherit;list-style:disc;"><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
from: <em>logs/*.done</em></li><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
to: <em>demuxProcessing/mrInput</em></li></ul></li><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
The Demux MapReduce job is run on the data in <em>demuxProcessing/mrInput</em>.</li><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
If demux is successful within 3 attempts, archives the completed files:
<ul style="border:0px;font-size:13.333333015441895px;line-height:1;font-family:inherit;list-style:disc;"><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
from: <em>demuxProcessing/mrOutput</em></li><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
to: <em>dataSinkArchives/[yyyyMMdd]/*/*.done</em></li></ul></li><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
Otherwise moves the completed files to an error folder:
<ul style="border:0px;font-size:13.333333015441895px;line-height:1;font-family:inherit;list-style:disc;"><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
from: <em>demuxProcessing/mrOutput</em></li><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
to: <em>dataSinkArchives/InError/[yyyyMMdd]/*/*.done</em></li></ul></li></ol></li><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
PostProcessManager wakes up every few minutes and aggregates, orders and de-dups record files.
<ul style="border:0px;font-size:13.333333015441895px;line-height:1;font-family:inherit;list-style:disc;"><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
from: <em>postProcess/demuxOutputDir_*/[clusterName]/[dataType]/[dataType]_[yyyyMMdd]_[HH].R.evt</em></li><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
to: <em>repos/[clusterName]/[dataType]/[yyyyMMdd]/[HH]/[mm]/[dataType]_[yyyyMMdd]_[HH]_[N].[N].evt</em></li></ul></li><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
HourlyChukwaRecordRolling runs M/R jobs at 16 past the hour to group 5 minute logs to hourly.
<ul style="border:0px;font-size:13.333333015441895px;line-height:1;font-family:inherit;list-style:disc;"><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
from: <em>repos/[clusterName]/[dataType]/[yyyyMMdd]/[HH]/[mm]/[dataType]_[yyyyMMdd]_[mm].[N].evt</em></li><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
to: <em>temp/hourlyRolling/[clusterName]/[dataType]/[yyyyMMdd]</em></li><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
to: <em>repos/[clusterName]/[dataType]/[yyyyMMdd]/[HH]/[dataType]_HourlyDone_[yyyyMMdd]_[HH].[N].evt</em></li><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
leaves: <em>repos/[clusterName]/[dataType]/[yyyyMMdd]/[HH]/rotateDone/</em></li></ul></li><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
DailyChukwaRecordRolling runs M/R jobs at 1:30AM to group hourly logs to daily.
<ul style="border:0px;font-size:13.333333015441895px;line-height:1;font-family:inherit;list-style:disc;"><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
from: <em>repos/[clusterName]/[dataType]/[yyyyMMdd]/[HH]/[dataType]_[yyyyMMdd]_[HH].[N].evt</em></li><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
to: <em>temp/dailyRolling/[clusterName]/[dataType]/[yyyyMMdd]</em></li><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
to: <em>repos/[clusterName]/[dataType]/[yyyyMMdd]/[dataType]_DailyDone_[yyyyMMdd].[N].evt</em></li><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
leaves: <em>repos/[clusterName]/[dataType]/[yyyyMMdd]/rotateDone/</em></li></ul></li><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
ChukwaArchiveManager every half hour or so aggregates and removes dataSinkArchives data using M/R.
<ul style="border:0px;font-size:13.333333015441895px;line-height:1;font-family:inherit;list-style:disc;"><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
from: <em>dataSinkArchives/[yyyyMMdd]/*/*.done</em></li><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
to: <em>archivesProcessing/mrInput</em></li><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
to: <em>archivesProcessing/mrOutput</em></li><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
to: <em>finalArchives/[yyyyMMdd]/*/chukwaArchive-part-*</em></li></ul></li></ol></div>
<div class="section" style="color:rgb(64,64,64);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13.333333015441895px;line-height:18px;">
<h2 style="border:0px;font-size:24px;line-height:36px;font-family:inherit;">
Log Directories Requiring Cleanup<a name="Log_Directories_Requiring_Cleanup" style="border:0px;font-weight:inherit;line-height:inherit;font-family:inherit;color:rgb(0,105,214);"></a></h2>
<p style="border:0px;font-family:inherit;">
The following directories will grow over time and will need to be periodically pruned:</p>
<ul style="border:0px;font-size:13.333333015441895px;line-height:1;font-family:inherit;"><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
<em>finalArchives/[yyyyMMdd]/*</em></li><li style="border:0px;font-size:13.333333015441895px;line-height:18px;font-family:inherit;">
<em>repos/[clusterName]/[dataType]/[yyyyMMdd]/*.evt</em></li></ul></div>
            </div>
                </div>