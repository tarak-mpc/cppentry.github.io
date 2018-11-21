---
layout:     post
title:      Kafka集群部署与shell命令行操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_37334135/article/details/78596391				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><strong>1、kafka简介</strong> <br>
在流式计算中，Kafka一般用来缓存数据，Storm通过消费Kafka的数据进行计算。</p>

<p>KAFKA + STORM +REDIS</p>

<p>1、Apache Kafka是一个开源消息系统，由Scala写成。是由Apache软件基金会开发的一个开源消息系统项目。 <br>
2、Kafka最初是由LinkedIn开发，并于2011年初开源。2012年10月从Apache Incubator毕业。该项目的目标是为处理实时数据提供一个统一、高通量、低等待的平台。 <br>
3、Kafka是一个分布式消息队列：生产者、消费者的功能。它提供了类似于JMS的特性，但是在设计实现上完全不同，此外它并不是JMS规范的实现。 <br>
4、Kafka对消息保存时根据Topic进行归类，发送消息者称为Producer,消息接受者称为Consumer,此外kafka集群有多个kafka实例组成，每个实例(server)称为broker。 <br>
5、无论是kafka集群，还是producer和consumer都依赖于zookeeper集群保存一些meta信息，来保证系统可用性 <br>
注：类JMS消息队列，结合JMS中的两种模式，可以有多个消费者主动拉取数据，在JMS中只有点对点模式才有消费者主动拉取数据。 <br>
<strong>2、kafka集群部署</strong> <br>
kafka集群跟storm集群类似，分为：下载安装包、解压安装包、修改配置文件、分发安装包（之后需要在各个机器上再次修改配置文件）、启动集群。</p>

<p>准备工作 <br>
这里使用三台机器进行集群:mini1,mini2,mini3 <br>
关闭防火墙，各机器的hosts文件如下</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-number">127.0</span><span class="hljs-number">.0</span><span class="hljs-number">.1</span>   localhost localhost<span class="hljs-preprocessor">.localdomain</span> localhost4 localhost4<span class="hljs-preprocessor">.localdomain</span>4
::<span class="hljs-number">1</span>         localhost localhost<span class="hljs-preprocessor">.localdomain</span> localhost6 localhost6<span class="hljs-preprocessor">.localdomain</span>6 localhost<span class="hljs-preprocessor">.jinbm</span>
<span class="hljs-number">192.168</span><span class="hljs-number">.25</span><span class="hljs-number">.127</span> mini1
<span class="hljs-number">192.168</span><span class="hljs-number">.25</span><span class="hljs-number">.129</span> mini2
<span class="hljs-number">192.168</span><span class="hljs-number">.25</span><span class="hljs-number">.130</span> mini3</code></pre>

<p>1、下载安装包并上传到mini1机器上，这里下载的是 kafka_2.11-0.8.2.0.tgz； <br>
2、解压安装包导指定目录，我这台机器的apps目录下安装了很多大数据相关的软件，所以解压到该目录了，并且重命名。</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@mini1</span> ~]<span class="hljs-comment"># tar -zxvf kafka_2.11-0.8.2.0.tgz -C apps/</span>
[root<span class="hljs-variable">@mini1</span> ~]<span class="hljs-comment"># cd apps/</span>
[root<span class="hljs-variable">@mini1</span> apps]<span class="hljs-comment"># ll</span>
总用量 <span class="hljs-number">28</span>
drwxr-xr-x.  <span class="hljs-number">8</span> root   root   <span class="hljs-number">4096</span> <span class="hljs-number">10</span>月 <span class="hljs-number">19</span> <span class="hljs-number">15</span><span class="hljs-symbol">:</span><span class="hljs-number">15</span> apache-flume-<span class="hljs-number">1.6</span>.<span class="hljs-number">0</span>-bin
drwxrwxr-x. <span class="hljs-number">10</span> hadoop hadoop <span class="hljs-number">4096</span> <span class="hljs-number">9</span>月  <span class="hljs-number">30</span> <span class="hljs-number">22</span><span class="hljs-symbol">:</span><span class="hljs-number">04</span> hadoop-<span class="hljs-number">2.6</span>.<span class="hljs-number">4</span>
drwxr-xr-x.  <span class="hljs-number">7</span> root   root   <span class="hljs-number">4096</span> <span class="hljs-number">10</span>月 <span class="hljs-number">30</span> <span class="hljs-number">00</span><span class="hljs-symbol">:</span><span class="hljs-number">20</span> hbase
drwxr-xr-x.  <span class="hljs-number">8</span> root   root   <span class="hljs-number">4096</span> <span class="hljs-number">10</span>月 <span class="hljs-number">17</span> <span class="hljs-number">12</span><span class="hljs-symbol">:</span><span class="hljs-number">38</span> hive
drwxr-xr-x.  <span class="hljs-number">5</span> root   root   <span class="hljs-number">4096</span> <span class="hljs-number">1</span>月  <span class="hljs-number">29</span> <span class="hljs-number">2015</span> kafka_2.<span class="hljs-number">11</span>-<span class="hljs-number">0</span>.<span class="hljs-number">8.2</span>.<span class="hljs-number">0</span>
drwxr-xr-x. <span class="hljs-number">10</span> root   root   <span class="hljs-number">4096</span> <span class="hljs-number">10</span>月 <span class="hljs-number">30</span> <span class="hljs-number">15</span><span class="hljs-symbol">:</span><span class="hljs-number">34</span> storm
drwxr-xr-x. <span class="hljs-number">10</span> root   root   <span class="hljs-number">4096</span> <span class="hljs-number">10</span>月 <span class="hljs-number">29</span> <span class="hljs-number">23</span><span class="hljs-symbol">:</span><span class="hljs-number">21</span> zookeeper-<span class="hljs-number">3.4</span>.<span class="hljs-number">6</span>
[root<span class="hljs-variable">@mini1</span> apps]<span class="hljs-comment"># mv kafka_2.11-0.8.2.0/ kafka</span>
[root<span class="hljs-variable">@mini1</span> apps]<span class="hljs-comment"># ll</span>
总用量 <span class="hljs-number">28</span>
drwxr-xr-x.  <span class="hljs-number">8</span> root   root   <span class="hljs-number">4096</span> <span class="hljs-number">10</span>月 <span class="hljs-number">19</span> <span class="hljs-number">15</span><span class="hljs-symbol">:</span><span class="hljs-number">15</span> apache-flume-<span class="hljs-number">1.6</span>.<span class="hljs-number">0</span>-bin
drwxrwxr-x. <span class="hljs-number">10</span> hadoop hadoop <span class="hljs-number">4096</span> <span class="hljs-number">9</span>月  <span class="hljs-number">30</span> <span class="hljs-number">22</span><span class="hljs-symbol">:</span><span class="hljs-number">04</span> hadoop-<span class="hljs-number">2.6</span>.<span class="hljs-number">4</span>
drwxr-xr-x.  <span class="hljs-number">7</span> root   root   <span class="hljs-number">4096</span> <span class="hljs-number">10</span>月 <span class="hljs-number">30</span> <span class="hljs-number">00</span><span class="hljs-symbol">:</span><span class="hljs-number">20</span> hbase
drwxr-xr-x.  <span class="hljs-number">8</span> root   root   <span class="hljs-number">4096</span> <span class="hljs-number">10</span>月 <span class="hljs-number">17</span> <span class="hljs-number">12</span><span class="hljs-symbol">:</span><span class="hljs-number">38</span> hive
drwxr-xr-x.  <span class="hljs-number">5</span> root   root   <span class="hljs-number">4096</span> <span class="hljs-number">1</span>月  <span class="hljs-number">29</span> <span class="hljs-number">2015</span> kafka
drwxr-xr-x. <span class="hljs-number">10</span> root   root   <span class="hljs-number">4096</span> <span class="hljs-number">10</span>月 <span class="hljs-number">30</span> <span class="hljs-number">15</span><span class="hljs-symbol">:</span><span class="hljs-number">34</span> storm
drwxr-xr-x. <span class="hljs-number">10</span> root   root   <span class="hljs-number">4096</span> <span class="hljs-number">10</span>月 <span class="hljs-number">29</span> <span class="hljs-number">23</span><span class="hljs-symbol">:</span><span class="hljs-number">21</span> zookeeper-<span class="hljs-number">3.4</span>.<span class="hljs-number">6</span></code></pre>

<p>3、修改配置文件，进入kafka的config目录下，可以查看到各类配置文件，这里修改server.properties 。</p>



<pre class="prettyprint"><code class=" hljs avrasm">[root@mini1 config]<span class="hljs-preprocessor"># ll</span>
总用量 <span class="hljs-number">32</span>
-rw-r--r--. <span class="hljs-number">1</span> root root <span class="hljs-number">1199</span> <span class="hljs-number">1</span>月  <span class="hljs-number">29</span> <span class="hljs-number">2015</span> consumer<span class="hljs-preprocessor">.properties</span>
-rw-r--r--. <span class="hljs-number">1</span> root root <span class="hljs-number">3846</span> <span class="hljs-number">1</span>月  <span class="hljs-number">29</span> <span class="hljs-number">2015</span> log4j<span class="hljs-preprocessor">.properties</span>
-rw-r--r--. <span class="hljs-number">1</span> root root <span class="hljs-number">2228</span> <span class="hljs-number">1</span>月  <span class="hljs-number">29</span> <span class="hljs-number">2015</span> producer<span class="hljs-preprocessor">.properties</span>
-rw-r--r--. <span class="hljs-number">1</span> root root <span class="hljs-number">5559</span> <span class="hljs-number">1</span>月  <span class="hljs-number">29</span> <span class="hljs-number">2015</span> server<span class="hljs-preprocessor">.properties</span>
-rw-r--r--. <span class="hljs-number">1</span> root root <span class="hljs-number">3325</span> <span class="hljs-number">1</span>月  <span class="hljs-number">29</span> <span class="hljs-number">2015</span> test-log4j<span class="hljs-preprocessor">.properties</span>
-rw-r--r--. <span class="hljs-number">1</span> root root  <span class="hljs-number">993</span> <span class="hljs-number">1</span>月  <span class="hljs-number">29</span> <span class="hljs-number">2015</span> tools-log4j<span class="hljs-preprocessor">.properties</span>
-rw-r--r--. <span class="hljs-number">1</span> root root <span class="hljs-number">1023</span> <span class="hljs-number">1</span>月  <span class="hljs-number">29</span> <span class="hljs-number">2015</span> zookeeper<span class="hljs-preprocessor">.properties</span>
[root@mini1 config]<span class="hljs-preprocessor"># vi server.properties </span>
<span class="hljs-preprocessor">#broker的全局唯一编号，不能重复</span>
broker<span class="hljs-preprocessor">.id</span>=<span class="hljs-number">0</span>

<span class="hljs-preprocessor">#用来监听链接的端口，producer或consumer将在此端口建立连接</span>
port=<span class="hljs-number">9092</span>

<span class="hljs-preprocessor">#处理网络请求的线程数量</span>
num<span class="hljs-preprocessor">.network</span><span class="hljs-preprocessor">.threads</span>=<span class="hljs-number">3</span>

<span class="hljs-preprocessor">#用来处理磁盘IO的线程数量</span>
num<span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.threads</span>=<span class="hljs-number">8</span>

<span class="hljs-preprocessor">#发送套接字的缓冲区大小</span>
socket<span class="hljs-preprocessor">.send</span><span class="hljs-preprocessor">.buffer</span><span class="hljs-preprocessor">.bytes</span>=<span class="hljs-number">102400</span>

<span class="hljs-preprocessor">#接受套接字的缓冲区大小</span>
socket<span class="hljs-preprocessor">.receive</span><span class="hljs-preprocessor">.buffer</span><span class="hljs-preprocessor">.bytes</span>=<span class="hljs-number">102400</span>

<span class="hljs-preprocessor">#请求套接字的缓冲区大小</span>
socket<span class="hljs-preprocessor">.request</span><span class="hljs-preprocessor">.max</span><span class="hljs-preprocessor">.bytes</span>=<span class="hljs-number">104857600</span>

<span class="hljs-preprocessor">#kafka运行日志存放的路径</span>
log<span class="hljs-preprocessor">.dirs</span>=/root/apps/kafka

<span class="hljs-preprocessor">#topic在当前broker上的分片个数</span>
num<span class="hljs-preprocessor">.partitions</span>=<span class="hljs-number">2</span>

<span class="hljs-preprocessor">#用来恢复和清理data下数据的线程数量</span>
num<span class="hljs-preprocessor">.recovery</span><span class="hljs-preprocessor">.threads</span><span class="hljs-preprocessor">.per</span><span class="hljs-preprocessor">.data</span><span class="hljs-preprocessor">.dir</span>=<span class="hljs-number">1</span>

<span class="hljs-preprocessor">#segment文件保留的最长时间，超时将被删除</span>
log<span class="hljs-preprocessor">.retention</span><span class="hljs-preprocessor">.hours</span>=<span class="hljs-number">168</span>

<span class="hljs-preprocessor">#滚动生成新的segment文件的最大时间</span>
log<span class="hljs-preprocessor">.roll</span><span class="hljs-preprocessor">.hours</span>=<span class="hljs-number">168</span>

<span class="hljs-preprocessor">#日志文件中每个segment的大小，默认为1G</span>
log<span class="hljs-preprocessor">.segment</span><span class="hljs-preprocessor">.bytes</span>=<span class="hljs-number">1073741824</span>

<span class="hljs-preprocessor">#周期性检查文件大小的时间</span>
log<span class="hljs-preprocessor">.retention</span><span class="hljs-preprocessor">.check</span><span class="hljs-preprocessor">.interval</span><span class="hljs-preprocessor">.ms</span>=<span class="hljs-number">300000</span>

<span class="hljs-preprocessor">#日志清理是否打开</span>
log<span class="hljs-preprocessor">.cleaner</span><span class="hljs-preprocessor">.enable</span>=true

<span class="hljs-preprocessor">#broker需要使用zookeeper保存meta数据</span>
zookeeper<span class="hljs-preprocessor">.connect</span>=mini1:<span class="hljs-number">2181</span>,mini2:<span class="hljs-number">2181</span>,mini3:<span class="hljs-number">2181</span>

<span class="hljs-preprocessor">#zookeeper链接超时时间</span>
zookeeper<span class="hljs-preprocessor">.connection</span><span class="hljs-preprocessor">.timeout</span><span class="hljs-preprocessor">.ms</span>=<span class="hljs-number">6000</span>

<span class="hljs-preprocessor">#partion buffer中，消息的条数达到阈值，将触发flush到磁盘</span>
log<span class="hljs-preprocessor">.flush</span><span class="hljs-preprocessor">.interval</span><span class="hljs-preprocessor">.messages</span>=<span class="hljs-number">10000</span>

<span class="hljs-preprocessor">#消息buffer的时间，达到阈值，将触发flush到磁盘</span>
log<span class="hljs-preprocessor">.flush</span><span class="hljs-preprocessor">.interval</span><span class="hljs-preprocessor">.ms</span>=<span class="hljs-number">3000</span>

<span class="hljs-preprocessor">#删除topic需要server.properties中设置delete.topic.enable=true否则只是标记删除</span>
delete<span class="hljs-preprocessor">.topic</span><span class="hljs-preprocessor">.enable</span>=true

<span class="hljs-preprocessor">#此处的host.name为本机IP(重要),如果不改,则客户端会抛出:Producer connection to localhost:9092 unsuccessful 错误!</span>
host<span class="hljs-preprocessor">.name</span>=mini1</code></pre>

<p>4、将mini1上的kafka分发到mini2和mini3上</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@mini1</span> ~]<span class="hljs-comment"># scp -r apps/kafka/ mini2:/root/apps/</span>
[root<span class="hljs-variable">@mini1</span> ~]<span class="hljs-comment"># scp -r apps/kafka/ mini3:/root/apps/</span></code></pre>

<p>5、再次修改mini2和mini3上的配置文件server.properties，修改两处，一个是broker编号一个是主机名 <br>
mini2上如下</p>



<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor">#broker的全局唯一编号，不能重复</span>
broker.id=<span class="hljs-number">1</span>
<span class="hljs-preprocessor">#此处的host.name为本机IP(重要),如果不改,则客户端会抛出:Producer connection to localhost:9092 unsuccessful 错误!</span>
host.name=mini2</code></pre>

<p>mini3上如下</p>



<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor">#broker的全局唯一编号，不能重复</span>
broker.id=<span class="hljs-number">2</span>
<span class="hljs-preprocessor">#此处的host.name为本机IP(重要),如果不改,则客户端会抛出:Producer connection to localhost:9092 unsuccessful 错误!</span>
host.name=mini3</code></pre>

<p>6、到这里配置完了，但是为了不进入kafka的bin目录下就能执行命令行操作，所以需要修改环境变量。三台机器上修改均如下(添加最后两行)</p>



<pre class="prettyprint"><code class=" hljs bash">[root@mini1 ~]<span class="hljs-comment"># vi /etc/profile</span>
...
JAVA_HOME=/heima32/jdk1.<span class="hljs-number">7.0</span>_55/
HADOOP_HOME=/root/apps/hadoop-<span class="hljs-number">2.6</span>.<span class="hljs-number">4</span>
<span class="hljs-keyword">export</span> PATH=<span class="hljs-variable">$JAVA_HOME</span>/bin:<span class="hljs-variable">$PATH</span>:<span class="hljs-variable">$HADOOP_HOME</span>/bin:<span class="hljs-variable">$HADOOP_HOME</span>/sbin
<span class="hljs-keyword">export</span> HBASE_HOME=/root/apps/hbase
<span class="hljs-keyword">export</span> PATH=<span class="hljs-variable">$PATH</span>:<span class="hljs-variable">$HBASE_HOME</span>/bin
<span class="hljs-keyword">export</span> STORM_HOME=/root/apps/storm
<span class="hljs-keyword">export</span> PATH=<span class="hljs-variable">$PATH</span>:<span class="hljs-variable">$STORM_HOME</span>/bin
<span class="hljs-keyword">export</span> KAFKA_HOME=/root/apps/kafka
<span class="hljs-keyword">export</span> PATH=<span class="hljs-variable">$PATH</span>:<span class="hljs-variable">$KAFKA_HOME</span>/bin
</code></pre>

<p>7、启动集群 <br>
依次启动mini1，mini2和mini3上的kafka（进入kafka安装目录执行以下命令）</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@mini1</span> kafka]<span class="hljs-comment"># bin/kafka-server-start.sh config/server.properties</span></code></pre>

<p><strong>3、kafka命令行操作</strong> <br>
1、创建topic（注：因为修改了环境变量，所以不管有没有进入bin目录都能执行相应命令行操作） <br>
参数表示：指定副本数，使用几个partition（分片），topic名称</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-title">[</span><span class="hljs-comment">root@mini1</span> <span class="hljs-comment">bin</span><span class="hljs-title">]</span><span class="hljs-comment">#</span> <span class="hljs-string">.</span><span class="hljs-comment">/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">create</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">mini1:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">replication</span><span class="hljs-literal">-</span><span class="hljs-comment">factor</span> <span class="hljs-comment">2</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">partitions</span> <span class="hljs-comment">3</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">orderMq</span>
<span class="hljs-comment">Created</span> <span class="hljs-comment">topic</span> <span class="hljs-comment">"orderMq"</span><span class="hljs-string">.</span>
<span class="hljs-title">[</span><span class="hljs-comment">root@mini1</span> <span class="hljs-comment">kafka</span><span class="hljs-title">]</span><span class="hljs-comment">#</span> <span class="hljs-comment">kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">create</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">mini1:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">replication</span><span class="hljs-literal">-</span><span class="hljs-comment">factor</span> <span class="hljs-comment">2</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">partitions</span> <span class="hljs-comment">3</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">test</span>
<span class="hljs-comment">Created</span> <span class="hljs-comment">topic</span> <span class="hljs-comment">"test"</span><span class="hljs-string">.</span></code></pre>

<p>2、查看当前存在哪些topic</p>

<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-title">[</span><span class="hljs-comment">root@mini1</span> <span class="hljs-comment">kafka</span><span class="hljs-title">]</span><span class="hljs-comment">#</span> <span class="hljs-comment">kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">list</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">mini1:2181</span>
<span class="hljs-comment">orderMq</span>
<span class="hljs-comment">test</span></code></pre>

<p>3、删除指定topic</p>



<pre class="prettyprint"><code class=" hljs coffeescript">[root<span class="hljs-property">@mini1</span> kafka]<span class="hljs-comment"># kafka-topics.sh --delete --zookeeper mini1:2181 --topic test</span>
Topic test <span class="hljs-keyword">is</span> marked <span class="hljs-keyword">for</span> deletion.
<span class="hljs-attribute">Note</span>: This will have <span class="hljs-literal">no</span> impact <span class="hljs-keyword">if</span> <span class="hljs-keyword">delete</span>.topic.enable <span class="hljs-keyword">is</span> <span class="hljs-keyword">not</span> set to <span class="hljs-literal">true</span>.
[root<span class="hljs-property">@mini1</span> kafka]<span class="hljs-comment"># kafka-topics.sh --list --zookeeper mini1:2181</span>
orderMq</code></pre>

<p>4、通过shell命令行发送message(消息)</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-title">[</span><span class="hljs-comment">root@mini1</span> <span class="hljs-comment">bin</span><span class="hljs-title">]</span><span class="hljs-comment">#</span> <span class="hljs-comment">kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">console</span><span class="hljs-literal">-</span><span class="hljs-comment">producer</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">broker</span><span class="hljs-literal">-</span><span class="hljs-comment">list</span> <span class="hljs-comment">mini1:9092</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">orderMq</span>
<span class="hljs-comment">hello</span> <span class="hljs-comment">tom</span>
<span class="hljs-comment">hi</span> <span class="hljs-comment">jerry</span>
<span class="hljs-comment">spring</span> <span class="hljs-comment">^H</span>
<span class="hljs-comment">hhaah</span>
<span class="hljs-comment">xixi</span>
<span class="hljs-comment">nini</span>
</code></pre>

<p>5、通过shell命令行消费消息</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-title">[</span><span class="hljs-comment">root@mini1</span> <span class="hljs-comment">bin</span><span class="hljs-title">]</span><span class="hljs-comment">#</span> <span class="hljs-comment">kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">console</span><span class="hljs-literal">-</span><span class="hljs-comment">consumer</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">mini1:2181</span> <span class="hljs-literal">-</span><span class="hljs-comment">from</span><span class="hljs-literal">-</span><span class="hljs-comment">beginning</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">orderMq</span>
<span class="hljs-comment">hello</span> <span class="hljs-comment">tom</span>
<span class="hljs-comment">hi</span> <span class="hljs-comment">jerry</span>
<span class="hljs-comment">spring</span> 
<span class="hljs-comment">hhaah</span>
<span class="hljs-comment">xixi</span>
<span class="hljs-comment">nini</span>
</code></pre>

<p>注：这里的生成和消费是同顺序的，但有时候是不会顺序一致的，只能保证消费一个partition里面的消息时是一致，消费多个partition（上面指定了消息被发送到了4个partition）里面的是不能保证顺序完全一致。</p>

<p>6、查看某个topic的详情 <br>
orderMq这个topic有用了三个partition（自己创建的时候指定的），各自有2个副本（也是自己创建的时候指定的）以及对于的leader（用处后面讲）。</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-title">[</span><span class="hljs-comment">root@mini2</span> <span class="hljs-comment">bin</span><span class="hljs-title">]</span><span class="hljs-comment">#</span> <span class="hljs-string">.</span><span class="hljs-comment">/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">orderMq</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">describe</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">mini1:2181</span>
<span class="hljs-comment">Topic:orderMq</span>   <span class="hljs-comment">PartitionCount:3</span>        <span class="hljs-comment">ReplicationFactor:2</span>     <span class="hljs-comment">Configs:</span>
        <span class="hljs-comment">Topic:</span> <span class="hljs-comment">orderMq</span>  <span class="hljs-comment">Partition:</span> <span class="hljs-comment">0</span>    <span class="hljs-comment">Leader:</span> <span class="hljs-comment">2</span>       <span class="hljs-comment">Replicas:</span> <span class="hljs-comment">2</span><span class="hljs-string">,</span><span class="hljs-comment">0</span>   <span class="hljs-comment">Isr:</span> <span class="hljs-comment">0</span><span class="hljs-string">,</span><span class="hljs-comment">2</span>
        <span class="hljs-comment">Topic:</span> <span class="hljs-comment">orderMq</span>  <span class="hljs-comment">Partition:</span> <span class="hljs-comment">1</span>    <span class="hljs-comment">Leader:</span> <span class="hljs-comment">0</span>       <span class="hljs-comment">Replicas:</span> <span class="hljs-comment">0</span><span class="hljs-string">,</span><span class="hljs-comment">1</span>   <span class="hljs-comment">Isr:</span> <span class="hljs-comment">0</span><span class="hljs-string">,</span><span class="hljs-comment">1</span>
        <span class="hljs-comment">Topic:</span> <span class="hljs-comment">orderMq</span>  <span class="hljs-comment">Partition:</span> <span class="hljs-comment">2</span>    <span class="hljs-comment">Leader:</span> <span class="hljs-comment">1</span>       <span class="hljs-comment">Replicas:</span> <span class="hljs-comment">1</span><span class="hljs-string">,</span><span class="hljs-comment">2</span>   <span class="hljs-comment">Isr:</span> <span class="hljs-comment">1</span><span class="hljs-string">,</span><span class="hljs-comment">2</span>
</code></pre>

<p>接下来再创建个topic并且查看详情（使用4个partition，两个副本），并且进入运行日志存放的路径查看</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-title">[</span><span class="hljs-comment">root@mini2</span> <span class="hljs-comment">bin</span><span class="hljs-title">]</span><span class="hljs-comment">#</span> <span class="hljs-comment">kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">create</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">mini1:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">replication</span><span class="hljs-literal">-</span><span class="hljs-comment">factor</span> <span class="hljs-comment">2</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">partitions</span> <span class="hljs-comment">4</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">payment</span>
<span class="hljs-comment">Created</span> <span class="hljs-comment">topic</span> <span class="hljs-comment">"payment"</span><span class="hljs-string">.</span>
<span class="hljs-title">[</span><span class="hljs-comment">root@mini2</span> <span class="hljs-comment">bin</span><span class="hljs-title">]</span><span class="hljs-comment">#</span> <span class="hljs-comment">kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">payment</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">describe</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">mini1:2181</span>
<span class="hljs-comment">Topic:payment</span>   <span class="hljs-comment">PartitionCount:4</span>        <span class="hljs-comment">ReplicationFactor:2</span>     <span class="hljs-comment">Configs:</span>
        <span class="hljs-comment">Topic:</span> <span class="hljs-comment">payment</span>  <span class="hljs-comment">Partition:</span> <span class="hljs-comment">0</span>    <span class="hljs-comment">Leader:</span> <span class="hljs-comment">2</span>       <span class="hljs-comment">Replicas:</span> <span class="hljs-comment">2</span><span class="hljs-string">,</span><span class="hljs-comment">1</span>   <span class="hljs-comment">Isr:</span> <span class="hljs-comment">2</span><span class="hljs-string">,</span><span class="hljs-comment">1</span>
        <span class="hljs-comment">Topic:</span> <span class="hljs-comment">payment</span>  <span class="hljs-comment">Partition:</span> <span class="hljs-comment">1</span>    <span class="hljs-comment">Leader:</span> <span class="hljs-comment">0</span>       <span class="hljs-comment">Replicas:</span> <span class="hljs-comment">0</span><span class="hljs-string">,</span><span class="hljs-comment">2</span>   <span class="hljs-comment">Isr:</span> <span class="hljs-comment">0</span><span class="hljs-string">,</span><span class="hljs-comment">2</span>
        <span class="hljs-comment">Topic:</span> <span class="hljs-comment">payment</span>  <span class="hljs-comment">Partition:</span> <span class="hljs-comment">2</span>    <span class="hljs-comment">Leader:</span> <span class="hljs-comment">1</span>       <span class="hljs-comment">Replicas:</span> <span class="hljs-comment">1</span><span class="hljs-string">,</span><span class="hljs-comment">0</span>   <span class="hljs-comment">Isr:</span> <span class="hljs-comment">1</span><span class="hljs-string">,</span><span class="hljs-comment">0</span>
        <span class="hljs-comment">Topic:</span> <span class="hljs-comment">payment</span>  <span class="hljs-comment">Partition:</span> <span class="hljs-comment">3</span>    <span class="hljs-comment">Leader:</span> <span class="hljs-comment">2</span>       <span class="hljs-comment">Replicas:</span> <span class="hljs-comment">2</span><span class="hljs-string">,</span><span class="hljs-comment">0</span>   <span class="hljs-comment">Isr:</span> <span class="hljs-comment">2</span><span class="hljs-string">,</span><span class="hljs-comment">0</span></code></pre>



<pre class="prettyprint"><code class=" hljs perl">[root<span class="hljs-variable">@mini1</span> kafka]<span class="hljs-comment"># cd kafka-logs/</span>
[root<span class="hljs-variable">@mini1</span> kafka-logs]<span class="hljs-comment"># ll</span>
总用量 <span class="hljs-number">32</span>
-rw-r--r--. <span class="hljs-number">1</span> root root    <span class="hljs-number">4</span> <span class="hljs-number">11</span>月 <span class="hljs-number">21</span> <span class="hljs-number">22</span>:<span class="hljs-number">34</span> cleaner-offset-checkpoint
drwxr-xr-<span class="hljs-keyword">x</span>. <span class="hljs-number">2</span> root root <span class="hljs-number">4096</span> <span class="hljs-number">11</span>月 <span class="hljs-number">21</span> <span class="hljs-number">22</span>:<span class="hljs-number">25</span> orderMq-<span class="hljs-number">0</span>
drwxr-xr-<span class="hljs-keyword">x</span>. <span class="hljs-number">2</span> root root <span class="hljs-number">4096</span> <span class="hljs-number">11</span>月 <span class="hljs-number">21</span> <span class="hljs-number">22</span>:<span class="hljs-number">25</span> orderMq-<span class="hljs-number">1</span>
drwxr-xr-<span class="hljs-keyword">x</span>. <span class="hljs-number">2</span> root root <span class="hljs-number">4096</span> <span class="hljs-number">11</span>月 <span class="hljs-number">21</span> <span class="hljs-number">23</span>:<span class="hljs-number">13</span> payment-<span class="hljs-number">1</span>
drwxr-xr-<span class="hljs-keyword">x</span>. <span class="hljs-number">2</span> root root <span class="hljs-number">4096</span> <span class="hljs-number">11</span>月 <span class="hljs-number">21</span> <span class="hljs-number">23</span>:<span class="hljs-number">13</span> payment-<span class="hljs-number">2</span>
drwxr-xr-<span class="hljs-keyword">x</span>. <span class="hljs-number">2</span> root root <span class="hljs-number">4096</span> <span class="hljs-number">11</span>月 <span class="hljs-number">21</span> <span class="hljs-number">23</span>:<span class="hljs-number">13</span> payment-<span class="hljs-number">3</span>
-rw-r--r--. <span class="hljs-number">1</span> root root   <span class="hljs-number">64</span> <span class="hljs-number">11</span>月 <span class="hljs-number">22</span> <span class="hljs-number">03</span>:<span class="hljs-number">38</span> recovery-point-offset-checkpoint
-rw-r--r--. <span class="hljs-number">1</span> root root   <span class="hljs-number">64</span> <span class="hljs-number">11</span>月 <span class="hljs-number">22</span> <span class="hljs-number">03</span>:<span class="hljs-number">39</span> replication-offset-checkpoint
[root<span class="hljs-variable">@mini1</span> kafka-logs]<span class="hljs-comment"># cd orderMq-1</span>
[root<span class="hljs-variable">@mini1</span> orderMq-<span class="hljs-number">1</span>]<span class="hljs-comment"># ll</span>
总用量 <span class="hljs-number">4</span>
-rw-r--r--. <span class="hljs-number">1</span> root root <span class="hljs-number">10485760</span> <span class="hljs-number">11</span>月 <span class="hljs-number">21</span> <span class="hljs-number">22</span>:<span class="hljs-number">31</span> <span class="hljs-number">00000000000000000000</span>.<span class="hljs-keyword">index</span>
-rw-r--r--. <span class="hljs-number">1</span> root root      <span class="hljs-number">194</span> <span class="hljs-number">11</span>月 <span class="hljs-number">21</span> <span class="hljs-number">22</span>:<span class="hljs-number">39</span> <span class="hljs-number">00000000000000000000</span>.<span class="hljs-keyword">log</span>
<span class="hljs-comment">#00000000000000000000.log里面是发送的消息内容，乱码就没贴出来了。</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>