---
layout:     post
title:      使用Kafka与Flume
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:24px;"><strong>一、描述</strong></span></p>
<p>      在CDH 5.2及更高版本中，Flume包含Kafka源和sink。 使用这些从Kafka到Hadoop或从任何Flume源到Kafka的流数据。</p>
<p><span style="font-size:24px;"><strong>二、条件</strong></span></p>
<p>      在CDH 5.7及更高版本中，Kafka的Flume连接器仅适用于Kafka 2.0及更高版本。</p>
<p><span style="font-size:24px;"><strong>三、要点</strong></span></p>
<p>      不要配置Kafka源以将数据发送到Kafka接收器。 如果这样做，Kafka源会在事件头中设置主题，覆盖sink配置并创建无限循环，在源和sink之间来回发送消息。 </p>
<p>      如果需要同时使用源和接收器，请使用拦截器修改事件头并设置不同的主题。</p>
<p><strong><span style="font-size:24px;">四、实践</span></strong></p>
<p>     (一)、Kafka来源</p>
<p>         使用Kafka源将Kafka主题中的数据流式传输到Hadoop。 Kafka源可以与任何Flume接收器结合使用，可以方便地将Kafka数据写入HDFS，HBase和Solr。</p>
<p>     1、以下Flume配置示例使用Kafka源向HDFS接收器发送数据：</p>
<p>          <span style="overflow:hidden;display:inline-block;vertical-align:middle;color:rgb(51,51,51);font-family:'Microsoft yahei';font-size:18px;line-height:25px;"><a href="https://code.csdn.net/snippets/1998894" rel="nofollow" style="color:rgb(51,51,51);text-decoration:none;background:transparent;">flume_get_kafka_then_send_data_to_hdfs.txt</a></span> 
      </p>
<p></p>
<div>          <pre><code class="language-plain">tier1.sources  = source1
tier1.channels = channel1
tier1.sinks = sink1

tier1.sources.source1.type = org.apache.flume.source.kafka.KafkaSource
#zookeeper地址
tier1.sources.source1.zookeeperConnect = hadoop1:2181,hadoop2:2181,hadoop3:2181
#topic名称
tier1.sources.source1.topic = 20161121a
tier1.sources.source1.groupId = flume
tier1.sources.source1.channels = channel1
tier1.sources.source1.interceptors = i1
tier1.sources.source1.interceptors.i1.type = timestamp
tier1.sources.source1.kafka.consumer.timeout.ms = 100

tier1.channels.channel1.type = memory
tier1.channels.channel1.capacity = 10000
tier1.channels.channel1.transactionCapacity = 1000

tier1.sinks.sink1.type = hdfs
#上传的Hdfs路径
tier1.sinks.sink1.hdfs.path = /user/root/testspark/%{topic}/%y-%m-%d
tier1.sinks.sink1.hdfs.rollInterval = 5
tier1.sinks.sink1.hdfs.rollSize = 0
tier1.sinks.sink1.hdfs.rollCount = 0
tier1.sinks.sink1.hdfs.fileType = DataStream
tier1.sinks.sink1.channel = channel1</code></pre></div>
<div>       注意：</div>
<div>            为了获得更高的吞吐量，请配置多个Kafka源以从同一主题读取。</div>
<div>            如果配置具有相同groupID的所有源，并且主题包含多个分区，则每个源从不同的分区集中读取数据，从而提高吞吐率。</div>
<div>     2、下表描述了Kafka源支持的参数; 所需属性以粗体列出：</div>
<div>
<table id="concept_rsb_tyb_kv__table_ubc_tyb_kv" class="table" style="text-align:left;border-collapse:collapse;border-spacing:0px;border:0px;color:rgb(102,102,102);font-family:'CalibreWeb-Regular', Arial;font-size:18px;line-height:23.4px;"><thead class="thead" align="left" style="font-size:14px;"><tr class="row" style="margin-left:-15px;"><th class="entry" valign="top" width="25%" id="d12147e81" style="text-align:left;line-height:1.2;vertical-align:top;border-width:0px 1px 1px;border-right-style:solid;border-bottom-style:solid;border-left-style:solid;border-right-color:rgb(229,229,229);border-bottom-color:rgb(229,229,229);border-left-color:rgb(229,229,229);color:rgb(255,255,255);background-color:rgb(41,167,222);">
<span style="font-size:12px;">Property Name</span></th>
<th class="entry" valign="top" width="12%" id="d12147e84" style="text-align:left;line-height:1.2;vertical-align:top;border-width:0px 1px 1px;border-right-style:solid;border-bottom-style:solid;border-left-style:solid;border-right-color:rgb(229,229,229);border-bottom-color:rgb(229,229,229);border-left-color:rgb(229,229,229);color:rgb(255,255,255);background-color:rgb(41,167,222);">
<span style="font-size:12px;">Default     Value</span></th>
<th class="entry" valign="top" width="62%" id="d12147e87" style="text-align:left;line-height:1.2;vertical-align:top;border-width:0px 1px 1px;border-right-style:solid;border-bottom-style:solid;border-left-style:solid;border-right-color:rgb(229,229,229);border-bottom-color:rgb(229,229,229);border-left-color:rgb(229,229,229);color:rgb(255,255,255);background-color:rgb(41,167,222);">
<span style="font-size:12px;">Description</span></th>
</tr></thead><tbody class="tbody" style="border-width:1px 1px 1px 0px;border-top-style:solid;border-right-style:solid;border-bottom-style:solid;border-top-color:rgb(229,229,229);border-right-color:rgb(229,229,229);border-bottom-color:rgb(229,229,229);border-collapse:separate;"><tr class="row" style="margin-left:-15px;"><td class="entry" valign="top" width="25%" style="text-align:left;line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span class="ph b"><strong><span style="color:#ff0000;"><span style="font-size:10px;">type</span></span></strong></span></td>
<td class="entry" valign="top" width="12%" style="text-align:left;line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;"><br></span></td>
<td class="entry" valign="top" width="62%" style="text-align:left;line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">Must be set to <samp class="ph codeph" style="font-family:monospace, serif;">org.apache.flume.source.kafka.KafkaSource</samp>.</span></td>
</tr><tr class="row" style="margin-left:-15px;"><td class="entry" valign="top" width="25%" style="text-align:left;line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span class="ph b"><strong><span style="color:#ff0000;"><span style="font-size:10px;">zookeeperConnect</span></span></strong></span></td>
<td class="entry" valign="top" width="12%" style="text-align:left;line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;"><br></span></td>
<td class="entry" valign="top" width="62%" style="text-align:left;line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">The URI of the ZooKeeper server or quorum used by Kafka. This can be a single host (for example, <samp class="ph codeph" style="font-family:monospace, serif;">zk01.example.com:2181</samp>) or a comma-separated
 list of hosts in a ZooKeeper quorum (for example, <samp class="ph codeph" style="font-family:monospace, serif;">zk01.example.com:2181,zk02.example.com:2181, zk03.example.com:2181</samp>).</span></td>
</tr><tr class="row" style="margin-left:-15px;"><td class="entry" valign="top" width="25%" style="text-align:left;line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span class="ph b"><span style="color:#ff0000;"><strong><span style="font-size:10px;">topic</span></strong></span></span></td>
<td class="entry" valign="top" width="12%" style="text-align:left;line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;"><br></span></td>
<td class="entry" valign="top" width="62%" style="text-align:left;line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">The Kafka topic from which this source reads messages. Flume supports only one topic per source.</span></td>
</tr><tr class="row" style="margin-left:-15px;"><td class="entry" valign="top" width="25%" style="text-align:left;line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">groupID</span></td>
<td class="entry" valign="top" width="12%" style="text-align:left;line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">flume</span></td>
<td class="entry" valign="top" width="62%" style="text-align:left;line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">The unique identifier of the Kafka consumer group. Set the same<samp class="ph codeph" style="font-family:monospace, serif;">groupID</samp> in all sources to indicate that they belong to the same
 consumer group.</span></td>
</tr><tr class="row" style="margin-left:-15px;"><td class="entry" valign="top" width="25%" style="text-align:left;line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">batchSize</span></td>
<td class="entry" valign="top" width="12%" style="text-align:left;line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">1000</span></td>
<td class="entry" valign="top" width="62%" style="text-align:left;line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">The maximum number of messages that can be written to a channel in a single batch.</span></td>
</tr><tr class="row" style="margin-left:-15px;"><td class="entry" valign="top" width="25%" style="text-align:left;line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">batchDurationMillis</span></td>
<td class="entry" valign="top" width="12%" style="text-align:left;line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">1000</span></td>
<td class="entry" valign="top" width="62%" style="text-align:left;line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">The maximum time (in ms) before a batch is written to the channel. The batch is written when the <samp class="ph codeph" style="font-family:monospace, serif;">batchSize</samp> limit or<samp class="ph codeph" style="font-family:monospace, serif;">batchDurationMillis</samp> limit
 is reached, whichever comes first.</span></td>
</tr><tr class="row" style="margin-left:-15px;"><td class="entry" valign="top" width="25%" style="text-align:left;line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">Other properties supported by the Kafka consumer</span></td>
<td class="entry" valign="top" width="12%" style="text-align:left;line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;"><br></span></td>
<td class="entry" valign="top" width="62%" style="text-align:left;line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">Used to configure the Kafka consumer used by the Kafka source. You can use any consumer properties supported by Kafka. Prepend the consumer property name with the prefix <samp class="ph codeph" style="font-family:monospace, serif;">kafka.</samp> (for
 example, <samp class="ph codeph" style="font-family:monospace, serif;">kafka.fetch.min.bytes</samp>). See the <a class="xref" href="http://kafka.apache.org/documentation.html#consumerconfigs" rel="nofollow" style="color:rgb(51,122,183);text-decoration:none;font-family:'CalibreWeb-Regular';background-color:transparent;">Kafka
 documentation</a>for the full list of Kafka consumer properties.</span></td>
</tr></tbody></table>
     3、调整注释：</div>
<div>          Kafka源覆盖两个Kafka使用者参数：<br>
         (1)、auto.commit.enable由源设置为false，并且每个批处理都提交。 </div>
<div>            为了提高性能，请使用kafka.auto.commit.enable设置将其设置为true。 如果源在提交前失败，则可能导致数据丢失。<br>
         (2)、consumer.timeout.ms设置为10，因此当Flume轮询Kafka的新数据时，它等待不超过10毫秒的数据可用。 </div>
<div>            将此设置为更高的值可以降低CPU利用率，因为轮询频率较低，但在向通道写入批处理时引入了延迟。</div>
<div><br><div style="text-align:left;">     (二)Kafka水槽</div>
<div style="text-align:left;">          1、描述：使用Kafka sink从Flume源向Kafka发送数据。 您可以使用Kafka接收器除了Flume接收器如HBase或HDFS。<br><div>          2、以下Flume配置示例使用带有exec源的Kafka sink：</div>
<div>
<div>          <pre><code class="language-plain">tier1.sources  = source1
 tier1.channels = channel1
 tier1.sinks = sink1
 
 tier1.sources.source1.type = exec
 tier1.sources.source1.command = /usr/bin/vmstat 1
 tier1.sources.source1.channels = channel1
 
 tier1.channels.channel1.type = memory
 tier1.channels.channel1.capacity = 10000
 tier1.channels.channel1.transactionCapacity = 1000
 
 tier1.sinks.sink1.type = org.apache.flume.sink.kafka.KafkaSink
 #Kafka的topic和broker
 tier1.sinks.sink1.topic = 20161121a
 tier1.sinks.sink1.brokerList = hadoop3:9092,hadoop4:9092
 tier1.sinks.sink1.channel = channel1
 tier1.sinks.sink1.batchSize = 20</code></pre></div>
</div>
</div>
          3、下表描述了Kafka sink支持的参数; 所必须的需属性以粗体列出。</div>
<div>          
<table id="concept_rsb_tyb_kv__table_fmc_tyb_kv" class="table" style="border-collapse:collapse;border-spacing:0px;border:0px;color:rgb(102,102,102);font-family:'CalibreWeb-Regular', Arial;font-size:18px;line-height:23.4px;"><thead class="thead" align="left" style="font-size:14px;"><tr class="row" style="margin-left:-15px;"><th class="entry" valign="top" width="25%" id="d12147e282" style="line-height:1.2;vertical-align:top;border-width:0px 1px 1px;border-right-style:solid;border-bottom-style:solid;border-left-style:solid;border-right-color:rgb(229,229,229);border-bottom-color:rgb(229,229,229);border-left-color:rgb(229,229,229);color:rgb(255,255,255);background-color:rgb(41,167,222);">
<span style="font-size:10px;">Property Name</span></th>
<th class="entry" valign="top" width="18%" id="d12147e285" style="line-height:1.2;vertical-align:top;border-width:0px 1px 1px;border-right-style:solid;border-bottom-style:solid;border-left-style:solid;border-right-color:rgb(229,229,229);border-bottom-color:rgb(229,229,229);border-left-color:rgb(229,229,229);color:rgb(255,255,255);background-color:rgb(41,167,222);">
<span style="font-size:10px;">Default Value</span></th>
<th class="entry" valign="top" width="56%" id="d12147e288" style="line-height:1.2;vertical-align:top;border-width:0px 1px 1px;border-right-style:solid;border-bottom-style:solid;border-left-style:solid;border-right-color:rgb(229,229,229);border-bottom-color:rgb(229,229,229);border-left-color:rgb(229,229,229);color:rgb(255,255,255);background-color:rgb(41,167,222);">
<span style="font-size:10px;">Description</span></th>
</tr></thead><tbody class="tbody" style="border-width:1px 1px 1px 0px;border-top-style:solid;border-right-style:solid;border-bottom-style:solid;border-top-color:rgb(229,229,229);border-right-color:rgb(229,229,229);border-bottom-color:rgb(229,229,229);border-collapse:separate;"><tr class="row" style="margin-left:-15px;"><td class="entry" valign="top" width="25%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span class="ph b"><span style="color:#ff0000;"><strong><span style="font-size:10px;">type</span></strong></span></span></td>
<td class="entry" valign="top" width="18%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;"><br></span></td>
<td class="entry" valign="top" width="56%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">Must be set to <samp class="ph codeph" style="font-family:monospace, serif;">org.apache.flume.sink.kafka.KafkaSink</samp>.</span></td>
</tr><tr class="row" style="margin-left:-15px;"><td class="entry" valign="top" width="25%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span class="ph b"><strong><span style="color:#ff0000;"><span style="font-size:10px;">brokerList</span></span></strong></span></td>
<td class="entry" valign="top" width="18%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;"><br></span></td>
<td class="entry" valign="top" width="56%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">The brokers the Kafka sink uses to discover topic partitions, formatted as a comma-separated list of<samp class="ph codeph" style="font-family:monospace, serif;">hostname:port</samp> entries. You
 do not need to specify the entire list of brokers, but Cloudera recommends that you specify at least two for high availability.</span></td>
</tr><tr class="row" style="margin-left:-15px;"><td class="entry" valign="top" width="25%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">topic</span></td>
<td class="entry" valign="top" width="18%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">default-flume-topic</span></td>
<td class="entry" valign="top" width="56%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">The Kafka topic to which messages are published by default. If the event header contains a <samp class="ph codeph" style="font-family:monospace, serif;">topic</samp> field, the event is published
 to the designated topic, overriding the configured topic.</span></td>
</tr><tr class="row" style="margin-left:-15px;"><td class="entry" valign="top" width="25%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">batchSize</span></td>
<td class="entry" valign="top" width="18%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">100</span></td>
<td class="entry" valign="top" width="56%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">The number of messages to process in a single batch. Specifying a larger <samp class="ph codeph" style="font-family:monospace, serif;">batchSize</samp> can improve throughput and increase latency.</span></td>
</tr><tr class="row" style="margin-left:-15px;"><td class="entry" valign="top" width="25%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">request.required.acks</span></td>
<td class="entry" valign="top" width="18%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">0</span></td>
<td class="entry" valign="top" width="56%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">The number of replicas that must acknowledge a message before it is written successfully. Possible values are <samp class="ph codeph" style="font-family:monospace, serif;">0</samp> (do not wait for
 an acknowledgement), <samp class="ph codeph" style="font-family:monospace, serif;">1</samp> (wait for the leader to acknowledge only), and <samp class="ph codeph" style="font-family:monospace, serif;">-1</samp> (wait
 for all replicas to acknowledge). To avoid potential loss of data in case of a leader failure, set this to <samp class="ph codeph" style="font-family:monospace, serif;">-1</samp>.</span></td>
</tr><tr class="row" style="margin-left:-15px;"><td class="entry" valign="top" width="25%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">Other properties supported by the Kafka producer</span></td>
<td class="entry" valign="top" width="18%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;"><br></span></td>
<td class="entry" valign="top" width="56%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">Used to configure the Kafka producer used by the Kafka sink. You can use any producer properties supported by Kafka. Prepend the producer property name with the prefix <samp class="ph codeph" style="font-family:monospace, serif;">kafka.</samp> (for
 example, <samp class="ph codeph" style="font-family:monospace, serif;">kafka.compression.codec</samp>). See the <a class="xref" href="http://kafka.apache.org/documentation.html#producerconfigs" rel="nofollow" style="color:rgb(51,122,183);text-decoration:none;font-family:'CalibreWeb-Regular';background-color:transparent;">Kafka
 documentation</a> for the full list of Kafka producer properties.</span></td>
</tr></tbody></table></div>
<div>  备注：</div>
<div>       Kafka sink使用FlumeEvent头中的主题和键属性来确定在Kafka中发送事件的位置。 </div>
<div>       1、如果头包含topic属性，那么该事件将发送到指定的主题，覆盖已配置的主题。 </div>
<div>       2、如果标题包含键属性，那么该键用于分隔主题中的事件。 具有相同键的事件将发送到同一个分区。 </div>
<div>       3、如果未指定键参数，则事件将随机分布到分区。 使用这些属性来控制通过Flume源或拦截器发送事件的主题和分区。</div>
<div><br></div>
<div>     (三)Kafka渠道</div>
<div>          1、描述：CDH 5.3及更高版本包括除了现有存储器和文件通道之外的到Flume的Kafka通道。 您可以使用Kafka渠道：</div>
<div>                (1)、直接从Kafka写入Hadoop，而不使用源代码。<br>
                (2)、直接从Flume源写入Kafka，而不需要额外的缓冲。<br>
                (3)、作为任何源/汇组合的可靠和高可用性通道。</div>
<div><br></div>
<div>          2、以下Flume配置使用带有exec源和hdfs sink的Kafka通道：</div>
<div>
<div>             <pre><code class="language-plain">tier1.sources = source1
tier1.channels = channel1
tier1.sinks = sink1

tier1.sources.source1.type = exec
tier1.sources.source1.command = /usr/bin/vmstat 1
tier1.sources.source1.channels = channel1

tier1.channels.channel1.type = org.apache.flume.channel.kafka.KafkaChannel
tier1.channels.channel1.capacity = 10000
tier1.channels.channel1.transactionCapacity = 1000
#kafka的topic和broker地址
tier1.channels.channel1.brokerList = hadoop3:9092,hadoop4:9092
tier1.channels.channel1.topic = channel2
#zookeeper地址
tier1.channels.channel1.zookeeperConnect = hadoop1:2181,hadoop2:2181,hadoop3:2181
tier1.channels.channel1.parseAsFlumeEvent = true

tier1.sinks.sink1.type = hdfs
#hdfs路径
tier1.sinks.sink1.hdfs.path = /user/root/testspark/20161121a
tier1.sinks.sink1.hdfs.rollInterval = 5
tier1.sinks.sink1.hdfs.rollSize = 0
tier1.sinks.sink1.hdfs.rollCount = 0
tier1.sinks.sink1.hdfs.fileType = DataStream
tier1.sinks.sink1.channel = channel1</code></pre></div>
</div>
<div><br></div>
<div>          3、下表描述了Kafka通道支持的参数; 所需属性以粗体列出。</div>
<div>
<table id="concept_rsb_tyb_kv__table_bxc_tyb_kv" class="table" style="border-collapse:collapse;border-spacing:0px;border:0px;color:rgb(102,102,102);font-family:'CalibreWeb-Regular', Arial;font-size:18px;line-height:23.4px;"><thead class="thead" align="left" style="font-size:14px;"><tr class="row" style="margin-left:-15px;"><th class="entry" valign="top" width="25%" id="d12147e477" style="line-height:1.2;vertical-align:top;border-width:0px 1px 1px;border-right-style:solid;border-bottom-style:solid;border-left-style:solid;border-right-color:rgb(229,229,229);border-bottom-color:rgb(229,229,229);border-left-color:rgb(229,229,229);color:rgb(255,255,255);background-color:rgb(41,167,222);">
<span style="font-size:10px;">Property Name</span></th>
<th class="entry" valign="top" width="12%" id="d12147e480" style="line-height:1.2;vertical-align:top;border-width:0px 1px 1px;border-right-style:solid;border-bottom-style:solid;border-left-style:solid;border-right-color:rgb(229,229,229);border-bottom-color:rgb(229,229,229);border-left-color:rgb(229,229,229);color:rgb(255,255,255);background-color:rgb(41,167,222);">
<span style="font-size:10px;">Default Value</span></th>
<th class="entry" valign="top" width="62%" id="d12147e483" style="line-height:1.2;vertical-align:top;border-width:0px 1px 1px;border-right-style:solid;border-bottom-style:solid;border-left-style:solid;border-right-color:rgb(229,229,229);border-bottom-color:rgb(229,229,229);border-left-color:rgb(229,229,229);color:rgb(255,255,255);background-color:rgb(41,167,222);">
<span style="font-size:10px;">Description</span></th>
</tr></thead><tbody class="tbody" style="border-width:1px 1px 1px 0px;border-top-style:solid;border-right-style:solid;border-bottom-style:solid;border-top-color:rgb(229,229,229);border-right-color:rgb(229,229,229);border-bottom-color:rgb(229,229,229);border-collapse:separate;"><tr class="row" style="margin-left:-15px;"><td class="entry" valign="top" width="25%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span class="ph b"><strong><span style="color:#ff0000;"><span style="font-size:10px;">type</span></span></strong></span></td>
<td class="entry" valign="top" width="12%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;"><br></span></td>
<td class="entry" valign="top" width="62%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">Must be set to <samp class="ph codeph" style="font-family:monospace, serif;">org.apache.flume.channel.kafka.KafkaChannel</samp>.</span></td>
</tr><tr class="row" style="margin-left:-15px;"><td class="entry" valign="top" width="25%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span class="ph b"><strong><span style="color:#ff0000;"><span style="font-size:10px;">brokerList</span></span></strong></span></td>
<td class="entry" valign="top" width="12%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;"><br></span></td>
<td class="entry" valign="top" width="62%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">The brokers the Kafka channel uses to discover topic partitions, formatted as a comma-separated list of<samp class="ph codeph" style="font-family:monospace, serif;">hostname:port</samp> entries.
 You do not need to specify the entire list of brokers, but Cloudera recommends that you specify at least two for high availability.</span></td>
</tr><tr class="row" style="margin-left:-15px;"><td class="entry" valign="top" width="25%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span class="ph b"><strong><span style="color:#ff0000;"><span style="font-size:10px;">zookeeperConnect</span></span></strong></span></td>
<td class="entry" valign="top" width="12%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;"><br></span></td>
<td class="entry" valign="top" width="62%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">The URI of the ZooKeeper server or quorum used by Kafka. This can be a single host (for example, <samp class="ph codeph" style="font-family:monospace, serif;">zk01.example.com:2181</samp>) or a comma-separated
 list of hosts in a ZooKeeper quorum (for example, <samp class="ph codeph" style="font-family:monospace, serif;">zk01.example.com:2181,zk02.example.com:2181, zk03.example.com:2181</samp>).</span></td>
</tr><tr class="row" style="margin-left:-15px;"><td class="entry" valign="top" width="25%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">topic</span></td>
<td class="entry" valign="top" width="12%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">flume-channel</span></td>
<td class="entry" valign="top" width="62%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">The Kafka topic the channel will use.</span></td>
</tr><tr class="row" style="margin-left:-15px;"><td class="entry" valign="top" width="25%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">groupID</span></td>
<td class="entry" valign="top" width="12%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">flume</span></td>
<td class="entry" valign="top" width="62%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">The unique identifier of the Kafka consumer group the channel uses to register with Kafka.</span></td>
</tr><tr class="row" style="margin-left:-15px;"><td class="entry" valign="top" width="25%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">parseAsFlumeEvent</span></td>
<td class="entry" valign="top" width="12%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">true</span></td>
<td class="entry" valign="top" width="62%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">Set to <samp class="ph codeph" style="font-family:monospace, serif;">true</samp> if a Flume source is writing to the channel and expects AvroDataums with the FlumeEvent schema (<samp class="ph codeph" style="font-family:monospace, serif;">org.apache.flume.source.avro.AvroFlumeEvent</samp>)
 in the channel. Set to <samp class="ph codeph" style="font-family:monospace, serif;">false</samp> if other producers are writing to the topic that the channel is using.</span></td>
</tr><tr class="row" style="margin-left:-15px;"><td class="entry" valign="top" width="25%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">readSmallestOffset</span></td>
<td class="entry" valign="top" width="12%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">false</span></td>
<td class="entry" valign="top" width="62%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">If <samp class="ph codeph" style="font-family:monospace, serif;">true</samp>, reads all data in the topic. If <samp class="ph codeph" style="font-family:monospace, serif;">false</samp>,
 reads only data written after the channel has started. Only used when<samp class="ph codeph" style="font-family:monospace, serif;">parseAsFlumeEvent</samp> is <samp class="ph codeph" style="font-family:monospace, serif;">false</samp>.</span></td>
</tr><tr class="row" style="margin-left:-15px;"><td class="entry" valign="top" width="25%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">kafka.consumer.timeout.ms</span></td>
<td class="entry" valign="top" width="12%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">100</span></td>
<td class="entry" valign="top" width="62%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">Polling interval when writing to the sink.</span></td>
</tr><tr class="row" style="margin-left:-15px;"><td class="entry" valign="top" width="25%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">Other properties supported by the Kafka producer</span></td>
<td class="entry" valign="top" width="12%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;"><br></span></td>
<td class="entry" valign="top" width="62%" style="line-height:1.2;vertical-align:top;border:1px solid rgb(229,229,229);">
<span style="font-size:10px;">Used to configure the Kafka producer. You can use any producer properties supported by Kafka. Prepend the producer property name with the prefix <samp class="ph codeph" style="font-family:monospace, serif;">kafka.</samp> (for
 example,<samp class="ph codeph" style="font-family:monospace, serif;">kafka.compression.codec</samp>). See the <a class="xref" href="http://kafka.apache.org/documentation.html#producerconfigs" rel="nofollow" style="color:rgb(51,122,183);text-decoration:none;font-family:'CalibreWeb-Regular';background-color:transparent;">Kafka
 documentation</a> for the full list of Kafka producer properties.</span></td>
</tr></tbody></table><br></div>
<div><strong><span style="font-size:14px;">英文文档地址：<a href="https://www.cloudera.com/documentation/kafka/latest/topics/kafka_flume.html" rel="nofollow">https://www.cloudera.com/documentation/kafka/latest/topics/kafka_flume.html</a></span></strong></div>
<div><br></div>
<div><br></div>
<p></p>
            </div>
                </div>