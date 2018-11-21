---
layout:     post
title:      kafka consumer api连接kafka server
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/fututadeyoushang/article/details/80941614				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p></p><div class="toc">
<ul>
<li><a href="#%E8%83%8C%E6%99%AF" rel="nofollow">背景</a></li>
<li><a href="#kafka%E7%9A%84%E4%B8%BB%E8%A6%81%E5%8A%9F%E8%83%BD" rel="nofollow">kafka的主要功能</a></li>
<li><a href="#kafka%E6%A0%B8%E5%BF%83api" rel="nofollow">kafka核心API</a></li>
<li><a href="#%E5%9F%BA%E6%9C%AC%E6%9C%AF%E8%AF%AD" rel="nofollow">基本术语</a><ul>
<li><ul>
<li><ul>
<li><ul>
<li><a href="#topic" rel="nofollow">Topic</a></li>
<li><a href="#producer" rel="nofollow">Producer</a></li>
<li><a href="#consumer" rel="nofollow">Consumer</a></li>
<li><a href="#broker" rel="nofollow">Broker</a></li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
<li><a href="#%E7%89%88%E6%9C%AC" rel="nofollow">版本</a></li>
<li><a href="#%E4%BE%9D%E8%B5%96%E5%8C%85" rel="nofollow">依赖包</a></li>
<li><a href="#%E5%B8%B8%E7%94%A8%E5%91%BD%E4%BB%A4" rel="nofollow">常用命令</a></li>
<li><a href="#%E5%8F%82%E8%80%83%E6%96%87%E7%AB%A0" rel="nofollow">参考文章</a></li>
</ul>
</div>


<h1 id="背景">背景</h1>

<blockquote>
  <p>最近在公司老大要做一个kafka consumer接收kafka server的message，其实很简单的一个功能，用kafka都有些大材小用了，一般的JMS都可以了。</p>
  
  <p>之前没有用过kafka，所以去简单了解了一下。</p>
</blockquote>



<h1 id="kafka的主要功能">kafka的主要功能</h1>

<ol>
<li>发布和订阅消息（流），在这方面，它类似于一个消息队列或企业消息系统。</li>
<li>以<code>容错</code>的方式存储消息（流）。</li>
<li>在消息流发生时处理它们。</li>
</ol>



<h1 id="kafka核心api">kafka核心API</h1>

<ul>
<li>生产者Producer API</li>
<li>消费者Consumer API</li>
<li>流处理Streams API</li>
<li>Connector API</li>
</ul>

<!--more-->



<h1 id="基本术语">基本术语</h1>

<blockquote>
  <h5 id="topic">Topic</h5>
  
  <p>Kafka将消息种子(Feed)分门别类，每一类的消息称之为一个主题(Topic).</p>
  
  <h5 id="producer">Producer</h5>
  
  <p>发布消息的对象称之为主题生产者(Kafka topic producer)</p>
  
  <h5 id="consumer">Consumer</h5>
  
  <p>订阅消息并处理发布的消息的种子的对象称之为主题消费者(consumers)</p>
  
  <h5 id="broker">Broker</h5>
  
  <p>已发布的消息保存在一组服务器中，称之为Kafka集群。集群中的每一个服务器都是一个代理(Broker). 消费者可以订阅一个或多个主题（topic），并从Broker拉数据，从而消费这些已发布的消息。</p>
</blockquote>



<h1 id="版本">版本</h1>

<p>之前做的时候老大说用high level consumer API或者low level conxumer API来做，但是我在做的过程中发现对接的kafka（网管团队维护）版本是0.11的，在网上查阅了解到kafka0.9之后新增了一个Java消费者用于替换现在基于zookeeper（一个分布式的应用程序协调服务，如果有做过dubbo项目的同学应该很熟悉）的high or low level consumer API。</p>



<h1 id="依赖包">依赖包</h1>

<blockquote>
  <pre class="prettyprint"><code class="language-java hljs ">&lt;dependency&gt;
      &lt;groupId&gt;org.apache.kafka&lt;/groupId&gt;
      &lt;artifactId&gt;kafka-clients&lt;/artifactId&gt;
      &lt;version&gt;<span class="hljs-number">0.10</span><span class="hljs-number">.1</span><span class="hljs-number">.0</span>&lt;/version&gt;
&lt;/dependency&gt;</code></pre>
</blockquote>



<h1 id="常用命令">常用命令</h1>

<p>查看当前kafka所有的topic <br>
./kafka-topics.sh –list –zookeeper localhost:2181/nokia/nsp/kafka</p>

<p>producer发送message <br>
./kafka-console-producer.sh –broker-list localhost:9092 –topic ns-eg-2cea2382-2f25-4524-80ec-31949dd37c0c</p>

<p>consumer接受message <br>
./kafka-console-consumer.sh –bootstrap-server localhost:9092 –topic ns-eg-2cea2382-2f25-4524-80ec-31949dd37c0c –from-beginning</p>

<p>查看版本（kafka没有类似于java -version这样的命令） <br>
find ./libs/ -name *kafka_* | head -1 | grep -o ‘\kafka[^\n]*’</p>

<p>这个命令查找到类似于kafka_2.9.2-0.8.1.1.jar.asc 这样的jar包，然后这个jar包的名称前面的数据2.9.2是scala（kafka就是用scala开发的）的版本，后面的0.8.1.1就是所用的kafka的版本。</p>

<p># 使用</p>

<p>因为我只使用到了kafka consumer API，所以这里列出consumer的代码</p>



<pre class="prettyprint"><code class="language-java hljs ">Properties props = <span class="hljs-keyword">new</span> Properties();
     props.put(<span class="hljs-string">"bootstrap.servers"</span>, <span class="hljs-string">"localhost:9092"</span>);
     props.put(<span class="hljs-string">"group.id"</span>, <span class="hljs-string">"test"</span>);
     props.put(<span class="hljs-string">"enable.auto.commit"</span>, <span class="hljs-string">"true"</span>);
     props.put(<span class="hljs-string">"auto.commit.interval.ms"</span>, <span class="hljs-string">"1000"</span>);
     props.put(<span class="hljs-string">"key.deserializer"</span>, <span class="hljs-string">"org.apache.kafka.common.serialization.StringDeserializer"</span>);
     props.put(<span class="hljs-string">"value.deserializer"</span>, <span class="hljs-string">"org.apache.kafka.common.serialization.StringDeserializer"</span>);
     KafkaConsumer&lt;String, String&gt; consumer = <span class="hljs-keyword">new</span> KafkaConsumer&lt;&gt;(props);
     consumer.subscribe(Arrays.asList(<span class="hljs-string">"foo"</span>, <span class="hljs-string">"bar"</span>));
     <span class="hljs-keyword">while</span> (<span class="hljs-keyword">true</span>) {
         ConsumerRecords&lt;String, String&gt; records = consumer.poll(<span class="hljs-number">100</span>);
         <span class="hljs-keyword">for</span> (ConsumerRecord&lt;String, String&gt; record : records)
             System.out.printf(<span class="hljs-string">"offset = %d, key = %s, value = %s%n"</span>, record.offset(), record.key(), record.value());
     }</code></pre>

<p>以上是自动提交偏移量（offset）的consumer client，消费者TCP长连接到Broker拉取message。关于偏移量的概念很重要，可参考下面提供的参考文章有详细叙述。</p>



<h1 id="参考文章">参考文章</h1>

<p><a href="kafka%E5%85%A5%E9%97%A8%E4%BB%8B%E7%BB%8D" rel="nofollow">http://orchome.com/5</a>   kafka入门介绍</p>

<p><a href="kafka%E6%B6%88%E8%B4%B9%E8%80%85%E5%AE%A2%E6%88%B7%E7%AB%AF" rel="nofollow">http://orchome.com/451</a>   kafka消费者客户端</p>

<p><a href="kafka%E5%AE%98%E7%BD%91" rel="nofollow">http://kafka.apache.org/</a>     kafka官网</p>

<p><a href="%E6%9F%A5%E7%9C%8Bkafka%E7%89%88%E6%9C%AC" rel="nofollow">https://blog.csdn.net/Damonhaus/article/details/54310868</a> 查看kafka版本</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>