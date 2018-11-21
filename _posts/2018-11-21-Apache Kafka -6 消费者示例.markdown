---
layout:     post
title:      Apache Kafka -6 消费者示例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="apache-kafka教程-之-apache-kafka-消费者示例">Apache Kafka教程  之 Apache Kafka - 消费者示例</h2>

<p><img src="http://upload-images.jianshu.io/upload_images/6871092-e776d27ac7f329bd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" alt="http://blogxinxiucan.sh1.newtouch.com/" title=""></p>

<p><strong>个人博客地址：</strong> <a href="http://blogxinxiucan.sh1.newtouch.com/2017/07/14/Apache-Kafka-%E6%B6%88%E8%B4%B9%E8%80%85%E7%A4%BA%E4%BE%8B/" rel="nofollow" target="_blank">http://blogxinxiucan.sh1.newtouch.com/2017/07/13/Apache-Kafka-消费者示例/</a></p>



<h3 id="apache-kafka-消费者示例consumer-group">Apache Kafka - 消费者示例（Consumer Group）</h3>

<p><strong>Consumer Group是从kafka Topic的多线程或多机器消费。</strong></p>

<blockquote>
  <p><strong>Consumer Group</strong> <br>
   - 消费者可以使用相同的<code>group.id</code>加入<code>Group</code>。 <br>
   - 组中的最大并行度是组中的消费者数量←不是分区。 <br>
   - kafka将一个 Topic的分区分配给组中的消费者，以便每个分区由组中的一个消费者消耗。 <br>
   - kafka保证消息只能由组内的<strong>单个消费者</strong>读取。 <br>
   - 消费者可以按照日志中存储的顺序查看消息。</p>
</blockquote>



<h3 id="重新平衡消费者">重新平衡消费者</h3>

<p><strong>添加更多进程/线程将导致kafka重新平衡。如果任何消费者或broker无法向<code>ZooKeeper</code>发送心跳信息，则可以通过Kafka群集重新配置。在此重新平衡过程中，Kafka会将可用的分区分配给可用的线程，可能将分区移动到另一个进程。</strong></p>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">import</span> java.util.Properties;
<span class="hljs-keyword">import</span> java.util.Arrays;
<span class="hljs-keyword">import</span> org.apache.kafka.clients.consumer.KafkaConsumer;
<span class="hljs-keyword">import</span> org.apache.kafka.clients.consumer.ConsumerRecords;
<span class="hljs-keyword">import</span> org.apache.kafka.clients.consumer.ConsumerRecord;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">ConsumerGroup</span> {</span>
   <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> Exception {
      <span class="hljs-keyword">if</span>(args.length &lt; <span class="hljs-number">2</span>){
         System.out.println(<span class="hljs-string">"Usage: consumer &lt;topic&gt; &lt;groupname&gt;"</span>);
         <span class="hljs-keyword">return</span>;
      }

      String topic = args[<span class="hljs-number">0</span>].toString();
      String group = args[<span class="hljs-number">1</span>].toString();
      Properties props = <span class="hljs-keyword">new</span> Properties();
      props.put(<span class="hljs-string">"bootstrap.servers"</span>, <span class="hljs-string">"localhost:9092"</span>);
      props.put(<span class="hljs-string">"group.id"</span>, group);
      props.put(<span class="hljs-string">"enable.auto.commit"</span>, <span class="hljs-string">"true"</span>);
      props.put(<span class="hljs-string">"auto.commit.interval.ms"</span>, <span class="hljs-string">"1000"</span>);
      props.put(<span class="hljs-string">"session.timeout.ms"</span>, <span class="hljs-string">"30000"</span>);
      props.put(<span class="hljs-string">"key.deserializer"</span>,          
         <span class="hljs-string">"org.apache.kafka.common.serializa-tion.StringDeserializer"</span>);
      props.put(<span class="hljs-string">"value.deserializer"</span>, 
         <span class="hljs-string">"org.apache.kafka.common.serializa-tion.StringDeserializer"</span>);
      KafkaConsumer&lt;String, String&gt; consumer = <span class="hljs-keyword">new</span> KafkaConsumer&lt;String, String&gt;(props);

      consumer.subscribe(Arrays.asList(topic));
      System.out.println(<span class="hljs-string">"Subscribed to topic "</span> + topic);
      <span class="hljs-keyword">int</span> i = <span class="hljs-number">0</span>;

      <span class="hljs-keyword">while</span> (<span class="hljs-keyword">true</span>) {
         ConsumerRecords&lt;String, String&gt; records = con-sumer.poll(<span class="hljs-number">100</span>);
            <span class="hljs-keyword">for</span> (ConsumerRecord&lt;String, String&gt; record : records)
               System.out.printf(<span class="hljs-string">"offset = %d, key = %s, value = %s\n"</span>, 
               record.offset(), record.key(), record.value());
      }     
   }  
}</code></pre>

<p>汇编</p>

<pre><code>javac -cp “/path/to/kafka/kafka_2.11-0.9.0.0/libs/*" ConsumerGroup.java
</code></pre>

<p>执行</p>

<pre><code>&gt;&gt;java -cp “/path/to/kafka/kafka_2.11-0.9.0.0/libs/*":. 
ConsumerGroup &lt;topic-name&gt; my-group
&gt;&gt;java -cp "/home/bala/Workspace/kafka/kafka_2.11-0.9.0.0/libs/*":. 
ConsumerGroup &lt;topic-name&gt; my-group
</code></pre>

<p>在这里，我们创建了一个示例组名称作为我的组与两个消费者。同样，您可以创建组中的组和消费者数量。</p>

<p>输入 <br>
打开生产者CLI并发送一些消息，如 -</p>

<pre><code>Test consumer group 01
Test consumer group 02
</code></pre>

<p>第一个过程的输出</p>

<pre><code>Subscribed to topic Hello-kafka
offset = 3, key = null, value = Test consumer group 01
</code></pre>

<p>第二过程的产出</p>

<pre><code>Subscribed to topic Hello-kafka
offset = 3, key = null, value = Test consumer group 02
</code></pre>

<p>现在希望您将通过使用Java客户端演示了解<code>SimpleConsumer</code>和<code>ConsumeGroup</code>。现在您了解如何使用Java客户端发送和接收消息。</p>

<hr>

<p><code>1</code> <a href="http://blogxinxiucan.sh1.newtouch.com/2017/07/12/Apache-Kafka-%E4%BB%8B%E7%BB%8D/" rel="nofollow">Apache Kafka - 介绍</a> <br>
<code>2</code> <a href="http://blogxinxiucan.sh1.newtouch.com/2017/07/12/Apache-Kafka-%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B/" rel="nofollow">Apache Kafka -工作流程</a> <br>
<code>3</code> <a href="http://blogxinxiucan.sh1.newtouch.com/2017/07/13/Apache-Kafka-%E5%AE%89%E8%A3%85%E6%AD%A5%E9%AA%A4/" rel="nofollow">Apache Kafka - 安装步骤</a> <br>
<code>4</code> <a href="http://blogxinxiucan.sh1.newtouch.com/2017/07/13/Apache-Kafka-%E5%9F%BA%E6%9C%AC%E6%93%8D%E4%BD%9C/" rel="nofollow">Apache Kafka - 基本操作</a> <br>
<code>5</code> <a href="http://blogxinxiucan.sh1.newtouch.com/2017/07/13/Apache-Kafka-%E7%94%9F%E4%BA%A7%E8%80%85%E7%A4%BA%E4%BE%8B/" rel="nofollow">Apache Kafka - 生产者示例</a> <br>
<code>6</code> <a href="http://blogxinxiucan.sh1.newtouch.com/2017/07/14/Apache-Kafka-%E6%B6%88%E8%B4%B9%E8%80%85%E7%A4%BA%E4%BE%8B/" rel="nofollow">Apache Kafka - 消费者示例</a> <br>
<code>7</code> <a href="http://blogxinxiucan.sh1.newtouch.com/2017/07/14/Apache-Kafka-%E4%B8%8EStorm%E9%9B%86%E6%88%90/" rel="nofollow">Apache Kafka - 与Storm集成</a> <br>
<code>8</code> <a href="http://blogxinxiucan.sh1.newtouch.com/2017/07/14/Apache-Kafka-%E4%B8%8ESpark%E9%9B%86%E6%88%90/" rel="nofollow">Apache Kafka - 与Spark集成</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>