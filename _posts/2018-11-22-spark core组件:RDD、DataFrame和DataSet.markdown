---
layout:     post
title:      spark core组件:RDD、DataFrame和DataSet
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/LW_GHY/article/details/56681273				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
spark生态系统中，Spark Core，包括各种Spark的各种核心组件，它们能够对内存和硬盘进行操作，或者调用CPU进行计算。<br>
spark core定义了RDD、DataFrame和DataSet<br><p>spark最初只有RDD，DataFrame在Spark 1.3中被首次发布，DataSet在Spark1.6版本中被加入。</p>
<p><img src="https://img-blog.csdn.net/20170223212509304" alt=""><br></p>
<p></p>
<h3>RDD是什么？</h3>
RDD：Spark的核心概念是RDD (resilientdistributed dataset)，指的是一个只读的，可分区的分布式数据集，这个数据集的全部或部分可以缓存在内存中，在多次计算间重用。<br><h3>DataFrame是什么？</h3>
在Spark中，DataFrame是一种以RDD为基础的分布式数据集，类似于传统数据库中的二维表格。DataFrame与RDD的主要区别在于，前者带有schema元信息，即DataFrame所表示的二维表数据集的每一列都带有名称和类型。这使得Spark SQL得以洞察更多的结构信息，从而对藏于DataFrame背后的数据源以及作用于DataFrame之上的变换进行了针对性的优化，最终达到大幅提升运行时效率的目标。反观RDD，由于无从得知所存数据元素的具体内部结构，Spark Core只能在stage层面进行简单、通用的流水线优化。<br><p></p>
<p><img src="https://img-blog.csdn.net/20170223212524757" alt=""><br></p>
<p></p>
<h3>DataSet是什么？</h3>
Dataset是一个强类型的特定领域的对象，这种对象可以函数式或者关系操作并行地转换。每个Dataset也有一个被称为一个DataFrame的类型化视图，这种DataFrame是Row类型的Dataset，即Dataset[Row]<br>
Dataset是“懒惰”的，只在执行行动操作时触发计算。本质上，数据集表示一个逻辑计划，该计划描述了产生数据所需的计算。当执行行动操作时，Spark的查询优化程序优化逻辑计划，并生成一个高效的并行和分布式物理计划。<br><h3>RDD和DataFrame比较</h3>
<p></p>
<p><img src="https://img-blog.csdn.net/20170223212608507" alt=""><br></p>
<p>DataFrame与RDD相同之处，都是不可变分布式弹性数据集。不同之处在于，DataFrame的数据集都是按指定列存储，即结构化数据。类似于传统数据库中的表。 DataFrame的设计是为了让大数据处理起来更容易。DataFrame允许开发者把结构化数据集导入DataFrame，并做了higher-level的抽象; DataFrame提供特定领域的语言（DSL）API来操作你的数据集。<br>
上图直观地体现了DataFrame和RDD的区别。左侧的RDD[Person]虽然以Person为类型参数，但Spark框架本身不了解Person类的内部结构。而右侧的DataFrame却提供了详细的结构信息，使得Spark SQL可以清楚地知道该数据集中包含哪些列，每列的名称和类型各是什么。DataFrame多了数据的结构信息，即schema。RDD是分布式的Java对象的集合。DataFrame是分布式的Row对象的集合。DataFrame除了提供了比RDD更丰富的算子以外，更重要的特点是提升执行效率、减少数据读取以及执行计划的优化，比如filter下推、裁剪等。<br></p>
<h3>提升执行效率</h3>
RDD API是函数式的，强调不变性，在大部分场景下倾向于创建新对象而不是修改老对象。这一特点虽然带来了干净整洁的API，却也使得Spark应用程序在运行期倾向于创建大量临时对象，对GC造成压力。在现有RDD API的基础之上，我们固然可以利用mapPartitions方法来重载RDD单个分片内的数据创建方式，用复用可变对象的方式来减小对象分配和GC的开销，但这牺牲了代码的可读性，而且要求开发者对Spark运行时机制有一定的了解，门槛较高。另一方面，Spark SQL在框架内部已经在各种可能的情况下尽量重用对象，这样做虽然在内部会打破了不变性，但在将数据返回给用户时，还会重新转为不可变数据。利用
 DataFrame API进行开发，可以免费地享受到这些优化效果。<br><h3>减少数据读取</h3>
分析大数据，最快的方法就是 ——忽略它。这里的“忽略”并不是熟视无睹，而是根据查询条件进行恰当的剪枝。<br>
上文讨论分区表时提到的分区剪 枝便是其中一种——当查询的过滤条件中涉及到分区列时，我们可以根据查询条件剪掉肯定不包含目标数据的分区目录，从而减少IO。<br>
对于一些“智能”数据格 式，Spark SQL还可以根据数据文件中附带的统计信息来进行剪枝。简单来说，在这类数据格式中，数据是分段保存的，每段数据都带有最大值、最小值、null值数量等 一些基本的统计信息。当统计信息表名某一数据段肯定不包括符合查询条件的目标数据时，该数据段就可以直接跳过（例如某整数列a某段的最大值为100，而查询条件要求a &gt; 200）。<br>
此外，Spark SQL也可以充分利用RCFile、ORC、Parquet等列式存储格式的优势，仅扫描查询真正涉及的列，忽略其余列的数据。<br><h3>执行优化</h3>
<div><img src="https://img-blog.csdn.net/20170223212849461" alt=""><br></div>
<div>为了说明查询优化，我们来看上图展示的人口数据分析的示例。图中构造了两个DataFrame，将它们join之后又做了一次filter操作。如果原封不动地执行这个执行计划，最终的执行效率是不高的。因为join是一个代价较大的操作，也可能会产生一个较大的数据集。如果我们能将filter下推到 join下方，先对DataFrame进行过滤，再join过滤后的较小的结果集，便可以有效缩短执行时间。而Spark SQL的查询优化器正是这样做的。简而言之，逻辑查询计划优化就是一个利用基于关系代数的等价变换，将高成本的操作替换为低成本操作的过程。<br>
得到的优化执行计划在转换成物 理执行计划的过程中，还可以根据具体的数据源的特性将过滤条件下推至数据源内。最右侧的物理执行计划中Filter之所以消失不见，就是因为溶入了用于执行最终的读取操作的表扫描节点内。<br>
对于普通开发者而言，查询优化 器的意义在于，即便是经验并不丰富的程序员写出的次优的查询，也可以被尽量转换为高效的形式予以执行。<br><h3>RDD和DataSet比较</h3>
“DataSet以Catalyst逻辑执行计划表示，并且数据以编码的二进制形式被存储，不需要反序列化就可以执行sorting、shuffle等操作。”<br>
“DataSet创立需要一个显式的Encoder，把对象序列化为二进制，可以把对象的scheme映射为Spark<br>
SQl类型，然而RDD依赖于运行时反射机制。”<br>
通过上面两点，DataSet的性能比RDD的要好很多<br><h3>DataFrame和DataSet比较</h3>
Dataset可以认为是DataFrame的一个特例，主要区别是Dataset每一个record存储的是一个强类型值而不是一个Row。因此具有如下三个特点：<br>
1.DataSet可以在编译时检查类型<br>
2.是面向对象的编程接口。用wordcount举例：<br></div>
<p></p>
<p></p><pre><code class="language-java">//DataFrame
// Load a text file and interpret each line as a java.lang.String
val ds = sqlContext.read.text(“/home/spark/1.6/lines”).as[String]
val result = ds
.flatMap(_.split(” “)) // Split on whitespace
.filter(_ != “”) // Filter empty words
.toDF() // Convert to DataFrame to perform aggregation / sorting
.groupBy($”value”) // Count number of occurences of each word
.agg(count(“*”) as “numOccurances”)
.orderBy($”numOccurances” desc) // Show most common words first</code></pre><pre><code class="language-java">//DataSet,完全使用scala编程，不要切换到DataFrame
val wordCount =
ds.flatMap(_.split(” “))
.filter(_ != “”)
.groupBy(_.toLowerCase()) // Instead of grouping on a column expression (i.e. $”value”) we pass a lambda function
.count()</code></pre>3.后面版本DataFrame会继承DataSet，DataFrame是面向Spark SQL的接口。<br>
DataFrame和DataSet可以相互转化，df.as[ElementType]这样可以把DataFrame转化为DataSet，ds.toDF()这样可以把DataSet转化为DataFrame。<br><h3>场景</h3>
<h3>什么时候用RDD？</h3>
使用RDD的一般场景:<br>
你需要使用low-level的transformation和action来控制你的数据集；<br>
你得数据集非结构化，比如，流媒体或者文本流；<br>
你想使用函数式编程来操作你得数据，而不是用特定领域语言（DSL）表达；<br>
你不在乎schema，比如，当通过名字或者列处理（或访问）数据属性不在意列式存储格式；<br>
你放弃使用DataFrame和Dataset来优化结构化和半结构化数据集<br>
RDD在Apache Spark 2.0中惨遭抛弃？答案当然是 NO !<br>
通过后面的描述你会得知：Spark用户可以在RDD，DataFrame和Dataset三种数据集之间无缝转换，而是只需使用超级简单的API方法。<br><h3>什么时候使用DataFrame或者Dataset？</h3>
你想使用丰富的语义，high-level抽象，和特定领域语言API，那你可DataFrame或者Dataset；<br>
你处理的半结构化数据集需要high-level表达， filter，map，aggregation，average，sum ，SQL 查询，列式访问和使用lambda函数，那你可DataFrame或者Dataset；<br>
你想利用编译时高度的type-safety，Catalyst优化和Tungsten的code生成，那你可DataFrame或者Dataset；<br>
你想统一和简化API使用跨Spark的Library，那你可DataFrame或者Dataset；<br>
如果你是一个R使用者，那你可DataFrame或者Dataset；<br>
如果你是一个Python使用者，那你可DataFrame或者Dataset；<br>
你可以无缝的把DataFrame或者Dataset转化成一个RDD，只需简单的调用 .rdd：<br><pre><code class="language-java">// select specific fields from the Dataset, apply a predicate
// using the where() method, convert to an RDD, and show first 10
// RDD rows
val deviceEventsDS = ds.select($”device_name”, $”cca3″, $”c02_level”).where($”c02_level” &gt; 1300)
// convert to RDDs and take the first 10 rows
val eventsRDD = deviceEventsDS.rdd.take(10)</code></pre>
<h3>RDD</h3>
<h3>优点:</h3>
编译时类型安全<br>
编译时就能检查出类型错误<br>
面向对象的编程风格<br>
直接通过类名点的方式来操作数据<br><h3>缺点:</h3>
序列化和反序列化的性能开销<br>
无论是集群间的通信, 还是IO操作都需要对对象的结构和数据进行序列化和反序列化.<br>
GC的性能开销<br>
频繁的创建和销毁对象, 势必会增加GC<br><pre><code class="language-java">import org.apache.spark.sql.SQLContext
import org.apache.spark.{SparkConf, SparkContext}
object Run {
def main(args: Array[String]) {
val conf = new SparkConf().setAppName(“test”).setMaster(“local”)
val sc = new SparkContext(conf)
sc.setLogLevel(“WARN”)
val sqlContext = new SQLContext(sc)
/**
* id age
* 1 30
* 2 29
* 3 21
*/
case class Person(id: Int, age: Int)
val idAgeRDDPerson = sc.parallelize(Array(Person(1, 30), Person(2, 29), Person(3, 21)))
// 优点1
// idAge.filter(_.age &gt; “”) // 编译时报错, int不能跟String比
// 优点2
idAgeRDDPerson.filter(_.age &gt; 25) // 直接操作一个个的person对象
}
}</code></pre>
<h3>DataFrame</h3>
DataFrame引入了schema和off-heap<br>
schema : RDD每一行的数据, 结构都是一样的. 这个结构就存储在schema中. Spark通过schame就能够读懂数据, 因此在通信和IO时就只需要序列化和反序列化数据, 而结构的部分就可以省略了.
<p></p>
<p>off-heap : 意味着JVM堆以外的内存, 这些内存直接受操作系统管理（而不是JVM）。Spark能够以二进制的形式序列化数据(不包括结构)到off-heap中, 当要操作数据时, 就直接操作off-heap内存. 由于Spark理解schema, 所以知道该如何操作.<br>
off-heap就像地盘, schema就像地图, Spark有地图又有自己地盘了, 就可以自己说了算了, 不再受JVM的限制, 也就不再收GC的困扰了.<br>
通过schema和off-heap, DataFrame解决了RDD的缺点, 但是却丢了RDD的优点. DataFrame不是类型安全的, API也不是面向对象风格的.<br></p><pre><code class="language-java">import org.apache.spark.sql.types.{DataTypes, StructField, StructType}
import org.apache.spark.sql.{Row, SQLContext}
import org.apache.spark.{SparkConf, SparkContext}
object Run {
def main(args: Array[String]) {
val conf = new SparkConf().setAppName(“test”).setMaster(“local”)
val sc = new SparkContext(conf)
sc.setLogLevel(“WARN”)
val sqlContext = new SQLContext(sc)
/**
* id age
* 1 30
* 2 29
* 3 21
*/
val idAgeRDDRow = sc.parallelize(Array(Row(1, 30), Row(2, 29), Row(4, 21)))
val schema = StructType(Array(StructField(“id”, DataTypes.IntegerType), StructField(“age”, DataTypes.IntegerType)))
val idAgeDF = sqlContext.createDataFrame(idAgeRDDRow, schema)
// API不是面向对象的
idAgeDF.filter(idAgeDF.col(“age”) &gt; 25)
// 不会报错, DataFrame不是编译时类型安全的
idAgeDF.filter(idAgeDF.col(“age”) &gt; “”)
}
}</code></pre>
<h3>DataSet</h3>
DataSet结合了RDD和DataFrame的优点, 并带来的一个新的概念Encoder<br>
当序列化数据时, Encoder产生字节码与off-heap进行交互, 能够达到按需访问数据的效果, 而不用反序列化整个对象. Spark还没有提供自定义Encoder的API, 但是未来会加入.<br>
下面看DataFrame和DataSet在2.0.0-preview中的实现<br>
下面这段代码, 在1.6.x中创建的是DataFrame<br><pre><code class="language-java"> 
// 上文DataFrame示例中提取出来的
val idAgeRDDRow = sc.parallelize(Array(Row(1, 30), Row(2, 29), Row(4, 21)))
val schema = StructType(Array(StructField(“id”, DataTypes.IntegerType), StructField(“age”, DataTypes.IntegerType)))
val idAgeDF = sqlContext.createDataFrame(idAgeRDDRow, schema)</code></pre>但是同样的代码在2.0.0-preview中, 创建的虽然还叫DataFrame<br><pre><code class="language-java">// sqlContext.createDataFrame(idAgeRDDRow, schema) 方法的实现, 返回值依然是DataFrame
def createDataFrame(rowRDD: RDD[Row], schema: StructType): DataFrame = {
sparkSession.createDataFrame(rowRDD, schema)
}</code></pre>但是其实却是DataSet, 因为DataFrame被声明为Dataset[Row]<br><pre><code class="language-java">package object sql {
// …省略了不相关的代码
type DataFrame = Dataset[Row]
}</code></pre>因此当我们从1.6.x迁移到2.0.0的时候, 无需任何修改就直接用上了DataSet.<br>
下面是一段DataSet的示例代码<br><pre><code class="language-java">import org.apache.spark.sql.types.{DataTypes, StructField, StructType}
import org.apache.spark.sql.{Row, SQLContext}
import org.apache.spark.{SparkConf, SparkContext}
object Test {
def main(args: Array[String]) {
val conf = new SparkConf().setAppName(“test”).setMaster(“local”) // 调试的时候一定不要用local[*]
val sc = new SparkContext(conf)
val sqlContext = new SQLContext(sc)
import sqlContext.implicits._
val idAgeRDDRow = sc.parallelize(Array(Row(1, 30), Row(2, 29), Row(4, 21)))
val schema = StructType(Array(StructField(“id”, DataTypes.IntegerType), StructField(“age”, DataTypes.IntegerType)))
// 在2.0.0-preview中这行代码创建出的DataFrame, 其实是DataSet[Row]
val idAgeDS = sqlContext.createDataFrame(idAgeRDDRow, schema)
// 在2.0.0-preview中, 还不支持自定的Encoder, Row类型不行, 自定义的bean也不行
// 官方文档也有写通过bean创建Dataset的例子，但是我运行时并不能成功
// 所以目前需要用创建DataFrame的方法, 来创建DataSet[Row]
// sqlContext.createDataset(idAgeRDDRow)
// 目前支持String, Integer, Long等类型直接创建Dataset
Seq(1, 2, 3).toDS().show()
sqlContext.createDataset(sc.parallelize(Array(1, 2, 3))).show()
}
}</code></pre>Spark Core提供Spark最基础与最核心的功能，主要包括以下功能：<br>
(1)SparkContext：通常而言，Driver Application的执行与输出都是通过SparkContext来完成的。在正式提交Application之前，首先需要初始化SparkContext。SparkContext隐藏了网络通信、分布式部署、消息通信、存储能力、计算能力、缓存、测量系统、文件服务、Web服务等内容，应用程序开发者只需要使用SparkContext提供的API完成功能开发。SparkContext内置的DAGScheduler负责创建Job，将DAG中的RDD划分到不同的Stage，提交Stage等功能。内置的TaskScheduler负责资源的申请，任务的提交及请求集群对任务的调度等工作。<br>
(2)存储体系：Spark优先考虑使用各节点的内存作为存储，当内存不足时才会考虑使用磁盘，这极大地减少了磁盘IO，提升了任务执行的效率，使得Spark适用于实时计算、流式计算等场景。此外，Spark还提供了以内存为中心的高容错的分布式文件系统Tachyon供用户进行选择。Tachyon能够为Spark提供可靠的内存级的文件共享服务。<br>
(3)计算引擎：计算引擎由SparkContext中的DAGScheduler、RDD以及具体节点上的Executor负责执行的Map和Reduce任务组成。DAGScheduler和RDD虽然位于SparkContext内部，但是在任务正式提交与执行之前会将Job中的RDD组织成有向无环图（DAG），并对Stage进行划分，决定了任务执行阶段任务的数量、迭代计算、shuffle等过程。<br>
(4)部署模式：由于单节点不足以提供足够的存储和计算能力，所以作为大数据处理的Spark在SparkContext的TaskScheduler组件中提供了对Standalone部署模式的实现和Yarn、Mesos等分布式资源管理系统的支持。通过使用Standalone、Yarn、Mesos等部署模式为Task分配计算资源，提高任务的并发执行效率。<br><br><p></p>
            </div>
                </div>