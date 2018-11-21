---
layout:     post
title:      HDFS Users Guide
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2>目的</h2>
<p>本文档对于使用HDFS的用户来说是一个起点，不管是作为Hadoop集群的一部分还是一个独立的通用的分布式文件系统。虽然HDFS被设计在很多环境下工作，但是HDFS工作原理的支持将极大的帮助配置的调高和特定集群的故障检测。</p>
<h2>概览</h2>
<p>HDFS是Hadoop应用程序使用的主要的分布式存储系统。一个HDFS集群主要包括一个NameNode和多个DataNode，NameNode管理文件系统元数据，DataNode存储真正的数据。HDFS的架构指南详细地描述了HDFS。本用户指南主要讲述用户和管理员与HDFS系统的交互。HDFS架构图描绘了NameNode，DataNode和client之间的基本的交互。Client连接NameNode取得文件元数据或者文件修改信息，然后直接与DataNode执行真正的文件I/O操作。</p>
<p>下面是一些可能引起很多用户兴趣的特性：</p>
<p>1.      Hadoop，包括HDFS，非常适合用标准硬件进行分布式存储和分布式处理。它具有容错，可伸缩和及其简单的扩容等特性。因其对于大量的分布式应用程序的简单和高适用性而出名的MapReduce是Hadoop的一部分。</p>
<p>2.      HDFS的默认配置适合大部分设备。大多数情况下，配置只在非常大的集群中时需要调优。</p>
<p>3.      Hadoop用java编写，支持所有主流的平台。</p>
<p>4.      Hadoop支持类shell的命令来与HDFS直接交互。</p>
<p>5.      NameNode和DataNode内置了web服务器，使其更容易的检查集群当前的状态。</p>
<p>6.      新的特性和改进通常实现在HDFS中，下面是HDFS中的部分有用的特性：</p>
<p>1&gt;    文件权限和认证</p>
<p>2&gt;    机架感知：在调度任务和申请存储的时候考虑到节点的物理位置。</p>
<p>3&gt;    安全模式：维护时的管理模式</p>
<p>4&gt;    fsck：一个检测集群文件系统健康状况的工具，可以找出丢失的文件和Block。</p>
<p>5&gt;    fetchdt：一个获取DelegationToken，然后存储到本地文件系统的工具。</p>
<p>6&gt;    Rebalancer：当数据不平均的分布在DataNode时平衡集群数据的工具。</p>
<p>7&gt;    Upgrade和rollback：在软件升级之后，再遇到不可预测的问题的情况下，回滚回HDFS升级之前的状态是可以的。</p>
<p>8&gt;    Secondary NameNode：周期性地执行namespace的检查点，帮助保持NameNode中存储HDFS的修改日志的文件的大小不超过某个范围。</p>
<p>9&gt;    Checkpoint Node：周期性地执行namespace的检查点操作，帮助减少存储在NameNode的包含HDFS变化的日志的大小。取代之前Secondary NameNode的角色，尽管还不是必须的。NameNode允许同时存在多个Checkpoint节点，只要系统中没有BackUp节点的存在。</p>
<p>10&gt; BackUp节点：Checkpoint节点的扩展。除了checkpoint，它还从NameNode接收一个edit文件流，在内存中维护他自己的namespace的copy，这个copy总是与NameNode节点上namespace的状态同步。NameNode一次只能注册一个BackUp节点。</p>
<h2>基本条件</h2>
<p>下面的文档描述了如何安装和启动一个Hadoop集群：</p>
<ul type="disc"><li style="color:#333333;background:#FFFFFF;"><span><a><span style="color:#6688AA;">Single Node
 Setup</span></a></span> for first-time users. </li><li style="color:#333333;background:#FFFFFF;"><span><a><span style="color:#6688AA;">Cluster Setup</span></a></span> for
 large, distributed clusters.</li></ul><p>本文档剩下的部分假设用户已能够建立和运行至少有一个DataNode节点的HDFS集群。为了实现本文档的目的，NameNode和DataNode节点可以运行在一台物理机器上。</p>
<h2>Web接口</h2>
<p>每一个NameNode和DataNode都运行一个内部的web服务器，以展示关于集群当前的状态的基本的信息。默认配置下，NameNode主页面在<a href="http://namenode-name:50070/" rel="nofollow"><span style="background:#FFFFFF;">http://namenode-name:50070/</span></a>。它列出了集群中所有的数据节点和集群中基本统计信息。这个web接口可以被用来浏览文件系统（在NameNode的主页上有“Browse
 the file system”的链接）。</p>
<h2>Shell 命令</h2>
<p>Hadoop拥有各种类shell命令，能够直接与HDFS和其他Hadoop支持的文件系统进行交互。bin/hdfsdfs –help命令可以列出Hadoop shell支持的命令。而且bin/hdfs dfs -help command-name会列出一个命令更多的细节。这些命令支持大多数标准文件系统操作，像复制文件，修改文件权限等。它也支持一些HDFS特有的操作，像改变文件副本个数。更多信息尽在 <a><span style="color:#6688AA;background:#FFFFFF;">File
 System Shell Guide</span></a>。</p>
<h3>DFSAdmin Command</h3>
<p>bin/hadoop dfsadmin命令支持一些HDFS管理相关的操作。bin/hadoopdfsadmin –help命令列出了当前支持的所有的命令，例如：</p>
<p>1.  –report：报告HDFS基本的统计信息。这些信息中一些亦可以在NameNode主页中查看。</p>
<p>2.  –safemode：最然通常不需要，但是一个管理员可以手工的进入或者离开安全模式。</p>
<p>3.  –finalizeUpgrade：移除最近的一次升级时先前集群的备份。</p>
<p>4.  –refreshNodes：更新namenode和多个连接到此NameNode的DataNode。NameNode重新读取定义在<tt><span style="color:#333333;background:#FFFFFF;">dfs.hosts</span></tt><span style="color:#333333;background:#FFFFFF;">, </span><tt><span style="color:#333333;background:#FFFFFF;">dfs.hosts.exclude</span></tt>文件中的DataNode的hostname。定义在<span style="color:#333333;background:#FFFFFF;">dfs.hosts</span><span style="color:#333333;background:#FFFFFF;">文</span>件中的主机是集群中的datanode的部分。如果dfs.hosts中有条目，只有其中出现的主机才被允许注册到NameNode。<span style="color:#333333;background:#FFFFFF;">dfs.hosts.exclude</span>中出现的条目是需要退役的DataNode。当这些节点上的副本在其他数据节点副本完成，这些DataNode完成退役。退役的节点不会自动关机，新的副本不会在选择这些节点写入。</p>
<p>5.  printTopology：打印机群的拓扑。展示一个树形的机架和依附于机架上的DataNode，就像在NameNode中看到的那样。</p>
<p>更多命令的用法，看 <a><span style="color:#6688AA;background:#FFFFFF;">dfsadmin</span></a>。</p>
<h2><span style="background:#FFFFFF;">Secondary NameNode</span></h2>
<p>NameNode存储文件系统的变化，添加这些信息到本地文件系统中的日志文件的末尾，这个日志文件时edits。当一个NameNode启动，它从一个镜像文件，fsimage中读取HDFS的状态，然后应用edits日志文件中的edits。然后将一个新的HDFS状态写到fsimage中，用一个新的空的edit文件存储正常的操作。因为NameNode只在启动时合并fsimage和edit文件，随着时间推移，edit的日志文件可能在一个忙碌的集群中变得非常大。大edit日志文件的另一个副作用是下一次NameNode的启动将会花费更长的时间。</p>
<p>Secondary NameNode周期性地合并fsimage和edit日志文件，保持日志文件的大小在一个范围内。它通常运行在一个不同于NameNode的机器上，因为它的内存需求跟NameNode一样。</p>
<p>Checkpoint进程在Secondary NameNode上的启动被两个配置参数管理：</p>
<p><a name="OLE_LINK2"></a><a name="OLE_LINK1">1.      <span style="color:#333333;background:#FFFFFF;">dfs.namenode.checkpoint.period</span></a><span style="color:#333333;background:#FFFFFF;">：</span>默认设置为1小时，这个参数指定两次连续的Checkpoint操作的最大间隔。</p>
<p>2.      <span style="color:#333333;background:#FFFFFF;">dfs.namenode.checkpoint.txns</span><span style="color:#333333;background:#FFFFFF;">：</span>默认设置为1百万，这个参数定义了NameNode中没有Checkpoint的事务的个数，如果超过这个个数，即使没有到Checkpoint的时间，也会强制Checkpoint。</p>
<p><a name="OLE_LINK4"></a><a name="OLE_LINK3">Secondary NameNode</a>将最近的Checkpoint存储到跟NameNode中一样结构的目录中。所以如果必要，被Checkpoint的image总是准备被NameNode读取。</p>
<p>更多命令用法，看<a><span style="color:#6688AA;background:#FFFFFF;">secondarynamenode</span></a>。</p>
<h2><span style="background:#FFFFFF;">Checkpoint Node</span></h2>
<p>NameNode用两个文件持久化它的namespace：fsimage和edits，fsimage是namespace的最近一次的Checkpoint，edits文件是自Checkpoint后namespace的变化日志。当NameNode启动时，它合并fsimage和edits日志文件以提供一个文件系统元数据的最新的视图。然后NameNode用新的HDFS状态覆盖fsimage，开起一个新的edits文件。</p>
<p>Checkpoint节点周期性的创建namespace的Checkpoint。它从active的NameNode下载fsimage和edits文件，本地合并它们生成新的image，然后将新的image上传回activeNameNode上。Checkpoint节点通常运行在不同于NameNode的机器上，因为它的内存需求跟NameNode一样。Checkpoint节点在配置文件中指定的节点上用bin/hdfs namenode –checkpoint启动。</p>
<p>Checkpoint（或Backup）节点和它通过<tt>dfs.namenode.backup.address</tt> 和<tt>dfs.namenode.backup.http-address</tt>配置的附带的web接口。</p>
<p>Checkpoint节点上的checkpoint进程被两个配置参数管理：</p>
<p>1.       <span style="color:#333333;background:#FFFFFF;">dfs.namenode.checkpoint.period</span><span style="color:#333333;background:#FFFFFF;">：</span>默认设置为1小时，这个参数指定两次连续的Checkpoint操作的最大间隔。</p>
<p>2.      <span style="color:#333333;background:#FFFFFF;">dfs.namenode.checkpoint.txns</span><span style="color:#333333;background:#FFFFFF;">：</span>默认设置为1百万，这个参数定义了NameNode中没有Checkpoint的事务的个数，如果超过这个个数，即使没有到Checkpoint的时间，也会强制Checkpoint。</p>
<p>Checkpoint节点将最近的Checkpoint存储到跟NameNode中一样结构的目录中。这使得必要时被checkpoint的image是可读的。查看Import checkpoint。</p>
<p>一个集群中可以配置多个Checkpoint节点。</p>
<p>更多的命令用法，看 <a><span style="color:#6688AA;background:#FFFFFF;">namenode</span></a>。</p>
<h2><span style="background:#FFFFFF;">Backup</span><span style="background:#FFFFFF;">节点</span></h2>
<p>跟Checkpoint node差不多。</p>
<h2>Import Checkpoint</h2>
<p>如果NameNode中所有其他的image和edits文件的copy都丢失了，最近的Checkpoint可以被import到NameNode中。为了可以import，你应该：</p>
<p>1.      在<span style="color:#333333;background:#FFFFFF;">dfs.namenode.name.dir</span>配置指定的path创建一个空的目录。</p>
<p>2.      用<span style="color:#333333;background:#FFFFFF;">dfs.namenode.checkpoint.dir</span>指定<span style="color:#333333;background:#FFFFFF;">Checkpoint</span>目录<span style="color:#333333;background:#FFFFFF;">。</span></p>
<p>3.       用<tt><span style="color:#333333;background:#FFFFFF;">-importCheckpoint</span></tt><span style="color:#333333;background:#FFFFFF;"> </span>选项启动<span style="color:#333333;background:#FFFFFF;">NameNode</span><span style="color:#333333;background:#FFFFFF;">。</span></p>
<p>NameNode将从dfs.namenode.checkpoint.dir目录中上传Checkpoint，然后将它保存到 <tt>dfs.namenode.name.dir</tt>设置的<tt>NameNode</tt>的目录<tt>。</tt>如果在dfs.namenode.name.dir目录中有一个合法的image，NameNode将会失败。NameNode检验<tt>dfs.namenode.checkpoint.dir</tt> 的image是否一致，但是任何情况下都不会修改它。</p>
<p>更多命令用法，查看<a><span style="color:#6688AA;background:#FFFFFF;">namenode</span></a>。</p>
<h2><span style="background:#FFFFFF;">Rebalancer</span></h2>
<p>HDFS数据可能不总是一致的被存放在DataNode中。一个常见的原因是新DataNode节点的增加。当存放新的Block（一个文件的数据被存放为一些列的Block）时，NameNode考虑很多的参数在选择接收这些Block的DataNode时。下面是一些考虑的因素：</p>
<p>1.      保持一个Block的多个副本中的一个与正在写入的Block在一个节点上。</p>
<p>2.      需要将副本跨机架传播，这样集群可以在整个机架沦陷时幸存。</p>
<p>3.      多个副本中的一个通常存放在跟正在写入的文件相同的机架上，这样可以减少跨机架的网络I/O。</p>
<p>4.      一致的的在集群中的DataNode之间传播HDFS数据。</p>
<p>考虑到多个相互矛盾的因素，数据可能不一致的存放在DataNode中。HDFS提供了一个分析数据块的位置和重新平衡DataNode中的数据的工具。<a href="https://issues.apache.org/jira/browse/HADOOP-1652" rel="nofollow"><span style="color:#6688AA;background:#FFFFFF;">HADOOP-1652</span></a>中是一个简短的rebalancer的管理员指南，pdf格式。</p>
<p>更多命令用法，看<a><span style="color:#6688AA;background:#FFFFFF;">balancer</span></a>。</p>
<h2><span style="background:#FFFFFF;">Rack Awareness</span></h2>
<p>通常一个大的Hadoop集群分布在多个机架上，同一个机架上的不同节点间的网络流量比跨机架的节点间的网络流量更令人满意。NameNode试图将Block的副本放到多个机架上以提高容错。通过配置net.topology.script.file.name，Hadoop让集群管理员自己决定一个节点属于哪一个机架。当此脚本配置，每一个节点运行这个脚本来决定它属于哪一个机架。默认设置是假设所有的节点属于同一个机架。此特性和配置更进一步的描述在 <a href="https://issues.apache.org/jira/browse/HADOOP-692" rel="nofollow"><span style="color:#6688AA;background:#FFFFFF;">HADOOP-692</span></a>。</p>
<h2><span style="background:#FFFFFF;">Safemode</span></h2>
<p>NameNode启动是从fsimage和edits日志文件中加载文件系统状态。然后等待DataNode报告它们的Block，所以NameNode不过早的复制Block，可能集群中有足够的副本。在这段时间内，NameNode在safemode状态。NameNode的Safemode本质上来说就是HDFS集群的只读模式，它不允许文件系统或Block的任何修改。正常情况下，在DataNode报告它的大多数文件系统的Block available之后，NameNode会自动的离开safemode模式。如果有必要，HDFS可以用bin/hadoop
 dfsadmin –safemode明确的进入safemode。</p>
<p>NameNode主页展示了safemode开关状态。更详细的描述和配置在setSafeMode()的java doc中。</p>
<h2><span style="background:#FFFFFF;">Fsck</span></h2>
<p>HDFS支持fsck命令来检查各种不一致状态。它被设置用来报告各种文件的各种问题，例如，丢失一个文件的某个Block或者正在复制的Block。不像针对本地文件系统的传统的fsck工具，这个命令不更正它检测到的错误。正常情况下，NameNode自动更正大部分可恢复的失效。默认，fsck忽略打开的文件但是提供一个选项在报告时选择所有的文件。HDFS fsck命令不是Hadoop shell命令。它可以用<tt>bin/hadoop fsck</tt>运行。更多命令的用法，看<a><span style="color:#6688AA;background:#FFFFFF;">fsck</span></a>。Fsck可以运行在整个文件系统或者所有文件的子集。</p>
<h2>Fetchdt</h2>
<p>HDFS支持<a name="OLE_LINK5">fetchdt</a>命令来获取 Delegation Token和将其存储到本地文件系统中。这个token之后可以被用来从一个不安全的客户端访问安全的服务（例如NameNode）。此工具使用RPC或者HTTPS（在Kerberos之上）来获取token，因此需要ticket才能运行（运行kinit 命令可以得到ticket）。HDFSfetchdt命令不是Hadoop shell命令。它可以用bin/hadoop
 fetchdt DTfile运行。在你取得token之后，通过指定 <tt>HADOOP_TOKEN_FILE_LOCATION</tt>环境变量你可以不需要Kerberosticket就运行HDFS命令， <tt>HADOOP_TOKEN_FILE_LOCATION</tt>指定delegationtoken文件的位置。更多命令用法，查看 <a><span style="color:#6688AA;background:#FFFFFF;">fetchdt</span></a>命令。</p>
<h2>Recovery Mode</h2>
<p>通常情况下，你需要配置多个元数据的存储位置。然后，若果一个存储位置崩溃，你可以从另一个其他的位置读取元数据。</p>
<p>但是，如果仅有的存储崩溃，你能做啥呢？在这种情况下，有一个特殊的NameNode启动模式，Recovery Mode，它允许你恢复你的大多数数据。</p>
<p>你可以用namenode –recover以recovery mode启动NameNode。</p>
<p>在Recovery Mode时，NameNode将在命令行交互性地提示你可以恢复数据的可能的行动步骤。</p>
<p>如果你不希望被提示，你可以给 <tt>-force</tt>选项。这个选项将强制RecoveryMode总是选择第一个选项。正常情况下，这将是最合理的选择。</p>
<p>因为Recovery Mode可能使你丢失信息，在使用它之前，你应该总是备份你的edit日志和fsimage。</p>
<h2>Upgrade 和 Rollback</h2>
<p>当Hadoop在一个已存在的集群上被升级的时候，就像任何的软件升级一样，它可能有一些新的bug或者不兼容的变化，这些bug和变化可能会影响已存在的应用程序，并且不能过早的发现。在任何重要的HDFS安装中，丢失任何数据都是不允许的，更不用说HDFS重新启动。HDFS允许管理员回滚回Hadoop升级之前的版本，将集群回滚回升级之前的状态。HDFS升级更多的细节在<a href="http://wiki.apache.org/hadoop/Hadoop_Upgrade" rel="nofollow"><span style="color:#6688AA;background:#FFFFFF;">Hadoop
 Upgrade</span></a>。这个时候，HDFS可以有这样一个备份。在升级之前，管理员需要 用bin/hadoop dfsadmin <tt>-finalizeUpgrade</tt>命令移除已经存在的备份。下面是对一个典型的升级过程简短的描述：</p>
<p>1．  在升级Hadoop软件之前，如果有一个已经存在的备份，finalize掉。<span style="color:#333333;background:#FFFFFF;">dfsadmin -upgradeProgress</span>状态可以告诉我们集群是否需要被finalize。</p>
<p>2．  停止集群，分发新版本的Hadoop。</p>
<p>3．  用<span style="color:#333333;background:#FFFFFF;">bin/start-dfs.sh-upgrade</span>运行新版本的hadoop</p>
<p>4．  大多数情况下，集群会很好的工作。一旦新HDFS被认为工作良好（可能是很多天的操作之后得出），finalize掉这个Upgrade。注意，直到集群被finalize，删除升级之前存在的文件不会释放DataNode上真正的存储空间。</p>
<p>5．  如果有需要回滚回旧版本，</p>
<p>a)        停掉集群，分发hadoop旧版本。</p>
<p>b)        用rollback选项启动集群，<span style="color:#333333;background:#FFFFFF;">bin/start-dfs.sh –rollback</span><span style="color:#333333;background:#FFFFFF;">。</span></p>
<p>当升级到一个新版本的HDFS，有必要更改或删除任何存储在新版本中的路径。如果升级期间，NameNode遇到一个存在的路径，它将会打印像下面这样的错误：</p>
<p>/.reserved is a reserved path and .snapshot is areserved path component in this version of HDFS. Please rollback and delete orrename this path, or upgrade with the -renameReserved [key-value pairs] optionto automatically rename these paths during upgrade.</p>
<p>指定 <tt>-upgrade -renameReserved[optional key-value pairs]</tt>会使NameNode自动更改启动过程中发现任何保存的路径。例如，更改所有的.snapshot命名的路径为.my-snapshot，更改所有的.reserved路径为.my-reserved，用户也可以指定<tt>-upgrade -renameReserved.snapshot=.my-snapshot,.reserved=.my-reserved</tt>。</p>
<p>如果没有key-value对用-renameReserved被指定，NameNode将添加后缀 <tt>.&lt;LAYOUT-VERSION&gt;.UPGRADE_RENAMED</tt>，例如， <tt>.snapshot.-51.UPGRADE_RENAMED</tt>。</p>
<p>Rename进程会有一些警告。建议，如果可能，升级之前先运行hdfs dfsadmin -saveNamespace。这是因为如果edit日志操作涉及到自动修改过的文件的话，数据会出现不一致的情况。</p>
<h2>文件权限和安全</h2>
<p>文件权限的设计跟其他常见的平台像linux是相似的。目前，安全仅限于简单的文件权限。启动NameNode的用户被认为是HDFS的超级用户。将来的HDFS版本将支持网络认证协议像Kerberos来支持用户认证和数据传输加密。更详细的讨论在权限指南。</p>
<h2>Scalability</h2>
<p>Hadoop目前可以运行在几千个节点的集群上。<a href="http://wiki.apache.org/hadoop/PoweredBy" rel="nofollow"><span style="color:#6688AA;background:#FFFFFF;">PoweredBy</span></a>Wiki页面上列出了一些部署hadoop大规模集群的组织。HDFS在每个集群中有一个NameNode。目前NameNode上总的内存是主要的扩展限制。在每一个大集群上，增加存储在HDFS中的文件的大小有助于在不增加NameNode内存的情况下增加集群存储能力。默认的配置可能不适合非常大的集群。<a href="http://wiki.apache.org/hadoop/FAQ" rel="nofollow"><span style="color:#6688AA;background:#FFFFFF;">FAQ</span></a> Wiki页面列出了对于大规模hadoop集群的建议的配置提高。</p>
<h2>相关的文档</h2>
<p>本用户指南对于用HDFS工作来说是一个好的起点。当用户指南继续改进，将会有一个很大的关于hadoop和HDFS的文档。下面列出了对于更进一步的探索的起点：</p>
<ul type="disc"><li style="color:#333333;background:#FFFFFF;"><a href="http://hadoop.apache.org/" rel="nofollow"><span style="color:#6688AA;">Hadoop Site</span></a>: The home page for the Apache Hadoop site.</li><li style="color:#333333;background:#FFFFFF;"><a href="http://wiki.apache.org/hadoop/FrontPage" rel="nofollow"><span style="color:#6688AA;">Hadoop Wiki</span></a>: The home page (FrontPage) for the Hadoop Wiki. Unlike the released documentation,
 which is part of Hadoop source tree, Hadoop Wiki is regularly edited by Hadoop Community.
</li><li style="color:#333333;background:#FFFFFF;"><a href="http://wiki.apache.org/hadoop/FAQ" rel="nofollow"><span style="color:#6688AA;">FAQ</span></a>: The FAQ Wiki page.</li><li style="color:#333333;background:#FFFFFF;"><a><span style="color:#6688AA;">Hadoop JavaDoc API</span></a>.</li><li style="color:#333333;background:#FFFFFF;">Hadoop User Mailing List: user[at]hadoop.apache.org.</li><li style="color:#333333;background:#FFFFFF;">Explore <a><span style="color:#6688AA;">hdfs-default.xml</span></a>. It includes
 brief description of most of the configuration variables available. </li><li style="color:#333333;background:#FFFFFF;"><a><span style="color:#6688AA;">Hadoop Commands Guide</span></a>: Hadoop
 commands usage.<br></li></ul>            </div>
                </div>