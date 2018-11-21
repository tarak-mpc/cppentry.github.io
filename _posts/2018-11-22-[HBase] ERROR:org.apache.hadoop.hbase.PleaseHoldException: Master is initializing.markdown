---
layout:     post
title:      [HBase] ERROR:org.apache.hadoop.hbase.PleaseHoldException: Master is initializing
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="article_title" style="color:rgb(51,51,51);font-family:'microsoft yahei';font-size:16px;line-height:30.4px;">
<h1 style="font-size:32px;"><span class="link_title">[HBase] ERROR:org.apache.hadoop.hbase.PleaseHoldException: Master is initializing         </span>   </h1>
</div>
<div class="article_content" id="article_content" style="color:rgb(51,51,51);font-family:'microsoft yahei';font-size:16px;line-height:30.4px;">
<div class="dp-highlighter bg_plain">
<ol><li style="list-style:decimal;">mango@master:~/hadoop-1.1.2//bin$ hbase shell  </li><li class="alt" style="list-style:decimal;">HBase Shell; enter 'help&lt;RETURN&gt;' for list of supported commands.  </li><li style="list-style:decimal;">Type "exit&lt;RETURN&gt;" to leave the HBase Shell  </li><li class="alt" style="list-style:decimal;">Version 0.94.2, r1395367, Sun Oct  7 19:11:01 UTC 2016  </li><li style="list-style:decimal;">  </li><li class="alt" style="list-style:decimal;">hbase(main):001:0&gt; status  </li><li style="list-style:decimal;">3 servers, 0 dead, 0.0000 average load  </li><li class="alt" style="list-style:decimal;">  </li><li style="list-style:decimal;">hbase(main):002:0&gt; create 't1','f1'  </li><li class="alt" style="list-style:decimal;">  </li><li style="list-style:decimal;">ERROR: org.apache.hadoop.hbase.PleaseHoldException: org.apache.hadoop.hbase.PleaseHoldException: Master is initializing  </li><li class="alt" style="list-style:decimal;">  </li><li style="list-style:decimal;">Here is some help for this command:  </li><li class="alt" style="list-style:decimal;">Create table; pass table name, a dictionary of specifications per  </li><li style="list-style:decimal;">column family, and optionally a dictionary of table configuration.  </li><li class="alt" style="list-style:decimal;">Dictionaries are described below in the GENERAL NOTES section.  </li><li style="list-style:decimal;">Examples:  </li><li class="alt" style="list-style:decimal;">  </li><li style="list-style:decimal;">  hbase&gt; create 't1', {NAME =&gt; 'f1', VERSIONS =&gt; 5}  </li><li class="alt" style="list-style:decimal;">  hbase&gt; create 't1', {NAME =&gt; 'f1'}, {NAME =&gt; 'f2'}, {NAME =&gt; 'f3'}  </li><li style="list-style:decimal;">  hbase&gt; # The above in shorthand would be the following:  </li><li class="alt" style="list-style:decimal;">  hbase&gt; create 't1', 'f1', 'f2', 'f3'  </li><li style="list-style:decimal;">  hbase&gt; create 't1', {NAME =&gt; 'f1', VERSIONS =&gt; 1, TTL =&gt; 2592000, BLOCKCACHE =&gt; true}  </li><li class="alt" style="list-style:decimal;">  hbase&gt; create 't1', 'f1', {SPLITS =&gt; ['10', '20', '30', '40']}  </li><li style="list-style:decimal;">  hbase&gt; create 't1', 'f1', {SPLITS_FILE =&gt; 'splits.txt'}  </li><li class="alt" style="list-style:decimal;">  hbase&gt; # Optionally pre-split the table into NUMREGIONS, using  </li><li style="list-style:decimal;">  hbase&gt; # SPLITALGO ("HexStringSplit", "UniformSplit" or classname)  </li><li class="alt" style="list-style:decimal;">  hbase&gt; create 't1', 'f1', {NUMREGIONS =&gt; 15, SPLITALGO =&gt; 'HexStringSplit'}  </li></ol></div>
<p style="font-size:18px;">
执行stop-hbase.sh 处于无限等待状态</p>
<p style="font-size:18px;">
</p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools"><strong>[plain]</strong> view plaincopyprint?</div>
</div>
<ol><li class="alt" style="list-style:decimal;">mango@master:~/hadoop-1.0.4/hbase-0.94.2/bin$ ./st  </li><li style="list-style:decimal;">start-hbase.sh  stop-hbase.sh     </li><li class="alt" style="list-style:decimal;">mango@master:~/hadoop-1.0.4/hbase-0.94.2/bin$ ./stop-hbase.sh   </li><li style="list-style:decimal;">stopping hbase..............................................  </li></ol></div>
<p style="font-size:18px;">
查看日志</p>
<p style="font-size:18px;">
</p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools"><strong>[plain]</strong> view plaincopyprint?</div>
</div>
<ol><li class="alt" style="list-style:decimal;">2013-01-15 15:37:45,377 INFO org.apache.hadoop.ipc.HBaseServer: Starting IPC Server listener on 60000  </li><li style="list-style:decimal;">2013-01-15 15:37:45,420 INFO org.apache.hadoop.hbase.ipc.HBaseRpcMetrics: Initializing RPC Metrics with hostName=HMaster, port=60000  </li><li class="alt" style="list-style:decimal;">2013-01-15 15:37:46,074 INFO org.apache.zookeeper.ZooKeeper: Client environment:zookeeper.version=3.4.3-1240972, built on 02/06/2012 10:48 GMT  </li><li style="list-style:decimal;">2013-01-15 15:37:46,075 INFO org.apache.zookeeper.ZooKeeper: Client environment:host.name=master  </li><li class="alt" style="list-style:decimal;">2013-01-15 15:37:46,075 INFO org.apache.zookeeper.ZooKeeper: Client environment:java.version=1.6.0_13  </li><li style="list-style:decimal;">2013-01-15 15:37:46,075 INFO org.apache.zookeeper.ZooKeeper: Client environment:java.vendor=Sun Microsystems Inc.  </li><li class="alt" style="list-style:decimal;">2013-01-15 15:37:46,075 INFO org.apache.zookeeper.ZooKeeper: Client environment:java.home=/home/mango/jdk1.6.0_13/jre  </li></ol></div>
<br><p style="font-size:18px;">
<br></p>
<p style="font-size:18px;">
查看 master:60010 发现hbase.rootdir 没有使用HDFS，典型的配置错误啊~~</p>
<p style="font-size:18px;">
</p>
<h2 style="font-size:14px;color:rgb(72,61,139);font-family:Simsun;">
Attributes</h2>
<table id="attributes_table" style="border-collapse:collapse;border-spacing:0px;border:thin solid rgb(30,144,255);font-family:Simsun;font-size:14px;"><tbody><tr style="border:thin solid rgb(30,144,255);"><th style="font-weight:normal;border:thin solid rgb(30,144,255);">
Attribute Name</th>
<th style="font-weight:normal;border:thin solid rgb(30,144,255);">
Value</th>
<th style="font-weight:normal;border:thin solid rgb(30,144,255);">
Description</th>
</tr><tr style="border:thin solid rgb(30,144,255);"><td style="border:thin solid rgb(30,144,255);">HBase Version</td>
<td style="border:thin solid rgb(30,144,255);">0.94.2, r1395367</td>
<td style="border:thin solid rgb(30,144,255);">HBase version and revision</td>
</tr><tr style="border:thin solid rgb(30,144,255);"><td style="border:thin solid rgb(30,144,255);">HBase Compiled</td>
<td style="border:thin solid rgb(30,144,255);">Sun Oct 7 19:11:01 UTC 2012, jenkins</td>
<td style="border:thin solid rgb(30,144,255);">When HBase version was compiled and by whom</td>
</tr><tr style="border:thin solid rgb(30,144,255);"><td style="border:thin solid rgb(30,144,255);">Hadoop Version</td>
<td style="border:thin solid rgb(30,144,255);">1.0.4, r1393290</td>
<td style="border:thin solid rgb(30,144,255);">Hadoop version and revision</td>
</tr><tr style="border:thin solid rgb(30,144,255);"><td style="border:thin solid rgb(30,144,255);">Hadoop Compiled</td>
<td style="border:thin solid rgb(30,144,255);">Wed Oct 3 05:13:58 UTC 2012, hortonfo</td>
<td style="border:thin solid rgb(30,144,255);">When Hadoop version was compiled and by whom</td>
</tr><tr style="border:thin solid rgb(30,144,255);"><td style="border:thin solid rgb(30,144,255);">HBase Root Directory</td>
<td style="border:thin solid rgb(30,144,255);">file:/tmp/hbase-mango/hbase</td>
<td style="border:thin solid rgb(30,144,255);">Location of HBase home directory</td>
</tr><tr style="border:thin solid rgb(30,144,255);"><td style="border:thin solid rgb(30,144,255);">HBase Cluster ID</td>
<td style="border:thin solid rgb(30,144,255);">2a63a79a-3ec9-48d1-b840-d7635c6fb304</td>
<td style="border:thin solid rgb(30,144,255);">Unique identifier generated for each HBase cluster</td>
</tr><tr style="border:thin solid rgb(30,144,255);"><td style="border:thin solid rgb(30,144,255);">Load average</td>
<td style="border:thin solid rgb(30,144,255);">2.67</td>
<td style="border:thin solid rgb(30,144,255);">Average number of regions per regionserver. Naive computation.</td>
</tr><tr style="border:thin solid rgb(30,144,255);"><td style="border:thin solid rgb(30,144,255);">Zookeeper Quorum</td>
<td style="border:thin solid rgb(30,144,255);">slave1:2181,master:2181,slave2:2181</td>
<td style="border:thin solid rgb(30,144,255);">Addresses of all registered ZK servers. For more, see zk dump.</td>
</tr><tr style="border:thin solid rgb(30,144,255);"><td style="border:thin solid rgb(30,144,255);">Coprocessors</td>
<td style="border:thin solid rgb(30,144,255);">[]</td>
<td style="border:thin solid rgb(30,144,255);">Coprocessors currently loaded loaded by the master</td>
</tr><tr style="border:thin solid rgb(30,144,255);"><td style="border:thin solid rgb(30,144,255);">HMaster Start Time</td>
<td style="border:thin solid rgb(30,144,255);">Tue Jan 15 16:17:44 CST 2013</td>
<td style="border:thin solid rgb(30,144,255);">Date stamp of when this HMaster was started</td>
</tr><tr style="border:thin solid rgb(30,144,255);"><td style="border:thin solid rgb(30,144,255);">HMaster Active Time</td>
<td style="border:thin solid rgb(30,144,255);">Tue Jan 15 16:17:44 CST 2013</td>
<td style="border:thin solid rgb(30,144,255);">Date stamp of when this HMaster became active</td>
</tr></tbody></table><p style="font-size:18px;">
</p>
<p style="font-size:18px;"><br></p>
<p style="font-size:18px;">hbase.rootdir 参数写成了 hbase.root.dir</p>
<p style="font-size:18px;">
<br></p>
<p style="font-size:18px;">
hbase-site.xml如下：</p>
<p style="font-size:18px;">
</p>
<div class="dp-highlighter bg_html">
<div class="bar">
<div class="tools"><strong>[html]</strong> view plaincopyprint?</div>
</div>
<ol class="dp-xml"><li class="alt" style="list-style:decimal;"><span class="tag">&lt;</span><span class="tag-name">configuration</span><span class="tag">&gt;</span>  </li><li style="list-style:decimal;">        <span class="tag">&lt;</span><span class="tag-name">property</span><span class="tag">&gt;</span>  </li><li class="alt" style="list-style:decimal;">                <span class="tag">&lt;</span><span class="tag-name">name</span><span class="tag">&gt;</span><span class="tag">&lt;</span><span class="tag-name">span</span> <span class="attribute">style</span>=<span class="attribute-value">"color: rgb(255, 0, 0);"</span><span class="tag">&gt;</span>hbase.root.dir<span class="tag">&lt;/</span><span class="tag-name">span</span><span class="tag">&gt;</span><span class="tag">&lt;/</span><span class="tag-name">name</span><span class="tag">&gt;</span>  </li><li style="list-style:decimal;">                <span class="tag">&lt;</span><span class="tag-name">value</span><span class="tag">&gt;</span>hdfs://master:9000/hbase<span class="tag">&lt;/</span><span class="tag-name">value</span><span class="tag">&gt;</span>  </li><li class="alt" style="list-style:decimal;">        <span class="tag">&lt;/</span><span class="tag-name">property</span><span class="tag">&gt;</span>  </li><li style="list-style:decimal;">        <span class="tag">&lt;</span><span class="tag-name">property</span><span class="tag">&gt;</span>  </li><li class="alt" style="list-style:decimal;">                <span class="tag">&lt;</span><span class="tag-name">name</span><span class="tag">&gt;</span>hbase.cluster.distributed<span class="tag">&lt;/</span><span class="tag-name">name</span><span class="tag">&gt;</span>  </li><li style="list-style:decimal;">                <span class="tag">&lt;</span><span class="tag-name">value</span><span class="tag">&gt;</span>true<span class="tag">&lt;/</span><span class="tag-name">value</span><span class="tag">&gt;</span>  </li><li class="alt" style="list-style:decimal;">        <span class="tag">&lt;/</span><span class="tag-name">property</span><span class="tag">&gt;</span>  </li><li style="list-style:decimal;">        <span class="tag">&lt;</span><span class="tag-name">property</span><span class="tag">&gt;</span>  </li><li class="alt" style="list-style:decimal;">                <span class="tag">&lt;</span><span class="tag-name">name</span><span class="tag">&gt;</span>hbase.master<span class="tag">&lt;/</span><span class="tag-name">name</span><span class="tag">&gt;</span>  </li><li style="list-style:decimal;">                <span class="tag">&lt;</span><span class="tag-name">value</span><span class="tag">&gt;</span>hdfs://master:<span class="tag">&lt;</span><span class="tag-name">span</span> <span class="attribute">style</span>=<span class="attribute-value">"color: rgb(255, 0, 0);"</span><span class="tag">&gt;</span>6000<span class="tag">&lt;/</span><span class="tag-name">span</span><span class="tag">&gt;</span><span class="tag">&lt;/</span><span class="tag-name">value</span><span class="tag">&gt;</span>  </li><li class="alt" style="list-style:decimal;">        <span class="tag">&lt;/</span><span class="tag-name">property</span><span class="tag">&gt;</span>  </li><li style="list-style:decimal;">        <span class="tag">&lt;</span><span class="tag-name">property</span><span class="tag">&gt;</span>  </li><li class="alt" style="list-style:decimal;">                <span class="tag">&lt;</span><span class="tag-name">name</span><span class="tag">&gt;</span>hbase.zookeeper.quorum<span class="tag">&lt;/</span><span class="tag-name">name</span><span class="tag">&gt;</span>  </li><li style="list-style:decimal;">                <span class="tag">&lt;</span><span class="tag-name">value</span><span class="tag">&gt;</span>master,slave1,slave2<span class="tag">&lt;/</span><span class="tag-name">value</span><span class="tag">&gt;</span>  </li><li class="alt" style="list-style:decimal;">        <span class="tag">&lt;/</span><span class="tag-name">property</span><span class="tag">&gt;</span>  </li><li style="list-style:decimal;">  </li><li class="alt" style="list-style:decimal;"><span class="tag">&lt;/</span><span class="tag-name">configuration</span><span class="tag">&gt;</span>  </li></ol></div>
<br>
修改hbase-site.xml后，重启集群，一切恢复正常。</div>
            </div>
                </div>