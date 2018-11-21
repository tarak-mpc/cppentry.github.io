---
layout:     post
title:      Flume NG：Flume OG 区别对比
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div id="ibm-leadspace-head" style="border-width:0px 0px 1px;border-bottom-style:solid;border-bottom-color:rgb(229,229,229);font-size:14px;vertical-align:baseline;font-family:Arial, sans-serif;clear:both;line-height:19px;background-color:rgb(249,249,249) !important;">
<div id="dw-summary-area" class="dw-summary-columns" style="border:0px;vertical-align:baseline;clear:both;overflow:hidden;">
<div class="ibm-col-6-4" style="border:0px;vertical-align:baseline;clear:none;width:620px;">
<p style="border:0px;font-size:.95em;vertical-align:baseline;font-family:'HelveticaNeue-Light', 'Helvetica Neue Light', 'Helvetica Neue', Helvetica, Arial;width:620px;line-height:1.3em;color:rgb(51,51,51);">
Flume 作为 cloudera 开发的实时日志收集系统，已经受到越来越多的关注。比如 IBM BigInsights 已经将 Flume 作为产品的一部分。Flume 初始的发行版本目前被统称为 Flume OG（original generation），属于 cloudera。但随着 FLume 功能的扩展，Flume OG 代码工程臃肿、核心组件设计不合理、核心配置不标准等缺点暴露出来，尤其是在 Flume OG 的最后一个发行版本 0.94.0 中，日志传输不稳定的现象尤为严重，这点可以在 BigInsights
 产品文档的 troubleshooting 板块发现。为了解决这些问题，2011 年 10 月 22 号，cloudera 完成了 Flume-728，对 Flume 进行了里程碑式的改动：重构核心组件、核心配置以及代码架构，重构后的版本统称为 Flume NG（next generation）；改动的另一原因是将 Flume 纳入 apache 旗下，cloudera Flume 改名为 Apache Flume。本文将从基本组件以及用户体验的角度阐述 Flume OG 到 Flume NG 发生的革命性变化。</p>
<p class="ibm-no-print ibm-ind-link" style="border:0px;font-size:.95em;vertical-align:baseline;font-family:'HelveticaNeue-Light', 'Helvetica Neue Light', 'Helvetica Neue', Helvetica, Arial;width:620px;line-height:1.3em;color:rgb(51,51,51);">
<span id="nCmts" style="border:0px;vertical-align:baseline;"><span style="border:0px;font-size:.8em;vertical-align:top;color:rgb(255,255,255);background-color:rgb(40,125,159);">1</span><img class="dw-cmts-arrow" alt="" src="http://dw1.s81c.com/developerworks/i/v17/dw-cmts-arrow.png" height="7" width="7" style="border:0px;vertical-align:1px;"> <a href="http://www.ibm.com/developerworks/cn/data/library/bd-1404flumerevolution/index.html#icomments" rel="nofollow" class="dw-cmt-link" style="border-width:0px;border-bottom-style:none;font-size:1em;vertical-align:baseline;color:rgb(0,82,141);font-family:Arial, sans-serif;display:inline;text-decoration:none;line-height:1em;">评论：</a></span></p>
</div>
<div class="ibm-col-6-2 dw-toc-margin" style="border:0px;vertical-align:baseline;clear:none;width:290px;">
<p class="dw-summary-author" style="border:0px;font-size:.8em;vertical-align:baseline;font-family:'HelveticaNeue-Light', 'Helvetica Neue Light', 'Helvetica Neue', Helvetica, Arial;width:290px;line-height:1.2em;color:rgb(51,51,51);">
<a href="http://www.ibm.com/developerworks/cn/data/library/bd-1404flumerevolution/index.html#authorN1001B" rel="nofollow" class="ibm-popup-link" style="border-width:0px;border-bottom-style:none;font-size:1em;vertical-align:baseline;color:rgb(116,82,133);font-family:Arial, sans-serif;display:inline;text-decoration:none;">陈
 芳芳</a>, 软件工程师, IBM</p>
<p class="dw-summary-date" style="border:0px;font-size:.8em;vertical-align:baseline;font-family:'HelveticaNeue-Light', 'Helvetica Neue Light', 'Helvetica Neue', Helvetica, Arial;width:290px;line-height:1.2em;color:rgb(51,51,51);">
2014 年 4 月 08 日</p>
<div class="ibm-container" id="dw-toc" style="border:1px solid rgb(204,204,204);vertical-align:baseline;background-color:rgb(255,255,255);color:rgb(102,102,102);width:290px;z-index:900;">
<div class="ibm-container-body" style="border:0px;vertical-align:baseline;clear:both;">
<ul class="ibm-twisty" style="border:0px;vertical-align:baseline;list-style:none;"><li style="border:0px;vertical-align:baseline;">
<a href="http://www.ibm.com/developerworks/cn/data/library/bd-1404flumerevolution/index.html#toggle" rel="nofollow" class="ibm-twisty-trigger ibm-twisty-trigger-closed" style="border:0px;vertical-align:baseline;color:rgb(116,82,133);display:block;overflow:hidden;width:16px;"><img src="http://www.ibm.com/i/c.gif" alt="+" style="border:0px;vertical-align:baseline;width:16px;"></a><span class="ibm-twisty-head" style="border:0px;font-size:14px;vertical-align:baseline;font-family:'HelveticaNeue-Light', 'Helvetica Neue Light', 'Helvetica Neue', Helvetica, Arial;display:block;font-weight:bold;">内容</span></li></ul></div>
</div>
</div>
</div>
</div>
<div id="ibm-pcon" style="border:0px;vertical-align:baseline;font-family:Arial, sans-serif;">
<div id="ibm-content" style="border:0px;vertical-align:baseline;">
<div id="ibm-content-body" style="border:0px;vertical-align:baseline;">
<div id="ibm-content-main" style="border:0px;vertical-align:baseline;clear:left;">
<div class="ibm-columns" style="line-height:19px;border:0px;vertical-align:baseline;clear:both;overflow:hidden;">
<div class="ibm-col-1-1" style="border:0px;vertical-align:baseline;min-height:1px;clear:both;">
<div class="dw-article-sidebar dw-bluemix" id="dwc-exp-a" style="border-width:0px 0px 0px 1px;border-left-style:solid;border-left-color:rgb(170,170,170);vertical-align:baseline;clear:none;width:224px;line-height:1.2 !important;">
<img src="http://dw1.s81c.com/developerworks/i/bluemix-graphic.png" height="64" width="64" alt="" style="border:0px;vertical-align:baseline;display:block;"><p style="border:0px;vertical-align:baseline;font-family:'Helvetica Neue', Helvetica, Arial;color:rgb(14,128,151);line-height:16px;text-align:center;font-size:1.094em !important;">
在 IBM Bluemix 云平台上开发并部署您的下一个应用。</p>
<p class="ibm-button-link-alternate dw-button-green" style="border:0px;vertical-align:baseline;color:rgb(34,34,34);line-height:1.5em;font-size:1.166em !important;">
<a id="tutc-sbar-bmix-home-btn-ABa" href="https://www.bluemix.net/?cm_mmc=dwchina-_-bluemix-_-bd-1404flumerevolution-_-sidebar" rel="nofollow" class="dw-button-large">现在就开始免费试用</a></p>
</div>
<h2 id="N10033" style="border:0px;font-size:1.6em !important;vertical-align:baseline;font-family:'HelveticaNeue-Light', 'Helvetica Neue Light', 'Helvetica Neue', Helvetica, Arial;">
背景</h2>
<p style="border:0px;vertical-align:baseline;color:rgb(34,34,34);line-height:1.5em;font-size:1.166em !important;">
Cloudera 开发的分布式日志收集系统 Flume，是 hadoop 周边组件之一。其可以实时的将分布在不同节点、机器上的日志收集到 hdfs 中。Flume 初始的发行版本目前被统称为 Flume OG（original generation），属于 cloudera。但随着 FLume 功能的扩展，Flume OG 代码工程臃肿、核心组件设计不合理、核心配置不标准等缺点暴露出来，尤其是在 Flume OG 的最后一个发行版本 0.94.0 中，日志传输不稳定的现象尤为严重，这点可以在 BigInsights
 产品文档的 troubleshooting 板块发现。为了解决这些问题，2011 年 10 月 22 号，cloudera 完成了 Flume-728，对 Flume 进行了里程碑式的改动：重构核心组件、核心配置以及代码架构，重构后的版本统称为 Flume NG（next generation）；改动的另一原因是将 Flume 纳入 apache 旗下，cloudera Flume 改名为 Apache Flume。</p>
<p style="border:0px;vertical-align:baseline;color:rgb(34,34,34);line-height:1.5em;font-size:1.166em !important;">
下面将从核心组件变化、角色变化、用户配置变化以及实战等方面阐述 Flume NG 相对于 Flume OG 所发生的革命性变化。</p>
<h2 id="code-hd" style="border:0px;font-size:1.6em !important;vertical-align:baseline;font-family:'HelveticaNeue-Light', 'Helvetica Neue Light', 'Helvetica Neue', Helvetica, Arial;">
核心组件变化</h2>
<p style="border:0px;vertical-align:baseline;color:rgb(34,34,34);line-height:1.5em;font-size:1.166em !important;">
图 1 和图 3 是两个版本的架构图。</p>
<p style="border:0px;vertical-align:baseline;color:rgb(34,34,34);line-height:1.5em;font-size:1.166em !important;">
FLUM OG 的特点是：</p>
<ul class="ibm-bullet-list" style="border:0px;font-size:1.166em;vertical-align:baseline;list-style:none;color:rgb(51,51,51);line-height:1.5em;"><li style="border:0px;vertical-align:baseline;clear:left;">
FLUM OG 有三种角色的节点，如图 1：代理节点（agent）、收集节点（collector）、主节点（master）。</li><li style="border:0px;vertical-align:baseline;clear:left;">
agent 从各个数据源收集日志数据，将收集到的数据集中到 collector，然后由收集节点汇总存入 hdfs。master 负责管理 agent，collector 的活动。</li><li style="border:0px;vertical-align:baseline;clear:left;">
agent、collector 都称为 node，node 的角色根据配置的不同分为 logical node（逻辑节点）、physical node（物理节点）。对 logical nodes 和 physical nodes 的区分、配置、使用一直以来都是使用者最头疼的地方。</li><li style="border:0px;vertical-align:baseline;clear:left;">
agent、collector 由 source、sink 组成，代表在当前节点数据是从 source 传送到 sink。如图 2。</li></ul><h5 id="N10050" style="border:0px;font-size:1.166em !important;vertical-align:baseline;">
图 1. FLUM OG 架构图</h5>
<img alt="FLUM OG 架构图" src="http://www.ibm.com/developerworks/cn/data/library/bd-1404flumerevolution/1.jpg" width="440" style="border:0px;vertical-align:baseline;"><h5 id="N1005A" style="border:0px;font-size:1.166em !important;vertical-align:baseline;">
图 2. OG 节点组成图</h5>
<img alt="OG 节点组成图" src="http://www.ibm.com/developerworks/cn/data/library/bd-1404flumerevolution/2.jpg" width="387" style="border:0px;vertical-align:baseline;"><p style="border:0px;vertical-align:baseline;color:rgb(34,34,34);line-height:1.5em;font-size:1.166em !important;">
对应于 OG 的特点，FLUM NG 的特点是：</p>
<ul class="ibm-bullet-list" style="border:0px;font-size:1.166em;vertical-align:baseline;list-style:none;color:rgb(51,51,51);line-height:1.5em;"><li style="border:0px;vertical-align:baseline;clear:left;">
NG 只有一种角色的节点：代理节点（agent）。</li><li style="border:0px;vertical-align:baseline;clear:left;">
没有 collector、master 节点。这是核心组件最核心的变化。</li><li style="border:0px;vertical-align:baseline;clear:left;">
去除了 physical nodes、logical nodes 的概念和相关内容。</li><li style="border:0px;vertical-align:baseline;clear:left;">
agent 节点的组成也发生了变化。如图 4，NG agent 由 source、sink、channel 组成。</li></ul><h5 id="N1006F" style="border:0px;font-size:1.166em !important;vertical-align:baseline;">
图 3. FLUM NG 架构图</h5>
<img alt="FLUM NG 架构图" src="http://www.ibm.com/developerworks/cn/data/library/bd-1404flumerevolution/3.jpg" width="672" style="border:0px;vertical-align:baseline;"><h5 id="N10079" style="border:0px;font-size:1.166em !important;vertical-align:baseline;">
图 4. NG 节点组成图</h5>
<img alt="NG 节点组成图" src="http://www.ibm.com/developerworks/cn/data/library/bd-1404flumerevolution/4.jpg" width="505" style="border:0px;vertical-align:baseline;"><p style="border:0px;vertical-align:baseline;color:rgb(34,34,34);line-height:1.5em;font-size:1.166em !important;">
从整体上讲，NG 在核心组件上进行了大规模的调整，核心组件的数目由 7 删减到 4。由于 Flume 的使用涉及到众多因素，如 avro、thrift、hdfs、jdbc、zookeeper 等，而这些组件和 Flume 的整合都需要关联到所有组件。所以核心组件的改革对整个 Flume 的使用影响深远：</p>
<ul class="ibm-bullet-list" style="border:0px;font-size:1.166em;vertical-align:baseline;list-style:none;color:rgb(51,51,51);line-height:1.5em;"><li style="border:0px;vertical-align:baseline;clear:left;">
大大降低了对用户的要求，如核心组件的变化使得 Flume 的稳定使用不再依赖 zookeeper，用户无需去搭建 zookeeper 集群；另外用户也不再纠结于 OG 中的模糊概念（尤其是 physical nodes、logical nodes，agent、collector）。</li><li style="border:0px;vertical-align:baseline;clear:left;">
有利于 Flume 和其他技术、hadoop 周边组件的整合，比如在 NG 版本中，Flume 轻松实现了和 jdbc、hbase 的集成。</li><li style="border:0px;vertical-align:baseline;clear:left;">
将 OG 版本中复杂、大规模、不稳定的标签移除，Flume 实现了向灵活、轻便的转变，而且在功能上更加强大、可扩展性更高，这一点主要表现在用户使用 Flume 搭建日志收集集群的过程中，后面的章节会详细介绍。</li></ul><div class="ibm-alternate-rule" style="border:0px;vertical-align:baseline;background-color:rgb(204,204,204);clear:both;">
</div>
<p class="ibm-ind-link ibm-back-to-top" style="border:0px;vertical-align:baseline;color:rgb(34,34,34);clear:both;text-align:right;line-height:1.5em;font-size:1.166em !important;">
<a href="http://www.ibm.com/developerworks/cn/data/library/bd-1404flumerevolution/index.html#ibm-pcon" rel="nofollow" class="ibm-anchor-up-link" style="border-width:0px;border-bottom-style:none;vertical-align:baseline;color:rgb(116,82,133);display:inline;text-decoration:none;line-height:1.065em;font-weight:bold;">回页首</a></p>
<h2 id="N1008B" style="border:0px;font-size:1.6em !important;vertical-align:baseline;font-family:'HelveticaNeue-Light', 'Helvetica Neue Light', 'Helvetica Neue', Helvetica, Arial;">
删减节点角色，脱离 zookeeper</h2>
<p style="border:0px;vertical-align:baseline;color:rgb(34,34,34);line-height:1.5em;font-size:1.166em !important;">
Zookeeper 是针对大型分布式系统的可靠协调系统，适用于有多类角色集群管理。比如在 hbase 中，对 HMaster、HRegionServer 的管理。</p>
<p style="border:0px;vertical-align:baseline;color:rgb(34,34,34);line-height:1.5em;font-size:1.166em !important;">
在 OG 版本中，Flume 的使用稳定性依赖 zookeeper。它需要 zookeeper 对其多类节点（agent、collector、master）的工作进行管理，尤其是在集群中配置多个 master 的情况下。当然，OG 也可以用内存的方式管理各类节点的配置信息，但是需要用户能够忍受在机器出现故障时配置信息出现丢失。所以说 OG 的稳定行使用是依赖 zookeeper 的。</p>
<p style="border:0px;vertical-align:baseline;color:rgb(34,34,34);line-height:1.5em;font-size:1.166em !important;">
而在 NG 版本中，节点角色的数量由 3 缩减到 1，不存在多类角色的问题，所以就不再需要 zookeeper 对各类节点协调的作用了，由此脱离了对 zookeeper 的依赖。由于 OG 的稳定使用对 zookeeper 的依赖表现在整个配置和使用过程中，这就要求用户掌握对 zookeeper 集群的搭建极其使用（尤其是要熟悉 zookeeper 数据文件夹 data，Flume 节点配置信息存放在此文件夹中）；掌握 Flume 中和 zookeeper 相关的配置。对初次接触 Flume 的用户来讲，这是非常痛苦的事。</p>
<h2 id="N10097" style="border:0px;font-size:1.6em !important;vertical-align:baseline;font-family:'HelveticaNeue-Light', 'Helvetica Neue Light', 'Helvetica Neue', Helvetica, Arial;">
用户配置变化</h2>
<p style="border:0px;vertical-align:baseline;color:rgb(34,34,34);line-height:1.5em;font-size:1.166em !important;">
从用户角度来讲，配置过程无疑是整个集群搭建的核心步骤。Flume 的配置分为两个部分：安装和数据传输配置。</p>
<h3 id="N1009F" style="border:0px;font-size:1.4em !important;vertical-align:baseline;font-family:'HelveticaNeue-Light', 'Helvetica Neue Light', 'Helvetica Neue', Helvetica, Arial;">
安装</h3>
<p style="border:0px;vertical-align:baseline;color:rgb(34,34,34);line-height:1.5em;font-size:1.166em !important;">
OG 在安装时：</p>
<ul class="ibm-bullet-list" style="border:0px;font-size:1.166em;vertical-align:baseline;list-style:none;color:rgb(51,51,51);line-height:1.5em;"><li style="border:0px;vertical-align:baseline;clear:left;">
在 flume-env.sh 中设置$JAVA_HOME。</li><li style="border:0px;vertical-align:baseline;clear:left;">
需要配置文件 flume-conf.xml。其中最主要的、必须的配置与 master 有关。集群中的每个 Flume 都需要配置 master 相关属性（如 flume.master.servers、flume.master.store、flume.master.serverid）。</li><li style="border:0px;vertical-align:baseline;clear:left;">
如果想稳定使用 Flume 集群，还需要安装 zookeeper 集群，这需要用户对 zookeeper 有较深入的了解。</li><li style="border:0px;vertical-align:baseline;clear:left;">
安装 zookeeper 之后，需要配置 flume-conf.xml 中的相关属性，如 flume.master.zk.use.external、flume.master.zk.servers。</li><li style="border:0px;vertical-align:baseline;clear:left;">
在使用 OG 版本传输数据之前，需要启动 master、agent。</li></ul><p style="border:0px;vertical-align:baseline;color:rgb(34,34,34);line-height:1.5em;font-size:1.166em !important;">
NG 在安装时，只需要在 flume-env.sh 中设置$JAVA_HOME。</p>
<h3 id="N100B4" style="border:0px;font-size:1.4em !important;vertical-align:baseline;font-family:'HelveticaNeue-Light', 'Helvetica Neue Light', 'Helvetica Neue', Helvetica, Arial;">
数据传输配置</h3>
<p style="border:0px;vertical-align:baseline;color:rgb(34,34,34);line-height:1.5em;font-size:1.166em !important;">
OG 版本的配置途径有两个：</p>
<ul class="ibm-bullet-list" style="border:0px;font-size:1.166em;vertical-align:baseline;list-style:none;color:rgb(51,51,51);line-height:1.5em;"><li style="border:0px;vertical-align:baseline;clear:left;">
shell 命令：需要用户掌握 Flume shell 命令；</li><li style="border:0px;vertical-align:baseline;clear:left;">
master console 页面：这是 OG 用户最常用的配置方式；弊端在于，除非用户熟悉复杂的各类 source，sink 配置函数以及格式（source：大约 25 个，sink：大约 46 个），否则在复杂的集群环境下，用户每次只能配置一个节点（指定 source、sink）来保证配置的准确性；</li></ul><p style="border:0px;vertical-align:baseline;color:rgb(34,34,34);line-height:1.5em;font-size:1.166em !important;">
NG 的配置只需要一个配置文件，这个配置文件中存放 source、sink、channel 的配置。如图 5 中是配置文件 example.conf 里面的内容，其中 agent_foo 是 agent 名字。然后在启动 agent 时，使用一下命令指定这个配置文件：</p>
<div class="codesection" style="border:0px;vertical-align:baseline;">
<pre class="displaycode" style="border:1px solid rgb(204,204,204);font-size:11px;vertical-align:baseline;font-family:'Andale Mono', 'Lucida Console', Monaco, Liberation, fixed, monospace;overflow:auto;clear:right;background-color:rgb(247,247,247) !important;">$ bin/flume-ng agent
--conf-file example.conf \
--name agent_foo \
-Dflume.root.logger=INFO,console</pre>
</div>
<h5 id="N100C7" style="border:0px;font-size:1.166em !important;vertical-align:baseline;">
图 5. example.conf</h5>
<img alt="example.conf" src="http://www.ibm.com/developerworks/cn/data/library/bd-1404flumerevolution/5.jpg" width="700" style="border:0px;vertical-align:baseline;"><h2 id="N100D0" style="border:0px;font-size:1.6em !important;vertical-align:baseline;font-family:'HelveticaNeue-Light', 'Helvetica Neue Light', 'Helvetica Neue', Helvetica, Arial;">
实战 Flume NG</h2>
<p style="border:0px;vertical-align:baseline;color:rgb(34,34,34);line-height:1.5em;font-size:1.166em !important;">
Flume 最常用的使用场景是，从节点收集日志数据，并以一定的格式存放到分布式文件系统 hdfs（hadoop 文件系统）中。下面介绍如何使用 Flume NG 从一个节点收集实时日志，并存放到 hdfs 中。</p>
<p style="border:0px;vertical-align:baseline;color:rgb(34,34,34);line-height:1.5em;font-size:1.166em !important;">
场景说明：</p>
<ul class="ibm-bullet-list" style="border:0px;font-size:1.166em;vertical-align:baseline;list-style:none;color:rgb(51,51,51);line-height:1.5em;"><li style="border:0px;vertical-align:baseline;clear:left;">
场景中有两台主机 host1、host2。</li><li style="border:0px;vertical-align:baseline;clear:left;">
数据源是 host2 上的系统日志文件“/var/log/secure”（登录到系统存取资料的记录，本机的测试系统有多人使用，所以记录在不断的生成）。数据目的地是 hadoop 文件系统 hdfs。</li><li style="border:0px;vertical-align:baseline;clear:left;">
在 host1、host2 上搭建 hadoop 集群。其中 host1 为 namenode、jobtracker，host2 为 datanode、tasktracker。</li></ul><h3 id="N100E1" style="border:0px;font-size:1.4em !important;vertical-align:baseline;font-family:'HelveticaNeue-Light', 'Helvetica Neue Light', 'Helvetica Neue', Helvetica, Arial;">
使用 ng 搭建日志传输场景：flume+hadoop</h3>
<p style="border:0px;vertical-align:baseline;color:rgb(34,34,34);line-height:1.5em;font-size:1.166em !important;">
场景搭建步骤：</p>
<ul class="ibm-bullet-list" style="border:0px;font-size:1.166em;vertical-align:baseline;list-style:none;color:rgb(51,51,51);line-height:1.5em;"><li style="border:0px;vertical-align:baseline;clear:left;">
下载 flume-ng 安装包，并解压到 host2。
<p style="border:0px;vertical-align:baseline;color:rgb(34,34,34);line-height:1.5em;font-size:1em !important;">
Flume 发布了两类包：source 和 bin。Source 包用于开发工作，bin 包用于安装 Flume 搭建日志收集场景。本次实验用的是 <a href="http://www.apache.org/dyn/closer.cgi/flume/1.2.0/apache-flume-1.2.0-bin.tar.gz" rel="nofollow" style="border:0px;vertical-align:baseline;color:rgb(116,82,133);display:inline;">apache-flume-1.2.0-bin.tar.gz</a></p>
</li><li style="border:0px;vertical-align:baseline;clear:left;">
生成配置文件 example.conf。内容如图 6。整个配置分为四部分。表 1 是配置说明。</li><li style="border:0px;vertical-align:baseline;clear:left;">
进入 bin 目录，使用一下命令启动 Flume，开始日志收集，控制台输出如图 7，传输到 hdfs 的文件如图 8。</li></ul><div class="codesection" style="border:0px;vertical-align:baseline;">
<pre class="displaycode" style="border:1px solid rgb(204,204,204);font-size:11px;vertical-align:baseline;font-family:'Andale Mono', 'Lucida Console', Monaco, Liberation, fixed, monospace;overflow:auto;clear:right;background-color:rgb(247,247,247) !important;">./flume-ng agent \
--conf-file ../example.conf \
--name agent_ff \
-Dflume.root.logger=INFO,cnsole</pre>
</div>
<h5 id="N100F9" style="border:0px;font-size:1.166em !important;vertical-align:baseline;">
图 6. example.conf</h5>
<img alt="example.conf" src="http://www.ibm.com/developerworks/cn/data/library/bd-1404flumerevolution/6.jpg" width="541" style="border:0px;vertical-align:baseline;"><h5 style="border:0px;font-size:1.166em !important;vertical-align:baseline;">
表 1. flume-conf.xml</h5>
<table border="1" cellpadding="1" cellspacing="1" class="ibm-data-table" summary="带表头、所有列左对齐的样式" width="100%" style="vertical-align:baseline;border-collapse:collapse;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(204,204,204);border-left-width:0px;border-left-style:none;border-right-width:0px;border-right-style:none;"><thead style="vertical-align:baseline;"><tr style="vertical-align:baseline;border-width:1px 0px 0px;border-style:solid none none;border-top-color:rgb(221,221,221);"><th scope="col" style="vertical-align:top;border-width:1px 0px 0px;border-style:solid none none;border-top-color:rgb(221,221,221);">
Properties Name</th>
<th scope="col" style="vertical-align:top;border-width:1px 0px 0px;border-style:solid none none;border-top-color:rgb(221,221,221);">
Description</th>
</tr></thead><tbody style="vertical-align:baseline;"><tr style="vertical-align:baseline;"><th class="tb-row" scope="row" style="vertical-align:top;border-left-width:0px;border-left-style:none;border-top-width:2px;border-top-style:solid;border-top-color:rgb(153,153,153);border-right-width:0px;border-right-style:none;">
agent_ff</th>
<td style="vertical-align:top;border-left-width:3px;border-left-style:solid;border-left-color:rgb(255,255,255);border-top-width:2px;border-top-style:solid;border-top-color:rgb(153,153,153);color:rgb(85,85,85);border-right-width:0px;border-right-style:none;">
用来收集日志信息的 agent 节点名称</td>
</tr><tr style="vertical-align:baseline;"><th class="tb-row" scope="row" style="vertical-align:top;border-left-width:0px;border-left-style:none;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-right-width:0px;border-right-style:none;">
agent_ff.source</th>
<td style="vertical-align:top;border-left-width:3px;border-left-style:solid;border-left-color:rgb(255,255,255);border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);color:rgb(85,85,85);border-right-width:0px;border-right-style:none;">
需要收集的信息源，名字：tailsource-ff</td>
</tr><tr style="vertical-align:baseline;"><th class="tb-row" scope="row" style="vertical-align:top;border-left-width:0px;border-left-style:none;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-right-width:0px;border-right-style:none;">
agent_ff.sinks</th>
<td style="vertical-align:top;border-left-width:3px;border-left-style:solid;border-left-color:rgb(255,255,255);border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);color:rgb(85,85,85);border-right-width:0px;border-right-style:none;">
日志需要被收集到此处，名字：hdfsSink-ff。</td>
</tr><tr style="vertical-align:baseline;"><th class="tb-row" scope="row" style="vertical-align:top;border-left-width:0px;border-left-style:none;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-right-width:0px;border-right-style:none;">
agent_ff.channels</th>
<td style="vertical-align:top;border-left-width:3px;border-left-style:solid;border-left-color:rgb(255,255,255);border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);color:rgb(85,85,85);border-right-width:0px;border-right-style:none;">
日志的收集需要通过此管道，名字：memoryChannel-ff。</td>
</tr><tr style="vertical-align:baseline;"><th class="tb-row" scope="row" style="vertical-align:top;border-left-width:0px;border-left-style:none;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-right-width:0px;border-right-style:none;">
tailsource-ff.type</th>
<td style="vertical-align:top;border-left-width:3px;border-left-style:solid;border-left-color:rgb(255,255,255);border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);color:rgb(85,85,85);border-right-width:0px;border-right-style:none;">
定义 source 的类型，此处 exec 代表数据源是 exec 命令</td>
</tr><tr style="vertical-align:baseline;"><th class="tb-row" scope="row" style="vertical-align:top;border-left-width:0px;border-left-style:none;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-right-width:0px;border-right-style:none;">
tailsource-ff.command</th>
<td style="vertical-align:top;border-left-width:3px;border-left-style:solid;border-left-color:rgb(255,255,255);border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);color:rgb(85,85,85);border-right-width:0px;border-right-style:none;">
定义具体命令，此处是对文件/var/log/secure 做 tail</td>
</tr><tr style="vertical-align:baseline;"><th class="tb-row" scope="row" style="vertical-align:top;border-left-width:0px;border-left-style:none;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-right-width:0px;border-right-style:none;">
tailsource-ff.channels</th>
<td style="vertical-align:top;border-left-width:3px;border-left-style:solid;border-left-color:rgb(255,255,255);border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);color:rgb(85,85,85);border-right-width:0px;border-right-style:none;">
数据传输的管道，此处的管道名称应该和 sink 相同。从而将 source、sink 通过 channels 进行连接。</td>
</tr><tr style="vertical-align:baseline;"><th class="tb-row" scope="row" style="vertical-align:top;border-left-width:0px;border-left-style:none;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-right-width:0px;border-right-style:none;">
memoryChannel-ff.type</th>
<td style="vertical-align:top;border-left-width:3px;border-left-style:solid;border-left-color:rgb(255,255,255);border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);color:rgb(85,85,85);border-right-width:0px;border-right-style:none;">
管道类型，代表事件存储的方式。Source 产生事件，sink 移除事件，以此代表数据传输完成。目前 Flume 支持 6 种 channel。此处是 momery，代表事件是存在内存里</td>
</tr><tr style="vertical-align:baseline;"><th class="tb-row" scope="row" style="vertical-align:top;border-left-width:0px;border-left-style:none;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-right-width:0px;border-right-style:none;">
memoryChannel-ff.capacity</th>
<td style="vertical-align:top;border-left-width:3px;border-left-style:solid;border-left-color:rgb(255,255,255);border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);color:rgb(85,85,85);border-right-width:0px;border-right-style:none;">
管道里可以存放的最多的事件数目。此处代表 memoryChannel-ff 最多可存放 1000 个事件。</td>
</tr><tr style="vertical-align:baseline;"><th class="tb-row" scope="row" style="vertical-align:top;border-left-width:0px;border-left-style:none;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-right-width:0px;border-right-style:none;">
hdfsSink-ff.type</th>
<td style="vertical-align:top;border-left-width:3px;border-left-style:solid;border-left-color:rgb(255,255,255);border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);color:rgb(85,85,85);border-right-width:0px;border-right-style:none;">
数据目的地的类型，此处是将数据存放在 hdfs 中。</td>
</tr><tr style="vertical-align:baseline;"><th class="tb-row" scope="row" style="vertical-align:top;border-left-width:0px;border-left-style:none;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-right-width:0px;border-right-style:none;">
hdfsSink-ff.channel</th>
<td style="vertical-align:top;border-left-width:3px;border-left-style:solid;border-left-color:rgb(255,255,255);border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);color:rgb(85,85,85);border-right-width:0px;border-right-style:none;">
定义和 source 相关联的管道。</td>
</tr><tr style="vertical-align:baseline;"><th class="tb-row" scope="row" style="vertical-align:top;border-left-width:0px;border-left-style:none;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-right-width:0px;border-right-style:none;">
hdfsSink-ff.hdfs.path</th>
<td style="vertical-align:top;border-left-width:3px;border-left-style:solid;border-left-color:rgb(255,255,255);border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);color:rgb(85,85,85);border-right-width:0px;border-right-style:none;">
数据存放在 hdfs 中的位置。</td>
</tr><tr style="vertical-align:baseline;"><th class="tb-row" scope="row" style="vertical-align:top;border-left-width:0px;border-left-style:none;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-right-width:0px;border-right-style:none;">
hdfsSink-ff.hdfs.filePrefix</th>
<td style="vertical-align:top;border-left-width:3px;border-left-style:solid;border-left-color:rgb(255,255,255);border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);color:rgb(85,85,85);border-right-width:0px;border-right-style:none;">
收集到的数据存放的文件以此为前缀。如图 8。</td>
</tr><tr style="vertical-align:baseline;"><th class="tb-row" scope="row" style="vertical-align:top;border-left-width:0px;border-left-style:none;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-right-width:0px;border-right-style:none;">
hdfsSink-ff.hdfs.round, hdfsSink-ff.hdfs.roundValue, hdfsSink-ff.hdfs.roundUnit</th>
<td style="vertical-align:top;border-left-width:3px;border-left-style:solid;border-left-color:rgb(255,255,255);border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);color:rgb(85,85,85);border-right-width:0px;border-right-style:none;">
定义在 hdfs 中生成的文件的时间戳。此处代表将 hdfs 中的文件的时间戳，向下取整到上一个十分钟内。比如说，在 2012 年 6 月 12 号上午 11:54:34 生成的事件，在 hdfs 中生成的路径将是/flume/events/2012-06-12/1150/00。</td>
</tr></tbody></table><h5 id="N10178" style="border:0px;font-size:1.166em !important;vertical-align:baseline;">
图 7. NG console</h5>
<img alt="NG console" src="http://www.ibm.com/developerworks/cn/data/library/bd-1404flumerevolution/7.jpg" width="699" style="border:0px;vertical-align:baseline;"><h5 id="N10182" style="border:0px;font-size:1.166em !important;vertical-align:baseline;">
图 8. hdfs</h5>
<img alt="hdfs" src="http://www.ibm.com/developerworks/cn/data/library/bd-1404flumerevolution/8.jpg" width="748" style="border:0px;vertical-align:baseline;"><h2 id="N1018B" style="border:0px;font-size:1.6em !important;vertical-align:baseline;font-family:'HelveticaNeue-Light', 'Helvetica Neue Light', 'Helvetica Neue', Helvetica, Arial;">
Flume OG vs Flume NG：用户体验对比</h2>
<p style="border:0px;vertical-align:baseline;color:rgb(34,34,34);line-height:1.5em;font-size:1.166em !important;">
整体上看，NG 的用户体验要比 OG 好得多，这一点从用户文档上就可见一斑。OG 版本的使用文档有 90 多页，而 OG 只用 20 多页的内容就完成了新版 Flume 的使用说明。对应于上一节“实战 Flume NG”，我们使用和 NG 中同样的场景说明，下面介绍如何使用 OG 搭建同样的日志收集场景。用户可以从场景的搭建上明显的看出差别：NG 的整个过程只涉及到 hadoop、agent，而 OG 则需要涉及到 hadoop、zookeeper、master、agent、flume-conf.xml。</p>
<h3 id="N10193" style="border:0px;font-size:1.4em !important;vertical-align:baseline;font-family:'HelveticaNeue-Light', 'Helvetica Neue Light', 'Helvetica Neue', Helvetica, Arial;">
使用 og 搭建日志传输场景：flume+zookeeper+hadoop</h3>
<p style="border:0px;vertical-align:baseline;color:rgb(34,34,34);line-height:1.5em;font-size:1.166em !important;">
场景搭建步骤：</p>
<ul class="ibm-bullet-list" style="border:0px;font-size:1.166em;vertical-align:baseline;list-style:none;color:rgb(51,51,51);line-height:1.5em;"><li style="border:0px;vertical-align:baseline;clear:left;">
下载 zookeeper 安装包，并在 host2 上安装 zookeeper-3.4.3。请参考：<a href="http://zookeeper.apache.org/doc/r3.4.3/zookeeperStarted.html" rel="nofollow" style="border:0px;vertical-align:baseline;color:rgb(116,82,133);display:inline;">zookeeperStarted</a>。</li><li style="border:0px;vertical-align:baseline;clear:left;">
下载 flume-0.94.0（OG），并解压在 host2 上。</li><li style="border:0px;vertical-align:baseline;clear:left;">
配置文件 conf/flume-conf.xml，必须配置的信息如表 2。</li><li style="border:0px;vertical-align:baseline;clear:left;">
进入 bin 目录，使用一下命令启动 flume master、agent。
<div class="codesection" style="border:0px;vertical-align:baseline;">
<pre class="displaycode" style="border:1px solid rgb(204,204,204);font-size:11px;vertical-align:baseline;font-family:'Andale Mono', 'Lucida Console', Monaco, Liberation, fixed, monospace;overflow:auto;color:rgb(0,0,0);clear:right;background-color:rgb(247,247,247) !important;line-height:1.2 !important;">master: ./flume-daemon.sh start master
agent: ./flume node -n agent-ff</pre>
</div>
</li><li style="border:0px;vertical-align:baseline;clear:left;">
进入 master 页面：http://host2:35871/flumemaster.jsp。配置 source、sink。如图 9。“Submit Query”后 Flume 便开始收集数据。</li><li style="border:0px;vertical-align:baseline;clear:left;">
agent-ff 控制台输出如图 10。</li></ul><h5 style="border:0px;font-size:1.166em !important;vertical-align:baseline;">
表 2. flume-conf.xml</h5>
<table border="1" cellpadding="1" cellspacing="1" class="ibm-data-table" summary="带表头、所有列左对齐的样式" width="100%" style="vertical-align:baseline;border-collapse:collapse;border-bottom-width:1px;border-bottom-style:solid;border-bottom-color:rgb(204,204,204);border-left-width:0px;border-left-style:none;border-right-width:0px;border-right-style:none;"><thead style="vertical-align:baseline;"><tr style="vertical-align:baseline;border-width:1px 0px 0px;border-style:solid none none;border-top-color:rgb(221,221,221);"><th scope="col" style="vertical-align:top;border-width:1px 0px 0px;border-style:solid none none;border-top-color:rgb(221,221,221);">
属性（Property）</th>
<th scope="col" style="vertical-align:top;border-width:1px 0px 0px;border-style:solid none none;border-top-color:rgb(221,221,221);">
赋值（Value）</th>
</tr></thead><tbody style="vertical-align:baseline;"><tr style="vertical-align:baseline;"><th class="tb-row" scope="row" style="vertical-align:top;border-left-width:0px;border-left-style:none;border-top-width:2px;border-top-style:solid;border-top-color:rgb(153,153,153);border-right-width:0px;border-right-style:none;">
flume.master.servers</th>
<td style="vertical-align:top;border-left-width:3px;border-left-style:solid;border-left-color:rgb(255,255,255);border-top-width:2px;border-top-style:solid;border-top-color:rgb(153,153,153);color:rgb(85,85,85);border-right-width:0px;border-right-style:none;">
host2</td>
</tr><tr style="vertical-align:baseline;"><th class="tb-row" scope="row" style="vertical-align:top;border-left-width:0px;border-left-style:none;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-right-width:0px;border-right-style:none;">
flume.master.store</th>
<td style="vertical-align:top;border-left-width:3px;border-left-style:solid;border-left-color:rgb(255,255,255);border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);color:rgb(85,85,85);border-right-width:0px;border-right-style:none;">
zookeeper</td>
</tr><tr style="vertical-align:baseline;"><th class="tb-row" scope="row" style="vertical-align:top;border-left-width:0px;border-left-style:none;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-right-width:0px;border-right-style:none;">
flume.master.serverid</th>
<td style="vertical-align:top;border-left-width:3px;border-left-style:solid;border-left-color:rgb(255,255,255);border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);color:rgb(85,85,85);border-right-width:0px;border-right-style:none;">
0</td>
</tr><tr style="vertical-align:baseline;"><th class="tb-row" scope="row" style="vertical-align:top;border-left-width:0px;border-left-style:none;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-right-width:0px;border-right-style:none;">
flume.master.zk.use.external</th>
<td style="vertical-align:top;border-left-width:3px;border-left-style:solid;border-left-color:rgb(255,255,255);border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);color:rgb(85,85,85);border-right-width:0px;border-right-style:none;">
true</td>
</tr><tr style="vertical-align:baseline;"><th class="tb-row" scope="row" style="vertical-align:top;border-left-width:0px;border-left-style:none;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);border-right-width:0px;border-right-style:none;">
flume.master.zk.servers</th>
<td style="vertical-align:top;border-left-width:3px;border-left-style:solid;border-left-color:rgb(255,255,255);border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);color:rgb(85,85,85);border-right-width:0px;border-right-style:none;">
host2:2181</td>
</tr></tbody></table><h5 id="N101E6" style="border:0px;font-size:1.166em !important;vertical-align:baseline;">
图 9. OG 配置</h5>
<img alt="OG 配置" src="http://www.ibm.com/developerworks/cn/data/library/bd-1404flumerevolution/9.jpg" width="713" style="border:0px;vertical-align:baseline;"><h5 id="N101F0" style="border:0px;font-size:1.166em !important;vertical-align:baseline;">
图 10. OG console</h5>
<img alt="OG console" src="http://www.ibm.com/developerworks/cn/data/library/bd-1404flumerevolution/10.jpg" width="784" style="border:0px;vertical-align:baseline;"><h2 id="N101F9" style="border:0px;font-size:1.6em !important;vertical-align:baseline;font-family:'HelveticaNeue-Light', 'Helvetica Neue Light', 'Helvetica Neue', Helvetica, Arial;">
结束语</h2>
<p style="border:0px;vertical-align:baseline;color:rgb(34,34,34);line-height:1.5em;font-size:1.166em !important;">
本文通过对比，从核心组件和用户的角度，阐述了 Flume NG 给 Flume 带来的第一次革命。从核心组件来讲，NG 简化核心组件，移除了 OG 版本代码工程臃肿、核心组件设计不合理、核心配置不标准等缺点；另外 NG 脱离了 Flume 稳定性对 zookeeper 的依赖。从用户角度来讲，NG 版本对用户要求大大降低：安装过程除了 java 无需配置复杂的 Flume 相关属性，也无需搭建 zookeeper 集群，安装过程几乎零工作量；数据流的配置过程更加简单、合理，只需要实现 source、sink、channel
 的简单配置即可。</p>
</div>
</div>
<div class="ibm-columns" style="border:0px;vertical-align:baseline;clear:both;overflow:hidden;">
<div class="ibm-col-6-4" style="border:0px;vertical-align:baseline;min-height:1px;width:620px;clear:none;">
<h2 id="resources" class="ibm-rule" style="font-size:1.6em !important;line-height:19px;border-width:2px 0px 0px;border-top-style:solid;border-top-color:rgb(102,102,102);vertical-align:baseline;font-family:'HelveticaNeue-Light', 'Helvetica Neue Light', 'Helvetica Neue', Helvetica, Arial;">
参考资料</h2>
<h3 class="dw-resources-category-heading" style="font-size:1.166em !important;line-height:19px;border:0px;vertical-align:baseline;font-family:'HelveticaNeue-Light', 'Helvetica Neue Light', 'Helvetica Neue', Helvetica, Arial;">
学习</h3>
<ul class="ibm-bullet-list" style="font-size:1.166em;line-height:1.5em;border:0px;vertical-align:baseline;list-style:none;color:rgb(51,51,51);"><li style="border:0px;vertical-align:baseline;clear:left;">
访问 <a href="https://db2id.torolab.ibm.com/bigins_v1.1.1/index.jsp" rel="nofollow" style="border:0px;vertical-align:baseline;color:rgb(116,82,133);display:inline;">BigInsights Information Center</a>，了解关于
 BigInsights 的详细信息。</li><li style="border:0px;vertical-align:baseline;clear:left;">
查看 <a href="http://flume.apache.org/" rel="nofollow" style="border:0px;vertical-align:baseline;color:rgb(116,82,133);display:inline;">Flume 官方网站</a>，了解 Flume 基本知识和最新信息。</li><li style="border:0px;vertical-align:baseline;clear:left;">
通过<a href="http://hadoop.apache.org/" rel="nofollow" style="border:0px;vertical-align:baseline;color:rgb(116,82,133);display:inline;">Hadoop 官方网站</a>，了解 Hadoop 基本知识和最新信息。</li><li style="border:0px;vertical-align:baseline;clear:left;">
通过 <a href="http://zookeeper.apache.org/" rel="nofollow" style="border:0px;vertical-align:baseline;color:rgb(116,82,133);display:inline;">Zookeeper 官网</a>，了解 Zookeeper 基本知识和最新信息。</li><li style="border:0px;vertical-align:baseline;clear:left;">
在 <a href="http://www.ibm.com/developerworks/cn/opensource/os-log-process-hadoop/" rel="nofollow" style="border:0px;vertical-align:baseline;color:rgb(116,82,133);display:inline;">实践：使用 Apache Hadoop
 处理日志</a></li></ul><div><span style="color:#333333;"><span style="font-size:15px;line-height:22.375px;"><br></span></span></div>
<div><span style="color:#333333;"><span style="font-size:15px;line-height:22.375px;">转载 ：<a href="http://www.ibm.com/developerworks/cn/data/library/bd-1404flumerevolution/index.html" rel="nofollow">http://www.ibm.com/developerworks/cn/data/library/bd-1404flumerevolution/index.html</a></span></span></div>
</div>
</div>
</div>
</div>
</div>
</div>
            </div>
                </div>