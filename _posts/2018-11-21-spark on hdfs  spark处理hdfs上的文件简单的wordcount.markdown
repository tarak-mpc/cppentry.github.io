---
layout:     post
title:      spark on hdfs  spark处理hdfs上的文件简单的wordcount
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>进入spark/bin 目录 输入spark-shell  进入spark shell模式</p>
<p>[hadoop@localhost bin]$ spark-shell<br>
14/05/21 14:04:59 INFO spark.HttpServer: Starting HTTP Server<br>
14/05/21 14:04:59 INFO server.Server: jetty-7.x.y-SNAPSHOT<br>
14/05/21 14:04:59 INFO server.AbstractConnector: Started SocketConnector@0.0.0.0:40562<br>
Welcome to<br>
      ____              __<br>
     / __/__  ___ _____/ /__<br>
    _\ \/ _ \/ _ `/ __/  '_/<br>
   /___/ .__/\_,_/_/ /_/\_\   version 0.9.1<br>
      /_/<br><br>
Using Scala version 2.10.3 (Java HotSpot(TM) 64-Bit Server VM, Java 1.7.0_55)<br>
Type in expressions to have them evaluated.<br>
Type :help for more information.<br>
14/05/21 14:05:05 INFO slf4j.Slf4jLogger: Slf4jLogger started<br>
14/05/21 14:05:05 INFO Remoting: Starting remoting<br>
14/05/21 14:05:05 INFO Remoting: Remoting started; listening on addresses :[akka.tcp://spark@localhost:57902]<br>
14/05/21 14:05:05 INFO Remoting: Remoting now listens on addresses: [akka.tcp://spark@localhost:57902]<br>
14/05/21 14:05:05 INFO spark.SparkEnv: Registering BlockManagerMaster<br>
14/05/21 14:05:05 INFO storage.DiskBlockManager: Created local directory at /tmp/spark-local-20140521140505-3f53<br>
14/05/21 14:05:05 INFO storage.MemoryStore: MemoryStore started with capacity 294.9 MB.<br>
14/05/21 14:05:06 INFO network.ConnectionManager: Bound socket to port 48084 with id = ConnectionManagerId(localhost,48084)<br>
14/05/21 14:05:06 INFO storage.BlockManagerMaster: Trying to register BlockManager<br>
14/05/21 14:05:06 INFO storage.BlockManagerMasterActor$BlockManagerInfo: Registering block manager localhost:48084 with 294.9 MB RAM<br>
14/05/21 14:05:06 INFO storage.BlockManagerMaster: Registered BlockManager<br>
14/05/21 14:05:06 INFO spark.HttpServer: Starting HTTP Server<br>
14/05/21 14:05:06 INFO server.Server: jetty-7.x.y-SNAPSHOT<br>
14/05/21 14:05:06 INFO server.AbstractConnector: Started SocketConnector@0.0.0.0:48508<br>
14/05/21 14:05:06 INFO broadcast.HttpBroadcast: Broadcast server started at http://localhost:48508<br>
14/05/21 14:05:06 INFO spark.SparkEnv: Registering MapOutputTracker<br>
14/05/21 14:05:06 INFO spark.HttpFileServer: HTTP File server directory is /tmp/spark-862d3bfc-8485-4adb-86a3-efd09c90fc03<br>
14/05/21 14:05:06 INFO spark.HttpServer: Starting HTTP Server<br>
14/05/21 14:05:06 INFO server.Server: jetty-7.x.y-SNAPSHOT<br>
14/05/21 14:05:06 INFO server.AbstractConnector: Started SocketConnector@0.0.0.0:38729<br>
14/05/21 14:05:06 INFO server.Server: jetty-7.x.y-SNAPSHOT<br>
14/05/21 14:05:06 INFO handler.ContextHandler: started o.e.j.s.h.ContextHandler{/storage/rdd,null}<br>
14/05/21 14:05:06 INFO handler.ContextHandler: started o.e.j.s.h.ContextHandler{/storage,null}<br>
14/05/21 14:05:06 INFO handler.ContextHandler: started o.e.j.s.h.ContextHandler{/stages/stage,null}<br>
14/05/21 14:05:06 INFO handler.ContextHandler: started o.e.j.s.h.ContextHandler{/stages/pool,null}<br>
14/05/21 14:05:06 INFO handler.ContextHandler: started o.e.j.s.h.ContextHandler{/stages,null}<br>
14/05/21 14:05:06 INFO handler.ContextHandler: started o.e.j.s.h.ContextHandler{/environment,null}<br>
14/05/21 14:05:06 INFO handler.ContextHandler: started o.e.j.s.h.ContextHandler{/executors,null}<br>
14/05/21 14:05:06 INFO handler.ContextHandler: started o.e.j.s.h.ContextHandler{/metrics/json,null}<br>
14/05/21 14:05:06 INFO handler.ContextHandler: started o.e.j.s.h.ContextHandler{/static,null}<br>
14/05/21 14:05:06 INFO handler.ContextHandler: started o.e.j.s.h.ContextHandler{/,null}<br>
14/05/21 14:05:06 INFO server.AbstractConnector: Started SelectChannelConnector@0.0.0.0:4040<br>
14/05/21 14:05:06 INFO ui.SparkUI: Started Spark Web UI at http://localhost:4040<br>
14/05/21 14:05:06 INFO executor.Executor: Using REPL class URI: http://localhost:40562<br>
14/05/21 14:05:07 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable<br>
Created spark context..<br>
Spark context available as sc.<br>
scala&gt;<span style="color:#FF0000;"> val file = sc.textFile("hdfs://localhost:9000/input")</span><br>
14/05/21 14:07:11 INFO storage.MemoryStore: ensureFreeSpace(140074) called with curMem=0, maxMem=309225062<br>
14/05/21 14:07:11 INFO storage.MemoryStore: Block broadcast_0 stored as values to memory (estimated size 136.8 KB, free 294.8 MB)<br>
file: org.apache.spark.rdd.RDD[String] = MappedRDD[1] at textFile at &lt;console&gt;:12<br><br>
scala&gt; <span style="color:#FF0000;"> val count = file.flatMap(line =&gt; line.split(" ")).map(word =&gt; (word, 1)).reduceByKey(_+_)</span><br>
14/05/21 14:07:30 INFO mapred.FileInputFormat: Total input paths to process : 2<br>
count: org.apache.spark.rdd.RDD[(String, Int)] = MapPartitionsRDD[6] at reduceByKey at &lt;console&gt;:14<br><br>
scala&gt; <span style="color:#FF0000;">count.collect()</span><br>
14/05/21 14:07:48 INFO spark.SparkContext: Starting job: collect at &lt;console&gt;:17<br>
14/05/21 14:07:48 INFO scheduler.DAGScheduler: Registering RDD 4 (reduceByKey at &lt;console&gt;:14)<br>
14/05/21 14:07:48 INFO scheduler.DAGScheduler: Got job 0 (collect at &lt;console&gt;:17) with 2 output partitions (allowLocal=false)<br>
14/05/21 14:07:48 INFO scheduler.DAGScheduler: Final stage: Stage 0 (collect at &lt;console&gt;:17)<br>
14/05/21 14:07:48 INFO scheduler.DAGScheduler: Parents of final stage: List(Stage 1)<br>
14/05/21 14:07:48 INFO scheduler.DAGScheduler: Missing parents: List(Stage 1)<br>
14/05/21 14:07:48 INFO scheduler.DAGScheduler: Submitting Stage 1 (MapPartitionsRDD[4] at reduceByKey at &lt;console&gt;:14), which has no missing parents<br>
14/05/21 14:07:48 INFO scheduler.DAGScheduler: Submitting 2 missing tasks from Stage 1 (MapPartitionsRDD[4] at reduceByKey at &lt;console&gt;:14)<br>
14/05/21 14:07:48 INFO scheduler.TaskSchedulerImpl: Adding task set 1.0 with 2 tasks<br>
14/05/21 14:07:48 INFO scheduler.TaskSetManager: Starting task 1.0:0 as TID 0 on executor localhost: localhost (PROCESS_LOCAL)<br>
14/05/21 14:07:48 INFO scheduler.TaskSetManager: Serialized task 1.0:0 as 1909 bytes in 7 ms<br>
14/05/21 14:07:48 INFO executor.Executor: Running task ID 0<br>
14/05/21 14:07:48 INFO storage.BlockManager: Found block broadcast_0 locally<br>
14/05/21 14:07:48 INFO rdd.HadoopRDD: Input split: hdfs://localhost:9000/input/test.txt:0+44<br>
14/05/21 14:07:49 INFO executor.Executor: Serialized size of result for 0 is 746<br>
14/05/21 14:07:49 INFO executor.Executor: Sending result for 0 directly to driver<br>
14/05/21 14:07:49 INFO executor.Executor: Finished task ID 0<br>
14/05/21 14:07:49 INFO scheduler.TaskSetManager: Starting task 1.0:1 as TID 1 on executor localhost: localhost (PROCESS_LOCAL)<br>
14/05/21 14:07:49 INFO scheduler.TaskSetManager: Serialized task 1.0:1 as 1910 bytes in 0 ms<br>
14/05/21 14:07:49 INFO executor.Executor: Running task ID 1<br>
14/05/21 14:07:49 INFO scheduler.TaskSetManager: Finished TID 0 in 866 ms on localhost (progress: 1/2)<br>
14/05/21 14:07:49 INFO storage.BlockManager: Found block broadcast_0 locally<br>
14/05/21 14:07:49 INFO scheduler.DAGScheduler: Completed ShuffleMapTask(1, 0)<br>
14/05/21 14:07:49 INFO rdd.HadoopRDD: Input split: hdfs://localhost:9000/input/test.txt1:0+41<br>
14/05/21 14:07:49 INFO executor.Executor: Serialized size of result for 1 is 746<br>
14/05/21 14:07:49 INFO executor.Executor: Sending result for 1 directly to driver<br>
14/05/21 14:07:49 INFO executor.Executor: Finished task ID 1<br>
14/05/21 14:07:49 INFO scheduler.DAGScheduler: Completed ShuffleMapTask(1, 1)<br>
14/05/21 14:07:49 INFO scheduler.TaskSetManager: Finished TID 1 in 62 ms on localhost (progress: 2/2)<br>
14/05/21 14:07:49 INFO scheduler.DAGScheduler: Stage 1 (reduceByKey at &lt;console&gt;:14) finished in 0.931 s<br>
14/05/21 14:07:49 INFO scheduler.TaskSchedulerImpl: Removed TaskSet 1.0, whose tasks have all completed, from pool
<br>
14/05/21 14:07:49 INFO scheduler.DAGScheduler: looking for newly runnable stages<br>
14/05/21 14:07:49 INFO scheduler.DAGScheduler: running: Set()<br>
14/05/21 14:07:49 INFO scheduler.DAGScheduler: waiting: Set(Stage 0)<br>
14/05/21 14:07:49 INFO scheduler.DAGScheduler: failed: Set()<br>
14/05/21 14:07:49 INFO scheduler.DAGScheduler: Missing parents for Stage 0: List()<br>
14/05/21 14:07:49 INFO scheduler.DAGScheduler: Submitting Stage 0 (MapPartitionsRDD[6] at reduceByKey at &lt;console&gt;:14), which is now runnable<br>
14/05/21 14:07:49 INFO scheduler.DAGScheduler: Submitting 2 missing tasks from Stage 0 (MapPartitionsRDD[6] at reduceByKey at &lt;console&gt;:14)<br>
14/05/21 14:07:49 INFO scheduler.TaskSchedulerImpl: Adding task set 0.0 with 2 tasks<br>
14/05/21 14:07:49 INFO scheduler.TaskSetManager: Starting task 0.0:0 as TID 2 on executor localhost: localhost (PROCESS_LOCAL)<br>
14/05/21 14:07:49 INFO scheduler.TaskSetManager: Serialized task 0.0:0 as 1779 bytes in 0 ms<br>
14/05/21 14:07:49 INFO executor.Executor: Running task ID 2<br>
14/05/21 14:07:49 INFO storage.BlockManager: Found block broadcast_0 locally<br>
14/05/21 14:07:49 INFO storage.BlockFetcherIterator$BasicBlockFetcherIterator: Getting 2 non-zero-bytes blocks out of 2 blocks<br>
14/05/21 14:07:49 INFO storage.BlockFetcherIterator$BasicBlockFetcherIterator: Started 0 remote gets in  3 ms<br>
14/05/21 14:07:49 INFO executor.Executor: Serialized size of result for 2 is 1095<br>
14/05/21 14:07:49 INFO executor.Executor: Sending result for 2 directly to driver<br>
14/05/21 14:07:49 INFO executor.Executor: Finished task ID 2<br>
14/05/21 14:07:49 INFO scheduler.TaskSetManager: Starting task 0.0:1 as TID 3 on executor localhost: localhost (PROCESS_LOCAL)<br>
14/05/21 14:07:49 INFO scheduler.TaskSetManager: Serialized task 0.0:1 as 1779 bytes in 0 ms<br>
14/05/21 14:07:49 INFO executor.Executor: Running task ID 3<br>
14/05/21 14:07:49 INFO scheduler.DAGScheduler: Completed ResultTask(0, 0)<br>
14/05/21 14:07:49 INFO scheduler.TaskSetManager: Finished TID 2 in 91 ms on localhost (progress: 1/2)<br>
14/05/21 14:07:49 INFO storage.BlockManager: Found block broadcast_0 locally<br>
14/05/21 14:07:49 INFO storage.BlockFetcherIterator$BasicBlockFetcherIterator: Getting 2 non-zero-bytes blocks out of 2 blocks<br>
14/05/21 14:07:49 INFO storage.BlockFetcherIterator$BasicBlockFetcherIterator: Started 0 remote gets in  1 ms<br>
14/05/21 14:07:49 INFO executor.Executor: Serialized size of result for 3 is 1009<br>
14/05/21 14:07:49 INFO executor.Executor: Sending result for 3 directly to driver<br>
14/05/21 14:07:49 INFO executor.Executor: Finished task ID 3<br>
14/05/21 14:07:49 INFO scheduler.DAGScheduler: Completed ResultTask(0, 1)<br>
14/05/21 14:07:49 INFO scheduler.TaskSetManager: Finished TID 3 in 16 ms on localhost (progress: 2/2)<br>
14/05/21 14:07:49 INFO scheduler.TaskSchedulerImpl: Removed TaskSet 0.0, whose tasks have all completed, from pool
<br>
14/05/21 14:07:49 INFO scheduler.DAGScheduler: Stage 0 (collect at &lt;console&gt;:17) finished in 0.105 s<br>
14/05/21 14:07:49 INFO spark.SparkContext: Job finished: collect at &lt;console&gt;:17, took 1.403272553 s<br><span style="color:#FF0000;"><span style="background-color:rgb(255,255,255);">res0: Array[(String, Int)] = Array(("",2), (caozw,1), (hello,4), (2.2.0,1), (world,1), (hitachi,2), (bhh,1), (hadoop,1), (china,1), (develop,1))</span></span></p>
<p>就可以看到wordcount的结果了  <br></p>
<p>比起mapreduce来代码精简了三分之二 而且速度很赞<br></p>
<p><br><br></p>
            </div>
                </div>