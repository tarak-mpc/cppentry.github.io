---
layout:     post
title:      HDFS scalability: the limits to growth 学习笔记
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 align="center"><br></h1>
<p>本文是《HDFS scalability: the limits to growth 》的学习笔记，勾画文章的主要观点。</p>
<p>原文引自：<a href="http://www.usenix.org/publications/login/2010-04/openpdfs/shvachko.pdf" rel="nofollow">http://www.usenix.org/publications/login/2010-04/openpdfs/shvachko.pdf</a></p>
<h2>HDFS 概览</h2>
<p>1.   HDFS是stand-alone的</p>
<p>分布式文件系统的树形结构信息(metadata)由NameNode存储，文件本身存放在众多的DataNode中。</p>
<p><span style="font-family:Simsun;font-size:16px;"></span></p>
<p style="margin-left:0px;font-family:Simsun;font-size:16px;">
</p>
<p style="display:inline !important;"><span style="font-family:Simsun;font-size:16px;"></span></p>
<p style="display:inline !important;"></p>
<p style="display:inline !important;"><span style="font-family:Simsun;font-size:16px;"><span style="font-family:Simsun;font-size:16px;">2.文件读写</span></span></p>
<span style="font-family:Simsun;font-size:16px;"></span>
<p>Client读写文件的流程是：Client向NameNode获取文件块的位置信息，然后与DataNode建立读/写文件流。</p>
<p>如下图所示：</p>
<p><img src="http://hi.csdn.net/attachment/201107/29/0_1311949351m5jo.gif" alt=""><br></p>
<p>3.命名空间和数据分离的主要目的是系统的可扩展性。元数据操作通常是非常快的，而文件的读写则通常需要较长时间。如果元数据操作和数据操作结合在一起，那么数据操作将占了觉大部分时间，容易造成系统瓶颈。元数据操作和数据操作分离开后，元数据的操作由命名空间服务器(单个)负责，数据操作由集群中的数据服务器完成，系统的吞吐量大大增加。</p>
<p>4．命名空间定义了整个文件系统的树形结构：包括目录和文件以及层次关系，也称为命名空间镜像，它被持久化到硬盘。其中一个重要的信息是：文件-&gt;blocklist的映射关系。Blocklist中并没有blocklocation的信息，只有blockID。这一结构在NN启动后被加载进内存。此外，对于命名空间的修改日志NN也保存，便于以后checkpoing。总之，镜像和日志操作都是会进行持久化，系统运行时被加载进内存。NN的内存还保存了:block-&gt;machinelist 及machine-&gt;block的信息，这些信息是根据datanode的心跳上报,NN不会将它们写入硬盘。</p>
<h2>命名空间的局限性</h2>
<p>首先，NN是单服务器结构。因此，NN的内存空间是HDFS的一个瓶颈。研究表明Namenode的一个metadata对象所需内存少于200字节。</p>
<h3>结论1</h3>
<p>假设：objSize表示medata对象的大小,是文件的平均block数；F是文件总数。那么，Namenode的内存大小至少为：</p>
<p>                  <img src="http://hi.csdn.net/attachment/201107/29/0_1311949984neEM.gif" alt=""></p>
<p>因此，如果存储100,000,000个文件，则有200,000,000个block，那么Namenode至少得有60GB的内存空间。</p>
<h2>副本</h2>
<p>副本即block，HDFS默认的副本数是3，副本技术提高了系统的可用性和可靠性。文件副本的大小就是文件的实际大小，它和操作系统上的文件没有什么区别，只是还多了一个对应的metadata文件，它存放了副本的校验和。</p>
<h2>HDFS存储容量VS 命名空间大小</h2>
<h3>结论2</h3>
<p>假设：blockSize是block的最大值，r是平均副本数，是文件的平均副本数，F是文件总数。那么，存储容量（SC）与命名空间的关系是：<span style="font-family:Simsun;font-size:16px;"></span></p>
<p style="margin-left:0px;font-family:Simsun;font-size:16px;display:inline !important;">
SC&lt;=F*r*blockSize</p>
<h3>法则1</h3>
<p>由结论1，2可得：</p>
<p>1GB metadata  ≈ 1PB的物理存储空间</p>
<h2>Block report与心跳</h2>
<p>      Namenode维持一个datanode-&gt;blocklist映射。</p>
<p>      Block report包括：blockID，length，generationstamp。</p>
<p>      Block report默认每一个小时发送一次。</p>
<p>      心跳：datanode每隔三秒给namenode发送心跳表示它还在线。如果，namenode10分钟内都没收到一个datanode的心跳就会认为该datanode离线了，并开始备份该datanode上的副本。心跳携带的信息有：磁盘容量、使用量以及数据传输量</p>
<p>      Namenode从不主动与datanode通信。Datanode的心跳后，Namenode返回指令给datanode。有以下指令：</p>
<p></p>
<ul><li><span style="font-family:Simsun;font-size:16px;"></span>
<p style="display:inline !important;"></p>
<span style="font-family:Simsun;font-size:16px;"></span>
<p style="display:inline !important;">复制block给其他节点</p>
<p style="font-family:Simsun;font-size:16px;"></p>
</li><li><span style="font-family:Simsun;font-size:16px;"></span>
<p style="display:inline !important;"></p>
<span style="font-family:Simsun;font-size:16px;"></span>
<p style="display:inline !important;">删除节点上的block</p>
<p style="font-family:Simsun;font-size:16px;"></p>
</li><li><span style="font-family:Simsun;font-size:16px;"></span>
<p style="display:inline !important;"></p>
<span style="font-family:Simsun;font-size:16px;"></span>
<p style="display:inline !important;">重新注册或关闭节点</p>
<p style="font-family:Simsun;font-size:16px;"></p>
</li><li><span style="font-family:Simsun;font-size:16px;"></span>
<p style="display:inline !important;"></p>
<span style="font-family:Simsun;font-size:16px;"></span>
<p style="display:inline !important;">发送紧急块报文</p>
</li></ul><p style="font-family:Simsun;font-size:16px;"></p>
<p style="display:inline !important;">注：心跳其实是Datanode通过RPC调用Namenode的heartbeat函数。</p>
<h2>内部负载(Internal load)</h2>
<p>内部负载由块报文（blockreport）和心跳构成，它取决于datanode的数量。对于10，000个datanode规模的集群，通过测试程序NNThroughputBenchmark模拟datanode的blockreport和heartbeat行为。测试程序运行在本地。</p>
<p><strong>结论4</strong>：</p>
<p>         对于有10,000个节点、容量为60PB的HDFS，Block report和heartbeat的内部负载，消耗了namenode30%的处理能力。</p>
<p>         此外，如下结论：</p>
<ul><li><span style="font-family:Simsun;font-size:16px;"></span>
<p style="display:inline !important;">Internalload取决于平均块报文的大小和次数，而心跳的影响非常小。</p>
</li><li><span style="font-family:Simsun;font-size:16px;"></span>
<p style="display:inline !important;">Internal load与集群节点数和节点的平均block数成正比关系。</p>
</li></ul><h2>合理的负载期望</h2>
<p>      Namenode的另外70%的处理能力用于应付外部客户端请求（externalclient request）。实测试了在10,000个节点，60PB容量的集群的负载期望及namenode是否能应付这样的负载。</p>
<p>首先，假设100,000个客户端运行不同的任务且只执行HDFS读操作。用map任务来模拟这一测试过程，map不产生数据即不写磁盘。读操作过程为：</p>
<p>1.   从namenod获取blocklocation</p>
<p>2.   从离client最近的节点读取数据</p>
<p>实验结论：</p>
<p>         10,000个节点的HDFS集群，namenode能应付30%的内部负载+100,000个客户端的只读操作。</p>
<p>对于只写操作的实验结论是：</p>
<p>         10,000个节点的HDFS集群，namenode不能完成100,000个HDFS客户端的只写操作。</p>
<h2>结尾</h2>
<p>         作者总结，对于一万个节点的集群+一个主节点(namenode)可以完成十万个客户端的读操作负载，但是，一万个写客户端就使得主节点疲于应付。这一差异主要是写操作对于namenode来说需要写日志，而读操作只是内存操作。</p>
<p>         改进单一namenode性能的方法很多，但是，任何针对单一namenode的性能优化都是缺乏可扩展性的。</p>
<p>         将来，尤其是随着小文件比例的增长，绝大多数的理想的解决方法似乎都是基于分布式的命名服务器自身的工作负载均衡和减少单个命名服务器的内存需求。目前，只有少数的文件系统采用并实现这一方法。</p>
            </div>
                </div>