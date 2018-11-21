---
layout:     post
title:      Apache Spark文档翻译之一 （快速入门）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载，同时也欢迎到我的个人站点:kooola.com                |					https://blog.csdn.net/z644041867/article/details/82758581				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><img src="http://kooola.com/upload/2018/06/7qanlnrum8i7jrg86u2vita3b2.jpg" alt="图片"><br>
<a href="http://spark.apache.org/docs/latest/quick-start.html" rel="nofollow">英文版原文</a></p>
<p>本教程为使用spark的快速入门介绍。首先我们会通过Spark的交互式shell(Python或者Scala)来介绍API，然后展示如何使用java、scala以及python来编写spark程序。</p>
<p>跟随这个指导，首先从Spark官网下载spark软件包。因为我们未必正在使用HDFS，你可以下载针对任何版本hadoop的spark软件包。</p>
<p>注意，在Spark 2.0之前，spark主要的编程接口是弹性分布式数据集（Resilient Distributed Dataset:RDD）。Spark 2.0之后，RDDS被Dataset取代，Dataset跟RDD非常相似，但是具有更好的性能。RDD的接口在2.0以后依然被支持，你可以在这里<a href="http://spark.apache.org/docs/latest/rdd-programming-guide.html" rel="nofollow">RDD编程指导</a>获得更完整的参考。然而，我们更加推荐你使用Dataset,因为相对RDD，它有更好的性能。点击<a href="http://spark.apache.org/docs/latest/sql-programming-guide.html" rel="nofollow">SQL编程指导</a>获取更多关于Dataset的信息。</p>
<h1><a id="Spark_Shell_9"></a>使用Spark Shell进行交互式分析</h1>
<h2><a id="_10"></a>基础</h2>
<p>Spark的shell提供了一种简单方式来学习API，并且是一个交互式分析数据的强大工具。使用Scala和python语言都能轻松使用。在Spark目录下运行如下命令来启动它：</p>
<pre><code>./bin/spark-shell
</code></pre>
<p>Spark的主要抽象是一个关于items的分布式集合，我们称之Dataset。Dataset可以创建自Hadppo inputFormats（例如HDFS的文件），或者通过其他Dataset来转换得到。现在，让我们使用Spark目录中的README文件内容来制作一个新的Dataset:</p>
<pre><code>scala&gt; val textFile = spark.read.textFile("README.md")
textFile: org.apache.spark.sql.Dataset[String] = [value: string]
</code></pre>
<p>你可以通过调用一些动作（actions）直接从Dataset中获取值，或者转换当前Dataset得到一个新的Dataset。对于更多细节，请查看<a href="http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.Dataset" rel="nofollow">API文档</a>。</p>
<pre><code>scala&gt; textFile.count() // Number of items in this Dataset
res0: Long = 126 // May be different from yours as README.md will change over time, similar to other outputs

scala&gt; textFile.first() // First item in this Dataset
res1: String = # Apache Spark
</code></pre>
<p>现在，让我们通过转换（transform）Dataset来得到一个新的Dataset。我们调用filter来返回一个新的Dataset：</p>
<pre><code>scala&gt; val linesWithSpark = textFile.filter(line =&gt; line.contains("Spark"))
linesWithSpark: org.apache.spark.sql.Dataset[String] = [value: string]
</code></pre>
<p>当然，我们可以把动作和转换放在一起一次性操作：</p>
<pre><code>scala&gt; textFile.filter(line =&gt; line.contains("Spark")).count() // How many lines contain "Spark"?
res3: Long = 15
</code></pre>
<h2><a id="Dataset_38"></a>更多在Dataset上的操作</h2>
<p>Dataset的动作和转换可以被用于更复杂的计算。接下来让我们看看如何找到文件中单词数最多的一行：</p>
<pre><code>scala&gt; textFile.map(line =&gt; line.split(" ").size).reduce((a, b) =&gt; if (a &gt; b) a else b)
res4: Long = 15
</code></pre>
<p>首先将一行转换为整数值，创建一个Dataset。在Dataset上调用reduce来查找最大的单词数。map和reduce的参数值是Scala函数的字面值，并且能使用任何语言的特性或者Scala/Java库。例如，我们可以很容易地调用在别处声明的函数。下面，我们使用Math.max()函数来让代码更容易理解：</p>
<pre><code>scala&gt; import java.lang.Math
import java.lang.Math

scala&gt; textFile.map(line =&gt; line.split(" ").size).reduce((a, b) =&gt; Math.max(a, b))
res5: Int = 15
</code></pre>
<p>一个常见的是数据流模式是在hadoop中流行的mapreduce。Spark可以很容易地实现mapreducede：</p>
<pre><code>scala&gt; val wordCounts = textFile.flatMap(line =&gt; line.split(" ")).groupByKey(identity).count()
wordCounts: org.apache.spark.sql.Dataset[(String, Long)] = [value: string, count(1): bigint]
</code></pre>
<p>这里，我们调用flatmap讲行的Dataset转换成单词的Dataset,然后结合groupByKey和count来计算文件中每个单词的个数，结果形式为（String,Long）对的Dataset。我们可以在shell中调用collect收集单词个数</p>
<pre><code>scala&gt; wordCounts.collect()
res6: Array[(String, Int)] = Array((means,1), (under,2), (this,3), (Because,1), (Python,2), (agree,1), (cluster.,1), ...)
</code></pre>
<h2><a id="_64"></a>缓存</h2>
<p>Spark支持将数据集放入一个集群式的内存缓存中。当数据需要经常被访问时非常有用，例如查询少量热点数据集或者当运行像PageRank这样的迭代算法。作为一个简单的示例，我们标记linesWithSpark数据集作为缓存：</p>
<pre><code>scala&gt; linesWithSpark.cache()
res7: linesWithSpark.type = [value: string]

scala&gt; linesWithSpark.count()
res8: Long = 15

scala&gt; linesWithSpark.count()
res9: Long = 15
</code></pre>
<p>使用Spark来标记并缓存一个100行的文本看起来有点大材小用。有趣的地方是这些相同的方法可以运用到非常大的数据集上，甚至当它们被横跨几十个或几百个节点时。你要可以使用<br>
bin/spark-shell来链接集群进行交互式操作，详细介绍点击<a href="http://spark.apache.org/docs/latest/rdd-programming-guide.html#using-the-shell" rel="nofollow">RDD编程指导</a></p>
<h2><a id="_79"></a>程序实现</h2>
<p>假如我们想自己使用Spark API来编写程序。我们将使用scala（使用sdt）,java(使用maven),python(使用pip)来完成一个简单的程序。<br>
我们将使用Scala编写一个简单的Spark程序-如此简单，我们将它命名为SimpleApp.scala:</p>
<pre><code>/* SimpleApp.scala */
import org.apache.spark.sql.SparkSession

object SimpleApp {
  def main(args: Array[String]) {
    val logFile = "YOUR_SPARK_HOME/README.md" // Should be some file on your system
    val spark = SparkSession.builder.appName("Simple Application").getOrCreate()
    val logData = spark.read.textFile(logFile).cache()
    val numAs = logData.filter(line =&gt; line.contains("a")).count()
    val numBs = logData.filter(line =&gt; line.contains("b")).count()
    println(s"Lines with a: $numAs, Lines with b: $numBs")
    spark.stop()
  }
}
</code></pre>
<p>请注意，应用程序应定义main（）方法，而不是扩展scala.App。scala.App的子类有可能不能正确地工作。<br>
这个程序只用来计算Spark README文件中包含’a’和’b’的行数。注意，你需要使用Spark安装目录来替换代码中的YOUR_SPARK_HOME。不像前面使用Spark shell的例子（自动初始化SparkSession），在编写程序时需要考虑在使用前进行初始化。<br>
我们调用SparkSession.builder来构造一个SparkSession，设置程序名称，最后调用getOrCreate来得到一个实例。<br>
我们的程序依赖 Spark API，所以我们需要一个sbt的配置文件：build.sbt，表示Spark为一个依赖项。这个文件还添加了Spark的依赖库：</p>
<pre><code>name := "Simple Project"

version := "1.0"

scalaVersion := "2.11.8"

libraryDependencies += "org.apache.spark" %% "spark-sql" % "2.3.1"
</code></pre>
<p>为了使sbt正常工作，我们需要根据典型的目录结构布局SimpleApp.scala和build.sbt。完成之后，我们可以创建一个包含应用程序代码的JAR包，然后使用spark-submit脚本来运行我们的程序：</p>
<pre><code># Your directory layout should look like this
$ find .
.
./build.sbt
./src
./src/main
./src/main/scala
./src/main/scala/SimpleApp.scala

# Package a jar containing your application
$ sbt package
...
[info] Packaging {..}/{..}/target/scala-2.11/simple-project_2.11-1.0.jar

# Use spark-submit to run your application
$ YOUR_SPARK_HOME/bin/spark-submit \
  --class "SimpleApp" \
  --master local[4] \
  target/scala-2.11/simple-project_2.11-1.0.jar
...
Lines with a: 46, Lines with b: 23
</code></pre>
<h2><a id="_136"></a>接下来</h2>
<p>恭喜你成功运行了第一个Spark程序。</p>
<ul>
<li>深入了解API，点击<a href="http://spark.apache.org/docs/latest/rdd-programming-guide.html" rel="nofollow">RDD编程知道</a>和<a href="http://spark.apache.org/docs/latest/sql-programming-guide.html" rel="nofollow">SQL编程指导</a></li>
<li>想了解如何将程序运行到集群上，点击<a href="http://spark.apache.org/docs/latest/cluster-overview.html" rel="nofollow">部署概要</a></li>
<li>最后,examples目录中包含了几个示例 (Scala, Java, Python, R)，你可以像下面一样运行它们:</li>
</ul>
<pre><code># For Scala and Java, use run-example:
./bin/run-example SparkPi

# For Python examples, use spark-submit directly:
./bin/spark-submit examples/src/main/python/pi.py

# For R examples, use spark-submit directly:
./bin/spark-submit examples/src/main/r/dataframe.R
</code></pre>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>