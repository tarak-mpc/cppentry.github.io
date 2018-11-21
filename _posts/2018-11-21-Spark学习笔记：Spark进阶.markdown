---
layout:     post
title:      Spark学习笔记：Spark进阶
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p id="main-toc"><strong>目录</strong></p>

<p id="Spark%E8%BF%9B%E9%98%B6-toc" style="margin-left:0px;"><a href="#Spark%E8%BF%9B%E9%98%B6" rel="nofollow">Spark进阶</a></p>

<p id="%E4%B8%80.%E5%9C%A8Spark%E4%B8%AD%E4%BD%BF%E7%94%A8%E4%B8%8D%E5%90%8C%E7%9A%84%E6%95%B0%E6%8D%AE%E6%BA%90-toc" style="margin-left:40px;"><a href="#%E4%B8%80.%E5%9C%A8Spark%E4%B8%AD%E4%BD%BF%E7%94%A8%E4%B8%8D%E5%90%8C%E7%9A%84%E6%95%B0%E6%8D%AE%E6%BA%90" rel="nofollow">一.在Spark shell中使用不同的数据源</a></p>

<p id="1.%E9%80%9A%E7%94%A8Load%2FSave%E5%87%BD%E6%95%B0-toc" style="margin-left:80px;"><a href="#1.%E9%80%9A%E7%94%A8Load%2FSave%E5%87%BD%E6%95%B0" rel="nofollow">1.通用Load/Save函数</a></p>

<p id="2.%E6%8E%8C%E6%8F%A1Parquet%E6%96%87%E4%BB%B6-toc" style="margin-left:80px;"><a href="#2.%E6%8E%8C%E6%8F%A1Parquet%E6%96%87%E4%BB%B6" rel="nofollow">2.掌握Parquet文件</a></p>

<p id="3.Spark%20SQL%20JDBC-toc" style="margin-left:80px;"><a href="#3.Spark%20SQL%20JDBC" rel="nofollow">3.Spark SQL JDBC</a></p>

<p id="5.Hive%20On%20Spark-toc" style="margin-left:80px;"><a href="#5.Hive%20On%20Spark" rel="nofollow">4.Hive On Spark</a></p>

<p id="%E4%BA%8C.Spark%20SQL%E5%BC%80%E5%8F%91-toc" style="margin-left:40px;"><a href="#%E4%BA%8C.Spark%20SQL%E5%BC%80%E5%8F%91" rel="nofollow">二.Spark SQL开发</a></p>

<p id="%E4%B8%89.Spark%20SQL%E6%80%A7%E8%83%BD%E8%B0%83%E4%BC%98-toc" style="margin-left:40px;"><a href="#%E4%B8%89.Spark%20SQL%E6%80%A7%E8%83%BD%E8%B0%83%E4%BC%98" rel="nofollow">三.Spark SQL性能调优</a></p>

<hr id="hr-toc"><h1 id="Spark%E8%BF%9B%E9%98%B6">Spark进阶</h1>

<h2 id="%E4%B8%80.%E5%9C%A8Spark%E4%B8%AD%E4%BD%BF%E7%94%A8%E4%B8%8D%E5%90%8C%E7%9A%84%E6%95%B0%E6%8D%AE%E6%BA%90">一.在Spark shell中使用不同的数据源</h2>

<h3 id="1.%E9%80%9A%E7%94%A8Load%2FSave%E5%87%BD%E6%95%B0">1.通用Load/Save函数</h3>

<p>（1）load函数是用在Spark SQL中，加载不同的数据源<br>
默认的数据源是：Parquet文件<br>
通过修改：spark.sql.sources.default参数可以修改默认的数据源</p>

<p>（2）加载Parquet文件，从而创建一个DataFrame<br>
val userDF = spark.read.load("/root/temp/users.parquet")</p>

<p>查看表的结构和数据<br>
userDF.printSchema<br>
userDF.show</p>

<p>（3）显式指定加载的文件格式<br>
val usersDF=spark.read.format("json").load("/root/sources./people.json")</p>

<p>（4）save函数的存储模式：<br>
默认模式是error，如果数据（目录）已经存在，就会报错<br>
append模式：追加写入<br>
overwrite模式：覆盖写入<br>
ignore模式：如果数据已经存在，将不会保存</p>

<p>例子：查询用户的名字和喜欢的颜色，并保存<br>
userDF.select($"name",$"favorite_color").write.mode("overwrite").save("/root/temp/result/parquet")<br>
验证<br>
val testResultDF = spark.read.load("/root/temp/result/parquet/part-00000-d1b9c43c-2ef6-4c48-b5a0-af382751b088-c000.snappy.parquet")    <br>
testResultDF.show</p>

<h3 id="2.%E6%8E%8C%E6%8F%A1Parquet%E6%96%87%E4%BB%B6">2.掌握Parquet文件</h3>

<p>（1）Parquet是一个列式存储文件、是Spark SQL默认的数据源，并且支持对其的读写，也就是自动保存原始数据的Schema。当写Parquet文件时，所有的列被自动转换成Nullable。</p>

<p>（2）把其他类型的数据文件（json文件）转换成是一个Parquet文件</p>

<p>读取json文件<br>
val empJson = spark.read.json("hdfs://centos:9000/emp.json")</p>

<p>转成Parquet文件<br>
empJson.write.mode("overwrite").parquet("hdfs://centos:9000/parquet")</p>

<p>重新从该目录读取数据<br>
val empParquet = spark.read.parquet("hdfs://centos:9000/parquet")</p>

<p>注册为视图<br>
empParquet.createOrReplaceTempView("empview")</p>

<p>进行查询<br>
spark.sql("select * from empview where deptno=10 and sal&gt;1500").show<br>
        <br>
（3）支持Schema的合并<br>
定义两个DataFrame：表<br>
val df1 = sc.makeRDD(1 to 5).map(x=&gt;(x,x*2)).toDF("single","double")<br>
val df2 = sc.makeRDD(6 to 10).map(x=&gt;(x,x*3)).toDF("single","triple")<br>
             <br>
保存到目录：默认是Parquet文件<br>
df1.write.parquet("hdfs://centos:9000/parquet/test_table/key=1")<br>
df2.write.parquet("hdfs://centos:9000/parquet/test_table/key=2")<br>
            <br>
读取hdfs://centos:9000/parquet/test_table目录，使用mergeSchema合并Schema<br>
val df3 = spark.read.option("mergeSchema","true").parquet("hdfs://centos:9000/parquet/test_table")<br><img alt="" class="has" height="459" src="https://img-blog.csdn.net/20180907104931296?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xyeGNtd3ky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="436"></p>

<h3 id="3.Spark%20SQL%20JDBC">3.Spark SQL JDBC</h3>

<p>（1）JDBC连接Oracle<br>
启动Spark shell加载（指定）Oracle的JDBC的Driver<br>
spark-shell --master spark://bigdata111:7077 --jars /root/temp/ojdbc6.jar --driver-class-path /root/temp/ojdbc6.jar</p>

<p>读取Oracle中的数据</p>

<p>方式1：直接读取</p>

<p>spark.read.format("jdbc").option("url","jdbc:oracle:thin:@192.168.157.101:1521/orcl.example.com").option("dbtable","scott.emp").option("user","scott").option("password","tiger").load</p>

<p>方式2：定义一个Properties类来进行读取<br>
import java.util.Properties<br>
val oracleProp = new Properties()<br>
oracleProp.setProperty("user","scott")<br>
oracleProp.setProperty("password","tiger")<br>
 val oracleDF = spark.read.jdbc("jdbc:oracle:thin:@192.168.157.101:1521/orcl.example.com","scott.emp",oracleProp)</p>

<p>（2）JDBC连接MySQL<br>
启动Spark shell加载（指定）MySQL的JDBC的Driver<br>
spark-shell --master spark://centos:7077 --jars /opt/software/hive-1.1.0-cdh5.14.0/lib/mysql-connector-java-5.1.40-bin.jar</p>

<p>定义一个Properties类<br>
import java.util.Properties<br>
val mysqlProp = new Properties()<br>
mysqlProp.setProperty("user","root")<br>
mysqlProp.setProperty("password,"root")<br>
mysqlProp.setProperty("driver","com.mysql.jdbc.Driver")<br>
val mysqldf=spark.read.jdbc("jdbc:mysql://centos:3306","test.Scores",mysqlProp)</p>

<h3 id="5.Hive%20On%20Spark">4.Hive On Spark</h3>

<p>Hive是基于HDFS之上的一个数据仓库，表或者表中的分区对应的是HDFS中的目录，数据对应的是HDFS中的文件。<br>
从另一个角度理解Hive，就是一个数据分析的引擎，支持HQL。<br>
它也是一个翻译器，把SQL翻译成MapReduce程序，但是从Hive 2.x开始，推荐使用Spark作为Hive的执行引擎</p>

<p><strong>通过Spark-SQL 对Hive的数据进行操作</strong></p>

<p>把hive-site.xml复制到SPARK_HOME/conf，把mysql驱动复制到$SPARK_HOME/jars。<br>
进到SPARK_HOME/bin，执行spark-sql --jars SPARK_HOME/jars/mysql-connector-java-5.1.40-bin.jar<br><img alt="" class="has" height="684" src="https://img-blog.csdn.net/20180910194735856?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xyeGNtd3ky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1000"></p>

<p> </p>

<h2 id="%E4%BA%8C.Spark%20SQL%E5%BC%80%E5%8F%91">二.Spark SQL开发</h2>

<p>1.使用StructType建立Schema，对文本文件里的数据进行读取，创建DataFrame，并建立视图进行查询</p>

<pre class="has">
<code class="language-java">package sparksql

import org.apache.spark.sql.SparkSession
import org.apache.spark.sql.types.StructType
import org.apache.spark.sql.types.StructField
import org.apache.spark.sql.types.IntegerType
import org.apache.spark.sql.types.StringType
import org.apache.spark.sql.Row

object SpecifySchema {
  def main(args: Array[String]): Unit = {
    //创建SparkSession对象
    val spark = SparkSession.builder().master("local").appName("SpecifyingSchema").getOrCreate()
    //从数据文件中读取数据，创建RDD
    val studentRDD = spark.sparkContext.textFile("d:\\student.txt").map(_.split(" "))
    //创建Schema，通过StructType
    val schema = StructType(List(
      //true表示这一列是否允许为空
      StructField("id", IntegerType, true),
      StructField("name", StringType, true),
      StructField("age", IntegerType, true)))
    //将studentRDD每一个数据映射到一个Row
    val rowRDD = studentRDD.map(p =&gt; Row(p(0).toInt, p(1), p(2).toInt))
    
    //将Schema信息映射到rowRDDshang 
    val studentDF = spark.createDataFrame(rowRDD, schema)
    
    //注册视图
    studentDF.createOrReplaceTempView("students")
    
    //进行查询
    spark.sql("select * from students").show
    
    //停止SparkSession
    spark.stop()
  }
}
  
  </code></pre>

<p>运行结果如下<br><img alt="" class="has" height="234" src="https://img-blog.csdn.net/20180910200450668?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xyeGNtd3ky/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1000"></p>

<p>2.运用case class建立schema，将数据与case class主构造器中的参数对照起来</p>

<pre class="has">
<code>package sparksql

import org.apache.spark.sql.SparkSession
import org.apache.spark.SparkContext

case class Student(stuID: Int, stuName: String, stuAge: Int)

object caseclassDemo {
  def main(args: Array[String]): Unit = {

    //创建SparkSession
    val spark = SparkSession.builder().master("local").appName("CaseClassDemo").getOrCreate()
    //读取文本文件，分词，建立RDD
    val lineRDD = spark.sparkContext.textFile("d:\\student.txt").map(_.split(" "))
    //将RDD与caseclass关联
    val studentRDD = lineRDD.map(line =&gt; Student(line(0).toInt, line(1), line(2).toInt))
    //创建DataFrame
    val studentDF = spark.createDataFrame(studentRDD)
    //创建视图
    studentDF.createOrReplaceTempView("student")
    //进行sql查询
    spark.sql("select * from student").show
    //关闭SparkSession
    spark.close()

  }
}</code></pre>

<p>3.从MySQL中读取数据，以及将数据保存到MySQL数据库中</p>

<pre class="has">
<code>package sparksql

import org.apache.spark.sql.SparkSession
import org.apache.spark.sql.types.StructType
import org.apache.spark.sql.types.StructField
import org.apache.spark.sql.types.IntegerType
import org.apache.spark.sql.types.StringType
import org.apache.spark.sql.Row
import org.apache.spark.SparkContext
import org.apache.spark.SparkConf
import org.apache.spark.sql._
import org.apache.spark.sql.types._

object Spark2MySQL {
  def main(args: Array[String]) {
    //创建sparkconf，sparkconetxt对象用于导入文件，sparksession用于与DBMS交互
    val conf = new SparkConf().setAppName("test").setMaster("local")
    val sc = new SparkContext(conf)
    val spark = SparkSession.builder().master("local").appName("SpecifyingSchema").getOrCreate()
    //通过jdbc的方式去读取MySQL表里的数据，并且转化为DataFrame
    val mysqlDF = spark.read.format("jdbc")
      .option("url", "jdbc:mysql://Hadoop01:3306")
      .option("dbtable", "test.student")
      .option("user", "root")
      .option("password", "root")
      .load()
    //在console中输出数据
    mysqlDF.show
    
    //从HDFS中读取数据
    val emp = sc.textFile("hdfs://Hadoop01:9000/emp.csv", 1).map(_.split(","))
    //通过StructType的方式建立Schema
    val myschema = StructType(List(
      StructField("empno", DataTypes.IntegerType),
      StructField("ename", DataTypes.StringType),
      StructField("job", DataTypes.StringType),
      StructField("mgr", DataTypes.StringType),
      StructField("hiredate", DataTypes.StringType),
      StructField("sal", DataTypes.IntegerType),
      StructField("comm", DataTypes.StringType),
      StructField("deptno", DataTypes.IntegerType)))

    //将数据与schema关联起来
    val rowRDD = emp.map(x =&gt; Row(x(0).toInt, x(1), x(2), x(3), x(4), x(5).toInt, x(6), x(7).toInt))

    //将RDD转换成DataFrame
    val df = spark.createDataFrame(rowRDD, myschema)

    //将DataFrame写入到MySQL
    df.write.format("jdbc")
      .option("url", "jdbc:mysql://Hadoop01:3306")
      .option("dbtable", "test.emp")
      .option("user", "root")
      .option("password", "root")
      .save()

    //关闭Sparksession
    spark.close()
  }
}</code></pre>

<p>对于在scala中开发与Hive的交互，感觉还没有什么好的一步到位的方法，因为类型对应的问题很麻烦，所以还是先老老实实存到HDFS然后load进去Hive吧QAQ</p>

<h2 id="%E4%B8%89.Spark%20SQL%E6%80%A7%E8%83%BD%E8%B0%83%E4%BC%98">三.Spark SQL性能调优</h2>

<p>通过在内存中缓存数据，从而提高读取性能</p>

<p>1.通过加载MySQL的Driver，在spark-shell中访问MySQL的数据</p>

<pre class="has">
<code>spark-shell --master spark://Hadoop01:7077 --jars /opt/software/apache-hive-2.2.0-bin/lib/mysql-connector-java-5.1.40-bin.jar --driver-class-path /opt/software/apache-hive-2.2.0-bin/lib/mysql-connector-java-5.1.40-bin.jar</code></pre>

<p>2.使用JDBC读取MySQL中的数据</p>

<pre class="has">
<code>val mysqlDF= spark.read.format("jdbc").option("url","jdbc:mysql://Hadoop01:3306").option("dbtable","test.student").option("user","root").option("password","root").load()</code></pre>

<p>3.将DataFrame注册成表<br>
mysqlDF.registerTempTable("student")<br>
4.执行查询，缓存后再进行查询，通过在Spark WebUI中查看执行时间<br>
spark.sql("select * from student").show<br>
缓存表<br>
spark.sqlContext.cacheTable("student")<br>
再次执行查询<br>
spark.sql("select * from student").show<br>
5.清空缓存<br>
spark.sqlContext.uncacheTable("student")<br>
spark.sqlContext.clearCache</p>            </div>
                </div>