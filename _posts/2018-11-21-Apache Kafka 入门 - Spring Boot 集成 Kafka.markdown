---
layout:     post
title:      Apache Kafka 入门 - Spring Boot 集成 Kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：版权归博主所有，转载请带上本文链接！联系方式：abel533@gmail.com					https://blog.csdn.net/isea533/article/details/73864435				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-light">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="apache-kafka-入门">Apache Kafka 入门</h1>

<ul>
<li>Kafka的基本配置和运行</li>
<li>Kafka命令详细介绍</li>
<li>Kafka-manager的基本配置和运行</li>
<li>Kafka API 简单用法</li>
<li>Spring Boot 集成Kafka</li>
</ul>

<p>本篇为第五篇。</p>

<blockquote>
  <p>第四篇和第五篇源码下载： <br>
  链接：<a href="http://pan.baidu.com/s/1dE4vpBj" rel="nofollow">http://pan.baidu.com/s/1dE4vpBj</a> 密码：j74j</p>
</blockquote>

<h2 id="spring-boot-集成-kafka">Spring Boot 集成 Kafka</h2>

<blockquote>
  <p>参考文档</p>
  
  <p><a href="https://kafka.apache.org" rel="nofollow">https://kafka.apache.org</a></p>
  
  <p><a href="https://projects.spring.io/spring-kafka/" rel="nofollow">https://projects.spring.io/spring-kafka/</a></p>
  
  <p><a href="http://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-messaging.html#boot-features-kafka" rel="nofollow">http://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-messaging.html#boot-features-kafka</a></p>
  
  <p><a href="http://docs.spring.io/spring-kafka/docs/1.2.2.RELEASE/reference/htmlsingle/" rel="nofollow">http://docs.spring.io/spring-kafka/docs/1.2.2.RELEASE/reference/htmlsingle/</a></p>
</blockquote>

<p>Spring Boot 集成 Kafka 相当容易，只需要在基本的依赖中添加下面的依赖。</p>



<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.springframework.kafka<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spring-kafka<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.2.2.RELEASE<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span></code></pre>

<p>然后在 application.properties 中增加相应的配置即可。</p>



<pre class="prettyprint"><code class="language-properties hljs avrasm"><span class="hljs-preprocessor"># kafka</span>
spring<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.bootstrap</span>-servers=<span class="hljs-number">192.168</span><span class="hljs-number">.16</span><span class="hljs-number">.150</span>:<span class="hljs-number">9092</span>
spring<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.group</span>-id=springboot-group1
spring<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.auto</span>-offset-reset=earliest</code></pre>

<p>更多可以配置的属性参考 <a href="https://github.com/spring-projects/spring-boot/tree/v1.5.4.RELEASE/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/kafka/KafkaProperties.java" rel="nofollow"><code>KafkaProperties</code></a> 。</p>

<p>然后在 Spring Boot 中就可以使用 <code>KafkaTemplate</code> 发送消息，使用 <code>@KafkaListener</code> 消费指定主题的消息。简单演示代码如下：</p>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">package</span> com.github.abel533.boot;

<span class="hljs-keyword">import</span> org.apache.kafka.clients.consumer.ConsumerRecord;
<span class="hljs-keyword">import</span> org.slf4j.Logger;
<span class="hljs-keyword">import</span> org.slf4j.LoggerFactory;
<span class="hljs-keyword">import</span> org.springframework.beans.factory.annotation.Autowired;
<span class="hljs-keyword">import</span> org.springframework.boot.SpringApplication;
<span class="hljs-keyword">import</span> org.springframework.boot.autoconfigure.EnableAutoConfiguration;
<span class="hljs-keyword">import</span> org.springframework.kafka.annotation.KafkaListener;
<span class="hljs-keyword">import</span> org.springframework.kafka.core.KafkaTemplate;
<span class="hljs-keyword">import</span> org.springframework.stereotype.Controller;
<span class="hljs-keyword">import</span> org.springframework.web.bind.annotation.RequestMapping;
<span class="hljs-keyword">import</span> org.springframework.web.bind.annotation.ResponseBody;

<span class="hljs-javadoc">/**
 *<span class="hljs-javadoctag"> @author</span> liuzh
 *<span class="hljs-javadoctag"> @since</span> 2017/6/17.
 */</span>
<span class="hljs-annotation">@Controller</span>
<span class="hljs-annotation">@EnableAutoConfiguration</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">SampleController</span> {</span>

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> Logger logger = LoggerFactory.getLogger(SampleController.class);

    <span class="hljs-annotation">@Autowired</span>
    <span class="hljs-keyword">private</span> KafkaTemplate&lt;String, String&gt; template;

    <span class="hljs-annotation">@RequestMapping</span>(<span class="hljs-string">"/send"</span>)
    <span class="hljs-annotation">@ResponseBody</span>
    String send(String topic, String key, String data) {
        template.send(topic, key, data);
        <span class="hljs-keyword">return</span> <span class="hljs-string">"success"</span>;
    }

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> Exception {
        SpringApplication.run(SampleController.class, args);
    }

    <span class="hljs-annotation">@KafkaListener</span>(id = <span class="hljs-string">"t1"</span>, topics = <span class="hljs-string">"t1"</span>)
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">listenT1</span>(ConsumerRecord&lt;?, ?&gt; cr) <span class="hljs-keyword">throws</span> Exception {
        logger.info(<span class="hljs-string">"{} - {} : {}"</span>, cr.topic(), cr.key(), cr.value());
    }

    <span class="hljs-annotation">@KafkaListener</span>(id = <span class="hljs-string">"t2"</span>, topics = <span class="hljs-string">"t2"</span>)
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">listenT2</span>(ConsumerRecord&lt;?, ?&gt; cr) <span class="hljs-keyword">throws</span> Exception {
        logger.info(<span class="hljs-string">"{} - {} : {}"</span>, cr.topic(), cr.key(), cr.value());
    }

}</code></pre>

<p>通过<code>http://localhost:8080/send?topic=t2&amp;key=test1&amp;data=hello122</code>方法可以产生指定主题的消息。或者可以通过前面使用命令行方式或者API方式创建消息。</p>

<p>上面代码输出的日志（和生产者有关）如下：</p>



<pre class="prettyprint"><code class=" hljs css"><span class="hljs-attr_selector">[t1-0-C-1]</span> <span class="hljs-tag">t1</span> <span class="hljs-tag">-</span> 0 : 0
<span class="hljs-attr_selector">[t2-0-C-1]</span> <span class="hljs-tag">t2</span> <span class="hljs-tag">-</span> 0 : 0
<span class="hljs-attr_selector">[t2-0-C-1]</span> <span class="hljs-tag">t2</span> <span class="hljs-tag">-</span> 1 : 1
<span class="hljs-attr_selector">[t1-0-C-1]</span> <span class="hljs-tag">t1</span> <span class="hljs-tag">-</span> 1 : 1
<span class="hljs-attr_selector">[t1-0-C-1]</span> <span class="hljs-tag">t1</span> <span class="hljs-tag">-</span> 2 : 2
<span class="hljs-attr_selector">[t2-0-C-1]</span> <span class="hljs-tag">t2</span> <span class="hljs-tag">-</span> 2 : 2
<span class="hljs-attr_selector">[t1-0-C-1]</span> <span class="hljs-tag">t1</span> <span class="hljs-tag">-</span> 3 : 3
<span class="hljs-attr_selector">[t2-0-C-1]</span> <span class="hljs-tag">t2</span> <span class="hljs-tag">-</span> 3 : 3
<span class="hljs-attr_selector">[t1-0-C-1]</span> <span class="hljs-tag">t1</span> <span class="hljs-tag">-</span> 4 : 4
<span class="hljs-attr_selector">[t2-0-C-1]</span> <span class="hljs-tag">t2</span> <span class="hljs-tag">-</span> 4 : 4
<span class="hljs-attr_selector">[t2-0-C-1]</span> <span class="hljs-tag">t2</span> <span class="hljs-tag">-</span> 5 : 5
<span class="hljs-attr_selector">[t1-0-C-1]</span> <span class="hljs-tag">t1</span> <span class="hljs-tag">-</span> 5 : 5
<span class="hljs-attr_selector">[t1-0-C-1]</span> <span class="hljs-tag">t1</span> <span class="hljs-tag">-</span> 6 : 6
<span class="hljs-attr_selector">[t2-0-C-1]</span> <span class="hljs-tag">t2</span> <span class="hljs-tag">-</span> 6 : 6
<span class="hljs-attr_selector">[t2-0-C-1]</span> <span class="hljs-tag">t2</span> <span class="hljs-tag">-</span> 7 : 7
<span class="hljs-attr_selector">[t1-0-C-1]</span> <span class="hljs-tag">t1</span> <span class="hljs-tag">-</span> 7 : 7
<span class="hljs-attr_selector">[t1-0-C-1]</span> <span class="hljs-tag">t1</span> <span class="hljs-tag">-</span> 8 : 8
<span class="hljs-attr_selector">[t2-0-C-1]</span> <span class="hljs-tag">t2</span> <span class="hljs-tag">-</span> 8 : 8
<span class="hljs-attr_selector">[t2-0-C-1]</span> <span class="hljs-tag">t2</span> <span class="hljs-tag">-</span> 9 : 9
<span class="hljs-attr_selector">[t1-0-C-1]</span> <span class="hljs-tag">t1</span> <span class="hljs-tag">-</span> 9 : 9</code></pre>

<p>上面只是在Spring Boot中最基本的用法，想要定制更多的功能可以参考 <a href="http://docs.spring.io/spring-kafka/docs/1.2.2.RELEASE/reference/htmlsingle/" rel="nofollow">Spring for Apache Kafka</a> 的完整文档。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>