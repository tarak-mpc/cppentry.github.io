---
layout:     post
title:      Kafka Topic Partition Replica Assignment实现原理及资源隔离方案
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2 style="font-size:20px;font-family:Verdana, Arial, Helvetica, sans-serif;">
<a id="cb_post_title_url" href="http://www.cnblogs.com/yurunmiao/p/5550906.html" rel="nofollow" style="color:rgb(34,51,85);text-decoration:none;">Kafka Topic Partition Replica Assignment实现原理及资源隔离方案</a></h2>
<div class="postbody" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<div id="cnblogs_post_body">
<p></p>
<div>本文共分为三个部分：</div>
<div> </div>
<ul style="margin-left:30px;"><li style="list-style-type:disc;">Kafka Topic创建方式</li><li style="list-style-type:disc;">Kafka Topic Partitions Assignment实现原理</li><li style="list-style-type:disc;">Kafka资源隔离方案</li></ul><div> </div>
<div>1. Kafka Topic创建方式</div>
<div> </div>
<div>Kafka Topic创建方式有以下两种表现形式：</div>
<div> </div>
<div>（1）创建Topic时直接指定Topic Partition Replica与Kafka Broker之间的存储映射关系</div>
<div> </div>
<div>/usr/lib/kafka_2.10-0.8.2.1/bin/kafka-topics.sh --zookeeper ZooKeeperHost:ZooKeeperPort --create --topic TopicName --replica-assignment id0:id1:id2,id3:id4:id5,id6:id7:id8</div>
<div> </div>
<div>其中，“id0:id1:id2,id3:id4:id5,id6:id7:id8”表示Topic TopicName一共有3个Partition（以“,”分隔），每个Partition均有3个Replica（以“:”分隔），Topic Partition Replica与Kafka Broker之间的对应关系如下：</div>
<div> </div>
<div>Partition0 Replica：Broker id0、Broker id1、Broker id2；</div>
<div>Partition1 Replica：Broker id3、Broker id4、Broker id5；</div>
<div>Partition2 Replica：Broker id6、Broker id7、Broker id8；</div>
<div> </div>
<div>（2）创建Topic时由Kafka自动分配Topic Partition Replica与Kafka Broker之间的存储映射关系</div>
<div> </div>
<div>/usr/lib/kafka_2.10-0.8.2.1/bin/kafka-topics.sh --zookeeper ZooKeeperHost:ZooKeeperPort --create --topic TopicName</div>
<div> </div>
<div>第（1）种方式完全依靠人为手工指定，这里仅仅探讨使用第（2）种方式创建Topic时，“自动分配”是如何实现的。</div>
<div> </div>
<div>2. Kafka Topic Partition Replica Assignment实现原理</div>
<div> </div>
<div>Replica Assignment的目标有两个：</div>
<div> </div>
<div>（1）使Partition Replica能够均匀地分配至各个Kafka Broker（负载均衡）；</div>
<div>（2）如果Partition的第一个Replica分配至某一个Kafka Broker，那么这个Partition的其它Replica则需要分配至其它的Kafka Brokers，即Partition Replica分配至不同的Broker；</div>
<div> </div>
<div>注意，这里有一个约束条件：Topic Partition Replicas Size &lt;= Kafka Brokers Size。</div>
<div> </div>
<div>“自动分配”的核心工作过程如下：</div>
<div> </div>
<div>随机选取一个StartingBroker（Broker id0、Broker id1、Broker id2、...），随机选取IncreasingShift初始值（[0，nBrokers - 1]）</div>
<div> </div>
<div>（1）从StartingBroker开始，使用轮询的方式依次将各个Partition的Replicas分配至各个Broker；</div>
<div> </div>
<div>对于每一个Partition，Replicas的分配过程如下：</div>
<div> </div>
<div>（2）Partition的第一个Replica分配至StartingBroker；</div>
<div> </div>
<div>（3）根据IncreasingShift计算第n（n&gt;=2）个Replica的Shift（即与第1个Replica的间隔量），依据Shift将其分配至相应的Broker；</div>
<div> </div>
<div>（4）StartingBroker移至下一个Broker；</div>
<div> </div>
<div>（5）如果Brokers已经被轮询完一次，则IncreasingShift递增一；否则，继续（2）。</div>
<div> </div>
<div>假设有5个Brokers（broker-0、broker-1、broker-2、broker-3、broker-4），Topic有10个Partition（p0、p1、p2、p3、p4、p5、p6、p7、p8、p9），每一个Partition有3个Replica，依据上述工作过程，分配结果如下：</div>
<div> </div>
<div>broker-0  broker-1  broker-2  broker-3  broker-4</div>
<div>p0           p1            p2           p3            p4       (1st replica)</div>
<div>p5           p6            p7           p8            p9       (1st replica)</div>
<div>p4           p0            p1           p2            p3       (2nd replica)</div>
<div>p8           p9            p5           p6            p7       (2nd replica)</div>
<div>p3           p4            p0           p1            p2       (3nd replica)</div>
<div>p7           p8            p9           p5            p6       (3nd replica)</div>
<div> </div>
<div>详细步骤如下：</div>
<div> </div>
<div>选取broker-0作为StartingBroker，IncreasingShift初始值为1，</div>
<div> </div>
<div>对于p0，replica1分配至broker-0，IncreasingShift为1，所以replica2分配至broker-1，replica3分配至broker-2；</div>
<div>对于p1，replica1分配至broker-1，IncreasingShift为1，所以replica2分配至broker-2，replica3分配至broker-3；</div>
<div>对于p2，replica1分配至broker-2，IncreasingShift为1，所以replica2分配至broker-3，replica3分配至broker-4；</div>
<div>对于p3，replica1分配至broker-3，IncreasingShift为1，所以replica2分配至broker-4，replica3分配至broker-1；</div>
<div>对于p4，replica1分配至broker-4，IncreasingShift为1，所以replica2分配至broker-0，replica3分配至broker-1；</div>
<div> </div>
<div>注：IncreasingShift用于计算Shift，Shift表示Partition的第n（n&gt;=2）个Replica与第1个Replica之间的间隔量。如果IncreasingShift值为m，那么Partition的第2个Replica与第1个Replica的间隔量为m + 1，第3个Replica与第1个Replica的间隔量为m + 2，...，依次类推。Shift的取值范围：[1，brokerSize - 1]。</div>
<div> </div>
<div>此时，broker-0、broker-1、broker-2、broker-3、broker-4分别作为StartingBroker被轮询分配一次，继续轮询；但IncreasingShift递增为2。</div>
<div> </div>
<div>对于p5，replica1分配至broker-0，IncreasingShift为2，所以replica2分配至broker-2，replica3分配至broker-3；</div>
<div>对于p6，replica1分配至broker-1，IncreasingShift为2，所以replica2分配至broker-3，replica3分配至broker-4；</div>
<div>对于p7，replica1分配至broker-2，IncreasingShift为2，所以replica2分配至broker-4，replica3分配至broker-0；</div>
<div>对于p8，replica1分配至broker-3，IncreasingShift为2，所以replica2分配至broker-0，replica3分配至broker-1；</div>
<div>对于p9，replica1分配至broker-4，IncreasingShift为2，所以replica2分配至broker-1，replica3分配至broker-2；</div>
<div> </div>
<div>此时，broker-0、broker-1、broker-2、broker-3、broker-4分别作为StartingBroker再次被轮询一次，如果还有其它Partition，则继续轮询，IncreasingShift递增为3，依次类推。</div>
<div> </div>
<div>这里有几点需要注意：</div>
<div> </div>
<div>（1）为什么要随机选取StartingBroker，而不是每次都选取broker-0作为StartingBroker？</div>
<div> </div>
<div>以broker-0、broker-1、broker-2、broker-3、broker-4为例，因为分配过程是以轮询方式进行的，如果每次都选取broker-0作为StartingBroker，那么Brokers列表中的前面部分将有可能被分配相对比较多的Partition Replicas，从而导致这部分Brokers负载较高，随机选取可以保证相对比较好的均匀效果。</div>
<div> </div>
<div>（2）为什么Brokers列表每次轮询一次，IncreasingShift值都需要递增1？</div>
<div> </div>
<div>Kafka Topic Partition数目较多的情况下，Partition的第1个Replica与第n（n&gt;=2）个Replica之间的间隔量随着IncreasingShift的变化面变化，能够更好的均匀分配Replica。</div>
<div> </div>
<div>scala.kafka.admin.AdminUtils.assignReplicasToBrokers()实现上述Topic Partition Replica与Broker之间的分配过程，源码如下：</div>
<div> </div>
<div><img src="http://images2015.cnblogs.com/blog/121862/201606/121862-20160601200520946-1532324689.png" alt="" style="border:0px;"></div>
<div> </div>
<div>brokerList：Kafka Brokers列表；</div>
<div>nPartitions：Topic待分配的Partition数目；</div>
<div>replicationFactor：Topic Partition Replica数目；</div>
<div>fixedStartIndex：如果显示指定，默认值为0；它的值与两个变量值相关：startIndex和nextReplicaShift，详情见后；</div>
<div>startPartitionId：从Topic的哪一个Partition开始分配，通常情况下是0，Topic增加Partition时该值不为0。</div>
<div> </div>
<div>val ret = new mutable.HashMap[Int, List[Int]]()</div>
<div> </div>
<div>分配结果保存至一个Map变量ret，key为Partition Id，value为分配的Brokers列表。</div>
<div> </div>
<div> val startIndex = if (fixedStartIndex &gt;= 0) fixedStartIndex else rand.nextInt(brokerList.size)</div>
<div> var currentPartitionId = if (startPartitionId &gt;= 0) startPartitionId else 0</div>
<div> </div>
<div> var nextReplicaShift = if (fixedStartIndex &gt;= 0) fixedStartIndex else rand.nextInt(brokerList.size)</div>
<div> </div>
<div>startIndex表示StartingBroker，currentPartitionId表示当前为哪个Partition分配Brokers，nextReplicaShift表示当前的IncreasingShit值。</div>
<div> </div>
<div><img src="http://images2015.cnblogs.com/blog/121862/201606/121862-20160601200532414-69141842.png" alt="" style="border:0px;"></div>
<div> </div>
<div>接下来就是一个循环，用于为每一个Partition的Replicas分配Brokers，其中Partition的第1个Replica由“(currentPartitionId + startIndex) % brokerList.size”决定，其余的Replica由“replicaIndex（）”决定。</div>
<div> </div>
<div><img src="http://images2015.cnblogs.com/blog/121862/201606/121862-20160601200542133-2104672461.png" alt="" style="border:0px;"></div>
<div> </div>
<div>shift表示着第n（n &gt;= 2）个Replica与第一个Replica之间的间隔量，“1 + (secondReplicaShift + replicaIndex) % (nBrokers - 1)”的计算方式非常巧妙，它保证了shift的取值范围：[1，nBrokers]（大家可以自己体会一下）。</div>
<div> </div>
<div>3. Kafka资源隔离方案</div>
<div> </div>
<div>实时数据处理场景中，如果数据量比较大，为了保证写入/消费的吞吐量，我们创建Topic时通常会指定比较大的Partition数目，从而使得数据尽可能地被分散至更多的Partition，Partition被尽可能均匀的分配至Kafka集群中的各个Broker，从负载均衡的角度看，一切都很美好。从业务的角度看，会有资源竞争的问题，毕竟Kafka Broker机器的带宽资源是有限的，在带宽比较紧张的情形下，任何一个业务方的数据量波动（这里仅指数据量增加），所有的业务方都会受到影响；从运维的角度看，会有可用性的问题，任何一台Kafka
 Broker机器都负载着所有Topic的数据传输、存储，如果出现宕机的情况，将会波及到所有的Topic。针对这种情况，我们提出了划分资源池的资源隔离方案：</div>
<div> </div>
<div><img src="http://images2015.cnblogs.com/blog/121862/201606/121862-20160601200552164-1457273786.png" alt="" style="border:0px;"></div>
<div> </div>
<div>Kafka集群有9台Brokers组成：broker-1、broker-2、...、broker-9，创建9个Topic：t1、t2、...、t9，每个Topic有9个Partition（假设Replica为1），如上图所示，我们将9台Brokers切分成3个资源池：Pool1（broker-1、broker-2、broker-3）、Pool2（broker-4、broker-5、broker-6）、Pool3（broker-7、broker-8、broker-9），Topic的分配情况如下：</div>
<div> </div>
<div>Pool1：t1、t2、t3</div>
<div>Pool2：t4、t5、t6</div>
<div>Pool3：t7、t8、t9</div>
<div> </div>
<div>可以看出，这三个资源池的物理资源是完全独立的，三个资源池实际上相当于三个小集群。</div>
<div> </div>
<div>这种资源池的划分方式不但可以做到物理资源的隔离，还可以一定程度上解决异构机型（MEM、DISK）带来的问题，可以把机型相似的机器组成一个资源池。实际实施时需要综合考虑业务情况、机器情况，合理划分资源池，并根据具体的Topic情况将其分配至合适的资源池内。</div>
<div> </div>
<div>Kafka Topic的创建也变为两步：</div>
<div> </div>
<div>（1）使用kafka-topics.sh创建Topic；</div>
<div>（2）使用kafka-reassign-partitions.sh移动Topic Partition Replicas至指定的资源池（具体的Brokers列表）。</div>
</div>
</div>
            </div>
                </div>