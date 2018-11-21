---
layout:     post
title:      利用Flume拦截器（interceptors）实现Kafka Sink的自定义规则多分区写入
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>我们目前的业务场景如下：前端的5台日志收集服务器产生网站日志，使用Flume实时收集日志，并将日志发送至Kafka，然后Kafka中的日志一方面可以导入到HDFS，另一方面供实时计算模块使用。</p>

<p>前面的文章《<a href="http://lxw1234.com/archives/2015/10/538.htm" rel="nofollow">Kafka分区机制介绍与示例</a>》介绍过Kafka的分区机制。我们对Kafka中存储日志的Topic指定了多个分区，默认情况下，Kafka Sink在收到events之后，将会随机选择一个该Topic的分区来存储数据，但我们不想这么做，我们需要根据网站日志中的cookieid来决定events存储到哪个分区中，简单来说，就是对cookieid计算hashcode，取绝对值，然后和Topic的分区数做模运算，这样，即实现了多分区的负载均衡，又确保相同的cookieid会写入同一个分区中，这样的处理，对后续的实时计算模块大有好处（后续再介绍）。</p>

<p>而这样的需求，利用Flume的拦截器即可实现。前面有两篇文章<br>
《<a href="http://lxw1234.com/archives/2015/11/543.htm" rel="nofollow">Flume中的拦截器（Interceptor）介绍与使用（一）</a>》和<br>
《<a href="http://lxw1234.com/archives/2015/11/545.htm" rel="nofollow">Flume中的拦截器（Interceptor）介绍与使用（二）</a>》<br>
介绍了Flume的拦截器和使用示例，这里我们使用的拦截器是Regex Extractor Interceptor。<br>
即从原始events中抽取出cookieid，放入到header中，而Kafka Sink在写入Kafka的时候，会从header中获取指定的key，然后根据分区规则确定该条events写入哪个分区中。</p>

<h2>网站日志格式</h2>

<p>假设原始网站日志有三个字段，分别为 时间|cookieid|ip，中间以单竖线分隔，比如：</p>

<pre>

 </pre>

<ol><li>2015-10-30 16:00:00| 967837DE00026C55D8DB2E|127.0.0.1</li>
	<li>2015-10-30 16:05:00| 967837DE00026C55D8DB2E|127.0.0.1</li>
	<li>2015-10-30 17:10:00| AC19BBDC0002A955A4A48F|127.0.0.1</li>
	<li>2015-10-30 17:15:00| AC19BBDC0002A955A4A48F|127.0.0.1</li>
</ol><h2>Flume Source的配置</h2>

<pre>

 </pre>

<ol><li>agent_lxw1234.sources = sources1</li>
	<li>agent_lxw1234.channels = fileChannel</li>
	<li>agent_lxw1234.sinks = sink1</li>
	<li>##source 配置</li>
	<li>agent_lxw1234.sources.sources1.type = com.lxw1234.flume17.TaildirSource</li>
	<li>agent_lxw1234.sources.sources1.positionFile = /tmp/flume/agent_lxw1234_position.json</li>
	<li>agent_lxw1234.sources.sources1.filegroups = f1</li>
	<li>agent_lxw1234.sources.sources1.filegroups.f1 = /tmp/lxw1234_.*.log</li>
	<li>agent_lxw1234.sources.sources1.batchSize = 100</li>
	<li>agent_lxw1234.sources.sources1.backoffSleepIncrement = 1000</li>
	<li>agent_lxw1234.sources.sources1.maxBackoffSleep = 5000</li>
	<li>agent_lxw1234.sources.sources1.channels = fileChannel</li>
	<li> </li>
</ol><p>该source用于监控/tmp/lxw1234_.*.log命名格式的文件。</p>

<h2>Flume Source拦截器配置</h2>

<pre>

 </pre>

<ol><li>## source 拦截器</li>
	<li>agent_lxw1234.sources.sources1.interceptors = i1</li>
	<li>agent_lxw1234.sources.sources1.interceptors.i1.type = regex_extractor</li>
	<li>agent_lxw1234.sources.sources1.interceptors.i1.regex = .*?\\|(.*?)\\|.*</li>
	<li>agent_lxw1234.sources.sources1.interceptors.i1.serializers = s1</li>
	<li>agent_lxw1234.sources.sources1.interceptors.i1.serializers.s1.name = key</li>
</ol><p>该拦截器（Regex Extractor Interceptor）用于从原始日志中抽取cookieid，访问到events header中，header名字为key。</p>

<h2>Flume Kafka Sink配置</h2>

<pre>

 </pre>

<ol><li># sink 1 配置</li>
	<li>agent_lxw1234.sinks.sink1.type = org.apache.flume.sink.kafka.KafkaSink</li>
	<li>agent_lxw1234.sinks.sink1.brokerList = developnode1:9091,developnode1:9092,developnode2:9091,developnode2:9092</li>
	<li>agent_lxw1234.sinks.sink1.topic = lxw1234</li>
	<li>agent_lxw1234.sinks.sink1.channel = fileChannel</li>
	<li>agent_lxw1234.sinks.sink1.batch-size = 100</li>
	<li>agent_lxw1234.sinks.sink1.requiredAcks = -1</li>
	<li>agent_lxw1234.sinks.sink1.kafka.partitioner.class = com.lxw1234.flume17.SimplePartitioner</li>
</ol><p>该Sink配置为Kafka Sink，将接收到的events发送至kafka集群的topic：lxw1234中。<br>
其中topic：lxw1234创建时候指定了4个分区，Kafka Sink使用的分区规则为<br>
com.lxw1234.flume17.SimplePartitioner，它会读取events header中的key值（即cookieid），然后对cookieid应用于分区规则，以便确定该条events发送至哪个分区中。<br>
关于com.lxw1234.flume17.SimplePartitioner的介绍和代码，见：<br>
《<a href="http://lxw1234.com/archives/2015/10/538.htm" rel="nofollow">Kafka分区机制介绍与示例</a>》。</p>

<h2>Kafka消费者</h2>

<p>使用下面的Java程序从Kafka中消费数据，打印出每条events所在的分区。</p>

<p>并从events中抽取cookieid，然后根据com.lxw1234.flume17.SimplePartitioner中的分区规则（Math.abs(cookieid.hashCode()) % 4）测试分区，看是否和获取到的分区一致。</p>

<pre>

 </pre>

<ol><li>package com.lxw1234.kafka;</li>
	<li> </li>
	<li>import java.util.HashMap;</li>
	<li>import java.util.List;</li>
	<li>import java.util.Map;</li>
	<li>import java.util.Properties;</li>
	<li> </li>
	<li>import kafka.consumer.Consumer;</li>
	<li>import kafka.consumer.ConsumerConfig;</li>
	<li>import kafka.consumer.ConsumerIterator;</li>
	<li>import kafka.consumer.KafkaStream;</li>
	<li>import kafka.javaapi.consumer.ConsumerConnector;</li>
	<li>import kafka.message.MessageAndMetadata;</li>
	<li> </li>
	<li>public class MyConsumer {</li>
	<li>public static void main(String[] args) {</li>
	<li>String topic = "lxw1234";</li>
	<li>ConsumerConnector consumer = Consumer.createJavaConsumerConnector(createConsumerConfig());</li>
	<li>Map&lt;String, Integer&gt; topicCountMap = new HashMap&lt;String, Integer&gt;();</li>
	<li>topicCountMap.put(topic, new Integer(1));</li>
	<li>Map&lt;String, List&lt;KafkaStream&lt;byte[], byte[]&gt;&gt;&gt; consumerMap = consumer.createMessageStreams(topicCountMap);</li>
	<li>KafkaStream&lt;byte[], byte[]&gt; stream = consumerMap.get(topic).get(0);</li>
	<li>ConsumerIterator&lt;byte[], byte[]&gt; it = stream.iterator();</li>
	<li>while(it.hasNext()) {</li>
	<li>MessageAndMetadata&lt;byte[], byte[]&gt; mam = it.next();</li>
	<li>String msg = new String(mam.message());</li>
	<li>String cookieid = msg.split("\\|")[1];</li>
	<li>int testPartition = Math.abs(cookieid.hashCode()) % 4;</li>
	<li>System.out.println("consume: Partition [" + mam.partition() + "] testPartition [" + testPartition + "] Message: [" + new String(mam.message()) + "] ..");</li>
	<li>}</li>
	<li> </li>
	<li>}</li>
	<li> </li>
	<li>private static ConsumerConfig createConsumerConfig() {</li>
	<li>Properties props = new Properties();</li>
	<li>props.put("group.id","group_lxw_test");</li>
	<li>props.put("zookeeper.connect","127.0.0.133:2182");</li>
	<li>props.put("zookeeper.session.timeout.ms", "4000");</li>
	<li>props.put("zookeeper.sync.time.ms", "200");</li>
	<li>props.put("auto.commit.interval.ms", "1000");</li>
	<li>props.put("auto.offset.reset", "smallest");</li>
	<li>return new ConsumerConfig(props);</li>
	<li>}</li>
	<li>}</li>
</ol><h2>运行结果</h2>

<p><img alt="kafka分区" class="has" height="643" src="http://7xipth.com1.z0.glb.clouddn.com/1102-9.jpg" width="780"></p>

<p>如图中红框所示，实际events所在的分区和期望分区（testPartition）的结果完全一致，由此可见，所有的events已经按照既定的规则写入Kafka分区中。</p>

<p>相关阅读：</p>

<h2><a href="http://lxw1234.com/archives/2015/10/538.htm" rel="nofollow">Kafka分区机制介绍与示例 </a></h2>

<h2><a href="http://lxw1234.com/archives/2015/11/543.htm" rel="nofollow">Flume中的拦截器（Interceptor）介绍与使用（一） </a></h2>

<h2><a href="http://lxw1234.com/archives/2015/11/545.htm" rel="nofollow">Flume中的拦截器（Interceptor）介绍与使用（二） </a></h2>

<h2><a href="http://lxw1234.com/archives/2015/10/524.htm" rel="nofollow">Flume中的TaildirSource </a></h2>            </div>
                </div>