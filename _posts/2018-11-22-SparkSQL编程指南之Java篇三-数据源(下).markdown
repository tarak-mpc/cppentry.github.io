---
layout:     post
title:      SparkSQL编程指南之Java篇三-数据源(下)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/gangchengzhong/article/details/61196348				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<strong>4. Hive Tables</strong><br><br>
Spark SQL支持对Hive的读写操作。然而因为Hive有很多依赖包，所以这些依赖包没有包含在默认的Spark包里面。如果Hive依赖的包能在classpath找到，Spark将会自动加载它们。需要注意的是，这些Hive依赖包必须复制到所有的工作节点上，因为它们为了能够访问存储在Hive的数据，会调用Hive的序列化和反序列化(SerDes)包。Hive的配置文件hive-site.xml、core-site.xml(security配置)和hdfs-site.xml(HDFS配置)是保存在conf目录下面。<br><br>
当使用Hive时，必须初始化一个支持Hive的SparkSession，用户即使没有部署一个Hive的环境仍然可以使用Hive。当没有配置hive-site.xml时，Spark会自动在当前应用目录创建metastore_db和创建由spark.sql.warehouse.dir配置的目录，如果没有配置，默认是当前应用目录下的spark-warehouse目录。注意，从Spark 2.0.0版本开始，hive-site.xml里面的hive.metastore.warehouse.dir属性已经被spark.sql.warehouse.dir替代，用于指定warehouse的默认数据路径(必须有写权限)。<br><br><pre><code class="language-java">import java.io.Serializable;
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
// only showing top 20 rows</code></pre><br>
* 如果使用eclipse运行上述代码的话需要添加spark-hive的jars，下面是maven的配置：<br><br><pre class="xml">&lt;!-- https://mvnrepository.com/artifact/org.apache.spark/spark-hive_2.11 --&gt;
&lt;dependency&gt;
	&lt;groupId&gt;org.apache.spark&lt;/groupId&gt;
	&lt;artifactId&gt;spark-hive_2.11&lt;/artifactId&gt;
	&lt;version&gt;2.1.0&lt;/version&gt;
&lt;/dependency&gt;</pre><br>
否则的话会遇到下面错误：<br><br><pre><code class="language-plain">Exception in thread "main" java.lang.IllegalArgumentException: Unable to instantiate SparkSession with Hive support because Hive classes are not found.
	at org.apache.spark.sql.SparkSession$Builder.enableHiveSupport(SparkSession.scala:815)
	at JavaSparkHiveExample.main(JavaSparkHiveExample.java:17)</code></pre><br><strong>4.1 与不同版本Hive Metastore的交互</strong><br><br>
Spark SQL对Hive的支持其中一个最重要的部分是与Hive metastore的交互，使得Spark SQL可以访问Hive表的元数据。从Spark 1.4.0版本开始，Spark SQL使用下面的配置可以用于查询不同版本的Hive metastores。需要注意的是，本质上Spark SQL会使用编译后的Hive 1.2.1版本的那些类来用于内部操作(serdes、UDFs、UDAFs等等)。<br><br><img src="https://img-blog.csdn.net/20170310142713433?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZ2FuZ2NoZW5nemhvbmc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><br><strong>5. JDBC To Other Databases</strong><br><br>
Spark SQL也支持使用JDBC访问其他数据库。该功能建议使用JdbcRDD，因为返回的结果是DataFrame，它们在Spark SQL里或者与其它数据源连接的时候更容易处理。JDBC数据源因为不需要用户提供ClassTag，所以在Java或Python里更容易使用(注意，这里说的JDBC和Spark SQL里面的JDBC server是不同的，后者允许其它应用使用Spark SQL去执行查询)。<br><br>
在使用之前需要在Spark的classpath里面添加指定数据库的JDBC驱动。例如要从Spark Shell连接Postgres数据库需要执行以下命令：<br><br><pre><code class="language-plain">bin/spark-shell --driver-class-path postgresql-9.4.1207.jar --jars postgresql-9.4.1207.jar</code></pre><br>
远程数据库的表可以通过使用数据源的API加载为DataFrame或者Spark SQL临时视图。用户可以在数据源的选项里指定JDBC连接的属性，例如用于登录数据库的用户名和密码。除了用于连接的属性之外，Spark也支持以下区分大小写的选项：<br><br><img src="https://img-blog.csdn.net/20170310143837480?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZ2FuZ2NoZW5nemhvbmc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><br><pre><code class="language-java">// Note: JDBC loading and saving can be achieved via either the load/save or jdbc methods
// Loading data from a JDBC source
Dataset&lt;Row&gt; jdbcDF = spark.read()
  .format("jdbc")
  .option("url", "jdbc:postgresql:dbserver")
  .option("dbtable", "schema.tablename")
  .option("user", "username")
  .option("password", "password")
  .load();

Properties connectionProperties = new Properties();
connectionProperties.put("user", "username");
connectionProperties.put("password", "password");
Dataset&lt;Row&gt; jdbcDF2 = spark.read()
  .jdbc("jdbc:postgresql:dbserver", "schema.tablename", connectionProperties);

// Saving data to a JDBC source
jdbcDF.write()
  .format("jdbc")
  .option("url", "jdbc:postgresql:dbserver")
  .option("dbtable", "schema.tablename")
  .option("user", "username")
  .option("password", "password")
  .save();

jdbcDF2.write()
  .jdbc("jdbc:postgresql:dbserver", "schema.tablename", connectionProperties);</code></pre><br><strong>6. 两个问题及解决方法</strong><br><ul><li>在客户端的session和所有executors中的启动类加载器必须能够加载相应的JDBC驱动类。这是因为Java的DriverManager类会执行安全检查，当打开一个连接的时候，忽略那些不能被启动类加载器加载的JDBC驱动类。其中一种方便的方法是修改所有工作节点的compute_classpath.sh，把需要的驱动jars包含进来</li></ul><ul><li>某些数据库，例如H2，会把所有的名字转换为大写，因此在Spark SQL里面使用时也需要使用大写</li></ul>
* 参考Spark SQL官方链接：http://spark.apache.org/docs/latest/sql-programming-guide.html#data-sources<br><br><strong>TO BE CONTINUED...O(∩_∩)O</strong>
            </div>
                </div>