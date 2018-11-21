---
layout:     post
title:      Apache Spark 入门简介
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本人博客，供大家分享学习，有需要的话，可以转载！					https://blog.csdn.net/u011225629/article/details/47839495				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
Spark已经被很多巨头使用，包括Amazon、eBay以及Yahoo!。很多组织都在拥有成千上万节点的集群上运行Spark。根据Spark FAQ，已知的最大的Spark集群拥有超过8000个节点。Spark确实是一个值得好好考虑和学习的技术。
<p><img alt="" src="http://ww4.sinaimg.cn/mw690/6941baebjw1ev5gmnzyq8j20h80h8wjo.jpg"></p>
<p>这篇文章会向你介绍Spark，包括用例和示例。其中的信息来自于Apache Spark网站以及 <a href="http://www.amazon.com/Learning-Spark-Lightning-Fast-Data-Analysis/dp/1449358624" rel="nofollow"><span style="color:#0099cc;">学习Spark – 快如闪电的大数据分析</span></a> 一书。</p>
<h2 id="what-is-apache-spark-an-introduction"><span style="font-family:'Microsoft YaHei', '宋体', 'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:42px;font-size:28px;font-style:normal;">Apache Spark是什么？一个简单介绍</span></h2>
<p>Spark是一个Apache项目，它被标榜为“快如闪电的集群计算”。它拥有一个繁荣的开源社区，并且是目前最活跃的Apache项目。</p>
<p>Spark提供了一个更快、更通用的数据处理平台。和Hadoop相比，Spark可以让你的程序在内存中运行时速度提升100倍，或者在磁盘上运行时速度提升10倍。去年，在100 TB Daytona GraySort比赛中，Spark战胜了Hadoop，它只使用了十分之一的机器，但运行速度提升了3倍。Spark也已经成为 <a href="http://databricks.com/blog/2014/10/10/spark-petabyte-sort.html" rel="nofollow"><span style="color:#0099cc;">针对
 PB 级别数据排序的最快的开源引擎</span></a>。</p>
<div>
<div class="syntaxhighlighter notranslate text ie" id="highlighter_963525">
<table border="0" cellspacing="0" cellpadding="0"><tbody><tr><td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
</td>
<td class="code">
<div>
<div class="line number1 index0 alt2"><code class="text plain">sparkContext.textFile("hdfs://...")</code></div>
<div class="line number2 index1 alt1"><code class="text spaces">            </code><code class="text plain">.flatMap(line =&amp;gt; line.split(" "))</code></div>
<div class="line number3 index2 alt2"><code class="text spaces">            </code><code class="text plain">.map(word =&amp;gt; (word, 1)).reduceByKey(_ + _)</code></div>
<div class="line number4 index3 alt1"><code class="text spaces">            </code><code class="text plain">.saveAsTextFile("hdfs://...")</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
<p>Spark也让我们更快地编写代码变得可能，这就好像有80多个高水平的操作员在帮你处理。为了说明这一点，我们来看一下大数据中的“Hello World!”：单词个数统计示例。在MapReduce中，我们需要编写大概50行代码来实现这一功能，但对于Spark（以及Scala）来说，你可以像下面这样简单实现：</p>
<div>
<div class="syntaxhighlighter notranslate text ie" id="highlighter_687655">
<table border="0" cellspacing="0" cellpadding="0"><tbody><tr><td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
</td>
<td class="code">
<div>
<div class="line number1 index0 alt2"><code class="text plain">sparkContext.textFile("hdfs://...")</code></div>
<div class="line number2 index1 alt1"><code class="text spaces">            </code><code class="text plain">.flatMap(line =&amp;gt; line.split(" "))</code></div>
<div class="line number3 index2 alt2"><code class="text spaces">            </code><code class="text plain">.map(word =&amp;gt; (word, 1)).reduceByKey(_ + _)</code></div>
<div class="line number4 index3 alt1"><code class="text spaces">            </code><code class="text plain">.saveAsTextFile("hdfs://...")</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
<p>在学习如何使用Apache Spark时，另外一个重要的部分就是交互式shell（REPL），它是开箱即用的。通过使用REPL，我们可以测试每一行代码的输出，而无需首先编写和执行整个作业（job）。这样，你可以更快得到可工作的代码，并且点对点数据分析也变得可能。</p>
<p>Spark还提供了其它一些关键特性：</p>
<ul><li>目前提供了针对Scala、Java和Python的API，即将提供针对其它语言（例如R）的支持。</li><li>可以很好地和Hadoop生态系统和数据源（HDFS、Amazon S3、Hive、HBase、Cassandra等）进行集成。</li><li>可以运行在由Hadoop YARN或者Apache Mesos管理的集群上，也可以运行在单独的集群上。</li></ul><p>Spark核心由一组功能强大的、高级别的库组成，这些库可以无缝的应用到同一个应用程序中。目前这些库包括SparkSQL、Spark Streaming、MLlib（用于机器学习）以及GraphX，我们会在稍后针对每一个库进行进一步描述。  其它一些Spark库和扩展也在陆续开发过程中。</p>
<p><img alt="" src="http://ww3.sinaimg.cn/mw690/6941baebjw1ev5gmnbuifj20hs0dsju5.jpg"></p>
<h2 id="spark-core"><span style="font-family:'Microsoft YaHei', '宋体', 'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:42px;font-size:28px;font-style:normal;">Spark Core</span></h2>
<p>Spark Core是一个基本引擎，用于大规模并行和分布式数据处理。它主要负责：</p>
<ul><li>内存管理和故障恢复</li><li>在集群上安排、分布和监控作业</li><li>和存储系统进行交互</li></ul><p>Spark引入了一个称为弹性分布式数据集（RDD，Resilient Distributed Dataset）的概念，它是一个不可变的、容错的、分布式对象集合，我们可以并行的操作这个集合。RDD可以包含任何类型的对象，它在加载外部数据集或者从驱动应用程序分发集合时创建。</p>
<p>RDD支持两种操作类型：</p>
<ul><li>转换是一种操作（例如映射、过滤、联接、联合等等），它在一个RDD上执行操作，然后创建一个新的RDD来保存结果。</li><li>行动是一种操作（例如归并、计数、第一等等），它在一个RDD上执行某种计算，然后将结果返回。</li></ul><p>在Spark中，转换是“懒惰”的，也就是说它们不会立刻计算出结果。相反，它们只是“记住”要执行的操作以及要操作的数据集（例如文件）。只有当行为被调用时，转换才会真正的进行计算，并将结果返回给驱动器程序。这种设计让Spark运行得更有效率。例如，如果一个大文件要通过各种方式进行转换操作，并且文件被传递给第一个行为，那么Spark只会处理文件的第一行内容并将结果返回，而不会处理整个文件。</p>
<p>默认情况下，当你在经过转换的RDD上运行一个行为时，这个RDD有可能会被重新计算。然而，你也可以通过使用持久化或者缓存的方法，将一个RDD持久化从年初在内存中，这样，Spark就会在集群上保留这些元素，当你下一次查询它时，查询速度会快很多。</p>
<h2 id="sparksql"><span style="font-family:'Microsoft YaHei', '宋体', 'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:42px;font-size:28px;font-style:normal;">SparkSQL</span></h2>
<p id="spark-streaming">SparkSQL是Spark的一个组件，它支持我们通过SQL或者Hive查询语言来查询数据。它最初来自于Apache Hive项目，用于运行在Spark上（来代替MapReduce），现在它已经被集成到Spark堆中。除了针对各种各样的数据源提供支持，它还让代码转换与SQL查询编织在一起变得可能，这最终会形成一个非常强大的工具。下面是一个兼容Hive的查询示例：</p>
<div>
<div class="syntaxhighlighter notranslate text ie" id="highlighter_948660">
<table border="0" cellspacing="0" cellpadding="0"><tbody><tr><td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
</td>
<td class="code">
<div>
<div class="line number1 index0 alt2"><code class="text plain">// sc is an existing SparkContext.</code></div>
<div class="line number2 index1 alt1"><code class="text plain">val sqlContext = new org.apache.spark.sql.hive.HiveContext(sc)</code></div>
<div class="line number3 index2 alt2"> </div>
<div class="line number4 index3 alt1"><code class="text plain">sqlContext.sql("CREATE TABLE IF NOT EXISTS src (key INT, value STRING)")</code></div>
<div class="line number5 index4 alt2"><code class="text plain">sqlContext.sql("LOAD DATA LOCAL INPATH 'examples/src/main/resources/kv1.txt' INTO TABLE src")</code></div>
<div class="line number6 index5 alt1"> </div>
<div class="line number7 index6 alt2"><code class="text plain">// Queries are expressed in HiveQL</code></div>
<div class="line number8 index7 alt1"><code class="text plain">sqlContext.sql("FROM src SELECT key, value").collect().foreach(println)</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
<h2><span style="font-family:'Microsoft YaHei', '宋体', 'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:42px;font-size:28px;font-style:normal;">Spark Streaming</span></h2>
<p>Spark Streaming支持对流数据的实时处理，例如产品环境web服务器的日志文件（例如Apache Flume和HDFS/S3）、诸如Twitter的社交媒体以及像Kafka那样的各种各样的消息队列。在这背后，Spark Streaming会接收输入数据，然后将其分为不同的批次，接下来Spark引擎来处理这些批次，并根据批次中的结果，生成最终的流。整个过程如下所示。</p>
<p><img alt="" src="http://ww3.sinaimg.cn/mw690/6941baebjw1ev5gmmorw1j20hs0dsq65.jpg"></p>
<p>Spark Streaming API可以非常紧密匹配Spark核心API，这使得程序员可以很容易的工作在批处理数据和流数据的海洋中。</p>
<h1>MLlib</h1>
<p>MLlib是一个机器学习库，它提供了各种各样的算法，这些算法用来在集群上针对分类、回归、聚类、协同过滤等（可以在 machine learning 上查看Toptal的文章，来获取更过的信息）。其中一些算法也可以应用到流数据上，例如使用普通最小二乘法或者K均值聚类（还有更多）来计算线性回归。Apache Mahout（一个针对Hadoop的机器学习库）已经脱离MapReduce，转而加入Spark MLlib。</p>
<h2 id="graphx"><span style="font-family:'Microsoft YaHei', '宋体', 'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:42px;font-size:28px;font-style:normal;">GraphX</span></h2>
<p><img alt="" src="http://ww2.sinaimg.cn/mw690/6941baebjw1ev5gmlwad0j20o008875e.jpg"></p>
<p>GraphX是一个库，用来处理图，执行基于图的并行操作。它针对ETL、探索性分析和迭代图计算提供了统一的工具。除了针对图处理的内置操作，GraphX还提供了一个库，用于通用的图算法，例如PageRank。</p>
<h2 id="how-to-use-apache-spark-event-detection-use-case"><span style="font-family:'Microsoft YaHei', '宋体', 'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:42px;font-size:28px;font-style:normal;">如何使用Apache Spark：事件探测用例</span></h2>
<p>既然我们已经回答了“Apache Spark是什么？”这个问题，接下来让我们思考一下，使用Spark来解决什么样的问题或者挑战最有效率。</p>
<p>最近，我偶然看到了一篇关于 通过分析Twitter流的方式来探测地震 的文章。它展示了这种技术可以比日本气象厅更快的通知你日本哪里发生了地震。虽然那篇文章使用了不同的技术，但我认为这是一个很好的示例，可以用来说明我们如何通过简单的代码片段，在不需要”胶水代码“的情况下应用Spark。</p>
<p>首先，我们需要处理tweet，将那些和”地震“或”震动“等相关的内容过滤出来。我们可以使用Spark Streaming的方式很容易实现这一目标，如下所示：</p>
<div>
<div class="syntaxhighlighter notranslate text ie" id="highlighter_103078">
<table border="0" cellspacing="0" cellpadding="0"><tbody><tr><td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
</td>
<td class="code">
<div>
<div class="line number1 index0 alt2"><code class="text plain">TwitterUtils.createStream(...)</code></div>
<div class="line number2 index1 alt1"><code class="text spaces">            </code><code class="text plain">.filter(_.getText.contains("earthquake") || _.getText.contains("shaking"))</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
<p>然后，我们需要在tweets上运行一些语义分析，来确定它们是否代表当前发生了地震。例如，像“地震！”或者“现在正在震动”这样的tweets，可能会被认为是正向匹配，而像“参加一个地震会议”或者“昨天的地震真可怕”这样的tweets，则不是。这篇文章的作者使用了一个支持向量机（support vector machine, SVM)来实现这一点。我们在这里使用同样的方式，但也可以试一下 <a href="http://spark.apache.org/docs/1.2.1/mllib-linear-methods.html#streaming-linear-regression" rel="nofollow"><span style="color:#0099cc;">流版本</span></a>。一个使用了MLlib的代码示例如下所示：</p>
<div>
<div class="syntaxhighlighter notranslate text ie" id="highlighter_146551">
<table border="0" cellspacing="0" cellpadding="0"><tbody><tr><td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
<div class="line number10 index9 alt1">10</div>
<div class="line number11 index10 alt2">11</div>
<div class="line number12 index11 alt1">12</div>
<div class="line number13 index12 alt2">13</div>
<div class="line number14 index13 alt1">14</div>
<div class="line number15 index14 alt2">15</div>
<div class="line number16 index15 alt1">16</div>
<div class="line number17 index16 alt2">17</div>
<div class="line number18 index17 alt1">18</div>
<div class="line number19 index18 alt2">19</div>
<div class="line number20 index19 alt1">20</div>
<div class="line number21 index20 alt2">21</div>
<div class="line number22 index21 alt1">22</div>
<div class="line number23 index22 alt2">23</div>
<div class="line number24 index23 alt1">24</div>
<div class="line number25 index24 alt2">25</div>
<div class="line number26 index25 alt1">26</div>
</td>
<td class="code">
<div>
<div class="line number1 index0 alt2"><code class="text plain">// We would prepare some earthquake tweet data and load it in LIBSVM format.</code></div>
<div class="line number2 index1 alt1"><code class="text plain">val data = MLUtils.loadLibSVMFile(sc, "sample_earthquate_tweets.txt")</code></div>
<div class="line number3 index2 alt2"> </div>
<div class="line number4 index3 alt1"><code class="text plain">// Split data into training (60%) and test (40%).</code></div>
<div class="line number5 index4 alt2"><code class="text plain">val splits = data.randomSplit(Array(0.6, 0.4), seed = 11L)</code></div>
<div class="line number6 index5 alt1"><code class="text plain">val training = splits(0).cache()</code></div>
<div class="line number7 index6 alt2"><code class="text plain">val test = splits(1)</code></div>
<div class="line number8 index7 alt1"> </div>
<div class="line number9 index8 alt2"><code class="text plain">// Run training algorithm to build the model</code></div>
<div class="line number10 index9 alt1"><code class="text plain">val numIterations = 100</code></div>
<div class="line number11 index10 alt2"><code class="text plain">val model = SVMWithSGD.train(training, numIterations)</code></div>
<div class="line number12 index11 alt1"> </div>
<div class="line number13 index12 alt2"><code class="text plain">// Clear the default threshold.</code></div>
<div class="line number14 index13 alt1"><code class="text plain">model.clearThreshold()</code></div>
<div class="line number15 index14 alt2"> </div>
<div class="line number16 index15 alt1"><code class="text plain">// Compute raw scores on the test set.
</code></div>
<div class="line number17 index16 alt2"><code class="text plain">val scoreAndLabels = test.map { point =&amp;gt;</code></div>
<div class="line number18 index17 alt1"><code class="text spaces">  </code><code class="text plain">val score = model.predict(point.features)</code></div>
<div class="line number19 index18 alt2"><code class="text spaces">  </code><code class="text plain">(score, point.label)</code></div>
<div class="line number20 index19 alt1"><code class="text plain">}</code></div>
<div class="line number21 index20 alt2"> </div>
<div class="line number22 index21 alt1"><code class="text plain">// Get evaluation metrics.</code></div>
<div class="line number23 index22 alt2"><code class="text plain">val metrics = new BinaryClassificationMetrics(scoreAndLabels)</code></div>
<div class="line number24 index23 alt1"><code class="text plain">val auROC = metrics.areaUnderROC()</code></div>
<div class="line number25 index24 alt2"> </div>
<div class="line number26 index25 alt1"><code class="text plain">println("Area under ROC = " + auROC)</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
<p id="other-apache-spark-use-cases">如果对于这个模型的预测比例满意，我们可以继续往下走，无论何时发现地震，我们都要做出反应。为了检测一个地震，我们需要在一个指定的时间窗口内（如文章中所述）有一定数量（例如密度）的正向tweets。请注意，对于带有Twitter位置服务信息的tweets来说，我们还能够从中提取地震的位置信息。有了这个只是以后，我们可以使用SparkSQL来查询现有的Hive表（保存那些对接收地震通知感兴趣的用户）来获取用户的邮箱地址，并向他们发送一些个性化的警告邮件，如下所示：</p>
<div>
<div class="syntaxhighlighter notranslate text ie" id="highlighter_177022">
<table border="0" cellspacing="0" cellpadding="0"><tbody><tr><td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
</td>
<td class="code">
<div>
<div class="line number1 index0 alt2"><code class="text plain">// sc is an existing SparkContext.</code></div>
<div class="line number2 index1 alt1"><code class="text plain">val sqlContext = new org.apache.spark.sql.hive.HiveContext(sc)</code></div>
<div class="line number3 index2 alt2"><code class="text plain">// sendEmail is a custom function</code></div>
<div class="line number4 index3 alt1"><code class="text plain">sqlContext.sql("FROM earthquake_warning_users SELECT firstName, lastName, city, email")</code></div>
<div class="line number5 index4 alt2"><code class="text spaces">          </code><code class="text plain">.collect().foreach(sendEmail)</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
<h2><span style="font-family:'Microsoft YaHei', '宋体', 'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:42px;font-size:28px;font-style:normal;">其它Apache Spark用例</span></h2>
<p>当然，Spark潜在的用例远远超出了地震预测。</p>
<p>下面是一个针对其它一些用例示例（当然远远没有列举全部），这些用例都需要快速处理各种各样的大数据，Spark也非常适合处理这些用例：</p>
<p>在游戏领域，如果能从实时游戏的事件的潜流中处理和发现模式，并能够快速做出响应，这种能力可以带来一门赚钱的生意，针对这种目的的例子包括玩家保留、定位广告、自动调整复杂度等等。</p>
<p>在电子商务领域，实时交易的信息可以被传到像K均值这样的流聚集算法或者像ALS这样的协同过滤的算法上。而产生的结果可能会组合其它一些非结构化的数据源，例如客户评论或者产品评审。随着时间的推移，我们可以用它来提升和改进系统的推荐功能。</p>
<p>在金融或者安全领域，Spark技术栈可以用于欺诈或者入侵检测系统或者基于风险的认证系统。通过分析大规模的压缩日志，并结合外部数据源，例如已经泄漏的数据以及泄漏的账户（可以参考<a href="https://haveibeenpwned.com/" rel="nofollow"><span style="color:#0099cc;">https://haveibeenpwned.com/</span></a>）、从连接/请求中得到的一些诸如IP地址或者时间等信息，我们可以实现一个非常好的结果。</p>
<h2 id="conclusion"><span style="font-family:'Microsoft YaHei', '宋体', 'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:42px;font-size:28px;font-style:normal;">结论</span></h2>
<p>总之，Spark可以帮助我们简化处理那些需要处理大量实时或压缩数据的计算密集型任务和挑战。这些数据既包括结构化数据，也包括非结构化数据。Spark可以和其它一些复杂能力进行无缝集成，例如机器学习、图算法等。Spark将大数据处理变得“接地气”。赶快来试试吧。</p>
            </div>
                </div>