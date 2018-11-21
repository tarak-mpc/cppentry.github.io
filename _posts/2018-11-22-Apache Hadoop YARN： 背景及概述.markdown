---
layout:     post
title:      Apache Hadoop YARN： 背景及概述
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1>Apache Hadoop YARN： 背景及概述</h1>

<p>从2012年8月开始Apache Hadoop YARN（YARN = Yet Another Resource Negotiator）成了Apache Hadoop的一项子工程。自此Apache Hadoop由下面四个子工程组成：</p>

<ul><li>Hadoop Comon：核心库，为其他部分服务</li>
	<li>Hadoop HDFS：分布式存储系统</li>
	<li>Hadoop MapReduce：MapReduce模型的开源实现</li>
	<li>Hadoop YARN：新一代Hadoop数据处理框架</li>
</ul><p>      概括来说，Hadoop YARN的目的是使得Hadoop数据处理能力超越MapReduce。众所周知，Hadoop HDFS是Hadoop的数据存储层，Hadoop MapReduce是数据处理层。然而，MapReduce已经不能满足今天广泛的数据处理需求，如实时/准实时计算，图计算等。而Hadoop YARN提供了一个更加通用的资源管理和分布式应用框架。在这个框架上，用户可以根据自己需求，实现定制化的数据处理应用。而Hadoop MapReduce也是YARN上的一个应用。我们将会看到MPI，图处理，在线服务等，都会和Hadoop MapReduce一样成为YARN上的应用。下面将分别介绍传统的Hadoop MapReduce以及新一代Hadoop YARN架构。</p>

<h2><a name="t0"></a>传统的Apache Hadoop MapReduce架构</h2>

<p>       传统的Apache Hadoop MapReduce系统由JobTracker和TaskTracker组成。其中JobTracker是master，只有一个；TaskTracker是slaves，每个节点部署一个。</p>

<p><img alt="alt" class="has" height="382" src="http://hortonworks.com/wp-content/uploads/2012/08/MRArch.png" width="485"></p>

<p>图 1 Apache Hadoop MapReduce系统架构</p>

<p>       JobTracker负责资源管理（通过管理TaskTracker节点），追踪资源消费/释放，以及Job的生命周期管理（调度Job的每个Task，追踪Task进度，为Task提供容错等）。而TaskTracker的职责很简单，依次启动和停止由JobTracker分配的Task，并且周期性的向JobTracker汇报Task进度及状态信息。</p>

<h2><a name="t1"></a>Apache Hadoop YARN架构</h2>

<p>       YARN的最基本思想是将JobTracker的两个主要职责：资源管理和Job调度管理分别交给两个角色负责。一个是全局的ResourceManager，一个是每个应用一个的ApplicationMaster。ResourceManager以及每个节点一个的NodeManager构成了新的通用系统，实现以分布式方式管理应用。</p>

<p><img alt="alt" class="has" height="348" src="http://hortonworks.com/wp-content/uploads/2012/08/YARNArch.png" width="463"></p>

<p>图2 Apache Hadoop YARN架构</p>

<p>       ResourceManager是系统中仲裁应用之间资源分配的最高权威。而每个应用一个的ApplicationMaster负责向ResourceManager协商资源，并与NodeManager协同工作来执行和管理task。ResourceManager有一个可插入的调度器，负责向各个应用分配资源以满足容量，组等限制。这个调度器是一个纯粹的调度器，意思是它不负责管理或追踪应用的状态，也不负责由于硬件错误或应用问题导致的task失败重启工作。调度器只依据应用的资源需求来执行调度工作，调度内容是一个抽象概念Resource Container，其中包含了资源元素，例如内存，CPU，网络，磁盘等。</p>

<p>       NodeManager是每个节点一个的slave，其负责启动应用的container，管理他们的资源使用（内存，CPU，网络，磁盘）,并向ResourceManager汇报整体的资源使用情况。</p>

<p>       每个应用一个的ApplicationMaster负责向ResourceManager的调度器协商合理的Resource Container并追踪他们的状态，管理进度。从系统角度看，ApplicationMaster本身也是以一个普通container的形式执行。</p>

<h2><a name="t2"></a>总结</h2>

<p>       由于MapReduce在计算模型方面的局限性，Hadoop实现了更加通用的资源管理系统YARN，并将MapReduce作为其一个应用。在YARN上可以实现多种多样计算模型的应用以满足业务需要。另外由于YARN系统将JobTracker的主要工作进行切分，使得master的压力大大减小（ResourceManager承担的工作量远小于JobTracker），这样YARN系统就可以支持更大的集群规模。</p>

<p>Resource Container：是监控程序中的各个进程的运行情况，包括：内存，CPU，网络，磁盘等。</p>            </div>
                </div>