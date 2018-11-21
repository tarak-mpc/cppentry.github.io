---
layout:     post
title:      分布式海量日志采集、聚合和传输系统：Cloudera Flume
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="intro">
<p><span class="f_red_12px">导读</span>：本文介绍了分布式海量日志采集、聚合和传输系统Cloudera Flume相关内容，Flume是Cloudera提供的日志收集系统，Flume支持在日志系统中定制各类数据发送方。</p>
<p><span class="f_red_12px">关键词：</span><a href="http://www.searchdatabase.com.cn/search.aspx?cx=006311256994434649565:dv5h1scnzoy&amp;cof=FORID:9&amp;q=Flume" rel="nofollow">Flume</a>
<a href="http://www.searchdatabase.com.cn/search.aspx?cx=006311256994434649565:dv5h1scnzoy&amp;cof=FORID:9&amp;q=Cloudera" rel="nofollow">
Cloudera</a> <a href="http://www.searchdatabase.com.cn/search.aspx?cx=006311256994434649565:dv5h1scnzoy&amp;cof=FORID:9&amp;q=%E6%97%A5%E5%BF%97%E6%94%B6%E9%9B%86" rel="nofollow">
日志收集</a> <a href="http://www.searchdatabase.com.cn/search.aspx?cx=006311256994434649565:dv5h1scnzoy&amp;cof=FORID:9&amp;q=%E5%88%86%E5%B8%83%E5%BC%8F%E6%95%B0%E6%8D%AE%E5%BA%93" rel="nofollow">
分布式数据库</a> </p>
</div>
                                                                                 
<div class="maintext" id="maintext">
<p>　　Flume是Cloudera提供的日志收集系统，Flume支持在日志系统中定制各类数据发送方，用于收集数据;同时，Flume提供对数据进行简单处理，并写到各种数据接受方(可定制)的能力。</p>
<p>　　Flume是一个分布式、可靠、和高可用的海量日志采集、聚合和传输的系统。</p>
<p>　　 </p>
<center><img id="eWebEditor_TempElement_Img" src="http://www.searchdatabase.com.cn/upload/article/2011/2011-03-02-10-28-12.jpg" border="0" alt=""></center>
<p></p>
<p></p>
<p></p>
<p>　　上图的Flume的Architecture，在Flume中，最重要的抽象是data flow(数据流)，data flow描述了数据从产生，传输、处理并最终写入目标的一条路径。在上图中，实线描述了data flow。</p>
<p>　　其中，Agent用于采集数据，agent是flume中产生数据流的地方，同时，agent会将产生的数据流传输到collector。对应的，collector用于对数据进行聚合，往往会产生一个更大的流。</p>
<p>　　Flume提供了从console(控制台)、RPC(Thrift-RPC)、text(文件)、tail(UNIX tail)、syslog(syslog日志系统，支持TCP和UDP等2种模式)，exec(命令执行)等数据源上收集数据的能力。同时，Flume的数据接受方，可以是console(控制台)、text(文件)、dfs(HDFS文件)、RPC(Thrift-RPC)和syslogTCP(TCP syslog日志系统)等。</p>
<p>　　其中，收集数据有2种主要工作模式，如下：</p>
<p>　　Push Sources：外部系统会主动地将数据推送到Flume中，如RPC、syslog。</p>
<p>　　Polling Sources：Flume到外部系统中获取数据，一般使用轮询的方式，如text和exec。</p>
<p>　　注意，在Flume中，agent和collector对应，而source和sink对应。Source和sink强调发送、接受方的特性(如数据格式、编码等)，而agent和collector关注功能。</p>
<p>　　Flume Master用于管理数据流的配置，如下图。</p>
<p>　　 </p>
<center><img src="http://www.searchdatabase.com.cn/upload/article/2011/2011-03-02-10-29-13.jpg" border="0" alt=""></center>
<p></p>
<p></p>
<p></p>
<p>　　为了保证可扩展性，Flume采用了多Master的方式。为了保证配置数据的一致性，Flume引入了ZooKeeper，用于保存配置数据，ZooKeeper本身可保证配置数据的一致性和高可用，另外，在配置数据发生变化时，ZooKeeper可以通知Flume Master节点。</p>
<p>　　Flume Master间使用gossip协议同步数据。</p>
<p>　　下面简要分析Flume如何支持Reliability、Scalability、Manageability和Extensibility。</p>
<p>　　Reliability：Flume提供3中数据可靠性选项，包括End-to-end、Store on failure和Best effort。其中End-to-end使用了磁盘日志和接受端Ack的方式，保证Flume接受到的数据会最终到达目的。Store on failure在目的不可用的时候，数据会保持在本地硬盘。和End-to-end不同的是，如果是进程出现问题，Store on failure可能会丢失部分数据。Best effort不做任何QoS保证。</p>
<p>　　Scalability：Flume的3大组件：collector、master和storage tier都是可伸缩的。需要注意的是，Flume中对事件的处理不需要带状态，它的Scalability可以很容易实现。</p>
<p>　　Manageability：Flume利用ZooKeeper和gossip，保证配置数据的一致性、高可用。同时，多Master，保证Master可以管理大量的节点。</p>
<p>　　Extensibility：基于Java，用户可以为Flume添加各种新的功能，如通过继承Source，用户可以实现自己的数据接入方式，实现Sink的子类，用户可以将数据写往特定目标，同时，通过SinkDecorator，用户可以对数据进行一定的预处理。</p>
</div>
            </div>
                </div>