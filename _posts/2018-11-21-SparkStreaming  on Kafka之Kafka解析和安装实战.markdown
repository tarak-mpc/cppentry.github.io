---
layout:     post
title:      SparkStreaming  on Kafka之Kafka解析和安装实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/snail_gesture/article/details/51344369				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>本篇博文将从以下方面组织内容： <br>
        1. Kafka解析 <br>
        2. 消息组件Kafka <br>
        3. Kafka安装 <br>
实验搭建所需要的软件： <br>
        kafka_2.10-0.9.0.1 <br>
        Zookeeper集群已经安装好。在上一篇博文有安装步骤，不清楚的朋友可以参考下。 <br>
<strong>一：Kafka解析</strong> <br>
1.  Kafka是生产者和消费者模式中广播概念，Kafka也可以实现队列的方式。 <br>
2.  Kafka不仅是一个消息中间键，还是一个存储系统，可以将流进来的数据存储一段时间。这就与传统的流式处理不一样，传统的流式处理处理完数据之后就消失了(指的是消息和流的角度)，但是如果通过Kafka方式可以数据持久化到磁盘上，这就为我们很多功能的实现打下了很好的基础，这也就形成了比较完善的流式处理系统。 <br>
3.  完善的流式处理系统：在线低延迟处理数据，稳定可靠的，可以对流进来的数据进行非常复杂的逻辑分析，不仅可以处理当前在线的数据，也能处理过去一天或者一周的数据。 <br>
<strong>二：消息组件Kafka</strong> <br>
1.  什么消息组件？A与B通信，中间需要组件，需要的这个组件就是中间键。 <br>
Kafka架构图如下： <br>
<img src="https://img-blog.csdn.net/20160508095140420" alt="这里写图片描述" title=""> <br>
Kafka的生产者和消费者比较特殊。 <br>
Producer: 也就是数据的来源，数据可能来自于数据库，来自于Server服务器，或者说比如安卓端收集用户行为的数据。Producer中的数据是推到Kafka Cluster，例如别人给你发邮件，这个邮件是别人推过来的，当别人send的时候就是一个push的过程，转过来我要消费的时候就是一个poll的过程。 <br>
<img src="https://img-blog.csdn.net/20160508095250984" alt="这里写图片描述" title=""> <br>
Topic：主题，代表了数据的类别或者是类型，生产者在放数据的时候就需要说明将自己放到是什么Topic中，Topic为了方便对数据的管理提出的一种抽象，本质就是一个对象。 <br>
<img src="https://img-blog.csdn.net/20160508095359909" alt="这里写图片描述" title=""> <br>
Group: 对于一个消息而言，在一个group中只能有一个消费实体，Group内部的实体，是互斥的(C1，C2是互斥的)，这样做的好处就是，假设有一个Consumer(消费者)搞了很多线程，一个线程去抓取数据就可以了，不需要多条线程重复去抓取，上图是广播模式。</p>

<p><img src="https://img-blog.csdn.net/20160508095916130" alt="这里写图片描述" title=""> <br>
3. Kafka的数据传输是基于内核级(zero-copy)别的，Kafka是使用Scala写的，基于JVM虚拟机的，是基于JVM虚拟机的。 <br>
普通传输数据的方式：如果要传输一个数据，首先将数据从磁盘读到内核，再从内核读到用户空间(JVM)，然后将数据传给其他机器的话，此时应用程序再从用户空间拷贝到内核，然后在从内核拷贝到网络上，这样的话数据前后就拷贝了四次。 <br>
Kafka：Kafka是基于Kernal级别的，没有用户空间的参与，当然用户空间会发起调用，但是数据并不传说到用户空间，所以这个时候如果消费数据的话例如上面图P0这个数据直接在内核中然后通过网络传给C1和C3,只需要内核态的参与，由于没有用户态的参与，这样性能得到了极大的提升。</p>

<ol>
<li>Kafka作为一个消息中间键，无限量的存储数据，只要磁盘足够大，Kafka中的数据是存储在磁盘中。 <br>
Kafka中的数据放入到磁盘中的好处：</li>
<li>多份备份。</li>
<li>速度非常快。</li>
<li>采用Zookeeper去管理元数据。</li>
<li>作为一个消息中间键，顺序写，相当于文件在背后一直在追加的方式，然后消息具体在那会有一个元数据，这个元数据会被保存在Zookeeper中，Zookeeper目前又是事实上一致性的最佳选择。</li>
</ol>

<p><strong>三：Kafka安装</strong> <br>
1.  到<a href="http://kafka.apache.org/downloads.html" rel="nofollow">http://kafka.apache.org/downloads.html</a>下载最新的Kafka版本，不过这里要注意的是有对应Scala版本。这里我选择第一个Scala2.10这个。 <br>
<img src="https://img-blog.csdn.net/20160508095737692" alt="这里写图片描述" title=""> <br>
3.  安装Zookeeper，这里已经安装过了,如果不清楚请您查看上一篇博文。</p>



<pre class="prettyprint"><code class=" hljs ruby">dataDir=<span class="hljs-regexp">/usr/local</span><span class="hljs-regexp">/spark/zookeeper</span>-<span class="hljs-number">3.4</span>.<span class="hljs-number">6</span>/data
dataLogDir=<span class="hljs-regexp">/usr/local</span><span class="hljs-regexp">/spark/zookeeper</span>-<span class="hljs-number">3.4</span>.<span class="hljs-number">6</span>/logs
server.<span class="hljs-number">0</span>=<span class="hljs-constant">Master</span><span class="hljs-symbol">:</span><span class="hljs-number">2888</span><span class="hljs-symbol">:</span><span class="hljs-number">3888</span> 
server.<span class="hljs-number">1</span>=<span class="hljs-constant">Master</span><span class="hljs-symbol">:</span><span class="hljs-number">2888</span><span class="hljs-symbol">:</span><span class="hljs-number">3888</span>
server.<span class="hljs-number">2</span>=<span class="hljs-constant">Master</span><span class="hljs-symbol">:</span><span class="hljs-number">2888</span><span class="hljs-symbol">:</span><span class="hljs-number">3888</span>
</code></pre>

<pre><code>4.  将slf4j-nop-1.7.6.jar拷贝到Kafka的libs目录下。
</code></pre>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@Master</span> slf4j-<span class="hljs-number">1.7</span>.<span class="hljs-number">6</span>]<span class="hljs-comment"># cp slf4j-nop-1.7.6.jar /root/Desktop/kafka_2.10-0.9.0.1/libs/</span></code></pre>

<pre><code>5.  将Kafka安装到local目录下。
</code></pre>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@Master</span> local]<span class="hljs-comment"># ls</span>
bin      games   hive     kafka_2.<span class="hljs-number">10</span>-<span class="hljs-number">0</span>.<span class="hljs-number">9.0</span>.<span class="hljs-number">1</span>  libexec  share
eclipse  hadoop  <span class="hljs-keyword">include</span>  lib                 sbin     spark
etc      hbase   jdk      lib64               scala    src
</code></pre>

<pre><code>6.  配置Bashrc。
</code></pre>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-keyword">export</span> KAKFKA_HOME=/usr/local/kafka_2.<span class="hljs-number">10</span>-<span class="hljs-number">0.9</span>.<span class="hljs-number">0.1</span>
<span class="hljs-keyword">export</span> PATH=/usr/local/eclipse/eclipse:/usr/local/idea/idea-IC-<span class="hljs-number">141.1532</span>.<span class="hljs-number">4</span>/bin:<span class="hljs-variable">${MAVEN_HOME}</span>/bin:<span class="hljs-variable">${FLUME_HOME}</span>/bin:<span class="hljs-variable">${SPARK_HOME}</span>/bin:<span class="hljs-variable">${SPARK_HOME}</span>/sbin/sbin::<span class="hljs-variable">${SCALA_HOME}</span>/bin:<span class="hljs-variable">${JAVA_HOME}</span>/bin:<span class="hljs-variable">${HADOOP_HOME}</span>/bin:<span class="hljs-variable">${HADOOP_HOME}</span>/sbin:<span class="hljs-variable">${HIVE_HOME}</span>/bin:<span class="hljs-variable">${ZOOKEEPER_HOME}</span>/bin:<span class="hljs-variable">${KAKFKA_HOME}</span>/bin:<span class="hljs-variable">$PATH</span>
</code></pre>

<pre><code>7.  将bashrc文件传送到Worker1和Worker2.
</code></pre>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@Master</span> local]<span class="hljs-comment"># scp ~/.bashrc root<span class="hljs-yardoctag">@Worker</span>1:~/.bashrc</span>
.bashrc                                                                                                                               <span class="hljs-number">100</span>% <span class="hljs-number">1236</span>     <span class="hljs-number">1.2</span>KB/s   <span class="hljs-number">00</span><span class="hljs-symbol">:</span><span class="hljs-number">00</span>    
[root<span class="hljs-variable">@Master</span> local]<span class="hljs-comment"># scp ~/.bashrc root<span class="hljs-yardoctag">@Worker</span>2:~/.bashrc</span>
.bashrc                                                                                                                               <span class="hljs-number">100</span>% <span class="hljs-number">1236</span>     <span class="hljs-number">1.2</span>KB/s   <span class="hljs-number">00</span><span class="hljs-symbol">:</span><span class="hljs-number">00</span>
</code></pre>

<pre><code>8.  配置Kafka中的service.properties
</code></pre>



<pre class="prettyprint"><code class=" hljs coffeescript"><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">##### Server Basics ###</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">##</span>
<span class="hljs-regexp">//</span>这个参数就是作为Kafka集群实体的id.
<span class="hljs-comment"># The id of the broker. This must be set to a unique integer for each broker.</span>
broker.id=<span class="hljs-number">0</span>

<span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">##### Zookeeper ###</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">##</span>

<span class="hljs-comment"># Zookeeper connection string (see zookeeper docs for details).</span>
<span class="hljs-comment"># This is a comma separated host:port pairs, each corresponding to a zk</span>
<span class="hljs-comment"># server. e.g. "127.0.0.1:3000,127.0.0.1:3001,127.0.0.1:3002".</span>
<span class="hljs-comment"># You can also append an optional chroot string to the urls to specify the</span>
<span class="hljs-comment"># root directory for all kafka znodes.</span>
<span class="hljs-regexp">//</span>zookeeper的默认端口是<span class="hljs-number">2181</span>
zookeeper.connect=<span class="hljs-attribute">Master</span>:<span class="hljs-number">2181</span>,<span class="hljs-attribute">Worker1</span>:<span class="hljs-number">2181</span>,<span class="hljs-attribute">Worker2</span>:<span class="hljs-number">2181</span>

<span class="hljs-comment"># Timeout in ms for connecting to zookeeper</span>
zookeeper.connection.timeout.ms=<span class="hljs-number">6000</span>
</code></pre>

<pre><code>9.  将Kafka中的配置拷贝到Worker1和Worker2节点上。
</code></pre>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@Master</span> local]<span class="hljs-comment"># scp -r ./kafka_2.10-0.9.0.1/ root<span class="hljs-yardoctag">@Worker</span>1:/usr/local/</span>

[root<span class="hljs-variable">@Master</span> local]<span class="hljs-comment"># scp -r ./kafka_2.10-0.9.0.1/ root<span class="hljs-yardoctag">@Worker</span>2:/usr/local/</span>
</code></pre>

<pre><code>10. 修改Worker1的server.properties
</code></pre>

<p>Worker1:</p>



<pre class="prettyprint"><code class=" hljs coffeescript"><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">##### Server Basics ###</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">##</span>

<span class="hljs-comment"># The id of the broker. This must be set to a unique integer for each broker.</span>
broker.id=<span class="hljs-number">1</span>
</code></pre>

<p>Worker2:</p>



<pre class="prettyprint"><code class=" hljs coffeescript"><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">##### Server Basics ###</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">######</span><span class="hljs-comment">##</span>

<span class="hljs-comment"># The id of the broker. This must be set to a unique integer for each broker.</span>
broker.id=<span class="hljs-number">2</span>
</code></pre>

<ol>
<li>启动Kafka。</li>
</ol>



<pre class="prettyprint"><code class=" hljs perl">//Master节点上Kafka启动成功。
[root<span class="hljs-variable">@Master</span> bin]<span class="hljs-comment"># ./kafka-server-start.sh ../config/server.properties &amp;</span>

[root<span class="hljs-variable">@Master</span> sbin]<span class="hljs-comment"># jps</span>
<span class="hljs-number">4789</span> Master
<span class="hljs-number">4134</span> NameNode
<span class="hljs-number">5064</span> Jps
<span class="hljs-number">4745</span> QuorumPeerMain
<span class="hljs-number">5005</span> Kafka

//Worker1节点上Kafka启动成功。
[root<span class="hljs-variable">@Worker1</span> bin]<span class="hljs-comment"># ./kafka-server-start.sh ../config/server.properties &amp;</span>

[root<span class="hljs-variable">@Master</span> sbin]<span class="hljs-comment"># jps</span>
<span class="hljs-number">4789</span> Master
<span class="hljs-number">4134</span> NameNode
<span class="hljs-number">5064</span> Jps
<span class="hljs-number">4745</span> QuorumPeerMain
<span class="hljs-number">5005</span> Kafka

//Worker2节点上Kafka启动成功。
[root<span class="hljs-variable">@Worker1</span> bin]<span class="hljs-comment"># ./kafka-server-start.sh ../config/server.properties &amp;</span>

[root<span class="hljs-variable">@Master</span> sbin]<span class="hljs-comment"># jps</span>
<span class="hljs-number">4789</span> Master
<span class="hljs-number">4134</span> NameNode
<span class="hljs-number">5064</span> Jps
<span class="hljs-number">4745</span> QuorumPeerMain
<span class="hljs-number">5005</span> Kafka
</code></pre>

<pre><code>12. 动手实战，创建HelloKafka文件成功。
</code></pre>



<pre class="prettyprint"><code class=" hljs avrasm">[root@Master bin]<span class="hljs-preprocessor"># ./kafka-topics.sh --create --zookeeper Master:2181,Worker1:2181,Worker2:2181 --replication-factor 3 --partitions 1 --topic HelloKafka</span>
<span class="hljs-label">SLF4J:</span> Class path contains multiple SLF4J bindings.
<span class="hljs-label">SLF4J:</span> Found binding <span class="hljs-keyword">in</span> [jar:file:/usr/local/kafka_2<span class="hljs-number">.10</span>-<span class="hljs-number">0.9</span><span class="hljs-number">.0</span><span class="hljs-number">.1</span>/libs/slf4j-log4j12-<span class="hljs-number">1.7</span><span class="hljs-number">.6</span><span class="hljs-preprocessor">.jar</span>!/org/slf4j/impl/StaticLoggerBinder<span class="hljs-preprocessor">.class</span>]
<span class="hljs-label">SLF4J:</span> Found binding <span class="hljs-keyword">in</span> [jar:file:/usr/local/kafka_2<span class="hljs-number">.10</span>-<span class="hljs-number">0.9</span><span class="hljs-number">.0</span><span class="hljs-number">.1</span>/libs/slf4j-<span class="hljs-keyword">nop</span>-<span class="hljs-number">1.7</span><span class="hljs-number">.6</span><span class="hljs-preprocessor">.jar</span>!/org/slf4j/impl/StaticLoggerBinder<span class="hljs-preprocessor">.class</span>]
<span class="hljs-label">SLF4J:</span> See http://www<span class="hljs-preprocessor">.slf</span>4j<span class="hljs-preprocessor">.org</span>/codes<span class="hljs-preprocessor">.html</span><span class="hljs-preprocessor">#multiple_bindings for an explanation.</span>
<span class="hljs-label">SLF4J:</span> Actual binding is of type [org<span class="hljs-preprocessor">.slf</span>4j<span class="hljs-preprocessor">.impl</span><span class="hljs-preprocessor">.Log</span>4jLoggerFactory]
Created topic <span class="hljs-string">"HelloKafka"</span>.
</code></pre>

<pre><code>13. 创建topic
</code></pre>



<pre class="prettyprint"><code class=" hljs avrasm">[root@Master bin]<span class="hljs-preprocessor"># ./kafka-topics.sh --describe --zookeeper Master:2181,Worker1:2181,Worker2:2181 --topic HelloKafka</span>
<span class="hljs-label">SLF4J:</span> Class path contains multiple SLF4J bindings.
<span class="hljs-label">SLF4J:</span> Found binding <span class="hljs-keyword">in</span> [jar:file:/usr/local/kafka_2<span class="hljs-number">.10</span>-<span class="hljs-number">0.9</span><span class="hljs-number">.0</span><span class="hljs-number">.1</span>/libs/slf4j-log4j12-<span class="hljs-number">1.7</span><span class="hljs-number">.6</span><span class="hljs-preprocessor">.jar</span>!/org/slf4j/impl/StaticLoggerBinder<span class="hljs-preprocessor">.class</span>]
<span class="hljs-label">SLF4J:</span> Found binding <span class="hljs-keyword">in</span> [jar:file:/usr/local/kafka_2<span class="hljs-number">.10</span>-<span class="hljs-number">0.9</span><span class="hljs-number">.0</span><span class="hljs-number">.1</span>/libs/slf4j-<span class="hljs-keyword">nop</span>-<span class="hljs-number">1.7</span><span class="hljs-number">.6</span><span class="hljs-preprocessor">.jar</span>!/org/slf4j/impl/StaticLoggerBinder<span class="hljs-preprocessor">.class</span>]
<span class="hljs-label">SLF4J:</span> See http://www<span class="hljs-preprocessor">.slf</span>4j<span class="hljs-preprocessor">.org</span>/codes<span class="hljs-preprocessor">.html</span><span class="hljs-preprocessor">#multiple_bindings for an explanation.</span>
<span class="hljs-label">SLF4J:</span> Actual binding is of type [org<span class="hljs-preprocessor">.slf</span>4j<span class="hljs-preprocessor">.impl</span><span class="hljs-preprocessor">.Log</span>4jLoggerFactory]
<span class="hljs-label">Topic:</span>HelloKafka    PartitionCount:<span class="hljs-number">1</span>    ReplicationFactor:<span class="hljs-number">3</span> Configs:
    Topic: HelloKafka   Partition: <span class="hljs-number">0</span>    Leader: <span class="hljs-number">1</span>   Replicas: <span class="hljs-number">1</span>,<span class="hljs-number">2</span>,<span class="hljs-number">0</span> Isr: <span class="hljs-number">1</span>,<span class="hljs-number">2</span>,<span class="hljs-number">0</span>
</code></pre>

<pre><code>14. Producer传入数据，输入数据之后，按Ctrl + C，其中broker和Spark中的Woker节点，差不多，Spark计算的时候以Worker为单位，而Kafka在传输数据的时候以broker为单位。
</code></pre>



<pre class="prettyprint"><code class=" hljs avrasm">[root@Master bin]<span class="hljs-preprocessor"># ./kafka-console-producer.sh --broker-list Master:9092,Worker1:9092,Worker2:9092 --topic HelloKafka</span>
<span class="hljs-label">SLF4J:</span> Class path contains multiple SLF4J bindings.
<span class="hljs-label">SLF4J:</span> Found binding <span class="hljs-keyword">in</span> [jar:file:/usr/local/kafka_2<span class="hljs-number">.10</span>-<span class="hljs-number">0.9</span><span class="hljs-number">.0</span><span class="hljs-number">.1</span>/libs/slf4j-log4j12-<span class="hljs-number">1.7</span><span class="hljs-number">.6</span><span class="hljs-preprocessor">.jar</span>!/org/slf4j/impl/StaticLoggerBinder<span class="hljs-preprocessor">.class</span>]
<span class="hljs-label">SLF4J:</span> Found binding <span class="hljs-keyword">in</span> [jar:file:/usr/local/kafka_2<span class="hljs-number">.10</span>-<span class="hljs-number">0.9</span><span class="hljs-number">.0</span><span class="hljs-number">.1</span>/libs/slf4j-<span class="hljs-keyword">nop</span>-<span class="hljs-number">1.7</span><span class="hljs-number">.6</span><span class="hljs-preprocessor">.jar</span>!/org/slf4j/impl/StaticLoggerBinder<span class="hljs-preprocessor">.class</span>]
<span class="hljs-label">SLF4J:</span> See http://www<span class="hljs-preprocessor">.slf</span>4j<span class="hljs-preprocessor">.org</span>/codes<span class="hljs-preprocessor">.html</span><span class="hljs-preprocessor">#multiple_bindings for an explanation.</span>
<span class="hljs-label">SLF4J:</span> Actual binding is of type [org<span class="hljs-preprocessor">.slf</span>4j<span class="hljs-preprocessor">.impl</span><span class="hljs-preprocessor">.Log</span>4jLoggerFactory]
Life is short! you need Spark
Life is short! you need Spark
^C[root@Master bin]<span class="hljs-preprocessor">#</span>
</code></pre>

<pre><code>15. Consumer端接收到数据。
</code></pre>



<pre class="prettyprint"><code class=" hljs avrasm">[root@Master bin]<span class="hljs-preprocessor"># ./kafka-console-consumer.sh  --zookeeper Master:2181,Worker1:2181,Worker2:2181 --from-beginning --topic HelloKafka</span>
<span class="hljs-label">SLF4J:</span> Class path contains multiple SLF4J bindings.
<span class="hljs-label">SLF4J:</span> Found binding <span class="hljs-keyword">in</span> [jar:file:/usr/local/kafka_2<span class="hljs-number">.10</span>-<span class="hljs-number">0.9</span><span class="hljs-number">.0</span><span class="hljs-number">.1</span>/libs/slf4j-log4j12-<span class="hljs-number">1.7</span><span class="hljs-number">.6</span><span class="hljs-preprocessor">.jar</span>!/org/slf4j/impl/StaticLoggerBinder<span class="hljs-preprocessor">.class</span>]
<span class="hljs-label">SLF4J:</span> Found binding <span class="hljs-keyword">in</span> [jar:file:/usr/local/kafka_2<span class="hljs-number">.10</span>-<span class="hljs-number">0.9</span><span class="hljs-number">.0</span><span class="hljs-number">.1</span>/libs/slf4j-<span class="hljs-keyword">nop</span>-<span class="hljs-number">1.7</span><span class="hljs-number">.6</span><span class="hljs-preprocessor">.jar</span>!/org/slf4j/impl/StaticLoggerBinder<span class="hljs-preprocessor">.class</span>]
<span class="hljs-label">SLF4J:</span> See http://www<span class="hljs-preprocessor">.slf</span>4j<span class="hljs-preprocessor">.org</span>/codes<span class="hljs-preprocessor">.html</span><span class="hljs-preprocessor">#multiple_bindings for an explanation.</span>
<span class="hljs-label">SLF4J:</span> Actual binding is of type [org<span class="hljs-preprocessor">.slf</span>4j<span class="hljs-preprocessor">.impl</span><span class="hljs-preprocessor">.Log</span>4jLoggerFactory]
Life is short! you need Spark
Life is short! you need Spark
</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>