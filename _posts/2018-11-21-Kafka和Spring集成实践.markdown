---
layout:     post
title:      Kafka和Spring集成实践
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><strong>目录</strong></p>

<hr>

<ol>
<li>安装Zookeeper</li>
<li>安装Kafka</li>
<li>创建一个Spring项目</li>
<li>使用Producer API发送消息到Kafka</li>
<li>使用Kafka High Level API接收消息</li>
<li>使用spring-integration-kafka发送消息</li>
<li><p>使用spring-integration-kafka接收消息</p>

<p>本文以单机的环境演示如何将Kafka和Spring集成。 <br>
单机的环境最容易搭建， 并且只需在自己的PC上运行即可， 不需要很多的硬件环境，便于学习。 况且，本文的目的不是搭建ZooKeeper的集群环境， 而是重点介绍Kafka和Spring的应用。 <br>
具体的软件环境如下：</p></li>
</ol>

<p>OS: CentOS 6.4 <br>
Zookepper： zookeeper-3.4.6 <br>
Kafka： kafka_2.9.1-0.8.2-beta <br>
Java： JDK 1.7.0_45-b18 <br>
Spring:4.0.6 <br>
本例子在我的这个环境中运行正常， 全部代码可以到 github 下载。</p>

<p>本文所有的操作系统用户都是root。 实际产品中可能安全标准需要特定的用户如zookeeper, kafka等。</p>

<p><strong>安装Zookeeper</strong></p>

<hr>

<p>首先下载解压zookeeper,选择合适的镜像站点以加快下载速度。 <br>
我们可以将zookeeper加到系统服务中， 增加一个/etc/init.d/zookeeper文件。</p>

<pre class="prettyprint"><code class=" hljs avrasm">cd /opt
wget  http://apache<span class="hljs-preprocessor">.fayea</span><span class="hljs-preprocessor">.com</span>/apache-mirror/zookeeper/zookeeper-<span class="hljs-number">3.4</span><span class="hljs-number">.6</span>/zookeeper-<span class="hljs-number">3.4</span><span class="hljs-number">.6</span><span class="hljs-preprocessor">.tar</span><span class="hljs-preprocessor">.gz</span>
tar zxvf zookeeper-<span class="hljs-number">3.4</span><span class="hljs-number">.6</span><span class="hljs-preprocessor">.tar</span><span class="hljs-preprocessor">.gz</span>
vi /etc/init<span class="hljs-preprocessor">.d</span>/zookeeper</code></pre>

<p>将<a href="https://raw.githubusercontent.com/apache/zookeeper/trunk/src/packages/rpm/init.d/zookeeper" rel="nofollow">https://raw.githubusercontent.com/apache/zookeeper/trunk/src/packages/rpm/init.d/zookeeper</a>文件的内容拷贝到这个文件，修改其中的运行zookeeper的用户以及zookeeper的文件夹位置。</p>



<pre class="prettyprint"><code class=" hljs bash">......
<span class="hljs-function"><span class="hljs-title">start</span></span>() {
  <span class="hljs-built_in">echo</span> -n $<span class="hljs-string">"Starting <span class="hljs-variable">$desc</span> (zookeeper): "</span>
  daemon --user root /opt/zookeeper-<span class="hljs-number">3.4</span>.<span class="hljs-number">6</span>/zkServer.sh start
  RETVAL=$?
  <span class="hljs-built_in">echo</span>
  [ <span class="hljs-variable">$RETVAL</span> <span class="hljs-operator">-eq</span> <span class="hljs-number">0</span> ] &amp;&amp; touch /var/lock/subsys/zookeeper
  <span class="hljs-keyword">return</span> <span class="hljs-variable">$RETVAL</span>
}
<span class="hljs-function"><span class="hljs-title">stop</span></span>() {
  <span class="hljs-built_in">echo</span> -n $<span class="hljs-string">"Stopping <span class="hljs-variable">$desc</span> (zookeeper): "</span>
  daemon --user root /opt/zookeeper-<span class="hljs-number">3.4</span>.<span class="hljs-number">6</span>/zkServer.sh stop
  RETVAL=$?
  sleep <span class="hljs-number">5</span>
  <span class="hljs-built_in">echo</span>
  [ <span class="hljs-variable">$RETVAL</span> <span class="hljs-operator">-eq</span> <span class="hljs-number">0</span> ] &amp;&amp; rm <span class="hljs-operator">-f</span> /var/lock/subsys/zookeeper <span class="hljs-variable">$PIDFILE</span>
}
......</code></pre>



<pre class="prettyprint"><code class=" hljs sql">chmod 755 /etc/init.d/zookeeper
service zookeeper <span class="hljs-operator"><span class="hljs-keyword">start</span></span></code></pre>

<p>如果你不想加到服务，也可以直接运行zookeeper。</p>



<pre class="prettyprint"><code class=" hljs sql">/opt/zookeeper-3.4.6/zkServer.sh <span class="hljs-operator"><span class="hljs-keyword">start</span></span></code></pre>

<p><strong>安装Kafka</strong></p>

<hr>

<pre><code>从合适的镜像站点下载最新的kafka并解压。
</code></pre>

<pre class="prettyprint"><code class=" hljs autohotkey"><span class="hljs-label">wget http:</span>//apache.<span class="hljs-number">01</span>link.hk/kafka/<span class="hljs-number">0.8</span>.<span class="hljs-number">2</span>-beta/kafk<span class="hljs-built_in">a_2</span>.<span class="hljs-number">9.1</span>-<span class="hljs-number">0.8</span>.<span class="hljs-number">2</span>-beta.tgz
tar zxvf kafk<span class="hljs-built_in">a_2</span>.<span class="hljs-number">9.1</span>-<span class="hljs-number">0.8</span>.<span class="hljs-number">2</span>-beta.tgz
cd kafk<span class="hljs-built_in">a_2</span>.<span class="hljs-number">9.1</span>-<span class="hljs-number">0.8</span>.<span class="hljs-number">2</span>-beta</code></pre>

<pre><code>启动Kafka:
</code></pre>



<pre class="prettyprint"><code class=" hljs axapta">bin/kafka-<span class="hljs-keyword">server</span>-start.sh config/<span class="hljs-keyword">server</span>.properties</code></pre>

<pre><code>创建一个test的topic：
</code></pre>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">create</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">localhost:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">replication</span><span class="hljs-literal">-</span><span class="hljs-comment">factor</span> <span class="hljs-comment">1</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">partitions</span> <span class="hljs-comment">1</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">test</span></code></pre>

<pre><code>可以利用kafka的命令启动一个生产者和消费者试验一下：
</code></pre>



<pre class="prettyprint"><code class=" hljs vhdl">bin/kafka-console-producer.sh <span class="hljs-comment">--broker-list localhost:9092 --topic test</span>
This <span class="hljs-keyword">is</span> a message
This <span class="hljs-keyword">is</span> another message</code></pre>



<pre class="prettyprint"><code class=" hljs cs">bin/kafka-console-consumer.sh --zookeeper localhost:<span class="hljs-number">2181</span> --topic test --<span class="hljs-keyword">from</span>-beginning
This <span class="hljs-keyword">is</span> a message
This <span class="hljs-keyword">is</span> another message</code></pre>

<p>更多的介绍可以查看我翻译整理的 <a href="http://colobu.com/2014/08/06/kafka-quickstart/" rel="nofollow">Kafka快速入门</a></p>

<p><strong>创建一个Spring项目</strong></p>

<hr>

<p>以上的准备环境完成，让我们开始创建一个项目。 <br>
以前我写过一篇简单介绍: <a href="http://colobu.com/2014/08/07/spring-integration-kafka/" rel="nofollow">Spring 集成 Kafka</a>. <br>
<a href="https://github.com/spring-projects/spring-integration-kafka" rel="nofollow">spring-integration-kafka</a>这个官方框架我就不介绍了。 我们主要使用它做集成。</p>

<p>首先我们先看一下使用Kafka自己的Producer/Consumer API发送/接收消息的例子。</p>

<p><strong>使用Producer API发送消息到Kafka</strong></p>

<p>OK，现在我们先看一个使用Kafka 自己的producer API发送消息的例子：</p>



<pre class="prettyprint"><code class=" hljs vbscript"><span class="hljs-keyword">public</span> <span class="hljs-keyword">class</span> NativeProducer {
    <span class="hljs-keyword">public</span> static void main(<span class="hljs-built_in">String</span>[] args) {
        <span class="hljs-built_in">String</span> topic= <span class="hljs-string">"test"</span>;
        long events = <span class="hljs-number">100</span>;
        Random rand = <span class="hljs-keyword">new</span> Random();

        Properties props = <span class="hljs-keyword">new</span> Properties();
        props.put(<span class="hljs-string">"metadata.broker.list"</span>, <span class="hljs-string">"localhost:9092"</span>);
        props.put(<span class="hljs-string">"serializer.class"</span>, <span class="hljs-string">"kafka.serializer.StringEncoder"</span>);
        props.put(<span class="hljs-string">"request.required.acks"</span>, <span class="hljs-string">"1"</span>);

        ProducerConfig config = <span class="hljs-keyword">new</span> ProducerConfig(props);

        Producer&lt;<span class="hljs-built_in">String</span>, <span class="hljs-built_in">String</span>&gt; producer = <span class="hljs-keyword">new</span> Producer&lt;<span class="hljs-built_in">String</span>, <span class="hljs-built_in">String</span>&gt;(config);

        <span class="hljs-keyword">for</span> (long nEvents = <span class="hljs-number">0</span>; nEvents &lt; events; nEvents++) {                
               <span class="hljs-built_in">String</span> msg = <span class="hljs-string">"NativeMessage-"</span> + rand.nextInt() ; 
               KeyedMessage&lt;<span class="hljs-built_in">String</span>, <span class="hljs-built_in">String</span>&gt; data = <span class="hljs-keyword">new</span> KeyedMessage&lt;<span class="hljs-built_in">String</span>, <span class="hljs-built_in">String</span>&gt;(topic, nEvents + <span class="hljs-string">""</span>, msg);
               producer.send(data);
        }
        producer.close();
    }
}</code></pre>

<p>这个例子中首先初始化Producer对象，指定相应的broker和serializer， 然后发送100个字符串消息给Kafka。</p>

<p>运行mvn package编译代码，执行查看结果：</p>



<pre class="prettyprint"><code class=" hljs ruby">java -cp target/lib/*<span class="hljs-symbol">:target/spring-kafka-demo-</span><span class="hljs-number">0</span>.<span class="hljs-number">2.0</span>-<span class="hljs-constant">SNAPSHOT</span>.jar com.colobu.spring_kafka_demo.<span class="hljs-constant">NativeProducer</span></code></pre>

<p>上面的消费者控制台窗口会打印出收到的消息:</p>



<pre class="prettyprint"><code class=" hljs asciidoc"><span class="hljs-code">......
NativeMessage--1645592376
NativeMessage-534168193
NativeMessage--1899432197
NativeMessage-1642480773
NativeMessage--911267171
NativeMessage-251458151
NativeMessage--55710397
NativeMessage-455515562
NativeMessage-1108982916
NativeMessage--1710296834
NativeMessage-2102648373
NativeMessage-499979365
NativeMessage--1200107003
NativeMessage-1184836299
NativeMessage--1161123005
NativeMessage-912582115
NativeMessage--1557863408
NativeMessage--1036456356
......</span></code></pre>

<p><strong>使用Kafka High Level API接收消息</strong></p>

<p>用High level Consumer API接收消息</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">import</span> java.util.HashMap;
<span class="hljs-keyword">import</span> java.util.List;
<span class="hljs-keyword">import</span> java.util.Map;
<span class="hljs-keyword">import</span> java.util.Properties;
<span class="hljs-keyword">import</span> java.util.concurrent.ExecutorService;
<span class="hljs-keyword">import</span> java.util.concurrent.Executors;
<span class="hljs-keyword">import</span> kafka.consumer.ConsumerConfig;
<span class="hljs-keyword">import</span> kafka.consumer.ConsumerIterator;
<span class="hljs-keyword">import</span> kafka.consumer.KafkaStream;
<span class="hljs-keyword">import</span> kafka.javaapi.consumer.ConsumerConnector;
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">NativeConsumer</span> {</span>
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">final</span> ConsumerConnector consumer;
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">final</span> String topic;
    <span class="hljs-keyword">private</span> ExecutorService executor;
    <span class="hljs-keyword">public</span> <span class="hljs-title">NativeConsumer</span>(String a_zookeeper, String a_groupId, String a_topic) {
        consumer = kafka.consumer.Consumer.createJavaConsumerConnector(createConsumerConfig(a_zookeeper, a_groupId));
        <span class="hljs-keyword">this</span>.topic = a_topic;
    }
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">shutdown</span>() {
        <span class="hljs-keyword">if</span> (consumer != <span class="hljs-keyword">null</span>)
            consumer.shutdown();
        <span class="hljs-keyword">if</span> (executor != <span class="hljs-keyword">null</span>)
            executor.shutdown();
    }
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">run</span>(<span class="hljs-keyword">int</span> a_numThreads) {
        Map&lt;String, Integer&gt; topicCountMap = <span class="hljs-keyword">new</span> HashMap&lt;String, Integer&gt;();
        topicCountMap.put(topic, <span class="hljs-keyword">new</span> Integer(a_numThreads));
        Map&lt;String, List&lt;KafkaStream&lt;<span class="hljs-keyword">byte</span>[], <span class="hljs-keyword">byte</span>[]&gt;&gt;&gt; consumerMap = consumer.createMessageStreams(topicCountMap);
        List&lt;KafkaStream&lt;<span class="hljs-keyword">byte</span>[], <span class="hljs-keyword">byte</span>[]&gt;&gt; streams = consumerMap.get(topic);
        <span class="hljs-comment">// now launch all the threads</span>
        <span class="hljs-comment">//</span>
        executor = Executors.newFixedThreadPool(a_numThreads);
        <span class="hljs-comment">// now create an object to consume the messages</span>
        <span class="hljs-comment">//</span>
        <span class="hljs-keyword">int</span> threadNumber = <span class="hljs-number">0</span>;
        <span class="hljs-keyword">for</span> (<span class="hljs-keyword">final</span> KafkaStream stream : streams) {
            executor.submit(<span class="hljs-keyword">new</span> ConsumerTest(stream, threadNumber));
            threadNumber++;
        }
    }
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> ConsumerConfig <span class="hljs-title">createConsumerConfig</span>(String a_zookeeper, String a_groupId) {
        Properties props = <span class="hljs-keyword">new</span> Properties();
        props.put(<span class="hljs-string">"zookeeper.connect"</span>, a_zookeeper);
        props.put(<span class="hljs-string">"group.id"</span>, a_groupId);
        props.put(<span class="hljs-string">"zookeeper.session.timeout.ms"</span>, <span class="hljs-string">"400"</span>);
        props.put(<span class="hljs-string">"zookeeper.sync.time.ms"</span>, <span class="hljs-string">"200"</span>);
        props.put(<span class="hljs-string">"auto.commit.interval.ms"</span>, <span class="hljs-string">"1000"</span>);
        <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> ConsumerConfig(props);
    }
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) {
        String zooKeeper = <span class="hljs-string">"localhost:2181"</span>;
        String groupId = <span class="hljs-string">"mygroup"</span>;
        String topic = <span class="hljs-string">"test"</span>;
        <span class="hljs-keyword">int</span> threads = <span class="hljs-number">1</span>;
        NativeConsumer example = <span class="hljs-keyword">new</span> NativeConsumer(zooKeeper, groupId, topic);
        example.run(threads);
        <span class="hljs-keyword">try</span> {
            Thread.sleep(<span class="hljs-number">10000</span>);
        } <span class="hljs-keyword">catch</span> (InterruptedException ie) {
        }
        <span class="hljs-comment">//example.shutdown();</span>
    }
}
class ConsumerTest implements Runnable {
    <span class="hljs-keyword">private</span> KafkaStream m_stream;
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">int</span> m_threadNumber;

    <span class="hljs-keyword">public</span> <span class="hljs-title">ConsumerTest</span>(KafkaStream a_stream, <span class="hljs-keyword">int</span> a_threadNumber) {
        m_threadNumber = a_threadNumber;
        m_stream = a_stream;
    }

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">run</span>() {
        ConsumerIterator&lt;<span class="hljs-keyword">byte</span>[], <span class="hljs-keyword">byte</span>[]&gt; it = m_stream.iterator();
        <span class="hljs-keyword">while</span> (it.hasNext())
            System.out.println(<span class="hljs-string">"Thread "</span> + m_threadNumber + <span class="hljs-string">": "</span> + <span class="hljs-keyword">new</span> String(it.next().message()));
        System.out.println(<span class="hljs-string">"Shutting down Thread: "</span> + m_threadNumber);
    }
}</code></pre>

<p>在生产者控制台输入几条消息，可以看到运行这个例子的控制台可以将这些消息打印出来。</p>

<p>教程的代码中还包括一个使用Simple Consumer API接收消息的例子。 因为spring-integration-kafka不支持这种API，这里也不列出对比代码了。</p>

<p><strong>使用spring-integration-kafka发送消息</strong></p>

<p>Outbound Channel Adapter用来发送消息到Kafka。 消息从Spring Integration Channel中读取。 你可以在Spring application context指定这个channel。 <br>
一旦配置好这个Channel，就可以利用这个Channel往Kafka发消息。 明显地，Spring Integration特定的消息发送给这个Adaptor，然后发送前在内部被转为Kafka消息。当前的版本要求你必须指定消息key和topic作为头部数据 (header)，消息作为有载荷(payload)。 <br>
例如</p>

<pre class="prettyprint"><code class=" hljs avrasm">final MessageChannel channel = ctx<span class="hljs-preprocessor">.getBean</span>(<span class="hljs-string">"inputToKafka"</span>, MessageChannel<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;</span>
channel<span class="hljs-preprocessor">.send</span>(
        MessageBuilder<span class="hljs-preprocessor">.withPayload</span>(payload) //设置有效载荷
                <span class="hljs-preprocessor">.setHeader</span>(<span class="hljs-string">"messageKey"</span>, <span class="hljs-string">"key"</span>) //指定key
                <span class="hljs-preprocessor">.setHeader</span>(<span class="hljs-string">"topic"</span>, <span class="hljs-string">"test"</span>)<span class="hljs-preprocessor">.build</span>())<span class="hljs-comment">; /指定topic/</span></code></pre>

<p>实际代码如下：</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">import</span> java.util.Random;
<span class="hljs-keyword">import</span> org.springframework.context.support.ClassPathXmlApplicationContext;
<span class="hljs-keyword">import</span> org.springframework.integration.support.MessageBuilder;
<span class="hljs-keyword">import</span> org.springframework.messaging.MessageChannel;
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">Producer</span> {</span>
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> String CONFIG = <span class="hljs-string">"/context.xml"</span>;
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> Random rand = <span class="hljs-keyword">new</span> Random();
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) {
        <span class="hljs-keyword">final</span> ClassPathXmlApplicationContext ctx = <span class="hljs-keyword">new</span> ClassPathXmlApplicationContext(CONFIG, Producer.class);
        ctx.start();
        <span class="hljs-keyword">final</span> MessageChannel channel = ctx.getBean(<span class="hljs-string">"inputToKafka"</span>, MessageChannel.class);
        <span class="hljs-keyword">for</span> (<span class="hljs-keyword">int</span> i = <span class="hljs-number">0</span>; i &lt; <span class="hljs-number">100</span>; i++) {
            channel.send(MessageBuilder.withPayload(<span class="hljs-string">"Message-"</span> + rand.nextInt()).setHeader(<span class="hljs-string">"messageKey"</span>, String.valueOf(i)).setHeader(<span class="hljs-string">"topic"</span>, <span class="hljs-string">"test"</span>).build());
        }
        <span class="hljs-keyword">try</span> {
            Thread.sleep(<span class="hljs-number">100000</span>);
        } <span class="hljs-keyword">catch</span> (InterruptedException e) {
            e.printStackTrace();
        }
        ctx.close();
    }
}</code></pre>

<p>Spring 配置文件：</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-pi">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">beans</span> <span class="hljs-attribute">xmlns</span>=<span class="hljs-value">"http://www.springframework.org/schema/beans"</span>
       <span class="hljs-attribute">xmlns:xsi</span>=<span class="hljs-value">"http://www.w3.org/2001/XMLSchema-instance"</span>
       <span class="hljs-attribute">xmlns:int</span>=<span class="hljs-value">"http://www.springframework.org/schema/integration"</span>
       <span class="hljs-attribute">xmlns:int-kafka</span>=<span class="hljs-value">"http://www.springframework.org/schema/integration/kafka"</span>
       <span class="hljs-attribute">xmlns:task</span>=<span class="hljs-value">"http://www.springframework.org/schema/task"</span>
       <span class="hljs-attribute">xsi:schemaLocation</span>=<span class="hljs-value">"http://www.springframework.org/schema/integration/kafka http://www.springframework.org/schema/integration/kafka/spring-integration-kafka.xsd
        http://www.springframework.org/schema/integration http://www.springframework.org/schema/integration/spring-integration.xsd
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task.xsd"</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">int:channel</span> <span class="hljs-attribute">id</span>=<span class="hljs-value">"inputToKafka"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">int:queue</span>/&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">int:channel</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">int-kafka:outbound-channel-adapter</span> <span class="hljs-attribute">id</span>=<span class="hljs-value">"kafkaOutboundChannelAdapter"</span>
                                        <span class="hljs-attribute">kafka-producer-context-ref</span>=<span class="hljs-value">"kafkaProducerContext"</span>
                                        <span class="hljs-attribute">auto-startup</span>=<span class="hljs-value">"false"</span>
                                        <span class="hljs-attribute">channel</span>=<span class="hljs-value">"inputToKafka"</span>
                                        <span class="hljs-attribute">order</span>=<span class="hljs-value">"3"</span>
            &gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">int:poller</span> <span class="hljs-attribute">fixed-delay</span>=<span class="hljs-value">"1000"</span> <span class="hljs-attribute">time-unit</span>=<span class="hljs-value">"MILLISECONDS"</span> <span class="hljs-attribute">receive-timeout</span>=<span class="hljs-value">"0"</span> <span class="hljs-attribute">task-executor</span>=<span class="hljs-value">"taskExecutor"</span>/&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">int-kafka:outbound-channel-adapter</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">task:executor</span> <span class="hljs-attribute">id</span>=<span class="hljs-value">"taskExecutor"</span> <span class="hljs-attribute">pool-size</span>=<span class="hljs-value">"5"</span> <span class="hljs-attribute">keep-alive</span>=<span class="hljs-value">"120"</span> <span class="hljs-attribute">queue-capacity</span>=<span class="hljs-value">"500"</span>/&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">bean</span> <span class="hljs-attribute">id</span>=<span class="hljs-value">"producerProperties"</span>
        <span class="hljs-attribute">class</span>=<span class="hljs-value">"org.springframework.beans.factory.config.PropertiesFactoryBean"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"properties"</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">props</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">prop</span> <span class="hljs-attribute">key</span>=<span class="hljs-value">"topic.metadata.refresh.interval.ms"</span>&gt;</span>3600000<span class="hljs-tag">&lt;/<span class="hljs-title">prop</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">prop</span> <span class="hljs-attribute">key</span>=<span class="hljs-value">"message.send.max.retries"</span>&gt;</span>5<span class="hljs-tag">&lt;/<span class="hljs-title">prop</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">prop</span> <span class="hljs-attribute">key</span>=<span class="hljs-value">"serializer.class"</span>&gt;</span>kafka.serializer.StringEncoder<span class="hljs-tag">&lt;/<span class="hljs-title">prop</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">prop</span> <span class="hljs-attribute">key</span>=<span class="hljs-value">"request.required.acks"</span>&gt;</span>1<span class="hljs-tag">&lt;/<span class="hljs-title">prop</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">props</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">bean</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">int-kafka:producer-context</span> <span class="hljs-attribute">id</span>=<span class="hljs-value">"kafkaProducerContext"</span>
        <span class="hljs-attribute">producer-properties</span>=<span class="hljs-value">"producerProperties"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">int-kafka:producer-configurations</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">int-kafka:producer-configuration</span> <span class="hljs-attribute">broker-list</span>=<span class="hljs-value">"localhost:9092"</span>
                       <span class="hljs-attribute">topic</span>=<span class="hljs-value">"test"</span>
                       <span class="hljs-attribute">compression-codec</span>=<span class="hljs-value">"default"</span>/&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">int-kafka:producer-configurations</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">int-kafka:producer-context</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">beans</span>&gt;</span></code></pre>

<p>int:channel是配置Spring Integration Channel, 此channel基于queue。 <br>
int-kafka:outbound-channel-adapter是outbound-channel-adapter对象， 内部使用一个线程池处理消息。关键是kafka-producer-context-ref。 <br>
int-kafka:producer-context配置producer列表，要处理的topic，这些Producer最终要转换成Kafka的Producer。</p>

<p>producer的配置参数如下：</p>



<pre class="prettyprint"><code class=" hljs vbnet">broker-list             List <span class="hljs-keyword">of</span> comma separated brokers that this producer connects <span class="hljs-keyword">to</span>
topic                   Topic name <span class="hljs-keyword">or</span> Java regex pattern <span class="hljs-keyword">of</span> topic name
compression-codec       Compression method <span class="hljs-keyword">to</span> be used. <span class="hljs-keyword">Default</span> <span class="hljs-keyword">is</span> no compression. Supported compression codec are gzip <span class="hljs-keyword">and</span> snappy.
                        Anything <span class="hljs-keyword">else</span> would result <span class="hljs-keyword">in</span> no compression
value-encoder           Serializer <span class="hljs-keyword">to</span> be used <span class="hljs-keyword">for</span> encoding messages.
<span class="hljs-keyword">key</span>-encoder             Serializer <span class="hljs-keyword">to</span> be used <span class="hljs-keyword">for</span> encoding the partition <span class="hljs-keyword">key</span>
<span class="hljs-keyword">key</span>-<span class="hljs-keyword">class</span>-type          Type <span class="hljs-keyword">of</span> the <span class="hljs-keyword">key</span> <span class="hljs-keyword">class</span>. This will be ignored <span class="hljs-keyword">if</span> no <span class="hljs-keyword">key</span>-encoder <span class="hljs-keyword">is</span> provided
value-<span class="hljs-keyword">class</span>-type        Type <span class="hljs-keyword">of</span> the value <span class="hljs-keyword">class</span>. This will be ignored <span class="hljs-keyword">if</span> no value-encoder <span class="hljs-keyword">is</span> provided.
partitioner             <span class="hljs-keyword">Custom</span> implementation <span class="hljs-keyword">of</span> a Kafka Partitioner <span class="hljs-keyword">interface</span>.
async                   <span class="hljs-literal">True</span>/<span class="hljs-literal">False</span> - <span class="hljs-keyword">default</span> <span class="hljs-keyword">is</span> <span class="hljs-literal">false</span>. Setting this <span class="hljs-keyword">to</span> <span class="hljs-literal">true</span> would make the Kafka producer <span class="hljs-keyword">to</span> use
                        an async producer
batch-num-messages      Number <span class="hljs-keyword">of</span> messages <span class="hljs-keyword">to</span> batch at the producer. <span class="hljs-keyword">If</span> async <span class="hljs-keyword">is</span> <span class="hljs-literal">false</span>, <span class="hljs-keyword">then</span> this has no effect.</code></pre>

<p>value-encoder 和key-encoder可以是其它实现了Kafka Encoder接口的Bean。同样partitioner也是实现了Kafka的Partitioner接口的Bean。 <br>
一个Encoder的例子：</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">bean</span> <span class="hljs-attribute">id</span>=<span class="hljs-value">"kafkaEncoder"</span> <span class="hljs-attribute">class</span>=<span class="hljs-value">"org.springframework.integration.kafka.serializer.avro.AvroSpecificDatumBackedKafkaEncoder"</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">constructor-arg</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"com.company.AvroGeneratedSpecificRecord"</span> /&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">bean</span>&gt;</span></code></pre>

<p>Spring Integration Kafka 也提供了个基于Avro的Encoder。 Avro也是Apache的一个项目， 在大数据处理时也是一个常用的序列化框架。 <br>
不指定Encoder将使用Kafka缺省的Encoder (kafka.serializer.DefaultEncoder, byte[] –&gt; same byte[])。</p>

<p>producerProperties可以用来设置配置属性进行调优。配置属性列表请参考 <a href="http://kafka.apache.org/documentation.html#producerconfigs" rel="nofollow">http://kafka.apache.org/documentation.html#producerconfigs</a></p>

<p><strong>使用spring-integration-kafka接收消息</strong></p>

<p>同样的原理实现一个消费者：</p>



<pre class="prettyprint"><code class=" hljs avrasm">package <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.colobu</span><span class="hljs-preprocessor">.spring</span>_kafka_demo<span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Collection</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.HashMap</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Iterator</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.List</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Map</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Random</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Set</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.concurrent</span><span class="hljs-preprocessor">.ConcurrentHashMap</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.slf</span>4j<span class="hljs-preprocessor">.LoggerFactory</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.springframework</span><span class="hljs-preprocessor">.context</span><span class="hljs-preprocessor">.support</span><span class="hljs-preprocessor">.ClassPathXmlApplicationContext</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.springframework</span><span class="hljs-preprocessor">.integration</span><span class="hljs-preprocessor">.channel</span><span class="hljs-preprocessor">.QueueChannel</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.springframework</span><span class="hljs-preprocessor">.messaging</span><span class="hljs-preprocessor">.Message</span><span class="hljs-comment">;</span>
import ch<span class="hljs-preprocessor">.qos</span><span class="hljs-preprocessor">.logback</span><span class="hljs-preprocessor">.classic</span><span class="hljs-preprocessor">.Level</span><span class="hljs-comment">;</span>
public class Consumer {
    private static final String CONFIG = <span class="hljs-string">"/consumer_context.xml"</span><span class="hljs-comment">;</span>
    private static Random rand = new Random()<span class="hljs-comment">;</span>
    @SuppressWarnings({ <span class="hljs-string">"unchecked"</span>, <span class="hljs-string">"unchecked"</span>, <span class="hljs-string">"rawtypes"</span> })
    public static void main(String[] args) {
        ch<span class="hljs-preprocessor">.qos</span><span class="hljs-preprocessor">.logback</span><span class="hljs-preprocessor">.classic</span><span class="hljs-preprocessor">.Logger</span> rootLogger = (ch<span class="hljs-preprocessor">.qos</span><span class="hljs-preprocessor">.logback</span><span class="hljs-preprocessor">.classic</span><span class="hljs-preprocessor">.Logger</span>)LoggerFactory<span class="hljs-preprocessor">.getLogger</span>(ch<span class="hljs-preprocessor">.qos</span><span class="hljs-preprocessor">.logback</span><span class="hljs-preprocessor">.classic</span><span class="hljs-preprocessor">.Logger</span><span class="hljs-preprocessor">.ROOT</span>_LOGGER_NAME)<span class="hljs-comment">;</span>
        rootLogger<span class="hljs-preprocessor">.setLevel</span>(Level<span class="hljs-preprocessor">.toLevel</span>(<span class="hljs-string">"info"</span>))<span class="hljs-comment">;</span>

        final ClassPathXmlApplicationContext ctx = new ClassPathXmlApplicationContext(CONFIG, Consumer<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;</span>
        ctx<span class="hljs-preprocessor">.start</span>()<span class="hljs-comment">;</span>
        final QueueChannel channel = ctx<span class="hljs-preprocessor">.getBean</span>(<span class="hljs-string">"inputFromKafka"</span>, QueueChannel<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;</span>
        Message msg<span class="hljs-comment">;        </span>
        while((msg = channel<span class="hljs-preprocessor">.receive</span>()) != null) {
            HashMap map = (HashMap)msg<span class="hljs-preprocessor">.getPayload</span>()<span class="hljs-comment">;</span>
            <span class="hljs-keyword">Set</span>&lt;Map<span class="hljs-preprocessor">.Entry</span>&gt; <span class="hljs-keyword">set</span> = map<span class="hljs-preprocessor">.entrySet</span>()<span class="hljs-comment">;</span>
            for (Map<span class="hljs-preprocessor">.Entry</span> entry : <span class="hljs-keyword">set</span>) {
                String topic = (String)entry<span class="hljs-preprocessor">.getKey</span>()<span class="hljs-comment">;</span>
                System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"Topic:"</span> + topic)<span class="hljs-comment">;</span>
                ConcurrentHashMap&lt;Integer,List&lt;byte[]&gt;&gt; messages = (ConcurrentHashMap&lt;Integer,List&lt;byte[]&gt;&gt;)entry<span class="hljs-preprocessor">.getValue</span>()<span class="hljs-comment">;</span>
                Collection&lt;List&lt;byte[]&gt;&gt; values = messages<span class="hljs-preprocessor">.values</span>()<span class="hljs-comment">;</span>
                for (Iterator&lt;List&lt;byte[]&gt;&gt; iterator = values<span class="hljs-preprocessor">.iterator</span>()<span class="hljs-comment">; iterator.hasNext();) {</span>
                    List&lt;byte[]&gt; list = iterator<span class="hljs-preprocessor">.next</span>()<span class="hljs-comment">;</span>
                    for (byte[] object : list) {
                        String message = new String(object)<span class="hljs-comment">;</span>
                        System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"\tMessage: "</span> + message)<span class="hljs-comment">;</span>
                    }

                }

            }

        }

        try {
            Thread<span class="hljs-preprocessor">.sleep</span>(<span class="hljs-number">100000</span>)<span class="hljs-comment">;</span>
        } catch (InterruptedException e) {
            e<span class="hljs-preprocessor">.printStackTrace</span>()<span class="hljs-comment">;</span>
        }
        ctx<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>
    }
}</code></pre>

<p>Spring的配置文件如下：</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-pi">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">beans</span> <span class="hljs-attribute">xmlns</span>=<span class="hljs-value">"http://www.springframework.org/schema/beans"</span>
    <span class="hljs-attribute">xmlns:xsi</span>=<span class="hljs-value">"http://www.w3.org/2001/XMLSchema-instance"</span> <span class="hljs-attribute">xmlns:int</span>=<span class="hljs-value">"http://www.springframework.org/schema/integration"</span>
    <span class="hljs-attribute">xmlns:int-kafka</span>=<span class="hljs-value">"http://www.springframework.org/schema/integration/kafka"</span>
    <span class="hljs-attribute">xmlns:task</span>=<span class="hljs-value">"http://www.springframework.org/schema/task"</span>
    <span class="hljs-attribute">xsi:schemaLocation</span>=<span class="hljs-value">"http://www.springframework.org/schema/integration/kafka http://www.springframework.org/schema/integration/kafka/spring-integration-kafka.xsd
        http://www.springframework.org/schema/integration http://www.springframework.org/schema/integration/spring-integration.xsd
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task.xsd"</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">int:channel</span> <span class="hljs-attribute">id</span>=<span class="hljs-value">"inputFromKafka"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">int:queue</span>/&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">int:channel</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">int-kafka:inbound-channel-adapter
</span>        <span class="hljs-attribute">id</span>=<span class="hljs-value">"kafkaInboundChannelAdapter"</span> <span class="hljs-attribute">kafka-consumer-context-ref</span>=<span class="hljs-value">"consumerContext"</span>
        <span class="hljs-attribute">auto-startup</span>=<span class="hljs-value">"false"</span> <span class="hljs-attribute">channel</span>=<span class="hljs-value">"inputFromKafka"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">int:poller</span> <span class="hljs-attribute">fixed-delay</span>=<span class="hljs-value">"10"</span> <span class="hljs-attribute">time-unit</span>=<span class="hljs-value">"MILLISECONDS"</span>
            <span class="hljs-attribute">max-messages-per-poll</span>=<span class="hljs-value">"5"</span> /&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">int-kafka:inbound-channel-adapter</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">bean</span> <span class="hljs-attribute">id</span>=<span class="hljs-value">"consumerProperties"</span>
        <span class="hljs-attribute">class</span>=<span class="hljs-value">"org.springframework.beans.factory.config.PropertiesFactoryBean"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"properties"</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">props</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">prop</span> <span class="hljs-attribute">key</span>=<span class="hljs-value">"auto.offset.reset"</span>&gt;</span>smallest<span class="hljs-tag">&lt;/<span class="hljs-title">prop</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">prop</span> <span class="hljs-attribute">key</span>=<span class="hljs-value">"socket.receive.buffer.bytes"</span>&gt;</span>10485760<span class="hljs-tag">&lt;/<span class="hljs-title">prop</span>&gt;</span> <span class="hljs-comment">&lt;!-- 10M --&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">prop</span> <span class="hljs-attribute">key</span>=<span class="hljs-value">"fetch.message.max.bytes"</span>&gt;</span>5242880<span class="hljs-tag">&lt;/<span class="hljs-title">prop</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">prop</span> <span class="hljs-attribute">key</span>=<span class="hljs-value">"auto.commit.interval.ms"</span>&gt;</span>1000<span class="hljs-tag">&lt;/<span class="hljs-title">prop</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">props</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">bean</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">int-kafka:consumer-context</span> <span class="hljs-attribute">id</span>=<span class="hljs-value">"consumerContext"</span>
        <span class="hljs-attribute">consumer-timeout</span>=<span class="hljs-value">"4000"</span> <span class="hljs-attribute">zookeeper-connect</span>=<span class="hljs-value">"zookeeperConnect"</span> <span class="hljs-attribute">consumer-properties</span>=<span class="hljs-value">"consumerProperties"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">int-kafka:consumer-configurations</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">int-kafka:consumer-configuration
</span>                <span class="hljs-attribute">group-id</span>=<span class="hljs-value">"mygroup"</span> <span class="hljs-attribute">max-messages</span>=<span class="hljs-value">"5000"</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">int-kafka:topic</span> <span class="hljs-attribute">id</span>=<span class="hljs-value">"test"</span> <span class="hljs-attribute">streams</span>=<span class="hljs-value">"4"</span> /&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">int-kafka:consumer-configuration</span>&gt;</span>
            <span class="hljs-comment">&lt;!-- &lt;int-kafka:consumer-configuration group-id="default3" value-decoder="kafkaSpecificDecoder" 
                key-decoder="kafkaReflectionDecoder" max-messages="10"&gt; &lt;int-kafka:topic-filter 
                pattern="regextopic.*" streams="4" exclude="false" /&gt; &lt;/int-kafka:consumer-configuration&gt; --&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">int-kafka:consumer-configurations</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">int-kafka:consumer-context</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">int-kafka:zookeeper-connect</span> <span class="hljs-attribute">id</span>=<span class="hljs-value">"zookeeperConnect"</span>
        <span class="hljs-attribute">zk-connect</span>=<span class="hljs-value">"localhost:2181"</span> <span class="hljs-attribute">zk-connection-timeout</span>=<span class="hljs-value">"6000"</span>
        <span class="hljs-attribute">zk-session-timeout</span>=<span class="hljs-value">"400"</span> <span class="hljs-attribute">zk-sync-time</span>=<span class="hljs-value">"200"</span> /&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">beans</span>&gt;</span></code></pre>

<p>这个配置和Producer类似， 同样声明一个channel, 定义inbound-channel-adapter, 它引用Bean kafka-consumer-context, <br>
kafka-consumer-context定义了消费者的列表。 consumer-configuration还提供了topic-filter，使用正则表达式建立白名单或者黑名单(exclude属性)。</p>

<p>消费者上下文还需要zookeeper-connect。</p>

<p>由于spring-integration-kafka只实现了high level Consumer API,这也就意味着你不可能回滚重新查看以前的消息， 因为high level API不提供offset管理。</p>

<p>注意Channel中得到的有效负载的类型是： <br>
Map</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>