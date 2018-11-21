---
layout:     post
title:      Apache Kafka 入门 - Kafka API 简单用法
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h3 class="article_t" style="font-family:Arial, 'Microsoft YaHei';font-weight:500;line-height:1.2;color:rgb(51,51,51);">
<span style="color:inherit;font-family:inherit;font-size:2.6em;line-height:1.1;">Apache Kafka 入门</span></h3>
<div class="article_c" style="color:rgb(51,51,51);font-family:sans-serif;">
<div class="markdown_views" style="font-family:'microsoft yahei';font-size:16px;color:rgb(63,63,63);">
<ul style="font-family:Arial, 'Microsoft YaHei';list-style:none;line-height:27.2000007629395px;"><li style="list-style:none;">Kafka的基本配置和运行</li><li style="list-style:none;">Kafka命令详细介绍</li><li style="list-style:none;">Kafka-manager的基本配置和运行</li><li style="list-style:none;">Kafka API 简单用法</li><li style="list-style:none;">Spring Boot 集成Kafka</li></ul><p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
本篇为第四篇。</p>
<blockquote style="border-left-width:10px;border-left-style:solid;line-height:27.2000007629395px;">
<p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
第四篇和第五篇源码下载： <br>
链接：<a href="http://pan.baidu.com/s/1dE4vpBj" rel="nofollow" style="color:rgb(79,161,219);text-decoration:none;background:transparent;">http://pan.baidu.com/s/1dE4vpBj</a> 密码：j74j</p>
</blockquote>
<h2 id="kafka-api-简单用法" style="font-family:inherit;font-weight:300;line-height:1.1;color:inherit;font-size:2.15em;">
Kafka API 简单用法</h2>
<p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
本篇会用到以下依赖：</p>
<pre class="prettyprint" style="font-family:'Source Code Pro', monospace;font-size:14px;line-height:23.8px;color:rgb(51,51,51);overflow:hidden;"><code class="language-xml hljs has-numbering" style="font-family:'Source Code Pro', monospace;font-size:12.6000003814697px;color:inherit;display:block;background:transparent;"><span class="hljs-tag" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,102,102);">&lt;<span class="hljs-title" style="color:rgb(0,0,136);">dependency</span>&gt;</span>
    <span class="hljs-tag" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,102,102);">&lt;<span class="hljs-title" style="color:rgb(0,0,136);">groupId</span>&gt;</span>org.apache.kafka<span class="hljs-tag" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,102,102);">&lt;/<span class="hljs-title" style="color:rgb(0,0,136);">groupId</span>&gt;</span>
    <span class="hljs-tag" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,102,102);">&lt;<span class="hljs-title" style="color:rgb(0,0,136);">artifactId</span>&gt;</span>kafka-clients<span class="hljs-tag" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,102,102);">&lt;/<span class="hljs-title" style="color:rgb(0,0,136);">artifactId</span>&gt;</span>
    <span class="hljs-tag" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,102,102);">&lt;<span class="hljs-title" style="color:rgb(0,0,136);">version</span>&gt;</span>0.10.2.0<span class="hljs-tag" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,102,102);">&lt;/<span class="hljs-title" style="color:rgb(0,0,136);">version</span>&gt;</span>
<span class="hljs-tag" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,102,102);">&lt;/<span class="hljs-title" style="color:rgb(0,0,136);">dependency</span>&gt;</span>

<span class="hljs-tag" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,102,102);">&lt;<span class="hljs-title" style="color:rgb(0,0,136);">dependency</span>&gt;</span>
    <span class="hljs-tag" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,102,102);">&lt;<span class="hljs-title" style="color:rgb(0,0,136);">groupId</span>&gt;</span>org.apache.kafka<span class="hljs-tag" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,102,102);">&lt;/<span class="hljs-title" style="color:rgb(0,0,136);">groupId</span>&gt;</span>
    <span class="hljs-tag" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,102,102);">&lt;<span class="hljs-title" style="color:rgb(0,0,136);">artifactId</span>&gt;</span>kafka-streams<span class="hljs-tag" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,102,102);">&lt;/<span class="hljs-title" style="color:rgb(0,0,136);">artifactId</span>&gt;</span>
    <span class="hljs-tag" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,102,102);">&lt;<span class="hljs-title" style="color:rgb(0,0,136);">version</span>&gt;</span>0.10.2.0<span class="hljs-tag" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,102,102);">&lt;/<span class="hljs-title" style="color:rgb(0,0,136);">version</span>&gt;</span>
<span class="hljs-tag" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,102,102);">&lt;/<span class="hljs-title" style="color:rgb(0,0,136);">dependency</span>&gt;</span></code></pre><ul class="pre-numbering" style="font-family:Arial, 'Microsoft YaHei';list-style:none;width:50px;line-height:23.7999992370605px;border-right-width:1px;border-right-style:solid;border-right-color:rgb(221,221,221);text-align:right;background-color:rgb(238,238,238);"><li style="list-style:none;">1</li><li style="list-style:none;">2</li><li style="list-style:none;">3</li><li style="list-style:none;">4</li><li style="list-style:none;">5</li><li style="list-style:none;">6</li><li style="list-style:none;">7</li><li style="list-style:none;">8</li><li style="list-style:none;">9</li><li style="list-style:none;">10</li><li style="list-style:none;">11</li></ul><h3 id="生产者api" style="font-family:inherit;font-weight:300;line-height:1.1;color:inherit;font-size:1.7em;">
生产者API</h3>
<p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
参考官方文档中 <a href="https://kafka.apache.org/0102/javadoc/index.html?org/apache/kafka/clients/producer/KafkaProducer.html" rel="nofollow" style="color:rgb(79,161,219);text-decoration:none;background:transparent;">KafkaProducer</a> 的介绍。</p>
<p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
Kafka客户端用于向 Kafka 集群发布记录。生产者是<span>线程安全的</span>，跨线程共享一个生产者实例通常比拥有多个实例要快。这是一个简单的例子，使用生产者发送包含序列号的字符串作为键/值对的记录，代码如下。</p>
<pre class="prettyprint" style="font-family:'Source Code Pro', monospace;font-size:14px;line-height:23.8px;color:rgb(51,51,51);overflow:hidden;"><code class="language-java hljs has-numbering" style="font-family:'Source Code Pro', monospace;font-size:12.6000003814697px;color:inherit;display:block;background:transparent;"><span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">package</span> com.github.abel533.kafka.api;

<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">import</span> org.apache.kafka.clients.producer.KafkaProducer;
<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">import</span> org.apache.kafka.clients.producer.Producer;
<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">import</span> org.apache.kafka.clients.producer.ProducerRecord;

<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">import</span> java.util.Properties;

<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">public</span> <span class="hljs-class" style="font-family:Arial, 'Microsoft YaHei';"><span class="hljs-keyword" style="color:rgb(0,0,136);">class</span> <span class="hljs-title" style="color:rgb(102,0,102);">ProducerApi</span> {</span>

    <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">public</span> <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">static</span> <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">void</span> <span class="hljs-title" style="font-family:Arial, 'Microsoft YaHei';">main</span>(String[] args) {
        Properties props = <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">new</span> Properties();
        props.put(<span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"bootstrap.servers"</span>, <span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"192.168.16.150:9092"</span>);
        props.put(<span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"acks"</span>, <span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"all"</span>);
        props.put(<span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"retries"</span>, <span class="hljs-number" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,102,102);">0</span>);
        props.put(<span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"batch.size"</span>, <span class="hljs-number" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,102,102);">16384</span>);
        props.put(<span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"linger.ms"</span>, <span class="hljs-number" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,102,102);">1</span>);
        props.put(<span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"buffer.memory"</span>, <span class="hljs-number" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,102,102);">33554432</span>);
        props.put(<span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"key.serializer"</span>,
                  <span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"org.apache.kafka.common.serialization.StringSerializer"</span>);
        props.put(<span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"value.serializer"</span>,
                  <span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"org.apache.kafka.common.serialization.StringSerializer"</span>);

        Producer&lt;String, String&gt; producer = <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">new</span> KafkaProducer&lt;&gt;(props);
        <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">for</span> (<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">int</span> i = <span class="hljs-number" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,102,102);">0</span>; i &lt; <span class="hljs-number" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,102,102);">100</span>; i++) {
            producer.send(<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">new</span> ProducerRecord&lt;String, String&gt;(
                      <span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"t1"</span>, Integer.toString(i), Integer.toString(i)));
        }
        producer.close();
    }
}</code></pre><ul class="pre-numbering" style="font-family:Arial, 'Microsoft YaHei';list-style:none;width:50px;line-height:23.7999992370605px;border-right-width:1px;border-right-style:solid;border-right-color:rgb(221,221,221);text-align:right;background-color:rgb(238,238,238);"><li style="list-style:none;">1</li><li style="list-style:none;">2</li><li style="list-style:none;">3</li><li style="list-style:none;">4</li><li style="list-style:none;">5</li><li style="list-style:none;">6</li><li style="list-style:none;">7</li><li style="list-style:none;">8</li><li style="list-style:none;">9</li><li style="list-style:none;">10</li><li style="list-style:none;">11</li><li style="list-style:none;">12</li><li style="list-style:none;">13</li><li style="list-style:none;">14</li><li style="list-style:none;">15</li><li style="list-style:none;">16</li><li style="list-style:none;">17</li><li style="list-style:none;">18</li><li style="list-style:none;">19</li><li style="list-style:none;">20</li><li style="list-style:none;">21</li><li style="list-style:none;">22</li><li style="list-style:none;">23</li><li style="list-style:none;">24</li><li style="list-style:none;">25</li><li style="list-style:none;">26</li><li style="list-style:none;">27</li><li style="list-style:none;">28</li><li style="list-style:none;">29</li><li style="list-style:none;">30</li><li style="list-style:none;">31</li></ul><p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
生产者包括一个缓冲区池，它保存尚未发送到服务器的记录，以及一个后台I/O线程，负责将这些记录转换为请求并将其传输到集群。使用后未能关闭生产者将泄漏这些资源。</p>
<p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
该<a href="https://kafka.apache.org/0102/javadoc/org/apache/kafka/clients/producer/KafkaProducer.html#send%28org.apache.kafka.clients.producer.ProducerRecord%29" rel="nofollow" style="color:rgb(79,161,219);text-decoration:none;background:transparent;"><code style="font-family:'Source Code Pro', monospace;font-size:25.2000007629395px;color:rgb(63,63,63);">send()</code></a>方法是异步的。当被调用时，它将记录添加到待处理记录发送的缓冲区并立即返回。这允许生产者将各个记录收集在一起以获得效率。</p>
<p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
<code style="font-family:'Source Code Pro', monospace;font-size:25.2000007629395px;color:rgb(63,63,63);">acks</code>配置其请求被视为完整性的标准。<code style="font-family:'Source Code Pro', monospace;font-size:25.2000007629395px;color:rgb(63,63,63);">"all"</code>意味着领导者将等待完整的同步副本来确认记录。只要至少有一个同步复制品仍然存在，这将保证记录不会丢失。这是最强大的保证。这相当于设置acks
 = -1。</p>
<p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
如果请求失败，生产者可以自动重试，但是由于我们指定<code style="font-family:'Source Code Pro', monospace;font-size:25.2000007629395px;color:rgb(63,63,63);">retries</code> 为<code style="font-family:'Source Code Pro', monospace;font-size:25.2000007629395px;color:rgb(63,63,63);">0</code>，所以不会重试。启用重试还会产生重复的可能性（有关详细信息，请参阅有关<a href="http://kafka.apache.org/documentation.html#semantics" rel="nofollow" style="color:rgb(79,161,219);text-decoration:none;background:transparent;">消息传递语义</a>的文档
 ）。</p>
<p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
生产者维护每个分区的未发送出去的缓冲区。这些缓冲区的大小由<code style="font-family:'Source Code Pro', monospace;font-size:25.2000007629395px;color:rgb(63,63,63);">batch.size</code>指定。使此更大可以缓存更多，但需要更多的内存（因为我们通常会为每个活动分区提供缓冲区）。</p>
<p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
默认情况下，即使缓冲区中存在额外的未使用空间，缓冲区也可立即发送。但是，如果要减少请求数可以设置<code style="font-family:'Source Code Pro', monospace;font-size:25.2000007629395px;color:rgb(63,63,63);">linger.ms</code>为大于0 的毫秒数。这将指示生产者在发送请求之前等待该毫秒数，这样将有更多记录到达缓冲区。这类似于Nagle在TCP中的算法。例如，在上面的代码片段中，可能所有100条记录都将在单个请求中发送，因为我们将延迟时间设置为1毫秒。但是，如果我们没有填满缓冲区，则此设置会为我们的请求增加1毫秒的延迟，以便等待更多记录到达。在重负荷下
 ，即使<code style="font-family:'Source Code Pro', monospace;font-size:25.2000007629395px;color:rgb(63,63,63);">linger.ms=0</code>，在时间上紧接在一起的记录也将一起批量处理。将其设置为大于0的值可能会让请求更少和更高效，而不是在最大负载下以少量延迟为代价。</p>
<p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
<code style="font-family:'Source Code Pro', monospace;font-size:25.2000007629395px;color:rgb(63,63,63);">buffer.memory</code>控制生产者可用于缓冲的总内存量。如果记录的发送速度比可以传输到服务器的速度快，那么这个缓冲空间就会耗尽。当缓冲区空间耗尽时，附加的发送呼叫将被阻塞。<code style="font-family:'Source Code Pro', monospace;font-size:25.2000007629395px;color:rgb(63,63,63);">max.block.ms</code>决定阻塞时间的阈值，超出此时间时，会引发<code style="font-family:'Source Code Pro', monospace;font-size:25.2000007629395px;color:rgb(63,63,63);">TimeoutException</code>。</p>
<p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
<code style="font-family:'Source Code Pro', monospace;font-size:25.2000007629395px;color:rgb(63,63,63);">key.serializer</code>和<code style="font-family:'Source Code Pro', monospace;font-size:25.2000007629395px;color:rgb(63,63,63);">value.serializer</code>指导如何将用户提供的<code style="font-family:'Source Code Pro', monospace;font-size:25.2000007629395px;color:rgb(63,63,63);">ProducerRecord</code>的键和值转换成字节。您可以使用提供的<a href="https://kafka.apache.org/0102/javadoc/org/apache/kafka/common/serialization/ByteArraySerializer.html" rel="nofollow" style="color:rgb(79,161,219);text-decoration:none;background:transparent;"><code style="font-family:'Source Code Pro', monospace;font-size:25.2000007629395px;color:rgb(63,63,63);">ByteArraySerializer</code></a>或 <a href="https://kafka.apache.org/0102/javadoc/org/apache/kafka/common/serialization/StringSerializer.html" rel="nofollow" style="color:rgb(79,161,219);text-decoration:none;background:transparent;"><code style="font-family:'Source Code Pro', monospace;font-size:25.2000007629395px;color:rgb(63,63,63);">StringSerializer</code></a>用于简单的字符串或字节类型。</p>
<p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
该客户端可以与0.10.0版本或更高版本的broker进行通信。旧的或较新的broker可能不支持某些功能。当调用运行的broker程序版本不可用的API时，会产生<code style="font-family:'Source Code Pro', monospace;font-size:25.2000007629395px;color:rgb(63,63,63);">UnsupportedVersionException</code>异常。</p>
<p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
有关生产者更多的配置属性可以参考<a href="https://kafka.apache.org/documentation/#producerconfigs" rel="nofollow" style="color:rgb(79,161,219);text-decoration:none;background:transparent;">Producer Configs</a>。</p>
<h3 id="消费者api" style="font-family:inherit;font-weight:300;line-height:1.1;color:inherit;font-size:1.7em;">
消费者API</h3>
<p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
参考官方文档中的 <a href="https://kafka.apache.org/0102/javadoc/org/apache/kafka/clients/consumer/KafkaConsumer.html" rel="nofollow" style="color:rgb(79,161,219);text-decoration:none;background:transparent;">KafkaConsumer</a> 介绍。</p>
<p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
从Kafka集群中消费记录的客户端。</p>
<p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
这个客户端透明地处理卡夫卡经纪人的失败，并透明地适应作为在集群中迁移的主题分区。该客户端还与代理商进行交互，以允许<a href="https://kafka.apache.org/0102/javadoc/org/apache/kafka/clients/consumer/KafkaConsumer.html#consumergroups" rel="nofollow" style="color:rgb(79,161,219);text-decoration:none;background:transparent;">消费群体</a>使用<a href="https://kafka.apache.org/0102/javadoc/org/apache/kafka/clients/consumer/KafkaConsumer.html#consumergroups" rel="nofollow" style="color:rgb(79,161,219);text-decoration:none;background:transparent;">消费者群体</a>来负载平衡消费。</p>
<p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
消费者保持TCP连接到必要的经纪人以获取数据。使用后未能关闭消费者将泄漏这些连接。消费者不是线程安全的。有关详细信息，请参阅<a href="https://kafka.apache.org/0102/javadoc/org/apache/kafka/clients/consumer/KafkaConsumer.html#multithreaded" rel="nofollow" style="color:rgb(79,161,219);text-decoration:none;background:transparent;">多线程处理</a>。</p>
<h4 id="自动提交偏移" style="font-family:inherit;font-weight:300;line-height:1.1;color:inherit;font-size:1.25em;">
自动提交偏移</h4>
<p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
下面这个例子使用了自动提交，设定了每1000ms提交一次偏移（就是当前已读取消息的位置）。</p>
<pre class="prettyprint" style="font-family:'Source Code Pro', monospace;font-size:14px;line-height:23.8px;color:rgb(51,51,51);overflow:hidden;"><code class="language-java hljs has-numbering" style="font-family:'Source Code Pro', monospace;font-size:12.6000003814697px;color:inherit;display:block;background:transparent;"><span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">package</span> com.github.abel533.kafka.api;

<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">import</span> org.apache.kafka.clients.consumer.ConsumerRecord;
<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">import</span> org.apache.kafka.clients.consumer.ConsumerRecords;
<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">import</span> org.apache.kafka.clients.consumer.KafkaConsumer;

<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">import</span> java.util.Arrays;
<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">import</span> java.util.Properties;

<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">public</span> <span class="hljs-class" style="font-family:Arial, 'Microsoft YaHei';"><span class="hljs-keyword" style="color:rgb(0,0,136);">class</span> <span class="hljs-title" style="color:rgb(102,0,102);">ConsumerAOC</span> {</span>
    <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">public</span> <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">static</span> <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">void</span> <span class="hljs-title" style="font-family:Arial, 'Microsoft YaHei';">main</span>(String[] args) {
        <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">final</span> Properties props = <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">new</span> Properties();
        props.put(<span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"bootstrap.servers"</span>, <span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"192.168.16.150:9092"</span>);
        props.put(<span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"group.id"</span>, <span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"test"</span>);
        props.put(<span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"enable.auto.commit"</span>, <span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"true"</span>);
        props.put(<span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"auto.commit.interval.ms"</span>, <span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"1000"</span>);
        props.put(<span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"key.deserializer"</span>,
                  <span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"org.apache.kafka.common.serialization.StringDeserializer"</span>);
        props.put(<span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"value.deserializer"</span>,
                  <span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"org.apache.kafka.common.serialization.StringDeserializer"</span>);

        KafkaConsumer&lt;String, String&gt; consumer = <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">new</span> KafkaConsumer&lt;&gt;(props);
        consumer.subscribe(Arrays.asList(<span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"t1"</span>));
        <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">while</span> (<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">true</span>) {
            ConsumerRecords&lt;String, String&gt; records = consumer.poll(<span class="hljs-number" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,102,102);">1000</span>);
            <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">for</span> (ConsumerRecord&lt;String, String&gt; record : records)
                System.out.printf(<span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"offset = %d, key = %s, value = %s%n"</span>, 
                                  record.offset(), record.key(), record.value());
        }

    }
}</code></pre><ul class="pre-numbering" style="font-family:Arial, 'Microsoft YaHei';list-style:none;width:50px;line-height:23.7999992370605px;border-right-width:1px;border-right-style:solid;border-right-color:rgb(221,221,221);text-align:right;background-color:rgb(238,238,238);"><li style="list-style:none;">1</li><li style="list-style:none;">2</li><li style="list-style:none;">3</li><li style="list-style:none;">4</li><li style="list-style:none;">5</li><li style="list-style:none;">6</li><li style="list-style:none;">7</li><li style="list-style:none;">8</li><li style="list-style:none;">9</li><li style="list-style:none;">10</li><li style="list-style:none;">11</li><li style="list-style:none;">12</li><li style="list-style:none;">13</li><li style="list-style:none;">14</li><li style="list-style:none;">15</li><li style="list-style:none;">16</li><li style="list-style:none;">17</li><li style="list-style:none;">18</li><li style="list-style:none;">19</li><li style="list-style:none;">20</li><li style="list-style:none;">21</li><li style="list-style:none;">22</li><li style="list-style:none;">23</li><li style="list-style:none;">24</li><li style="list-style:none;">25</li><li style="list-style:none;">26</li><li style="list-style:none;">27</li><li style="list-style:none;">28</li><li style="list-style:none;">29</li><li style="list-style:none;">30</li><li style="list-style:none;">31</li><li style="list-style:none;">32</li></ul><p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
首先通过<code style="font-family:'Source Code Pro', monospace;font-size:25.2000007629395px;color:rgb(63,63,63);">bootstrap.servers</code>设置要连接的Broker，多个可以使用逗号隔开。通过<code style="font-family:'Source Code Pro', monospace;font-size:25.2000007629395px;color:rgb(63,63,63);">group.id</code>设置了当前的分组id，同一个分组id中的多个消费者可以通过负载均衡处理消息（消费者数量多于主题的分区数时，多出来的消费者不会被分配任何消息）。</p>
<p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
通过设置<code style="font-family:'Source Code Pro', monospace;font-size:25.2000007629395px;color:rgb(63,63,63);">enable.auto.commit</code>为<code style="font-family:'Source Code Pro', monospace;font-size:25.2000007629395px;color:rgb(63,63,63);">true</code>开启自动提交，自动提交的频率由 <code style="font-family:'Source Code Pro', monospace;font-size:25.2000007629395px;color:rgb(63,63,63);">auto.commit.interval.ms</code> 设置。</p>
<p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
后面两个 <code style="font-family:'Source Code Pro', monospace;font-size:25.2000007629395px;color:rgb(63,63,63);">deserializer</code> 用于序列化 key 和 value。</p>
<p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
通过 <code style="font-family:'Source Code Pro', monospace;font-size:25.2000007629395px;color:rgb(63,63,63);">consumer.subscribe</code> 定义了主题 <code style="font-family:'Source Code Pro', monospace;font-size:25.2000007629395px;color:rgb(63,63,63);">t1</code>，一个消费者可以订阅多个主题。通过<code style="font-family:'Source Code Pro', monospace;font-size:25.2000007629395px;color:rgb(63,63,63);">consumer.poll</code>获取消息，参数1000（毫秒）的含义是，当缓冲区中没有可用消息时，以此时间进行轮训等待。当设置为0时，理解返回当前可用的消息或者返回空。</p>
<h4 id="手动提交偏移" style="font-family:inherit;font-weight:300;line-height:1.1;color:inherit;font-size:1.25em;">
手动提交偏移</h4>
<p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
消费者不是必须自动提交偏移。用户也可以手动控制提交偏移来决定消息是否已被消费。当消息需要经过一些特殊逻辑进行处理时，手动提交就非常有必要，没有经过处理的消息不应该当成已消费。</p>
<pre class="prettyprint" style="font-family:'Source Code Pro', monospace;font-size:14px;line-height:23.8px;color:rgb(51,51,51);overflow:hidden;"><code class="language-java hljs has-numbering" style="font-family:'Source Code Pro', monospace;font-size:12.6000003814697px;color:inherit;display:block;background:transparent;"><span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">package</span> com.github.abel533.kafka.api;

<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">import</span> org.apache.kafka.clients.consumer.ConsumerRecord;
<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">import</span> org.apache.kafka.clients.consumer.ConsumerRecords;
<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">import</span> org.apache.kafka.clients.consumer.KafkaConsumer;

<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">import</span> java.util.ArrayList;
<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">import</span> java.util.Arrays;
<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">import</span> java.util.List;
<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">import</span> java.util.Properties;

<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">public</span> <span class="hljs-class" style="font-family:Arial, 'Microsoft YaHei';"><span class="hljs-keyword" style="color:rgb(0,0,136);">class</span> <span class="hljs-title" style="color:rgb(102,0,102);">ConsumerManual</span> {</span>

    <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">public</span> <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">static</span> <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">void</span> <span class="hljs-title" style="font-family:Arial, 'Microsoft YaHei';">main</span>(String[] args) {
        Properties props = <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">new</span> Properties();
        props.put(<span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"bootstrap.servers"</span>, <span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"192.168.16.150:9092"</span>);
        props.put(<span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"group.id"</span>, <span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"test"</span>);
        props.put(<span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"enable.auto.commit"</span>, <span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"false"</span>);
        props.put(<span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"key.deserializer"</span>,
                  <span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"org.apache.kafka.common.serialization.StringDeserializer"</span>);
        props.put(<span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"value.deserializer"</span>,
                  <span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"org.apache.kafka.common.serialization.StringDeserializer"</span>);
        KafkaConsumer&lt;String, String&gt; consumer = <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">new</span> KafkaConsumer&lt;&gt;(props);
        consumer.subscribe(Arrays.asList(<span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"t1"</span>, <span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"t2"</span>));
        <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">final</span> <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">int</span> minBatchSize = <span class="hljs-number" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,102,102);">200</span>;
        List&lt;ConsumerRecord&lt;String, String&gt;&gt; buffer = <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">new</span> ArrayList&lt;&gt;();
        <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">while</span> (<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">true</span>) {
            ConsumerRecords&lt;String, String&gt; records = consumer.poll(<span class="hljs-number" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,102,102);">100</span>);
            <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">for</span> (ConsumerRecord&lt;String, String&gt; record : records) {
                buffer.add(record);
            }
            <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">if</span> (buffer.size() &gt;= minBatchSize) {
                <span class="hljs-comment" style="font-family:Arial, 'Microsoft YaHei';color:rgb(136,0,0);">//逻辑处理，例如保存到数据库</span>
                consumer.commitSync();
                buffer.clear();
            }
        }
    }
}</code></pre><ul class="pre-numbering" style="font-family:Arial, 'Microsoft YaHei';list-style:none;width:50px;line-height:23.7999992370605px;border-right-width:1px;border-right-style:solid;border-right-color:rgb(221,221,221);text-align:right;background-color:rgb(238,238,238);"><li style="list-style:none;">1</li><li style="list-style:none;">2</li><li style="list-style:none;">3</li><li style="list-style:none;">4</li><li style="list-style:none;">5</li><li style="list-style:none;">6</li><li style="list-style:none;">7</li><li style="list-style:none;">8</li><li style="list-style:none;">9</li><li style="list-style:none;">10</li><li style="list-style:none;">11</li><li style="list-style:none;">12</li><li style="list-style:none;">13</li><li style="list-style:none;">14</li><li style="list-style:none;">15</li><li style="list-style:none;">16</li><li style="list-style:none;">17</li><li style="list-style:none;">18</li><li style="list-style:none;">19</li><li style="list-style:none;">20</li><li style="list-style:none;">21</li><li style="list-style:none;">22</li><li style="list-style:none;">23</li><li style="list-style:none;">24</li><li style="list-style:none;">25</li><li style="list-style:none;">26</li><li style="list-style:none;">27</li><li style="list-style:none;">28</li><li style="list-style:none;">29</li><li style="list-style:none;">30</li><li style="list-style:none;">31</li><li style="list-style:none;">32</li><li style="list-style:none;">33</li><li style="list-style:none;">34</li><li style="list-style:none;">35</li><li style="list-style:none;">36</li><li style="list-style:none;">37</li><li style="list-style:none;">38</li><li style="list-style:none;">39</li></ul><p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
在这个例子中，我们将<code style="font-family:'Source Code Pro', monospace;font-size:25.2000007629395px;color:rgb(63,63,63);">enable.auto.commit</code>设置为<code style="font-family:'Source Code Pro', monospace;font-size:25.2000007629395px;color:rgb(63,63,63);">false</code>，这是因为这个值默认情况下是<code style="font-family:'Source Code Pro', monospace;font-size:25.2000007629395px;color:rgb(63,63,63);">true</code>，只有手动设置为<code style="font-family:'Source Code Pro', monospace;font-size:25.2000007629395px;color:rgb(63,63,63);">false</code>后才能进行手动提交。</p>
<p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
每当buffer的大小超过设置的批量大小后就会通过<code style="font-family:'Source Code Pro', monospace;font-size:25.2000007629395px;color:rgb(63,63,63);">consumer.commitSync()</code>进行提交。</p>
<p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
在某些情况下，您可能希望通过明确指定偏移量来更精确地控制已经提交的记录。在下面的例子中，我们在完成处理每个分区中的记录之后提交偏移量。</p>
<pre class="prettyprint" style="font-family:'Source Code Pro', monospace;font-size:14px;line-height:23.8px;color:rgb(51,51,51);overflow:hidden;"><code class="language-java hljs has-numbering" style="font-family:'Source Code Pro', monospace;font-size:12.6000003814697px;color:inherit;display:block;background:transparent;"><span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">package</span> com.github.abel533.kafka.api;

<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">import</span> org.apache.kafka.clients.consumer.ConsumerRecord;
<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">import</span> org.apache.kafka.clients.consumer.ConsumerRecords;
<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">import</span> org.apache.kafka.clients.consumer.KafkaConsumer;
<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">import</span> org.apache.kafka.clients.consumer.OffsetAndMetadata;
<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">import</span> org.apache.kafka.common.TopicPartition;

<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">import</span> java.util.*;

<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">public</span> <span class="hljs-class" style="font-family:Arial, 'Microsoft YaHei';"><span class="hljs-keyword" style="color:rgb(0,0,136);">class</span> <span class="hljs-title" style="color:rgb(102,0,102);">ConsumerManualPartition</span> {</span>

    <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">public</span> <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">static</span> <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">void</span> <span class="hljs-title" style="font-family:Arial, 'Microsoft YaHei';">main</span>(String[] args) {
        Properties props = <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">new</span> Properties();
        props.put(<span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"bootstrap.servers"</span>, <span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"192.168.16.150:9092"</span>);
        props.put(<span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"group.id"</span>, <span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"test2"</span>);
        props.put(<span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"enable.auto.commit"</span>, <span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"false"</span>);
        props.put(<span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"key.deserializer"</span>,
                  <span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"org.apache.kafka.common.serialization.StringDeserializer"</span>);
        props.put(<span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"value.deserializer"</span>,
                  <span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"org.apache.kafka.common.serialization.StringDeserializer"</span>);
        KafkaConsumer&lt;String, String&gt; consumer = <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">new</span> KafkaConsumer&lt;&gt;(props);
        consumer.subscribe(Arrays.asList(<span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"t1"</span>));

        <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">try</span> {
            <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">while</span>(<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">true</span>) {
                ConsumerRecords&lt;String, String&gt; records = consumer.poll(Long.MAX_VALUE);
                <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">for</span> (TopicPartition partition : records.partitions()) {
                    List&lt;ConsumerRecord&lt;String, String&gt;&gt; partitionRecords =
                      records.records(partition);
                    <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">for</span> (ConsumerRecord&lt;String, String&gt; record : partitionRecords) {
                        System.out.println(partition.partition() + <span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">": "</span> 
                                           + record.offset() + <span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">": "</span> 
                                           + record.value());
                    }
                    <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">long</span> lastOffset = partitionRecords.get(
                              partitionRecords.size() - <span class="hljs-number" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,102,102);">1</span>).offset();
                    consumer.commitSync(
                              Collections.singletonMap(partition, 
                                               <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">new</span> OffsetAndMetadata(lastOffset + <span class="hljs-number" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,102,102);">1</span>)));
                }
            }
        } <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">finally</span> {
            consumer.close();
        }
    }
}</code></pre><ul class="pre-numbering" style="font-family:Arial, 'Microsoft YaHei';list-style:none;width:50px;line-height:23.7999992370605px;border-right-width:1px;border-right-style:solid;border-right-color:rgb(221,221,221);text-align:right;background-color:rgb(238,238,238);"><li style="list-style:none;">1</li><li style="list-style:none;">2</li><li style="list-style:none;">3</li><li style="list-style:none;">4</li><li style="list-style:none;">5</li><li style="list-style:none;">6</li><li style="list-style:none;">7</li><li style="list-style:none;">8</li><li style="list-style:none;">9</li><li style="list-style:none;">10</li><li style="list-style:none;">11</li><li style="list-style:none;">12</li><li style="list-style:none;">13</li><li style="list-style:none;">14</li><li style="list-style:none;">15</li><li style="list-style:none;">16</li><li style="list-style:none;">17</li><li style="list-style:none;">18</li><li style="list-style:none;">19</li><li style="list-style:none;">20</li><li style="list-style:none;">21</li><li style="list-style:none;">22</li><li style="list-style:none;">23</li><li style="list-style:none;">24</li><li style="list-style:none;">25</li><li style="list-style:none;">26</li><li style="list-style:none;">27</li><li style="list-style:none;">28</li><li style="list-style:none;">29</li><li style="list-style:none;">30</li><li style="list-style:none;">31</li><li style="list-style:none;">32</li><li style="list-style:none;">33</li><li style="list-style:none;">34</li><li style="list-style:none;">35</li><li style="list-style:none;">36</li><li style="list-style:none;">37</li><li style="list-style:none;">38</li><li style="list-style:none;">39</li><li style="list-style:none;">40</li><li style="list-style:none;">41</li><li style="list-style:none;">42</li><li style="list-style:none;">43</li><li style="list-style:none;">44</li><li style="list-style:none;">45</li><li style="list-style:none;">46</li><li style="list-style:none;">47</li></ul><p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
因为每个主题可能存在多个分区，每个分区都维护了一个索引，因此上面针对不同的分区进行处理。</p>
<p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
消费者API中还存在很多有用的方法，可以通过查看<a href="https://kafka.apache.org/0102/javadoc/org/apache/kafka/clients/consumer/KafkaConsumer.html" rel="nofollow" style="color:rgb(79,161,219);text-decoration:none;background:transparent;">官方的API文档</a>了解更多。</p>
<h3 id="流api" style="font-family:inherit;font-weight:300;line-height:1.1;color:inherit;font-size:1.7em;">
流API</h3>
<p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
参考官方文档中 <a href="https://kafka.apache.org/0102/javadoc/org/apache/kafka/streams/KafkaStreams.html" rel="nofollow" style="color:rgb(79,161,219);text-decoration:none;background:transparent;">KafkaStreams</a> 的介绍。</p>
<p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
流API允许对来自一个或多个主题的消息进行连续计算，并将结果发送到零个，一个或多个主题中。</p>
<p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
可以从Kafka获取某个主题的消息，经过处理后输出到另一个主题。相当于是对主题做了一个加工。下面是一个示例，这个示例从t1主题获取消息，然后计算数字的平方后发送消息到t2主题中。</p>
<pre class="prettyprint" style="font-family:'Source Code Pro', monospace;font-size:14px;line-height:23.8px;color:rgb(51,51,51);overflow:hidden;"><code class="language-java hljs has-numbering" style="font-family:'Source Code Pro', monospace;font-size:12.6000003814697px;color:inherit;display:block;background:transparent;"><span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">package</span> com.github.abel533.kafka.api;

<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">import</span> org.apache.kafka.common.serialization.Serdes;
<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">import</span> org.apache.kafka.streams.KafkaStreams;
<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">import</span> org.apache.kafka.streams.StreamsConfig;
<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">import</span> org.apache.kafka.streams.kstream.KStreamBuilder;

<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">import</span> java.util.HashMap;
<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">import</span> java.util.Map;

<span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">public</span> <span class="hljs-class" style="font-family:Arial, 'Microsoft YaHei';"><span class="hljs-keyword" style="color:rgb(0,0,136);">class</span> <span class="hljs-title" style="color:rgb(102,0,102);">StreamApi</span> {</span>
    <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">public</span> <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">static</span> <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">void</span> <span class="hljs-title" style="font-family:Arial, 'Microsoft YaHei';">main</span>(String[] args) {
        Map&lt;String, Object&gt; props = <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">new</span> HashMap&lt;&gt;();
        props.put(StreamsConfig.APPLICATION_ID_CONFIG, <span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"my-stream-processing-application"</span>);
        props.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, <span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"192.168.16.150:9092"</span>);
        props.put(StreamsConfig.KEY_SERDE_CLASS_CONFIG, Serdes.String().getClass());
        props.put(StreamsConfig.VALUE_SERDE_CLASS_CONFIG, Serdes.String().getClass());
        StreamsConfig config = <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">new</span> StreamsConfig(props);

        KStreamBuilder builder = <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">new</span> KStreamBuilder();
        builder.stream(<span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"t1"</span>).mapValues(value -&gt; {
            Integer i = Integer.parseInt((String)value);
            <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">return</span> String.valueOf(i * i);
        }).to(<span class="hljs-string" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,136,0);">"t2"</span>);

        KafkaStreams streams = <span class="hljs-keyword" style="font-family:Arial, 'Microsoft YaHei';color:rgb(0,0,136);">new</span> KafkaStreams(builder, config);
        streams.start();
    }
}</code></pre><ul class="pre-numbering" style="font-family:Arial, 'Microsoft YaHei';list-style:none;width:50px;line-height:23.7999992370605px;border-right-width:1px;border-right-style:solid;border-right-color:rgb(221,221,221);text-align:right;background-color:rgb(238,238,238);"><li style="list-style:none;">1</li><li style="list-style:none;">2</li><li style="list-style:none;">3</li><li style="list-style:none;">4</li><li style="list-style:none;">5</li><li style="list-style:none;">6</li><li style="list-style:none;">7</li><li style="list-style:none;">8</li><li style="list-style:none;">9</li><li style="list-style:none;">10</li><li style="list-style:none;">11</li><li style="list-style:none;">12</li><li style="list-style:none;">13</li><li style="list-style:none;">14</li><li style="list-style:none;">15</li><li style="list-style:none;">16</li><li style="list-style:none;">17</li><li style="list-style:none;">18</li><li style="list-style:none;">19</li><li style="list-style:none;">20</li><li style="list-style:none;">21</li><li style="list-style:none;">22</li><li style="list-style:none;">23</li><li style="list-style:none;">24</li><li style="list-style:none;">25</li><li style="list-style:none;">26</li><li style="list-style:none;">27</li><li style="list-style:none;">28</li><li style="list-style:none;">29</li></ul><p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
在例子中<span style="color:rgb(63,63,63);font-family:'Source Code Pro', monospace;font-size:25.2px;line-height:14px;">StreamsConfig.APPLICATION_ID_CONFIG</span>用于设置当前流处理的ID，具有相同流ID的应用会根据输入主题的分区来分配</p>
<p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
任务。当流处理应用的数量大于主题的分区数时，超出部分的流处理不会被分配任何消息。</p>
<p style="font-family:Arial, 'Microsoft YaHei';color:rgb(51,51,51);">
以上是关于Kafka基本API的应用。</p>
</div>
</div>
            </div>
                </div>