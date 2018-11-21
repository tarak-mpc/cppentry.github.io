---
layout:     post
title:      spark 将dataframe 数据写入hive分区表
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>DataFrame 将数据写入hive中时，默认的是hive默认数据库,insertInto没有指定数据库的参数，数据写入hive表或者hive表分区中：</p>

<p>1、将DataFrame数据写入到hive表中</p>

<p>从DataFrame类中可以看到与hive表有关的写入API有一下几个：</p>

<p>registerTempTable(tableName:String):Unit,</p>

<p>inserInto(tableName:String):Unit</p>

<p>insertInto(tableName:String,overwrite:Boolean):Unit</p>

<p>saveAsTable(tableName:String,source:String,mode:SaveMode,options:Map[String,String]):Unit</p>

<p>有很多重载函数，不一一列举</p>

<p>registerTempTable函数是创建spark临时表</p>

<p>insertInto函数是向表中写入数据，可以看出此函数不能指定数据库和分区等信息，不可以直接写入。</p>

<p>向hive数据仓库写入数据必须指定数据库，hive数据表建立可以在hive上建立，或者使用hiveContext.sql("create table .....")</p>

<p>下面语句是向指定数据库数据表中写入数据：</p>

<p><code>case</code> <code>class Person(</code><code>name</code><code>:String,col1:</code><code>Int</code><code>,col2:String)</code></p>

<p><code>val sc = new org.apache.spark.SparkContext   </code></p>

<p><code>val hiveContext = new org.apache.spark.sql.hive.HiveContext(sc)</code></p>

<p><code>import hiveContext.implicits._</code></p>

<p><code>hiveContext.sql(</code><code>"use DataBaseName"</code><code>)</code></p>

<p><code>val data = sc.textFile(</code><code>"path"</code><code>).map(x=&gt;x.split(</code><code>"\\s+"</code><code>)).map(x=&gt;Person(x(0),x(1).toInt,x(2)))</code></p>

<p><code>data.toDF().insertInto(</code><code>"tableName"</code><code>)</code></p>

<p>创建一个case类将RDD中数据类型转为case类类型，然后通过toDF转换DataFrame,调用insertInto函数时，首先指定数据库，使用的是hiveContext.sql("use DataBaseName") 语句，就可以将DataFrame数据写入hive数据表中了</p>

<p>2、将DataFrame数据写入hive指定数据表的分区中</p>

<p>hive数据表建立可以在hive上建立，或者使用hiveContext.sql("create table....") ,使用saveAsTable时数据存储格式有限，默认格式为parquet，将数据写入分区的思路是：首先将DataFrame数据写入临时表，之后由hiveContext.sql语句将数据写入hive分区表中，具体操作如下：</p>

<p><code>case</code> <code>class Person(</code><code>name</code><code>:String,col1:</code><code>Int</code><code>,col2:String)</code></p>

<p><code>val sc = new org.apache.spark.SparkContext   </code></p>

<p><code>val hiveContext = new org.apache.spark.sql.hive.HiveContext(sc)</code></p>

<p><code>import hiveContext.implicits._</code></p>

<p><code>hiveContext.sql(</code><code>"use DataBaseName"</code><code>)</code></p>

<p><code>val data = sc.textFile(</code><code>"path"</code><code>).map(x=&gt;x.split(</code><code>"\\s+"</code><code>)).map(x=&gt;Person(x(0),x(1).toInt,x(2)))</code></p>

<p><code>data.toDF().registerTempTable(</code><code>"table1"</code><code>)</code></p>

<p><code>hiveContext.sql(</code><code>"insert into table2 partition(date='2015-04-02') select name,col1,col2 from table1"</code><code>)</code></p>

<p><code>上面代码是spark版本1.6</code></p>

<p><code>下面代码是spark版本2.0及以上版本</code></p>

<pre>
val session = SparkSession.builder().appName("WarehouseInventoryByNewMysqlSnap").enableHiveSupport().getOrCreate()
val sc: SparkContext=session.sparkContext
session.sql("use bi_work")
import session.implicits._
val data = sc.textFile("path").map(x=&gt;x.split("\\s+")).map(x=&gt;Person(x(0),x(1).toInt,x(2)))
data.toDF().registerTempTable("table1")
session.sql("insert into table2 partition(date='2015-04-02') select name,col1,col2 from table1")
</pre>

<p>这边捎带介绍一下hive创建分区表</p>

<p>hive分区表：是指在创建表时指定的partition的分区空间，若需要创建有分区的表，需要在create表的时候调用可选参数partitioned by </p>

<p>注意：</p>

<p>1、一个表可以拥有一个或者多个分区，每个分区以文件夹的形式单独存在表文件夹的目录下。</p>

<p>2、hive的表和列名不区分大小写</p>

<p>3、分区是以字段的形式在表的结构中存在，通过desc table_name 命令可以查看到字段存在，该字段是分区的标识</p>

<p>4、建表的语句：</p>

<p>CREATE EXTERNAL TABLE bi_work.`dw_inventory_snap`<br>
(`warehouse_id` string COMMENT '',<br>
 `internal_id` string COMMENT '', <br>
 `logical_inventory` string COMMENT '', <br>
 `create_time` timestamp COMMENT '')<br><strong>PARTITIONED BY (`snap_time` string) --指定分区</strong><br>
row format delimited fields terminated by '\t'</p>

<p> </p>            </div>
                </div>