---
layout:     post
title:      Kafka之Producer篇
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<blockquote>
<p>Kafka之Producer篇</p>
</blockquote>
<p><strong>Kafka生产者案例</strong><br>
本篇我们叙述Kafka是如何将数据发送到服务端的;<br>
首先我们来看下案例,如下代码所示，这是一个简单的将消息发送到服务端的例子,首先配置链接Kafka的参数,然后用生成的配置生成一个KafkaProducer,这个就是kafka的生产者,紧接着生成一个ProducerRecord用于封装要发送的消息,最终调用producer.send(recoder)将消息发送至服务端,这个Demo比较粗糙;<br>
消息发送有两种方式:<br>
1.同步发送消息,producer.send(recoder)会返回一个future对象，然后future.get()会一直等待Kafka服务器的响应;<br>
2.异步发送消息,producer.send(recoder,new DemoProducerCallback());传入一个DemoProducerCallback类，但是这类要实现kafka的Callback，而异步处理便在onCompletion()方法中完成;</p>
<pre><code class="prism language-java">  <span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">ProdectDemo</span> <span class="token punctuation">{</span>


    <span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span>String<span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>

        Properties props <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Properties</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token comment">//broker地址</span>
        props<span class="token punctuation">.</span><span class="token function">put</span><span class="token punctuation">(</span><span class="token string">"bootstrap.servers"</span><span class="token punctuation">,</span> <span class="token string">"node1:9092,node2:9092,node3:9092"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token comment">//请求时候需要验证</span>
        props<span class="token punctuation">.</span><span class="token function">put</span><span class="token punctuation">(</span><span class="token string">"acks"</span><span class="token punctuation">,</span> <span class="token string">"all"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token comment">//请求失败时候需要重试</span>
        props<span class="token punctuation">.</span><span class="token function">put</span><span class="token punctuation">(</span><span class="token string">"retries"</span><span class="token punctuation">,</span> <span class="token number">0</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token comment">//指定消息key序列化方式</span>
        props<span class="token punctuation">.</span><span class="token function">put</span><span class="token punctuation">(</span><span class="token string">"key.serializer"</span><span class="token punctuation">,</span>
                <span class="token string">"org.apache.kafka.common.serialization.StringSerializer"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token comment">//指定消息本身的序列化方式</span>
        props<span class="token punctuation">.</span><span class="token function">put</span><span class="token punctuation">(</span><span class="token string">"value.serializer"</span><span class="token punctuation">,</span>
                <span class="token string">"org.apache.kafka.common.serialization.StringSerializer"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>


        Producer<span class="token generics function"><span class="token punctuation">&lt;</span>String<span class="token punctuation">,</span> String<span class="token punctuation">&gt;</span></span> producer <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">KafkaProducer</span><span class="token generics function"><span class="token punctuation">&lt;</span>String<span class="token punctuation">,</span> String<span class="token punctuation">&gt;</span></span><span class="token punctuation">(</span>props<span class="token punctuation">)</span><span class="token punctuation">;</span>
        ProducerRecord<span class="token generics function"><span class="token punctuation">&lt;</span>String<span class="token punctuation">,</span> String<span class="token punctuation">&gt;</span></span> recoder<span class="token operator">=</span><span class="token keyword">new</span> <span class="token class-name">ProducerRecord</span><span class="token generics function"><span class="token punctuation">&lt;</span>String<span class="token punctuation">,</span> String<span class="token punctuation">&gt;</span></span><span class="token punctuation">(</span><span class="token string">"test"</span><span class="token punctuation">,</span><span class="token string">"test1"</span><span class="token punctuation">,</span><span class="token string">"test value"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token comment">//同步发送消息</span>
        <span class="token keyword">try</span> <span class="token punctuation">{</span>
            RecordMetadata recordMetadata<span class="token operator">=</span>producer<span class="token punctuation">.</span><span class="token function">send</span><span class="token punctuation">(</span>recoder<span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span> <span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">Exception</span> e<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            e<span class="token punctuation">.</span><span class="token function">printStackTrace</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

        <span class="token comment">//异步发送消息</span>
        <span class="token keyword">try</span> <span class="token punctuation">{</span>
            producer<span class="token punctuation">.</span><span class="token function">send</span><span class="token punctuation">(</span>recoder<span class="token punctuation">,</span><span class="token keyword">new</span> <span class="token class-name">DemoProducerCallback</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span> <span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">Exception</span> e<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            e<span class="token punctuation">.</span><span class="token function">printStackTrace</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span>

 <span class="token keyword">class</span> <span class="token class-name">DemoProducerCallback</span> <span class="token keyword">implements</span> <span class="token class-name">Callback</span> <span class="token punctuation">{</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">onCompletion</span><span class="token punctuation">(</span>RecordMetadata recordMetadata<span class="token punctuation">,</span> Exception e<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        <span class="token keyword">if</span> <span class="token punctuation">(</span>e <span class="token operator">!=</span> null<span class="token punctuation">)</span> <span class="token punctuation">{</span><span class="token comment">//如果Kafka返回一个错误，onCompletion方法抛出一个non null异常。</span>
            e<span class="token punctuation">.</span><span class="token function">printStackTrace</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span><span class="token comment">//对异常进行一些处理，这里只是简单打印出来</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<p><strong>Kafka生产者常用参数配置</strong></p>
<pre><code>ack机制：
Ack=0:生产者在成功写入消息之前不会等待任何服务器的响应；
Ack=1:只要集群的leader接收到消息，生产者就会收到一个来自服务器的成功响应；
Ack=all：只有当所有参与与复制的服务器全部都收到消息时，生产者才能收到一个来自服务器的成功响应；

Buffer.memory
用来设置生产者内存缓冲区的大小，生产者用这个来缓冲要发送到broker的消息；

Comperession.type
指定消息在发送给broker之前采用哪种压缩方式，snappy,gzip,lz4…..

Retries
生产者有可能会收到服务器的错误，它指定生产者可以重发消息的次数，如果达到这个次数生产者会放弃重试并返回错误；

Batch.size
指定一个批次可以使用的内存大小，按照字节来计算；

Lingers.ms
指定在生产者在发送批次之前等待更多的消息加入批次的时间，以便提高吞吐量；

Client.id
消息id

Max.in.flight.request.per.connection
指定生产者在收到服务器相应之前可以发送多少个消息；设置为1，可以保证消息按照发送的顺序写入服务器，即使发生重试；

Request.timeout.ms
指定生产者再发送数据时等待服务器返回相应的时间；

Metadata.fatch.timeout.ms
指定生产者在获取元数据时等待服务器返回响应的时间；

Timeout.ms
指定broker等待同步副本返回消息确认的时间，与ask搭配使用；

Max.block.ms
指定调用send()或者partitionsFor()方法获取元数据时阻塞的时间；

Max.request.size
控制单条消息的最大值；
</code></pre>
<p><strong>Kafka生产者的工作原理</strong><br>
上面介绍了Kafka如何生产数据的,下面我们来看下Kafka的数据生产的工作流程，如下图所示，根据上面的Demo我们可以看的出:<br>
1.我们先构建了一个KafkaProducer，然后就构建出了ProducerRecord；<br>
2.然后就调用producer.send(recoder)进行发送；<br>
3.而KafkaProducer接收到消息后会先对其进行序列化，然后结合本地缓存的元数据信息一起发送给partitioner去确定目标分区；<br>
4.确认分区后追加写入到内存中的消息缓冲池(accumulator),此时的send方法完成；<br>
5.最后发送给服务端Broker,服务器对生产者做出响应,至此消息发送完毕；</p>
<p><img src="https://ws4.sinaimg.cn/large/006tNbRwly1fwi7kfqmgtj30i906umxc.jpg" alt=""></p>
<p><strong>Kafka生产者的内部原理</strong><br>
下面将详细展开Kafka生产者的工作原理；<br>
ProducerRecord和KafkaProducer的产生就不说了,那就先说下消息的序列化和确定目标分区,如果消息是属于字符串的那么就可以直接使用StringSerializer，如果是对象之类的话可以使用Avro，Thrift,Protobuf或者自定义序列化器;传输的对象序列化后结合KafkaProducerr缓存的元数据共同传递给后面Partitioner实现类进行目标分区的计算,当然这里我们可以使用我们自定义的分区，直接实现Partitioner，重写它的partition方法即可;</p>
<p><img src="https://ws2.sinaimg.cn/large/006tNbRwly1fwi89by8doj30k103u0sp.jpg" alt=""></p>
<p>序列化和计算完分区之后便要向缓冲区追加消息,producer创建时会创建一个默认32MB(由buffer.memory参数指定)的accumulator缓冲区，专门保存待发送的消息。该数据结构中还包含了一个特别重要的集合信息：消息批次信息(batches)。该集合本质上是一个HashMap，里面分别保存了每个topic分区下的batch队列，即前面说的批次是按照topic分区进行分组的。这样发往不同分区的消息保存在对应分区下的batch队列中。举个简单的例子，假设消息M1, M2被发送到test的0分区但属于不同的batch，M3分送到test的1分区，那么batches中包含的信息就是：{“test-0” -&gt; [batch1, batch2], “test-1” -&gt; [batch3]};</p>
<p>单个topic分区下的batch队列中保存的是若干个消息批次。每个batch中最重要的3个组件包括：<br>
compressor: 负责执行追加写入操作<br>
batch缓冲区：由batch.size参数控制，消息被真正追加写入到的地方<br>
thunks：保存消息回调逻辑的集合</p>
<p><img src="https://ws3.sinaimg.cn/large/006tNbRwly1fwiamnrl8bj30nr09tmxh.jpg" alt=""></p>
<p>当消息被追加到缓冲区后就需要将消息发送给Broker了，那么这个动作谁来做呢?其实在KafkaProducer创建完成后会有一个IO线程即Sender线程,它负责将缓冲区的消息发送至Broker;<br>
具体的工作流程:<br>
1.不断轮询缓冲区寻找已做好发送准备的分区;<br>
2.将轮询获得的各个batch按照目标分区所在的leader broker进行分组;<br>
3.将分组后的batch通过底层创建的Socket连接发送给各个broker;<br>
4.等待服务器端发送response回来;</p>
<p><img src="https://ws1.sinaimg.cn/large/006tNbRwgy1fwii7gbd4rj30nr09tmxh.jpg" alt=""></p>
<p>如果我们发送消息的时候使用的是同步方法，则会一直等待服务器端的相应，如果是异步的话会在callback中获取到服务器的相应,在上面我们已经将消息发送至服务端，如果Broker处理完毕后会就会做出相应,broker会把响应信息发给Sender线程,然后Sender线程会依次往回传递，直至callback函数，如下图所示;</p>
<p><img src="https://ws2.sinaimg.cn/large/006tNbRwgy1fwiiriktyvj30g20773yj.jpg" alt=""></p>
<p><strong>至此Kafka生产者的使用以及工作原理以及叙述完毕!</strong></p>
<p>参考:<br>
《Kafka权威指南》<br>
<a href="http://www.cnblogs.com/huxi2b/p/6364613.html" rel="nofollow">http://www.cnblogs.com/huxi2b/p/6364613.html</a></p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>