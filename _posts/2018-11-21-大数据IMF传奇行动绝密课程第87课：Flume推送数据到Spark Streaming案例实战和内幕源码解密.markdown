---
layout:     post
title:      大数据IMF传奇行动绝密课程第87课：Flume推送数据到Spark Streaming案例实战和内幕源码解密
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="flume推送数据到spark-streaming案例实战和内幕源码解密">Flume推送数据到Spark Streaming案例实战和内幕源码解密</h1>

<p>1、Flume on HDFS案例回顾 <br>
2、Flume推送数据到Spark Streaming实战 <br>
3、原理绘图剖析 <br>
<img title="" alt="图87-1 Flume基于HDFS数据流" src="https://img-blog.csdn.net/20170403193222028?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdG9tXzg4OTlfbGk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast"></p>

<p>一、配置.bashrc <br>
vi ~/.bashrc</p>



<pre class="prettyprint"><code class=" hljs lasso">export FLUME_HOME<span class="hljs-subst">=</span>/usr/<span class="hljs-built_in">local</span>/flume/apache<span class="hljs-attribute">-flume</span><span class="hljs-subst">-</span><span class="hljs-number">1.6</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span>

export FLUME_CONF_DIR<span class="hljs-subst">=</span><span class="hljs-variable">$FLUME_HOME</span>/conf</code></pre>

<p>PATH 加上 </p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-variable">$FLUME_HOME</span>/bin</code></pre>

<p>二、配置conf <br>
拷贝flume-conf.properties.template为flume-conf.properties并改写</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor">#agent1表示代理名称</span>
agent1<span class="hljs-preprocessor">.sources</span>=source1
agent1<span class="hljs-preprocessor">.sinks</span>=sink1
agent1<span class="hljs-preprocessor">.channels</span>=channel1

<span class="hljs-preprocessor">#配置source1</span>
agents1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.type</span>=spooldir
agents1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.spoolDir</span>=/usr/local/flume/tmp/TestDir
agents1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.channels</span>=channel1
agents1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.fileHeader</span>=false
agents1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.interceptors</span>=i1
agents1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>1<span class="hljs-preprocessor">.type</span>=timestamp

<span class="hljs-preprocessor">#配置sink1</span>
<span class="hljs-preprocessor">#sink到HDFS</span>
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.type</span>=hdfs
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.path</span>=hdfs://master:<span class="hljs-number">9000</span>/library/flume
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.fileType</span>=DataStream
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.writeFormat</span>=TEXT
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollInterval</span>=<span class="hljs-number">1</span>
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.channel</span>=channel1
agent1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.filePrefix</span>=%<span class="hljs-built_in">Y</span>-%m-%d
<span class="hljs-preprocessor">#sink到某个端口</span>
<span class="hljs-preprocessor">#agent1.sinks.sink1.type=avro</span>
<span class="hljs-preprocessor">#agent1.sinks.sink1.channel=channel1</span>
<span class="hljs-preprocessor">#agent1.sinks.sink1.hostname=Master</span>
<span class="hljs-preprocessor">#agent1.sinks.sink1.port=9999</span>
<span class="hljs-preprocessor">#sink到Kafka</span>
<span class="hljs-preprocessor">#agent1.sinks.sink1.type=org.apache.flume.plugins.KafkaSink</span>
<span class="hljs-preprocessor">#agent1.sinks.sink1.metadata.broker.list=Master:9092,Worker1:9092,Worker2.9092</span>
<span class="hljs-preprocessor">#agent1.sinks.sink1.partition.key=0</span>
<span class="hljs-preprocessor">#agent1.sinks.sink1.partitioner.class=org.apache.flume.plugins.SinglePartition</span>
<span class="hljs-preprocessor">#agent1.sinks.sink1.serializer.class=kafka.serializer.StringEncoder</span>
<span class="hljs-preprocessor">#agent1.sinks.sink1.request.requiredacks=0</span>
<span class="hljs-preprocessor">#agent1.sinks.sink1.max.message.size=1000000</span>
<span class="hljs-preprocessor">#agent1.sinks.sink1.producer.type=sync</span>
<span class="hljs-preprocessor">#agent1.sinks.sink1.custom.encoding=UTF-8</span>
<span class="hljs-preprocessor">#agent1.sinks.sink1.custom.topic.name=HelloKafka</span>
<span class="hljs-preprocessor">#agent1.sinks.sink1.channel= channel1</span>


<span class="hljs-preprocessor">#配置channel1</span>
agent1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.channel</span>1<span class="hljs-preprocessor">.type</span>=file
agent1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.channel</span>1<span class="hljs-preprocessor">.chackpointDir</span>=/usr/local/flume/tmp/checkpointDir
agent1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.channel</span>1<span class="hljs-preprocessor">.dataDirs</span>=/usr/local/flume/tmp/dataDirs</code></pre>

<p>启动Flume： </p>

<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-built_in">.</span>/flume<span class="hljs-attribute">-ng</span> agent <span class="hljs-attribute">-n</span> agent1 <span class="hljs-attribute">-c</span> conf <span class="hljs-attribute">-f</span> /usr/<span class="hljs-built_in">local</span>/flume/apache<span class="hljs-attribute">-flume</span><span class="hljs-subst">-</span><span class="hljs-number">1.6</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span>/conf/flume<span class="hljs-attribute">-conf</span><span class="hljs-built_in">.</span>properties <span class="hljs-attribute">-Dflume</span><span class="hljs-built_in">.</span>root<span class="hljs-built_in">.</span>logger<span class="hljs-subst">=</span>DEBUG,console</code></pre>

<p>Java代码：</p>

<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">package</span> com.tom.spark.SparkApps.sparkstreaming;

<span class="hljs-keyword">import</span> java.util.Arrays;

<span class="hljs-keyword">import</span> org.apache.spark.SparkConf;
<span class="hljs-keyword">import</span> org.apache.spark.api.java.function.FlatMapFunction;
<span class="hljs-keyword">import</span> org.apache.spark.api.java.function.Function2;
<span class="hljs-keyword">import</span> org.apache.spark.api.java.function.PairFunction;
<span class="hljs-keyword">import</span> org.apache.spark.streaming.Durations;
<span class="hljs-keyword">import</span> org.apache.spark.streaming.api.java.JavaDStream;
<span class="hljs-keyword">import</span> org.apache.spark.streaming.api.java.JavaPairDStream;
<span class="hljs-keyword">import</span> org.apache.spark.streaming.api.java.JavaReceiverInputDStream;
<span class="hljs-keyword">import</span> org.apache.spark.streaming.api.java.JavaStreamingContext;
<span class="hljs-keyword">import</span> org.apache.spark.streaming.flume.FlumeUtils;
<span class="hljs-keyword">import</span> org.apache.spark.streaming.flume.SparkFlumeEvent;

<span class="hljs-keyword">import</span> scala.Tuple2;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">FlumePushData2SparkStreaming</span> {</span>

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) {
        <span class="hljs-comment">// TODO Auto-generated method stub</span>
        <span class="hljs-javadoc">/**
         * 第一步：配置SparkConf，
         * 1、至少2条线程：因为Spark Streaming应用程序在运行的时候至少有一条
         * 线程用于不断的循环接收数据，并且至少有一条线程用于处理接收的数据（否则的话无法有线程用
         * 于处理数据，随着时间的推移，内存和磁盘都会不堪重负）
         * 2、对于集群而言，每个Executor一般肯定不止一个Thread，那对于处理Spark Streaming
         * 应用程序而言，每个Executor一般分配多少Core比较合适？根据经验，5个左右的Core是最佳的
         * （一个段子：分配为奇数个Core表现最佳，例如3个、5个、7个Core等
         */</span>
        SparkConf conf = <span class="hljs-keyword">new</span> SparkConf().setMaster(<span class="hljs-string">"local[4]"</span>).setAppName(<span class="hljs-string">"FlumePushData2SparkStreaming"</span>);
        <span class="hljs-javadoc">/**
         * 第二步：创建Spark StreamingContext：
         * 1、这是SparkStreaming应用程序所有功能的起始点和程序调度的核心.
         * SparkStreamingContext的构建可以基于SparkConf参数，也可以基于持久化的SparkStreamingContext的
         * 内容来恢复过来（典型的场景是Driver崩溃后重新启动，由于Spark Streaming具有连续7*24小时不间断运行的特征，
         * 所以需要在Driver重新启动后继续上一次的状态，此时的状态恢复需要基于曾经的Checkpoint）
         * 2、在一个Spark Streaming应用程序中可以创建若干个SparkStreamingContext对象，使用下一个SparkStreaming
         * 之前需要把前面正在运行的SparkStreamingContext对象关闭掉，由此，我们得出一个重大启发，SparkStreaming框架也只是
         * Spark Core上的一个应用程序而已，只不过Spark Streaming框架想运行的话需要Spark工程师写业务逻辑处理代码
         */</span>
        JavaStreamingContext javassc = <span class="hljs-keyword">new</span> JavaStreamingContext(conf, Durations.seconds(<span class="hljs-number">5</span>));

        <span class="hljs-javadoc">/**
         * 第三步：创建Spark Streaming输入数据来源input Stream:
         * 1、数据输入来源可以基于File、HDFS、Flume、Kafka、Socket等
         * 2、在这里我们指定数据来源于网络Socket端口，Spark Streaming连接上该端口并在运行的时候一直监听该端口的数据
         *      (当然该端口服务首先必须存在），并且在后续会根据业务需要不断有数据产生（当然对于Spark Streaming
         *      应用程序的运行而言，有无数据其处理流程都是一样的）
         * 3、如果经常在每间隔5秒钟没有数据的话不断启动空的Job其实会造成调度资源的浪费，因为并没有数据需要发生计算；所以
         *      实际的企业级生成环境的代码在具体提交Job前会判断是否有数据，如果没有的话就不再提交Job；
         */</span>

        JavaReceiverInputDStream&lt;SparkFlumeEvent&gt; lines = FlumeUtils.createStream(javassc, <span class="hljs-string">"Master"</span>, <span class="hljs-number">9999</span>);
        <span class="hljs-javadoc">/**
         * 第四步：接下来就像对于RDD编程一样基于DStream进行编程，原因是DStream是RDD产生的模板（或者说类），在Spark Streaming具体
         * 发生计算前，其实质是把每个Batch的DStream的操作翻译成为对RDD的操作！
         * 
         */</span>
        JavaDStream&lt;String&gt; words = lines.flatMap(<span class="hljs-keyword">new</span> FlatMapFunction&lt;SparkFlumeEvent, String&gt;(){ <span class="hljs-comment">//如果是Scala，由于SAM转换，所以可以写成val words = lines.flatMap(_.split(" ")) </span>

            <span class="hljs-keyword">public</span> Iterable&lt;String&gt; <span class="hljs-title">call</span>(SparkFlumeEvent event) <span class="hljs-keyword">throws</span> Exception {
                String line = event.event().body.array().toString();            
                <span class="hljs-keyword">return</span> Arrays.asList(line.split(<span class="hljs-string">" "</span>));
            }
        });

        <span class="hljs-javadoc">/**
         * 第4.2步：在单词拆分的基础上对每个单词实例计数为1，也就是word =&gt; (word, 1)
         */</span>
        JavaPairDStream&lt;String, Integer&gt; pairs = words.mapToPair(<span class="hljs-keyword">new</span> PairFunction&lt;String, String, Integer&gt;() {

            <span class="hljs-keyword">public</span> Tuple2&lt;String, Integer&gt; <span class="hljs-title">call</span>(String word) <span class="hljs-keyword">throws</span> Exception {
                <span class="hljs-comment">// TODO Auto-generated method stub</span>
                <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> Tuple2&lt;String, Integer&gt; (word, <span class="hljs-number">1</span>);
            }

        });
     <span class="hljs-javadoc">/**
     * 第4.3步：在单词实例计数为1基础上，统计每个单词在文件中出现的总次数
     */</span>
        JavaPairDStream&lt;String, Integer&gt; wordsCount = pairs.reduceByKey(<span class="hljs-keyword">new</span> Function2&lt;Integer, Integer, Integer&gt;(){
            <span class="hljs-comment">//对相同的key，进行Value的累加（包括Local和Reducer级别同时Reduce）</span>
            <span class="hljs-keyword">public</span> Integer <span class="hljs-title">call</span>(Integer v1, Integer v2) <span class="hljs-keyword">throws</span> Exception {
                <span class="hljs-comment">// TODO Auto-generated method stub</span>
                <span class="hljs-keyword">return</span> v1 + v2;
            }

        });

        <span class="hljs-javadoc">/**
         * 此处的print并不会直接触发Job的支持，因为现在的一切都是在Spark Streaming框架的控制之下的，对于SparkStreaming
         * 而言，具体是否触发真正的Job运行是基于设置的Duration时间间隔的
         * 
         * 注意，Spark Streaming应用程序要想执行具体的Job，对DStream就必须有ouptputstream操作
         * outputstream有很多类型的函数触发，例如print,saveAsTextFile,saveAsHadoopFiles等，
         * 其中最为重要的一个方法是foreachRDD，因为Spark Streaming处理的结果一般会放在Redis、DB、DashBoard
         * 等上面，所以foreachRDD主要就是用来完成这些功能的，而且可以随意自定义具体数据到底放在哪里。
         */</span>
        wordsCount.print();


        <span class="hljs-javadoc">/**
         * Spark Streaming 执行引擎也就是Driver开始运行，Driver启动的时候是位于一条新的线程中的，当然其内部有消息循环体，用于
         * 接收应用程序本身或者Executor中的消息，
         */</span>
        javassc.start();
        javassc.awaitTermination();
        javassc.close();


    }

}</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>