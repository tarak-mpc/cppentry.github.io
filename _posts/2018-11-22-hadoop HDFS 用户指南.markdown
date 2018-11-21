---
layout:     post
title:      hadoop HDFS 用户指南
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 style="font-size:28px;font-family:'Roboto Slab', serif;line-height:1.1;color:rgb(218,68,83);">
1.目的</h1>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
这个文档的出发点是为了那些使用HDFS的用户，不管是集群环境或者是作为独立的通用分布式文件系统。当在许多环境中使用HDFS时，HDFS的工作知识帮助对一个特定的群集上的配置进行改进和诊断</p>
<h1 style="font-size:28px;font-family:'Roboto Slab', serif;line-height:1.1;color:rgb(218,68,83);">
<a name="_Toc430283920"></a>2.综述：</h1>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
HDFS是Hadoop应用程序使用的主要分布式存储。一个HDFS集群只要包括一个NameNode 来管理文件系统的元数据，dataNode来存储实际数据。HDFS构架指导详细的描述了HDFS。这个用户指导主要用于处理HDFS集群中的用户和管理者的交互。HDFS构架图标描述了NameNode，DataNode和客户端之间的基本交互。客户端为了文件远数据或者修改文件描述而联系nameNode，直接通过DataNode来执行实际的文件I/O。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
下面的一些特征，很多用户可能会感兴趣。</p>
<ul style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;"><li>Hadoop,包括HDFS，非常适用于使用标准硬件来进行分布式存储和分布式处理。它是容错的，可扩展的，并且扩展非常简单。分布式计算（MapReduce），众所周知，它非常简单的适用于大型分布式应用程序，它是hadoop的一个组成部分。</li><li>HDFS是高度可配置的，作为默认的配置对于很多安装来说是非常合适的。大多数时候，仅仅对于大型集群需要对配置进行调整。</li><li>Hadoop是JAVA写的，支持所有的平台。</li><li>Hadoop支持类型shell的命令来直接和HDFS进行交互。</li><li>NameNode和Datanode内置web服务器，这样便于检查集群当前状态。</li><li>在HDFS中会定期的实施新特性和改进。以下是HDFS的一个有用功能的子集：</li></ul><p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
.文件权限和验证。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
. 构架意识（rack awareness）：在调度任务和分配存储时，将节点的物理位置考虑在内。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
.安全模块（safemode）：一个维护的管理模块。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
.fsck:一个诊断文件系统健康的工具，用来找出丢失的文件或块。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
.fetchd：一个获得Delegation token的工具，并且存储到本地文件系统的一个文件中。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
.平衡（blancer）:当DataNodes中的数据是不均衡分布时，一个平衡集群的工具。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
.第二个 NameNode（secondary namenode）：执行定期的命名空间检查点,并且在NameNode中，有助于保持HDFS修改日志文件大小在一定限额内。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
.检查点节点(checkpoint node): 执行定期的命名空间检查点，并且在NameNode中，有助于保持HDFS修改日志的最小的大小。以往这个角色由secondary namenode所替代，虽然它还没有被增强。NameNode允许同时多个检查点节点，只要没有备份的节点注册到系统中。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
.备份节点（backup node）：检查点节点的延伸。除此之外，它也从NameNode接受编辑流，并且维护自己再命名空间的内存副本，其总是与活跃的NameNode命名空间状态一致。只有一个备份节点可以与NameNode进行一次注册(dbdao.com Hadoop 大数据学习)。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
<img class="alignnone" src="http://7xjuxf.com2.z0.glb.qiniucdn.com/%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE_091715_080714_PM.jpg" alt="" width="891" height="601" style="border:0px;vertical-align:middle;"></p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
<span id="more-887"></span></p>
<h1 style="font-size:28px;font-family:'Roboto Slab', serif;line-height:1.1;color:rgb(218,68,83);">
<a name="_Toc430283921"></a>3.先决条件：</h1>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
下面文档描述了如何安装和配置一个Hadoop集群：</p>
<ul style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;"><li>第一次单节点安装。</li><li>为大型，分布式集群配置集群。</li></ul><p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
本文档其余部分假设用户已经配置和运行了一个HDFS，至少有一个DataNode。本文档的目标，NameNode和DataNode可以在相同的物理机上运行。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<h1 style="font-size:28px;font-family:'Roboto Slab', serif;line-height:1.1;color:rgb(218,68,83);">
<a name="_Toc430283922"></a>4.WEB接口</h1>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
NameNode和DataNode 每个都在内部运行了WEB服务，用来显示集群现状的基本信息。在默认配置下，NameNode的首页是：http://namenode-name:50070.它列出了再集群中的数据节点和集群的基本情况。web接口也可以用于游览文件系统（在NameNode首页上使用 “Browse the file system” 链接）</p>
<h1 style="font-size:28px;font-family:'Roboto Slab', serif;line-height:1.1;color:rgb(218,68,83);">
<a name="_Toc430283923"></a>5.shell 命令</h1>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
Hadoop 包含类似于shell命令的命令，来直接让HDFS于那些hadoop支持的文件系统进行交互。bin/hdfs dfs -help命令列出了hadoop shell支持的命令。此外，bin/hdfs dfs -help command-name命令显示了更多关于命令的信息。这些命令支持一些和普通文件系统类似的命令，例如拷贝文件，更改文件权限等等。它也支持一些HDFS特定的操作，例如文件的变更复制。更多信息可以参见 文件系统shell指导(dbdao.com Hadoop 大数据学习)。</p>
<h2 style="font-family:'Roboto Slab', serif;line-height:1.1;color:rgb(218,68,83);font-size:24px;">
<a name="_Toc430283924"></a>5.1 DFSAdmin 命令</h2>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
bin/hdfs dfsadmin命令支持一些管理员相关的操作。bin/hdfs dfsadmin -hel命令列出了当前所有支持的命令。例如：</p>
<ul style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;"><li>-report：打印HDFS基本的统计信息。一些信息也可以在NameNode 首页上看到。<img class="alignnone" src="http://7xjuxf.com2.z0.glb.qiniucdn.com/h_2.jpg" alt="" width="906" height="519" style="border:0px;vertical-align:middle;"></li><li>-safemode:虽然通常并不需要，管理员可以手动加载或卸载安全模块。</li><li>-finalizeUpgrade: 删除上次集群升级过程中所做的备份。</li><li>-refreshNode:在namenode上更新那些允许连接到namenode的datanodes。Namenodes 重新读取到在df.hosts中定义的dfs.hosts,dfs.hosts.exclude中的datanode主机名。如果配置了dfs.hosts，那么只有其中的主机允许注册到namenode。配置在dfs.hosts.exclude的那些datanodes是需要被剔除掉的。当datanode上的副本完全复制到其他datanode上时，节点才完全的被剔除。被剔除的节点不会自动的关闭，并且不会选择写入新的副本。</li></ul><p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
（用于剔除节点和再次加进集群）</p>
<ul style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;"><li>-printTopology:打印集群拓扑。显示一个树形构架，datanode会作为Namenode的一个视图添加到构架中。</li></ul><p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
（这个命令只会显示当前存在的拓扑，如果节点没注册到namenode，或者集群未启动 不会显示）</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
<img class="alignnone" src="http://7xjuxf.com2.z0.glb.qiniucdn.com/h_1.jpg" alt="" width="779" height="167" style="border:0px;vertical-align:middle;"></p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
更多命令使用，可以参考hdfs命令指导</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<h1 style="font-size:28px;font-family:'Roboto Slab', serif;line-height:1.1;color:rgb(218,68,83);">
<a name="_Toc430283925"></a>6.Secondary NameNode</h1>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
NameNode存储了文件系统的修改，作为日志增加到本地文件系统中，编辑。当一个NameNode启动，它从一个镜像文件,fsimage中读取HDFS状态，然后应用编辑日志文件中的编辑（edits）。然后写入新HDFS状态到fsimage，在一个空的编辑文件中开始普通的操作。由于只有在启动的时候，nameNode才合并edits文件和fsimage，在一个繁忙的集群系统中edits日志文件可能会非常大。较大的edits文件的另一个副作用是在下次重启NameNode需要更长的时间。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
secondary NameNode 合并fsimage和之前的edits日志文件，保持edits日志大小在一个限制内。它经常运行在另外的机器上，对比主NameNode 上的内存请求是和主NameNode上的顺序一样(dbdao.com Hadoop 大数据学习)。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
在secondary NameNode上启动检查点进程是由两个配置参数控制。</p>
<ul style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;"><li>dfs.namenode.checkpoint.period,默认设置是1小时，指定两个连续检查点的最大延时。</li><li>dfs.namenode.checkpoint.txns,默认是1百万，定义了NameNode上未检查点事务的数量，将会强制进行紧急检查点，即使没达到检查点时间。</li></ul><p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
secondary NameNode 在一个和主NameNode中结构相同目录中存储了最近检查点。因此，如果必要的话，检查点镜像总是准备去从NameNode去读。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
更多命令使用，可以参考hdfs命令指导</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<h1 style="font-size:28px;font-family:'Roboto Slab', serif;line-height:1.1;color:rgb(218,68,83);">
<a name="_Toc430283926"></a>7.检查点节点（checkpoint Node）</h1>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
NameNode  坚持它的命名空间使用两种文件：fsimage,是最近一次命名空间和edits的检查点，由于检查点，一个日志（log）的变化到命名空间。当一个NameNode启动时，它会合并fsimage和edits日志来为文件系统元数据提供一个最新的视图。NameNode然后使用新HDFS状态覆盖fsimage，然后开始一个新的edits日志。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
检查点 节点定期地为命名空间创建检查点。它会从活动地NameNode上下载fsimage和edits，在本地合并他们，然后将新的image上传到活动地NameNode。检查点节点通常运行在不同的主机上，对比主NameNode 上的内存请求是和主NameNode上的顺序一样。在配置文件中指定的节点上，使用bin/hdfs namenode -checkpoint命令来启动检查点节点。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
检查点（或者备份）节点，它们伴随的web接口的位置是通过,dfs.namenode.backup.address和dfs.name.backup.http-address的值来配置。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
在检查点 节点上启动检查点进程是由两个配置参数控制。</p>
<ul style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;"><li>dfs.namenode.checkpoint.period,默认设置是1小时，指定两个连续检查点的最大延时。</li><li>dfs.namenode.checkpoint.txns,默认是1百万，定义了NameNode上未检查点事务的数量，将会强制进行紧急检查点，即使没达到检查点时间。</li></ul><p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
checkpoint node 在一个和主NameNode中结构相同目录中存储了最近检查点。这使得检查点 镜像在需要的时候随时可以通过Namenode读取。参考 导入 检查点。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
复合检查点 节点可以在集群配置中被定义。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
更多命令使用，可以参考hdfs命令指导(dbdao.com Hadoop 大数据学习)</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<h1 style="font-size:28px;font-family:'Roboto Slab', serif;line-height:1.1;color:rgb(218,68,83);">
<a name="_Toc430283927"></a>8.备份节点（Backup Node）</h1>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
备份节点提供了和检查点 节点一样的检查点功能，同时在内存中维护一个从活动NameNode 拷贝的副本，总是同步最新的文件系统副本。随着从NameNode接收文件系统的日志流和保持它们到磁盘，备份节点也应用这些edite，到自己内存里命名空间的副本中，这样就生成了一个命名空间的备份。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
备份节点不需要为了创建一个检查点，而从活动地NameNode上下载fsimage和edits文件，作为一个检查点节点和secondary nameNode是需要的，因为它已经拥有了内存中的命名空间的最新状态。备份节点检查点进程更有效，只需要保存命名空间到本地fsimage文件和重置edites。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
最为备份节点要在内存中维护一份命名空间的拷贝，它们的RAM（内存大小）需要和NameNode节点一样。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
NameNode 在同一时间只支持一个备份节点。如果使用了一个备份节点，那么不能将检查点节点注册。同时使用多个备份节点将在未来被支持。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
备份节点和检查点节点的配置一样。它使用bin/hdfs namenode -backup命令启动。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
备份（或者检查点）节点，它们伴随的web接口的位置是通过,dfs.namenode.backup.address和dfs.name.backup.http-address的值来配置。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
使用一个备份节点提供了选项在运行NameNode时，将非持久性存储，所有命名空间状态持久性的责任，抛给了备份节点。为了做这些，开启NameNode使用-importCheckPoint选项，同时在NameNode中，为edits类型设置非持久性存储目录 dfs.namenode.edits.dir</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
查看创建备份节点和检查点节点背后的动机，可以参考HADOOP-4539这个完全的讨论（https://issues.apache.org/jira/browse/HADOOP-4539）。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
更多命令使用，可以参考hdfs命令指导</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<h1 style="font-size:28px;font-family:'Roboto Slab', serif;line-height:1.1;color:rgb(218,68,83);">
<a name="_Toc430283928"></a>9.导入检查点（Import  Checkpoint）</h1>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
如果所有的image拷贝和edits文件都丢失了，最近的检查点可以被导入到NameNode。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
为了做到这一点需要：</p>
<ul style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;"><li>创建一个在dfs.namenode.name.dir中配置值的空路径；</li><li>在dfs.namenode.checkpoint.dir配置的值中指定检查点目录的位置；</li><li>使用-importCheckpoint选项启动NameNode</li></ul><p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
NameNode将从dfs.namenode.checkoint.dir路径中上传检查点，并且保存它到dfs.namenode.name.dir中配置的NameNode 目录。如果在dfs.namenode.name.dir中存在一个合法的image，NameNode将失败。 NameNode验证 dfs.namenode.checkpoint.dir中image一致性，但不以任何方式修改它。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
更多命令使用，可以参考hdfs命令指导(dbdao.com Hadoop 大数据学习)</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<h1 style="font-size:28px;font-family:'Roboto Slab', serif;line-height:1.1;color:rgb(218,68,83);">
<a name="_Toc430283929"></a>10.平衡（balancer）</h1>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
HDFS数据可能并不是在DataNode中被放置的是均匀的。一个常见的原因是新的DataNode添加到现存的集群中。当放置新块时（文件的数据被存储为一系列的块），NameNode在选择DataNodes来接受这些块之前，要考虑多个参数。一些注意事项(dbdao.com Hadoop 大数据学习)：</p>
<ul style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;"><li>保持一个在同一节点上的块的副本，作为写入该块到节点的策略。</li><li>需要通过构架来传播不同块的副本，这样机器可以在整个构架丢失的情况下存活下来。</li><li>其中一个副本通常是放在同一个构架上，作为写入文件的节点，这样就减少了网络构架的I/O。</li><li>在集群中HDFS数据均匀的传播到DataNode</li></ul><p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
由于多种竞争性的考虑，数据可能并不是均衡的放置在DataNode中。HDFS提供了一个工具给管理员来分析块放置和对DataNode中的数据再平衡。一个简短的管理员手册对于平衡是可用的（HADOOP-1653 https://issues.apache.org/jira/browse/HADOOP-1652）</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
更多命令使用，可以参考hdfs命令指导</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<h1 style="font-size:28px;font-family:'Roboto Slab', serif;line-height:1.1;color:rgb(218,68,83);">
<a name="_Toc430283930"></a>11.构架意识</h1>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
通常大型Hadoop集群被安排在一些机架上，集群被不同节点之前的网络流量比不同机架间的网络流量更可取。另外，NameNode试图放置块的副本在不同机架上来提高容错。hadoop可以让集群管理员通过配置变量net.topology.scrript.file.name的值来决定节点属于哪个机架。当这个脚本被配置，每个节点运行这个脚本来决定它们的机架id。默认安装时假设所有的节点属于一个机架。这个特性和配置进一步在HADOOP-692中附带的PDF中介绍（https://issues.apache.org/jira/browse/HADOOP-692）</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<h1 style="font-size:28px;font-family:'Roboto Slab', serif;line-height:1.1;color:rgb(218,68,83);">
<a name="_Toc430283931"></a>12.安全模块（safemode）</h1>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
在启动NameNode期间会从fsimage和edits日志文件中，加载文件系统状态。然后它会等待DataNode汇报它们的块，这样它并未过早的开始复制块，虽然集群中已经存在足够的复制。在这个期间，NameNode 位于安全模式。安全模式对于NameNode本质上来说，是对HDFS集群的只读模式，它不允许任何修改文件系统或块。通常呢，在NameNode接收到DataNode的报告,所有文件系统块是可用的之后，会自动脱离安全模式。如果需要，HDFS可以被放置到安全模式，通过明确指定 bin/hdfs dfsadmin
 -safemode 命令。NameNode首页显示了安全模式是否是开或关(dbdao.com Hadoop 大数据学习)。更详细的描述和配置可以参考JavaDoc中setSafeMode().</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<h1 style="font-size:28px;font-family:'Roboto Slab', serif;line-height:1.1;color:rgb(218,68,83);">
<a name="_Toc430283932"></a>13 .修复（fsck）</h1>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
HDFS提供了fsck命令来检查各种不一致。它被设置为各种文件报告出错误，例如，一个文件丢失块或者在复制块后。不同于传统的本地文件系统中的用途，这个命令不会修正检测到的错误。通常情况下，NameNode自动的修正那些可恢复故障。默认情况下，fsck会忽略打开文件，但是提供一个选项来在报告中选择所有文件。HDFS fsck命令不是一个Hadoop shell命令。它可以使用 bin/hdfs fsck来运行。fsck可以运行在整个文件系统上，或者一个文件子集上。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
更多命令使用，可以参考hdfs命令指导</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<h1 style="font-size:28px;font-family:'Roboto Slab', serif;line-height:1.1;color:rgb(218,68,83);">
<a name="_Toc430283933"></a>14 . fetchdt</h1>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
HDFS通过fetchdt命令来获得Delegation Token，然后存储到一个本地文件系统的文件中。这个token可以在之后从一个非安全客户端来访问安全服务器（例如NameNode）。程序使用RPC或者HTTPS来获得token，因此在运行之前需要存在kerberos tickets（运行kinit来获得tickets）。HDFS fetchdt命令不是一个Hadoop shell命令。可以使用bin/hdfs fetchdt DTfile来运行。在你获得token之后，可以允许一个HDFS命令而没有kerberos
 tickets，通过制定HADOOP_TOKEN_FILE_LOCATION环境变量来表示token文件(dbdao.com Hadoop 大数据学习)。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
更多命令使用，可以参考hdfs命令指导</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<h1 style="font-size:28px;font-family:'Roboto Slab', serif;line-height:1.1;color:rgb(218,68,83);">
<a name="_Toc430283934"></a>15.恢复模块（recovery mode）</h1>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
通常，你将配置多个元数据存储位置。这样，如果一个存储位置坏了，你可以从其他存储位置来读取元数据。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
但是，唯一存储位置损坏了，你能够做些什么？在这个场景下，可以指定NameNode启动模式调用Recovery mode，可能会让你恢复你的大部分数据。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
你可以在恢复模式开启NameNode，例如这样：namenode -recover</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
当在恢复模式时，NameNode将会以交互式的提示你在命令行，采取可能的行动来恢复你的数据。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
如果你不想被体制，你可以使用 -force选项。这个选项将强制恢复模式总是选择第一个选项。通常情况下，这些都是合理的选择。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
因为恢复模式会导致丢失数据，你需要在使用它之前备份你的edit日志和fsimage(dbdao.com Hadoop 大数据学习)。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<h1 style="font-size:28px;font-family:'Roboto Slab', serif;line-height:1.1;color:rgb(218,68,83);">
<a name="_Toc430283935"></a>16.升级和回滚</h1>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
当Hadoop在现有的集群上进行升级，作为任何升级的软件，它可能有一些在之前没有发现的，新的bug或不兼容的变更，从而影响现有的应用。在任何非普通地HDFS安装中，松散任何数据并不是一个选项，更不用说从头开始重启HDFS。HDFS允许管理员去回退到更早的Hadoop版本，并且回滚集群的状态到升级之前。关于hadoop升级的详细描述可以参考(http://wiki.apache.org/hadoop/Hadoop_Upgrade).HDFS可以同时有这样一个备份。在升级之前，管理员需要移除现有的备份使用 bin/hadoop
 dfsadmin -finalizeUpgrade 命令。下面简要的介绍了典型的升级过程：</p>
<ul style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;"><li>在升级Hadoop软件之前，最后确定这里是否存在备份。dfsadmin -upgradeProgress状态可以说明集群是否需要最后确定。</li><li>停止集群，然后分布新的hadoop版本</li><li>使用新版本选项-upgrafe（bin/start-dfs.sh -upgrade）</li><li>在大多数情况，集群运行的很好。一旦新的HDFS被认为工作正常（在许多天操作之后），完成升级。注意直到集群被确认，在升级之前删除存在的文件不会真正的在DataNodes上释放磁盘空间.</li><li>如果这里需要回退到之前旧的版本(dbdao.com Hadoop 大数据学习)，</li></ul><p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
.关闭集群然后分布早期的hadoop版本。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
.在namenode上运行回退命令（bin/hdfs namenode -rollback）。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
.启动集群使用rollback选项(sbin/start-dfs.sh -rollback)。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
当升级到一个新版本的HDFS时，对保留在新版本HDFS中的任何路径进行重命名或者删除是必要的。如果NameNode在升级过程中遇到了一个保留的路径，他会打印一个错误如下：</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
/.reserved is a reserved path and .snapshot is a reserved path component in this version of HDFS. Please rollback and delete or rename this path, or upgrade with the -renameReserved [key-value pairs] option to automatically rename these paths during upgrade.</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
指定-upgrade -renameReserved [optional key-value pairs]可以让NameNode来自动重命名任何在启动中发现的保留路径。例如，重命名所有路径.snapshot 到 .my-snapshot ， .reserved 到 .my-reserved。用户需要指定-upgrade -renameReserved .snapshot=.my-snapshot,.reserved=.my-reserved.</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
如果在-renameReserved中没有指定键值对，NameNode将使用后缀.&lt;LAYOUT-VERSION&gt;.UPGRADE_RENAMED,例如.snapshot.-51.UPGRADE_RENAMED.</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
在重命名过程中会有一些警告。建议在可能的情况下， 首先在升级前hdfs dfsadmin -saveNamespace。这是因为如果一个edit日志操作是关联到一个自动重命名文件目标端，可能会出现数据不一致。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<h1 style="font-size:28px;font-family:'Roboto Slab', serif;line-height:1.1;color:rgb(218,68,83);">
<a name="_Toc430283936"></a>17.DataNode 热插拔驱动（Hot Swap Drive）</h1>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
DataNode支持热插拔驱动。用户可以在不关闭DataNode下增加或替换HDFS数据卷。下面简要介绍了典型的热插拔驱动程序：</p>
<ul style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;"><li>如果是新存储目录，用户需要格式化它们并适当地挂载它们。</li><li>用户更新DataNode 配置dfs.datanode.data.dir来反映将使用的数据卷的目录</li><li>用户运行dfsadmin -reconfig datanode HOST:PORT start来启动配置进程。用户可以使用dfsadmin -reconfig datanode HOST:PORT status来查询配置任务的运行状态。</li><li>一旦配置任务完毕，用户可以使用umount来溢出数据卷目录和物理移除磁盘(dbdao.com Hadoop 大数据学习)。</li></ul><p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<h1 style="font-size:28px;font-family:'Roboto Slab', serif;line-height:1.1;color:rgb(218,68,83);">
<a name="_Toc430283937"></a>18.文件权限和安全</h1>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
文件权限配置被设计和其他属性平台上类似，例如linux。目前，安全性仅限于简单的文件权限。启动NameNode的用户被视为HDFS的超级用户。未来的HDFS版本将会支持网络认证协议例如Kerberos，为用户认证和传输数据加密。详细的讨论可以参考权限指导。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<h1 style="font-size:28px;font-family:'Roboto Slab', serif;line-height:1.1;color:rgb(218,68,83);">
<a name="_Toc430283938"></a>19.可扩展性</h1>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
Hadoop目前能运行上千节点的集群。在wiki页面（http://wiki.apache.org/hadoop/PoweredBy）上列出了一些部署了大型hadoop的组织。对于每个机器来说，HDFS有一个NameNode。目前来说NameNode上总的内存大小是拓展性的限制。在一个非常大的集群中，增加存储在HDFS中文件平均大小，有助于与集群的增加而不增加NameNode的内存需求。默认的配置可能不使用太大的集群。FAQ（http://wiki.apache.org/hadoop/FAQ）wiki页面列出了提升大hadoop集群的建议配置(dbdao.com
 Hadoop 大数据学习)。</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
 </p>
<h1 style="font-size:28px;font-family:'Roboto Slab', serif;line-height:1.1;color:rgb(218,68,83);">
<a name="_Toc430283939"></a>20.相关文件</h1>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
本用户指南是使用HDFS的一个很好的起点，随着用户指南的延伸，这里有大量丰富的Hadoop和HDFS文档。下面列出一些进一步查找的起点：</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
http://hadoop.apache.org/</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
http://wiki.apache.org/hadoop/FrontPage</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
http://wiki.apache.org/hadoop/FAQ</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
http://hadoop.apache.org/docs/r2.7.1/api/index.html</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
http://hadoop.apache.org/docs/r2.7.1/hadoop-project-dist/hadoop-hdfs/hdfs-default.xml</p>
<p style="color:rgb(107,107,107);font-family:'Open Sans';font-size:14px;line-height:23px;">
http://hadoop.apache.org/docs/r2.7.1/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html</p>
            </div>
                </div>