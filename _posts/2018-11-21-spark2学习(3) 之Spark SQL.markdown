---
layout:     post
title:      spark2学习(3) 之Spark SQL
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/willwill101/article/details/81591435				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>Spark SQL是用于结构化数据处理的Spark模块。与基本的Spark RDD API不同，Spark SQL提供的接口为Spark提供了有关数据结构和正在执行的计算的更多信息。在内部，Spark SQL使额外的优化。有几种与Spark SQL交互的方法，包括SQL和Dataset API。</p>

<h3>创建sparksession</h3>

<pre>
<code class="language-html hljs">import org.apache.spark.sql.SparkSession

val spark = SparkSession
  .builder()
  .appName("Spark SQL basic example")
  .config("spark.some.config.option", "some-value")
  .getOrCreate()

// For implicit conversions like converting RDDs to DataFrames
import spark.implicits._</code></pre>

<p>sparksql默认支持hive的功能。</p>

<h3>创建一个DataFrame</h3>

<p>可以从RDD，hive，hdfs，来创建DataFrame，读取一个json文件,代码如下：</p>

<p>json文件内容</p>

<p><img alt="" class="has" height="106" src="https://img-blog.csdn.net/20180811233454798?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dpbGx3aWxsMTAx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="472"></p>

<pre>

 </pre>

<p>无类型的DataSet操作就是DataFram操作。</p>

<p><img alt="" class="has" height="673" src="https://img-blog.csdn.net/20180811233709403?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dpbGx3aWxsMTAx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="740"></p>

<p>dataframe操作手册地址：<a href="http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.functions%24" rel="nofollow">http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.functions$</a></p>

<h2 id="running-sql-queries-programmatically" style="margin-left:0px;"><strong><span style="color:#1d1f22;">以编程方式运行SQL查询</span></strong></h2>

<p>这里我们将刚刚的dataframe注册成一个临时表（createOrReplaceTempView），然后使用sql语句执行，使用非常方便</p>

<p><img alt="" class="has" height="266" src="https://img-blog.csdn.net/20180811234335807?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dpbGx3aWxsMTAx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="782"></p>

<h3>全局临时表</h3>

<p>上面的临时表当会话关闭后就不存在了，如果我们想要全局的临时表需要添加createGlobalTempView</p>

<p>就ok了</p>

<p><img alt="" class="has" height="418" src="https://img-blog.csdn.net/20180811235048717?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dpbGx3aWxsMTAx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="664"></p>

<p> </p>

<h2 id="creating-datasets" style="margin-left:0px;">创建dataset</h2>

<p style="margin-left:0px;"><span style="color:#1d1f22;">数据集与RDD类似，但是，它们不使用Java序列化或Kryo，而是使用专门的<a href="http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.Encoder" rel="nofollow">编码器</a>来序列化对象以便通过网络进行处理或传输。虽然编码器和标准序列化都负责将对象转换为字节，但编码器是动态生成的代码，并使用一种格式，</span><span style="color:#f33b45;">允许Spark执行许多操作，如过滤，排序和散列，而无需将字节反序列化为对象。</span></p>

<p style="margin-left:0px;"><img alt="" class="has" height="523" src="https://img-blog.csdn.net/2018081123574257?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dpbGx3aWxsMTAx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="817"></p>

<p style="margin-left:0px;">sparkSQL和RDD的交互方式</p>

<h3 style="margin-left:0px;">第一种就是通过对象的反射，首先将文本格式的对象定义成一个case class 类型，然后执行 toDS，然后再注册临时表就ok了，代码如下：</h3>

<pre>
// For implicit conversions from RDDs to DataFrames
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
// +------------+</pre>

<h3 id="programmatically-specifying-the-schema" style="margin-left:0px;"><strong><span style="color:#1d1f22;">第二种以编程方式指定</span></strong></h3>

<p>如果无法提前定义案例类，<code>DataFrame</code>则可以通过三个步骤以编程方式创建。</p>

<ol style="margin-left:25px;"><li><font face="monospace">为原生的RDD创建Row</font></li>
	<li>创建schema 的<code>StructType</code> 为 Row</li>
	<li>通过SparkSession将schema 和RDD进行绑定</li>
</ol><p>如下：</p>

<pre>
import org.apache.spark.sql.types._

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
// +-------------+</pre>            </div>
                </div>