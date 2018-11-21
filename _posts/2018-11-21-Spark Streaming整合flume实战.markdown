---
layout:     post
title:      Spark Streaming整合flume实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>Spark Streaming对接Flume有两种方式</p>

<ul><li>Poll：Spark Streaming从flume 中拉取数据</li>
	<li>Push：Flume将消息Push推给Spark Streaming</li>
</ul><h3 id="1安装flume16以上"><a name="t1"></a>1、安装flume1.6以上</h3>

<h3 id="2下载依赖包"><a name="t2"></a>2、下载依赖包</h3>

<p>spark-streaming-flume-sink_2.11-2.0.2.jar放入到flume的lib目录下</p>

<h3 id="3生成数据"><a name="t3"></a>3、生成数据</h3>

<p>服务器上的 /root/data目录下准备数据文件data.txt</p>

<pre class="has">
<code>vi data.txt

hadoop spark hive spark
hadoop sqoop flume redis flume hadoop
solr kafka solr hadoop</code></pre>

<h3 id="4配置采集方案"><a name="t4"></a>4、配置采集方案</h3>

<pre class="has">
<code>vi flume-poll.conf

a1.sources = r1
a1.sinks = k1
a1.channels = c1
#source
a1.sources.r1.channels = c1
a1.sources.r1.type = spooldir
a1.sources.r1.spoolDir = /root/data
a1.sources.r1.fileHeader = true
#channel
a1.channels.c1.type =memory
a1.channels.c1.capacity = 20000
a1.channels.c1.transactionCapacity=5000
#sinks
a1.sinks.k1.channel = c1
a1.sinks.k1.type = org.apache.spark.streaming.flume.sink.SparkSink
a1.sinks.k1.hostname=hdp-node-01
a1.sinks.k1.port = 8888
a1.sinks.k1.batchSize= 2000   </code></pre>

<h3 id="5添加依赖"><a name="t5"></a>5、添加依赖</h3>

<pre class="has">
<code>&lt;dependency&gt;
    &lt;groupId&gt;org.apache.spark&lt;/groupId&gt;
    &lt;artifactId&gt;spark-streaming-flume_2.10&lt;/artifactId&gt;
    &lt;version&gt;2.0.2&lt;/version&gt;
&lt;/dependency&gt;</code></pre>

<h3 id="6代码实现"><a name="t6"></a>6、代码实现</h3>

<pre class="has">
<code>package cn.cheng.spark
import java.net.InetSocketAddress
import org.apache.spark.storage.StorageLevel
import org.apache.spark.streaming.dstream.{DStream, ReceiverInputDStream}
import org.apache.spark.streaming.flume.{FlumeUtils, SparkFlumeEvent}
import org.apache.spark.streaming.{Seconds, StreamingContext}
import org.apache.spark.{SparkConf, SparkContext}

/**
  * sparkStreaming整合flume 拉模式Poll

  */
object SparkStreaming_Flume_Poll {
  //newValues 表示当前批次汇总成的(word,1)中相同单词的所有的1
  //runningCount 历史的所有相同key的value总和
  def updateFunction(newValues: Seq[Int], runningCount: Option[Int]): Option[Int] = {
    val newCount =runningCount.getOrElse(0)+newValues.sum
    Some(newCount)
  }


  def main(args: Array[String]): Unit = {
    //配置sparkConf参数
    val sparkConf: SparkConf = new SparkConf().setAppName("SparkStreaming_Flume_Poll").setMaster("local[2]")
    //构建sparkContext对象
    val sc: SparkContext = new SparkContext(sparkConf)
    //构建StreamingContext对象，每个批处理的时间间隔
    val scc: StreamingContext = new StreamingContext(sc, Seconds(5))
    //设置checkpoint
      scc.checkpoint("./")
    //设置flume的地址，可以设置多台
    val address=Seq(new InetSocketAddress("192.168.200.160",8888))
    // 从flume中拉取数据
    val flumeStream: ReceiverInputDStream[SparkFlumeEvent] = FlumeUtils.createPollingStream(scc,address,StorageLevel.MEMORY_AND_DISK)

    //获取flume中数据，数据存在event的body中，转化为String
    val lineStream: DStream[String] = flumeStream.map(x=&gt;new String(x.event.getBody.array()))
    //实现单词汇总
   val result: DStream[(String, Int)] = lineStream.flatMap(_.split(" ")).map((_,1)).updateStateByKey(updateFunction)

    result.print()
    scc.start()
    scc.awaitTermination()
  }

}</code></pre>

<h3 id="7启动flume"><a name="t7"></a>7、启动flume</h3>

<pre class="has">
<code>flume-ng agent -n a1 -c /opt/bigdata/flume/conf -f /opt/bigdata/flume/conf/flume-poll.conf -Dflume.root.logger=INFO,console</code></pre>

<h3 id="8启动spark-streaming应用程序"><a name="t8"></a>8、启动spark-streaming应用程序</h3>

<h3 id="9查看结果"><a name="t9"></a>9、查看结果</h3>

<p><img alt="这里写图片描述" class="has" src="https://img-blog.csdn.net/20180311193924486?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfNDE0NTU0MjA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast"></p>

<p> </p>

<h2 id="flume将消息push推给spark-streaming">flume将消息Push推给Spark Streaming</h2>

<h3 id="1配置采集方案"><a></a>1、配置采集方案</h3>

<pre class="has">
<code>vi flume-push.conf

#push mode
a1.sources = r1
a1.sinks = k1
a1.channels = c1
#source
a1.sources.r1.channels = c1
a1.sources.r1.type = spooldir
a1.sources.r1.spoolDir = /root/data
a1.sources.r1.fileHeader = true
#channel
a1.channels.c1.type =memory
a1.channels.c1.capacity = 20000
a1.channels.c1.transactionCapacity=5000
#sinks
a1.sinks.k1.channel = c1
a1.sinks.k1.type = avro
a1.sinks.k1.hostname=172.16.43.63
a1.sinks.k1.port = 8888
a1.sinks.k1.batchSize= 2000     </code></pre>

<p>注意配置文件中指明的hostname和port是spark应用程序所在服务器的ip地址和端口。</p>

<h3 id="2代码实现"><a></a>2、代码实现</h3>

<pre class="has">
<code>package cn.cheng.spark

import java.net.InetSocketAddress

import org.apache.spark.storage.StorageLevel
import org.apache.spark.streaming.dstream.{DStream, ReceiverInputDStream}
import org.apache.spark.streaming.flume.{FlumeUtils, SparkFlumeEvent}
import org.apache.spark.streaming.{Seconds, StreamingContext}
import org.apache.spark.{SparkConf, SparkContext}

/**
  * sparkStreaming整合flume  推模式Push
  */
object SparkStreaming_Flume_Push {
  //newValues 表示当前批次汇总成的(word,1)中相同单词的所有的1
  //runningCount 历史的所有相同key的value总和
  def updateFunction(newValues: Seq[Int], runningCount: Option[Int]): Option[Int] = {
    val newCount =runningCount.getOrElse(0)+newValues.sum
    Some(newCount)
  }


  def main(args: Array[String]): Unit = {
    //配置sparkConf参数
    val sparkConf: SparkConf = new SparkConf().setAppName("SparkStreaming_Flume_Push").setMaster("local[2]")
    //构建sparkContext对象
    val sc: SparkContext = new SparkContext(sparkConf)
    //构建StreamingContext对象，每个批处理的时间间隔
    val scc: StreamingContext = new StreamingContext(sc, Seconds(5))
    //设置日志输出级别
    sc.setLogLevel("WARN")
    //设置检查点目录
    scc.checkpoint("./")
    //flume推数据过来
    // 当前应用程序部署的服务器ip地址，跟flume配置文件保持一致
    val flumeStream: ReceiverInputDStream[SparkFlumeEvent] = FlumeUtils.createStream(scc,"172.16.43.63",8888,StorageLevel.MEMORY_AND_DISK)

    //获取flume中数据，数据存在event的body中，转化为String
    val lineStream: DStream[String] = flumeStream.map(x=&gt;new String(x.event.getBody.array()))
    //实现单词汇总
   val result: DStream[(String, Int)] = lineStream.flatMap(_.split(" ")).map((_,1)).updateStateByKey(updateFunction)

    result.print()
    scc.start()
    scc.awaitTermination()
  }

}
}</code></pre>

<h3 id="3启动spark-streaming应用程序"><a></a>3、启动spark-streaming应用程序</h3>

<h3 id="4生成数据"><a></a>4、生成数据</h3>

<pre class="has">
<code>cp data.txt data2.txt</code></pre>

<h3 id="5启动flume"><a></a>5、启动flume</h3>

<pre class="has">
<code>flume-ng agent -n a1 -c /opt/bigdata/flume/conf -f /opt/bigdata/flume/conf/flume-push.conf -Dflume.root.logger=INFO,console</code></pre>

<h3 id="6查看结果"><a></a>6、查看结果</h3>

<p><img alt="这里写图片描述" class="has" src="https://img-blog.csdn.net/20180311194905431?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfNDE0NTU0MjA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast"></p>

<p> </p>            </div>
                </div>