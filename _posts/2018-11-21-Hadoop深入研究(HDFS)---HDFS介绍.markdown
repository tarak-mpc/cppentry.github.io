---
layout:     post
title:      Hadoop深入研究(HDFS)---HDFS介绍
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1>HDFS 设计原则:</h1>
<span></span>1. 非常大的文件<br><span></span>2. 流式数据访问<br><span></span>基于一次写, 多次读<br><br><br>
HDFS不适用的场景<br><span></span>1. 低延迟的数据访问<br><span></span>HDFS的强项在于大量的数据传输, 低延迟不适合HDFS, 10毫秒以下的访问可以无视HDFS,不过HBase可以弥补这个缺陷.<br><span></span>2. 太多小文件<br><span></span>namenode节点在内存中hold住了整个文件系统的元数据, 因此文件的数量就会收到限制, 每个文件的元数据大约150个字节, 100万个文件, 每个文件只占用一个block, 那么就需要300MB内存, 你的服务器可以hlod住多少呢? 可以自己算.<br><span></span>3. 多处写和随机修改<br><span></span>目前还不支持多处写入以及通过偏移量随机修改.<br><br><br><h1>HDFS BLOCK</h1>
<span></span>为了最小化查找时间比例，hdfs的块要比磁盘的块大很多。hdfs块的大小默认为64MB，和文件系统的块不同，<br><span></span>hdfs的文件可以小于块大小，并且不会占满整个块大小。<br><span></span>查找时间在10ms左右，数据传输几率在100MB/s,为了使查找时间是传输时间的1%，块的大小必须在100MB左右<br><span></span>一般都会设置为128MB<br><br><br><span></span>有了块的抽象之后，hdfs有了三个优点：<br><span></span>1.可以存储比单个磁盘更大的文件<br><span></span>2.存储块比存储文件更加简单，每个块的大小都基本相同<br><span></span>3.使用块比文件更适合做容错性和高可用<br><br><br><h1>NameNodes 和 DataNodes</h1>
<span></span>HDFS集群由两种类型的节点, 一种为master即NameNode, 另一种为Worker即DataNodes.<br><span></span>NameNode节点管理文件系统的命名空间, 它包含一个文件系统的树, 所有文件和目录的元数据都在这个树上, 这些信息被存储在本地磁盘的两个文件中, image文件和editlog文件, 文件相关的块存储在那个块中, 块在哪个地方, 这些信息都是在系统启动的时候加载到NameNode的内存中, 并不会存储在磁盘中.<br><br><br><span></span>Datanode节点在文件系统中充当的角色就是苦力，按照Namenode和Client的指令进行存储或者检索block，并且周期性<br><span></span>的向Namenode节点报告它存了哪些文件的block<br><br><br><span></span>Namenode节点如果不能使用了，那么整个hdfs就玩完了。为了防止这种情况，有两种方式可供选择<br><span></span>1. NameNode通过配置元数据可以写到多个磁盘中, 最好是独立的磁盘, 或者NFS.<br><span></span>2. 使用第二NameNode节点, 第二NameNode节点平时并不作为NameNode节点工作, 它的主要工作内容就是定期根据编辑日志(Editlog)合并命名空间的镜像(namespace image).防止编辑日志过大, 合并后的image它自己也保留一份, 等着NameNode节点挂掉, 然后它就可以转正, 由于不是实时的，有数据上的损失是很可能发生的。<br><br><br><h1>HDFS Federation</h1>
<span></span>NameNode节点保持所有的文件和块的引用在内存中.这就意味着在一个拥有很多很多文件的很大的集群中, 内存就成为了一个限制条件, HDFS Federation在Hadoop 2.x 中被实现, 允许HDFS有多个NameNode节点, 每个管理HDFS的一部分, 比如一个管理/user, 另一个管理/home , 每个NameNode节点是相互隔离的, 一个挂掉不会影响另一个.<br><br><br><h1>HDFS 的高可用</h1>
<span></span>不管NameNode节点的备份还是第二NameNode节点都只能保证数据的恢复, 并不能保证HDFS的高可用性, 一旦NameNode节点挂掉就会产生单点故障, 这时候就要手动去数据备份恢复, 或者启用第二节点. 新的NameNode节点在对外服务时要做三件事:<br><span></span>1. 把命名空间的镜像加载到内存中<br><span></span>2. 重新运行编辑日志<br><span></span>3. 接受各个DataNode节点的Block报告.<br><span></span>在一个大型一点的hdfs系统中，等这些做完需要30分钟左右。<br><br><br><span></span>Hadoop 2.x 已经支持了高可用性(HA), 通过一对NameNode热备来实现, 一台挂掉, 备机马上提供无中断服务.要实现HA要做三点微调:<br><span></span>1. NameNode节点必须使用高可用的共享存储<br><span></span>2. DataNode节点必须向两个DataNode节点发送Block报告<br><span></span>3. 客户端做改动可以在故障时切换到可用的NameNode节点上, 而且要对用户是无感知的.<br><br><br><h1>Failover 和Fencing</h1>
<span></span>将备份NameNode激活的过程就叫做Failover(失效备援), 管理激活备份NameNode的系统叫做Failover controller.<br><span></span>Zookeeper就可以担当这样的角色, 可以保证只有一个节点处于激活状态.<br><span></span>必须确认原来的namenode已经真的挂掉了，很多时候只是网络延迟，如果备份节点已经激活了，原来的节点又可以提供服务了，这样是不行的，防止原来NameNode活过来的过程就叫Fencing.可以用STONITH实现, STONITH可以做到直接断电把源NameNode节点fencing掉
            </div>
                </div>