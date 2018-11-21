---
layout:     post
title:      SparkSQL操作Hive Table
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>Spark SQL支持对Hive的读写操作。然而因为Hive有很多依赖包，所以这些依赖包没有包含在默认的Spark包里面。如果Hive依赖的包能在classpath找到，Spark将会自动加载它们。需要注意的是，这些Hive依赖包必须复制到所有的工作节点上，因为它们为了能够访问存储在Hive的数据，会调用Hive的序列化和反序列化(SerDes)包。Hive的配置文件<strong>hive-site.xml</strong>、<strong>core-site.xml</strong>(security配置)和<strong>hdfs-site.xml</strong>(HDFS配置)是保存在conf目录下面。 <br>
当使用Hive时，必须初始化一个支持Hive的SparkSession，用户即使没有部署一个Hive的环境仍然可以使用Hive。当没有配置hive-site.xml时，Spark会自动在当前应用目录创建metastore_db和创建由spark.sql.warehouse.dir配置的目录，如果没有配置，默认是当前应用目录下的spark-warehouse目录。 <br><strong>注意：</strong>从Spark 2.0.0版本开始，hive-site.xml里面的hive.metastore.warehouse.dir属性已经被spark.sql.warehouse.dir替代，用于指定warehouse的默认数据路径(必须有写权限)。</p>

<pre class="has">
<code>import java.io.Serializable;  
import java.util.ArrayList;  
import java.util.List;  

import org.apache.spark.api.java.function.MapFunction;  
import org.apache.spark.sql.Dataset;  
import org.apache.spark.sql.Encoders;  
import org.apache.spark.sql.Row;  
import org.apache.spark.sql.SparkSession;  

public static class Record implements Serializable {  
  private int key;  
  private String value;  

  public int getKey() {  
    return key;  
  }  

  public void setKey(int key) {  
    this.key = key;  
  }  

  public String getValue() {  
    return value;  
  }  

  public void setValue(String value) {  
    this.value = value;  
  }  
}  

// warehouseLocation points to the default location for managed databases and tables  
String warehouseLocation = "/spark-warehouse";  
// init spark session with hive support  
SparkSession spark = SparkSession  
  .builder()  
  .appName("Java Spark Hive Example")  
  .master("local[*]")  
  .config("spark.sql.warehouse.dir", warehouseLocation)  
  .enableHiveSupport()  
  .getOrCreate();  

spark.sql("CREATE TABLE IF NOT EXISTS src (key INT, value STRING)");  
spark.sql("LOAD DATA LOCAL INPATH 'examples/src/main/resources/kv1.txt' INTO TABLE src");  

// Queries are expressed in HiveQL  
spark.sql("SELECT * FROM src").show();  
// +---+-------+  
// |key|  value|  
// +---+-------+  
// |238|val_238|  
// | 86| val_86|  
// |311|val_311|  
// ...  
// only showing top 20 rows  

// Aggregation queries are also supported.  
spark.sql("SELECT COUNT(*) FROM src").show();  
// +--------+  
// |count(1)|  
// +--------+  
// |    500 |  
// +--------+  

// The results of SQL queries are themselves DataFrames and support all normal functions.  
Dataset&lt;Row&gt; sqlDF = spark.sql("SELECT key, value FROM src WHERE key &lt; 10 ORDER BY key");  

// The items in DaraFrames are of type Row, which lets you to access each column by ordinal.  
Dataset&lt;String&gt; stringsDS = sqlDF.map(row -&gt; "Key: " + row.get(0) + ", Value: " + row.get(1), Encoders.STRING());  
stringsDS.show();  
// +--------------------+  
// |               value|  
// +--------------------+  
// |Key: 0, Value: val_0|  
// |Key: 0, Value: val_0|  
// |Key: 0, Value: val_0|  
// ...  

// You can also use DataFrames to create temporary views within a SparkSession.  
List&lt;Record&gt; records = new ArrayList&lt;Record&gt;();  
for (int key = 1; key &lt; 100; key++) {  
  Record record = new Record();  
  record.setKey(key);  
  record.setValue("val_" + key);  
  records.add(record);  
}  
Dataset&lt;Row&gt; recordsDF = spark.createDataFrame(records, Record.class);  
recordsDF.createOrReplaceTempView("records");  

// Queries can then join DataFrames data with data stored in Hive.  
spark.sql("SELECT * FROM records r JOIN src s ON r.key = s.key").show();  
// +---+------+---+------+  
// |key| value|key| value|  
// +---+------+---+------+  
// |  2| val_2|  2| val_2|  
// |  2| val_2|  2| val_2|  
// |  4| val_4|  4| val_4|  
// ...  
// only showing top 20 rows  </code></pre>

<p>如果使用eclipse运行上述代码的话需要添加spark-hive的jars，下面是maven的配置：</p>

<pre class="has">
<code>&lt;!-- https://mvnrepository.com/artifact/org.apache.spark/spark-hive_2.11 --&gt;  
&lt;dependency&gt;  
    &lt;groupId&gt;org.apache.spark&lt;/groupId&gt;  
    &lt;artifactId&gt;spark-hive_2.11&lt;/artifactId&gt;  
    &lt;version&gt;2.1.0&lt;/version&gt;  
&lt;/dependency&gt;</code></pre>

<p>否则的话会遇到下面错误：</p>

<pre class="has">
<code>Exception in thread "main" java.lang.IllegalArgumentException: Unable to instantiate SparkSession with Hive support because Hive classes are not found.  
    at org.apache.spark.sql.SparkSession$Builder.enableHiveSupport(SparkSession.scala:815)  
    at JavaSparkHiveExample.main(JavaSparkHiveExample.java:17)  </code></pre>

<p><strong>与不同版本Hive Metastore的交互</strong></p>

<p>Spark SQL对Hive的支持其中一个最重要的部分是与Hive metastore的交互，使得Spark SQL可以访问Hive表的元数据。从Spark 1.4.0版本开始，Spark SQL使用下面的配置可以用于查询不同版本的Hive metastores。需要注意的是，本质上Spark SQL会使用编译后的Hive 1.2.1版本的那些类来用于内部操作(serdes、UDFs、UDAFs等等)。</p>

<p><img alt="这里写图片描述" class="has" src="https://img-blog.csdn.net/20171103142203019?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvemhhbzg5NzQyNjE4Mg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast"></p>            </div>
                </div>