---
layout:     post
title:      zookeeper+kafka+storm+flume+log4j
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/asdf57847225/article/details/78557565				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>1.修改 flume 配置并重启。</p>
<p>conf/log4j-kafka-flume.properties</p>
<p><br></p>
<p># Name the components on this agent<br>
a1.sources = r1<br>
a1.sinks = k1<br>
a1.channels = c1<br><br><br>
# Describe/configure the source<br>
a1.sources.r1.type = avro<br>
a1.sources.r1.bind = 0.0.0.0<br>
a1.sources.r1.port = 44446<br><br><br>
# Describe the sink<br>
a1.sinks.k1.type = org.apache.flume.sink.kafka.KafkaSink<br>
a1.sinks.k1.brokerList = storm1:9092,storm2:9092,storm3:9092<br>
a1.sinks.k1.topic = kafkatest<br>
a1.sinks.k1.serializer.class = kafka.serializer.StringEncoder<br>
a1.sinks.k1.producer.type = async<br><br><br>
# Use a channel which buffers events in memory<br>
a1.channels.c1.type = file<br>
#写出到检查点的目录<br>
a1.channels.c1.checkpointDir = /tmp/flume/channels/kafkatest/checkpoint<br>
#告诉 channel 一旦它被完全写出是否支持检查点。参数值 true/false 如果设置true，backupCheckpointDir参数必须设置<br>
a1.channels.c1.useDualCheckpoints = true<br>
#支持检查点的目录。如果主检查点损坏或不完整，channel 可以从备份中恢复从而避免数据文件的完整回放。不同于 checkpointDir<br>
a1.channels.c1.backupCheckpointDir = /tmp/flume/channels/kafkatest/backup<br>
#写入事件到以逗号分隔的列表目录。配置多个目录，每个挂载不同的磁盘，通过并行写入磁盘可以显著提高性能。<br>
a1.channels.c1.dataDirs = /data1/flume/channels/kafkatest/data<br>
#每次写入或读取应该等待完成的最大时间周期（以秒为单位）<br>
#a1.channels.c1.keep-alive = 30<br>
#每个数据文件的最大大小，字节为单位，一旦文件达到这个大小，该文件保存关闭并在那个目录下创建一个新的数据文件<br>
a1.channels.c1.maxFileSize = 20000<br>
# Bind the source and sink to the channel<br>
a1.sources.r1.channels = c1<br>
a1.sinks.k1.channel = c1<br></p>
<p><br></p>
<p></p>
<div style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace;background-color:rgb(244,244,244);">bin/flume-ng agent --conf-file  conf/conf/log4j-kafka-flume.properties -c conf/ --name
 a1-Dflume.root.logger=DEBUG,console</span></div>
<div><span style="font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace;background-color:rgb(244,244,244);"><br></span></div>
<br><p></p>
<p><br></p>
<p>2.新建 springboot 项目 springboot-log4j 用于 log4j 直接输出日志到 flume</p>
<p>（1）resource下新建 log4j.properties</p>
<p></p>
<pre style="background-color:rgb(255,255,255);font-size:9pt;font-family:'宋体';"><span style="color:rgb(128,128,128);"><em>### set log levels ###
</em></span><span style="color:rgb(0,0,128);"><strong>log4j.rootLogger</strong></span>=<span style="color:rgb(0,128,0);"><strong>INFO, stdout, file, flume
</strong></span><span style="color:rgb(0,0,128);"><strong>log4j.logger.per.flume</strong></span>=<span style="color:rgb(0,128,0);"><strong>INFO
</strong></span><span style="color:rgb(0,128,0);"><strong>
</strong></span><span style="color:rgb(128,128,128);"><em>### flume ###
</em></span><span style="color:rgb(0,0,128);"><strong>log4j.appender.flume</strong></span>=<span style="color:rgb(0,128,0);"><strong>org.apache.flume.clients.log4jappender.Log4jAppender
</strong></span><span style="color:rgb(0,0,128);"><strong>log4j.appender.flume.layout</strong></span>=<span style="color:rgb(0,128,0);"><strong>org.apache.log4j.PatternLayout
</strong></span><span style="color:rgb(0,0,128);"><strong>log4j.appender.flume.Hostname</strong></span>=<span style="color:rgb(0,128,0);"><strong>192.168.0.66
</strong></span><span style="color:rgb(0,0,128);"><strong>log4j.appender.flume.Port</strong></span>=<span style="color:rgb(0,128,0);"><strong>44446
</strong></span><span style="color:rgb(0,128,0);"><strong></strong></span></pre><pre style="background-color:rgb(255,255,255);font-family:'宋体';font-size:9pt;"><span style="color:#000080;"><strong>log4j.appender.flume.UnsafeMode</strong></span>=<span style="color:#008000;"><strong>true</strong></span></pre>
<pre style="background-color:rgb(255,255,255);font-size:9pt;font-family:'宋体';"><span style="color:rgb(0,128,0);"><strong>
</strong></span><span style="color:rgb(128,128,128);"><em>### stdout ###
</em></span><span style="color:rgb(0,0,128);"><strong>log4j.appender.stdout</strong></span>=<span style="color:rgb(0,128,0);"><strong>org.apache.log4j.ConsoleAppender
</strong></span><span style="color:rgb(0,0,128);"><strong>log4j.appender.stdout.Threshold</strong></span>=<span style="color:rgb(0,128,0);"><strong>INFO
</strong></span><span style="color:rgb(0,0,128);"><strong>log4j.appender.stdout.Target</strong></span>=<span style="color:rgb(0,128,0);"><strong>System.out
</strong></span><span style="color:rgb(0,0,128);"><strong>log4j.appender.stdout.layout</strong></span>=<span style="color:rgb(0,128,0);"><strong>org.apache.log4j.PatternLayout
</strong></span><span style="color:rgb(0,0,128);"><strong>log4j.appender.stdout.layout.ConversionPattern</strong></span>=<span style="color:rgb(0,128,0);"><strong>%d{yyyy-MM-dd HH:mm:ss} %c{1} [%p] %m%n
</strong></span><span style="color:rgb(0,128,0);"><strong>
</strong></span><span style="color:rgb(128,128,128);"><em>### file ###
</em></span><span style="color:rgb(0,0,128);"><strong>log4j.appender.file</strong></span>=<span style="color:rgb(0,128,0);"><strong>org.apache.log4j.DailyRollingFileAppender
</strong></span><span style="color:rgb(0,0,128);"><strong>log4j.appender.file.Threshold</strong></span>=<span style="color:rgb(0,128,0);"><strong>INFO
</strong></span><span style="color:rgb(0,0,128);"><strong>log4j.appender.file.File</strong></span>=<span style="color:rgb(0,128,0);"><strong>./logs/tracker/tracker.log
</strong></span><span style="color:rgb(0,0,128);"><strong>log4j.appender.file.Append</strong></span>=<span style="color:rgb(0,128,0);"><strong>true
</strong></span><span style="color:rgb(0,0,128);"><strong>log4j.appender.file.DatePattern</strong></span>=<span style="color:rgb(0,128,0);"><strong>'.'yyyy-MM-dd
</strong></span><span style="color:rgb(0,0,128);"><strong>log4j.appender.file.layout</strong></span>=<span style="color:rgb(0,128,0);"><strong>org.apache.log4j.PatternLayout
</strong></span><span style="color:rgb(0,0,128);"><strong>log4j.appender.file.layout.ConversionPattern</strong></span>=<span style="color:rgb(0,128,0);"><strong>%d{yyyy-MM-dd HH:mm:ss} %c{1} [%p] %m%n</strong></span></pre>
<br><p></p>
<p>（2）pom.xml</p>
<p></p>
<pre style="background-color:rgb(255,255,255);font-family:'宋体';font-size:9pt;"></pre><pre style="background-color:rgb(255,255,255);font-family:'宋体';font-size:9pt;"><span style="font-style:italic;">&lt;?</span><span style="color:#0000ff;background-color:#efefef;"><strong>xml version</strong></span><span style="color:#008000;background-color:#efefef;"><strong>="1.0" </strong></span><span style="color:#0000ff;background-color:#efefef;"><strong>encoding</strong></span><span style="color:#008000;background-color:#efefef;"><strong>="UTF-8"</strong></span><span style="font-style:italic;">?&gt;
</span><span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>project </strong></span><span style="color:#0000ff;background-color:#efefef;"><strong>xmlns</strong></span><span style="color:#008000;background-color:#efefef;"><strong>="http://maven.apache.org/POM/4.0.0"
</strong></span><span style="color:#008000;background-color:#efefef;"><strong>         </strong></span><span style="color:#0000ff;background-color:#efefef;"><strong>xmlns:</strong></span><span style="color:#660e7a;background-color:#efefef;"><strong>xsi</strong></span><span style="color:#008000;background-color:#efefef;"><strong>="http://www.w3.org/2001/XMLSchema-instance"
</strong></span><span style="color:#008000;background-color:#efefef;"><strong>         </strong></span><span style="color:#660e7a;background-color:#efefef;"><strong>xsi</strong></span><span style="color:#0000ff;background-color:#efefef;"><strong>:schemaLocation</strong></span><span style="color:#008000;background-color:#efefef;"><strong>="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"</strong></span><span style="background-color:#efefef;">&gt;</span>
    <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>modelVersion</strong></span><span style="background-color:#efefef;">&gt;</span>4.0.0<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>modelVersion</strong></span><span style="background-color:#efefef;">&gt;</span>

    <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>groupId</strong></span><span style="background-color:#efefef;">&gt;</span>com.whl.demo<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>groupId</strong></span><span style="background-color:#efefef;">&gt;</span>
    <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>artifactId</strong></span><span style="background-color:#efefef;">&gt;</span>flume-log4j<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>artifactId</strong></span><span style="background-color:#efefef;">&gt;</span>
    <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>version</strong></span><span style="background-color:#efefef;">&gt;</span>1.0-SNAPSHOT<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>version</strong></span><span style="background-color:#efefef;">&gt;</span>

    <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>dependencies</strong></span><span style="background-color:#efefef;">&gt;</span>
        <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>dependency</strong></span><span style="background-color:#efefef;">&gt;</span>
            <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>groupId</strong></span><span style="background-color:#efefef;">&gt;</span>org.slf4j<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>groupId</strong></span><span style="background-color:#efefef;">&gt;</span>
            <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>artifactId</strong></span><span style="background-color:#efefef;">&gt;</span>slf4j-log4j12<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>artifactId</strong></span><span style="background-color:#efefef;">&gt;</span>
            <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>version</strong></span><span style="background-color:#efefef;">&gt;</span>1.7.10<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>version</strong></span><span style="background-color:#efefef;">&gt;</span>
        <span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>dependency</strong></span><span style="background-color:#efefef;">&gt;</span>
        <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>dependency</strong></span><span style="background-color:#efefef;">&gt;</span>
            <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>groupId</strong></span><span style="background-color:#efefef;">&gt;</span>org.apache.flume<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>groupId</strong></span><span style="background-color:#efefef;">&gt;</span>
            <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>artifactId</strong></span><span style="background-color:#efefef;">&gt;</span>flume-ng-core<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>artifactId</strong></span><span style="background-color:#efefef;">&gt;</span>
            <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>version</strong></span><span style="background-color:#efefef;">&gt;</span>1.6.0<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>version</strong></span><span style="background-color:#efefef;">&gt;</span>
        <span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>dependency</strong></span><span style="background-color:#efefef;">&gt;</span>
        <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>dependency</strong></span><span style="background-color:#efefef;">&gt;</span>
            <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>groupId</strong></span><span style="background-color:#efefef;">&gt;</span>org.apache.flume.flume-ng-clients<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>groupId</strong></span><span style="background-color:#efefef;">&gt;</span>
            <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>artifactId</strong></span><span style="background-color:#efefef;">&gt;</span>flume-ng-log4jappender<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>artifactId</strong></span><span style="background-color:#efefef;">&gt;</span>
            <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>version</strong></span><span style="background-color:#efefef;">&gt;</span>1.6.0<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>version</strong></span><span style="background-color:#efefef;">&gt;</span>
        <span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>dependency</strong></span><span style="background-color:#efefef;">&gt;</span>
    <span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>dependencies</strong></span><span style="background-color:#efefef;">&gt;</span>

<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>project</strong></span><span style="background-color:#efefef;">&gt;</span></pre><br>
<br><p></p>
<p></p>
<pre style="background-color:rgb(255,255,255);font-family:'宋体';font-size:9pt;">（3）新建测试类 WriteLog</pre><div>
</div>
<pre style="background-color:rgb(255,255,255);font-family:'宋体';font-size:9pt;"></pre><pre style="background-color:rgb(255,255,255);font-family:'宋体';font-size:9pt;"><span style="color:#000080;"><strong>package </strong></span>com.whl.demo;

<span style="color:#000080;"><strong>import </strong></span>org.slf4j.Logger;
<span style="color:#000080;"><strong>import </strong></span>org.slf4j.LoggerFactory;

<span style="color:#000080;"><strong>import </strong></span>java.util.Date;

<span style="color:#000080;"><strong>public class </strong></span>WriteLog {

    <span style="color:#000080;"><strong>protected static final </strong></span>Logger <span style="color:#660e7a;"><strong><em>logger </em></strong></span>= LoggerFactory.<span style="font-style:italic;">getLogger</span>(WriteLog.<span style="color:#000080;"><strong>class</strong></span>);

    <span style="color:#000080;"><strong>public static void </strong></span>main(String[] args) {

        <span style="color:#660e7a;"><strong><em>logger</em></strong></span>.info(String.<span style="font-style:italic;">valueOf</span>(<span style="color:#000080;"><strong>new </strong></span>Date().getTime()));
        <span style="color:#000080;"><strong>try </strong></span>{
            <span style="color:#000080;"><strong>for </strong></span>(<span style="color:#000080;"><strong>int </strong></span>i=<span style="color:#0000ff;">0</span>;i&lt;<span style="color:#0000ff;">10000</span>;i++){
                <span style="color:#808080;"><em>//Thread.sleep(2000);
</em></span><span style="color:#808080;"><em>                </em></span><span style="color:#660e7a;"><strong><em>logger</em></strong></span>.info(<span style="color:#008000;"><strong>"id--test:"</strong></span>+i);
            }
        } <span style="color:#000080;"><strong>catch </strong></span>(Exception e) {
            e.printStackTrace();
        }
    }
}
</pre><br>
<pre style="background-color:rgb(255,255,255);font-family:'宋体';font-size:9pt;">（4）运行上述代码后，便可在kafka消费者终端看到结果。</pre>
<pre style="background-color:rgb(255,255,255);font-family:'宋体';font-size:9pt;"></pre>
<pre style="background-color:rgb(255,255,255);font-family:'宋体';font-size:9pt;">2.新建springboot-storm 项目 用于 storm 处理实时消息。</pre>
<pre style="background-color:rgb(255,255,255);font-family:'宋体';font-size:9pt;">（1）pom.xml</pre>
<pre style="background-color:rgb(255,255,255);font-family:'宋体';font-size:9pt;"></pre>
<pre style="background-color:rgb(255,255,255);font-family:'宋体';font-size:9pt;"></pre><pre style="background-color:rgb(255,255,255);font-family:'宋体';font-size:9pt;"><span style="font-style:italic;">&lt;?</span><span style="color:#0000ff;background-color:#efefef;"><strong>xml version</strong></span><span style="color:#008000;background-color:#efefef;"><strong>="1.0" </strong></span><span style="color:#0000ff;background-color:#efefef;"><strong>encoding</strong></span><span style="color:#008000;background-color:#efefef;"><strong>="UTF-8"</strong></span><span style="font-style:italic;">?&gt;
</span><span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>project </strong></span><span style="color:#0000ff;background-color:#efefef;"><strong>xmlns</strong></span><span style="color:#008000;background-color:#efefef;"><strong>="http://maven.apache.org/POM/4.0.0"
</strong></span><span style="color:#008000;background-color:#efefef;"><strong>         </strong></span><span style="color:#0000ff;background-color:#efefef;"><strong>xmlns:</strong></span><span style="color:#660e7a;background-color:#efefef;"><strong>xsi</strong></span><span style="color:#008000;background-color:#efefef;"><strong>="http://www.w3.org/2001/XMLSchema-instance"
</strong></span><span style="color:#008000;background-color:#efefef;"><strong>         </strong></span><span style="color:#660e7a;background-color:#efefef;"><strong>xsi</strong></span><span style="color:#0000ff;background-color:#efefef;"><strong>:schemaLocation</strong></span><span style="color:#008000;background-color:#efefef;"><strong>="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"</strong></span><span style="background-color:#efefef;">&gt;</span>
    <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>modelVersion</strong></span><span style="background-color:#efefef;">&gt;</span>4.0.0<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>modelVersion</strong></span><span style="background-color:#efefef;">&gt;</span>

    <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>groupId</strong></span><span style="background-color:#efefef;">&gt;</span>com.whl.demo<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>groupId</strong></span><span style="background-color:#efefef;">&gt;</span>
    <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>artifactId</strong></span><span style="background-color:#efefef;">&gt;</span>storm-demo<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>artifactId</strong></span><span style="background-color:#efefef;">&gt;</span>
    <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>version</strong></span><span style="background-color:#efefef;">&gt;</span>1.0-SNAPSHOT<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>version</strong></span><span style="background-color:#efefef;">&gt;</span>
    <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>packaging</strong></span><span style="background-color:#efefef;">&gt;</span>jar<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>packaging</strong></span><span style="background-color:#efefef;">&gt;</span>

    <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>name</strong></span><span style="background-color:#efefef;">&gt;</span>storm-demo<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>name</strong></span><span style="background-color:#efefef;">&gt;</span>
    <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>url</strong></span><span style="background-color:#efefef;">&gt;</span>http://maven.apache.org<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>url</strong></span><span style="background-color:#efefef;">&gt;</span>

    <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>repositories</strong></span><span style="background-color:#efefef;">&gt;</span>
        <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>repository</strong></span><span style="background-color:#efefef;">&gt;</span>
            <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>id</strong></span><span style="background-color:#efefef;">&gt;</span>clojars.org<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>id</strong></span><span style="background-color:#efefef;">&gt;</span>
            <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>url</strong></span><span style="background-color:#efefef;">&gt;</span>http://clojars.org/repo<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>url</strong></span><span style="background-color:#efefef;">&gt;</span>
        <span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>repository</strong></span><span style="background-color:#efefef;">&gt;</span>
    <span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>repositories</strong></span><span style="background-color:#efefef;">&gt;</span>

    <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>properties</strong></span><span style="background-color:#efefef;">&gt;</span>
        <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>project.build.sourceEncoding</strong></span><span style="background-color:#efefef;">&gt;</span>UTF-8<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>project.build.sourceEncoding</strong></span><span style="background-color:#efefef;">&gt;</span>
    <span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>properties</strong></span><span style="background-color:#efefef;">&gt;</span>

    <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>dependencies</strong></span><span style="background-color:#efefef;">&gt;</span>

        <span style="color:#808080;"><em>&lt;!-- storm --&gt;
</em></span><span style="color:#808080;"><em>        </em></span><span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>dependency</strong></span><span style="background-color:#efefef;">&gt;</span>
            <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>groupId</strong></span><span style="background-color:#efefef;">&gt;</span>org.apache.storm<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>groupId</strong></span><span style="background-color:#efefef;">&gt;</span>
            <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>artifactId</strong></span><span style="background-color:#efefef;">&gt;</span>storm-core<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>artifactId</strong></span><span style="background-color:#efefef;">&gt;</span>
            <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>version</strong></span><span style="background-color:#efefef;">&gt;</span>0.10.2<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>version</strong></span><span style="background-color:#efefef;">&gt;</span>
            <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>scope</strong></span><span style="background-color:#efefef;">&gt;</span>provided<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>scope</strong></span><span style="background-color:#efefef;">&gt;</span>

        <span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>dependency</strong></span><span style="background-color:#efefef;">&gt;</span>

        <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>dependency</strong></span><span style="background-color:#efefef;">&gt;</span>
            <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>groupId</strong></span><span style="background-color:#efefef;">&gt;</span>org.twitter4j<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>groupId</strong></span><span style="background-color:#efefef;">&gt;</span>
            <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>artifactId</strong></span><span style="background-color:#efefef;">&gt;</span>twitter4j-stream<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>artifactId</strong></span><span style="background-color:#efefef;">&gt;</span>
            <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>version</strong></span><span style="background-color:#efefef;">&gt;</span>3.0.3<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>version</strong></span><span style="background-color:#efefef;">&gt;</span>

        <span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>dependency</strong></span><span style="background-color:#efefef;">&gt;</span>

        <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>dependency</strong></span><span style="background-color:#efefef;">&gt;</span>
            <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>groupId</strong></span><span style="background-color:#efefef;">&gt;</span>commons-collections<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>groupId</strong></span><span style="background-color:#efefef;">&gt;</span>
            <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>artifactId</strong></span><span style="background-color:#efefef;">&gt;</span>commons-collections<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>artifactId</strong></span><span style="background-color:#efefef;">&gt;</span>
            <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>version</strong></span><span style="background-color:#efefef;">&gt;</span>3.2.1<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>version</strong></span><span style="background-color:#efefef;">&gt;</span>

        <span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>dependency</strong></span><span style="background-color:#efefef;">&gt;</span>

        <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>dependency</strong></span><span style="background-color:#efefef;">&gt;</span>
            <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>groupId</strong></span><span style="background-color:#efefef;">&gt;</span>com.google.guava<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>groupId</strong></span><span style="background-color:#efefef;">&gt;</span>
            <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>artifactId</strong></span><span style="background-color:#efefef;">&gt;</span>guava<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>artifactId</strong></span><span style="background-color:#efefef;">&gt;</span>
            <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>version</strong></span><span style="background-color:#efefef;">&gt;</span>13.0<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>version</strong></span><span style="background-color:#efefef;">&gt;</span>
        <span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>dependency</strong></span><span style="background-color:#efefef;">&gt;</span>

        <span style="color:#808080;"><em>&lt;!-- kafka --&gt;
</em></span><span style="color:#808080;"><em>        </em></span><span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>dependency</strong></span><span style="background-color:#efefef;">&gt;</span>
            <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>groupId</strong></span><span style="background-color:#efefef;">&gt;</span>org.apache.kafka<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>groupId</strong></span><span style="background-color:#efefef;">&gt;</span>
            <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>artifactId</strong></span><span style="background-color:#efefef;">&gt;</span>kafka_2.10<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>artifactId</strong></span><span style="background-color:#efefef;">&gt;</span>
            <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>version</strong></span><span style="background-color:#efefef;">&gt;</span>0.9.0.0<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>version</strong></span><span style="background-color:#efefef;">&gt;</span>
            <span style="color:#808080;"><em>&lt;!-- 这个必须要加，否则会有jar包冲突导致无法 --&gt;
</em></span><span style="color:#808080;"><em>            </em></span><span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>exclusions</strong></span><span style="background-color:#efefef;">&gt;</span>
                <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>exclusion</strong></span><span style="background-color:#efefef;">&gt;</span>
                    <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>groupId</strong></span><span style="background-color:#efefef;">&gt;</span>org.slf4j<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>groupId</strong></span><span style="background-color:#efefef;">&gt;</span>
                    <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>artifactId</strong></span><span style="background-color:#efefef;">&gt;</span>slf4j-log4j12<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>artifactId</strong></span><span style="background-color:#efefef;">&gt;</span>
                <span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>exclusion</strong></span><span style="background-color:#efefef;">&gt;</span>
            <span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>exclusions</strong></span><span style="background-color:#efefef;">&gt;</span>
        <span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>dependency</strong></span><span style="background-color:#efefef;">&gt;</span>

        <span style="color:#808080;"><em>&lt;!-- storm-kafka --&gt;
</em></span><span style="color:#808080;"><em>        </em></span><span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>dependency</strong></span><span style="background-color:#efefef;">&gt;</span>
            <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>groupId</strong></span><span style="background-color:#efefef;">&gt;</span>org.apache.storm<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>groupId</strong></span><span style="background-color:#efefef;">&gt;</span>
            <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>artifactId</strong></span><span style="background-color:#efefef;">&gt;</span>storm-kafka<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>artifactId</strong></span><span style="background-color:#efefef;">&gt;</span>
            <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>version</strong></span><span style="background-color:#efefef;">&gt;</span>0.10.2<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>version</strong></span><span style="background-color:#efefef;">&gt;</span>
        <span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>dependency</strong></span><span style="background-color:#efefef;">&gt;</span>

        <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>dependency</strong></span><span style="background-color:#efefef;">&gt;</span>
            <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>groupId</strong></span><span style="background-color:#efefef;">&gt;</span>org.apache.kafka<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>groupId</strong></span><span style="background-color:#efefef;">&gt;</span>
            <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>artifactId</strong></span><span style="background-color:#efefef;">&gt;</span>kafka-clients<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>artifactId</strong></span><span style="background-color:#efefef;">&gt;</span>
            <span style="background-color:#efefef;">&lt;</span><span style="color:#000080;background-color:#efefef;"><strong>version</strong></span><span style="background-color:#efefef;">&gt;</span>0.9.0.0<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>version</strong></span><span style="background-color:#efefef;">&gt;</span>
        <span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>dependency</strong></span><span style="background-color:#efefef;">&gt;</span>

    <span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>dependencies</strong></span><span style="background-color:#efefef;">&gt;</span>

<span style="background-color:#efefef;">&lt;/</span><span style="color:#000080;background-color:#efefef;"><strong>project</strong></span><span style="background-color:#efefef;">&gt;</span></pre><br>
<pre style="background-color:rgb(255,255,255);font-family:'宋体';font-size:9pt;">（2）KafkaTopology 类</pre>
<pre style="background-color:rgb(255,255,255);font-family:'宋体';font-size:9pt;"></pre>
<pre style="background-color:rgb(255,255,255);font-family:'宋体';font-size:9pt;"><span style="color:#000080;"><strong>package </strong></span>com.whl.demo.bolts;

<span style="color:#000080;"><strong>import </strong></span>java.util.Arrays;

<span style="color:#000080;"><strong>import </strong></span>storm.kafka.BrokerHosts;
<span style="color:#000080;"><strong>import </strong></span>storm.kafka.KafkaSpout;
<span style="color:#000080;"><strong>import </strong></span>storm.kafka.SpoutConfig;
<span style="color:#000080;"><strong>import </strong></span>storm.kafka.StringScheme;
<span style="color:#000080;"><strong>import </strong></span>storm.kafka.ZkHosts;
<span style="color:#000080;"><strong>import </strong></span>backtype.storm.Config;
<span style="color:#000080;"><strong>import </strong></span>backtype.storm.LocalCluster;
<span style="color:#000080;"><strong>import </strong></span>backtype.storm.StormSubmitter;
<span style="color:#000080;"><strong>import </strong></span>backtype.storm.spout.SchemeAsMultiScheme;
<span style="color:#000080;"><strong>import </strong></span>backtype.storm.topology.TopologyBuilder;

<span style="color:#000080;"><strong>public class </strong></span>KafkaTopology {

    <span style="color:#000080;"><strong>public static void </strong></span>main(String[] args) {
        <span style="color:#000080;"><strong>try </strong></span>{
            System.<span style="color:#660e7a;"><strong><em>out</em></strong></span>.println(<span style="color:#008000;"><strong>"=============start================"</strong></span>);
            <span style="color:#808080;"><em>//实例化topologyBuilder类。
</em></span><span style="color:#808080;"><em>            </em></span>TopologyBuilder topologyBuilder = <span style="color:#000080;"><strong>new </strong></span>TopologyBuilder();
            <span style="color:#808080;"><em>//设置喷发节点并分配并发数，该并发数将会控制该对象在集群中的线程数。
</em></span><span style="color:#808080;"><em>            </em></span>String zks = <span style="color:#008000;"><strong>"storm1:2181,storm2:2181,storm3:2181"</strong></span>;
            String topic = <span style="color:#008000;"><strong>"kafkatest"</strong></span>;
            String zkRoot = <span style="color:#008000;"><strong>"/storm"</strong></span>; <span style="color:#808080;"><em>// default zookeeper root configuration for storm
</em></span><span style="color:#808080;"><em>            </em></span>String id = <span style="color:#008000;"><strong>"word"</strong></span>;

            BrokerHosts brokerHosts = <span style="color:#000080;"><strong>new </strong></span>ZkHosts(zks);
            <span style="color:#808080;"><em>// 配置Kafka订阅的Topic，以及zookeeper中数据节点目录和名字
</em></span><span style="color:#808080;"><em>            </em></span>SpoutConfig spoutConf = <span style="color:#000080;"><strong>new </strong></span>SpoutConfig(brokerHosts, topic, zkRoot, id);
            spoutConf.<span style="color:#660e7a;"><strong>scheme </strong></span>= <span style="color:#000080;"><strong>new </strong></span>SchemeAsMultiScheme(<span style="color:#000080;"><strong>new </strong></span>StringScheme());
            <span style="color:#808080;"><em>//spoutConf.forceFromStart = true;
</em></span><span style="color:#808080;"><em>            </em></span>spoutConf.<span style="color:#660e7a;"><strong>zkServers </strong></span>= Arrays.<span style="font-style:italic;">asList</span>(<span style="color:#000080;"><strong>new </strong></span>String[] {<span style="color:#008000;"><strong>"storm1"</strong></span>, <span style="color:#008000;"><strong>"storm2"</strong></span>, <span style="color:#008000;"><strong>"storm3"</strong></span>});
            spoutConf.<span style="color:#660e7a;"><strong>zkPort </strong></span>= <span style="color:#0000ff;">2181</span>;
            <span style="color:#808080;"><em>//spoutConf.forceFromStart = false;
</em></span><span style="color:#808080;"><em>            </em></span>KafkaSpout receiver = <span style="color:#000080;"><strong>new </strong></span>KafkaSpout(spoutConf);
            topologyBuilder.setSpout(<span style="color:#008000;"><strong>"kafka-spout"</strong></span>, receiver,<span style="color:#0000ff;">5</span>).setNumTasks(<span style="color:#0000ff;">10</span>);
            topologyBuilder.setBolt(<span style="color:#008000;"><strong>"kafka-bolt"</strong></span>, <span style="color:#000080;"><strong>new </strong></span>SimpleBolt(),<span style="color:#0000ff;">5</span>).setNumTasks(<span style="color:#0000ff;">10</span>).shuffleGrouping(<span style="color:#008000;"><strong>"kafka-spout"</strong></span>);
            <span style="color:#808080;"><em>//topologyBuilder.setBolt("kafka-hbase-bolt", new SimpleBolt2(),5).setNumTasks(10).shuffleGrouping("kafka-bolt");
</em></span><span style="color:#808080;"><em>            </em></span>Config config = <span style="color:#000080;"><strong>new </strong></span>Config();
            config.setDebug(<span style="color:#000080;"><strong>false</strong></span>);
            <span style="color:#000080;"><strong>if </strong></span>(args != <span style="color:#000080;"><strong>null </strong></span>&amp;&amp; args.<span style="color:#660e7a;"><strong>length </strong></span>&gt; <span style="color:#0000ff;">0</span>) {
                <span style="color:#808080;"><em>/*设置该topology在storm集群中要抢占的资源slot数，一个slot对应这supervisor节点上的以个worker进程
</em></span><span style="color:#808080;"><em>                 如果你分配的spot数超过了你的物理节点所拥有的worker数目的话，有可能提交不成功，加入你的集群上面已经有了
</em></span><span style="color:#808080;"><em>                 一些topology而现在还剩下2个worker资源，如果你在代码里分配4个给你的topology的话，那么这个topology可以提交
</em></span><span style="color:#808080;"><em>                 但是提交以后你会发现并没有运行。 而当你kill掉一些topology后释放了一些slot后你的这个topology就会恢复正常运行。
</em></span><span style="color:#808080;"><em>                */
</em></span><span style="color:#808080;"><em>                </em></span>config.setNumWorkers(<span style="color:#0000ff;">1</span>);
                StormSubmitter.<span style="font-style:italic;">submitTopology</span>(args[<span style="color:#0000ff;">0</span>], config, topologyBuilder.createTopology());
            }<span style="color:#000080;"><strong>else</strong></span>{
                <span style="color:#808080;"><em>//这里是本地模式下运行的启动代码。
</em></span><span style="color:#808080;"><em>                </em></span>config.setNumWorkers(<span style="color:#0000ff;">2</span>);
                config.setMaxTaskParallelism(<span style="color:#0000ff;">1</span>);
                LocalCluster cluster = <span style="color:#000080;"><strong>new </strong></span>LocalCluster();
                cluster.submitTopology(<span style="color:#008000;"><strong>"simple"</strong></span>, config,
                        topologyBuilder.createTopology());
            }
        } <span style="color:#000080;"><strong>catch </strong></span>(Exception e) {
            e.printStackTrace();  <span style="color:#808080;"><em>//To change body of catch statement use File | Settings | File Templates.
</em></span><span style="color:#808080;"><em>        </em></span>}
    }
}</pre>
<p></p>
<p>（3）SimpleBolt 类</p>
<p></p>
<pre style="background-color:rgb(255,255,255);font-family:'宋体';font-size:9pt;"><span style="color:#000080;"><strong>package </strong></span>com.whl.demo.bolts;


<span style="color:#000080;"><strong>import </strong></span>java.util.Map;

<span style="color:#000080;"><strong>import </strong></span>backtype.storm.task.OutputCollector;
<span style="color:#000080;"><strong>import </strong></span>backtype.storm.task.TopologyContext;
<span style="color:#000080;"><strong>import </strong></span>backtype.storm.topology.BasicOutputCollector;
<span style="color:#000080;"><strong>import </strong></span>backtype.storm.topology.OutputFieldsDeclarer;
<span style="color:#000080;"><strong>import </strong></span>backtype.storm.topology.base.BaseBasicBolt;
<span style="color:#000080;"><strong>import </strong></span>backtype.storm.topology.base.BaseRichBolt;
<span style="color:#000080;"><strong>import </strong></span>backtype.storm.tuple.Fields;
<span style="color:#000080;"><strong>import </strong></span>backtype.storm.tuple.Tuple;
<span style="color:#000080;"><strong>import </strong></span>backtype.storm.tuple.Values;
<span style="color:#000080;"><strong>import </strong></span>org.slf4j.Logger;
<span style="color:#000080;"><strong>import </strong></span>org.slf4j.LoggerFactory;

<span style="color:#000080;"><strong>public class </strong></span>SimpleBolt <span style="color:#000080;"><strong>extends </strong></span>BaseRichBolt{

    <span style="color:#000080;"><strong>private </strong></span>OutputCollector <span style="color:#660e7a;"><strong>collector</strong></span>;

    <span style="color:#000080;"><strong>protected static final </strong></span>Logger <span style="color:#660e7a;"><strong><em>logger </em></strong></span>= LoggerFactory.<span style="font-style:italic;">getLogger</span>(SimpleBolt.<span style="color:#000080;"><strong>class</strong></span>);

    <span style="color:#000080;"><strong>public void </strong></span>declareOutputFields(OutputFieldsDeclarer declarer) {
        declarer.declare(<span style="color:#000080;"><strong>new </strong></span>Fields(<span style="color:#008000;"><strong>"info"</strong></span>,<span style="color:#008000;"><strong>"id"</strong></span>));
    }

    <span style="color:#808000;">@SuppressWarnings</span>(<span style="color:#008000;"><strong>"rawtypes"</strong></span>)
    <span style="color:#000080;"><strong>public void </strong></span>prepare(Map stormConf, TopologyContext context,
                        OutputCollector collector) {
        <span style="color:#000080;"><strong>this</strong></span>.<span style="color:#660e7a;"><strong>collector </strong></span>= collector;
    }

    <span style="color:#000080;"><strong>public void </strong></span>execute(Tuple input) {
        <span style="color:#000080;"><strong>try </strong></span>{
            String mesg = input.getString(<span style="color:#0000ff;">0</span>);
            <span style="color:#000080;"><strong>if </strong></span>(mesg != <span style="color:#000080;"><strong>null</strong></span>) {
                <span style="color:#660e7a;"><strong>collector</strong></span>.emit(<span style="color:#000080;"><strong>new </strong></span>Values( mesg+<span style="color:#008000;"><strong>"mesg is processed!"</strong></span>,mesg));
               <span style="color:#808080;"><em>//System.out.println("Bolt"+this.hashCode()+":"+mesg);
</em></span><span style="color:#808080;"><em>                </em></span><span style="color:#660e7a;"><strong><em>logger</em></strong></span>.info(<span style="color:#008000;"><strong>"mesg:"</strong></span>+mesg);
            }
        } <span style="color:#000080;"><strong>catch </strong></span>(Exception e) {
            e.printStackTrace(); <span style="color:#808080;"><em>// To change body of catch statement use File |
</em></span><span style="color:#808080;"><em>            </em></span><span style="color:#660e7a;"><strong>collector</strong></span>.fail(input);                      <span style="color:#808080;"><em>// Settings | File Templates.
</em></span><span style="color:#808080;"><em>        </em></span>}
        <span style="color:#660e7a;"><strong>collector</strong></span>.ack(input);
    }
}
</pre>
   
<p></p>
<p><br></p>
<p>（4）打包成 jar 后 放置于 storm服务器。</p>
<p></p>
<p><span style="font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace;font-size:15px;">bin/storm jar storm-demo-1.0-SNAPSHOT.jar com.whl.demo.bolts.KafkaTopology</span><br></p>
<p><span style="font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace;font-size:15px;">注意坑点：将maven下的jar 放置于 storm 的lib目录下。运行后有错误，根据提示来选择。</span></p>
<p><span style="font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace;font-size:15px;"><br></span></p>
<p><span style="font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace;font-size:15px;">（5）运行第一个项目，将在控制台能看到相应的输出结果。</span></p>
<div><span style="font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace;font-size:15px;"><br></span></div>
<br><p><br></p>
<p><br></p>
            </div>
                </div>