---
layout:     post
title:      第3章 kafka生产者—向Kafka写入数据
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u010819416/article/details/83964324				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>可以使用Kafka内置的客户端API开发Kafka应用程序。</p>
<p>3.1 生产者概述<br>
<img src="https://img-blog.csdnimg.cn/2018111320532981.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTA4MTk0MTY=,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<p>3.2 创建Kafka生产者<br>
Kafka生产者有3个必选的属性：<br>
*bootstrap.servers<br>
指定broker的地址清单，地址的格式为host:port<br>
*key.serializer<br>
设置为实现一个org.apache.kafka.common.serialization.Serializer接口的类，生产者会使用这个类把键对象序列化为字节数组。默认提供：ByteArraySerializer、StringSerializer、IntegerSerializer。<br>
*value.serializer<br>
指定类将值序列化<br>
创建一个新的生产者：</p>
<pre><code>Properties props = new Properties();
props.put("bootstrap.servers", "localhost:9092");
props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");
Producer&lt;String, String&gt; producer = new KafkaProducer&lt;&gt;(props);
</code></pre>
<p>实例化生产者对象后，就可以发送消息了。发送消息有3种方式：<br>
1）发送并忘记（fire-and-forger）<br>
并不关心它是否正常到达<br>
2）同步发送<br>
send()方法返回Future对象<br>
3）异步发送<br>
send()方法，指定一个回调函数，服务器在返回响应时调用该函数。</p>
<p>3.3 发送消息到Kafka</p>
<pre><code>ProducerRecord&lt;String, String&gt; record = new ProducerRecord&lt;&gt;("CustomerCountry", "Precision Products", "France");//目标主题的名字和要发送的键和值对象
try{
    producer.send(record);
} catch (Exception e) {
    e.printStackTrace();
}
</code></pre>
<p>3.3.1 同步发送消息</p>
<pre><code> ProducerRecord&lt;String, String&gt; record = new ProducerRecord&lt;&gt;("CustomerCountry", "Precision Products", "France");//目标主题的名字和要发送的键和值对象
 try{
     producer.send(record).get();
     //send()方法返回一个Future对象，get()方法等待Kafka响应，如果没有发送错误，会得到一个RecordMetadata对象，可以用它
     //获取消息的偏移量
 } catch (Exception e) {
     e.printStackTrace();
 }
</code></pre>
<p>3.3.2 异步发送消息</p>
<pre><code> private static class DemoProducerCallback implements Callback {
        @Override
        public void onCompletion(RecordMetadata recordMetadata, Exception e) {
            if(e != null) {
                e.printStackTrace();
            }
        }
    }
</code></pre>
<pre><code>//异步发送消息
ProducerRecord&lt;String, String&gt; record = new ProducerRecord&lt;&gt;("CustomerCountry", "Biomedical Materials", "USA");
producer.send(record, new DemoProducerCallback());
</code></pre>
<p>3.4 生产者的配置</p>
<p>1、acks<br>
acks参数指定了必须要有多少个分区副本收到消息，生产者才会认为消息写入是成功的。</p>
<p>2、 buffer.memory<br>
设置生产者内存缓冲区的大小，生产者用它缓冲要发送到服务器的消息。</p>
<p>3、compression.type<br>
消息发送不会被压缩，参数可以设置为snappy,gzip或lz4,指定了消息被发送给broker之前使用哪一种压缩算法进行压缩。</p>
<p>4、retries<br>
生产者从服务器收到的错误有可能是临时性的错误，在这种情况下，retries参数的值决定了生产者可以重发消息的次数。</p>
<p>5 batch.size<br>
当有多个消息需要被发送到同一个分区时，生产者会把它们放在同一个批次里。该参数指定了一个可以使用的内存大小，按照字节数计算。</p>
<p>6 <a href="http://linger.ms" rel="nofollow">linger.ms</a><br>
该参数指定了生产者在发送批次之前等待更多消息加入批次的时间。</p>
<p>7 <a href="http://client.id" rel="nofollow">client.id</a><br>
可以是任意的字符串，服务器用它来识别消息的来源</p>
<p>8 max.in.flight.requests.per.connection<br>
该参数指定了生产者在收到服务器响应之前可以发送多少个消息</p>
<p>9 <a href="http://timeout.ms" rel="nofollow">timeout.ms</a>、<a href="http://request.timeout.xn--msmetadata-ih7q.fetch.timeout.ms" rel="nofollow">request.timeout.ms和metadata.fetch.timeout.ms</a><br>
request.timeout.ms指定了生产者在发送数据时等待服务器返回响应的时间；<br>
metadata.fetch.timeout.ms指定了生产者在获取元数据时等待服务器返回响应的时间；<br>
timeout.ms指定了broker等待同步副本返回消息确认的时间</p>
<p>10 <a href="http://max.block.ms" rel="nofollow">max.block.ms</a><br>
该参数指定了在调用send()方法或使用partitionsFor()方法获取元数据时生产者的阻塞时间。</p>
<p>11 max.request.size<br>
该参数用于控制生产者发送的请求大小</p>
<p>12 receive.buffer.bytes 和send.buffer.bytes<br>
分别指定了TCP socket接收和发送数据包的缓冲区大小。</p>
<p>3.5 序列化器</p>
<p>3.5.1 自定义序列化器</p>
<p>3.5.2 使用Avro序列化<br>
Apache Avro是一种与编程语言无关的序列化格式。<br>
Avro数据通过与语言无关的schema来定义。schema通过JSON来描述，数据被序列化成二进制文件或JSON文件。<br>
特性：当负责写消息的应用程序使用了新的schema，负责读消息的应用程序可以继续处理消息而无需做任何改动。</p>
<p>3.5.3 在Kafka里使用Avro</p>
<p>3.6 分区<br>
ProducerRecord对象包含了目标主题、键和值。<br>
Kafka的消息是一个个键值对，ProducerRecord对象可以只包含目标主题和值，键可以设置为默认的null。<br>
键有两个用途：可以作为消息的附加信息，也可以用来决定消息该被写到主题的哪个分区。<br>
如果键值为null,并且使用了默认的分区器，那么记录将被随机地发送到主题内各个可用的分区上。<br>
如果键不为null，并且使用了默认的分区器，那么Kafka会对键进行散列，然后根据散列值把消息映射到特定的分区上。</p>
<ul>
<li>实现自定义分区策略</li>
</ul>
<p>3.7 旧版的生产者API</p>
<p>3.8 总结</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>