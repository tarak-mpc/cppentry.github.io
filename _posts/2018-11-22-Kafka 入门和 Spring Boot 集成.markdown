---
layout:     post
title:      Kafka 入门和 Spring Boot 集成
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="kafka-入门和-spring-boot-集成">Kafka 入门和 Spring Boot 集成</h1>



<h2 id="标签博客">标签：博客</h2>

<p></p><div class="toc"><div class="toc">
<ul>
<li><a href="#kafka-%E5%85%A5%E9%97%A8%E5%92%8C-spring-boot-%E9%9B%86%E6%88%90" rel="nofollow">Kafka 入门和 Spring Boot 集成</a><ul>
<li><a href="#%E6%A0%87%E7%AD%BE%E5%8D%9A%E5%AE%A2" rel="nofollow">标签：博客</a></li>
</ul>
</li>
<li><a href="#%E6%A6%82%E8%BF%B0" rel="nofollow">概述</a></li>
<li><a href="#%E5%BA%94%E7%94%A8%E5%9C%BA%E6%99%AF" rel="nofollow">应用场景</a></li>
<li><a href="#%E5%9F%BA%E6%9C%AC%E6%A6%82%E5%BF%B5" rel="nofollow">基本概念</a></li>
<li><a href="#%E5%9F%BA%E6%9C%AC%E7%BB%93%E6%9E%84" rel="nofollow">基本结构</a></li>
<li><a href="#%E5%92%8Cspring-boot-%E9%9B%86%E6%88%90" rel="nofollow">和Spring Boot 集成</a><ul>
<li><ul>
<li><a href="#%E9%9B%86%E6%88%90%E6%A6%82%E8%BF%B0" rel="nofollow">集成概述</a></li>
<li><a href="#%E9%9B%86%E6%88%90%E7%8E%AF%E5%A2%83" rel="nofollow">集成环境</a></li>
<li><a href="#kafka-%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA" rel="nofollow">kafka 环境搭建</a></li>
<li><a href="#spring-boot-%E5%92%8C-spring-for-apache-kafka-%E9%9B%86%E6%88%90%E6%AD%A5%E9%AA%A4" rel="nofollow">Spring Boot 和 Spring for Apache Kafka 集成步骤</a></li>
<li><a href="#%E6%9C%80%E5%90%8E" rel="nofollow">最后</a></li>
<li><a href="#%E8%B8%A9%E8%BF%87%E7%9A%84%E5%9D%91" rel="nofollow">踩过的坑</a></li>
</ul>
</li>
</ul>
</li>
</ul>
</div>
</div>




<h1 id="概述">概述</h1>

<p>kafka 是一个高性能的消息队列，也是一个分布式流处理平台（这里的流指的是数据流）。由java 和 Scala 语言编写，最早由 LinkedIn 开发，并 2011年开源，现在由 Apache 开发维护。</p>



<h1 id="应用场景">应用场景</h1>

<p>下面列举了一些kafka常见的应用场景。</p>

<p><strong>消息队列</strong> ： Kafka 可以作为消息队列使用，可用于系统内异步解耦，流量削峰等场景。</p>

<p><strong>应用监控</strong>：利用 Kafka 采集应用程序和服务器健康相关的指标，如应用程序相关的日志，服务器相关的 CPU、占用率、 IO、内存、连接数、 TPS、 QPS等，然后将指标信息进行处理，从而构建一个具有监控仪表盘、曲线图等可视化监控系统。 例如， 很多公司采用 Kafka 与 ELK（ElasticSearch、 Logstash 和Kibana）整合构建应用服务的监控系统。</p>

<p><strong>流处理</strong>：比如将 kafka 接收到的数据发送给 Storm 流式计算框架处理。</p>



<h1 id="基本概念">基本概念</h1>

<p><strong>record（消息）</strong>：kafka 通信的基本单位，每一条消息称为record</p>

<p><strong>producer （生产者 ）</strong>：发送消息的客户端。</p>

<p><strong>consumer（消费者 ）</strong>：消费消息的客户端。</p>

<p><strong>consumerGroup （消费者组）</strong>：每一个消费者都属于一个特定的消费者组。</p>

<p><strong>消费者和消费者组的关系</strong>：</p>

<ul>
<li>如果a，b，c 属于同一个消费者组，那一条消息只能被 a，b，c 中的某一个消费者消费。</li>
<li>如果a，b，c 属于不同的消费者组（比如 ga，gb，gc） ，那一条消息过来，a，b，c 三个消费者都能消费到。</li>
</ul>

<p><strong>topic （主题）</strong>： kafka的消息通过topic来分类，类似于数据库的表。 producer 发布消息到 topic，consumer订阅 topic 进行消费</p>

<p><strong>partition（ 分区）</strong>：一个topic会被分成一到多个分区（partition），然后多个分区可以分布在不同的机器上，这样一个主题就相当于运行在了多台机子上，kafka用分区的方式提高了性能和吞吐量</p>

<p><strong>replica （副本）</strong>：一个分区有一到多个副本，副本的作用是提高分区的 可用性。</p>

<p><strong>offset（偏移量）</strong>：偏移量 类似数据库自增int Id，随着数据的不断写入 kafka 分区内的偏移量会不断增加，一条消息由一个唯一的偏移量来标识。偏移量的作用是，让消费者知道自己消费到了哪个位置，下次可以接着从这里消费。如下图： <br>
<img src="http://static.zybuluo.com/anuo/fyldzb56nptvd975qifnf96f/%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_20180605170012.png" alt="微信截图_20180605170012.png-35.4kB" title=""> <br>
消费者A 消费到了 offset 为 9 的记录，消费者 B 消费到了offset 为 11 的记录。</p>



<h1 id="基本结构">基本结构</h1>

<p>kafka 最基本的结构如下，跟常见的消息队列结构一样。 <br>
<img src="http://static.zybuluo.com/anuo/ytnd0vl2vch9som1ekuoe7io/image.png" alt="image.png-27.8kB" title=""> <br>
消息通过生产者发送到 kafka 集群， 然后消费者从 kafka 集群拉取消息进行消费。</p>



<h1 id="和spring-boot-集成">和Spring Boot 集成</h1>



<h3 id="集成概述">集成概述</h3>

<p>本集成方式采用的是 spring boot 官方文档说的集成方式，<a href="https://docs.spring.io/spring-boot/docs/1.5.8.BUILD-SNAPSHOT/reference/html/boot-features-messaging.html#boot-features-kafka" rel="nofollow">官方链接</a>，集成的大体思路是，通过在 spring boot application.properties 中配置 生产者和消费者的基本信息，然后spring boot 启动后会创建 KafkaTemplate 对象，这个对象可以用来发送消息到Kafka，然后用 @KafkaListener 注解来消费 kafka 里面的消息，具体步骤如下。</p>



<h3 id="集成环境">集成环境</h3>

<p><code>spring boot</code>:1.5.13 版本 <br>
<code>spring-kafka</code>:1.3.5 版本 <br>
<code>kafka</code>:1.0.1 版本</p>



<h3 id="kafka-环境搭建">kafka 环境搭建</h3>

<p><strong>先启动Zookeeper</strong>:  </p>

<pre><code>docker run -d --name zookeeper --publish 2181:2181 --volume /etc/localtime:/etc/localtime zookeeper:latest 
</code></pre>

<p><strong>再启动Kafka</strong>:替换下面的IP为你服务器IP即可</p>

<pre><code>docker run -d --name kafka --publish 9092:9092 --link zookeeper --env KAFKA_ZOOKEEPER_CONNECT=zookeeper:2181 --env KAFKA_ADVERTISED_HOST_NAME=192.168.10.253 --env KAFKA_ADVERTISED_PORT=9092 --volume /etc/localtime:/etc/localtime wurstmeister/kafka:latest 
</code></pre>



<h3 id="spring-boot-和-spring-for-apache-kafka-集成步骤">Spring Boot 和 Spring for Apache Kafka 集成步骤</h3>

<ol>
<li>首先pom中引入 Spring for Apache Kafka</li>
</ol>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-comment">&lt;!-- kafka --&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.springframework.kafka<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spring-kafka<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.3.5.RELEASE<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
</code></pre>

<ol>
<li>然后 application.properties 配置文件中加入如下配置： <br>
各个配置的解释见：<a href="https://docs.spring.io/spring-boot/docs/1.5.8.BUILD-SNAPSHOT/reference/html/common-application-properties.html" rel="nofollow">spring boot 附录中的 kafka 配置</a>，搜索kafka 关键字即可定位。</li>
</ol>



<pre class="prettyprint"><code class=" hljs avrasm">server<span class="hljs-preprocessor">.port</span>=<span class="hljs-number">8090</span>

<span class="hljs-preprocessor">####### kafka</span>

<span class="hljs-preprocessor">### producer 配置</span>
spring<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.producer</span><span class="hljs-preprocessor">.bootstrap</span>-servers=<span class="hljs-number">192.168</span><span class="hljs-number">.10</span><span class="hljs-number">.48</span>:<span class="hljs-number">9092</span>
spring<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.producer</span><span class="hljs-preprocessor">.key</span>-serializer=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.common</span><span class="hljs-preprocessor">.serialization</span><span class="hljs-preprocessor">.StringSerializer</span>
spring<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.producer</span><span class="hljs-preprocessor">.value</span>-serializer=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.common</span><span class="hljs-preprocessor">.serialization</span><span class="hljs-preprocessor">.StringSerializer</span>

<span class="hljs-preprocessor">### consumer 配置</span>
spring<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.bootstrap</span>-servers=<span class="hljs-number">192.168</span><span class="hljs-number">.10</span><span class="hljs-number">.48</span>:<span class="hljs-number">9092</span>
spring<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.group</span>-id=anuoapp
spring<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.enable</span>-auto-commit=true
spring<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.auto</span>-commit-interval=<span class="hljs-number">100</span>
spring<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.max</span>-poll-records=<span class="hljs-number">1</span>
spring<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.key</span>-deserializer=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.common</span><span class="hljs-preprocessor">.serialization</span><span class="hljs-preprocessor">.StringDeserializer</span>
spring<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.value</span>-deserializer=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.common</span><span class="hljs-preprocessor">.serialization</span><span class="hljs-preprocessor">.StringDeserializer</span>
spring<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.listener</span><span class="hljs-preprocessor">.concurrency</span>=<span class="hljs-number">5</span></code></pre>

<ol>
<li>创建 Kafka Producer 生产者</li>
</ol>



<pre class="prettyprint"><code class=" hljs avrasm">package <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.example</span><span class="hljs-preprocessor">.anuoapp</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-comment">;</span>

import <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.alibaba</span><span class="hljs-preprocessor">.fastjson</span><span class="hljs-preprocessor">.JSON</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.springframework</span><span class="hljs-preprocessor">.beans</span><span class="hljs-preprocessor">.factory</span><span class="hljs-preprocessor">.annotation</span><span class="hljs-preprocessor">.Autowired</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.springframework</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.core</span><span class="hljs-preprocessor">.KafkaTemplate</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.springframework</span><span class="hljs-preprocessor">.stereotype</span><span class="hljs-preprocessor">.Component</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.springframework</span><span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.concurrent</span><span class="hljs-preprocessor">.ListenableFuture</span><span class="hljs-comment">;</span>

@Component
public class KafkaProducer {
    @Autowired
    KafkaTemplate kafkaTemplate<span class="hljs-comment">;</span>

    public void kafkaSend() throws Exception {
        UserAccount userAccount=new UserAccount()<span class="hljs-comment">;</span>
        userAccount<span class="hljs-preprocessor">.setCard</span>_name(<span class="hljs-string">"jk"</span>)<span class="hljs-comment">;</span>
        userAccount<span class="hljs-preprocessor">.setAddress</span>(<span class="hljs-string">"cd"</span>)<span class="hljs-comment">;</span>
        ListenableFuture send = kafkaTemplate<span class="hljs-preprocessor">.send</span>(<span class="hljs-string">"jktopic"</span>, <span class="hljs-string">"key"</span>, JSON<span class="hljs-preprocessor">.toJSONString</span>(userAccount))<span class="hljs-comment">;</span>



    }
}
</code></pre>

<ol>
<li>创建 Kafka Consumer 消费者</li>
</ol>



<pre class="prettyprint"><code class=" hljs avrasm">package <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.example</span><span class="hljs-preprocessor">.anuoapp</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-comment">;</span>

import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.clients</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.ConsumerRecord</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.slf</span>4j<span class="hljs-preprocessor">.Logger</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.slf</span>4j<span class="hljs-preprocessor">.LoggerFactory</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.springframework</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.annotation</span><span class="hljs-preprocessor">.KafkaListener</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.springframework</span><span class="hljs-preprocessor">.stereotype</span><span class="hljs-preprocessor">.Component</span><span class="hljs-comment">;</span>

@Component
public class KafkaConsumer {

    public static final Logger logger = LoggerFactory<span class="hljs-preprocessor">.getLogger</span>(KafkaConsumer<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;</span>


    @KafkaListener(topics = {<span class="hljs-string">"jktopic"</span>})
    public void jktopic(ConsumerRecord consumerRecord) throws InterruptedException {

        System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(consumerRecord<span class="hljs-preprocessor">.offset</span>())<span class="hljs-comment">;</span>
        System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(consumerRecord<span class="hljs-preprocessor">.value</span>()<span class="hljs-preprocessor">.toString</span>())<span class="hljs-comment">;</span>
        Thread<span class="hljs-preprocessor">.sleep</span>(<span class="hljs-number">3000</span>)<span class="hljs-comment">;</span>


    }


}</code></pre>

<ol>
<li>创建一个rest api 来调用 Kafka 的消息生产者 </li>
</ol>



<pre class="prettyprint"><code class=" hljs avrasm">package <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.example</span><span class="hljs-preprocessor">.anuoapp</span><span class="hljs-preprocessor">.controller</span><span class="hljs-comment">;</span>

import <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.example</span><span class="hljs-preprocessor">.anuoapp</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.KafkaProducer</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.slf</span>4j<span class="hljs-preprocessor">.Logger</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.slf</span>4j<span class="hljs-preprocessor">.LoggerFactory</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.springframework</span><span class="hljs-preprocessor">.beans</span><span class="hljs-preprocessor">.factory</span><span class="hljs-preprocessor">.annotation</span><span class="hljs-preprocessor">.Autowired</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.springframework</span><span class="hljs-preprocessor">.web</span><span class="hljs-preprocessor">.bind</span><span class="hljs-preprocessor">.annotation</span><span class="hljs-preprocessor">.RequestMapping</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.springframework</span><span class="hljs-preprocessor">.web</span><span class="hljs-preprocessor">.bind</span><span class="hljs-preprocessor">.annotation</span><span class="hljs-preprocessor">.RequestMethod</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.springframework</span><span class="hljs-preprocessor">.web</span><span class="hljs-preprocessor">.bind</span><span class="hljs-preprocessor">.annotation</span><span class="hljs-preprocessor">.RestController</span><span class="hljs-comment">;</span>


@RestController
@RequestMapping(<span class="hljs-string">"/api/system"</span>)
public class SystemController {
    private Logger logger = LoggerFactory<span class="hljs-preprocessor">.getLogger</span>(SystemController<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;</span>

    @Autowired
    KafkaProducer kafkaProducer<span class="hljs-comment">;</span>

    @RequestMapping(value = <span class="hljs-string">"/Kafka/send"</span>, method = RequestMethod<span class="hljs-preprocessor">.GET</span>)
    public void WarnInfo() throws Exception {
        int count=<span class="hljs-number">10</span><span class="hljs-comment">;</span>
        for (int i = <span class="hljs-number">0</span><span class="hljs-comment">; i &lt; count; i++) {</span>
            kafkaProducer<span class="hljs-preprocessor">.kafkaSend</span>()<span class="hljs-comment">;</span>
        }

    }



}
</code></pre>

<ol>
<li>用 post man 调用 第 5 步创建的接口， 就可以看到 如下消费者产生的输出信息</li>
</ol>



<pre class="prettyprint"><code class=" hljs json"><span class="hljs-number">30</span>
{"<span class="hljs-attribute">address</span>":<span class="hljs-value"><span class="hljs-string">"cd"</span></span>,"<span class="hljs-attribute">bind_qq</span>":<span class="hljs-value"><span class="hljs-literal">false</span></span>,"<span class="hljs-attribute">bind_weixin</span>":<span class="hljs-value"><span class="hljs-literal">false</span></span>,"<span class="hljs-attribute">card_name</span>":<span class="hljs-value"><span class="hljs-string">"jk"</span></span>,"<span class="hljs-attribute">passwordDirectCompare</span>":<span class="hljs-value"><span class="hljs-literal">false</span></span>}
<span class="hljs-number">31</span>
{"<span class="hljs-attribute">address</span>":<span class="hljs-value"><span class="hljs-string">"cd"</span></span>,"<span class="hljs-attribute">bind_qq</span>":<span class="hljs-value"><span class="hljs-literal">false</span></span>,"<span class="hljs-attribute">bind_weixin</span>":<span class="hljs-value"><span class="hljs-literal">false</span></span>,"<span class="hljs-attribute">card_name</span>":<span class="hljs-value"><span class="hljs-string">"jk"</span></span>,"<span class="hljs-attribute">passwordDirectCompare</span>":<span class="hljs-value"><span class="hljs-literal">false</span></span>}
<span class="hljs-number">32</span>
{"<span class="hljs-attribute">address</span>":<span class="hljs-value"><span class="hljs-string">"cd"</span></span>,"<span class="hljs-attribute">bind_qq</span>":<span class="hljs-value"><span class="hljs-literal">false</span></span>,"<span class="hljs-attribute">bind_weixin</span>":<span class="hljs-value"><span class="hljs-literal">false</span></span>,"<span class="hljs-attribute">card_name</span>":<span class="hljs-value"><span class="hljs-string">"jk"</span></span>,"<span class="hljs-attribute">passwordDirectCompare</span>":<span class="hljs-value"><span class="hljs-literal">false</span></span>}</code></pre>



<h3 id="最后">最后</h3>

<p><strong>恭喜你 ! spring boot kafka 集成完毕。</strong> <br>
<strong>完整的基础源码见：</strong> <br>
链接: <a href="https://pan.baidu.com/s/1E2Lmbj9A9uruTXG54uPl_g" rel="nofollow">https://pan.baidu.com/s/1E2Lmbj9A9uruTXG54uPl_g</a> 密码: e6d6</p>



<h3 id="踩过的坑">踩过的坑</h3>

<ol>
<li><p>集成 spring kafka的时候注意版本要选对 ，目前spring boot 1.5.1 对应集成 spring kafka 1.x 版本  , 如果直接集成 spring kafka 2.x 版本 的话会报错, 要用 spring kafka 2.x 的话, 需要升级spring boot 到 2.x 版本。</p></li>
<li><p>消费者配置的时候 spring.kafka.consumer.max-poll-records=1 ，轮询拉取的最大记录数要设置成1，不然会出现重复消费，即：如果消费者程序崩了，再启动起来，会消费到以前消费过的数据，造成重复消费。 设置成 1 就没得这个问题了， 不知道是不是 spring kafka 这个lib封装得有问题。</p></li>
</ol>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>