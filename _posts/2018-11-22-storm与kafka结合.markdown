---
layout:     post
title:      storm与kafka结合
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u013360022/article/details/50553190				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;"><strong>一、kafka基本概念</strong></span></p>
<p><span style="font-size:14px;">      1、kafka是一个分布式的消息<span style="color:#ff0000;"><strong>缓存</strong></span>系统<br>
      2、kafka集群中的服务器都叫做<span style="color:#ff0000;"><strong>broker</strong></span><br>
      3、kafka有两类客户端，一类叫<span style="color:#ff0000;"><strong>producer</strong></span>（消息生产者），一类叫做<strong><span style="color:#ff0000;">consumer</span></strong>（消息消费者），客户端和broker服务器之间采用tcp协议连接<br>
      4、kafka中不同业务系统的消息可以通过<strong><span style="color:#ff0000;">topic</span></strong>进行区分，而且每一个消息topic都会被分区，以分担消息读写的负载<br>
      5、每一个分区都可以有多个副本，以防止数据的丢失<br>
      6、某一个分区中的数据如果需要更新，都必须通过该分区所有副本中的<span style="color:#ff0000;"><strong>leader</strong></span>来更新<br>
      7、消费者可以分组，比如有两个消费者组A和B，共同消费一个topic：order_info,A和B所消费的消息不会重复<br>
           比如 order_info 中有100个消息，每个消息有一个id,编号从0-99，那么，如果A组消费0-49号，B组就消费50-99号<br>
      8、消费者在具体消费某个topic中的消息时，可以指定起始偏移量</span></p>
<p><span style="font-size:14px;"><img src="https://img-blog.csdn.net/20160121103508165?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="font-size:14px;"><img src="https://img-blog.csdn.net/20160121105335584?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:18px;"><strong>二、kafka集群安装</strong></span></p>
<p><span style="font-size:14px;">1、解压</span></p>
<p><span style="font-size:14px;"><br>
2、修改server.properties<br>
broker.id=1<br>
zookeeper.connect=weekend05:2181,weekend06:2181,weekend07:2181<br><br>
3、将zookeeper集群启动<br><br>
4、在每一台节点上启动broker<br>
bin/kafka-server-start.sh config/server.properties<br><br><br>
5、在kafka集群中创建一个topic<br>
bin/kafka-topics.sh --create --zookeeper weekend05:2181 --replication-factor 3 --partitions 1 --topic order<br><br><br>
6、用一个producer向某一个topic中写入消息<br>
bin/kafka-console-producer.sh --broker-list weekend:9092 --topic order<br><br><br>
7、用一个comsumer从某一个topic中读取信息<br>
bin/kafka-console-consumer.sh --zookeeper weekend05:2181 --from-beginning --topic order<br><br><br>
8、查看一个topic的分区及副本状态信息<br>
bin/kafka-topics.sh --describe --zookeeper weekend05:2181 --topic order</span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:18px;"><strong>三、kafka与storm结合</strong></span></p>
<p><span style="font-size:14px;">首先来看一张业务流程图</span></p>
<p><span style="font-size:14px;"><img src="https://img-blog.csdn.net/20160121110449537?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="font-size:14px;">这里，flume作为生产者客户端，storm作为消费者客户端</span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;">例子：<span style="color:rgb(68,68,68);font-family:Verdana, Geneva, sans-serif;font-size:15px;line-height:25.5px;background-color:rgb(255,255,255);">下面，我们开发了一个简单WordCount示例程序，从Kafka读取订阅的消息行，通过空格拆分出单个单词，然后再做词频统计计算</span></span></p>
<p><span style="font-size:14px;"></span></p>
<pre><code class="language-java">import java.util.Arrays;
import java.util.HashMap;
import java.util.Iterator;
import java.util.Map;
import java.util.Map.Entry;
import java.util.concurrent.atomic.AtomicInteger;

import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;

import storm.kafka.BrokerHosts;//导入storm-kafka-0.9.2-incubating.jar包
import storm.kafka.KafkaSpout;
import storm.kafka.SpoutConfig;
import storm.kafka.StringScheme;
import storm.kafka.ZkHosts;
import backtype.storm.Config;
import backtype.storm.LocalCluster;
import backtype.storm.StormSubmitter;
import backtype.storm.generated.AlreadyAliveException;
import backtype.storm.generated.InvalidTopologyException;
import backtype.storm.spout.SchemeAsMultiScheme;
import backtype.storm.task.OutputCollector;
import backtype.storm.task.TopologyContext;
import backtype.storm.topology.OutputFieldsDeclarer;
import backtype.storm.topology.TopologyBuilder;
import backtype.storm.topology.base.BaseRichBolt;
import backtype.storm.tuple.Fields;
import backtype.storm.tuple.Tuple;
import backtype.storm.tuple.Values;

public class MyKafkaTopology {

     public static class KafkaWordSplitter extends BaseRichBolt {

          private static final Log LOG = LogFactory.getLog(KafkaWordSplitter.class);
          private static final long serialVersionUID = 886149197481637894L;
          private OutputCollector collector;
         
          @Override
          public void prepare(Map stormConf, TopologyContext context,
                    OutputCollector collector) {
               this.collector = collector;              
          }

          @Override
          public void execute(Tuple input) {
               String line = input.getString(0);
               LOG.info("RECV[kafka -&gt; splitter] " + line);
               String[] words = line.split("\\s+");
               for(String word : words) {
                    LOG.info("EMIT[splitter -&gt; counter] " + word);
                    collector.emit(input, new Values(word, 1));
               }
               collector.ack(input);
          }

          @Override
          public void declareOutputFields(OutputFieldsDeclarer declarer) {
               declarer.declare(new Fields("word", "count"));         
          }
         
     }
    
     public static class WordCounter extends BaseRichBolt {

          private static final Log LOG = LogFactory.getLog(WordCounter.class);
          private static final long serialVersionUID = 886149197481637894L;
          private OutputCollector collector;
          private Map&lt;String, AtomicInteger&gt; counterMap;
         
          @Override
          public void prepare(Map stormConf, TopologyContext context,
                    OutputCollector collector) {
               this.collector = collector;    
               this.counterMap = new HashMap&lt;String, AtomicInteger&gt;();
          }

          @Override
          public void execute(Tuple input) {
               String word = input.getString(0);
               int count = input.getInteger(1);
               LOG.info("RECV[splitter -&gt; counter] " + word + " : " + count);
               AtomicInteger ai = this.counterMap.get(word);
               if(ai == null) {
                    ai = new AtomicInteger();
                    this.counterMap.put(word, ai);
               }
               ai.addAndGet(count);
               collector.ack(input);
               LOG.info("CHECK statistics map: " + this.counterMap);
          }

          @Override
          public void cleanup() {
               LOG.info("The final result:");
               Iterator&lt;Entry&lt;String, AtomicInteger&gt;&gt; iter = this.counterMap.entrySet().iterator();
               while(iter.hasNext()) {
                    Entry&lt;String, AtomicInteger&gt; entry = iter.next();
                    LOG.info(entry.getKey() + "\t:\t" + entry.getValue().get());
               }
              
          }

          @Override
          public void declareOutputFields(OutputFieldsDeclarer declarer) {
               declarer.declare(new Fields("word", "count"));         
          }
     }
    
     public static void main(String[] args) throws AlreadyAliveException, InvalidTopologyException, InterruptedException {
          String zks = "h1:2181,h2:2181,h3:2181";
          String topic = "my-replicated-topic5";
          String zkRoot = "/storm"; // default zookeeper root configuration for storm
          String id = "word";
         
          BrokerHosts brokerHosts = new ZkHosts(zks);
          SpoutConfig spoutConf = new SpoutConfig(brokerHosts, topic, zkRoot, id);
          spoutConf.scheme = new SchemeAsMultiScheme(new StringScheme());
          spoutConf.forceFromStart = false;//该配置是指，如果该Topology因故障停止处理，下次正常运行时是否从Spout对应数据源Kafka//中的该订阅Topic的起始位置开始读取，如果forceFromStart=true，则之前处理过的Tuple还要重新处理一遍，否则会从上次处理的位置//继续处理，保证Kafka中的Topic数据不被重复处理，是在数据源的位置进行状态记录
          spoutConf.zkServers = Arrays.asList(new String[] {"h1", "h2", "h3"});
          spoutConf.zkPort = 2181;
         
          TopologyBuilder builder = new TopologyBuilder();
          builder.setSpout("kafka-reader", new KafkaSpout(spoutConf), 5); // Kafka我们创建了一个5分区的Topic，这里并行度设置为5
          builder.setBolt("word-splitter", new KafkaWordSplitter(), 2).shuffleGrouping("kafka-reader");
          builder.setBolt("word-counter", new WordCounter()).fieldsGrouping("word-splitter", new Fields("word"));
         
          Config conf = new Config();
         
          String name = MyKafkaTopology.class.getSimpleName();
          if (args != null &amp;&amp; args.length &gt; 0) {
               // Nimbus host name passed from command line
               conf.put(Config.NIMBUS_HOST, args[0]);
               conf.setNumWorkers(3);
               StormSubmitter.submitTopologyWithProgressBar(name, conf, builder.createTopology());
          } else {
               conf.setMaxTaskParallelism(3);
               LocalCluster cluster = new LocalCluster();
               cluster.submitTopology(name, conf, builder.createTopology());
               Thread.sleep(60000);
               cluster.shutdown();
          }
     }
}</code></pre><br><span style="color:rgb(68,68,68);font-family:Verdana, Geneva, sans-serif;font-size:15px;line-height:25.5px;background-color:rgb(255,255,255);">可以通过查看日志文件（logs/目录下）或者Storm UI来监控Topology的运行状况。如果程序没有错误，可以使用前面我们使用的Kafka Producer来生成消息，就能看到我们开发的Storm
 Topology能够实时接收到并进行处理</span><br><p></p>
<p><span style="font-size:14px;"><strong>四、kafka学习网址</strong></span></p>
<p><span style="font-size:14px;">学习官网：http://kafka.apache.org/documentation.html#introduction<br></span></p>
<p><span style="font-size:14px;">storm+kafka+hdfs: http://shiyanjun.cn/archives/934.html</span></p>
            </div>
                </div>