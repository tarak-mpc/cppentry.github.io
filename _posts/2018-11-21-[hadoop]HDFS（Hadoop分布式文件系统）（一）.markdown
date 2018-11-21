---
layout:     post
title:      [hadoop]HDFS（Hadoop分布式文件系统）（一）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/alex_bean/article/details/51383638				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><strong>Hadoop的起源：</strong></p>
<p>Hadoop是Google的集群系统的开源实现</p>
<p></p>
<ul><li>Google集群系统：GFS(Google File System)、 MapReduce、BigTable</li><li>Hadoop主要由HDFS(Hadoop Distributed File System Hadoop分布式文件系统)、MapReduce和HBase组成</li></ul><div>Hadoop的初衷是为了解决Nutch的海量数据爬取和储存的需要</div>
<div>Hadoop与2005年秋天作为Lucene的子项目Nutch的一部分正式引入Apache基金会</div>
<div>Hadoop官方网站</div>
<div><br></div>
<div><strong>Hadoop 两大核心</strong></div>
<div>
<ul><li>MapReduce<br>
-Map: 任务的分解<br>
-Reduce:结果的汇总</li><li>HDFS<br>
-Name<br>
-DataNode<br>
-Client</li></ul><div><strong>什么是HDFS？</strong></div>
</div>
<div>HDFS(Hadoop Distributed File System) Hadoop分布式文件系统，是储存数据使用的。HDFS为了做到可靠性（reliability）创建了多份数据块(data blocks)的复制(replicas)，并将它们放置在服务器群的计算节点中(computer nodes)，MapReduce就可以在它们所在的节点上处理这些数据了。<br></div>
<div><strong>大数据与传统数据的区别是什么？</strong></div>
<div><img src="https://img-blog.csdn.net/20160512142137165?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div><strong>并行关系数据库与 MPP or Hadoop 区别？</strong></div>
<div><strong>并行关系数据库</strong></div>
<div>
<ul><li>多个独立关系数据库服务器，访问共享的资源储存池<br></li><li>优势：<br>
采用多个关系数据库服务器，多个储存，与原有的架构相比，扩展了储存容量和计算能力</li><li>劣势：<br>
计算与储存分离，数据访问存在竞争和带宽瓶颈<br>
支持的关系数据库服务器数量有限<br>
只能向上扩展，不能横向扩展</li><li>适合复杂的需要事务处理的应用</li></ul><div><strong>MPP or Hadoop</strong></div>
</div>
<div>
<ul><li>由大量独立的服务器通过网络互连形成集群，每个服务器带储存</li><li>优势：<br>
计算与储存融合，支持横向扩展，更好的扩展性</li><li>劣势：<br>
解决数据冲突时需要节点间协作</li><li>适用范围：<br>
数据仓库和离线数据分析(MPP,Hadoop/HBase)<br>
大规模在线实时应用(单行事务处理能力满足的场景)(HBase)</li></ul><div><strong>Hadoop的子项目（红色表示hadoop家族）</strong></div>
</div>
<div>
<ul><li><span style="color:#ff0000;">Core</span>: 一套分布式文件系统以及支持Map-Reduce的计算框架</li><li>Avro: 定义了一种用于支持大数据应用的数据格式，并为这种格式提供了不同的编程语言的支持</li><li><span style="color:#ff0000;">HDFS</span>: Hadoop分布式文件系统</li><li><span style="color:#ff0000;">Map/Reduce</span>: 是一个使用简易的软件框架，基于它写出来的应用程序能够运行在由上千个商用机器组成的大型集群上，并以一种可靠容错的方式并行处理上T级别的数据集</li><li><span style="color:#ff0000;">Zookeeper</span>: 是高可用的和可靠的分布式协同系统</li><li><span style="color:#ff0000;">Pig</span>: 建立于Hadoop Core之上为并行计算环境提供了一套数据工作流语言和执行框架</li><li><span style="color:#ff0000;">Hive</span>: 是为提供简单的数据操作而设计的下一代分布式数据仓库。它提供了简单的类似SQL的语法的HiveQL语言进行数据查询</li><li><span style="color:#ff0000;">HBase</span>: 建立于Hadoop Core之上提供一个可扩展的数据库系统</li><li>Flume: 一个分布式、可靠、和高可用的海量日志聚合的系统，支持在系统中定制各类数据发送方，用于收集数据</li><li>Mashout: 是一套具有可扩展能力的机器学习类库</li><li>Sqoop: 是Apache下用于RDBMS和HDFS相互导数据的工具</li></ul><div><strong>HDFS结构</strong></div>
</div>
<div>元数据：文件大小，文件名称等</div>
<div><img src="https://img-blog.csdn.net/20160512154208979?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div><br></div>
<div><strong>HDFS架构</strong></div>
<div><strong>HDFS有三个节点：NameNode、SecondaryNameNode、DataNode</strong></div>
<div><img src="https://img-blog.csdn.net/20160512161234472?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div><strong>HDFS运行机制</strong></div>
<div>
<ul><li>一个Name Node（名字节点） 和 多个 Data Node（数据节点）</li><li>数据复制（冗余机制）<br>
--存放的位置（机架感知策略）</li><li>故障检测<br>
--数据节点<br>
心跳包（监测是否宕机）<br>
块报告（安全模式下检测）<br>
数据完整性检测（校验和比较）<br>
--名字节点（日志文件，镜像文件）</li><li>空间回收机制</li></ul><div><strong>HDFS优点</strong></div>
</div>
<div>
<ul><li>高容错性<br>
数据自动保存多个副本<br>
副本丢失后，自动恢复</li><li>适合批处理<br>
移动计算而非数据<br>
数据位置暴露给计算框架</li><li>适合大数据处理<br>
GB、TB、甚至PB级数据<br>
百万规模以上的文件数量<br>
10K+节点</li><li>可构建在廉价机器上<br>
通过多副本提高可靠性<br>
提供了容错和恢复机制</li></ul><div><strong>HDFS缺点</strong><br><ul><li>低延迟数据访问<br>
比如毫秒级<br>
低延迟与高吞吐率</li><li>小文件存取<br>
占用NameNode大量内存<br>
寻道时间超过读取时间</li><li>并发写入、文件随机修改<br>
一个文件只能有一个写者<br>
仅支持append</li></ul><div>
<div><strong>HDFS数据储存单元（block）</strong></div>
<div>
<ul><li><strong>文件被切分成固定大小的数据块<br></strong>默认数据块大小为64M，可配置<br>
若文件大小不到64M，则单独存成一个block</li><li><strong>一个文件储存方式<br></strong>按大小被切分成若干个block，存储到不同节点上<br>
默认情况下每个block都有三个副本</li><li><strong>Block大小和副本数通过client端上传文件时设置，文件上传成功后副本数可以变更，block size不可变更</strong></li></ul><div><strong>HDFS设计思想</strong></div>
</div>
<div><strong><img src="https://img-blog.csdn.net/20160512164127208?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></strong></div>
<div><strong>NDFS三大节点介绍</strong></div>
<div><strong>NameNode(NN)</strong></div>
<div>
<ul><li><strong><strong>NameNode主要功能：接受客户端的读写服务</strong><br></strong></li><li><strong>NameNode保存metadata信息包括<br></strong>
<div>文件owership（所有权）和permissions（权限）</div>
<div>文件包含哪些块(block)</div>
block位置保存在哪个DataNode（由DataNode启动时上报）</li><li><strong>NameNode的metadata信息在启动后会加载到内存<br></strong>meatadata储存到磁盘文件名为“fsimage”<br>
block的位置信息不会保存到fsimage<br>
edits记录对metadata的操作日志</li></ul></div>
<div><strong>SecondaryNameNode(SNN)</strong></div>
<ul><li><strong>它不是NN的备份（但可以做备份），它的主要工作是帮助NN合并editslog，减少NN启动时间</strong></li><li><strong>SNN执行合并时机</strong><br>
根据配置文件设置的时间间隔fs.checkpoint.period 默认3600秒<br>
根据配置文件设置edits log大小 fs.checkpoint.size规定edits文件的最大值默认是64M</li></ul><div>SNN合并流程</div>
</div>
</div>
</div>
<div><img src="https://img-blog.csdn.net/20160513100350029?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div><strong>DataNode (DN)</strong></div>
<div>
<ul><li>储存数据(Block)</li><li>启动DN线程的时候会向NN汇报block信息</li><li>通过向NN发送心跳保持与其联系（3秒一次），如果NN 10分钟收到DN的心跳，则认为其已经lost，并copy其上的block到其他DN</li></ul><div><strong>Block的副本放置策略</strong></div>
</div>
<div>
<ul><li>第一个副本：放置在上传文件的DN；如果是集群外提交，则随机挑选一台磁盘不太满，CPU不太忙的节点。</li><li>第二个副本：放置在于第一个副本<span style="color:#ff0000;">不同的机架节点</span>上</li><li>第三个副本：与第二个副本<span style="color:#ff0000;">相同机架的节点</span></li><li>更多副本：随机节点</li></ul></div>
<div><img src="https://img-blog.csdn.net/20160513135918215?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div><strong>HDFS读流程</strong></div>
<div><img src="https://img-blog.csdn.net/20160513140606788?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div><strong>HDFS写流程</strong></div>
<div><img src="https://img-blog.csdn.net/20160513140924667?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div><strong>HDFS文件权限</strong></div>
<div>
<ul><li>与Linux文件权限类似<br>
r:read;w:write;x:execute，权限x对于文件忽略，对于文件夹表示是否允许访问其内容</li><li>如果Linux系统用户zhangsan使用hadoop命令创建一个文件，那么这个文件在HDFS中owner就是zhangsan</li><li>HDFS的权限目的：阻止好人做错事，而不是阻止坏人做坏事。HDFS对owner不做密码验证，只要知道用户名就可以。</li></ul><div><strong>安全模式</strong></div>
</div>
<div>
<ul><li>NameNode启动的时候，首先将映像文件(fsimage)载入内存，并执行编辑日志(edits)中的各项操作</li><li>一旦在内存中成功建立文件系统元数据的映射，则创建一个新的fsimage文件（这个操作不需要SecondaryNameNode）和一个空的编辑日志。</li><li>此刻NameNode运行在安全模式。即NameNode的文件系统对于客户端来说是只读的。（显示目录，显示文件内容等。写，删除，重命名都会失败）。</li><li>在此阶段NameNode收集各个DataNode的报告，当数据块达到最小副本数以上时，会被认为是安全的，在一定比例（可设置）的数据块被确定为“安全”后，再过若干时间，安全模式结束。</li><li>当检测到副本数不足的数据块时，该块会被复制直到达到最小副本数，系统中数据块的位置并不是由NameNode维护的，而是以块列表形式储存在DataNode中。</li></ul></div>
<div><br></div>
<p></p>
            </div>
                </div>