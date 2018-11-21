---
layout:     post
title:      第88讲：Spark Streaming从Flume Poll数据
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span></span></p>
<pre style="font-size:9pt;font-family:'宋体';background-color:rgb(255,255,255);"><span style="color:rgb(51,51,51);font-family:Arial, 'Microsoft YaHei';font-size:14px;line-height:14px;background-color:rgb(238,238,238);">有兴趣想学习国内整套Spark+Spark Streaming+Machine learning最</span><span style="color:rgb(51,51,51);font-family:Arial, 'Microsoft YaHei';font-size:14px;line-height:14px;background-color:rgb(238,238,238);">顶级</span><span style="color:rgb(51,51,51);font-family:Arial, 'Microsoft YaHei';font-size:14px;line-height:14px;background-color:rgb(238,238,238);">课程的，可加我qq  471186150。共享视频，性价比超高！</span></pre>
<br><p></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>本节课分成二部分讲解：</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
一、Spark Streaming on Polling from Flume实战</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
二、Spark Streaming on Polling from Flume源码</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="font-size:15px;"><span>第一部分：</span></span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>推模式(Flume push SparkStreaming) <span style="color:rgb(0,0,255);">VS</span> 拉模式（SparkStreaming poll Flume）</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>采用推模式：</span>推模式的理解就是Flume作为缓存，存有数据。监听对应端口，如果服务可以链接，就将数据push过去。(简单，耦合要低)，缺点是SparkStreaming 程序没有启动的话，Flume端会报错，同时会导致Spark Streaming 程序来不及消费的情况。</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>采用拉模式：</span>拉模式就是自己定义一个sink，SparkStreaming自己去channel里面取数据，根据自身条件去获取数据，稳定性好。</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="font-size:16px;"><span><span lang="en-us" xml:lang="en-us">Flume poll </span>实战：</span></span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>1.<span lang="en-us" xml:lang="en-us">Flume poll 配置</span></span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
进入<span lang="en-us" xml:lang="en-us">http://spark.apache.org/docs/latest/streaming-flume-integration.html官网，下载</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span lang="en-us" xml:lang="en-us"><span lang="en-us" xml:lang="en-us">spark-streaming-flume-sink_2.10-1.6.0.jar、<span lang="en-us" xml:lang="en-us">scala-library-2.10.5.jar、<span lang="en-us" xml:lang="en-us">commons-lang3-3.3.2.jar三个包：</span></span></span></span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span lang="en-us" xml:lang="en-us"><img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160430121316660-1432407016.jpg" alt="" style="border:0px;"></span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
 </p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
将下载后的三个jar包放入Flume安装lib目录：</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160430121457925-196803712.jpg" alt="" style="border:0px;"> </p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
配置Flume conf环境参数：</p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160430132751957-1778095934.jpg" alt="" style="border:0px;"></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160430132636550-194656239.jpg" alt="" style="border:0px;"></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>编写业务代码：</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
public class SparkStreamingPollDataFromFlume {</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
          public static void main(String[] args) {</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                    <span style="color:rgb(0,128,128);">/*</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     * 第一步：配置SparkConf：</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     * 1，至少2条线程：因为Spark Streaming应用程序在运行的时候，至少有一条</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     * 线程用于不断的循环接收数据，并且至少有一条线程用于处理接受的数据（否则的话无法</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     * 有线程用于处理数据，随着时间的推移，内存和磁盘都会不堪重负）；</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     * 2，对于集群而言，每个Executor一般肯定不止一个Thread，那对于处理Spark Streaming的</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     * 应用程序而言，每个Executor一般分配多少Core比较合适？根据我们过去的经验，5个左右的</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     * Core是最佳的（一个段子分配为奇数个Core表现最佳，例如3个、5个、7个Core等）；</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     */</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                    SparkConf conf = new SparkConf().setAppName("SparkStreamingPollDataFromFlume").setMaster("local[2]");</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                   <span style="color:rgb(0,128,128);"> /*</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     * 第二步：创建SparkStreamingContext：</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     * 1，这个是SparkStreaming应用程序所有功能的起始点和程序调度的核心</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     * SparkStreamingContext的构建可以基于SparkConf参数，也可基于持久化的SparkStreamingContext的内容</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     * 来恢复过来（典型的场景是Driver崩溃后重新启动，由于Spark Streaming具有连续7*24小时不间断运行的特征，</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     * 所有需要在Driver重新启动后继续上衣系的状态，此时的状态恢复需要基于曾经的Checkpoint）；</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     * 2，在一个Spark Streaming应用程序中可以创建若干个SparkStreamingContext对象，使用下一个SparkStreamingContext</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     * 之前需要把前面正在运行的SparkStreamingContext对象关闭掉，由此，我们获得一个重大的启发SparkStreaming框架也只是</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     * Spark Core上的一个应用程序而已，只不过Spark Streaming框架箱运行的话需要Spark工程师写业务逻辑处理代码；</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     */</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                    JavaStreamingContext jsc = new JavaStreamingContext(conf, Durations.seconds(30));</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                  <span style="color:rgb(0,128,128);">  /*</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     * 第三步：创建Spark Streaming输入数据来源input Stream：</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     * 1，数据输入来源可以基于File、HDFS、Flume、Kafka、Socket等</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     * 2, 在这里我们指定数据来源于网络Socket端口，Spark Streaming连接上该端口并在运行的时候一直监听该端口</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     *                  的数据（当然该端口服务首先必须存在）,并且在后续会根据业务需要不断的有数据产生(当然对于Spark Streaming</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     *                  应用程序的运行而言，有无数据其处理流程都是一样的)；</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     * 3,如果经常在每间隔5秒钟没有数据的话不断的启动空的Job其实是会造成调度资源的浪费，因为并没有数据需要发生计算，所以</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     *                 实例的企业级生成环境的代码在具体提交Job前会判断是否有数据，如果没有的话就不再提交Job；</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     */       </span>          </p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                    JavaReceiverInputDStream lines = FlumeUtils.createPollingStream(jsc, "Master", 9999);           </p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                   <span style="color:rgb(0,128,128);"> /*</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     * 第四步：接下来就像对于RDD编程一样基于DStream进行编程！！！原因是DStream是RDD产生的模板（或者说类），在Spark Streaming具体</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     * 发生计算前，其实质是把每个Batch的DStream的操作翻译成为对RDD的操作！！！</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     *对初始的DStream进行Transformation级别的处理，例如map、filter等高阶函数等的编程，来进行具体的数据计算</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">               *    第4.1步：讲每一行的字符串拆分成单个的单词</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">               */</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                    JavaDStream&lt;String&gt; words = lines.flatMap(new FlatMapFunction&lt;SparkFlumeEvent, String&gt;() {<span style="color:rgb(0,128,128);"> //如果是Scala，由于SAM转换，所以可以写成val words = lines.flatMap { line =&gt; line.split(" ")}</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                               @Override</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                               public Iterable&lt;String&gt; call(SparkFlumeEvent event) throws Exception {</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                                         String line = new String(event.event().getBody().array());</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                                         return Arrays.asList(line.split(" "));</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                               }</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                    });</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                   <span style="color:rgb(0,128,128);">  /*</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                 * 第四步：对初始的DStream进行Transformation级别的处理，例如map、filter等高阶函数等的编程，来进行具体的数据计算</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                 * 第4.2步：在单词拆分的基础上对每个单词实例计数为1，也就是word =&gt; (word, 1)</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                 */</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                    JavaPairDStream&lt;String, Integer&gt; pairs = words.mapToPair(new PairFunction&lt;String, String, Integer&gt;() {</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                               @Override</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                               public Tuple2&lt;String, Integer&gt; call(String word) throws Exception {</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                                         return new Tuple2&lt;String, Integer&gt;(word, 1);</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                               }</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                    });           </p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                    <span style="color:rgb(0,128,128);"> /*</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                 * 第四步：对初始的DStream进行Transformation级别的处理，例如map、filter等高阶函数等的编程，来进行具体的数据计算</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                 * 第4.3步：在每个单词实例计数为1基础之上统计每个单词在文件中出现的总次数</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                 */</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                    JavaPairDStream&lt;String, Integer&gt; wordsCount = pairs.reduceByKey(new Function2&lt;Integer, Integer, Integer&gt;() { <span style="color:rgb(0,128,128);">//对相同的Key，进行Value的累计（包括Local和Reducer级别同时Reduce）     </span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                               @Override</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                               public Integer call(Integer v1, Integer v2) throws Exception {</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                                         return v1 + v2;</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                               }</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                    });</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                    <span style="color:rgb(0,128,128);">/*</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     *        此处的print并不会直接出发Job的执行，因为现在的一切都是在Spark Streaming框架的控制之下的，对于Spark Streaming</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     *        而言具体是否触发真正的Job运行是基于设置的Duration时间间隔的</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     *        诸位一定要注意的是Spark Streaming应用程序要想执行具体的Job，对Dtream就必须有output Stream操作，</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     *        output Stream有很多类型的函数触发，类print、saveAsTextFile、saveAsHadoopFiles等，最为重要的一个</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     *        方法是foraeachRDD,因为Spark Streaming处理的结果一般都会放在Redis、DB、DashBoard等上面，foreachRDD</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     *        主要就是用用来完成这些功能的，而且可以随意的自定义具体数据到底放在哪里！！！</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     *</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     */</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                    wordsCount.print();</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                   <span style="color:rgb(0,128,128);"> /*</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     * Spark Streaming执行引擎也就是Driver开始运行，Driver启动的时候是位于一条新的线程中的，当然其内部有消息循环体，用于</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     * 接受应用程序本身或者Executor中的消息；</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="color:rgb(0,128,128);">                     */</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                    jsc.start();                </p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                    jsc.awaitTermination();</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                    jsc.close();</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
          }</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
启动HDFS集群：</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160430133509878-1149422752.jpg" alt="" style="border:0px;"></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
启动运行Flume：</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160430133631800-1045759499.jpg" alt="" style="border:0px;"></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160430133642488-1656913743.jpg" alt="" style="border:0px;"></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
启动eclipse下的应用程序：</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160430133940675-1074682388.jpg" alt="" style="border:0px;"></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
copy测试文件hellospark.txt到Flume flume-conf.properties配置文件中指定的/usr/local/flume/tmp/TestDir目录下：</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160430134237910-558919183.jpg" alt="" style="border:0px;"></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160430134253082-334543907.jpg" alt="" style="border:0px;"></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
隔30秒后可以在eclipse程序控制台中看到上传的文件单词统计结果。</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span style="font-size:16px;"><span>第二部分：源码分析</span></span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>1、创建createPollingStream （FlumeUtils.scala ）</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>注意</span>：默认的存储方式是<span>MEMORY_AND_DISK_SER_2</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>/**</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span> * Creates an input stream that is to be used with the Spark Sink deployed on a Flume agent.</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span> * This stream will poll the sink for data and will pull events as they are available.</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span> * This stream will use a batch size of 1000 events and run 5 threads to pull data.</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span> * <span>@param hostname </span>Address of the host on which the Spark Sink is running</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span> * <span>@param port </span>Port of the host at which the Spark Sink is listening</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span> * <span>@param storageLevel </span>Storage level to use for storing the received objects</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>*/</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>def </span>createPollingStream(</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    ssc: StreamingContext,</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    hostname: String,</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    port: Int,</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    storageLevel: StorageLevel = StorageLevel.<span>MEMORY_AND_DISK_SER_2</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>  </span>): ReceiverInputDStream[SparkFlumeEvent] = {</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
  <span>createPollingStream</span>(ssc, <span>Seq</span>(<span>new </span>InetSocketAddress(hostname, port)), storageLevel)</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
}</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>2、参数配置：默认的全局参数，private 级别配置无法修改</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>private val </span><span>DEFAULT_POLLING_PARALLELISM </span>= 5</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>private val </span><span>DEFAULT_POLLING_BATCH_SIZE </span>= 1000</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>/**</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span> * Creates an input stream that is to be used with the Spark Sink deployed on a Flume agent.</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span> * This stream will poll the sink for data and will pull events as they are available.</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span> * This stream will use a batch size of 1000 events and run 5 threads to pull data.</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span> * <span>@param addresses </span>List of InetSocketAddresses representing the hosts to connect to.</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span> * <span>@param storageLevel </span>Storage level to use for storing the received objects</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span> */</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>def </span>createPollingStream(</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    ssc: StreamingContext,</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    addresses: Seq[InetSocketAddress],</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    storageLevel: StorageLevel</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
  ): ReceiverInputDStream[SparkFlumeEvent] = {</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
  <span>createPollingStream</span>(ssc, addresses, storageLevel,</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    <span>DEFAULT_POLLING_BATCH_SIZE</span>, <span>DEFAULT_POLLING_PARALLELISM</span>)</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
}</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>3、创建FlumePollingInputDstream对象</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>/**</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span> * Creates an input stream that is to be used with the Spark Sink deployed on a Flume agent.</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span> * This stream will poll the sink for data and will pull events as they are available.</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span> * <span>@param addresses </span>List of InetSocketAddresses representing the hosts to connect to.</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span> * <span>@param maxBatchSize </span>Maximum number of events to be pulled from the Spark sink in a</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span> *                     single RPC call</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span> * <span>@param parallelism </span>Number of concurrent requests this stream should send to the sink. Note</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span> *                    that having a higher number of requests concurrently being pulled will</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span> *                    result in this stream using more threads</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span> * <span>@param storageLevel </span>Storage level to use for storing the received objects</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span> */</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>def </span>createPollingStream(</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    ssc: StreamingContext,</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    addresses: Seq[InetSocketAddress],</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    storageLevel: StorageLevel,</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    maxBatchSize: Int,</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    parallelism: Int</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
  ): ReceiverInputDStream[SparkFlumeEvent] = {</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
  <span>new </span>FlumePollingInputDStream[SparkFlumeEvent](ssc, addresses, maxBatchSize,</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    parallelism, storageLevel)</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
}</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span><span lang="en-us" xml:lang="en-us">4、继承自<span lang="en-us" xml:lang="en-us">ReceiverInputDstream并覆写<span lang="en-us" xml:lang="en-us">getReciver方法，调用<span lang="en-us" xml:lang="en-us">FlumePollingReciver接口</span></span></span></span></span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>private</span>[streaming] <span>class </span>FlumePollingInputDStream[T: ClassTag](</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    _ssc: StreamingContext,</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    <span>val </span>addresses: Seq[InetSocketAddress],</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    <span>val </span>maxBatchSize: Int,</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    <span>val </span>parallelism: Int,</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    storageLevel: StorageLevel</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
  ) <span>extends </span>ReceiverInputDStream[SparkFlumeEvent](_ssc) {</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
   <span>override def </span>getReceiver(): Receiver[SparkFlumeEvent] = {</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    <span>new </span>FlumePollingReceiver(addresses, maxBatchSize, parallelism, storageLevel)</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
  }</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
}</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span><span lang="en-us" xml:lang="en-us">5、<span lang="en-us" xml:lang="en-us">ReceiverInputDstream 构建了一个线程池，设置为后台线程；并使用<span lang="en-us" xml:lang="en-us">lazy和工厂方法创建线程和<span lang="en-us" xml:lang="en-us">NioClientSocket（<span lang="en-us" xml:lang="en-us">NioClientSocket底层使用<span lang="en-us" xml:lang="en-us">NettyServer的方式）</span></span></span></span></span></span></span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>lazy val </span><span>channelFactoryExecutor </span>=</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
  Executors.<span>newCachedThreadPool</span>(<span>new </span>ThreadFactoryBuilder().setDaemon(<span>true</span>).</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    setNameFormat("Flume Receiver Channel Thread - %d").build())</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>lazy val </span><span>channelFactory </span>=</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
  <span>new </span>NioClientSocketChannelFactory(<span>channelFactoryExecutor</span>, <span>channelFactoryExecutor</span>)</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>6、receiverExecutor 内部也是线程池；connections是指链接分布式Flume集群的FlumeConnection实体句柄的个数，线程拿到实体句柄访问数据。</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>lazy val </span><span>receiverExecutor </span>= Executors.<span>newFixedThreadPool</span>(parallelism,</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
  <span>new </span>ThreadFactoryBuilder().setDaemon(<span>true</span>).setNameFormat("Flume Receiver Thread - %d").build())</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>private lazy val </span><span>connections </span>= <span>new </span>LinkedBlockingQueue[FlumeConnection]()</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>7、启动时创建NettyTransceiver，根据并行度(默认5个)循环提交FlumeBatchFetcher</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>override def </span>onStart(): Unit = {</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
  // Create the connections to each Flume agent.</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
  addresses.foreach(host =&gt; {</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    <span>val </span>transceiver = <span>new </span>NettyTransceiver(host, <span>channelFactory</span>)</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    <span>val </span>client = SpecificRequestor.<span>getClient</span>(<span>classOf</span>[SparkFlumeProtocol.Callback], transceiver)</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    <span>connections</span>.add(<span>new </span>FlumeConnection(transceiver, client))</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
  })</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
  <span>for </span>(i &lt;- 0 until parallelism) {</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    logInfo("Starting Flume Polling Receiver worker threads..")</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    // Threads that pull data from Flume.</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    <span>receiverExecutor</span>.submit(<span>new </span>FlumeBatchFetcher(<span>this</span>))</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
  }</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
}</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>8、FlumeBatchFetcher run方法中从Receiver中获取connection链接句柄ack跟消息确认有关</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>def </span>run(): Unit = {</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
  <span>while </span>(!receiver.isStopped()) {</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    <span>val </span>connection = receiver.getConnections.poll()</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    <span>val </span>client = connection.client</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    <span>var </span>batchReceived = <span>false</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>    var </span>seq: CharSequence = <span>null</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>    try </span>{</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
      getBatch(client) <span>match </span>{</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
        <span>case </span><span>Some</span>(eventBatch) =&gt;</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
          batchReceived = <span>true</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>          </span>seq = eventBatch.getSequenceNumber</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
          <span>val </span>events = toSparkFlumeEvents(eventBatch.getEvents)</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
          <span>if </span>(store(events)) {</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
            sendAck(client, seq)</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
          } <span>else </span>{</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
            sendNack(batchReceived, client, seq)</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
          }</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
        <span>case </span>None =&gt;</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
      }</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    } <span>catch </span>{</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>9、获取一批一批数据方法</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>/**</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span> * Gets a batch of events from the specified client. This method does not handle any exceptions</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span> * which will be propogated to the caller.</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span> * <span>@param client </span>Client to get events from</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span> * <span>@return </span></span>[[<span>Some</span>]] <span>which contains the event batch if Flume sent any events back, else </span>[[<span>None</span>]]</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
 <span>*/</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>private def </span>getBatch(client: SparkFlumeProtocol.Callback): Option[EventBatch] = {</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
  <span>val </span>eventBatch = client.getEventBatch(receiver.getMaxBatchSize)</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
  <span>if </span>(!SparkSinkUtils.<span>isErrorBatch</span>(eventBatch)) {</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    // No error, proceed with processing data</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    logDebug(s"Received batch of <span>$</span>{eventBatch.getEvents.size} events with sequence " +</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
      s"number: <span>$</span>{eventBatch.getSequenceNumber}")</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    <span>Some</span>(eventBatch)</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
  } <span>else </span>{</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    logWarning("Did not receive events from Flume agent due to error on the Flume agent: " +</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
      eventBatch.getErrorMsg)</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    None</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
  }</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
}</p>
            </div>
                </div>