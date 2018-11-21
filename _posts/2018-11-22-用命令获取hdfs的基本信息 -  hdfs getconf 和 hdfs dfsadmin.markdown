---
layout:     post
title:      用命令获取hdfs的基本信息 -  hdfs getconf 和 hdfs dfsadmin
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/hjh00/article/details/53096934				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>用hdfs getconf 和  hdfs dfsadmin 可以获得hdfs的基本信息。用 hdfs --help 可以列出这两个命令。</p>
<p><strong><span style="font-family:'Microsoft YaHei';font-size:14px;">1 hdfs --help </span></strong></p>
<p></p><pre><code class="language-plain">hdfs@hbase3:~$ hdfs --help
Usage: hdfs [--config confdir] COMMAND
       where COMMAND is one of:
  dfs                  run a filesystem command on the file systems supported in Hadoop.
  namenode -format     format the DFS filesystem
  secondarynamenode    run the DFS secondary namenode
  namenode             run the DFS namenode
  journalnode          run the DFS journalnode
  zkfc                 run the ZK Failover Controller daemon
  datanode             run a DFS datanode
  dfsadmin             run a DFS admin client
  haadmin              run a DFS HA admin client
  fsck                 run a DFS filesystem checking utility
  balancer             run a cluster balancing utility
  jmxget               get JMX exported values from NameNode or DataNode.
  mover                run a utility to move block replicas across
                       storage types
  oiv                  apply the offline fsimage viewer to an fsimage
  oiv_legacy           apply the offline fsimage viewer to an legacy fsimage
  oev                  apply the offline edits viewer to an edits file
  fetchdt              fetch a delegation token from the NameNode
  getconf              get config values from configuration
  groups               get the groups which users belong to
  snapshotDiff         diff two snapshots of a directory or diff the
                       current directory contents with a snapshot
  lsSnapshottableDir   list all snapshottable dirs owned by the current user
                                                Use -help to see options
  portmap              run a portmap service
  nfs3                 run an NFS version 3 gateway
  cacheadmin           configure the HDFS cache
  crypto               configure HDFS encryption zones
  storagepolicies      list/get/set block storage policies
  version              print the version</code></pre>
<p><span style="font-family:'Microsoft YaHei';font-size:14px;"><strong>2 hdfs getconf</strong></span></p>
<p></p><pre><code class="language-plain">hdfs@hbase3:~$ hdfs getconf
hdfs getconf is utility for getting configuration information from the config file.

hadoop getconf 
        [-namenodes]                    gets list of namenodes in the cluster.
        [-secondaryNameNodes]                   gets list of secondary namenodes in the cluster.
        [-backupNodes]                  gets list of backup nodes in the cluster.
        [-includeFile]                  gets the include file path that defines the datanodes that can join the cluster.
        [-excludeFile]                  gets the exclude file path that defines the datanodes that need to decommissioned.
        [-nnRpcAddresses]                       gets the namenode rpc addresses
        [-confKey [key]]                        gets a specific key from the configuration

hdfs@hbase3:~$ hdfs getconf -namenodes
hbase3
hdfs@hbase3:~$ hdfs getconf -secondaryNameNodes 
0.0.0.0
hdfs@hbase3:~$ hdfs getconf -backupNodes       
0.0.0.0
hdfs@hbase3:~$ hdfs getconf -includeFile
Configuration dfs.hosts is missing.
hdfs@hbase3:~$ hdfs getconf -excludeFile
Configuration dfs.hosts.exclude is missing.
hdfs@hbase3:~$ hdfs getconf -nnRpcAddresses
hbase3:8022</code></pre><span style="font-family:'Microsoft YaHei';font-size:14px;"><strong>3 hdfs dfsadmin</strong></span>
<p>这个命令有多个选项，查看信息用hdfs dfsadmin -report</p>
<p></p><pre><code class="language-plain">hdfs@hbase3:~$ hdfs dfsadmin -report
Configured Capacity: 8762720919552 (7.97 TB)
Present Capacity: 8408422924288 (7.65 TB)
DFS Remaining: 8407717933056 (7.65 TB)
DFS Used: 704991232 (672.33 MB)
DFS Used%: 0.01%
Under replicated blocks: 0
Blocks with corrupt replicas: 0
Missing blocks: 0
Missing blocks (with replication factor 1): 0

-------------------------------------------------
Live datanodes (3):

Name: 192.1.103.154:50010 (hbase2)
Hostname: hbase2
Rack: /default
Decommission Status : Normal
Configured Capacity: 2920906973184 (2.66 TB)
DFS Used: 234676224 (223.80 MB)
Non DFS Used: 118094106624 (109.98 GB)
DFS Remaining: 2802578190336 (2.55 TB)
DFS Used%: 0.01%
DFS Remaining%: 95.95%
Configured Cache Capacity: 4294967296 (4 GB)
Cache Used: 0 (0 B)
Cache Remaining: 4294967296 (4 GB)
Cache Used%: 0.00%
Cache Remaining%: 100.00%
Xceivers: 10
Last contact: Wed Nov 09 09:10:40 CST 2016


Name: 192.1.103.153:50010 (hbase3)
Hostname: hbase3
Rack: /default
Decommission Status : Normal
Configured Capacity: 2920906973184 (2.66 TB)
DFS Used: 235249664 (224.35 MB)
Non DFS Used: 118159708160 (110.04 GB)
DFS Remaining: 2802512015360 (2.55 TB)
DFS Used%: 0.01%
DFS Remaining%: 95.95%
Configured Cache Capacity: 4294967296 (4 GB)
Cache Used: 0 (0 B)
Cache Remaining: 4294967296 (4 GB)
Cache Used%: 0.00%
Cache Remaining%: 100.00%
Xceivers: 10
Last contact: Wed Nov 09 09:10:39 CST 2016


Name: 192.1.103.155:50010 (hbase1)
Hostname: hbase1
Rack: /default
Decommission Status : Normal
Configured Capacity: 2920906973184 (2.66 TB)
DFS Used: 235065344 (224.18 MB)
Non DFS Used: 118044180480 (109.94 GB)
DFS Remaining: 2802627727360 (2.55 TB)
DFS Used%: 0.01%
DFS Remaining%: 95.95%
Configured Cache Capacity: 4294967296 (4 GB)
Cache Used: 0 (0 B)
Cache Remaining: 4294967296 (4 GB)
Cache Used%: 0.00%
Cache Remaining%: 100.00%
Xceivers: 10
Last contact: Wed Nov 09 09:10:40 CST 2016
</code></pre><br><br><p><br></p>
<p><br></p>
<p><br></p>
            </div>
                </div>