---
layout:     post
title:      Apache Hadoop YARN
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1 style="margin-left:0px;"><span style="color:#404041;">Apache Hadoop YARN - 概念和应用程序</span></h1>

<p><span style="color:#404041;"><span style="color:#404041;"><em>本系列的其他文章：</em><br><a href="https://zh.hortonworks.com/blog/introducing-apache-hadoop-yarn/" rel="nofollow">Apache Hadoop简介YARN </a><br><a href="https://zh.hortonworks.com/blog/apache-hadoop-yarn-background-and-an-overview/" rel="nofollow">Apache Hadoop YARN - 背景和概述</a><br><a href="https://zh.hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/" rel="nofollow">Apache Hadoop YARN - 概念和应用程序</a><br><a href="https://zh.hortonworks.com/blog/apache-hadoop-yarn-resourcemanager/" rel="nofollow">Apache Hadoop YARN - ResourceManager </a><br><a href="https://zh.hortonworks.com/blog/apache-hadoop-yarn-nodemanager/" rel="nofollow">Apache Hadoop YARN - NodeManager</a></span></span></p>

<h3 style="margin-left:0px;"><span style="color:#404041;"><strong>APACHE HADOOP YARN - 概念与应用</strong></span></h3>

<p style="margin-left:0px;"><span style="color:#404041;"><span style="color:#404041;">如<a href="https://zh.hortonworks.com/blog/apache-hadoop-yarn-background-and-an-overview/" rel="nofollow">先前</a>描述的，Yarn实质上是用于管理分布式应用的系统。它<em> </em>由一个仲裁所有可用群集资源的中央<strong>ResourceManager</strong>和一个每个节点的<strong>NodeManager组成</strong>，它从ResourceManager获取资源，负责管理单个节点上可用的资源。</span></span></p>

<p style="margin-left:0px;"><span style="color:#404041;"><span style="color:#404041;"><strong><em>资源管理</em></strong></span></span></p>

<p style="margin-left:0px;"><span style="color:#404041;"><span style="color:#404041;">在YARN中，</span></span>ResourceManager有两个主要组件：Scheduler和ApplicationsManager<span style="color:#404041;"><span style="color:#404041;">。从本质上讲，它严格限制在竞争应用程序之间仲裁系统中的可用资源。它针对各种约束（例如容量保证，公平性和SLA）优化集群利用率（始终保持所有资源使用）。为了允许不同的策略约束，ResourceManager具有<em>可插拔</em> <em>调度程序</em>，允许根据需要使用不同的算法，如容量和公平调度。</span></span></p>

<p style="margin-left:0px;"><span style="color:#404041;"><span style="color:#404041;"><strong><em>ApplicationMaster</em></strong></span></span></p>

<p style="margin-left:0px;"><span style="color:#404041;"><span style="color:#404041;">许多人将在YARN和现有的Hadoop MapReduce系统（Apache Hadoop 1.x中的MR1）之间取得平行。但是，关键的区别是<em>新概念</em>的的<strong>ApplicationMaster</strong>。</span></span></p>

<p style="margin-left:0px;"><span style="color:#404041;"><span style="color:#404041;">ApplicationMaster实际上是一个<em>特定</em>于<em>框架的库</em>的<em>实例</em>，负责协商来自ResourceManager的资源，并与NodeManager一起执行和监视容器及其资源消耗。它负责从ResourceManager协商适当的资源容器，跟踪其状态并监控进度。</span></span></p>

<p style="margin-left:0px;"><span style="color:#404041;"><span style="color:#404041;">ApplicationMaster允许YARN展示以下关键特征：</span></span></p>

<ul style="margin-left:0px;"><li><span style="color:#404041;">Scale：Application Master提供了传统ResourceManager的大部分功能，因此整个系统可以更加大规模地扩展。在测试中，我们已经成功地模拟了由现代硬件组成的10,000个节点集群而没有重大问题。这是我们选择将ResourceManager设计为<em>纯调度程序的</em>主要原因之一，即它不会尝试为资源提供容错。我们将其转变为ApplicationMaster实例的主要职责。此外，由于每个应用程序都有一个ApplicationMaster实例，因此ApplicationMaster本身不是群集中的常见瓶颈。</span></li>
	<li>Open<span style="color:#404041;">：将所有特定于应用程序框架的代码移动到ApplicationMaster中，对系统进行了概括，以便我们现在可以支持多个框架，如MapReduce，MPI和Graph Processing。</span></li>
</ul><p style="margin-left:0px;"><span style="color:#404041;"><span style="color:#404041;">插入一些关键的YARN设计决策是一个很好的观点：</span></span></p>

<ul style="margin-left:0px;"><li><span style="color:#404041;"><em>将所有复杂性（尽可能）移至ApplicationMaster，同时提供足够的功能以允许应用程序框架作者具有足够的灵活性和功能。</em></span></li>
	<li><span style="color:#404041;"><em>由于它本质上是用户代码，因此不要信任ApplicationMaster，即任何ApplicationMaster都不是特权服务。</em></span></li>
	<li><span style="color:#404041;"><em>YARN系统（ResourceManager和NodeManager）必须不惜一切代价保护自己免受错误或恶意的ApplicationMaster和资源的影响。</em></span></li>
</ul><p style="margin-left:0px;"><span style="color:#404041;"><span style="color:#404041;">记住，实际上，每个应用程序都有自己的ApplicationMaster实例，这很有用。但是，实现ApplicationMaster来管理一组应用程序（例如，ApplicationMaster for Pig或Hive来管理一组MapReduce作业）是完全可行的。此外，这个概念已被扩展到管理长期运行的服务，这些服务管理自己的应用程序（例如，通过假设的HBaseAppMaster在YARN中启动HBase）。</span></span></p>

<p style="margin-left:0px;"><span style="color:#404041;"><span style="color:#404041;"><strong><em>资源模型</em></strong></span></span></p>

<p style="margin-left:0px;"><span style="color:#404041;"><span style="color:#404041;">YARN支持应用程序的非常通用的<em>资源模型</em>。应用程序（通过ApplicationMaster）可以请求具有高度特定要求的资源，例如：</span></span></p>

<ul style="margin-left:0px;"><li><span style="color:#404041;">资源名称（主机名，机架名称 - 我们正在进一步概括这一点，以支持使用<a href="https://issues.apache.org/jira/browse/YARN-18" rel="nofollow">YARN-18的</a>更复杂的网络拓扑）。</span></li>
	<li><span style="color:#404041;">内存（以MB为单位）</span></li>
	<li><span style="color:#404041;">CPU（核心，现在）</span></li>
	<li><span style="color:#404041;">将来，我们希望能够添加更多资源类型，例如磁盘/网络I / O，GPU等。</span></li>
</ul><p style="margin-left:0px;"><span style="color:#404041;"><span style="color:#404041;"><strong><em>ResourceRequest和Container</em></strong></span></span></p>

<p style="margin-left:0px;"><span style="color:#404041;"><span style="color:#404041;">YARN旨在允许各个应用程序（通过ApplicationMaster）以共享，安全和多租户方式利用群集资源。此外，它仍然了解<em>群集拓扑</em>，以便有效地调度和优化数据访问，即尽可能减少应用程序的数据运动。</span></span></p>

<p style="margin-left:0px;"><span style="color:#404041;"><span style="color:#404041;">为了实现这些目标，中央调度程序（在ResourceManager中）具有有关应用程序资源需求的大量信息，这使其能够跨集群中的所有应用程序做出更好的调度决策。这将我们引向<strong>ResourceRequest</strong>和生成的<strong>Container</strong>。</span></span></p>

<p style="margin-left:0px;"><span style="color:#404041;"><span style="color:#404041;">本质上，应用程序可以通过ApplicationMaster <em>请求</em>特定的<em>资源请求</em>，以满足其资源需求。Scheduler通过授予<em>容器</em>来响应资源请求，该<em>容器</em>满足ApplicationMaster在初始ResourceRequest中规定的要求。</span></span></p>

<p style="margin-left:0px;"><span style="color:#404041;"><span style="color:#404041;">让我们看一下ResourceRequest - 它有以下形式：</span></span></p>

<p style="margin-left:0px;"><span style="color:#404041;"><span style="color:#404041;">&lt;resource-name，priority，resource-requirement，container-of-container&gt;</span></span></p>

<p style="margin-left:0px;"><span style="color:#404041;"><span style="color:#404041;">让我们来看看ResourceRequest的每个组件，以便更好地理解它。</span></span></p>

<ul style="margin-left:0px;"><li><span style="color:#404041;">resource-name是hostname，rackname或*表示没有首选项。将来，我们希望在主机，更复杂的网络等上支持更复杂的虚拟机拓扑。</span></li>
	<li><span style="color:#404041;">优先级是此请求的应用程序内优先级（强调，这不是跨多个应用程序）。</span></li>
	<li><span style="color:#404041;">资源需求是必需的功能，如内存，CPU等。（在编写时，YARN仅支持内存和CPU）。</span></li>
	<li><span style="color:#404041;">容器数量只是这种<em>容器</em>的倍数。</span></li>
</ul><p style="margin-left:0px;"><span style="color:#404041;"><span style="color:#404041;">现在，进入Container。</span></span></p>

<p style="margin-left:0px;"><span style="color:#404041;"><span style="color:#404041;">本质上，Container是资源<strong>分配，</strong>这是ResourceManager授予特定ResourceRequest 的<em>成功结果</em>。Container授予应用程序在<em>特定主机</em>上使用<em>特定数量资源</em>（内存，CPU等）的<em>权限</em>。</span></span></p>

<p style="margin-left:0px;"><span style="color:#404041;"><span style="color:#404041;">ApplicationMaster必须获取Container并将其呈现给NodeManager，管理分配容器的主机，以使用资源启动其任务。当然，在安全模式下验证Container分配，以确保ApplicationMaster <em>不能伪造</em>集群中的<em>分配</em>。</span></span></p>

<p style="margin-left:0px;"><span style="color:#404041;"><span style="color:#404041;"><em>集装箱发射期间的集装箱规</em></span></span></p>

<p style="margin-left:0px;"><span style="color:#404041;"><span style="color:#404041;">虽然如上所述，Container仅仅是在群集中的特定机器（NodeManager）上使用指定数量的资源的<em>权利</em>，但ApplicationMaster必须向NodeManager提供更多信息以实际<em>启动</em>容器。</span></span></p>

<p style="margin-left:0px;"><span style="color:#404041;"><span style="color:#404041;">YARN允许应用程序启动任何进程，与hadoop-1.x（又名MR1）中的现有Hadoop MapReduce不同，它不仅限于Java应用程序。</span></span></p>

<p style="margin-left:0px;"><span style="color:#404041;"><span style="color:#404041;">YARN Container启动规范API与平台无关，包含：</span></span></p>

<ul style="margin-left:0px;"><li><span style="color:#404041;">命令行在容器内启动进程。</span></li>
	<li><span style="color:#404041;">环境变量。</span></li>
	<li><span style="color:#404041;">启动前机器上所需的本地资源，如jar，共享对象，辅助数据文件等。</span></li>
	<li><span style="color:#404041;">与安全相关的</span>tokens<span style="color:#404041;">。</span></li>
</ul><p style="margin-left:0px;"><span style="color:#404041;"><span style="color:#404041;">这允许ApplicationMaster与NodeManager一起启动容器，范围从简单的shell脚本到Unix / Windows上的C / Java / Python进程，再到成熟的虚拟机（例如KVM）。</span></span></p>

<p style="margin-left:0px;"><span style="color:#404041;"><span style="color:#404041;"><strong>YARN - </strong></span></span><strong>Walkthrough</strong></p>

<p style="margin-left:0px;"><span style="color:#404041;"><span style="color:#404041;">有了上述概念的知识，概述应用程序在YARN中的概念性工作方式将非常有用。</span></span></p>

<p style="margin-left:0px;"><span style="color:#404041;"><span style="color:#404041;">应用程序执行包括以下步骤：</span></span></p>

<ul style="margin-left:0px;"><li><span style="color:#404041;">申请提交。</span></li>
	<li><span style="color:#404041;">引导应用程序的ApplicationMaster实例。</span></li>
	<li><span style="color:#404041;">由ApplicationMaster实例管理的应用程序执行。</span></li>
</ul><p style="margin-left:0px;"><span style="color:#404041;"><span style="color:#404041;">让我们来看看应用程序执行顺序（步骤如图所示）：</span></span></p>

<ol style="margin-left:0px;"><li><span style="color:#404041;">客户端程序<em>提交</em>应用程序，包括<em>启动</em>特定于<em>应用程序的ApplicationMaster</em>本身的必要规范。</span></li>
	<li><span style="color:#404041;">ResourceManager承担协商指定容器的责任，在该容器中启动ApplicationMaster。</span></li>
	<li><span style="color:#404041;">该ApplicationMaster，在启动时，向ResourceManager注册，允许客户端程序来查询ResourceManager的详细信息，这允许它可以直接与自己的ApplicationMaster通信。</span></li>
	<li><span style="color:#404041;">在正常操作期间，ApplicationMaster通过资源请求协议协商适当的资源容器。</span></li>
	<li><span style="color:#404041;">在成功的容器分配中，ApplicationMaster通过向NodeManager提供容器启动规范来启动容器。通常，启动规范包括允许容器与ApplicationMaster本身通信的必要信息。</span></li>
	<li><span style="color:#404041;">然后，在容器内执行的应用程序代码通过<em>特定</em>于<em>应用程序的协议</em>向其ApplicationMaster提供必要的信息（进度，状态等）。</span></li>
	<li><span style="color:#404041;">在应用程序执行期间，提交程序的客户端直接与ApplicationMaster通信，以通过特定于应用程序的协议获取状态，进度更新等。</span></li>
	<li><span style="color:#404041;">一旦应用程序完成，并且所有必要的工作都已完成，ApplicationMaster将使用ResourceManager取消注册并关闭，从而允许自己的容器重新调整用途。</span></li>
</ol><p style="margin-left:0px;"><span style="color:#404041;"><span style="color:#404041;"><a href="https://2xbbhjxc6wk3v21p62t8n4d4-wpengine.netdna-ssl.com/wp-content/uploads/2012/08/yarnflow.png" rel="nofollow"><img alt="" class="alignnone wp-image-8324" height="438" src="https://2xbbhjxc6wk3v21p62t8n4d4-wpengine.netdna-ssl.com/wp-content/uploads/2012/08/yarnflow.png" width="540"></a></span></span></p>

<p style="margin-left:0px;"><span style="color:#404041;"><span style="color:#404041;"><em>在本系列的下一篇文章中，我们将更多地探讨YARN系统的内容，特别是ResourceManager - 敬请期待！</em></span></span></p>

<p style="margin-left:0px;"><span style="color:#404041;"><span style="color:#404041;"><em>本系列的其他文章：</em><br><a href="https://zh.hortonworks.com/blog/introducing-apache-hadoop-yarn/" rel="nofollow">Apache Hadoop简介YARN </a><br><a href="https://zh.hortonworks.com/blog/apache-hadoop-yarn-background-and-an-overview/" rel="nofollow">Apache Hadoop YARN - 背景和概述</a><br><a href="https://zh.hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/" rel="nofollow">Apache Hadoop YARN - 概念和应用程序</a><br><a href="https://zh.hortonworks.com/blog/apache-hadoop-yarn-resourcemanager/" rel="nofollow">Apache Hadoop YARN - ResourceManager </a><br><a href="https://zh.hortonworks.com/blog/apache-hadoop-yarn-nodemanager/" rel="nofollow">Apache Hadoop YARN - NodeManager</a></span></span></p>            </div>
                </div>