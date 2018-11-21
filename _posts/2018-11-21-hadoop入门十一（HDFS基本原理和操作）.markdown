---
layout:     post
title:      hadoop入门十一（HDFS基本原理和操作）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，欢迎转载，欢迎添加微信panpan668706交流经验！					https://blog.csdn.net/m0_37639542/article/details/78260916				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="hadoop-2x-hdfs新特性">Hadoop 2.x HDFS新特性</h2>

<ul>
<li>HDFS联邦</li>
<li>HDFS HA(要用到Zookeeper)</li>
<li>HDFS快照</li>
</ul>

<p>解决了namenode单点得问题 <br>
高可用性 <br>
我有一个3000个主机，是不是得写3000个hosts文件，3000两两面密码，所以在企业级应用得时候还是有问题得。 <br>
快照得功能：是恢复到某个时刻得文件得内容。 <br>
操作命令与1.0x也是不同得。</p>

<p><a href="http://hadoop.apache.org/docs/" rel="nofollow" target="_blank">管理命令手册</a></p>

<h2 id="回顾hdfs两层模型">回顾：HDFS两层模型</h2>

<p><img src="https://img-blog.csdn.net/20171017142412184?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbTBfMzc2Mzk1NDI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
Block Manager :具体数据块得映射。</p>

<ol>
<li>当前HDFS架构和功能概述</li>
</ol>

<p>我们先回顾一下HDFS功能。HDFS实际上具有两个功能：命名空间管理（Namespace management）和块/存储管理服务（block/storage management）。</p>

<p>1.1 命名空间管理</p>

<p>HDFS的命名空间包含目录、文件和块。命名空间管理：是指命名空间支持对HDFS中的目录、文件和块做类似文件系统的创建、修改、删除、列表文件和目录等基本操作。</p>

<p>1.2 块/存储管理</p>

<p>在块存储服务中包含两部分工作：块管理和物理存储。这是一个更通用的存储服务。其他的应用可以直接建立在Block Storage上，如HBase，Foreign Namespaces等。</p>

<p>1.2.1 块管理</p>

<p>A) 处理Data Node向Name Node注册的请求，处理datanode的成员关系，处理来自Data Node周期性的心跳。 <br>
B) 处理来自块的报告信息，维护块的位置信息。 <br>
C) 处理与块相关的操作：块的创建、删除、修改及获取块信息。 <br>
D) 管理副本放置（replica placement）和块的复制及多余块的删除。</p>

<p>1.2.2 物理存储</p>

<p>所谓物理存储就是：Data Node把块存储到本地文件系统中，对本地文件系统的读、写。</p>

<p>1.3 当前HDFS的架构</p>

<p>在当前的HDFS架构中（Hadoop v0.23之前），在整个HDFS集群中只有一个命名空间，并且只有单独的一个Name Node，这个Name Node负责对这单独的一个命名空间进行管理。这也正是单点失效（Single Point Failure）的隐患所在。本文所讲的HDFS Federation就是针对当前HDFS架构上的缺陷所做的改进，简单说HDFS Federation就是使得HDFS支持多个命名空间，并且允许在HDFS中同时存在多个Name Node。 <br>
简单回顾一下目前HDFS的架构，如下图所示。在整个HDFS集群中只有一个Namenode，还有一个Backup Namenode。Namenode会实时将变化的HDFS的信息同步给Backup Namenode。Backup Namenode顾名思义是用来做Namenode的备份的。Namenode中命名空间以层次结构组织中存储着文件名和BlockID的对应关系、BlockID和具体Block位置的对应关系。这个单独的Namenode管理着数个Datanode，Block分布在各个Datanode中，每个Datanode会周期性的向此Namenode发送心跳消息，报告自己所在Datanode的使用状态。Block是用来存储数据的最小单元，通常一个文件会存储在一个或者多个Block中，默认Block大小为64MB。 <br>
<img src="https://img-blog.csdn.net/20171017143951277?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbTBfMzc2Mzk1NDI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<h2 id="2-单个namenode的hdfs架构的局限性">2. 单个Namenode的HDFS架构的局限性</h2>

<p><img src="https://img-blog.csdn.net/20171017142536890?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbTBfMzc2Mzk1NDI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><a href="http://blog.csdn.net/strongerbit/article/details/7013221" rel="nofollow" target="_blank">HDFS Federation(HDFS 联盟)介绍</a></p>

<p>2.1 Namespace（命名空间）的限制</p>

<p>由于Namenode在内存中存储所有的元数据（metadata），因此单个Namenode所能存储的对象（文件+块）数目受到Namenode所在JVM的heap size的限制。50G的heap能够存储20亿（200 million）个对象，这20亿个对象支持4000个datanode，12PB的存储（假设文件平均大小为40MB）。 <br>
随着数据的飞速增长，存储的需求也随之增长。单个datanode从4T增长到36T，集群的尺寸增长到8000个datanode。存储的需求从12PB增长到大于100PB。</p>

<p>2.2 性能的瓶颈</p>

<p>由于是单个Namenode的HDFS架构，因此整个HDFS文件系统的吞吐量受限于单个Namenode的吞吐量。毫无疑问，这将成为下一代MapReduce的瓶颈。</p>

<p>2.3 隔离问题</p>

<p>由于HDFS仅有一个Namenode，无法隔离各个程序，因此HDFS上的一个实验程序就很有可能影响整个HDFS上运行的程序。那么在HDFS Federation中，可以用不同的Namespace来隔离不同的用户应用程序，使得不同Namespace Volume中的程序相互不影响。</p>

<p>2.4 集群的可用性</p>

<p>在只有一个Namenode的HDFS中，此Namenode的宕机无疑会导致整个集群不可用。</p>

<p>2.5 Namespace和Block Management的紧密耦合</p>

<p>当前在Namenode中的Namespace和Block Management组合的紧密耦合关系会导致如果想要实现另外一套Namenode方案比较困难，而且也限制了其他想要直接使用块存储的应用。</p>

<p>2.6 为什么纵向扩展目前的Namenode不可行？比如将Namenode的Heap空间扩大到512GB。</p>

<p>这样纵向扩展带来的第一个问题就是启动问题，启动花费的时间太长。当前具有50GB Heap Namenode的HDFS启动一次大概需要30分钟到2小时，那512GB的需要多久？ <br>
第二个潜在的问题就是Namenode在Full GC时，如果发生错误将会导致整个集群宕机。 <br>
第三个问题是对大JVM Heap进行调试比较困难。优化Namenode的内存使用性价比比较低。</p>



<h2 id="为什么要引入federation">为什么要引入Federation</h2>



<h3 id="多namenodes和namespaces体系">多namenodes和namespaces体系</h3>

<p><img src="https://img-blog.csdn.net/20171017143451082?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbTBfMzc2Mzk1NDI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
联邦和HA是完全不一样得</p>

<ul>
<li>联邦是为了解决单节点</li>
<li>HA是备份 <br>
<img src="https://img-blog.csdn.net/20171017143639957?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbTBfMzc2Mzk1NDI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></li>
</ul>

<p>从这个图上：可以得出每个datanode都可能被上面得namespace所使用，每个namespace都有可能来自于下面很多个datanode得数据。整个集群都是可用得。</p>

<p>引入Federation的最主要原因是简单，其简单性是与真正的分布式Namenode相比而言的。Federation能够快速的解决了大部分单Namenode HDFS的问题。</p>

<p>Federation是简单鲁棒的设计，由于联盟中<strong>各个Namenode之间是相互独立</strong>的。Federation整个核心设计实现大概用了3.5个月。大部分改变是在Datanode、Config和Tools，而Namenode本身的改动非常少，这样Namenode原先的鲁棒性不会受到影响。比分布式的Namenode简单，虽然这种实现的扩展性比起真正的分布式的Namenode要小些，但是可以迅速满足需求。另外一个原因是Federation良好的向后兼容性，已有的单Namenode的部署配置不需要任何改变就可以继续工作。</p>

<p>因此Federation（联盟）是未来可选的方案之一。在Federation架构中可以无缝的支持目前单Namenode架构中的配置。</p>

<ol>
<li>HDFS Federation</li>
</ol>

<p>HDFS Federation使用了多个独立的Namenode/namespace来使得HDFS的命名服务能够水平扩展。在HDFS Federation中的Namenode之间是联盟关系，他们之间相互独立且不需要相互协调。HDFS Federation中的Namenode提供了提供了命名空间和块管理功能。HDFS Federation中的datanode被所有的Namenode用作公共存储块的地方。每一个datanode都会向所在集群中所有的Namenode注册，并且会周期性的发送心跳和块信息报告，同时处理来自Namenode的指令。</p>

<p>4.1 Federation HDFS与当前HDFS的比较</p>

<p>当前HDFS只有一个命名空间（Namespace），它使用全部的块。而Federation HDFS中有多个独立的命名空间（Namespace），并且每一个命名空间使用一个块池（block pool）。 <br>
当前HDFS中只有一组块。而Federation HDFS中有多组独立的块。块池（block pool）就是属于同一个命名空间的一组块。 <br>
当前HDFS由一个Namenode和一组datanode组成。而Federation HDFS由多个Namenode和一组datanode，每一个datanode会为多个块池（block pool）存储块。</p>



<h2 id="42-block-pool块池">4.2 Block Pool(块池)</h2>

<p><img src="https://img-blog.csdn.net/20171017144056469?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbTBfMzc2Mzk1NDI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><a href="http://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-hdfs/Federation.html" rel="nofollow" target="_blank">HDFS Federation得官方文档解释</a> <br>
所谓Block pool(块池)就是属于单个命名空间的一组block(块)。 <br>
每一个datanode为所有的block pool存储块。 <br>
Datanode是一个物理概念，而block pool是一个重新将block划分的逻辑概念。同一个datanode中可以存着属于多个block pool的多个块。Block pool允许一个命名空间在不通知其他命名空间的情况下为一个新的block创建Block ID。同时，一个Namenode失效不会影响其下的datanode为其他Namenode的服务。 <br>
当datanode与Namenode建立联系并开始会话后自动建立Block pool。每个block都有一个唯一的标识，这个标识我们称之为扩展的块ID（Extended Block ID）= BlockID+BlockID。这个扩展的块ID在HDFS集群之间都是唯一的，这为以后集群归并创造了条件。 <br>
Datanode中的数据结构都通过块池ID（BlockPoolID）索引，即datanode中的BlockMap，storage等都通过BPID索引。 <br>
在HDFS中，所有的更新、回滚都是以Namenode和BlockPool为单元发生的。即同一HDFS Federation中不同的Namenode/BlockPool之间没有什么关系。 <br>
Hadoop V0.23版本中Block Pool的管理功能依然放在了Namenode中，将来的版本中会将Block Pool的管理功能移动的新的功能节点中。 <br>
<img src="https://img-blog.csdn.net/20171017144306932?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbTBfMzc2Mzk1NDI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h2 id="好处">好处</h2>

<p><img src="https://img-blog.csdn.net/20171017144417522?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbTBfMzc2Mzk1NDI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>4.3 Datanode的改进</p>

<p>在datanode中，对应于每个Namnode都有一条相应的线程。每个datanode会去每一个Namenode注册，并且周期性的给所有的Namenode发送心跳及datanode的使用报告。Datanode还会给Namenode发送其所在的block pool的block report（块报告）。由于有多个Namenode同时存在，因此任何一个Namenode都可以随时动态加入、删除和更新。</p>

<p>4.4 Federation中的其他方面的改进</p>

<p>提供了工具，对于Namenode的初始化和退役的监控和管理。 <br>
允许在datanode级别或者block pool级别的负载均衡。 <br>
Datanode的后台守护进程，为Federation所做的磁盘和目录扫描。 <br>
提供了显示Namenode的Block pool的使用状态的Web UI。 <br>
还提供了对全部集群存储使用状态的UI展示。 <br>
在Web UI中列出了所有的Namenode及其细节，如Namenode-BlockPoolID和存储的使用状态，失去联系的、活的和死的块信息。还有前往各个Namenode Web UI的链接。 <br>
Datanode退役状态的展示。</p>

<p>4.5 多命名空间的管理问题</p>

<p>在一个集群中需要唯一的命名空间还是多个命名空间，核心问题命名空间中数据的共享和访问的问题。使用全局唯一的命名空间是解决数据共享和访问的一种方法。在多命名空间下，我们还可以使用Client Side Mount Table方式做到数据共享和访问。</p>

<p>如上图所示，每个深色三角形代表一个独立的命名空间，上方浅色的三角形代表从客户角度去访问下方的子命名空间。各个深色的命名空间Mount到浅色的表中，客户可以访问不同的挂载点来访问不同的命名空间，这就如同在Linux系统中访问不同挂载点一样。这就是HDFS Federation中命名空间管理的基本原理：将各个命名空间挂载到全局mount－table中，就可以做将数据到全局共享；同样的命名空间挂载到个人的mount-table中，这就成为应用程序可见的命名空间视图。</p>

<p><img src="https://img-blog.csdn.net/20171017144455032?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbTBfMzc2Mzk1NDI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><img src="https://img-blog.csdn.net/20171017144719872?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbTBfMzc2Mzk1NDI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
看起来像是整体得，联邦制。</p>

<p>4.6 Namespace Volume（命名空间卷）</p>

<p>一个Namespace和它的Block Pool合在一起称作Namespace Volume。Namespace Volume是一个独立完整的管理单元。当一个Namenode/Namespace被删除，与之相对应的Block Pool也也被删除。在升级时每一个Namespace Volume也会整体作为一个单元。</p>

<p>4.7 ClusterID</p>

<p>在HDFS Federation中添加了Cluster ID用来区分集群中的每个节点。当格式化一个Namenode时，这个ClusterID会自动生成或者手动提供。在格式化同一集群中其他Namenode时会用到这个ClusterID。</p>

<p>4.8 HDFS Federation对老版本的HDFS是兼容的</p>

<p>这种兼容性可以使得已有的Namenode配置不需要任何改变继续工作。 <br>
具体的如何配置和管理Federation HDFS，</p>



<h2 id="未解决得问题">未解决得问题</h2>

<p><img src="https://img-blog.csdn.net/20171017144957862?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbTBfMzc2Mzk1NDI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h2 id="配置">配置</h2>

<p>一般情况下，大型得公司才会使用。 <br>
<img src="https://img-blog.csdn.net/20171017145024872?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbTBfMzc2Mzk1NDI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h2 id="格式化名称节点">格式化名称节点</h2>

<p><img src="https://img-blog.csdn.net/20171017145205446?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbTBfMzc2Mzk1NDI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h2 id="通俗得解释">通俗得解释</h2>

<p><img src="https://img-blog.csdn.net/20171017145323408?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbTBfMzc2Mzk1NDI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>可以贴切得解释：联邦</p>

<p>把每个namenode看出拼图上得小块，小块之间不重叠，没有复制备份得机制，有个小块失效了，但是地图基本轮廓还是有的，以目录为单位映射到namenode上，不能使用hash映射，导致每个目录下面都有坏掉得文件，现在根据目录来映射得效率感觉很低，但是损坏率低。</p>



<h2 id="hdfs快照">HDFS快照</h2>

<p><img src="https://img-blog.csdn.net/20171017145816923?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbTBfMzc2Mzk1NDI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
一个单独得节点坏掉了，但是可以重别的地方恢复过来，有个很麻烦得事情，误操作导致得数据不可挽回，我可以把某个时刻某个数据保留下来，我误操作以后，在恢复到某个时刻。 <br>
<img src="https://img-blog.csdn.net/20171017150206382?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbTBfMzc2Mzk1NDI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<hr>

<h2 id="数据集成工具">数据集成工具</h2>

<p><img src="https://img-blog.csdn.net/20171017140818366?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbTBfMzc2Mzk1NDI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
把bar复原 <br>
S0 是备份得快照</p>

<h2 id="hdfs得c得api">HDFS得C得api</h2>



<pre class="prettyprint"><code class=" hljs lasso">yum <span class="hljs-attribute">-y</span> install gcc gcc<span class="hljs-attribute">-c</span><span class="hljs-subst">++</span> autoconf make</code></pre>



<h2 id="运行java得程序">运行java得程序</h2>

<p>都可以到官网上去查得到</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>