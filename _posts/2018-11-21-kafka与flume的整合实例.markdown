---
layout:     post
title:      kafka与flume的整合实例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>一、整体步骤：</p>

<p>1.首先安装kafka，配置flume。创建kafka的topic（利用zookeeper进行管理，所以首先要安装zookeeper）</p>

<p>2.将文件放置在flume的source目录下，启动flume。将文件读取到指定的kafka的topic中。</p>

<p>3.启动的kafka的consumer端。</p>

<p> </p>

<p>二、具体整合过程：</p>

<p>1.前提kafka和flume已经安装好，我们主要讲解整合过程。</p>

<p>2，创建kafka的topic</p>

<p>：[root@hadoop11 ~]# kafka-topic.sh --create --topic <span style="color:#f33b45;">mytopic</span> --replication-factor 1 --partition 10 --zookeeper localhosts:2181</p>

<p>查看创建topic：</p>

<p>[root@hadoop11 ~]# kafka-topic.sh --list --zookeeper localhosts:2181   </p>

<p>3.flume的读取文件到kafka的配置,在flume的conf目录下创建<span style="color:#f33b45;">flume-dirToKafka.properties，添加如下配置</span>：</p>

<table border="1" cellpadding="1" cellspacing="1" style="width:500px;"><tbody><tr><td>
			<p>[root@hadoop11 conf]# cat <span style="color:#f33b45;">flume-dirToKafka.properties </span><br><span style="color:#f33b45;">#agent1 name</span><br>
			agent1.sources=source1<br>
			agent1.sinks=sink1<br>
			agent1.channels=channel1</p>

			<p><br><span style="color:#f33b45;">#set source1</span><br>
			agent1.sources.source1.type=spooldir</p>

			<p>#注意创建目录的权限问题：chmod 777 -R （flumePath）和（dir）<br>
			agent1.sources.source1.spoolDir=/yangxiaohai/flumePath/dir/logdfs<br>
			agent1.sources.source1.channels=channel1<br>
			agent1.sources.source1.fileHeader = false<br>
			agent1.sources.source1.interceptors = i1<br>
			agent1.sources.source1.interceptors.i1.type = timestamp</p>

			<p> </p>

			<p><span style="color:#f33b45;">#set sink1</span></p>

			<p><span style="color:#f33b45;">#设置获取数据存储位置，这里是kafka，如果是hdfs，就设置为相应的hdfs</span><br>
			agent1.sinks.sink1.type = org.apache.flume.sink.kafka.KafkaSink<br>
			agent1.sinks.sink1.topic =mytopic（创建的kafka topic）<br>
			agent1.sinks.sink1.brokerList = hadoop11:9092,hadoop12:9092,hadoop13:9092<br>
			agent1.sinks.sink1.requiredAcks = 1<br>
			agent1.sinks.sink1.batchSize = 100<br>
			agent1.sinks.sink1.channel = channel1</p>

			<p> </p>

			<p>#<span style="color:#f33b45;">set channel1</span><br>
			agent1.channels.channel1.type=file<br>
			agent1.channels.channel1.checkpointDir=/yangxiaohai/flumePath/dir/logdfstmp/point <br>
			agent1.channels.channel1.dataDirs=/yangxiaohai/flumePath/dir/logdfstmp</p>
			</td>
		</tr></tbody></table><p>4.启动flume：</p>

<p>注意：agent1为配置文件中设置的agent命名，要对应，不然启动会卡主不动。大致为：no configuration host=错误名</p>

<p>[root@hadoop11 bin]# ./flume-ng agent --conf conf --conf-file ../conf/flume-dirToKafka.properties --name <span style="color:#f33b45;">agent1</span> -Dflume.root.logger=INFO,console </p>

<p><span style="color:#f33b45;">注意：在这里可能会报一个错误，如下：</span></p>

<table border="1" cellpadding="1" cellspacing="1" style="width:600px;"><tbody><tr><td>18/11/07 00:39:29 INFO instrumentation.MonitoredCounterGroup: Component type: SINK, name: sink1 started<br>
			18/11/07 00:39:29 <span style="color:#f33b45;">ERROR kafka.KafkaSink: Failed to publish events</span><br><span style="color:#f33b45;">java.lang.IllegalStateException: Empty value [channel=[channel=channel1]]</span><br>
			        at com.google.common.base.Preconditions.checkState(Preconditions.java:145)<br>
			        at org.apache.flume.channel.file.FlumeEventQueue.removeHead(FlumeEventQueue.java:160)<br>
			        at org.apache.flume.channel.file.FileChannel$FileBackedTransaction.doTake(FileChannel.java:512)<br>
			        at org.apache.flume.channel.BasicTransactionSemantics.take(BasicTransactionSemantics.java:113)<br>
			        at org.apache.flume.channel.BasicChannelSemantics.take(BasicChannelSemantics.java:95)<br>
			        at org.apache.flume.sink.kafka.KafkaSink.process(KafkaSink.java:97)<br>
			        at org.apache.flume.sink.DefaultSinkProcessor.process(DefaultSinkProcessor.java:68)<br>
			        at org.apache.flume.SinkRunner$PollingRunner.run(SinkRunner.java:147)<br>
			        at java.lang.Thread.run(Thread.java:745)<br>
			18/11/07 00:39:29 ERROR flume.SinkRunner: Unable to deliver event. Exception follows.<br>
			org.apache.flume.EventDeliveryException: Failed to publish events</td>
		</tr></tbody></table><p>解决方法：</p>

<p>     删除checkpointDir=/flumePath/dir/logdfstmp/point 目录下的所有文件，再次启动，就可以了。</p>

<p>flume启动成功后，会显示如下，已将spoolDir=/flumePath/dir/logdfs下的文件读入topic中，在此处于停留监控状态，当我们在监控目录下传入数据时，会在此下面显示：</p>

<table border="1" cellpadding="1" cellspacing="1" style="width:700px;"><tbody><tr><td>18/11/07 00:47:20 INFO producer.SyncProducer: Connected to hadoop13:9092 for producing<br>
			18/11/07 00:47:20 INFO producer.SyncProducer: Disconnecting from hadoop13:9092<br>
			18/11/07 00:47:20 INFO producer.SyncProducer: Disconnecting from hadoop12:9092<br>
			18/11/07 00:47:20 INFO producer.SyncProducer: Connected to hadoop12:9092 for producing<br>
			18/11/07 00:47:43 INFO file.EventQueueBackingStoreFile: Start checkpoint for /yangxiaohai/flumePath/dir/logdfstmp/point/checkpoint, elements to sync = 26806<br>
			18/11/07 00:47:43 INFO file.EventQueueBackingStoreFile: Updating checkpoint metadata: logWriteOrderID: 1541522865614, queueSize: 0, queueHead: 43508<br>
			18/11/07 00:47:43 INFO file.Log: Updated checkpoint for file:/yangxiaohai/flumePath/dir/logdfstmp/log-12 position: 1108730 logWriteOrderID: 1541522865614<br><span style="color:#f33b45;">18/11/07 00:47:43 INFO file.LogFile: Closing RandomReader /yangxiaohai/flumePath/dir/logdfstmp/log-8<br>
			18/11/07 00:47:43 INFO file.LogFile: Closing RandomReader /yangxiaohai/flumePath/dir/logdfstmp/log-9<br>
			18/11/07 00:47:43 INFO file.LogFile: Closing RandomReader /yangxiaoha</span>i<span style="color:#f33b45;">/flumePath/dir/logdfstmp/log-10</span></td>
		</tr></tbody></table><p>5.这时flume已经source目录下的文件读入kafka的mytopic中，这时我们启动kafka的consumer，这时会有文件不断的被独处，结果如下：</p>

<p>启动consumer:</p>

<p>[root@hadoop11 ~]# kafka-console-consumer.sh --zookeeper localhost:2181 --topic mytopic(创建的topic) --from-beginning</p>

<p>读取的文件的结果：</p>

<table border="1" cellpadding="1" cellspacing="1" style="width:700px;"><tbody><tr><td>{"timestamp":"2017-02-11T10:49:43.043Z","url":"/original/index_6.html","is_entrance":0,"data_type":"null","channel":"null","to_target":0,"keyword":"","search_engine":"","country":"中国","area":"华北","province":"北京市","city":"北京市","use_duration_cat":"null","domain":"www.donews.com","to_domain":0,"use_interval_cat":"null","is_exit":0,"event":"startup","os":"Windows XP","os_type":"pc","browser":"Firefox","browser_version":"Firefox 9.0.1","suuid":"47ab648cb5c15bc8e1952efc16a037cb","short_cookie":"null","ip":"118.192.66.41","use_duration":0,"use_interval":0,"pv_cat":"null","event_name":[],"refer":"","hour":"10","gender":"null","age":0,"account_level":0,"payment_method":"","consumption_point":"","money":0.0,"account":"","zone_id":"","app_version":"","network":"null","nettype":"null","lang":"","app_upgrade_from":"","display":"null","device_type":"null","register_days":0,"refer_domain":"null","appkey":"donews_website_nginx_log","day":"2017-02-11"}<br>
			{"timestamp":"2017-02-11T10:22:01.001Z","url":"/column/get_adv_bottom","is_entrance":0,"data_type":"null","channel":"null","to_target":0,"keyword":"","search_engine":"","country":"中国","area":"华东","province":"福建省","city":"福州市","use_duration_cat":"null","domain":"www.donews.com","to_domain":0,"use_interval_cat":"null","is_exit":0,"event":"jump","os":"Windows 7","os_type":"pc","browser":"Internet Explorer","browser_version":"Internet Explorer 7.0","suuid":"4f41eff515e7be6774749383270794e7","short_cookie":"null","ip":"112.5.236.153","use_duration":0,"use_interval":0,"pv_cat":"null","event_name":[],"refer":"http://www.donews.com/ent/index","hour":"10","gender":"null","age":0,"account_level":0,"payment_method":"","consumption_point":"","money":0.0,"account":"","zone_id":"","app_version":"","network":"null","nettype":"null","lang":"","app_upgrade_from":"","display":"null","device_type":"null","register_days":0,"refer_domain":"null","appkey":"donews_website_nginx_log","day":"2017-02-11"}<br>
			{"timestamp":"2017-02-11T10:22:14.014Z","url":"/editor/person/34","is_entrance":0,"data_type":"null","channel":"null","to_target":0,"keyword^C<br>
			[root@hadoop11 ~]# ^C</td>
		</tr></tbody></table><p>6.这时当我们在次向flume的spoolDir=/flumePath/dir/logdfs下传入文件时，flume将会监控到，并显示。我们传入一个test.txt.</p>

<table border="1" cellpadding="1" cellspacing="1" style="width:700px;"><tbody><tr><td>[root@hadoop11 dir]# scp -r test.txt ./logdfs<br>
			[root@hadoop11 dir]# cd logdfs<br>
			[root@hadoop11 logdfs]# ll<br>
			-rw-r--r--. 1 root root 14506039 8月  19 2017 sdkJson.log.COMPLETED<br>
			-rw-r--r--. 1 root root       34 11月  7 00:50 <span style="color:#f33b45;">test.txt.COMPLETED</span></td>
		</tr></tbody></table><p>flume端的监控结果：</p>

<table border="1" cellpadding="1" cellspacing="1" style="width:700px;"><tbody><tr><td>
			<p>18/11/07 00:47:43 INFO file.LogFile: Closing RandomReader /yangxiaohai/flumePath/dir/logdfstmp/log-8<br>
			1<span style="color:#f33b45;">8/11/07 00:47:43 INFO file.LogFile: Closing RandomReader /yangxiaohai/flumePath/dir/logdfstmp/log-9<br>
			18/11/07 00:47:43 INFO file.LogFile: Closing RandomReader /yangxiaohai/flumePath/dir/logdfstmp/log-10</span></p>

			<p><span style="color:#f33b45;">（以上是启动时停留的位置，下面是添加文件后增加的日志）</span></p>

			<p><br>
			18/11/07 00:50:58 INFO avro.ReliableSpoolingFileEventReader: Last read took us just up to a file boundary. Rolling to the next file, if there is one.<br>
			18/11/07 00:50:58 INFO avro.ReliableSpoolingFileEventReader: <span style="color:#f33b45;">Preparing to move file /flumePath/dir/logdfs/test.txt to /yangxiaohai/flumePath/dir/logdfs/test.txt.COMPLETED</span><br><span style="color:#f33b45;">18/11/07 00:51:13 INFO file.EventQueueBackingStoreFile: Start checkpoint for </span>/<span style="color:#f33b45;">yangxiaohai/flumePath/dir/logdfstmp/point/checkpoint, elements to sync = 2<br>
			18/11/07 00:51:13 INFO file.EventQueueBackingStoreFile: Updating checkpoint metadata: logWriteOrderID: 1541522865621, queueSize: 0, queueHead: 43508<br>
			18/11/07 00:51:13 INFO file.Log: Updated checkpoint for file:/yangxiaohai/flumePath/dir/logdfstmp/log-12 position: 1109060 logWriteOrderID: 1541522865621</span><br>
			18/11/07 00:51:13 INFO file.Log: Removing old file:/yangxiaohai /flumePath/dir/logdfstmp/log-8<br>
			18/11/07 00:51:13 INFO file.Log: Removing old file:/yangxiaohai/flumePath/dir/logdfstmp/log-8.meta<br>
			18/11/07 00:51:13 INFO file.Log: Removing old file: /yangxiaohai/flumePath/dir/logdfstmp/log-9<br>
			18/11/07 00:51:13 INFO file.Log: Removing old file: /yangxiaohai/flumePath/dir/logdfstmp/log-9.meta<br>
			18/11/07 00:51:13 INFO file.Log: Removing old file: /yangxiaohai/flumePath/dir/logdfstmp/log-10<br>
			18/11/07 00:51:13 INFO file.Log: Removing old file: /yangxiaohai/flumePath/dir/logdfstmp/log-10.meta</p>
			</td>
		</tr></tbody></table><p> </p>            </div>
                </div>