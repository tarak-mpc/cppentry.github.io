---
layout:     post
title:      使用flume将kafka数据sink到HBase
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1 id="toc_0">1. hbase sink介绍</h1>

<p>如果还不了解flume请查看我写的其他<a href="http://kaimingwan.com/category/flume" rel="nofollow">flume</a>下的博客。</p>

<p>接下来的内容主要来自<a href="http://flume.apache.org/FlumeUserGuide.html" rel="nofollow">flume官方文档</a>的学习。</p>

<p>顺便也强烈推荐<a href="http://flume.apache.org/releases/content/1.6.0/apidocs/" rel="nofollow">flume 1.6 官方API</a></p>

<p>hbase的sink主要有以下两种。两种方式都提供和HBASE一样的一致性保证，即行级原子性</p>

<h2 id="toc_1">1.1 HbaseSink</h2>

<p>agent的配置时提供两种序列化模式：</p>

<ol><li>SimpleHbaseEventSerializer: 将整个事件body部分当做完整的一列写入hbase</li>
	<li>RegexHbaseEventSerializer: 根据正则表达式将event body拆分到不同的列当中</li>
</ol><p>优点：<br>
安全性较高：支持往secure hbase写数据（hbase可以开启kerberos校验）</p>

<p>缺点：<br>
性能没有后面的那种AsyncHBaseSink高</p>

<h2 id="toc_2">1.2 AsyncHbaseSink</h2>

<p>异步的Sink，可见速度是比前者快的，但是不支持往Secure Hbase写数据。</p>

<p>采用的序列化器是：SimpleAsyncHbaseEventSerializer，也支持将event body分割成多个列，插入到对应KEY的ROW里</p>

<h1 id="toc_3">2. 配置flume</h1>

<p>我们这里hbase没有开启安全相关选项，一般这集群也主要在内网环境。所以我们这里采用AsyncHbaseSink来进行本次操作。source则为kafka。</p>

<p>channel我们也选用kafka channel。之所以选择kafka channel的依据可以参考<a href="http://kaimingwan.com/post/flume/flumezhong-ge-lei-channelfen-xi-dui-bi" rel="nofollow">flume中各类channel分析对比</a></p>

<p>配置文件如下：</p>

<pre>
# ------------------- 定义数据流----------------------
# source的名字
agent.sources = kafkaSource
# channels的名字，建议按照type来命名
agent.channels = kafkaChannel
# sink的名字，建议按照目标来命名
agent.sinks = hbaseSink


# ---------------------定义source和sink的绑定关系----------------

# 指定source使用的channel名字
agent.sources.kafkaSource.channels = kafkaChannel
# 指定sink需要使用的channel的名字,注意这里是channel
agent.sinks.hbaseSink.channel = kafkaChannel

#-------- kafkaSource相关配置-----------------
# 定义消息源类型
agent.sources.kafkaSource.type = org.apache.flume.source.kafka.KafkaSource
# 定义kafka所在zk的地址
agent.sources.kafkaSource.zookeeperConnect = 10.45.9.139:2181
# 配置消费的kafka topic
agent.sources.kafkaSource.topic = my-topic-test
# 配置消费者组的id
agent.sources.kafkaSource.groupId = flume
# 消费超时时间,参照如下写法可以配置其他所有kafka的consumer选项。注意格式从kafka.xxx开始是consumer的配置属性
agent.sources.kafkaSource.kafka.consumer.timeout.ms = 100



#------- kafkaChannel相关配置-------------------------
# channel类型
agent.channels.kafkaChannel.type = org.aprache.flume.channel.kafka.KafkaChannel
# channel存储的事件容量,即队列长度
agent.channels.kafkaChannel.capacity=10000
# 事务容量
agent.channels.kafkaChannel.transactionCapacity=1000
# kafka broker list
agent.channels.kafkaChannel.brokerList=mysql1:9092,mysql4:9092
# 指定topic
agent.channels.topic=flume
# 指定zk地址
agent.channels.kafkaChannel.zookeeperConnect=10.45.9.139:2181
# 指定producer的选项,关键是指定acks的值，保证消息发送的可靠性，retries采用默认的3
agent.channels.kafkaChannel.kafka.producer.acks=all


#---------hbaseSink 相关配置------------------
# 指定sink类型。PS：如果使用RegexHbaseEventSerializer只能使用hbase类型
# agent.sinks.hbaseSink.type = hbase
agent.sinks.hbaseSink.type = asynchbase
# 指定hbase中的表名
agent.sinks.hbaseSink.table = student
# 指明column family
agent.sinks.hbaseSink.columnFamily= info
# 使用的serializer
agent.sinks.hbaseSink.serializer=org.apache.flume.sink.hbase.SimpleAsyncHbaseEventSerializer
# 如果需要使用正则处理value可以使用以下的serializer
#agent.sinks.hbaseSink.serializer=org.apache.flume.sink.hbase.RegexHbaseEventSerializer
# 指定正则表达式,这里用的正则是匹配逗号分隔的字符串
#agent.sinks.hbaseSink.serializer.regex= ^([^,]+),([^,]+),([^,]+),([^,]+)$
# 指定在列族中对应的的colName
# agent.sinks.hbaseSink.serializer.colNames=c1,c2,c3


# 指定hbase所用的zk集合
agent.sinks.hbaseSink.zookeeperQuorum = mysql3:2181,mysql4:2181,mysql5:2181
</pre>

<h1 id="toc_4">3. 运行测试flume</h1>

<p>在$FLUME_HOME/bin下执行以下命令运行。后台会开启一个Application 的Java进程</p>

<pre class="has">
<code>nohup sh flume-ng agent --conf-file ../conf/flume-conf.properties --name agent -Dflume.root.logger=INFO,console &amp;</code></pre>

<p>然后写个kafka的producer往我们前面定义的my-topic-test中写消息。<br>
producer的代码如下：</p>

<pre>
import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.Producer;
import org.apache.kafka.clients.producer.ProducerRecord;

import java.util.Properties;

/**
 * @author Wan Kaiming on 2016/8/1
 * @version 1.0
 */
public class MyProducer {
    public static void main(String[] args) {
        Properties props = new Properties();
        //broker地址 这里用域名，记得修改本地的hosts文件
        props.put("bootstrap.servers", "mysql1:9092,mysql4:9092");
        //消息可靠性语义
        props.put("acks", "all");
        //请求broker失败进行重试的次数，避免由于请求broker失败造成的消息重复
        props.put("retries", 0);
        //按批发送，每批的消息数量
        props.put("batch.size", 16384);
        //防止来不及发送，延迟一点点时间，使得能够批量发送消息
        props.put("linger.ms", 1);
        //缓冲大小，bytes
        props.put("buffer.memory", 33554432);
        //key的序列化类
        props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        //value的序列化类
        props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");

        //创建一个Producer对象，加载配置上下文信息
        Producer&lt;String, String&gt; producer = new KafkaProducer&lt;String,String&gt;(props);

        for(int i=0;i&lt;6;i++){
            producer.send(new ProducerRecord&lt;String, String&gt;("my-topic-test", "hello", "world"));
        }

//        while(true){
//            //调用send方法进行发送。send方法将消息加到缓存，异步发送
//            producer.send(new ProducerRecord&lt;String, String&gt;("my-topic", "hello", "world"));
//        }

        producer.close();
    }
}
</pre>

<p>运行完毕后可以去hbase shell中检查下：</p>

<p><img alt="" class="has" src="http://kaimingwan.com/flume/_image/%E4%BD%BF%E7%94%A8flume%E5%B0%86kafka%E6%95%B0%E6%8D%AEsink%E5%88%B0hbase/16-37-46.jpg"></p>

<p>总结：可见key全部是由flume自动生成的。发送给kafka的 value值"world"全部成功保存到HBASE</p>

<p>PS：</p>

<ol><li>最后一行多出来的incRow是Flume的SimpleAsyncHbaseEventSerializer中使用的。用来统计行数的，每次都在最后一行,效果就是一个计数的count。</li>
	<li>这里产生的行的名字是pCol和iCol都是SimpleAsyncHbaseEventSerializer的默认值，其实可以自定义指定</li>
</ol><p>总结：可见，如果需要更加自由的对写入HBASE的数据做自定义，建议需要了解下这个Event序列化类的源码,然后可以自定义序列化类</p>

<h1 id="toc_5">4. 使用RegexHbaseEventSerializer来处理些HBASE的值</h1>

<p>重要提示：使用RegexHbaseEventSerializer不支持最新的hbase1.2.2。请换成<a href="https://mirrors.tuna.tsinghua.edu.cn/apache/hbase/0.98.20/" rel="nofollow">hbase-0.98.20-hadoop2-bin.tar.gz</a>，否则可能会报错找不到类。该BUG说明见：<a href="https://issues.apache.org/jira/browse/FLUME-2794" rel="nofollow">Flume 1.6 HBase 1.12 java.lang.NoSuchMethodError</a></p>

<ol><li>修改flume的配置文件，改用RegexHbaseEventSerializer，我使用的配置文件如下</li>
</ol><pre>
# ------------------- 定义数据流----------------------
# source的名字
agent.sources = kafkaSource
# channels的名字，建议按照type来命名
agent.channels = kafkaChannel
# sink的名字，建议按照目标来命名
agent.sinks = hbaseSink


# ---------------------定义source和sink的绑定关系----------------

# 指定source使用的channel名字
agent.sources.kafkaSource.channels = kafkaChannel
# 指定sink需要使用的channel的名字,注意这里是channel
agent.sinks.hbaseSink.channel = kafkaChannel

#-------- kafkaSource相关配置-----------------
# 定义消息源类型
agent.sources.kafkaSource.type = org.apache.flume.source.kafka.KafkaSource
# 定义kafka所在zk的地址
agent.sources.kafkaSource.zookeeperConnect = 10.45.9.139:2181
# 配置消费的kafka topic
agent.sources.kafkaSource.topic = my-topic-regex
# 配置消费者组的id
agent.sources.kafkaSource.groupId = flume
# 消费超时时间,参照如下写法可以配置其他所有kafka的consumer选项。注意格式从kafka.xxx开始是consumer的配置属性
agent.sources.kafkaSource.kafka.consumer.timeout.ms = 100
#------- kafkaChannel相关配置-------------------------
# channel类型
agent.channels.kafkaChannel.type = org.apache.flume.channel.kafka.KafkaChannel
# channel存储的事件容量,即队列长度
agent.channels.kafkaChannel.capacity=10000
# 事务容量
agent.channels.kafkaChannel.transactionCapacity=1000
# kafka broker list
agent.channels.kafkaChannel.brokerList=mysql1:9092,mysql4:9092
# 指定topic
agent.channels.kafkaChannel.topic=flume-regex-channel
# 指定zk地址
agent.channels.kafkaChannel.zookeeperConnect=10.45.9.139:2181
# 指定producer的选项,关键是指定acks的值，保证消息发送的可靠性，retries采用默认的3
# agent.channels.kafkaChannel.kafka.producer.acks=all

#---------hbaseSink 相关配置------------------
# 指定sink类型
# agent.sinks.hbaseSink.type = asynchbase
agent.sinks.hbaseSink.type = hbase
# 指定hbase中的表名
agent.sinks.hbaseSink.table = student
# 指明column family
agent.sinks.hbaseSink.columnFamily = info
# 使用的serializer
# agent.sinks.hbaseSink.serializer=org.apache.flume.sink.hbase.SimpleAsyncHbaseEventSerializer

# 如果需要使用正则处理value可以使用以下的serializer
agent.sinks.hbaseSink.serializer= org.apache.flume.sink.hbase.RegexHbaseEventSerializer
# 指定某一列来当主键，而不是用随机生成的key
# agent.sinks.hbaseSink.serializer.rowKeyIndex = 0
# 指定正则表达式,这里用的正则是匹配逗号分隔的字符串
agent.sinks.hbaseSink.serializer.regex=^([^,]+),([^,]+),([^,]+),([^,]+)$
# 指定在列族中对应的的colName
agent.sinks.hbaseSink.serializer.colNames=c1,c2,c3,c4


# 指定hbase所用的zk集合
agent.sinks.hbaseSink.zookeeperQuorum = mysql3:2181,mysql4:2181,mysql5:2181
</pre>

<ol><li>修改producer文件，将value值发送"one,two,three,four"可以匹配正则。执行结果如下图：</li>
</ol><p><img alt="" class="has" src="http://kaimingwan.com/flume/_image/%E4%BD%BF%E7%94%A8flume%E5%B0%86kafka%E6%95%B0%E6%8D%AEsink%E5%88%B0hbase/09-58-27.jpg"></p>

<p>PS：建议把lfume默认的JVM大小改大点，并且开启JMX方便监控JVM</p>

<pre>
vi $FLUME_HOME/bin/flume-ng
# set default params
# 若干内容...
JAVA_OPTS="-Xmx1500m -Dcom.sun.management.jmxremote.port=9999 -Dcom.sun.management.jmxremote.authenticate=false -Dcom.sun.management.jmxremote.ssl=false "
LD_LIBRARY_PATH=""
# 若干内容...
</pre>

<h1 id="toc_6">5. 效率测试</h1>

<p>测试按照第四节的配置来进行。生产者代码如下：</p>

<pre>
import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.Producer;
import org.apache.kafka.clients.producer.ProducerRecord;

import java.util.Date;
import java.util.Properties;

/**
 * @author Wan Kaiming on 2016/8/1
 * @version 1.0
 */
public class MyProducer {
    public static void main(String[] args) {

        //统计时间
        System.out.println("程序开始时间戳信息："+new Date());
        final long startTime=System.currentTimeMillis();

        Properties props = new Properties();
        //broker地址 这里用域名，记得修改本地的hosts文件
        props.put("bootstrap.servers", "mysql1:9092,mysql4:9092");
        //消息可靠性语义
        props.put("acks", "all");
        //请求broker失败进行重试的次数，避免由于请求broker失败造成的消息重复
        props.put("retries", 3);
        //按批发送，每批的消息数量
        //props.put("batch.size", 16384);
        props.put("batch.size", 16384);
        //防止来不及发送，延迟一点点时间，使得能够批量发送消息
        props.put("linger.ms", 1);
        //缓冲大小，bytes
        props.put("buffer.memory", 33554432);
        //key的序列化类
        props.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");
        //value的序列化类
        props.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");

        //创建一个Producer对象，加载配置上下文信息
        Producer&lt;String, String&gt; producer = new KafkaProducer&lt;String,String&gt;(props);

        for(int i=0;i&lt;1000000;i++){
            producer.send(new ProducerRecord&lt;String, String&gt;("my-topic-regex", Integer.toString(i), "one,two,three,four"));
        }

        producer.close();

        final long endTime=System.currentTimeMillis();
        float excTime=(float)(endTime-startTime)/1000;
        System.out.println("执行时间："+excTime+"s");
        System.out.println("当前时间为："+ new Date());
    }
}
</pre>

<p>测试基本信息：</p>

<table><thead><tr><th>名称</th>
			<th>信息</th>
		</tr></thead><tbody><tr><td>PC硬件信息</td>
			<td>Intel(R) Xeon(R) CPU E7- 4830 @ 2.13GHz，内存4G</td>
		</tr><tr><td>JAVA</td>
			<td>JDK1.8</td>
		</tr><tr><td>KAFKA</td>
			<td>2台broker(heap size =1200M),1个topic,5个分区，2个复制分区，acks=all</td>
		</tr><tr><td>flume</td>
			<td>1.60版本，heap size=1.5G,kafkachannel,kafka source,hbase sink。</td>
		</tr><tr><td>负载信息</td>
			<td>100万条消息，每条消息20byte的样子</td>
		</tr><tr><td>hbase</td>
			<td>1台master,3台slave,其中1台slave和master在一台机器，版本0.98-hadoop2</td>
		</tr></tbody></table><p>测试结果1（kafkachannel=0）:</p>

<ol><li>kafka发送消息时间：6.912s</li>
	<li>hbase接受完全部消息：4分33s</li>
	<li>延迟时间：4分26s</li>
</ol><p>测试结果2(kafkachannel=all):</p>

<ol><li>kafka发送消息时间：8.25s</li>
	<li>hbase接受完全部消息：4分59s</li>
	<li>延迟时间：4分51s</li>
</ol><p>PS: 测试会存在一定误差。因为读取hbase的时候是按照1000条的批大小批量读取的，count完整个HBASE的记录本身也会花很多时间。也就是意味着，实际的延迟时间肯定比我测试的要小。测试1的时候，32.8万条消息，花费时间约为120s，得到吞吐量TPS=2733。该值基本比较准确。</p>

<p>综上，在保证消息可靠性前提下，kafka消息通过flume写hbase的吞吐量TPS基本在3K左右这个数量级。相信经过更多的配置优化、硬件性能提升、增大JVM堆等方式，提升TPS不是问题。</p>            </div>
                </div>