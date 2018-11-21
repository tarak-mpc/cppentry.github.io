---
layout:     post
title:      Apache Kafka -5 生产者示例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="apache-kafka教程-之-apache-kafka-生产者示例">Apache Kafka教程  之 Apache Kafka - 生产者示例</h2>

<p><img src="http://upload-images.jianshu.io/upload_images/6871092-cb3834d9a849cae3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" alt="http://blogxinxiucan.sh1.newtouch.com/" title=""></p>

<p><strong>个人博客地址：</strong> <a href="http://blogxinxiucan.sh1.newtouch.com/2017/07/13/Apache-Kafka-%E7%94%9F%E4%BA%A7%E8%80%85%E7%A4%BA%E4%BE%8B/" rel="nofollow" target="_blank">http://blogxinxiucan.sh1.newtouch.com/2017/07/13/Apache-Kafka-生产者示例/</a></p>



<h3 id="apache-kafka-生产者示例">Apache Kafka - 生产者示例</h3>

<blockquote>
  <p>让我们创建一个使用Java客户端发布和使用消息的应用程序。Kafka生产者客户端由以下API组成。</p>
</blockquote>



<h3 id="kafkaproducer-api">KafkaProducer API</h3>

<p>让我们了解本节中最重要的一套Kafka生产者API。KafkaProducer API的核心部分是KafkaProducer类。</p>

<ul>
<li>KafkaProducer类提供了一个选项，可以使用以下方法在其构造函数中连接Kafka代理。</li>
</ul>

<p><code>producer.send(new ProducerRecord&lt;byte[],byte[]&gt;(topic,  partition, key1, value1) , callback);</code></p>

<ul>
<li>ProducerRecord - 生产者管理一个等待发送的记录缓冲区。</li>
<li>回调 - 当服务器确认记录时执行的用户提供的回调（null表示无回调）。</li>
<li>KafkaProducer类提供了一种刷新方法，以确保所有先前发送的消息已经实际完成。flush方法的语法如下 public void flush()</li>
<li>KafkaProducer类提供了partitionFor方法，它有助于获取给定主题的分区元数据。这可以用于自定义分区。此方法的签名如下 <br>
<code>public Map metrics()</code> <br>
它返回生产者维护的内部指标图。</li>
<li>public void close（） KafkaProducer类提供了紧密的方法块，直到所有先前发送的请求完成为止。</li>
</ul>



<h3 id="生产者api">生产者API</h3>

<p>Producer API的中心部分是Producer类。生产者类提供了通过以下方法在其构造函数中连接Kafka代理的选项。</p>

<p><strong>生产者类</strong> <br>
生产者类提供发送方法，使用以下签名将消息发送到单个或多个主题。</p>

<pre><code>public void send(KeyedMessaget&lt;k,v&gt; message) 
- sends the data to a single topic,par-titioned by key using either sync or async producer.
public void send(List&lt;KeyedMessage&lt;k,v&gt;&gt;messages)
- sends data to multiple topics.
Properties prop = new Properties();
prop.put(producer.type,”async”)
ProducerConfig config = new ProducerConfig(prop);
</code></pre>

<p>有两种类型的生产者 - <strong>同步</strong>和<strong>异步</strong>。</p>

<p>同样的API配置也适用于同步生成器。它们之间的区别是同步生成器直接发送消息，但是在后台发送消息。当您想要更高的吞吐量时，Async生产者是首选。在以前的版本，如0.8，一个异步生成器没有回调send（）来注册错误处理程序。这仅在当前版本的0.9中可用。</p>

<p><strong>public void close（）</strong> <br>
生产者类提供了关闭与所有kafka兄弟的生产者池连接的紧密方法。</p>



<h3 id="配置设置">配置设置</h3>

<p>Producer API的主要配置设置列在下表中，以便更好地了解 -</p>

<table>
<thead>
<tr>
  <th>S.No</th>
  <th>配置设置和说明</th>
</tr>
</thead>
<tbody><tr>
  <td>1  client.id</td>
  <td>识别生产者应用程序</td>
</tr>
<tr>
  <td>2    producer.type</td>
  <td>同步或异步</td>
</tr>
<tr>
  <td>3    Acks</td>
  <td>acks配置控制生产者请求下的条件被完全匹配。</td>
</tr>
<tr>
  <td>4    retries</td>
  <td>如果生产者请求失败，则会自动重试具体值。</td>
</tr>
<tr>
  <td>5    bootstrap.servers</td>
  <td>经纪人的引导列表。</td>
</tr>
<tr>
  <td>6    linger.ms</td>
  <td>如果要减少请求数，可以将linger.ms设置为大于某值的值。</td>
</tr>
<tr>
  <td>7    key.serializer</td>
  <td>串行器接口的关键。</td>
</tr>
<tr>
  <td>8    value.serializer</td>
  <td>串行器接口的值。</td>
</tr>
<tr>
  <td>9   batch.size</td>
  <td>缓冲区大小。</td>
</tr>
<tr>
  <td>10  buffer.memory</td>
  <td>控制生产者可用于缓冲的总内存量。</td>
</tr>
</tbody></table>




<h3 id="producerrecord-api">ProducerRecord API</h3>

<p>ProducerRecord是发送给<code>Kafka cluster.ProducerRecord</code>类构造函数的一个键/值对，用于使用以下签名创建具有分区，键和值对的记录。</p>

<pre><code>public ProducerRecord (string topic, int partition, k key, v value)
</code></pre>

<ul>
<li><strong>Topic</strong> - 将附加到记录的用户定义的主题名称。</li>
<li><strong>Partition</strong> - 分区计数</li>
<li><strong>Key</strong> - 将包括在记录中的关键。</li>
<li><p><strong>Value</strong> - 记录内容</p>

<p>public ProducerRecord (string topic, k key, v value)</p></li>
</ul>

<p>ProducerRecord类构造函数用于创建具有键，值对和无分区的记录。</p>

<ul>
<li><strong>Topic</strong>- 创建主题以分配记录。</li>
<li><strong>Key</strong> - 键记录。</li>
<li><p><strong>Value</strong> - 记录内容。</p>

<p>public ProducerRecord (string topic, v value)</p></li>
</ul>

<p>ProducerRecord类创建一个没有分区和键的记录。</p>

<ul>
<li><strong>Topic</strong>- 创建主题。</li>
<li><strong>Value</strong> - 记录内容。</li>
</ul>

<p>ProducerRecord类方法列在下表中：</p>

<table>
<thead>
<tr>
  <th>S.No</th>
  <th>类方法和描述</th>
</tr>
</thead>
<tbody><tr>
  <td>1</td>
  <td>public string topic() 主题将附加到记录。</td>
</tr>
<tr>
  <td>2</td>
  <td>public K key（）将包含在记录中的关键字。如果没有这样的键，null将在这里重新转换。</td>
</tr>
<tr>
  <td>3</td>
  <td>public V value（）记录内容。</td>
</tr>
<tr>
  <td>4</td>
  <td>partition() 记录的分区数</td>
</tr>
</tbody></table>




<h3 id="simpleproducer应用程序">SimpleProducer应用程序</h3>

<p>在创建应用程序之前，首先启动ZooKeeper和Kafka代理，然后使用create topic命令在Kafka代理中创建自己的主题。之后，创建一个名为<code>SimplepleProducer.java</code>的java类，并键入以下代码。</p>



<pre class="prettyprint"><code class="language-java hljs ">
<span class="hljs-comment">//import util.properties packages</span>
<span class="hljs-keyword">import</span> java.util.Properties;

<span class="hljs-comment">//import simple producer packages</span>
<span class="hljs-keyword">import</span> org.apache.kafka.clients.producer.Producer;

<span class="hljs-comment">//import KafkaProducer packages</span>
<span class="hljs-keyword">import</span> org.apache.kafka.clients.producer.KafkaProducer;

<span class="hljs-comment">//import ProducerRecord packages</span>
<span class="hljs-keyword">import</span> org.apache.kafka.clients.producer.ProducerRecord;

<span class="hljs-comment">//Create java class named “SimpleProducer”</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">SimpleProducer</span> {</span>

   <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> Exception{

      <span class="hljs-comment">// Check arguments length value</span>
      <span class="hljs-keyword">if</span>(args.length == <span class="hljs-number">0</span>){
         System.out.println(<span class="hljs-string">"Enter topic name”);
         return;
      }

      //Assign topicName to string variable
      String topicName = args[0].toString();

      // create instance for properties to access producer configs   
      Properties props = new Properties();

      //Assign localhost id
      props.put("</span>bootstrap.servers<span class="hljs-string">", “localhost:9092"</span>);

      <span class="hljs-comment">//Set acknowledgements for producer requests.      </span>
      props.put(<span class="hljs-string">"acks"</span>, “all<span class="hljs-string">");

      //If the request fails, the producer can automatically retry,
      props.put("</span>retries<span class="hljs-string">", 0);

      //Specify buffer size in config
      props.put("</span>batch.size<span class="hljs-string">", 16384);

      //Reduce the no of requests less than 0   
      props.put("</span>linger.ms<span class="hljs-string">", 1);

      //The buffer.memory controls the total amount of memory available to the producer for buffering.   
      props.put("</span>buffer.memory<span class="hljs-string">", 33554432);

      props.put("</span>key.serializer<span class="hljs-string">", 
         "</span>org.apache.kafka.common.serializa-tion.StringSerializer<span class="hljs-string">");

      props.put("</span>value.serializer<span class="hljs-string">", 
         "</span>org.apache.kafka.common.serializa-tion.StringSerializer<span class="hljs-string">");

      Producer&lt;String, String&gt; producer = new KafkaProducer
         &lt;String, String&gt;(props);

      for(int i = 0; i &lt; 10; i++)
         producer.send(new ProducerRecord&lt;String, String&gt;(topicName, 
            Integer.toString(i), Integer.toString(i)));
               System.out.println(“Message sent successfully”);
               producer.close();
   }
}</span></code></pre>

<p>编译 - 可以使用以下命令编译应用程序。</p>

<pre><code>javac -cp “/path/to/kafka/kafka_2.11-0.9.0.0/lib/*” *.java
</code></pre>

<p>执行 - 可以使用以下命令执行应用程序。</p>

<pre><code>java -cp “/path/to/kafka/kafka_2.11-0.9.0.0/lib/*”:. SimpleProducer &lt;topic-name&gt;
</code></pre>

<p>产量</p>



<pre class="prettyprint"><code class="language-java hljs ">Message sent successfully
To check the above output open <span class="hljs-keyword">new</span> terminal and type Consumer CLI command to receive messages.
&gt;&gt; bin/kafka-console-consumer.sh --zookeeper localhost:<span class="hljs-number">2181</span> —topic &lt;topic-name&gt; —from-beginning
<span class="hljs-number">1</span>
<span class="hljs-number">2</span>
<span class="hljs-number">3</span>
<span class="hljs-number">4</span>
<span class="hljs-number">5</span>
<span class="hljs-number">6</span>
<span class="hljs-number">7</span>
<span class="hljs-number">8</span>
<span class="hljs-number">9</span>
<span class="hljs-number">10</span></code></pre>



<h3 id="简单的消费者例子">简单的消费者例子</h3>

<p>到目前为止，我们已经创建了一个生产者来发送消息到Kafka集群。现在让我们创建一个消费者来消费kafka群集的消息。KafkaConsumer API用于消费来自Kafka群集的消息。KafkaConsumer类构造函数定义如下。</p>

<pre><code>public KafkaConsumer(java.util.Map&lt;java.lang.String,java.lang.Object&gt; configs)
</code></pre>

<p>configs - 返回消费者配置的映射。</p>

<p><code>KafkaConsumer</code>类具有下表中列出的以下重要方法。</p>

<table>
<thead>
<tr>
  <th>S.No</th>
  <th>方法和说明</th>
</tr>
</thead>
<tbody><tr>
  <td>1</td>
  <td>public java.util.Set  assignment（）获取当前由con-sumer分配的分区集。</td>
</tr>
<tr>
  <td>2</td>
  <td>public string subscription（）订阅给定的主题列表以获得动态签名的分区。</td>
</tr>
<tr>
  <td>3</td>
  <td>public void sub-scribe（java.util.List  topics，ConsumerRe-balanceListener listener）订阅给定的主题列表以获得动态签名的分区。</td>
</tr>
<tr>
  <td>4</td>
  <td>public void unsubscribe（）从给定的分区列表中取消订阅主题。</td>
</tr>
<tr>
  <td>5</td>
  <td>public void sub-scribe（java.util.List  topics）订阅给定的主题列表以获得动态签名的分区。如果给定的主题列表为空，则它将被视为与unsubscribe（）相同。</td>
</tr>
<tr>
  <td>6</td>
  <td>public void sub-scribe（java.util.regex.Pattern pattern，ConsumerRebalanceLis-tener listener）参数模式是指以正则表达式格式的订阅模式，并且listener参数从订阅模式获取通知。</td>
</tr>
<tr>
  <td>7</td>
  <td>public void as-sign（java.util.List 分区）手动分配给客户的分区列表。</td>
</tr>
<tr>
  <td>8</td>
  <td>poll()获取使用其中一个订阅/分配API指定的主题或分区的数据。如果在轮询数据之前没有订阅主题，这将返回错误。</td>
</tr>
<tr>
  <td>9</td>
  <td>public void commitSync（）针对所有主题和分区的划分列表，对最后一次poll（）返回的提交偏移量。相同的操作将应用于commitAsyn（）。</td>
</tr>
<tr>
  <td>10</td>
  <td>public void seek（TopicPartition partition，long offset）获取消费者将在下一个poll（）方法上使用的当前偏移值。</td>
</tr>
<tr>
  <td>11</td>
  <td>public void resume（）恢复已暂停的分区。</td>
</tr>
<tr>
  <td>12</td>
  <td>public void wakeup（）唤醒消费者。</td>
</tr>
</tbody></table>




<h3 id="consumerrecord-api">ConsumerRecord API</h3>

<p><code>ConsumerRecord API</code>用于从Kafka集群接收记录。该API由一个主题名称，分区号，从其接收的记录和指向Kafka分区中的记录的偏移量组成。<code>ConsumerRecord</code>类用于创建具有特定主题名称，分区计数和</p>

<h3 id="consumerrecords类定义了以下方法">ConsumerRecords类定义了以下方法。</h3>

<table>
<thead>
<tr>
  <th>S.No</th>
  <th>方法和说明</th>
</tr>
</thead>
<tbody><tr>
  <td>1</td>
  <td>public int count（） 所有主题的记录数。</td>
</tr>
<tr>
  <td>2</td>
  <td>public set partitions（） 该记录集中的数据集（如果没有数据被返回，则该集合为空）。</td>
</tr>
<tr>
  <td>3</td>
  <td>public Iterator iterator（） 迭代器使您能够遍历集合，获取或重新移动元素。</td>
</tr>
<tr>
  <td>4</td>
  <td>公开列表记录（） 获取给定分区的记录列表。</td>
</tr>
</tbody></table>




<h3 id="配置设置-1">配置设置</h3>

<p>Consumer客户端API主配置设置的配置设置如下所示：</p>

<table>
<thead>
<tr>
  <th>S.No</th>
  <th>设置和说明</th>
</tr>
</thead>
<tbody><tr>
  <td>1</td>
  <td>bootstrap.servers 经纪人列表。</td>
</tr>
<tr>
  <td>2</td>
  <td>group.id 将一个消费者分配给一个组。</td>
</tr>
<tr>
  <td>3</td>
  <td>enable.auto.commit 如果值为true，则启用自动提交偏移量，否则不提交。</td>
</tr>
<tr>
  <td>4</td>
  <td>auto.commit.interval.ms 更新消耗的偏移量返回给ZooKeeper的频率。</td>
</tr>
<tr>
  <td>5</td>
  <td>session.timeout.ms 表示Kafka将在放弃并继续使用消息之前等待ZooKeeper响应请求（读取或写入）多少毫秒。</td>
</tr>
</tbody></table>


<p><strong>SimpleConsumer应用程序</strong> <br>
生产者应用步骤在此保持不变。首先，启动您的ZooKeeper和Kafka经纪人。然后使用名为<code>SimpleCon-sumer.java</code>的java类创建一个<code>SimpleConsumer</code>应用程序，并键入以下代码。</p>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">import</span> java.util.Properties;
<span class="hljs-keyword">import</span> java.util.Arrays;
<span class="hljs-keyword">import</span> org.apache.kafka.clients.consumer.KafkaConsumer;
<span class="hljs-keyword">import</span> org.apache.kafka.clients.consumer.ConsumerRecords;
<span class="hljs-keyword">import</span> org.apache.kafka.clients.consumer.ConsumerRecord;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">SimpleConsumer</span> {</span>
   <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> Exception {
      <span class="hljs-keyword">if</span>(args.length == <span class="hljs-number">0</span>){
         System.out.println(<span class="hljs-string">"Enter topic name"</span>);
         <span class="hljs-keyword">return</span>;
      }
      <span class="hljs-comment">//Kafka consumer configuration settings</span>
      String topicName = args[<span class="hljs-number">0</span>].toString();
      Properties props = <span class="hljs-keyword">new</span> Properties();

      props.put(<span class="hljs-string">"bootstrap.servers"</span>, <span class="hljs-string">"localhost:9092"</span>);
      props.put(<span class="hljs-string">"group.id"</span>, <span class="hljs-string">"test"</span>);
      props.put(<span class="hljs-string">"enable.auto.commit"</span>, <span class="hljs-string">"true"</span>);
      props.put(<span class="hljs-string">"auto.commit.interval.ms"</span>, <span class="hljs-string">"1000"</span>);
      props.put(<span class="hljs-string">"session.timeout.ms"</span>, <span class="hljs-string">"30000"</span>);
      props.put(<span class="hljs-string">"key.deserializer"</span>, 
         <span class="hljs-string">"org.apache.kafka.common.serializa-tion.StringDeserializer"</span>);
      props.put(<span class="hljs-string">"value.deserializer"</span>, 
         <span class="hljs-string">"org.apache.kafka.common.serializa-tion.StringDeserializer"</span>);
      KafkaConsumer&lt;String, String&gt; consumer = <span class="hljs-keyword">new</span> KafkaConsumer
         &lt;String, String&gt;(props);

      <span class="hljs-comment">//Kafka Consumer subscribes list of topics here.</span>
      consumer.subscribe(Arrays.asList(topicName))

      <span class="hljs-comment">//print the topic name</span>
      System.out.println(<span class="hljs-string">"Subscribed to topic "</span> + topicName);
      <span class="hljs-keyword">int</span> i = <span class="hljs-number">0</span>;

      <span class="hljs-keyword">while</span> (<span class="hljs-keyword">true</span>) {
         ConsumerRecords&lt;String, String&gt; records = con-sumer.poll(<span class="hljs-number">100</span>);
         <span class="hljs-keyword">for</span> (ConsumerRecord&lt;String, String&gt; record : records)

         <span class="hljs-comment">// print the offset,key and value for the consumer records.</span>
         System.out.printf(<span class="hljs-string">"offset = %d, key = %s, value = %s\n"</span>, 
            record.offset(), record.key(), record.value());
      }
   }
}</code></pre>

<p><strong>编译 - 可以使用以下命令编译应用程序。</strong></p>

<pre><code>javac -cp “/path/to/kafka/kafka_2.11-0.9.0.0/lib/*” *.java
</code></pre>

<p><strong>执行 -可以使用以下命令执行应用程序</strong></p>

<pre><code>java -cp “/path/to/kafka/kafka_2.11-0.9.0.0/lib/*”:. SimpleConsumer &lt;topic-name&gt;
</code></pre>

<p><strong>输入 - 打开生产者CLI并向主题发送一些消息。您可以将smple输入作为“您好消费者”。</strong></p>

<p><strong>输出 - 以下是输出。</strong></p>

<pre><code>Subscribed to topic Hello-Kafka
offset = 3, key = null, value = Hello Consumer
</code></pre>

<hr>

<p><code>1</code> <a href="http://blogxinxiucan.sh1.newtouch.com/2017/07/12/Apache-Kafka-%E4%BB%8B%E7%BB%8D/" rel="nofollow" target="_blank">Apache Kafka - 介绍</a> <br>
<code>2</code> <a href="http://blogxinxiucan.sh1.newtouch.com/2017/07/12/Apache-Kafka-%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B/" rel="nofollow" target="_blank">Apache Kafka -工作流程</a> <br>
<code>3</code> <a href="http://blogxinxiucan.sh1.newtouch.com/2017/07/13/Apache-Kafka-%E5%AE%89%E8%A3%85%E6%AD%A5%E9%AA%A4/" rel="nofollow" target="_blank">Apache Kafka - 安装步骤</a> <br>
<code>4</code> <a href="http://blogxinxiucan.sh1.newtouch.com/2017/07/13/Apache-Kafka-%E5%9F%BA%E6%9C%AC%E6%93%8D%E4%BD%9C/" rel="nofollow" target="_blank">Apache Kafka - 基本操作</a> <br>
<code>5</code> <a href="http://blogxinxiucan.sh1.newtouch.com/2017/07/13/Apache-Kafka-%E7%94%9F%E4%BA%A7%E8%80%85%E7%A4%BA%E4%BE%8B/" rel="nofollow" target="_blank">Apache Kafka - 生产者示例</a> <br>
<code>6</code> <a href="http://blogxinxiucan.sh1.newtouch.com/2017/07/14/Apache-Kafka-%E6%B6%88%E8%B4%B9%E8%80%85%E7%A4%BA%E4%BE%8B/" rel="nofollow" target="_blank">Apache Kafka - 消费者示例</a> <br>
<code>7</code> <a href="http://blogxinxiucan.sh1.newtouch.com/2017/07/14/Apache-Kafka-%E4%B8%8EStorm%E9%9B%86%E6%88%90/" rel="nofollow" target="_blank">Apache Kafka - 与Storm集成</a> <br>
<code>8</code> <a href="http://blogxinxiucan.sh1.newtouch.com/2017/07/14/Apache-Kafka-%E4%B8%8ESpark%E9%9B%86%E6%88%90/" rel="nofollow" target="_blank">Apache Kafka - 与Spark集成</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>