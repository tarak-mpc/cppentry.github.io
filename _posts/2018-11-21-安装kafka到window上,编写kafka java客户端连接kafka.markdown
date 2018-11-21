---
layout:     post
title:      安装kafka到window上,编写kafka java客户端连接kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/linsongbin1/article/details/48022941				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-family:SimSun;"><span style="font-size:18px;">最近想测试一下kafka的性能，折腾了大半天才把kafka安装到window上。下文提供安装的整个过程，绝对是可用的完整的，同时提供完整的kafka java客户端代码，用于与kafka沟通。在这里必须吐槽一下，网上大部分关于如何把kafka安装到window上的文章，要么不完整，要么kafka客户端代码是错误的或者不是基于0.8版本的。但是必须提醒一下，这篇文章只是介绍了其中的一种安装方法，可能不是最简洁的。</span></span></p>
<p><br></p>
<h1><span style="font-size:24px;">1、软件准备</span></h1>
<p></p>
<ul><li><span style="font-size:18px;">kafka_2.9.1-0.8.2.1.tgz，需要到<a href="http://kafka.apache.org/downloads.html" rel="nofollow">kafka官网</a>下载。</span></li><li><span style="font-size:18px;">sbt-0.13.9.msi，需要到<a href="http://www.scala-sbt.org/release/tutorial/Setup.html" rel="nofollow">sbt官网下载</a>下载。</span></li><li><span style="font-size:18px;">git bash 网上大把，我用的是Git-1.9.4-preview20140611.exe</span></li><li><span style="font-size:18px;">Eclipse</span></li><li><span style="font-size:18px;">Maven</span></li><li><span style="font-size:18px;">Java 7</span></li></ul><div><span style="font-size:14px;"><br></span></div>
<h1><span style="font-size:24px;">2、kafka环境设置</span></h1>
<h2><span style="font-size:18px;">2.1  切换到kafka目录</span></h2>
<div><span style="font-size:18px;">先解压kafka_2.9.1-0.8.2.1.tgz，例如解压到D:\sam.lin\software\kafka\kafka_2.9.1-0.8.2.1。使用git bash，切换到kafka目录下。例如：</span></div>
<div><span style="font-size:14px;"><span style="font-size:14px;"><img src="https://img-blog.csdn.net/20150828102220655?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></span></div>
<div><span style="font-size:14px;"><span style="font-size:14px;"><br></span></span></div>
<div><span style="font-size:18px;">kafka需要的一些组件可以使用<span style="font-family:Simsun;">sbt update命令得到，如下：</span></span></div>
<div><span style="font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><img src="" alt=""><br></span></span></div>
<div><span style="font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><img src="https://img-blog.csdn.net/20150828102247846?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></span></div>
<h2><span style="font-family:Simsun;"><span style="font-size:18px;">2.2 <span style="font-family:Simsun;">sbt update</span></span></span></h2>
<div><span style="font-family:Simsun;"><span style="font-size:18px;">敲打回车键后，会出现一个错误信息，"sbt: No such file or directory"，这个时候你需要安装sbt，下载网址我在上面的【软件准备】中已经提供了。<span style="font-family:'宋体';">本文下载的是</span><span lang="en-us" style="font-family:Simsun, serif;" xml:lang="en-us">sbt-0.13.9.msi</span><span style="font-family:'宋体';">这个版本。</span></span></span></div>
<div><span style="font-family:Simsun;"><span style="font-size:18px;"><br></span></span></div>
<div><span style="font-family:Simsun;"><span style="font-size:18px;">sbt update命令会开始下载组件，如果是第一次安装，这个过程比较漫长，耐心等待吧。</span></span></div>
<div><span style="font-family:Simsun;"><span style="font-size:18px;">当出现[<span>success</span>]字样时，说明安装组件成功了。</span></span></div>
<h2><span style="font-family:Simsun;"><span style="font-size:18px;">2.3 sbt package</span></span></h2>
<div><span style="font-family:Simsun;"><span style="font-size:18px;">如下图：</span></span></div>
<div><span style="font-family:Simsun;font-size:14px;"><img src="https://img-blog.csdn.net/20150828102338927?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div><span style="font-family:Simsun;font-size:14px;"><img src="" alt=""><br></span></div>
<div><span style="font-family:Simsun;"><span style="font-size:18px;"><span style="font-family:Simsun;">当出现[</span><span style="font-family:Simsun;">success</span><span style="font-family:Simsun;">]字样时，说明命令执行成功了。</span></span><br></span></div>
<div><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><br></span></span></div>
<h2><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-size:18px;">2.4 sbt sbt-dependency</span></span></span></h2>
<div><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-size:18px;">如下图：</span></span></span></div>
<div><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><img src="https://img-blog.csdn.net/20150828102404945?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></span></div>
<div><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-size:18px;"><img src="" alt=""><br></span></span></span></div>
<div><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-size:18px;">当出现[info] org.scala-sbt:sbt:0.13.8，说明命令执行成功了。</span></span></span></div>
<div><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-size:18px;">到此，kafka环境搭建完毕。接下来需要搭建<span style="font-family:Simsun;">zookeeper.</span></span></span></span></div>
<div><br></div>
<div><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"></span></span></span>
<h1>3、<span style="font-family:Simsun;"><span style="font-size:32px;">zookeeper</span></span>环境设置</h1>
<span style="font-size:18px;">注意，安装<span style="font-family:Simsun;">zookeeper的时候，在window上使用shell来安装是没法成功的，至少我尝试很多次都没成功。其实kafka已经为我们提供了在window安装zookeeper的bat脚本了，这些脚本存放在kafka安装目录中的bin/window上，如下：</span></span></div>
<div><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><br></span></span></span></span></div>
<div><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><img src="" alt=""><img src="https://img-blog.csdn.net/20150828102507790?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></span></span></div>
<div><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><img src="https://img-blog.csdn.net/20150828102518326?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></span></span></div>
<div><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><br></span></span></span></div>
<div><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;">注意在这里不能使用git bash了，原因是git bash执行bat文件时，会报语法错误。我们换成window的cmd命令行。</span></span></span></div>
<div><br></div>
<h2><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-size:18px;">3.1 修改zookeeper和kafka的配置文件</span></span></span></span></h2>
<div><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-size:18px;">  1) 修改config目录下的server.properties文件，修改</span></span></span></span></div>
<div><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-size:18px;">log.dirs=/D/sam.lin/software/kafka/kafka_2.9.1-0.8.2.1/kafka-logs</span></span></span></span></div>
<div><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-size:18px;"><br></span></span></span></span></div>
<div><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-size:18px;">  2) 修改config目录下的log4j.properties文件,修改</span></span></span></span></div>
<div><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-size:18px;">log4j.appender.kafkaAppender.File=/D/sam.lin/software/kafka/kafka_2.9.1-0.8.2.1/logs/server.log<br></span></span></span></span></div>
<div><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-size:18px;"><br></span></span></span></span></div>
<div><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-size:18px;">  3) 修改config目录下的zookeeper.properties文件，加入或者修改</span></span></span></span></div>
<div><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-size:18px;">dataDir=/D/sam.lin/software/kafka/kafka_2.9.1-0.8.2.1/data/zookeeper</span><br></span></span></span></div>
<h2><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-size:18px;">3.2 使用window cmd命令行界面，切换到/bin/window目录</span></span></span></span></h2>
<h2><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-size:18px;">3.3 启动zookeeper</span></span></span></span></h2>
<div><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><img src="" alt=""><img src="https://img-blog.csdn.net/20150828102605945?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></span></span></div>
<div><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><br></span></span></span></div>
<div><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-size:18px;">命令：zookeeper-server-start.bat ../../config/zookeeper.properties<br></span></span></span></span></div>
<div><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-size:18px;">启动成功后，不要关闭这个cmd命令行界面，因为关闭它，zookeeper进程就停了。</span></span></span></span></div>
<div><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><br></span></span></span></div>
<div><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><br></span></span></span></div>
<h1><span style="font-family:Simsun;font-size:24px;"><span style="font-family:Simsun;"><span style="font-family:Simsun;">4、启动kafka broker</span></span></span></h1>
<div><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><img src="https://img-blog.csdn.net/20150828102633990?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></span></span></div>
<div><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-size:18px;">命令：kafka-server-start.bat ../../config/server.properties</span></span></span></span></span></div>
<div><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-size:18px;"><br></span></span></span></span></span></div>
<div><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-size:18px;">启动成功后，不要关闭这个cmd命令行界面，因为关闭它，kafka进程就停了。</span></span><br></span></span></span></span></div>
<h1><span style="font-family:Simsun;font-size:24px;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;">5、创建topic</span></span></span></span></h1>
<div><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><img src="" alt=""><img src="https://img-blog.csdn.net/20150828102719010?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></span></span></span></div>
<div><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-size:18px;">命令：kafka-run-class.bat kafka.admin.TopicCommand --create --zookeeper localhost:2181 --replication-factor
 1 --partitions 1 --topic hellotest</span></span></span></span></span></div>
<div><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-size:18px;"><br></span></span></span></span></span></div>
<div><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-size:18px;">这个命令创建了名字为"hellotest"的topic。</span></span></span></span></span></div>
<div><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><br></span></span></span></span></div>
<h1><span style="font-family:Simsun;font-size:24px;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;">6、发送消息</span></span></span></span></h1>
<div><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><img src="https://img-blog.csdn.net/20150828102759450?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""> 
   <img src="" alt=""></span></span></span></span></div>
<div><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-size:18px;">命令：kafka-console-producer.bat --broker-list localhost:9092 --topic hellotest<br></span></span></span></span></span></div>
<div><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-size:18px;">注意这里的hellotest就是刚才创建的topic。这个命令执行后，可以直接在命令行后面输入你要发的消息。</span></span></span></span></span></div>
<div><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-size:18px;"><img src="" alt=""><br></span></span></span></span></span></div>
<div><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-size:18px;">后面的samsamsam就我输入的，往hellotest这个topic发送的消息。下面我们创建消费者来消费消息。</span></span></span></span></span></div>
<div><br></div>
<h1><span style="font-family:Simsun;font-size:24px;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;">7、接收消息</span></span></span></span></h1>
<div><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><span style="font-family:Simsun;font-size:14px;"><img src="https://img-blog.csdn.net/20150828102812159?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></span></span></span></div>
<div><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-size:18px;">命令：kafka-console-consumer.bat --zookeeper localhost:2181 --topic hellotest --from-beginning</span></span></span></span></span></div>
<div><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-family:Simsun;"><span style="font-size:18px;"><br></span></span></span></span></span></div>
<div><span style="font-family:Simsun;"><span style="font-size:18px;">同样的，必须指定到hellotest这个topic消费消息，我在生产者的cmd命令行界面中输入samsamsam后，消息者cmd命令行界面立刻接收到了。</span></span></div>
<div><span style="font-family:Simsun;font-size:14px;"><br></span></div>
<div><span style="font-family:Simsun;font-size:14px;"><br></span></div>
<h1>8、使用kafka提供的java 客户端来发送和接收消息</h1>
<div><span style="font-size:18px;">上面提到可以使用kafka自带的命令行工具来发送消息和接收消息，下面介绍一下使用java发送和接收消息。</span></div>
<div><br></div>
<h2>8.1 发送者</h2>
<div>     <pre><code class="language-java">package com.kafka;

import java.util.Properties;

import kafka.javaapi.producer.Producer;
import kafka.producer.KeyedMessage;
import kafka.producer.ProducerConfig;

public class Sender {

  public static void main(String[] args) {
    Properties prop = new Properties();
    prop.put("metadata.broker.list", "127.0.0.1:9092");
    prop.put("serializer.class", "kafka.serializer.StringEncoder");
    ProducerConfig producerConfig = new ProducerConfig(prop);
    Producer&lt;String, String&gt; producer = new&lt;String, String&gt; Producer(producerConfig);
    String topic = "hellotest";
    KeyedMessage&lt;String, String&gt; message = new&lt;String, String&gt; KeyedMessage(topic, "Sam Hello Test message2");
    producer.send(message);
    producer.close();
  }
}
</code></pre><br></div>
<h2>8.2 消费者</h2>
<p>代码一</p>
<p><br></p>
<div><span style="font-family:Simsun;font-size:14px;"></span><pre><code class="language-java">package com.kafka;

import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.Properties;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.TimeUnit;

import kafka.consumer.ConsumerConfig;
import kafka.consumer.KafkaStream;
import kafka.javaapi.consumer.ConsumerConnector;

public class ConsumerDemo {

  private final ConsumerConnector consumer;
  private final String topic;
  private ExecutorService executor;

  public ConsumerDemo(String zookeeper, String groupid, String aTopic) {
    consumer = kafka.consumer.Consumer.createJavaConsumerConnector(ConsumerProps(zookeeper, groupid));
    this.topic = aTopic;
  }

  public void run(int threads) {
    Map&lt;String, Integer&gt; topicMap = new HashMap&lt;String, Integer&gt;();
    topicMap.put(topic, new Integer(threads));
    Map&lt;String, List&lt;KafkaStream&lt;byte[], byte[]&gt;&gt;&gt; consumerMap = consumer.createMessageStreams(topicMap);
    List&lt;KafkaStream&lt;byte[], byte[]&gt;&gt; streams = consumerMap.get(topic);

    executor = Executors.newFixedThreadPool(threads);

    int numThread = 0;

    for (final KafkaStream stream : streams) {
      executor.submit(new ConsumerDemoRun(stream, numThread));
      numThread++;
    }
  }

  private static ConsumerConfig ConsumerProps(String zookeeper, String groupid) {

    Properties properties = new Properties(); // config properties file

    properties.put("zookeeper.connect", zookeeper);
    properties.put("group.id", groupid);
    properties.put("zookeeper.session.timeout.ms", "400");
    properties.put("zookeeper.sync.time.ms", "200");
    properties.put("auto.commit.interval.ms", "1000");
    properties.put("auto.offset.reset", "smallest");

    return new ConsumerConfig(properties);
  }

  public void shutdown() {
    if (consumer != null)
      consumer.shutdown();
    if (executor != null)
      executor.shutdown();

    try {
      if (!executor.awaitTermination(5000, TimeUnit.MILLISECONDS)) {
        System.out.println("Timed out waiting for consumer threads to shut down, exiting uncleanly");
      }
    } catch (InterruptedException e) {
      System.out.println("Interrupted during shutdown, exiting uncleanly");
    }
  }

  public static void main(String[] args) {
    String zookeeper = "localhost:2181";
    String groupid = "group1";
    String topic = "hellotest";
    int threads = 1;

    ConsumerDemo test = new ConsumerDemo(zookeeper, groupid, topic);
    test.run(threads);

    try {
      Thread.sleep(10000);
    } catch (InterruptedException ie) {
    }

    test.shutdown();
  }
}
</code></pre><br>
代码二<br><pre><code class="language-java">package com.kafka;

import kafka.consumer.ConsumerIterator;
import kafka.consumer.KafkaStream;

public class ConsumerDemoRun implements Runnable {
  private KafkaStream aStream;
  private int aThread;

  public ConsumerDemoRun(KafkaStream stream, int thread) {
    aStream = stream; // set stream from main read
    aThread = thread; // set thread from main read
  }

  public void run() {

    ConsumerIterator&lt;byte[], byte[]&gt; iterator = aStream.iterator(); // used to
                                                                    // check
                                                                    // throughout
                                                                    // the list
                                                                    // continiously

    while (iterator.hasNext()) {
      System.out.println("Thread " + aThread + ": " + new String(iterator.next().message()));
    }
    System.out.println("Shutting down Thread: " + aThread);

  }
}
</code></pre><br><br><span style="font-size:18px;">要运行上面的代码，需要加入如下依赖</span></div>
<div><span style="font-family:Simsun;font-size:14px;"></span><pre><code class="language-html">                 &lt;dependency&gt;
			&lt;groupId&gt;org.apache.kafka&lt;/groupId&gt;
			&lt;artifactId&gt;kafka_2.9.2&lt;/artifactId&gt;
			&lt;version&gt;0.8.2.1&lt;/version&gt;
		&lt;/dependency&gt;</code></pre><br><br></div>
<div><span style="font-family:Simsun;font-size:14px;"><br></span></div>
<h1><span style="font-family:Simsun;"><span style="font-size:24px;">总结：</span></span></h1>
<div><span style="font-family:Simsun;"><span style="font-size:18px;">在window上安装kafka还挺麻烦的，上面提供的安装方式比较繁琐，应该有更加简单的方式。目前还没找到。</span></span></div>
<div><span style="font-family:Simsun;font-size:14px;">   </span></div>
<p></p>
            </div>
                </div>