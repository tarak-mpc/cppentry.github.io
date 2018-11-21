---
layout:     post
title:      kafka+storm初探
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>由于接触kafka和storm的时间不长，本文只是备忘。</p>
<p><span></span>关于kafka和storm的介绍不多说，官网有详尽的介绍，这里就是kafka+storm的具体案例</p>
<p><span></span>storm是一个实时的流式处理，主要是来源(Spout)、线路(Topology)、流向(Bolt)。</p>
<p>一、从kafka中读取数据，在storm中处理。既然从kafka中读取数据，spout的具体实现肯定是kafka实现spout即KafkaSpout类，此类中肯定有kafka的配置信息。所以不难推断出其逻辑。。</p>
<pre><code class="language-java">public static void main(String[] args) throws InterruptedException, AlreadyAliveException, InvalidTopologyException {
		TopologyBuilder builder = new TopologyBuilder();
		builder.setSpout("ksSpout", new KafkaSpout(getSpoutConfig()), 1);
		builder.setBolt("myBolt", new BoltTest(), 1).shuffleGrouping("ksSpout");
		builder.setBolt("ksBolt", new mykafkaBolt(), 1).shuffleGrouping("myBolt");

		Config conf = new Config();
		conf.setDebug(true);
		conf.setMaxTaskParallelism(1);
		// conf.put(arg0, arg1);

		Map&lt;String, String&gt; map = new HashMap&lt;String, String&gt;();
		// 配置Kafka broker地址
		map.put("metadata.broker.list", "hadoop3:2181");
		// serializer.class为消息的序列化类
		map.put("serializer.class", "kafka.serializer.StringEncoder");
		conf.put(TridentKafkaState.KAFKA_BROKER_PROPERTIES, map);

		LocalCluster local = new LocalCluster();
		
		//StormSubmitter.submitTopologyWithProgressBar(args[0], conf, builder.createTopology());
		local.submitTopology("kafkaStorm", conf, builder.createTopology());
		Thread.sleep(100000);
		local.shutdown();
	}

	public static SpoutConfig getSpoutConfig() {
		// 设置kafka的zookeeper集群
		BrokerHosts hosts = new ZkHosts("hadoop3:2181");
		// 配置Kafka订阅的Topic，以及zookeeper中数据节点目录和名字
		SpoutConfig conf = new SpoutConfig(hosts, "topic1_test", "/brokers","apooutID");
		// 在topology中设置spout
		conf.scheme = new SchemeAsMultiScheme(new MessageScheme());
		return conf;
	}

}

class MessageScheme implements Scheme {

	public List&lt;Object&gt; deserialize(byte[] ser) {
		try {
			String msg = new String(ser, "UTF-8");
			return new Values(msg+"");
		} catch (UnsupportedEncodingException e) {
			e.printStackTrace();
		}
		return null;
	}

	public Fields getOutputFields() {
		// TODO Auto-generated method stub
		return new Fields("msg");
	}
}

class BoltTest extends BaseBasicBolt{

	@Override
	public void execute(Tuple tuple, BasicOutputCollector collector) {
		String msg=tuple.getString(0);
		System.out.println(msg);
		collector.emit(new Values(msg));
		
	}

	@Override
	public void declareOutputFields(OutputFieldsDeclarer declarer) {
		declarer.declare(new Fields("msg"));
		
	}
	
}</code></pre>
<p></p>
<p>二、写人kafka中，consumer消费。</p>
<p></p><pre><code class="language-java">public class TestStorm {

	public static void main(String[] args) throws Exception {
		TopologyBuilder builder = new TopologyBuilder();
		builder.setSpout("testSpout", new TestWordSpout(), 2);
		builder.setBolt("testBolt", new TestWordBolt(), 2).shuffleGrouping( "testSpout");
		
		KafkaBolt&lt;String, String&gt; kafkaBolt = new KafkaBolt&lt;String, String&gt;();
		kafkaBolt.withTopicSelector(new storm.kafka.bolt.selector.DefaultTopicSelector("topic1_test"))
		.withTupleToKafkaMapper(new FieldNameBasedTupleToKafkaMapper&lt;String, String&gt;());
		
		builder.setBolt("kafak",kafkaBolt , 1).shuffleGrouping( "testBolt");

		Config conf = new Config();
		conf.setDebug(true);
		conf.setMaxTaskParallelism(3);
		LocalCluster local = new LocalCluster();
		
		
		 //3. 设置kafka producer的配置
       Properties props = new Properties();
       props.put("metadata.broker.list", "hadoop3:2181");
       props.put("producer.type","async");
       props.put("request.required.acks", "0"); // 0 ,-1 ,1
       props.put("serializer.class", "kafka.serializer.StringEncoder");
       conf.put(TridentKafkaState.KAFKA_BROKER_PROPERTIES, props);
		
		local.submitTopology("testStorm", conf, builder.createTopology());
		Thread.sleep(100000);
		local.shutdown();
	}
}

class TestWordSpout extends BaseRichSpout {
	/**
	 * 
	 */
	private static final long serialVersionUID = -5375148193042273211L;

	private SpoutOutputCollector collector;
	private static String[] info = new String[] {
			"comaple\t,12424,44w46,654,12424,44w46,654,",
			"lisi\t,435435,6537,12424,44w46,654,",
			"lipeng\t,45735,6757,12424,44w46,654,",
			"hujintao\t,45735,6757,12424,44w46,654,",
			"jiangmin\t,23545,6457,2455,7576,qr44453",
			"beijing\t,435435,6537,12424,44w46,654,",
			"xiaoming\t,46654,8579,w3675,85877,077998,",
			"xiaozhang\t,9789,788,97978,656,345235,09889,",
			"ceo\t,46654,8579,w3675,85877,077998,",
			"cto\t,46654,8579,w3675,85877,077998,",
			"zhansan\t,46654,8579,w3675,85877,077998," };
	Random random = new Random();

	@Override
	public void declareOutputFields(OutputFieldsDeclarer declarer) {
		declarer.declare(new Fields("key", "message"));

	}

	@Override
	public void nextTuple() {
		System.out.println("*****************");
		Utils.sleep(1000);
		String msg = info[random.nextInt(11)];
		collector.emit(new Values(msg,"testv"));
	}

	@Override
	public void open(Map map, TopologyContext contxt,
			SpoutOutputCollector collector) {
		this.collector = collector;
	}

}

class TestWordBolt extends BaseBasicBolt {

	/**
	 * 
	 */
	private static final long serialVersionUID = 1611510001266293355L;

	@Override
	public void declareOutputFields(OutputFieldsDeclarer declarer) {
		declarer.declare(new Fields("key", "message"));

	}

	@Override
	public void execute(Tuple tuple, BasicOutputCollector collector) {
		String msg = tuple.getString(0);
		System.out.println(msg+"********baseBasicBolt************");
		if (msg != null) {
			collector.emit(new Values(msg + " msg is processed!" ,"testv"));
		}
	}

}</code></pre><br><br><p><br></p>
            </div>
                </div>