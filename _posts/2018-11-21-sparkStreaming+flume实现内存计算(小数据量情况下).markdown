---
layout:     post
title:      sparkStreaming+flume实现内存计算(小数据量情况下)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>架构分析sparkStreaming一般结合kafka使用,但是如果你的数据量比较小,就可以不用搭建kafka集群,那么flume提供了两种提供数据给sparkStreaming的方式一种是push,一种是Pull,Pull是sparkStreaming向flume拉取数据效果更好一些.因为push只能提供数据给一个spark,而Pull可以从多个flume进行拉取<br>
1安装flume到服务器<br>
上传apache-flume-1.6.0-bin.tar.gz到服务器<br>
解压缩<br>
tar -zxf apache-flume-1.6.0-bin.tar.gz<br>
改名<br>
mv apache-flume-1.6.0-bin flume<br>
2安装JDK<br>
请参考我的另一篇博客(<a href="https://blog.csdn.net/qq_16563637/article/details/81738113" rel="nofollow">https://blog.csdn.net/qq_16563637/article/details/81738113</a>)<br>
2修改配置文件(重点)<br>
cd flume/conf/<br>
vi flume-pull.conf</p>
<pre><code># Name the components on this agent
a1.sources = r1
a1.sinks = k1
a1.channels = c1

# source
a1.sources.r1.type = spooldir
a1.sources.r1.spoolDir = /export/data/flume
a1.sources.r1.fileHeader = true

# Describe the sink
a1.sinks.k1.type = org.apache.spark.streaming.flume.sink.SparkSink
#一定要写成flume所在那台机器的地址
a1.sinks.k1.hostname = 192.168.1.103
a1.sinks.k1.port = 8888

# Use a channel which buffers events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
</code></pre>
<p>保存<br>
或者采用push方法<br>
vi flume-push.conf</p>
<pre><code># Name the components on this agent
a1.sources = r1
a1.sinks = k1
a1.channels = c1

# source
a1.sources.r1.type = spooldir
a1.sources.r1.spoolDir = /export/data/flume
a1.sources.r1.fileHeader = true

# Describe the sink
a1.sinks.k1.type = avro
#这是接收方这里要写成worker所在的IP地址
a1.sinks.k1.hostname = 192.168.31.172
a1.sinks.k1.port = 8888

# Use a channel which buffers events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
</code></pre>
<p>保存<br>
3(重要)<br>
spark-streaming-flume-sink_2.10-1.6.1.jar复制到flume的lib目录<br>
commons-lang3-3.3.2.jar复制到flume的lib目录<br>
scala-library-2.10.5.jar复制到flume的lib目录<br>
4设置flume的JAVA_HOME<br>
vi <a href="http://flume-env.sh" rel="nofollow">flume-env.sh</a><br>
export JAVA_HOME=/usr/local/jdk/jdk1.8.0_161<br>
保存<br>
5先启动flume<br>
bin/flume-ng agent --conf conf --conf-file conf/flume-pull.conf --name a1 -Dflume.root.logger=INFO,console<br>
6在本地启动程序</p>
<pre><code>package cn.itcast.spark.day5

import java.net.InetSocketAddress

import org.apache.spark.SparkConf
import org.apache.spark.storage.StorageLevel
import org.apache.spark.streaming.flume.FlumeUtils
import org.apache.spark.streaming.{Seconds, StreamingContext}
//flume 执行语句
//bin/flume-ng agent --conf conf --conf-file conf/flume-pull.conf --name a1 -Dflume.root.logger=INFO,console
object FlumePollWordCount {
  def main(args: Array[String]) {
    //设置日志级别
    LoggerLevels.setStreamingLogLevels()

    val conf = new SparkConf().setAppName("FlumePollWordCount").setMaster("local[2]")
    val ssc = new StreamingContext(conf, Seconds(5))
    //从flume中拉取数据(flume的地址)
    val address = Seq(new InetSocketAddress("192.168.1.103", 8888))
    val flumeStream = FlumeUtils.createPollingStream(ssc, address, StorageLevel.MEMORY_AND_DISK)
    val words = flumeStream.flatMap(x =&gt; new String(x.event.getBody().array()).split(" ")).map((_,1))
    val results = words.reduceByKey(_+_)
    results.print()
    ssc.start()
    ssc.awaitTermination()
  }
}
</code></pre>
<p>7在服务器上创建一个文件test.txt<br>
vi test.txt<br>
zhangsan is holle<br>
lisi is hello<br>
保存<br>
cp test.txt /export/data/flume/test.txt<br>
查看控制台输出<br>
完毕</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>