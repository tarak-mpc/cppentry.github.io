---
layout:     post
title:      HDFS High Availability
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 style="font-family:Verdana, Helvetica, Arial, sans-serif;"></h1>
<h1 style="font-family:Verdana, Helvetica, Arial, sans-serif;">HDFS High Availability</h1>
<ul style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;"><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HDFSHighAvailabilityWithNFS.html#HDFS_High_Availability" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">HDFS
 High Availability</a>
<ul><li><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HDFSHighAvailabilityWithNFS.html#Purpose" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Purpose</a></li><li><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HDFSHighAvailabilityWithNFS.html#Note:_Using_the_Quorum_Journal_Manager_or_Conventional_Shared_Storage" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Note:
 Using the Quorum Journal Manager or Conventional Shared Storage</a></li><li><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HDFSHighAvailabilityWithNFS.html#Background" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Background</a></li><li><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HDFSHighAvailabilityWithNFS.html#Architecture" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Architecture</a></li><li><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HDFSHighAvailabilityWithNFS.html#Hardware_resources" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Hardware resources</a></li><li><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HDFSHighAvailabilityWithNFS.html#Deployment" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Deployment</a>
<ul><li><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HDFSHighAvailabilityWithNFS.html#Configuration_overview" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Configuration overview</a></li><li><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HDFSHighAvailabilityWithNFS.html#Configuration_details" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Configuration details</a></li><li><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HDFSHighAvailabilityWithNFS.html#Deployment_details" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Deployment details</a></li><li><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HDFSHighAvailabilityWithNFS.html#Administrative_commands" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Administrative commands</a></li></ul></li><li><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HDFSHighAvailabilityWithNFS.html#Automatic_Failover" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Automatic Failover</a>
<ul><li><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HDFSHighAvailabilityWithNFS.html#Introduction" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Introduction</a></li><li><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HDFSHighAvailabilityWithNFS.html#Components" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Components</a></li><li><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HDFSHighAvailabilityWithNFS.html#Deploying_ZooKeeper" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Deploying ZooKeeper</a></li><li><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HDFSHighAvailabilityWithNFS.html#Before_you_begin" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Before you begin</a></li><li><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HDFSHighAvailabilityWithNFS.html#Configuring_automatic_failover" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Configuring automatic failover</a></li><li><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HDFSHighAvailabilityWithNFS.html#Initializing_HA_state_in_ZooKeeper" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Initializing HA state in ZooKeeper</a></li><li><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HDFSHighAvailabilityWithNFS.html#Starting_the_cluster_with_start-dfs.sh" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Starting the cluster with start-dfs.sh</a></li><li><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HDFSHighAvailabilityWithNFS.html#Starting_the_cluster_manually" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Starting the cluster manually</a></li><li><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HDFSHighAvailabilityWithNFS.html#Securing_access_to_ZooKeeper" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Securing access to ZooKeeper</a></li><li><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HDFSHighAvailabilityWithNFS.html#Verifying_automatic_failover" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Verifying automatic failover</a></li></ul></li><li><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HDFSHighAvailabilityWithNFS.html#Automatic_Failover_FAQ" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Automatic Failover FAQ</a></li><li><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HDFSHighAvailabilityWithNFS.html#BookKeeper_as_a_Shared_storage_EXPERIMENTAL" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">BookKeeper as a Shared storage
 (EXPERIMENTAL)</a></li></ul></li></ul><div class="section" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<h2 style="font-size:17px;color:rgb(51,51,51);"><a name="Purpose"></a>Purpose</h2>
<p style="line-height:1.3em;font-size:12px;">This guide provides an overview of the HDFS High Availability (HA) feature and how to configure and manage an HA HDFS cluster, using NFS for the shared storage required by the NameNodes.</p>
<p style="line-height:1.3em;font-size:12px;">This document assumes that the reader has a general understanding of general components and node types in an HDFS cluster. Please refer to the HDFS Architecture guide for details.</p>
</div>
<div class="section" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<h2 style="font-size:17px;color:rgb(51,51,51);"><a name="Note:_Using_the_Quorum_Journal_Manager_or_Conventional_Shared_Storage"></a>Note: Using the Quorum Journal Manager or Conventional Shared Storage</h2>
<p style="line-height:1.3em;font-size:12px;">This guide discusses how to configure and use HDFS HA using a shared NFS directory to share edit logs between the Active and Standby NameNodes. For information on how to configure HDFS HA using the Quorum Journal
 Manager instead of NFS, please see <a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HDFSHighAvailabilityWithQJM.html" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">this alternative guide.</a></p>
</div>
<div class="section" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<h2 style="font-size:17px;color:rgb(51,51,51);"><a name="Background"></a>Background</h2>
<p style="line-height:1.3em;font-size:12px;">Prior to Hadoop 2.0.0, the NameNode was a single point of failure (SPOF) in an HDFS cluster. Each cluster had a single NameNode, and if that machine or process became unavailable, the cluster as a whole would be
 unavailable until the NameNode was either restarted or brought up on a separate machine.</p>
<p style="line-height:1.3em;font-size:12px;">This impacted the total availability of the HDFS cluster in two major ways:</p>
<ul><li style="font-size:12px;color:rgb(51,51,51);">
<p style="line-height:1.3em;color:rgb(0,0,0);">In the case of an unplanned event such as a machine crash, the cluster would be unavailable until an operator restarted the NameNode.</p>
</li><li style="font-size:12px;color:rgb(51,51,51);">
<p style="line-height:1.3em;color:rgb(0,0,0);">Planned maintenance events such as software or hardware upgrades on the NameNode machine would result in windows of cluster downtime.</p>
</li></ul><p style="line-height:1.3em;font-size:12px;">The HDFS High Availability feature addresses the above problems by providing the option of running two redundant NameNodes in the same cluster in an Active/Passive configuration with a hot standby. This allows a
 fast failover to a new NameNode in the case that a machine crashes, or a graceful administrator-initiated failover for the purpose of planned maintenance.</p>
</div>
<div class="section" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<h2 style="font-size:17px;color:rgb(51,51,51);"><a name="Architecture"></a>Architecture</h2>
<p style="line-height:1.3em;font-size:12px;">In a typical HA cluster, two separate machines are configured as NameNodes. At any point in time, exactly one of the NameNodes is in an <em>Active</em> state, and the other is in a<em>Standby</em> state. The Active
 NameNode is responsible for all client operations in the cluster, while the Standby is simply acting as a slave, maintaining enough state to provide a fast failover if necessary.</p>
<p style="line-height:1.3em;font-size:12px;">In order for the Standby node to keep its state synchronized with the Active node, the current implementation requires that the two nodes both have access to a directory on a shared storage device (eg an NFS mount
 from a NAS). This restriction will likely be relaxed in future versions.</p>
<p style="line-height:1.3em;font-size:12px;">When any namespace modification is performed by the Active node, it durably logs a record of the modification to an edit log file stored in the shared directory. The Standby node is constantly watching this directory
 for edits, and as it sees the edits, it applies them to its own namespace. In the event of a failover, the Standby will ensure that it has read all of the edits from the shared storage before promoting itself to the Active state. This ensures that the namespace
 state is fully synchronized before a failover occurs.</p>
<p style="line-height:1.3em;font-size:12px;">In order to provide a fast failover, it is also necessary that the Standby node have up-to-date information regarding the location of blocks in the cluster. In order to achieve this, the DataNodes are configured
 with the location of both NameNodes, and send block location information and heartbeats to both.</p>
<p style="line-height:1.3em;font-size:12px;">It is vital for the correct operation of an HA cluster that only one of the NameNodes be Active at a time. Otherwise, the namespace state would quickly diverge between the two, risking data loss or other incorrect
 results. In order to ensure this property and prevent the so-called “split-brain scenario,” the administrator must configure at least one <em>fencing method</em> for the shared storage. During a failover, if it cannot be verified that the previous Active node
 has relinquished its Active state, the fencing process is responsible for cutting off the previous Active’s access to the shared edits storage. This prevents it from making any further edits to the namespace, allowing the new Active to safely proceed with
 failover.</p>
</div>
<div class="section" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<h2 style="font-size:17px;color:rgb(51,51,51);"><a name="Hardware_resources"></a>Hardware resources</h2>
<p style="line-height:1.3em;font-size:12px;">In order to deploy an HA cluster, you should prepare the following:</p>
<ul><li style="font-size:12px;color:rgb(51,51,51);">
<p style="line-height:1.3em;color:rgb(0,0,0);"><strong>NameNode machines</strong> - the machines on which you run the Active and Standby NameNodes should have equivalent hardware to each other, and equivalent hardware to what would be used in a non-HA cluster.</p>
</li><li style="font-size:12px;color:rgb(51,51,51);">
<p style="line-height:1.3em;color:rgb(0,0,0);"><strong>Shared storage</strong> - you will need to have a shared directory which both NameNode machines can have read/write access to. Typically this is a remote filer which supports NFS and is mounted on each
 of the NameNode machines. Currently only a single shared edits directory is supported. Thus, the availability of the system is limited by the availability of this shared edits directory, and therefore in order to remove all single points of failure there needs
 to be redundancy for the shared edits directory. Specifically, multiple network paths to the storage, and redundancy in the storage itself (disk, network, and power). Beacuse of this, it is recommended that the shared storage server be a high-quality dedicated
 NAS appliance rather than a simple Linux server.</p>
</li></ul><p style="line-height:1.3em;font-size:12px;">Note that, in an HA cluster, the Standby NameNode also performs checkpoints of the namespace state, and thus it is not necessary to run a Secondary NameNode, CheckpointNode, or BackupNode in an HA cluster. In fact,
 to do so would be an error. This also allows one who is reconfiguring a non-HA-enabled HDFS cluster to be HA-enabled to reuse the hardware which they had previously dedicated to the Secondary NameNode.</p>
</div>
<div class="section" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<h2 style="font-size:17px;color:rgb(51,51,51);"><a name="Deployment"></a>Deployment</h2>
<div class="section">
<h3 style="color:rgb(102,102,102);font-size:14px;background-color:rgb(204,204,204);">
<a name="Configuration_overview"></a>Configuration overview</h3>
<p style="line-height:1.3em;font-size:12px;">Similar to Federation configuration, HA configuration is backward compatible and allows existing single NameNode configurations to work without change. The new configuration is designed such that all the nodes in
 the cluster may have the same configuration without the need for deploying different configuration files to different machines based on the type of the node.</p>
<p style="line-height:1.3em;font-size:12px;">Like HDFS Federation, HA clusters reuse the <tt>nameservice ID</tt> to identify a single HDFS instance that may in fact consist of multiple HA NameNodes. In addition, a new abstraction called <tt>NameNode ID</tt> is
 added with HA. Each distinct NameNode in the cluster has a different NameNode ID to distinguish it. To support a single configuration file for all of the NameNodes, the relevant configuration parameters are suffixed with the <strong>nameservice ID</strong> as
 well as the <strong>NameNode ID</strong>.</p>
</div>
<div class="section">
<h3 style="color:rgb(102,102,102);font-size:14px;background-color:rgb(204,204,204);">
<a name="Configuration_details"></a>Configuration details</h3>
<p style="line-height:1.3em;font-size:12px;">To configure HA NameNodes, you must add several configuration options to your <strong>hdfs-site.xml</strong> configuration file.</p>
<p style="line-height:1.3em;font-size:12px;">The order in which you set these configurations is unimportant, but the values you choose for <strong>dfs.nameservices</strong> and <strong>dfs.ha.namenodes.[nameservice ID]</strong> will determine the keys of those
 that follow. Thus, you should decide on these values before setting the rest of the configuration options.</p>
<ul><li style="font-size:12px;color:rgb(51,51,51);">
<p style="line-height:1.3em;color:rgb(0,0,0);"><strong>dfs.nameservices</strong> - the logical name for this new nameservice</p>
<p style="line-height:1.3em;color:rgb(0,0,0);">Choose a logical name for this nameservice, for example “mycluster”, and use this logical name for the value of this config option. The name you choose is arbitrary. It will be used both for configuration and as
 the authority component of absolute HDFS paths in the cluster.</p>
<p style="line-height:1.3em;color:rgb(0,0,0);"><strong>Note:</strong> If you are also using HDFS Federation, this configuration setting should also include the list of other nameservices, HA or otherwise, as a comma-separated list.</p>
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>&lt;property&gt;
  &lt;name&gt;dfs.nameservices&lt;/name&gt;
  &lt;value&gt;mycluster&lt;/value&gt;
&lt;/property&gt;
</pre>
</div>
</div>
</li><li style="font-size:12px;color:rgb(51,51,51);">
<p style="line-height:1.3em;color:rgb(0,0,0);"><strong>dfs.ha.namenodes.[nameservice ID]</strong> - unique identifiers for each NameNode in the nameservice</p>
<p style="line-height:1.3em;color:rgb(0,0,0);">Configure with a list of comma-separated NameNode IDs. This will be used by DataNodes to determine all the NameNodes in the cluster. For example, if you used “mycluster” as the nameservice ID previously, and you
 wanted to use “nn1” and “nn2” as the individual IDs of the NameNodes, you would configure this as such:</p>
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>&lt;property&gt;
  &lt;name&gt;dfs.ha.namenodes.mycluster&lt;/name&gt;
  &lt;value&gt;nn1,nn2&lt;/value&gt;
&lt;/property&gt;
</pre>
</div>
</div>
<p style="line-height:1.3em;color:rgb(0,0,0);"><strong>Note:</strong> Currently, only a maximum of two NameNodes may be configured per nameservice.</p>
</li><li style="font-size:12px;color:rgb(51,51,51);">
<p style="line-height:1.3em;color:rgb(0,0,0);"><strong>dfs.namenode.rpc-address.[nameservice ID].[name node ID]</strong> - the fully-qualified RPC address for each NameNode to listen on</p>
<p style="line-height:1.3em;color:rgb(0,0,0);">For both of the previously-configured NameNode IDs, set the full address and IPC port of the NameNode processs. Note that this results in two separate configuration options. For example:</p>
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>&lt;property&gt;
  &lt;name&gt;dfs.namenode.rpc-address.mycluster.nn1&lt;/name&gt;
  &lt;value&gt;machine1.example.com:8020&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
  &lt;name&gt;dfs.namenode.rpc-address.mycluster.nn2&lt;/name&gt;
  &lt;value&gt;machine2.example.com:8020&lt;/value&gt;
&lt;/property&gt;
</pre>
</div>
</div>
<p style="line-height:1.3em;color:rgb(0,0,0);"><strong>Note:</strong> You may similarly configure the “<strong>servicerpc-address</strong>” setting if you so desire.</p>
</li><li style="font-size:12px;color:rgb(51,51,51);">
<p style="line-height:1.3em;color:rgb(0,0,0);"><strong>dfs.namenode.http-address.[nameservice ID].[name node ID]</strong> - the fully-qualified HTTP address for each NameNode to listen on</p>
<p style="line-height:1.3em;color:rgb(0,0,0);">Similarly to <em>rpc-address</em> above, set the addresses for both NameNodes’ HTTP servers to listen on. For example:</p>
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>&lt;property&gt;
  &lt;name&gt;dfs.namenode.http-address.mycluster.nn1&lt;/name&gt;
  &lt;value&gt;machine1.example.com:50070&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
  &lt;name&gt;dfs.namenode.http-address.mycluster.nn2&lt;/name&gt;
  &lt;value&gt;machine2.example.com:50070&lt;/value&gt;
&lt;/property&gt;
</pre>
</div>
</div>
<p style="line-height:1.3em;color:rgb(0,0,0);"><strong>Note:</strong> If you have Hadoop’s security features enabled, you should also set the <em>https-address</em> similarly for each NameNode.</p>
</li><li style="font-size:12px;color:rgb(51,51,51);">
<p style="line-height:1.3em;color:rgb(0,0,0);"><strong>dfs.namenode.shared.edits.dir</strong> - the location of the shared storage directory</p>
<p style="line-height:1.3em;color:rgb(0,0,0);">This is where one configures the path to the remote shared edits directory which the Standby NameNode uses to stay up-to-date with all the file system changes the Active NameNode makes. <strong>You should only
 configure one of these directories.</strong> This directory should be mounted r/w on both NameNode machines. The value of this setting should be the absolute path to this directory on the NameNode machines. For example:</p>
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>&lt;property&gt;
  &lt;name&gt;dfs.namenode.shared.edits.dir&lt;/name&gt;
  &lt;value&gt;file:///mnt/filer1/dfs/ha-name-dir-shared&lt;/value&gt;
&lt;/property&gt;
</pre>
</div>
</div>
</li><li style="font-size:12px;color:rgb(51,51,51);">
<p style="line-height:1.3em;color:rgb(0,0,0);"><strong>dfs.client.failover.proxy.provider.[nameservice ID]</strong> - the Java class that HDFS clients use to contact the Active NameNode</p>
<p style="line-height:1.3em;color:rgb(0,0,0);">Configure the name of the Java class which will be used by the DFS Client to determine which NameNode is the current Active, and therefore which NameNode is currently serving client requests. The only implementation
 which currently ships with Hadoop is the <strong>ConfiguredFailoverProxyProvider</strong>, so use this unless you are using a custom one. For example:</p>
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>&lt;property&gt;
  &lt;name&gt;dfs.client.failover.proxy.provider.mycluster&lt;/name&gt;
  &lt;value&gt;org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider&lt;/value&gt;
&lt;/property&gt;
</pre>
</div>
</div>
</li><li style="font-size:12px;color:rgb(51,51,51);">
<p style="line-height:1.3em;color:rgb(0,0,0);"><strong>dfs.ha.fencing.methods</strong> - a list of scripts or Java classes which will be used to fence the Active NameNode during a failover</p>
<p style="line-height:1.3em;color:rgb(0,0,0);">It is critical for correctness of the system that only one NameNode be in the Active state at any given time. Thus, during a failover, we first ensure that the Active NameNode is either in the Standby state, or
 the process has terminated, before transitioning the other NameNode to the Active state. In order to do this, you must configure at least one <strong>fencing method.</strong> These are configured as a carriage-return-separated list, which will be attempted
 in order until one indicates that fencing has succeeded. There are two methods which ship with Hadoop: <em>shell</em> and <em>sshfence</em>. For information on implementing your own custom fencing method, see the<em>org.apache.hadoop.ha.NodeFencer</em> class.</p>
<hr><p style="line-height:1.3em;color:rgb(0,0,0);"><strong>sshfence</strong> - SSH to the Active NameNode and kill the process</p>
<p style="line-height:1.3em;color:rgb(0,0,0);">The <em>sshfence</em> option SSHes to the target node and uses <em>fuser</em> to kill the process listening on the service’s TCP port. In order for this fencing option to work, it must be able to SSH to the target
 node without providing a passphrase. Thus, one must also configure the <strong>dfs.ha.fencing.ssh.private-key-files</strong> option, which is a comma-separated list of SSH private key files. For example:</p>
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>    &lt;property&gt;
      &lt;name&gt;dfs.ha.fencing.methods&lt;/name&gt;
      &lt;value&gt;sshfence&lt;/value&gt;
    &lt;/property&gt;

    &lt;property&gt;
      &lt;name&gt;dfs.ha.fencing.ssh.private-key-files&lt;/name&gt;
      &lt;value&gt;/home/exampleuser/.ssh/id_rsa&lt;/value&gt;
    &lt;/property&gt;
</pre>
</div>
</div>
<p style="line-height:1.3em;color:rgb(0,0,0);">Optionally, one may configure a non-standard username or port to perform the SSH. One may also configure a timeout, in milliseconds, for the SSH, after which this fencing method will be considered to have failed.
 It may be configured like so:</p>
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>&lt;property&gt;
  &lt;name&gt;dfs.ha.fencing.methods&lt;/name&gt;
  &lt;value&gt;sshfence([[username][:port]])&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
  &lt;name&gt;dfs.ha.fencing.ssh.connect-timeout&lt;/name&gt;
  &lt;value&gt;30000&lt;/value&gt;
&lt;/property&gt;
</pre>
</div>
</div>
<hr><p style="line-height:1.3em;color:rgb(0,0,0);"><strong>shell</strong> - run an arbitrary shell command to fence the Active NameNode</p>
<p style="line-height:1.3em;color:rgb(0,0,0);">The <em>shell</em> fencing method runs an arbitrary shell command. It may be configured like so:</p>
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>&lt;property&gt;
  &lt;name&gt;dfs.ha.fencing.methods&lt;/name&gt;
  &lt;value&gt;shell(/path/to/my/script.sh arg1 arg2 ...)&lt;/value&gt;
&lt;/property&gt;
</pre>
</div>
</div>
<p style="line-height:1.3em;color:rgb(0,0,0);">The string between ‘(’ and ‘)’ is passed directly to a bash shell and may not include any closing parentheses.</p>
<p style="line-height:1.3em;color:rgb(0,0,0);">The shell command will be run with an environment set up to contain all of the current Hadoop configuration variables, with the ‘_’ character replacing any ‘.’ characters in the configuration keys. The configuration
 used has already had any namenode-specific configurations promoted to their generic forms – for example <strong>dfs_namenode_rpc-address</strong> will contain the RPC address of the target node, even though the configuration may specify that variable as <strong>dfs.namenode.rpc-address.ns1.nn1</strong>.</p>
<p style="line-height:1.3em;color:rgb(0,0,0);">Additionally, the following variables referring to the target node to be fenced are also available:</p>
<table border="0" class="bodyTable" style="margin-left:-2px;font-size:10px;"><thead><tr class="a" style="background-color:rgb(221,221,221);"><th align="left" style="vertical-align:top;color:#FFFFFF;font-size:11px;background-color:rgb(187,187,187);">
 </th>
<th align="left" style="vertical-align:top;color:#FFFFFF;font-size:11px;background-color:rgb(187,187,187);">
 </th>
</tr></thead><tbody><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
$target_host</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
hostname of the node to be fenced</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
$target_port</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
IPC port of the node to be fenced</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
$target_address</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
the above two, combined as host:port</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
$target_nameserviceid</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
the nameservice ID of the NN to be fenced</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
$target_namenodeid</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
the namenode ID of the NN to be fenced</td>
</tr></tbody></table><p style="line-height:1.3em;color:rgb(0,0,0);">These environment variables may also be used as substitutions in the shell command itself. For example:</p>
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>&lt;property&gt;
  &lt;name&gt;dfs.ha.fencing.methods&lt;/name&gt;
  &lt;value&gt;shell(/path/to/my/script.sh --nameservice=$target_nameserviceid $target_host:$target_port)&lt;/value&gt;
&lt;/property&gt;
</pre>
</div>
</div>
<p style="line-height:1.3em;color:rgb(0,0,0);">If the shell command returns an exit code of 0, the fencing is determined to be successful. If it returns any other exit code, the fencing was not successful and the next fencing method in the list will be attempted.</p>
<p style="line-height:1.3em;color:rgb(0,0,0);"><strong>Note:</strong> This fencing method does not implement any timeout. If timeouts are necessary, they should be implemented in the shell script itself (eg by forking a subshell to kill its parent in some number
 of seconds).</p>
<hr></li><li style="font-size:12px;color:rgb(51,51,51);">
<p style="line-height:1.3em;color:rgb(0,0,0);"><strong>fs.defaultFS</strong> - the default path prefix used by the Hadoop FS client when none is given</p>
<p style="line-height:1.3em;color:rgb(0,0,0);">Optionally, you may now configure the default path for Hadoop clients to use the new HA-enabled logical URI. If you used “mycluster” as the nameservice ID earlier, this will be the value of the authority portion
 of all of your HDFS paths. This may be configured like so, in your <strong>core-site.xml</strong> file:</p>
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>&lt;property&gt;
  &lt;name&gt;fs.defaultFS&lt;/name&gt;
  &lt;value&gt;hdfs://mycluster&lt;/value&gt;
&lt;/property&gt;
</pre>
</div>
</div>
</li></ul></div>
<div class="section">
<h3 style="color:rgb(102,102,102);font-size:14px;background-color:rgb(204,204,204);">
<a name="Deployment_details"></a>Deployment details</h3>
<p style="line-height:1.3em;font-size:12px;">After all of the necessary configuration options have been set, one must initially synchronize the two HA NameNodes’ on-disk metadata.</p>
<ul><li style="font-size:12px;color:rgb(51,51,51);">
<p style="line-height:1.3em;color:rgb(0,0,0);">If you are setting up a fresh HDFS cluster, you should first run the format command (<em>hdfs namenode -format</em>) on one of NameNodes.</p>
</li><li style="font-size:12px;color:rgb(51,51,51);">
<p style="line-height:1.3em;color:rgb(0,0,0);">If you have already formatted the NameNode, or are converting a non-HA-enabled cluster to be HA-enabled, you should now copy over the contents of your NameNode metadata directories to the other, unformatted NameNode
 by running the command “<em>hdfs namenode -bootstrapStandby</em>” on the unformatted NameNode. Running this command will also ensure that the shared edits directory (as configured by <strong>dfs.namenode.shared.edits.dir</strong>) contains sufficient edits
 transactions to be able to start both NameNodes.</p>
</li><li style="font-size:12px;color:rgb(51,51,51);">
<p style="line-height:1.3em;color:rgb(0,0,0);">If you are converting a non-HA NameNode to be HA, you should run the command “<em>hdfs -initializeSharedEdits</em>”, which will initialize the shared edits directory with the edits data from the local NameNode
 edits directories.</p>
</li></ul><p style="line-height:1.3em;font-size:12px;">At this point you may start both of your HA NameNodes as you normally would start a NameNode.</p>
<p style="line-height:1.3em;font-size:12px;">You can visit each of the NameNodes’ web pages separately by browsing to their configured HTTP addresses. You should notice that next to the configured address will be the HA state of the NameNode (either “standby”
 or “active”.) Whenever an HA NameNode starts, it is initially in the Standby state.</p>
</div>
<div class="section">
<h3 style="color:rgb(102,102,102);font-size:14px;background-color:rgb(204,204,204);">
<a name="Administrative_commands"></a>Administrative commands</h3>
<p style="line-height:1.3em;font-size:12px;">Now that your HA NameNodes are configured and started, you will have access to some additional commands to administer your HA HDFS cluster. Specifically, you should familiarize yourself with all of the subcommands
 of the “<em>hdfs haadmin</em>” command. Running this command without any additional arguments will display the following usage information:</p>
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>Usage: DFSHAAdmin [-ns &lt;nameserviceId&gt;]
    [-transitionToActive &lt;serviceId&gt;]
    [-transitionToStandby &lt;serviceId&gt;]
    [-failover [--forcefence] [--forceactive] &lt;serviceId&gt; &lt;serviceId&gt;]
    [-getServiceState &lt;serviceId&gt;]
    [-checkHealth &lt;serviceId&gt;]
    [-help &lt;command&gt;]
</pre>
</div>
</div>
<p style="line-height:1.3em;font-size:12px;">This guide describes high-level uses of each of these subcommands. For specific usage information of each subcommand, you should run “<em>hdfs haadmin -help &lt;command</em>&gt;”.</p>
<ul><li style="font-size:12px;color:rgb(51,51,51);">
<p style="line-height:1.3em;color:rgb(0,0,0);"><strong>transitionToActive</strong> and <strong>transitionToStandby</strong> - transition the state of the given NameNode to Active or Standby</p>
<p style="line-height:1.3em;color:rgb(0,0,0);">These subcommands cause a given NameNode to transition to the Active or Standby state, respectively. <strong>These commands do not attempt to perform any fencing, and thus should rarely be used.</strong> Instead,
 one should almost always prefer to use the “<em>hdfs haadmin -failover</em>” subcommand.</p>
</li><li style="font-size:12px;color:rgb(51,51,51);">
<p style="line-height:1.3em;color:rgb(0,0,0);"><strong>failover</strong> - initiate a failover between two NameNodes</p>
<p style="line-height:1.3em;color:rgb(0,0,0);">This subcommand causes a failover from the first provided NameNode to the second. If the first NameNode is in the Standby state, this command simply transitions the second to the Active state without error. If
 the first NameNode is in the Active state, an attempt will be made to gracefully transition it to the Standby state. If this fails, the fencing methods (as configured by <strong>dfs.ha.fencing.methods</strong>) will be attempted in order until one succeeds.
 Only after this process will the second NameNode be transitioned to the Active state. If no fencing method succeeds, the second NameNode will not be transitioned to the Active state, and an error will be returned.</p>
</li><li style="font-size:12px;color:rgb(51,51,51);">
<p style="line-height:1.3em;color:rgb(0,0,0);"><strong>getServiceState</strong> - determine whether the given NameNode is Active or Standby</p>
<p style="line-height:1.3em;color:rgb(0,0,0);">Connect to the provided NameNode to determine its current state, printing either “standby” or “active” to STDOUT appropriately. This subcommand might be used by cron jobs or monitoring scripts which need to behave
 differently based on whether the NameNode is currently Active or Standby.</p>
</li><li style="font-size:12px;color:rgb(51,51,51);">
<p style="line-height:1.3em;color:rgb(0,0,0);"><strong>checkHealth</strong> - check the health of the given NameNode</p>
<p style="line-height:1.3em;color:rgb(0,0,0);">Connect to the provided NameNode to check its health. The NameNode is capable of performing some diagnostics on itself, including checking if internal services are running as expected. This command will return
 0 if the NameNode is healthy, non-zero otherwise. One might use this command for monitoring purposes.</p>
<p style="line-height:1.3em;color:rgb(0,0,0);"><strong>Note:</strong> This is not yet implemented, and at present will always return success, unless the given NameNode is completely down.</p>
</li></ul></div>
</div>
<div class="section" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<h2 style="font-size:17px;color:rgb(51,51,51);"><a name="Automatic_Failover"></a>Automatic Failover</h2>
<div class="section">
<h3 style="color:rgb(102,102,102);font-size:14px;background-color:rgb(204,204,204);">
<a name="Introduction"></a>Introduction</h3>
<p style="line-height:1.3em;font-size:12px;">The above sections describe how to configure manual failover. In that mode, the system will not automatically trigger a failover from the active to the standby NameNode, even if the active node has failed. This section
 describes how to configure and deploy automatic failover.</p>
</div>
<div class="section">
<h3 style="color:rgb(102,102,102);font-size:14px;background-color:rgb(204,204,204);">
<a name="Components"></a>Components</h3>
<p style="line-height:1.3em;font-size:12px;">Automatic failover adds two new components to an HDFS deployment: a ZooKeeper quorum, and the ZKFailoverController process (abbreviated as ZKFC).</p>
<p style="line-height:1.3em;font-size:12px;">Apache ZooKeeper is a highly available service for maintaining small amounts of coordination data, notifying clients of changes in that data, and monitoring clients for failures. The implementation of automatic HDFS
 failover relies on ZooKeeper for the following things:</p>
<ul><li style="font-size:12px;color:rgb(51,51,51);">
<p style="line-height:1.3em;color:rgb(0,0,0);"><strong>Failure detection</strong> - each of the NameNode machines in the cluster maintains a persistent session in ZooKeeper. If the machine crashes, the ZooKeeper session will expire, notifying the other NameNode
 that a failover should be triggered.</p>
</li><li style="font-size:12px;color:rgb(51,51,51);">
<p style="line-height:1.3em;color:rgb(0,0,0);"><strong>Active NameNode election</strong> - ZooKeeper provides a simple mechanism to exclusively elect a node as active. If the current active NameNode crashes, another node may take a special exclusive lock in
 ZooKeeper indicating that it should become the next active.</p>
</li></ul><p style="line-height:1.3em;font-size:12px;">The ZKFailoverController (ZKFC) is a new component which is a ZooKeeper client which also monitors and manages the state of the NameNode. Each of the machines which runs a NameNode also runs a ZKFC, and that ZKFC
 is responsible for:</p>
<ul><li style="font-size:12px;color:rgb(51,51,51);">
<p style="line-height:1.3em;color:rgb(0,0,0);"><strong>Health monitoring</strong> - the ZKFC pings its local NameNode on a periodic basis with a health-check command. So long as the NameNode responds in a timely fashion with a healthy status, the ZKFC considers
 the node healthy. If the node has crashed, frozen, or otherwise entered an unhealthy state, the health monitor will mark it as unhealthy.</p>
</li><li style="font-size:12px;color:rgb(51,51,51);">
<p style="line-height:1.3em;color:rgb(0,0,0);"><strong>ZooKeeper session management</strong> - when the local NameNode is healthy, the ZKFC holds a session open in ZooKeeper. If the local NameNode is active, it also holds a special “lock” znode. This lock uses
 ZooKeeper’s support for “ephemeral” nodes; if the session expires, the lock node will be automatically deleted.</p>
</li><li style="font-size:12px;color:rgb(51,51,51);">
<p style="line-height:1.3em;color:rgb(0,0,0);"><strong>ZooKeeper-based election</strong> - if the local NameNode is healthy, and the ZKFC sees that no other node currently holds the lock znode, it will itself try to acquire the lock. If it succeeds, then it
 has “won the election”, and is responsible for running a failover to make its local NameNode active. The failover process is similar to the manual failover described above: first, the previous active is fenced if necessary, and then the local NameNode transitions
 to active state.</p>
</li></ul><p style="line-height:1.3em;font-size:12px;">For more details on the design of automatic failover, refer to the design document attached to HDFS-2185 on the Apache HDFS JIRA.</p>
</div>
<div class="section">
<h3 style="color:rgb(102,102,102);font-size:14px;background-color:rgb(204,204,204);">
<a name="Deploying_ZooKeeper"></a>Deploying ZooKeeper</h3>
<p style="line-height:1.3em;font-size:12px;">In a typical deployment, ZooKeeper daemons are configured to run on three or five nodes. Since ZooKeeper itself has light resource requirements, it is acceptable to collocate the ZooKeeper nodes on the same hardware
 as the HDFS NameNode and Standby Node. Many operators choose to deploy the third ZooKeeper process on the same node as the YARN ResourceManager. It is advisable to configure the ZooKeeper nodes to store their data on separate disk drives from the HDFS metadata
 for best performance and isolation.</p>
<p style="line-height:1.3em;font-size:12px;">The setup of ZooKeeper is out of scope for this document. We will assume that you have set up a ZooKeeper cluster running on three or more nodes, and have verified its correct operation by connecting using the ZK
 CLI.</p>
</div>
<div class="section">
<h3 style="color:rgb(102,102,102);font-size:14px;background-color:rgb(204,204,204);">
<a name="Before_you_begin"></a>Before you begin</h3>
<p style="line-height:1.3em;font-size:12px;">Before you begin configuring automatic failover, you should shut down your cluster. It is not currently possible to transition from a manual failover setup to an automatic failover setup while the cluster is running.</p>
</div>
<div class="section">
<h3 style="color:rgb(102,102,102);font-size:14px;background-color:rgb(204,204,204);">
<a name="Configuring_automatic_failover"></a>Configuring automatic failover</h3>
<p style="line-height:1.3em;font-size:12px;">The configuration of automatic failover requires the addition of two new parameters to your configuration. In your <tt>hdfs-site.xml</tt> file, add:</p>
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre> &lt;property&gt;
   &lt;name&gt;dfs.ha.automatic-failover.enabled&lt;/name&gt;
   &lt;value&gt;true&lt;/value&gt;
 &lt;/property&gt;
</pre>
</div>
</div>
<p style="line-height:1.3em;font-size:12px;">This specifies that the cluster should be set up for automatic failover. In your <tt>core-site.xml</tt> file, add:</p>
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre> &lt;property&gt;
   &lt;name&gt;ha.zookeeper.quorum&lt;/name&gt;
   &lt;value&gt;zk1.example.com:2181,zk2.example.com:2181,zk3.example.com:2181&lt;/value&gt;
 &lt;/property&gt;
</pre>
</div>
</div>
<p style="line-height:1.3em;font-size:12px;">This lists the host-port pairs running the ZooKeeper service.</p>
<p style="line-height:1.3em;font-size:12px;">As with the parameters described earlier in the document, these settings may be configured on a per-nameservice basis by suffixing the configuration key with the nameservice ID. For example, in a cluster with federation
 enabled, you can explicitly enable automatic failover for only one of the nameservices by setting <tt>dfs.ha.automatic-failover.enabled.my-nameservice-id</tt>.</p>
<p style="line-height:1.3em;font-size:12px;">There are also several other configuration parameters which may be set to control the behavior of automatic failover; however, they are not necessary for most installations. Please refer to the configuration key
 specific documentation for details.</p>
</div>
<div class="section">
<h3 style="color:rgb(102,102,102);font-size:14px;background-color:rgb(204,204,204);">
<a name="Initializing_HA_state_in_ZooKeeper"></a>Initializing HA state in ZooKeeper</h3>
<p style="line-height:1.3em;font-size:12px;">After the configuration keys have been added, the next step is to initialize required state in ZooKeeper. You can do so by running the following command from one of the NameNode hosts.</p>
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>[hdfs]$ $HADOOP_PREFIX/bin/zkfc -formatZK
</pre>
</div>
</div>
<p style="line-height:1.3em;font-size:12px;">This will create a znode in ZooKeeper inside of which the automatic failover system stores its data.</p>
</div>
<div class="section">
<h3 style="color:rgb(102,102,102);font-size:14px;background-color:rgb(204,204,204);">
<a name="Starting_the_cluster_with_start-dfs.sh"></a>Starting the cluster with <tt>start-dfs.sh</tt></h3>
<p style="line-height:1.3em;font-size:12px;">Since automatic failover has been enabled in the configuration, the <tt>start-dfs.sh</tt> script will now automatically start a ZKFC daemon on any machine that runs a NameNode. When the ZKFCs start, they will automatically
 select one of the NameNodes to become active.</p>
</div>
<div class="section">
<h3 style="color:rgb(102,102,102);font-size:14px;background-color:rgb(204,204,204);">
<a name="Starting_the_cluster_manually"></a>Starting the cluster manually</h3>
<p style="line-height:1.3em;font-size:12px;">If you manually manage the services on your cluster, you will need to manually start the <tt>zkfc</tt> daemon on each of the machines that runs a NameNode. You can start the daemon by running:</p>
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>[hdfs]$ $HADOOP_PREFIX/sbin/hadoop-daemon.sh --script $HADOOP_PREFIX/bin/hdfs start zkfc
</pre>
</div>
</div>
</div>
<div class="section">
<h3 style="color:rgb(102,102,102);font-size:14px;background-color:rgb(204,204,204);">
<a name="Securing_access_to_ZooKeeper"></a>Securing access to ZooKeeper</h3>
<p style="line-height:1.3em;font-size:12px;">If you are running a secure cluster, you will likely want to ensure that the information stored in ZooKeeper is also secured. This prevents malicious clients from modifying the metadata in ZooKeeper or potentially
 triggering a false failover.</p>
<p style="line-height:1.3em;font-size:12px;">In order to secure the information in ZooKeeper, first add the following to your <tt>core-site.xml</tt> file:</p>
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre> &lt;property&gt;
   &lt;name&gt;ha.zookeeper.auth&lt;/name&gt;
   &lt;value&gt;@/path/to/zk-auth.txt&lt;/value&gt;
 &lt;/property&gt;
 &lt;property&gt;
   &lt;name&gt;ha.zookeeper.acl&lt;/name&gt;
   &lt;value&gt;@/path/to/zk-acl.txt&lt;/value&gt;
 &lt;/property&gt;
</pre>
</div>
</div>
<p style="line-height:1.3em;font-size:12px;">Please note the ‘@’ character in these values – this specifies that the configurations are not inline, but rather point to a file on disk.</p>
<p style="line-height:1.3em;font-size:12px;">The first configured file specifies a list of ZooKeeper authentications, in the same format as used by the ZK CLI. For example, you may specify something like:</p>
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>digest:hdfs-zkfcs:mypassword
</pre>
</div>
</div>
<p style="line-height:1.3em;font-size:12px;">…where <tt>hdfs-zkfcs</tt> is a unique username for ZooKeeper, and <tt>mypassword</tt> is some unique string used as a password.</p>
<p style="line-height:1.3em;font-size:12px;">Next, generate a ZooKeeper ACL that corresponds to this authentication, using a command like the following:</p>
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>[hdfs]$ java -cp $ZK_HOME/lib/*:$ZK_HOME/zookeeper-3.4.2.jar org.apache.zookeeper.server.auth.DigestAuthenticationProvider hdfs-zkfcs:mypassword
output: hdfs-zkfcs:mypassword-&gt;hdfs-zkfcs:P/OQvnYyU/nF/mGYvB/xurX8dYs=
</pre>
</div>
</div>
<p style="line-height:1.3em;font-size:12px;">Copy and paste the section of this output after the ‘-&gt;’ string into the file <tt>zk-acls.txt</tt>, prefixed by the string “<tt>digest:</tt>”. For example:</p>
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>digest:hdfs-zkfcs:vlUvLnd8MlacsE80rDuu6ONESbM=:rwcda
</pre>
</div>
</div>
<p style="line-height:1.3em;font-size:12px;">In order for these ACLs to take effect, you should then rerun the <tt>zkfc -formatZK</tt> command as described above.</p>
<p style="line-height:1.3em;font-size:12px;">After doing so, you may verify the ACLs from the ZK CLI as follows:</p>
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>[zk: localhost:2181(CONNECTED) 1] getAcl /hadoop-ha
'digest,'hdfs-zkfcs:vlUvLnd8MlacsE80rDuu6ONESbM=
: cdrwa
</pre>
</div>
</div>
</div>
<div class="section">
<h3 style="color:rgb(102,102,102);font-size:14px;background-color:rgb(204,204,204);">
<a name="Verifying_automatic_failover"></a>Verifying automatic failover</h3>
<p style="line-height:1.3em;font-size:12px;">Once automatic failover has been set up, you should test its operation. To do so, first locate the active NameNode. You can tell which node is active by visiting the NameNode web interfaces – each node reports its
 HA state at the top of the page.</p>
<p style="line-height:1.3em;font-size:12px;">Once you have located your active NameNode, you may cause a failure on that node. For example, you can use <tt>kill -9 &lt;pid of NN</tt>&gt; to simulate a JVM crash. Or, you could power cycle the machine or unplug its
 network interface to simulate a different kind of outage. After triggering the outage you wish to test, the other NameNode should automatically become active within several seconds. The amount of time required to detect a failure and trigger a fail-over depends
 on the configuration of <tt>ha.zookeeper.session-timeout.ms</tt>, but defaults to 5 seconds.</p>
<p style="line-height:1.3em;font-size:12px;">If the test does not succeed, you may have a misconfiguration. Check the logs for the <tt>zkfc</tt> daemons as well as the NameNode daemons in order to further diagnose the issue.</p>
</div>
</div>
<div class="section" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<h2 style="font-size:17px;color:rgb(51,51,51);"><a name="Automatic_Failover_FAQ"></a>Automatic Failover FAQ</h2>
<ul><li style="font-size:12px;color:rgb(51,51,51);">
<p style="line-height:1.3em;color:rgb(0,0,0);"><strong>Is it important that I start the ZKFC and NameNode daemons in any particular order?</strong></p>
<p style="line-height:1.3em;color:rgb(0,0,0);">No. On any given node you may start the ZKFC before or after its corresponding NameNode.</p>
</li><li style="font-size:12px;color:rgb(51,51,51);">
<p style="line-height:1.3em;color:rgb(0,0,0);"><strong>What additional monitoring should I put in place?</strong></p>
<p style="line-height:1.3em;color:rgb(0,0,0);">You should add monitoring on each host that runs a NameNode to ensure that the ZKFC remains running. In some types of ZooKeeper failures, for example, the ZKFC may unexpectedly exit, and should be restarted to
 ensure that the system is ready for automatic failover.</p>
<p style="line-height:1.3em;color:rgb(0,0,0);">Additionally, you should monitor each of the servers in the ZooKeeper quorum. If ZooKeeper crashes, then automatic failover will not function.</p>
</li><li style="font-size:12px;color:rgb(51,51,51);">
<p style="line-height:1.3em;color:rgb(0,0,0);"><strong>What happens if ZooKeeper goes down?</strong></p>
<p style="line-height:1.3em;color:rgb(0,0,0);">If the ZooKeeper cluster crashes, no automatic failovers will be triggered. However, HDFS will continue to run without any impact. When ZooKeeper is restarted, HDFS will reconnect with no issues.</p>
</li><li style="font-size:12px;color:rgb(51,51,51);">
<p style="line-height:1.3em;color:rgb(0,0,0);"><strong>Can I designate one of my NameNodes as primary/preferred?</strong></p>
<p style="line-height:1.3em;color:rgb(0,0,0);">No. Currently, this is not supported. Whichever NameNode is started first will become active. You may choose to start the cluster in a specific order such that your preferred node starts first.</p>
</li><li style="font-size:12px;color:rgb(51,51,51);">
<p style="line-height:1.3em;color:rgb(0,0,0);"><strong>How can I initiate a manual failover when automatic failover is configured?</strong></p>
<p style="line-height:1.3em;color:rgb(0,0,0);">Even if automatic failover is configured, you may initiate a manual failover using the same <tt>hdfs haadmin</tt> command. It will perform a coordinated failover.</p>
</li></ul></div>
<div class="section" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<h2 style="font-size:17px;color:rgb(51,51,51);"><a name="BookKeeper_as_a_Shared_storage_EXPERIMENTAL"></a>BookKeeper as a Shared storage (EXPERIMENTAL)</h2>
<p style="line-height:1.3em;font-size:12px;">One option for shared storage for the NameNode is BookKeeper. BookKeeper achieves high availability and strong durability guarantees by replicating edit log entries across multiple storage nodes. The edit log can
 be striped across the storage nodes for high performance. Fencing is supported in the protocol, i.e, BookKeeper will not allow two writers to write the single edit log.</p>
<p style="line-height:1.3em;font-size:12px;">The meta data for BookKeeper is stored in ZooKeeper. In current HA architecture, a Zookeeper cluster is required for ZKFC. The same cluster can be for BookKeeper metadata.</p>
<p style="line-height:1.3em;font-size:12px;">For more details on building a BookKeeper cluster, please refer to the <a class="externalLink" href="http://zookeeper.apache.org/bookkeeper/docs/trunk/bookkeeperConfig.html" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">BookKeeper
 documentation</a></p>
<p style="line-height:1.3em;font-size:12px;">The BookKeeperJournalManager is an implementation of the HDFS JournalManager interface, which allows custom write ahead logging implementations to be plugged into the HDFS NameNode.</p>
<ul><li style="font-size:12px;color:rgb(51,51,51);">
<p style="line-height:1.3em;color:rgb(0,0,0);"><strong>BookKeeper Journal Manager</strong></p>
<p style="line-height:1.3em;color:rgb(0,0,0);">To use BookKeeperJournalManager, add the following to hdfs-site.xml.</p>
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>    &lt;property&gt;
      &lt;name&gt;dfs.namenode.shared.edits.dir&lt;/name&gt;
      &lt;value&gt;bookkeeper://zk1:2181;zk2:2181;zk3:2181/hdfsjournal&lt;/value&gt;
    &lt;/property&gt;

    &lt;property&gt;
      &lt;name&gt;dfs.namenode.edits.journal-plugin.bookkeeper&lt;/name&gt;
      &lt;value&gt;org.apache.hadoop.contrib.bkjournal.BookKeeperJournalManager&lt;/value&gt;
    &lt;/property&gt;
</pre>
</div>
</div>
<p style="line-height:1.3em;color:rgb(0,0,0);">The URI format for bookkeeper is <tt>bookkeeper://[zkEnsemble]/[rootZnode] [zookkeeper ensemble]</tt> is a list of semi-colon separated, zookeeper host:port pairs. In the example above there are 3 servers, in the
 ensemble, zk1, zk2 &amp; zk3, each one listening on port 2181.</p>
<p style="line-height:1.3em;color:rgb(0,0,0);"><tt>[root znode]</tt> is the path of the zookeeper znode, under which the edit log information will be stored.</p>
<p style="line-height:1.3em;color:rgb(0,0,0);">The class specified for the journal-plugin must be available in the NameNode’s classpath. We explain how to generate a jar file with the journal manager and its dependencies, and how to put it into the classpath
 below.</p>
</li><li style="font-size:12px;color:rgb(51,51,51);">
<p style="line-height:1.3em;color:rgb(0,0,0);"><strong>More configuration options</strong></p>
<ul><li><strong>dfs.namenode.bookkeeperjournal.output-buffer-size</strong> - Number of bytes a bookkeeper journal stream will buffer before forcing a flush. Default is 1024.</li></ul><div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>           &lt;property&gt;
             &lt;name&gt;dfs.namenode.bookkeeperjournal.output-buffer-size&lt;/name&gt;
             &lt;value&gt;1024&lt;/value&gt;
           &lt;/property&gt;
</pre>
</div>
</div>
<ul><li><strong>dfs.namenode.bookkeeperjournal.ensemble-size</strong> - Number of bookkeeper servers in edit log ensembles. This is the number of bookkeeper servers which need to be available for the edit log to be writable. Default is 3.</li></ul><div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>           &lt;property&gt;
             &lt;name&gt;dfs.namenode.bookkeeperjournal.ensemble-size&lt;/name&gt;
             &lt;value&gt;3&lt;/value&gt;
           &lt;/property&gt;
</pre>
</div>
</div>
<ul><li><strong>dfs.namenode.bookkeeperjournal.quorum-size</strong> - Number of bookkeeper servers in the write quorum. This is the number of bookkeeper servers which must have acknowledged the write of an entry before it is considered written. Default is 2.</li></ul><div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>           &lt;property&gt;
             &lt;name&gt;dfs.namenode.bookkeeperjournal.quorum-size&lt;/name&gt;
             &lt;value&gt;2&lt;/value&gt;
           &lt;/property&gt;
</pre>
</div>
</div>
<ul><li><strong>dfs.namenode.bookkeeperjournal.digestPw</strong> - Password to use when creating edit log segments.</li></ul><div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>           &lt;property&gt;
            &lt;name&gt;dfs.namenode.bookkeeperjournal.digestPw&lt;/name&gt;
            &lt;value&gt;myPassword&lt;/value&gt;
           &lt;/property&gt;
</pre>
</div>
</div>
<ul><li><strong>dfs.namenode.bookkeeperjournal.zk.session.timeout</strong> - Session timeout for Zookeeper client from BookKeeper Journal Manager. Hadoop recommends that this value should be less than the ZKFC session timeout value. Default value is 3000.</li></ul><div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>           &lt;property&gt;
             &lt;name&gt;dfs.namenode.bookkeeperjournal.zk.session.timeout&lt;/name&gt;
             &lt;value&gt;3000&lt;/value&gt;
           &lt;/property&gt;
</pre>
</div>
</div>
</li><li style="font-size:12px;color:rgb(51,51,51);">
<p style="line-height:1.3em;color:rgb(0,0,0);"><strong>Building BookKeeper Journal Manager plugin jar</strong></p>
<p style="line-height:1.3em;color:rgb(0,0,0);">To generate the distribution packages for BK journal, do the following.</p>
<p style="line-height:1.3em;color:rgb(0,0,0);">$ mvn clean package -Pdist</p>
<p style="line-height:1.3em;color:rgb(0,0,0);">This will generate a jar with the BookKeeperJournalManager, hadoop-hdfs/src/contrib/bkjournal/target/hadoop-hdfs-bkjournal-<em>VERSION</em>.jar</p>
<p style="line-height:1.3em;color:rgb(0,0,0);">Note that the -Pdist part of the build command is important, this would copy the dependent bookkeeper-server jar under hadoop-hdfs/src/contrib/bkjournal/target/lib.</p>
</li><li style="font-size:12px;color:rgb(51,51,51);">
<p style="line-height:1.3em;color:rgb(0,0,0);"><strong>Putting the BookKeeperJournalManager in the NameNode classpath</strong></p>
<p style="line-height:1.3em;color:rgb(0,0,0);">To run a HDFS namenode using BookKeeper as a backend, copy the bkjournal and bookkeeper-server jar, mentioned above, into the lib directory of hdfs. In the standard distribution of HDFS, this is at $HADOOP_HDFS_HOME/share/hadoop/hdfs/lib/</p>
<p style="line-height:1.3em;color:rgb(0,0,0);">cp hadoop-hdfs/src/contrib/bkjournal/target/hadoop-hdfs-bkjournal-<em>VERSION</em>.jar $HADOOP_HDFS_HOME/share/hadoop/hdfs/lib/</p>
</li><li style="font-size:12px;color:rgb(51,51,51);">
<p style="line-height:1.3em;color:rgb(0,0,0);"><strong>Current limitations</strong></p>
<p style="line-height:1.3em;color:rgb(0,0,0);">1) Security in BookKeeper. BookKeeper does not support SASL nor SSL for connections between the NameNode and BookKeeper storage nodes.</p>
</li></ul></div>
<div class="section" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<ul><li style="font-size:12px;color:rgb(51,51,51);">
<p style="line-height:1.3em;color:rgb(0,0,0);"></p>
</li></ul></div>
            </div>
                </div>