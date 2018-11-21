---
layout:     post
title:      kafka安装及删除Topic
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，请尊重劳动成果，觉得不错就在文章下方顶一下呗，转载请标明原地址。					https://blog.csdn.net/m0_37739193/article/details/76688408				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<strong><span style="color:#3366ff;">安装Kafka：</span></strong><br>
我们使用3台机器搭建Kafka集群：<br>
192.168.4.142   h40<br>
192.168.4.143   h41<br><p>192.168.4.144   h42</p>
<p><br></p>
<p>kafka_2.10-0.8.2.0下载地址：<a href="http://mirror.bit.edu.cn/apache/kafka/0.8.2.0/kafka_2.10-0.8.2.0.tgz" rel="nofollow">http://mirror.bit.edu.cn/apache/kafka/0.8.2.0/kafka_2.10-0.8.2.0.tgz</a><br></p>
<p>我安装的这个版本有点老了，你也可以下载较新的0.10.1.0版本：<a href="http://mirror.bit.edu.cn/apache/kafka/0.10.1.0/kafka_2.10-0.10.1.0.tgz" rel="nofollow">http://mirror.bit.edu.cn/apache/kafka/0.10.1.0/kafka_2.10-0.10.1.0.tgz</a><br></p>
<p>（0.10.1.0版本安装方法和旧版本0.8一样，就是在启动时会比旧版本0.8在界面多输出配置信息，在默认的/tmp/kafka-logs目录下也会多出cleaner-offset-checkpoint和meta.properties这两个文件，在zookeeper客户端中的/kafka/brokers下多出个seqid目录，在/kafka/config下多出个clients目录）<br></p>
<p><br></p>
<p><span style="color:#993399;">我一般爱装在hadoop用户下面：</span><br>
创建hadoop用户和组（所有虚拟机）<br>
groupadd hadoop<br>
useradd -g hadoop hadoop<br>
passwd hadoop<br></p>
<p><br></p>
<p>[hadoop@h40 ~]$ tar -zxvf kafka_2.10-0.8.2.0.tgz<br>
[hadoop@h40 ~]$ cd kafka_2.10-0.8.2.0<br>
修改配置文件kafka_2.10-0.8.2.0/config/server.properties，修改如下内容为：<br>
zookeeper.connect=h40:2181,h41:2181,h42:2181/kafka<br><span style="color:#663300;">这里需要说明的是，默认Kafka会使用ZooKeeper默认的/路径，这样有关Kafka的ZooKeeper配置就会散落在根路径下面，如果你有其他的应用也在使用ZooKeeper集群，查看ZooKeeper中数据可能会不直观，所以强烈建议指定一个chroot路径，直接在zookeeper.connect配置项中指定。</span><br></p>
<p><br></p>
<p>而且，需要手动在ZooKeeper中创建路径/kafka（后来发现不手动创建的话它也会自动生成），使用如下命令连接到任意一台ZooKeeper服务器：<br>
[hadoop@h40 ~]$ cd zookeeper-3.4.5/<br>
[hadoop@h40 zookeeper-3.4.5]$ bin/zkCli.sh<br>
在ZooKeeper执行如下命令创建chroot路径：<br>
[zk: localhost:2181(CONNECTED) 0] create /kafka ''<br>
（我不太明白''有什么作用，可参考http://5ydycm.blog.51cto.com/115934/1591768和http://blog.csdn.net/rickesy/article/details/43735217了解zookeeper客户端命令的相关知识）<br>
这样，每次连接Kafka集群的时候（使用--zookeeper选项），也必须使用带chroot路径的连接字符串，后面会看到。<br></p>
<p><br></p>
<p><span style="color:#993399;">将kafka拷贝到其他两台机器：</span><br>
[hadoop@h40 ~]$ scp -r kafka_2.10-0.8.2.0/ h41:/home/hadoop/<br>
[hadoop@h40 ~]$ scp -r kafka_2.10-0.8.2.0/ h42:/home/hadoop/<br></p>
<p><br></p>
<p><span style="color:#993399;">修改server.properties中的broker.id，三台机器都修改</span><br>
因为Kafka集群需要保证各个Broker的id在整个集群中必须唯一，需要调整这个配置项的值（如果在单机上，可以通过建立多个Broker进程来模拟分布式的Kafka集群，但也需要Broker的id唯一，还需要修改一些配置目录的信息）。<br>
[hadoop@h40 ~]$ vi kafka_2.10-0.8.2.0/config/server.properties<br>
broker.id=0<br>
[hadoop@h41 ~]$ vi kafka_2.10-0.8.2.0/config/server.properties<br>
broker.id=1<br>
[hadoop@h42 ~]$ vi kafka_2.10-0.8.2.0/config/server.properties<br>
broker.id=2<br></p>
<p><br></p>
<p><span style="color:#993399;">首先启动zookeeper集群，然后三台机器都执行以下命令：</span><br>
[hadoop@h40 kafka_2.10-0.8.2.0]$ bin/kafka-server-start.sh config/server.properties<br></p>
<p><br></p>
<p><span style="color:#993399;">创建消息主题topic</span><br>
可以通过查看日志，或者检查进程状态，保证Kafka集群启动成功。<br>
我们创建一个名称为test的Topic，5个分区，并且复制因子为3，执行如下命令：<br>
[hadoop@h40 kafka_2.10-0.8.2.0]$ bin/kafka-topics.sh --create --zookeeper h40:2181,h41:2181,h42:2181/kafka --replication-factor 3 --partitions 5 --topic test<br>
Created topic "test".<br>
zookeeper只指定一个ip也可以的，比如：<br>
[hadoop@h40 kafka_2.10-0.8.2.0]$ bin/kafka-topics.sh --create --zookeeper h40:2181/kafka --replication-factor 2 --partitions 2 --topic hui<br>
Created topic "hui".<br></p>
<p><br></p>
<p><span style="color:#993399;">查看创建的Topic，执行如下命令：</span></p>
<pre><code class="language-plain">[hadoop@h40 kafka_2.10-0.8.2.0]$ bin/kafka-topics.sh --describe --zookeeper h40:2181,h41:2181,h42:2181/kafka --topic test
Topic:test     PartitionCount:5     ReplicationFactor:3     Configs:
     Topic: test     Partition: 0     Leader: 0     Replicas: 0,2,1     Isr: 0,2,1
     Topic: test     Partition: 1     Leader: 0     Replicas: 1,0,2     Isr: 0,2,1
     Topic: test     Partition: 2     Leader: 2     Replicas: 2,1,0     Isr: 2,0,1
     Topic: test     Partition: 3     Leader: 0     Replicas: 0,1,2     Isr: 0,2,1
     Topic: test     Partition: 4     Leader: 2     Replicas: 1,2,0     Isr: 2,0,1</code></pre>上面Leader、Replicas、Isr的含义如下：<br>
Partition： 分区<br>
Leader   ： 负责读写指定分区的节点<br>
Replicas ： 复制该分区log的节点列表<br><p>Isr      ： "in-sync" replicas，当前活跃的副本列表（是一个子集），并且可能成为Leader</p>
<p><br></p>
<p><span style="color:#993399;">自带压力测试：</span>（我这个是在单节点安装的kafka上测试的，等有时间再在集群上测试吧）</p>
<p>下面使用perf命令来测试topic的性能，50W条消息，每条1000字节，batch大小1000，topic为test，4个线程<br>
bin/kafka-topics.sh --create --zookeeper h153:2181 --replication-factor 1 --partitions 2 --topic test<br></p>
<p></p><pre><code class="language-plain">生产者测试：
[hadoop@h153 kafka_2.10-0.8.2.0]$ bin/kafka-producer-perf-test.sh --messages 500000 --message-size 1000  --batch-size 1000 --topics test --threads 4 --broker-list h153:9092
start.time, end.time, compression, message.size, batch.size, total.data.sent.in.MB, MB.sec, total.data.sent.in.nMsg, nMsg.sec
2017-09-27 01:33:54:610, 2017-09-27 01:34:10:381, 0, 1000, 1000, 476.84, 30.2351, 500000, 31703.7601
消费者：
[hadoop@h153 kafka_2.10-0.8.2.0]$ bin/kafka-consumer-perf-test.sh --zookeeper h153 --messages 500000 --topic test --threads 4
start.time, end.time, fetch.size, data.consumed.in.MB, MB.sec, data.consumed.in.nMsg, nMsg.sec
2017-09-27 01:36:41:066, 2017-09-27 01:36:46:068, 1048576, 0.0000, 0.0000, 0, 0.0000</code></pre>
<p><br></p>
<p><strong><span style="color:#ff0000;">注意：</span></strong>在执行命令参数--zookeeper h40:2181,h41:2181,h42:2181/kafka中的/kafka一定要和server.properties中的zookeeper.connect=h40:2181,h41:2181,h42:2181/kafka为参考，一开始我server.properties中配置的为zookeeper.connect=h40:2181,h41:2181,h42:2181
   执行命令的时候却加了/kafka的话会报这个错：</p>
<pre><code class="language-plain">Error while executing topic command org.apache.zookeeper.KeeperException$NoNodeException: KeeperErrorCode = NoNode for /brokers/ids
org.I0Itec.zkclient.exception.ZkNoNodeException: org.apache.zookeeper.KeeperException$NoNodeException: KeeperErrorCode = NoNode for /brokers/ids
        at org.I0Itec.zkclient.exception.ZkException.create(ZkException.java:47)
        at org.I0Itec.zkclient.ZkClient.retryUntilConnected(ZkClient.java:685)
        at org.I0Itec.zkclient.ZkClient.getChildren(ZkClient.java:413)
        at org.I0Itec.zkclient.ZkClient.getChildren(ZkClient.java:409)
        at kafka.utils.ZkUtils$.getChildren(ZkUtils.scala:468)
        at kafka.utils.ZkUtils$.getSortedBrokerList(ZkUtils.scala:78)
        at kafka.admin.AdminUtils$.createTopic(AdminUtils.scala:170)
        at kafka.admin.TopicCommand$.createTopic(TopicCommand.scala:93)
        at kafka.admin.TopicCommand$.main(TopicCommand.scala:55)
        at kafka.admin.TopicCommand.main(TopicCommand.scala)
Caused by: org.apache.zookeeper.KeeperException$NoNodeException: KeeperErrorCode = NoNode for /brokers/ids
        at org.apache.zookeeper.KeeperException.create(KeeperException.java:111)
        at org.apache.zookeeper.KeeperException.create(KeeperException.java:51)
        at org.apache.zookeeper.ZooKeeper.getChildren(ZooKeeper.java:1472)
        at org.apache.zookeeper.ZooKeeper.getChildren(ZooKeeper.java:1500)
        at org.I0Itec.zkclient.ZkConnection.getChildren(ZkConnection.java:99)
        at org.I0Itec.zkclient.ZkClient$2.call(ZkClient.java:416)
        at org.I0Itec.zkclient.ZkClient$2.call(ZkClient.java:413)
        at org.I0Itec.zkclient.ZkClient.retryUntilConnected(ZkClient.java:675)
        ... 8 more</code></pre><span style="color:#3366ff;">启动Producer，并向我们上面创建的名称为my-replicated-topic5的Topic中生产消息，执行如下脚本：</span><br>
[hadoop@h40 kafka_2.10-0.8.2.0]$ bin/kafka-console-producer.sh --broker-list h40:9092,h41:9092,h42:9092 --topic test<br><br><span style="color:#3366ff;">新开窗口，创建消费者consumer ：</span><br>
[hadoop@h40 kafka_2.10-0.8.2.0]$ bin/kafka-console-consumer.sh --zookeeper h40:2181,h41:2181,h42:2181/kafka --topic  test  --from-beginning<br>
（--from-beginning参数为将该topic中的消息从头全部读出，不加的话只会读出新增加的消息）
<p><br></p>
<p><br></p>
<p><strong><span style="color:#ff0000;">删除topic：</span></strong><br>
在kafka集群的所有三台机器中修改配置文件server.properties<br>
添加如下配置：<span style="color:#ff0000;">delete.topic.enable=true</span><br></p>
<p><br></p>
<p>当执行创建命令bin/kafka-topics.sh --create --zookeeper h40:2181/kafka --replication-factor 2 --partitions 2 --topic hui后，可以查看每个节点的/tmp/kafka-logs（server.properties文件log.dirs配置，默认为"/tmp/kafka-logs"，不同broker下存储的topic不一定相同，所有broker都要看一下）目录的变化：<br>
[root@h40 ~]# ll /tmp/kafka-logs/<br>
total 16<br>
drwxrwxr-x 2 hadoop hadoop 4096 Jun 20 16:35 hui-0<br>
drwxrwxr-x 2 hadoop hadoop 4096 Jun 20 16:35 hui-1<br>
-rw-rw-r-- 1 hadoop hadoop   20 Jun 20 16:35 recovery-point-offset-checkpoint<br>
-rw-rw-r-- 1 hadoop hadoop   20 Jun 20 16:35 replication-offset-checkpoint<br>
[root@h41 ~]# ll /tmp/kafka-logs/<br>
total 12<br>
drwxrwxr-x 2 hadoop hadoop 4096 Jun 20 16:35 hui-1<br>
-rw-rw-r-- 1 hadoop hadoop   12 Jun 20 16:36 recovery-point-offset-checkpoint<br>
-rw-rw-r-- 1 hadoop hadoop   12 Jun 20 16:36 replication-offset-checkpoint<br>
[root@h42 ~]# ll /tmp/kafka-logs/<br>
total 12<br>
drwxrwxr-x 2 hadoop hadoop 4096 Jun 20 16:36 hui-0<br>
-rw-rw-r-- 1 hadoop hadoop   12 Jun 20 16:36 recovery-point-offset-checkpoint<br>
-rw-rw-r-- 1 hadoop hadoop   12 Jun 20 16:37 replication-offset-checkpoint<br></p>
<p><br></p>
<p><span style="color:#993399;">进入zookeeper客户端查看对应topic：</span><br>
[hadoop@h40 ~]$ cd zookeeper-3.4.5/<br>
[hadoop@h40 zookeeper-3.4.5]$ bin/zkCli.sh<br>
找到topic所在的目录：<br>
[zk: localhost:2181(CONNECTED) 17] ls /kafka/brokers/topics<br>
[hui]<br>
[zk: localhost:2181(CONNECTED) 51] ls /kafka/config/topics<br>
[hui]<br></p>
<p><br></p>
<p>查看Toptics：<br>
[hadoop@h40 kafka_2.10-0.8.2.0]$ bin/kafka-topics.sh  --list --zookeeper h40:2181/kafka<br>
hui<br></p>
<p><br></p>
<p><span style="color:#993399;">重启kafka集群后进行删除操作：</span><br>
[hadoop@h40 kafka_2.10-0.8.2.0]$ ./bin/kafka-topics.sh --delete --zookeeper h40:2181/kafka --topic hui<br>
再执行查看Toptics命令会发现该Toptic已被删除<br></p>
<p><br></p>
<p>如果kafaka启动时加载的配置文件中server.properties没有配置delete.topic.enable=true，那么此时的删除并不是真正的删除，而是把topic标记为：marked for deletion<br>
[hadoop@h40 kafka_2.10-0.8.2.0]$ ./bin/kafka-topics.sh --delete --zookeeper h40:2181/kafka --topic hui  <br>
Topic hui is marked for deletion.<br>
Note: This will have no impact if delete.topic.enable is not set to true.<br>
[hadoop@h40 kafka_2.10-0.8.2.0]$ bin/kafka-topics.sh  --list --zookeeper h40:2181/kafka                                                   <br>
hui - marked for deletion<br>
被标记为marked for deletion的topic你可以在zookeeper客户端中通过命令获得：ls /admin/delete_topics/【topic name】，<br>
如果你删除了此处的topic，那么marked for deletion标记消失<br></p>
<p><br></p>
<p><strong><span style="color:#ff0000;">注意：</span></strong><span style="color:#990000;">网上总是说有第二种删除Topic的方法，那就是在配置文件中server.properties没有配置delete.topic.enable=true，然后再删除kafka存储目录（/tmp/kafka-logs）下对应的topic，并且在zookeeper客户端中的/kafka/brokers/topics目录下删掉对应topic：rmr
 /kafka/brokers/topics/【topic name】</span><br></p>
<p><br></p>
<p>可是我按上述做法后是可以正常删除相应的Topic，但是在删除后再创建一个一模一样的Topic的话（再次执行bin/kafka-topics.sh --create --zookeeper h40:2181/kafka --replication-factor 2 --partitions 2 --topic hui命令，如果再次创建的Topic名称不一样的话也倒不会出现这个问题），在/tmp/kafka-logs目录下不会再次产生相应的Topic目录，虽然该Topic可以正常生产和消费，但是在启动kafka进程窗口的控制台会报下面这个错：</p>
<pre><code class="language-plain">[2017-06-20 19:03:25,799] ERROR Uncaught exception in scheduled task 'kafka-log-retention' (kafka.utils.KafkaScheduler)
java.io.FileNotFoundException: /tmp/kafka-logs/hui-0/00000000000000000000.index (No such file or directory)</code></pre><br>
0.10.1.0版本则会一开始在控制台一直循环报这个错：<pre><code class="language-plain">[2017-06-20 16:13:20,327] ERROR [KafkaApi-0] Error when handling request Name: FetchRequest; Version: 3; CorrelationId: 179; ClientId: ReplicaFetcherThread-0-0; ReplicaId: 2; MaxWait: 500 ms; MinBytes: 1 bytes; MaxBytes:10485760 bytes; RequestInfo: ([hui,1],PartitionFetchInfo(0,1048576)) (kafka.server.KafkaApis)
kafka.common.NotAssignedReplicaException: Leader 0 failed to record follower 2's position 0 since the replica is not recognized to be one of the assigned replicas 0,1 for partition [hui,1].</code></pre>再将kafka进程关闭的时候也会报这个错：<pre><code class="language-plain">[2017-06-20 16:16:54,583] WARN /tmp/kafka-logs/hui-1/00000000000000000000.timeindex (No such file or directory) (kafka.utils.CoreUtils$)
java.io.FileNotFoundException: /tmp/kafka-logs/hui-1/00000000000000000000.timeindex (No such file or directory)</code></pre><br><span style="color:#990000;">解决：将kafka集群重启后又就不会报这个错并且一切正常，并且在/tmp/kafka-logs目录下也会生成相应的Topic</span>
<p><br></p>
<p><strong><span style="color:#663333;">第二种方法虽然可行，但是<span style="color:rgb(102,51,51);"><strong>比较繁琐，所以</strong></span>说我并不建议使用这种方法。既然修改配置文件简单可行，那你为什么又非要舍近求远呢？？？！</span></strong><br><br></p>
            </div>
                </div>