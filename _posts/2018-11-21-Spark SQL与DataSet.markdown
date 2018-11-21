---
layout:     post
title:      Spark SQL与DataSet
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/mingyunxiaohai/article/details/81334030				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>Spark SQL的架构图 <br>
<img src="https://img-blog.csdn.net/20180801165713707?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L21pbmd5dW54aWFvaGFp/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
Spark SQL是用于结构化数据处理的Spark模块。与基本的Spark RDD API不同，Spark SQL提供的接口为Spark提供了有关数据结构和正在执行的计算的更多信息。在内部，Spark SQL使用此额外信息来执行额外的优化</p>

<p><strong>Spark SQL执行计划生成和优化都由Catalyst完成</strong></p>

<p><strong>DataSet</strong>是分布式数据集合。Dataset是Spark 1.6中添加的一个新接口，它提供了RDD的优势（强类型，使用强大的lambda函数的能力）以及Spark SQL优化执行引擎的优点。数据集可以被构造从JVM对象，然后使用功能性的转换（操作map，flatMap，filter等等）。 <br>
<strong>DataFrame</strong>是组织为命名列的数据集。它在概念上等同于关系数据库中的表或R / Python中的数据框，但在引擎盖下具有更丰富的优化。DataFrame可以从多种来源构建，例如：结构化数据文件，Hive中的表，外部数据库或现有RDD。DataFrame API在Scala，Java，Python和R中可用。在Scala和Java中，DataFrame由Rows 的数据集表示。在Scala API中，DataFrame它只是一个类型别名Dataset[Row]。而在Java API中，用户需要使用Dataset来表示DataFrame。</p>

<p>RDD 优点</p>

<ul>
<li>JVM对象组成的分布式数据集合</li>
<li>不可变并且有容错能力</li>
<li>可处理机构化和非结构化的数据</li>
<li>支持函数式转换</li>
</ul>

<p>RDD缺点</p>

<ul>
<li>没有Schema</li>
<li>用户自己优化程序</li>
<li>从不同的数据源读取数据非常困难</li>
<li>合并多个数据源中的数据也非常困难</li>
</ul>

<p>DataFrame API</p>

<ul>
<li>Row对象组成的分布式数据集</li>
<li>不可变并且有容错能力</li>
<li>处理结构化数据</li>
<li>自带优化器Catalyset,可自动优化程序</li>
<li>Data source API <br>
DataFrame让Spark对结构化数据有了处理能力</li>
</ul>

<p>DataFrame的缺点： <br>
1.编译时不能类型转化安全检查，运行时才能确定是否有问题 <br>
2.对于对象支持不友好，rdd内部数据直接以java对象存储，dataframe内存存储的是row对象而不能是自定义对象</p>

<p>DateSet的优点： <br>
1.DateSet整合了RDD和DataFrame的优点，支持结构化和非结构化数据 <br>
2.和RDD一样，支持自定义对象存储 <br>
3.和DataFrame一样，支持结构化数据的sql查询 <br>
4.采用堆外内存存储，gc友好 <br>
5.类型转化安全，代码友好</p>

<p>foreach 在Executor端遍历 <br>
cache <br>
persist  //持久化 <br>
printSchema <br>
toDF <br>
unpersist  //清除持久化的</p>

<p>创建SparkSession 命名为spark 下面使用</p>

<pre class="prettyprint"><code class=" hljs avrasm">import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.sql</span><span class="hljs-preprocessor">.SparkSession</span>

val spark = SparkSession
  <span class="hljs-preprocessor">.builder</span>()
  <span class="hljs-preprocessor">.appName</span>(<span class="hljs-string">"Spark SQL basic example"</span>)
  <span class="hljs-preprocessor">.config</span>(<span class="hljs-string">"spark.some.config.option"</span>, <span class="hljs-string">"some-value"</span>)
  <span class="hljs-preprocessor">.getOrCreate</span>()

// For implicit conversions like converting RDDs to DataFrames
import spark<span class="hljs-preprocessor">.implicits</span>._</code></pre>

<p><strong>利用反射机制推断RDD模式</strong></p>

<p>在利用反射机制推断RDD模式时，需要首先定义一个case class，因为，只有case class才能被Spark隐式地转换为DataFrame <br>
代码片段</p>



<pre class="prettyprint"><code class=" hljs avrasm">import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.sql</span><span class="hljs-preprocessor">.catalyst</span><span class="hljs-preprocessor">.encoders</span><span class="hljs-preprocessor">.ExpressionEncoder</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.sql</span><span class="hljs-preprocessor">.Encoder</span> 
import spark<span class="hljs-preprocessor">.implicits</span>._  //导入包，支持把一个RDD隐式转换为一个DataFrame
//定义一个case class
case class Person(name: String, age: Long)  
//转换成DataFrame
val peopleDF = spark<span class="hljs-preprocessor">.sparkContext</span>
                <span class="hljs-preprocessor">.textFile</span>(<span class="hljs-string">"file:///usr/local/spark/examples/src/main/resources/people.txt"</span>)
                <span class="hljs-preprocessor">.map</span>(_<span class="hljs-preprocessor">.split</span>(<span class="hljs-string">","</span>))
                <span class="hljs-preprocessor">.map</span>(attributes =&gt;Person(attributes(<span class="hljs-number">0</span>),attributes(<span class="hljs-number">1</span>)<span class="hljs-preprocessor">.trim</span><span class="hljs-preprocessor">.toInt</span>))
                <span class="hljs-preprocessor">.toDF</span>()
//必须注册为临时表才能供下面的查询使用
peopleDF<span class="hljs-preprocessor">.createOrReplaceTempView</span>(<span class="hljs-string">"people"</span>) 
val personsRDD = spark<span class="hljs-preprocessor">.sql</span>(<span class="hljs-string">"select name,age from people where age &gt; 20"</span>)
//最终生成一个DataFrame，下面是系统执行返回的信息
//personsRDD: org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.sql</span><span class="hljs-preprocessor">.DataFrame</span> = [name: string, age: bigint]
personsRDD<span class="hljs-preprocessor">.map</span>(t =&gt; <span class="hljs-string">"Name: "</span>+t(<span class="hljs-number">0</span>)+ <span class="hljs-string">","</span>+<span class="hljs-string">"Age: "</span>+t(<span class="hljs-number">1</span>))<span class="hljs-preprocessor">.show</span>()  //DataFrame中的每个元素都是一行记录，包含name和age两个字段，分别用t(<span class="hljs-number">0</span>)和t(<span class="hljs-number">1</span>)来获取值
//下面是系统执行返回的信息
+------------------+ 
| value|
+------------------+
|Name:Michael,Age:<span class="hljs-number">29</span>|
| Name:Andy,Age:<span class="hljs-number">30</span>|
+------------------+
</code></pre>

<p>当无法提前定义case class时，就需要采用编程方式定义RDD模式。 <br>
代码片段</p>



<pre class="prettyprint"><code class=" hljs avrasm">import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.sql</span><span class="hljs-preprocessor">.types</span>._
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.sql</span><span class="hljs-preprocessor">.Row</span>
//生成字段
val fields = Array(StructField(<span class="hljs-string">"name"</span>,StringType,true), StructField(<span class="hljs-string">"age"</span>,IntegerType,true))
val schema = StructType(fields)
//从上面信息可以看出，schema描述了模式信息，模式中包含name和age两个字段
//shcema就是“表头”
val peopleRDD = spark<span class="hljs-preprocessor">.sparkContext</span><span class="hljs-preprocessor">.textFile</span>(<span class="hljs-string">"file:///usr/local/spark/examples/src/main/resources/people.txt"</span>)
//对peopleRDD 这个RDD中的每一行元素都进行解析
val rowRDD = peopleRDD<span class="hljs-preprocessor">.map</span>(_<span class="hljs-preprocessor">.split</span>(<span class="hljs-string">","</span>))<span class="hljs-preprocessor">.map</span>(attributes =&gt; Row(attributes(<span class="hljs-number">0</span>),attributes(<span class="hljs-number">1</span>)<span class="hljs-preprocessor">.trim</span><span class="hljs-preprocessor">.toInt</span>))
//上面得到的rowRDD就是“表中的记录”
//下面把“表头”和“表中的记录”拼装起来
 val peopleDF = spark<span class="hljs-preprocessor">.createDataFrame</span>(rowRDD, schema)
 //必须注册为临时表才能供下面查询使用
peopleDF<span class="hljs-preprocessor">.createOrReplaceTempView</span>(<span class="hljs-string">"people"</span>)
val results = spark<span class="hljs-preprocessor">.sql</span>(<span class="hljs-string">"SELECT name,age FROM people"</span>)
results<span class="hljs-preprocessor">.map</span>(attributes =&gt; <span class="hljs-string">"name: "</span> + attributes(<span class="hljs-number">0</span>)+<span class="hljs-string">","</span>+<span class="hljs-string">"age:"</span>+attributes(<span class="hljs-number">1</span>))<span class="hljs-preprocessor">.show</span>()
+--------------------+
| value|
+--------------------+
|name: Michael,age:<span class="hljs-number">29</span>|
| name: Andy,age:<span class="hljs-number">30</span>|
| name: Justin,age:<span class="hljs-number">19</span>|</code></pre>

<p><strong>下面的spark代表SparkSession</strong> <br>
读写json数据 </p>



<pre class="prettyprint"><code class=" hljs avrasm">val peopleDF = spark<span class="hljs-preprocessor">.read</span><span class="hljs-preprocessor">.format</span>(<span class="hljs-string">"json"</span>)<span class="hljs-preprocessor">.load</span>(<span class="hljs-string">"/people.json"</span>)
或者 
val peopleDF = spark<span class="hljs-preprocessor">.read</span><span class="hljs-preprocessor">.json</span>(<span class="hljs-string">"/people.json"</span>)

peopleDF<span class="hljs-preprocessor">.select</span>(<span class="hljs-string">"name"</span>, <span class="hljs-string">"age"</span>)<span class="hljs-preprocessor">.write</span><span class="hljs-preprocessor">.format</span>(<span class="hljs-string">"json"</span>)<span class="hljs-preprocessor">.save</span>(<span class="hljs-string">"namesAndAges.json"</span>)</code></pre>

<p>读写parquet数据</p>



<pre class="prettyprint"><code class=" hljs avrasm">val peopleDF = spark<span class="hljs-preprocessor">.read</span><span class="hljs-preprocessor">.format</span>(<span class="hljs-string">"parquet"</span>)<span class="hljs-preprocessor">.load</span>(<span class="hljs-string">"/people.parquet"</span>)
或者
val peopleDF = spark<span class="hljs-preprocessor">.read</span><span class="hljs-preprocessor">.parquet</span>(<span class="hljs-string">"/people.parquet"</span>)

peopleDF<span class="hljs-preprocessor">.select</span>(<span class="hljs-string">"name"</span>, <span class="hljs-string">"age"</span>)<span class="hljs-preprocessor">.write</span><span class="hljs-preprocessor">.format</span>(<span class="hljs-string">"parquet"</span>)<span class="hljs-preprocessor">.save</span>(<span class="hljs-string">"namesAndAges.parquet"</span>)</code></pre>

<p>读取text数据</p>



<pre class="prettyprint"><code class=" hljs livecodeserver"> spark.<span class="hljs-built_in">read</span>.<span class="hljs-keyword">text</span>(…)<span class="hljs-comment"> // 返 回 Dataset&lt;Row&gt;</span>
或者
 spark.<span class="hljs-built_in">read</span>.textFile(…)<span class="hljs-comment"> // 返 回 Dataset&lt;String&gt;</span></code></pre>

<p>读取写jdbc数据  如：mysql</p>



<pre class="prettyprint"><code class=" hljs avrasm">val jdbcDF = spark<span class="hljs-preprocessor">.read</span>
  <span class="hljs-preprocessor">.format</span>(<span class="hljs-string">"jdbc"</span>)
  <span class="hljs-preprocessor">.option</span>(<span class="hljs-string">"url"</span>, <span class="hljs-string">"jdbc:mysql://localhost:3306/spark"</span>)
  <span class="hljs-preprocessor">.option</span>(<span class="hljs-string">"driver"</span>,<span class="hljs-string">"com.mysql.jdbc.Driver"</span>).
  <span class="hljs-preprocessor">.option</span>(<span class="hljs-string">"dbtable"</span>, <span class="hljs-string">"schema.tablename"</span>)
  <span class="hljs-preprocessor">.option</span>(<span class="hljs-string">"user"</span>, <span class="hljs-string">"username"</span>)
  <span class="hljs-preprocessor">.option</span>(<span class="hljs-string">"password"</span>, <span class="hljs-string">"password"</span>)
  <span class="hljs-preprocessor">.load</span>()

jdbcDF<span class="hljs-preprocessor">.write</span>
  <span class="hljs-preprocessor">.format</span>(<span class="hljs-string">"jdbc"</span>)
  <span class="hljs-preprocessor">.option</span>(<span class="hljs-string">"url"</span>, <span class="hljs-string">"jdbc:postgresql:dbserver"</span>)
  <span class="hljs-preprocessor">.option</span>(<span class="hljs-string">"dbtable"</span>, <span class="hljs-string">"schema.tablename"</span>)
  <span class="hljs-preprocessor">.option</span>(<span class="hljs-string">"user"</span>, <span class="hljs-string">"username"</span>)
  <span class="hljs-preprocessor">.option</span>(<span class="hljs-string">"password"</span>, <span class="hljs-string">"password"</span>)
  <span class="hljs-preprocessor">.save</span>()</code></pre>

<p>Spark SQL调优 <br>
DataFrame缓存 <br>
dataFrame.cache() <br>
Reduce task数目： spark.sql.shuffle.partitions （默认是200）可以根据自己的情况设置partitions个数 <br>
读数据时每个Partition大小：spark.sql.files.maxPartitionBytes（默认128MB） <br>
小文件合并读： spark.sql.files.openCostInBytes （默认是4194304 (4 MB) ） <br>
广播小表大小： spark.sql.autoBroadcastJoinThreshold（默认是10485760 (10 MB)）</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>