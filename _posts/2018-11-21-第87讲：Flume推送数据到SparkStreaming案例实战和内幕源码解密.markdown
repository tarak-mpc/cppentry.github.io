---
layout:     post
title:      第87讲：Flume推送数据到SparkStreaming案例实战和内幕源码解密
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
</p>
<pre style="font-size:9pt;font-family:'宋体';background-color:rgb(255,255,255);"><span style="color:rgb(51,51,51);font-family:Arial, 'Microsoft YaHei';font-size:14px;line-height:14px;background-color:rgb(238,238,238);">有兴趣想学习国内整套Spark+Spark Streaming+Machine learning最</span><span style="color:rgb(51,51,51);font-family:Arial, 'Microsoft YaHei';font-size:14px;line-height:14px;background-color:rgb(238,238,238);">顶级</span><span style="color:rgb(51,51,51);font-family:Arial, 'Microsoft YaHei';font-size:14px;line-height:14px;background-color:rgb(238,238,238);">课程的，可加我qq  471186150。共享视频，性价比超高！</span></pre>
<br><p></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
本期内容：</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
1. Flume on HDFS案例回顾</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
2. Flume推送数据到Spark Streaming实战</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
3. 原理绘图剖析</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>1. Flume on HDFS案例回顾</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
上节课要求大家自己安装配置Flume，并且测试数据的传输。我昨天是要求传送的HDFS上。</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
文件配置：</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
~/.bashrc:</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
export FLUME_HOME=/usr/local/flume/apache-flume-1.6.0-bin</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
export FLUME_CONF_DIR=$FLUME_HOME/conf</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
PATH中增加：${FLUME_HOME}/bin;</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
拷贝conf/flume-conf.properties.template，更名为conf/flume-cong.properties，只写以下内容：</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
agent1表示代理名称</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
agent1.sources=source1</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
agent1.sinks=sink1</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
agent1.channels=channel1</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
#配置source1</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
agent1.sources.source1.type=spooldir</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
agent1.sources.source1.spoolDir=/usr/local/flume/tmp/TestDir</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
agent1.sources.source1.channels=channel1</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
agent1.sources.source1.fileHeader = false</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
agent1.sources.source1.interceptors = i1</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
agent1.sources.source1.interceptors.i1.type = timestamp</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
#配置sink1</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
agent1.sinks.sink1.type=hdfs</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
agent1.sinks.sink1.hdfs.path=hdfs://master:9000/library/flume</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
agent1.sinks.sink1.hdfs.fileType=DataStream</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
agent1.sinks.sink1.hdfs.writeFormat=TEXT</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
agent1.sinks.sink1.hdfs.rollInterval=1</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
agent1.sinks.sink1.channel=channel1</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
agent1.sinks.sink1.hdfs.filePrefix=%Y-%m-%d</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
#agent1.sinks.sink1.type=avro</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
#agent1.sinks.sink1.channel=channel1</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
#agent1.sinks.sink1.hostname=Master</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
#agent1.sinks.sink1.port=9999</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
#配置channel1</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
agent1.channels.channel1.type=file</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
agent1.channels.channel1.checkpointDir=/usr/local/flume/tmp/checkpointDir</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
agent1.channels.channel1.dataDirs=/usr/local/flume/tmp/dataDirs</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
flume-env.sh配置:</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
# export JAVA_HOME=/usr/lib/jvm/java-6-sun</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
export JAVA_HOME=/usr/local/jdk/jdk1.8.0_60</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
# Give Flume more memory and pre-allocate, enable remote monitoring via JMX</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
# export JAVA_OPTS="-Xms100m -Xmx2000m -Dcom.sun.management.jmxremote"</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
export JAVA_OPTS="-Xms100m -Xmx2000m -Dcom.sun.management.jmxremote"</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
建立文件夹 /usr/local/flume/tmp/TestDir。</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
在hdfs上建立/library/flume文件夹。</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
flume的bin文件夹下启动Flume:</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
./flume-ng agent -n agent1 -c conf -f /usr/local/flume/apache-flume-1.6.0-bin/conf/flume-conf.properties -Dflume.root.logger=DEBUG,console</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
在/usr/local/flume/tmp/TestDir下，拷入测试用的文件，比如：NOTICE</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
flume 控制台会有一些相关信息：</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
16/04/22 11:03:49 INFO avro.ReliableSpoolingFileEventReader: Preparing to move file /usr/local/flume/tmp/TestDir/NOTICE to /usr/local/flume/tmp/TestDir/NOTICE.COMPLETED</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
16/04/22 11:03:51 INFO hdfs.HDFSDataStream: Serializer = TEXT, UseRawLocalFileSystem = false</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
16/04/22 11:03:51 INFO hdfs.BucketWriter: Creating hdfs://master:9000/library/flume/2016-04-22.1461294231806.tmp</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
16/04/22 11:03:52 INFO hdfs.BucketWriter: Closing hdfs://master:9000/library/flume/2016-04-22.1461294231806.tmp</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
16/04/22 11:03:52 INFO hdfs.BucketWriter: Renaming hdfs://master:9000/library/flume/2016-04-22.1461294231806.tmp to hdfs://master:9000/library/flume/2016-04-22.1461294231806</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
可以发现本地的NOTICE文件更名为NOTICE.COMPLETED。</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
用浏览器查询：<a href="http://localhost:50070/explorer.html#/library/flume" rel="nofollow" style="color:#000000;">http://localhost:50070/explorer.html#/library/flume</a>，可看到Flume把NOTICE文件传送到 HDFS的/library/flume下，文件名为 2016-04-22.1461294231806。打开文件看内容可以验证。说明当Flume指定的源文件夹中有新文件时，Flume会自动将此文件，导入到Flume配置时指定的HDFS文件夹中。</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
一般正常业务情况下，应该是把Flume的数据放到Kafka中，然后让不同的数据消费者去消费数据。如果要在Flume和Kafka两者间做选择的话，则要看业务中数据是否持续不断的产生。如果是这样，则应该选择Kafka。如果产生的数据时大时小，甚至有些时间没有数据，则没必要用Kafka，可以节省资源。</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>2. Flume推送数据到Spark Streaming实战</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
现在我们不把Flume的数据导入到HDFS中，而是把数据推送到Spark Streaming中。</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
修改conf/flume-cong.properties文件，改导入到HDFS，变为推送到Spark Streaming。</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
#配置sink1</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
#agent1.sinks.sink1.type=hdfs</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
#agent1.sinks.sink1.hdfs.path=hdfs://master:9000/library/flume</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
#agent1.sinks.sink1.hdfs.fileType=DataStream</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
#agent1.sinks.sink1.hdfs.writeFormat=TEXT</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
#agent1.sinks.sink1.hdfs.rollInterval=1</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
#agent1.sinks.sink1.channel=channel1</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
#agent1.sinks.sink1.hdfs.filePrefix=%Y-%m-%d</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
agent1.sinks.sink1.type=avro</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
agent1.sinks.sink1.channel=channel1</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
agent1.sinks.sink1.hostname=Master</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
agent1.sinks.sink1.port=9999</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
编写Spark Streaming应用的Java程序：</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
public class FlumePushData2SparkStreaming {</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    public static void main(String[] args) {</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
        SparkConf conf = new SparkConf().setMaster("local[4]").setAppName("FlumePushDate2SparkStreaming");</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
        JavaStreamingContext jsc = new JavaStreamingContext(conf, Durations.seconds(30));</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
        JavaReceiverInputDStream lines = FlumeUtils.createStream(jsc,"Master", 9999);</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
        // 注意此处输入的event类型是SparkFlumeEvent类型。</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
        JavaDStream&lt;String&gt; words = lines.flatMap(new FlatMapFunction&lt;SparkFlumeEvent, String&gt;() {</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
            @Override</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
            public Iterable&lt;String&gt; call(SparkFlumeEvent event) throws Exception {</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                String line = new String(event.event().getBody().array());</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                return Arrays.asList(line.split(" "));</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
            }</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
        });</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
        JavaPairDStream&lt;String, Integer&gt; pairs = words.mapToPair(new PairFunction&lt;String, String, Integer&gt;() {</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
            @Override</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
            public Tuple2&lt;String, Integer&gt; call(String word) throws Exception {</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                return new Tuple2&lt;String, Integer&gt;(word, 1);</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
            }</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
        });</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
        JavaPairDStream&lt;String, Integer&gt; wordsCount = pairs.reduceByKey(new Function2&lt;Integer, Integer, Integer&gt;() {</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
            @Override</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
            public Integer call(Integer v1, Integer v2) throws Exception {</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
                return v1 + v2;</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
            }</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
        });</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
        wordsCount.print();</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
        jsc.start();</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
        jsc.awaitTermination();</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
        jsc.close();</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
    }</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
}</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
代码中用到了FlumeUtils。我们剖析一下代码中用到的FlumeUtils。</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
以上代码中FlumeUtil的方法createStream：</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160426000331658-670547077.png" alt="" style="border:0px;"></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
实际是调用以下createStream方法：</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160426000424611-1877647334.png" alt="" style="border:0px;"></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
可以看到流处理默认的存储方式是，既在内存，又在磁盘中，同时做序列化，而且用两台机器。</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
继续看调用的createStream方法：</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160426000502720-144850338.png" alt="" style="border:0px;"></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
实际上返回的是FlumeInputDStream对象，而且事件是Flume所定义的事件SparkFlumeEvent。所以要注意，在以上Java代码做flatMap时，FlatMapFunction的输入类型必须是SparkFlumeEvent类型。</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
再看看FlumeInputDStream的代码：</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160426000546611-1196489651.png" alt="" style="border:0px;"></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
可以看到getReceiver返回的是用于接收数据的FlumeReceiver对象。再看FlumeReceiver：</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160426000635158-339085343.png" alt="" style="border:0px;"></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
可以发现Flume使用了Netty。如果搞分布式编程，要重视使用Netty。</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
 </p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
把以上的应用Spark Streaming的Java程序运行起来。确认Flume也在运行。</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
我们找若干文件拷入TestDir文件夹，比如：flume下的若干文本文件。那么在Java运行的控制台，可以发现以下信息：</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160426000717861-563825012.png" alt="" style="border:0px;"></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
说明Flume推送数据到了Spark Streaming，Spark Streaming对数据及时进行了处理。</p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span>3. 原理绘图剖析</span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<span><img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160426000749361-989103463.jpg" alt="" style="border:0px;"></span></p>
<p align="left" style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<img src="http://images2015.cnblogs.com/blog/860767/201604/860767-20160426000807908-2039127583.png" alt="" style="border:0px;"></p>
<p style="font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:21px;background-color:rgb(238,238,238);">
<br></p>
<br>            </div>
                </div>