---
layout:     post
title:      flume-NG整合hdfs和kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>flume版本：apache-flume-1.7.0-bin.tar <br>
hadoop版本：hadoop-2.7.3 <br>
kafka版本：kafka_2.11-0.10.2.1 <br>
zookeeper版本：zookeeper-3.4.6 <br>
    最近在安装搭建flume和kafka这两款软件，网上有很多这方面的简介，在这里，我把flume—NG和kafka、hdfs整合在一起。flume作为消息采集和传输系统，将数据落地到hdfs进行备份，然后就是kafka作为消息中间件为spark-streaming提供数据支持。当我们搜集某个网站的日志的时候，我们就可以使用flume监控log的一个文件或者是一个目录，每当有新的log，flume就可以将其持久化到hdfs，然后将这个消息发给kafka，kafka在对消息进行分发，处理，实时计算等等。 <br>
    在这里我准备了5台服务器，为了方便介绍，我画了一幅图，不是很好看，意思到位就行。 <br>
    <img src="https://img-blog.csdn.net/20170608153549074?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZGF5X29uZV9zdGVw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
    配置文件： <br>
<strong><em>flume-kafka-hdfs-client.properties</em></strong></p>

<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor"># set agent name</span>
agent<span class="hljs-preprocessor">.sources</span> = <span class="hljs-built_in">r1</span>  
agent<span class="hljs-preprocessor">.channels</span> = c_kafka c_hdfs  
agent<span class="hljs-preprocessor">.sinks</span> = s_kafka_k1 s_kafka_k2 s_kafka_k3 s_hdfs_k1 s_hdfs_k2

<span class="hljs-preprocessor"># set group</span>
agent1<span class="hljs-preprocessor">.sinkgroups</span> = g_kafka g_hdfs

<span class="hljs-preprocessor"># set sources</span>
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.channels</span> = c_kafka c_hdfs  
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.type</span> = exec  
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.command</span> = tail -F /root/logs/a<span class="hljs-preprocessor">.txt</span> 
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.inputCharset</span> = UTF-<span class="hljs-number">8</span>

<span class="hljs-preprocessor"># set kafka channels</span>
agent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>_kafka<span class="hljs-preprocessor">.type</span> = memory
agent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>_kafka<span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">1000</span>
agent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>_kafka<span class="hljs-preprocessor">.transactionCapacity</span> = <span class="hljs-number">100</span>

<span class="hljs-preprocessor"># set hdfs channels</span>
agent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>_hdfs<span class="hljs-preprocessor">.type</span> = memory
agent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>_hdfs<span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">1000</span>
agent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>_hdfs<span class="hljs-preprocessor">.transactionCapacity</span> = <span class="hljs-number">100</span>

<span class="hljs-preprocessor"># set kafka sink1</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.s</span>_kafka_k1<span class="hljs-preprocessor">.channel</span> = c_kafka
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.s</span>_kafka_k1<span class="hljs-preprocessor">.type</span> = avro
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.s</span>_kafka_k1<span class="hljs-preprocessor">.hostname</span> = <span class="hljs-number">192.168</span><span class="hljs-number">.183</span><span class="hljs-number">.103</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.s</span>_kafka_k1<span class="hljs-preprocessor">.port</span> = <span class="hljs-number">52021</span>

<span class="hljs-preprocessor"># set kafka sink2</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.s</span>_kafka_k2<span class="hljs-preprocessor">.channel</span> = c_kafka
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.s</span>_kafka_k2<span class="hljs-preprocessor">.type</span> = avro
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.s</span>_kafka_k2<span class="hljs-preprocessor">.hostname</span> = <span class="hljs-number">192.168</span><span class="hljs-number">.183</span><span class="hljs-number">.104</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.s</span>_kafka_k2<span class="hljs-preprocessor">.port</span> = <span class="hljs-number">52021</span>

<span class="hljs-preprocessor"># set kafka sink3</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.s</span>_kafka_k3<span class="hljs-preprocessor">.channel</span> = c_kafka
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.s</span>_kafka_k3<span class="hljs-preprocessor">.type</span> = avro
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.s</span>_kafka_k3<span class="hljs-preprocessor">.hostname</span> = <span class="hljs-number">192.168</span><span class="hljs-number">.183</span><span class="hljs-number">.105</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.s</span>_kafka_k3<span class="hljs-preprocessor">.port</span> = <span class="hljs-number">52021</span>

<span class="hljs-preprocessor"># set hdfs sink1</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.s</span>_hdfs_k1<span class="hljs-preprocessor">.channel</span> = c_hdfs
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.s</span>_hdfs_k1<span class="hljs-preprocessor">.type</span> = avro
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.s</span>_hdfs_k1<span class="hljs-preprocessor">.hostname</span> = <span class="hljs-number">192.168</span><span class="hljs-number">.183</span><span class="hljs-number">.102</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.s</span>_hdfs_k1<span class="hljs-preprocessor">.port</span> = <span class="hljs-number">52020</span>

<span class="hljs-preprocessor"># set hdfs sink2</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.s</span>_hdfs_k1<span class="hljs-preprocessor">.channel</span> = c_hdfs
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.s</span>_hdfs_k1<span class="hljs-preprocessor">.type</span> = avro
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.s</span>_hdfs_k1<span class="hljs-preprocessor">.hostname</span> = <span class="hljs-number">192.168</span><span class="hljs-number">.183</span><span class="hljs-number">.103</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.s</span>_hdfs_k1<span class="hljs-preprocessor">.port</span> = <span class="hljs-number">52020</span>

<span class="hljs-preprocessor"># set sink group</span>
agent<span class="hljs-preprocessor">.sinkgroups</span><span class="hljs-preprocessor">.g</span>_kafka<span class="hljs-preprocessor">.sinks</span> = s_kafka_k1 s_kafka_k2 s_kafka_k3
agent<span class="hljs-preprocessor">.sinkgroups</span><span class="hljs-preprocessor">.g</span>_hdfs<span class="hljs-preprocessor">.sinks</span> = s_hdfs_k1 s_hdfs_k2

<span class="hljs-preprocessor"># set failover_kafka</span>
agent<span class="hljs-preprocessor">.sinkgroups</span><span class="hljs-preprocessor">.g</span>_kafka<span class="hljs-preprocessor">.processor</span><span class="hljs-preprocessor">.type</span> = failover
agent<span class="hljs-preprocessor">.sinkgroups</span><span class="hljs-preprocessor">.g</span>_kafka<span class="hljs-preprocessor">.processor</span><span class="hljs-preprocessor">.priority</span><span class="hljs-preprocessor">.s</span>_kafka_k1 = <span class="hljs-number">1</span>
agent<span class="hljs-preprocessor">.sinkgroups</span><span class="hljs-preprocessor">.g</span>_kafka<span class="hljs-preprocessor">.processor</span><span class="hljs-preprocessor">.priority</span><span class="hljs-preprocessor">.s</span>_kafka_k2 = <span class="hljs-number">10</span>
agent<span class="hljs-preprocessor">.sinkgroups</span><span class="hljs-preprocessor">.g</span>_kafka<span class="hljs-preprocessor">.processor</span><span class="hljs-preprocessor">.priority</span><span class="hljs-preprocessor">.s</span>_kafka_k3 = <span class="hljs-number">100</span>
agent<span class="hljs-preprocessor">.sinkgroups</span><span class="hljs-preprocessor">.g</span>_kafka<span class="hljs-preprocessor">.processor</span><span class="hljs-preprocessor">.maxpenalty</span> = <span class="hljs-number">10000</span>

<span class="hljs-preprocessor"># set failover_hdfs</span>
agent<span class="hljs-preprocessor">.sinkgroups</span><span class="hljs-preprocessor">.g</span>_hdfs<span class="hljs-preprocessor">.processor</span><span class="hljs-preprocessor">.type</span> = failover
agent<span class="hljs-preprocessor">.sinkgroups</span><span class="hljs-preprocessor">.g</span>_hdfs<span class="hljs-preprocessor">.processor</span><span class="hljs-preprocessor">.priority</span><span class="hljs-preprocessor">.s</span>_hdfs_k1 = <span class="hljs-number">1</span>
agent<span class="hljs-preprocessor">.sinkgroups</span><span class="hljs-preprocessor">.g</span>_hdfs<span class="hljs-preprocessor">.processor</span><span class="hljs-preprocessor">.priority</span><span class="hljs-preprocessor">.s</span>_kafka_k2 = <span class="hljs-number">10</span>
agent<span class="hljs-preprocessor">.sinkgroups</span><span class="hljs-preprocessor">.g</span>_hdfs<span class="hljs-preprocessor">.processor</span><span class="hljs-preprocessor">.maxpenalty</span> = <span class="hljs-number">10000</span> </code></pre>

<p><strong><em>flume-hdfs-server1.properties</em></strong></p>

<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor">#set Agent name</span>
hdfs1<span class="hljs-preprocessor">.sources</span> = <span class="hljs-built_in">r1</span>
hdfs1<span class="hljs-preprocessor">.channels</span> = c1
hdfs1<span class="hljs-preprocessor">.sinks</span> = k1

<span class="hljs-preprocessor">#set channel</span>
hdfs1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span> = memory
hdfs1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">1000</span>
hdfs1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.transactionCapacity</span> = <span class="hljs-number">100</span>

<span class="hljs-preprocessor"># set sources</span>
hdfs1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.type</span> = avro
hdfs1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.bind</span> = <span class="hljs-number">192.168</span><span class="hljs-number">.183</span><span class="hljs-number">.102</span>
hdfs1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.port</span> = <span class="hljs-number">52020</span>
hdfs1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.channels</span> = c1

<span class="hljs-preprocessor">#set sink to hdfs</span>
hdfs1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.type</span>=hdfs
hdfs1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.path</span>=hdfs://<span class="hljs-number">192.168</span><span class="hljs-number">.183</span><span class="hljs-number">.101</span>:<span class="hljs-number">9000</span>/flume/logs/%<span class="hljs-built_in">Y</span>/%m/%d
hdfs1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.fileType</span>=DataStream
hdfs1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.writeFormat</span>=TEXT
hdfs1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.custom</span><span class="hljs-preprocessor">.encoding</span> = UTF-<span class="hljs-number">8</span>
hdfs1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.channel</span>=c1
hdfs1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.filePrefix</span>=%<span class="hljs-built_in">Y</span>-%m-%d
hdfs1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.fileSuffix</span>=<span class="hljs-preprocessor">.txt</span>
hdfs1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollInterval</span>=<span class="hljs-number">60</span>
hdfs1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollSize</span>=<span class="hljs-number">1024</span>
hdfs1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollCount</span>=<span class="hljs-number">0</span>
hdfs1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.idleTimeout</span>=<span class="hljs-number">60</span>
hdfs1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.useLocalTimeStamp</span> = true</code></pre>

<p><strong><em>flume-hdfs-server2.properties</em></strong></p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor">#set Agent name</span>
hdfs2<span class="hljs-preprocessor">.sources</span> = <span class="hljs-built_in">r1</span>
hdfs2<span class="hljs-preprocessor">.channels</span> = c1
hdfs2<span class="hljs-preprocessor">.sinks</span> = k1

<span class="hljs-preprocessor">#set channel</span>
hdfs2<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span> = memory
hdfs2<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">1000</span>
hdfs2<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.transactionCapacity</span> = <span class="hljs-number">100</span>

<span class="hljs-preprocessor"># set sources</span>
hdfs2<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.type</span> = avro
hdfs2<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.bind</span> = <span class="hljs-number">192.168</span><span class="hljs-number">.183</span><span class="hljs-number">.103</span>
hdfs2<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.port</span> = <span class="hljs-number">52020</span>
hdfs2<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.channels</span> = c1

<span class="hljs-preprocessor">#set sink to hdfs</span>
hdfs2<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.type</span>=hdfs
hdfs2<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.path</span>=hdfs://<span class="hljs-number">192.168</span><span class="hljs-number">.183</span><span class="hljs-number">.101</span>:<span class="hljs-number">9000</span>/flume/logs/%<span class="hljs-built_in">Y</span>/%m/%d
hdfs2<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.fileType</span>=DataStream
hdfs2<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.writeFormat</span>=TEXT
hdfs2<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.custom</span><span class="hljs-preprocessor">.encoding</span> = UTF-<span class="hljs-number">8</span>
hdfs2<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.channel</span>=c1
hdfs2<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.filePrefix</span>=%<span class="hljs-built_in">Y</span>-%m-%d
hdfs2<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.fileSuffix</span>=<span class="hljs-preprocessor">.txt</span>
hdfs2<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollInterval</span>=<span class="hljs-number">60</span>
hdfs2<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollSize</span>=<span class="hljs-number">1024</span>
hdfs2<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollCount</span>=<span class="hljs-number">0</span>
hdfs2<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.idleTimeout</span>=<span class="hljs-number">60</span>
hdfs2<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.useLocalTimeStamp</span> = true</code></pre>

<p><strong><em>flume-kafka-server1.properties</em></strong></p>

<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor">#set kafka1 name</span>
kafka1<span class="hljs-preprocessor">.sources</span> = <span class="hljs-built_in">r1</span>
kafka1<span class="hljs-preprocessor">.channels</span> = c1
kafka1<span class="hljs-preprocessor">.sinks</span> = k1

<span class="hljs-preprocessor">#set channel</span>
kafka1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span> = memory
kafka1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">10000</span>
kafka1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.transactionCapacity</span> = <span class="hljs-number">1000</span>

<span class="hljs-preprocessor"># set sources</span>
kafka1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.type</span> = avro
kafka1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.bind</span> = <span class="hljs-number">192.168</span><span class="hljs-number">.183</span><span class="hljs-number">.103</span>
kafka1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.port</span> = <span class="hljs-number">52021</span>
kafka1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.channels</span> = c1

<span class="hljs-preprocessor"># set sink to kafka</span>
kafka1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.type</span> = org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.KafkaSink</span>  
kafka1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.metadata</span><span class="hljs-preprocessor">.broker</span><span class="hljs-preprocessor">.list</span>= node3:<span class="hljs-number">9092</span>,node4:<span class="hljs-number">9092</span>,node5:<span class="hljs-number">9092</span>
kafka1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.bootstrap</span><span class="hljs-preprocessor">.servers</span> = node3:<span class="hljs-number">9092</span>,node4:<span class="hljs-number">9092</span>,node5:<span class="hljs-number">9092</span>
kafka1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.partition</span><span class="hljs-preprocessor">.key</span>=<span class="hljs-number">0</span>  
kafka1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.partitioner</span><span class="hljs-preprocessor">.class</span>=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.plugins</span><span class="hljs-preprocessor">.SinglePartition</span>  
kafka1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.serializer</span><span class="hljs-preprocessor">.class</span>=kafka<span class="hljs-preprocessor">.serializer</span><span class="hljs-preprocessor">.StringEncoder</span>  
kafka1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.request</span><span class="hljs-preprocessor">.required</span><span class="hljs-preprocessor">.acks</span>=<span class="hljs-number">0</span>  
kafka1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.max</span><span class="hljs-preprocessor">.message</span><span class="hljs-preprocessor">.size</span>=<span class="hljs-number">1000000</span>  
kafka1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.producer</span><span class="hljs-preprocessor">.type</span>=sync
kafka1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.custom</span><span class="hljs-preprocessor">.encoding</span>=UTF-<span class="hljs-number">8</span>  
<span class="hljs-preprocessor">#kafka1.sinks.k1.custom.topic.name=test</span>
kafka1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.topic</span>=test
kafka1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.channel</span> = c1
kafka1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.zkconnect</span> = node1:<span class="hljs-number">2181</span>,node2:<span class="hljs-number">2181</span>,node3:<span class="hljs-number">2181</span>,node4:<span class="hljs-number">2181</span>,node5:<span class="hljs-number">2181</span></code></pre>

<p><strong><em>flume-kafka-server2.properties</em></strong></p>

<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor">#set kafka2 name</span>
kafka2<span class="hljs-preprocessor">.sources</span> = <span class="hljs-built_in">r1</span>
kafka2<span class="hljs-preprocessor">.channels</span> = c1
kafka2<span class="hljs-preprocessor">.sinks</span> = k1

<span class="hljs-preprocessor">#set channel</span>
kafka2<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span> = memory
kafka2<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">10000</span>
kafka2<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.transactionCapacity</span> = <span class="hljs-number">1000</span>

<span class="hljs-preprocessor"># set sources</span>
kafka2<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.type</span> = avro
kafka2<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.bind</span> = <span class="hljs-number">192.168</span><span class="hljs-number">.183</span><span class="hljs-number">.104</span>
kafka2<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.port</span> = <span class="hljs-number">52021</span>
kafka2<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.channels</span> = c1

<span class="hljs-preprocessor"># set sink to kafka</span>
kafka2<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.type</span> = org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.KafkaSink</span>  
kafka2<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.metadata</span><span class="hljs-preprocessor">.broker</span><span class="hljs-preprocessor">.list</span>= node3:<span class="hljs-number">9092</span>,node4:<span class="hljs-number">9092</span>,node5:<span class="hljs-number">9092</span>  
kafka2<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.bootstrap</span><span class="hljs-preprocessor">.servers</span> = node3:<span class="hljs-number">9092</span>,node4:<span class="hljs-number">9092</span>,node5:<span class="hljs-number">9092</span>
kafka2<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.partition</span><span class="hljs-preprocessor">.key</span>=<span class="hljs-number">0</span>  
kafka2<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.partitioner</span><span class="hljs-preprocessor">.class</span>=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.plugins</span><span class="hljs-preprocessor">.SinglePartition</span>  
kafka2<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.serializer</span><span class="hljs-preprocessor">.class</span>=kafka<span class="hljs-preprocessor">.serializer</span><span class="hljs-preprocessor">.StringEncoder</span>  
kafka2<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.request</span><span class="hljs-preprocessor">.required</span><span class="hljs-preprocessor">.acks</span>=<span class="hljs-number">0</span>  
kafka2<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.max</span><span class="hljs-preprocessor">.message</span><span class="hljs-preprocessor">.size</span>=<span class="hljs-number">1000000</span>  
kafka2<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.producer</span><span class="hljs-preprocessor">.type</span>=sync 
kafka2<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.custom</span><span class="hljs-preprocessor">.encoding</span>=UTF-<span class="hljs-number">8</span>  
<span class="hljs-preprocessor">#kafka2.sinks.k1.custom.topic.name=test</span>
kafka2<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.topic</span>=test
kafka2<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.channel</span> = c1
kafka2<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.zkconnect</span> = node1:<span class="hljs-number">2181</span>,node2:<span class="hljs-number">2181</span>,node3:<span class="hljs-number">2181</span>,node4:<span class="hljs-number">2181</span>,node5:<span class="hljs-number">2181</span></code></pre>

<p><strong><em>flume-kafka-server3.properties</em></strong></p>

<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor">#set kafka3 name</span>
kafka3<span class="hljs-preprocessor">.sources</span> = <span class="hljs-built_in">r1</span>
kafka3<span class="hljs-preprocessor">.channels</span> = c1
kafka3<span class="hljs-preprocessor">.sinks</span> = k1

<span class="hljs-preprocessor">#set channel</span>
kafka3<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span> = memory
kafka3<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">10000</span>
kafka3<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.transactionCapacity</span> = <span class="hljs-number">1000</span>

<span class="hljs-preprocessor"># set sources</span>
kafka3<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.type</span> = avro
kafka3<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.bind</span> = <span class="hljs-number">192.168</span><span class="hljs-number">.183</span><span class="hljs-number">.105</span>
kafka3<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.port</span> = <span class="hljs-number">52021</span>
kafka3<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.channels</span> = c1

<span class="hljs-preprocessor"># set sink to kafka</span>
kafka3<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.type</span> = org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.KafkaSink</span> 
kafka3<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.metadata</span><span class="hljs-preprocessor">.broker</span><span class="hljs-preprocessor">.list</span>= node3:<span class="hljs-number">9092</span>,node4:<span class="hljs-number">9092</span>,node5:<span class="hljs-number">9092</span> 
kafka3<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.bootstrap</span><span class="hljs-preprocessor">.servers</span> = node3:<span class="hljs-number">9092</span>,node4:<span class="hljs-number">9092</span>,node5:<span class="hljs-number">9092</span> 
kafka3<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.partition</span><span class="hljs-preprocessor">.key</span>=<span class="hljs-number">0</span>  
kafka3<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.partitioner</span><span class="hljs-preprocessor">.class</span>=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.plugins</span><span class="hljs-preprocessor">.SinglePartition</span>  
kafka3<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.serializer</span><span class="hljs-preprocessor">.class</span>=kafka<span class="hljs-preprocessor">.serializer</span><span class="hljs-preprocessor">.StringEncoder</span>  
kafka3<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.request</span><span class="hljs-preprocessor">.required</span><span class="hljs-preprocessor">.acks</span>=<span class="hljs-number">0</span>  
kafka3<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.max</span><span class="hljs-preprocessor">.message</span><span class="hljs-preprocessor">.size</span>=<span class="hljs-number">1000000</span>  
kafka3<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.producer</span><span class="hljs-preprocessor">.type</span>=sync 
kafka3<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.custom</span><span class="hljs-preprocessor">.encoding</span>=UTF-<span class="hljs-number">8</span>  
<span class="hljs-preprocessor">#kafka3.sinks.k1.custom.topic.name=test</span>
kafka3<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.topic</span>=test
kafka3<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.channel</span> = c1
kafka3<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.zkconnect</span> = node1:<span class="hljs-number">2181</span>,node2:<span class="hljs-number">2181</span>,node3:<span class="hljs-number">2181</span>,node4:<span class="hljs-number">2181</span>,node5:<span class="hljs-number">2181</span></code></pre>

<p><strong>程序启动顺序：</strong> <br>
1、启动zookeeper <br>
2、启动hdfs <br>
3、启动kafka <br>
4、首先启动flume的server</p>



<pre class="prettyprint"><code class=" hljs lasso">启动命令：
<span class="hljs-number">1</span>、node2节点
flume<span class="hljs-attribute">-ng</span> agent <span class="hljs-subst">--</span>conf conf <span class="hljs-subst">--</span>conf<span class="hljs-attribute">-file</span> /root/myInstall/flume<span class="hljs-subst">-</span><span class="hljs-number">1.7</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span>/properties/flume<span class="hljs-attribute">-hdfs</span><span class="hljs-attribute">-server1</span><span class="hljs-built_in">.</span>properties <span class="hljs-subst">--</span>name hdfs1 <span class="hljs-attribute">-Dflume</span><span class="hljs-built_in">.</span>root<span class="hljs-built_in">.</span>logger<span class="hljs-subst">=</span>INFO,console <span class="hljs-subst">&gt;</span> /root/myInstall/flume<span class="hljs-subst">-</span><span class="hljs-number">1.7</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span>/logs/flume<span class="hljs-attribute">-hdfs</span><span class="hljs-attribute">-server1</span><span class="hljs-built_in">.</span><span class="hljs-keyword">log</span> <span class="hljs-number">2</span><span class="hljs-subst">&gt;&amp;</span><span class="hljs-number">1</span> <span class="hljs-subst">&amp;</span> 
<span class="hljs-number">2</span>、node3节点
flume<span class="hljs-attribute">-ng</span> agent <span class="hljs-subst">--</span>conf conf <span class="hljs-subst">--</span>conf<span class="hljs-attribute">-file</span> /root/myInstall/flume<span class="hljs-subst">-</span><span class="hljs-number">1.7</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span>/properties/flume<span class="hljs-attribute">-hdfs</span><span class="hljs-attribute">-server2</span><span class="hljs-built_in">.</span>properties <span class="hljs-subst">--</span>name hdfs2 <span class="hljs-attribute">-Dflume</span><span class="hljs-built_in">.</span>root<span class="hljs-built_in">.</span>logger<span class="hljs-subst">=</span>INFO,console <span class="hljs-subst">&gt;</span> /root/myInstall/flume<span class="hljs-subst">-</span><span class="hljs-number">1.7</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span>/logs/flume<span class="hljs-attribute">-hdfs</span><span class="hljs-attribute">-server2</span><span class="hljs-built_in">.</span><span class="hljs-keyword">log</span> <span class="hljs-number">2</span><span class="hljs-subst">&gt;&amp;</span><span class="hljs-number">1</span> <span class="hljs-subst">&amp;</span> 
flume<span class="hljs-attribute">-ng</span> agent <span class="hljs-subst">--</span>conf conf <span class="hljs-subst">--</span>conf<span class="hljs-attribute">-file</span> /root/myInstall/flume<span class="hljs-subst">-</span><span class="hljs-number">1.7</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span>/properties/flume<span class="hljs-attribute">-kafka</span><span class="hljs-attribute">-server1</span><span class="hljs-built_in">.</span>properties <span class="hljs-subst">--</span>name kafka1 <span class="hljs-attribute">-Dflume</span><span class="hljs-built_in">.</span>root<span class="hljs-built_in">.</span>logger<span class="hljs-subst">=</span>INFO,console <span class="hljs-subst">&gt;</span> /root/myInstall/flume<span class="hljs-subst">-</span><span class="hljs-number">1.7</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span>/logs/flume<span class="hljs-attribute">-kafka</span><span class="hljs-attribute">-server1</span><span class="hljs-built_in">.</span><span class="hljs-keyword">log</span> <span class="hljs-number">2</span><span class="hljs-subst">&gt;&amp;</span><span class="hljs-number">1</span> <span class="hljs-subst">&amp;</span> 
<span class="hljs-number">3</span>、node4节点
flume<span class="hljs-attribute">-ng</span> agent <span class="hljs-subst">--</span>conf conf <span class="hljs-subst">--</span>conf<span class="hljs-attribute">-file</span> /root/myInstall/flume<span class="hljs-subst">-</span><span class="hljs-number">1.7</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span>/properties/flume<span class="hljs-attribute">-kafka</span><span class="hljs-attribute">-server2</span><span class="hljs-built_in">.</span>properties <span class="hljs-subst">--</span>name kafka2 <span class="hljs-attribute">-Dflume</span><span class="hljs-built_in">.</span>root<span class="hljs-built_in">.</span>logger<span class="hljs-subst">=</span>INFO,console <span class="hljs-subst">&gt;</span> /root/myInstall/flume<span class="hljs-subst">-</span><span class="hljs-number">1.7</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span>/logs/flume<span class="hljs-attribute">-kafka</span><span class="hljs-attribute">-server2</span><span class="hljs-built_in">.</span><span class="hljs-keyword">log</span> <span class="hljs-number">2</span><span class="hljs-subst">&gt;&amp;</span><span class="hljs-number">1</span> <span class="hljs-subst">&amp;</span> 
<span class="hljs-number">4</span>、node5节点
flume<span class="hljs-attribute">-ng</span> agent <span class="hljs-subst">--</span>conf conf <span class="hljs-subst">--</span>conf<span class="hljs-attribute">-file</span> /root/myInstall/flume<span class="hljs-subst">-</span><span class="hljs-number">1.7</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span>/properties/flume<span class="hljs-attribute">-kafka</span><span class="hljs-attribute">-server3</span><span class="hljs-built_in">.</span>properties <span class="hljs-subst">--</span>name kafka3 <span class="hljs-attribute">-Dflume</span><span class="hljs-built_in">.</span>root<span class="hljs-built_in">.</span>logger<span class="hljs-subst">=</span>INFO,console <span class="hljs-subst">&gt;</span> /root/myInstall/flume<span class="hljs-subst">-</span><span class="hljs-number">1.7</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span>/logs/flume<span class="hljs-attribute">-kafka</span><span class="hljs-attribute">-server3</span><span class="hljs-built_in">.</span><span class="hljs-keyword">log</span> <span class="hljs-number">2</span><span class="hljs-subst">&gt;&amp;</span><span class="hljs-number">1</span> <span class="hljs-subst">&amp;</span> </code></pre>

<p>5、然后启动flume的client</p>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-number">1</span>、node1节点
flume<span class="hljs-attribute">-ng</span> agent <span class="hljs-subst">--</span>conf conf <span class="hljs-subst">--</span>conf<span class="hljs-attribute">-file</span> /root/myInstall/flume<span class="hljs-subst">-</span><span class="hljs-number">1.7</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span>/properties/flume<span class="hljs-attribute">-kafka</span><span class="hljs-attribute">-hdfs</span><span class="hljs-attribute">-client</span><span class="hljs-built_in">.</span>properties <span class="hljs-subst">--</span>name agent <span class="hljs-attribute">-Dflume</span><span class="hljs-built_in">.</span>root<span class="hljs-built_in">.</span>logger<span class="hljs-subst">=</span>INFO,console <span class="hljs-subst">&gt;</span> /root/myInstall/flume<span class="hljs-subst">-</span><span class="hljs-number">1.7</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span>/logs/flume<span class="hljs-attribute">-kafka</span><span class="hljs-attribute">-hdfs</span><span class="hljs-attribute">-client</span><span class="hljs-built_in">.</span><span class="hljs-keyword">log</span> <span class="hljs-number">2</span><span class="hljs-subst">&gt;&amp;</span><span class="hljs-number">1</span> <span class="hljs-subst">&amp;</span> 
<span class="hljs-number">2</span>、node2节点
flume<span class="hljs-attribute">-ng</span> agent <span class="hljs-subst">--</span>conf conf <span class="hljs-subst">--</span>conf<span class="hljs-attribute">-file</span> /root/myInstall/flume<span class="hljs-subst">-</span><span class="hljs-number">1.7</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span>/properties/flume<span class="hljs-attribute">-kafka</span><span class="hljs-attribute">-hdfs</span><span class="hljs-attribute">-client</span><span class="hljs-built_in">.</span>properties <span class="hljs-subst">--</span>name agent <span class="hljs-attribute">-Dflume</span><span class="hljs-built_in">.</span>root<span class="hljs-built_in">.</span>logger<span class="hljs-subst">=</span>INFO,console <span class="hljs-subst">&gt;</span> /root/myInstall/flume<span class="hljs-subst">-</span><span class="hljs-number">1.7</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span>/logs/flume<span class="hljs-attribute">-kafka</span><span class="hljs-attribute">-hdfs</span><span class="hljs-attribute">-client</span><span class="hljs-built_in">.</span><span class="hljs-keyword">log</span> <span class="hljs-number">2</span><span class="hljs-subst">&gt;&amp;</span><span class="hljs-number">1</span> <span class="hljs-subst">&amp;</span> </code></pre>

<p>好了，至此flume整合hdfs和kafka就搭建好了！</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>