---
layout:     post
title:      Spark Streaming：初步了解
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">问题导读：</span><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:#ff0000;"><br></span></span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:#ff0000;">1.什么是Spark Streaming？</span></span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:#ff0000;">2.Spark Streaming可以接受那些数据源？</span></span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:#ff0000;">3.Dstream，我们可以进行哪两种操作？</span></span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br><br></span><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">参考：</span><a href="http://www.aboutyun.com/thread-8482-1-1.html" rel="nofollow" style="color:rgb(51,102,153);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">Spark：一个高效的分布式计算系统</a><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br>
在看spark Streaming，我们需要首先知道什么是</span><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">Spark streaming？</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">Spark streaming: 构建在Spark上处理Stream数据的框架，基本的原理是将Stream数据分成小的时间片断（几秒），以类似batch批量处理的方式来处理这小部分数据。Spark Streaming构建在Spark上，一方面是因为Spark的低延迟执行引擎（100ms+）可以用于实时计算，另一方面相比基于Record的其它处理框架（如Storm），RDD数据集更容易做高效的容错处理。此外小批量处理的方式使得它可以同时兼容批量和实时数据处理的逻辑和算法。方便了一些需要历史数据和实时数据联合分析的特定应用场合。</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">Overview</span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
Spark Streaming属于Spark的核心api，它支持高吞吐量、支持容错的实时流数据处理。</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
它可以接受来自Kafka, Flume, Twitter, ZeroMQ和TCP Socket的数据源，使用简单的api函数比如 map, reduce, join, window等操作，还可以直接使用内置的机器学习算法、图算法包来处理数据。</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<img id="aimg_6632" src="http://www.aboutyun.com/data/attachment/forum/201408/24/193916osdq1c7vapiakq9u.png" class="zoom" width="600" alt=""> <br></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
它的工作流程像下面的图所示一样，接受到实时数据后，给数据分批次，然后传给Spark Engine处理最后生成该批次的结果。</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<img id="aimg_6635" src="http://www.aboutyun.com/data/attachment/forum/201408/24/193919bdfdeegmekwhhh0t.png" class="zoom" width="600" alt=""> <br></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
它支持的数据流叫Dstream，直接支持Kafka、Flume的数据源。Dstream是一种连续的RDDs，下面是一个例子帮助大家理解Dstream。<br></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">A Quick Example</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div class="blockcode" style="overflow:hidden;color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<div id="code_j74">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// 创建StreamingContext，1秒一个批次<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val ssc = new StreamingContext(sparkConf, Seconds(1));<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// 获得一个DStream负责连接 监听端口:地址<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val lines = ssc.socketTextStream(serverIP, serverPort);<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// 对每一行数据执行Split操作<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val words = lines.flatMap(_.split(" "));<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// 统计word的数量<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val pairs = words.map(word =&gt; (word, 1));<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val wordCounts = pairs.reduceByKey(_ + _);<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// 输出结果<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
wordCounts.print();<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
ssc.start();             // 开始<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
ssc.awaitTermination();  // 计算完毕退出</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">具体的代码可以访问这个页面：</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><a href="https://github.com/apache/incubator-spark/blob/master/examples/src/main/scala/org/apache/spark/streaming/examples/NetworkWordCount.scala" rel="nofollow" style="color:rgb(51,102,153);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">https://github.com/apache/incuba
 ... workWordCount.scala</a><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">如果已经装好Spark的朋友，我们可以通过下面的例子试试。</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">首先，启动Netcat，这个工具在Unix-like的系统都存在，是个简易的数据服务器。</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">使用下面这句命令来启动Netcat：</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div class="blockcode" style="overflow:hidden;color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<div id="code_Kx1">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
$ nc -lk 9999<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">接着启动example</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div class="blockcode" style="overflow:hidden;color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<div id="code_Zr7">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
$ ./bin/run-example org.apache.spark.streaming.examples.NetworkWordCount local[2] localhost 9999<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">在Netcat这端输入hello world，看Spark这边的</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div class="blockcode" style="overflow:hidden;color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<div id="code_I4h">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
# TERMINAL 1:<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
# Running Netcat<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
$ nc -lk 9999<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
hello world<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
...<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
# TERMINAL 2: RUNNING NetworkWordCount or JavaNetworkWordCount<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
$ ./bin/run-example org.apache.spark.streaming.examples.NetworkWordCount local[2] localhost 9999<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
...<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
-------------------------------------------<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
Time: 1357008430000 ms<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
-------------------------------------------<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
(hello,1)<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
(world,1)<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
...</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">Basics</span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
下面这块是如何编写代码的啦，哇咔咔！</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
首先我们要在SBT或者Maven工程添加以下信息：</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div class="blockcode" style="overflow:hidden;color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<div id="code_RIc">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
groupId = org.apache.spark<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
artifactId = spark-streaming_2.10<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
version = 0.9.0-incubating</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<div class="blockcode" style="overflow:hidden;color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<div id="code_DGE">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
//需要使用一下数据源的，还要添加相应的依赖<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
Source    Artifact<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
Kafka     spark-streaming-kafka_2.10<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
Flume     spark-streaming-flume_2.10<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
Twitter     spark-streaming-twitter_2.10<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
ZeroMQ     spark-streaming-zeromq_2.10<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
MQTT     spark-streaming-mqtt_2.10</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">接着就是实例化</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div class="blockcode" style="overflow:hidden;color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<div id="code_MqL">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
new StreamingContext(master, appName, batchDuration, [sparkHome], [jars])<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
这是之前的例子对DStream的操作。</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<img id="aimg_6633" src="http://www.aboutyun.com/data/attachment/forum/201408/24/193917tp5jq59790sxs75s.png" class="zoom" width="600" alt=""></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">Input Sources</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
除了sockets之外，我们还可以这样创建Dstream</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div class="blockcode" style="overflow:hidden;color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<div id="code_s6F">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
streamingContext.fileStream(dataDirectory)<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
这里有3个要点：</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
（1）dataDirectory下的文件格式都是一样</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
（2）在这个目录下创建文件都是通过移动或者重命名的方式创建的</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
（3）一旦文件进去之后就不能再改变</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
假设我们要创建一个Kafka的Dstream。</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div class="blockcode" style="overflow:hidden;color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<div id="code_z46">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
import org.apache.spark.streaming.kafka._<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
KafkaUtils.createStream(streamingContext, kafkaParams, ...)<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
如果我们需要自定义流的receiver，可以查看<a href="https://spark.incubator.apache.org/docs/latest/streaming-custom-receivers.html" rel="nofollow" style="color:rgb(51,102,153);">https://spark.incubator.apache.o ... stom-receivers.html</a></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">Operations</span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
对于Dstream，我们可以进行两种操作，transformations 和 output</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">Transformations</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div class="blockcode" style="overflow:hidden;color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<div id="code_iX2">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
Transformation                          Meaning<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
map(func)                        对每一个元素执行func方法<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
flatMap(func)                    类似map函数，但是可以map到0+个输出<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
filter(func)                     过滤<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
repartition(numPartitions)       增加分区，提高并行度     <br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
union(otherStream)               合并两个流<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
count()     　　　　　　　　       统计元素的个数<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
reduce(func)                     对RDDs里面的元素进行聚合操作，2个输入参数，1个输出参数<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
countByValue()                   针对类型统计，当一个Dstream的元素的类型是K的时候，调用它会返回一个新的Dstream，包含&lt;K,Long&gt;键值对，Long是每个K出现的频率。<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
reduceByKey(func, [numTasks])    对于一个(K, V)类型的Dstream，为每个key，执行func函数，默认是local是2个线程，cluster是8个线程，也可以指定numTasks <br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
join(otherStream, [numTasks])    把(K, V)和(K, W)的Dstream连接成一个(K, (V, W))的新Dstream <br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
cogroup(otherStream, [numTasks]) 把(K, V)和(K, W)的Dstream连接成一个(K, Seq[V], Seq[W])的新Dstream <br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
transform(func)                  转换操作，把原来的RDD通过func转换成一个新的RDD<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
updateStateByKey(func)           针对key使用func来更新状态和值，可以将state该为任何值</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">UpdateStateByKey Operation</span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
使用这个操作，我们是希望保存它状态的信息，然后持续的更新它，使用它有两个步骤：</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
（1）定义状态，这个状态可以是任意的数据类型</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
（2）定义状态更新函数，从前一个状态更改新的状态</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
下面展示一个例子：</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div class="blockcode" style="overflow:hidden;color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<div id="code_i43">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
def updateFunction(newValues: Seq[Int], runningCount: Option[Int]): Option[Int] = {<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    val newCount = ...  // add the new values with the previous running count to get the new count<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    Some(newCount)<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
}</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
它可以用在包含(word, 1) 的Dstream当中，比如前面展示的example</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div class="blockcode" style="overflow:hidden;color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<div id="code_xhP">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val runningCounts = pairs.updateStateByKey[Int](updateFunction _)<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
它会针对里面的每个word调用一下更新函数，newValues是最新的值，runningCount是之前的值。</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">Transform Operation</span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
和transformWith一样，可以对一个Dstream进行RDD-&gt;RDD操作，比如我们要对Dstream流里的RDD和另外一个数据集进行join操作，但是Dstream的API没有直接暴露出来，我们就可以使用transform方法来进行这个操作，下面是例子：</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div class="blockcode" style="overflow:hidden;color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<div id="code_CD9">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val spamInfoRDD = sparkContext.hadoopFile(...) // RDD containing spam information<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val cleanedDStream = inputDStream.transform(rdd =&gt; {<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  rdd.join(spamInfoRDD).filter(...) // join data stream with spam information to do data cleaning<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  ...<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
})</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
另外，我们也可以在里面使用机器学习算法和图算法。</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">Window Operations</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<img id="aimg_6634" src="http://www.aboutyun.com/data/attachment/forum/201408/24/193918prprpbfx7kn7pbax.png" class="zoom" width="600" alt=""> 、</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
先举个例子吧，比如前面的word count的例子，我们想要每隔10秒计算一下最近30秒的单词总数。</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
我们可以使用以下语句：</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div class="blockcode" style="overflow:hidden;color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<div id="code_Tnw">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// Reduce last 30 seconds of data, every 10 seconds<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val windowedWordCounts = pairs.reduceByKeyAndWindow(_ + _, Seconds(30), Seconds(10))</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
这里面提到了windows的两个参数：</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
（1）window length：window的长度是30秒，最近30秒的数据</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
（2）slice interval：计算的时间间隔</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
通过这个例子，我们大概能够窗口的意思了，定期计算滑动的数据。</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
下面是window的一些操作函数，还是有点儿理解不了window的概念，Meaning就不翻译了，直接删掉</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div class="blockcode" style="overflow:hidden;color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<div id="code_J9i">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
Transformation                                                                              Meaning<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
window(windowLength, slideInterval)     <br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
countByWindow(windowLength, slideInterval)     <br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
reduceByWindow(func, windowLength, slideInterval)     <br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
reduceByKeyAndWindow(func, windowLength, slideInterval, [numTasks])     <br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
reduceByKeyAndWindow(func, invFunc, windowLength, slideInterval, [numTasks])    <br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
countByValueAndWindow(windowLength, slideInterval, [numTasks])</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">Output Operations</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div class="blockcode" style="overflow:hidden;color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<div id="code_S2u">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
Output Operation                                      Meaning<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
print()                                 打印到控制台<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
foreachRDD(func)                        对Dstream里面的每个RDD执行func，保存到外部系统<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
saveAsObjectFiles(prefix, [suffix])     保存流的内容为SequenceFile, 文件名 : "prefix-TIME_IN_MS[.suffix]".<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
saveAsTextFiles(prefix, [suffix])       保存流的内容为文本文件, 文件名 : "prefix-TIME_IN_MS[.suffix]".<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
saveAsHadoopFiles(prefix, [suffix])     保存流的内容为hadoop文件, 文件名 : "prefix-TIME_IN_MS[.suffix]".</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">Persistence</span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
Dstream中的RDD也可以调用persist()方法保存在内存当中，但是基于window和state的操作，reduceByWindow,reduceByKeyAndWindow,updateStateByKey它们就是隐式的保存了，系统已经帮它自动保存了。</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
从网络接收的数据(such as, Kafka, Flume, sockets, etc.)，默认是保存在两个节点来实现容错性，以序列化的方式保存在内存当中。</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">RDD Checkpointing</span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
状态的操作是基于多个批次的数据的。它包括基于window的操作和updateStateByKey。因为状态的操作要依赖于上一个批次的数据，所以它要根据时间，不断累积元数据。为了清空数据，它支持周期性的检查点，通过把中间结果保存到hdfs上。因为检查操作会导致保存到hdfs上的开销，所以设置这个时间间隔，要很慎重。对于小批次的数据，比如一秒的，检查操作会大大降低吞吐量。但是检查的间隔太长，会导致任务变大。通常来说，5-10秒的检查间隔时间是比较合适的。</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div class="blockcode" style="overflow:hidden;color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<div id="code_siP">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
ssc.checkpoint(hdfsPath)  //设置检查点的保存位置<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
dstream.checkpoint(checkpointInterval)  //设置检查点间隔</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
对于必须设置检查点的Dstream，比如通过updateStateByKey和reduceByKeyAndWindow创建的Dstream，默认设置是至少10秒。</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">Performance Tuning</span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
对于调优，可以从两个方面考虑：</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
（1）利用集群资源，减少处理每个批次的数据的时间</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
（2）给每个批次的数据量的设定一个合适的大小</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">Level of Parallelism</span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
像一些分布式的操作，比如reduceByKey和reduceByKeyAndWindow，默认的8个并发线程，可以通过对应的函数提高它的值，或者通过修改参数spark.default.parallelism来提高这个默认值。</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">Task Launching Overheads</span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
通过进行的任务太多也不好，比如每秒50个，发送任务的负载就会变得很重要，很难实现压秒级的时延了，当然可以通过压缩来降低批次的大小。</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">Setting the Right Batch Size</span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
要使流程序能在集群上稳定的运行，要使处理数据的速度跟上数据流入的速度。最好的方式计算这个批量的大小，我们首先设置batch size为5-10秒和一个很低的数据输入速度。确实系统能跟上数据的速度的时候，我们可以根据经验设置它的大小，通过查看日志看看Total delay的多长时间。如果delay的小于batch的，那么系统可以稳定，如果delay一直增加，说明系统的处理速度跟不上数据的输入速度。</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">24/7 Operation</span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
Spark默认不会忘记元数据，比如生成的RDD，处理的stages，但是Spark Streaming是一个24/7的程序，它需要周期性的清理元数据，通过spark.cleaner.ttl来设置。比如我设置它为600，当超过10分钟的时候，Spark就会清楚所有元数据，然后持久化RDDs。但是这个属性要在SparkContext 创建之前设置。</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
但是这个值是和任何的window操作绑定。Spark会要求输入数据在过期之后必须持久化到内存当中，所以必须设置delay的值至少和最大的window操作一致，如果设置小了，就会报错。</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">Monitoring</span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
除了Spark内置的监控能力，还可以StreamingListener这个接口来获取批处理的时间, 查询时延, 全部的端到端的试验。</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">Memory Tuning</span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
Spark Stream默认的序列化方式是<a href="https://spark.incubator.apache.org/docs/latest/api/core/index.html#org.apache.spark.storage.StorageLevel%24" rel="nofollow" style="color:rgb(51,102,153);">StorageLevel.MEMORY_ONLY_SER</a>，而不是RDD的<a href="https://spark.incubator.apache.org/docs/latest/api/core/index.html#org.apache.spark.storage.StorageLevel%24" rel="nofollow" style="color:rgb(51,102,153);">StorageLevel.MEMORY_ONLY</a>。<br></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
默认的，所有持久化的RDD都会通过被Spark的LRU算法剔除出内存，如果设置了spark.cleaner.ttl，就会周期性的清理，但是这个参数设置要很谨慎。一个更好的方法是设置spark.streaming.unpersist为true，这就让Spark来计算哪些RDD需要持久化，这样有利于提高GC的表现。</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
推荐使用concurrent mark-and-sweep GC，虽然这样会降低系统的吞吐量，但是这样有助于更稳定的进行批处理。</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">Fault-tolerance Properties</span><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">Failure
 of a Worker Node</span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
下面有两种失效的方式：</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
1.使用hdfs上的文件，因为hdfs是可靠的文件系统，所以不会有任何的数据失效。</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
2.如果数据来源是网络，比如Kafka和Flume，为了防止失效，默认是数据会保存到2个节点上，但是有一种可能性是接受数据的节点挂了，那么数据可能会丢失，因为它还没来得及把数据复制到另外一个节点。</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">Failure of the Driver Node</span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
为了支持24/7不间断的处理，Spark支持驱动节点失效后，重新恢复计算。Spark Streaming会周期性的写数据到hdfs系统，就是前面的检查点的那个目录。驱动节点失效之后，StreamingContext可以被恢复的。</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
为了让一个Spark Streaming程序能够被回复，它需要做以下操作：</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
（1）第一次启动的时候，创建 StreamingContext，创建所有的streams，然后调用start()方法。</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
（2）恢复后重启的，必须通过检查点的数据重新创建StreamingContext。</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
下面是一个实际的例子：</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
通过StreamingContext.getOrCreate来构造StreamingContext，可以实现上面所说的。</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div class="blockcode" style="overflow:hidden;color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<div id="code_D7Y">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// Function to create and setup a new StreamingContext<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
def functionToCreateContext(): StreamingContext = {<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    val ssc = new StreamingContext(...)   // new context<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    val lines = ssc.socketTextStream(...) // create DStreams<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    ...<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    ssc.checkpoint(checkpointDirectory)   // set checkpoint directory<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    ssc<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
}<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// Get StreaminContext from checkpoint data or create a new one<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
val context = StreamingContext.getOrCreate(checkpointDirectory, functionToCreateContext _)<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// Do additional setup on context that needs to be done,<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// irrespective of whether it is being started or restarted<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
context. ...<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
// Start the context<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
context.start()<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
context.awaitTermination()</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
在stand-alone的部署模式下面，驱动节点失效了，也可以自动恢复，让别的驱动节点替代它。这个可以在本地进行测试，在提交的时候采用supervise模式，当提交了程序之后，使用jps查看进程，看到类似DriverWrapper就杀死它，如果是使用YARN模式的话就得使用其它方式来重新启动了。<br></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
这里顺便提一下向客户端提交程序吧，之前总结的时候把这块给落下了。</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div class="blockcode" style="overflow:hidden;color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<div id="code_hZ3">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
./bin/spark-class org.apache.spark.deploy.Client launch<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
   [client-options] \<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
   &lt;cluster-url&gt; &lt;application-jar-url&gt; &lt;main-class&gt; \<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
   [application-options]<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
cluster-url: master的地址.<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
application-jar-url: jar包的地址，最好是hdfs上的,带上hdfs：//...否则要所有的节点的目录下都有这个jar的 <br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
main-class: 要发布的程序的main函数所在类. <br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
Client Options: <br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
--memory &lt;count&gt; (驱动程序的内存，单位是MB) <br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
--cores &lt;count&gt; (为你的驱动程序分配多少个核心) <br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
--supervise (节点失效的时候，是否重新启动应用) <br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
--verbose (打印增量的日志输出)</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
在未来的版本，会支持所有的数据源的可恢复性。</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
为了更好的理解基于HDFS的驱动节点失效恢复，下面用一个简单的例子来说明：</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<div class="blockcode" style="overflow:hidden;color:rgb(102,102,102);border:1px solid rgb(204,204,204);">
<div id="code_V5D">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
Time Number of lines in input file Output without driver failure Output with driver failure<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
　　10 　　　　　　　　　　　　　　　　　10 　　　　　　　　　　　　　　　　10<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
　　20 　　　　　　　　　　　　　　　　　20 　　　　　　　　　　　　　　　　20<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
　　30 　　　　　　　　　　　　　　　　　30 　　　　　　　　　　　　　　　　30<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
　　40 　　　　　　　　　　　　　　　　　40 　　　　　　　　　　　　　　　　[DRIVER FAILS] no output<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
　　50 　　　　　　　　　　　　　　　　　50 　　　　　　　　　　　　　　　　no output<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
　　60 　　　　　　　　　　　　　　　　　60 　　　　　　　　　　　　　　　　no output<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
　　70 　　　　　　　　　　　　　　　　　70 　　　　　　　　　　　　　　　　[DRIVER RECOVERS] 40, 50, 60, 70<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
　　80 　　　　　　　　　　　　　　　　　80 　　　　　　　　　　　　　　　　80<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
　　90 　　　　　　　　　　　　　　　　　90 　　　　　　　　　　　　　　　　90<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  　100 　　　　　　　　　　　　　　　　   100 　　　　　　　　　　　　 　　　 100</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br><br></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">在4的时候出现了错误，40,50,60都没有输出，到70的时候恢复了，恢复之后把之前没输出的一下子全部输出</span>
            </div>
                </div>