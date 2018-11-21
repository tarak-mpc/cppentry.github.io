---
layout:     post
title:      Spark Streaming从Flume Poll数据案例实战和内幕源码解密
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/erfucun/article/details/52263178				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>本博文内容主要包括以下几点内容： <br>
1、Spark Streaming on Polling from Flume实战 <br>
2、Spark Streaming on Polling from Flume源码</p>

<p><strong>一、推模式(Flume push SparkStreaming)与拉模式（SparkStreaming poll Flume）比较 ：</strong></p>

<p>采用推模式：推模式的理解就是Flume作为缓存，存有数据。监听对应端口，如果服务可以链接，就将数据push过去。(简单，耦合要低)，缺点是SparkStreaming 程序没有启动的话，Flume端会报错，同时可能会导致Spark Streaming 程序来不及消费的情况。</p>

<p>采用拉模式：拉模式就是自己定义一个sink，SparkStreaming自己去channel里面取数据，根据自身条件去获取数据，稳定性好。</p>

<p><strong>二、Flume poll 实战：</strong></p>

<p>1.Flume poll 配置</p>

<p>进入<a href="http://spark.apache.org/docs/latest/streaming-flume-integration.html" rel="nofollow">http://spark.apache.org/docs/latest/streaming-flume-integration.html</a>官网，下载 <br>
spark-streaming-flume-sink_2.10-1.6.0.jar、scala-library-2.10.5.jar、commons-lang3-3.3.2.jar三个包： <br>
<img src="https://img-blog.csdn.net/20160820172712009" alt="这里写图片描述" title=""></p>

<p>2、将下载后的三个jar包放入Flume安装lib目录：</p>

<p>3、配置Flume conf环境参数： <br>
首先进入此入境 <br>
<img src="https://img-blog.csdn.net/20160820173421168" alt="这里写图片描述" title=""> <br>
接下来在此文件中的sink1中添加此内容：</p>

<pre class="prettyprint"><code class=" hljs avrasm">agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.type</span> = org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.SparkSink</span>

agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hostname</span> = Master

agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.port</span> = <span class="hljs-number">9999</span>

agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.channel</span> = channel1</code></pre>

<p><img src="https://img-blog.csdn.net/20160820173816466" alt="这里写图片描述" title=""></p>

<p><strong>三、编写代码：</strong></p>

<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">SkarkStreamingPollDataFromFlume</span> {</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) {
         <span class="hljs-comment">/*
        * 第一步：配置SparkConf：
        * 1，至少2条线程：因为Spark Streaming应用程序在运行的时候，至少有一条
        * 线程用于不断的循环接收数据，并且至少有一条线程用于处理接受的数据（否则的话无法
        * 有线程用于处理数据，随着时间的推移，内存和磁盘都会不堪重负）；
        * 2，对于集群而言，每个Executor一般肯定不止一个Thread，那对于处理Spark Streaming的
        * 应用程序而言，每个Executor一般分配多少Core比较合适？根据我们过去的经验，5个左右的
        * Core是最佳的（一个段子分配为奇数个Core表现最佳，例如3个、5个、7个Core等）；
        */</span>

        SparkConf conf = <span class="hljs-keyword">new</span> SparkConf().setAppName(<span class="hljs-string">"SparkStreamingPollDataFromFlume"</span>).setMaster(<span class="hljs-string">"local[2]"</span>);
          <span class="hljs-comment">/*
         * 第二步：创建SparkStreamingContext：
         * 1，这个是SparkStreaming应用程序所有功能的起始点和程序调度的核心
         * SparkStreamingContext的构建可以基于SparkConf参数，也可基于持久化的SparkStreamingContext的内容
         * 来恢复过来（典型的场景是Driver崩溃后重新启动，由于Spark Streaming具有连续7*24小时不间断运行的特征，
        * 所有需要在Driver重新启动后继续上衣系的状态，此时的状态恢复需要基于曾经的Checkpoint）；
         * 2，在一个Spark Streaming应用程序中可以创建若干个SparkStreamingContext对象，使用下一个SparkStreamingContext
         * 之前需要把前面正在运行的SparkStreamingContext对象关闭掉，由此，我们获得一个重大的启发SparkStreaming框架也只是
         * Spark Core上的一个应用程序而已，只不过Spark Streaming框架箱运行的话需要Spark工程师写业务逻辑处理代码；
         */</span>
        JavaStreamingContext jsc = <span class="hljs-keyword">new</span> JavaStreamingContext(conf,Durations.seconds(<span class="hljs-number">30</span>));
         <span class="hljs-comment">/*

         * 第三步：创建Spark Streaming输入数据来源input Stream：
         * 1，数据输入来源可以基于File、HDFS、Flume、Kafka、Socket等
         * 2, 在这里我们指定数据来源于网络Socket端口，Spark Streaming连接上该端口并在运行的时候一直监听该端口
         *                  的数据（当然该端口服务首先必须存在）,并且在后续会根据业务需要不断的有数据产生(当然对于Spark Streaming
         *                  应用程序的运行而言，有无数据其处理流程都是一样的)；
         * 3,如果经常在每间隔5秒钟没有数据的话不断的启动空的Job其实是会造成调度资源的浪费，因为并没有数据需要发生计算，所以
         *                 实例的企业级生成环境的代码在具体提交Job前会判断是否有数据，如果没有的话就不再提交Job；
         */</span>                 

        JavaReceiverInputDStream lines = FlumeUtils.createPollingStream(jsc, <span class="hljs-string">"Master"</span>, <span class="hljs-number">9999</span>);
          <span class="hljs-comment">/*
         * 第四步：接下来就像对于RDD编程一样基于DStream进行编程！！！原因是DStream是RDD产生的模板（或者说类），在Spark Streaming具体
         * 发生计算前，其实质是把每个Batch的DStream的操作翻译成为对RDD的操作！！！
         *对初始的DStream进行Transformation级别的处理，例如map、filter等高阶函数等的编程，来进行具体的数据计算
   *    第4.1步：讲每一行的字符串拆分成单个的单词
   */</span>   
        JavaDStream&lt;String&gt; words = lines.flatMap(<span class="hljs-keyword">new</span> FlatMapFunction&lt;SparkFlumeEvent, String&gt;() {

            <span class="hljs-annotation">@Override</span>
            <span class="hljs-keyword">public</span> Iterable&lt;String&gt; <span class="hljs-title">call</span>(SparkFlumeEvent event) <span class="hljs-keyword">throws</span> Exception {
                String line = <span class="hljs-keyword">new</span> String(event.event().getBody().array());
                <span class="hljs-keyword">return</span> Arrays.asList(line.split(<span class="hljs-string">" "</span>));

            }
        });
          <span class="hljs-comment">/*
         * 第四步：对初始的DStream进行Transformation级别的处理，例如map、filter等高阶函数等的编程，来进行具体的数据计算
         * 第4.2步：在单词拆分的基础上对每个单词实例计数为1，也就是word =&gt; (word, 1)
         */</span>
        JavaPairDStream&lt;String,Integer&gt; pairs = words.mapToPair(<span class="hljs-keyword">new</span> PairFunction&lt;String, String, Integer&gt;() {

            <span class="hljs-annotation">@Override</span>
            <span class="hljs-keyword">public</span> Tuple2&lt;String, Integer&gt; <span class="hljs-title">call</span>(String word) <span class="hljs-keyword">throws</span> Exception {
                <span class="hljs-comment">// TODO Auto-generated method stub</span>
                <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> Tuple2&lt;String,Integer&gt;(word,<span class="hljs-number">1</span>);
            }
        });
          <span class="hljs-comment">/*
         * 第四步：对初始的DStream进行Transformation级别的处理，例如map、filter等高阶函数等的编程，来进行具体的数据计算
         * 第4.3步：在每个单词实例计数为1基础之上统计每个单词在文件中出现的总次数
         */</span>
        JavaPairDStream&lt;String,Integer&gt; wordsCount = pairs.reduceByKey(<span class="hljs-keyword">new</span> Function2&lt;Integer, Integer, Integer&gt;() {

            <span class="hljs-annotation">@Override</span>
            <span class="hljs-keyword">public</span> Integer <span class="hljs-title">call</span>(Integer v1, Integer v2) <span class="hljs-keyword">throws</span> Exception {
                <span class="hljs-comment">// TODO Auto-generated method stub</span>
                <span class="hljs-keyword">return</span> v1 + v2;
            }
        });
         <span class="hljs-comment">/*
         *        此处的print并不会直接出发Job的执行，因为现在的一切都是在Spark Streaming框架的控制之下的，对于Spark Streaming
         *        而言具体是否触发真正的Job运行是基于设置的Duration时间间隔的
         *        诸位一定要注意的是Spark Streaming应用程序要想执行具体的Job，对Dtream就必须有output Stream操作，
         *        output Stream有很多类型的函数触发，类print、saveAsTextFile、saveAsHadoopFiles等，最为重要的一个
         *        方法是foraeachRDD,因为Spark Streaming处理的结果一般都会放在Redis、DB、DashBoard等上面，foreachRDD
         *        主要就是用用来完成这些功能的，而且可以随意的自定义具体数据到底放在哪里！！！
         *
         */</span>
        wordsCount.print();
          <span class="hljs-comment">/*
         * Spark Streaming执行引擎也就是Driver开始运行，Driver启动的时候是位于一条新的线程中的，当然其内部有消息循环体，用于
         * 接受应用程序本身或者Executor中的消息；
         */</span>
        jsc.start();
        jsc.awaitTermination();
        jsc.close();
    }
}
</code></pre>

<p>启动HDFS集群：</p>

<p>启动运行Flume：</p>

<p>启动eclipse下的应用程序：</p>

<p>copy测试文件hellospark.txt到Flume flume-conf.properties配置文件中指定的/usr/local/flume/tmp/TestDir目录下：</p>

<p>隔24秒后可以在eclipse程序控制台中看到上传的文件单词统计结果。</p>

<p><strong>四：源码分析：</strong></p>

<p>1、创建createPollingStream （FlumeUtils.scala ）：</p>

<p><img src="https://img-blog.csdn.net/20160820193418341" alt="这里写图片描述" title=""></p>

<p>2、参数配置：默认的全局参数，private 级别配置无法修改： <br>
<img src="https://img-blog.csdn.net/20160820193438545" alt="这里写图片描述" title=""></p>

<p>3、创建FlumePollingInputDstream对象</p>

<p><img src="https://img-blog.csdn.net/20160820193453410" alt="这里写图片描述" title=""></p>

<p>4、继承自ReceiverInputDstream并覆写getReciver方法，调用FlumePollingReciver接口：</p>

<p><img src="https://img-blog.csdn.net/20160820193506769" alt="这里写图片描述" title=""></p>

<p>5、ReceiverInputDstream 构建了一个线程池，设置为后台线程；并使用lazy和工厂方法创建线程和NioClientSocket（NioClientSocket底层使用NettyServer的方式）</p>

<p><img src="https://img-blog.csdn.net/20160820193522910" alt="这里写图片描述" title=""></p>

<p>6、receiverExecutor 内部也是线程池；connections是指链接分布式Flume集群的FlumeConnection实体句柄的个数，线程拿到实体句柄访问数据。</p>

<p><img src="https://img-blog.csdn.net/20160820193538781" alt="这里写图片描述" title=""></p>

<p>7、启动时创建NettyTransceiver，根据并行度(默认5个)循环提交FlumeBatchFetcher</p>

<p><img src="https://img-blog.csdn.net/20160820193553801" alt="这里写图片描述" title=""></p>

<p>8、FlumeBatchFetcher run方法中从Receiver中获取connection链接句柄ack跟消息确认有关</p>

<p><img src="https://img-blog.csdn.net/20160820193612141" alt="这里写图片描述" title=""></p>

<p>9、获取一批一批数据方法</p>

<p><img src="https://img-blog.csdn.net/20160820193626129" alt="这里写图片描述" title=""></p>

<p>补充说明：</p>

<p>使用Spark Streaming可以处理各种数据来源类型，如：数据库、HDFS，服务器log日志、网络流，其强大超越了你想象不到的场景，只是很多时候大家不会用，其真正原因是对Spark、spark streaming本身不了解。</p>

<p>博文内容源自DT大数据梦工厂Spark课程。相关课程内容视频可以参考：  <br>
百度网盘链接：<a href="http://pan.baidu.com/s/1slvODe1" rel="nofollow">http://pan.baidu.com/s/1slvODe1</a>（如果链接失效或需要后续的更多资源，请联系QQ460507491或者微信号：DT1219477246 获取上述资料）。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>