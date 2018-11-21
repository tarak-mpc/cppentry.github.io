---
layout:     post
title:      Spark SQL, DataFrames 和 Datasets 指南
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<blockquote>
  <p>目前在Spark中，SparkSQL用的是最多的，Spark Streaming次之，所以将Spark SQL相关官方文档翻译下，官方文档是最好的学习资料。</p>
</blockquote>



<h2 id="概述">概述</h2>

<p>Spark SQL是用于结构化数据处理的Spark模块。与基本Spark RDD API不同，Spark SQL提供的接口为Spark提供了有关数据结构和正在执行的计算的更多信息。在内部，Spark SQL使用这些额外的信息来执行额外的优化。 有几种方法与Spark SQL（包括SQL和Dataset API）进行交互。当计算结果时，使用相同的执行引擎，独立于用来表示计算的哪种API 或者语言。 这种统一意味着开发人员可以容易地在不同的API之间来回切换，基于这些API提供了表达给定变换的最自然的方式。</p>

<p>此页面上的所有示例都使用Spark发行版中包含的示例数据，并且可以在spark-shell，pyspark shell或sparkR shell中运行。</p>



<h2 id="sql">SQL</h2>

<p>Spark SQL的一个用途是执行SQL查询。 Spark SQL还可以用于从现有安装的Hive中读取数据。 有关如何配置此功能的更多信息，请参阅<a href="http://spark.apache.org/docs/latest/sql-programming-guide.html#hive-tables" rel="nofollow" target="_blank">Hive表</a>部分。 当在另一种编程语言中运行SQL时，结果将作为<a href="http://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes" rel="nofollow" target="_blank">Dataset / DataFrame</a>返回。 您还可以与SQL接口交互，使用<a href="http://spark.apache.org/docs/latest/sql-programming-guide.html#running-the-spark-sql-cli" rel="nofollow" target="_blank">命令行</a>或通过<a href="http://spark.apache.org/docs/latest/sql-programming-guide.html#running-the-thrift-jdbcodbc-server" rel="nofollow" target="_blank">JDBC / ODBC</a>。</p>



<h2 id="datasets和dataframes">Datasets和DataFrames</h2>

<p>一个Dataset 是数据的分布式集合。数据集是在Spark 1.6中添加的一个新接口，它提供了RDD的优点（强类型化，使用强大的lambda函数的能力）以及Spark SQL优化后的执行引擎的优点。一个Dataset 可以从JVM对象<a href="http://spark.apache.org/docs/latest/sql-programming-guide.html#creating-datasets" rel="nofollow" target="_blank">构造</a>，然后使用函数转换（map， flatMap，filter等）去操作。 Dataset API 支持<a href="http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.Dataset" rel="nofollow" target="_blank">Scala</a>和<a href="http://spark.apache.org/docs/latest/api/java/index.html?org/apache/spark/sql/Dataset.html" rel="nofollow" target="_blank">Java</a>。 Python不支持Dataset API。但是由于Python的动态特性，Dataset API的许多优点已经可用（即，您可以通过名称row.columnName自然地访问行的字段）。 R的情况是类似的。</p>

<p>DataFrame是组织成命名列的Dataset 。它在概念上等同于关系数据库中的表或R / Python中的数据框架，但在底层具有更丰富的优化。 DataFrames可以从各种来<a href="http://spark.apache.org/docs/latest/sql-programming-guide.html#data-sources" rel="nofollow" target="_blank">源</a>构建，例如：结构化数据文件，Hive中的表，外部数据库或现有RDDs。 DataFrame API支持的语言有Scala，Java，<a href="http://spark.apache.org/docs/latest/api/python/pyspark.sql.html#pyspark.sql.DataFrame" rel="nofollow" target="_blank">Python</a>和<a href="http://spark.apache.org/docs/latest/api/R/index.html" rel="nofollow" target="_blank">R</a>。在Scala和Java中，DataFrame由<code>Rows</code>的一个Dataset表示。在<a href="http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.Dataset" rel="nofollow" target="_blank">Scala API</a>中，DataFrame只是Dataset [Row]的类型别名。而在<a href="http://spark.apache.org/docs/latest/api/java/index.html?org/apache/spark/sql/Dataset.html" rel="nofollow" target="_blank">Java API</a>中，用户需要使用<code>Dataset &lt;Row&gt;</code>来表示DataFrame。</p>

<p>在本文档中，我们经常将Scala / Java Datasets 的Rows 作为DataFrames。</p>



<h2 id="入门">入门</h2>



<h3 id="起始点sparksession">起始点：SparkSession</h3>

<p><strong>Scala：</strong> <br>
Spark中所有功能的入口点是<a href="http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.SparkSession" rel="nofollow" target="_blank">SparkSession</a>类。 要创建基本的<code>SparkSession</code>，只需使用<code>SparkSession.builder（）</code>：</p>



<pre class="prettyprint"><code class=" hljs avrasm">import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.sql</span><span class="hljs-preprocessor">.SparkSession</span>

val spark = SparkSession
  <span class="hljs-preprocessor">.builder</span>()
  <span class="hljs-preprocessor">.appName</span>(<span class="hljs-string">"Spark SQL basic example"</span>)
  <span class="hljs-preprocessor">.config</span>(<span class="hljs-string">"spark.some.config.option"</span>, <span class="hljs-string">"some-value"</span>)
  <span class="hljs-preprocessor">.getOrCreate</span>()

// For implicit conversions like converting RDDs to DataFrames
import spark<span class="hljs-preprocessor">.implicits</span>._</code></pre>

<p>在Spark repo中的<code>“examples / src / main / scala / org / apache / spark / examples / sql / SparkSQLExample.scala”</code>下找到完整的示例代码。 <br>
Spark 2.0中的SparkSession为Hive功能提供了内置支持，包括使用HiveQL编写查询，访问Hive UDF以及从Hive表中读取数据的能力。 要使用这些功能，您不需要具有现有的Hive设置。</p>

<p>Java: <br>
<a href="http://spark.apache.org/docs/latest/api/java/index.html#org.apache.spark.sql.SparkSession" rel="nofollow" target="_blank">SparkSession </a>类</p>



<pre class="prettyprint"><code class=" hljs avrasm">import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.sql</span><span class="hljs-preprocessor">.SparkSession</span><span class="hljs-comment">;</span>

SparkSession spark = SparkSession
  <span class="hljs-preprocessor">.builder</span>()
  <span class="hljs-preprocessor">.appName</span>(<span class="hljs-string">"Java Spark SQL basic example"</span>)
  <span class="hljs-preprocessor">.config</span>(<span class="hljs-string">"spark.some.config.option"</span>, <span class="hljs-string">"some-value"</span>)
  <span class="hljs-preprocessor">.getOrCreate</span>()<span class="hljs-comment">;</span></code></pre>

<p>在Spark repo中的 <br>
<code>examples/src/main/java/org/apache/spark/examples/sql/JavaSparkSQLExample.java</code>下找到完整的示例代码。</p>



<h3 id="创建dataframes">创建DataFrames</h3>

<p><strong>Scala:</strong> <br>
使用SparkSession，应用程序可以从<a href="http://spark.apache.org/docs/latest/sql-programming-guide.html#interoperating-with-rdds" rel="nofollow" target="_blank">现有RDD</a>，Hive表或<a href="http://spark.apache.org/docs/latest/sql-programming-guide.html#data-sources" rel="nofollow" target="_blank">Spark数据源</a>创建DataFrames。</p>

<p>例如，以下内容根据JSON文件的内容创建DataFrame：</p>



<pre class="prettyprint"><code class=" hljs fsharp"><span class="hljs-keyword">val</span> df = spark.read.json(<span class="hljs-string">"examples/src/main/resources/people.json"</span>)

<span class="hljs-comment">// Displays the content of the DataFrame to stdout</span>
df.show()
<span class="hljs-comment">// +----+-------+</span>
<span class="hljs-comment">// | age|   name|</span>
<span class="hljs-comment">// +----+-------+</span>
<span class="hljs-comment">// |null|Michael|</span>
<span class="hljs-comment">// |  30|   Andy|</span>
<span class="hljs-comment">// |  19| Justin|</span>
<span class="hljs-comment">// +----+-------+</span></code></pre>

<p>在Spark repo中的“examples / src / main / scala / org / apache / spark / examples / sql / SparkSQLExample.scala”下找到完整的示例代码。</p>

<p><strong>Java:</strong></p>



<pre class="prettyprint"><code class=" hljs actionscript"><span class="hljs-preprocessor"><span class="hljs-keyword">import</span> org.apache.spark.sql.Dataset;</span>
<span class="hljs-preprocessor"><span class="hljs-keyword">import</span> org.apache.spark.sql.Row;</span>

Dataset&lt;Row&gt; df = spark.read().json(<span class="hljs-string">"examples/src/main/resources/people.json"</span>);

<span class="hljs-comment">// Displays the content of the DataFrame to stdout</span>
df.show();
<span class="hljs-comment">// +----+-------+</span>
<span class="hljs-comment">// | age|   name|</span>
<span class="hljs-comment">// +----+-------+</span>
<span class="hljs-comment">// |null|Michael|</span>
<span class="hljs-comment">// |  30|   Andy|</span>
<span class="hljs-comment">// |  19| Justin|</span>
<span class="hljs-comment">// +----+-------+</span></code></pre>

<p>在Spark repo中的“examples / src / main / java / org / apache / spark / examples / sql / JavaSparkSQLExample.java”中查找完整的示例代码。</p>

<hr>

<p><strong>未完</strong>，接下来会对Spark SQL以下几点做介绍：</p>

<ul>
<li><p>数据源</p></li>
<li><p>性能调优</p></li>
<li><p>分布式SQL引擎</p></li>
<li>Spark SQL 迁移指南（Spark SQL 版本升级、兼容Apache Hive）</li>
</ul>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>