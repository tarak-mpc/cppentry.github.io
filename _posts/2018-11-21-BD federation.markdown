---
layout:     post
title:      BD federation
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>HA+HA=federation</p>
<p>core-site.xml</p>
<p>&lt;configuration&gt;<br>
<br>
&lt;property&gt;<br>
&lt;name&gt;fs.defaultFS&lt;/name&gt;<br>
&lt;value&gt;viewfs:///&lt;/value&gt;<br>
&lt;/property&gt;<br>
<br>
&lt;property&gt;<br>
&lt;name&gt;fs.viewfs.mounttable.default.link./bi&lt;/name&gt;<br>
&lt;value&gt;hdfs://bi/&lt;/value&gt;<br>
&lt;/property&gt;<br>
<br>
&lt;property&gt;<br>
&lt;name&gt;fs.viewfs.mounttable.default.link./dt&lt;/name&gt;<br>
&lt;value&gt;hdfs://dt/&lt;/value&gt;<br>
&lt;/property&gt;<br>
<br>
&lt;!-- 指定hadoop临时目录 --&gt;<br>
&lt;property&gt;<br>
&lt;name&gt;hadoop.tmp.dir&lt;/name&gt;<br>
&lt;value&gt;/home/hadoop/apps/hdpdata/&lt;/value&gt;<br>
&lt;/property&gt;<br>
<br>
&lt;!-- 指定zookeeper地址 --&gt;<br>
&lt;property&gt;<br>
&lt;name&gt;ha.zookeeper.quorum&lt;/name&gt;<br>
&lt;value&gt;mini5:2181,mini6:2181,mini7:2181&lt;/value&gt;<br>
&lt;/property&gt;<br>
&lt;/configuration&gt;⏎                 <br>
</p>
<p><br>
</p>
<p>hdfs-site.xml</p>
<p><br>
</p>
<p>&lt;configuration&gt;<br>
&lt;!--指定hdfs的nameservice为bi，需要和core-site.xml中的保持一致 --&gt;<br>
&lt;property&gt;<br>
&lt;name&gt;dfs.nameservices&lt;/name&gt;<br>
&lt;value&gt;bi,dt&lt;/value&gt;<br>
&lt;/property&gt;<br>
&lt;!-- bi下面有两个NameNode，分别是nn1，nn2 --&gt;<br>
&lt;property&gt;<br>
&lt;name&gt;dfs.ha.namenodes.bi&lt;/name&gt;<br>
&lt;value&gt;nn1,nn2&lt;/value&gt;<br>
&lt;/property&gt;<br>
<br>
&lt;property&gt;<br>
&lt;name&gt;dfs.ha.namenodes.dt&lt;/name&gt;<br>
&lt;value&gt;nn3,nn4&lt;/value&gt;<br>
&lt;/property&gt;<br>
<br>
&lt;!-- bi的RPC通信地址 --&gt;<br>
&lt;property&gt;<br>
&lt;name&gt;dfs.namenode.rpc-address.bi.nn1&lt;/name&gt;<br>
&lt;value&gt;mini1:9000&lt;/value&gt;<br>
&lt;/property&gt;<br>
&lt;!-- nn1的http通信地址 --&gt;<br>
&lt;property&gt;<br>
&lt;name&gt;dfs.namenode.http-address.bi.nn1&lt;/name&gt;<br>
&lt;value&gt;mini1:50070&lt;/value&gt;<br>
&lt;/property&gt;<br>
&lt;!-- nn2的RPC通信地址 --&gt;<br>
&lt;property&gt;<br>
&lt;name&gt;dfs.namenode.rpc-address.bi.nn2&lt;/name&gt;<br>
&lt;value&gt;mini2:9000&lt;/value&gt;<br>
&lt;/property&gt;<br>
&lt;!-- nn2的http通信地址 --&gt;<br>
&lt;property&gt;<br>
&lt;name&gt;dfs.namenode.http-address.bi.nn2&lt;/name&gt;<br>
&lt;value&gt;mini2:50070&lt;/value&gt;<br>
&lt;/property&gt;<br>
<br>
&lt;!-- dt的RPC通信地址 --&gt;<br>
&lt;property&gt;<br>
&lt;name&gt;dfs.namenode.rpc-address.dt.nn3&lt;/name&gt;<br>
&lt;value&gt;mini3:9000&lt;/value&gt;<br>
&lt;/property&gt;<br>
&lt;!-- nn1的http通信地址 --&gt;<br>
&lt;property&gt;<br>
&lt;name&gt;dfs.namenode.http-address.dt.nn3&lt;/name&gt;<br>
&lt;value&gt;mini3:50070&lt;/value&gt;<br>
&lt;/property&gt;<br>
&lt;!-- nn2的RPC通信地址 --&gt;<br>
&lt;property&gt;<br>
&lt;name&gt;dfs.namenode.rpc-address.dt.nn4&lt;/name&gt;<br>
&lt;value&gt;mini4:9000&lt;/value&gt;<br>
&lt;/property&gt;<br>
&lt;!-- nn2的http通信地址 --&gt;<br>
&lt;property&gt;<br>
&lt;name&gt;dfs.namenode.http-address.dt.nn4&lt;/name&gt;<br>
&lt;value&gt;mini4:50070&lt;/value&gt;<br>
&lt;/property&gt;<br>
<br>
<br>
&lt;!-- 指定NameNode的edits元数据在JournalNode上的存放位置 --&gt;<br>
&lt;!--一下property项的配置，不能都配 --&gt;<br>
<br>
&lt;!--  在bi名称空间的两个namenode中用如下配置  --&gt;<br>
&lt;property&gt;<br>
&lt;name&gt;dfs.namenode.shared.edits.dir&lt;/name&gt;<br>
&lt;value&gt;qjournal://mini5:8485;mini6:8485;mini7:8485/bi&lt;/value&gt;<br>
&lt;/property&gt;<br>
&lt;!--  在dt名称空间的两个namenode中，用如下配置--&gt;<br>
&lt;property&gt;<br>
&lt;name&gt;dfs.namenode.shared.edits.dir&lt;/name&gt;<br>
&lt;value&gt;qjournal://mini5:8485;mini6:8485;mini7:8485/dt&lt;/value&gt;<br>
&lt;/property&gt;<br>
<br>
<br>
&lt;!-- 指定JournalNode在本地磁盘存放数据的位置 --&gt;<br>
&lt;property&gt;<br>
&lt;name&gt;dfs.journalnode.edits.dir&lt;/name&gt;<br>
&lt;value&gt;/home/hadoop/apps/hdpdata/journaldata&lt;/value&gt;<br>
&lt;/property&gt;<br>
&lt;!-- 开启NameNode失败自动切换 --&gt;<br>
&lt;property&gt;<br>
&lt;name&gt;dfs.ha.automatic-failover.enabled&lt;/name&gt;<br>
&lt;value&gt;true&lt;/value&gt;<br>
&lt;/property&gt;<br>
<br>
<br>
&lt;!-- 配置失败自动切换实现方式 --&gt;<br>
&lt;property&gt;<br>
&lt;name&gt;dfs.client.failover.proxy.provider.bi&lt;/name&gt;<br>
&lt;value&gt;org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider&lt;/value&gt;<br>
&lt;/property&gt;<br>
<br>
&lt;property&gt;<br>
&lt;name&gt;dfs.client.failover.proxy.provider.dt&lt;/name&gt;<br>
&lt;value&gt;org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider&lt;/value&gt;<br>
&lt;/property&gt;<br>
<br>
&lt;!-- 配置隔离机制方法，多个机制用换行分割，即每个机制暂用一行--&gt;<br>
&lt;property&gt;<br>
&lt;name&gt;dfs.ha.fencing.methods&lt;/name&gt;<br>
&lt;value&gt;<br>
sshfence<br>
shell(/bin/true)<br>
&lt;/value&gt;<br>
&lt;/property&gt;<br>
&lt;!-- 使用sshfence隔离机制时需要ssh免登陆 --&gt;<br>
&lt;property&gt;<br>
&lt;name&gt;dfs.ha.fencing.ssh.private-key-files&lt;/name&gt;<br>
&lt;value&gt;/home/hadoop/.ssh/id_rsa&lt;/value&gt;<br>
&lt;/property&gt;<br>
&lt;!-- 配置sshfence隔离机制超时时间 --&gt;<br>
&lt;property&gt;<br>
&lt;name&gt;dfs.ha.fencing.ssh.connect-timeout&lt;/name&gt;<br>
&lt;value&gt;30000&lt;/value&gt;<br>
&lt;/property&gt;<br>
&lt;/configuration&gt;⏎            <br>
<br>
</p>
<p>mapred-site.xml</p>
<p><br>
</p>
<p>&lt;configuration&gt;<br>
&lt;!-- 指定mr框架为yarn方式 --&gt;<br>
&lt;property&gt;<br>
&lt;name&gt;mapreduce.framework.name&lt;/name&gt;<br>
&lt;value&gt;yarn&lt;/value&gt;<br>
&lt;/property&gt;<br>
&lt;/configuration&gt;    <br>
</p>
<p><br>
</p>
<p>yarn-site.xml</p>
<p><br>
</p>
<p>&lt;configuration&gt;<br>
&lt;!-- 开启RM高可用 --&gt;<br>
&lt;property&gt;<br>
&lt;name&gt;yarn.resourcemanager.ha.enabled&lt;/name&gt;<br>
&lt;value&gt;true&lt;/value&gt;<br>
&lt;/property&gt;<br>
&lt;!-- 指定RM的cluster id --&gt;<br>
&lt;property&gt;<br>
&lt;name&gt;yarn.resourcemanager.cluster-id&lt;/name&gt;<br>
&lt;value&gt;yrc&lt;/value&gt;<br>
&lt;/property&gt;<br>
&lt;!-- 指定RM的名字 --&gt;<br>
&lt;property&gt;<br>
&lt;name&gt;yarn.resourcemanager.ha.rm-ids&lt;/name&gt;<br>
&lt;value&gt;rm1,rm2&lt;/value&gt;<br>
&lt;/property&gt;<br>
&lt;!-- 分别指定RM的地址 --&gt;<br>
&lt;property&gt;<br>
&lt;name&gt;yarn.resourcemanager.hostname.rm1&lt;/name&gt;<br>
&lt;value&gt;mini3&lt;/value&gt;<br>
&lt;/property&gt;<br>
&lt;property&gt;<br>
&lt;name&gt;yarn.resourcemanager.hostname.rm2&lt;/name&gt;<br>
&lt;value&gt;mini4&lt;/value&gt;<br>
&lt;/property&gt;<br>
&lt;!-- 指定zk集群地址 --&gt;<br>
&lt;property&gt;<br>
&lt;name&gt;yarn.resourcemanager.zk-address&lt;/name&gt;<br>
&lt;value&gt;mini5:2181,mini6:2181,mini7:2181&lt;/value&gt;<br>
&lt;/property&gt;<br>
&lt;property&gt;<br>
&lt;name&gt;yarn.nodemanager.aux-services&lt;/name&gt;<br>
&lt;value&gt;mapreduce_shuffle&lt;/value&gt;<br>
&lt;/property&gt;<br>
&lt;/configuration&gt;⏎               <br>
</p>
<p><br>
</p>
<p>因为是namenode的federation所以yarn和HA的一样</p>
<p>启动 <br>
</p>
<p>先启动zookeeper集群<br>
再在5/6/7上启动journalnode<br>
hadoop-daemon.sh start journalnode<br>
<br>
<br>
在bi下nn1上  <br>
hdfs namenode -format –clusterID xxx<br>
hdfs zkfc -formatZK<br>
拷贝元数据目录到standby(nn2)<br>
<br>
在dt下nn3上  <br>
hdfs namenode -format –clusterID xxx   ###clusterID必须与bi的相同，否则为两个集群<br>
hdfs zkfc -formatZK<br>
拷贝元数据目录到standby(nn4)<br>
<br>
在bi下nn1上<br>
sbin/start-dfs.sh,就会启动所有的NN<br>
<br>
在resoucemanager配置的主机上启动yarn<br>
sbin/start-yarn.sh<br>
<br>
<br>
</p>
<p><br>
</p>
<p><br>
</p>
            </div>
                </div>