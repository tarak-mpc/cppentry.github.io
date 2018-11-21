---
layout:     post
title:      spark streaming读取kafka示例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/xzj9581/article/details/79223826				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>spark streaming读取kafka示例，其中</p>
Spark Streaming优雅的关闭策略优化部分参考：<br>
http://qindongliang.iteye.com/blog/2404100<br><br>
如何管理Spark Streaming消费Kafka的偏移量部分参考：<br>
http://qindongliang.iteye.com/blog/2401194<br><br>
Spark向kafka中写入数据部分参考：<br><p>http://blog.csdn.net/bsf5521/article/details/76635867?locationNum=9&amp;fps=1</p>
<p><br></p>
<p>object Demo {</p>
  val LOGGER = org.apache.log4j.LogManager.getLogger(Demo.getClass)<br>
  val PROP = conf<br>
  lazy val kafkaProducer = KafkaSink[String, String](KafkaHelper.getProducerConfigs())<br><br>
  def main(args: Array[String]): Unit = {<br>
    //创建StreamingContext<br>
    val ssc = createStreamingContext()<br><br>
    //开始执行<br>
    ssc.start()<br><br>
    //启动接受停止请求的守护进程：http://192.168.1.XX:3443/close/Demo<br>
    daemonHttpServer(3443, ssc) //通过Http方式优雅的关闭策略<br><br>
    //等待任务终止<br>
    ssc.awaitTermination()<br>
  }<br><br>
  /**<br>
   * *<br>
   * 创建StreamingContext<br>
   * @return<br>
   */<br>
  def createStreamingContext(): StreamingContext = {<br>
    conf<br><br>
    val ssc = StreamingUtils.getStreamingContext(Demo.getClass.getSimpleName)<br>
    val spark = SparkSqlUtils.createSpark(this.getClass.getSimpleName());<br>
    var offsetRanges: Array[OffsetRange] = null<br><br>
    // Create direct kafka stream with brokers and topics<br>
    val messages = KafkaUtils.createDirectStream[String, String](<br>
      ssc,<br>
      LocationStrategies.PreferConsistent,<br>
      ConsumerStrategies.Subscribe[String, String](KafkaHelper.getTopic(), KafkaHelper.getConsumerConfigs(), KafkaHelper.readOffset(Demo.getClass.getSimpleName)))<br><br>
    val windowsmessage = messages.transform(rdd =&gt; {<br>
      // 创建一个新的离散流（DStream），取得kafka数据offset，准备手动提交offset<br>
      offsetRanges = rdd.asInstanceOf[HasOffsetRanges].offsetRanges<br>
      rdd<br>
    })<br>
      .map(record =&gt; {<br>
        try {<br>
          // 解析JSON数据<br>
          val value = record.value()<br>
          // record.topic()<br>
          if (value == null) {<br>
            // 无效数据设为空 TODO<br>
            null<br>
          } else {<br>
            value<br>
          }<br>
        } catch {<br>
          case e: Throwable =&gt; {<br>
            LOGGER.error("解析kafka数据错误。" + record, e)<br>
            e.printStackTrace()<br>
            throw e<br>
          }<br>
        }<br>
      })<br>
      // 过滤无效数据<br>
      .filter(r =&gt; {<br>
        // 过滤为空的数据<br>
        if (null == r) {<br>
          false<br>
        } else {<br>
          true<br>
        }<br>
      })<br>
      // 聚合等操作<br>
      //.groupByKey() 根据需要决定是否聚合<br>
      .foreachRDD(rdd =&gt; {<br>
        // RDD转化为dataframe<br>
        // 构造schema<br>
        //        val schema = StructType(structFileds) <br>
        //        val dataframe = sparkSession.createDataFrame(rowRDD, schema)<br>
        //        dataframe.createOrReplaceTempView("tablename");<br>
        // 关联其它表进行计算<br>
        var json = spark.read.json(rdd)<br>
        json.createOrReplaceTempView("json")<br>
        spark.sql("select unix_timestamp() from json")<br>
        val result = spark.sql("select unix_timestamp() a")<br>
        // driver<br>
        result.foreachPartition(itRow=&gt;{<br>
          // worker<br>
          itRow.foreach(f=&gt;{<br>
            // worker-sub<br>
            val fut = kafkaProducer.send("topic", "key", f.getAs[String]("a"))<br>
            fut.get// 同步方式<br>
          })<br>
        })<br>
        try {<br>
          // 提交kafka offset<br>
          KafkaHelper.saveOffset(Demo.getClass.getSimpleName, offsetRanges);<br>
        } catch {<br>
          case e: Throwable =&gt; {<br>
            LOGGER.error("commitAsync错误。" + offsetRanges, e)<br>
            offsetRanges.foreach { x =&gt; LOGGER.error("offsetRanges:" + x.toString()) }<br>
            throw e<br>
          }<br>
        }<br>
      })<br>
    ssc<br>
  }<br><br>
  /**<br>
   *<br>
   * 负责启动守护的jetty服务<br>
   * @param port 对外暴露的端口号<br>
   * @param ssc Stream上下文<br>
   */<br>
  def daemonHttpServer(port: Int, ssc: StreamingContext) = {<br>
    val server = new Server(port)<br>
    val context = new ContextHandler();<br>
    context.setContextPath("/close/" + Demo.getClass.getSimpleName.replace("$", ""));<br>
    context.setHandler(new CloseStreamHandler(ssc))<br>
    server.setHandler(context)<br>
    server.start()<br>
  }<br><br>
  /**<br>
   * 负责接受http请求来优雅的关闭流<br>
   * @param ssc  Stream上下文<br>
   */<br>
  class CloseStreamHandler(ssc: StreamingContext) extends AbstractHandler {<br>
    override def handle(s: String, baseRequest: Request, req: HttpServletRequest, response: HttpServletResponse): Unit = {<br>
      LOGGER.warn("开始关闭......")<br>
      ssc.stop(true, true) //优雅的关闭<br>
      response.setContentType("text/html; charset=utf-8");<br>
      response.setStatus(HttpServletResponse.SC_OK);<br>
      val out = response.getWriter();<br>
      out.println("close success");<br>
      baseRequest.setHandled(true);<br>
      LOGGER.warn("关闭成功.....")<br>
    }<br>
  }<br><br>
  def conf() = {<br>
    val props = new Properties()<br>
    //文件要放到resource文件夹下<br>
    val is = getClass.getResourceAsStream("/config.properties")<br>
    props.load(new BufferedInputStream(is))<br>
    props.putAll(System.getProperties);<br>
    System.setProperties(props)<br>
    props<br>
  }<br><p>}</p>
<p>object StreamingUtils {<br>
  def getStreamingContext(name: String): StreamingContext = {<br>
    val sparkConf = new SparkConf() //创建SparkConf对象<br>
    val osName = System.getProperty("os.name");<br>
    if (osName != null &amp;&amp; osName.startsWith("Windows")) {<br>
      System.setProperty("user.name", "root");<br>
      sparkConf.set("spark.executor.memory", "512M")<br>
      sparkConf.set("spark.executor.cores", "1")<br>
      sparkConf.setMaster("local[2]")<br>
    }<br>
    sparkConf.setAppName(this.getClass.getSimpleName)<br>
    sparkConf.set("spark.serializer", "org.apache.spark.serializer.KryoSerializer")<br>
    //sparkConf.set("spark.sql.shuffle.partitions", "6")<br>
    sparkConf.set("spark.streaming.stopGracefullyOnShutdown","true")//优雅的关闭<br>
    sparkConf.set("spark.streaming.backpressure.enabled","true")//激活削峰功能<br>
    sparkConf.set("spark.streaming.backpressure.initialRate","5000")//第一次读取的最大数据值<br>
    sparkConf.set("spark.streaming.kafka.maxRatePerPartition","2000")//每个进程每秒最多从kafka读取的数据条数<br>
    val ssc = new StreamingContext(sparkConf, Seconds(Integer.parseInt(System.getProperty("streaming.batchduration"))))<br>
    ssc<br>
  }<br>
}</p>
<p><br></p>
<p>object SerialiableUtils {<br>
  //对象序列化为字符串<br>
  def objectSerialiable(obj: Object): String = {<br>
    val byteArrayOutputStream = new ByteArrayOutputStream()<br>
    val objectOutputStream = new ObjectOutputStream(byteArrayOutputStream)<br>
    objectOutputStream.writeObject(obj)<br>
    var serISO = byteArrayOutputStream.toString("ISO-8859-1")<br>
    var serUTF8 = java.net.URLEncoder.encode(serISO, "UTF-8")<br>
    objectOutputStream.close()<br>
    byteArrayOutputStream.close()<br>
    serUTF8;<br>
  }<br><br>
  //字符串反序列化为对象<br>
  def objectDeserialization[T](serStr: String): T = {<br>
    val redStr = java.net.URLDecoder.decode(serStr, "UTF-8")<br>
    val byteArrayInputStream = new ByteArrayInputStream(redStr.getBytes("ISO-8859-1"))<br>
    val objectInputStream = new ObjectInputStream(byteArrayInputStream)<br>
    val newObj = objectInputStream.readObject()<br>
    objectInputStream.close()<br>
    byteArrayInputStream.close()<br>
    newObj.asInstanceOf[T]<br>
  }<br>
}</p>
<p>@SerialVersionUID(1L)<br>
class KafkaSink[K, V](createProducer: () =&gt; KafkaProducer[K, V]) extends Serializable {<br>
  /* This is the key idea that allows us to work around running into<br>
     NotSerializableExceptions. */<br>
  lazy val producer = createProducer()<br>
  val callback = new Callback() {<br>
    def onCompletion(metadata: RecordMetadata, e: Exception) {<br>
      if (e != null) {<br>
        println("kafka send Callback:" + metadata.toString())<br>
        e.printStackTrace()<br>
      }<br>
    }<br>
  }<br><br>
  def send(topic: String, key: K, value: V): Future[RecordMetadata] =<br>
    //producer.send(new ProducerRecord[K, V](topic, key, value))<br>
    producer.send(new ProducerRecord[K, V](topic, key, value), callback)<br><br>
  def send(topic: String, value: V): Future[RecordMetadata] =<br>
    producer.send(new ProducerRecord[K, V](topic, value))<br>
}<br><br>
object KafkaSink {<br>
  import scala.collection.JavaConversions._<br><br>
  def apply[K, V](config: Map[String, String]): KafkaSink[K, V] = {<br>
    val createProducerFunc = () =&gt; {<br>
      val producer = new KafkaProducer[K, V](config)<br>
      sys.addShutdownHook {<br>
        // Ensure that, on executor JVM shutdown, the Kafka producer sends<br>
        // any buffered messages to Kafka before shutting down.<br>
        producer.close()<br>
      }<br>
      producer<br>
    }<br>
    new KafkaSink(createProducerFunc)<br>
  }<br><br>
  def apply[K, V](config: java.util.Properties): KafkaSink[K, V] = apply(config.toMap)<br>
}<br></p>
<p>object KafkaHelper {<br>
  lazy val log = org.apache.log4j.LogManager.getLogger(KafkaHelper.getClass)<br>
  lazy val zkClient = ZkUtils.createZkClient(System.getProperty("zookeeper.url"), Integer.MAX_VALUE, 10000)<br>
  lazy val zkUtils = ZkUtils.apply(zkClient, true)<br><br>
  val OFFSET_ROOT = "/spark/streaming/offset/"<br><br>
  /**<br>
   * *读取zk里面的偏移量，如果有就返回对应的分区和偏移量<br>
   * 如果没有就返回None<br>
   * @param zkClient  zk连接的client<br>
   * @param zkOffsetPath   偏移量路径<br>
   * @param topic    topic名字<br>
   * @return  偏移量Map or None<br>
   */<br>
  private def readOffsets(zkOffsetPath: String, topic: String): Option[Map[TopicPartition, Long]] = {<br>
    //（偏移量字符串,zk元数据)<br>
    val (offsetsRangesStrOpt, _) = zkUtils.readDataMaybeNull(zkOffsetPath) //从zk上读取偏移量<br>
    offsetsRangesStrOpt match {<br>
      case Some(null) =&gt;<br>
        None //如果是null，就返回None<br>
      case Some(offsetsRangesStr) =&gt;<br>
        //这个topic在zk里面最新的分区数量<br>
        val lastest_partitions = zkUtils.getPartitionsForTopics(Seq(topic)).get(topic).get<br>
        var offsets = offsetsRangesStr.split(",") //按逗号split成数组<br>
          .map(s =&gt; s.split(":")) //按冒号拆分每个分区和偏移量<br>
          .map { case Array(partitionStr, offsetStr) =&gt; (new TopicPartition(topic, partitionStr.toInt) -&gt; offsetStr.toLong) } //加工成最终的格式<br>
          .toMap //返回一个Map<br>
        //说明有分区扩展了<br>
        if (offsets.size &lt; lastest_partitions.size) {<br>
          //得到旧的所有分区序号<br>
          val old_partitions = offsets.keys.map(p =&gt; p.partition).toArray<br>
          //通过做差集得出来多的分区数量数组<br>
          val add_partitions = lastest_partitions.diff(old_partitions)<br>
          if (add_partitions.size &gt; 0) {<br>
            log.warn("发现kafka新增分区：" + add_partitions.mkString(","))<br>
            add_partitions.foreach(partitionId =&gt; {<br>
              offsets += (new TopicPartition(topic, partitionId) -&gt; 0)<br>
              log.warn("新增分区id：" + partitionId + "添加完毕....")<br>
            })<br>
          }<br>
        } else {<br>
          log.info("没有发现新增的kafka分区：" + lastest_partitions.mkString(","))<br>
        }<br>
        Some(offsets) //将Map返回<br>
      case None =&gt;<br>
        None //如果是null，就返回None<br>
    }<br>
  }<br><br>
  /**<br>
   * **<br>
   * 保存每个批次的rdd的offset到zk中<br>
   * @param zkClient zk连接的client<br>
   * @param zkOffsetPath   偏移量路径<br>
   * @param rdd     每个批次的rdd<br>
   */<br>
  private def saveOffsets(zkOffsetPath: String, offsetRanges: Array[OffsetRange]): Unit = {<br>
    //转换rdd为Array[OffsetRange]<br>
    //val offsetsRanges = rdd.asInstanceOf[HasOffsetRanges].offsetRanges<br>
    //转换每个OffsetRange为存储到zk时的字符串格式 :  分区序号1:偏移量1,分区序号2:偏移量2,......<br>
    val offsetsRangesStr = offsetRanges.map(offsetRange =&gt; s"${offsetRange.partition}:${offsetRange.untilOffset}").mkString(",")<br>
    log.debug(" 保存的偏移量：  " + offsetsRangesStr)<br>
    //将最终的字符串结果保存到zk里面<br>
    zkUtils.updatePersistentPath(zkOffsetPath, offsetsRangesStr)<br>
  }<br><br>
  private def getZKPath(name: String): String = {<br>
    OFFSET_ROOT + name.replace("$", "")<br>
  }<br><br>
  def readOffset(name: String) = {<br>
    val path = getZKPath(name)<br>
    val e = zkClient.exists(path);<br>
    if (!e) {<br>
      zkClient.createPersistent(path, true);<br>
    }<br>
    val offset = readOffsets(path, System.getProperty("kafka.source.topics"))<br><br>
    if (offset.isEmpty) {<br>
      ju.Collections.emptyMap[TopicPartition, jl.Long]()<br>
    } else {<br>
      val offsetJavaMap = new ju.HashMap[TopicPartition, java.lang.Long]()<br>
      offset.get.foreach(f =&gt; {<br>
        offsetJavaMap.put(f._1, f._2)<br>
      })<br>
      offsetJavaMap<br>
    }<br>
  }<br><br>
  def saveOffset(name: String, offsetRanges: Array[OffsetRange]): Unit = {<br>
    saveOffsets(getZKPath(name), offsetRanges)<br>
  }<br><br>
  def getConsumerConfigs(): ju.HashMap[String, Object] = {<br>
    val kafkaParams = new ju.HashMap[String, Object]<br>
    kafkaParams.put("bootstrap.servers", System.getProperty("kafka.brokers"))<br>
    kafkaParams.put("group.id", System.getProperty("kafka.source.group.id"))<br>
    kafkaParams.put("key.deserializer", "org.apache.kafka.common.serialization.StringDeserializer")<br>
    kafkaParams.put("value.deserializer", "org.apache.kafka.common.serialization.StringDeserializer")<br>
    kafkaParams.putAll(strToJMap(System.getProperty("kafka.consumer.configs", ""), ",", "="))<br>
    kafkaParams<br>
  }<br><br>
  def getProducerConfigs(): Properties = {<br>
    val kafkaProps = new Properties()<br>
    kafkaProps.put("bootstrap.servers", System.getProperty("kafka.brokers"))<br>
    kafkaProps.put("group.id", System.getProperty("kafka.source.group.id"))<br>
    kafkaProps.put("key.serializer", "org.apache.kafka.common.serialization.StringSerializer");<br>
    kafkaProps.put("value.serializer", "org.apache.kafka.common.serialization.StringSerializer");<br>
    kafkaProps.put("retries", "1000"); // 请求失败重试的次数<br>
    // 默认发送不进行压缩，推荐配置一种适合的压缩算法，可以大幅度的减缓网络压力和Broker的存储压力。正确的选项值是none、gzip、snappy.压缩最好用于批量处理，批量处理消息越多，压缩性能越好<br>
    kafkaProps.put("compression.type", "snappy");<br>
    // 默认情况即使缓冲区有剩余的空间，也会立即发送请求，<br>
    // 设置一段时间用来等待从而将缓冲区填的更多，单位为毫秒，<br>
    // producer发送数据会延迟1ms，可以减少发送到kafka服务器的请求数据<br>
    kafkaProps.put("linger.ms", "1");<br>
    //这里是只要求leader响应就OK，更高的要求则应该设置成"all<br>
    kafkaProps.put("acks", "1");<br>
    // kafka可以在一个connection中发送多个请求，叫作一个flight,这样可以减少开销，但是如果产生错误，可能会造成数据的发送顺序改变,默认是5<br>
    kafkaProps.put("max.in.flight.requests.per.connection", "5")<br>
    kafkaProps.putAll(strToJMap(System.getProperty("kafka.producer.configs", ""), ",", "="))<br>
    kafkaProps<br>
  }<br><br>
  def getTopic(): ArrayList[String] = {<br>
    val topics = System.getProperty("kafka.source.topics")<br>
    var topicsList = new ArrayList[String]()<br>
    topics.split(",").foreach { t =&gt;<br>
      topicsList.add(t.trim)<br>
    }<br>
    topicsList<br>
  }<br><br>
  /**<br>
   * Configeration string convert to java.util.Map.&lt;br&gt;<br>
   * if in is empty, then retrun null<br>
   * @parma: in: "a=1,b=2,c=3";<br>
   * @parma: spliterPairs: ","<br>
   * @parma: spliterKV: "="<br>
   * @return: java.util.Map("a"-&gt;"1","b"-&gt;"2","c"-&gt;"3")<br>
   */<br>
  def strToJMap(in: String, spliterPairs: String, spliterKV: String): java.util.Map[String, String] = {<br>
    if (in == null) {<br>
      return null<br>
    }<br>
    var map = new java.util.HashMap[String, String]()<br>
    in.split(spliterPairs).foreach { pairs =&gt;<br>
      val arr = pairs.split(spliterKV)<br>
      if (arr.length == 2) {<br>
        map.put(arr(0).trim(), arr(1).trim())<br>
      }<br>
    }<br>
    map<br>
  }<br>
}<br></p>
<p><br></p>
            </div>
                </div>