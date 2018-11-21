---
layout:     post
title:      Spark Learning
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-github-gist">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<blockquote>
  <p>这篇博客是本人在学习和使用Spark的过程中的所作的笔记！</p>
</blockquote>

<ul>
<li><p>Spark </p>

<ul><li>Spark core</li>
<li>Spark SQL</li>
<li>Spark Streaming</li>
<li>MLib(mechine learing)</li>
<li>GraphX(graph)</li></ul></li>
<li><p>submit the spark job.</p></li>
</ul>



<pre class="prettyprint"><code class=" hljs avrasm">mvn clean &amp;&amp; mvn compile &amp;&amp; mvn package
$SPARK_HOME/bin/spark-submit \
  --class <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.oreilly</span><span class="hljs-preprocessor">.learningsparkexamples</span><span class="hljs-preprocessor">.mini</span><span class="hljs-preprocessor">.java</span><span class="hljs-preprocessor">.WordCount</span> \
  ./target/learning-spark-mini-example-<span class="hljs-number">0.0</span><span class="hljs-number">.1</span><span class="hljs-preprocessor">.jar</span> \
  ./README<span class="hljs-preprocessor">.md</span> ./wordcounts</code></pre>

<ul>
<li>RDD:  <br>
Each RDD is split into multiple partitions, which may be computed on different nodes of the cluster.</li>
</ul>

<p>As we’ve discussed, RDDs support two types of operations: transformations and actions. Transformations are operations on RDDs that return a new RDD, such as map() and filter(). Actions are operations that return a result to the driver program or write it to storage, and kick off a computation, such as count() and first(). Spark treats transformations and actions very differently, so understanding which type of operation you are performing will be important. <strong>If you are ever confused whether a given function is a transformation or an action, you can look at its return type: transformations return RDDs, whereas actions return some other data type.</strong></p>

<p>It is important to note that each time we call a new action, the entire RDD must be computed “from scratch.” To avoid this inefficiency, users can persist intermediate results, as we will cover in “Persistence (Caching)”.</p>

<ul>
<li>aggregate <br>
the method can change the Type. the following code is for get average.</li>
</ul>



<pre class="prettyprint"><code class="language-java hljs ">val result = input.aggregate((<span class="hljs-number">0</span>, <span class="hljs-number">0</span>))(
              <span class="hljs-comment">/*an operator used to accumulate results within a partition, this operator is similar with fold()*/</span>
               (acc, value) =&gt; (acc._1 + value, acc._2 + <span class="hljs-number">1</span>),
               <span class="hljs-comment">/*an associative operator used to combine results from different partitions*/</span>
               (acc1, acc2) =&gt; (acc1._1 + acc2._1, acc1._2 + acc2._2))
val avg = result._1 / result._2.toDouble</code></pre>

<ul>
<li><p>cogroup??</p></li>
<li><p>partitionBy</p></li>
</ul>



<pre class="prettyprint"><code class=" hljs markdown">val sc = new SparkContext(...)
val userData = sc.sequenceFile[<span class="hljs-link_label">UserID, UserInfo</span>](<span class="hljs-link_url">"hdfs://..."</span>)
<span class="hljs-code">                 .partitionBy(new HashPartitioner(100))   // Create 100 partitions</span>
<span class="hljs-code">                 .persist()</span>
</code></pre>

<blockquote>
  <p>Note that partitionBy() is a transformation, so it always returns a new RDD — it does not change the original RDD in place. RDDs can never be modified once created. Therefore it is important to persist and save as userData the result of partitionBy(), not the original sequenceFile(). Also, the 100 passed to partitionBy() represents the number of partitions, which will control how many parallel tasks perform further operations on the RDD (e.g., joins); in general, make this at least as large as the number of cores in your cluster.without persist(), subsequent RDD actions will evaluate the entire lineage of partitioned, which will cause pairs to be hash-partitioned over and over.</p>
</blockquote>



<pre class="prettyprint"><code class=" hljs avrasm">scala&gt; val pairs = sc<span class="hljs-preprocessor">.parallelize</span>(List((<span class="hljs-number">1</span>, <span class="hljs-number">1</span>), (<span class="hljs-number">2</span>, <span class="hljs-number">2</span>), (<span class="hljs-number">3</span>, <span class="hljs-number">3</span>)))
<span class="hljs-label">pairs:</span> spark<span class="hljs-preprocessor">.RDD</span>[(Int, Int)] = ParallelCollectionRDD[<span class="hljs-number">0</span>] at parallelize at &lt;console&gt;:<span class="hljs-number">12</span>

scala&gt; pairs<span class="hljs-preprocessor">.partitioner</span>
<span class="hljs-label">res0:</span> Option[spark<span class="hljs-preprocessor">.Partitioner</span>] = None

scala&gt; val partitioned = pairs<span class="hljs-preprocessor">.partitionBy</span>(new spark<span class="hljs-preprocessor">.HashPartitioner</span>(<span class="hljs-number">2</span>))
<span class="hljs-label">partitioned:</span> spark<span class="hljs-preprocessor">.RDD</span>[(Int, Int)] = ShuffledRDD[<span class="hljs-number">1</span>] at partitionBy at &lt;console&gt;:<span class="hljs-number">14</span>

scala&gt; partitioned<span class="hljs-preprocessor">.partitioner</span>
<span class="hljs-label">res1:</span> Option[spark<span class="hljs-preprocessor">.Partitioner</span>] = Some(spark<span class="hljs-preprocessor">.HashPartitioner</span><span class="hljs-localvars">@5147788</span>d)</code></pre>

<ul>
<li>shared variables:         aggregation and broadcasts</li>
</ul>

<p>Spark’s shared variables, accumulators and broadcast variables, relax this restriction for two common types of communication patterns: aggregation of results and broadcasts.</p>

<ul>
<li>mapPartitions and map</li>
</ul>

<blockquote>
  <p>Q1. What’s the difference between an RDD’s map and mapPartitions <br>
  map works the function being utilized at a per element level while mapPartitions exercises the function at the partition level.</p>
  
  <p>Tip <br>
  Spark’s documentation consistently uses the terms driver and executor when describing the processes that execute each Spark application. The terms master and worker are used to describe the centralized and distributed portions of the cluster manager. It’s easy to confuse these terms, so pay close attention. For instance, Hadoop YARN runs a master daemon (called the Resource Manager) and several worker daemons called Node Managers. Spark can run both drivers and executors on the YARN worker nodes.</p>
</blockquote>

<ul>
<li><p>spark configuration precedence order <br>
The highest priority is given to configurations declared explicitly in the user’s code using the set() function on a SparkConf object. Next are flags passed to spark-submit, then values in the properties file, and finally default values.</p></li>
<li><p>coalesce() operator</p></li>
</ul>



<h2 id="problem-and-solution">problem and solution</h2>



<h3 id="problem-1">problem 1</h3>



<pre class="prettyprint"><code class=" hljs livecodeserver"><span class="hljs-operator">a</span> Spark error: Invalid signature <span class="hljs-built_in">file</span> digest <span class="hljs-keyword">for</span> Manifest main attributes</code></pre>

<p>原因：</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">When <span class="hljs-keyword">using</span> spark-submit <span class="hljs-built_in">to</span> run <span class="hljs-operator">a</span> jar, you may encounter this error:
Invalid signature <span class="hljs-built_in">file</span> digest <span class="hljs-keyword">for</span> Manifest main attributes
The error occurs when <span class="hljs-constant">one</span> <span class="hljs-operator">of</span> <span class="hljs-operator">the</span> included libraries <span class="hljs-operator">in</span> <span class="hljs-operator">the</span> jar<span class="hljs-string">'s META-INF directory has a bad signature.</span></code></pre>

<p>solution 1-1    </p>



<pre class="prettyprint"><code class=" hljs applescript">One <span class="hljs-keyword">of</span> <span class="hljs-keyword">the</span> jars you extracted <span class="hljs-keyword">to</span> <span class="hljs-keyword">the</span> target jar was signed. The easiest way <span class="hljs-keyword">to</span> fix <span class="hljs-keyword">it</span> <span class="hljs-keyword">is</span> <span class="hljs-keyword">to</span> select <span class="hljs-string">"copy to the output directory and link via manifest"</span> <span class="hljs-keyword">in</span> <span class="hljs-keyword">the</span> <span class="hljs-string">"Create JAR from Modules"</span> dialog</code></pre>

<p>solution 1-2 <br>
Solve the error with this command，删除所引进的jar包中的相关文件:</p>



<pre class="prettyprint"><code class=" hljs applescript">zip -d &lt;jar <span class="hljs-type">file</span> <span class="hljs-property">name</span>&gt;.jar META-INF/*.RSA META-INF/*.DSA META-INF/*.SF</code></pre>

<hr>

<p>Spark的算子：</p>

<p>Function name Purpose Example Result</p>



<pre class="prettyprint"><code class=" hljs css">输入数据：
<span class="hljs-rules">{<span class="hljs-rule">(1, 2), (3, 4), (3, 6)}</span></span></code></pre>

<p>函数1： <br>
reduceByKey(func)</p>

<p>描述： <br>
 Combine values with <br>
the same key. </p>

<p>例子： <br>
rdd.reduceByKey( <br>
(x, y) =&gt; x + y)</p>

<p>结果： <br>
 {(1,2), (3,10)}</p>

<hr>

<p>函数2： <br>
groupByKey()</p>

<p>描述： <br>
 Group values with the same key. </p>

<p>例子： <br>
rdd.groupByKey()</p>

<p>结果： <br>
{(1,[2]),(3, [4,6])}</p>

<hr>

<p>函数3： <br>
combineByKey ( createCombiner,mergeValue,mergeCombiners,partitioner ) </p>

<p>描述： <br>
Combine values with the same key using a different result type. Apply a function to each value of a pair RDD without <br>
changing the key. rdd.mapValues(x =&gt; x+1) flatMapValues(func) Apply a function that <br>
returns an iterator to <br>
each value of a pair <br>
RDD, and for each <br>
element returned, <br>
produce a key/value</p>

<p>结果： <br>
{(1,3), (3,5), (3,7)}</p>

<p><a href="https://spark.apache.org/docs/2.0.2/mllib-statistics.html" rel="nofollow">mllib-statistics</a></p>

<p><a href="http://www.changhai.org/articles/technology/misc/google_math.php" rel="nofollow">google-math</a></p>

<p><a href="http://spark.apache.org/docs/latest/programming-guide.html" rel="nofollow">programming-guide</a></p>

<p><a href="http://spark.apache.org/docs/latest/tuning.html" rel="nofollow">tuning-spark</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>