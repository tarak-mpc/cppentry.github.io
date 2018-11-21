---
layout:     post
title:      HADOOP之HDFS
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">转载：http://www.wangluqing.com/2014/02/27/hadoop-hdfs/</span></p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">摘要：</span>HDFS是Hadoop的核心模块之一，围绕HDFS是什么、HDFS的设计思想和HDFS的体系结构三方面来介绍。</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">关键词：Hadoop  HDFS   分布式存储系统<span id="more-471" style="border:0px;font-family:inherit;font-style:inherit;font-weight:inherit;vertical-align:baseline;"></span></span></p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
HDFS是Hadoop的核心模块之一，HDFS是什么？它是Hadoop分布式文件系统（Hadoop Distributed File System），摘录Apache 组织关于HDFS的介绍“<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">The Hadoop Distributed
 File System (<acronym title="Hadoop Distributed File System" style="border:0px;font-family:inherit;font-style:inherit;font-weight:inherit;vertical-align:baseline;">HDFS</acronym>) is a distributed file system designed
 to run on commodity hardware. It has many similarities with existing distributed file systems. However, the differences from other distributed file systems are significant. HDFS is highly fault-tolerant and is designed to be deployed on low-cost hardware.
 HDFS provides high throughput access to application data and is suitable for applications that have large data sets. HDFS relaxes a few POSIX requirements to enable streaming access to file system data. HDFS was originally built as infrastructure for the Apache
 Nutch web search engine project.</span> ”</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
Hadoop的设计思想受到Google公司的GFS设计思想的启示，基于一种开源的理念实现的分布式分布式文件系统。HDFS的设计基础与目标如下。</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
1）<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">硬件错误（Hardware Failure）</span>是常态，因而需要数据冗余技术。</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
2）<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">流失数据访问（Streaming Data Access）</span>，即数据批量读取而非随机读写，Hadoop擅长做数据分析而不是事务处理。</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
3）<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">大规模数据集（Large Data Sets）</span>。</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
4）<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">简单一致性模型（Simple Coherency Model）</span>，即为了降低系统复杂度，对文件采用一次性写多次读的逻辑设计，也就是文件一经写入，关闭，就再不要修改。</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
5）<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">“Moving Computation is Cheaper than Moving Data”</span>，通俗理解，程序采用“数据就近”原则分配节点执行。</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
6）<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">Portability Across Heterogeneous Hardware and Software Platforms</span>，即有着很强的可扩展性。</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
<a href="http://www.wangluqing.com/wp-content/uploads/2014/02/hdfsarchitecture.gif" rel="nofollow" style="border:0px;font-family:inherit;font-style:inherit;font-weight:inherit;vertical-align:baseline;color:rgb(36,137,13);"><img class="alignnone size-medium wp-image-525" alt="hdfsarchitecture" src="http://www.wangluqing.com/wp-content/uploads/2014/02/hdfsarchitecture-300x207.gif" width="300" height="207" style="border:0px;vertical-align:middle;"></a></p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
 </p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">图1 HDFS体系结构</span></p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
HDFS体系结构如图1所示，它采用主从结构，Namenode属于主段，Datanode属于从端。</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">Namenode</span></p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">1）管理文件系统的命名空间。</span></p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">2）记录 每个文件数据快在各个Datanode上的位置和副本信息。</span></p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">3）协调客户端对文件的访问。</span></p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">4）记录命名空间内的改动或者空间本省属性的改动。</span></p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">5）Namenode 使用事务日志记录HDFS元数据的变化。使用映像文件存储文件系统的命名空间，包括文件映射，文件属性等。</span></p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
从社会学来看，Namenode是HDFS里面的管理者，发挥者管理、协调、操控的作用。</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">Datanode</span></p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">1）负责所在物理节点的存储管理。</span></p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">2）一次写入，多次读取（不修改）。</span></p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">3）文件由数据库组成，一般情况下，数据块的大小为64MB。</span></p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">4）数据尽量散步到各个节点。</span></p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
从社会学的角度来看，Datanode是HDFS的工作者，发挥按着Namenode的命令干活，并且把干活的进展和问题反馈到Namenode的作用。</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
客户端如何访问HDFS中一个文件呢？具体流程如下。</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">1）首先从Namenode获得组成这个文件的数据块位置列表。</span></p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">2）接下来根据位置列表知道存储数据块的Datanode。</span></p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">3）最后访问Datanode获取数据。</span></p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
注意：Namenode并不参与数据实际传输。</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
数据存储系统，数据存储的可靠性至关重要。HDFS是如何保证其可靠性呢？它主要采用如下机理。</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
1）<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">冗余副本策略</span>，即所有数据都有副本，副本的数目可以在hdfs-site.xml中设置相应的复制因子。</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
2）<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">机架策略</span>，即HDFS的“机架感知”，一般在本机架存放一个副本，在其它机架再存放别的副本，这样可以防止机架失效时丢失数据，也可以提供带宽利用率。</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
3）<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">心跳机制</span>，即Namenode周期性从Datanode接受心跳信号和快报告，没有按时发送心跳的Datanode会被标记为宕机，不会再给任何I/O请求，若是Datanode失效造成副本数量下降，并且低于预先设置的阈值，Namenode会检测出这些数据块，并在合适的时机进行重新复制。</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
4）<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">安全模式</span>，Namenode启动时会先经过一个“安全模式”阶段。</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
5）<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">校验和</span>，客户端获取数据通过检查校验和，发现数据块是否损坏，从而确定是否要读取副本。</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
6）<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">回收站</span>，删除文件，会先到回收站/trash，其里面文件可以快速回复。</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
7）<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">元数据保护</span>，映像文件和事务日志是Namenode的核心数据，可以配置为拥有多个副本。</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
8）<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">快照</span>，支持存储某个时间点的映像，需要时可以使数据重返这个时间点的状态。</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
附录:HDFS 文件操作</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
对HDFS文件操作有两种方式，一是命令行方式，二是HDFS API方式。</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">HDFS常用命令归总</span></p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
1）列出HDFS下的文件</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">hadoop fs -ls</span></p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
2）上传本地文件到HDFS</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">hadoop fs -put  dir1  dir2</span></p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
说明：dir1，本地文件系统文件目录；dir2，HDFS文件系统文件目录</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
3）下载HDFS文件到本地</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">hadoop fs -get dir1  dir2</span></p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
说明：dir1，本地文件系统文件目录；dir2，HDFS文件系统文件目录</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
4）创建HDFS文件</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">hadoop fs -mkdir  dir</span></p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
5）删除HDFS下的文档</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">hadoop fs -rmr dir</span></p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
6）查看HDFS下某个文件的内容</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">hadoop fs -cat   文件路径</span></p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
7）查阅帮助</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
获取所用Hadoop版本关于HDFS完整的命令列表，可执行</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">  hadoop fs</span></p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
或者</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
使用help来显示某个命令的用法与简短描述。</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
例如 要了解ls，可执行</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">   hadoop fs -help ls</span></p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
 </p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
<span style="border:0px;font-family:inherit;font-style:inherit;font-weight:700;vertical-align:baseline;">Source：</span></p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
1 <a href="http://hadoop.apache.org/docs/r1.2.1/hdfs_design.html" rel="nofollow" style="border:0px;font-family:inherit;font-style:inherit;font-weight:inherit;vertical-align:baseline;color:rgb(36,137,13);">http://hadoop.apache.org/docs/r1.2.1/hdfs_design.html</a></p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
2 <a href="http://www.dataguru.cn/" rel="nofollow" style="border:0px;font-family:inherit;font-style:inherit;font-weight:inherit;vertical-align:baseline;color:rgb(36,137,13);">炼数成金</a>《Hadoop数据分析平台》第三课分布式文件系统HDFS</p>
<p style="border:0px;font-family:Lato, sans-serif;font-size:16px;vertical-align:baseline;color:rgb(43,43,43);line-height:24px;">
3 <a href="http://blog.csdn.net/netcoder/article/details/7442779" rel="nofollow" style="border:0px;font-family:inherit;font-style:inherit;font-weight:inherit;vertical-align:baseline;color:rgb(36,137,13);">漫画HDFS工作原理</a></p>
            </div>
                </div>