---
layout:     post
title:      High Availability for the Hadoop Distributed File System (HDFS)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2 style="font-size:14pt;line-height:17px;">
<span style="background-color:rgb(255,255,255);">Background</span></h2>
<p style="color:rgb(80,80,80);font-size:14px;line-height:20px;">
<span style="background-color:rgb(255,255,255);">Apache Hadoop consists of two primary components: HDFS and MapReduce. HDFS, the Hadoop Distributed File System, is the primary storage system of Hadoop, and is responsible for storing and serving all data stored
 in Hadoop. MapReduce is a distributed processing framework designed to operate on data stored in HDFS.</span></p>
<p style="color:rgb(80,80,80);font-size:14px;line-height:20px;">
<span style="background-color:rgb(255,255,255);">HDFS has long been considered a highly <em>reliable</em> file system.  An empirical <a href="http://www.youtube.com/watch?v=zbycDpVWhp0" rel="nofollow" style="text-decoration:none;color:rgb(1,126,197);">study done at Yahoo!</a> concluded
 that across Yahoo!’s 20,000 nodes running Apache Hadoop in 10 different clusters in 2009, HDFS lost only 650 blocks out of 329 million total blocks. The vast majority of these lost blocks were due to a handful of bugs which have long since been fixed.</span></p>
<p style="color:rgb(80,80,80);font-size:14px;line-height:20px;">
<span style="background-color:rgb(255,255,255);">Despite this very high level of reliability, HDFS has always had a well-known single point of failure which impacts HDFS’s <em>availability</em>: the system relies on a single Name Node to coordinate access to
 the file system data. In clusters which are used exclusively for ETL or batch-processing workflows, a brief HDFS outage may not have immediate business impact on an organization; however, in the past few years we have seen HDFS begin to be used for more interactive
 workloads or, in the case of HBase, used to directly serve customer requests in real time. In cases such as this, an HDFS outage will immediately impact the productivity of internal users, and perhaps result in downtime visible to external users. For these
 reasons, adding high availability (HA) to the HDFS Name Node became one of the top priorities for the HDFS community.</span></p>
<p style="color:rgb(80,80,80);font-size:14px;line-height:20px;">
<span style="background-color:rgb(255,255,255);">The remainder of this post discusses the implementation of a new feature for HDFS, called the “HA Name Node.” For a detailed discussion of other issues surrounding the availability of Hadoop as a whole, take a
 look at this <a href="http://www.cloudera.com/blog/2011/02/hadoop-availability/" rel="nofollow" style="text-decoration:none;color:rgb(1,126,197);">excellent blog post</a> by my colleague Eli Collins.<span style="font-style:inherit;color:rgb(47,43,44);font-size:13px;"><span style="font-style:inherit;"></span></span></span></p>
<h2 style="font-size:14pt;line-height:17px;">
<span style="background-color:rgb(255,255,255);">High-level Architecture</span></h2>
<p style="color:rgb(80,80,80);font-size:14px;line-height:20px;">
<span style="background-color:rgb(255,255,255);">The goal of the HA Name Node project is to add support for deploying two Name Nodes in an active/passive configuration. This is a common configuration for highly-available distributed systems, and HDFS’s architecture
 lends itself well to this design. Even in a non-HA configuration, HDFS already requires both a Name Node and another node with similar hardware specs which performs checkpointing operations for the Name Node. The design of the HA Name Node is such that the
 passive Name Node is capable of performing this checkpointing role, thus requiring no additional Hadoop server machines beyond what HDFS already requires.<img src="http://www.cloudera.com/wp-content/uploads/2012/03/HANNdiagram-2.png" alt="Hadoop Distributed File System High Available Name Node" style="border:0px none;"></span></p>
<p style="color:rgb(80,80,80);font-size:14px;line-height:20px;">
<span style="background-color:rgb(255,255,255);">The HDFS Name Node is primarily responsible for serving two types of file system metadata: file system namespace information and block locations. Because of the architecture of HDFS, these must be handled separately.<span style="font-style:inherit;color:rgb(47,43,44);font-size:13px;"><span style="font-style:inherit;"></span></span></span></p>
<p style="color:rgb(80,80,80);font-size:14px;line-height:20px;">
<span style="font-style:inherit;color:rgb(47,43,44);font-size:13px;background-color:rgb(255,255,255);">Namespace Information</span></p>
<p style="color:rgb(80,80,80);font-size:14px;line-height:20px;">
<span style="background-color:rgb(255,255,255);">All mutations to the file system namespace, such as file renames, permission changes, file creations, block allocations, etc, are written to a persistent write-ahead log by the Name Node before returning success
 to a client call. In addition to this edit log, periodic checkpoints of the file system, called the fsimage, are also created and stored on-disk on the Name Node. Block locations, on the other hand, are stored only in memory. The locations of all blocks are
 received via “block reports” sent from the Data Nodes when the Name Node is started.</span></p>
<p style="color:rgb(80,80,80);font-size:14px;line-height:20px;">
<span style="background-color:rgb(255,255,255);">The goal of the HA Name Node is to provide a <em>hot standby</em> Name Node that can take over serving the role of the active Name Node with no downtime. To provide this capability, it is critical that the standby
 Name Node has the most complete and up-to-date file system state possible in memory. Empirically, starting a Name Node from cold state can take tens of minutes to load the namespace information (fsimage and edit log) from disk, and up to an hour to receive
 the necessary block reports from all Data Nodes in a large cluster.</span></p>
<p style="color:rgb(80,80,80);font-size:14px;line-height:20px;">
<span style="background-color:rgb(255,255,255);">The Name Node has long supported the ability to write its edit logs to multiple, redundant local directories. To address the issue of sharing state between the active and standby Name Nodes, the HA Name Node feature
 allows for the configuration of a special shared edits directory. This directory should be available via a network file system, and should be read/write accessible from both Name Nodes. This directory is treated as being <em>required</em> by the active Name
 Node, meaning that success will not be returned to a client call unless the file system change has been written to the edit log in this directory. The standby Name Node polls the shared edits directory frequently, looking for new edits written by the active
 Name Node, and reads these edits into its own in-memory view of the file system state.</span></p>
<p style="color:rgb(80,80,80);font-size:14px;line-height:20px;">
<span style="background-color:rgb(255,255,255);">Note that requiring a single shared edits directory does not necessarily imply a new single point of failure. It does, however, mean that the filer providing this shared directory must itself be HA, and that multiple
 network routes should be configured between the Name Nodes and the service providing this shared directory. Plans to improve this situation are discussed further below.</span></p>
<p style="color:rgb(80,80,80);font-size:14px;line-height:20px;">
<span style="font-style:inherit;color:rgb(47,43,44);font-size:13px;background-color:rgb(255,255,255);">Block Locations</span></p>
<p style="color:rgb(80,80,80);font-size:14px;line-height:20px;">
<span style="background-color:rgb(255,255,255);">The other part of keeping the standby Name Node hot is making sure that it has up-to-date block location information. Since block locations aren’t written to the Name Node edit log, reading from the shared edits
 directory is not sufficient to share this file system metadata between the two Name Nodes. To address this issue, when HA is enabled, all Data Nodes in the cluster are configured with the network addresses of both Name Nodes. Data Nodes send all block reports,
 block location updates, and heartbeats to both Name Nodes, but Data Nodes will only act on block commands issued by the currently-active Name Node.</span></p>
<p style="color:rgb(80,80,80);font-size:14px;line-height:20px;">
<span style="background-color:rgb(255,255,255);">With both up-to-date namespace information and block locations in the standby Name Node, the system is able to perform a failover from the active Name Node to the standby with no delay.</span></p>
<p style="color:rgb(80,80,80);font-size:14px;line-height:20px;">
<span style="font-style:inherit;color:rgb(47,43,44);font-size:13px;background-color:rgb(255,255,255);">Client Failover</span></p>
<p style="color:rgb(80,80,80);font-size:14px;line-height:20px;">
<span style="background-color:rgb(255,255,255);">Since multiple distinct daemons are now capable of serving as the active Name Node for a single cluster, the HDFS client must be able to determine which Name Node to communicate with at any given time. The HA
 Name Node feature does not support an active-active configuration, and thus all client calls must go to the active Name Node in order to be served.</span></p>
<p style="color:rgb(80,80,80);font-size:14px;line-height:20px;">
<span style="background-color:rgb(255,255,255);">To implement this feature, the HDFS client was extended to support the configuration of multiple network addresses, one for each Name Node, which collectively represent the HA name service. The name service is
 identified by a single <em>logical URI</em>, which is mapped to the two network addresses of the HA Name Nodes via client-side configuration. These addresses are tried in order by the HDFS client. If a client makes a call to the standby Name Node, a special
 result is returned to the client, indicating that it should retry elsewhere. The configured addresses are tried in order by the client until an active Name Node is found.</span></p>
<p style="color:rgb(80,80,80);font-size:14px;line-height:20px;">
<span style="background-color:rgb(255,255,255);">In the event that the active Name Node crashes while in the middle of processing a request, the client will be unable to determine whether or not the request was processed. For many operations such as reads (or<a href="http://en.wikipedia.org/wiki/Idempotent" rel="nofollow" style="text-decoration:none;color:rgb(1,126,197);">idempotent</a> writes
 such as setting permissions, setting modification time, etc), this is not a problem — the client may simply retry after the failover has completed. For others, the error must be bubbled up to the caller to be correctly handled. In the course of the HA project,
 we extended the Hadoop IPC system to be able to classify each operation’s idempotence using special annotations.</span></p>
<h2 style="font-size:14pt;line-height:17px;">
<span style="background-color:rgb(255,255,255);">Current Status</span></h2>
<p style="color:rgb(80,80,80);font-size:14px;line-height:20px;">
<span style="background-color:rgb(255,255,255);">Active development work began on the HA Name Node in August 2011, in a branch off of Apache Hadoop trunk. Development was done under the umbrella JIRAs <a href="https://issues.apache.org/jira/browse/HDFS-1623" rel="nofollow" style="text-decoration:none;color:rgb(1,126,197);">HDFS-1623</a> and <a href="https://issues.apache.org/jira/browse/HADOOP-7454" rel="nofollow" style="text-decoration:none;color:rgb(1,126,197);">HADOOP-7454</a>.
 Last Friday, March 2nd 2012 we merged this branch back into Apache Hadoop trunk. We closed over 170 individual JIRAs in the course of implementing this feature. The stated intention of the community is to merge this work from HDFS trunk into the 0.23 branch,
 where it will be released as an update of the Apache Hadoop 0.23 release line. Much of this work is already available as part of <a href="http://www.cloudera.com/blog/2012/02/introducing-cdh4/" rel="nofollow" style="text-decoration:none;color:rgb(1,126,197);">CDH4 beta 1,
 released on February 13th, 2012.</a></span></p>
<p style="color:rgb(80,80,80);font-size:14px;line-height:20px;">
<span style="background-color:rgb(255,255,255);">Once a failover has been initiated, the actual process of stopping the active and starting the standby Name Node takes a matter of seconds or less. This speed allows for little or no detectable service disruption
 during a failover. I’ve personally run hundreds of MR jobs over a running HA cluster, doing failovers back and forth between two HA Name Nodes, without any job failures.</span></p>
<p style="color:rgb(80,80,80);font-size:14px;line-height:20px;">
<span style="background-color:rgb(255,255,255);">This first implementation of the HA Name Node supports only manual failover — that is, failure of one of the Name Nodes is not automatically detected by the system, but rather requires intervention by an operator
 to initiate a failover between the Name Nodes. Though this is an obvious limitation, this version should still be useful to eliminate the need for planned HDFS downtime in many cases, e.g. changing the configuration of the Name Node, scheduled hardware maintenance
 of a Name Node, or scheduled OS upgrade of a Name Node.</span></p>
<h2 style="font-size:14pt;line-height:17px;">
<span style="background-color:rgb(255,255,255);">Next Up</span></h2>
<p style="color:rgb(80,80,80);font-size:14px;line-height:20px;">
<span style="background-color:rgb(255,255,255);">The highest priority feature to add to the HA Name Node implementation is support for automatically detecting the failure of the Active Name Node and initiating a failover to the Standby when it is determined
 that the Active is no longer functional. <a href="https://issues.apache.org/jira/browse/HDFS-3042" rel="nofollow" style="text-decoration:none;color:rgb(1,126,197);">HDFS-3042</a> and its sub-tasks are actively being worked on to provide this functionality.</span></p>
<p style="color:rgb(80,80,80);font-size:14px;line-height:20px;">
<span style="background-color:rgb(255,255,255);">The dependence on an HA filer for HDFS edit logs is a limitation that we’d like to address in the near to medium term as well. Several different options have been discussed to address this:<span style="font-style:inherit;color:rgb(47,43,44);font-size:13px;"><br></span></span></p>
<ul style="color:rgb(80,80,80);font-size:14px;line-height:20px;"><li style="list-style:disc;"><span style="background-color:rgb(255,255,255);"><span style="font-style:inherit;color:rgb(47,43,44);">BookKeeper</span> – <a href="http://zookeeper.apache.org/doc/r3.2.2/bookkeeperStarted.html" rel="nofollow" style="text-decoration:none;color:rgb(1,126,197);">BookKeeper</a> is
 a highly available write-ahead logging system. Work has already been done to allow the HDFS Name Node to be able to write its edits log to BookKeeper, though this has not yet been tested with the HA Name Node.</span></li><li style="list-style:disc;"><span style="background-color:rgb(255,255,255);"><span style="font-style:inherit;color:rgb(47,43,44);">Multiple, non-HA filers</span> – the HA Name Node presently only supports logging to a single
 shared edits directory. Perhaps the easiest improvement from the current situation would be to allow the Name Node to log to several shared edits directories, and require that all edits be logged to a quorum of shared edits directories. This proposal is being
 tracked by <a href="https://issues.apache.org/jira/browse/HDFS-2782" rel="nofollow" style="text-decoration:none;color:rgb(1,126,197);">HDFS-2782</a>.</span></li><li style="list-style:disc;"><span style="background-color:rgb(255,255,255);"><span style="font-style:inherit;color:rgb(47,43,44);">Stream edits to remote NNs</span> – in addition to writing edits to a local file system,
 edit log entries could be sent directly to other Name Nodes over the network. The active Name Node would require a quorum of the involved Name Nodes to acknowledge receipt of the edits before responding with success to the client call.</span></li><li style="list-style:disc;"><span style="background-color:rgb(255,255,255);"><span style="font-style:inherit;color:rgb(47,43,44);">Store edit logs in HDFS itself</span> – systems such as HBase already use HDFS to store a
 write-ahead log of all data mutations. If HDFS were extended to have a modicum of bootstrapping information, it is not inconceivable that HDFS edit logs could be stored in HDFS itself. This proposal is being discussed on<a href="https://issues.apache.org/jira/browse/HDFS-2601" rel="nofollow" style="text-decoration:none;color:rgb(1,126,197);">HDFS-2601</a>.</span></li></ul><p style="color:rgb(80,80,80);font-size:14px;line-height:20px;">
<span style="background-color:rgb(255,255,255);">In the next few weeks, we will be evaluating all of these options and selecting one to implement.<br><span id="internal-source-marker_0.7045455041807145" style="font-style:inherit;color:rgb(47,43,44);font-size:13px;"><br></span>Currently, deploying HA Name Nodes is somewhat cumbersome, requiring the operator to <a href="https://ccp.cloudera.com/display/CDH4B1/HDFS+High+Availability+Initial+Deployment" rel="nofollow" style="text-decoration:none;color:rgb(1,126,197);">manually synchronize the
 on-disk metadata</a> of the two Name Nodes. <a href="https://issues.apache.org/jira/browse/HDFS-2731" rel="nofollow" style="text-decoration:none;color:rgb(1,126,197);">HDFS-2731</a> aims to improve the user experience of this deployment process by having the second Name
 Node automatically synchronize itself with the state of the first Name Node. This feature will make the process faster and less error prone.</span></p>
<h2 style="font-size:14pt;line-height:17px;">
<span style="background-color:rgb(255,255,255);">Further Reading</span></h2>
<p style="color:rgb(80,80,80);font-size:14px;line-height:20px;">
<span style="background-color:rgb(255,255,255);">Take a look at the <a href="https://ccp.cloudera.com/display/CDH4B1/CDH4+Beta+1+High+Availability+Guide" rel="nofollow" style="text-decoration:none;color:rgb(1,126,197);">CDH4 docs</a> for detailed information on configuring
 the HA Name Node in CDH4.</span></p>
<p style="color:rgb(80,80,80);font-size:14px;line-height:20px;">
<span style="background-color:rgb(255,255,255);">Be on the lookout for an upcoming blog post from my colleague Todd Lipcon, which will go into greater detail about some of the specific challenges encountered while implementing the HA Name Node feature, and how
 these issues were overcome.</span></p>
<h2 style="font-size:14pt;line-height:17px;">
<span style="background-color:rgb(255,255,255);">Acknowledgments</span></h2>
<p style="color:rgb(80,80,80);font-size:14px;line-height:20px;">
<span style="background-color:rgb(255,255,255);">This work has been a community effort from the start, and represents the work of many contributors. Both the architecture and implementation were the collaborative effort of many. In particular, this work would
 not have been possible without contributions from Todd Lipcon, Eli Collins, Uma Maheswara Rao G, Bikas Saha, Suresh Srinivas, Jitendra Nath Pandey, Hari Mankude, Brandon Li, Sanjay Radia, Mingjie Lai, and Gregory Chanan. Also thanks to Dhruba Borthakur and
 Konstantin Shvachko for helpful design discussions and recommendations on testing. Thanks also to Stephen Chu, Wing Yew Poon, and Patrick Ramsey for their help in testing the HA Name Node.</span></p>
<p style="color:rgb(80,80,80);font-size:14px;line-height:20px;">
Ref: <a href="http://www.cloudera.com/blog/2012/03/high-availability-for-the-hadoop-distributed-file-system-hdfs/" rel="nofollow">http://www.cloudera.com/blog/2012/03/high-availability-for-the-hadoop-distributed-file-system-hdfs/</a></p>
            </div>
                </div>