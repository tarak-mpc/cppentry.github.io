---
layout:     post
title:      SparkStreaming整合Kafka-0.8的官方文档要点翻译
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1>Spark Streaming + Kafka Integration Guide (Kafka broker version 0.8.2.1 or higher)<br>
Note: Kafka 0.8 support is deprecated as of Spark 2.3.0.</h1>

<p> </p>

<p>Here we explain how to configure Spark Streaming to receive data from Kafka. <span style="color:#f33b45;">There are two approaches to this - the old approach using Receivers and Kafka’s high-level API, and a new approach (introduced in Spark 1.3) without using Receivers.</span> They have different programming models, performance characteristics, and semantics guarantees, so read on for more details. Both approaches are considered stable APIs as of the current version of Spark.</p>

<p><u><strong>SparkStreaming整合kafka有两种方式：</strong></u></p>

<p><u><strong>第一种就是：<span style="color:#f33b45;">using Receivers and Kafka’s high-level API</span></strong></u></p>

<p><u><strong>第二种就是：<span style="color:#f33b45;">without using Receivers</span></strong></u></p>

<p> </p>

<h2>Approach 1: Receiver-based Approach</h2>

<p><br>
This approach <span style="color:#f33b45;">uses a Receiver to receive the data</span>. The Receiver is implemented using the <span style="color:#f33b45;">Kafka high-level consumer API</span>. As with all receivers, the data received from Kafka through a Receiver is stored in Spark executors, and then jobs launched by Spark Streaming processes the data.</p>

<p><u><strong>第一种消费方式告诉我们，使用kafka的high-level comsumer API结合Receiver数据接收器接收数据进行sparkStreaming的程序处理</strong></u></p>

<p> </p>

<p>However, under default configuration, this approach <span style="color:#f33b45;">can lose data under failures</span> (see receiver reliability. <span style="color:#f33b45;">To ensure zero-data loss, you have to additionally enable Write Ahead Logs in Spark Streaming</span> (introduced in Spark 1.2). <span style="color:#f33b45;">This synchronously saves all the received Kafka data into write ahead logs on a distributed file system</span> (e.g HDFS), <span style="color:#f33b45;">so that all the data can be recovered on failure</span>. See Deploying section in the streaming programming guide for more details on Write Ahead Logs.</p>

<p><u><strong>这种方式有可能在压力过载下导致丢失数据，如果要保证不丢失数据，可以额外的开启sparkStreaming的WAL机制，这样，sparkStreaming程序就会在一个例如HDFS的分布式文件系统中对消费进度进行持久化存储。如果应用程序重启，那么sparkStreaming应用程序的状态就可以得到恢复</strong></u></p>

<p> </p>

<p>Next, we discuss how to use this approach in your streaming application.</p>

<p>Linking: For Scala/Java applications using SBT/Maven project definitions, link your streaming application with the following artifact (see Linking section in the main programming guide for further information).</p>

<pre class="has">
<code> groupId = org.apache.spark
 artifactId = spark-streaming-kafka-0-8_2.11
 version = 2.3.1</code></pre>

<p><u><strong>这是编写scala或者javad代码时，要引入的sparkStreaming整合kafka-0.8的依赖</strong></u></p>

<p><br>
For Python applications, you will have to add this above library and its dependencies when deploying your application. See the Deploying subsection below.</p>

<p>Programming: In the streaming application code, import KafkaUtils and create an input DStream as follows.</p>

<p>Scala</p>

<pre class="has">
<code class="language-java">import org.apache.spark.streaming.kafka._

 val kafkaStream = KafkaUtils.createStream(streamingContext,
     [ZK quorum], [consumer group id], [per-topic number of Kafka partitions to consume])</code></pre>

<p><br>
You can also specify the key and value classes and their corresponding decoder classes using variations of createStream. See the API docs.</p>

<p><strong>这种方式可以指定从kafka中读取到的数据的key-value的类型， 以及他们的解码器</strong></p>

<p> </p>

<p>Points to remember:</p>

<p><strong>要点：</strong></p>

<p><span style="color:#f33b45;">Topic partitions in Kafka does not correlate to partitions of RDDs generated in Spark Streaming. So increasing the number of topic-specific partitions in the KafkaUtils.createStream() only increases the number of threads using which topics that are consumed within a single receiver. It does not increase the parallelism of Spark in processing the data</span>. Refer to the main document for more information on that.</p>

<p><strong><u>Kafka的分区数和SparkStreaming应用程序中生成的RDD的分区数据不是对应的。所以增加Kafka的Topic的分区数，仅仅只能给每个接收数据的Receiver的消费多增加消费线程， 这并没有增加SparkStreaming消费数据的并行度</u></strong></p>

<p> </p>

<p>Multiple Kafka input DStreams can be created with different groups and topics for parallel receiving of data using multiple receivers.</p>

<p>If you have enabled Write Ahead Logs with a replicated file system like HDFS, the received data is already being replicated in the log. Hence, the storage level in storage level for the input stream to StorageLevel.MEMORY_AND_DISK_SER (that is, use KafkaUtils.createStream(..., StorageLevel.MEMORY_AND_DISK_SER)).</p>

<p>Deploying: As with any Spark applications, spark-submit is used to launch your application. However, the details are slightly different for Scala/Java applications and Python applications.</p>

<p>For Scala and Java applications, if you are using SBT or Maven for project management, then package spark-streaming-kafka-0-8_2.11 and its dependencies into the application JAR. Make sure spark-core_2.11 and spark-streaming_2.11 are marked as provided dependencies as those are already present in a Spark installation. Then use spark-submit to launch your application (see Deploying section in the main programming guide).</p>

<p>For Python applications which lack SBT/Maven project management, spark-streaming-kafka-0-8_2.11 and its dependencies can be directly added to spark-submit using --packages (see Application Submission Guide). That is,</p>

<p> ./bin/spark-submit --packages org.apache.spark:spark-streaming-kafka-0-8_2.11:2.3.1 ...<br>
Alternatively, you can also download the JAR of the Maven artifact spark-streaming-kafka-0-8-assembly from the Maven repository and add it to spark-submit with --jars.</p>

<p> </p>

<h2>Approach 2: Direct Approach (No Receivers)</h2>

<p><br><span style="color:#f33b45;">This new receiver-less “direct” approach has been introduced in Spark 1.3 to ensure stronger end-to-end guarantees</span>. Instead of using receivers to receive data, this approach periodically <span style="color:#f33b45;">queries Kafka for the latest offsets in each topic+partition,</span> and <span style="color:#f33b45;">accordingly defines the offset ranges to process in each batch. When the jobs to process the data are launched, Kafka’s simple consumer API is used to read the defined ranges of offsets from Kafka</span> (similar to read files from a file system). Note that this feature was introduced in Spark 1.3 for the Scala and Java API, in Spark 1.4 for the Python API.</p>

<p><u><strong>这种在Spark-1.3引出的新的不基于Receriver的"Direct"方式确保更强大的端对端保证。跟过去的基于Receiver接收数据相比，这种新的方式周期性的查询kafka中的每个partition的最新offset便宜范围。通过这个offset范围来指定这个时间段内批次数据的范围。当对应的处理这个批次数据的task启动起来的时候，默认会使用Kafka's simple Consumer API来按照这个offset范围读取数据</strong></u></p>

<p> </p>

<p>This approach has the following advantages over the receiver-based approach (i.e. Approach 1).</p>

<blockquote>
<p><strong><span style="color:#f33b45;">Simplified Parallelism</span></strong>: No need to create multiple input Kafka streams and union them. With directStream, Spark Streaming will create as many RDD partitions as there are Kafka partitions to consume, which will all read data from Kafka in parallel. So there is a <span style="color:#f33b45;"><strong>one-to-one mapping between Kafka and RDD partitions,</strong></span> which is easier to understand and tune.</p>

<p><u><strong>跟以前创建多个并行的KafkaStream来结合处理相对比，DirectStream的方式中，SparkStreaming应用程序将创建和Kafka中这个Topic的parititons的数量一样的RDD，这些消费者讲并行的进行所有数据的读取。所以这种方式就是相对容易理解的一对一映射关系：RDD的Partition和Topic的Partition一一对应</strong></u></p>
</blockquote>

<blockquote>
<p><span style="color:#f33b45;"><strong>Efficiency</strong></span>: <span style="color:#f33b45;">Achieving zero-data loss in the first approach required the data to be stored in a Write Ahead Log</span>, which further replicated the data. This is actually inefficient as the data effectively gets replicated twice - once by Kafka, and a second time by the Write Ahead Log. This second approach eliminates the problem as there is no receiver, and hence no need for Write Ahead Logs. As long as you have sufficient Kafka retention, messages can be recovered from Kafka.</p>

<p><u><strong>为了确保数据零丢失，第一种方式需要把数据通过WAL方式存储在日志中。并追加备份。第二种方式没有Receiver，所以也就么有必要记录操作日志了。相比来说，性能有很大提升。</strong></u></p>
</blockquote>

<blockquote>
<p><span style="color:#f33b45;"><strong>Exactly-once semantics</strong></span>: <span style="color:#f33b45;">The first approach uses Kafka’s high level API to store consumed offsets in Zookeeper</span>. This is traditionally the way to consume data from Kafka. While this approach (in combination with write ahead logs) can ensure zero data loss (i.e. at-least once semantics), there is a small chance some records may get consumed twice under some failures. This occurs because of inconsistencies between <span style="color:#f33b45;">data reliably received by Spark Streaming and offsets tracked by Zookeeper</span>. Hence, in this second approach, we use <span style="color:#f33b45;">simple Kafka API that does not use Zookeeper</span>. <span style="color:#f33b45;"><strong>Offsets are tracked by Spark Streaming within its checkpoints. </strong></span>This eliminates inconsistencies between Spark Streaming and Zookeeper/Kafka, and so each record is received by Spark Streaming effectively exactly once despite failures. In order to achieve exactly-once semantics for output of your results, your output operation that saves the data to an external data store must be either <span style="color:#f33b45;"><strong>idempotent</strong></span>, or an <span style="color:#f33b45;"><strong>atomic transaction</strong></span> that <span style="color:#f33b45;"><strong>saves results and offsets</strong></span> (see Semantics of output operations in the main programming guide for further information).</p>

<p><u><strong>ExactlyOnce语义：第一种使用receiver的传统的从Kafka消费数据的方式，是通过Kafka's High-Level API保存offset到zookeeper来确保ExactlyOnce的语义的。因为这种语义使用了WAL机制记录消费日志，所以可以确保零数据丢失。但是会在某些异常情况下导致个别消息被消费了两次。这种异常情况出现在SparkStreaming应用程序的Receiver接收了数据以后，和发送回执给ZooKeeper之间。因此，第二种方式，我们使用不需要整合ZooKeeper的Simple Kafka API。Offset被保存在sparkStreaming应用程序的checkPointDirectory中了。这就消除了SparkStreaming和ZooKeeper/Kafka之间的冲突。所以不管是否有异常，SparkStreaming都可以高效的从Kafka中获取数据消费。但是如果要保证，那么就必须要输出手段保证幂等性或者事务操作</strong></u></p>
</blockquote>

<p><span style="color:#f33b45;">Note that one disadvantage of this approach is that it does not update offsets in Zookeeper, hence Zookeeper-based Kafka monitoring tools will not show progress</span>. However, you can access the offsets processed by this approach in each batch and update Zookeeper yourself (see below).</p>

<p><u><strong>这种方式也有一个缺点，就是基于zookeeper的kafka管理工具，将不显示topic的消费进度。然而，用户可以自己保存topic的每个partition的offset到zookeeper中。</strong></u></p>

<p> </p>

<p>Next, we discuss how to use this approach in your streaming application.</p>

<p>Linking: This approach is supported only in Scala/Java application. Link your SBT/Maven project with the following artifact (see Linking section in the main programming guide for further information).</p>

<pre class="has">
<code> groupId = org.apache.spark
 artifactId = spark-streaming-kafka-0-8_2.11
 version = 2.3.1</code></pre>

<p><br>
Programming: In the streaming application code, import KafkaUtils and create an input DStream as follows.</p>

<pre class="has">
<code class="language-java"> import org.apache.spark.streaming.kafka._

 val directKafkaStream = KafkaUtils.createDirectStream[
     [key class], [value class], [key decoder class], [value decoder class] ](
     streamingContext, [map of Kafka parameters], [set of topics to consume])</code></pre>

<p>You can also pass a messageHandler to createDirectStream to access MessageAndMetadata that contains metadata about the current message and transform it to any desired type. See the API docs.</p>

<p> </p>

<p><span style="color:#f33b45;"><strong>In the Kafka parameters, you must specify either metadata.broker.list or bootstrap.servers. By default, it will start consuming from the latest offset of each Kafka partition. If you set configuration auto.offset.reset in Kafka parameters to smallest, then it will start consuming from the smallest offset.</strong></span></p>

<p><u><strong>如果使用KafkaUtils.createDirectStream的方式，那么必须传入一个表示kafka的broker的地址列表Kafka的参数，key可以使用"metadata.broker.list"或者"bootstrap.servers"。默认情况下，将从parition的最近的offset开始消费。如果你设置“auto.offset.reset”=“smallest”, 那么sparkStreaming应用程序将从该partition的最小offset开始消费</strong></u></p>

<p> </p>

<p>Y<span style="color:#f33b45;">ou can also start consuming from any arbitrary offset using other variations of KafkaUtils.createDirectStream.</span> Furthermore, if you want to access the Kafka offsets consumed in each batch, you can do the following.</p>

<p><u><strong>你也可以使用KafkaUtils.createDirectStream的其他重载方法从任意offset开始消费。如果你想获取kafka的被消费的每个批次的offset，那么可以使用下面这种API：</strong></u><br>
Scala实现：</p>

<pre class="has">
<code class="language-java">// Hold a reference to the current offset ranges, so it can be used downstream
 var offsetRanges = Array.empty[OffsetRange]

 directKafkaStream.transform { rdd =&gt;
   offsetRanges = rdd.asInstanceOf[HasOffsetRanges].offsetRanges
   rdd
 }.map {
           ...
 }.foreachRDD { rdd =&gt;
   for (o &lt;- offsetRanges) {
     println(s"${o.topic} ${o.partition} ${o.fromOffset} ${o.untilOffset}")
   }
   ...
 }</code></pre>

<p>You can use this to update Zookeeper yourself if you want Zookeeper-based Kafka monitoring tools to show progress of the streaming application.</p>

<p><u><strong>如果你能获取到offset,那么就可以自己手动保存该offset到ZooKeeper,这样，基于zookeeper的kafka管理工具就可以显示流式处理应用程序的消费数据的进度了。</strong></u></p>

<p> </p>

<p>1、Note that the typecast to HasOffsetRanges will only succeed if it is done in the first method called on the directKafkaStream, not later down a chain of methods. You can use transform() instead of foreachRDD() as your first method call in order to access offsets, then call further Spark methods. However, <span style="color:#f33b45;">be aware that the one-to-one mapping between RDD partition and Kafka partition does not remain after any methods that shuffle or repartition, e.g. reduceByKey() or window()</span>.</p>

<p><u><strong>注意类型转换HasOffsetRanges 只会成功，如果是在第一个方法中调用的结果directKafkaStream，不是后来一系列的方法。请注意，RDD分区和Kafka分区之间的一对一映射在任何随机或重新分区的方法（例如reduceByKey（）或window（））后不会保留。</strong></u></p>

<p> </p>

<p>2、Another thing to note is that since this approach does not use Receivers, the standard receiver-related (that is, configurations of the form <span style="color:#f33b45;"><strong>spark.streaming.receiver.*</strong></span> ) will not apply to the input DStreams created by this approach (will apply to other input DStreams though). Instead, use the configurations <span style="color:#f33b45;"><strong>spark.streaming.kafka.*</strong></span>. An important one is <span style="color:#f33b45;"><strong>spark.streaming.kafka.maxRatePerPartition</strong></span> which is the maximum rate (in messages per second) at which each Kafka partition will be read by this direct API.</p>

<p><u><strong>在基于Receiver实现的消费方式中，spark.streaming.receiver.* 这样的参数都将失效。不基于Receiver的方式中，如果想使用一些参数控制程序的运行，那么请使用spark.streaming.kafka.*。其中有一个最重要的参数就是控制消费速率的参数：spark.streaming.kafka.maxRatePerPartition， 它表示在使用Direct API消费数据的时候，每秒钟，每个分区 最多能被消费的消息数量。</strong></u></p>

<p>Deploying: This is same as the first approach.</p>

<p> </p>

<p>借鉴其他大牛的一个总结：</p>

<h3 id="1基于接收者的方法"><strong>-1,基于接收者的方法</strong></h3>

<p><strong>算子</strong>：KafkaUtils.createStream <br><strong>方法</strong>：PUSH，从topic中去推送数据，将数据推送过来 <br><strong>API</strong>：调用的Kafka高级API <br><strong>效果</strong>：SparkStreaming中的Receivers，恰好Kafka有发布/订阅 ，然而：<strong>此种方式企业不常用</strong>，说明有BUG，不符合企业需求。因为：接收到的数据存储在Executor的内存，会出现数据漏处理或者多处理状况 <br><strong>解释</strong>：这种方法使用Receiver来接收数据。Receiver是使用Kafka高级消费者API实现的。与所有的接收者一样，通过Receiver从Kafka接收的数据存储在Spark执行程序exector中，然后由Spark Streaming启动的作业处理数据。但是，在默认配置下，这种方法可能会在失败时丢失数据。为了确保零数据丢失，您必须在Spark Streaming（在Spark 1.2中引入）中额外启用写入日志，同时保存所有接收到的Kafka数据写入分布式文件系统（例如HDFS）的预先写入日志，以便所有数据都可以在失败时恢复。 <br><strong>缺点</strong>： <br>
①、Kafka中的主题分区与Spark Streaming中生成的RDD的分区不相关。因此，增加主题特定分区KafkaUtils.createStream()的数量只会增加在单个接收器中使用哪些主题消耗的线程的数量。在处理数据时不会增加Spark的并行性 <br>
②、多个kafka输入到DStream会创建多个group和topic，用于使用多个接收器并行接收数据 <br>
③、如果已经使用HDFS等复制文件系统启用了写入日志，则接收到的数据已经在日志中复制。因此，输入流的存储级别为存储级别StorageLevel.MEMORY_AND_DISK_SER</p>

<h3 id="2直接方法无接收者"><a name="t1"></a><strong>-2,直接方法（无接收者）</strong></h3>

<p><strong>算子</strong>：KafkaUtils.createDirectStream <br><strong>方式</strong>：PULL，到topic中去拉取数据。 <br><strong>API</strong>：kafka低级API <br><strong>效果</strong>：每次到Topic的每个分区依据偏移量进行获取数据，拉取数据以后进行处理，可以实现高可用 <br><strong>解释</strong>：在Spark 1.3中引入了这种新的无接收器“直接”方法，以确保更强大的端到端保证。这种方法不是使用接收器来接收数据，而是定期查询Kafka在每个topic+分partition中的最新偏移量，并相应地定义要在每个批次中处理的偏移量范围。当处理数据的作业启动时，Kafka简单的客户API用于读取Kafka中定义的偏移范围（类似于从文件系统读取文件）。请注意，此功能在Spark 1.3中为Scala和Java API引入，在Spark 1.4中针对Python API引入。 <br><strong>优势</strong>： <br>
①、简化的并行性：不需要创建多个输入Kafka流并将其合并。与此同时directStream，Spark Streaming将创建与使用Kafka分区一样多的RDD分区，这些分区将全部从Kafka并行读取数据。所以<strong>在Kafka和RDD分区之间有一对一的映射关系，这更容易理解和调整</strong>。</p>

<p>②、效率：在第一种方法中实现零数据丢失需要将数据存储在预写日志中，这会进一步复制数据。这实际上是效率低下的，因为数据被有效地复制了两次，一次是由Kafka，另一次是由预先写入日志（Write Ahead Log）复制。此方法消除了这个问题，因为没有接收器，因此不需要预先写入日志。<strong>只要你有足够的kafka保留，消息可以从kafka恢复</strong>。</p>

<p>③、精确语义：第一种方法是使用Kafka的高级API在Zookeeper中存储消耗的偏移量。传统上这是从Kafka消费数据的方式。虽然这种方法（合并日志）可以确保零数据丢失，但在某些失败情况下，很小的几率两次数据都同时丢失，发生这种情况是因为Spark Streaming可靠接收到的数据与Zookeeper跟踪的偏移之间的不一致。因此，在第二种方法中，我们使用不使用Zookeeper的简单Kafka API。在其检查点内，Spark Streaming跟踪偏移量。<strong>这消除了Spark Streaming和Zookeeper / Kafka之间的不一致性</strong>，因此Spark Streaming每次记录都会在发生故障时有效地接收一次。</p>

<p>请注意，这种方法的一个缺点是它不会更新Zookeeper中的偏移量，因此基于Zookeeper的Kafka监控工具将不会显示进度。但是，您可以在每个批次中访问由此方法处理的偏移量，并自己更新Zookeeper</p>            </div>
                </div>