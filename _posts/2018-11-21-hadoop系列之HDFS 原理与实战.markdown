---
layout:     post
title:      hadoop系列之HDFS 原理与实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><strong><strong><strong>HDFS</strong></strong><strong> </strong><strong><strong>文件系统</strong></strong></strong></p>

<ol><li><strong><strong><strong><a name="_bookmark32"></a>HDFS</strong></strong><strong> </strong><strong><strong>简介</strong></strong></strong></li>
</ol><p style="margin-left:6.6pt;">HDFS 是 Hadoop Distributed File System 的简称，即 Hadoop 分布式文件系统。它起源于谷歌发表的 GFS 论文， 是该论文的开源实现，也是整个大数据的基础。</p>

<p style="margin-left:6.6pt;">HDFS 专门为解决大数据的存储问题而产生的，具有如下特点：</p>

<p>      可存储超大文件：HDFS 可存储 PB 级的数据</p>

<p>      流式数据访问：一次写入，多次读取。数据集通常从数据源复制而来，每次分析都涉及该数据集的</p>

<p>     商用硬件：Hadoop 不需要运行在高昂可靠的硬件上<span style="color:#0000ff;">.</span>，它是设计运行在普通的商用硬件上，节点的故障不会影响到系统的运行。</p>

<p style="margin-left:6.6pt;"> </p>

<p><strong><strong><strong>不适用的场景：</strong></strong></strong></p>

<p>  1.低延时的数据访问：HDFS 是为高数据吞吐量而设计的，这会以提高时间延迟为代价。因此，对于低延时的访问需求不适合在 HDFS 上。</p>

<p>2.大量的小文件：由于 namenode 将文件系统的元数据存储在内存中，因此该文件系统所能存储的文件总数受限于 namenode 的内存容量。</p>

<p>3.多用户写入，随意修改文件：HDFS 中的文件只支持单个写入者，而且不支持在任意位置进行修改。</p>

<p style="margin-left:6.6pt;"> </p>

<p><strong><strong><strong><a name="_bookmark33"></a>HDFS</strong></strong><strong> </strong><strong><strong>文件块</strong></strong></strong></p>

<p style="margin-left:6.6pt;">每个磁盘有默认的数据块，这是磁盘读写的最小单位。文件系统的块大小一般为磁盘块的整数倍。磁盘块大小一般为 512 字节。</p>

<p style="margin-left:6.6pt;">HDFS 文件系统也有块(block)的概念，默认值为 128M，也可以修改。超过块大小的文件在存储的时候会被分为多个分块(chunk)。有点需要注意：HDFS 中小于一个块大小的文件不会占据整个块的空间，而单一磁盘中如果文件小于块大小，那么整个磁盘块的空间都会被占用。</p>

<p style="margin-left:6.6pt;"> </p>

<p style="margin-left:6.6pt;">对文件进行分块存储有很多好处，比如：</p>

<p>1.文件的大小可以不受限于单个磁盘的容量；</p>

<p>2.文件的所有块不必存储在同一个磁盘上，可以利用集群上的任意一个磁盘进行存储；</p>

<p>3.将文件分块存储便于备份和容错，提高可用性。</p>

<p style="margin-left:6.6pt;"> </p>

<p style="margin-left:6.6pt;">HDFS 的文件块信息可以通过 fsck 命令查看，比如：</p>

<p style="margin-left:6.6pt;">hdfs fsck / -files -blocks</p>

<p style="margin-left:6.6pt;"> </p>

<p style="margin-left:6.6pt;">另外，对于访问频繁的文件，其对应的块可能会被显示地缓存在内存中。</p>

<p> </p>

<p><strong><a name="_bookmark34"></a> <strong><strong>HDFS</strong></strong><strong> </strong><strong><strong>节点</strong></strong></strong></p>

<p><strong><strong>namenode</strong></strong></p>

<p style="margin-left:6.6pt;">namenode 维护 HDFS 文件系统的元数据。所谓元数据，就是数据的数据。即存储在 HDFS 文件系统上的数据的基本信息，比如文件系统树及整棵树内所有的文件和目录，每个文件中各个数据块所在数据节点信息，但并不永久保存块的位置信息，因为这块可能随时变化。这部分的信息在系统启动的时候根据数据节点的信息重 建。</p>

<p><strong><strong><strong>datanode</strong></strong></strong></p>

<p style="margin-left:6.6pt;">datanode 是 HDFS 文件系统存储具体数据的节点。它们根据 namenode 节点的调度来存储和检索文件的数据块。由于文件的元数据都存储在 namenode，如果没有 namenode，文件系统将无法工作，如果 namenode 的数据损坏，那么存储在 datanode 上的数据就作废了。</p>

<p style="margin-left:6.6pt;">因此，对 namenode 的容错显得非常重要。</p>

<p style="margin-left:6.6pt;">通常 来说，可以通过 NFS 或者 HA 的方式来确保 HDFS 的高可用。</p>

<p><strong><strong><strong><a name="_bookmark35"></a>HDFS HA</strong></strong><strong> </strong><strong><strong>高可用集群</strong></strong></strong></p>

<p><strong><strong>HDFS</strong></strong><strong> </strong><strong><strong>HA</strong></strong><strong> </strong><strong><strong>概述</strong></strong></p>

<p style="margin-left:6.6pt;">在完全分布式 HDFS 集群中，即使通过 namenode 备份还是无法做到及时的文件系统高可用，namenode 存在单点故障（SPOF，Single Point Of Failure）。</p>

<p style="margin-left:6.6pt;">对于一个大型集群来说，冷启动的时间乐观情况下一般都需要半个小时以上，甚至更多。因此，Hadoop2 增加了 HDFS 的高可用 HA（High Availability）的支持。</p>

<p style="margin-left:6.6pt;">HDFS HA 有两种方案，NFS 和 QJM。这里推荐使用 QJM</p>

<p style="margin-left:6.6pt;">为了保障 HDFS 集群的高可用，需要实现主备(active-standby) namenode。</p>

<p><strong><strong>HDFS</strong></strong><strong> </strong><strong><strong>HA</strong></strong><strong> </strong><strong><strong>架构</strong></strong>下面是 HDFS HA 的架构</p>

<p style="margin-left:6.6pt;"><img alt="" class="has" src="https://img-blog.csdn.net/2018100915510484?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0xVRkFOWkhFTkc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p> </p>

<p style="margin-left:6.6pt;">HDFS HA 架构图</p>

<p><strong><strong><strong>基于 </strong></strong><strong><strong>QJM</strong></strong><strong> </strong><strong><strong>的 </strong></strong><strong><strong>HDFS</strong></strong><strong> </strong><strong><strong>HA</strong></strong><strong> </strong><strong><strong>实现</strong></strong></strong></p>

<p style="margin-left:6.6pt;">QJM，Quorum Journal Manager，也就是群里日志管理器。它是一个专用的 HDFS 实现，为提供一个高可用编辑日志而设计，应用于大多数的 HDFS 部署。QJM 包含一组日志节点（Journal Node），NameNode 每次写入的时候必须写入多数的日志节点。</p>

<p style="margin-left:6.6pt;">典型的情况，有三个日志节点，系统能够容忍任何一个节点的失效。NameNode 就是通过一组 JournalNodes 的独立进程进行相互通信，来实现数据的同步。</p>

<p style="margin-left:6.6pt;">当 Active 状态的 NameNode 的命名空间有任何修改时，会通知大部分的 JournalNodes 进程。Standby 状态的</p>

<p style="margin-left:6.6pt;">NameNode 会读取 JournalNodes 中的变更信息，并且一直监控 editlog 的变化，把变化应用于自己的命名空间。</p>

<p style="margin-left:6.6pt;">Standby 节点可以确保在集群出错时，命名空间状态已经完全同步了。</p>

<p><strong><a name="_bookmark38"></a> <strong><strong>HDFS</strong></strong><strong> </strong><strong><strong>的文件容错测试</strong></strong></strong></p>

<p style="margin-left:6.6pt;">测试目的：测试 HDFS 在节点发生故障时，是否会发生数据丢失的情况。</p>

<p style="margin-left:6.6pt;">第一步：准备一个简单的文本文件</p>

<p style="margin-left:6.6pt;">vi hdfsha.txt</p>

<p style="margin-left:6.6pt;">This is a test file for hdfs.replication. hdfs dfs -mkdir /user/test</p>

<p style="margin-left:6.6pt;">第二步：上传该文件到 HDSF 文件系统[root@hadoop01 ~]# hdfs dfs -put hdfsha.txt /user/test/ [root@hadoop01 ~]# hdfs dfs -cat /user/test/hdfsha.txt</p>

<p style="margin-left:6.6pt;">第三步：关闭任意两个 DataNode 节点，然后读取该文件先关闭一台，测试</p>

<p style="margin-left:6.6pt;">[root@hadoop01 ~]# hadoop-daemon.sh stop datanode [root@hadoop01 ~]# hdfs dfs -cat /user/test/hdfsha.txt</p>

<p style="margin-left:6.6pt;"> </p>

<p><strong><strong><strong>关键日志信息：</strong></strong></strong></p>

<p style="margin-left:6.6pt;">WARN hdfs.DFSClient: Failed to connect to /192.168.159.206:50010 for block, add to deadNodes and continue.</p>

<p style="margin-left:6.6pt;">INFO hdfs.DFSClient: Successfully connected to /192.168.159.208:50010</p>

<p style="margin-left:6.6pt;">结果：成功从第三台机器获取到文件。</p>

<p style="margin-left:6.6pt;">先关闭第二台（选上次读取成功的那台），测试[root@hadoop03 ~]# hadoop-daemon.sh stop datanode [root@hadoop01 ~]# hdfs dfs -cat /user/test/hdfsha.txt</p>

<p style="margin-left:6.6pt;"> </p>

<p><strong><strong><strong>关键日志信息：</strong></strong></strong></p>

<p style="margin-left:6.6pt;">WARN hdfs.DFSClient: Failed to connect to /192.168.159.206:50010 for block, add to deadNodes and continue. WARN hdfs.DFSClient: Failed to connect to /192.168.159.208:50010 for block</p>

<p style="margin-left:6.6pt;">INFO hdfs.DFSClient: Successfully connected to /192.168.159.207:50010</p>

<p style="margin-left:6.6pt;">结果：成功从第二台机器获取到文件。第四步：关闭第三台</p>

<p style="margin-left:6.6pt;">[root@hadoop02 ~]# hadoop-daemon.sh stop datanode [root@hadoop01 ~]# hdfs dfs -cat /user/test/hdfsha.txt 彻底 Over，无法成功获取文件。</p>

<p> </p>

<ol><li>
	<ol><li><strong><strong><strong><a name="_bookmark39"></a>HDFS</strong></strong><strong> </strong><strong><strong>命令行基本操作</strong></strong></strong></li>
	</ol></li>
</ol><p style="margin-left:6.6pt;">通过“hdfs -help”和“hdfs dfs -help”查看帮助</p>

<p style="margin-left:6.6pt;"> </p>

<p style="margin-left:6.6pt;">HDFS 的文件操作命令跟 Linux 的操作命令大多类似。</p>

<p style="margin-left:6.6pt;"> </p>

<p><strong><a name="_bookmark40"></a> <strong><strong>创建目录</strong></strong></strong></p>

<p style="margin-left:6.6pt;">[root@hadoop01 ~]# hdfs dfs -mkdir -p /user/$USER</p>

<p style="margin-left:6.6pt;">当前为 root 用户，因此会创建/user/root 目录。</p>

<p style="margin-left:6.6pt;"><strong>查看刚创建的目录</strong></p>

<p style="margin-left:6.6pt;">[root@hadoop01 ~]# hdfs dfs -ls /user</p>

<p><strong><a name="_bookmark41"></a>上传文件</strong></p>

<p style="margin-left:6.6pt;">[root@hadoop01 ~]# echo "Test file content" &gt; testfile</p>

<p style="margin-left:6.6pt;">[root@hadoop01 ~]# hdfs dfs -put testfile /user/root/</p>

<p style="margin-left:6.6pt;"> </p>

<p style="margin-left:6.6pt;">查看刚复制到 hdfs 的 文件</p>

<p style="margin-left:6.6pt;">[root@hadoop01 ~]# hdfs dfs -ls /user/root/</p>

<p><strong><strong><strong><a name="_bookmark42"></a>读取文件</strong></strong></strong></p>

<p>直接展示</p>

<p style="margin-left:6.6pt;">[root@hadoop01 ~]# hdfs dfs -cat /user/root/testfile</p>

<p><strong>获取到本地文件系统</strong></p>

<p style="margin-left:6.6pt;">[root@hadoop01 ~]# hdfs dfs -get /user/root/testfile</p>

<p><strong><strong><strong><a name="_bookmark43"></a>删除文件</strong></strong></strong></p>

<p>删除单个文件</p>

<p style="margin-left:6.6pt;">[root@hadoop01 ~]# hdfs dfs -rm /user/root/testfile</p>

<p>删除目录</p>

<p style="margin-left:6.6pt;">[root@hadoop01 ~]# hdfs dfs -rm -r /user/root</p>

<p><strong><a name="_bookmark44"></a> <strong><strong>HDFS</strong></strong><strong> </strong><strong><strong>与 </strong></strong><strong><strong>Hadoop</strong></strong><strong> </strong><strong><strong>命令</strong></strong></strong></p>

<p style="margin-left:6.6pt;">Hadoop 支持的文件系统包括：本地文件系统 Local FileSystem、HDFS、WebHDFS、Secure WebHDFS、</p>

<p style="margin-left:6.6pt;">HAR、View、FTP、S3、Azure 和 Swift。</p>

<p style="margin-left:6.6pt;">hadoop fs 面向是是一个抽象的文件系统，支持多种文件系统的实现。通过 uri 前缀来识别。</p>

<p style="margin-left:6.6pt;">hadoop dfs 已经被弃用(deprecated)，执行的时候会被替换为 hdfs dfs 命令执行。</p>

<p style="margin-left:6.6pt;">hdfs dfs 专门针对 HDFS 文件系统的操作。</p>

<p><strong><a name="_bookmark45"></a> <strong><strong>DistCp</strong></strong><strong> </strong><strong><strong>跨集群拷贝数据</strong></strong></strong></p>

<p style="margin-left:6.6pt;">DistCp 是 Distributed Copy 的缩写，即分布式拷贝，它是一个集群中间数据拷贝的工具。</p>

<p style="margin-left:6.6pt;">它使用 Map/Reduce 实现文件分发，错误处理和恢复，以及报告生成。 它把文件和目录的列表作为 map 任务的输入，每个任务会完成源列表中部分文件的拷贝。 由于使用了 Map/Reduce 方法，这个工具在语义和执行上都会有特殊的地方。</p>

<p style="margin-left:6.6pt;">一般格式：</p>

<p style="margin-left:6.6pt;">hadoop distcp hdfs://nn1:8020/foo/bar hdfs://nn2:8020/bar/foo</p>

<p style="margin-left:6.6pt;"> </p>

<p style="margin-left:6.6pt;">详细请参考：<a href="https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html" rel="nofollow"><u><span style="color:#0000ff;"><u>https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html</u></span></u></a></p>

<p><strong><strong><strong><a name="_bookmark46"></a>HDFS</strong></strong><strong> </strong><strong><strong>压缩与分片</strong></strong></strong></p>

<p><strong><strong><strong><a name="_bookmark47"></a>HDFS</strong></strong><strong> </strong><strong><strong>压缩</strong></strong></strong></p>

<p style="margin-left:6.6pt;">文件压缩有两大好处：</p>

<ol><li>减少存储文件所需要的磁盘空间，节约硬件成本;</li>
	<li>减少读取的时间和在网络上传输的带宽需求。</li>
</ol><p style="margin-left:6.6pt;">由于大数据的场景下，数据量比较大，高效率的压缩就显得更为重要。Hadoop 支持的压缩格式包括：</p>

<p style="margin-left:6.6pt;">选则压缩方法的时候，还需要考虑压缩方法的时间效率和空间效率，也就是压缩速度的快慢与压缩的比率。<strong><strong>注：</strong></strong>LZO 可以通过建索引的方式来支持切分。</p>

<p><strong><strong><strong><a name="_bookmark48"></a>HDFS</strong></strong><strong> </strong><strong><strong>分片</strong></strong></strong></p>

<p style="margin-left:6.6pt;">MapReduce 在处理数据的时候，如果数据经过了压缩，那么了解这些压缩格式是否支持切分(splitting)是很重要的。</p>

<p style="margin-left:6.6pt;">比如，一个压缩完之后为 1G 的文件，根据 128M 的默认块大小，文件会被存储为 8 个块。如果该压缩格式支持切分，那么 MapReduce 就可以将每个数据单独作为一个分片让 map 来工作，这样就可以有 8 个 map 任务同时运行。但是如果该压缩格式不支持切分，那么单个数据块就无法让 map 支持工作，它需要将 8 个数据块都读到一起才能工作。这样有两个缺点：</p>

<p>8 个数据块通常存储在不同的 DataNode 节点上，读取到一起会牺牲数据的本地性，减缓处理速度;</p>

<p>map 任务数减少了，作业的粒度变大，整体运行的时间变长。</p>

<p><strong><a name="_bookmark49"></a> <strong><strong>HDFS</strong></strong><strong> </strong><strong><strong>文件格式</strong></strong></strong></p>

<p><strong><strong><strong><a name="_bookmark50"></a>文件格式概述</strong></strong></strong></p>

<p style="margin-left:6.6pt;">HDFS 支持多种文件格式，如：Text Files、CSV Files、JSON Records、Sequence Files，Avro Files、Parquet</p>

<p style="margin-left:6.6pt;">Files、RC Files、ORC Files 等。</p>

<p style="margin-left:6.6pt;"> </p>

<p style="margin-left:6.6pt;">文件格式根据内容的组织形式可分为两类：</p>

<p>1.面向行 Sequence Files，Avro Files</p>

<p>2.面向列 Parquet Files、RC Files、ORC Files</p>

<p style="margin-left:6.6pt;">注：Flume 采用面向行的存储格式。</p>

<p><strong><strong><strong><a name="_bookmark51"></a>常见格式</strong></strong></strong></p>

<p><strong><strong>Sequence/Map</strong></strong><strong> </strong><strong><strong>Files</strong></strong></p>

<p style="margin-left:6.6pt;">Sequence 文件就是顺序文件，由文件头和随后的一条或多条记录组成。</p>

<p style="margin-left:6.6pt;">Sequence 文件格式压缩前后的内部结构下面是采用块压缩的结构，块压缩就是一次性压缩多条记录。</p>

<p style="margin-left:6.6pt;">采用块压缩的 Sequence 文件格式内部结构</p>

<p style="margin-left:6.6pt;">Map Files 就是排过序的 Sequence Files，有索引，可以按键查找。</p>

<p><strong><strong><strong>Avro</strong></strong></strong></p>

<p style="margin-left:6.6pt;">Apache Avro 是一个编程语言无关的数据序列化系统。创建它的目的是为了解决 Hadoop 中 Writable 类型缺乏语言移植性的不足。</p>

<p style="margin-left:6.6pt;">Avro 数据文件支持压缩，并且是可切分的，这有利于 MapReduce 任务的执行。</p>

<p><strong><strong><strong>RC/ORC</strong></strong><strong> </strong><strong><strong>Files</strong></strong></strong></p>

<p style="margin-left:6.6pt;">RC 是 Record Columnar 的缩写，是 Hadoop 中第一个面向列的文件格式，目前已经被 Hive 的 ORCFile</p>

<p style="margin-left:6.6pt;">（Optimized Record Columnar File）及 Parquet 取代了。</p>

<p><strong><strong><strong>Parquet</strong></strong></strong></p>

<p style="margin-left:6.6pt;">Apache Parquet 是一种能够有效存储嵌套数据的列式存储格式。列式存储的优势： 类型相同，压缩率更高，占用空间就更少</p>

<p style="margin-left:6.6pt;"> </p>

<p style="margin-left:6.6pt;">Parquet 源自于 Google 发表的一篇关于 Dremel 的论文，它以扁平的列式存储格式和很小的额外开销来存储嵌套的结构，从而带来 了性能上的很大提升。</p>

<p style="margin-left:0pt;"> </p>            </div>
                </div>