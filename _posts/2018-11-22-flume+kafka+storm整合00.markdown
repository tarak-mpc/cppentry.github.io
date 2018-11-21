---
layout:     post
title:      flume+kafka+storm整合00
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/wuxintdrh/article/details/63683170				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3 id="一安装">一、安装</h3>

<p>flume，kafka, storm 的安装在下面三篇文章: <br>
flume:1.6.0 <br>
kafka:注意这里最好下载scala2.10版本的kafka，因为scala2.10版本的兼容性比较好和2.11版本差别太大 <br>
<img src="https://img-blog.csdn.net/20170319113321451?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd3V4aW50ZHJo/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><a href="http://blog.csdn.net/wuxintdrh/article/details/62246961" rel="nofollow">flume安装部署 </a> <br>
<a href="http://blog.csdn.net/wuxintdrh/article/details/62414061" rel="nofollow">kafka安装部署</a> <br>
<a href="http://blog.csdn.net/wuxintdrh/article/details/60879278" rel="nofollow">storm安装部署</a></p>

<h3 id="二各个部分调试">二、各个部分调试</h3>



<h4 id="21flume">2.1、flume</h4>

<p><a href="http://blog.csdn.net/wuxintdrh/article/details/79478710" rel="nofollow">flume Source之SpoolDir</a> <br>
<a href="http://blog.csdn.net/wuxintdrh/article/details/79478747" rel="nofollow">flume 自定义Sink之kafkaSink</a></p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor"># 监听ftp日志</span>
<span class="hljs-preprocessor">#agent的名字a1</span>
a1<span class="hljs-preprocessor">.sources</span> = src
a1<span class="hljs-preprocessor">.channels</span> = chl
a1<span class="hljs-preprocessor">.sinks</span> = sk


<span class="hljs-preprocessor">#定义source， source使用spooldir， 监控ftp日志</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.src</span><span class="hljs-preprocessor">.type</span>=spooldir
<span class="hljs-preprocessor">#监控目录</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.src</span><span class="hljs-preprocessor">.spoolDir</span>=/hadoop/ftp/idc_bakupload/<span class="hljs-number">20180307</span>/<span class="hljs-number">23</span>/idc/
<span class="hljs-preprocessor">#忽略的文件</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.src</span><span class="hljs-preprocessor">.ignorePattern</span> = ^(.)*\\<span class="hljs-preprocessor">.AVL</span>\\.(.)*$
<span class="hljs-preprocessor">#处理后的文件，添加后缀</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.src</span><span class="hljs-preprocessor">.fileSuffix</span> = <span class="hljs-preprocessor">.bak</span>


<span class="hljs-preprocessor">#定义channel， 使用memory 作为channel</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.chl</span><span class="hljs-preprocessor">.type</span> = memory
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.chl</span><span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">100000</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.chl</span><span class="hljs-preprocessor">.transactionCapacity</span> = <span class="hljs-number">10000</span>

<span class="hljs-preprocessor">#定义sink， 输出到控制台</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sk</span><span class="hljs-preprocessor">.type</span> = <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.chb</span><span class="hljs-preprocessor">.test</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.MyKafkaSink</span>

<span class="hljs-preprocessor">#定义sink， source 与channel的关系</span>
<span class="hljs-preprocessor">#注意sink后面是channel, 而不是s</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sk</span><span class="hljs-preprocessor">.channel</span> = chl
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.src</span><span class="hljs-preprocessor">.channels</span> = chl</code></pre>



<h4 id="22kafka">2.2、kafka</h4>

<p>2.2.1、kafka自身测试， 起一个生产者，一个消费者 <br>
2.2.2、启动消费这去消费flumesink的数据</p>



<h2 id="三-storm获取数据流程">三、 Storm获取数据流程</h2>



<h2 id="31首先来了解strom-kafka">3.1、首先来了解Strom-kafka</h2>

<p><a href="https://github.com/apache/storm/tree/master/external/storm-kafka" rel="nofollow">Strom-kafka的官网介绍项目</a> <br>
注意：可能使用浏览器的问题， 导致在IE上只能看到部分，换成其他浏览器就好了。</p>

<p>介绍Storm核心Spout 和Trident spout的实现，用户消费从 Apache Kafka 0.8.x获取的数据。</p>



<h2 id="311spouts">3.1.1、Spouts</h2>

<p>We support both Trident and core Storm spouts.为了两种Spout实现，Strom使用一个<strong>BrokerHost</strong> interface 跟踪Kafka broker主机到分区映射和<strong>kafkaConfig</strong>控制一些Kafka相关的参数。</p>



<h2 id="312-brokerhosts">3.1.2 BrokerHosts</h2>

<p>为了初始化您的Kafka spout/emitter，您需要创建一个标记<strong>BrokerHosts</strong>接口的实例。 目前，支持以下两种实现：</p>



<h3 id="zkhosts">ZkHosts</h3>

<p>如果你想动态跟踪Kafka broker到分区映射（partition mapping）， 你应该使用ZkHosts。 这个类使用Kafka的ZooKeeper entries 来跟踪brokerHost - &gt;分区映射。 您可以通过调用下面方法实例化对象：</p>



<pre class="prettyprint"><code class=" hljs vbnet">   <span class="hljs-keyword">public</span> ZkHosts(<span class="hljs-built_in">String</span> brokerZkStr, <span class="hljs-built_in">String</span> brokerZkPath)
   <span class="hljs-keyword">public</span> ZkHosts(<span class="hljs-built_in">String</span> brokerZkStr)</code></pre>

<p>其中:</p>

<ul>
<li>brokerZkStr**只为**ip:post(eg. localhost:2181),</li>
<li>brokerZkPath： the root directory under which all the topics and partition information is stored， 默认为 /brokers 。</li>
<li>默认情况下，代理分区映射(borker-partition mapping)每60秒从ZooKeeper刷新。 如果要更改它，您应该将<code>host.refreshFrezqSecs</code>设置为您选择的值。 <br>
实现如：</li>
</ul>



<pre class="prettyprint"><code class=" hljs cs">ZkHosts zkHosts = <span class="hljs-keyword">new</span> ZkHosts(<span class="hljs-string">"192.168.57.4:2181,192.168.57.5:2181,192.168.57.6:2181"</span>);</code></pre>



<h3 id="statichosts">StaticHosts</h3>

<p>这是一个可选的实现，其中broker - &gt;分区信息是静态的。 为了构造这个类的实例，您需要首先构造一个<strong>GlobalPartitionInformation</strong>的实例。</p>



<pre class="prettyprint"><code class=" hljs cs">Broker brokerForPartition0 = <span class="hljs-keyword">new</span> Broker(<span class="hljs-string">"localhost"</span>);<span class="hljs-comment">//localhost:9092</span>
    Broker brokerForPartition1 = <span class="hljs-keyword">new</span> Broker(<span class="hljs-string">"localhost"</span>, <span class="hljs-number">9092</span>);<span class="hljs-comment">//localhost:9092 but we specified the port explicitly</span>
    Broker brokerForPartition2 = <span class="hljs-keyword">new</span> Broker(<span class="hljs-string">"localhost:9092"</span>);<span class="hljs-comment">//localhost:9092 specified as one string.</span>
    GlobalPartitionInformation partitionInfo = <span class="hljs-keyword">new</span> GlobalPartitionInformation();
    partitionInfo.addPartition(<span class="hljs-number">0</span>, brokerForPartition0);<span class="hljs-comment">//mapping from partition 0 to brokerForPartition0</span>
    partitionInfo.addPartition(<span class="hljs-number">1</span>, brokerForPartition1);<span class="hljs-comment">//mapping from partition 1 to brokerForPartition1</span>
    partitionInfo.addPartition(<span class="hljs-number">2</span>, brokerForPartition2);<span class="hljs-comment">//mapping from partition 2 to brokerForPartition2</span>
    StaticHosts hosts = <span class="hljs-keyword">new</span> StaticHosts(partitionInfo);</code></pre>



<h2 id="kafkaconfig">KafkaConfig</h2>

<p>为创建KafkaSpout所需的第二个对象是KafkaConfig <br>
创建KafkaConfig</p>



<pre class="prettyprint"><code class=" hljs vbnet"> <span class="hljs-keyword">public</span> KafkaConfig(BrokerHosts hosts, <span class="hljs-built_in">String</span> topic)
 <span class="hljs-keyword">public</span> KafkaConfig(BrokerHosts hosts, <span class="hljs-built_in">String</span> topic, <span class="hljs-built_in">String</span> clientId)</code></pre>

<p>BrokerHosts可以是如上所述的BrokerHosts接口的任何实现。 topic是Kafka topic的名称。 ClientId是可选的， 用作ZooKeeper路径的一部分，其中存储了spout的当前消耗偏移量。</p>



<h3 id="目前有2个kafkaconfig的扩展">目前有2个KafkaConfig的扩展。</h3>



<h3 id="spoutconfig">Spoutconfig</h3>

<p>Spoutconfig是KafkaConfig的扩展，它支持使用ZooKeeper连接信息的其他字段，并用于控制特定于KafkaSpout的行为。 Zkroot将用作root来存储消费的偏移量。 ID应该唯一标识您的spout。</p>



<pre class="prettyprint"><code class=" hljs vbnet"><span class="hljs-keyword">public</span> SpoutConfig(BrokerHosts hosts, <span class="hljs-built_in">String</span> topic, <span class="hljs-built_in">String</span> zkRoot, <span class="hljs-built_in">String</span> id);</code></pre>

<p>实现：</p>



<pre class="prettyprint"><code class=" hljs cs">SpoutConfig spoutConfig 
    = <span class="hljs-keyword">new</span> SpoutConfig(
        zkHosts, 
        topic, 
        <span class="hljs-string">"/test"</span>, <span class="hljs-comment">// 偏移量offset的根目录</span>
        <span class="hljs-string">"test"</span>);<span class="hljs-comment">// ID应该唯一标识您的spout</span></code></pre>

<p>除了这些参数，SpoutConfig包含以下字段控制KafkaSpout的行为：</p>



<pre class="prettyprint"><code class=" hljs cs">    spoutConfig.forceFromStart = <span class="hljs-keyword">false</span>; <span class="hljs-comment">// 不从头开始消费,保证spout出现故障， 重启之后，能够从kafka的原来位置处理， 而不是从开始位置处理，kafka的偏移量，周期性的写入zookeeper中， </span>
    <span class="hljs-comment">// setting for how often to save the current Kafka offset to ZooKeeper</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">long</span> stateUpdateIntervalMs = <span class="hljs-number">2000</span>;

    <span class="hljs-comment">// Retry strategy for failed messages</span>
    <span class="hljs-keyword">public</span> String failedMsgRetryManagerClass = ExponentialBackoffMsgRetryManager.class.getName();

    <span class="hljs-comment">// Exponential back-off retry settings.  These are used by ExponentialBackoffMsgRetryManager for retrying messages after a bolt</span>
    <span class="hljs-comment">// calls OutputCollector.fail(). These come into effect only if ExponentialBackoffMsgRetryManager is being used.</span>
    <span class="hljs-comment">// Initial delay between successive retries</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">long</span> retryInitialDelayMs = <span class="hljs-number">0</span>;
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">double</span> retryDelayMultiplier = <span class="hljs-number">1.0</span>;

    <span class="hljs-comment">// Maximum delay between successive retries    </span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">long</span> retryDelayMaxMs = <span class="hljs-number">60</span> * <span class="hljs-number">1000</span>;
    <span class="hljs-comment">// Failed message will be retried infinitely if retryLimit is less than zero. </span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">int</span> retryLimit = -<span class="hljs-number">1</span>;    </code></pre>

<p>Core KafkaSpout <strong>only accepts</strong> an instance of SpoutConfig.</p>



<pre class="prettyprint"><code class=" hljs fix"><span class="hljs-attribute">KafkaSpout kafkaSpout </span>=<span class="hljs-string"> new KafkaSpout(spoutConfig);</span></code></pre>



<h3 id="tridentkafkaconfig">TridentKafkaConfig</h3>

<p>TridentKafkaConfig is another extension of KafkaConfig. TridentKafkaEmitter only accepts TridentKafkaConfig.</p>



<h3 id="kafkaconfig类还有一堆公共变量用于控制应用程序的行为-这里是默认值">KafkaConfig类还有一堆公共变量，用于控制应用程序的行为。 这里是默认值：</h3>



<pre class="prettyprint"><code class=" hljs java">    <span class="hljs-keyword">public</span> <span class="hljs-keyword">int</span> fetchSizeBytes = <span class="hljs-number">1024</span> * <span class="hljs-number">1024</span>;
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">int</span> socketTimeoutMs = <span class="hljs-number">10000</span>;
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">int</span> fetchMaxWait = <span class="hljs-number">10000</span>;
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">int</span> bufferSizeBytes = <span class="hljs-number">1024</span> * <span class="hljs-number">1024</span>;
    <span class="hljs-keyword">public</span> MultiScheme scheme = <span class="hljs-keyword">new</span> RawMultiScheme();
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">boolean</span> ignoreZkOffsets = <span class="hljs-keyword">false</span>;
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">long</span> startOffsetTime = kafka.api.OffsetRequest.EarliestTime();
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">long</span> maxOffsetBehind = Long.MAX_VALUE;
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">boolean</span> useStartOffsetTimeIfOffsetOutOfRange = <span class="hljs-keyword">true</span>;
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">int</span> metricsTimeBucketSizeInSecs = <span class="hljs-number">60</span>;</code></pre>

<p>Most of them are self explanatory except MultiScheme.</p>



<h2 id="multischeme">MultiScheme</h2>

<p>MultiScheme是一个接口，指示如何将从Kafka中消耗的ByteBuffer转换为成Storm中的tuple。 它还控制输出字段的命名。</p>



<pre class="prettyprint"><code class=" hljs cs">  <span class="hljs-keyword">public</span> Iterable&lt;List&lt;Object&gt;&gt; <span class="hljs-title">deserialize</span>(ByteBuffer ser);
  <span class="hljs-keyword">public</span> Fields <span class="hljs-title">getOutputFields</span>();</code></pre>

<p>默认的RawMultiScheme只接受ByteBuffer，并返回一个带有ByteBuffer的tuple，ByteBuffer转换为<code>byte []</code>。 outputField的名称为“bytes”。 还有一些可选实现，如SchemeAsMultiScheme和KeyValueSchemeAsMultiScheme，它们可以将ByteBuffer转换为String。 <br>
<font size="4" color="red">//从Kafka中取出的byte[]，该如何反序列化</font> <br>
如在整合项目中实现：使用SchemeAsMultiScheme</p>



<pre class="prettyprint"><code class=" hljs cs">spoutConfig.scheme = <span class="hljs-keyword">new</span> SchemeAsMultiScheme(<span class="hljs-keyword">new</span> StringScheme()); <span class="hljs-comment">// 定义输出为String</span></code></pre>

<p>还有SchemeAsMultiScheme，MessageMetadataSchemeAsMultiScheme的扩展，<strong>它具有一个附加的反序列化方法</strong>，除了与消息关联的分区和偏移之外，还接受消息ByteBuffer。</p>



<pre class="prettyprint"><code class=" hljs cs"><span class="hljs-keyword">public</span> Iterable&lt;List&lt;Object&gt;&gt; <span class="hljs-title">deserializeMessageWithMetadata</span>(ByteBuffer message, Partition partition, <span class="hljs-keyword">long</span> offset)</code></pre>

<p>这对于从Kafka topic上的任意处 auditing/replaying 消息非常有用，可以保存离散流的每个消息的分区和偏移量，而不是保留整个消息。</p>



<h3 id="四kafakspout的具体实现">四、KafakSpout的具体实现</h3>



<pre class="prettyprint"><code class=" hljs javascript">        TopologyBuilder builder = <span class="hljs-keyword">new</span> TopologyBuilder();
        <span class="hljs-comment">// config kafka spout</span>
        <span class="hljs-built_in">String</span> topic = <span class="hljs-string">"testflume"</span>;
        <span class="hljs-comment">//第一步创建Zkhosts</span>
        ZkHosts zkHosts = <span class="hljs-keyword">new</span> ZkHosts(<span class="hljs-string">"192.168.57.4:2181,192.168.57.5:2181,192.168.57.6:2181"</span>);
        <span class="hljs-comment">//第二步创建SpoutConfig, 为了设置各种参数</span>
        SpoutConfig spoutConfig = <span class="hljs-keyword">new</span> SpoutConfig(zkHosts, 
                topic,    <span class="hljs-comment">//kafka的topic名称</span>
                <span class="hljs-string">"/test"</span>,  <span class="hljs-comment">// 偏移量offset的根目录</span>
                <span class="hljs-string">"test"</span>);  <span class="hljs-comment">// kafka的唯一表示。</span>
        <span class="hljs-comment">//设置zkserver的信息， 可选的， 应为在上面的ZkHosts中已经设置了zookeeper的主机和端口号。</span>
        List&lt;<span class="hljs-built_in">String</span>&gt; zkServers = <span class="hljs-keyword">new</span> ArrayList&lt;<span class="hljs-built_in">String</span>&gt;();
        System.out.println(zkHosts.brokerZkStr);
        <span class="hljs-keyword">for</span> (<span class="hljs-built_in">String</span> host : zkHosts.brokerZkStr.split(<span class="hljs-string">","</span>)) {
            zkServers.add(host.split(<span class="hljs-string">":"</span>)[<span class="hljs-number">0</span>]);
        }
        spoutConfig.zkServers = zkServers;
        spoutConfig.zkPort = <span class="hljs-number">2181</span>;
        <span class="hljs-comment">//设置kafka的消费模式， 是否从头开始。</span>
        spoutConfig.forceFromStart = <span class="hljs-literal">false</span>; <span class="hljs-comment">// 不从头开始消费</span>
        spoutConfig.socketTimeoutMs = <span class="hljs-number">60</span> * <span class="hljs-number">1000</span>;  <span class="hljs-comment">//与Kafka broker的连接的socket超时时间</span>
        <span class="hljs-comment">//从Kafka中取出的byte[]，该如何反序列化</span>
        spoutConfig.scheme = <span class="hljs-keyword">new</span> SchemeAsMultiScheme(<span class="hljs-keyword">new</span> StringScheme()); <span class="hljs-comment">// 定义输出为String</span>
        <span class="hljs-comment">//KafkaSpout之接收一个参数SpoutConfig.</span>
        KafkaSpout kafkaSpout = <span class="hljs-keyword">new</span> KafkaSpout(spoutConfig);

        <span class="hljs-comment">// set kafka spout</span>
        builder.setSpout(<span class="hljs-string">"kafka_spout"</span>, kafkaSpout, <span class="hljs-number">3</span>);</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>