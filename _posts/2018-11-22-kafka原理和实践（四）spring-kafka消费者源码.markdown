---
layout:     post
title:      kafka原理和实践（四）spring-kafka消费者源码
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <div class="blogpost-body"><a></a><div><p style="font-size:18px;"><b>目录</b></p><ul><li><a href="#_label0" rel="nofollow">一、kafkaConsumer消费者模型</a></li><li><a href="#_label1" rel="nofollow">一、kafkaConsumer构造</a></li><li><a href="#_label2" rel="nofollow">二、消费者容器启动流程</a></li></ul></div><p> </p><p style="font-size:18px;"><b>正文</b></p><p>系列目录</p>
<p><a class="singleposttitle" href="http://www.cnblogs.com/dennyzhangdd/p/7759869.html" rel="nofollow">kafka原理和实践（一）原理：10分钟入门</a></p>
<p><a href="http://www.cnblogs.com/dennyzhangdd/p/7759875.html" rel="nofollow">kafka原理和实践（二）spring-kafka简单实践</a></p>
<p><a class="singleposttitle" href="http://www.cnblogs.com/dennyzhangdd/p/7827564.html" rel="nofollow">kafka原理和实践（三）spring-kafka生产者源码</a></p>
<p><a href="http://www.cnblogs.com/dennyzhangdd/p/7759876.html" rel="nofollow">kafka原理和实践（四）spring-kafka消费者源码</a></p>
<p><a class="singleposttitle" href="http://www.cnblogs.com/dennyzhangdd/p/7834143.html" rel="nofollow">kafka原理和实践（五）spring-kafka配置详解</a></p>
<p><a href="http://www.cnblogs.com/dennyzhangdd/p/7759878.html" rel="nofollow">kafka原理和实践（六）总结升华</a></p>
<p> </p>
<p> </p>
<p>==============正文分割线=====================</p>
<div style="text-align:right;"><a href="#_labelTop" rel="nofollow">回到顶部</a><a></a></div><h2>一、kafkaConsumer消费者模型</h2>
<p><img src="https://images2017.cnblogs.com/blog/584866/201711/584866-20171123122057196-119766742.png" alt=""></p>
<p>如上图所示，spring-kafka消费者模型主要流程：</p>
<p><span style="background-color:#ff9900;">1.容器启动，轮询执行消费。</span></p>
<p><span style="background-color:#ff9900;">2.kafkaConsumer拉取消息流程：</span></p>
<p><span style="background-color:#ff9900;">1）Fetcher请求获取器获取请求并存储在unset中</span></p>
<p><span style="background-color:#ff9900;">2）ConsumerNetworkClient网络客户端执行poll(),调用NetWlrikClient的send()方法从unset中获取ClientRequest请求转成RequestSend最终塞进Selector的KafkaChannel通道中，Seletcor.send()从kafka集群拉取待消费数据ConsumerRecords</span></p>
<p><span style="background-color:#ff9900;">3. 消费者监听器MessageListener.onMessage()执行用户自定义的实际消费业务逻辑。</span></p>
<div style="text-align:right;"><a href="#_labelTop" rel="nofollow">回到顶部</a><a></a></div><h2>一、kafkaConsumer构造</h2>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color:#008080;">  1</span> @SuppressWarnings("unchecked"<span style="color:#000000;">)
</span><span style="color:#008080;">  2</span>     <span style="color:#0000ff;">private</span><span style="color:#000000;"><span style="color:#ff0000;"> KafkaConsumer</span>(ConsumerConfig config,
</span><span style="color:#008080;">  3</span>                           Deserializer&lt;K&gt;<span style="color:#000000;"> keyDeserializer,
</span><span style="color:#008080;">  4</span>                           Deserializer&lt;V&gt;<span style="color:#000000;"> valueDeserializer) {
</span><span style="color:#008080;">  5</span>         <span style="color:#0000ff;">try</span><span style="color:#000000;"> {
</span><span style="color:#008080;">  6</span>             log.debug("Starting the Kafka consumer"<span style="color:#000000;">);
</span><span style="color:#008080;">  7</span>             <span style="color:#0000ff;">this</span>.requestTimeoutMs =<span style="color:#000000;"> config.getInt(ConsumerConfig.REQUEST_TIMEOUT_MS_CONFIG);
</span><span style="color:#008080;">  8</span>             <span style="color:#0000ff;">int</span> sessionTimeOutMs =<span style="color:#000000;"> config.getInt(ConsumerConfig.SESSION_TIMEOUT_MS_CONFIG);
</span><span style="color:#008080;">  9</span>             <span style="color:#0000ff;">int</span> fetchMaxWaitMs =<span style="color:#000000;"> config.getInt(ConsumerConfig.FETCH_MAX_WAIT_MS_CONFIG);
</span><span style="color:#008080;"> 10</span>             <span style="color:#0000ff;">if</span> (<span style="color:#0000ff;">this</span>.requestTimeoutMs &lt;= sessionTimeOutMs || <span style="color:#0000ff;">this</span>.requestTimeoutMs &lt;=<span style="color:#000000;"> fetchMaxWaitMs)
</span><span style="color:#008080;"> 11</span>                 <span style="color:#0000ff;">throw</span> <span style="color:#0000ff;">new</span> ConfigException(ConsumerConfig.REQUEST_TIMEOUT_MS_CONFIG + " should be greater than " + ConsumerConfig.SESSION_TIMEOUT_MS_CONFIG + " and " +<span style="color:#000000;"> ConsumerConfig.FETCH_MAX_WAIT_MS_CONFIG);
</span><span style="color:#008080;"> 12</span>             <span style="color:#0000ff;">this</span>.time = <span style="color:#0000ff;">new</span><span style="color:#000000;"> SystemTime();
</span><span style="color:#008080;"> 13</span> 
<span style="color:#008080;"> 14</span>             String clientId =<span style="color:#000000;"> config.getString(ConsumerConfig.CLIENT_ID_CONFIG);
</span><span style="color:#008080;"> 15</span>             <span style="color:#0000ff;">if</span> (clientId.length() &lt;= 0<span style="color:#000000;">)
</span><span style="color:#008080;"> 16</span>                 clientId = "consumer-" +<span style="color:#000000;"> CONSUMER_CLIENT_ID_SEQUENCE.getAndIncrement();
</span><span style="color:#008080;"> 17</span>             <span style="color:#0000ff;">this</span>.clientId =<span style="color:#000000;"> clientId;
</span><span style="color:#008080;"> 18</span>             Map&lt;String, String&gt; metricsTags = <span style="color:#0000ff;">new</span> LinkedHashMap&lt;&gt;<span style="color:#000000;">();
</span><span style="color:#008080;"> 19</span>             metricsTags.put("client-id"<span style="color:#000000;">, clientId);
</span><span style="color:#008080;"> 20</span>             MetricConfig metricConfig = <span style="color:#0000ff;">new</span><span style="color:#000000;"> MetricConfig().samples(config.getInt(ConsumerConfig.METRICS_NUM_SAMPLES_CONFIG))
</span><span style="color:#008080;"> 21</span> <span style="color:#000000;">                    .timeWindow(config.getLong(ConsumerConfig.METRICS_SAMPLE_WINDOW_MS_CONFIG), TimeUnit.MILLISECONDS)
</span><span style="color:#008080;"> 22</span> <span style="color:#000000;">                    .tags(metricsTags);
</span><span style="color:#008080;"> 23</span>             List&lt;MetricsReporter&gt; reporters =<span style="color:#000000;"> config.getConfiguredInstances(ConsumerConfig.METRIC_REPORTER_CLASSES_CONFIG,
</span><span style="color:#008080;"> 24</span>                     MetricsReporter.<span style="color:#0000ff;">class</span><span style="color:#000000;">);
</span><span style="color:#008080;"> 25</span>             reporters.add(<span style="color:#0000ff;">new</span><span style="color:#000000;"> JmxReporter(JMX_PREFIX));
</span><span style="color:#008080;"> 26</span>             <span style="color:#0000ff;">this</span>.metrics = <span style="color:#0000ff;">new</span><span style="color:#000000;"> Metrics(metricConfig, reporters, time);
</span><span style="color:#008080;"> 27</span>             <span style="color:#0000ff;">this</span>.retryBackoffMs =<span style="color:#000000;"> config.getLong(ConsumerConfig.RETRY_BACKOFF_MS_CONFIG);
</span><span style="color:#008080;"> 28</span> 
<span style="color:#008080;"> 29</span>             <span style="color:#008000;">//</span><span style="color:#008000;"> load interceptors and make sure they get clientId</span>
<span style="color:#008080;"> 30</span>             Map&lt;String, Object&gt; userProvidedConfigs =<span style="color:#000000;"> config.originals();
</span><span style="color:#008080;"> 31</span> <span style="color:#000000;">            userProvidedConfigs.put(ConsumerConfig.CLIENT_ID_CONFIG, clientId);
</span><span style="color:#008080;"> 32</span>             List&lt;ConsumerInterceptor&lt;K, V&gt;&gt; interceptorList = (List) (<span style="color:#0000ff;">new</span><span style="color:#000000;"> ConsumerConfig(userProvidedConfigs)).getConfiguredInstances(ConsumerConfig.INTERCEPTOR_CLASSES_CONFIG,
</span><span style="color:#008080;"> 33</span>                     ConsumerInterceptor.<span style="color:#0000ff;">class</span><span style="color:#000000;">);
</span><span style="color:#008080;"> 34</span>             <span style="color:#0000ff;">this</span>.interceptors = interceptorList.isEmpty() ? <span style="color:#0000ff;">null</span> : <span style="color:#0000ff;">new</span> ConsumerInterceptors&lt;&gt;<span style="color:#000000;">(interceptorList);
</span><span style="color:#008080;"> 35</span>             <span style="color:#0000ff;">if</span> (keyDeserializer == <span style="color:#0000ff;">null</span><span style="color:#000000;">) {
</span><span style="color:#008080;"> 36</span>                 <span style="color:#0000ff;">this</span>.keyDeserializer =<span style="color:#000000;"> config.getConfiguredInstance(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG,
</span><span style="color:#008080;"> 37</span>                         Deserializer.<span style="color:#0000ff;">class</span><span style="color:#000000;">);
</span><span style="color:#008080;"> 38</span>                 <span style="color:#0000ff;">this</span>.keyDeserializer.configure(config.originals(), <span style="color:#0000ff;">true</span><span style="color:#000000;">);
</span><span style="color:#008080;"> 39</span>             } <span style="color:#0000ff;">else</span><span style="color:#000000;"> {
</span><span style="color:#008080;"> 40</span> <span style="color:#000000;">                config.ignore(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG);
</span><span style="color:#008080;"> 41</span>                 <span style="color:#0000ff;">this</span>.keyDeserializer =<span style="color:#000000;"> keyDeserializer;
</span><span style="color:#008080;"> 42</span> <span style="color:#000000;">            }
</span><span style="color:#008080;"> 43</span>             <span style="color:#0000ff;">if</span> (valueDeserializer == <span style="color:#0000ff;">null</span><span style="color:#000000;">) {
</span><span style="color:#008080;"> 44</span>                 <span style="color:#0000ff;">this</span>.valueDeserializer =<span style="color:#000000;"> config.getConfiguredInstance(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG,
</span><span style="color:#008080;"> 45</span>                         Deserializer.<span style="color:#0000ff;">class</span><span style="color:#000000;">);
</span><span style="color:#008080;"> 46</span>                 <span style="color:#0000ff;">this</span>.valueDeserializer.configure(config.originals(), <span style="color:#0000ff;">false</span><span style="color:#000000;">);
</span><span style="color:#008080;"> 47</span>             } <span style="color:#0000ff;">else</span><span style="color:#000000;"> {
</span><span style="color:#008080;"> 48</span> <span style="color:#000000;">                config.ignore(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG);
</span><span style="color:#008080;"> 49</span>                 <span style="color:#0000ff;">this</span>.valueDeserializer =<span style="color:#000000;"> valueDeserializer;
</span><span style="color:#008080;"> 50</span> <span style="color:#000000;">            }
</span><span style="color:#008080;"> 51</span>             ClusterResourceListeners clusterResourceListeners =<span style="color:#000000;"> configureClusterResourceListeners(keyDeserializer, valueDeserializer, reporters, interceptorList);
</span><span style="color:#008080;"> 52</span>             <span style="color:#0000ff;">this</span>.<span style="color:#ff0000;">metadata</span> = <span style="color:#0000ff;">new</span> Metadata(retryBackoffMs, config.getLong(ConsumerConfig.METADATA_MAX_AGE_CONFIG), <span style="color:#0000ff;">false</span><span style="color:#000000;">, clusterResourceListeners);
</span><span style="color:#008080;"> 53</span>             List&lt;InetSocketAddress&gt; addresses =<span style="color:#000000;"> ClientUtils.parseAndValidateAddresses(config.getList(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG));
</span><span style="color:#008080;"> 54</span>             <span style="color:#0000ff;">this</span>.metadata.update(Cluster.bootstrap(addresses), 0<span style="color:#000000;">);
</span><span style="color:#008080;"> 55</span>             String metricGrpPrefix = "consumer"<span style="color:#000000;">;
</span><span style="color:#008080;"> 56</span>             ChannelBuilder channelBuilder =<span style="color:#000000;"> ClientUtils.createChannelBuilder(config.values());
</span><span style="color:#008080;"> 57</span>             NetworkClient netClient = <span style="color:#0000ff;">new</span><span style="color:#000000;"> NetworkClient(
</span><span style="color:#008080;"> 58</span>                     <span style="color:#0000ff;">new</span><span style="color:#000000;"> Selector(config.getLong(ConsumerConfig.CONNECTIONS_MAX_IDLE_MS_CONFIG), metrics, time, metricGrpPrefix, channelBuilder),
</span><span style="color:#008080;"> 59</span>                     <span style="color:#0000ff;">this</span><span style="color:#000000;">.metadata,
</span><span style="color:#008080;"> 60</span> <span style="color:#000000;">                    clientId,
</span><span style="color:#008080;"> 61</span>                     100, <span style="color:#008000;">//</span><span style="color:#008000;"> a fixed large enough value will suffice</span>
<span style="color:#008080;"> 62</span> <span style="color:#000000;">                    config.getLong(ConsumerConfig.RECONNECT_BACKOFF_MS_CONFIG),
</span><span style="color:#008080;"> 63</span> <span style="color:#000000;">                    config.getInt(ConsumerConfig.SEND_BUFFER_CONFIG),
</span><span style="color:#008080;"> 64</span> <span style="color:#000000;">                    config.getInt(ConsumerConfig.RECEIVE_BUFFER_CONFIG),
</span><span style="color:#008080;"> 65</span> <span style="color:#000000;">                    config.getInt(ConsumerConfig.REQUEST_TIMEOUT_MS_CONFIG), time);
</span><span style="color:#008080;"> 66</span>             <span style="color:#0000ff;">this</span>.<span style="color:#ff0000;">client</span> = <span style="color:#0000ff;">new</span><span style="color:#000000;"> ConsumerNetworkClient(netClient, metadata, time, retryBackoffMs,
</span><span style="color:#008080;"> 67</span> <span style="color:#000000;">                    config.getInt(ConsumerConfig.REQUEST_TIMEOUT_MS_CONFIG));
</span><span style="color:#008080;"> 68</span>             OffsetResetStrategy offsetResetStrategy =<span style="color:#000000;"> OffsetResetStrategy.valueOf(config.getString(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG).toUpperCase(Locale.ROOT));
</span><span style="color:#008080;"> 69</span>             <span style="color:#0000ff;">this</span>.<span style="color:#ff0000;">subscriptions</span> = <span style="color:#0000ff;">new</span><span style="color:#000000;"> SubscriptionState(offsetResetStrategy);
</span><span style="color:#008080;"> 70</span>             List&lt;PartitionAssignor&gt; assignors =<span style="color:#000000;"> config.getConfiguredInstances(
</span><span style="color:#008080;"> 71</span> <span style="color:#000000;">                    ConsumerConfig.PARTITION_ASSIGNMENT_STRATEGY_CONFIG,
</span><span style="color:#008080;"> 72</span>                     PartitionAssignor.<span style="color:#0000ff;">class</span><span style="color:#000000;">);
</span><span style="color:#008080;"> 73</span>             <span style="color:#0000ff;">this</span>.<span style="color:#ff0000;">coordinator</span> = <span style="color:#0000ff;">new</span> ConsumerCoordinator(<span style="color:#0000ff;">this</span><span style="color:#000000;">.client,
</span><span style="color:#008080;"> 74</span> <span style="color:#000000;">                    config.getString(ConsumerConfig.GROUP_ID_CONFIG),
</span><span style="color:#008080;"> 75</span> <span style="color:#000000;">                    config.getInt(ConsumerConfig.MAX_POLL_INTERVAL_MS_CONFIG),
</span><span style="color:#008080;"> 76</span> <span style="color:#000000;">                    config.getInt(ConsumerConfig.SESSION_TIMEOUT_MS_CONFIG),
</span><span style="color:#008080;"> 77</span> <span style="color:#000000;">                    config.getInt(ConsumerConfig.HEARTBEAT_INTERVAL_MS_CONFIG),
</span><span style="color:#008080;"> 78</span> <span style="color:#000000;">                    assignors,
</span><span style="color:#008080;"> 79</span>                     <span style="color:#0000ff;">this</span><span style="color:#000000;">.metadata,
</span><span style="color:#008080;"> 80</span>                     <span style="color:#0000ff;">this</span><span style="color:#000000;">.subscriptions,
</span><span style="color:#008080;"> 81</span> <span style="color:#000000;">                    metrics,
</span><span style="color:#008080;"> 82</span> <span style="color:#000000;">                    metricGrpPrefix,
</span><span style="color:#008080;"> 83</span>                     <span style="color:#0000ff;">this</span><span style="color:#000000;">.time,
</span><span style="color:#008080;"> 84</span> <span style="color:#000000;">                    retryBackoffMs,
</span><span style="color:#008080;"> 85</span>                     <span style="color:#0000ff;">new</span><span style="color:#000000;"> ConsumerCoordinator.DefaultOffsetCommitCallback(),
</span><span style="color:#008080;"> 86</span> <span style="color:#000000;">                    config.getBoolean(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG),
</span><span style="color:#008080;"> 87</span> <span style="color:#000000;">                    config.getInt(ConsumerConfig.AUTO_COMMIT_INTERVAL_MS_CONFIG),
</span><span style="color:#008080;"> 88</span>                     <span style="color:#0000ff;">this</span><span style="color:#000000;">.interceptors,
</span><span style="color:#008080;"> 89</span> <span style="color:#000000;">                    config.getBoolean(ConsumerConfig.EXCLUDE_INTERNAL_TOPICS_CONFIG));
</span><span style="color:#008080;"> 90</span>             <span style="color:#0000ff;">this</span>.<span style="color:#ff0000;">fetcher</span> = <span style="color:#0000ff;">new</span> Fetcher&lt;&gt;(<span style="color:#0000ff;">this</span><span style="color:#000000;">.client,
</span><span style="color:#008080;"> 91</span> <span style="color:#000000;">                    config.getInt(ConsumerConfig.FETCH_MIN_BYTES_CONFIG),
</span><span style="color:#008080;"> 92</span> <span style="color:#000000;">                    config.getInt(ConsumerConfig.FETCH_MAX_BYTES_CONFIG),
</span><span style="color:#008080;"> 93</span> <span style="color:#000000;">                    config.getInt(ConsumerConfig.FETCH_MAX_WAIT_MS_CONFIG),
</span><span style="color:#008080;"> 94</span> <span style="color:#000000;">                    config.getInt(ConsumerConfig.MAX_PARTITION_FETCH_BYTES_CONFIG),
</span><span style="color:#008080;"> 95</span> <span style="color:#000000;">                    config.getInt(ConsumerConfig.MAX_POLL_RECORDS_CONFIG),
</span><span style="color:#008080;"> 96</span> <span style="color:#000000;">                    config.getBoolean(ConsumerConfig.CHECK_CRCS_CONFIG),
</span><span style="color:#008080;"> 97</span>                     <span style="color:#0000ff;">this</span><span style="color:#000000;">.keyDeserializer,
</span><span style="color:#008080;"> 98</span>                     <span style="color:#0000ff;">this</span><span style="color:#000000;">.valueDeserializer,
</span><span style="color:#008080;"> 99</span>                     <span style="color:#0000ff;">this</span><span style="color:#000000;">.metadata,
</span><span style="color:#008080;">100</span>                     <span style="color:#0000ff;">this</span><span style="color:#000000;">.subscriptions,
</span><span style="color:#008080;">101</span> <span style="color:#000000;">                    metrics,
</span><span style="color:#008080;">102</span> <span style="color:#000000;">                    metricGrpPrefix,
</span><span style="color:#008080;">103</span>                     <span style="color:#0000ff;">this</span><span style="color:#000000;">.time,
</span><span style="color:#008080;">104</span>                     <span style="color:#0000ff;">this</span><span style="color:#000000;">.retryBackoffMs);
</span><span style="color:#008080;">105</span> 
<span style="color:#008080;">106</span> <span style="color:#000000;">            config.logUnused();
</span><span style="color:#008080;">107</span> <span style="color:#000000;">            AppInfoParser.registerAppInfo(JMX_PREFIX, clientId);
</span><span style="color:#008080;">108</span> 
<span style="color:#008080;">109</span>             log.debug("Kafka consumer created"<span style="color:#000000;">);
</span><span style="color:#008080;">110</span>         } <span style="color:#0000ff;">catch</span><span style="color:#000000;"> (Throwable t) {
</span><span style="color:#008080;">111</span>             <span style="color:#008000;">//</span><span style="color:#008000;"> call close methods if internal objects are already constructed
</span><span style="color:#008080;">112</span>             <span style="color:#008000;">//</span><span style="color:#008000;"> this is to prevent resource leak. see KAFKA-2121</span>
<span style="color:#008080;">113</span>             close(<span style="color:#0000ff;">true</span><span style="color:#000000;">);
</span><span style="color:#008080;">114</span>             <span style="color:#008000;">//</span><span style="color:#008000;"> now propagate the exception</span>
<span style="color:#008080;">115</span>             <span style="color:#0000ff;">throw</span> <span style="color:#0000ff;">new</span> KafkaException("Failed to construct kafka consumer"<span style="color:#000000;">, t);
</span><span style="color:#008080;">116</span> <span style="color:#000000;">        }
</span><span style="color:#008080;">117</span>     }</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p> 从<span style="color:#000000;"><span style="color:#ff0000;">KafkaConsumer构造函数来看，核心组件有：<br></span></span></p>
<p><span style="color:#000000;">1.</span><strong>Metadata</strong>：封装了元数据的一些逻辑的类。元数据仅保留一个主题的子集，随着时间的推移可以添加。当我们请求一个主题的元数据时，我们没有任何元数据会触发元数据更新。如果对元数据启用了主题过期，那么在更新之后，在过期时间间隔内未使用的任何主题都将从元数据刷新集中删除。</p>
<p>2<strong>.</strong><span style="color:#000000;"><strong>ConsumerNetworkClient</strong>：高等级消费者访问网络层，为请求Future任务<span style="color:#000000;">提供基本支持。这个类是线程安全的，但是不提供响应回调的同步。这保证在调用它们时不会持有锁。</span></span></p>
<p>3.<span style="color:#000000;"><strong>SubscriptionState</strong>：订阅的TopicPartition的offset状态维护</span></p>
<p><span style="color:#000000;">4.<strong>ConsumerCoordinator</strong>：消费者的协调者，负责partitiion的分配，reblance</span></p>
<p>
<span style="color:#000000;">5.<strong>Fetcher</strong>：从brokers上按照配置获取消息。</span></p>
<div style="text-align:right;"><a href="#_labelTop" rel="nofollow">回到顶部</a><a></a></div><h2><span style="color:#000000;">二、消费者容器启动流程</span></h2>
<p>kafka消费者有两种常见的实现方式：</p>
<p><span style="color:#ff0000;">1.xml配置文件</span></p>
<p><span style="color:#ff0000;">2.基于注解实现</span></p>
<p><span style="color:#ff0000;"><span style="color:#000000;">其实，</span>不管哪种方式，本质只是生成Spring Bean的方式不同而已。我们就以xml的实现方式来追踪源码。<br></span></p>
<p><span style="color:#000000;">基于xml的总体配置如下：</span></p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color:#008080;"> 1 &lt;!-- 1.定义consumer的参数 --&gt;
<span style="color:#008080;"> 2     &lt;bean id="<span style="color:#ff0000;">consumerProperties" <span style="color:#0000ff;">class="java.util.HashMap"&gt;
<span style="color:#008080;"> 3         &lt;constructor-arg&gt;
<span style="color:#008080;"> 4             &lt;map&gt;
<span style="color:#008080;"> 5                 &lt;entry key="bootstrap.servers" value="${bootstrap.servers}" /&gt;
<span style="color:#008080;"> 6                 &lt;entry key="group.id" value="${group.id}" /&gt;
<span style="color:#008080;"> 7                 &lt;entry key="enable.auto.commit" value="${enable.auto.commit}" /&gt;
<span style="color:#008080;"> 8                 &lt;entry key="session.timeout.ms" value="${session.timeout.ms}" /&gt;
<span style="color:#008080;"> 9                 &lt;entry key="key.deserializer"
<span style="color:#008080;">10                     value="org.apache.kafka.common.serialization.StringDeserializer" /&gt;
<span style="color:#008080;">11                 &lt;entry key="value.deserializer"
<span style="color:#008080;">12                     value="org.apache.kafka.common.serialization.StringDeserializer" /&gt;
<span style="color:#008080;">13             &lt;/map&gt;
<span style="color:#008080;">14         &lt;/constructor-arg&gt;
<span style="color:#008080;">15     &lt;/bean&gt;
<span style="color:#008080;">16 
<span style="color:#008080;">17     &lt;!-- 2.创建consumerFactory bean --&gt;
<span style="color:#008080;">18     &lt;bean id="consumerFactory"
<span style="color:#008080;">19         <span style="color:#0000ff;">class="org.springframework.kafka.core.<span style="color:#ff0000;">DefaultKafkaConsumerFactory" &gt;
<span style="color:#008080;">20         &lt;constructor-arg&gt;
<span style="color:#008080;">21             &lt;ref bean="consumerProperties" /&gt;
<span style="color:#008080;">22         &lt;/constructor-arg&gt;
<span style="color:#008080;">23     &lt;/bean&gt;
<span style="color:#008080;">24 
<span style="color:#008080;">25     &lt;!-- 3.定义消费实现类 --&gt;
<span style="color:#008080;">26     &lt;bean id="kafkaConsumerService" <span style="color:#0000ff;">class="<span style="color:#ff0000;">xxx.service.impl.KafkaConsumerSerivceImpl" /&gt;
<span style="color:#008080;">27 
<span style="color:#008080;">28     &lt;!-- 4.消费者容器配置信息 --&gt;
<span style="color:#008080;">29     &lt;bean id="containerProperties" <span style="color:#0000ff;">class="org.springframework.kafka.listener.config.<span style="color:#ff0000;">ContainerProperties"&gt;
<span style="color:#008080;">30         &lt;!-- topic --&gt;
<span style="color:#008080;">31         &lt;constructor-arg name="topics"&gt;
<span style="color:#008080;">32             &lt;list&gt;
<span style="color:#008080;">33                 &lt;value&gt;${kafka.consumer.topic.credit.<span style="color:#0000ff;">for.lease}&lt;/value&gt;
<span style="color:#008080;">34                 &lt;value&gt;${loan.application.feedback.topic}&lt;/value&gt;
<span style="color:#008080;">35                 &lt;value&gt;${templar.agreement.feedback.topic}&lt;/value&gt;
<span style="color:#008080;">36                 &lt;value&gt;${templar.aggrement.active.feedback.topic}&lt;/value&gt;
<span style="color:#008080;">37                 &lt;value&gt;${templar.aggrement.agreementRepaid.topic}&lt;/value&gt;
<span style="color:#008080;">38                 &lt;value&gt;${templar.aggrement.agreementWithhold.topic}&lt;/value&gt;
<span style="color:#008080;">39                 &lt;value&gt;${templar.aggrement.agreementRepayRemind.topic}&lt;/value&gt;
<span style="color:#008080;">40             &lt;/list&gt;
<span style="color:#008080;">41         &lt;/constructor-arg&gt;
<span style="color:#008080;">42         &lt;property name="<span style="color:#ff0000;">messageListener" ref="kafkaConsumerService" /&gt;
<span style="color:#008080;">43     &lt;/bean&gt;
<span style="color:#008080;">44     &lt;!-- 5.<span style="color:#ff0000;">消费者并发消息监听容器，执行<span style="color:#ff0000;">doStart()方法 --&gt;
<span style="color:#008080;">45     &lt;bean id="messageListenerContainer" <span style="color:#0000ff;">class="org.springframework.kafka.listener.<span style="color:#ff0000;">ConcurrentMessageListenerContainer" init-method="<span style="color:#ff0000;">doStart" &gt;
<span style="color:#008080;">46         &lt;constructor-arg ref="<span style="color:#ff0000;">consumerFactory" /&gt;
<span style="color:#008080;">47         &lt;constructor-arg ref="<span style="color:#ff0000;">containerProperties" /&gt;
<span style="color:#008080;">48         &lt;property name="concurrency" value="${concurrency}" /&gt;
<span style="color:#008080;">49     &lt;/bean&gt;</span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p> 分为5个步骤：</p>
<h4> 2.1.定义消费参数bean</h4>
<p><span style="color:#ff0000;"><span style="color:#000000;">consumerProperties ,就是个map&lt;key,value&gt;</span></span></p>
<h4>2.2.创建consumerFactory bean</h4>
<pre><span style="color:#ff0000;">DefaultKafkaConsumerFactory</span> 实现了ConsumerFactory接口，提供<span style="color:#ff0000;">创建消费者</span>和<span style="color:#ff0000;">判断是否自动提交</span>2个方法。通过<span style="color:#ff0000;"><span style="color:#000000;">consumerProperties作为参数构造。</span></span></pre>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color:#008080;">1</span> <span style="color:#0000ff;">public</span> <span style="color:#0000ff;">interface</span> ConsumerFactory&lt;K, V&gt;<span style="color:#000000;"> {
</span><span style="color:#008080;">2</span> 
<span style="color:#008080;">3</span>     Consumer&lt;K, V&gt;<span style="color:#000000;"> createConsumer();
</span><span style="color:#008080;">4</span> 
<span style="color:#008080;">5</span>     <span style="color:#0000ff;">boolean</span><span style="color:#000000;"> isAutoCommit();
</span><span style="color:#008080;">6</span> 
<span style="color:#008080;">7</span> 
<span style="color:#008080;">8</span> }</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<h4>2.3.定义消费实现类</h4>
<p>自定义一个类实现MessageListener接口，接口设计如下：</p>
<p><img src="https://images2017.cnblogs.com/blog/584866/201711/584866-20171103101001201-1676699925.png" alt=""></p>
<p>实现<span style="color:#ff0000;">onMessage</span>方法，去消费接收到的消息。两种方案：</p>
<p>1）<span style="color:#ff0000;">MessageListener</span> 消费完消息后<span style="color:#ff0000;">自动提交offset</span><span style="color:#000000;">(enable.auto.commit=true时),可提高效率，存在消费失败但移动了偏移量的风险。</span></p>
<p>2）<span style="color:#000000;"><span style="color:#ff0000;">AcknowledgingMessageListener</span> <span style="color:#ff0000;"><span style="color:#000000;">消费完消息后</span>手动提交offset</span>(enable.auto.commit=false时)效率降低，无消费失败但移动偏移量的风险。</span></p>
<h4>2.4.监听容器配置信息</h4>
<pre><span style="color:#ff0000;">ContainerProperties：包含了一个监听容器的运行时配置信息，主要定义了监听的主题、分区、初始化偏移量，还有消息监听器。</span></pre>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color:#008080;">  1</span> <span style="color:#0000ff;">public</span> <span style="color:#0000ff;">class</span><span style="color:#000000;"> ContainerProperties {
</span><span style="color:#008080;">  2</span> 
<span style="color:#008080;">  3</span>     <span style="color:#0000ff;">private</span> <span style="color:#0000ff;">static</span> <span style="color:#0000ff;">final</span> <span style="color:#0000ff;">int</span> DEFAULT_SHUTDOWN_TIMEOUT = 10000<span style="color:#000000;">;
</span><span style="color:#008080;">  4</span> 
<span style="color:#008080;">  5</span>     <span style="color:#0000ff;">private</span> <span style="color:#0000ff;">static</span> <span style="color:#0000ff;">final</span> <span style="color:#0000ff;">int</span> DEFAULT_QUEUE_DEPTH = 1<span style="color:#000000;">;
</span><span style="color:#008080;">  6</span> 
<span style="color:#008080;">  7</span>     <span style="color:#0000ff;">private</span> <span style="color:#0000ff;">static</span> <span style="color:#0000ff;">final</span> <span style="color:#0000ff;">int</span> DEFAULT_PAUSE_AFTER = 10000<span style="color:#000000;">;
</span><span style="color:#008080;">  8</span> 
<span style="color:#008080;">  9</span>     <span style="color:#008000;">/**</span>
<span style="color:#008080;"> 10</span> <span style="color:#008000;">     * Topic names.监听的主题字符串数组
</span><span style="color:#008080;"> 11</span>      <span style="color:#008000;">*/</span>
<span style="color:#008080;"> 12</span>     <span style="color:#0000ff;">private</span> <span style="color:#0000ff;">final</span><span style="color:#000000;"> String[] </span><span style="color:#ff0000;">topics</span><span style="color:#000000;">;
</span><span style="color:#008080;"> 13</span> 
<span style="color:#008080;"> 14</span>     <span style="color:#008000;">/**</span>
<span style="color:#008080;"> 15</span> <span style="color:#008000;">     * Topic pattern.监听的主题模板
</span><span style="color:#008080;"> 16</span>      <span style="color:#008000;">*/</span>
<span style="color:#008080;"> 17</span>     <span style="color:#0000ff;">private</span> <span style="color:#0000ff;">final</span><span style="color:#000000;"> Pattern topicPattern;
</span><span style="color:#008080;"> 18</span> 
<span style="color:#008080;"> 19</span>     <span style="color:#008000;">/**</span>
<span style="color:#008080;"> 20</span> <span style="color:#008000;">     * Topics/partitions/initial offsets.
</span><span style="color:#008080;"> 21</span>      <span style="color:#008000;">*/</span>
<span style="color:#008080;"> 22</span>     <span style="color:#0000ff;">private</span> <span style="color:#0000ff;">final</span><span style="color:#000000;"> TopicPartitionInitialOffset[] topicPartitions;
</span><span style="color:#008080;"> 23</span> 
<span style="color:#008080;"> 24</span>     <span style="color:#008000;">/**</span>
<span style="color:#008080;"> 25</span> <span style="color:#008000;">     * 确认模式（自动确认属性为false时使用）
</span><span style="color:#008080;"> 26</span> <span style="color:#008000;">     * &lt;ul&gt;
</span><span style="color:#008080;"> 27</span> <span style="color:#008000;">     * &lt;li&gt;1.RECORD逐条确认: 每条消息被发送给监听者后确认&lt;/li&gt;
</span><span style="color:#008080;"> 28</span> <span style="color:#008000;">     * &lt;li&gt;2.BATCH批量确认: 当批量消息记录被消费者接收到并传送给监听器时确认</span><span style="color:#008000;">&lt;/li&gt;
</span><span style="color:#008080;"> 30</span> <span style="color:#008000;">     * &lt;li&gt;3.TIME超时确认:当超过设置的超时时间毫秒数时确认(should be greater than
</span><span style="color:#008080;"> 31</span> <span style="color:#008000;">     * {</span><span style="color:#808080;">@code</span><span style="color:#008000;"> #setPollTimeout(long) pollTimeout}.&lt;/li&gt;
</span><span style="color:#008080;"> 32</span> <span style="color:#008000;">     * &lt;li&gt;4.COUNT计数确认: 当接收到指定数量之后确认&lt;/li&gt;
</span><span style="color:#008080;"> 33</span> <span style="color:#008000;">     * &lt;li&gt;5.MANUAL手动确认：由监听器负责确认（<span style="color:#008000;">AcknowledgingMessageListener</span>）</span><span style="color:#008000;">&lt;/ul&gt;
</span><span style="color:#008080;"> 36</span>      <span style="color:#008000;">*/</span>
<span style="color:#008080;"> 37</span>     <span style="color:#0000ff;">private</span> AbstractMessageListenerContainer.AckMode ackMode =<span style="color:#000000;"> AckMode.BATCH;
</span><span style="color:#008080;"> 38</span> 
<span style="color:#008080;"> 39</span>     <span style="color:#008000;">/**</span>
<span style="color:#008080;"> 40</span> <span style="color:#008000;">     * The number of outstanding record count after which offsets should be
</span><span style="color:#008080;"> 41</span> <span style="color:#008000;">     * committed when {</span><span style="color:#808080;">@link</span><span style="color:#008000;"> AckMode#COUNT} or {</span><span style="color:#808080;">@link</span><span style="color:#008000;"> AckMode#COUNT_TIME} is being
</span><span style="color:#008080;"> 42</span> <span style="color:#008000;">     * used.
</span><span style="color:#008080;"> 43</span>      <span style="color:#008000;">*/</span>
<span style="color:#008080;"> 44</span>     <span style="color:#0000ff;">private</span> <span style="color:#0000ff;">int</span><span style="color:#000000;"> ackCount;
</span><span style="color:#008080;"> 45</span> 
<span style="color:#008080;"> 46</span>     <span style="color:#008000;">/**</span>
<span style="color:#008080;"> 47</span> <span style="color:#008000;">     * The time (ms) after which outstanding offsets should be committed when
</span><span style="color:#008080;"> 48</span> <span style="color:#008000;">     * {</span><span style="color:#808080;">@link</span><span style="color:#008000;"> AckMode#TIME} or {</span><span style="color:#808080;">@link</span><span style="color:#008000;"> AckMode#COUNT_TIME} is being used. Should be
</span><span style="color:#008080;"> 49</span> <span style="color:#008000;">     * larger than
</span><span style="color:#008080;"> 50</span>      <span style="color:#008000;">*/</span>
<span style="color:#008080;"> 51</span>     <span style="color:#0000ff;">private</span> <span style="color:#0000ff;">long</span><span style="color:#000000;"> ackTime;
</span><span style="color:#008080;"> 52</span> 
<span style="color:#008080;"> 53</span>     <span style="color:#008000;">/**</span>
<span style="color:#008080;"> 54</span> <span style="color:#008000;">     * 消息监听器，必须是 <span style="color:#008000;">MessageListener或者<span style="color:#008000;">AcknowledgingMessageListener两者中的一个</span></span></span><span style="color:#008080;"> 55</span> <span style="color:#008000;">     *</span><span style="color:#008080;"> 56</span>      <span style="color:#008000;">*/</span>
<span style="color:#008080;"> 57</span>     <span style="color:#0000ff;">private</span><span style="color:#000000;"> Object <span style="color:#ff0000;">messageListener</span>;
</span><span style="color:#008080;"> 58</span> 
<span style="color:#008080;"> 59</span>     <span style="color:#008000;">/**</span>
<span style="color:#008080;"> 60</span> <span style="color:#008000;">     * The max time to block in the consumer waiting for records.
</span><span style="color:#008080;"> 61</span>      <span style="color:#008000;">*/</span>
<span style="color:#008080;"> 62</span>     <span style="color:#0000ff;">private</span> <span style="color:#0000ff;">volatile</span> <span style="color:#0000ff;">long</span> pollTimeout = 1000<span style="color:#000000;">;
</span><span style="color:#008080;"> 63</span> 
<span style="color:#008080;"> 64</span>     <span style="color:#008000;">/**</span>
<span style="color:#008080;"> 65</span> <span style="color:#008000;">     * 线程执行器：轮询消费者
</span><span style="color:#008080;"> 66</span>      <span style="color:#008000;">*/</span>
<span style="color:#008080;"> 67</span>     <span style="color:#0000ff;">private</span><span style="color:#000000;"> AsyncListenableTaskExecutor consumerTaskExecutor;
</span><span style="color:#008080;"> 68</span> 
<span style="color:#008080;"> 69</span>     <span style="color:#008000;">/**</span>
<span style="color:#008080;"> 70</span> <span style="color:#008000;">     * <span style="color:#008000;">线程执行器：调用监听器</span>
</span><span style="color:#008080;"> 71</span>      <span style="color:#008000;">*/</span>
<span style="color:#008080;"> 72</span>     <span style="color:#0000ff;">private</span><span style="color:#000000;"> AsyncListenableTaskExecutor listenerTaskExecutor;
</span><span style="color:#008080;"> 73</span> 
<span style="color:#008080;"> 74</span>     <span style="color:#008000;">/**</span>
<span style="color:#008080;"> 75</span> <span style="color:#008000;">     * 错误回调，当监听器抛出异常时
</span><span style="color:#008080;"> 76</span>      <span style="color:#008000;">*/</span>
<span style="color:#008080;"> 77</span>     <span style="color:#0000ff;">private</span> GenericErrorHandler&lt;?&gt;<span style="color:#000000;"> errorHandler;
</span><span style="color:#008080;"> 78</span> 
<span style="color:#008080;"> 79</span>     <span style="color:#008000;">/**</span>
<span style="color:#008080;"> 80</span> <span style="color:#008000;">     * When using Kafka group management and {</span><span style="color:#808080;">@link</span><span style="color:#008000;"> #setPauseEnabled(boolean)} is
</span><span style="color:#008080;"> 81</span> <span style="color:#008000;">     * true, the delay after which the consumer should be paused. Default 10000.
</span><span style="color:#008080;"> 82</span>      <span style="color:#008000;">*/</span>
<span style="color:#008080;"> 83</span>     <span style="color:#0000ff;">private</span> <span style="color:#0000ff;">long</span> pauseAfter =<span style="color:#000000;"> DEFAULT_PAUSE_AFTER;
</span><span style="color:#008080;"> 84</span> 
<span style="color:#008080;"> 85</span>     <span style="color:#008000;">/**</span>
<span style="color:#008080;"> 86</span> <span style="color:#008000;">     * When true, avoids rebalancing when this consumer is slow or throws a
</span><span style="color:#008080;"> 87</span> <span style="color:#008000;">     * qualifying exception - pauses the consumer. Default: true.
</span><span style="color:#008080;"> 88</span> <span style="color:#008000;">     * </span><span style="color:#808080;">@see</span><span style="color:#008000;"> #pauseAfter
</span><span style="color:#008080;"> 89</span>      <span style="color:#008000;">*/</span>
<span style="color:#008080;"> 90</span>     <span style="color:#0000ff;">private</span> <span style="color:#0000ff;">boolean</span> pauseEnabled = <span style="color:#0000ff;">true</span><span style="color:#000000;">;
</span><span style="color:#008080;"> 91</span> 
<span style="color:#008080;"> 92</span>     <span style="color:#008000;">/**</span>
<span style="color:#008080;"> 93</span> <span style="color:#008000;">     * Set the queue depth for handoffs from the consumer thread to the listener
</span><span style="color:#008080;"> 94</span> <span style="color:#008000;">     * thread. Default 1 (up to 2 in process).
</span><span style="color:#008080;"> 95</span>      <span style="color:#008000;">*/</span>
<span style="color:#008080;"> 96</span>     <span style="color:#0000ff;">private</span> <span style="color:#0000ff;">int</span> queueDepth =<span style="color:#000000;"> DEFAULT_QUEUE_DEPTH;
</span><span style="color:#008080;"> 97</span> 
<span style="color:#008080;"> 98</span>     <span style="color:#008000;">/**</span>
<span style="color:#008080;"> 99</span> <span style="color:#008000;">     * 停止容器超时时间</span>    <span style="color:#008000;">*/</span>
<span style="color:#008080;">103</span>     <span style="color:#0000ff;">private</span> <span style="color:#0000ff;">long</span> shutdownTimeout =<span style="color:#000000;"> DEFAULT_SHUTDOWN_TIMEOUT;
</span><span style="color:#008080;">104</span> 
<span style="color:#008080;">105</span>     <span style="color:#008000;">/**</span>
<span style="color:#008080;">106</span> <span style="color:#008000;">     * 用户定义的消费者再平衡监听器实现类</span>     <span style="color:#008000;">*/</span>
<span style="color:#008080;">108</span>     <span style="color:#0000ff;">private</span><span style="color:#000000;"> ConsumerRebalanceListener consumerRebalanceListener;
</span><span style="color:#008080;">109</span> 
<span style="color:#008080;">110</span>     <span style="color:#008000;">/**</span>
<span style="color:#008080;">111</span> <span style="color:#008000;">     * 提交回调，默认记录日志。</span>      <span style="color:#008000;">*/</span>
<span style="color:#008080;">114</span>     <span style="color:#0000ff;">private</span><span style="color:#000000;"> OffsetCommitCallback commitCallback;
</span><span style="color:#008080;">115</span> 
<span style="color:#008080;">116</span>     <span style="color:#008000;">/**</span>
<span style="color:#008080;">117</span> <span style="color:#008000;">     * Whether or not to call consumer.commitSync() or commitAsync() when the
</span><span style="color:#008080;">118</span> <span style="color:#008000;">     * container is responsible for commits. Default true. See
</span><span style="color:#008080;">119</span> <span style="color:#008000;">     * </span><span style="color:#008000;text-decoration:underline;">https://github.com/spring-projects/spring-kafka/issues/62</span><span style="color:#008000;"> At the time of
</span><span style="color:#008080;">120</span> <span style="color:#008000;">     * writing, async commits are not entirely reliable.
</span><span style="color:#008080;">121</span>      <span style="color:#008000;">*/</span>
<span style="color:#008080;">122</span>     <span style="color:#0000ff;">private</span> <span style="color:#0000ff;">boolean</span> syncCommits = <span style="color:#0000ff;">true</span><span style="color:#000000;">;
</span><span style="color:#008080;">123</span> 
<span style="color:#008080;">124</span>     <span style="color:#0000ff;">private</span> <span style="color:#0000ff;">boolean</span> ackOnError = <span style="color:#0000ff;">true</span><span style="color:#000000;">;
</span><span style="color:#008080;">125</span> 
<span style="color:#008080;">126</span>     <span style="color:#0000ff;">private</span><span style="color:#000000;"> Long idleEventInterval;
</span><span style="color:#008080;">127</span> 
<span style="color:#008080;">128</span>     <span style="color:#0000ff;">public</span><span style="color:#000000;"><span style="color:#ff0000;"> ContainerProperties</span>(String... topics) {
</span><span style="color:#008080;">129</span>         Assert.notEmpty(topics, "An array of topicPartitions must be provided"<span style="color:#000000;">);
</span><span style="color:#008080;">130</span>         <span style="color:#0000ff;">this</span>.topics = Arrays.asList(topics).toArray(<span style="color:#0000ff;">new</span><span style="color:#000000;"> String[topics.length]);
</span><span style="color:#008080;">131</span>         <span style="color:#0000ff;">this</span>.topicPattern = <span style="color:#0000ff;">null</span><span style="color:#000000;">;
</span><span style="color:#008080;">132</span>         <span style="color:#0000ff;">this</span>.topicPartitions = <span style="color:#0000ff;">null</span><span style="color:#000000;">;
</span><span style="color:#008080;">133</span> <span style="color:#000000;">    }
</span><span style="color:#008080;">134</span> 
<span style="color:#008080;">135</span>     <span style="color:#0000ff;">public</span><span style="color:#000000;"> ContainerProperties(Pattern topicPattern) {
</span><span style="color:#008080;">136</span>         <span style="color:#0000ff;">this</span>.topics = <span style="color:#0000ff;">null</span><span style="color:#000000;">;
</span><span style="color:#008080;">137</span>         <span style="color:#0000ff;">this</span>.topicPattern =<span style="color:#000000;"> topicPattern;
</span><span style="color:#008080;">138</span>         <span style="color:#0000ff;">this</span>.topicPartitions = <span style="color:#0000ff;">null</span><span style="color:#000000;">;
</span><span style="color:#008080;">139</span> <span style="color:#000000;">    }
</span><span style="color:#008080;">140</span> 
<span style="color:#008080;">141</span>     <span style="color:#0000ff;">public</span><span style="color:#000000;"> ContainerProperties(TopicPartitionInitialOffset... topicPartitions) {
</span><span style="color:#008080;">142</span>         <span style="color:#0000ff;">this</span>.topics = <span style="color:#0000ff;">null</span><span style="color:#000000;">;
</span><span style="color:#008080;">143</span>         <span style="color:#0000ff;">this</span>.topicPattern = <span style="color:#0000ff;">null</span><span style="color:#000000;">;
</span><span style="color:#008080;">144</span>         Assert.notEmpty(topicPartitions, "An array of topicPartitions must be provided"<span style="color:#000000;">);
</span><span style="color:#008080;">145</span>         <span style="color:#0000ff;">this</span>.topicPartitions = <span style="color:#0000ff;">new</span> LinkedHashSet&lt;&gt;<span style="color:#000000;">(Arrays.asList(topicPartitions))
</span><span style="color:#008080;">146</span>                 .toArray(<span style="color:#0000ff;">new</span><span style="color:#000000;"> TopicPartitionInitialOffset[topicPartitions.length]);
</span><span style="color:#008080;">147</span> <span style="color:#000000;">    }
</span><span style="color:#008080;">148</span> <span style="color:#000000;">...省略各种set、get
</span><span style="color:#008080;">149</span> 
<span style="color:#008080;">150</span> }</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p> </p>
<h4><span style="color:#000000;">2.5.启动并发消息监听容器</span></h4>
<p>核心类<span style="color:#ff0000;">ConcurrentMessageListenerContainer</span>,继承自抽象类<span style="color:#ff0000;">AbstractMessageListenerContainer</span>，类图如下：</p>
<p><img src="https://images2017.cnblogs.com/blog/584866/201711/584866-20171106093320434-1867289530.png" alt=""></p>
<p> </p>
<p>看上图可知AbstractMessageListenerContainer有2个实现类分别对应单线程和多线程，建议采用多线程消费。下面分析一下主要ConcurrentMessageListenerContainer类，注意2个方法：</p>
<p>1.构造函数，入参：消费者工厂ConsumerFactory+容器配置<span style="color:#000000;">ContainerProperties </span></p>
<p>2.<span style="color:#000000;"><span style="color:#ff0000;">doStart()：核心方法KafkaMessageListenerContainer的<span style="color:#ff0000;">start</span>()方法。<span style="color:#000000;">源码如下：</span></span></span></p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color:#008080;">  1</span> <span style="color:#0000ff;">public</span> <span style="color:#0000ff;">class</span> ConcurrentMessageListenerContainer&lt;K, V&gt; <span style="color:#0000ff;">extends</span> AbstractMessageListenerContainer&lt;K, V&gt;<span style="color:#000000;"> {
</span><span style="color:#008080;">  2</span> 
<span style="color:#008080;">  3</span>     <span style="color:#0000ff;">private</span> <span style="color:#0000ff;">final</span> ConsumerFactory&lt;K, V&gt;<span style="color:#000000;"> consumerFactory;
</span><span style="color:#008080;">  4</span> 
<span style="color:#008080;">  5</span>     <span style="color:#0000ff;">private</span> <span style="color:#0000ff;">final</span> List&lt;KafkaMessageListenerContainer&lt;K, V&gt;&gt; containers = <span style="color:#0000ff;">new</span> ArrayList&lt;&gt;<span style="color:#000000;">();
</span><span style="color:#008080;">  6</span> 
<span style="color:#008080;">  7</span>     <span style="color:#0000ff;">private</span> <span style="color:#0000ff;">int</span> concurrency = 1<span style="color:#000000;">;
</span><span style="color:#008080;">  8</span> 
<span style="color:#008080;">  9</span>     <span style="color:#008000;">/**</span>
<span style="color:#008080;"> 10</span> <span style="color:#008000;">     * Construct an instance with the supplied configuration properties.
</span><span style="color:#008080;"> 11</span> <span style="color:#008000;">     * The topic partitions are distributed evenly across the delegate
</span><span style="color:#008080;"> 12</span> <span style="color:#008000;">     * {</span><span style="color:#808080;">@link</span><span style="color:#008000;"> KafkaMessageListenerContainer}s.
</span><span style="color:#008080;"> 13</span> <span style="color:#008000;">     * </span><span style="color:#808080;">@param</span><span style="color:#008000;"> consumerFactory the consumer factory.
</span><span style="color:#008080;"> 14</span> <span style="color:#008000;">     * </span><span style="color:#808080;">@param</span><span style="color:#008000;"> containerProperties the container properties.
</span><span style="color:#008080;"> 15</span>      <span style="color:#008000;">*/</span>
<span style="color:#008080;"> 16</span>     <span style="color:#0000ff;">public</span> <span style="color:#ff0000;">ConcurrentMessageListenerContainer</span>(ConsumerFactory&lt;K, V&gt;<span style="color:#000000;"> consumerFactory,
</span><span style="color:#008080;"> 17</span> <span style="color:#000000;">            ContainerProperties containerProperties) {
</span><span style="color:#008080;"> 18</span>         <span style="color:#0000ff;">super</span><span style="color:#000000;">(containerProperties);
</span><span style="color:#008080;"> 19</span>         Assert.notNull(consumerFactory, "A ConsumerFactory must be provided"<span style="color:#000000;">);
</span><span style="color:#008080;"> 20</span>         <span style="color:#0000ff;">this</span>.consumerFactory =<span style="color:#000000;"> consumerFactory;
</span><span style="color:#008080;"> 21</span> <span style="color:#000000;">    }
</span><span style="color:#008080;"> 22</span> 
<span style="color:#008080;"> 23</span>     <span style="color:#0000ff;">public</span> <span style="color:#0000ff;">int</span><span style="color:#000000;"> getConcurrency() {
</span><span style="color:#008080;"> 24</span>         <span style="color:#0000ff;">return</span> <span style="color:#0000ff;">this</span><span style="color:#000000;">.concurrency;
</span><span style="color:#008080;"> 25</span> <span style="color:#000000;">    }
</span><span style="color:#008080;"> 26</span> 
<span style="color:#008080;"> 27</span>     <span style="color:#008000;">/**</span>
<span style="color:#008080;"> 28</span> <span style="color:#008000;">     * The maximum number of concurrent {</span><span style="color:#808080;">@link</span><span style="color:#008000;"> KafkaMessageListenerContainer}s running.
</span><span style="color:#008080;"> 29</span> <span style="color:#008000;">     * Messages from within the same partition will be processed sequentially.
</span><span style="color:#008080;"> 30</span> <span style="color:#008000;">     * </span><span style="color:#808080;">@param</span><span style="color:#008000;"> concurrency the concurrency.
</span><span style="color:#008080;"> 31</span>      <span style="color:#008000;">*/</span>
<span style="color:#008080;"> 32</span>     <span style="color:#0000ff;">public</span> <span style="color:#0000ff;">void</span> setConcurrency(<span style="color:#0000ff;">int</span><span style="color:#000000;"> concurrency) {
</span><span style="color:#008080;"> 33</span>         Assert.isTrue(concurrency &gt; 0, "concurrency must be greater than 0"<span style="color:#000000;">);
</span><span style="color:#008080;"> 34</span>         <span style="color:#0000ff;">this</span>.concurrency =<span style="color:#000000;"> concurrency;
</span><span style="color:#008080;"> 35</span> <span style="color:#000000;">    }
</span><span style="color:#008080;"> 36</span> 
<span style="color:#008080;"> 37</span>     <span style="color:#008000;">/**</span>
<span style="color:#008080;"> 38</span> <span style="color:#008000;">     * Return the list of {</span><span style="color:#808080;">@link</span><span style="color:#008000;"> KafkaMessageListenerContainer}s created by
</span><span style="color:#008080;"> 39</span> <span style="color:#008000;">     * this container.
</span><span style="color:#008080;"> 40</span> <span style="color:#008000;">     * </span><span style="color:#808080;">@return</span><span style="color:#008000;"> the list of {</span><span style="color:#808080;">@link</span><span style="color:#008000;"> KafkaMessageListenerContainer}s created by
</span><span style="color:#008080;"> 41</span> <span style="color:#008000;">     * this container.
</span><span style="color:#008080;"> 42</span>      <span style="color:#008000;">*/</span>
<span style="color:#008080;"> 43</span>     <span style="color:#0000ff;">public</span> List&lt;KafkaMessageListenerContainer&lt;K, V&gt;&gt;<span style="color:#000000;"> getContainers() {
</span><span style="color:#008080;"> 44</span>         <span style="color:#0000ff;">return</span> Collections.unmodifiableList(<span style="color:#0000ff;">this</span><span style="color:#000000;">.containers);
</span><span style="color:#008080;"> 45</span> <span style="color:#000000;">    }
</span><span style="color:#008080;"> 46</span> 
<span style="color:#008080;"> 47</span>     <span style="color:#008000;">/*</span>
<span style="color:#008080;"> 48</span> <span style="color:#008000;">     * Under lifecycle lock.
</span><span style="color:#008080;"> 49</span>      <span style="color:#008000;">*/</span>
<span style="color:#008080;"> 50</span> <span style="color:#000000;">    @Override
</span><span style="color:#008080;"> 51</span>     <span style="color:#0000ff;">protected</span> <span style="color:#0000ff;">void</span><span style="color:#000000;"><span style="color:#ff0000;"> doStart</span>() {
</span><span style="color:#008080;"> 52</span>         <span style="color:#0000ff;">if</span> (!<span style="color:#000000;">isRunning()) {
</span><span style="color:#008080;"> 53</span>             ContainerProperties containerProperties =<span style="color:#000000;"> getContainerProperties();
</span><span style="color:#008080;"> 54</span>             TopicPartitionInitialOffset[] topicPartitions =<span style="color:#000000;"> containerProperties.getTopicPartitions();
</span><span style="color:#008080;"> 55</span>             <span style="color:#0000ff;">if</span> (topicPartitions != <span style="color:#0000ff;">null<span style="color:#008000;">/<span style="color:#008000;">/校验并发数&gt;分区数，报错。</span></span></span>
<span style="color:#008080;"> 56</span>                     &amp;&amp; <span style="color:#0000ff;">this</span>.concurrency &gt;<span style="color:#000000;"> topicPartitions.length) {
</span><span style="color:#008080;"> 57</span>                 <span style="color:#0000ff;">this</span>.logger.warn("When specific partitions are provided, the concurrency must be less than or "
<span style="color:#008080;"> 58</span>                         + "equal to the number of partitions; reduced from " + <span style="color:#0000ff;">this</span>.concurrency + " to "
<span style="color:#008080;"> 59</span>                         +<span style="color:#000000;"> topicPartitions.length);
</span><span style="color:#008080;"> 60</span>                 <span style="color:#0000ff;">this</span>.concurrency =<span style="color:#000000;"> topicPartitions.length;<span style="color:#0000ff;"><span style="color:#008000;">/<span style="color:#008000;">/并发数最大只能=分区数</span></span></span>
</span><span style="color:#008080;"> 61</span> <span style="color:#000000;">            }
</span><span style="color:#008080;"> 62</span>             setRunning(<span style="color:#0000ff;">true</span><span style="color:#000000;">);
</span><span style="color:#008080;"> 63</span>             <span style="color:#000000;"><span style="color:#0000ff;"><span style="color:#008000;">/<span style="color:#008000;">/遍历创建监听器容器</span></span></span></span>
<span style="color:#008080;"> 64</span>             <span style="color:#0000ff;">for</span> (<span style="color:#0000ff;">int</span> i = 0; i &lt; <span style="color:#0000ff;">this</span>.concurrency; i++<span style="color:#000000;">) {
</span><span style="color:#008080;"> 65</span>                 KafkaMessageListenerContainer&lt;K, V&gt;<span style="color:#000000;"> container;
</span><span style="color:#008080;"> 66</span>                 <span style="color:#0000ff;">if</span> (topicPartitions == <span style="color:#0000ff;">null</span><span style="color:#000000;">) {
</span><span style="color:#008080;"> 67</span>                     container = <span style="color:#0000ff;">new</span> KafkaMessageListenerContainer&lt;&gt;(<span style="color:#0000ff;">this</span><span style="color:#000000;">.consumerFactory, containerProperties);
</span><span style="color:#008080;"> 68</span> <span style="color:#000000;">                }
</span><span style="color:#008080;"> 69</span>                 <span style="color:#0000ff;">else</span><span style="color:#000000;"> {
</span><span style="color:#008080;"> 70</span>                     container = <span style="color:#0000ff;">new</span> KafkaMessageListenerContainer&lt;&gt;(<span style="color:#0000ff;">this</span><span style="color:#000000;">.consumerFactory, containerProperties,
</span><span style="color:#008080;"> 71</span> <span style="color:#000000;">                            partitionSubset(containerProperties, i));
</span><span style="color:#008080;"> 72</span> <span style="color:#000000;">                }
</span><span style="color:#008080;"> 73</span>                 <span style="color:#0000ff;">if</span> (getBeanName() != <span style="color:#0000ff;">null</span><span style="color:#000000;">) {
</span><span style="color:#008080;"> 74</span>                     container.setBeanName(getBeanName() + "-" +<span style="color:#000000;"> i);
</span><span style="color:#008080;"> 75</span> <span style="color:#000000;">                }
</span><span style="color:#008080;"> 76</span>                 <span style="color:#0000ff;">if</span> (getApplicationEventPublisher() != <span style="color:#0000ff;">null</span><span style="color:#000000;">) {
</span><span style="color:#008080;"> 77</span> <span style="color:#000000;">                    container.setApplicationEventPublisher(getApplicationEventPublisher());
</span><span style="color:#008080;"> 78</span> <span style="color:#000000;">                }
</span><span style="color:#008080;"> 79</span>                 container.setClientIdSuffix("-" +<span style="color:#000000;"> i);
</span><span style="color:#008080;"> 80</span> <span style="color:#ff0000;">                container.start();//核心方法，启动容器
</span><span style="color:#008080;"> 81</span>                 <span style="color:#0000ff;">this</span><span style="color:#000000;">.containers.add(container);
</span><span style="color:#008080;"> 82</span> <span style="color:#000000;">            }
</span><span style="color:#008080;"> 83</span> <span style="color:#000000;">        }
</span><span style="color:#008080;"> 84</span> <span style="color:#000000;">    }</span><span style="color:#008080;">146</span> ...省略
<span style="color:#008080;">147</span> }</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p> 继续追踪<span style="color:#000000;">，调用AbstractMessageListenerContainer的<span style="color:#ff0000;">doStart</span>(),值得注意的是start()和stop方法加了同一把锁，用于锁住生命周期。</span></p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color:#008080;"> 1</span> <span style="color:#0000ff;">private</span> <span style="color:#0000ff;">final</span> Object <span style="color:#ff0000;">lifecycleMonitor</span> = <span style="color:#0000ff;">new</span><span style="color:#000000;"> Object();
</span><span style="color:#008080;"> 2</span> 
<span style="color:#008080;"> 3</span> <span style="color:#000000;">@Override
</span><span style="color:#008080;"> 4</span>     <span style="color:#0000ff;">public</span> <span style="color:#0000ff;">final</span> <span style="color:#0000ff;">void</span><span style="color:#000000;"> start() {
</span><span style="color:#008080;"> 5</span>         <span style="color:#0000ff;">synchronized</span> (<span style="color:#0000ff;">this</span><span style="color:#000000;">.lifecycleMonitor) {
</span><span style="color:#008080;"> 6</span> <span style="color:#000000;">            Assert.isTrue(
</span><span style="color:#008080;"> 7</span>                     <span style="color:#0000ff;">this</span>.containerProperties.getMessageListener() <span style="color:#0000ff;">instanceof</span><span style="color:#000000;"> KafkaDataListener,
</span><span style="color:#008080;"> 8</span>                     "A " + KafkaDataListener.<span style="color:#0000ff;">class</span>.getName() + " implementation must be provided"<span style="color:#000000;">);
</span><span style="color:#008080;"> 9</span> <span style="color:#000000;"><span style="color:#ff0000;">            doStart</span>();
</span><span style="color:#008080;">10</span> <span style="color:#000000;">        }
</span><span style="color:#008080;">11</span> <span style="color:#000000;">    }
</span><span style="color:#008080;">12</span> 
<span style="color:#008080;">13</span>     <span style="color:#0000ff;">protected</span> <span style="color:#0000ff;">abstract</span> <span style="color:#0000ff;">void</span> <span style="color:#ff0000;">doStart</span>();</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>最终调用的是KafkaMessageListenerContainer的<span style="color:#ff0000;">doStart()</span></p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color:#008080;"> 1</span> <span style="color:#000000;">@Override
</span><span style="color:#008080;"> 2</span>     <span style="color:#0000ff;">protected</span> <span style="color:#0000ff;">void</span><span style="color:#000000;"><span style="color:#ff0000;"> doStart</span>() {
</span><span style="color:#008080;"> 3</span>         <span style="color:#0000ff;">if</span><span style="color:#000000;"> (isRunning()) {
</span><span style="color:#008080;"> 4</span>             <span style="color:#0000ff;">return</span><span style="color:#000000;">;
</span><span style="color:#008080;"> 5</span> <span style="color:#000000;">        }
</span><span style="color:#008080;"> 6</span>         ContainerProperties containerProperties =<span style="color:#000000;"> getContainerProperties();
</span><span style="color:#008080;"> 7</span> 
<span style="color:#008080;"> 8</span>         <span style="color:#0000ff;">if</span> (!<span style="color:#0000ff;">this</span><span style="color:#000000;">.consumerFactory.isAutoCommit()) {
</span><span style="color:#008080;"> 9</span>             AckMode ackMode =<span style="color:#000000;"> containerProperties.getAckMode();
</span><span style="color:#008080;">10</span>             <span style="color:#0000ff;">if</span> (ackMode.equals(AckMode.COUNT) ||<span style="color:#000000;"> ackMode.equals(AckMode.COUNT_TIME)) {
</span><span style="color:#008080;">11</span>                 Assert.state(containerProperties.getAckCount() &gt; 0, "'ackCount' must be &gt; 0"<span style="color:#000000;">);
</span><span style="color:#008080;">12</span> <span style="color:#000000;">            }
</span><span style="color:#008080;">13</span>             <span style="color:#0000ff;">if</span> ((ackMode.equals(AckMode.TIME) ||<span style="color:#000000;"> ackMode.equals(AckMode.COUNT_TIME))
</span><span style="color:#008080;">14</span>                     &amp;&amp; containerProperties.getAckTime() == 0<span style="color:#000000;">) {
</span><span style="color:#008080;">15</span>                 containerProperties.setAckTime(5000<span style="color:#000000;">);
</span><span style="color:#008080;">16</span> <span style="color:#000000;">            }
</span><span style="color:#008080;">17</span> <span style="color:#000000;">        }
</span><span style="color:#008080;">18</span> 
<span style="color:#008080;">19</span>         Object messageListener =<span style="color:#000000;"> containerProperties.getMessageListener();
</span><span style="color:#008080;">20</span>         Assert.state(messageListener != <span style="color:#0000ff;">null</span>, "A MessageListener is required"<span style="color:#000000;">);
</span><span style="color:#008080;">21</span>         <span style="color:#0000ff;">if</span> (messageListener <span style="color:#0000ff;">instanceof</span><span style="color:#000000;"> GenericAcknowledgingMessageListener) {
</span><span style="color:#008080;">22</span>             <span style="color:#0000ff;">this</span>.acknowledgingMessageListener = (GenericAcknowledgingMessageListener&lt;?&gt;<span style="color:#000000;">) messageListener;
</span><span style="color:#008080;">23</span> <span style="color:#000000;">        }
</span><span style="color:#008080;">24</span>         <span style="color:#0000ff;">else</span> <span style="color:#0000ff;">if</span> (messageListener <span style="color:#0000ff;">instanceof</span><span style="color:#000000;"> GenericMessageListener) {
</span><span style="color:#008080;">25</span>             <span style="color:#0000ff;">this</span>.listener = (GenericMessageListener&lt;?&gt;<span style="color:#000000;">) messageListener;
</span><span style="color:#008080;">26</span> <span style="color:#000000;">        }
</span><span style="color:#008080;">27</span>         <span style="color:#0000ff;">else</span><span style="color:#000000;"> {
</span><span style="color:#008080;">28</span>             <span style="color:#0000ff;">throw</span> <span style="color:#0000ff;">new</span> IllegalStateException("messageListener must be 'MessageListener' "
<span style="color:#008080;">29</span>                     + "or 'AcknowledgingMessageListener', not " +<span style="color:#000000;"> messageListener.getClass().getName());
</span><span style="color:#008080;">30</span> <span style="color:#000000;">        }
</span><span style="color:#008080;">31</span>         <span style="color:#0000ff;">if</span> (containerProperties.getConsumerTaskExecutor() == <span style="color:#0000ff;">null</span><span style="color:#000000;">) {
</span><span style="color:#008080;">32</span>             SimpleAsyncTaskExecutor consumerExecutor = <span style="color:#0000ff;">new</span><span style="color:#000000;"> SimpleAsyncTaskExecutor(
</span><span style="color:#008080;">33</span>                     (getBeanName() == <span style="color:#0000ff;">null</span> ? "" : getBeanName()) + "-C-"<span style="color:#000000;">);
</span><span style="color:#008080;">34</span> <span style="color:#000000;">            containerProperties.setConsumerTaskExecutor(consumerExecutor);
</span><span style="color:#008080;">35</span> <span style="color:#000000;">        }
</span><span style="color:#008080;">36</span>         <span style="color:#0000ff;">if</span> (containerProperties.getListenerTaskExecutor() == <span style="color:#0000ff;">null</span><span style="color:#000000;">) {
</span><span style="color:#008080;">37</span>             SimpleAsyncTaskExecutor listenerExecutor = <span style="color:#0000ff;">new</span><span style="color:#000000;"> SimpleAsyncTaskExecutor(
</span><span style="color:#008080;">38</span>                     (getBeanName() == <span style="color:#0000ff;">null</span> ? "" : getBeanName()) + "-L-"<span style="color:#000000;">);
</span><span style="color:#008080;">39</span> <span style="color:#000000;">            containerProperties.setListenerTaskExecutor(listenerExecutor);
</span><span style="color:#008080;">40</span> <span style="color:#000000;">        }<span style="color:#000000;"><span style="color:#0000ff;"><span style="color:#008000;">/<span style="color:#008000;">/1.构建 监听消费者</span></span></span></span>
</span><span style="color:#008080;">41</span>         <span style="color:#0000ff;">this</span>.listenerConsumer = <span style="color:#0000ff;">new</span> <span style="color:#ff0000;">ListenerConsumer</span>(<span style="color:#0000ff;">this</span>.listener, <span style="color:#0000ff;">this</span><span style="color:#000000;">.acknowledgingMessageListener);
</span><span style="color:#008080;">42</span>         setRunning(<span style="color:#0000ff;">true</span><span style="color:#000000;">);<br><span style="color:#000000;"><span style="color:#0000ff;"><span style="color:#008000;">　　　　　　/<span style="color:#008000;">/2.异步提交 监听消费者任务，返回Future并赋值。</span></span></span></span>
</span><span style="color:#008080;">43</span>         <span style="color:#0000ff;">this</span>.listenerConsumerFuture =<span style="color:#000000;"> containerProperties
</span><span style="color:#008080;">44</span> <span style="color:#000000;">                .getConsumerTaskExecutor()
</span><span style="color:#008080;">45</span>                 .<span style="color:#ff0000;">submitListenable</span>(<span style="color:#0000ff;">this</span><span style="color:#000000;">.listenerConsumer);
</span><span style="color:#008080;">46</span>     }</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p> </p>
<p><span style="color:#000000;"><span style="color:#ff0000;">doStart</span></span>主要包含2个操作：构建内部类<span style="color:#ff0000;">ListenerConsumer</span><span style="color:#000000;">和</span><span style="color:#ff0000;">提交 监听消费者任务</span>，返回Future并赋值。</p>
<h4><span style="color:#ff0000;">1.构建内部类</span><span style="color:#ff0000;">ListenerConsumer</span></h4>
<p>ListenerConsumer类图如下：</p>
<p><img src="https://images2017.cnblogs.com/blog/584866/201711/584866-20171106105831778-1608297677.png" alt=""></p>
<p>ListenerConsumer构造函数源码如下：</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color:#008080;"> 1</span> @SuppressWarnings("unchecked"<span style="color:#000000;">)
</span><span style="color:#008080;"> 2</span>         ListenerConsumer(GenericMessageListener&lt;?&gt; listener, GenericAcknowledgingMessageListener&lt;?&gt;<span style="color:#000000;"> ackListener) {
</span><span style="color:#008080;"> 3</span>             Assert.state(!<span style="color:#0000ff;">this</span>.isAnyManualAck || !<span style="color:#0000ff;">this</span><span style="color:#000000;">.autoCommit,
</span><span style="color:#008080;"> 4</span>                     "Consumer cannot be configured for auto commit for ackMode " + <span style="color:#0000ff;">this</span><span style="color:#000000;">.containerProperties.getAckMode());
</span><span style="color:#008080;"> 5</span>             @SuppressWarnings("deprecation"<span style="color:#000000;">)
</span><span style="color:#008080;"> 6</span>             <span style="color:#0000ff;">final</span> Consumer&lt;K, V&gt; <span style="color:#ff0000;">consumer</span> =
<span style="color:#008080;"> 7</span>                     KafkaMessageListenerContainer.<span style="color:#0000ff;">this</span>.consumerFactory <span style="color:#0000ff;">instanceof</span>
<span style="color:#008080;"> 8</span> <span style="color:#000000;">                                    org.springframework.kafka.core.ClientIdSuffixAware
</span><span style="color:#008080;"> 9</span>                             ? ((org.springframework.kafka.core.ClientIdSuffixAware&lt;K, V&gt;<span style="color:#000000;">) KafkaMessageListenerContainer
</span><span style="color:#008080;">10</span>                                     .<span style="color:#0000ff;">this</span><span style="color:#000000;">.consumerFactory)
</span><span style="color:#008080;">11</span>                                         .<span style="color:#ff0000;">createConsumer</span>(KafkaMessageListenerContainer.<span style="color:#0000ff;">this</span><span style="color:#000000;">.clientIdSuffix)
</span><span style="color:#008080;">12</span>                             : KafkaMessageListenerContainer.<span style="color:#0000ff;">this</span><span style="color:#000000;">.consumerFactory.<span style="color:#ff0000;">createConsumer</span>();
</span><span style="color:#008080;">13</span> 
<span style="color:#008080;">14</span>             <span style="color:#0000ff;">this</span>.theListener = listener == <span style="color:#0000ff;">null</span> ?<span style="color:#000000;"> ackListener : listener;
</span><span style="color:#008080;">15</span>             ConsumerRebalanceListener rebalanceListener =<span style="color:#000000;"> createRebalanceListener(consumer);
</span><span style="color:#008080;">16</span> 
<span style="color:#008080;">17</span>             <span style="color:#0000ff;">if</span> (KafkaMessageListenerContainer.<span style="color:#0000ff;">this</span>.topicPartitions == <span style="color:#0000ff;">null</span><span style="color:#000000;">) {
</span><span style="color:#008080;">18</span>                 <span style="color:#0000ff;">if</span> (<span style="color:#0000ff;">this</span>.containerProperties.getTopicPattern() != <span style="color:#0000ff;">null</span><span style="color:#000000;">) {
</span><span style="color:#008080;">19</span>                     consumer.<span style="color:#ff0000;">subscribe</span>(<span style="color:#0000ff;">this</span><span style="color:#000000;">.containerProperties.getTopicPattern(), rebalanceListener);
</span><span style="color:#008080;">20</span> <span style="color:#000000;">                }
</span><span style="color:#008080;">21</span>                 <span style="color:#0000ff;">else</span><span style="color:#000000;"> {
</span><span style="color:#008080;">22</span>                     consumer.<span style="color:#ff0000;">subscribe</span>(Arrays.asList(<span style="color:#0000ff;">this</span><span style="color:#000000;">.containerProperties.getTopics()), rebalanceListener);
</span><span style="color:#008080;">23</span> <span style="color:#000000;">                }
</span><span style="color:#008080;">24</span> <span style="color:#000000;">            }
</span><span style="color:#008080;">25</span>             <span style="color:#0000ff;">else</span><span style="color:#000000;"> {
</span><span style="color:#008080;">26</span>                 List&lt;TopicPartitionInitialOffset&gt; topicPartitions =
<span style="color:#008080;">27</span>                         Arrays.asList(KafkaMessageListenerContainer.<span style="color:#0000ff;">this</span><span style="color:#000000;">.topicPartitions);
</span><span style="color:#008080;">28</span>                 <span style="color:#0000ff;">this</span>.definedPartitions = <span style="color:#0000ff;">new</span> HashMap&lt;&gt;<span style="color:#000000;">(topicPartitions.size());
</span><span style="color:#008080;">29</span>                 <span style="color:#0000ff;">for</span><span style="color:#000000;"> (TopicPartitionInitialOffset topicPartition : topicPartitions) {
</span><span style="color:#008080;">30</span>                     <span style="color:#0000ff;">this</span><span style="color:#000000;">.definedPartitions.put(topicPartition.topicPartition(),
</span><span style="color:#008080;">31</span>                             <span style="color:#0000ff;">new</span><span style="color:#000000;"> OffsetMetadata(topicPartition.initialOffset(), topicPartition.isRelativeToCurrent()));
</span><span style="color:#008080;">32</span> <span style="color:#000000;">                }
</span><span style="color:#008080;">33</span>                 consumer.assign(<span style="color:#0000ff;">new</span> ArrayList&lt;&gt;(<span style="color:#0000ff;">this</span><span style="color:#000000;">.definedPartitions.keySet()));
</span><span style="color:#008080;">34</span> <span style="color:#000000;">            }
</span><span style="color:#008080;">35</span>             <span style="color:#ff0000;">this.consumer = consumer;
</span><span style="color:#008080;">36</span>             GenericErrorHandler&lt;?&gt; errHandler = <span style="color:#0000ff;">this</span><span style="color:#000000;">.containerProperties.getGenericErrorHandler();
</span><span style="color:#008080;">37</span>             <span style="color:#0000ff;">this</span>.genericListener = listener; <br><span style="color:#000000;"><span style="color:#0000ff;"><span style="color:#008000;">/<span style="color:#008000;">/1.</span></span></span></span><br><span style="color:#0000ff;">               if</span> (<span style="color:#0000ff;">this</span>.theListener <span style="color:#0000ff;">instanceof</span><span style="color:#000000;"> BatchAcknowledgingMessageListener) {
</span><span style="color:#008080;">38</span>                 <span style="color:#0000ff;">this</span>.listener = <span style="color:#0000ff;">null</span><span style="color:#000000;">;
</span><span style="color:#008080;">39</span>                 <span style="color:#0000ff;">this</span>.batchListener = <span style="color:#0000ff;">null</span><span style="color:#000000;">;
</span><span style="color:#008080;">40</span>                 <span style="color:#0000ff;">this</span>.acknowledgingMessageListener = <span style="color:#0000ff;">null</span><span style="color:#000000;">;
</span><span style="color:#008080;">41</span>                 <span style="color:#0000ff;">this</span>.batchAcknowledgingMessageListener = (BatchAcknowledgingMessageListener&lt;K, V&gt;) <span style="color:#0000ff;">this</span><span style="color:#000000;">.theListener;
</span><span style="color:#008080;">42</span>                 <span style="color:#0000ff;">this</span>.isBatchListener = <span style="color:#0000ff;">true</span><span style="color:#000000;">;
</span><span style="color:#008080;">43</span> <span style="color:#000000;">            }<span style="color:#000000;"><span style="color:#0000ff;"><span style="color:#008000;">/<span style="color:#008000;">/2.</span></span></span></span>
</span><span style="color:#008080;">44</span>             <span style="color:#0000ff;">else</span> <span style="color:#0000ff;">if</span> (<span style="color:#0000ff;">this</span>.theListener <span style="color:#0000ff;">instanceof</span><span style="color:#000000;"> AcknowledgingMessageListener) {
</span><span style="color:#008080;">45</span>                 <span style="color:#0000ff;">this</span>.listener = <span style="color:#0000ff;">null</span><span style="color:#000000;">;
</span><span style="color:#008080;">46</span>                 <span style="color:#0000ff;">this</span>.acknowledgingMessageListener = (AcknowledgingMessageListener&lt;K, V&gt;) <span style="color:#0000ff;">this</span><span style="color:#000000;">.theListener;
</span><span style="color:#008080;">47</span>                 <span style="color:#0000ff;">this</span>.batchListener = <span style="color:#0000ff;">null</span><span style="color:#000000;">;
</span><span style="color:#008080;">48</span>                 <span style="color:#0000ff;">this</span>.batchAcknowledgingMessageListener = <span style="color:#0000ff;">null</span><span style="color:#000000;">;
</span><span style="color:#008080;">49</span>                 <span style="color:#0000ff;">this</span>.isBatchListener = <span style="color:#0000ff;">false</span><span style="color:#000000;">;
</span><span style="color:#008080;">50</span> <span style="color:#000000;">            }<span style="color:#000000;"><span style="color:#0000ff;"><span style="color:#008000;">/<span style="color:#008000;">/3.</span></span></span></span>
</span><span style="color:#008080;">51</span>             <span style="color:#0000ff;">else</span> <span style="color:#0000ff;">if</span> (<span style="color:#0000ff;">this</span>.theListener <span style="color:#0000ff;">instanceof</span><span style="color:#000000;"> BatchMessageListener) {
</span><span style="color:#008080;">52</span>                 <span style="color:#0000ff;">this</span>.listener = <span style="color:#0000ff;">null</span><span style="color:#000000;">;
</span><span style="color:#008080;">53</span>                 <span style="color:#0000ff;">this</span>.batchListener = (BatchMessageListener&lt;K, V&gt;) <span style="color:#0000ff;">this</span><span style="color:#000000;">.theListener;
</span><span style="color:#008080;">54</span>                 <span style="color:#0000ff;">this</span>.acknowledgingMessageListener = <span style="color:#0000ff;">null</span><span style="color:#000000;">;
</span><span style="color:#008080;">55</span>                 <span style="color:#0000ff;">this</span>.batchAcknowledgingMessageListener = <span style="color:#0000ff;">null</span><span style="color:#000000;">;
</span><span style="color:#008080;">56</span>                 <span style="color:#0000ff;">this</span>.isBatchListener = <span style="color:#0000ff;">true</span><span style="color:#000000;">;
</span><span style="color:#008080;">57</span> <span style="color:#000000;">            }<span style="color:#000000;"><span style="color:#0000ff;"><span style="color:#008000;">/<span style="color:#008000;">/4.</span></span></span></span>
</span><span style="color:#008080;">58</span>             <span style="color:#0000ff;">else</span> <span style="color:#0000ff;">if</span> (<span style="color:#0000ff;">this</span>.theListener <span style="color:#0000ff;">instanceof</span><span style="color:#000000;"> MessageListener) {
</span><span style="color:#008080;">59</span>                 <span style="color:#0000ff;">this</span>.listener = (MessageListener&lt;K, V&gt;) <span style="color:#0000ff;">this</span><span style="color:#000000;">.theListener;
</span><span style="color:#008080;">60</span>                 <span style="color:#0000ff;">this</span>.batchListener = <span style="color:#0000ff;">null</span><span style="color:#000000;">;
</span><span style="color:#008080;">61</span>                 <span style="color:#0000ff;">this</span>.acknowledgingMessageListener = <span style="color:#0000ff;">null</span><span style="color:#000000;">;
</span><span style="color:#008080;">62</span>                 <span style="color:#0000ff;">this</span>.batchAcknowledgingMessageListener = <span style="color:#0000ff;">null</span><span style="color:#000000;">;
</span><span style="color:#008080;">63</span>                 <span style="color:#0000ff;">this</span>.isBatchListener = <span style="color:#0000ff;">false</span><span style="color:#000000;">;
</span><span style="color:#008080;">64</span> <span style="color:#000000;">            }
</span><span style="color:#008080;">65</span>             <span style="color:#0000ff;">else</span><span style="color:#000000;"> {
</span><span style="color:#008080;">66</span>                 <span style="color:#0000ff;">throw</span> <span style="color:#0000ff;">new</span> IllegalArgumentException("Listener must be one of 'MessageListener', "
<span style="color:#008080;">67</span>                         + "'BatchMessageListener', 'AcknowledgingMessageListener', "
<span style="color:#008080;">68</span>                         + "'BatchAcknowledgingMessageListener', not " + <span style="color:#0000ff;">this</span><span style="color:#000000;">.theListener.getClass().getName());
</span><span style="color:#008080;">69</span> <span style="color:#000000;">            }
</span><span style="color:#008080;">70</span>             <span style="color:#0000ff;">if</span> (<span style="color:#0000ff;">this</span><span style="color:#000000;">.isBatchListener) {
</span><span style="color:#008080;">71</span>                 validateErrorHandler(<span style="color:#0000ff;">true</span><span style="color:#000000;">);
</span><span style="color:#008080;">72</span>                 <span style="color:#0000ff;">this</span>.errorHandler = <span style="color:#0000ff;">new</span><span style="color:#000000;"> LoggingErrorHandler();
</span><span style="color:#008080;">73</span>                 <span style="color:#0000ff;">this</span>.batchErrorHandler = errHandler == <span style="color:#0000ff;">null</span> ? <span style="color:#0000ff;">new</span><span style="color:#000000;"> BatchLoggingErrorHandler()
</span><span style="color:#008080;">74</span> <span style="color:#000000;">                        : (BatchErrorHandler) errHandler;
</span><span style="color:#008080;">75</span> <span style="color:#000000;">            }
</span><span style="color:#008080;">76</span>             <span style="color:#0000ff;">else</span><span style="color:#000000;"> {
</span><span style="color:#008080;">77</span>                 validateErrorHandler(<span style="color:#0000ff;">false</span><span style="color:#000000;">);
</span><span style="color:#008080;">78</span>                 <span style="color:#0000ff;">this</span>.errorHandler = errHandler == <span style="color:#0000ff;">null</span> ? <span style="color:#0000ff;">new</span><span style="color:#000000;"> LoggingErrorHandler() : (ErrorHandler) errHandler;
</span><span style="color:#008080;">79</span>                 <span style="color:#0000ff;">this</span>.batchErrorHandler = <span style="color:#0000ff;">new</span><span style="color:#000000;"> BatchLoggingErrorHandler();
</span><span style="color:#008080;">80</span> <span style="color:#000000;">            }
</span><span style="color:#008080;">81</span>             Assert.state(!<span style="color:#0000ff;">this</span>.isBatchListener || !<span style="color:#0000ff;">this</span>.isRecordAck, "Cannot use AckMode.RECORD with a batch listener"<span style="color:#000000;">);
</span><span style="color:#008080;">82</span>         }</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p><span style="color:#ff0000;">1.定义消费者订阅topic或者指定分区<br></span></p>
<p><span style="color:#ff0000;">2.设置监听器，支持4种：</span></p>
<p><span style="color:#000000;">　　1）BatchAcknowledgingMessageListener批量需确认消息监听器</span></p>
<p><span style="color:#000000;">　　2）AcknowledgingMessageListener需确认消息监听器</span></p>
<p><span style="color:#000000;">　　3）BatchMessageListener批量消息监听器</span></p>
<p><span style="color:#000000;">　　4）<span style="color:#ff0000;">MessageListener消息监听器（用的最多，一次消费一条）</span></span></p>
<p> </p>
<h4><span style="color:#ff0000;">2.提交 监听消费者任务（ListenerConsumer），返回Future并赋值。</span></h4>
<p><span style="color:#000000;">这里我们看一下任务Runnable接口的run方法，分两种情况<br></span></p>
<p><span style="color:#000000;"><span style="color:#000000;"><span style="color:#008000;"><span style="color:#ff0000;">1.如果自定义了分区,没必要再平衡分配分区了，直接回调</span></span></span></span></p>
<p><span style="color:#000000;"><span style="color:#000000;"><span style="color:#008000;"><span style="color:#ff0000;"><span style="color:#000000;"><span style="color:#ff0000;">2.未指定分区，进入自旋消费</span></span></span></span></span></span></p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color:#008080;"> 1</span> <span style="color:#000000;">@Override
</span><span style="color:#008080;"> 2</span>         <span style="color:#0000ff;">public</span> <span style="color:#0000ff;">void</span><span style="color:#000000;"> run() {
</span><span style="color:#008080;"> 3</span>             <span style="color:#0000ff;">if</span> (<span style="color:#0000ff;">this</span>.genericListener <span style="color:#0000ff;">instanceof</span><span style="color:#000000;"> ConsumerSeekAware) {
</span><span style="color:#008080;"> 4</span>                 ((ConsumerSeekAware) <span style="color:#0000ff;">this</span>.genericListener).registerSeekCallback(<span style="color:#0000ff;">this</span><span style="color:#000000;">);
</span><span style="color:#008080;"> 5</span> <span style="color:#000000;">            }
</span><span style="color:#008080;"> 6</span>             <span style="color:#0000ff;">this</span>.count = 0<span style="color:#000000;">;
</span><span style="color:#008080;"> 7</span>             <span style="color:#0000ff;">this</span>.last =<span style="color:#000000;"> System.currentTimeMillis();
</span><span style="color:#008080;"> 8</span>             <span style="color:#0000ff;">if</span> (isRunning() &amp;&amp; <span style="color:#0000ff;">this</span>.definedPartitions != <span style="color:#0000ff;">null</span><span style="color:#000000;">) {<span style="color:#008000;">//<span style="color:#ff0000;"><strong> 1.如果运行中且自定义了分区,没必要再平衡分配分区了，直接回调</strong></span></span>
</span><span style="color:#008080;"> 9</span> <span style="color:#000000;">                initPartitionsIfNeeded();<span style="color:#000000;"><span style="color:#008000;">//<span style="color:#008000;"> 有需要就初始化分区</span></span></span>
</span><span style="color:#008080;">10</span>                 <span style="color:#008000;">//</span><span style="color:#ff0000;"><strong> 回调</strong></span>
<span style="color:#008080;">13</span>                 <span style="color:#0000ff;">if</span> (!<span style="color:#0000ff;">this</span><span style="color:#000000;">.autoCommit) {
</span><span style="color:#008080;">14</span> <span style="color:#000000;"><span style="color:#ff0000;">                    startInvoker</span>();
</span><span style="color:#008080;">15</span> <span style="color:#000000;">                }
</span><span style="color:#008080;">16</span> <span style="color:#000000;">            }
</span><span style="color:#008080;">17</span>             <span style="color:#0000ff;">long</span> lastReceive =<span style="color:#000000;"> System.currentTimeMillis();
</span><span style="color:#008080;">18</span>             <span style="color:#0000ff;">long</span> lastAlertAt =<span style="color:#000000;"> lastReceive;
</span><span style="color:#008080;">19</span>             <span style="color:#0000ff;">while</span><span style="color:#000000;"> (isRunning()) {<span style="color:#ff0000;"><strong>//2.未指定分区，进入自旋消费
</strong></span></span><span style="color:#008080;">20</span>                 <span style="color:#0000ff;">try</span><span style="color:#000000;"> {
</span><span style="color:#008080;">21</span>                     <span style="color:#0000ff;">if</span> (!<span style="color:#0000ff;">this</span><span style="color:#000000;">.autoCommit) {
</span><span style="color:#008080;">22</span> <span style="color:#000000;">                        processCommits();<span style="color:#008000;">//<span style="color:#008000;"> 如果手动提交，处理提交</span></span>
</span><span style="color:#008080;">23</span> <span style="color:#000000;">                    }
</span><span style="color:#008080;">24</span> <span style="color:#000000;">                    processSeeks();<span style="color:#000000;"><span style="color:#008000;">//<span style="color:#008000;"> 重新定位偏移量，下一次消费时使用</span></span></span>
</span><span style="color:#008080;">25</span>                     <span style="color:#0000ff;">if</span> (<span style="color:#0000ff;">this</span><span style="color:#000000;">.logger.isTraceEnabled()) {
</span><span style="color:#008080;">26</span>                         <span style="color:#0000ff;">this</span>.logger.trace("Polling (paused=" + <span style="color:#0000ff;">this</span>.paused + ")..."<span style="color:#000000;">);
</span><span style="color:#008080;">27</span> <span style="color:#000000;">                    }<span style="color:#000000;"><span style="color:#000000;"><span style="color:#008000;">//<span style="color:#008000;"> 1）拉取消费记录</span></span></span></span>
</span><span style="color:#008080;">28</span>                     <span style="color:#ff0000;">ConsumerRecords&lt;K, V&gt; records = this.consumer.poll(this.containerProperties.getPollTimeout());
</span><span style="color:#008080;">29</span>                     <span style="color:#0000ff;">if</span> (records != <span style="color:#0000ff;">null</span> &amp;&amp; <span style="color:#0000ff;">this</span><span style="color:#000000;">.logger.isDebugEnabled()) {
</span><span style="color:#008080;">30</span>                         <span style="color:#0000ff;">this</span>.logger.debug("Received: " + records.count() + " records"<span style="color:#000000;">);
</span><span style="color:#008080;">31</span> <span style="color:#000000;">                    }
</span><span style="color:#008080;">32</span>                     <span style="color:#0000ff;">if</span> (records != <span style="color:#0000ff;">null</span> &amp;&amp; records.count() &gt; 0<span style="color:#000000;">) {
</span><span style="color:#008080;">33</span>                         <span style="color:#0000ff;">if</span> (<span style="color:#0000ff;">this</span>.containerProperties.getIdleEventInterval() != <span style="color:#0000ff;">null</span><span style="color:#000000;">) {
</span><span style="color:#008080;">34</span>                             lastReceive =<span style="color:#000000;"> System.currentTimeMillis();
</span><span style="color:#008080;">35</span> <span style="color:#000000;">                        }</span><span style="color:#008000;">//</span><span style="color:#008000;"> 2）如果设置了自动提交，直接在当前线程执行</span>
<span style="color:#008080;">39</span>                         <span style="color:#0000ff;">if</span> (<span style="color:#0000ff;">this</span><span style="color:#000000;">.autoCommit) {
</span><span style="color:#008080;">40</span> <span style="color:#000000;"><span style="color:#ff0000;">                            invokeListener</span>(records);
</span><span style="color:#008080;">41</span> <span style="color:#000000;">                        }
</span><span style="color:#008080;">42</span>                         <span style="color:#0000ff;">else</span><span style="color:#000000;"> {<span style="color:#008000;">//<span style="color:#008000;"> 3）否则发送消息进缓存队列</span></span>
</span><span style="color:#008080;">43</span>                             <span style="color:#0000ff;">if</span><span style="color:#000000;"> (<span style="color:#ff0000;">sendToListener</span>(records)) {
</span><span style="color:#008080;">44</span>                                 <span style="color:#0000ff;">if</span> (<span style="color:#0000ff;">this</span>.assignedPartitions != <span style="color:#0000ff;">null</span><span style="color:#000000;">) {
</span><span style="color:#008080;">45</span>                                     <span style="color:#008000;">//</span><span style="color:#008000;"> avoid group management rebalance due to a slow
</span><span style="color:#008080;">46</span>                                     <span style="color:#008000;">//</span><span style="color:#008000;"> consumer</span>
<span style="color:#008080;">47</span>                                     <span style="color:#0000ff;">this</span>.consumer.pause(<span style="color:#0000ff;">this</span><span style="color:#000000;">.assignedPartitions);
</span><span style="color:#008080;">48</span>                                     <span style="color:#0000ff;">this</span>.paused = <span style="color:#0000ff;">true</span><span style="color:#000000;">;
</span><span style="color:#008080;">49</span>                                     <span style="color:#0000ff;">this</span>.unsent =<span style="color:#000000;"> records;
</span><span style="color:#008080;">50</span> <span style="color:#000000;">                                }
</span><span style="color:#008080;">51</span> <span style="color:#000000;">                            }
</span><span style="color:#008080;">52</span> <span style="color:#000000;">                        }
</span><span style="color:#008080;">53</span> <span style="color:#000000;">                    }
</span><span style="color:#008080;">54</span>                     <span style="color:#0000ff;">else</span><span style="color:#000000;"> {
</span><span style="color:#008080;">55</span>                         <span style="color:#0000ff;">if</span> (<span style="color:#0000ff;">this</span>.containerProperties.getIdleEventInterval() != <span style="color:#0000ff;">null</span><span style="color:#000000;">) {
</span><span style="color:#008080;">56</span>                             <span style="color:#0000ff;">long</span> now =<span style="color:#000000;"> System.currentTimeMillis();
</span><span style="color:#008080;">57</span>                             <span style="color:#0000ff;">if</span> (now &gt; lastReceive + <span style="color:#0000ff;">this</span><span style="color:#000000;">.containerProperties.getIdleEventInterval()
</span><span style="color:#008080;">58</span>                                     &amp;&amp; now &gt; lastAlertAt + <span style="color:#0000ff;">this</span><span style="color:#000000;">.containerProperties.getIdleEventInterval()) {
</span><span style="color:#008080;">59</span>                                 publishIdleContainerEvent(now -<span style="color:#000000;"> lastReceive);
</span><span style="color:#008080;">60</span>                                 lastAlertAt =<span style="color:#000000;"> now;
</span><span style="color:#008080;">61</span>                                 <span style="color:#0000ff;">if</span> (<span style="color:#0000ff;">this</span>.genericListener <span style="color:#0000ff;">instanceof</span><span style="color:#000000;"> ConsumerSeekAware) {
</span><span style="color:#008080;">62</span>                                     seekPartitions(getAssignedPartitions(), <span style="color:#0000ff;">true</span><span style="color:#000000;">);
</span><span style="color:#008080;">63</span> <span style="color:#000000;">                                }
</span><span style="color:#008080;">64</span> <span style="color:#000000;">                            }
</span><span style="color:#008080;">65</span> <span style="color:#000000;">                        }
</span><span style="color:#008080;">66</span> <span style="color:#000000;">                    }
</span><span style="color:#008080;">67</span>                     <span style="color:#0000ff;">this</span>.unsent = checkPause(<span style="color:#0000ff;">this</span><span style="color:#000000;">.unsent);
</span><span style="color:#008080;">68</span> <span style="color:#000000;">                }
</span><span style="color:#008080;">69</span>                 <span style="color:#0000ff;">catch</span><span style="color:#000000;"> (WakeupException e) {
</span><span style="color:#008080;">70</span>                     <span style="color:#0000ff;">this</span>.unsent = checkPause(<span style="color:#0000ff;">this</span><span style="color:#000000;">.unsent);
</span><span style="color:#008080;">71</span> <span style="color:#000000;">                }
</span><span style="color:#008080;">72</span>                 <span style="color:#0000ff;">catch</span><span style="color:#000000;"> (Exception e) {
</span><span style="color:#008080;">73</span>                     <span style="color:#0000ff;">if</span> (<span style="color:#0000ff;">this</span>.containerProperties.getGenericErrorHandler() != <span style="color:#0000ff;">null</span><span style="color:#000000;">) {
</span><span style="color:#008080;">74</span>                         <span style="color:#0000ff;">this</span>.containerProperties.getGenericErrorHandler().handle(e, <span style="color:#0000ff;">null</span><span style="color:#000000;">);
</span><span style="color:#008080;">75</span> <span style="color:#000000;">                    }
</span><span style="color:#008080;">76</span>                     <span style="color:#0000ff;">else</span><span style="color:#000000;"> {
</span><span style="color:#008080;">77</span>                         <span style="color:#0000ff;">this</span>.logger.error("Container exception"<span style="color:#000000;">, e);
</span><span style="color:#008080;">78</span> <span style="color:#000000;">                    }
</span><span style="color:#008080;">79</span> <span style="color:#000000;">                }
</span><span style="color:#008080;">80</span> <span style="color:#000000;">            }
</span><span style="color:#008080;">81</span>             <span style="color:#0000ff;">if</span> (<span style="color:#0000ff;">this</span>.listenerInvokerFuture != <span style="color:#0000ff;">null</span><span style="color:#000000;">) {
</span><span style="color:#008080;">82</span> <span style="color:#000000;">                stopInvoker();
</span><span style="color:#008080;">83</span> <span style="color:#000000;">                commitManualAcks();
</span><span style="color:#008080;">84</span> <span style="color:#000000;">            }
</span><span style="color:#008080;">85</span>             <span style="color:#0000ff;">try</span><span style="color:#000000;"> {
</span><span style="color:#008080;">86</span>                 <span style="color:#0000ff;">this</span><span style="color:#000000;">.consumer.unsubscribe();
</span><span style="color:#008080;">87</span> <span style="color:#000000;">            }
</span><span style="color:#008080;">88</span>             <span style="color:#0000ff;">catch</span><span style="color:#000000;"> (WakeupException e) {
</span><span style="color:#008080;">89</span>                 <span style="color:#008000;">//</span><span style="color:#008000;"> No-op. Continue process</span>
<span style="color:#008080;">90</span> <span style="color:#000000;">            }
</span><span style="color:#008080;">91</span>             <span style="color:#0000ff;">this</span><span style="color:#000000;">.consumer.close();
</span><span style="color:#008080;">92</span>             <span style="color:#0000ff;">if</span> (<span style="color:#0000ff;">this</span><span style="color:#000000;">.logger.isInfoEnabled()) {
</span><span style="color:#008080;">93</span>                 <span style="color:#0000ff;">this</span>.logger.info("Consumer stopped"<span style="color:#000000;">);
</span><span style="color:#008080;">94</span> <span style="color:#000000;">            }
</span><span style="color:#008080;">95</span>         }</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p><span style="color:#ff0000;">1.如果用户自定义了分区且非自动提交</span>，那么开启异步线程执行<span class="header">ListenerInvoker任务，源码如下：</span></p>
<div class="cnblogs_code">
<pre><span style="color:#008080;">1</span> <span style="color:#0000ff;">private</span> <span style="color:#0000ff;">void</span><span style="color:#000000;"><span style="color:#ff0000;"> startInvoker</span>() {
</span><span style="color:#008080;">2</span>             ListenerConsumer.<span style="color:#0000ff;">this</span>.invoker = <span style="color:#0000ff;">new</span><span style="color:#000000;"> ListenerInvoker();
</span><span style="color:#008080;">3</span>             ListenerConsumer.<span style="color:#0000ff;">this</span>.listenerInvokerFuture = <span style="color:#0000ff;">this</span><span style="color:#000000;">.containerProperties.getListenerTaskExecutor()
</span><span style="color:#008080;">4</span>                     .<span style="color:#ff0000;">submit</span>(ListenerConsumer.<span style="color:#0000ff;">this</span><span style="color:#000000;">.<span style="color:#ff0000;">invoker</span>);
</span><span style="color:#008080;">5</span>         }</pre>
</div>
<p>执行<span class="header">ListenerInvoker</span>的run方法，实际上就执行一遍，因为CountDownLatch初始化为1</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color:#008080;"> 1</span> <span style="color:#0000ff;">private</span> <span style="color:#0000ff;">final</span> <span style="color:#0000ff;">class</span> <span style="color:#ff0000;">ListenerInvoker</span> <span style="color:#0000ff;">implements</span><span style="color:#000000;"> SchedulingAwareRunnable {
</span><span style="color:#008080;"> 2</span> 
<span style="color:#008080;"> 3</span>             <span style="color:#0000ff;">private</span> <span style="color:#0000ff;">final</span> CountDownLatch exitLatch = <span style="color:#0000ff;">new</span> <span style="color:#ff0000;">CountDownLatch</span>(1<span style="color:#000000;">);
</span><span style="color:#008080;"> 4</span> 
<span style="color:#008080;"> 5</span>             <span style="color:#0000ff;">private</span> <span style="color:#0000ff;">volatile</span> <span style="color:#0000ff;">boolean</span> active = <span style="color:#0000ff;">true</span><span style="color:#000000;">;
</span><span style="color:#008080;"> 6</span> 
<span style="color:#008080;"> 7</span>             <span style="color:#0000ff;">private</span> <span style="color:#0000ff;">volatile</span><span style="color:#000000;"> Thread executingThread;
</span><span style="color:#008080;"> 8</span> 
<span style="color:#008080;"> 9</span> <span style="color:#000000;">            ListenerInvoker() {
</span><span style="color:#008080;">10</span>                 <span style="color:#0000ff;">super</span><span style="color:#000000;">();
</span><span style="color:#008080;">11</span> <span style="color:#000000;">            }
</span><span style="color:#008080;">12</span> 
<span style="color:#008080;">13</span> <span style="color:#000000;">            @Override
</span><span style="color:#008080;">14</span>             <span style="color:#0000ff;">public</span> <span style="color:#0000ff;">void</span><span style="color:#000000;"><span style="color:#ff0000;"> run</span>() {
</span><span style="color:#008080;">15</span>                 Assert.isTrue(<span style="color:#0000ff;">this</span>.active, "This instance is not active anymore"<span style="color:#000000;">);
</span><span style="color:#008080;">16</span>                 <span style="color:#0000ff;">if</span> (ListenerConsumer.<span style="color:#0000ff;">this</span>.theListener <span style="color:#0000ff;">instanceof</span><span style="color:#000000;"> ConsumerSeekAware) {
</span><span style="color:#008080;">17</span>                     ((ConsumerSeekAware) ListenerConsumer.<span style="color:#0000ff;">this</span>.theListener).registerSeekCallback(ListenerConsumer.<span style="color:#0000ff;">this</span><span style="color:#000000;">);
</span><span style="color:#008080;">18</span> <span style="color:#000000;">                }
</span><span style="color:#008080;">19</span>                 <span style="color:#0000ff;">try</span><span style="color:#000000;"> {
</span><span style="color:#008080;">20</span>                     <span style="color:#0000ff;">this</span>.executingThread =<span style="color:#000000;"> Thread.currentThread();
</span><span style="color:#008080;">21</span>                     <span style="color:#0000ff;">while</span> (<span style="color:#0000ff;">this</span><span style="color:#000000;">.active) {
</span><span style="color:#008080;">22</span>                         <span style="color:#0000ff;">try</span><span style="color:#000000;"> {<span style="color:#008000;">//<span style="color:#008000;"> 从阻塞队列LinkedBlockingQueue recordsToProcess中拉取 待消费记录</span></span>
</span><span style="color:#008080;">23</span>                             ConsumerRecords&lt;K, V&gt; <span style="color:#ff0000;">records</span> = ListenerConsumer.<span style="color:#0000ff;">this</span>.recordsToProcess.poll(1<span style="color:#000000;">,
</span><span style="color:#008080;">24</span> <span style="color:#000000;">                                    TimeUnit.SECONDS);
</span><span style="color:#008080;">25</span>                             <span style="color:#0000ff;">if</span> (<span style="color:#0000ff;">this</span><span style="color:#000000;">.active) {
</span><span style="color:#008080;">26</span>                                 <span style="color:#0000ff;">if</span> (records != <span style="color:#0000ff;">null</span><span style="color:#000000;">) {
</span><span style="color:#008080;">27</span> <span style="color:#000000;"><span style="color:#ff0000;">                                    invokeListener</span>(records);<span style="color:#000000;"><span style="color:#008000;">//<span style="color:#008000;"> 消费</span></span></span>
</span><span style="color:#008080;">28</span> <span style="color:#000000;">                                }
</span><span style="color:#008080;">29</span>                                 <span style="color:#0000ff;">else</span><span style="color:#000000;"> {
</span><span style="color:#008080;">30</span>                                     <span style="color:#0000ff;">if</span> (ListenerConsumer.<span style="color:#0000ff;">this</span><span style="color:#000000;">.logger.isTraceEnabled()) {
</span><span style="color:#008080;">31</span>                                         ListenerConsumer.<span style="color:#0000ff;">this</span>.logger.trace("No records to process"<span style="color:#000000;">);
</span><span style="color:#008080;">32</span> <span style="color:#000000;">                                    }
</span><span style="color:#008080;">33</span> <span style="color:#000000;">                                }
</span><span style="color:#008080;">34</span> <span style="color:#000000;">                            }
</span><span style="color:#008080;">35</span> <span style="color:#000000;">                        }
</span><span style="color:#008080;">36</span>                         <span style="color:#0000ff;">catch</span><span style="color:#000000;"> (InterruptedException e) {
</span><span style="color:#008080;">37</span>                             <span style="color:#0000ff;">if</span> (!<span style="color:#0000ff;">this</span><span style="color:#000000;">.active) {
</span><span style="color:#008080;">38</span> <span style="color:#000000;">                                Thread.currentThread().interrupt();
</span><span style="color:#008080;">39</span> <span style="color:#000000;">                            }
</span><span style="color:#008080;">40</span>                             <span style="color:#0000ff;">else</span><span style="color:#000000;"> {
</span><span style="color:#008080;">41</span>                                 ListenerConsumer.<span style="color:#0000ff;">this</span>.logger.debug("Interrupt ignored"<span style="color:#000000;">);
</span><span style="color:#008080;">42</span> <span style="color:#000000;">                            }
</span><span style="color:#008080;">43</span> <span style="color:#000000;">                        }
</span><span style="color:#008080;">44</span> <span style="color:#000000;">                    }
</span><span style="color:#008080;">45</span> <span style="color:#000000;">                }
</span><span style="color:#008080;">46</span>                 <span style="color:#0000ff;">finally</span><span style="color:#000000;"> {
</span><span style="color:#008080;">47</span>                     <span style="color:#0000ff;">this</span>.active = <span style="color:#0000ff;">false</span><span style="color:#000000;">;
</span><span style="color:#008080;">48</span>                     <span style="color:#0000ff;">this</span><span style="color:#000000;">.exitLatch.countDown();
</span><span style="color:#008080;">49</span> <span style="color:#000000;">                }
</span><span style="color:#008080;">50</span> <span style="color:#000000;">            }
</span><span style="color:#008080;">51</span> 
<span style="color:#008080;">52</span> <span style="color:#000000;">            @Override
</span><span style="color:#008080;">53</span>             <span style="color:#0000ff;">public</span> <span style="color:#0000ff;">boolean</span><span style="color:#000000;"> isLongLived() {
</span><span style="color:#008080;">54</span>                 <span style="color:#0000ff;">return</span> <span style="color:#0000ff;">true</span><span style="color:#000000;">;
</span><span style="color:#008080;">55</span> <span style="color:#000000;">            }
</span><span style="color:#008080;">5</span><span style="color:#008080;">81</span>         }</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color:#008080;">1</span> <span style="color:#0000ff;">private</span> <span style="color:#0000ff;">void</span> <span style="color:#ff0000;">invokeListener</span>(<span style="color:#0000ff;">final</span> ConsumerRecords&lt;K, V&gt;<span style="color:#000000;"> records) {
</span><span style="color:#008080;">2</span>             <span style="color:#0000ff;">if</span> (<span style="color:#0000ff;">this</span><span style="color:#000000;">.isBatchListener) {
</span><span style="color:#008080;">3</span> <span style="color:#000000;">                invokeBatchListener(records);
</span><span style="color:#008080;">4</span> <span style="color:#000000;">            }
</span><span style="color:#008080;">5</span>             <span style="color:#0000ff;">else</span><span style="color:#000000;"> {
</span><span style="color:#008080;">6</span> <span style="color:#000000;"><span style="color:#ff0000;">                invokeRecordListener</span>(records);
</span><span style="color:#008080;">7</span> <span style="color:#000000;">            }
</span><span style="color:#008080;">8</span>         }</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>如上图，从阻塞队列中取得待消费记录，用迭代器iterator消费，根据自定义消费类型，用不同listener来执行onMessage方法（<strong><span style="color:#ff0000;">用户自定义<span class="header">MessageListener接口的onMessage方法，实现用户自己的消费业务逻辑</span></span></strong>）</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color:#008080;"> 1</span> <span style="color:#0000ff;">private</span> <span style="color:#0000ff;">void</span> <span style="color:#ff0000;">invokeRecordListener</span>(<span style="color:#0000ff;">final</span> ConsumerRecords&lt;K, V&gt;<span style="color:#000000;"> records) {
</span><span style="color:#008080;"> 2</span>             Iterator&lt;ConsumerRecord&lt;K, V&gt;&gt; iterator =<span style="color:#000000;"> records.iterator();
</span><span style="color:#008080;"> 3</span>             <span style="color:#0000ff;">while</span> (iterator.hasNext() &amp;&amp; (<span style="color:#0000ff;">this</span>.autoCommit || (<span style="color:#0000ff;">this</span>.invoker != <span style="color:#0000ff;">null</span> &amp;&amp; <span style="color:#0000ff;">this</span><span style="color:#000000;">.invoker.active))) {
</span><span style="color:#008080;"> 4</span>                 <span style="color:#0000ff;">final</span> ConsumerRecord&lt;K, V&gt; record =<span style="color:#000000;"> iterator.next();
</span><span style="color:#008080;"> 5</span>                 <span style="color:#0000ff;">if</span> (<span style="color:#0000ff;">this</span><span style="color:#000000;">.logger.isTraceEnabled()) {
</span><span style="color:#008080;"> 6</span>                     <span style="color:#0000ff;">this</span>.logger.trace("Processing " +<span style="color:#000000;"> record);
</span><span style="color:#008080;"> 7</span> <span style="color:#000000;">                }
</span><span style="color:#008080;"> 8</span>                 <span style="color:#0000ff;">try</span><span style="color:#000000;"> {
</span><span style="color:#008080;"> 9</span>                     <span style="color:#0000ff;">if</span> (<span style="color:#0000ff;">this</span>.acknowledgingMessageListener != <span style="color:#0000ff;">null</span><span style="color:#000000;">) {
</span><span style="color:#008080;">10</span>                         <span style="color:#0000ff;">this</span><span style="color:#000000;">.acknowledgingMessageListener.<span style="color:#ff0000;">onMessage</span>(record,<span style="color:#000000;"><span style="color:#000000;"><span style="color:#008000;">//<span style="color:#ff0000;"><strong> 终极核心方法，用户自定义的<span class="header">MessageListener接口的onMessage方法</span></strong></span></span></span></span>
</span><span style="color:#008080;">11</span>                                 <span style="color:#0000ff;">this</span><span style="color:#000000;">.isAnyManualAck
</span><span style="color:#008080;">12</span>                                         ? <span style="color:#0000ff;">new</span> ConsumerAcknowledgment(record, <span style="color:#0000ff;">this</span><span style="color:#000000;">.isManualImmediateAck)
</span><span style="color:#008080;">13</span>                                         : <span style="color:#0000ff;">null</span><span style="color:#000000;">);
</span><span style="color:#008080;">14</span> <span style="color:#000000;">                    }
</span><span style="color:#008080;">15</span>                     <span style="color:#0000ff;">else</span><span style="color:#000000;"> {
</span><span style="color:#008080;">16</span>                         <span style="color:#0000ff;">this</span><span style="color:#000000;">.listener.<span style="color:#ff0000;">onMessage</span>(record);<span style="color:#000000;"><span style="color:#008000;">//<span style="color:#ff0000;"><strong> 终极核心方法，用户自定义的<span class="header">MessageListener</span>接口的onMessage方法</strong></span></span></span>
</span><span style="color:#008080;">17</span> <span style="color:#000000;">                    }
</span><span style="color:#008080;">18</span>                     <span style="color:#0000ff;">if</span> (!<span style="color:#0000ff;">this</span>.isAnyManualAck &amp;&amp; !<span style="color:#0000ff;">this</span><span style="color:#000000;">.autoCommit) {
</span><span style="color:#008080;">19</span>                         <span style="color:#0000ff;">this</span><span style="color:#000000;">.acks.add(record);
</span><span style="color:#008080;">20</span> <span style="color:#000000;">                    }
</span><span style="color:#008080;">21</span> <span style="color:#000000;">                }
</span><span style="color:#008080;">22</span>                 <span style="color:#0000ff;">catch</span><span style="color:#000000;"> (Exception e) {
</span><span style="color:#008080;">23</span>                     <span style="color:#0000ff;">if</span> (<span style="color:#0000ff;">this</span>.containerProperties.isAckOnError() &amp;&amp; !<span style="color:#0000ff;">this</span><span style="color:#000000;">.autoCommit) {
</span><span style="color:#008080;">24</span>                         <span style="color:#0000ff;">this</span><span style="color:#000000;">.acks.add(record);
</span><span style="color:#008080;">25</span> <span style="color:#000000;">                    }
</span><span style="color:#008080;">26</span>                     <span style="color:#0000ff;">try</span><span style="color:#000000;"> {
</span><span style="color:#008080;">27</span>                         <span style="color:#0000ff;">this</span><span style="color:#000000;">.errorHandler.handle(e, record);
</span><span style="color:#008080;">28</span> <span style="color:#000000;">                    }
</span><span style="color:#008080;">29</span>                     <span style="color:#0000ff;">catch</span><span style="color:#000000;"> (Exception ee) {
</span><span style="color:#008080;">30</span>                         <span style="color:#0000ff;">this</span>.logger.error("Error handler threw an exception"<span style="color:#000000;">, ee);
</span><span style="color:#008080;">31</span> <span style="color:#000000;">                    }
</span><span style="color:#008080;">32</span>                     <span style="color:#0000ff;">catch</span> (Error er) { <span style="color:#008000;">//</span><span style="color:#008000;">NOSONAR</span>
<span style="color:#008080;">33</span>                         <span style="color:#0000ff;">this</span>.logger.error("Error handler threw an error"<span style="color:#000000;">, er);
</span><span style="color:#008080;">34</span>                         <span style="color:#0000ff;">throw</span><span style="color:#000000;"> er;
</span><span style="color:#008080;">35</span> <span style="color:#000000;">                    }
</span><span style="color:#008080;">36</span> <span style="color:#000000;">                }
</span><span style="color:#008080;">37</span> <span style="color:#000000;">            }
</span><span style="color:#008080;">38</span>         }</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p><span style="color:#ff0000;"><span style="color:#ff0000;">2.未指定分区，进入自旋<br></span></span></p>
<pre><span style="color:#000000;"><span style="color:#000000;"><span style="color:#000000;"><span style="color:#008000;">//<span style="color:#008000;"> 1）拉取消费记录
<span style="color:#008080;"><span style="color:#ff0000;">ConsumerRecords&lt;K, V&gt; records = this.consumer.poll(this.containerProperties.getPollTimeout());<br></span></span></span></span></span></span></span></pre>
<pre><span style="color:#008000;"> 2）如果设置了自动提交，直接在当前线程执行</span><span style="color:#000000;"><span style="color:#000000;"><span style="color:#000000;"><span style="color:#008000;"><span style="color:#008000;"><span style="color:#008080;"><span style="color:#ff0000;"><br></span></span></span></span></span></span><span style="color:#ff0000;">invokeListener(records);<br></span></span></pre>
<pre><span style="color:#000000;"><span style="color:#008000;">//<span style="color:#008000;"> 3）否则发送消息进缓存队列
<span style="color:#008080;"><span style="color:#0000ff;"><span style="color:#000000;"><span style="color:#ff0000;">sendToListener(records)</span></span></span></span></span></span></span></pre>
<p>1）在每个轮询中，消费者将尝试使用最后一个被使用的偏移量作为起始偏移量，并按顺序提取。最后一个被消费的偏移量可以通过 seek(TopicPartition,long)或自动设置为最后一个被订阅的分区列表的偏移量获得。</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color:#008080;"> 1</span> <span style="color:#000000;">@Override
</span><span style="color:#008080;"> 2</span>     <span style="color:#0000ff;">public</span> ConsumerRecords&lt;K, V&gt; <span style="color:#ff0000;">poll</span>(<span style="color:#0000ff;">long</span><span style="color:#000000;"> timeout) {
</span><span style="color:#008080;"> 3</span> <span style="color:#000000;">        acquire();
</span><span style="color:#008080;"> 4</span>         <span style="color:#0000ff;">try</span><span style="color:#000000;"> {
</span><span style="color:#008080;"> 5</span>             <span style="color:#0000ff;">if</span> (timeout &lt; 0<span style="color:#000000;">)
</span><span style="color:#008080;"> 6</span>                 <span style="color:#0000ff;">throw</span> <span style="color:#0000ff;">new</span> IllegalArgumentException("Timeout must not be negative"<span style="color:#000000;">);
</span><span style="color:#008080;"> 7</span> 
<span style="color:#008080;"> 8</span>             <span style="color:#0000ff;">if</span> (<span style="color:#0000ff;">this</span><span style="color:#000000;">.subscriptions.hasNoSubscriptionOrUserAssignment())
</span><span style="color:#008080;"> 9</span>                 <span style="color:#0000ff;">throw</span> <span style="color:#0000ff;">new</span> IllegalStateException("Consumer is not subscribed to any topics or assigned any partitions"<span style="color:#000000;">);
</span><span style="color:#008080;">10</span> 
<span style="color:#008080;">11</span>             <span style="color:#008000;">//</span><span style="color:#008000;"> poll for new data until the timeout expires</span>
<span style="color:#008080;">12</span>             <span style="color:#0000ff;">long</span> start =<span style="color:#000000;"> time.milliseconds();
</span><span style="color:#008080;">13</span>             <span style="color:#0000ff;">long</span> remaining =<span style="color:#000000;"> timeout;
</span><span style="color:#008080;">14</span>             <span style="color:#0000ff;">do</span><span style="color:#000000;"> {
</span><span style="color:#008080;">15</span>                 Map&lt;TopicPartition, List&lt;ConsumerRecord&lt;K, V&gt;&gt;&gt; records =<span style="color:#000000;"><span style="color:#ff0000;"> pollOnce</span>(remaining);
</span><span style="color:#008080;">16</span>                 <span style="color:#0000ff;">if</span> (!<span style="color:#000000;">records.isEmpty()) {</span>
<span style="color:#008080;">23</span> <span style="color:#000000;">                    fetcher.<span style="color:#ff0000;">sendFetches</span>();<span style="color:#008000;">//<span style="color:#008000;"> 在返回所获取的记录之前，我们可以发送下一轮的fetches并避免阻塞等待它们的响应，以便在用户处理获取的记录时进行流水线操作。</span></span>
</span><span style="color:#008080;">24</span> <span style="color:#000000;">                    client.<span style="color:#ff0000;">pollNoWakeup</span>();<span style="color:#008000;">//由于已经更新了所使用的位置，所以我们不允许在返回所获取的记录之前触发wakeups或任何其他错误。</span>
</span><span style="color:#008080;">25</span> 
<span style="color:#008080;">26</span>                     <span style="color:#0000ff;">if</span> (<span style="color:#0000ff;">this</span>.interceptors == <span style="color:#0000ff;">null</span><span style="color:#000000;">)
</span><span style="color:#008080;">27</span>                         <span style="color:#0000ff;">return</span> <span style="color:#0000ff;">new</span> ConsumerRecords&lt;&gt;<span style="color:#000000;">(<span style="color:#ff0000;">records</span>);
</span><span style="color:#008080;">28</span>                     <span style="color:#0000ff;">else<span style="color:#000000;"><span style="color:#008000;">//<span style="color:#008000;"> 如果存在消费者拦截器执行拦截</span></span></span></span>
<span style="color:#008080;">29</span>                         <span style="color:#0000ff;">return</span> <span style="color:#0000ff;">this</span>.interceptors.onConsume(<span style="color:#0000ff;">new</span> ConsumerRecords&lt;&gt;<span style="color:#000000;">(records));
</span><span style="color:#008080;">30</span> <span style="color:#000000;">                }
</span><span style="color:#008080;">31</span> 
<span style="color:#008080;">32</span>                 <span style="color:#0000ff;">long</span> elapsed = time.milliseconds() -<span style="color:#000000;"> start;
</span><span style="color:#008080;">33</span>                 remaining = timeout -<span style="color:#000000;"> elapsed;
</span><span style="color:#008080;">34</span>             } <span style="color:#0000ff;">while</span> (remaining &gt; 0<span style="color:#000000;">);
</span><span style="color:#008080;">35</span> 
<span style="color:#008080;">36</span>             <span style="color:#0000ff;">return</span><span style="color:#000000;"> ConsumerRecords.empty();
</span><span style="color:#008080;">37</span>         } <span style="color:#0000ff;">finally</span><span style="color:#000000;"> {
</span><span style="color:#008080;">38</span> <span style="color:#000000;">            release();
</span><span style="color:#008080;">39</span> <span style="color:#000000;">        }
</span><span style="color:#008080;">40</span>     }</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p>pollOnce：</p>
<div class="cnblogs_code"><div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color:#008080;"> 1</span> <span style="color:#0000ff;">private</span> Map&lt;TopicPartition, List&lt;ConsumerRecord&lt;K, V&gt;&gt;&gt; <span style="color:#ff0000;">pollOnce</span>(<span style="color:#0000ff;">long</span><span style="color:#000000;"> timeout) {
</span><span style="color:#008080;"> 2</span> <span style="color:#000000;">        coordinator.poll(time.milliseconds());
</span><span style="color:#008080;"> 3</span> 
<span style="color:#008080;"> 4</span>         <span style="color:#008000;">//</span><span style="color:#008000;"> <code class="java comments">遍历所有的TopicPartition</code>，如果有未知偏移量（分区的），那么更新。涉及<span style="color:#000000;">coordinator</span>刷新已提交分区偏移量</span>+<span style="color:#000000;">fetcher</span>更新获取位置
<span style="color:#008080;"> 6</span>         <span style="color:#0000ff;">if</span> (!<span style="color:#000000;">subscriptions.hasAllFetchPositions())
</span><span style="color:#008080;"> 7</span>             <span style="color:#ff0000;">updateFetchPositions</span>(<span style="color:#0000ff;">this</span><span style="color:#000000;">.subscriptions.missingFetchPositions());
</span><span style="color:#008080;"> 8</span> 
<span style="color:#008080;"> 9</span>         <span style="color:#008000;">//</span><span style="color:#008000;"> 返回已获取到的记录</span>
<span style="color:#008080;">10</span>         Map&lt;TopicPartition, List&lt;ConsumerRecord&lt;K, V&gt;&gt;&gt; records =<span style="color:#000000;"> fetcher.<span style="color:#ff0000;">fetchedRecords</span>();
</span><span style="color:#008080;">11</span>         <span style="color:#0000ff;">if</span> (!<span style="color:#000000;">records.isEmpty())
</span><span style="color:#008080;">12</span>             <span style="color:#0000ff;">return</span><span style="color:#000000;"><span style="color:#ff0000;"> records</span>;
</span><span style="color:#008080;">13</span> 
<span style="color:#008080;">14</span>         <span style="color:#008000;">//</span><span style="color:#008000;"> 发送fetch请求</span>
<span style="color:#008080;">15</span> <span style="color:#000000;">        fetcher.sendFetches();
</span><span style="color:#008080;">16</span> 
<span style="color:#008080;">17</span>         <span style="color:#0000ff;">long</span> now =<span style="color:#000000;"> time.milliseconds();
</span><span style="color:#008080;">18</span>         <span style="color:#0000ff;">long</span> pollTimeout =<span style="color:#000000;"> Math.min(coordinator.timeToNextPoll(now), timeout);
</span><span style="color:#008080;">19</span>         <span style="color:#008000;">//<span style="color:#008000;"> 执行IO，拉取数据</span></span>
<span style="color:#008080;">20</span>         client.<span style="color:#ff0000;">poll</span>(pollTimeout, now, <span style="color:#0000ff;">new</span><span style="color:#000000;"> PollCondition() {
</span><span style="color:#008080;">21</span> <span style="color:#000000;">            @Override
</span><span style="color:#008080;">22</span>             <span style="color:#0000ff;">public</span> <span style="color:#0000ff;">boolean</span><span style="color:#000000;"> shouldBlock() {
</span><span style="color:#008080;">23</span>                 <span style="color:#008000;">//</span><span style="color:#008000;"> since a fetch might be completed by the background thread, we need this poll condition
</span><span style="color:#008080;">24</span>                 <span style="color:#008000;">//</span><span style="color:#008000;"> to ensure that we do not block unnecessarily in poll()</span>
<span style="color:#008080;">25</span>                 <span style="color:#0000ff;">return</span> !<span style="color:#000000;">fetcher.hasCompletedFetches();
</span><span style="color:#008080;">26</span> <span style="color:#000000;">            }
</span><span style="color:#008080;">27</span> <span style="color:#000000;">        });</span>
<span style="color:#008080;">31</span>         <span style="color:#0000ff;">if</span><span style="color:#000000;"> (coordinator.needRejoin())
</span><span style="color:#008080;">32</span>             <span style="color:#0000ff;">return</span><span style="color:#000000;"> Collections.emptyMap();
</span><span style="color:#008080;">33</span> 
<span style="color:#008080;">34</span>         <span style="color:#0000ff;">return</span><span style="color:#000000;"> fetcher.fetchedRecords();
</span><span style="color:#008080;">35</span>     }</pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="//common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div></div>
<p> 好吧，再往下涉及到通信IO层了，这里不再多说。将来补全了kafka通信协议相关文章后再加上飞机票。</p>
<p>2）<span style="color:#000000;"><span style="color:#ff0000;">invokeListener和分支1一样<span style="color:#000000;"><span style="color:#000000;"><span style="color:#008000;"><span style="color:#ff0000;"><strong>，</strong><span style="color:#000000;">最终调用的是用户自定义的<span class="header">MessageListener接口的<span style="color:#ff0000;">onMessage</span>方法，不再重复。</span></span></span></span></span></span></span></span></p>
<p><span style="color:#000000;"><span style="color:#ff0000;"><span style="color:#000000;"><span style="color:#000000;"><span style="color:#008000;"><span style="color:#ff0000;"><span style="color:#000000;"><span class="header">3) <span style="color:#000000;"><span style="color:#008000;"><span style="color:#008000;"><span style="color:#008080;"><span style="color:#0000ff;"><span style="color:#000000;"><span style="color:#ff0000;">sendToListener,<span style="color:#000000;">这里塞进缓存队列LinkedBlockingQueue&lt;ConsumerRecords&lt;K, V&gt;&gt; recordsToProcess,</span>塞进队列后，何时再消费？ListenerInvoker的run方法执行了recordsToProcess.poll进行了消费， </span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></p>
<p> </p></div>            </div>
                </div>