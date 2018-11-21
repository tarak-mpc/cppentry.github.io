---
layout:     post
title:      Hadoop中的HDFS的存储机制
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-github-gist">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<div id="article_content" class="article_content csdn-tracking-statistics tracking-click">

<p>
<strong>Hadoop中HDFS的存储机制</strong></p>
<p>
HDFS（Hadoop Distributed File System）是Hadoop分布式计算中的数据存储<a href="http://www.2cto.com/os/" rel="nofollow" class="keylink" target="_blank">系统</a>，是基于流数据模式访问和处理超大文件的需求而开发的。下面我们首先介绍HDFS中的一些基础概念，然后介绍HDFS中读写操作的过程，最后分析了HDFS的优缺点。</p>
<h1 id="1-hdfs中的基础概念"><a></a>
1. HDFS中的基础概念</h1>
<p>
 </p>
<p><span>Block：</span><span>HDFS中的存储单元是每个数据块block，HDFS默认的最基本的存储单位是64M的数据块。和普通的文件系统相同的是，HDFS中的文件也是被分成64M一块的数据块存储的。不同的是，在HDFS中，如果一个文件大小小于一个数据块的大小，它是不需要占用整个数据块的存储空间的。</span></p>
<p><span><span>NameNode：</span><span>元数据节点。</span></span><span>该节点用来管理文件系统中的命名空间，是master。其将所有的为了见和文件夹的元数据保存在一个文件系统树中，这些信息在硬盘上保存为了：命名空间镜像（namespace
 image）以及修改日志（edit log），后面还会讲到。此外，NameNode还保存了一个文件包括哪些数据块，分布在哪些数据节点上。然而，这些信息不存放在硬盘上，而是在系统启动的时候从数据节点收集而成的。</span></p>
<p><span><span>DataNode：</span><span>数据节点。</span></span><span>是HDFS真正存储数据的地方。客户端（client）和元数据节点（NameNode）可以向数据节点请求写入或者读出数据块。此外，DataNode需要周期性的向元数据节点回报其存储的数据块信息。</span></p>
<p><span><span>Secondary NameNode:</span><span>从元数据节点。</span></span><span>从元数据节点并不是NameNode出现问题时候的备用节点，它的主要功能是周期性的将NameNode中的namespace
 image和edit log合并，以防log文件过大。此外，合并过后的namespace image文件也会在Secondary NameNode上保存一份，以防NameNode失败的时候，可以恢复。</span></p>
<p><span><span>edit log：</span><span>修改日志。</span></span><span>当文件系统客户端client进行——写——操作的时候，我们就要把这条记录放在修改日志中。在记录了修改日志后，NameNode则修改内存中的数据结构。每次写操作成功之前，edit
 log都会同步到文件系统中。</span></p>
<p><span><span>fsimage：</span><span>命名空间镜像。</span></span><span>它是内存中的元数据在硬盘上的checkpoint。当NameNode失败的时候，最新的checkpoint的元数据信息就会从fsimage加载到内存中，然后注意重新执行修改日志中的操作。而Secondary
 NameNode就是用来帮助元数据节点将内存中的元数据信息checkpoint到硬盘上的。</span></p>
<p>
 </p>


<blockquote>
<p>
具体checkpoint的过程如下图：（参考hadoop集群的博客）</p>
</blockquote>




<blockquote>
<p>
checkpoint的过程如下：Secondary NameNode通知NameNode生成新的日志文件，以后的日志都写到新的日志文件中。Secondary NameNode用http get从NameNode获得fsimage文件及旧的日志文件。Secondary NameNode将fsimage文件加载到内存中，并执行日志文件中的操作，然后生成新的fsimage文件。Secondary NameNode将新的fsimage文件用http post传回NameNode。NameNode可以将旧的fsimage文件及旧的日志文件，换为新的fsimage文件和新的日志文件(第一步生成的)，然后更新fstime文件，写入此次checkpoint的时间。这样NameNode中的fsimage文件保存了最新的checkpoint的元数据信息，日志文件也重新开始，不会变的很大了。</p>
</blockquote>


<p>
<img alt="\" src="http://www.2cto.com/uploadfile/Collfiles/20141203/20141203091603126.png"></p>
<p>
<br></p>
<h1 id="2-hdfs中文件读写操作流程"><a></a>
2. HDFS中文件读写操作流程</h1>
<p>
在HDFS中，文件的读写过程就是client和NameNode以及DataNode一起交互的过程。我们已经知道NameNode管理着文件系统的元数据，DataNode存储的是实际的数据，那么client就会联系NameNode以获取文件的元数据，而真正的文件读取操作是直接和DataNode进行交互的。</p>
<p>
<strong><br></strong></p>
<p>
<strong>写文件的过程：</strong></p>
<p>
 客户端调用create()来创建文件DistributedFileSystem用RPC调用元数据节点，在文件系统的命名空间中创建一个新的文件。元数据节点首先确定文件原来不存在，并且客户端有创建文件的权限，然后创建新文件。DistributedFileSystem返回DFSOutputStream，客户端用于写数据。客户端开始写入数据，DFSOutputStream将数据分成块，写入data queue。Data queue由Data Streamer读取，并通知元数据节点分配数据节点，用来存储数据块(每块默认复制3块)。分配的数据节点放在一个pipeline里。Data
 Streamer将数据块写入pipeline中的第一个数据节点。第一个数据节点将数据块发送给第二个数据节点。第二个数据节点将数据发送给第三个数据节点。DFSOutputStream为发出去的数据块保存了ack queue，等待pipeline中的数据节点告知数据已经写入成功。如果数据节点在写入的过程中失败：</p>
<p>
 </p>
<p>
关闭pipeline，将ack queue中的数据块放入data queue的开始。<br>
当前的数据块在已经写入的数据节点中被元数据节点赋予新的标示，则错误节点重启后能够察觉其数据块是过时的，会被删除。<br>
失败的数据节点从pipeline中移除，另外的数据块则写入pipeline中的另外两个数据节点。<br>
元数据节点则被通知此数据块是复制块数不足，将来会再创建第三份备份。</p>
<span>当客户端结束写入数据，则调用stream的close函数。此操作将所有的数据块写入pipeline中的数据节点，并等待ack queue返回成功。最后通知元数据节点写入完毕。</span>
<p>
 </p>
<p>
<img alt="\" src="http://www.2cto.com/uploadfile/Collfiles/20141203/20141203091605130.png"></p>
<p>
<strong>读取文件的过程：</strong></p>
<p>
 </p>
<span>客户端(client)用FileSystem的open()函数打开文件DistributedFileSystem用RPC调用元数据节点，得到文件的数据块信息。对于每一个数据块，元数据节点返回保存数据块的数据节点的地址。DistributedFileSystem返回FSDataInputStream给客户端，用来读取数据。客户端调用stream的read()函数开始读取数据。DFSInputStream连接保存此文件第一个数据块的最近的数据节点。Data从数据节点读到客户端(client)当此数据块读取完毕时，DFSInputStream关闭和此数据节点的连接，然后连接此文件下一个数据块的最近的数据节点。当客户端读取完毕数据的时候，调用FSDataInputStream的close函数。
 在读取数据的过程中，如果客户端在与数据节点通信出现错误，则尝试连接包含此数据块的下一个数据节点。失败的数据节点将被记录，以后不再连接。</span><br><p>
 </p>
<p>
<img alt="\" src="http://www.2cto.com/uploadfile/Collfiles/20141203/20141203091605134.png"></p>
<h1 id="3-hdfs的优缺点分析"><a></a>
3. HDFS的优缺点分析</h1>
<p>
优点：</p>
<p>
1）能够处理超大的文件；</p>
<p>
2）流式访问数据。HDFS能够很好的处理“一次写入，多次读写”的任务。也就是说，一个数据集一旦生成了，就会被复制到不同的存储节点中，然后响应各种各样的数据分析任务请求。在多数情况下，分析任务都会涉及到数据集中的大部分数据。所以，HDFS请求读取整个数据集要比读取一条记录更加高效。</p>
<p>
3）可以运行在比较廉价的商用机器集群上。</p>
<p>
<strong>缺点和改进策略：</strong></p>
<p>
1）不适合低延迟数据访问：HDFS是为了处理大型数据集分析任务的，主要是为达到大数据分析，所以延迟时间可能会较高。改进策略：对于那些有低延时要求的应用程序，HBase是一个更好的选择。通过上层数据管理项目来尽可能地弥补这个不足。在性能上有了很大的提升，它的口号就是goes real time。使用缓存或多master设计可以降低client的数据请求压力，以减少延时。还有就是对HDFS系统内部的修改，这就得权衡大吞吐量与低延时了。</p>
<p>
2）无法高效存储大量小文件：因为Namenode把文件系统的元数据放置在内存中，所以文件系统所能容纳的文件数目是由Namenode的内存大小来决定。一般来说，每一个文件、文件夹和Block需要占据150字节左右的空间，所以，如果你有100万个文件，每一个占据一个Block，你就至少需要300MB内存。当前来说，数百万的文件还是可行的，当扩展到数十亿时，对于当前的硬件水平来说就没法实现了。还有一个问题就是，因为Map task的数量是由splits来决定的，所以用MR处理大量的小文件时，就会产生过多的Maptask，线程管理开销将会增加作业时间。举个例子，处理10000M的文件，若每个split为1M，那就会有10000个Maptasks，会有很大的线程开销；若每个split为100M，则只有100个Maptasks，每个Maptask将会有更多的事情做，而线程的管理开销也将减小很多。改进策略：要想让HDFS能处理好小文件，有不少方法。利用SequenceFile、MapFile、Har等方式归档小文件，这个方法的原理就是把小文件归档起来管理，HBase就是基于此的。对于这种方法，如果想找回原来的小文件内容，那就必须得知道与归档文件的映射关系。横向扩展，一个Hadoop集群能管理的小文件有限，那就把几个Hadoop集群拖在一个虚拟服务器后面，形成一个大的Hadoop集群。google也是这么干过的。多Master设计，这个作用显而易见了。正在研发中的GFS
 II也要改为分布式多Master设计，还支持Master的Failover，而且Block大小改为1M，有意要调优处理小文件啊。<br>
附带个Alibaba DFS的设计，也是多Master设计，它把Metadata的映射存储和管理分开了，由多个Metadata存储节点和一个查询Master节点组成。</p>
<p>
3）不支持多用户写入以及任意修改文件：在HDFS的一个文件中只有一个写入者，而且写操作只能在文件末尾完成，即只能执行追加操作。目前HDFS还不支持多个用户对同一文件的写操作，以及在文件任意位置进行修改。</p>
<p>
 </p>
                    </div>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>