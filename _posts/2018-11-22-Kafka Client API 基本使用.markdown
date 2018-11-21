---
layout:     post
title:      Kafka Client API 基本使用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/ZHBR_F1/article/details/73457478				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>之前讲过了<a href="http://blog.csdn.net/zhbr_f1/article/details/73457466" rel="nofollow" target="_blank">Kafka基本概念及原理</a>，这次我们来看看Kafka Client的API。要使用Kafka Client的API，首先需要先部署Kafka集群，部署过程请参见<a href="http://kafka.apache.org/quickstart" rel="nofollow" target="_blank">官网</a>。然后在项目中添加Kafka Client的依赖，在这里我们使用0.10.0.1版本：</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.kafka<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>kafka-clients<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>0.10.0.1<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
</code></pre>

<p>Kafka有以下四个核心API： <br>
- Producer <br>
- Consumer <br>
- Streams <br>
- Connect</p>

<p>这里我们来讲解比较基础的Producer和Consumer。假设读者已经按照<a href="http://kafka.apache.org/quickstart" rel="nofollow" target="_blank">官网</a>在本机配置好了Kafka服务，并创建了名为“test”的topic。</p>



<h2 id="producer-api">Producer API</h2>

<p>Producer用来向Kafka集群中发布消息记录的Kafka客户端。Producer是线程安全的，并且通常来讲，在多个线程间共享一个producer要比每个线程都创建一个producer速度更快。Producer的API相对比较简单，下面给出一个较为简单的API实例：</p>



<pre class="prettyprint"><code class=" hljs avrasm">import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Properties</span><span class="hljs-comment">;</span>

import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.clients</span><span class="hljs-preprocessor">.producer</span><span class="hljs-preprocessor">.KafkaProducer</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.clients</span><span class="hljs-preprocessor">.producer</span><span class="hljs-preprocessor">.Producer</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.clients</span><span class="hljs-preprocessor">.producer</span><span class="hljs-preprocessor">.ProducerRecord</span><span class="hljs-comment">;</span>

public class ProducerDemo {

    public static void main(String[] args) {
        Properties props = new Properties()<span class="hljs-comment">;</span>
        props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"bootstrap.servers"</span>, <span class="hljs-string">"localhost:9092"</span>)<span class="hljs-comment">;</span>
                props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"acks"</span>, <span class="hljs-string">"all"</span>)<span class="hljs-comment">;</span>
                props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"retries"</span>, <span class="hljs-number">0</span>)<span class="hljs-comment">;</span>
                props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"batch.size"</span>, <span class="hljs-number">16384</span>)<span class="hljs-comment">;</span>
                props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"linger.ms"</span>, <span class="hljs-number">1</span>)<span class="hljs-comment">;</span>
                props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"buffer.memory"</span>, <span class="hljs-number">33554432</span>)<span class="hljs-comment">;</span>
        props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"key.serializer"</span>, <span class="hljs-string">"org.apache.kafka.common.serialization.StringSerializer"</span>)<span class="hljs-comment">;</span>
        props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"value.serializer"</span>, <span class="hljs-string">"org.apache.kafka.common.serialization.StringSerializer"</span>)<span class="hljs-comment">;</span>

        Producer&lt;String, String&gt; producer = new KafkaProducer&lt;&gt;(props)<span class="hljs-comment">;</span>
        for (int i = <span class="hljs-number">0</span><span class="hljs-comment">; i &lt; 100; i++)</span>
            producer<span class="hljs-preprocessor">.send</span>(new ProducerRecord&lt;String, String&gt;(<span class="hljs-string">"test"</span>, Integer<span class="hljs-preprocessor">.toString</span>(i), Integer<span class="hljs-preprocessor">.toString</span>(i)))<span class="hljs-comment">;</span>

        producer<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>

    }
}</code></pre>

<p>Producer由一个持有未发送消息记录的资源池和一个用来向Kafka集群发送消息记录的后台IO线程组成。使用后未关闭producer将导致这些资源泄露。</p>

<p>send方法是异步的。当它被调用时，它会将消息记录添加到待发送缓冲区并立即返回。使用这种方式可以使生产者聚集一批消息记录后一起发送，从而提高效率。</p>

<p>ack 配置项用来控制producer要求leader确认多少消息后返回调用成功。当值为0时producer不需要等待任何确认消息。当值为1时只需要等待leader确认。当值为-1或all时需要全部ISR集合返回确认才可以返回成功。</p>

<p>当 retries &gt; 0 时，如果发送失败，会自动尝试重新发送数据。发送次数为retries设置的值。</p>

<p>buffer.memory、batch.size、linger.ms三个参数用来控制缓冲区大小和延迟发送时间，具体含义可以参考官方文档的配置。</p>

<p>bootstrap.servers 配置项处需要填写我们要发送到的Kafka集群地址。</p>

<p>key.serializer 和 value.serializer 指定使用什么序列化方式将用户提供的key和value进行序列化。 <br>
运行此程序，在$KAFKA_HOME目录下运行：</p>



<pre class="prettyprint"><code class=" hljs lasso">bin/kafka<span class="hljs-attribute">-console</span><span class="hljs-attribute">-consumer</span><span class="hljs-built_in">.</span>sh <span class="hljs-subst">--</span>bootstrap<span class="hljs-attribute">-server</span> localhost:<span class="hljs-number">9092</span> <span class="hljs-subst">--</span>topic test <span class="hljs-subst">--</span>from<span class="hljs-attribute">-beginning</span></code></pre>

<p>可以看到写入Kafka的消息。</p>



<h2 id="consumer-api">Consumer API</h2>

<p>Consumer的API分为High-level API和Low-level API。前者提供了高度抽象的API，使用起来简单、方便。因此本文将主要讲述High-level API。Low-level API提供了更强的控制能力，但使用起来较为繁琐。下面我们来看一种最简单的方式，自动确认offset：</p>



<pre class="prettyprint"><code class=" hljs avrasm">import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Arrays</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Properties</span><span class="hljs-comment">;</span>

import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.clients</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.ConsumerRecord</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.clients</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.ConsumerRecords</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.clients</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.KafkaConsumer</span><span class="hljs-comment">;</span>

public class AutoCommitConsumerDemo {

    public static void main(String[] args) {
         Properties props = new Properties()<span class="hljs-comment">;</span>
         props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"bootstrap.servers"</span>, <span class="hljs-string">"localhost:9092"</span>)<span class="hljs-comment">;</span>
         props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"group.id"</span>, <span class="hljs-string">"test"</span>)<span class="hljs-comment">;</span>
         props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"enable.auto.commit"</span>, <span class="hljs-string">"true"</span>)<span class="hljs-comment">;</span>
         props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"auto.commit.interval.ms"</span>, <span class="hljs-string">"1000"</span>)<span class="hljs-comment">;</span>
         props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"key.deserializer"</span>, <span class="hljs-string">"org.apache.kafka.common.serialization.StringDeserializer"</span>)<span class="hljs-comment">;</span>
         props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"value.deserializer"</span>, <span class="hljs-string">"org.apache.kafka.common.serialization.StringDeserializer"</span>)<span class="hljs-comment">;</span>
         @SuppressWarnings(<span class="hljs-string">"resource"</span>)
        KafkaConsumer&lt;String, String&gt; consumer = new KafkaConsumer&lt;&gt;(props)<span class="hljs-comment">;</span>
         consumer<span class="hljs-preprocessor">.subscribe</span>(Arrays<span class="hljs-preprocessor">.asList</span>(<span class="hljs-string">"test"</span>))<span class="hljs-comment">;</span>
         while (true) {
             ConsumerRecords&lt;String, String&gt; records = consumer<span class="hljs-preprocessor">.poll</span>(<span class="hljs-number">100</span>)<span class="hljs-comment">;</span>
             for (ConsumerRecord&lt;String, String&gt; record : records)
                 System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.printf</span>(<span class="hljs-string">"offset = %d, key = %s, value = %s%n"</span>, record<span class="hljs-preprocessor">.offset</span>(), record<span class="hljs-preprocessor">.key</span>(), record<span class="hljs-preprocessor">.value</span>())<span class="hljs-comment">;</span>
         }
    }
}</code></pre>

<p>bootstrap.servers配置项指定了consumer需要连接的服务器集群。多台服务器用“,”分隔。这个配置项就算只配置了一台server的地址，也可以通过这一台server发现集群中的其他服务器。但是为了避免这台server挂掉引发单点问题，所以把所有服务器地址列举上去是一个比较好的选择。</p>

<p>enable.auto.commit配置项指定了提交offset的方式为自动提交，auto.commit.interval.ms配置项配置了每次自动提交的时间间隔。</p>

<p>group.id即消费者组标签，本例中消费者组的名称为test。不了解消费者组的概念可以看我的这篇文章：<a href="http://blog.csdn.net/zhbr_f1/article/details/73457466" rel="nofollow" target="_blank">Kafka基本概念及原理</a>。</p>

<p>自动提交offset的方式非常简单，但多数情况下，我们不会使用自动提交的方式。因为不论从Kafka集群中拉取的数据是否被处理成功，offset都会被更新，也就是如果处理过程中出现错误可能会出现数据丢失的情况。所以多数情况下我们会选择手动提交方式：</p>



<pre class="prettyprint"><code class=" hljs avrasm">import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.ArrayList</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Arrays</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.List</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Properties</span><span class="hljs-comment">;</span>

import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.clients</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.ConsumerRecord</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.clients</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.ConsumerRecords</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.clients</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.KafkaConsumer</span><span class="hljs-comment">;</span>

public class ManualCommitConsumerDemo {

    public static void main(String[] args) {
         Properties props = new Properties()<span class="hljs-comment">;</span>
         props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"bootstrap.servers"</span>, <span class="hljs-string">"localhost:9092"</span>)<span class="hljs-comment">;</span>
         props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"group.id"</span>, <span class="hljs-string">"test"</span>)<span class="hljs-comment">;</span>
         props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"enable.auto.commit"</span>, <span class="hljs-string">"false"</span>)<span class="hljs-comment">;</span>
         props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"key.deserializer"</span>, <span class="hljs-string">"org.apache.kafka.common.serialization.StringDeserializer"</span>)<span class="hljs-comment">;</span>
         props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"value.deserializer"</span>, <span class="hljs-string">"org.apache.kafka.common.serialization.StringDeserializer"</span>)<span class="hljs-comment">;</span>
         @SuppressWarnings(<span class="hljs-string">"resource"</span>)
         KafkaConsumer&lt;String, String&gt; consumer = new KafkaConsumer&lt;&gt;(props)<span class="hljs-comment">;</span>
         consumer<span class="hljs-preprocessor">.subscribe</span>(Arrays<span class="hljs-preprocessor">.asList</span>(<span class="hljs-string">"foo"</span>, <span class="hljs-string">"bar"</span>))<span class="hljs-comment">;</span>
         final int minBatchSize = <span class="hljs-number">200</span><span class="hljs-comment">;</span>
         List&lt;ConsumerRecord&lt;String, String&gt;&gt; buffer = new ArrayList&lt;&gt;()<span class="hljs-comment">;</span>
         while (true) {
             ConsumerRecords&lt;String, String&gt; records = consumer<span class="hljs-preprocessor">.poll</span>(<span class="hljs-number">100</span>)<span class="hljs-comment">;</span>
             for (ConsumerRecord&lt;String, String&gt; record : records) {
                 buffer<span class="hljs-preprocessor">.add</span>(record)<span class="hljs-comment">;</span>
             }
             if (buffer<span class="hljs-preprocessor">.size</span>() &gt;= minBatchSize) {
                 insertIntoDb(buffer)<span class="hljs-comment">;</span>
                 consumer<span class="hljs-preprocessor">.commitSync</span>()<span class="hljs-comment">;</span>
                 buffer<span class="hljs-preprocessor">.clear</span>()<span class="hljs-comment">;</span>
             }
         }

    }

    private static void insertIntoDb(List&lt;ConsumerRecord&lt;String, String&gt;&gt; buffer) {
        // Insert into db
    }

}</code></pre>

<p>从对比自动提交offset的代码，我们看到 enable.auto.commit 配置项被设置为false，代表手动提交。代码中定义了一个ConsumerRecord的列表作为缓冲，当缓冲中的数据大于200条时，才一次性插入数据库中，并手动提交offset。这样，只有当数据成功插入数据库时才会更新offset，从而保证了数据不丢失。但如果在数据插入数据库后和手动提交offset之间这段时间（虽然很短，但也是有可能的）程序崩溃或服务器down机，那么再次启动会导致重复消费。所以这种方式其实是提供了 at least once 语义。</p>

<p>另外，consumer并不是线程安全的，所以在进行多线程操作时需要在每个线程实例化一个consumer。如果需要跨线程使用consumer，需要进行手动同步。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>