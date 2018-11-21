---
layout:     post
title:      大数据系列之实时计算Spark（七）SparkSQL介绍
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u011444062/article/details/81590724				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>1.SparkSQL介绍</p>

<p>Spark SQL是用于<span style="color:#f33b45;">结构化数据处理的Spark模块</span>。与基本Spark RDD API不同，Spark SQL提供的接口为Spark提供了更多关于数据结构和正在执行的计算的信息。在内部，Spark SQL使用这些额外的信息来执行额外的优化。有几种方法可以与Spark SQL交互，包括<span style="color:#f33b45;">SQL和数据集API</span>。在计算结果时，使用相同的执行引擎，而不依赖于使用哪种API /语言来表示计算。这种统一意味着开发人员可以很容易地在不同的API之间来回切换，基于这种切换提供了表达给定转换的最自然的方式。</p>

<p>                 <img alt="" class="has" height="279" src="https://img-blog.csdn.net/20180811205017181?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE0NDQwNjI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="734"><br><span style="color:#f33b45;">1.1.SQL:</span></p>

<p><span style="color:#f33b45;">Spark SQL的一个用途是执行SQL查询。spark SQL也可以用于从现有的配置单元安装中读取数据。请参阅配置单元表部分。当从另一种编程语言运行SQL时，结果将作为数据集/数据帧返回。您也可以使用命令行或通过JDBC / ODBC与SQL接口交互。</span></p>

<p><span style="color:#f33b45;">1.2.数据集API和数据帧</span></p>

<p><span style="color:#f33b45;">数据集是分布式数据集合。dataset是Spark 1.6中添加的一个新界面，它提供了RDDs的好处(</span>(strong typing, ability to use powerful lambda functions<span style="color:#f33b45;">)和Spark SQL的优化执行引擎的好处。数据集可以由JVM对象构建，然后使用功能转换(地图、平面地图、过滤器等)进行操作。) .数据集API在Scala和Java中可用。python不支持数据集API。但是由于Python的动态特性，数据集API的许多好处已经可用(即，您可以按自然名称row . columnname访问一行的字段)。R的情况类似。<br>
数据框（DataSet&lt;Row&gt; === DataFrame ==&gt; //类似于table操作）是组织成命名列的数据集。它在概念上等同于关系数据库中的表或R / Python中的数据帧。数据帧可以从各种来源构建，例如:结构化数据文件、Hive中的表、外部数据库或现有的RDDs。数据帧API在Scala、Java、Python和r中可用。在Scala和Java中，数据帧由行数据集表示。在Scala API中，数据帧只是数据集[行的类型别名]。而在Java API中，用户需要使用数据集&amp;lt; Row &amp;gt;来表示数据帧。<br>
在本文档中，我们经常将行的Scala / Java数据集称为数据帧。</span></p>

<p><span style="color:#f33b45;">一些数据集API:可以使用数据框对象直接操纵</span></p>

<p><img alt="" class="has" height="275" src="https://img-blog.csdn.net/20180811210145318?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE0NDQwNjI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="926"></p>

<p>总结：SparkSQL使用两种方式来操作数据集，分别是SQL语句和数据集API，而SparkSQL的<span style="color:#f33b45;">数据框（DataSet&lt;Row&gt; === DataFrame ==&gt; //类似于table操作）是组织成命名列的数据集</span>。请注意：其底层依然是转换成RDD，进行一系列操作。</p>

<p>1.3使用spark-shell体验SparkSQL</p>

<p>//创建样例类<br>
    $scala&gt;case class Customer(id:Int,name:String,age:Int)<br>
    //构造数据<br>
    $scala&gt;val arr = Array("1,tom,12","2,tomas,13","3,tomasLee,14")<br>
    $scala&gt;val rdd1 = sc.makeRDD(arr)<br>
    //创建对象rdd<br>
    $scala&gt;val rdd2 = rdd1.map(e=&gt;{val arr=e.split(",");Customer(arr(0).toInt,arr(1),arr(2).toInt)})<br>
    //通过rdd创建数据框<br>
    $scala&gt;val df = spark.createDataFrame(rdd2);<br>
    //打印表结构<br>
    $scala&gt;df.printSchema<br>
    $scala&gt;df.show            //查询数据</p>

<p>    <img alt="" class="has" height="211" src="https://img-blog.csdn.net/20180811205944382?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE0NDQwNjI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="794"></p>

<p>           </p>

<p>//创建临时视图<br>
    $scala&gt;df.createTempView("customers")<br>
    $scala&gt;val df2 = spark.sql("select * from customers")<br>
    $scala&gt;spark.sql("select * from customers").show        //使用sql语句<br>
    $scala&gt;val df1 = spark.sql("select * from cusotmers where id &lt; 2");<br>
    $scala&gt;val df2 = spark.sql("select * from cusotmers where id &gt; 2");<br>
    $scala&gt;df1.createTempView("c1")<br>
    $scala&gt;df2.createTempView("c2")<br>
    $scala&gt;spark.sql("select * from c1 union select * from c2").show()<br>
    $scala&gt;df1.union(df2);<br>
    $scala&gt;spark.sql("select id,name from customers").show<br>
    $scala&gt;df.selectExpr("id","name").show<br>
    $scala&gt;df.where("name like 't%'").show<br>
    <br>
    //映射<br>
    $scala&gt;df.map(_.getAs[Int]("age")).reduce(_+_)            //聚合操作DataSet[Int]<br>
    $scala&gt;df.agg(sum("age"),max("age"),min("age")).show            //聚合函数</p>

<p>              <img alt="" class="has" height="147" src="https://img-blog.csdn.net/20180811205313621?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTE0NDQwNjI=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="631"></p>            </div>
                </div>