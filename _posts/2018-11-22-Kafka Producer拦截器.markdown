---
layout:     post
title:      Kafka Producer拦截器
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主朱小厮允许不得转载。					https://blog.csdn.net/u013256816/article/details/78573425				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>Kafka中的拦截器（Interceptor）是0.10.x.x版本引入的一个功能，一共有两种：Kafka Producer端的拦截器和Kafka Consumer端的拦截器。本篇主要讲述的是Kafka Producer端的拦截器，它主要用来对消息进行拦截或者修改，也可以用于Producer的Callback回调之前进行相应的预处理。</p>

<p>使用Kafka Producer端的拦截器非常简单，主要是实现ProducerInterceptor接口，此接口包含4个方法：</p>

<ol>
<li>ProducerRecord&lt;K, V&gt; onSend(ProducerRecord&lt;K, V&gt; record)：Producer在将消息序列化和分配分区之前会调用拦截器的这个方法来对消息进行相应的操作。一般来说最好不要修改消息ProducerRecord的topic、key以及partition等信息，如果要修改，也需确保对其有准确的判断，否则会与预想的效果出现偏差。比如修改key不仅会影响分区的计算，同样也会影响Broker端日志压缩（Log Compaction）的功能。</li>
<li>void onAcknowledgement(RecordMetadata metadata, Exception exception)：在消息被应答（Acknowledgement）之前或者消息发送失败时调用，优先于用户设定的Callback之前执行。这个方法运行在Producer的IO线程中，所以这个方法里实现的代码逻辑越简单越好，否则会影响消息的发送速率。</li>
<li>void close()：关闭当前的拦截器，此方法主要用于执行一些资源的清理工作。</li>
<li>configure(Map&lt;String, ?&gt; configs)：用来初始化此类的方法，这个是ProducerInterceptor接口的父接口Configurable中的方法。</li>
</ol>

<p>一般情况下只需要关注并实现onSend或者onAcknowledgement方法即可。下面我们来举个案例，通过onSend方法来过滤消息体为空的消息以及通过onAcknowledgement方法来计算发送消息的成功率。</p>



<pre class="prettyprint"><code class=" hljs cs"><span class="hljs-keyword">public</span> <span class="hljs-keyword">class</span> ProducerInterceptorDemo implements ProducerInterceptor&lt;String,String&gt; {
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">volatile</span> <span class="hljs-keyword">long</span> sendSuccess = <span class="hljs-number">0</span>;
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">volatile</span> <span class="hljs-keyword">long</span> sendFailure = <span class="hljs-number">0</span>;

    @Override
    <span class="hljs-keyword">public</span> ProducerRecord&lt;String, String&gt; <span class="hljs-title">onSend</span>(ProducerRecord&lt;String, String&gt; record) {
        <span class="hljs-keyword">if</span>(record.<span class="hljs-keyword">value</span>().length()&lt;=<span class="hljs-number">0</span>)
            <span class="hljs-keyword">return</span> <span class="hljs-keyword">null</span>;
        <span class="hljs-keyword">return</span> record;
    }

    @Override
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">onAcknowledgement</span>(RecordMetadata metadata, Exception exception) {
        <span class="hljs-keyword">if</span> (exception == <span class="hljs-keyword">null</span>) {
            sendSuccess++;
        } <span class="hljs-keyword">else</span> {
            sendFailure ++;
        }
    }

    @Override
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">close</span>() {
        <span class="hljs-keyword">double</span> successRatio = (<span class="hljs-keyword">double</span>)sendSuccess / (sendFailure + sendSuccess);
        System.<span class="hljs-keyword">out</span>.println(<span class="hljs-string">"[INFO] 发送成功率="</span>+String.format(<span class="hljs-string">"%f"</span>, successRatio * <span class="hljs-number">100</span>)+<span class="hljs-string">"%"</span>);
    }

    @Override
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">configure</span>(Map&lt;String, ?&gt; configs) {}
}</code></pre>

<p>自定义的ProducerInterceptorDemo类实现之后就可以在Kafka Producer的主程序中指定，示例代码如下：</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">ProducerMain</span> {</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> String brokerList = <span class="hljs-string">"localhost:9092"</span>;
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> String topic = <span class="hljs-string">"hidden-topic"</span>;

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> ExecutionException, InterruptedException {
        Properties properties = <span class="hljs-keyword">new</span> Properties();
        properties.put(<span class="hljs-string">"key.serializer"</span>, <span class="hljs-string">"org.apache.kafka.common.serialization.StringSerializer"</span>);
        properties.put(<span class="hljs-string">"value.serializer"</span>, <span class="hljs-string">"org.apache.kafka.common.serialization.StringSerializer"</span>);
        properties.put(<span class="hljs-string">"bootstrap.servers"</span>, brokerList);
        properties.put(<span class="hljs-string">"interceptor.classes"</span>, <span class="hljs-string">"com.hidden.producer.ProducerInterceptorDemo"</span>);

        Producer&lt;String, String&gt; producer = <span class="hljs-keyword">new</span> KafkaProducer&lt;String, String&gt;(properties);

        <span class="hljs-keyword">for</span>(<span class="hljs-keyword">int</span> i=<span class="hljs-number">0</span>;i&lt;<span class="hljs-number">100</span>;i++) {
            ProducerRecord&lt;String, String&gt; producerRecord = <span class="hljs-keyword">new</span> ProducerRecord&lt;String, String&gt;(topic, <span class="hljs-string">"msg-"</span> + i);
            producer.send(producerRecord).get();
        }
        producer.close();
    }
}</code></pre>

<p>Kafka Producer不仅可以指定一个拦截器，还可以指定多个拦截器以形成拦截链，这个拦截链会按照其中的拦截器的加入顺序一一执行。比如上面的程序多添加一个拦截器，示例如下：</p>



<pre class="prettyprint"><code class=" hljs avrasm">properties<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"interceptor.classes"</span>, <span class="hljs-string">"com.hidden.producer.ProducerInterceptorDemo,com.hidden.producer.ProducerInterceptorDemoPlus"</span>)<span class="hljs-comment">;</span></code></pre>

<p>这样Kafka Producer会先执行拦截器ProducerInterceptorDemo，之后再执行ProducerInterceptorDemoPlus。</p>

<p>有关interceptor.classes参数，在kafka 1.0.0版本中的定义如下：</p>

<table>
<thead>
<tr>
  <th>NAME</th>
  <th>DESCRIPTION</th>
  <th>TYPE</th>
  <th>DEFAULT</th>
  <th>VALID VALUES</th>
  <th>IMPORTANCE</th>
</tr>
</thead>
<tbody><tr>
  <td>interceptor.calssses</td>
  <td>A list of classes to use as interceptors. Implementing the org.apache.kafka.clients.producer.ProducerInterceptor interface allows you to intercept (and possibly mutate) the records received by the producer before they are published to the Kafka cluster. By default, there no interceptors.</td>
  <td>list</td>
  <td>null</td>
  <td></td>
  <td>low</td>
</tr>
</tbody></table>


<hr>

<p>欢迎支持《RabbitMQ实战指南》以及关注微信公众号：朱小厮的博客。 <br>
<img src="https://img-blog.csdn.net/20180116193232073?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzI1NjgxNg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>