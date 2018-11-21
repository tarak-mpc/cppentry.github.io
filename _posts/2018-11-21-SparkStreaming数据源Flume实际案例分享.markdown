---
layout:     post
title:      SparkStreaming数据源Flume实际案例分享
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>本期内容：</p>
<p>1.Spark Streaming on polling from Flume<span style="font-family:'宋体';">实战</span></p>
<p>2.Spark Streaming on polling from<span style="font-family:SimSun;font-size:18px;"><strong style="background-color:rgb(255,255,255);">
</strong></span>Flume<span style="font-family:'宋体';">源码</span></p>
<p>FlumeConnection:<span style="font-family:'宋体';">分布式连接的</span><span style="font-family:Calibri;">Flume</span><span style="font-family:'宋体';">实体</span></p>
<p>I.<span style="font-family:'宋体';">实战</span></p>
<p>一．通过<span style="font-family:Calibri;">Spark Streaming</span><span style="font-family:'宋体';">主动从</span><span style="font-family:Calibri;">Flume</span><span style="font-family:'宋体';">这边获取数据，首先配置</span><span style="font-family:Calibri;">Flume-config</span><span style="font-family:'宋体';">配置文件</span></p>
<p></p>
<p>二．编写源代码<span style="font-family:Calibri;">SparkStreamingPullFlume.java</span></p>
<p><span style="font-family:Calibri;">/*<span style="font-family:Calibri;"><span style="color:#ff0000;">DT大数据梦工厂微信公众号DT_Spar</span>k </span></span></p>
<p><span style="font-family:Calibri;">*/</span></p>
<p>package com.dt.spark.SparkApps.SparkStreaming;</p>
<p>import java.util.Arrays;</p>
<p>import org.apache.spark.SparkConf;</p>
<p>import org.apache.spark.api.java.function.FlatMapFunction;</p>
<p>import org.apache.spark.api.java.function.Function2;</p>
<p>import org.apache.spark.api.java.function.PairFunction;</p>
<p>import org.apache.spark.streaming.Duration;</p>
<p>import org.apache.spark.streaming.Durations;</p>
<p>import org.apache.spark.streaming.api.java.JavaDStream;</p>
<p>import org.apache.spark.streaming.api.java.JavaPairDStream;</p>
<p>import org.apache.spark.streaming.api.java.JavaReceiverInputDStream;</p>
<p>import org.apache.spark.streaming.api.java.JavaStreamingContext;</p>
<p>import org.apache.spark.streaming.api.java.JavaStreamingContextFactory;</p>
<p>import org.apache.spark.streaming.flume.FlumeUtils;</p>
<p>import org.apache.spark.streaming.flume.SparkFlumeEvent;</p>
<p>import scala.Tuple2;</p>
<p>public class SparkStreamingPullFlume {</p>
<p>public static void main(String[] args) {</p>
<p>final SparkConf conf = new SparkConf().setMaster("local[4]").setAppName("FlumePushDate2SparkStreaming");</p>
<p>JavaStreamingContext jsc = new JavaStreamingContext(conf, Durations.seconds(30));</p>
<p>     JavaReceiverInputDStream&lt;SparkFlumeEvent&gt; lines = FlumeUtils.createPollingStream(jsc, "Master", 9999);</p>
<p>JavaDStream&lt;String&gt; words = lines.flatMap(new FlatMapFunction&lt;SparkFlumeEvent, String&gt;() {
</p>
<p>private static final long serialVersionUID = 1L;</p>
<p>public Iterable&lt;String&gt; call(SparkFlumeEvent arg0) throws Exception {</p>
<p>String line = new String(arg0.event().getBody().array());</p>
<p>return Arrays.asList(line.split(" "));</p>
<p>} </p>
<p>});</p>
<p>JavaPairDStream&lt;String, Integer&gt; pairs = words.mapToPair(new PairFunction&lt;String, String, Integer&gt;() {</p>
<p>public Tuple2&lt;String, Integer&gt; call(String arg0) throws Exception {</p>
<p>return new Tuple2&lt;String, Integer&gt;(arg0, 1);</p>
<p>}</p>
<p>});</p>
<p>/*<span style="font-family:Calibri;"><span style="color:#3366ff;">IMF晚8点大数据实战YY直播频道号：68917580</span></span>*/</p>
<p>JavaPairDStream&lt;String, Integer&gt; wordsCount = pairs.reduceByKey(new Function2&lt;Integer, Integer, Integer&gt;() {
</p>
<p>public Integer call(Integer v1, Integer v2) throws Exception {</p>
<p>return v1 + v2;</p>
<p>}</p>
<p>});</p>
<p>wordsCount.print();</p>
<p>jsc.start();</p>
<p>jsc.awaitTermination();</p>
<p>jsc.close();</p>
<p>}</p>
<p>}</p>
<p>三．安装三个<span style="font-family:Calibri;">jar</span><span style="font-family:'宋体';">包到</span><span style="font-family:Calibri;">flume</span><span style="font-family:'宋体';">的</span><span style="font-family:Calibri;">lib</span><span style="font-family:'宋体';">目录下</span><br>
commons-lang3-3.3.2.jar,scala-library-2.10.4.jar,spark-streaming-flume-sink_2.10-1.6.1.jar</p>
<p> </p>
<p>四．运行<span style="font-family:Calibri;">eclipse</span><span style="font-family:'宋体';">中的程序</span><span style="font-family:Calibri;">SparkStreamingPullFlume.java</span></p>
<p><span style="font-family:Calibri;"><img src="https://img-blog.csdn.net/20160502073121262?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="font-family:Calibri;"></span></p>
<p>五．运行<span style="font-family:Calibri;">flume-ng</span></p>
<p>./flume-ng agent -n agent1 -c conf -f/usr/local/flume/apache-flume-1.6.0-bin/conf/flume-conf.properties -Dflume.root.logger=DEBUG,console</p>
<p>六.将<span style="font-family:Calibri;">hello.txt</span><span style="font-family:'宋体';">拷贝到</span><span style="font-family:Calibri;">/usr/local/flume/apache-flume-1.6.0-bin/tmp/TestDir/</span><span style="font-family:'宋体';">下面</span></p>
<p><span style="font-family:'宋体';"></span></p>
<p>Hello Spark</p>
<p>Hello Hadoop</p>
<p>Hello Kafka</p>
<p>Hello HDFS</p>
<p>II.<span style="font-family:'宋体';">源代码分析</span></p>
<p>一.创建<span style="font-family:Calibri;">createPollingStre</span></p>
<img src="https://img-blog.csdn.net/20160502073425843?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><p><span style="font-family:'宋体';"><img src="https://img-blog.csdn.net/20160502073443878?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p>继承自<span style="font-family:Calibri;">ReceiverInputDstream</span><span style="font-family:'宋体';">中覆写</span><span style="font-family:Calibri;">getReciver</span><span style="font-family:'宋体';">方法，调用</span><span style="font-family:Calibri;">FlumePollingReciver</span><span style="font-family:'宋体';">接口</span></p>
<img src="https://img-blog.csdn.net/20160502073513457?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><p><span style="font-family:Calibri;"></span></p>
<p>使用<span style="font-family:Calibri;">lazy</span><span style="font-family:'宋体';">和工厂方法创建线程和</span><span style="font-family:Calibri;">Nio</span><span style="font-family:'宋体';">客户端</span><span style="font-family:Calibri;">socket</span></p>
<img src="https://img-blog.csdn.net/20160502073638018?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><img src="https://img-blog.csdn.net/20160502073638018?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><p><span style="font-family:Calibri;"></span></p>
<p>工作线程从<span style="font-family:Calibri;">Flume Polling</span><span style="font-family:'宋体';">中</span><span style="font-family:Calibri;">pull
</span><span style="font-family:'宋体';">数据，实质上是从消息队列中获取数据</span></p>
<img src="https://img-blog.csdn.net/20160502073721879?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><img src="https://img-blog.csdn.net/20160502073721879?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><p><span style="font-family:Calibri;"></span></p>
<p>看<span style="font-family:Calibri;">run()</span><span style="font-family:'宋体';">方法中的</span><span style="font-family:Calibri;">receiver.getConnections.poll()</span><span style="font-family:'宋体';">中的</span><span style="font-family:Calibri;">poll</span><span style="font-family:'宋体';">方法</span></p>
<img src="https://img-blog.csdn.net/20160502073809629?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><p><span style="font-family:Calibri;"></span></p>
<p>发现<span style="font-family:Calibri;">dequeue</span><span style="font-family:'宋体';">出消息队列</span><span style="font-family:Calibri;">:</span><span style="font-family:'宋体';">进入</span><span style="font-family:Calibri;">dequeue</span><span style="font-family:'宋体';">中观察奇妙的现象出来了发现</span><span style="font-family:Calibri;">enqueue</span><span style="font-family:'宋体';">投递消息队列中我们发现调用</span><span style="font-family:Calibri;">enqueue</span><span style="font-family:'宋体';">方法的地方</span></p>
<img src="https://img-blog.csdn.net/20160502073840614?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><p><span style="font-family:Calibri;">发现FlumePollingInputDStream.ca</span></p>
<p><span style="font-family:Calibri;color:rgb(255,255,255);">[FlumeConnectionfaa</span></p>
<p><span style="font-family:Calibri;"><img src="https://img-blog.csdn.net/20160502073923380?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="font-family:Calibri;"><br></span></p>
<p><span style="font-family:Calibri;"></span></p>
<p style="background:rgb(43,43,43);"><span style="color:rgb(255,255,255);"><img src="https://img-blog.csdn.net/20160502074006428?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<br><p><span style="font-family:Calibri;"></span></p>
<p style="background:rgb(43,43,43);"><span style="color:rgb(255,255,255);">其中</span><span style="color:rgb(255,255,255);">   </span></p>
<p style="background:rgb(43,43,43);"><span style="color:rgb(255,255,255);"> sendNack(batchReceived</span><span style="color:rgb(255,255,255);">,
</span><span style="color:rgb(255,255,255);">client</span><span style="color:rgb(255,255,255);">,
</span><span style="color:rgb(255,255,255);">seq)</span><span style="color:rgb(255,255,255);"><br></span><span style="color:rgb(255,255,255);">  }</span><span style="color:rgb(255,255,255);"><br></span><strong><span style="color:rgb(255,255,255);">case </span></strong><span style="color:rgb(255,255,255);">exception:
</span><span style="color:rgb(169,183,198);background:rgb(52,65,52);color:rgb(255,255,255);">Exception</span><span style="color:rgb(255,255,255);"> =&gt;</span><span style="color:rgb(255,255,255);"><br></span><span style="color:rgb(255,255,255);">  logWarning(</span><span style="color:rgb(255,255,255);">"Error while receiving data from Flume"</span><span style="color:rgb(255,255,255);">,
</span><span style="color:rgb(255,255,255);">exception)</span><span style="color:rgb(255,255,255);"><br></span><span style="color:rgb(255,255,255);">  sendNack(batchReceived</span><span style="color:rgb(255,255,255);">,
</span><span style="color:rgb(255,255,255);">client</span><span style="color:rgb(255,255,255);">,
</span><span style="color:rgb(255,255,255);">seq)</span></p>
<p>sendNack,sendAck<span style="font-family:'宋体';">个人理解像</span><span style="font-family:Calibri;">tcp</span><span style="font-family:'宋体';">三次握手协议一样，传输协议之际数据传输的互相约定，再回头看poll接口其实就是从消息队列中获取要处理的消息</span></p>
<p><span style="font-family:'宋体';"><img src="https://img-blog.csdn.net/20160502074742672?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p>poll<span style="font-family:'宋体';">：监听数据链接端口，获得数据后放入收入消息的线程的主消息队列中，然后分发到工作线程的消息队列中进行数据提取</span></p>
<br><span style="color:#33ff33;">新浪微博：http://www.weibo.com/ilovepains</span><br>            </div>
                </div>