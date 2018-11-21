---
layout:     post
title:      hive tutorial
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<table border="0" cellpadding="2" cellspacing="0" width="100%" style="font-size:13px; line-height:17px; color:rgb(0,0,0); clear:left; font-family:Helvetica,Arial,sans-serif">
<tbody>
<tr class="topBar" style="font-size:10pt; line-height:1.3; background-color:rgb(0,51,102)">
<td align="left" valign="middle" class="topBarDiv" nowrap style="font-size:10pt; line-height:1.3; color:rgb(255,255,255)">
 <a href="https://cwiki.apache.org/Hive/home.html" rel="nofollow" title="Apache Hive" style="color:rgb(255,255,255)">Apache Hive</a> &gt; <a href="https://cwiki.apache.org/Hive/tutorial.html" rel="nofollow" title="Tutorial" style="color:rgb(255,255,255)">Tutorial</a></td>
<td align="right" valign="middle" nowrap style="font-size:10pt; line-height:1.3">
<form name="search" action="http://www.google.com/search" method="get" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<input type="text" name="q" maxlength="255" value="" style="font-family:Helvetica,Arial,sans-serif; font-size:10pt; color:rgb(0,0,0)"> <input type="submit" name="btnG" value="Google Search" style="font-family:Helvetica,Arial,sans-serif; font-size:10pt; color:rgb(0,0,0)"></form>
</td>
</tr>
</tbody>
</table>
<div id="PageContent" style="text-align:left; padding-top:0px; padding-right:0px; padding-bottom:20px; padding-left:0px; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; font-family:Helvetica,Arial,sans-serif; font-size:13px; line-height:17px">
<div class="pageheader" style="padding-top:6px; padding-right:0px; padding-bottom:0px; padding-left:0px; border-bottom-width:1px; border-bottom-style:solid; border-bottom-color:rgb(102,153,204)">
<div class="smalltext" style="color:rgb(102,102,102); font-size:12px; margin-top:0px; margin-right:10px; margin-bottom:0px; margin-left:10px">
Apache Hive</div>
<div class="pagetitle" style="font-size:18pt; line-height:normal; font-weight:bold; color:rgb(0,51,102); margin-top:0px; margin-right:10px; margin-bottom:8px; margin-left:10px">
Tutorial</div>
<div class="greynavbar" align="right" style="background-color:rgb(240,240,240); border-top-width:1px; border-top-style:solid; border-top-color:rgb(102,153,204); padding-top:2px; padding-right:10px; padding-bottom:2px; padding-left:10px; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px">
<a href="https://cwiki.apache.org/confluence/pages/editpage.action?pageId=27362061" rel="nofollow" style="color:rgb(0,51,102)"><img src="https://cwiki.apache.org/confluence/images/icons/notep_16.gif" height="16" width="16" border="0" align="absmiddle" title="Edit Page" alt="" style="border-top-style:none; border-right-style:none; border-bottom-style:none; border-left-style:none; border-width:initial; border-color:initial"></a> <a href="https://cwiki.apache.org/confluence/pages/editpage.action?pageId=27362061" rel="nofollow" style="color:rgb(0,51,102)">Edit
 Page</a>   <a href="https://cwiki.apache.org/confluence/pages/listpages.action?key=Hive" rel="nofollow" style="color:rgb(0,51,102)"><img src="https://cwiki.apache.org/confluence/images/icons/browse_space.gif" height="16" width="16" border="0" align="absmiddle" title="Browse Space" alt="" style="border-top-style:none; border-right-style:none; border-bottom-style:none; border-left-style:none; border-width:initial; border-color:initial"></a> <a href="https://cwiki.apache.org/confluence/pages/listpages.action?key=Hive" rel="nofollow" style="color:rgb(0,51,102)">Browse
 Space</a>   <a href="https://cwiki.apache.org/confluence/pages/createpage.action?spaceKey=Hive&amp;fromPageId=27362061" rel="nofollow" style="color:rgb(0,51,102)"><img src="https://cwiki.apache.org/confluence/images/icons/add_page_16.gif" height="16" width="16" border="0" align="absmiddle" title="Add Page" alt="" style="border-top-style:none; border-right-style:none; border-bottom-style:none; border-left-style:none; border-width:initial; border-color:initial"></a> <a href="https://cwiki.apache.org/confluence/pages/createpage.action?spaceKey=Hive&amp;fromPageId=27362061" rel="nofollow" style="color:rgb(0,51,102)">Add
 Page</a>   <a href="https://cwiki.apache.org/confluence/pages/createblogpost.action?spaceKey=Hive&amp;fromPageId=27362061" rel="nofollow" style="color:rgb(0,51,102)"><img src="https://cwiki.apache.org/confluence/images/icons/add_blogentry_16.gif" height="16" width="16" border="0" align="absmiddle" title="Add News" alt="" style="border-top-style:none; border-right-style:none; border-bottom-style:none; border-left-style:none; border-width:initial; border-color:initial"></a> <a href="https://cwiki.apache.org/confluence/pages/createblogpost.action?spaceKey=Hive&amp;fromPageId=27362061" rel="nofollow" style="color:rgb(0,51,102)">Add
 News</a></div>
</div>
<div class="pagesubheading" style="color:rgb(102,102,102); font-size:10px; padding-top:0px; padding-right:0px; padding-bottom:5px; padding-left:0px; margin-top:0px; margin-right:10px; margin-bottom:0px; margin-left:10px">
#editReport()</div>
<div class="pagecontent">
<div class="wiki-content" style="font-size:10pt; line-height:13pt">
<h1 style="line-height:normal; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; font-size:21pt; margin-top:38px; margin-right:0px; margin-bottom:4px; margin-left:0px; color:rgb(0,51,102); border-bottom-color:rgb(102,153,204)">
<a name="Tutorial-HiveTutorial"></a><strong>Hive Tutorial</strong></h1>
<hr style="color:rgb(204,204,204); height:1px">
<div>
<ul style="font-size:10pt; line-height:13pt">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<a href="https://cwiki.apache.org/Hive/tutorial.html#Tutorial-HiveTutorial" rel="nofollow" style="color:rgb(0,51,102)"><strong>Hive Tutorial</strong></a></li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<a href="https://cwiki.apache.org/Hive/tutorial.html#Tutorial-Concepts" rel="nofollow" style="color:rgb(0,51,102)">Concepts</a>
<ul style="font-size:10pt; line-height:13pt; list-style-type:disc">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<a href="https://cwiki.apache.org/Hive/tutorial.html#Tutorial-WhatisHive" rel="nofollow" style="color:rgb(0,51,102)">What is Hive</a></li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<a href="https://cwiki.apache.org/Hive/tutorial.html#Tutorial-WhatHiveisNOT" rel="nofollow" style="color:rgb(0,51,102)">What Hive is NOT</a></li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<a href="https://cwiki.apache.org/Hive/tutorial.html#Tutorial-DataUnits" rel="nofollow" style="color:rgb(0,51,102)">Data Units</a></li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<a href="https://cwiki.apache.org/Hive/tutorial.html#Tutorial-TypeSystem" rel="nofollow" style="color:rgb(0,51,102)">Type System</a>
<ul style="font-size:10pt; line-height:13pt; list-style-type:disc">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<a href="https://cwiki.apache.org/Hive/tutorial.html#Tutorial-PrimitiveTypes" rel="nofollow" style="color:rgb(0,51,102)">Primitive Types</a></li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<a href="https://cwiki.apache.org/Hive/tutorial.html#Tutorial-ComplexTypes" rel="nofollow" style="color:rgb(0,51,102)">Complex Types</a></li></ul>
</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<a href="https://cwiki.apache.org/Hive/tutorial.html#Tutorial-Builtinoperatorsandfunctions" rel="nofollow" style="color:rgb(0,51,102)">Built in operators and functions</a>
<ul style="font-size:10pt; line-height:13pt; list-style-type:disc">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<a href="https://cwiki.apache.org/Hive/tutorial.html#Tutorial-Builtinoperators" rel="nofollow" style="color:rgb(0,51,102)">Built in operators</a></li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<a href="https://cwiki.apache.org/Hive/tutorial.html#Tutorial-Builtinfunctions" rel="nofollow" style="color:rgb(0,51,102)">Built in functions</a></li></ul>
</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<a href="https://cwiki.apache.org/Hive/tutorial.html#Tutorial-Languagecapabilities" rel="nofollow" style="color:rgb(0,51,102)">Language capabilities</a></li></ul>
</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<a href="https://cwiki.apache.org/Hive/tutorial.html#Tutorial-UsageandExamples" rel="nofollow" style="color:rgb(0,51,102)">Usage and Examples</a>
<ul style="font-size:10pt; line-height:13pt; list-style-type:disc">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<a href="https://cwiki.apache.org/Hive/tutorial.html#Tutorial-CreatingTables" rel="nofollow" style="color:rgb(0,51,102)">Creating Tables</a></li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<a href="https://cwiki.apache.org/Hive/tutorial.html#Tutorial-BrowsingTablesandPartitions" rel="nofollow" style="color:rgb(0,51,102)">Browsing Tables and Partitions</a></li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<a href="https://cwiki.apache.org/Hive/tutorial.html#Tutorial-LoadingData" rel="nofollow" style="color:rgb(0,51,102)">Loading Data</a></li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<a href="https://cwiki.apache.org/Hive/tutorial.html#Tutorial-SimpleQuery" rel="nofollow" style="color:rgb(0,51,102)">Simple Query</a></li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<a href="https://cwiki.apache.org/Hive/tutorial.html#Tutorial-PartitionBasedQuery" rel="nofollow" style="color:rgb(0,51,102)">Partition Based Query</a></li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<a href="https://cwiki.apache.org/Hive/tutorial.html#Tutorial-Joins" rel="nofollow" style="color:rgb(0,51,102)">Joins</a></li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<a href="https://cwiki.apache.org/Hive/tutorial.html#Tutorial-Aggregations" rel="nofollow" style="color:rgb(0,51,102)">Aggregations</a></li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<a href="https://cwiki.apache.org/Hive/tutorial.html#Tutorial-MultiTable%252FFileInserts" rel="nofollow" style="color:rgb(0,51,102)">Multi Table/File Inserts</a></li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<a href="https://cwiki.apache.org/Hive/tutorial.html#Tutorial-DynamicpartitionInsert" rel="nofollow" style="color:rgb(0,51,102)">Dynamic-partition Insert</a></li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<a href="https://cwiki.apache.org/Hive/tutorial.html#Tutorial-Insertingintolocalfiles" rel="nofollow" style="color:rgb(0,51,102)">Inserting into local files</a></li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<a href="https://cwiki.apache.org/Hive/tutorial.html#Tutorial-Sampling" rel="nofollow" style="color:rgb(0,51,102)">Sampling</a></li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<a href="https://cwiki.apache.org/Hive/tutorial.html#Tutorial-Unionall" rel="nofollow" style="color:rgb(0,51,102)">Union all</a></li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<a href="https://cwiki.apache.org/Hive/tutorial.html#Tutorial-ArrayOperations" rel="nofollow" style="color:rgb(0,51,102)">Array Operations</a></li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<a href="https://cwiki.apache.org/Hive/tutorial.html#Tutorial-Map%2528AssociativeArrays%2529Operations" rel="nofollow" style="color:rgb(0,51,102)">Map(Associative Arrays) Operations</a></li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<a href="https://cwiki.apache.org/Hive/tutorial.html#Tutorial-Custommap%252Freducescripts" rel="nofollow" style="color:rgb(0,51,102)">Custom map/reduce scripts</a></li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<a href="https://cwiki.apache.org/Hive/tutorial.html#Tutorial-CoGroups" rel="nofollow" style="color:rgb(0,51,102)">Co-Groups</a></li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<a href="https://cwiki.apache.org/Hive/tutorial.html#Tutorial-AlteringTables" rel="nofollow" style="color:rgb(0,51,102)">Altering Tables</a></li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<a href="https://cwiki.apache.org/Hive/tutorial.html#Tutorial-DroppingTablesandPartitions" rel="nofollow" style="color:rgb(0,51,102)">Dropping Tables and Partitions</a></li></ul>
</li></ul>
</div>
<h1 style="line-height:normal; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; font-size:21pt; margin-top:38px; margin-right:0px; margin-bottom:4px; margin-left:0px; color:rgb(0,51,102); border-bottom-color:rgb(102,153,204)">
<a name="Tutorial-Concepts"></a>Concepts</h1>
<h2 style="line-height:normal; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; font-size:18pt; margin-top:32px; margin-right:0px; margin-bottom:4px; margin-left:0px; color:rgb(0,51,102); border-bottom-color:rgb(102,153,204)">
<a name="Tutorial-WhatisHive"></a>What is Hive</h2>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
Hive is a data warehousing infrastructure based on the Hadoop. Hadoop provides massive scale out and fault tolerance capabilities for data storage and processing (using the map-reduce programming paradigm) on commodity hardware.</p>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
Hive is designed to enable easy data summarization, ad-hoc querying and analysis of large volumes of data. It provides a simple query language called Hive QL, which is based on SQL and which enables users familiar with SQL to do ad-hoc querying, summarization
 and data analysis easily. At the same time, Hive QL also allows traditional map/reduce programmers to be able to plug in their custom mappers and reducers to do more sophisticated analysis that may not be supported by the built-in capabilities of the language.</p>
<h2 style="line-height:normal; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; font-size:18pt; margin-top:32px; margin-right:0px; margin-bottom:4px; margin-left:0px; color:rgb(0,51,102); border-bottom-color:rgb(102,153,204)">
<a name="Tutorial-WhatHiveisNOT"></a>What Hive is NOT</h2>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
Hadoop is a batch processing system and Hadoop jobs tend to have high latency and incur substantial overheads in job submission and scheduling. As a result - latency for Hive queries is generally very high (minutes) even when data sets involved are very small
 (say a few hundred megabytes). As a result it cannot be compared with systems such as Oracle where analyses are conducted on a significantly smaller amount of data but the analyses proceed much more iteratively with the response times between iterations being
 less than a few minutes. Hive aims to provide acceptable (but not optimal) latency for interactive data browsing, queries over small data sets or test queries.</p>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
Hive is not designed for online transaction processing and does not offer real-time queries and row level updates. It is best used for batch jobs over large sets of immutable data (like web logs).</p>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
In the following sections we provide a tutorial on the capabilities of the system. We start by describing the concepts of data types, tables and partitions (which are very similar to what you would find in a traditional relational DBMS) and then illustrate
 the capabilities of the QL language with the help of some examples.</p>
<h2 style="line-height:normal; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; font-size:18pt; margin-top:32px; margin-right:0px; margin-bottom:4px; margin-left:0px; color:rgb(0,51,102); border-bottom-color:rgb(102,153,204)">
<a name="Tutorial-DataUnits"></a>Data Units</h2>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
In the order of granularity - Hive data is organized into:</p>
<ul style="font-size:10pt; line-height:13pt">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<strong>Databases</strong>: Namespaces that separate tables and other data units from naming confliction.</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<strong>Tables</strong>: Homogeneous units of data which have the same schema. An example of a table could be page_views table, where each row could comprise of the following columns (schema):
<ul style="font-size:10pt; line-height:13pt; list-style-type:disc">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
timestamp - which is of INT type that corresponds to a unix timestamp of when the page was viewed.</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
userid - which is of BIGINT type that identifies the user who viewed the page.</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
page_url - which is of STRING type that captures the location of the page.</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
referer_url - which is of STRING that captures the location of the page from where the user arrived at the current page.</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
IP - which is of STRING type that captures the IP address from where the page request was made.</li></ul>
</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<strong>Partitions</strong>: Each Table can have one or more partition Keys which determines how the data is stored. Partitions - apart from being storage units - also allow the user to efficiently identify the rows that satisfy a certain criteria. For example,
 a date_partition of type STRING and country_partition of type STRING. Each unique value of the partition keys defines a partition of the Table. For example all "US" data from "2009-12-23" is a partition of the page_views table. Therefore, if you run analysis
 on only the "US" data for 2009-12-23, you can run that query only on the relevant partition of the table thereby speeding up the analysis significantly. Note however, that just because a partition is named 2009-12-23 does not mean that it contains all or only
 data from that date; partitions are named after dates for convenience but it is the user's job to guarantee the relationship between partition name and data content!). Partition columns are virtual columns, they are not part of the data itself but are derived
 on load.</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<strong>Buckets</strong> (or <strong>Clusters</strong>): Data in each partition may in turn be divided into Buckets based on the value of a hash function of some column of the Table. For example the page_views table may be bucketed by userid, which is one of
 the columns, other than the partitions columns, of the page_view table. These can be used to efficiently sample the data.</li></ul>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
Note that it is not necessary for tables to be partitioned or bucketed, but these abstractions allow the system to prune large quantities of data during query processing, resulting in faster query execution.</p>
<h2 style="line-height:normal; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; font-size:18pt; margin-top:32px; margin-right:0px; margin-bottom:4px; margin-left:0px; color:rgb(0,51,102); border-bottom-color:rgb(102,153,204)">
<a name="Tutorial-TypeSystem"></a>Type System</h2>
<h3 style="line-height:normal; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; font-size:15pt; margin-top:28px; margin-right:0px; margin-bottom:4px; margin-left:0px; color:rgb(0,51,102)">
<a name="Tutorial-PrimitiveTypes"></a>Primitive Types</h3>
<ul style="font-size:10pt; line-height:13pt">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
Types are associated with the columns in the tables. The following Primitive types are supported:</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
Integers
<ul style="font-size:10pt; line-height:13pt; list-style-type:disc">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
TINYINT - 1 byte integer</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
SMALLINT - 2 byte integer</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
INT - 4 byte integer</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
BIGINT - 8 byte integer</li></ul>
</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
Boolean type
<ul style="font-size:10pt; line-height:13pt; list-style-type:disc">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
BOOLEAN - TRUE/FALSE</li></ul>
</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
Floating point numbers
<ul style="font-size:10pt; line-height:13pt; list-style-type:disc">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
FLOAT - single precision</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
DOUBLE - Double precision</li></ul>
</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
String type
<ul style="font-size:10pt; line-height:13pt; list-style-type:disc">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
STRING - sequence of characters in a specified character set</li></ul>
</li></ul>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
The Types are organized in the following hierarchy (where the parent is a super type of all the children instances):</p>
<ul style="font-size:10pt; line-height:13pt">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
Type
<ul style="font-size:10pt; line-height:13pt; list-style-type:disc">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<div class="table-wrap">
<table class="confluenceTable " style="font-size:10pt; line-height:13pt; color:rgb(0,0,0); clear:left; margin-top:5px; margin-right:0px; margin-bottom:5px; margin-left:0px; border-collapse:collapse; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<tbody>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
Primitive Type</td>
</tr>
</tbody>
</table>
</div>
<ul style="font-size:10pt; line-height:13pt; list-style-type:disc">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<div class="table-wrap">
<table class="confluenceTable " style="font-size:10pt; line-height:13pt; color:rgb(0,0,0); clear:left; margin-top:5px; margin-right:0px; margin-bottom:5px; margin-left:0px; border-collapse:collapse; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<tbody>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
Number</td>
</tr>
</tbody>
</table>
</div>
<ul style="font-size:10pt; line-height:13pt; list-style-type:disc">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<div class="table-wrap">
<table class="confluenceTable " style="font-size:10pt; line-height:13pt; color:rgb(0,0,0); clear:left; margin-top:5px; margin-right:0px; margin-bottom:5px; margin-left:0px; border-collapse:collapse; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<tbody>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
DOUBLE</td>
</tr>
</tbody>
</table>
</div>
<ul style="font-size:10pt; line-height:13pt; list-style-type:disc">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<div class="table-wrap">
<table class="confluenceTable " style="font-size:10pt; line-height:13pt; color:rgb(0,0,0); clear:left; margin-top:5px; margin-right:0px; margin-bottom:5px; margin-left:0px; border-collapse:collapse; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<tbody>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
BIGINT</td>
</tr>
</tbody>
</table>
</div>
<ul style="font-size:10pt; line-height:13pt; list-style-type:disc">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<div class="table-wrap">
<table class="confluenceTable " style="font-size:10pt; line-height:13pt; color:rgb(0,0,0); clear:left; margin-top:5px; margin-right:0px; margin-bottom:5px; margin-left:0px; border-collapse:collapse; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<tbody>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
INT</td>
</tr>
</tbody>
</table>
</div>
<ul style="font-size:10pt; line-height:13pt; list-style-type:disc">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<div class="table-wrap">
<table class="confluenceTable " style="font-size:10pt; line-height:13pt; color:rgb(0,0,0); clear:left; margin-top:5px; margin-right:0px; margin-bottom:5px; margin-left:0px; border-collapse:collapse; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<tbody>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
TINYINT</td>
</tr>
</tbody>
</table>
</div>
</li></ul>
</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<div class="table-wrap">
<table class="confluenceTable " style="font-size:10pt; line-height:13pt; color:rgb(0,0,0); clear:left; margin-top:5px; margin-right:0px; margin-bottom:5px; margin-left:0px; border-collapse:collapse; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<tbody>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
FLOAT</td>
</tr>
</tbody>
</table>
</div>
</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<div class="table-wrap">
<table class="confluenceTable " style="font-size:10pt; line-height:13pt; color:rgb(0,0,0); clear:left; margin-top:5px; margin-right:0px; margin-bottom:5px; margin-left:0px; border-collapse:collapse; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<tbody>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
INT</td>
</tr>
</tbody>
</table>
</div>
<ul style="font-size:10pt; line-height:13pt; list-style-type:disc">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<div class="table-wrap">
<table class="confluenceTable " style="font-size:10pt; line-height:13pt; color:rgb(0,0,0); clear:left; margin-top:5px; margin-right:0px; margin-bottom:5px; margin-left:0px; border-collapse:collapse; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<tbody>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
TINYINT</td>
</tr>
</tbody>
</table>
</div>
</li></ul>
</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<div class="table-wrap">
<table class="confluenceTable " style="font-size:10pt; line-height:13pt; color:rgb(0,0,0); clear:left; margin-top:5px; margin-right:0px; margin-bottom:5px; margin-left:0px; border-collapse:collapse; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<tbody>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
STRING</td>
</tr>
</tbody>
</table>
</div>
</li></ul>
</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<div class="table-wrap">
<table class="confluenceTable " style="font-size:10pt; line-height:13pt; color:rgb(0,0,0); clear:left; margin-top:5px; margin-right:0px; margin-bottom:5px; margin-left:0px; border-collapse:collapse; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<tbody>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
BOOLEAN</td>
</tr>
</tbody>
</table>
</div>
</li></ul>
</li></ul>
</li></ul>
</li></ul>
</li></ul>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
This type hierarchy defines how the types are implicitly converted in the query language. Implicit conversion is allowed for types from child to an ancestor. So when a query expression expects type1 and the data is of type2 type2 is implicitly converted to
 type1 if type1 is an ancestor of type2 in the type hierarchy. Apart from these fundamental rules for implicit conversion based on type system, Hive also allows the special case for conversion:</p>
<ul style="font-size:10pt; line-height:13pt">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<em>&lt;STRING&gt;</em> to <em>&lt;DOUBLE&gt;</em></li></ul>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
Explicit type conversion can be done using the cast operator as shown in the <a href="https://cwiki.apache.org/Hive/tutorial.html#Tutorial-Builtinfunctions" rel="nofollow" style="color:rgb(0,51,102)">Built in functions</a> section below.</p>
<h3 style="line-height:normal; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; font-size:15pt; margin-top:28px; margin-right:0px; margin-bottom:4px; margin-left:0px; color:rgb(0,51,102)">
<a name="Tutorial-ComplexTypes"></a>Complex Types</h3>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
Complex Types can be built up from primitive types and other composite types using:</p>
<ul style="font-size:10pt; line-height:13pt">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
Structs: the elements within the type can be accessed using the DOT (.) notation. For example, for a column c of type STRUCT {a INT; b INT} the a field is accessed by the expression c.a</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
Maps (key-value tuples): The elements are accessed using ['element name'] notation. For example in a map M comprising of a mapping from 'group' -&gt; gid the gid value can be accessed using M['group']</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
Arrays (indexable lists): The elements in the array have to be in the same type. Elements can be accessed using the [n] notation where n is an index (zero-based) into the array. For example for an array A having the elements ['a', 'b', 'c'], A[1] retruns 'b'.</li></ul>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
Using the primitive types and the constructs for creating complex types, types with arbitrary levels of nesting can be created. For example, a type User may comprise of the following fields:</p>
<ul style="font-size:10pt; line-height:13pt">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
gender - which is a STRING.</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
active - which is a BOOLEAN.</li></ul>
<h2 style="line-height:normal; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; font-size:18pt; margin-top:32px; margin-right:0px; margin-bottom:4px; margin-left:0px; color:rgb(0,51,102); border-bottom-color:rgb(102,153,204)">
<a name="Tutorial-Builtinoperatorsandfunctions"></a>Built in operators and functions</h2>
<h3 style="line-height:normal; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; font-size:15pt; margin-top:28px; margin-right:0px; margin-bottom:4px; margin-left:0px; color:rgb(0,51,102)">
<a name="Tutorial-Builtinoperators"></a>Built in operators</h3>
<ul style="font-size:10pt; line-height:13pt">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<strong>Relational Operators</strong> - The following operators compare the passed operands and generate a TRUE or FALSE value depending on whether the comparison between the operands holds or not.</li></ul>
<div class="table-wrap">
<table class="confluenceTable " style="font-size:10pt; line-height:13pt; color:rgb(0,0,0); clear:left; margin-top:5px; margin-right:0px; margin-bottom:5px; margin-left:0px; border-collapse:collapse; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<tbody>
<tr style="font-size:10pt; line-height:13pt">
<th class="confluenceTh" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; background-color:rgb(240,240,240); text-align:left; vertical-align:top; min-width:0.6em; color:rgb(0,51,102)">
<strong>Relational Operator</strong></th>
<th class="confluenceTh" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; background-color:rgb(240,240,240); text-align:left; vertical-align:top; min-width:0.6em; color:rgb(0,51,102)">
<strong>Operand types</strong></th>
<th class="confluenceTh" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; background-color:rgb(240,240,240); text-align:left; vertical-align:top; min-width:0.6em; color:rgb(0,51,102)">
<strong>Description</strong></th>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
A = B</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
all primitive types</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
TRUE if expression A is equivalent to expression B otherwise FALSE</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
A != B</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
all primitive types</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
TRUE if expression A is <em>not</em> equivalent to expression B otherwise FALSE</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
A &lt; B</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
all primitive types</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
TRUE if expression A is less than expression B otherwise FALSE</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
A &lt;= B</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
all primitive types</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
TRUE if expression A is less than or equal to expression B otherwise FALSE</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
A &gt; B</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
all primitive types</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
TRUE if expression A is greater than expression B otherwise FALSE</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
A &gt;= B</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
all primitive types</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
TRUE if expression A is greater than or equal to expression B otherwise FALSE</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
A IS NULL</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
all types</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
TRUE if expression A evaluates to NULL otherwise FALSE</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
A IS NOT NULL</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
all types</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
FALSE if expression A evaluates to NULL otherwise TRUE</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
A LIKE B</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
strings</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
TRUE if string A matches the SQL simple regular expression B, otherwise FALSE. The comparison is done character by character. The _ character in B matches any character in A (similar to . in posix regular expressions), and the % character in B matches an arbitrary
 number of characters in A (similar to .* in posix regular expressions). For example, <tt>'foobar' LIKE 'foo'</tt> evaluates to FALSE where as <tt>'foobar' LIKE 'foo___'</tt> evaluates to TRUE and so does <tt>'foobar' LIKE 'foo%'</tt>. To escape % use \ (%
 matches one % character). If the data contains a semi-colon, and you want to search for it, it needs to be escaped, <tt>columnValue LIKE 'a\;b'</tt></td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
A RLIKE B</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
strings</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
TRUE if string A matches the Java regular expression B (See <a href="http://java.sun.com/j2se/1.4.2/docs/api/java/util/regex/Pattern.html" rel="nofollow" class="external-link" style="color:rgb(0,51,102)">Java regular expressions syntax</a>), otherwise FALSE.
 For example, 'foobar' rlike 'foo' evaluates to FALSE whereas 'foobar' rlike '^f.*r$' evaluates to TRUE</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
A REGEXP B</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
strings</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
Same as RLIKE</td>
</tr>
</tbody>
</table>
</div>
<ul style="font-size:10pt; line-height:13pt">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<strong>Arithmetic Operators</strong> - The following operators support various common arithmetic operations on the operands. All of them return number types.</li></ul>
<div class="table-wrap">
<table class="confluenceTable " style="font-size:10pt; line-height:13pt; color:rgb(0,0,0); clear:left; margin-top:5px; margin-right:0px; margin-bottom:5px; margin-left:0px; border-collapse:collapse; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<tbody>
<tr style="font-size:10pt; line-height:13pt">
<th class="confluenceTh" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; background-color:rgb(240,240,240); text-align:left; vertical-align:top; min-width:0.6em; color:rgb(0,51,102)">
<strong>Arithmetic Operators</strong></th>
<th class="confluenceTh" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; background-color:rgb(240,240,240); text-align:left; vertical-align:top; min-width:0.6em; color:rgb(0,51,102)">
<strong>Operand types</strong></th>
<th class="confluenceTh" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; background-color:rgb(240,240,240); text-align:left; vertical-align:top; min-width:0.6em; color:rgb(0,51,102)">
<strong>Description</strong></th>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
A + B</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
all number types</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
Gives the result of adding A and B. The type of the result is the same as the common parent(in the type hierarchy) of the types of the operands. e.g. since every integer is a float, therefore float is a containing type of integer so the + operator on a float
 and an int will result in a float.</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
A - B</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
all number types</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
Gives the result of subtracting B from A. The type of the result is the same as the common parent(in the type hierarchy) of the types of the operands.</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
A * B</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
all number types</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
Gives the result of multiplying A and B. The type of the result is the same as the common parent(in the type hierarchy) of the types of the operands. Note that if the multiplication causing overflow, you will have to cast one of the operators to a type higher
 in the type hierarchy.</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
A / B</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
all number types</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
Gives the result of dividing B from A. The type of the result is the same as the common parent(in the type hierarchy) of the types of the operands. If the operands are integer types, then the result is the quotient of the division.</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
A % B</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
all number types</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
Gives the reminder resulting from dividing A by B. The type of the result is the same as the common parent(in the type hierarchy) of the types of the operands.</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
A &amp; B</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
all number types</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
Gives the result of bitwise AND of A and B. The type of the result is the same as the common parent(in the type hierarchy) of the types of the operands.</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
A | B</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
all number types</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
Gives the result of bitwise OR of A and B. The type of the result is the same as the common parent(in the type hierarchy) of the types of the operands.</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
A ^ B</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
all number types</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
Gives the result of bitwise XOR of A and B. The type of the result is the same as the common parent(in the type hierarchy) of the types of the operands.</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
~A</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
all number types</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
Gives the result of bitwise NOT of A. The type of the result is the same as the type of A.</td>
</tr>
</tbody>
</table>
</div>
<ul style="font-size:10pt; line-height:13pt">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<strong>Logical Operators</strong> - The following operators provide support for creating logical expressions. All of them return boolean TRUE or FALSE depending upon the boolean values of the operands.</li></ul>
<div class="table-wrap">
<table class="confluenceTable " style="font-size:10pt; line-height:13pt; color:rgb(0,0,0); clear:left; margin-top:5px; margin-right:0px; margin-bottom:5px; margin-left:0px; border-collapse:collapse; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<tbody>
<tr style="font-size:10pt; line-height:13pt">
<th class="confluenceTh" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; background-color:rgb(240,240,240); text-align:left; vertical-align:top; min-width:0.6em; color:rgb(0,51,102)">
<strong>Logical Operators</strong></th>
<th class="confluenceTh" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; background-color:rgb(240,240,240); text-align:left; vertical-align:top; min-width:0.6em; color:rgb(0,51,102)">
<strong>Operands types</strong></th>
<th class="confluenceTh" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; background-color:rgb(240,240,240); text-align:left; vertical-align:top; min-width:0.6em; color:rgb(0,51,102)">
<strong>Description</strong></th>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
A AND B</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
boolean</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
TRUE if both A and B are TRUE, otherwise FALSE</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
A &amp;&amp; B</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
boolean</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
Same as A AND B</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
A OR B</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
boolean</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
TRUE if either A or B or both are TRUE, otherwise FALSE</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
<tt>A | B</tt></td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
boolean</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
Same as A OR B</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
NOT A</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
boolean</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
TRUE if A is FALSE, otherwise FALSE</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
!A</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
boolean</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
Same as NOT A</td>
</tr>
</tbody>
</table>
</div>
<ul style="font-size:10pt; line-height:13pt">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<strong>Operators on Complex Types</strong> - The following operators provide mechanisms to access elements in Complex Types</li></ul>
<div class="table-wrap">
<table class="confluenceTable " style="font-size:10pt; line-height:13pt; color:rgb(0,0,0); clear:left; margin-top:5px; margin-right:0px; margin-bottom:5px; margin-left:0px; border-collapse:collapse; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<tbody>
<tr style="font-size:10pt; line-height:13pt">
<th class="confluenceTh" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; background-color:rgb(240,240,240); text-align:left; vertical-align:top; min-width:0.6em; color:rgb(0,51,102)">
<strong>Operator</strong></th>
<th class="confluenceTh" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; background-color:rgb(240,240,240); text-align:left; vertical-align:top; min-width:0.6em; color:rgb(0,51,102)">
<strong>Operand types</strong></th>
<th class="confluenceTh" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; background-color:rgb(240,240,240); text-align:left; vertical-align:top; min-width:0.6em; color:rgb(0,51,102)">
<strong>Description</strong></th>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
A[n]</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
A is an Array and n is an int</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
returns the nth element in the array A. The first element has index 0 e.g. if A is an array comprising of ['foo', 'bar'] then A[0] returns 'foo' and A[1] returns 'bar'</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
M[key]</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
M is a Map&lt;K, V&gt; and key has type K</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
returns the value corresponding to the key in the map e.g. if M is a map comprising of {'f' -&gt; 'foo', 'b' -&gt; 'bar', 'all' -&gt; 'foobar'} then M['all'] returns 'foobar'</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
S.x</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
S is a struct</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
returns the x field of S e.g for struct foobar {int foo, int bar} foobar.foo returns the integer stored in the foo field of the struct.</td>
</tr>
</tbody>
</table>
</div>
<h3 style="line-height:normal; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; font-size:15pt; margin-top:28px; margin-right:0px; margin-bottom:4px; margin-left:0px; color:rgb(0,51,102)">
<a name="Tutorial-Builtinfunctions"></a>Built in functions</h3>
<ul style="font-size:10pt; line-height:13pt">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
The following built in functions are supported in hive:<br>
<a href="http://svn.apache.org/viewvc/hive/trunk/ql/src/java/org/apache/hadoop/hive/ql/exec/FunctionRegistry.java?view=markup" rel="nofollow" class="external-link" style="color:rgb(0,51,102)">(Function list in source code: FunctionRegistry.java)</a></li></ul>
<div class="table-wrap">
<table class="confluenceTable " style="font-size:10pt; line-height:13pt; color:rgb(0,0,0); clear:left; margin-top:5px; margin-right:0px; margin-bottom:5px; margin-left:0px; border-collapse:collapse; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<tbody>
<tr style="font-size:10pt; line-height:13pt">
<th class="confluenceTh" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; background-color:rgb(240,240,240); text-align:left; vertical-align:top; min-width:0.6em; color:rgb(0,51,102)">
<strong>Return Type</strong></th>
<th class="confluenceTh" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; background-color:rgb(240,240,240); text-align:left; vertical-align:top; min-width:0.6em; color:rgb(0,51,102)">
<strong>Function Name (Signature)</strong></th>
<th class="confluenceTh" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; background-color:rgb(240,240,240); text-align:left; vertical-align:top; min-width:0.6em; color:rgb(0,51,102)">
<strong>Description</strong></th>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
BIGINT</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
round(double a)</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
returns the rounded BIGINT value of the double</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
BIGINT</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
floor(double a)</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
returns the maximum BIGINT value that is equal or less than the double</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
BIGINT</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
ceil(double a)</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
returns the minimum BIGINT value that is equal or greater than the double</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
double</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
rand(), rand(int seed)</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
returns a random number (that changes from row to row). Specifiying the seed will make sure the generated random number sequence is deterministic.</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
string</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
concat(string A, string B,...)</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
returns the string resulting from concatenating B after A. For example, concat('foo', 'bar') results in 'foobar'. This function accepts arbitrary number of arguments and return the concatenation of all of them.</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
string</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
substr(string A, int start)</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
returns the substring of A starting from start position till the end of string A. For example, substr('foobar', 4) results in 'bar'</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
string</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
substr(string A, int start, int length)</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
returns the substring of A starting from start position with the given length e.g. substr('foobar', 4, 2) results in 'ba'</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
string</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
upper(string A)</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
returns the string resulting from converting all characters of A to upper case e.g. upper('fOoBaR') results in 'FOOBAR'</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
string</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
ucase(string A)</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
Same as upper</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
string</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
lower(string A)</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
returns the string resulting from converting all characters of B to lower case e.g. lower('fOoBaR') results in 'foobar'</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
string</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
lcase(string A)</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
Same as lower</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
string</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
trim(string A)</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
returns the string resulting from trimming spaces from both ends of A e.g. trim(' foobar ') results in 'foobar'</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
string</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
ltrim(string A)</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
returns the string resulting from trimming spaces from the beginning(left hand side) of A. For example, ltrim(' foobar ') results in 'foobar '</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
string</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
rtrim(string A)</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
returns the string resulting from trimming spaces from the end(right hand side) of A. For example, rtrim(' foobar ') results in ' foobar'</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
string</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
regexp_replace(string A, string B, string C)</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
returns the string resulting from replacing all substrings in B that match the Java regular expression syntax(See <a href="http://java.sun.com/j2se/1.4.2/docs/api/java/util/regex/Pattern.html" rel="nofollow" class="external-link" style="color:rgb(0,51,102)">Java
 regular expressions syntax</a>) with C. For example, regexp_replace('foobar', 'oo|ar', ) returns 'fb'</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
int</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
size(Map&lt;K.V&gt;)</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
returns the number of elements in the map type</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
int</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
size(Array&lt;T&gt;)</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
returns the number of elements in the array type</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
<em>value of &lt;type&gt;</em></td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
cast(<em>&lt;expr&gt;</em> as <em>&lt;type&gt;</em>)</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
converts the results of the expression expr to &lt;type&gt; e.g. cast('1' as BIGINT) will convert the string '1' to it integral representation. A null is returned if the conversion does not succeed.</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
string</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
from_unixtime(int unixtime)</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
convert the number of seconds from unix epoch (1970-01-01 00:00:00 UTC) to a string representing the timestamp of that moment in the current system time zone in the format of "1970-01-01 00:00:00"</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
string</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
to_date(string timestamp)</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
Return the date part of a timestamp string: to_date("1970-01-01 00:00:00") = "1970-01-01"</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
int</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
year(string date)</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
Return the year part of a date or a timestamp string: year("1970-01-01 00:00:00") = 1970, year("1970-01-01") = 1970</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
int</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
month(string date)</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
Return the month part of a date or a timestamp string: month("1970-11-01 00:00:00") = 11, month("1970-11-01") = 11</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
int</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
day(string date)</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
Return the day part of a date or a timestamp string: day("1970-11-01 00:00:00") = 1, day("1970-11-01") = 1</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
string</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
get_json_object(string json_string, string path)</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
Extract json object from a json string based on json path specified, and return json string of the extracted json object. It will return null if the input json string is invalid</td>
</tr>
</tbody>
</table>
</div>
<ul style="font-size:10pt; line-height:13pt">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
The following built in aggregate functions are supported in Hive:</li></ul>
<div class="table-wrap">
<table class="confluenceTable " style="font-size:10pt; line-height:13pt; color:rgb(0,0,0); clear:left; margin-top:5px; margin-right:0px; margin-bottom:5px; margin-left:0px; border-collapse:collapse; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<tbody>
<tr style="font-size:10pt; line-height:13pt">
<th class="confluenceTh" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; background-color:rgb(240,240,240); text-align:left; vertical-align:top; min-width:0.6em; color:rgb(0,51,102)">
<strong>Return Type</strong></th>
<th class="confluenceTh" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; background-color:rgb(240,240,240); text-align:left; vertical-align:top; min-width:0.6em; color:rgb(0,51,102)">
<strong>Aggregation Function Name (Signature)</strong></th>
<th class="confluenceTh" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; background-color:rgb(240,240,240); text-align:left; vertical-align:top; min-width:0.6em; color:rgb(0,51,102)">
<strong>Description</strong></th>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
BIGINT</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
count(*), count(expr), count(DISTINCT expr[, expr_.])</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
count(*) - Returns the total number of retrieved rows, including rows containing NULL values; count(expr) - Returns the number of rows for which the supplied expression is non-NULL; count(DISTINCT expr[, expr]) - Returns the number of rows for which the supplied
 expression(s) are unique and non-NULL.</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
DOUBLE</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
sum(col), sum(DISTINCT col)</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
returns the sum of the elements in the group or the sum of the distinct values of the column in the group</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
DOUBLE</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
avg(col), avg(DISTINCT col)</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
returns the average of the elements in the group or the average of the distinct values of the column in the group</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
DOUBLE</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
min(col)</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
returns the minimum value of the column in the group</td>
</tr>
<tr style="font-size:10pt; line-height:13pt">
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
DOUBLE</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
max(col)</td>
<td class="confluenceTd" style="font-size:10pt; line-height:13pt; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:solid; border-right-style:solid; border-bottom-style:solid; border-left-style:solid; border-top-color:rgb(221,221,221); border-right-color:rgb(221,221,221); border-bottom-color:rgb(221,221,221); border-left-color:rgb(221,221,221); padding-top:5px; padding-right:5px; padding-bottom:5px; padding-left:5px; vertical-align:top; min-width:0.6em">
returns the maximum value of the column in the group</td>
</tr>
</tbody>
</table>
</div>
<h2 style="line-height:normal; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; font-size:18pt; margin-top:32px; margin-right:0px; margin-bottom:4px; margin-left:0px; color:rgb(0,51,102); border-bottom-color:rgb(102,153,204)">
<a name="Tutorial-Languagecapabilities"></a>Language capabilities</h2>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
<a href="https://cwiki.apache.org/Hive/languagemanual.html" rel="nofollow" title="LanguageManual" style="color:rgb(0,51,102)">Hive query language</a> provides the basic SQL like operations. These operations work on tables or partitions. These operations are:</p>
<ul style="font-size:10pt; line-height:13pt">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
Ability to filter rows from a table using a where clause.</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
Ability to select certain columns from the table using a select clause.</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
Ability to do equi-joins between two tables.</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
Ability to evaluate aggregations on multiple "group by" columns for the data stored in a table.</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
Ability to store the results of a query into another table.</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
Ability to download the contents of a table to a local (e.g., nfs) directory.</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
Ability to store the results of a query in a hadoop dfs directory.</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
Ability to manage tables and partitions (create, drop and alter).</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
Ability to plug in custom scripts in the language of choice for custom map/reduce jobs.</li></ul>
<h1 style="line-height:normal; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; font-size:21pt; margin-top:38px; margin-right:0px; margin-bottom:4px; margin-left:0px; color:rgb(0,51,102); border-bottom-color:rgb(102,153,204)">
<a name="Tutorial-UsageandExamples"></a>Usage and Examples</h1>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
The following examples highlight some salient features of the system. A detailed set of query test cases can be found at <a href="http://svn.apache.org/viewvc/hive/trunk/ql/src/test/queries/clientpositive/" rel="nofollow" class="external-link" style="color:rgb(0,51,102)">Hive
 Query Test Cases</a> and the corresponding results can be found at <a href="http://svn.apache.org/viewvc/hive/trunk/ql/src/test/results/clientpositive/" rel="nofollow" class="external-link" style="color:rgb(0,51,102)">Query Test Case Results</a></p>
<h2 style="line-height:normal; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; font-size:18pt; margin-top:32px; margin-right:0px; margin-bottom:4px; margin-left:0px; color:rgb(0,51,102); border-bottom-color:rgb(102,153,204)">
<a name="Tutorial-CreatingTables"></a>Creating Tables</h2>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
An example statement that would create the page_view table mentioned above would be like:</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    CREATE TABLE page_view(viewTime INT, userid BIGINT,
                    page_url STRING, referrer_url STRING,
                    ip STRING COMMENT 'IP Address of the User')
    COMMENT 'This is the page view table'
    PARTITIONED BY(dt STRING, country STRING)
    STORED AS SEQUENCEFILE;
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
In this example the columns of the table are specified with the corresponding types. Comments can be attached both at the column level as well as at the table level. Additionally the partitioned by clause defines the partitioning columns which are different
 from the data columns and are actually not stored with the data. When specified in this way, the data in the files is assumed to be delimited with ASCII 001(ctrl-A) as the field delimiter and newline as the row delimiter.</p>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
The field delimiter can be parametrized if the data is not in the above format as illustrated in the following example:</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    CREATE TABLE page_view(viewTime INT, userid BIGINT,
                    page_url STRING, referrer_url STRING,
                    ip STRING COMMENT 'IP Address of the User')
    COMMENT 'This is the page view table'
    PARTITIONED BY(dt STRING, country STRING)
    ROW FORMAT DELIMITED
            FIELDS TERMINATED BY '1'
    STORED AS SEQUENCEFILE;
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
The row deliminator currently cannot be changed since it is not determined by Hive but Hadoop. e delimiters.</p>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
It is also a good idea to bucket the tables on certain columns so that efficient sampling queries can be executed against the data set. If bucketing is absent, random sampling can still be done on the table but it is not efficient as the query has to scan all
 the data. The following example illustrates the case of the page_view table that is bucketed on the userid column:</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    CREATE TABLE page_view(viewTime INT, userid BIGINT,
                    page_url STRING, referrer_url STRING,
                    ip STRING COMMENT 'IP Address of the User')
    COMMENT 'This is the page view table'
    PARTITIONED BY(dt STRING, country STRING)
    CLUSTERED BY(userid) SORTED BY(viewTime) INTO 32 BUCKETS
    ROW FORMAT DELIMITED
            FIELDS TERMINATED BY '1'
            COLLECTION ITEMS TERMINATED BY '2'
            MAP KEYS TERMINATED BY '3'
    STORED AS SEQUENCEFILE;
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
In the example above, the table is clustered by a hash function of userid into 32 buckets. Within each bucket the data is sorted in increasing order of viewTime. Such an organization allows the user to do efficient sampling on the clustered column - in this
 case userid. The sorting property allows internal operators to take advantage of the better-known data structure while evaluating queries with greater efficiency.</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    CREATE TABLE page_view(viewTime INT, userid BIGINT,
                    page_url STRING, referrer_url STRING,
                    friends ARRAY&lt;BIGINT&gt;, properties MAP&lt;STRING, STRING&gt;
                    ip STRING COMMENT 'IP Address of the User')
    COMMENT 'This is the page view table'
    PARTITIONED BY(dt STRING, country STRING)
    CLUSTERED BY(userid) SORTED BY(viewTime) INTO 32 BUCKETS
    ROW FORMAT DELIMITED
            FIELDS TERMINATED BY '1'
            COLLECTION ITEMS TERMINATED BY '2'
            MAP KEYS TERMINATED BY '3'
    STORED AS SEQUENCEFILE;
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
In this example the columns that comprise of the table row are specified in a similar way as the definition of types. Comments can be attached both at the column level as well as at the table level. Additionally the partitioned by clause defines the partitioning
 columns which are different from the data columns and are actually not stored with the data. The CLUSTERED BY clause specifies which column to use for bucketing as well as how many buckets to create. The delimited row format specifies how the rows are stored
 in the hive table. In the case of the delimited format, this specifies how the fields are terminated, how the items within collections (arrays or maps) are terminated and how the map keys are terminated. STORED AS SEQUENCEFILE indicates that this data is stored
 in a binary format (using hadoop SequenceFiles) on hdfs. The values shown for the ROW FORMAT and STORED AS clauses in the above example represent the system defaults.</p>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
Table names and column names are case insensitive.</p>
<h2 style="line-height:normal; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; font-size:18pt; margin-top:32px; margin-right:0px; margin-bottom:4px; margin-left:0px; color:rgb(0,51,102); border-bottom-color:rgb(102,153,204)">
<a name="Tutorial-BrowsingTablesandPartitions"></a>Browsing Tables and Partitions</h2>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    SHOW TABLES;
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
To list existing tables in the warehouse; there are many of these, likely more than you want to browse.</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    SHOW TABLES 'page.*';
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
To list tables with prefix 'page'. The pattern follows Java regular expression syntax (so the period is a wildcard).</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    SHOW PARTITIONS page_view;
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
To list partitions of a table. If the table is not a partitioned table then an error is thrown.</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    DESCRIBE page_view;
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
To list columns and column types of table.</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    DESCRIBE EXTENDED page_view;
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
To list columns and all other properties of table. This prints lot of information and that too not in a pretty format. Usually used for debugging.</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">   DESCRIBE EXTENDED page_view PARTITION (ds='2008-08-08');
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
To list columns and all other properties of a partition. This also prints lot of information which is usually used for debugging.</p>
<h2 style="line-height:normal; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; font-size:18pt; margin-top:32px; margin-right:0px; margin-bottom:4px; margin-left:0px; color:rgb(0,51,102); border-bottom-color:rgb(102,153,204)">
<a name="Tutorial-LoadingData"></a>Loading Data</h2>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
There are multiple ways to load data into Hive tables. The user can create an external table that points to a specified location within <a href="http://hadoop.apache.org/common/docs/current/hdfs_design.html" rel="nofollow" class="external-link" style="color:rgb(0,51,102)">HDFS</a>.
 In this particular usage, the user can copy a file into the specified location using the HDFS put or copy commands and create a table pointing to this location with all the relevant row format information. Once this is done, the user can transform the data
 and insert them into any other Hive table. For example, if the file /tmp/pv_2008-06-08.txt contains comma separated page views served on 2008-06-08, and this needs to be loaded into the page_view table in the appropriate partition, the following sequence of
 commands can achieve this:</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    CREATE EXTERNAL TABLE page_view_stg(viewTime INT, userid BIGINT,
                    page_url STRING, referrer_url STRING,
                    ip STRING COMMENT 'IP Address of the User',
                    country STRING COMMENT 'country of origination')
    COMMENT 'This is the staging page view table'
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '44' LINES TERMINATED BY '12'
    STORED AS TEXTFILE
    LOCATION '/user/data/staging/page_view';

    hadoop dfs -put /tmp/pv_2008-06-08.txt /user/data/staging/page_view

    FROM page_view_stg pvs
    INSERT OVERWRITE TABLE page_view PARTITION(dt='2008-06-08', country='US')
    SELECT pvs.viewTime, pvs.userid, pvs.page_url, pvs.referrer_url, <span class="code-keyword" style="color:rgb(0,0,145); background-color:inherit">null</span>, <span class="code-keyword" style="color:rgb(0,0,145); background-color:inherit">null</span>, pvs.ip
    WHERE pvs.country = 'US';
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
In the example above nulls are inserted for the array and map types in the destination tables but potentially these can also come from the external table if the proper row formats are specified.</p>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
This method is useful if there is already legacy data in HDFS on which the user wants to put some metadata so that the data can be queried and manipulated using Hive.</p>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
Additionally, the system also supports syntax that can load the data from a file in the local files system directly into a Hive table where the input data format is the same as the table format. If /tmp/pv_2008-06-08_us.txt already contains the data for US,
 then we do not need any additional filtering as shown in the previous example. The load in this case can be done using the following syntax:</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">   LOAD DATA LOCAL INPATH /tmp/pv_2008-06-08_us.txt INTO TABLE page_view PARTITION(date='2008-06-08', country='US')
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
The path argument can take a directory (in which case all the files in the directory are loaded), a single file name, or a wildcard (in which case all the matching files are uploaded). If the argument is a directory - it cannot contain subdirectories. Similarly
 - the wildcard must match file names only.</p>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
In the case that the input file /tmp/pv_2008-06-08_us.txt is very large, the user may decide to do a parallel load of the data (using tools that are external to Hive). Once the file is in HDFS - the following syntax can be used to load the data into a Hive
 table:</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">   LOAD DATA INPATH '/user/data/pv_2008-06-08_us.txt' INTO TABLE page_view PARTITION(date='2008-06-08', country='US')
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
It is assumed that the array and map fields in the input.txt files are null fields for these examples.</p>
<h2 style="line-height:normal; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; font-size:18pt; margin-top:32px; margin-right:0px; margin-bottom:4px; margin-left:0px; color:rgb(0,51,102); border-bottom-color:rgb(102,153,204)">
<a name="Tutorial-SimpleQuery"></a>Simple Query</h2>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
For all the active users, one can use the query of the following form:</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    INSERT OVERWRITE TABLE user_active
    SELECT user.*
    FROM user
    WHERE user.active = 1;
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
Note that unlike SQL, we always insert the results into a table. We will illustrate later how the user can inspect these results and even dump them to a local file. You can also run the following query on Hive CLI:</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    SELECT user.*
    FROM user
    WHERE user.active = 1;
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
This will be internally rewritten to some temporary file and displayed to the Hive client side.</p>
<h2 style="line-height:normal; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; font-size:18pt; margin-top:32px; margin-right:0px; margin-bottom:4px; margin-left:0px; color:rgb(0,51,102); border-bottom-color:rgb(102,153,204)">
<a name="Tutorial-PartitionBasedQuery"></a>Partition Based Query</h2>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
What partitions to use in a query is determined automatically by the system on the basis of where clause conditions on partition columns. For example, in order to get all the page_views in the month of 03/2008 referred from domain xyz.com, one could write the
 following query:</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    INSERT OVERWRITE TABLE xyz_com_page_views
    SELECT page_views.*
    FROM page_views
    WHERE page_views.date &gt;= '2008-03-01' AND page_views.date &lt;= '2008-03-31' AND
          page_views.referrer_url like '%xyz.com';
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
Note that page_views.date is used here because the table (above) was defined with PARTITIONED BY(date DATETIME, country STRING) ; if you name your partition something different, don't expect .date to do what you think!</p>
<h2 style="line-height:normal; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; font-size:18pt; margin-top:32px; margin-right:0px; margin-bottom:4px; margin-left:0px; color:rgb(0,51,102); border-bottom-color:rgb(102,153,204)">
<a name="Tutorial-Joins"></a>Joins</h2>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
In order to get a demographic breakdown (by gender) of page_view of 2008-03-03 one would need to join the page_view table and the user table on the userid column. This can be accomplished with a join as shown in the following query:</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    INSERT OVERWRITE TABLE pv_users
    SELECT pv.*, u.gender, u.age
    FROM user u JOIN page_view pv ON (pv.userid = u.id)
    WHERE pv.date = '2008-03-03';
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
In order to do outer joins the user can qualify the join with LEFT OUTER, RIGHT OUTER or FULL OUTER keywords in order to indicate the kind of outer join (left preserved, right preserved or both sides preserved). For example, in order to do a full outer join
 in the query above, the corresponding syntax would look like the following query:</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    INSERT OVERWRITE TABLE pv_users
    SELECT pv.*, u.gender, u.age
    FROM user u FULL OUTER JOIN page_view pv ON (pv.userid = u.id)
    WHERE pv.date = '2008-03-03';
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
In order check the existence of a key in another table, the user can use LEFT SEMI JOIN as illustrated by the following example.</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    INSERT OVERWRITE TABLE pv_users
    SELECT u.*
    FROM user u LEFT SEMI JOIN page_view pv ON (pv.userid = u.id)
    WHERE pv.date = '2008-03-03';
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
In order to join more than one tables, the user can use the following syntax:</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    INSERT OVERWRITE TABLE pv_friends
    SELECT pv.*, u.gender, u.age, f.friends
    FROM page_view pv JOIN user u ON (pv.userid = u.id) JOIN friend_list f ON (u.id = f.uid)
    WHERE pv.date = '2008-03-03';
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
Note that Hive only supports <a href="http://en.wikipedia.org/wiki/Join_(SQL)#Equi-join" rel="nofollow" class="external-link" style="color:rgb(0,51,102)">equi-joins</a>. Also it is best to put the largest table on the rightmost side of the join to get the best
 performance.</p>
<h2 style="line-height:normal; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; font-size:18pt; margin-top:32px; margin-right:0px; margin-bottom:4px; margin-left:0px; color:rgb(0,51,102); border-bottom-color:rgb(102,153,204)">
<a name="Tutorial-Aggregations"></a>Aggregations</h2>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
In order to count the number of distinct users by gender one could write the following query:</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    INSERT OVERWRITE TABLE pv_gender_sum
    SELECT pv_users.gender, count (DISTINCT pv_users.userid)
    FROM pv_users
    GROUP BY pv_users.gender;
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
Multiple aggregations can be done at the same time, however, no two aggregations can have different DISTINCT columns .e.g while the following is possible</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    INSERT OVERWRITE TABLE pv_gender_agg
    SELECT pv_users.gender, count(DISTINCT pv_users.userid), count(*), sum(DISTINCT pv_users.userid)
    FROM pv_users
    GROUP BY pv_users.gender;
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
however, the following query is not allowed</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    INSERT OVERWRITE TABLE pv_gender_agg
    SELECT pv_users.gender, count(DISTINCT pv_users.userid), count(DISTINCT pv_users.ip)
    FROM pv_users
    GROUP BY pv_users.gender;
</code></pre>
</div>
</div>
<h2 style="line-height:normal; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; font-size:18pt; margin-top:32px; margin-right:0px; margin-bottom:4px; margin-left:0px; color:rgb(0,51,102); border-bottom-color:rgb(102,153,204)">
<a name="Tutorial-MultiTable%2FFileInserts"></a>Multi Table/File Inserts</h2>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
The output of the aggregations or simple selects can be further sent into multiple tables or even to hadoop dfs files (which can then be manipulated using hdfs utilities). e.g. if along with the gender breakdown, one needed to find the breakdown of unique page
 views by age, one could accomplish that with the following query:</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    FROM pv_users
    INSERT OVERWRITE TABLE pv_gender_sum
        SELECT pv_users.gender, count_distinct(pv_users.userid)
        GROUP BY pv_users.gender

    INSERT OVERWRITE DIRECTORY '/user/data/tmp/pv_age_sum'
        SELECT pv_users.age, count_distinct(pv_users.userid)
        GROUP BY pv_users.age;
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
The first insert clause sends the results of the first group by to a Hive table while the second one sends the results to a hadoop dfs files.</p>
<h2 style="line-height:normal; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; font-size:18pt; margin-top:32px; margin-right:0px; margin-bottom:4px; margin-left:0px; color:rgb(0,51,102); border-bottom-color:rgb(102,153,204)">
<a name="Tutorial-DynamicpartitionInsert"></a>Dynamic-partition Insert</h2>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
In the previous examples, the user has to know which partition to insert into and only one partition can be inserted in one insert statement. If you want to load into multiple partitions, you have to use multi-insert statement as illustrated below.</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    FROM page_view_stg pvs
    INSERT OVERWRITE TABLE page_view PARTITION(dt='2008-06-08', country='US')
           SELECT pvs.viewTime, pvs.userid, pvs.page_url, pvs.referrer_url, <span class="code-keyword" style="color:rgb(0,0,145); background-color:inherit">null</span>, <span class="code-keyword" style="color:rgb(0,0,145); background-color:inherit">null</span>, pvs.ip WHERE pvs.country = 'US'
    INSERT OVERWRITE TABLE page_view PARTITION(dt='2008-06-08', country='CA')
           SELECT pvs.viewTime, pvs.userid, pvs.page_url, pvs.referrer_url, <span class="code-keyword" style="color:rgb(0,0,145); background-color:inherit">null</span>, <span class="code-keyword" style="color:rgb(0,0,145); background-color:inherit">null</span>, pvs.ip WHERE pvs.country = 'CA'
    INSERT OVERWRITE TABLE page_view PARTITION(dt='2008-06-08', country='UK')
           SELECT pvs.viewTime, pvs.userid, pvs.page_url, pvs.referrer_url, <span class="code-keyword" style="color:rgb(0,0,145); background-color:inherit">null</span>, <span class="code-keyword" style="color:rgb(0,0,145); background-color:inherit">null</span>, pvs.ip WHERE pvs.country = 'UK';
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
In order to load data into all country partitions in a particular day, you have to add an insert statement for each country in the input data. This is very inconvenient since you have to have the priori knowledge of the list of countries exist in the input
 data and create the partitions beforehand. If the list changed for another day, you have to modify your insert DML as well as the partition creation DDLs. It is also inefficient since each insert statement may be turned into a MapReduce Job.</p>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
<em>Dynamic-partition insert</em> (or multi-partition insert) is designed to solve this problem by dynamically determining which partitions should be created and populated while scanning the input table. This is a newly added feature that is only available
 from version 0.6.0. In the dynamic partition insert, the input column values are evaluated to determine which partition this row should be inserted into. If that partition has not been created, it will create that partition automatically. Using this feature
 you need only one insert statement to create and populate all necessary partitions. In addition, since there is only one insert statement, there is only one corresponding MapReduce job. This significantly improves performance and reduce the Hadoop cluster
 workload comparing to the multiple insert case.</p>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
Below is an example of loading data to all country partitions using one insert statement:</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    FROM page_view_stg pvs
    INSERT OVERWRITE TABLE page_view PARTITION(dt='2008-06-08', country)
           SELECT pvs.viewTime, pvs.userid, pvs.page_url, pvs.referrer_url, <span class="code-keyword" style="color:rgb(0,0,145); background-color:inherit">null</span>, <span class="code-keyword" style="color:rgb(0,0,145); background-color:inherit">null</span>, pvs.ip, pvs.country
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
There are several syntactic differences from the multi-insert statement:</p>
<ul style="font-size:10pt; line-height:13pt">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
country appears in the PARTITION specification, but with no value associated. In this case, country is a <em>dynamic partition column</em>. On the other hand, ds has a value associated with it, which means it is a <em>static partition column</em>. If a column
 is dynamic partition column, its value will be coming from the input column. Currently we only allow dynamic partition columns to be the last column(s) in the partition clause because the partition column order indicates its hierarchical order (meaning dt
 is the root partition, and country is the child partition). You cannot specify a partition clause with (dt, country='US') because that means you need to update all partitions with any date and its country sub-partition is 'US'.</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
An additional pvs.country column is added in the select statement. This is the corresponding input column for the dynamic partition column. Note that you do not need to add an input column for the static partition column because its value is already known in
 the PARTITION clause. Note that the dynamic partition values are selected by ordering, not name, and taken as the last columns from the select clause.</li></ul>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
Semantics of the dynamic partition insert statement:</p>
<ul style="font-size:10pt; line-height:13pt">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
When there are already non-empty partitions exists for the dynamic partition columns, (e.g., country='CA' exists under some ds root partition), it will be overwritten if the dynamic partition insert saw the same value (say 'CA') in the input data. This is in
 line with the 'insert overwrite' semantics. However, if the partition value 'CA' does not appear in the input data, the existing partition will not be overwritten.</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
Since a Hive partition corresponds to a directory in HDFS, the partition value has to conform to the HDFS path format (URI in Java). Any character having a special meaning in URI (e.g., '%', ':', '/', '#') will be escaped with '%' followed by 2 bytes of its
 ASCII value.</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
If the input column is a type different than STRING, its value will be first converted to STRING to be used to construct the HDFS path.</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
If the input column value is NULL or empty string, the row will be put into a special partition, whose name is controlled by the hive parameter hive.exec.default.partition.name. The default value is<tt>__HIVE_DEFAULT_PARTITION__</tt>. Basically this partition
 will contain all "bad" rows whose value are not valid partition names. The caveat of this approach is that the bad value will be lost and is replaced by<tt>__HIVE_DEFAULT_PARTITION__</tt> if you select them Hive. JIRA HIVE-1309 is a solution to let user specify
 "bad file" to retain the input partition column values as well.</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
Dynamic partition insert could potentially resource hog in that it could generate a large number of partitions in a short time. To get yourself buckled, we define three parameters:
<ul style="font-size:10pt; line-height:13pt; list-style-type:disc">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<strong>hive.exec.max.dynamic.partitions.pernode</strong> (default value being 100) is the maximum dynamic partitions that can be created by each mapper or reducer. If one mapper or reducer created more than that the threshold, a fatal error will be raised
 from the mapper/reducer (through counter) and the whole job will be killed.</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<strong>hive.exec.max.dynamic.partitions</strong> (default value being 1000) is the total number of dynamic partitions could be created by one DML. If each mapper/reducer did not exceed the limit but the total number of dynamic partitions does, then an exception
 is raised at the end of the job before the intermediate data are moved to the final destination.</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
<strong>hive.exec.max.created.files</strong> (default value being 100000) is the maximum total number of files created by all mappers and reducers. This is implemented by updating a Hadoop counter by each mapper/reducer whenever a new file is created. If the
 total number is exceeding hive.exec.max.created.files, a fatal error will be thrown and the job will be killed.</li></ul>
</li></ul>
<ul style="font-size:10pt; line-height:13pt">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
Another situation we want to protect against dynamic partition insert is that the user may accidentally specify all partitions to be dynamic partitions without specifying one static partition, while the original intention is to just overwrite the sub-partitions
 of one root partition. We define another parameter hive.exec.dynamic.partition.mode=strict to prevent the all-dynamic partition case. In the strict mode, you have to specify at least one static partition. The default mode is strict. In addition, we have a
 parameter hive.exec.dynamic.partition=true/false to control whether to allow dynamic partition at all. The default value is false.</li><li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
In Hive 0.6, dynamic partition insert does not work with hive.merge.mapfiles=true or hive.merge.mapredfiles=true, so it internally turns off the merge parameters. Merging files in dynamic partition inserts are supported in Hive 0.7 (see JIRA HIVE-1307 for details).</li></ul>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
Troubleshooting and best practices:</p>
<ul style="font-size:10pt; line-height:13pt">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
As stated above, there are too many dynamic partitions created by a particular mapper/reducer, a fatal error could be raised and the job will be killed. The error message looks something like:
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    hive&gt; set hive.exec.dynamic.partition.mode=nonstrict;
    hive&gt; FROM page_view_stg pvs
          INSERT OVERWRITE TABLE page_view PARTITION(dt, country)
                 SELECT pvs.viewTime, pvs.userid, pvs.page_url, pvs.referrer_url, <span class="code-keyword" style="color:rgb(0,0,145); background-color:inherit">null</span>, <span class="code-keyword" style="color:rgb(0,0,145); background-color:inherit">null</span>, pvs.ip,
                        from_unixtimestamp(pvs.viewTime, 'yyyy-MM-dd') ds, pvs.country;
...
2010-05-07 11:10:19,816 Stage-1 map = 0%,  reduce = 0%
[Fatal Error] Operator FS_28 (id=41): fatal error. Killing the job.
Ended Job = job_201005052204_28178 with errors
...
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
The problem of this that one mapper will take a random set of rows and it is very likely that the number of distinct (dt, country) pairs will exceed the limit of hive.exec.max.dynamic.partitions.pernode. One way around it is to group the rows by the dynamic
 partition columns in the mapper and distribute them to the reducers where the dynamic partitions will be created. In this case the number of distinct dynamic partitions will be significantly reduced. The above example query could be rewritten to:</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    hive&gt; set hive.exec.dynamic.partition.mode=nonstrict;
    hive&gt; FROM page_view_stg pvs
          INSERT OVERWRITE TABLE page_view PARTITION(dt, country)
                 SELECT pvs.viewTime, pvs.userid, pvs.page_url, pvs.referrer_url, <span class="code-keyword" style="color:rgb(0,0,145); background-color:inherit">null</span>, <span class="code-keyword" style="color:rgb(0,0,145); background-color:inherit">null</span>, pvs.ip,
                        from_unixtimestamp(pvs.viewTime, 'yyyy-MM-dd') ds, pvs.country
                 DISTRIBUTE BY ds, country;
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
This query will generate a MapReduce job rather than Map-only job. The SELECT-clause will be converted to a plan to the mappers and the output will be distributed to the reducers based on the value of (ds, country) pairs. The INSERT-clause will be converted
 to the plan in the reducer which writes to the dynamic partitions.</p>
<h2 style="line-height:normal; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; font-size:18pt; margin-top:32px; margin-right:0px; margin-bottom:4px; margin-left:0px; color:rgb(0,51,102); border-bottom-color:rgb(102,153,204)">
<a name="Tutorial-Insertingintolocalfiles"></a>Inserting into local files</h2>
<p style="font-size:10pt; line-height:13pt; margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
In certain situations you would want to write the output into a local file so that you could load it into an excel spreadsheet. This can be accomplished with the following command:</p>
</li></ul>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    INSERT OVERWRITE LOCAL DIRECTORY '/tmp/pv_gender_sum'
    SELECT pv_gender_sum.*
    FROM pv_gender_sum;
</code></pre>
</div>
</div>
<h2 style="line-height:normal; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; font-size:18pt; margin-top:32px; margin-right:0px; margin-bottom:4px; margin-left:0px; color:rgb(0,51,102); border-bottom-color:rgb(102,153,204)">
<a name="Tutorial-Sampling"></a>Sampling</h2>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
The sampling clause allows the users to write queries for samples of the data instead of the whole table. Currently the sampling is done on the columns that are specified in the CLUSTERED BY clause of the CREATE TABLE statement. In the following example we
 choose 3rd bucket out of the 32 buckets of the pv_gender_sum table:</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    INSERT OVERWRITE TABLE pv_gender_sum_sample
    SELECT pv_gender_sum.*
    FROM pv_gender_sum TABLESAMPLE(BUCKET 3 OUT OF 32);
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
In general the TABLESAMPLE syntax looks like:</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    TABLESAMPLE(BUCKET x OUT OF y)
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
y has to be a multiple or divisor of the number of buckets in that table as specified at the table creation time. The buckets chosen are determined if bucket_number module y is equal to x. So in the above example the following tablesample clause</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">      TABLESAMPLE(BUCKET 3 OUT OF 16)
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
would pick out the 3rd and 19th buckets. The buckets are numbered starting from 0.</p>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
On the other hand the tablesample clause</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">     TABLESAMPLE(BUCKET 3 OUT OF 64 ON userid)
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
would pick out half of the 3rd bucket.</p>
<h2 style="line-height:normal; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; font-size:18pt; margin-top:32px; margin-right:0px; margin-bottom:4px; margin-left:0px; color:rgb(0,51,102); border-bottom-color:rgb(102,153,204)">
<a name="Tutorial-Unionall"></a>Union all</h2>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
The language also supports union all, e.g. if we suppose there are two different tables that track which user has published a video and which user has published a comment, the following query joins the results of a union all with the user table to create a
 single annotated stream for all the video publishing and comment publishing events:</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    INSERT OVERWRITE TABLE actions_users
    SELECT u.id, actions.date
    FROM (
        SELECT av.uid AS uid
        FROM action_video av
        WHERE av.date = '2008-06-03'

        UNION ALL

        SELECT ac.uid AS uid
        FROM action_comment ac
        WHERE ac.date = '2008-06-03'
        ) actions JOIN users u ON(u.id = actions.uid);
</code></pre>
</div>
</div>
<h2 style="line-height:normal; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; font-size:18pt; margin-top:32px; margin-right:0px; margin-bottom:4px; margin-left:0px; color:rgb(0,51,102); border-bottom-color:rgb(102,153,204)">
<a name="Tutorial-ArrayOperations"></a>Array Operations</h2>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
Array columns in tables can only be created programmatically currently. We will be extending this soon to be available as part of the create table statement. For the purpose of the current example assume that pv.friends is of the type array&lt;INT&gt; i.e. it is
 an array of integers.The user can get a specific element in the array by its index as shown in the following command:</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    SELECT pv.friends[2]
    FROM page_views pv;
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
The select expressions gets the third item in the pv.friends array.</p>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
The user can also get the length of the array using the size function as shown below:</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">   SELECT pv.userid, size(pv.friends)
   FROM page_view pv;
</code></pre>
</div>
</div>
<h2 style="line-height:normal; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; font-size:18pt; margin-top:32px; margin-right:0px; margin-bottom:4px; margin-left:0px; color:rgb(0,51,102); border-bottom-color:rgb(102,153,204)">
<a name="Tutorial-Map%28AssociativeArrays%29Operations"></a>Map(Associative Arrays) Operations</h2>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
Maps provide collections similar to associative arrays. Such structures can only be created programmatically currently. We will be extending this soon. For the purpose of the current example assume that pv.properties is of the type map&lt;String, String&gt; i.e.
 it is an associative array from strings to string. Accordingly, the following query:</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    INSERT OVERWRITE page_views_map
    SELECT pv.userid, pv.properties['page type']
    FROM page_views pv;
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
can be used to select the 'page_type' property from the page_views table.</p>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
Similar to arrays, the size function can also be used to get the number of elements in a map as shown in the following query:</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">   SELECT size(pv.properties)
   FROM page_view pv;
</code></pre>
</div>
</div>
<h2 style="line-height:normal; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; font-size:18pt; margin-top:32px; margin-right:0px; margin-bottom:4px; margin-left:0px; color:rgb(0,51,102); border-bottom-color:rgb(102,153,204)">
<a name="Tutorial-Custommap%2Freducescripts"></a>Custom map/reduce scripts</h2>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
Users can also plug in their own custom mappers and reducers in the data stream by using features natively supported in the Hive language. e.g. in order to run a custom mapper script - map_script - and a custom reducer script - reduce_script - the user can
 issue the following command which uses the TRANSFORM clause to embed the mapper and the reducer scripts.</p>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
Note that columns will be transformed to string and delimited by TAB before feeding to the user script, and the standard output of the user script will be treated as TAB-separated string columns. User scripts can output debug information to standard error which
 will be shown on the task detail page on hadoop.</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">   FROM (
        FROM pv_users
        MAP pv_users.userid, pv_users.date
        USING 'map_script'
        AS dt, uid
        CLUSTER BY dt) map_output

    INSERT OVERWRITE TABLE pv_users_reduced
        REDUCE map_output.dt, map_output.uid
        USING 'reduce_script'
        AS date, count;
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
Sample map script (weekday_mapper.py )</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java"><span class="code-keyword" style="color:rgb(0,0,145); background-color:inherit">import</span> sys
<span class="code-keyword" style="color:rgb(0,0,145); background-color:inherit">import</span> datetime

<span class="code-keyword" style="color:rgb(0,0,145); background-color:inherit">for</span> line in sys.stdin:
  line = line.strip()
  userid, unixtime = line.split('\t')
  weekday = datetime.datetime.fromtimestamp(<span class="code-object" style="color:rgb(145,0,145); background-color:inherit">float</span>(unixtime)).isoweekday()
  print ','.join([userid, str(weekday)])
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
Of course, both MAP and REDUCE are "syntactic sugar" for the more general select transform. The inner query could also have been written as such:</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    SELECT TRANSFORM(pv_users.userid, pv_users.date) USING 'map_script' AS dt, uid CLUSTER BY dt FROM pv_users;
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
Schema-less map/reduce: If there is no "AS" clause after "USING map_script", Hive assumes the output of the script contains 2 parts: key which is before the first tab, and value which is the rest after the first tab. Note that this is different from specifying
 "AS key, value" because in that case value will only contains the portion between the first tab and the second tab if there are multiple tabs.</p>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
In this way, we allow users to migrate old map/reduce scripts without knowing the schema of the map output. User still needs to know the reduce output schema because that has to match what is in the table that we are inserting to.</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    FROM (
        FROM pv_users
        MAP pv_users.userid, pv_users.date
        USING 'map_script'
        CLUSTER BY key) map_output

    INSERT OVERWRITE TABLE pv_users_reduced

        REDUCE map_output.dt, map_output.uid
        USING 'reduce_script'
        AS date, count;
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
Distribute By and Sort By: Instead of specifying "cluster by", the user can specify "distribute by" and "sort by", so the partition columns and sort columns can be different. The usual case is that the partition columns are a prefix of sort columns, but that
 is not required.</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    FROM (
        FROM pv_users
        MAP pv_users.userid, pv_users.date
        USING 'map_script'
        AS c1, c2, c3
        DISTRIBUTE BY c2
        SORT BY c2, c1) map_output

    INSERT OVERWRITE TABLE pv_users_reduced

        REDUCE map_output.c1, map_output.c2, map_output.c3
        USING 'reduce_script'
        AS date, count;
</code></pre>
</div>
</div>
<h2 style="line-height:normal; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; font-size:18pt; margin-top:32px; margin-right:0px; margin-bottom:4px; margin-left:0px; color:rgb(0,51,102); border-bottom-color:rgb(102,153,204)">
<a name="Tutorial-CoGroups"></a>Co-Groups</h2>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
Amongst the user community using map/reduce, cogroup is a fairly common operation wherein the data from multiple tables are sent to a custom reducer such that the rows are grouped by the values of certain columns on the tables. With the UNION ALL operator and
 the CLUSTER BY specification, this can be achieved in the Hive query language in the following way. Suppose we wanted to cogroup the rows from the actions_video and action_comments table on the uid column and send them to the 'reduce_script' custom reducer,
 the following syntax can be used by the user:</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">   FROM (
        FROM (
                FROM action_video av
                SELECT av.uid AS uid, av.id AS id, av.date AS date

               UNION ALL

                FROM action_comment ac
                SELECT ac.uid AS uid, ac.id AS id, ac.date AS date
        ) union_actions
        SELECT union_actions.uid, union_actions.id, union_actions.date
        CLUSTER BY union_actions.uid) map

    INSERT OVERWRITE TABLE actions_reduced
        SELECT TRANSFORM(map.uid, map.id, map.date) USING 'reduce_script' AS (uid, id, reduced_val);
</code></pre>
</div>
</div>
<h2 style="line-height:normal; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; font-size:18pt; margin-top:32px; margin-right:0px; margin-bottom:4px; margin-left:0px; color:rgb(0,51,102); border-bottom-color:rgb(102,153,204)">
<a name="Tutorial-AlteringTables"></a>Altering Tables</h2>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
To rename existing table to a new name. If a table with new name already exists then an error is returned:</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    ALTER TABLE old_table_name RENAME TO new_table_name;
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
To rename the columns of an existing table. Be sure to use the same column types, and to include an entry for each preexisting column:</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    ALTER TABLE old_table_name REPLACE COLUMNS (col1 TYPE, ...);
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
To add columns to an existing table:</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    ALTER TABLE tab1 ADD COLUMNS (c1 INT COMMENT 'a <span class="code-keyword" style="color:rgb(0,0,145); background-color:inherit">new</span> <span class="code-object" style="color:rgb(145,0,145); background-color:inherit">int</span> column', c2 STRING DEFAULT 'def val');
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
Note that a change in the schema (such as the adding of the columns), preserves the schema for the old partitions of the table in case it is a partitioned table. All the queries that access these columns and run over the old partitions implicitly return a null
 value or the specified default values for these columns.</p>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
In the later versions we can make the behavior of assuming certain values as opposed to throwing an error in case the column is not found in a particular partition configurable.</p>
<h2 style="line-height:normal; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; font-size:18pt; margin-top:32px; margin-right:0px; margin-bottom:4px; margin-left:0px; color:rgb(0,51,102); border-bottom-color:rgb(102,153,204)">
<a name="Tutorial-DroppingTablesandPartitions"></a>Dropping Tables and Partitions</h2>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
Dropping tables is fairly trivial. A drop on the table would implicitly drop any indexes(this is a future feature) that would have been built on the table. The associated command is</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    DROP TABLE pv_users;
</code></pre>
</div>
</div>
<p style="font-size:10pt; line-height:13pt; margin-top:10px; margin-bottom:10px; padding-top:0px; padding-bottom:0px">
To dropping a partition. Alter the table to drop the partition.</p>
<div class="code panel" style="padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px; margin-top:10px; margin-right:20px; margin-bottom:10px; margin-left:20px; border-top-width:1px; border-right-width:1px; border-bottom-width:1px; border-left-width:1px; border-top-style:dashed; border-right-style:dashed; border-bottom-style:dashed; border-left-style:dashed; overflow-x:auto; overflow-y:auto; border-top-color:rgb(102,153,204); border-right-color:rgb(102,153,204); border-bottom-color:rgb(102,153,204); border-left-color:rgb(102,153,204)">
<div class="codeContent panelContent" style="margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:12px; padding-right:10px; padding-bottom:12px; padding-left:10px; font-size:0.95em">
<pre><code class="language-java">    ALTER TABLE pv_users DROP PARTITION (ds='2008-08-08')
</code></pre>
</div>
</div>
<ul style="font-size:10pt; line-height:13pt">
<li style="font-size:10pt; line-height:13pt; margin-top:0px; margin-right:0px; margin-bottom:0px; margin-left:0px; padding-top:0px; padding-right:0px; padding-bottom:0px; padding-left:0px">
Note that any data for this table or partitions will be dropped and may not be recoverable. *</li></ul>
</div>
</div>
</div>
<div class="footer" style="padding-top:4px; color:rgb(102,102,102); font-family:Helvetica,Arial,sans-serif; font-size:13px; line-height:17px">
Generated by <a href="http://www.atlassian.com/confluence/" rel="nofollow" style="color:rgb(0,51,102)">Atlassian Confluence</a> (Version: 3.4.9 Build: 2042 Feb 14, 2011) <a href="http://could.it/autoexport/" rel="nofollow" style="color:rgb(0,51,102)">Auto Export Plugin</a> (Version: 1.0.0-dkulp)</div>
            </div>
                </div>