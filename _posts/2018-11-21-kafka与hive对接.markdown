---
layout:     post
title:      kafka与hive对接
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p style="text-indent:50px;">Flume是Cloudera提供的一个高可用的，高可靠的，分布式的海量日志采集，聚合和传输的系统，Flume支持系统中定制的各类数据发送方，用于收集数据，同时，Flume提供对数据进行简单处理，并写到各种数据接受方（可定制的）的能力。</p>

<p style="text-indent:50px;">当前Flume有两个版本Flume 0.9X版本统称Flume-og，Flume1.X版本的统称Flume-ng。由于Flume-ng经过重大重构，与Flume-og有很大不同。使用时请注意区分。</p>

<p style="text-indent:50px;">接下来就是操作步骤了。</p>

<h1>1.hive建表</h1>

<p style="text-indent:50px;">注意:建表时要进行分桶，赋予事务性，这样才可以对hive进行增删改查。默认是不被允许的。</p>

<p style="text-indent:50px;">建表如下：</p>

<pre class="has">
<code>create table kafkatest(id int,name string,age int) clustered by(id) into 2 buckets stored as orc tblproperties(‘transactional’=’true’);</code></pre>

<h1>2.配置flume（kafkatohive.conf）官网上很清楚，<a href="http://flume.apache.org/FlumeUserGuide.html" rel="nofollow">用户文档</a></h1>

<pre class="has">
<code>a.sources=source_from_kafka
a.channels=mem_channel
a.sinks=hive_sink
#kafka为souce的配置
a.sources.source_from_kafka.type=org.apache.flume.source.kafka.KafkaSource
a.sources.source_from_kafka.zookeeperConnect=localhost:2181
a.sources.source_from_kafka.bootstrap.servers=localhost:9092
a.sources.source_from_kafka.topic=kafkatest
a.sources.source_from_kafka.channels=mem_channel
a.sources.source_from_kafka.consumer.timeout.ms=1000
#hive为sink的配置
a.sinks.hive_sink.type=hive
a.sinks.hive_sink.hive.metastore=thrift://localhost:9083
a.sinks.hive_sink.hive.database=itcast
a.sinks.hive_sink.hive.table=kafkatest
a.sinks.hive_sink.hive.txnsPerBatchAsk=2
a.sinks.hive_sink.batchSize=10
a.sinks.hive_sink.serializer=DELIMITED
a.sinks.hive_sink.serializer.delimiter=,
a.sinks.hive_sink.serializer.fieldnames=id,name,age
#channel的配置
a.channels.mem_channel.type=memory
a.channels.mem_channel.capacity=1000
a.channels.mem_channel.transactionCapacity=100
#三者之间的关系
a.sources.source_from_kafka.channels=mem_channel
a.sinks.hive_sink.channel=mem_channel
</code></pre>

<h1>3.启动hive前要设置hive.txn.manager</h1>

<p style="text-indent:50px;"> 第一种方式：脚本命令</p>

<pre class="has">
<code>set hive.txn.manager=org.apache.hadoop.hive.ql.lockmgr.DbTxnManager;</code></pre>

<p style="text-indent:50px;"> 第二种方式：配置文件（hive-site.xml）</p>

<pre class="has">
<code>&lt;property&gt;
    &lt;name&gt;hive.txn.manager&lt;/name&gt;
    &lt;value&gt;org.apache.hadoop.hive.ql.lockmgr.DbTxnManager&lt;/value&gt;
&lt;/property&gt;
</code></pre>

<h1>4.启动hive</h1>

<p style="text-indent:50px;">先执行如下脚本：</p>

<pre class="has">
<code>hive --service metastore &amp;</code></pre>

<p style="text-indent:50px;">再启动hive</p>

<h1>5.启动flume</h1>

<pre class="has">
<code> flume-ng agent --conf conf/ --conf-file conf/….  --name a -Dflume.root.logger=INFO,console;</code></pre>

<h1>6.创建kafka的topic</h1>

<pre class="has">
<code>kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic kafkatest;</code></pre>

<h1>7.启动kafka生产者，进行生产消息</h1>

<pre class="has">
<code>kafka-console-producer.sh --broker-list localhost:9092 --topic kafkatest;</code></pre>

<p>至此，就大功告成了，kafka生产的消息就可以通过flume下沉到hive中了。</p>            </div>
                </div>