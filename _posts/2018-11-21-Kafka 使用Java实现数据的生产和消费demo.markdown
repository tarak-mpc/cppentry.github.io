---
layout:     post
title:      Kafka 使用Java实现数据的生产和消费demo
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，转载请标明出处！博客地址:http://blog.csdn.net/qazwsxpcm					https://blog.csdn.net/qazwsxpcm/article/details/79186668				</div>
								            <div id="content_views" class="markdown_views prism-atelier-sulphurpool-light">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="前言">前言</h2>

<p>在<a href="http://www.cnblogs.com/xuwujing/p/8361629.html" rel="nofollow">上一篇</a>中讲述如何搭建kafka集群，本篇则讲述如何简单的使用 kafka 。不过在使用kafka的时候，还是应该简单的了解下kafka。</p>

<h2 id="kafka的介绍">Kafka的介绍</h2>

<p>Kafka是一种高吞吐量的分布式发布订阅消息系统，它可以处理消费者规模的网站中的所有动作流数据。 <br>
Kafka 有如下特性： <br>
 - 以时间复杂度为O(1)的方式提供消息持久化能力，即使对TB级以上数据也能保证常数时间复杂度的访问性能。 <br>
 - 高吞吐率。即使在非常廉价的商用机器上也能做到单机支持每秒100K条以上消息的传输。 <br>
 - 支持Kafka Server间的消息分区，及分布式消费，同时保证每个Partition内的消息顺序传输。 <br>
 - 同时支持离线数据处理和实时数据处理。 <br>
 - Scale out：支持在线水平扩展。</p>



<h3 id="kafka的术语">kafka的术语</h3>

<ul>
<li>Broker：Kafka集群包含一个或多个服务器，这种服务器被称为broker。</li>
<li>Topic：每条发布到Kafka集群的消息都有一个类别，这个类别被称为Topic。（物理上不同Topic的消息分开存储，逻辑上一个Topic的消息虽然保存于一个或多个broker上但用户只需指定消息的Topic即可生产或消费数据而不必关心数据存于何处）</li>
<li>Partition：Partition是物理上的概念，每个Topic包含一个或多个Partition。</li>
<li>Producer：负责发布消息到Kafka broker。</li>
<li>Consumer：消息消费者，向Kafka broker读取消息的客户端。</li>
<li>Consumer Group:每个Consumer属于一个特定的Consumer Group（可为每个Consumer指定group name，若不指定group name则属于默认的group）。</li>
</ul>



<h3 id="kafka核心api">kafka核心Api</h3>

<p>kafka有四个核心API <br>
 - 应用程序使用producer API发布消息到1个或多个topic中。 <br>
 - 应用程序使用consumer API来订阅一个或多个topic,并处理产生的消息。 <br>
 - 应用程序使用streams API充当一个流处理器,从1个或多个topic消费输入流,并产生一个输出流到1个或多个topic,有效地将输入流转换到输出流。 <br>
 - connector API允许构建或运行可重复使用的生产者或消费者,将topic链接到现有的应用程序或数据系统。</p>

<p>示例图如下: <br>
 <img src="https://img-blog.csdn.net/20180128125655810?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcWF6d3N4cGNt/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h3 id="kafka-应用场景">kafka 应用场景</h3>

<ul>
<li>构建可在系统或应用程序之间可靠获取数据的实时流数据管道。</li>
<li>构建实时流应用程序，可以转换或响应数据流。</li>
</ul>

<p>以上介绍参考kafka官方文档。</p>



<h2 id="开发准备">开发准备</h2>

<p>如果我们要开发一个kafka的程序，应该做些什么呢？ <br>
首先，在搭建好kafka环境之后，我们要考虑的是我们是生产者还是消费者，也就是消息的发送者还是接受者。 <br>
不过在本篇中，生产者和消费者都会进行开发和讲解。</p>

<p>在大致的了解kafka之后，我们来开发第一个程序。 <br>
这里用的开发语言是Java，构建工具Maven。 <br>
Maven的依赖如下:</p>

<pre class="prettyprint"><code class=" hljs xml">    <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.kafka<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
         <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>kafka_2.12<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
         <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.0.0<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">scope</span>&gt;</span>provided<span class="hljs-tag">&lt;/<span class="hljs-title">scope</span>&gt;</span> 
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
             <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.kafka<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
             <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>kafka-clients<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
              <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.0.0<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.kafka<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>kafka-streams<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.0.0<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span></code></pre>

<h2 id="kafka-producer">Kafka Producer</h2>

<p>在开发生产的时候，先简单的介绍下kafka各种配置说明：</p>

<ul>
<li>bootstrap.servers： kafka的地址。</li>
<li>acks:消息的确认机制，默认值是0。 <br>
acks=0：如果设置为0，生产者不会等待kafka的响应。 <br>
acks=1：这个配置意味着kafka会把这条消息写到本地日志文件中，但是不会等待集群中其他机器的成功响应。 <br>
acks=all：这个配置意味着leader会等待所有的follower同步完成。这个确保消息不会丢失，除非kafka集群中所有机器挂掉。这是最强的可用性保证。</li>
<li>retries：配置为大于0的值的话，客户端会在消息发送失败时重新发送。</li>
<li>batch.size:当多条消息需要发送到同一个分区时，生产者会尝试合并网络请求。这会提高client和生产者的效率。</li>
<li>key.serializer: 键序列化，默认org.apache.kafka.common.serialization.StringDeserializer。</li>
<li>value.deserializer:值序列化，默认org.apache.kafka.common.serialization.StringDeserializer。 <br>
… <br>
还有更多配置，可以去查看官方文档，这里就不在说明了。 <br>
那么我们kafka 的producer配置如下:</li>
</ul>



<pre class="prettyprint"><code class=" hljs avrasm">        Properties props = new Properties()<span class="hljs-comment">;</span>
        props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"bootstrap.servers"</span>, <span class="hljs-string">"master:9092,slave1:9092,slave2:9092"</span>)<span class="hljs-comment">;</span>
        props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"acks"</span>, <span class="hljs-string">"all"</span>)<span class="hljs-comment">;</span>
        props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"retries"</span>, <span class="hljs-number">0</span>)<span class="hljs-comment">;</span>
        props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"batch.size"</span>, <span class="hljs-number">16384</span>)<span class="hljs-comment">;</span>
        props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"key.serializer"</span>, StringSerializer<span class="hljs-preprocessor">.class</span><span class="hljs-preprocessor">.getName</span>())<span class="hljs-comment">;</span>
        props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"value.serializer"</span>, StringSerializer<span class="hljs-preprocessor">.class</span><span class="hljs-preprocessor">.getName</span>())<span class="hljs-comment">;</span>
        KafkaProducer&lt;String, String&gt; producer = new KafkaProducer&lt;String, String&gt;(props)<span class="hljs-comment">;</span></code></pre>

<p>kafka的配置添加之后，我们便开始生产数据，生产数据代码只需如下就行:</p>

<pre class="prettyprint"><code class=" hljs vbnet"> producer.send(<span class="hljs-keyword">new</span> ProducerRecord&lt;<span class="hljs-built_in">String</span>, <span class="hljs-built_in">String</span>&gt;(topic,<span class="hljs-keyword">key</span>,value));</code></pre>

<ul>
<li>topic: 消息队列的名称，可以先行在kafka服务中进行创建。如果kafka中并未创建该topic，那么便会自动创建！</li>
<li>key:键值，也就是value对应的值，和Map类似。</li>
<li><p>value:要发送的数据，数据格式为String类型的。</p>

<p>在写好生产者程序之后，那我们先来生产吧！ <br>
我这里发送的消息为:</p></li>
</ul>



<pre class="prettyprint"><code class=" hljs javascript"> <span class="hljs-built_in">String</span> messageStr=<span class="hljs-string">"你好，这是第"</span>+messageNo+<span class="hljs-string">"条数据"</span>;</code></pre>

<p>并且只发送1000条就退出，结果如下: <br>
<img src="https://img-blog.csdn.net/20180128140154683?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcWF6d3N4cGNt/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>可以看到信息成功的打印了。 <br>
如果不想用程序进行验证程序是否发送成功，以及消息发送的准确性，可以在kafka服务器上使用命令查看。</p>

<h2 id="kafka-consumer">Kafka Consumer</h2>

<p>kafka消费这块应该来说是重点，毕竟大部分的时候，我们主要使用的是将数据进行消费。</p>

<p>kafka消费的配置如下:</p>

<ul>
<li>bootstrap.servers： kafka的地址。</li>
<li>group.id：组名 不同组名可以重复消费。例如你先使用了组名A消费了kafka的1000条数据，但是你还想再次进行消费这1000条数据，并且不想重新去产生，那么这里你只需要更改组名就可以重复消费了。</li>
<li>enable.auto.commit：是否自动提交，默认为true。</li>
<li>auto.commit.interval.ms: 从poll(拉)的回话处理时长。</li>
<li>session.timeout.ms:超时时间。</li>
<li>max.poll.records:一次最大拉取的条数。</li>
<li>auto.offset.reset：消费规则，默认earliest 。 <br>
earliest: 当各分区下有已提交的offset时，从提交的offset开始消费；无提交的offset时，从头开始消费 。 <br>
latest: 当各分区下有已提交的offset时，从提交的offset开始消费；无提交的offset时，消费新产生的该分区下的数据 。 <br>
none: topic各分区都存在已提交的offset时，从offset后开始消费；只要有一个分区不存在已提交的offset，则抛出异常。</li>
<li>key.serializer: 键序列化，默认org.apache.kafka.common.serialization.StringDeserializer。</li>
<li><p>value.deserializer:值序列化，默认org.apache.kafka.common.serialization.StringDeserializer。</p>

<p>那么我们kafka 的consumer配置如下:</p></li>
</ul>



<pre class="prettyprint"><code class=" hljs avrasm">    Properties props = new Properties()<span class="hljs-comment">;</span>
        props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"bootstrap.servers"</span>, <span class="hljs-string">"master:9092,slave1:9092,slave2:9092"</span>)<span class="hljs-comment">;</span>
        props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"group.id"</span>, GROUPID)<span class="hljs-comment">;</span>
        props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"enable.auto.commit"</span>, <span class="hljs-string">"true"</span>)<span class="hljs-comment">;</span>
        props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"auto.commit.interval.ms"</span>, <span class="hljs-string">"1000"</span>)<span class="hljs-comment">;</span>
        props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"session.timeout.ms"</span>, <span class="hljs-string">"30000"</span>)<span class="hljs-comment">;</span>
        props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"max.poll.records"</span>, <span class="hljs-number">1000</span>)<span class="hljs-comment">;</span>
        props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"auto.offset.reset"</span>, <span class="hljs-string">"earliest"</span>)<span class="hljs-comment">;</span>
        props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"key.deserializer"</span>, StringDeserializer<span class="hljs-preprocessor">.class</span><span class="hljs-preprocessor">.getName</span>())<span class="hljs-comment">;</span>
        props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"value.deserializer"</span>, StringDeserializer<span class="hljs-preprocessor">.class</span><span class="hljs-preprocessor">.getName</span>())<span class="hljs-comment">;</span>
        KafkaConsumer&lt;String, String&gt; consumer = new KafkaConsumer&lt;String, String&gt;(props)<span class="hljs-comment">;</span></code></pre>

<p>由于我这是设置的自动提交，所以消费代码如下: <br>
我们需要先订阅一个topic，也就是指定消费哪一个topic。</p>

<pre class="prettyprint"><code class=" hljs avrasm">consumer<span class="hljs-preprocessor">.subscribe</span>(Arrays<span class="hljs-preprocessor">.asList</span>(topic))<span class="hljs-comment">;</span></code></pre>

<p>订阅之后，我们再从kafka中拉取数据:</p>



<pre class="prettyprint"><code class=" hljs javascript">ConsumerRecords&lt;<span class="hljs-built_in">String</span>, <span class="hljs-built_in">String</span>&gt; msgList=consumer.poll(<span class="hljs-number">1000</span>);</code></pre>

<p>一般来说进行消费会使用监听，这里我们就用for(;;)来进行监听， 并且设置消费1000条就退出！ <br>
结果如下: <br>
<img src="https://img-blog.csdn.net/20180128142903484?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcWF6d3N4cGNt/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>可以看到我们这里已经成功消费了生产的数据了。</p>



<h2 id="代码">代码</h2>

<p>那么生产者和消费者的代码如下:</p>

<p><strong>生产者:</strong></p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">import</span> java.util.Properties;
<span class="hljs-keyword">import</span> org.apache.kafka.clients.producer.KafkaProducer;
<span class="hljs-keyword">import</span> org.apache.kafka.clients.producer.ProducerRecord;
<span class="hljs-keyword">import</span> org.apache.kafka.common.serialization.StringSerializer;

<span class="hljs-javadoc">/**
 * 
* Title: KafkaProducerTest
* Description: 
* kafka 生产者demo
* Version:1.0.0  
*<span class="hljs-javadoctag"> @author</span> pancm
*<span class="hljs-javadoctag"> @date</span> 2018年1月26日
 */</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">KafkaProducerTest</span> <span class="hljs-keyword">implements</span> <span class="hljs-title">Runnable</span> {</span>

    <span class="hljs-keyword">private</span> <span class="hljs-keyword">final</span> KafkaProducer&lt;String, String&gt; producer;
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">final</span> String topic;
    <span class="hljs-keyword">public</span> <span class="hljs-title">KafkaProducerTest</span>(String topicName) {
        Properties props = <span class="hljs-keyword">new</span> Properties();
        props.put(<span class="hljs-string">"bootstrap.servers"</span>, <span class="hljs-string">"master:9092,slave1:9092,slave2:9092"</span>);
        props.put(<span class="hljs-string">"acks"</span>, <span class="hljs-string">"all"</span>);
        props.put(<span class="hljs-string">"retries"</span>, <span class="hljs-number">0</span>);
        props.put(<span class="hljs-string">"batch.size"</span>, <span class="hljs-number">16384</span>);
        props.put(<span class="hljs-string">"key.serializer"</span>, StringSerializer.class.getName());
        props.put(<span class="hljs-string">"value.serializer"</span>, StringSerializer.class.getName());
        <span class="hljs-keyword">this</span>.producer = <span class="hljs-keyword">new</span> KafkaProducer&lt;String, String&gt;(props);
        <span class="hljs-keyword">this</span>.topic = topicName;
    }

    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">run</span>() {
        <span class="hljs-keyword">int</span> messageNo = <span class="hljs-number">1</span>;
        <span class="hljs-keyword">try</span> {
            <span class="hljs-keyword">for</span>(;;) {
                String messageStr=<span class="hljs-string">"你好，这是第"</span>+messageNo+<span class="hljs-string">"条数据"</span>;
                producer.send(<span class="hljs-keyword">new</span> ProducerRecord&lt;String, String&gt;(topic, <span class="hljs-string">"Message"</span>, messageStr));
                <span class="hljs-comment">//生产了100条就打印</span>
                <span class="hljs-keyword">if</span>(messageNo%<span class="hljs-number">100</span>==<span class="hljs-number">0</span>){
                    System.out.println(<span class="hljs-string">"发送的信息:"</span> + messageStr);
                }
                <span class="hljs-comment">//生产1000条就退出</span>
                <span class="hljs-keyword">if</span>(messageNo%<span class="hljs-number">1000</span>==<span class="hljs-number">0</span>){
                    System.out.println(<span class="hljs-string">"成功发送了"</span>+messageNo+<span class="hljs-string">"条"</span>);
                    <span class="hljs-keyword">break</span>;
                }
                messageNo++;
            }
        } <span class="hljs-keyword">catch</span> (Exception e) {
            e.printStackTrace();
        } <span class="hljs-keyword">finally</span> {
            producer.close();
        }
    }

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String args[]) {
        KafkaProducerTest test = <span class="hljs-keyword">new</span> KafkaProducerTest(<span class="hljs-string">"KAFKA_TEST"</span>);
        Thread thread = <span class="hljs-keyword">new</span> Thread(test);
        thread.start();
    }
}</code></pre>

<p><strong>消费者:</strong></p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">import</span> java.util.Arrays;
<span class="hljs-keyword">import</span> java.util.Properties;
<span class="hljs-keyword">import</span> org.apache.kafka.clients.consumer.ConsumerRecord;
<span class="hljs-keyword">import</span> org.apache.kafka.clients.consumer.ConsumerRecords;
<span class="hljs-keyword">import</span> org.apache.kafka.clients.consumer.KafkaConsumer;
<span class="hljs-keyword">import</span> org.apache.kafka.common.serialization.StringDeserializer;


<span class="hljs-javadoc">/**
 * 
* Title: KafkaConsumerTest
* Description: 
*  kafka消费者 demo
* Version:1.0.0  
*<span class="hljs-javadoctag"> @author</span> pancm
*<span class="hljs-javadoctag"> @date</span> 2018年1月26日
 */</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">KafkaConsumerTest</span> <span class="hljs-keyword">implements</span> <span class="hljs-title">Runnable</span> {</span>

    <span class="hljs-keyword">private</span> <span class="hljs-keyword">final</span> KafkaConsumer&lt;String, String&gt; consumer;
    <span class="hljs-keyword">private</span> ConsumerRecords&lt;String, String&gt; msgList;
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">final</span> String topic;
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> String GROUPID = <span class="hljs-string">"groupA"</span>;

    <span class="hljs-keyword">public</span> <span class="hljs-title">KafkaConsumerTest</span>(String topicName) {
        Properties props = <span class="hljs-keyword">new</span> Properties();
        props.put(<span class="hljs-string">"bootstrap.servers"</span>, <span class="hljs-string">"master:9092,slave1:9092,slave2:9092"</span>);
        props.put(<span class="hljs-string">"group.id"</span>, GROUPID);
        props.put(<span class="hljs-string">"enable.auto.commit"</span>, <span class="hljs-string">"true"</span>);
        props.put(<span class="hljs-string">"auto.commit.interval.ms"</span>, <span class="hljs-string">"1000"</span>);
        props.put(<span class="hljs-string">"session.timeout.ms"</span>, <span class="hljs-string">"30000"</span>);
        props.put(<span class="hljs-string">"auto.offset.reset"</span>, <span class="hljs-string">"earliest"</span>);
        props.put(<span class="hljs-string">"key.deserializer"</span>, StringDeserializer.class.getName());
        props.put(<span class="hljs-string">"value.deserializer"</span>, StringDeserializer.class.getName());
        <span class="hljs-keyword">this</span>.consumer = <span class="hljs-keyword">new</span> KafkaConsumer&lt;String, String&gt;(props);
        <span class="hljs-keyword">this</span>.topic = topicName;
        <span class="hljs-keyword">this</span>.consumer.subscribe(Arrays.asList(topic));
    }

    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">run</span>() {
        <span class="hljs-keyword">int</span> messageNo = <span class="hljs-number">1</span>;
        System.out.println(<span class="hljs-string">"---------开始消费---------"</span>);
        <span class="hljs-keyword">try</span> {
            <span class="hljs-keyword">for</span> (;;) {
                    msgList = consumer.poll(<span class="hljs-number">1000</span>);
                    <span class="hljs-keyword">if</span>(<span class="hljs-keyword">null</span>!=msgList&amp;&amp;msgList.count()&gt;<span class="hljs-number">0</span>){
                    <span class="hljs-keyword">for</span> (ConsumerRecord&lt;String, String&gt; record : msgList) {
                        <span class="hljs-comment">//消费100条就打印 ,但打印的数据不一定是这个规律的</span>
                        <span class="hljs-keyword">if</span>(messageNo%<span class="hljs-number">100</span>==<span class="hljs-number">0</span>){
                            System.out.println(messageNo+<span class="hljs-string">"=======receive: key = "</span> + record.key() + <span class="hljs-string">", value = "</span> + record.value()+<span class="hljs-string">" offset==="</span>+record.offset());
                        }
                        <span class="hljs-comment">//当消费了1000条就退出</span>
                        <span class="hljs-keyword">if</span>(messageNo%<span class="hljs-number">1000</span>==<span class="hljs-number">0</span>){
                            <span class="hljs-keyword">break</span>;
                        }
                        messageNo++;
                    }
                }<span class="hljs-keyword">else</span>{  
                    Thread.sleep(<span class="hljs-number">1000</span>);
                }
            }       
        } <span class="hljs-keyword">catch</span> (InterruptedException e) {
            e.printStackTrace();
        } <span class="hljs-keyword">finally</span> {
            consumer.close();
        }
    }  
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String args[]) {
        KafkaConsumerTest test1 = <span class="hljs-keyword">new</span> KafkaConsumerTest(<span class="hljs-string">"KAFKA_TEST"</span>);
        Thread thread1 = <span class="hljs-keyword">new</span> Thread(test1);
        thread1.start();
    }
}</code></pre>

<p><strong>注:</strong> master、slave1、slave2 是因为我在自己的环境做了关系映射，这个可以换成服务器的IP。</p>

<p>当然项目我放在Github上了，有兴趣的可以看看。 <a href="https://github.com/xuwujing/kafka" rel="nofollow">https://github.com/xuwujing/kafka</a></p>

<h2 id="总结">总结</h2>

<p>简单的开发一个kafka的程序需要以下步骤:</p>

<ol>
<li>成功搭建kafka服务器，并成功启动！</li>
<li>得到kafka服务信息，然后在代码中进行相应的配置。</li>
<li>配置完成之后，监听kafka中的消息队列是否有消息产生。</li>
<li>将产生的数据进行业务逻辑处理！</li>
</ol>

<p>kafka介绍参考官方文档: <br>
<a href="http://kafka.apache.org/intro" rel="nofollow">http://kafka.apache.org/intro</a></p>

<p>到此，本文就结束了，谢谢阅读！</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>