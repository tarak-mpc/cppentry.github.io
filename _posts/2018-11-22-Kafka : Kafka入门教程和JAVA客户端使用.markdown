---
layout:     post
title:      Kafka : Kafka入门教程和JAVA客户端使用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/shangmingtao/article/details/79567921				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="目录">目录</h1>

<p></p><div class="toc"><div class="toc">
<ul>
<li><a href="#%E7%9B%AE%E5%BD%95" rel="nofollow">目录</a></li>
<li><a href="#kafka%E7%AE%80%E4%BB%8B" rel="nofollow">Kafka简介</a></li>
<li><a href="#%E7%8E%AF%E5%A2%83%E4%BB%8B%E7%BB%8D" rel="nofollow">环境介绍</a></li>
<li><a href="#%E6%9C%AF%E8%AF%AD%E4%BB%8B%E7%BB%8D" rel="nofollow">术语介绍</a></li>
<li><a href="#%E6%B6%88%E8%B4%B9%E6%A8%A1%E5%BC%8F" rel="nofollow">消费模式</a></li>
<li><a href="#%E4%B8%8B%E8%BD%BD" rel="nofollow">下载</a></li>
<li><a href="#%E9%9B%86%E7%BE%A4%E5%AE%89%E8%A3%85%E9%85%8D%E7%BD%AE" rel="nofollow">集群安装配置</a></li>
<li><a href="#%E5%91%BD%E4%BB%A4%E4%BD%BF%E7%94%A8" rel="nofollow">命令使用</a></li>
<li><a href="#java%E5%AE%9E%E6%88%98" rel="nofollow">JAVA实战</a></li>
<li><a href="#%E5%8F%82%E8%80%83%E6%96%87%E7%8C%AE" rel="nofollow">参考文献</a></li>
</ul>
</div>
</div>




<h1 id="kafka简介">Kafka简介</h1>

<p>由Scala和Java编写,Kafka是一种高吞吐量的分布式发布订阅消息系统.</p>



<h1 id="环境介绍">环境介绍</h1>

<p>操作系统:centos6.5 <br>
kafka:1.0.1 <br>
zookeeper:3.4.6</p>



<h1 id="术语介绍">术语介绍</h1>

<ul>
<li>Broker : Kafka集群包含一个或多个服务器，这种服务器被称为broker</li>
<li>Topic : 每条发布到Kafka集群的消息都有一个类别，这个类别被称为Topic。（物理上不同Topic的消息分开存储，逻辑上一个Topic的消息虽然保存于一个或多个broker上但用户只需指定消息的Topic即可生产或消费数据而不必关心数据存于何处）</li>
<li>Partition : Partition是物理上的概念，每个Topic包含一个或多个Partition.</li>
<li>Producer : 负责发布消息到Kafka broker</li>
<li>Consumer : 消息消费者，向Kafka broker读取消息的客户端。</li>
<li>Consumer Group : 每个Consumer属于一个特定的Consumer Group（可为每个Consumer指定group name，若不指定group name则属于默认的group）。</li>
</ul>



<h1 id="消费模式">消费模式</h1>

<p><img src="//img-blog.csdn.net/20180315151540326?watermark/2/text/Ly9ibG9nLmNzZG4ubmV0L3NoYW5nbWluZ3Rhbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>为了照顾对MQ不是很了解的同学,先讲一下MQ的原理.一般MQ都是在服务端存储一个队列.生产者把消息丢到MQ server,消费者从MQ server消费.这样一来解决了生产者和消费者的高耦合问题,同时也解决了生产速度和消费速度差异导致的消费者跟不上生产者的生产速度而导致的消费者压力过大问题.</p>

<p>在kafka中的topic就是一系列队列的总称,称为一个主题.当然ActiveMQ和RabbitMQ中都有这个概念.一类消息都会丢到一个topic中去.</p>

<p>讲完topic我们讲一下partition(分区),这个东西是kafka独有的东西,也是kafka实现横向扩展和高并发的一个重要设计.我们试想一下,如果每个topic只有一个队列,随着业务增加topic里消息越来越多.多到一台server装不下了怎么办.为了解决这个问题,我们引入了partition这个概念.一个partition(分区)代表了一个物理上存在的队列.topic只是一组partition(分区)的总称,也就是说topic仅是逻辑上的概念.这样一来当topic上的消息越来越多.我们就可以将新增的partition(分区)放在其他server上.也就是说topic里边的partition(分区)可以分属于不同的机器.实际生产中,也基本都是这样玩的.</p>

<p>这里说一个特殊情况,有时我们创建了一个topic没有指定partition(分区)数量或者指定了partition(分区)数量为1,这时实际也是有一个默认的partition(分区)的,名字我忘记了.</p>

<p>从Producer(生产者)角度,一个消息丢到topic中任务就完成了.至于具体丢到了topic中的哪个partition(分区),Producer(生产者)不需要关注.这里kafka自动帮助我们做了负载均衡.当然如果我们指定某个partition(分区)也是可以的.这个大家官方文档和百度.</p>

<p>接下里我们讲Consumer Group(消费组),Consumer Group(消费组)顾名思义就是一组Consumer(消费者)的总称.那有了组的概念以后能起到什么作用.如果只有一组内且组内只有一个Consumer,那这个就是传统的点对点模式,如果有多组,每组内都有一个Consumer,那这个就是发布-订阅(pub-sub)模式.每组都会收到同样的消息.</p>

<p>最后讲最难理解也是大家讨论最多的地方,partition(分区)和Consumer(消费者)的关系.首先,<strong>一个Consumer(消费者)的一个线程在某个时刻只能接收一个partition(分区)的数据,一个partition(分区)某个时刻也只会把消息发给一个Consumer(消费者)</strong>.我们设计出来几种场景:</p>

<p><strong>场景一:</strong> topic-1 下有partition-1和partition-2 <br>
            group-1 下有consumer-1和consumer-2和consumer-3 <br>
            所有consumer只有一个线程,且都消费topic-1的消息. <br>
            <strong>消费情况 :</strong> consumer-1只消费partition-1的数据 <br>
                           consumer-2只消费partition-2的数据 <br>
                       consumer-3不会消费到任何数据 <br>
        <strong>原因 :</strong> 只能接受一个partition(分区)的数据</p>

<p><strong>场景二:</strong> topic-1 下有partition-1和partition-2 <br>
            group-1 下有consumer-1 <br>
            consumer只有一个线程,且消费topic-1的消息. <br>
            <strong>消费情况 :</strong> consumer-1先消费partition-1的数据 <br>
                           consumer-1消费完partition-1数据后开始消费partition-2的数据 <br>
        <strong>原因 :</strong> 这里是kafka检测到当前consumer-1消费完partition-1处于空闲状态,自动帮我做了负载.所以大家看到这里在看一下上边那句话的”某个时刻” <br>
       <strong>特例:</strong> consumer在消费消息时必须指定topic,可以不指定partition,场景二的情况就是发生在不指定partition的情况下,如果consumer-1指定了partition-1,那么consumer-1消费完partition-1后哪怕处于空闲状态了也是不会消费partition-2的消息的.</p>

<p>进而我们总结出了一条经验,同组内的消费者(单线程消费)数量不应多于topic下的partition(分区)数量,不然就会出有消费者空闲的状态,此时并发线程数=partition(分区)数量.反之消费者数量少于topic下的partition(分区)数量也是不理想的,原因是此时并发线程数=消费者数量,并不能完全发挥kafka并发效率.</p>

<p>最后我们看下上边的图,Consumer Group A的两个机器分别开启两个线程消费P0 P1 P2 P3的消息Consumer Group B的四台机器单线程消费P0 P1 P2 P3的消息就可以了.此时效率最高.</p>

<h1 id="下载">下载</h1>

<p>下载地址:<a href="http://kafka.apache.org/downloads" rel="nofollow">http://kafka.apache.org/downloads</a> <br>
这里我们下载到<code>/usr/local</code>目录下</p>



<h1 id="集群安装配置">集群安装配置</h1>

<p><strong>解压 :</strong> <code>cd /usr/local &amp;&amp; tar -xzvf kafka_2.11-1.0.1.tgz</code></p>

<p><strong>创建log目录 :</strong> <code>cd /usr/local/kafka_2.11-1.0.1 &amp;&amp; mkdir kafkaLogs</code></p>

<p><strong>配置:</strong><code>vi /usr/local/kafka_2.11-1.0.1/config/server.properties</code>需改下边五个地方</p>

<pre class="prettyprint"><code class="language-bash hljs "><span class="hljs-comment">#broker的id,集群中的每台机器id唯一,其他两台分别1和2</span>
broker.id=<span class="hljs-number">0</span>  
<span class="hljs-comment">#是Kafka绑定的interface,这里需要写本机内网ip地址,不然bind端口失败</span>
<span class="hljs-comment">#其他两台分别是192.168.1.5和192.168.1.9</span>
host.name=<span class="hljs-number">192.168</span>.<span class="hljs-number">1.3</span> 
<span class="hljs-comment">#向zookeeper注册的对外暴露的ip和port,118.212.149.51是192.168.1.3的外网ip地址</span>
<span class="hljs-comment">#如果不配置kafka部署在外网服务器的话本地是访问不到的.</span>
advertised.listeners=PLAINTEXT://<span class="hljs-number">118.212</span>.<span class="hljs-number">149.51</span>:<span class="hljs-number">9092</span> 
<span class="hljs-comment">#zk集群的ip和port,zk集群教程:</span>
zookeeper.connect=<span class="hljs-number">192.168</span>.<span class="hljs-number">1.3</span>:<span class="hljs-number">2181</span>,<span class="hljs-number">192.168</span>.<span class="hljs-number">1.5</span>:<span class="hljs-number">2181</span>,<span class="hljs-number">192.168</span>.<span class="hljs-number">1.9</span>:<span class="hljs-number">2181</span>
<span class="hljs-comment">#log目录,刚刚上边建好的.</span>
log.dirs=/usr/local/kafka_2.<span class="hljs-number">11</span>-<span class="hljs-number">1.0</span>.<span class="hljs-number">1</span>/kafkaLogs</code></pre>

<p><strong>启动集群(分别在三台broker执行):</strong>进入bin目录<code>cd /usr/local/kafka_2.11-1.0.1/bin/</code>执行启动脚本并指定配置文件<code>./kafka-server-start.sh -daemon ../config/server.properties</code></p>

<p><strong>验证集群是否启动成功:</strong></p>

<pre class="prettyprint"><code class=" hljs r">[root@template ~]<span class="hljs-comment"># cd /usr/local/zookeeper-3.4.6/bin/</span>
[root@template bin]<span class="hljs-comment"># ./zkCli.sh -server 127.0.0.1:2181</span>
<span class="hljs-keyword">...</span>
[zk: <span class="hljs-number">127.0</span><span class="hljs-number">.0</span><span class="hljs-number">.1</span>:<span class="hljs-number">2181</span>(CONNECTED) <span class="hljs-number">0</span>] ls /brokers/ids
[<span class="hljs-number">0</span>, <span class="hljs-number">1</span>, <span class="hljs-number">2</span>] <span class="hljs-comment">#这里的012分别是三个broker的id</span></code></pre>

<p><strong>查看某个broker信息:</strong>注意<code>endpoints</code>信息的ip:port,这个就是我们对外服务暴露的地址,我这里是外网访问,所以暴露的是外网ip和端口</p>

<pre class="prettyprint"><code class="language-bash hljs ">[zk: <span class="hljs-number">127.0</span>.<span class="hljs-number">0.1</span>:<span class="hljs-number">2181</span>(CONNECTED) <span class="hljs-number">1</span>] get /brokers/ids/<span class="hljs-number">0</span>
{<span class="hljs-string">"listener_security_protocol_map"</span>:{<span class="hljs-string">"PLAINTEXT"</span>:<span class="hljs-string">"PLAINTEXT"</span>},<span class="hljs-string">"endpoints"</span>:[<span class="hljs-string">"PLAINTEXT://118.212.149.51:9092"</span>],<span class="hljs-string">"jmx_port"</span>:-<span class="hljs-number">1</span>,<span class="hljs-string">"host"</span>:<span class="hljs-string">"118.212.149.51"</span>,<span class="hljs-string">"timestamp"</span>:<span class="hljs-string">"1521010377533"</span>,<span class="hljs-string">"port"</span>:<span class="hljs-number">9092</span>,<span class="hljs-string">"version"</span>:<span class="hljs-number">4</span>}
cZxid = <span class="hljs-number">0</span>x700000626
ctime = Wed Mar <span class="hljs-number">14</span> <span class="hljs-number">14</span>:<span class="hljs-number">52</span>:<span class="hljs-number">57</span> CST <span class="hljs-number">2018</span>
mZxid = <span class="hljs-number">0</span>x700000626
mtime = Wed Mar <span class="hljs-number">14</span> <span class="hljs-number">14</span>:<span class="hljs-number">52</span>:<span class="hljs-number">57</span> CST <span class="hljs-number">2018</span>
pZxid = <span class="hljs-number">0</span>x700000626
cversion = <span class="hljs-number">0</span>
dataVersion = <span class="hljs-number">0</span>
aclVersion = <span class="hljs-number">0</span>
ephemeralOwner = <span class="hljs-number">0</span>x3621e366ae20014
dataLength = <span class="hljs-number">198</span>
numChildren = <span class="hljs-number">0</span></code></pre>



<h1 id="命令使用">命令使用</h1>

<p><strong>创建topic : </strong></p>

<pre class="prettyprint"><code class="language-bash hljs "><span class="hljs-comment">#--replication-factor 创建的副本数,这个使用来备份的.副本数不能大于broker数</span>
<span class="hljs-comment">#--partitions 1 创建的分区数.根据实际情况创建</span>
./kafka-topics.sh --create --zookeeper <span class="hljs-number">192.168</span>.<span class="hljs-number">1.3</span>:<span class="hljs-number">2181</span> --replication-factor <span class="hljs-number">1</span> --partitions <span class="hljs-number">1</span> --topic milo</code></pre>

<p><strong>查看topic :</strong> </p>

<pre class="prettyprint"><code class="language-bash hljs ">./kafka-topics.sh --list --zookeeper <span class="hljs-number">192.168</span>.<span class="hljs-number">1.3</span>:<span class="hljs-number">2181</span></code></pre>

<p><strong>查看topic详细信息 :</strong> </p>

<pre class="prettyprint"><code class="language-bash hljs ">./kafka-topics.sh --describe --zookeeper <span class="hljs-number">192.168</span>.<span class="hljs-number">1.3</span>:<span class="hljs-number">2181</span></code></pre>

<p>结果如下: <br>
<img src="//img-blog.csdn.net/2018031516135553?watermark/2/text/Ly9ibG9nLmNzZG4ubmV0L3NoYW5nbWluZ3Rhbw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
第一行topic信息摘要:分别是topic名字(Topic),partition数量(PartitionCount),副本数量(ReplicationFactor),配置(Config) <br>
第二行~第四行分别列出了名为milo的topic的所有partition.依次为topic名字(Topic),partition号(Partition),此partition所在的borker(Leader),副本所在的broker(Replicas),Isr列表(Isr) <br>
ps:同步状态的副本的集合（a set of in-sync replicas），简称ISR,通俗理解就是替补队员,不是每个broker都可以作为替补队员.首先这个broker得存有副本,其次副本还得满足条件.就像我们大学足球队,有的人是替补,有的人连大名单都没进去,原因是他不会踢球. ^ ^ </p>

<p><strong>生产消息 :</strong> </p>

<pre class="prettyprint"><code class="language-bash hljs ">./kafka-console-producer.sh --broker-list <span class="hljs-number">118.212</span>.<span class="hljs-number">149.51</span>:<span class="hljs-number">9092</span> --topic test\
&gt;hello world</code></pre>

<p><strong>消费消息 :</strong> </p>

<pre class="prettyprint"><code class="language-bash hljs ">./kafka-console-consumer.sh --zookeeper <span class="hljs-number">118.212</span>.<span class="hljs-number">149.51</span>:<span class="hljs-number">2181</span> --topic milo --from-beginning
hello world</code></pre>



<h1 id="java实战">JAVA实战</h1>

<p>pom.xml</p>



<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-tag">&lt;<span class="hljs-title">dependencies</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.kafka<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>kafka_2.11<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.0.1<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.kafka<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>kafka-clients<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.0.1<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
  <span class="hljs-tag">&lt;/<span class="hljs-title">dependencies</span>&gt;</span></code></pre>

<p>Producer.java</p>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">package</span> cn.milo.kafka;

<span class="hljs-keyword">import</span> org.apache.kafka.clients.producer.*;
<span class="hljs-keyword">import</span> org.apache.kafka.common.serialization.StringSerializer;
<span class="hljs-keyword">import</span> org.apache.log4j.Logger;

<span class="hljs-keyword">import</span> java.util.Properties;

<span class="hljs-javadoc">/******************************************************
 ******<span class="hljs-javadoctag"> @ClassName</span>   : Producer.java                                            
 ******<span class="hljs-javadoctag"> @author</span>      : milo ^ ^                     
 ******<span class="hljs-javadoctag"> @date</span>        : 2018 03 14 11:34     
 ******<span class="hljs-javadoctag"> @version</span>     : v1.0.x                      
 *******************************************************/</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">Producer</span> {</span>

    <span class="hljs-keyword">static</span> Logger log = Logger.getLogger(Producer.class);

    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> String TOPIC = <span class="hljs-string">"milo2"</span>;
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> String BROKER_LIST = <span class="hljs-string">"118.212.149.51:9092"</span>;
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> KafkaProducer&lt;String,String&gt; producer = <span class="hljs-keyword">null</span>;

    <span class="hljs-comment">/*
    初始化生产者
     */</span>
    <span class="hljs-keyword">static</span> {
        Properties configs = initConfig();
        producer = <span class="hljs-keyword">new</span> KafkaProducer&lt;String, String&gt;(configs);
    }

    <span class="hljs-comment">/*
    初始化配置
     */</span>
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> Properties <span class="hljs-title">initConfig</span>(){
        Properties properties = <span class="hljs-keyword">new</span> Properties();
        properties.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG,BROKER_LIST);
        properties.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        properties.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG,StringSerializer.class.getName());
        <span class="hljs-keyword">return</span> properties;
    }

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> InterruptedException {
        <span class="hljs-comment">//消息实体</span>
        ProducerRecord&lt;String , String&gt; record = <span class="hljs-keyword">null</span>;
        <span class="hljs-keyword">for</span> (<span class="hljs-keyword">int</span> i = <span class="hljs-number">0</span>; i &lt; <span class="hljs-number">1000</span>; i++) {
            record = <span class="hljs-keyword">new</span> ProducerRecord&lt;String, String&gt;(TOPIC, <span class="hljs-string">"value"</span>+(<span class="hljs-keyword">int</span>)(<span class="hljs-number">10</span>*(Math.random())));
            <span class="hljs-comment">//发送消息</span>
            producer.send(record, <span class="hljs-keyword">new</span> Callback() {
                <span class="hljs-annotation">@Override</span>
                <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">onCompletion</span>(RecordMetadata recordMetadata, Exception e) {
                    <span class="hljs-keyword">if</span> (<span class="hljs-keyword">null</span> != e){
                        log.info(<span class="hljs-string">"send error"</span> + e.getMessage());
                    }<span class="hljs-keyword">else</span> {
                        System.out.println(String.format(<span class="hljs-string">"offset:%s,partition:%s"</span>,recordMetadata.offset(),recordMetadata.partition()));
                    }
                }
            });
        }
        producer.close();
    }
}
</code></pre>

<p>Consumer : </p>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">package</span> cn.milo.kafka;

<span class="hljs-keyword">import</span> org.apache.kafka.clients.consumer.ConsumerRecord;
<span class="hljs-keyword">import</span> org.apache.kafka.clients.consumer.ConsumerRecords;
<span class="hljs-keyword">import</span> org.apache.kafka.clients.consumer.KafkaConsumer;
<span class="hljs-keyword">import</span> org.apache.log4j.Logger;

<span class="hljs-keyword">import</span> java.util.Properties;

<span class="hljs-javadoc">/******************************************************
 ******<span class="hljs-javadoctag"> @ClassName</span>   : Consumer.java                                            
 ******<span class="hljs-javadoctag"> @author</span>      : milo ^ ^                     
 ******<span class="hljs-javadoctag"> @date</span>        : 2018 03 14 15:50     
 ******<span class="hljs-javadoctag"> @version</span>     : v1.0.x                      
 *******************************************************/</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">Consumer</span> {</span>

    <span class="hljs-keyword">static</span> Logger log = Logger.getLogger(Producer.class);

    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> String TOPIC = <span class="hljs-string">"milo2"</span>;
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> String BROKER_LIST = <span class="hljs-string">"118.212.149.51:9092"</span>;
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> KafkaConsumer&lt;String,String&gt; consumer = <span class="hljs-keyword">null</span>;

    <span class="hljs-keyword">static</span> {
        Properties configs = initConfig();
        consumer = <span class="hljs-keyword">new</span> KafkaConsumer&lt;String, String&gt;(configs);
    }

    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> Properties <span class="hljs-title">initConfig</span>(){
        Properties properties = <span class="hljs-keyword">new</span> Properties();
        properties.put(<span class="hljs-string">"bootstrap.servers"</span>,BROKER_LIST);
        properties.put(<span class="hljs-string">"group.id"</span>,<span class="hljs-string">"0"</span>);
        properties.put(<span class="hljs-string">"key.deserializer"</span>, <span class="hljs-string">"org.apache.kafka.common.serialization.StringDeserializer"</span>);
        properties.put(<span class="hljs-string">"value.deserializer"</span>, <span class="hljs-string">"org.apache.kafka.common.serialization.StringDeserializer"</span>);
        properties.setProperty(<span class="hljs-string">"enable.auto.commit"</span>, <span class="hljs-string">"true"</span>);
        properties.setProperty(<span class="hljs-string">"auto.offset.reset"</span>, <span class="hljs-string">"earliest"</span>);
        <span class="hljs-keyword">return</span> properties;
    }


    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) {
        <span class="hljs-keyword">while</span> (<span class="hljs-keyword">true</span>) {
            ConsumerRecords&lt;String, String&gt; records = consumer.poll(<span class="hljs-number">10</span>);
            <span class="hljs-keyword">for</span> (ConsumerRecord&lt;String, String&gt; record : records) {
                log.info(record);
            }
        }
    }
}</code></pre>



<h1 id="参考文献">参考文献</h1>

<p>[1].kafka 学习 非常详细的经典教程 : <a href="http://blog.csdn.net/tangdong3415/article/details/53432166" rel="nofollow">http://blog.csdn.net/tangdong3415/article/details/53432166</a> <br>
[2].Kafka入门与实践.牟大恩</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>