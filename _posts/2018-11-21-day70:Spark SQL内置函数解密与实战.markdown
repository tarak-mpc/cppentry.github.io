---
layout:     post
title:      day70:Spark SQL内置函数解密与实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>Spark on hive:</p>
<p>将hive的元数据当作一个数据源</p>
<p>Hive on spark:（未来一个趋势）</p>
<p>Hive 本来的计算框架是mr，换成spark 来计算</p>
<p>目前企业及大数据spark开发的时候绝大多数情况下采用hive 作为数据仓库的；spark提供了Hive的支持功能，spark通过hiveContext 可以直接操作hive 的数据；</p>
<p>基于HiveContextk可以使用sql/hql两种方式编写sql来对hive进行操作，包括表增删改查，往表里导入数据以及用sql语法</p>
<p>实现sparkSQL 查询</p>
<p></p><pre><code class="language-java">package cn.db.scala.sparksql.day70

import org.apache.spark.sql.Row
import org.apache.spark.sql.hive.HiveContext
import org.apache.spark.sql.types.{StringType, StructField, StructType}
import org.apache.spark.{SparkContext, SparkConf}
import org.apache.spark.sql.functions._


/**
 * Created by ptbx on 2016/5/30.
 */
/**
 * Created with IntelliJ IDEA.
 * User: ptbx
 * Date: 2016/5/30
 * Time: 23:35
 * To change this template use File | Settings | File Templates.
  spark 1.5 以后提供了大量的内置函数：
   def agg(aggExpr: (String, String), aggExprs: (String, String)*): DataFrame = {
    groupBy().agg(aggExpr, aggExprs : _*)
 还有max，mean、min sum、avg、explode、size、sort、array、day、
 to_date、abs、acros
 1、聚合函数 例如 countDistinct 去重级别  sumDistinct
 2、集合函数 例如： sort_array explode 等
 3、日期、时间函数 ：
  }
 */
object SparkSQLAvg {
  def main (args: Array[String]) {
   val conf = new SparkConf()
   conf.setAppName("sparksql").setMaster("local")
   val sc = new SparkContext(conf)
   val sqlContext = new HiveContext(sc)
   //要使用spark SQL 的内置函数就一定要导入的SQLContext 的隐式转换
   import sqlContext.implicits._
   var userDatas = Array(
   "2016-06-01, 001,http://spark.apache.org/",
   "2016-06-02, 002,http://hadoop.apache.org/",
   "2016-06-08, 003,http://flume.apache.org/",
   "2016-06-06, 004,http://kafka.apache.org/",
   "2016-06-07, 005,http://hive.apache.org/",
   "2016-06-10, 006,http://hbase.apache.org/",
   "2016-06-11, 007,http://parquet.apache.org/"
   )

   val userDataRDD = sc.parallelize(userDatas)
   /**
    * 4、根据业务需要将对数据进行预处理生成DAtaFrame，要想把RDD转成DataFrame，需要把RDD中的元素类型变成Row类型
    * 同时要提供DataFrame中的Columns的元数据信息描述
    */
  val userRDDRow =  userDataRDD.map( row =&gt; {val splited = row.split(","); Row(splited(0), splited(1), splited(2))})
   val structType = StructType(Array(
      StructField("time",StringType, true),
      StructField("id",StringType, true),
      StructField("url",StringType, true)
   ))
  val userDataDF =sqlContext.createDataFrame(userRDDRow, structType)

   /**
    * 5、根据sparkSql提供的内置函数对DataFrame进行操作，
    */
   userDataDF.groupBy("time").agg('time,countDistinct('id)).show
  }
}
</code></pre><br><br>            </div>
                </div>