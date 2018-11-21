---
layout:     post
title:      Spark Stream 实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u012016268/article/details/51182350				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>1.Spark Streaming on HDFS</p>
<p>2.Spark Streaming On HDFS 源码解析</p>
<p><br></p>
<p></p>
<pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:'宋体';font-size:13.5pt;"><span style="color:#cc7832;">import </span>java.util.Arrays<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">import </span>org.apache.spark.SparkConf<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">import </span>org.apache.spark.api.java.function.FlatMapFunction<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">import </span>org.apache.spark.api.java.function.Function2<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">import </span>org.apache.spark.api.java.function.PairFunction<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">import </span>org.apache.spark.streaming.Duration<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">import </span>org.apache.spark.streaming.Durations<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">import </span>org.apache.spark.streaming.api.java.JavaDStream<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">import </span>org.apache.spark.streaming.api.java.JavaPairDStream<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">import </span>org.apache.spark.streaming.api.java.JavaReceiverInputDStream<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">import </span>org.apache.spark.streaming.api.java.JavaStreamingContext<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">import </span>org.apache.spark.streaming.api.java.JavaStreamingContextFactory<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">import </span>scala.Tuple2<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">public class </span>SparkStreamingOnHDFS {

   <span style="color:#cc7832;">public static void </span><span style="color:#ffc66d;">main</span>(String[] args) {
      
      <span style="color:#808080;">/*
</span><span style="color:#808080;">       * 第一步：配置SparkConf：
</span><span style="color:#808080;">       * 1，至少2条线程：因为Spark Streaming应用程序在运行的时候，至少有一条
</span><span style="color:#808080;">       * 线程用于不断的循环接收数据，并且至少有一条线程用于处理接受的数据（否则的话无法
</span><span style="color:#808080;">       * 有线程用于处理数据，随着时间的推移，内存和磁盘都会不堪重负）；
</span><span style="color:#808080;">       * 2，对于集群而言，每个Executor一般肯定不止一个Thread，那对于处理Spark Streaming的
</span><span style="color:#808080;">       * 应用程序而言，每个Executor一般分配多少Core比较合适？根据我们过去的经验，5个左右的
</span><span style="color:#808080;">       * Core是最佳的（一个段子分配为奇数个Core表现最佳，例如3个、5个、7个Core等）；
</span><span style="color:#808080;">       */
</span><span style="color:#808080;">      /*SparkConf conf = new SparkConf().setMaster("local[2]").
</span><span style="color:#808080;">            setAppName("WordCountOnline");*/
</span><span style="color:#808080;">      
</span><span style="color:#808080;">      </span><span style="color:#cc7832;">final </span>SparkConf conf = <span style="color:#cc7832;">new </span>SparkConf().setMaster(<span style="color:#6a8759;">"local[2]"</span>).
            setAppName(<span style="color:#6a8759;">"SparkStreamingOnHDFS"</span>)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">      
</span><span style="color:#cc7832;">      </span><span style="color:#808080;">/*
</span><span style="color:#808080;">       * 第二步：创建SparkStreamingContext：
</span><span style="color:#808080;">       * 1，这个是SparkStreaming应用程序所有功能的起始点和程序调度的核心
</span><span style="color:#808080;">       * SparkStreamingContext的构建可以基于SparkConf参数，也可基于持久化的SparkStreamingContext的内容
</span><span style="color:#808080;">       * 来恢复过来（典型的场景是Driver崩溃后重新启动，由于Spark Streaming具有连续7*24小时不间断运行的特征，
</span><span style="color:#808080;">       * 所有需要在Driver重新启动后继续上衣系的状态，此时的状态恢复需要基于曾经的Checkpoint）；
</span><span style="color:#808080;">       * 2，在一个Spark Streaming应用程序中可以创建若干个SparkStreamingContext对象，使用下一个SparkStreamingContext
</span><span style="color:#808080;">       * 之前需要把前面正在运行的SparkStreamingContext对象关闭掉，由此，我们获得一个重大的启发SparkStreaming框架也只是
</span><span style="color:#808080;">       * Spark Core上的一个应用程序而已，只不过Spark Streaming框架箱运行的话需要Spark工程师写业务逻辑处理代码；
</span><span style="color:#808080;">       */
</span><span style="color:#808080;">//    JavaStreamingContext jsc = new JavaStreamingContext(conf, Durations.seconds(5));
</span><span style="color:#808080;">      
</span><span style="color:#808080;">      </span><span style="color:#cc7832;">final </span>String checkpointDirectory = <span style="color:#6a8759;">"hdfs://master:9000/streaming/checkpointPath"</span><span style="color:#cc7832;">;
</span><span style="color:#cc7832;">      
</span><span style="color:#cc7832;">      </span><span style="color:#808080;">//JavaStreamingContextFactory factory = () -&gt; createContext(checkpointDirectory, conf);
</span><span style="color:#808080;">       
</span><span style="color:#808080;">       </span><span style="color:#629755;"><em>/**
</em></span><span style="color:#629755;"><em>        * 可以从失败中回复Driver,不过还需要制定Driver这个进程运行在Cluster,并且提交应用程序的时候制定--supervise;
</em></span><span style="color:#629755;"><em>        */
</em></span><span style="color:#629755;"><em>       </em></span>JavaStreamingContext jsc =  <span style="color:#cc7832;">new </span>JavaStreamingContext(conf<span style="color:#cc7832;">, new </span>Duration(<span style="color:#6897bb;">5000</span>))<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">      
</span><span style="color:#cc7832;">      </span><span style="color:#808080;">/*
</span><span style="color:#808080;">       * 第三步：创建Spark Streaming输入数据来源input Stream：
</span><span style="color:#808080;">       * 1，数据输入来源可以基于File、HDFS、Flume、Kafka、Socket等
</span><span style="color:#808080;">       * 2, 在这里我们指定数据来源于网络Socket端口，Spark Streaming连接上该端口并在运行的时候一直监听该端口
</span><span style="color:#808080;">       *        的数据（当然该端口服务首先必须存在）,并且在后续会根据业务需要不断的有数据产生(当然对于Spark Streaming
</span><span style="color:#808080;">       *        应用程序的运行而言，有无数据其处理流程都是一样的)； 
</span><span style="color:#808080;">       * 3,如果经常在每间隔5秒钟没有数据的话不断的启动空的Job其实是会造成调度资源的浪费，因为并没有数据需要发生计算，所以
</span><span style="color:#808080;">       *        实例的企业级生成环境的代码在具体提交Job前会判断是否有数据，如果没有的话就不再提交Job；
</span><span style="color:#808080;">       * 4,此处没有Receiver,SparkStreaming应用程序只是按照时间间隔监控目录下每个Batch新增的内容(把新增的)作为RDD的数据来源生产原始RDD
</span><span style="color:#808080;">       */
</span><span style="color:#808080;">      
</span><span style="color:#808080;">      </span>JavaDStream lines = jsc.textFileStream(<span style="color:#6a8759;">"file:///D:</span><span style="color:#cc7832;">\\</span><span style="color:#6a8759;">work</span><span style="color:#cc7832;">\\</span><span style="color:#6a8759;">admin</span><span style="color:#cc7832;">\\</span><span style="color:#6a8759;">src</span><span style="color:#cc7832;">\\</span><span style="color:#6a8759;">main</span><span style="color:#cc7832;">\\</span><span style="color:#6a8759;">java</span><span style="color:#cc7832;">\\</span><span style="color:#6a8759;">com</span><span style="color:#cc7832;">\\</span><span style="color:#6a8759;">zy</span><span style="color:#cc7832;">\\</span><span style="color:#6a8759;">bean</span><span style="color:#cc7832;">\\</span><span style="color:#6a8759;">bus</span><span style="color:#cc7832;">\\</span><span style="color:#6a8759;">"</span>)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">      
</span><span style="color:#cc7832;">      </span><span style="color:#808080;">/*
</span><span style="color:#808080;">       * 第四步：接下来就像对于RDD编程一样基于DStream进行编程！！！原因是DStream是RDD产生的模板（或者说类），在Spark Streaming具体
</span><span style="color:#808080;">       * 发生计算前，其实质是把每个Batch的DStream的操作翻译成为对RDD的操作！！！
</span><span style="color:#808080;">       *对初始的DStream进行Transformation级别的处理，例如map、filter等高阶函数等的编程，来进行具体的数据计算
</span><span style="color:#808080;">        *     第4.1步：讲每一行的字符串拆分成单个的单词
</span><span style="color:#808080;">        */
</span><span style="color:#808080;">      </span>JavaDStream&lt;String&gt; words = lines.flatMap((FlatMapFunction&lt;String<span style="color:#cc7832;">, </span>String&gt;) line -&gt; Arrays.<span style="font-style:italic;">asList</span>(line.split(<span style="color:#6a8759;">" "</span>)))<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">       </span><span style="color:#808080;">/*
</span><span style="color:#808080;">          * 第四步：对初始的DStream进行Transformation级别的处理，例如map、filter等高阶函数等的编程，来进行具体的数据计算
</span><span style="color:#808080;">          *   第4.2步：在单词拆分的基础上对每个单词实例计数为1，也就是word =&gt; (word, 1)
</span><span style="color:#808080;">          */
</span><span style="color:#808080;">      </span>JavaPairDStream&lt;String<span style="color:#cc7832;">, </span>Integer&gt; pairs = words.mapToPair((PairFunction&lt;String<span style="color:#cc7832;">, </span>String<span style="color:#cc7832;">, </span>Integer&gt;) word -&gt; <span style="color:#cc7832;">new </span>Tuple2&lt;&gt;(word<span style="color:#cc7832;">, </span><span style="color:#6897bb;">1</span>))<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">      
</span><span style="color:#cc7832;">       </span><span style="color:#808080;">/*
</span><span style="color:#808080;">          * 第四步：对初始的DStream进行Transformation级别的处理，例如map、filter等高阶函数等的编程，来进行具体的数据计算
</span><span style="color:#808080;">          *   第4.3步：在每个单词实例计数为1基础之上统计每个单词在文件中出现的总次数
</span><span style="color:#808080;">          */
</span><span style="color:#808080;">      </span>JavaPairDStream&lt;String<span style="color:#cc7832;">, </span>Integer&gt; wordsCount = pairs.reduceByKey((Function2&lt;Integer<span style="color:#cc7832;">, </span>Integer<span style="color:#cc7832;">, </span>Integer&gt;) (v1<span style="color:#cc7832;">, </span>v2) -&gt; v1 + v2)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">      
</span><span style="color:#cc7832;">      </span><span style="color:#808080;">/*
</span><span style="color:#808080;">       * 此处的print并不会直接出发Job的执行，因为现在的一切都是在Spark Streaming框架的控制之下的，对于Spark Streaming
</span><span style="color:#808080;">       * 而言具体是否触发真正的Job运行是基于设置的Duration时间间隔的
</span><span style="color:#808080;">       * 
</span><span style="color:#808080;">       * 诸位一定要注意的是Spark Streaming应用程序要想执行具体的Job，对Dtream就必须有output Stream操作，
</span><span style="color:#808080;">       * output Stream有很多类型的函数触发，类print、saveAsTextFile、saveAsHadoopFiles等，最为重要的一个
</span><span style="color:#808080;">       * 方法是foraeachRDD,因为Spark Streaming处理的结果一般都会放在Redis、DB、DashBoard等上面，foreachRDD
</span><span style="color:#808080;">       * 主要就是用用来完成这些功能的，而且可以随意的自定义具体数据到底放在哪里！！！
</span><span style="color:#808080;">       *
</span><span style="color:#808080;">       */
</span><span style="color:#808080;">      </span>wordsCount.print()<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">      
</span><span style="color:#cc7832;">      </span><span style="color:#808080;">/*
</span><span style="color:#808080;">       * Spark Streaming执行引擎也就是Driver开始运行，Driver启动的时候是位于一条新的线程中的，当然其内部有消息循环体，用于
</span><span style="color:#808080;">       * 接受应用程序本身或者Executor中的消息；
</span><span style="color:#808080;">       */
</span><span style="color:#808080;">      </span>jsc.start()<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">      
</span><span style="color:#cc7832;">      </span>jsc.awaitTermination()<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">      </span>jsc.close()<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">
</span><span style="color:#cc7832;">   </span>}
   
   <span style="color:#cc7832;">private static </span>JavaStreamingContext <span style="color:#ffc66d;">createContext</span>(
            String checkpointDirectory<span style="color:#cc7832;">,</span>SparkConf conf) {

      <span style="color:#808080;">// If you do not see this printed, that means the StreamingContext has been loaded
</span><span style="color:#808080;">      // from the new checkpoint
</span><span style="color:#808080;">      </span>System.<span style="color:#9876aa;"><em>out</em></span>.println(<span style="color:#6a8759;">"Creating new context"</span>)<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">   
</span><span style="color:#cc7832;">      </span>SparkConf sparkConf = conf<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">      </span><span style="color:#808080;">// Create the context with a 1 second batch size
</span><span style="color:#808080;">      </span>JavaStreamingContext ssc = <span style="color:#cc7832;">new </span>JavaStreamingContext(sparkConf<span style="color:#cc7832;">, </span>Durations.<span style="font-style:italic;">seconds</span>(<span style="color:#6897bb;">15</span>))<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">      </span><span style="color:#808080;">//ssc.checkpoint(checkpointDirectory);
</span><span style="color:#808080;">      
</span><span style="color:#808080;">      </span><span style="color:#cc7832;">return </span>ssc<span style="color:#cc7832;">;
</span><span style="color:#cc7832;">   </span>}

}</pre>
<br><p></p>
            </div>
                </div>