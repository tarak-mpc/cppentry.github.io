---
layout:     post
title:      Spark和Flume-ng整合
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>如何将Flune-ng里面的数据发送到Spark，利用Spark进行实时的分析计算。本文将通过Java和Scala版本的程序进行程序的测试。</p>

<p>Spark和<a href="https://www.iteblog.com/archives/tag/flume/" rel="nofollow">Flume</a>-ng的整合属于Spark的Streaming这块。在讲述如何使用Spark Streaming之前，我们先来了解一下什么是Spark Streaming，在Spark官方文档是这么描述的（英文我就不翻译了，里面没有很复杂的语句）：</p>

<p>Spark Streaming is an extension of the core Spark API that allows enables high-throughput, fault-tolerant stream processing of live data streams. Data can be ingested from many sources like Kafka, Flume, Twitter, ZeroMQ or plain old TCP sockets and be processed using complex algorithms expressed with high-level functions like <em>map</em>, <em>reduce</em>, <em>join</em>and <em>window</em>. Finally, processed data can be pushed out to filesystems, databases, and live dashboards.</p>

<p>从上面的说明中我们可以看出Spark可以和Kafka、Flume、Twitter、ZeroMQ 和TCP sockets等进行整合，经过Spark处理，然后写入到filesystems、databases和live dashboards中。引用官方文档上面的一副图：</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/2018101520570250?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdXhpYW83MjM4NDY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>从上面的图片可以清楚的了解到各个模块所处的位置。这篇文章主要是讲述开发Spark Streaming这块，因为Flume-ng这块不需要特别的处理，完全和Flume-ng之间的交互一样。所有的Spark Streaming程序都是以JavaStreamingContext作为切入点的。如下：</p>

<pre class="has">
<code>JavaStreamingContext jssc = 
    new JavaStreamingContext(master, appName, 
                             new Duration(1000), 
                             [sparkHome], [jars]);
JavaDStream&lt;SparkFlumeEvent&gt; flumeStream = 
                             FlumeUtils.createStream(jssc, host, port);</code></pre>

<p>最后需要调用JavaStreamingContext的start方法来启动这个程序。如下：</p>

<pre class="has">
<code>jssc.start();
jssc.awaitTermination();</code></pre>

<p>java版整个代码如下：</p>

<pre class="has">
<code>package scala;
 
import org.apache.flume.source.avro.AvroFlumeEvent;
import org.apache.spark.api.java.JavaRDD;
import org.apache.spark.api.java.function.Function;
import org.apache.spark.api.java.function.VoidFunction;
import org.apache.spark.storage.StorageLevel;
import org.apache.spark.streaming.Duration;
import org.apache.spark.streaming.api.java.JavaDStream;
import org.apache.spark.streaming.api.java.JavaStreamingContext;
import org.apache.spark.streaming.flume.FlumeUtils;
import org.apache.spark.streaming.flume.SparkFlumeEvent;
import java.nio.ByteBuffer;
 

public static void JavaFlumeEventTest(String master, String host, int port) {
        Duration batchInterval = new Duration(2000);
 
        JavaStreamingContext ssc = new JavaStreamingContext(master, 
               "FlumeEventCount", batchInterval,
                System.getenv("SPARK_HOME"),
                JavaStreamingContext.jarOfClass(JavaFlumeEventCount.class));
        StorageLevel storageLevel = StorageLevel.MEMORY_ONLY();
        JavaDStream&lt;SparkFlumeEvent&gt; flumeStream = 
                FlumeUtils.createStream(ssc, host, port, storageLevel);
 
        flumeStream.count().map(new Function&lt;java.lang.Long, String&gt;() {
            @Override
            public String call(java.lang.Long in) {
                return "Received " + in + " flume events.";
            }
        }).print();
 
        ssc.start();
        ssc.awaitTermination();
}</code></pre>

<p>然后开启Flume往这边发数据，在Spark的这端可以接收到数据。</p>

<p>下面是一段Scala的程序：</p>

<pre class="has">
<code>def ScalaFlumeEventTest(master :　String, host : String, port : Int) {
    val batchInterval = Milliseconds(2000)
 
    val ssc = new StreamingContext(master, "FlumeEventCount", batchInterval,
      System.getenv("SPARK_HOME"), StreamingContext.jarOfClass(this.getClass))
 
    val stream = FlumeUtils.createStream(ssc, host,port,StorageLevel.MEMORY_ONLY)
 
    stream.count().map(cnt =&gt; "Received " + cnt + " flume events." ).print()
    ssc.start()
    ssc.awaitTermination()
}</code></pre>

<p>以上程序都是在Spark tandalone Mode下面运行的，如果你想在YARN上面运行，也是可以的，不过需要做点修改。具体怎么在Yarn上面运行，请参见官方文档。</p>

<p>参考：<a href="https://www.iteblog.com/archives/1063.html" rel="nofollow">https://www.iteblog.com/archives/1063.html</a></p>            </div>
                </div>