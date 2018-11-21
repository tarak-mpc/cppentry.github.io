---
layout:     post
title:      kafka介绍，安装以及简单的java调用kafka代码
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><br></p>
<p>Producer ：消息生产者，向broker发消息的客户端。<br>
Consumer ：消息消费者，向broker取消息的客户端<br>
Topic ：一个队列，主题。</p>
<p><span>Message</span>：消息是<span>kafka</span>处理的对象，在<span>kafka</span>中，消息是被发布到<span>broker</span>的<span>topic</span>中。而<span>consumer</span>也是从相应的<span>topic</span>中拿数据。也就是说，<span>message</span>是按<span>topic</span>存储的<br>
Consumer Group ：将topic消息的广播发给consumer的手段。一个topic可以有多个CG。<br>
Broker ：一台kafka服务器就是一个broker。一个集群由多个broker组成。一个broker可以容纳多个topic。<br>
Partition：为了实现扩展性，一个非常大的topic可以分布到多个broker（即服务器）上，一个topic可以分为多个partition，每个partition是一个有序的队列。partition中的每条消息都会被分配一个有序的id（offset）。kafka只保证按一个partition中的顺序将消息发给consumer，不保证一个topic的整体（多个partition间）的顺序。<br>
Offset：kafka的存储文件都是按照offset.kafka来命名，用offset做名字的好处是方便查找。例如你想找位于2049的位置，只要找到2048.kafka的文件即可。当然the first offset就是00000000000.kafka<br></p>
<p><br></p>
<p><strong><span style="font-size:24px;">安装过程</span></strong>：</p>
<p>（1）下载解压。官网下载kafka，http://kafka.apache.org/  <br></p>
<p>解压到安装目录下 tar -xcvf </p>
<p>（2）修改配置文件/usr/local/kafka/config/server.properties，修改如下内容</p>
<p>broker.id=0<br></p>
<p>host.name=hadoop1</p>
<p>zookeeper.connect=hadoop1:2181,hadoop2:2181,hadoop3:2181,hadoop4:2181,hadoop5:2181,hadoop6:2181</p>
<p>（3）修改完配置文件即可将整个文件夹传输到其他节点  scp -r 。。。。</p>
<p>（4）传输完之后修改每个节点的broker.id的编号，递增。</p>
<p>（5）启动zookeeper。</p>
<p>这边可以使用kafka自带的zookeeper，也可以使用自己安装的zookeeper。<br></p>
<p>启动自己安装的zookeeper  ：   /app/zookeeper-3.4.6/bin/zkServer.sh start</p>
<p>各个节点均启动完成之后，可以查看zk的状态  /app/zookeeper-3.4.6/bin/zkServer.sh status<br></p>
<p>（6）启动kafka<br></p>
<p>/app/kafka_2.9.2-0.8.2.1/bin/kafka-server-start.sh /app/kafka_2.9.2-0.8.2.1/config/server.properties &amp;</p>
<p>尾部加上&amp;的作用是可以启动完之后直接按回车退出，继续下一步操作。也可不加&amp;</p>
<p>（7）创建topic</p>
<p>/app/kafka_2.9.2-0.8.2.1/bin/kafka-topics.sh --create --topic idoall_testTopic --replication-factor 6 --partitions 2 --zookeeper hadoop1:2181</p>
<p><br>
可通过指令查看所有的topic     /app/kafka_2.9.2-0.8.2.1/bin/kafka-topics.sh --list --zookeeper hadoop2:2181</p>
<p>（8）发送消息<br>
/app/kafka_2.9.2-0.8.2.1/bin/kafka-console-producer.sh --broker-list hadoop3:9092 --sync --topic idoall_testTopic<br></p>
<p>（9）消费消息。重新打开一个终端执行指令<br></p>
<p>/app/kafka_2.9.2-0.8.2.1/bin/kafka-console-consumer.sh --zookeeper hadoop1:2181 --topic idoall_testTopic --from-beginning<br>
在原终端中输入消息，新终端中会显示出输入的消息。<br><br>
关闭kafka指令。  /app/kafka_2.9.2-0.8.2.1/bin/kafka-server-stop.sh /app/kafka_2.9.2-0.8.2.1/config/server.properties &amp;<br><br><strong><span style="font-size:24px;">java调用kafka</span></strong></p>
<p>首先创建一个java project，网上很多说需创建maven工程，本人经过测验，发现maven工程和普通的java project均可调用。</p>
<p>需要注意，工程所采用的jar包，可以在相应版本的kafka安装文件夹的lib目录下引用，不同版本的jar包可能不通用，</p>
<p>如果出现java.lang.NoClassDefFoundError: scala/reflect/ClassManifest的报错，可能是由于jar包不匹配引起的。</p>
<p><br></p>
<p><strong>Producer端代码：</strong></p>
<p>package com;<br><br><br>
import java.util.Date;<br>
import java.util.Properties;<br>
import java.text.SimpleDateFormat;   <br><br>
import kafka.javaapi.producer.Producer;<br>
import kafka.producer.KeyedMessage;<br>
import kafka.producer.ProducerConfig;<br><br><br>
public class Producertest {<br>
     <br>
     public static void main(String[] args) {<br>
         Properties props = new Properties();<br>
         props.put("zk.connect", "hadoop1:2181/kafka,hadoop2:2181/kafka,hadoop3:2181/kafka,hadoop4:2181/kafka,hadoop5:2181/kafka,hadoop6:2181/kafka");<br>
         // serializer.class为消息的序列化类<br>
         props.put("serializer.class", "kafka.serializer.StringEncoder");<br>
         // 配置metadata.broker.list, 为了高可用, 最好配两个broker实例<br>
         props.put("metadata.broker.list", "hadoop1:9092,hadoop2:9092,hadoop3:9092");<br>
         // 设置Partition类, 对队列进行合理的划分<br>
         //props.put("partitioner.class", "idoall.testkafka.Partitionertest");<br>
         // ACK机制, 消息发送需要kafka服务端确认<br>
         props.put("request.required.acks", "1");<br><br>
          props.put("num.partitions", "6");<br>
         ProducerConfig config = new ProducerConfig(props);<br>
         Producer&lt;String, String&gt; producer = new Producer&lt;String, String&gt;(config);<br>
         for (int i = 0; i &lt; 10; i++)<br>
         {<br>
           // KeyedMessage&lt;K, V&gt;<br>
           // 　　K对应Partition Key的类型<br>
           // 　　V对应消息本身的类型<br>
//　　 topic: "test", key: "key", message: "message"<br>
           SimpleDateFormat formatter = new SimpleDateFormat   ("yyyy年MM月dd日 HH:mm:ss SSS");      <br>
           Date curDate = new Date(System.currentTimeMillis());//获取当前时间      <br>
           String str = formatter.format(curDate);   <br>
            <br>
           String msg = "idoall.org" + i+"="+str;<br>
           String key = i+"";<br>
           producer.send(new KeyedMessage&lt;String, String&gt;("idoall_testTopic",key, msg));<br>
         }<br>
       }<br>
}<br></p>
<p><strong>Consumer端代码：</strong></p>
<p>package com;<br>
import java.util.HashMap;<br>
import java.util.List;  <br>
import java.util.Map;  <br>
import java.util.Properties;  <br>
   <br>
import kafka.consumer.ConsumerConfig;  <br>
import kafka.consumer.ConsumerIterator;  <br>
import kafka.consumer.KafkaStream;  <br>
import kafka.javaapi.consumer.ConsumerConnector;<br><br>
public class Consumertest extends Thread{<br>
      <br>
     private final ConsumerConnector consumer;  <br>
    private final String topic;  <br><br>
    public static void main(String[] args) {  <br>
      Consumertest consumerThread = new Consumertest("idoall_testTopic");  <br>
        consumerThread.start();  <br>
    }  <br>
    public Consumertest(String topic) {  <br>
        consumer =kafka.consumer.Consumer.createJavaConsumerConnector(createConsumerConfig());  <br>
        this.topic =topic;  <br>
    }  <br><br>
private static ConsumerConfig createConsumerConfig() {  <br>
    Properties props = new Properties();  <br>
    // 设置zookeeper的链接地址<br>
    props.put("zookeeper.connect","hadoop1,hadoop2,hadoop3,hadoop4,hadoop5,hadoop6:2181");  <br>
    // 设置group id<br>
    props.put("group.id", "1");  <br>
    // kafka的group 消费记录是保存在zookeeper上的, 但这个信息在zookeeper上不是实时更新的, 需要有个间隔时间更新<br>
    props.put("auto.commit.interval.ms", "1000");<br>
    props.put("zookeeper.session.timeout.ms","10000");  <br>
    return new ConsumerConfig(props);  <br>
}  <br><br>
public void run(){  <br>
     //设置Topic=&gt;Thread Num映射关系, 构建具体的流<br>
    Map&lt;String,Integer&gt; topickMap = new HashMap&lt;String, Integer&gt;();  <br>
    topickMap.put(topic, 1);  <br>
    Map&lt;String, List&lt;KafkaStream&lt;byte[],byte[]&gt;&gt;&gt;  streamMap=consumer.createMessageStreams(topickMap);  <br>
 <br>
    KafkaStream&lt;byte[],byte[]&gt;stream = streamMap.get(topic).get(0);  <br>
    ConsumerIterator&lt;byte[],byte[]&gt; it =stream.iterator();  <br>
    System.out.println("*********Results********");  <br>
    while(it.hasNext()){  <br>
        System.err.println("get data:" +new String(it.next().message()));  <br>
        try {  <br>
            Thread.sleep(1000);  <br>
        } catch (InterruptedException e) {  <br>
            e.printStackTrace();  <br>
        }  <br>
    }  <br>
}  <br>
}<br></p>
<p><br></p>
<p>之后运行代码。</p>
<p>运行Producer端代码，会发现之前的客户端中会接收到代码中发送的消息。</p>
<p>运行consumer端代码，在终端中输入消息，eclipse中会读取到发送的消息，打印出来。</p>
<p>至此，简单的java调用kafka操作完成。<br></p>
<p>如果将Kafka在zookeeper的默认目录，修改为自定义目录时，在运行过程中会报出java.lang.IllegalArgumentException: Path length must be &gt; 0”错误</p>
<p>网上找了好久，发现别人说这是一个Bug，由于initZk()方法没有对路径进行处理导致</p>
<p>原代码：</p>
<div class="cnblogs_code">
<pre><span style="color:#0000ff;">private</span> def initZk(): ZkClient =<span style="color:#000000;"> {
    info(</span>"Connecting to zookeeper on " +<span style="color:#000000;"> config.zkConnect)
    val zkClient </span>= <span style="color:#0000ff;">new</span><span style="color:#000000;"> ZkClient(config.zkConnect, config.zkSessionTimeoutMs, config.zkConnectionTimeoutMs, ZKStringSerializer)
    ZkUtils.setupCommonPaths(zkClient)
    zkClient
}</span></pre>
</div>
<p> </p>
<p><span style="line-height:1.5;">解决代码：</span></p>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><br></span></div>
<pre><span style="color:#0000ff;">private</span> def initZk(): ZkClient =<span style="color:#000000;"> {
    info(</span>"Connecting to zookeeper on " +<span style="color:#000000;"> config.zkConnect)
    val chroot </span>=<span style="color:#000000;"> {
      </span><span style="color:#0000ff;">if</span> (config.zkConnect.indexOf("/") &gt; 0<span style="color:#000000;">)
        config.zkConnect.substring(config.zkConnect.indexOf(</span>"/"<span style="color:#000000;">))
      </span><span style="color:#0000ff;">else</span>
        ""<span style="color:#000000;">
    }
    </span><span style="color:#0000ff;">if</span> (chroot.length &gt; 1<span style="color:#000000;">) {
      val zkConnForChrootCreation </span>= config.zkConnect.substring(0, config.zkConnect.indexOf("/"<span style="color:#000000;">))
      val zkClientForChrootCreation </span>= <span style="color:#0000ff;">new</span><span style="color:#000000;"> ZkClient(zkConnForChrootCreation, config.zkSessionTimeoutMs, config.zkConnectionTimeoutMs, ZKStringSerializer)
      ZkUtils.makeSurePersistentPathExists(zkClientForChrootCreation, chroot)
      info(</span>"Created zookeeper path " +<span style="color:#000000;"> chroot)
      zkClientForChrootCreation.close()
    }
    val zkClient </span>= <span style="color:#0000ff;">new</span><span style="color:#000000;"> ZkClient(config.zkConnect, config.zkSessionTimeoutMs, config.zkConnectionTimeoutMs, ZKStringSerializer)
    ZkUtils.setupCommonPaths(zkClient)
    zkClient
}</span></pre>
<br><p>如果无法修改，那么可以将自定义目录修改成原来的默认目录，则不会报错。<br></p>
<p><br></p>
<p><br></p>
<p><span style="font-family:Arial;font-size:12px;color:#333333;line-height:26px;">附录网上找到的关于server.properties中所有配置参数的说明</span> http://www.inter12.org/archives/842<br><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
            </div>
                </div>