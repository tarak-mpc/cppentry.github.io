---
layout:     post
title:      大数据IMF传奇行动绝密课程第91课：SparkStreaming基于Kafka Direct案例实战和内幕源码解密
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="sparkstreaming基于kafka-direct案例实战和内幕源码解密">SparkStreaming基于Kafka Direct案例实战和内幕源码解密</h1>

<p>1、sparkStreaming on Kafka Direct工作原理机制 <br>
2、sparkStreaming on Kafka Direct案例实战 <br>
3、sparkStreaming on Kafka Direct源码解析</p>

<p>可以避免重复消费，RDD的Partition与Kafka的Partition对应</p>

<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">package</span> com.tom.spark.SparkApps.sparkstreaming;

<span class="hljs-keyword">import</span> java.util.HashMap;
<span class="hljs-keyword">import</span> java.util.HashSet;
<span class="hljs-keyword">import</span> java.util.Iterator;
<span class="hljs-keyword">import</span> java.util.Map;
<span class="hljs-keyword">import</span> java.util.Set;

<span class="hljs-keyword">import</span> kafka.serializer.StringDecoder;

<span class="hljs-keyword">import</span> org.apache.spark.SparkConf;
<span class="hljs-keyword">import</span> org.apache.spark.api.java.JavaPairRDD;
<span class="hljs-keyword">import</span> org.apache.spark.api.java.JavaRDD;
<span class="hljs-keyword">import</span> org.apache.spark.api.java.function.Function;
<span class="hljs-keyword">import</span> org.apache.spark.api.java.function.Function2;
<span class="hljs-keyword">import</span> org.apache.spark.api.java.function.PairFunction;
<span class="hljs-keyword">import</span> org.apache.spark.api.java.function.VoidFunction;
<span class="hljs-keyword">import</span> org.apache.spark.streaming.Durations;
<span class="hljs-keyword">import</span> org.apache.spark.streaming.api.java.JavaDStream;
<span class="hljs-keyword">import</span> org.apache.spark.streaming.api.java.JavaPairDStream;
<span class="hljs-keyword">import</span> org.apache.spark.streaming.api.java.JavaPairInputDStream;
<span class="hljs-keyword">import</span> org.apache.spark.streaming.api.java.JavaStreamingContext;
<span class="hljs-keyword">import</span> org.apache.spark.streaming.api.java.JavaStreamingContextFactory;
<span class="hljs-keyword">import</span> org.apache.spark.streaming.kafka.KafkaUtils;

<span class="hljs-keyword">import</span> scala.Tuple2;

<span class="hljs-javadoc">/**
 * 在线处理广告点击流
 */</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">SparkStreamingOnKafkaDirect</span> {</span>

    <span class="hljs-javadoc">/**
     *<span class="hljs-javadoctag"> @param</span> args
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) {
        <span class="hljs-comment">// TODO Auto-generated method stub</span>
        <span class="hljs-comment">//好处：1、checkpoint 2、工厂</span>
        <span class="hljs-keyword">final</span> SparkConf conf = <span class="hljs-keyword">new</span> SparkConf().setAppName(<span class="hljs-string">"SparkStreamingOnKafkaDirect"</span>).setMaster(<span class="hljs-string">"hdfs://Master:7077/"</span>);
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

        <span class="hljs-comment">//创建Kafka元数据来让Spark Streaming这个Kafka Consumer利用</span>

        Map&lt;String, String&gt; kafkaParameters = <span class="hljs-keyword">new</span> HashMap&lt;String, String&gt;();
        kafkaParameters.put(<span class="hljs-string">"metadata.broker.list"</span>, <span class="hljs-string">"Master:9092,Worker1:9092,Worker2:9092"</span>);

        Set&lt;String&gt; topics = <span class="hljs-keyword">new</span> HashSet&lt;String&gt;();
        topics.add(<span class="hljs-string">"AdClicked"</span>);

        JavaPairInputDStream&lt;String, String&gt; adClickedStreaming = KafkaUtils.createDirectStream(javassc, 
                String.class, String.class, 
                StringDecoder.class, StringDecoder.class,
                kafkaParameters, 
                topics);
        <span class="hljs-javadoc">/**
         * 广告点击的基本数据格式：timestamp、ip、userID、adID、province、city
         * 
         */</span>

        JavaPairDStream&lt;String, Long&gt; pairs = adClickedStreaming.mapToPair(<span class="hljs-keyword">new</span> PairFunction&lt;Tuple2&lt;String,String&gt;, String, Long&gt;() {

            <span class="hljs-keyword">public</span> Tuple2&lt;String, Long&gt; <span class="hljs-title">call</span>(Tuple2&lt;String, String&gt; t)
                    <span class="hljs-keyword">throws</span> Exception {
                <span class="hljs-comment">// TODO Auto-generated method stub</span>
                String[] splited = t._2.split(<span class="hljs-string">"\t"</span>);
                String timestamp = splited[<span class="hljs-number">0</span>]; <span class="hljs-comment">//yyyy-MM-dd</span>
                String ip = splited[<span class="hljs-number">1</span>];
                String userID = splited[<span class="hljs-number">2</span>];
                String adID = splited[<span class="hljs-number">3</span>];
                String province = splited[<span class="hljs-number">4</span>];
                String city = splited[<span class="hljs-number">5</span>];
                String clickedRecord = timestamp + <span class="hljs-string">"_"</span> + ip + <span class="hljs-string">"_"</span> + userID + <span class="hljs-string">"_"</span> + adID + <span class="hljs-string">"_"</span> + province + <span class="hljs-string">"_"</span> + city;
                <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> Tuple2&lt;String, Long&gt;(clickedRecord, <span class="hljs-number">1</span>L);
            }
        });


     <span class="hljs-javadoc">/**
     * 计算每个Batch Duration中每个User的广告点击量
     */</span>
        JavaPairDStream&lt;String, Long&gt; adClickedUsers = pairs.reduceByKey(<span class="hljs-keyword">new</span> Function2&lt;Long, Long, Long&gt;(){
            <span class="hljs-comment">//对相同的key，进行Value的累加（包括Local和Reducer级别同时Reduce）</span>
            <span class="hljs-keyword">public</span> Long <span class="hljs-title">call</span>(Long v1, Long v2) <span class="hljs-keyword">throws</span> Exception {
                <span class="hljs-comment">// TODO Auto-generated method stub</span>
                <span class="hljs-keyword">return</span> v1 + v2;
            }           
        });

        <span class="hljs-javadoc">/**
         * 计算出什么叫有效的点击
         * 1、复杂化的一般都是采用机器学习训练好模型直接在线进行过滤
         * 2、简单的？可以通过一个Batch Duration中的点击次数来判断是不是非法广告点击，但是实际上讲，非法广告
         * 点击程序会尽可能模拟真实的广告点击行为，所以通过一个Batch来判断是不完整的，我们需要对例如一天（也可以是每小时）的数据进行判断
         * 3、比在线机器学习退而求其次的做法如下：
         *  例如：一段时间内，同一个IP（MAC地址）有多个用户的账号访问
         *  例如：可以统计一天内一个用户点击广告的次数，如果一天点击同样的广告操作50次的话就列入黑名单
         *  
         *  黑名单有一个重要的特征：动态生成！所以每次每一个Batch Duration都要考虑是否有新的黑名单加入，此时黑名单需要存储起来
         *  具体存储在什么地方，存储在DB中即可
         *  
         *  例如邮件系统的“黑名单”，可以采用Spark Streaming不断监控每个用户的操作，如果用户发送邮件的频率超过某个值，可以
         *  暂时把用户列入黑名单，从而阻止用户过度怕、频繁地发送邮件
         *  
         */</span>
        JavaPairDStream&lt;String, Long&gt; filteredClickedInbatch = adClickedUsers.filter(<span class="hljs-keyword">new</span> Function&lt;Tuple2&lt;String,Long&gt;, Boolean&gt;() {

            <span class="hljs-keyword">public</span> Boolean <span class="hljs-title">call</span>(Tuple2&lt;String, Long&gt; v1) <span class="hljs-keyword">throws</span> Exception {
                <span class="hljs-comment">// 10s内&lt;=1才正常</span>
                <span class="hljs-keyword">if</span>(<span class="hljs-number">1</span> &lt; v1._2) {
                    <span class="hljs-comment">//更新一下黑名单的数据表</span>
                    <span class="hljs-keyword">return</span> <span class="hljs-keyword">false</span>;
                } <span class="hljs-keyword">else</span> {
                    <span class="hljs-keyword">return</span> <span class="hljs-keyword">true</span>;
                }
            }
        });

<span class="hljs-comment">//      filteredClickedInbatch.print();</span>
        filteredClickedInbatch.foreachRDD(<span class="hljs-keyword">new</span> Function&lt;JavaPairRDD&lt;String,Long&gt;, Void&gt;() {

            <span class="hljs-keyword">public</span> Void <span class="hljs-title">call</span>(JavaPairRDD&lt;String, Long&gt; rdd) <span class="hljs-keyword">throws</span> Exception {
                <span class="hljs-comment">// TODO Auto-generated method stub</span>
                rdd.foreachPartition(<span class="hljs-keyword">new</span> VoidFunction&lt;Iterator&lt;Tuple2&lt;String,Long&gt;&gt;&gt;() {

                    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">call</span>(Iterator&lt;Tuple2&lt;String, Long&gt;&gt; partition) <span class="hljs-keyword">throws</span> Exception {
                        <span class="hljs-comment">// 在这里我们使用数据库连接池的高效读写数据库的方式把数据写入数据库MySQL</span>
                        <span class="hljs-comment">// 由于传入的参数是一个Iterator类型的集合，所以为了更加高效地操作，我们需要批量处理</span>
                        <span class="hljs-comment">// 例如一次性插入1000条Record，使用insertBatch或者updateBatch类型的操作</span>
                        <span class="hljs-comment">// 插入的用户信息可以包含：userID、adIDclickedCount、time</span>
                        <span class="hljs-comment">// 这里有一个问题：可能出现两条记录的Key是一样的，此时就需要更新累加操作</span>

                    }
                });
                <span class="hljs-keyword">return</span> <span class="hljs-keyword">null</span>;
            }
        });
        JavaPairDStream&lt;String, Long&gt; blackListBasedOnHistory = filteredClickedInbatch.filter(<span class="hljs-keyword">new</span> Function&lt;Tuple2&lt;String,Long&gt;, Boolean&gt;() {

            <span class="hljs-keyword">public</span> Boolean <span class="hljs-title">call</span>(Tuple2&lt;String, Long&gt; v1) <span class="hljs-keyword">throws</span> Exception {
                <span class="hljs-comment">// 广告点击的基本数据格式：timestamp、ip、userID、adID、province、city</span>
                String[] splited = v1._1.split(<span class="hljs-string">"\t"</span>);
                String dateString = splited[<span class="hljs-number">0</span>];
                String userID = splited[<span class="hljs-number">2</span>];
                String adId = splited[<span class="hljs-number">3</span>];

                <span class="hljs-javadoc">/**
                 * 接下来根据date、userID、adID等条件去查询用户点击广告的数据表，获得总的点击次数
                 * 这个时候基于点击次数判断是否属于黑名单点击
                 */</span>
                <span class="hljs-keyword">int</span> clickedCountTotalToday = <span class="hljs-number">81</span>;
                <span class="hljs-keyword">if</span>(clickedCountTotalToday &gt; <span class="hljs-number">50</span>)
                    <span class="hljs-keyword">return</span> <span class="hljs-keyword">true</span>;

                <span class="hljs-keyword">else</span> <span class="hljs-keyword">return</span> <span class="hljs-keyword">false</span>;
            }
        });
        <span class="hljs-javadoc">/**
         * 必须对黑名单的整个RDD进行去重操作
         */</span>
        JavaDStream&lt;String&gt; blackListuserIDBasedOnHistory = blackListBasedOnHistory.map(<span class="hljs-keyword">new</span> Function&lt;Tuple2&lt;String,Long&gt;, String&gt;() {

            <span class="hljs-keyword">public</span> String <span class="hljs-title">call</span>(Tuple2&lt;String, Long&gt; v1) <span class="hljs-keyword">throws</span> Exception {
                <span class="hljs-comment">// TODO Auto-generated method stub</span>
                <span class="hljs-keyword">return</span> v1._1.split(<span class="hljs-string">"\t"</span>)[<span class="hljs-number">2</span>];            }

        });


         JavaDStream&lt;String&gt; blackListUniqueUserID = blackListuserIDBasedOnHistory.transform(<span class="hljs-keyword">new</span> Function&lt;JavaRDD&lt;String&gt;, JavaRDD&lt;String&gt;&gt;() {

            <span class="hljs-keyword">public</span> JavaRDD&lt;String&gt; <span class="hljs-title">call</span>(JavaRDD&lt;String&gt; rdd) <span class="hljs-keyword">throws</span> Exception {
                <span class="hljs-comment">// TODO Auto-generated method stub</span>
                <span class="hljs-keyword">return</span> rdd.distinct();
            }
        });

        <span class="hljs-comment">//下一步写入黑名单数据表中</span>

         blackListUniqueUserID.foreachRDD(<span class="hljs-keyword">new</span> Function&lt;JavaRDD&lt;String&gt;, Void&gt;() {

            <span class="hljs-keyword">public</span> Void <span class="hljs-title">call</span>(JavaRDD&lt;String&gt; rdd) <span class="hljs-keyword">throws</span> Exception {
                rdd.foreachPartition(<span class="hljs-keyword">new</span> VoidFunction&lt;Iterator&lt;String&gt;&gt;() {

                    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">call</span>(Iterator&lt;String&gt; t) <span class="hljs-keyword">throws</span> Exception {
                        <span class="hljs-comment">// 在这里我们使用数据库连接池的高效读写数据库的方式把数据写入数据库MySQL</span>
                        <span class="hljs-comment">// 由于传入的参数是一个Iterator类型的集合，所以为了更加高效地操作，我们需要批量处理</span>
                        <span class="hljs-comment">// 例如一次性插入1000条Record，使用insertBatch或者updateBatch类型的操作</span>
                        <span class="hljs-comment">// 插入的用户信息可以包含：userID、adIDclickedCount、time</span>
                        <span class="hljs-comment">// 此时直接插入黑名单数据表即可</span>

                    }
                });
                <span class="hljs-keyword">return</span> <span class="hljs-keyword">null</span>;
            }
        });

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