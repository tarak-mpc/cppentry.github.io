---
layout:     post
title:      Spark-Spark Streaming例子整理(二)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/youdianjinjin/article/details/51452682				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 style="text-align:center"><a target="_blank" href="http://blog.csdn.net/qq_21234493/article/details/51303009" rel="nofollow" style="text-decoration:none; color:rgb(0,0,0); font-family:'microsoft yahei'; font-size:18px; font-weight:bold">Spark Streaming从Flume
 Poll数据</a></h1>
<div>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
一、<a target="_blank" href="http://lib.csdn.net/base/10" rel="nofollow" class="replace_word" title="Apache Spark知识库" style="text-decoration:none; color:rgb(223,52,52); font-weight:bold">Spark</a> Streaming on Polling from Flume实战</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
二、Spark Streaming on Polling from Flume源码</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; font-size:15px">第一部分：</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">推模式(Flume push SparkStreaming) <span style="margin:0px; padding:0px; color:rgb(0,0,255)">VS</span> 拉模式（SparkStreaming poll Flume）</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">采用推模式：</span>推模式的理解就是Flume作为缓存，存有数据。监听对应端口，如果服务可以链接，就将数据push过去。(简单，耦合要低)，缺点是SparkStreaming 程序没有启动的话，Flume端会报错，同时会导致Spark Streaming 程序来不及消费的情况。</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">采用拉模式：</span>拉模式就是自己定义一个sink，SparkStreaming自己去channel里面取数据，根据自身条件去获取数据，稳定性好。</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; font-size:16px"><span lang="EN-US" style="margin:0px; padding:0px">Flume poll </span>实战：</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">1.<span lang="EN-US" style="margin:0px; padding:0px">Flume poll 配置</span></span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
进入<span lang="EN-US" style="margin:0px; padding:0px">http://spark.apache.org/docs/latest/streaming-flume-integration.html官网，下载</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span lang="EN-US" style="margin:0px; padding:0px"><span lang="EN-US" style="margin:0px; padding:0px">spark-streaming-flume-sink_2.10-1.6.0.jar、<span lang="EN-US" style="margin:0px; padding:0px">scala-library-2.10.5.jar、<span lang="EN-US" style="margin:0px; padding:0px">commons-lang3-3.3.2.jar三个包：</span></span></span></span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span lang="EN-US" style="margin:0px; padding:0px"><img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160430121316660-1432407016.jpg" alt="" style="border:0px; margin:0px; padding:0px; max-width:900px"></span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
 </p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
将下载后的三个jar包放入Flume安装lib目录：</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160430121457925-196803712.jpg" alt="" style="border:0px; max-width:900px; height:auto; margin:0px; padding:0px"> </p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
配置Flume conf环境参数：</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160430132751957-1778095934.jpg" alt="" style="border:0px; max-width:900px; height:auto; margin:0px; padding:0px"></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160430132636550-194656239.jpg" alt="" style="border:0px; max-width:900px; height:auto; margin:0px; padding:0px"></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">编写业务代码：</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
public class SparkStreamingPollDataFromFlume {</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
          public static void main(String[] args) {</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
                    <span style="margin:0px; padding:0px; color:rgb(0,128,128)">/*</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     * 第一步：配置SparkConf：</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     * 1，至少2条线程：因为Spark Streaming应用程序在运行的时候，至少有一条</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     * 线程用于不断的循环接收数据，并且至少有一条线程用于处理接受的数据（否则的话无法</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     * 有线程用于处理数据，随着时间的推移，内存和磁盘都会不堪重负）；</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     * 2，对于集群而言，每个Executor一般肯定不止一个Thread，那对于处理Spark Streaming的</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     * 应用程序而言，每个Executor一般分配多少Core比较合适？根据我们过去的经验，5个左右的</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     * Core是最佳的（一个段子分配为奇数个Core表现最佳，例如3个、5个、7个Core等）；</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     */</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
                    SparkConf conf = new SparkConf().setAppName("SparkStreamingPollDataFromFlume").setMaster("local[2]");</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
                   <span style="margin:0px; padding:0px; color:rgb(0,128,128)"> /*</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     * 第二步：创建SparkStreamingContext：</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     * 1，这个是SparkStreaming应用程序所有功能的起始点和程序调度的核心</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     * SparkStreamingContext的构建可以基于SparkConf参数，也可基于持久化的SparkStreamingContext的内容</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     * 来恢复过来（典型的场景是Driver崩溃后重新启动，由于Spark Streaming具有连续7*24小时不间断运行的特征，</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     * 所有需要在Driver重新启动后继续上衣系的状态，此时的状态恢复需要基于曾经的Checkpoint）；</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     * 2，在一个Spark Streaming应用程序中可以创建若干个SparkStreamingContext对象，使用下一个SparkStreamingContext</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     * 之前需要把前面正在运行的SparkStreamingContext对象关闭掉，由此，我们获得一个重大的启发SparkStreaming框架也只是</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     * Spark Core上的一个应用程序而已，只不过Spark Streaming框架箱运行的话需要Spark工程师写业务逻辑处理代码；</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     */</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
                    JavaStreamingContext jsc = new JavaStreamingContext(conf, Durations.seconds(30));</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
                  <span style="margin:0px; padding:0px; color:rgb(0,128,128)">  /*</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     * 第三步：创建Spark Streaming输入数据来源input Stream：</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     * 1，数据输入来源可以基于File、HDFS、Flume、Kafka、Socket等</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     * 2, 在这里我们指定数据来源于网络Socket端口，Spark Streaming连接上该端口并在运行的时候一直监听该端口</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     *                  的数据（当然该端口服务首先必须存在）,并且在后续会根据业务需要不断的有数据产生(当然对于Spark Streaming</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     *                  应用程序的运行而言，有无数据其处理流程都是一样的)；</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     * 3,如果经常在每间隔5秒钟没有数据的话不断的启动空的Job其实是会造成调度资源的浪费，因为并没有数据需要发生计算，所以</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     *                 实例的企业级生成环境的代码在具体提交Job前会判断是否有数据，如果没有的话就不再提交Job；</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     */       </span>          </p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
                    JavaReceiverInputDStream lines = FlumeUtils.createPollingStream(jsc, "Master", 9999);           </p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
                   <span style="margin:0px; padding:0px; color:rgb(0,128,128)"> /*</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     * 第四步：接下来就像对于RDD编程一样基于DStream进行编程！！！原因是DStream是RDD产生的模板（或者说类），在Spark Streaming具体</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     * 发生计算前，其实质是把每个Batch的DStream的操作翻译成为对RDD的操作！！！</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     *对初始的DStream进行Transformation级别的处理，例如map、filter等高阶函数等的编程，来进行具体的数据计算</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">               *    第4.1步：讲每一行的字符串拆分成单个的单词</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">               */</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
                    JavaDStream&lt;String&gt; words = lines.flatMap(new FlatMapFunction&lt;SparkFlumeEvent, String&gt;() {<span style="margin:0px; padding:0px; color:rgb(0,128,128)"> //如果是Scala，由于SAM转换，所以可以写成val words = lines.flatMap { line =&gt; line.split(" ")}</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
                               @Override</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
                               public Iterable&lt;String&gt; call(SparkFlumeEvent event) throws Exception {</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
                                         String line = new String(event.event().getBody().array());</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
                                         return Arrays.asList(line.split(" "));</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
                               }</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
                    });</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
                   <span style="margin:0px; padding:0px; color:rgb(0,128,128)">  /*</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                 * 第四步：对初始的DStream进行Transformation级别的处理，例如map、filter等高阶函数等的编程，来进行具体的数据计算</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                 * 第4.2步：在单词拆分的基础上对每个单词实例计数为1，也就是word =&gt; (word, 1)</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                 */</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
                    JavaPairDStream&lt;String, Integer&gt; pairs = words.mapToPair(new PairFunction&lt;String, String, Integer&gt;() {</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
                               @Override</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
                               public Tuple2&lt;String, Integer&gt; call(String word) throws Exception {</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
                                         return new Tuple2&lt;String, Integer&gt;(word, 1);</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
                               }</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
                    });           </p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
                    <span style="margin:0px; padding:0px; color:rgb(0,128,128)"> /*</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                 * 第四步：对初始的DStream进行Transformation级别的处理，例如map、filter等高阶函数等的编程，来进行具体的数据计算</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                 * 第4.3步：在每个单词实例计数为1基础之上统计每个单词在文件中出现的总次数</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                 */</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
                    JavaPairDStream&lt;String, Integer&gt; wordsCount = pairs.reduceByKey(new Function2&lt;Integer, Integer, Integer&gt;() { <span style="margin:0px; padding:0px; color:rgb(0,128,128)">//对相同的Key，进行Value的累计（包括Local和Reducer级别同时Reduce）     </span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
                               @Override</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
                               public Integer call(Integer v1, Integer v2) throws Exception {</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
                                         return v1 + v2;</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
                               }</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
                    });</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
                    <span style="margin:0px; padding:0px; color:rgb(0,128,128)">/*</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     *        此处的print并不会直接出发Job的执行，因为现在的一切都是在Spark Streaming框架的控制之下的，对于Spark Streaming</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     *        而言具体是否触发真正的Job运行是基于设置的Duration时间间隔的</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     *        诸位一定要注意的是Spark Streaming应用程序要想执行具体的Job，对Dtream就必须有output Stream操作，</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     *        output Stream有很多类型的函数触发，类print、saveAsTextFile、saveAsHadoopFiles等，最为重要的一个</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     *        方法是foraeachRDD,因为Spark Streaming处理的结果一般都会放在Redis、DB、DashBoard等上面，foreachRDD</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     *        主要就是用用来完成这些功能的，而且可以随意的自定义具体数据到底放在哪里！！！</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     *</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     */</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
                    wordsCount.print();</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
                   <span style="margin:0px; padding:0px; color:rgb(0,128,128)"> /*</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     * Spark Streaming执行引擎也就是Driver开始运行，Driver启动的时候是位于一条新的线程中的，当然其内部有消息循环体，用于</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     * 接受应用程序本身或者Executor中的消息；</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(0,128,128)">                     */</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
                    jsc.start();                </p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
                    jsc.awaitTermination();</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
                    jsc.close();</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
          }</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
启动HDFS集群：</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160430133509878-1149422752.jpg" alt="" style="border:0px; max-width:900px; height:auto; margin:0px; padding:0px"></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
启动运行Flume：</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160430133631800-1045759499.jpg" alt="" style="border:0px; max-width:900px; height:auto; margin:0px; padding:0px"></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160430133642488-1656913743.jpg" alt="" style="border:0px; max-width:900px; height:auto; margin:0px; padding:0px"></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
启动eclipse下的应用程序：</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160430133940675-1074682388.jpg" alt="" style="border:0px; max-width:900px; height:auto; margin:0px; padding:0px"></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
copy测试文件hellospark.txt到Flume flume-conf.properties配置文件中指定的/usr/local/flume/tmp/TestDir目录下：</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160430134237910-558919183.jpg" alt="" style="border:0px; max-width:900px; height:auto; margin:0px; padding:0px"></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160430134253082-334543907.jpg" alt="" style="border:0px; max-width:900px; height:auto; margin:0px; padding:0px"></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
隔30秒后可以在eclipse程序控制台中看到上传的文件单词统计结果。</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; font-size:16px">第二部分：源码分析</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">1、创建createPollingStream （FlumeUtils.scala ）</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">注意</span>：默认的存储方式是<span style="margin:0px; padding:0px">MEMORY_AND_DISK_SER_2</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">/**</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px"> * Creates an input stream that is to be used with the Spark Sink deployed on a Flume agent.</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px"> * This stream will poll the sink for data and will pull events as they are available.</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px"> * This stream will use a batch size of 1000 events and run 5 threads to pull data.</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px"> * <span style="margin:0px; padding:0px">@param hostname </span>Address of the host on which the Spark Sink is running</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px"> * <span style="margin:0px; padding:0px">@param port </span>Port of the host at which the Spark Sink is listening</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px"> * <span style="margin:0px; padding:0px">@param storageLevel </span>Storage level to use for storing the received objects</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">*/</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">def </span>createPollingStream(</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    ssc: StreamingContext,</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    hostname: String,</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    port: Int,</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    storageLevel: StorageLevel = StorageLevel.<span style="margin:0px; padding:0px">MEMORY_AND_DISK_SER_2</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">  </span>): ReceiverInputDStream[SparkFlumeEvent] = {</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
  <span style="margin:0px; padding:0px">createPollingStream</span>(ssc, <span style="margin:0px; padding:0px">Seq</span>(<span style="margin:0px; padding:0px">new </span>InetSocketAddress(hostname, port)), storageLevel)</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
}</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">2、参数配置：默认的全局参数，private 级别配置无法修改</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">private val </span><span style="margin:0px; padding:0px">DEFAULT_POLLING_PARALLELISM </span>= 5</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">private val </span><span style="margin:0px; padding:0px">DEFAULT_POLLING_BATCH_SIZE </span>= 1000</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">/**</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px"> * Creates an input stream that is to be used with the Spark Sink deployed on a Flume agent.</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px"> * This stream will poll the sink for data and will pull events as they are available.</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px"> * This stream will use a batch size of 1000 events and run 5 threads to pull data.</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px"> * <span style="margin:0px; padding:0px">@param addresses </span>List of InetSocketAddresses representing the hosts to connect to.</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px"> * <span style="margin:0px; padding:0px">@param storageLevel </span>Storage level to use for storing the received objects</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px"> */</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">def </span>createPollingStream(</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    ssc: StreamingContext,</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    addresses: Seq[InetSocketAddress],</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    storageLevel: StorageLevel</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
  ): ReceiverInputDStream[SparkFlumeEvent] = {</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
  <span style="margin:0px; padding:0px">createPollingStream</span>(ssc, addresses, storageLevel,</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    <span style="margin:0px; padding:0px">DEFAULT_POLLING_BATCH_SIZE</span>, <span style="margin:0px; padding:0px">DEFAULT_POLLING_PARALLELISM</span>)</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
}</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">3、创建FlumePollingInputDstream对象</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">/**</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px"> * Creates an input stream that is to be used with the Spark Sink deployed on a Flume agent.</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px"> * This stream will poll the sink for data and will pull events as they are available.</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px"> * <span style="margin:0px; padding:0px">@param addresses </span>List of InetSocketAddresses representing the hosts to connect to.</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px"> * <span style="margin:0px; padding:0px">@param maxBatchSize </span>Maximum number of events to be pulled from the Spark sink in a</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px"> *                     single RPC call</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px"> * <span style="margin:0px; padding:0px">@param parallelism </span>Number of concurrent requests this stream should send to the sink. Note</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px"> *                    that having a higher number of requests concurrently being pulled will</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px"> *                    result in this stream using more threads</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px"> * <span style="margin:0px; padding:0px">@param storageLevel </span>Storage level to use for storing the received objects</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px"> */</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">def </span>createPollingStream(</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    ssc: StreamingContext,</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    addresses: Seq[InetSocketAddress],</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    storageLevel: StorageLevel,</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    maxBatchSize: Int,</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    parallelism: Int</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
  ): ReceiverInputDStream[SparkFlumeEvent] = {</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
  <span style="margin:0px; padding:0px">new </span>FlumePollingInputDStream[SparkFlumeEvent](ssc, addresses, maxBatchSize,</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    parallelism, storageLevel)</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
}</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px"><span lang="EN-US" style="margin:0px; padding:0px">4、继承自<span lang="EN-US" style="margin:0px; padding:0px">ReceiverInputDstream并覆写<span lang="EN-US" style="margin:0px; padding:0px">getReciver方法，调用<span lang="EN-US" style="margin:0px; padding:0px">FlumePollingReciver接口</span></span></span></span></span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">private</span>[streaming] <span style="margin:0px; padding:0px">class </span>FlumePollingInputDStream[T: ClassTag](</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    _ssc: StreamingContext,</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    <span style="margin:0px; padding:0px">val </span>addresses: Seq[InetSocketAddress],</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    <span style="margin:0px; padding:0px">val </span>maxBatchSize: Int,</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    <span style="margin:0px; padding:0px">val </span>parallelism: Int,</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    storageLevel: StorageLevel</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
  ) <span style="margin:0px; padding:0px">extends </span>ReceiverInputDStream[SparkFlumeEvent](_ssc) {</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
   <span style="margin:0px; padding:0px">override def </span>getReceiver(): Receiver[SparkFlumeEvent] = {</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    <span style="margin:0px; padding:0px">new </span>FlumePollingReceiver(addresses, maxBatchSize, parallelism, storageLevel)</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
  }</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
}</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px"><span lang="EN-US" style="margin:0px; padding:0px">5、<span lang="EN-US" style="margin:0px; padding:0px">ReceiverInputDstream 构建了一个线程池，设置为后台线程；并使用<span lang="EN-US" style="margin:0px; padding:0px">lazy和工厂方法创建线程和<span lang="EN-US" style="margin:0px; padding:0px">NioClientSocket（<span lang="EN-US" style="margin:0px; padding:0px">NioClientSocket底层使用<span lang="EN-US" style="margin:0px; padding:0px">NettyServer的方式）</span></span></span></span></span></span></span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">lazy val </span><span style="margin:0px; padding:0px">channelFactoryExecutor </span>=</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
  Executors.<span style="margin:0px; padding:0px">newCachedThreadPool</span>(<span style="margin:0px; padding:0px">new </span>ThreadFactoryBuilder().setDaemon(<span style="margin:0px; padding:0px">true</span>).</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    setNameFormat("Flume Receiver Channel Thread - %d").build())</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">lazy val </span><span style="margin:0px; padding:0px">channelFactory </span>=</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
  <span style="margin:0px; padding:0px">new </span>NioClientSocketChannelFactory(<span style="margin:0px; padding:0px">channelFactoryExecutor</span>, <span style="margin:0px; padding:0px">channelFactoryExecutor</span>)</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">6、receiverExecutor 内部也是线程池；connections是指链接分布式Flume集群的FlumeConnection实体句柄的个数，线程拿到实体句柄访问数据。</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">lazy val </span><span style="margin:0px; padding:0px">receiverExecutor </span>= Executors.<span style="margin:0px; padding:0px">newFixedThreadPool</span>(parallelism,</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
  <span style="margin:0px; padding:0px">new </span>ThreadFactoryBuilder().setDaemon(<span style="margin:0px; padding:0px">true</span>).setNameFormat("Flume Receiver Thread - %d").build())</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">private lazy val </span><span style="margin:0px; padding:0px">connections </span>= <span style="margin:0px; padding:0px">new </span>LinkedBlockingQueue[FlumeConnection]()</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">7、启动时创建NettyTransceiver，根据并行度(默认5个)循环提交FlumeBatchFetcher</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">override def </span>onStart(): Unit = {</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
  // Create the connections to each Flume agent.</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
  addresses.foreach(host =&gt; {</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    <span style="margin:0px; padding:0px">val </span>transceiver = <span style="margin:0px; padding:0px">new </span>NettyTransceiver(host, <span style="margin:0px; padding:0px">channelFactory</span>)</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    <span style="margin:0px; padding:0px">val </span>client = SpecificRequestor.<span style="margin:0px; padding:0px">getClient</span>(<span style="margin:0px; padding:0px">classOf</span>[SparkFlumeProtocol.Callback], transceiver)</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    <span style="margin:0px; padding:0px">connections</span>.add(<span style="margin:0px; padding:0px">new </span>FlumeConnection(transceiver, client))</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
  })</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
  <span style="margin:0px; padding:0px">for </span>(i &lt;- 0 until parallelism) {</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    logInfo("Starting Flume Polling Receiver worker threads..")</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    // Threads that pull data from Flume.</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    <span style="margin:0px; padding:0px">receiverExecutor</span>.submit(<span style="margin:0px; padding:0px">new </span>FlumeBatchFetcher(<span style="margin:0px; padding:0px">this</span>))</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
  }</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
}</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">8、FlumeBatchFetcher run方法中从Receiver中获取connection链接句柄ack跟消息确认有关</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">def </span>run(): Unit = {</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
  <span style="margin:0px; padding:0px">while </span>(!receiver.isStopped()) {</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    <span style="margin:0px; padding:0px">val </span>connection = receiver.getConnections.poll()</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    <span style="margin:0px; padding:0px">val </span>client = connection.client</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    <span style="margin:0px; padding:0px">var </span>batchReceived = <span style="margin:0px; padding:0px">false</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">    var </span>seq: CharSequence = <span style="margin:0px; padding:0px">null</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">    try </span>{</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
      getBatch(client) <span style="margin:0px; padding:0px">match </span>{</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
        <span style="margin:0px; padding:0px">case </span><span style="margin:0px; padding:0px">Some</span>(eventBatch) =&gt;</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
          batchReceived = <span style="margin:0px; padding:0px">true</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">          </span>seq = eventBatch.getSequenceNumber</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
          <span style="margin:0px; padding:0px">val </span>events = toSparkFlumeEvents(eventBatch.getEvents)</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
          <span style="margin:0px; padding:0px">if </span>(store(events)) {</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
            sendAck(client, seq)</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
          } <span style="margin:0px; padding:0px">else </span>{</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
            sendNack(batchReceived, client, seq)</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
          }</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
        <span style="margin:0px; padding:0px">case </span>None =&gt;</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
      }</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    } <span style="margin:0px; padding:0px">catch </span>{</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">9、获取一批一批数据方法</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">/**</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px"> * Gets a batch of events from the specified client. This method does not handle any exceptions</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px"> * which will be propogated to the caller.</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px"> * <span style="margin:0px; padding:0px">@param client </span>Client to get events from</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px"> * <span style="margin:0px; padding:0px">@return </span></span>[[<span style="margin:0px; padding:0px">Some</span>]] <span style="margin:0px; padding:0px">which contains the event batch if Flume sent any events back, else </span>[[<span style="margin:0px; padding:0px">None</span>]]</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
 <span style="margin:0px; padding:0px">*/</span></p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">private def </span>getBatch(client: SparkFlumeProtocol.Callback): Option[EventBatch] = {</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
  <span style="margin:0px; padding:0px">val </span>eventBatch = client.getEventBatch(receiver.getMaxBatchSize)</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
  <span style="margin:0px; padding:0px">if </span>(!SparkSinkUtils.<span style="margin:0px; padding:0px">isErrorBatch</span>(eventBatch)) {</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    // No error, proceed with processing data</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    logDebug(s"Received batch of <span style="margin:0px; padding:0px">$</span>{eventBatch.getEvents.size} events with sequence " +</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
      s"number: <span style="margin:0px; padding:0px">$</span>{eventBatch.getSequenceNumber}")</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    <span style="margin:0px; padding:0px">Some</span>(eventBatch)</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
  } <span style="margin:0px; padding:0px">else </span>{</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    logWarning("Did not receive events from Flume agent due to error on the Flume agent: " +</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
      eventBatch.getErrorMsg)</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
    None</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
  }</p>
<p align="left" style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
}</p>
<h1 style="text-align:center"><a target="_blank" href="http://blog.csdn.net/qq_21234493/article/details/51339450" rel="nofollow" style="text-decoration:none; color:rgb(0,0,0); font-family:'microsoft yahei'; font-size:18px; font-weight:bold">Spark streaming基于kafka
 以Receiver方式获取数据 原理和案例实战</a></h1>
</div>
<div>
<p class="正文" align="justify" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px; text-align:justify">
<span style="font-size:10.5pt; font-family:Calibri">1：SparkSteaming基于kafka获取数据的方式，主要有俩种，即Receiver和Derict，基于Receiver的方式，是sparkStreaming给我们提供了kafka访问的高层api的封装，而基于Direct的方式，就是直接访问，在sparkSteaming中直接去操作kafka中的数据，不需要前面的高层api的封装。而Direct的方式，可以对kafka进行更好的控制！同时性能也更好。</span></p>
<p class="正文" align="justify" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px; text-align:justify">
<span style="font-size:10.5pt; font-family:Calibri">2：实际上做kafka receiver的时候，通过receiver来获取数据，这个时候，kafka receiver是使用的kafka高层次的comsumer api来实现的。receiver会从kafka中获取数据，然后把它存储到我们具体的Executor内存中。然后<a target="_blank" href="http://lib.csdn.net/base/10" rel="nofollow" class="replace_word" title="Apache Spark知识库" style="text-decoration:none; color:rgb(223,52,52); font-weight:bold">Spark</a> streaming也就是driver中，会根据这获取到的数据，启动job去处理。</span></p>
<p class="正文" align="justify" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px; text-align:justify">
<span style="font-size:10.5pt; font-family:Calibri">3：注意事项：</span></p>
<p class="正文" align="justify" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px; text-align:justify">
<span style="font-size:10.5pt; font-family:Calibri">1）在通过kafka receiver去获取kafka的数据，在正在获取数据的过程中，这台机器有可能崩溃了。如果来不及做备份，数据就会丢失，切换到另外一台机器上，也没有相关数据。这时候，为了数据安全，采用WAL的方式。write  ahead log，预写日志的方式会同步的将接收到的kafka数据，写入到分布式文件系统中。但是预写日志的方式消耗时间，所以存储时建议Memory_and_Disc，不要2.如果是写到hdfs，会自动做副本。如果是写到本地，这其实有个风险，就是如果这台机器崩溃了，再想恢复过来，这个是需要时间的。</span></p>
<p class="正文" align="justify" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px; text-align:justify">
<span style="font-size:10.5pt; font-family:Calibri">2）：我们的kafka receiver接收数据的时候，通过线程或者多线程的方式，kafka中的topic是以partition的方式存在的。sparkstreaming中的kafka receiver接收kafka中topic中的数据，也是通过线程并发的方式去获取的不同的partition，例如用五条线程同时去读取kafka中的topics中的不同的partition数据，这时你这个读取数据的并发线程数，和RDD实际处理数据的并发线程数是没任何关系的。因为获取数据时都还没产生RDD呢。RDD是Driver端决定产生RDD的。</span></p>
<p class="正文" align="justify" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px; text-align:justify">
<span style="font-size:10.5pt; font-family:Calibri">3）默认情况下，一个Executor中是不是只有一个receiver去接收kafka中的数据。那能不能多找一些Executor去更高的并发度，就是使用更多的机器去接收数据，当然可以，基于kafa的api去创建更多的Dstream就可以了。很多的Dstream接收kafka不同topics中的不同的数据，最后你计算的时候，再把他优联就行了。其实这是非常灵活的，因为可以自由的组合。</span></p>
<p class="正文" align="justify" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px; text-align:justify">
<span style="font-size:10.5pt; font-family:Calibri"><br>
</span></p>
<p class="正文" align="justify" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px; text-align:justify">
<span style="font-size:10.5pt; font-family:Calibri"><img src="https://img-blog.csdn.net/20160507211611293?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none"><br>
</span></p>
<p class="正文" align="justify" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px; text-align:justify">
<span style="font-size:10.5pt; font-family:Calibri"><br>
</span></p>
<p class="正文" align="justify" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px; text-align:justify">
<span style="font-size:10.5pt; font-family:Calibri"></span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
kafka + spark streaming <span style="font-family:宋体">集群</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 </p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
前提：</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
spark <span style="font-family:宋体">安装成功，</span><span style="font-family:Calibri">spark 1.6.0</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
zookeeper 安装成功 </p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
kafka <span style="font-family:宋体">安装成功</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
启动集群和zookeeper<span style="font-family:宋体">和</span><span style="font-family:Calibri">kafka</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
步骤：</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
1<span style="font-family:宋体">：创建</span><span style="font-family:Calibri">topic</span><span style="font-family:宋体">为</span><span style="font-family:Calibri">test</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
<span style="font-family:Calibri"><span style="font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">kafka-topics.sh --create --zookeeper master1:2181,work1:2181,work2:2181 --replication-factor
 3 --partitions 1 --topic test</span><br>
</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
在<span style="font-family:Calibri">worker</span>1<span style="font-family:宋体">中</span>启动<span style="font-family:Calibri">kafka</span> 生产者：</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
root@worker1:/usr/local/kafka_2.10-0.9.0.1# bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 <img src="https://img-blog.csdn.net/20160507204406961?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none; max-width:602px; height:auto"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 </p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
在<span style="font-family:Calibri">worker2</span><span style="font-family:宋体">中启动消费者：</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
root@worker2:/usr/local/kafka_2.10-0.9.0.1# bin/kafka-console-consumer.sh --zookeeper master1:2181 --topic test</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 <img src="https://img-blog.csdn.net/20160507204422086?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none; max-width:602px; height:auto"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
生产者生产的消息，消费者可以消费到。说明<span style="font-family:Calibri">kafka</span><span style="font-family:宋体">集群没问题。进入下一步。</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 </p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
在<span style="font-family:Calibri">master</span><span style="font-family:宋体">中启动</span><span style="font-family:Calibri">spark-shell</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
./spark-shell --master  local[2] --packages org.apache.spark:spark-streaming-kafka_2.10:1.6.0</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
笔者用的<span style="font-family:Calibri">spark </span><span style="font-family:宋体">是 </span><span style="font-family:Calibri">1.6.0 </span><span style="font-family:宋体">，读者根据自己版本调整。</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
shell<span style="font-family:宋体">中的逻辑代码（</span><span style="font-family:Calibri">wordcount</span><span style="font-family:宋体">）</span>，启动完成，把下面代码直接丢进去:</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 </p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
import org.apache.spark.SparkConf</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
import org.apache.spark.streaming._</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
import org.apache.spark.streaming.kafka._</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
import org.apache.spark.streaming.kafka.KafkaUtils</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
import org.apache.spark.streaming.{Durations, StreamingContext}</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 </p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
val ssc = new StreamingContext(sc, Durations.seconds(5))</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
// <span style="font-family:宋体">第二个参数是</span>zk<span style="font-family:宋体">集群信息，</span>zk<span style="font-family:宋体">的</span><span style="font-family:Calibri">client host:port</span>，生动的说明了kafka<span style="font-family:宋体">读取数据获取</span><span style="font-family:Calibri">offset</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
//<span style="font-family:宋体">等元数据等信息，是从</span><span style="font-family:Calibri">zk</span><span style="font-family:宋体">里面获取的。所以要连</span><span style="font-family:Calibri">zk</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
// <span style="font-family:宋体">第三个参数是</span>Consumer groupID，随便写的</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
//<span style="font-family:宋体">第</span><span style="font-family:Calibri">4</span><span style="font-family:宋体">个参数是消费的</span><span style="font-family:Calibri">topic</span><span style="font-family:宋体">，以及并发读取</span><span style="font-family:Calibri">topic</span><span style="font-family:宋体">中</span><span style="font-family:Calibri">Partition</span><span style="font-family:宋体">的线程数，这个</span><span style="font-family:Calibri">Map</span><span style="font-family:宋体">指定了你</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
//<span style="font-family:宋体">要消费什么</span><span style="font-family:Calibri">topic</span><span style="font-family:宋体">，以及怎么消费</span><span style="font-family:Calibri">topic</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
KafkaUtils.<span style="background:rgb(255,255,0)">createStream</span>(ssc, "master:2181,worker1:2181,worker2:2181", "StreamingWordCountSelfKafkaScala", Map("test" -&gt; 1)).map(_._2).flatMap(_.split(" ")).map((_,1)).reduceByKey(_+_).print()</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 </p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
生产者再生产消息：</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 <img src="https://img-blog.csdn.net/20160507204438892?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none; max-width:602px; height:auto"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 </p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
spark streaming<span style="font-family:宋体">的反应：</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 <img src="https://img-blog.csdn.net/20160507204451564?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none; max-width:602px; height:auto"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 </p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
返回<span style="font-family:Calibri">worker2</span><span style="font-family:宋体">查看消费者</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 <img src="https://img-blog.csdn.net/20160507204504080?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none; max-width:602px; height:auto"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
可见，groupId<span style="font-family:宋体">不一样，相互之间没有互斥。</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 </p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
上述是使用 <span style="background:rgb(255,255,0)">createStream</span> 方式链接<span style="font-family:Calibri">kafka</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 </p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
还有更高效的方式，请使用<span style="font-family:Calibri">createDirectStream</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 </p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
参考：</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
http://spark.apache.org/docs/latest/streaming-kafka-integration.html</p>
<h1 style="text-align:center"><a target="_blank" href="http://blog.csdn.net/qq_21234493/article/details/51340138" rel="nofollow" style="text-decoration:none; color:rgb(0,0,0); font-family:'microsoft yahei'; font-size:18px; font-weight:bold">sparkStreaming基于kafka的Direct详解</a></h1>
</div>
<div>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
1：Direct方式特点：</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
1）Direct的方式是会直接操作kafka底层的元数据信息，这样如果计算失败了，可以把数据重新读一下，重新处理。即数据一定会被处理。拉数据，是RDD在执行的时候直接去拉数据。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
2）由于直接操作的是kafka，kafka就相当于你底层的文件系统。这个时候能保证严格的事务一致性，即一定会被处理，而且只会被处理一次。而Receiver的方式则不能保证，因为Receiver和ZK中的数据可能不同步，<a target="_blank" href="http://lib.csdn.net/base/10" rel="nofollow" class="replace_word" title="Apache Spark知识库" style="text-decoration:none; color:rgb(223,52,52); font-weight:bold">Spark</a> Streaming可能会重复消费数据，这个调优可以解决，但显然没有Direct方便。而Direct
 api直接是操作kafka的，spark streaming自己负责追踪消费这个数据的偏移量或者offset，并且自己保存到checkpoint，所以它的数据一定是同步的，一定不会被重复。即使重启也不会重复，因为checkpoint了，但是程序升级的时候，不能读取原先的checkpoint，面对升级checkpoint无效这个问题，怎么解决呢?升级的时候读取我指定的备份就可以了，即手动的指定checkpoint也是可以的，这就再次完美的确保了事务性，有且仅有一次的事务机制。那么怎么手动checkpoint呢？构建SparkStreaming的时候，有getorCreate这个api，它就会获取checkpoint的内容，具体指定下这个checkpoint在哪就好了。或者如下图：</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
<img src="https://img-blog.csdn.net/20160507224651648?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none; max-width:602px; height:auto"><br>
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
而如果从checkpoint恢复后，如果数据累积太多处理不过来，怎么办?1）限速2）增强机器的处理能力3）放到数据缓冲池中。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
3）由于底层是直接读数据，没有所谓的Receiver，直接是周期性(Batch Intervel)的查询kafka，处理数据的时候，我们会使用基于kafka原生的Consumer  api来获取kafka中特定范围(offset范围)中的数据。这个时候，Direct Api访问kafka带来的一个显而易见的性能上的好处就是，如果你要读取多个partition，Spark也会创建RDD的partition，这个时候RDD的partition和kafka的partition是一致的。而Receiver的方式，这2个partition是没任何关系的。这个优势是你的RDD，其实本质上讲在底层读取kafka的时候，kafka的partition就相当于原先hdfs上的一个block。这就符合了数据本地性。RDD和kafka数据都在这边。所以读数据的地方，处理数据的地方和驱动数据处理的程序都在同样的机器上，这样就可以极大的提高性能。不足之处是由于RDD和kafka的patition是一对一的，想提高并行度就会比较麻烦。提高并行度还是repartition，即重新分区，因为产生shuffle，很耗时。这个问题，以后也许新版本可以自由配置比例，不是一对一。因为提高并行度，可以更好的利用集群的计算资源，这是很有意义的。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
4）不需要开启wal机制，从数据零丢失的角度来看，极大的提升了效率，还至少能节省一倍的磁盘空间。从kafka获取数据，比从hdfs获取数据，因为zero copy的方式，速度肯定更快。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
2：实战部分</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
<br>
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
kafka + spark streaming <span style="font-family:宋体">集群</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 </p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
前提：</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
spark <span style="font-family:宋体">安装成功，</span><span style="font-family:Calibri">spark 1.6.0</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
zookeeper 安装成功 </p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
kafka <span style="font-family:宋体">安装成功</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 </p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
步骤：</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
1：先启动三台机器上的ZK，然后三台机器同样启动kafka，<br>
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
2<span style="font-family:宋体">：在kafka上创建</span><span style="font-family:Calibri">topic  test</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
3：在<span style="font-family:Calibri">worker</span>1<span style="font-family:宋体">中</span>启动<span style="font-family:Calibri">kafka</span> 生产者：</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
root@worker1:/usr/local/kafka_2.10-0.9.0.1# bin/kafka-console-producer.sh --broker-list <span style="color:rgb(0,255,204)">localhost:9092</span> --topic test</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 <img src="https://img-blog.csdn.net/20160508075339872?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none; max-width:602px; height:auto"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 </p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
在<span style="font-family:Calibri">worker2</span><span style="font-family:宋体">中启动消费者：</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
root@worker2:/usr/local/kafka_2.10-0.9.0.1# bin/kafka-console-consumer.sh --zookeeper <span style="color:rgb(250,191,143)">master:2181</span> --topic test</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 <img src="https://img-blog.csdn.net/20160508074958664?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none; max-width:602px; height:auto"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
生产者生产的消息，消费者可以消费到。说明<span style="font-family:Calibri">kafka</span><span style="font-family:宋体">集群没问题。进入下一步。</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 </p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
在<span style="font-family:Calibri">master</span><span style="font-family:宋体">中启动</span><span style="font-family:Calibri">spark-shell</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
./spark-shell --master local[2] --packages org.apache.spark:spark-streaming-kafka_2.10:<span style="background:rgb(255,255,0)">1.6.0</span>,org.apache.kafka:kafka_2.10:0.8.2.1</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
笔者用的<span style="font-family:Calibri">spark </span><span style="font-family:宋体">是 </span><span style="background:rgb(255,255,0)">1.6.0</span> ，读者根据自己版本调整。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 <img src="https://img-blog.csdn.net/20160508075037837?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none; max-width:602px; height:auto"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 </p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
shell<span style="font-family:宋体">中的逻辑代码（</span><span style="font-family:Calibri">wordcount</span><span style="font-family:宋体">）</span><span style="font-family:Calibri">:</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 </p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
import org.apache.spark.SparkConf</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
import kafka.serializer.StringDecoder</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
import org.apache.spark.streaming.kafka.KafkaUtils</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
import org.apache.spark.streaming.{Durations, StreamingContext}</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 </p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px; background:rgb(43,43,43)">
<span style="color:rgb(255,255,255)">val </span><span style="color:rgb(169,183,198)">ssc = </span><span style="color:rgb(204,120,50)">new </span><span style="color:rgb(169,183,198)">StreamingContext(sc</span><span style="color:rgb(204,120,50)">, </span><span style="color:rgb(169,183,198)">Durations.</span><span style="color:rgb(169,183,198)">seconds</span><span style="color:rgb(169,183,198)">(</span><span style="color:rgb(104,151,187)">5</span><span style="color:rgb(169,183,198)">))</span><span style="color:rgb(169,183,198)"><br>
</span><span style="color:rgb(169,183,198)">KafkaUtils.</span><span style="color:rgb(169,183,198)">createDirectStream</span><span style="color:rgb(169,183,198)">[</span><span style="color:rgb(78,128,125)">String</span><span style="color:rgb(204,120,50)">, </span><span style="color:rgb(78,128,125)">String</span><span style="color:rgb(204,120,50)">, </span><span style="color:rgb(169,183,198)">StringDecoder</span><span style="color:rgb(204,120,50)">, </span><span style="color:rgb(169,183,198)">StringDecoder](ssc</span><span style="color:rgb(169,183,198)"><br>
</span><span style="color:rgb(169,183,198)">  </span><span style="color:rgb(204,120,50)">, </span><span style="color:rgb(152,118,170)">Map</span><span style="color:rgb(169,183,198)">(</span><span style="color:rgb(106,135,89)">"bootstrap.servers" </span><span style="color:rgb(169,183,198)">-&gt; </span><span style="color:rgb(106,135,89)">"master:2181,worker1:2181,worker2:2181"</span><span style="color:rgb(204,120,50)">, </span><span style="color:rgb(106,135,89)">"metadata.broker.list" </span><span style="color:rgb(169,183,198)">-&gt; </span><span style="color:rgb(106,135,89)">"master:9092,worker1:9092,worker2:9092"</span><span style="color:rgb(204,120,50)">, </span><span style="color:rgb(106,135,89)">"group.id" </span><span style="color:rgb(169,183,198)">-&gt; </span><span style="color:rgb(106,135,89)">"StreamingWordCountSelfKafkaDirectStreamScala"</span><span style="color:rgb(169,183,198)">)</span><span style="color:rgb(169,183,198)"><br>
</span><span style="color:rgb(169,183,198)">  </span><span style="color:rgb(204,120,50)">, </span><span style="color:rgb(152,118,170)">Set</span><span style="color:rgb(169,183,198)">(</span><span style="color:rgb(106,135,89)">"test"</span><span style="color:rgb(169,183,198)">)).map(t =&gt; t._2).flatMap(_.toString.split(</span><span style="color:rgb(106,135,89)">" "</span><span style="color:rgb(169,183,198)">)).map((_</span><span style="color:rgb(204,120,50)">, </span><span style="color:rgb(104,151,187)">1</span><span style="color:rgb(169,183,198)">)).reduceByKey(_ + _).print()</span><span style="color:rgb(169,183,198)"><br>
</span><span style="color:rgb(169,183,198)">ssc.start()</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 </p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 </p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
生产者再生产消息：</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 <img src="https://img-blog.csdn.net/20160508075111076?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none; max-width:602px; height:auto"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 </p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
spark streaming<span style="font-family:宋体">的反应：</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 <img src="https://img-blog.csdn.net/20160508075135557?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none; max-width:602px; height:auto"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 </p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
返回<span style="font-family:Calibri">worker2</span><span style="font-family:宋体">查看消费者</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 <img src="https://img-blog.csdn.net/20160508075159045?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none; max-width:602px; height:auto"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
可见，groupId<span style="font-family:宋体">不一样，相互之间没有互斥。</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 </p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
上述是使用 <span style="background:rgb(255,255,0)">createDirectStream</span> 方式链接<span style="font-family:Calibri">kafka，实际使用中，其实就是和Receiver在api以及api中参数上有不同，其它基本一样</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 </p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
参考：</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
http://spark.apache.org/docs/latest/streaming-kafka-integration.html</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
1.SparkStreaming中的Transforamtions</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
2.SparkStreaming中的状态管理</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
一.DStream就是一个RDD之上的一个抽象，DStream和时间结合起来就不断的触发产生RDD的实例，可以说我们对Dstream的操作就初步定义了对RDD的操作，只不过需要时间的间隔也就是internalbatch去激活这个模板，生成具体的RDD的实例和具体的job.</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
二.我们鼓励Repartition，更多的是把更多的partition变成更少的partition，进行流的碎片的整理，我们不太鼓励把更少的partition变成更多的partion，因为会牵扯shuffle。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
三.DStream是离散流，离散流就没状态，除了计算每个时间间隔产生一个job，我们还有必要计算过去十分钟或者半个小时，所以这个时候我们需要维护这个状态。后台<a target="_blank" href="http://lib.csdn.net/base/10" rel="nofollow" class="replace_word" title="Apache Spark知识库" style="text-decoration:none; color:rgb(223,52,52); font-weight:bold">Spark</a>提供了专门维护这个状态的函数updateStateByKey(func),即基于key，我们可以进行多个状态的维护。因为你可以把每一个时间间隔都做为一个状态，例如每一秒钟做为一个状态，我算下过去十分钟或者半个小时。值的更新就是通过传进来的func函数。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
四.Transform</p>
<table border="0" cellspacing="0" cellpadding="0" width="560" style="color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
<tbody>
<tr>
<td>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<strong>transform</strong>(<em>func</em>)</p>
</td>
<td>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
Return a new DStream by applying a RDD-to-RDD function to every RDD of the source DStream. This can be used to do arbitrary RDD operations on the DStream.</p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
编程的逻辑是作用于RDD</p>
</td>
</tr>
</tbody>
</table>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
Transform操作，允许任意的RDD和RDD的操作被应用在DStream上。他可以使这些RDD不容易暴露在DstreamAPI中。比如让两个batch产生join操作而不暴露在DstreamAPi中，然后你可以很容易的使用transform来做这。这将是非常有作用的，例如，能够将实时数据清理通过将输入的数据流和预先计算的垃圾信息过滤掉。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
五.UpdateByKey</p>
<table border="0" cellspacing="0" cellpadding="0" width="560" style="color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
<tbody>
<tr>
<td>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<strong>updateStateByKey</strong>(<em>func</em>)</p>
</td>
<td>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
Return a new "state" DStream where the state for each key is updated by applying the given function on the previous state of the key and the new values for the key. This can be used to maintain arbitrary state data for each key.</p>
</td>
</tr>
</tbody>
</table>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
UpdaeStateByKey的操作，允许你维护任意的不断通过新的信息来更新的状态。使用这个函数你必须遵守两个步骤</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
1.定义一个状态：这个状态可以是任意的数据类型</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
2.定义一个状态更新函数：怎么样去使用从一个数据流中产生的旧的状态和新的状态来更新出一个状态。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
六.forecachRDD(func)</p>
<table border="0" cellspacing="0" cellpadding="0" width="560" style="color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
<tbody>
<tr>
<td>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<strong>foreachRDD</strong>(<em>func</em>)</p>
</td>
<td>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
The most generic output operator that applies a function,<em>func</em>, to each RDD generated from the stream. This function should push the data in each RDD to an external system, such as saving the RDD to files, or writing it over the network to a database.
 Note that the function <em>func</em> is executed in the driver process running the streaming application, and will usually have RDD actions in it that will force the computation of the streaming RDDs.</p>
</td>
</tr>
</tbody>
</table>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
mapWithState将流式的状态管理性能提高10倍以上</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
foreachRDD(<em>func</em>)中的函数func是作用于最后一个RDD,也就是结果RDD，如果RDD没有数据，就不需要进行操作，foreachRDD()可以将数据写在Redis/Hbase/<a target="_blank" href="http://lib.csdn.net/base/14" rel="nofollow" class="replace_word" title="MySQL知识库" style="text-decoration:none; color:rgb(223,52,52); font-weight:bold">数据库</a>/具体文件中，foreachRDD是在Driver程序中执行的，func就是action。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
七.updateStateByKey</p>
<pre style="white-space:pre-wrap; word-wrap:break-word; color:rgb(85,85,85); font-size:15px; line-height:35px; background:rgb(43,43,43)"><strong><span style="color:rgb(204,120,50)">val </span></strong><span style="color:rgb(169,183,198)">cogroupedRDD = parentRDD.cogroup(prevStateRDD</span><span style="color:rgb(204,120,50)">, </span><span style="color:rgb(169,183,198)">partitioner)
</span><strong><span style="color:rgb(204,120,50)">val </span></strong><span style="color:rgb(169,183,198)">stateRDD = cogroupedRDD.mapPartitions(finalFunc</span><span style="color:rgb(204,120,50)">, </span><span style="color:rgb(169,183,198)">preservePartitioning)
<em>Some</em>(stateRDD)</span></pre>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
 </p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
cogroup是性能的瓶颈，所有的老数据，过去的数据都要进行cogroup操作，即使新的数据pairedRDD只有一条记录，也要把所有的老记录都要进行cogroup操作。这时相当耗时的。理论上讲，只应对这条记录对应的key和历史的一批数据中对应的这个key进行更新操作就行了，而它更新全部的，99%的时间都是浪费和消耗。性能非常低。也会产生shuffle。而下面的MapWithState则只更新你必须要更新的，所以极大提升了性能。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:'microsoft yahei'; font-size:15px; line-height:35px">
MapWithState只需要更新你必须更新的，没有必要更新所有的记录，官方宣传这个api会把流式的状态管理性能提升10倍以上。</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">第一部分：</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
updateStateByKey它的主要功能是随着时间的流逝，在Spark Streaming中可以为每一个key可以通过CheckPoint来维护一份state状态，通过更新函数对该key的状态不断更新；在更新的时候，对每一个新<span style="margin:0px; padding:0px; font-family:宋体; font-size:10.5pt; line-height:1.5">批次的数据（</span><span style="margin:0px; padding:0px; line-height:1.5">batch）而言，Spark
 Streaming通过使用updateStateByKey为已经存在的key进行state的状态更新（对每个新出现的key，会同样执行state的更新函数操作）；但是如果通过更新函数对state更新后返回none的话，此时刻key对应的state状态会被删除掉，需要特别说明的是state可以是任意类型的<a target="_blank" href="http://lib.csdn.net/base/31" rel="nofollow" class="replace_word" title="算法与数据结构知识库" style="text-decoration:none; color:rgb(223,52,52); font-weight:bold">数据结构</a>，这就为我们的计算带来无限的想象空间；</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">非常重要：</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
如果要不断的更新每个key的state，就一定会涉及到状态的保存和容错，这个时候就需要开启checkpoint机制和功能，需要说明的是checkpoint可以保存一切可以存储在文件系统上的内容，例如：程序未处理的但已经拥有状态的<span style="margin:0px; padding:0px; line-height:1.5">数据</span><span style="margin:0px; padding:0px; line-height:1.5">。</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; line-height:1.5">虽然说DStream是流式处理，但是由于我们保存了前面处理的结果，所以我可以不断在历史的基础上进行次数的更新。</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">补充说明：</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
关于流式处理对历史状态进行保存和更新具有重大实用意义，例如进行广告点击全面的动态评估（动态评估就是既有历史的数据又有现在的数据）（投放广告和运营广告效果评估的价值意义，热点随时追踪、热力图）</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">案例实战源码：</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">1.编写源码：</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
ackage org.apache.spark.examples.streaming;</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
import <a target="_blank" href="http://lib.csdn.net/base/17" rel="nofollow" class="replace_word" title="Java EE知识库" style="text-decoration:none; color:rgb(223,52,52); font-weight:bold">Java</a>.util.Arrays;</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
import java.util.List;</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
import org.apache.spark.SparkConf;</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
import org.apache.spark.api.java.function.FlatMapFunction;</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
import org.apache.spark.api.java.function.Function2;</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
import org.apache.spark.api.java.function.PairFunction;</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
import org.apache.spark.streaming.Durations;</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
import org.apache.spark.streaming.api.java.JavaDStream;</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
import org.apache.spark.streaming.api.java.JavaPairDStream;</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
import org.apache.spark.streaming.api.java.JavaReceiverInputDStream;</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
import org.apache.spark.streaming.api.java.JavaStreamingContext;</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
import com.google.common.base.Optional;</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
import scala.Tuple2;</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
 </p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
public class UpdateStateByKeyDemo {</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
public static void main(String[] args) {</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<br>
</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
SparkConf conf = new SparkConf().setMaster("local[2]").</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
setAppName("UpdateStateByKeyDemo");</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<br>
</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
JavaStreamingContext jsc = new JavaStreamingContext(conf, Durations.seconds(5));</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(51,153,102)">//报错解决办法做checkpoint,开启checkpoint机制，把checkpoint中的数据放在这里设置的目录中，这里必须做checkpoint</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(51,153,102)">//checkpoint如果挂了，那就挂了。所以生产环境下一般放在HDFS中，因为checkpoint有三份副本，一份挂了，还有另外2份容错。每次都要checkpoint，是会耗性能的，后面可以改进</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
jsc.checkpoint("/usr/local/tmp/checkpoint");</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(51,153,102)">/*</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(51,153,102)">* 第三步：创建Spark Streaming输入数据来源input Stream：</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(51,153,102)">* 1，数据输入来源可以基于File、HDFS、Flume、Kafka、Socket等</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(51,153,102)">* 2, 在这里我们指定数据来源于网络Socket端口，Spark Streaming连接上该端口并在运行的时候一直监听该端口</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(51,153,102)">* 的数据（当然该端口服务首先必须存在）,并且在后续会根据业务需要不断的有数据产生(当然对于Spark Streaming</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(51,153,102)">* 应用程序的运行而言，有无数据其处理流程都是一样的)； </span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(51,153,102)">* 3,如果经常在每间隔5秒钟没有数据的话不断的启动空的Job其实是会造成调度资源的浪费，因为并没有数据需要发生计算，所以</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(51,153,102)">* 实例的企业级生成环境的代码在具体提交Job前会判断是否有数据，如果没有的话就不再提交Job；</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(51,153,102)">*/</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
JavaReceiverInputDStream lines = jsc.socketTextStream("Master", 9999);</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<br>
</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
JavaDStream&lt;String&gt; words = lines.flatMap(new FlatMapFunction&lt;String, String&gt;() { //如果是Scala，由于SAM转换，所以可以写成val words = lines.flatMap { line =&gt; line.split(" ")}</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
@Override</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
public Iterable&lt;String&gt; call(String line) throws Exception {</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
return Arrays.asList(line.split(" "));</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
}</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
});</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<br>
</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
JavaPairDStream&lt;String, Integer&gt; pairs = words.mapToPair(new PairFunction&lt;String, String, Integer&gt;() {</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
@Override</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
public Tuple2&lt;String, Integer&gt; call(String word) throws Exception {</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
return new Tuple2&lt;String, Integer&gt;(word, 1);</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
}</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
});</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(51,153,102)">/*</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(51,153,102)">      *第4.3步：在这里是通过updateStateByKey来以Batch Interval为单位来对历史状态进行更新，</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(51,153,102)">      * 这是功能上的一个非常大的改进，否则的话需要完成同样的目的，就可能需要把数据保存在Redis、</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(51,153,102)">      * Tagyon或者HDFS或者HBase或者<a target="_blank" href="http://lib.csdn.net/base/14" rel="nofollow" class="replace_word" title="MySQL知识库" style="text-decoration:none; color:rgb(223,52,52); font-weight:bold">数据库</a>中来不断的完成同样一个key的State更新，如果你对性能有极为苛刻的要求，</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(51,153,102)">      * 且数据量特别大的话，可以考虑把数据放在分布式的Redis或者Tachyon内存文件系统中，如精准的秒杀系统；</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(51,153,102)">      * 当然从Spark1.6.x开始可以尝试使用mapWithState，Spark2.X后mapWithState应该非常稳定了。这样就去除了cogroup的弊端</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(51,153,102)"> */</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(51,153,102)">//如果发现不识别报错，一般是导包导错了，这里就导错了Optional的包，搞了好久<br>
</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
JavaPairDStream&lt;String, Integer&gt; wordsCount = pairs.updateStateByKey(new Function2&lt;List&lt;Integer&gt;, Optional&lt;Integer&gt;, Optional&lt;Integer&gt;&gt;() { <span style="margin:0px; padding:0px; color:rgb(51,153,102)">//对相同的Key，进行Value的累计（包括Local和Reducer级别同时Reduce）</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
@Override</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
public Optional&lt;Integer&gt; call(List&lt;Integer&gt; values, Optional&lt;Integer&gt; state)</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
throws Exception {</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
//第一个参数就是key传进来的数据，第二个参数是曾经已有的数据</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
Integer updatedValue = 0 ;//如果第一次，state没有，updatedValue为0，如果有，就获取</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
if(state.isPresent()){</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
updatedValue = state.get();</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
}</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
//遍历batch传进来的数据可以一直加，随着时间的流式会不断去累加相同key的value的结果。</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
for(Integer value: values){</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
updatedValue += value;</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
}</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
return Optional.of(updatedValue);//返回更新的值</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
}</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
});</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(51,153,102)">/*</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(51,153,102)">*此处的print并不会直接出发Job的执行，因为现在的一切都是在Spark Streaming框架的控制之下的，对于Spark Streaming</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(51,153,102)">*而言具体是否触发真正的Job运行是基于设置的Duration时间间隔的</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(51,153,102)">*诸位一定要注意的是Spark Streaming应用程序要想执行具体的Job，对Dtream就必须有output Stream操作，</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(51,153,102)">*output Stream有很多类型的函数触发，类print、saveAsTextFile、saveAsHadoopFiles等，最为重要的一个</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(51,153,102)">*方法是foraeachRDD,因为Spark Streaming处理的结果一般都会放在Redis、DB、DashBoard等上面，foreachRDD</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(51,153,102)">*主要就是用用来完成这些功能的，而且可以随意的自定义具体数据到底放在哪里！！！</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(51,153,102)">*/</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
wordsCount.print();</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(51,153,102)">/*</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(51,153,102)">* Spark Streaming执行引擎也就是Driver开始运行，Driver启动的时候是位于一条新的线程中的，当然其内部有消息循环体，用于</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(51,153,102)">* 接受应用程序本身或者Executor中的消息；</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px; color:rgb(51,153,102)">*/</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
jsc.start();</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
jsc.awaitTermination();</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
jsc.close();</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
}</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">2.创建checkpoint目录：</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
jsc.checkpoint("/usr/local/tmp/checkpoint");</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">3.　在eclipse中通过run 方法启动main函数：</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span lang="EN-US" style="margin:0px; padding:0px; font-size:10.5pt; font-family:Calibri,sans-serif"><img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160430093043160-1868835882.jpg" alt="" style="border:0px; margin:0px; padding:0px; max-width:900px"><br style="margin:0px; padding:0px">
</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px"><span style="margin:0px; padding:0px; font-size:10.5pt; font-family:宋体">4.启动hdfs服务并发送nc -lk 9999请求：</span></span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160430093348285-477095064.png" alt="" style="border:0px; max-width:900px; height:auto; margin:0px; padding:0px"></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">继续输入Hello hdfs</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
结果就是累加后的结果，</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
(Hello,3)</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
(SPark,1)</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
(hdfs,2)</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
再次输入Hello SPark，下一个batch就会继续累加，(Hello,3)(SPark,2) (hdfs,2)的结果输出</p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">5.查看checkpoint目录输出：因为是二进制</span></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160430093524691-1929970307.jpg" alt="" style="border:0px; max-width:900px; height:auto; margin:0px; padding:0px"></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160430093540472-1350436620.png" alt="" style="border:0px; max-width:900px; height:auto; margin:0px; padding:0px"></p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
 </p>
<p style="margin:10px auto; padding-top:0px; padding-bottom:0px; color:rgb(85,85,85); font-family:Verdana,Arial,Helvetica,sans-serif; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">
<span style="margin:0px; padding:0px">源码解析：</span></p>
<pre style="white-space:pre-wrap; word-wrap:break-word; color:rgb(85,85,85); margin-top:0px; margin-bottom:0px; padding:0px; font-size:14px; line-height:21px; background-color:rgb(238,238,238)"><span style="margin:0px; padding:0px">1.PairDStreamFunctions类：</span></pre>
<pre style="white-space:pre-wrap; word-wrap:break-word; color:rgb(85,85,85); margin-top:0px; margin-bottom:0px; padding:0px; font-size:14px; line-height:21px; background-color:rgb(238,238,238)"><span id="__mceDel" style="margin:0px; padding:0px">/**</span></pre>
<pre style="white-space:pre-wrap; word-wrap:break-word; color:rgb(85,85,85); margin-top:0px; margin-bottom:0px; padding:0px; font-size:14px; line-height:21px; background-color:rgb(238,238,238)"><span id="__mceDel" style="margin:0px; padding:0px"> * Return a new "state" DStream where the state for each key is updated by applying<br style="margin:0px; padding:0px"> * the given function on the previous state of the key and the new values of each key.<br style="margin:0px; padding:0px"> * Hash partitioning is used to generate the RDDs with Spark's default number of partitions.<br style="margin:0px; padding:0px"> * @param updateFunc State update function. If `this` function returns None, then<br style="margin:0px; padding:0px"> *                   corresponding state key-value pair will be eliminated.<br style="margin:0px; padding:0px"> * @tparam S State type<br style="margin:0px; padding:0px"> */<br style="margin:0px; padding:0px">def updateStateByKey[S: ClassTag](<br style="margin:0px; padding:0px">    updateFunc: (Seq[V], Option[S]) =&gt; Option[S]<br style="margin:0px; padding:0px">  ): DStream[(K, S)] = ssc.withScope {<br style="margin:0px; padding:0px">  updateStateByKey(updateFunc, defaultPartitioner())<br style="margin:0px; padding:0px">}</span></pre>
<pre style="white-space:pre-wrap; word-wrap:break-word; color:rgb(85,85,85); margin-top:0px; margin-bottom:0px; padding:0px; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">/**<br style="margin:0px; padding:0px"> * Return a new "state" DStream where the state for each key is updated by applying<br style="margin:0px; padding:0px"> * the given function on the previous state of the key and the new values of the key.<br style="margin:0px; padding:0px"> * org.apache.spark.Partitioner is used to control the partitioning of each RDD.<br style="margin:0px; padding:0px"> * @param updateFunc State update function. If `this` function returns None, then<br style="margin:0px; padding:0px"> *                   corresponding state key-value pair will be eliminated.<br style="margin:0px; padding:0px"> * @param partitioner Partitioner for controlling the partitioning of each RDD in the new<br style="margin:0px; padding:0px"> *                    DStream.<br style="margin:0px; padding:0px"> * @tparam S State type<br style="margin:0px; padding:0px"> */<br style="margin:0px; padding:0px">def updateStateByKey[S: ClassTag](<br style="margin:0px; padding:0px">    updateFunc: (Seq[V], Option[S]) =&gt; Option[S],<br style="margin:0px; padding:0px">    partitioner: Partitioner<br style="margin:0px; padding:0px">  ): DStream[(K, S)] = ssc.withScope {<br style="margin:0px; padding:0px">  val cleanedUpdateF = sparkContext.clean(updateFunc)<br style="margin:0px; padding:0px">  val newUpdateFunc = (iterator: Iterator[(K, Seq[V], Option[S])]) =&gt; {<br style="margin:0px; padding:0px">    iterator.flatMap(t =&gt; cleanedUpdateF(t._2, t._3).map(s =&gt; (t._1, s)))<br style="margin:0px; padding:0px">  }<br style="margin:0px; padding:0px">  updateStateByKey(newUpdateFunc, partitioner, true)<br style="margin:0px; padding:0px">}</pre>
<pre style="white-space:pre-wrap; word-wrap:break-word; color:rgb(85,85,85); margin-top:0px; margin-bottom:0px; padding:0px; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">/**<br style="margin:0px; padding:0px"> * Return a new "state" DStream where the state for each key is updated by applying<br style="margin:0px; padding:0px"> * the given function on the previous state of the key and the new values of each key.<br style="margin:0px; padding:0px"> * org.apache.spark.Partitioner is used to control the partitioning of each RDD.<br style="margin:0px; padding:0px"> * @param updateFunc State update function. Note, that this function may generate a different<br style="margin:0px; padding:0px"> *                   tuple with a different key than the input key. Therefore keys may be removed<br style="margin:0px; padding:0px"> *                   or added in this way. It is up to the developer to decide whether to<br style="margin:0px; padding:0px"> *                   remember the partitioner despite the key being changed.<br style="margin:0px; padding:0px"> * @param partitioner Partitioner for controlling the partitioning of each RDD in the new<br style="margin:0px; padding:0px"> *                    DStream<br style="margin:0px; padding:0px"> * @param rememberPartitioner Whether to remember the paritioner object in the generated RDDs.<br style="margin:0px; padding:0px"> * @tparam S State type<br style="margin:0px; padding:0px"> */<br style="margin:0px; padding:0px"><br style="margin:0px; padding:0px">def updateStateByKey[S: ClassTag](<br style="margin:0px; padding:0px">    updateFunc: (Iterator[(K, Seq[V], Option[S])]) =&gt; Iterator[(K, S)],<br style="margin:0px; padding:0px">    partitioner: Partitioner,<br style="margin:0px; padding:0px">    rememberPartitioner: Boolean<br style="margin:0px; padding:0px">  ): DStream[(K, S)] = ssc.withScope {<br style="margin:0px; padding:0px">   new StateDStream(self, ssc.sc.clean(updateFunc), partitioner, rememberPartitioner, None)<br style="margin:0px; padding:0px">}<br style="margin:0px; padding:0px"><br style="margin:0px; padding:0px"></pre>
<pre style="white-space:pre-wrap; word-wrap:break-word; color:rgb(85,85,85); margin-top:0px; margin-bottom:0px; padding:0px; font-size:14px; line-height:21px; background-color:rgb(238,238,238)">override def compute(validTime: Time): Option[RDD[(K, S)]] = {<br style="margin:0px; padding:0px"><br style="margin:0px; padding:0px">  // Try to get the previous state RDD<br style="margin:0px; padding:0px">  getOrCompute(validTime - slideDuration) match {<br style="margin:0px; padding:0px"><br style="margin:0px; padding:0px">    case Some(prevStateRDD) =&gt; {    // If previous state RDD exists<br style="margin:0px; padding:0px"><br style="margin:0px; padding:0px">      // Try to get the parent RDD<br style="margin:0px; padding:0px">      parent.getOrCompute(validTime) match {<br style="margin:0px; padding:0px">        case Some(parentRDD) =&gt; {   // If parent RDD exists, then compute as usual<br style="margin:0px; padding:0px">          computeUsingPreviousRDD (parentRDD, prevStateRDD)<br style="margin:0px; padding:0px">        }<br style="margin:0px; padding:0px">        case None =&gt; {    // If parent RDD does not exist<br style="margin:0px; padding:0px"><br style="margin:0px; padding:0px">          // Re-apply the update function to the old state RDD<br style="margin:0px; padding:0px">          val updateFuncLocal = updateFunc<br style="margin:0px; padding:0px">          val finalFunc = (iterator: Iterator[(K, S)]) =&gt; {<br style="margin:0px; padding:0px">            val i = iterator.map(t =&gt; (t._1, Seq[V](), Option(t._2)))<br style="margin:0px; padding:0px">            updateFuncLocal(i)<br style="margin:0px; padding:0px">          }<br style="margin:0px; padding:0px">          val stateRDD = prevStateRDD.mapPartitions(finalFunc, preservePartitioning)<br style="margin:0px; padding:0px">          Some(stateRDD)<br style="margin:0px; padding:0px">        }<br style="margin:0px; padding:0px">      }<br style="margin:0px; padding:0px">    }<br style="margin:0px; padding:0px"><br style="margin:0px; padding:0px">    case None =&gt; {    // If previous session RDD does not exist (first input data)<br style="margin:0px; padding:0px"><br style="margin:0px; padding:0px">      // Try to get the parent RDD<br style="margin:0px; padding:0px">      parent.getOrCompute(validTime) match {<br style="margin:0px; padding:0px">        case Some(parentRDD) =&gt; {   // If parent RDD exists, then compute as usual<br style="margin:0px; padding:0px">          initialRDD match {<br style="margin:0px; padding:0px">            case None =&gt; {<br style="margin:0px; padding:0px">              // Define the function for the mapPartition operation on grouped RDD;<br style="margin:0px; padding:0px">              // first map the grouped tuple to tuples of required type,<br style="margin:0px; padding:0px">              // and then apply the update function<br style="margin:0px; padding:0px">              val updateFuncLocal = updateFunc<br style="margin:0px; padding:0px">              val finalFunc = (iterator : Iterator[(K, Iterable[V])]) =&gt; {<br style="margin:0px; padding:0px">                updateFuncLocal (iterator.map (tuple =&gt; (tuple._1, tuple._2.toSeq, None)))<br style="margin:0px; padding:0px">              }<br style="margin:0px; padding:0px"><br style="margin:0px; padding:0px">              val groupedRDD = parentRDD.groupByKey (partitioner)<br style="margin:0px; padding:0px">              val sessionRDD = groupedRDD.mapPartitions (finalFunc, preservePartitioning)<br style="margin:0px; padding:0px">              // logDebug("Generating state RDD for time " + validTime + " (first)")<br style="margin:0px; padding:0px">              Some (sessionRDD)<br style="margin:0px; padding:0px">            }<br style="margin:0px; padding:0px">            case Some (initialStateRDD) =&gt; {<br style="margin:0px; padding:0px">              computeUsingPreviousRDD(parentRDD, initialStateRDD)<br style="margin:0px; padding:0px">            }<br style="margin:0px; padding:0px">          }<br style="margin:0px; padding:0px">        }<br style="margin:0px; padding:0px">        case None =&gt; { // If parent RDD does not exist, then nothing to do!<br style="margin:0px; padding:0px">          // logDebug("Not generating state RDD (no previous state, no parent)")<br style="margin:0px; padding:0px">          None<br style="margin:0px; padding:0px">        }<br style="margin:0px; padding:0px">      }<br style="margin:0px; padding:0px">    }<br style="margin:0px; padding:0px">  }</pre>
<br>
<br>
</div>
            </div>
                </div>