---
layout:     post
title:      SparkStreaming读kafka写入HDFS（kerberos认证）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p></p><div class="toc"><div class="toc">
<ul>
<li><a href="#sparkstreaming%E8%AF%BBkafka%E5%86%99%E5%85%A5hdfs" rel="nofollow">SparkStreaming读kafka写入HDFS</a><ul>
<li><a href="#pom" rel="nofollow">pom</a></li>
<li><a href="#%E8%AE%BF%E9%97%AEkerberos%E7%8E%AF%E5%A2%83%E4%B8%8B%E7%9A%84hbase%E4%BB%A3%E7%A0%81" rel="nofollow">访问Kerberos环境下的HBase代码</a></li>
<li><a href="#spark2streaming%E5%BA%94%E7%94%A8%E5%AE%9E%E6%97%B6%E8%AF%BB%E5%8F%96kafka%E4%BB%A3%E7%A0%81" rel="nofollow">Spark2Streaming应用实时读取Kafka代码</a></li>
<li><a href="#%E5%86%99%E5%85%A5%E6%95%B0%E6%8D%AE%E5%88%B0kafka%E4%BB%A3%E7%A0%81" rel="nofollow">写入数据到kafka代码</a></li>
<li>
</ul>
</li>
</ul>
</div>
</div>




<h1 id="sparkstreaming读kafka写入hdfs">SparkStreaming读kafka写入HDFS</h1>

<p>本教程基于CDH5.8.0其它组件版本为：spark2.1.0、kafka0.10.2、HDFS2.6.0</p>



<h2 id="pom">pom</h2>



<pre class="prettyprint"><code class=" hljs xml">  <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-streaming_2.11<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>2.1.0<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">scope</span>&gt;</span>compile<span class="hljs-tag">&lt;/<span class="hljs-title">scope</span>&gt;</span>
  <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-streaming-kafka-0-10_2.11<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>2.1.0<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">scope</span>&gt;</span>compile<span class="hljs-tag">&lt;/<span class="hljs-title">scope</span>&gt;</span>
  <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
       <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.hbase<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
       <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>hbase-client<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
       <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.2.0<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
       <span class="hljs-tag">&lt;<span class="hljs-title">scope</span>&gt;</span>compile<span class="hljs-tag">&lt;/<span class="hljs-title">scope</span>&gt;</span>
  <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span></code></pre>



<h2 id="访问kerberos环境下的hbase代码">访问Kerberos环境下的HBase代码</h2>



<pre class="prettyprint"><code class=" hljs scala"><span class="hljs-keyword">package</span> utils
<span class="hljs-keyword">import</span> java.security.PrivilegedAction
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.HBaseConfiguration
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.client.{Connection, ConnectionFactory}
<span class="hljs-keyword">import</span> org.apache.hadoop.security.UserGroupInformation

<span class="hljs-javadoc">/**
  * Created by LHX on 2018/7/12 15:13.
  * 访问Kerberos环境下的HBase
  */</span>

<span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">HBaseUtil</span> <span class="hljs-keyword">extends</span> <span class="hljs-title">Serializable</span>{</span>

    <span class="hljs-javadoc">/**
      * HBase 配置文件路径
      * <span class="hljs-javadoctag">@return</span>
      */</span>
    <span class="hljs-keyword">def</span> getHBaseConn(): Connection = {
        System.setProperty(<span class="hljs-string">"java.security.krb5.conf"</span>,<span class="hljs-string">"krb5.conf"</span>)
        <span class="hljs-keyword">var</span> table_name =<span class="hljs-string">"DAC_test01"</span>
        <span class="hljs-keyword">val</span> conf = HBaseConfiguration.create()
        <span class="hljs-keyword">val</span> user = <span class="hljs-string">"asmp"</span>
        <span class="hljs-keyword">val</span> keyPath = <span class="hljs-string">"asmp.keytab"</span>
        <span class="hljs-comment">//Kerberos验证</span>
        conf.set(<span class="hljs-string">"hbase.zookeeper.quorum"</span>,<span class="hljs-string">"www.test.com"</span>)
        conf.set(<span class="hljs-string">"hbase.zookeeper.property.clientPort"</span>, <span class="hljs-string">"2181"</span>)
        conf.set(<span class="hljs-string">"hbase.rootdir"</span>, <span class="hljs-string">"hdfs://www.test.com:8020/hbase"</span>)
        conf.set(<span class="hljs-string">"hadoop.security.authentication"</span>,<span class="hljs-string">"Kerberos"</span>)
        conf.set(<span class="hljs-string">"hbase.security.authentication"</span>,<span class="hljs-string">"Kerberos"</span>)
        conf.set(<span class="hljs-string">"hbase.zookeeper.client.keytab.file"</span>, keyPath)
        conf.set(<span class="hljs-string">"hbase.master.kerberos.principal"</span>, <span class="hljs-string">"hbase/_HOST"</span>)
        conf.set(<span class="hljs-string">"hbase.master.keytab.file"</span>, keyPath)
        conf.set(<span class="hljs-string">"hbase.regionserver.kerberos.principal"</span>, <span class="hljs-string">"hbase"</span>)
        conf.set(<span class="hljs-string">"hbase.regionserver.keytab.file"</span>, keyPath)

        UserGroupInformation.setConfiguration(conf)
        UserGroupInformation.loginUserFromKeytab(user, keyPath)
        <span class="hljs-keyword">val</span> loginUser = UserGroupInformation.getLoginUser
        loginUser.doAs(<span class="hljs-keyword">new</span> PrivilegedAction[Connection] {
            <span class="hljs-keyword">override</span> <span class="hljs-keyword">def</span> run(): Connection = ConnectionFactory.createConnection(conf)
        })
    }

}</code></pre>



<h2 id="spark2streaming应用实时读取kafka代码">Spark2Streaming应用实时读取Kafka代码</h2>



<pre class="prettyprint"><code class=" hljs avrasm">package <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.egridcloud</span><span class="hljs-preprocessor">.kafka</span>

import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.commons</span><span class="hljs-preprocessor">.lang</span><span class="hljs-preprocessor">.StringUtils</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span>.{HBaseConfiguration, TableName}
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span>.{Connection, ConnectionFactory, Put}
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Bytes</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.security</span><span class="hljs-preprocessor">.UserGroupInformation</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.common</span><span class="hljs-preprocessor">.serialization</span><span class="hljs-preprocessor">.StringDeserializer</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span>.{SparkConf, SparkContext}
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.sql</span><span class="hljs-preprocessor">.SparkSession</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span><span class="hljs-preprocessor">.kafka</span>010.{ConsumerStrategies, KafkaUtils, LocationStrategies}
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span>.{Seconds, StreamingContext}
import utils<span class="hljs-preprocessor">.HBaseUtil</span>

import scala<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Try</span>
import scala<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.parsing</span><span class="hljs-preprocessor">.json</span><span class="hljs-preprocessor">.JSON</span>

<span class="hljs-comment">/**
  * Created by LHX on 2018/7/12 15:07.
  * describe: Kerberos环境中Spark2Streaming应用实时读取Kafka数据，解析后存入HBase
  * 使用spark2-submit的方式提交作业
  */</span>
object Kafka2Spark2HbaseTest {

    def main(args: Array[String]): Unit = {
System<span class="hljs-preprocessor">.setProperty</span>(<span class="hljs-string">"java.security.krb5.conf"</span>,<span class="hljs-string">"krb5.conf"</span>)
        var table_name =<span class="hljs-string">"DAC_test01"</span>
        val conf = HBaseConfiguration<span class="hljs-preprocessor">.create</span>()
        val user = <span class="hljs-string">"asmp"</span>
        val keyPath = <span class="hljs-string">"asmp.keytab"</span>
        //Kerberos验证
        conf<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"hbase.zookeeper.quorum"</span>,<span class="hljs-string">"www.test.com"</span>)
        conf<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"hbase.zookeeper.property.clientPort"</span>, <span class="hljs-string">"2181"</span>)
        conf<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"hbase.rootdir"</span>, <span class="hljs-string">"hdfs://www.test.com:8020/hbase"</span>)
        conf<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"hadoop.security.authentication"</span>,<span class="hljs-string">"Kerberos"</span>)
        conf<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"hbase.security.authentication"</span>,<span class="hljs-string">"Kerberos"</span>)
        conf<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"hbase.zookeeper.client.keytab.file"</span>, keyPath)
        conf<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"hbase.master.kerberos.principal"</span>, <span class="hljs-string">"hbase/_HOST"</span>)
        conf<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"hbase.master.keytab.file"</span>, keyPath)
        conf<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"hbase.regionserver.kerberos.principal"</span>, <span class="hljs-string">"hbase/_HOST"</span>)
        conf<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"hbase.regionserver.keytab.file"</span>, keyPath)

        UserGroupInformation<span class="hljs-preprocessor">.setConfiguration</span>(conf)
        UserGroupInformation<span class="hljs-preprocessor">.loginUserFromKeytab</span>(user, keyPath)

        //加载配置文件
        val brokers = <span class="hljs-string">"10.122.17.129:9095,10.122.17.130:9095,10.122.17.131:9095"</span>
        val topics = <span class="hljs-string">"stream_test01"</span>
        println(<span class="hljs-string">"kafka.brokers:"</span> + brokers)
        println(<span class="hljs-string">"kafka.topics:"</span> + topics)

        if(StringUtils<span class="hljs-preprocessor">.isEmpty</span>(brokers)|| StringUtils<span class="hljs-preprocessor">.isEmpty</span>(topics) ) {
            println(<span class="hljs-string">"未配置Kafka和Kerberos信息"</span>)
            System<span class="hljs-preprocessor">.exit</span>(<span class="hljs-number">0</span>)
        }
        val topicsSet = topics<span class="hljs-preprocessor">.split</span>(<span class="hljs-string">","</span>)<span class="hljs-preprocessor">.toSet</span>

        val sparkConf = new SparkConf()<span class="hljs-preprocessor">.setAppName</span>(<span class="hljs-string">"Kafka2Spark2HBase"</span>)<span class="hljs-preprocessor">.setMaster</span>(<span class="hljs-string">"local[2]"</span>)
        val sc =new SparkContext(sparkConf)
        //    批次间隔<span class="hljs-number">5</span>秒
        val ssc = new StreamingContext(sc, Seconds(<span class="hljs-number">5</span>))
        //        val ssc = new StreamingContext(spark<span class="hljs-preprocessor">.sparkContext</span>, Seconds(<span class="hljs-number">5</span>)) //设置Spark时间窗口，每<span class="hljs-number">5</span>s处理一次
        val kafkaParams = Map[String, Object](<span class="hljs-string">"bootstrap.servers"</span> -&gt; brokers
            , <span class="hljs-string">"auto.offset.reset"</span> -&gt; <span class="hljs-string">"latest"</span>
            , <span class="hljs-string">"sasl.kerberos.service.name"</span> -&gt; <span class="hljs-string">"kafka"</span>
            , <span class="hljs-string">"key.deserializer"</span> -&gt; classOf[StringDeserializer]
            , <span class="hljs-string">"value.deserializer"</span> -&gt; classOf[StringDeserializer]
            , <span class="hljs-string">"group.id"</span> -&gt; <span class="hljs-string">"testgroup"</span>
        )

        val dStream = KafkaUtils<span class="hljs-preprocessor">.createDirectStream</span>[String, String](ssc,
            LocationStrategies<span class="hljs-preprocessor">.PreferConsistent</span>,
            ConsumerStrategies<span class="hljs-preprocessor">.Subscribe</span>[String, String](topicsSet, kafkaParams))

        dStream<span class="hljs-preprocessor">.foreachRDD</span>(rdd =&gt; {
            rdd<span class="hljs-preprocessor">.foreachPartition</span>(partitionRecords =&gt; {
                val connection = HBaseUtil<span class="hljs-preprocessor">.getHBaseConn</span>() // 获取Hbase连接
                partitionRecords<span class="hljs-preprocessor">.foreach</span>(line =&gt; {
                    //将Kafka的每一条消息解析为JSON格式数据
                    val jsonObj =  JSON<span class="hljs-preprocessor">.parseFull</span>(line<span class="hljs-preprocessor">.value</span>())
                    println(line<span class="hljs-preprocessor">.value</span>())
                    val map:Map[String,Any] = jsonObj<span class="hljs-preprocessor">.get</span><span class="hljs-preprocessor">.asInstanceOf</span>[Map[String, Any]]
                    val rowkey = map<span class="hljs-preprocessor">.get</span>(<span class="hljs-string">"id"</span>)<span class="hljs-preprocessor">.get</span><span class="hljs-preprocessor">.asInstanceOf</span>[String]
                    val name = map<span class="hljs-preprocessor">.get</span>(<span class="hljs-string">"name"</span>)<span class="hljs-preprocessor">.get</span><span class="hljs-preprocessor">.asInstanceOf</span>[String]
                    val sex = map<span class="hljs-preprocessor">.get</span>(<span class="hljs-string">"sex"</span>)<span class="hljs-preprocessor">.get</span><span class="hljs-preprocessor">.asInstanceOf</span>[String]
                    val city = map<span class="hljs-preprocessor">.get</span>(<span class="hljs-string">"city"</span>)<span class="hljs-preprocessor">.get</span><span class="hljs-preprocessor">.asInstanceOf</span>[String]
                    val occupation = map<span class="hljs-preprocessor">.get</span>(<span class="hljs-string">"occupation"</span>)<span class="hljs-preprocessor">.get</span><span class="hljs-preprocessor">.asInstanceOf</span>[String]
                    val mobile_phone_num = map<span class="hljs-preprocessor">.get</span>(<span class="hljs-string">"mobile_phone_num"</span>)<span class="hljs-preprocessor">.get</span><span class="hljs-preprocessor">.asInstanceOf</span>[String]
                    val fix_phone_num = map<span class="hljs-preprocessor">.get</span>(<span class="hljs-string">"fix_phone_num"</span>)<span class="hljs-preprocessor">.get</span><span class="hljs-preprocessor">.asInstanceOf</span>[String]
                    val bank_name = map<span class="hljs-preprocessor">.get</span>(<span class="hljs-string">"bank_name"</span>)<span class="hljs-preprocessor">.get</span><span class="hljs-preprocessor">.asInstanceOf</span>[String]
                    val address = map<span class="hljs-preprocessor">.get</span>(<span class="hljs-string">"address"</span>)<span class="hljs-preprocessor">.get</span><span class="hljs-preprocessor">.asInstanceOf</span>[String]
                    val marriage = map<span class="hljs-preprocessor">.get</span>(<span class="hljs-string">"marriage"</span>)<span class="hljs-preprocessor">.get</span><span class="hljs-preprocessor">.asInstanceOf</span>[String]
                    val child_num = map<span class="hljs-preprocessor">.get</span>(<span class="hljs-string">"child_num"</span>)<span class="hljs-preprocessor">.get</span><span class="hljs-preprocessor">.asInstanceOf</span>[String]

                    val tableName = TableName<span class="hljs-preprocessor">.valueOf</span>(<span class="hljs-string">"ASMP:DAC_test01"</span>)
                    val table = connection<span class="hljs-preprocessor">.getTable</span>(tableName)
                    val put = new Put(Bytes<span class="hljs-preprocessor">.toBytes</span>(rowkey))
                    put<span class="hljs-preprocessor">.addColumn</span>(Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"f1"</span>), Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"name"</span>), Bytes<span class="hljs-preprocessor">.toBytes</span>(name))
                    put<span class="hljs-preprocessor">.addColumn</span>(Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"f1"</span>), Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"sex"</span>), Bytes<span class="hljs-preprocessor">.toBytes</span>(sex))
                    put<span class="hljs-preprocessor">.addColumn</span>(Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"f1"</span>), Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"city"</span>), Bytes<span class="hljs-preprocessor">.toBytes</span>(city))
                    put<span class="hljs-preprocessor">.addColumn</span>(Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"f1"</span>), Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"occupation"</span>), Bytes<span class="hljs-preprocessor">.toBytes</span>(occupation))
                    put<span class="hljs-preprocessor">.addColumn</span>(Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"f1"</span>), Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"mobile_phone_num"</span>), Bytes<span class="hljs-preprocessor">.toBytes</span>(mobile_phone_num))
                    put<span class="hljs-preprocessor">.addColumn</span>(Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"f1"</span>), Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"fix_phone_num"</span>), Bytes<span class="hljs-preprocessor">.toBytes</span>(fix_phone_num))
                    put<span class="hljs-preprocessor">.addColumn</span>(Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"f1"</span>), Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"bank_name"</span>), Bytes<span class="hljs-preprocessor">.toBytes</span>(bank_name))
                    put<span class="hljs-preprocessor">.addColumn</span>(Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"f1"</span>), Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"address"</span>), Bytes<span class="hljs-preprocessor">.toBytes</span>(address))
                    put<span class="hljs-preprocessor">.addColumn</span>(Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"f1"</span>), Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"marriage"</span>), Bytes<span class="hljs-preprocessor">.toBytes</span>(marriage))
                    put<span class="hljs-preprocessor">.addColumn</span>(Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"f1"</span>), Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"child_num"</span>), Bytes<span class="hljs-preprocessor">.toBytes</span>(child_num))

                    Try(table<span class="hljs-preprocessor">.put</span>(put))<span class="hljs-preprocessor">.getOrElse</span>(table<span class="hljs-preprocessor">.close</span>())//将数据写入HBase，若出错关闭table
                    table<span class="hljs-preprocessor">.close</span>()//分区数据写入HBase后关闭连接
                })
                connection<span class="hljs-preprocessor">.close</span>()
            })
        })
        ssc<span class="hljs-preprocessor">.start</span>()
        ssc<span class="hljs-preprocessor">.awaitTermination</span>()
    }

}</code></pre>



<h2 id="写入数据到kafka代码">写入数据到kafka代码</h2>



<pre class="prettyprint"><code class=" hljs scala"><span class="hljs-keyword">package</span> com.##.kafka

<span class="hljs-keyword">import</span> java.util.Properties

<span class="hljs-keyword">import</span> org.apache.kafka.clients.producer.{KafkaProducer, ProducerRecord}
<span class="hljs-keyword">import</span> org.apache.kafka.common.serialization.StringSerializer
<span class="hljs-keyword">import</span> scala.util.parsing.json._
<span class="hljs-javadoc">/**
  * Created by LHX on 2018/7/10 上午 11:00.
  * 测试集群kafka发送Json数据
  */</span>
<span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">TestProducer</span><span class="hljs-params">(val topic: String)</span> <span class="hljs-keyword">extends</span> <span class="hljs-title">Thread</span> {</span>
    <span class="hljs-keyword">var</span> producer: KafkaProducer[String, String] = _

    <span class="hljs-keyword">def</span> init: TestProducer = {
        <span class="hljs-keyword">val</span> props = <span class="hljs-keyword">new</span> Properties()
        props.put(<span class="hljs-string">"bootstrap.servers"</span>, <span class="hljs-string">"10.122.17.129:9095,10.122.17.130:9095,10.122.17.131:9095"</span>)
        props.put(<span class="hljs-string">"key.serializer"</span>, classOf[StringSerializer].getName)
        props.put(<span class="hljs-string">"value.serializer"</span>, classOf[StringSerializer].getName)
        <span class="hljs-keyword">this</span>.producer = <span class="hljs-keyword">new</span> KafkaProducer[String, String](props)
        <span class="hljs-keyword">this</span>
    }
    <span class="hljs-keyword">override</span> <span class="hljs-keyword">def</span> run(): Unit = {

        <span class="hljs-keyword">var</span> num = <span class="hljs-number">1</span>
        <span class="hljs-keyword">while</span> (<span class="hljs-keyword">true</span>) {
            <span class="hljs-keyword">val</span> colors:Map[String,Object]  = Map(<span class="hljs-string">"id"</span> -&gt; s<span class="hljs-string">"65005${num}"</span>, <span class="hljs-string">"name"</span> -&gt; s<span class="hljs-string">"仲淑${num}"</span>, <span class="hljs-string">"sex"</span> -&gt; <span class="hljs-string">"1"</span>,
                <span class="hljs-string">"city"</span> -&gt; <span class="hljs-string">"长治"</span>, <span class="hljs-string">"occupation"</span> -&gt; <span class="hljs-string">"生产工作"</span>, <span class="hljs-string">"mobile_phone_num"</span> -&gt; <span class="hljs-string">"13607268580"</span>, <span class="hljs-string">"fix_phone_num"</span> -&gt; <span class="hljs-string">"15004170180"</span>,
                <span class="hljs-string">"bank_name"</span> -&gt; <span class="hljs-string">"广州银行"</span>, <span class="hljs-string">"address"</span> -&gt; <span class="hljs-string">"台东二路16号"</span>, <span class="hljs-string">"marriage"</span> -&gt; <span class="hljs-string">"1"</span>, <span class="hljs-string">"child_num"</span> -&gt; <span class="hljs-string">"1"</span>)
            <span class="hljs-keyword">val</span> json = JSONObject(colors)
            <span class="hljs-comment">//要发送的消息</span>
            <span class="hljs-keyword">val</span> messageStr = <span class="hljs-keyword">new</span> String(json.toString())
            println(s<span class="hljs-string">"send:${messageStr}"</span>)
            producer.send(<span class="hljs-keyword">new</span> ProducerRecord[String, String](<span class="hljs-keyword">this</span>.topic, messageStr))
            num += <span class="hljs-number">1</span>
            <span class="hljs-keyword">if</span> (num &gt; <span class="hljs-number">10</span>) num = <span class="hljs-number">0</span>
            Thread.sleep(<span class="hljs-number">3000</span>)
        }
    }
}
<span class="hljs-comment">// 伴生对象</span>
<span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">TestProducer</span> </span>
    <span class="hljs-keyword">def</span> apply(topic: String): TestProducer = <span class="hljs-keyword">new</span> TestProducer(topic).init
}

<span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">SparkWrite2Kafka</span> {</span>
    <span class="hljs-keyword">def</span> main(args: Array[String]): Unit = {
        <span class="hljs-keyword">val</span> producer = TestProducer(<span class="hljs-string">"stream_test01"</span>)
        producer.start()
    }
}
</code></pre>

<p>注意事项： <br>
先执行Kafka2Spark2HbaseTest.scala接收kafka数据， <br>
再执行SparkWrite2Kafka.scala写入数据到kafka</p>



<h2 id="title"><img src="https://img-blog.csdn.net/20180716153752474?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xoeHNpcg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=" 写入数据到kafka " title=""></h2>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>