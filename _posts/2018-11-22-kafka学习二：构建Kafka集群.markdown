---
layout:     post
title:      kafka学习二：构建Kafka集群
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>现在我们构建Kafka消息订阅发布系统。Kafka支持多种集群方式，例如：</p>
<ul><li>单节点单broker集群</li><li>单节点多broker集群</li><li>多节点多broker集群</li></ul><p>一个Kafka集群主要包含以下五个组件：</p>
<ul><li>topic：topic是producer发布的消息的类别。在Kafka中，topic是分区的，每个partition内是顺序不可变的消息序列。Kafka集群维护每个topic的partition日志。partition内每个消息分配了一个被称为offset的唯一序列ID。</li><li>broker：一个Kafka集群包含一个或多个服务器，每个服务器有一个或多个运行的被称为broker的服务进程。topic是在broker进程的上下文中创建的。</li><li>zookeeper：Kafka使用ZooKeeper来协调broker和cunsumers。ZooKeeper允许分布式进程通过一个共享的层级数据寄存器命名空间相互协调（这些数据寄存器被称为znode），就像文件系统一样。它跟标准文件系统的区别是，每个znode可以拥有关联的数据，且数据量有限制。ZooKeeper是被设计用来存储协调数据的，如状态信息、配置信息、位置信息等。关于ZooKeeper详见<a href="http://wiki.apache.org/hadoop/ZooKeeper/ProjectDescription" rel="nofollow">Hadoop
 Wiki</a>。</li><li>producers：producer选择topic内的合适的partition并向其发布数据。为了负载均衡，可以基于循环的方式或者自定义方法来关联信息与topic partition。</li><li>consumer：consumer是订阅topics并处理发布的消息的应用或进程。</li></ul><h3 id="单节点单broker集群">单节点单broker集群</h3>
<p>在上篇中，我们在单台机器上部署了Kafka，现在将其设置为单节点单broker集群。架构如图所示：</p>
<p><img src="http://images2015.cnblogs.com/blog/834562/201601/834562-20160101124446635-1889577.png" alt=""></p>
<h4 id="启动zookeeper服务">启动ZooKeeper服务：</h4>
<p>Kafka提供了一个默认的简单的ZooKeeper配置文件，可启动本地的单ZooKeeper实例。</p>
<div class="sourceCode">
<pre class="sourceCode bash"><code class="sourceCode bash hljs"><span class="kw">bin/zookeeper-server-start.sh</span> config/zookeeper.properties <span class="kw">&amp;</span></code></pre>
</div>
<p>zookeeper.properties配置文件中定义了以下的关键属性：</p>
<p></p><pre><code class="language-html"># Data directory where the zookeeper snapshot is stored.
dataDir=/tmp/zookeeper
# The port listening for client request
clientPort=2181
# disable the per-ip limit on the number of connections since this is a non-production config
maxClientCnxns=0</code></pre><br><p>在默认的配置中，ZooKeeper监听2181端口。关于构建多ZooKeeper服务，详见<a href="http://zookeeper.apache.org/" rel="nofollow" class="uri">http://zookeeper.apache.org/</a>.</p>
<h4 id="启动kafka-broker">启动Kafka broker</h4>
<pre><code class="language-html">bin/kafka-server-start.sh config/server.properties &amp;</code></pre><br>
server.properties配置文件中定义了以下的关键属性：
<p></p><pre><code class="language-html"># The id of the broker. This must  be set to a unique integer for each broker.
Broker.id=0
# The port the socket server listens on
port=9092
# The directory under which to store log files
log.dir=/tmp/kafka8-logs
# The default number of log partitions per topic.  
num.partitions=2
# Zookeeper connection string  
zookeeper.connect=localhost:2181</code></pre><br><h4 id="创建一个kafka-topic">创建一个Kafka topic</h4>
<p></p>
<p>创建一个名为<code>kafkatopic</code>的topic，单partitoin且只有一个副本：</p>
<pre><code class="language-html">bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic kafkatopic</code></pre><br>
查看topic列表：
<p></p><pre><code class="language-html">bin/kafka-topics.sh --list --zookeeper localhost:2181</code></pre><br><h4 id="启动producer发送信息">启动producer发送信息</h4>
<p></p>
<p>Kafka提供了一个命令行producer，可接收命令行输入并将其作为消息发布到Kafka集群。默认每一行是一个消息。</p>
<p></p><pre><code class="language-html">bin/kafka-console-producer.sh --broker-list localhost:9092 --topic kafkatopic</code></pre><br><p>其中，broker-list和topic这两个参数是必须的，broker-list指定要连接的broker，格式为<code>node_address:port</code>。topic是必须的，因为需要发送消息给订阅了该topic的consumer group。</p>
<p>现在可以在命令行里输入一些信息，每一行会被作为一个消息。</p>
<p>默认的属性配置在/config/producer.properties文件中：</p>
<pre><code class="language-html"># list of brokers used for bootstrapping knowledge about the rest of the format: host1:port1,host2:port2...
metadata.broker.list=localhost:9092
# specify the compression codec for all data generated: none , gzip, snappy.
compression.codec=none</code></pre><br><pre><code class="language-html">启动consumer接收消息</code></pre><br><pre><code class="language-html">bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic kafkatopic --from-beginning</code></pre><br><p>consumer加载完成后，会输出刚才在producer里输入消息。</p>
<p>默认的属性配置在/config/consumer.properties文件中：</p>
<p></p><pre><code class="language-html"># consumer group id (A string that uniquely identifies a set of consumers within the same consumer group)
group.id=test-consumer-group</code></pre><br><p></p>
<p>在不同的终端里分别启动zookeeper，broker，producer，consumer后，在producer终端里输入消息，消息就会在consumer终端中显示了。</p>
<h3 id="单节点多broker集群">单节点多broker集群</h3>
<p>接下来构建一个单节点多broker集群：</p>
<p><img src="http://images2015.cnblogs.com/blog/834562/201601/834562-20160101141224745-1089426879.png" alt=""></p>
<h4 id="启动zookeeper">启动ZooKeeper</h4>
<p>同上。</p>
<pre><code class="language-html">bin/zookeeper-server-start.sh config/zookeeper.properties &amp;</code></pre><br><h4 id="启动broker">启动broker</h4>
<p>在单节点上配置多个bbroker时，需要为每个broker指定单独的属性配置文件，其中<code>broker.id</code>、<code>port</code>、<code>log.dir</code>这三个属性必须时不同的。</p>
<p>在broker1的配置文件server-1.properties中，定义参数为：</p>
<p></p><pre><code class="language-html">broker.id=1
port=9093
log.dir=/tmp/kafka-logs-1</code></pre><br>
在broker2的配置文件server-2.properties中，定义参数为：
<p></p><pre><code class="language-html">broker.id=2
port=9094
log.dir=/tmp/kafka-logs-2</code></pre><br><p>我们为每个broker指定了不同的端口，是因为我们在同一台机器上搭建多个broker，实际生产环境中，broker可能分布在多台机器上。</p>
<p>启动每个broker：</p>
<p></p><pre><code class="language-html">bin/kafka-server-start.sh config/server-1.properties &amp;
bin/kafka-server-start.sh config/server-2.properties &amp;</code></pre><br><h4 id="创建topic">创建topic</h4>
<p></p>
<p>创建一个名为<code>replicated-kafkatopic</code>的topic：</p>
<p></p><pre><code class="language-html">bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 3 --partitions 1 --topic replicated-kafkatopic</code></pre><br><h4 id="启动producer">启动producer</h4>
<p></p>
<p>如果一个producer连接多个broker，需要传递参数<code>broker-list</code>：</p>
<p></p><pre><code class="language-html">bin/kafka-console-producer.sh --broker-list localhost:9093, localhost:9094 --topic replicated-kafkatopic</code></pre><br><p>多个producer时，为每个指定<code>broker-list</code>。</p>
<h4 id="启动consumer">启动consumer</h4>
<p></p><pre><code class="language-html">bin/kafka-console-consumer.sh --zookeeper localhost:2181 --from-beginning --topic replicated-kafkatopic</code></pre><br><h3 id="多节点多broker集群">多节点多broker集群</h3>
<p></p>
<p>在多节点多broker集群中，每个节点都需要安装Kafka，且所有的broker都连接到同一个ZooKeeper。</p>
<p><img src="http://images2015.cnblogs.com/blog/834562/201601/834562-20160101153021354-458120785.png" alt=""></p>
<br><p><br></p>
<p><br></p>
            </div>
                </div>