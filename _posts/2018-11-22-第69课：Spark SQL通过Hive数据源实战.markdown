---
layout:     post
title:      第69课：Spark SQL通过Hive数据源实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/someby/article/details/83900054				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h3>内容：</h3>

<p>    1.Spark SQL操作Hive解析<br>
    2.SparkSQL操作Hive实战</p>

<h3>一、Spark SQL操作Hive解析</h3>

<p>    1.在目前企业级大数据Spark开发的时候，绝大多数是采用Hive作为数据仓库的spark提供Hive的支持功能，Spark通过HiveContex可以操作Hive中的数据，基于HiveContext可以使用sql/hsql两种方式编写SQL语句对Hive进行操作，包括创建表、从删除表、往表里导入数据构造各种SQL语法构造各种sql语句对表中的数据进行操作；<br>
    2.也可以直接通过saveAsTable的方式把DataFrame中的数据保存到Hive数据仓库中<br>
    3.可以直接通过HiveContext.table方法直接加载Hive中的表来生成dataframe</p>

<h3>二、SparkSQL操作Hive实战</h3>

<p>数据score.txt</p>

<pre class="has">
<code>Michael	98
Andy	95
Justin	91</code></pre>

<p>数据people.txt </p>

<pre class="has">
<code>Michael	29
Andy	30
Justin	19</code></pre>

<p>代码示例</p>

<pre class="has">
<code class="language-java">import org.apache.spark.sql.SparkSession
import org.apache.spark.{SparkConf, SparkContext}


/**
  * FileName: SparkSQLToHive
  * Author:   hadoop
  * Email:    3165845957@qq.com
  * Date:     18-11-9 下午12:25
  * Description:
  */
object SparkSQLToHive {
  def main(args: Array[String]): Unit = {
    //创建SparkConf用于读取系统信息并设置运用程序的名称
    val conf = new SparkConf().setMaster("local").setAppName("SparkSQLToHive")
    //创建JavaSparkContext对象实例作为整个Driver的核心基石
    val sc = new SparkContext(conf)
    //设置输出log的等级,可以设置INFO,WARN,ERROR
    sc.setLogLevel("INFO")
    //创建SQLContext上下文对象，用于SqL的分析
  val hiveContext = SparkSession
    .builder()
    .enableHiveSupport()
    .appName("SparkSQLToHive")
    .config("spark.some.config.option", "some-value")
    .getOrCreate()
    /**
      * 在目前企业级大数据Spark开发的时候，绝大多数是采用Hive作为数据仓库的spark提供Hive的支持功能，Spark通过HiveContex可以操作Hive中的数据
      * 基于HiveContext可以使用sql/hsql两种方式编写SQL语句对Hive进行操作，
      * 包括创建表、从删除表、往表里导入数据构造各种SQL语法构造各种sql语句对表中的数据进行操作；
      * 二、也可以直接通过saveAsTable的方式把DataFrame中的数据保存到Hive数据仓库中
      * 三、可以直接通过HiveContext.table方法直接加载Hive中的表来生成dataframe
      */
    hiveContext.sql("use hive") //使用Hive数据仓库中的hive数据库
    hiveContext.sql("drop table if exists people") //删除同名的table
    hiveContext.sql("create table if not exists people (name String,age Int)") //创建自定义的table
    hiveContext.sql("load data local inpath '/home/hadoop/people/people.txt' into table people") //导入本地数据到Hive数据仓库中，背后实际上发生了数据的拷贝，当然也可以通过load data input去获得HDFS等上面的数据到Hive（此时发生数据的移动）
    hiveContext.sql("drop table if exists score")
    hiveContext.sql("create table if not exists score (name String,score Int)")
    hiveContext.sql("load data local inpath '/home/hadoop/people/people.txt' into table score")


    //   通过HiveContext使用join直接基于Hive中的两张表进行操作获得成绩大于90分的人的name,age,score
    val resultDS = hiveContext.sql("select people.name,people.age,score.score from people join score on people.name = score.name")

    /**
      * 通过registerTempTable创建一张Hive Manager Table ，数据的元数据和数据即将具体的数据位置都是由Hive数据仓库进行管理，当删除的时候，数据也会一起被删除（磁盘上的数据也被删除）
      */
    hiveContext.sql("drop table if exists peopleinfor")
    resultDS.registerTempTable("peopleinfor")


      //* 使用HiveContext的table方法可以直接去读取Hive数据仓库中的table并生成DataFrame，接下来可以进行机器学习、图计算、各种复杂etl等操作
    val dataFromHive = hiveContext.table("peopleinfor")
    dataFromHive.show()
  }

}</code></pre>

<p> </p>            </div>
                </div>