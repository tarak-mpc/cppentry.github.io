---
layout:     post
title:      Structured Streaming集成Kafka《官方文档翻译》
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p id="main-toc"><strong>目录</strong></p>

<p id="2.1%20%E9%93%BE%E6%8E%A5-toc" style="margin-left:40px;"><a href="#2.1%20%E9%93%BE%E6%8E%A5" rel="nofollow">1. 链接</a></p>

<p id="2.2%20%E4%BB%8EKafka%E8%AF%BB%E6%95%B0%E6%8D%AE-toc" style="margin-left:40px;"><a href="#2.2%20%E4%BB%8EKafka%E8%AF%BB%E6%95%B0%E6%8D%AE" rel="nofollow">2. 从Kafka读数据</a></p>

<p id="2.2.1%20%E4%BB%8E%E6%B5%81%E6%9F%A5%E8%AF%A2%E5%88%9B%E5%BB%BAKafka%E6%95%B0%E6%8D%AE%E6%BA%90-toc" style="margin-left:80px;"><a href="#2.2.1%20%E4%BB%8E%E6%B5%81%E6%9F%A5%E8%AF%A2%E5%88%9B%E5%BB%BAKafka%E6%95%B0%E6%8D%AE%E6%BA%90" rel="nofollow">2.1 从流查询创建Kafka数据源</a></p>

<p id="2.2.2%20%E4%BB%8E%E6%89%B9%E6%9F%A5%E8%AF%A2Kafka%E6%95%B0%E6%8D%AE%E6%BA%90%EF%BC%88spark.readStream%E5%8F%98%E6%88%90%E4%BA%86spark.read%EF%BC%89-toc" style="margin-left:80px;"><a href="#2.2.2%20%E4%BB%8E%E6%89%B9%E6%9F%A5%E8%AF%A2Kafka%E6%95%B0%E6%8D%AE%E6%BA%90%EF%BC%88spark.readStream%E5%8F%98%E6%88%90%E4%BA%86spark.read%EF%BC%89" rel="nofollow">2.2 从批查询Kafka数据源（spark.readStream变成了spark.read）</a></p>

<p id="%C2%A02.3%20%E5%90%91Kafka%E5%86%99%E6%95%B0%E6%8D%AE-toc" style="margin-left:40px;"><a href="#%C2%A02.3%20%E5%90%91Kafka%E5%86%99%E6%95%B0%E6%8D%AE" rel="nofollow">3. 向Kafka写数据</a></p>

<p id="%C2%A02.3.1%20%E5%88%9B%E5%BB%BA%E6%B5%81%E6%9F%A5%E8%AF%A2Kafka%20Sink-toc" style="margin-left:80px;"><a href="#%C2%A02.3.1%20%E5%88%9B%E5%BB%BA%E6%B5%81%E6%9F%A5%E8%AF%A2Kafka%20Sink" rel="nofollow">3.1 创建流查询Kafka Sink</a></p>

<p id="2.3.2%20%E5%88%9B%E5%BB%BA%E6%89%B9%E6%9F%A5%E8%AF%A2Kafka%20Sink-toc" style="margin-left:80px;"><a href="#2.3.2%20%E5%88%9B%E5%BB%BA%E6%89%B9%E6%9F%A5%E8%AF%A2Kafka%20Sink" rel="nofollow">3.2 创建批查询Kafka Sink</a></p>

<p id="2.4%20Kafka%20%E7%89%B9%E6%9C%89%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE-toc" style="margin-left:40px;"><a href="#2.4%20Kafka%20%E7%89%B9%E6%9C%89%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE" rel="nofollow">4 Kafka 特有参数配置</a></p>

<hr id="hr-toc"><h2 id="2.1%20%E9%93%BE%E6%8E%A5">1. 链接</h2>

<pre class="has">
<code class="language-java">groupId = org.apache.spark
artifactId = spark-sql-kafka-0-10_2.11
version = 2.4.0</code></pre>

<h2 id="2.2%20%E4%BB%8EKafka%E8%AF%BB%E6%95%B0%E6%8D%AE">2. 从Kafka读数据</h2>

<h3 id="2.2.1%20%E4%BB%8E%E6%B5%81%E6%9F%A5%E8%AF%A2%E5%88%9B%E5%BB%BAKafka%E6%95%B0%E6%8D%AE%E6%BA%90">2.1 从流查询创建Kafka数据源</h3>

<pre class="has">
<code class="language-java">// 订阅一个主题
val df = spark
  .readStream
  .format("kafka")
  .option("kafka.bootstrap.servers", "idayan00:9092,idayan01:9092,idayan02:9092")
  .option("subscribe", "topicA")
  .load()
df.selectExpr("CAST(key AS STRING)", "CAST(value AS STRING)")
  .as[(String, String)]

//  订阅多个主题
val df = spark
  .readStream
  .format("kafka")
  .option("kafka.bootstrap.servers", "idayan00:9092,idayan01:9092,idayan02:9092")
  .option("subscribe", "topicA,topicA")
  .load()
df.selectExpr("CAST(key AS STRING)", "CAST(value AS STRING)")
  .as[(String, String)]

// 订阅通过正则匹配的主题
val df = spark
  .readStream
  .format("kafka")
  .option("kafka.bootstrap.servers", "idayan00:9092,idayan01:9092,idayan02:9092")
  .option("subscribePattern", "topic*")
  .load()
df.selectExpr("CAST(key AS STRING)", "CAST(value AS STRING)")
  .as[(String, String)]</code></pre>

<h3 id="2.2.2%20%E4%BB%8E%E6%89%B9%E6%9F%A5%E8%AF%A2Kafka%E6%95%B0%E6%8D%AE%E6%BA%90%EF%BC%88spark.readStream%E5%8F%98%E6%88%90%E4%BA%86spark.read%EF%BC%89">2.2 从批查询Kafka数据源（spark.readStream变成了spark.read）</h3>

<pre class="has">
<code class="language-java">// 订阅一个主题， 默认使用最早、最新的偏移量
val df = spark
  .read
  .format("kafka")
  .option("kafka.bootstrap.servers", "host1:9092,host2:9092")
  .option("subscribe", "topicA")
  .load()
df.selectExpr("CAST(key AS STRING)", "CAST(value AS STRING)")
  .as[(String, String)]

// 订阅多个主题，, 指定明确的 Kafka 偏移量
val df = spark
  .read
  .format("kafka")
  .option("kafka.bootstrap.servers", "host1:9092,host2:9092")
  .option("subscribe", "topicA,topicA")
  .option("startingOffsets", """{"topic1":{"0":23,"1":-2},"topic2":{"0":-2}}""")
  .option("endingOffsets", """{"topic1":{"0":50,"1":-1},"topic2":{"0":-1}}""")
  .load()
df.selectExpr("CAST(key AS STRING)", "CAST(value AS STRING)")
  .as[(String, String)]

// 订阅通过正则匹配的主题, 使用最早、最新的偏移量
val df = spark
  .read
  .format("kafka")
  .option("kafka.bootstrap.servers", "host1:port1,host2:port2")
  .option("subscribePattern", "topic.*")
  .option("startingOffsets", "earliest")
  .option("endingOffsets", "latest")
  .load()
df.selectExpr("CAST(key AS STRING)", "CAST(value AS STRING)")
  .as[(String, String)]</code></pre>

<p>数据源中各字段模式：</p>

<table><tbody><tr><td colspan="1" rowspan="1"><strong>Column</strong></td>
			<td colspan="1" rowspan="1"><strong>Type</strong></td>
		</tr><tr><td colspan="1" rowspan="1">key</td>
			<td colspan="1" rowspan="1">binary</td>
		</tr><tr><td colspan="1" rowspan="1">value</td>
			<td colspan="1" rowspan="1">binary</td>
		</tr><tr><td colspan="1" rowspan="1">topic</td>
			<td colspan="1" rowspan="1">string</td>
		</tr><tr><td colspan="1" rowspan="1">partition</td>
			<td colspan="1" rowspan="1">int</td>
		</tr><tr><td colspan="1" rowspan="1">offset</td>
			<td colspan="1" rowspan="1">long</td>
		</tr><tr><td colspan="1" rowspan="1">timestamp</td>
			<td colspan="1" rowspan="1">long</td>
		</tr><tr><td colspan="1" rowspan="1">timestampType</td>
			<td colspan="1" rowspan="1">int</td>
		</tr></tbody></table><p>Kafka source必需的参数如下：</p>

<table><tbody><tr><td style="vertical-align:middle;"><strong>Option</strong></td>
			<td style="vertical-align:middle;"><strong>value</strong></td>
			<td style="vertical-align:middle;"><strong>meaning</strong></td>
		</tr><tr><td style="vertical-align:middle;">assign</td>
			<td style="vertical-align:middle;">JSON字符串：{"topicA":[0,1],"topicB":[2,4]}</td>
			<td style="vertical-align:middle;">指定主题分区消费。只有“assgin”,"subscribe"或"subscribePattern"当中的一个可以指定给Kafka source,</td>
		</tr><tr><td style="vertical-align:middle;">subscribe</td>
			<td style="vertical-align:middle;">逗号分隔的主题列表</td>
			<td style="vertical-align:middle;">需要订阅的主题。只有“assgin”,"subscribe"或"subscribePattern"当中的一个可以指定给Kafka source,</td>
		</tr><tr><td style="vertical-align:middle;">subscribePattern</td>
			<td style="vertical-align:middle;">Java正则字符串</td>
			<td style="vertical-align:middle;">用来订阅主题的正则表达式。只有“assgin”,"subscribe"或"subscribePattern"当中的一个可以指定给Kafka source,</td>
		</tr><tr><td style="vertical-align:middle;">kafka.bootstrap.servers</td>
			<td style="vertical-align:middle;">逗号分隔的主机端口列表</td>
			<td style="vertical-align:middle;">Kafka的"bootstrap.servers"配置</td>
		</tr></tbody></table><p>下述配置是可选的：</p>

<table><tbody><tr><td style="vertical-align:middle;"><strong>Option</strong></td>
			<td style="vertical-align:middle;"><strong>value</strong></td>
			<td style="vertical-align:middle;"><strong>default</strong></td>
			<td style="vertical-align:middle;"><strong>query type</strong></td>
			<td style="vertical-align:middle;"><strong>meaning</strong></td>
		</tr><tr><td style="vertical-align:middle;">startingOffsets</td>
			<td style="vertical-align:middle;">"earliest", "latest" (streaming only), or json string """ {"topicA":{"0":23,"1":-1},"topicB":{"0":-2}} """ </td>
			<td style="vertical-align:middle;">"latest" for streaming, "earliest" for batch</td>
			<td style="vertical-align:middle;">streaming and batch</td>
			<td style="vertical-align:middle;">查询的起点。“earliest”是从最早的offset起，“latest”最新的offset.也可使用json字符串来指定每个主题分区的起点和结束点。在json中 -2代表最早，-1代表最新。在批查询中latest不可用，流查询中，只有启动新查询时会使用这个值，并在下一次查询中重新获取（上一次查询的结束点）。新发现的分区会从最早点开始</td>
		</tr><tr><td style="vertical-align:middle;">endingOffsets</td>
			<td style="vertical-align:middle;">latest or json string {"topicA":{"0":23,"1":-1},"topicB":{"0":-1}} </td>
			<td style="vertical-align:middle;">latest</td>
			<td style="vertical-align:middle;">batch query</td>
			<td style="vertical-align:middle;">批查询的结束点。...</td>
		</tr><tr><td style="vertical-align:middle;">failOnDataLoss</td>
			<td style="vertical-align:middle;">true or false</td>
			<td style="vertical-align:middle;">true</td>
			<td style="vertical-align:middle;">streaming query</td>
			<td style="vertical-align:middle;">当数据可能丢失时查询是否会失败。这可作为失败预警，当它不能按预期工作时可以禁用掉。批查询时，如果因为数据丢失而获取不到数据，则查询总是会失败</td>
		</tr><tr><td style="vertical-align:middle;">kafkaConsumer.pollTimeoutMs</td>
			<td style="vertical-align:middle;">long</td>
			<td style="vertical-align:middle;">512</td>
			<td style="vertical-align:middle;">streaming and batch</td>
			<td style="vertical-align:middle;">检测executor中kafka数据的超时时间，单位毫秒</td>
		</tr><tr><td style="vertical-align:middle;">fetchOffset.numRetries</td>
			<td style="vertical-align:middle;">int</td>
			<td style="vertical-align:middle;">3</td>
			<td style="vertical-align:middle;">streaming and batch</td>
			<td style="vertical-align:middle;">放弃获取kafka offset前尝试的次数</td>
		</tr><tr><td>fetchOffset.retryIntervalMs</td>
			<td>long</td>
			<td>10</td>
			<td>streaming and batch</td>
			<td>重试获取kfaka offset前的等待时间</td>
		</tr><tr><td>maxOffsetsPerTrigger</td>
			<td>long</td>
			<td>none</td>
			<td>streaming and batch</td>
			<td>每个trigger间隔处理的最大offset数的比率。指定的offset总数会被成比例的分割到不同容量的主题分区去。</td>
		</tr></tbody></table><h2 id="%C2%A02.3%20%E5%90%91Kafka%E5%86%99%E6%95%B0%E6%8D%AE">3. 向Kafka写数据</h2>

<p style="text-indent:50px;">这里我们讨论如何将流查询和批查询结果写出到Apache Kafka.需要注意的是Apache Kafka只支持最少一次的写算法。所以， 当写到Kafka时（不管是流查询还是批查询），有些消息可能会重复。这是可能发生的，例如，kafka需要重试没有被Broker确认的信息时，即使Broker已经接收到并且写入了这个消息。因为kafka的这个写算法，Structured Streaming 不能保证这样的重复不发生。不过，如果写操作成功，你可以认为这个查询结果最少被写出了一次。一个在读取这些数据时去除重的方案是引入一个唯一key。</p>

<p>    写出到Kafka的DataFrame应该有下列结构：</p>

<table><tbody><tr><td style="vertical-align:middle;"><strong>Column</strong></td>
			<td style="vertical-align:middle;"><strong>Type</strong></td>
		</tr><tr><td style="vertical-align:middle;">key（可选）</td>
			<td style="vertical-align:middle;">string或 binary</td>
		</tr><tr><td style="vertical-align:middle;">value(必须)</td>
			<td style="vertical-align:middle;">string 或 binary</td>
		</tr><tr><td style="vertical-align:middle;">topic(*option)</td>
			<td style="vertical-align:middle;">string</td>
		</tr></tbody></table><p>* 如果主题的配置选项没有指定，那么topic这一列是必须的。</p>

<p>    value 列是唯一一个必须的。如果key 列没有指定，会默认指定为null。如果主题列不存在，那么他的值会被用作主题写给kafka，除非设置了主题配置。主题配置会覆盖主题列。</p>

<p>Kafka sink 必须配置下列选项：</p>

<table><tbody><tr><td style="vertical-align:middle;"><strong>option</strong></td>
			<td style="vertical-align:middle;"><strong>value</strong></td>
			<td style="vertical-align:middle;"><strong>meaning</strong></td>
		</tr><tr><td style="vertical-align:middle;">kafka.bootstrap.servers</td>
			<td style="vertical-align:middle;">逗号分隔的主机：端口列表</td>
			<td style="vertical-align:middle;">Kafka "bootstrap.servers" 配置</td>
		</tr></tbody></table><p>下述参数是可选的：</p>

<table><tbody><tr><td style="vertical-align:middle;">Option</td>
			<td style="vertical-align:middle;">value</td>
			<td style="vertical-align:middle;">default</td>
			<td style="vertical-align:middle;">query type</td>
			<td style="vertical-align:middle;">meaning</td>
		</tr><tr><td style="vertical-align:middle;">topic</td>
			<td style="vertical-align:middle;">string</td>
			<td style="vertical-align:middle;">none</td>
			<td style="vertical-align:middle;">streaming and batch</td>
			<td style="vertical-align:middle;">设置写出的kafka 主题，这个配置将覆盖数据中的主题列配置</td>
		</tr></tbody></table><h3 id="%C2%A02.3.1%20%E5%88%9B%E5%BB%BA%E6%B5%81%E6%9F%A5%E8%AF%A2Kafka%20Sink">3.1 创建流查询Kafka Sink</h3>

<pre class="has">
<code class="language-java">// Write key-value data from a DataFrame to a specific Kafka topic specified in an option
val ds = df
  .selectExpr("CAST(key AS STRING)", "CAST(value AS STRING)")
  .writeStream
  .format("kafka")
  .option("kafka.bootstrap.servers", "host1:port1,host2:port2")
  .option("topic", "topic1")
  .start()

// Write key-value data from a DataFrame to Kafka using a topic specified in the data
val ds = df
  .selectExpr("topic", "CAST(key AS STRING)", "CAST(value AS STRING)")
  .writeStream
  .format("kafka")
  .option("kafka.bootstrap.servers", "host1:port1,host2:port2")
  .start()</code></pre>

<h3 id="2.3.2%20%E5%88%9B%E5%BB%BA%E6%89%B9%E6%9F%A5%E8%AF%A2Kafka%20Sink">3.2 创建批查询Kafka Sink</h3>

<pre class="has">
<code class="language-java">/ Write key-value data from a DataFrame to a specific Kafka topic specified in an option
df.selectExpr("CAST(key AS STRING)", "CAST(value AS STRING)")
  .write
  .format("kafka")
  .option("kafka.bootstrap.servers", "host1:port1,host2:port2")
  .option("topic", "topic1")
  .save()

// Write key-value data from a DataFrame to Kafka using a topic specified in the data
df.selectExpr("topic", "CAST(key AS STRING)", "CAST(value AS STRING)")
  .write
  .format("kafka")
  .option("kafka.bootstrap.servers", "host1:port1,host2:port2")
  .save()</code></pre>

<h2 id="2.4%20Kafka%20%E7%89%B9%E6%9C%89%E5%8F%82%E6%95%B0%E9%85%8D%E7%BD%AE">4 Kafka 特有参数配置</h2>

<p style="text-indent:50px;">Kafka特有的配置可以通过DataStreamReader.option的kafka.前缀配置。例如 stream.option("kafka.bootstrap.servers", "host:port")，kafka可用的参数可以参见kafka生产者/消费者参数配置文档。<br>
    请注意，下述参数不可以设置，否则kafka source 或sink将会抛出异常：</p>

<ul><li>group.id: Kafka Source会为每个查询创建一个唯一的group id.</li>
	<li>auto.offset.reset: 设为source选项 startingOffsets为指定offset.</li>
	<li>key.deserializer： key总是使用ByteArrayDeserializer反序列化为字节数组。</li>
	<li>value.deserializer：value总是使用ByteArrayDeserializer反序列化为字节数组。</li>
	<li>key.serializer: key总是使用ByteArraySerializer 或StringSerializer序列化。</li>
	<li>value.serializer：value 总是使用ByteArraySerializer 或StringSerializer序列化。</li>
	<li>enable.auto.commit: Kafka source 不提交任何offset</li>
	<li>interceptor.classes: kafka source将values读取做字节数组。使用ConsumerInterceptor可能会破坏查询，因此是不安全的。</li>
</ul>            </div>
                </div>