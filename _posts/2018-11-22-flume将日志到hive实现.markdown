---
layout:     post
title:      flume将日志到hive实现
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qqpy789/article/details/48470563				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>科普：flume是apache下的一个日志收集系统，主要由source+channel+ sink组成：source可以看做是源，也就是日志的来源，本例子是用exec source；channel可以看做是中转的路，可以是文件也可以是内存；sink是输出，一般有hive sink，hbase sink,hdfs sink,avro sink。当然一个机器可以有多个source+channel+ sink。</p>
<p>资源： 172.16.6.152 node1 安装有flume+datanode</p>
<p><span></span>     172.16.6.151 master  安装有flume +hive+ namenode</p>
<p>思路:1.node1机器中使用exec source 执行tail -F /*/*.log获取日志的source，然后使用memory channel，然后使用avro传输至master</p>
<p><span></span>2.master中的flume 的souce是avro，接收node1机器所发送过来的数据，经过memory channel,最后使用hdfs sink写入hdfs中</p>
<p><span></span>3.由于本人水平有限，本来想直接在master中使用hive sink，无奈一直报hive class找不着的错误。所以就使用了另外一个损招，hive导入数据，是可以直接复制文件进入到hive表所对应的location中，所以就有了解决办法。</p>
<p>附上详细的设置：</p>
<p>1.flume安装，解压，然后修改配置文件conf/flume-env.sh 设置环境变量</p>
<p>2.node1节点，在conf目录下生成example4.conf文件，example4.conf文件内容如下</p>
<p><br></p>
<p># Define a memory channel called ch1 on agent1<br>
agent1.channels.ch1.type = memory<br>
agent1.channels.ch1.capacity = 100000<br>
agent1.channels.ch1.transactionCapacity = 100000<br>
agent1.channels.ch1.keep-alive = 30<br>
 <br>
# Define an Avro source called avro-source1 on agent1 and tell it<br>
# to bind to 0.0.0.0:41414. Connect it to channel ch1.<br>
#agent1.sources.avro-source1.channels = ch1<br>
#agent1.sources.avro-source1.type = avro<br>
#agent1.sources.avro-source1.bind = 0.0.0.0<br>
#agent1.sources.avro-source1.port = 41414<br>
#agent1.sources.avro-source1.threads = 5<br>
 <br>
#define source monitor a file<br>
agent1.sources.avro-source1.type = exec<br>
agent1.sources.avro-source1.shell = /bin/bash -c<br>
agent1.sources.avro-source1.command =  tail -F /opt/cdh5.3.0/hadoop/logs/hadoop-hadoop-namenode-node1.log<br>
agent1.sources.avro-source1.channels = ch1<br>
agent1.sources.avro-source1.threads = 5<br>
 <br>
# Define a logger sink that simply logs all events it receives<br>
# and connect it to the other end of the same channel.<br>
agent1.sinks.log-sink1.channel = ch1<br>
agent1.sinks.log-sink1.type = avro<br>
agent1.sinks.log-sink1.hostname=master<br>
agent1.sinks.log-sink1.port=41415<br><br>
 <br>
# Finally, now that we've defined all of our components, tell<br>
# agent1 which ones we want to activate.<br>
agent1.channels = ch1<br>
agent1.sources = avro-source1<br>
agent1.sinks = log-sink1<br></p>
<p><br></p>
<p>3.master节点flume设置，在conf中生成example6.conf,内容如下</p>
<p># Define a memory channel called ch1 on agent1<br>
agent1.channels.ch1.type = memory<br>
agent1.channels.ch1.capacity = 100000<br>
agent1.channels.ch1.transactionCapacity = 100000<br>
agent1.channels.ch1.keep-alive = 30<br>
 <br>
# Define an Avro source called avro-source1 on agent1 and tell it<br>
# to bind to 0.0.0.0:41414. Connect it to channel ch1.<br>
#agent1.sources.avro-source1.channels = ch1<br>
#agent1.sources.avro-source1.type = avro<br>
#agent1.sources.avro-source1.bind = 0.0.0.0<br>
#agent1.sources.avro-source1.port = 41414<br>
#agent1.sources.avro-source1.threads = 5<br>
 <br>
#define source monitor a file<br>
agent1.sources.avro-source1.type = avro<br>
agent1.sources.avro-source1.bind = master<br>
agent1.sources.avro-source1.port =  41415<br>
agent1.sources.avro-source1.channels = ch1<br>
agent1.sources.avro-source1.threads = 5<br>
 <br>
# Define a logger sink that simply logs all events it receives<br>
# and connect it to the other end of the same channel.<br>
agent1.sinks.log-sink1.channel = ch1<br>
agent1.sinks.log-sink1.type = hdfs<br>
agent1.sinks.log-sink1.hdfs.path = hdfs://master:8020/zhonghui/flume/logs/type=hadoop/host=172.16.6.152/<br>
agent1.sinks.log-sink1.hdfs.useLocalTimeStamp=true<br>
agent1.sinks.log-sink1.hdfs.writeFormat = Text<br>
agent1.sinks.log-sink1.hdfs.fileType = DataStream<br>
agent1.sinks.log-sink1.hdfs.rollInterval = 0<br>
agent1.sinks.log-sink1.hdfs.rollSize = 1000000<br>
agent1.sinks.log-sink1.hdfs.rollCount = 0<br>
agent1.sinks.log-sink1.hdfs.batchSize = 1000<br>
agent1.sinks.log-sink1.hdfs.txnEventMax = 1000<br>
agent1.sinks.log-sink1.hdfs.callTimeout = 60000<br>
agent1.sinks.log-sink1.hdfs.appendTimeout = 60000<br>
agent1.sinks.log-sink1.hdfs.filePrefix = log<br>
agent1.sinks.log-sink1.hdfs.batchSize=2<br><br><br>
 <br>
# Finally, now that we've defined all of our components, tell<br>
# agent1 which ones we want to activate.<br>
agent1.channels = ch1<br>
agent1.sources = avro-source1<br>
agent1.sinks = log-sink1<br></p>
<p>4.启动master的flume，和node1的flum </p>
<p><span></span>master:bin/flume-ng agent --conf conf --conf-file conf/example6.conf --name agent1 -Dflume.root.logger=INFO,consolee</p>
<p><span></span>node1:bin/flume-ng agent --conf conf --conf-file conf/example4.conf --name agent1 -Dflume.root.logger=INFO,consolee</p>
<p>6.大概过个几分钟（最多10分钟，当然你可以改配置文件，我暂时没有找到配置项），就会产生文件，如果会产生文件，那就成功了一半，反之，自己小心核对配置文件。然后停止node1和master上的服务，删除刚生成的文件夹（包括文件）</p>
<p>7.hive环境搭建我就不复述了，自己百度吧。</p>
<p>create table logs(<br>
create string,<br>
content string)<br>
PARTITIONED BY (type string,host string)<br>
row format delimited fields terminated by ','  location '/zhonghui/flume/logs/';<br></p>
<p>alter table logs add partition (type='hadoop',host='172.16.6.152');<br></p>
<p>这样就创建了一个hive表，以及一个分区</p>
<p>8.启动node1和master上的flume。然后坐等数据吧</p>
<p>hive&gt; select * from logs;<br>
OK<br>
2015-09-15 17:49:15<span> </span>800 INFO org.apache.hadoop.hdfs.server.blockmanagement.CacheReplicationMonitor: Rescanning after 30000 milliseconds<span>
</span>hadoop<span> </span>172.16.6.152<br>
2015-09-15 17:49:15<span> </span>801 INFO org.apache.hadoop.hdfs.server.blockmanagement.CacheReplicationMonitor: Scanned 0 directive(s) and 0 block(s) in 1 millisecond(s).<span>
</span>hadoop<span> </span>172.16.6.152<br>
2015-09-15 17:49:37<span> </span>825 INFO org.apache.hadoop.hdfs.server.namenode.FSEditLog: Number of transactions: 2 Total time for transactions(ms): 1 Number of transactions batched in Syncs: 0 Number of syncs: 1 SyncTimes(ms): 10
 50 <span></span>hadoop<span> </span>
172.16.6.152<br>
2015-09-15 17:49:37<span> </span>853 INFO BlockStateChange: BLOCK* addToInvalidates: blk_1073860942_120256 172.16.6.153:50010 172.16.6.152:50010
<span></span>hadoop<span> </span>
172.16.6.152<br>
2015-09-15 17:49:37<span> </span>870 INFO BlockStateChange: BLOCK* addToInvalidates: blk_1073860943_120257 172.16.6.152:50010 172.16.6.151:50010
<span></span>hadoop<span> </span>
172.16.6.152<br>
2015-09-15 17:49:38<span> </span>801 INFO org.apache.hadoop.hdfs.StateChange: BLOCK* BlockManager: ask 172.16.6.151:50010 to delete [blk_1073860943_120257]<span>
</span>hadoop<span> </span>172.16.6.152<br>
2015-09-15 17:49:41<span> </span>801 INFO org.apache.hadoop.hdfs.StateChange: BLOCK* BlockManager: ask 172.16.6.153:50010 to delete [blk_1073860942_120256]<span>
</span>hadoop<span> </span>172.16.6.152<br>
2015-09-15 17:49:44<span> </span>802 INFO org.apache.hadoop.hdfs.StateChange: BLOCK* BlockManager: ask 172.16.6.152:50010 to delete [blk_1073860942_120256<span>
</span>hadoop<span> </span>172.16.6.152<br>
2015-09-15 17:49:45<span> </span>800 INFO org.apache.hadoop.hdfs.server.blockmanagement.CacheReplicationMonitor: Rescanning after 30000 milliseconds<span>
</span>hadoop<span> </span>172.16.6.152<br>
2015-09-15 17:49:45<span> </span>801 INFO org.apache.hadoop.hdfs.server.blockmanagement.CacheReplicationMonitor: Scanned 0 directive(s) and 0 block(s) in 1 millisecond(s).<span>
</span>hadoop<span> </span>172.16.6.152<br>
2015-09-15 17:50:15<span> </span>800 INFO org.apache.hadoop.hdfs.server.blockmanagement.CacheReplicationMonitor: Rescanning after 30000 milliseconds<span>
</span>hadoop<span> </span>172.16.6.152<br>
2015-09-15 17:50:15<span> </span>800 INFO org.apache.hadoop.hdfs.server.blockmanagement.CacheReplicationMonitor: Scanned 0 directive(s) and 0 block(s) in 0 millisecond(s).<span>
</span>hadoop<span> </span>172.16.6.152<br>
2015-09-15 17:50:22<span> </span>104 INFO org.apache.hadoop.hdfs.server.namenode.FSNamesystem: Roll Edit Log from 172.16.6.150<span>
</span>hadoop<span> </span>172.16.6.152<br>
2015-09-15 17:50:22<span> </span>104 INFO org.apache.hadoop.hdfs.server.namenode.FSEditLog: Rolling edit logs<span>
</span>hadoop<span> </span>172.16.6.152<br></p>
<p>附：https://flume.apache.org/FlumeUserGuide.html#hdfs-sink 官网上比较详细的资料</p>
<p><br></p>
            </div>
                </div>