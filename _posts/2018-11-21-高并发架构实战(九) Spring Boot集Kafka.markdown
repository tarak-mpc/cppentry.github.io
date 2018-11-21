---
layout:     post
title:      高并发架构实战(九) Spring Boot集Kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>转载请标注原文地址：<a href="https://blog.csdn.net/lilyssh/article/details/83346324" rel="nofollow">https://blog.csdn.net/lilyssh/article/details/83346324</a></p>
<p>Spring Boot 2.0.4 集成 <a href="http://kafka.apache.org/" rel="nofollow">Kafka</a> 2.0.0。<a href="https://gitee.com/lilyssh/high-concurrency" rel="nofollow">项目源码地址</a></p>
<h1><a id="_3"></a>一、简介</h1>
<p>kafka是一种高吞吐量的分布式发布订阅消息系统。kafka对消息保存时根据Topic进行归类，发送消息者成为Producer,消息接受者成为Consumer,此外kafka集群有多个kafka实例组成，每个实例(server)成为broker。无论是kafka集群，还是producer和consumer都依赖于zookeeper来保证系统可用性集群保存一些meta信息。<br>
Kafka的安装请参考文章：<a href="https://blog.csdn.net/lilyssh/article/details/83346201" rel="nofollow">Kafka的安装与使用</a>。</p>
<h1><a id="_6"></a>二、使用方法</h1>
<h2><a id="1_7"></a>（1）添加依赖</h2>
<pre><code>&lt;dependency&gt;
    &lt;groupId&gt;org.springframework.kafka&lt;/groupId&gt;
    &lt;artifactId&gt;spring-kafka&lt;/artifactId&gt;
    &lt;version&gt;2.1.10.RELEASE&lt;/version&gt;
&lt;/dependency&gt;
</code></pre>
<h2><a id="2applicationyml_15"></a>（2）在<code>application.yml</code>中添加配置</h2>
<p>官方文档说只要配置两个必要项就可以了，spring.kafka.consumer.group-id和spring.kafka.consumer.auto-offset-reset。此处对其他配置稍作解释。</p>
<pre><code class="prism language-yml"><span class="token key atrule">spring</span><span class="token punctuation">:</span>
  <span class="token key atrule">kafka</span><span class="token punctuation">:</span>
    <span class="token comment"># 指定kafka代理地址，brokers集群。</span>
    <span class="token key atrule">bootstrap-servers</span><span class="token punctuation">:</span> ssh.qianxunclub.com<span class="token punctuation">:</span><span class="token number">9092</span>
    <span class="token key atrule">producer</span><span class="token punctuation">:</span>
      <span class="token comment"># 发送失败重试次数。</span>
      <span class="token key atrule">retries</span><span class="token punctuation">:</span> <span class="token number">0</span>
      <span class="token comment"># 每次批量发送消息的数量 批处理条数：当多个记录被发送到同一个分区时，生产者会尝试将记录合并到更少的请求中。这有助于客户端和服务器的性能。</span>
      <span class="token key atrule">batch-size</span><span class="token punctuation">:</span> <span class="token number">16384</span>
      <span class="token comment"># 32MB的批处理缓冲区。</span>
      <span class="token key atrule">buffer-memory</span><span class="token punctuation">:</span> <span class="token number">33554432</span>
      <span class="token comment"># 指定消息key和消息体的编解码方式。</span>
      <span class="token key atrule">key-serializer</span><span class="token punctuation">:</span> org.apache.kafka.common.serialization.StringSerializer
      <span class="token key atrule">value-serializer</span><span class="token punctuation">:</span> org.apache.kafka.common.serialization.StringSerializer
    <span class="token key atrule">consumer</span><span class="token punctuation">:</span>
      <span class="token comment"># 消费者群组ID，发布-订阅模式，即如果一个生产者，多个消费者都要消费，那么需要定义自己的群组，同一群组内的消费者只有一个能消费到消息。</span>
      <span class="token key atrule">group-id</span><span class="token punctuation">:</span> kafka_order_group
      <span class="token key atrule">auto-offset-reset</span><span class="token punctuation">:</span> earliest
      <span class="token comment"># 如果为true，消费者的偏移量将在后台定期提交。</span>
      <span class="token key atrule">enable-auto-commit</span><span class="token punctuation">:</span> <span class="token boolean important">true</span>
      <span class="token comment"># 自动提交周期</span>
      <span class="token key atrule">auto-commit-interval</span><span class="token punctuation">:</span> <span class="token number">100</span>
      <span class="token comment"># 指定消息key和消息体的编解码方式。</span>
      <span class="token key atrule">key-deserializer</span><span class="token punctuation">:</span> org.apache.kafka.common.serialization.StringDeserializer
      <span class="token key atrule">value-deserializer</span><span class="token punctuation">:</span> org.apache.kafka.common.serialization.StringDeserializer
</code></pre>
<h2><a id="3_44"></a>（3）消息发送类</h2>
<pre><code class="prism language-java"><span class="token keyword">package</span> cn<span class="token punctuation">.</span>lilyssh<span class="token punctuation">.</span>common<span class="token punctuation">.</span>kafka<span class="token punctuation">.</span>provider<span class="token punctuation">;</span>

<span class="token keyword">import</span> com<span class="token punctuation">.</span>google<span class="token punctuation">.</span>gson<span class="token punctuation">.</span>Gson<span class="token punctuation">;</span>
<span class="token keyword">import</span> com<span class="token punctuation">.</span>google<span class="token punctuation">.</span>gson<span class="token punctuation">.</span>GsonBuilder<span class="token punctuation">;</span>
<span class="token keyword">import</span> lombok<span class="token punctuation">.</span>extern<span class="token punctuation">.</span>slf4j<span class="token punctuation">.</span>Slf4j<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>beans<span class="token punctuation">.</span>factory<span class="token punctuation">.</span>annotation<span class="token punctuation">.</span>Autowired<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>stereotype<span class="token punctuation">.</span>Component<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>kafka<span class="token punctuation">.</span>core<span class="token punctuation">.</span>KafkaTemplate<span class="token punctuation">;</span>

<span class="token annotation punctuation">@Component</span>
<span class="token annotation punctuation">@Slf4j</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">KafkaSender</span> <span class="token punctuation">{</span>
    <span class="token annotation punctuation">@Autowired</span>
    <span class="token keyword">private</span> KafkaTemplate<span class="token generics function"><span class="token punctuation">&lt;</span>String<span class="token punctuation">,</span>String<span class="token punctuation">&gt;</span></span> kafkaTemplate<span class="token punctuation">;</span>
    <span class="token keyword">private</span> Gson gson <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">GsonBuilder</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">create</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">//发送消息方法</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">send</span><span class="token punctuation">(</span>String topic<span class="token punctuation">,</span>String key<span class="token punctuation">,</span>Object message<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        kafkaTemplate<span class="token punctuation">.</span><span class="token function">send</span><span class="token punctuation">(</span>topic<span class="token punctuation">,</span>key<span class="token punctuation">,</span>gson<span class="token punctuation">.</span><span class="token function">toJson</span><span class="token punctuation">(</span>message<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"+++++++++++++++++++++  message = {}"</span><span class="token punctuation">,</span> gson<span class="token punctuation">.</span><span class="token function">toJson</span><span class="token punctuation">(</span>message<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<p>此处关键代码为<code>kafkaTemplate.send()</code>，参数topic是<code>Kafka</code>里的<code>topic</code>，这个<code>topic</code>在 Java程序中是不需要提前在<code>Kafka</code>中设置的，因为它会在发送的时候自动创建你设置的<code>topic</code>， gson.toJson(message)是消息内容。</p>
<h2><a id="4_69"></a>（4）在下单业务中调用消息发送</h2>
<pre><code class="prism language-java"><span class="token keyword">package</span> cn<span class="token punctuation">.</span>lilyssh<span class="token punctuation">.</span>order<span class="token punctuation">.</span>provider<span class="token punctuation">.</span>service<span class="token punctuation">;</span>

<span class="token keyword">import</span> cn<span class="token punctuation">.</span>lilyssh<span class="token punctuation">.</span>common<span class="token punctuation">.</span>kafka<span class="token punctuation">.</span>provider<span class="token punctuation">.</span>KafkaSender<span class="token punctuation">;</span>
<span class="token keyword">import</span> cn<span class="token punctuation">.</span>lilyssh<span class="token punctuation">.</span>order<span class="token punctuation">.</span>api<span class="token punctuation">.</span>model<span class="token punctuation">.</span>request<span class="token punctuation">.</span>OrderInsertReq<span class="token punctuation">;</span>
<span class="token keyword">import</span> cn<span class="token punctuation">.</span>lilyssh<span class="token punctuation">.</span>order<span class="token punctuation">.</span>api<span class="token punctuation">.</span>service<span class="token punctuation">.</span>OrderServiceApi<span class="token punctuation">;</span>
<span class="token keyword">import</span> cn<span class="token punctuation">.</span>lilyssh<span class="token punctuation">.</span>order<span class="token punctuation">.</span>provider<span class="token punctuation">.</span>dao<span class="token punctuation">.</span>entity<span class="token punctuation">.</span>OrderEntity<span class="token punctuation">;</span>
<span class="token keyword">import</span> com<span class="token punctuation">.</span>alibaba<span class="token punctuation">.</span>dubbo<span class="token punctuation">.</span>config<span class="token punctuation">.</span>annotation<span class="token punctuation">.</span>Service<span class="token punctuation">;</span>
<span class="token keyword">import</span> lombok<span class="token punctuation">.</span>AllArgsConstructor<span class="token punctuation">;</span>
<span class="token keyword">import</span> lombok<span class="token punctuation">.</span>extern<span class="token punctuation">.</span>slf4j<span class="token punctuation">.</span>Slf4j<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>beans<span class="token punctuation">.</span>BeanUtils<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>stereotype<span class="token punctuation">.</span>Component<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>util<span class="token punctuation">.</span>StringUtils<span class="token punctuation">;</span>

<span class="token keyword">import</span> java<span class="token punctuation">.</span>math<span class="token punctuation">.</span>BigDecimal<span class="token punctuation">;</span>
<span class="token keyword">import</span> java<span class="token punctuation">.</span>util<span class="token punctuation">.</span>*<span class="token punctuation">;</span>

<span class="token annotation punctuation">@Slf4j</span>
<span class="token annotation punctuation">@Service</span>
<span class="token annotation punctuation">@Component</span>
<span class="token annotation punctuation">@AllArgsConstructor</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">OrderService</span> <span class="token keyword">implements</span> <span class="token class-name">OrderServiceApi</span> <span class="token punctuation">{</span>
    <span class="token keyword">private</span> KafkaSender kafkaSender<span class="token punctuation">;</span>
    <span class="token comment">/**
     * 保存到kafka
     * @param orderInsertReq
     * @return
     */</span>
    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">saveByKafka</span><span class="token punctuation">(</span>OrderInsertReq orderInsertReq<span class="token punctuation">)</span><span class="token punctuation">{</span>
        OrderEntity orderEntity<span class="token operator">=</span><span class="token keyword">new</span> <span class="token class-name">OrderEntity</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token comment">//直接写入数据库太慢，引起dubbo超时，导致调用多次，此处需要改造成kafka异步写入。</span>
        BeanUtils<span class="token punctuation">.</span><span class="token function">copyProperties</span><span class="token punctuation">(</span>orderInsertReq<span class="token punctuation">,</span>orderEntity<span class="token punctuation">)</span><span class="token punctuation">;</span>
        kafkaSender<span class="token punctuation">.</span><span class="token function">send</span><span class="token punctuation">(</span><span class="token string">"placeOrder"</span><span class="token punctuation">,</span> orderEntity<span class="token punctuation">.</span><span class="token function">getUserId</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> orderEntity<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<h2><a id="5_107"></a>（5）消息接收类</h2>
<pre><code class="prism language-java"><span class="token keyword">package</span> cn<span class="token punctuation">.</span>lilyssh<span class="token punctuation">.</span>order<span class="token punctuation">.</span>provider<span class="token punctuation">.</span>kafka<span class="token punctuation">.</span>consumer<span class="token punctuation">;</span>

<span class="token keyword">import</span> cn<span class="token punctuation">.</span>lilyssh<span class="token punctuation">.</span>order<span class="token punctuation">.</span>provider<span class="token punctuation">.</span>dao<span class="token punctuation">.</span>entity<span class="token punctuation">.</span>OrderEntity<span class="token punctuation">;</span>
<span class="token keyword">import</span> cn<span class="token punctuation">.</span>lilyssh<span class="token punctuation">.</span>order<span class="token punctuation">.</span>provider<span class="token punctuation">.</span>dao<span class="token punctuation">.</span>repository<span class="token punctuation">.</span>OrderRepository<span class="token punctuation">;</span>
<span class="token keyword">import</span> com<span class="token punctuation">.</span>google<span class="token punctuation">.</span>gson<span class="token punctuation">.</span>Gson<span class="token punctuation">;</span>
<span class="token keyword">import</span> lombok<span class="token punctuation">.</span>AllArgsConstructor<span class="token punctuation">;</span>
<span class="token keyword">import</span> lombok<span class="token punctuation">.</span>extern<span class="token punctuation">.</span>slf4j<span class="token punctuation">.</span>Slf4j<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>kafka<span class="token punctuation">.</span>clients<span class="token punctuation">.</span>consumer<span class="token punctuation">.</span>ConsumerRecord<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>kafka<span class="token punctuation">.</span>annotation<span class="token punctuation">.</span>KafkaListener<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>stereotype<span class="token punctuation">.</span>Component<span class="token punctuation">;</span>

<span class="token keyword">import</span> java<span class="token punctuation">.</span>util<span class="token punctuation">.</span>Optional<span class="token punctuation">;</span>

<span class="token annotation punctuation">@Component</span>
<span class="token annotation punctuation">@Slf4j</span>
<span class="token annotation punctuation">@AllArgsConstructor</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">KafkaReceiver</span> <span class="token punctuation">{</span>

    <span class="token keyword">private</span> OrderRepository orderRepository<span class="token punctuation">;</span>
    <span class="token comment">/**
     * 监听下单
     */</span>
    <span class="token annotation punctuation">@KafkaListener</span><span class="token punctuation">(</span>topics <span class="token operator">=</span> <span class="token punctuation">{</span><span class="token string">"placeOrder"</span><span class="token punctuation">}</span><span class="token punctuation">)</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">listen</span><span class="token punctuation">(</span>String orderEntityStr<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        log<span class="token punctuation">.</span><span class="token function">info</span><span class="token punctuation">(</span><span class="token string">"------------------ orderEntityStr ="</span> <span class="token operator">+</span> orderEntityStr<span class="token punctuation">)</span><span class="token punctuation">;</span>
        Gson gs <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Gson</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        OrderEntity orderEntity <span class="token operator">=</span> gs<span class="token punctuation">.</span><span class="token function">fromJson</span><span class="token punctuation">(</span>orderEntityStr<span class="token punctuation">,</span>OrderEntity<span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span><span class="token comment">//把JSON字符串转为对象</span>
        orderRepository<span class="token punctuation">.</span><span class="token function">save</span><span class="token punctuation">(</span>orderEntity<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<p>接收消息直接用<code>@KafkaListener</code>注解即可，并在监听中设置监听的<code>topic</code>，<code>topics</code>是一个数组所以是可以绑定多个主题的，如@KafkaListener(topics = {“topicA”,“topicB”})。这里的<code>topic</code>需要和消息发送类 KafkaSender.java中设置的<code>topic</code>一致。</p>
<p>spring.kafka.bootstrap-servers 后面设置你安装的<code>Kafka</code>的机器IP地址和端口号<code>9092</code>。</p>
<h1><a id="Kafka_144"></a>三、启动Kafka服务</h1>
<pre><code>bin/kafka-server-start.sh  config/server.properties
</code></pre>
<p><code>千万注意</code>： 记得将你的虚拟机或者服务器关闭防火墙或者开启<code>Kafka</code>的端口<code>9092</code>。</p>
<h1><a id="_149"></a>四、测试</h1>
<p>启动order-provider，调用下单接口,可以看到下单成功。<br>
我们来看下<code>Kafka</code>中的<code>topic</code>列表:</p>
<pre><code>bin/kafka-topics.sh --list --zookeeper localhost:2181
</code></pre>
<p>会看到：</p>
<pre><code>__consumer_offsets
placeOrder
</code></pre>
<p>接下来，我们来测试下kafka的消费能力。<br>
我们把 OrderService 改造一下：</p>
<pre><code class="prism language-java"><span class="token keyword">package</span> cn<span class="token punctuation">.</span>lilyssh<span class="token punctuation">.</span>order<span class="token punctuation">.</span>provider<span class="token punctuation">.</span>service<span class="token punctuation">;</span>

<span class="token keyword">import</span> cn<span class="token punctuation">.</span>lilyssh<span class="token punctuation">.</span>common<span class="token punctuation">.</span>kafka<span class="token punctuation">.</span>provider<span class="token punctuation">.</span>KafkaSender<span class="token punctuation">;</span>
<span class="token keyword">import</span> cn<span class="token punctuation">.</span>lilyssh<span class="token punctuation">.</span>order<span class="token punctuation">.</span>api<span class="token punctuation">.</span>model<span class="token punctuation">.</span>request<span class="token punctuation">.</span>OrderInsertReq<span class="token punctuation">;</span>
<span class="token keyword">import</span> cn<span class="token punctuation">.</span>lilyssh<span class="token punctuation">.</span>order<span class="token punctuation">.</span>api<span class="token punctuation">.</span>service<span class="token punctuation">.</span>OrderServiceApi<span class="token punctuation">;</span>
<span class="token keyword">import</span> cn<span class="token punctuation">.</span>lilyssh<span class="token punctuation">.</span>order<span class="token punctuation">.</span>provider<span class="token punctuation">.</span>dao<span class="token punctuation">.</span>entity<span class="token punctuation">.</span>OrderEntity<span class="token punctuation">;</span>
<span class="token keyword">import</span> com<span class="token punctuation">.</span>alibaba<span class="token punctuation">.</span>dubbo<span class="token punctuation">.</span>config<span class="token punctuation">.</span>annotation<span class="token punctuation">.</span>Service<span class="token punctuation">;</span>
<span class="token keyword">import</span> lombok<span class="token punctuation">.</span>AllArgsConstructor<span class="token punctuation">;</span>
<span class="token keyword">import</span> lombok<span class="token punctuation">.</span>extern<span class="token punctuation">.</span>slf4j<span class="token punctuation">.</span>Slf4j<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>beans<span class="token punctuation">.</span>BeanUtils<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>stereotype<span class="token punctuation">.</span>Component<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>util<span class="token punctuation">.</span>StringUtils<span class="token punctuation">;</span>

<span class="token keyword">import</span> java<span class="token punctuation">.</span>math<span class="token punctuation">.</span>BigDecimal<span class="token punctuation">;</span>
<span class="token keyword">import</span> java<span class="token punctuation">.</span>util<span class="token punctuation">.</span>*<span class="token punctuation">;</span>

<span class="token annotation punctuation">@Slf4j</span>
<span class="token annotation punctuation">@Service</span>
<span class="token annotation punctuation">@Component</span>
<span class="token annotation punctuation">@AllArgsConstructor</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">OrderService</span> <span class="token keyword">implements</span> <span class="token class-name">OrderServiceApi</span> <span class="token punctuation">{</span>

    <span class="token keyword">private</span> KafkaSender kafkaSender<span class="token punctuation">;</span>

    <span class="token comment">/**
     * 每两秒，新建五百个下单线程，一分钟后停止，查看kafka每秒是否能消费250条数据。
     */</span>
    <span class="token annotation punctuation">@Override</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">saveByKafka</span><span class="token punctuation">(</span>OrderInsertReq orderInsertReq<span class="token punctuation">)</span><span class="token punctuation">{</span>
        OrderEntity orderEntity<span class="token operator">=</span><span class="token keyword">new</span> <span class="token class-name">OrderEntity</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token comment">//直接写入数据库太慢，引起dubbo超时，导致调用多次，此处需要改造成kafka异步写入。</span>
        BeanUtils<span class="token punctuation">.</span><span class="token function">copyProperties</span><span class="token punctuation">(</span>orderInsertReq<span class="token punctuation">,</span>orderEntity<span class="token punctuation">)</span><span class="token punctuation">;</span>
        System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"预备备！开始！"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        Timer timer <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Timer</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
        timer<span class="token punctuation">.</span><span class="token function">schedule</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">MyTask</span><span class="token punctuation">(</span>timer<span class="token punctuation">)</span><span class="token punctuation">,</span> <span class="token number">0</span><span class="token punctuation">,</span> <span class="token number">2000</span><span class="token punctuation">)</span><span class="token punctuation">;</span>  <span class="token comment">//任务等待0秒后开始执行，之后每2秒执行一次</span>
    <span class="token punctuation">}</span>
    <span class="token comment">//任务：每次新建五百个下单线程。</span>
    <span class="token keyword">class</span> <span class="token class-name">MyTask</span> <span class="token keyword">extends</span> <span class="token class-name">TimerTask</span> <span class="token punctuation">{</span>
        <span class="token keyword">private</span> Timer timer<span class="token punctuation">;</span>
        <span class="token keyword">public</span> <span class="token function">MyTask</span><span class="token punctuation">(</span>Timer timer<span class="token punctuation">)</span> <span class="token punctuation">{</span>
            <span class="token keyword">this</span><span class="token punctuation">.</span>timer <span class="token operator">=</span> timer<span class="token punctuation">;</span>
        <span class="token punctuation">}</span>

        <span class="token keyword">int</span> second <span class="token operator">=</span> <span class="token number">0</span><span class="token punctuation">;</span>
        <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">run</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"~~~第"</span><span class="token operator">+</span>second<span class="token operator">+</span><span class="token string">"秒~~~"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token keyword">for</span> <span class="token punctuation">(</span><span class="token keyword">int</span> i <span class="token operator">=</span> <span class="token number">0</span><span class="token punctuation">;</span> i <span class="token operator">&lt;</span> <span class="token number">500</span><span class="token punctuation">;</span> i<span class="token operator">++</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
                AddOrder addOrder<span class="token operator">=</span><span class="token keyword">new</span> <span class="token class-name">AddOrder</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
                Thread thread<span class="token operator">=</span><span class="token keyword">new</span> <span class="token class-name">Thread</span><span class="token punctuation">(</span>addOrder<span class="token punctuation">)</span><span class="token punctuation">;</span>
                thread<span class="token punctuation">.</span><span class="token function">start</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>
            second<span class="token operator">++</span><span class="token punctuation">;</span>
            <span class="token keyword">if</span><span class="token punctuation">(</span> second <span class="token operator">==</span> <span class="token number">30</span><span class="token punctuation">)</span><span class="token punctuation">{</span>
                <span class="token keyword">this</span><span class="token punctuation">.</span>timer<span class="token punctuation">.</span><span class="token function">cancel</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
                System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span><span class="token string">"#### 程序结束 ####"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            <span class="token punctuation">}</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>
    <span class="token comment">//下单线程</span>
    <span class="token keyword">class</span> <span class="token class-name">AddOrder</span> <span class="token keyword">implements</span> <span class="token class-name">Runnable</span><span class="token punctuation">{</span>
        <span class="token annotation punctuation">@Override</span>
        <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">run</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
            OrderEntity orderEntity <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">OrderEntity</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            orderEntity<span class="token punctuation">.</span><span class="token function">setUserId</span><span class="token punctuation">(</span><span class="token number">753</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            orderEntity<span class="token punctuation">.</span><span class="token function">setPayment</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">BigDecimal</span><span class="token punctuation">(</span><span class="token number">928.23</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            orderEntity<span class="token punctuation">.</span><span class="token function">setCreateTime</span><span class="token punctuation">(</span><span class="token keyword">new</span> <span class="token class-name">Date</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
            kafkaSender<span class="token punctuation">.</span><span class="token function">send</span><span class="token punctuation">(</span><span class="token string">"placeOrder"</span><span class="token punctuation">,</span> orderEntity<span class="token punctuation">.</span><span class="token function">getUserId</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span> orderEntity<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span>
</code></pre>
<p>结果毫无压力！</p>
<p>大功告成！<br>
<a href="https://gitee.com/lilyssh/high-concurrency" rel="nofollow"><br>
<img src="https://gitee.com/lilyssh/high-concurrency/widgets/widget_2.svg?color=2431e0" alt="Fork me on Gitee"></a></p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>