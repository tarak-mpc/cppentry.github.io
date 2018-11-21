---
layout:     post
title:      HDFS Federation(HDFS 联盟)介绍
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1 style="text-align:center;">HDFS Federation(HDFS 联盟)介绍</h1>
<p></p>
<p style="line-height:26px;text-align:left;">
张贵宾</p>
<p style="line-height:26px;text-align:left;">
guibin.beijing@gmail.com</p>
<p style="line-height:26px;text-align:left;">
2011.11.25</p>
<br><h2>1. 当前HDFS架构和功能概述</h2>
<p>我们先回顾一下HDFS功能。HDFS实际上具有两个功能：<strong>命名空间管理</strong>（Namespace management）和<strong>块/存储管理服务</strong>（block/storage management）。</p>
<p style="text-align:center;"><img src="http://hi.csdn.net/attachment/201111/27/0_1322396774E5cK.gif" alt=""><br></p>
<h3><br></h3>
<h3>1.1 命名空间管理</h3>
<p>HDFS的命名空间包含目录、文件和块。命名空间管理：是指命名空间支持对HDFS中的目录、文件和块做类似文件系统的创建、修改、删除、列表文件和目录等基本操作。</p>
<p><br></p>
<h3>1.2 块/存储管理</h3>
<p>在块存储服务中包含两部分工作：<strong>块管理</strong>和<strong>物理存储</strong>。这是一个更通用的存储服务。其他的应用可以直接建立在Block Storage上，如HBase，Foreign Namespaces等。</p>
<p><br></p>
<h4>1.2.1 块管理</h4>
<p>A) 处理Data Node向Name Node注册的请求，处理datanode的成员关系，处理来自Data Node周期性的心跳。</p>
<p>B) 处理来自块的报告信息，维护块的位置信息。</p>
<p>C) 处理与块相关的操作：块的创建、删除、修改及获取块信息。</p>
<p>D) 管理副本放置（replica placement）和块的复制及多余块的删除。</p>
<p><br></p>
<h4>1.2.2 物理存储</h4>
<p>所谓物理存储就是：Data Node把块存储到本地文件系统中，对本地文件系统的读、写。</p>
<p><br></p>
<h3>1.3 当前HDFS的架构</h3>
<p>在当前的HDFS架构中（Hadoop v0.23之前），<span style="color:#ff0000;"><strong>在整个HDFS集群中只有一个命名空间，并且只有单独的一个Name Node</strong></span>，这个Name Node负责对这单独的一个命名空间进行管理。这也正是单点失效（Single Point Failure）的隐患所在。本文所讲的HDFS Federation就是针对当前HDFS架构上的缺陷所做的改进，<span style="color:#ff0000;">简单说HDFS
 Federation就是使得HDFS支持多个命名空间，并且允许在HDFS中同时存在多个Name Node</span>。</p>
<p>简单回顾一下目前HDFS的架构，如下图所示。在整个HDFS集群中只有一个Namenode，还有一个Backup Namenode。Namenode会实时将变化的HDFS的信息同步给Backup Namenode。Backup Namenode顾名思义是用来做Namenode的备份的。Namenode中命名空间以层次结构组织中存储着文件名和BlockID的对应关系、BlockID和具体Block位置的对应关系。这个单独的Namenode管理着数个Datanode，Block分布在各个Datanode中，每个Datanode会周期性的向此Namenode发送心跳消息，报告自己所在Datanode的使用状态。Block是用来存储数据的最小单元，通常一个文件会存储在一个或者多个Block中，默认Block大小为64MB。<br></p>
<p style="text-align:center;"><img src="http://hi.csdn.net/attachment/201111/28/0_13224624387YYa.gif" alt=""><br></p>
<p style="text-align:left;"><br></p>
<h2><br></h2>
<h2>2. 单个Namenode的HDFS架构的局限性</h2>
<h3>2.1 Namespace（命名空间）的限制</h3>
<div>由于Namenode在内存中存储所有的元数据（metadata），因此单个Namenode所能存储的对象（文件+块）数目受到Namenode所在JVM的heap size的限制。50G的heap能够存储20亿（200 million）个对象，这20亿个对象支持4000个datanode，12PB的存储（假设文件平均大小为40MB）。</div>
<div>随着数据的飞速增长，存储的需求也随之增长。单个datanode从4T增长到36T，集群的尺寸增长到8000个datanode。存储的需求从12PB增长到大于100PB。</div>
<div><br></div>
<h3>2.2 性能的瓶颈</h3>
<div>由于是单个Namenode的HDFS架构，因此整个HDFS文件系统的吞吐量受限于单个Namenode的吞吐量。毫无疑问，这将成为下一代MapReduce的瓶颈。</div>
<div><br></div>
<h3>2.3 隔离问题</h3>
<div>由于HDFS仅有一个Namenode，无法隔离各个程序，因此HDFS上的一个实验程序就很有可能影响整个HDFS上运行的程序。那么在HDFS Federation中，可以用不同的Namespace来隔离不同的用户应用程序，使得不同Namespace Volume中的程序相互不影响。</div>
<div><br></div>
<h4>2.4 集群的可用性</h4>
<div>在只有一个Namenode的HDFS中，此Namenode的宕机无疑会导致整个集群不可用。</div>
<div><br></div>
<h3>2.5 Namespace和Block Management的紧密耦合</h3>
<div>当前在Namenode中的Namespace和Block Management组合的紧密耦合关系会导致如果想要实现另外一套Namenode方案比较困难，而且也限制了其他想要直接使用块存储的应用。</div>
<div><br></div>
<h3>2.6 为什么纵向扩展目前的Namenode不可行？比如将Namenode的Heap空间扩大到512GB。</h3>
<div>这样纵向扩展带来的第一个问题就是启动问题，启动花费的时间太长。当前具有50GB Heap Namenode的HDFS启动一次大概需要30分钟到2小时，那512GB的需要多久？</div>
<div>第二个潜在的问题就是Namenode在Full GC时，如果发生错误将会导致整个集群宕机。</div>
<div>第三个问题是对大JVM Heap进行调试比较困难。优化Namenode的内存使用性价比比较低。</div>
<div><br></div>
<h2>3. 为什么要引入Federation</h2>
<h3><span style="font-size:12px;font-weight:normal;"></span></h3>
<h3><span style="font-size:12px;font-weight:normal;">引入Federation的最主要原因是简单，其简单性是与真正的分布式Namenode相比而言的。Federation能够快速的解决了大部分单Namenode HDFS的问题。</span></h3>
<h3>
<span style="font-size:12px;font-weight:normal;">Federation是简单鲁棒的设计，由于联盟中各个Namenode之间是相互独立的。</span><span style="font-size:12px;font-weight:normal;">Federation整个核心设计实现大概用了3.5个月。大部分改变是在Datanode、Config和Tools，而Namenode本身的改动非常少，这样Namenode原先的鲁棒性不会受到影响。比分布式的Namenode简单，虽然这种实现的扩展性比起真正的分布式的Namenode要小些，但是可以迅速满足需求。另外一个原因是Federation良好的向后兼容性，已有的单Namenode的部署配置不需要任何改变就可以继续工作。</span>
</h3>
<div>因此Federation（联盟）是未来可选的方案之一。在Federation架构中可以无缝的支持目前单Namenode架构中的配置。</div>
<div><br></div>
<h2>4. HDFS Federation</h2>
<div>HDFS Federation使用了多个独立的Namenode/namespace来使得HDFS的命名服务能够水平扩展。在HDFS Federation中的Namenode之间是联盟关系，他们之间相互独立且不需要相互协调。HDFS Federation中的Namenode提供了提供了命名空间和块管理功能。HDFS Federation中的datanode被所有的Namenode用作公共存储块的地方。每一个datanode都会向所在集群中所有的Namenode注册，并且会周期性的发送心跳和块信息报告，同时处理来自Namenode的指令。</div>
<div style="text-align:center;">
<img src="http://hi.csdn.net/attachment/201111/28/0_1322450389u9Rr.gif" alt=""><br></div>
<div style="text-align:center;"><br></div>
<h3>4.1 Federation HDFS与当前HDFS的比较</h3>
<div>
<ul><li>当前HDFS只有一个命名空间（Namespace），它使用全部的块。而Federation HDFS中有多个独立的命名空间（Namespace），并且每一个命名空间使用一个块池（block pool）。</li>
<li>当前HDFS中只有一组块。而Federation HDFS中有多组独立的块。块池（block pool）就是属于同一个命名空间的一组块。</li>
<li>当前HDFS由一个Namenode和一组datanode组成。而Federation HDFS由多个Namenode和一组datanode，每一个datanode会为多个块池（block pool）存储块。</li>
</ul></div>
<h3><br></h3>
<h3>4.2 Block Pool(块池)</h3>
<p>所谓Block pool(块池)就是属于单个命名空间的一组block(块)。每一个datanode为所有的block pool存储块。Datanode是一个物理概念，而block pool是一个重新将block划分的逻辑概念。同一个datanode中可以存着属于多个block pool的多个块。Block pool允许一个命名空间在不通知其他命名空间的情况下为一个新的block创建Block ID。同时，一个Namenode失效不会影响其下的datanode为其他Namenode的服务。</p>
<p>当datanode与Namenode建立联系并开始会话后自动建立Block pool。每个block都有一个唯一的标识，这个标识我们称之为扩展的块ID（Extended Block ID）= BlockID+BlockID。这个扩展的块ID在HDFS集群之间都是唯一的，这为以后集群归并创造了条件。</p>
<p>Datanode中的数据结构都通过块池ID（BlockPoolID）索引，即datanode中的BlockMap，storage等都通过BPID索引。</p>
<p>在HDFS中，所有的更新、回滚都是以Namenode和BlockPool为单元发生的。即同一HDFS Federation中不同的Namenode/BlockPool之间没有什么关系。</p>
<p>Hadoop V0.23版本中Block Pool的管理功能依然放在了Namenode中，将来的版本中会将Block Pool的管理功能移动的新的功能节点中。</p>
<p><br></p>
<h3>4.3 Datanode的改进</h3>
<p>在datanode中，对应于每个Namnode都有一条相应的线程。每个datanode会去每一个Namenode注册，并且周期性的给所有的Namenode发送心跳及datanode的使用报告。Datanode还会给Namenode发送其所在的block pool的block report（块报告）。由于有多个Namenode同时存在，因此任何一个Namenode都可以随时动态加入、删除和更新。</p>
<p><br></p>
<h3>4.4 Federation中的其他方面的改进</h3>
<div>
<ul><li>提供了工具，对于Namenode的初始化和退役的监控和管理。</li>
<li>允许在datanode级别或者block pool级别的负载均衡。</li>
<li>Datanode的后台守护进程，为Federation所做的磁盘和目录扫描。</li>
<li>提供了显示Namenode的Block pool的使用状态的Web UI。</li>
<li>还提供了对全部集群存储使用状态的UI展示。</li>
<li>在Web UI中列出了所有的Namenode及其细节，如Namenode-BlockPoolID和存储的使用状态，失去联系的、活的和死的块信息。还有前往各个Namenode Web UI的链接。</li>
<li>Datanode退役状态的展示。</li>
</ul></div>
<h3><br></h3>
<h3>4.5 多命名空间的管理问题</h3>
<div>在一个集群中需要唯一的命名空间还是多个命名空间，核心问题命名空间中数据的共享和访问的问题。使用全局唯一的命名空间是解决数据共享和访问的一种方法。在多命名空间下，我们还可以使用Client Side Mount Table方式做到数据共享和访问。</div>
<div style="text-align:center;">
<img src="http://hi.csdn.net/attachment/201111/28/0_1322461437dGs2.gif" alt=""><br></div>
<p>如上图所示，每个深色三角形代表一个独立的命名空间，上方浅色的三角形代表从客户角度去访问下方的子命名空间。各个深色的命名空间Mount到浅色的表中，客户可以访问不同的挂载点来访问不同的命名空间，这就如同在Linux系统中访问不同挂载点一样。这就是HDFS Federation中命名空间管理的基本原理：<span style="color:#ff0000;">将各个命名空间挂载到全局mount－table中，就可以做将数据到全局共享；同样的命名空间挂载到个人的mount-table中，这就成为应用程序可见的命名空间视图</span>。</p>
<p><br></p>
<h3>4.6 Namespace Volume（命名空间卷）</h3>
<div>一个Namespace和它的Block Pool合在一起称作Namespace Volume。Namespace Volume是一个独立完整的管理单元。当一个Namenode/Namespace被删除，与之相对应的Block Pool也也被删除。在升级时每一个Namespace Volume也会整体作为一个单元。</div>
<div><br></div>
<h3>4.7 ClusterID</h3>
<p>在HDFS Federation中添加了Cluster ID用来区分集群中的每个节点。当格式化一个Namenode时，这个ClusterID会自动生成或者手动提供。在格式化同一集群中其他Namenode时会用到这个ClusterID。</p>
<p><br></p>
<h3>4.8 HDFS Federation对老版本的HDFS是兼容的</h3>
<p>这种兼容性可以使得已有的Namenode配置不需要任何改变继续工作。</p>
<p>具体的如何配置和管理Federation HDFS，请参考<a href="http://hadoop.apache.org/common/docs/r0.23.0/hadoop-yarn/hadoop-yarn-site/Federation.html#Federation_Configuration" rel="nofollow">http://hadoop.apache.org/common/docs/r0.23.0/hadoop-yarn/hadoop-yarn-site/Federation.html#Federation_Configuration</a>。</p>
<p><br></p>
<p><br></p>
<p>参考资料：</p>
<p>Hadoop is here:<a href="http://hortonworks.com/apache-hadoop-is-here" rel="nofollow">http://hortonworks.com/apache-hadoop-is-here</a></p>
<p>HDFS Federation:<a href="http://www.slideshare.net/ydn/hug-march-hdfs-federation" rel="nofollow">http://www.slideshare.net/ydn/hug-march-hdfs-federation</a></p>
<p>HDFS Federation:<a href="http://hadoop.apache.org/common/docs/r0.23.0/hadoop-yarn/hadoop-yarn-site/Federation.html" rel="nofollow">http://hadoop.apache.org/common/docs/r0.23.0/hadoop-yarn/hadoop-yarn-site/Federation.html</a></p>
<p>HDFS Federation hadoop submit 2011:<a href="http://www.slideshare.net/huguk/hdfs-federation-hadoop-summit2011" rel="nofollow">http://www.slideshare.net/huguk/hdfs-federation-hadoop-summit2011</a></p>
<p>An Introduction to HDFS Federation:<a href="http://hortonworks.com/an-introduction-to-hdfs-federation/" rel="nofollow">http://hortonworks.com/an-introduction-to-hdfs-federation/</a><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>            </div>
                </div>