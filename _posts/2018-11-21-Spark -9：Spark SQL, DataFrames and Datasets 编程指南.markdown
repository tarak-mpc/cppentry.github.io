---
layout:     post
title:      Spark -9：Spark SQL, DataFrames and Datasets 编程指南
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：转自 http://www.234plus.com/					https://blog.csdn.net/ZhongGuoZhiChuang/article/details/54617253				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:14px;">翻译：http://spark.apache.org/docs/latest/sql-programming-guide.html</span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><strong><span style="font-size:14px;">概述</span></strong></p>
<p><span style="font-size:14px;">Spark SQL是用于结构化数据处理的Spark模块。与基本Spark RDD API不同，Spark SQL提供的接口为Spark提供了有关数据结构和正在执行的计算的更多信息。在内部，Spark SQL使用这些额外的信息来执行额外的优化。有几种方法与Spark SQL（包括SQL和Dataset API）进行交互。当计算结果时，使用相同的执行引擎，独立于您用来表达计算的API /语言。这种统一意味着开发人员可以容易地在不同的API之间来回切换，基于这些API提供了表达给定变换的最自然的方式。</span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;">此页面上的所有示例都使用Spark发行版中包含的示例数据，并且可以在spark-shell，pyspark shell或sparkR shell中运行。<br></span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;"><br></span></p>
<p></p>
<h2 id="sql" style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:40px;color:rgb(29,31,34);">
<span style="font-size:14px;">SQL</span></h2>
<span style="font-size:14px;">Spark SQL的一个用途是执行SQL查询。 Spark SQL还可以用于从现有Hive安装中读取数据。有关如何配置此功能的更多信息，请参阅Hive表部分。当在另一种编程语言中运行SQL时，结果将作为Dataset / DataFrame返回。您还可以使用命令行或通过JDBC / ODBC与SQL界面交互。<br></span>
<p></p>
<p><span style="font-size:14px;"><br></span></p>
<p></p>
<h2 id="datasets-and-dataframes" style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:40px;color:rgb(29,31,34);">
<span style="font-size:14px;">Datasets and DataFrames</span></h2>
<span style="font-family:'Microsoft YaHei', Arial, Helvetica, sans-serif;"><span style="font-size:14px;"></span></span>
<h2 style="line-height:40px;"><span style="font-size:14px;"><span style="font-weight:normal;">Datasets是数据的分布式集合。数据集是在Spark 1.6中添加的一个新接口，它提供了RDD的优点（强类型化，使用强大的lambda函数的能力）以及Spark SQL优化的执行引擎的优点。数据集可以从JVM对象构建，然后使用函数转换（map，flatMap，filter等）操作。</span><span style="font-weight:normal;">Scala和Java中提供了Dataset
 API。Python没有对Dataset API的支持。但是由于Python的动态特性，Dataset API的许多优点已经可用（即，您可以通过名称自然地访问行的字段row.columnName）。R的情况是类似的。</span></span></h2>
<div><span style="font-size:14px;"><span style="font-weight:normal;"><br></span></span></div>
<div><span style="font-size:14px;"><span style="font-weight:normal;">DataFrame是组织成命名列的数据集。它在概念上等同于关系数据库中的表或R / Python中的数据框架，但具有更丰富的优化。DataFrames可以从各种来源构建，例如：结构化数据文件，Hive中的表，外部数据库或现有RDD。DataFrame API可用于Scala，Java，Python和R中。在Scala和Java中，DataFrame由Rows的Dataset表示。在Scala
 API中，DataFrame只是Dataset [Row]的类型别名。而在Java API中，用户需要使用Dataset &lt;Row&gt;来表示DataFrame。<br></span></span></div>
<div><span style="font-size:14px;"><span style="font-weight:normal;"><br></span></span><h2 id="starting-point-sparksession" style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:40px;color:rgb(29,31,34);font-size:24px;">
Starting Point: SparkSession</h2>
<br>
Spark中所有功能的入口点是SparkSession类。要创建基本的SparkSession，只需使用SparkSession.builder（）：<br></div>
<div><span style="font-size:14px;"><span style="font-weight:normal;"></span></span>
<pre style="font-family:Menlo, 'Lucida Console', monospace;color:rgb(51,51,51);line-height:20px;background-color:rgb(245,245,245);"><span class="kn" style="color:rgb(0,112,32);font-weight:bold;">import</span> <span class="nn" style="color:rgb(14,132,181);font-weight:bold;">org.apache.spark.sql.SparkSession</span><span class="o" style="color:rgb(102,102,102);">;</span>

<span class="n">SparkSession</span> <span class="n">spark</span> <span class="o" style="color:rgb(102,102,102);">=</span> <span class="n">SparkSession</span>
  <span class="o" style="color:rgb(102,102,102);">.</span><span class="na" style="color:rgb(64,112,160);">builder</span><span class="o" style="color:rgb(102,102,102);">()</span>
  <span class="o" style="color:rgb(102,102,102);">.</span><span class="na" style="color:rgb(64,112,160);">appName</span><span class="o" style="color:rgb(102,102,102);">(</span><span class="s" style="color:rgb(64,112,160);">"Java Spark SQL basic example"</span><span class="o" style="color:rgb(102,102,102);">)</span>
  <span class="o" style="color:rgb(102,102,102);">.</span><span class="na" style="color:rgb(64,112,160);">config</span><span class="o" style="color:rgb(102,102,102);">(</span><span class="s" style="color:rgb(64,112,160);">"spark.some.config.option"</span><span class="o" style="color:rgb(102,102,102);">,</span> <span class="s" style="color:rgb(64,112,160);">"some-value"</span><span class="o" style="color:rgb(102,102,102);">)</span>
  <span class="o" style="color:rgb(102,102,102);">.</span><span class="na" style="color:rgb(64,112,160);">getOrCreate</span><span class="o" style="color:rgb(102,102,102);">();</span></pre>
<br></div>
<div><span style="font-size:14px;"><span style="font-weight:normal;"><br></span></span></div>
<div><span style="font-size:14px;"><span style="font-weight:normal;">在Spark “examples / src / main / java / org / apache / spark / examples / sql / JavaSparkSQLExample.java”中查找完整的示例代码。Spark 2.0中的SparkSession为Hive功能提供了内置支持，包括使用HiveQL编写查询，访问Hive UDF以及从Hive表中读取数据的能力。要使用这些功能，您不需要具有现有的Hive设置。<br></span></span></div>
<div><span style="font-weight:normal;"><span style="font-size:14px;"><br></span></span></div>
<div><span style="font-weight:normal;"><span style="font-size:14px;"></span></span>
<h2 id="creating-dataframes" style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:40px;color:rgb(29,31,34);font-size:24px;">
Creating DataFrames</h2>
<br></div>
<div><span style="font-size:14px;">使用SparkSession，应用程序可以从现有RDD，Hive表或Spark数据源创建DataFrames。例如，以下内容根据JSON文件的内容创建DataFrame：</span>
<pre style="font-weight:normal;font-family:Menlo, 'Lucida Console', monospace;color:rgb(51,51,51);line-height:20px;background-color:rgb(245,245,245);"><span class="kn" style="color:rgb(0,112,32);font-weight:bold;">import</span> <span class="nn" style="color:rgb(14,132,181);font-weight:bold;">org.apache.spark.sql.Dataset</span><span class="o" style="color:rgb(102,102,102);">;</span>
<span class="kn" style="color:rgb(0,112,32);font-weight:bold;">import</span> <span class="nn" style="color:rgb(14,132,181);font-weight:bold;">org.apache.spark.sql.Row</span><span class="o" style="color:rgb(102,102,102);">;</span>

<span class="n">Dataset</span><span class="o" style="color:rgb(102,102,102);">&lt;</span><span class="n">Row</span><span class="o" style="color:rgb(102,102,102);">&gt;</span> <span class="n">df</span> <span class="o" style="color:rgb(102,102,102);">=</span> <span class="n">spark</span><span class="o" style="color:rgb(102,102,102);">.</span><span class="na" style="color:rgb(64,112,160);">read</span><span class="o" style="color:rgb(102,102,102);">().</span><span class="na" style="color:rgb(64,112,160);">json</span><span class="o" style="color:rgb(102,102,102);">(</span><span class="s" style="color:rgb(64,112,160);">"examples/src/main/resources/people.json"</span><span class="o" style="color:rgb(102,102,102);">);</span>

<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// Displays the content of the DataFrame to stdout</span>
<span class="n">df</span><span class="o" style="color:rgb(102,102,102);">.</span><span class="na" style="color:rgb(64,112,160);">show</span><span class="o" style="color:rgb(102,102,102);">();</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// +----+-------+</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// | age|   name|</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// +----+-------+</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// |null|Michael|</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// |  30|   Andy|</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// |  19| Justin|</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// +----+-------+</span></pre>
DataFrames为Scala，Java，Python和R中的结构化数据操作提供了特定领域的语言。如上所述，在Spark 2.0中，DataFrames只是Scala和Java API中的Rows数据集。这些操作也称为“非类型转换”，与带有强类型化Scala / Java数据集的“类型转换”相反。<br>
这里我们包括使用数据集的结构化数据处理的一些基本示例：<br><pre style="font-weight:normal;font-family:Menlo, 'Lucida Console', monospace;color:rgb(51,51,51);line-height:20px;background-color:rgb(245,245,245);"><span class="c1" style="color:rgb(96,160,176);font-style:italic;">// col("...") is preferable to df.col("...")</span>
<span class="kn" style="color:rgb(0,112,32);font-weight:bold;">import static</span> <span class="nn" style="color:rgb(14,132,181);font-weight:bold;">org.apache.spark.sql.functions.col</span><span class="o" style="color:rgb(102,102,102);">;</span>

<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// Print the schema in a tree format</span>
<span class="n">df</span><span class="o" style="color:rgb(102,102,102);">.</span><span class="na" style="color:rgb(64,112,160);">printSchema</span><span class="o" style="color:rgb(102,102,102);">();</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// root</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// |-- age: long (nullable = true)</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// |-- name: string (nullable = true)</span>

<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// Select only the "name" column</span>
<span class="n">df</span><span class="o" style="color:rgb(102,102,102);">.</span><span class="na" style="color:rgb(64,112,160);">select</span><span class="o" style="color:rgb(102,102,102);">(</span><span class="s" style="color:rgb(64,112,160);">"name"</span><span class="o" style="color:rgb(102,102,102);">).</span><span class="na" style="color:rgb(64,112,160);">show</span><span class="o" style="color:rgb(102,102,102);">();</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// +-------+</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// |   name|</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// +-------+</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// |Michael|</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// |   Andy|</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// | Justin|</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// +-------+</span>

<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// Select everybody, but increment the age by 1</span>
<span class="n">df</span><span class="o" style="color:rgb(102,102,102);">.</span><span class="na" style="color:rgb(64,112,160);">select</span><span class="o" style="color:rgb(102,102,102);">(</span><span class="n">col</span><span class="o" style="color:rgb(102,102,102);">(</span><span class="s" style="color:rgb(64,112,160);">"name"</span><span class="o" style="color:rgb(102,102,102);">),</span> <span class="n">col</span><span class="o" style="color:rgb(102,102,102);">(</span><span class="s" style="color:rgb(64,112,160);">"age"</span><span class="o" style="color:rgb(102,102,102);">).</span><span class="na" style="color:rgb(64,112,160);">plus</span><span class="o" style="color:rgb(102,102,102);">(</span><span class="mi" style="color:rgb(64,160,112);">1</span><span class="o" style="color:rgb(102,102,102);">)).</span><span class="na" style="color:rgb(64,112,160);">show</span><span class="o" style="color:rgb(102,102,102);">();</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// +-------+---------+</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// |   name|(age + 1)|</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// +-------+---------+</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// |Michael|     null|</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// |   Andy|       31|</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// | Justin|       20|</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// +-------+---------+</span>

<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// Select people older than 21</span>
<span class="n">df</span><span class="o" style="color:rgb(102,102,102);">.</span><span class="na" style="color:rgb(64,112,160);">filter</span><span class="o" style="color:rgb(102,102,102);">(</span><span class="n">col</span><span class="o" style="color:rgb(102,102,102);">(</span><span class="s" style="color:rgb(64,112,160);">"age"</span><span class="o" style="color:rgb(102,102,102);">).</span><span class="na" style="color:rgb(64,112,160);">gt</span><span class="o" style="color:rgb(102,102,102);">(</span><span class="mi" style="color:rgb(64,160,112);">21</span><span class="o" style="color:rgb(102,102,102);">)).</span><span class="na" style="color:rgb(64,112,160);">show</span><span class="o" style="color:rgb(102,102,102);">();</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// +---+----+</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// |age|name|</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// +---+----+</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// | 30|Andy|</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// +---+----+</span>

<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// Count people by age</span>
<span class="n">df</span><span class="o" style="color:rgb(102,102,102);">.</span><span class="na" style="color:rgb(64,112,160);">groupBy</span><span class="o" style="color:rgb(102,102,102);">(</span><span class="s" style="color:rgb(64,112,160);">"age"</span><span class="o" style="color:rgb(102,102,102);">).</span><span class="na" style="color:rgb(64,112,160);">count</span><span class="o" style="color:rgb(102,102,102);">().</span><span class="na" style="color:rgb(64,112,160);">show</span><span class="o" style="color:rgb(102,102,102);">();</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// +----+-----+</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// | age|count|</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// +----+-----+</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// |  19|    1|</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// |null|    1|</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// |  30|    1|</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// +----+-----+</span></pre>
<span style="font-family:'Microsoft YaHei', Arial, Helvetica, sans-serif;font-size:14px;"><strong>以编程方式运行SQL查询</strong></span><br><span style="font-family:'Microsoft YaHei', Arial, Helvetica, sans-serif;font-size:14px;"><strong><br></strong></span><span style="font-family:'Microsoft YaHei', Arial, Helvetica, sans-serif;font-size:14px;"><span style="font-family:'Microsoft YaHei', Arial, Helvetica, sans-serif;font-size:14px;">SparkSession上的sql函数使应用程序以编程方式运行SQL查询，并以数据集&lt;Row&gt;返回结果。</span><br></span><br></div>
<div><span style="font-size:14px;"></span>
<pre style="font-family:Menlo, 'Lucida Console', monospace;color:rgb(51,51,51);line-height:20px;background-color:rgb(245,245,245);"><span class="kn" style="color:rgb(0,112,32);font-weight:bold;">port</span> <span class="nn" style="color:rgb(14,132,181);font-weight:bold;">org.apache.spark.sql.Dataset</span><span class="o" style="color:rgb(102,102,102);">;</span>
<span class="kn" style="color:rgb(0,112,32);font-weight:bold;">import</span> <span class="nn" style="color:rgb(14,132,181);font-weight:bold;">org.apache.spark.sql.Row</span><span class="o" style="color:rgb(102,102,102);">;</span>

<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// Register the DataFrame as a SQL temporary view</span>
<span class="n">df</span><span class="o" style="color:rgb(102,102,102);">.</span><span class="na" style="color:rgb(64,112,160);">createOrReplaceTempView</span><span class="o" style="color:rgb(102,102,102);">(</span><span class="s" style="color:rgb(64,112,160);">"people"</span><span class="o" style="color:rgb(102,102,102);">);</span>

<span class="n">Dataset</span><span class="o" style="color:rgb(102,102,102);">&lt;</span><span class="n">Row</span><span class="o" style="color:rgb(102,102,102);">&gt;</span> <span class="n">sqlDF</span> <span class="o" style="color:rgb(102,102,102);">=</span> <span class="n">spark</span><span class="o" style="color:rgb(102,102,102);">.</span><span class="na" style="color:rgb(64,112,160);">sql</span><span class="o" style="color:rgb(102,102,102);">(</span><span class="s" style="color:rgb(64,112,160);">"SELECT * FROM people"</span><span class="o" style="color:rgb(102,102,102);">);</span>
<span class="n">sqlDF</span><span class="o" style="color:rgb(102,102,102);">.</span><span class="na" style="color:rgb(64,112,160);">show</span><span class="o" style="color:rgb(102,102,102);">();</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// +----+-------+</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// | age|   name|</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// +----+-------+</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// |null|Michael|</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// |  30|   Andy|</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// |  19| Justin|</span>
<span class="c1" style="color:rgb(96,160,176);font-style:italic;">// +----+-------+</span></pre>
<br></div>
<div><span style="font-size:14px;"><br></span></div>
<br><p></p>
            </div>
                </div>