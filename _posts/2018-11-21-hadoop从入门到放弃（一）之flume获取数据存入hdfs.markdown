---
layout:     post
title:      hadoop从入门到放弃（一）之flume获取数据存入hdfs
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许最好别转载，你非要转我也没办法。 ╮(╯▽╰)╭					https://blog.csdn.net/u011478909/article/details/52164109				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1>一、<strong>解压<span style="font-family:Calibri;">flume</span><span style="font-family:'宋体';">到</span><span style="font-family:Calibri;">/hadoop/</span><span style="font-family:'宋体';">目录下</span></strong></h1>
<p></p>
<pre><code class="language-plain">tar -zxvf apache-flume-1.6.0-bin.tar.gz  -C /hadoop/</code></pre><br><br><p></p>
<h1>二、<strong>配置<span style="font-family:Calibri;">flume</span><span style="font-family:'宋体';">配置文件</span></strong></h1>
<pre><code class="language-plain">[hadoop@hadoop01 flume]$ cat conf/agent1.conf

# Name the components on this agent

agent1.sources = spooldirSource

agent1.channels = fileChannel

agent1.sinks = hdfsSink

 

# Describe/configure the source

agent1.sources.spooldirSource.type=spooldir

agent1.sources.spooldirSource.spoolDir=/home/hadoop/spooldir

 

# Describe the sink

agent1.sinks.hdfsSink.type=hdfs

agent1.sinks.hdfsSink.hdfs.path=hdfs://hadoop01:9000/flume/%y-%m-%d/%H%M/%S

agent1.sinks.hdfsSink.hdfs.round = true

agent1.sinks.hdfsSink.hdfs.roundValue = 10

agent1.sinks.hdfsSink.hdfs.roundUnit = minute

agent1.sinks.hdfsSink.hdfs.useLocalTimeStamp = true

agent1.sinks.hdfsSink.hdfs.fileType=DataStream  

 

# Describe the channel

agent1.channels.fileChannel.type = file

agent1.channels.fileChannel.dataDirs=/hadoop/flume/datadir

 

# Bind the source and sink to the channel

agent1.sources.spooldirSource.channels=fileChannel

agent1.sinks.hdfsSink.channel=fileChannel</code></pre><br><br><h1>三、<strong>启动<span style="font-family:Calibri;">flume</span></strong></h1>
<p>进入<span style="font-family:'Courier New' !important;">flume home</span><span style="font-family:'宋体';">目录</span></p>
<p>bin/flume-ng agent --conf conf --conf-file conf/agent1.conf --name agent1 -Dflume.root.logger=INFO,console</p>
<p> </p>
<p>启动成功有如下输出</p>
<pre><code class="language-plain">....................................

2016-08-09 16:28:33,888 (conf-file-poller-0) [INFO - org.apache.flume.node.Application.startAllComponents(Application.java:173)] Starting Sink hdfsSink

2016-08-09 16:28:33,891 (conf-file-poller-0) [INFO - org.apache.flume.node.Application.startAllComponents(Application.java:184)] Starting Source spooldirSource

2016-08-09 16:28:33,891 (lifecycleSupervisor-1-3) [INFO - org.apache.flume.source.SpoolDirectorySource.start(SpoolDirectorySource.java:78)] SpoolDirectorySource source starting with directory: /home/hadoop/spooldir

2016-08-09 16:28:33,900 (lifecycleSupervisor-1-1) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.register(MonitoredCounterGroup.java:120)] Monitored counter group for type: SINK, name: hdfsSink: Successfully registered new MBean.

2016-08-09 16:28:33,900 (lifecycleSupervisor-1-1) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.start(MonitoredCounterGroup.java:96)] Component type: SINK, name: hdfsSink started

2016-08-09 16:28:33,925 (lifecycleSupervisor-1-3) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.register(MonitoredCounterGroup.java:120)] Monitored counter group for type: SOURCE, name: spooldirSource: Successfully registered new MBean.

2016-08-09 16:28:33,925 (lifecycleSupervisor-1-3) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.start(MonitoredCounterGroup.java:96)] Component type: SOURCE, name: spooldirSource started</code></pre><br><br><p> </p>
<h1>四、<strong>将日志写入到<span style="font-family:Calibri;">flume spooldir</span><span style="font-family:'宋体';">下</span></strong></h1>
<p>写入完成之后可以看到如下输出：</p>
<pre><code class="language-plain">2016-08-09 16:36:51,204 (pool-4-thread-1) [INFO - org.apache.flume.client.avro.ReliableSpoolingFileEventReader.readEvents(ReliableSpoolingFileEventReader.java:258)] Last read took us just up to a file boundary. Rolling to the next file, if there is one.

2016-08-09 16:36:51,204 (pool-4-thread-1) [INFO - org.apache.flume.client.avro.ReliableSpoolingFileEventReader.rollCurrentFile(ReliableSpoolingFileEventReader.java:348)] Preparing to move file /home/hadoop/spooldir/HTTP_20130313143750.dat to /home/hadoop/spooldir/HTTP_20130313143750.dat.COMPLETED

2016-08-09 16:36:53,965 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.hdfs.HDFSDataStream.configure(HDFSDataStream.java:58)] Serializer = TEXT, UseRawLocalFileSystem = false

2016-08-09 16:36:54,206 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.hdfs.BucketWriter.open(BucketWriter.java:234)] Creating hdfs://hadoop01:9000/flume/16-08-09/1630/00/FlumeData.1470731813966.tmp

2016-08-09 16:36:56,772 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.hdfs.BucketWriter.close(BucketWriter.java:363)] Closing hdfs://hadoop01:9000/flume/16-08-09/1630/00/FlumeData.1470731813966.tmp

2016-08-09 16:36:56,903 (hdfs-hdfsSink-call-runner-3) [INFO - org.apache.flume.sink.hdfs.BucketWriter$8.call(BucketWriter.java:629)] Renaming hdfs://hadoop01:9000/flume/16-08-09/1630/00/FlumeData.1470731813966.tmp to hdfs://hadoop01:9000/flume/16-08-09/1630/00/FlumeData.1470731813966

2016-08-09 16:36:57,149 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.hdfs.BucketWriter.open(BucketWriter.java:234)] Creating hdfs://hadoop01:9000/flume/16-08-09/1630/00/FlumeData.1470731813967.tmp

2016-08-09 16:36:57,637 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.hdfs.BucketWriter.close(BucketWriter.java:363)] Closing hdfs://hadoop01:9000/flume/16-08-09/1630/00/FlumeData.1470731813967.tmp

2016-08-09 16:36:57,805 (hdfs-hdfsSink-call-runner-7) [INFO - org.apache.flume.sink.hdfs.BucketWriter$8.call(BucketWriter.java:629)] Renaming hdfs://hadoop01:9000/flume/16-08-09/1630/00/FlumeData.1470731813967.tmp to hdfs://hadoop01:9000/flume/16-08-09/1630/00/FlumeData.1470731813967

2016-08-09 16:36:57,955 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.hdfs.BucketWriter.open(BucketWriter.java:234)] Creating hdfs://hadoop01:9000/flume/16-08-09/1630/00/FlumeData.1470731813968.tmp

2016-08-09 16:37:03,525 (Log-BackgroundWorker-fileChannel) [INFO - org.apache.flume.channel.file.EventQueueBackingStoreFile.beginCheckpoint(EventQueueBackingStoreFile.java:230)] Start checkpoint for /home/hadoop/.flume/file-channel/checkpoint/checkpoint, elements to sync = 22

2016-08-09 16:37:03,566 (Log-BackgroundWorker-fileChannel) [INFO - org.apache.flume.channel.file.EventQueueBackingStoreFile.checkpoint(EventQueueBackingStoreFile.java:255)] Updating checkpoint metadata: logWriteOrderID: 1470731313610, queueSize: 0, queueHead: 20

2016-08-09 16:37:03,572 (Log-BackgroundWorker-fileChannel) [INFO - org.apache.flume.channel.file.Log.writeCheckpoint(Log.java:1034)] Updated checkpoint for file: /hadoop/flume/datadir/log-5 position: 4155 logWriteOrderID: 1470731313610

2016-08-09 16:37:03,572 (Log-BackgroundWorker-fileChannel) [INFO - org.apache.flume.channel.file.LogFile$RandomReader.close(LogFile.java:504)] Closing RandomReader /hadoop/flume/datadir/log-3

2016-08-09 16:37:28,072 (hdfs-hdfsSink-roll-timer-0) [INFO - org.apache.flume.sink.hdfs.BucketWriter.close(BucketWriter.java:363)] Closing hdfs://hadoop01:9000/flume/16-08-09/1630/00/FlumeData.1470731813968.tmp

2016-08-09 16:37:28,182 (hdfs-hdfsSink-call-runner-3) [INFO - org.apache.flume.sink.hdfs.BucketWriter$8.call(BucketWriter.java:629)] Renaming hdfs://hadoop01:9000/flume/16-08-09/1630/00/FlumeData.1470731813968.tmp to hdfs://hadoop01:9000/flume/16-08-09/1630/00/FlumeData.1470731813968

2016-08-09 16:37:28,364 (hdfs-hdfsSink-roll-timer-0) [INFO - org.apache.flume.sink.hdfs.HDFSEventSink$1.run(HDFSEventSink.java:394)] Writer callback called.</code></pre><br><br><p align="justify"> </p>
<p align="justify"> </p>
<p>查看<span style="font-family:Calibri;">hdfs</span><span style="font-family:'宋体';">上相应目录</span></p>
<pre><code class="language-plain">[hadoop@hadoop01 spooldir]$ hadoop fs -ls /flume/16-08-09/1630/00

Found 3 items

-rw-r--r--   3 hadoop supergroup        969 2016-08-09 16:36 /flume/16-08-09/1630/00/FlumeData.1470731813966

-rw-r--r--   3 hadoop supergroup       1070 2016-08-09 16:36 /flume/16-08-09/1630/00/FlumeData.1470731813967

-rw-r--r--   3 hadoop supergroup        191 2016-08-09 16:37 /flume/16-08-09/1630/00/FlumeData.1470731813968</code></pre><br><br><p align="justify"> </p>
<p align="justify">查看<span style="font-family:Calibri;">hdfs</span><span style="font-family:'宋体';">上文件内容</span></p>
<pre><code class="language-plain">[hadoop@hadoop01 spooldir]$ hadoop fs -cat /flume/16-08-09/1630/00/*

﻿1363157985066 13726230503 00-FD-07-A4-72-B8:CMCC 120.196.100.82 i02.c.aliimg.com 24 27 2481 2468200

1363157995052 13826544101 5C-0E-8B-C7-F1-E0:CMCC 120.197.40.4 4 0 264 0 200

1363157991076 13926435656 20-10-7A-28-CC-0A:CMCC 120.196.100.99 2 4 132 1512 200

1363154400022 13926251106 5C-0E-8B-8B-B1-50:CMCC 120.197.40.4 4 0 240 0 200

1363157993044 18211575961 94-71-AC-CD-E6-18:CMCC-EASY 120.196.100.99 iface.qiyi.com 视频网站 15 12 15272106 200

1363157995074 84138413 5C-0E-8B-8C-E8-20:7DaysInn 120.197.40.4 122.72.52.12 20 16 4116 1432200

1363157993055 13560439658 C4-17-FE-BA-DE-D9:CMCC 120.196.100.99 18 15 1116 954 200

1363157995033 15920133257 5C-0E-8B-C7-BA-20:CMCC 120.197.40.4 sug.so.360.cn 信息安全 20 20 3156 2936200

1363157983019 13719199419 68-A1-B7-03-07-B1:CMCC-EASY 120.196.100.82 4 0 240 0 200

1363157984041 13660577991 5C-0E-8B-92-5C-20:CMCC-EASY 120.197.40.4 s19.cnzz.com 站点统计 24 9 6960690 200

1363157973098 15013685858 5C-0E-8B-C7-F7-90:CMCC 120.197.40.4 rank.ie.sogou.com 搜索引擎 28 27 36593538 200

1363157986029 15989002119 E8-99-C4-4E-93-E0:CMCC-EASY 120.196.100.99 www.umeng.com 站点统计 3 3 1938180 200

1363157992093 13560439658 C4-17-FE-BA-DE-D9:CMCC 120.196.100.99 15 9 918 4938 200

1363157986041 13480253104 5C-0E-8B-C7-FC-80:CMCC-EASY 120.197.40.4 3 3 180 180 200

1363157984040 13602846565 5C-0E-8B-8B-B6-00:CMCC 120.197.40.4 2052.flash2-http.qq.com 综合门户 15 12 19382910 200

1363157995093 13922314466 00-FD-07-A2-EC-BA:CMCC 120.196.100.82 img.qfc.cn 12 12 3008 3720 200

1363157982040 13502468823 5C-0A-5B-6A-0B-D4:CMCC-EASY 120.196.100.99 y0.ifengimg.com 综合门户 57 102 7335110349 200

1363157986072 18320173382 84-25-DB-4F-10-1A:CMCC-EASY 120.196.100.99 input.shouji.sogou.com 搜索引擎 21 18  9531 2412 200

1363157990043 13925057413 00-1F-64-E1-E6-9A:CMCC 120.196.100.55 t3.baidu.com 搜索引擎 69 63 11058 4824200

1363157988072 13760778710 00-FD-07-A4-7B-08:CMCC 120.196.100.82 2 2 120 120 200

1363157985066 13726238888 00-FD-07-A4-72-B8:CMCC 120.196.100.82 i02.c.aliimg.com 24 27 2481 2468200

1363157993055 13560436666 C4-17-FE-BA-DE-D9:CMCC 120.196.100.99 18 15 1116 954 200

[hadoop@hadoop01 spooldir]$ </code></pre><br><br><p>上传成功</p>
            </div>
                </div>