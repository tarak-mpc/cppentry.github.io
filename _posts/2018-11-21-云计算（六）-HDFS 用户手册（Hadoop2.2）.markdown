---
layout:     post
title:      云计算（六）-HDFS 用户手册（Hadoop2.2）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2 style="color:rgb(51,51,51);line-height:26px;font-size:17px;font-family:Verdana, Helvetica, Arial, sans-serif;">
HDFS Users Guide</h2>
<div style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<br></div>
<div style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<div>这篇文章作为工作在Hadoop分布式文件系统(HDFS)，无论是作为Hadoop集群的一部分还是作为一个独立的通用的分布式文件系统的用户的一个起点。HDFS设计用来在多种环境中轻松的使用，HDFS的工作知识非常有助于对一个特定集群配置的提升和诊断工作。</div>
<div>
<h3 style="color:rgb(102,102,102);background-color:rgb(204,204,204);font-size:14px;font-family:Verdana, Helvetica, Arial, sans-serif;">
<a name="t1" style="color:rgb(51,102,153);"></a>综述</h3>
HDFS是Hadoop应用程序所使用的主要分布式存储。一个HDFS集群主要有管理文件系统元数据的NameNode和存储真正数据的DataNode组成，HDFS的架构指南详细的描述了HDFS。用户手册主要涉及用户和管理员与HDFS集群的相互作用。HDFS的架构图描述了NameNode,DataNode,Clinets之间的基础交互。Clinets连接到Namenode提供文件元数据和文件修改，并且直接与Datanode完成文件I/O操作。</div>
<div><br></div>
<div>下边是一些许多用户感兴趣的显著特征：</div>
<div><span></span>
<ul style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;"><li style="font-size:12px;">Hadoop包括HDFS,非常适合使用普通硬件进行分布式存储和分布式处理，其具有高容错性，稳定性，并且非常容易扩展。Mapreduce以大型分布式应用程序设置的简单性和适用性而出名，其是Hadoop的一部分。</li><li style="font-size:12px;">HDFS默认配置的高可配置性适合多种环境安装。 大多数时候，只有非常大的集群才需要调整配置。</li><li style="font-size:12px;">Hadoop由java语言编写，并且被大多数平台支持。</li><li style="font-size:12px;">Hadoop支持类似shell的命令与HDFS进行交互</li><li style="font-size:12px;">NameNode和DataNode都集成在web服务器中，可以很容易的检测当前集群的状态。</li><li style="font-size:12px;">HDFS会定期的新增功能和改进. 以下是HDFS有用的一部分：
<ul><li>文件权限和身份认证。</li><li>机架感知：调度任务和分配存储时候考虑物理节点的位置。</li><li>安全模式：维护管理模式。</li><li><tt>fsck</tt>: 一个用来检测文件系统健康的工具，找到丢失的文件和区块。</li><li><tt>fetchdt</tt>: 一个用例获取 DelegationToken 的工具，并将其存在本地文件系统中。</li><li>重分配: 当数据在Datanode中被不平衡的分配时平衡分配到集群的工具。</li><li>升级和回滚:在意想不到的问题发生时可以回滚到升级之前的状态。</li><li>Secondary NameNode:周期性的检查命名空间,有助于保持在Namenode上的修改的HDFS日志文件的大小在一定范围内。</li><li>Checkpoint node:定期的检查命名空间以助于缩小在Namenode上的包含HDFS变动的日志文件的大小。</li><li>Backup node: checkpoint的扩展. 除了checkpointing其还可以接受Namenode来的可编辑的数据流并且在内存中维护一份属于自己的namespace, 并且和激活的Namesapce的状态保持同步，只有Backup节点可以和namespace同时注册。</li></ul></li></ul></div>
<h3 style="color:rgb(102,102,102);background-color:rgb(204,204,204);font-size:14px;font-family:Verdana, Helvetica, Arial, sans-serif;">
<a name="t2" style="color:rgb(51,102,153);"></a>必备条件 </h3>
<div>
<p style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;">以下文档描述了如何安装和配置Hadoop集群:</p>
<ul style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;"><li style="font-size:12px;"><a href="http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html#Single_Node_Setup" rel="nofollow" style="color:rgb(153,0,0);text-decoration:none;">Single Node Setup</a> 第一次使用的用户.</li><li style="font-size:12px;"><a href="http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html#Cluster_Setup" rel="nofollow" style="color:rgb(102,136,170);text-decoration:none;">Cluster Setup</a> 大规模分布式用户.</li></ul><p style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;">这篇文档其余部分假设读者可以至少在一个节点配置和运行HDFS.达到本文档的目的，NameNode和DataNode可以运行在同一台物理机器上。</p>
<p style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;"></p>
<h3 style="color:rgb(102,102,102);background-color:rgb(204,204,204);font-size:14px;font-family:Verdana, Helvetica, Arial, sans-serif;">
<a name="t3" style="color:rgb(51,102,153);"></a>Web接口</h3>
<div><span style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">NameNode和DataNode各自运行一个内部web服务器以现实当前集群状态的基本信息.默认配置下, Namenode的访问页面 </span><tt style="line-height:15.59375px;">http://namenode-name:50070/</tt><span style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">.
 其列出了集群中的DataNode和集群的基本统计. Web接口也可以用与浏览文件系统(用 "文件浏览" 链接 在Namenode的首页.</span></div>
<p></p>
<h3 style="color:rgb(102,102,102);background-color:rgb(204,204,204);font-size:14px;font-family:Verdana, Helvetica, Arial, sans-serif;">
<a name="t4" style="color:rgb(51,102,153);"></a>Shell 命令</h3>
<div>
<p style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;">Hadoop包含丰富的类似Shell的命令用来直接与HDFS及其他Hadoop支持的文件系统进行交互。命令 <tt>bin/hdfs dfs -help</tt> 列出了Hadoopshell支持的命令. 并且, 命令 <tt>bin/hdfs dfs -help command-name 显示命令的更多详细信息</tt>. 这些命令支持大多数的文件系统操作，像复制文件，改变权限，等等。它还支持一些HDFS的特有命令如文件的更改复制。更多的信息请参阅：.</p>
<div class="section" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
</div>
<h4 style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<a name="t5" style="color:rgb(51,102,153);"></a>DFSAdmin Command<a name="DFSAdmin_Command" style="color:rgb(51,102,153);"></a></h4>
<p style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;">The <tt>bin/hadoop dfsadmin</tt> 支持一些HDFS的相关管理操作。<tt>bin/hadoop dfsadmin -help</tt> 列出了目前所有支持的命令如下:</p>
<ul style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;"><li style="font-size:12px;"><tt>-report</tt>: 报告HDFS的基础数据统计 ，其中的一些信息在Namenode的首页也可以现实。</li><li style="font-size:12px;"><tt>-safemode</tt>: 尽管不常用，但是管理员可以用来管理进入和离开的安全级别。</li><li style="font-size:12px;"><tt>-finalizeUpgrade：移除在最后一次升级中的集群备份。</tt></li><li style="font-size:12px;"><tt>-refreshNodes</tt>: 更新Namenode允许Datanodes链接到Namenode. Namenode重新读取定义在<tt>dfs.hosts</tt>, <tt>dfs.hosts.exclude Datanode的主机名称</tt>.定义在 <tt>dfs.hosts中的主机是作为集群一部分的Datanodes</tt>. 如果有条目在 <tt>dfs.hosts中</tt>,只有其中列出的主机才允许注册Namenode。<tt>dfs.hosts.exclude中的条目</tt> 中定义需要退役的Datanodes.。
 Datnodes完全退役是在它的所有副本都复制到其他datanodes时候。退役的节点不会自动关机并且不允许选择写入新副本.</li><li style="font-size:12px;"><tt>-printTopology</tt> :输出集群的拓扑.现实一个机架树和datanodes关联的以Namenode作为的trackes（这句话还是看原版吧）。</li></ul><p style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;">更多的命令使用请参考 <a href="http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html#dfsadmin" rel="nofollow" style="color:rgb(102,136,170);text-decoration:none;"></a>.</p>
</div>
</div>
<br></div>
<div><span style="color:#333333;"><span style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:26px;font-size:14px;"></span></span>
<h3 style="color:rgb(102,102,102);font-size:14px;font-family:Verdana, Helvetica, Arial, sans-serif;line-height:26px;background-color:rgb(204,204,204);">
<a name="t6" style="color:rgb(51,102,153);"></a>Secondary NameNode<a name="Secondary_NameNode" style="color:rgb(51,102,153);"></a></h3>
<div>
<p style="color:rgb(51,51,51);font-size:12px;font-family:Verdana, Helvetica, Arial, sans-serif;line-height:1.3em;">
Namenode存储文件系统的西修改作为日志添加在本地文件系统。 当一个Namenode启动, 它从镜像对去HDFS的状态, fsimage,然后从编辑日志文件编辑. 然后它向fsimage写入HDFS的新状态，并且对空的可编辑文件进行操作。Namenode只有 <span style="line-height:17.32638931274414px;">在启动时候</span>合并fsimage和edits files，在一个繁忙的集群中日志文件会随着时间变得非常大。大的日志文件另一方面的影响是下次重启Namenode需要更长的时间。</p>
<p style="color:rgb(51,51,51);font-size:12px;font-family:Verdana, Helvetica, Arial, sans-serif;line-height:1.3em;">
Secondary Namenode 定期合并fsimage和日志文件并保持其大小在一定范围内。它经常运行于与primary NameNode不同的机器上，由于它的内存需要和primary Node具有相同的顺序。</p>
<p style="color:rgb(51,51,51);font-size:12px;font-family:Verdana, Helvetica, Arial, sans-serif;line-height:1.3em;">
在secondary namenode上的checkpoint进程启动由两个配置控制：</p>
<ul style="color:rgb(51,51,51);font-size:13px;font-family:Verdana, Helvetica, Arial, sans-serif;line-height:26px;"><li style="font-size:12px;"><tt>dfs.namenode.checkpoint.period</tt>, 默认设在1小时, 指定两个相邻checkpoint的最大延迟。</li><li style="font-size:12px;"><tt>dfs.namenode.checkpoint.txns</tt>, 默认设置1000000 ，定义为在<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:26px;">NameNode </span>没有进行checkpoint的事物的数量，即使<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:26px;">checkpoint
 period 没有达到也会进行紧急的<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:26px;">checkpoint</span></span></li></ul><p style="color:rgb(51,51,51);font-size:12px;font-family:Verdana, Helvetica, Arial, sans-serif;line-height:1.3em;">
Secondary Namenode把最后的checkpoints存入字典和primary Namenode具有相同的组织形式。所以如果需要的话checkpointed image随时都可被primary Namenode读取。</p>
<p style="color:rgb(51,51,51);font-size:12px;font-family:Verdana, Helvetica, Arial, sans-serif;line-height:1.3em;">
更多用法请参考。</p>
<h3 style="color:rgb(102,102,102);font-size:14px;font-family:Verdana, Helvetica, Arial, sans-serif;line-height:26px;background-color:rgb(204,204,204);">
<a name="t7" style="color:rgb(51,102,153);"></a>Checkpoint Node<a name="Checkpoint_Node" style="color:rgb(51,102,153);"></a></h3>
<div style="color:rgb(51,51,51);">
<p style="font-size:12px;font-family:Verdana, Helvetica, Arial, sans-serif;line-height:1.3em;">
Namenode用两个文件保存其配置: fsimage,namespace的最后一个checkpoint，记录从checkpoint开始的namespce的变化的日志。当一个NameNode启动，它合并fsimage和edits日志用来提供一个文件系统元数据的最新视图。Namenode之后以HDFS的状态重写fsimage并且开始新的日志记录。</p>
<p style="font-size:12px;font-family:Verdana, Helvetica, Arial, sans-serif;line-height:1.3em;">
Checkpoint 定时的为namespace创建检查点。它从正在激活的NameNode下载fsimage和编辑日志，在本地合并他们，并且上传新的image到正在激活的Namenode。CheckPoint通常和Namenode运行在不同的主机上因为它的内存要求和Namenode具有相同的顺序。Checkpoint由配置文件中指定的<span style="line-height:17.32638931274414px;">bin/hdfs namenode -checkpoint开始。</span>   </p>
<p style="font-size:12px;font-family:Verdana, Helvetica, Arial, sans-serif;line-height:1.3em;">
Checkpoint (or Backup)节点的位置 和随同的Web借口通过配置 <tt>dfs.namenode.backup.address</tt> 和 <tt>dfs.namenode.backup.实现，http-address</tt> 配置变量。</p>
<p style="font-size:12px;font-family:Verdana, Helvetica, Arial, sans-serif;line-height:1.3em;">
 Checkpoint 节点上的checkpoint启动由一下两个配置参数控制：</p>
<p style="font-size:12px;"><tt style="line-height:15.59375px;"><span style="font-family:Verdana, Helvetica, Arial, sans-serif;">                    </span></tt><tt style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:26px;">dfs.namenode.checkpoint.period</tt><span style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:26px;">,
 默认设在1小时, 指定两个相邻checkpoint的最大延迟。 </span></p>
<p style="font-size:12px;"><tt style="line-height:26px;">              dfs.namenode.checkpoint.txns</tt><span style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:26px;">, 默认设置1000000 ，定义为在NameNode 没有进行checkpoint的事物的数量，即使checkpoint period 没有达到也会进行紧急的checkpoint</span></p>
<p style="font-size:12px;font-family:Verdana, Helvetica, Arial, sans-serif;line-height:1.3em;">
Checkpoint节点采取和Namenode的字典相同的组织方式把最后的checkpoint存储进字典。这样checkpointed image就可以随时被Namenode所读取了。参考checkpoint的重要。</p>
<p style="font-size:12px;font-family:Verdana, Helvetica, Arial, sans-serif;line-height:1.3em;">
多数的checkpoint 节点要在集群配置文件中指定。</p>
<p style="font-size:12px;font-family:Verdana, Helvetica, Arial, sans-serif;line-height:1.3em;">
<br></p>
<p style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:1.3em;"><span style="font-size:12px;">注（为了方便理解，非原文档）：</span></p>
<p style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:1.3em;"><span style="color:rgb(51,51,51);font-family:Arial;line-height:26px;"><span style="font-size:12px;">C<span style="background-color:rgb(255,255,255);">heckpoint Node和Secondary NameNode的作用以及配置完全相同，<span style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">可能是由于Secondary
 NameNode这个名字给人带来的混淆，Hadoop后面的版本（1.0.4 ）建议不要使用Secondary NameNode，而使用CheckPoint Node。</span></span></span></span></p>
<p style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:1.3em;"><span style="color:rgb(51,51,51);font-family:Arial;line-height:26px;"><span style="font-size:12px;"><span style="color:rgb(51,51,51);font-family:Arial;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;background-color:rgb(255,255,255);">fsimage：<span style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:19px;">文件系统元数据的一个永久性检查点，包含文件系统中的所有目录和文件idnode的序列化信息。</span></span></span></span></span></p>
<p style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:1.3em;"><span style="background-color:rgb(255,255,255);"><span style="line-height:1.5;font-family:Verdana, Arial, Helvetica, sans-serif;font-size:13px;">edits：</span><span style="line-height:1.5;font-family:Verdana, Arial, Helvetica, sans-serif;font-size:13px;">文件系统的写操作首先把它记录在edit中</span></span></p>
<p style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:1.3em;"><span style="color:rgb(51,51,51);font-family:Arial;line-height:26px;"><span style="font-size:12px;"><span style="color:rgb(51,51,51);font-family:Arial;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;"><span style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:19px;"></span></span></span></span></span></p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;">
<span style="line-height:1.5;background-color:rgb(255,255,255);">hadoop将文件系统个元数据操作分开操作，是为了提升处理效率。如果不分开处理，即所有的写操作均记录在一个文件中，比如，fsimage中，那么每个操作都会对这个文件进行修改，因为这个文件可能会很大，所以每次进行写操作的时候就会很慢，随着fsimage越来越大，速度便会越来越低。</span></p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;">
<span style="background-color:rgb(255,255,255);">Hadoop的解决方案是辅助Namenode节点，文件系统的写操作不是直接被修改到fsimage中，二是edits中，辅助Namenode节点负责将两者在自己的内存中整合然后进行相应的替换操作，这样会频繁的对edits进行修改而不是fsimage，而edits文件的大小是可以接受的，而且大小也不会不断增加。具体的checkpoint执行过程如下：</span></p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;">
<img src="http://pic002.cnblogs.com/images/2012/392238/2012072517143596.jpg" alt="" style="border:0px;display:block;"></p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;">
<span style="background-color:rgb(255,255,255);"> </span></p>
<p style="line-height:19px;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;">
<span style="background-color:rgb(255,255,255);">以下即是checkpoint过程：</span></p>
<ol style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:19px;"><li style="list-style:decimal;"><span style="background-color:rgb(255,255,255);">辅助Namenode请求主Namenode停止使用edits文件，暂时将新的写操作记录到一个新文件中，如edits.new。</span></li><li style="list-style:decimal;"><span style="background-color:rgb(255,255,255);">辅助Namenode节点从主Namenode节点获取fsimage和edits文件（采用HTTP GET）</span></li><li style="list-style:decimal;"><span style="background-color:rgb(255,255,255);">辅助Namenode将fsimage文件载入到内存，逐一执行edits文件中的操作，创建新的fsimage文件</span></li><li style="list-style:decimal;"><span style="background-color:rgb(255,255,255);">辅助Namenode将新的fsimage文件发送回主Namenode（使用HTTP POST）</span></li><li style="list-style:decimal;"><span style="background-color:rgb(255,255,255);">主Namenode节点将从辅助Namenode节点接收的fsimage文件替换旧的fsimage文件，用步骤1产生的edits.new文件替换旧的edits文件（即改名）。同时更新fstime文件来记录检查点执行的时间</span></li></ol><div><span style="background-color:rgb(255,255,255);"><br></span></div>
<br></div>
<h3 style="color:rgb(102,102,102);font-size:14px;font-family:Verdana, Helvetica, Arial, sans-serif;line-height:26px;background-color:rgb(204,204,204);">
<a name="t8" style="color:rgb(51,102,153);"></a>Backup Node<a name="Backup_Node" style="color:rgb(51,102,153);"></a></h3>
<div style="color:rgb(51,51,51);font-size:14px;font-family:Arial;line-height:26px;">
<p style="line-height:1.3em;font-size:12px;font-family:Verdana, Helvetica, Arial, sans-serif;">
<span style="line-height:17.32638931274414px;"> Backup 节点提供和Checkpoint节点一样的检测功能，同样维护一个与激活的NameNode同步的在内存中，时时更新的命名空间文件系统。 Backup节点提供和checkpoint节点一样的功能，同样维护一个内存中的，时时的与激活状态的Namenode状态一致的命名空间的文件副本。随着从Namenode接受一个可编辑的文件日志并持久化到硬盘，Backup节点同样适用于这些这些命名空间在内存中的拷贝，创建一个命名空间的备份。</span></p>
<p style="line-height:1.3em;font-size:12px;font-family:Verdana, Helvetica, Arial, sans-serif;">
Backup节点不需要为了创建checkpoint从激活的Namenode下载fsimage和edits，而Checkpont节点和Secondary NameNode需要是需要的，因为它已经有一个在内存中时时的命名空间的状态。Backup节点的进程更高效，因为它只需要把命名空间存储到本地的fsimage和重至edits。</p>
<p style="line-height:1.3em;font-size:12px;font-family:Verdana, Helvetica, Arial, sans-serif;">
Backup节点在内存中维护一个命名空间的副本，它的RAM要求和Namenode一致。</p>
<p style="line-height:1.3em;font-size:12px;font-family:Verdana, Helvetica, Arial, sans-serif;">
NameNode一次只支持一个Backup节点，Checkpoint节点不允许注册如果Backup节点在使用。使用多个Backup节点会在不久的将来进行支持。</p>
<p style="line-height:1.3em;font-size:12px;font-family:Verdana, Helvetica, Arial, sans-serif;">
Backup节点和Checkpont节点在同一个管理器，用 bin/hdfs namenode -backup启动。</p>
<p style="line-height:1.3em;font-size:12px;font-family:Verdana, Helvetica, Arial, sans-serif;">
Backup节点的位置和web借口通过dfs.namenode.backup.address和dfs.namenode.backup.http-address配置。</p>
<p style="line-height:1.3em;font-size:12px;font-family:Verdana, Helvetica, Arial, sans-serif;">
Backup节点提供了选项支持运行Namenode节点不持久化到硬盘，代理所有的命名空间持久化状态的请求到Backup节点。为了做到这样，启动Namenode和命令 -importCheckpoint 选项，随着指定没有持久化存储目录通过<tt style="line-height:17.32638931274414px;">dfs.namenode.edits.dir</tt><span style="line-height:17.32638931274414px;"> f配置。</span></p>
<p style="line-height:1.3em;font-size:12px;font-family:Verdana, Helvetica, Arial, sans-serif;">
更全面的讨论Backup节点和Checkpont节点背后的机制，请参考<span style="line-height:1.3em;"> </span><a class="externalLink" href="https://issues.apache.org/jira/browse/HADOOP-4539" rel="nofollow" style="color:rgb(102,136,170);text-decoration:none;line-height:1.3em;">HADOOP-4539</a><span style="line-height:1.3em;">.
 更多资料请参考。</span></p>
<p style="line-height:1.3em;font-size:12px;font-family:Verdana, Helvetica, Arial, sans-serif;">
<span style="line-height:1.3em;">同时可以参考<a href="http://share.blog.51cto.com/278008/1033994" rel="nofollow">http://share.blog.51cto.com/278008/1033994</a></span></p>
<br></div>
<h3 style="color:rgb(102,102,102);font-size:14px;font-family:Verdana, Helvetica, Arial, sans-serif;line-height:26px;background-color:rgb(204,204,204);">
<a name="t9" style="color:rgb(51,102,153);"></a>Import Checkpoint<a name="Import_Checkpoint" style="color:rgb(51,102,153);"></a></h3>
<div style="color:rgb(51,51,51);font-size:14px;font-family:Arial;line-height:26px;">
<br></div>
<p style="color:rgb(51,51,51);font-size:12px;font-family:Verdana, Helvetica, Arial, sans-serif;line-height:1.3em;">
<span style="line-height:1.3em;">如果其他备份的image和edits文件丢失，可以导入最新的<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">checkpoint到<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">NameNode
 ，我们可以这么做：</span></span></span></p>
<p style="color:rgb(51,51,51);font-size:12px;font-family:Verdana, Helvetica, Arial, sans-serif;line-height:1.3em;">
<span style="line-height:1.3em;"><span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;"><span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">建立一个<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:26px;"> </span><tt style="color:rgb(51,51,51);line-height:26px;">dfs.namenode.name.dir</tt><span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:26px;"> 配置所指定的文件夹</span></span></span></span></p>
<p style="color:rgb(51,51,51);font-size:12px;font-family:Verdana, Helvetica, Arial, sans-serif;">
<span style="line-height:26px;">在配置文件中设定<span style="color:rgb(51,51,51);font-family:monospace;line-height:26px;">dfs.namenode.checkpoint.dir到一个特殊的文件夹</span></span></p>
<p style="color:rgb(51,51,51);font-size:12px;font-family:Verdana, Helvetica, Arial, sans-serif;">
<span style="line-height:26px;"><span style="color:rgb(51,51,51);font-family:monospace;line-height:26px;">启动<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:26px;">NameNode使用<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:26px;"> </span><tt style="color:rgb(51,51,51);line-height:26px;">-importCheckpoint选项</tt></span></span></span></p>
<p style="color:rgb(51,51,51);"><span style="line-height:26px;"><span style="color:rgb(51,51,51);line-height:26px;"><span style="color:rgb(51,51,51);line-height:26px;"><tt style="color:rgb(51,51,51);line-height:26px;"><span style="color:rgb(51,51,51);line-height:15.59375px;"><span style="font-family:Verdana, Helvetica, Arial, sans-serif;">NameNode
 会上传</span><span style="color:rgb(51,51,51);line-height:15.59375px;"><span style="font-family:Verdana, Helvetica, Arial, sans-serif;">checkpoint从</span><span style="color:rgb(51,51,51);line-height:15.59375px;">dfs.namenode.checkpoint.dir所指定的目录，并保存到<span style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:12px;color:rgb(51,51,51);line-height:15.59375px;"> </span><tt style="color:rgb(51,51,51);line-height:15.59375px;">dfs.namenode.name.dir所指定的目录，如果已经有一个<span style="color:rgb(51,51,51);line-height:15.59375px;"><span style="font-family:Verdana, Helvetica, Arial, sans-serif;">image在</span><span style="color:rgb(51,51,51);line-height:15.59375px;">dfs.namenode.name.dir目录下，<span style="font-family:SimSun;font-size:12px;">那么</span><span style="color:rgb(51,51,51);line-height:15.59375px;"><span style="font-family:SimSun;font-size:12px;">NameNode将会失败，<span style="color:rgb(102,102,102);line-height:24px;text-align:justify;"><span style="color:rgb(51,51,51);line-height:15.59375px;">NameNode会<span style="color:rgb(102,102,102);line-height:24px;text-align:justify;">验证</span></span>dfs.namenode.checkpoint.dir
 NameNode的<span style="color:rgb(51,51,51);line-height:15.59375px;">image的</span>一致性,但不以任何方式修改它</span></span></span></span></span></tt></span></span></span><br></tt></span></span></span></p>
<p style="color:rgb(51,51,51);font-size:12px;font-family:Verdana, Helvetica, Arial, sans-serif;line-height:1.3em;">
</p>
<div class="section" style="color:rgb(51,51,51);font-size:13px;">
<h3 style="font-family:Verdana, Helvetica, Arial, sans-serif;color:rgb(102,102,102);background-color:rgb(204,204,204);font-size:14px;">
Rebalancer<a name="Rebalancer"></a></h3>
<p style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:1.3em;font-size:12px;">
<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">HDFS中的数据在各个<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">DataNode之间</span>并不总是一致的，一个常见的原因就是添加一些新的<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">DataNode到集群里面，当安置一些新的块（数据文件通常是以一些块的形式储存的），<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">NameNode可以通过一些参数选择<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">DataNodes去接受这些块，具体如下：</span></span></span></span><br></p>
<p style="font-size:12px;"><span style="color:rgb(51,51,51);"><span style="color:rgb(51,51,51);"><span style="color:rgb(51,51,51);"><span style="color:rgb(51,51,51);"></span></span></span></span></p>
<ul style="font-family:Verdana, Arial, Helvetica, sans-serif;line-height:18px;"><li style="list-style: !important;"><span style="background-color:rgb(245,250,254);">正</span><span style="background-color:rgb(255,255,255);">在写该Block的数据节点会保留一份该Block</span></li><li style="list-style: !important;"><span style="background-color:rgb(255,255,255);">要把该Block分散到不同机架，达到容灾需求</span></li><li style="list-style: !important;"><span style="background-color:rgb(255,255,255);">将Block存一份到与正在写该Block所属文件的节点同机架的节点，以减少机架间的网络传输</span></li><li style="list-style: !important;"><span style="background-color:rgb(255,255,255);">HDFS数据在集群中平均分配</span></li></ul><div><span style="font-family:Verdana, Arial, Helvetica, sans-serif;"><span style="line-height:18px;"></span></span>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;line-height:18px;">
<span style="background-color:rgb(255,255,255);">基于很多权衡考虑，数据在DataNodes间可能不是平均分配。HDFS提供一个分析Block位置和在节点间均衡数据的管理工具。Rebalancer的简要介绍在<a href="http://issues.apache.org/jira/browse/HADOOP-1652" rel="nofollow" style="color:#000000;">HADOOP-1652</a>的一个<a href="http://issues.apache.org/jira/secure/attachment/12368261/RebalanceDesign6.pdf" rel="nofollow" style="color:#000000;">PDF</a>中。</span></p>
</div>
<p></p>
</div>
<div class="section" style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<h3 style="color:rgb(102,102,102);background-color:rgb(204,204,204);font-size:14px;">
Rack Awareness(机架感知)</h3>
<p style="line-height:1.3em;font-size:12px;">大型的hadoop集群会安排在同一个机架上的的不同node的交互多余分布在不同机架上的交互。另外<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">NameNode会放置一些block在不同的机架上用于容错，管理员可以通过配置变量<span style="color:rgb(51,51,51);font-family:monospace;line-height:15.59375px;">net.topology.script.file.name来决定哪个机架的节点，当这个脚本被配置之后，每个节点都会运行这个脚本确定它对应的机架id，默认的安装会假设所用的node在一个机架上。这个功能和描述可以参考<a class="externalLink" href="https://issues.apache.org/jira/browse/HADOOP-692" rel="nofollow" style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:1.3em;text-decoration:none;color:rgb(102,136,170);">HADOOP-692</a>中的PDF。</span></span></p>
<p style="line-height:1.3em;font-size:12px;">注（非原文档）：可以参考以下链接：</p>
<p style="line-height:1.3em;font-size:12px;"><a href="http://blog.csdn.net/hjwang1/article/details/6592714" rel="nofollow">http://blog.csdn.net/hjwang1/article/details/6592714</a></p>
</div>
<div class="section" style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;">
<h3 style="font-size:14px;color:rgb(102,102,102);background-color:rgb(204,204,204);">
Safemode<a name="Safemode"></a></h3>
<p style="line-height:1.3em;">当<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">NameNode启动时会从fsimage和edits日志文件中加载文件系统的状态，并且等待<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">DataNode报考block<span style="font-size:12px;">的情况，DataNode不会提早进行任何的复制操作<span style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">，在此期间<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">NameNode处于<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">Safemode。<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">Safemode对于<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;font-size:12px;line-height:15.59375px;">NameNode来说是一个只读模式，他不允许任何的任何修改，一般当<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;font-size:12px;line-height:15.59375px;">DataNode报告了大多数系统的block是活动状态后<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;font-size:12px;line-height:15.59375px;">NameNode会自动离开<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;font-size:12px;line-height:15.59375px;">Safemode，如有需要，<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">HDFS
 可以通过<span style="color:rgb(51,51,51);font-family:monospace;line-height:15.59375px;">bin/hadoop dfsadmin -safemode命令启动<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;font-size:12px;line-height:15.59375px;">Safemode，<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">NameNode
 的前端页面是否显示取决于<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">Safemode是开还是关，更多的细节和配置请参考一个<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;"> </span><tt style="color:rgb(51,51,51);line-height:15.59375px;">setSafeMode()的</tt>API文档。</span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></p>
<p style="line-height:1.3em;"><span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;"><span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;"><span style="font-size:12px;"><span style="color:rgb(51,51,51);font-family:Arial;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;"><span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;"><span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;"><span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;font-size:12px;line-height:15.59375px;"><span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;font-size:12px;line-height:15.59375px;"><span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;font-size:12px;line-height:15.59375px;"><span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;font-size:12px;line-height:15.59375px;"><span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;"><span style="color:rgb(51,51,51);font-family:monospace;line-height:15.59375px;"><span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;font-size:12px;line-height:15.59375px;"><span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;"><span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;"><span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">注（非原文档）：可以参考以下链接：</span><br></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></p>
<p style="line-height:1.3em;"><a href="http://blog.csdn.net/hguisu/article/details/7261480" rel="nofollow">http://blog.csdn.net/hguisu/article/details/7261480</a><br></p>
</div>
<div class="section" style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<h3 style="color:rgb(102,102,102);background-color:rgb(204,204,204);font-size:14px;">
fsck<a name="fsck"></a></h3>
<p style="line-height:1.3em;font-size:12px;"><span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">HDFS支持fsck命令用来检测系统的一致性，他被设计用来报告众多文件的问题，例如，一个文件缺少block或者存在的block低于备份块数。不同于传统的为了普通文件系统的工具，这个命令不会纠正发现的错误，一般来说<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">NameNode
 会自动的纠正大多数可以恢复的错误，默认的fsck会忽略打开的文件，但是提供了一个选项可以选择所有的文件。<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">fsck 命令并不是一个hadoop shell 命令，他可以以<span style="color:rgb(51,51,51);font-family:monospace;line-height:15.59375px;">bin/hadoop
 fsck</span>的方式运行，他可以运行与整个文件系统或一堆文件上。</span></span></span></p>
</div>
<div class="section" style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<h3 style="color:rgb(102,102,102);background-color:rgb(204,204,204);font-size:14px;">
fetchdt<a name="fetchdt"></a></h3>
<p style="line-height:1.3em;font-size:12px;"><span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">HDFS 支持<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">fetchdt
 命令用来获取代理令牌并保存到本地文件系统，这个令牌可以用来进入安全的服务器（比如Namenode）从一个不安全的客户端，一般利用RPC或者https（<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">通过 Kerberos</span>）在需要出示令牌之前去获得令牌。这个命令并不是一个<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">hadoop
 shell 命令,他可以通过<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;"> </span><tt style="color:rgb(51,51,51);line-height:15.59375px;">bin/hadoop fetchdt DTfile的方式运行，如果在命令后没有加载任何的<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">Kerberos纳闷会通过<span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;"> </span><tt style="color:rgb(51,51,51);line-height:15.59375px;">HADOOP_TOKEN_FILE_LOCATION</tt><span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;"> 这个配置去找令牌文件。</span></span></tt></span></span></span><br></p>
<p style="line-height:1.3em;font-size:12px;"><span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;"><span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;"><span style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;"><tt style="color:rgb(51,51,51);line-height:15.59375px;">注：可以参考<a href="http://blog.csdn.net/nachuang/article/details/8729729" rel="nofollow">http://blog.csdn.net/nachuang/article/details/8729729</a></tt></span></span></span></p>
</div>
<div class="section" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<h3 style="color:rgb(102,102,102);background-color:rgb(204,204,204);font-size:14px;">
Recovery Mode<a name="Recovery_Mode"></a></h3>
<p style="line-height:1.3em;font-size:12px;"><span style="background-color:rgb(255,255,255);">一般会配置多个元数据的位置如果一个被毁坏，可以从其他位置上读取，但是如果仅有的一个活动的位置被损坏了呢，在这个case里面就需要一个特殊的NameNode启动方式，叫做<span style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">Recovery
 mode这种模式能使你恢复大部分数据。</span></span></p>
<p style="line-height:1.3em;font-size:12px;"><span style="background-color:rgb(255,255,255);"><span style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">你可以以<span style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">recovery
 mode的方式</span></span>启动<span style="line-height:1.3em;">Namenode像这样：<span style="font-family:monospace;line-height:15.59375px;">namenode -recover</span></span></span></p>
<p style="line-height:1.3em;font-size:12px;"><span style="line-height:1.3em;background-color:rgb(255,255,255);"><span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;">在恢复模式下,NameNode将在命令行交互提示您可能采取的行动,帮助恢复你的数据。</span><br></span></p>
<p style="line-height:1.3em;font-size:12px;"><span style="line-height:1.3em;"><span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;background-color:rgb(255,255,255);"><span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;">如果你不想被提示,你可以给-
 force选项。<span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;">这个选项将迫使恢复模式总是选择第一个选择。<span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;">通常情况下,这将是最合理的选择。</span></span></span><br></span></span></p>
<p style="line-height:1.3em;font-size:12px;"><span style="line-height:1.3em;"><span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;"><span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;"><span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;"><span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;"><span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;background-color:rgb(255,255,255);">因为恢复模式可以让你丢失数据,您应该备份你的编辑日志和fsimage之前使用它。</span></span></span></span></span></span></p>
</div>
<div class="section" style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<h3 style="color:rgb(102,102,102);background-color:rgb(204,204,204);font-size:14px;">
Upgrade and Rollback<a name="Upgrade_and_Rollback"></a></h3>
<p style="line-height:1.3em;font-size:12px;"></p>
<p style="line-height:15px;font-family:Verdana, Helvetica, sans-serif;font-size:13px;">
当在一个已有集群上升级Hadoop时，像其他的软件升级一样，可能会有新的bug或一些会影响到现有应用的非兼容性变更出现。在任何有实际意义的HDSF系统上，丢失数据是不被允许的，更不用说重新搭建启动HDFS了。HDFS允许管理员退回到之前的Hadoop版本，并将集群的状态回滚到升级之前。更多关于HDFS升级的细节在<a href="http://wiki.apache.org/hadoop/Hadoop%20Upgrade" rel="nofollow" style="color:rgb(0,153,153);">升级wiki</a>上可以找到。HDFS在一个时间可以有一个这样的备份。在升级之前，管理员需要用<span class="codefrag" style="font-family:'Courier New', Courier, monospace;font-size:14px;">bin/hadoop
 dfsadmin -finalizeUpgrade</span>（升级终结操作）命令删除存在的备份文件。下面简单介绍一下一般的升级过程：</p>
<ul style="font-family:Verdana, Helvetica, sans-serif;font-size:13px;"><li>升级 Hadoop 软件之前，请检查是否已经存在一个备份，如果存在，可执行升级终结操作删除这个备份。通过<span class="codefrag" style="font-family:'Courier New', Courier, monospace;font-size:14px;">dfsadmin -upgradeProgress status</span>命令能够知道是否需要对一个集群执行升级终结操作。</li><li>停止集群并部署新版本的Hadoop。</li><li>使用<span class="codefrag" style="font-family:'Courier New', Courier, monospace;font-size:14px;">-upgrade</span>选项运行新的版本（<span class="codefrag" style="font-family:'Courier New', Courier, monospace;font-size:14px;">bin/start-dfs.sh
 -upgrade</span>）。</li><li>在大多数情况下，集群都能够正常运行。一旦我们认为新的HDFS运行正常（也许经过几天的操作之后），就可以对之执行升级终结操作。注意，在对一个集群执行升级终结操作之前，删除那些升级前就已经存在的文件并不会真正地释放DataNodes上的磁盘空间。</li><li>如果需要退回到老版本，
<ul><li>停止集群并且部署老版本的Hadoop。</li><li>用回滚选项启动集群（<span class="codefrag" style="font-family:'Courier New', Courier, monospace;font-size:14px;">bin/start-dfs.h -rollback</span>）。</li></ul></li></ul></div>
<div class="section" style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<h3 style="color:rgb(102,102,102);background-color:rgb(204,204,204);font-size:14px;">
File Permissions and Security<a name="File_Permissions_and_Security"></a></h3>
<p style="line-height:1.3em;font-size:12px;"><span style="font-family:Verdana, Helvetica, sans-serif;font-size:13px;line-height:15px;">这里的文件权限和其他常见平台如Linux的文件权限类似。目前，安全性仅限于简单的文件权限。启动NameNode的用户被视为HDFS的超级用户。HDFS以后的版本将会支持网络验证协议（比如Kerberos）来对用户身份进行验证和对数据进行加密传输。具体的细节请参考</span><a href="http://hadoop.apache.org/docs/r1.0.4/cn/hdfs_permissions_guide.html" rel="nofollow" style="color:rgb(0,153,153);font-family:Verdana, Helvetica, sans-serif;font-size:13px;line-height:15px;">权限使用管理指南</a><span style="font-family:Verdana, Helvetica, sans-serif;font-size:13px;line-height:15px;">。</span></p>
</div>
<div class="section" style="color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<h3 style="color:rgb(102,102,102);background-color:rgb(204,204,204);font-size:14px;">
Scalability<a name="Scalability"></a></h3>
<p style="line-height:1.3em;font-size:12px;"><span style="font-family:Verdana, Helvetica, sans-serif;font-size:13px;line-height:15px;">现在，Hadoop已经运行在上千个节点的集群上。</span><a href="http://wiki.apache.org/hadoop/PoweredBy" rel="nofollow" style="color:rgb(0,153,153);font-family:Verdana, Helvetica, sans-serif;font-size:13px;line-height:15px;">Powered
 By Hadoop</a><span style="font-family:Verdana, Helvetica, sans-serif;font-size:13px;line-height:15px;">页面列出了一些已将Hadoop部署在他们的大型集群上的组织。HDFS集群只有一个NameNode节点。目前，NameNode上可用内存大小是一个主要的扩展限制。在超大型的集群中，增大HDFS存储文件的平均大小能够增大集群的规模，而不需要增加NameNode的内存。默认配置也许并不适合超大规模的集群。</span><a href="http://wiki.apache.org/hadoop/FAQ" rel="nofollow" style="color:rgb(0,153,153);font-family:Verdana, Helvetica, sans-serif;font-size:13px;line-height:15px;">Hadoop
 FAQ</a><span style="font-family:Verdana, Helvetica, sans-serif;font-size:13px;line-height:15px;">页面列举了针对大型Hadoop集群的配置改进。</span></p>
</div>
<div class="section" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<h3 style="color:rgb(102,102,102);background-color:rgb(204,204,204);font-size:14px;">
Related Documentation<a name="Related_Documentation"></a></h3>
<p style="line-height:1.3em;font-size:12px;"></p>
<p style="color:rgb(51,51,51);line-height:15px;font-family:Verdana, Helvetica, sans-serif;font-size:13px;">
这个用户手册给用户提供了一个学习和使用HDSF文件系统的起点。本文档会不断地进行改进，同时，用户也可以参考更多的Hadoop和HDFS文档。下面的列表是用户继续学习的起点：</p>
<p></p>
<ul style="font-family:Verdana, Helvetica, sans-serif;font-size:13px;"><li><span style="color:#000000;"><a href="http://hadoop.apache.org/" rel="nofollow">Hadoop官方主页</a>：所有Hadoop相关的起始页。</span></li></ul><ul style="font-family:Verdana, Helvetica, sans-serif;font-size:13px;"><li><span style="color:#000000;">Hadoop<a href="http://wiki.apache.org/hadoop/FrontPage" rel="nofollow">Wiki</a>：Hadoop Wiki文档首页。这个指南是Hadoop代码树中的一部分，与此不同，Hadoop Wiki是由Hadoop社区定期编辑的。</span></li></ul><ul style="font-family:Verdana, Helvetica, sans-serif;font-size:13px;"><li>Hadoop Wiki上的<a href="http://wiki.apache.org/hadoop/FAQ" rel="nofollow">FAQ</a>。</li></ul><ul style="font-family:Verdana, Helvetica, sans-serif;font-size:13px;"><li>Hadoop <a href="http://hadoop.apache.org/core/docs/current/api/" rel="nofollow">JavaDoc API</a>。</li></ul><ul style="font-family:Verdana, Helvetica, sans-serif;font-size:13px;"><li>Hadoop用户邮件列表：<a href="mailto:core-user@hadoop.apache.org" rel="nofollow">core-user[at]hadoop.apache.org</a>。</li></ul><ul style="font-family:Verdana, Helvetica, sans-serif;font-size:13px;"><li>查看<span class="codefrag" style="font-family:'Courier New', Courier, monospace;font-size:14px;">conf/hadoop-default.xml</span>文件。这里包括了大多数配置参数的简要描述。</li></ul><ul style="font-family:Verdana, Helvetica, sans-serif;font-size:13px;"><li><span style="color:#000000;"><a href="http://hadoop.apache.org/docs/r1.0.4/cn/commands_manual.html" rel="nofollow">命令手册</a>：命令使用说明。</span></li></ul><p></p>
</div>
<p style="color:rgb(51,51,51);">后几部分严重怀疑是不是从以前版本抄过来的，HDFS只有一个NameNode指的是没有集群还是没有实现HA，2.0版本的重要特性就是实现NameNode的双机，这里还是列为一个问题，不是很清楚，后面在一点点的研究吧，现在hadoop各种资料太乱了，各个版本混杂包括官方的资料也是如此，我个人是根据官方的文档进行学习的，由于刚起步对于各个版本的历史和问题不很清楚，也希望可以同一些朋友一起交流学习，目前我学习的版本是2.2GA，希望有兴趣的同学一起探讨（QQ：878236523），<span style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:1.3em;">最后附上Hadoop的Jira的URL:</span><a href="https://issues.apache.org/jira/browse/HADOOP" rel="nofollow" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:19px;background-color:rgb(254,254,242);">https://issues.apache.org/jira/browse/HADOOP</a>，对于HDFS和YARN我会将<a href="http://hadoop.apache.org/docs/current/" rel="nofollow">http://hadoop.apache.org/docs/current/</a>中相关的文档进行翻译和学习.</p>
</div>
<div style="color:rgb(51,51,51);font-size:14px;font-family:Arial;line-height:26px;">
<br></div>
</div>
            </div>
                </div>