---
layout:     post
title:      Spark修炼之道（进阶篇）——Spark入门到精通：第八节 Spark SQL与DataFrame（一)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h2 id="本节主要内宾">本节主要内宾</h2>

<ol><li>Spark SQL简介</li>
	<li>DataFrame</li>
</ol><h2 id="1-spark-sql简介"><a name="t1"></a>1. Spark SQL简介</h2>

<p>Spark SQL是Spark的五大核心模块之一，用于在Spark平台之上处理结构化数据，利用Spark SQL可以构建大数据平台上的数据仓库，它具有如下特点： <br>
（1）能够无缝地将SQL语句集成到Spark应用程序当中 <br><img alt="这里写图片描述" class="has" src="https://img-blog.csdn.net/20150924224445285"> <br>
（2）统一的数据访问方式 <br>
DataFrames and SQL provide a common way to access a variety of data sources, including Hive, Avro, Parquet, ORC, JSON, and JDBC. You can even join data across these sources. <br><img alt="这里写图片描述" class="has" src="https://img-blog.csdn.net/20150924224400748"> <br>
(3) 兼容Hive <br><img alt="这里写图片描述" class="has" src="https://img-blog.csdn.net/20150924224103818"> <br>
(4) 可采用JDBC or ODBC连接 <br><img alt="这里写图片描述" class="has" src="https://img-blog.csdn.net/20150924224139172"> <br>
具体见：<a href="http://spark.apache.org/sql/" rel="nofollow">http://spark.apache.org/sql/</a></p>

<p>关于Spark SQL的运行原理可参见：<a href="http://blog.csdn.net/book_mmicky/article/details/39956809" rel="nofollow">http://blog.csdn.net/book_mmicky/article/details/39956809</a>，文章写得非常好 ，这里不再赘述，在此向作者致敬</p>

<h2 id="2-dataframe"><a name="t2"></a>2. DataFrame</h2>

<h3 id="1dataframe简介"><a name="t3"></a>（1）DataFrame简介</h3>

<p>本文部分内容译自<a href="https://databricks.com/blog/2015/02/17/introducing-dataframes-in-spark-for-large-scale-data-science.html" rel="nofollow">https://databricks.com/blog/2015/02/17/introducing-dataframes-in-spark-for-large-scale-data-science.html</a></p>

<p>DataFrames在Spark-1.3.0中引入，主要解决使用Spark RDD API使用的门槛，使熟悉R语言等的数据分析师能够快速上手Spark下的数据分析工作，极大地扩大了Spark使用者的数量，由于DataFrames脱胎自SchemaRDD，因此它天然适用于分布式大数据场景。相信在不久的将来，Spark将是大数据分析的终极归宿。</p>

<p>在Spark中，DataFrame是一种以RDD为基础的分布式数据集，与传统RDBMS的表结构类似。与一般的RDD不同的是，DataFrame带有schema元信息，即DataFrame所表示的表数据集的每一列都带有名称和类型，它对于数据的内部结构具有很强的描述能力。因此Spark SQL可以对藏于DataFrame背后的数据源以及作用于DataFrame之上的变换进行了针对性的优化，最终达到大幅提升运行时效率。</p>

<p>DataFrames具有如下特点：</p>

<p>（1）Ability to scale from kilobytes of data on a single laptop to petabytes on a large cluster（支持单机KB级到集群PB级的数据处理） <br>
（2）Support for a wide array of data formats and storage systems（支持多种数据格式和存储系统，如图所示） <br><img alt="这里写图片描述" class="has" src="https://img-blog.csdn.net/20150925200628346"> <br>
（3）State-of-the-art optimization and code generation through the Spark SQL Catalyst optimizer（通过Spark SQL Catalyst优化器可以进行高效的代码生成和优化） <br>
（4）Seamless integration with all big data tooling and infrastructure via Spark（能够无缝集成所有的大数据处理工具） <br>
（5）APIs for Python, Java, Scala, and R (in development via SparkR)（提供Python, Java, Scala, R语言API)</p>

<h3 id="2dataframe-实战"><a name="t4"></a>（2）DataFrame 实战</h3>

<p>本节部分内容来自：<a href="http://spark.apache.org/docs/latest/sql-programming-guide.html#dataframes" rel="nofollow">http://spark.apache.org/docs/latest/sql-programming-guide.html#dataframes</a></p>

<p>将people.json上传到HDFS上，放置在/data目录下，people.json文件内容如下：</p>

<pre class="has">
<code>root@sparkslave01:~# hdfs dfs -cat /data/people.json
{"name":"Michael"}
{"name":"Andy", "age":30}
{"name":"Justin", "age":19}</code></pre>

<ul><li> </li>
</ul><p>由于json文件中已经包括了列名称的信息，因此它可以直接创建DataFrame</p>

<pre class="has">
<code>scala&gt; val df = sqlContext.read.json("/data/people.json")
df: org.apache.spark.sql.DataFrame = [age: bigint, name: string]

//显示DataFrame完整信息
scala&gt; df.show()
+----+-------+
| age|   name|
+----+-------+
|null|Michael|
|  30|   Andy|
|  19| Justin|
+----+-------+
</code></pre>

<ul><li> </li>
</ul><p><img alt="这里写图片描述" class="has" src="https://img-blog.csdn.net/20150926161046661"></p>

<pre class="has">
<code>//查看DataFrame元数据信息
scala&gt; df.printSchema()
root
 |-- age: long (nullable = true)
 |-- name: string (nullable = true)
</code></pre>

<ul><li>1</li>
</ul><pre class="has">
<code>//返回DataFrame某列所有数据
scala&gt; df.select("name").show()
+-------+
|   name|
+-------+
|Michael|
|   Andy|
| Justin|
+-------+
</code></pre>

<ul><li> </li>
</ul><pre class="has">
<code>//DataFrame数据过滤
scala&gt; df.filter(df("age") &gt; 21).show()
+---+----+
|age|name|
+---+----+
| 30|Andy|
+---+----+
</code></pre>

<ul><li> </li>
</ul><pre class="has">
<code>//按年龄分组
scala&gt; df.groupBy("age").count().show()
+----+-----+
| age|count|
+----+-----+
|null|    1|
|  19|    1|
|  30|    1|
+----+-----+
</code></pre>

<ul><li> </li>
</ul><pre class="has">
<code>//注册成表
scala&gt; df.registerTempTable("people")
//执行SparkSQL
scala&gt; val teenagers = sqlContext.sql("SELECT name, age FROM people WHERE age &gt;= 13 AND age &lt;= 19")
teenagers: org.apache.spark.sql.DataFrame = [name: string, age: bigint]
//结果格式化输出
scala&gt; teenagers.map(t =&gt; "Name: " + t(0)).collect().foreach(println)
Name: Justin</code></pre>

<ul><li> </li>
</ul><p><img alt="这里写图片描述" class="has" src="https://img-blog.csdn.net/20150926161913919"></p>            </div>
                </div>