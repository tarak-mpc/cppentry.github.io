---
layout:     post
title:      Spark Streaming-Kafka实例(Python与Java版本)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u013817676/article/details/81774543				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>本文实现kafka与Spark Streaming之间的通信，其中Kafka端producer实现使用Java，Spark Streaming端Consumer使用Python实现。</p>

<p>首先安装kafka与spark streaming环境，kafka测试连通测试参考上文，本文的实验环境都为本地单机版本。</p>

<p>Kafka</p>

<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">import</span> org.apache.kafka.clients.producer.*;
<span class="hljs-keyword">import</span> org.apache.kafka.common.serialization.StringSerializer;
<span class="hljs-keyword">import</span> java.util.Properties;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">producer</span> {</span>
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">final</span> <span class="hljs-keyword">static</span> String TOPIC = <span class="hljs-string">"data-message"</span>;
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">final</span> <span class="hljs-keyword">static</span> String BOOTSTRAP_SERVER = <span class="hljs-string">"127.0.0.1:9092"</span>;


    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> Producer&lt;String,String&gt; <span class="hljs-title">createProducer</span>() {
        Properties props = <span class="hljs-keyword">new</span> Properties();
        props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG,BOOTSTRAP_SERVER);
        props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());

        <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> KafkaProducer&lt;&gt;(props);
    }

    <span class="hljs-comment">// 实现自定义partition</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">int</span> <span class="hljs-title">partition</span>(<span class="hljs-keyword">long</span> time){
        <span class="hljs-keyword">if</span>(time%<span class="hljs-number">2</span> == <span class="hljs-number">0</span>)
            <span class="hljs-keyword">return</span> <span class="hljs-number">0</span>;
        <span class="hljs-keyword">else</span>
            <span class="hljs-keyword">return</span> <span class="hljs-number">1</span>;
    }
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">runProducer</span>() <span class="hljs-keyword">throws</span> Exception{
        <span class="hljs-keyword">final</span> Producer&lt;String,String&gt; producer = createProducer();
        <span class="hljs-keyword">long</span> time  = System.currentTimeMillis();
        <span class="hljs-keyword">long</span> curTime = time;
        <span class="hljs-keyword">try</span>{
            <span class="hljs-keyword">while</span>(<span class="hljs-keyword">true</span>){
                curTime = System.currentTimeMillis();
                <span class="hljs-keyword">if</span>(curTime-time == <span class="hljs-number">10000</span>){
                    <span class="hljs-keyword">final</span> ProducerRecord&lt;String,String&gt; record =
                            <span class="hljs-keyword">new</span> ProducerRecord&lt;&gt;(TOPIC, partition(curTime) ,<span class="hljs-string">"JP_"</span>+curTime,<span class="hljs-string">"AUX|989|bid|276|"</span>+curTime);
                    RecordMetadata metadata = producer.send(record).get();
                    <span class="hljs-keyword">long</span> elapsedTime = System.currentTimeMillis() - time;
                    System.out.printf(<span class="hljs-string">"sent record(key=%s value=%s) "</span> +
                            <span class="hljs-string">"meta(partition=%d, offset=%d) time=%d\n"</span>,
                    record.key(), record.value(), metadata.partition(),
                    metadata.offset(), elapsedTime);
                    curTime = time = System.currentTimeMillis();
                }
            }
        } <span class="hljs-keyword">finally</span> {
            producer.flush();
            producer.close();
        }   
    }
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> Exception{
            runProducer();
    }
}</code></pre>

<p>Spark Streaming实现了Spark Steaming两者通信方式，createStream和createDirectStream</p>

<pre class="prettyprint"><code class=" hljs python"><span class="hljs-keyword">import</span> os

<span class="hljs-keyword">from</span> pyspark <span class="hljs-keyword">import</span> SparkContext
<span class="hljs-keyword">from</span> pyspark.streaming <span class="hljs-keyword">import</span> StreamingContext
<span class="hljs-keyword">from</span> pyspark.streaming.kafka <span class="hljs-keyword">import</span> KafkaUtils
<span class="hljs-keyword">import</span> json
<span class="hljs-keyword">import</span> configparser

<span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">startReceiver</span><span class="hljs-params">(config,topics,ssc)</span>:</span>
    <span class="hljs-comment">#connect kafka</span>
    kafkaStreams = [KafkaUtils.createStream(ssc,config.get(<span class="hljs-string">'oppo'</span>,<span class="hljs-string">'zookeeper'</span>),
                                       config.get(<span class="hljs-string">'oppo'</span>,<span class="hljs-string">'consumer'</span>),topics) <span class="hljs-keyword">for</span> _ <span class="hljs-keyword">in</span> range(int(config.get(<span class="hljs-string">'oppo'</span>,<span class="hljs-string">'numStreams'</span>)))]
    uniStream = ssc.union(*kafkaStreams)

    stream = uniStream.map(<span class="hljs-keyword">lambda</span> x: x[<span class="hljs-number">0</span>])

    stream.pprint()

    ssc.start()
    ssc.awaitTermination()

<span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">startDirect</span><span class="hljs-params">(config,topic,ssc)</span>:</span>
    brokerList = config.get(<span class="hljs-string">'oppo'</span>,<span class="hljs-string">'brokerList'</span>)
    <span class="hljs-comment">#connect kafka</span>
    kafkaStreams = KafkaUtils.createDirectStream(ssc,[config.get(<span class="hljs-string">'oppo'</span>,<span class="hljs-string">'topic'</span>)],
                                                  {<span class="hljs-string">"metadata.broker.list"</span>:brokerList})
    stream = kafkaStreams.map(<span class="hljs-keyword">lambda</span> x: x[<span class="hljs-number">1</span>])
    stream.pprint()

    ssc.start()
    ssc.awaitTermination()

<span class="hljs-keyword">if</span> __name__ == <span class="hljs-string">'__main__'</span>:
    config = configparser.SafeConfigParser()
    config.read(<span class="hljs-string">"properties.conf"</span>)

    sc = SparkContext(appName=config.get(<span class="hljs-string">'oppo'</span>, <span class="hljs-string">'appName'</span>))
    sc.setLogLevel(config.get(<span class="hljs-string">'oppo'</span>, <span class="hljs-string">'logLevel'</span>))

    <span class="hljs-comment"># create Streaming Context</span>
    <span class="hljs-comment"># deal with internal 10 seconds</span>
    ssc = StreamingContext(sc, <span class="hljs-number">10</span>)

    topic = config.get(<span class="hljs-string">'oppo'</span>, <span class="hljs-string">'topic'</span>)
    topics = {topic: <span class="hljs-number">0</span>, topic: <span class="hljs-number">1</span>}

    <span class="hljs-comment">#startReceiver(config,topics,ssc)</span>
    startDirect(config,topic,ssc)</code></pre>

<p>properties.conf配置文件</p>



<pre class="prettyprint"><code class=" hljs ini"><span class="hljs-title">[oppo]</span>
<span class="hljs-setting">appName = <span class="hljs-value">SparkStreamingKafka</span></span>
<span class="hljs-setting">logLevel = <span class="hljs-value">WARN</span></span>
<span class="hljs-setting">topic = <span class="hljs-value">data-message</span></span>
<span class="hljs-setting">partitions = <span class="hljs-value"><span class="hljs-number">2</span></span></span>
<span class="hljs-setting">zookeeper=<span class="hljs-value"><span class="hljs-number">127.0</span>.<span class="hljs-number">0.1</span>:<span class="hljs-number">2181</span></span></span>
<span class="hljs-setting">numStreams = <span class="hljs-value"><span class="hljs-number">2</span></span></span>
<span class="hljs-setting">consumer = <span class="hljs-value">spark-streaming</span></span>
<span class="hljs-setting">brokerList=<span class="hljs-value"><span class="hljs-number">127.0</span>.<span class="hljs-number">0.1</span>:<span class="hljs-number">9092</span></span></span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>