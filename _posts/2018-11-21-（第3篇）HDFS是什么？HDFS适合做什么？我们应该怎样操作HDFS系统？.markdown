---
layout:     post
title:      （第3篇）HDFS是什么？HDFS适合做什么？我们应该怎样操作HDFS系统？
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/ijia1/article/details/62040634				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2 style="color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;">
<span><span style="font-family:'微软雅黑';font-size:21px;"><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;color:rgb(51,51,51);">点击链接 </span><a href="http://blog.csdn.net/ijia1" rel="nofollow" style="text-decoration:none;color:rgb(12,137,207);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;"><strong><span style="color:rgb(255,0,0);">http://blog.csdn.net/ijia1 </span></strong></a><span style="font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;color:rgb(51,51,51);"> 访问我的博客，学习更多<a href="http://lib.csdn.net/base/hadoop" rel="nofollow" class="replace_word" title="Hadoop知识库" style="text-decoration:none;color:rgb(223,52,52);font-weight:bold;">Hadoop</a>知识。</span><br></span></span></h2>
<h2 style="color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;">
<span><span style="font-family:'微软雅黑';font-size:21px;"><br></span></span></h2>
<h2 style="color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;">
<span><span style="font-family:'微软雅黑';font-size:21px;">H</span><span style="font-family:'微软雅黑';font-size:21px;">DFS</span><span style="font-size:21px;font-family:'微软雅黑';">文件系统</span></span></h2>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;text-indent:28px;">
<span style="font-family:'微软雅黑';">Hadoop 附带了一个名为 HDFS(Hadoop分布式文件系统)的分布式文件系统，<span>专门存储超大数据文件，为整个</span>Hadoop生态圈提供了<span>基础的存储服务。</span></span></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;text-indent:28px;">
<span style="font-family:'微软雅黑';">本章内容：</span></p>
<p style="margin-left:56px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'微软雅黑';">1) HDFS<span>文件系统的特点，以及不适用的场景</span></span></p>
<p style="margin-left:56px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'微软雅黑';">2) HDFS文件<span>系统重点知识点：体系架构和数据读写流程</span></span></p>
<p style="margin-left:56px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'微软雅黑';">3) <span>关于操作</span>HDFS文件系统<span>的一些基本用户命令</span></span></p>
<h3 style="color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;">
<span style="font-family:'宋体';font-size:21px;">1. </span><span><span style="font-family:'宋体';font-size:21px;">HDFS<span>特点</span></span><span style="font-size:21px;font-family:'宋体';">：</span></span></h3>
<p style="margin-left:28px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'微软雅黑';">HDFS<span>专为解决大数据存储问题而产生的，其具备了以下特点：</span></span></p>
<p style="margin-left:56px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'微软雅黑';">1) HDFS<span>文件系统可存储超大文件</span></span></p>
<p style="margin-left:28px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;text-indent:28px;">
<span style="font-family:'微软雅黑';"><span>每个磁盘都有默认的数据块大小，这是磁盘在对数据进行读和写时要求的最小单位，文件系统是要构建于磁盘上的，文件系统的也有块的逻辑概念，通常是磁盘块的整数倍，通常文件系统为几千个字节，而磁盘块一般为</span>512个字节。</span></p>
<p style="margin-left:28px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;text-indent:28px;">
<span style="font-family:'微软雅黑';">HDFS是一种文件系统<span>，自身也有块（</span>block）<span>的概念，其文件块要比普通单一磁盘上文件系统大的多，默认是</span>64MB<span>。</span></span></p>
<p style="margin-left:28px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'微软雅黑';">HDFS<span>上的块之所以设计的如此之大，其目的是为了最小化寻址开销。</span></span></p>
<p style="margin-left:28px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'微软雅黑';">HDFS文件<span>的大小可以大于网络中任意一个磁盘的容量，文件的所有块并不需要存储在一个磁盘上，因此可以利用集群上任意一个磁盘进行存储，由于具备这种分布式存储的逻辑，所以可以存储超大的文件，通常</span>G<span>、</span>T<span>、</span>P级别<span>。</span></span></p>
<p style="margin-left:56px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'微软雅黑';">2) <span>一次写入，多次读取</span></span></p>
<p style="margin-left:42px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;text-indent:14px;">
<span style="font-family:'微软雅黑';">一个文件经过创建、写入和关闭之后就不需要改变，这个假设简化了数据一致性的问题，同时提高数据访问的吞吐量。</span></p>
<p style="margin-left:56px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'微软雅黑';">3) <span>运行在普通廉价的机器上</span></span></p>
<p style="margin-left:28px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;text-indent:28px;">
<span style="font-family:'微软雅黑';">Hadoop<span>的设计对硬件要求低，无需昂贵的高可用性机器上，因为在</span>HDFS设计中充分考虑<span>到了数据的可靠性、安全性和高可用性。</span></span></p>
<h3 style="color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;">
<span style="font-family:'宋体';font-size:21px;">2. </span><span><span style="font-size:21px;font-family:'宋体';">不适用于</span><span style="font-family:Calibri;font-size:21px;">HDFS<span style="font-family:'宋体';">的场景：</span></span></span></h3>
<p style="margin-left:56px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'微软雅黑';">1) <span>低延迟</span></span></p>
<p style="margin-left:28px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;text-indent:28px;">
<span style="font-family:'微软雅黑';">HDFS<span>不适用于实时查询这种对延迟要求高的场景，例如：股票实盘。往往应对低延迟数据访问场景需要通过数据库访问索引的方案来解决，</span>Hadoop<span>生态圈中的</span>Hbase<span>具有这种随机读、低延迟等特点。</span></span></p>
<p style="margin-left:56px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'微软雅黑';">2) <span>大量小文件</span></span></p>
<p style="margin-left:28px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;text-indent:28px;">
<span style="font-family:'微软雅黑';"><span>对于</span>Hadoop<span>系统，小文件通常定义为远小于</span>HDFS的block size（<span>默认</span>64MB）<span>的文件，由于每个文件都会产生各自的</span>MetaData元数据，Hadoop通过Namenode来存储这些信息，<span>若小文件过多，容易导致</span>Namenode存储出现瓶颈<span>。</span></span></p>
<p style="margin-left:56px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'微软雅黑';">3) <span>多用户更新</span></span></p>
<p style="margin-left:28px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;text-indent:28px;">
<span style="font-family:'微软雅黑';"><span>为了保证并发性，</span>HDFS<span>需要一次写入多次读取，目前不支持多用户写入，若要修改，也是通过追加的方式添加到文件的末尾处，出现太多文件需要更新的情况，</span>Hadoop是不支持的<span>。</span></span></p>
<p style="margin-left:28px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;text-indent:28px;">
<span style="font-family:'微软雅黑';"><span>针对有多人写入数据的场景，可以考虑采用</span>Hbase<span>的方案。</span></span></p>
<p style="margin-left:56px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'微软雅黑';">4) <span>结构化数据</span></span></p>
<p style="margin-left:28px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;text-indent:28px;">
<span style="font-family:'微软雅黑';">HDFS适合<span>存储半结构化和非结构化数据，若有严格的结构化数据存储场景，也可以考虑采用</span>Hbase的方案。</span></p>
<p style="margin-left:56px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'微软雅黑';">5) <span>数据量并不大</span></span></p>
<p style="margin-left:28px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;text-indent:28px;">
<span style="font-family:'微软雅黑';"><span>通常</span>Hadoop<span>适用于</span>TB<span>、</span>PB数据，若待处理的数据只有几十GB的话，不建议<span>使用</span>Hadoop<span>，因为没有任何好处。</span></span></p>
<h3 style="color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;">
<span style="font-family:'宋体';font-size:21px;">3. </span><span><span style="font-family:'宋体';font-size:21px;">HDFS<span>体系</span></span><span style="font-size:21px;font-family:'宋体';">架构</span></span></h3>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;text-indent:28px;">
<span style="font-family:'微软雅黑';">HDFS<span>是一个主</span>/<span>从（</span>Master/Slave<span>）体系架构，由于分布式存储的性质，集群拥有两类节点</span>NameNode和DataNode<span>。</span></span></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'微软雅黑';">NameNode<span>（名字节点）：系统中通常只有一个，中心服务器的角色，管理存储和检索多个</span>DataNode的实际数据所需的所有元数据<span>。</span></span></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'微软雅黑';">DataNode（<span>数据节点）：系统中通常有多个，是文件系统中真正存储数据的地方，在</span>NameNode统一调度下进行数据块的<span>创建、删除和复制。</span></span></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;text-align:center;">
<span style="font-family:'微软雅黑';"> <a href="https://s4.51cto.com/wyfs02/M02/8E/50/wKioL1i9KcmCIznvAAB4PhOoKbo579.png" rel="nofollow" style="color:rgb(1,95,145);text-decoration:none;"><img src="https://s4.51cto.com/wyfs02/M02/8E/50/wKioL1i9KcmCIznvAAB4PhOoKbo579.png" title="第四章1.png" alt="wKioL1i9KcmCIznvAAB4PhOoKbo579.png" width="650" style="vertical-align:top;border:none;"></a></span></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;text-indent:28px;">
<span style="font-family:'微软雅黑';"><span>图中的</span>Client<span>是</span>HDFS<span>的客户端，是应用程序可通过该模块与</span>NameNode和DataNode进行交互，进行文件的读写操作。</span></p>
<h3 style="color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;">
<span style="font-family:'宋体';font-size:21px;">4. </span><span><span style="font-family:'宋体';font-size:21px;">HDFS</span><span style="font-size:21px;font-family:'宋体';">数据块复制</span></span></h3>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;text-indent:28px;">
<span style="font-family:'微软雅黑';"><span>为了系统容错，文件系统会对所有数据块进行副本复制多份，</span>Hadoop<span>是默认</span>3副本<span>管理。</span></span></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;text-indent:28px;text-align:center;">
<span style="font-family:'微软雅黑';"><a href="https://s5.51cto.com/wyfs02/M00/8E/51/wKioL1i9KpyiQG9YAACGRYANGLE426.png" rel="nofollow" style="color:rgb(1,95,145);text-decoration:none;"><img src="https://s5.51cto.com/wyfs02/M00/8E/51/wKioL1i9KpyiQG9YAACGRYANGLE426.png" title="第四章2.png" alt="wKioL1i9KpyiQG9YAACGRYANGLE426.png" style="vertical-align:top;border:none;"></a> </span></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;text-indent:28px;">
<span style="font-family:'微软雅黑';">复本管理策略是运行客户端的节点上放一个复本（若客户端运行在集群之外，会随机选择一个节点），第二个复本会放在与第一个不同且随机另外选择的机架中节点上，第三个复本与第二个复本放在相同机架，切随机选择另一个节点。所存在其他复本，则放在集群中随机选择的节点上，不过系统会尽量避免在相同机架上放太多复本。</span></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;text-indent:28px;">
<span style="font-family:'微软雅黑';"><span>所有有关块复制的决策统一由</span>NameNode负责，NameNode会周期性地接受</span><span style="font-family:'微软雅黑';color:rgb(62,62,62);"><span>集群中数据节点</span>DataNode的心跳和块报告。一个心跳的到达表示这个数据节点是正常的。一个块报告包括该数据节点上所有块的列表。</span></p>
<h3 style="color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;">
<span style="font-family:'宋体';font-size:21px;">5. </span><span><span style="font-family:'宋体';font-size:21px;">H</span><span style="font-family:Calibri;font-size:21px;">DFS<span style="font-family:'宋体';">读取</span></span><span style="font-size:21px;font-family:'宋体';">和</span><span style="font-size:21px;font-family:'宋体';">写入流程</span></span></h3>
<p style="margin-left:56px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'微软雅黑';">1) <span>读文件的过程：</span></span></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;text-indent:28px;text-align:center;">
<span style="font-family:'微软雅黑';"><a href="https://s4.51cto.com/wyfs02/M00/8E/51/wKioL1i9Kt6Tt0gKAAHxOTzccog276.png" rel="nofollow" style="color:rgb(1,95,145);text-decoration:none;"><img src="https://s4.51cto.com/wyfs02/M00/8E/51/wKioL1i9Kt6Tt0gKAAHxOTzccog276.png" title="第四章3.png" alt="wKioL1i9Kt6Tt0gKAAHxOTzccog276.png" style="vertical-align:top;border:none;"></a> </span></p>
<p style="margin-left:2px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;text-indent:26px;">
<span style="font-family:'微软雅黑';color:rgb(62,62,62);"><span>首先</span>Client<span>通过</span>File System的Open函数打开文件<span>，</span>Distributed
 File System用RPC调用NameNode<span>节点，得到文件的数据块信息。对于每一个数据块，</span>NameNode<span>节点返回保存数据块的数据节点的地址。</span>Distributed File System返回FSDataInputStream给客户端，用来读取数据。客户端调用stream的read()函数开始读取数据。DFSInputStream连接保存此文件第一个数据块的最近的数据节点。DataNode从数据节点读到客户端(client)<span>，当此数据块读取完毕时，</span>DFSInputStream关闭和此数据节点的连接，然后连接此文件下一个数据块的最近的数据节点。当客户端读取完毕数据的时候，调用FSDataInputStream的close函数。</span></p>
<p style="margin-left:2px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;text-indent:26px;">
<span style="color:rgb(62,62,62);font-family:'微软雅黑';">在读取数据的过程中，如果客户端在与数据节点通信出现错误，则尝试连接包含此数据块的下一个数据节点。失败的数据节点将被记录，以后不再连接。</span></p>
<p style="margin-left:56px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'微软雅黑';">2) <span>写文件的过程：</span></span></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;text-indent:28px;text-align:center;">
<span style="font-family:'微软雅黑';"><a href="https://s1.51cto.com/wyfs02/M01/8E/53/wKiom1i9KuuzYBs4AAJLZczgjOs382.png" rel="nofollow" style="color:rgb(1,95,145);text-decoration:none;"><img src="https://s1.51cto.com/wyfs02/M01/8E/53/wKiom1i9KuuzYBs4AAJLZczgjOs382.png" title="第四章4.png" alt="wKiom1i9KuuzYBs4AAJLZczgjOs382.png" style="vertical-align:top;border:none;"></a> </span></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;text-indent:28px;">
<span style="font-family:'微软雅黑';color:rgb(62,62,62);"><span>客户端调用</span>create()来创建文件<span>，</span>Distributed File System用RPC调用NameNode节点，在文件系统的命名空间中创建一个新的文件。NameNode节点首先确定文件原来不存在，并且客户端有创建文件的权限，然后创建新文件。</span></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;text-indent:28px;">
<span style="font-family:'微软雅黑';color:rgb(62,62,62);">Distributed File System返回DFSOutputStream，客户端用于写数据。客户端开始写入数据，DFSOutputStream将数据分成块，写入Data Queue。Data Queue由Data Streamer读取，并通知NameNode<span>节点分配数据节点，用来存储数据块</span>(每块默认复制3块)。分配的数据节点放在一个Pipeline里。Data
 Streamer将数据块写入Pipeline中的第一个数据节点。第一个数据节点将数据块发送给第二个数据节点。第二个数据节点将数据发送给第三个数据节点。</span></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;text-indent:28px;">
<span style="font-family:'微软雅黑';color:rgb(62,62,62);">DFSOutputStream为发出去的数据块保存了Ack Queue，等待Pipeline中的数据节点告知数据已经写入成功。</span></p>
<h3 style="color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;">
<span style="font-family:'宋体';font-size:21px;">6. </span><span><span style="font-size:21px;font-family:'宋体';">操作</span><span style="font-family:Calibri;font-size:21px;">HDFS<span style="font-family:'宋体';">的</span></span><span style="font-size:21px;font-family:'宋体';">基本</span><span style="font-size:21px;font-family:'宋体';">命令</span></span></h3>
<p style="margin-left:56px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'微软雅黑';color:rgb(62,62,62);">1) <span>打印文件列表（</span>ls）</span></p>
<table width="525" style="font-size:12px;border-collapse:collapse;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;"><tbody><tr><td width="525" valign="top" style="border:1px solid rgb(221,221,221);background:rgb(231,230,230);">
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">标准写法：</span></p>
<p style="margin-left:28px;clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">hadoop fs -ls hdfs:/#hdfs: 明确说明是HDFS系统路径</span></p>
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';"> </span></p>
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">简写：</span></p>
<p style="margin-left:28px;clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">hadoop fs -ls /#默认是HDFS系统下的根目录</span></p>
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';"> </span></p>
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">打印指定子目录：</span></p>
<p style="margin-left:28px;clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">hadoop fs -ls /package/test/#HDFS系统下某个目录</span></p>
</td>
</tr></tbody></table><p style="margin-left:56px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'微软雅黑';color:rgb(62,62,62);">2) <span>上传文件、目录（</span>put、copyFromLocal<span>）</span></span></p>
<p style="margin-left:56px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'微软雅黑';color:rgb(62,62,62);">put用法<span>：</span></span></p>
<table width="525" style="font-size:12px;border-collapse:collapse;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;"><tbody><tr><td width="525" valign="top" style="border:1px solid rgb(221,221,221);background:rgb(231,230,230);">
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">上传新文件：</span></p>
<p style="margin-left:28px;clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">hdfs fs -put file:/root/test.txt hdfs:/  #上传本地test.txt文件到HDFS根目录，HDFS根目录须无同名文件，否则“File exists”</span></p>
<p style="margin-left:28px;clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">hdfs fs -put test.txt /test2.txt  #上传并重命名文件。</span></p>
<p style="margin-left:28px;clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">hdfs fs -put test1.txt test2.txt hdfs:/  #一次上传多个文件到HDFS路径。</span></p>
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';"> </span></p>
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">上传文件夹：</span></p>
<p style="margin-left:28px;clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">hdfs fs -put mypkg /newpkg  #上传并重命名了文件夹。</span></p>
<p style="margin-left:28px;clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';"> </span></p>
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">覆盖上传：</span></p>
<p style="margin-left:28px;clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">hdfs fs -put -f /root/test.txt /  #如果HDFS目录中有同名文件会被覆盖</span></p>
</td>
</tr></tbody></table><p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;text-indent:28px;">
<span style="font-family:'微软雅黑';color:rgb(62,62,62);">copyFromLocal<span>用法：</span></span></p>
<table width="525" style="font-size:12px;border-collapse:collapse;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;"><tbody><tr><td width="525" valign="top" style="border:1px solid rgb(221,221,221);background:rgb(231,230,230);">
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">上传文件并重命名：</span></p>
<p style="margin-left:28px;clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">hadoop fs -copyFromLocal file:/test.txt hdfs:/test2.txt</span></p>
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';"> </span></p>
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">覆盖上传：</span></p>
<p style="margin-left:28px;clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">hadoop fs -copyFromLocal -f test.txt /test.txt</span></p>
</td>
</tr></tbody></table><p style="margin-left:56px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'微软雅黑';color:rgb(62,62,62);">3) <span>下载文件、目录（</span>get<span>、</span>copyToLocal<span>）</span></span></p>
<p style="margin-left:28px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;text-indent:28px;">
<span style="font-family:'微软雅黑';color:rgb(62,62,62);">get<span>用法：</span></span></p>
<table width="525" style="font-size:12px;border-collapse:collapse;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;"><tbody><tr><td width="525" valign="top" style="border:1px solid rgb(221,221,221);background:rgb(231,230,230);">
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">拷贝文件到本地目录：</span></p>
<p style="margin-left:28px;clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">hadoop fs -get hdfs:/test.txt file:/root/</span></p>
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';"> </span></p>
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">拷贝文件并重命名，可以简写：</span></p>
<p style="margin-left:28px;clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">hadoop fs -get /test.txt /root/test.txt</span></p>
</td>
</tr></tbody></table><p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;text-indent:28px;">
<span style="font-family:'微软雅黑';color:rgb(62,62,62);">copyToLocal<span>用法</span></span></p>
<table width="525" style="font-size:12px;border-collapse:collapse;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;"><tbody><tr><td width="525" valign="top" style="border:1px solid rgb(221,221,221);background:rgb(231,230,230);">
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">拷贝文件到本地目录：</span></p>
<p style="margin-left:28px;clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">hadoop fs -copyToLocal hdfs:/test.txt file:/root/</span></p>
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';"> </span></p>
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">拷贝文件并重命名，可以简写：</span></p>
<p style="margin-left:28px;clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">hadoop fs -copyToLocal /test.txt /root/test.txt</span></p>
</td>
</tr></tbody></table><p style="margin-left:56px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'微软雅黑';color:rgb(62,62,62);">4) <span>拷贝文件、目录（</span>cp<span>）</span></span></p>
<table width="525" style="font-size:12px;border-collapse:collapse;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;"><tbody><tr><td width="525" valign="top" style="border:1px solid rgb(221,221,221);background:rgb(231,230,230);">
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">从本地到HDFS，同put</span></p>
<p style="margin-left:28px;clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">hadoop fs -cp file:/test.txt hdfs:/test2.txt</span></p>
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';"> </span></p>
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">从HDFS到HDFS</span></p>
<p style="margin-left:28px;clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">hadoop fs -cp hdfs:/test.txt hdfs:/test2.txt</span></p>
<p style="margin-left:28px;clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">hadoop fs -cp /test.txt /test2.txt</span></p>
</td>
</tr></tbody></table><p style="margin-left:56px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'微软雅黑';color:rgb(62,62,62);">5) <span>移动文件（</span>mv<span>）</span></span></p>
<table width="525" style="font-size:12px;border-collapse:collapse;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;"><tbody><tr><td width="525" valign="top" style="border:1px solid rgb(221,221,221);background:rgb(231,230,230);">
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">hadoop fs -mv hdfs:/test.txt hdfs:/dir/test.txt</span></p>
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">hadoop fs -mv /test.txt /dir/test.txt</span></p>
</td>
</tr></tbody></table><p style="margin-left:56px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'微软雅黑';color:rgb(62,62,62);">6) <span>删除文件、目录（</span>rm<span>）</span></span></p>
<table width="525" style="font-size:12px;border-collapse:collapse;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;"><tbody><tr><td width="525" valign="top" style="border:1px solid rgb(221,221,221);background:rgb(231,230,230);">
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">删除指定文件</span></p>
<p style="margin-left:28px;clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">hadoop fs -rm /a.txt</span></p>
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';"> </span></p>
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">删除全部txt文件</span></p>
<p style="margin-left:28px;clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">hadoop fs -rm /*.txt</span></p>
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';"> </span></p>
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">递归删除全部文件和目录</span></p>
<p style="margin-left:28px;clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">hadoop fs -rm -R /dir/</span></p>
</td>
</tr></tbody></table><p style="margin-left:56px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'微软雅黑';color:rgb(62,62,62);">7) <span>读取文件（</span>cat、tail）</span></p>
<table width="525" style="font-size:12px;border-collapse:collapse;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;"><tbody><tr><td width="525" valign="top" style="border:1px solid rgb(221,221,221);background:rgb(231,230,230);">
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">hadoop fs -cat /test.txt  #以字节码的形式读取</span></p>
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">hadoop fs -tail /test.txt</span></p>
</td>
</tr></tbody></table><p style="margin-left:56px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'微软雅黑';color:rgb(62,62,62);">8) <span>创建空文件（</span>touchz<span>）</span></span></p>
<table width="525" style="font-size:12px;border-collapse:collapse;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;"><tbody><tr><td width="525" valign="top" style="border:1px solid rgb(221,221,221);background:rgb(231,230,230);">
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">hadoop fs - touchz /newfile.txt</span></p>
</td>
</tr></tbody></table><p style="margin-left:56px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'微软雅黑';color:rgb(62,62,62);">9) <span>创建文件夹（</span>mkdir<span>）</span></span></p>
<table width="525" style="font-size:12px;border-collapse:collapse;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;"><tbody><tr><td width="525" valign="top" style="border:1px solid rgb(221,221,221);background:rgb(231,230,230);">
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">hadoop fs -mkdir /newdir /newdir2#可以同时创建多个</span></p>
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">hadoop fs -mkdir -p /newpkg/newpkg2/newpkg3 #同时创建父级目录</span></p>
</td>
</tr></tbody></table><p style="margin-left:56px;clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'微软雅黑';color:rgb(62,62,62);">10) <span>获取逻辑空间文件、目录大小（</span>du<span>）</span></span></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<br></p>
<table width="525" style="font-size:12px;border-collapse:collapse;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;"><tbody><tr><td width="525" valign="top" style="border:1px solid rgb(221,221,221);background:rgb(231,230,230);">
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">hadoop fs - du /  #显示HDFS根目录中各文件和文件夹大小</span></p>
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">hadoop fs -du -h /  #以最大单位显示HDFS根目录中各文件和文件夹大小</span></p>
<p style="clear:both;overflow:hidden;">
<span style="font-family:'微软雅黑';">hadoop fs -du -s /  #仅显示HDFS根目录大小。即各文件和文件夹大小之和 </span></p>
</td>
</tr></tbody></table><p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<br></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
这时，你已经了解了HDFS和HDFS的操作命令，接下来我会继续介绍Mapreduce计算框架，Mapreduce在hadoop中又起到什么作用呢？</p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<br></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">点击链接 </span><a href="http://blog.csdn.net/ijia1" rel="nofollow" style="text-decoration:none;color:rgb(12,137,207);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;"><strong><span style="color:rgb(255,0,0);">http://blog.csdn.net/ijia1 </span></strong></a><span style="color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;"> 访问我的博客，学习更多hadoop知识。</span><br></p>
            </div>
                </div>