---
layout:     post
title:      Spark Streaming on Kafka解析和安装实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/erfucun/article/details/52266500				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>本博文内容主要包括以下几点：</p>

<p>1、Kafka解析； <br>
2、Kafka的安装和实战。</p>



<h2 id="一kafka的概念架构和用例场景">一、Kafka的概念、架构和用例场景：</h2>

<p>1、Kafka的概念： <br>
        Apache Kafka是分布式发布-订阅消息系统。 <br>
        它提供了类似于JMS的特性，但是在设计实现上完全不同，此外它并不是JMS规范的实现。Kafka是一种快速、可扩展的、设计内在就是分布式的，分区的和可复制的提交日志服务。 <br>
        <strong>kafka对消息保存时根据Topic（及发送消息内容）进行归类，发送消息者成为Producer,消息接受者成为Consumer,此外kafka集群有多个kafka实例组成，每个实例(server)成为broker。</strong> <br>
        Kafka就是这样的通信组件，将不同对象组件粘合起来的纽带，且是解耦合方式传递数据。</p>

<p>2，为何使用消息系统： <br>
（1）解耦 <br>
在项目启动之初来预测将来项目会碰到什么需求，是极其困难的。消息系统在处理过程中间插入了一个隐含的、基于数据的接口层，两边的处理过程都要实现这一接口。这允许你独立的扩展或修改两边的处理过程，只要确保它们遵守同样的接口约束。 <br>
（2）冗余 <br>
有些情况下，处理数据的过程会失败。除非数据被持久化，否则将造成丢失。消息队列把数据进行持久化直到它们已经被完全处理，通过这一方式规避了数据丢失风险。许多消息队列所采用的”插入-获取-删除”范式中，在把一个消息从队列中删除之前，需要你的处理系统明确的指出该消息已经被处理完毕，从而确保你的数据被安全的保存直到你使用完毕。 <br>
（3）扩展性 <br>
因为消息队列解耦了你的处理过程，所以增大消息入队和处理的频率是很容易的，只要另外增加处理过程即可。不需要改变代码、不需要调节参数。扩展就像调大电力按钮一样简单。 <br>
（4）灵活性 &amp; 峰值处理能力 <br>
在访问量剧增的情况下，应用仍然需要继续发挥作用，但是这样的突发流量并不常见；如果为以能处理这类峰值访问为标准来投入资源随时待命无疑是巨大的浪费。使用消息队列能够使关键组件顶住突发的访问压力，而不会因为突发的超负荷的请求而完全崩溃。 <br>
（5）可恢复性 <br>
系统的一部分组件失效时，不会影响到整个系统。消息队列降低了进程间的耦合度，所以即使一个处理消息的进程挂掉，加入队列中的消息仍然可以在系统恢复后被处理。 <br>
（6）顺序保证 <br>
在大多使用场景下，数据处理的顺序都很重要。大部分消息队列本来就是排序的，并且能保证数据会按照特定的顺序来处理。Kafka保证一个Partition内的消息的有序性。 <br>
（7）缓冲 <br>
在任何重要的系统中，都会有需要不同的处理时间的元素。例如，加载一张图片比应用过滤器花费更少的时间。消息队列通过一个缓冲层来帮助任务最高效率的执行———写入队列的处理会尽可能的快速。该缓冲有助于控制和优化数据流经过系统的速度。 <br>
（8）异步通信 <br>
很多时候，用户不想也不需要立即处理消息。消息队列提供了异步处理机制，允许用户把一个消息放入队列，但并不立即处理它。想向队列中放入多少消息就放多少，然后在需要的时候再去处理它们。        </p>

<p><strong>Apache Kafka与传统消息系统相比，有以下不同的特点：</strong> <br>
（1）分布式系统，易于向外扩展； <br>
（2）在线低延迟，同时为发布和订阅提供高吞吐量； <br>
（3）将消息存储到磁盘，因此可以处理1天甚至1周前内容</p>

<p>3、Kafka的架构： <br>
<img src="https://img-blog.csdn.net/20160820203114715" alt="这里写图片描述" title=""></p>

<p>Kafka既然具备消息系统的基本功能，那么就必然会有组成消息系统的组件：Topic，Producer和Consumer。Kafka还有其特殊的Kafka Cluster组件。</p>

<p>Topic主题： <br>
        代表一种数据的类别或类型，工作、娱乐、生活有不同的Topic，生产者需要说明把说明数据分别放在那些Topic中，里面就是一个个小对象，并将数据数据推到Kafka，消费者获取数据是pull的过程。一组相同类型的消息数据流。这些消息在Kafka会被分区存放，并且有多个副本，以防数据丢失。每个分区的消息是顺序写入的，并且不可改写。 <br>
<img src="https://img-blog.csdn.net/20160820203236154" alt="这里写图片描述" title=""></p>

<ul>
<li><p>Producer（生产者）：把数据推到Kafka系统的任何对象。</p>

<ul><li>Kafka Cluster（Kafka集群）：把推到Kafka系统的消息保存起来的一组服务器，也叫Broker。因为Kafka集群用到了Zookeeper作为底层支持框架，所以由一个选出的服务器作为Leader来处理所有消息的读和写的请求，其他服务器作为Follower接受Leader的广播同步备份数据，以备灾难恢复时用。</li></ul></li>
<li><p>Consumer（消费者）：从Kafka系统订阅消息的任何对象。</p></li>
</ul>

<p>消费者可以有多个，并且某些消费者还可以组成Consumer Group。多个Consumer Group之间组成消息广播的关系，所以各个Group可以拉相同的消息数据。在Consumer Group内部，各消费者之间对Consumer Group拉出来的消息数据是队列先进先出的关系，某个消息数据只能给该Group的一个消费者使用。</p>

<p><img src="https://img-blog.csdn.net/20160820203348499" alt="这里写图片描述" title=""></p>

<p>数据传输基于kernel（内核）级别的（传输速度接近0拷贝-ZeroCopy）、没有用户空间的参与。Linux本身是软件，软件启动时第一个启动进程叫init，在init进程启动后会进入用户空间；例如：在分布式系统中，机器A上的应用程序需要读取机器B上的Java服务数据，由于Java程序对应的JVM是用户空间级别而且数据在磁盘上，A上应用程序读取数据时会首先进入机器B上的内核空间再进入机器B的用户空间，读取用户空间的数据后，数据再经过B机器上的内核空间分发到网络中，机器A网卡接收到传输过来的数据后再将数据写入A机器的内核空间，从而最终将数据传输给A的用户空间进行处理。如下图：</p>

<p><img src="https://img-blog.csdn.net/20160821080916727" alt="这里写图片描述" title=""></p>

<p>外部系统从Java程序中读取数据，传输给内核空间并依赖网卡将数据写入到网络中，从而把数据传输出去。其实Java本身是内核的一层外衣，Java Socket编程，操作的各种数据都是在JVM的用户空间中进行的。而Kafka操作数据是放在内核空间的，通常内核空间处理数据的速度比用户空间快上万倍，所以通过kafka可以实现高速读、写数据</p>



<h2 id="二kafka的安装">二：Kafka的安装：</h2>

<p><strong>1、最详细的Zookeeper安装和配置</strong></p>

<p>Kafka集群模式需要提前安装好Zookeeper。 <br>
-       提示：Kafka单例模式不需要安装额外的Zookeeper，可以使用内置的Zookeeper。 <br>
-       Kafka集群模式需要至少3台服务器。本课实战用到的服务器Hostname：master，slave1，slave2。 <br>
-       本博文中用到的Zookeeper版本是Zookeeper-3.4.6。 <br>
进入<a href="http://www.apache.org/dyn/closer.cgi/zookeeper/" rel="nofollow">http://www.apache.org/dyn/closer.cgi/zookeeper/</a>，你可以选择其他镜像网址去下载，用官网推荐的镜像：<a href="http://mirror.bit.edu.cn/apache/zookeeper/" rel="nofollow">http://mirror.bit.edu.cn/apache/zookeeper/</a>提示：下载官网里的的zookeeper-3.4.6.tar.gz  安装文件。</p>

<p>1)    安装Zookeeper</p>

<p>提示：下面的步骤发生在master服务器。</p>

<p>以ubuntu14.04举例，把下载好的文件放到root/Downloads目录，用下面的命令解压：</p>

<p>cd Downloads</p>

<p>tar -zxvf zookeeper-3.4.6.tar.gz</p>

<p>解压后在Downloads目录会多出一个zookeeper-3.4.6的新目录，用下面的命令把它剪切到指定目录即安装好Zookeeper了：</p>

<p>cd Downloads</p>

<p>mv zookeeper-3.4.6 /usr/local/spark</p>

<p>之后在/usr/local/spark目录会多出一个zookeeper-3.4.6的新目录。下面我们讲如何配置安装好的Zookeeper。</p>

<p>2)    配置Zookeeper</p>

<p>提示：下面的步骤发生在master服务器。</p>

<p>配置.bashrc <br>
-       打开文件：vi ~/.bashrc</p>

<ul>
<li>在PATH配置行前添加：</li>
</ul>

<p>export ZOOKEEPER_HOME=/usr/local/spark/zookeeper-3.4.6</p>

<ul>
<li>最后修改PATH：</li>
</ul>

<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-keyword">export</span> PATH=/usr/local/eclipse:<span class="hljs-variable">${JAVA_HOME}</span>/bin:<span class="hljs-variable">${ZOOKEEPER_HOME}</span>/bin:<span class="hljs-variable">${HADOOP_HOME}</span>/bin:<span class="hljs-variable">${HADOOP_HOME}</span>/sbin:<span class="hljs-variable">${SPARK_HOME}</span>/bin:<span class="hljs-variable">${SPARK_HOME}</span>/sbin:<span class="hljs-variable">${SCALA_HOME}</span>/bin:<span class="hljs-variable">${HIVE_HOME}</span>/bin:<span class="hljs-variable">${FLUME_HOME}</span>/bin:<span class="hljs-variable">$PATH</span></code></pre>

<ul>
<li>使配置的环境变量立即生效：source ~/.bashrc</li>
</ul>

<p>3）创建data目录 <br>
-       cd $ZOOKEEPER_HOME</p>

<ul>
<li>mkdir data</li>
</ul>

<p>4）创建并打开zoo.cfg文件 <br>
-       cd $ZOOKEEPER_HOME/conf</p>

<ul>
<li><p>cp zoo_sample.cfg zoo.cfg</p></li>
<li><p>vi zoo.cfg</p></li>
</ul>

<p>5）配置zoo.cfg，在zoo.cfg的文档中输入下面的内容：</p>



<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor"># The number of milliseconds of each tick</span>
tickTime=<span class="hljs-number">2000</span>
<span class="hljs-preprocessor"># The number of ticks that the initial </span>
<span class="hljs-preprocessor"># synchronization phase can take</span>
initLimit=<span class="hljs-number">10</span>
<span class="hljs-preprocessor"># The number of ticks that can pass between </span>
<span class="hljs-preprocessor"># sending a request and getting an acknowledgement</span>
syncLimit=<span class="hljs-number">5</span>
<span class="hljs-preprocessor"># the directory where the snapshot is stored.</span>
<span class="hljs-preprocessor"># do not use /tmp for storage, /tmp here is just </span>
<span class="hljs-preprocessor"># example sakes.</span>
<span class="hljs-preprocessor">#dataDir=/tmp/zookeeper</span>
<span class="hljs-preprocessor"># the port at which the clients will connect</span>
<span class="hljs-preprocessor">#clientPort=2181</span>
<span class="hljs-preprocessor"># the maximum number of client connections.</span>
<span class="hljs-preprocessor"># increase this if you need to handle more clients</span>
<span class="hljs-preprocessor">#maxClientCnxns=60</span>
<span class="hljs-preprocessor">#</span>
<span class="hljs-preprocessor"># Be sure to read the maintenance section of the </span>
<span class="hljs-preprocessor"># administrator guide before turning on autopurge.</span>
<span class="hljs-preprocessor">#http://zookeeper.apache.org/doc/current/zookeeperAdmin.htmlsc_maintenance</span>
<span class="hljs-preprocessor">#</span>
<span class="hljs-preprocessor"># The number of snapshots to retain in dataDir</span>
<span class="hljs-preprocessor">#autopurge.snapRetainCount=3</span>
<span class="hljs-preprocessor"># Purge task interval in hours</span>
<span class="hljs-preprocessor"># Set to "0" to disable auto purge feature</span>
<span class="hljs-preprocessor">#autopurge.purgeInterval=1</span>
<span class="hljs-preprocessor"># 配置Zookeeper的日志和服务器身份证号等数据存放的目录。</span>
<span class="hljs-preprocessor"># 千万不要用默认的/tmp/zookeeper目录，因为/tmp目录的数据容易被意外删除。</span>
dataDir=../data
<span class="hljs-preprocessor"># Zookeeper与客户端连接的端口</span>
clientPort=<span class="hljs-number">2181</span>
<span class="hljs-preprocessor"># 在文件最后新增3行配置每个服务器的2个重要端口：Leader端口和选举端口</span>
<span class="hljs-preprocessor"># server.A=B：C：D：其中 A 是一个数字，表示这个是第几号服务器；</span>
<span class="hljs-preprocessor"># B 是这个服务器的hostname或ip地址；</span>
<span class="hljs-preprocessor"># C 表示的是这个服务器与集群中的 Leader 服务器交换信息的端口；</span>
<span class="hljs-preprocessor"># D 表示的是万一集群中的 Leader 服务器挂了，需要一个端口来重新进行选举，</span>
<span class="hljs-preprocessor"># 选出一个新的 Leader，而这个端口就是用来执行选举时服务器相互通信的端口。</span>
<span class="hljs-preprocessor"># 如果是伪集群的配置方式，由于 B 都是一样，所以不同的 Zookeeper 实例通信</span>
<span class="hljs-preprocessor"># 端口号不能一样，所以要给它们分配不同的端口号。</span>

server<span class="hljs-number">.1</span>=master:<span class="hljs-number">2888</span>:<span class="hljs-number">3888</span>
server<span class="hljs-number">.2</span>=slave1:<span class="hljs-number">2888</span>:<span class="hljs-number">3888</span>
server<span class="hljs-number">.3</span>=slave2:<span class="hljs-number">2888</span>:<span class="hljs-number">3888</span></code></pre>

<p>6）创建并打开myid文件 <br>
-       cd $ZOOKEEPER_HOME/data <br>
-       touch myid <br>
-       vi myid</p>

<p>配置myid <br>
按照zoo.cfg的配置，myid的内容就是1。 <br>
7)    同步master的安装和配置到slave1和slave2</p>

<ul>
<li>在master服务器上运行下面的命令</li>
</ul>

<p>cd /root</p>

<p>scp ./.bashrc root@slave1:/root</p>

<p>scp ./.bashrc root@slave2:/root</p>

<p>cd /usr/local/spark</p>

<p>scp -r ./zookeeper-3.4.6 root@slave1:/usr/local/spark</p>

<p>scp -r ./zookeeper-3.4.6 root@slave2:/usr/local/spark</p>

<ul>
<li>在slave1服务器上运行下面的命令</li>
</ul>

<p>vi $ZOOKEEPER_HOME/data/myid</p>

<p>按照zoo.cfg的配置，myid的内容就是2。</p>

<ul>
<li>在slave2服务器上运行下面的命令</li>
</ul>

<p>vi $ZOOKEEPER_HOME/data/myid</p>

<p>按照zoo.cfg的配置，myid的内容就是3。</p>

<p>8)    启动Zookeeper服务</p>

<ul>
<li>在master服务器上运行下面的命令</li>
</ul>

<p>zkServer.sh start</p>

<ul>
<li>在slave1服务器上运行下面的命令</li>
</ul>

<p>source /root/.bashrc</p>

<p>zkServer.sh start <br>
在slave1服务器上运行下面的命令</p>

<p>source /root/.bashrc</p>

<p>zkServer.sh start</p>

<ul>
<li>在slave2服务器上运行下面的命令</li>
</ul>

<p>source /root/.bashrc</p>

<p>zkServer.sh start</p>

<p>5)    验证Zookeeper是否安装和启动成功</p>

<ul>
<li>在master服务器上运行(1)命令jps:</li>
</ul>

<p><img src="https://img-blog.csdn.net/20160821102545100" alt="这里写图片描述" title=""></p>

<p>(2)zkServer.sh status命令： <br>
<img src="https://img-blog.csdn.net/20160821102657866" alt="这里写图片描述" title=""></p>

<ul>
<li>在slave1服务器上运行命令：jps和zkServer.sh status</li>
</ul>

<p>source /root/.bashrc</p>

<p><img src="https://img-blog.csdn.net/20160821102734366" alt="这里写图片描述" title=""></p>

<ul>
<li><p>在slave2服务器上运行命令：jps和zkServer.sh status  ……</p>

<p>至此，代表Zookeeper已经安装和配置成功。</p></li>
</ul>

<p><strong>2,Kafka的安装配置</strong></p>

<p>本博文中用到的Kafka版本是Kafka-2.10-0.9.0.1。</p>

<p>1)    下载Kafka  <br>
进入<a href="http://kafka.apache.org/downloads.html" rel="nofollow">http://kafka.apache.org/downloads.html</a>，左键单击kafka_2.10-0.9.0.1.tgz。下载的为<a href="http://mirrors.hust.edu.cn/apache/kafka/0.9.0.1/kafka_2.10-0.9.0.1.tgz" rel="nofollow">http://mirrors.hust.edu.cn/apache/kafka/0.9.0.1/kafka_2.10-0.9.0.1.tgz</a> 。</p>

<p>1)    安装Kafka</p>

<p>提示：下面的步骤发生在master服务器。 <br>
以ubuntu14.04举例，把下载好的文件放到/root目录，用下面的命令解压： <br>
cd /root <br>
tar -zxvf kafka_2.10-0.9.0.1.tgz <br>
解压后在/root目录会多出一个kafka_2.10-0.9.0.1的新目录，用下面的命令把它剪切到指定目录即安装好Kafka了： <br>
cd /root <br>
mv kafka_2.10-0.9.0.1 /usr/local <br>
之后在/usr/local目录会多出一个kafka_2.10-0.9.0.1的新目录。下面我们讲如何配置安装好的Kafka。</p>

<p>2)    配置Kafka <br>
提示：下面的步骤发生在master服务器。 <br>
    (1)配置.bashrc <br>
-       打开文件：vim ~/.bashrc</p>

<ul>
<li>在PATH配置行前添加：</li>
</ul>



<pre class="prettyprint"><code class=" hljs autohotkey">export KAFK<span class="hljs-built_in">A_HOME</span>=/usr/local/kafk<span class="hljs-built_in">a_2</span>.<span class="hljs-number">10</span>-<span class="hljs-number">0.9</span>.<span class="hljs-number">0.1</span></code></pre>

<ul>
<li>最后修改PATH：</li>
</ul>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-keyword">export</span> PATH=/usr/local/eclipse:<span class="hljs-variable">${JAVA_HOME}</span>/bin:<span class="hljs-variable">${KAFKA_HOME}</span>/bin:<span class="hljs-variable">${ZOOKEEPER_HOME}</span>/bin:<span class="hljs-variable">${HADOOP_HOME}</span>/bin:<span class="hljs-variable">${HADOOP_HOME}</span>/sbin:<span class="hljs-variable">${SPARK_HOME}</span>/bin:<span class="hljs-variable">${SPARK_HOME}</span>/sbin:<span class="hljs-variable">${SCALA_HOME}</span>/bin:<span class="hljs-variable">${HIVE_HOME}</span>/bin:<span class="hljs-variable">${FLUME_HOME}</span>/bin:<span class="hljs-variable">$PATH</span>
</code></pre>

<ul>
<li><p>使配置的环境变量立即生效：source ~/.bashrc</p>

<p>(2)配置server.properties:</p></li>
<li><p>cd $KAFKA_HOME/config</p></li>
<li><p>vi server.properties</p></li>
</ul>

<p>配置server.properties，初步修改为下方这样，后期将会进行跟复杂的修改，已达到性能最优情况：</p>



<pre class="prettyprint"><code class=" hljs avrasm">broker<span class="hljs-preprocessor">.id</span>=<span class="hljs-number">0</span>
port=<span class="hljs-number">9092</span>
zookeeper<span class="hljs-preprocessor">.connect</span>=master:<span class="hljs-number">2181</span>,slave1:<span class="hljs-number">2181</span>,slave2:<span class="hljs-number">2181</span></code></pre>

<p>3)    同步master的安装和配置到slave1和slave2</p>

<ul>
<li>在master服务器上运行下面的命令</li>
</ul>

<p>cd /root</p>

<p>scp ./.bashrc root@slave1:/root</p>

<p>scp ./.bashrc root@slave2:/root</p>

<p>cd /usr/local</p>

<p>scp -r ./kafka_2.10-0.9.0.1 root@slave1:/usr/local</p>

<p>scp -r ./kafka_2.10-0.9.0.1 root@slave2:/usr/local</p>

<ul>
<li>在slave1服务器上运行下面的命令</li>
</ul>

<p>vi $KAFKA_HOME/config/server.properties</p>

<p>修改broker.id=1。</p>

<ul>
<li>在slave2服务器上运行下面的命令</li>
</ul>

<p>vi $KAFKA_HOME/config/server.properties</p>

<p>修改broker.id=2。</p>

<p>4)    启动Kafka服务</p>

<ul>
<li>在master服务器上运行下面的命令</li>
</ul>

<p>cd $KAFKA_HOME/bin</p>

<p>kafka-server-start.sh ../config/server.properties &amp;</p>

<ul>
<li>在slave1服务器上运行下面的命令</li>
</ul>

<p>source /root/.bashrc <br>
cd $KAFKA_HOME/bin <br>
kafka-server-start.sh ../config/server.properties &amp;</p>

<ul>
<li>在slave2服务器上运行下面的命令</li>
</ul>

<p>source /root/.bashrc <br>
cd $KAFKA_HOME/bin <br>
kafka-server-start.sh ../config/server.properties &amp;</p>

<p>5)    验证Kafka是否安装和启动成功</p>

<ul>
<li>在任意服务器上运行命令创建Topic“HelloKafka”：</li>
</ul>

<p>kafka-topics.sh –create –zookeeper master:2181,slave1:2181,slave2:2181 –replication-factor 3 –partitions 1 –topic HelloKafka</p>

<ul>
<li>在任意服务器上运行命令为创建的Topic“HelloKafka”生产一些消息：</li>
</ul>

<p>kafka-console-producer.sh –broker-list master:9092,slave1:9092,slave2:9092 –topic HelloKafka</p>

<p>输入下面的消息内容：</p>

<p><img src="https://img-blog.csdn.net/20160821113743226" alt="这里写图片描述" title=""></p>

<ul>
<li>在任意服务器上运行命令从指定的Topic“HelloKafka”上消费（拉取）消息：</li>
</ul>

<p>kafka-console-consumer.sh –zookeeper master:2181,slave1:2181,slave2:2181 –from-beginning –topic HelloKafka</p>

<p>过一会儿，你会看到打印的消息内容：</p>

<p><img src="https://img-blog.csdn.net/20160821113800195" alt="这里写图片描述" title=""></p>

<ul>
<li>在任意服务器上运行命令查看所有的Topic名字：</li>
</ul>

<p>kafka-topics.sh –list –zookeeper master:2181,slave1:2181,slave2:2181</p>

<ul>
<li>在任意服务器上运行命令查看指定Topic的概况：</li>
</ul>

<p>kafka-topics.sh –describe –zookeepermaster:2181,slave1:2181,slave2:2181 –topic HelloKafka</p>

<p>至此，代表Kafka已经安装和配置成功。</p>

<p>博文内容源自DT大数据梦工厂Spark课程。相关课程内容视频可以参考：  <br>
百度网盘链接：<a href="http://pan.baidu.com/s/1slvODe1" rel="nofollow">http://pan.baidu.com/s/1slvODe1</a>（如果链接失效或需要后续的更多资源，请联系QQ460507491或者微信号：DT1219477246 获取上述资料）。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>