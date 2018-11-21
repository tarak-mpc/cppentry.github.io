---
layout:     post
title:      Hadoop分布式文件系统（HDFS）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/XueFengPlay/article/details/78869007				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="hdfs简介"><strong>HDFS简介</strong>：</h2>

<p>HDFS（Hadoop Distributed File System）是Hadoop项目的核心子项目，是分布式计算中数据存储管理的基础，是基于流数据模式访问和处理超大文件的需求而开发的，可以运行于廉价的商用服务器上。 <br>
       它所具有的高容错、高可靠性、高可扩展性、高获得性、高吞吐率等特征为海量数据提供了不怕故障的存储，为超大数据集的应用处理带来了很多便利。 <br>
<img src="https://img-blog.csdn.net/20171221190411785?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvWHVlRmVuZ1BsYXk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h1 id="1hdfs要求与限制"><strong>1、HDFS要求与限制</strong></h1>

<p><strong>能处理超大文件</strong> <br>
HDFS以支持大数据集合为目标，文件大小一般都在千兆至T字节，一个单一HDFS实例应该能支撑数以千万计的文件。</p>

<p><strong>流式数据访问</strong> <br>
HDFS设计的思想：一次写入、多次读取(write-one-read-many访问模型)。一个文件经过创建和写入，关闭之后就不需要改变。 <br>
      这一假设简化了数据一致性问题，使高吞吐量的数据访问成为可能。</p>

<p><strong>使用商用硬件</strong> <br>
      Hadoop不需要运行在昂贵并且高可靠性的硬件上，因此，<strong>硬件错误</strong>是常态。 <br>
      HDFS可能是有成百上千的server组成，任何一个组件都可能失效，因此错误检测和快速、自动地恢复是HDFS的核心架构目标。 <br>
      HDFS在面对这种故障时，被设计为能够继续运行而不让用户觉察到明显的中断。</p>

<p><strong>低延迟数据访问</strong> <br>
     HDFS为达到高数据吞吐量而优化的，这可能会以延迟为代价。因此，需要毫秒范围内低延迟访问数据的应用不适合HDFS。</p>

<p><strong>小文件存储问题</strong> <br>
     HDFS中的名称节点（Namenode）存储着文件系统的元数据，因此文件数量的限制也由NameNode的内存大小决定。 <br>
     HDFS上每个文件索引数据块的大小约为150个字节，因此，HDFS上存储文件个数的上限就能确定了。</p>

<p><strong>文件随机读写限制</strong> <br>
     HDFS中的文件只有一个写入者，而且写操作总是在文件的末尾。它不支持多个写入者，或是在文件的任意位置修改。</p>

<h4 id="总结"><strong>总结</strong>：</h4>

<ul>
<li>流式数据访问模式—简化了数据一致性问题，使数据并发访问成为可能。</li>
<li>廉价设备—错误检测、快速恢复、不丢失数据。</li>
<li>超大文件存储方式—-分布式存储、分布式访问。</li>
<li>HDFS存储空间—动态扩容</li>
<li>众多小文件的存储—–压缩</li>
<li>文件的随机读写—-Hbase</li>
</ul>

<hr>

<ul>
<li>大文件总是被分隔成很多小的文件块，分散存储在机房的各个机器的不同磁盘上。</li>
<li>文件的读写都是并发执行的</li>
<li>一个磁盘或者一台服务器出故障不影响业务。</li>
<li>可根据实际需要增加或者删除集群中的节点数</li>
</ul>



<h1 id="2hdfs设计需求"><strong>2、HDFS设计需求</strong></h1>

<p>针对HDFS的要求，设计需求大概是这么几个：</p>

<ul>
<li>透明性（针对HDFS的一切操作和管理都是透明的）</li>
<li>并发控制</li>
<li>可伸缩性</li>
<li>容错</li>
<li>安全需求</li>
</ul>



<h3 id="1透明性"><strong>（1）透明性</strong></h3>

<p>开放分布式处理的标准确定就有8种透明性：访问的透明性、位置的透明性、并发透明性、复制透明性、故障透明性、移动透明性、性能透明性和伸缩透明性。 <br>
      对于HDSF而言，最重要的是希望能达到如下5个透明性要求：</p>

<p><strong>访问的透明性</strong> <br>
用户访问本地文件和远程文件资源所采用的接口是一致的，通过修改配置文件来实现。访问HDFS时也不需要知道所访问的文件存放在集群的哪个节点的哪个磁盘上。 <br>
<img src="https://img-blog.csdn.net/20171221190411785?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvWHVlRmVuZ1BsYXk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>（a）Namenode负责HDFS的管理，Datanode负责HDFS中数据存储 <br>
（b）Client读写数据均需与Namenode连接，获得文件的存储位置，然后进行读写操作 <br>
（c）HDFS访问接口是通用的，可以通过配置决定访问HDFS还是本地文件系统</p>

<p><strong>位置的透明性：</strong> <br>
HDFS使用单一的文件命名空间，由Namenode来负责文件系统命名空间的管理。HDFS上文件的block可以重新分布复制，block可以增加或者减少副本，副本可以跨机架存储，而这一切对客户端都是透明的。</p>

<p>（a）文件可以分割成多个block，每个block存储的节点是在保证负载均衡的基础上随机确定的。 <br>
（b）为了保证数据不丢失，一个block都有多个副本。 <br>
（c）一个block的副本也是存放在不同节点上的</p>

<p><strong>移动的透明性：</strong> <br>
HDFS中的文件经常由于节点的失效、增加或者replication因子的改变或者重新均衡等进行着复制或者移动，而客户端和客户端程序并不需要改变什么，Namenode的edits日志文件记录着这些变更。</p>

<p>（a）Namenode中存储了两个非常重要的文件：fsimage和edits <br>
（b）fsimage是HDFS文件系统的一个镜像文件 <br>
（c）edits是HDFS最近操作的日志</p>

<p><strong>性能的透明性和伸缩的透明性：</strong> <br>
HDFS的目标就是构建在大规模廉价机器上的分布式文件系统集群，可伸缩性毋庸置疑，至于性能可以参考它首页上的一些benchmark。</p>

<p>（a）Slave文件记录了所有Datanode节点的hostname. <br>
（b）集群节点可以根据应用需要动态增加或者减少 <br>
（c）增加：在Namenode所在节点的slave文件中增加待增加Datanode节点的hostname，然后在相应的节点执行hadoop-daemon.sh start datanode <br>
（d）下架：集群中增加配置文件exclude。并把要下架的节点hostname写入到exclude文件中。或者在下架的节点上执行hadoop-daemon.sh stop datanode强制下架</p>



<h3 id="2并发控制"><strong>（2）并发控制</strong></h3>

<p>文件分隔成block，且分散存储在集群的各个节点的不同磁盘上，为大文件的并发读取提供了天然的优势。</p>

<p>（a）文件分隔后分散存储，大大增加了磁盘的IO，同时使分布式计算成为可能。 <br>
（b）block多副本也可实现同一数据多个并发读取，使得数据分析可并行执行。 <br>
（c）HDFS上的多个文件可以实现并发读写。</p>



<h3 id="3文件复制功能"><strong>（3）文件复制功能</strong></h3>

<p>鉴于商业设备的不可靠性，需要保证数据的安全性，确保数据不会丢失。HDFS采用了副本复制策略来实现这一要求。 <br>
<img src="https://img-blog.csdn.net/20171221193014153?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvWHVlRmVuZ1BsYXk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
H：Host  <br>
R：Rack 机架 <br>
D：DataCenter 数据中心</p>

<h4 id="hdfs副本复制策略"><strong>HDFS副本复制策略：</strong></h4>

<p>HDFS默认的副本因子replication=3，也就是说每个block都必须在HDFS上保存三份。</p>

<p>副本存放的策略也很有讲究，一个放在本地机架的节点，一个放在其他机架上，另一个放在同一机架的另一节点。这样可以最大限度地防止因故障导致的副本的丢失。不仅如此，HDFS读文件的时候也将优先选择从同一机架乃至同一数据中心的节点上读取block。 <br>
<img src="https://img-blog.csdn.net/20171221193513224?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvWHVlRmVuZ1BsYXk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
1st replica. 如果写请求方所在机器是其中一个Datanode，则直接存放在本地，否则由Namenode随机在集群中选择一个Datanode. <br>
2nd replica. 第二个副本存放于不同第一个副本的所在的机架。 <br>
3rd replica.第三个副本存放于第二个副本所在的机架，但是属于不同的节点。</p>



<h3 id="4硬件和操作系统的异构性"><strong>（4）硬件和操作系统的异构性</strong></h3>

<p>由于构建在java平台上，HDFS的跨平台能力毋庸置疑，得益于java平台已经封装好的文件IO系统，HDFS可以在不同的操作系统和计算机上实现同样的客户端和服务端程序。</p>

<h3 id="5容错能力"><strong>（5）容错能力</strong></h3>

<p>尽管使用廉价的商用设备，HDFS仍然需要为用户提供企业级专用设备所具有的可靠性和容错能力。因此，在客户端或者服务端出现问题的时候, HDFS必须保证文件服务仍能正常使用。HDFS的容错能力大概可以分为两个方面：文件系统的容错性、Hadoop本身的容错能力</p>



<h4 id="hdfs容错性"><strong>HDFS容错性</strong>：</h4>

<p>Datenode以固定周期向Namenode发送心跳，Namenode如果在一段时间内没有收到心跳，就会标记datenode为宕机。此段时间的计算公式是：timeout  = 2 * heartbeat.recheck.interval + 10 * dfs.heartbeat.interval     </p>

<p>而默认的heartbeat.recheck.interval 大小为5分钟，dfs.heartbeat.interval默认的大小为3秒。 所以Namenode如果在10分钟+30秒后，仍然没有收到Datanode的心跳，就认为Datanode已经宕机，并标记为dead. </p>

<p>Namenode就不会将任何新的IO操作派发给那个Datanode，该Datanode上的数据被认为是无效的，因此Namenode会检测是否有文件block的副本数目小于设置值，如果小于就自动开始复制新的副本并分发到其他Datanode节点。</p>

<p>由于节点的失效或者增加，可能导致数据分布的不均匀，当某个Datanode节点的空闲空间大于一个临界值的时候，HDFS会自动从其他Datanode迁移数据过来。</p>

<p>这就是<strong>HDFS的负载均衡策略</strong>。</p>



<h4 id="文件系统容错性"><strong>文件系统容错性</strong></h4>

<p>检测文件block的完整性，HDFS会记录每个新创建的文件的所有block的校验和。当以后检索这些文件的时候，从某个节点获取block，会首先确认校验和是否一致，如果不一致，会从其他Datanode节点上获取该block的副本。</p>

<p>Namenode上的fsimage和edits日志文件是HDFS的核心数据结构，如果这些文件损坏了，HDFS将失效。因而，Namenode可以配置成支持维护多 个fsimage和editlog的拷贝。任何对fsimage或者editlog的修改，都将同步到它们的副本上。它总是选取最近的一致的fsimage和editlog使用。</p>

<p>文件的删除，删除并不是马上从Namenode移出namespace，而是放在/trash目录随时可恢复，直到超过设置时间才被正式移除。。</p>

<h4 id="hadoop本身的容错性"><strong>Hadoop本身的容错性</strong></h4>

<p>Hadoop支持升级和回滚，当升级Hadoop软件时出现bug或者不兼容现象，可以通过回滚恢复到老的Hadoop版本。</p>



<h3 id="6安全性问题"><strong>（6）安全性问题</strong></h3>

<p>HDFS自身安全性是比较弱的，只有简单的与unix文件系统类似的文件许可控制。 <br>
Hadoop kerberos子系统服务安全和验证服务。</p>

<h3 id="总结-1"><strong>总结</strong></h3>

<p>HDFS作为通用的分布式文件系统并不适合，它在并发控制、缓存一致性以及小文件读写的效率上是比较弱的。但是它有自己明确的设计目标，那就是支持大的数据文件（兆至T级），并且这些文件以顺序读为主，以文件读的高吞吐量为目标，并且与MapReduce框架紧密结合。</p>



<h1 id="3hdfsnamenode启动过程"><strong>3、HDFS—NameNode启动过程</strong></h1>

<p>1、namenode格式化时生成fsimage文件。 <br>
2、第一次启动namenode硬盘中将fsimage加载到内存中。 <br>
3、datanode向namenode发送block report，汇报datanode的数据节点情况。如果HDFS存在修改，则写入edits日志中。</p>



<h1 id="4hdfsfsimage与edits合并机制"><strong>4、HDFS—fsimage与edits合并机制</strong></h1>

<p><img src="https://img-blog.csdn.net/20171222000038547?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvWHVlRmVuZ1BsYXk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
<strong>fsimage：HDFS元数据文件</strong> <br>
<strong>edits：HDFS操作日志</strong></p>

<p>（1）Primary Namenode启动时读取fsimag文件并加载到内存。 <br>
（2） Primary NameNode相应客户端的操作，生成edits文件。 <br>
（3）Secondary Namenode周期性同Primary Namenode通信，请求停止使用edits文件，将新的操作写入到edits.new文件中。 <br>
（4） Secondary Namenode从Primary Namenode读取fsimage和edits文件，并将它们合并成fsimage.ckpt，并发送给Primary Namenode <br>
（5）Primary Namenode将接收到的fsimage文件替换原来的fsimage，同时用edits.new替换原来的edits。</p>



<h1 id="5hdfs负载均衡策略"><strong>5、HDFS负载均衡策略</strong></h1>

<p><strong>进行数据的负载均衡调整，必须要满足如下原则：</strong></p>

<ul>
<li>数据平衡不能导致数据块减少，数据块备份丢失</li>
<li>管理员可以中止数据平衡进程</li>
<li>每次移动的数据量以及占用的网络资源，必须是可控的</li>
<li>数据均衡过程，不能影响Namenode的正常工作</li>
</ul>

<p>数据均衡过程的核心是一个数据均衡算法，该数据均衡算法将不断迭代数据均衡逻辑，直至集群内数据均衡为止。该数据均衡算法每次迭代的逻辑如下： <br>
<img src="https://img-blog.csdn.net/20171222000435891?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvWHVlRmVuZ1BsYXk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>（1）数据均衡服务（Rebalancing Server）首先要求 NameNode 生成 DataNode 数据分布分析报告,获取每个DataNode磁盘使用情况 <br>
（2）Rebalancing Server汇总需要移动的数据分布情况，计算具体数据块迁移路线图。数据块迁移路线图，确保网络内数据移动的路径最短 <br>
（3）开始数据块迁移任务，Proxy Source Data Node复制一块需要移动数据块 <br>
（4）将复制的数据块复制到目标DataNode上 <br>
（5）删除原始数据块 <br>
（6）目标DataNode向Proxy Source Data Node确认该数据块迁移完成 <br>
（7）Proxy Source Data Node向Rebalancing Server确认本次数据块迁移完成。然后继续执行这个过程，直至集群达到数据均衡标准 <br>
<img src="https://img-blog.csdn.net/20171222000622579?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvWHVlRmVuZ1BsYXk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>（1）HDFS会把当前的DataNode节点,根据阈值的设定情况划分到Over、Above、Below、Under四个组中。 <br>
（2）在移动数据块的时候，Over组、Above组中的块向Below组、Under组移动。</p>



<h3 id="导致负载不均衡的原因"><strong>导致负载不均衡的原因</strong>：</h3>

<p>（1）部分节点网络延迟大 <br>
（2）一些节点经常出现故障 <br>
（3）集群中增加了新的节点</p>

<p>当HDFS负载不均衡时，需要对HDFS进行数据的负载均衡调整，即对各节点机器上数据的存储分布进行调整。从而，让数据均匀的分布在各个DataNode上，均衡IO性能，防止热点的发生。HDFS为管理员提供了一个工具，用于分析数据块分布和重新均衡DataNode上的数据分布：</p>

<p>$HADOOP_HOME/bin/start-balancer.sh -t 10%</p>

<p>在这个命令中，-t参数后面跟的是HDFS达到平衡状态的磁盘使用率偏差值。如果机器与机器之间磁盘使用率偏差小于10%，那么我们就认为HDFS集群已经达到了平衡状态。</p>

<h1 id="6hdfs读文件"><strong>6、HDFS读文件</strong></h1>

<p><img src="https://img-blog.csdn.net/20171222001018127?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvWHVlRmVuZ1BsYXk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
（1）客户端通过调用FileSystem对象的open()来读取希望打开的文件。</p>

<p>（2）DistributedFileSystem通过RPC来调用namenode，以确定文件的开头部分的块位置。对于每一块，namenode返回具有该块副本的datanode地址。DistributedFileSystem 返回一个FSDataInputStream对象给client读取数据，FSDataInputStream转而包装成一个DFSInputStream对象</p>

<p>（3）client对这个输入流调用read()方法。存储着文件开头部分的块的数据节点的地址DFSInputStream随即与这些块最近的datanode相连接。</p>

<p>（4）通过在数据流中反复调用read()，数据会从datanode返回client。</p>

<p>（5）到达块的末端时，DFSInputStream会关闭与datanode间的联系，然后为下一个块找到最佳的datanode。client端只需要读取一个连续的流，这些对于client来说都是透明的。</p>

<p>client在读取文件时，如果与datanode通信遇到错误，那么它就会去尝试对这个块来说下一个最近的块，并记住那个故障的datanode，以保证不会再对之后的块进行徒劳无益的尝试。</p>

<p>client也会确认datanode发来的数据的校验和。如果发现一个损坏的块，client在会试图从别的datanode中读取一个块的副本之前，将这个错误报告给namenode。</p>

<p>client检索数据时，总是被namenode指引到块中最好的datanode。（这里涉及到一个数据块选择算法）</p>

<p>集群中，namenode仅提供数据块的位置请求（存储在内存中，十分高效），不是提供数据。否则如果客户端数量增长，namenode就会快速成为一个“瓶颈”。</p>

<h1 id="7hdfs写文件"><strong>7、HDFS写文件</strong></h1>

<p><img src="https://img-blog.csdn.net/20171222001309295?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvWHVlRmVuZ1BsYXk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
（1）客户端通过在DistributedFileSystem中调用create()来创建文件。</p>

<p>（2）DistributedFileSystem 使用RPC去调用namenode，在HDFS命名空间创一个新的文件。HDFS返回一个文件系统数据输出流FSDataOutputStream，让client开始写入数据。 FSDataOutputStream控制一个DFSOutputStream，负责处理datanode和namenode之间的通信。</p>

<p>（3）client写入数据时，DFSOutputStream将文件切分一个个的Block，对于每个block有分成一个个数据包写入数据队列。数据队列在数据节点管线中流动。</p>

<p>（4）对于任意一个数据包，首先写入管线中第一个的datanode，第一个节点会存储包并且发送给管线中的第二个datanode。同样地，第二个datanode存储包并且传给管线中的第三个数据节点。与此同时，client会将当前block的下一个包发给第一个datanode。</p>

<p>（5）DFSOutputStream也有一个内部的包队列来等待datanode收到确认。一个Block只有其所有的包在被管线中所有的节点确认后才会被移除出确认队列。</p>

<p>（6）client完成数据的写入后，就会在流中调用close()。</p>

<p>（7）向namenode节点发生写文件已经完成的消息。与此同步的，datanode主动向namenode汇报所存储文件块的位置信息</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>