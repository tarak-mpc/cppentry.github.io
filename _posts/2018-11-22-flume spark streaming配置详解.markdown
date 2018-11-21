---
layout:     post
title:      flume spark streaming配置详解
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u011018780/article/details/47784045				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p class="pre"><span style="font-family:'宋体';color:rgb(0,0,255);letter-spacing:0pt;font-size:13.5pt;">      Apache Flume<span style="font-family:'宋体';">是一个用来有效地收集，聚集和移动大量日志数据的分布式的，有效的服务。这里我们解释一下怎样配置</span><span style="font-family:Arial;">Flume</span><span style="font-family:'宋体';">和</span><span style="font-family:Arial;">Spark Streaming</span><span style="font-family:'宋体';">来从</span><span style="font-family:Arial;">Flume</span><span style="font-family:'宋体';">获取数据</span></span><span style="font-family:'宋体';color:rgb(0,0,255);letter-spacing:0pt;font-size:13.5pt;">，我们让</span><span style="font-family:'宋体';color:rgb(0,0,255);letter-spacing:0pt;font-size:13.5pt;">flume<span style="font-family:'宋体';">直接将收集到的数据发送到</span><span style="font-family:Arial;">spark streaming</span><span style="font-family:'宋体';">去处理</span></span><span style="font-family:'宋体';color:rgb(0,0,255);letter-spacing:0pt;font-size:13.5pt;"></span></p>
<p class="pre"><span style="font-family:'宋体';letter-spacing:0pt;font-size:13.5pt;"><span style="font-family:'宋体';"></span></span></p><pre><code class="language-java">package com.pinganfu.flumespark
import org.apache.spark.SparkConf
import org.apache.spark.storage.StorageLevel
import org.apache.spark.streaming.flume.FlumeUtils
import org.apache.spark.streaming.{Milliseconds, StreamingContext}

object SparkFlumeEvent{
  def main(args: Array[String]) {
    val batchInterval = Milliseconds(5000)
    val sparkConf = new SparkConf().setAppName("flumetospark").setMaster("local[2]")
    val ssc = new StreamingContext(sparkConf, batchInterval)
    val stream = FlumeUtils.createStream(ssc,"localhost",33333, StorageLevel.MEMORY_AND_DISK)
    stream.count().map(cnt =&gt; "Received " + cnt + " flume events." ).print()
    ssc.start()
    ssc.awaitTermination()
  }
}</code></pre>
<p>Flume<span style="font-family:'宋体';">配置</span></p>
<pre><code class="language-html">a1.channels = c1
a1.sinks = k1
a1.sources = r1
a1.sinks.k1.type = avro
a1.sinks.k1.channel = c1
a1.sinks.k1.hostname = localhost
a1.sinks.k1.port = 33333
 
a1.sources.r1.type = netcat
a1.sources.r1.bind = localhost
a1.sources.r1.port = 44444
a1.sources.r1.channels = c1
 
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100</code></pre>
<p>启动三个窗口</p>
<p>先启动<span style="font-family:'Times New Roman';">spark-shell</span><span style="font-family:'宋体';">（引入两个依赖包）</span></p>
<pre><code class="language-plain">bin/spark-shell  --jars lib/spark-streaming-flume_2.10-1.0.0.jar,lib/flume-ng-sdk-1.6.0.jar</code></pre>
<p>然后将上面代码贴进去</p>
<br><p>再启动<span style="font-family:'Times New Roman';">flume</span></p>
<pre><code class="language-html">bin/flume-ng agent --conf conf --conf-file conf/flume.conf --name a1 -Dflume.root.logger=INFO,console</code></pre><br><p>再启动<span style="font-family:'Times New Roman';">telnet localhost 44444</span></p>
<p>发数据</p>
截图如下：
<p class="pre"><span style="font-family:'宋体';letter-spacing:0pt;font-size:13.5pt;"><span style="font-family:'宋体';"><img src="https://img-blog.csdn.net/20150819174048074?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br><br><img src="https://img-blog.csdn.net/20150819174131144?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></span></span></p>
<p class="pre"><span style="font-family:'宋体';letter-spacing:0pt;font-size:13.5pt;"><span style="font-family:'宋体';"><img src="https://img-blog.csdn.net/20150819174214482?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></span></span></p>
            </div>
                </div>