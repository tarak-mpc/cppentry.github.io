---
layout:     post
title:      HBase集群搭建1Master、2Slaves
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p align="center"><strong>HBase集群</strong></p>
<p>Master.Hadoop 10.9.148.21 master(root,123456) /softs/hbase  /softs/ hbase/logs</p>
<p>Slave1.Hadoop 10.9.148.26  slave1(root,123456) /softs/hbase  /softs/ hbase/logs</p>
<p>Slave2.Hadoop 10.9.148.23  slave2(root,123456) /softs/hbase  /softs/hbase/logs</p>
<p> </p>
<p>hbase-1.2.6-bin.tar.gz</p>
<p> 826  scp hbase-1.2.6-bin.tar.gzroot@10.9.148.26:/softs/</p>
<p> 827  scp hbase-1.2.6-bin.tar.gzroot@10.9.148.23:/softs/</p>
<p><strong>Master slave 相同：</strong></p>
<p>tar –zxf hbase-1.2.6-bin.tar.gz</p>
<p> 830  mv hbase-1.2.6 hbase</p>
<p> 833  cd hbase</p>
<p> 836  vi /etc/profile</p>
<p><span style="color:#FF0000;">添加内容：</span></p>
<p><span style="color:#00B050;">#hbase      </span></p>
<p><span style="color:#00B050;">exportHBASE_HOME=/softs/hbase</span></p>
<p><span style="color:#00B050;">exportPATH=$PATH:$HBASE_HOME</span></p>
<p><span style="color:#00B050;">#stop hbase zookeeperrunning</span></p>
<p><span style="color:#00B050;">exportHBASE_MANAGES_ZK=false</span></p>
<p>      vi /etc/hosts</p>
<p><span style="color:#00B050;">#zookeeper MasterSlave IP address</span></p>
<p><span style="color:#00B050;">10.9.148.21 master</span></p>
<p><span style="color:#00B050;">10.9.148.26 slave1</span></p>
<p><span style="color:#00B050;">10.9.148.23 slave2</span></p>
<p> 837  source /etc/profile</p>
<p> 839  vi conf/hbase-site.xml</p>
<p><span style="color:#FF0000;">添加内容：</span></p>
<p><span style="color:#00B050;">&lt;configuration&gt;</span></p>
<p><span style="color:#00B050;">        &lt;property&gt;</span></p>
<p><span style="color:#00B050;">               &lt;name&gt;hbase.rootdir&lt;/name&gt;</span></p>
<p><span style="color:#00B050;">               &lt;value&gt;hdfs://10.9.148.21:9000/hbase&lt;/value&gt;</span></p>
<p><span style="color:#00B050;">        &lt;/property&gt;</span></p>
<p><span style="color:#00B050;">        &lt;property&gt;</span></p>
<p><span style="color:#00B050;">               &lt;name&gt;hbase.cluster.distributed&lt;/name&gt;</span></p>
<p><span style="color:#00B050;">                &lt;value&gt;true&lt;/value&gt;</span></p>
<p><span style="color:#00B050;">        &lt;/property&gt;</span></p>
<p><span style="color:#00B050;">        &lt;property&gt;</span></p>
<p><span style="color:#00B050;">               &lt;name&gt;hbase.master&lt;/name&gt;</span></p>
<p><span style="color:#00B050;">                &lt;value&gt;master&lt;/value&gt;</span></p>
<p><span style="color:#00B050;">        &lt;/property&gt;</span></p>
<p><span style="color:#00B050;">        &lt;property&gt;</span></p>
<p><span style="color:#00B050;">               &lt;name&gt;hbase.zookeeper.quorum&lt;/name&gt;</span></p>
<p><span style="color:#00B050;">               &lt;value&gt;master:2181,slave1:2181,slave2:2181&lt;/value&gt;</span></p>
<p><span style="color:#00B050;">        &lt;/property&gt;</span></p>
<p><span style="color:#00B050;">        &lt;property&gt;</span></p>
<p><span style="color:#00B050;">               &lt;name&gt;hbase.zookeeper.property.dataDir&lt;/name&gt;</span></p>
<p><span style="color:#00B050;">               &lt;value&gt;/softs/zookeeper/data&lt;/value&gt;</span></p>
<p><span style="color:#00B050;">        &lt;/property&gt;</span></p>
<p><span style="color:#00B050;">&lt;/configuration&gt;</span></p>
<p> 840  vi conf/regionservers</p>
<p><span style="color:#FF0000;">添加内容：</span></p>
<p><span style="color:#00B050;">slave1</span></p>
<p><span style="color:#00B050;">slave2</span></p>
<p>     vi conf/hbase-env.sh</p>
<p>添加内容：</p>
<p>export JAVA_HOME=/usr/local/jdk1.7</p>
<p>export HBASE_MANAGES_ZK=false</p>
<p> </p>
<p><strong>启动HBase集群</strong></p>
<p>启动之前，在Hadoop的文件系统中创建hbase目录</p>
<p>  850 start-all.sh</p>
<p>查看./logs目录下的</p>
<p>vihbase/logs/hbase-root-master-Master.Hadoop.log</p>
<p>发现slave1和slave2没有启动，错误提示：连接master超时（或者hbase集群节点HRegionServer启动后自动关闭）</p>
<p>出现问题的原因：3台主机的系统时钟不一致，相差超过30s导致。</p>
<p>解决问题：使用工具，统一时间（集群时钟管理工具）</p>
<p>           在这里，我是用的是直接设置时钟：</p>
<p>           1052 date -s 11:41:00</p>
<p> 1053 clock -w</p>
<p>重新启动集群</p>
<p>1057  bin/start-hbase.sh</p>
<p><strong>验证：</strong></p>
<p><strong><span style="color:#FF0000;">Master</span><span style="color:#FF0000;">：</span></strong></p>
<p><span style="color:#FF0000;">[root@Master softs]# jps</span></p>
<p><span style="color:#FF0000;">3717 SecondaryNameNode</span></p>
<p><span style="color:#FF0000;">4606 QuorumPeerMain</span></p>
<p><span style="color:#FF0000;">13543 Jps</span></p>
<p><span style="color:#FF0000;">6909 HMaster</span></p>
<p><span style="color:#FF0000;">6756 NameNode</span></p>
<p><span style="color:#FF0000;">3911 ResourceManager</span></p>
<p><span style="color:#FF0000;">[root@Master softs]#</span></p>
<p><strong><span style="color:#FF0000;">Slave1</span><span style="color:#FF0000;">：</span></strong></p>
<p><span style="color:#FF0000;">[root@Slave1 hbase]# jps</span></p>
<p><span style="color:#FF0000;">24344 Jps</span></p>
<p><span style="color:#FF0000;">4148 SecondaryNameNode</span></p>
<p><span style="color:#FF0000;">3058 QuorumPeerMain</span></p>
<p><span style="color:#FF0000;">3981 DataNode</span></p>
<p><span style="color:#FF0000;">4310 ResourceManager</span></p>
<p><span style="color:#FF0000;">4427 NodeManager</span></p>
<p><span style="color:#FF0000;">23638 HRegionServer</span></p>
<p><span style="color:#FF0000;">[root@Slave1 hbase]#</span></p>
<p><strong><span style="color:#FF0000;">Slave2</span><span style="color:#FF0000;">：</span></strong></p>
<p><span style="color:#FF0000;">[root@Slave2 hbase]# jps</span></p>
<p><span style="color:#FF0000;">3596 QuorumPeerMain</span></p>
<p><span style="color:#FF0000;">4048 DataNode</span></p>
<p><span style="color:#FF0000;">11495 HRegionServer</span></p>
<p><span style="color:#FF0000;">12157 Jps</span></p>
<p><span style="color:#FF0000;">[root@Slave2 hbase]#</span></p>
<p><strong>在浏览器访问主节点16010端口（即<a href="http://10.9.148.21:16010/" rel="nofollow">http://10.9.148.21:16010/</a>），可以产看HBase Web UI</strong></p>
<p></p>
<p><img src="https://img-blog.csdn.net/20170829214751076?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSVR3YW5nbmVuZ2ppZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p><img src="https://img-blog.csdn.net/20170829214807993?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSVR3YW5nbmVuZ2ppZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><img src="https://img-blog.csdn.net/20170829214826823?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSVR3YW5nbmVuZ2ppZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p></p>
<p><strong>在hadoop的文件系统中：</strong></p>
<p><img src="https://img-blog.csdn.net/20170829214849690?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSVR3YW5nbmVuZ2ppZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p><img src="https://img-blog.csdn.net/20170829214908484?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSVR3YW5nbmVuZ2ppZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p></p>
<p>HBase启动几次失败，后来改正HBase配置后，依旧失败。可以试着将hadoop的HBase的文件目录删掉重建，再重新启动HBase集群。这样可以成功。</p>
<p><br></p>
<p>HBaseJava使用<br></p>
<p> <img src="https://img-blog.csdn.net/20170829214926900?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSVR3YW5nbmVuZ2ppZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p><img src="https://img-blog.csdn.net/20170829214956555?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvSVR3YW5nbmVuZ2ppZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p> </p>
<p>Maven依赖：</p>
<p align="left"><span style="color:#3F5FBF;">&lt;!-- <u>hbase</u> --&gt;</span></p>
<p align="left">  <span style="color:#008080;">&lt;</span><span style="color:#3F7F7F;">dependency</span><span style="color:#008080;">&gt;</span></p>
<p align="left">   <span style="color:#008080;">&lt;</span><span style="color:#3F7F7F;">groupId</span><span style="color:#008080;">&gt;</span>org.apache.hbase<span style="color:#008080;">&lt;/</span><span style="color:#3F7F7F;">groupId</span><span style="color:#008080;">&gt;</span></p>
<p align="left">   <span style="color:#008080;">&lt;</span><span style="color:#3F7F7F;">artifactId</span><span style="color:#008080;">&gt;</span><u>hbase</u>-it<span style="color:#008080;">&lt;/</span><span style="color:#3F7F7F;">artifactId</span><span style="color:#008080;">&gt;</span></p>
<p align="left">   <span style="color:#008080;">&lt;</span><span style="color:#3F7F7F;">version</span><span style="color:#008080;">&gt;</span>1.2.6<span style="color:#008080;">&lt;/</span><span style="color:#3F7F7F;">version</span><span style="color:#008080;">&gt;</span></p>
<p align="left">  <span style="color:#008080;">&lt;/</span><span style="color:#3F7F7F;">dependency</span><span style="color:#008080;">&gt;</span></p>
<p align="left">  <span style="color:#3F5FBF;">&lt;!--spring --&gt;</span></p>
<p align="left">            <span style="color:#008080;">&lt;</span><span style="color:#3F7F7F;">dependency</span><span style="color:#008080;">&gt;</span></p>
<p align="left">            <span style="color:#008080;">&lt;</span><span style="color:#3F7F7F;">groupId</span><span style="color:#008080;">&gt;</span>org.springframework<span style="color:#008080;">&lt;/</span><span style="color:#3F7F7F;">groupId</span><span style="color:#008080;">&gt;</span></p>
<p align="left">            <span style="color:#008080;">&lt;</span><span style="color:#3F7F7F;">artifactId</span><span style="color:#008080;">&gt;</span>spring-context-support<span style="color:#008080;">&lt;/</span><span style="color:#3F7F7F;">artifactId</span><span style="color:#008080;">&gt;</span></p>
<p align="left">            <span style="color:#008080;">&lt;</span><span style="color:#3F7F7F;">version</span><span style="color:#008080;">&gt;</span>3.2.13.RELEASE<span style="color:#008080;">&lt;/</span><span style="color:#3F7F7F;">version</span><span style="color:#008080;">&gt;</span></p>
<p>       <span style="color:#008080;">&lt;/</span><span style="color:#3F7F7F;">dependency</span><span style="color:#008080;">&gt;</span></p>
<p><span style="color:#008080;">本地计算机</span><span style="color:#FF0000;">C:\WINDOWS\system32\drivers\etc\hosts</span></p>
<p><span style="color:#FF0000;">增加：</span></p>
<p><span style="color:#FF0000;">10.9.148.21 master</span></p>
<p><span style="color:#FF0000;">10.9.148.26 slave1</span></p>
<p><span style="color:#FF0000;">10.9.148.23 slave2</span></p>
<p><span style="color:#FF0000;">为</span><span style="color:#FF0000;">HBase Java API</span><span style="color:#FF0000;">的配置文件做准备：</span></p>
<p align="left">   <strong><span style="color:#7F0055;">static</span></strong>{</p>
<p align="left">      <em><span style="color:#0000C0;">conf</span></em>=HBaseConfiguration.<em>create</em>();</p>
<p align="left">        <em><span style="color:#0000C0;">conf</span></em>.set(<span style="color:#2A00FF;">"hbase.rootdir"</span>,<span style="color:#2A00FF;">"hdfs://10.9.148.21:9000/hbase"</span>);</p>
<p align="left">        <em><span style="color:#0000C0;">conf</span></em>.set(<span style="color:#2A00FF;">"hbase.master"</span>,<span style="color:#2A00FF;">"10.9.148.21"</span>);</p>
<p align="left">        <em><span style="color:#0000C0;">conf</span></em>.set(<span style="color:#2A00FF;">"hbase.zookeeper.quorum"</span>,<span style="color:#2A00FF;">"10.9.148.21,10.9.148.26,10.9.148.23"</span>);</p>
<p>         }</p>
<p><span style="color:#008080;">Java</span><span style="color:#008080;">操作</span><span style="color:#008080;">HBase</span><span style="color:#008080;">集群，可以创建表删除表。当进行对表的</span><span style="color:#008080;">put</span><span style="color:#008080;">、</span><span style="color:#008080;">get</span><span style="color:#008080;">、</span><span style="color:#008080;">admin.tableExists(tablename)</span><span style="color:#008080;">等时，运行</span><span style="color:#008080;">java</span><span style="color:#008080;">代码出错。查看</span><span style="color:#008080;">zookeeper</span><span style="color:#008080;">日志发现：</span></p>
<p><span style="color:#008080;">2017-08-03 18:42:58,643 [myid:1] - INFO  [QuorumPeer[myid=1]/0.0.0.0:2181:Learner@326]- Getting a diff from the leader 0x1000000a7</span></p>
<p><span style="color:#008080;">2017-08-03 18:42:58,644 [myid:1] - INFO [QuorumPeer[myid=1]/0.0.0.0:2181:FileTxnSnapLog@240] - Snapshotting:0x1000000a7 to ./data/version-2/snapshot.1000000a7</span></p>
<p><span style="color:#008080;">2017-08-03 18:43:00,905 [myid:1] - INFO  [/10.9.148.21:3888:QuorumCnxManager$Listener@541]- Received connection request /10.9.148.23:43171</span></p>
<p><span style="color:#008080;">2017-08-03 18:43:00,908 [myid:1] - INFO [WorkerReceiver[myid=1]:FastLeaderElection@600] - Notification: 1(message format version), 3 (n.leader), 0x1000000a7 (n.zxid), 0x1 (n.round),LOOKING (n.state), 3 (n.sid), 0x1 (n.peerEpoch)
 FOLLOWING (my state)</span></p>
<p><span style="color:#008080;">2017-08-03 18:43:00,922 [myid:1] - INFO [WorkerReceiver[myid=1]:FastLeaderElection@600] - Notification: 1(message format version), 3 (n.leader), 0x1000000a7 (n.zxid), 0x2 (n.round),LOOKING (n.state), 3 (n.sid), 0x1 (n.peerEpoch)
 FOLLOWING (my state)</span></p>
<p><span style="color:#008080;">2017-08-03 18:43:23,050 [myid:1] - INFO [NIOServerCxn.Factory:0.0.0.0/0.0.0.0:2181:NIOServerCnxnFactory@192] -Accepted socket connection from /10.9.148.21:20942</span></p>
<p><span style="color:#008080;">2017-08-03 18:43:23,063 [myid:1] - INFO [NIOServerCxn.Factory:0.0.0.0/0.0.0.0:2181:ZooKeeperServer@900] - Clientattempting to establish new session at /10.9.148.21:20942</span></p>
<p><span style="color:#008080;">2017-08-03 18:43:23,065 [myid:1] - WARN [QuorumPeer[myid=1]/0.0.0.0:2181:Follower@118] - Got zxid 0x200000001expected 0x1</span></p>
<p><span style="color:#008080;">2017-08-03 18:43:23,071 [myid:1] - INFO  [CommitProcessor:1:ZooKeeperServer@645] -Established session 0x15da7b0f5d30000 with negotiated timeout 800000 for client/10.9.148.21:20942</span></p>
<p><span style="color:#008080;">2017-08-03 18:43:24,321 [myid:1] - INFO  [NIOServerCxn.Factory:0.0.0.0/0.0.0.0:2181:NIOServerCnxnFactory@192]- Accepted socket connection from /10.9.148.23:37715</span></p>
<p><span style="color:#008080;">2017-08-03 18:43:24,323 [myid:1] - INFO [NIOServerCxn.Factory:0.0.0.0/0.0.0.0:2181:ZooKeeperServer@900] - Clientattempting to establish new session at /10.9.148.23:37715</span></p>
<p><span style="color:#008080;">2017-08-03 18:43:24,326 [myid:1] - INFO  [CommitProcessor:1:ZooKeeperServer@645] -Established session 0x15da7b0f5d30001 with negotiated</span><span style="color:#FF0000;">timeout 800000</span><span style="color:#008080;"> for client /10.9.148.23:37715</span></p>
<p><span style="color:#008080;">2017-08-03 18:43:25,495 [myid:1] - INFO  [NIOServerCxn.Factory:0.0.0.0/0.0.0.0:2181:NIOServerCnxnFactory@192]- Accepted socket connection from /10.9.148.21:20951</span></p>
<p><span style="color:#008080;">2017-08-03 18:43:25,496 [myid:1] - INFO [NIOServerCxn.Factory:0.0.0.0/0.0.0.0:2181:ZooKeeperServer@900] - Clientattempting to establish new session at /10.9.148.21:20951</span></p>
<p><span style="color:#008080;">2017-08-03 18:43:25,499 [myid:1] - INFO  [CommitProcessor:1:ZooKeeperServer@645] -Established session 0x15da7b0f5d30002 with negotiated timeout 800000 for client/10.9.148.21:20951</span></p>
<p><span style="color:#FF0000;">Zoomkeeper</span><span style="color:#FF0000;">连接超时。</span></p>
<p><span style="color:#FF0000;">解决方法：</span></p>
<p><span style="color:#FF0000;">在运行</span><span style="color:#FF0000;">java</span><span style="color:#FF0000;">代码的</span><span style="color:#FF0000;">windows</span><span style="color:#FF0000;">本地计算机中：</span><span style="color:#FF0000;">C:\WINDOWS\system32\drivers\etc\hosts</span></p>
<p><span style="color:#FF0000;">增加：</span></p>
<p><span style="color:#FF0000;">10.9.148.21 Master.Hadoop</span></p>
<p><span style="color:#FF0000;">10.9.148.26 Slave1.Hadoop</span></p>
<p><span style="color:#FF0000;">10.9.148.23 Slave2.Hadoop</span></p>
<p><span style="color:#FF0000;">Master.Hadoop Slave1.Hadoop Slave2.Hadoop</span><span style="color:#FF0000;">分别为三台服务器的主机名。</span></p>
<p><span style="color:#FF0000;">如此操作成功：</span></p>
<p></p>
<p></p>
<p>常用操作：</p>
<p></p>
<p></p>
<p></p>
<p></p>
<p></p>
<p> </p>
<p>对表的操作</p>
<p></p>
<p>创建表</p>
<p></p>
<p> </p>
            </div>
                </div>