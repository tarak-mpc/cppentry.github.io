---
layout:     post
title:      大数据技术之Kafka 第6章 kafka Streams
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h2><strong><strong><strong>6</strong></strong><strong><strong>.</strong></strong><strong><strong>1 </strong></strong><strong><strong>概述</strong></strong></strong></h2>

<h3><strong><strong><strong>6.1.1 Kafka Streams </strong></strong></strong></h3>

<p style="margin-left:0pt;">Kafka Streams。Apache Kafka开源项目的一个组成部分。是一个功能强大，易于使用的库。用于在Kafka上构建高可分布式、拓展性，容错的应用程序。</p>

<h3><strong><strong><strong>6.1.2 Kafka Streams</strong></strong><strong><strong>特点</strong></strong></strong></h3>

<p style="margin-left:0pt;">1）功能强大 </p>

<p style="margin-left:0pt;">高扩展性，弹性，容错 </p>

<p style="margin-left:0pt;">2）轻量级 </p>

<p style="margin-left:0pt;">无需专门的集群 </p>

<p style="margin-left:0pt;">一个库，而不是框架</p>

<p style="margin-left:0pt;">3）完全集成 </p>

<p style="margin-left:0pt;">100%的Kafka 0.10.0版本兼容</p>

<p style="margin-left:0pt;">易于集成到现有的应用程序 </p>

<p style="margin-left:0pt;">4）实时性</p>

<p style="margin-left:0pt;">毫秒级延迟 </p>

<p style="margin-left:0pt;">并非微批处理 </p>

<p style="margin-left:0pt;">窗口允许乱序数据 </p>

<p style="margin-left:0pt;">允许迟到数据</p>

<h3><strong><strong><strong>6.1.3 </strong></strong><strong><strong>为什么</strong></strong><strong><strong>要有Kafka Stream</strong></strong></strong></h3>

<p style="margin-left:0pt;">当前已经有非常多的流式处理系统，最知名且应用最多的开源流式处理系统有Spark Streaming和Apache Storm。Apache Storm发展多年，应用广泛，提供记录级别的处理能力，当前也支持SQL on Stream。而Spark Streaming基于Apache Spark，可以非常方便与图计算，SQL处理等集成，功能强大，对于熟悉其它Spark应用开发的用户而言使用门槛低。另外，目前主流的Hadoop发行版，如Cloudera和Hortonworks，都集成了Apache Storm和Apache Spark，使得部署更容易。</p>

<p style="margin-left:0pt;">既然Apache Spark与Apache Storm拥用如此多的优势，那为何还需要Kafka Stream呢？主要有如下原因。</p>

<p style="margin-left:0pt;">第一，Spark和Storm都是流式处理框架，而<span style="color:#ff0000;">Kafka Stream提供的是一个基于Kafka的流式处理</span><span style="color:#ff0000;">类库</span>。框架要求开发者按照特定的方式去开发逻辑部分，供框架调用。开发者很难了解框架的具体运行方式，从而使得调试成本高，并且使用受限。而Kafka Stream作为流式处理类库，直接提供具体的类给开发者调用，整个应用的运行方式主要由开发者控制，方便使用和调试。</p>

<p style="margin-left:0pt;"><img alt="" class="has" src="https://img-blog.csdn.net/20180910154548353?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NnZ3RnZnM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p style="margin-left:0pt;">第二，虽然Cloudera与Hortonworks方便了Storm和Spark的部署，但是这些框架的部署仍然相对复杂。而<span style="color:#ff0000;">Kafka Stream作为类库，可以非常方便的嵌入应用程序中，它对应用的打包和部署基本没有任何要求。</span></p>

<p style="margin-left:0pt;">第三，就流式处理系统而言，基本都支持Kafka作为数据源。例如Storm具有专门的kafka-spout，而Spark也提供专门的spark-streaming-kafka模块。事实上，Kafka基本上是主流的流式处理系统的标准数据源。换言之，<span style="color:#ff0000;">大部分流式系统中都已部署了Kafka，此时使用Kafka Stream的成本非常低。</span></p>

<p style="margin-left:0pt;">第四，<span style="color:#ff0000;">使用Storm或Spark Streaming时，需要为框架本身的进程预留资源</span>，如Storm的supervisor和Spark on YARN的node manager。即使对于应用实例而言，框架本身也会占用部分资源，如Spark Streaming需要为shuffle和storage预留内存。但是Kafka作为类库不占用系统资源。</p>

<p style="margin-left:0pt;">第五，由于<span style="color:#ff0000;">Kafka本身提供数据持久化</span>，因此Kafka Stream提供滚动部署和滚动升级以及重新计算的能力。</p>

<p style="margin-left:0pt;">第六，由于Kafka Consumer Rebalance机制，<span style="color:#ff0000;">Kafka Stream可以在线动态调整并行度</span>。</p>

<h2><strong><strong><strong>6</strong></strong><strong><strong>.</strong></strong><strong><strong>2 Kafka Stream</strong></strong><strong><strong>数据清洗</strong></strong><strong><strong>案例</strong></strong></strong></h2>

<p style="margin-left:0pt;">0）需求：</p>

<p style="margin-left:0pt;">实时处理单词带有”&gt;&gt;&gt;”前缀的内容。例如输入”atguigu&gt;&gt;&gt;ximenqing”，最终处理成“ximenqing”</p>

<p style="margin-left:0pt;">1）需求分析：</p>

<p style="margin-left:0pt;"><img alt="" class="has" src="https://img-blog.csdn.net/20180910154634756?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NnZ3RnZnM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p style="margin-left:0pt;">2）案例实操</p>

<p style="margin-left:0pt;">（1）创建一个工程，并添加jar包</p>

<p style="margin-left:0pt;">（2）创建主类</p>

<table border="1" cellspacing="0" style="margin-left:5.4pt;width:411.1pt;"><tbody><tr><td style="vertical-align:top;width:411.1pt;">
			<p style="margin-left:0pt;">package com.atguigu.kafka.stream;</p>

			<p style="margin-left:0pt;">import java.util.Properties;</p>

			<p style="margin-left:0pt;">import org.apache.kafka.streams.KafkaStreams;</p>

			<p style="margin-left:0pt;">import org.apache.kafka.streams.StreamsConfig;</p>

			<p style="margin-left:0pt;">import org.apache.kafka.streams.processor.Processor;</p>

			<p style="margin-left:0pt;">import org.apache.kafka.streams.processor.ProcessorSupplier;</p>

			<p style="margin-left:0pt;">import org.apache.kafka.streams.processor.TopologyBuilder;</p>

			<p style="margin-left:0pt;"> </p>

			<p style="margin-left:0pt;">public class Application {</p>

			<p style="margin-left:0pt;"> </p>

			<p style="margin-left:0pt;">public static void main(String[] args) {</p>

			<p style="margin-left:0pt;"> </p>

			<p style="margin-left:0pt;">// 定义输入的topic</p>

			<p style="margin-left:0pt;">        String from = "first";</p>

			<p style="margin-left:0pt;">        // 定义输出的topic</p>

			<p style="margin-left:0pt;">        String to = "second";</p>

			<p style="margin-left:0pt;"> </p>

			<p style="margin-left:0pt;">        // 设置参数</p>

			<p style="margin-left:0pt;">        Properties settings = new Properties();</p>

			<p style="margin-left:0pt;">        settings.put(StreamsConfig.APPLICATION_ID_CONFIG, "logFilter");</p>

			<p style="margin-left:0pt;">        settings.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, "hadoop102:9092");</p>

			<p style="margin-left:0pt;"> </p>

			<p style="margin-left:0pt;">        StreamsConfig config = new StreamsConfig(settings);</p>

			<p style="margin-left:0pt;"> </p>

			<p style="margin-left:0pt;">        // 构建拓扑</p>

			<p style="margin-left:0pt;">        TopologyBuilder builder = new TopologyBuilder();</p>

			<p style="margin-left:0pt;"> </p>

			<p style="margin-left:0pt;">        builder.addSource("SOURCE", from)</p>

			<p style="margin-left:0pt;">               .addProcessor("PROCESS", new ProcessorSupplier&lt;byte[], byte[]&gt;() {</p>

			<p style="margin-left:0pt;"> </p>

			<p style="margin-left:0pt;">@Override</p>

			<p style="margin-left:0pt;">public Processor&lt;byte[], byte[]&gt; get() {</p>

			<p style="margin-left:0pt;">// 具体分析处理</p>

			<p style="margin-left:0pt;">return new LogProcessor();</p>

			<p style="margin-left:0pt;">}</p>

			<p style="margin-left:0pt;">}, "SOURCE")</p>

			<p style="margin-left:0pt;">                .addSink("SINK", to, "PROCESS");</p>

			<p style="margin-left:0pt;"> </p>

			<p style="margin-left:0pt;">        // 创建kafka stream</p>

			<p style="margin-left:0pt;">        KafkaStreams streams = new KafkaStreams(builder, config);</p>

			<p style="margin-left:0pt;">        streams.start();</p>

			<p style="margin-left:0pt;">}</p>

			<p style="margin-left:0pt;">}</p>
			</td>
		</tr></tbody></table><p style="margin-left:0pt;">（3）具体业务处理</p>

<table border="1" cellspacing="0" style="margin-left:5.4pt;width:411.1pt;"><tbody><tr><td style="vertical-align:top;width:411.1pt;">
			<p style="margin-left:0pt;">package com.atguigu.kafka.stream;</p>

			<p style="margin-left:0pt;">import org.apache.kafka.streams.processor.Processor;</p>

			<p style="margin-left:0pt;">import org.apache.kafka.streams.processor.ProcessorContext;</p>

			<p style="margin-left:0pt;"> </p>

			<p style="margin-left:0pt;">public class LogProcessor implements Processor&lt;byte[], byte[]&gt; {</p>

			<p style="margin-left:0pt;"> </p>

			<p style="margin-left:0pt;">private ProcessorContext context;</p>

			<p style="margin-left:0pt;"> </p>

			<p style="margin-left:0pt;">@Override</p>

			<p style="margin-left:0pt;">public void init(ProcessorContext context) {</p>

			<p style="margin-left:0pt;">this.context = context;</p>

			<p style="margin-left:0pt;">}</p>

			<p style="margin-left:0pt;"> </p>

			<p style="margin-left:0pt;">@Override</p>

			<p style="margin-left:0pt;">public void process(byte[] key, byte[] value) {</p>

			<p style="margin-left:0pt;">String input = new String(value);</p>

			<p style="margin-left:0pt;"> </p>

			<p style="margin-left:0pt;">// 如果包含“&gt;&gt;&gt;”则只保留该标记后面的内容</p>

			<p style="margin-left:0pt;">if (input.contains("&gt;&gt;&gt;")) {</p>

			<p style="margin-left:0pt;">input = input.split("&gt;&gt;&gt;")[1].trim();</p>

			<p style="margin-left:0pt;">// 输出到下一个topic</p>

			<p style="margin-left:0pt;">context.forward("logProcessor".getBytes(), input.getBytes());</p>

			<p style="margin-left:0pt;">}else{</p>

			<p style="margin-left:0pt;">context.forward("logProcessor".getBytes(), input.getBytes());</p>

			<p style="margin-left:0pt;">}</p>

			<p style="margin-left:0pt;">}</p>

			<p style="margin-left:0pt;"> </p>

			<p style="margin-left:0pt;">@Override</p>

			<p style="margin-left:0pt;">public void punctuate(long timestamp) {</p>

			<p style="margin-left:0pt;"> </p>

			<p style="margin-left:0pt;">}</p>

			<p style="margin-left:0pt;"> </p>

			<p style="margin-left:0pt;">@Override</p>

			<p style="margin-left:0pt;">public void close() {</p>

			<p style="margin-left:0pt;"> </p>

			<p style="margin-left:0pt;">}</p>

			<p style="margin-left:0pt;">}</p>
			</td>
		</tr></tbody></table><p style="margin-left:0pt;">（4）运行程序</p>

<p style="margin-left:0pt;"><span style="color:#000000;">（</span><span style="color:#000000;">5</span><span style="color:#000000;">）在hadoop</span><span style="color:#000000;">104</span><span style="color:#000000;">上</span><span style="color:#000000;">启动生产者</span></p>

<p style="margin-left:0pt;"><span style="color:#7030a0;">[atguigu@hadoop104 kafka]$ bin/kafka-console-producer.sh --broker-list hadoop102:9092 --topic first</span></p>

<p style="margin-left:0pt;">&gt;hello&gt;&gt;&gt;world</p>

<p style="margin-left:0pt;">&gt;h&gt;&gt;&gt;atguigu</p>

<p style="margin-left:0pt;">&gt;hahaha</p>

<p style="margin-left:0pt;">（6）在hadoop103上启动消费者</p>

<p style="margin-left:0pt;"><span style="color:#7030a0;">[atguigu@hadoop103 kafka]$ bin/kafka-console-consumer.sh --zookeeper hadoop102:2181 --from-beginning --topic second</span></p>

<p style="margin-left:0pt;">world</p>

<p style="margin-left:0pt;">atguigu</p>

<p style="margin-left:0pt;">hahaha</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;">本教程由尚硅谷教育大数据研究院出品，如需转载请注明来源。</p>

<p style="margin-left:0pt;"> </p>

<p style="margin-left:0pt;"> </p>            </div>
                </div>