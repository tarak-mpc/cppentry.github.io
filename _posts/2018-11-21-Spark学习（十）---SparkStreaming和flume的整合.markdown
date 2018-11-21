---
layout:     post
title:      Spark学习（十）---SparkStreaming和flume的整合
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>这次我们介绍SparkStreaming和flume的整合，flume作为日志实时采集的框架，可以与SparkStreaming实时处理框架进行对接，flume实时产生数据，sparkStreaming做实时处理。<br>
Spark Streaming对接FlumeNG有两种方式，一种是FlumeNG将消息Push推给Spark Streaming，还有一种是Spark Streaming从flume 中Poll拉取数据。</p>
<hr>
<h1><a id="_4"></a>环境准备</h1>
<ul>
<li>
<p>1.1 安装flume1.6以上</p>
</li>
<li>
<p>1.2 下载依赖包<br>
spark-streaming-flume-sink_2.11-2.0.2.jar放入到flume的lib目录下</p>
</li>
<li>
<p>1.3 修改flume/lib下的scala依赖包版本<br>
从spark安装目录的jars文件夹下找到scala-library-2.11.8.jar 包，替换掉flume的lib目录下自带的scala-library-2.10.1.jar。</p>
</li>
</ul>
<h1><a id="1_Poll_13"></a>1. Poll方式</h1>
<ul>
<li>
<p>1.1 写flume的agent，注意既然是拉取的方式，那么flume向自己所在的机器上产数据就行</p>
</li>
<li>
<p>1.2编写flume-poll.conf配置文件，放在flume的conf目录下，提前在/root目录下创建data目录</p>
</li>
</ul>
<pre><code>a1.sources = r1
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
a1.sinks.k1.hostname=nidel01
a1.sinks.k1.port = 8888
a1.sinks.k1.batchSize= 2000   
</code></pre>
<ul>
<li>1.3 编写代码<br>
pom依赖</li>
</ul>
<pre><code>&lt;dependency&gt;
    &lt;groupId&gt;org.apache.spark&lt;/groupId&gt;
    &lt;artifactId&gt;spark-streaming-flume_2.11&lt;/artifactId&gt;
    &lt;version&gt;2.0.2&lt;/version&gt;
&lt;/dependency&gt;
</code></pre>
<p>运行代码</p>
<pre><code>import org.apache.spark.{SparkConf, SparkContext}
import org.apache.spark.streaming.{Seconds, StreamingContext}
import org.apache.spark.streaming.dstream.{DStream, ReceiverInputDStream}
import org.apache.spark.streaming.flume.{FlumeUtils, SparkFlumeEvent}

//todo:sparkStreaming整合flume----采用的是拉模式
object SparkStreamingPollFlume {
  def main(args: Array[String]): Unit = {
  
      //1、创建sparkConf
      val sparkConf: SparkConf = new SparkConf().setAppName("SparkStreamingPollFlume").setMaster("local[2]")
      
      //2、创建sparkContext
      val sc = new SparkContext(sparkConf)
      sc.setLogLevel("WARN")
    
     //3、创建streamingContext
      val ssc = new StreamingContext(sc,Seconds(5))
      ssc.checkpoint("./flume")
    
     //4、通过FlumeUtils调用createPollingStream方法获取flume中的数据
     val pollingStream: ReceiverInputDStream[SparkFlumeEvent] = FlumeUtils.createPollingStream(ssc,"192.168.200.100",8888)
   
    //5、获取flume中event的body {"headers":xxxxxx,"body":xxxxx}
     val data: DStream[String] = pollingStream.map(x=&gt;new String(x.event.getBody.array()))
   
    //6、切分每一行,每个单词计为1
    val wordAndOne: DStream[(String, Int)] = data.flatMap(_.split(" ")).map((_,1))
  
    //7、相同单词出现的次数累加
      val result: DStream[(String, Int)] = wordAndOne.updateStateByKey(updateFunc)
   
    //8、打印输出
      result.print()
   
    //9、开启流式计算
      ssc.start()
      ssc.awaitTermination()

  }

  //currentValues:他表示在当前批次每个单词出现的所有的1   (hadoop,1) (hadoop,1)(hadoop,1)
  //historyValues:他表示在之前所有批次中每个单词出现的总次数   (hadoop,100)
  
  def updateFunc(currentValues:Seq[Int], historyValues:Option[Int]):Option[Int] = {
    val newValue: Int = currentValues.sum+historyValues.getOrElse(0)
    Some(newValue)
  }

}
</code></pre>
<ul>
<li>1.4启动步骤</li>
</ul>
<pre><code>//1. 启动flume
bin/flume-ng agent -n a1 -c conf/ -f conf/flume-push-spark.conf -Dflume.root.logger=INFO,console
//2. 启动程序
//3. 在data目录下创建单词的文件，观察控制台的变化
</code></pre>
<h1><a id="2_push_115"></a>2. push的方式</h1>
<ul>
<li>2.1 编写flume-push.conf配置文件</li>
</ul>
<pre><code>#push mode
a1.sources = r1
a1.sinks = k1
a1.channels = c1

#source 监控文件夹
a1.sources.r1.channels = c1
a1.sources.r1.type = spooldir
a1.sources.r1.spoolDir = /root/data
a1.sources.r1.fileHeader = true

#source2，监控文件
#a1.sources.r1.channels = c1
#a1.sources.r1.type = exec
#a1.sources.r1.command = tail -f /root/test.txt
#a1.sources.r1.fileHeader = true

#channel
a1.channels.c1.type =memory
a1.channels.c1.capacity = 20000
a1.channels.c1.transactionCapacity=5000

#sinks
a1.sinks.k1.channel = c1
a1.sinks.k1.type = avro
a1.sinks.k1.hostname=程序运行的IP
a1.sinks.k1.port = 8888
a1.sinks.k1.batchSize= 2000 
</code></pre>
<p>注意配置文件中指明的hostname和port是spark应用程序所在服务器的ip地址和端口。</p>
<ul>
<li>2.1 代码实现</li>
</ul>
<pre><code>import java.net.InetSocketAddress

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
}
</code></pre>
<ul>
<li>程序的执行</li>
</ul>
<pre><code>//1. 启动flume
bin/flume-ng agent -n a1 -c conf/ -f conf/flume-push-spark.conf -Dflume.root.logger=INFO,console

//2. 启动程序
//3. 监控文件夹的方式添加数据，不断在data目录添加文件
//4. 监控文件的方式添加数据，输入命令
while true;do echo hadoop hadoop spark&gt;&gt;/root/test.txt;sleep 2;done

</code></pre>
<p>输出结果可以看到控制台文件的不断递增</p>
<hr>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>