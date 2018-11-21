---
layout:     post
title:      分布式消息中间件（四）——Flume+Kafka+Storm+Redis生态架构实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/Daybreak1209/article/details/51679482				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><strong><span style="font-family:'Comic Sans MS';"><span style="color:rgb(0,0,102);">一、</span><span style="color:rgb(0,0,102);">Kafka项目应用架构分析</span></span></strong></p>
<p><span style="font-family:'Comic Sans MS';">1、Kafka生态架构</span></p>
<p><span style="font-family:'Comic Sans MS';"> <img src="https://img-blog.csdn.net/20160615104824473?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p><span style="font-family:'Comic Sans MS';">     数据收集的速度，跟处理的速度不一定一致，故使用Kafka中间件作为数据收集和数据处理的一个Access入口，接收flume收集的数据，并通过kafkaSpout提交给Storm进行处理。</span></p>
<p><span style="font-family:'Comic Sans MS';">2、kafka 消息生产者</span></p>
<p><span style="font-family:'Comic Sans MS';"><img src="https://img-blog.csdn.net/20160615104859407?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="font-family:'Comic Sans MS';">3、kafka 消息消费者</span></p>
<p><span style="font-family:'Comic Sans MS';"> <img src="https://img-blog.csdn.net/20160615104923486?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p><span style="font-family:'Comic Sans MS';color:rgb(0,0,102);font-weight:bold;">二、Kafka Producer</span></p>
<p><span style="font-family:'Comic Sans MS';"> 1、配置FlumeCluster向Kafka传送数据</span><span style="font-family:'Comic Sans MS';"> </span></p>
<p><span style="font-family:'Comic Sans MS';"></span></p><pre><code class="language-html">#配置数据来源，flume channel通道名
producer.sources.s.type=spooldir   
producer.sources.s.spoolDi=/home/dir/logdfs
producer.sources.s.channels=c

#配置flume sink 类型
producer.sinks.r.type=org.apache.flume.plugins.KafkaSink
producer.sinks.r.metadata.broker.list=dn1:9092,dn2:9092,dn3:9092
producer.sinks.r.costom.topic.name=kafka-ubas
</code></pre>    服务器上一脚本负责不断产生数据，<span style="font-family:'Comic Sans MS';">flume负责接收，</span>通过配置kafka，接收flume指定通道/sink数据；kafkaMonitor管理页面（<a href="http://blog.csdn.net/daybreak1209/article/details/51671837" rel="nofollow">同ActiveMQ管理页面</a>）可看到Producer数据动态增长。
<p><span style="font-family:'Comic Sans MS';"><img src="https://img-blog.csdn.net/20160615112018530?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="font-family:'Comic Sans MS';">2、Kafka客户端消费测试</span></p>
<p><span style="font-family:'Comic Sans MS';"></span></p><pre><code class="language-html">/*
 *  Kafka Consumer测试 
 */
public class TestKafkaConsumer extends Thread {
	private ConsumerConnector consumer;
	private String topic;
	private final int SLEEP = 1000 * 3;

	public static void main(String[] args) {
		TestKafkaConsumer consumer = new TestKafkaConsumer(KafkaParam.TOPIC);  //创建指定好连接信息的consumer实例
		consumer.start();  //执行kafkaConsumer的run方法
	}
	/*
	 * 构造函数，创建Consumer实例
	 * consumerConfig-集群连接配置信息
	 */
	public TestKafkaConsumer(String topic) {
		consumer = Consumer.createJavaConsumerConnector(this.consumerConfig());
		this.topic = topic;
	}

	private ConsumerConfig consumerConfig() {
		Properties props = new Properties();
		props.put("zookeeper.connect", KafkaParam.ZK_HOSTS);//该常量来自于配置信息
		props.put("group.id", KafkaParam.GROUP_ID);
		props.put("zookeeper.session.timeout.ms", "40000");
		props.put("zookeeper.sync.time.ms", "200");
		props.put("auto.commit.interval.ms", "1000");
		return new ConsumerConfig(props);
	}

	/*
	 * 执行接收kafka消息流 
	 */
	@Override
	public void run() {
		Map&lt;String, Integer&gt; topicCountMap = new HashMap&lt;String, Integer&gt;();
		topicCountMap.put(topic, new Integer(1));
		Map&lt;String, List&lt;KafkaStream&lt;byte[], byte[]&gt;&gt;&gt; consumerMap = consumer.createMessageStreams(topicCountMap);//创建数据流Map，用于存放get到的信息
		KafkaStream&lt;byte[], byte[]&gt; stream = consumerMap.get(topic).get(0);
		ConsumerIterator&lt;byte[], byte[]&gt; it = stream.iterator();
		while (it.hasNext()) {
			System.out.println("接收消息-&gt;" + new String(it.next().message()));
			try {
				sleep(SLEEP);
			} catch (Exception ex) {
				ex.printStackTrace();
			}
		}
	}}</code></pre>抽象一个关于kafka连接的配置信息类
<p><span style="font-family:'Comic Sans MS';"></span></p><pre><code class="language-html">public class KafkaConfigure{

	private static String zkHosts = "";

	static {
		String flag = SystemConfig.getProperty("dev.tag"); //加载配置文件 如下
		zkHosts = SystemConfig.getProperty(flag + ".kafka.zk.host");//获取配置中zk连接信息
	}

	public interface KafkaConf {
		public static final String CREATE = "--create";
		public static final String DELETE = "--delete";
		public static final String LIST = "--list";
		public static final String PARTITIONS = "--partitions";
		public static final String REPLICATION = "--replication-factor";
		public static final String TOPIC = "--topic";
		public static final String ZK = "--zookeeper";
	}

	public interface KafkaParam {
		public static final String GROUP_ID = "kafkaUbasGroup";
		public static final String TOPIC = "kafka-ubas"; //同1中consumer.topic.name参数
		public static String ZK_HOSTS = zkHosts;
	}

	public interface StormParam {
		public static final String PV = "pv";
		public static final String APP_ID = "appid";
	}
}</code></pre>system-config.properties配置文件如下
<p><span style="font-family:'Comic Sans MS';"></span></p><pre><code class="language-html"># set hdfs input path   
hdfs.input.path.ubas=/home/hdfs/kafka/in/%s

# set hdfs output path
hdfs.output.path.ubas=/home/hdfs/kafka/out/%s

# set test hdfs host
test.kafka.zk.host=192.168.55.15:2181,192.168.55.17:2181,192.168.55.18:2181

# set pro hdfs host
pro.kafka.zk.host=192.168.10.101:2181,192.168.10.103:2181,192.168.10.104:2181

# set redis host
real-time.redis.host=192.168.55.18,192.168.55.17,192.168.55.15
real-time.redis.port=6379
</code></pre><span style="font-family:'Comic Sans MS';color:rgb(0,0,102);font-weight:bold;">三、Kafka Consumer  </span>
<p><span style="font-family:'Comic Sans MS';">     数据从Kafka consumer 到Storm实时处理</span></p>
<p><span style="font-family:'Comic Sans MS';"><img src="https://img-blog.csdn.net/20160615112648196?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="font-family:'Comic Sans MS';color:#ff0000;">     关于Storm数据处理的几个核心类，Spout、Bolt详见：<a href="http://blog.csdn.net/daybreak1209/article/details/50952730" rel="nofollow">storm集群组件编程模型</a></span></p>
<p><span style="font-family:'Comic Sans MS';color:#ff0000;">1、Spout</span></p>
<p><span style="font-family:'Comic Sans MS';"></span></p><pre><code class="language-html">public class KafkaSpout implements IRichSpout {
	private static final long serialVersionUID = -7107773519958260350L;
	private static final Logger LOGGER = LoggerFactory.getLogger(KafkaSpout.class);

	SpoutOutputCollector collector;
	private ConsumerConnector consumer;
	private String topic;
&lt;span style="white-space:pre"&gt;	&lt;/span&gt;//连接信息
	private static ConsumerConfig createConsumerConfig() {
		Properties props = new Properties();
		props.put("zookeeper.connect", KafkaParam.ZK_HOSTS);
		props.put("group.id", KafkaParam.GROUP_ID);
		props.put("zookeeper.session.timeout.ms", "40000");
		props.put("zookeeper.sync.time.ms", "200");
		props.put("auto.commit.interval.ms", "1000");
		return new ConsumerConfig(props);
	}
&lt;span style="white-space:pre"&gt;	&lt;/span&gt;//构造函数
	public KafkaSpout(String topic) {
		this.topic = topic;
	}

	@SuppressWarnings("rawtypes")
	public void open(Map conf, TopologyContext context, SpoutOutputCollector collector) {
		this.collector = collector;
	}

	public void activate() {
		try {
			this.consumer = Consumer.createJavaConsumerConnector(createConsumerConfig());
			Map&lt;String, Integer&gt; topickMap = new HashMap&lt;String, Integer&gt;();
			topickMap.put(topic, new Integer(1));
			Map&lt;String, List&lt;KafkaStream&lt;byte[], byte[]&gt;&gt;&gt; streamMap = consumer.createMessageStreams(topickMap);
			KafkaStream&lt;byte[], byte[]&gt; stream = streamMap.get(topic).get(0);
			ConsumerIterator&lt;byte[], byte[]&gt; it = stream.iterator();
			while (it.hasNext()) {
				String value = new String(it.next().message());
				LOGGER.info("[ Consumer ] Message is : " + value);
				collector.emit(new Values(value), value);
			}
		} catch (Exception ex) {
			ex.printStackTrace();
			LOGGER.error("Spout has error,msg is " + ex.getMessage());
		}
	}
	public void declareOutputFields(OutputFieldsDeclarer declarer) {
		declarer.declare(new Fields("KafkaSpout"));
	}</code></pre>2、两个bolt，分别用于数据分割处理和计算处理
<p><span style="font-family:'Comic Sans MS';"></span></p><pre><code class="language-html">/**
 * 负责分割数据处理
 */
public class MessageBlots implements IRichBolt {
	private static final long serialVersionUID = -2025360103997307370L;
	OutputCollector collector;

	@SuppressWarnings("rawtypes")
	public void prepare(Map stormConf, TopologyContext context, OutputCollector collector) {
		this.collector = collector;
	}

	public void execute(Tuple input) {
		String[] line = input.getString(0).split(",");
		for (int i = 0; i &lt; line.length; i++) {
			List&lt;Tuple&gt; a = new ArrayList&lt;Tuple&gt;();
			a.add(input);
			switch (i) {
			case 0:
				this.collector.emit(a, new Values(line[i]));
				break;
			case 3:
				this.collector.emit(a, new Values(line[i]));
				break;
			case 4:
				this.collector.emit(a, new Values(line[i]));
				break;
			case 6:
				this.collector.emit(a, new Values(line[i]));
				break;
			default:
				break;
			}
		}
		this.collector.ack(input);
	}

	public void declareOutputFields(OutputFieldsDeclarer declarer) {
		declarer.declare(new Fields("attribute"));
	}
}
/**
 *数据保存处理
 */
public class StatsBlots implements IRichBolt {
	private static final long serialVersionUID = -619395076356762569L;
	private static Logger LOG = LoggerFactory.getLogger(StatsBlots.class);
	OutputCollector collector;
	Map&lt;String, Integer&gt; counter;

	@SuppressWarnings("rawtypes")
	public void prepare(Map stormConf, TopologyContext context, OutputCollector collector) {
		this.collector = collector;
		this.counter = new HashMap&lt;String, Integer&gt;();
	}

	public void execute(Tuple input) {
		String key = input.getString(0);
		// Other KPI
		if (!InetAddressUtils.isIPv4(key) &amp;&amp; !key.contains(StormParam.APP_ID)) {
			Integer integer = this.counter.get(key);
			if (integer != null) {
				integer += 1;
				this.counter.put(key, integer);
			} else {
				this.counter.put(key, 1);
			}
		}

		// PV
		if (InetAddressUtils.isIPv4(key)) {
			Integer pvInt = this.counter.get(StormParam.PV);
			if (pvInt != null) {
				pvInt += 1;
				this.counter.put(StormParam.PV, pvInt);
			} else {
				this.counter.put(StormParam.PV, 1);
			}
			System.out.println(key + ",pv=" + pvInt);
		}

		// AppId
		if (key.contains(StormParam.APP_ID)) {
			Integer appIdInt = this.counter.get(key);
			if (appIdInt != null) {
				appIdInt += 1;
				this.counter.put(key, appIdInt);
			} else {
				this.counter.put(key, 1);
			}
		}

		/*
		 * 数据持久化
		 */
		try {
			Jedis jedis = JedisFactory.getJedisInstance("real-time");
			for (Entry&lt;String, Integer&gt; entry : this.counter.entrySet()) {

				LOG.info("Bolt stats kpi is [" + entry.getKey() + "|" + entry.getValue().toString() + "]");
				// write result to redis
				jedis.set(CalendarUtils.today() + "_" + entry.getKey(), entry.getValue().toString());

				// write result to mysql
				// ...
			}
		} catch (Exception ex) {
			ex.printStackTrace();
			LOG.error("Jedis error, msg is " + ex.getMessage());
		}
		this.collector.ack(input);
	}
}</code></pre>3、提交Storm topology
<p><span style="font-family:'Comic Sans MS';"></span></p><pre><code class="language-html">public class KafkaTopology {
	public static void main(String[] args) {
		TopologyBuilder builder = new TopologyBuilder();
		builder.setSpout("kafkaUbasGroup", new KafkaSpout("kafka-ubas"));
		builder.setBolt("messageBlots", new MessageBlots()).shuffleGrouping("kafkaUbasGroup");
		builder.setBolt("kpiCounter", new StatsBlots(), 2).fieldsGrouping("messageBlots", new Fields("attribute"));
		Config config = new Config();
		// config.setDebug(true);
		// storm.messaging.netty.max_retries
		// storm.messaging.netty.max_wait_ms
		if (args != null &amp;&amp; args.length &gt; 0) {   
			// online commit Topology &lt;span style="font-family: 'Comic Sans MS';"&gt;集群提交&lt;/span&gt;
			config.put(Config.NIMBUS_HOST, args[0]);
			config.setNumWorkers(3);
			try {
				StormSubmitter.submitTopologyWithProgressBar(KafkaTopology.class.getSimpleName(), config,
						builder.createTopology());
			} catch (Exception e) {
				e.printStackTrace();
			}
		} else {
			// Local commit jar 本地提交
			LocalCluster local = new LocalCluster();
			local.submitTopology("stats", config, builder.createTopology());
//			try {
//				Thread.sleep(50);
//			} catch (InterruptedException e) {
//				e.printStackTrace();
//			}
			// local.shutdown();
		}
	}</code></pre><span style="color:rgb(0,0,102);font-family:'Comic Sans MS';"><strong>四、storm到DB的数据持久化 </strong></span><br><img src="https://img-blog.csdn.net/20160615113938806?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br>
     在Storm第二个boult（StatsBoult）<span style="font-family:'Comic Sans MS';">进行数据计算的excute方法中的最后一个try-catch中，将计算结果信息持久化到redis和mysql同步更新。</span><span style="font-family:'Comic Sans MS';">  </span>
            </div>
                </div>