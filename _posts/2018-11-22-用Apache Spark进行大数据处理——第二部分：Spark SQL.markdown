---
layout:     post
title:      用Apache Spark进行大数据处理——第二部分：Spark SQL
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="border:0px;font-size:14px;line-height:25.2px;clear:none;width:610px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
基于Spark SQL可以重用HIve本身提供的元数据仓库（MetaStore).HiveQL,用户自定义函数UDF,及序列化和反序列化的工具（SerDes）</p>
<p style="border:0px;font-size:14px;line-height:25.2px;clear:none;width:610px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
在Apache Spark文章系列的<a href="http://www.infoq.com/articles/apache-spark-introduction" rel="nofollow" style="text-decoration:none;color:rgb(40,106,178);border:0px;">前一篇文章</a>中，我们学习了什么是Apache Spark框架，以及如何用该框架帮助组织处理大数据处理分析的需求。</p>
<p style="border:0px;font-size:14px;line-height:25.2px;clear:none;width:610px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
Spark SQL，作为Apache Spark大数据框架的一部分，主要用于结构化数据处理和对Spark数据执行类SQL的查询。通过Spark SQL，可以针对不同格式的数据执行ETL操作（如JSON，Parquet，数据库）然后完成特定的查询操作。</p>
<p style="border:0px;font-size:14px;line-height:25.2px;clear:none;width:610px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
在这一文章系列的第二篇中，我们将讨论Spark SQL库，如何使用Spark SQL库对存储在批处理文件、JSON数据集或Hive表中的数据执行SQL查询。</p>
<div class="clear" style="border:0px;clear:both;font-size:0px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
</div>
<p style="border:0px;font-size:14px;line-height:25.2px;clear:none;width:610px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
Spark大数据处理框架目前最新的版本是上个月发布的Spark 1.3。这一版本之前，Spark SQL模块一直处于“Alpha”状态，现在该团队已经从Spark SQL库上将这一标签移除。这一版本中包含了许多新的功能特性，其中一部分如下：</p>
<ul style="border:0px;clear:left;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;font-size:14px;line-height:25.2px;"><li style="border:0px;clear:none;">
<span style="border:0px;font-weight:600;">数据框架（DataFrame</span><span style="border:0px;font-weight:600;">）：</span>Spark新版本中提供了可以作为分布式SQL查询引擎的程序化抽象DataFrame。</li><li style="border:0px;clear:none;">
<span style="border:0px;font-weight:600;">数据源（Data Sources</span><span style="border:0px;font-weight:600;">）：</span>随着数据源API的增加，Spark SQL可以便捷地处理以多种不同格式存储的结构化数据，如Parquet，JSON以及Apache Avro库。</li><li style="border:0px;clear:none;">
<span style="border:0px;font-weight:600;">JDBC</span><span style="border:0px;font-weight:600;">服务器（JDBC Server</span><span style="border:0px;font-weight:600;">）：</span>内置的JDBC服务器可以便捷地连接到存储在关系型数据库表中的结构化数据并利用传统的商业智能（BI）工具进行大数据分析。</li></ul><h2 style="font-size:20px;font-weight:normal;border:0px;clear:none;width:610px;color:rgb(34,34,34);line-height:24px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
Spark SQL组件</h2>
<p style="border:0px;font-size:14px;line-height:25.2px;clear:none;width:610px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
使用Spark SQL时，最主要的两个组件就是DataFrame和SQLContext。</p>
<p style="border:0px;font-size:14px;line-height:25.2px;clear:none;width:610px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
首先，我们来了解一下DataFrame。</p>
<div id="lowerFullwidthVCR" style="border:0px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;font-size:14px;line-height:25.2px;">
</div>
<h3 style="font-size:18px;font-weight:normal;border:0px;clear:none;width:610px;color:rgb(34,34,34);line-height:36px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
DataFrame</h3>
<p style="border:0px;font-size:14px;line-height:25.2px;clear:none;width:610px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
<a href="https://spark.apache.org/docs/1.3.0/api/scala/index.html#org.apache.spark.sql.DataFrame" rel="nofollow" style="text-decoration:none;color:rgb(40,106,178);border:0px;">DataFrame</a>是一个分布式的，按照命名列的形式组织的数据集合。DataFrame基于R语言中的data
 frame概念，与关系型数据库中的数据库表类似。</p>
<p style="border:0px;font-size:14px;line-height:25.2px;clear:none;width:610px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
之前版本的Spark SQL API中的SchemaRDD已经更名为DataFrame。</p>
<p style="border:0px;font-size:14px;line-height:25.2px;clear:none;width:610px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
通过调用将DataFrame的内容作为行RDD（RDD of Rows）返回的<a href="https://spark.apache.org/docs/1.3.0/api/scala/index.html#org.apache.spark.sql.DataFrame" rel="nofollow" style="text-decoration:none;color:rgb(40,106,178);border:0px;">rdd方法</a>，可以将DataFrame转换成RDD。</p>
<p style="border:0px;font-size:14px;line-height:25.2px;clear:none;width:610px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
可以通过如下数据源创建DataFrame：</p>
<ul style="border:0px;clear:left;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;font-size:14px;line-height:25.2px;"><li style="border:0px;clear:none;">
已有的RDD</li><li style="border:0px;clear:none;">
结构化数据文件</li><li style="border:0px;clear:none;">
JSON数据集</li><li style="border:0px;clear:none;">
Hive表</li><li style="border:0px;clear:none;">
外部数据库</li></ul><p style="border:0px;font-size:14px;line-height:25.2px;clear:none;width:610px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
Spark SQL和DataFrame API已经在下述几种程序设计语言中实现：</p>
<ul style="border:0px;clear:left;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;font-size:14px;line-height:25.2px;"><li style="border:0px;clear:none;">
Scala（https://spark.apache.org/docs/1.3.0/api/scala/index.html#org.apache.spark.sql.package）</li><li style="border:0px;clear:none;">
Java（https://spark.apache.org/docs/1.3.0/api/java/index.html?org/apache/spark/sql/api/java/package-summary.html）</li><li style="border:0px;clear:none;">
Python（https://spark.apache.org/docs/1.3.0/api/python/pyspark.sql.html）</li></ul><p style="border:0px;font-size:14px;line-height:25.2px;clear:none;width:610px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
本文中所涉及的Spark SQL代码示例均使用Spark Scala Shell程序。</p>
<h3 style="font-size:18px;font-weight:normal;border:0px;clear:none;width:610px;color:rgb(34,34,34);line-height:36px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
SQLContext</h3>
<p style="border:0px;font-size:14px;line-height:25.2px;clear:none;width:610px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
Spark SQL提供<a href="http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.SQLContext" rel="nofollow" style="text-decoration:none;color:rgb(40,106,178);border:0px;">SQLContext</a>封装Spark中的所有关系型功能。可以用之前的示例中的现有SparkContext创建SQLContext。下述代码片段展示了如何创建一个SQLContext对象。</p>
<pre style="border:1px solid rgb(232,232,232);font-family:Consolas, Monaco, 'Andale Mono', monospace;color:rgb(49,78,100);line-height:21px;width:597.797px;overflow:auto;clear:none;font-size:14px;background-color:rgb(245,242,240);">val sqlContext = new org.apache.spark.sql.SQLContext(sc)</pre>
<p style="border:0px;font-size:14px;line-height:25.2px;clear:none;width:610px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
此外，Spark SQL中的<a href="https://spark.apache.org/docs/1.3.0/api/scala/index.html#org.apache.spark.sql.hive.HiveContext" rel="nofollow" style="text-decoration:none;color:rgb(40,106,178);border:0px;">HiveContext</a>可以提供SQLContext所提供功能的超集。可以在用HiveQL解析器编写查询语句以及从Hive表中读取数据时使用。</p>
<p style="border:0px;font-size:14px;line-height:25.2px;clear:none;width:610px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
在Spark程序中使用HiveContext无需既有的Hive环境。</p>
<h3 style="font-size:18px;font-weight:normal;border:0px;clear:none;width:610px;color:rgb(34,34,34);line-height:36px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
JDBC数据源</h3>
<p style="border:0px;font-size:14px;line-height:25.2px;clear:none;width:610px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
Spark SQL库的其他功能还包括数据源，如JDBC数据源。</p>
<p style="border:0px;font-size:14px;line-height:25.2px;clear:none;width:610px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
JDBC数据源可用于通过JDBC API读取关系型数据库中的数据。相比于使用<a href="https://spark.apache.org/docs/1.3.0/api/scala/index.html#org.apache.spark.rdd.JdbcRDD" rel="nofollow" style="text-decoration:none;color:rgb(40,106,178);border:0px;">JdbcRDD</a>，应该将JDBC数据源的方式作为首选，因为JDBC数据源能够将结果作为DataFrame对象返回，直接用Spark
 SQL处理或与其他数据源连接。</p>
<h3 style="font-size:18px;font-weight:normal;border:0px;clear:none;width:610px;color:rgb(34,34,34);line-height:36px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
Spark SQL示例应用</h3>
<p style="border:0px;font-size:14px;line-height:25.2px;clear:none;width:610px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
在上一篇文章中，我们学习了如何在本地环境中安装Spark框架，如何启动Spark框架并用Spark Scala Shell与其交互。如需安装最新版本的Spark，可以从Spark<a href="http://spark.apache.org/downloads.html" rel="nofollow" style="text-decoration:none;color:rgb(40,106,178);border:0px;">网站</a>下载该软件。</p>
<p style="border:0px;font-size:14px;line-height:25.2px;clear:none;width:610px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
对于本文中的代码示例，我们将使用相同的Spark Shell执行Spark SQL程序。这些代码示例适用于Windows环境。</p>
<p style="border:0px;font-size:14px;line-height:25.2px;clear:none;width:610px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
为了确保Spark Shell程序有足够的内存，可以在运行spark-shell命令时，加入driver-memory命令行参数，如下所示：</p>
<pre style="border:1px solid rgb(232,232,232);font-family:Consolas, Monaco, 'Andale Mono', monospace;color:rgb(49,78,100);line-height:21px;width:597.797px;overflow:auto;clear:none;font-size:14px;background-color:rgb(245,242,240);">spark-shell.cmd --driver-memory 1G</pre>
<h3 style="font-size:18px;font-weight:normal;border:0px;clear:none;width:610px;color:rgb(34,34,34);line-height:36px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
Spark SQL应用</h3>
<p style="border:0px;font-size:14px;line-height:25.2px;clear:none;width:610px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
Spark Shell启动后，就可以用Spark SQL API执行数据分析查询。</p>
<p style="border:0px;font-size:14px;line-height:25.2px;clear:none;width:610px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
在第一个示例中，我们将从文本文件中加载用户数据并从数据集中创建一个DataFrame对象。然后运行DataFrame函数，执行特定的数据选择查询。</p>
<p style="border:0px;font-size:14px;line-height:25.2px;clear:none;width:610px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
文本文件customers.txt中的内容如下：</p>
<pre style="border:1px solid rgb(232,232,232);font-family:Consolas, Monaco, 'Andale Mono', monospace;color:rgb(49,78,100);line-height:21px;width:597.797px;overflow:auto;clear:none;font-size:14px;background-color:rgb(245,242,240);">100, John Smith, Austin, TX, 78727
200, Joe Johnson, Dallas, TX, 75201
300, Bob Jones, Houston, TX, 77028
400, Andy Davis, San Antonio, TX, 78227
500, James Williams, Austin, TX, 78727</pre>
<p style="border:0px;font-size:14px;line-height:25.2px;clear:none;width:610px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
下述代码片段展示了可以在Spark Shell终端执行的Spark SQL命令。</p>
<pre style="border:1px solid rgb(232,232,232);font-family:Consolas, Monaco, 'Andale Mono', monospace;color:rgb(49,78,100);line-height:21px;width:597.797px;overflow:auto;clear:none;font-size:14px;background-color:rgb(245,242,240);">// 首先用已有的Spark Context对象创建SQLContext对象
val sqlContext = new org.apache.spark.sql.SQLContext(sc)

// 导入语句，可以隐式地将RDD转化成DataFrame
import sqlContext.implicits._

// 创建一个表示客户的自定义类
case class Customer(customer_id: Int, name: String, city: String, state: String, zip_code: String)

// 用数据集文本文件创建一个Customer对象的DataFrame
val dfCustomers = sc.textFile("data/customers.txt").map(_.split(",")).map(p =&gt; Customer(p(0).trim.toInt, p(1), p(2), p(3), p(4))).toDF()

// 将DataFrame注册为一个表
dfCustomers.registerTempTable("customers")

// 显示DataFrame的内容
dfCustomers.show()

// 打印DF模式
dfCustomers.printSchema()

// 选择客户名称列
dfCustomers.select("name").show()

// 选择客户名称和城市列
dfCustomers.select("name", "city").show()

// 根据id选择客户
dfCustomers.filter(dfCustomers("customer_id").equalTo(500)).show()

// 根据邮政编码统计客户数量
dfCustomers.groupBy("zip_code").count().show()
</pre>
<p style="border:0px;font-size:14px;line-height:25.2px;clear:none;width:610px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
在上一示例中，模式是通过反射而得来的。我们也可以通过编程的方式指定数据集的模式。这种方法在由于数据的结构以字符串的形式编码而无法提前定义定制类的情况下非常实用。</p>
<p style="border:0px;font-size:14px;line-height:25.2px;clear:none;width:610px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
如下代码示例展示了如何使用新的数据类型类StructType，StringType和StructField指定模式。</p>
<pre style="border:1px solid rgb(232,232,232);font-family:Consolas, Monaco, 'Andale Mono', monospace;color:rgb(49,78,100);line-height:21px;width:597.797px;overflow:auto;clear:none;font-size:14px;background-color:rgb(245,242,240);">//
// 用编程的方式指定模式
//

// 用已有的Spark Context对象创建SQLContext对象
val sqlContext = new org.apache.spark.sql.SQLContext(sc)

// 创建RDD对象
val rddCustomers = sc.textFile("data/customers.txt")

// 用字符串编码模式
val schemaString = "customer_id name city state zip_code"

// 导入Spark SQL数据类型和Row
import org.apache.spark.sql._

import org.apache.spark.sql.types._;

// 用模式字符串生成模式对象
val schema = StructType(schemaString.split(" ").map(fieldName =&gt; StructField(fieldName, StringType, true)))

// 将RDD（rddCustomers）记录转化成Row。
val rowRDD = rddCustomers.map(_.split(",")).map(p =&gt; Row(p(0).trim,p(1),p(2),p(3),p(4)))

// 将模式应用于RDD对象。
val dfCustomers = sqlContext.createDataFrame(rowRDD, schema)

// 将DataFrame注册为表
dfCustomers.registerTempTable("customers")

// 用sqlContext对象提供的sql方法执行SQL语句。
val custNames = sqlContext.sql("SELECT name FROM customers")

// SQL查询的返回结果为DataFrame对象，支持所有通用的RDD操作。
// 可以按照顺序访问结果行的各个列。
custNames.map(t =&gt; "Name: " + t(0)).collect().foreach(println)

// 用sqlContext对象提供的sql方法执行SQL语句。
val customersByCity = sqlContext.sql("SELECT name,zip_code FROM customers ORDER BY zip_code")

// SQL查询的返回结果为DataFrame对象，支持所有通用的RDD操作。
// 可以按照顺序访问结果行的各个列。
customersByCity.map(t =&gt; t(0) + "," + t(1)).collect().foreach(println)
</pre>
<p style="border:0px;font-size:14px;line-height:25.2px;clear:none;width:610px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
除了文本文件之外，也可以从其他数据源中加载数据，如JSON数据文件，Hive表，甚至可以通过JDBC数据源加载关系型数据库表中的数据。</p>
<p style="border:0px;font-size:14px;line-height:25.2px;clear:none;width:610px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
如上所示，Spark SQL提供了十分友好的SQL接口，可以与来自多种不同数据源的数据进行交互，而且所采用的语法也是团队熟知的SQL查询语法。这对于非技术类的项目成员，如数据分析师以及数据库管理员来说，非常实用。</p>
<p style="border:0px;font-size:14px;line-height:25.2px;clear:none;width:610px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
<span style="border:0px;font-weight:600;"></span></p>
<h2 style="font-size:20px;font-weight:normal;border:0px;clear:none;width:610px;color:rgb(34,34,34);line-height:24px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
总结</h2>
<p style="border:0px;font-size:14px;line-height:25.2px;clear:none;width:610px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
本文中，我们了解到Apache Spark SQL如何用熟知的SQL查询语法提供与Spark数据交互的SQL接口。Spark SQL是一个功能强大的库，组织中的非技术团队成员，如业务分析师和数据分析师，都可以用Spark SQL执行数据分析。</p>
<p style="border:0px;font-size:14px;line-height:25.2px;clear:none;width:610px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
下一篇文章中，我们将讨论可用于处理实时数据或流数据的<a href="http://spark.apache.org/streaming/" rel="nofollow" style="text-decoration:none;color:rgb(40,106,178);border:0px;">Spark Streaming库</a>。Spark Streaming库是任何一个组织的整体数据处理和管理生命周期中另外一个重要的组成部分，因为流数据处理可为我们提供对系统的实时观察。这对于欺诈检测、在线交易系统、事件处理解决方案等用例来说至关重要。</p>
<h2 style="font-size:20px;font-weight:normal;border:0px;clear:none;width:610px;color:rgb(34,34,34);line-height:24px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
参考文献</h2>
<ul style="border:0px;clear:left;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;font-size:14px;line-height:25.2px;"><li style="border:0px;clear:none;">
<a href="http://spark.apache.org/" rel="nofollow" style="text-decoration:none;color:rgb(40,106,178);border:0px;">Spark主站</a></li><li style="border:0px;clear:none;">
<a href="https://spark.apache.org/sql/" rel="nofollow" style="text-decoration:none;color:rgb(40,106,178);border:0px;">Spark SQL网站</a></li><li style="border:0px;clear:none;">
<a href="https://spark.apache.org/docs/latest/sql-programming-guide.html" rel="nofollow" style="text-decoration:none;color:rgb(40,106,178);border:0px;">Spark SQL程序设计指南</a></li><li style="border:0px;clear:none;">
<a href="http://www.infoq.com/articles/apache-spark-introduction" rel="nofollow" style="text-decoration:none;color:rgb(40,106,178);border:0px;">用Apache Spark进行大数据处理——第一部分：入门介绍</a></li></ul><h2 style="font-size:20px;font-weight:normal;border:0px;clear:none;width:610px;color:rgb(34,34,34);line-height:24px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
关于作者</h2>
<p style="border:0px;font-size:14px;line-height:25.2px;clear:none;width:610px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
<img src="http://cdn4.infoqstatic.com/statics_s1_20160517-0032u4/resource/articles/apache-spark-sql/zh/resources/0520079.jpg" alt="" style="border:0px;"><span style="border:0px;font-weight:600;">Srini
 Penchikala</span>目前是一家金融服务机构的软件架构师，这个机构位于德克萨斯州的奥斯汀。他在软件系统架构、设计和开发方面有超过20年的经验。Srini目前正在撰写一本关于NoSQL数据库模式的书。他还是曼宁出版社出版的《Spring Roo in Action》一书的合著者（http://www.manning.com/SpringRooinAction）。他还曾经出席各种会议，如JavaOne，SEI Architecture Technology Conference（SATURN），IT
 Architect Conference（ITARC），No Fluff Just Stuff，NoSQL Now和Project World Conference等。Srini还在InfoQ，The ServerSide，OReilly Network（ONJava），DevX Java，java.net以及JavaWorld等网站上发表过很多关于软件系统架构、安全和风险管理以及NoSQL数据库等方面的文章。他还是InfoQ NoSQL数据库社区的责任编辑（http://www.infoq.com/author/Srini-Penchikala）。</p>
<div style="border:0px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;font-size:14px;line-height:25.2px;clear:both;">
</div>
<p style="border:0px;font-size:14px;line-height:25.2px;clear:none;width:610px;font-family:'Lantinghei SC', 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, Helvetica, sans-serif;">
<span style="font-weight:600;border:0px;">查看英文原文：</span><a href="http://www.infoq.com/articles/apache-spark-sql" rel="nofollow" style="text-decoration:none;color:rgb(40,106,178);border:0px;">Big
 Data Processing with Apache Spark - Part 2: Spark SQL</a></p>
            </div>
                </div>