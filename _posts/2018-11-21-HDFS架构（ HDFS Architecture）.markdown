---
layout:     post
title:      HDFS架构（ HDFS Architecture）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2 style="font-size:17px;color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;">
HDFS架构</h2>
<div>
<div class="section">
<h2 style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:17px;color:rgb(51,51,51);">
<a name="t1" style="color:rgb(51,102,153);"></a>介绍</h2>
<p style="line-height:1.3em;"><span style="font-family:SimSun;">Hadoop分布式文件系统（HDFS）是一个运行在普通商用服务器上的分布式文件系统。他和很多的分布式文件系统类似，但是也有很显著的不同。HDFS是一个高容错并且可以部署在廉价机器上运行的系统。HDFS适用于大型系统提供了应用数据的高吞吐量支持。<span style="line-height:24px;">HDFS降低了流式访问文件系统数据的POSIX协议相关的一些要求。HDFS一开始是设计成为Apache
 Nutch网络搜索引擎项目的基础架构. 而HDFS也是Apache Hadoop Core项目的部分. 详情参见项目链接</span><a href="http://hadoop.apache.org/" rel="nofollow" style="color:rgb(51,102,153);text-decoration:none;line-height:24px;">http://hadoop.apache.org</a><span style="line-height:24px;">。</span></span></p>
</div>
<div class="section" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<h2 style="font-size:17px;color:rgb(51,51,51);"><a name="t2" style="color:rgb(51,102,153);"></a></h2>
<h2 style="font-family:Arial;line-height:24px;color:rgb(51,51,51);">
<a name="t3" style="color:rgb(51,102,153);"></a><span style="font-size:17px;font-family:Verdana, Helvetica, Arial, sans-serif;">设想与目标</span></h2>
</div>
<div class="section">
<h2 style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:17px;color:rgb(51,51,51);">
<a name="t4" style="color:rgb(51,102,153);"></a>硬件错误</h2>
<p style="line-height:1.3em;"><span style="font-family:SimSun;"><span style="line-height:20.796875px;">硬件错误时有发生，并非意外。一个HDFS实例可能包含了成千上万台服务器, 它们各自存储了该文件系统的一部分数据。可以说，分布式系统中可能连接了数量极多的节点，而每个节点由于不同的可能性发生故障，这意味着在HDFS中总是</span><span style="line-height:1.3em;">有些节点不能正常提供服务。因此，自动和快速的检查并修复故障，是设计HDFS架构的一个核心目标.</span></span></p>
</div>
<div class="section">
<h2 style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:17px;color:rgb(51,51,51);">
<a name="t5" style="color:rgb(51,102,153);"></a>流数据访问</h2>
<p style="line-height:1.3em;"><span style="line-height:20.796875px;"><span style="font-family:SimSun;">运行于HDFS之上的应用会流式访问它们的数据集。They are not general purpose applications that typically run on general purpose file systems. HDFS被设计为更适合批处理数据，而不是与用户有频繁交互的场景.
 其侧重点为数据访问的高吞吐量，而不是低延迟性。 POSIX规定硬件方面导到一定要求，而以HDFS为存储目标的应用并不会用到这些资源；因此POSIX中一些关键的语义已经被更改，以提高数据吞吐率。</span></span></p>
</div>
<div class="section">
<h2 style="font-size:17px;font-family:Verdana, Helvetica, Arial, sans-serif;color:rgb(51,51,51);">
<a name="t6" style="color:rgb(51,102,153);"></a>大数据</h2>
<p style="line-height:1.3em;"><span style="font-family:SimSun;"><span style="line-height:20.796875px;">运行在HDFS之上的应用一般都拥有大量数据. 一个HDFS文件通常大小在GB到TB之间。因此，HDFS理应很好地支持大文件. 它在一个集群上</span><span style="line-height:1.3em;">应该能提供相当高的数据带宽，并能能将数据拓展到数百个节点.
 在一个应用实例中. 它要支持数以百万计的数据文件。</span></span></p>
</div>
<div class="section">
<h2 style="font-size:17px;font-family:Verdana, Helvetica, Arial, sans-serif;color:rgb(51,51,51);">
<a name="t7" style="color:rgb(51,102,153);"></a>简单一致性模型</h2>
<p style="line-height:1.3em;"><span style="line-height:20.796875px;"><span style="font-family:SimSun;">HDFS应用要求文件访问为write-once-read-many模型. 一个文件被创建，写入并关闭后，就不应该再作修改。这个原则简化了文件的数据一致性问题，并使得数据能被高速率地访问。一个Map/Reduce或者web crawler应用就很适合这种模型. 现在已有计划在将来实现追加数据到文件中的功能。</span></span></p>
</div>
<div class="section">
<h2 style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:17px;color:rgb(51,51,51);">
<a name="t8" style="color:rgb(51,102,153);"></a>“转移计算比移动数据本身更划算”</h2>
<p style="line-height:1.3em;"><span style="line-height:20.796875px;"><span style="font-family:SimSun;">一个应用所需的计算，数据离应用越近该计算就越高效；对于大数据集的应用场景来说更是如此. 这最大程度地减少了网络开销并增加了系统整体的数据吞吐量。这个理念就是，将计算模块迁移到数据所在的节点中，会比将数据传输到应用所在的节点上的性能显然要好得多。而HDFS也提供了将应用本身移动到相关数据所在节点上的接口。</span></span></p>
</div>
<div class="section">
<h2 style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:17px;color:rgb(51,51,51);">
<a name="t9" style="color:rgb(51,102,153);"></a>异构硬件及软件平台间的可移植性</h2>
<p style="line-height:1.3em;"><span style="font-family:SimSun;"><span style="line-height:20.796875px;">HDFS可以轻易</span><span style="line-height:1.3em;">在不同平台间做到移植。这有利于HDFS成为一个处理拥有大数据的应用的</span><span style="line-height:20.796875px;">首选</span><span style="line-height:1.3em;">平台，并被广泛使用。</span></span></p>
</div>
<div class="section">
<h2 style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:17px;color:rgb(51,51,51);">
<a name="t10" style="color:rgb(51,102,153);"></a>NameNode和DataNodes</h2>
<div><span style="line-height:20.796875px;"><span style="font-family:SimSun;">HDFS采用了master/slave架构设计。一个HDFS集群包含了一个NameNode, 也就是一个管理文件系统命名空间及用户对数据的访问相关信息的master服务节点. 另外，包含了一定数量的DataNode，通常集群中每一个节点都管理并存储了整体数据的一部分。HDFS对外暴露了文件系统的命名空间，同时允许用户数据存储于其中。一个文件通常会被分割成多个block同时存储在部分DataNode中。NameNode会执行类似于打开，关闭或重命名文件夹或文件的操作。同时它也会决定block在DataNote群中的存储节点。
 DataNode的任务是响应文件系统的用户请求并提供数据的读写服务。DataNode同时会执行block的创建，删除和基于NameNode指示的冗余备份等工作。</span></span></div>
<span style="font-family:Verdana, Helvetica, Arial, sans-serif;"><img src="http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/images/hdfsarchitecture.png" alt="HDFS Architecture" style="border:none;"></span>
<p style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:1.3em;"><br></p>
<p style="line-height:1.3em;"></p>
<p style="line-height:1.3em;">
<span><span style="font-family:SimSun;">NameNode和DataNode都是为了设计成运行在商业机器上的软件体。这些机器一般都安装了GNU/Linux操作系统. HDFS采用Java实现; 任何装有Java运行环境的机器都可以配置为NameNode或者DataNode。采用高可移植性的Java语言意味着HDFS可以部署在绝大部分类型的机器上。一种典型的集群部署为:专门用一台机器作为NameNode来管理相关的资源和任务调度；其他所有的机器作为DataNode来执行相关的计算及存储任务。HDFS架构并不排除在同一台机器上部署了多个DataNode，但这样的场景并不多见。</span></span></p>
<p style="line-height:1.3em;">
<span><span style="font-family:SimSun;">只存在一个NameNode实例的模型很大程度简化了系统的架构。而NameNode则是一个仲裁者并维护了所有的HDFS元数据，通过这样的方式使得用户数据的传输不用经过NameNode。</span></span></p>
</div>
<div class="section" style="font-family:Verdana, Helvetica, Arial, sans-serif;">
<h2 style="font-size:17px;color:rgb(51,51,51);"><a name="t11" style="color:rgb(51,102,153);"></a>文件系统命名空间</h2>
<p style="line-height:1.3em;"></p>
<p style="font-family:Arial;line-height:1.3em;">
<span style="font-family:'Microsoft YaHei';">HDFS支持传统的分层文件组织。用户或者应用可以在这些文件目录下创建子目录并存储文件数据。HDFS命名空间层次结构与现有大多数的文件系统相似: 用户可以创建，删除，移动或重命名文件。HDFS目前还不支持用户对磁盘配额及修改访问权限，也不支持硬链接或软链接。然而并不排除将来加入这些特性的可能。</span></p>
<p style="font-family:Arial;line-height:1.3em;">
<span style="font-family:'Microsoft YaHei';">我们知道NameNode是用来维护文件系统命名空间的，它会记录对命名空间及其属性所有的改动。用户可以在NameNode中指定数据文件有多少份备份，文件的备份数量称为replication。</span></p>
</div>
<div class="section">
<h2 style="font-size:17px;font-family:Verdana, Helvetica, Arial, sans-serif;color:rgb(51,51,51);">
<a name="t12" style="color:rgb(51,102,153);"></a>数据的冗余备份</h2>
<p style="line-height:1.3em;"></p>
<p style="line-height:1.3em;">
<span><span style="font-family:SimSun;">HDFS要求在一个大集群中对跨节点的数据存储有高可靠性。文件会被存储为顺序的block，除了最后一个block其他的size都相等。这些block通过冗余备份来增强容错性，而备份数量和size都可以在配置文件中指定。创建了完整的冗余文件后，该文件不用担心同步的问题，因为HDFS是一次写入模式，并且在任何时候只有一个进程在写。</span></span></p>
<p style="line-height:1.3em;">
<span><span style="font-family:SimSun;">NameNode对block的复制有绝对的决策权。它会从各节点中周期性地接收Heartbeat信号和Blockreport信息。Heartbeat表明该DataNode还可以正常工作，而Blockreport则包含了一个该节点所有block的列表。</span></span></p>
<span style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;"><img src="http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/images/hdfsdatanodes.png" alt="HDFS DataNodes" style="border:none;"></span></div>
<div class="section">
<h2 style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:17px;color:rgb(51,51,51);">
<a name="t13" style="color:rgb(51,102,153);"></a>副本放置之第一步</h2>
<p style="line-height:1.3em;"></p>
<p style="line-height:1.3em;">
<span style="font-family:SimSun;">由于HDFS的可靠性和高性能，副本位置的选择是严格的。经过优化的副本放置可以让HDFS区别于其他分布式文件系统，而该项特性需要不断的调优和实践。副本放置采用机架感知策略目的是为了提高数据的可靠性，可用性和网络带宽利用率。 采用这种策略的短期目标是在生产系统上进行验证，同时了解更多关于它的行为，并建立一系列测试来研究更复杂的策略。</span></p>
<p style="line-height:1.3em;">
<span style="font-family:SimSun;">许多HDFS实例通常是运行在由跨机架组成的计算机集群中，而不同机架上的两个节点一般要通过交换机来进行通信。通常来说，同一机架上的机器的网络带宽要比不同机架的大很多。</span></p>
<p style="line-height:1.3em;">
<span style="font-family:SimSun;">NameNode通过 <a href="http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/ClusterSetup.html#HadoopRackAwareness" rel="nofollow" style="color:rgb(102,136,170);text-decoration:none;">Hadoop
 Rack Awareness</a>所阐述的过程来决定DataNode所属的rack ID。一个简单的但未经优化的策略是将副本放到单独的机架上。这样可以避免由于整台机架崩溃引起的数据丢失，同时在进行读数据时利用到多个机架的带宽。这个策略甚至可以将副本分布在不同机器上以实现均衡负载。然而，这也增加了读操作的开销，因为写意味着需要将block数据传输到不同的机架上。</span></p>
<p style="line-height:1.3em;">
<span style="font-family:SimSun;">一般来副本数量为3时，HDFS的策略为将一份副本放在本机架的与正本相同的节点上，另一份放在同一机架的不同节点上，最后一份会放在另一机架的节点上。这种策略会减少机架间写操作的通讯开销，从而提高了写的性能。整个机架崩溃要比节点发生故障的概率小的多，因此该策略不会影响数据的可靠性和稳定性。另外它也降低了读数据时的网络开销，因为一个block数据是放置在两个独立的机架上而不是三个。使用这种策略，一个文件的副本并不均匀地分布在不同的机架:三分之一的副本在同一个节点上，三分之二在同一个机架上，剩下三分之一均匀地分布在其余的机架。不需要通过数据可靠性和读性能的折中调优，写性能也得到了提高。</span></p>
<p style="line-height:1.3em;">
<span style="font-family:SimSun;">上述描述的作为默认配置的副本防止策略正在紧张开发中。</span></p>
</div>
<div class="section">
<h2 style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:17px;color:rgb(51,51,51);">
<a name="t14" style="color:rgb(51,102,153);"></a>副本的选择</h2>
<p style="line-height:1.3em;"><span style="line-height:18.1875px;"><span style="font-family:SimSun;">为了最小化集群整体的网络带宽开销及读延时，HDFS尝试让发起读数据请求的客户端能从离他最近的一份副本获取数据。如果在发起读请求的client同一机架上有对应的副本，该副本首先会被HDFS用来完成数据的读取。如果副本跨越了HDFS的多个数据中心，存储于本地数据中心的副本会优先于远程副本来作为数据的访问源</span></span></p>
</div>
<div class="section">
<h2 style="font-size:17px;font-family:Verdana, Helvetica, Arial, sans-serif;color:rgb(51,51,51);">
<a name="t15" style="color:rgb(51,102,153);"></a>安全模式</h2>
<p style="line-height:1.3em;"><span style="line-height:18.1875px;"><span style="font-family:SimSun;">集群启动期间，NameNode节点会进入一个成为安全模式(Safemode)的特殊状态。这种状态下不会做数据的备份，而NameNode会从DataNodes接收倒Heartbeat和Blockreport信息。一个Blockreport包含了该DataNode所存储的block列表。每个block指定了一个最少副本数量值。当数据block副本的最小序号通过NameNode核对时，我们认为该block是能安全复制的。在经过NameNode核对数据block的备份配置完成后(另外等待30秒)，NameNode退出Safemode状态。然后NameNode会判断并列出副本数仍然少于配置要求的份数的block列表，如果列表不为空，那么它就会将这些block拷贝到其他DataNode.</span></span></p>
</div>
<div class="section">
<h2 style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:17px;color:rgb(51,51,51);">
<a name="t16" style="color:rgb(51,102,153);"></a>File System元数据持久化</h2>
<p style="line-height:1.3em;"></p>
<p style="line-height:1.3em;">
<span style="font-family:SimSun;">HDFS的命名空间数据保存在NameNode中。NameNode使用了名为EditLog的事务日志来持续记录了文件系统元数据所发生的每一个变化。例如，在HDFS中创建新文件时，NameNode会往EditLog插入一条记录来说明此操作。同样地，修改文件的副本系数时也会在EditLog记录这样的操作。NameNode将EditLog存储在本地操作系统的文件系统的一个文件中。 整个文件系统的命名空间，包括block到file和file system
 property之间的映射关系, 保存在一个成为FsImage的文件中。此FsImage也是作为一个文件形式存储在NameNode本地文件系统中。</span></p>
<p style="line-height:1.3em;">
<span style="font-family:SimSun;">NameNode在内存中保存了一份整个文件系统命名空间和文件Blockmap的镜像。这份重要的元数据项是被压缩保存的，因此一个拥有4GB RAM的NameNode可以支持大量的<span style="line-height:1.3em;">文件和目录信息。NameNode启动后，它会从磁盘中读取FsImage及EditLog，应用EditLog中所有的事务到存在于内存中的FsImage文件对象，然后将版本较新的这个FsImage文件写入磁盘，之后EditLog就可以被删除了，因为所有的事务已经被持久化到FsImage中。该过程称为checkpoint。当前的实现机制为：一个checkpoint只发生在NameNode启动的时候;将来会逐渐支持周期性的checkpoint过程。</span></span></p>
<div style="line-height:26px;"><span style="font-family:SimSun;">DataNode在本地文件系统中保存了HDFS数据，但它并不知道HDFS保存的具体内容，只是把HDFS数据保存在本地的独立的block中。DataNode在同一目录不会创建所有的文件，相反它会启发式的决定每个目录创建最合适的文件数和子目录数。一开始就在同一目录下初始化所有文件是不推荐的，因为本地文件系统可能不支持一个目录中创建过多数量的文件。当一个DataNode启动时，它会扫描本地文件系统，生成所有HDFS数据块的一个列表，然后向NameNode发送一个报告(这个报告称为Blockreport)。</span></div>
</div>
<div class="section">
<h2 style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:17px;color:rgb(51,51,51);">
<a name="t17" style="color:rgb(51,102,153);"></a>通信协议</h2>
<p style="line-height:1.3em;"><span style="font-family:SimSun;"><span style="line-height:18.1875px;">所有HDFS的通信协议都基于TCP/IP协议，客户端会与NameNode机器上的TCP端口建立连接，并通过ClientProtocol协议与NameNode进行会话。而DataNode节点则通过DataNode Protocol与</span><span style="line-height:1.3em;">NameNode进行通信。远程过程调用(RPC)同时封装了Client
 Protocol和DataNode Protocol。而NameNode从不启动任何RPC，相反它会响应来自DataNode或客户端的RPC。</span></span></p>
</div>
<div class="section" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<h2 style="font-size:17px;color:rgb(51,51,51);"><a name="t18" style="color:rgb(51,102,153);"></a>稳健性</h2>
<p style="line-height:1.3em;font-size:12px;"><span style="font-family:'Microsoft YaHei';font-size:14px;line-height:18.1875px;">HDFS的首要目标是即使出现故障时仍然可以安全地存储数据。三种故障的类型可以概括为NameNode故障，DataNode故障和网络中断。</span></p>
</div>
<div class="section">
<h2 style="font-family:Arial;font-size:14px;color:rgb(51,51,51);">
<a name="t20" style="color:rgb(51,102,153);"></a><span style="font-family:'Microsoft YaHei';"></span></h2>
<h2 style="font-family:Arial;line-height:26px;color:rgb(51,51,51);">
<a name="t21" style="color:rgb(51,102,153);"></a><span style="font-family:'Microsoft YaHei';font-size:14px;">数据磁盘故障, 心跳及重新复制</span></h2>
<h2 style="color:rgb(51,51,51);">
</h2><div style="line-height:26px;">
<p style="font-family:Arial;font-size:14px;line-height:1.3em;">
<span style="font-family:'Microsoft YaHei';font-weight:normal;">每个DataNode会定期向NameNode发送一个心跳信息。如果一部分DataNode与NameNode断开了连接，NameNode会根据心跳信息来觉察到。NameNode会将最近没有发送心跳信息的DataNode标记为dead状态，并且不会再像它们发送任何的IO请求。一个节点被标记为dead，此时所有它那里注册过的任何信息对HDFS来说都不起作用了，这也引起了部分block的拷贝数量少于指定值。NameNode会不断跟踪需要备份的block并且启动复制。必须进行重新复制的理由是：一个DataNode可能因为故障而不可用,
 拷贝过程可能失败, DataNode上的磁盘可能坏掉, 或者副本数量会被增加。</span></p>
<br></div>

<h2 style="font-family:Arial;font-size:14px;color:rgb(51,51,51);">
<a name="t22" style="color:rgb(51,102,153);"></a><span style="font-family:'Microsoft YaHei';">集群的重新均衡</span></h2>
<p style="line-height:1.3em;">
<span style="font-family:SimSun;">HDFS架构会兼容数据重新均衡的策略。其设想是当节点 A的剩余空间明显低于某一阈值时，HDFS会自动选择从其他节点移动数据到节点A；当对某个特定文件有紧急高优先级的需求时, HDFS将会动态增加文件拷贝及重新均衡数据。但目前这些设想还没有实现。</span></p>
<p style="line-height:1.3em;">
<span style="font-family:'Microsoft YaHei';font-size:14px;line-height:26px;">数据完整性</span></p>
</div>
<div style="line-height:26px;">
<p style="line-height:1.3em;">
<span style="font-family:SimSun;">存储在某个DataNode上的数据很可能会被损坏，可能是由于存储设备故障，网络故障或者软件漏洞所致。HDFS客户端应用实现了对文件内容的校验和。客户端创建HDFS文件时，它会计算每个文件的每个block的校验和并在相同的命名空间下的单独隐藏的文件中保存这些值。当客户端接收文件数据时，会首先验证校验和。如果校验失败，客户端会向其他拥有同样复本的DataNode重新获取数据。</span></p>
<p style="font-family:Arial;font-size:14px;line-height:1.3em;">
<span style="line-height:26px;font-family:'Microsoft YaHei';">元数据磁盘故障</span></p>
</div>
<div style="line-height:26px;">
<div><span style="font-family:SimSun;">FsImage及 EditLog文件是HDFS系统的重要数据结构，这些文件的异常会导致HDFS无法正常工作。因此NameNode可以配置拥有FsImage和EditLog的多份拷贝。对FsImage或EditLog文件的更改会引起所有复本的同步更新，这同步更新的过程会降低NameNode可以支持的命名空间每秒事务处理的频率。但这是可以接受的，因为即使HDFS应用对数据很敏感，也不会对元数据敏感。当NameNode重启时，它会选择使用最新版本的FsImage和EditLog文件。</span></div>
<div><span style="line-height:1.3em;"><span style="font-family:SimSun;">NameNode在HDFS集群中属于单点故障。如果NameNode宕掉了，必须要经过人工来处理和修复，目前还不支持NameNode的自动重启和运行NameNode服务于备份服务器。</span></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:14px;">快照</span></div>
</div>
<div style="font-family:Arial;font-size:14px;line-height:26px;">
<p style="line-height:1.3em;">
<span style="font-family:'Microsoft YaHei';">快照(Snapshot)支持在某个时间点拷贝并保存一份数据。一种应用场景是快照用于将HDFS实例回滚到故障之前某个合适的状态(HDFS目前不支持Snapshot,但会在以后的版本中实现)。</span></p>
<p style="line-height:1.3em;">
<span style="line-height:26px;font-family:'Microsoft YaHei';"><span style="font-size:18px;">数据组织</span></span></p>
<p style="line-height:1.3em;">
<span style="line-height:26px;font-family:'Microsoft YaHei';">数据块</span></p>
</div>
<div style="font-family:Arial;font-size:14px;line-height:26px;">
<p style="line-height:1.3em;">
<span style="font-family:'Microsoft YaHei';">HDFS用于存储大文件，兼容HDFS的应用都是那些需要处理大数据集的应用。这些应用一般会只一次写入数据，但会读取多次并要求满足流式读取。HDFS支持文件的write-once-read-many原则。HDFS中典型的块大小为64 MB，因此一个文件往往会被切成64MB的大量的块(通常这些快会保存在不同的节点上)。</span></p>
<p style="line-height:1.3em;">
<span style="line-height:26px;font-family:'Microsoft YaHei';">Staging</span></p>
</div>
<div style="font-family:Arial;font-size:14px;line-height:26px;">
<p style="line-height:15px;">
<span style="font-family:'Microsoft YaHei';">客户端创建文件的请求其实并没有立即发送给Namenode，事实上初始时HDFS客户端会先将文件数据缓存到本地的一个临时文件。应用程序的写操作被重定向到这个临时文件，这个操作对外是透明的。当这个临时文件的数据量超过block大小时，客户端才会通知Namenode。Namenode将文件名插入文件系统的层次结构中，并且分配一个数据块给它，然后向客户端返回Datanode的标识符和目标数据块。接着客户端将这块数据从本地临时文件flush到指定的Datanode。当文件关闭时，在临时文件中还没有上传的数据也会传输到指定的Datanode上；然后客户端通知Namenode该文件已关闭。此时Namenode才将文件创建操作持久化到日志文件里。如果Namenode在文件关闭前宕机，则该文件将丢失。</span></p>
<p style="line-height:1.3em;">
<span style="font-family:'Microsoft YaHei';">以上方法是对在HDFS上运行的目标应用进行认真考虑后才采用的。这些应用规定要以流式写入文件，如果不采用客户端缓存，网络速度和网络堵塞会对吞吐率造成较大的影响。这种方法并不是首创的，早期的文件系统如<acronym title="Andrew File System">AFS</acronym>，就是用客户端缓存来提高性能。为了有更高的数据上传效率，已经放松了POSIX标准。</span></p>
<p style="line-height:1.3em;">
<span style="line-height:26px;font-family:'Microsoft YaHei';">流水线复制</span></p>
</div>
<div style="font-family:Arial;font-size:14px;line-height:26px;">
<div><span style="font-family:'Microsoft YaHei';line-height:15px;">当客户端向HDFS文件写入数据时，一开始是写到本地临时文件中。假设该文件的副本系数为3，当本地临时文件累积到一个数据块的大小时，客户端会从Namenode获取到一份Datanode列表用于存储副本，接着客户端开始向列表中第一个Datanode传输数据。此Datanode会分多个小块(4 KB)来接收数据，将每一部分写入本地目录的同时传输该部分到列表中第二个Datanode。第二个Datanode也像前一个节点一样接收数据和存储，并传给第三个节点。最后第三个Datanode接收数据并存储在本地。因此Datanode可以流水线地从前一个节点接收数据，然后转发给下一个节点。这样数据便以流水线的方式从前一个Datanode复制到下一个。</span></div>
<div><span style="font-family:'Microsoft YaHei';">可访问性</span></div>
</div>
<div style="font-family:Arial;font-size:14px;line-height:26px;">
<p style="line-height:15px;">
<span style="font-family:'Microsoft YaHei';">HDFS向应用提供了多种访问方式。用户可以通过原生的 <a href="http://hadoop.apache.org/docs/current/api/" rel="nofollow" style="color:rgb(102,136,170);text-decoration:none;">FileSystem Java
 API</a>接口来访问，也可以通过C语言的封装API访问，甚至还可以通过浏览器的方式访问HDFS中的实例文件。目前正在开发通过<acronym title="Web-based Distributed Authoring and Versioning">WebDAV</acronym>协议来访问HDFS。</span></p>
<p style="line-height:15px;">
<span style="line-height:26px;font-family:'Microsoft YaHei';">FS Shell</span></p>
</div>
<div style="font-family:Arial;font-size:14px;line-height:26px;">
<div><span style="font-family:'Microsoft YaHei';line-height:15px;">HDFS支持以文件和目录的形式组织用户数据。它提供了一个命令行接口(FS Shell)让用户与HDFS中的数据进行交互。命令的语法类似于用户熟悉的其他shell(例如 bash, csh)工具。以下是一些操作/命令的示例：</span></div>
<div><span style="font-family:'Microsoft YaHei';"><br></span></div>
<table border="1" style="margin-left:-2px;"><tbody><tr style="background-color:rgb(221,221,221);"><th align="left" colspan="1" rowspan="1" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);">
<span style="font-family:'Microsoft YaHei';font-size:14px;">操作</span></th>
<td align="left" colspan="1" rowspan="1" style="color:rgb(51,51,51);vertical-align:top;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">命令</span></td>
</tr><tr style="background-color:rgb(238,238,238);"><td align="left" colspan="1" rowspan="1" style="color:rgb(51,51,51);vertical-align:top;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">创建目录<tt>/foodir</tt></span></td>
<td align="left" colspan="1" rowspan="1" style="color:rgb(51,51,51);vertical-align:top;">
<tt><span style="font-family:'Microsoft YaHei';font-size:14px;">bin/hadoop dfs -mkdir /foodir</span></tt></td>
</tr><tr style="background-color:rgb(221,221,221);"><td align="left" colspan="1" rowspan="1" style="color:rgb(51,51,51);vertical-align:top;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">删除目录<tt>/foodir</tt></span></td>
<td align="left" colspan="1" rowspan="1" style="color:rgb(51,51,51);vertical-align:top;">
<tt><span style="font-family:'Microsoft YaHei';font-size:14px;">bin/hadoop dfs -rmr /foodir</span></tt></td>
</tr><tr style="background-color:rgb(238,238,238);"><td align="left" colspan="1" rowspan="1" style="color:rgb(51,51,51);vertical-align:top;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">查看文件<tt>/foodir/myfile.txt的内容</tt></span></td>
<td align="left" colspan="1" rowspan="1" style="color:rgb(51,51,51);vertical-align:top;">
<tt><span style="font-family:'Microsoft YaHei';font-size:14px;">bin/hadoop dfs -cat /foodir/myfile.txt</span></tt></td>
</tr></tbody></table><p style="line-height:1.3em;">
<span style="font-family:'Microsoft YaHei';">FS shell的目的是为那些需要通过脚本语言来与数据交互的应用。</span></p>
<p style="line-height:1.3em;">
<span style="line-height:26px;font-family:'Microsoft YaHei';">DFSAdmin</span></p>
</div>
<div style="font-family:Arial;font-size:14px;line-height:26px;">
<p style="line-height:1.3em;">
<span style="font-family:'Microsoft YaHei';">DFSAdmin命令用于管理HDFS集群，只能被HDFS管理员使用。以下是一些操作/命令的示例：</span></p>
<table border="1" style="margin-left:-2px;"><tbody><tr style="background-color:rgb(221,221,221);"><th align="left" colspan="1" rowspan="1" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);">
<span style="font-family:'Microsoft YaHei';font-size:14px;">操作</span></th>
<td align="left" colspan="1" rowspan="1" style="color:rgb(51,51,51);vertical-align:top;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">命令</span></td>
</tr><tr style="background-color:rgb(238,238,238);"><td align="left" colspan="1" rowspan="1" style="color:rgb(51,51,51);vertical-align:top;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">将集群设置为安全模式</span></td>
<td align="left" colspan="1" rowspan="1" style="color:rgb(51,51,51);vertical-align:top;">
<tt><span style="font-family:'Microsoft YaHei';font-size:14px;">bin/hadoop dfsadmin -safemode enter</span></tt></td>
</tr><tr style="background-color:rgb(221,221,221);"><td align="left" colspan="1" rowspan="1" style="color:rgb(51,51,51);vertical-align:top;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">显示DataNode列表</span></td>
<td align="left" colspan="1" rowspan="1" style="color:rgb(51,51,51);vertical-align:top;">
<tt><span style="font-family:'Microsoft YaHei';font-size:14px;">bin/hadoop dfsadmin -report</span></tt></td>
</tr><tr style="background-color:rgb(238,238,238);"><td align="left" colspan="1" rowspan="1" style="color:rgb(51,51,51);vertical-align:top;">
<span style="font-family:'Microsoft YaHei';font-size:14px;">上线或下线DataNode(s)</span></td>
<td align="left" colspan="1" rowspan="1" style="color:rgb(51,51,51);vertical-align:top;">
<tt><span style="font-family:'Microsoft YaHei';font-size:14px;">bin/hadoop dfsadmin -refreshNodes</span></tt></td>
</tr></tbody></table></div>
<div style="font-family:Arial;font-size:14px;line-height:26px;">
<h2 style="color:rgb(51,51,51);"><a name="t23" style="color:rgb(51,102,153);"></a><a style="color:rgb(202,0,0);"></a><span style="font-family:'Microsoft YaHei';font-size:14px;">浏览器接口</span></h2>
<p style="line-height:1.3em;">
<span style="font-family:'Microsoft YaHei';">一个典型的HDFS安装会在一个可配置的TCP端口开启一个用于展示命名空间的web服务。这使得用户可以浏览HDFS明明空间及查看文件内容。</span></p>
<p style="line-height:1.3em;">
<span style="line-height:26px;font-family:'Microsoft YaHei';"><span style="font-size:18px;"><br></span></span></p>
<p style="line-height:1.3em;">
<span style="line-height:26px;font-family:'Microsoft YaHei';"><span style="font-size:18px;">回收存储空间</span></span></p>
<p style="line-height:1.3em;">
<span style="line-height:26px;font-family:'Microsoft YaHei';">文件的删除及恢复</span></p>
</div>
<div style="font-family:Arial;font-size:14px;line-height:26px;">
<div><span style="font-family:'Microsoft YaHei';">当一个文件被用户或应用删除时，它并不会立即从HDFS中移除，相反它会先被重命名到目录/trash下的一个文件。因此该文件只要还在/trash下，就可以快速的还原。目录</span><tt>/trash下的文件的保存时长是可以配置的，超过了这个时限后NameNode将会把它从HDFS命名空间中删除。</tt><span style="font-family:'Microsoft YaHei';">文件的</span></div>
<div><span style="font-family:'Microsoft YaHei';">物理删除会引起对应block空间的释放。可以注意到，从用户删除一个文件到HDFS空闲空间的增加会有一定的延迟。</span></div>
<div><span style="font-family:'Microsoft YaHei';"><br></span></div>
<div><span style="font-family:'Microsoft YaHei';"><span style="line-height:15px;">只要被删除的文件还在</span><span style="line-height:15px;">/trash</span><span style="line-height:15px;">目录中，用户就可以还原它。如果用户想还原被删除的文件，可以通过浏览</span><span style="line-height:15px;">/trash</span><span style="line-height:15px;">目录找回该文件，而</span><span style="line-height:15px;">/trash</span><span style="line-height:15px;">目录仅保存被删除文件的最后版本。</span><span style="line-height:15px;">/trash</span><span style="line-height:15px;">目录与其他的目录几乎一样，除了HDFS会应用一个特殊策略来自动删除该目录下的文件。目前默认策略是删除</span><span style="line-height:15px;">/trash</span><span style="line-height:15px;">中保留时间超过6小时的文件。以后这个策略可以通过一个被定义的接口来配置。</span></span></div>
<div><span style="font-family:'Microsoft YaHei';">减少副本系数</span></div>
</div>
<div style="font-family:Arial;font-size:14px;line-height:26px;">
<div style="color:rgb(51,51,51);"><span style="font-family:'Microsoft YaHei';">当副本系数被降低时，NameNode会选择删除多余的副本，决定好后的下次Heartbeat检查会传递这个信息给DataNode. DataNode接收命令后即删除相应的block，这样HDFS集群对应的空闲空间便会增大。重新说明一下，从调用setReplication API到集群空间的回收会有一段时间的延迟。</span></div>
<div><span style="font-family:'Microsoft YaHei';"><br></span></div>
</div>
</div>
            </div>
                </div>