---
layout:     post
title:      Spark算子：RDD行动Action操作(6)–saveAsHadoopFile、saveAsHadoopDataset
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：如需转载请留言，谢谢，遇到问题可以留言					https://blog.csdn.net/qq_36764089/article/details/81276903				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h2><a href="http://lxw1234.com/archives/2015/07/404.htm" rel="nofollow">Spark算子：RDD行动Action操作(6)–saveAsHadoopFile、saveAsHadoopDataset </a></h2>

<h1>关键字：Spark算子、Spark函数、Spark RDD行动Action、Spark RDD存储操作、saveAsHadoopFile、saveAsHadoopDataset</h1>

<h1><br>
saveAsHadoopFile</h1>

<p>def saveAsHadoopFile(path: String, keyClass: Class[_], valueClass: Class[_], outputFormatClass: Class[_ &lt;: OutputFormat[_, _]], codec: Class[_ &lt;: CompressionCodec]): Unit</p>

<p>def saveAsHadoopFile(path: String, keyClass: Class[_], valueClass: Class[_], outputFormatClass: Class[_ &lt;: OutputFormat[_, _]], conf: JobConf = …, codec: Option[Class[_ &lt;: CompressionCodec]] = None): Unit</p>

<p> </p>

<p>saveAsHadoopFile是将RDD存储在HDFS上的文件中，支持老版本Hadoop API。</p>

<p>可以指定outputKeyClass、outputValueClass以及压缩格式。</p>

<p>每个分区输出一个文件。</p>

<p><strong>    var rdd1 = sc.makeRDD(Array(("A",2),("A",1),("B",6),("B",3),("B",7)))</strong><br>
     <br>
   <strong> import org.apache.hadoop.mapred.TextOutputFormat<br>
    import org.apache.hadoop.io.Text<br>
    import org.apache.hadoop.io.IntWritable</strong><br>
     <br>
  rdd1.saveAsHadoopFile("/tmp/lxw1234.com/",classOf[Text],classOf[IntWritable],classOf[TextOutputFormat[Text,IntWritable]])<br>
     <br>
  rdd1.saveAsHadoopFile("/tmp/lxw1234.com/",classOf[Text],classOf[IntWritable],classOf[TextOutputFormat[Text,IntWritable]],<br>
                          classOf[com.hadoop.compression.lzo.LzopCodec])<br>
     </p>

<h1>saveAsHadoopDataset</h1>

<p>def saveAsHadoopDataset(conf: JobConf): Unit</p>

<p>saveAsHadoopDataset用于将RDD保存到除了HDFS的其他存储中，比如HBase。</p>

<p>在JobConf中，通常需要关注或者设置五个参数：</p>

<p>文件的保存路径、key值的class类型、value值的class类型、RDD的输出格式(OutputFormat)、以及压缩相关的参数。</p>

<p> </p>

<p>##使用saveAsHadoopDataset将RDD保存到HDFS中</p>

<p> <strong>   import org.apache.spark.SparkConf<br>
    import org.apache.spark.SparkContext<br>
    import SparkContext._<br>
    import org.apache.hadoop.mapred.TextOutputFormat<br>
    import org.apache.hadoop.io.Text<br>
    import org.apache.hadoop.io.IntWritable<br>
    import org.apache.hadoop.mapred.JobConf</strong><br>
     <br>
     <br>
     <br><strong>    var rdd1 = sc.makeRDD(Array(("A",2),("A",1),("B",6),("B",3),("B",7)))<br>
    var jobConf = new JobConf()<br>
    jobConf.setOutputFormat(classOf[TextOutputFormat[Text,IntWritable]])<br>
    jobConf.setOutputKeyClass(classOf[Text])<br>
    jobConf.setOutputValueClass(classOf[IntWritable])<br>
    jobConf.set("mapred.output.dir","/tmp/lxw1234/")<br>
    rdd1.saveAsHadoopDataset(jobConf)</strong><br>
     <br>
    结果：<br><strong>    hadoop fs -cat /tmp/lxw1234/part-00000</strong><br>
    A       2<br>
    A       1<br>
  <strong>  hadoop fs -cat /tmp/lxw1234/part-00001</strong><br>
    B       6<br>
    B       3<br>
    B       7<br>
     </p>

<p>##保存数据到HBASE</p>

<p>HBase建表：</p>

<p><strong>create ‘lxw1234′,{NAME =&gt; ‘f1′,VERSIONS =&gt; 1},{NAME =&gt; ‘f2′,VERSIONS =&gt; 1},{NAME =&gt; ‘f3′,VERSIONS =&gt; 1}</strong></p>

<p>    import org.apache.spark.SparkConf<br>
    import org.apache.spark.SparkContext<br>
    import SparkContext._<br>
    import org.apache.hadoop.mapred.TextOutputFormat<br>
    import org.apache.hadoop.io.Text<br>
    import org.apache.hadoop.io.IntWritable<br>
    import org.apache.hadoop.mapred.JobConf<br>
    import org.apache.hadoop.hbase.HBaseConfiguration<br>
    import org.apache.hadoop.hbase.mapred.TableOutputFormat<br>
    import org.apache.hadoop.hbase.client.Put<br>
    import org.apache.hadoop.hbase.util.Bytes<br>
    import org.apache.hadoop.hbase.io.ImmutableBytesWritable<br>
     <br>
    var conf = HBaseConfiguration.create()<br>
        var jobConf = new JobConf(conf)<br>
        jobConf.set("hbase.zookeeper.quorum","zkNode1,zkNode2,zkNode3")<br>
        jobConf.set("zookeeper.znode.parent","/hbase")<br>
        jobConf.set(TableOutputFormat.OUTPUT_TABLE,"lxw1234")<br>
        jobConf.setOutputFormat(classOf[TableOutputFormat])<br>
        <br>
        var rdd1 = sc.makeRDD(Array(("A",2),("B",6),("C",7)))<br>
        rdd1.map(x =&gt;<br>
          {<br>
            var put = new Put(Bytes.toBytes(x._1))<br>
            put.add(Bytes.toBytes("f1"), Bytes.toBytes("c1"), Bytes.toBytes(x._2))<br>
            (new ImmutableBytesWritable,put)<br>
          }<br>
        ).saveAsHadoopDataset(jobConf)<br>
     <br>
    ##结果：<br>
    hbase(main):005:0&gt; scan 'lxw1234'<br>
    ROW     COLUMN+CELL                                                                                                <br>
     A       column=f1:c1, timestamp=1436504941187, value=\x00\x00\x00\x02                                              <br>
     B       column=f1:c1, timestamp=1436504941187, value=\x00\x00\x00\x06                                              <br>
     C       column=f1:c1, timestamp=1436504941187, value=\x00\x00\x00\x07                                              <br>
    3 row(s) in 0.0550 seconds<br>
     </p>

<p>注意：保存到HBase，运行时候需要在SPARK_CLASSPATH中加入HBase相关的jar包。</p>            </div>
                </div>