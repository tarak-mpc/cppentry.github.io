---
layout:     post
title:      Hadoop集群之flume安装配置
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/haoxiaoyan/article/details/52805304				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1><a name="_Toc944096346"></a>Hadoop集群之flume安装配置</h1>
<p><a name="_Toc1492779399"></a>1. 官方文档</p>
<p><a href="http://flume.apache.org/" rel="nofollow"><span>http://flume.apache.org/</span></a>　</p>
<p><a name="_Toc35182603"></a>2. 配置环境变量</p>
<p>v<span style="color:#000000;">i /etc/profile</span></p>
<p><span style="color:#000000;">#set flum</span></p>
<p><span style="color:#000000;">export FLUME_HOME=/opt/hadoop/flume-bin</span></p>
<p><span style="color:#000000;">export FLUME_CONF_DIR=$FLUME_HOME/conf</span></p>
<p><span style="color:#000000;">export PATH=$PATH:$FLUME_HOME/bin </span></p>
<p><span style="color:#000000;">sftp&gt; put apache-flume-1.6.0-bin.tar.gz</span></p>
<p><span style="color:#000000;">sftp&gt; put apache-flume-1.6.0-src.tar.gz</span></p>
<p><a name="_Toc1282437140"></a>3. 解压软件包</p>
<p><span style="color:#000000;">[</span><span style="color:#000000;">hadoop@</span><span style="color:#000000;">slavenode1 hadoop]# pwd</span></p>
<p><span style="color:#000000;">/opt/hadoop</span></p>
<p><span style="color:#000000;">tar -zxvf apache-flume-1.</span><span style="color:#000000;">6</span><span style="color:#000000;">.0-src.tar.gz ; tar -zxvf apache-flume-1.</span><span style="color:#000000;">6</span><span style="color:#000000;">.0-bin.tar.gz </span></p>
<p><span style="color:#000000;">[</span><span style="color:#000000;">hadoop@</span><span style="color:#000000;">slavenode1 hadoop]# mv apache-flume-1.6.0-bin flume-bin</span></p>
<p><a name="_Toc1005613176"></a>4. 修改配置文件</p>
<p><span style="color:#000000;">[</span><span style="color:#000000;">hadoop@</span><span style="color:#000000;">slavenode1 flume-bin]# cd conf/</span></p>
<p><span style="color:#000000;">[</span><span style="color:#000000;">hadoop@</span><span style="color:#000000;">slavenode1 conf]# cp flume-env.sh.template flume-env.sh</span></p>
<p><span style="color:#000000;">[</span><span style="color:#000000;">hadoop@</span><span style="color:#000000;">slavenode1 conf]# vi flume-env.sh</span></p>
<p><span style="color:#000000;">export JAVA_HOME=/usr/java/jdk1.7.0_79</span></p>
<p><a name="_Toc462902134"></a>5. 验证是否安装成功</p>
<p><span style="color:#000000;">[</span><span style="color:#000000;">hadoop@</span><span style="color:#000000;">slavenode1 conf]# </span></p>
<p><span style="color:#000000;">[hadoop@slavenode8 conf]$ /opt/hadoop/flume-bin/bin/flume-ng version</span></p>
<p><span style="color:#000000;">Flume 1.6.0</span></p>
<p><span style="color:#000000;">Source code repository: https://git-wip-us.apache.org/repos/asf/flume.git</span></p>
<p><span style="color:#000000;">Revision: 2561a23240a71ba20bf288c7c2cda88f443c2080</span></p>
<p><span style="color:#000000;">Compiled by hshreedharan on Mon May 11 11:15:44 PDT 2015</span></p>
<p><span style="color:#000000;">From source with checksum b29e416802ce9ece3269d34233baf43f</span></p>
<p><a name="_Toc1842328423"></a>6. 分发各节点（<span style="font-family:'Times New Roman';">slavenode1-slavenode7</span><span style="font-family:'宋体';">）</span></p>
<p><span style="color:#000000;">[hadoop@slavenode8 </span><span style="color:#000000;">hadoop</span><span style="color:#000000;">]$ for i in {32,33,34,35,36,37,38};do scp -r flume-bin 192.168.237.2$i:/opt/hadoop/ ; done</span></p>
<p><a name="_Toc1333931680"></a>7. Flume<span style="font-family:'宋体';">采集某个日志的具体实例</span></p>
<p><a href="http://flume.apache.org/FlumeUserGuide.html#hdfs-sink" rel="nofollow"><span></span></a><br></p>
<p><span style="color:#000000;">创建一个新的目录共平时日志采集放配置文件使用。</span></p>
<p><span style="color:#000000;">[hadoop@slavenode4 example]$ </span><span style="color:#000000;">mkdir </span><span style="color:#000000;">/opt/hadoop/flume-bin/example</span></p>
<p><span style="color:#000000;">1) </span><span style="color:#000000;">单节点flume直接写入hdfs，监控一个日志文件</span></p>
<p><span style="color:#000000;">[hadoop@slavenode4 example]$ cat flume_directHDFS.conf </span></p>
<p><span style="color:#000000;"># Define a memory channel called ch1 on agent1</span></p>
<p><span style="color:#000000;background:rgb(255,255,0);">agent1</span><span style="color:#000000;">.channels.ch1.type = memory</span></p>
<p><span style="color:#000000;">agent1.channels.ch1.capacity = 100000</span></p>
<p><span style="color:#000000;">agent1.channels.ch1.transactionCapacity = 100000</span></p>
<p><span style="color:#000000;">agent1.channels.ch1.keep-alive = 30</span></p>
<p><span style="color:#000000;"> </span></p>
<p><span style="color:#000000;"># Define an Avro source called avro-source1 on agent1 and tell it</span></p>
<p><span style="color:#000000;">#define source monitor a file</span></p>
<p><span style="color:#000000;">agent1.sources.avro-source1.type = exec</span></p>
<p><span style="color:#000000;">agent1.sources.avro-source1.shell = /bin/bash -c</span></p>
<p><span style="color:#000000;">agent1.sources.avro-source1.command =</span><span style="color:#000000;background:rgb(255,255,0);">tail -n +0 -F /opt/hadoop/hadoop-2.7.2/logs/hadoop-hadoop-datanode-slavenode4.log </span></p>
<p><span style="color:#000000;">agent1.sources.avro-source1.channels = ch1</span></p>
<p><span style="color:#000000;">agent1.sources.avro-source1.threads = 5</span></p>
<p><span style="color:#000000;"> </span></p>
<p><span style="color:#000000;"># Define a logger sink that simply logs all events it receives</span></p>
<p><span style="color:#000000;"># and connect it to the other end of the same channel.</span></p>
<p><span style="color:#000000;">agent1.sinks.log-sink1.channel = ch1</span></p>
<p><span style="color:#000000;">agent1.sinks.log-sink1.type = hdfs</span></p>
<p><span style="color:#000000;">agent1.sinks.log-sink1.hdfs.path = </span><span style="color:#000000;background:rgb(255,255,0);">hdfs://cluster-ha/flumeTest</span><span style="color:#000000;background:rgb(255,255,0);">  #需要手动创建</span></p>
<p><span style="color:#000000;">agent1.sinks.log-sink1.hdfs.writeFormat = Text</span></p>
<p><span style="color:#000000;">agent1.sinks.log-sink1.hdfs.fileType = DataStream</span></p>
<p><span style="color:#000000;">agent1.sinks.log-sink1.hdfs.rollInterval = 0</span></p>
<p><span style="color:#000000;">agent1.sinks.log-sink1.hdfs.rollSize = 1000000</span></p>
<p><span style="color:#000000;">agent1.sinks.log-sink1.hdfs.rollCount = 0</span></p>
<p><span style="color:#000000;">agent1.sinks.log-sink1.hdfs.batchSize = 1000</span></p>
<p><span style="color:#000000;">agent1.sinks.log-sink1.hdfs.txnEventMax = 1000</span></p>
<p><span style="color:#000000;">agent1.sinks.log-sink1.hdfs.callTimeout = 60000</span></p>
<p><span style="color:#000000;">agent1.sinks.log-sink1.hdfs.appendTimeout = 60000</span></p>
<p><span style="color:#000000;"> </span></p>
<p><span style="color:#000000;"># Finally, now that we've defined all of our components, tell</span></p>
<p><span style="color:#000000;"># agent1 which ones we want to activate.</span></p>
<p><span style="color:#000000;">agent1.channels = ch1</span></p>
<p><span style="color:#000000;">agent1.sources = avro-source1</span></p>
<p><span style="color:#000000;">agent1.sinks = log-sink1</span></p>
<p><span style="color:#000000;"> </span></p>
<p><span style="color:#000000;">[hadoop@masternode2 ~]$ hdfs dfs -mkdir  hdfs://cluster-ha/flumeTest</span></p>
<p><span style="color:#000000;">启动如下命令，之后在hdfs上面看效果</span></p>
<p><span style="color:#000000;">[hadoop@slavenode4 example]$ ../bin/flume-ng agent --conf ../conf/ -f flume_directHDFS.conf -n agent1 </span><span style="color:#000000;background:rgb(255,255,0);">-Dflume.root.logger=INFO,console</span><span style="color:#000000;background:rgb(255,255,0);">　生产环境不用用该选项</span></p>
<p><span style="color:#000000;">当出现如下日志说明配置已成功</span></p>
<p><span style="color:#000000;">2016-10-10 11:16:24,794 (lifecycleSupervisor-1-4) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.start(MonitoredCounterGroup.java:96)] Component type: SOURCE, name: avro-source1 started</span></p>
<p><span style="color:#000000;">2016-10-10 11:16:24,811 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.hdfs.HDFSDataStream.configure(HDFSDataStream.java:58)] Serializer = TEXT, UseRawLocalFileSystem = false</span></p>
<p><span style="color:#000000;">2016-10-10 11:16:25,166 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.hdfs.BucketWriter.open(BucketWriter.java:234)] </span><span style="color:#000000;background:rgb(255,255,0);">Creating hdfs://cluster-ha/flumeTest/FlumeData.1476069384811.tmp</span></p>
<p><span style="color:#000000;">2016-10-10 11:16:25,400 (hdfs-log-sink1-call-runner-0) [WARN - org.apache.hadoop.util.NativeCodeLoader.&lt;clinit&gt;(NativeCodeLoader.java:62)] Unable to load native-hadoop library for your platform... using builtin-java classes where applicable</span></p>
<p><span style="color:#000000;">2016-10-10 11:16:27,593 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.hdfs.BucketWriter.close(BucketWriter.java:363)] Closing hdfs://cluster-ha/flumeTest/FlumeData.1476069384811.tmp</span></p>
<p><span style="color:#000000;">2016-10-10 11:16:27,635 (hdfs-log-sink1-call-runner-4) [INFO - org.apache.flume.sink.hdfs.BucketWriter$8.call(BucketWriter.java:629)] Renaming hdfs://cluster-ha/flumeTest/FlumeData.1476069384811.tmp to hdfs://cluster-ha/flumeTest/FlumeData.1476069384811</span></p>
<p><span style="color:#000000;">2016-10-10 11:16:27,681 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.hdfs.BucketWriter.open(BucketWriter.java:234)] Creating hdfs://cluster-ha/flumeTest/FlumeData.1476069384812.tmp</span></p>
<p><span style="color:#000000;"> </span></p>
<p><span style="color:#000000;">HDFS上查看效果如下。自动出来一些日志文件</span></p>
<p><span style="color:#000000;"> </span></p>
<p><span style="color:#000000;">[hadoop@masternode2 ~]$ hdfs dfs -ls  hdfs://cluster-ha/flumeTest/</span></p>
<p><span style="color:#000000;">Found 2 items</span></p>
<p><span style="color:#000000;">-rw-r--r--   3 hadoop supergroup    1004251 2016-10-10 11:16 hdfs://cluster-ha/flumeTest/FlumeData.1476069384811</span></p>
<p><span style="color:#000000;">-rw-r--r--   3 hadoop supergroup     179996 2016-10-10 11:16 hdfs://cluster-ha/flumeTest/FlumeData.1476069384812.tmp</span></p>
<p><span style="color:#000000;"> </span></p>
<p><span style="color:#000000;">2) </span><span style="color:#000000;">在各个webserv日志机上配置 Flume Clie</span></p>
<p><span style="color:#000000;">Slavenode3机器上配置</span></p>
<p><span style="color:#000000;">[hadoop@slavenode3 logs]$ mkdir -p /opt/hadoop/flume-bin/spool/checkpoint;mkdir  -p /opt/hadoop/flume-bin/spool/data</span></p>
<p><span style="color:#000000;">[hadoop@slavenode3 logs]$ mkdir /opt/hadoop/flume-bin/logs/</span></p>
<p><span style="color:#000000;">[hadoop@slavenode3 logs]$ cd /opt/hadoop/flume-bin/example/</span></p>
<p><span style="color:#000000;">[hadoop@slavenode3 example]$ ls</span></p>
<p><span style="color:#000000;">flume_Consolidation.conf</span></p>
<p><span style="color:#000000;">[hadoop@slavenode3 example]$ cat flume_Consolidation.conf </span></p>
<p><span style="color:#000000;">clientMainAgent.channels = c1</span></p>
<p><span style="color:#000000;">clientMainAgent.sources  = s1</span></p>
<p><span style="color:#000000;">clientMainAgent.sinks    = k1 k2</span></p>
<p><span style="color:#000000;"># clientMainAgent sinks group</span></p>
<p><span style="color:#000000;">clientMainAgent.sinkgroups = g1</span></p>
<p><span style="color:#000000;"># clientMainAgent Spooling Directory Source</span></p>
<p><span style="color:#000000;">clientMainAgent.sources.s1.type = </span><span style="color:#000000;background:rgb(255,255,0);">spooldir</span></p>
<p><span style="color:#000000;">clientMainAgent.sources.s1.spoolDir  =</span><span style="color:#000000;background:rgb(255,255,0);">/opt/hadoop/flume-bin/logs/</span></p>
<p><span style="color:#000000;">clientMainAgent.sources.s1.fileHeader = true</span></p>
<p><span style="color:#000000;">clientMainAgent.sources.s1.deletePolicy =immediate</span></p>
<p><span style="color:#000000;">clientMainAgent.sources.s1.batchSize =1000</span></p>
<p><span style="color:#000000;">clientMainAgent.sources.s1.channels =c1</span></p>
<p><span style="color:#000000;">clientMainAgent.sources.s1.deserializer.maxLineLength =1048576</span></p>
<p><span style="color:#000000;"># clientMainAgent FileChannel</span></p>
<p><span style="color:#000000;">clientMainAgent.channels.c1.type = file</span></p>
<p><span style="color:#000000;">clientMainAgent.channels.c1.checkpointDir = </span><span style="color:#000000;background:rgb(255,255,0);">/opt/hadoop/flume-bin/spool/checkpoint</span></p>
<p><span style="color:#000000;">clientMainAgent.channels.c1.dataDirs </span><span style="color:#000000;background:rgb(255,255,0);">= /opt/hadoop/flume-bin/spool/data</span></p>
<p><span style="color:#000000;">clientMainAgent.channels.c1.capacity = 200000000</span></p>
<p><span style="color:#000000;">clientMainAgent.channels.c1.keep-alive = 30</span></p>
<p><span style="color:#000000;">clientMainAgent.channels.c1.write-timeout = 30</span></p>
<p><span style="color:#000000;">clientMainAgent.channels.c1.checkpoint-timeout=600</span></p>
<p><span style="color:#000000;"># clientMainAgent Sinks</span></p>
<p><span style="color:#000000;"># k1 sink</span></p>
<p><span style="color:#000000;">clientMainAgent.sinks.k1.channel = c1</span></p>
<p><span style="color:#000000;">clientMainAgent.sinks.k1.type = avro</span></p>
<p><span style="color:#000000;"># connect to CollectorMainAgent</span></p>
<p><span style="color:#000000;">clientMainAgent.sinks.k1.hostname = </span><span style="color:#000000;background:rgb(255,255,0);">slavenode4</span></p>
<p><span style="color:#000000;">clientMainAgent.sinks.k1.port = </span><span style="color:#000000;background:rgb(255,255,0);">41415 </span></p>
<p><span style="color:#000000;"># k2 sink</span></p>
<p><span style="color:#000000;">clientMainAgent.sinks.k2.channel = c1</span></p>
<p><span style="color:#000000;">clientMainAgent.sinks.k2.type = avro</span></p>
<p><span style="color:#000000;"># connect to CollectorBackupAgent</span></p>
<p><span style="color:#000000;">clientMainAgent.sinks.k2.hostname = </span><span style="color:#000000;background:rgb(255,255,0);">slavenode3</span></p>
<p><span style="color:#000000;">clientMainAgent.sinks.k2.port = </span><span style="color:#000000;background:rgb(255,255,0);">41415</span></p>
<p><span style="color:#000000;"># clientMainAgent sinks group</span></p>
<p><span style="color:#000000;">clientMainAgent.sinkgroups.g1.sinks = k1 k2</span></p>
<p><span style="color:#000000;"># load_balance type</span></p>
<p><span style="color:#000000;">clientMainAgent.sinkgroups.g1.processor.type = load_balance</span></p>
<p><span style="color:#000000;">clientMainAgent.sinkgroups.g1.processor.backoff   = true</span></p>
<p><span style="color:#000000;">clientMainAgent.sinkgroups.g1.processor.selector  = random</span></p>
<p><span style="color:#000000;background:rgb(255,255,0);">[hadoop@slavenode3 example]$scp flume_Consolidation.conf hadoop@slavenode4:/opt/hadoop/flume-bin/example/</span></p>
<p><span style="color:#000000;">Slavenode4机器上配置</span></p>
<p><span style="color:#000000;">[hadoop@slavenode</span><span style="color:#000000;">4</span><span style="color:#000000;"> logs]$ mkdir -p /opt/hadoop/flume-bin/spool/checkpoint;mkdir  -p /opt/hadoop/flume-bin/spool/data</span></p>
<p><span style="color:#000000;">[hadoop@slavenode</span><span style="color:#000000;">4</span><span style="color:#000000;"> logs]$ mkdir /opt/hadoop/flume-bin/logs/</span></p>
<p><span style="color:#000000;">[hadoop@slavenode</span><span style="color:#000000;">4</span><span style="color:#000000;"> logs]$ cd /opt/hadoop/flume-bin/example/</span></p>
<p><span style="color:#000000;"> </span></p>
<p><span style="color:#000000;">Server服务器配置</span></p>
<p><span style="color:#000000;"> </span></p>
<p><span style="color:#000000;">[hadoop@slavenode8 example]$ mkdir -p /opt/hadoop/flume-bin/spool_back/data</span></p>
<p><span style="color:#000000;">[hadoop@slavenode8 example]$  mkdir -p /opt/hadoop/flume-bin/spool/checkpoint;mkdir  -p /opt/hadoop/flume-bin/spool/data</span></p>
<p><span style="color:#000000;">[hadoop@slavenode8 example]$ cat flume_Consolidation.conf </span></p>
<p><span style="color:#000000;">collectorMainAgent.channels = c2</span></p>
<p><span style="color:#000000;">collectorMainAgent.sources  = s2</span></p>
<p><span style="color:#000000;">collectorMainAgent.sinks    =k1 k2</span></p>
<p><span style="color:#000000;"># collectorMainAgent AvroSource</span></p>
<p><span style="color:#000000;">collectorMainAgent.sources.s2.type = avro</span></p>
<p><span style="color:#000000;">collectorMainAgent.sources.s2.bind = slavenode8</span></p>
<p><span style="color:#000000;">collectorMainAgent.sources.s2.port = 41415</span></p>
<p><span style="color:#000000;">collectorMainAgent.sources.s2.channels = c2</span></p>
<p><span style="color:#000000;"># collectorMainAgent FileChannel</span></p>
<p><span style="color:#000000;">collectorMainAgent.channels.c2.type = file</span></p>
<p><span style="color:#000000;">collectorMainAgent.channels.c2.checkpointDir =/opt/hadoop/flume-bin/spool/checkpoint</span></p>
<p><span style="color:#000000;">collectorMainAgent.channels.c2.dataDirs = /opt/hadoop/flume-bin/spool/data,/opt/hadoop/flume-bin/spool_back/data</span></p>
<p><span style="color:#000000;">collectorMainAgent.channels.c2.capacity = 200000000</span></p>
<p><span style="color:#000000;">collectorMainAgent.channels.c2.transactionCapacity=6000</span></p>
<p><span style="color:#000000;">collectorMainAgent.channels.c2.checkpointInterval=60000</span></p>
<p><span style="color:#000000;"># collectorMainAgent hdfsSink</span></p>
<p><span style="color:#000000;">collectorMainAgent.sinks.k2.type = hdfs</span></p>
<p><span style="color:#000000;">collectorMainAgent.sinks.k2.channel = c2</span></p>
<p><span style="color:#000000;">collectorMainAgent.sinks.k2.hdfs.path = hdfs://cluster-ha/flume%{dir}</span></p>
<p><span style="color:#000000;">collectorMainAgent.sinks.k2.hdfs.filePrefix =k2_%{file}</span></p>
<p><span style="color:#000000;">collectorMainAgent.sinks.k2.hdfs.inUsePrefix =_</span></p>
<p><span style="color:#000000;">collectorMainAgent.sinks.k2.hdfs.inUseSuffix =.tmp</span></p>
<p><span style="color:#000000;">collectorMainAgent.sinks.k2.hdfs.rollSize = 0</span></p>
<p><span style="color:#000000;">collectorMainAgent.sinks.k2.hdfs.rollCount = 0</span></p>
<p><span style="color:#000000;">collectorMainAgent.sinks.k2.hdfs.rollInterval = 240</span></p>
<p><span style="color:#000000;">collectorMainAgent.sinks.k2.hdfs.writeFormat = Text</span></p>
<p><span style="color:#000000;">collectorMainAgent.sinks.k2.hdfs.fileType = DataStream</span></p>
<p><span style="color:#000000;">collectorMainAgent.sinks.k2.hdfs.batchSize = 6000</span></p>
<p><span style="color:#000000;">collectorMainAgent.sinks.k2.hdfs.callTimeout = 60000</span></p>
<p><span style="color:#000000;">collectorMainAgent.sinks.k1.type = hdfs</span></p>
<p><span style="color:#000000;">collectorMainAgent.sinks.k1.channel = c2</span></p>
<p><span style="color:#000000;">collectorMainAgent.sinks.k1.hdfs.path = hdfs://cluster-ha/flume%{dir}</span></p>
<p><span style="color:#000000;">collectorMainAgent.sinks.k1.hdfs.filePrefix =k1_%{file}</span></p>
<p><span style="color:#000000;">collectorMainAgent.sinks.k1.hdfs.inUsePrefix =_</span></p>
<p><span style="color:#000000;">collectorMainAgent.sinks.k1.hdfs.inUseSuffix =.tmp</span></p>
<p><span style="color:#000000;">collectorMainAgent.sinks.k1.hdfs.rollSize = 0</span></p>
<p><span style="color:#000000;">collectorMainAgent.sinks.k1.hdfs.rollCount = 0</span></p>
<p><span style="color:#000000;">collectorMainAgent.sinks.k1.hdfs.rollInterval = 240</span></p>
<p><span style="color:#000000;">collectorMainAgent.sinks.k1.hdfs.writeFormat = Text</span></p>
<p><span style="color:#000000;">collectorMainAgent.sinks.k1.hdfs.fileType = DataStream</span></p>
<p><span style="color:#000000;">collectorMainAgent.sinks.k1.hdfs.batchSize = 6000</span></p>
<p><span style="color:#000000;">collectorMainAgent.sinks.k1.hdfs.callTimeout = 60000</span></p>
<p><span style="color:#000000;"> </span></p>
<p><span style="color:#000000;">[hadoop@slavenode8 example]$ ../bin/flume-ng agent --conf ../conf/ -f flume_Consolidation.conf -n collectorMainAgent -Dflume.root.logger=DEBUG,console</span></p>
<p><span style="color:#000000;">该目录下会自动出现两个目录</span></p>
<p><span style="color:#000000;background:rgb(255,255,0);">[hadoop@slavenode8 spool]$ ls</span></p>
<p><span style="color:#000000;background:rgb(255,255,0);">checkpoint  data</span></p>
<p><span style="color:#000000;background:rgb(255,255,0);"> </span></p>
            </div>
                </div>