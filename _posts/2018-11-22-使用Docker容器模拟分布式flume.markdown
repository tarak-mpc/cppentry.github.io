---
layout:     post
title:      使用Docker容器模拟分布式flume
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="使用docker容器模拟分布式flume">使用Docker容器模拟分布式flume</h1>

<p>使用Docker模拟分布式flume，agent1通过avro接口连接agent2和agent3，agent2和agent3采用负荷分担方式。</p>

<p>使用docker-hub上的cogniteev/flume作为原始镜像：</p>

<blockquote>
  <p>docker pull cogniteev/flume <br>
  镜像中确实一些必要组件，请自行安装，如log4j</p>
</blockquote>

<p>启动agent2和agent3并命名为flume_center_master和flume_center_slave，配置端口44444和45454分别映射虚拟机上，用作avro监听和状态监控，命令如下：</p>

<blockquote>
  <p>docker run -i -t –name flume_center_master -p 24444:44444 -p 25454:45454 cogniteev/flume /bin/bash <br>
  docker run -i -t –name flume_center_slave -p 34444:44444 -p 35454:45454 cogniteev/flume /bin/bash</p>
</blockquote>

<p>agent2的flume配置如下，路径：var/flume/conf/collect1.conf：</p>

<blockquote>
  <p>//Name the compents on this agent <br>
  a2.sources = r1 <br>
  a2.sinks = k1 <br>
  a2.channels = c1</p>
  
  <p>//Describe/configure the source <br>
  a2.sources.r1.type = avro <br>
  a2.sources.r1.channels = c1 <br>
  a2.sources.r1.bind = 0.0.0.0 <br>
  a2.sources.r1.port = 44444</p>
  
  <p>//Describe the sink <br>
  a2.sinks.k1.type = logger <br>
  a2.sinks.k1.channel = c1</p>
  
  <p>//Use a channel which buffers events in memory <br>
  a2.channels.c1.type = memory <br>
  a2.channels.c1.capacity = 1000 <br>
  a2.channels.c1.transactionCapacity = 100</p>
</blockquote>

<p><em>注：由于是在docker中运行，a2.sources.r1.bind = 0.0.0.0是必须这样的，否则在容器之外无法访问</em></p>

<p>agent3的flume配置与agent2类似，仅将agent命名改为a3，文件名改为collect2</p>

<p>启动flume-ng:</p>

<blockquote>
  <p>bin/flume-ng agent –conf conf –conf-file conf/collect1.conf –name a2 -Dflume.root.logger=INFO,console -Dflume.monitoring.type=http -Dflume.monitoring.port=45454 <br>
  bin/flume-ng agent –conf conf –conf-file conf/collect2.conf –name a3 -Dflume.root.logger=INFO,console -Dflume.monitoring.type=http -Dflume.monitoring.port=45454</p>
</blockquote>

<p>启动agent1并命名为flume_1_1，配置端口44444和45454分别映射虚拟机上，用作http监听和状态监控，命令如下： <br>
docker run -i -t –name flume_1_1 -p 44444:44444 -p 45454:45454 cogniteev/flume /bin/bash</p>

<p>1.agent1配置：</p>

<blockquote>
  <p>//Name the components on this agent <br>
  a1.sources = r1 <br>
  a1.sinks = k1 k2 <br>
  a1.channels = c1</p>
  
  <p>//Describe the sinkgroups <br>
  a1.sinkgroups = g1 <br>
  a1.sinkgroups.g1.sinks = k1 k2 <br>
  a1.sinkgroups.g1.processor.type = load_balance <br>
  a1.sinkgroups.g1.processor.backoff = true <br>
  a1.sinkgroups.g1.processer.selector = round_robin</p>
  
  <p>//Describe/configure the source <br>
  a1.sources = r1 <br>
  a1.channels = c1 <br>
  a1.sources.r1.type = http <br>
  a1.sources.r1.port = 44444 <br>
  a1.sources.r1.channels = c1 <br>
  a1.sources.r1.handler = org.apache.flume.source.http.JSONHandler <br>
  a1.sources.r1.handler.nickname = post test</p>
  
  <p>//Describe the sink <br>
  a1.sinks.k1.type = avro <br>
  a1.sinks.k1.hostname = 192.168.99.100 <br>
  a1.sinks.k1.port = 24444 <br>
  a1.sinks.k2.type = avro <br>
  a1.sinks.k2.hostname = 192.168.99.100 <br>
  a1.sinks.k2.port = 344444</p>
  
  <p>//Use a channel which buffers events in memory <br>
  a1.channels.c1.type = memory <br>
  a1.channels.c1.capacity = 1000 <br>
  a1.channels.c1.transactionCapacity = 100</p>
  
  <p>//Bind the source and sink to the channel <br>
  a1.sources.r1.channels = c1 <br>
  a1.sinks.k1.channel = c1 <br>
  a1.sinks.k2.channel = c1</p>
</blockquote>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>