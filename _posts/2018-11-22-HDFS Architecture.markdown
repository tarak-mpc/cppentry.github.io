---
layout:     post
title:      HDFS Architecture
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1><a name="_Toc396057108"></a></h1>
<h1><a name="_Toc396057539">HDFS Architecture</a></h1>
<h2><a name="_Toc396057540">Introduction</a></h2>
<p>HDFS是分布式文件系统，它被设计为运行在普通商用硬件之上。它与已经存在的文件系统有很多相似性。但是，也有巨大的不同。HDFS有很高的容错，被设计为部署在低廉的硬件上。HDFS提供为应用程序的数据提供一个高通量的访问，适合有大量数据的应用程序。HDFS放松了一些POSIX的需求，以使可以用流的方式访问文件系统的数据。HDFS最初作为Apache Nutch web搜索引擎项目的基础设施。HDFS是Apache Hadoop核心项目。</p>
<h2><a name="_Toc396057541">Assumptions</a>和Goals</h2>
<h3><a name="_Toc396057542">Hardware Failure</a></h3>
<p>硬件故障时常态而非例外。一个HDFS实例可能包含成百上千台服务器，每一台机器存储文件系统的部分数据。事实上,有大量的组件,每个组件都有一个不平凡的失效概率,这意味着总是有一些组件是不能工作的。因此，故障的检测和快速自动故障恢复是HDFS的主要架构目标。</p>
<h3><a name="_Toc396057543">Streaming Data Access</a></h3>
<p>运行在HDFS的应用需要以流的方式访问它们的数据集。这些应用不是运行于一般用途的文件系统上的一般用户的应用。HDFS的设计更适合批处理而不是交互式处理。HDFS的重点是高通量的数据访问而不是低延迟的数据访问。POSIX强加了很多对HDFS适合的应用不需要的硬性需求。POXIS在部分关键地方的语义已被替换以提高数据吞吐率。</p>
<h3><a name="_Toc396057544">Large Data Sets</a></h3>
<p>运行在HDFS上的应用都有大量的数据集。HDFS中文件通常是GB大小。因此，HDFS被设置为支持大文件。它应该为成百上千的机器提供高聚合数据带宽和规模。一个HDFS实例应该支持数百万的文件。</p>
<h3><a name="_Toc396057545">Simple Coherency Model</a></h3>
<p>简单一致性模型</p>
<p>HDFS应用需要对文件的访问模式为write-once-read-many 。一个文件一旦创建，写入，关闭后，不需要在改变。这个建设简化了数据一致性的问题，使其具有一个高通量的数据访问。一个MR应用或者一个web爬虫应用首先完美的适用此模型。在将来，有支持appending-writes 到文件的计划。</p>
<h3><a name="_Toc396057546">“Moving Computation is Cheaperthan Moving Data”</a></h3>
<p>移动计算代价小于移动数据</p>
<p>如果应用在紧挨着数据的地方运行应用所需要的计算将更有效率。当数据集非常大是尤是如此。这减少了网络拥塞，增加了系统整体的吞吐量。这个假设是移动计算到数据所在的位置而不是移动数据到应用运行所在的位置。HDFS为应用程序提供了一个接口，以移动应用程序自己到数据所在的位置。</p>
<h3><a name="_Toc396057547">Portability Across </a>
<a name="OLE_LINK2"></a><a name="OLE_LINK1">Heterogeneous</a>Hardware and SoftwarePlatforms</h3>
<p>跨异构的硬件和软件平台</p>
<p>HDFS被设置为可以很容易的从一个平台移动到另一个平台。这使HDFS作为一个平台对于大量的应用程序具有普遍的适用性。</p>
<h2><a name="_Toc396057548">NameNode and DataNodes</a></h2>
<p>HDFS是一个master/slave的架构。一个HDFS集群包含一个NameNode（masterServer）管理文件系统命名空间和控制客户端访问文件。此外，一个集群还包括很多的DataNode，DataNode管理它们所在的节点的存储。HDFS暴露一个文件系统命名空间，允许用户将数据存在其中的文件。在内部，一个文件被分割成一个或者多个Block，这些Block存储在一系列的DataNode上。NameNode执行对文件系统命名空间的操作，比如打开，关闭，重命名文件或者目录。它也决定Block到DataNode的映射。DataNode负责处理文件系统客户端的读写请求。DataNode也执行来自NameNode的Block的创建，删除和复制指令。</p>
<p><img src="" alt=""><img src="https://img-blog.csdn.net/20140817165729745?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGljaGVuZ3Vhbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" width="474" height="304" alt=""></p>
<p>NameNode和DataNode是被设计为运行在普通商用机器上的软件。这些机器通常运行GNU/Linux操作系统。HDFS用Java构建；任何支持Java的机器都可以运行NameNode和DataNode软件。利用高可移植性的Java语言意味着HDFS可以大规模的部署。通常部署方案是用一个专用的机器运行NameNode软件。集群中的其他机器运行DataNode软件的实例。这个架构不会组织在一个机器上运行多个DataNode，但是在真正的部署过程中，几乎没有这样的例子。</p>
<p>一个集群中只有一个NameNode的存在极大的简化了系统的架构。NameNode是仲裁者，同时存储HDFS的元数据。系统被设计为用户数据永远不会流过NameNode。</p>
<h2><a name="_Toc396057549">The File System Namespace</a></h2>
<p>HDFS 支持传统的分层的文件组织结构。一个用户或者一个应用可以创建一个目录然后在其中存储文件。文件系统命名空间分层与很多其他的文件系统相似。你可以创建和删除文件，移动文件或者重命名文件。HDFS不在实现用户配额或者访问权限。HDFS不支持硬链接和软连接。但是，HDFS的机构不妨碍实现这些特性。</p>
<p>NameNode维护这个文件系统命名空间。任何的文件系统命名空间的改变，它的属性被NameNode记录。一个应用可以指定HDFS维护的一个文件的多个副本。一个文件的副本的个数称为文件的副本因子。这个信息会被NameNode存储。</p>
<h2><a name="_Toc396057550">Data Replication</a></h2>
<p>HDFS被设计为在一个大规模的集群中的各个机器之间可靠的存储大文件。它将每一个文件存储为一个个块的序列；一个文件的所有的块，除了最后一个，大小相同。一个文件的所有Block为容错而复制多份。Block的大小和副本因子对于每一个文件都是可配置的。一个应用可以指定一个文件的副本的个数。副本因子可以在文件创建时被指定，也可之后修改。HDFS中的文件是一次性写入，在任意时刻，这个文件只有一个写入进程。</p>
<p>NameNode做出所有关于块的副本的决定。它周期性的收到集群中的每一个DataNode的心跳信息和Block报告。收到心跳信息意味着这个DataNode在正确的工作。一个Block报告包括一个DataNode上所有块的列表。</p>
<p><img src="https://img-blog.csdn.net/20140817165816920?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGljaGVuZ3Vhbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" width="474" height="336" alt=""><br></p>
<p><img src="" alt=""></p>
<h3><a name="_Toc396057551">Replica Placement</a>：The First Baby Steps</h3>
<p>副本的存放位置关系到HDFS的可靠性和性能。优化副本的存放位置是HDFS与其他分布式文件系统不同的地方。这个特性需要很多调优经验。机架感知的副本存放策略的目的是提高数据的可靠性，可用性和带宽利用率。目前的副本存放策略的实现首先在这些方面努力。是现在这个策略的短期的目标是在生产系统上验证它，获取更多其表现信息，然后构建一个基础来测试和研究更多复杂的策略。</p>
<p>大规模的HDFS实例运行在一个包含很多计算机的集群，集群通常需要部署到多个机架上。不同机架上的两个节点的通信需要通过交换机。在大多数情况下，同一个机架上的机器之间的带宽比不同机架上的机器的带宽要快。</p>
<p>NameNode通过<a><span style="color:#990000;background:#FFFFFF;">Hadoop
 MapReduce Next Generation - Cluster Setup</span></a>#<a><span style="color:#6688AA;background:#FFFFFF;">HadoopRack
 Awareness</span></a>所述的步骤判断每一个DataNode所属的机架。一个简单但不是最优的策略是将副本都放到不同的机架上。这将在一个机架发生故障时防止数据丢失，在读数据时，允许使用多个机架的带宽。这个策略将副本公平地分布到集群中，这可以在有组件故障时很容易平衡负载。但是，这个策略增加了写代价，因为一个写过程需要将Block传输到多个机架。</p>
<p>在大多数情况下，副本因子是3，HDFS的副本放置策略是将一个副本放置到本地机架上的一个节点，另一个在本地机架上的另一个节点，最后一个放到另一个机架上的一个节点。这个策略减少了机架间的写负载，这通常可以提高写性能。机架故障的几率比单个节点故障的几率小的多，所以这个策略不会影响数据的可靠性和可用性保证。而且，当读数据时它将减少所用的网络带宽，因为一个Block仅仅被放置到两个机架上而不是3个。使用这个策略，一个文件的副本不会平均分布到机架上。1/3的副本在一个节点上，2/3的副本在一个机架上，两外1/3平均分布到剩下的机架上。这个策略在没有妥协数据可靠性和读性能的情况下提高了写性能。</p>
<p>目前，这里描述的默认的副本放置策略正在进行中。</p>
<h3><a name="_Toc396057552">Replica Selection</a></h3>
<p>为了减少全局带宽消耗和读延迟，HDFS尝试从一个最近的副本响应一个读请求。如果与读者所在的节点所在的机架上的某个节点存在着一个副本，这个副本将会优先用来响应这个读请求。如果一个HDFS集群跨越多个数据中心，一个位于本地数据中心的副本会优先于其他远程的副本。</p>
<h3><a name="_Toc396057553">Safemode</a></h3>
<p>在HDFS启动时，NameNode进入一个叫做Safemode的特殊的状态。数据Block的复制在NameNode处在Safemode时不会发生。NameNode接收DataNode的Heartbeat和Blockreport消息。一个Blockreport包括这个DataNode上所有的数据块的列表。每一个Block会被指定一个副本个数的最小值。当一个数据块的副本个数的最小数量与NameNode核对之后，一个Block被认为是安全地复制过了。一个可配置的与NameNode核对的安全地复制的数据块百分比（加上额外的30s），NamNode结束Safemode状态。然后它判断拥有副本个数少于指定的副本的个数的数据块的列表。然后，NameNode复制这些数据块到其他的DataNode。</p>
<h2><a name="_Toc396057554">The Persistence of File SystemMetadata</a></h2>
<p>HDFS的命名空间存储在NameNode节点。NameNode用一个叫做EditLog的事务日志来持久化的记录发生在文件系统元数据的每一个变化。例如，在HDFS中创建一个新文件会使NameNode插入一条记录到EditLog以声明这个操作。类似的，改变一个文件的副本因子会使一个新纪录被插入到EditLog文件中。NameNode用一个本地操作系统中的文件来存储EditLog。整个文件系统命名空间，包括Block到文件的映射和文件系统属性，都被存储到一个叫做FsImage的文件中。FsImage文件也是存储到NameNode本地文件系统中。</p>
<p>NameNode保存全局文件系统命名空间的一个镜像和内存中文件的Blockmap。这些元数据被设计成紧凑的，比如，一个4GB RAM的NameNode是足够支持大规模的文件和目录。当NameNode启动时，它从磁盘读取FsImage和EditLog，应用所有来自EditLog的事务到内存中的FsImage代表，然后flush这个新的版本到磁盘中的一个新的FsImage文件。这可以截短旧的Editlog，因为它的事务已经被应用的持久化的FsImage了。这个过程叫做checkpoint。在当前的实现中，一次Checkpoint渐渐发生在NameNode启动时。将来支持周期性的Checkpoint的工作正在进行中。</p>
<p>DataNode存储HDFS数据到本地文件系统的文件中。DataNode不知道HDFS文件信息。它存储HDFS的每一个Block到本地文件系统的一个单独的文件中。DataNode不会在同一个目录中创建所有的文件。而是，它用一个启发式的方式来判定每个目录中最优的文件数量，在适当的时候创建子目录。创建所有的本地文件到一个目录下不是最优的，因为，本地文件系统可能不能高效的支持在一个单独的目录中存放大量的文件。当一个DataNode启动时，它扫描它的本地文件系统，生成所有HDFS的对应于每一个本地文件的数据Block的列表，然后发送这个列表到NameNode：这就是Blockreport。</p>
<h2><a name="_Toc396057555">The Communication Protocols</a></h2>
<p>HDFS通信协议在TCP/IP协议的上层。一个客户端可以建立一个连接，连接到一个NameNode所在的机器上配置好的端口。这就是与NameNode通信的ClientProtocol。DataNode用DataNode Protocol与NameNode通信。RPC抽象封装ClientProtocol和DataNodeProtocol。按照设计，NameNode永远不发起任何RPC。NameNode只响应来自DataNode和Client的RPC请求。</p>
<h2><a name="_Toc396057556">Robustness</a></h2>
<p>HDFS主要的目标是在故障发生时也可以可靠的存储数据。下面3个常见的故障类型是NameNode故障，DataNode故障和网络划分。</p>
<h3><a name="_Toc396057557">Data Disk Failure</a>，Heartbeats and Re-Replication</h3>
<p>每一个DataNode周期性地发送心跳消息给NameNode。一个网络划分可能导致一部分DataNode与NameNode失去联系。NameNode通过心跳消息的缺失来检测这个情况。NameNode标记最近没有发送心跳消息的DataNode为dead，然后不转发新的IO请求到这些DataNode。任何注册到一个dead的DataNode的数据对于HDFS不在可用。DataNode死亡可能导致一些Block的复制因子减少到指定的值。NameNode不断最总需要复制的Block，然后在必要的时候发起复制。重新复制的必要性可因多个原因上升：一个DataNode可能变的不可用，一个副本可能被毁坏，DataNode上的硬盘可能发生故障或者一个文件的副本因子增加。</p>
<h3><a name="_Toc396057558">Cluster Rebalancing</a></h3>
<p>HDFS的架构与数据重新平衡的体制是兼容的。一个策略可能在一个DataNode上的空闲空间减少到特定阀值的时候，自动从一个DataNode移动数据到另一个。在突然出现对一个文件的高需求时，一个策略可能自动的创建额外的副本，重新平衡集群中的其他数据。这些类型的数据平衡策略还没有实现。</p>
<h3><a name="_Toc396057559">Data Intergrity</a></h3>
<p>数据完整性</p>
<p>从DataNode获取的数据块到达的时候损坏是可能的。损坏可能因为存储设备的故障，网络故障或者是软件的bug。HDFS客户端软件会用校验和检查HDFS文件内容。当一个客户端创建一个HDFS文件时，它计算这个文件每一块的校验和，然后存储这些校验和到在一个单独的隐藏文件。这个校验和文件与HDFS命名空间在一个位置。当一个客户端检索文件内容时，它验证从每一个DataNode接收的数据是否匹配存储在相关校验和文件中的校验和。如果不匹配，客户端可以选择从其他拥有这个数据块副本的DataNode上重新检索数据。</p>
<h3><a name="_Toc396057560">MetaData Disk Failure</a></h3>
<p>FsImage 和Editlog是HDFS的核心数据结构。这些文件损坏可以导致HDFS实例不能工作。因为这个原因，NameNode可被配置支持维护Fsimage和EditLog文件的多个副本。任何到FsImage或者Editlog的更新使FsImage和Editlog的每个副本同步的更新。多个FsImage和Editlog的副本的同步更新可能减少命名空间事务的效率。但是，这个减少是可接受的，因为尽管HDFS应用是数据密集型的，但它们不是元数据密集型的。当一个NameNode重新启动，它选择最近一致的FsImage和Editlog文件使用。</p>
<p>NameNode对于一个HDFS集群时单点故障的。如果NameNode机器故障，手工干预是必要的。目前，NameNode的自动重启和故障转移到另一台机器还不支持。</p>
<h3><a name="_Toc396057561">Snapshot</a></h3>
<p>快照支持存储数据在任意时间点的复制。快照特定的一个应用可能是回滚一个损坏的HDFS实例到之前的一个好的时间点。HDFS目前不支持快照，但是将来会支持。</p>
<h2><a name="_Toc396057562">Data Organization</a></h2>
<h3><a name="_Toc396057563">Data Blocks</a></h3>
<p>HDFS被设计支持非常大的文件。与HDFS兼容的应用时那些处理大规模数据集的应用。这些应用一次性写入数据，但是读一次或多次。应用需要满足一定的读速度。HDFS支持文件write-once-read-many语义。HDFS通常使用的Block大小是64MB。因此，一个HDFS文件被截断成64MB的块，如果可能，每一个块将位于不同的DataNode。</p>
<h3><a name="_Toc396057564">Staging</a></h3>
<p>一个客户端创建一个文件的请求不能立即到达NameNode。事实上，开始时，HDFS客户端缓存文件的数据到一个临时本地文件。应用程序写被透明的重定向到这个临时的本地文件。当本地文件积累的数据操作一个HDFS Block的大小，客户端联系NameNode。NameNode插入这个文件的名字到文件系统层次中，然后为它申请一个数据块。NameNode用一个DataNode和目标数据块的标识响应客户端的请求。然后客户端从本地临时文件flush这个数据块到指定的DataNode。当一个文件关闭，剩下的本地临时文件中未flush的数据块被传输到DataNode中。客户端然后告诉NameNode，这个文件关闭了。这个时候，NameNode提交这个文件的创建操作到一个持久化的存储中。如果NameNode在文件关闭之前死掉，这个文件就丢失了。</p>
<p>上边的方法是经过了认真的考虑运行在HDFS上的目标应用的特点之后采用的。这些应用需要流式的写入文件。如果一个客户端不用客户端缓存直接写入一个远程文件，网络速度和网络拥塞将会极大的影响吞吐量。这个方法不是没有先例的。早一些的分布式文件系统，例如，AFS，也是用客户端缓存来提高性能。为了实现更高的数据上传性能，一个POSIX需求已经被放松了。</p>
<h3><a name="_Toc396057565">Replication Pipelining</a></h3>
<p>复制管道</p>
<p>当一个客户端正在往一个HDFS文件写数据时，它的数据首先被写入到一个本地文件中，上边解释过了。假设HDFS文件的副本因子是3.当这个本地文件积累了一整个Block的用户数据之后，客户端检索来自NameNode的DataNode的列表。这个列表包含将会持有这个Block的一个副本的DataNode。然后，客户端flush数据块到第一个DataNode。第一个DataNode一小部分一小部分的接收数据，将每一小部分写入它的本地仓库，然后传输这一小部分到列表中的第二个DataNode。第二的DataNode，开始接收这个数据块的每一个portion，将这一小部分写入本地仓库，然后flush这一小部分到第三个DataNode。最后，第三个DataNode将接收到的数据写入本地仓库。因此，一个DataNode可以从管道的前一个DataNode接收数据，并同时转发数据到管道的下一个DataNode。因此，数据从一个DataNode到另一个DataNode构成一个管道。</p>
<h2><a name="_Toc396057566">Accessibility</a></h2>
<p>访问</p>
<p>HDFS可以被应用程序以多种不同的方式访问。最基本的，HDFS为应用程序提供一个FileSystem JAVA API。一个对JAVA API的C语言的封装也可以使用。此外，HTTP浏览器也可以用来浏览一个HDFS实例的文件。通过WebDAV协议访问HDFS的工作正在进行中。</p>
<h3><a name="_Toc396057567">FS Shell</a></h3>
<p>HDFS允许用户数据已文件和目录的形式被组织。它提供了一个叫做FS Shell的命令行接口，这让一个用户可以与HDFS中数据交互。这个命令集的语法与其他的用户已经熟悉的shell命令类似。这是一些命令的使用例子：</p>
<table border="1" cellpadding="0" style="background:#FFFFFF;"><tbody><tr><td valign="top" style="background:#BBBBBB;">
<p align="left"><strong><span style="color:#FFFFFF;">Action</span></strong></p>
</td>
<td valign="top" style="background:#DFE3DB;">
<p align="left"><span style="color:#333333;">Command</span></p>
</td>
</tr><tr><td valign="top" style="background:#EEEEEE;">
<p align="left"><span style="color:#333333;">Create a directory named </span><span style="color:#333333;">/foodir</span></p>
</td>
<td valign="top" style="background:#EEEEEE;">
<p align="left"><span style="color:#333333;">bin/hadoop dfs -mkdir /foodir</span></p>
</td>
</tr><tr><td valign="top" style="background:#DDDDDD;">
<p align="left"><span style="color:#333333;">Remove a directory named </span><span style="color:#333333;">/foodir</span></p>
</td>
<td valign="top" style="background:#DDDDDD;">
<p align="left"><span style="color:#333333;">bin/hadoop dfs -rmr /foodir</span></p>
</td>
</tr><tr><td valign="top" style="background:#EEEEEE;">
<p align="left"><span style="color:#333333;">View the contents of a file named </span><span style="color:#333333;">/foodir/myfile.txt</span></p>
</td>
<td valign="top" style="background:#EEEEEE;">
<p align="left"><span style="color:#333333;">bin/hadoop dfs -cat /foodir/myfile.txt</span></p>
</td>
</tr></tbody></table><p>FS Shell目标是需要脚本语言来与存储的数据交互的应用。</p>
<h3><a name="_Toc396057568">DFSAdmin</a></h3>
<p>DFSAdmin命令集被用来管理一个HDFS集群。这些命令仅被管理员使用。这是一些命令的使用例子：</p>
<table border="1" cellpadding="0" style="background:#FFFFFF;"><tbody><tr><td valign="top" style="background:#BBBBBB;">
<p align="left"><strong><span style="color:#FFFFFF;">Action</span></strong></p>
</td>
<td valign="top" style="background:#DDDDDD;">
<p align="left"><span style="color:#333333;">Command</span></p>
</td>
</tr><tr><td valign="top" style="background:#DFE3DB;">
<p align="left"><span style="color:#333333;">Put the cluster in Safemode</span></p>
</td>
<td valign="top" style="background:#DFE3DB;">
<p align="left"><span style="color:#333333;">bin/hadoop dfsadmin -safemode enter</span></p>
</td>
</tr><tr><td valign="top" style="background:#DDDDDD;">
<p align="left"><span style="color:#333333;">Generate a list of DataNodes</span></p>
</td>
<td valign="top" style="background:#DDDDDD;">
<p align="left"><span style="color:#333333;">bin/hadoop dfsadmin -report</span></p>
</td>
</tr><tr><td valign="top" style="background:#EEEEEE;">
<p align="left"><span style="color:#333333;">Recommission or decommission DataNode(s)</span></p>
</td>
<td valign="top" style="background:#EEEEEE;">
<p align="left"><span style="color:#333333;">bin/hadoop dfsadmin -refreshNodes</span></p>
</td>
</tr></tbody></table><h3><a name="_Toc396057569">Browser Interface</a></h3>
<p>一个典型的HDFS安装会配置一个web Server来通过一个TCP端口暴露HDFS的命名空间。这允许一个用户用web 浏览器浏览HDFS的命名空间和查看它的文件的内容。</p>
<h2><a name="_Toc396057570">Space Reclamation</a></h2>
<p>空间回收</p>
<h3><a name="_Toc396057571">File deletes and Undeletes</a></h3>
<p>当一个文件被一个或者应用删除的时候，它不会立即从HDFS中移除。相反的是，HDFS首先重命名它为一个在/trash目录中的文件。只要这个文件在/trash中，它就可被快速的恢复。一个文件保存在/trash一个可配置的时间。在超过这个时间之后，NameNode从HDFS命名空间中删除这个文件。这次删除会导致Block相关的文件被释放。注意，在一个文件被删除和响应的磁盘空间被释放之间可能会有一个明显的时间延迟。</p>
<p>一个用户可以删除一个文件之后Undelete一个文件，只要它还保存在/trash目录。如果一个用户想undelete一个已经删除的文件，他可以浏览/trash目录，然后检索这个文件。/trash目录仅包含被删除文件最近时间点的副本。/trash目录跟其他目录一样，只有一个特别的特性：HDFS应用特殊的策略来自动删除这个目录中的文件。目前默认的删除间隔是0（直接删除，不存在/trash中）。这个值可被core-site.xml文件中fs.trash.interval属性配置。</p>
<h3><a name="_Toc396057572">Decrease Replication Factor</a></h3>
<p>当一个文件的副本因子被减少时，NameNode选择可被删除的多余的副本。下一次心跳传输这个信息给DataNode。然后，DataNode移除相应的Block，相应的空闲空间会出现在集群中。再说一次，setReplication API调用和集群中空闲空间出现之间可能会有延迟。</p>
<p></p>
            </div>
                </div>