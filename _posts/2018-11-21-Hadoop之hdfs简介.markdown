---
layout:     post
title:      Hadoop之hdfs简介
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2><a id="Hadoophdfs_0"></a><strong>Hadoop的hdfs简介</strong></h2>
<p><strong>hadoop2x三大核心</strong>：hdfs、yarn、mapreduce.<br>
<strong>hadoop四大模块：</strong><br>
Hadoop Common:<br>
为其他Hadoop模块提供基础设施。<br>
Hadoop HDFS:<br>
一个高可靠、高吞吐量的分布式文件存储系统<br>
Hadoop MapReduce:<br>
一个分布式的离线并行计算框架<br>
Hadoop YARN:<br>
一个新的MapReduce框架，任务调度与资源管理框架</p>
<h2><a id="Hadoop_Distributed_File_System_12"></a>Hadoop Distributed File System</h2>
<p>1.易于扩展的分布式文件系统。<br>
2.运行在大量廉价机器上，提供容错机制。<br>
3.为大量用户提供不错的文件存取服务。<br>
<strong>HDFS设计目标</strong><br>
1.存储最大（pb级别）<br>
2.自动快速检测硬件错误。（成千上万的计算很容易出错，需要快速调整回复）<br>
3.流式访问数据（存取量特别大，只能用流式）<br>
4.移动计算比移动数据本身更划算。（比如a b c三台机子 a b 上有1个gb或者更大的数据，c机子上<br>
有计算程序，所以只能通过把c上程序拷贝到a b 上更划算）<br>
5.简单一致性模型。（一次性写入，多次性读入）<br>
6.异构平台可移植。（可以在其他平台部署可以与其他框架融合）。</p>
<p><strong>HDFS的特点：</strong><br>
优点：<br>
（一）高可靠性：hadoop一般都在成千的计算机集群之上，且可以搭建hadoop的高可靠集群，及内部容错功能优秀；<br>
（二）高扩展性：hadoop是在可用的计算机集簇间分配数据并完成计算任务的，这些集簇可以方便扩展到数以千计的节点中。<br>
（三）高效性：Hadoop集群能够在节点之间动态移动数据，并保证各个节点的动态平衡，因此处理速度非常快。<br>
（四）高容错性：hadoop能够自动保存数据的多个副本，并且能够自动将失败的任务重新分配。<br>
缺点：<br>
（-）不适合低延迟数据访问。<br>
（二）无法高效存储大量小文件。（底层有索引有很多元数据，占据空间极易浪费资源）<br>
（三）不支持用户写入及任意修改文件。</p>
<h2><a id="HDFS_37"></a>HDFS三个服务：</h2>
<p>1.namenode metadata 元数据<br>
2.secondarynamenode<br>
3.datanode<br>
元数据：文件大小 、文件名称、所属人、地址 128k<br>
元数据包括镜像文件（Fsimage详细文件压缩)+日志文件（Edits用户操作hdfs文件的记录)<br>
<strong>1.NameNode</strong><br>
namenode是一个中心服务器，单一节点，负责管理文件系统的名字空间（namespace)以及客户端对文件的访问。<br>
文件操作，namenode 负责文件元数据的操作，Datanode负责处理文件内容的读写请求，跟文件内容相关的数据流不经过namenode,只会询问它那个Datanode联系，否则namenode会成为系统的瓶颈。<br>
副本存放在哪些Datenode上由namenode来控制，根据全局情况做出块放置决定，读取文件时namenode尽量让用户读取最近的副本，降低带块消耗和读取延时。<br>
namenode全权管理数据块的复制，它周期性的从集群中每个Datanode接受心跳信号和块状（blockreport）。接受心跳信号意味着该Datanode节点工作正常。块状态报告包含了一个该Datanode上所有数据块列表。<br>
<strong>2.DataNode</strong><br>
一个数据块在Datanode以文件存储在磁盘上，包括两个五年级，一个是数据本身，一个是元数据包括数据块的		      长度，块数据的校验和，以及时间戳。<br>
Datanode 启动后向namenode注册，通过后，周期性（1小时）的向namenode上包所有的块信息。<br>
心跳是每三秒一次，心跳返回结果带有namenode给该Datanode的命令如果复制块数据到另一台机器，或删除某个数据块，如果超过十分钟没有收到某个Datanode的心跳，认为该节点不可用。<br>
集群运行可以安全加入和退出一些机器<br>
<strong>3. SecondaryNameNode</strong></p>
<p>NameNode元数据信息存储在FsImage中，NameNode每次重启后会把FsImage读取到内存中，在运行过程中为了防止数据丢失，NameNode的操作会被不断的写入本地EditLog文件中。<br>
当检查点被触发，FsImage会把EditLog文件中的操作应用一遍，然后把新版的FsImage写回磁盘中，删除EditLog文件中旧的事务信息。检查点有两种触发机制：<br>
（1）按秒为单位的时间间隔触发（dfs.namenode.checkpoint.period）；<br>
（2）达到文件系统累加的事务值触发（dfs.namenode.checkpoint.txns）。</p>
<p><img src="https://img-blog.csdnimg.cn/20181112141600288.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4MjU5NTU3,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<p>HDFS具有主从架构。HDFS集群由单个NameNode组成，这是一个主服务器，用于管理文件系统命名空间并调节客户端对文件的访问。此外，还有许多DataNodes，通常是集群中的每个节点，它们管理连接到运行它们的节点的存储。HDFS公开文件系统命名空间，并允许将用户数据存储在文件中。在内部，一个文件被分割成一个或多个块，这些块存储在一组DataNodes中。NameNode执行文件系统命名空间操作，比如打开、关闭和重命名文件和目录。它还确定块到DataNodes的映射。DataNodes负责服务来自文件系统客户端的读写请求。DataNodes还对NameNode的指令执行块创建、删除和复制。</p>
<blockquote>
<p>Rack1和Rack2是2个不同的机架，1上面有三台Datanode,2上面有2台Datanode。client是客户端如这里的[hadoop@master bin]$ hdfs dfs -ls / 就是一个客户端还有java端访问hdfs也是一个客户端能够实现对hdfs的操作都是client。这里只有一个NameNode后面用户也可以根据zookeeper实现hdfs高可用配置两台nNameNode。读取操作时client先去访问NameNode,NameNode分析请求并给予相应的地址，然后client再去与该相应的Datanode联系。</p>
</blockquote>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>