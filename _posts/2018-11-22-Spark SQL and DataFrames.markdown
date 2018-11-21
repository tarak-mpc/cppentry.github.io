---
layout:     post
title:      Spark SQL and DataFrames
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：转载或者应用请注明出处					https://blog.csdn.net/qq_35180983/article/details/83903423				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1><a id="Spark_SQL_and_DataFrames_0"></a>Spark SQL and DataFrames</h1>
<h2><a id="Spark_SQL_2"></a>Spark SQL</h2>
<h3><a id="Spark_SQL_4"></a>Spark SQL概述</h3>
<h4><a id="Spark_SQL_6"></a>什么是Spark SQL？</h4>
<p>SparkSQL是spark用来处理结构化数据的一个模块，它提供了一个编程抽象叫做DataFrames，并且作为分布式SQL查询引擎的作用。</p>
<p>为什么要学SparkSQL?<br>
SparkSQL将Spark SQL转换为RDD，然后提交到集群执行，执行效率非常快，比如hive是将hive SQL转换为MapReduce然后提交到集群上执行，大大简化了编写程序的复杂性。</p>
<h4><a id="_12"></a>总结:</h4>
<ul>
<li>SparkSQL是Spark上的高级模块，SparkSQL是一个SQL解析引擎，将SQL解析成特殊的RDD（DataFrame），然后在Spark集群中运行。</li>
<li>SparkSQL是用来处理结构化数据的（先将非结构化的数据转换成结构化数据）</li>
<li>SparkSQL支持两种编程API 1.SQL方式 2.DataFrame的方式（DSL）</li>
<li>SparkSQL兼容hive（元数据库、SQL语法、UDF、序列化、反序列化机制）</li>
<li>SparkSQL支持统一的数据源，课程读取多种类型的数据</li>
<li>SparkSQL提供了标准的连接（JDBC、ODBC），以后可以对接一下BI工具</li>
</ul>
<p>参考：<a href="http://spark.apache.org/sql/" rel="nofollow">http://spark.apache.org/sql/</a></p>
<h4><a id="SparkSQL_22"></a>SparkSQL特点：</h4>
<ul>
<li>Integraied:易整合，SparkSQL允许SQL、DataFrame API访问数据。</li>
<li>Union Data Access:统一的数据访问方式，提供了访问各种数据源的通用方法，包括Hive、Avro、Parquet、ORC、JSON和JDBC。</li>
<li>Hive Integration:兼容hive，支持HiveQL语法以及HiveSerDes和UDF。</li>
<li>Standard Connectivity:标准的数据连接，支持标准连接（JDBC、ODBC)</li>
</ul>
<h2><a id="DataFrames_28"></a>DataFrames</h2>
<h3><a id="DataFrames_29"></a>什么是DataFrames？</h3>
<p>与RDD类似，DataFrames也是一个分布式数据容器，但DataFrames更像传统数据库的二维表格，除了数据以外，还记录数据的结构信息，即schema。它是抽象的数据集，放的都是描述信息，DataFrames是一个特殊的RDD。好处：可以使用SQL语句，带来编码上的方便，降低了spark的使用难度。<br>
<img src="https://i.imgur.com/fxroXXC.png" alt=""></p>
<p>在Spark中，DataFrame是一种按列组织的分布式数据集，概念上等价于关系数据库中一个表或者是Python中的data frame，但是在底层进行了更丰富的优化。</p>
<h3><a id="DataFrameRDD_35"></a>DataFrame与RDD对比</h3>
<h4><a id="RDDDataFrame_36"></a>RDD和DataFrame的区别：</h4>
<p>DataFrame里面存放的结构化数据的描述信息，DataFrame要有表头（表的描述信息），描述了有多少列，每一列数叫什么名字、什么类型、能不能为空<br>
DataFrame是特殊的RDD（RDD+Schema信息就变成了DataFrame）DataFrame是一种以RDD为基础的分布式数据集，类似于传统数据库中的二维表格。<br>
与RDD的主要区别在于：前者带有Schema元数据，即DataFrame所表示的二维数据集的每一列都有名称和类型。<br>
由于无法知道RDD数据集内部的结构，Spark执行作业只能在调度阶段进行简单通用的优化，而DataFrame带有数据集内部的结构，可以根据这些信息进行针对性的优化，最终实现优化运行效率。</p>
<h4><a id="DataFrame_43"></a>DataFrame带来的好处：</h4>
<ul>
<li>精简代码</li>
<li>提升执行效率</li>
<li>减少数据读取：忽略无关数据，根据查询条件进行适当裁剪。</li>
</ul>
<h3><a id="DataFrame_47"></a>DataFrame使用</h3>
<p>Spark SQL支持两种方式来将RDD转换为DataFrame。<br>
第一种方式，是使用反射来推断包含了特定数据类型的RDD的元数据。这种基于反射的方式，代码比较简洁，当你已经知道你的RDD的元数据时，是一种非常不错的方式。<br>
第二种方式，是通过编程接口来创建DataFrame，你可以在程序运行时动态构建一份元数据，然后将其应用到已经存在的RDD上。这种方式的代码比较冗长，但是如果在编写程序时，还不知道RDD的元数据，只有在程序运行时，才能动态得知其元数据，那么只能通过这种动态构建元数据的方式。</p>
<h4><a id="DataFrames_51"></a>创建DataFrames</h4>
<p>在Spark SQL中sqlContext是创建Sql的入口<br>
在1.6.x前入口为sqlContext，2.x后入口为SparkSession。</p>
<p>4种：<br>
结构化数据文件、hive表、外部表、已经存在的RDDs</p>
<p>存在的RDDs:<br>
反射、编程（2种）</p>
<h4><a id="16x_61"></a>1.6.x</h4>
<p>在1.x的基础上，由普通的RDD转化成DataFrame，然后执行SQL，具体步骤如下：<br>
1.创建sparkContext，然后再创建SQLContext<br>
2.先创建RDD，对数据进行整理，然后关联case class，将非结构化数据转换成结构化数据<br>
3.显示的调用toDF方法将RDD转换成DataFrame<br>
4.注册临时表<br>
5.执行SQL（Transformation，lazy）<br>
6.执行Action</p>
<p>case class就定义了元数据。Spark SQL会通过反射读取传递给case class的参数的名称，然后将其作为列名。</p>
<pre><code>&lt;dependency&gt;
        &lt;groupId&gt;org.apache.spark&lt;/groupId&gt;
        &lt;artifactId&gt;spark-sql_2.10&lt;/artifactId&gt;
        &lt;version&gt;1.6.1&lt;/version&gt;
&lt;/dependency&gt;
</code></pre>
<p>使用反射推断Schema在已有的RDD创建:</p>
<pre><code>import org.apache.spark.sql.SQLContext
import org.apache.spark.{SparkConf, SparkContext}

//使用反射的方式，把RDD转化为DataFrame
object RDDtoDf {
  def main(args: Array[String]): Unit = {
    //创建RDD
    val sparkConf = new SparkConf().setAppName("sparkSqlDemo").setMaster("local")
    val sc = new SparkContext(sparkConf)

    //创建sparksql的入口
    val sqlContext = new SQLContext(sc)

    val linesrdd = sc.textFile("C:\\Users\\brz\\Desktop\\数据\\person.txt")

    //把RDD关联schema（case class）
    val personRDD = linesrdd
      .map(line =&gt; {
      val fields = line.split(",")
      val id = fields(0).toLong
      val name = fields(1)
      val age = fields(2).toInt
      val faceValue = fields(3).toDouble
      Person(id, name, age, faceValue)
    })

    //转换RDD=&gt;DataFrame
	//导入隐式转换
    import sqlContext.implicits._
    val df = personRDD.toDF()

    //使用SQL
    //把DataFrame注册成一张临时表
    df.registerTempTable("t_person")

    //执行SQL语句
    val res = sqlContext.sql("select * from t_person")

    //查看结果
    res.show()
    sc.stop()
  }
}

//定义case class（相当于表的schema）
case class Person(id:Long,name:String,age:Int,faceValue:Double)
</code></pre>
<p>通过StructType直接指定Schema编程创建:</p>
<blockquote>
<p>1.创建sparkContext，然后再创建SQLContext<br>
2.先创建RDD，对数据进行整理，然后关联Row，将非结构化数据转换成结构化数据<br>
3.定义schema<br>
4.调用sqlContext的createDataFrame方法<br>
5.注册临时表<br>
6.执行SQL（Transformation，lazy）<br>
7.执行Action</p>
</blockquote>
<pre><code>import org.apache.spark.sql.types._
import org.apache.spark.sql.{Row, SQLContext}
import org.apache.spark.{SparkConf, SparkContext}

object RDDtoDf2 {
  def main(args: Array[String]): Unit = {
    val sparkConf = new SparkConf().setAppName("rddtodf2").setMaster("local")
    val sc = new SparkContext(sparkConf)
    val sqlContecxt = new SQLContext(sc)
    //创建RDD
    val lines = sc.textFile("C:\\Users\\brz\\Desktop\\数据\\person.txt")

    //数据整理，把数据从数组里放到一个对象里
    val personrdd = lines.map(line=&gt;{
      val fields = line.split(",")
      val id = fields(0).toLong
      val name = fields(1)
      val age = fields(2).toInt
      val faceValue = fields(3).toDouble
      Row(id,name,age,faceValue)

    })

    //定义表的结构
    val sch:StructType = StructType(List(
      StructField("id",LongType,true),
      StructField("name",StringType,true),
      StructField("age",IntegerType,true),
      StructField("faceValue",DoubleType,true)
    ))

    //把RDD和schema关联
    val df = sqlContecxt.createDataFrame(personrdd,sch)

    //sql方式基于DataFrame去做数据操作
    df.registerTempTable("t_person")

    val res  = sqlContecxt.sql("select * from t_person")
    res.show()
    sc.stop()

	//不使用SQL的方式，就不用注册临时表了
/*
	//通过datafram提供的API去操作数据(dsl)
    val resDF = df.select("id","name","age","faceValue")
    //排序
    import sqlContecxt.implicits._
    val sortedDF = resDF.orderBy($"name",$"age" asc)
    //结果的展示
    sortedDF.show()

    sc.stop()
/*
  }
}
</code></pre>
<p>2.x版本</p>
<pre><code>import org.apache.spark.sql.types._
import org.apache.spark.sql.{Row, SparkSession, types}

object RDDtoDF3 {
  def main(args: Array[String]): Unit = {
    //spark2.x SQL执行的入口
    val session = SparkSession.builder()
      .appName("RDDtoDF3").master("local[2]").getOrCreate()
    //创建RDD
    val lines = session.sparkContext.textFile("C:\\Users\\brz\\Desktop\\数据\\person.txt")
    //数据整理
    val rowRDD = lines.map(line=&gt;{
      val fields = line.split(",")
      val id = fields(0).toLong
      val name = fields(1)
      val age = fields(2).toInt
      val faceValue = fields(3).toDouble
      Row(id,name,age,faceValue)
    })

    //定义一个schema
    val schema:StructType = StructType(List(
      StructField("id",LongType,true),
      StructField("name",StringType,true),
      StructField("age",IntegerType,true),
      StructField("faceValue",DoubleType,true)
    ))

    //创建DataFrame
    val df = session.createDataFrame(rowRDD,schema)

//    //DataFrame 变 RDD
//    df.rdd
    import session.implicits._
    val res = df.where($"age"&gt;30).orderBy($"name")

    res.show()
    session.stop()
  }
}
</code></pre>
<h3><a id="DataFrame_235"></a>DataFrame常用操作：</h3>
<h4><a id="DSL_236"></a>DSL风格语法</h4>
<pre><code>//查看DataFrame中的内容
res.show

//查看DataFrame部分列中的内容
res.select(res.col("name")).show
res.select(col("name"), col("age")).show
res.select("name").show

//打印DataFrame的Schema信息
res.printSchema

//查询所有的name和age，并将age+1
res.select(col("id"), col("name"), col("age") + 1).show
res.select(res("id"), res("name"), res("age") + 1).show
 

//过滤age大于等于18的
res.filter(col("age") &gt;= 18).show
 
//按年龄进行分组并统计相同年龄的人数
res.groupBy("age").count().show()
</code></pre>
<h4><a id="SQL_259"></a>SQL风格语法</h4>
<p>如果想使用SQL风格的语法，需要将DataFrame注册成表</p>
<pre><code>res.registerTempTable("t_person")

//查询年龄最大的前两名
sqlContext.sql("select * from t_person order by age desc limit 2").show

//显示表的Schema信息
sqlContext.sql("desc t_person").show
</code></pre>
<h3><a id="Dataset_269"></a>Dataset</h3>
<p>Dataset是数据的分布式集合。Dataset是Spark 1.6中添加的一个新接口，它提供了RDDs的优点(强类型，能够使用强大的lambda函数)和Spark SQL的优点优化的执行引擎。可以从JVM对象构造数据集，然后使用它进行操作函数转换(map、flatMap、filter等)。Dataset API在Scala和Java中可用。<br>
Python不支持Dataset API。但是由于Python的动态特性，很多Dataset API的优点已经可用(例如，您可以通过名称自然地访问一行的字段行。columnName)。R的情况类似。</p>
<pre><code>val ds = Seq(1, 2, 3).toDS()
ds.map(_ + 1).collect() // Returns: Array(2, 3, 4)

case class Person(name: String, age: Long)
val ds = Seq(Person("Andy", 32)).toDS()

val path = "examples/src/main/resources/people.json"
val people = sqlContext.read.json(path).as[Person]
</code></pre>
<h4><a id="DatasetRDD_282"></a>Dataset和RDD互操作</h4>
<p>Spark SQL支持通过两种方式将存在的RDD转换为Dataset，转换的过程中需要让Dataset获取RDD中的Schema信息，主要有两种方式，一种是通过反射来获取RDD中的Schema信息。这种方式适合于列名已知的情况下。第二种是通过编程接口的方式将Schema信息应用于RDD，这种方式可以处理那种在运行时才能知道列的方式。</p>
<h3><a id="RDDDataFrameDataSets_285"></a>RDD，DataFrame，DataSets</h3>
<p>在spark中，RDD、DataFrame、Dataset是最常用的数据类型，可以借助API，在 DataFrame、Dataset 、RDDs之间无缝迁移，而且DataFrame、 Dataset是建立在RDD的基础上的。</p>
<h4><a id="_287"></a>对比</h4>
<p><strong>RDD</strong><br>
RDD是Spark建立之初的核心API。RDD是不可变分布式弹性数据集，在Spark集群中可跨节点分区，并提供分布式low-level API来操作RDD，包括transformation和action。<br>
<strong>何时使用RDD？</strong><br>
使用RDD的一般场景：<br>
你需要使用偏底层的transformation和action来控制你的数据集；你的数据集非结构化，比如：流媒体或者文本流；你想使用函数式编程来操作你的数据，而不是用特定领域语言(DSL)表达；你不想加入schema，比如，当通过名字或者列处理(或访问)数据属性不在意列式存储格式；当你可以放弃使用DataFrame和Dataset来优化结构化和半结构化数据集的时候。</p>
<p><strong>DataFrames</strong><br>
与RDD类似，DataFrame是不可变的分布式数据集合，与RDD不同的是，数据按列的方式组织，类似于关系型数据库的一张表。设计的初衷是使得大数据集的处理更简单， DataFrame允许用户在分布式数据集上施加一个结构，是对数据更高级的抽象，提供了具体的API处理分布式数据，同时使得Spark拥有更广泛的用户群。<br>
DataFrame与RDD相同之处，都是不可变分布式弹性数据集。不同之处在于，DataFrame的数据集都是按指定列存储，即结构化数据，类似于传统数据库中的表。DataFrame的设计是为了让大数据处理起来更容易。<br>
DataFrame允许开发者把结构化数据集导入DataFrame，并做了更高层次的抽象；DataFrame提供特定领域的语言(DSL)API来操作你的数据集。<br>
在Spark2.0中，DataFrame API将会和Dataset API合并，统一数据处理API。由于这个统一“有点急”，导致大部分Spark开发者对Dataset的high-level和type-safe API并没有什么概念。</p>
<p><strong>Dataset</strong><br>
从Spark2.0开始，DataSets扮演了两种不同的角色：强类型API和弱类型API，见下表。从概念上来讲，可以把DataFrame 当作一个泛型对象的集合DataSet[Row], Row是一个弱类型JVM 对象。相对应地，如果JVM对象是通过Scala的case class或者Java class来表示的，Dataset是强类型的。</p>
<p>Dataset API的优势：</p>
<p>对于Spark开发者而言，你将从Spark 2.0的DataFrame和Dataset统一的API获得以下好处：<br>
1，静态类型和运行时类型安全 考虑静态类型和运行时类型安全，SQL有很少的限制而Dataset限制很多。例如，Spark SQL查询语句，你直到运行时才能发现语法错误(syntax error)，代价较大。然后DataFrame和Dataset在编译时就可捕捉到错误，节约开发时间和成本。 Dataset API都是lambda函数和JVM typed object，任何typed-parameters不匹配即会在编译阶段报错。因此使用Dataset节约开发时间。<br>
2，High-level抽象以及结构化和半结构化数据集的自定义视图 DataFrame是Dataset[Row]的集合，把结构化数据集视图转换成半结构化数据集。<br>
3，简单易用的API 虽然结构化数据会给Spark程序操作数据集带来挺多限制，但它却引进了丰富的语义和易用的特定领域语言。大部分计算可以被Dataset的high-level API所支持。例如，简单的操作agg，select，avg，map，filter或者groupBy即可访问DeviceIoTData类型的Dataset。 使用特定领域语言API进行计算是非常简单的。<br>
4，性能和优化 使用DataFrame和Dataset API获得空间效率和性能优化的两个原因： 首先：因为DataFrame和Dataset是在Spark SQL 引擎上构建的，它会使用Catalyst优化器来生成优化过的逻辑计划和物理查询计划。 R，Java，Scala或者Python的DataFrame/Dataset API，所有的关系型的查询都运行在相同的代码优化器下，代码优化器带来的的是空间和速度的提升。不同的是Dataset[T]强类型API优化数据引擎任务，而弱类型API DataFrame在交互式分析场景上更快，更合适。其次，Dataset能使用Encoder映射特定类型的JVM 对象到Tungsten内部内存表示。Tungsten的Encoder可以有效<br>
的序列化/反序列化JVM object，生成字节码来提高执行速度。</p>
<p>转换：</p>
<pre><code>import org.apache.spark.sql.SparkSession

object Change {
  def main(args: Array[String]): Unit = {
    //dataset  DataFrame  转  RDD
    val session = SparkSession.builder().appName("transform").master("local[2]").getOrCreate()

    val Rdd = session.read.textFile("C:\\Users\\brz\\Desktop\\数据\\person.txt").rdd

    //RDD 转 DataFrame
    import session.implicits._
    Rdd.map(line=&gt;(line,1)).toDF("column1","column2")

    //RDD转DataSet
//    import session.implicits._

//    val ds = Rdd.map(line=&gt;column(line,1)).toDS()

    //dataset 转 DataFrame
    //import session.implicits._
//    val df = ds.toDF()
    
//    //dataFrame 转 dataSet
//    //import session.implicits._
    
//    val ds2 = df.as[column1]
  }
}
    case class column(col1:String,col2:Int) extends Serializable
    case class column1(col1:String,col2:Int) extends Serializable
</code></pre>
<h3><a id="_345"></a>数据源</h3>
<h4><a id="JDBC_346"></a>JDBC</h4>
<p>Spark SQL可以通过JDBC从关系型数据库中读取数据的方式创建DataFrame，通过对DataFrame一系列的计算后，还可以将数据再写回关系型数据库中。（这里使用的是2.x版本）</p>
<pre><code>import java.util.Properties
import org.apache.spark.sql.SparkSession

object ReadFromMysql {
  def main(args: Array[String]): Unit = {
    val sparkSession = SparkSession.builder().appName("ReadFromMysql").master("local[2]").getOrCreate()

    val res = sparkSession.read.format("jdbc").options(
      Map("url"-&gt;"jdbc:mysql://localhost/mytest?useUnicode=true&amp;characterEncoding=utf8&amp;serverTimezone=GMT%2B8&amp;useSSL=false&amp;allowPublicKeyRetrieval=true",
        "driver"-&gt;"com.mysql.cj.jdbc.Driver",
        "dbtable"-&gt;"student",
        "user"-&gt;"root",
        "password"-&gt; "123456")
      ).load()

    //写数据到数据库
    val props = new Properties()
    props.put("user","root")
    props.put("password","123456")
    props.put("driver","com.mysql.cj.jdbc.Driver")
    res.write.mode("append").jdbc("jdbc:mysql://localhost/mytest?useUnicode=true&amp;characterEncoding=utf8&amp;serverTimezone=GMT%2B8&amp;useSSL=false&amp;allowPublicKeyRetrieval=true","student",props)
    res.printSchema()
    res.show()
  }
}
</code></pre>
<h4><a id="_374"></a>文件输入输出</h4>
<pre><code>import org.apache.spark.sql.{DataFrame, SparkSession}

object FileSource {
  def main(args: Array[String]): Unit = {
    val sparkSession = SparkSession.builder().appName("FileRead").master("local[2]").getOrCreate()
    import sparkSession.implicits._
    //读取json文件
    val df:DataFrame = sparkSession.read.json("C:\\Users\\brz\\Desktop\\数据\\js.txt")
    //val df1 = sparkSession.read.format("json").load("")
    //val df = sparkSession.read.csv("")
    //val df = sparkSession.read.parquet("")

    val res = df.where($"age"&lt;=30).select($"age")
    res.printSchema()
    //写文件
//    res.write.csv("C:\\Users\\brz\\Desktop\\数据\\cjs.txt")
//    res.write.json("")
    res.write.text("C:\\Users\\brz\\Desktop\\数据\\chjs.txt")
    res.show()
  }
}
</code></pre>
<p>sparkSql版的wordCount:</p>
<pre><code>import org.apache.spark.sql.{Dataset, SparkSession}

object sparkSqlWC {
  def main(args: Array[String]): Unit = {

    //创建sparkSession
    val sparkSession = SparkSession.builder().appName("sparkSqlWC").master("local[2]").getOrCreate()
    val lines:Dataset[String] = sparkSession.read.textFile("C:\\Users\\brz\\Desktop\\数据\\person.txt")
    lines.show()

    import sparkSession.implicits._
    val words:Dataset[String] = lines.flatMap(_.split(","))
    words.show()

    //使用sql
    words.createTempView("t_wc")
    val res0 = sparkSession.sql("SELECT value,count(*) counts from t_wc GROUP BY value ORDER BY counts desc")
    res0.show()

    //使用dataset的API
    val res = words.groupBy($"value").count().sort($"count" desc)
    res.show()

    //使用聚合函数
    import org.apache.spark.sql.functions._
    val res1 = words.groupBy($"value" as "word").agg(count("*") as "counts").orderBy($"counts" desc)
    res1.show()
    sparkSession.stop()
  }
}
</code></pre>
<h2><a id="spark_SQL_join_432"></a>spark SQL join深入</h2>
<p>Join是SQL语句中的常用操作，良好的表结构能够将数据分散在不同的表中，使其符合某种范式，减少表冗余、更新容错等。而建立表和表之间关系的最佳方式就是Join操作。<br>
SparkSQL作为大数据领域的SQL实现，自然也对Join操作做了不少优化，今天主要看一下在SparkSQL中对于Join，常见的3种实现。</p>
<p>SparkSQL的3种Join实现:</p>
<h3><a id="Broadcast_Join_437"></a>Broadcast Join</h3>
<p>大家知道，在数据库的常见模型中（比如星型模型或者雪花模型），表一般分为两种：事实表和维度表。维度表一般指固定的、变动较少的表，例如联系人、物品种类等，一般数据有限。而事实表一般记录流水，比如销售清单等，通常随着时间的增长不断膨胀。<br>
因为Join操作是对两个表中key值相同的记录进行连接，在SparkSQL中，对两个表做Join最直接的方式是先根据key分区，再在每个分区中把key值相同的记录拿出来做连接操作。但这样就不可避免地涉及到shuffle，而shuffle在Spark中是比较耗时的操作，我们应该尽可能的设计Spark应用使其避免大量的shuffle.<br>
当维度表和事实表进行Join操作时，为了避免shuffle，我们可以将大小有限的维度表的全部数据分发到每个节点上，供事实表使用。executor存储维度表的全部数据，一定程度上牺牲了空间，换取shuffle操作大量的耗时，这在SparkSQL中称作Broadcast Join，如下图所示：<br>
<img src="https://i.imgur.com/b4ihFnN.png" alt=""><br>
Table B是较小的表，黑色表示将其广播到每个executor节点上，Table A的每个partition会通过block manager取到Table A的数据。根据每条记录的Join Key取到Table B中相对应的记录，根据Join Type进行操作。这个过程比较简单，不做赘述。<br>
Broadcast Join的条件有以下几个：</p>
<ol>
<li>被广播的表需要小于spark.sql.autoBroadcastJoinThreshold所配置的值，默认是10M （或者加了broadcast join的hint）</li>
<li>基表不能被广播，比如left outer join时，只能广播右表</li>
</ol>
<h3><a id="Shuffle_Hash_Join_446"></a>Shuffle Hash Join</h3>
<p>当一侧的表比较小时，我们选择将其广播出去以避免shuffle，提高性能。但因为被广播的表首先被collect到driver段，然后被冗余分发到每个executor上，所以当表比较大时，采用broadcast join会对driver端和executor端造成较大的压力。<br>
但由于Spark是一个分布式的计算引擎，可以通过分区的形式将大批量的数据划分成n份较小的数据集进行并行计算。这种思想应用到Join上便是Shuffle Hash Join了。利用key相同必然分区相同的这个原理，SparkSQL将较大表的join分而治之，先将表划分成n个分区，再对两个表中相对应分区的数据分别进行Hash Join，这样即在一定程度上减少了driver广播一侧表的压力，也减少了executor端取整张被广播表的内存消耗。其原理如下图：<br>
<img src="https://img-blog.csdnimg.cn/20181109211032410.PNG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1MTgwOTgz,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"><br>
Shuffle Hash Join分为两步：</p>
<ol>
<li>对两张表分别按照join keys进行重分区，即shuffle，目的是为了让有相同join keys值的记录分到对应的分区中</li>
<li>对对应分区中的数据进行join，此处先将小表分区构造为一张hash表，然后根据大表分区中记录的join keys值拿出来进行匹配</li>
</ol>
<p>Shuffle Hash Join的条件有以下几个：</p>
<ol>
<li>分区的平均大小不超过spark.sql.auto BroadcastJoinThreshold所配置的值，默认是10M</li>
<li>基表不能被广播，比如left outer join时，只能广播右表</li>
<li>一侧的表要明显小于另外一侧，小的一侧将被广播（明显小于的定义为3倍小，此处为经验值）</li>
<li>我们可以看到，在一定大小的表中，SparkSQL从时空结合的角度来看，将两个表进行重新分区，并且对小表中的分区进行hash化，从而完成join。在保持一定复杂度的基础上，尽量减少driver和executor的内存压力，提升了计算时的稳定性。</li>
</ol>
<h3><a id="Sort_Merge_Join_460"></a>Sort Merge Join</h3>
<p>上面介绍的两种实现对于一定大小的表比较适用，但当两个表都非常大时，显然无论适用哪种都会对计算内存造成很大压力。这是因为join时两者采取的都是hash join，是将一侧的数据完全加载到内存中，使用hash code取joinkeys值相等的记录进行连接。<br>
当两个表都非常大时，SparkSQL采用了一种全新的方案来对表进行Join，即Sort Merge Join。这种实现方式不用将一侧数据全部加载后再进行hash join，但需要在join前将数据排序，如下图所示：<br>
<img src="https://i.imgur.com/EvPYpE0.png" alt=""><br>
可以看到，首先将两张表按照join keys进行了重新shuffle，保证join keys值相同的记录会被分在相应的分区。分区后对每个分区内的数据进行排序，排序后再对相应的分区内的记录进行连接，如下图示：<br>
<img src="https://i.imgur.com/PGOdK4A.png" alt=""><br>
因为两个序列都是有序的，从头遍历，碰到key相同的就输出；如果不同，左边小就继续取左边，反之取右边。<br>
可以看出，无论分区有多大，Sort Merge Join都不用把某一侧的数据全部加载到内存中，而是即用即取即丢，从而大大提升了大数据量下sql join的稳定性。</p>
<p>案例：</p>
<pre><code>import org.apache.spark.sql.{Dataset, SparkSession}
object JoinDemo {
  def main(args: Array[String]): Unit = {
    //创建程序入口
    val sparkSession = SparkSession.builder().appName("JoinDemo").master("local[*]").getOrCreate()
    //通过一个集合去构建一个dataset
    import sparkSession.implicits._
    val lines:Dataset[String] = sparkSession.createDataset(Array("1,hanmeimei,China","2,tom,USA","3,lili,AS"))

    //数据整理
    val tupleDS = lines.map(line =&gt;{
      val fields = line.split(",")
      val id = fields(0).toLong
      val name  = fields(1)
      val country = fields(2)
      (id,name,country)
    })
    val df1 = tupleDS.toDF("id","name","country")

    val countrys:Dataset[String] = sparkSession.createDataset(List("China,中国","USA,美国","AS,俄国"))

    val tupleDS2  = countrys.map(line=&gt;{
      val fields = line.split(",")
      val ename = fields(0)
      val cname = fields(1)
      (ename,cname)
    })
    val df2  = tupleDS2.toDF("ename","cname")

    //创建一个视图
    df1.createTempView("t_user")
    df2.createTempView("t_country")
    val res = sparkSession.sql("select id,name,cname,ename from t_user join t_country on t_user.country = t_country.ename")
    res.show()
  }
}
</code></pre>
<p>-2-</p>
<pre><code>import org.apache.spark.sql.SparkSession

object JoinDemo2 {
  def main(args: Array[String]): Unit = {
    //创建程序入口
    val sparkSession = SparkSession.builder().appName("JoinDemo").master("local[*]").getOrCreate()
    //通过一个集合去构建一个dataset
    import sparkSession.implicits._
    //对表的大小不进行限制
    sparkSession.conf.set("spark.sql.autoBroadcastJoinThreshold",-1)
    sparkSession.conf.set("spark.sql.join.preferSortMergeJoin",true)

    val df1 = Seq(
      (0,"tom"),
      (1,"jeryy"),
      (2,"kate")
    ).toDF("id","name")

    val df2 = Seq(
      (0,10),
      (1,20),
      (2,30)
    ).toDF("aid","age")

    df2.repartition()
    val res = df1.join(df2,$"id" === $"aid")
    res.explain()
    res.show()
    sparkSession.stop()
  }
}
</code></pre>
<h3><a id="UDFUDAF_542"></a>自定义UDF和UDAF</h3>
<p>注意：sparksql 没有udtf 这类函数<br>
UDF：</p>
<pre><code>import org.apache.spark.sql.types.{StringType, StructField, StructType}
import org.apache.spark.sql.{Row, SQLContext, SparkSession}
import org.apache.spark.{SparkConf, SparkContext}
object UDF {
	def main(args: Array[String]): Unit = {
		val ss = SparkSession.builder().appName(this.getClass.getName).master("local").getOrCreate()
		val sc = ss.sparkContext
		// 构造模拟数据
		val names = Array("Leo", "Marry", "Jack", "Tom")
		val namesRDD = sc.parallelize(names, 5)
		val namesRowRDD = namesRDD.map { name =&gt; Row(name) }
		val structType = StructType(Array(StructField("name", StringType, true)))
		val namesDF = ss.createDataFrame(namesRowRDD, structType)
		//注册临时表
		namesDF.createTempView("names")
		//定义和注册自定义函数
		//定义函数：自己写匿名函数
		//注册函数：.udf.register
		ss.udf.register("strLen",(str:String)=&gt;str.length)
		//接下来使用自定义函数
		ss.sql("select name,strLen(name) from names").collect().foreach(println)
	}
}
</code></pre>
<p>UDAF：</p>
<pre><code>import org.apache.spark.sql.expressions.{MutableAggregationBuffer, UserDefinedAggregateFunction}
import org.apache.spark.sql.types._
import org.apache.spark.sql.{Row, SparkSession}
object UDAF {
def main(args: Array[String]): Unit = {
		val ssc = SparkSession.builder().appName(this.getClass.getName).master("local").getOrCreate()
		val sc = ssc.sparkContext
		// 构造模拟数据
		val names = Array("Leo", "Marry", "Jack", "Tom", "Tom", "Tom", "Leo")
		val namesRDD = sc.parallelize(names, 5)
		val namesRowRDD = namesRDD.map { name =&gt; Row(name) }
		val structType = StructType(Array(StructField("name", StringType, true)))
		val namesDF = ssc.createDataFrame(namesRowRDD, structType)
		// 注册一张names表
		namesDF.createTempView("names")
		// 定义和注册自定义函数
		// 定义函数：自己写匿名函数
		// 注册函数：SQLContext.udf.register()
		ssc.udf.register("strCount", new UDAF)
		// 使用自定义函数
		ssc.sql("select name,strCount(name) from names group by name")
		.collect()
		.foreach(println)
	}
}
class UDAF extends UserDefinedAggregateFunction {
	//指的是，输入数据类型
	override def inputSchema: StructType = {
		StructType(Array(StructField("str", StringType, true)))
	}
	//指的是，中间进行聚合时，所处理数据的类型
	override def bufferSchema: StructType = {
		StructType(Array(StructField("count", IntegerType, true)))
	}
	//指的是函数返回值的类型
	override def dataType: DataType = {
		IntegerType
	}
	//数据的统一性，一般是true
	override def deterministic: Boolean = true
	//为每个分组的数据执行初始化操作
	override def initialize(buffer: MutableAggregationBuffer): Unit = {
		buffer(0) = 0
	}
	// 在进行聚合的时候，每当有新的值进来，对分组后的聚合如何进行计算
	// 本地的聚合操作，相当于Hadoop MapReduce模型中的Combiner
	override def update(buffer: MutableAggregationBuffer, input: Row): Unit = {
		buffer(0)=buffer.getAs[Int](0) + 1
	}
	// 由于Spark是分布式的，所以一个分组的数据，可能会在不同的节点上进行局部聚合，就是update
	// 但是，最后一个分组，在各个节点上的聚合值，要进行merge，也就是合并
	override def merge(buffer1: MutableAggregationBuffer, buffer2: Row): Unit = {
		buffer1(0) = buffer1.getAs[Int](0) + buffer2.getAs[Int](0)
	}
	// 最后返回一个最终的聚合值
	override def evaluate(buffer: Row): Any = {
		buffer.getAs[Int](0)
	}
}
</code></pre>
<p>注：使用dataset时需要序列化</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>