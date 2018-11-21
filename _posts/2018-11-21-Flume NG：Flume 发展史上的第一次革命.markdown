---
layout:     post
title:      Flume NG：Flume 发展史上的第一次革命
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>Flume 作为 cloudera 开发的实时日志收集系统，已经受到越来越多的关注。比如 IBM BigInsights 已经将 Flume 作为产品的一部分。Flume 初始的发行版本目前被统称为 Flume OG（original generation），属于 cloudera。但随着 FLume 功能的扩展，Flume OG 代码工程臃肿、核心组件设计不合理、核心配置不标准等缺点暴露出来，尤其是在 Flume OG 的最后一个发行版本 0.94.0 中，日志传输不稳定的现象尤为严重，这点可以在 BigInsights
 产品文档的 troubleshooting 板块发现。为了解决这些问题，2011 年 10 月 22 号，cloudera 完成了 Flume-728，对 Flume 进行了里程碑式的改动：重构核心组件、核心配置以及代码架构，重构后的版本统称为 Flume NG（next generation）；改动的另一原因是将 Flume 纳入 apache 旗下，cloudera Flume 改名为 Apache Flume。本文将从基本组件以及用户体验的角度阐述 Flume OG 到 Flume NG 发生的革命性变化。</p>
<p></p>
<h2 id="N10033">背景</h2>
<p></p>
<p>Cloudera 开发的分布式日志收集系统 Flume，是 hadoop 周边组件之一。其可以实时的将分布在不同节点、机器上的日志收集到 hdfs 中。Flume 初始的发行版本目前被统称为 Flume OG（original generation），属于 cloudera。但随着 FLume 功能的扩展，Flume OG 代码工程臃肿、核心组件设计不合理、核心配置不标准等缺点暴露出来，尤其是在 Flume OG 的最后一个发行版本 0.94.0 中，日志传输不稳定的现象尤为严重，这点可以在 BigInsights
 产品文档的 troubleshooting 板块发现。为了解决这些问题，2011 年 10 月 22 号，cloudera 完成了 Flume-728，对 Flume 进行了里程碑式的改动：重构核心组件、核心配置以及代码架构，重构后的版本统称为 Flume NG（next generation）；改动的另一原因是将 Flume 纳入 apache 旗下，cloudera Flume 改名为 Apache Flume。</p>
<p>下面将从核心组件变化、角色变化、用户配置变化以及实战等方面阐述 Flume NG 相对于 Flume OG 所发生的革命性变化。</p>
<h2 id="code-hd">核心组件变化</h2>
<p>图 1 和图 3 是两个版本的架构图。</p>
<p>FLUM OG 的特点是： </p>
<ul class="ibm-bullet-list"><li>FLUM OG 有三种角色的节点，如图 1：代理节点（agent）、收集节点（collector）、主节点（master）。 </li><li>agent 从各个数据源收集日志数据，将收集到的数据集中到 collector，然后由收集节点汇总存入 hdfs。master 负责管理 agent，collector 的活动。</li><li>agent、collector 都称为 node，node 的角色根据配置的不同分为 logical node（逻辑节点）、physical node（物理节点）。对 logical nodes 和 physical nodes 的区分、配置、使用一直以来都是使用者最头疼的地方。</li><li>agent、collector 由 source、sink 组成，代表在当前节点数据是从 source 传送到 sink。如图 2。 </li></ul><h5 id="N10050">图 1. FLUM OG 架构图</h5>
<p><img src="https://img-blog.csdn.net/20141128144656843" alt=""></p>
<p></p>
<h5 id="N1005A">图 2. OG 节点组成图</h5>
<img src="https://img-blog.csdn.net/20141128144722942" alt=""><p></p>
<p></p>
<p>对应于 OG 的特点，FLUM NG 的特点是： </p>
<ul class="ibm-bullet-list"><li>NG 只有一种角色的节点：代理节点（agent）。 </li><li>没有 collector、master 节点。这是核心组件最核心的变化。 </li><li>去除了 physical nodes、logical nodes 的概念和相关内容。 </li><li>agent 节点的组成也发生了变化。如图 4，NG agent 由 source、sink、channel 组成。 </li></ul><h5 id="N1006F">图 3. FLUM NG 架构图</h5>
<img src="https://img-blog.csdn.net/20141128144751303" alt=""><p></p>
<h5 id="N10079">图 4. NG 节点组成图</h5>
<img src="https://img-blog.csdn.net/20141128144801630" alt=""><p></p>
<p></p>
<p>从整体上讲，NG 在核心组件上进行了大规模的调整，核心组件的数目由 7 删减到 4。由于 Flume 的使用涉及到众多因素，如 avro、thrift、hdfs、jdbc、zookeeper 等，而这些组件和 Flume 的整合都需要关联到所有组件。所以核心组件的改革对整个 Flume 的使用影响深远：</p>
<ul class="ibm-bullet-list"><li>大大降低了对用户的要求，如核心组件的变化使得 Flume 的稳定使用不再依赖 zookeeper，用户无需去搭建 zookeeper 集群；另外用户也不再纠结于 OG 中的模糊概念（尤其是 physical nodes、logical nodes，agent、collector）。</li><li>有利于 Flume 和其他技术、hadoop 周边组件的整合，比如在 NG 版本中，Flume 轻松实现了和 jdbc、hbase 的集成。 </li><li>将 OG 版本中复杂、大规模、不稳定的标签移除，Flume 实现了向灵活、轻便的转变，而且在功能上更加强大、可扩展性更高，这一点主要表现在用户使用 Flume 搭建日志收集集群的过程中，后面的章节会详细介绍。</li></ul><h2 id="N1008B"><br></h2>
<h2>删减节点角色，脱离 zookeeper</h2>
<p>Zookeeper 是针对大型分布式系统的可靠协调系统，适用于有多类角色集群管理。比如在 hbase 中，对 HMaster、HRegionServer 的管理。</p>
<p>在 OG 版本中，Flume 的使用稳定性依赖 zookeeper。它需要 zookeeper 对其多类节点（agent、collector、master）的工作进行管理，尤其是在集群中配置多个 master 的情况下。当然，OG 也可以用内存的方式管理各类节点的配置信息，但是需要用户能够忍受在机器出现故障时配置信息出现丢失。所以说 OG 的稳定行使用是依赖 zookeeper 的。</p>
<p>而在 NG 版本中，节点角色的数量由 3 缩减到 1，不存在多类角色的问题，所以就不再需要 zookeeper 对各类节点协调的作用了，由此脱离了对 zookeeper 的依赖。由于 OG 的稳定使用对 zookeeper 的依赖表现在整个配置和使用过程中，这就要求用户掌握对 zookeeper 集群的搭建极其使用（尤其是要熟悉 zookeeper 数据文件夹 data，Flume 节点配置信息存放在此文件夹中）；掌握 Flume 中和 zookeeper 相关的配置。对初次接触 Flume 的用户来讲，这是非常痛苦的事。</p>
<h2 id="N10097">用户配置变化</h2>
<p>从用户角度来讲，配置过程无疑是整个集群搭建的核心步骤。Flume 的配置分为两个部分：安装和数据传输配置。</p>
<h3 id="N1009F">安装</h3>
<p>OG 在安装时： </p>
<ul class="ibm-bullet-list"><li>在 flume-env.sh 中设置$JAVA_HOME。 </li><li>需要配置文件 flume-conf.xml。其中最主要的、必须的配置与 master 有关。集群中的每个 Flume 都需要配置 master 相关属性（如 flume.master.servers、flume.master.store、flume.master.serverid）。</li><li>如果想稳定使用 Flume 集群，还需要安装 zookeeper 集群，这需要用户对 zookeeper 有较深入的了解。 </li><li>安装 zookeeper 之后，需要配置 flume-conf.xml 中的相关属性，如 flume.master.zk.use.external、flume.master.zk.servers。</li><li>在使用 OG 版本传输数据之前，需要启动 master、agent。 </li></ul><p>NG 在安装时，只需要在 flume-env.sh 中设置$JAVA_HOME。</p>
<h3 id="N100B4">数据传输配置</h3>
<p>OG 版本的配置途径有两个： </p>
<ul class="ibm-bullet-list"><li>shell 命令：需要用户掌握 Flume shell 命令； </li><li>master console 页面：这是 OG 用户最常用的配置方式；弊端在于，除非用户熟悉复杂的各类 source，sink 配置函数以及格式（source：大约 25 个，sink：大约 46 个），否则在复杂的集群环境下，用户每次只能配置一个节点（指定 source、sink）来保证配置的准确性；</li></ul><p>NG 的配置只需要一个配置文件，这个配置文件中存放 source、sink、channel 的配置。如图 5 中是配置文件 example.conf 里面的内容，其中 agent_foo 是 agent 名字。然后在启动 agent 时，使用一下命令指定这个配置文件：</p>
<pre><code class="language-plain">$ bin/flume-ng agent
--conf-file example.conf \
--name agent_foo \
-Dflume.root.logger=INFO,console</code></pre>
<h5 id="N100C7">图 5. example.conf</h5>
<img src="https://img-blog.csdn.net/20141128145006274" alt=""><br><h2 id="N100D0">实战 Flume NG </h2>
<p>Flume 最常用的使用场景是，从节点收集日志数据，并以一定的格式存放到分布式文件系统 hdfs（hadoop 文件系统）中。下面介绍如何使用 Flume NG 从一个节点收集实时日志，并存放到 hdfs 中。</p>
<p>场景说明： </p>
<ul class="ibm-bullet-list"><li>场景中有两台主机 host1、host2。 </li><li>数据源是 host2 上的系统日志文件“/var/log/secure”（登录到系统存取资料的记录，本机的测试系统有多人使用，所以记录在不断的生成）。数据目的地是 hadoop 文件系统 hdfs。</li><li>在 host1、host2 上搭建 hadoop 集群。其中 host1 为 namenode、jobtracker，host2 为 datanode、tasktracker。</li></ul><h3 id="N100E1">使用 ng 搭建日志传输场景：flume+hadoop</h3>
<p>场景搭建步骤： </p>
<ul class="ibm-bullet-list"><li>下载 flume-ng 安装包，并解压到 host2。
<p>Flume 发布了两类包：source 和 bin。Source 包用于开发工作，bin 包用于安装 Flume 搭建日志收集场景。本次实验用的是 <a href="http://www.apache.org/dyn/closer.cgi/flume/1.5.2/apache-flume-1.5.2-bin.tar.gz" rel="nofollow">
apache-flume-1.5.2-bin.tar.gz</a></p>
</li><li>生成配置文件 example.conf。内容如图 6。整个配置分为四部分。表 1 是配置说明。</li></ul><h5 id="N100F9">图 6. example.conf</h5>
<img src="https://img-blog.csdn.net/20141128145636390" alt=""><p></p>
<h5>表 1. flume-conf.xml</h5>
<table class="ibm-data-table" summary="带表头、所有列左对齐的样式" width="100%" border="1" cellpadding="1" cellspacing="1"><thead><tr><th scope="col">Properties Name</th>
<th scope="col">Description</th>
</tr></thead><tbody><tr><th class="tb-row" scope="row">agent_ff</th>
<td>用来收集日志信息的 agent 节点名称</td>
</tr><tr><th class="tb-row" scope="row">agent_ff.source</th>
<td>需要收集的信息源，名字：tailsource-ff</td>
</tr><tr><th class="tb-row" scope="row">agent_ff.sinks</th>
<td>日志需要被收集到此处，名字：hdfsSink-ff。</td>
</tr><tr><th class="tb-row" scope="row">agent_ff.channels</th>
<td>日志的收集需要通过此管道，名字：memoryChannel-ff。</td>
</tr><tr><th class="tb-row" scope="row">tailsource-ff.type</th>
<td>定义 source 的类型，此处 exec 代表数据源是 exec 命令</td>
</tr><tr><th class="tb-row" scope="row">tailsource-ff.command</th>
<td>定义具体命令，此处是对文件/var/log/secure 做 tail</td>
</tr><tr><th class="tb-row" scope="row">tailsource-ff.channels</th>
<td>数据传输的管道，此处的管道名称应该和 sink 相同。从而将 source、sink 通过 channels 进行连接。</td>
</tr><tr><th class="tb-row" scope="row">memoryChannel-ff.type</th>
<td>管道类型，代表事件存储的方式。Source 产生事件，sink 移除事件，以此代表数据传输完成。目前 Flume 支持 6 种 channel。此处是 momery，代表事件是存在内存里</td>
</tr><tr><th class="tb-row" scope="row">memoryChannel-ff.capacity</th>
<td>管道里可以存放的最多的事件数目。此处代表 memoryChannel-ff 最多可存放 1000 个事件。</td>
</tr><tr><th class="tb-row" scope="row">hdfsSink-ff.type</th>
<td>数据目的地的类型，此处是将数据存放在 hdfs 中。</td>
</tr><tr><th class="tb-row" scope="row">hdfsSink-ff.channel</th>
<td>定义和 source 相关联的管道。</td>
</tr><tr><th class="tb-row" scope="row">hdfsSink-ff.hdfs.path</th>
<td>数据存放在 hdfs 中的位置。</td>
</tr><tr><th class="tb-row" scope="row">hdfsSink-ff.hdfs.filePrefix</th>
<td>收集到的数据存放的文件以此为前缀。如图 8。</td>
</tr><tr><th class="tb-row" scope="row">hdfsSink-ff.hdfs.round, hdfsSink-ff.hdfs.roundValue, hdfsSink-ff.hdfs.roundUnit</th>
<td>定义在 hdfs 中生成的文件的时间戳。此处代表将 hdfs 中的文件的时间戳，向下取整到上一个十分钟内。比如说，在 2012 年 6 月 12 号上午 11:54:34 生成的事件，在 hdfs 中生成的路径将是/flume/events/2012-06-12/1150/00。</td>
</tr></tbody></table><ul class="ibm-bullet-list"><li>进入 bin 目录，使用一下命令启动 Flume，开始日志收集，控制台输出如图 7，传输到 hdfs 的文件如图 8。 </li></ul><pre><code class="language-plain">./flume-ng agent \
--conf-file ../example.conf \
--name agent_ff \
-Dflume.root.logger=INFO,cnsole</code></pre>
<h5 id="N10178">图 7. NG console</h5>
<img src="https://img-blog.csdn.net/20141128145804805" alt=""><p></p>
<p></p>
<h5 id="N10182">图 8. hdfs</h5>
<p></p>
<p><img src="https://img-blog.csdn.net/20141128145842000" alt=""></p>
<p><br></p>
<p></p>
<h2 id="N1018B">Flume OG vs Flume NG：用户体验对比 </h2>
<p></p>
<p>整体上看，NG 的用户体验要比 OG 好得多，这一点从用户文档上就可见一斑。OG 版本的使用文档有 90 多页，而 OG 只用 20 多页的内容就完成了新版 Flume 的使用说明。对应于上一节“实战 Flume NG”，我们使用和 NG 中同样的场景说明，下面介绍如何使用 OG 搭建同样的日志收集场景。用户可以从场景的搭建上明显的看出差别：NG 的整个过程只涉及到 hadoop、agent，而 OG 则需要涉及到 hadoop、zookeeper、master、agent、flume-conf.xml。</p>
<h3 id="N10193">使用 og 搭建日志传输场景：flume+zookeeper+hadoop</h3>
<p>场景搭建步骤： </p>
<ul class="ibm-bullet-list"><li>下载 zookeeper 安装包，并在 host2 上安装 zookeeper-3.4.3。请参考：<a href="http://zookeeper.apache.org/doc/r3.4.3/zookeeperStarted.html" rel="nofollow">zookeeperStarted</a>。</li><li>下载 flume-0.94.0（OG），并解压在 host2 上。 </li><li>配置文件 conf/flume-conf.xml，必须配置的信息如表 2。 </li><li>进入 bin 目录，使用一下命令启动 flume master、agent。 </li></ul><pre><code class="language-plain">master: ./flume-daemon.sh start master
agent: ./flume node -n agent-ff</code></pre>
<ul class="ibm-bullet-list"><li>进入 master 页面：http://host2:35871/flumemaster.jsp。配置 source、sink。如图 9。“Submit Query”后 Flume 便开始收集数据。</li><li>agent-ff 控制台输出如图 10。 </li></ul><h5>表 2. flume-conf.xml</h5>
<table class="ibm-data-table" summary="带表头、所有列左对齐的样式" width="100%" border="1" cellpadding="1" cellspacing="1"><thead><tr><th scope="col">属性（Property）</th>
<th scope="col">赋值（Value）</th>
</tr></thead><tbody><tr><th class="tb-row" scope="row">flume.master.servers</th>
<td>host2</td>
</tr><tr><th class="tb-row" scope="row">flume.master.store</th>
<td>zookeeper</td>
</tr><tr><th class="tb-row" scope="row">flume.master.serverid</th>
<td>0</td>
</tr><tr><th class="tb-row" scope="row">flume.master.zk.use.external</th>
<td>true</td>
</tr><tr><th class="tb-row" scope="row">flume.master.zk.servers</th>
<td>host2:2181</td>
</tr></tbody></table><h5 id="N101E6">图 9. OG 配置</h5>
<img src="https://img-blog.csdn.net/20141128145929843" alt=""><p></p>
<h5 id="N101F0">图 10. OG console</h5>
<p></p>
<p><img src="https://img-blog.csdn.net/20141128150007530" alt=""></p>
<p></p>
<h2 id="N101F9">结束语</h2>
<p></p>
<p>本文通过对比，从核心组件和用户的角度，阐述了 Flume NG 给 Flume 带来的第一次革命。从核心组件来讲，NG 简化核心组件，移除了 OG 版本代码工程臃肿、核心组件设计不合理、核心配置不标准等缺点；另外 NG 脱离了 Flume 稳定性对 zookeeper 的依赖。从用户角度来讲，NG 版本对用户要求大大降低：安装过程除了 java 无需配置复杂的 Flume 相关属性，也无需搭建 zookeeper 集群，安装过程几乎零工作量；数据流的配置过程更加简单、合理，只需要实现 source、sink、channel
 的简单配置即可。 </p>
<p><br></p>
<p></p>
<h2 id="resources" class="ibm-rule">参考资料 </h2>
<h3 class="dw-resources-category-heading">学习</h3>
<ul class="ibm-bullet-list"><li>访问 <a href="https://db2id.torolab.ibm.com/bigins_v1.1.1/index.jsp" rel="nofollow">
BigInsights Information Center</a>，了解关于 BigInsights 的详细信息。</li><li>查看 <a href="http://flume.apache.org/" rel="nofollow">Flume 官方网站</a>，了解 Flume 基本知识和最新信息。</li><li>通过<a href="http://hadoop.apache.org/" rel="nofollow">Hadoop 官方网站</a>，了解 Hadoop 基本知识和最新信息。</li><li>通过 <a href="http://zookeeper.apache.org/" rel="nofollow">Zookeeper 官网</a>，了解 Zookeeper 基本知识和最新信息。</li><li>在 <a href="http://www.ibm.com/developerworks/cn/opensource/os-log-process-hadoop/" rel="nofollow">
实践：使用 Apache Hadoop 处理日志</a></li><li>在 <a href="http://www.ibm.com/developerworks/cn/data/" rel="nofollow">
developerWorks Information Management 专区</a>，了解关于信息管理的更多信息，获取技术文档、how-to 文章、培训、下载、产品信息以及其他资源。</li></ul><p></p>
<p><br></p>
<p>转自：<a href="http://www.ibm.com/developerworks/cn/data/library/bd-1404flumerevolution/index.html" rel="nofollow">http://www.ibm.com/developerworks/cn/data/library/bd-1404flumerevolution/index.html</a><br></p>
<p><a href="http://flume.apache.org/FlumeUserGuide.html" rel="nofollow">《Flume 1.5.2 User Guide》</a></p>
<p><a href="http://flume.apache.org/FlumeDeveloperGuide.html" rel="nofollow">《Flume 1.5.2 Developer Guide》</a></p>
<p><a href="http://tech.meituan.com/mt-log-system-arch.html" rel="nofollow">《基于Flume的美团日志收集系统(一)架构和设计》</a><br></p>
<p><a href="http://www.cnblogs.com/oubo/archive/2012/05/25/2517751.html" rel="nofollow">Flume OG介绍《Flume日志收集》</a><br></p>
<br>            </div>
                </div>