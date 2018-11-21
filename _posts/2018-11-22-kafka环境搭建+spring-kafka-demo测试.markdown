---
layout:     post
title:      kafka环境搭建+spring-kafka-demo测试
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/programmeryu/article/details/81333538				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p style="margin-left:0cm;"><strong><span style="color:#333333;">kafka</span></strong><strong><span style="color:#333333;">环境搭建</span></strong><strong><span style="color:#333333;">1-</span></strong><strong><span style="color:#333333;">单机</span></strong></p>

<p style="margin-left:0cm;"><em><span style="color:#2f2f2f;">kafka</span></em><em><span style="color:#2f2f2f;">是基于</span></em><em><span style="color:#2f2f2f;">scala</span></em><em><span style="color:#2f2f2f;">语言开发，所以需要</span></em><em><span style="color:#2f2f2f;">java</span></em><em><span style="color:#2f2f2f;">运行环境，下载前请先确认是否已经安装并配置</span></em><em><span style="color:#2f2f2f;">java</span></em><em><span style="color:#2f2f2f;">环境</span></em></p>

<p style="margin-left:0cm;"><strong><span style="color:#2f2f2f;">下</span></strong><strong><span style="color:#2f2f2f;">载</span></strong><strong><span style="color:#2f2f2f;">安装</span></strong></p>

<ul><li><span style="color:#2f2f2f;">下</span><span style="color:#2f2f2f;">载</span> <span style="color:#2f2f2f;">最新版的</span><span style="color:#2f2f2f;">kafka</span><span style="color:#2f2f2f;">软</span><span style="color:#2f2f2f;">件</span></li>
	<li><span style="color:#abb2bf;">wget </span><a href="http://mirrors.shu.edu.cn/apache/kafka/1.1.0/kafka_2.12-1.1.0.tgz" rel="nofollow"><strong><span style="color:#585ac2;">http://mirrors.shu.edu.cn/apache/kafka/1.1.0/kafka_2.12-1.1.0.tgz</span></strong></a></li>
</ul><ul><li><span style="color:#2f2f2f;">解</span><span style="color:#2f2f2f;">压缩</span></li>
</ul><p style="margin-left:0cm;"><span style="color:#e06c75;">tar</span> <span style="color:#e06c75;">-xzf</span> <span style="color:#e06c75;">kafka_2</span><span style="color:#d19a66;">.11-0.9.0.0.tgz</span></p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;"><strong><span style="color:#2f2f2f;">配置</span></strong><strong><span style="color:#2f2f2f;">访问</span></strong><strong><span style="color:#2f2f2f;">地址：</span></strong></p>

<p style="margin-left:0cm;"><strong><span style="color:#2f2f2f;">config/server.properties</span></strong></p>

<ul><li><span style="color:#2f2f2f;">#</span><span style="color:#2f2f2f;">内网地址：</span><span style="color:#2f2f2f;">192.168.85.171</span></li>
	<li><span style="color:#2f2f2f;">listeners=PLAINTEXT://192.168.85.171:9092</span></li>
	<li><span style="color:#2f2f2f;">#</span><span style="color:#2f2f2f;">外网地址：</span><span style="color:#2f2f2f;">192.168.85.171</span></li>
	<li><span style="color:#2f2f2f;">advertised.listeners=PLAINTEXT://192.168.85.171:9092</span></li>
</ul><p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;"><span style="color:#2f2f2f;">开放默</span><span style="color:#2f2f2f;">认</span><span style="color:#2f2f2f;">端口</span><span style="color:#2f2f2f;">：</span></p>

<pre style="margin-left:0cm;">
<code><span style="color:#333333;">/sbin/iptables </span></code><span style="color:#333333;">-I INPUT -p tcp </span><span style="color:#333333;">--dport </span><span style="color:#333333;">9092 </span><span style="color:#333333;">-j ACCEPT</span></pre>

<pre style="margin-left:0cm;">
<span style="color:#333333;">/sbin/iptables -I INPUT -p tcp </span><span style="color:#333333;">--dport </span><span style="color:#333333;">2181 </span><span style="color:#333333;">-j ACCEPT</span></pre>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;"><strong><span style="color:#2f2f2f;">启</span></strong><strong><span style="color:#2f2f2f;">动</span></strong></p>

<p style="margin-left:0cm;"><span style="color:#2f2f2f;">首先要进入</span><span style="color:#2f2f2f;">kafka</span><span style="color:#2f2f2f;">目录</span></p>

<ul><li><span style="color:#2f2f2f;">启</span><span style="color:#2f2f2f;">动</span><span style="color:#2f2f2f;">zookeeper server</span><span style="color:#2f2f2f;">（</span><span style="color:#2f2f2f;">kafka</span><span style="color:#2f2f2f;">自</span><span style="color:#2f2f2f;">带</span><span style="color:#2f2f2f;">的</span><span style="color:#2f2f2f;">zookeeper</span><span style="color:#2f2f2f;">）</span></li>
</ul><p style="margin-left:0cm;"><span style="color:#abb2bf;">bin/zookeeper-server-start.sh config/zookeeper.properties &amp;</span></p>

<p style="margin-left:0cm;"><em><span style="color:#2f2f2f;">注意</span></em><em><span style="color:#2f2f2f;"> &amp; </span></em><em><span style="color:#2f2f2f;">号的使用：命令结尾添加</span></em><em><span style="color:#2f2f2f;"> &amp; </span></em><em><span style="color:#2f2f2f;">号，可以在执行完命令后退出当前命令环境却不会结束进程</span></em></p>

<ul><li><span style="color:#2f2f2f;">启</span><span style="color:#2f2f2f;">动</span><span style="color:#2f2f2f;">kafka server</span></li>
</ul><p style="margin-left:0cm;"><span style="color:#abb2bf;">bin/kafka-server-start.sh config/server.properties &amp; </span></p>

<p style="margin-left:0cm;"><strong><span style="color:#2f2f2f;">创建主题</span></strong></p>

<p style="margin-left:0cm;"><span style="color:#2f2f2f;">kafka</span><span style="color:#2f2f2f;">生产和消费数据，必须基于主题</span><span style="color:#2f2f2f;">topic</span><span style="color:#2f2f2f;">。主题其实就是对消息的分类。</span></p>

<ul><li><span style="color:#2f2f2f;">创建主题：名称为</span><span style="color:#2f2f2f;">“test”</span><span style="color:#2f2f2f;">、复制数目</span><span style="color:#2f2f2f;">为</span><span style="color:#2f2f2f;">1</span><span style="color:#2f2f2f;">、</span><span style="color:#2f2f2f;">partitions</span><span style="color:#2f2f2f;">为</span><span style="color:#2f2f2f;">1</span><span style="color:#2f2f2f;">的</span><span style="color:#2f2f2f;">topic</span><span style="color:#2f2f2f;">主</span><span style="color:#2f2f2f;">题</span></li>
</ul><p style="margin-left:0cm;"><span style="color:#abb2bf;">bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic </span><span style="color:#e6c07b;">test</span></p>

<p style="margin-left:0cm;"><strong><span style="color:#2f2f2f;">replication-factor</span></strong> <span style="color:#2f2f2f;">：</span> <em><span style="color:#2f2f2f;">复制数目，提供</span></em><em><span style="color:#2f2f2f;">failover</span></em><em><span style="color:#2f2f2f;">机制；</span></em><em><span style="color:#2f2f2f;">1</span></em><em><span style="color:#2f2f2f;">代表只在一个</span></em><em><span style="color:#2f2f2f;">broker</span></em><em><span style="color:#2f2f2f;">上有数据记录，一般值都大于</span></em><em><span style="color:#2f2f2f;">1</span></em><em><span style="color:#2f2f2f;">，代表一份数据会自动同步到其他的多个</span></em><em><span style="color:#2f2f2f;">broker</span></em><em><span style="color:#2f2f2f;">，防止某个</span></em><em><span style="color:#2f2f2f;">broker</span></em><em><span style="color:#2f2f2f;">宕机后数据丢失。</span></em><br><span style="color:#2f2f2f;"><strong>partitions</strong> </span><span style="color:#2f2f2f;">：</span> <em><span style="color:#2f2f2f;">一个</span></em><em><span style="color:#2f2f2f;">topic</span></em><em><span style="color:#2f2f2f;">可以被切分成多个</span></em><em><span style="color:#2f2f2f;">partitions</span></em><em><span style="color:#2f2f2f;">，一个消费者可以消费多个</span></em><em><span style="color:#2f2f2f;">partitions</span></em><em><span style="color:#2f2f2f;">，但一个</span></em><em><span style="color:#2f2f2f;">partitions</span></em><em><span style="color:#2f2f2f;">只能被一个消费者消费，所以增加</span></em><em><span style="color:#2f2f2f;">partitions</span></em><em><span style="color:#2f2f2f;">可以增加消费者的吞吐量。</span></em><em><span style="color:#2f2f2f;">kafka</span></em><em><span style="color:#2f2f2f;">只保证一个</span></em><em><span style="color:#2f2f2f;">partitions</span></em><em><span style="color:#2f2f2f;">内的消息是有序的，多个一个</span></em><em><span style="color:#2f2f2f;">partitions</span></em><em><span style="color:#2f2f2f;">之间的数据是无序的。</span></em></p>

<ul><li><span style="color:#2f2f2f;">查看</span><span style="color:#2f2f2f;">已</span><span style="color:#2f2f2f;">经创</span><span style="color:#2f2f2f;">建的主</span><span style="color:#2f2f2f;">题</span></li>
</ul><p style="margin-left:0cm;"><span style="color:#abb2bf;">bin/kafka-topics.sh --</span><span style="color:#e6c07b;">list</span><span style="color:#abb2bf;"> --zookeeper localhost:</span><span style="color:#d19a66;">2181</span></p>

<p style="margin-left:0cm;"><strong><span style="color:#2f2f2f;">启</span></strong><strong><span style="color:#2f2f2f;">动</span></strong><strong><span style="color:#2f2f2f;">生</span></strong><strong><span style="color:#2f2f2f;">产</span></strong><strong><span style="color:#2f2f2f;">者和消</span></strong><strong><span style="color:#2f2f2f;">费</span></strong><strong><span style="color:#2f2f2f;">者</span></strong></p>

<p style="margin-left:0cm;"><span style="color:#2f2f2f;">生产者产生（输入）数据，消费者消费（输出）数据</span></p>

<ul><li><span style="color:#2f2f2f;">启</span><span style="color:#2f2f2f;">动</span><span style="color:#2f2f2f;">生</span><span style="color:#2f2f2f;">产</span><span style="color:#2f2f2f;">者</span></li>
</ul><p style="margin-left:0cm;"><span style="color:#abb2bf;">bin/kafka-console-producer.sh --broker-list localhost:9092 --topic </span><span style="color:#e6c07b;">test</span></p>

<p style="margin-left:0cm;"><em><span style="color:#2f2f2f;">启动后，在命令行下每输入一些字符串按下回车时，就作为一个消息并发送的</span></em><em><span style="color:#2f2f2f;">kafka</span></em></p>

<ul><li><span style="color:#2f2f2f;">启</span><span style="color:#2f2f2f;">动</span><span style="color:#2f2f2f;">消</span><span style="color:#2f2f2f;">费</span><span style="color:#2f2f2f;">者</span></li>
</ul><p style="margin-left:0cm;"><span style="color:#abb2bf;">bin/kafka-</span><span style="color:#e6c07b;">console</span><span style="color:#abb2bf;">-consumer.sh --zookeeper localhost:</span><span style="color:#d19a66;">2181</span><span style="color:#abb2bf;"> --topic test --</span><span style="color:#c678dd;">from</span><span style="color:#abb2bf;">-beginning</span></p>

<p style="margin-left:0cm;"><em><span style="color:#2f2f2f;">启动消费者时，建议另开一个</span></em><em><span style="color:#2f2f2f;">ssh</span></em><em><span style="color:#2f2f2f;">窗口，方便一遍通过生产者命令行输入消息，一遍观察消费者消费的数据</span></em></p>

<p style="margin-left:0cm;"><span style="color:#2f2f2f;">当在生产者下输入消息并回车后，在消费者窗口下就能立即看到对应的消息，这就说明环境搭建成功。</span></p>

<p style="margin-left:0cm;">Springboot +kafka</p>

<p style="margin-left:0cm;">pom</p>

<pre style="margin-left:0cm;">

<span style="color:#e8bf6a;">&lt;dependency&gt;

   &lt;groupId&gt;</span><span style="color:#a9b7c6;">org.springframework.kafka</span><span style="color:#e8bf6a;">&lt;/groupId&gt;

   &lt;artifactId&gt;</span><span style="color:#a9b7c6;">spring-kafka</span><span style="color:#e8bf6a;">&lt;/artifactId&gt;

   &lt;version&gt;</span><span style="color:#a9b7c6;">1.2.2.RELEASE</span><span style="color:#e8bf6a;">&lt;/version&gt;

&lt;/dependency&gt;</span></pre>

<p style="margin-left:0cm;">application.properties</p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;"> </p>

<pre style="margin-left:0cm;">
<span style="color:#808080;"># kafka

</span><span style="color:#cc7832;">spring.kafka.bootstrap-servers</span><span style="color:#808080;">=</span><span style="color:#6a8759;">192.168.85.171:9092

</span><span style="color:#cc7832;">spring.kafka.consumer.group-id</span><span style="color:#808080;">=</span><span style="color:#6a8759;">group1

</span><span style="color:#cc7832;">spring.kafka.consumer.auto-offset-reset</span><span style="color:#808080;">=</span><span style="color:#6a8759;">earliest</span></pre>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;">controller</p>

<p style="margin-left:0cm;"> </p>

<pre style="margin-left:0cm;">

 </pre>

<pre style="margin-left:0cm;">
<span style="color:#cc7832;">package </span><span style="color:#a9b7c6;">com.kaicom.nettytest.kafka</span><span style="color:#cc7832;">;



import </span><span style="color:#a9b7c6;">org.apache.kafka.clients.consumer.ConsumerRecord</span><span style="color:#cc7832;">;

import </span><span style="color:#a9b7c6;">org.slf4j.Logger</span><span style="color:#cc7832;">;

import </span><span style="color:#a9b7c6;">org.slf4j.LoggerFactory</span><span style="color:#cc7832;">;

import </span><span style="color:#a9b7c6;">org.springframework.beans.factory.annotation.</span><span style="color:#bbb529;">Autowired</span><span style="color:#cc7832;">;

import </span><span style="color:#a9b7c6;">org.springframework.boot.SpringApplication</span><span style="color:#cc7832;">;

import </span><span style="color:#a9b7c6;">org.springframework.kafka.annotation.</span><span style="color:#bbb529;">KafkaListener</span><span style="color:#cc7832;">;

import </span><span style="color:#a9b7c6;">org.springframework.kafka.core.KafkaTemplate</span><span style="color:#cc7832;">;

import </span><span style="color:#a9b7c6;">org.springframework.web.bind.annotation.</span><span style="color:#bbb529;">RequestMapping</span><span style="color:#cc7832;">;

import </span><span style="color:#a9b7c6;">org.springframework.web.bind.annotation.</span><span style="color:#bbb529;">ResponseBody</span><span style="color:#cc7832;">;

import </span><span style="color:#a9b7c6;">org.springframework.web.bind.annotation.</span><span style="color:#bbb529;">RestController</span><span style="color:#cc7832;">;



</span><em><span style="color:#629755;">/**

 * <strong>@Author: </strong>BillYu

 * <strong>@Description:kafka</strong></span></em><strong><em><span style="color:#629755;">连接测试</span></em></strong>
<strong><em>
 </em></strong><em><span style="color:#629755;">* <strong>@Date: </strong>Created in </span></em><em><span style="color:#629755;">下午</span></em><em><span style="color:#629755;">3:52 2018/6/4.

 */

</span></em><span style="color:#bbb529;">@RestController

</span><span style="color:#cc7832;">public class </span><span style="color:#a9b7c6;">SampleController {

    </span><span style="color:#cc7832;">private final </span><span style="color:#a9b7c6;">Logger </span><span style="color:#9876aa;">log </span><span style="color:#a9b7c6;">= LoggerFactory.<em>getLogger</em>(SampleController.</span><span style="color:#cc7832;">class</span><span style="color:#a9b7c6;">)</span><span style="color:#cc7832;">;







    </span><span style="color:#bbb529;">@Autowired

    </span><span style="color:#cc7832;">private </span><span style="color:#a9b7c6;">KafkaTemplate&lt;String</span><span style="color:#cc7832;">, </span><span style="color:#a9b7c6;">String&gt; </span><span style="color:#9876aa;">template</span><span style="color:#cc7832;">;



    </span><span style="color:#bbb529;">@RequestMapping</span><span style="color:#a9b7c6;">(</span><span style="color:#6a8759;">"/send"</span><span style="color:#a9b7c6;">)

    </span><span style="color:#bbb529;">@ResponseBody

    </span><span style="color:#a9b7c6;">String </span><span style="color:#ffc66d;">send</span><span style="color:#a9b7c6;">(String topic</span><span style="color:#cc7832;">, </span><span style="color:#a9b7c6;">String key</span><span style="color:#cc7832;">, </span><span style="color:#a9b7c6;">String data) {

        </span><span style="color:#9876aa;">template</span><span style="color:#a9b7c6;">.send(topic</span><span style="color:#cc7832;">, </span><span style="color:#a9b7c6;">key</span><span style="color:#cc7832;">, </span><span style="color:#a9b7c6;">data)</span><span style="color:#cc7832;">;

        return </span><span style="color:#6a8759;">"success"</span><span style="color:#cc7832;">;

    </span><span style="color:#a9b7c6;">}







    </span><span style="color:#bbb529;">@KafkaListener</span><span style="color:#a9b7c6;">(</span><span style="color:#d0d0ff;">id </span><span style="color:#a9b7c6;">= </span><span style="color:#6a8759;">"test"</span><span style="color:#cc7832;">, </span><span style="color:#d0d0ff;">topics </span><span style="color:#a9b7c6;">= </span><span style="color:#6a8759;">"test"</span><span style="color:#a9b7c6;">)

    </span><span style="color:#cc7832;">public void </span><span style="color:#ffc66d;">listenTest</span><span style="color:#a9b7c6;">(ConsumerRecord&lt;?</span><span style="color:#cc7832;">, </span><span style="color:#a9b7c6;">?&gt; cr) </span><span style="color:#cc7832;">throws </span><span style="color:#a9b7c6;">Exception {

        System.</span><em><span style="color:#9876aa;">out</span></em><span style="color:#a9b7c6;">.println(</span><span style="color:#6a8759;">"===&gt;listen"</span><span style="color:#a9b7c6;">)</span><span style="color:#cc7832;">;

        </span><span style="color:#9876aa;">log</span><span style="color:#a9b7c6;">.info(</span><span style="color:#6a8759;">"{} - {} : {}"</span><span style="color:#cc7832;">, </span><span style="color:#a9b7c6;">cr.topic()</span><span style="color:#cc7832;">, </span><span style="color:#a9b7c6;">cr.key()</span><span style="color:#cc7832;">, </span><span style="color:#a9b7c6;">cr.value())</span><span style="color:#cc7832;">;

    </span><span style="color:#a9b7c6;">}









}</span></pre>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;"> </p>

<h2 style="margin-left:0cm;"><strong>可视化监控安装</strong></h2>

<h2 style="margin-left:0cm;"><strong> <span style="color:#ebebeb;">一、KafkaOffsetMonitor简述</span></strong></h2>

<p style="margin-left:0cm;"><span style="color:#000000;">KafkaOffsetMonitor</span><span style="color:#000000;">是Kafka的一款客户端消费监控工具，用来实时监控Kafka服务的Consumer以及它们所在的Partition中的Offset，我们可以浏览当前的消费者组，并且每个Topic的所有Partition的消费情况都可以一目了然。</span></p>

<h2 style="margin-left:0cm;"><strong><span style="color:#ebebeb;">二、KafkaOffsetMonitor下载</span></strong></h2>

<p style="margin-left:0cm;"><span style="color:#000000;">KafkaOffsetMonitor</span><span style="color:#000000;">托管在Github上，可以通过Github下载。<br>
下载地址：https://github.com/quantifind/KafkaOffsetMonitor/releases</span></p>

<p style="margin-left:0cm;"><span style="color:#000000;">或者下载百度网盘：链接：https://pan.baidu.com/s/1geEBEvT 密码：jaeu</span></p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;"><span style="color:#000000;">github</span><span style="color:#000000;">提供下载的jar包应用了google网站提供的js文件，由于被墙的原因导致web界面无法访问。可以直接使用别人修改后的jar包，也可以从github上拉取源码然后修改打包。</span></p>

<p style="margin-left:0cm;"><span style="color:#000000;"><img alt="https://images2017.cnblogs.com/blog/1209537/201801/1209537-20180108132411629-753572802.png" class="has" height="279" src="https://images2017.cnblogs.com/blog/1209537/201801/1209537-20180108132411629-753572802.png" width="833"></span></p>

<p style="margin-left:0cm;"> </p>

<h2 style="margin-left:0cm;"><strong><span style="color:#ebebeb;">三、KafkaOffsetMonitor启动</span></strong></h2>

<p style="margin-left:0cm;"><span style="color:#000000;">将下载下来的KafkaOffsetMonitor jar包上传到linux上，可以新建一个目录KafkaMonitor，用于存放KafkaOffsetMonitor-assembly-0.2.0.jar进入到KafkaMonitor目录下，通过java编译命令来运行这个jar包：</span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="20" src="https://img-blog.csdn.net/20180801164118810?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Byb2dyYW1tZXJ5dQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="20"></p>

<pre style="margin-left:0cm;">
<span style="color:#000000;">[root@kafka50 KafkaMonitor]# </span><strong><span style="color:#0000FF;">java -cp KafkaOffsetMonitor-assembly-0.2.0.jar com.quantifind.kafka.offsetapp.OffsetGetterWeb --zk 10.0.0.50:12181,10.0.0.60:12181,10.0.0.70:12181 --port 8088  --refresh 5.seconds --retain 1.days</span></strong></pre>

<pre style="margin-left:0cm;">
<strong><span style="color:#000000;">按回车后，可以看到控制台输出：</span></strong></pre>

<pre style="margin-left:0cm;">

 </pre>

<pre style="margin-left:0cm;">
<span style="color:#000000;">serving resources </span><span style="color:#0000FF;">from</span><span style="color:#000000;">: jar:file:/data/KafkaMonitor/KafkaOffsetMonitor-assembly-</span><span style="color:#800080;">0.2</span><span style="color:#000000;">.</span><span style="color:#800080;">0</span><span style="color:#000000;">.jar!/offsetapp</span></pre>

<pre style="margin-left:0cm;">
<span style="color:#000000;">SLF4J: Failed to load </span><span style="color:#0000FF;">class</span> <span style="color:#800000;">"org.slf4j.impl.StaticLoggerBinder"</span><span style="color:#000000;">.</span></pre>

<pre style="margin-left:0cm;">
<span style="color:#000000;">SLF4J: Defaulting to no-operation (NOP) logger implementation</span></pre>

<pre style="margin-left:0cm;">
<span style="color:#000000;">SLF4J: See http:</span><span style="color:#008000;">//www.slf4j.org/codes.html#StaticLoggerBinder for further details.</span></pre>

<pre style="margin-left:0cm;">
<span style="color:#800080;">2018</span><span style="color:#000000;">-</span><span style="color:#800080;">01</span><span style="color:#000000;">-</span><span style="color:#800080;">05</span> <span style="color:#800080;">21</span><span style="color:#000000;">:</span><span style="color:#800080;">17</span><span style="color:#000000;">:</span><span style="color:#800080;">36.267</span><span style="color:#000000;">:INFO:oejs.Server:jetty-</span><span style="color:#800080;">7</span><span style="color:#000000;">.x.y-SNAPSHOT</span></pre>

<pre style="margin-left:0cm;">
<span style="color:#000000;">log4j:WARN No appenders could be found </span><span style="color:#0000FF;">for</span><span style="color:#000000;"> logger (org.I0Itec.zkclient.ZkConnection).</span></pre>

<pre style="margin-left:0cm;">
<span style="color:#000000;">log4j:WARN Please initialize the log4j system properly.</span></pre>

<pre style="margin-left:0cm;">
<span style="color:#000000;">log4j:WARN See http:</span><span style="color:#008000;">//logging.apache.org/log4j/1.2/faq.html#noconfig for more info.</span></pre>

<pre style="margin-left:0cm;">
<span style="color:#800080;">2018</span><span style="color:#000000;">-</span><span style="color:#800080;">01</span><span style="color:#000000;">-</span><span style="color:#800080;">05</span> <span style="color:#800080;">21</span><span style="color:#000000;">:</span><span style="color:#800080;">17</span><span style="color:#000000;">:</span><span style="color:#800080;">36.630</span><span style="color:#000000;">:INFO:oejsh.ContextHandler:started o.e.j.s.ServletContextHandler{/,jar:file:/data/KafkaMonitor/KafkaOffsetMonitor-assembly-</span><span style="color:#800080;">0.2</span><span style="color:#000000;">.</span><span style="color:#800080;">0</span><span style="color:#000000;">.jar!/offsetapp}</span></pre>

<pre style="margin-left:0cm;">
<span style="color:#800080;">2018</span><span style="color:#000000;">-</span><span style="color:#800080;">01</span><span style="color:#000000;">-</span><span style="color:#800080;">05</span> <span style="color:#800080;">21</span><span style="color:#000000;">:</span><span style="color:#800080;">17</span><span style="color:#000000;">:</span><span style="color:#800080;">36.662</span><span style="color:#000000;">:INFO:oejs.AbstractConnector:Started SocketConnector@</span><span style="color:#800080;">0.0</span><span style="color:#000000;">.</span><span style="color:#800080;">0.0</span><span style="color:#000000;">:</span><span style="color:#800080;">8088</span></pre>

<p style="margin-left:0cm;"><img alt="" class="has" height="20" src="https://img-blog.csdn.net/20180801164118860?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Byb2dyYW1tZXJ5dQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="20"></p>

<p style="margin-left:0cm;"><span style="color:#000000;">如果没有指定端口，则默认会开启一个随机端口。</span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="20" src="https://img-blog.csdn.net/20180801164118871?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Byb2dyYW1tZXJ5dQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="20"></p>

<pre style="margin-left:0cm;">
<strong><span style="color:#000000;">参数说明</span></strong><strong><span style="color:#000000;">:</span></strong></pre>

<pre style="margin-left:0cm;">

 </pre>

<pre style="margin-left:0cm;">
<span style="color:#000000;">zk </span><span style="color:#000000;">：</span><span style="color:#000000;">zookeeper</span><span style="color:#000000;">主机地址，如果有多个，用逗号隔开</span></pre>

<pre style="margin-left:0cm;">
<span style="color:#000000;">port </span><span style="color:#000000;">：应用程序端口</span></pre>

<pre style="margin-left:0cm;">
<span style="color:#000000;">refresh </span><span style="color:#000000;">：应用程序在数据库中刷新和存储点的频率</span></pre>

<pre style="margin-left:0cm;">
<span style="color:#000000;">retain </span><span style="color:#000000;">：在</span><span style="color:#000000;">db</span><span style="color:#000000;">中保留多长时间</span></pre>

<pre style="margin-left:0cm;">
<span style="color:#000000;">dbName </span><span style="color:#000000;">：保存的数据库文件名，默认为</span><span style="color:#000000;">offsetapp</span></pre>

<p style="margin-left:0cm;"><img alt="" class="has" height="20" src="https://img-blog.csdn.net/20180801164118891?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Byb2dyYW1tZXJ5dQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="20"></p>

<p style="margin-left:0cm;"><span style="color:#000000;">为了更方便的启动KafkaOffsetMonitor，可以写一个启动脚本来直接运行，我这里新建一个名为：kafka-monitor-start.sh的脚本，然后编辑这个脚本：</span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="20" src="https://img-blog.csdn.net/20180801164118890?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Byb2dyYW1tZXJ5dQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="20"></p>

<pre style="margin-left:0cm;">
<span style="color:#000000;">[root@kafka50 KafkaMonitor]# vim kafka-monitor-start.sh </span></pre>

<pre style="margin-left:0cm;">
<strong><span style="color:#0000FF;">java -Xms512M -Xmx512M -Xss1024K -XX:PermSize=256m -XX:MaxPermSize=512m  -cp KafkaOffsetMonitor-assembly-0.2.0.jar com.quantifind.kafka.offsetapp.OffsetGetterWeb \</span></strong></pre>

<pre style="margin-left:0cm;">
<strong><span style="color:#0000FF;">--port 8088 \</span></strong></pre>

<pre style="margin-left:0cm;">
<strong><span style="color:#0000FF;">--zk 10.0.0.50:12181,10.0.0.60:12181,10.0.0.70:12181 \</span></strong></pre>

<pre style="margin-left:0cm;">
<strong><span style="color:#0000FF;">--refresh 5.minutes \</span></strong></pre>

<pre style="margin-left:0cm;">
<strong><span style="color:#0000FF;">--retain 1.day &gt;/dev/null 2&gt;&amp;1;</span></strong></pre>

<p style="margin-left:0cm;"><img alt="" class="has" height="20" src="https://img-blog.csdn.net/20180801164118911?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Byb2dyYW1tZXJ5dQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="20"></p>

<p style="margin-left:0cm;"><span style="color:#000000;">然后退出保存即可，接下来修改一下kafka-monitor-start.sh的权限</span></p>

<pre style="margin-left:0cm;">
<span style="color:#000000;">[root@kafka50 KafkaMonitor]# </span><strong><span style="color:#0000FF;">chmod +x kafka-monitor-start.sh</span></strong> </pre>

<p style="margin-left:0cm;"><span style="color:#000000;">启动KafkaOffsetMonitor：</span></p>

<pre style="margin-left:0cm;">
<span style="color:#000000;">[root@kafka50 KafkaMonitor]# </span><strong><span style="color:#0000FF;">nohup /data/KafkaMonitor/kafka-monitor-start.sh &amp;</span></strong></pre>

<pre style="margin-left:0cm;">
<span style="color:#000000;">[</span><span style="color:#800080;">1</span><span style="color:#000000;">] </span><span style="color:#800080;">6551</span></pre>

<pre style="margin-left:0cm;">
<span style="color:#000000;">[root@kafka50 KafkaMonitor]# lsof -i:</span><span style="color:#800080;">8088</span></pre>

<pre style="margin-left:0cm;">
<span style="color:#000000;">COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME</span></pre>

<pre style="margin-left:0cm;">
<span style="color:#000000;">java    </span><span style="color:#800080;">6552</span><span style="color:#000000;"> root   </span><span style="color:#800080;">16u</span><span style="color:#000000;">  IPv6  </span><span style="color:#800080;">26047</span><span style="color:#000000;">      0t0  TCP *:radan-http (LISTEN)</span></pre>

<h2 style="margin-left:0cm;"><strong><span style="color:#ebebeb;">四、KafkaOffsetMonitor Web UI</span></strong></h2>

<p style="margin-left:0cm;"><span style="color:#000000;">在游览器中输入：http://ip:port即可以查看KafkaOffsetMonitor Web UI,如下图：</span></p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;"> </p>            </div>
                </div>