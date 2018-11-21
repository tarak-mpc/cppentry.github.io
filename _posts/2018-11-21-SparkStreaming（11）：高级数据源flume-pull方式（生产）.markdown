---
layout:     post
title:      SparkStreaming（11）：高级数据源flume-pull方式（生产）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1>1.环境</h1>

<h3>（1）生产环境</h3>

<p>flume1.6.0</p>

<p>spark2.1.0</p>

<h3>（2）下载对应依赖</h3>

<p>备注：一定要将依赖都放入flume的Flume’s classpath内，否则flume运行有问题。（遇到过坑~~~）</p>

<pre class="has">
<code>(i) Custom sink JAR:

 groupId = org.apache.spark
 artifactId = spark-streaming-flume-sink_2.11
 version = 2.1.0

(ii) Scala library JAR:

 groupId = org.scala-lang
 artifactId = scala-library
 version = 2.11.7

(iii) Commons Lang 3 JAR: 

 groupId = org.apache.commons
 artifactId = commons-lang3
 version = 3.5</code></pre>

<p> </p>

<h1>2.fluem的配置文件flume_pull_streaming.conf</h1>

<pre class="has">
<code>simple-agent.sources = netcat-source
simple-agent.sinks = spark-sink
simple-agent.channels = memory-channel

simple-agent.sources.netcat-source.type = netcat
simple-agent.sources.netcat-source.bind = bigdata.ibeifeng.com
simple-agent.sources.netcat-source.port = 44444

simple-agent.sinks.spark-sink.type = org.apache.spark.streaming.flume.sink.SparkSink
simple-agent.sinks.spark-sink.hostname =  bigdata.ibeifeng.com
simple-agent.sinks.spark-sink.port = 41414

simple-agent.channels.memory-channel.type = memory

simple-agent.sources.netcat-source.channels = memory-channel
simple-agent.sinks.spark-sink.channel = memory-channel</code></pre>

<h1>3.scala代码</h1>

<pre class="has">
<code>package Spark

import org.apache.spark.SparkConf
import org.apache.spark.streaming.flume.FlumeUtils
import org.apache.spark.streaming.{Seconds, StreamingContext}

/**
  * sparkstreaming 整合flume的第二种方式
  */
object FlumePullWordCount_product_server {
  def main(args: Array[String]): Unit = {

    //实际生产使用
    if(args.length!=2){
      System.err.println("Usage:FlumePullWordCount_product &lt;hostname&gt;&lt;port&gt;")
      System.exit(1)
    }

    val Array(hostname,port)=args

    var sparkConf=new SparkConf() //.setMaster("local[2]").setAppName("FlumePullWordCount_product")
    val ssc=new StreamingContext(sparkConf,Seconds(5))

    //TODO:如何使用Sparkfluming 整合flume
    val flumeStream= FlumeUtils.createPollingStream(ssc,hostname,port.toInt)

    flumeStream.map(x=&gt;new String(x.event.getBody.array()).trim)
      .flatMap(_.split(" ")).map((_,1)).reduceByKey(_+_).print()

    ssc.start()
    ssc.awaitTermination()

  }

}
</code></pre>

<h1><br>
4.测试</h1>

<h3>（1）将代码打包</h3>

<h3>（2）启动flume</h3>

<pre class="has">
<code>	bin/flume-ng agent  \
	--name simple-agent \
	--conf conf \
	--conf-file conf/flume_pull_streaming.conf \
	-Dflume.root.logger=INFO,console</code></pre>

<h3>（3）启动telnet</h3>

<pre class="has">
<code>telnet bigdata.ibeifeng.com 44444</code></pre>

<h3>（4）开启hdfs（如不开启，会报错）</h3>

<h3>（5）提交spark任务</h3>

<pre class="has">
<code>	bin/spark-submit \
	--class Spark.FlumePullWordCount_product_server \
	--master local[2] \
	--packages org.apache.spark:spark-streaming-flume_2.11:2.1.0 \
	/opt/datas/lib/scalaProjectMaven.jar \
	bigdata.ibeifeng.com 41414</code></pre>

<h3>（6）telnet测试输入</h3>

<pre class="has">
<code>	OK
	s d f s 
	OK
	sd  fd f
	OK</code></pre>

<p>（结果，成功！）</p>            </div>
                </div>