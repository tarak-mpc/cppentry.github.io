---
layout:     post
title:      大数据IMF传奇行动绝密课程第90课：SparkStreaming基于Kafka Receiver案例实战和内幕源码解密
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="sparkstreaming基于kafka-receiver案例实战和内幕源码解密">SparkStreaming基于Kafka Receiver案例实战和内幕源码解密</h1>

<p>1、sparkStreaming on Kafka Receiver工作原理机制 <br>
2、sparkStreaming on Kafka Receiver案例实战 <br>
3、sparkStreaming on Kafka Receiver源码解析</p>

<p><img title="" alt="图90-1 sparkStreaming on Kafka Receiver工作原理机制" src="https://img-blog.csdn.net/20170403195353852?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdG9tXzg4OTlfbGk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast"></p>

<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">package</span> com.tom.spark.SparkApps.sparkstreaming;

<span class="hljs-keyword">import</span> java.util.Arrays;
<span class="hljs-keyword">import</span> java.util.HashMap;
<span class="hljs-keyword">import</span> java.util.Map;

<span class="hljs-keyword">import</span> org.apache.spark.SparkConf;
<span class="hljs-keyword">import</span> org.apache.spark.api.java.function.FlatMapFunction;
<span class="hljs-keyword">import</span> org.apache.spark.api.java.function.Function2;
<span class="hljs-keyword">import</span> org.apache.spark.api.java.function.PairFunction;
<span class="hljs-keyword">import</span> org.apache.spark.streaming.Durations;
<span class="hljs-keyword">import</span> org.apache.spark.streaming.api.java.JavaDStream;
<span class="hljs-keyword">import</span> org.apache.spark.streaming.api.java.JavaPairDStream;
<span class="hljs-keyword">import</span> org.apache.spark.streaming.api.java.JavaPairReceiverInputDStream;
<span class="hljs-keyword">import</span> org.apache.spark.streaming.api.java.JavaReceiverInputDStream;
<span class="hljs-keyword">import</span> org.apache.spark.streaming.api.java.JavaStreamingContext;
<span class="hljs-keyword">import</span> org.apache.spark.streaming.api.java.JavaStreamingContextFactory;
<span class="hljs-keyword">import</span> org.apache.spark.streaming.kafka.KafkaUtils;

<span class="hljs-keyword">import</span> scala.Tuple2;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">SparkStreamingOnKafkaReceiver</span> {</span>

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) {
        <span class="hljs-comment">// TODO Auto-generated method stub</span>
        <span class="hljs-comment">//好处：1、checkpoint 2、工厂</span>
        <span class="hljs-keyword">final</span> SparkConf conf = <span class="hljs-keyword">new</span> SparkConf().setAppName(<span class="hljs-string">"SparkStreamingOnKafkaReceiver"</span>).setMaster(<span class="hljs-string">"hdfs://Master:7077/"</span>);
        <span class="hljs-keyword">final</span> String checkpointDirectory = <span class="hljs-string">"hdfs://Master:9000/library/SparkStreaming/CheckPoint_Data"</span>;

        JavaStreamingContextFactory factory = <span class="hljs-keyword">new</span> JavaStreamingContextFactory() {

            <span class="hljs-keyword">public</span> JavaStreamingContext <span class="hljs-title">create</span>() {
                <span class="hljs-comment">// TODO Auto-generated method stub</span>
                <span class="hljs-keyword">return</span> createContext(checkpointDirectory, conf);
            }

        };

        <span class="hljs-javadoc">/**
         * 可以从失败中恢复Driver，不过还需要指定Driver这个进程运行在Cluster，并且在提交应用程序的时候制定--supervise;
         */</span>
        JavaStreamingContext javassc = JavaStreamingContext.getOrCreate(checkpointDirectory, factory);
        <span class="hljs-javadoc">/**
         * 第三步：创建Spark Streaming输入数据来源input Stream:
         * 1、数据输入来源可以基于File、HDFS、Flume、Kafka、Socket等
         * 2、在这里我们指定数据来源于网络Socket端口，Spark Streaming连接上该端口并在运行的时候一直监听该端口的数据
         *      (当然该端口服务首先必须存在），并且在后续会根据业务需要不断有数据产生（当然对于Spark Streaming
         *      应用程序的运行而言，有无数据其处理流程都是一样的）
         * 3、如果经常在每间隔5秒钟没有数据的话不断启动空的Job其实会造成调度资源的浪费，因为并没有数据需要发生计算；所以
         *      实际的企业级生成环境的代码在具体提交Job前会判断是否有数据，如果没有的话就不再提交Job；
         */</span>

        <span class="hljs-comment">//第一个参数是StreamingContext实例</span>
        <span class="hljs-comment">//第二个参数是ZooKeeper集群信息（接收Kafka数据的时候从ZooKeeper中获得Offset等元数据信息）</span>
        <span class="hljs-comment">//第三个参数是Consumer Group</span>
        <span class="hljs-comment">//第四个参数是消费的Topic以及并发读取Topic中Partition的线程数</span>
        Map&lt;String, Integer&gt; topicConsumerConcurrency = <span class="hljs-keyword">new</span> HashMap&lt;String, Integer&gt;();
        topicConsumerConcurrency.put(<span class="hljs-string">"HelloKafkaFromSparkStreaming"</span>, <span class="hljs-number">2</span>); 

        JavaPairReceiverInputDStream&lt;String, String&gt; lines = KafkaUtils.createStream(javassc,
                <span class="hljs-string">"Master:2181,Worker1:2181,Worker2:2181"</span>, 
                <span class="hljs-string">"MyFirstConsumerGroup"</span>, 
                topicConsumerConcurrency);
        <span class="hljs-javadoc">/**
         * 第四步：接下来就像对于RDD编程一样基于DStream进行编程，原因是DStream是RDD产生的模板（或者说类），在Spark Streaming具体
         * 发生计算前，其实质是把每个Batch的DStream的操作翻译成为对RDD的操作！
         * 
         */</span>
        JavaDStream&lt;String&gt; words = lines.flatMap(<span class="hljs-keyword">new</span> FlatMapFunction&lt;Tuple2&lt;String, String&gt;, String&gt;(){ <span class="hljs-comment">//如果是Scala，由于SAM转换，所以可以写成val words = lines.flatMap(_.split(" ")) </span>

            <span class="hljs-keyword">public</span> Iterable&lt;String&gt; <span class="hljs-title">call</span>(Tuple2&lt;String, String&gt; tuple) {
                <span class="hljs-keyword">return</span> Arrays.asList(tuple._2.split(<span class="hljs-string">" "</span>));
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

    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> JavaStreamingContext <span class="hljs-title">createContext</span>(String checkpointDirectory, SparkConf conf) {
        <span class="hljs-comment">// If you do not see this printed, that means the StreamingContext has been loaded</span>
        <span class="hljs-comment">// from the new checkpoint</span>
        System.out.println(<span class="hljs-string">"Creating new context"</span>);

        <span class="hljs-comment">// Create the context with a 5 second batch size</span>
        JavaStreamingContext ssc = <span class="hljs-keyword">new</span> JavaStreamingContext(conf, Durations.seconds(<span class="hljs-number">10</span>));
        ssc.checkpoint(checkpointDirectory);

        <span class="hljs-keyword">return</span> ssc;
    }
}</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>