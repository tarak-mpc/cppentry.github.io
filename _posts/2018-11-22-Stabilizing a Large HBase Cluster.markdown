---
layout:     post
title:      Stabilizing a Large HBase Cluster
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="font-family:arial, sans-serif;font-size:13.333333015441895px;">Running a large HBase cluster smoothly with minimum downtime is a skill which requires a deep understanding of how HBase works. When a disaster strikes, you find yourself digging into HBase
 code and/or mailing lists to understand what went wrong, determine how to recover from the current mess and most importantly figure out what can be done to prevent the same thing from happening again.  Apart from the inconvenience downtime, a service crash
 can also lead to inconsistencies in HBase meta tables.</p>
<p style="font-family:arial, sans-serif;font-size:13.333333015441895px;">While some crashes are environment specific, especially in large clusters with heavy load, there are few knobs that can be tuned to help minimize the frequency of failures. This article
 discusses some of the common scenarios that could result in service crashes and inconsistencies, as well as possible tweaks to HBase to avoid them.</p>
<p style="font-family:arial, sans-serif;font-size:13.333333015441895px;"><strong>Frequent unassigned regions</strong><br>
When an HBase cluster starts, the active Master begins region assignment for both meta and user tables present in the cluster. Since the ‘.META.’ table contains the assignment information of every region of each user table in the cluster, it must be assigned
 and brought online before any other user table.</p>
<p style="font-family:arial, sans-serif;font-size:13.333333015441895px;">In HBase versions prior to 0.92.0, especially in a large size cluster with thousands of regions, it is possible that region assignments for user table begin before the assignment of ‘.META.’
 has completed. All such assignments are bound to fail with ‘NotAllMetaRegionsOnlineException’ and they remain offline for 30 minutes (default) before the master takes notice of such regions and attempts reassignment.</p>
<p style="font-family:arial, sans-serif;font-size:13.333333015441895px;">Unfortunately, no full-proof solution is available to prevent this from happening in HBase versions prior to 0.92.0. In version 0.92.0 onwards, the HBase Master ensures that both ‘-ROOT-’
 and ‘.META.’ tables are assigned before it attempts to assign any user table region. So if you are running HBase version 0.90.6 or earlier and have been seeing assignment related issues frequently, it may be time to upgrade.</p>
<p style="font-family:arial, sans-serif;font-size:13.333333015441895px;"><strong>The problem of a runaway Region Server</strong><br>
A runaway Region Server can happen in any cluster running with a default configuration, but it becomes a real problem in a large cluster.</p>
<p style="font-family:arial, sans-serif;font-size:13.333333015441895px;">On startup, the HBase Master waits for ‘n’ number of Region Servers to register with it during ‘t’ period of time before creating a region assignment plan. So even if your cluster has a
 large number of region servers, the initial region assignments will be distributed among these registered servers alone. The default value of ‘n’ and ‘t’ is 1 and 4.5 seconds, respectively. So if by chance if one Region Server starts fast enough to beat other
 servers by 4.5 seconds, all the regions will be assigned to it alone. You can read more about it in <a href="https://issues.apache.org/jira/browse/HBASE-6389" rel="nofollow" style="color:rgb(17,85,204);">HBASE-6389</a> and <a href="https://issues.apache.org/jira/browse/HBASE-6375" rel="nofollow" style="color:rgb(17,85,204);">HBASE-6375</a>.</p>
<p style="font-family:arial, sans-serif;font-size:13.333333015441895px;">The effect of such bulk assignment to a single server could be disastrous if you have tens of thousands of regions in the zone with a Region Server crashing with an ‘Out of Memory’ error
 during this assignment phase leading to inconsistency in the ‘.META.’ table. This would be more pronounced in the newer releases (0.92.0 onwards) as <a href="http://hbase.apache.org/book/upgrade0.92.html#d1967e3030" rel="nofollow" style="color:rgb(17,85,204);">MSLAB
 is enabled by default</a>, which places additional memory (~2MB) requirement per region.</p>
<p style="font-family:arial, sans-serif;font-size:13.333333015441895px;">Fortunately, this can be easily avoided with a simple configuration. The value of ‘n’ is controlled by the parameter<code style="font-size:10.833333015441895px;">'hbase.master.wait.on.regionservers.mintostart'</code> which
 needs to be set on each node running HBase Master in <code style="font-size:10.833333015441895px;">'hbase-site.xml'</code>. Set this to a value close to the number of region servers sufficient to handle the startup assignment load.</p>
<p style="font-family:arial, sans-serif;font-size:13.333333015441895px;"><strong>Long Java garbage collection pause</strong><br>
Like any other Java server process running with gigabytes of memory, HBase Region Servers are prone to long garbage collection pauses especially when under heavy write load. This can cause increased latency or timeouts for client requests. A more severe effect
 of this is that during this pause, the Region Server stops sending heartbeat signals to ZooKeeper which, after a <a href="http://hbase.apache.org/book.html#zookeeper.session.timeout" rel="nofollow" style="color:rgb(17,85,204);">certain period of time</a>,
 makes the Master believe that the Region Server has crashed and then initiates the recovery process which involves reassignment of the region currently assigned to the affected Region Server.</p>
<p style="font-family:arial, sans-serif;font-size:13.333333015441895px;">This can be largely avoided by enabling <a href="http://hbase.apache.org/book.html#hbase.hregion.memstore.mslab.enabled" rel="nofollow" style="color:rgb(17,85,204);">MemStore Local Allocation
 Buffers</a> (MSLAB). This is already enabled by default in version 0.92.0 onward. However if you start getting the ‘Out of Memory’ error frequently, you may want to bring down the total number of regions in the cluster to a more appropriate number.</p>
<p style="font-family:arial, sans-serif;font-size:13.333333015441895px;">Learning to minimize frequent unassigned regions, runaway Region Servers and long Java garbage collection pauses could reduce downtime significantly and add stability to your large HBase
 cluster.<br><br></p>
<p style="font-family:arial, sans-serif;font-size:13.333333015441895px;">Ref: <a href="http://www.mapr.com/blog/stabilizing-a-large-hbase-cluster" rel="nofollow">http://www.mapr.com/blog/stabilizing-a-large-hbase-cluster</a></p>
            </div>
                </div>