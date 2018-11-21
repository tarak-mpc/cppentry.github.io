---
layout:     post
title:      springboot整合kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><strong>Kafka安装</strong></p>

<p>Kafka需要依赖JAVA环境运行，如何安装JDK这里不做介绍。</p>

<p><strong>下载kafka：</strong></p>



<pre class="prettyprint"><code class=" hljs avrasm">wget http://mirror<span class="hljs-preprocessor">.bit</span><span class="hljs-preprocessor">.edu</span><span class="hljs-preprocessor">.cn</span>/apache/kafka/<span class="hljs-number">1.1</span><span class="hljs-number">.0</span>/kafka_2<span class="hljs-number">.11</span>-<span class="hljs-number">1.1</span><span class="hljs-number">.0</span><span class="hljs-preprocessor">.tgz</span></code></pre>

<p>将包下载到执行目录并解压：</p>



<pre class="prettyprint"><code class=" hljs lasso">cd /usr/<span class="hljs-built_in">local</span><span class="hljs-subst">/</span>
tar <span class="hljs-attribute">-xzvf</span> kafka_2<span class="hljs-number">.11</span><span class="hljs-subst">-</span><span class="hljs-number">0.10</span><span class="hljs-number">.0</span><span class="hljs-number">.1</span><span class="hljs-built_in">.</span>tgz </code></pre>

<p>修改kafka配置文件：</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-built_in">cd</span> kafka_2.<span class="hljs-number">11</span>-<span class="hljs-number">0.10</span>.<span class="hljs-number">0.1</span>/config/</code></pre>

<p>编辑配置文件</p>

<pre class="prettyprint"><code class=" hljs axapta">vi <span class="hljs-keyword">server</span>.properties</code></pre>



<pre class="prettyprint"><code class=" hljs vala">broker.id=<span class="hljs-number">0</span>
<span class="hljs-preprocessor">#端口号、记得开启端口，云服务器要开放安全组</span>
port=<span class="hljs-number">9092</span>
<span class="hljs-preprocessor">#服务器IP地址，修改为自己的服务器IP</span>
<span class="hljs-preprocessor">#host.name=127.0.0.1</span>
host.name=<span class="hljs-number">192.168</span><span class="hljs-number">.200</span><span class="hljs-number">.130</span>
<span class="hljs-preprocessor">#zookeeper地址和端口， Kafka支持内置的Zookeeper和引用外部的Zookeeper</span>
zookeeper.connect=localhost:<span class="hljs-number">2181</span>
<span class="hljs-preprocessor">#非本地生产者和消费者访问Kafka，记得修改 config/server.properties中的listeners, 例如</span>
listeners=PLAINTEXT:<span class="hljs-comment">//192.168.200.130:9092</span></code></pre>

<p>分别启动 kafka 和 zookeeper：</p>

<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-built_in">.</span>/zookeeper<span class="hljs-attribute">-server</span><span class="hljs-attribute">-start</span><span class="hljs-built_in">.</span>sh /usr/<span class="hljs-built_in">local</span>/kafka_2<span class="hljs-number">.11</span><span class="hljs-subst">-</span><span class="hljs-number">0.10</span><span class="hljs-number">.0</span><span class="hljs-number">.1</span>/config/zookeeper<span class="hljs-built_in">.</span>properties <span class="hljs-subst">&amp;</span>
<span class="hljs-built_in">.</span>/kafka<span class="hljs-attribute">-server</span><span class="hljs-attribute">-start</span><span class="hljs-built_in">.</span>sh /usr/<span class="hljs-built_in">local</span>/kafka_2<span class="hljs-number">.11</span><span class="hljs-subst">-</span><span class="hljs-number">0.10</span><span class="hljs-number">.0</span><span class="hljs-number">.1</span>/config/server<span class="hljs-built_in">.</span>properties <span class="hljs-subst">&amp;</span></code></pre>

<p>后台启动方式:</p>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-built_in">.</span>/zookeeper<span class="hljs-attribute">-server</span><span class="hljs-attribute">-start</span><span class="hljs-built_in">.</span>sh /usr/<span class="hljs-built_in">local</span>/kafka_2<span class="hljs-number">.11</span><span class="hljs-subst">-</span><span class="hljs-number">0.10</span><span class="hljs-number">.0</span><span class="hljs-number">.1</span>/config/zookeeper<span class="hljs-built_in">.</span>properties  <span class="hljs-number">1</span><span class="hljs-subst">&gt;</span>/dev/<span class="hljs-built_in">null</span> <span class="hljs-number">2</span><span class="hljs-subst">&gt;&amp;</span><span class="hljs-number">1</span> <span class="hljs-subst">&amp;</span>
<span class="hljs-built_in">.</span>/kafka<span class="hljs-attribute">-server</span><span class="hljs-attribute">-start</span><span class="hljs-built_in">.</span>sh /usr/<span class="hljs-built_in">local</span>/kafka_2<span class="hljs-number">.11</span><span class="hljs-subst">-</span><span class="hljs-number">0.10</span><span class="hljs-number">.0</span><span class="hljs-number">.1</span>/config/server<span class="hljs-built_in">.</span>properties  <span class="hljs-number">1</span><span class="hljs-subst">&gt;</span>/dev/<span class="hljs-built_in">null</span> <span class="hljs-number">2</span><span class="hljs-subst">&gt;&amp;</span><span class="hljs-number">1</span> <span class="hljs-subst">&amp;</span></code></pre>

<hr>

<p><strong>SpringBoot集成</strong></p>

<p>pom.xml引入：</p>

<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-comment">&lt;!--kafka支持--&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.springframework.kafka<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spring-kafka<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.3.5.RELEASE<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span><span class="hljs-comment">&lt;!--$NO-MVN-MAN-VER$--&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span></code></pre>

<p>application.properties配置：</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor">#kafka相关配置</span>
spring<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.bootstrap</span>-servers=<span class="hljs-number">192.168</span><span class="hljs-number">.200</span><span class="hljs-number">.130</span>:<span class="hljs-number">9092</span>
<span class="hljs-preprocessor">#设置一个默认组</span>
spring<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.group</span>-id=<span class="hljs-number">0</span>
<span class="hljs-preprocessor">#key-value序列化反序列化</span>
spring<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.key</span>-deserializer=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.common</span><span class="hljs-preprocessor">.serialization</span><span class="hljs-preprocessor">.StringDeserializer</span>
spring<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.value</span>-deserializer=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.common</span><span class="hljs-preprocessor">.serialization</span><span class="hljs-preprocessor">.StringDeserializer</span>
spring<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.producer</span><span class="hljs-preprocessor">.key</span>-serializer=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.common</span><span class="hljs-preprocessor">.serialization</span><span class="hljs-preprocessor">.StringSerializer</span>
spring<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.producer</span><span class="hljs-preprocessor">.value</span>-serializer=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.common</span><span class="hljs-preprocessor">.serialization</span><span class="hljs-preprocessor">.StringSerializer</span>
<span class="hljs-preprocessor">#每次批量发送消息的数量</span>
spring<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.producer</span><span class="hljs-preprocessor">.batch</span>-size=<span class="hljs-number">65536</span>
spring<span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.producer</span><span class="hljs-preprocessor">.buffer</span>-memory=<span class="hljs-number">524288</span></code></pre>

<p>生产者KafkaSender：</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-javadoc">/**
 * 生产者
 */</span>
<span class="hljs-annotation">@RestController</span>
<span class="hljs-annotation">@RequestMapping</span>(<span class="hljs-string">"/kafka"</span>)
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">KafkaSender</span> {</span>
    <span class="hljs-annotation">@Autowired</span>
    <span class="hljs-keyword">private</span> KafkaTemplate&lt;String,String&gt; kafkaTemplate;

    <span class="hljs-javadoc">/**
     * 发送消息到kafka
     */</span>
    <span class="hljs-annotation">@RequestMapping</span>(<span class="hljs-string">"/sendMs"</span>)
    <span class="hljs-keyword">public</span> String <span class="hljs-title">sendMs</span>(String message){
        kafkaTemplate.send(<span class="hljs-string">"seckill"</span>,<span class="hljs-string">"Hi Kafka!"</span>);
        <span class="hljs-keyword">return</span> <span class="hljs-string">"消息发送成功!"</span>;
    }
}</code></pre>

<p>消费者：</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-javadoc">/**
 * 消费者 spring-kafka 2.0 + 依赖JDK8
 */</span>
<span class="hljs-annotation">@Component</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">KafkaConsumer</span> {</span>
    <span class="hljs-javadoc">/**
     * 监听seckill主题,有消息就读取
     *<span class="hljs-javadoctag"> @param</span> message
     */</span>
    <span class="hljs-annotation">@KafkaListener</span>(topics = {<span class="hljs-string">"seckill"</span>})
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">receiveMessage</span>(String message){
        <span class="hljs-comment">//收到通道的消息之后执行动作</span>
        System.out.println(<span class="hljs-string">"收到消息...."</span>+message);
    }
}</code></pre>

<p>启动服务  ,浏览器请求 <a href="http://localhost:8888/kafka/sendMs" rel="nofollow">http://localhost:8888/kafka/sendMs</a> <br>
浏览器返回:</p>



<pre class="prettyprint"><code class=" hljs erlang-repl">消息发送成功<span class="hljs-exclamation_mark">!</span></code></pre>

<p>控制台打印:</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-number">2018</span>-<span class="hljs-number">07</span>-<span class="hljs-number">20</span> <span class="hljs-number">14</span>:<span class="hljs-number">58</span>:<span class="hljs-number">48.891</span>  INFO <span class="hljs-number">6792</span> --- [nio-<span class="hljs-number">8888</span>-exec-<span class="hljs-number">1</span>] o<span class="hljs-preprocessor">.a</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.common</span><span class="hljs-preprocessor">.utils</span><span class="hljs-preprocessor">.AppInfoParser</span>     : Kafka version : <span class="hljs-number">0.11</span><span class="hljs-number">.0</span><span class="hljs-number">.2</span>
<span class="hljs-number">2018</span>-<span class="hljs-number">07</span>-<span class="hljs-number">20</span> <span class="hljs-number">14</span>:<span class="hljs-number">58</span>:<span class="hljs-number">48.891</span>  INFO <span class="hljs-number">6792</span> --- [nio-<span class="hljs-number">8888</span>-exec-<span class="hljs-number">1</span>] o<span class="hljs-preprocessor">.a</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.common</span><span class="hljs-preprocessor">.utils</span><span class="hljs-preprocessor">.AppInfoParser</span>     : Kafka commitId : <span class="hljs-number">73</span>be1e1168f91ee2
收到消息...<span class="hljs-preprocessor">.Hi</span> Kafka!</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>