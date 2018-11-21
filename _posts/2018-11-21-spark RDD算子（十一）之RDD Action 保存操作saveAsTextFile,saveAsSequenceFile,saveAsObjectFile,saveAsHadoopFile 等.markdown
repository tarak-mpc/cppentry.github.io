---
layout:     post
title:      spark RDD算子（十一）之RDD Action 保存操作saveAsTextFile,saveAsSequenceFile,saveAsObjectFile,saveAsHadoopFile 等
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<hr>

<p>关键字:Spark算子、Spark函数、Spark RDD行动Action、Spark RDD存储操作、saveAsTextFile、saveAsSequenceFile、saveAsObjectFile,saveAsHadoopFile、saveAsHadoopDataset,saveAsNewAPIHadoopFile、saveAsNewAPIHadoopDataset </p>

<hr>



<h1 id="saveastextfile"><strong>saveAsTextFile</strong></h1>

<p>def saveAsTextFile(path: String): Unit</p>

<p>def saveAsTextFile(path: String, codec: Class[_ &lt;: CompressionCodec]): Unit</p>

<p>saveAsTextFile用于将RDD以文本文件的格式存储到文件系统中。</p>

<p>codec参数可以指定压缩的类名。</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">var</span> rdd1 = sc.makeRDD(<span class="hljs-number">1</span> to <span class="hljs-number">10</span>,<span class="hljs-number">2</span>)
scala&gt; rdd1.saveAsTextFile(<span class="hljs-string">"hdfs://cdh5/tmp/lxw1234.com/"</span>) <span class="hljs-comment">//保存到HDFS</span>
hadoop fs -ls /tmp/lxw1234.com
Found <span class="hljs-number">2</span> items
-rw-r--r--   <span class="hljs-number">2</span> lxw1234 supergroup        <span class="hljs-number">0</span> <span class="hljs-number">2015</span>-<span class="hljs-number">07</span>-<span class="hljs-number">10</span> <span class="hljs-number">09</span>:<span class="hljs-number">15</span> /tmp/lxw1234.com/_SUCCESS
-rw-r--r--   <span class="hljs-number">2</span> lxw1234 supergroup        <span class="hljs-number">21</span> <span class="hljs-number">2015</span>-<span class="hljs-number">07</span>-<span class="hljs-number">10</span> <span class="hljs-number">09</span>:<span class="hljs-number">15</span> /tmp/lxw1234.com/part-<span class="hljs-number">00000</span>

hadoop fs -cat /tmp/lxw1234.com/part-<span class="hljs-number">00000</span></code></pre>

<p>注意：如果使用rdd1.saveAsTextFile(“file:///tmp/lxw1234.com”)将文件保存到本地文件系统，那么只会保存在Executor所在机器的本地目录。 <br>
<strong>指定压缩格式保存</strong>  </p>



<pre class="prettyprint"><code class="language-scala hljs ">rdd1.saveAsTextFile(<span class="hljs-string">"hdfs://cdh5/tmp/lxw1234.com/"</span>,classOf[com.hadoop.compression.lzo.LzopCodec])

hadoop fs -ls /tmp/lxw1234.com
-rw-r--r--   <span class="hljs-number">2</span> lxw1234 supergroup    <span class="hljs-number">0</span> <span class="hljs-number">2015</span>-<span class="hljs-number">07</span>-<span class="hljs-number">10</span> <span class="hljs-number">09</span>:<span class="hljs-number">20</span> /tmp/lxw1234.com/_SUCCESS
-rw-r--r--   <span class="hljs-number">2</span> lxw1234 supergroup    <span class="hljs-number">71</span> <span class="hljs-number">2015</span>-<span class="hljs-number">07</span>-<span class="hljs-number">10</span> <span class="hljs-number">09</span>:<span class="hljs-number">20</span> /tmp/lxw1234.com/part-<span class="hljs-number">00000.</span>lzo

hadoop fs -text /tmp/lxw1234.com/part-<span class="hljs-number">00000.</span>lzo</code></pre>



<h1 id="saveassequencefile"><strong>saveAsSequenceFile</strong></h1>

<p>saveAsSequenceFile用于将RDD以SequenceFile的文件格式保存到HDFS上。</p>

<p>用法同saveAsTextFile。</p>

<h1 id="saveasobjectfile"><strong>saveAsObjectFile</strong></h1>

<p>def saveAsObjectFile(path: String): Unit</p>

<p>saveAsObjectFile用于将RDD中的元素序列化成对象，存储到文件中。</p>

<p>对于HDFS，默认采用SequenceFile保存。  </p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">var</span> rdd1 = sc.makeRDD(<span class="hljs-number">1</span> to <span class="hljs-number">10</span>,<span class="hljs-number">2</span>)
rdd1.saveAsObjectFile(<span class="hljs-string">"hdfs://cdh5/tmp/lxw1234.com/"</span>)

hadoop fs -cat /tmp/lxw1234.com/part-<span class="hljs-number">00000</span>
SEQ !org.apache.hadoop.io.NullWritable<span class="hljs-string">"org.apache.hadoop.io.BytesWritableT</span></code></pre>



<h1 id="saveashadoopfile"><strong>saveAsHadoopFile</strong></h1>

<p>def saveAsHadoopFile(path: String, keyClass: Class[<em>], valueClass: Class[</em>], outputFormatClass: Class[_ &lt;: OutputFormat[<em>, </em>]], codec: Class[_ &lt;: CompressionCodec]): Unit</p>

<p>def saveAsHadoopFile(path: String, keyClass: Class[<em>], valueClass: Class[</em>], outputFormatClass: Class[_ &lt;: OutputFormat[<em>, </em>]], conf: JobConf = …, codec: Option[Class[_ &lt;: CompressionCodec]] = None): Unit</p>

<p>saveAsHadoopFile是将RDD存储在HDFS上的文件中，支持老版本Hadoop API。</p>

<p>可以指定outputKeyClass、outputValueClass以及压缩格式。</p>

<p>每个分区输出一个文件。</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">var</span> rdd1 = sc.makeRDD(Array((<span class="hljs-string">"A"</span>,<span class="hljs-number">2</span>),(<span class="hljs-string">"A"</span>,<span class="hljs-number">1</span>),(<span class="hljs-string">"B"</span>,<span class="hljs-number">6</span>),(<span class="hljs-string">"B"</span>,<span class="hljs-number">3</span>),(<span class="hljs-string">"B"</span>,<span class="hljs-number">7</span>)))

<span class="hljs-keyword">import</span> org.apache.hadoop.mapred.TextOutputFormat
<span class="hljs-keyword">import</span> org.apache.hadoop.io.Text
<span class="hljs-keyword">import</span> org.apache.hadoop.io.IntWritable

rdd1.saveAsHadoopFile(<span class="hljs-string">"/tmp/lxw1234.com/"</span>,classOf[Text],classOf[IntWritable],classOf[TextOutputFormat[Text,IntWritable]])

rdd1.saveAsHadoopFile(<span class="hljs-string">"/tmp/lxw1234.com/"</span>,classOf[Text],classOf[IntWritable],classOf[TextOutputFormat[Text,IntWritable]],
                      classOf[com.hadoop.compression.lzo.LzopCodec])</code></pre>



<h1 id="saveashadoopdataset"><strong>saveAsHadoopDataset</strong></h1>

<p>def saveAsHadoopDataset(conf: JobConf): Unit</p>

<p>saveAsHadoopDataset用于将RDD保存到除了HDFS的其他存储中，比如HBase。</p>

<p>在JobConf中，通常需要关注或者设置五个参数：</p>

<p>文件的保存路径、key值的class类型、value值的class类型、RDD的输出格式(OutputFormat)、以及压缩相关的参数。 <br>
<strong>##使用saveAsHadoopDataset将RDD保存到HDFS中</strong>  </p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">import</span> org.apache.spark.SparkConf
<span class="hljs-keyword">import</span> org.apache.spark.SparkContext
<span class="hljs-keyword">import</span> SparkContext._
<span class="hljs-keyword">import</span> org.apache.hadoop.mapred.TextOutputFormat
<span class="hljs-keyword">import</span> org.apache.hadoop.io.Text
<span class="hljs-keyword">import</span> org.apache.hadoop.io.IntWritable
<span class="hljs-keyword">import</span> org.apache.hadoop.mapred.JobConf



<span class="hljs-keyword">var</span> rdd1 = sc.makeRDD(Array((<span class="hljs-string">"A"</span>,<span class="hljs-number">2</span>),(<span class="hljs-string">"A"</span>,<span class="hljs-number">1</span>),(<span class="hljs-string">"B"</span>,<span class="hljs-number">6</span>),(<span class="hljs-string">"B"</span>,<span class="hljs-number">3</span>),(<span class="hljs-string">"B"</span>,<span class="hljs-number">7</span>)))
<span class="hljs-keyword">var</span> jobConf = <span class="hljs-keyword">new</span> JobConf()
jobConf.setOutputFormat(classOf[TextOutputFormat[Text,IntWritable]])
jobConf.setOutputKeyClass(classOf[Text])
jobConf.setOutputValueClass(classOf[IntWritable])
jobConf.set(<span class="hljs-string">"mapred.output.dir"</span>,<span class="hljs-string">"/tmp/lxw1234/"</span>)
rdd1.saveAsHadoopDataset(jobConf)

结果：
hadoop fs -cat /tmp/lxw1234/part-<span class="hljs-number">00000</span>
A       <span class="hljs-number">2</span>
A       <span class="hljs-number">1</span>
hadoop fs -cat /tmp/lxw1234/part-<span class="hljs-number">00001</span>
B       <span class="hljs-number">6</span>
B       <span class="hljs-number">3</span>
B       <span class="hljs-number">7</span></code></pre>

<p><strong>##保存数据到HBASE</strong> <br>
HBase建表：</p>

<p>create ‘lxw1234′,{NAME =&gt; ‘f1′,VERSIONS =&gt; 1},{NAME =&gt; ‘f2′,VERSIONS =&gt; 1},{NAME =&gt; ‘f3′,VERSIONS =&gt; 1}  </p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">import</span> org.apache.spark.SparkConf
<span class="hljs-keyword">import</span> org.apache.spark.SparkContext
<span class="hljs-keyword">import</span> SparkContext._
<span class="hljs-keyword">import</span> org.apache.hadoop.mapred.TextOutputFormat
<span class="hljs-keyword">import</span> org.apache.hadoop.io.Text
<span class="hljs-keyword">import</span> org.apache.hadoop.io.IntWritable
<span class="hljs-keyword">import</span> org.apache.hadoop.mapred.JobConf
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.HBaseConfiguration
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.mapred.TableOutputFormat
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.client.Put
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.util.Bytes
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.io.ImmutableBytesWritable

<span class="hljs-keyword">var</span> conf = HBaseConfiguration.create()
    <span class="hljs-keyword">var</span> jobConf = <span class="hljs-keyword">new</span> JobConf(conf)
    jobConf.set(<span class="hljs-string">"hbase.zookeeper.quorum"</span>,<span class="hljs-string">"zkNode1,zkNode2,zkNode3"</span>)
    jobConf.set(<span class="hljs-string">"zookeeper.znode.parent"</span>,<span class="hljs-string">"/hbase"</span>)
    jobConf.set(TableOutputFormat.OUTPUT_TABLE,<span class="hljs-string">"lxw1234"</span>)
    jobConf.setOutputFormat(classOf[TableOutputFormat])

    <span class="hljs-keyword">var</span> rdd1 = sc.makeRDD(Array((<span class="hljs-string">"A"</span>,<span class="hljs-number">2</span>),(<span class="hljs-string">"B"</span>,<span class="hljs-number">6</span>),(<span class="hljs-string">"C"</span>,<span class="hljs-number">7</span>)))
    rdd1.map(x =&gt; 
      {
        <span class="hljs-keyword">var</span> put = <span class="hljs-keyword">new</span> Put(Bytes.toBytes(x._1))
        put.add(Bytes.toBytes(<span class="hljs-string">"f1"</span>), Bytes.toBytes(<span class="hljs-string">"c1"</span>), Bytes.toBytes(x._2))
        (<span class="hljs-keyword">new</span> ImmutableBytesWritable,put)
      }
    ).saveAsHadoopDataset(jobConf)

##结果：
hbase(main):<span class="hljs-number">005</span>:<span class="hljs-number">0</span>&gt; scan <span class="hljs-string">'lxw1234'</span>
ROW     COLUMN+CELL                                                                                                
 A       column=f1:c1, timestamp=<span class="hljs-number">1436504941187</span>, value=\x00\x00\x00\x02                                              
 B       column=f1:c1, timestamp=<span class="hljs-number">1436504941187</span>, value=\x00\x00\x00\x06                                              
 C       column=f1:c1, timestamp=<span class="hljs-number">1436504941187</span>, value=\x00\x00\x00\x07                                              
<span class="hljs-number">3</span> row(s) in <span class="hljs-number">0.0550</span> seconds</code></pre>

<p>注意：保存到HBase，运行时候需要在SPARK_CLASSPATH中加入HBase相关的jar包。</p>

<p>可参考：<a href="http://lxw1234.com/archives/2015/07/332.htm" rel="nofollow">http://lxw1234.com/archives/2015/07/332.htm</a>  </p>



<h1 id="saveasnewapihadoopfile"><strong>saveAsNewAPIHadoopFile</strong></h1>

<p>def saveAsNewAPIHadoopFile[F &lt;: OutputFormat[K, V]](path: String)(implicit fm: ClassTag[F]): Unit</p>

<p>def saveAsNewAPIHadoopFile(path: String, keyClass: Class[<em>], valueClass: Class[</em>], outputFormatClass: Class[_ &lt;: OutputFormat[<em>, </em>]], conf: Configuration = self.context.hadoopConfiguration): Unit</p>

<p>saveAsNewAPIHadoopFile用于将RDD数据保存到HDFS上，使用新版本Hadoop API。</p>

<p>用法基本同saveAsHadoopFile。</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">import</span> org.apache.spark.SparkConf
<span class="hljs-keyword">import</span> org.apache.spark.SparkContext
<span class="hljs-keyword">import</span> SparkContext._
<span class="hljs-keyword">import</span> org.apache.hadoop.mapreduce.lib.output.TextOutputFormat
<span class="hljs-keyword">import</span> org.apache.hadoop.io.Text
<span class="hljs-keyword">import</span> org.apache.hadoop.io.IntWritable

<span class="hljs-keyword">var</span> rdd1 = sc.makeRDD(Array((<span class="hljs-string">"A"</span>,<span class="hljs-number">2</span>),(<span class="hljs-string">"A"</span>,<span class="hljs-number">1</span>),(<span class="hljs-string">"B"</span>,<span class="hljs-number">6</span>),(<span class="hljs-string">"B"</span>,<span class="hljs-number">3</span>),(<span class="hljs-string">"B"</span>,<span class="hljs-number">7</span>)))
rdd1.saveAsNewAPIHadoopFile(<span class="hljs-string">"/tmp/lxw1234/"</span>,classOf[Text],classOf[IntWritable],classOf[TextOutputFormat[Text,IntWritable]])</code></pre>



<h1 id="saveasnewapihadoopdataset"><strong>saveAsNewAPIHadoopDataset</strong></h1>

<p>def saveAsNewAPIHadoopDataset(conf: Configuration): Unit</p>

<p>作用同saveAsHadoopDataset,只不过采用新版本Hadoop API。</p>

<p>以写入HBase为例：</p>

<p>HBase建表：</p>

<p>create ‘lxw1234′,{NAME =&gt; ‘f1′,VERSIONS =&gt; 1},{NAME =&gt; ‘f2′,VERSIONS =&gt; 1},{NAME =&gt; ‘f3′,VERSIONS =&gt; 1}</p>

<p>完整的Spark应用程序：  </p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">package</span> com.lxw1234.test

<span class="hljs-keyword">import</span> org.apache.spark.SparkConf
<span class="hljs-keyword">import</span> org.apache.spark.SparkContext
<span class="hljs-keyword">import</span> SparkContext._
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.HBaseConfiguration
<span class="hljs-keyword">import</span> org.apache.hadoop.mapreduce.Job
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.mapreduce.TableOutputFormat
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.io.ImmutableBytesWritable
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.client.Result
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.util.Bytes
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.client.Put

<span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">Test</span> {</span>
  <span class="hljs-keyword">def</span> main(args : Array[String]) {
   <span class="hljs-keyword">val</span> sparkConf = <span class="hljs-keyword">new</span> SparkConf().setMaster(<span class="hljs-string">"spark://lxw1234.com:7077"</span>).setAppName(<span class="hljs-string">"lxw1234.com"</span>)
   <span class="hljs-keyword">val</span> sc = <span class="hljs-keyword">new</span> SparkContext(sparkConf);
   <span class="hljs-keyword">var</span> rdd1 = sc.makeRDD(Array((<span class="hljs-string">"A"</span>,<span class="hljs-number">2</span>),(<span class="hljs-string">"B"</span>,<span class="hljs-number">6</span>),(<span class="hljs-string">"C"</span>,<span class="hljs-number">7</span>)))

    sc.hadoopConfiguration.set(<span class="hljs-string">"hbase.zookeeper.quorum "</span>,<span class="hljs-string">"zkNode1,zkNode2,zkNode3"</span>)
    sc.hadoopConfiguration.set(<span class="hljs-string">"zookeeper.znode.parent"</span>,<span class="hljs-string">"/hbase"</span>)
    sc.hadoopConfiguration.set(TableOutputFormat.OUTPUT_TABLE,<span class="hljs-string">"lxw1234"</span>)
    <span class="hljs-keyword">var</span> job = <span class="hljs-keyword">new</span> Job(sc.hadoopConfiguration)
    job.setOutputKeyClass(classOf[ImmutableBytesWritable])
    job.setOutputValueClass(classOf[Result])
    job.setOutputFormatClass(classOf[TableOutputFormat[ImmutableBytesWritable]])

    rdd1.map(
      x =&gt; {
        <span class="hljs-keyword">var</span> put = <span class="hljs-keyword">new</span> Put(Bytes.toBytes(x._1))
        put.add(Bytes.toBytes(<span class="hljs-string">"f1"</span>), Bytes.toBytes(<span class="hljs-string">"c1"</span>), Bytes.toBytes(x._2))
        (<span class="hljs-keyword">new</span> ImmutableBytesWritable,put)
      }    
    ).saveAsNewAPIHadoopDataset(job.getConfiguration)

    sc.stop()   
  }
}
</code></pre>

<p>注意：保存到HBase，运行时候需要在SPARK_CLASSPATH中加入HBase相关的jar包。</p>

<p>可参考：<a href="http://lxw1234.com/archives/2015/07/332.htm" rel="nofollow">http://lxw1234.com/archives/2015/07/332.htm</a>  </p>

<p>感谢原作者的总结 <br>
本文转自: lxw的大数据田地 <br>
<a href="http://lxw1234.com/archives/2015/07/402.htm" rel="nofollow">http://lxw1234.com/archives/2015/07/402.htm</a> <br>
<a href="http://lxw1234.com/archives/2015/07/404.htm" rel="nofollow">http://lxw1234.com/archives/2015/07/404.htm</a> <br>
<a href="http://lxw1234.com/archives/2015/07/406.htm" rel="nofollow">http://lxw1234.com/archives/2015/07/406.htm</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>