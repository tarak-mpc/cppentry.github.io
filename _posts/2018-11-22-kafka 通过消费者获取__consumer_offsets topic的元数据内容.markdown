---
layout:     post
title:      kafka 通过消费者获取__consumer_offsets topic的元数据内容
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="kafka-通过消费者获取consumeroffsets-topic的元数据内容">kafka 通过消费者获取__consumer_offsets topic的元数据内容</h1>

<p>工作中遇到一个问题需要获取kafka的元数据信息，诸如topic创建信息，消费者消费topic的信息等。要获取kafka的元数据信息，首先想到找zookeeper，利用zookeeper的watcher机制去监听kafka的元数据节点的创建，进而拿到对应信息。但由于kafka新版本存在两种消费者元数据保存机制，因此我们不能只考虑获取zookeeper上的元数据。</p>



<h1 id="kafka-消费者offset存储方式">kafka 消费者offset存储方式</h1>

<p>在kafka 0.9 版本开始提供了新的consumer及consumer group，位移的管理与保存机制发生了很大的变化，新版本的consumer默认将不再保存位移到zookeeper中，而是__consumer_offsets topic中</p>

<ul>
<li>如果消费者根据java api来消费，，也就是<code>kafka.javaapi.consumer.ConsumerConnector</code>,通过配置参数<code>zookeeper.connect</code>来消费。这种情况，消费者的offset会更新到zookeeper的<code>/kafka/consumers/&lt;group.id&gt;/offsets/&lt;topic&gt;/&lt;partitionId&gt;</code>，但是zookeeper其实并不适合进行大批量的读写操作，尤其是写操作。</li>
<li>因此kafka提供了另一种解决方案：增加__consumeroffsets topic，将offset信息写入这个topic，摆脱对zookeeper的依赖(指保存offset这件事情)。__consumer_offsets中的消息保存了每个consumer group某一时刻提交的offset信息。</li>
</ul>

<p>如下图，可能有这两种情况ZK:zookeeper,KF:kafka <br>
<img src="https://img-blog.csdn.net/2018081109283283?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MDk2NjQx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<h2 id="通过新建一个消费者去获取该topic的信息">通过新建一个消费者去获取该topic的信息</h2>

<p>既然__consumer_offsets 是一个topic，kafka将consumer的消费信息push到该topic，便想到通过消费者的方式去获取该topic的信息</p>

<p>代码如下:</p>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">import</span> kafka.common.OffsetAndMetadata;
<span class="hljs-keyword">import</span> kafka.coordinator.*;
<span class="hljs-keyword">import</span> org.apache.kafka.clients.consumer.ConsumerRecord;
<span class="hljs-keyword">import</span> org.apache.kafka.clients.consumer.ConsumerRecords;
<span class="hljs-keyword">import</span> org.apache.kafka.clients.consumer.KafkaConsumer;

<span class="hljs-keyword">import</span> java.nio.ByteBuffer;
<span class="hljs-keyword">import</span> java.util.Collections;
<span class="hljs-keyword">import</span> java.util.Properties;

<span class="hljs-javadoc">/**
 *<span class="hljs-javadoctag"> @author</span>: wangjian
 *<span class="hljs-javadoctag"> @date</span>: 2018-08-01 15:19
 */</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">KafkaCli</span> {</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) {
        Properties props = <span class="hljs-keyword">new</span> Properties();

        props.put(<span class="hljs-string">"bootstrap.servers"</span>, <span class="hljs-string">"cdh003:9092"</span>);
        <span class="hljs-comment">//每个消费者分配独立的组号</span>
        props.put(<span class="hljs-string">"group.id"</span>, <span class="hljs-string">"test_1"</span>);

        <span class="hljs-comment">//如果value合法，则自动提交偏移量</span>
        props.put(<span class="hljs-string">"enable.auto.commit"</span>, <span class="hljs-string">"true"</span>);

        <span class="hljs-comment">//设置多久一次更新被消费消息的偏移量</span>
        props.put(<span class="hljs-string">"auto.commit.interval.ms"</span>, <span class="hljs-string">"1000"</span>);

        <span class="hljs-comment">//设置会话响应的时间，超过这个时间kafka可以选择放弃消费或者消费下一条消息</span>
        props.put(<span class="hljs-string">"session.timeout.ms"</span>, <span class="hljs-string">"30000"</span>);

        <span class="hljs-comment">//该参数表示从头开始消费该主题</span>
        props.put(<span class="hljs-string">"auto.offset.reset"</span>, <span class="hljs-string">"earliest"</span>);

        <span class="hljs-comment">//注意反序列化方式为ByteArrayDeserializer</span>
        props.put(<span class="hljs-string">"key.deserializer"</span>,
                <span class="hljs-string">"org.apache.kafka.common.serialization.ByteArrayDeserializer"</span>);
        props.put(<span class="hljs-string">"value.deserializer"</span>,
                <span class="hljs-string">"org.apache.kafka.common.serialization.ByteArrayDeserializer"</span>);

        KafkaConsumer&lt;<span class="hljs-keyword">byte</span>[], <span class="hljs-keyword">byte</span>[]&gt; consumer = <span class="hljs-keyword">new</span> KafkaConsumer&lt;&gt;(props);

        consumer.subscribe(Collections.singletonList(<span class="hljs-string">"__consumer_offsets"</span>));

        System.out.println(<span class="hljs-string">"Subscribed to topic "</span> + <span class="hljs-string">"__consumer_offsets"</span>);

        <span class="hljs-keyword">while</span> (<span class="hljs-keyword">true</span>) {
            ConsumerRecords&lt;<span class="hljs-keyword">byte</span>[], <span class="hljs-keyword">byte</span>[]&gt; records = consumer.poll(<span class="hljs-number">100</span>);
            <span class="hljs-keyword">for</span> (ConsumerRecord&lt;<span class="hljs-keyword">byte</span>[], <span class="hljs-keyword">byte</span>[]&gt; record : records) {
<span class="hljs-comment">//                这里直接将record的全部信息写到System.out打印流中</span>
<span class="hljs-comment">//                GroupMetadataManager.OffsetsMessageFormatter formatter = new GroupMetadataManager.OffsetsMessageFormatter();</span>
<span class="hljs-comment">//                formatter.writeTo(record, System.out);</span>

                <span class="hljs-comment">//对record的key进行解析，注意这里的key有两种OffsetKey和GroupMetaDataKey</span>
                <span class="hljs-comment">//GroupMetaDataKey中只有消费者组ID信息，OffsetKey中还包含了消费的topic信息</span>
                BaseKey key = GroupMetadataManager.readMessageKey(ByteBuffer.wrap(record.key()));
                <span class="hljs-keyword">if</span> (key <span class="hljs-keyword">instanceof</span> OffsetKey) {
                    GroupTopicPartition partition = (GroupTopicPartition) key.key();
                    String topic = partition.topicPartition().topic();
                    String group = partition.group();

                    System.out.println(<span class="hljs-string">"group : "</span> + group + <span class="hljs-string">"  topic : "</span> + topic);
                    System.out.println(key.toString());
                } <span class="hljs-keyword">else</span> <span class="hljs-keyword">if</span> (key <span class="hljs-keyword">instanceof</span> GroupMetadataKey) {
                    System.out.println(<span class="hljs-string">"groupMetadataKey:------------ "</span>+key.key());
                }

                <span class="hljs-comment">//对record的value进行解析</span>
                OffsetAndMetadata om = GroupMetadataManager.readOffsetMessageValue(ByteBuffer.wrap(record.value()));
                <span class="hljs-comment">//System.out.println(om.toString());</span>
            }
        }
    }
}</code></pre>



<h3 id="key的解析输出信息如下">key的解析输出信息如下：</h3>

<p><img src="https://img-blog.csdn.net/20180811092935810?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MDk2NjQx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<h3 id="value的解析输出信息如下">value的解析输出信息如下：</h3>

<p><img src="https://img-blog.csdn.net/20180811093006674?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MDk2NjQx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>但是使用这种方式解析过一会儿会出现如下异常：</p>

<pre class="prettyprint"><code class="language-java hljs ">Exception in thread <span class="hljs-string">"main"</span> org.apache.kafka.common.protocol.types.SchemaException: Error reading field <span class="hljs-string">'metadata'</span>: Error reading string of length <span class="hljs-number">25970</span>, only <span class="hljs-number">12</span> bytes available
    at org.apache.kafka.common.protocol.types.Schema.read(Schema.java:<span class="hljs-number">72</span>)
    at kafka.coordinator.GroupMetadataManager$.readOffsetMessageValue(GroupMetadataManager.scala:<span class="hljs-number">958</span>)
    at kafka.coordinator.GroupMetadataManager.readOffsetMessageValue(GroupMetadataManager.scala)
    at com.wangjian.KafkaCli.main(KafkaCli.java:<span class="hljs-number">68</span>)</code></pre>

<p>测试发现这是当key类型为GroupMetadataKey时，去解析OffsetAndMetadata会抛异常，应该用如下方法去解析GroupMetadata：</p>

<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">if</span> (key <span class="hljs-keyword">instanceof</span> GroupMetadataKey) {  
    System.out.println(<span class="hljs-string">"groupMetadataKey: "</span> + key.key());    
<span class="hljs-comment">//第一个参数为group id，先将key转换为GroupMetadataKey类型，再调用它的key()方法就可以获得group id</span>
    GroupMetadata groupMetadata = GroupMetadataManager.readGroupMessageValue(((GroupMetadataKey) key).key(), ByteBuffer.wrap(record.value()));

    System.out.println(<span class="hljs-string">"GroupMetadata: "</span>+groupMetadata.toString());
}</code></pre>

<p>它的输出如下：</p>



<pre class="prettyprint"><code class=" hljs http"><span class="hljs-attribute">groupMetadataKey</span>: <span class="hljs-string">test_1</span>
<span class="hljs-attribute">GroupMetadata</span>: <span class="hljs-string">[test_1,Some(consumer),Stable,Map(consumer-1-2b952983-41bd-4bdb-bc65-89ceedd91d26 -&gt; [consumer-1-2b952983-41bd-4bdb-bc65-89ceedd91d26,consumer-1,/172.18.89.153,30000])]</span></code></pre>

<p>可以看到GroupMetadata中保存了该消费者组中每个消费者的具体信息，包括了消费者所在IP等</p>

<h3 id="使用formatter方式写到systemout输出信息如下">使用formatter方式写到System.out输出信息如下：</h3>



<pre class="prettyprint"><code class="language-java hljs ">    GroupMetadataManager.OffsetsMessageFormatter formatter = <span class="hljs-keyword">new</span> GroupMetadataManager.OffsetsMessageFormatter();
    formatter.writeTo(record, System.out);</code></pre>

<p><img src="https://img-blog.csdn.net/20180811093156313?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2MDk2NjQx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>这是完整的OffsetMessage信息</p>

<p>同时也可以看到__consumer_offsets topic的每一日志项的格式都是：[Group, Topic, Partition]::[OffsetMetadata[Offset, Metadata], CommitTime, ExpirationTime]</p>

<h1 id="总结">总结：</h1>

<p>kafka push 到__consumer_offsets 该topic的元数据信息有两种：</p>

<p>key：OffsetKey  —————————&gt;  value：OffsetAndMetadata     保存了消费者组各个partition的offset位移信息元数据</p>

<p>key：GroupMetadataKey ————————&gt; value：GroupMetadata   保存了消费者组中各个消费者的信息</p>

<p>在通过java 开发consumer去消费该topic获取元数据时，应注意区分这两种情况，还有反序列化方式</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>