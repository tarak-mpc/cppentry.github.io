---
layout:     post
title:      centos7在hadoop2.7.6和jdk1.8上安装hbase2.0.1的方法
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>centos7在hadoop2.7.6和jdk1.8上安装hbase2.0.1的方法<br>
依赖hadoop 环境，我这边的版本是hadoop-2.7.6</p>
<p>选择hbase2.0.1版本的时候，去官网查看支持的hadoop版本</p>
<p>tar -zxvf  hbase-2.0.1-bin.tar.gz<br>
mv hbase-2.0.1 hbase</p>
<p>3、chwon -R hadoop:hadoop  hbase //赋于hadoop权限</p>
<p>主机/etc/hosts  #127.0.0.1 去掉127.0.0.1这一行</p>
<pre><code>[hadoop3@master ~]$ cat /etc/hosts
#127.0.0.1  core  localhost.localdomain localhost
::1    localhost
192.168.145.200  master
192.168.145.201  slave1
192.168.145.202  slave2

</code></pre>
<p>zookeeper/conf/zoo.cfg 内容：</p>
<pre><code># The number of milliseconds of each tick
tickTime=2000
# The number of ticks that the initial 
# synchronization phase can take
initLimit=10
# The number of ticks that can pass between 
# sending a request and getting an acknowledgement
syncLimit=5
# the directory where the snapshot is stored.
# do not use /tmp for storage, /tmp here is just 
# example sakes.
dataDir=/home/hadoop3/data/zookeeper/zkdata
dataLogDir=/home/hadoop3/data/zookeeper/zkdatalog
# the port at which the clients will connect
clientPort=2181
# the maximum number of client connections.
# increase this if you need to handle more clients
#maxClientCnxns=60
#
# Be sure to read the maintenance section of the 
# administrator guide before turning on autopurge.
#
# http://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_maintenance
#
# The number of snapshots to retain in dataDir
#autopurge.snapRetainCount=3
# Purge task interval in hours
# Set to "0" to disable auto purge feature
#autopurge.purgeInterval=1
server.1=master:2888:3888
server.2=slave1:2888:3888
server.3=slave2:2888:3888
</code></pre>
<p>hadoop2.7.6   /etc/hadoop/core-site.xml</p>
<pre><code>&lt;configuration&gt;
    &lt;property&gt;
	  &lt;name&gt;fs.defaultFS&lt;/name&gt;
	  &lt;value&gt;hdfs://mycluster&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
	  &lt;name&gt;hadoop.tmp.dir&lt;/name&gt;
	  &lt;value&gt;/home/hadoop3/data/tmp&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
	   &lt;name&gt;ha.zookeeper.quorum&lt;/name&gt;
	   &lt;value&gt;master:2181,slave1:2181,slave2:2181&lt;/value&gt;
	&lt;/property&gt;
&lt;/configuration&gt;
</code></pre>
<p>hdfs-site.xml</p>
<pre><code>&lt;configuration&gt;
    &lt;property&gt;
	  &lt;name&gt;dfs.nameservices&lt;/name&gt;
	  &lt;value&gt;mycluster&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
      &lt;name&gt;dfs.permissions.enabled&lt;/name&gt;
      &lt;value&gt;false&lt;/value&gt;
    &lt;/property&gt;
	&lt;property&gt;
	  &lt;name&gt;dfs.ha.namenodes.mycluster&lt;/name&gt;
	  &lt;value&gt;nn1,nn2&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
	  &lt;name&gt;dfs.namenode.rpc-address.mycluster.nn1&lt;/name&gt;
	  &lt;value&gt;master:8020&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
	  &lt;name&gt;dfs.namenode.rpc-address.mycluster.nn2&lt;/name&gt;
	  &lt;value&gt;slave1:8020&lt;/value&gt;
	&lt;/property&gt;	
	&lt;property&gt;
	  &lt;name&gt;dfs.namenode.http-address.mycluster.nn1&lt;/name&gt;
	  &lt;value&gt;master:50070&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
	  &lt;name&gt;dfs.namenode.http-address.mycluster.nn2&lt;/name&gt;
	  &lt;value&gt;slave1:50070&lt;/value&gt;
	&lt;/property&gt;		
	&lt;property&gt;
	  &lt;name&gt;dfs.namenode.shared.edits.dir&lt;/name&gt;
	  &lt;value&gt;qjournal://master:8485;slave1:8485/mycluster&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
	  &lt;name&gt;dfs.journalnode.edits.dir&lt;/name&gt;
	  &lt;value&gt;/home/hadoop3/data/journaldata/jn&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
	  &lt;name&gt;dfs.client.failover.proxy.provider.mycluster&lt;/name&gt;
	  &lt;value&gt;org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
      &lt;name&gt;dfs.ha.fencing.methods&lt;/name&gt;
      &lt;value&gt;
	  sshfence
	  shell(/bin/true)
	  &lt;/value&gt;
    &lt;/property&gt;
    &lt;property&gt;
      &lt;name&gt;dfs.ha.fencing.ssh.private-key-files&lt;/name&gt;
      &lt;value&gt;/home/hadoop3/.ssh/id_rsa&lt;/value&gt;
    &lt;/property&gt;
	&lt;property&gt;
      &lt;name&gt;dfs.ha.fencing.ssh.connect-timeout&lt;/name&gt;
      &lt;value&gt;10000&lt;/value&gt;
    &lt;/property&gt;
    &lt;property&gt;
      &lt;name&gt;dfs.namenode.handler.count&lt;/name&gt;
      &lt;value&gt;100&lt;/value&gt;
    &lt;/property&gt;
	&lt;property&gt;
	   &lt;name&gt;dfs.ha.automatic-failover.enabled&lt;/name&gt;
	   &lt;value&gt;true&lt;/value&gt;
	 &lt;/property&gt;
    &lt;property&gt;
		 &lt;name&gt;dfs.webhdfs.enabled&lt;/name&gt;
		 &lt;value&gt;true&lt;/value&gt;
	&lt;/property&gt;	 
	&lt;property&gt;
	    &lt;name&gt;dfs.datanode.max.xcievers&lt;/name&gt;
	    &lt;value&gt;8192&lt;/value&gt;
	&lt;/property&gt;
&lt;/configuration&gt;
</code></pre>
<p>mapred-site.xml</p>
<pre><code>&lt;configuration&gt;
    &lt;property&gt;
		&lt;name&gt;mapreduce.framework.name&lt;/name&gt;
		&lt;value&gt;yarn&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
		&lt;name&gt;mapreduce.application.classpath&lt;/name&gt;
		&lt;value&gt;
		    /home/hadoop3/app/hadoop/etc/hadoop,
			/home/hadoop3/app/hadoop/share/hadoop/common/*,
			/home/hadoop3/app/hadoop/share/hadoop/common/lib/*,
			/home/hadoop3/app/hadoop/share/hadoop/hdfs/*,
			/home/hadoop3/app/hadoop/share/hadoop/hdfs/lib/*,
			/home/hadoop3/app/hadoop/share/hadoop/mapreduce/*,
			/home/hadoop3/app/hadoop/share/hadoop/mapreduce/lib/*,
			/home/hadoop3/app/hadoop/share/hadoop/yarn/*,
			/home/hadoop3/app/hadoop/share/hadoop/yarn/lib/*
		&lt;/value&gt;
	&lt;/property&gt;
&lt;/configuration&gt;
</code></pre>
<p>yarn-site.xml</p>
<pre><code>&lt;configuration&gt;

&lt;!-- Site specific YARN configuration properties --&gt;
	&lt;property&gt;
		&lt;name&gt;yarn.resourcemanager.connect.retry-interval.ms&lt;/name&gt;
		&lt;value&gt;2000&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
		&lt;name&gt;yarn.resourcemanager.ha.enabled&lt;/name&gt;
		&lt;value&gt;true&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
		&lt;name&gt;yarn.resourcemanager.ha.automatic-failover.enabled&lt;/name&gt;
		&lt;value&gt;true&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
		&lt;name&gt;yarn.resourcemanager.ha.automatic-failover.embedded&lt;/name&gt;
		&lt;value&gt;true&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
		&lt;name&gt;yarn.resourcemanager.cluster-id&lt;/name&gt;
		&lt;value&gt;yarn-rm-cluster&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
		&lt;name&gt;yarn.resourcemanager.ha.rm-ids&lt;/name&gt;
		&lt;value&gt;rm1,rm2&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
		&lt;name&gt;yarn.resourcemanager.hostname.rm1&lt;/name&gt;
		&lt;value&gt;master&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
		&lt;name&gt;yarn.resourcemanager.hostname.rm2&lt;/name&gt;
		&lt;value&gt;slave1&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
		&lt;name&gt;yarn.resourcemanager.recovery.enabled&lt;/name&gt;
		&lt;value&gt;true&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
	   &lt;description&gt;The class to use as the persistent store.&lt;/description&gt;
	   &lt;name&gt;yarn.resourcemanager.store.class&lt;/name&gt;
	   &lt;value&gt;org.apache.hadoop.yarn.server.resourcemanager.recovery.ZKRMStateStore&lt;/value&gt;
	 &lt;/property&gt;
	&lt;property&gt;
		&lt;name&gt;yarn.resourcemanager.zk.state-store.address&lt;/name&gt;
		&lt;value&gt;master:2181,slave1:2181,slave2:2181&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
		&lt;name&gt;yarn.resourcemanager.zk-address&lt;/name&gt;
		&lt;value&gt;master:2181,slave1:2181,slave2:2181&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
		&lt;name&gt;yarn.resourcemanager.address.rm1&lt;/name&gt;
		&lt;value&gt;master:8032&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
		&lt;name&gt;yarn.resourcemanager.scheduler.address.rm1&lt;/name&gt;
		&lt;value&gt;master:8034&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
		&lt;name&gt;yarn.resourcemanager.webapp.address.rm1&lt;/name&gt;
		&lt;value&gt;master:8088&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
		&lt;name&gt;yarn.resourcemanager.address.rm2&lt;/name&gt;
		&lt;value&gt;slave1:8032&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
		&lt;name&gt;yarn.resourcemanager.scheduler.address.rm2&lt;/name&gt;
		&lt;value&gt;slave1:8034&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
		&lt;name&gt;yarn.resourcemanager.webapp.address.rm2&lt;/name&gt;
		&lt;value&gt;slave1:8088&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
		&lt;name&gt;yarn.nodemanager.aux-services&lt;/name&gt;
		&lt;value&gt;mapreduce_shuffle&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
		&lt;name&gt;yarn.nodemanager.aux-services.mapreduce_shuffle.class&lt;/name&gt;
		&lt;value&gt;org.apache.hadoop.mapred.ShuffleHandler&lt;/value&gt;
	&lt;/property&gt;

&lt;/configuration&gt;
</code></pre>
<p>slaves</p>
<pre><code>master
slave1
slave2
</code></pre>
<p>4、配置添加环境变量：</p>
<p>vi /etc/profile</p>
<pre><code>export HBASE_HOME=/home/hadoop3/app//hbase
export PATH=$PATH:$HBASE_HOME/bin
</code></pre>
<p>source /etc/profile</p>
<p>5、配置hbase/conf/hbase-env.sh</p>
<p>export JAVA_HOME=/home/hadoop3/app/jdk<br>
export HBASE_HOME=/home/hadoop3/app/hbase<br>
export HBASE_CONF_DIR=/home/hadoop3/app/hbase/conf<br>
#export HBASE_CLASSPATH=/home/hadoop3/app/hbase/conf<br>
export HBASE_CLASSPATH=/home/hadoop3/app/hadoop/etc/hadoop<br>
export HBASE_MANAGES_ZK=false      # //不用自带的zookeeper<br>
export HBASE_LOG_DIR=${HBASE_HOME}/logs<br>
export HBASE_PID_DIR=/home/hadoop3/data/hbase/pids</p>
<p>6、配置hbase/conf/hbase-site.xml<br>
当os user为root时<br>
hbase.tmp.dir=/hbase/tmp<br>
有权限</p>
<p>当os user为hadoop3时<br>
hbase.tmp.dir=/home/hadoop3/data/hbase/tmp</p>
<pre><code>&lt;configuration&gt;
	&lt;property&gt;
	  &lt;name&gt;hbase.tmp.dir&lt;/name&gt;
	  &lt;value&gt;/home/hadoop3/data/hbase/tmp&lt;/value&gt;
	&lt;/property&gt;
    &lt;!--HBase数据目录位置，master是mater机器的域名（别名）--&gt;
    &lt;property&gt;
        &lt;name&gt;hbase.root.dir&lt;/name&gt;
        &lt;value&gt;hdfs://mycluster/hbase&lt;/value&gt;
    &lt;/property&gt;
	&lt;!--启用分布式集群--&gt;
    &lt;property&gt;
        &lt;name&gt;hbase.cluster.distributed&lt;/name&gt;
        &lt;value&gt;true&lt;/value&gt;
    &lt;/property&gt;
	&lt;property&gt;
		&lt;name&gt;hbase.master&lt;/name&gt;
		&lt;value&gt;60000&lt;/value&gt;&lt;!--这里注意了，只需端口即可，不必再写主机名称了！--&gt;
		&lt;description&gt;指定 hbase 集群主控节点&lt;/description&gt;
   &lt;/property&gt;
	&lt;!--默认HMaster HTTP访问端口--&gt;
   &lt;property&gt;
       &lt;name&gt;hbase.master.info.port&lt;/name&gt;
       &lt;value&gt;16010&lt;/value&gt;
    &lt;/property&gt;
       &lt;!--默认HRegionServer HTTP访问端口--&gt;
    &lt;property&gt;
       &lt;name&gt;hbase.regionserver.info.port&lt;/name&gt;
       &lt;value&gt;16030&lt;/value&gt;
    &lt;/property&gt;
	&lt;property&gt;
        &lt;name&gt;hbase.zookeeper.quorum&lt;/name&gt;
        &lt;value&gt;master,slave1,slave2&lt;/value&gt;
		&lt;description&gt;配置独立的zk集群地址，除了master,自已配了几台zookeeper，此处就配几台&lt;/description&gt;
    &lt;/property&gt;
	&lt;property&gt;
		&lt;name&gt;hbase.zookeeper.property.clientPort&lt;/name&gt;
		&lt;value&gt;2181&lt;/value&gt;
		&lt;description&gt; 连接到zookeeper的端口，默认是2181&lt;/description&gt;
  &lt;/property&gt;
  &lt;property&gt;
		&lt;name&gt;hbase.zookeeper.property.dataDir&lt;/name&gt;
		&lt;value&gt;/home/hadoop3/data/hbase/zookeeper&lt;/value&gt;
  &lt;/property&gt;
  &lt;property&gt;
    &lt;name&gt;zookeeper.session.timeout&lt;/name&gt;
    &lt;value&gt;60000000&lt;/value&gt;
  &lt;/property&gt;
  &lt;property&gt;
	&lt;name&gt;hbase.regionserver.restart.on.zk.expire&lt;/name&gt;
	&lt;value&gt;true&lt;/value&gt;
	&lt;description&gt;
	Zookeeper session expired will force regionserver exit.
	Enable this will make the regionserver restart.
	&lt;/description&gt;
 &lt;/property&gt;
&lt;/configuration&gt;
</code></pre>
<p>hbase/conf/regionservers</p>
<pre><code>master
slave1
slave2
</code></pre>
<p>hbase/conf/backup-masters</p>
<pre><code>slave1
</code></pre>
<p>7、启动hbase</p>
<p>启动顺序：先启动zookeeper-&gt;Hadoop–&gt;再启动HBase，关闭顺序：先关闭HBase–&gt;再关闭Hadoop</p>
<p>bin/start-hbase.sh</p>
<p>8、hdfs下去查看hbase目录</p>
<p>hadoop fs -ls /hbase</p>
<p>9、进入hbase命令行</p>
<p>hbase shell</p>
<p>10、停止hbase</p>
<p>bin/stop-hbase.sh</p>
<p>#启动报错问题org.apache.hadoop.hbase.PleaseHoldException: Master is initializing<br>
解决方法：<br>
master&gt;<a href="http://runRemoteCmd.sh" rel="nofollow">runRemoteCmd.sh</a> “<a href="http://hbase-daemon.sh" rel="nofollow">hbase-daemon.sh</a> start regionserver” all<br>
master&gt;<a href="http://runRemoteCmd.sh" rel="nofollow">runRemoteCmd.sh</a> “<a href="http://hbase-daemon.sh" rel="nofollow">hbase-daemon.sh</a> start master”  master<br>
解决问题！<br>
即先启动regionserver，再启动HMaster。<br>
在hregionServer服务器上./hbase-daemon.sh start regionserver<br>
在hmasterServer服务器上执行：./hbase-daemon.sh start master<br>
同时确保hbase/lib/zookeeper-3.4.12.jar 与zookeeper集群版本一致<br>
<a href="http://zkCli.sh" rel="nofollow">zkCli.sh</a><br>
rmr  /home/hadoop3/data/hbase<br>
或<br>
rmr  /hbase</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>