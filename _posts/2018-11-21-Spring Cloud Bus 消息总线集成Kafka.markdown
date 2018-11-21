---
layout:     post
title:      Spring Cloud Bus 消息总线集成Kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<blockquote>
  <p>Kafka并没有使用Eureka作为注册中心，必须使用 ZooKeeper ，好在Kafka安装包自带了 ZooKeeper ，只需下载Kafka就好了</p>
</blockquote>

<ul>
<li><a href="http://kafka.apache.org/downloads" rel="nofollow" title="Kafka 下载" target="_blank">下载 Apache Kafka</a></li>
</ul>

<h3 id="kafka-快速入门">Kafka 快速入门</h3>

<p><strong>环境安装</strong></p>

<p>下载Kafka并解压后可以看到目录结构如下：</p>

<pre class="prettyprint"><code class=" hljs lasso">kafka
  <span class="hljs-subst">+-</span>bin
    <span class="hljs-subst">+-</span>windows
  <span class="hljs-subst">+-</span>config
  <span class="hljs-subst">+-</span>libs
  <span class="hljs-subst">+-</span>logs
  <span class="hljs-subst">+-</span>site<span class="hljs-attribute">-docs</span></code></pre>

<p>因为 Kafka 是依赖 ZooKeeper 的，所以在 <code>bin</code> 和 <code>config</code> 目录下除了Kafka相关的内容还存在ZooKeeper 相关的内容。<code>bin</code> 目录存放了 Kafka 和 ZooKeeper 的命令行工具，其中根目录是适用于<code>Linux/UNIX</code> 的shell，<code>bin/windows</code> 下则存放的适用于 <code>Windows</code> 的bat。 <code>config</code> 目录则存放了 Kafka 和 ZooKeeper的配置信息。</p>

<p><strong>启动测试</strong></p>

<ul>
<li><p>启动 ZooKeeper，执行命令 <code>zookeeper-server-start config/zookeeper.properties</code> ，该命令需要指定 ZooKeeper 的配置文件位置才能正确启动，Kafka 的压缩包中包含了其默认配置，开发和测试环境无需修改。</p></li>
<li><p>启动 Kafka，执行命令 <code>kafka-server-start config/server.properties</code> ，与 ZooKeeper 启动类似，但要指定其他环境的 ZooKeeper 的话可以修改 <code>config/server.properties</code> 的 zookeeper.connect 来设置连接 ZooKeeper 的地址和端口。</p></li>
</ul>



<h3 id="spring-cloud-集成kafka">Spring Cloud 集成Kafka</h3>

<p><strong>pom.xml加入依赖</strong></p>

<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.springframework.cloud<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spring-cloud-starter-bus-kafka<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span></code></pre>

<p>加入以上依赖后，Spring Cloud 消息总线 Kafka 已经集成完成，使用的配置则是默认启动 Kafka 和 ZooKeeper 时的配置。确定 ZooKeeper 和 Kafka 已经启动，然后再启动有上面依赖的 Spring Boot 应用 ，这时 Kafka 会新增一个名为 springCloudBus 的 Topic，可以使用命令 <code>kafka-topics --list --zookeeper localhost:2181</code> 来查看当前 Kafka 中的 Topic。</p>

<p><strong>集成后Kafka 配置</strong></p>

<blockquote>
  <p>以上的例子中 Kafka、ZooKeeper 均运行于本地，但实际应用中，Kafka 和 ZooKeeper 一般会独立部署，所以需要为Kafka 和 ZooKeeper 配置一些连接信息，Spring Boot 1.3.7 没有为 Kafka 直接提供 Starter 模块，而是使用 Spring Cloud Stream 的 Kafka 模块，配置的时候则采用 <code>spring.cloud.stream.kafka</code> 前缀</p>
</blockquote>

<p>spring.cloud.stream.kafka 配置</p>

<pre class="prettyprint"><code class=" hljs lasso">spring:
  cloud:
    stream:
      binders:
        <span class="hljs-variable">#binderName</span>
        kafka1:  
          <span class="hljs-keyword">type</span>: kafka  
          environment:
               spring:
                 cloud:
                   stream:
                     kafka: 
                       binder: 
                         <span class="hljs-variable">#kafka</span>地址
                         brokers: localhost:<span class="hljs-number">9092</span>
                         <span class="hljs-variable">#zookeeper</span>节点地址
                         zk<span class="hljs-attribute">-nodes</span>: localhost:<span class="hljs-number">2181</span>
       bindings:
         <span class="hljs-variable">#channelName</span>
         channelKafka:
           <span class="hljs-variable">#binderName</span>
           binder: kafka1
           destination: event<span class="hljs-attribute">-demo</span>
           content<span class="hljs-attribute">-type</span>: text/plain
           producer:
             partitionCount: <span class="hljs-number">1</span></code></pre>

<p>spring.kafka 配置 </p>

<blockquote>
  <p>在启动Kafka的时候有这样一个配置 <code>config/server.properties</code> 的 zookeeper.connect 指定 ZooKeeper连接地址，但是在 <code>spring.kafka</code> 中并没有看到可以配置 ZooKeeper 连接地址 的地方</p>
</blockquote>

<pre class="prettyprint"><code class=" hljs vbnet">spring:
  kafka:
    consumer:
      <span class="hljs-preprocessor">#消费者服务器地址</span>
      bootstrap-servers: localhost:<span class="hljs-number">9092</span>
    producer:
      <span class="hljs-preprocessor">#生产者服务器地址</span>
      bootstrap-servers: localhost:<span class="hljs-number">9092</span>
  cloud:
    stream:
      bindings:
        channel1:
          destination: <span class="hljs-keyword">event</span>-demo
          content-type: <span class="hljs-keyword">text</span>/plain
          producer:
            partitionCount: <span class="hljs-number">1</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>