---
layout:     post
title:      structured  -streaming -kafka源码分析
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u011707542/article/details/82316308				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>structured streaming支持四种input sources分别为File source 、Kafka source 、Socket source (for testing)和Rate source (for testing) 。</p>

<p>本文主要介绍Kafka source，从kafka读取数据，兼容 kafka broker版本0.10+。</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark <span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>  spark-sql-kafka-0-10_2.11<span class="hljs-tag">&lt;/<span class="hljs-title">.artifactId</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span> 2.3.1<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span></code></pre>



<h2 id="使用代码如下">使用代码如下</h2>



<pre class="prettyprint"><code class=" hljs avrasm">val df = spark
  <span class="hljs-preprocessor">.readStream</span>
  <span class="hljs-preprocessor">.format</span>(<span class="hljs-string">"kafka"</span>)
  <span class="hljs-preprocessor">.option</span>(<span class="hljs-string">"kafka.bootstrap.servers"</span>, <span class="hljs-string">"host1:port1,host2:port2"</span>)
  <span class="hljs-preprocessor">.option</span>(<span class="hljs-string">"subscribe"</span>, <span class="hljs-string">"topic1"</span>)
  <span class="hljs-preprocessor">.load</span>()</code></pre>



<h2 id="源码解析">源码解析</h2>

<p>spark:SparkSession  调用了readStream函数，该函数返回对象是DataStreamReader。DataStreamReader的format方法是输入的数据源名字，名字的定义是在实现DataSourceRegister接口然后重写其方法shortName中定义的。</p>



<pre class="prettyprint"><code class=" hljs scala"><span class="hljs-keyword">private</span>[kafka010] <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">KafkaSourceProvider</span> <span class="hljs-keyword">extends</span> <span class="hljs-title">DataSourceRegister</span></span>
<span class="hljs-keyword">override</span> <span class="hljs-keyword">def</span> shortName(): String = <span class="hljs-string">"kafka"</span></code></pre>

<p>然后继续调用DataStreamReader的option方法，根据format的数据源不同设置该数据源相应的参数，kafka consumer的相关参数<a href="http://kafka.apache.org/documentation.html#newconsumerconfigs" rel="nofollow">点击此处</a>，当然不仅仅是kafka consumer的参数，还有spark自己对于kafka的参数如下(kafka开头的是kafka的参数)：</p>

<table>
<thead>
<tr>
  <th>option</th>
  <th>value</th>
  <th>default</th>
  <th>query type</th>
  <th>meaning</th>
</tr>
</thead>
<tbody><tr>
  <td>startingOffsets</td>
  <td>“earliest”, “latest” (streaming only), or json string “”” {“topicA”:{“0”:23,”1”:-1},”topicB”:{“0”:-2}} “”“</td>
  <td>“latest” for streaming, “earliest” for batch</td>
  <td>streaming and batch</td>
  <td>The start point when a query is started, either “earliest” which is from the earliest offsets, “latest” which is just from the latest offsets, or a json string specifying a starting offset for each TopicPartition. In the json, -2 as an offset can be used to refer to earliest, -1 to latest. Note: For batch queries, latest (either implicitly or by using -1 in json) is not allowed. For streaming queries, this only applies when a new query is started, and that resuming will always pick up from where the query left off. Newly discovered partitions during a query will start at earliest.</td>
</tr>
<tr>
  <td>endingOffsets</td>
  <td>latest or json string {“topicA”:{“0”:23,”1”:-1},”topicB”:{“0”:-1}}</td>
  <td>latest</td>
  <td>batch query</td>
  <td>The end point when a batch query is ended, either “latest” which is just referred to the latest, or a json string specifying an ending offset for each TopicPartition. In the json, -1 as an offset can be used to refer to latest, and -2 (earliest) as an offset is not allowed.</td>
</tr>
<tr>
  <td>failOnDataLoss</td>
  <td>true or false</td>
  <td>true</td>
  <td>streaming query</td>
  <td>Whether to fail the query when it’s possible that data is lost (e.g., topics are deleted, or offsets are out of range). This may be a false alarm. You can disable it when it doesn’t work as you expected. Batch queries will always fail if it fails to read any data from the provided offsets due to lost data.</td>
</tr>
<tr>
  <td>kafkaConsumer.pollTimeoutMs</td>
  <td>long</td>
  <td>512</td>
  <td>streaming and batch</td>
  <td>The timeout in milliseconds to poll data from Kafka in executors.</td>
</tr>
<tr>
  <td>fetchOffset.numRetries</td>
  <td>int</td>
  <td>3</td>
  <td>streaming and batch</td>
  <td>Number of times to retry before giving up fetching Kafka offsets.</td>
</tr>
<tr>
  <td>fetchOffset.retryIntervalMs</td>
  <td>long</td>
  <td>10</td>
  <td>streaming and batch</td>
  <td>milliseconds to wait before retrying to fetch Kafka offsets</td>
</tr>
<tr>
  <td>maxOffsetsPerTrigger</td>
  <td>long</td>
  <td>none</td>
  <td>streaming and batch</td>
  <td>Rate limit on maximum number of offsets processed per trigger interval. The specified total number of offsets will be proportionally split across topicPartitions of different volume.</td>
</tr>
</tbody></table>


<p>然后继续调用load方法  ，load方法是核心实现。load返回的datafram中ConsumerRecord对象字段如下：</p>

<table>
<thead>
<tr>
  <th>Column</th>
  <th>Type</th>
</tr>
</thead>
<tbody><tr>
  <td>key</td>
  <td>binary</td>
</tr>
<tr>
  <td>value</td>
  <td>binary</td>
</tr>
<tr>
  <td>topic</td>
  <td>string</td>
</tr>
<tr>
  <td>partition</td>
  <td>int</td>
</tr>
<tr>
  <td>offset</td>
  <td>long</td>
</tr>
<tr>
  <td>timestamp</td>
  <td>long</td>
</tr>
<tr>
  <td>timestampType</td>
  <td>int</td>
</tr>
</tbody></table>


<p>我们可以看到key  value类型是binary  ，所以通常我们代码会增加如下：</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor">.load</span>()
<span class="hljs-preprocessor">.selectExpr</span>(<span class="hljs-string">"CAST(value AS STRING)"</span>)
      <span class="hljs-preprocessor">.as</span>[String]</code></pre>

<p>load代码如下：</p>



<pre class="prettyprint"><code class=" hljs avrasm">def load(): DataFrame = {
    if (source<span class="hljs-preprocessor">.toLowerCase</span>(Locale<span class="hljs-preprocessor">.ROOT</span>) == DDLUtils<span class="hljs-preprocessor">.HIVE</span>_PROVIDER) {
      throw new AnalysisException(<span class="hljs-string">"Hive data source can only be used with tables, you can not "</span> +
        <span class="hljs-string">"read files of Hive data source directly."</span>)
    }

    val ds = DataSource<span class="hljs-preprocessor">.lookupDataSource</span>(source, sparkSession<span class="hljs-preprocessor">.sqlContext</span><span class="hljs-preprocessor">.conf</span>)<span class="hljs-preprocessor">.newInstance</span>()
    val options = new DataSourceOptions(extraOptions<span class="hljs-preprocessor">.asJava</span>)
     <span class="hljs-comment">/*
    * 生成V1数据源 因为在这不知道是是否查询是持续性的，所以没有使用V2
    * */</span>
    val v1DataSource = DataSource(
      sparkSession,
      userSpecifiedSchema = userSpecifiedSchema,
      className = source,
      options = extraOptions<span class="hljs-preprocessor">.toMap</span>)
    val v1Relation = ds match {
      case _: StreamSourceProvider =&gt; Some(StreamingRelation(v1DataSource))
      case _ =&gt; None
    }
    ds match {
      case s: MicroBatchReadSupport =&gt;
        val tempReader = s<span class="hljs-preprocessor">.createMicroBatchReader</span>(
          Optional<span class="hljs-preprocessor">.ofNullable</span>(userSpecifiedSchema<span class="hljs-preprocessor">.orNull</span>),
          Utils<span class="hljs-preprocessor">.createTempDir</span>(namePrefix = s<span class="hljs-string">"temporaryReader"</span>)<span class="hljs-preprocessor">.getCanonicalPath</span>,
          options)
        Dataset<span class="hljs-preprocessor">.ofRows</span>(
          sparkSession,
          StreamingRelationV2(
            s, source, extraOptions<span class="hljs-preprocessor">.toMap</span>,
            tempReader<span class="hljs-preprocessor">.readSchema</span>()<span class="hljs-preprocessor">.toAttributes</span>, v1Relation)(sparkSession))
      case s: ContinuousReadSupport =&gt;
       <span class="hljs-comment">/* KafkaContinuousReader实现了ContinuousReader*/</span>
        val tempReader = s<span class="hljs-preprocessor">.createContinuousReader</span>(
          Optional<span class="hljs-preprocessor">.ofNullable</span>(userSpecifiedSchema<span class="hljs-preprocessor">.orNull</span>),
          Utils<span class="hljs-preprocessor">.createTempDir</span>(namePrefix = s<span class="hljs-string">"temporaryReader"</span>)<span class="hljs-preprocessor">.getCanonicalPath</span>,
          options)
        Dataset<span class="hljs-preprocessor">.ofRows</span>(
          sparkSession,
          StreamingRelationV2(
            s, source, extraOptions<span class="hljs-preprocessor">.toMap</span>,
            tempReader<span class="hljs-preprocessor">.readSchema</span>()<span class="hljs-preprocessor">.toAttributes</span>, v1Relation)(sparkSession))
      case _ =&gt;
        // Code path for data source v1.
        Dataset<span class="hljs-preprocessor">.ofRows</span>(sparkSession, StreamingRelation(v1DataSource))
    }
  }</code></pre>

<p>load函数中核心代码：</p>



<pre class="prettyprint"><code class=" hljs avrasm">val ds = DataSource<span class="hljs-preprocessor">.lookupDataSource</span>(source,   sparkSession<span class="hljs-preprocessor">.sqlContext</span><span class="hljs-preprocessor">.conf</span>)<span class="hljs-preprocessor">.newInstance</span>()
</code></pre>

<p>首先调用lookupDataSource函数，返回相应类的getClass返回的对象，此处的source是”kafka”</p>



<pre class="prettyprint"><code class=" hljs php">  def lookupDataSource(provider: String, conf: SQLConf): <span class="hljs-class"><span class="hljs-keyword">Class</span>[<span class="hljs-title">_</span>] = {</span>
    <span class="hljs-comment">/*
    * File Sourcec:从给定的目录读取数据，目前支持的格式有text,csv,json,parquet.容错。
    * Kafka Source:从kafka拉取数据。仅兼容kafka 0.10.0或者更高版本。容错。
    * Socket Source(for testing):从一个连接中读取UTF8编码的文本数据。不容错。
    *
    * backwardCompatibilityMap是jdbc csv json等之类的映射存储不包含外部数据源，因此如果是kafka那么返回provider1=kafka
    * */</span>
    val provider1 = backwardCompatibilityMap.getOrElse(provider, provider) match {
      <span class="hljs-keyword">case</span> name <span class="hljs-keyword">if</span> name.equalsIgnoreCase(<span class="hljs-string">"orc"</span>) &amp;&amp;
          conf.getConf(SQLConf.ORC_IMPLEMENTATION) == <span class="hljs-string">"native"</span> =&gt;
        classOf[OrcFileFormat].getCanonicalName
      <span class="hljs-keyword">case</span> name <span class="hljs-keyword">if</span> name.equalsIgnoreCase(<span class="hljs-string">"orc"</span>) &amp;&amp;
          conf.getConf(SQLConf.ORC_IMPLEMENTATION) == <span class="hljs-string">"hive"</span> =&gt;
        <span class="hljs-string">"org.apache.spark.sql.hive.orc.OrcFileFormat"</span>
      <span class="hljs-keyword">case</span> name =&gt; name
    }
    <span class="hljs-comment">// provider2=kafka.DefaultSource   provider1=kafka</span>
    val provider2 = s<span class="hljs-string">"$provider1.DefaultSource"</span>
    val loader = Utils.getContextOrSparkClassLoader
    <span class="hljs-comment">/*
    * KafkaSourceProvider继承了DataSourceRegister，重写了shortName="kafka"
    * serviceLoader：ServiceLoader[DataSourceRegister]
    * */</span>
    val serviceLoader = ServiceLoader.load(classOf[DataSourceRegister], loader)

    <span class="hljs-keyword">try</span> {
      <span class="hljs-comment">/* 在DataSourceRegister的实现类中匹配重写shorname="kafka"的 ,进步一得到了KafkaSourceProvider */</span>
      serviceLoader.asScala.filter(_.shortName().equalsIgnoreCase(provider1)).toList match {

          <span class="hljs-comment">/*  没有匹配到相应的实现类 */</span>
        <span class="hljs-keyword">case</span> Nil =&gt;
          <span class="hljs-keyword">try</span> {
            <span class="hljs-keyword">Try</span>(loader.loadClass(provider1)).orElse(<span class="hljs-keyword">Try</span>(loader.loadClass(provider2))) match {
              <span class="hljs-keyword">case</span> Success(dataSource) =&gt;
                <span class="hljs-comment">//此处返回的是完全限定路径</span>
                dataSource
              <span class="hljs-keyword">case</span> Failure(error) =&gt;
                <span class="hljs-keyword">if</span> (provider1.startsWith(<span class="hljs-string">"org.apache.spark.sql.hive.orc"</span>)) {
                  <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> AnalysisException(
                    <span class="hljs-string">"Hive built-in ORC data source must be used with Hive support enabled. "</span> +
                    <span class="hljs-string">"Please use the native ORC data source by setting 'spark.sql.orc.impl' to "</span> +
                    <span class="hljs-string">"'native'"</span>)
                } <span class="hljs-keyword">else</span> <span class="hljs-keyword">if</span> (provider1.toLowerCase(Locale.ROOT) == <span class="hljs-string">"avro"</span> ||
                  provider1 == <span class="hljs-string">"com.databricks.spark.avro"</span>) {
                  <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> AnalysisException(
                    s<span class="hljs-string">"Failed to find data source: ${provider1.toLowerCase(Locale.ROOT)}. "</span> +
                    <span class="hljs-string">"Please find an Avro package at "</span> +
                    <span class="hljs-string">"http://spark.apache.org/third-party-projects.html"</span>)
                } <span class="hljs-keyword">else</span> {
                  <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> ClassNotFoundException(
                    s<span class="hljs-string">"Failed to find data source: $provider1. Please find packages at "</span> +
                      <span class="hljs-string">"http://spark.apache.org/third-party-projects.html"</span>,
                    error)
                }
            }
          } <span class="hljs-keyword">catch</span> {
            <span class="hljs-keyword">case</span> e: NoClassDefFoundError =&gt; <span class="hljs-comment">// This one won't be caught by Scala NonFatal</span>
              <span class="hljs-comment">// NoClassDefFoundError's class name uses "/" rather than "." for packages</span>
              val className = e.getMessage.replaceAll(<span class="hljs-string">"/"</span>, <span class="hljs-string">"."</span>)
              <span class="hljs-keyword">if</span> (spark2RemovedClasses.contains(className)) {
                <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> ClassNotFoundException(s<span class="hljs-string">"$className was removed in Spark 2.0. "</span> +
                  <span class="hljs-string">"Please check if your library is compatible with Spark 2.0"</span>, e)
              } <span class="hljs-keyword">else</span> {
                <span class="hljs-keyword">throw</span> e
              }
          }
        <span class="hljs-comment">/*  匹配到了一个实现类 */</span>
        <span class="hljs-keyword">case</span> head :: Nil =&gt;
          <span class="hljs-comment">//   这里面匹配到了KafkaSourceProvider，返回其getClass</span>
          head.getClass
          <span class="hljs-comment">/* 如果有多个实现类情况，优先使用内部提供的，否则扔出异常 */</span>
        <span class="hljs-keyword">case</span> sources =&gt;
          val sourceNames = sources.map(_.getClass.getName)
          val internalSources = sources.filter(_.getClass.getName.startsWith(<span class="hljs-string">"org.apache.spark"</span>))
          <span class="hljs-keyword">if</span> (internalSources.size == <span class="hljs-number">1</span>) {
            logWarning(s<span class="hljs-string">"Multiple sources found for $provider1 (${sourceNames.mkString("</span>, <span class="hljs-string">")}), "</span> +
              s<span class="hljs-string">"defaulting to the internal datasource (${internalSources.head.getClass.getName})."</span>)
            internalSources.head.getClass
          } <span class="hljs-keyword">else</span> {
            <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> AnalysisException(s<span class="hljs-string">"Multiple sources found for $provider1 "</span> +
              s<span class="hljs-string">"(${sourceNames.mkString("</span>, <span class="hljs-string">")}), please specify the fully qualified class name."</span>)
          }
      }
    } <span class="hljs-keyword">catch</span> {
      <span class="hljs-keyword">case</span> e: ServiceConfigurationError <span class="hljs-keyword">if</span> e.getCause.isInstanceOf[NoClassDefFoundError] =&gt;
        <span class="hljs-comment">// NoClassDefFoundError's class name uses "/" rather than "." for packages</span>
        val className = e.getCause.getMessage.replaceAll(<span class="hljs-string">"/"</span>, <span class="hljs-string">"."</span>)
        <span class="hljs-keyword">if</span> (spark2RemovedClasses.contains(className)) {
          <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> ClassNotFoundException(s<span class="hljs-string">"Detected an incompatible DataSourceRegister. "</span> +
            <span class="hljs-string">"Please remove the incompatible library from classpath or upgrade it. "</span> +
            s<span class="hljs-string">"Error: ${e.getMessage}"</span>, e)
        } <span class="hljs-keyword">else</span> {
          <span class="hljs-keyword">throw</span> e
        }
    }
  }</code></pre>

<p>然后继续回到load函数来，对刚刚lookupDataSource函数返回的对象ds进行匹配：</p>



<pre class="prettyprint"><code class=" hljs php">  val v1Relation = ds match {
      <span class="hljs-keyword">case</span> _: StreamSourceProvider =&gt; Some(StreamingRelation(v1DataSource))
      <span class="hljs-keyword">case</span> _ =&gt; None
    }</code></pre>

<p>class KafkaSourceProvider extends DataSourceRegister，因此会返回 Some(StreamingRelation(v1DataSource))，具体如下：</p>



<pre class="prettyprint"><code class=" hljs scala"><span class="hljs-comment">/* 
*链接一个streaming DataSource 到逻辑执行计划LogicalPlan
*/</span>
<span class="hljs-class"><span class="hljs-keyword">case</span> <span class="hljs-keyword">class</span> <span class="hljs-title">StreamingRelation</span><span class="hljs-params">(dataSource: DataSource, sourceName: String, output: Seq[Attribute])</span></span>
  <span class="hljs-keyword">extends</span> LeafNode <span class="hljs-keyword">with</span> MultiInstanceRelation{

  <span class="hljs-keyword">override</span> <span class="hljs-keyword">def</span> isStreaming: Boolean = <span class="hljs-keyword">true</span>
  <span class="hljs-keyword">override</span> <span class="hljs-keyword">def</span> toString: String = sourceName
  .
  ...  
  }</code></pre>

<p>其中类LeafNode又继承了LogicalPlan ，StreamingRelation重写了其方法isStreaming。</p>

<p>之后ds继续进行匹配，匹配ds是MicroBatchReader还是ContinuousReader类型的。kafka的会匹配这个选项：</p>



<pre class="prettyprint"><code class=" hljs avrasm">    case s: ContinuousReadSupport =&gt;
        <span class="hljs-comment">/* 
        * KafkaSourceProvider继承了ContinuousReadSupport
        * KafkaContinuousReader实现了ContinuousReader, tempRead就是其对象
        */</span>
        val tempReader = s<span class="hljs-preprocessor">.createContinuousReader</span>(
          Optional<span class="hljs-preprocessor">.ofNullable</span>(userSpecifiedSchema<span class="hljs-preprocessor">.orNull</span>),
          Utils<span class="hljs-preprocessor">.createTempDir</span>(namePrefix = s<span class="hljs-string">"temporaryReader"</span>)<span class="hljs-preprocessor">.getCanonicalPath</span>,
          options)
        Dataset<span class="hljs-preprocessor">.ofRows</span>(
          sparkSession,
          StreamingRelationV2(
            s, source, extraOptions<span class="hljs-preprocessor">.toMap</span>,
            tempReader<span class="hljs-preprocessor">.readSchema</span>()<span class="hljs-preprocessor">.toAttributes</span>, v1Relation)(sparkSession))</code></pre>

<p>public interface ContinuousReadSupport extends DataSourceV2  调用其子类KafkaSourceProvider的createContinuousReader函数，然后返回KafkaContinuousReader对象。</p>



<pre class="prettyprint"><code class=" hljs avrasm">
  override def createContinuousReader(

      schema: Optional[StructType],

      metadataPath: String,

      options: DataSourceOptions): KafkaContinuousReader = {

    val parameters = options<span class="hljs-preprocessor">.asMap</span>()<span class="hljs-preprocessor">.asScala</span><span class="hljs-preprocessor">.toMap</span>
    validateStreamOptions(parameters)
    // id. Hence, we should generate a unique id for each query.
    <span class="hljs-comment">/* 对于每一个查询我们需要创建不同的groupid */</span>
    val uniqueGroupId = s<span class="hljs-string">"spark-kafka-source-${UUID.randomUUID}-${metadataPath.hashCode}"</span>

    <span class="hljs-comment">/*  具体化kafka相应的参数*/</span>
    val caseInsensitiveParams = parameters<span class="hljs-preprocessor">.map</span> { case (k, v) =&gt; (k<span class="hljs-preprocessor">.toLowerCase</span>(Locale<span class="hljs-preprocessor">.ROOT</span>), v) }
    val specifiedKafkaParams =
      parameters
        <span class="hljs-preprocessor">.keySet</span>
        <span class="hljs-preprocessor">.filter</span>(_<span class="hljs-preprocessor">.toLowerCase</span>(Locale<span class="hljs-preprocessor">.ROOT</span>)<span class="hljs-preprocessor">.startsWith</span>(<span class="hljs-string">"kafka."</span>))
        <span class="hljs-preprocessor">.map</span> { k =&gt; k<span class="hljs-preprocessor">.drop</span>(<span class="hljs-number">6</span>)<span class="hljs-preprocessor">.toString</span> -&gt; parameters(k) }
        <span class="hljs-preprocessor">.toMap</span>

    <span class="hljs-comment">/*
    * EarliestOffsetRangeLimit
    * 默认情况是 LatestOffsetRangeLimit
    * SpecificOffsetRangeLimit 用户提供的[TopicPartiton,Long]
    * */</span>
    val startingStreamOffsets = KafkaSourceProvider<span class="hljs-preprocessor">.getKafkaOffsetRangeLimit</span>(caseInsensitiveParams,
      STARTING_OFFSETS_OPTION_KEY, LatestOffsetRangeLimit)

    <span class="hljs-comment">/* KafkaOffsetReader用于kafka自己的consumer api读取offset  */</span>
    val kafkaOffsetReader = new KafkaOffsetReader(
      <span class="hljs-comment">/*  查看是哪种消费kafka方式  assign  subscirbe  subscribepattern 三种*/</span>
      strategy(caseInsensitiveParams),
      <span class="hljs-comment">/*  往ConsumerConfig中设置相应参数的值*/</span>
      kafkaParamsForDriver(specifiedKafkaParams),
      parameters,
      driverGroupIdPrefix = s<span class="hljs-string">"$uniqueGroupId-driver"</span>)

    new KafkaContinuousReader(
      kafkaOffsetReader,
      kafkaParamsForExecutors(specifiedKafkaParams, uniqueGroupId),
      parameters,
      metadataPath,
      startingStreamOffsets,
      failOnDataLoss(caseInsensitiveParams))
  }</code></pre>

<p>创建完相应的ContinuousReader extends BaseStreamingSource, DataSourceReader , <br>
然后会创建DataSet</p>



<pre class="prettyprint"><code class=" hljs avrasm">Dataset<span class="hljs-preprocessor">.ofRows</span>(
          sparkSession,
          StreamingRelationV2(
            s, source, extraOptions<span class="hljs-preprocessor">.toMap</span>,
            tempReader<span class="hljs-preprocessor">.readSchema</span>()<span class="hljs-preprocessor">.toAttributes</span>, v1Relation)(sparkSession))</code></pre>

<p>创建dataset时候使用了刚刚 ds 两次匹配后的得到的结果 <br>
v1Relation：用于将DataSource链接到逻辑计划</p>

<blockquote>
  <p>/** <br>
   * Used to link a streaming [[DataSource]] into a <br>
   * [[org.apache.spark.sql.catalyst.plans.logical.LogicalPlan]]. This is only used for creating <br>
   * a streaming [[org.apache.spark.sql.DataFrame]] from [[org.apache.spark.sql.DataFrameReader]]. <br>
   * It should be used to create [[Source]] and converted to [[StreamingExecutionRelation]] when <br>
   * passing to [[StreamExecution]] to run a query. <br>
   */</p>
</blockquote>

<p>tempReader：KafkaContinuousReader  用于消费kafka相关操作  </p>

<p>然后利用这两个对象进行dataset的创建“</p>



<pre class="prettyprint"><code class=" hljs avrasm">Dataset<span class="hljs-preprocessor">.ofRows</span>(
          sparkSession,
          StreamingRelationV2(
            s, source, extraOptions<span class="hljs-preprocessor">.toMap</span>,
            tempReader<span class="hljs-preprocessor">.readSchema</span>()<span class="hljs-preprocessor">.toAttributes</span>, v1Relation)(sparkSession))
</code></pre>

<p>我们可以看一下ofRows函数实现：</p>



<pre class="prettyprint"><code class=" hljs python"> <span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">ofRows</span><span class="hljs-params">(sparkSession: SparkSession, logicalPlan: LogicalPlan)</span>:</span> DataFrame = {
    val qe = sparkSession.sessionState.executePlan(logicalPlan)
    qe.assertAnalyzed()
    new Dataset[Row](sparkSession, qe, RowEncoder(qe.analyzed.schema))
  }</code></pre>

<p>首先得到拥有这个sparksession.sessionState方法得到对应的SessionState对象，这个对象 <br>
包括SQL配置、临时表、已注册的函数和接受[[org .APACHEC.SPACK.SQL.NET.SQLCONF] ]的所有内容。 <br>
然后调用sessionState.executePlan(logicalPlan)，这里面的logicalPlan就是那会传过来的，代码如下：</p>

<pre class="prettyprint"><code class=" hljs avrasm">StreamingRelationV2(
            s, source, extraOptions<span class="hljs-preprocessor">.toMap</span>,
            tempReader<span class="hljs-preprocessor">.readSchema</span>()<span class="hljs-preprocessor">.toAttributes</span>, v1Relation)(sparkSession)</code></pre>

<p>然后返回对象是QueryExecution类型的，具体实现如下：</p>

<pre class="prettyprint"><code class=" hljs coffeescript"> def executePlan(<span class="hljs-attribute">plan</span>: LogicalPlan): QueryExecution = createQueryExecution(plan)

<span class="hljs-regexp">//</span> createQueryExecution函数在哪里呢? 在sessionState中

  private[sql] <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">SessionState</span>(</span>
    <span class="hljs-attribute">sharedState</span>: SharedState,
    val <span class="hljs-attribute">conf</span>: SQLConf,
    val <span class="hljs-attribute">experimentalMethods</span>: ExperimentalMethods,
    val <span class="hljs-attribute">functionRegistry</span>: FunctionRegistry,
    val <span class="hljs-attribute">udfRegistration</span>: UDFRegistration,
    <span class="hljs-attribute">catalogBuilder</span>: <span class="hljs-function"><span class="hljs-params">()</span> =&gt;</span> SessionCatalog,
    val <span class="hljs-attribute">sqlParser</span>: ParserInterface,
    <span class="hljs-attribute">analyzerBuilder</span>: <span class="hljs-function"><span class="hljs-params">()</span> =&gt;</span> Analyzer,
    <span class="hljs-attribute">optimizerBuilder</span>: <span class="hljs-function"><span class="hljs-params">()</span> =&gt;</span> Optimizer,
    val <span class="hljs-attribute">planner</span>: SparkPlanner,
    val <span class="hljs-attribute">streamingQueryManager</span>: StreamingQueryManager,
    val <span class="hljs-attribute">listenerManager</span>: ExecutionListenerManager,
    <span class="hljs-attribute">resourceLoaderBuilder</span>: <span class="hljs-function"><span class="hljs-params">()</span> =&gt;</span> SessionResourceLoader,
    <span class="hljs-attribute">createQueryExecution</span>: LogicalPlan<span class="hljs-function"> =&gt;</span> QueryExecution,
    <span class="hljs-attribute">createClone</span>: <span class="hljs-function"><span class="hljs-params">(SparkSession, SessionState)</span> =&gt;</span> SessionState) {......}</code></pre>

<p>然后执行ofRows函数主要执行相关查询的工作代码：</p>

<pre class="prettyprint"><code class=" hljs python">qe.assertAnalyzed()
    ↓
    ↓
    ↓
    ↓
<span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">assertAnalyzed</span><span class="hljs-params">()</span>:</span> Unit = analyzed
    ↓
    ↓ 
    ↓
    ↓
lazy val analyzed: LogicalPlan = {
/*
 *当调用SparkSession.getOrCreate()时候，改变这个线程将要返回的sparksession和他的children,
 *这个确保了线程接收的sparksession是一个孤立的而不是全局的
*/
    SparkSession.setActiveSession(sparkSession)
    sparkSession.sessionState.analyzer.executeAndCheck(logical)
  }
    ↓
    ↓
    ↓
    ↓
<span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">executeAndCheck</span><span class="hljs-params">(plan: LogicalPlan)</span>:</span> LogicalPlan = {
    val analyzed = execute(plan)
    <span class="hljs-keyword">try</span> {
      checkAnalysis(analyzed)
      EliminateBarriers(analyzed)
    } catch {
      case e: AnalysisException =&gt;
        val ae = new AnalysisException(e.message, e.line, e.startPosition, Option(analyzed))
        ae.setStackTrace(e.getStackTrace)
        throw ae
    }
  }

    ↓
    ↓
    ↓
    ↓
    //执行逻辑计划
  override <span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">execute</span><span class="hljs-params">(plan: LogicalPlan)</span>:</span> LogicalPlan = {
    AnalysisContext.reset()
    <span class="hljs-keyword">try</span> {
      executeSameContext(plan)
    } <span class="hljs-keyword">finally</span> {
      AnalysisContext.reset()
    }
  }</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>