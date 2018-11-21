---
layout:     post
title:      集群管理工具KafkaAdminClient——原理与示例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主朱小厮允许不得转载。					https://blog.csdn.net/u013256816/article/details/79996056				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3 id="前言">前言</h3>

<p>一般情况下，我们都习惯使用Kafka中bin目录下的脚本工具来管理查看Kafka，但是有些时候需要将某些管理查看的功能集成到系统（比如Kafka Manager）中，那么就需要调用一些API来直接操作Kafka了。在Kafka0.11.0.0版本之前，可以通过kafka-core包（Kafka的服务端代码，采用Scala编写）下的AdminClient和AdminUtils来实现部分的集群管理操作，比如笔者之前在<a href="https://blog.csdn.net/u013256816/article/details/79303825" rel="nofollow">Kafka解析之topic创建(1)</a>和<a href="https://blog.csdn.net/u013256816/article/details/79303846" rel="nofollow">Kafka解析之topic创建(2)</a>两篇文章中所讲解的Topic的创建就用到了AdminUtils类。而在Kafka0.11.0.0版本之后，又多了一个AdminClient，这个是在kafka-client包下的，这是一个抽象类，具体的实现是org.apache.kafka.clients.admin.KafkaAdminClient，这个就是本文所要陈述的重点了。</p>

<h3 id="功能与原理介绍">功能与原理介绍</h3>

<p>在Kafka官网中这么描述AdminClient：The AdminClient API supports managing and inspecting topics, brokers, acls, and other Kafka objects. 具体的KafkaAdminClient包含了一下几种功能（以Kafka1.0.0版本为准）：</p>

<ol>
<li>创建Topic：createTopics(Collection&lt;NewTopic&gt; newTopics)</li>
<li>删除Topic：deleteTopics(Collection&lt;String&gt; topics)</li>
<li>罗列所有Topic：listTopics()</li>
<li>查询Topic：describeTopics(Collection&lt;String&gt; topicNames)</li>
<li>查询集群信息：describeCluster()</li>
<li>查询ACL信息：describeAcls(AclBindingFilter filter)</li>
<li>创建ACL信息：createAcls(Collection&lt;AclBinding&gt; acls)</li>
<li>删除ACL信息：deleteAcls(Collection&lt;AclBindingFilter&gt; filters)</li>
<li>查询配置信息：describeConfigs(Collection&lt;ConfigResource&gt; resources)</li>
<li>修改配置信息：alterConfigs(Map&lt;ConfigResource, Config&gt; configs)</li>
<li>修改副本的日志目录：alterReplicaLogDirs(Map&lt;TopicPartitionReplica, String&gt; replicaAssignment)</li>
<li>查询节点的日志目录信息：describeLogDirs(Collection&lt;Integer&gt; brokers)</li>
<li>查询副本的日志目录信息：describeReplicaLogDirs(Collection&lt;TopicPartitionReplica&gt; replicas)</li>
<li>增加分区：createPartitions(Map&lt;String, NewPartitions&gt; newPartitions)</li>
</ol>

<p>其内部原理是使用Kafka自定义的一套二进制协议来实现，详细可以参见<a href="https://kafka.apache.org/protocol" rel="nofollow">Kafka协议</a>。主要实现步骤：</p>

<ol>
<li>客户端根据方法的调用创建相应的协议请求，比如创建Topic的createTopics方法，其内部就是发送CreateTopicRequest请求。</li>
<li>客户端发送请求至Kafka Broker。</li>
<li>Kafka Broker处理相应的请求并回执，比如与CreateTopicRequest对应的是CreateTopicResponse。</li>
<li>客户端接收相应的回执并进行解析处理。 <br>
和协议有关的请求和回执的类基本都在org.apache.kafka.common.requests包中，AbstractRequest和AbstractResponse是这些请求和回执类的两个基本父类。</li>
</ol>

<h3 id="示例">示例</h3>

<p>下面就以创建Topic来举一个简单的KafkaAdminClient的使用案例，【代码清单1】：</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> String NEW_TOPIC = <span class="hljs-string">"topic-test2"</span>;
<span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> String brokerUrl = <span class="hljs-string">"localhost:9092"</span>;

<span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> AdminClient adminClient;

<span class="hljs-annotation">@BeforeClass</span>
<span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">beforeClass</span>(){
    Properties properties = <span class="hljs-keyword">new</span> Properties();
    properties.put(CommonClientConfigs.BOOTSTRAP_SERVERS_CONFIG, brokerUrl);
    adminClient = AdminClient.create(properties);
}

<span class="hljs-annotation">@AfterClass</span>
<span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">afterClass</span>(){
    adminClient.close();
}

<span class="hljs-annotation">@Test</span>
<span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">createTopics</span>() {
    NewTopic newTopic = <span class="hljs-keyword">new</span> NewTopic(NEW_TOPIC,<span class="hljs-number">4</span>, (<span class="hljs-keyword">short</span>) <span class="hljs-number">1</span>);
    Collection&lt;NewTopic&gt; newTopicList = <span class="hljs-keyword">new</span> ArrayList&lt;&gt;();
    newTopicList.add(newTopic);
    adminClient.createTopics(newTopicList);
}</code></pre>

<p>示例中的createTopics()方法就创建了一个分区数为4，副本因子为1的“topic-test2”的Topic。</p>



<h3 id="代码剖析">代码剖析</h3>

<p>下面来详细介绍一下KafkaAdminClient中现有的listTopics()方法(这个方法的实现相对干净利落，代码量少、易于讲解)的实现方式，以便可以了解KafkaAdminClient中的大体脉络。listTopics()方法的具体代码如【代码清单2】所示：</p>



<pre class="prettyprint"><code class=" hljs cs"><span class="hljs-keyword">public</span> ListTopicsResult <span class="hljs-title">listTopics</span>(final ListTopicsOptions options) {
    final KafkaFutureImpl&lt;Map&lt;String, TopicListing&gt;&gt; topicListingFuture = <span class="hljs-keyword">new</span> KafkaFutureImpl&lt;&gt;();
    final <span class="hljs-keyword">long</span> now = time.milliseconds();
    runnable.call(<span class="hljs-keyword">new</span> Call(<span class="hljs-string">"listTopics"</span>, calcDeadlineMs(now, options.timeoutMs()),
        <span class="hljs-keyword">new</span> LeastLoadedNodeProvider()) {
        @Override
        AbstractRequest.Builder createRequest(<span class="hljs-keyword">int</span> timeoutMs) {
            <span class="hljs-keyword">return</span> MetadataRequest.Builder.allTopics();
        }
        @Override
        <span class="hljs-keyword">void</span> handleResponse(AbstractResponse abstractResponse) {
            MetadataResponse response = (MetadataResponse) abstractResponse;
            Cluster cluster = response.cluster();
            Map&lt;String, TopicListing&gt; topicListing = <span class="hljs-keyword">new</span> HashMap&lt;&gt;();
            <span class="hljs-keyword">for</span> (String topicName : cluster.topics()) {
                boolean <span class="hljs-keyword">internal</span> = cluster.internalTopics().contains(topicName);
                <span class="hljs-keyword">if</span> (!<span class="hljs-keyword">internal</span> || options.<span class="hljs-title">shouldListInternal</span>())
                    topicListing.<span class="hljs-title">put</span>(topicName, <span class="hljs-keyword">new</span> <span class="hljs-title">TopicListing</span>(topicName, <span class="hljs-keyword">internal</span>));
            }
            topicListingFuture.complete(topicListing);
        }
        @Override
        <span class="hljs-keyword">void</span> handleFailure(Throwable throwable) {
            topicListingFuture.completeExceptionally(throwable);
        }
    }, now);
    <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> ListTopicsResult(topicListingFuture);
}</code></pre>

<p>listTopics()方法接收一个ListTopicsOptions类型的参数，KafkaAdminClient中基本所有的应用类方法都有一个类似XXXOptions类型的参数，这个类型一般只包含timeoutMs这个成员变量，用来设定请求的超时时间，如果没有指定则使用默认的request.timeout.ms参数值，即30000ms。就拿查询Topic信息所对应的DescribeTopicsOptions来说，其就包含一个timeoutMs参数，具体如【代码清单3】所示：</p>



<pre class="prettyprint"><code class=" hljs axapta"><span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">DescribeTopicsOptions</span> <span class="hljs-inheritance"><span class="hljs-keyword">extends</span></span> <span class="hljs-title">AbstractOptions</span>&lt;<span class="hljs-title">DescribeTopicsOptions</span>&gt; {</span>}
<span class="hljs-keyword">public</span> <span class="hljs-keyword">abstract</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">AbstractOptions</span>&lt;<span class="hljs-title">T</span> <span class="hljs-inheritance"><span class="hljs-keyword">extends</span></span> <span class="hljs-title">AbstractOptions</span>&gt; {</span>
    <span class="hljs-keyword">private</span> Integer timeoutMs = <span class="hljs-keyword">null</span>;
    @SuppressWarnings(<span class="hljs-string">"unchecked"</span>)
    <span class="hljs-keyword">public</span> T timeoutMs(Integer timeoutMs) {
        <span class="hljs-keyword">this</span>.timeoutMs = timeoutMs;
        <span class="hljs-keyword">return</span> (T) <span class="hljs-keyword">this</span>;
    }
    <span class="hljs-keyword">public</span> Integer timeoutMs() {
        <span class="hljs-keyword">return</span> timeoutMs;
    }
}</code></pre>

<p>不过ListTopicsOptions扩展了一个成员变量listInternal，用来指明是否需要罗列内部Topic，比如在<a href="https://blog.csdn.net/u013256816/article/details/79303825" rel="nofollow">Kafka解析之topic创建(1)</a>中提及的“__consumer_offsets”和“transaction_state”就是两个内部Topic。ListTopicsOptions的代码如【代码清单4】所示：</p>



<pre class="prettyprint"><code class=" hljs axapta"><span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">ListTopicsOptions</span> <span class="hljs-inheritance"><span class="hljs-keyword">extends</span></span> <span class="hljs-title">AbstractOptions</span>&lt;<span class="hljs-title">ListTopicsOptions</span>&gt; {</span>
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">boolean</span> listInternal = <span class="hljs-keyword">false</span>;
    <span class="hljs-keyword">public</span> ListTopicsOptions listInternal(<span class="hljs-keyword">boolean</span> listInternal) {
        <span class="hljs-keyword">this</span>.listInternal = listInternal;
        <span class="hljs-keyword">return</span> <span class="hljs-keyword">this</span>;
    }
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">boolean</span> shouldListInternal() {
        <span class="hljs-keyword">return</span> listInternal;
    }
}</code></pre>

<p>listInternal的值默认为false，如果同时要罗列出目前的内部Topic的话就需要将这个listInternal设置为true，示例代码如【代码清单5】所示：</p>



<pre class="prettyprint"><code class=" hljs cs">@Test
<span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">listTopicsIncludeInternal</span>() throws ExecutionException, InterruptedException {
    ListTopicsOptions listTopicsOptions = <span class="hljs-keyword">new</span> ListTopicsOptions();
    listTopicsOptions.listInternal(<span class="hljs-keyword">true</span>);
    ListTopicsResult result = adminClient.listTopics(listTopicsOptions);
    Collection&lt;TopicListing&gt; list = result.listings().<span class="hljs-keyword">get</span>();
    System.<span class="hljs-keyword">out</span>.println(list);
}</code></pre>

<p>接下去继续讲解listTopics()方法，其返回值为ListTopicResult类型。与ListTopicsOptions对应，KafkaAdminClient中基本所有的应用类方法都有一个类似XXXResult类型的返回值，其内部一般包含一个KafkaFuture，用于异步发送请求之后等待操作结果。KafkaFuture实现了Java中的Future接口，用来支持链式调用以及其他异步编程模型，可以看成是Java8中CompletableFuture的一个小型版本，其中也有类似thenApply、complete、completeExceptionally的方法。</p>

<p>再来看代码清单2中的 runnable.call(new Call(“listTopics”, calcDeadlineMs(now, options.timeoutMs()),new LeastLoadedNodeProvider()) 这行代码，runnable的类型是AdminClientRunnable，其是KafkaAdminClient负责处理与服务端交互请求的服务线程。AdminClientRunnable中的call方法用作入队一个Call请求，进而对其处理。Call请求代表与服务端的一次请求交互，比如listTopics和createTopics都是一次Call请求，AdminClientRunnable线程负责处理这些Call请求。</p>

<p>Call类是一个抽象类，构造方法接收三个参数：本次请求的名称callName、超时时间deadlineMs、以及节点提供器nodeProvider。nodeProvider是NodeProvider类型，用来提供本次请求所交互的Broker节点。Call类中还有3个抽象方法：createRequest()、handleResponse()、handleFailure()，分别用来创建请求、处理回执和处理失败。在代码清单2中，对于listTopics(）方法而言，其内部原理就是发送MetadataRequest请求然后处理MetadataResponse，其处理逻辑峰封装在createRequest()、handleResponse()、handleFailure()这三个方法之中了。</p>

<p>综上，如果要自定义实现一个功能，只需要三个步骤： <br>
1. 自定义XXXOptions; <br>
2. 自定义XXXResult返回值； <br>
3. 自定义Call，然后挑选合适的XXXRequest和XXXResponse来实现Call类中的3个抽象方法。</p>

<p>KafkaAdminClient目前而言尚未形成一个完全体，里面还可以扩展很多功能，就拿上一篇文章《<a href="https://blog.csdn.net/u013256816/article/details/79968647" rel="nofollow">如何获取Kafka的消费者详情——从Scala到Java的切换</a>》中介绍的而言，目前KafkaAdminClient尚未实现describeConsumerGroup和listGroupOffsets的功能，所以需要进一步的升级改造。篇幅限制，这部分内容将在下一篇文章进行介绍，如果想要先睹为快，可以参考下<a href="https://github.com/hiddenzzh/kafka/blob/master/src/main/java/org/apache/kafka/clients/admin/app/KafkaConsumerGroupService.java" rel="nofollow">代码实现</a>，详细的逻辑解析敬请期待….</p>

<hr>

<p>欢迎支持《RabbitMQ实战指南》以及关注微信公众号：朱小厮的博客。 <br>
<img src="https://img-blog.csdn.net/20180116193232073?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzI1NjgxNg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>