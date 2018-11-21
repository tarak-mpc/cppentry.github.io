---
layout:     post
title:      spark-shell 基础操作（持续更新）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/jiede1/article/details/77936718				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><strong>1.概述</strong> <br>
Spark SQL 是 Spark 处理结构化数据的一个模块。与基础的 Spark RDD API 不同，Spark SQL 提供了<strong>查询结构化数据</strong>及<strong>计算结果</strong>等信息的接口。在内部，Spark SQL 使用这个额外的信息去执行额外的优化。有几种方式可以跟 Spark SQL 进行交互，包括 SQL 和 Dataset API。当使用相同执行引擎进行计算时，无论使用哪种 API / 语言都可以快速的计算。这种统一意味着开发人员能够在基于提供最自然的方式来表达一个给定的 transformation API 之间实现轻松的来回切换不同的 。</p>

<p><strong>2.SQL</strong>  <br>
Spark SQL 的功能之一是执行 SQL 查询。Spark SQL 也能够被用于从已存在的 Hive 环境中读取数据，不过需要进行hive配置（关于这部分不再我们讨论范围内，请自行百度）。Spark SQL也可以直接从本地读取文件。</p>

<p><strong>3.Datasets 和 DataFrames</strong> <br>
一个 Dataset 是一个分布式的数据集合。Dataset 是在 Spark 1.6 中被添加的新接口，它提供了 RDD 的优点（强类型化，能够使用强大的 lambda 函数）与 Spark SQL 优化的执行引擎的好处。一个 Dataset 可以从 JVM 对象来构造并且使用转换功能（map，flatMap，filter，等等）。Dataset API 在 Scala 和 Java 中是可用的。Python 不支持 Dataset API。但是由于 Python 的动态特性，许多 Dataset API 的有点已经可用了（也就是说，你可能通过 name 天生的 row.columnName 属性访问一行中的字段）。这种情况和 R 相似。 <br>
<strong>一个 DataFrame 是一个 Dataset 组织成的指定列</strong>。它的概念与一个在关系型数据库或者在 R/Python 中的表是相等的，但是有更多的优化。DataFrame 可以从大量的 Source 中构造出来，像 : 结构化的数据文件，Hive 中的表，外部的数据库，或者已存在的 RDD。DataFrame API 在 Scala，Java，Python 和 R 中是可用的。<strong>在 Scala 和 Java 中，一个 DataFrame 所代表的是一个多个 Row（行）的 Dataset。</strong>在 Scala API 中，DataFrame 仅仅是一个 Dataset[Row] 类型的别名 。然而，在 Java API 中，用户需要去使用 Dataset 来表示 DataFrame。</p>

<p><strong>4.SparkSession-Spark的一个全新的切入点</strong> <br>
　　在Spark的早期版本，<strong>sparkContext是进入Spark的切入点</strong>。我们都知道RDD是Spark中重要的API，然而它的创建和操作得使用sparkContext提供的API；对于RDD之外的其他东西，我们需要使用其他的Context。比如对于流处理来说，我们得使用StreamingContext；对于SQL得使用sqlContext；而对于hive得使用HiveContext。然而DataSet和Dataframe提供的API逐渐称为新的标准API，我们需要一个切入点来构建它们，所以在 Spark 2.0中我们引入了一个新的切入点(entry point)：SparkSession</p>

<p>　　<strong>SparkSession实质上是SQLContext和HiveContext的组合（未来可能还会加上StreamingContext）</strong>，所以在SQLContext和HiveContext上可用的API在SparkSession上同样是可以使用的。SparkSession内部封装了sparkContext，所以计算实际上是由sparkContext完成的。</p>

<p><strong>5.进入Sparksession</strong></p>

<pre class="prettyprint"><code class=" hljs avrasm">import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.sql</span><span class="hljs-preprocessor">.SparkSession</span>
val spark = SparkSession
  <span class="hljs-preprocessor">.builder</span>()
  <span class="hljs-preprocessor">.appName</span>(<span class="hljs-string">"Spark SQL Example"</span>)
  <span class="hljs-preprocessor">.config</span>(<span class="hljs-string">"spark.some.config.option"</span>, <span class="hljs-string">"some-value"</span>)
  <span class="hljs-preprocessor">.getOrCreate</span>()

// For implicit conversions like converting RDDs to DataFrames
import spark<span class="hljs-preprocessor">.implicits</span>._</code></pre>

<blockquote>
  <p>实际上spark-shell本身已经开启了sparkSession，命名为spark，我们可以直接使用</p>
</blockquote>

<p><strong>6.创建 DataFrames</strong> <br>
与一个 SparkSession 一起，应用程序可以从一个 已存在的 RDD，或者一个 Hive 表中，或者从 Spark 数据源 中创建 DataFrame。 <br>
举个例子，下面基于一个 JSON 文件的内容创建一个 DataFrame : </p>

<pre class="prettyprint"><code class=" hljs fsharp"><span class="hljs-keyword">val</span> df = spark.read.json(<span class="hljs-string">"examples/src/main/resources/people.json"</span>)

<span class="hljs-comment">// Displays the content of the DataFrame to stdout</span>
df.show()
<span class="hljs-comment">// +----+-------+</span>
<span class="hljs-comment">// | age|   name|</span>
<span class="hljs-comment">// +----+-------+</span>
<span class="hljs-comment">// |null|Michael|</span>
<span class="hljs-comment">// |  30|   Andy|</span>
<span class="hljs-comment">// |  19| Justin|</span>
<span class="hljs-comment">// +----+-------+</span>

<span class="hljs-comment">// 所有的示例代码可以在 Spark repo 的 “examples/src/main/scala/org/apache/spark/examples/sql/SparkSQLExample.scala” 中找到。</span></code></pre>

<p><strong>7.无类型 Dataset 操作（aka DataFrame 操作）</strong> <br>
DataFrame 提供了一个 DSL（domain-specific language）用于在 Scala，Java，Python 或者 R 中的结构化数据操作。 <br>
正如上面提到的一样，在Spark 2.0 ，Scala 和 JavaAPI 中的 DataFrame 仅仅是多个 Row（行）的 Dataset 。这些操作也参考了与强类型的 Scala/Java Datasets 的 “类型转换” 相对应的 “无类型转换”。 <br>
这里包括一些使用 Dataset 进行结构化数据处理的示例 : </p>

<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-comment">// This import is needed to use the $-notation</span>
<span class="hljs-keyword">import</span> spark<span class="hljs-built_in">.</span>implicits<span class="hljs-built_in">.</span>_
<span class="hljs-comment">// Print the schema in a tree format</span>
df<span class="hljs-built_in">.</span>printSchema()
<span class="hljs-comment">// root</span>
<span class="hljs-comment">// |-- age: long (nullable = true)</span>
<span class="hljs-comment">// |-- name: string (nullable = true)</span>

<span class="hljs-comment">// Select only the "name" column</span>
df<span class="hljs-built_in">.</span><span class="hljs-keyword">select</span>(<span class="hljs-string">"name"</span>)<span class="hljs-built_in">.</span>show()
<span class="hljs-comment">// +-------+</span>
<span class="hljs-comment">// |   name|</span>
<span class="hljs-comment">// +-------+</span>
<span class="hljs-comment">// |Michael|</span>
<span class="hljs-comment">// |   Andy|</span>
<span class="hljs-comment">// | Justin|</span>
<span class="hljs-comment">// +-------+</span>

<span class="hljs-comment">// Select everybody, but increment the age by 1</span>
df<span class="hljs-built_in">.</span><span class="hljs-keyword">select</span>($<span class="hljs-string">"name"</span>, $<span class="hljs-string">"age"</span> <span class="hljs-subst">+</span> <span class="hljs-number">1</span>)<span class="hljs-built_in">.</span>show()
<span class="hljs-comment">// +-------+---------+</span>
<span class="hljs-comment">// |   name|(age + 1)|</span>
<span class="hljs-comment">// +-------+---------+</span>
<span class="hljs-comment">// |Michael|     null|</span>
<span class="hljs-comment">// |   Andy|       31|</span>
<span class="hljs-comment">// | Justin|       20|</span>
<span class="hljs-comment">// +-------+---------+</span>

<span class="hljs-comment">// Select people older than 21</span>
df<span class="hljs-built_in">.</span>filter($<span class="hljs-string">"age"</span> <span class="hljs-subst">&gt;</span> <span class="hljs-number">21</span>)<span class="hljs-built_in">.</span>show()
<span class="hljs-comment">// +---+----+</span>
<span class="hljs-comment">// |age|name|</span>
<span class="hljs-comment">// +---+----+</span>
<span class="hljs-comment">// | 30|Andy|</span>
<span class="hljs-comment">// +---+----+</span>

<span class="hljs-comment">// Count people by age</span>
df<span class="hljs-built_in">.</span>groupBy(<span class="hljs-string">"age"</span>)<span class="hljs-built_in">.</span>count()<span class="hljs-built_in">.</span>show()
<span class="hljs-comment">// +----+-----+</span>
<span class="hljs-comment">// | age|count|</span>
<span class="hljs-comment">// +----+-----+</span>
<span class="hljs-comment">// |  19|    1|</span>
<span class="hljs-comment">// |null|    1|</span>
<span class="hljs-comment">// |  30|    1|</span>
<span class="hljs-comment">// +----+-----+</span>

<span class="hljs-comment">// 所有的示例代码可以在 Spark repo 的 “examples/src/main/scala/org/apache/spark/examples/sql/SparkSQLExample.scala” 中找到。</span></code></pre>

<p><strong>8.以编程的方式运行 SQL 查询</strong></p>

<pre class="prettyprint"><code class=" hljs fsharp"><span class="hljs-comment">// SparkSession 使应用程序的 SQL 函数能够以编程的方式运行 SQL 查询并且将查询结果以一个 DataFrame。</span>

<span class="hljs-comment">// Register the DataFrame as a SQL temporary view</span>
df.createOrReplaceTempView(<span class="hljs-string">"people"</span>)

<span class="hljs-keyword">val</span> sqlDF = spark.sql(<span class="hljs-string">"SELECT * FROM people"</span>)
sqlDF.show()
<span class="hljs-comment">// +----+-------+</span>
<span class="hljs-comment">// | age|   name|</span>
<span class="hljs-comment">// +----+-------+</span>
<span class="hljs-comment">// |null|Michael|</span>
<span class="hljs-comment">// |  30|   Andy|</span>
<span class="hljs-comment">// |  19| Justin|</span>
<span class="hljs-comment">// +----+-------+</span>

<span class="hljs-comment">// 所有的示例代码可以在 Spark repo 的 “examples/src/main/scala/org/apache/spark/examples/sql/SparkSQLExample.scala” 中找到。</span></code></pre>

<p><strong>9.创建 Datasets</strong> <br>
Dataset 与 RDD 相似，然而，并不是使用 Java 序列化或者 Kryo，他们使用一个指定的 Encoder（编码器） 来序列化用于处理或者通过网络进行传输的对象。虽然编码器和标准的序列化都负责将一个对象序列化成字节，编码器是动态生成的代码，并且使用了一种允许 Spark 去执行许多像 filtering，sorting 以及 hashing 这样的操作，不需要将字节反序列化成对象的格式。</p>

<pre class="prettyprint"><code class=" hljs scala"><span class="hljs-comment">// Note: Case classes in Scala 2.10 can support only up to 22 fields. To work around this limit,</span>
<span class="hljs-comment">// you can use custom classes that implement the Product interface</span>
<span class="hljs-class"><span class="hljs-keyword">case</span> <span class="hljs-keyword">class</span> <span class="hljs-title">Person</span><span class="hljs-params">(name: String, age: Long)</span></span>

<span class="hljs-comment">// Encoders are created for case classes</span>
<span class="hljs-keyword">val</span> caseClassDS = Seq(Person(<span class="hljs-string">"Andy"</span>, <span class="hljs-number">32</span>)).toDS()
caseClassDS.show()
<span class="hljs-comment">// +----+---+</span>
<span class="hljs-comment">// |name|age|</span>
<span class="hljs-comment">// +----+---+</span>
<span class="hljs-comment">// |Andy| 32|</span>
<span class="hljs-comment">// +----+---+</span>

<span class="hljs-comment">// Encoders for most common types are automatically provided by importing spark.implicits._</span>
<span class="hljs-keyword">val</span> primitiveDS = Seq(<span class="hljs-number">1</span>, <span class="hljs-number">2</span>, <span class="hljs-number">3</span>).toDS()
primitiveDS.map(_ + <span class="hljs-number">1</span>).collect() <span class="hljs-comment">// Returns: Array(2, 3, 4)</span>

<span class="hljs-comment">// DataFrames can be converted to a Dataset by providing a class. Mapping will be done by name</span>
<span class="hljs-keyword">val</span> path = <span class="hljs-string">"examples/src/main/resources/people.json"</span>
<span class="hljs-keyword">val</span> peopleDS = spark.read.json(path).as[Person]
peopleDS.show()
<span class="hljs-comment">// +----+-------+</span>
<span class="hljs-comment">// | age|   name|</span>
<span class="hljs-comment">// +----+-------+</span>
<span class="hljs-comment">// |null|Michael|</span>
<span class="hljs-comment">// |  30|   Andy|</span>
<span class="hljs-comment">// |  19| Justin|</span>
<span class="hljs-comment">// +----+-------+</span>

<span class="hljs-comment">// 所有的示例代码可以在 Spark repo 的 “examples/src/main/scala/org/apache/spark/examples/sql/SparkSQLExample.scala” 中找到。</span></code></pre>

<p><strong>10.数据源（读写操作）</strong> <br>
spark SQL 支持通过 DataFrame 接口对各种 data sources （数据源）进行操作. DataFrame 可以使用 relational transformations （关系转换）操作, 也可用于创建 temporary view （临时视图）. 将 DataFrame 注册为 temporary view （临时视图）允许您对其数据运行 SQL 查询. 本节 描述了使用 Spark Data Sources 加载和保存数据的一般方法, 然后涉及可用于 built-in data sources （内置数据源）的 specific options （特定选项）.</p>

<p><strong>11.通用的 Load/Save 函数</strong> <br>
在最简单的方式下，默认的数据源（parquet 除非另外配置通过spark.sql.sources.default）将会用于所有的操作。</p>

<pre class="prettyprint"><code class=" hljs avrasm">val usersDF = spark<span class="hljs-preprocessor">.read</span><span class="hljs-preprocessor">.load</span>(<span class="hljs-string">"examples/src/main/resources/users.parquet"</span>)
usersDF<span class="hljs-preprocessor">.select</span>(<span class="hljs-string">"name"</span>, <span class="hljs-string">"favorite_color"</span>)<span class="hljs-preprocessor">.write</span><span class="hljs-preprocessor">.save</span>(<span class="hljs-string">"namesAndFavColors.parquet"</span>)</code></pre>

<p>或者</p>



<pre class="prettyprint"><code class=" hljs ocaml"><span class="hljs-keyword">val</span> df = sparkSession.read.<span class="hljs-built_in">option</span>(<span class="hljs-string">"header"</span>,<span class="hljs-string">"true"</span>).  
    csv(<span class="hljs-string">"examples/src/main/resources/sales.csv"</span>)  </code></pre>

<p><strong>12.手动指定选项</strong> <br>
你也可以手动的指定数据源，并且将与你想要传递给数据源的任何额外选项一起使用。数据源由其完全限定名指定（例如 : org.apache.spark.sql.parquet），不过对于内置数据源你也可以使用它们的缩写名（json, parquet, jdbc）。使用下面这个语法可以将从任意类型数据源加载的DataFrames 转换为其他类型。</p>

<pre class="prettyprint"><code class=" hljs avrasm">val peopleDF = spark<span class="hljs-preprocessor">.read</span><span class="hljs-preprocessor">.format</span>(<span class="hljs-string">"json"</span>)<span class="hljs-preprocessor">.load</span>(<span class="hljs-string">"examples/src/main/resources/people.json"</span>)
peopleDF<span class="hljs-preprocessor">.select</span>(<span class="hljs-string">"name"</span>,<span class="hljs-string">"age"</span>)<span class="hljs-preprocessor">.write</span><span class="hljs-preprocessor">.format</span>(<span class="hljs-string">"parquet"</span>)<span class="hljs-preprocessor">.save</span>(<span class="hljs-string">"namesAndAges.parquet"</span>)</code></pre>

<p>完整的示例代码在  <br>
“examples/src/main/scala/org/apache/spark/examples/sql/SQLDataSourceExample.scala”。</p>

<p><strong>13.直接在文件上运行 SQL</strong> <br>
不使用读取 API 将文件加载到 DataFrame 并进行查询, 也可以直接用 SQL 查询该文件.</p>



<pre class="prettyprint"><code class=" hljs sql">val sqlDF = spark.sql("<span class="hljs-operator"><span class="hljs-keyword">SELECT</span> * <span class="hljs-keyword">FROM</span> parquet.<span class="hljs-string">`examples/src/main/resources/users.parquet`</span><span class="hljs-string">")</span></span></code></pre>

<p><strong>14.Parquet Files</strong> <br>
Parquet 是许多其他数据处理系统支持的 columnar format （柱状格式）. Spark SQL 支持读写 Parquet 文件, 可自动保留 schema of the original data （原始数据的模式）. 当编写 Parquet 文件时, 出于兼容性原因, 所有 columns 都将自动转换为可空.</p>

<p>Loading Data Programmatically （以编程的方式加载数据）</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">// Encoders <span class="hljs-keyword">for</span> most common types are automatically provided <span class="hljs-keyword">by</span> importing spark.implicits._
import spark.implicits._

val peopleDF = spark.<span class="hljs-built_in">read</span>.json(<span class="hljs-string">"examples/src/main/resources/people.json"</span>)
<span class="hljs-comment">
// DataFrames can be saved as Parquet files, maintaining the schema information</span>
peopleDF.<span class="hljs-built_in">write</span>.parquet(<span class="hljs-string">"people.parquet"</span>)
<span class="hljs-comment">
// Read in the parquet file created above</span><span class="hljs-comment">
// Parquet files are self-describing so the schema is preserved</span><span class="hljs-comment">
// The result of loading a Parquet file is also a DataFrame</span>
val parquetFileDF = spark.<span class="hljs-built_in">read</span>.parquet(<span class="hljs-string">"people.parquet"</span>)
<span class="hljs-comment">
// Parquet files can also be used to create a temporary view and then used in SQL statements</span>
parquetFileDF.createOrReplaceTempView(<span class="hljs-string">"parquetFile"</span>)
val namesDF = spark.sql(<span class="hljs-string">"SELECT name FROM parquetFile WHERE age BETWEEN 13 AND 19"</span>)
namesDF.map(attributes =&gt; <span class="hljs-string">"Name: "</span> + attributes(<span class="hljs-number">0</span>)).show()<span class="hljs-comment">
// +------------+</span><span class="hljs-comment">
// |       value|</span><span class="hljs-comment">
// +------------+</span><span class="hljs-comment">
// |Name: Justin|</span><span class="hljs-comment">
// +------------+</span></code></pre>

<hr>

<p>参考博客：</p>

<ul>
<li><p>操作技巧：将 Spark 中的文本转换为 Parquet 以提升性能 <br>
<a href="https://www.ibm.com/developerworks/cn/analytics/blog/ba-parquet-for-spark-sql/index.html" rel="nofollow">https://www.ibm.com/developerworks/cn/analytics/blog/ba-parquet-for-spark-sql/index.html</a></p></li>
<li><p>sparkAPI <br>
<a href="http://spark.apache.org/docs/2.2.0/api/" rel="nofollow">http://spark.apache.org/docs/2.2.0/api/</a></p></li>
</ul>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>