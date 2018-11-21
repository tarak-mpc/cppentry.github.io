---
layout:     post
title:      SparkSQL操作Hive Table（enableHiveSupport()）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>Spark SQL支持对Hive的读写操作。然而因为Hive有很多依赖包，所以这些依赖包没有包含在默认的Spark包里面。如果Hive依赖的包能在classpath找到，Spark将会自动加载它们。需要注意的是，这些Hive依赖包必须复制到所有的工作节点上，因为它们为了能够访问存储在Hive的数据，会调用Hive的序列化和反序列化(SerDes)包。Hive的配置文件<strong>hive-site.xml</strong>、<strong>core-site.xml</strong>(security配置)和<strong>hdfs-site.xml</strong>(HDFS配置)是保存在conf目录下面。 <br>
当使用Hive时，必须初始化一个支持Hive的SparkSession，用户即使没有部署一个Hive的环境仍然可以使用Hive。当没有配置hive-site.xml时，Spark会自动在当前应用目录创建metastore_db和创建由spark.sql.warehouse.dir配置的目录，如果没有配置，默认是当前应用目录下的spark-warehouse目录。 <br>
<strong>注意：</strong>从Spark 2.0.0版本开始，hive-site.xml里面的hive.metastore.warehouse.dir属性已经被spark.sql.warehouse.dir替代，用于指定warehouse的默认数据路径(必须有写权限)。</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">import</span> java.io.Serializable;  
<span class="hljs-keyword">import</span> java.util.ArrayList;  
<span class="hljs-keyword">import</span> java.util.List;  

<span class="hljs-keyword">import</span> org.apache.spark.api.java.function.MapFunction;  
<span class="hljs-keyword">import</span> org.apache.spark.sql.Dataset;  
<span class="hljs-keyword">import</span> org.apache.spark.sql.Encoders;  
<span class="hljs-keyword">import</span> org.apache.spark.sql.Row;  
<span class="hljs-keyword">import</span> org.apache.spark.sql.SparkSession;  

<span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">Record</span> <span class="hljs-keyword">implements</span> <span class="hljs-title">Serializable</span> {</span>  
  <span class="hljs-keyword">private</span> <span class="hljs-keyword">int</span> key;  
  <span class="hljs-keyword">private</span> String value;  

  <span class="hljs-keyword">public</span> <span class="hljs-keyword">int</span> <span class="hljs-title">getKey</span>() {  
    <span class="hljs-keyword">return</span> key;  
  }  

  <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">setKey</span>(<span class="hljs-keyword">int</span> key) {  
    <span class="hljs-keyword">this</span>.key = key;  
  }  

  <span class="hljs-keyword">public</span> String <span class="hljs-title">getValue</span>() {  
    <span class="hljs-keyword">return</span> value;  
  }  

  <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">setValue</span>(String value) {  
    <span class="hljs-keyword">this</span>.value = value;  
  }  
}  

<span class="hljs-comment">// warehouseLocation points to the default location for managed databases and tables  </span>
String warehouseLocation = <span class="hljs-string">"/spark-warehouse"</span>;  
<span class="hljs-comment">// init spark session with hive support  </span>
SparkSession spark = SparkSession  
  .builder()  
  .appName(<span class="hljs-string">"Java Spark Hive Example"</span>)  
  .master(<span class="hljs-string">"local[*]"</span>)  
  .config(<span class="hljs-string">"spark.sql.warehouse.dir"</span>, warehouseLocation)  
  .enableHiveSupport()  
  .getOrCreate();  

spark.sql(<span class="hljs-string">"CREATE TABLE IF NOT EXISTS src (key INT, value STRING)"</span>);  
spark.sql(<span class="hljs-string">"LOAD DATA LOCAL INPATH 'examples/src/main/resources/kv1.txt' INTO TABLE src"</span>);  

<span class="hljs-comment">// Queries are expressed in HiveQL  </span>
spark.sql(<span class="hljs-string">"SELECT * FROM src"</span>).show();  
<span class="hljs-comment">// +---+-------+  </span>
<span class="hljs-comment">// |key|  value|  </span>
<span class="hljs-comment">// +---+-------+  </span>
<span class="hljs-comment">// |238|val_238|  </span>
<span class="hljs-comment">// | 86| val_86|  </span>
<span class="hljs-comment">// |311|val_311|  </span>
<span class="hljs-comment">// ...  </span>
<span class="hljs-comment">// only showing top 20 rows  </span>

<span class="hljs-comment">// Aggregation queries are also supported.  </span>
spark.sql(<span class="hljs-string">"SELECT COUNT(*) FROM src"</span>).show();  
<span class="hljs-comment">// +--------+  </span>
<span class="hljs-comment">// |count(1)|  </span>
<span class="hljs-comment">// +--------+  </span>
<span class="hljs-comment">// |    500 |  </span>
<span class="hljs-comment">// +--------+  </span>

<span class="hljs-comment">// The results of SQL queries are themselves DataFrames and support all normal functions.  </span>
Dataset&lt;Row&gt; sqlDF = spark.sql(<span class="hljs-string">"SELECT key, value FROM src WHERE key &lt; 10 ORDER BY key"</span>);  

<span class="hljs-comment">// The items in DaraFrames are of type Row, which lets you to access each column by ordinal.  </span>
Dataset&lt;String&gt; stringsDS = sqlDF.map(row -&gt; <span class="hljs-string">"Key: "</span> + row.get(<span class="hljs-number">0</span>) + <span class="hljs-string">", Value: "</span> + row.get(<span class="hljs-number">1</span>), Encoders.STRING());  
stringsDS.show();  
<span class="hljs-comment">// +--------------------+  </span>
<span class="hljs-comment">// |               value|  </span>
<span class="hljs-comment">// +--------------------+  </span>
<span class="hljs-comment">// |Key: 0, Value: val_0|  </span>
<span class="hljs-comment">// |Key: 0, Value: val_0|  </span>
<span class="hljs-comment">// |Key: 0, Value: val_0|  </span>
<span class="hljs-comment">// ...  </span>

<span class="hljs-comment">// You can also use DataFrames to create temporary views within a SparkSession.  </span>
List&lt;Record&gt; records = <span class="hljs-keyword">new</span> ArrayList&lt;Record&gt;();  
<span class="hljs-keyword">for</span> (<span class="hljs-keyword">int</span> key = <span class="hljs-number">1</span>; key &lt; <span class="hljs-number">100</span>; key++) {  
  Record record = <span class="hljs-keyword">new</span> Record();  
  record.setKey(key);  
  record.setValue(<span class="hljs-string">"val_"</span> + key);  
  records.add(record);  
}  
Dataset&lt;Row&gt; recordsDF = spark.createDataFrame(records, Record.class);  
recordsDF.createOrReplaceTempView(<span class="hljs-string">"records"</span>);  

<span class="hljs-comment">// Queries can then join DataFrames data with data stored in Hive.  </span>
spark.sql(<span class="hljs-string">"SELECT * FROM records r JOIN src s ON r.key = s.key"</span>).show();  
<span class="hljs-comment">// +---+------+---+------+  </span>
<span class="hljs-comment">// |key| value|key| value|  </span>
<span class="hljs-comment">// +---+------+---+------+  </span>
<span class="hljs-comment">// |  2| val_2|  2| val_2|  </span>
<span class="hljs-comment">// |  2| val_2|  2| val_2|  </span>
<span class="hljs-comment">// |  4| val_4|  4| val_4|  </span>
<span class="hljs-comment">// ...  </span>
<span class="hljs-comment">// only showing top 20 rows  </span></code></pre>

<p>如果使用eclipse运行上述代码的话需要添加spark-hive的jars，下面是maven的配置：</p>

<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-comment">&lt;!-- https://mvnrepository.com/artifact/org.apache.spark/spark-hive_2.11 --&gt;</span>  
<span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>  
    <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>  
    <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-hive_2.11<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>  
    <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>2.1.0<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>  
<span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span></code></pre>

<p>否则的话会遇到下面错误：</p>



<pre class="prettyprint"><code class=" hljs avrasm">Exception <span class="hljs-keyword">in</span> thread <span class="hljs-string">"main"</span> java<span class="hljs-preprocessor">.lang</span><span class="hljs-preprocessor">.IllegalArgumentException</span>: Unable to instantiate SparkSession with Hive support because Hive classes are not found.  
    at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.sql</span><span class="hljs-preprocessor">.SparkSession</span>$Builder<span class="hljs-preprocessor">.enableHiveSupport</span>(SparkSession<span class="hljs-preprocessor">.scala</span>:<span class="hljs-number">815</span>)  
    at JavaSparkHiveExample<span class="hljs-preprocessor">.main</span>(JavaSparkHiveExample<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">17</span>)  </code></pre>

<p><strong>与不同版本Hive Metastore的交互</strong></p>

<p>Spark SQL对Hive的支持其中一个最重要的部分是与Hive metastore的交互，使得Spark SQL可以访问Hive表的元数据。从Spark 1.4.0版本开始，Spark SQL使用下面的配置可以用于查询不同版本的Hive metastores。需要注意的是，本质上Spark SQL会使用编译后的Hive 1.2.1版本的那些类来用于内部操作(serdes、UDFs、UDAFs等等)。</p>

<p><img src="https://img-blog.csdn.net/20171103142203019?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvemhhbzg5NzQyNjE4Mg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>