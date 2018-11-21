---
layout:     post
title:      Spark-SQL官网翻译--Getting Started
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h5><a id="Spark_SQL_DataFrames_and_Datasets_Guide_0"></a>Spark SQL, DataFrames and Datasets Guide</h5>
<p><code>Spark SQL is a Spark module for structured data processing. Unlike the basic Spark RDD API, the interfaces provided by Spark SQL provide Spark with more information about the structure of both the data and the computation being performed. Internally, Spark SQL uses this extra information to perform extra optimizations. There are several ways to interact with Spark SQL including SQL and the Dataset API. When computing a result the same execution engine is used, independent of which API/language you are using to express the computation. This unification means that developers can easily switch back and forth between different APIs based on which provides the most natural way to express a given transformation.</code></p>
<p>Spark SQL是Spark中的一个结构化数据处理模块。与基本Spark RDD API不同，Spark SQL提供的接口为Spark提供了关于数据和正在执行的计算的结构的更多信息。在内部，Spark使用这些额外信息来执行额外的优化。与Sql SQL交互的方法有多种，包括SQL和DataSet API。当计算一个结果时，使用相同的执行引擎，这与您用来表达计算的API/语言无关。这种统一意味着，基于此API提供的表达一个给定转化的方式，开发人员可以轻松地在不同API之间来回切换。</p>
<p><code>All of the examples on this page use sample data included in the Spark distribution and can be run in the spark-shell, pyspark shell, or sparkR shell.</code></p>
<p>本页上的所有示例都使用Spark分布中包含的示例数据，并且可以在spark-shell、pyspark shell或sparkR shell中运行。</p>
<h5><a id="SQL_16"></a>SQL</h5>
<p><code>One use of Spark SQL is to execute SQL queries. Spark SQL can also be used to read data from an existing Hive installation. For more on how to configure this feature, please refer to the Hive Tables section. When running SQL from within another programming language the results will be returned as a Dataset/DataFrame. You can also interact with the SQL interface using the command-line or over JDBC/ODBC.</code></p>
<p>Spark SQL的一个使用是执行SQL查询。Spark SQL也可以用于从现有的Hive安装中读取数据。有关如何配置此功能的更多信息，请参阅Hive表部分。当在另一种编程语言中运行SQL时，结果将返回为DataSet／DataFrame。您还可以使用命令行或通过JDBC/ODBC与SQL界面交互。</p>
<h5><a id="Datasets_and_DataFrames_25"></a>Datasets and DataFrames</h5>
<p><code>A Dataset is a distributed collection of data. Dataset is a new interface added in Spark 1.6 that provides the benefits of RDDs (strong typing, ability to use powerful lambda functions) with the benefits of Spark SQL’s optimized execution engine. A Dataset can be constructed from JVM objects and then manipulated using functional transformations (map, flatMap, filter, etc.). The Dataset API is available in Scala and Java. Python does not have the support for the Dataset API. But due to Python’s dynamic nature, many of the benefits of the Dataset API are already available (i.e. you can access the field of a row by name naturally row.columnName). The case for R is similar.</code></p>
<p>Dataset是分布式的数据集合。Dataset是Spark 1.6中添加的一个新接口，它提供了RDD的优点（强类型、使用强大lambda函数的能力）以及Spark SQL的优化执行引擎的优点。Dataset可以由JVM对象构造，然后使用功能转换map, flatMap, filter等）进行操作。DatasetAPI在Scala和Java中可用。Python不支持DataSet API。但是由于Python的动态特性，Dataset API的许多优点已经可用（即，您可以通过row.columnName按名称访问行的字段，）。R的情况类似。</p>
<h5><a id="Getting_Started_35"></a>Getting Started</h5>
<h6><a id="Starting_Point_SparkSessionSparkSession_38"></a>Starting Point: SparkSession（开始着手的入口：SparkSession）</h6>
<p><code>The entry point into all functionality in Spark is the SparkSession class. To create a basic SparkSession, just use SparkSession.builder():</code></p>
<p>进入Spark的所有功能的入口点是SparkSession类。要创建一个基本的SparkSession，只需使用SparkSession.builder()：</p>
<pre><code>import org.apache.spark.sql.SparkSession

val spark = SparkSession
  .builder()
  .appName("Spark SQL basic example")
  .config("spark.some.config.option", "some-value")
  .getOrCreate()

// For implicit conversions like converting RDDs to DataFrames(为了使用隐式转化)
import spark.implicits._
</code></pre>
<p><code>Find full example code at "examples/src/main/scala/org/apache/spark/examples/sql/SparkSQLExample.scala" in the Spark repo.</code></p>
<p>在"examples/src/main/scala/org/apache/spark/examples/sql/SparkSQLExample.scala"查看完整示例代码。</p>
<p><code>SparkSession in Spark 2.0 provides builtin support for Hive features including the ability to write queries using HiveQL, access to Hive UDFs, and the ability to read data from Hive tables. To use these features, you do not need to have an existing Hive setup.</code></p>
<p>Spark 2.0中SparkSession内置支持Hive特性，包括使用HiveQL编写查询的能力、对Hive UDF的访问以及从Hive表读取数据的能力。要使用这些特性，您不需要安装hive。</p>
<h6><a id="Creating_DataFramesDataFrames_76"></a>Creating DataFrames(创建DataFrames)</h6>
<p><code>With a SparkSession, applications can create DataFrames from an existing RDD, from a Hive table, or from Spark data sources.</code></p>
<p>利用SparkSession，应用可以从一个已经存在的RDD，或一个Hive表或者Spark的数据源中创建DataFrames</p>
<p>As an example, the following creates a DataFrame based on the content of a JSON file:（下面是一个使用Json创建DataFrame的一个例子）</p>
<pre><code>val df = spark.read.json("examples/src/main/resources/people.json")

// Displays the content of the DataFrame to stdout
df.show()
// +----+-------+
// | age|   name|
// +----+-------+
// |null|Michael|
// |  30|   Andy|
// |  19| Justin|
// +----+-------+
</code></pre>
<p><code>Find full example code at "examples/src/main/scala/org/apache/spark/examples/sql/SparkSQLExample.scala" in the Spark repo.</code></p>
<p>在Spark repo的"examples/src/main/scala/org/apache/spark/examples/sql/SparkSQLExample.scala"中获取完整的代码</p>
<h6><a id="Untyped_Dataset_Operations_aka_DataFrame_Operations_109"></a>Untyped Dataset Operations (aka DataFrame Operations)</h6>
<p><code>DataFrames provide a domain-specific language for structured data manipulation in Scala, Java, Python and R.</code></p>
<p>DataFrames为Scala、Java、Python和R伪造的中的结构化数据提供了domain-specific的语言。</p>
<p><code>As mentioned above, in Spark 2.0, DataFrames are just Dataset of Rows in Scala and Java API. These operations are also referred as “untyped transformations” in contrast to “typed transformations” come with strongly typed Scala/Java Datasets.</code></p>
<p>如上所述，在Scale 2.0中，在Scala和JavaAPI中DataFrames只是一行行的Dataset。这些操作也称为“隐式型化转换”，与“类型化转换”相反，转化为强类型的Scala/Java Datasets。</p>
<p><code>Here we include some basic examples of structured data processing using Datasets:</code></p>
<p>这里我们包括一些使用Datasets处理结构化数据的基本例子：</p>
<pre><code>// This import is needed to use the $-notation
import spark.implicits._
// Print the schema in a tree format
df.printSchema()
// root
// |-- age: long (nullable = true)
// |-- name: string (nullable = true)

// Select only the "name" column
df.select("name").show()
// +-------+
// |   name|
// +-------+
// |Michael|
// |   Andy|
// | Justin|
// +-------+

// Select everybody, but increment the age by 1
df.select($"name", $"age" + 1).show()
// +-------+---------+
// |   name|(age + 1)|
// +-------+---------+
// |Michael|     null|
// |   Andy|       31|
// | Justin|       20|
// +-------+---------+

// Select people older than 21
df.filter($"age" &gt; 21).show()
// +---+----+
// |age|name|
// +---+----+
// | 30|Andy|
// +---+----+

// Count people by age
df.groupBy("age").count().show()
// +----+-----+
// | age|count|
// +----+-----+
// |  19|    1|
// |null|    1|
// |  30|    1|
// +----+-----+
</code></pre>
<p><code>Find full example code at "examples/src/main/scala/org/apache/spark/examples/sql/SparkSQLExample.scala" in the Spark repo.</code></p>
<p>在Spark repo的"examples/src/main/scala/org/apache/spark/examples/sql/SparkSQLExample.scala"中获取完整的代码</p>
<p><code>For a complete list of the types of operations that can be performed on a Dataset refer to the API Documentation.</code></p>
<p>查看API文档，找到完整的可以在一个Dataset上进行操作的类型的列表</p>
<p><code>In addition to simple column references and expressions, Datasets also have a rich library of functions including string manipulation, date arithmetic, common math operations and more. The complete list is available in the DataFrame Function Reference.</code></p>
<p>除了简单的列引用和表达式之外，Datasets还具有丰富的函数库，包括字符串操作、日期算术、普通数学运算等。完整列表在可以参考DataFrame函数参考手册。</p>
<h6><a id="Running_SQL_Queries_ProgrammaticallySQL__202"></a>Running SQL Queries Programmatically（以编程方式运行SQL查询 ）</h6>
<p><code>The sql function on a SparkSession enables applications to run SQL queries programmatically and returns the result as a DataFrame.</code></p>
<p>SparkSession上的SQL函数使应用程序能够以编程方式运行SQL查询，并将结果作为DataFrame返回。</p>
<pre><code>// Register the DataFrame as a SQL temporary view
df.createOrReplaceTempView("people")

val sqlDF = spark.sql("SELECT * FROM people")
sqlDF.show()
// +----+-------+
// | age|   name|
// +----+-------+
// |null|Michael|
// |  30|   Andy|
// |  19| Justin|
// +----+-------+
</code></pre>
<p><code>Find full example code at "examples/src/main/scala/org/apache/spark/examples/sql/SparkSQLExample.scala" in the Spark repo.</code></p>
<p>在Spark repo的"examples/src/main/scala/org/apache/spark/examples/sql/SparkSQLExample.scala"中查看完整代码</p>
<h6><a id="Global_Temporary_View__236"></a>Global Temporary View(全局临时视图 )</h6>
<p><code>Temporary views in Spark SQL are session-scoped and will disappear if the session that creates it terminates. If you want to have a temporary view that is shared among all sessions and keep alive until the Spark application terminates, you can create a global temporary view. Global temporary view is tied to a system preserved database global_temp, and we must use the qualified name to refer it, e.g. SELECT * FROM global_temp.view1.</code></p>
<p>Spark SQL中的临时视图是会话范围的，如果创建它的会话终止，则会消失。如果希望有一个临时视图在所有会话之间共享，并在Spark应用程序终止之前保持活动状态，则可以创建一个全局临时视图。全局临时视图绑定到系统保留的数据库global_temp中，我们必须使用限定名称来引用它，例如，SELECT*FROM.global_temp.view1。</p>
<pre><code>// Register the DataFrame as a global temporary view
df.createGlobalTempView("people")

// Global temporary view is tied to a system preserved database `global_temp`
spark.sql("SELECT * FROM global_temp.people").show()
// +----+-------+
// | age|   name|
// +----+-------+
// |null|Michael|
// |  30|   Andy|
// |  19| Justin|
// +----+-------+

// Global temporary view is cross-session
spark.newSession().sql("SELECT * FROM global_temp.people").show()
// +----+-------+
// | age|   name|
// +----+-------+
// |null|Michael|
// |  30|   Andy|
// |  19| Justin|
// +----+-------+
</code></pre>
<p><code>Find full example code at "examples/src/main/scala/org/apache/spark/examples/sql/SparkSQLExample.scala" in the Spark repo.</code></p>
<p>在Spark repo 中的"examples/src/main/scala/org/apache/spark/examples/sql/SparkSQLExample.scala"查看完整代码</p>
<h6><a id="Creating_DatasetsDatasets_278"></a>Creating Datasets（创建Datasets）</h6>
<p><code>Datasets are similar to RDDs, however, instead of using Java serialization or Kryo they use a specialized Encoder to serialize the objects for processing or transmitting over the network. While both encoders and standard serialization are responsible for turning an object into bytes, encoders are code generated dynamically and use a format that allows Spark to perform many operations like filtering, sorting and hashing without deserializing the bytes back into an object.</code></p>
<p>Datasets与RDDS相似，但是，它们不使用Java序列化或Kryo，而是使用专门的编码器来序列化对象以在网络上进行处理或传输。虽然编码器和标准序列化都负责将对象转换成字节，但编码器是动态生成的代码，并使用一种允许Spark执行许多操作（如过滤、排序和哈希）的格式，而不必将字节反序列化为对象。</p>
<pre><code>case class Person(name: String, age: Long)

// Encoders are created for case classes
val caseClassDS = Seq(Person("Andy", 32)).toDS()
caseClassDS.show()
// +----+---+
// |name|age|
// +----+---+
// |Andy| 32|
// +----+---+

// Encoders for most common types are automatically provided by importing spark.implicits._
val primitiveDS = Seq(1, 2, 3).toDS()
primitiveDS.map(_ + 1).collect() // Returns: Array(2, 3, 4)

// DataFrames can be converted to a Dataset by providing a class. Mapping will be done by name
val path = "examples/src/main/resources/people.json"
val peopleDS = spark.read.json(path).as[Person]
peopleDS.show()
// +----+-------+
// | age|   name|
// +----+-------+
// |null|Michael|
// |  30|   Andy|
// |  19| Justin|
// +----+-------+
</code></pre>
<p><code>Find full example code at "examples/src/main/scala/org/apache/spark/examples/sql/SparkSQLExample.scala" in the Spark repo.</code></p>
<p>在Spark repo 中的"examples/src/main/scala/org/apache/spark/examples/sql/SparkSQLExample.scala"查看完整代码</p>
<h6><a id="Interoperating_with_RDDsRDD_323"></a>Interoperating with RDDs（与RDD交互的操作）</h6>
<p><code>Spark SQL supports two different methods for converting existing RDDs into Datasets. The first method uses reflection to infer the schema of an RDD that contains specific types of objects. This reflection-based approach leads to more concise code and works well when you already know the schema while writing your Spark application.</code></p>
<p>Spark SQL支持两种不同方法将现有RDDS转换成Datasets。第一种方法基于反射方式把RDD转化为Datasets。这种基于反射的方法可以使代码更简洁。<br>
这种基于反射的方法使代码更加简洁，在写Spark的应用时已经知道schema的情况下效果很好。</p>
<p>The second method for creating Datasets is through a programmatic interface that allows you to construct a schema and then apply it to an existing RDD. While this method is more verbose, it allows you to construct Datasets when the columns and their types are not known until runtime.</p>
<p>创建Datasets的第二种方法是通过编程接口，它允许您构造一个schema，然后将其应用到现有的RDD。虽然这个方法比较繁琐，但是它允许您在运行之前当列和列的类型都不确定时构建Datasets。</p>
<h6><a id="Inferring_the_Schema_Using_ReflectionSchema_342"></a>Inferring the Schema Using Reflection（使用反射方式推导Schema）</h6>
<p><code>The Scala interface for Spark SQL supports automatically converting an RDD containing case classes to a DataFrame. The case class defines the schema of the table. The names of the arguments to the case class are read using reflection and become the names of the columns. Case classes can also be nested or contain complex types such as Seqs or Arrays. This RDD can be implicitly converted to a DataFrame and then be registered as a table. Tables can be used in subsequent SQL statements.</code></p>
<p>Scala SQL的Scala接口支持将包case classes(样例类)的RDD自动转换为DataFrame。case classes（样例类）定义表的结构。通过反射来读取case class（样例类）的参数的名称，并成为列的名称。case classes（样例类）也可以嵌套或包含复杂的类型，如Seqs（队列）或数组。这些RDD可以被隐式转换为DataFrame，然后将其注册为表，用于后续SQL语句。</p>
<pre><code>// For implicit conversions from RDDs to DataFrames
import spark.implicits._

// Create an RDD of Person objects from a text file, convert it to a Dataframe
val peopleDF = spark.sparkContext
  .textFile("examples/src/main/resources/people.txt")
  .map(_.split(","))
  .map(attributes =&gt; Person(attributes(0), attributes(1).trim.toInt))
  .toDF()
// Register the DataFrame as a temporary view
peopleDF.createOrReplaceTempView("people")

// SQL statements can be run by using the sql methods provided by Spark
val teenagersDF = spark.sql("SELECT name, age FROM people WHERE age BETWEEN 13 AND 19")

// The columns of a row in the result can be accessed by field index
teenagersDF.map(teenager =&gt; "Name: " + teenager(0)).show()
// +------------+
// |       value|
// +------------+
// |Name: Justin|
// +------------+

// or by field name
teenagersDF.map(teenager =&gt; "Name: " + teenager.getAs[String]("name")).show()
// +------------+
// |       value|
// +------------+
// |Name: Justin|
// +------------+

// No pre-defined encoders for Dataset[Map[K,V]], define explicitly
implicit val mapEncoder = org.apache.spark.sql.Encoders.kryo[Map[String, Any]]
// Primitive types and case classes can be also defined as
// implicit val stringIntMapEncoder: Encoder[Map[String, Any]] = ExpressionEncoder()

// row.getValuesMap[T] retrieves multiple columns at once into a Map[String, T]
teenagersDF.map(teenager =&gt; teenager.getValuesMap[Any](List("name", "age"))).collect()
// Array(Map("name" -&gt; "Justin", "age" -&gt; 19))
</code></pre>
<p><code>Find full example code at "examples/src/main/scala/org/apache/spark/examples/sql/SparkSQLExample.scala" in the Spark repo.</code></p>
<p>在Spark repo 中的"examples/src/main/scala/org/apache/spark/examples/sql/SparkSQLExample.scala"查看完整代码</p>
<h6><a id="Programmatically_Specifying_the_Schema_401"></a>Programmatically Specifying the Schema（编程接口方式指定表结构）</h6>
<p><code>When case classes cannot be defined ahead of time (for example, the structure of records is encoded in a string, or a text dataset will be parsed and fields will be projected differently for different users), a DataFrame can be created programmatically with three steps.</code></p>
<p>当无法提前定义 case classes(样例类)（例如，记录结构被编码在字符串中，或传递的是文本数据集，并且字段将针对不同的用户进行不同的投影）时，可以通过三个步骤以编程方式创建DataFrame。</p>
<ol>
<li>Create an RDD of Rows from the original RDD;（从最初的RDD上创建Row）</li>
<li>Create the schema represented by a StructType matching the structure of Rows in the RDD created in Step 1.（创建一个和步骤一中创建的Row结构相同的StructType来代表表的结构）</li>
<li>Apply the schema to the RDD of Rows via createDataFrame method provided by SparkSession. （通过SparkSession的createDataFrame方法将步骤二中创建的表结构应用到Rows上）</li>
</ol>
<p>For example:（例如:）</p>
<pre><code>import org.apache.spark.sql.types._

// Create an RDD
val peopleRDD = spark.sparkContext.textFile("examples/src/main/resources/people.txt")

// The schema is encoded in a string
val schemaString = "name age"

// Generate the schema based on the string of schema
val fields = schemaString.split(" ")
  .map(fieldName =&gt; StructField(fieldName, StringType, nullable = true))
val schema = StructType(fields)

// Convert records of the RDD (people) to Rows
val rowRDD = peopleRDD
  .map(_.split(","))
  .map(attributes =&gt; Row(attributes(0), attributes(1).trim))

// Apply the schema to the RDD
val peopleDF = spark.createDataFrame(rowRDD, schema)

// Creates a temporary view using the DataFrame
peopleDF.createOrReplaceTempView("people")

// SQL can be run over a temporary view created using DataFrames
val results = spark.sql("SELECT name FROM people")

// The results of SQL queries are DataFrames and support all the normal RDD operations
// The columns of a row in the result can be accessed by field index or by field name
results.map(attributes =&gt; "Name: " + attributes(0)).show()
// +-------------+
// |        value|
// +-------------+
// |Name: Michael|
// |   Name: Andy|
// | Name: Justin|
// +-------------+
</code></pre>
<p><code>Find full example code at "examples/src/main/scala/org/apache/spark/examples/sql/SparkSQLExample.scala" in the Spark repo.</code></p>
<p>在Spark repo的"examples/src/main/scala/org/apache/spark/examples/sql/SparkSQLExample.scala"中查看完整代码</p>
<h6><a id="Aggregations_464"></a>Aggregations（聚合）</h6>
<p><code>The built-in DataFrames functions provide common aggregations such as count(), countDistinct(), avg(), max(), min(), etc. While those functions are designed for DataFrames, Spark SQL also has type-safe versions for some of them in Scala and Java to work with strongly typed Datasets. Moreover, users are not limited to the predefined aggregate functions and can create their own.</code></p>
<p>DataFrames内置了常见的聚合的函数，如count(), countDistinct(), avg(), max(), min()（等等），而这些函数是为DataFrames设计的， Spark SQL 在Scala和Java中也提供一些类型安全的版本，可以与强类型数据集一起工作。此外，不限制预定义的聚合函数，用户可以创建自己的聚合函数。</p>
<h6><a id="Untyped_UserDefined_Aggregate_Functions_473"></a>Untyped User-Defined Aggregate Functions（用户定义的隐式聚合函数）</h6>
<p><code>Users have to extend the UserDefinedAggregateFunction abstract class to implement a custom untyped aggregate function. For example, a user-defined average can look like:</code></p>
<p>用户需要继承UserDefinedAggregateFunction这个抽象类，来实现自定义的隐式转化函数。例如：一个用户自定义隐式转化函数看起来像：</p>
<pre><code>import org.apache.spark.sql.{Row, SparkSession}
import org.apache.spark.sql.expressions.MutableAggregationBuffer
import org.apache.spark.sql.expressions.UserDefinedAggregateFunction
import org.apache.spark.sql.types._

object MyAverage extends UserDefinedAggregateFunction {
  // Data types of input arguments of this aggregate function
  def inputSchema: StructType = StructType(StructField("inputColumn", LongType) :: Nil)
  // Data types of values in the aggregation buffer
  def bufferSchema: StructType = {
    StructType(StructField("sum", LongType) :: StructField("count", LongType) :: Nil)
  }
  // The data type of the returned value
  def dataType: DataType = DoubleType
  // Whether this function always returns the same output on the identical input
  def deterministic: Boolean = true
  // Initializes the given aggregation buffer. The buffer itself is a `Row` that in addition to
  // standard methods like retrieving a value at an index (e.g., get(), getBoolean()), provides
  // the opportunity to update its values. Note that arrays and maps inside the buffer are still
  // immutable.
  def initialize(buffer: MutableAggregationBuffer): Unit = {
    buffer(0) = 0L
    buffer(1) = 0L
  }
  // Updates the given aggregation buffer `buffer` with new input data from `input`
  def update(buffer: MutableAggregationBuffer, input: Row): Unit = {
    if (!input.isNullAt(0)) {
      buffer(0) = buffer.getLong(0) + input.getLong(0)
      buffer(1) = buffer.getLong(1) + 1
    }
  }
  // Merges two aggregation buffers and stores the updated buffer values back to `buffer1`
  def merge(buffer1: MutableAggregationBuffer, buffer2: Row): Unit = {
    buffer1(0) = buffer1.getLong(0) + buffer2.getLong(0)
    buffer1(1) = buffer1.getLong(1) + buffer2.getLong(1)
  }
  // Calculates the final result
  def evaluate(buffer: Row): Double = buffer.getLong(0).toDouble / buffer.getLong(1)
}

// Register the function to access it
spark.udf.register("myAverage", MyAverage)

val df = spark.read.json("examples/src/main/resources/employees.json")
df.createOrReplaceTempView("employees")
df.show()
// +-------+------+
// |   name|salary|
// +-------+------+
// |Michael|  3000|
// |   Andy|  4500|
// | Justin|  3500|
// |  Berta|  4000|
// +-------+------+

val result = spark.sql("SELECT myAverage(salary) as average_salary FROM employees")
result.show()
// +--------------+
// |average_salary|
// +--------------+
// |        3750.0|
// +--------------+
</code></pre>
<p><code>Find full example code at "examples/src/main/scala/org/apache/spark/examples/sql/UserDefinedUntypedAggregation.scala" in the Spark repo.</code></p>
<p>在Spark repo 中的"examples/src/main/scala/org/apache/spark/examples/sql/UserDefinedUntypedAggregation.scala"中查看完整代码</p>
<h6><a id="TypeSafe_UserDefined_Aggregate_Functions_553"></a>Type-Safe User-Defined Aggregate Functions（类型安全的自定义聚合函数）</h6>
<p><code>User-defined aggregations for strongly typed Datasets revolve around the Aggregator abstract class. For example, a type-safe user-defined average can look like:</code></p>
<p>继承Aggregator这个抽象类，来实现自定义的类型安全的自定义聚合函数，一个类型安全的聚合函数看起来像：</p>
<pre><code>import org.apache.spark.sql.{Encoder, Encoders, SparkSession}
import org.apache.spark.sql.expressions.Aggregator

case class Employee(name: String, salary: Long)
case class Average(var sum: Long, var count: Long)

object MyAverage extends Aggregator[Employee, Average, Double] {
  // A zero value for this aggregation. Should satisfy the property that any b + zero = b
  def zero: Average = Average(0L, 0L)
  // Combine two values to produce a new value. For performance, the function may modify `buffer`
  // and return it instead of constructing a new object
  def reduce(buffer: Average, employee: Employee): Average = {
    buffer.sum += employee.salary
    buffer.count += 1
    buffer
  }
  // Merge two intermediate values
  def merge(b1: Average, b2: Average): Average = {
    b1.sum += b2.sum
    b1.count += b2.count
    b1
  }
  // Transform the output of the reduction
  def finish(reduction: Average): Double = reduction.sum.toDouble / reduction.count
  // Specifies the Encoder for the intermediate value type
  def bufferEncoder: Encoder[Average] = Encoders.product
  // Specifies the Encoder for the final output value type
  def outputEncoder: Encoder[Double] = Encoders.scalaDouble
}

val ds = spark.read.json("examples/src/main/resources/employees.json").as[Employee]
ds.show()
// +-------+------+
// |   name|salary|
// +-------+------+
// |Michael|  3000|
// |   Andy|  4500|
// | Justin|  3500|
// |  Berta|  4000|
// +-------+------+

// Convert the function to a `TypedColumn` and give it a name
val averageSalary = MyAverage.toColumn.name("average_salary")
val result = ds.select(averageSalary)
result.show()
// +--------------+
// |average_salary|
// +--------------+
// |        3750.0|
// +--------------+
</code></pre>
<p><code>Find full example code at "examples/src/main/scala/org/apache/spark/examples/sql/UserDefinedTypedAggregation.scala" in the Spark repo.</code></p>
<p>在Spark repo的"examples/src/main/scala/org/apache/spark/examples/sql/UserDefinedTypedAggregation.scala"中查看完整代码</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>