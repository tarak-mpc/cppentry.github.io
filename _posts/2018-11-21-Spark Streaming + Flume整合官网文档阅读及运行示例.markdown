---
layout:     post
title:      Spark Streaming + Flume整合官网文档阅读及运行示例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/dabokele/article/details/48681397				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
1,基于Flume的Push模式(<span style="line-height:1.571428em;">Flume</span>-style Push-based Approach)</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
     Flume被用于在Flume agents之间推送数据.在这种方式下,<span style="line-height:1.571428em;">Spark</span> Streaming可以很方便的建立一个receiver,起到一个Avro agent的作用.Flume可以将数据推送到改receiver.</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
1),需求</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
从集群中选择一台机器,</div>
<ul style="border:0px;line-height:1.571428em;list-style-position:outside;color:rgb(56,56,56);font-family:gotham, helvetica, arial, sans-serif;font-size:14px;"><li style="border:0px;line-height:1.571428em;">当Flume+<span style="line-height:1.571428em;">Spark</span> Streaming程序运行时,需要保证Spark的一个worker运行在同一台机器上.</li><li style="border:0px;line-height:1.571428em;">Flume可以通过配置文件指定推送到该台机器的一个端口.<br clear="none"></li></ul><div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
    因为在push模式中,streaming程序在运行时,Flume需要通过配置的端口号监听该机器上的receiver,这样Flume才能进行数据推送</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
2)配置Flume</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
    配置Flume agent用于发送数据到一个Avro sink,使用如下配置文件:</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
    
<table border="1" width="100%" cellspacing="0" cellpadding="2" style="line-height:1.571428em;font-size:1em;border-collapse:collapse;table-layout:fixed;"><tbody style="line-height:1.571428em;"><tr style="line-height:1.571428em;"><td colspan="1" rowspan="1" valign="top" style="line-height:1.571428em;vertical-align:top;border:1px solid rgb(217,217,217);">
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;">
agent.sinks = avroSink</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;">
agent.sinks.avroSink.type = avro</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;">
<span style="line-height:1.571428em;">agent.sinks.avroSink.channel = memoryChannel</span></div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;">
<span style="line-height:1.571428em;">agent.sinks.avroSink.hostname = &lt;选择机器的ip地址&gt;</span></div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;">
<span style="line-height:1.571428em;">agent.sinks.avroSink.port = &lt;选择机器的端口号&gt;</span></div>
</td>
</tr></tbody></table><div style="border:0px;line-height:1.571428em;">     可以参考<span style="line-height:1.571428em;"> </span><a href="https://flume.apache.org/documentation.html" rel="nofollow" style="border:0px;line-height:1.571428em;color:rgb(4,122,198);"><span style="line-height:1.571428em;"><span style="line-height:1.571428em;"><span style="line-height:1.571428em;"><span style="line-height:1.571428em;">Flume</span></span></span></span>’s
 documentation</a><span style="line-height:1.571428em;"> 获得更多的配置信息</span></div>
<div style="border:0px;line-height:1.571428em;"><span style="line-height:1.571428em;">3)配置Spark Streaming程序</span></div>
<div style="border:0px;line-height:1.571428em;"><span style="line-height:1.571428em;">    a)连接:在SBT/Maven项目中定义,通过以下配置来配置运行环境</span></div>
<div style="border:0px;line-height:1.571428em;">
<table border="1" width="100%" cellspacing="0" cellpadding="2" style="line-height:1.571428em;font-size:1em;border-collapse:collapse;table-layout:fixed;"><tbody style="line-height:1.571428em;"><tr style="line-height:1.571428em;"><td colspan="1" rowspan="1" valign="top" style="line-height:1.571428em;vertical-align:top;border:1px solid rgb(217,217,217);">
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;">
groupId = org.apache.<span style="line-height:1.571428em;">spark</span></div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;">
artifactId = <span style="line-height:1.571428em;">spark</span>-streaming-flume_2.10</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;">
version = 1.5.0</div>
</td>
</tr></tbody></table></div>
<div style="border:0px;line-height:1.571428em;">    b)编程:在程序中import FlumeUtils,创建DStream    </div>
<div style="border:0px;line-height:1.571428em;">
<table border="1" width="100%" cellspacing="0" cellpadding="2" style="line-height:1.571428em;font-size:1em;border-collapse:collapse;table-layout:fixed;"><tbody style="line-height:1.571428em;"><tr style="line-height:1.571428em;"><td colspan="1" rowspan="1" valign="top" style="line-height:1.571428em;vertical-align:top;border:1px solid rgb(217,217,217);">
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;">
import org.apache.<span style="line-height:1.571428em;">spark</span>.streaming.<span style="line-height:1.571428em;">flume</span>._</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;">
<br clear="none"></div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;">
val flumeStream = FlumeUtils.createStream(streamingContext, [选择的机器ip], [选择的机器端口号])</div>
</td>
</tr></tbody></table></div>
</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
    更多可以参考<a href="http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.streaming.flume.FlumeUtils%24" rel="nofollow" style="border:0px;line-height:1.571428em;color:rgb(4,122,198);">API
 docs</a><span style="line-height:1.571428em;"> 和 </span><a href="https://github.com/apache/spark/tree/master/examples/src/main/scala/org/apache/spark/examples/streaming/FlumeEventCount.scala" rel="nofollow" style="border:0px;line-height:1.571428em;color:rgb(4,122,198);">example</a><span style="line-height:1.571428em;">.</span></div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
<span style="line-height:1.571428em;">    需要注意的是:选择机器的ip必须与集群中resource manager中某一台机器一致.这样资源分配(resource allocation)才能匹配到这台机器,并且启动receiver.</span></div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
<span style="line-height:1.571428em;">    c)发布(运行)程序:将spark-streaming-flume_2.10以及相关依赖(除spark-core_2.10和spark-streaming_2.10之外)打包到应用程序的jar包中.然后使用spark-submit来启动应用程序(具体可以参考<a href="http://spark.apache.org/docs/latest/streaming-programming-guide.html#deploying-applications" rel="nofollow" style="border:0px;line-height:1.571428em;color:rgb(4,122,198);">Deploying
 section</a><span style="line-height:1.571428em;">)</span></span></div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
<span style="line-height:1.571428em;">4)示例</span></div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
<span style="line-height:1.571428em;">    (a)Spark程序编写</span></div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
<span style="line-height:1.571428em;">    完整程序如下:</span></div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
<span style="line-height:1.571428em;">    </span>
<table border="1" width="100%" cellspacing="0" cellpadding="2" style="line-height:1.571428em;font-size:1em;border-collapse:collapse;table-layout:fixed;"><tbody style="line-height:1.571428em;"><tr style="line-height:1.571428em;"><td colspan="1" rowspan="1" valign="top" style="line-height:1.571428em;vertical-align:top;border:1px solid rgb(217,217,217);">
object FlumeLog {<br clear="none">
def main(args: Array[String]) {<br clear="none">
StreamingExamples.setStreamingLogLevels()<br clear="none"><br clear="none">
val host = "localhost"<br clear="none">
val port = 19999<br clear="none">
val batchInterval = Milliseconds(2000)<br clear="none"><br clear="none">
// Create the context and set the batch size<br clear="none">
val sparkConf = new SparkConf().setAppName("FlumeEventCount")<br clear="none">
val ssc = new StreamingContext(sparkConf, batchInterval)<br clear="none"><br clear="none">
// Create a flume stream<br clear="none">
val stream = FlumeUtils.createStream(ssc, host, port, StorageLevel.MEMORY_ONLY_SER_2)<br clear="none"><br clear="none">
// Print out the count of events received from this server in each batch<br clear="none">
stream.count().map(cnt =&gt; "Received " + cnt + " flume events." ).print()<br clear="none"><br clear="none">
ssc.start()<br clear="none">
ssc.awaitTermination()<br clear="none">
}<br clear="none">
}</td>
</tr></tbody></table><pre style="border:0px;line-height:1.571428em;font-family:Monaco, Courier, monospace;"><br clear="none">     用于统计flume的event事件.<br clear="none">  (b)编写flume的配置文件<br clear="none">    </pre>
<table border="1" width="100%" cellspacing="0" cellpadding="2" style="line-height:1.571428em;font-size:1em;border-collapse:collapse;table-layout:fixed;"><tbody style="line-height:1.571428em;"><tr style="line-height:1.571428em;"><td colspan="1" rowspan="1" valign="top" style="line-height:1.571428em;vertical-align:top;border:1px solid rgb(217,217,217);">
a1.channels = c1<br clear="none">
     a1.sinks = k1<br clear="none">
     a1.sources = r1 <br clear="none"><br clear="none">
     a1.sinks.k1.type = avro<br clear="none">
     a1.sinks.k1.channel = c1<br clear="none">
     a1.sinks.k1.hostname = localhost<br clear="none">
     a1.sinks.k1.port = 19999 <br clear="none"><br clear="none">
     a1.sources.r1.type = exec<br clear="none">
     a1.sources.r1.command = tail -F /home/file/bigdatatest/datalake/SougouQ.data<br clear="none">
     a1.sources.r1.bind = localhost<br clear="none">
     a1.sources.r1.port = 44444<br clear="none">
     a1.sources.r1.channels = c1 <br clear="none"><br clear="none">
     a1.channels.c1.type = memory<br clear="none">
     a1.channels.c1.capacity = 1000<br clear="none">
     a1.channels.c1.transactionCapacity = 100</td>
</tr></tbody></table><pre style="border:0px;line-height:1.571428em;font-family:Monaco, Courier, monospace;"><br clear="none">其中的sinks按照文档中的进行配置.sources用于从日志文件SougouQ.data中读取数据.SougouQ.data中的数据动态生成.<br clear="none">(c)运行<br clear="none">     首先启动Spark Streaming程序,可以看到如下输出信息:<br clear="none">     <br clear="none"></pre>
</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
<span style="line-height:1.571428em;">          启动flume:/usr/local/flume140/jobconf# ../bin/<span style="line-height:1.571428em;">flume</span>-ng agent --conf ../conf/ --conf-file ./spark_avro.conf --name a1 -Dflume.root.logger=INFO,console</span></div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
<span style="line-height:1.571428em;">(d)结果,此时观察Spark程序的输出结果如下:</span></div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
<span style="line-height:1.571428em;">    <img src="https://app.yinxiang.com/shard/s22/res/861fa72a-2250-4370-a18d-fa3aa8ebc8c3/2.png?search=pull" alt="" name="861fa72a-2250-4370-a18d-fa3aa8ebc8c3" class="en-media" style="border:0px;"></span></div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
<span style="line-height:1.571428em;">    可以看到,每隔一定的间隔,就有一个Received 100 flume events的信息输出,表示spark streaming程序已从flume获取到数据.可以将Spark Streaming应用程序按照实际业务需求进行修改.</span></div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
<br clear="none"></div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
2,基于Custom Sink的Pull模式(<span class="highlight" style="line-height:1.571428em;border:1px solid rgb(98,235,146);background:rgb(201,242,208);"></span><span style="line-height:1.571428em;"><span class="highlight" style="line-height:1.571428em;border:1px solid rgb(98,235,146);background:rgb(201,242,208);">Pull</span></span>-based
 Approach using a Custom Sink)</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
     不同于Flume直接将数据推送到Spark Streaming中,第二种模式通过以下条件运行一个正常的Flume sink:</div>
<ul style="border:0px;line-height:1.571428em;list-style-position:outside;color:rgb(56,56,56);font-family:gotham, helvetica, arial, sans-serif;font-size:14px;"><li style="border:0px;line-height:1.571428em;">Flume将数据推送到sink中,并且数据保持buffered状态</li><li style="border:0px;line-height:1.571428em;"><span style="line-height:1.571428em;">Spark</span> Streaming使用一个可靠的Flume接收器(<a href="http://spark.apache.org/docs/latest/streaming-programming-guide.html#receiver-reliability" rel="nofollow" style="border:0px;line-height:1.571428em;color:rgb(4,122,198);">reliable <span style="line-height:1.571428em;"><span style="line-height:1.571428em;"><span style="line-height:1.571428em;"><span style="line-height:1.571428em;">Flume</span></span></span></span> receiver</a><span style="line-height:1.571428em;"> </span>)和转换器(transaction)从sink拉取数据.只要当数据被接收并且被Spark
 Streaming备份后,转换器才运行成功.</li></ul><div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
    这样,与第一种模式相比,保证了很好的健壮性和容错能力(<span style="line-height:1.571428em;"> </span><a href="http://spark.apache.org/docs/latest/streaming-programming-guide.html#fault-tolerance-semantics" rel="nofollow" style="border:0px;line-height:1.571428em;color:rgb(4,122,198);">fault-tolerance
 guarantees</a><span style="line-height:1.571428em;"> </span>).然而,这种模式需要为Flume配置一个正常的sink.以下为配置步骤</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
    1)基本要求</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
     选择一台运行在一个Flume agent中的普通sink节点的机器.Flume其他的pipeline配置成向该agent发送数据.Spark集群中的机器应该可以访问到选为sink节点的那台机器</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
    2)配置Flume</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
        Flume中sink节点的配置</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
(a)Sink JARS:将如下JAR包添加到Flume中被选为普通sink节点的classpath中,修改FLUME_HOME/conf/flune_env.sh文件中的FLUME_CLASSPATH=""配置信息,将以下三个jar包路径写入,或者将以下三个jar包复制到FLUME_HOME/lib/目录下</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
    包括三种类型的JAR包</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
    <span style="line-height:1.571428em;">(i) 普通的sink JAR包(下载地址 <a href="http://search.maven.org/remotecontent?filepath=org/apache/spark/spark-streaming-flume-sink_2.10/1.5.0/spark-streaming-flume-sink_2.10-1.5.0.jar" rel="nofollow" style="border:0px;line-height:1.571428em;color:rgb(4,122,198);">direct
 link</a><span style="line-height:1.571428em;">)</span>:</span></div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
<table border="1" width="100%" cellspacing="0" cellpadding="2" style="line-height:1.571428em;font-size:1em;border-collapse:collapse;table-layout:fixed;"><tbody style="line-height:1.571428em;"><tr style="line-height:1.571428em;"><td colspan="1" rowspan="1" valign="top" style="line-height:1.571428em;vertical-align:top;border:1px solid rgb(217,217,217);">
<pre style="border:0px;line-height:1.571428em;font-family:Monaco, Courier, monospace;"><code style="border:0px;line-height:1.571428em;font-family:Monaco, Courier, monospace;"> groupId = org.apache.spark
 artifactId = spark-streaming-flume-sink_2.10
 version = 1.5.0</code></pre>
</td>
</tr></tbody></table></div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
<span style="line-height:1.571428em;">     (ii) Scala运行相关JAR包(下载地址  <a href="http://search.maven.org/remotecontent?filepath=org/scala-lang/scala-library/2.10.4/scala-library-2.10.4.jar" rel="nofollow" style="border:0px;line-height:1.571428em;color:rgb(4,122,198);">direct
 link</a><span style="line-height:1.571428em;">).</span>:</span></div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
<table border="1" width="100%" cellspacing="0" cellpadding="2" style="line-height:1.571428em;font-size:1em;border-collapse:collapse;table-layout:fixed;"><tbody style="line-height:1.571428em;"><tr style="line-height:1.571428em;"><td colspan="1" rowspan="1" valign="top" style="line-height:1.571428em;vertical-align:top;border:1px solid rgb(217,217,217);">
<pre style="border:0px;line-height:1.571428em;font-family:Monaco, Courier, monospace;"><code style="border:0px;line-height:1.571428em;font-family:Monaco, Courier, monospace;"> groupId = org.scala-lang
 artifactId = scala-library
 version = 2.10.4</code></pre>
</td>
</tr></tbody></table></div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
<span style="line-height:1.571428em;">(iii)Lang 3JAR包(下载地址 <a href="http://search.maven.org/remotecontent?filepath=org/apache/commons/commons-lang3/3.3.2/commons-lang3-3.3.2.jar" rel="nofollow" style="border:0px;line-height:1.571428em;color:rgb(4,122,198);">direct
 link</a><span style="line-height:1.571428em;">)</span>:</span></div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
<table border="1" width="100%" cellspacing="0" cellpadding="2" style="line-height:1.571428em;font-size:1em;border-collapse:collapse;table-layout:fixed;"><tbody style="line-height:1.571428em;"><tr style="line-height:1.571428em;"><td colspan="1" rowspan="1" valign="top" style="line-height:1.571428em;vertical-align:top;border:1px solid rgb(217,217,217);">
<pre style="border:0px;line-height:1.571428em;font-family:Monaco, Courier, monospace;"><code style="border:0px;line-height:1.571428em;font-family:Monaco, Courier, monospace;"> groupId = org.apache.commons
 artifactId = commons-lang3
 version = 3.3.2</code></pre>
</td>
</tr></tbody></table></div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
(b)配置Flume conf文件</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
使用如下配置参数配置Flume agent用于发送数据到一个Avro sink</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
<table border="1" width="100%" cellspacing="0" cellpadding="2" style="line-height:1.571428em;font-size:1em;border-collapse:collapse;table-layout:fixed;"><tbody style="line-height:1.571428em;"><tr style="line-height:1.571428em;"><td colspan="1" rowspan="1" valign="top" style="line-height:1.571428em;vertical-align:top;border:1px solid rgb(217,217,217);">
<pre style="border:0px;line-height:1.571428em;font-family:Monaco, Courier, monospace;"><code style="border:0px;line-height:1.571428em;font-family:Monaco, Courier, monospace;">agent.sinks = spark
 agent.sinks.spark.type = org.apache.spark.streaming.flume.sink.SparkSink
 agent.sinks.spark.hostname = &lt;hostname of the local machine&gt;
 agent.sinks.spark.port = &lt;port to listen on for connection from Spark&gt;
 agent.sinks.spark.channel = memoryChannel</code></pre>
</td>
</tr></tbody></table></div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
     同样需要保证Flume upstream pipline已经配置好向运行上述sink的agent发送数据.更多Flume的配置信息可以在<a href="https://flume.apache.org/documentation.html" rel="nofollow" style="border:0px;line-height:1.571428em;color:rgb(4,122,198);">Flume’s
 documentation</a><span style="line-height:1.571428em;"> </span>中查看.</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
3)编写Spark Streaming应用程序</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
(a)连接</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
    在SBT/Maven项目定义中,需要引入spark-streaming-flume_2.10相关jar包.可以参考之前的配置</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
(b)编程</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
    导入FLumeUtils创建一个flumeStream</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
<table border="1" width="100%" cellspacing="0" cellpadding="2" style="line-height:1.571428em;font-size:1em;border-collapse:collapse;table-layout:fixed;"><tbody style="line-height:1.571428em;"><tr style="line-height:1.571428em;"><td colspan="1" rowspan="1" valign="top" style="line-height:1.571428em;vertical-align:top;border:1px solid rgb(217,217,217);">
<pre style="border:0px;line-height:1.571428em;font-family:Monaco, Courier, monospace;"><code style="border:0px;line-height:1.571428em;font-family:Monaco, Courier, monospace;"> import org.apache.spark.streaming.flume._

 val flumeStream = FlumeUtils.createPollingStream(streamingContext, [sink machine hostname], [sink port])</code></pre>
</td>
</tr></tbody></table><div style="border:0px;line-height:1.571428em;">可以查看示例程序<a href="https://github.com/apache/spark/tree/master/examples/src/main/scala/org/apache/spark/examples/streaming/FlumePollingEventCount.scala" rel="nofollow" style="border:0px;line-height:1.571428em;color:rgb(4,122,198);">FlumePollingEventCount</a><span style="line-height:1.571428em;">.</span></div>
<div style="border:0px;line-height:1.571428em;">    注意:每个input Dstream可以接收从多个sinks中的数据</div>
</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
(c)发布(运行)</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
    <span style="line-height:1.571428em;">将spark-streaming-flume_2.10以及相关依赖(除spark-core_2.10和spark-streaming_2.10之外)打包到应用程序的jar包中.然后使用spark-submit来启动应用程序(具体可以参考</span><a href="http://spark.apache.org/docs/latest/streaming-programming-guide.html#deploying-applications" rel="nofollow" style="border:0px;line-height:1.571428em;color:rgb(4,122,198);">Deploying
 section</a><span style="line-height:1.571428em;">)</span></div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
<span style="line-height:1.571428em;">4)示例</span></div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
<span style="line-height:1.571428em;">(a)Spark程序:</span></div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
<table border="1" width="100%" cellspacing="0" cellpadding="2" style="line-height:1.571428em;font-size:1em;border-collapse:collapse;table-layout:fixed;"><tbody style="line-height:1.571428em;"><tr style="line-height:1.571428em;"><td colspan="1" rowspan="1" valign="top" style="line-height:1.571428em;vertical-align:top;border:1px solid rgb(217,217,217);">
<pre style="border:0px;line-height:1.571428em;font-family:Monaco, Courier, monospace;"><span style="line-height:1.571428em;">object </span>FlumeLogPull {<br clear="none"><span style="line-height:1.571428em;">def </span>main(args: Array[<span style="line-height:1.571428em;">String</span>]) {<br clear="none">     StreamingExamples.<span style="line-height:1.571428em;">setStreamingLogLevels</span>()<br clear="none"><br clear="none"><span style="line-height:1.571428em;">val </span>host = <span style="line-height:1.571428em;">"localhost"<br clear="none"></span><span style="line-height:1.571428em;">     </span><span style="line-height:1.571428em;">val </span>port = <span style="line-height:1.571428em;">19999<br clear="none"></span><span style="line-height:1.571428em;">     </span><span style="line-height:1.571428em;">val </span>batchInterval = <span style="line-height:1.571428em;">Milliseconds</span>(<span style="line-height:1.571428em;">2000</span>)<br clear="none"><br clear="none"><span style="line-height:1.571428em;">// Create the context and set the batch size<br clear="none"></span><span style="line-height:1.571428em;">     </span><span style="line-height:1.571428em;">val </span>sparkConf = <span style="line-height:1.571428em;">new </span>SparkConf().setAppName(<span style="line-height:1.571428em;">"FlumeEventCount"</span>)<br clear="none"><span style="line-height:1.571428em;">val </span>ssc = <span style="line-height:1.571428em;">new </span>StreamingContext(sparkConf, batchInterval)<br clear="none"><br clear="none"><span style="line-height:1.571428em;">// Create a flume stream<br clear="none"></span><span style="line-height:1.571428em;">     </span><span style="line-height:1.571428em;">val </span>stream = FlumeUtils.<span style="line-height:1.571428em;">createPollingStream</span>(ssc, host, port, StorageLevel.<span style="line-height:1.571428em;">MEMORY_ONLY_SER_2</span>)<br clear="none"><br clear="none"><span style="line-height:1.571428em;">// Print out the count of events received from this server in each batch<br clear="none"></span><span style="line-height:1.571428em;">     </span>stream.count().map(cnt =&gt; <span style="line-height:1.571428em;">"Received " </span>+ cnt + <span style="line-height:1.571428em;">" flume events." </span>).print()<br clear="none"><br clear="none">     ssc.start()<br clear="none">     ssc.awaitTermination()<br clear="none">   }<br clear="none"> }</pre>
</td>
</tr></tbody></table><div style="border:0px;line-height:1.571428em;">可以看到与之前一个相比,只是把<span style="line-height:1.571428em;">val stream = FlumeUtils.createStream(ssc, host, port, StorageLevel.MEMORY_ONLY_SER_2)改成了val stream = FlumeUtils.createPollingStream(ssc,
 host, port, StorageLevel.MEMORY_ONLY_SER_2)</span></div>
</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
(b)配置Flume</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
    
<table border="1" width="100%" cellspacing="0" cellpadding="2" style="line-height:1.571428em;font-size:1em;border-collapse:collapse;table-layout:fixed;"><tbody style="line-height:1.571428em;"><tr style="line-height:1.571428em;"><td colspan="1" rowspan="1" valign="top" style="line-height:1.571428em;vertical-align:top;border:1px solid rgb(217,217,217);">
a1.channels = c1<br clear="none">
a1.sinks = k1<br clear="none">
a1.sources = r1
<p style="border:0px;line-height:1.571428em;">
<br clear="none"></p>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;">
a1.sinks.k1.type = org.apache.spark.streaming.flume.sink.SparkSink<br clear="none">
a1.sinks.k1.channel = c1<br clear="none">
a1.sinks.k1.hostname = localhost<br clear="none">
a1.sinks.k1.port = 19999
<p style="border:0px;line-height:1.571428em;">
<br clear="none"></p>
<div style="border:0px;line-height:1.571428em;">a1.sources.r1.type = exec<br clear="none">
a1.sources.r1.command = tail -F /home/file/bigdatatest/datalake/SougouQ.data<br clear="none">
a1.sources.r1.bind = localhost<br clear="none">
a1.sources.r1.port = 44444<br clear="none">
a1.sources.r1.channels = c1
<p style="border:0px;line-height:1.571428em;">
<br clear="none"></p>
<div style="border:0px;line-height:1.571428em;">a1.channels.c1.type = memory<br clear="none">
a1.channels.c1.capacity = 100000<br clear="none">
a1.channels.c1.transactionCapacity = 100000</div>
</div>
</div>
</td>
</tr></tbody></table></div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
(c)运行</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
    需要先启动flume,再启动spark程序</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
启动flume:/usr/local/flume140/jobconf# ../bin/flume-ng agent --conf ../conf/ --conf-file ./spark_flume_log_pull.conf --name a1 -Dflume.root.logger=INFO,console<br clear="none"></div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
(d)结果</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
<img src="https://app.yinxiang.com/shard/s22/res/bf66c777-ab8a-4263-9c93-c3587e5acd76/1.png?search=pull" alt="" name="bf66c777-ab8a-4263-9c93-c3587e5acd76" class="en-media" style="border:0px;"></div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
比较启动顺序:</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
    在push模式中,先启动spark application,进入等待状态,等待flume push数据,此时启动flume进行数据的传递.</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
    在pull模式中,spark application会从配置的端口pull数据,此时若flume还未启动,spark application会提示端口连接失败.所以需要先启动flume后启动spark application</div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
<br clear="none"></div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
<br clear="none"></div>
<div style="border:0px;line-height:1.571428em;font-family:gotham, helvetica, arial, sans-serif;font-size:14px;color:rgb(56,56,56);">
<br></div>
<div><br></div>
            </div>
                </div>