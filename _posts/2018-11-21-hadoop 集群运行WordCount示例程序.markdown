---
layout:     post
title:      hadoop 集群运行WordCount示例程序
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>在博客中<a href="http://blog.csdn.net/stormragewang/article/details/41148917" rel="nofollow">http://blog.csdn.net/stormragewang/article/details/41148917</a>写到运行wordcount程序</p>
<p></p><pre><code class="language-plain">[hadoop@Yarn-Master ~]$ hadoop/2.6.1/bin/hadoop jar hadoop/2.6.1/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.6.1.jar wordcount /tmp/wordcount/word.txt /tmp/wordcount/output
15/12/19 16:01:48 INFO Configuration.deprecation: session.id is deprecated. Instead, use dfs.metrics.session-id
15/12/19 16:01:48 INFO jvm.JvmMetrics: Initializing JVM Metrics with processName=JobTracker, sessionId=
15/12/19 16:01:48 INFO input.FileInputFormat: Total input paths to process : 1
15/12/19 16:01:48 INFO mapreduce.JobSubmitter: number of splits:1
15/12/19 16:01:49 INFO mapreduce.JobSubmitter: Submitting tokens for job: job_local1612809929_0001
15/12/19 16:01:49 INFO mapreduce.Job: The url to track the job: http://localhost:8080/
15/12/19 16:01:49 INFO mapreduce.Job: Running job: job_local1612809929_0001
...
15/12/19 16:01:52 INFO mapred.LocalJobRunner: reduce &gt; reduce
15/12/19 16:01:52 INFO mapred.Task: Task 'attempt_local1612809929_0001_r_000000_0' done.
15/12/19 16:01:52 INFO mapred.LocalJobRunner: Finishing task: attempt_local1612809929_0001_r_000000_0
15/12/19 16:01:52 INFO mapred.LocalJobRunner: reduce task executor complete.
15/12/19 16:01:52 INFO mapreduce.Job:  map 100% reduce 100%
15/12/19 16:01:52 INFO mapreduce.Job: Job job_local1612809929_0001 completed successfully
15/12/19 16:01:52 INFO mapreduce.Job: Counters: 38
	File System Counters
		FILE: Number of bytes read=541332
		FILE: Number of bytes written=1060707
		FILE: Number of read operations=0
		FILE: Number of large read operations=0
		FILE: Number of write operations=0
		HDFS: Number of bytes read=166
		HDFS: Number of bytes written=81
		HDFS: Number of read operations=13
		HDFS: Number of large read operations=0
		HDFS: Number of write operations=4
	Map-Reduce Framework
		Map input records=1
		Map output records=13
		Map output bytes=136
		Map output materialized bytes=123
		Input split bytes=101
		Combine input records=13
		Combine output records=9
		Reduce input groups=9
		Reduce shuffle bytes=123
		Reduce input records=9
		Reduce output records=9
		Spilled Records=18
		Shuffled Maps =1
		Failed Shuffles=0
		Merged Map outputs=1
		GC time elapsed (ms)=77
		CPU time spent (ms)=0
		Physical memory (bytes) snapshot=0
		Virtual memory (bytes) snapshot=0
		Total committed heap usage (bytes)=241836032
	Shuffle Errors
		BAD_ID=0
		CONNECTION=0
		IO_ERROR=0
		WRONG_LENGTH=0
		WRONG_MAP=0
		WRONG_REDUCE=0
	File Input Format Counters 
		Bytes Read=83
	File Output Format Counters 
		Bytes Written=81
[hadoop@Yarn-Master ~]$</code></pre>网上很多搭建集群的例子也到这里就完了，当时觉得运行没什么问题，后来发现运行始终是在本地运行的，并没有提交到集群中，在Yarn的8088端口控制台也看不到提交的任务信息。注意看log信息，就会发现是以本地方式运行的。<br><img src="https://img-blog.csdn.net/20151219163624928" alt=""><br><p>根据官方文档<a href="https://hadoop.apache.org/docs/r2.6.1/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml" rel="nofollow">https://hadoop.apache.org/docs/r2.6.1/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml</a>中的描述</p>
<p><img src="https://img-blog.csdn.net/20151219163851992" alt=""><br></p>
<p>发现是maped-default.xml文件中的mapreduce.framework.name属性起作用，将ResoureManager机器上的maped-default.xml改成如下配置</p>
<p></p><pre><code class="language-plain">&lt;?xml version="1.0"?&gt;
&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;
&lt;!--
  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License. See accompanying LICENSE file.
--&gt;

&lt;!-- Put site-specific property overrides in this file. --&gt;

&lt;configuration&gt;
&lt;property&gt;
	&lt;name&gt;mapreduce.framework.name&lt;/name&gt;
	&lt;value&gt;yarn&lt;/value&gt;
	&lt;description&gt;The runtime framework for executing MapReduce jobs. Can be one of local, classic or yarn.&lt;/description&gt;
&lt;/property&gt;
&lt;/configuration&gt;</code></pre>然后重启就会发现该任务提交到集群中了。
<p><img src="https://img-blog.csdn.net/20151219164220159" alt=""><br></p>
<p><br></p>
<p><br></p>
<p>在运行过程中可能会出现以下问题：</p>
<p></p><pre><code class="language-plain">15/12/18 16:45:25 INFO mapreduce.Job: Task Id : attempt_1450426128352_0003_m_000000_2, Status : FAILED
Container launch failed for container_1450426128352_0003_01_000004 : java.lang.IllegalArgumentException: Does not contain a valid host:port authority: Yarn-Slave_2:56415
	at org.apache.hadoop.net.NetUtils.createSocketAddr(NetUtils.java:212)
	at org.apache.hadoop.net.NetUtils.createSocketAddr(NetUtils.java:164)
	at org.apache.hadoop.net.NetUtils.createSocketAddr(NetUtils.java:153)
	at org.apache.hadoop.yarn.client.api.impl.ContainerManagementProtocolProxy$ContainerManagementProtocolProxyData.newProxy(ContainerManagementProtocolProxy.java:258)
	at org.apache.hadoop.yarn.client.api.impl.ContainerManagementProtocolProxy$ContainerManagementProtocolProxyData.&lt;init&gt;(ContainerManagementProtocolProxy.java:244)
	at org.apache.hadoop.yarn.client.api.impl.ContainerManagementProtocolProxy.getProxy(ContainerManagementProtocolProxy.java:129)
	at org.apache.hadoop.mapreduce.v2.app.launcher.ContainerLauncherImpl.getCMProxy(ContainerLauncherImpl.java:403)
	at org.apache.hadoop.mapreduce.v2.app.launcher.ContainerLauncherImpl$Container.launch(ContainerLauncherImpl.java:138)
	at org.apache.hadoop.mapreduce.v2.app.launcher.ContainerLauncherImpl$EventProcessor.run(ContainerLauncherImpl.java:369)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
	at java.lang.Thread.run(Thread.java:745)</code></pre>通过跟踪源码，发现这是因为主机名[Yarn-Slave_2]起的不符合主机的命名规则，主机名中不能有下划线。
<p><img src="https://img-blog.csdn.net/20151219165242368" alt=""><br></p>
<p>修改/etc/sysconfig/network和/etc/hosts以修改主机名即可。<br><br></p>
<p>2、类似<a href="http://gethue.com/hadoop-yarn-11-local-dirs-are-bad-varlibhadoop-yarncacheyarnnm-local-dir-11-log-dirs-are-bad-varloghadoop-yarncontainers/" rel="nofollow">http://gethue.com/hadoop-yarn-11-local-dirs-are-bad-varlibhadoop-yarncacheyarnnm-local-dir-11-log-dirs-are-bad-varloghadoop-yarncontainers/</a>中某个nodemanager报unhealth。</p>
<p></p><pre><code class="language-plain">yarn.server.nodemanager.DirectoryCollection: Directory /var/lib/hadoop-yarn/cache/yarn/nm-local-dir error, used space above threshold of 90.0%, removing from list of valid directories
2014-11-17 17:45:00,713 WARN org.apache.hadoop.yarn.server.nodemanager.DirectoryCollection: Directory /var/log/hadoop-yarn/containers error, used space above threshold of 90.0%, removing from list of valid directories
2014-11-17 17:45:00,713 INFO org.apache.hadoop.yarn.server.nodemanager.LocalDirsHandlerService: Disk(s) failed: 1/1 local-dirs are bad: /var/lib/hadoop-yarn/cache/yarn/nm-local-dir; 1/1 log-dirs are bad: /var/log/hadoop</code></pre>很多人说是因为core-site.xml中hadoop.tmp.dir中配置的问题，但我发现我的配置是对的，该 属性对应的文件夹是存在并且hadoop用户是有权限的，后来发现只是因为硬盘在没有足够的空间而已，删除一些不用的文件并重启yarn即可。<br><img src="https://img-blog.csdn.net/20151219170108728" alt=""><br><br>            </div>
                </div>