---
layout:     post
title:      Hadoop之——HDFS
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/l1028386804/article/details/45849029				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<pre class="p0" style="text-indent:21pt;"><span style="font-family:'宋体';font-size:10.5pt;"><span style="font-family:'宋体';"><a href="http://blog.csdn.net/l1028386804/article/details/45849029" rel="nofollow">转载</a><span style="font-family:'宋体';"><a href="http://blog.csdn.net/l1028386804/article/details/45849029" rel="nofollow">请注明出处:http://blog.csdn.net/l1028386804/article/details/45849029</a></span></span>
</span></pre>
<p class="p0" style="text-indent:21pt;"><span style="font-family:'宋体';font-size:10.5pt;"> Hadoop 主要由HDFS和MapReduce 引擎两部分组成。最底部是HDFS，它存储Hadoop 集群中所有存储节点上的文件。HDFS 的上一层是MapReduce 引擎，该引擎由JobTrackers 和TaskTrackers组成<span style="font-family:'宋体';">。</span></span><span style="font-family:'宋体';"> </span><span style="font-family:'宋体';font-size:10.5pt;"><strong><br></strong></span></p>
<h3 class="p0" style="text-indent:21pt;"><span style="font-family:'宋体';font-size:10.5pt;"><strong>一、HDFS基本概念</strong></span></h3>
<h4><span style="font-family:'宋体';"></span>
</h4><p class="p0" style="text-indent:21pt;"><strong><span style="font-family:'宋体';font-size:10.5pt;">   1、</span><span style="font-family:'宋体';font-size:10.5pt;">数据块</span></strong></p>

<p><span style="font-family:'宋体';"></span></p>
<p class="p0" style="text-indent:21pt;margin-left:21pt;">
<span style="font-family:'宋体';font-size:10.5pt;">HDFS默认的最基本的存储单位是64M的数据块，这个数据块可以理解和一般的文件里面的分块是一样的</span></p>
<h4><span style="font-family:'宋体';"></span>
</h4><p class="p0" style="text-indent:21pt;"><strong><span style="font-family:'宋体';font-size:10.5pt;">   2、</span><span style="font-family:'宋体';font-size:10.5pt;">元数据节点和数据节点</span></strong></p>

<p><span style="font-family:'宋体';"></span></p>
<p class="p0" style="text-indent:21pt;margin-left:21pt;">
<span style="font-family:'宋体';font-size:10.5pt;">元数据节点（namenode）用来管理文件系统的命名空间，它将所有的文件和文件夹的元数据保存在一个文件系统树中。</span></p>
<p class="p0" style="text-indent:21pt;margin-left:21pt;">
<span style="font-family:'宋体';font-size:10.5pt;">数据节点（datanode）就是用来存储数据文件的。</span><span style="font-family:'宋体';font-size:10.5pt;"><br></span></p>
<p class="p0" style="text-indent:21pt;margin-left:21pt;">
<span style="font-family:'宋体';font-size:10.5pt;">从元数据节点（secondarynamenode）不是我们所想象的元数据节点的备用节点，其实它主要的功能是主要功能就是周期性将元数据节点的命名空间镜像文件和修改日志合并，以防日志文件过大。</span></p>
<span style="font-family:'宋体';font-size:10.5pt;">     这里先来弄清楚这个三种节点的关系吧！其实元数据节点上存储的东西就相当于一般文件系统中的目录，也是有命名空间的映射文件以及修改的日志，只是分布式文件系统就将数据分布在各个机器上进行存储罢了，下面你看看这几张说明图应该就能明白了。</span>
<p><span style="font-family:'宋体';"><span style="font-family:'宋体';font-size:10.5pt;"><span style="font-family:'宋体';"> <img src="https://img-blog.csdn.net/20150519195743715?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbDEwMjgzODY4MDQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" width="344" height="291"></span></span></span></p>
<p><br><span style="font-family:'宋体';"><span style="font-family:'宋体';font-size:10.5pt;"><span style="font-family:'宋体';"></span></span></span></p>
<p><span style="font-family:'宋体';"><span style="font-family:'宋体';font-size:10.5pt;"><span style="font-family:'宋体';"><img src="https://img-blog.csdn.net/20150519195819392?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbDEwMjgzODY4MDQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" width="339" height="224"></span></span></span></p>
<p><img src="https://img-blog.csdn.net/20150519195957801?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbDEwMjgzODY4MDQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" width="608" height="284"></p>
<p></p>
<p class="p0" style="text-indent:21pt;margin-left:21pt;">
<span style="font-family:'宋体';font-size:10.5pt;">Namenode与secondary namenode之间的进行checkpoint的过程。</span></p>
<p class="p0" style="text-indent:21pt;margin-left:21pt;">
 </p>
<h4 class="p0" style="text-indent:21pt;margin-left:21pt;">
<span style="font-family:'宋体';font-size:10.5pt;"><strong>3、HDFS中的数据流</strong></span></h4>
<p class="p0" style="text-indent:21pt;margin-left:21pt;">
<span style="font-family:'宋体';font-size:10.5pt;"><strong>读文件</strong></span></p>
<p class="p0" style="text-indent:21pt;margin-left:21pt;">
<span style="font-family:'宋体';font-size:10.5pt;">客户端(client)用FileSystem的open()函数打开文件，DistributedFileSystem用RPC调用元数据节点，得到文件的数据块信息。对于每一个数据块，元数据节点返回保存数据块的数据节点的地址。DistributedFileSystem返回FSDataInputStream给客户端，用来读取数据。客户端调用stream的read()函数开始读取数据。DFSInputStream连接保存此文件第一个数据块的最近的数据节点。Data从数据节点读到客户端(client)，当此数据块读取完毕时，DFSInputStream关闭和此数据节点的连接，然后连接此文件下一个数据块的最近的数据节点。当客户端读取完毕数据的时候，调用FSDataInputStream的close函数。</span></p>
<span style="font-family:'宋体';font-size:10.5pt;">   整个过程就是如图所示：</span>
<p>  <img src="https://img-blog.csdn.net/20150519200015238?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbDEwMjgzODY4MDQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" width="576" height="228"></p>
<p></p>
<p class="p0" style="text-indent:21pt;margin-left:21pt;">
<span style="font-family:'宋体';font-size:10.5pt;"><strong>写文件</strong></span></p>
<p class="p0" style="text-indent:21pt;margin-left:21pt;">
<span style="font-family:'宋体';font-size:10.5pt;">客户端调用create()来创建文件，DistributedFileSystem用RPC调用元数据节点，在文件系统的命名空间中创建一个新的文件。元数据节点首先确定文件原来不存在，并且客户端有创建文件的权限，然后创建新文件。DistributedFileSystem返回DFSOutputStream，客户端用于写数据。客户端开始写入数据，DFSOutputStream将数据分成块，写入data queue。Data queue由Data Streamer读取，并通知元数据节点分配数据节点，用来存储数据块(每块默认复制3块)。分配的数据节点放在一个pipeline里。Data Streamer将数据块写入pipeline中的第一个数据节点。第一个数据节点将数据块发送给第二个数据节点。第二个数据节点将数据发送给第三个数据节点。DFSOutputStream为发出去的数据块保存了ack queue，等待pipeline中的数据节点告知数据已经写入成功。如果数据节点在写入的过程中失败：关闭pipeline，将ack queue中的数据块放入data queue的开始。</span></p>
<p class="p0" style="text-indent:21pt;margin-left:21pt;">
<span style="font-family:'宋体';font-size:10.5pt;">整个过程如图所示：</span></p>
 <img src="https://img-blog.csdn.net/20150519200204672?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbDEwMjgzODY4MDQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" width="621" height="264"><p></p>
<p class="p0" style="text-align:center;text-indent:21pt;margin-left:21pt;">
<span style="font-family:'宋体';font-size:10.5pt;"><strong>HDFS构架与设计</strong></span></p>
<p class="p0"><span style="font-family:'宋体';font-size:10.5pt;">    Hadoop也是一个能够分布式处理大规模海量数据的软件框架，这一切都是在可靠、高效、可扩展的基础上。Hadoop的可靠性——因为Hadoop假设计算元素和存储会出现故障，因为它维护多个工作数据副本，在出现故障时可以对失败的节点重新分布处理。Hadoop的高效性——在MapReduce的思想下，Hadoop是并行工作的，以加快任务处理速度。Hadoop的可扩展——依赖于部署Hadoop软件框架计算集群的规模，Hadoop的运算是可扩展的，具有处理PB级数据的能力。</span></p>
<p class="p0"><span style="font-family:'宋体';font-size:10.5pt;">      Hadoop 主要由HDFS（Hadoop Distributed File System）和MapReduce 引</span><span style="font-family:'宋体';font-size:10.5pt;">擎两部分组成。最底部是HDFS，它存储Hadoop 集群中所有存储节点上的文件。HDFS 的上一层是MapReduce 引擎，该引擎由JobTrackers 和TaskTrackers组成</span><span style="font-family:'宋体';font-size:10.5pt;">。</span></p>
<p class="p0" style="text-indent:21pt;"><span style="font-family:'宋体';font-size:10.5pt;">  HDFS 可以执行的操作有创建、删除、移动或重命名文件等，架构类似于传统的分级文件系统。需要注意的是，HDFS 的架构基于一组特定的节点而构建（参见图2），这是它自身的特点。HDFS 包括唯一的NameNode，它在HDFS 内部提供元数据服务；DataNode 为HDFS 提供存储块。由于NameNode 是唯一的，这也是HDFS 的一个弱点（单点失败）。一旦NameNode 故障，后果可想而知。</span></p>
<h4 class="p0" style="text-indent:21pt;"><strong><span style="font-family:'宋体';font-size:10.5pt;">1、</span><span style="font-family:'宋体';font-size:10.5pt;">HDFS构架（如图所示）</span></strong></h4>
<img src="https://img-blog.csdn.net/20150519200413797?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbDEwMjgzODY4MDQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" width="636" height="241"><p></p>
<h4 class="p0" style="text-indent:21pt;"><strong><span style="font-family:'宋体';font-size:10.5pt;">2、</span><span style="font-family:'宋体';font-size:10.5pt;">HDFS的设计</span></strong></h4>
<p></p>
<p class="p0" style="text-indent:21pt;margin-left:21pt;">
<span style="font-family:'宋体';font-size:10.5pt;">1）</span><span style="font-family:'宋体';font-size:10.5pt;">错误检测和快速、自动的恢复是HDFS的核心架构目标。</span><span style="font-family:'宋体';font-size:10.5pt;"></span></p>
<p class="p0" style="text-indent:21pt;margin-left:21pt;">
<span style="font-family:'宋体';font-size:10.5pt;">2）</span><span style="font-family:'宋体';font-size:10.5pt;">比之关注数据访问的低延迟问题，更关键的在于数据访问的高吞吐量。</span><span style="font-family:'宋体';font-size:10.5pt;"></span></p>
<p class="p0" style="text-indent:21pt;margin-left:21pt;">
<span style="font-family:'宋体';font-size:10.5pt;">3）</span><span style="font-family:'宋体';font-size:10.5pt;">HDFS应用对文件要求的是write-one-read-many访问模型</span><span style="font-family:'宋体';font-size:10.5pt;">。</span><span style="font-family:'宋体';font-size:10.5pt;"></span></p>
<p class="p0" style="text-indent:21pt;margin-left:21pt;">
<span style="font-family:'宋体';font-size:10.5pt;">4）</span><span style="font-family:'宋体';font-size:10.5pt;">移动计算的代价比之移动数据的代价低。</span><span style="font-family:'宋体';font-size:10.5pt;"></span></p>
<p class="p0" style="text-indent:21pt;margin-left:21pt;">
<span style="font-family:'宋体';font-size:10.5pt;"></span> </p>
<h4 class="p0" style="text-indent:21pt;"><strong><span style="font-family:'宋体';font-size:10.5pt;">3、</span><span style="font-family:'宋体';font-size:10.5pt;">文件系统的namespace</span></strong></h4>
<p class="p0" style="text-indent:21pt;margin-left:21pt;">
<span style="font-family:'宋体';font-size:10.5pt;">Namenode维护文件系统的namespace，一切对namespace和文件属性进行修改的都会被namenode记录下来，连文件副本的数目称为replication因子，这个也是由namenode记录的。</span><span style="font-family:'宋体';font-size:10.5pt;"></span></p>
<p class="p0" style="text-indent:21pt;"><strong></strong></p>
<h4 class="p0" style="text-indent:21pt;"><strong><span style="font-family:'宋体';font-size:10.5pt;">4、</span><span style="font-family:'宋体';font-size:10.5pt;">数据复制</span></strong></h4>
<p class="p0" style="text-indent:21pt;margin-left:21pt;">
<span style="font-family:'宋体';font-size:10.5pt;">Namenode全权管理block的复制，它周期性地从集群中的每个Datanode接收心跳包和一个Blockreport。心跳包的接收表示该Datanode节点正常工作，而Blockreport包括了该Datanode上所有的block组成的列表。HDFS采用一种称为rack-aware的策略来改进数据的可靠性、有效性和网络带宽的利用。</span><span style="font-family:'宋体';font-size:10.5pt;">完成对副本的存放。</span><span style="font-family:'宋体';font-size:10.5pt;"></span></p>
<p class="p0" style="text-indent:21pt;"><strong></strong></p>
<h4 class="p0" style="text-indent:21pt;"><strong><span style="font-family:'宋体';font-size:10.5pt;">5、</span><span style="font-family:'宋体';font-size:10.5pt;">文件系统元数据的持久化</span></strong></h4>
<p class="p0" style="text-indent:21pt;margin-left:21pt;">
<span style="font-family:'宋体';font-size:10.5pt;">Namenode在内存中保存着整个文件系统namespace和文件Blockmap的映像。这个关键的元数据设计得很紧凑，因而一个带有4G内存的 Namenode足够支撑海量的文件和目录。当Namenode启动时，它从硬盘中读取Editlog和FsImage，将所有Editlog中的事务作用（apply)在内存中的FsImage ，并将这个新版本的FsImage从内存中flush到硬盘上,然后再truncate这个旧的Editlog，因为这个旧的Editlog的事务都已经作用在FsImage上了。这个过程称为checkpoint。在当前实现中，checkpoint只发生在Namenode启动时，在不久的将来我们将实现支持周期性的checkpoint。</span><span style="font-family:'宋体';font-size:10.5pt;"></span></p>
<p class="p0" style="text-indent:21pt;"><strong></strong></p>
<h4 class="p0" style="text-indent:21pt;"><strong><span style="font-family:'宋体';font-size:10.5pt;">6、</span><span style="font-family:'宋体';font-size:10.5pt;">通信协议</span></strong></h4>
<p class="p0" style="text-indent:21pt;margin-left:21pt;">
<span style="font-family:'宋体';font-size:10.5pt;">所有的HDFS通讯协议都是构建在TCP/IP协议上。客户端通过一个可配置的端口连接到Namenode，通过ClientProtocol与 Namenode交互。而Datanode是使用DatanodeProtocol与Namenode交互。从ClientProtocol和 Datanodeprotocol抽象出一个远程调用(RPC），在设计上，Namenode不会主动发起RPC，而是是响应来自客户端和 Datanode 的RPC请求。</span><span style="font-family:'宋体';font-size:10.5pt;"></span></p>
<p class="p0"> </p>
<p class="p0"><span style="font-family:'宋体';font-size:10.5pt;">   HDFS不是这么简单就能说清楚的，在以后的博客中我还会继续研究hadoop的分布式文件系统，包括HDFS的源码分析等，现由于时间有限，暂时只做了以上一些简单的介绍吧，希望对大家由此对HDFS有一定的了解！</span></p>
<br><span style="font-family:'宋体';"><span style="font-family:'宋体';font-size:10.5pt;"><span style="font-family:'宋体';"></span></span></span>
<p><span style="font-family:'宋体';"><span style="font-family:'宋体';font-size:10.5pt;"><span style="font-family:'宋体';"></span></span><br></span></p>
            </div>
                </div>