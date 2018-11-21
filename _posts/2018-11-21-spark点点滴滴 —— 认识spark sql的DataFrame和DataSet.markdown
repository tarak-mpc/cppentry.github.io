---
layout:     post
title:      spark点点滴滴 —— 认识spark sql的DataFrame和DataSet
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="概述">概述</h1>

<p>spark的DataFrames和DataSets是spark SQL中的关键概念，相比于RDD，DataFrame更能描述数据类型，因此是spark sql的基础类型，同时在spark 2.0.x及其以后的版本中，spark的机器学习也会逐渐替换成基于DataFrame的api，所有我们有必要了解spark的DataFrame相关概念。 <br>
spark sql在spark框架中的位置： <br>
<img src="https://img-blog.csdn.net/20170331140930148?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzU3OTkwMDM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
我们可以看到，spark sql是建立在spark框架之上的一个大数据关系型数据处理系统，和impala类似。 <br>
其核心部分有两个：DataFrame API和Catalyst引擎，SparkSQL的优化器系统Catalyst和大多数当前的大数据SQL处理引擎设计基本相同（Impala、Presto、Hive（Calcite）等），本篇我们重点看看DataFrame的基本原理和使用。</p>



<h1 id="dataframe基本原理">DataFrame基本原理</h1>



<h2 id="和rdd的比较">和RDD的比较</h2>

<p>我们知道，RDD是spark早期很重要的一个概念，是数据的immutable distributed的集合，由不同节点上的partition组成。DataFrame和RDD类似，也是数据的不可变分布式集合。不同的是，数据被组织成带名字的列，就像关系型数据库中的表。是一种有结构的高级别抽象，与之相应的提供了一种领域特定语言（DSL）API来操作这些分布式数据。 <br>
<img src="https://img-blog.csdn.net/20170331174520963?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzU3OTkwMDM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
DataFrame直观上很像是RDDs的加强版，它和RDDs在数据存储上最大的区别就在于，DataFrame是有Schema的，通俗的讲，就是上图中蓝色框住的那个表头。不要小看这一点，对于复杂的数据类型，DataFrame的这种结构可以使编程大大简化。</p>

<p>在spark2.0后，DataFrame的API和DataSet的API合并统一了，现在只需要处理DataSet相关API即可。 <br>
<img src="https://img-blog.csdn.net/20170331173415958?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzU3OTkwMDM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h2 id="dataset">DataSet</h2>

<p>从spark 2.0开始，两种独立的API特点：strongly-typed API 和untyped API。从概念上来说，将DataFrame作为 一般对象Dataset[Row]的集合的别名，而DataSet是strongly-typed JVM对象的集合，即java和scala中的类。 <br>
不同语言对应抽象类型如下：</p>

<table>
<thead>
<tr>
  <th>Language</th>
  <th>Main Abstraction</th>
</tr>
</thead>
<tbody><tr>
  <td>Scala</td>
  <td>Dataset[T] &amp; DataFrame (alias for Dataset[Row])</td>
</tr>
<tr>
  <td>Java</td>
  <td>Dataset[T]</td>
</tr>
<tr>
  <td>Python*</td>
  <td>DataFrame</td>
</tr>
<tr>
  <td>R*</td>
  <td>DataFrame</td>
</tr>
</tbody></table>




<h2 id="为什么用dataframe">为什么用DataFrame</h2>

<p>在RDDs中，数据集中的每项数据都是一个整体，因为你无法得知其内部的结构，这也就使得你对数据项的操作能力很弱，当你想获得数据项内部的部分信息的时候，你需要手动将object按照你预先设定的数据格式进行分割，麻烦，且容易出错。而使用DataFrame，意味着你可以直接获得数据项的内部数据结构，并且由于DataFrame的Schema的存在，数据项的转换也都将是类型安全的，这对于较为复杂的数据计算程序的调试是十分有利的，很多数据类型不匹配的问题都可以在编译阶段就被检查出来，而对于不合法的数据文件，DataFrame也具备一定分辨能力。</p>

<p>而另一方面，我们注意到，当RDD被切割出“列”并加上“表头”变成DataFrame之后，就意味着DataFrame要支持比RDD更加细粒度的查询，而这种Table式的结构，很容易就可以让我们联想到数据库中数据表，同时DataFrame API也支持使用者对DataFrame进行数据库那样的关联、聚合、筛选等查询操作。 <br>
对DataFrame的查询，都是在RDDs数据集上做得直接映射，。你可以把DataFrame中的数据，理解为“逻辑数据”，而“物理数据”实际上在RDDs中。</p>



<h2 id="dataframe性能">DataFrame性能</h2>

<p>为了能够更快更好的定位到数据，甚至于更好的利用内存与磁盘中的存储空间，DataFrame中的数据在内存和磁盘中的排列也必须更为考究，才能够在不损失性能的前提下提供这些操作。Spark SQL团队给出的方案是：按列压缩存储。 <br>
而列式存储则是将行拆开，将一列的数据放在一起，同时不同列可以存放在不同的位置（由于天然利于纵向分表，所以在超大数据集的存储上，列式存储也具有一定优势）。通常情况下，我们查询一个数据并不需要检查一行数据中的每个列条目，但是在行式存储中，必须要扫描全部数据集才能够筛选出我们想要的那条数据，既然我们检索的项目很可能只是“Id”一项而已，那为什么要去管其他列呢？特别是在磁盘上，磁头访问数据的方式是线性的，如果只想根据“Id”进行筛选，即便只是上面那个只有两列的数据表，磁头移动的距离也要超过列式存储的好几倍。不过相应的，列式存储中“更新”“插入“”查询“等操作会比较麻烦，但是由于DataFrame和RDDs一样都是Immutable的，所以恰好规避了这一问题。 <br>
最终，DataFrame API中这种支持对列进行访问的形式，要比RDDs API的数据访问粒度更为细腻，这也就意味着数据工程师可以根据“列”的性质，来为列建立索引，从而避免遍历所有的数据项。</p>



<h1 id="dataset的操作">DataSet的操作</h1>

<p>我们先使用spark-shell来简单看下DataSet的结构： <br>
我们将json数据文件people.json上传到hdfs上/XXX/spark/people.json，内容如下：</p>



<pre class="prettyprint"><code class=" hljs json">{"<span class="hljs-attribute">name</span>":<span class="hljs-value"><span class="hljs-string">"Michael"</span></span>}
{"<span class="hljs-attribute">name</span>":<span class="hljs-value"><span class="hljs-string">"Andy"</span></span>, "<span class="hljs-attribute">age</span>":<span class="hljs-value"><span class="hljs-number">30</span></span>}
{"<span class="hljs-attribute">name</span>":<span class="hljs-value"><span class="hljs-string">"Justin"</span></span>, "<span class="hljs-attribute">age</span>":<span class="hljs-value"><span class="hljs-number">19</span></span>}</code></pre>

<p>在spark-shell中执行结果如下<img src="https://img-blog.csdn.net/20170331180352346?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzU3OTkwMDM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title="">： <br>
更多DataSet上的api可以参考<a href="http://spark.apache.org/docs/2.0.2/api/scala/index.html#org.apache.spark.sql.Dataset" rel="nofollow">这里</a>。</p>



<h2 id="如何构建dataset">如何构建DataSet</h2>



<h3 id="和class类对应转换">和class类对应转换</h3>

<p>在spark-shell上执行效果如下图： <br>
<img src="https://img-blog.csdn.net/20170331181247663?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzU3OTkwMDM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h3 id="从rdd转换">从RDD转换</h3>

<p>构造文件people.txt，内容如下：</p>



<pre class="prettyprint"><code class=" hljs ">Michael, 29
Andy, 30
Justin, 19</code></pre>

<p>上次到hdfs上，执行效果： <br>
<img src="https://img-blog.csdn.net/20170331181912544?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzU3OTkwMDM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h3 id="编程指定">编程指定</h3>

<p>scala代码如下：</p>



<pre class="prettyprint"><code class=" hljs sql">import org.apache.spark.sql.types._

// <span class="hljs-operator"><span class="hljs-keyword">Create</span> an RDD
val peopleRDD = spark.sparkContext.textFile(<span class="hljs-string">"examples/src/main/resources/people.txt"</span>)

// The <span class="hljs-keyword">schema</span> <span class="hljs-keyword">is</span> encoded <span class="hljs-keyword">in</span> a string
val schemaString = <span class="hljs-string">"name age"</span>

// Generate the <span class="hljs-keyword">schema</span> based <span class="hljs-keyword">on</span> the string <span class="hljs-keyword">of</span> <span class="hljs-keyword">schema</span>
val fields = schemaString.split(<span class="hljs-string">" "</span>)
  .map(fieldName =&gt; StructField(fieldName, StringType, nullable = <span class="hljs-keyword">true</span>))
val <span class="hljs-keyword">schema</span> = StructType(fields)

// Convert records <span class="hljs-keyword">of</span> the RDD (people) <span class="hljs-keyword">to</span> <span class="hljs-keyword">Rows</span>
val rowRDD = peopleRDD
  .map(_.split(<span class="hljs-string">","</span>))
  .map(attributes =&gt; <span class="hljs-keyword">Row</span>(attributes(<span class="hljs-number">0</span>), attributes(<span class="hljs-number">1</span>).trim))

// Apply the <span class="hljs-keyword">schema</span> <span class="hljs-keyword">to</span> the RDD
val peopleDF = spark.createDataFrame(rowRDD, <span class="hljs-keyword">schema</span>)</span></code></pre>

<hr>

<p>【参考资料】<a href="https://databricks.com/blog/2016/07/14/a-tale-of-three-apache-spark-apis-rdds-dataframes-and-datasets.html" rel="nofollow">A Tale of Three Apache Spark APIs: RDDs, DataFrames, and Datasets</a> <br>
【参考资料】<a href="http://blueve.me/archives/1452?utm_source=tuicool&amp;utm_medium=referral" rel="nofollow">进化的Spark, 从DataFrame说起</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>