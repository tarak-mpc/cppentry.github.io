---
layout:     post
title:      Flume，Kafka与Storm整合
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/shen_jz2012/article/details/43701507				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>本文参考文章：http://www.cnblogs.com/lion.net/p/3922960.html#top 以及各种官方文档。</p>
<p><br></p>
<p><br></p>
1、Kafka与Storm的整合<br>
     <br>
     Kafka与storm的整合就是用Kafka的消费者实现为为storm的数据源spout，用Kafka的stream数据作为storm中spout的数据源。<br>
    在eclipse中添加了Kafka和storm的lib下的所有的包，但是有几个log4j的包分别有Kafka和storm的不同版本而导致程序运行出错，删掉重复而不同版本的包即可。写程序的时候主要用到的jar包要有：<span style="color:#ff0000;">kafka_2.10-0.8.2-beta.jar，metrics-core-2.2.0.jar，scala-library-2.10.1.jar，zkclient-0.3.jar，storm-core-0.9.3.jar</span>。最好将这kafka和storm目录下的lib包全部导进来，否则会出现ClassNotFoundError异常。<br>
     <br><p>     Spout主要配置消费者，然后从消费者数据流中把数据取出来，用一个while循环不断emit发送数据给下游的bolt。</p>
<p></p>
<pre><code class="language-java">package com.mrshen.cvlab.scut;

import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.Properties;


import kafka.consumer.ConsumerConfig;
import kafka.consumer.ConsumerIterator;
import kafka.consumer.KafkaStream;
import kafka.javaapi.consumer.ConsumerConnector;
import backtype.storm.spout.SpoutOutputCollector;
import backtype.storm.task.TopologyContext;
import backtype.storm.topology.IRichSpout;
import backtype.storm.topology.OutputFieldsDeclarer;
import backtype.storm.tuple.Fields;
import backtype.storm.tuple.Values;

public class KafkaSpout implements IRichSpout{

	private SpoutOutputCollector collector;
	private ConsumerConnector consumer;
	private String topic;
	
	
	public KafkaSpout() {
		super();
	}

	
	public KafkaSpout(String topic) {
		super();
		this.topic = topic;
	}


	@Override
	public void ack(Object arg0) {
		// TODO Auto-generated method stub
		
	}

	@Override
	public void activate() {
		// TODO Auto-generated method stub
		consumer = kafka.consumer.Consumer.createJavaConsumerConnector(ConsumerConfig());
		Map&lt;String , Integer&gt; topicMap = new HashMap&lt;String, Integer&gt;();
		topicMap.put(topic, 1);
		
		System.out.println("**************topic***************: " + topic);
		
		Map&lt;String, List&lt;KafkaStream&lt;byte[], byte[]&gt;&gt;&gt; streamMap = consumer.createMessageStreams(topicMap);
		KafkaStream&lt;byte[], byte[]&gt;stream = streamMap.get(topic).get(0);
		ConsumerIterator&lt;byte[], byte[]&gt; iterator = stream.iterator();
		
		while(iterator.hasNext()) {
			String value = new String(iterator.next().message());
			
			System.out.println("Kafka ---&gt; storm: " + value);
			
			collector.emit(new Values(value), value);
		}
	}

	private ConsumerConfig ConsumerConfig() {
		// TODO Auto-generated method stub
		Properties properties = new Properties();
		properties.put("zookeeper.connect", "storm:2181");
		properties.put("group.id", "0");
		properties.put("auto.commit.interval.ms", "1000");
		properties.put("num.partitioners", "1");
		properties.put("zookeeper.session.timeout.ms", "10000");
		
		return new ConsumerConfig(properties);
	}


	@Override
	public void close() {
		// TODO Auto-generated method stub
		
	}

	@Override
	public void deactivate() {
		// TODO Auto-generated method stub
		
	}

	@Override
	public void fail(Object arg0) {
		// TODO Auto-generated method stub
		
	}

	@Override
	public void nextTuple() {
		// TODO Auto-generated method stub
		
	}

	@Override
	public void open(Map arg0, TopologyContext arg1, SpoutOutputCollector arg2) {
		// TODO Auto-generated method stub
		this.collector = arg2;
	}

	@Override
	public void declareOutputFields(OutputFieldsDeclarer arg0) {
		// TODO Auto-generated method stub
		arg0.declare(new Fields("word"));
	}

	@Override
	public Map&lt;String, Object&gt; getComponentConfiguration() {
		// TODO Auto-generated method stub
		System.out.println("getComponentConfiguration is being calling");
		topic = "Topic";
		return null;
	}
}</code></pre><br><br><p></p>
<p>     两个bolt都只是做了简单的添加字符串，然后打印出来。</p>
<p></p>
<pre><code class="language-java">package com.mrshen.cvlab.scut;

import java.util.HashMap;
import java.util.Map;

import backtype.storm.Config;
import backtype.storm.LocalCluster;
import backtype.storm.topology.BasicOutputCollector;
import backtype.storm.topology.OutputFieldsDeclarer;
import backtype.storm.topology.TopologyBuilder;
import backtype.storm.topology.base.BaseBasicBolt;
import backtype.storm.tuple.Fields;
import backtype.storm.tuple.Tuple;
import backtype.storm.tuple.Values;
import backtype.storm.utils.Utils;

public class KafkaTopology {
	
	public static void main(String[] args) {
		TopologyBuilder builder = new TopologyBuilder();
		
		builder.setSpout("spout", new KafkaSpout(), 1);
		builder.setBolt("bolt1", new Bolt1(), 2).shuffleGrouping("spout");
		builder.setBolt("bolt2", new Bolt2(), 2).fieldsGrouping("bolt1", new Fields("word"));
		
		Map config = new HashMap();
		config.put(Config.TOPOLOGY_WORKERS, 1);
		config.put(Config.TOPOLOGY_DEBUG, false);
		
		LocalCluster cluster = new LocalCluster();
		cluster.submitTopology("kafkaTop4Storm", config, builder.createTopology());
		
		Utils.sleep(1000*60*5);
		cluster.shutdown();
	}
	
	public static class Bolt1 extends BaseBasicBolt {

		@Override
		public void execute(Tuple arg0, BasicOutputCollector arg1) {
			// TODO Auto-generated method stub
			try {
				String msg = arg0.getString(0);
				System.out.println("words in botl1======&gt;" + msg);
				msg = msg + " @bolt1";
				if(msg != null) {
					arg1.emit(new Values(msg));
				}
			} catch (Exception e) {
				// TODO: handle exception
				e.printStackTrace();
			}
		}

		@Override
		public void declareOutputFields(OutputFieldsDeclarer arg0) {
			// TODO Auto-generated method stub
			arg0.declare(new Fields("word"));
		}
		
	}
	
	public static class Bolt2 extends BaseBasicBolt {

		Map&lt;String , Integer&gt; counts = new HashMap&lt;String, Integer&gt;();
		
		@Override
		public void execute(Tuple arg0, BasicOutputCollector arg1) {
			// TODO Auto-generated method stub
			String msg = arg0.getString(0);
			System.out.println("words int bolt2=======&gt;" + msg);
			msg = msg + " @bolt2";
			arg1.emit(new Values(msg, 1));
		}

		@Override
		public void declareOutputFields(OutputFieldsDeclarer arg0) {
			// TODO Auto-generated method stub
			arg0.declare(new Fields("word", "count"));
		}
		
	}
}</code></pre><br><br><p></p>
     运行<br><p>     首先在终端开启zookeeper，Kafka服务。然后开启生产者，往上面spout文件中定义的topic写入数据，最后在eclipse中运行KafkaTopology程序。结果如下：</p>
<p><br></p>
<p style="text-align:center;"><img src="https://img-blog.csdn.net/20150210133528756?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hlbl9qejIwMTI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><br></p>
<p><br></p>
2、Flume，Kafka与storm的整合<br><br>
     包括上面我们已经做了flume2Kafka，Kafka2Storm的整合工作，而三者的整合就是将上述KafkaTopology打包成jar包提交到storm上去运行。同时别忘了将写好的KafkaSink类打包成jar放在flume的lib下。<br>
     运行的步骤如下：<br>
     1）开启zookeeper<br>
     2）开启storm集群，包括nimbus，superviosr，ui<br>
     3）开启Kafka服务<br>
     4）开启一个flume agent（配置文件较于之前的有点区别，为了能显示批量发送和显示我将agent的source.type从syslog改为avro，端口号也改过）<br>
     5）开启一个flume 的avro-client发送数据<br>
     6）用storm的jar参数运行提交的jar包<br>
     7）观察结果，如果4）中的agent不断输出数据，证明flume到Kafka连通；如果6）中控制台不断输出bolt中处理过的数据，说明Kafka到storm中的数据也是连通的，从而三者整合成功。<br>
     <br><p>     agent的配置：</p>
<p><br></p>
<p style="text-align:center;"><img src="https://img-blog.csdn.net/20150210133704181?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hlbl9qejIwMTI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><br></p>
<p>     1）2）3）4）步运行起来之后，现在打开一个avro-client发送数据：</p>
<p><br></p>
<p style="text-align:center;"><img src="https://img-blog.csdn.net/20150210133745659?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hlbl9qejIwMTI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><br></p>
<p>     在4）的控制台看到数据如下（数据保密，已马赛克）：</p>
<p><br></p>
<p style="text-align:center;"><img src="https://img-blog.csdn.net/20150210134022099?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hlbl9qejIwMTI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><br></p>
<p>     再看一下6）中的结果：</p>
<p><br></p>
<p style="text-align:center;"><img src="https://img-blog.csdn.net/20150210134107836?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2hlbl9qejIwMTI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><br></p>
     运行成功，至此三者整合结束。
            </div>
                </div>