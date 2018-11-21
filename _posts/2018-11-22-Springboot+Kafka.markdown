---
layout:     post
title:      Springboot+Kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2><a id="1_Overview_0"></a>1. Overview</h2>
<ul>
<li><a href="http://kafka.apache.org/documentation/" rel="nofollow">Kafka Documentation</a></li>
<li><a href="https://docs.spring.io/spring-kafka/reference/html/" rel="nofollow">Spring for Apache Kafka</a></li>
</ul>
<h2><a id="2_Kafka__5"></a>2. Kafka 搭建</h2>
<ul>
<li><a href="https://blog.csdn.net/MyHerux/article/details/84108223" rel="nofollow">Mac 安装 Kafka</a></li>
</ul>
<h2><a id="3__9"></a>3. 初试</h2>
<ul>
<li>
<p>依赖</p>
<pre><code>&lt;dependency&gt;
    &lt;groupId&gt;org.springframework.kafka&lt;/groupId&gt;
    &lt;artifactId&gt;spring-kafka&lt;/artifactId&gt;
    &lt;version&gt;2.1.7.RELEASE&lt;/version&gt;
&lt;/dependency&gt;
</code></pre>
</li>
<li>
<p>配置</p>
<pre><code>spring:
    kafka:
        bootstrap-servers: localhost:9092
        consumer:
            group-id: myGroup
</code></pre>
</li>
<li>
<p>消费</p>
<p><code>topic</code> 为测试时建立的 <code>topic</code></p>
<pre><code>@Component
public class KafkaConsumer {

    @KafkaListener(topics = "test")
    public void consume(String content){
        System.out.println(content);
    }
}
</code></pre>
</li>
<li>
<p>启动</p>
<p>启动项目，并且在前面自己安装的 <code>Kafka Producer</code> 产生消息，可以看到启动的项目里面收到消息：</p>
<p><img src="http://cdn.heroxu.com/20181115154227642642510.png" alt=""></p>
</li>
</ul>
<h2><a id="4_Kafka_53"></a>4. Kafka架构</h2>
<h3><a id="41__55"></a>4.1. 术语</h3>
<ul>
<li>
<p>Broker</p>
<p><code>Kafka</code> 集群包含一个或多个服务器，这种服务器被称为 <code>broker</code></p>
</li>
<li>
<p>Topic</p>
<p>每条发布到 <code>Kafka</code> 集群的消息都有一个类别，这个类别被称为 <code>Topic</code> 。（物理上不同 <code>Topic</code> 的消息分开存储，逻辑上一个 <code>Topic</code> 的消息虽然保存于一个或多个 <code>broker</code> 上但用户只需指定消息的 <code>Topic</code> 即可生产或消费数据而不必关心数据存于何处）</p>
</li>
<li>
<p>Partition</p>
<p><code>Parition</code> 是物理上的概念，每个 <code>Topic</code> 包含一个或多个 <code>Partition</code>.</p>
</li>
<li>
<p>Producer</p>
<p>负责发布消息到 <code>Kafka broker</code></p>
</li>
<li>
<p>Consumer</p>
<p>消息消费者，向 <code>Kafka broker</code> 读取消息的客户端。</p>
</li>
<li>
<p>Consumer Group</p>
<p>每个 <code>Consumer</code> 属于一个特定的 <code>Consumer Group</code>（可为每个 <code>Consumer</code> 指定 <code>groupname</code> ，若不指定 <code>group name</code> 则属于默认的 <code>group</code>）。</p>
</li>
</ul>
<h2><a id="5__82"></a>5. 更多用法</h2>
<ul>
<li>
<p>多消费者组消费同一条消息</p>
<p>根据 <code>Kafka</code> 的设计原理可知，如果两个不同的 <code>consumer</code> 分别处于两个不同的 <code>consumer group</code> ，那么它们就可以同时消费同一条消息：</p>
<pre><code>@KafkaListener(topics = "test",groupId = "myGroup")
public void consume(String content){
    System.out.println("myGroup message: "+content);
}

@KafkaListener(topics = "test",groupId = "myGroup2")
public void consume2(String content){
    System.out.println("myGroup2 message: "+content);
}
</code></pre>
</li>
<li>
<p>批量消费消息</p>
<p>配置：</p>
<pre><code>@Bean("batchContainerFactory")
public KafkaListenerContainerFactory&lt;?&gt; batchContainerFactory() {
    ConcurrentKafkaListenerContainerFactory&lt;Object, Object&gt; containerFactory = new ConcurrentKafkaListenerContainerFactory&lt;&gt;();
    containerFactory.setConsumerFactory(consumerFactory());
    containerFactory.setConcurrency(4);
    containerFactory.setBatchListener(true); //批量消费

    return containerFactory;
}
</code></pre>
<p>消费：</p>
<pre><code>@KafkaListener(topics = "test", groupId = "myGroup3", containerFactory = "batchContainerFactory")
public void consume3(List&lt;String&gt; content) {
    System.out.println("myGroup3 list-&gt;string message: " + content.stream().reduce((a, b) -&gt; a + b).get());
}
</code></pre>
</li>
</ul>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>