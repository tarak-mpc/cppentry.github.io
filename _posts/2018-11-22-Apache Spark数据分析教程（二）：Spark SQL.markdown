---
layout:     post
title:      Apache Spark数据分析教程（二）：Spark SQL
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
<a href="http://spark.apache.org/" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Spark</a>是一款非常流行同时功能又十分强大的实时数据分析工具。在本 <a href="https://dzone.com/articles/introduction-to-bigdata-analytics-with-apache-spar-6" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;"></a><a href="http://www.csdn.net/article/2015-11-25/2826324" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Spark序列教程的第一部分</a>，我们已经对Spark进行了介绍，讲解了Spark的历史，详细解释了用于在Spark集群中进行数据分片存储的弹性分布式数据集（ <a href="https://spark.apache.org/docs/0.8.1/api/core/org/apache/spark/rdd/RDD.html" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">RDDs</a>）并对Apache Spark的生态系统进行了介绍。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
本教程（第二部分）将对Spark生态系统中占有重要地位的Spark SQL和DataFrame进行介绍，给大家演示Spark、Spark SQL结合Cassandara的使用。如果你还没有学习过本序列教程的第一部分，请点击 <a href="https://dzone.com/articles/introduction-to-bigdata-analytics-with-apache-spar-6" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;"></a><a href="http://www.csdn.net/article/2015-11-25/2826324" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Apache Spark介绍（第一部分）：实时数据分析</a>进行学习。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
<a href="https://en.wikipedia.org/wiki/Apache_Spark" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Apache Spark</a>是 <a href="https://hadoop.apache.org/" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Hadoop</a>的有效继任者并对其进行了有效补充，它引领了大数据技术的发展趋势。Spark为数据分析运行在大规模分布式系统任务上提供了易于使用的API，它能够比其它形式的数据分析运行得更快，这缘于其大多数的任务都能够在内存中完成。Apache Spark为一个普通的开发人员提供了实时大数据分析能力, Spark SQL便是明证，Spark SQL API不仅易于使用而且功能强大。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;text-align:center;">
<img src="http://img.ptcms.csdn.net/article/201511/25/5655d5d6c613c_middle.jpg?_=42356" alt="" style="vertical-align:middle;border:none;"></p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<h2 style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;">
Spark SQL</h2>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
Spark SQL使得运行SQL和HiveQL查询十分简单（注意： <a href="https://en.wikipedia.org/wiki/Apache_Hive" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">HiveQL源于Apache Hive，Hive是一个构建在Hadoop平台基础上的数据仓库系统，用于提供大数据分析服务</a>）。Spark SQL 能够轻易地定位相应的表和元数据。Spark SQL 为Spark提供了查询结构化数据的能力，查询时既可以使用SQL也可以使用人们熟知的DataFrame API（RDD）。Spark SQL支持多语言编程包括Java、Scala、Python及R，开发人员可以根据自身喜好进行选择。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
<strong>使用Java 查询数据</strong></p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;">String query = "SELECT * FROM table";

ResultSet results = session.execute(query);</pre>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
<a href="http://spark.apache.org/docs/latest/sql-programming-guide.html" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">DataFrame</a>是Spark SQL的核心，它将数据保存为行构成的集合，行对应列有相应的列名。使用DataFrames可以非常方便地查询数据、给数据绘图及进行数据过滤。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
DataFrames也可以用于数据的输入与输出，例如利用Spark SQL中的DataFrames，可以轻易地将下列数据格式加载为表并进行相应的查询操作：</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<ul style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;"><li style="list-style:disc;"> RDD</li><li style="list-style:disc;"> JSON</li><li style="list-style:disc;"> Hive</li><li style="list-style:disc;"> Parquet</li><li style="list-style:disc;"> MySQL</li><li style="list-style:disc;"> HDFS</li><li style="list-style:disc;"> S3</li><li style="list-style:disc;"> JDBC</li><li style="list-style:disc;"> 其它 ...</li></ul><p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
数据一旦被读取，借助于DataFrames便可以很方便地进行数据过滤、列查询、计数、求平均值及将不同数据源的数据进行整合。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
如果你正计划通过读取和写数据来进行分析，Spark SQL可以轻易地帮你实现并将整个过程自动化。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
在后面的例子中，我们将在Python Spark shell中给大家演示如何使用Spark SQL和DataFrames。从GitHub上获取提交的 <a href="https://github.com/advantageous/qbit" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">QBit, the Java Microservices Lib</a>历史数据，然后将其加载到Spark当中，并对数据进行相应的操作，具体步骤如下：</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
<strong>在终端上启动Python Spark shell：</strong></p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;">cd spark-1.5.0-bin-hadoop2.4
./bin/pyspark
15/08/22 22:30:40 INFO BlockManagerMaster: Registered BlockManager
Welcome to
      ____              __
     / __/__  ___ _____/ /__
    _\ \/ _ \/ _ `/ __/  '_/
   /__ / .__/\_,_/_/ /_/\_\   version 1.5.0
      /_/
Using Python version 2.7.5 (default, Mar  9 2014 22:15:05)
SparkContext available as sc, HiveContext available as sqlContext.</pre>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
从github上获取QBit的提交历史，并保存到名称为test.log的文件中：</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
<strong>抽取提交历史并保存为log文件</strong></p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;">git log &gt; test.log</pre>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
由于此次使用的是Python，我们先通过textFile方法将test.log加载为RDD，然后在该RDD上执行一些操作：</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;">textFile = sc.textFile("../qbit/test.log")</pre>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
执行完上面这条语句，可以得到一个textFile RDD，该RDD由文本行组成的分区数据构成，先来统计一个RDD中的文本行数：</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;">textFile.count()
5776</pre>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
代码执行完，得到的行数为5776。然后我们先行中带有commit关键字的行筛选出来：</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;">linesWithCommit = textFile.filter(lambda line: "commit" in line)</pre>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
通过前面的操作足以说明通过Python 使用RDD 的简便性。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
为后面演示DataFrame的使用，先让github的历史记录文件抽取保存为JSON类型并将文件命名为sparktest.json：</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
<a href="https://github.com/advantageous/qbit" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;"></a></p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
<strong>将github上的提交历史保存为JSON</strong></p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;">git log  --pretty=format:'{"commit":"%H","author":"%an","author_email":"%ae","date":"%ad","message":"%f"}' &gt; sparktest.json</pre>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
在正式进行Spark SQL操作之前，先得创建sqlContext，它可以通过SparkContext进行创建：</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;">from pyspark.sql import SQLContext
sqlContext = SQLContext(sc)</pre>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
在shell命令行中，sqlContext 同SparkContext 一样都是自动创建的，无需自己手动去创建，SparkContext以SC变量名的形式存在，sqlContext则以sqlContext 变量名的形式存在。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
接下来，将JSON数据加载为Spark的DataFrame，变量命名为dataframe：</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
<strong>将JSON数据加载成DataFrame ，变量命名为dataframe</strong></p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;">dataframe = sqlContext.load("../qbit/sparktest.json", "json")</pre>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
加载数据时，只需调用sqlContext 的load()方法，方法中传入的参数为文件目录和文件类型。Spark会为dataframe解析所有的列及对应名称，为确保所有的工作都已按预期执行，可以打印出dataframe的模式（Schema）：</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
<strong>打印dataframe的模式（Schema）</strong></p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;">dataframe.printSchema()
root
 |-- author: string (nullable = true)
 |-- author_email: string (nullable = true)
 |-- commit: string (nullable = true)
 |-- date: string (nullable = true)
 |-- message: string (nullable = true)</pre>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
上面这个带根（root）的图展示了各行对应的列名及其对应类型。本例中的每行表示的是Gihub上<a href="https://github.com/advantageous/qbit" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">QBit Microservices Lib</a>项目对应的一次提交。所有的准备工作完成后，便可以在数据上进行相应的操作。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
例如，我们可以获取文件的第一条提交记录，该提交记录表示的是github的最近一次提交。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
<strong>获取最近的提交记录用以分析</strong></p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;">dataframe.first()
Row(author=u'Richard Hightower', author_email=u'richardhightower@gmail.com', 
commit=u'696a94f80d1eedae97175f76b9139a340fab1a27', 
date=u'Wed Aug 19 17:51:11 2015 -0700', 
message=u'Merge-pull-request-359-from-advantageous-add_better_uri_param_handling')</pre>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
我们可以查询所有列中的某一列并显示其内容，例如，只查询 <a href="https://github.com/advantageous/qbit" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">QBit Microservices Lib</a>项目的作者(author)列并显示最近的20个源码贡献者，默认情况下Spark会返回最近的20条记录。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
<strong>采用Spark SQL进行分析—查询author列并返回最近的20条记录</strong></p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;">dataframe.select("author").show()
+-----------------+
|           author|
+-----------------+
|Richard Hightower|
|   Rick Hightower|
|   Rick Hightower|
|Richard Hightower|
|   Rick Hightower|
|Richard Hightower|
|   Rick Hightower|
|Geoffrey Chandler|
|Geoffrey Chandler|
|Richard Hightower|
|Richard Hightower|
|Richard Hightower|
|Richard Hightower|
|Richard Hightower|
|Richard Hightower|
|   Rick Hightower|
|   Rick Hightower|
|   Rick Hightower|
|   Rick Hightower|
|   Rick Hightower|
+-----------------+</pre>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
当然，也可以设置show()函数的参数以返回需要的记录行数，这里只返回最近5个为<a href="https://github.com/advantageous/qbit" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">QBit Microservices Lib</a>项目贡献过源码作者：</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
<strong>查询作者列并返回最近的5个贡献过源码的作者</strong></p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;">dataframe.select("author").show(5)
+-----------------+
|           author|
+-----------------+
|Richard Hightower|
|   Rick Hightower|
|   Rick Hightower|
|Richard Hightower|
|   Rick Hightower|
+-----------------+</pre>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
我们可以再好好想想，这里使用的是一些相对非结构化的数据，在这个案例中，我们抓取项目的git提交日志后，可以马上执行相应的查询。现在我们想象一下，如果要在成千上万的项目上执行同样的操作，所有这些项目构成的可能是一个大公司git库，另外经常需要对所有的数据进行分析，而不只是对其中一个项目数据进行分析的话，便可以使用Spark集群处理大量的非结构化数据。此时你便可以看到Spark作为一个实时数据分析平台的处理能力，它具有简单易用、可扩展且处理能力强的特点。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
查询Date列并显示最近的20条提交日期记录：</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<h4 style="list-style:none;color:rgb(51,51,51);font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;">
查询Date列并显示最近的20条提交日期记录</h4>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;">dataframe.select("date").show()
+--------------------+
|                date|
+--------------------+
|Wed Aug 19 17:51:...|
|Wed Aug 19 17:37:...|
|Wed Aug 19 16:59:...|
|Wed Aug 19 14:47:...|
|Wed Aug 19 14:42:...|
|Wed Aug 19 13:05:...|
|Wed Aug 19 11:59:...|
|Mon Aug 17 10:18:...|
|Mon Aug 17 10:17:...|
|Mon Aug 17 00:46:...|
|Sun Aug 16 23:52:...|
|Sun Aug 16 23:33:...|
|Sun Aug 16 23:05:...|
|Sun Aug 16 23:03:...|
|Sun Aug 16 22:33:...|
|Thu Aug 13 21:20:...|
|Thu Aug 13 21:15:...|
|Thu Aug 13 20:31:...|
|Thu Aug 13 20:05:...|
|Thu Aug 13 20:04:...|
+--------------------+</pre>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
通过dataframe获取 <a href="https://github.com/MammatusTech/qbit-microservices-examples/wiki" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">QBit Microservices Lib</a>已提交次数，计算dataframe的行数：</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<h4 style="list-style:none;color:rgb(51,51,51);font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;">
</h4>
<h4 style="list-style:none;color:rgb(51,51,51);font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;">
获取QBit Microservice Lib已经提交次数</h4>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;">dataframe.count()
914</pre>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
914便是提交次数，该提交次数也可以从Github上看到。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
我们也使用DataFrame的 filter函数进行提交次数统计，例如可以统计有多少提交是由Richard Hightower或Geoffrey Chandler完成的。</p>
<h4 style="list-style:none;color:rgb(51,51,51);font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;">
筛选出Richard Hightower 的提交并统计提交次数</h4>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;">dataframe.filter(dataframe.author =="Richard Hightower").count()
708</pre>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
Richard Hightower的提交次数是708。</p>
<h4 style="list-style:none;color:rgb(51,51,51);font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;">
</h4>
<h4 style="list-style:none;color:rgb(51,51,51);font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;">
筛选出Geoffrey Chandler 的提交并统计提交次数</h4>
<div style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
<pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);">dataframe.filter(dataframe.author =="Geoffrey Chandler").count()
102</pre></div>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
Geoffrey Chandler的提交次数是102。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
前面的例子是通过JSON格式的数据文件创建DataFrame，我们也可以通过另外两种方式创建DataFrame：</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<ul style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;"><li style="list-style:disc;">如果列及其类型在运行时之前都是未知的，可以通过创建模式并将其应用到RDD上来创建。</li><li style="list-style:disc;">如列及其类型是已知的，可以通过反射机制来创建。</li></ul><p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
为简单起见，这里使用Spark自带的people.txt文件创建RDD，该文件中有三个人名及对应年龄，姓名与年龄使用逗号分隔，该文件可以使用通过下列文件路径找到：~/spark/examples/src/main/resources/people.txt。下面的编码步骤将使用详细的注释以便于理想。</p>
<h4 style="list-style:none;color:rgb(51,51,51);font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;">
People.txt 文件内容</h4>
<div style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
<pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);">Michael, 29
Andy, 30
Justin, 19</pre></div>
<h4 style="list-style:none;color:rgb(51,51,51);font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;">
创建模式（Schema）并将其应用到 textFile RDD</h4>
<div style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
<pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);"># Import data types
from pyspark.sql.types import *
# Create a RDD from `people.txt`
# then convert each line to a tuple.
lines = sc.textFile("examples/src/main/resources/people.txt")
parts = lines.map(lambda l: l.split(","))
people = parts.map(lambda p: (p[0], p[1].strip()))
# encode the schema in a string.
schemaString = "name age"
# Create a type fields
fields = [StructField(field_name, StringType(), True) \
            for field_name in schemaString.split()]
# Create the schema
schema = StructType(fields)
# Apply the schema to the RDD.
schemaPeople = sqlContext.createDataFrame(people, schema)
# In order to query data you need
# to register the DataFrame as a table.
schemaPeople.registerTempTable("people")
# Using sql query all the name from the table
results = sqlContext.sql("SELECT name FROM people")
# The results of SQL queries are RDDs
# and support all the normal RDD operations.
names = results.map(lambda p: "Name: " + p.name)
for name in names.collect():
  print name</pre></div>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
上面的代码输出下列内容：</p>
<h4 style="list-style:none;color:rgb(51,51,51);font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;">
输出</h4>
<div style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
<pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);">Name: Michael
Name: Andy
Name: Justin</pre></div>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
输出内容确实为所有人的名字。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
可以看到，Spark能够非常方便地赋与非结构化数据相应的结构化信息以利于查询，Spark甚至能够将集群节点中的数据进行分割并进行并行分析。目前你可以视Apache Spark为一个能够进行实时数据分析和即席查询分析的快速、通用的大规模数据处理引擎。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
现在让我们来演示如何利用反射机制进行数据分析。</p>
<h4 style="list-style:none;color:rgb(51,51,51);font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;">
在Spark SQL中通过反射机制进行数据分析</h4>
<div style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
<pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);"># First we need to import the following Row class
from pyspark.sql import SQLContext, Row
# Create a RDD peopleAge,
# when this is done the RDD will
# be partitioned into three partitions
peopleAge = sc.textFile("examples/src/main/resources/people.txt")
# Since name and age are separated by a comma let's split them
parts = peopleAge.map(lambda l: l.split(","))
# Every line in the file will represent a row
# with 2 columns name and age.
# After this line will have a table called people
people = parts.map(lambda p: Row(name=p[0], age=int(p[1])))
# Using the RDD create a DataFrame
schemaPeople = sqlContext.createDataFrame(people)
# In order to do sql query on a dataframe,
# you need to register it as a table
schemaPeople.registerTempTable("people")
# Finally we are ready to use the DataFrame.
# Let's query the adults that are aged between 21 and 50
adults = sqlContext.sql("SELECT name FROM people \
       WHERE age &gt;= 21 AND age &lt;= 50")
# loop through names and ages
adults = adults.map(lambda p: "Name: " + p.name)
for Adult in adults.collect():
  print Adult</pre></div>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
上面的代码将输出：</p>
<h4 style="list-style:none;color:rgb(51,51,51);font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;">
输出</h4>
<div style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
<pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);">Name: Michael
Name: Andy</pre></div>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
上面两人的年龄确实在21~50之间。</p>
<h2 style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;">
Spark、SparkSQL与 Cassandra协同使用</h2>
<h4 style="list-style:none;color:rgb(51,51,51);font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;">
Spark 与Cassandra协同使用</h4>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
假设想利用 <a href="http://spark.apache.org/" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Spark</a>和 <a href="http://cassandra.apache.org/" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Cassandra</a>并通过Java编写一个程序。这里给出使Apache Spark与<a href="http://cassandra.apache.org/" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Apache Cassandra</a>能够协同使用的步骤:</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
首先需要导入下列依赖：</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<ul style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;"><li style="list-style:disc;">spark-cassandra-connector_2.10:1.1.1-rc4'</li><li style="list-style:disc;">spark-cassandra-connector-java_2.10:1.1.1'</li><li style="list-style:disc;">spark-streaming_2.10:1.5.0'</li></ul><p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
使用Gradle管理依赖：</p>
<h4 style="list-style:none;color:rgb(51,51,51);font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;">
Spark SQL和Cassandra 协同使用进行数据分析时的Gradle构建文件</h4>
<div style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
<pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);">dependencies {
    //Spark and Cassandra connector to work with java
   compile 'com.datastax.spark:spark-cassandra-connector_2.10:1.1.1-rc4'
    compile 'com.datastax.spark:spark-cassandra-connector-java_2.10:1.1.1'
    compile 'org.apache.spark:spark-streaming_2.10:1.5.0'
}</pre></div>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
然后，设置Spark配置文件，SparkConf用于对Spark的配置属性（如Spark Master及应用程序名称）进行配置，也可以通过set()方法进行任意的键值对如spark.cassandra.connection.host进行配置。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
Spark master为需要连接的集群管理器，支持以几种URL：</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<ul style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;"><li style="list-style:disc;">local，将Spark运行在本地的一个woker线程上，本例使用的便是这种方式</li><li style="list-style:disc;">local[K]，将Spark运行在本地的K个线程上，通常K被设置为机器的CPU核数</li><li style="list-style:disc;">spark://HOST:PORT ，连接给定的集群master，端口必须与master匹配，默认值为7077</li></ul><p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
为使Spark能够使用Cassandra，需要设置spark.cassandra.connection.host为Spark master的主机地址，在本例中为本地主机地址，具体配置如下：</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;">SparkConf conf = new SparkConf();
  ...
        conf.setAppName("TODO spark and cassandra");
        conf.setMaster("local");
        conf.set("spark.cassandra.connection.host", "localhost");</pre>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
完成前面的配置后，便可以着手创建模式（Schema），该模式为Cassandra的表和keyspace ，它可以保存后期需要加载的数据。</p>
<h4 style="list-style:none;color:rgb(51,51,51);font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;">
创建一个CassandraConnector 的连接器实例，同时创建Cassandra的Keyspacce todolist和Table的todolist</h4>
<div style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
<pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);">private void createSchema(JavaSparkContext sc) {
        CassandraConnector connector = 
                   CassandraConnector.apply(sc.getConf());
        try (Session session = connector.openSession()) {
            session.execute(deletekeyspace);
            session.execute(keyspace);
            session.execute("USE todolist");
            session.execute(table);
            session.execute(tableRDD);
        }
 }</pre></div>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
正如上面的代码描述的，我们创建了一个CassandraConnector 的实例，然后执行Cassandra查询语言（Cassandra Query Language，CQL）。我们将在后面的其它文章中对这一主题进行详细讲解。</p>
<h4 style="list-style:none;color:rgb(51,51,51);font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;">
使用Cassandra查询语言</h4>
<div style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
<pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);">/* Delete keyspace todolist if exists. */
String deletekeyspace = "DROP KEYSPACE IF EXISTS todolist";
/* Create keyspace todolist. */
String keyspace = "CREATE KEYSPACE IF NOT EXISTS todolist" +
  " WITH replication = {'class': 'SimpleStrategy'," +
  " 'replication_factor':1}";
/* Create table todolisttable. */
String table = "CREATE TABLE todolist.todolisttable(" +
            + " id text PRIMARY KEY, "
            + " description text, "
            + " category text, "
            + " date timestamp )";
/* Create table temp. */
String tableRDD = "CREATE TABLE todolist.temp(id text PRIMARY KEY, "
            + "description text, "
            + "category text )";</pre></div>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
我们现在有两张表，它们分别是todolisttable 和temp，然后使用Cassandra CQL将todo项的数据加载到todolisttable当中：</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);color:rgb(51,51,51);font-size:14px;">private void loadData(JavaSparkContext sc) {
        CassandraConnector connector = CassandraConnector.apply(sc.getConf());
        try (Session session = connector.openSession()) {
            session.execute(task1);
            session.execute(task2);
            session.execute(task3);
            session.execute(task4);
            session.execute(task5);
            session.execute(task6);
            session.execute(task7);
        }</pre>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
下面给出的是需要加载到Cassandra 中的todo项，最后面跟的是CQL 命令。</p>
<h4 style="list-style:none;color:rgb(51,51,51);font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;">
需要加载到Spark中的 Todo项目，加载时使用Cassandra CQL 命令</h4>
<div style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
<pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);">TodoItem item = new TodoItem("George", "Buy a new computer", "Shopping");
   TodoItem item2 = new TodoItem("John", "Go to the gym", "Sport");
    TodoItem item3 = new TodoItem("Ron", "Finish the homework", "Education");
    TodoItem item4 = new TodoItem("Sam", "buy a car", "Shopping");
    TodoItem item5 = new TodoItem("Janet", "buy groceries", "Shopping");
    TodoItem item6 = new TodoItem("Andy", "go to the beach", "Fun");
    TodoItem item7 = new TodoItem("Paul", "Prepare lunch", "Coking");
//index data
    String task1 = "INSERT INTO todolisttable (ID, Description, Category, Date)"
            + item.toString();
    String task2 = "INSERT INTO todolisttable (ID, Description, Category, Date)"
            + item2.toString();
    String task3 = "INSERT INTO todolisttable (ID, Description, Category, Date)"
            + item3.toString();
    String task4 = "INSERT INTO todolisttable (ID, Description, Category, Date)"
            + item4.toString();
    String task5 = "INSERT INTO todolisttable (ID, Description, Category, Date)"
            + item5.toString();
    String task6 = "INSERT INTO todolisttable (ID, Description, Category, Date)"
            + item6.toString();
    String task7 = "INSERT INTO todolisttable (ID, Description, Category, Date)"
            + item7.toString();</pre></div>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
接下来便可以从Cassandra的todolisttable中查询数据：</p>
<h4 style="list-style:none;color:rgb(51,51,51);font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;">
从Cassandra的todolisttable中查询数据</h4>
<div style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
<pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);">private void queryData(JavaSparkContext sc) {
        CassandraConnector connector = 
                   CassandraConnector.apply(sc.getConf());
        try (Session session = connector.openSession()) {
            ResultSet results = session.execute(query);
            System.out.println("Query all results from cassandra:\n" + results.all());
        }</pre></div>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
将Cassandra的表作为Spark RDD并从中获取数据：</p>
<h4 style="list-style:none;color:rgb(51,51,51);font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;">
将Cassandra的表作为Spark RDD并从中获取数据</h4>
<div style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
<pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);">public  void accessTableWitRDD(JavaSparkContext sc){
        JavaRDD&lt;String&gt; cassandraRDD = javaFunctions(sc).cassandraTable("todolist", "todolisttable")
                .map(new Function&lt;CassandraRow, String&gt;() {
                    @Override
                    public String call(CassandraRow cassandraRow) throws Exception {
                        return cassandraRow.toString();
                    }
                });
    }</pre></div>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
为将Cassandra的表作为RDD读取数据，我们使用cassandraTable("keyspace", "table")方法。cassandraTable 方法要能够起作用，需要利用javaFunctions()方法将sparkcontext作为参数传入。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<h4 style="list-style:none;color:rgb(51,51,51);font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;">
</h4>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
对该RDD来说，其数据类型是CassandraRow</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
打印该RDD：</p>
<h4 style="list-style:none;color:rgb(51,51,51);font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;">
打印Spark RDD的数据</h4>
<div style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
<pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);">System.out.println("\nData as CassandraRows from a RDD: \n" + StringUtils.join(cassandraRDD.toArray(), "\n"));</pre></div>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
我们也可以像读取Cassandra表中的数据一样简单地将RDD保存到Cassandra当中，首先创建一个类型为TodoItem的RDD并填充部分数据，然后将其保存为Cassandra的临时表：</p>
<h4 style="list-style:none;color:rgb(51,51,51);font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;">
创建一个包含todo items 集合的RDD，然后将其保存到Cassandra</h4>
<div style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
<pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);">public void saveRDDToCass(JavaSparkContext sc) {
        List&lt;TodoItem&gt; todos = Arrays.asList(
        new TodoItem("George", "Buy a new computer", "Shopping"),
        new TodoItem("John", "Go to the gym", "Sport"),
        new TodoItem("Ron", "Finish the homework", "Education"),
        new TodoItem("Sam", "buy a car", "Shopping"),
        new TodoItem("Janet", "buy groceries", "Shopping"),
        new TodoItem("Andy", "go to the beach", "Fun"),
        new TodoItem("Paul", "Prepare lunch", "Coking")
        );
        JavaRDD&lt;TodoItem&gt; rdd = sc.parallelize(todos);
        javaFunctions(rdd).writerBuilder("todolist", "temp", mapToRow(TodoItem.class)).saveToCassandra();</pre></div>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
上面我们创建了TodoItem的List集合，然后使用parallelize 方法创建对应的Spark RDD对象rdd，然后通过调用传入参数为rdd对象的writerBuilder 方法将RDD保存为一个keyspace todolist和一个temp表。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
为确保rdd已经保存到Cassandra的temp表中，我们从该表中查询数据：</p>
<h4 style="list-style:none;color:rgb(51,51,51);font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;">
</h4>
<h4 style="list-style:none;color:rgb(51,51,51);font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;">
从Cassandra 中查询temp表数据</h4>
<div style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
<pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);">String query1 = "SELECT * FROM todolist.temp";
ResultSet results1 = session.execute(query1);
System.out.println("\nQuery all results from temp" +
 " table after saving a RDD into Cassandra:\n" +
 results1.all());</pre></div>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
最后，我们给出完整的代码列表，并在代码运行部分给出所有的命令，包括如何从Github中获取源码、如何在机器上运行。</p>
<h2 style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;">
Spark SQL与Cassandra协同使用</h2>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
Spark SQL能够让你查询结构化的数据，包括RDD和任何存储在Cassandra中的数据，为使用Spark SQL 我们需要做以几件事：</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<ul style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;"><li style="list-style:disc;">创建SQLContext （SQLContext构造函数参数为SparkContext)。</li><li style="list-style:disc;">加载parquet 格式数据 (parquet数据格式是一种列式数据存储格式，意味着数据表按列组织而非行组织)。</li><li style="list-style:disc;">数据加载完成后便得到DataFrame。</li><li style="list-style:disc;">额外的信息使得在数据注册成表之后可以使用SQL进行查询</li><li style="list-style:disc;">SQL查询得到的是行对象</li><li style="list-style:disc;">SQL查询是一款强大的工具</li></ul><p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
值得注意的是Spark DataFrame具有普通Spark RDD所拥有的函数，而且在其数据集还具备更多关于列名称和类型的元数据。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
关于Spark SQL有用的信息包括：</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<ul style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;"><li style="list-style:disc;">Spark SQL可以将表缓存到内存当中</li><li style="list-style:disc;">当使用SQL进行数据查询时，返回的结果是RDD</li><li style="list-style:disc;">使用parquets格式读取数据：列存储格式能够过滤掉不需要的数据</li><li style="list-style:disc;">RDD能够以parquet格式文件存储</li><li style="list-style:disc;">JSON对象可以使用jsonRDD方法转换成DataFrame </li></ul><p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
RDD可以并行执行，它是一种弹性分布式数据集，是构成Spark的主要组件，它是数据的一种表示方式。RDD的数据可以分片存储在集群上，正是这些分片数据使得task可以并行执行。RDD的分片越多，其并行执行度越高。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
<a href="http://www.csdn.net/article/2015-11-25/2826325#parquet-files" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Parquet</a>是一种列数据存储格式，它也可以被其它数据处理系统如Hive所支持。<a href="http://parquet.apache.org/" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Apache Parquet</a>是Hadoop生态圈的一部分，它是一种跨语言、跨数据处理框架的列式数据格式。Spark SQL能够读写Parquet文件，这些文件保存了数据的Schema信息。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
让我们来演示如何通过java语言使用Spark SQL进行前面 todo item例子的开发。</p>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
首先，需要在gradle 文件中引入spark-sql 的依赖：</p>
<h4 style="list-style:none;color:rgb(51,51,51);font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;">
在Gradle 中使用Spark SQL 依赖</h4>
<div style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
<pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);">dependencies {
 compile 'org.apache.spark:spark-sql_2.10:1.5.0'
}</pre></div>
<p style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;">
然后，创建Spark configuration 对象并连接Cassandra：</p>
<span style="color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;"></span>
<h4 style="list-style:none;font-size:1em;line-height:24px;">
为Cassandra 创建SparkSpark configuration对象</h4>
<div style="list-style:none;"><pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);">SparkConf conf = new SparkConf();
conf.setAppName("TODO sparkSQL and cassandra");
conf.setMaster("local");
conf.set("spark.cassandra.connection.host", "localhost");</pre></div>
<p style="list-style:none;">
创建Spark Context对象 (JavaSparkContext)。</p>
<h4 style="list-style:none;font-size:1em;line-height:24px;">
创建Spark Context</h4>
<pre style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);">JavaSparkContextsc=newJavaSparkContext(conf);</pre>
<p style="list-style:none;">
创建SQLContext对象以便使用SQL连接Cassandra：</p>
<h4 style="list-style:none;font-size:1em;line-height:24px;">
创建Spark SQL Context</h4>
<div style="list-style:none;">
<pre class="brush:" style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);">JavaSparkContext sc = new JavaSparkContext(conf);</pre>
</div>
<p style="list-style:none;">
通过SQLContext便能注册RDD并利用Spark SQL进行查询操作。</p>
<p style="list-style:none;">
然后，创建RDD对象（rdd）并加载数据(TodoItems)：</p>
<h4 style="list-style:none;font-size:1em;line-height:24px;">
RDD 加载 TodoItems</h4>
<div style="list-style:none;">
<pre class="brush:" style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);"> List&lt;TodoItem&gt; todos = Arrays.asList(
                new TodoItem("George", "Buy a new computer", "Shopping"),
                new TodoItem("John", "Go to the gym", "Sport"),
                new TodoItem("Ron", "Finish the homework", "Education"),
                new TodoItem("Sam", "buy a car", "Shopping"),
                new TodoItem("Janet", "buy groceries", "Shopping"),
                new TodoItem("Andy", "go to the beach", "Fun"),
                new TodoItem("Paul", "Prepare lunch", "Cooking")
        );
        JavaRDD&lt;TodoItem&gt; rdd = sc.parallelize(todos);</pre>
</div>
<p style="list-style:none;">
需要注意的是我们使用parallelize方法将所有的Todo数据加载到整个Spark集群。<a href="https://spark.apache.org/docs/1.5.0/api/java/org/apache/spark/api/java/JavaRDD.html" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">JavaRDD</a>通过context.parallelize方法产生。</p>
<p style="list-style:none;">
然后，通过sqlContext创建DataFrame：</p>
<h4 style="list-style:none;font-size:1em;line-height:24px;">
通过sqlContext创建DataFrame</h4>
<div style="list-style:none;">
<pre class="brush:" style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);">DataFrame dataframe = sqlContext.createDataFrame(rdd, TodoItem.class);</pre>
</div>
<p style="list-style:none;">
Dataframe从 TodoItem.class获取对应的schema。</p>
<p style="list-style:none;">
然后，将dataframe注册成名为todo的表：</p>
<h4 style="list-style:none;font-size:1em;line-height:24px;">
将DataFrame注册成名为todo的表</h4>
<div style="list-style:none;">
<pre class="brush:" style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);">sqlContext.registerDataFrameAsTable(dataframe, "todo");</pre>
</div>
<p style="list-style:none;">
这样后面便可以使用todo进行DataFrame数据的查询。</p>
<p style="list-style:none;">
到这一步便可以使用Spark SQL提供的所有操作，首先对todo items进行计数，它将加载数据到内存以便进行更快速的查询操作：</p>
<h4 style="list-style:none;font-size:1em;line-height:24px;">
获取DataFrame中TODO items的数量</h4>
<div style="list-style:none;">
<pre class="brush:" style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);">System.out.println("Total number of TodoItems = [" + rdd.count() + "]\n");</pre>
</div>
<p style="list-style:none;">
最后，使用SQL进行数据的查询：</p>
<h4 style="list-style:none;font-size:1em;line-height:24px;">
使用Spark SQL查询Todo Items并显示查询结果</h4>
<div style="list-style:none;">
<pre class="brush:" style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);"> DataFrame result = sqlContext.sql("SELECT * from todo");
        System.out.println("Show the DataFrame result:\n");
        result.show();
        System.out.println("Select the id column and show its contents:\n");
        result.select("id").show();</pre>
</div>
<p style="list-style:none;">
可以在本文最后的运行部分获取相关代码及如何运行这些代码的教程。这里给出本文例子的完整代码。</p>
<p style="list-style:none;">
</p>
<h2 style="list-style:none;">完整代码清单</h2>
<p style="list-style:none;">
</p>
<h4 style="list-style:none;font-size:1em;line-height:24px;">
SparkApp.java清单</h4>
<div style="list-style:none;">
<pre class="brush:" style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);">package com.example;
import com.datastax.driver.core.ResultSet;
import com.datastax.driver.core.Session;
import com.datastax.spark.connector.cql.CassandraConnector;
import com.datastax.spark.connector.japi.CassandraRow;
import org.apache.commons.lang.StringUtils;
import org.apache.spark.SparkConf;
import org.apache.spark.api.java.JavaRDD;
import org.apache.spark.api.java.JavaSparkContext;
import org.apache.spark.api.java.function.Function;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import java.io.Serializable;
import java.util.Arrays;
import java.util.List;
import static com.datastax.spark.connector.japi.CassandraJavaUtil.javaFunctions;
import static com.datastax.spark.connector.japi.CassandraJavaUtil.mapToRow;
//import org.apache.cassandra.cql.BatchStatement;
/**
 * Created by fadi on 5/18/15.
 */
public class SparkApp implements Serializable {
    static final Logger logger = LoggerFactory.getLogger(SparkApp.class);
    TodoItem item = new TodoItem("George", "Buy a new computer", "Shopping");
    TodoItem item2 = new TodoItem("John", "Go to the gym", "Sport");
    TodoItem item3 = new TodoItem("Ron", "Finish the homework", "Education");
    TodoItem item4 = new TodoItem("Sam", "buy a car", "Shopping");
    TodoItem item5 = new TodoItem("Janet", "buy groceries", "Shopping");
    TodoItem item6 = new TodoItem("Andy", "go to the beach", "Fun");
    TodoItem item7 = new TodoItem("Paul", "Prepare lunch", "Coking");
    String keyspace = "CREATE KEYSPACE IF NOT EXISTS todolist  WITH replication = {'class': 'SimpleStrategy', 'replication_factor':1}";
    //index data
    String task1 = "INSERT INTO todolisttable (ID, Description, Category, Date)"
            + item.toString();
    String task2 = "INSERT INTO todolisttable (ID, Description, Category, Date)"
            + item2.toString();
    String task3 = "INSERT INTO todolisttable (ID, Description, Category, Date)"
            + item3.toString();
    String task4 = "INSERT INTO todolisttable (ID, Description, Category, Date)"
            + item4.toString();
    String task5 = "INSERT INTO todolisttable (ID, Description, Category, Date)"
            + item5.toString();
    String task6 = "INSERT INTO todolisttable (ID, Description, Category, Date)"
            + item6.toString();
    String task7 = "INSERT INTO todolisttable (ID, Description, Category, Date)"
            + item7.toString();
    //delete keyspace
    String deletekeyspace = "DROP KEYSPACE IF EXISTS todolist";
    //delete table
    String deletetable = "DROP TABLE todolisttable";
    //create table
    String table = "CREATE TABLE todolist.todolisttable(id text PRIMARY KEY, "
            + "description text, "
            + "category text, "
            + "date timestamp )";
    String tableRDD = "CREATE TABLE todolist.temp(id text PRIMARY KEY, "
            + "description text, "
            + "category text )";
    //Query all data
    String query = "SELECT * FROM todolist.todolisttable";
    String query1 = "SELECT * FROM todolist.temp";
    //Update table
    String update = "UPDATE todolisttable SET Category='Fun',Description='Go to the beach' WHERE ID='Ron'";
    //Deleting data where the index id = George
    String delete = "DELETE FROM todolisttable WHERE ID='George'";
    //Deleting all data
    String deleteall = "TRUNCATE todolisttable";
//---------------------------------------------------------------------------------
    private transient SparkConf conf;
    private SparkApp(SparkConf conf) {
        this.conf = conf;
    }
    private void run() {
        JavaSparkContext sc = new JavaSparkContext(conf);
        createSchema(sc);
        loadData(sc);
        saveRDDToCassandra(sc);
        queryData(sc);
        accessTableWitRDD(sc);
        sc.stop();
    }
    private void createSchema(JavaSparkContext sc) {
        CassandraConnector connector = CassandraConnector.apply(sc.getConf());
        try (Session session = connector.openSession()) {
            session.execute(deletekeyspace);
            session.execute(keyspace);
            session.execute("USE todolist");
            session.execute(table);
            session.execute(tableRDD);
        }
    }
    private void loadData(JavaSparkContext sc) {
        CassandraConnector connector = CassandraConnector.apply(sc.getConf());
        try (Session session = connector.openSession()) {
            session.execute(task1);
            session.execute(task2);
            session.execute(task3);
            session.execute(task4);
            session.execute(task5);
            session.execute(task6);
            session.execute(task7);
        }
    }
    private void queryData(JavaSparkContext sc) {
        CassandraConnector connector = CassandraConnector.apply(sc.getConf());
        try (Session session = connector.openSession()) {
            ResultSet results = session.execute(query);
            System.out.println("\nQuery all results from cassandra's todolisttable:\n" + results.all());
            ResultSet results1 = session.execute(query1);
            System.out.println("\nSaving RDD into a temp table in casssandra then query all results from cassandra:\n" + results1.all());
        }
    }
    public  void accessTableWitRDD(JavaSparkContext sc){
        JavaRDD&lt;String&gt; cassandraRDD = javaFunctions(sc).cassandraTable("todolist", "todolisttable")
                .map(new Function&lt;CassandraRow, String&gt;() {
                    @Override
                    public String call(CassandraRow cassandraRow) throws Exception {
                        return cassandraRow.toString();
                    }
                });
        System.out.println("\nReading Data from todolisttable in Cassandra with a RDD: \n" + StringUtils.join(cassandraRDD.toArray(), "\n"));
        // javaFunctions(cassandraRDD).writerBuilder("todolist", "todolisttable", mapToRow(String.class)).saveToCassandra();
    }
    public void saveRDDToCassandra(JavaSparkContext sc) {
        List&lt;TodoItem&gt; todos = Arrays.asList(
                new TodoItem("George", "Buy a new computer", "Shopping"),
                new TodoItem("John", "Go to the gym", "Sport"),
                new TodoItem("Ron", "Finish the homework", "Education"),
                new TodoItem("Sam", "buy a car", "Shopping"),
                new TodoItem("Janet", "buy groceries", "Shopping"),
                new TodoItem("Andy", "go to the beach", "Fun"),
                new TodoItem("Paul", "Prepare lunch", "Coking")
        );
        JavaRDD&lt;TodoItem&gt; rdd = sc.parallelize(todos);
        javaFunctions(rdd).writerBuilder("todolist", "temp", mapToRow(TodoItem.class)).saveToCassandra();
    }
//----------------------------------------------------------------------------------------------------------------------------
    public static void main( String args[] )
    {
        SparkConf conf = new SparkConf();
        conf.setAppName("TODO spark and cassandra");
        conf.setMaster("local");
        conf.set("spark.cassandra.connection.host", "localhost");
        SparkApp app = new SparkApp(conf);
        app.run();
    }
}</pre>
</div>
<p style="list-style:none;">
</p>
<h4 style="list-style:none;font-size:1em;line-height:24px;">
SparkSQLApp.java清单</h4>
<div style="list-style:none;">
<pre class="brush:" style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);">package com.example;
import org.apache.spark.SparkConf;
import org.apache.spark.api.java.JavaRDD;
import org.apache.spark.api.java.JavaSparkContext;
import org.apache.spark.sql.DataFrame;
import org.apache.spark.sql.SQLContext;
import java.util.Arrays;
import java.util.List;
/**
 * Created by fadi on 6/14/15.
 */
public class SparkSQLApp {
    private transient SparkConf conf;
    private SparkSQLApp(SparkConf conf) {
        this.conf = conf;
    }
    private void run() {
        JavaSparkContext sc = new JavaSparkContext(conf);
         SQLContext sqlContext = new SQLContext(sc);
        createDataframe(sc, sqlContext);
        querySQLData(sqlContext);
        sc.stop();
    }
    public void createDataframe(JavaSparkContext sc, SQLContext sqlContext ) {
        List&lt;TodoItem&gt; todos = Arrays.asList(
                new TodoItem("George", "Buy a new computer", "Shopping"),
                new TodoItem("John", "Go to the gym", "Sport"),
                new TodoItem("Ron", "Finish the homework", "Education"),
                new TodoItem("Sam", "buy a car", "Shopping"),
                new TodoItem("Janet", "buy groceries", "Shopping"),
                new TodoItem("Andy", "go to the beach", "Fun"),
                new TodoItem("Paul", "Prepare lunch", "Cooking")
        );
        JavaRDD&lt;TodoItem&gt; rdd = sc.parallelize(todos);
        DataFrame dataframe =   sqlContext.createDataFrame(rdd, TodoItem.class);
        sqlContext.registerDataFrameAsTable(dataframe, "todo");
        System.out.println("Total number of TodoItems = [" + rdd.count() + "]\n");
    }
    public void querySQLData(SQLContext sqlContext) {
        DataFrame result = sqlContext.sql("SELECT * from todo");
        System.out.println("Show the DataFrame result:\n");
        result.show();
        System.out.println("Select the id column and show its contents:\n");
        result.select("id").show();
    }
    public static void main( String args[] )
    {
        SparkConf conf = new SparkConf();
        conf.setAppName("TODO sparkSQL and cassandra");
        conf.setMaster("local");
        conf.set("spark.cassandra.connection.host", "localhost");
        SparkSQLApp app = new SparkSQLApp(conf);
        app.run();
    }
}</pre>
</div>
<p style="list-style:none;">
</p>
<h4 style="list-style:none;font-size:1em;line-height:24px;">
Todoitem.java清单</h4>
<div style="list-style:none;">
<pre class="brush:" style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);">package com.example;
import java.io.Serializable;
import java.time.LocalDateTime;
public class TodoItem implements Serializable {
    private String id;
    private String description;
    private String category;
    private final LocalDateTime date = LocalDateTime.now();
    public TodoItem(String id, String description, String category) {
        this.id = id;
        this.description = description;
        this.category = category;
    }
    public String getId(){
        return this.id;
    }
    public  String getDescription(){
        return this.description;
    }
    public String getCategory(){
        return this.category;
    }
    public void setId(String id) {
        this.id = id;
    }
    public void setDescription(String description) {
        this.description = description;
    }
    public void setCategory(String category) {
        this.category = category;
    }
    @Override
    public String toString() {
        return  "VALUES ( " + "'" + this.id +"'" + ", " + "'" + this.description +"'" + ", " + "'" + this.category +"'" +", "  + "'" + date +"'" + ")";
    }
}</pre>
</div>
<p style="list-style:none;">
</p>
<h4 style="list-style:none;font-size:1em;line-height:24px;">
build.gradle清单</h4>
<div style="list-style:none;">
<pre class="brush:" style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);">apply plugin: 'idea'
apply plugin: 'java'
apply plugin: 'jetty'
apply plugin: 'application'
applicationName = 'todocass'
applicationDefaultJvmArgs = ["-Dlogback.configurationFile=etc/todosolr/logging.xml"]
sourceCompatibility = 1.8
version = '1.0'
repositories {
    mavenLocal()
    mavenCentral()
}
task runSpark(type: JavaExec, dependsOn: 'classes') {
    main = "com.example.SparkApp"
    classpath = sourceSets.main.runtimeClasspath
}
task runSparkSQL(type: JavaExec, dependsOn: 'classes') {
    main = "com.example.SparkSQLApp"
    classpath = sourceSets.main.runtimeClasspath
}
dependencies {
    //spark and cassandra connector to work with java
    compile 'com.datastax.spark:spark-cassandra-connector_2.10:1.1.1-rc4'
    compile 'com.datastax.spark:spark-cassandra-connector-java_2.10:1.1.1'
    compile 'org.apache.spark:spark-streaming_2.10:1.5.0'
    compile 'org.apache.spark:spark-sql_2.10:1.5.0'
    //logback dependencies
    compile 'ch.qos.logback:logback-core:1.1.3'
    compile 'ch.qos.logback:logback-classic:1.1.3'
    compile 'org.slf4j:slf4j-api:1.7.12'
}
//Install/copy tasks
task copyDist(type: Copy) {
    dependsOn "installApp"
    from "$buildDir/install/todocass"
    into 'opt/todocass'
}
task copyLog(type: Copy) {
    from "src/main/resources/logback.xml"
    into "etc/todocass/"
}
task copyLogToImage(type: Copy) {
    from "src/main/resources/logback.xml"
    into "image-todo-cass/etc"
}
task copyDistToImage(type: Copy) {
    dependsOn "installApp"
    from "$buildDir/install/todocass"
    into "$projectDir/image-todo-cass/opt/todocass"
}</pre>
</div>
<p style="list-style:none;">
</p>
<h2 style="list-style:none;">运行</h2>
<p style="list-style:none;">
</p>
<p style="list-style:none;">
首先运行Cassandra：</p>
<p style="list-style:none;">
</p>
<pre class="brush:" style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);">cd ~/cassandra
bin/cassandra -f</pre>
<p style="list-style:none;">
</p>
<p style="list-style:none;">
获取代码：</p>
<p style="list-style:none;">
</p>
<pre class="brush:" style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);">git clone <a href="https://github.com/MammatusTech/Spark-Course.git" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">https://github.com/MammatusTech/Spark-Course.git</a></pre>
<p style="list-style:none;">
</p>
<p style="list-style:none;">
然后构建Spark-Course：</p>
<p style="list-style:none;">
</p>
<pre class="brush:" style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);">cd Spark-Course
gradle clean build</pre>
<p style="list-style:none;">
</p>
<p style="list-style:none;">
首先，运行SparkApp，这是Spark与Cassandra协同工作的例子：</p>
<p style="list-style:none;">
</p>
<pre class="brush:" style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);">gradle runSpark</pre>
<p style="list-style:none;">
</p>
<p style="list-style:none;">
运行时将看到下列内容：</p>
<p style="list-style:none;">
</p>
<pre class="brush:" style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);">Query all results from cassandra's todolisttable:
[Row[George, Shopping, Mon Jun 15 13:36:07 PDT 2015, Buy a new computer], Row[Janet, Shopping, Mon Jun 15 13:36:07 PDT 2015, buy groceries], Row[John, Sport, Mon Jun 15 13:36:07 PDT 2015, Go to the gym], Row[Paul, Coking, Mon Jun 15 13:36:07 PDT 2015, Prepare lunch], Row[Ron, Education, Mon Jun 15 13:36:07 PDT 2015, Finish the homework], Row[Andy, Fun, Mon Jun 15 13:36:07 PDT 2015, go to the beach], Row[Sam, Shopping, Mon Jun 15 13:36:07 PDT 2015, buy a car]]
Saving RDD into a temp table in casssandra then query all results from cassandra:
[Row[George, Shopping, Buy a new computer], Row[Janet, Shopping, buy groceries], Row[John, Sport, Go to the gym], Row[Paul, Coking, Prepare lunch], Row[Ron, Education, Finish the homework], Row[Andy, Fun, go to the beach], Row[Sam, Shopping, buy a car]]
Reading Data from todolisttable in Cassandra with a RDD:
CassandraRow{id: Paul, category: Coking, date: 2015-06-15 13:36:07-0700, description: Prepare lunch}
CassandraRow{id: Sam, category: Shopping, date: 2015-06-15 13:36:07-0700, description: buy a car}
CassandraRow{id: Ron, category: Education, date: 2015-06-15 13:36:07-0700, description: Finish the homework}
CassandraRow{id: Janet, category: Shopping, date: 2015-06-15 13:36:07-0700, description: buy groceries}
CassandraRow{id: John, category: Sport, date: 2015-06-15 13:36:07-0700, description: Go to the gym}
CassandraRow{id: George, category: Shopping, date: 2015-06-15 13:36:07-0700, description: Buy a new computer}
CassandraRow{id: Andy, category: Fun, date: 2015-06-15 13:36:07-0700, description: go to the beach}</pre>
<p style="list-style:none;">
</p>
<p style="list-style:none;">
然后运行SparkSQL APP，这是Spark SQL与Cassandra协同工作的例子：</p>
<p style="list-style:none;">
</p>
<pre class="brush:" style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);">gradle runSparkSQL:</pre>
<p style="list-style:none;">
</p>
<p style="list-style:none;">
运行时将看到下列内容：</p>
<p style="list-style:none;">
</p>
<pre class="brush:" style="list-style:none;overflow:hidden;background-color:rgb(247,247,247);border:1px solid rgb(221,221,221);">Total number of TodoItems = [7]
Show the DataFrame result:
+---------+-------------------+------+
| category|        description|    id|
+---------+-------------------+------+
| Shopping| Buy a new computer|George|
|    Sport|      Go to the gym|  John|
|Education|Finish the homework|   Ron|
| Shopping|          buy a car|   Sam|
| Shopping|      buy groceries| Janet|
|      Fun|    go to the beach|  Andy|
|  Cooking|      Prepare lunch|  Paul|
+---------+-------------------+------+
Select the id column and show its contents:
+------+
|    id|
+------+
|George|
|  John|
|   Ron|
|   Sam|
| Janet|
|  Andy|
|  Paul|
+------+</pre>
<p style="list-style:none;">
</p>
<p style="list-style:none;">
</p>
<h2 style="list-style:none;">结束语</h2>
<p style="list-style:none;">
</p>
<p style="list-style:none;">
本文展示了Spark在实时数据分析方面的强大功能，介绍了Spark生态系统中的一个重要部分——Spark SQL和DataFrames。DataFrames构建在RDD之上，RDD的数据分片使得DataFrame能够被并行处理。</p>
<p style="list-style:none;">
在本文中，我们也演示了如何将Spark和Spark SQL与Cassandra结合起来使用。</p>
<p style="list-style:none;">
Apache Spark已经被证明是Hadoop的天然继承，并引领着大数据技术趋势。它在Hadoop生态圈中运行良好，是实现大数据分析的一条快速通道。Spark提供一套易于使用的API，具备大规模分布式任务下的数据分析能力。Apache Spark 使得普通开发者也具备大数据的实时数据分析能力。 Spark SQL是Apache Spark提供的API的一个实例，易于使用而功能强大。</p>
<p style="list-style:none;">
</p>
<h2 style="list-style:none;">参考文献</h2>
<p style="list-style:none;">
</p>
<p style="list-style:none;">
</p>
<ul style="list-style:none;"><li style="list-style:disc;"> <a href="http://spark.apache.org/docs/" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Spark Docs</a></li><li style="list-style:disc;"> <a href="https://github.com/apache/spark" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Spark Github</a></li><li style="list-style:disc;"> <a href="https://databricks.com/spark/about" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">About Spark</a></li><li style="list-style:disc;"> <a href="http://www.mammatustech.com/" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Mammatus Tech</a></li><li style="list-style:disc;"> <a href="http://www.mammatustech.com/apache-spark-course-quick-start-real-time-data-analytics" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Mammatus Apache Spark Course</a></li></ul><p style="list-style:none;">
</p>
<p style="list-style:none;">
<strong>原文链接：</strong><a href="https://dzone.com/articles/analytics-with-apache-spark-tutorial-part-2-spark" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;line-height:24px;">Analytics with Apache Spark Tutorial Part 2: Spark SQL</a><span style="line-height:24px;">（译者/牛亚真
 审校/朱正贵 责编/仲浩）</span></p>
            </div>
                </div>