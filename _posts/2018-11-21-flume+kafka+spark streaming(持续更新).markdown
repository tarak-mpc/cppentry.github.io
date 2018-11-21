---
layout:     post
title:      flume+kafka+spark streaming(持续更新)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div id="article_content" class="article_content tracking-ad">
<div class="markdown_views">
<h2 id="kafka"><a name="t0"></a>kafka</h2>
<p><a href="http://orchome.com/5" rel="nofollow">kafka中文教程</a></p>
<p>Kafka是一种高吞吐量的分布式发布订阅消息系统，它可以处理消费者规模的网站中的所有动作流数据。 kafka的设计初衷是希望作为一个统一的信息收集平台,能够实时的收集反馈信息,并需要能够支撑较大的数据量,且具备良好的容错能力.
<br>
Apache kafka是消息中间件的一种。 <br>
一 、术语介绍 <br><strong><em>Broker</em></strong> <br>
Kafka集群包含一个或多个服务器，这种服务器被称为broker。 <br><strong><em>Topic</em></strong> <br>
每条发布到Kafka集群的消息都有一个类别，这个类别被称为Topic。（物理上不同Topic的消息分开存储，逻辑上一个Topic的消息虽然保存于一个或多个broker上但用户只需指定消息的Topic即可生产或消费数据而不必关心数据存于何处）。每个topic都具有这两种模式：（队列：消费者组（consumer group）允许同名的消费者组成员瓜分处理；发布订阅：允许你广播消息给多个消费者组（不同名））。
<br><strong><em>Partition</em></strong> <br>
Partition是物理上的概念，每个Topic包含一个或多个Partition. <br><strong><em>Producer</em></strong> <br>
负责发布消息到Kafka broker，比如flume采集机就是Producer。 <br><strong><em>Consumer</em></strong> <br>
消息消费者，向Kafka broker读取消息的客户端。比如<a href="http://lib.csdn.net/base/hadoop" rel="nofollow" class="replace_word" title="Hadoop知识库" style="color:#df3434;font-weight:bold;">Hadoop</a>机器就是Consumer。
<br><strong><em>Consumer Group</em></strong> <br>
每个Consumer属于一个特定的Consumer Group（可为每个Consumer指定group name，若不指定group name则属于默认的group）。</p>
<p>二、使用场景 <br>
1、Messaging <br>
对于一些常规的消息系统,kafka是个不错的选择;partitons/replication和容错,可以使kafka具有良好的扩展性和性能优势.不过到目前为止,我们应该很清楚认识到,kafka并没有提供JMS中的”事务性”“消息传输担保(消息确认机制)”“消息分组”等企业级特性;kafka只能使用作为”常规”的消息系统,在一定程度上,尚未确保消息的发送与接收绝对可靠(比如,消息重发,消息发送丢失等)
<br>
2、Websit activity tracking <br>
kafka可以作为”网站活性跟踪”的最佳工具;可以将网页/用户操作等信息发送到kafka中.并实时监控,或者离线统计分析等 <br>
3、Log Aggregation <br>
kafka的特性决定它非常适合作为”日志收集中心”;application可以将操作日志”批量”“异步”的发送到kafka集群中,而不是保存在本地或者DB中;kafka可以批量提交消息/压缩消息等,这对producer端而言,几乎感觉不到性能的开支.此时consumer端可以使<a href="http://lib.csdn.net/base/hadoop" rel="nofollow" class="replace_word" title="Hadoop知识库" style="color:#df3434;font-weight:bold;">hadoop</a>等其他系统化的存储和分析系统.
<br>
4、它应用于2大类应用 <br>
构建实时的流数据管道，可靠地获取系统和应用程序之间的数据。 <br>
构建实时流的应用程序，对数据流进行转换或反应。</p>
<p>三、分布式 <br>
Log的分区被分布到集群中的多个服务器上。每个服务器处理它分到的分区。 根据配置每个分区还可以复制到其它服务器作为备份容错。 每个分区有一个leader，零或多个follower。Leader处理此分区的所有的读写请求，而follower被动的复制数据。如果leader宕机，其它的一个follower会被推举为新的leader。 一台服务器可能同时是一个分区的leader，另一个分区的follower。 这样可以平衡负载，避免所有的请求都只让一台或者某几台服务器处理。</p>
<p>四、消息处理顺序 <br>
Kafka保证消息的顺序不变。 在这一点上Kafka做的更好，尽管并没有完全解决上述问题。 Kafka采用了一种分而治之的策略：分区。 因为Topic分区中消息只能由消费者组中的唯一一个消费者处理，所以消息肯定是按照先后顺序进行处理的。但是它也仅仅是保证Topic的一个分区顺序处理，不能保证跨分区的消息先后处理顺序。 所以，如果你想要顺序的处理Topic的所有消息，那就只提供一个分区。</p>
<p>五、安装 <br><a href="http://orchome.com/6" rel="nofollow">kafka安装和启动</a></p>
<p>六、Key和Value <br>
Kafka是一个分布式消息系统，Producer生产消息并推送(Push)给Broker，然后Consumer再从Broker那里取走(Pull)消息。Producer生产的消息就是由Message来表示的，对用户来讲，它就是键-值对。</p>
<pre class="prettyprint"><code class="hljs mathematica has-numbering"><span class="hljs-keyword">Message</span> =&gt; Crc MagicByte <span class="hljs-keyword">Attributes</span> Key <span class="hljs-keyword">Value</span></code></pre><ul class="pre-numbering" style="opacity:0;"><li>1</li></ul><div class="save_code tracking-ad"><a><img src="http://static.blog.csdn.net/images/save_snippets.png" alt="save_snippets.png"></a></div><ul class="pre-numbering"><li>1</li></ul><p>kafka会根据传进来的key计算其分区，但key可以不传，可以为null，空的话，producer会把这条消息随机的发送给一个partition。</p>
<p><img src="https://img-blog.csdn.net/20170321163742094?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHV5YW9xaWFveWFvZ2U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>
<p>MessageSet用来组合多条Message，它在每条Message的基础上加上了Offset和MessageSize，其结构是：</p>
<pre class="prettyprint"><code class="hljs mathematica has-numbering">MessageSet =&gt; [<span class="hljs-keyword">Offset</span> MessageSize <span class="hljs-keyword">Message</span>]</code></pre><ul class="pre-numbering" style="opacity:0;"><li>1</li></ul><div class="save_code tracking-ad"><a><img src="http://static.blog.csdn.net/images/save_snippets.png" alt="save_snippets.png"></a></div><ul class="pre-numbering"><li>1</li></ul><p>它的含义是MessageSet是个数组，数组的每个元素由三部分组成，分别是Offset，MessageSize和Message，它们的含义分别是： <br><img src="https://img-blog.csdn.net/20170321163900538?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHV5YW9xaWFveWFvZ2U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>
<p>七、小例子 <br>
1.启动ZooKeeper <br>
进入kafka目录，加上daemon表示在后台启动，不占用当前的命令行窗口。 <br>
bin/zookeeper-server-start.sh -daemon config/zookeeper.properties <br>
如果要关闭，下面这个 <br>
bin/zookeeper-server-stop.sh <br>
ZooKeeper 的端口号是2181，输入jps查看进程号是QuorumPeerMain <br>
2.启动kafka <br>
在server.properties中加入，第一个是保证你删topic可以删掉，第二个不然的话就报topic找不到的错误： <br>
delete.topic.enable=true <br>
listeners=PLAINTEXT://localhost:9092 <br>
然后： <br>
bin/kafka-server-start.sh -daemon config/server.properties <br>
如果要关闭，下面这个 <br>
bin/kafka-server-stop.sh <br>
Kafka的端口号是9092，输入jps查看进程号是Kafka <br>
3.创建一个主题(topic) <br>
创建一个名为“test”的Topic，只有一个分区和一个备份： <br>
bin/kafka-topics.sh –create –zookeeper localhost:2181 –replication-factor 1 –partitions 1 –topic test
<br>
创建好之后，可以通过运行以下命令，查看已创建了哪些topic： <br>
bin/kafka-topics.sh –list –zookeeper localhost:2181 <br>
查看具体topic的信息： <br>
bin/kafka-topics.sh –describe –zookeeper localhost:2181 –topic test <br>
4.发送消息 <br>
启动kafka生产者： <br>
bin/kafka-console-producer.sh –broker-list localhost:9092 –topic test <br>
5.接收消息 <br>
新开一个命令行窗口，启动kafka消费者： <br>
bin/kafka-console-consumer.sh –zookeeper localhost:2181 –topic test –from-beginning
<br>
6.最后 <br>
在producer窗口中输入消息，可以在consumer窗口中显示： <br><img src="https://img-blog.csdn.net/20170327162727646?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHV5YW9xaWFveWFvZ2U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""><br><img src="https://img-blog.csdn.net/20170327162740799?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHV5YW9xaWFveWFvZ2U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>
<h2 id="spark-streaming"><a name="t1"></a>spark streaming</h2>
<p><a href="http://www.kancloud.cn/kancloud/spark-programming-guide/51531" rel="nofollow">spark中文学习指南</a></p>
<p><a href="http://lib.csdn.net/base/spark" rel="nofollow" class="replace_word" title="Apache Spark知识库" style="color:#df3434;font-weight:bold;">Spark</a> Streaming是一种构建在Spark上的实时计算框架，它扩展了Spark处理大规模流式数据的能力。</p>
<p>Spark Streaming的优势在于： <br>
能运行在100+的结点上，并达到秒级延迟。 <br>
使用基于内存的Spark作为执行引擎，具有高效和容错的特性。 <br>
能集成Spark的批处理和交互查询。 <br>
为实现复杂的<a href="http://lib.csdn.net/base/datastructure" rel="nofollow" class="replace_word" title="算法与数据结构知识库" style="color:#df3434;font-weight:bold;">算法</a>提供和批处理类似的简单接口。</p>
<p>首先，Spark Streaming把实时输入数据流以时间片Δt （如1秒）为单位切分成块。Spark Streaming会把每块数据作为一个RDD，并使用RDD操作处理每一小块数据。每个块都会生成一个Spark Job处理，最终结果也返回多块。
<br>
在Spark Streaming中，则通过操作DStream（表示数据流的RDD序列）提供的接口，这些接口和RDD提供的接口类似。 <br>
正如Spark Streaming最初的目标一样，它通过丰富的API和基于内存的高速计算引擎让用户可以结合流式处理，批处理和交互查询等应用。因此Spark Streaming适合一些需要历史数据和实时数据结合分析的应用场合。当然，对于实时性要求不是特别高的应用也能完全胜任。另外通过RDD的数据重用机制可以得到更高效的容错处理。</p>
<p>当一个上下文（context）定义之后，你必须按照以下几步进行操作： <br>
定义输入源； <br>
准备好流计算指令； <br>
利用streamingContext.start()方法接收和处理数据； <br>
处理过程将一直持续，直到streamingContext.stop()方法被调用。</p>
<p>可以利用已经存在的SparkContext对象创建StreamingContext对象：</p>
<pre class="prettyprint"><code class="hljs r has-numbering">val sc = <span class="hljs-keyword">...</span>                // existing SparkContext
val ssc = new StreamingContext(sc, Seconds(<span class="hljs-number">1</span>))</code></pre><ul class="pre-numbering" style="opacity:0;"><li>1</li><li>2</li></ul><div class="save_code tracking-ad"><a><img src="http://static.blog.csdn.net/images/save_snippets.png" alt="save_snippets.png"></a></div><ul class="pre-numbering"><li>1</li><li>2</li></ul><p><strong><em>窗口函数</em></strong> <br>
对于spark streaming中的窗口函数，参见： <br><a href="http://blog.csdn.net/lovehuangjiaju/article/details/50095491" rel="nofollow">窗口函数解释</a></p>
<p>对非(K,V)形式的RDD 窗口化reduce： <br>
1.reduceByWindow(reduceFunc, windowDuration, slideDuration) <br>
2.reduceByWindow(reduceFunc, invReduceFunc, windowDuration, slideDuration) </p>
<p>对(K,V)形式RDD 按Key窗口化reduce: <br>
1.reduceByKeyAndWindow(reduceFunc, windowDuration, slideDuration) <br>
2.reduceByKeyAndWindow(reduceFunc, invReduceFunc, windowDuration, slideDuration, numPartitions, filterFunc)
<br><strong>从效率来说，应选择带有invReduceFunc的方法。</strong></p>
<p>可以通过在多个RDD或者批数据间重用连接对象做更进一步的优化。开发者可以保有一个静态的连接对象池，重复使用池中的对象将多批次的RDD推送到外部系统，以进一步节省开支:</p>
<pre class="prettyprint"><code class="hljs coffeescript has-numbering"> dstream.foreachRDD(rdd<span class="hljs-function"> =&gt;</span> {
      rdd.foreachPartition(partitionOfRecords<span class="hljs-function"> =&gt;</span> {
          <span class="hljs-regexp">//</span> ConnectionPool <span class="hljs-keyword">is</span> a static, lazily initialized pool <span class="hljs-keyword">of</span> connections
          val connection = ConnectionPool.getConnection()
          partitionOfRecords.foreach(record<span class="hljs-function"> =&gt;</span> connection.send(record))
          ConnectionPool.returnConnection(connection)  <span class="hljs-regexp">//</span> <span class="hljs-keyword">return</span> to the pool <span class="hljs-keyword">for</span> future reuse
      })
  })</code></pre><ul class="pre-numbering" style="opacity:0;"><li>1</li><li>2</li><li>3</li><li>4</li><li>5</li><li>6</li><li>7</li><li>8</li></ul><div class="save_code tracking-ad"><a><img src="http://static.blog.csdn.net/images/save_snippets.png" alt="save_snippets.png"></a></div><ul class="pre-numbering"><li>1</li><li>2</li><li>3</li><li>4</li><li>5</li><li>6</li><li>7</li><li>8</li></ul><p>spark执行时间是少了，但<a href="http://lib.csdn.net/base/mysql" rel="nofollow" class="replace_word" title="MySQL知识库" style="color:#df3434;font-weight:bold;">数据库</a>压力比较大，会一直占资源。</p>
<p><strong><em>小例子：</em></strong></p>
<pre class="prettyprint"><code class="hljs scala has-numbering"><span class="hljs-keyword">package</span> SparkStreaming

<span class="hljs-keyword">import</span> org.apache.spark._
<span class="hljs-keyword">import</span> org.apache.spark.storage.StorageLevel
<span class="hljs-keyword">import</span> org.apache.spark.streaming._

<span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">Spark_streaming_Test</span> {</span>
  <span class="hljs-keyword">def</span> main(args: Array[String]): Unit = {
    <span class="hljs-comment">//local[2]表示在本地建立2个working线程</span>
    <span class="hljs-comment">//当运行在本地，如果你的master URL被设置成了“local”，这样就只有一个核运行任务。这对程序来说是不足的，因为作为receiver的输入DStream将会占用这个核，这样就没有剩余的核来处理数据了。</span>
    <span class="hljs-comment">//所以至少得2个核，也就是local[2]</span>
    <span class="hljs-keyword">val</span> conf = <span class="hljs-keyword">new</span> SparkConf().setMaster(<span class="hljs-string">"local[2]"</span>).setAppName(<span class="hljs-string">"NetworkWordCount"</span>)
    <span class="hljs-comment">//时间间隔是1秒</span>
    <span class="hljs-keyword">val</span> ssc = <span class="hljs-keyword">new</span> StreamingContext(conf, Seconds(<span class="hljs-number">1</span>))
    <span class="hljs-comment">//有滑动窗口时，必须有checkpoint</span>
    ssc.checkpoint(<span class="hljs-string">"F:\\checkpoint"</span>)
    <span class="hljs-comment">//DStream是一个基类</span>
    <span class="hljs-comment">//ssc.socketTextStream() 将创建一个 SocketInputDStream；这个 InputDStream 的 SocketReceiver 将监听服务器 9999 端口</span>
    <span class="hljs-comment">//ssc.socketTextStream()将 new 出来一个 DStream 具体子类 SocketInputDStream 的实例。</span>
    <span class="hljs-keyword">val</span> lines = ssc.socketTextStream(<span class="hljs-string">"192.168.1.66"</span>, <span class="hljs-number">9999</span>, StorageLevel.MEMORY_AND_DISK_SER)
    <span class="hljs-comment">//    val lines = ssc.textFileStream("F:\\scv")</span>
    <span class="hljs-keyword">val</span> words = lines.flatMap(_.split(<span class="hljs-string">" "</span>)) <span class="hljs-comment">// DStream transformation</span>
    <span class="hljs-keyword">val</span> pairs = words.map(word =&gt; (word, <span class="hljs-number">1</span>)) <span class="hljs-comment">// DStream transformation</span>
    <span class="hljs-comment">//    val wordCounts = pairs.reduceByKey(_ + _) // DStream transformation</span>
    <span class="hljs-comment">//每隔3秒钟，计算过去5秒的词频，显然一次计算的内容与上次是有重复的。如果不想重复，把2个时间设为一样就行了。</span>
    <span class="hljs-comment">//    val windowedWordCounts = pairs.reduceByKeyAndWindow((a:Int,b:Int) =&gt; (a + b), Seconds(5), Seconds(3))</span>
    <span class="hljs-keyword">val</span> windowedWordCounts = pairs.reduceByKeyAndWindow(_ + _, _ - _, Seconds(<span class="hljs-number">5</span>), Seconds(<span class="hljs-number">3</span>))
    windowedWordCounts.filter(x =&gt; x._2 != <span class="hljs-number">0</span>).print()
    <span class="hljs-comment">//    wordCounts.print() // DStream output，打印每秒计算的词频</span>
    <span class="hljs-comment">//需要注意的是，当以上这些代码被执行时，Spark Streaming仅仅准备好了它要执行的计算，实际上并没有真正开始执行。在这些转换操作准备好之后，要真正执行计算，需要调用如下的方法</span>
    ssc.start() <span class="hljs-comment">// Start the computation</span>
    ssc.awaitTermination() <span class="hljs-comment">// Wait for the computation to terminate</span>
    <span class="hljs-comment">//在StreamingContext上调用stop()方法，也会关闭SparkContext对象。如果只想仅关闭StreamingContext对象，设置stop()的可选参数为false</span>
    <span class="hljs-comment">//一个SparkContext对象可以重复利用去创建多个StreamingContext对象，前提条件是前面的StreamingContext在后面StreamingContext创建之前关闭（不关闭SparkContext）</span>
    ssc.stop()
  }
}
</code></pre><ul class="pre-numbering" style="opacity:0;"><li>1</li><li>2</li><li>3</li><li>4</li><li>5</li><li>6</li><li>7</li><li>8</li><li>9</li><li>10</li><li>11</li><li>12</li><li>13</li><li>14</li><li>15</li><li>16</li><li>17</li><li>18</li><li>19</li><li>20</li><li>21</li><li>22</li><li>23</li><li>24</li><li>25</li><li>26</li><li>27</li><li>28</li><li>29</li><li>30</li><li>31</li><li>32</li><li>33</li><li>34</li><li>35</li><li>36</li><li>37</li><li>38</li></ul><div class="save_code tracking-ad"><a><img src="http://static.blog.csdn.net/images/save_snippets.png" alt="save_snippets.png"></a></div><ul class="pre-numbering"><li>1</li><li>2</li><li>3</li><li>4</li><li>5</li><li>6</li><li>7</li><li>8</li><li>9</li><li>10</li><li>11</li><li>12</li><li>13</li><li>14</li><li>15</li><li>16</li><li>17</li><li>18</li><li>19</li><li>20</li><li>21</li><li>22</li><li>23</li><li>24</li><li>25</li><li>26</li><li>27</li><li>28</li><li>29</li><li>30</li><li>31</li><li>32</li><li>33</li><li>34</li><li>35</li><li>36</li><li>37</li><li>38</li></ul><p>1.启动 <br>
start-dfs.sh <br>
start-yarn.sh <br><img src="https://img-blog.csdn.net/20170323171749451?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHV5YW9xaWFveWFvZ2U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""><br>
2.终端输入： <br>
nc -lk 9999 <br>
然后在IEDA中运行spark程序。由于9999端口中还没有写东西，所以运行是下图：</p>
<p><img src="https://img-blog.csdn.net/20170323171840904?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHV5YW9xaWFveWFvZ2U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""><br>
只有时间，没有打印出东西。然后在终端输入下面的东西，也可以从其他地方复制进来。 <br>
hello world <br>
hello hadoop <br>
hadoop love <br>
love cat <br>
cat love rabbit <br>
这时，IDEA的控制台就输出下面的东西。 <br><img src="https://img-blog.csdn.net/20170323172048421?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHV5YW9xaWFveWFvZ2U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>
<p>3.下面运行带时间窗口的，注意如果加了时间窗口就必须有checkpoint <br>
输入下面的，不要一次全输入，一次输个几行。 <br>
checkpoint <br>
hello world <br>
hello hadoop <br>
hadoop love <br>
love cat <br>
cat love rabbit <br>
ni hao a <br>
hello world <br>
hello hadoop <br>
hadoop love <br>
love cat <br>
cat love rabbit <br>
hello world <br>
hello hadoop <br>
hadoop love <br>
love cat <br>
cat love rabbit</p>
<p>先是++–的那种： <br><img src="https://img-blog.csdn.net/20170323172403516?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHV5YW9xaWFveWFvZ2U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""><br><img src="https://img-blog.csdn.net/20170323172427542?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHV5YW9xaWFveWFvZ2U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""><br><img src="https://img-blog.csdn.net/20170323172444277?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHV5YW9xaWFveWFvZ2U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""><br><img src="https://img-blog.csdn.net/20170323172533044?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHV5YW9xaWFveWFvZ2U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""><br>
再然后是不++–的那种： <br><img src="https://img-blog.csdn.net/20170323172741550?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHV5YW9xaWFveWFvZ2U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""><br><img src="https://img-blog.csdn.net/20170323172825985?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHV5YW9xaWFveWFvZ2U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""><br>
++–的那种是因为把过去的RDD也带进来计算了，所以出现了0这个情况，为了避免这种情况只能在打印前过滤掉0的再打印。而没有++–的那种情况是不需要这样做的。</p>
<p><strong><em>Checkpointing</em></strong> <br>
在容错、可靠的文件系统（HDFS、s3等）中设置一个目录用于保存checkpoint信息。就可以通过streamingContext.checkpoint(checkpointDirectory)方法来做。
<br>
默认的间隔时间是批间隔时间的倍数，最少10秒。它可以通过dstream.checkpoint来设置。需要注意的是，随着 streaming application 的持续运行，checkpoint 数据占用的存储空间会不断变大。因此，需要小心设置checkpoint 的时间间隔。设置得越小，checkpoint 次数会越多，占用空间会越大；如果设置越大，会导致恢复时丢失的数据和进度越多。一般推荐设置为 batch duration 的5~10倍。</p>
<pre class="prettyprint"><code class="hljs scala has-numbering"><span class="hljs-keyword">package</span> streaming

<span class="hljs-keyword">import</span> java.io.File
<span class="hljs-keyword">import</span> java.nio.charset.Charset

<span class="hljs-keyword">import</span> com.google.common.io.Files

<span class="hljs-keyword">import</span> org.apache.spark.SparkConf
<span class="hljs-keyword">import</span> org.apache.spark.streaming.{Seconds, StreamingContext}

<span class="hljs-javadoc">/**
  * Created by Administrator on 2017/3/12.
  */</span>

<span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">RecoverableNetworkWordCount</span> {</span>

  <span class="hljs-keyword">def</span> createContext(ip: String, port: Int, outputPath: String, checkpointDirectory: String): StreamingContext = {
    println(<span class="hljs-string">"Creating new context"</span>) <span class="hljs-comment">//如果没有出现这句话，说明StreamingContext是从checkpoint里面加载的</span>
    <span class="hljs-keyword">val</span> outputFile = <span class="hljs-keyword">new</span> File(outputPath) <span class="hljs-comment">//输出文件的目录</span>
    <span class="hljs-keyword">if</span> (outputFile.exists()) outputFile.delete()
    <span class="hljs-keyword">val</span> sparkConf = <span class="hljs-keyword">new</span> SparkConf().setAppName(<span class="hljs-string">"RecoverableNetworkWordCount"</span>)
    <span class="hljs-keyword">val</span> ssc = <span class="hljs-keyword">new</span> StreamingContext(sparkConf, Seconds(<span class="hljs-number">1</span>)) <span class="hljs-comment">//时间间隔是1秒</span>
    ssc.checkpoint(checkpointDirectory) <span class="hljs-comment">//设置一个目录用于保存checkpoint信息</span>

    <span class="hljs-keyword">val</span> lines = ssc.socketTextStream(ip, port)
    <span class="hljs-keyword">val</span> words = lines.flatMap(_.split(<span class="hljs-string">" "</span>))
    <span class="hljs-keyword">val</span> wordCounts = words.map(x =&gt; (x, <span class="hljs-number">1</span>)).reduceByKey(_ + _)
    <span class="hljs-keyword">val</span> windowedWordCounts = wordCounts.reduceByKeyAndWindow(_ + _, _ - _, Seconds(<span class="hljs-number">30</span>), Seconds(<span class="hljs-number">10</span>))
    windowedWordCounts.checkpoint(Seconds(<span class="hljs-number">10</span>))<span class="hljs-comment">//一般推荐设置为 batch duration 的5~10倍,即StreamingContext的第二个参数的5~10倍</span>
    windowedWordCounts.print()
    Files.append(windowedWordCounts + <span class="hljs-string">"\n"</span>, outputFile, Charset.defaultCharset())
    ssc
  }

  <span class="hljs-keyword">def</span> main(args: Array[String]): Unit = {
    <span class="hljs-keyword">if</span> (args.length != <span class="hljs-number">4</span>) {
      System.exit(<span class="hljs-number">1</span>)
    }
    <span class="hljs-keyword">val</span> ip = args(<span class="hljs-number">0</span>)
    <span class="hljs-keyword">val</span> port = args(<span class="hljs-number">1</span>).toInt
    <span class="hljs-keyword">val</span> checkpointDirectory = args(<span class="hljs-number">2</span>)
    <span class="hljs-keyword">val</span> outputPath = args(<span class="hljs-number">3</span>)
    <span class="hljs-keyword">val</span> ssc = StreamingContext.getOrCreate(checkpointDirectory, () =&gt; createContext(ip, port, outputPath, checkpointDirectory))
    ssc.start()
    ssc.awaitTermination()
  }
}
</code></pre><ul class="pre-numbering" style="opacity:0;"><li>1</li><li>2</li><li>3</li><li>4</li><li>5</li><li>6</li><li>7</li><li>8</li><li>9</li><li>10</li><li>11</li><li>12</li><li>13</li><li>14</li><li>15</li><li>16</li><li>17</li><li>18</li><li>19</li><li>20</li><li>21</li><li>22</li><li>23</li><li>24</li><li>25</li><li>26</li><li>27</li><li>28</li><li>29</li><li>30</li><li>31</li><li>32</li><li>33</li><li>34</li><li>35</li><li>36</li><li>37</li><li>38</li><li>39</li><li>40</li><li>41</li><li>42</li><li>43</li><li>44</li><li>45</li><li>46</li><li>47</li><li>48</li></ul><div class="save_code tracking-ad"><a><img src="http://static.blog.csdn.net/images/save_snippets.png" alt="save_snippets.png"></a></div><ul class="pre-numbering"><li>1</li><li>2</li><li>3</li><li>4</li><li>5</li><li>6</li><li>7</li><li>8</li><li>9</li><li>10</li><li>11</li><li>12</li><li>13</li><li>14</li><li>15</li><li>16</li><li>17</li><li>18</li><li>19</li><li>20</li><li>21</li><li>22</li><li>23</li><li>24</li><li>25</li><li>26</li><li>27</li><li>28</li><li>29</li><li>30</li><li>31</li><li>32</li><li>33</li><li>34</li><li>35</li><li>36</li><li>37</li><li>38</li><li>39</li><li>40</li><li>41</li><li>42</li><li>43</li><li>44</li><li>45</li><li>46</li><li>47</li><li>48</li></ul><p><strong><em>优化</em></strong> <br>
1.数据接收的并行水平 <br>
创建多个输入DStream并配置它们可以从源中接收不同分区的数据流，从而实现多数据流接收。因此允许数据并行接收，提高整体的吞吐量。</p>
<pre class="prettyprint"><code class="hljs r has-numbering">val numStreams = <span class="hljs-number">5</span>
val kafkaStreams = (<span class="hljs-number">1</span> to numStreams).map { i =&gt; KafkaUtils.createStream(<span class="hljs-keyword">...</span>) }
val unifiedStream = streamingContext.union(kafkaStreams)
unifiedStream.print()</code></pre><ul class="pre-numbering" style="opacity:0;"><li>1</li><li>2</li><li>3</li><li>4</li></ul><div class="save_code tracking-ad"><a><img src="http://static.blog.csdn.net/images/save_snippets.png" alt="save_snippets.png"></a></div><ul class="pre-numbering"><li>1</li><li>2</li><li>3</li><li>4</li></ul><p>多输入流或者多receiver的可选的方法是明确地重新分配输入数据流（利用inputStream.repartition()），在进一步操作之前，通过集群的机器数分配接收的批数据。
<br>
2.任务序列化 <br>
运行kyro序列化任何可以减小任务的大小，从而减小任务发送到slave的时间。</p>
<pre class="prettyprint"><code class="hljs cs has-numbering">    val conf = <span class="hljs-keyword">new</span> SparkConf().setAppName(<span class="hljs-string">"analyse_domain_day"</span>).<span class="hljs-keyword">set</span>(<span class="hljs-string">"spark.serializer"</span>, <span class="hljs-string">"org.apache.spark.serializer.KryoSerializer"</span>)
</code></pre><ul class="pre-numbering" style="opacity:0;"><li>1</li><li>2</li></ul><div class="save_code tracking-ad"><a><img src="http://static.blog.csdn.net/images/save_snippets.png" alt="save_snippets.png"></a></div><ul class="pre-numbering"><li>1</li><li>2</li></ul><p>3.设置合适的批间隔时间(即批数据的容量) <br>
批处理时间应该小于批间隔时间。如果时间间隔是1秒，但处理需要2秒，则处理赶不上接收，待处理的数据会越来越多，最后就嘣了。 <br>
找出正确的批容量的一个好的办法是用一个保守的批间隔时间（5-10,秒）和低数据速率来<a href="http://lib.csdn.net/base/softwaretest" rel="nofollow" class="replace_word" title="软件测试知识库" style="color:#df3434;font-weight:bold;">测试</a>你的应用程序。为了验证你的系统是否能满足数据处理速率，你可以通过检查端到端的延迟值来判断（可以在Spark驱动程序的log4j日志中查看”Total
 delay”或者利用StreamingListener接口）。如果延迟维持稳定，那么系统是稳定的。如果延迟持续增长，那么系统无法跟上数据处理速率，是不稳定的。你能够尝试着增加数据处理速率或者减少批容量来作进一步的测试。</p>
<h2 id="demo"><a name="t2"></a>DEMO</h2>
<p>spark流操作kafka有两种方式： <br>
一种是利用接收器（receiver）和kafaka的高层API实现。 <br>
一种是不利用接收器，直接用kafka底层的API来实现（spark1.3以后引入）。</p>
<p>相比基于Receiver方式有几个优点： <br>
1、不需要创建多个kafka输入流，然后Union他们，而使用DirectStream，spark Streaming将会创建和kafka分区一样的RDD的分区数，而且会从kafka并行读取数据，Spark的分区数和Kafka的分区数是一一对应的关系。
<br>
2、第一种实现数据的零丢失是将数据预先保存在WAL中，会复制一遍数据，会导致数据被拷贝两次：一次是被Kafka复制；另一次是写入到WAL中。 <br>
Direct的方式是会直接操作kafka底层的元数据信息，这样如果计算失败了，可以把数据重新读一下，重新处理。即数据一定会被处理。拉数据，是RDD在执行的时候直接去拉数据。
<br>
3、Receiver方式读取kafka，使用的是高层API将偏移量写入ZK中，虽然这种方法可以通过数据保存在WAL中保证数据的不对，但是可能会因为sparkStreaming和ZK中保存的偏移量不一致而导致数据被消费了多次。
<br>
第二种方式不采用ZK保存偏移量，消除了两者的不一致，保证每个记录只被Spark Streaming操作一次，即使是在处理失败的情况下。如果想更新ZK中的偏移量数据，需要自己写代码来实现。
<br>
由于直接操作的是kafka，kafka就相当于你底层的文件系统。这个时候能保证严格的事务一致性，即一定会被处理，而且只会被处理一次。</p>
<p>首先去maven的官网上下载jar包 <br>
spark-streaming_2.10-1.6.2.jar <br>
spark-streaming-kafka_2.10-1.6.2.jar <br>
我的<a href="http://lib.csdn.net/base/scala" rel="nofollow" class="replace_word" title="Scala知识库" style="color:#df3434;font-weight:bold;">Scala</a>是2.10的，spark是1.6.0的，下载的spark.streaming和kafka版本要与之对应，spark-streaming_2.10-1.6.2.jar中2.10是<a href="http://lib.csdn.net/base/scala" rel="nofollow" class="replace_word" title="Scala知识库" style="color:#df3434;font-weight:bold;">scala</a>版本号，1.6.2是spark版本号。当然下载1.6.1也行。
<br>
需要添加 kafka-clients-0.8.2.1.jar以及kafka_2.10-0.8.2.1.jar <br>
这里的2.10是Scala版本号，0.8.2.1是kafka的版本号。就下这个版本，别的版本不对应，会出错。</p>
<p>在kafka的配置文件里面： <br>
delete.topic.enable=true <br>
host.name=192.168.1.66 <br>
zookeeper.connect=192.168.1.66:2181 <br>
我这里写主机名的话，各种报错，所以干脆就写IP地址了。 <br>
启动kafka以及ZK的步骤和kafka 1-2是一样的。 <br>
进入/kafka_2.10-0.8.2.1 新建一个主题： <br>
bin/kafka-topics.sh –create –zookeeper 192.168.1.66:2181 –replication-factor 1 –partitions 1 –topic test
<br>
启动一个生产者： <br>
bin/kafka-console-producer.sh –broker-list 192.168.1.66:9092 –topic test <br>
在自己的电脑上运行spark程序后，在命令行输入： <br><img src="https://img-blog.csdn.net/20170403195934870?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHV5YW9xaWFveWFvZ2U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""><br>
在控制台会显示： <br><img src="https://img-blog.csdn.net/20170403195947823?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveHV5YW9xaWFveWFvZ2U=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>
<pre class="prettyprint"><code class="hljs avrasm has-numbering">package SparkStreaming

//TopicAndPartition是对 topic和partition的id的封装的一个样例类
import kafka<span class="hljs-preprocessor">.common</span><span class="hljs-preprocessor">.TopicAndPartition</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span><span class="hljs-preprocessor">.kafka</span>.{HasOffsetRanges, KafkaUtils}
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span>.{Seconds, StreamingContext}
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span>.{SparkConf, SparkContext}
import kafka<span class="hljs-preprocessor">.serializer</span><span class="hljs-preprocessor">.StringDecoder</span>

object SparkStreaming_Kafka_Test {

  val kafkaParams = Map(
    //kafka broker的IP加端口号，这个是必须的
    <span class="hljs-string">"metadata.broker.list"</span> -&gt; <span class="hljs-string">"192.168.1.66:9092"</span>,
    // <span class="hljs-string">"group.id"</span> -&gt; <span class="hljs-string">"group1"</span>,
    <span class="hljs-comment">/*此配置参数表示当此groupId下的消费者,
     在ZK中没有offset值时(比如新的groupId,或者是zk数据被清空),
     consumer应该从哪个offset开始消费.largest表示接受接收最大的offset(即最新消息),
     smallest表示最小offset,即从topic的开始位置消费所有消息.*/</span>
    <span class="hljs-string">"auto.offset.reset"</span> -&gt; <span class="hljs-string">"smallest"</span>
  )

  val topicsSet = <span class="hljs-keyword">Set</span>(<span class="hljs-string">"test"</span>)

  //  val zkClient = new ZkClient(<span class="hljs-string">"xxx:2181,xxx:2181,xxx:2181"</span>,Integer<span class="hljs-preprocessor">.MAX</span>_VALUE,<span class="hljs-number">100000</span>,ZKStringSerializer)

  def main(args: Array[String]): Unit = {
    val conf = new SparkConf()<span class="hljs-preprocessor">.setMaster</span>(<span class="hljs-string">"local[2]"</span>)<span class="hljs-preprocessor">.setAppName</span>(<span class="hljs-string">"SparkStreaming_Kafka_Test"</span>)
    val sc = new SparkContext(conf)
    val ssc = new StreamingContext(sc, Seconds(<span class="hljs-number">2</span>))
    ssc<span class="hljs-preprocessor">.checkpoint</span>(<span class="hljs-string">"F:\\checkpoint"</span>)
    <span class="hljs-comment">/*
    KafkaUtils.createDirectStream[
       [key的数据类型], [value的数据类型], [key解码的类], [value解码的类] ](
       streamingContext, [Kafka配置的参数，是一个map], [topics的集合，是一个set])
       */</span>
    val messages = KafkaUtils<span class="hljs-preprocessor">.createDirectStream</span>[String, String, StringDecoder, StringDecoder](ssc, kafkaParams, topicsSet)
    val lines = messages<span class="hljs-preprocessor">.map</span>(_._2) //取value
    val words = lines<span class="hljs-preprocessor">.flatMap</span>(_<span class="hljs-preprocessor">.split</span>(<span class="hljs-string">" "</span>))
    val wordCounts = words<span class="hljs-preprocessor">.map</span>(<span class="hljs-built_in">x</span> =&gt; (<span class="hljs-built_in">x</span>, <span class="hljs-number">1</span>))<span class="hljs-preprocessor">.reduceByKey</span>(_ + _)
    wordCounts<span class="hljs-preprocessor">.print</span>()

    ssc<span class="hljs-preprocessor">.start</span>()
    ssc<span class="hljs-preprocessor">.awaitTermination</span>()
    ssc<span class="hljs-preprocessor">.stop</span>()
  }
}

</code></pre><ul class="pre-numbering" style="opacity:0;"><li>1</li><li>2</li><li>3</li><li>4</li><li>5</li><li>6</li><li>7</li><li>8</li><li>9</li><li>10</li><li>11</li><li>12</li><li>13</li><li>14</li><li>15</li><li>16</li><li>17</li><li>18</li><li>19</li><li>20</li><li>21</li><li>22</li><li>23</li><li>24</li><li>25</li><li>26</li><li>27</li><li>28</li><li>29</li><li>30</li><li>31</li><li>32</li><li>33</li><li>34</li><li>35</li><li>36</li><li>37</li><li>38</li><li>39</li><li>40</li><li>41</li><li>42</li><li>43</li><li>44</li><li>45</li><li>46</li><li>47</li><li>48</li><li>49</li></ul><div class="save_code tracking-ad"><a><img src="http://static.blog.csdn.net/images/save_snippets.png" alt="save_snippets.png"></a></div><ul class="pre-numbering"><li>1</li><li>2</li><li>3</li><li>4</li><li>5</li><li>6</li><li>7</li><li>8</li><li>9</li><li>10</li><li>11</li><li>12</li><li>13</li><li>14</li><li>15</li><li>16</li><li>17</li><li>18</li><li>19</li><li>20</li><li>21</li><li>22</li><li>23</li><li>24</li><li>25</li><li>26</li><li>27</li><li>28</li><li>29</li><li>30</li><li>31</li><li>32</li><li>33</li><li>34</li><li>35</li><li>36</li><li>37</li><li>38</li><li>39</li><li>40</li><li>41</li><li>42</li><li>43</li><li>44</li><li>45</li><li>46</li><li>47</li><li>48</li><li>49</li></ul><p>偏移量仅仅被ssc保存在checkpoint中，消除了zk和ssc偏移量不一致的问题。所以说checkpoint就已经可以保证容错性了。 <br>
如果需要把偏移量写入ZK，首先在工程中新建一个包：org.apache.spark.streaming.kafka，然后建一个KafkaCluster类：</p>
<pre class="prettyprint"><code class="hljs scala has-numbering"><span class="hljs-keyword">package</span> org.apache.spark.streaming.kafka

<span class="hljs-keyword">import</span> kafka.api.OffsetCommitRequest
<span class="hljs-keyword">import</span> kafka.common.{ErrorMapping, OffsetAndMetadata, TopicAndPartition}
<span class="hljs-keyword">import</span> kafka.consumer.SimpleConsumer
<span class="hljs-keyword">import</span> org.apache.spark.SparkException
<span class="hljs-keyword">import</span> org.apache.spark.streaming.kafka.KafkaCluster.SimpleConsumerConfig

<span class="hljs-keyword">import</span> scala.collection.mutable.ArrayBuffer
<span class="hljs-keyword">import</span> scala.util.Random
<span class="hljs-keyword">import</span> scala.util.control.NonFatal

<span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">KafkaCluster</span><span class="hljs-params">(val kafkaParams: Map[String, String])</span> <span class="hljs-keyword">extends</span> <span class="hljs-title">Serializable</span> {</span>
  <span class="hljs-keyword">type</span> Err = ArrayBuffer[Throwable]

  <span class="hljs-annotation">@transient</span> <span class="hljs-keyword">private</span> <span class="hljs-keyword">var</span> _config: SimpleConsumerConfig = <span class="hljs-keyword">null</span>

  <span class="hljs-keyword">def</span> config: SimpleConsumerConfig = <span class="hljs-keyword">this</span>.synchronized {
    <span class="hljs-keyword">if</span> (_config == <span class="hljs-keyword">null</span>) {
      _config = SimpleConsumerConfig(kafkaParams)
    }
    _config
  }

  <span class="hljs-keyword">def</span> setConsumerOffsets(groupId: String, offsets: Map[TopicAndPartition, Long], consumerApiVersion: Short): Either[Err, Map[TopicAndPartition, Short]] = {
    <span class="hljs-keyword">val</span> meta = offsets.map {
      kv =&gt; kv._1 -&gt; OffsetAndMetadata(kv._2)
    }
    setConsumerOffsetMetadata(groupId, meta, consumerApiVersion)
  }

  <span class="hljs-keyword">def</span> setConsumerOffsetMetadata(groupId: String, metadata: Map[TopicAndPartition, OffsetAndMetadata], consumerApiVersion: Short): Either[Err, Map[TopicAndPartition, Short]] = {
    <span class="hljs-keyword">var</span> result = Map[TopicAndPartition, Short]()
    <span class="hljs-keyword">val</span> req = OffsetCommitRequest(groupId, metadata, consumerApiVersion)
    <span class="hljs-keyword">val</span> errs = <span class="hljs-keyword">new</span> Err
    <span class="hljs-keyword">val</span> topicAndPartitions = metadata.keySet
    withBrokers(Random.shuffle(config.seedBrokers), errs) { consumer =&gt;
      <span class="hljs-keyword">val</span> resp = consumer.commitOffsets(req)
      <span class="hljs-keyword">val</span> respMap = resp.commitStatus
      <span class="hljs-keyword">val</span> needed = topicAndPartitions.diff(result.keySet)
      needed.foreach { tp: TopicAndPartition =&gt;
        respMap.get(tp).foreach { err: Short =&gt;
          <span class="hljs-keyword">if</span> (err == ErrorMapping.NoError) {
            result += tp -&gt; err
          } <span class="hljs-keyword">else</span> {
            errs.append(ErrorMapping.exceptionFor(err))
          }
        }
      }
      <span class="hljs-keyword">if</span> (result.keys.size == topicAndPartitions.size) {
        <span class="hljs-keyword">return</span> Right(result)
      }
    }
    <span class="hljs-keyword">val</span> missing = topicAndPartitions.diff(result.keySet)
    errs.append(<span class="hljs-keyword">new</span> SparkException(s<span class="hljs-string">"Couldn't set offsets for ${missing}"</span>))
    Left(errs)
  }

  <span class="hljs-keyword">private</span> <span class="hljs-keyword">def</span> withBrokers(brokers: Iterable[(String, Int)], errs: Err)(fn: SimpleConsumer =&gt; Any): Unit = {
    brokers.foreach { hp =&gt;
      <span class="hljs-keyword">var</span> consumer: SimpleConsumer = <span class="hljs-keyword">null</span>
      <span class="hljs-keyword">try</span> {
        consumer = connect(hp._1, hp._2)
        fn(consumer)
      } <span class="hljs-keyword">catch</span> {
        <span class="hljs-keyword">case</span> NonFatal(e) =&gt;
          errs.append(e)
      } <span class="hljs-keyword">finally</span> {
        <span class="hljs-keyword">if</span> (consumer != <span class="hljs-keyword">null</span>) {
          consumer.close()
        }
      }
    }
  }

  <span class="hljs-keyword">def</span> connect(host: String, port: Int): SimpleConsumer =
    <span class="hljs-keyword">new</span> SimpleConsumer(host, port, config.socketTimeoutMs,
      config.socketReceiveBufferBytes, config.clientId)
}
</code></pre><ul class="pre-numbering" style="opacity:0;"><li>1</li><li>2</li><li>3</li><li>4</li><li>5</li><li>6</li><li>7</li><li>8</li><li>9</li><li>10</li><li>11</li><li>12</li><li>13</li><li>14</li><li>15</li><li>16</li><li>17</li><li>18</li><li>19</li><li>20</li><li>21</li><li>22</li><li>23</li><li>24</li><li>25</li><li>26</li><li>27</li><li>28</li><li>29</li><li>30</li><li>31</li><li>32</li><li>33</li><li>34</li><li>35</li><li>36</li><li>37</li><li>38</li><li>39</li><li>40</li><li>41</li><li>42</li><li>43</li><li>44</li><li>45</li><li>46</li><li>47</li><li>48</li><li>49</li><li>50</li><li>51</li><li>52</li><li>53</li><li>54</li><li>55</li><li>56</li><li>57</li><li>58</li><li>59</li><li>60</li><li>61</li><li>62</li><li>63</li><li>64</li><li>65</li><li>66</li><li>67</li><li>68</li><li>69</li><li>70</li><li>71</li><li>72</li><li>73</li><li>74</li><li>75</li><li>76</li><li>77</li><li>78</li><li>79</li><li>80</li></ul><div class="save_code tracking-ad"><a><img src="http://static.blog.csdn.net/images/save_snippets.png" alt="save_snippets.png"></a></div><ul class="pre-numbering"><li>1</li><li>2</li><li>3</li><li>4</li><li>5</li><li>6</li><li>7</li><li>8</li><li>9</li><li>10</li><li>11</li><li>12</li><li>13</li><li>14</li><li>15</li><li>16</li><li>17</li><li>18</li><li>19</li><li>20</li><li>21</li><li>22</li><li>23</li><li>24</li><li>25</li><li>26</li><li>27</li><li>28</li><li>29</li><li>30</li><li>31</li><li>32</li><li>33</li><li>34</li><li>35</li><li>36</li><li>37</li><li>38</li><li>39</li><li>40</li><li>41</li><li>42</li><li>43</li><li>44</li><li>45</li><li>46</li><li>47</li><li>48</li><li>49</li><li>50</li><li>51</li><li>52</li><li>53</li><li>54</li><li>55</li><li>56</li><li>57</li><li>58</li><li>59</li><li>60</li><li>61</li><li>62</li><li>63</li><li>64</li><li>65</li><li>66</li><li>67</li><li>68</li><li>69</li><li>70</li><li>71</li><li>72</li><li>73</li><li>74</li><li>75</li><li>76</li><li>77</li><li>78</li><li>79</li><li>80</li></ul><p>然后在主函数中：</p>
<pre class="prettyprint"><code class="hljs fsharp has-numbering"><span class="hljs-comment">// 手动更新ZK偏移量，使得基于ZK偏移量的kafka监控工具可以使用</span>
    messages.foreachRDD(rdd =&gt; {
      <span class="hljs-comment">// 先处理消息</span>
      <span class="hljs-keyword">val</span> lines = rdd.map(_._2) <span class="hljs-comment">//取value</span>
      <span class="hljs-keyword">val</span> words = lines.flatMap(_.split(<span class="hljs-string">" "</span>))
      <span class="hljs-keyword">val</span> wordCounts = words.map(x =&gt; (x, <span class="hljs-number">1</span>)).reduceByKey(_ + _)
      wordCounts.foreach(println)
      <span class="hljs-comment">// 再更新offsets</span>
      <span class="hljs-comment">//spark内部维护kafka偏移量信息是存储在HasOffsetRanges类的offsetRanges中</span>
      <span class="hljs-comment">//OffsetRange 包含信息有：topic名字，分区Id，开始偏移，结束偏移。</span>
      <span class="hljs-keyword">val</span> offsetsList = rdd.asInstanceOf[HasOffsetRanges].offsetRanges <span class="hljs-comment">//得到该 rdd 对应 kafka 的消息的 offset</span>
      <span class="hljs-keyword">val</span> kc = <span class="hljs-keyword">new</span> KafkaCluster(kafkaParams)
      <span class="hljs-keyword">for</span> (offsets &lt;- offsetsList) {
        <span class="hljs-keyword">val</span> topicAndPartition = TopicAndPartition(<span class="hljs-string">"test"</span>, offsets.partition)
        <span class="hljs-keyword">val</span> o = kc.setConsumerOffsets(<span class="hljs-string">"group1"</span>, Map((topicAndPartition, offsets.untilOffset)),<span class="hljs-number">8</span>)
        <span class="hljs-keyword">if</span> (o.isLeft) {
          println(s<span class="hljs-string">"Error updating the offset to Kafka cluster: ${o.left.get}"</span>)
        }
      }
    })</code></pre><ul class="pre-numbering" style="opacity:0;"><li>1</li><li>2</li><li>3</li><li>4</li><li>5</li><li>6</li><li>7</li><li>8</li><li>9</li><li>10</li><li>11</li><li>12</li><li>13</li><li>14</li><li>15</li><li>16</li><li>17</li><li>18</li><li>19</li><li>20</li></ul><div class="save_code tracking-ad"><a><img src="http://static.blog.csdn.net/images/save_snippets.png" alt="save_snippets.png"></a></div><ul class="pre-numbering"><li>1</li><li>2</li><li>3</li><li>4</li><li>5</li><li>6</li><li>7</li><li>8</li><li>9</li><li>10</li><li>11</li><li>12</li><li>13</li><li>14</li><li>15</li><li>16</li><li>17</li><li>18</li><li>19</li><li>20</li></ul><p>下面是用kafka的API自己写一个程序读取文件，作为kafka的生产者，需要将Scala和kafka的所有的jar包都导入，lib文件夹下面的都导入进去。
<br>
如果没有2台电脑，可以开2个开发环境，IDEA作为消费者，eclipse作为生产者。 <br>
生产者代码如下：</p>
<pre class="prettyprint"><code class="hljs java has-numbering"><span class="hljs-keyword">package</span> spark_streaming_kafka_test;

<span class="hljs-keyword">import</span> java.io.BufferedReader;
<span class="hljs-keyword">import</span> java.io.File;
<span class="hljs-keyword">import</span> java.io.FileNotFoundException;
<span class="hljs-keyword">import</span> java.io.FileReader;
<span class="hljs-keyword">import</span> java.io.IOException;
<span class="hljs-keyword">import</span> java.util.Properties;
<span class="hljs-keyword">import</span> kafka.javaapi.producer.Producer;
<span class="hljs-keyword">import</span> kafka.producer.KeyedMessage;
<span class="hljs-keyword">import</span> kafka.producer.ProducerConfig;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">MakeRealtimeDate</span> <span class="hljs-keyword">extends</span> <span class="hljs-title">Thread</span> {</span>

    <span class="hljs-keyword">private</span> Producer&lt;Integer, String&gt; producer;

    <span class="hljs-keyword">public</span> <span class="hljs-title">MakeRealtimeDate</span>() {
        Properties props = <span class="hljs-keyword">new</span> Properties();
        props.put(<span class="hljs-string">"serializer.class"</span>, <span class="hljs-string">"kafka.serializer.StringEncoder"</span>);
        props.put(<span class="hljs-string">"zk.connect"</span>, <span class="hljs-string">"192.168.1.66:2181"</span>);
        props.put(<span class="hljs-string">"metadata.broker.list"</span>, <span class="hljs-string">"192.168.1.66:9092"</span>);
        ProducerConfig pc = <span class="hljs-keyword">new</span> ProducerConfig(props);
        producer = <span class="hljs-keyword">new</span> Producer&lt;Integer, String&gt;(pc);
    }

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">run</span>() {
        <span class="hljs-keyword">while</span> (<span class="hljs-keyword">true</span>) {
            File file = <span class="hljs-keyword">new</span> File(<span class="hljs-string">"C:\\Users\\Administrator\\Desktop\\wordcount.txt"</span>);
            BufferedReader reader = <span class="hljs-keyword">null</span>;
            <span class="hljs-keyword">try</span> {
                reader = <span class="hljs-keyword">new</span> BufferedReader(<span class="hljs-keyword">new</span> FileReader(file));
            } <span class="hljs-keyword">catch</span> (FileNotFoundException e1) {
                <span class="hljs-comment">// TODO Auto-generated catch block</span>
                e1.printStackTrace();
            }
            String lineTxt = <span class="hljs-keyword">null</span>;
            <span class="hljs-keyword">try</span> {
                <span class="hljs-keyword">while</span> ((lineTxt = reader.readLine()) != <span class="hljs-keyword">null</span>) {
                    System.out.println(lineTxt);
                    producer.send(<span class="hljs-keyword">new</span> KeyedMessage&lt;Integer, String&gt;(<span class="hljs-string">"test"</span>, lineTxt));
                    <span class="hljs-keyword">try</span> {
                        Thread.sleep(<span class="hljs-number">1000</span>);
                    } <span class="hljs-keyword">catch</span> (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            } <span class="hljs-keyword">catch</span> (IOException e) {
                <span class="hljs-comment">// TODO Auto-generated catch block</span>
                e.printStackTrace();
            }

        }
    }

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) {
        <span class="hljs-keyword">new</span> MakeRealtimeDate().start();
    }

}
</code></pre><ul class="pre-numbering" style="opacity:0;"><li>1</li><li>2</li><li>3</li><li>4</li><li>5</li><li>6</li><li>7</li><li>8</li><li>9</li><li>10</li><li>11</li><li>12</li><li>13</li><li>14</li><li>15</li><li>16</li><li>17</li><li>18</li><li>19</li><li>20</li><li>21</li><li>22</li><li>23</li><li>24</li><li>25</li><li>26</li><li>27</li><li>28</li><li>29</li><li>30</li><li>31</li><li>32</li><li>33</li><li>34</li><li>35</li><li>36</li><li>37</li><li>38</li><li>39</li><li>40</li><li>41</li><li>42</li><li>43</li><li>44</li><li>45</li><li>46</li><li>47</li><li>48</li><li>49</li><li>50</li><li>51</li><li>52</li><li>53</li><li>54</li><li>55</li><li>56</li><li>57</li><li>58</li><li>59</li><li>60</li></ul><div class="save_code tracking-ad"><a><img src="http://static.blog.csdn.net/images/save_snippets.png" alt="save_snippets.png"></a></div><ul class="pre-numbering"><li>1</li><li>2</li><li>3</li><li>4</li><li>5</li><li>6</li><li>7</li><li>8</li><li>9</li><li>10</li><li>11</li><li>12</li><li>13</li><li>14</li><li>15</li><li>16</li><li>17</li><li>18</li><li>19</li><li>20</li><li>21</li><li>22</li><li>23</li><li>24</li><li>25</li><li>26</li><li>27</li><li>28</li><li>29</li><li>30</li><li>31</li><li>32</li><li>33</li><li>34</li><li>35</li><li>36</li><li>37</li><li>38</li><li>39</li><li>40</li><li>41</li><li>42</li><li>43</li><li>44</li><li>45</li><li>46</li><li>47</li><li>48</li><li>49</li><li>50</li><li>51</li><li>52</li><li>53</li><li>54</li><li>55</li><li>56</li><li>57</li><li>58</li><li>59</li><li>60</li></ul><p>先启动之前写的sparkstreaming消费者统计单词个数的程序，然后再启动我们现在写的这个生产者程序，最后就会在IDEA的控制台中看到实时结果。</p>
</div>
</div>
            </div>
                </div>