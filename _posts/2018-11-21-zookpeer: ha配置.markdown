---
layout:     post
title:      zookpeer: ha配置
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>要点如下：</p>

<ol><li>zookeeper集群搭建</li>
	<li>hdfs高可用（hdfs + zookeeper:  zk管理namenode, 实现failover机制 )</li>
	<li>修改start-dfs.sh :  使得zk集群---- 先于 ---hdfs启动 </li>
</ol><hr><h1>hdfs集群“热备份”（zk + hdfs =ha)</h1>

<p><img alt="" class="has" height="588" src="https://img-blog.csdn.net/20181012133900747?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2V5ZW9mZWFnbGU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="978"></p>

<h1>ha具体配置文件</h1>

<h3>/etc/hosts</h3>

<pre class="has">
<code>192.168.56.111  s101
192.168.56.112  s102
192.168.56.113  slave1
192.168.56.114  slave2
192.168.56.115  slave3
192.168.56.116  zk1
192.168.56.117  zk2
192.168.56.118  zk3</code></pre>

<h3>core-site.xml</h3>

<pre class="has">
<code>&lt;configuration&gt;
&lt;property&gt;
&lt;name&gt;fs.defaultFS&lt;/name&gt;
&lt;value&gt;hdfs://mycluster&lt;/value&gt;
&lt;/property&gt; 

&lt;!-- 指定hadoop临时目录 --&gt; 
&lt;property&gt; 
&lt;name&gt;hadoop.tmp.dir&lt;/name&gt; 
&lt;value&gt;/home/wang/ha&lt;/value&gt; 
&lt;/property&gt; 

&lt;!-- 指定zookeeper地址 --&gt; 
&lt;property&gt; 
&lt;name&gt;ha.zookeeper.quorum&lt;/name&gt; 
&lt;value&gt;zk1:2181,zk2:2181,zk3:2181&lt;/value&gt; 
&lt;/property&gt;
&lt;/configuration&gt;</code></pre>

<h3>hdfs-site.xml</h3>

<pre class="has">
<code>&lt;configuration&gt; 
&lt;!--指定hdfs的nameservice为mycluster，和core-site.xml 一致 --&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.nameservices&lt;/name&gt;
        &lt;value&gt;mycluster&lt;/value&gt;
    &lt;/property&gt; 

&lt;!-- mycluster下面有两个NameNode，分别是nn1，nn2 是逻辑名字--&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.ha.namenodes.mycluster&lt;/name&gt;
        &lt;value&gt;nn1,nn2&lt;/value&gt;
    &lt;/property&gt; 

&lt;!-- nn1的RPC通信地址 --&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.namenode.rpc-address.mycluster.nn1&lt;/name&gt;
        &lt;value&gt;s101:9000&lt;/value&gt;
    &lt;/property&gt; 

&lt;!-- nn1的http通信地址 --&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.namenode.http-address.mycluster.nn1&lt;/name&gt;
        &lt;value&gt;s101:50070&lt;/value&gt;
    &lt;/property&gt; 

&lt;!-- nn2的RPC通信地址 --&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.namenode.rpc-address.mycluster.nn2&lt;/name&gt;
        &lt;value&gt;s102:9000&lt;/value&gt;
    &lt;/property&gt; 

&lt;!-- nn2的http通信地址 --&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.namenode.http-address.mycluster.nn2&lt;/name&gt;
        &lt;value&gt;s102:50070&lt;/value&gt;
    &lt;/property&gt; 

&lt;!-- 指定NameNode的edits元数据在JournalNode上的存放位置 --&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.namenode.shared.edits.dir&lt;/name&gt;
        &lt;value&gt;qjournal://slave1:8485;slave2:8485;slave3:8485/mycluster&lt;/value&gt;
    &lt;/property&gt; 

&lt;!-- 指定JournalNode在本地磁盘存放数据的位置 --&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.journalnode.edits.dir&lt;/name&gt;
        &lt;value&gt;/home/wang/ha/qjournal&lt;/value&gt;
    &lt;/property&gt;

 &lt;!-- 开启NameNode失败自动切换 --&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.ha.automatic-failover.enabled&lt;/name&gt;
        &lt;value&gt;true&lt;/value&gt;
    &lt;/property&gt; 

&lt;!-- 配置失败自动切换实现方式 --&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.client.failover.proxy.provider.mycluster&lt;/name&gt;
        &lt;value&gt;org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider&lt;/value&gt;
    &lt;/property&gt; 

&lt;!-- 配置隔离机制方法，多个机制用换行分割，即每个机制暂用一行--&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.ha.fencing.methods&lt;/name&gt;
        &lt;value&gt;sshfence
		 shell(/bin/true)
	&lt;/value&gt;
    &lt;/property&gt; 

&lt;!-- 使用sshfence隔离机制时需要ssh免登陆 --&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.ha.fencing.ssh.private-key-files&lt;/name&gt;
        &lt;value&gt;/home/wang/.ssh/id_rsa&lt;/value&gt;
    &lt;/property&gt;

 &lt;!-- 配置sshfence隔离机制超时时间 --&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.ha.fencing.ssh.connect-timeout&lt;/name&gt;
        &lt;value&gt;30000&lt;/value&gt;
    &lt;/property&gt;
&lt;/configuration&gt;

</code></pre>

<h3>mapred-site.xml </h3>

<pre class="has">
<code>&lt;configuration&gt;
&lt;!-- 指定mr框架为yarn方式 --&gt;
&lt;property&gt;
&lt;name&gt;mapreduce.framework.name&lt;/name&gt;
&lt;value&gt;yarn&lt;/value&gt;
&lt;/property&gt;
&lt;/configuration&gt;</code></pre>

<h3>yarn-site.xml </h3>

<pre class="has">
<code>&lt;configuration&gt; 
 &lt;!-- 开启RM高可用 --&gt;
    &lt;property&gt;
        &lt;name&gt;yarn.resourcemanager.ha.enabled&lt;/name&gt;
        &lt;value&gt;true&lt;/value&gt;
    &lt;/property&gt; 

&lt;!-- 指定RM的cluster id --&gt;
    &lt;property&gt;
        &lt;name&gt;yarn.resourcemanager.cluster-id&lt;/name&gt;
        &lt;value&gt;yarn-cluster&lt;/value&gt;
    &lt;/property&gt; 

&lt;!-- 指定RM的名字 --&gt;
    &lt;property&gt;
        &lt;name&gt;yarn.resourcemanager.ha.rm-ids&lt;/name&gt;
        &lt;value&gt;rm1,rm2&lt;/value&gt;
    &lt;/property&gt; 

&lt;!-- 分别指定RM的地址 --&gt;
    &lt;property&gt;
        &lt;name&gt;yarn.resourcemanager.hostname.rm1&lt;/name&gt;
        &lt;value&gt;s101&lt;/value&gt;
    &lt;/property&gt;
    &lt;property&gt;
        &lt;name&gt;yarn.resourcemanager.hostname.rm2&lt;/name&gt;
        &lt;value&gt;s102&lt;/value&gt;
    &lt;/property&gt; 

&lt;!-- 指定zookeeper集群地址 --&gt;
    &lt;property&gt;
        &lt;name&gt;yarn.resourcemanager.zk-address&lt;/name&gt;
        &lt;value&gt;zk1:2181,zk2:2181,zk3:2181&lt;/value&gt;
    &lt;/property&gt;
    &lt;property&gt;
        &lt;name&gt;yarn.nodemanager.aux-services&lt;/name&gt;
        &lt;value&gt;mapreduce_shuffle&lt;/value&gt;
    &lt;/property&gt;
&lt;/configuration&gt;
       </code></pre>

<p> </p>

<p> </p>            </div>
                </div>