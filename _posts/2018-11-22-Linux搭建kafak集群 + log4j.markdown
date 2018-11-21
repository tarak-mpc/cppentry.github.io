---
layout:     post
title:      Linux搭建kafak集群 + log4j
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_39657597/article/details/80784495				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-light">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><strong>版本信息</strong> <br>
zookeeper-3.4.10 <br>
kafka_2.11-1.1.0</p>

<p><strong>集群信息</strong> <br>
192.168.0.109 <br>
192.168.0.108 <br>
192.168.0.111</p>

<p><strong>安装kafka集群</strong> <br>
1.解压kafka <br>
tar -zxvf  kafka_2.11-1.1.0.tgz</p>

<p>2.将解压的kafka拷贝到108，111上 <br>
scp -r kafka_2.11-1.1.0 root@192.168.0.108:/usr/</p>

<p>3.修改server.properties配置文件 <br>
vi config/server.properties <br>
主要有以下几点需要更改：</p>



<pre class="prettyprint"><code class=" hljs avrasm">broker<span class="hljs-preprocessor">.id</span>=<span class="hljs-number">0</span>
listeners=PLAINTEXT://<span class="hljs-number">192.168</span><span class="hljs-number">.0</span><span class="hljs-number">.109</span>:<span class="hljs-number">9091</span>
<span class="hljs-preprocessor">#kafka日志存放位置可以为默认</span>
log<span class="hljs-preprocessor">.dirs</span>=/tmp/kafka-logs
<span class="hljs-preprocessor">#kafka日志留存时间</span>
log<span class="hljs-preprocessor">.retention</span><span class="hljs-preprocessor">.hours</span>=<span class="hljs-number">168</span>
<span class="hljs-preprocessor">#在指定时间后关闭空闲连接，默认是600000</span>
connections<span class="hljs-preprocessor">.max</span><span class="hljs-preprocessor">.idle</span><span class="hljs-preprocessor">.ms</span>=<span class="hljs-number">600000</span>
<span class="hljs-preprocessor">#zookeeper的ip与端口</span>
zookeeper<span class="hljs-preprocessor">.connect</span>=<span class="hljs-number">192.168</span><span class="hljs-number">.0</span><span class="hljs-number">.109</span>:<span class="hljs-number">2181</span>,<span class="hljs-number">192.168</span><span class="hljs-number">.0</span><span class="hljs-number">.108</span>:<span class="hljs-number">2181</span>,<span class="hljs-number">192.168</span><span class="hljs-number">.0</span><span class="hljs-number">.111</span>:<span class="hljs-number">2181</span>/kafka</code></pre>

<p>参照上面的配置修改其他108，111上的server.properties</p>

<p>4.启动kafka <br>
配置完后分别启动109，108，111上的kafka <br>
启动：bin/kafka-server-start.sh config/server.properties &amp; <br>
关闭：bin/kafka-server-stop.sh config/server.properties <br>
<font color="#4590a3">注：启动加&amp;符号是因为我用xshell远程连接服务器，当我断开xshell连接时kafka的访问也关闭了，加上&amp;符号后再启动后可以用exit断开xshell连接，不会影响kafka。</font></p>

<p>5.创建topic <br>
创建一个名为topic_r3p3，3分区3备份的topic <br>
bin/kafka-topics.sh –create –zookeeper 192.168.0.109:2181/kafka –replication-factor 3 –partitions 3 –topic topic_r3p3 <br>
查看所有topic <br>
bin/kafka-topics.sh –list –zookeeper 192.168.0.109:2181/kafka <br>
查看单个topic <br>
bin/kafka-topics.sh –describe –zookeeper 192.168.0.109:2181/kafka –topic topic_r3p3  <br>
<img src="https://img-blog.csdn.net/20180623155955165?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NjU3NTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
Partition： 分区 <br>
Leader ： 负责读写指定分区的节点 <br>
Replicas ： 复制该分区log的节点列表 <br>
Isr ： “in-sync” replicas，当前活跃的副本列表（是一个子集），并且可能成为Leader <br>
删除topic <br>
bin/kafka-topics.sh –delete –zookeeper 192.168.0.109:2181/kafka –topic topic_r3p3</p>

<p>6.启动Producer和Consumer <br>
在109启动Producer: <br>
bin/kafka-console-producer.sh –broker-list 192.168.0.109:9091,192.168.0.108:9092,192.168.0.111:9093 –topic topic_r3p3 <br>
在108启动Consumer: <br>
bin/kafka-console-consumer.sh –zookeeper 192.168.0.109:2181/kafka –from-beginning –topic topic_r3p3</p>

<p>7.验证 <br>
在Producer输入你的消息，然后在Consumer查看看到你输入的消息内容，如果接收正常说明你的集群安装成功 <br>
Producer: <br>
<img src="https://img-blog.csdn.net/20180623161151814?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NjU3NTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
consumer: <br>
<img src="https://img-blog.csdn.net/20180623161218665?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NjU3NTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p><strong>使用Log4j将程序日志实时写入Kafka</strong> <br>
1.引入jar包</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.kafka<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>kafka_2.9.2<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>0.8.2.1<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span> </code></pre>

<p>2.log4j.properties：</p>



<pre class="prettyprint"><code class=" hljs avrasm">log4j<span class="hljs-preprocessor">.rootLogger</span>=INFO,console,kafka

log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.console</span>=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.log</span>4j<span class="hljs-preprocessor">.ConsoleAppender</span>
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.console</span><span class="hljs-preprocessor">.target</span>=System<span class="hljs-preprocessor">.out</span>
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.console</span><span class="hljs-preprocessor">.layout</span>=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.log</span>4j<span class="hljs-preprocessor">.PatternLayout</span>
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.console</span><span class="hljs-preprocessor">.layout</span><span class="hljs-preprocessor">.ConversionPattern</span>=%d [%-<span class="hljs-number">5</span>p] [%t] - [%l] %m%n

log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.KAFKA</span>=<span class="hljs-keyword">com</span><span class="hljs-preprocessor">.vision</span><span class="hljs-preprocessor">.log</span><span class="hljs-preprocessor">.MyKafkaLog</span>4jAppender
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.KAFKA</span><span class="hljs-preprocessor">.topic</span>=topic_r3p3
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.KAFKA</span><span class="hljs-preprocessor">.brokerList</span>=<span class="hljs-number">192.168</span><span class="hljs-number">.0</span><span class="hljs-number">.109</span>:<span class="hljs-number">9091</span>,<span class="hljs-number">192.168</span><span class="hljs-number">.0</span><span class="hljs-number">.108</span>:<span class="hljs-number">9092</span>,<span class="hljs-number">192.168</span><span class="hljs-number">.0</span><span class="hljs-number">.111</span>:<span class="hljs-number">9093</span>
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.KAFKA</span><span class="hljs-preprocessor">.compressionType</span>=none
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.KAFKA</span><span class="hljs-preprocessor">.syncSend</span>=true
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.KAFKA</span><span class="hljs-preprocessor">.layout</span>=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.log</span>4j<span class="hljs-preprocessor">.PatternLayout</span>
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.KAFKA</span><span class="hljs-preprocessor">.layout</span><span class="hljs-preprocessor">.ConversionPattern</span>=%d %p [%c] - &lt;%m&gt;%n
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.KAFKA</span><span class="hljs-preprocessor">.Encoding</span>=utf-<span class="hljs-number">8</span></code></pre>

<p>3.Application.java</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">import</span> org.apache.log4j.Logger;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">Application</span> {</span>

    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> Logger LOGGER = Logger.getLogger(Application.class);

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> InterruptedException {
        <span class="hljs-keyword">int</span> inex=<span class="hljs-number">1</span>;
        <span class="hljs-keyword">for</span> (<span class="hljs-keyword">int</span> i = inex; i &lt; inex+<span class="hljs-number">5</span>; i++) {
            LOGGER.info(<span class="hljs-string">"Info &gt;"</span> + i + <span class="hljs-string">"&lt;"</span>);
            Thread.sleep(<span class="hljs-number">500</span>);
        }
    }
}</code></pre>

<p>4.验证 <br>
运行main方法，控制台结果： <br>
<img src="https://img-blog.csdn.net/20180623162552822?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NjU3NTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
Consumer结果： <br>
<img src="https://img-blog.csdn.net/20180623162720171?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NjU3NTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
日志： <br>
topic_r3p3-0： <br>
<img src="https://img-blog.csdn.net/20180623163330219?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NjU3NTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
topic_r3p3-1： <br>
<img src="https://img-blog.csdn.net/2018062316333883?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NjU3NTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title="">topic_r3p3-2： <br>
<img src="https://img-blog.csdn.net/20180623163343920?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM5NjU3NTk3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
Topic为opic_r3p3的消息有3个partion，日志随机存放</p>

<p>kafka+log4jdemo：<a href="https://download.csdn.net/download/qq_39657597/10495053" rel="nofollow">https://download.csdn.net/download/qq_39657597/10495053</a> <br>
有需要的可以去下载。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>