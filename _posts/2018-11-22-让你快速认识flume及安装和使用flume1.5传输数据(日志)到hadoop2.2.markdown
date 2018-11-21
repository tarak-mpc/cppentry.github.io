---
layout:     post
title:      让你快速认识flume及安装和使用flume1.5传输数据(日志)到hadoop2.2
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<table border="1" cellpadding="2" cellspacing="0" style="font-size:12px;border-collapse:collapse;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td style="border:1px solid rgb(153,153,153);min-height:25px;min-width:25px;background-color:inherit;font-size:14px;">
<span style="background-color:inherit;color:rgb(153,153,153);font-size:12px;">本帖最后由 pig2 于 2014-7-16 02:56 编辑</span><br style="background-color:inherit;"><span style="background-color:inherit;font-weight:700;">问题导读：<br style="background-color:inherit;"><span style="color:#ff0000;background-color:inherit;">1.什么是flume？</span></span><br style="background-color:inherit;"><span style="background-color:inherit;font-weight:700;"><span style="color:#ff0000;background-color:inherit;">2.如何安装flume？<br style="background-color:inherit;">
3.flume的配置文件与其它软件有什么不同？</span></span><br style="background-color:inherit;"><span style="background-color:inherit;font-weight:700;"><span style="font-size:18px;background-color:inherit;"><br style="background-color:inherit;"><br style="background-color:inherit;"><br style="background-color:inherit;"><br style="background-color:inherit;"><br style="background-color:inherit;">
一、认识flume</span></span><br style="background-color:inherit;"><span style="background-color:inherit;font-weight:700;">1.flume是什么？</span><br style="background-color:inherit;">
这里简单介绍一下，它是Cloudera的一个产品<br style="background-color:inherit;"><span style="background-color:inherit;font-weight:700;">2.flume是干什么的？</span><br style="background-color:inherit;">
收集日志的<br style="background-color:inherit;"><span style="background-color:inherit;font-weight:700;">3.flume如何搜集日志？</span><br style="background-color:inherit;"><span style="background-color:inherit;font-weight:700;"><span style="color:#ff0000;background-color:inherit;">我们把flume比作情报人员</span></span><br style="background-color:inherit;">
（1）搜集信息<br style="background-color:inherit;">
（2）获取记忆信息<br style="background-color:inherit;">
（3）传递报告间谍信息<br style="background-color:inherit;">
flume是怎么完成上面三件事情的，三个组件：<br style="background-color:inherit;">
source： 搜集信息<br style="background-color:inherit;">
channel：传递信息<br style="background-color:inherit;">
sink：存储信息<br style="background-color:inherit;">
上面有点简练，详细可以参考<a href="http://www.aboutyun.com/thread-7415-1-1.html" rel="nofollow" style="background-color:inherit;color:rgb(51,102,153);">Flume内置channel,source,sink三组件介绍</a><br style="background-color:inherit;">
上面我们认识了，flume。<br style="background-color:inherit;">
下面我们来安装flume1.5<br style="background-color:inherit;"><br style="background-color:inherit;"><span style="background-color:inherit;font-weight:700;"><span style="font-size:18px;background-color:inherit;"><br style="background-color:inherit;"></span></span><br style="background-color:inherit;"><span style="background-color:inherit;font-weight:700;"><span style="font-size:18px;background-color:inherit;">二、安装flume1.5</span></span><br style="background-color:inherit;"><br style="background-color:inherit;"><br style="background-color:inherit;"><span style="background-color:inherit;font-weight:700;">1.下载安装包</span><br style="background-color:inherit;">
（1）官网下载<br style="background-color:inherit;"><a href="http://www.apache.org/dyn/closer.cgi/flume/1.5.0/apache-flume-1.5.0-bin.tar.gz" rel="nofollow" style="background-color:inherit;color:rgb(51,102,153);">apache-flume-1.5.0-bin.tar.gz</a><br style="background-color:inherit;"><a href="http://www.apache.org/dyn/closer.cgi/flume/1.5.0/apache-flume-1.5.0-src.tar.gz" rel="nofollow" style="background-color:inherit;color:rgb(51,102,153);">apache-flume-1.5.0-src.tar.gz</a><br style="background-color:inherit;">
（2）百度网盘下载<br style="background-color:inherit;">
链接: <a href="http://pan.baidu.com/s/1dDip8RZ" rel="nofollow" style="background-color:inherit;color:rgb(51,102,153);">http://pan.baidu.com/s/1dDip8RZ</a> 密码: 268r<br style="background-color:inherit;"><br style="background-color:inherit;"><div style="min-width:2px;background-color:inherit;color:rgb(102,102,102);">
<blockquote style="background-color:inherit;">我们走到这一步，我们会想到一个问题，我的电脑是32位的，不知道能否安装？如果我的电脑是64位的，能否安装。之前我们装的hadoop就分为32位和64位，想到这个问题是正常的，但是这里不用担心，因为我们下载的是二进制包，也就是说你32位和64位都可以安装。</blockquote>
</div>
<br style="background-color:inherit;"><span style="background-color:inherit;font-weight:700;">2.分别解压：<br style="background-color:inherit;"></span>下载之后，我们看到下面两个包：<br style="background-color:inherit;"><span style="background-color:inherit;font-weight:700;">（1）上传Linux<br style="background-color:inherit;"></span> <br style="background-color:inherit;">
上面两个包，可以下载window，然后通过WinSCP,如果不会 <a href="http://www.aboutyun.com/thread-7663-1-1.html" rel="nofollow" style="background-color:inherit;color:rgb(51,102,153);">新手指导：使用 WinSCP（下载） 上文件到 Linux图文教程</a><br style="background-color:inherit;"><span style="background-color:inherit;font-weight:700;">（2）解压包</span><br style="background-color:inherit;"><br style="background-color:inherit;"><span style="background-color:inherit;font-weight:700;">解压apache-flume-1.5.0-bin.tar.gz，</span><span style="background-color:inherit;font-weight:700;">解压到usr文件夹下面</span><br style="background-color:inherit;"><div style="min-width:2px;background-color:inherit;color:rgb(102,102,102);">
<div style="background-color:inherit;">
<ol style="background-color:inherit;"><li style="background-color:inherit;font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;">
sudo tar zxvf apache-flume-1.5.0-bin.tar.gz</li></ol></div>
<span style="background-color:inherit;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
 <br style="background-color:inherit;"><br style="background-color:inherit;"><br style="background-color:inherit;"><span style="background-color:inherit;font-weight:700;">解压apache-flume-1.5.0-src.tar.gz，</span><span style="background-color:inherit;font-weight:700;">解压到usr文件夹下面</span><br style="background-color:inherit;"><div style="min-width:2px;background-color:inherit;color:rgb(102,102,102);">
<div style="background-color:inherit;">
<ol style="background-color:inherit;"><li style="background-color:inherit;font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;">
sudo tar zxvf apache-flume-1.5.0-src.tar.gz</li></ol></div>
<span style="background-color:inherit;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
 <br style="background-color:inherit;"><br style="background-color:inherit;"><span style="background-color:inherit;font-weight:700;">(3) src里面文件内容，覆盖解压后bin文件里面的内容<br style="background-color:inherit;"></span>
<div style="min-width:2px;background-color:inherit;color:rgb(102,102,102);">
<div style="background-color:inherit;">
<ol style="background-color:inherit;"><li style="background-color:inherit;font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;">
sudo cp -ri apache-flume-1.5.0-src/* apache-flume-1.5.0-bin<br style="background-color:inherit;"></li></ol></div>
<span style="background-color:inherit;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
 <br style="background-color:inherit;"><br style="background-color:inherit;"><span style="background-color:inherit;font-weight:700;">(4)重命名</span><br style="background-color:inherit;"><div style="min-width:2px;background-color:inherit;color:rgb(102,102,102);">
<div style="background-color:inherit;">
<ol style="background-color:inherit;"><li style="background-color:inherit;font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;">
mv apache-flume-1.5.0-bin/ flume</li></ol></div>
<span style="background-color:inherit;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
 <br style="background-color:inherit;"><br style="background-color:inherit;"><span style="background-color:inherit;font-weight:700;">3.配置环境变量：</span><br style="background-color:inherit;"> <br style="background-color:inherit;"><br style="background-color:inherit;">
配置环境变量生效<br style="background-color:inherit;"><div style="min-width:2px;background-color:inherit;color:rgb(102,102,102);">
<div style="background-color:inherit;">
<ol style="background-color:inherit;"><li style="background-color:inherit;font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;">
source /etc/environment</li></ol></div>
<span style="background-color:inherit;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="background-color:inherit;"><span style="background-color:inherit;font-weight:700;">3.建立配置文件</span><br style="background-color:inherit;">
这里面的配置文件还是比较特别的，不同于以往我们安装的软件，我们这里可以自己建立配置文件。<br style="background-color:inherit;">
首先我们建立一个 example文件
<div style="min-width:2px;background-color:inherit;color:rgb(102,102,102);">
<div style="background-color:inherit;">
<ol style="background-color:inherit;"><li style="background-color:inherit;font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;">
vi example</li></ol></div>
<span style="background-color:inherit;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="background-color:inherit;">
，然后把下面内容，粘帖到里面就可以了，注意不要有乱码，有乱码的话，可以直接创建一个文件，然后上传。方法也有很多，能解决就好。<br style="background-color:inherit;"><br style="background-color:inherit;">
对于下面红字部分，记得创建<a href="http://cpro.baidu.com/cpro/ui/uijs.php?rs=1&amp;u=http%3A%2F%2Fwww.aboutyun.com%2Fthread-7949-1-1.html&amp;p=baidu&amp;c=news&amp;n=10&amp;t=tpclicked3_hc&amp;q=92051019_cpr&amp;k=%CE%C4%BC%FE%BC%D0&amp;k0=%BC%E0%BF%D8&amp;kdi0=8&amp;k1=master&amp;kdi1=8&amp;k2=%CE%C4%BC%FE%BC%D0&amp;kdi2=8&amp;k3=shell&amp;kdi3=8&amp;k4=linux&amp;kdi4=8&amp;sid=bf59923c8ce7aa6a&amp;ch=0&amp;tu=u1692056&amp;jk=484aab98e27532b4&amp;cf=29&amp;fv=14&amp;stid=9&amp;urlid=0&amp;luki=3&amp;seller_id=1&amp;di=128" rel="nofollow" style="background-color:inherit;color:rgb(51,102,153);"><span style="color:rgb(0,0,255);background-color:inherit;">文件夹</span></a>，并且注意他们的权限一致，这个比较简单的，就不在书写了。对于下面的配置项，可以参考<a href="http://www.aboutyun.com/thread-7924-1-1.html" rel="nofollow" style="background-color:inherit;color:rgb(51,102,153);">flume参考文档</a>，这里面的参数很详细。<br style="background-color:inherit;"><br style="background-color:inherit;"><div style="min-width:2px;background-color:inherit;color:rgb(102,102,102);">
<blockquote style="background-color:inherit;">agent1表示代理名称<br style="background-color:inherit;">
agent1.sources=source1<br style="background-color:inherit;">
agent1.sinks=sink1<br style="background-color:inherit;">
agent1.channels=channel1<br style="background-color:inherit;"><br style="background-color:inherit;"><br style="background-color:inherit;">
#配置source1<br style="background-color:inherit;">
agent1.sources.source1.type=spooldir<br style="background-color:inherit;">
agent1.sources.source1.spoolDir=<span style="color:#ff0000;background-color:inherit;">/usr/aboutyunlog</span><br style="background-color:inherit;">
agent1.sources.source1.channels=channel1<br style="background-color:inherit;">
agent1.sources.source1.fileHeader = false<br style="background-color:inherit;"><br style="background-color:inherit;">
#配置sink1<br style="background-color:inherit;">
agent1.sinks.sink1.type=hdfs<br style="background-color:inherit;">
agent1.sinks.sink1.hdfs.path=<span style="color:#ff0000;background-color:inherit;">hdfs://<a href="http://cpro.baidu.com/cpro/ui/uijs.php?rs=1&amp;u=http%3A%2F%2Fwww.aboutyun.com%2Fthread-7949-1-1.html&amp;p=baidu&amp;c=news&amp;n=10&amp;t=tpclicked3_hc&amp;q=92051019_cpr&amp;k=master&amp;k0=%BC%E0%BF%D8&amp;kdi0=8&amp;k1=master&amp;kdi1=8&amp;k2=%CE%C4%BC%FE%BC%D0&amp;kdi2=8&amp;k3=shell&amp;kdi3=8&amp;k4=linux&amp;kdi4=8&amp;sid=bf59923c8ce7aa6a&amp;ch=0&amp;tu=u1692056&amp;jk=484aab98e27532b4&amp;cf=29&amp;fv=14&amp;stid=9&amp;urlid=0&amp;luki=2&amp;seller_id=1&amp;di=128" rel="nofollow" style="background-color:inherit;color:rgb(51,102,153);"><span style="color:rgb(0,0,255);background-color:inherit;">master</span></a>:8020/aboutyunlog</span><br style="background-color:inherit;">
agent1.sinks.sink1.hdfs.fileType=DataStream<br style="background-color:inherit;">
agent1.sinks.sink1.hdfs.writeFormat=TEXT<br style="background-color:inherit;">
agent1.sinks.sink1.hdfs.rollInterval=4<br style="background-color:inherit;">
agent1.sinks.sink1.channel=channel1<br style="background-color:inherit;"><br style="background-color:inherit;"><br style="background-color:inherit;">
#配置channel1<br style="background-color:inherit;">
agent1.channels.channel1.type=file<br style="background-color:inherit;">
agent1.channels.channel1.checkpointDir=<span style="color:#ff0000;background-color:inherit;">/usr/aboutyun_tmp123</span><br style="background-color:inherit;">
agent1.channels.channel1.dataDirs=<span style="color:#ff0000;background-color:inherit;">/usr/aboutyun_tmp</span></blockquote>
</div>
<br style="background-color:inherit;"> <br style="background-color:inherit;"><br style="background-color:inherit;"><br style="background-color:inherit;"><span style="background-color:inherit;font-weight:700;">4.启动flume</span><br style="background-color:inherit;"><div style="min-width:2px;background-color:inherit;color:rgb(102,102,102);">
<blockquote style="background-color:inherit;">flume-ng agent -n agent1 -c conf -f /<span style="color:#ff0000;background-color:inherit;">usr/flume/conf/example</span> <span style="color:#9acd32;background-color:inherit;">-Dflume.root.logger=DEBUG,console</span></blockquote>
</div>
<br style="background-color:inherit;">
上面注意红字部分，是我们自己建立的文件，而对于绿色部分，则是输出调试信息，也可以在配置文件中配置。<br style="background-color:inherit;"><br style="background-color:inherit;"><br style="background-color:inherit;"><span style="background-color:inherit;font-weight:700;">5.我们启动flume之后</span><br style="background-color:inherit;"><span style="background-color:inherit;font-weight:700;">会看到下面信息，并且信息不停的重复。这个其实是在</span><span style="background-color:inherit;font-weight:700;">空文件的时候，</span><span style="background-color:inherit;font-weight:700;">监控的信息输出。</span><br style="background-color:inherit;"> <br style="background-color:inherit;"><br style="background-color:inherit;"><br style="background-color:inherit;">
一旦有文件输入，我们会看到下面信息。<br style="background-color:inherit;"><div style="min-width:2px;background-color:inherit;color:rgb(102,102,102);">
<blockquote style="background-color:inherit;">注意：这个不要关闭，我们另外开启一个<a href="http://cpro.baidu.com/cpro/ui/uijs.php?rs=1&amp;u=http%3A%2F%2Fwww.aboutyun.com%2Fthread-7949-1-1.html&amp;p=baidu&amp;c=news&amp;n=10&amp;t=tpclicked3_hc&amp;q=92051019_cpr&amp;k=shell&amp;k0=%BC%E0%BF%D8&amp;kdi0=8&amp;k1=master&amp;kdi1=8&amp;k2=%CE%C4%BC%FE%BC%D0&amp;kdi2=8&amp;k3=shell&amp;kdi3=8&amp;k4=linux&amp;kdi4=8&amp;sid=bf59923c8ce7aa6a&amp;ch=0&amp;tu=u1692056&amp;jk=484aab98e27532b4&amp;cf=29&amp;fv=14&amp;stid=9&amp;urlid=0&amp;luki=4&amp;seller_id=1&amp;di=128" rel="nofollow" style="background-color:inherit;color:rgb(51,102,153);"><span style="color:rgb(0,0,255);background-color:inherit;">shell</span></a>，在<a href="http://cpro.baidu.com/cpro/ui/uijs.php?rs=1&amp;u=http%3A%2F%2Fwww.aboutyun.com%2Fthread-7949-1-1.html&amp;p=baidu&amp;c=news&amp;n=10&amp;t=tpclicked3_hc&amp;q=92051019_cpr&amp;k=%BC%E0%BF%D8&amp;k0=%BC%E0%BF%D8&amp;kdi0=8&amp;k1=master&amp;kdi1=8&amp;k2=%CE%C4%BC%FE%BC%D0&amp;kdi2=8&amp;k3=shell&amp;kdi3=8&amp;k4=linux&amp;kdi4=8&amp;sid=bf59923c8ce7aa6a&amp;ch=0&amp;tu=u1692056&amp;jk=484aab98e27532b4&amp;cf=29&amp;fv=14&amp;stid=9&amp;urlid=0&amp;luki=1&amp;seller_id=1&amp;di=128" rel="nofollow" style="background-color:inherit;color:rgb(51,102,153);"><span style="color:rgb(0,0,255);background-color:inherit;">监控</span></a>文件夹中放入要上传的文件</blockquote>
</div>
<br style="background-color:inherit;">
比如我们在监控文件夹下，创建一个test1文件，内容如下<br style="background-color:inherit;"><span style="background-color:inherit;font-weight:700;"> <br style="background-color:inherit;"></span><br style="background-color:inherit;"><br style="background-color:inherit;">
这时候flume监控shell，会有相应的如下下面变化<br style="background-color:inherit;"><div style="min-width:2px;background-color:inherit;color:rgb(102,102,102);">
<blockquote style="background-color:inherit;">2014-06-02 12:01:04,066 (pool-6-thread-1) [INFO - org.apache.flume.client.avro.ReliableSpoolingFileEventReader.rollCurrentFile(ReliableSpoolingFileEventReader.java:332)] Preparing to move file /usr/aboutyunlog/test1
 to /usr/aboutyunlog/test1.COMPLETED<br style="background-color:inherit;">
2014-06-02 12:01:04,070 (pool-6-thread-1) [ERROR - org.apache.flume.source.SpoolDirectorySource$SpoolDirectoryRunnable.run(SpoolDirectorySource.java:256)] FATAL: Spool Directory source source1: { spoolDir: /usr/aboutyunlog }: Uncaught exception in SpoolDirectorySource
 thread. Restart or reconfigure Flume to continue processing.<br style="background-color:inherit;">
java.lang.IllegalStateException: File name has been re-used with different files. Spooling assumptions violated for /usr/aboutyunlog/test1.COMPLETED<br style="background-color:inherit;">
at org.apache.flume.client.avro.ReliableSpoolingFileEventReader.rollCurrentFile(ReliableSpoolingFileEventReader.java:362)<br style="background-color:inherit;">
at org.apache.flume.client.avro.ReliableSpoolingFileEventReader.retireCurrentFile(ReliableSpoolingFileEventReader.java:314)<br style="background-color:inherit;">
at org.apache.flume.client.avro.ReliableSpoolingFileEventReader.readEvents(ReliableSpoolingFileEventReader.java:243)<br style="background-color:inherit;">
at org.apache.flume.source.SpoolDirectorySource$SpoolDirectoryRunnable.run(SpoolDirectorySource.java:227)<br style="background-color:inherit;">
at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)<br style="background-color:inherit;">
at java.util.concurrent.FutureTask.runAndReset(FutureTask.java:304)<br style="background-color:inherit;">
at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.access$301(ScheduledThreadPoolExecutor.java:178)<br style="background-color:inherit;">
at java.util.concurrent.ScheduledThreadPoolExecutor$ScheduledFutureTask.run(ScheduledThreadPoolExecutor.java:293)<br style="background-color:inherit;">
at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)<br style="background-color:inherit;">
at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)<br style="background-color:inherit;">
at java.lang.Thread.run(Thread.java:744)<br style="background-color:inherit;">
2014-06-02 12:01:07,749 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.hdfs.HDFSDataStream.configure(HDFSDataStream.java:58)] Serializer = TEXT, UseRawLocalFileSystem = false<br style="background-color:inherit;">
2014-06-02 12:01:07,803 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.hdfs.BucketWriter.open(BucketWriter.java:261)] Creating hdfs://<a href="http://cpro.baidu.com/cpro/ui/uijs.php?rs=1&amp;u=http%3A%2F%2Fwww.aboutyun.com%2Fthread-7949-1-1.html&amp;p=baidu&amp;c=news&amp;n=10&amp;t=tpclicked3_hc&amp;q=92051019_cpr&amp;k=master&amp;k0=%BC%E0%BF%D8&amp;kdi0=8&amp;k1=master&amp;kdi1=8&amp;k2=%CE%C4%BC%FE%BC%D0&amp;kdi2=8&amp;k3=shell&amp;kdi3=8&amp;k4=linux&amp;kdi4=8&amp;sid=bf59923c8ce7aa6a&amp;ch=0&amp;tu=u1692056&amp;jk=484aab98e27532b4&amp;cf=29&amp;fv=14&amp;stid=9&amp;urlid=0&amp;luki=2&amp;seller_id=1&amp;di=128" rel="nofollow" style="background-color:inherit;color:rgb(51,102,153);"><span style="color:rgb(0,0,255);background-color:inherit;">master</span></a>:8020/aboutyunlog/FlumeData.1401681667750.tmp<br style="background-color:inherit;">
2014-06-02 12:01:07,871 (hdfs-sink1-call-runner-2) [DEBUG - org.apache.flume.sink.hdfs.AbstractHDFSWriter.reflectGetNumCurrentReplicas(AbstractHDFSWriter.java:195)] Using getNumCurrentReplicas--HDFS-826<br style="background-color:inherit;">
2014-06-02 12:01:07,871 (hdfs-sink1-call-runner-2) [DEBUG - org.apache.flume.sink.hdfs.AbstractHDFSWriter.reflectGetDefaultReplication(AbstractHDFSWriter.java:223)] Using FileSystem.getDefaultReplication(Path) from HADOOP-8014<br style="background-color:inherit;">
2014-06-02 12:01:10,945 (Log-BackgroundWorker-channel1) [INFO - org.apache.flume.channel.file.EventQueueBackingStoreFile.beginCheckpoint(EventQueueBackingStoreFile.java:214)] Start checkpoint for /usr/aboutyun_tmp123/checkpoint, elements to sync = 3<br style="background-color:inherit;">
2014-06-02 12:01:10,949 (Log-BackgroundWorker-channel1) [INFO - org.apache.flume.channel.file.EventQueueBackingStoreFile.checkpoint(EventQueueBackingStoreFile.java:239)] Updating checkpoint metadata: logWriteOrderID: 1401681430998, queueSize: 0, queueHead:
 11<br style="background-color:inherit;">
2014-06-02 12:01:10,952 (Log-BackgroundWorker-channel1) [INFO - org.apache.flume.channel.file.Log.writeCheckpoint(Log.java:1005)] Updated checkpoint for file: /usr/aboutyun_tmp/log-8 position: 2482 logWriteOrderID: 1401681430998<br style="background-color:inherit;">
2014-06-02 12:01:10,953 (Log-BackgroundWorker-channel1) [DEBUG - org.apache.flume.channel.file.Log.removeOldLogs(Log.java:1067)] Files currently in use: [8]<br style="background-color:inherit;">
2014-06-02 12:01:11,872 (hdfs-sink1-roll-timer-0) [DEBUG - org.apache.flume.sink.hdfs.BucketWriter$2.call(BucketWriter.java:303)] Rolling file (hdfs://master:8020/aboutyunlog/FlumeData.1401681667750.tmp): Roll scheduled after 4 sec elapsed.<br style="background-color:inherit;">
2014-06-02 12:01:11,873 (hdfs-sink1-roll-timer-0) [INFO - org.apache.flume.sink.hdfs.BucketWriter.close(BucketWriter.java:409)] Closing hdfs://<a href="http://cpro.baidu.com/cpro/ui/uijs.php?rs=1&amp;u=http%3A%2F%2Fwww.aboutyun.com%2Fthread-7949-1-1.html&amp;p=baidu&amp;c=news&amp;n=10&amp;t=tpclicked3_hc&amp;q=92051019_cpr&amp;k=master&amp;k0=%BC%E0%BF%D8&amp;kdi0=8&amp;k1=master&amp;kdi1=8&amp;k2=%CE%C4%BC%FE%BC%D0&amp;kdi2=8&amp;k3=shell&amp;kdi3=8&amp;k4=linux&amp;kdi4=8&amp;sid=bf59923c8ce7aa6a&amp;ch=0&amp;tu=u1692056&amp;jk=484aab98e27532b4&amp;cf=29&amp;fv=14&amp;stid=9&amp;urlid=0&amp;luki=2&amp;seller_id=1&amp;di=128" rel="nofollow" style="background-color:inherit;color:rgb(51,102,153);"><span style="color:rgb(0,0,255);background-color:inherit;">master</span></a>:8020/aboutyunlog/FlumeData.1401681667750.tmp<br style="background-color:inherit;">
2014-06-02 12:01:11,873 (hdfs-sink1-call-runner-7) [INFO - org.apache.flume.sink.hdfs.BucketWriter$3.call(BucketWriter.java:339)] Close tries incremented<br style="background-color:inherit;">
2014-06-02 12:01:11,895 (hdfs-sink1-call-runner-8) [INFO - org.apache.flume.sink.hdfs.BucketWriter$8.call(BucketWriter.java:669)] Renaming hdfs://<a href="http://cpro.baidu.com/cpro/ui/uijs.php?rs=1&amp;u=http%3A%2F%2Fwww.aboutyun.com%2Fthread-7949-1-1.html&amp;p=baidu&amp;c=news&amp;n=10&amp;t=tpclicked3_hc&amp;q=92051019_cpr&amp;k=master&amp;k0=%BC%E0%BF%D8&amp;kdi0=8&amp;k1=master&amp;kdi1=8&amp;k2=%CE%C4%BC%FE%BC%D0&amp;kdi2=8&amp;k3=shell&amp;kdi3=8&amp;k4=linux&amp;kdi4=8&amp;sid=bf59923c8ce7aa6a&amp;ch=0&amp;tu=u1692056&amp;jk=484aab98e27532b4&amp;cf=29&amp;fv=14&amp;stid=9&amp;urlid=0&amp;luki=2&amp;seller_id=1&amp;di=128" rel="nofollow" style="background-color:inherit;color:rgb(51,102,153);"><span style="color:rgb(0,0,255);background-color:inherit;">master</span></a>:8020/aboutyunlog/FlumeData.1401681667750.tmp
 to hdfs://master:8020/aboutyunlog/FlumeData.1401681667750<br style="background-color:inherit;">
2014-06-02 12:01:11,897 (hdfs-sink1-roll-timer-0) [INFO - org.apache.flume.sink.hdfs.HDFSEventSink$1.run(HDFSEventSink.java:402)] Writer callback called.<br style="background-color:inherit;">
2014-06-02 12:01:12,423 (conf-file-poller-0) [DEBUG - org.apache.flume.node.PollingPropertiesFileConfigurationProvider$FileWatcherRunnable.run(PollingPropertiesFileConfigurationProvider.java:126)] Checking file:conf/example for changes<br style="background-color:inherit;">
2014-06-02 12:01:40,953 (Log-BackgroundWorker-channel1) [DEBUG - org.apache.flume.channel.file.FlumeEventQueue.checkpoint(FlumeEventQueue.java:137)] Checkpoint not required</blockquote>
</div>
<br style="background-color:inherit;">
上传成功之后，我们去hdfs上，查看上传文件：<br style="background-color:inherit;"> <br style="background-color:inherit;"><br style="background-color:inherit;"><br style="background-color:inherit;">
这样我们做到了flume上传到hadoop2.2。<br style="background-color:inherit;"><br style="background-color:inherit;">
完毕</td>
</tr></tbody></table>            </div>
                </div>