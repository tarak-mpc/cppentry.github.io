---
layout:     post
title:      Hadoop之HDFS原理
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/ymf827311945/article/details/71682994				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>**什么是HDFS？ <br>
Hadoop分布式文件系统(HDFS)是一个适合运行在通用硬件(commodity hardware)上的分布式文件系统。它和现有的分布式文件系统有很多共同点。但同时，它和其他的分布式文件系统的区别也是很明显的。HDFS是一个高度容错性的系统，适合部署在廉价的机器上。HDFS能提供高吞吐量的数据访问，非常适合大规模数据集上的应用。HDFS放宽了一部分POSIX约束，来实现流式读取文件系统数据的目的。 <br>
HDFS的优点： <br>
1、高容错性（数据自动保存多个副本、一个数据默认有三个副本，副本丢失后，自动恢复）一台机器宕机之后，hadoop自动创建，如果恢复之后，不会删除多余的副本，因为删除磁盘上的数据太麻烦了，而且多一份数据没什么坏处，而且HDFS是水平扩展的，并不会占有很多空间—通过副本提高可靠性，提供了容错和恢复机制 <br>
2、适合批处理：移动计算而不移动数据，成本低  数据位置暴露给框架（数据本地性） <br>
3、适合大数据处理  大数据分析（log日志或者清洗过的数据进行分析）GB、TB、PB级  每天产生几十G、百万规模以上的文件数量、10K+ 节点 <br>
4、可构建在廉价的机器上  （百度 50-100万的服务器——物理节点）——容易宕机 <br>
HDFS的缺点： <br>
1、低延迟数据访问——毫秒级 <br>
2、低延迟和高吞吐率    MySQL 建索引，存在机器的内存，数据存在磁盘，提高速度 <br>
3、小文件存取  占用NameNode大量内存、寻道时间超过读取时间 <br>
4、并发写入和文件随机修改    一个文件只能有一个写者，一个owner，网盘没有修改功能，因为覆盖和修改的代价太高，仅支持append（追加） <br>
在HDFS中有一个概念叫replication（副本），它的理念就是把你存入到HDFS中的文件复制成几份，默认是3份，也就说，会把你的文件复制成三份，当其中一份丢失的时候，你可以使用另外一份，这样就保证了高容错性 <br>
Block的副本放置策略： <br>
1、第一份放置在上传文件的Datanode，如果是集群外提交，则随机挑选一台磁盘不太满，CPU不太忙的节点 <br>
2、第二份放置在与第一个副本不同的机架的节点上（一个机架的服务器公用一个网络和电源） <br>
3、第三份放置在与第二个副本相同的机架的节点上（不同机架的传输代价大，减小成本） <br>
4、更多副本：随机节点  如果只有一个机架，就在一个机架上放在三个不同的节点 <br>
HDFS的架构： <br>
主从结构—主：NameNode  从：DataNode <br>
Block特点： <br>
1、HDFS 数据存储单元（block）——Linux 4096逻辑单元块 <br>
Hadoop 数据块 64MB （1.0版本） <br>
2、Hadoop 128MB(2.0版本）  不到128MB会单独存成一个block    占该文件的大小空间 <br>
命令行：hadoop dfsadmin -safemode leave 离开安全模式 <br>
Block存在那些DataNode并不会落到本地磁盘，因为DataNode很有可能挂掉，block存在的位置会发生变化，所以Block采用DateNode每次启动时上报策略 <br>
针对Secondary版本的特点： <br>
metadata存到磁盘文件名叫fsimage <br>
edits记录对metadata的操作日志 <br>
HDFS存在safemode 安全模式，当完全启动后，自动退出 <br>
SecondaryNameNode 不是NameNode的备份，但可以做不完全一样的备份，主要是帮助NN合并edits log，减少NN的启动时间，进行合并，不会影响DataNode的数据，fsimage载入内存，开始执行edits的各种操作，拿走之后重新建立，SNN执行合并时机  时间间隔是默认3600秒，同时edits log 的大小，默认最大值是64MB   ，checkpoint 落地磁盘   阈值达到也开始落地磁盘，因为edits 文件达到64MB，命令太多可能就并不过来了———–也就是说，无论是达到3600s或者edits文件的大小达到64MB，都会立刻发生合并 <br>
为什么伪分布式的副本是一个：不能在同一个block上 <br>
针对HDFS-HA版本的特点 <br>
metadata： <br>
包含文件名、权限、Blocks等等 <br>
YARN：资源管理系统 <br>
ZooKeeper的znode定期落地磁盘、被动的存数据 <br>
ZK：监控（watcher）机制 <br>
safe mode:failover和刚启动的时候存在 <br>
metadata包含文件名、权限、Blocks等等 <br>
YARN：资源管理系统 <br>
ZooKeeper的znode定期落地磁盘、被动的存数据 <br>
ZK：监控（watcher）机制 <br>
safe mode:failover和刚启动的时候存在 <br>
Hadoop 1.0 HDFS、MapReduce <br>
2.0 MapReduce、YARN、HDFS、Others（其他计算框架） <br>
HDFS存在的问题： <br>
1、NameNode单点故障、难以应用于实际在线场景—单点故障 <br>
2、NameNode压力过大，且内存受限，影响系统扩展性—内存受限 <br>
MapReduce存在的问题： <br>
1、JobTracker访问压力大，影响系统扩展性（资源、计算） <br>
2、难以支持除MapReduce之外的计算框架（Spark、Storm） <br>
单点故障： <br>
HDFS HA：通过准备NameNode解决 <br>
内存受限问题：  <br>
HDFS Federation—水平扩展，支持多个NameNode，每个NameNode分管一部分目录，所有NameNode共享所有DataNode存储资源——不需要广播，所有的DataNode向两个NameNode汇报数据块信息 <br>
DataNode和副本不可能出现在一起 <br>
思考题：Federation的搭建——同步公用机制 <br>
针对fsimage 使用同步机制—数据量大，处理不过来—节约资源 <br>
针对edits 使用共用机制——数据量小 <br>
JN（journal node） 做到第三方存储，做到高可用，用于存储edits文件（奇数台） <br>
ZKFC（failover controller）：hadoop的一个类，代码封装的就是watcher <br>
基于ZooKeeper的自动切换：ZKFC、向ZooKeeper注册—-信息发送到Zookeeper中，然后进行注册 <br>
Hadoop的框架： <br>
1.0版本： <br>
HDFS、MapReduce、Jobtracker、Tasktracker <br>
2.0版本： <br>
MapReduce、YARN、HDFS、Others（其他计算框架） <br>
由于HDFS存在以下问题，所以采用HDFS-HA版本： <br>
1、NameNode单点故障、难以应用于实际在线场景——–单点故障 <br>
2、NameNode压力过大，且内存受限，影响系统扩展性—内存受限 <br>
由于MapReduce存在以下问题，所以在Hadoop 2.0版本采用Yarn： <br>
1、JobTracker访问压力大，影响系统扩展性（资源、计算） <br>
2、难以支持除MapReduce之外的计算框架（Spark、Storm） <br>
单点故障： <br>
HDFS HA：通过增加NameNode解决 <br>
内存受限问题：  <br>
HDFS Federation—水平扩展，支持多个NameNode，每个NameNode分管一部分目录，所有NameNode共享所有DataNode存储资源——不需要广播，所有的DataNode向两个NameNode汇报数据块信息 <br>
注意： <br>
DataNode和副本不可能出现在一起，因为如果Datanode挂掉之后，副本也会跟着挂掉，也就失去了HDFS的高容错性 <br>
针对Federation的搭建——同步公用机制 <br>
针对fsimage 使用同步机制—因为fsimage的数据量大，如果使用共用机制的话，处理不过来—节约资源 <br>
针对edits 使用共用机制——因为edits的数据量小**</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>