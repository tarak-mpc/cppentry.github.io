---
layout:     post
title:      第 12 讲 SpringBoot集成Kafka消息中间件
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/kye055947/article/details/83692450				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-light">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1><a id="_SpringBootKafka_0"></a>第十二讲 SpringBoot集成Kafka消息中间件</h1>
<p></p><div class="toc"><h3>文章目录</h3><ul><li><a href="#_SpringBootKafka_0" rel="nofollow">第十二讲 SpringBoot集成Kafka消息中间件</a></li><ul><li><a href="#1_kafka_3" rel="nofollow">1. kafka简介</a></li><li><a href="#2_linuxkafka_7" rel="nofollow">2. linux下启动kafka</a></li><li><a href="#3_SpringBootkafka_47" rel="nofollow">3. SpringBoot集成kafka</a></li><ul><li><a href="#31__pomxml_49" rel="nofollow">3.1 引入依赖： pom.xml</a></li><li><a href="#32_kafkaapplicationyml_59" rel="nofollow">3.2 配置kafka：application.yml</a></li><li><a href="#33_KafkaProducerController_76" rel="nofollow">3.3 编写消息生成类：KafkaProducerController</a></li><li><a href="#34_KafkaConsumerController_108" rel="nofollow">3.4 编写消息消费类：KafkaConsumerController</a></li><li><a href="#35__134" rel="nofollow">3.5 测试：</a></li></ul></ul></ul></div><p></p>
<h2><a id="1_kafka_3"></a>1. kafka简介</h2>
<blockquote>
<p>Kafka是由Apache软件基金会开发的一个开源流处理平台，由Scala和Java编写。Kafka是一种高吞吐量的分布式发布订阅消息系统，它可以处理消费者规模的网站中的所有动作流数据。 这种动作（网页浏览，搜索和其他用户的行动）是在现代网络上的许多社会功能的一个关键因素。 这些数据通常是由于吞吐量的要求而通过处理日志和日志聚合来解决。 对于像Hadoop的一样的日志数据和离线分析系统，但又要求实时处理的限制，这是一个可行的解决方案。Kafka的目的是通过Hadoop的并行加载机制来统一线上和离线的消息处理，也是为了通过集群来提供实时的消息。</p>
</blockquote>
<h2><a id="2_linuxkafka_7"></a>2. linux下启动kafka</h2>
<ul>
<li>2.1 百度网盘下载</li>
</ul>
<p>链接：<a href="https://pan.baidu.com/s/1wP3Ki3PogmAQCnSX-cK1kA" rel="nofollow">https://pan.baidu.com/s/1wP3Ki3PogmAQCnSX-cK1kA</a><br>
提取码：tuyy</p>
<ul>
<li>
<p>2.2 <a href="https://blog.csdn.net/xuzhelin/article/details/71515208" rel="nofollow">Linux下Kafka环境的搭建</a></p>
</li>
<li>
<p>2.3 启动zookeeper</p>
<ul>
<li>进入Zookeeper解压包下：/home/tools/zookeeper-3.4.12/bin，运行以下命令，启动zookeeper。</li>
<li>
</ul>
</li>
</ul>
<pre><code>./zkServer.sh start  //启动zookeeper
</code></pre>
<p><img src="picture/2018-11-03-14-21-39.png" alt="a"></p>
<ul>
<li>2.4 启动kafka</li>
</ul>
<pre><code>./bin/kafka-server-start.bat ./config/server.properties
</code></pre>
<p><img src="picture/2018-11-03-14-16-11.png" alt="a"></p>
<pre><code>创建TOPIC
./kafka-topics.sh --create --zookeeper 192.168.0.111:2181 --replication -factor 1 --partitions 1 --topic mytopic 

创建生产者：
./kafka-console-producer.sh --broker-list 192.168.0.111:9092 --topic mytopic

创建生产者：
./kafka-console-consumer.bat --zookeeper 192.168.0.111:2181 --topic mytopic 

查看topic列表：
./kafka-topics.bat --list --zookeeper 192.168.0.111:2181
</code></pre>
<h2><a id="3_SpringBootkafka_47"></a>3. SpringBoot集成kafka</h2>
<h3><a id="31__pomxml_49"></a>3.1 引入依赖： pom.xml</h3>
<pre><code class="prism language-xml"><span class="token comment">&lt;!--SpringBoot集成kafka--&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>dependency</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>groupId</span><span class="token punctuation">&gt;</span></span>org.springframework.kafka<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>groupId</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>artifactId</span><span class="token punctuation">&gt;</span></span>spring-kafka<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>artifactId</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>dependency</span><span class="token punctuation">&gt;</span></span>
</code></pre>
<h3><a id="32_kafkaapplicationyml_59"></a>3.2 配置kafka：application.yml</h3>
<pre><code class="prism language-yml"><span class="token key atrule">spring</span><span class="token punctuation">:</span>
  <span class="token key atrule">kafka</span><span class="token punctuation">:</span>
    <span class="token key atrule">bootstrap-servers</span><span class="token punctuation">:</span> 192.168.0.111<span class="token punctuation">:</span><span class="token number">9092</span>
    <span class="token key atrule">producer</span><span class="token punctuation">:</span>
      <span class="token key atrule">key-serializer</span><span class="token punctuation">:</span> org.apache.kafka.common.serialization.StringSerializer
      <span class="token key atrule">value-serializer</span><span class="token punctuation">:</span> org.apache.kafka.common.serialization.StringSerializer
    <span class="token key atrule">consumer</span><span class="token punctuation">:</span>
      <span class="token key atrule">group-id</span><span class="token punctuation">:</span> test
      <span class="token key atrule">enable-auto-commit</span><span class="token punctuation">:</span> <span class="token boolean important">true</span>
      <span class="token key atrule">auto-commit-interval</span><span class="token punctuation">:</span> <span class="token number">1000</span>
      <span class="token key atrule">key-deserializer</span><span class="token punctuation">:</span> org.apache.kafka.common.serialization.StringDeserializer
      <span class="token key atrule">value-deserializer</span><span class="token punctuation">:</span> org.apache.kafka.common.serialization.StringDeserializer
</code></pre>
<h3><a id="33_KafkaProducerController_76"></a>3.3 编写消息生成类：KafkaProducerController</h3>
<pre><code class="prism language-java"><span class="token keyword">package</span> com<span class="token punctuation">.</span>springboot<span class="token punctuation">.</span>kafka<span class="token punctuation">.</span>controller<span class="token punctuation">;</span>

<span class="token keyword">import</span> org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>beans<span class="token punctuation">.</span>factory<span class="token punctuation">.</span>annotation<span class="token punctuation">.</span>Autowired<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>kafka<span class="token punctuation">.</span>core<span class="token punctuation">.</span>KafkaTemplate<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation<span class="token punctuation">.</span>RequestMapping<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>web<span class="token punctuation">.</span>bind<span class="token punctuation">.</span>annotation<span class="token punctuation">.</span>RestController<span class="token punctuation">;</span>

<span class="token comment">/**
 * @Description:
 * @Author: zrblog
 * @CreateTime: 2018-09-27 07:52
 * @Version:v1.0
 */</span>
<span class="token annotation punctuation">@RestController</span>
<span class="token annotation punctuation">@RequestMapping</span><span class="token punctuation">(</span><span class="token string">"/kafka"</span><span class="token punctuation">)</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">KafkaProducerController</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@Autowired</span>
    <span class="token keyword">private</span> KafkaTemplate<span class="token generics function"><span class="token punctuation">&lt;</span>String<span class="token punctuation">,</span>String<span class="token punctuation">&gt;</span></span> kafkaTemplate<span class="token punctuation">;</span>

    <span class="token annotation punctuation">@RequestMapping</span><span class="token punctuation">(</span><span class="token string">"/send"</span><span class="token punctuation">)</span>
    <span class="token keyword">public</span> String <span class="token function">send</span><span class="token punctuation">(</span>String string<span class="token punctuation">)</span> <span class="token punctuation">{</span>
        kafkaTemplate<span class="token punctuation">.</span><span class="token function">send</span><span class="token punctuation">(</span><span class="token string">"mytopic"</span><span class="token punctuation">,</span> string<span class="token punctuation">)</span><span class="token punctuation">;</span>
        <span class="token keyword">return</span> <span class="token string">"KafkaProducerController：Kafka发送消息成功！"</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>

</code></pre>
<h3><a id="34_KafkaConsumerController_108"></a>3.4 编写消息消费类：KafkaConsumerController</h3>
<pre><code class="prism language-java"><span class="token keyword">package</span> com<span class="token punctuation">.</span>springboot<span class="token punctuation">.</span>kafka<span class="token punctuation">.</span>controller<span class="token punctuation">;</span>

<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>kafka<span class="token punctuation">.</span>clients<span class="token punctuation">.</span>consumer<span class="token punctuation">.</span>ConsumerRecord<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>kafka<span class="token punctuation">.</span>annotation<span class="token punctuation">.</span>KafkaListener<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>springframework<span class="token punctuation">.</span>stereotype<span class="token punctuation">.</span>Component<span class="token punctuation">;</span>

<span class="token comment">/**
 * @Description:
 * @Author: zrblog
 * @CreateTime: 2018-09-27 07:52
 * @Version:v1.0
 */</span>
<span class="token annotation punctuation">@Component</span>
<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">KafkaConsumerController</span> <span class="token punctuation">{</span>

    <span class="token annotation punctuation">@KafkaListener</span><span class="token punctuation">(</span>topics <span class="token operator">=</span> <span class="token punctuation">{</span><span class="token string">"mytopic"</span><span class="token punctuation">}</span><span class="token punctuation">)</span>
    <span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">listen</span><span class="token punctuation">(</span>ConsumerRecord<span class="token operator">&lt;</span><span class="token operator">?</span><span class="token punctuation">,</span> <span class="token operator">?</span><span class="token operator">&gt;</span> record<span class="token punctuation">)</span> <span class="token punctuation">{</span>

        System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">printf</span><span class="token punctuation">(</span><span class="token string">"topic = %s, offest = %d, value = %s\n"</span><span class="token punctuation">,</span>record<span class="token punctuation">.</span><span class="token function">topic</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span>record<span class="token punctuation">.</span><span class="token function">offset</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span>record<span class="token punctuation">.</span><span class="token function">value</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<h3><a id="35__134"></a>3.5 测试：</h3>
<p>访问： <a href="http://localhost:8080/kafka/send?string=%E4%BD%A0%E5%A5%BD" rel="nofollow">http://localhost:8080/kafka/send?string=你好</a></p>
<p><img src="picture/2018-11-03-14-23-08.png" alt="a"></p>
<p><img src="picture/2018-11-03-14-23-44.png" alt="a"></p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>