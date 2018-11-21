---
layout:     post
title:      Spark DStream数据源之Kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/zxc123e/article/details/83347072				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3><a id="Kafka_1"></a>Kafka介绍</h3>
<p>Kafka是一种高吞吐量的分布式发布订阅消息系统，用户通过Kafka系统卡伊发布大量的消息，同时也能实时订阅消费消息<br>
Kafka可以同时满足在线实时处理和批量离线处理<br>
在大公司生态系统中，可以把Kafka作为数据交换枢纽，不同类型的分布式系统（关系数据库、NoSQL数据库、流处理系统、批处理系统等），可以统一接入到Kafka，实现和Hadoop各个组件之间的不同类型数据的实时高效交换。</p>
<h3><a id="Kafka_6"></a>Kafka的安装和准备工作</h3>
<p>关于Kafka的具体安装方法请参考网上教程，这里加上一句成功安装Kafka到"/usr/local/kafka"目录下</p>
<p>说明：本课程下载的安装文件为Kafka_2.11-0.10.2.0.tgz，前面的2.11就是该Kafka所支持的Scala版本号，后面的0.10.2.0是Kafka自身的版本号</p>
<p>打开一个终端，输入下面命令启动Zookeeper服务：</p>
<pre><code>$ cd /usr/local/kafka
$ ./bin/zookeeper-server-start.sh config/zookeeper.properties
</code></pre>
<p>千万不要关闭这个终端窗口，一旦关闭，Zookeeper服务就停止了。<br>
打开第二个终端，然后输入下面命令启动Kafka服务：</p>
<pre><code>$ cd /usr/local/kafka
$ ./bin/kafka-server-start.sh config/server.properties
</code></pre>
<p>千万不要关闭这个终端窗口，一旦关闭，kafka服务就停止了。</p>
<h3><a id="Spark_25"></a>Spark准备工作</h3>
<p>使用Kafka作为Spark输入源，Spark需要依赖独立的库（jar文件），在spark-shell中执行下面import语句进行测试：</p>
<pre><code>import org.apache.spark.streaming.kafka._
</code></pre>
<p>对于Spark2.3.0版本，如果要使用Kafka,则需要下载spark-streaming-kafka-0-8_2.11相关jar包，下载地址：<a href="http://mvnrepository.com/artifact/org.apache.spark/spark-streaming-kafka-0-8_2.11/2.3.0" rel="nofollow">http://mvnrepository.com/artifact/org.apache.spark/spark-streaming-kafka-0-8_2.11/2.3.0</a></p>
<p>把下载的jar文件复制到Spark目录的jars目录下</p>
<pre><code>$ cd /usr/local/spark/jars
$ mkdir kafka
$ cd ~
$ cd 下载
$ cp ./spark-streaming-kafka-0-8_2.11-2.1.0.jar /usr/local/spark/jars/kafka
</code></pre>
<p>继续把Kafka安装目录的libs目录下所以jar文件复制到"/usr/local/spark/jars/kafka"目录下，即再终端执行下面命令：</p>
<pre><code>$ cd /usr/local/kafka/libs
$ cp ./* /usr/local/spark/jars/kafka
</code></pre>
<h3><a id="_45"></a>编写程序</h3>
<p>编写一个Kafka词频统计功能的程序</p>
<p>编写KafkaWordProducer程序，执行命令创建代码目录：</p>
<pre><code>$ /usr/local/spark/mycode
$ mkdir kafka
$ cd kafka
$ mkdir -p src/main/scala
$ cd src/main/scala
$ vim KafkaWordProducer.scala
</code></pre>
<p>在KafkaWordProducer.scala中输入以下代码：</p>
<pre><code>package org.apache.spark.examples.streaming
improt java.util.HashMap
import org.apache.kafka.clients.producer.{KafkaProducer,ProducerConfig, ProducerRecord}
import org.apache.spark,SparkConf
import org.apache.spark.streaming._
import org.apache.spark.streaming.kafka._

object KafkaWordProducer {
	def main(args:Array[String]){
		if(args.lenght &lt; 4)
		{
			System.err.println("Usage:KafkaWordProducer &lt;metadataBrokerList&gt;&lt;topic&gt;"+"&lt;messageePerSec&gt;&lt;wordsPerMessage&gt;")
			System.exit(1)
		}
		val Array(brokers, topic, messagesPerSec, wordsPerMessage) = args
		//Zookeeper connection properties
		val props = new HashMap[String, Object]()
		props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, brokers)
		props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG,
		"org.apache.kafka.common.serialization.StringSerializer")
		props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG,
		"org.apache.kafka.common.serialization.StringSerializer")
		val producer = new KafkaProducer[String, Stgring](props)
		//Send some messages
		while(true)	{
			(1 to messagesPerSec.toInt).foreach { messageNum =&gt;
				val str = (1 to wordsPerMessage.toInt).map(x =&gt;
				scala.util.Random.nextInt(10).toString)
				.mkString(" ")
				print(str)
				println()
				val message = new ProducerRecord[String, String](topic, null,str)
				producer.send(message)
			}
			Thread.sleep(1000)
		}
	}
} 
</code></pre>
<p>继续在当前目录下创建KafkaWordCount.scala代码文件，它会把KafkaWordProducer发送过来的单词进行词频统计，代码内容如下：</p>
<pre><code>package org.apache.spark.examples.streaming
import org.apache.spark,SparkConf
import org.apache.spark.streaming._
import org.apache.spark.streaming.kafka._
import org.apache.spark.streaming.StreamingContext._
import org.apache.spark.streaming.kafka.KafkaUtils

object KafkaWordCount{
	def main(args:Array[String]){
		StreamingExamples.setStreamingLogLevels()
		val sc = new SparkConf.setAppName("KafkaWordCount").setMaster("local[2]")
		val ssc = new StreamingContext(sc, Seconds(10))
		//设置检查点，如果存放在HDFS上面，则写成类似ssc.checkpoint("/usr/hadoop/checkpoint")这种形式，hadoop要启动
		ssc.checkpoint("file:///usr/local/spark/mycode/kafka/chekcpoint")
		val zkQuorum = "localhost:2181" //Zookeeper服务器地址
		val group = "1" //topic所在的group，可以设置为自己想要的名称，比如不用1，而是val group = "test-consumer-group"
		val topics = "wordsender" //topics的名称
		val numThreads = 1 //每个topic的名称
		val topicMap = topics.split(",").map((_,numThreads.toInt)).toMap
		val lineMap = KafkaUtils.createStream(ssc，zkQuorum, group, topicMap)
		val lines = lineMap.map(_._2)
		val words = lines.flaMap(_split(" "))
		val pair = words.map(x=&gt;(x,1))
		val wordCounts = pair.reduceByKeyAndWindow(_+_,_-_,Minutes(2),Seconds(10),2) //窗口转换操作，每隔10秒将最近2分钟的数据作为一个窗口，最后参数2表示任务数
		wordCounts.print
		ssc.start
		ssc.awaitTermination
	}
	
}
</code></pre>
<p>Spark Streaming 提供了滑动窗口的操作的支持，从而让我们可以对一个滑动窗口内的数据执行计算操作。每次落在窗口里面的RDD 数据，会被集合起来，然后生成新的RDD 会作为windows DStream 的一个RDD 。<a href="https://blog.csdn.net/u014384314/article/details/82620057" rel="nofollow">参考文章</a></p>
<p>继续在当前目录下创建StreamingExamples.scala代码文件，用于设置log4j:</p>
<pre><code>package org.apacke.spark.exmaples.streaming
import org.apache.spark.internal.Logging
import org.apache.log4j.{Level, Logger}
/** Utility functions for Spark Streaming examples. */ 
object StreamingExamples extends Logging {
	/** Set reasonable logging levels for streaming if the user has not configured log4j. */
	def setStreamingLogLevels() {
		val log4jInitialized = Logger.getRootLogger.getAllAppenders.hasMoreElements
		if(!log4jInitialized){
			//We first log something to initialize Spark's default loggin, then we override the logging level
			logInfo("Setting log level to [WARN] for steaming exmaple."+" To override add a custom log4j.properties to the classpath.")
			Logger.getRootLogger.setLevel(Level.WARN)		
		}
	}
}
</code></pre>
<p>创建simple.sbt文件</p>
<pre><code>$ cd /usr/local/spark/mycode/kafka/
$ vim simple.sbt
</code></pre>
<p>在simple.sbt中输入以下代码：</p>
<pre><code>name:="Simple Project"
version:="1.0"
scalaVersion:="2.11.8"
libraryDependencies += "org.apache.spark" %% "spark-core" % "2.3.0"
libraryDependencies += "org.apache.spark" % "spark-streaming_2.11" % "2.3.0"
libraryDependencies += "org.apache.spark" % "spark-streaming-kafka-0-8_2.11" % "2.3.0"
</code></pre>
<p>打开一个终端，执行如下命令，运行"KafkaWordProducer"程序，生成一些单词（是一堆整数形式的单词）：</p>
<pre><code>$ cd /usr/local/spark
$ /usr/local/spark/bin/spark-submit --driver-class-path /usr/local/spark/jars/*:/usr/local/spark/jars/kafka/* --class "org.apache.spark.examples.streaming.KafakWordProducer" /usr/local/spark/mycode/kafka/target/scala-2.11/simple-project_2.11-1.0.jar localhost:9092 wordsender 3 5
</code></pre>
<p>执行上面命令后，屏幕上就会不断滚动出现新的单词，不要关闭这个终端窗口，，让它一直不断发送单词。打开另一个终端，执行下面命令，运行KafkaWordCount程序，执行词频统计：</p>
<pre><code>$ cd /usr/local/spark
$ /usr/local/spark/bin/spark-submit --driver-class-path /usr/local/spark/jars/*:/usr/local/spark/jars/kafka/* --class "org.apache.spark.examples.streaming.KafakWordCount" /usr/local/spark/mycode/kafka/target/scala-2.11/simple-project_2.11-1.0.jar
</code></pre>
<p>运行上面命令以后，就启动了词频统计功能。</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>