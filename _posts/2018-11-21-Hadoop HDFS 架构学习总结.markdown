---
layout:     post
title:      Hadoop HDFS 架构学习总结
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p></p><div class="toc"><h3>文章目录</h3><ul><ul><li><a href="#HDFS_10__5" rel="nofollow">HDFS 1.0 架构</a></li><ul><li><a href="#NameNode_8" rel="nofollow">NameNode</a></li><li><a href="#Secondary_NameNode_19" rel="nofollow">Secondary NameNode</a></li><li><a href="#DataNode_21" rel="nofollow">DataNode</a></li></ul><li><a href="#_23" rel="nofollow">文件写入过程</a></li><li><a href="#_35" rel="nofollow">文件读取过程</a></li><li><a href="#HDFS_10__46" rel="nofollow">HDFS 1.0 的问题</a></li><li><a href="#HDFS_20__HA__54" rel="nofollow">HDFS 2.0 的 HA 实现</a></li><ul><li><a href="#FailoverController_65" rel="nofollow">FailoverController</a></li><li><a href="#_69" rel="nofollow">自动触发主备选举</a></li></ul><li><a href="#HDFS__75" rel="nofollow">HDFS 脑裂问题</a></li><li><a href="#_104" rel="nofollow">第三方存储（共享存储）</a></li><li><a href="#HDFS_20_Federation__115" rel="nofollow">HDFS 2.0 Federation 实现</a></li><ul><li><a href="#Federation__121" rel="nofollow">Federation 架构</a></li><li><a href="#Federation__124" rel="nofollow">Federation 的核心设计思想</a></li></ul></ul></ul></div><br>
转载自<br>
<a href="https://www.colabug.com/3574500.html" rel="nofollow">https://www.colabug.com/3574500.html</a><p></p>
<p>HDFS（Hadoop Distributed File System）是一个分布式文件存储系统，几乎是离线存储领域的标准解决方案（有能力自研的大厂列外），业内应用非常广泛。近段抽时间，看一下 HDFS 的架构设计，虽然研究生也学习过相关内容，但是现在基本忘得差不多了，今天抽空对这块做了一个简单的总结，也算是再温习了一下这块的内容，这样后续再看 HDFS 方面的文章时，不至于处于懵逼状态。</p>
<h2><a id="HDFS_10__5"></a>HDFS 1.0 架构</h2>
<p>HDFS 采用的是 Master/Slave 架构，一个 HDFS 集群包含一个单独的 NameNode 和多个 DataNode 节点，如下图所示（这个图是 HDFS1.0的架构图，经典的架构图）：<br>
<img src="https://img-blog.csdnimg.cn/20181119112854590.png" alt="在这里插入图片描述"></p>
<h3><a id="NameNode_8"></a>NameNode</h3>
<p>NameNode 负责管理整个分布式系统的元数据，主要包括：</p>
<ul>
<li>目录树结构；</li>
<li>文件到数据库 Block 的映射关系；</li>
<li>Block 副本及其存储位置等管理数据；</li>
<li>DataNode 的状态监控，两者通过段时间间隔的心跳来传递管理信息和数据信息，通过这种方式的信息传递，NameNode 可以获知每个 DataNode 保存的 Block 信息、DataNode 的健康状况、命令 DataNode 启动停止等（如果发现某个 DataNode 节点故障，NameNode 会将其负责的 block 在其他 DataNode 上进行备份）。</li>
</ul>
<p>这些数据保存在内存中，同时在磁盘保存两个元数据管理文件：fsimage 和 editlog。</p>
<ul>
<li>fsimage：是内存命名空间元数据在外存的镜像文件；</li>
<li>editlog：则是各种元数据操作的 write-ahead-log 文件，在体现到内存数据变化前首先会将操作记入 editlog 中，以防止数据丢失。<br>
这两个文件相结合可以构造完整的内存数据。</li>
</ul>
<h3><a id="Secondary_NameNode_19"></a>Secondary NameNode</h3>
<p>Secondary NameNode 并不是 NameNode 的热备机，而是定期从 NameNode 拉取 fsimage 和 editlog 文件，并对两个文件进行合并，形成新的 fsimage 文件并传回 NameNode，这样做的目的是减轻 NameNod 的工作压力，本质上 SNN 是一个提供检查点功能服务的服务点。</p>
<h3><a id="DataNode_21"></a>DataNode</h3>
<p>负责数据块的实际存储和读写工作，Block 默认是64MB（HDFS2.0改成了128MB），当客户端上传一个大文件时，HDFS 会自动将其切割成固定大小的 Block，为了保证数据可用性，每个 Block 会以多备份的形式存储，默认是3份。</p>
<h2><a id="_23"></a>文件写入过程</h2>
<p>Client 向 HDFS 文件写入的过程可以参考 HDFS写文件过程分析 ，整体过程如下图（这个图比较经典，最开始来自 《Hadoop：The Definitive Guide》 ）所示：<br>
<img src="https://img-blog.csdnimg.cn/20181119113057513.png" alt="在这里插入图片描述"><br>
具体过程如下：</p>
<ol>
<li>Client 调用 DistributedFileSystem 对象的create方法，创建一个文件输出流（FSDataOutputStream）对象；</li>
<li>通过 DistributedFileSystem 对象与集群的 NameNode 进行一次 RPC 远程调用，在 HDFS 的 Namespace 中创建一个文件条目（Entry），此时该条目没有任何的 Block，NameNode 会返回该数据每个块需要拷贝的 DataNode 地址信息；</li>
<li>通过 FSDataOutputStream 对象，开始向 DataNode 写入数据，数据首先被写入 FSDataOutputStream 对象内部的数据队列中，数据队列由 DataStreamer 使用，它通过选择合适的 DataNode 列表来存储副本，从而要求 NameNode 分配新的 block；</li>
<li>DataStreamer 将数据包以流式传输的方式传输到分配的第一个 DataNode 中，该数据流将数据包存储到第一个 DataNode 中并将其转发到第二个 DataNode 中，接着第二个 DataNode 节点会将数据包转发到第三个 DataNode 节点；</li>
<li>DataNode 确认数据传输完成，最后由第一个 DataNode 通知 client 数据写入成功；</li>
<li>完成向文件写入数据，Client 在文件输出流（FSDataOutputStream）对象上调用close方法，完成文件写入；</li>
<li>调用 DistributedFileSystem 对象的 complete 方法，通知 NameNode 文件写入成功，NameNode 会将相关结果记录到 editlog 中。</li>
</ol>
<h2><a id="_35"></a>文件读取过程</h2>
<p>相对于文件写入，文件的读取就简单一些，流程如下图所示：<br>
<img src="https://img-blog.csdnimg.cn/20181119113216682.png" alt="在这里插入图片描述"><br>
其具体过程总结如下（简单总结一下）：</p>
<ol>
<li>Client 通过 DistributedFileSystem 对象与集群的 NameNode 进行一次 RPC 远程调用，获取文件 block 位置信息；</li>
<li>NameNode 返回存储的每个块的 DataNode 列表；</li>
<li>Client 将连接到列表中最近的 DataNode；</li>
<li>Client 开始从 DataNode 并行读取数据；</li>
<li>一旦 Client 获得了所有必须的 block，它就会将这些 block 组合起来形成一个文件。</li>
</ol>
<p>在处理 Client 的读取请求时，HDFS 会利用机架感知选举最接近 Client 位置的副本，这将会减少读取延迟和带宽消耗。</p>
<h2><a id="HDFS_10__46"></a>HDFS 1.0 的问题</h2>
<p>在前面的介绍中，关于 HDFS1.0 的架构，首先都会看到 NameNode 的单点问题，这个在生产环境中是非常要命的问题，早期的 HDFS 由于规模较小，有些问题就被隐藏了，但自从进入了移动互联网时代，很多公司都开始进入了 PB 级的大数据时代，HDFS 1.0的设计缺陷已经无法满足生产的需求，最致命的问题有以下两点：</p>
<ul>
<li>NameNode 的单点问题，如果 NameNode 挂掉了，数据读写都会受到影响，HDFS 整体将变得不可用，这在生产环境中是不可接受的；</li>
<li>水平扩展问题，随着集群规模的扩大，1.0 时集群规模达到3000时，会导致整个集群管理的文件数目达到上限（因为 NameNode 要管理整个集群 block 元信息、数据目录信息等）。</li>
</ul>
<p>为了解决上面的两个问题，Hadoop2.0 提供一套统一的解决方案：</p>
<ol>
<li>HA（High Availability 高可用方案）：这个是为了解决 NameNode 单点问题；</li>
<li>NameNode Federation：是用来解决 HDFS 集群的线性扩展能力。</li>
</ol>
<h2><a id="HDFS_20__HA__54"></a>HDFS 2.0 的 HA 实现</h2>
<p>关于 HDFS 高可用方案，非常推荐这篇文章： Hadoop NameNode 高可用 (High Availability) 实现解析 ，IBM 博客的质量确实很高，这部分我这里也是主要根据这篇文章做一个总结，这里会从问题的原因、如何解决的角度去总结，并不会深入源码的实现细节，想有更深入了解还是推荐上面文章。</p>
<p>这里先看下 HDFS 高可用解决方案的架构设计，如下图（下图来自上面的文章）所示：<br>
<img src="https://img-blog.csdnimg.cn/20181119140651183.png" alt="在这里插入图片描述"><br>
这里与前面 1.0 的架构已经有很大变化，简单介绍一下上面的组件：</p>
<ol>
<li>Active NameNode 和 Standby NameNode：两台 NameNode 形成互备，一台处于 Active 状态，为主 NameNode，另外一台处于 Standby 状态，为备 NameNode，只有主 NameNode 才能对外提供读写服务；</li>
<li>ZKFailoverController（主备切换控制器，FC）：ZKFailoverController 作为独立的进程运行，对 NameNode 的主备切换进行总体控制。ZKFailoverController 能及时检测到 NameNode 的健康状况，在主 NameNode 故障时借助 Zookeeper 实现自动的主备选举和切换（当然 NameNode 目前也支持不依赖于 Zookeeper 的手动主备切换）；</li>
<li>Zookeeper 集群：为主备切换控制器提供主备选举支持；</li>
<li>共享存储系统：共享存储系统是实现 NameNode 的高可用最为关键的部分，共享存储系统保存了 NameNode 在运行过程中所产生的 HDFS 的元数据。主 NameNode 和备 NameNode 通过共享存储系统实现元数据同步。在进行主备切换的时候，新的主 NameNode 在确认元数据完全同步之后才能继续对外提供服务。</li>
<li>DataNode 节点：因为主 NameNode 和备 NameNode 需要共享 HDFS 的数据块和 DataNode 之间的映射关系，为了使故障切换能够快速进行，DataNode 会同时向主 NameNode 和备 NameNode 上报数据块的位置信息。</li>
</ol>
<h3><a id="FailoverController_65"></a>FailoverController</h3>
<p>FC 最初的目的是为了实现 SNN 和 ANN 之间故障自动切换，FC 是独立与 NN 之外的故障切换控制器，ZKFC 作为 NameNode 机器上一个独立的进程启动 ，它启动的时候会创建 HealthMonitor 和 ActiveStandbyElector 这两个主要的内部组件，其中：</p>
<ol>
<li>HealthMonitor：主要负责检测 NameNode 的健康状态，如果检测到 NameNode 的状态发生变化，会回调 ZKFailoverController 的相应方法进行自动的主备选举；</li>
<li>ActiveStandbyElector：主要负责完成自动的主备选举，内部封装了 Zookeeper 的处理逻辑，一旦 Zookeeper 主备选举完成，会回调 ZKFailoverController 的相应方法来进行 NameNode 的主备状态切换。</li>
</ol>
<h3><a id="_69"></a>自动触发主备选举</h3>
<p>NameNode 在选举成功后，会在 zk 上创建了一个 /hadoop-ha/${dfs.nameservices}/ActiveStandbyElectorLock 节点，而没有选举成功的备 NameNode 会监控这个节点，通过 Watcher 来监听这个节点的状态变化事件，ZKFC 的 ActiveStandbyElector 主要关注这个节点的 NodeDeleted 事件（这部分实现跟 Kafka 中 Controller 的选举一样）。</p>
<p>如果 Active NameNode 对应的 HealthMonitor 检测到 NameNode 的状态异常时， ZKFailoverController 会主动删除当前在 Zookeeper 上建立的临时节点 /hadoop-ha/<span class="katex--inline"><span class="katex"><span class="katex-mathml"><math><semantics><mrow><mrow><mi>d</mi><mi>f</mi><mi>s</mi><mi mathvariant="normal">.</mi><mi>n</mi><mi>a</mi><mi>m</mi><mi>e</mi><mi>s</mi><mi>e</mi><mi>r</mi><mi>v</mi><mi>i</mi><mi>c</mi><mi>e</mi><mi>s</mi></mrow><mi mathvariant="normal">/</mi><mi>A</mi><mi>c</mi><mi>t</mi><mi>i</mi><mi>v</mi><mi>e</mi><mi>S</mi><mi>t</mi><mi>a</mi><mi>n</mi><mi>d</mi><mi>b</mi><mi>y</mi><mi>E</mi><mi>l</mi><mi>e</mi><mi>c</mi><mi>t</mi><mi>o</mi><mi>r</mi><mi>L</mi><mi>o</mi><mi>c</mi><mi>k</mi><mi mathvariant="normal">，</mi><mi mathvariant="normal">这</mi><mi mathvariant="normal">样</mi><mi mathvariant="normal">处</mi><mi mathvariant="normal">于</mi><mi>S</mi><mi>t</mi><mi>a</mi><mi>n</mi><mi>d</mi><mi>b</mi><mi>y</mi><mi mathvariant="normal">状</mi><mi mathvariant="normal">态</mi><mi mathvariant="normal">的</mi><mi>N</mi><mi>a</mi><mi>m</mi><mi>e</mi><mi>N</mi><mi>o</mi><mi>d</mi><mi>e</mi><mi mathvariant="normal">的</mi><mi>A</mi><mi>c</mi><mi>t</mi><mi>i</mi><mi>v</mi><mi>e</mi><mi>S</mi><mi>t</mi><mi>a</mi><mi>n</mi><mi>d</mi><mi>b</mi><mi>y</mi><mi>E</mi><mi>l</mi><mi>e</mi><mi>c</mi><mi>t</mi><mi>o</mi><mi>r</mi><mi mathvariant="normal">注</mi><mi mathvariant="normal">册</mi><mi mathvariant="normal">的</mi><mi mathvariant="normal">监</mi><mi mathvariant="normal">听</mi><mi mathvariant="normal">器</mi><mi mathvariant="normal">就</mi><mi mathvariant="normal">会</mi><mi mathvariant="normal">收</mi><mi mathvariant="normal">到</mi><mi mathvariant="normal">这</mi><mi mathvariant="normal">个</mi><mi mathvariant="normal">节</mi><mi mathvariant="normal">点</mi><mi mathvariant="normal">的</mi><mi>N</mi><mi>o</mi><mi>d</mi><mi>e</mi><mi>D</mi><mi>e</mi><mi>l</mi><mi>e</mi><mi>t</mi><mi>e</mi><mi>d</mi><mi mathvariant="normal">事</mi><mi mathvariant="normal">件</mi><mi mathvariant="normal">。</mi><mi mathvariant="normal">收</mi><mi mathvariant="normal">到</mi><mi mathvariant="normal">这</mi><mi mathvariant="normal">个</mi><mi mathvariant="normal">事</mi><mi mathvariant="normal">件</mi><mi mathvariant="normal">之</mi><mi mathvariant="normal">后</mi><mi mathvariant="normal">，</mi><mi mathvariant="normal">会</mi><mi mathvariant="normal">马</mi><mi mathvariant="normal">上</mi><mi mathvariant="normal">再</mi><mi mathvariant="normal">次</mi><mi mathvariant="normal">进</mi><mi mathvariant="normal">入</mi><mi mathvariant="normal">到</mi><mi mathvariant="normal">创</mi><mi mathvariant="normal">建</mi><mi mathvariant="normal">/</mi><mi>h</mi><mi>a</mi><mi>d</mi><mi>o</mi><mi>o</mi><mi>p</mi><mo>−</mo><mi>h</mi><mi>a</mi><mi mathvariant="normal">/</mi></mrow><annotation encoding="application/x-tex">{dfs.nameservices}/ActiveStandbyElectorLock，这样处于 Standby 状态的 NameNode 的 ActiveStandbyElector 注册的监听器就会收到这个节点的 NodeDeleted 事件。收到这个事件之后，会马上再次进入到创建 /hadoop-ha/</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height: 1em; vertical-align: -0.25em;"></span><span class="mord"><span class="mord mathit">d</span><span class="mord mathit" style="margin-right: 0.10764em;">f</span><span class="mord mathit">s</span><span class="mord">.</span><span class="mord mathit">n</span><span class="mord mathit">a</span><span class="mord mathit">m</span><span class="mord mathit">e</span><span class="mord mathit">s</span><span class="mord mathit">e</span><span class="mord mathit" style="margin-right: 0.02778em;">r</span><span class="mord mathit" style="margin-right: 0.03588em;">v</span><span class="mord mathit">i</span><span class="mord mathit">c</span><span class="mord mathit">e</span><span class="mord mathit">s</span></span><span class="mord">/</span><span class="mord mathit">A</span><span class="mord mathit">c</span><span class="mord mathit">t</span><span class="mord mathit">i</span><span class="mord mathit" style="margin-right: 0.03588em;">v</span><span class="mord mathit">e</span><span class="mord mathit" style="margin-right: 0.05764em;">S</span><span class="mord mathit">t</span><span class="mord mathit">a</span><span class="mord mathit">n</span><span class="mord mathit">d</span><span class="mord mathit">b</span><span class="mord mathit" style="margin-right: 0.03588em;">y</span><span class="mord mathit" style="margin-right: 0.05764em;">E</span><span class="mord mathit" style="margin-right: 0.01968em;">l</span><span class="mord mathit">e</span><span class="mord mathit">c</span><span class="mord mathit">t</span><span class="mord mathit">o</span><span class="mord mathit" style="margin-right: 0.02778em;">r</span><span class="mord mathit">L</span><span class="mord mathit">o</span><span class="mord mathit">c</span><span class="mord mathit" style="margin-right: 0.03148em;">k</span><span class="mord cjk_fallback">，</span><span class="mord cjk_fallback">这</span><span class="mord cjk_fallback">样</span><span class="mord cjk_fallback">处</span><span class="mord cjk_fallback">于</span><span class="mord mathit" style="margin-right: 0.05764em;">S</span><span class="mord mathit">t</span><span class="mord mathit">a</span><span class="mord mathit">n</span><span class="mord mathit">d</span><span class="mord mathit">b</span><span class="mord mathit" style="margin-right: 0.03588em;">y</span><span class="mord cjk_fallback">状</span><span class="mord cjk_fallback">态</span><span class="mord cjk_fallback">的</span><span class="mord mathit" style="margin-right: 0.10903em;">N</span><span class="mord mathit">a</span><span class="mord mathit">m</span><span class="mord mathit">e</span><span class="mord mathit" style="margin-right: 0.10903em;">N</span><span class="mord mathit">o</span><span class="mord mathit">d</span><span class="mord mathit">e</span><span class="mord cjk_fallback">的</span><span class="mord mathit">A</span><span class="mord mathit">c</span><span class="mord mathit">t</span><span class="mord mathit">i</span><span class="mord mathit" style="margin-right: 0.03588em;">v</span><span class="mord mathit">e</span><span class="mord mathit" style="margin-right: 0.05764em;">S</span><span class="mord mathit">t</span><span class="mord mathit">a</span><span class="mord mathit">n</span><span class="mord mathit">d</span><span class="mord mathit">b</span><span class="mord mathit" style="margin-right: 0.03588em;">y</span><span class="mord mathit" style="margin-right: 0.05764em;">E</span><span class="mord mathit" style="margin-right: 0.01968em;">l</span><span class="mord mathit">e</span><span class="mord mathit">c</span><span class="mord mathit">t</span><span class="mord mathit">o</span><span class="mord mathit" style="margin-right: 0.02778em;">r</span><span class="mord cjk_fallback">注</span><span class="mord cjk_fallback">册</span><span class="mord cjk_fallback">的</span><span class="mord cjk_fallback">监</span><span class="mord cjk_fallback">听</span><span class="mord cjk_fallback">器</span><span class="mord cjk_fallback">就</span><span class="mord cjk_fallback">会</span><span class="mord cjk_fallback">收</span><span class="mord cjk_fallback">到</span><span class="mord cjk_fallback">这</span><span class="mord cjk_fallback">个</span><span class="mord cjk_fallback">节</span><span class="mord cjk_fallback">点</span><span class="mord cjk_fallback">的</span><span class="mord mathit" style="margin-right: 0.10903em;">N</span><span class="mord mathit">o</span><span class="mord mathit">d</span><span class="mord mathit">e</span><span class="mord mathit" style="margin-right: 0.02778em;">D</span><span class="mord mathit">e</span><span class="mord mathit" style="margin-right: 0.01968em;">l</span><span class="mord mathit">e</span><span class="mord mathit">t</span><span class="mord mathit">e</span><span class="mord mathit">d</span><span class="mord cjk_fallback">事</span><span class="mord cjk_fallback">件</span><span class="mord cjk_fallback">。</span><span class="mord cjk_fallback">收</span><span class="mord cjk_fallback">到</span><span class="mord cjk_fallback">这</span><span class="mord cjk_fallback">个</span><span class="mord cjk_fallback">事</span><span class="mord cjk_fallback">件</span><span class="mord cjk_fallback">之</span><span class="mord cjk_fallback">后</span><span class="mord cjk_fallback">，</span><span class="mord cjk_fallback">会</span><span class="mord cjk_fallback">马</span><span class="mord cjk_fallback">上</span><span class="mord cjk_fallback">再</span><span class="mord cjk_fallback">次</span><span class="mord cjk_fallback">进</span><span class="mord cjk_fallback">入</span><span class="mord cjk_fallback">到</span><span class="mord cjk_fallback">创</span><span class="mord cjk_fallback">建</span><span class="mord">/</span><span class="mord mathit">h</span><span class="mord mathit">a</span><span class="mord mathit">d</span><span class="mord mathit">o</span><span class="mord mathit">o</span><span class="mord mathit">p</span><span class="mspace" style="margin-right: 0.222222em;"></span><span class="mbin">−</span><span class="mspace" style="margin-right: 0.222222em;"></span></span><span class="base"><span class="strut" style="height: 1em; vertical-align: -0.25em;"></span><span class="mord mathit">h</span><span class="mord mathit">a</span><span class="mord">/</span></span></span></span></span>{dfs.nameservices}/ActiveStandbyElectorLock 节点的流程，如果创建成功，这个本来处于 Standby 状态的 NameNode 就选举为主 NameNode 并随后开始切换为 Active 状态。</p>
<p>当然，如果是 Active 状态的 NameNode 所在的机器整个宕掉的话，那么根据 Zookeeper 的临时节点特性，/hadoop-ha/${dfs.nameservices}/ActiveStandbyElectorLock 节点会自动被删除，从而也会自动进行一次主备切换。</p>
<h2><a id="HDFS__75"></a>HDFS 脑裂问题</h2>
<p>在实际中，NameNode 可能会出现这种情况，NameNode 在垃圾回收（GC）时，可能会在长时间内整个系统无响应，因此，也就无法向 zk 写入心跳信息，这样的话可能会导致临时节点掉线，备 NameNode 会切换到 Active 状态，这种情况，可能会导致整个集群会有同时有两个 NameNode，这就是脑裂问题。</p>
<p>脑裂问题的解决方案是隔离（Fencing），主要是在以下三处采用隔离措施：</p>
<ol>
<li>第三方共享存储：任一时刻，只有一个 NN 可以写入；</li>
<li>DataNode：需要保证只有一个 NN 发出与管理数据副本有关的删除命令；</li>
<li>Client：需要保证同一时刻只有一个 NN 能够对 Client 的请求发出正确的响应。</li>
</ol>
<p>关于这个问题目前解决方案的实现如下：</p>
<ol>
<li>ActiveStandbyElector 为了实现 fencing，会在成功创建 Zookeeper 节点 hadoop-ha/<span class="katex--inline"><span class="katex"><span class="katex-mathml"><math><semantics><mrow><mrow><mi>d</mi><mi>f</mi><mi>s</mi><mi mathvariant="normal">.</mi><mi>n</mi><mi>a</mi><mi>m</mi><mi>e</mi><mi>s</mi><mi>e</mi><mi>r</mi><mi>v</mi><mi>i</mi><mi>c</mi><mi>e</mi><mi>s</mi></mrow><mi mathvariant="normal">/</mi><mi>A</mi><mi>c</mi><mi>t</mi><mi>i</mi><mi>v</mi><mi>e</mi><mi>S</mi><mi>t</mi><mi>a</mi><mi>n</mi><mi>d</mi><mi>b</mi><mi>y</mi><mi>E</mi><mi>l</mi><mi>e</mi><mi>c</mi><mi>t</mi><mi>o</mi><mi>r</mi><mi>L</mi><mi>o</mi><mi>c</mi><mi>k</mi><mi mathvariant="normal">从</mi><mi mathvariant="normal">而</mi><mi mathvariant="normal">成</mi><mi mathvariant="normal">为</mi><mi>A</mi><mi>c</mi><mi>t</mi><mi>i</mi><mi>v</mi><mi>e</mi><mi>N</mi><mi>a</mi><mi>m</mi><mi>e</mi><mi>N</mi><mi>o</mi><mi>d</mi><mi>e</mi><mi mathvariant="normal">之</mi><mi mathvariant="normal">后</mi><mi mathvariant="normal">，</mi><mi mathvariant="normal">创</mi><mi mathvariant="normal">建</mi><mi mathvariant="normal">另</mi><mi mathvariant="normal">外</mi><mi mathvariant="normal">一</mi><mi mathvariant="normal">个</mi><mi mathvariant="normal">路</mi><mi mathvariant="normal">径</mi><mi mathvariant="normal">为</mi><mi mathvariant="normal">/</mi><mi>h</mi><mi>a</mi><mi>d</mi><mi>o</mi><mi>o</mi><mi>p</mi><mo>−</mo><mi>h</mi><mi>a</mi><mi mathvariant="normal">/</mi></mrow><annotation encoding="application/x-tex">{dfs.nameservices}/ActiveStandbyElectorLock 从而成为 Active NameNode 之后，创建另外一个路径为 /hadoop-ha/</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height: 1em; vertical-align: -0.25em;"></span><span class="mord"><span class="mord mathit">d</span><span class="mord mathit" style="margin-right: 0.10764em;">f</span><span class="mord mathit">s</span><span class="mord">.</span><span class="mord mathit">n</span><span class="mord mathit">a</span><span class="mord mathit">m</span><span class="mord mathit">e</span><span class="mord mathit">s</span><span class="mord mathit">e</span><span class="mord mathit" style="margin-right: 0.02778em;">r</span><span class="mord mathit" style="margin-right: 0.03588em;">v</span><span class="mord mathit">i</span><span class="mord mathit">c</span><span class="mord mathit">e</span><span class="mord mathit">s</span></span><span class="mord">/</span><span class="mord mathit">A</span><span class="mord mathit">c</span><span class="mord mathit">t</span><span class="mord mathit">i</span><span class="mord mathit" style="margin-right: 0.03588em;">v</span><span class="mord mathit">e</span><span class="mord mathit" style="margin-right: 0.05764em;">S</span><span class="mord mathit">t</span><span class="mord mathit">a</span><span class="mord mathit">n</span><span class="mord mathit">d</span><span class="mord mathit">b</span><span class="mord mathit" style="margin-right: 0.03588em;">y</span><span class="mord mathit" style="margin-right: 0.05764em;">E</span><span class="mord mathit" style="margin-right: 0.01968em;">l</span><span class="mord mathit">e</span><span class="mord mathit">c</span><span class="mord mathit">t</span><span class="mord mathit">o</span><span class="mord mathit" style="margin-right: 0.02778em;">r</span><span class="mord mathit">L</span><span class="mord mathit">o</span><span class="mord mathit">c</span><span class="mord mathit" style="margin-right: 0.03148em;">k</span><span class="mord cjk_fallback">从</span><span class="mord cjk_fallback">而</span><span class="mord cjk_fallback">成</span><span class="mord cjk_fallback">为</span><span class="mord mathit">A</span><span class="mord mathit">c</span><span class="mord mathit">t</span><span class="mord mathit">i</span><span class="mord mathit" style="margin-right: 0.03588em;">v</span><span class="mord mathit">e</span><span class="mord mathit" style="margin-right: 0.10903em;">N</span><span class="mord mathit">a</span><span class="mord mathit">m</span><span class="mord mathit">e</span><span class="mord mathit" style="margin-right: 0.10903em;">N</span><span class="mord mathit">o</span><span class="mord mathit">d</span><span class="mord mathit">e</span><span class="mord cjk_fallback">之</span><span class="mord cjk_fallback">后</span><span class="mord cjk_fallback">，</span><span class="mord cjk_fallback">创</span><span class="mord cjk_fallback">建</span><span class="mord cjk_fallback">另</span><span class="mord cjk_fallback">外</span><span class="mord cjk_fallback">一</span><span class="mord cjk_fallback">个</span><span class="mord cjk_fallback">路</span><span class="mord cjk_fallback">径</span><span class="mord cjk_fallback">为</span><span class="mord">/</span><span class="mord mathit">h</span><span class="mord mathit">a</span><span class="mord mathit">d</span><span class="mord mathit">o</span><span class="mord mathit">o</span><span class="mord mathit">p</span><span class="mspace" style="margin-right: 0.222222em;"></span><span class="mbin">−</span><span class="mspace" style="margin-right: 0.222222em;"></span></span><span class="base"><span class="strut" style="height: 1em; vertical-align: -0.25em;"></span><span class="mord mathit">h</span><span class="mord mathit">a</span><span class="mord">/</span></span></span></span></span>{dfs.nameservices}/ActiveBreadCrumb 的持久节点，这个节点里面保存了这个 Active NameNode 的地址信息；</li>
<li>Active NameNode 的 ActiveStandbyElector 在正常的状态下关闭 Zookeeper Session 的时候，会一起删除这个持久节点；</li>
<li>但如果 ActiveStandbyElector 在异常的状态下 Zookeeper Session 关闭 (比如前述的 Zookeeper 假死)，那么由于 /hadoop-ha/${dfs.nameservices}/ActiveBreadCrumb 是持久节点，会一直保留下来，后面当另一个 NameNode 选主成功之后，会注意到上一个 Active NameNode 遗留下来的这个节点，从而会回调 ZKFailoverController 的方法对旧的 Active NameNode 进行 fencing。</li>
</ol>
<p>在进行 fencing 的时候，会执行以下的操作：</p>
<ol>
<li>首先尝试调用这个旧 Active NameNode 的 HAServiceProtocol RPC 接口的transitionToStandby方法，看能不能把它转换为 Standby 状态；</li>
<li>如果transitionToStandby方法调用失败，那么就执行 Hadoop 配置文件之中预定义的隔离措施。</li>
</ol>
<p>Hadoop 目前主要提供两种隔离措施，通常会选择第一种：</p>
<ol>
<li>sshfence：通过 SSH 登录到目标机器上，执行命令 fuser 将对应的进程杀死；</li>
<li>shellfence：执行一个用户自定义的 shell 脚本来将对应的进程隔离。<br>
只有在成功地执行完成 fencing 之后，选主成功的 ActiveStandbyElector 才会回调 ZKFailoverController 的becomeActive方法将对应的 NameNode 转换为 Active 状态，开始对外提供服务。</li>
</ol>
<p>NameNode 选举的实现机制与 Kafka 的 Controller 类似，那么 Kafka 是如何避免脑裂问题的呢？</p>
<ol>
<li>Controller 给 Broker 发送的请求中，都会携带 controller epoch 信息，如果 broker 发现当前请求的 epoch 小于缓存中的值，那么就证明这是来自旧 Controller 的请求，就会决绝这个请求，正常情况下是没什么问题的；</li>
<li>但是异常情况下呢？如果 Broker 先收到异常 Controller 的请求进行处理呢？现在看 Kafka 在这一部分并没有适合的方案；</li>
<li>正常情况下，Kafka 新的 Controller 选举出来之后，Controller 会向全局所有 broker 发送一个 metadata 请求，这样全局所有 Broker 都可以知道当前最新的 controller epoch，但是并不能保证可以完全避免上面这个问题，还是有出现这个问题的几率的，只不过非常小，而且即使出现了由于 Kafka 的高可靠架构，影响也非常有限，至少从目前看，这个问题并不是严重的问题。</li>
</ol>
<h2><a id="_104"></a>第三方存储（共享存储）</h2>
<p>上述 HA 方案还有一个明显缺点，那就是第三方存储节点有可能失效，之前有很多共享存储的实现方案，目前社区已经把由 Clouderea 公司实现的基于 QJM 的方案合并到 HDFS 的 trunk 之中并且作为默认的共享存储实现，本部分只针对基于 QJM 的共享存储方案的内部实现原理进行分析。</p>
<p>QJM（Quorum Journal Manager）本质上是利用 Paxos 协议来实现的，QJM 在2F+1个 JournalNode 上存储 NN 的 editlog，每次写入操作都通过 Paxos 保证写入的一致性，它最多可以允许有 F 个 JournalNode 节点同时故障，其实现如下：<br>
<img src="https://img-blog.csdnimg.cn/20181119141327175.png" alt="在这里插入图片描述"><br>
Active NameNode 首先把 EditLog 提交到 JournalNode 集群，然后 Standby NameNode 再从 JournalNode 集群定时同步 EditLog。</p>
<p>还有一点需要注意的是，在 2.0 中不再有 SNN 这个角色了，NameNode 在启动后，会先加载 FSImage 文件和共享目录上的 EditLog Segment 文件，之后 NameNode 会启动 EditLogTailer 线程和 StandbyCheckpointer 线程，正式进入 Standby 模式，其中：</p>
<ol>
<li>EditLogTailer 线程的作用是定时从 JournalNode 集群上同步 EditLog；</li>
<li>StandbyCheckpointer 线程的作用其实是为了替代 Hadoop 1.x 版本之中的 Secondary NameNode 的功能，StandbyCheckpointer 线程会在 Standby NameNode 节点上定期进行 Checkpoint，将 Checkpoint 之后的 FSImage 文件上传到 Active NameNode 节点。</li>
</ol>
<h2><a id="HDFS_20_Federation__115"></a>HDFS 2.0 Federation 实现</h2>
<p>在 1.0 中，HDFS 的架构设计有以下缺点：</p>
<ol>
<li>namespace 扩展性差：在单一的 NN 情况下，因为所有 namespace 数据都需要加载到内存，所以物理机内存的大小限制了整个 HDFS 能够容纳文件的最大个数（namespace 指的是 HDFS 中树形目录和文件结构以及文件对应的 block 信息）；</li>
<li>性能可扩展性差：由于所有请求都需要经过 NN，单一 NN 导致所有请求都由一台机器进行处理，很容易达到单台机器的吞吐；</li>
<li>隔离性差：多租户的情况下，单一 NN 的架构无法在租户间进行隔离，会造成不可避免的相互影响。<br>
而 Federation 的设计就是为了解决这些问题，采用 Federation 的最主要原因是设计实现简单，而且还能解决问题。</li>
</ol>
<h3><a id="Federation__121"></a>Federation 架构</h3>
<p>Federation 的架构设计如下图所示：<br>
<img src="https://img-blog.csdnimg.cn/20181119141507307.png" alt="在这里插入图片描述"></p>
<h3><a id="Federation__124"></a>Federation 的核心设计思想</h3>
<p>Federation 的核心思想是将一个大的 namespace 划分多个子 namespace，并且每个 namespace 分别由单独的 NameNode 负责，这些 NameNode 之间互相独立，不会影响，不需要做任何协调工作（其实跟拆集群有一些相似），集群的所有 DataNode 会被多个 NameNode 共享。</p>
<p>其中，每个子 namespace 和 DataNode 之间会由数据块管理层作为中介建立映射关系，数据块管理层由若干数据块池（Pool）构成，每个数据块只会唯一属于某个固定的数据块池，而一个子 namespace 可以对应多个数据块池。每个 DataNode 需要向集群中所有的 NameNode 注册，且周期性地向所有 NameNode 发送心跳和块报告，并执行来自所有 NameNode 的命令。</p>
<ul>
<li>一个 block pool 由属于同一个 namespace 的数据块组成，每个 DataNode 可能会存储集群中所有 block pool 的数据块；</li>
<li>每个 block pool 内部自治，也就是说各自管理各自的 block，不会与其他 block pool 交流，如果一个 NameNode 挂掉了，不会影响其他 NameNode;</li>
<li>某个 NameNode 上的 namespace 和它对应的 block pool 一起被称为 namespace volume，它是管理的基本单位。当一个 NameNode/namespace 被删除后，其所有 DataNode 上对应的 block pool 也会被删除，当集群升级时，每个 namespace volume 可以作为一个基本单元进行升级。</li>
</ul>
<p>到这里，基本对 HDFS 这部分总结完了，后续结合公司 HDFS 团队的 CaseStudy 深入学习这部分的内容，工作中，也慢慢感觉到分布式系统，很多的设计实现与问题解决方案都很类似，只不过因为面对业务场景的不同而采用了不同的实现。</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>