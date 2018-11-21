---
layout:     post
title:      kafka集群部署以及java客户端测试
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="kafka集群部署以及java客户端测试">kafka集群部署以及java客户端测试</h1>

<p>本文主要讲述本人的集群部署kafka过程以及遇到的问题： <br>
其中：kafka版本为：kafka_2.10，zookeeper版本为：zookeeper-3.4.8，jdk-8u101-linux-x64 <br>
    <strong>一 kafka以及zookeeper安装以及环境配置</strong> <br>
    <strong>二 zookeeper集群配置以及测试</strong> <br>
    <strong>三 kafka集群配置以及测试</strong> <br>
    <strong>四 java客户端生产与消费测试</strong></p>

<hr>



<h2 id="一-kafka以及zookeeper安装以及环境配置">一  kafka以及zookeeper安装以及环境配置*</h2>

<pre><code>系统环境：CentOS 6.5
服务器三台:192.168.1.1,192.168.1.2,192.168.1.3
jdk下载地址：http://www.oracle.com/technetwork/java/javase/downloads/index.html
zookeeper下载地址:http://www.apache.org/dyn/closer.cgi/zookeeper/
kafka下载地址:http://kafka.apache.org/downloads

1.jdk安装
    cmd:rpm -ivh jdk-8u101-linux-x64.rpm
    安装完毕:java -version显示如下信息即安装成功:
    java version "1.8.0_101"
    Java(TM) SE Runtime Environment (build 1.8.0_101-b13)
    Java HotSpot(TM) 64-Bit Server VM (build 25.101-b13, mixed mode)
2.zookeeper安装
    将下载的zookeeper-3.4.8.tar.gz解压,同时将zookeeper放入usr/local下面
    cmd:unzip zookeeper-3.4.8.tar.gz
    cmd:mv zookeeper-3.4.8.tar.gz zookeeper
    cmd:mv zookeeper /usr/local

3.kafka安装
    将下载的kafka_2.10-0.10.0.0.tgz解压安装,同时将zookeeper放入usr/local下面
    cmd:unzip kafka_2.10-0.10.0.0.tgz
    cmd:mv kafka_2.10-0.10.0.0.tgz kafka
    cmd:mv kafka /usr/local
</code></pre>



<h2 id="二-zookeeper集群配置以及测试">二 zookeeper集群配置以及测试</h2>

<p>1.由于kafka必须依赖zookeeper进行管理自身的broker,offset,producer,comsumer等，所以在安装部署kafka之前必须先部署zookeeeper,虽然kafka也自带了zookeeper，不过本人这里还是自己独立使用的zookeeper.</p>

<pre><code>首先进入上面已经解压好的zookeeper目录:
cmd:cd /usr/local/zookeeper
修改配置文件:cd conf
cmd:cp zoo_sample.cfg zoo.cfg
cmd:vi zoo.cfg
    # The number of milliseconds of each 
    ticktickTime=2000 
    # synchronization phase can take 
    # The number of ticks that can pass between 
    syncLimit=5 
    # the directory where the snapshot is stored.
    dataDir=/usr/local/zookeeper/data
    dataLogDir=/usr/local/zookeeper/logs
    # the port at which the clients will connect 
    clientPort=2181
    server.0=192.168.1.1:4001:4002 
    server.1=192.168.1.2:4001:4002 
    server.2=192.168.1.3:4001:4002
</code></pre>

<p>2.以上就是zookeeper的配置文件，server.0,1,2位三个服务器的地址以及端口，首先得确保这三台服务器之间是互通的，同时4001以及4002端口也都是开放的。另外4001表示这三台服务器之间通信使用的端口，4002则是服务器之间选举使用的端口，当有一台服务器down以后就会时候此端口进行选取新的leader。 <br>
3.最重要的一步： <br>
    分别在三台服务器的dataDir路径下创建一个myid的文件（本例的dataDir地址为:/usr/local/zookeeper/data），文件的内容为zookeeper阶段的编号，例如： <br>
    192.168.1.1这台服务器上的文件内容为0，其余的分别是1,2。 <br>
        cmd:cd /usr/local/zookeeper/data <br>
        cmd:echo 0 &gt; myid <br>
        cmd:cat myid  <br>
    若显示0即成功，同时另外两台服务器上配置也如此，只是修改myid里的内容分别为1与2即可。 <br>
4.启动集群服务器： <br>
    启动三台服务器上的zookeeper服务： <br>
    cmd:bin/zkService.sh start <br>
    全都启动以后查看状况: <br>
     cmd:bin/zkService.sh status <br>
     显示如下:</p>

<blockquote>
  <p>ZooKeeper JMX enabled by default <br>
          Using config: /usr/local/zookeeper/bin/../conf/zoo.cfg <br>
          Mode: follower</p>
</blockquote>

<p>其中Mode表示node的状态，会有两个follower以及一个leader,此时的server1位leader,为了测试选举，将server1的服务停止: <br>
    cmd:bin/zkService.sh stop <br>
    再次查看服务器状态，就可以看到新的leader已经更换。 <br>
    此时zookeeper集群已经搭建OK</p>



<h2 id="三-kafka集群配置以及测试">三 kafka集群配置以及测试</h2>

<p>1.由于是三个服务器，因此这里会存在三个broker，分别是0,1,2，对应的服务器为：192.168.1.1,192.168.1.2,192.168.1.3，因此在创建分区的时候会实用三个分区，如下是安装配置步骤： <br>
    1.首先要修改kakfa的配置文件 <br>
    cmd:cd /use/local/kafka/config <br>
    cmd:vi server.properties</p>

<pre><code>    #此Broker的ID，集群中每个Broker的ID不可相同
    broker.id=0（此处三个服务器分别填写0,1,2 请不要写重了）
    #监听器，端口号与port一致即可
    listeners=PLAINTEXT://:9092
    #Broker的Hostname，填主机IP即可 
    advertised.host.name=192.168.1.1(如果要kafka远程访问，此处必填，否则只能局域网访问kafka)
    #进行IO的线程数，应大于主机磁盘数
    num.io.threads=8 
    #消息文件存储的路径
    log.dirs=/usr/local/kafka/kafka-logs 
    #消息文件清理周期，即清理x小时前的消息记录 
    log.retention.hours=168 
    #每个Topic默认的分区数，一般在创建Topic时都会指定分区数，所以这个配成1就行了 
    num.partitions=1 
    #Zookeeper连接串，此处填写上一节中安装的三个zk节点的ip和端口即可
    zookeeper.connect=192.168.1.1:2181,192.168.1.2:2181,192.168.1.3:2181
</code></pre>

<p>2.接下来分别启动三个服务器上的kafka: <br>
cmd:bin/kafka-server-start.sh config/server.properties &amp; <br>
没有报错即启动成功 <br>
3.然后创建topic</p>

<blockquote>
  <p>bin/kafka-topics.sh –create –zookeeper 192.168.1.1:2181,192.168.1.2:2181,192.168.1.3:2181 –replication-factor 3 –partitions 3 –topic test <br>
  查看Topic的状态： <br>
  bin/kafka-topics.sh –describe –zookeeper 192.168.1.1:2181,192.168.1.2:2181,192.168.1.3:2181 –topic test <br>
  输出: <br>
  Topic:test  PartitionCount:3       ReplicationFactor:3     Configs: <br>
          Topic: test Partition: 0    Leader: 1       Replicas: 1,2,0 Isr: 2,0,1 <br>
          Topic: test Partition: 1    Leader: 2       Replicas: 2,0,1 Isr: 2,0,1 <br>
          Topic: test Partition: 2    Leader: 3       Replicas: 0,1,2 Isr: 2,0,1</p>
</blockquote>

<p>简单测试：</p>

<blockquote>
  <p>生产者： <br>
  bin/kafka-console-producer.sh –broker-list 192.168.1.1:9092,192.168.1.2:9092,192.168.1.3:9092 –topic test  <br>
  消费者: <br>
  bin/kafka-console-consumer.sh –zookeeper 192.168.1.1:2181,192.168.1.2:2181,192.168.1.3:2181 –topic test –from-beginning <br>
  此时在生产者控制台输入：hello world <br>
  消费者正常显示：hello world</p>
</blockquote>

<p>如此集群就搭建完毕，剩下的就是利用java客户端进行远程测试。</p>



<h2 id="四-java客户端生产与消费测试">四 java客户端生产与消费测试</h2>

<p>本人直接使用的lib包的方式进行测试的，并非maven。如下是本人的lib包 <br>
<img src="https://img-blog.csdn.net/20161208162239711?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTYyMjIyNg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>这些lib包都是可以再kafka根目录下的libs里面找到的。 <br>
1.首先利用kafka api创建一个生产者：</p>



<pre class="prettyprint"><code class=" hljs avrasm">package <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.KafkaService</span><span class="hljs-comment">;</span>

import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Properties</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.clients</span><span class="hljs-preprocessor">.producer</span><span class="hljs-preprocessor">.Callback</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.clients</span><span class="hljs-preprocessor">.producer</span><span class="hljs-preprocessor">.KafkaProducer</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.clients</span><span class="hljs-preprocessor">.producer</span><span class="hljs-preprocessor">.ProducerRecord</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.clients</span><span class="hljs-preprocessor">.producer</span><span class="hljs-preprocessor">.RecordMetadata</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.slf</span>4j<span class="hljs-preprocessor">.Logger</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.slf</span>4j<span class="hljs-preprocessor">.LoggerFactory</span><span class="hljs-comment">;</span>

public class KafkaProducerService {
    private static Logger LOG = LoggerFactory
            <span class="hljs-preprocessor">.getLogger</span>(KafkaProducerService<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;</span>

    public static void main(String[] args) {
        Properties props = new Properties()<span class="hljs-comment">;</span>
        props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"bootstrap.servers"</span>, <span class="hljs-string">"192.168.1.1:9092,192.168.1.2:9092,192.168.1.3:9092"</span>)<span class="hljs-comment">;</span>
        props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"retries"</span>, <span class="hljs-number">3</span>)<span class="hljs-comment">;</span>
        props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"linger.ms"</span>, <span class="hljs-number">1</span>)<span class="hljs-comment">;</span>
        props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"key.serializer"</span>,
                <span class="hljs-string">"org.apache.kafka.common.serialization.StringSerializer"</span>)<span class="hljs-comment">;</span>
        props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"value.serializer"</span>,
                <span class="hljs-string">"org.apache.kafka.common.serialization.StringSerializer"</span>)<span class="hljs-comment">;</span>
        KafkaProducer&lt;String, String&gt; producer = new KafkaProducer&lt;String, String&gt;(
                props)<span class="hljs-comment">;</span>
        for (int i = <span class="hljs-number">0</span><span class="hljs-comment">; i &lt; 1; i++) {</span>
            ProducerRecord&lt;String, String&gt; record = new ProducerRecord&lt;String, String&gt;(
                    <span class="hljs-string">"tests"</span>, <span class="hljs-string">"11"</span>, <span class="hljs-string">"今天天气不错哟yoyo=======&gt;"</span> + i)<span class="hljs-comment">;</span>
            producer<span class="hljs-preprocessor">.send</span>(record, new Callback() {
                @Override
                public void onCompletion(RecordMetadata metadata, Exception e) {
                    // TODO Auto-generated method stub
                    if (e != null)
                        System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"the producer has a error:"</span>
                                + e<span class="hljs-preprocessor">.getMessage</span>())<span class="hljs-comment">;</span>
                    else {
                        System<span class="hljs-preprocessor">.out</span>
                                <span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"The offset of the record we just sent is: "</span>
                                        + metadata<span class="hljs-preprocessor">.offset</span>())<span class="hljs-comment">;</span>
                        System<span class="hljs-preprocessor">.out</span>
                                <span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"The partition of the record we just sent is: "</span>
                                        + metadata<span class="hljs-preprocessor">.partition</span>())<span class="hljs-comment">;</span>
                    }
                }
            })<span class="hljs-comment">;</span>
            try {
                Thread<span class="hljs-preprocessor">.sleep</span>(<span class="hljs-number">1000</span>)<span class="hljs-comment">;</span>
                // producer<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>
            } catch (InterruptedException e1) {
                // TODO Auto-generated catch block
                e1<span class="hljs-preprocessor">.printStackTrace</span>()<span class="hljs-comment">;</span>
            }
        }
    }
}</code></pre>

<p>其次，创建消费者代码：</p>



<pre class="prettyprint"><code class=" hljs avrasm">package <span class="hljs-keyword">com</span><span class="hljs-comment">;</span>

import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.ArrayList</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Arrays</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.List</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Properties</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.clients</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.ConsumerRecord</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.clients</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.ConsumerRecords</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.clients</span><span class="hljs-preprocessor">.consumer</span><span class="hljs-preprocessor">.KafkaConsumer</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.slf</span>4j<span class="hljs-preprocessor">.Logger</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.slf</span>4j<span class="hljs-preprocessor">.LoggerFactory</span><span class="hljs-comment">;</span>

<span class="hljs-comment">/**
 * @author Joker
 * 自己控制偏移量提交
 * 很多时候，我们是希望在获得消息并经过一些逻辑处理后，才认为该消息已被消费，这可以通过自己控制偏移量提交来实现。
 */</span>
public class ManualOffsetConsumer {
    private static Logger LOG = LoggerFactory<span class="hljs-preprocessor">.getLogger</span>(ManualOffsetConsumer<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;</span>

    public static void main(String[] args) {
        // TODO Auto-generated method stub
        Properties props = new Properties()<span class="hljs-comment">;</span>
        //设置brokerServer(kafka)ip地址
        props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"bootstrap.servers"</span>, <span class="hljs-string">"192.168.1.1:9092,192.168.1.2:9092,192.168.1.3:9092"</span>)<span class="hljs-comment">;</span>
        //设置consumer group name
        props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"group.id"</span>,<span class="hljs-string">"mygroup11"</span>)<span class="hljs-comment">;</span>
        props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"enable.auto.commit"</span>, <span class="hljs-string">"false"</span>)<span class="hljs-comment">;</span>
        //设置使用最开始的offset偏移量为该group<span class="hljs-preprocessor">.id</span>的最早。如果不设置，则会是latest即该topic最新一个消息的offset
        //如果采用latest，消费者只能得道其启动后，生产者生产的消息
        props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"auto.offset.reset"</span>, <span class="hljs-string">"earliest"</span>)<span class="hljs-comment">;</span>
      //设置心跳时间
        props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"session.timeout.ms"</span>, <span class="hljs-string">"30000"</span>)<span class="hljs-comment">;</span>
        props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"key.deserializer"</span>, <span class="hljs-string">"org.apache.kafka.common.serialization.StringDeserializer"</span>)<span class="hljs-comment">;</span>
        props<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"value.deserializer"</span>, <span class="hljs-string">"org.apache.kafka.common.serialization.StringDeserializer"</span>)<span class="hljs-comment">;</span>
        KafkaConsumer&lt;String ,String&gt; consumer = new KafkaConsumer&lt;String ,String&gt;(props)<span class="hljs-comment">;</span>
        consumer<span class="hljs-preprocessor">.subscribe</span>(Arrays<span class="hljs-preprocessor">.asList</span>(<span class="hljs-string">"test"</span>))<span class="hljs-comment">;</span>
        final int minBatchSize = <span class="hljs-number">5</span><span class="hljs-comment">;  //批量提交数量</span>
         List&lt;ConsumerRecord&lt;String, String&gt;&gt; buffer = new ArrayList&lt;&gt;()<span class="hljs-comment">;</span>
         while (true) {
             ConsumerRecords&lt;String, String&gt; records = consumer<span class="hljs-preprocessor">.poll</span>(<span class="hljs-number">100</span>)<span class="hljs-comment">;</span>
             for (ConsumerRecord&lt;String, String&gt; record : records) {
                 System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"consumer message values is "</span>+record<span class="hljs-preprocessor">.value</span>()+<span class="hljs-string">" and the offset is "</span>+ record<span class="hljs-preprocessor">.offset</span>())<span class="hljs-comment">;</span>
                 buffer<span class="hljs-preprocessor">.add</span>(record)<span class="hljs-comment">;</span>
             }
             if (buffer<span class="hljs-preprocessor">.size</span>() &gt;= minBatchSize) {
                 System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"now commit offset"</span>+buffer<span class="hljs-preprocessor">.size</span>())<span class="hljs-comment">;</span>
                 consumer<span class="hljs-preprocessor">.commitSync</span>()<span class="hljs-comment">;</span>
                 buffer<span class="hljs-preprocessor">.clear</span>()<span class="hljs-comment">;</span>
             }
         }
    }
}
</code></pre>

<p>如此便是java客户端的一个生产者以及消费者的例子，例子中消费者只是利用了一个简单的main方面进行测试，在正常使用中必然要使用线程去监听消费队列的，而在生产这方面也是要利用线程去提交数据，保障主线程的畅通等等，这点根据自己的业务需要进行完善了，本人这里也只是抛砖引玉罢了。最后，本文都是基于本人实际搭建过程的心得，若有错误欢迎指正。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>