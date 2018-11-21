---
layout:     post
title:      SparkStreaming消费Kafka消息的简单示例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>Kafka是基于发布／订阅的消息系统，是一个分布式流平台。支持多个生产者和多个消费者，作为消息中间件，Kafka解耦了生产者和消费者，彼此透明，不需要在他们之间建立任何直接的连接，数据从生产者流入Kafka再从Kafka流入消费者。Spark Streaming是Spark核心API的一个扩展，提供高性能高容错性的流数据处理能力。</p><p><img src="https://img-blog.csdn.net/20180513222026382?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Jpd3c2MjA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></p><p>支持包括Kafka，Flume，HDFS/S3，Kinesis，Twitter，TCPSockets等作为数据源。数据经过处理后可以存入HDFS，数据库或展示到仪表盘上。SparkStreaming还可以合影包括SparkMLlib，SparkGraphx等Spark等其它组件无缝结合，得益于Spark的各个组件都是基于同样的Spark Core。<br></p><p>下面是一个简单的示例，利用sparkStream处理kafka中的数据。例子中部署的是kafka0.8.x的版本。例子是通过Direct Approach（No Receivers）的方式来融合Spark Streaming和Kafka。没有使用Receiver-Based的方式。后续再整理一下两种融合方式的区别和优劣。Spark融合Kafka0.10与0.8点Direct Approach方式类似。本文主要是记录一个示例，一些细节和理论部分留着以后再整理吧。<br></p><p>一、配置</p><p></p><div> JDK1.8</div><div>kafka_2.11-0.8.2.0</div><div>Spark-2.3.0-bin-hadoop2.7 </div><p>二、示例代码</p><pre><code class="language-plain">import kafka.serializer.StringDecoder
import org.apache.spark.sql.SparkSession
import org.apache.spark.streaming.kafka.KafkaUtils
import org.apache.spark.streaming.{Seconds, StreamingContext}
/*
* spark消费kafka例子
*
* 2018/5/13
*/
object SparkKafkaTest {
  def main(args: Array[String]): Unit = {

    val spark = SparkSession.builder().appName("spark_kafka").master("local[*]").getOrCreate()
    val batchDuration = Seconds(5) //时间单位为秒
    val streamContext = new StreamingContext(spark.sparkContext, batchDuration)
    streamContext.checkpoint("/Users/eric/SparkWorkspace/checkpoint")
    val topics = Array("behaviors").toSet
    val kafkaParams = Map[String, String]("metadata.broker.list" -&gt; "localhost:9092")
    val stream = KafkaUtils.createDirectStream[String, String, StringDecoder, StringDecoder](streamContext, kafkaParams, topics)
    stream.foreachRDD(rdd =&gt; {
      rdd.foreach(line =&gt; {
        println("key=" + line._1 + "  value=" + line._2)
      })
    })
    streamContext.start()  //spark stream系统启动
    streamContext.awaitTermination() //
  }
}</code></pre><p>三、pom文件</p><pre><code class="language-html">&lt;project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd"&gt;
  &lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;
  &lt;groupId&gt;com.eric.spark&lt;/groupId&gt;
  &lt;artifactId&gt;spark-learning&lt;/artifactId&gt;
  &lt;version&gt;1.0-SNAPSHOT&lt;/version&gt;
  &lt;inceptionYear&gt;2008&lt;/inceptionYear&gt;
  &lt;properties&gt;
      &lt;scala.version&gt;2.11.8&lt;/scala.version&gt;
      &lt;spark.version&gt;2.3&lt;/spark.version&gt;
      &lt;spark.artifact&gt;2.11&lt;/spark.artifact&gt;
      &lt;dependency.scope&gt;compile&lt;/dependency.scope&gt;
  &lt;/properties&gt;

  &lt;repositories&gt;
    &lt;repository&gt;
      &lt;id&gt;scala-tools.org&lt;/id&gt;
      &lt;name&gt;Scala-Tools Maven2 Repository&lt;/name&gt;
      &lt;url&gt;http://scala-tools.org/repo-releases&lt;/url&gt;
    &lt;/repository&gt;
  &lt;/repositories&gt;

  &lt;pluginRepositories&gt;
    &lt;pluginRepository&gt;
      &lt;id&gt;scala-tools.org&lt;/id&gt;
      &lt;name&gt;Scala-Tools Maven2 Repository&lt;/name&gt;
      &lt;url&gt;http://scala-tools.org/repo-releases&lt;/url&gt;
    &lt;/pluginRepository&gt;
  &lt;/pluginRepositories&gt;

  &lt;dependencies&gt;
    &lt;dependency&gt;
      &lt;groupId&gt;log4j&lt;/groupId&gt;
      &lt;artifactId&gt;log4j&lt;/artifactId&gt;
      &lt;version&gt;1.2.17&lt;/version&gt;
    &lt;/dependency&gt;
    &lt;dependency&gt;
      &lt;groupId&gt;org.scala-lang&lt;/groupId&gt;
      &lt;artifactId&gt;scala-library&lt;/artifactId&gt;
      &lt;version&gt;${scala.version}&lt;/version&gt;
    &lt;/dependency&gt;
    &lt;!-- https://mvnrepository.com/artifact/org.apache.spark/spark-core --&gt;
    &lt;dependency&gt;
      &lt;groupId&gt;org.apache.spark&lt;/groupId&gt;
      &lt;artifactId&gt;spark-core_2.11&lt;/artifactId&gt;
      &lt;version&gt;2.3.0&lt;/version&gt;
    &lt;/dependency&gt;
    &lt;!-- https://mvnrepository.com/artifact/org.apache.spark/spark-sql --&gt;
    &lt;dependency&gt;
      &lt;groupId&gt;org.apache.spark&lt;/groupId&gt;
      &lt;artifactId&gt;spark-sql_2.11&lt;/artifactId&gt;
      &lt;version&gt;2.3.0&lt;/version&gt;
    &lt;/dependency&gt;
    &lt;!-- https://mvnrepository.com/artifact/org.apache.spark/spark-hive --&gt;
    &lt;dependency&gt;
      &lt;groupId&gt;org.apache.spark&lt;/groupId&gt;
      &lt;artifactId&gt;spark-hive_2.11&lt;/artifactId&gt;
      &lt;version&gt;2.3.0&lt;/version&gt;
    &lt;/dependency&gt;
    &lt;!-- https://mvnrepository.com/artifact/org.apache.spark/spark-mllib --&gt;
    &lt;dependency&gt;
      &lt;groupId&gt;org.apache.spark&lt;/groupId&gt;
      &lt;artifactId&gt;spark-mllib_2.11&lt;/artifactId&gt;
      &lt;version&gt;2.3.0&lt;/version&gt;
    &lt;/dependency&gt;
    &lt;!-- https://mvnrepository.com/artifact/org.apache.spark/spark-streaming-kafka-0-8 --&gt;
    &lt;dependency&gt;
      &lt;groupId&gt;org.apache.spark&lt;/groupId&gt;
      &lt;artifactId&gt;spark-streaming-kafka-0-8_2.11&lt;/artifactId&gt;
      &lt;version&gt;2.3.0&lt;/version&gt;
    &lt;/dependency&gt;

    &lt;dependency&gt;
      &lt;groupId&gt;junit&lt;/groupId&gt;
      &lt;artifactId&gt;junit&lt;/artifactId&gt;
      &lt;version&gt;4.4&lt;/version&gt;
      &lt;scope&gt;test&lt;/scope&gt;
    &lt;/dependency&gt;
    &lt;dependency&gt;
      &lt;groupId&gt;org.specs&lt;/groupId&gt;
      &lt;artifactId&gt;specs&lt;/artifactId&gt;
      &lt;version&gt;1.2.5&lt;/version&gt;
      &lt;scope&gt;test&lt;/scope&gt;
    &lt;/dependency&gt;
  &lt;/dependencies&gt;

  &lt;build&gt;
    &lt;sourceDirectory&gt;src/main/scala&lt;/sourceDirectory&gt;
    &lt;testSourceDirectory&gt;src/test/scala&lt;/testSourceDirectory&gt;
    &lt;plugins&gt;
      &lt;plugin&gt;
        &lt;groupId&gt;org.scala-tools&lt;/groupId&gt;
        &lt;artifactId&gt;maven-scala-plugin&lt;/artifactId&gt;
        &lt;executions&gt;
          &lt;execution&gt;
            &lt;goals&gt;
              &lt;goal&gt;compile&lt;/goal&gt;
              &lt;goal&gt;testCompile&lt;/goal&gt;
            &lt;/goals&gt;
          &lt;/execution&gt;
        &lt;/executions&gt;
        &lt;configuration&gt;
          &lt;scalaVersion&gt;${scala.version}&lt;/scalaVersion&gt;
          &lt;args&gt;
            &lt;arg&gt;-target:jvm-1.5&lt;/arg&gt;
          &lt;/args&gt;
        &lt;/configuration&gt;
      &lt;/plugin&gt;
      &lt;plugin&gt;
        &lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;
        &lt;artifactId&gt;maven-eclipse-plugin&lt;/artifactId&gt;
        &lt;configuration&gt;
          &lt;downloadSources&gt;true&lt;/downloadSources&gt;
          &lt;buildcommands&gt;
            &lt;buildcommand&gt;ch.epfl.lamp.sdt.core.scalabuilder&lt;/buildcommand&gt;
          &lt;/buildcommands&gt;
          &lt;additionalProjectnatures&gt;
            &lt;projectnature&gt;ch.epfl.lamp.sdt.core.scalanature&lt;/projectnature&gt;
          &lt;/additionalProjectnatures&gt;
          &lt;classpathContainers&gt;
            &lt;classpathContainer&gt;org.eclipse.jdt.launching.JRE_CONTAINER&lt;/classpathContainer&gt;
            &lt;classpathContainer&gt;ch.epfl.lamp.sdt.launching.SCALA_CONTAINER&lt;/classpathContainer&gt;
          &lt;/classpathContainers&gt;
        &lt;/configuration&gt;
      &lt;/plugin&gt;
    &lt;/plugins&gt;
  &lt;/build&gt;
  &lt;reporting&gt;
    &lt;plugins&gt;
      &lt;plugin&gt;
        &lt;groupId&gt;org.scala-tools&lt;/groupId&gt;
        &lt;artifactId&gt;maven-scala-plugin&lt;/artifactId&gt;
        &lt;configuration&gt;
          &lt;scalaVersion&gt;${scala.version}&lt;/scalaVersion&gt;
        &lt;/configuration&gt;
      &lt;/plugin&gt;
    &lt;/plugins&gt;
  &lt;/reporting&gt;
&lt;/project&gt;</code></pre><p>四、创建一个topic</p><p><img src="https://img-blog.csdn.net/20180513222321984?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Jpd3c2MjA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></p><p>五、运行测试代码并向自定义topic输入消息，查看打印结果</p><p><img src="https://img-blog.csdn.net/2018051322235024?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Jpd3c2MjA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></p><p><img src="https://img-blog.csdn.net/20180513222408569?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Jpd3c2MjA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></p><p>对通过kafka-console-producer写入behaviors主题的消息进行简单的打印。<br></p>            </div>
                </div>