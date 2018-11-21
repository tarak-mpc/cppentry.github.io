---
layout:     post
title:      Kafka实战－Kafka Cluster
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1>1.概述</h1>
<p>　　在《<a id="homepage1_HomePageDays_ctl00_DayList_TitleUrl_0" class="postTitle2" href="http://www.cnblogs.com/smartloli/p/4530117.html" rel="nofollow">Kafka实战－入门</a>》一篇中，为大家介绍了Kafka的相关背景、原理架构以及一些关键知识点，本篇博客为大家来赘述一下Kafka Cluster的相关内容，下面是今天为大家分享的目录：</p>
<ul><li>基础软件的准备</li><li>Kafka Cluster的部署</li><li>Send Messages</li><li>HA特性</li></ul><p>　　下面开始今天的内容分享。</p>
<h1>2.基础软件的准备</h1>
<h2>2.1 ZK</h2>
<p>　　由于Kafka Cluster需要依赖ZooKeeper（后面简称ZK）集群来协同管理，所以这里我们需要事先搭建好ZK集群，关于ZK的集群搭建，大家可以参考我写的《<a id="cb_post_title_url" class="postTitle2" href="http://www.cnblogs.com/smartloli/p/4298430.html" rel="nofollow">配置高可用的Hadoop平台</a>》，这篇文章中我介绍了如何去搭建ZK，这里就不多赘述，本篇博客为大家介绍如何去搭建Kafka
 Cluster。</p>
<h2>2.2 Kafka</h2>
<p>　　由于Kafka已经贡献到Apache基金会了，我们可以到Apache的官方网站上去下载Kafka的基础安装包，下载地址如下所示：</p>
<p>　　Kafka安装包 ［<a href="http://apache.mirrors.ionfish.org/kafka/0.8.2.1/kafka_2.9.1-0.8.2.1.tgz" rel="nofollow">下载地址</a>］</p>
<p>　　Kafka源代码 ［<a href="http://mirror.nexcess.net/apache/kafka/0.8.2.1/kafka-0.8.2.1-src.tgz" rel="nofollow">下载地址</a>］</p>
<p>　　这里，我们直接使用官方编译好的安装包进行Kafka Cluster的安装部署。下面我们来开始Kafka Cluster的搭建部署。</p>
<h2>3.Kafka Cluster的部署</h2>
<p>　　首先，我们将下载好的Kafka基础安装包解压，命令如下所示：</p>
<ul><li>解压Kafka</li></ul><div class="cnblogs_code">
<pre>[hadoop@dn1 ~]$ <span style="color:#0000ff;">tar</span> -zxvf kafka_2.<span style="color:#800080;">9.1</span>-<span style="color:#800080;">0.8</span>.<span style="color:#800080;">2.1</span>.tgz</pre>
</div>
<ul><li>进入到Kafka解压目录</li></ul><div class="cnblogs_code">
<pre>[hadoop@dn1 ~]$ cd kafka_2.<span style="color:#800080;">9.1</span>-<span style="color:#800080;">0.8</span>.<span style="color:#800080;">2.1</span></pre>
</div>
<ul><li>配置环境变量</li></ul><div class="cnblogs_code">
<pre>[hadoop@dn1 ~]$ <span style="color:#0000ff;">vi</span> /etc/profile</pre>
</div>
<div class="cnblogs_code">
<pre>export KAFKA_HOME=/home/hadoop/kafka_2.<span style="color:#800080;">11</span>-<span style="color:#800080;">0.8</span>.<span style="color:#800080;">2.1</span><span style="color:#000000;">
export PATH</span>=$PATH:$KAFKA_HOME/bin</pre>
</div>
<ul><li>配置Kafka的zookeeper.properties </li></ul><div class="cnblogs_code">
<pre><span style="color:#000000;"># the directory where the snapshot is stored.
dataDir</span>=/home/hadoop/data/<span style="color:#000000;">zk
# the port at </span><span style="color:#0000ff;">which</span><span style="color:#000000;"> the clients will connect
clientPort</span>=<span style="color:#800080;">2181</span><span style="color:#000000;">
# disable the per</span>-ip limit on the number of connections since this is a non-<span style="color:#000000;">production config
maxClientCnxns</span>=<span style="color:#800080;">0</span></pre>
</div>
<ul><li class="p1">配置server.properties</li></ul><div class="cnblogs_code">
<pre># The <span style="color:#0000ff;">id</span> of the broker. This must be set to a unique integer <span style="color:#0000ff;">for</span><span style="color:#000000;"> each broker.
broker.</span><span style="color:#0000ff;">id</span>=<span style="color:#800080;">0</span></pre>
</div>
<p>　　<span style="color:#ff0000;">注：这里配置broker的时候，每台机器上的broker保证唯一，从0开始。如：在另外2台机器上分别配置broker.id=1，broker.id=2</span></p>
<ul><li class="p1">配置producer.properties</li></ul><div class="cnblogs_code">
<pre># list of brokers used <span style="color:#0000ff;">for</span><span style="color:#000000;"> bootstrapping knowledge about the rest of the cluster
# format: host1:port1,host2:port2 ...
metadata.broker.list</span>=dn1:<span style="color:#800080;">9092</span>,dn2:<span style="color:#800080;">9092</span>,dn3:<span style="color:#800080;">9092</span></pre>
</div>
<ul><li class="p1">配置consumer.properties</li></ul><div class="cnblogs_code">
<pre># Zookeeper connection <span style="color:#0000ff;">string</span><span style="color:#000000;">
# comma separated host:port pairs, each corresponding to a zk
# server. e.g. </span><span style="color:#800000;">"</span><span style="color:#800000;">127.0.0.1:3000,127.0.0.1:3001,127.0.0.1:3002</span><span style="color:#800000;">"</span><span style="color:#000000;">
zookeeper.connect</span>=dn1:<span style="color:#800080;">2181</span>,dn2:<span style="color:#800080;">2181</span>,dn3:<span style="color:#800080;">2181</span></pre>
</div>
<p>　　至此，Kafka Cluster部署完成。</p>
<h1>4.Send Messages</h1>
<h2>4.1启动</h2>
<p>　　首先，在启动Kafka集群服务之前，确保我们的ZK集群已启动，下面我们启动Kafka集群服务。启动命令如下所示：</p>
<div class="cnblogs_code">
<pre>[hadoop@dn1 kafka_2.<span style="color:#800080;">11</span>-<span style="color:#800080;">0.8</span>.<span style="color:#800080;">2.1</span>]$ kafka-server-start.<span style="color:#0000ff;">sh</span> config/server.properties &amp; </pre>
</div>
<p>　　<span style="color:#ff0000;">注：其他2个节点参照上述方式启动。</span></p>
<p><span style="color:#000000;">　　另外，启动其他节点的时候，在最先开始启动的节点会显示其它节点加入的信息记录，如下图所示：</span></p>
<p><img src="http://images0.cnblogs.com/blog2015/666745/201505/291132462195874.png" alt=""></p>
<h2>4.2验证启动进程</h2>
<div class="cnblogs_code">
<pre>[hadoop@dn1 kafka_2.<span style="color:#800080;">11</span>-<span style="color:#800080;">0.8</span>.<span style="color:#800080;">2.1</span><span style="color:#000000;">]$ jps
</span><span style="color:#800080;">2049</span><span style="color:#000000;"> QuorumPeerMain
</span><span style="color:#800080;">2184</span><span style="color:#000000;"> Kafka
</span><span style="color:#800080;">2233</span> Jps</pre>
</div>
<h2>4.3创建Topic</h2>
<p>　　在服务启动后，我们开始创建一个Topic，命令如下所示：</p>
<div class="cnblogs_code">
<pre>[hadoop@dn1 ]$ kafka-topics.<span style="color:#0000ff;">sh</span> --zookeeper dn1:<span style="color:#800080;">2181</span>,dn2:<span style="color:#800080;">2181</span>,dn3:<span style="color:#800080;">2181</span> --topic test1 --replication-factor <span style="color:#800080;">3</span> --partitions <span style="color:#800080;">1</span> --create</pre>
</div>
<p>　　然后，我们查看该Topic的相关信息，命令如下所示：</p>
<div class="cnblogs_code">
<pre>[hadoop@dn1 ]$ kafka-topics.<span style="color:#0000ff;">sh</span> --zookeeper dn1:<span style="color:#800080;">2181</span>,dn2:<span style="color:#800080;">2181</span>,dn3:<span style="color:#800080;">2181</span> --topic test1 --describe</pre>
</div>
<p>　　预览信息如下图所示：</p>
<p><img src="http://images0.cnblogs.com/blog2015/666745/201505/291138316732346.png" alt=""></p>
<p>　　下面解释一下这些输出。第一行是对所有分区的一个描述，然后每个分区都会对应一行，因为我们只有一个分区所以下面就只加了一行。</p>
<ul><li>Leader：负责处理消息的读和写，Leader是从所有节点中随机选择的。</li><li>Replicas：列出了所有的副本节点，不管节点是否在服务中。</li><li>Isr：是正在服务中的节点</li></ul><h2>4.4生产消息</h2>
<p>　　下面我们使用kafka的Producer生产一些消息，然后让Kafka的Consumer去消费，命令如下所示：</p>
<div class="cnblogs_code">
<pre>[hadoop@dn1 ]$ kafka-console-producer.<span style="color:#0000ff;">sh</span> --broker-list dn1:<span style="color:#800080;">9092</span>,dn2:<span style="color:#800080;">9092</span>,dn3:<span style="color:#800080;">9092</span> --topic test1</pre>
</div>
<p><img src="http://images0.cnblogs.com/blog2015/666745/201505/291145150943560.png" alt=""></p>
<h2>4.4消费消息</h2>
<p>　　接着，我们在另外一个节点启动消费进程，来消费这些消息，命令如下所示：</p>
<div class="cnblogs_code">
<pre>[hadoop@dn2 ]$ kafka-console-consumer.<span style="color:#0000ff;">sh</span> --zookeeper dn1:<span style="color:#800080;">2181</span>,dn2:<span style="color:#800080;">2181</span>,dn3:<span style="color:#800080;">2181</span> --from-beginning --topic test1</pre>
</div>
<p>　　消费记录如下图所示：</p>
<p><img src="http://images0.cnblogs.com/blog2015/666745/201505/291149134858122.png" alt=""></p>
<h1>5.HA特性</h1>
<p>　　这里，从上面的截图信息可以看出，在DN1节点上Kafka服务上Lead，我们先将DN1节点的Kafka服务kill掉，命令如下：</p>
<div class="cnblogs_code">
<pre><span style="color:#000000;">[hadoop@dn1 config]$ jps
</span><span style="color:#800080;">2049</span><span style="color:#000000;"> QuorumPeerMain
</span><span style="color:#800080;">2375</span><span style="color:#000000;"> Jps
</span><span style="color:#800080;">2184</span><span style="color:#000000;"> Kafka
[hadoop@dn1 config]$ </span><span style="color:#0000ff;">kill</span> -<span style="color:#800080;">9</span> <span style="color:#800080;">2184</span></pre>
</div>
<p>　　然后，其他节点立马选举出了新的Leader，如下图所示：</p>
<p><img src="http://images0.cnblogs.com/blog2015/666745/201505/291153298444242.png" alt=""></p>
<p>　　下面，我们来测试消息的生产和消费，如下图所示：</p>
<ul><li>生产消息</li></ul><p><img src="http://images0.cnblogs.com/blog2015/666745/201505/291155520944234.png" alt=""></p>
<ul><li>消费消息</li></ul><p><img src="http://images0.cnblogs.com/blog2015/666745/201505/291156060489629.png" alt=""></p>
<p>　　通过测试，可以发现，Kafka的HA特性还是不错的，拥有不错的容错机制。</p>
<h1>6.总结</h1>
<p>　　这里，在部署Kafka Cluster的时候，有些地方需要我们注意，比如：在我们启动Kafka集群的时候，确保ZK集群启动，另外，在配置Kafka配置文件信息时，确保ZK的集群信息配置到相应的配置文件中，总体来说，配置还算较为简单，需要在部署的时候，仔细配置各个文件即可。</p>
            </div>
                </div>