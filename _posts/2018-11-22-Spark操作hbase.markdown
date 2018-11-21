---
layout:     post
title:      Spark操作hbase
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>在Spark是一种计算框架，在Spark环境下，不仅支持操作单机文件，HDFS文件，同时也可以用Spark对Hbase操作。</p><p> 企业中数据源会从HBase取出，这就涉及到了读取hbase数据，本文为了尽可能的让大家能尽快实践和操作Hbase，使用的是Spark Shell 来进行Hbase操作。</p><h2>一、环境：</h2><p>Haoop2.2.0</p><p>Hbase版本0.96.2-hadoop2, r1581096</p><p>Spark1.0.0</p><p>本文假设环境已经搭建好，Spark环境搭建可见<a href="http://blog.csdn.net/oopsoom/article/details/24257981" rel="nofollow">Spark Haoop集群搭建</a></p><p>Hadoop2.2.0要注意和Hbase的版本兼容，这里Hbase采用0.96.2</p><h2>二、原理</h2><p>Spark操作HBase其实是和java client操作HBase的原理是一致的：</p><p>scala和java都是基于jvm的语言，只要将hbase的类加载到classpath内，即可调用操作，其它框架类似。</p><p><strong>相同点</strong>：即都是当作client来连接HMaster，然后利用hbase的API来对Hbase进行操作。</p><p><strong>不同点</strong>：唯一不同的是：Spark可以将Hbase的数据来当作RDD处理，从而利用Spark来进行并行计算。</p><h2>三、实践</h2><div>1、首先检查依赖jar包，在这之前如果hbase的jar包不在spark-shell的classpath里，则需要添加进来。</div><div>设置方法： 在Spark-evn.sh里添加SPARK_CLASSPATH=/home/victor/software/hbase/lib/*</div><div>这样再再启动启动bin/spark-shell， 启动完毕并且Worker成功注册上之后，import jar 包。 </div><div><br></div><h3>2、操作hbase</h3><h4>2.1 Hbase中数据</h4><div>hbase里有张score表，里面有2个CF，分别为course和grade。数据如下：</div><div><pre><code class="language-java">hbase(main):001:0&gt; scan 'scores'
ROW                                    COLUMN+CELL                                                                                                     
 Jim                                   column=course:art, timestamp=1404142440676, value=67                                                            
 Jim                                   column=course:math, timestamp=1404142434405, value=77                                                           
 Jim                                   column=grade:, timestamp=1404142422653, value=3                                                                 
 Tom                                   column=course:art, timestamp=1404142407018, value=88                                                            
 Tom                                   column=course:math, timestamp=1404142398986, value=97                                                           
 Tom                                   column=grade:, timestamp=1404142383206, value=5                                                                 
 shengli                               column=course:art, timestamp=1404142468266, value=17                                                            
 shengli                               column=course:math, timestamp=1404142461952, value=27                                                           
 shengli                               column=grade:, timestamp=1404142452157, value=8                                                                 
3 row(s) in 0.3230 seconds
</code></pre><div><br></div><h4>2.1  初始化连接参数</h4><div><pre><code class="language-java">scala&gt; import org.apache.spark._
import org.apache.spark._

scala&gt; import org.apache.spark.rdd.NewHadoopRDD
import org.apache.spark.rdd.NewHadoopRDD

scala&gt; import org.apache.hadoop.conf.Configuration;  
import org.apache.hadoop.conf.Configuration

scala&gt; import org.apache.hadoop.hbase.HBaseConfiguration;  
import org.apache.hadoop.hbase.HBaseConfiguration

scala&gt; import org.apache.hadoop.hbase.mapreduce.TableInputFormat
import org.apache.hadoop.hbase.mapreduce.TableInputFormat

scala&gt; val configuration = HBaseConfiguration.create();  //初始化配置
configuration: org.apache.hadoop.conf.Configuration = Configuration: core-default.xml, core-site.xml, mapred-default.xml, mapred-site.xml, yarn-default.xml, yarn-site.xml, hbase-default.xml, hbase-site.xml

scala&gt; configuration.set("hbase.zookeeper.property.clientPort", "2181"); //设置zookeeper client端口
 
scala&gt; configuration.set("hbase.zookeeper.quorum", "localhost");  //设置zookeeper quorum

scala&gt; configuration.set("hbase.master", "localhost:60000");  //设置hbase master

scala&gt; configuration.addResource("/home/victor/software/hbase/conf/hbase-site.xml")  //将hbase的配置加载

</code></pre><pre><code class="language-java"></code></pre><pre class="java">scala&gt; <span style="font-family:Arial, Helvetica, sans-serif;">configuration.set(</span><span style="font-family:Arial, Helvetica, sans-serif;">TableInputFormat.INPUT_TABLE, "scores"</span><span style="font-family:Arial, Helvetica, sans-serif;">)</span></pre>scala&gt; import org.apache.hadoop.hbase.client.HBaseAdminimport org.apache.hadoop.hbase.client.HBaseAdmin<pre><code class="language-java">scala&gt; val hadmin = new HBaseAdmin(configuration); //实例化hbase管理
2014-07-01 00:39:24,649 INFO  [main] zookeeper.ZooKeeper (ZooKeeper.java:&lt;init&gt;(438)) - Initiating client connection, connectString=localhost:2181 sessionTimeout=90000 watcher=hconnection-0xc7eea5, quorum=localhost:2181, baseZNode=/hbase
2014-07-01 00:39:24,707 INFO  [main] zookeeper.RecoverableZooKeeper (RecoverableZooKeeper.java:&lt;init&gt;(120)) - Process identifier=hconnection-0xc7eea5 connecting to ZooKeeper ensemble=localhost:2181
2014-07-01 00:39:24,753 INFO  [main-SendThread(localhost:2181)] zookeeper.ClientCnxn (ClientCnxn.java:logStartConnect(966)) - Opening socket connection to server localhost/127.0.0.1:2181. Will not attempt to authenticate using SASL (unknown error)
2014-07-01 00:39:24,755 INFO  [main-SendThread(localhost:2181)] zookeeper.ClientCnxn (ClientCnxn.java:primeConnection(849)) - Socket connection established to localhost/127.0.0.1:2181, initiating session
2014-07-01 00:39:24,938 INFO  [main-SendThread(localhost:2181)] zookeeper.ClientCnxn (ClientCnxn.java:onConnected(1207)) - Session establishment complete on server localhost/127.0.0.1:2181, sessionid = 0x146ed61c4ef0015, negotiated timeout = 40000
hadmin: org.apache.hadoop.hbase.client.HBaseAdmin = org.apache.hadoop.hbase.client.HBaseAdmin@1260466</code></pre><br><pre></pre>接下来用haoop api来创建一个RDD</div><div><pre><code class="language-java">scala&gt; val hrdd = sc.newAPIHadoopRDD(configuration, classOf[TableInputFormat], 
     | classOf[org.apache.hadoop.hbase.io.ImmutableBytesWritable],
     | classOf[org.apache.hadoop.hbase.client.Result])
2014-07-01 00:51:06,683 WARN  [main] util.SizeEstimator (Logging.scala:logWarning(70)) - Failed to check whether UseCompressedOops is set; assuming yes
2014-07-01 00:51:06,936 INFO  [main] storage.MemoryStore (Logging.scala:logInfo(58)) - ensureFreeSpace(85877) called with curMem=0, maxMem=308910489
2014-07-01 00:51:06,946 INFO  [main] storage.MemoryStore (Logging.scala:logInfo(58)) - Block broadcast_0 stored as values to memory (estimated size 83.9 KB, free 294.5 MB)
hrdd: org.apache.spark.rdd.RDD[(org.apache.hadoop.hbase.io.ImmutableBytesWritable, org.apache.hadoop.hbase.client.Result)] = NewHadoopRDD[0] at newAPIHadoopRDD at &lt;console&gt;:22
</code></pre><div><br></div><h3>版本一：（最新的版本下列code可能不work，请看版本二）</h3><h4>读取记录：</h4></div><div>这里我们take 1 条数据，可以看到格式是按照我们设定的HadoopRDD。key是一个不变的ImmutableBytesWritable，value是Hbase的Result</div><div><pre><code class="language-java">scala&gt; hrdd take 1
2014-07-01 00:51:50,371 INFO  [main] spark.SparkContext (Logging.scala:logInfo(58)) - Starting job: take at &lt;console&gt;:25
2014-07-01 00:51:50,423 INFO  [spark-akka.actor.default-dispatcher-16] scheduler.DAGScheduler (Logging.scala:logInfo(58)) - Got job 0 (take at &lt;console&gt;:25) with 1 output partitions (allowLocal=true)
2014-07-01 00:51:50,425 INFO  [spark-akka.actor.default-dispatcher-16] scheduler.DAGScheduler (Logging.scala:logInfo(58)) - Final stage: Stage 0(take at &lt;console&gt;:25)
2014-07-01 00:51:50,426 INFO  [spark-akka.actor.default-dispatcher-16] scheduler.DAGScheduler (Logging.scala:logInfo(58)) - Parents of final stage: List()
2014-07-01 00:51:50,477 INFO  [spark-akka.actor.default-dispatcher-16] scheduler.DAGScheduler (Logging.scala:logInfo(58)) - Missing parents: List()
2014-07-01 00:51:50,478 INFO  [spark-akka.actor.default-dispatcher-16] scheduler.DAGScheduler (Logging.scala:logInfo(58)) - Computing the requested partition locally
2014-07-01 00:51:50,509 INFO  [Local computation of job 0] rdd.NewHadoopRDD (Logging.scala:logInfo(58)) - Input split: localhost:,
2014-07-01 00:51:50,894 INFO  [main] spark.SparkContext (Logging.scala:logInfo(58)) - Job finished: take at &lt;console&gt;:25, took 0.522612687 s
res5: Array[(org.apache.hadoop.hbase.io.ImmutableBytesWritable, org.apache.hadoop.hbase.client.Result)] = Array((4a 69 6d,keyvalues={Jim/course:art/1404142440676/Put/vlen=2/mvcc=0, Jim/course:math/1404142434405/Put/vlen=2/mvcc=0, Jim/grade:/1404142422653/Put/vlen=1/mvcc=0}))
</code></pre><br>找到Result对象</div><div><pre><code class="language-java">scala&gt; val res = hrdd.take(1)
2014-07-01 01:09:13,486 INFO  [main] spark.SparkContext (Logging.scala:logInfo(58)) - Starting job: take at &lt;console&gt;:24
2014-07-01 01:09:13,487 INFO  [spark-akka.actor.default-dispatcher-15] scheduler.DAGScheduler (Logging.scala:logInfo(58)) - Got job 4 (take at &lt;console&gt;:24) with 1 output partitions (allowLocal=true)
2014-07-01 01:09:13,487 INFO  [spark-akka.actor.default-dispatcher-15] scheduler.DAGScheduler (Logging.scala:logInfo(58)) - Final stage: Stage 4(take at &lt;console&gt;:24)
2014-07-01 01:09:13,487 INFO  [spark-akka.actor.default-dispatcher-15] scheduler.DAGScheduler (Logging.scala:logInfo(58)) - Parents of final stage: List()
2014-07-01 01:09:13,488 INFO  [spark-akka.actor.default-dispatcher-15] scheduler.DAGScheduler (Logging.scala:logInfo(58)) - Missing parents: List()
2014-07-01 01:09:13,488 INFO  [spark-akka.actor.default-dispatcher-15] scheduler.DAGScheduler (Logging.scala:logInfo(58)) - Computing the requested partition locally
2014-07-01 01:09:13,488 INFO  [Local computation of job 4] rdd.NewHadoopRDD (Logging.scala:logInfo(58)) - Input split: localhost:,
2014-07-01 01:09:13,504 INFO  [main] spark.SparkContext (Logging.scala:logInfo(58)) - Job finished: take at &lt;console&gt;:24, took 0.018069267 s
res: Array[(org.apache.hadoop.hbase.io.ImmutableBytesWritable, org.apache.hadoop.hbase.client.Result)] = Array((4a 69 6d,keyvalues={Jim/course:art/1404142440676/Put/vlen=2/mvcc=0, Jim/course:math/1404142434405/Put/vlen=2/mvcc=0, Jim/grade:/1404142422653/Put/vlen=1/mvcc=0}))

scala&gt; res(0)
res33: (org.apache.hadoop.hbase.io.ImmutableBytesWritable, org.apache.hadoop.hbase.client.Result) = (4a 69 6d,keyvalues={Jim/course:art/1404142440676/Put/vlen=2/mvcc=0, Jim/course:math/1404142434405/Put/vlen=2/mvcc=0, Jim/grade:/1404142422653/Put/vlen=1/mvcc=0})

scala&gt; res(0)._2
res34: org.apache.hadoop.hbase.client.Result = keyvalues={Jim/course:art/1404142440676/Put/vlen=2/mvcc=0, Jim/course:math/1404142434405/Put/vlen=2/mvcc=0, Jim/grade:/1404142422653/Put/vlen=1/mvcc=0}

scala&gt; val rs = res(0)._2
rs: org.apache.hadoop.hbase.client.Result = keyvalues={Jim/course:art/1404142440676/Put/vlen=2/mvcc=0, Jim/course:math/1404142434405/Put/vlen=2/mvcc=0, Jim/grade:/1404142422653/Put/vlen=1/mvcc=0}

scala&gt; rs.
asInstanceOf             cellScanner              containsColumn           containsEmptyColumn      containsNonEmptyColumn   copyFrom                 
getColumn                getColumnCells           getColumnLatest          getColumnLatestCell      getExists                getFamilyMap             
getMap                   getNoVersionMap          getRow                   getValue                 getValueAsByteBuffer     isEmpty                  
isInstanceOf             list                     listCells                loadValue                raw                      rawCells                 
setExists                size                     toString                 value                    
</code></pre><div><br></div>遍历这条记录，取出每个cell的值：<br><pre><code class="language-java">scala&gt; val kv_array = rs.raw
warning: there were 1 deprecation warning(s); re-run with -deprecation for details
kv_array: Array[org.apache.hadoop.hbase.KeyValue] = Array(Jim/course:art/1404142440676/Put/vlen=2/mvcc=0, Jim/course:math/1404142434405/Put/vlen=2/mvcc=0, Jim/grade:/1404142422653/Put/vlen=1/mvcc=0)</code></pre><div><br></div><h4>遍历记录</h4><pre><code class="language-java">scala&gt; for(keyvalue &lt;- kv) println("rowkey:"+ new String(keyvalue.getRow)+ " cf:"+new String(keyvalue.getFamily()) + " column:" + new String(keyvalue.getQualifier) + " " + "value:"+new String(keyvalue.getValue()))
warning: there were 4 deprecation warning(s); re-run with -deprecation for details
rowkey:Jim cf:course column:art value:67
rowkey:Jim cf:course column:math value:77
rowkey:Jim cf:grade column: value:3
</code></pre><br><h4>查询记录个数</h4></div><div><pre><code class="language-java">scala&gt; hrdd.count
2014-07-01 01:26:03,133 INFO  [main] spark.SparkContext (Logging.scala:logInfo(58)) - Starting job: count at &lt;console&gt;:25
2014-07-01 01:26:03,134 INFO  [spark-akka.actor.default-dispatcher-16] scheduler.DAGScheduler (Logging.scala:logInfo(58)) - Got job 5 (count at &lt;console&gt;:25) with 1 output partitions (allowLocal=false)
2014-07-01 01:26:03,134 INFO  [spark-akka.actor.default-dispatcher-16] scheduler.DAGScheduler (Logging.scala:logInfo(58)) - Final stage: Stage 5(count at &lt;console&gt;:25)
2014-07-01 01:26:03,134 INFO  [spark-akka.actor.default-dispatcher-16] scheduler.DAGScheduler (Logging.scala:logInfo(58)) - Parents of final stage: List()
2014-07-01 01:26:03,135 INFO  [spark-akka.actor.default-dispatcher-16] scheduler.DAGScheduler (Logging.scala:logInfo(58)) - Missing parents: List()
2014-07-01 01:26:03,166 INFO  [spark-akka.actor.default-dispatcher-16] scheduler.DAGScheduler (Logging.scala:logInfo(58)) - Submitting Stage 5 (NewHadoopRDD[0] at newAPIHadoopRDD at &lt;console&gt;:22), which has no missing parents
2014-07-01 01:26:03,397 INFO  [spark-akka.actor.default-dispatcher-16] scheduler.DAGScheduler (Logging.scala:logInfo(58)) - Submitting 1 missing tasks from Stage 5 (NewHadoopRDD[0] at newAPIHadoopRDD at &lt;console&gt;:22)
2014-07-01 01:26:03,401 INFO  [spark-akka.actor.default-dispatcher-16] scheduler.TaskSchedulerImpl (Logging.scala:logInfo(58)) - Adding task set 5.0 with 1 tasks
2014-07-01 01:26:03,427 INFO  [spark-akka.actor.default-dispatcher-16] scheduler.FairSchedulableBuilder (Logging.scala:logInfo(58)) - Added task set TaskSet_5 tasks to pool default
2014-07-01 01:26:03,439 INFO  [spark-akka.actor.default-dispatcher-5] scheduler.TaskSetManager (Logging.scala:logInfo(58)) - Starting task 5.0:0 as TID 0 on executor 0: 192.168.2.105 (PROCESS_LOCAL)
2014-07-01 01:26:03,469 INFO  [spark-akka.actor.default-dispatcher-5] scheduler.TaskSetManager (Logging.scala:logInfo(58)) - Serialized task 5.0:0 as 1305 bytes in 7 ms
2014-07-01 01:26:11,015 INFO  [Result resolver thread-0] scheduler.TaskSetManager (Logging.scala:logInfo(58)) - Finished TID 0 in 7568 ms on 192.168.2.105 (progress: 1/1)
2014-07-01 01:26:11,017 INFO  [Result resolver thread-0] scheduler.TaskSchedulerImpl (Logging.scala:logInfo(58)) - Removed TaskSet 5.0, whose tasks have all completed, from pool default
2014-07-01 01:26:11,036 INFO  [spark-akka.actor.default-dispatcher-4] scheduler.DAGScheduler (Logging.scala:logInfo(58)) - Completed ResultTask(5, 0)
2014-07-01 01:26:11,057 INFO  [spark-akka.actor.default-dispatcher-4] scheduler.DAGScheduler (Logging.scala:logInfo(58)) - Stage 5 (count at &lt;console&gt;:25) finished in 7.605 s
2014-07-01 01:26:11,067 INFO  [main] spark.SparkContext (Logging.scala:logInfo(58)) - Job finished: count at &lt;console&gt;:25, took 7.933270634 s
res71: Long = 3
</code></pre><br></div><h3>版本二、</h3><div><pre><code class="language-java">hrdd.map(tuple =&gt; tuple._2).map(result =&gt; (result.getRow, result.getColumn("course".getBytes(), "art".getBytes()))).map(row =&gt; {
(
  row._1.map(_.toChar).mkString,
  row._2.asScala.reduceLeft {
    (a, b) =&gt; if (a.getTimestamp &gt; b.getTimestamp) a else b
  }.getValue.map(_.toChar).mkString
)
}).take(10)</code></pre><div><br></div>这样就能得到row key 和对应 column family的值了。<br><br></div><br></div><div>四、总结</div><div><br></div><div>Spark操作Hbase其实和java client操作Hbas大体流程是一致的，都是客户端去连接HMaster，最终利用java api来操作hbase。</div><div>只不过Spark提供了一种与RDD结合的概念，并且利用scala的语法简洁性，提高了编程效率。</div><div><br></div><div>——EOF——</div><div><br></div><div>原创文章，转载请注明出自：<a href="http://blog.csdn.net/oopsoom/article/details/36071323" rel="nofollow">http://blog.csdn.net/oopsoom/article/details/36071323</a></div>            </div>
                </div>