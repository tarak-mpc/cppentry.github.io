---
layout:     post
title:      Spark Streaming和Storm对比
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：					https://blog.csdn.net/u013063153/article/details/75201146				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<table border="1" cellspacing="1" cellpadding="0" width="557"><tbody><tr><td>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;"><br></span></p>
</td>
<td>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;">Spark Streaming</span></p>
</td>
<td>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;">Storm</span></p>
</td>
</tr><tr><td>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;">Data sources</span></p>
</td>
<td>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;">HDFS, HBase, Cassandra, Kafka</span></p>
</td>
<td>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;">HDFS, Base, Cassandra, Kafka</span></p>
</td>
</tr><tr><td>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;">Resource Manager</span></p>
</td>
<td>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;">YARN, Mesos</span></p>
</td>
<td>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;">YARN, Mesos</span></p>
</td>
</tr><tr><td>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;">Latency</span></p>
</td>
<td>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;">Few seconds</span></p>
</td>
<td>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;">&lt;1 second</span></p>
</td>
</tr><tr><td>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;">Fault tolerance(every recourd processed)</span></p>
</td>
<td>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;">Exactly once</span></p>
</td>
<td>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;">At least once</span></p>
</td>
</tr><tr><td>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;">Reliability</span></p>
</td>
<td>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;">Imporoved reliability(Spark + YARN)</span></p>
</td>
<td>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;">Guarantees on data loss(Storm + Kafka)</span></p>
</td>
</tr></tbody></table><p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;">区别：</span></p>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;">1.Latency</span></p>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;">2.Fault tolerance</span></p>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;">3.Reliability</span></p>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;"><br></span></p>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;">Storm架构</span></p>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;"><img src="https://img-blog.csdn.net/20170716141301692" alt=""><br></span></p>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;">Supervisor：从节点，物理机器</span></p>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;">Worker: Supervisor的进程</span></p>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;">executor: Worker的线程</span></p>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;"><br></span></p>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;">Spark Streaming架构：</span></p>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;"><img src="https://img-blog.csdn.net/20170716141521560" alt=""><br></span></p>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;">worker: 物理节点</span></p>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;">executor: 进程</span></p>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;"><br></span></p>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;"><br></span></p>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;">------------------------------</span></p>
<p align="left"><span style="font-family:'Comic Sans MS';font-size:14px;">Data Source:<br>
- SQL<br>
- NOSQL<br>
- Log Data<br>
- Streaming Data<br><br><br>
Ingestion:<br>
- Flume<br>
- Sqoop<br>
- NFS<br>
- Kafka<br><br><br>
Processing:<br>
- MapReduce<br>
- Spark<br>
- Storm<br>
- Drill<br>
- Mahout<br>
- Ooize<br>
- Hive<br>
- Pig<br>
- HBase<br>
- Elasticsearch<br>
- Slor<br><br><br>
Visualization:<br>
- WebTier<br>
- Banana<br>
- Kibana<br>
- Data Warehouse<br><br><br>
ELK:<br>
- E: Elasticsearch<br>
- L: Logstash<br>
- K: Kibana<br><br><br>
Alluxio(Tachyon)&lt;br&gt;<br><br><br>
Real time Processing:<br>
Storm/Trident/Spark Streaming/Samza/Flink&lt;br&gt;<br><br><br><br><br>
Latency:&lt;br&gt;<br>
Spark: Few Seconds&lt;br&gt;<br>
Storm: &lt;1 Seconds&lt;br&gt;<br><br><br>
Fault tolerance:&lt;br&gt;<br>
Spark: Exactly once&lt;br&gt;<br>
Storm: at least once/at most once&lt;br&gt;<br>
Trident:Exactly once&lt;br&gt;<br><br><br>
Reliability:&lt;br&gt;<br>
Spark: Improved reliability (cache)&lt;br&gt;<br>
Storm: Guarantees no data loss&lt;br&gt;<br><br><br>
Storm:&lt;br&gt;<br>
- Niumus<br>
- Zookeeper<br>
- Supervisor<br>
   Worker: 进程&lt;br&gt;<br>
      Executor：线程&lt;br&gt;<br>
      Task: Bolt/Spout&lt;br&gt;<br><br><br>
Spark Streaming:&lt;br&gt;<br>
- Cluster Manager&lt;br&gt;<br>
   Mesos&lt;br&gt;<br>
   Yarn&lt;br&gt;<br>
- Executor<br>
   Task&lt;br&gt;<br>
   cache&lt;br&gt;<br><br><br>
Spark Streaming: DStream&lt;br&gt;<br><br><br>
```<br>
val conf = new SparkConf().setAppName().setMaster("local")<br>
val ssc = new StreamingContext(conf, Seconds(1))<br>
val lines = ssc.socketTextStream("spark001", 9999)<br>
val lines2 = ssc.textFileStream("hdfs://spark:9000/wordcount_dir");<br>
```<br><br><br>
Streaming Window：&lt;br&gt;<br>
reduceByKeyAndWindow(f(), Durations.seconds(60), Durations.seconds(10))&lt;br&gt;<br>
每隔10s计算前60s的RDD。&lt;br&gt;<br><br><br>
Kafka + Spark Streaming + HBase&lt;br&gt;<br>
```<br>
val lines = KafkaUtils.createStream(<br>
ssc,<br>
"192.168.80.201:2181,192.168.80.202:2181,192.168.80.203:2181", //zookeeper<br>
"wordcountGroup",<br>
"topicThreadMap"<br>
)<br>
```<br><br><br>
```<br>
topics.add("topic1")<br>
KafkaParms.put("meta.broker.list",<br>
192.168.80.201:9092,192.168.80.202:9092,192.168.80.203:9092); //broker.list<br>
val lines = KafkaUtils.createDirectStream(<br>
   ssc,<br>
   String.class //key类型<br>
   String.class //value类型<br>
   StringDecoder.class, //解码器<br>
   StringDecode.class,<br>
   KafkaParms,<br>
   topics<br>
) <br>
```<br><br><br>
jar包提交时 --master 会覆盖代码中的.setMaster("local")&lt;br&gt;<br><br><br>
hdfs dfs -copyFromLocal spakr.txt /wordcount_dir&lt;br&gt;<br><br></span></p>
            </div>
                </div>