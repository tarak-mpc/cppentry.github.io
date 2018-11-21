---
layout:     post
title:      Spark学习笔记：Spark Streaming与Spark SQL协同工作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1>Spark Streaming与Spark SQL协同工作</h1>

<p>Spark Streaming可以和Spark Core，Spark SQL整合在一起使用，这也是它最强大的一个地方。</p>

<h2>实例：实时统计搜索次数大于3次的搜索词</h2>

<pre class="has">
<code class="language-java">package StreamingDemo

import org.apache.log4j.{Level, Logger}
import org.apache.spark.SparkConf
import org.apache.spark.sql.types.{IntegerType, StringType, StructField, StructType}
import org.apache.spark.sql.{Row, SparkSession}
import org.apache.spark.streaming.{Seconds, StreamingContext}

/**
  * Spark Streaming与Spark相结合
  * 需求：实时统计搜索次数大于3次的搜索词
  */
object StreamingAndSQLDemo {
  def main(args: Array[String]): Unit = {
    Logger.getLogger("org").setLevel(Level.WARN)
    System.setProperty("HADOOP_USER_NAME", "Setsuna")
    val conf = new SparkConf()
      .setAppName(this.getClass.getSimpleName)
      .setMaster("local[2]")
    val ssc = new StreamingContext(conf, Seconds(2))

    //开启checkpoint
    ssc.checkpoint("hdfs://Hadoop01:9000/checkpoint")

    //分词，wordcount，记录状态
    val resultDStream =
      ssc
        .socketTextStream("Hadoop01", 6666)
        .flatMap(_.split(" "))
        .map((_, 1))
        .updateStateByKey((values: Seq[Int], state: Option[Int]) =&gt; {
          var count = state.getOrElse(0)
          for (value &lt;- values) {
            count += value
          }

          Option(count)
        })

    //将wordcount结果存进表里
    resultDStream.foreachRDD(rdd=&gt;{
      //创建SparkSession对象
      val sparkSession=SparkSession.builder().getOrCreate()
      //创建Row类型的RDD
      val rowRDD=rdd.map(x=&gt;Row(x._1,x._2))
      //创建schema
      val schema=StructType(List(
        StructField("word",StringType,true),
        StructField("count",IntegerType,true)
      ))
      //创建DataFrame，并注册临时视图
      sparkSession.createDataFrame(rowRDD,schema).
        createOrReplaceTempView("wordcount")
      //进行查询并在Console里输出
      sparkSession.sql("select * from wordcount where count&gt;3").show()
    })
    ssc.start()
    ssc.awaitTermination()
  }
}
</code></pre>

<p>测试<br>
在nc里输入数据<br><img alt="" class="has" height="85" src="https://img-blog.csdn.net/20180919173043156?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xyeGNtd3ky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="395"><br>
Console里的输出<br><img alt="" class="has" height="447" src="https://img-blog.csdn.net/20180919173132793?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xyeGNtd3ky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1000"></p>            </div>
                </div>