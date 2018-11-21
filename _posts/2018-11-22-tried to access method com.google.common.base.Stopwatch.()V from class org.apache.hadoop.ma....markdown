---
layout:     post
title:      tried to access method com.google.common.base.Stopwatch.()V from class org.apache.hadoop.ma...
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/blackweekend/article/details/51719385				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>用spark跑一个wordcount，报错如下：</p>
<p>Using Spark's default log4j profile: org/apache/spark/log4j-defaults.properties<br>
16/06/20 11:05:54 INFO SparkContext: Running Spark version 1.6.1<br>
16/06/20 11:05:54 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable<br>
16/06/20 11:05:55 INFO SecurityManager: Changing view acls to: Max<br>
16/06/20 11:05:55 INFO SecurityManager: Changing modify acls to: Max<br>
16/06/20 11:05:55 INFO SecurityManager: SecurityManager: authentication disabled; ui acls disabled; users with view permissions: Set(Max); users with modify permissions: Set(Max)<br>
16/06/20 11:05:55 INFO Utils: Successfully started service 'sparkDriver' on port 53249.<br>
16/06/20 11:05:55 INFO Slf4jLogger: Slf4jLogger started<br>
16/06/20 11:05:56 INFO Remoting: Starting remoting<br>
16/06/20 11:05:56 INFO Remoting: Remoting started; listening on addresses :[akka.tcp://sparkDriverActorSystem@192.168.2.109:53250]<br>
16/06/20 11:05:56 INFO Utils: Successfully started service 'sparkDriverActorSystem' on port 53250.<br>
16/06/20 11:05:56 INFO SparkEnv: Registering MapOutputTracker<br>
16/06/20 11:05:56 INFO SparkEnv: Registering BlockManagerMaster<br>
16/06/20 11:05:56 INFO DiskBlockManager: Created local directory at /private/var/folders/z_/thb8h2gd4hqdcz9z5r9xzl140000gn/T/blockmgr-7fd2ee1f-8875-4e1b-87a6-483264892d39<br>
16/06/20 11:05:56 INFO MemoryStore: MemoryStore started with capacity 1140.4 MB<br>
16/06/20 11:05:56 INFO SparkEnv: Registering OutputCommitCoordinator<br>
16/06/20 11:05:56 INFO Utils: Successfully started service 'SparkUI' on port 4040.<br>
16/06/20 11:05:56 INFO SparkUI: Started SparkUI at http://192.168.2.109:4040<br>
16/06/20 11:05:56 INFO Executor: Starting executor ID driver on host localhost<br>
16/06/20 11:05:56 INFO Utils: Successfully started service 'org.apache.spark.network.netty.NettyBlockTransferService' on port 53253.<br>
16/06/20 11:05:56 INFO NettyBlockTransferService: Server created on 53253<br>
16/06/20 11:05:56 INFO BlockManagerMaster: Trying to register BlockManager<br>
16/06/20 11:05:56 INFO BlockManagerMasterEndpoint: Registering block manager localhost:53253 with 1140.4 MB RAM, BlockManagerId(driver, localhost, 53253)<br>
16/06/20 11:05:56 INFO BlockManagerMaster: Registered BlockManager<br>
16/06/20 11:05:57 INFO MemoryStore: Block broadcast_0 stored as values in memory (estimated size 156.2 KB, free 156.2 KB)<br>
16/06/20 11:05:57 INFO MemoryStore: Block broadcast_0_piece0 stored as bytes in memory (estimated size 14.1 KB, free 170.3 KB)<br>
16/06/20 11:05:57 INFO BlockManagerInfo: Added broadcast_0_piece0 in memory on localhost:53253 (size: 14.1 KB, free: 1140.4 MB)<br>
16/06/20 11:05:57 INFO SparkContext: Created broadcast 0 from textFile at WC.scala:15<br><span style="color:#ff6666;">Exception in thread "main" java.lang.IllegalAccessError: tried to access method com.google.common.base.Stopwatch.&lt;init&gt;()V from class org.apache.hadoop.mapred.FileInputFormat<br><span></span>at org.apache.hadoop.mapred.FileInputFormat.getSplits(FileInputFormat.java:312)<br><span></span>at org.apache.spark.rdd.HadoopRDD.getPartitions(HadoopRDD.scala:199)<br><span></span>at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:239)<br><span></span>at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:237)<br><span></span>at scala.Option.getOrElse(Option.scala:120)<br><span></span>at org.apache.spark.rdd.RDD.partitions(RDD.scala:237)<br><span></span>at org.apache.spark.rdd.MapPartitionsRDD.getPartitions(MapPartitionsRDD.scala:35)<br><span></span>at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:239)<br><span></span>at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:237)<br><span></span>at scala.Option.getOrElse(Option.scala:120)<br><span></span>at org.apache.spark.rdd.RDD.partitions(RDD.scala:237)<br><span></span>at org.apache.spark.rdd.MapPartitionsRDD.getPartitions(MapPartitionsRDD.scala:35)<br><span></span>at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:239)<br><span></span>at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:237)<br><span></span>at scala.Option.getOrElse(Option.scala:120)<br><span></span>at org.apache.spark.rdd.RDD.partitions(RDD.scala:237)<br><span></span>at org.apache.spark.rdd.MapPartitionsRDD.getPartitions(MapPartitionsRDD.scala:35)<br><span></span>at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:239)<br><span></span>at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:237)<br><span></span>at scala.Option.getOrElse(Option.scala:120)<br><span></span>at org.apache.spark.rdd.RDD.partitions(RDD.scala:237)<br><span></span>at org.apache.spark.Partitioner$.defaultPartitioner(Partitioner.scala:65)<br><span></span>at org.apache.spark.rdd.PairRDDFunctions$$anonfun$reduceByKey$3.apply(PairRDDFunctions.scala:331)<br><span></span>at org.apache.spark.rdd.PairRDDFunctions$$anonfun$reduceByKey$3.apply(PairRDDFunctions.scala:331)<br><span></span>at org.apache.spark.rdd.RDDOperationScope$.withScope(RDDOperationScope.scala:150)<br><span></span>at org.apache.spark.rdd.RDDOperationScope$.withScope(RDDOperationScope.scala:111)<br><span></span>at org.apache.spark.rdd.RDD.withScope(RDD.scala:316)<br><span></span>at org.apache.spark.rdd.PairRDDFunctions.reduceByKey(PairRDDFunctions.scala:330)<br><span></span>at cn.itcast.spark.WC$.main(WC.scala:16)<br><span></span>at cn.itcast.spark.WC.main(WC.scala)<br><span></span>at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)<br><span></span>at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)<br><span></span>at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)<br><span></span>at java.lang.reflect.Method.invoke(Method.java:606)<br><span></span>at com.intellij.rt.execution.application.AppMain.main(AppMain.java:140)</span><br>
16/06/20 11:05:58 INFO SparkContext: Invoking stop() from shutdown hook<br>
16/06/20 11:05:58 INFO SparkUI: Stopped Spark web UI at http://192.168.2.109:4040<br>
16/06/20 11:05:58 INFO MapOutputTrackerMasterEndpoint: MapOutputTrackerMasterEndpoint stopped!<br>
16/06/20 11:05:58 INFO MemoryStore: MemoryStore cleared<br>
16/06/20 11:05:58 INFO BlockManager: BlockManager stopped<br>
16/06/20 11:05:58 INFO BlockManagerMaster: BlockManagerMaster stopped<br>
16/06/20 11:05:58 INFO OutputCommitCoordinator$OutputCommitCoordinatorEndpoint: OutputCommitCoordinator stopped!<br>
16/06/20 11:05:58 INFO SparkContext: Successfully stopped SparkContext<br>
16/06/20 11:05:58 INFO ShutdownHookManager: Shutdown hook called<br>
16/06/20 11:05:58 INFO ShutdownHookManager: Deleting directory /private/var/folders/z_/thb8h2gd4hqdcz9z5r9xzl140000gn/T/spark-81bc4c8a-5f7f-45c0-97ab-a82b88619ee5<br><br><br>
Process finished with exit code 1<br></p>
<p>===================================================================================================================================</p>
<p>我的pom.xml几个重要的包得版本如下：</p>
<p>&lt;properties&gt;<br>
    &lt;maven.compiler.source&gt;1.7&lt;/maven.compiler.source&gt;<br>
    &lt;maven.compiler.target&gt;1.7&lt;/maven.compiler.target&gt;<br>
    &lt;encoding&gt;UTF-8&lt;/encoding&gt;<br>
    &lt;scala.version&gt;2.10.6&lt;/scala.version&gt;<br>
    &lt;spark.version&gt;1.6.1&lt;/spark.version&gt;<br>
    &lt;hadoop.version&gt;2.5.2&lt;/hadoop.version&gt;<br>
&lt;/properties&gt;<br></p>
<p>做了几个测试，其他版本不变，hadoop.version 为 2.4.0，2.4.1，2.5.2，2.6.1，2.6.4 都会报上面的错误，不知道是不是hadoop的bug，有人说将hadoop的源码重新编译一下就行，但我没试。</p>
<p><strong><span style="color:#ff0000;"><br>
解决办法：</span></strong><br>
把hadoop version改成2.2.0就可以了，测了一下2.7.2也可以，所以估计是hadoop的一个坑，2.7.2就修复了。其他版本没试<br></p>
<p><br></p>
<p><br></p>
<p><br></p>
            </div>
                </div>