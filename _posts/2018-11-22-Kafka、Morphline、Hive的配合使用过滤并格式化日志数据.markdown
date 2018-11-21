---
layout:     post
title:      Kafka、Morphline、Hive的配合使用过滤并格式化日志数据
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><strong>目录</strong></p>

<ol><li id="%E6%97%A5%E5%BF%97%E6%95%B0%E6%8D%AE-toc" style="margin-left:80px;"><a href="#%E6%97%A5%E5%BF%97%E6%95%B0%E6%8D%AE" rel="nofollow">日志数据</a></li>
	<li id="%E5%88%9B%E5%BB%BA%E5%B9%B6%E7%BC%96%E5%86%992%E4%B8%AAFlume%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%EF%BC%9Akafka_morphline_in.conf%E3%80%81kafka_morphline_out.conf-toc" style="margin-left:80px;"><a href="#%E5%88%9B%E5%BB%BA%E5%B9%B6%E7%BC%96%E5%86%992%E4%B8%AAFlume%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%EF%BC%9Akafka_morphline_in.conf%E3%80%81kafka_morphline_out.conf" rel="nofollow">创建并编写2个Flume配置文件：kafka_morphline_in.conf、kafka_morphline_out.conf</a></li>
	<li id="%E5%9C%A8Flume%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E5%AD%98%E6%94%BE%E7%9A%84%E5%90%8C%E7%9B%AE%E5%BD%95%E4%B8%8B%E5%88%9B%E5%BB%BA%E5%B9%B6%E7%BC%96%E5%86%99Morphline%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%EF%BC%9Amorphline.conf-toc" style="margin-left:80px;"><a href="#%E5%9C%A8Flume%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E5%AD%98%E6%94%BE%E7%9A%84%E5%90%8C%E7%9B%AE%E5%BD%95%E4%B8%8B%E5%88%9B%E5%BB%BA%E5%B9%B6%E7%BC%96%E5%86%99Morphline%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%EF%BC%9Amorphline.conf" rel="nofollow">在Flume配置文件存放的同目录下创建并编写Morphline配置文件：morphline.conf</a></li>
	<li id="%E5%90%AF%E5%8A%A8Zookeeper-toc" style="margin-left:80px;"><a href="#%E5%90%AF%E5%8A%A8Zookeeper" rel="nofollow">启动Zookeeper</a></li>
	<li id="%E5%90%AF%E5%8A%A8Kafka-toc" style="margin-left:80px;"><a href="#%E5%90%AF%E5%8A%A8Kafka" rel="nofollow">启动Kafka</a></li>
	<li id="%E4%BB%A5kafka_morphline_out.conf%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6agent2%E5%90%AF%E5%8A%A8Flume-toc" style="margin-left:80px;"><a href="#%E4%BB%A5kafka_morphline_out.conf%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6agent2%E5%90%AF%E5%8A%A8Flume" rel="nofollow">以kafka_morphline_out.conf配置文件agent2启动Flume</a></li>
	<li id="%E4%BB%A5kafka_morphline_in.conf%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6agnet1%E5%90%AF%E5%8A%A8Flume-toc" style="margin-left:80px;"><a href="#%E4%BB%A5kafka_morphline_in.conf%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6agnet1%E5%90%AF%E5%8A%A8Flume" rel="nofollow">以kafka_morphline_in.conf配置文件agnet1启动Flume</a></li>
	<li id="%E5%9C%A8HDFS%E4%B8%AD%E6%A3%80%E6%9F%A5%E6%97%A5%E5%BF%97%E6%96%87%E4%BB%B6%E6%98%AF%E5%90%A6%E5%B7%B2%E7%BB%8F%E4%BF%9D%E5%AD%98%E5%88%B0HDFS%E3%80%82-toc" style="margin-left:80px;"><a href="#%E5%9C%A8HDFS%E4%B8%AD%E6%A3%80%E6%9F%A5%E6%97%A5%E5%BF%97%E6%96%87%E4%BB%B6%E6%98%AF%E5%90%A6%E5%B7%B2%E7%BB%8F%E4%BF%9D%E5%AD%98%E5%88%B0HDFS%E3%80%82" rel="nofollow">在HDFS中检查日志文件是否已经保存到HDFS。</a></li>
	<li id="%E5%90%AF%E5%8A%A8Hive%E5%B9%B6%E7%94%A8%E5%91%BD%E4%BB%A4%E5%BB%BA%E4%BB%A5%E6%97%B6%E9%97%B4%E5%88%86%E5%8C%BA%E7%9A%84%E8%A1%A8%EF%BC%8C%E5%B9%B6%E5%AF%BC%E5%85%A5HDFS%E4%B8%AD%E7%9A%84%E6%95%B0%E6%8D%AE%E3%80%82-toc" style="margin-left:80px;"><a href="#%E5%90%AF%E5%8A%A8Hive%E5%B9%B6%E7%94%A8%E5%91%BD%E4%BB%A4%E5%BB%BA%E4%BB%A5%E6%97%B6%E9%97%B4%E5%88%86%E5%8C%BA%E7%9A%84%E8%A1%A8%EF%BC%8C%E5%B9%B6%E5%AF%BC%E5%85%A5HDFS%E4%B8%AD%E7%9A%84%E6%95%B0%E6%8D%AE%E3%80%82" rel="nofollow">启动Hive并用命令建以时间分区的表，并导入HDFS中的数据。</a></li>
</ol><hr id="hr-toc"><p>1、日志数据</p>

<p><img alt="" class="has" height="563" src="https://img-blog.csdn.net/20180820093852182?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzM3ODkwNDgy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="560"></p>

<p style="text-indent:0;">下载地址：</p>

<p style="text-indent:0;"><a href="https://stusspueducn-my.sharepoint.com/:u:/g/personal/20154847880_stu_sspu_edu_cn/EcflB0XMZYJNjFq9yz38y48BIsSu-S7tii1t_wd7iRcYVA?e=3E0bid" rel="nofollow">https://stusspueducn-my.sharepoint.com/:u:/g/personal/20154847880_stu_sspu_edu_cn/EcflB0XMZYJNjFq9yz38y48BIsSu-S7tii1t_wd7iRcYVA?e=3E0bid</a></p>

<p style="text-indent:0;">下载后，将该日志文件存放到 /flume/morphline文件夹下</p>

<p style="text-indent:0;"><img alt="" class="has" height="146" src="https://img-blog.csdn.net/20180820100341914?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzM3ODkwNDgy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="291"></p>

<p style="text-indent:0;">schema文件：</p>

<p style="text-indent:0;">存放到 /flume/schema文件夹下，以便Morphline调用</p>

<p style="text-indent:0;"><img alt="" class="has" height="153" src="https://img-blog.csdn.net/20180820100531889?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzM3ODkwNDgy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="343"></p>

<pre class="has">
<code>{
	"type" : "record",
	"name" : "Line",
	"fields" : [
	{
		"name" : "date",
		"type" : "string"
	},{
		"name" : "time",
		"type" : "string"
	},{
		"name" : "stage",
		"type" : "string"
	},{
		"name" : "stage_status",
		"type" : "string"
	},{
		"name" : "soft_name",
		"type" : "string"
	},{
		"name" : "platform_type",
		"type" : "string"
	},{
		"name" : "version",
		"type" : "string"
	}
	]
}</code></pre>

<p style="text-indent:0;">2、创建并编写2个Flume配置文件：kafka_morphline_in.conf、kafka_morphline_out.conf</p>

<p style="text-indent:0;">kafka_morphline_in.conf :</p>

<pre class="has">
<code>agent1.sources = source_spool
agent1.channels = channel_kafka

agent1.sources.source_spool.type = spooldir
agent1.sources.source_spool.spoolDir = /flume/morphline

agent1.sources.source_spool.interceptors = i1
agent1.sources.source_spool.interceptors.i1.type=org.apache.flume.sink.solr.morphline.MorphlineInterceptor$Builder
agent1.sources.source_spool.interceptors.i1.morphlineFile=/etc/flume-ng/conf/morphline.conf
agent1.sources.source_spool.interceptors.i1.morphlineId=morphline1

agent1.sources.source_spool.channels = channel_kafka

agent1.channels.channel_kafka.type = org.apache.flume.channel.kafka.KafkaChannel
agent1.channels.channel_kafka.brokerList = localhost:9092
agent1.channels.channel_kafka.kafka.bootstrap.servers = localhost:9092
agent1.channels.channel_kafka.zookeeperConnect = localhost:2181
agent1.channels.channel_kafka.kafka.topic = access
agent1.channels.channel_kafka.kafka.consumer.group.id = flume-consumer
agent1.channels.channel_kafka.capacity = 1000
agent1.channels.channel_kafka.transactionCapacoty = 10000</code></pre>

<p>kafka_morphline_out.conf :</p>

<pre class="has">
<code>agent2.channels = channel_kafka
agent2.sinks = sink_hdfs

agent2.sinks.sink_hdfs.type = hdfs
agent2.sinks.sink_hdfs.filesuffix = .avro
agent2.sinks.sink_hdfs.hdfs.path = /loudacre/morphline/%{date}
agent2.sinks.sink_hdfs.hdfs.rollSize = 524288
agent2.sinks.sink_hdfs.hdfs.rollCount = 0
agent2.sinks.sink_hdfs.hdfs.rollInterval = 0
agent2.sinks.sink_hdfs.hdfs.threadsPoolSize = 30
agent2.sinks.sink_hdfs.hdfs.fileType=DataStream
agent2.sinks.sink_hdfs.serializer = org.apache.flume.serialization.AvroEventSerializer$Builder
agent2.sniks.sink_hdfs.serializer.compressionCodec = snappy

agent2.sinks.sink_hdfs.channel = channel_kafka

agent2.channels.channel_kafka.type = org.apache.flume.channel.kafka.KafkaChannel
agent2.channels.channel_kafka.brokerList = localhost:9092
agent2.channels.channel_kafka.kafka.bootstrap.servers = localhost:9092
agent2.channels.channel_kafka.zookeeperConnect = localhost:2181
agent2.channels.channel_kafka.kafka.topic = access
agent2.channels.channel_kafka.kafka.consumer.group.id = flume-consumer
agent2.channels.channel_kafka.capacity = 1000
agent2.channels.channel_kafka.transactionCapacoty = 10000</code></pre>

<p>3、在Flume配置文件存放的同目录下创建并编写Morphline配置文件：morphline.conf</p>

<p>morphline.conf:</p>

<pre class="has">
<code>morphlines: [
  {
    id: morphline1
    importCommands: ["org.kitesdk.**", "org.apache.solr.**"]
    commands : [
	{
		readLine {
		charset: UTF-8
		}
	}
	{
		split {
			inputField: message
			outputFields: [date, time, soft_info, version]
			separator: " "
			isRegex: false
			addEmptyStrings: false
			trim: true
		}
	}
	{
		split {
			inputField: soft_info
			outputFields: [stage, stage_status, name_platform]
			separator: ","
		}
	}
	{
		split {
			inputField: name_platform
			outputFields: [soft_name, platform_type]
			separator : ":"
		}
	}
	{
		addValues {
			flume.avro.schema.url: "file:/flume/schema/schema.avsc"
		}
	}
	{
		toAvro {
			schemaFile : /flume/schema/schema.avsc
		}	
	}	
	{
		writeAvroToByteArray {
			format : containerlessBinary
			#codec : snappy
		}
	}

    ]
  }
]</code></pre>

<p>4、启动Zookeeper</p>

<p>我这里的Zookeeper是安装在 /usr/lib文件夹下面的。</p>

<pre class="has">
<code>cd /usr/lib/zookeeper/
./bin/zkServer.sh conf/zoo.cfg</code></pre>

<p>5、启动Kafka</p>

<p>同样Kafka也是安装在 /usr/lib文件夹下面的。</p>

<pre class="has">
<code>$ cd /usr/lib/kafka
$ ./bin/kafka-server-start.sh config/server.properties</code></pre>

<p>server.properties是配置文件</p>

<p>6、以kafka_morphline_out.conf配置文件agent2启动Flume</p>

<p>我这里的flume配置文件目录为 /etc/flume-ng/conf</p>

<pre class="has">
<code>$ flume-ng agent --conf /etc/flume-ng/conf --conf-file /etc/flume-ng/conf/kafka_morphline_out.conf --name agent2 -Dflume.root.logger=INFO,console</code></pre>

<p>7、以kafka_morphline_in.conf配置文件agnet1启动Flume</p>

<pre class="has">
<code>$ flume-ng agent --conf /etc/flume-ng/conf --conf-file /etc/flume-ng/conf/kafka_morphline_in.conf --name agent1 -Dflume.root.logger=INFO,console</code></pre>

<p>8、在HDFS中检查日志文件是否已经保存到HDFS</p>

<p><img alt="" class="has" height="114" src="https://img-blog.csdn.net/20180820100808884?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzM3ODkwNDgy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="692"></p>

<p><img alt="" class="has" height="512" src="https://img-blog.csdn.net/20180820100944343?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzM3ODkwNDgy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="992"></p>

<p>9、启动Hive并用命令建以时间分区的表，并导入HDFS中的数据。</p>

<p>将之前放到 /flume/schema/schema.avsc文件复制一份到HDFS中存放，以便下一步使用</p>

<pre class="has">
<code>$ cd /flume/schema/
$ hdfs dfs -mkdir -p /loudacre/avro
$ hdfs dfs -put schema.avsc /loudacre/avro/schema.avsc</code></pre>

<p>启动Hive</p>

<pre class="has">
<code>$ hive</code></pre>

<p> 以date_part的String字段分区创建分区表，schema结构使用HDFS中的 /loudacre/avro/schema.avsc文件</p>

<pre class="has">
<code>CREATE EXTERNAL TABLE mor
PARTITIONED BY (date_part STRING)
ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.avro.AvroSerDe'
STORED AS
INPUTFORMAT 'org.apache.hadoop.hive.ql.io.avro.AvroContainerInputFormat'
OUTPUTFORMAT 'org.apache.hadoop.hive.ql.io.avro.AvroContainerOutputFormat'
LOCATION '/loudacre/morphline/'
TBLPROPERTIES ( 'avro.schema.url'='/loudacre/avro/schema.avsc' );</code></pre>

<p>将之前放入HDFS中的数据导入到hive中：</p>

<pre class="has">
<code>alter table mor add partition (date_part='20180804') location '/loudacre/morphline/2018-08-04';
alter table mor add partition (date_part='20180805') location '/loudacre/morphline/2018-08-05';
alter table mor add partition (date_part='20180806') location '/loudacre/morphline/2018-08-06';</code></pre>

<p>查询导入的数据进行测试：</p>

<pre class="has">
<code class="language-sql">select * from mor where date_part='20180805';</code></pre>

<p><img alt="" class="has" height="431" src="https://img-blog.csdn.net/20180820101824714?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzM3ODkwNDgy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1018"></p>            </div>
                </div>