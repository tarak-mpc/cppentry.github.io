---
layout:     post
title:      Hadoop+Hbase+Spark集群配置—Hadoop HA安装
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/Dyforever_com/article/details/60959422				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div>﻿﻿</div>
<p>解压、改名 <br>
tar -zxvf hadoop-2.6.0.tar.gz<br>
mv hadoop-2.6.0 hadoop <br>
验证hadoop安装成功 <br>
hadoop version </p>
<p>修改hadoop配置文件 <br>
[1] <br>
vi /usr/hadoop/hadoop/etc/hadoop/core-site.xml <br>
############################################### <br>
&lt;?xml version="1.0" encoding="UTF-8"?&gt; <br>
&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt; <br>
&lt;configuration&gt; <br>
  &lt;!-- 开启垃圾箱功能,1440分钟 --&gt; <br>
  &lt;property&gt; <br>
    &lt;name&gt;fs.trash.interval&lt;/name&gt; <br>
    &lt;value&gt;1440&lt;/value&gt; <br>
  &lt;/property&gt; </p>
<p>  &lt;!-- 指定hdfs的nameservice为ns1,是NameNode的URI。hdfs://主机名:端口/ --&gt; <br>
  &lt;property&gt; <br>
    &lt;name&gt;fs.defaultFS&lt;/name&gt; <br>
    &lt;value&gt;hdfs://gagcluster:8020&lt;/value&gt; <br>
  &lt;/property&gt; </p>
<p>  &lt;property&gt; <br>
    &lt;name&gt;io.file.buffer.size&lt;/name&gt; <br>
    &lt;value&gt;131072&lt;/value&gt; <br>
  &lt;/property&gt; </p>
<p>  &lt;!-- 指定hadoop临时目录 --&gt; <br>
  &lt;property&gt; <br>
    &lt;name&gt;hadoop.tmp.dir&lt;/name&gt; <br>
    &lt;value&gt;/usr/hadoop/storage/hadoop/tmp&lt;/value&gt; <br>
  &lt;/property&gt; </p>
<p>  &lt;!--指定可以在任何IP访问--&gt; <br>
  &lt;property&gt; <br>
    &lt;name&gt;hadoop.proxyuser.root.hosts&lt;/name&gt; <br>
    &lt;value&gt;*&lt;/value&gt; <br>
  &lt;/property&gt; </p>
<p>  &lt;!--指定所有用户可以访问--&gt; <br>
  &lt;property&gt; <br>
    &lt;name&gt;hadoop.proxyuser.root.groups&lt;/name&gt; <br>
    &lt;value&gt;*&lt;/value&gt; <br>
  &lt;/property&gt; </p>
<p>  &lt;!-- 指定zookeeper地址 --&gt; <br>
  &lt;property&gt; <br>
    &lt;name&gt;ha.zookeeper.quorum&lt;/name&gt; <br>
    &lt;value&gt;SA01:2181,SA02:2181,SA03:2181&lt;/value&gt; <br>
  &lt;/property&gt; <br>
&lt;/configuration&gt; <br>
################################################# </p>
<p>[2] <br>
vi /usr/hadoop/hadoop/etc/hadoop/hdfs-site.xml <br>
################################################ <br>
&lt;?xml version="1.0" encoding="UTF-8"?&gt; <br>
&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt; <br>
&lt;configuration&gt; <br>
&lt;!--节点黑名单列表文件，用于下线hadoop节点 --&gt; <br>
&lt;property&gt; <br>
  &lt;name&gt;dfs.hosts.exclude&lt;/name&gt; <br>
  &lt;value&gt;/usr/hadoop/hadoop/etc/hadoop/exclude&lt;/value&gt; <br>
&lt;/property&gt; </p>
<p>&lt;!--指定hdfs的block大小64M --&gt; <br>
  &lt;property&gt; <br>
    &lt;name&gt;dfs.block.size&lt;/name&gt; <br>
    &lt;value&gt;67108864&lt;/value&gt; <br>
  &lt;/property&gt; </p>
<p>&lt;!--指定hdfs的nameservice为ns1，需要和core-site.xml中的保持一致 --&gt; <br>
  &lt;property&gt; <br>
    &lt;name&gt;dfs.nameservices&lt;/name&gt; <br>
    &lt;value&gt;gagcluster&lt;/value&gt; <br>
  &lt;/property&gt; </p>
<p>&lt;!-- ns1下面有两个NameNode，分别是nn1，nn2 --&gt; <br>
  &lt;property&gt; <br>
    &lt;name&gt;dfs.ha.namenodes.gagcluster&lt;/name&gt; <br>
    &lt;value&gt;nn1,nn2&lt;/value&gt; <br>
  &lt;/property&gt; </p>
<p>&lt;!-- nn1的RPC通信地址 --&gt; <br>
  &lt;property&gt; <br>
    &lt;name&gt;dfs.namenode.rpc-address.gagcluster.nn1&lt;/name&gt; <br>
    &lt;value&gt;SA01:8020&lt;/value&gt; <br>
  &lt;/property&gt; </p>
<p>&lt;!-- nn1的http通信地址 --&gt; <br>
  &lt;property&gt; <br>
    &lt;name&gt;dfs.namenode.http-address.gagcluster.nn1&lt;/name&gt; <br>
    &lt;value&gt;SA01:50070&lt;/value&gt; <br>
  &lt;/property&gt; </p>
<p>&lt;!-- nn2的RPC通信地址 --&gt; <br>
  &lt;property&gt; <br>
    &lt;name&gt;dfs.namenode.rpc-address.gagcluster.nn2&lt;/name&gt; <br>
    &lt;value&gt;SA02:8020&lt;/value&gt; <br>
  &lt;/property&gt; </p>
<p>&lt;!-- nn2的http通信地址 --&gt; <br>
  &lt;property&gt; <br>
    &lt;name&gt;dfs.namenode.http-address.gagcluster.nn2&lt;/name&gt; <br>
    &lt;value&gt;SA02:50070&lt;/value&gt; <br>
  &lt;/property&gt; </p>
<p>&lt;!-- 指定NameNode的元数据在JournalNode上的存放位置 --&gt; <br>
  &lt;property&gt; <br>
    &lt;name&gt;dfs.namenode.shared.edits.dir&lt;/name&gt; <br>
    &lt;value&gt;qjournal://SA01:8485;SA02:8485;SA03:8485/gagcluster&lt;/value&gt; <br>
  &lt;/property&gt; </p>
<p>&lt;!-- 配置失败自动切换实现方式 --&gt; <br>
  &lt;property&gt; <br>
    &lt;name&gt;dfs.client.failover.proxy.provider.gagcluster&lt;/name&gt; <br>
    &lt;value&gt;org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider&lt;/value&gt;
<br>
  &lt;/property&gt; </p>
<p>&lt;!-- 配置隔离机制 --&gt; <br>
  &lt;property&gt; <br>
    &lt;name&gt;dfs.ha.fencing.methods&lt;/name&gt; <br>
    &lt;value&gt;sshfence&lt;/value&gt; <br>
  &lt;/property&gt; </p>
<p>&lt;!-- 使用隔离机制时需要ssh免密码登陆 --&gt; <br>
  &lt;property&gt; <br>
    &lt;name&gt;dfs.ha.fencing.ssh.private-key-files&lt;/name&gt; <br>
    &lt;value&gt;/root/.ssh/id_rsa&lt;/value&gt; <br>
  &lt;/property&gt; </p>
<p>&lt;!-- 指定NameNode的元数据在JournalNode上的存放位置 --&gt; <br>
  &lt;property&gt; <br>
    &lt;name&gt;dfs.journalnode.edits.dir&lt;/name&gt; <br>
    &lt;value&gt;/usr/hadoop/storage/hadoop/journal&lt;/value&gt; <br>
  &lt;/property&gt; </p>
<p>&lt;!--指定支持高可用自动切换机制--&gt; <br>
  &lt;property&gt; <br>
    &lt;name&gt;dfs.ha.automatic-failover.enabled&lt;/name&gt; <br>
    &lt;value&gt;true&lt;/value&gt; <br>
  &lt;/property&gt; </p>
<p>&lt;!--指定namenode名称空间的存储地址--&gt; <br>
  &lt;property&gt; <br>
    &lt;name&gt;dfs.namenode.name.dir&lt;/name&gt;    <br>
    &lt;value&gt;/usr/hadoop/storage/hadoop/name&lt;/value&gt; <br>
  &lt;/property&gt; </p>
<p>&lt;!--指定datanode数据存储地址--&gt; <br>
  &lt;property&gt; <br>
    &lt;name&gt;dfs.datanode.data.dir&lt;/name&gt; <br>
    &lt;value&gt;file:/usr/hadoop/storage/hadoop/data&lt;/value&gt; <br>
  &lt;/property&gt; </p>
<p>&lt;!--指定数据冗余份数--&gt; <br>
  &lt;property&gt; <br>
    &lt;name&gt;dfs.replication&lt;/name&gt; <br>
    &lt;value&gt;2&lt;/value&gt; <br>
  &lt;/property&gt; </p>
<p>&lt;!--指定可以通过web访问hdfs目录--&gt; <br>
  &lt;property&gt; <br>
    &lt;name&gt;dfs.webhdfs.enabled&lt;/name&gt; <br>
    &lt;value&gt;true&lt;/value&gt; <br>
  &lt;/property&gt; </p>
<p>&lt;!--保证数据恢复 --&gt; <br>
  &lt;property&gt; <br>
    &lt;name&gt;dfs.journalnode.http-address&lt;/name&gt; <br>
    &lt;value&gt;0.0.0.0:8480&lt;/value&gt; <br>
  &lt;/property&gt; </p>
<p>  &lt;property&gt; <br>
    &lt;name&gt;dfs.journalnode.rpc-address&lt;/name&gt; <br>
    &lt;value&gt;0.0.0.0:8485&lt;/value&gt; <br>
  &lt;/property&gt; </p>
<p>  &lt;property&gt; <br>
    &lt;name&gt;ha.zookeeper.quorum&lt;/name&gt; <br>
    &lt;value&gt;SA01:2181,SA02:2181,SA03:2181&lt;/value&gt; <br>
  &lt;/property&gt; <br>
&lt;/configuration&gt; <br>
################################################# <br>
[3] <br>
cp /usr/hadoop/hadoop/etc/hadoop/mapred-site.xml.template /usr/hadoop/hadoop/etc/hadoop/mapred-site.xml<br>
vi /usr/hadoop/hadoop/etc/hadoop/mapred-site.xml <br>
################################################# <br>
&lt;?xml version="1.0"?&gt; <br>
&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt; <br>
&lt;configuration&gt; <br>
&lt;!-- 配置MapReduce运行于yarn中 --&gt; <br>
  &lt;property&gt; <br>
    &lt;name&gt;mapreduce.framework.name&lt;/name&gt; <br>
    &lt;value&gt;yarn&lt;/value&gt; <br>
  &lt;/property&gt; </p>
<p>&lt;!-- 配置 MapReduce JobHistory Server 地址 ，默认端口10020 --&gt; <br>
  &lt;property&gt; <br>
    &lt;name&gt;mapreduce.jobhistory.address&lt;/name&gt; <br>
    &lt;value&gt;0.0.0.0:10020&lt;/value&gt; <br>
  &lt;/property&gt; </p>
<p>&lt;!-- 配置 MapReduce JobHistory Server web ui 地址， 默认端口19888 --&gt; <br>
  &lt;property&gt; <br>
    &lt;name&gt;mapreduce.jobhistory.webapp.address&lt;/name&gt; <br>
    &lt;value&gt;0.0.0.0:19888&lt;/value&gt; <br>
  &lt;/property&gt; <br>
&lt;/configuration&gt; <br>
################################################### </p>
<p>[4] <br>
vi /usr/hadoop/hadoop/etc/hadoop/yarn-site.xml <br>
################################################### <br>
&lt;?xml version="1.0"?&gt; <br>
&lt;configuration&gt; <br>
&lt;!--日志聚合功能--&gt;  <br>
  &lt;property&gt; <br>
     &lt;name&gt;yarn.log-aggregation-enable&lt;/name&gt; <br>
     &lt;value&gt;true&lt;/value&gt; <br>
  &lt;/property&gt; </p>
<p>&lt;!--在HDFS上聚合的日志最长保留多少秒。3天--&gt;  <br>
  &lt;property&gt; <br>
     &lt;name&gt;yarn.log-aggregation.retain-seconds&lt;/name&gt; <br>
     &lt;value&gt;259200&lt;/value&gt; <br>
  &lt;/property&gt; </p>
<p>&lt;!--rm失联后重新链接的时间--&gt;  <br>
  &lt;property&gt;  <br>
     &lt;name&gt;yarn.resourcemanager.connect.retry-interval.ms&lt;/name&gt;  <br>
     &lt;value&gt;2000&lt;/value&gt;  <br>
  &lt;/property&gt; </p>
<p>&lt;!--开启resource manager HA,默认为false--&gt;  <br>
  &lt;property&gt;  <br>
     &lt;name&gt;yarn.resourcemanager.ha.enabled&lt;/name&gt;  <br>
     &lt;value&gt;true&lt;/value&gt;  <br>
  &lt;/property&gt;  </p>
<p>&lt;!--配置resource manager --&gt; <br>
  &lt;property&gt; <br>
    &lt;name&gt;yarn.resourcemanager.ha.rm-ids&lt;/name&gt; <br>
    &lt;value&gt;rm1,rm2&lt;/value&gt; <br>
  &lt;/property&gt; </p>
<p>  &lt;property&gt; <br>
    &lt;name&gt;ha.zookeeper.quorum&lt;/name&gt; <br>
    &lt;value&gt;SA01:2181,SA02:2181,SA03:2181&lt;/value&gt;  <br>
  &lt;/property&gt; <br>
   <br>
&lt;!--开启故障自动切换--&gt;  <br>
  &lt;property&gt;  <br>
     &lt;name&gt;yarn.resourcemanager.ha.automatic-failover.enabled&lt;/name&gt;  <br>
     &lt;value&gt;true&lt;/value&gt;  <br>
  &lt;/property&gt;  </p>
<p>  &lt;property&gt; <br>
    &lt;name&gt;yarn.resourcemanager.hostname.rm1&lt;/name&gt; <br>
    &lt;value&gt;SA01&lt;/value&gt; <br>
  &lt;/property&gt; <br>
                      <br>
  &lt;property&gt; <br>
     &lt;name&gt;yarn.resourcemanager.hostname.rm2&lt;/name&gt; <br>
     &lt;value&gt;SA02&lt;/value&gt; <br>
  &lt;/property&gt; </p>
<p>&lt;!--在namenode1上配置rm1,在namenode2上配置rm2,注意：一般都喜欢把配置好的文件远程复制到其它机器上，但这个在YARN的另一个机器上一定要修改--&gt; 
<br>
  &lt;property&gt;  <br>
    &lt;name&gt;yarn.resourcemanager.ha.id&lt;/name&gt;  <br>
    &lt;value&gt;rm1&lt;/value&gt; <br>
  &lt;/property&gt;  </p>
<p>&lt;!--开启自动恢复功能--&gt;  <br>
  &lt;property&gt; <br>
    &lt;name&gt;yarn.resourcemanager.recovery.enabled&lt;/name&gt;  <br>
    &lt;value&gt;true&lt;/value&gt;  <br>
  &lt;/property&gt; </p>
<p>&lt;!--配置与zookeeper的连接地址--&gt;  <br>
  &lt;property&gt;  <br>
    &lt;name&gt;yarn.resourcemanager.zk-state-store.address&lt;/name&gt;  <br>
    &lt;value&gt;SA01:2181,SA02:2181,SA03:2181&lt;/value&gt; <br>
  &lt;/property&gt;  </p>
<p>  &lt;property&gt;  <br>
    &lt;name&gt;yarn.resourcemanager.store.class&lt;/name&gt;  <br>
    &lt;value&gt;org.apache.hadoop.yarn.server.resourcemanager.recovery.ZKRMStateStore&lt;/value&gt; 
<br>
  &lt;/property&gt;  </p>
<p>  &lt;property&gt; <br>
    &lt;name&gt;yarn.resourcemanager.zk-address&lt;/name&gt; <br>
    &lt;value&gt;SA01:2181,SA02:2181,SA03:2181&lt;/value&gt; <br>
  &lt;/property&gt; </p>
<p>  &lt;property&gt;  <br>
    &lt;name&gt;yarn.resourcemanager.cluster-id&lt;/name&gt;  <br>
    &lt;value&gt;gagcluster-yarn&lt;/value&gt;  <br>
  &lt;/property&gt;  </p>
<p>&lt;!--schelduler失联等待连接时间--&gt;  <br>
  &lt;property&gt;  <br>
    &lt;name&gt;yarn.app.mapreduce.am.scheduler.connection.wait.interval-ms&lt;/name&gt;  <br>
    &lt;value&gt;5000&lt;/value&gt;  <br>
  &lt;/property&gt;  </p>
<p>&lt;!--配置rm1--&gt;  <br>
  &lt;property&gt;  <br>
    &lt;name&gt;yarn.resourcemanager.address.rm1&lt;/name&gt;  <br>
    &lt;value&gt;SA01:8132&lt;/value&gt;  <br>
  &lt;/property&gt;  </p>
<p>  &lt;property&gt;  <br>
    &lt;name&gt;yarn.resourcemanager.scheduler.address.rm1&lt;/name&gt;  <br>
    &lt;value&gt;SA01:8130&lt;/value&gt;  <br>
  &lt;/property&gt;  </p>
<p>  &lt;property&gt;  <br>
    &lt;name&gt;yarn.resourcemanager.webapp.address.rm1&lt;/name&gt;  <br>
    &lt;value&gt;SA01:8188&lt;/value&gt;  <br>
  &lt;/property&gt;  </p>
<p>  &lt;property&gt; <br>
    &lt;name&gt;yarn.resourcemanager.resource-tracker.address.rm1&lt;/name&gt;  <br>
    &lt;value&gt;SA01:8131&lt;/value&gt;  <br>
  &lt;/property&gt;  </p>
<p>  &lt;property&gt;  <br>
    &lt;name&gt;yarn.resourcemanager.admin.address.rm1&lt;/name&gt;  <br>
    &lt;value&gt;SA01:8033&lt;/value&gt;  <br>
  &lt;/property&gt;  </p>
<p>  &lt;property&gt;  <br>
    &lt;name&gt;yarn.resourcemanager.ha.admin.address.rm1&lt;/name&gt;  <br>
    &lt;value&gt;SA01:23142&lt;/value&gt;  <br>
  &lt;/property&gt;  </p>
<p>&lt;!--配置rm2--&gt;  <br>
  &lt;property&gt;  <br>
    &lt;name&gt;yarn.resourcemanager.address.rm2&lt;/name&gt;  <br>
    &lt;value&gt;SA02:8132&lt;/value&gt;  <br>
  &lt;/property&gt;  </p>
<p>  &lt;property&gt;  <br>
    &lt;name&gt;yarn.resourcemanager.scheduler.address.rm2&lt;/name&gt;  <br>
    &lt;value&gt;SA02:8130&lt;/value&gt;  <br>
  &lt;/property&gt;  </p>
<p>  &lt;property&gt;  <br>
    &lt;name&gt;yarn.resourcemanager.webapp.address.rm2&lt;/name&gt;  <br>
    &lt;value&gt;SA02:8188&lt;/value&gt;  <br>
  &lt;/property&gt;  </p>
<p>  &lt;property&gt; <br>
    &lt;name&gt;yarn.resourcemanager.resource-tracker.address.rm2&lt;/name&gt;  <br>
    &lt;value&gt;SA02:8131&lt;/value&gt;  <br>
  &lt;/property&gt;  </p>
<p>  &lt;property&gt;  <br>
    &lt;name&gt;yarn.resourcemanager.admin.address.rm2&lt;/name&gt;  <br>
    &lt;value&gt;SA02:8033&lt;/value&gt;  <br>
  &lt;/property&gt;  </p>
<p>  &lt;property&gt;  <br>
    &lt;name&gt;yarn.resourcemanager.ha.admin.address.rm2&lt;/name&gt;  <br>
    &lt;value&gt;SA02:23142&lt;/value&gt;  <br>
  &lt;/property&gt;  </p>
<p>  &lt;property&gt;  <br>
    &lt;name&gt;yarn.nodemanager.aux-services&lt;/name&gt;  <br>
    &lt;value&gt;mapreduce_shuffle&lt;/value&gt;  <br>
  &lt;/property&gt;  </p>
<p>  &lt;property&gt;  <br>
    &lt;name&gt;yarn.nodemanager.aux-services.mapreduce.shuffle.class&lt;/name&gt;  <br>
    &lt;value&gt;org.apache.hadoop.mapred.ShuffleHandler&lt;/value&gt;  <br>
  &lt;/property&gt;  </p>
<p>  &lt;property&gt;  <br>
    &lt;name&gt;yarn.nodemanager.local-dirs&lt;/name&gt;  <br>
    &lt;value&gt;/usr/hadoop/storage/yarn/local&lt;/value&gt;  <br>
  &lt;/property&gt;  </p>
<p>  &lt;property&gt;  <br>
    &lt;name&gt;yarn.nodemanager.log-dirs&lt;/name&gt;  <br>
    &lt;value&gt;/usr/hadoop/storage/yarn/logs&lt;/value&gt;  <br>
  &lt;/property&gt;  </p>
<p>  &lt;property&gt;  <br>
    &lt;name&gt;mapreduce.shuffle.port&lt;/name&gt;  <br>
    &lt;value&gt;23080&lt;/value&gt;  <br>
  &lt;/property&gt;  </p>
<p>&lt;!--故障处理类--&gt;  <br>
  &lt;property&gt;  <br>
    &lt;name&gt;yarn.client.failover-proxy-provider&lt;/name&gt;  <br>
    &lt;value&gt;org.apache.hadoop.yarn.client.ConfiguredRMFailoverProxyProvider&lt;/value&gt; 
<br>
  &lt;/property&gt;  </p>
<p>  &lt;property&gt; <br>
      &lt;name&gt;yarn.resourcemanager.ha.automatic-failover.zk-base-path&lt;/name&gt; <br>
      &lt;value&gt;/yarn-leader-election&lt;/value&gt; <br>
  &lt;/property&gt; <br>
&lt;/configuration&gt; </p>
<p>####################################################### </p>
<p>配置DataNode节点 <br>
vi /usr/hadoop/hadoop/etc/hadoop/slaves <br>
#######################################################  <br>
slave141 <br>
slave142 <br>
slave143<br>
Slave144<br>
Slave145 </p>
<p>vi /usr/hadoop/hadoop/etc/hadoop/*.env.sh（三个env.sh文件）<br>
####################################################### <br>
export JAVA_HOME=/usr/hadoop/jdk</p>
<p>创建exclude文件，用于以后下线hadoop节点 <br>
touch /usr/hadoop/hadoop/etc/hadoop/exclude </p>
<p>同步hadoop工程到hadoop002~005机器上面 <br>
for ip in `seq 140 145`;do scp -r /usr/hadoop/hadoop slave$ip:/usr/hadoop/;done </p>
<p>修改nn2配置文件yarn-site.xml <br>
##################################################### <br>
  &lt;property&gt; <br>
    &lt;name&gt;yarn.resourcemanager.ha.id&lt;/name&gt;  <br>
    &lt;value&gt;rm2&lt;/value&gt; <br>
  &lt;/property&gt; <br>
##################################################### <br></p>
            </div>
                </div>