---
layout:     post
title:      flume+kafka+zookeeper 单机实现实时数据的获取
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span></span>之前在做大数据的时候，一直不知道数据是怎么上传到hdfs的，问了架构师用flume，自己也一直想玩一下flume，无奈没太多的时间，今天有点时间，就查找资料，搭建了一个单机环境下的日志监控。所有资料全部来源与网络，我只是做了一个简单的整合。</p>
<p><span></span>首先，第一步安装flume。</p>
<p><span></span>1.安装flume，首先要安装好jvm。</p>
<p><span></span>2.下载flume。地址 <a href="http://mirror.bit.edu.cn/apache/flume/1.7.0/apache-flume-1.7.0-bin.tar.gz" rel="nofollow">http://mirror.bit.edu.cn/apache/flume/1.7.0/apache-flume-1.7.0-bin.tar.gz</a></p>
<p><span></span>3.解压项目，进入conf下面，默认的配置文件带一个后缀是.template,去掉这个后缀。</p>
<p><span></span>4.修改flume-env.sh 设置jdk的安装目录，</p>
<p><span></span>5.校验flume是否安装成功，可以进入bin目录下，输入：</p>
<p></p>
<pre><code class="language-java">flume-ng version</code></pre><br><br><p></p>
<span></span>查看是否输出版本信息，即为以成功。
<p></p>
<p><span></span>6.测试flume的功能，修改配置文件：flume-conf.properties</p>
<p></p>
<pre><code class="language-java">       #Spool监测配置的目录下新增的文件，并将文件中的数据读取出来。需要注意两点：
　　　　#1) 拷贝到spool目录下的文件不可以再打开编辑。
　　　　#2) spool目录下不可包含相应的子目录</code></pre>
<p></p>
<pre><code class="language-java">a1.sources = r1
a1.sinks = k1
a1.channels = c1

# Describe/configure the source
a1.sources.r1.type = spooldir
a1.sources.r1.channels = c1
a1.sources.r1.spoolDir = /Users/haomaiche/Downloads/apache-flume-1.7.0-bin/logs
a1.sources.r1.fileHeader = true

# Describe the sink
a1.sinks.k1.type = logger

# Use a channel which buffers events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1



# logser可以看做是flume服务的名称，每个flume都由sources、channels和sinks三部分组成
# sources可以看做是数据源头、channels是中间转存的渠道、sinks是数据后面的去向
logser.sources = src_launcherclick
logser.sinks = kfk_launcherclick
logser.channels = ch_launcherclick

# source
# 源头类型是TAILDIR，就可以实时监控以追加形式写入文件的日志
logser.sources.src_launcherclick.type = TAILDIR
# positionFile记录所有监控的文件信息
logser.sources.src_launcherclick.positionFile = /Users/haomaiche/Downloads/apache-flume-1.7.0-bin/log1/taildir_position.json
# 监控的文件组
logser.sources.src_launcherclick.filegroups = f1
# 文件组包含的具体文件，也就是我们监控的文件
logser.sources.src_launcherclick.filegroups.f1 = /Users/haomaiche/Downloads/apache-flume-1.7.0-bin/log/.*

# interceptor
# 写kafka的topic即可
logser.sources.src_launcherclick.interceptors = i1 i2
logser.sources.src_launcherclick.interceptors.i1.type=static
logser.sources.src_launcherclick.interceptors.i1.key = type
logser.sources.src_launcherclick.interceptors.i1.value = launcher_click
logser.sources.src_launcherclick.interceptors.i2.type=static
logser.sources.src_launcherclick.interceptors.i2.key = topic
logser.sources.src_launcherclick.interceptors.i2.value = launcher_click

# channel
logser.channels.ch_launcherclick.type = memory
logser.channels.ch_launcherclick.capacity = 10000
logser.channels.ch_launcherclick.transactionCapacity = 1000

# kfk sink
# 指定sink类型是Kafka，说明日志最后要发送到Kafka
logser.sinks.kfk_launcherclick.type = org.apache.flume.sink.kafka.KafkaSink
# Kafka broker
logser.sinks.kfk_launcherclick.brokerList = 10.0.5.203:9092

# Bind the source and sink to the channel
logser.sources.src_launcherclick.channels = ch_launcherclick
logser.sinks.kfk_launcherclick.channel = ch_launcherclick</code></pre>
<p></p>
<p><br></p>
<p>安装kafka：(zookeeper的安装不再写了，也很简单，网上都是例子：<a href="http://blog.csdn.net/wo541075754/article/details/56483533" rel="nofollow">http://blog.csdn.net/wo541075754/article/details/56483533</a>)</p>
<p><span></span>1.下载kafka。地址：<a href="http://mirrors.cnnic.cn/apache/kafka/0.9.0.0/kafka_2.10-0.9.0.0.tgz" rel="nofollow">http://mirrors.cnnic.cn/apache/kafka/0.9.0.0/kafka_2.10-0.9.0.0.tgz</a></p>
<p><span></span>2.解压，之后可以直接启动：</p>
<pre><code class="language-html">     bin/kafka-server-start.sh config/server.properties &amp;</code></pre>结下来整合：
<p></p>
<p><span></span>1.启动flume：</p>
<pre><code class="language-html">./flume-ng agent -c . -f ../conf/flume-conf.properties -n logser -Dflume.root.logger=INFO</code></pre><span></span>2.重新打开一个窗口，启动kafka的消费模式，监听：<span style="background-color:rgb(240,240,240);">launcher_click 主题。命令：</span>
<p></p>
<p><span style="background-color:rgb(240,240,240);"></span></p>
<pre><code class="language-html">bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic launcher_click --from-beginning</code></pre>
<p></p>
<p><span></span>3.新建一个窗口：对</p>
<pre><code class="language-java">    /Users/haomaiche/Downloads/apache-flume-1.7.0-bin/log这个目录下的文件进行追加，命令：</code></pre><pre><code class="language-java"></code></pre><pre class="java">echo "spool test2s nihaoi www.baidu.com" &gt;&gt; /Users/haomaiche/Downloads/apache-flume-1.7.0-bin/log/spool_text1sd.log
这个时候，在kafka的监听窗口会打印：
$bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic launcher_click --from-beginning
Using the ConsoleConsumer with old consumer is deprecated and will be removed in a future major release. Consider using the new consumer by passing [bootstrap-server] instead of [zookeeper].
spool test2s
spool test2s nihaoi www
spool test2s nihaoi www.baidu.com
下面是用java监听输出数据：
	1.maven
&lt;dependency&gt;
    &lt;groupId&gt;org.apache.kafka&lt;/groupId&gt;
    &lt;artifactId&gt;kafka-clients&lt;/artifactId&gt;
    &lt;version&gt;0.9.0.1&lt;/version&gt;
&lt;/dependency&gt;

	2.代码：
	
import org.apache.kafka.clients.consumer.ConsumerRecord;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.KafkaConsumer;

import java.util.Arrays;
import java.util.Properties;

public class KafkaConsumerExample {
    public static void main(String[] args) {
        Properties props = new Properties();
        props.put("bootstrap.servers", "localhost:9092");
        props.put("group.id", "test");
        props.put("enable.auto.commit", "true");
        props.put("auto.commit.interval.ms", "1000");
        props.put("session.timeout.ms", "30000");
        props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
        KafkaConsumer&lt;String, String&gt; consumer = new KafkaConsumer&lt;String, String&gt;(props);
        consumer.subscribe(Arrays.asList("launcher_click"));
        while (true) {
            ConsumerRecords&lt;String, String&gt; records = consumer.poll(100);
            for (ConsumerRecord&lt;String, String&gt; record : records)
                System.out.println("内容是："+ record.value());
        }
    }
}</pre><pre><code class="language-plain"><span style="font-size:18px;">参考资料：
Flume1.5.0入门：安装、部署、及flume的案例：<a href="http://www.aboutyun.com/thread-8917-1-1.html" rel="nofollow">http://www.aboutyun.com/thread-8917-1-1.html</a>
kafka的安装  <a href="http://www.cnblogs.com/wangyangliuping/p/5546465.html" rel="nofollow">http://www.cnblogs.com/wangyangliuping/p/5546465.html</a>
flume实时日志分析  <a href="http://itindex.net/detail/56956-flume-kafka-sparkstreaming" rel="nofollow">http://itindex.net/detail/56956-flume-kafka-sparkstreaming</a>
kafka的java代码来源： <a href="http://blog.csdn.net/lnho2015/article/details/51353936" rel="nofollow">http://blog.csdn.net/lnho2015/article/details/51353936</a></span></code></pre>
<pre></pre>
<pre><code class="language-java"></code></pre><pre class="java"></pre><pre><code class="language-java"></code></pre><pre class="java"></pre><pre><code class="language-java"></code></pre><pre class="java"></pre><pre></pre>
<pre></pre>
<pre></pre>
<pre></pre>
<pre></pre>
<pre></pre>
<pre></pre>
<pre></pre>
<pre></pre>
            </div>
                </div>