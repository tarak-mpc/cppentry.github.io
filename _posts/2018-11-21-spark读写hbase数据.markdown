---
layout:     post
title:      spark读写hbase数据
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，欢迎转载，转载时请注明出处。欢迎留言讨论，大数据讨论qq群201011292					https://blog.csdn.net/Dante_003/article/details/79088900				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>如果原始数据在hbase中，这时想用spark对hbase数据做一些批量计算，就可以用spark的api直接读写hbase数据</p>



<h2 id="读取hbase数据">读取hbase数据</h2>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">import</span> org.apache.hadoop.hbase.HBaseConfiguration
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.mapreduce.TableInputFormat
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.util.Bytes
<span class="hljs-keyword">import</span> org.apache.spark._


object HbaseSparkRead {
  def main(args: Array[String]): Unit = {
    val sparkConf = <span class="hljs-keyword">new</span> SparkConf().setAppName(<span class="hljs-string">"HBaseTest"</span>).setMaster(<span class="hljs-string">"local"</span>)
    val sc = <span class="hljs-keyword">new</span> SparkContext(sparkConf)

    <span class="hljs-comment">//hbase信息</span>
    val conf = HBaseConfiguration.create()
    conf.set(<span class="hljs-string">"hbase.zookeeper.quorum"</span>, <span class="hljs-string">"ht05"</span>)
    conf.set(<span class="hljs-string">"hbase.zookeeper.property.clientPort"</span>, <span class="hljs-string">"2181"</span>)
    conf.set(TableInputFormat.INPUT_TABLE, <span class="hljs-string">"spark_hbase"</span>)


    <span class="hljs-comment">//读取数据并转化成rdd</span>
    val hBaseRDD = sc.newAPIHadoopRDD(conf, classOf[TableInputFormat],
      classOf[org.apache.hadoop.hbase.io.ImmutableBytesWritable],
      classOf[org.apache.hadoop.hbase.client.Result])

    <span class="hljs-comment">//val count = hBaseRDD.count()</span>
    <span class="hljs-comment">//println(count)</span>
    hBaseRDD.foreach { <span class="hljs-keyword">case</span> (_, result) =&gt; {
      <span class="hljs-comment">//获取行键</span>
      val key = Bytes.toString(result.getRow)
      <span class="hljs-comment">//通过列族和列名获取列</span>
      val name = Bytes.toString(result.getValue(<span class="hljs-string">"cf"</span>.getBytes, <span class="hljs-string">"name"</span>.getBytes))
      println(<span class="hljs-string">"Row key:"</span> + key + <span class="hljs-string">" Name:"</span> + name)
    }
    }

    <span class="hljs-comment">//将hbase数据保存到txt</span>
    hBaseRDD.map(x =&gt; Bytes.toString(x._2.getRow)).saveAsTextFile(<span class="hljs-string">"hdfs://ht05:9000/test1"</span>)
  }
}</code></pre>

<h2 id="写入hbase数据">写入hbase数据</h2>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">import</span> org.apache.hadoop.hbase.client.{Put, Result}
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.io.ImmutableBytesWritable
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.mapreduce.TableOutputFormat
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.util.Bytes
<span class="hljs-keyword">import</span> org.apache.hadoop.mapreduce.Job
<span class="hljs-keyword">import</span> org.apache.spark._

object HbaseSparkWrite {
  def main(args: Array[String]): Unit = {
    val sparkConf = <span class="hljs-keyword">new</span> SparkConf().setAppName(<span class="hljs-string">"HBaseTest"</span>).setMaster(<span class="hljs-string">"local"</span>)
    val sc = <span class="hljs-keyword">new</span> SparkContext(sparkConf)

    <span class="hljs-comment">//从txt写入到hbase</span>
    val dataRdd = sc.textFile(<span class="hljs-string">"hdfs://ht05:9000//zhaow/hotle0.txt"</span>)

    <span class="hljs-comment">//hbase信息</span>
    sc.hadoopConfiguration.set(<span class="hljs-string">"hbase.zookeeper.quorum"</span>, <span class="hljs-string">"ht05"</span>)
    sc.hadoopConfiguration.set(<span class="hljs-string">"hbase.zookeeper.property.clientPort"</span>, <span class="hljs-string">"2181"</span>)
    sc.hadoopConfiguration.set(TableOutputFormat.OUTPUT_TABLE, <span class="hljs-string">"spark_test0"</span>)

    <span class="hljs-comment">//lazy，延迟加载，如果程序在spark-shell里面运行必须使用延迟加载，因为spark-shell里面每一部都会打印结果</span>
    lazy val job = <span class="hljs-keyword">new</span> Job(sc.hadoopConfiguration)
    job.setOutputFormatClass(classOf[TableOutputFormat[ImmutableBytesWritable]])
    job.setOutputKeyClass(classOf[ImmutableBytesWritable])
    job.setOutputValueClass(classOf[Result])

    val rdd = dataRdd.filter(_.length &gt; <span class="hljs-number">0</span>).map { line =&gt; {
      val rowkey: String = line
      val put = <span class="hljs-keyword">new</span> Put(Bytes.toBytes(rowkey))
      put.add(Bytes.toBytes(<span class="hljs-string">"cf"</span>), Bytes.toBytes(<span class="hljs-string">"name"</span>), Bytes.toBytes(rowkey))
      (<span class="hljs-keyword">new</span> ImmutableBytesWritable, put)
    }
    }
    rdd.saveAsNewAPIHadoopDataset(job.getConfiguration)
  }
}</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>