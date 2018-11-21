---
layout:     post
title:      hbase学习笔记（1）——hbase体系结构
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 id="WizKMOutline_1355539505096664"><span style="font-family:Arial;font-size:14px;">HBase体系结构</span></h1>
<div><span style="font-family:Arial;font-size:14px;">HBase的服务器体系结构遵从简单的<span style="color:#ff0000;">主从服务器架构</span>，它由<span style="color:#ff0000;">HRegion Server群</span>和<span style="color:#ff0000;">HBase Master Server</span>构成。</span></div>
<div><span style="font-family:Arial;font-size:14px;"><br></span></div>
<div></div>
<div><span style="font-family:Arial;font-size:14px;">HBase Master Server负责管理所有的HRegion Server，而HBase中所有的服务器都是通过ZooKeeper来进行协调并处理HBase服务器运行期间可能遇到的错误。</span></div>
<div><span style="font-family:Arial;font-size:14px;"><br></span></div>
<div></div>
<div><span style="font-family:Arial;font-size:14px;">HBase Master本身并<span style="color:#ff0000;">不存储HBase中的任何数据</span>，HBase逻辑上的表可能会被划分成多个HRegion，然后存储到HRegion服务器群中。</span></div>
<div><span style="font-family:Arial;font-size:14px;"><br></span></div>
<div></div>
<div><span style="font-family:Arial;font-size:14px;">HBase Master服务器中存储的是从<span style="color:#ff0000;">数据到HRegion服务器的映射</span>。</span></div>
<div><span style="font-family:Arial;font-size:14px;"><br></span></div>
<div></div>
<div><span style="font-family:Arial;font-size:14px;">HBase体系结构如下图：</span></div>
<h2 id="WizKMOutline_1355539505096404"><span style="font-family:Arial;font-size:14px;">HRegion</span></h2>
<div><span style="font-family:Arial;font-size:14px;">当表的大小超过设置值的时候，<span style="color:#ff0000;">HBase会自动将表划分为不同的区域</span>，每个区域包含<span style="color:#ff0000;">所有行的一个子集</span>。对用户来说，每个表是一堆数据的集合，靠<span style="color:#ff0000;">主键来区分</span>。从物理上来说，一张<span style="color:#ff0000;">表是被拆分成多块</span>，每块就是一个HRegion。</span></div>
<div><span style="font-family:Arial;font-size:14px;"><br></span></div>
<div></div>
<div><span style="font-family:Arial;font-size:14px;">我们用<span style="color:#ff0000;">表名+开始/结束</span>主键来区分每一个HRegion。一个HRegion会保存一个表里面某段连续的数据，从开始主键到结束主键，一张完整的表格是保存在<span style="color:#ff0000;">多个HRegion</span>上面的。</span></div>
<div></div>
<h2 id="WizKMOutline_1355539505096522"><span style="font-family:Arial;font-size:14px;">HRegion服务器</span></h2>
<div><span style="font-family:Arial;font-size:14px;">所有的数据库数据一般是保存在<span style="color:#ff0000;">Hadoop分布式文件系统（HDFS）</span>上面的，用户通过一系列HRegion服务器获取这些数据。<span style="color:#ff0000;">一台机器上一般只运行一个HRegion服务器</span>，而且每一个区段的HRegion也只会被一个HRegion服务器维护。</span></div>
<div><span style="font-family:Arial;font-size:14px;"><br></span></div>
<div></div>
<div><span style="font-family:Arial;font-size:14px;">HRegion服务器包括两大部分：HLOG部分和HRegion部分。</span></div>
<div><span style="font-family:Arial;font-size:14px;"><br></span></div>
<div></div>
<div><span style="font-family:Arial;font-size:14px;">其中HLOG用来<span style="color:#ff0000;">存储数据日志</span>，采用的是<span style="color:#ff0000;">先写日志的方式</span>（Write-ahead log）。</span></div>
<div><span style="font-family:Arial;font-size:14px;"><br></span></div>
<div></div>
<div><span style="font-family:Arial;font-size:14px;">HRegion部分由很多的HRegion组成，存储的是实际的数据。每一个HRegion又由很多的Store组成，<span style="color:#ff0000;">每一个Store存储的实际上是一个列族（ColumnFamily）下的数据</span>。</span></div>
<div><span style="font-family:Arial;font-size:14px;"><br></span></div>
<div></div>
<div><span style="font-family:Arial;font-size:14px;">此外，<span style="color:#ff0000;">每一个HSore中包含一块MemStore</span>。MemStore驻留在内存中，数据来到时<span style="color:#ff0000;">首先更新到MemStore</span>中，当到达阀值之后再更新到对应的StoreFile（又名HFile）中。</span></div>
<div><br></div>
<div></div>
<div><span style="font-family:Arial;font-size:14px;">每一个Store包含了多个StoreFile，StoreFile负责的是实际的数据存储，为<span style="color:#ff0000;">HBase中最小的存储单元</span>。</span></div>
<div><span style="font-family:Arial;font-size:14px;"><br></span></div>
<div></div>
<div><span style="font-family:Arial;font-size:14px;">HBase中不涉及<span style="color:#ff0000;">数据的直接删除和更新操作</span>，<span style="color:#ff0000;">所有的数据均通过追加的方式进行更新</span>。数据的删除和更新在HBase合并（compact）的时候进行。当Store中StoreFile的数量超过设定的<span style="color:#ff0000;">阀值</span>时将<span style="color:#cc0000;">触发合并</span>操作，该操作会把<span style="color:#ff0000;">多个StoreFile文件合并成一个StoreFile</span>。</span></div>
<div><span style="font-family:Arial;font-size:14px;"><br></span></div>
<div></div>
<div><span style="font-family:Arial;font-size:14px;">当用户需要更新数据的时候，数据会被分配到对应的HRegion服务器上提交修改。<span style="color:#ff0000;">数据首先被提交到HLOG文件里面</span>，在写入HLOG之后，commit()调用才会将其返回给客户端。HLOG文件用于故障恢复。例如某一台HRegionServer发生故障时，那么它所维护的HRegion会被重新分配到新的机器上。这时HLOG会按照HRegion进行划分。新的机器在加载HRegion的时候可以通过HLOG对数据进行恢复。</span></div>
<div><span style="font-family:Arial;font-size:14px;"><br></span></div>
<div></div>
<div><span style="font-family:Arial;font-size:14px;">当一个HRegion变得过于巨大、超过了设定的阀值时，HRegion服务器会调用HRegion.closeAndSplit()，将此HRegion拆分为两个，并且报告主服务器让它决定由哪台HRegion服务器来存放新的HRegion。</span></div>
<div><span style="font-family:Arial;font-size:14px;"><br></span></div>
<div></div>
<div><span style="font-family:Arial;font-size:14px;">这个拆分过程十分迅速，因为两个新的HRegion最初只是保留原来HRegionFile文件的引用。这时旧的HRegion会处于停止服务的状态，当新的HRegion拆分完成并且把引用删除了以后，旧的HRegion才会删除。</span></div>
<div><span style="font-family:Arial;font-size:14px;"><br></span></div>
<div><span style="font-family:Arial;font-size:14px;">另外，两个HRegion可以通过调用HRegion.closeAndMerge()合并成一个新的HRegion，当前版本下进行此操作需要两台HRegion服务器都停机。</span></div>
<div><span style="font-family:Arial;font-size:14px;"><br></span></div>
<div></div>
<h2 id="WizKMOutline_1355539505096885"><span style="font-family:Arial;font-size:14px;">HBase Master服务器</span></h2>
<div><span style="font-family:Arial;font-size:14px;">每台HRegion服务器都会和HMaster服务器通信，HMaster的主要任务就是<span style="color:#ff0000;">告诉每个HRegion服务器它要维护哪些HRegion</span>。</span></div>
<div><span style="font-family:Arial;font-size:14px;"><br></span></div>
<div></div>
<div><span style="font-family:Arial;font-size:14px;">当一台新的HRegion服务器登录到HMaster服务器时，HMaster会告诉它先等待分配数据。当一台HRegion死机时，HMaster会把它负责的HRegion标记为未分配，然后再把它们分配到其它HRegion服务器中。</span></div>
<div><span style="font-family:Arial;font-size:14px;"><br></span></div>
<div></div>
<div><span style="font-family:Arial;font-size:14px;">如果当前HBase已经解决了之前存在的SPFO（单点故障），并且HBase总可以启动多个HMaster，那么它就能通过Zookeeper来保证系统中总有一个Master在运行。</span></div>
<div><span style="font-family:Arial;font-size:14px;"><br></span></div>
<div></div>
<div><span style="font-family:Arial;font-size:14px;">HMaster在功能上主要负责Table和HRegion的管理工作，具体包括：</span></div>
<ul><li><span style="font-family:Arial;font-size:14px;">管理用户对table的增、删、改、查操作； </span>
</li><li><span style="font-family:Arial;font-size:14px;">管理HRegion服务器的负载均衡，调整HRegion分布；
</span></li><li><span style="font-family:Arial;font-size:14px;">在HRegion分裂后，负责新HRegion的分配； </span>
</li><li><span style="font-family:Arial;font-size:14px;">在HRegion服务器停机后，负责失效HRegion服务器上的HRegion迁移。</span></li></ul><h2 id="WizKMOutline_1355539505096988"><span style="font-family:Arial;font-size:14px;">ROOT表和META表</span></h2>
<div><span style="font-family:Arial;font-size:14px;">HRegion是按照表名和主键范围来区分，由于主键范围是连续的，所以一般用开始主键就可以表示相应的HRegion。</span></div>
<div></div>
<div><span style="font-family:Arial;font-size:14px;">但是，因为我们有<span style="color:#ff0000;">合并和分割</span>操作，如果正好在执行这些操作的过程中出现<span style="color:#ff0000;">死机</span>，那么就可能存在多份表名和开始主键相同的数据，这样的话只有开始主键就不够了，这就需要通过HBase的元数据信息来区分那一份才是正确的数据文件，<span style="color:#ff0000;">为了区分这样的情况，每个HRegion都有一个‘regionId’来标识它的唯一性</span>。</span></div>
<div><span style="font-family:Arial;font-size:14px;"><br></span></div>
<div></div>
<div><span style="font-family:Arial;font-size:14px;">所以，一个HRegion的唯一标识为：<span style="color:#ff0000;">表名+开始主键+唯一ID（tableName+startKey+regionId）。</span>通过这个标识符来区分不同的HRegion，这些数据就是元数据（META），而元数据本身也是被保存在HRegion里面的，<span style="color:#ff0000;">所以我们称这个表为元数据表（META Table），里面保存的就是HRegion标识符和实际HRegion服务器的映射关系</span>。</span></div>
<div><span style="font-family:Arial;font-size:14px;"><br></span></div>
<div></div>
<div><span style="font-family:Arial;font-size:14px;">META表也会增长，并且可能被分割成几个HRegion，为了定位这些HRegion，我们采用一个ROOT表，它保存了所有META表的位置，且<span style="color:#ff0000;">ROOT表是不能被分割的，永远只存在一个HRegion</span>。</span></div>
<div><span style="font-family:Arial;font-size:14px;"><br></span></div>
<div></div>
<div><span style="font-family:Arial;font-size:14px;">在HBase启动的时候，HMaster就会扫描ROOT表，因为这个表只会有一个HRegion，所以这个HRegion的名字是被写死的。当然要把ROOT表分配到一个HRegion服务器中需要一定的时间。</span></div>
<div><span style="font-family:Arial;font-size:14px;"><br></span></div>
<div></div>
<div><span style="font-family:Arial;font-size:14px;">当ROOT表被分配好之后，HMaster就会扫描ROOT表，获取META表的名字和位置，然后把META表分配到不同的HRegion服务器中，最后就是扫描META表，找到所有HRegion区域的信息，把它们分配给不同的HRegion服务器。</span></div>
<div><span style="font-family:Arial;font-size:14px;"><br></span></div>
<div></div>
<div><span style="font-family:Arial;font-size:14px;">HMaster在内存中保存着当前<span style="color:#ff0000;">活跃的HRegion</span>服务器的数据，因此如果HMaster死机，整个系统也就无法访问了，这时服务器的信息也就必要保存到文件里面了。</span></div>
<div><span style="font-family:Arial;font-size:14px;"><br></span></div>
<div></div>
<div><span style="font-family:Arial;font-size:14px;">ROOT表和META表的每一行都包含一个列族（info列族）：</span></div>
<ul><li><span style="font-family:Arial;font-size:14px;">info:regioninfo 包含了一个串行化的HRegionInfo对象。
</span></li><li><span style="font-family:Arial;font-size:14px;">info:server 保存了一个字符串，是服务器的地址HServerAddress.toString()。
</span></li><li><span style="font-family:Arial;font-size:14px;">info:startcode 是一个长整型的数字字符串，它是在HRegion服务器启动的时候传给HMaster的，让HMaster确定这个HRegion服务器的信息有没有更改。</span></li></ul><div><span style="font-family:Arial;font-size:14px;">因此，当一个客户端拿到ROOT表地址后，就没有不要再连接HMaster了，HMaster的负载相对就小了很多。<span style="color:#ff0000;">它只会处理超时的HRegion服务器，并在启动的时候扫描ROOT表和META表，以及返回ROOT表的HRegion服务器地址</span>。</span></div>
<div><span style="font-family:Arial;font-size:14px;"><br></span></div>
<div></div>
<div><span style="font-family:Arial;font-size:14px;"><span style="color:#ff0000;">ROOT表包含<span style="background-color:#00ccff;">META表所在的区域列表</span>，META表包含所有<span style="background-color:#00ccff;">用户的空间区域列表</span>，以及HRegion服务器地址。客户端能够缓存所有已知的ROOT表和META表，从而提高访问的效率</span>。</span></div>
<div></div>
<h2 id="WizKMOutline_1355539505096473"><span style="font-family:Arial;font-size:14px;">ZooKeeper</span></h2>
<div><span style="font-family:Arial;font-size:14px;">ZooKeeper存储的是HBase中ROOT表和META表的位置。</span></div>
<div><span style="font-family:Arial;font-size:14px;"><br></span></div>
<div></div>
<div><span style="font-family:Arial;font-size:14px;">此外，ZooKeeper还负责监控各个机器的状态（每台机器到ZooKeeper中注册一个实例）。当某台及其发生故障的时，ZooKeeper会第一时间感知到，并通知HBase Master进行相应的处理。</span></div>
<div><span style="font-family:Arial;font-size:14px;"><br></span></div>
<div></div>
<div><span style="font-family:Arial;font-size:14px;">同时，当HBase Master发生故障的时候，ZooKeeper还负责HBase Master的恢复工作，能够保证在同一时刻系统中只有一台HBase Master提供服务。</span></div>
<div><span style="font-family:Arial;font-size:14px;"><br></span></div>
            </div>
                </div>