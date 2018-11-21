---
layout:     post
title:      Kafka AdminClient 管理Kafka Offset代码实现
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/lisi1129/article/details/72869194				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-family:'Microsoft YaHei';font-size:18px;">一：offset保存位置</span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:18px;">     kafka的offset管理，可以交给zookeeper实现，经过配置offsets.storage=zookeeper；也可以直接跟kafka自己管理，在你 的配置文件设置下面的配置项；</span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:18px;">offsets.storage=kafka </span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:18px;">dual.commit.enabled=true </span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:18px;">kafka自己管理自己的offset，并经过命令可以查看<br></span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:18px;">bin/kafka-consumer-groups.sh --bootstrap-server broker1:9092 --describe --group kafkatest<br></span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:18px;"><img src="https://img-blog.csdn.net/20170606181956783" alt=""><br></span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:18px;"> 如上图可以查看到group中每个消费者对应topic的patition,当前消费的offset;每个partition中最大的offset等信息；经过测试，当没有消费者进行消费时候，若执行上面命令会出现下面的现象：消费者不见了<br></span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:18px;"><img src="https://img-blog.csdn.net/20170606181939576" alt=""><br></span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:18px;">以上就是用kafka命令获取的结果；</span></p>
<p><span style="font-family:'Microsoft YaHei';"><span style="font-size:18px;">下面是经过查看借鉴源码，用Java API实现的offset管理（kafka 版本为0.10）</span></span></p>
<p><span style="font-family:'Microsoft YaHei';"><span style="font-size:18px;">&lt;dependency&gt;<br>
    &lt;groupId&gt;org.apache.kafka&lt;/groupId&gt;<br>
    &lt;artifactId&gt;kafka_2.10&lt;/artifactId&gt;<br>
    &lt;version&gt;0.10.2.1&lt;/version&gt;<br>
&lt;/dependency&gt;<br>
&lt;dependency&gt;<br>
    &lt;groupId&gt;org.apache.kafka&lt;/groupId&gt;<br>
    &lt;artifactId&gt;kafka-clients&lt;/artifactId&gt;<br>
    &lt;version&gt;0.10.2.1&lt;/version&gt;<br>
&lt;/dependency&gt;<br></span></span></p>
<p><span style="font-family:'Microsoft YaHei';"><span style="font-size:18px;">测试类代码如下：</span></span></p>
<p><span style="font-family:'Microsoft YaHei';"><span style="font-size:18px;"></span></span></p><pre><code class="language-java">public static void main(String[] args) throws InstantiationException, IllegalAccessException {
	  Properties props = new Properties();
	  props.put(CommonClientConfigs.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
	  AdminClient adminClient = AdminClient.create(props);
	  ConsumerGroupSummary  consumerGroupSummary =  adminClient.describeConsumerGroup("kafkatest");
	  if(consumerGroupSummary.state().equals("Empty")){
		  System.out.println("niaho");
	  }
	  Option&lt;List&lt;ConsumerSummary&gt;&gt; consumerSummaryOption =  consumerGroupSummary.consumers();

	  List&lt;ConsumerSummary&gt; ConsumerSummarys = consumerSummaryOption.get();//获取组中的消费者
	  KafkaConsumer consumer = getNewConsumer();
	  for(int i=0;i&lt;ConsumerSummarys.size();i++){ //循环组中的每一个消费者
		  
		  ConsumerSummary consumerSummary = ConsumerSummarys.apply(i);
		  String consumerId  = consumerSummary.consumerId();//获取消费者的id
		  scala.collection.immutable.Map&lt;TopicPartition, Object&gt; maps = 
				  adminClient.listGroupOffsets("kafkatest");//或者这个组消费的所有topic，partition和当前消费到的offset
		  List&lt;TopicPartition&gt; topicPartitions= consumerSummary.assignment();//获取这个消费者下面的所有topic和partion
		  for(int j =0;j&lt; topicPartitions.size();j++){ //循环获取每一个topic和partion
			  TopicPartition topicPartition = topicPartitions.apply(j);
			  String CURRENToFFSET = maps.get(topicPartition).get().toString();
			  long endOffset =getLogEndOffset(topicPartition);
			  System.out.println("topic的名字为："+topicPartition.topic()+"====分区为："+topicPartition.partition()+"===目前消费offset为："+CURRENToFFSET+"===,此分区最后offset为："+endOffset);
		  }
	  }
}</code></pre>引用的两个方法代码如下：
<p><span style="font-family:'Microsoft YaHei';"><span style="font-size:18px;">获取消费者</span></span></p>
<p><span style="font-family:'Microsoft YaHei';"><span style="font-size:18px;"></span></span></p><pre><code class="language-java">public static KafkaConsumer getNewConsumer(){
	     Properties props = new Properties();
	     props.put("bootstrap.servers", "localhost:9092");
	     props.put("group.id", "kafkatest");
	     props.put("enable.auto.commit", "true");
	     props.put("auto.offset.reset", "earliest");
	     props.put("auto.commit.interval.ms", "1000");
	     props.put("auto.commit.interval.ms", "1000");
	     props.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
	     props.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer");
	     KafkaConsumer&lt;String, String&gt; consumer = new KafkaConsumer&lt;&gt;(props);
	     return consumer;
  }</code></pre>获取最后的offset:
<p><span style="font-family:'Microsoft YaHei';"><span style="font-size:18px;"></span></span></p><pre><code class="language-java">public static long getLogEndOffset(TopicPartition topicPartition){
	  KafkaConsumer&lt;String, String&gt; consumer= getNewConsumer();
	  consumer.assign(Arrays.asList(topicPartition));
	  consumer.seekToEnd(Arrays.asList(topicPartition));
	  long endOffset = consumer.position(topicPartition);
	  return endOffset;
  }</code></pre>运行后，可以获取结果如下：<br><img src="https://img-blog.csdn.net/20170607091645966" alt=""><br><p><span style="font-family:'Microsoft YaHei';"><span style="font-size:18px;"><br></span></span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:18px;"><br></span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:18px;"><br></span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:18px;"><br></span></p>
            </div>
                </div>