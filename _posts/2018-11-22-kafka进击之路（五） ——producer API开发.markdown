---
layout:     post
title:      kafka进击之路（五） ——producer API开发
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="说明">说明</h1>

<p>kafka版本：kafka_2.10-0.8.2.1（kafka0.9.xx版本提供了新的API）  <br>
IED环境：intellij14 + maven3.3  <br>
开发语言：java</p>



<h1 id="kafka-producer-api开发">kafka producer API开发</h1>

<p>kafka producer的开发使用起来要不consumer简单的多，不过我们要知道producer有两种方式，一种是同步，一种是异步。两种producer的发送结构如下： <br>
1. 同步producer <br>
<img src="https://img-blog.csdn.net/20160818101752393" alt="这里写图片描述" title=""> <br>
2. 异步producer <br>
<img src="https://img-blog.csdn.net/20160818101810269" alt="这里写图片描述" title=""></p>

<p>下面我们就写一个简单的异步kafka producer示例，在这里，我们额外对producer做一下封装，封装成一个单例模式的服务来供向kafka发送数据。</p>



<h4 id="maven依赖">maven依赖</h4>



<pre class="prettyprint"><code class="language-java hljs ">      &lt;dependency&gt;
            &lt;groupId&gt;org.apache.kafka&lt;/groupId&gt;
            &lt;artifactId&gt;kafka_2<span class="hljs-number">.10</span>&lt;/artifactId&gt;
            &lt;version&gt;<span class="hljs-number">0.8</span><span class="hljs-number">.2</span><span class="hljs-number">.1</span>&lt;/version&gt;
      &lt;/dependency&gt;</code></pre>



<h4 id="java包组成">java包组成</h4>

<p><img src="https://img-blog.csdn.net/20160818104556417" alt="这里写图片描述" title=""></p>



<h4 id="配置文件producerproperties">配置文件producer.properties</h4>



<pre class="prettyprint"><code class="language-java hljs ">bootstrap.servers=xxxx:<span class="hljs-number">9092</span>,xxxx:<span class="hljs-number">9092</span>,xxxx:<span class="hljs-number">9092</span>
batch.size=<span class="hljs-number">1</span>
retries=<span class="hljs-number">0</span>
acks=<span class="hljs-number">1</span>
block.on.buffer.full=<span class="hljs-keyword">true</span>
timeout.ms=<span class="hljs-number">30000</span>
reconnect.backoff.ms=<span class="hljs-number">10</span>
retry.backoff.ms = <span class="hljs-number">100</span>
max.in.flight.requests.per.connection = <span class="hljs-number">5</span>

buffer.memory = <span class="hljs-number">33554432</span>
send.buffer.bytes=<span class="hljs-number">131072</span>
receive.buffer.bytes=<span class="hljs-number">32768</span>
max.request.size=<span class="hljs-number">1048576</span>

key.serializer=org.apache.kafka.common.serialization.StringSerializer
value.serializer=org.apache.kafka.common.serialization.StringSerializer</code></pre>



<h4 id="kafkaproduceradapterjava代码">KafkaProducerAdapter.java代码</h4>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">package</span> kafka.producer;

<span class="hljs-keyword">import</span> org.apache.kafka.clients.producer.Callback;
<span class="hljs-keyword">import</span> org.apache.kafka.clients.producer.KafkaProducer;
<span class="hljs-keyword">import</span> org.apache.kafka.clients.producer.ProducerRecord;
<span class="hljs-keyword">import</span> org.apache.kafka.clients.producer.RecordMetadata;
<span class="hljs-keyword">import</span> org.apache.log4j.LogManager;
<span class="hljs-keyword">import</span> org.apache.log4j.Logger;

<span class="hljs-keyword">import</span> java.io.FileInputStream;
<span class="hljs-keyword">import</span> java.util.Properties;


<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">KafkaProducerAdapter</span> {</span>
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> Logger logger = LogManager.getLogger(KafkaProducerAdapter.class);

    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> KafkaProducer kafkaProducer = <span class="hljs-keyword">null</span>;

    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> KafkaProducerAdapter kafkaProducerAdapter = <span class="hljs-keyword">null</span>;

    <span class="hljs-keyword">private</span> <span class="hljs-title">KafkaProducerAdapter</span>() { }

    <span class="hljs-javadoc">/**
     * 单例
     *<span class="hljs-javadoctag"> @return</span>
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> KafkaProducerAdapter <span class="hljs-title">getInstance</span>() {
        <span class="hljs-keyword">if</span> (kafkaProducerAdapter == <span class="hljs-keyword">null</span>) {
            <span class="hljs-keyword">synchronized</span> (KafkaProducerAdapter.class) {
                <span class="hljs-keyword">if</span> (kafkaProducerAdapter == <span class="hljs-keyword">null</span>) {
                    kafkaProducerAdapter = <span class="hljs-keyword">new</span> KafkaProducerAdapter();
                }
            }
        }
        <span class="hljs-keyword">return</span> kafkaProducerAdapter;
    }

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">init</span>(String configFile) <span class="hljs-keyword">throws</span> Exception {
        <span class="hljs-keyword">try</span> {
            Properties properties = <span class="hljs-keyword">new</span> Properties();
            properties.load(<span class="hljs-keyword">new</span> FileInputStream(configFile));
            kafkaProducer = <span class="hljs-keyword">new</span> KafkaProducer(properties);
        } <span class="hljs-keyword">catch</span> (Exception e) {
            <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> Exception(<span class="hljs-string">"init kafka producer exception:"</span> + e.getMessage());
        }
    }

    <span class="hljs-javadoc">/**
     * 发送一条消息
     *<span class="hljs-javadoctag"> @param</span> message
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">send</span>(String topic, String message) {
        ProducerRecord record;
        record = <span class="hljs-keyword">new</span> ProducerRecord&lt;&gt;(topic, <span class="hljs-string">""</span>, message);
        kafkaProducer.send(record, <span class="hljs-keyword">new</span> SendCallback(record, <span class="hljs-number">0</span>));
    }

    <span class="hljs-javadoc">/**
     * producer回调
     */</span>
    <span class="hljs-keyword">static</span> class SendCallback implements Callback {
        ProducerRecord&lt;String, String&gt; record;
        <span class="hljs-keyword">int</span> sendSeq = <span class="hljs-number">0</span>;

        <span class="hljs-keyword">public</span> <span class="hljs-title">SendCallback</span>(ProducerRecord record, <span class="hljs-keyword">int</span> sendSeq) {
            <span class="hljs-keyword">this</span>.record = record;
            <span class="hljs-keyword">this</span>.sendSeq = sendSeq;
        }
        <span class="hljs-annotation">@Override</span>
        <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">onCompletion</span>(RecordMetadata recordMetadata, Exception e) {
            <span class="hljs-comment">//send success</span>
            <span class="hljs-keyword">if</span> (<span class="hljs-keyword">null</span> == e) {
                String meta = <span class="hljs-string">"topic:"</span> + recordMetadata.topic() + <span class="hljs-string">", partition:"</span>
                        + recordMetadata.topic() + <span class="hljs-string">", offset:"</span> + recordMetadata.offset();
                logger.info(<span class="hljs-string">"send message success, record:"</span> + record.toString() + <span class="hljs-string">", meta:"</span> + meta);
                <span class="hljs-keyword">return</span>;
            }
            <span class="hljs-comment">//send failed</span>
            logger.error(<span class="hljs-string">"send message failed, seq:"</span> + sendSeq + <span class="hljs-string">", record:"</span> + record.toString() + <span class="hljs-string">", errmsg:"</span> + e.getMessage());
            <span class="hljs-keyword">if</span> (sendSeq &lt; <span class="hljs-number">1</span>) {
                kafkaProducer.send(record, <span class="hljs-keyword">new</span> SendCallback(record, ++sendSeq));
            }
        }
    }
}</code></pre>

<h4 id="mainjava代码">Main.java代码</h4>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">package</span> kafka.producer;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">Main</span> {</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) {
        <span class="hljs-keyword">try</span> {
            KafkaProducerAdapter producer = KafkaProducerAdapter.getInstance();
            producer.init(<span class="hljs-string">"./producer.properties"</span>);

            <span class="hljs-comment">// send some message</span>
            producer.send(<span class="hljs-string">"test"</span>);
        } <span class="hljs-keyword">catch</span> (Exception e) {
            e.printStackTrace();
        }
    }
}</code></pre>

<hr>

<p>更多可参考： <br>
1. <a href="https://cwiki.apache.org/confluence/display/KAFKA/0.8.0+Producer+Example" rel="nofollow">https://cwiki.apache.org/confluence/display/KAFKA/0.8.0+Producer+Example</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>