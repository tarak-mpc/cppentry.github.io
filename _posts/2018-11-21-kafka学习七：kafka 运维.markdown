---
layout:     post
title:      kafka学习七：kafka 运维
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h3 id="kafka管理工具">Kafka管理工具</h3>
<h4 id="kafka集群管理工具">Kafka集群管理工具</h4>
<p>Kafka集群管理内容包括服务器启停、leader均衡、复制、集群镜像、集群扩展等。</p>
<h4 id="添加服务器">添加服务器</h4>
<p>向Kafka集群中添加服务器时，需要分配一个唯一的broker ID给新服务器。这时添加新服务器不会自动分配数据分区。重分配工具kafka-reassign-partitions.sh用于在broker之间移动partition。Kafka将新服务器当成待迁移的partition的follower，这样新服务器可以完全复制该partition内的所有数据。一旦复制完成，新服务器成为in-sync replica之一，之前的副本中的某一个将会删除该partition数据。</p>
<p>kafka-reassign-partitions.sh有三种模式：</p>
<ul><li>--generate：生成候选的重分配方案</li><li>--execute：执行重分配计划</li><li>--verify：验证最后一次--execute的结果</li></ul><p>partition重分配工具可用于将topic从当前broker集中移到新添加的broker中。使用时指定需要移动的topic清单和目标broker的ID清单。该工具会平均地将toipc分配到新的broker中，同时将topic的副本也带过去。</p>
<p><br></p>
<p></p><pre><code class="language-html">#topics-for-new-server.json
{"partitions":
    [{"topic": "kafkatopic",
     {"topic": "kafkatopic1"}],
 "version":1
}</code></pre><br>
生成topic重分配计划文件：
<p></p><pre><code class="language-html">bin/kafka-reassign-partitions.sh --zookeeper localhost:2181 --topics-to-move-json-file topics-for-new-server.json --broker-list "4,5" -–generate new-topic-reassignment.json</code></pre><br>
执行topic重分配：
<p></p><pre><code class="language-html">bin/kafka-reassign-partitions.sh --zookeeper localhost:2181 --reassignment-json-file new-topic-reassignment.json --execute</code></pre>也可以可选地移动分区：
<p></p><pre><code class="language-html">#partitions-reassignment.json
{"partitions":
    [{"topic": "kafkatopic",
      "partition": 1,
      "replicas": [1,2,4] }],   
      }],
  "version":1
}</code></pre><br><pre><code class="language-html">bin/kafka-reassign-partitions.sh --zookeeper localhost:2181 --reassignment-json-file partitions-reassignment.json --execute</code></pre><br>
重分配完成后，可以验证一下操作：
<p></p><pre><code class="language-html">bin/kafka-reassign-partitions.sh --zookeeper localhost:2181 --reassignment-json-file new-topic-reassignment.json --verify

# output like this：
Status of partition reassignment:
Reassignment of partition [kafkatopic,0] completed successfully
Reassignment of partition [kafkatopic,1] is in progress
Reassignment of partition [kafkatopic,2] completed successfully
Reassignment of partition [kafkatopic1,0] completed successfully
Reassignment of partition [kafkatopic1,1] completed successfully
Reassignment of partition [kafkatopic1,2] is in progress</code></pre><br><p>从集群中移除服务器时，需要先将该服务器（broker）上所有分区的副本移除，均匀地分配到剩下的broker中。</p>
<p>增加复制因子：</p>
<p></p><pre><code class="language-html">#increase-replication-factor.json
{"partitions":[{"topic":"kafkatopic","partition":0,"replicas":[2,3]}],
 "version":1
}</code></pre><br><pre><code class="language-html">bin/kafka-reassign-partitions.sh --zookeeper localhost:2181 --reassignment-json-file increase-replication-factor.json --execute</code></pre><br><h4 id="管理topic">管理topic</h4>
<p></p>
<p>创建topic，指定复制因子和分区数量：</p>
<pre><code class="language-html">bin/kafka-topics.sh --create --zookeeper localhost:2181/chroot --replication-factor 3 --partitions 10 --topic kafkatopic</code></pre><br><p>复制因子指定每个消息保存副本的数量，复制因子为3表示有两台服务器失效时仍不会丢失消息。分区数量指定topic分片的数量。一个分区只能在一个单独的服务器上。分区数量为10表示所有的数据会被除了副本服务器外的不超过10台服务器处理。</p>
<p>kafka-topics.sh还可以修改topic。目前不支持减少分区数量。例如，将分区数量增加到20：</p>
<pre><code class="language-html">bin/kafka-topics.sh --alter --zookeeper localhost:2181/chroot --partitions 20 --topic kafkatopic</code></pre><br>
删除topic：
<p></p><pre><code class="language-html">bin/kafka-topics.sh --delete --zookeeper localhost:2181/chroot --topic kafkatopic</code></pre><br>
添加topic配置：
<p></p><pre><code class="language-html">bin/kafka-topics.sh --alter --zookeeper localhost:2181/chroot --topic kafkatopic --config &lt;key&gt;=&lt;value&gt;</code></pre><br>
删除topic配置：
<p></p><pre><code class="language-html">bin/kafka-topics.sh --alter --zookeeper localhost:2181/chroot --topic kafkatopic --deleteconfig &lt;key&gt;=&lt;value&gt;</code></pre><br>
列出所有topic信息。可选参数有under-replicated-partitions和unavailable-partitions：
<p></p><pre><code class="language-html">bin/kafka-topics.sh --list --zookeeper localhost:2181</code></pre><br><h3 id="kafka集群镜像">Kafka集群镜像</h3>
<p></p>
<p>Kafka提供了将现有集群镜像到一个新集群的工具。</p>
<p><img src="http://images2015.cnblogs.com/blog/834562/201601/834562-20160126211228988-4932735.png" alt=""></p>
<p>如图中所示，镜像工具的任务是从源集群消费消息，使用内嵌的producer重新发布到目标集群。</p>
<p>镜像源集群时，需要构建好目标集群，并启动MirrorMaker：</p>
<pre><code class="language-html">bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config sourceClusterConsumer.config --num.streams 2 --producer.config targetClusterProducer.config --whitelist=".*"</code></pre><br><p>启动MirrorMaker的最少参数有：一个或多个consumer配置文件、一个producer配置文件、基于标准Java正则表达式的白名单或黑名单。例如，镜像名为A和B的topic使用<code>--whitelist 'A|B'</code>，镜像所有topic使用<code>--whitelist '*'</code>。同时还需要MirrorMaker的consumer连接到源集群的ZooKeeper，producer连接到目标集群的ZooKeeper或broker.list参数。</p>
<p>出于高吞吐量的考虑，内嵌的异步producer使用阻塞模式，这确保了消息不会丢失producer在队列满了的情况下会等待所有的消息写入目标集群。如果producer的队列始终是满的则说明MirrorMaker是集群镜像的瓶颈。<code>--num.producers</code>选项可用于指定MirrorMaker中producer pool中consumer的数量来增加吞吐量，<code>--num.streams</code>指定consumer线程的数量。</p>
<p>通常，MirrorMaker的consumer配置中的socket.buffersize和目标集群broker配置中的socket.send.buffer配置为很大的值，而且MirrorMaker的consumer配置中的fetch.size大于socket.buffersize。如果producer配置中指定了broker.list且使用了硬件负载均衡，可以配置一个producer失效时的重试次数。</p>
<p>Kafka还提供了检查consumer位置的工具。该工具可查询一个consumer group中所有consumer的位置。参数<code>--zkconnect</code>指向目标集群的ZooKeeper，参数<code>--topic</code>是可选的，不指定时将输出指定consumer group下所有topic信息。</p>
<pre><code class="language-html">bin/kafka-run-class.sh kafka.tools.ConsumerOffsetChecker --group MirrorGroup --zkconnect localhost:2181 --topic kafkatopic

#输出结果
Group        Topic       Pid  Offset  logSize    Lag    Owner
MirrorGroup  kafkatopic  0    5       5          0      none
MirrorGroup  kafkatopic  1    3       4          1      none
MirrorGroup  kafkatopic  2    6       9          3      none</code></pre><br><h3 id="与其他工具集成">与其他工具集成</h3>
<p><a href="https://github.com/linkedin/camus" rel="nofollow">Camus：provides a pipeline from Kafka to HDFS</a><br><a href="https://github.com/nathanmarz/kafka-deploy" rel="nofollow">Automated deployment and configuration of Kafka and ZooKeeper on Amazon</a><br><a href="https://github.com/leandrosilva/klogd2" rel="nofollow">A logging utility</a><br><a href="https://github.com/mozilla-metrics/bagheera" rel="nofollow">A REST service for Mozilla Metrics</a><br><a href="https://github.com/BreizhBeans/camel-kafka/wiki" rel="nofollow">Apache Camel-Kafka integration</a></p>
<p><a href="https://cwiki.apache.org/confluence/display/KAFKA/Ecosystem" rel="nofollow">Kafka ecosystem tools</a></p>
<br><br>            </div>
                </div>