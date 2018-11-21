---
layout:     post
title:      spark点点滴滴 —— spark streaming+kafka流式计算实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="概述">概述</h1>

<p>本篇不会讲spark streaming原理，会直接进入实战，因此建立在你对spark有了基本的了解基础之上。 <br>
不同于storm等流式计算框架的设计，spark streaming的流式计算框架本质上还是spark的批处理框架，只是将流式数据按时间维度切分为细粒度的批处理框架，因此了解spark的话spark streaming应该也不难理解。 <br>
我们以一张图来直观的看看spark streaming的基本原理： <br>
<img src="https://img-blog.csdn.net/20170329104033898?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzU3OTkwMDM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
下面我们直接进入spark streaming+kafka实战。</p>



<h1 id="实战">实战</h1>



<h2 id="环境">环境</h2>

<table>
<thead>
<tr>
  <th>环境</th>
  <th>说明</th>
</tr>
</thead>
<tbody><tr>
  <td>hadoop</td>
  <td>版本2.6</td>
</tr>
<tr>
  <td>spark</td>
  <td>版本2.0.2</td>
</tr>
<tr>
  <td>spark模式</td>
  <td>spark on yarn</td>
</tr>
<tr>
  <td>kafka</td>
  <td>版本0.8.2</td>
</tr>
</tbody></table>




<h2 id="场景描述">场景描述</h2>

<p>在实际生产环境中，我们采用spark streaming进行流式计算，数据源一般接kafka，输出方式有很多，有直接存储数据的，有发送给kafka消息队列供下游继续处理的，简单的视图如下： <br>
<img src="https://img-blog.csdn.net/20170331100646995?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzU3OTkwMDM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
当然也可以继续发送给kafka到下游，本篇我们讲的是直接发送到kafka消息队列的情况。 <br>
本篇我们要采用的场景是： <br>
假如我们kafka发送过来的是web请求日志，其中包含了请求的url，假如我们用流式计算来解析日志，提取出其中的url并发送出去。 <br>
包含url的日志格式形如：</p>



<pre class="prettyprint"><code class=" hljs bash">[<span class="hljs-number">29</span>/Mar/<span class="hljs-number">2017</span>:<span class="hljs-number">11</span>:<span class="hljs-number">00</span>:<span class="hljs-number">14</span> +<span class="hljs-number">0800</span>] <span class="hljs-string">"POST xxxxx?aaa=111&amp;bbb=222&amp;ccc=333 HTTP/1.1"</span> nYyU1pZQVFBQUFBJCQAAAAAAAAAAAEAAAANZUtcsrvKx8K~tv</code></pre>

<p>我们要做的是从杂乱的日志文本中提取出“GET/POST xxxx HTTP/1.1”这段数据，并发送给下游处理。 <br>
好，下面直接开始我们的实战。</p>



<h2 id="java版">java版</h2>

<p>先直接上代码：</p>



<h3 id="maven依赖">maven依赖</h3>



<pre class="prettyprint"><code class=" hljs xml">    <span class="hljs-tag">&lt;<span class="hljs-title">dependencies</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-core_2.10<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>2.0.2<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-streaming_2.10<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>2.0.2<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-streaming-kafka_2.10<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.5.1<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.kafka<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>kafka_2.10<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>0.8.2.1<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">dependencies</span>&gt;</span></code></pre>



<h3 id="kafkaproducer的封装"><strong>kafkaProducer的封装</strong></h3>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">KafkaProducerWrapper</span> {</span>
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> KafkaProducerWrapper kafkaProducerWrapper = <span class="hljs-keyword">null</span>;
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">boolean</span> inited = <span class="hljs-keyword">false</span>;

    <span class="hljs-keyword">private</span> <span class="hljs-title">KafkaProducerWrapper</span>() { }

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> KafkaProducerWrapper <span class="hljs-title">getInstance</span>() {
        <span class="hljs-keyword">if</span> (kafkaProducerWrapper == <span class="hljs-keyword">null</span>) {
            <span class="hljs-keyword">synchronized</span> (KafkaProducerWrapper.class) {
                <span class="hljs-keyword">if</span> (kafkaProducerWrapper == <span class="hljs-keyword">null</span>) {
                    kafkaProducerWrapper = <span class="hljs-keyword">new</span> KafkaProducerWrapper();
                }
            }
        }
        <span class="hljs-keyword">return</span> kafkaProducerWrapper;
    }
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">isInited</span>() { <span class="hljs-keyword">return</span> inited; }

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">init</span>(String kafkaPros) {
        <span class="hljs-comment">// todo</span>
        inited = <span class="hljs-keyword">true</span>;
    }

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">send</span>(String message) {
        <span class="hljs-comment">// todo</span>
    }
}
</code></pre>

<p>以上，是对kafka producer的封装，不详细写，参考<a href="http://blog.csdn.net/qq_35799003/article/details/52238744" rel="nofollow">kafka进击之路（五） ——producer API开发</a>。</p>



<h3 id="spark的运行代码"><strong>spark的运行代码</strong></h3>

<p>下面是要提交的spark的运行代码：</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">import</span> kafka.serializer.StringDecoder;
<span class="hljs-keyword">import</span> org.apache.spark.SparkConf;
<span class="hljs-keyword">import</span> org.apache.spark.api.java.JavaRDD;
<span class="hljs-keyword">import</span> org.apache.spark.api.java.function.Function;
<span class="hljs-keyword">import</span> org.apache.spark.api.java.function.VoidFunction;
<span class="hljs-keyword">import</span> org.apache.spark.streaming.Durations;
<span class="hljs-keyword">import</span> org.apache.spark.streaming.api.java.JavaDStream;
<span class="hljs-keyword">import</span> org.apache.spark.streaming.api.java.JavaPairInputDStream;
<span class="hljs-keyword">import</span> org.apache.spark.streaming.api.java.JavaStreamingContext;
<span class="hljs-keyword">import</span> org.apache.spark.streaming.kafka.KafkaUtils;
<span class="hljs-keyword">import</span> scala.Tuple2;
<span class="hljs-keyword">import</span> template.KafkaProducerWrapper;

<span class="hljs-keyword">import</span> java.util.HashMap;
<span class="hljs-keyword">import</span> java.util.HashSet;
<span class="hljs-keyword">import</span> java.util.Map;
<span class="hljs-keyword">import</span> java.util.Set;
<span class="hljs-keyword">import</span> java.util.regex.Matcher;
<span class="hljs-keyword">import</span> java.util.regex.Pattern;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">SparkMain</span> {</span>
    <span class="hljs-javadoc">/**
     * 匹配日志中的url
     */</span>
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> String filterReg = <span class="hljs-string">"(.*)([GET|POST].*\\?)(.*)(HTTP/1\\.1)(.*)"</span>;
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> String LOG_IGNORE = <span class="hljs-string">"IGNORE"</span>;
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> String <span class="hljs-title">parseLog</span>(String log) {
        <span class="hljs-keyword">if</span> (log == <span class="hljs-keyword">null</span>) {
            <span class="hljs-keyword">return</span> <span class="hljs-keyword">null</span>;
        }
        Pattern pattern = Pattern.compile(filterReg);
        Matcher matcher = pattern.matcher(log);
        <span class="hljs-keyword">if</span> (matcher.find()) {
            <span class="hljs-keyword">return</span> matcher.group(<span class="hljs-number">3</span>);
        } <span class="hljs-keyword">else</span> {
            <span class="hljs-keyword">return</span> <span class="hljs-keyword">null</span>;
        }
    }

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) {
        <span class="hljs-javadoc">/**
         * spark提交的appName
         */</span>
        String appName = <span class="hljs-string">"JavaSparkStreamingTest"</span>;
        <span class="hljs-javadoc">/**
         * spark streaming处理的时间片，即1s为一个时间单元
         */</span>
        <span class="hljs-keyword">int</span> sparkStreamingInterval = <span class="hljs-number">1000</span>;
        <span class="hljs-javadoc">/**
         * 读取的kafka topic集合，支持多个
         */</span>
        Set&lt;String&gt; topicsSet = <span class="hljs-keyword">new</span> HashSet&lt;String&gt;();
        topicsSet.add(<span class="hljs-string">"testTopic"</span>);
        <span class="hljs-javadoc">/**
         * kafka的broker配置
         */</span>
        String brokers = <span class="hljs-string">"x.x.x.1:9092,x.x.x.2:9092"</span>;
        Map&lt;String, String&gt; kafkaParams = <span class="hljs-keyword">new</span> HashMap&lt;String, String&gt;();
        kafkaParams.put(<span class="hljs-string">"metadata.broker.list"</span>, brokers);


        SparkConf sparkConf = <span class="hljs-keyword">new</span> SparkConf().setAppName(appName);

        JavaStreamingContext jssc = <span class="hljs-keyword">new</span> JavaStreamingContext(sparkConf, Durations.milliseconds(sparkStreamingInterval));

        <span class="hljs-javadoc">/**
         * 构造从kafka读取的数据源
         */</span>
        JavaPairInputDStream&lt;String, String&gt; logMessages = KafkaUtils.createDirectStream(
                jssc,
                String.class,
                String.class,
                StringDecoder.class,
                StringDecoder.class,
                kafkaParams,
                topicsSet
        );

        <span class="hljs-javadoc">/**
         * 执行map操作，解析日志
         */</span>
        <span class="hljs-keyword">final</span> JavaDStream&lt;String&gt; parsedurls = logMessages.map(<span class="hljs-keyword">new</span> Function&lt;Tuple2&lt;String, String&gt;, String&gt;() {
            <span class="hljs-keyword">public</span> String <span class="hljs-title">call</span>(Tuple2&lt;String, String&gt; stringStringTuple2) <span class="hljs-keyword">throws</span> Exception {
                String log = stringStringTuple2._2();
                String url = parseLog(log);
                <span class="hljs-keyword">if</span> (url == <span class="hljs-keyword">null</span>) {
                    <span class="hljs-keyword">return</span> LOG_IGNORE;
                } <span class="hljs-keyword">else</span> {
                    <span class="hljs-keyword">return</span> url;
                }
            }
        });

        <span class="hljs-javadoc">/**
         *  过滤操作，过滤非URL
         */</span>
        <span class="hljs-keyword">final</span> JavaDStream&lt;String&gt; urls = parsedurls.filter(<span class="hljs-keyword">new</span> Function&lt;String, Boolean&gt;() {
            <span class="hljs-keyword">public</span> Boolean <span class="hljs-title">call</span>(String s) <span class="hljs-keyword">throws</span> Exception {
                <span class="hljs-keyword">return</span> s == <span class="hljs-keyword">null</span> || s.equals(LOG_IGNORE);
            }
        });

        <span class="hljs-javadoc">/**
         * 遍历每个url，并发送到kafka
         */</span>
        urls.foreachRDD(<span class="hljs-keyword">new</span> VoidFunction&lt;JavaRDD&lt;String&gt;&gt;() {
            <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">call</span>(JavaRDD&lt;String&gt; stringJavaRDD) <span class="hljs-keyword">throws</span> Exception {
                <span class="hljs-comment">// 这个判断非常重要</span>
                <span class="hljs-keyword">if</span> (!KafkaProducerWrapper.getInstance().isInited()) {
                    <span class="hljs-comment">// 从resource文件初始化</span>
                    KafkaProducerWrapper.getInstance().init(<span class="hljs-string">"/producer.properties"</span>);
                }
                <span class="hljs-keyword">for</span> (String url : stringJavaRDD.collect()) {
                    KafkaProducerWrapper.getInstance().send(url);
                }
            }
        });

        <span class="hljs-javadoc">/**
         * 启动spark streaming
         */</span>
        jssc.start();
        <span class="hljs-keyword">try</span> {
            jssc.awaitTermination();
        } <span class="hljs-keyword">catch</span> (Exception e) {
            e.printStackTrace();
        }
    }
}</code></pre>



<h3 id="代码关键节点说明"><strong>代码关键节点说明</strong></h3>

<p>由于spark提交后是在分布式环境中运行，main函数中代码在driver中执行，转换和action的代码分发到各个节点执行，因此我们要考虑到同一份代码在不同节点中运行注意事项： <br>
1. <strong>初始化</strong> <br>
不能只在main函数里初始化，每个转换和action里用到需要初始化的对象，都要初始化，当然可以用单例模式； <br>
2. <strong>配置文件</strong> <br>
要读取配置文件，如果配置的是本地路径，则每个spark节点都需要在相同路径中有同一份配置文件；当然这是很不靠谱的事情，因此，我们可以采用HDFS上的文件或者工程中的资源文件（java中的resources目录），我们在上述示例中初始化kafka的producer使用的就是资源文件路径。</p>



<h3 id="spark作业提交命令"><strong>spark作业提交命令</strong></h3>

<pre><code>spark-submit --class SparkMain --master yarn-cluster --jars lib/spark-streaming-kafka_2.10-1.3.0.jar,lib/kafka-clients-0.8.2.1.jar,lib/scala-library-2.10.4.jar,lib/kafka_2.10-0.8.2.1.jar,lib/spark-streaming_2.10-2.0.2.jar ./spark-test.jar
</code></pre>



<h2 id="配置调优">配置调优</h2>

<p>如果按照上面的spark提交命令提交任务后，我们会发现，这些spark streaming任务跑不了多久就有可能会挂掉了，会出现各种 java.lang.OutOfMemoryError等堆错误或者GC问题，这在实际生产环境中没法保证spark streaming任务的7 * 24小时运行的。 <br>
实际工程中，要保证稳定性，我们还需要对很多参数进行调优处理，下面介绍下我在实际工程中处理这些配置中一些关键的配置。 <br>
关于spark提交任务有哪些配置，可以看<a href="http://spark.apache.org/docs/2.0.2/configuration.html" rel="nofollow">这里</a>。下面介绍对spark streaming任务有很大影响的配置的一些优化建议：</p>

<table>
<thead>
<tr>
  <th>spark任务参数</th>
  <th>说明</th>
  <th>默认值</th>
  <th>优化建议</th>
</tr>
</thead>
<tbody><tr>
  <td>spark.executor.memory</td>
  <td>executor内存大小</td>
  <td>1G</td>
  <td>看实际情况尽可能大，比如8G</td>
</tr>
<tr>
  <td>spark.driver.memory</td>
  <td>driver内存大小</td>
  <td>1G</td>
  <td>尽可能大，比如4G</td>
</tr>
<tr>
  <td>spark.executor.extraJavaOptions</td>
  <td>java参数</td>
  <td>无</td>
  <td>合适的GC算法，也可以打印gc日志来辅助定位问题</td>
</tr>
<tr>
  <td>spark.storage.memoryFraction</td>
  <td>spark任务缓存所占内存比例</td>
  <td>0.6</td>
  <td>如果实际任务不太需要使用缓存，尽可能小，比如0.1</td>
</tr>
<tr>
  <td>spark.cleaner.ttl</td>
  <td>spark缓存数据的周期</td>
  <td>默认infinite</td>
  <td>尽可能小，比如600秒</td>
</tr>
</tbody></table>


<p>特别最后两个参数，非常影响spark streaming任务的内存占用，要保证任务的稳定性，我们需要针对参数进行详细的优化配置。 <br>
调优后我们的提交命令如下：</p>

<pre><code>spark-submit --class SparkMain --master yarn-cluster --executor-memory 8G --driver-cores 2 --driver-memory 4G --conf spark.storage.memoryFraction=0.1 --conf spark.cleaner.ttl=3600 --jars lib/spark-streaming-kafka_2.10-1.3.0.jar,lib/kafka-clients-0.8.2.1.jar,lib/scala-library-2.10.4.jar,lib/kafka_2.10-0.8.2.1.jar,lib/spark-streaming_2.10-2.0.2.jar ./spark-test.jar
</code></pre>



<h1 id="结语">结语</h1>

<p>以上，就是一个典型的spark streaming+kafka的流式计算实战，从代码的编写到任务调优，就可以开始我们的spark流式计算之路了。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>