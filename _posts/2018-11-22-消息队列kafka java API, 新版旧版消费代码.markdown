---
layout:     post
title:      消息队列kafka java API, 新版旧版消费代码
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，转载请注明原文地址，谢谢~					https://blog.csdn.net/u011250851/article/details/53542120				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="一kafka-zookeeper环境配置">一、kafka / zookeeper环境配置</h2>

<p>（mac下可以使用以下进行配置）</p>

<ol>
<li>安装zookeeper ：brew install zookeeper</li>
<li>启动zookeeper：zkServer start <br>
<img src="https://img-blog.csdn.net/20161209165920469?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnJpZ2h0aW5nZm9yYW1iaXRpb24=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></li>
<li>安装kafka：brew install kafka</li>
<li>启动kafka服务：kafka-server-start /usr/local/etc/kafka/server.properties</li>
<li>新建topic :kafka-topics –create –topic kafkatopic –replication-factor 1 –partitions 1 –zookeeper localhost:2181</li>
<li>启动生产者与消费者：kafka-console-producer –broker-list localhost:9092 –sync –topic kafkatopic <br>
kafka-console-consumer –zookeeper localhost:2181 –topic kafkatopic –from-beginning  <br>
现在可以测试发送消息啦： <br>
<img src="https://img-blog.csdn.net/20161209170518726?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnJpZ2h0aW5nZm9yYW1iaXRpb24=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
<img src="https://img-blog.csdn.net/20161209171633961?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnJpZ2h0aW5nZm9yYW1iaXRpb24=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></li>
</ol>



<h2 id="二java中使用kafka">二、java中使用kafka</h2>

<ol>
<li>maven依赖：</li>
</ol>



<pre class="prettyprint"><code class=" hljs xml"> <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.kafka<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>kafka_2.10<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>0.8.2.0<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span></code></pre>

<ol>
<li>生产者：</li>
</ol>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">package</span> com.lenovo.m2.buy.purchase.webap.test;
<span class="hljs-keyword">import</span> java.util.Properties;
<span class="hljs-keyword">import</span> java.util.concurrent.TimeUnit;
<span class="hljs-keyword">import</span> kafka.javaapi.producer.Producer;
<span class="hljs-keyword">import</span> kafka.producer.KeyedMessage;
<span class="hljs-keyword">import</span> kafka.producer.ProducerConfig;
<span class="hljs-keyword">import</span> kafka.serializer.StringEncoder;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">kafkaProducer</span> <span class="hljs-keyword">extends</span> <span class="hljs-title">Thread</span>{</span>

    <span class="hljs-keyword">private</span> String topic;

    <span class="hljs-keyword">public</span> <span class="hljs-title">kafkaProducer</span>(String topic){
        <span class="hljs-keyword">super</span>();
        <span class="hljs-keyword">this</span>.topic = topic;
    }
    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">run</span>() {
        Producer producer = createProducer();
        <span class="hljs-keyword">int</span> i=<span class="hljs-number">0</span>;
        <span class="hljs-keyword">while</span>(<span class="hljs-keyword">true</span>){
            producer.send(<span class="hljs-keyword">new</span> KeyedMessage&lt;Integer, String&gt;(topic, <span class="hljs-string">"message: "</span> + i++));
            <span class="hljs-keyword">try</span> {
                TimeUnit.SECONDS.sleep(<span class="hljs-number">1</span>);
            } <span class="hljs-keyword">catch</span> (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }

    <span class="hljs-keyword">private</span> Producer <span class="hljs-title">createProducer</span>() {
        Properties properties = <span class="hljs-keyword">new</span> Properties();
        properties.put(<span class="hljs-string">"zookeeper.connect"</span>, <span class="hljs-string">"localhost:2181,localhost:2181,localhost:2181"</span>);<span class="hljs-comment">//声明zk</span>
        properties.put(<span class="hljs-string">"serializer.class"</span>, StringEncoder.class.getName());
        properties.put(<span class="hljs-string">"metadata.broker.list"</span>, <span class="hljs-string">"localhost:9092"</span>);<span class="hljs-comment">// 声明kafka broker,localhost:9093,localhost:9094</span>
        <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> Producer&lt;Integer, String&gt;(<span class="hljs-keyword">new</span> ProducerConfig(properties));
    }


    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) {
        <span class="hljs-keyword">new</span> kafkaProducer(<span class="hljs-string">"test"</span>).start();<span class="hljs-comment">// 使用kafka集群中创建好的主题 test</span>

    }

}
</code></pre>

<ol>
<li>消费者：</li>
</ol>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">package</span> com.lenovo.m2.buy.purchase.webap.test;
<span class="hljs-keyword">import</span> java.util.HashMap;
<span class="hljs-keyword">import</span> java.util.List;
<span class="hljs-keyword">import</span> java.util.Map;
<span class="hljs-keyword">import</span> java.util.Properties;

<span class="hljs-keyword">import</span> kafka.consumer.Consumer;
<span class="hljs-keyword">import</span> kafka.consumer.ConsumerConfig;
<span class="hljs-keyword">import</span> kafka.consumer.ConsumerIterator;
<span class="hljs-keyword">import</span> kafka.consumer.KafkaStream;
<span class="hljs-keyword">import</span> kafka.javaapi.consumer.ConsumerConnector;

<span class="hljs-javadoc">/**
 1. Created by D_xiao on 16/12/9.
 */</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">kafkaConsumer</span> <span class="hljs-keyword">extends</span> <span class="hljs-title">Thread</span>{</span>

    <span class="hljs-keyword">private</span> String topic;

    <span class="hljs-keyword">public</span> <span class="hljs-title">kafkaConsumer</span>(String topic){
        <span class="hljs-keyword">super</span>();
        <span class="hljs-keyword">this</span>.topic = topic;
    }
    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">run</span>() {
        ConsumerConnector consumer = createConsumer();
        Map&lt;String, Integer&gt; topicCountMap = <span class="hljs-keyword">new</span> HashMap&lt;String, Integer&gt;();
        topicCountMap.put(topic, <span class="hljs-number">1</span>); <span class="hljs-comment">// 一次从主题中获取一个数据</span>
        Map&lt;String, List&lt;KafkaStream&lt;<span class="hljs-keyword">byte</span>[], <span class="hljs-keyword">byte</span>[]&gt;&gt;&gt;  messageStreams = consumer.createMessageStreams(topicCountMap);
        KafkaStream&lt;<span class="hljs-keyword">byte</span>[], <span class="hljs-keyword">byte</span>[]&gt; stream = messageStreams.get(topic).get(<span class="hljs-number">0</span>);<span class="hljs-comment">// 获取每次接收到的这个数据</span>
        ConsumerIterator&lt;<span class="hljs-keyword">byte</span>[], <span class="hljs-keyword">byte</span>[]&gt; iterator =  stream.iterator();
        <span class="hljs-keyword">while</span>(iterator.hasNext()){
            String message = <span class="hljs-keyword">new</span> String(iterator.next().message());
            System.out.println(<span class="hljs-string">"接收到: "</span> + message);
        }
    }
    <span class="hljs-keyword">private</span> ConsumerConnector <span class="hljs-title">createConsumer</span>() {
        Properties properties = <span class="hljs-keyword">new</span> Properties();
        properties.put(<span class="hljs-string">"zookeeper.connect"</span>, <span class="hljs-string">"localhost:2181,localhost:2181,localhost:2181"</span>);<span class="hljs-comment">//声明zk</span>
        properties.put(<span class="hljs-string">"group.id"</span>, <span class="hljs-string">"group1"</span>);<span class="hljs-comment">// 必须要使用别的组名称， 如果生产者和消费者都在同一组，则不能访问同一组内的topic数据</span>
        <span class="hljs-keyword">return</span> Consumer.createJavaConsumerConnector(<span class="hljs-keyword">new</span> ConsumerConfig(properties));
    }

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) {
        <span class="hljs-keyword">new</span> kafkaConsumer(<span class="hljs-string">"test"</span>).start();<span class="hljs-comment">// 使用kafka集群中创建好的主题 test</span>

    }
}</code></pre>

<p><strong>先运行生产者，在运行消费者，消费端便可接收到消息啦~</strong> <br>
<img src="https://img-blog.csdn.net/20161209173141779?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnJpZ2h0aW5nZm9yYW1iaXRpb24=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
  <img src="https://img-blog.csdn.net/20161209173156920?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnJpZ2h0aW5nZm9yYW1iaXRpb24=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<hr>

<p>更新一下新版kafka 消费端代码， 新版纯java实现，新版jar包兼容旧版。 <br>
jar:</p>

<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.kafka<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>kafka_2.11<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>0.10.1.1<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span></code></pre>

<p>代码：</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">package</span> com.fengjr.elk.web.write;


<span class="hljs-keyword">import</span> java.util.*;

<span class="hljs-keyword">import</span> org.apache.kafka.clients.consumer.ConsumerRecord;
<span class="hljs-keyword">import</span> org.apache.kafka.clients.consumer.ConsumerRecords;
<span class="hljs-keyword">import</span> org.apache.kafka.clients.consumer.KafkaConsumer;
<span class="hljs-javadoc">/**
 * Created by D_xiao on 2018/4/10.
 */</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">kafkaConsumerTest1</span> <span class="hljs-keyword">extends</span> <span class="hljs-title">Thread</span>{</span>

    <span class="hljs-keyword">private</span> String topic;

    <span class="hljs-keyword">public</span> <span class="hljs-title">kafkaConsumerTest1</span>(String topic){
        <span class="hljs-keyword">super</span>();
        <span class="hljs-keyword">this</span>.topic = topic;
    }
    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">run</span>() {
        Properties props = <span class="hljs-keyword">new</span> Properties();
        props.put(<span class="hljs-string">"bootstrap.servers"</span>, <span class="hljs-string">"10.255.73.160:9092"</span>);
        props.put(<span class="hljs-string">"group.id"</span>, <span class="hljs-string">"test"</span>);
        props.put(<span class="hljs-string">"enable.auto.commit"</span>, <span class="hljs-string">"true"</span>);
        props.put(<span class="hljs-string">"auto.commit.interval.ms"</span>, <span class="hljs-string">"1000"</span>);
        props.put(<span class="hljs-string">"key.deserializer"</span>, <span class="hljs-string">"org.apache.kafka.common.serialization.StringDeserializer"</span>);
        props.put(<span class="hljs-string">"value.deserializer"</span>, <span class="hljs-string">"org.apache.kafka.common.serialization.StringDeserializer"</span>);
        KafkaConsumer&lt;String, String&gt; consumer = <span class="hljs-keyword">new</span> KafkaConsumer&lt;&gt;(props);
        consumer.subscribe(Arrays.asList(<span class="hljs-string">"app-log-all"</span>));
        <span class="hljs-keyword">while</span> (<span class="hljs-keyword">true</span>) {

            ConsumerRecords&lt;String, String&gt; records =  consumer.poll(<span class="hljs-number">100</span>);
            <span class="hljs-keyword">for</span>(ConsumerRecord&lt;String, String&gt; record : records){
                System.out.println(<span class="hljs-string">"partition:"</span>+ record.partition()+ <span class="hljs-string">",value:"</span> + record.value());
            }
        }

    }

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) {
        <span class="hljs-keyword">new</span> kafkaConsumerTest1(<span class="hljs-string">"app-log-all"</span>).start();<span class="hljs-comment">// 使用kafka集群中创建好的主题 test</span>

    }
}
</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>