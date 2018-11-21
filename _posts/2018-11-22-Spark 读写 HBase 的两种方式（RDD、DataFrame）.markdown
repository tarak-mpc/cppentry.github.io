---
layout:     post
title:      Spark 读写 HBase 的两种方式（RDD、DataFrame）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/yitengtongweishi/article/details/82556824				</div>
								            <div id="content_views" class="markdown_views prism-github-gist">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="使用-saveashadoopdataset-写入数据">使用 saveAsHadoopDataset 写入数据</h2>



<pre class="prettyprint"><code class=" hljs avrasm">import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span>.{HBaseConfiguration, HTableDescriptor, TableName}
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span>.{HBaseAdmin, Put, Result}
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.ImmutableBytesWritable</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.mapreduce</span><span class="hljs-preprocessor">.TableInputFormat</span>
//import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.mapreduce</span><span class="hljs-preprocessor">.TableOutputFormat</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.mapred</span><span class="hljs-preprocessor">.TableOutputFormat</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Bytes</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapred</span><span class="hljs-preprocessor">.JobConf</span>
//import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapreduce</span><span class="hljs-preprocessor">.Job</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.log</span>4j.{Level, Logger}
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.sql</span><span class="hljs-preprocessor">.SparkSession</span>

<span class="hljs-comment">/**
  * Created by blockchain on 18-9-9 下午3:45 in Beijing.
  */</span>

object SparkHBaseRDD {
  def main(args: Array[String]) {
    // 屏蔽不必要的日志显示在终端上
    Logger<span class="hljs-preprocessor">.getLogger</span>(<span class="hljs-string">"org.apache.spark"</span>)<span class="hljs-preprocessor">.setLevel</span>(Level<span class="hljs-preprocessor">.WARN</span>)

    val spark = SparkSession<span class="hljs-preprocessor">.builder</span>()<span class="hljs-preprocessor">.appName</span>(<span class="hljs-string">"SparkHBaseRDD"</span>)<span class="hljs-preprocessor">.getOrCreate</span>()
    val sc = spark<span class="hljs-preprocessor">.sparkContext</span>

    val tablename = <span class="hljs-string">"SparkHBase"</span>

    val hbaseConf = HBaseConfiguration<span class="hljs-preprocessor">.create</span>()
    hbaseConf<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"hbase.zookeeper.quorum"</span>,<span class="hljs-string">"localhost"</span>)  //设置zooKeeper集群地址，也可以通过将hbase-site<span class="hljs-preprocessor">.xml</span>导入classpath，但是建议在程序里这样设置
    hbaseConf<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"hbase.zookeeper.property.clientPort"</span>, <span class="hljs-string">"2181"</span>)       //设置zookeeper连接端口，默认<span class="hljs-number">2181</span>
    hbaseConf<span class="hljs-preprocessor">.set</span>(TableOutputFormat<span class="hljs-preprocessor">.OUTPUT</span>_TABLE, tablename)

    // 初始化job，TableOutputFormat 是 org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.mapred</span> 包下的
    val jobConf = new JobConf(hbaseConf)
    jobConf<span class="hljs-preprocessor">.setOutputFormat</span>(classOf[TableOutputFormat])

    val indataRDD = sc<span class="hljs-preprocessor">.makeRDD</span>(Array(<span class="hljs-string">"2,jack,16"</span>, <span class="hljs-string">"1,Lucy,15"</span>, <span class="hljs-string">"5,mike,17"</span>, <span class="hljs-string">"3,Lily,14"</span>))

    val rdd = indataRDD<span class="hljs-preprocessor">.map</span>(_<span class="hljs-preprocessor">.split</span>(<span class="hljs-string">','</span>))<span class="hljs-preprocessor">.map</span>{ arr=&gt;
      <span class="hljs-comment">/*一个Put对象就是一行记录，在构造方法中指定主键
       * 所有插入的数据 须用 org.apache.hadoop.hbase.util.Bytes.toBytes 转换
       * Put.addColumn 方法接收三个参数：列族，列名，数据*/</span>
      val put = new Put(Bytes<span class="hljs-preprocessor">.toBytes</span>(arr(<span class="hljs-number">0</span>)))
      put<span class="hljs-preprocessor">.addColumn</span>(Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"cf1"</span>),Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"name"</span>),Bytes<span class="hljs-preprocessor">.toBytes</span>(arr(<span class="hljs-number">1</span>)))
      put<span class="hljs-preprocessor">.addColumn</span>(Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"cf1"</span>),Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"age"</span>),Bytes<span class="hljs-preprocessor">.toBytes</span>(arr(<span class="hljs-number">2</span>)))
      (new ImmutableBytesWritable, put)
    }
    rdd<span class="hljs-preprocessor">.saveAsHadoopDataset</span>(jobConf)

    spark<span class="hljs-preprocessor">.stop</span>()
  }
}</code></pre>

<p>在 <a href="https://blog.csdn.net/yitengtongweishi/article/details/82154631" rel="nofollow">HBase shell</a> 中 查看写入的数据</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">005</span>:<span class="hljs-number">0</span>* scan <span class="hljs-string">'SparkHBase'</span>
ROW                        COLUMN+CELL                                                                
 <span class="hljs-number">1</span>                         column=cf1:age, timestamp=<span class="hljs-number">1536494344379</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">15</span>                          
 <span class="hljs-number">1</span>                         column=cf1:name, timestamp=<span class="hljs-number">1536494344379</span>, <span class="hljs-built_in">value</span>=Lucy                       
 <span class="hljs-number">2</span>                         column=cf1:age, timestamp=<span class="hljs-number">1536494344380</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">16</span>                          
 <span class="hljs-number">2</span>                         column=cf1:name, timestamp=<span class="hljs-number">1536494344380</span>, <span class="hljs-built_in">value</span>=jack                       
 <span class="hljs-number">3</span>                         column=cf1:age, timestamp=<span class="hljs-number">1536494344379</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">14</span>                          
 <span class="hljs-number">3</span>                         column=cf1:name, timestamp=<span class="hljs-number">1536494344379</span>, <span class="hljs-built_in">value</span>=Lily                       
 <span class="hljs-number">5</span>                         column=cf1:age, timestamp=<span class="hljs-number">1536494344380</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">17</span>                          
 <span class="hljs-number">5</span>                         column=cf1:name, timestamp=<span class="hljs-number">1536494344380</span>, <span class="hljs-built_in">value</span>=mike                       
<span class="hljs-number">4</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0940</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">006</span>:<span class="hljs-number">0</span>&gt; </code></pre>

<p>如上所示，写入成功。</p>



<h2 id="使用-newapihadooprdd-读取数据">使用 newAPIHadoopRDD 读取数据</h2>



<pre class="prettyprint"><code class=" hljs avrasm">import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span>.{HBaseConfiguration, HTableDescriptor, TableName}
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span>.{HBaseAdmin, Put, Result}
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.ImmutableBytesWritable</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.mapreduce</span><span class="hljs-preprocessor">.TableInputFormat</span>
//import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.mapreduce</span><span class="hljs-preprocessor">.TableOutputFormat</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.mapred</span><span class="hljs-preprocessor">.TableOutputFormat</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Bytes</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapred</span><span class="hljs-preprocessor">.JobConf</span>
//import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapreduce</span><span class="hljs-preprocessor">.Job</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.log</span>4j.{Level, Logger}
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.sql</span><span class="hljs-preprocessor">.SparkSession</span>

<span class="hljs-comment">/**
  * Created by blockchain on 18-9-9 下午3:45 in Beijing.
  */</span>

object SparkHBaseRDD {
  def main(args: Array[String]) {
    // 屏蔽不必要的日志显示在终端上
    Logger<span class="hljs-preprocessor">.getLogger</span>(<span class="hljs-string">"org.apache.spark"</span>)<span class="hljs-preprocessor">.setLevel</span>(Level<span class="hljs-preprocessor">.WARN</span>)

    val spark = SparkSession<span class="hljs-preprocessor">.builder</span>()<span class="hljs-preprocessor">.appName</span>(<span class="hljs-string">"SparkHBaseRDD"</span>)<span class="hljs-preprocessor">.getOrCreate</span>()
    val sc = spark<span class="hljs-preprocessor">.sparkContext</span>

    val tablename = <span class="hljs-string">"SparkHBase"</span>

    val hbaseConf = HBaseConfiguration<span class="hljs-preprocessor">.create</span>()
    hbaseConf<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"hbase.zookeeper.quorum"</span>,<span class="hljs-string">"localhost"</span>)  //设置zooKeeper集群地址，也可以通过将hbase-site<span class="hljs-preprocessor">.xml</span>导入classpath，但是建议在程序里这样设置
    hbaseConf<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"hbase.zookeeper.property.clientPort"</span>, <span class="hljs-string">"2181"</span>)       //设置zookeeper连接端口，默认<span class="hljs-number">2181</span>
    hbaseConf<span class="hljs-preprocessor">.set</span>(TableInputFormat<span class="hljs-preprocessor">.INPUT</span>_TABLE, tablename)

    // 如果表不存在，则创建表
    val admin = new HBaseAdmin(hbaseConf)
    if (!admin<span class="hljs-preprocessor">.isTableAvailable</span>(tablename)) {
      val tableDesc = new HTableDescriptor(TableName<span class="hljs-preprocessor">.valueOf</span>(tablename))
      admin<span class="hljs-preprocessor">.createTable</span>(tableDesc)
    }

    //读取数据并转化成rdd TableInputFormat 是 org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.mapreduce</span> 包下的
    val hBaseRDD = sc<span class="hljs-preprocessor">.newAPIHadoopRDD</span>(hbaseConf, classOf[TableInputFormat],
      classOf[ImmutableBytesWritable],
      classOf[Result])

    hBaseRDD<span class="hljs-preprocessor">.foreach</span>{ case (_ ,result) =&gt;
      //获取行键
      val key = Bytes<span class="hljs-preprocessor">.toString</span>(result<span class="hljs-preprocessor">.getRow</span>)
      //通过列族和列名获取列
      val name = Bytes<span class="hljs-preprocessor">.toString</span>(result<span class="hljs-preprocessor">.getValue</span>(<span class="hljs-string">"cf1"</span><span class="hljs-preprocessor">.getBytes</span>,<span class="hljs-string">"name"</span><span class="hljs-preprocessor">.getBytes</span>))
      val age = Bytes<span class="hljs-preprocessor">.toString</span>(result<span class="hljs-preprocessor">.getValue</span>(<span class="hljs-string">"cf1"</span><span class="hljs-preprocessor">.getBytes</span>,<span class="hljs-string">"age"</span><span class="hljs-preprocessor">.getBytes</span>))
      println(<span class="hljs-string">"Row key:"</span>+key+<span class="hljs-string">"\tcf1.Name:"</span>+name+<span class="hljs-string">"\tcf1.Age:"</span>+age)
    }
    admin<span class="hljs-preprocessor">.close</span>()

    spark<span class="hljs-preprocessor">.stop</span>()
  }
}</code></pre>

<p>输出如下</p>



<pre class="prettyprint"><code class=" hljs css"><span class="hljs-tag">Row</span> <span class="hljs-tag">key</span><span class="hljs-pseudo">:1</span>   <span class="hljs-tag">cf1</span><span class="hljs-class">.Name</span><span class="hljs-pseudo">:Lucy</span>   <span class="hljs-tag">cf1</span><span class="hljs-class">.Age</span><span class="hljs-pseudo">:15</span>
<span class="hljs-tag">Row</span> <span class="hljs-tag">key</span><span class="hljs-pseudo">:2</span>   <span class="hljs-tag">cf1</span><span class="hljs-class">.Name</span><span class="hljs-pseudo">:jack</span>   <span class="hljs-tag">cf1</span><span class="hljs-class">.Age</span><span class="hljs-pseudo">:16</span>
<span class="hljs-tag">Row</span> <span class="hljs-tag">key</span><span class="hljs-pseudo">:3</span>   <span class="hljs-tag">cf1</span><span class="hljs-class">.Name</span><span class="hljs-pseudo">:Lily</span>   <span class="hljs-tag">cf1</span><span class="hljs-class">.Age</span><span class="hljs-pseudo">:14</span>
<span class="hljs-tag">Row</span> <span class="hljs-tag">key</span><span class="hljs-pseudo">:5</span>   <span class="hljs-tag">cf1</span><span class="hljs-class">.Name</span><span class="hljs-pseudo">:mike</span>   <span class="hljs-tag">cf1</span><span class="hljs-class">.Age</span><span class="hljs-pseudo">:17</span></code></pre>

<hr>



<h2 id="spark-dataframe-通过-phoenix-读写-hbase">Spark DataFrame 通过 Phoenix 读写 HBase</h2>

<p><em>友情提示</em>：<a href="https://blog.csdn.net/yitengtongweishi/article/details/82419926" rel="nofollow">JDBC方式 访问 Phoenix</a></p>

<p><a href="http://phoenix.apache.org/phoenix_spark.html" rel="nofollow">Apache Spark Plugin</a></p>

<p>部署Maven：<a href="https://blog.csdn.net/yitengtongweishi/article/details/81946562" rel="nofollow">https://blog.csdn.net/yitengtongweishi/article/details/81946562</a> <br>
需要添加的依赖如下：</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
   <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.phoenix<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
   <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>phoenix-core<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
   <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>${phoenix.version}<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.phoenix<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>phoenix-spark<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>${phoenix.version}<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span></code></pre>

<p>下面老规矩，直接上代码。</p>



<pre class="prettyprint"><code class=" hljs avrasm">import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.log</span>4j.{Level, Logger}
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.sql</span>.{SaveMode, SparkSession}

<span class="hljs-comment">/**
  * Created by blockchain on 18-9-9 下午8:33 in Beijing.
  */</span>

object SparkHBaseDataFrame {
  def main(args: Array[String]) {
    // 屏蔽不必要的日志显示在终端上
    Logger<span class="hljs-preprocessor">.getLogger</span>(<span class="hljs-string">"org.apache.spark"</span>)<span class="hljs-preprocessor">.setLevel</span>(Level<span class="hljs-preprocessor">.WARN</span>)

    val spark = SparkSession<span class="hljs-preprocessor">.builder</span>()<span class="hljs-preprocessor">.appName</span>(<span class="hljs-string">"SparkHBaseDataFrame"</span>)<span class="hljs-preprocessor">.getOrCreate</span>()

    val url = s<span class="hljs-string">"jdbc:phoenix:localhost:2181"</span>
    val dbtable = <span class="hljs-string">"PHOENIXTEST"</span>

    //spark 读取 phoenix 返回 DataFrame 的 第一种方式
    val rdf = spark<span class="hljs-preprocessor">.read</span>
      <span class="hljs-preprocessor">.format</span>(<span class="hljs-string">"jdbc"</span>)
      <span class="hljs-preprocessor">.option</span>(<span class="hljs-string">"driver"</span>, <span class="hljs-string">"org.apache.phoenix.jdbc.PhoenixDriver"</span>)
      <span class="hljs-preprocessor">.option</span>(<span class="hljs-string">"url"</span>, url)
      <span class="hljs-preprocessor">.option</span>(<span class="hljs-string">"dbtable"</span>, dbtable)
      <span class="hljs-preprocessor">.load</span>()
    rdf<span class="hljs-preprocessor">.printSchema</span>()

    //spark 读取 phoenix 返回 DataFrame 的 第二种方式
    val df = spark<span class="hljs-preprocessor">.read</span>
      <span class="hljs-preprocessor">.format</span>(<span class="hljs-string">"org.apache.phoenix.spark"</span>)
      <span class="hljs-preprocessor">.options</span>(Map(<span class="hljs-string">"table"</span> -&gt; dbtable, <span class="hljs-string">"zkUrl"</span> -&gt; url))
      <span class="hljs-preprocessor">.load</span>()
    df<span class="hljs-preprocessor">.printSchema</span>()

    //spark DataFrame 写入 phoenix，需要先建好表
    df<span class="hljs-preprocessor">.write</span>
      <span class="hljs-preprocessor">.format</span>(<span class="hljs-string">"org.apache.phoenix.spark"</span>)
      <span class="hljs-preprocessor">.mode</span>(SaveMode<span class="hljs-preprocessor">.Overwrite</span>)
      <span class="hljs-preprocessor">.options</span>(Map(<span class="hljs-string">"table"</span> -&gt; <span class="hljs-string">"PHOENIXTESTCOPY"</span>, <span class="hljs-string">"zkUrl"</span> -&gt; url))
      <span class="hljs-preprocessor">.save</span>()

    spark<span class="hljs-preprocessor">.stop</span>()
  }
}</code></pre>

<p>在 <a href="https://blog.csdn.net/yitengtongweishi/article/details/82190871" rel="nofollow">Phoenix</a> 中查看写入的数据</p>



<pre class="prettyprint"><code class=" hljs asciidoc"><span class="hljs-header">0: jdbc:phoenix:localhost:2181&gt; SELECT * FROM PHOENIXTEST ;
+-----+----------+</span>
<span class="hljs-header">| PK  |   COL1   |
+-----+----------+</span>
| 1   | Hello    |
| 2   | World    |
| 3   | HBase    |
<span class="hljs-header">| 4   | Phoenix  |
+-----+----------+</span>
4 rows selected (0.049 seconds)
0: jdbc:phoenix:localhost:2181&gt; 
<span class="hljs-header">0: jdbc:phoenix:localhost:2181&gt; SELECT * FROM PHOENIXTESTCOPY ;
+-----+----------+</span>
<span class="hljs-header">| PK  |   COL1   |
+-----+----------+</span>
| 1   | Hello    |
| 2   | World    |
| 3   | HBase    |
<span class="hljs-header">| 4   | Phoenix  |
+-----+----------+</span>
4 rows selected (0.03 seconds)
0: jdbc:phoenix:localhost:2181&gt; </code></pre>

<p>如上所示，写入成功。</p>

<p><a href="https://blog.csdn.net/yitengtongweishi/article/details/82556824" rel="nofollow">原文链接：转载请注明出处，谢谢！</a></p>

<hr>

<p>本文参考链接：</p>

<p><a href="https://www.jianshu.com/p/b2fea6687735" rel="nofollow">Spark与HBase的整合</a></p>

<p><a href="https://cloud.tencent.com/developer/article/1076249" rel="nofollow">Spark DataFrame写入HBase的常用方式</a></p>

<p><a href="https://blog.csdn.net/u013468917/article/details/52822074" rel="nofollow">spark将数据写入hbase以及从hbase读取数据</a></p>

<p><a href="https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-using-spark-query-hbase" rel="nofollow">Use Spark to read and write HBase data</a></p>

<p><a href="https://github.com/hortonworks-spark/shc" rel="nofollow">Apache Spark - Apache HBase Connector</a></p>

<p><a href="http://blog.cloudera.com/blog/2015/08/apache-spark-comes-to-apache-hbase-with-hbase-spark-module/" rel="nofollow">Apache Spark Comes to Apache HBase with HBase-Spark Module</a></p>

<p><a href="https://zh.hortonworks.com/blog/spark-hbase-dataframe-based-hbase-connector/" rel="nofollow">Spark-on-HBase: DataFrame based HBase connector</a></p>

<p><a href="http://wuchong.me/blog/2015/04/06/spark-on-hbase-new-api/" rel="nofollow">Spark 下操作 HBase（1.0.0 新 API）</a></p>

<p><a href="https://blog.csdn.net/UUfFO/article/details/79243644" rel="nofollow">Spark整合HBase（自定义HBase DataSource）</a></p>

<p><a href="https://blog.csdn.net/qq_32284189/article/details/79152982" rel="nofollow">spark通过Phoenix读取hbase数据</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>