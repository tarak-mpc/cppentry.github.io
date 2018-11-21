---
layout:     post
title:      flume报错 Configured capacity is 100000000 but the  checkpoint file capacity is 1000000
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><strong><span style="font-size:14px;">最近flume运行不是很稳定，本次由于hadoop不能写入，导致flume报错，Configured capacity is 100000000 but the checkpoint file capacity is 1000000，重启flume后问题仍然存在。</span></strong></p>
<p><strong><span style="font-size:14px;">1，详细报错如下：</span></strong><br></p>
<p>22 Jan 2013 11:07:42,568 INFO [pool-7-thread-1] (org.apache.avro.ipc.NettyServer$NettyServerAvroHandler.channelClosed:209) - Connection to /10.4.203.176:60322 disconnected.</p>
22 Jan 2013 11:07:44,617 ERROR [SinkRunner-PollingRunner-DefaultSinkProcessor] (org.apache.flume.SinkRunner$PollingRunner.run:160) - Unable to deliver event. Exception follows.<br>
java.lang.IllegalStateException: Channel closed [channel=file_chan_1]. Due to java.lang.IllegalStateException: Configured capacity is 100000000 but the checkpoint file capacity is 1000000. See FileChannel documentation on how to change a channels capacity.<br>
 at org.apache.flume.channel.file.FileChannel.createTransaction(FileChannel.java:321)<br>
 at org.apache.flume.channel.BasicChannelSemantics.getTransaction(BasicChannelSemantics.java:122)<br>
 at org.apache.flume.sink.hdfs.HDFSEventSink.process(HDFSEventSink.java:385)<br>
 at org.apache.flume.sink.DefaultSinkProcessor.process(DefaultSinkProcessor.java:68)<br>
 at org.apache.flume.SinkRunner$PollingRunner.run(SinkRunner.java:147)<br>
 at java.lang.Thread.run(Thread.java:722)<br>
Caused by: java.lang.IllegalStateException: Configured capacity is 100000000 but the checkpoint file capacity is 1000000. See FileChannel documentation on how to change a channels capacity.<br>
 at org.apache.flume.channel.file.EventQueueBackingStoreFile.&lt;init&gt;(EventQueueBackingStoreFile.java:80)<br>
 at org.apache.flume.channel.file.EventQueueBackingStoreFileV3.&lt;init&gt;(EventQueueBackingStoreFileV3.java:42)<br>
 at org.apache.flume.channel.file.EventQueueBackingStoreFactory.get(EventQueueBackingStoreFactory.java:67)<br>
 at org.apache.flume.channel.file.EventQueueBackingStoreFactory.get(EventQueueBackingStoreFactory.java:36)<br>
 at org.apache.flume.channel.file.Log.replay(Log.java:339)<br>
 at org.apache.flume.channel.file.FileChannel.start(FileChannel.java:271)<br>
 at org.apache.flume.lifecycle.LifecycleSupervisor$MonitorRunnable.run(LifecycleSupervisor.java:236)<br>
 at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)<br>
 at java.util.concurrent.FutureTask$Sync.innerRunAndReset(FutureTask.java:351)<br>
 at java.util.concurrent.FutureTask.runAndReset(FutureTask.java:178)<br>
 at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.access$301(ScheduledThreadPoolExecutor.java:178)<br>
 at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.run(ScheduledThreadPoolExecutor.java:293)<br>
 at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1110)<br>
 at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:603)<br>
 ... 1 more<br><br><strong><span style="font-size:14px;">2，故障原因：</span><br>
 FileChannel使用了固定大小的checkpoint file，修改channel的能力，简单的方法如下<br>
 1，关闭agent <br>
 2, 删除或者备份checkpoint目录<br>
 3，重启flume agent（如果channel中有大量文件，会导致全面的延迟，建议先关闭数据源，等把file channel中的数据全部写入sink后，等上2分钟，等数据文件删除后，再重启channel<br>
 具体详见参考资料这段话，这段意思未完全读明白</strong>，通过解决方法靠谱<br><br>
The FileChannel actually uses a fixed size checkpoint file -- so it is not possible to set<br>
it to unlimited size (the checkpoint file is mmap-ed to a fixed size buffer). To change the<br>
capacity of the channel, the easiest way off the top of my head is:<br><br>
* Shutdown the agent.<br>
* Delete all files in the file channel's checkpoint directory. (not the data directories.<br>
Also you might want to move them out, rather than delete to be safe)<br>
* Change your configuration to increase the capacity of the channel.<br>
* Restart the agent - this will cause full replay, so the agent might take sometime to start<br>
up if there are a lot of events in the channel (to avoid this - shutdown the source before<br>
shutting the agent down - so the sink can drain out the channel completely, wait for about<br>
1-2 mins after the channel is empty so that the data files get deleted (this happens only<br>
immediately after a checkpoint - you can verify this by making sure each data dir has only<br>
2 files each), since all events have been sent out - so during restart the channel will be<br>
quite empty, with very little to replay).<br><br><br><strong><span style="font-size:12px;">参考资料</span></strong><br>
http://mail-archives.apache.org/mod_mbox/flume-user/201211.mbox/%3CDA66E7657BFBD949829F3C98D73A54CF18E23C@RHV-EXRDA-S11.corp.ebay.com%3E            </div>
                </div>