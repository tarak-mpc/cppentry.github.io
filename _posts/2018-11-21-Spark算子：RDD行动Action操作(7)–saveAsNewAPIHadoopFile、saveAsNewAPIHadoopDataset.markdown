---
layout:     post
title:      Spark算子：RDD行动Action操作(7)–saveAsNewAPIHadoopFile、saveAsNewAPIHadoopDataset
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：如需转载请留言，谢谢，遇到问题可以留言					https://blog.csdn.net/qq_36764089/article/details/81276931				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h2><a href="http://lxw1234.com/archives/2015/07/406.htm" rel="nofollow">Spark算子：RDD行动Action操作(7)–saveAsNewAPIHadoopFile、saveAsNewAPIHadoopDataset </a></h2>

<p>关键字：Spark算子、Spark函数、Spark RDD行动Action、Spark RDD存储操作、saveAsNewAPIHadoopFile、saveAsNewAPIHadoopDataset</p>

<h1><br>
saveAsNewAPIHadoopFile</h1>

<p>def saveAsNewAPIHadoopFile[F &lt;: OutputFormat[K, V]](path: String)(implicit fm: ClassTag[F]): Unit</p>

<p>def saveAsNewAPIHadoopFile(path: String, keyClass: Class[_], valueClass: Class[_], outputFormatClass: Class[_ &lt;: OutputFormat[_, _]], conf: Configuration = self.context.hadoopConfiguration): Unit</p>

<p> </p>

<p>saveAsNewAPIHadoopFile用于将RDD数据保存到HDFS上，使用新版本Hadoop API。</p>

<p>用法基本同saveAsHadoopFile。</p>

<p><strong>    import org.apache.spark.SparkConf<br>
    import org.apache.spark.SparkContext<br>
    import SparkContext._<br>
    import org.apache.hadoop.mapreduce.lib.output.TextOutputFormat<br>
    import org.apache.hadoop.io.Text<br>
    import org.apache.hadoop.io.IntWritable<br>
     <br>
    var rdd1 = sc.makeRDD(Array(("A",2),("A",1),("B",6),("B",3),("B",7)))<br>
rdd1.saveAsNewAPIHadoopFile("/tmp/lxw1234/",classOf[Text],classOf[IntWritable],classOf[TextOutputFormat[Text,IntWritable]])<br>
     </strong></p>

<h1><strong>saveAsNewAPIHadoopDataset</strong></h1>

<p>def saveAsNewAPIHadoopDataset(conf: Configuration): Unit</p>

<p>作用同saveAsHadoopDataset,只不过采用新版本Hadoop API。</p>

<p>以写入HBase为例：</p>

<p> </p>

<p>HBase建表：</p>

<p><strong>create ‘lxw1234′,{NAME =&gt; ‘f1′,VERSIONS =&gt; 1},{NAME =&gt; ‘f2′,VERSIONS =&gt; 1},{NAME =&gt; ‘f3′,VERSIONS =&gt; 1}</strong></p>

<p> </p>

<p>完整的Spark应用程序：</p>

<p>    package com.lxw1234.test<br>
     <br>
    import org.apache.spark.SparkConf<br>
    import org.apache.spark.SparkContext<br>
    import SparkContext._<br>
    import org.apache.hadoop.hbase.HBaseConfiguration<br>
    import org.apache.hadoop.mapreduce.Job<br>
    import org.apache.hadoop.hbase.mapreduce.TableOutputFormat<br>
    import org.apache.hadoop.hbase.io.ImmutableBytesWritable<br>
    import org.apache.hadoop.hbase.client.Result<br>
    import org.apache.hadoop.hbase.util.Bytes<br>
    import org.apache.hadoop.hbase.client.Put<br>
     <br>
    object Test {<br>
      def main(args : Array[String]) {<br>
       val sparkConf = new SparkConf().setMaster("spark://lxw1234.com:7077").setAppName("lxw1234.com")<br>
       val sc = new SparkContext(sparkConf);<br>
       var rdd1 = sc.makeRDD(Array(("A",2),("B",6),("C",7)))<br>
       <br>
        sc.hadoopConfiguration.set("hbase.zookeeper.quorum ","zkNode1,zkNode2,zkNode3")<br>
        sc.hadoopConfiguration.set("zookeeper.znode.parent","/hbase")<br>
        sc.hadoopConfiguration.set(TableOutputFormat.OUTPUT_TABLE,"lxw1234")<br>
        var job = new Job(sc.hadoopConfiguration)<br>
        job.setOutputKeyClass(classOf[ImmutableBytesWritable])<br>
        job.setOutputValueClass(classOf[Result])<br>
        job.setOutputFormatClass(classOf[TableOutputFormat[ImmutableBytesWritable]])<br>
        <br>
        rdd1.map(<br>
          x =&gt; {<br>
            var put = new Put(Bytes.toBytes(x._1))<br>
            put.add(Bytes.toBytes("f1"), Bytes.toBytes("c1"), Bytes.toBytes(x._2))<br>
            (new ImmutableBytesWritable,put)<br>
          }    <br>
        ).saveAsNewAPIHadoopDataset(job.getConfiguration)<br>
        <br>
        sc.stop()   <br>
      }<br>
    }</p>            </div>
                </div>