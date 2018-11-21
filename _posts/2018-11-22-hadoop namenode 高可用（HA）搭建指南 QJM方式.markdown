---
layout:     post
title:      hadoop namenode 高可用（HA）搭建指南 QJM方式
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h3><strong>一、HDFS的高可用性</strong></h3>
<h4><strong>1.概述</strong></h4>
<p>         本指南提供了一个HDFS的高可用性（HA）功能的概述，以及如何配置和管理HDFS高可用性(HA)集群。本文档假定读者具有对HDFS集群的组件和节点类型具有一定理解。有关详情，请参阅Apache的HDFS的架构指南。</p>
<p><a href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/HDFSHighAvailabilityWithQJM.html" rel="nofollow">http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/HDFSHighAvailabilityWithQJM.html</a></p>
<h4><strong>2.背景</strong></h4>
<p>         CDH4之前，在HDFS集群中NameNode存在单点故障（SPOF）。对于只有一个NameNode的集群，如果NameNode机器出现故障，那么整个集群将无法使用，直到NameNode重新启动。</p>
<p>NameNode主要在以下两个方面影响HDFS集群:</p>
<p>(1). NameNode机器发生意外，比如宕机，集群将无法使用，直到管理员重启NameNode</p>
<p>(2). NameNode机器需要升级，包括软件、硬件升级，此时集群也将无法使用</p>
<p>         HDFS的HA功能通过配置Active/Standby两个NameNodes实现在集群中对NameNode的热备来解决上述问题。如果出现故障，如机器崩溃或机器需要升级维护，这时可通过此种方式将NameNode很快的切换到另外一台机器。</p>
<h4><strong>3.架构</strong></h4>
<p>         HDFSHA的解决方案可谓百花齐放，Linux HA, VMware FT, sharedNAS+NFS, BookKeeper, QJM/Quorum Journal Manager, BackupNode等等。目前普遍采用的是shard NAS+NFS，因为简单易用，但是需要提供一个HA的共享存储设备。而社区已经把基于QJM/Quorum Journal Manager的方案merge到trunk了，clouderea提供的发行版中也包含了这个feature，这种方案也是社区在未来发行版中默认的HA方案。</p>
<p>         在HA具体实现方法不同的情况下，HA框架的流程是一致的。不一致的就是如何存储和管理日志。在Active NN和Standby NN之间要有个共享的存储日志的地方，Active NN把EditLog写到这个共享的存储日志的地方，Standby NN去读取日志然后执行，这样Active和Standby NN内存中的HDFS元数据保持着同步。一旦发生主从切换Standby NN可以尽快接管Active NN的工作（虽然要经历一小段时间让原来Standby追上原来的Active，但是时间很短）。</p>
<p>         说到这个共享的存储日志的地方，目前采用最多的就是用共享存储NAS+NFS。缺点有：1）这个存储设备要求是HA的，不能挂掉；2）主从切换时需要fencing方法让原来的Active不再写EditLog，否则的话会发生brain-split，因为如果不阻止原来的Active停止向共享存储写EditLog，那么就有两个Active NN了，这样就会破坏HDFS的元数据了。对于防止brain-split问题，在QJM出现之前，常见的方法就是在发生主从切换的时候，把共享存储上存放EditLog的文件夹对原来的Active的写权限拿掉，那么就可以保证同时至多只有一个Active
 NN，防止了破坏HDFS元数据。</p>
<p>         Clouera为解决这个问题提出了QJM/Qurom Journal Manager，这是一个基于Paxos算法实现的HDFS HA方案。QJM的结构图如下所示：</p>
<p></p>
<p>         QJM的基本原理就是用2N+1台JournalNode存储EditLog，每次写数据操作有大多数（&gt;=N+1）返回成功时即认为该次写成功，数据不会丢失了。当然这个算法所能容忍的是最多有N台机器挂掉，如果多于N台挂掉，这个算法就失效了。这个原理是基于Paxos算法的，可以参考<a href="http://en.wikipedia.org/wiki/Paxos_(computer_science)" rel="nofollow">http://en.wikipedia.org/wiki/Paxos_(computer_science)</a>。</p>
<p>         用QJM的方式来实现HA的主要好处有：1）不需要配置额外的高共享存储，这样对于基于commodityhardware的云计算数据中心来说，降低了复杂度和维护成本；2）不在需要单独配置fencing实现，因为QJM本身内置了fencing的功能；3）不存在Single Point Of Failure；4）系统鲁棒性的程度是可配置的（QJM基于Paxos算法，所以如果配置2N+1台JournalNode组成的集群，能容忍最多N台机器挂掉）；5）QJM中存储日志的JournalNode不会因为其中一台的延迟而影响整体的延迟，而且也不会因为JournalNode的数量增多而影响性能（因为NN向JournalNode发送日志是并行的）。</p>
<br clear="all"><p align="left"> </p>
<h3><strong>二、HDFS(HA)软硬件配置</strong></h3>
<h4><strong>1.硬件</strong></h4>
<p>NameNode 机器，Active 、Standby 应该具有相同的硬件</p>
<h4><strong>2.软件</strong></h4>
<h6>(1)core-site.xml</h6>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p>&lt;property&gt;</p>
<p>      &lt;name&gt;fs.defaultFS&lt;/name&gt;</p>
<p>      &lt;value&gt;hdfs://<span style="color:#FF0000;">mycluster</span>&lt;/value&gt;</p>
<p>&lt;/property&gt;</p>
<p>&lt;property&gt;</p>
<p>      &lt;name&gt;ha.zookeeper.quorum&lt;/name&gt;</p>
<p>      &lt;value&gt;master,slave1,slave2,pcmk104,pcmk108&lt;/value&gt;</p>
<p>&lt;/property&gt;</p>
</td>
</tr></tbody></table><h6>(2)hdfs-site.xml</h6>
<p>1. dfs.nameservices注意与core-site.xml中的fs.defaultFS中的value保持一致</p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p>&lt;property&gt;</p>
<p>     &lt;name&gt;dfs.nameservices&lt;/name&gt;</p>
<p>     &lt;value&gt;<span style="color:#FF0000;">mycluster</span>&lt;/value&gt;</p>
<p>&lt;/property&gt;</p>
</td>
</tr></tbody></table><p> </p>
<p>2. dfs.ha.namenodes.mycluster每个namenode在名称服务中的唯一标识</p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p>&lt;property&gt;</p>
<p>     &lt;name&gt;dfs.ha.namenodes.<span style="color:#FF0000;">mycluster</span>&lt;/name&gt;</p>
<p>     &lt;value&gt;nn1,nn2&lt;/value&gt;</p>
<p>&lt;/property&gt;</p>
</td>
</tr></tbody></table><p> </p>
<p>3.两个结点的rpc地址</p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p>&lt;property&gt;</p>
<p>     &lt;name&gt;dfs.namenode.rpc-address.<span style="color:#FF0000;">mycluster</span>.nn1&lt;/name&gt;</p>
<p>     &lt;value&gt;master:54310&lt;/value&gt;</p>
<p>&lt;/property&gt;</p>
<p>&lt;property&gt;</p>
<p>     &lt;name&gt;dfs.namenode.rpc-address.<span style="color:#FF0000;">mycluster</span>.nn2&lt;/name&gt;</p>
<p>     &lt;value&gt;pcmk104:54310&lt;/value&gt;</p>
<p>&lt;/property&gt;</p>
</td>
</tr></tbody></table><p> </p>
<p>4. servicerpc地址</p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p>&lt;property&gt;</p>
<p>        &lt;name&gt;dfs.namenode.servicerpc-address.<span style="color:#FF0000;">mycluster</span>.nn1&lt;/name&gt;</p>
<p>        &lt;value&gt;master:53310&lt;/value&gt;</p>
<p>   &lt;/property&gt;</p>
<p>&lt;property&gt;</p>
<p>        &lt;name&gt;dfs.namenode.servicerpc-address.<span style="color:#FF0000;">mycluster</span>.nn2&lt;/name&gt;</p>
<p>        &lt;value&gt;pcmk104:53310&lt;/value&gt;</p>
<p>&lt;/property&gt;</p>
</td>
</tr></tbody></table><p> </p>
<p>5.http通信地址</p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p>&lt;property&gt;</p>
<p>     &lt;name&gt;dfs.namenode.http-address.<span style="color:#FF0000;">mycluster</span>.nn1&lt;/name&gt;</p>
<p>     &lt;value&gt;master:50070&lt;/value&gt;</p>
<p>&lt;/property&gt;</p>
<p>&lt;property&gt;</p>
<p>     &lt;name&gt;dfs.namenode.http-address.<span style="color:#FF0000;">mycluster</span>.nn2&lt;/name&gt;</p>
<p>     &lt;value&gt;pcmk104:50070&lt;/value&gt;</p>
<p>&lt;/property&gt;</p>
</td>
</tr></tbody></table><p> </p>
<p>6.<span style="color:rgb(54,46,43);">我们采用</span><span style="color:rgb(54,46,43);">3</span><span style="color:rgb(54,46,43);">个</span><span style="color:rgb(54,46,43);">journalnode</span><span style="color:rgb(54,46,43);">节点存储元数据，这是他们的</span><span style="color:rgb(54,46,43);">IP</span><span style="color:rgb(54,46,43);">与端口</span></p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p>&lt;property&gt;</p>
<p>     &lt;name&gt;dfs.namenode.shared.edits.dir&lt;/name&gt;</p>
<p>     &lt;value&gt;qjournal://master:8485;pcmk104:8485;slave1:8485/<span style="color:#FF0000;">mycluster</span>&lt;/value&gt;</p>
<p>&lt;/property&gt;</p>
</td>
</tr></tbody></table><p> </p>
<p>7.<span style="color:rgb(54,46,43);"> journaldata</span><span style="color:rgb(54,46,43);">的存储路径</span></p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p>&lt;property&gt;</p>
<p>      &lt;name&gt;dfs.journalnode.edits.dir&lt;/name&gt;</p>
<p>      &lt;value&gt;/home/hadoop/journaldata/&lt;/value&gt;</p>
<p>&lt;/property&gt;</p>
</td>
</tr></tbody></table><p> </p>
<p>8.<span style="color:rgb(54,46,43);">该类用来判断哪个</span><span style="color:rgb(54,46,43);">namenode</span><span style="color:rgb(54,46,43);">处于生效状态</span></p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p>&lt;property&gt;</p>
<p>    &lt;name&gt;dfs.client.failover.proxy.provider.mycluster&lt;/name&gt; &lt;value&gt;org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider&lt;/value&gt;</p>
<p>&lt;/property&gt;</p>
</td>
</tr></tbody></table><p> </p>
<p>9.<span style="color:rgb(54,46,43);">打开自动切换</span><span style="color:rgb(54,46,43);">namenode</span><span style="color:rgb(54,46,43);">的功能</span></p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p>   &lt;property&gt;</p>
<p>        &lt;name&gt;dfs.ha.automatic-failover.enabled&lt;/name&gt;</p>
<p>        &lt;value&gt;true&lt;/value&gt;</p>
<p>   &lt;/property&gt;</p>
</td>
</tr></tbody></table><p> </p>
<p> </p>
<p>10. 运行脚本实现安全机制</p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td valign="top">
<p>   &lt;property&gt;</p>
<p>        &lt;name&gt;dfs.ha.fencing.methods&lt;/name&gt;</p>
<p>        &lt;value&gt;shell(/bin/true)&lt;/value&gt;</p>
<p>    &lt;/property&gt;</p>
</td>
</tr></tbody></table><p> </p>
<h3><strong>三、HDFS(HA)初始化</strong></h3>
<h4><strong>1.格式化NameNode</strong></h4>
<p>设定所有的必要配置项后，必须首先同步两个NameNode上的元数据。如果是新建的HDFS 集群，则应首先格式化一个NameNode</p>
<p>（1）在格式化NameNode之前先启动<span style="color:rgb(54,46,43);">journalnode</span><span style="color:rgb(54,46,43);">服务</span></p>
<p><span style="color:rgb(54,46,43);">进入</span><span style="color:rgb(54,46,43);">bin</span><span style="color:rgb(54,46,43);">目录执行</span><span style="color:rgb(54,46,43);"> ./hdfs journalnode</span></p>
<p><span style="color:rgb(54,46,43);">注意：在每一台</span><span style="color:rgb(54,46,43);">journalnode</span><span style="color:rgb(54,46,43);">机上都需要启动该服务。</span></p>
<p><span style="color:rgb(54,46,43);">检查服务是否正常可以访问</span><span style="color:rgb(54,46,43);">master:8480,slave1:8480,pcmk104:8480</span><span style="color:rgb(54,46,43);">来验证。启动后若出现异常，格式化</span>NameNode之后就好了。</p>
<p>（2）格式化NameNode</p>
<p><span style="color:rgb(54,46,43);">进入</span><span style="color:rgb(54,46,43);">bin</span><span style="color:rgb(54,46,43);">目录执行</span><span style="color:rgb(54,46,43);"> ./hdfs namenode –format</span></p>
<h4><strong>2启动hadoop</strong></h4>
<p><span style="color:rgb(54,46,43);">在</span><span style="color:rgb(54,46,43);">sbin</span><span style="color:rgb(54,46,43);">目录下执行</span><span style="color:rgb(54,46,43);"> ./start-dfs.sh</span><span style="color:rgb(54,46,43);">启动</span><span style="color:rgb(54,46,43);">hadoop</span><span style="color:rgb(54,46,43);">集群。</span></p>
<p><span style="color:rgb(54,46,43);">查看页面</span><a href="http://pcmk104:50070/" rel="nofollow">http://pcmk104:50070</a>和<a href="http://master:50070/" rel="nofollow">http://master:50070/</a>应该一个处于active状态一个处于standby状态。</p>
<span style="color:rgb(54,46,43);"><br clear="all"></span>
<p align="left"><span style="color:rgb(54,46,43);"> </span></p>
<h3><strong>四、参考文献</strong></h3>
<p>[1].apache <a href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/HDFSHighAvailabilityWithQJM.html" rel="nofollow">
<span>HighAvailability With QJM</span></a>部分</p>
<p><a href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/HDFSHighAvailabilityWithQJM.html" rel="nofollow">http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/HDFSHighAvailabilityWithQJM.html</a></p>
<p><span style="color:rgb(54,46,43);">[2].</span> <a href="http://yanbohappy.sinaapp.com/?p=205" rel="nofollow">
http://yanbohappy.sinaapp.com/?p=205</a></p>
<p><span style="color:rgb(54,46,43);"> </span></p>
            </div>
                </div>