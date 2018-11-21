---
layout:     post
title:      SparkSql ----DataFrame
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>Spark SQL是spark用来处理结构化数据的</p>

<p>Spark中所有功能的入口点都是<a href="http://spark.apache.org/docs/2.1.1/api/scala/index.html#org.apache.spark.sql.SparkSession" rel="nofollow"><code>SparkSession</code></a>类。要创建基本的<code>SparkSession</code>，只需使用<code>SparkSession.builder()</code>：</p>

<pre style="margin-left:0px;">
<span style="color:#333333;"><span style="color:#007020;"><strong>import </strong></span> <span style="color:#0e84b5;"><strong>org.apache.spark.sql.SparkSession</strong></span>

<span style="color:#007020;"><strong>val </strong></span> spark  <span style="color:#007020;"><strong>= </strong></span> <span style="color:#0e84b5;"><strong>SparkSession </strong></span>
  <span style="color:#666666;">。</span>builder <span style="color:#666666;">（）</span>
  <span style="color:#666666;">。</span>appName <span style="color:#666666;">（</span><span style="color:#4070a0;">“Spark SQL基本示例” </span><span style="color:#666666;">）</span>
  <span style="color:#666666;">。</span>config <span style="color:#666666;">（</span><span style="color:#4070a0;">“spark.some.config.option” </span><span style="color:#666666;">，</span> <span style="color:#4070a0;">“some-value” </span><span style="color:#666666;">）</span>
  <span style="color:#666666;">。</span>getOrCreate <span style="color:#666666;">（）</span>

<span style="color:#60a0b0;"><em>//对于将RDD转换为DataFrames等隐式转换，请</em></span>
<span style="color:#007020;"><strong>导入</strong></span> <span style="color:#0e84b5;"><strong>spark.implicits._</strong></span></span>
</pre>

<p> </p>

<p>①Creating DataFrames：</p>

<p style="margin-left:0px;"><span style="color:#1d1f22;">使用 <code>SparkSession</code>，应用程序可以从<a href="http://spark.apache.org/docs/2.1.1/sql-programming-guide.html#interoperating-with-rdds" rel="nofollow">现有的<code>RDD</code></a>，Hive表或<a href="http://spark.apache.org/docs/2.1.1/sql-programming-guide.html#data-sources" rel="nofollow">Spark数据源</a>创建DataFrame 。</span></p>

<p style="margin-left:0px;"><span style="color:#1d1f22;">作为示例，以下内容基于JSON文件的内容创建DataFrame：</span></p>

<p style="margin-left:0px;">val df = spark.read.json("examples/src/main/resources/people.json")</p>

<pre>
df.show()
// +----+-------+
// | age|   name|
// +----+-------+
// |null|Michael|
// |  30|   Andy|
// |  19| Justin|
// +----+-------+</pre>

<p>②创建好DataFrame的操作，接下来是关于DataFrame的操作：</p>

<pre>
// This import is needed to use the $-notation
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
// +----+-----+</pre>

<p>③Sql方式运行</p>

<pre>
// 将DataFrame注册为一个临时表
df.createOrReplaceTempView("people")
//执行sql，返回DataFrame的类型数据
val sqlDF = spark.sql("SELECT * FROM people")
sqlDF.show()
// +----+-------+
// | age|   name|
// +----+-------+
// |null|Michael|
// |  30|   Andy|
// |  19| Justin|
// +----+-------+</pre>

<p>Spark SQL中的临时视图是会话范围的，如果创建它的会话终止，它将消失。如果您希望拥有一个在所有会话之间共享的临时视图并保持活动状态，直到Spark应用程序终止，您可以创建一个全局临时视图。全局临时视图与系统保留的数据库绑定<code>global_temp</code>，我们必须使用限定名称来引用它，例如<code>SELECT * FROM global_temp.view1</code>。</p>

<pre>
// Register the DataFrame as a global temporary view
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
// +----+-------+</pre>

<p>注：  <strong>临时表在查询的时候可以不用写库，全局表必须在之前加上global_temp.view1</strong></p>

<p> </p>

<p> </p>            </div>
                </div>