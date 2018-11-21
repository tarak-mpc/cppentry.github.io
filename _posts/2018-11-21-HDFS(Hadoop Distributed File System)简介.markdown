---
layout:     post
title:      HDFS(Hadoop Distributed File System)简介
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u010096900/article/details/78159810				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h3>简介</h3>
<p>本文简单介绍了HDFS的架构，并总结了HDFS的主要特性。<br></p>
<h3>分布式文件系统DFS(Distributed File System)</h3>
<p>在了解HDFS之前，先来看一下什么是分布式文件系统。分布式文件系统说的是跨越多台计算机或服务器来管理如文件或目录等数据。换句话说，分布式文件是允许我们在一个集群的多个节点或机器上存储数据，并允许多个用户访问这些数据的文件系统。基本上，它实现了其它文件系统如Windows系统上的NTFS， Mac系统上的HFS等相同的功能，唯一的差别就是在分布式文件系统的场景下，用户将数据存储在多台机器上而非单个机器上。尽管文件的存储跨越网络，在某种程度上，分布式文件系统对于数据的组织和显示，使得用户操作起来感觉所有的数据都存储在单台机器上。<br></p>
<h3>HDFS(Hadoop Distributed File System)</h3>
<p>HDFS是基于Java的分布式文件系统，允许用户在Hadoop集群里跨多个节点(node)存储大的数据。所以，如果你安装了Hadoop，你就可以在分布式环境下，使用HDFS作为存储系统来存储数据。这里举一个例子来理解HDFS。想象一下你拥有10台机器或计算机，每台机器拥有1TB容量的存储器。如果你在这10台机器上安装了Hadoop作为运行的平台，那HDFS将为你提供容量为10TB分布式存储服务，每台机器都将贡献它们的存储空间来存储任何类型的数据。<br>
HDFS是一个高度容错性的分布式文件系统，适合部署在廉价的机器上，能够提供高吞吐量的数据访问，非常适合大规模数据集上的应用。在介绍这些特性之前，我们先来了解一下HDFS架构。</p>
<h3>HDFS架构</h3>
<p>                <img src="https://img-blog.csdn.net/20171004225304368?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamluZ3VhbmdsaXU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br>
HDFS总体上采用了master/slave架构，主要由以下几个组件构成：Client、NameNode、Secondary NameNode和DataNode。<br>
NameNode管理文件系统的命名空间，维护文件系统树及整棵树内所有的文件和目录。这些信息以两个文件形式永久保存在本地磁盘上：命名空间镜像文件fsimage和编辑日志edits文件。NameNode也记录着每个文件中各个块所在的数据节点信息，但它并不永久保存块的位置信息，因为这些信息会在系统启动时由数据节点重建。<br>
Secondary NameNode最重要的任务并不是为NameNode元数据进行热备份，而是定期合并fsimage和edits日志，并传输给NameNode。这里需要注意的是，为了减少NameNode的压力，NameNode并不会自己合并fsimage和edits，而是将文件存储到磁盘上，交由Secondary NameNode完成。<br>
DataNode是文件系统的工作节点。它们根据需要存储并检索数据块(受客户端或NameNode调度)，并且定期向NameNode发送它们所存储的块的列表。<br>
客户端(client)代表用户通过与NameNode和DataNode交互来访问整个文件系统。客户端提供一个类似于POSIX的文件系统接口，因此用户在编程时无需知道NameNode和DataNode也可以实现其功能。</p>
<p><img src="https://img-blog.csdn.net/20171008181425826?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamluZ3VhbmdsaXU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>了解了HDFS架构，我们进一步分析HDFS特点。<br></p>
<h3>HDFS特点</h3>
<p><em><strong>分布式存储</strong></em><br>
假如Hadoop集群有10个DataNode节点，每个节点有1TB的存储容量，那么HDFS的总体容量可以达到10TB。当用户从Hadoop集群的10个节点的任一台访问HDFS时，用户都感觉在访问一个拥有10TB存储的单台大型服务器。<br><em><strong>分布式和并行计算</strong></em><br>
因为数据被存储到集群中的多台机器上，它将允许我们利用分布式并行计算的功能。这就意味单台机器处理10TB的文件需要43分钟，那在Hadoop集群中，数据分布存储到10台机器上，每台机器处理1TB的文件，这些处理可以并行运行，那么仅需要单台机器1/10的时间就可以处理完成。这样，可以提供高吞吐量的数据处理。</p>
<p><em><strong>                                                   <img src="https://img-blog.csdn.net/20171008181518875?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamluZ3VhbmdsaXU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br>
水平扩展</strong></em><br>
对于水平扩展，意味着你可以向已存在的集群中添加更多的节点，而不是增加单台机器的硬件容量。更重要的是添加更多机器到集群中并不需要中断系统。如果存储容量或计算能力不足，我们可以向Hadoop集群中添加新的DataNode节点即可。</p>
<p><img src="https://img-blog.csdn.net/20171008181626503?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamluZ3VhbmdsaXU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><em><strong>DataNode容错</strong></em><br>
DataNode以数据块作为容错单位，通常一个数据块会被复制到三个DataNode上去。一旦某个数据块访问失效，则去其他备份的机器上去读取。并且会把这个数据块再进行一次复制，以达到备份标准。<br>
也就是说，DataNode不是在机器级别上进行备份，而是在数据块级别上进行备份，这样就大大节省了机器数量。<br><br><em><strong>NameNode容错</strong></em><br>
没有NameNode，文件系统将无法使用。事实上，如果运行NameNode服务的机器坏掉，文件系统上所有的文件将会丢失，因为我们不知道如何根据data node的块重建文件。因此，对NameNode实现容错非常重要，Hadoop为此提供两种机制。<br>
第一种机制是备份那些组成文件系统元数据持久状态的文件。Hadoop可以通过配置使NameNode在多个文件系统上保存元数据的持久状态。这些写操作是实时同步的，是原子操作。一般的配置是，将持久状态写入本地磁盘的同时，写入一个远程挂载的网络文件系统NFS。<br>
另一种可行的方法使运行一个辅助(secondary) NameNode，但它不能被用作NameNode。这个辅助NameNode的重要作用是定期编辑日志合并命名空间镜像，以防止编辑日志过大。这个辅助NameNode一般在另外一台单独的物理计算机上运行，因为它需要占用大量的CPU时间与NameNode相同容量的内存来执行合并操作。它会保存合并后的命名空间镜像的副本，并在NameNode发生故障时启用。但是，辅助NameNode保存的状态总是滞后于主节点，所以在主节点全部失效时，难免会丢失部分数据。在这种情况下，一般把存储在NFS上的NameNode元数据复制到辅助NameNode并作为新的主NameNode运行。在这种情况下，要想从一个失效的NameNode恢复，系统管理员得启动一个拥有文件系统元数据副本的新的NameNode，并配置data
 node和客户端以便使用这个新的NameNode。新的NameNode直到满足以下情形才能够响应服务：1）将命名空间的印象导入内存中；2）重做编辑日志；3)接收到足够多来自data node的数据块报告并退出安全模式。<img src="" alt=""><br><em><strong>NameNode扩展</strong></em><br>
NameNode在内存中保存文件系统中每个文件和每个数据块的引用关系，这意味着对于一个拥有大量文件的超大集群来说，内存将成为限制消停横向扩展的瓶颈。在2.x发行版本中引入的联邦HDFS允许系统通过添加NameNode实现扩展，其中每个NameNode管理文件系统命名空间中的一部分。例如,一个NameNode可能管理/user目录下的所有文件，而另一个NameNode可能管理/share目录下的所有文件。<br>
在联邦环境下，每个NameNode维护一个命名空间卷(namespace volume), 包括命名空间的源数据和在该命名空间下的文件的所有数据块的数据池。命名空间卷之间相互独立的，两两之间并不相互通信，其中一个NameNode的失效也不会影响其它NameNode维护的命名空间的可用性。数据块池不再进行切分，因此集群中的data node需要注册到每个NameNode，并且存储着来自多个数据块池中的数据块。<br><br><em><strong>NameNode高可用性HA</strong></em><br>
Hadoop的2.x发行版本在HDFS中增加了高可用性(HA)的支持。在实现中，配置了一对活动-备用(active-standby) NameNode。当活动NameNode失效，备用NameNode就会接管它的任务并开始服务于来自客户端的请求，不会有任何明显中断。实现这一目标需要在架构上做如下修改：<br>
1. NameNode之间需要通过高可用的共享存储实现编辑日志的共享。当备用NameNode接管工作之后，它将通读共享编辑日志直至末尾，以实现与活动NameNode的状态同步，并继续读取由活动NameNode写入的新条目<br>
2. data node需要同时向两个NameNode发送数据块处理报告，因为数据块的映射信息存储在NameNode的内存中，而非磁盘。<br>
3. 客户端需要使用特定的机制来处理NameNode的失效问题，这一机制对用户透明。<br>
在活动NameNode失效后，备用NameNode能够快速（几十秒的时间）实现任务接管，因为最新的状态存储在内存中：包括最新的编辑日志条目和最新的数据块映射信息。实际观察到的失效时间稍长一点（需1分钟左右），这是因为系统需要保守确定活动NameNode是否真的失效了。<br></p>
            </div>
                </div>