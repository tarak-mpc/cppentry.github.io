---
layout:     post
title:      Spark算子：RDD行动Action操作(5)–saveAsTextFile、saveAsSequenceFile、saveAsObjectFile
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：如需转载请留言，谢谢，遇到问题可以留言					https://blog.csdn.net/qq_36764089/article/details/81276878				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h2><a href="http://lxw1234.com/archives/2015/07/402.htm" rel="nofollow">Spark算子：RDD行动Action操作(5)–saveAsTextFile、saveAsSequenceFile、saveAsObjectFile </a></h2>

<h1>关键字：Spark算子、Spark函数、Spark RDD行动Action、Spark RDD存储操作、saveAsTextFile、saveAsSequenceFile、saveAsObjectFile<br>
saveAsTextFile</h1>

<p>def saveAsTextFile(path: String): Unit</p>

<p>def saveAsTextFile(path: String, codec: Class[_ &lt;: CompressionCodec]): Unit</p>

<p>saveAsTextFile用于将RDD以文本文件的格式存储到文件系统中。</p>

<p>codec参数可以指定压缩的类名。</p>

<p> <strong>   var rdd1 = sc.makeRDD(1 to 10,2)</strong><br>
    scala&gt; <strong>rdd1.saveAsTextFile("hdfs://cdh5/tmp/lxw1234.com/") </strong>//保存到HDFS<br>
    hadoop fs -ls /tmp/lxw1234.com<br>
    Found 2 items<br>
    -rw-r--r--   2 lxw1234 supergroup        0 2015-07-10 09:15 /tmp/lxw1234.com/_SUCCESS<br>
    -rw-r--r--   2 lxw1234 supergroup        21 2015-07-10 09:15 /tmp/lxw1234.com/part-00000<br>
     <br>
  <strong>  hadoop fs -cat /tmp/lxw1234.com/part-00000</strong><br>
    1<br>
    2<br>
    3<br>
    4<br>
    5<br>
    6<br>
    7<br>
    8<br>
    9<br>
    10<br>
     </p>

<p>注意：如果使用rdd1.saveAsTextFile(“file:///tmp/lxw1234.com”)将文件保存到本地文件系统，那么只会保存在Executor所在机器的本地目录。</p>

<p>//指定压缩格式保存</p>

<p>    rdd1.saveAsTextFile("hdfs://cdh5/tmp/lxw1234.com/",classOf[com.hadoop.compression.lzo.LzopCodec])<br>
     <br>
   <strong> hadoop fs -ls /tmp/lxw1234.com</strong><br>
    -rw-r--r--   2 lxw1234 supergroup    0 2015-07-10 09:20 /tmp/lxw1234.com/_SUCCESS<br>
    -rw-r--r--   2 lxw1234 supergroup    71 2015-07-10 09:20 /tmp/lxw1234.com/part-00000.lzo<br>
     <br>
  <strong>  hadoop fs -text /tmp/lxw1234.com/part-00000.lzo</strong><br>
    1<br>
    2<br>
    3<br>
    4<br>
    5<br>
    6<br>
    7<br>
    8<br>
    9<br>
    10<br>
     <br>
     </p>

<h1>saveAsSequenceFile</h1>

<p>saveAsSequenceFile用于将RDD以SequenceFile的文件格式保存到HDFS上。</p>

<p>用法同saveAsTextFile。<br>
saveAsObjectFile</p>

<p>def saveAsObjectFile(path: String): Unit</p>

<p>saveAsObjectFile用于将RDD中的元素序列化成对象，存储到文件中。</p>

<p>对于HDFS，默认采用SequenceFile保存。</p>

<p>    var rdd1 = sc.makeRDD(1 to 10,2)<br>
    rdd1.saveAsObjectFile("hdfs://cdh5/tmp/lxw1234.com/")<br>
     <br>
    hadoop fs -cat /tmp/lxw1234.com/part-00000<br>
    SEQ !org.apache.hadoop.io.NullWritable"org.apache.hadoop.io.BytesWritableT</p>

<p> </p>            </div>
                </div>