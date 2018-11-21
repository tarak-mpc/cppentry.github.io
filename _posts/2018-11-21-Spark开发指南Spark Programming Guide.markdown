---
layout:     post
title:      Spark开发指南Spark Programming Guide
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 id="spark-programming-guide" class="title">Spark Programming Guide</h1>
    Overview<br>
    Linking with Spark<br>
    Initializing Spark<br>
        Using the Shell<br>
    Resilient Distributed Datasets (RDDs)<br>
        Parallelized Collections<br>
        External Datasets<br>
        RDD Operations<br>
            Basics<br>
            Passing Functions to Spark<br>
            Understanding closures<br>
                Example<br>
                Local vs. cluster modes<br>
                Printing elements of an RDD<br>
            Working with Key-Value Pairs<br>
            Transformations<br>
            Actions<br>
            Shuffle operations<br>
                Background<br>
                Performance Impact<br>
        RDD Persistence<br>
            Which Storage Level to Choose?<br>
            Removing Data<br>
    Shared Variables<br>
        Broadcast Variables<br>
        Accumulators <br>
    Deploying to a Cluster<br>
    Launching Spark jobs from Java / Scala<br>
    Unit Testing<br>
    Migrating from pre-1.0 Versions of Spark<br>
    Where to Go from Here
<h1 id="overview">Overview</h1>
<p>At a high level, every Spark application consists of a <em>driver program</em> that runs the user’s
<code>main</code> function and executes various <em>parallel operations</em> on a cluster. The main abstraction Spark provides is a
<em>resilient distributed dataset</em> (RDD), which is a collection of elements partitioned across the nodes of the cluster that can be operated on in parallel. RDDs are created by starting with a file in the Hadoop file system (or any other Hadoop-supported
 file system), or an existing Scala collection in the driver program, and transforming it. Users may also ask Spark to
<em>persist</em> an RDD in memory, allowing it to be reused efficiently across parallel operations. Finally, RDDs automatically recover from node failures.</p>
<p>A second abstraction in Spark is <em>shared variables</em> that can be used in parallel operations. By default, when Spark runs a function in parallel as a set of tasks on different nodes, it ships a copy of each variable used in the function to each task.
 Sometimes, a variable needs to be shared across tasks, or between tasks and the driver program. Spark supports two types of shared variables:
<em>broadcast variables</em>, which can be used to cache a value in memory on all nodes, and
<em>accumulators</em>, which are variables that are only “added” to, such as counters and sums.</p>
<p>This guide shows each of these features in each of Spark’s supported languages. It is easiest to followalong with if you launch Spark’s interactive shell – either
<code>bin/spark-shell</code> for the Scala shell or<code>bin/pyspark</code> for the Python one.</p>
<h1 id="linking-with-spark">Linking with Spark</h1>
<ul class="nav nav-tabs"><li class="active"><a class="tab_scala" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_scala_0" rel="nofollow"><strong>Scala</strong></a></li><li><a class="tab_java" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_java_0" rel="nofollow"><strong>Java</strong></a></li><li><a class="tab_python" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_python_0" rel="nofollow"><strong>Python</strong></a></li></ul><div class="codetabs tab-content">
<div id="tab_scala_0" class="tab-pane active">
<p>Spark 1.5.2 uses Scala 2.10. To writeapplications in Scala, you will need to use a compatible Scala version (e.g. 2.10.X).</p>
<p>To write a Spark application, you need to add a Maven dependency on Spark. Spark is available through Maven Central at:</p>
<pre><code>groupId = org.apache.spark
artifactId = spark-core_2.10
version = 1.5.2
</code></pre>
<p>In addition, if you wish to access an HDFS cluster, you need to add a dependency on<code>hadoop-client</code> for your version of HDFS. Some common HDFS version tags are listed on the<a href="http://spark.apache.org/docs/latest/hadoop-third-party-distributions.html" rel="nofollow">third
 party distributions</a> page.</p>
<pre><code>groupId = org.apache.hadoop
artifactId = hadoop-client
version = &lt;your-hdfs-version&gt;
</code></pre>
<p>Finally, you need to import some Spark classes into your program. Add the following lines:</p>
<div class="highlight">
<pre><code class="language-scala"><span class="k">import</span> <span class="nn">org.apache.spark.SparkContext</span>
<span class="k">import</span> <span class="nn">org.apache.spark.SparkConf</span></code></pre>
</div>
<p>(Before Spark 1.3.0, you need to explicitly <code>import org.apache.spark.SparkContext._</code> to enable essential implicit conversions.)</p>
</div>
</div>
<h1 id="initializing-spark">Initializing Spark</h1>
<ul class="nav nav-tabs"><li class="active"><a class="tab_scala" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_scala_1" rel="nofollow"><strong>Scala</strong></a></li><li><a class="tab_java" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_java_1" rel="nofollow"><strong>Java</strong></a></li><li><a class="tab_python" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_python_1" rel="nofollow"><strong>Python</strong></a></li></ul><div class="codetabs tab-content">
<div id="tab_scala_1" class="tab-pane active">
<p>The first thing a Spark program must do is to create a <a href="http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.SparkContext" rel="nofollow">
SparkContext</a> object, which tells Sparkhow to access a cluster. To create a <code>
SparkContext</code> you first need to build a <a href="http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.SparkConf" rel="nofollow">
SparkConf</a> objectthat contains information about your application.</p>
<p>Only one SparkContext may be active per JVM. You must <code>stop()</code> the active SparkContext before creating a new one.</p>
<div class="highlight">
<pre><code class="language-scala"><span class="k">val</span> <span class="n">conf</span> <span class="k">=</span> <span class="k">new</span> <span class="nc">SparkConf</span><span class="o">().</span><span class="n">setAppName</span><span class="o">(</span><span class="n">appName</span><span class="o">).</span><span class="n">setMaster</span><span class="o">(</span><span class="n">master</span><span class="o">)</span>
<span class="k">new</span> <span class="nc">SparkContext</span><span class="o">(</span><span class="n">conf</span><span class="o">)</span></code></pre>
</div>
</div>
</div>
<p>The <code>appName</code> parameter is a name for your application to show on the cluster UI.<code>master</code> is a
<a href="http://spark.apache.org/docs/latest/submitting-applications.html#master-urls" rel="nofollow">
Spark, Mesos or YARN cluster URL</a>,or a special “local” string to run in local mode.In practice, when running on a cluster, you will not want to hardcode
<code>master</code> in the program,but rather <a href="http://spark.apache.org/docs/latest/submitting-applications.html" rel="nofollow">
launch the application with <code>spark-submit</code></a> andreceive it there. However, for local testing and unit tests, you can pass “local” to run Sparkin-process.</p>
<h2 id="using-the-shell">Using the Shell</h2>
<ul class="nav nav-tabs"><li class="active"><a class="tab_scala" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_scala_2" rel="nofollow"><strong>Scala</strong></a></li><li><a class="tab_python" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_python_2" rel="nofollow"><strong>Python</strong></a></li></ul><div class="codetabs tab-content">
<div id="tab_scala_2" class="tab-pane active">
<p>In the Spark shell, a special interpreter-aware SparkContext is already created for you, in thevariable called
<code>sc</code>. Making your own SparkContext will not work. You can set which master thecontext connects to using the
<code>--master</code> argument, and you can add JARs to the classpathby passing a comma-separated list to the
<code>--jars</code> argument. You can also add dependencies (e.g. Spark Packages) to your shell session by supplying a comma-separated list of maven coordinates to the
<code>--packages</code> argument. Any additional repositories where dependencies might exist (e.g. SonaType)can be passed to the
<code>--repositories</code> argument. For example, to run <code>bin/spark-shell</code> on exactlyfour cores, use:</p>
<div class="highlight">
<pre><code class="language-bash"><span class="nv">$ </span>./bin/spark-shell --master <span class="nb">local</span><span class="o">[</span>4<span class="o">]</span></code></pre>
</div>
<p>Or, to also add <code>code.jar</code> to its classpath, use:</p>
<div class="highlight">
<pre><code class="language-bash"><span class="nv">$ </span>./bin/spark-shell --master <span class="nb">local</span><span class="o">[</span>4<span class="o">]</span> --jars code.jar</code></pre>
</div>
<p>To include a dependency using maven coordinates:</p>
<div class="highlight">
<pre><code class="language-bash"><span class="nv">$ </span>./bin/spark-shell --master <span class="nb">local</span><span class="o">[</span>4<span class="o">]</span> --packages <span class="s2">"org.example:example:0.1"</span></code></pre>
</div>
<p>For a complete list of options, run <code>spark-shell --help</code>. Behind the scenes,<code>spark-shell</code> invokes the more general
<a href="http://spark.apache.org/docs/latest/submitting-applications.html" rel="nofollow">
<code>spark-submit</code> script</a>.</p>
</div>
</div>
<h1 id="resilient-distributed-datasets-rdds">Resilient Distributed Datasets (RDDs)</h1>
<p>Spark revolves around the concept of a <em>resilient distributed dataset</em> (RDD), which is a fault-tolerant collection of elements that can be operated on in parallel. There are two ways to create RDDs:
<em>parallelizing</em>an existing collection in your driver program, or referencing a dataset in an external storage system, such as ashared filesystem, HDFS, HBase, or any data source offering a Hadoop InputFormat.</p>
<h2 id="parallelized-collections">Parallelized Collections</h2>
<ul class="nav nav-tabs"><li class="active"><a class="tab_scala" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_scala_3" rel="nofollow"><strong>Scala</strong></a></li><li><a class="tab_java" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_java_3" rel="nofollow"><strong>Java</strong></a></li><li><a class="tab_python" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_python_3" rel="nofollow"><strong>Python</strong></a></li></ul><div class="codetabs tab-content">
<div id="tab_scala_3" class="tab-pane active">
<p>Parallelized collections are created by calling <code>SparkContext</code>’s <code>
parallelize</code> method on an existing collection in your driver program (a Scala
<code>Seq</code>). The elements of the collection are copied to form a distributed dataset that can be operated on in parallel. For example, here is how to create a parallelized collection holding the numbers 1 to 5:</p>
<div class="highlight">
<pre><code class="language-scala"><span class="k">val</span> <span class="n">data</span> <span class="k">=</span> <span class="nc">Array</span><span class="o">(</span><span class="mi">1</span><span class="o">,</span> <span class="mi">2</span><span class="o">,</span> <span class="mi">3</span><span class="o">,</span> <span class="mi">4</span><span class="o">,</span> <span class="mi">5</span><span class="o">)</span>
<span class="k">val</span> <span class="n">distData</span> <span class="k">=</span> <span class="n">sc</span><span class="o">.</span><span class="n">parallelize</span><span class="o">(</span><span class="n">data</span><span class="o">)</span></code></pre>
</div>
<p>Once created, the distributed dataset (<code>distData</code>) can be operated on in parallel. For example, we might call
<code>distData.reduce((a, b) =&gt; a + b)</code> to add up the elements of the array. We describe operations on distributed datasets later on.</p>
</div>
</div>
<p>One important parameter for parallel collections is the number of <em>partitions</em> to cut the dataset into. Spark will run one task for each partition of the cluster. Typically you want 2-4 partitions for each CPU in your cluster. Normally, Spark tries
 to set the number of partitions automatically based on your cluster. However, you can also set it manually by passing it as a second parameter to
<code>parallelize</code> (e.g. <code>sc.parallelize(data, 10)</code>). Note: some places in the code use the term slices (a synonym for partitions) to maintain backward compatibility.</p>
<h2 id="external-datasets">External Datasets</h2>
<ul class="nav nav-tabs"><li class="active"><a class="tab_scala" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_scala_4" rel="nofollow"><strong>Scala</strong></a></li><li><a class="tab_java" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_java_4" rel="nofollow"><strong>Java</strong></a></li><li><a class="tab_python" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_python_4" rel="nofollow"><strong>Python</strong></a></li></ul><div class="codetabs tab-content">
<div id="tab_scala_4" class="tab-pane active">
<p>Spark can create distributed datasets from any storage source supported by Hadoop, including your local file system, HDFS, Cassandra, HBase,
<a href="http://wiki.apache.org/hadoop/AmazonS3" rel="nofollow">Amazon S3</a>, etc. Spark supports text files,
<a href="http://hadoop.apache.org/common/docs/current/api/org/apache/hadoop/mapred/SequenceFileInputFormat.html" rel="nofollow">
SequenceFiles</a>, and any other Hadoop <a href="http://hadoop.apache.org/docs/stable/api/org/apache/hadoop/mapred/InputFormat.html" rel="nofollow">
InputFormat</a>.</p>
<p>Text file RDDs can be created using <code>SparkContext</code>’s <code>textFile</code> method. This method takes an URI for the file (either a local path on the machine, or a
<code>hdfs://</code>, <code>s3n://</code>, etc URI) and reads it as a collection of lines. Here is an example invocation:</p>
<div class="highlight">
<pre><code class="language-scala"><span class="n">scala</span><span class="o">&gt;</span> <span class="k">val</span> <span class="n">distFile</span> <span class="k">=</span> <span class="n">sc</span><span class="o">.</span><span class="n">textFile</span><span class="o">(</span><span class="s">"data.txt"</span><span class="o">)</span>
<span class="n">distFile</span><span class="k">:</span> <span class="kt">RDD</span><span class="o">[</span><span class="kt">String</span><span class="o">]</span> <span class="k">=</span> <span class="nc">MappedRDD</span><span class="k">@</span><span class="mi">1</span><span class="n">d4cee08</span></code></pre>
</div>
<p>Once created, <code>distFile</code> can be acted on by dataset operations. For example, we can add up the sizes of all the lines using the
<code>map</code> and <code>reduce</code> operations as follows: <code>distFile.map(s =&gt; s.length).reduce((a, b) =&gt; a + b)</code>.</p>
<p>Some notes on reading files with Spark:</p>
<ul><li>
<p>If using a path on the local filesystem, the file must also be accessible at the same path on worker nodes. Either copy the file to all workers or use a network-mounted shared file system.</p>
</li><li>
<p>All of Spark’s file-based input methods, including <code>textFile</code>, support running on directories, compressed files, and wildcards as well. For example, you can use
<code>textFile("/my/directory")</code>, <code>textFile("/my/directory/*.txt")</code>, and
<code>textFile("/my/directory/*.gz")</code>.</p>
</li><li>
<p>The <code>textFile</code> method also takes an optional second argument for controlling the number of partitions of the file. By default, Spark creates one partition for each block of the file (blocks being 64MB by default in HDFS), but you can also ask
 for a higher number of partitions by passing a larger value. Note that you cannot have fewer partitions than blocks.</p>
</li></ul><p>Apart from text files, Spark’s Scala API also supports several other data formats:</p>
<ul><li>
<p><code>SparkContext.wholeTextFiles</code> lets you read a directory containing multiple small text files, and returns each of them as (filename, content) pairs. This is in contrast with
<code>textFile</code>, which would return one record per line in each file.</p>
</li><li>
<p>For <a href="http://hadoop.apache.org/common/docs/current/api/org/apache/hadoop/mapred/SequenceFileInputFormat.html" rel="nofollow">
SequenceFiles</a>, use SparkContext’s <code>sequenceFile[K, V]</code> method where
<code>K</code> and <code>V</code> are the types of key and values in the file. These should be subclasses of Hadoop’s
<a href="http://hadoop.apache.org/common/docs/current/api/org/apache/hadoop/io/Writable.html" rel="nofollow">
Writable</a> interface, like <a href="http://hadoop.apache.org/common/docs/current/api/org/apache/hadoop/io/IntWritable.html" rel="nofollow">
IntWritable</a> and <a href="http://hadoop.apache.org/common/docs/current/api/org/apache/hadoop/io/Text.html" rel="nofollow">
Text</a>. In addition, Spark allows you to specify native types for a few common Writables; for example,
<code>sequenceFile[Int, String]</code> will automatically read IntWritables and Texts.</p>
</li><li>
<p>For other Hadoop InputFormats, you can use the <code>SparkContext.hadoopRDD</code> method, which takes an arbitrary
<code>JobConf</code> and input format class, key class and value class. Set these the same way you would for a Hadoop job with your input source. You can also use
<code>SparkContext.newAPIHadoopRDD</code> for InputFormats based on the “new” MapReduce API (<code>org.apache.hadoop.mapreduce</code>).</p>
</li><li>
<p><code>RDD.saveAsObjectFile</code> and <code>SparkContext.objectFile</code> support saving an RDD in a simple format consisting of serialized Java objects. While this is not as efficient as specialized formats like Avro, it offers an easy way to save any
 RDD.</p>
</li></ul></div>
</div>
<h2 id="rdd-operations">RDD Operations</h2>
<p>RDDs support two types of operations: <em>transformations</em>, which create a new dataset from an existing one, and
<em>actions</em>, which return a value to the driver program after running a computation on the dataset. For example,
<code>map</code> is a transformation that passes each dataset element through a function and returns a new RDD representing the results. On the other hand,
<code>reduce</code> is an action that aggregates all the elements of the RDD using some function and returns the final result to the driver program (although there is also a parallel
<code>reduceByKey</code> that returns a distributed dataset).</p>
<p>All transformations in Spark are <em>lazy</em>, in that they do not compute their results right away. Instead, they just remember the transformations applied to some base dataset (e.g. a file). The transformations are only computed when an action requires
 a result to be returned to the driver program. This design enables Spark to run more efficiently – for example, we can realize that a dataset created through
<code>map</code> will be used in a <code>reduce</code> and return only the result of the
<code>reduce</code> to the driver, rather than the larger mapped dataset.</p>
<p>By default, each transformed RDD may be recomputed each time you run an action on it. However, you may also
<em>persist</em> an RDD in memory using the <code>persist</code> (or <code>cache</code>) method, in which case Spark will keep the elements around on the cluster for much faster access the next time you query it. There is also support for persisting RDDs on
 disk, or replicated across multiple nodes.</p>
<h3 id="basics">Basics</h3>
<ul class="nav nav-tabs"><li class="active"><a class="tab_scala" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_scala_5" rel="nofollow"><strong>Scala</strong></a></li><li><a class="tab_java" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_java_5" rel="nofollow"><strong>Java</strong></a></li><li><a class="tab_python" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_python_5" rel="nofollow"><strong>Python</strong></a></li></ul><div class="codetabs tab-content">
<div id="tab_scala_5" class="tab-pane active">
<p>To illustrate RDD basics, consider the simple program below:</p>
<div class="highlight">
<pre><code class="language-scala"><span class="k">val</span> <span class="n">lines</span> <span class="k">=</span> <span class="n">sc</span><span class="o">.</span><span class="n">textFile</span><span class="o">(</span><span class="s">"data.txt"</span><span class="o">)</span>
<span class="k">val</span> <span class="n">lineLengths</span> <span class="k">=</span> <span class="n">lines</span><span class="o">.</span><span class="n">map</span><span class="o">(</span><span class="n">s</span> <span class="k">=&gt;</span> <span class="n">s</span><span class="o">.</span><span class="n">length</span><span class="o">)</span>
<span class="k">val</span> <span class="n">totalLength</span> <span class="k">=</span> <span class="n">lineLengths</span><span class="o">.</span><span class="n">reduce</span><span class="o">((</span><span class="n">a</span><span class="o">,</span> <span class="n">b</span><span class="o">)</span> <span class="k">=&gt;</span> <span class="n">a</span> <span class="o">+</span> <span class="n">b</span><span class="o">)</span></code></pre>
</div>
<p>The first line defines a base RDD from an external file. This dataset is not loaded in memory orotherwise acted on:
<code>lines</code> is merely a pointer to the file.The second line defines <code>
lineLengths</code> as the result of a <code>map</code> transformation. Again, <code>
lineLengths</code>is <em>not</em> immediately computed, due to laziness.Finally, we run
<code>reduce</code>, which is an action. At this point Spark breaks the computation into tasksto run on separate machines, and each machine runs both its part of the map and a local reduction,returning only its answer to the driver program.</p>
<p>If we also wanted to use <code>lineLengths</code> again later, we could add:</p>
<div class="highlight">
<pre><code class="language-scala"><span class="n">lineLengths</span><span class="o">.</span><span class="n">persist</span><span class="o">()</span></code></pre>
</div>
<p>before the <code>reduce</code>, which would cause <code>lineLengths</code> to be saved in memory after the first time it is computed.</p>
</div>
</div>
<h3 id="passing-functions-to-spark">Passing Functions to Spark</h3>
<ul class="nav nav-tabs"><li class="active"><a class="tab_scala" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_scala_6" rel="nofollow"><strong>Scala</strong></a></li><li><a class="tab_java" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_java_6" rel="nofollow"><strong>Java</strong></a></li><li><a class="tab_python" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_python_6" rel="nofollow"><strong>Python</strong></a></li></ul><div class="codetabs tab-content">
<div id="tab_scala_6" class="tab-pane active">
<p>Spark’s API relies heavily on passing functions in the driver program to run on the cluster.There are two recommended ways to do this:</p>
<ul><li><a href="http://docs.scala-lang.org/tutorials/tour/anonymous-function-syntax.html" rel="nofollow">Anonymous function syntax</a>,which can be used for short pieces of code.</li><li>Static methods in a global singleton object. For example, you can define <code>
object MyFunctions</code> and thenpass <code>MyFunctions.func1</code>, as follows:</li></ul><div class="highlight">
<pre><code class="language-scala"><span class="k">object</span> <span class="nc">MyFunctions</span> <span class="o">{</span>
  <span class="k">def</span> <span class="n">func1</span><span class="o">(</span><span class="n">s</span><span class="k">:</span> <span class="kt">String</span><span class="o">)</span><span class="k">:</span> <span class="kt">String</span> <span class="o">=</span> <span class="o">{</span> <span class="o">...</span> <span class="o">}</span>
<span class="o">}</span>

<span class="n">myRdd</span><span class="o">.</span><span class="n">map</span><span class="o">(</span><span class="nc">MyFunctions</span><span class="o">.</span><span class="n">func1</span><span class="o">)</span></code></pre>
</div>
<p>Note that while it is also possible to pass a reference to a method in a class instance (as opposed toa singleton object), this requires sending the object that contains that class along with the method.For example, consider:</p>
<div class="highlight">
<pre><code class="language-scala"><span class="k">class</span> <span class="nc">MyClass</span> <span class="o">{</span>
  <span class="k">def</span> <span class="n">func1</span><span class="o">(</span><span class="n">s</span><span class="k">:</span> <span class="kt">String</span><span class="o">)</span><span class="k">:</span> <span class="kt">String</span> <span class="o">=</span> <span class="o">{</span> <span class="o">...</span> <span class="o">}</span>
  <span class="k">def</span> <span class="n">doStuff</span><span class="o">(</span><span class="n">rdd</span><span class="k">:</span> <span class="kt">RDD</span><span class="o">[</span><span class="kt">String</span><span class="o">])</span><span class="k">:</span> <span class="kt">RDD</span><span class="o">[</span><span class="kt">String</span><span class="o">]</span> <span class="k">=</span> <span class="o">{</span> <span class="n">rdd</span><span class="o">.</span><span class="n">map</span><span class="o">(</span><span class="n">func1</span><span class="o">)</span> <span class="o">}</span>
<span class="o">}</span></code></pre>
</div>
<p>Here, if we create a <code>new MyClass</code> and call <code>doStuff</code> on it, the
<code>map</code> inside there references the<code>func1</code> method <em>of that
<code>MyClass</code> instance</em>, so the whole object needs to be sent to the cluster. It issimilar to writing
<code>rdd.map(x =&gt; this.func1(x))</code>.</p>
<p>In a similar way, accessing fields of the outer object will reference the whole object:</p>
<div class="highlight">
<pre><code class="language-scala"><span class="k">class</span> <span class="nc">MyClass</span> <span class="o">{</span>
  <span class="k">val</span> <span class="n">field</span> <span class="k">=</span> <span class="s">"Hello"</span>
  <span class="k">def</span> <span class="n">doStuff</span><span class="o">(</span><span class="n">rdd</span><span class="k">:</span> <span class="kt">RDD</span><span class="o">[</span><span class="kt">String</span><span class="o">])</span><span class="k">:</span> <span class="kt">RDD</span><span class="o">[</span><span class="kt">String</span><span class="o">]</span> <span class="k">=</span> <span class="o">{</span> <span class="n">rdd</span><span class="o">.</span><span class="n">map</span><span class="o">(</span><span class="n">x</span> <span class="k">=&gt;</span> <span class="n">field</span> <span class="o">+</span> <span class="n">x</span><span class="o">)</span> <span class="o">}</span>
<span class="o">}</span></code></pre>
</div>
<p>is equilvalent to writing <code>rdd.map(x =&gt; this.field + x)</code>, which references all of
<code>this</code>. To avoid thisissue, the simplest way is to copy <code>field</code> into a local variable instead of accessing it externally:</p>
<div class="highlight">
<pre><code class="language-scala"><span class="k">def</span> <span class="n">doStuff</span><span class="o">(</span><span class="n">rdd</span><span class="k">:</span> <span class="kt">RDD</span><span class="o">[</span><span class="kt">String</span><span class="o">])</span><span class="k">:</span> <span class="kt">RDD</span><span class="o">[</span><span class="kt">String</span><span class="o">]</span> <span class="k">=</span> <span class="o">{</span>
  <span class="k">val</span> <span class="n">field_</span> <span class="k">=</span> <span class="k">this</span><span class="o">.</span><span class="n">field</span>
  <span class="n">rdd</span><span class="o">.</span><span class="n">map</span><span class="o">(</span><span class="n">x</span> <span class="k">=&gt;</span> <span class="n">field_</span> <span class="o">+</span> <span class="n">x</span><span class="o">)</span>
<span class="o">}</span></code></pre>
</div>
</div>
</div>
<h3 id="understanding-closures-a-nameclosureslinka">Understanding closures <a name="ClosuresLink">
</a></h3>
<p>One of the harder things about Spark is understanding the scope and life cycle of variables and methods when executing code across a cluster. RDD operations that modify variables outside of their scope can be a frequent source of confusion. In the example
 below we’ll look at code that uses <code>foreach()</code> to increment a counter, but similar issues can occur for other operations as well.</p>
<h4 id="example">Example</h4>
<p>Consider the naive RDD element sum below, which behaves completely differently depending on whether execution is happening within the same JVM. A common example of this is when running Spark in
<code>local</code> mode (<code>--master = local[n]</code>) versus deploying a Spark application to a cluster (e.g. via spark-submit to YARN):</p>
<ul class="nav nav-tabs"><li class="active"><a class="tab_scala" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_scala_7" rel="nofollow"><strong>Scala</strong></a></li><li><a class="tab_java" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_java_7" rel="nofollow"><strong>Java</strong></a></li><li><a class="tab_python" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_python_7" rel="nofollow"><strong>Python</strong></a></li></ul><div class="codetabs tab-content">
<div id="tab_scala_7" class="tab-pane active">
<div class="highlight">
<pre><code class="language-scala"><span class="k">var</span> <span class="n">counter</span> <span class="k">=</span> <span class="mi">0</span>
<span class="k">var</span> <span class="n">rdd</span> <span class="k">=</span> <span class="n">sc</span><span class="o">.</span><span class="n">parallelize</span><span class="o">(</span><span class="n">data</span><span class="o">)</span>

<span class="c1">// Wrong: Don't do this!!</span>
<span class="n">rdd</span><span class="o">.</span><span class="n">foreach</span><span class="o">(</span><span class="n">x</span> <span class="k">=&gt;</span> <span class="n">counter</span> <span class="o">+=</span> <span class="n">x</span><span class="o">)</span>

<span class="n">println</span><span class="o">(</span><span class="s">"Counter value: "</span> <span class="o">+</span> <span class="n">counter</span><span class="o">)</span></code></pre>
</div>
</div>
</div>
<h4 id="local-vs-cluster-modes">Local vs. cluster modes</h4>
<p>The primary challenge is that the behavior of the above code is undefined. In local mode with a single JVM, the above code will sum the values within the RDD and store it in
<strong>counter</strong>. This is because both the RDD and the variable <strong>counter</strong> are in the same memory space on the driver node.</p>
<p>However, in <code>cluster</code> mode, what happens is more complicated, and the above may not work as intended. To execute jobs, Spark breaks up the processing of RDD operations into tasks - each of which is operated on by an executor. Prior to execution,
 Spark computes the <strong>closure</strong>. The closure is those variables and methods which must be visible for the executor to perform its computations on the RDD (in this case
<code>foreach()</code>). This closure is serialized and sent to each executor. In
<code>local</code> mode, there is only the one executors so everything shares the same closure. In other modes however, this is not the case and the executors running on seperate worker nodes each have their own copy of the closure.</p>
<p>What is happening here is that the variables within the closure sent to each executor are now copies and thus, when
<strong>counter</strong> is referenced within the <code>foreach</code> function, it’s no longer the
<strong>counter</strong> on the driver node. There is still a <strong>counter</strong> in the memory of the driver node but this is no longer visible to the executors! The executors only sees the copy from the serialized closure. Thus, the final value of
<strong>counter</strong> will still be zero since all operations on <strong>counter</strong> were referencing the value within the serialized closure.</p>
<p>To ensure well-defined behavior in these sorts of scenarios one should use an <a href="http://spark.apache.org/docs/latest/programming-guide.html#AccumLink" rel="nofollow">
<code>Accumulator</code></a>. Accumulators in Spark are used specifically to provide a mechanism for safely updating a variable when execution is split up across worker nodes in a cluster. The Accumulators section of this guide discusses these in more detail.</p>
<p>In general, closures - constructs like loops or locally defined methods, should not be used to mutate some global state. Spark does not define or guarantee the behavior of mutations to objects referenced from outside of closures. Some code that does this
 may work in local mode, but that’s just by accident and such code will not behave as expected in distributed mode. Use an Accumulator instead if some global aggregation is needed.</p>
<h4 id="printing-elements-of-an-rdd">Printing elements of an RDD</h4>
<p>Another common idiom is attempting to print out the elements of an RDD using <code>
rdd.foreach(println)</code> or <code>rdd.map(println)</code>. On a single machine, this will generate the expected output and print all the RDD’s elements. However, in
<code>cluster</code> mode, the output to <code>stdout</code> being called by the executors is now writing to the executor’s
<code>stdout</code> instead, not the one on the driver, so <code>stdout</code> on the driver won’t show these! To print all elements on the driver, one can use the
<code>collect()</code> method to first bring the RDD to the driver node thus: <code>
rdd.collect().foreach(println)</code>. This can cause the driver to run out of memory, though, because
<code>collect()</code> fetches the entire RDD to a single machine; if you only need to print a few elements of the RDD, a safer approach is to use the
<code>take()</code>: <code>rdd.take(100).foreach(println)</code>.</p>
<h3 id="working-with-key-value-pairs">Working with Key-Value Pairs</h3>
<ul class="nav nav-tabs"><li class="active"><a class="tab_scala" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_scala_8" rel="nofollow"><strong>Scala</strong></a></li><li><a class="tab_java" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_java_8" rel="nofollow"><strong>Java</strong></a></li><li><a class="tab_python" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_python_8" rel="nofollow"><strong>Python</strong></a></li></ul><div class="codetabs tab-content">
<div id="tab_scala_8" class="tab-pane active">
<p>While most Spark operations work on RDDs containing any type of objects, a few special operations areonly available on RDDs of key-value pairs.The most common ones are distributed “shuffle” operations, such as grouping or aggregating the elementsby a key.</p>
<p>In Scala, these operations are automatically available on RDDs containing<a href="http://www.scala-lang.org/api/2.10.4/index.html#scala.Tuple2" rel="nofollow">Tuple2</a> objects(the built-in tuples in the language, created by simply writing
<code>(a, b)</code>). The key-value pair operations are available in the<a href="http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.rdd.PairRDDFunctions" rel="nofollow">PairRDDFunctions</a> class,which automatically wraps around an RDD
 of tuples.</p>
<p>For example, the following code uses the <code>reduceByKey</code> operation on key-value pairs to count howmany times each line of text occurs in a file:</p>
<div class="highlight">
<pre><code class="language-scala"><span class="k">val</span> <span class="n">lines</span> <span class="k">=</span> <span class="n">sc</span><span class="o">.</span><span class="n">textFile</span><span class="o">(</span><span class="s">"data.txt"</span><span class="o">)</span>
<span class="k">val</span> <span class="n">pairs</span> <span class="k">=</span> <span class="n">lines</span><span class="o">.</span><span class="n">map</span><span class="o">(</span><span class="n">s</span> <span class="k">=&gt;</span> <span class="o">(</span><span class="n">s</span><span class="o">,</span> <span class="mi">1</span><span class="o">))</span>
<span class="k">val</span> <span class="n">counts</span> <span class="k">=</span> <span class="n">pairs</span><span class="o">.</span><span class="n">reduceByKey</span><span class="o">((</span><span class="n">a</span><span class="o">,</span> <span class="n">b</span><span class="o">)</span> <span class="k">=&gt;</span> <span class="n">a</span> <span class="o">+</span> <span class="n">b</span><span class="o">)</span></code></pre>
</div>
<p>We could also use <code>counts.sortByKey()</code>, for example, to sort the pairs alphabetically, and finally<code>counts.collect()</code> to bring them back to the driver program as an array of objects.</p>
<p><strong>Note:</strong> when using custom objects as the key in key-value pair operations, you must be sure that acustom
<code>equals()</code> method is accompanied with a matching <code>hashCode()</code> method. For full details, seethe contract outlined in the
<a href="http://docs.oracle.com/javase/7/docs/api/java/lang/Object.html#hashCode%28%29" rel="nofollow">
Object.hashCode()documentation</a>.</p>
</div>
</div>
<h3 id="transformations">Transformations</h3>
<p>The following table lists some of the common transformations supported by Spark. Refer to theRDD API doc(<a href="http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.rdd.RDD" rel="nofollow">Scala</a>,
<a href="http://spark.apache.org/docs/latest/api/java/index.html?org/apache/spark/api/java/JavaRDD.html" rel="nofollow">
Java</a>, <a href="http://spark.apache.org/docs/latest/api/python/pyspark.html#pyspark.RDD" rel="nofollow">
Python</a>, <a href="http://spark.apache.org/docs/latest/api/R/index.html" rel="nofollow">
R</a>)and pair RDD functions doc(<a href="http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.rdd.PairRDDFunctions" rel="nofollow">Scala</a>,
<a href="http://spark.apache.org/docs/latest/api/java/index.html?org/apache/spark/api/java/JavaPairRDD.html" rel="nofollow">
Java</a>)for details.</p>
<table class="table"><tbody><tr><th>Transformation</th>
<th>Meaning</th>
</tr><tr><td><strong>map</strong>(<em>func</em>)</td>
<td>Return a new distributed dataset formed by passing each element of the source through a function
<em>func</em>.</td>
</tr><tr><td><strong>filter</strong>(<em>func</em>)</td>
<td>Return a new dataset formed by selecting those elements of the source on which
<em>func</em> returns true.</td>
</tr><tr><td><strong>flatMap</strong>(<em>func</em>)</td>
<td>Similar to map, but each input item can be mapped to 0 or more output items (so
<em>func</em> should return a Seq rather than a single item).</td>
</tr><tr><td><strong>mapPartitions</strong>(<em>func</em>) <a name="MapPartLink">
</a></td>
<td>Similar to map, but runs separately on each partition (block) of the RDD, so <em>
func</em> must be of type Iterator&lt;T&gt; =&gt; Iterator&lt;U&gt; when running on an RDD of type T.</td>
</tr><tr><td><strong>mapPartitionsWithIndex</strong>(<em>func</em>)</td>
<td>Similar to mapPartitions, but also provides <em>func</em> with an integer value representing the index of the partition, so
<em>func</em> must be of type (Int, Iterator&lt;T&gt;) =&gt; Iterator&lt;U&gt; when running on an RDD of type T.</td>
</tr><tr><td><strong>sample</strong>(<em>withReplacement</em>, <em>fraction</em>, <em>seed</em>)</td>
<td>Sample a fraction <em>fraction</em> of the data, with or without replacement, using a given random number generator seed.</td>
</tr><tr><td><strong>union</strong>(<em>otherDataset</em>)</td>
<td>Return a new dataset that contains the union of the elements in the source dataset and the argument.</td>
</tr><tr><td><strong>intersection</strong>(<em>otherDataset</em>)</td>
<td>Return a new RDD that contains the intersection of elements in the source dataset and the argument.</td>
</tr><tr><td><strong>distinct</strong>([<em>numTasks</em>]))</td>
<td>Return a new dataset that contains the distinct elements of the source dataset.</td>
</tr><tr><td><strong>groupByKey</strong>([<em>numTasks</em>]) <a name="GroupByLink">
</a></td>
<td>When called on a dataset of (K, V) pairs, returns a dataset of (K, Iterable&lt;V&gt;) pairs.
<br><strong>Note:</strong> If you are grouping in order to perform an aggregation (such as a sum or average) over each key, using
<code>reduceByKey</code> or <code>aggregateByKey</code> will yield much better performance.
<br><strong>Note:</strong> By default, the level of parallelism in the output depends on the number of partitions of the parent RDD. You can pass an optional
<code>numTasks</code> argument to set a different number of tasks.</td>
</tr><tr><td><strong>reduceByKey</strong>(<em>func</em>, [<em>numTasks</em>]) <a name="ReduceByLink">
</a></td>
<td>When called on a dataset of (K, V) pairs, returns a dataset of (K, V) pairs where the values for each key are aggregated using the given reduce function
<em>func</em>, which must be of type (V,V) =&gt; V. Like in <code>groupByKey</code>, the number of reduce tasks is configurable through an optional second argument.</td>
</tr><tr><td><strong>aggregateByKey</strong>(<em>zeroValue</em>)(<em>seqOp</em>, <em>combOp</em>, [<em>numTasks</em>])
<a name="AggregateByLink"></a></td>
<td>When called on a dataset of (K, V) pairs, returns a dataset of (K, U) pairs where the values for each key are aggregated using the given combine functions and a neutral "zero" value. Allows an aggregated value type that is different than the input value
 type, while avoiding unnecessary allocations. Like in <code>groupByKey</code>, the number of reduce tasks is configurable through an optional second argument.</td>
</tr><tr><td><strong>sortByKey</strong>([<em>ascending</em>], [<em>numTasks</em>]) <a name="SortByLink">
</a></td>
<td>When called on a dataset of (K, V) pairs where K implements Ordered, returns a dataset of (K, V) pairs sorted by keys in ascending or descending order, as specified in the boolean
<code>ascending</code> argument.</td>
</tr><tr><td><strong>join</strong>(<em>otherDataset</em>, [<em>numTasks</em>]) <a name="JoinLink">
</a></td>
<td>When called on datasets of type (K, V) and (K, W), returns a dataset of (K, (V, W)) pairs with all pairs of elements for each key. Outer joins are supported through
<code>leftOuterJoin</code>, <code>rightOuterJoin</code>, and <code>fullOuterJoin</code>.</td>
</tr><tr><td><strong>cogroup</strong>(<em>otherDataset</em>, [<em>numTasks</em>]) <a name="CogroupLink">
</a></td>
<td>When called on datasets of type (K, V) and (K, W), returns a dataset of (K, (Iterable&lt;V&gt;, Iterable&lt;W&gt;)) tuples. This operation is also called
<code>groupWith</code>.</td>
</tr><tr><td><strong>cartesian</strong>(<em>otherDataset</em>)</td>
<td>When called on datasets of types T and U, returns a dataset of (T, U) pairs (all pairs of elements).</td>
</tr><tr><td><strong>pipe</strong>(<em>command</em>, <em>[envVars]</em>)</td>
<td>Pipe each partition of the RDD through a shell command, e.g. a Perl or bash script. RDD elements are written to the process's stdin and lines output to its stdout are returned as an RDD of strings.</td>
</tr><tr><td><strong>coalesce</strong>(<em>numPartitions</em>) <a name="CoalesceLink">
</a></td>
<td>Decrease the number of partitions in the RDD to numPartitions. Useful for running operations more efficiently after filtering down a large dataset.</td>
</tr><tr><td><strong>repartition</strong>(<em>numPartitions</em>)</td>
<td>Reshuffle the data in the RDD randomly to create either more or fewer partitions and balance it across them. This always shuffles all data over the network.
<a name="RepartitionLink"></a></td>
</tr><tr><td><strong>repartitionAndSortWithinPartitions</strong>(<em>partitioner</em>) <a name="Repartition2Link">
</a></td>
<td>Repartition the RDD according to the given partitioner and, within each resulting partition, sort records by their keys. This is more efficient than calling
<code>repartition</code> and then sorting within each partition because it can push the sorting down into the shuffle machinery.</td>
</tr></tbody></table><h3 id="actions">Actions</h3>
<p>The following table lists some of the common actions supported by Spark. Refer to theRDD API doc(<a href="http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.rdd.RDD" rel="nofollow">Scala</a>,
<a href="http://spark.apache.org/docs/latest/api/java/index.html?org/apache/spark/api/java/JavaRDD.html" rel="nofollow">
Java</a>, <a href="http://spark.apache.org/docs/latest/api/python/pyspark.html#pyspark.RDD" rel="nofollow">
Python</a>, <a href="http://spark.apache.org/docs/latest/api/R/index.html" rel="nofollow">
R</a>)</p>
<p>and pair RDD functions doc(<a href="http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.rdd.PairRDDFunctions" rel="nofollow">Scala</a>,
<a href="http://spark.apache.org/docs/latest/api/java/index.html?org/apache/spark/api/java/JavaPairRDD.html" rel="nofollow">
Java</a>)for details.</p>
<table class="table"><tbody><tr><th>Action</th>
<th>Meaning</th>
</tr><tr><td><strong>reduce</strong>(<em>func</em>)</td>
<td>Aggregate the elements of the dataset using a function <em>func</em> (which takes two arguments and returns one). The function should be commutative and associative so that it can be computed correctly in parallel.</td>
</tr><tr><td><strong>collect</strong>()</td>
<td>Return all the elements of the dataset as an array at the driver program. This is usually useful after a filter or other operation that returns a sufficiently small subset of the data.</td>
</tr><tr><td><strong>count</strong>()</td>
<td>Return the number of elements in the dataset.</td>
</tr><tr><td><strong>first</strong>()</td>
<td>Return the first element of the dataset (similar to take(1)).</td>
</tr><tr><td><strong>take</strong>(<em>n</em>)</td>
<td>Return an array with the first <em>n</em> elements of the dataset.</td>
</tr><tr><td><strong>takeSample</strong>(<em>withReplacement</em>, <em>num</em>, [<em>seed</em>])</td>
<td>Return an array with a random sample of <em>num</em> elements of the dataset, with or without replacement, optionally pre-specifying a random number generator seed.</td>
</tr><tr><td><strong>takeOrdered</strong>(<em>n</em>, <em>[ordering]</em>)</td>
<td>Return the first <em>n</em> elements of the RDD using either their natural order or a custom comparator.</td>
</tr><tr><td><strong>saveAsTextFile</strong>(<em>path</em>)</td>
<td>Write the elements of the dataset as a text file (or set of text files) in a given directory in the local filesystem, HDFS or any other Hadoop-supported file system. Spark will call toString on each element to convert it to a line of text in the file.</td>
</tr><tr><td><strong>saveAsSequenceFile</strong>(<em>path</em>) <br>
(Java and Scala)</td>
<td>Write the elements of the dataset as a Hadoop SequenceFile in a given path in the local filesystem, HDFS or any other Hadoop-supported file system. This is available on RDDs of key-value pairs that implement Hadoop's Writable interface. In Scala, it is
 also available on types that are implicitly convertible to Writable (Spark includes conversions for basic types like Int, Double, String, etc).</td>
</tr><tr><td><strong>saveAsObjectFile</strong>(<em>path</em>) <br>
(Java and Scala)</td>
<td>Write the elements of the dataset in a simple format using Java serialization, which can then be loaded using
<code>SparkContext.objectFile()</code>.</td>
</tr><tr><td><strong>countByKey</strong>() <a name="CountByLink"></a></td>
<td>Only available on RDDs of type (K, V). Returns a hashmap of (K, Int) pairs with the count of each key.</td>
</tr><tr><td><strong>foreach</strong>(<em>func</em>)</td>
<td>Run a function <em>func</em> on each element of the dataset. This is usually done for side effects such as updating an
<a href="http://spark.apache.org/docs/latest/programming-guide.html#AccumLink" rel="nofollow">
Accumulator</a> or interacting with external storage systems. <br><strong>Note</strong>: modifying variables other than Accumulators outside of the
<code>foreach()</code> may result in undefined behavior. See <a href="http://spark.apache.org/docs/latest/programming-guide.html#ClosuresLink" rel="nofollow">
Understanding closures </a>for more details.</td>
</tr></tbody></table><h3 id="shuffle-operations">Shuffle operations</h3>
<p>Certain operations within Spark trigger an event known as the shuffle. The shuffle is Spark’smechanism for re-distributing data so that it’s grouped differently across partitions. This typicallyinvolves copying data across executors and machines, making
 the shuffle a complex andcostly operation.</p>
<h4 id="background">Background</h4>
<p>To understand what happens during the shuffle we can consider the example of the<a href="http://spark.apache.org/docs/latest/programming-guide.html#ReduceByLink" rel="nofollow"><code>reduceByKey</code></a> operation. The
<code>reduceByKey</code> operation generates a new RDD where allvalues for a single key are combined into a tuple - the key and the result of executing a reducefunction against all values associated with that key. The challenge is that not all values for asingle
 key necessarily reside on the same partition, or even the same machine, but they must beco-located to compute the result.</p>
<p>In Spark, data is generally not distributed across partitions to be in the necessary place for aspecific operation. During computations, a single task will operate on a single partition - thus, toorganize all the data for a single
<code>reduceByKey</code> reduce task to execute, Spark needs to perform anall-to-all operation. It must read from all partitions to find all the values for all keys, and then bring together values across partitions to compute the final result for each key -
 this is called the <strong>shuffle</strong>.</p>
<p>Although the set of elements in each partition of newly shuffled data will be deterministic, and sois the ordering of partitions themselves, the ordering of these elements is not. If one desires predictably ordered data following shuffle then it’s possible
 to use:</p>
<ul><li><code>mapPartitions</code> to sort each partition using, for example, <code>.sorted</code></li><li><code>repartitionAndSortWithinPartitions</code> to efficiently sort partitions while simultaneously repartitioning</li><li><code>sortBy</code> to make a globally ordered RDD</li></ul><p>Operations which can cause a shuffle include <strong>repartition</strong> operations like<a href="http://spark.apache.org/docs/latest/programming-guide.html#RepartitionLink" rel="nofollow"><code>repartition</code></a> and
<a href="http://spark.apache.org/docs/latest/programming-guide.html#CoalesceLink" rel="nofollow">
<code>coalesce</code></a>, <strong>‘ByKey</strong> operations(except for counting) like
<a href="http://spark.apache.org/docs/latest/programming-guide.html#GroupByLink" rel="nofollow">
<code>groupByKey</code></a> and <a href="http://spark.apache.org/docs/latest/programming-guide.html#ReduceByLink" rel="nofollow">
<code>reduceByKey</code></a>, and<strong>join</strong> operations like <a href="http://spark.apache.org/docs/latest/programming-guide.html#CogroupLink" rel="nofollow">
<code>cogroup</code></a> and <a href="http://spark.apache.org/docs/latest/programming-guide.html#JoinLink" rel="nofollow">
<code>join</code></a>.</p>
<h4 id="performance-impact">Performance Impact</h4>
<p>The <strong>Shuffle</strong> is an expensive operation since it involves disk I/O, data serialization, andnetwork I/O. To organize data for the shuffle, Spark generates sets of tasks -
<em>map</em> tasks toorganize the data, and a set of <em>reduce</em> tasks to aggregate it. This nomenclature comes fromMapReduce and does not directly relate to Spark’s
<code>map</code> and <code>reduce</code> operations.</p>
<p>Internally, results from individual map tasks are kept in memory until they can’t fit. Then, these are sorted based on the target partition and written to a single file. On the reduce side, tasks read the relevant sorted blocks.</p>
<p>Certain shuffle operations can consume significant amounts of heap memory since they employ in-memory data structures to organize records before or after transferring them. Specifically,
<code>reduceByKey</code> and <code>aggregateByKey</code> create these structures on the map side, and
<code>'ByKey</code> operations generate these on the reduce side. When data does not fit in memory Spark will spill these tables to disk, incurring the additional overhead of disk I/O and increased garbage collection.</p>
<p>Shuffle also generates a large number of intermediate files on disk. As of Spark 1.3, these filesare preserved until the corresponding RDDs are no longer used and are garbage collected. This is done so the shuffle files don’t need to be re-created if the
 lineage is re-computed. Garbage collection may happen only after a long period time, if the application retains references to these RDDs or if GC does not kick in frequently. This means that long-running Spark jobs may consume a large amount of disk space.
 The temporary storage directory is specified by the<code>spark.local.dir</code> configuration parameter when configuring the Spark context.</p>
<p>Shuffle behavior can be tuned by adjusting a variety of configuration parameters. See the‘Shuffle Behavior’ section within the
<a href="http://spark.apache.org/docs/latest/configuration.html" rel="nofollow">
Spark Configuration Guide</a>.</p>
<h2 id="rdd-persistence">RDD Persistence</h2>
<p>One of the most important capabilities in Spark is <em>persisting</em> (or <em>
caching</em>) a dataset in memoryacross operations. When you persist an RDD, each node stores any partitions of it that it computes inmemory and reuses them in other actions on that dataset (or datasets derived from it). This allowsfuture actions to be much
 faster (often by more than 10x). Caching is a key tool foriterative algorithms and fast interactive use.</p>
<p>You can mark an RDD to be persisted using the <code>persist()</code> or <code>
cache()</code> methods on it. The first timeit is computed in an action, it will be kept in memory on the nodes. Spark’s cache is fault-tolerant –if any partition of an RDD is lost, it will automatically be recomputed using the transformationsthat originally
 created it.</p>
<p>In addition, each persisted RDD can be stored using a different <em>storage level</em>, allowing you, for example,to persist the dataset on disk, persist it in memory but as serialized Java objects (to save space),replicate it across nodes, or store it off-heap
 in <a href="http://tachyon-project.org/" rel="nofollow">Tachyon</a>.These levels are set by passing a<code>StorageLevel</code> object (<a href="http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.storage.StorageLevel" rel="nofollow">Scala</a>,<a href="http://spark.apache.org/docs/latest/api/java/index.html?org/apache/spark/storage/StorageLevel.html" rel="nofollow">Java</a>,<a href="http://spark.apache.org/docs/latest/api/python/pyspark.html#pyspark.StorageLevel" rel="nofollow">Python</a>)to
<code>persist()</code>. The <code>cache()</code> method is a shorthand for using the default storage level,which is
<code>StorageLevel.MEMORY_ONLY</code> (store deserialized objects in memory). The full set ofstorage levels is:</p>
<table class="table"><tbody><tr><th>Storage Level</th>
<th>Meaning</th>
</tr><tr><td>MEMORY_ONLY</td>
<td>Store RDD as deserialized Java objects in the JVM. If the RDD does not fit in memory, some partitions will not be cached and will be recomputed on the fly each time they're needed. This is the default level.</td>
</tr><tr><td>MEMORY_AND_DISK</td>
<td>Store RDD as deserialized Java objects in the JVM. If the RDD does not fit in memory, store the partitions that don't fit on disk, and read them from there when they're needed.</td>
</tr><tr><td>MEMORY_ONLY_SER</td>
<td>Store RDD as <em>serialized</em> Java objects (one byte array per partition). This is generally more space-efficient than deserialized objects, especially when using a
<a href="http://spark.apache.org/docs/latest/tuning.html" rel="nofollow">fast serializer</a>, but more CPU-intensive to read.</td>
</tr><tr><td>MEMORY_AND_DISK_SER</td>
<td>Similar to MEMORY_ONLY_SER, but spill partitions that don't fit in memory to disk instead of recomputing them on the fly each time they're needed.</td>
</tr><tr><td>DISK_ONLY</td>
<td>Store the RDD partitions only on disk.</td>
</tr><tr><td>MEMORY_ONLY_2, MEMORY_AND_DISK_2, etc.</td>
<td>Same as the levels above, but replicate each partition on two cluster nodes.</td>
</tr><tr><td>OFF_HEAP (experimental)</td>
<td>Store RDD in serialized format in <a href="http://tachyon-project.org" rel="nofollow">
Tachyon</a>. Compared to MEMORY_ONLY_SER, OFF_HEAP reduces garbage collection overhead and allows executors to be smaller and to share a pool of memory, making it attractive in environments with large heaps or multiple concurrent applications. Furthermore,
 as the RDDs reside in Tachyon, the crash of an executor does not lead to losing the in-memory cache. In this mode, the memory in Tachyon is discardable. Thus, Tachyon does not attempt to reconstruct a block that it evicts from memory. If you plan to use Tachyon
 as the off heap store, Spark is compatible with Tachyon out-of-the-box. Please refer to this
<a href="http://tachyon-project.org/master/Running-Spark-on-Tachyon.html" rel="nofollow">
page</a> for the suggested version pairings.</td>
</tr></tbody></table><p><strong>Note:</strong> <em>In Python, stored objects will always be serialized with the
<a href="https://docs.python.org/2/library/pickle.html" rel="nofollow">Pickle</a> library, so it does not matter whether you choose a serialized level.</em></p>
<p>Spark also automatically persists some intermediate data in shuffle operations (e.g.
<code>reduceByKey</code>), even without users calling <code>persist</code>. This is done to avoid recomputing the entire input if a node fails during the shuffle. We still recommend users call
<code>persist</code> on the resulting RDD if they plan to reuse it.</p>
<h3 id="which-storage-level-to-choose">Which Storage Level to Choose?</h3>
<p>Spark’s storage levels are meant to provide different trade-offs between memory usage and CPUefficiency. We recommend going through the following process to select one:</p>
<ul><li>
<p>If your RDDs fit comfortably with the default storage level (<code>MEMORY_ONLY</code>), leave them that way.This is the most CPU-efficient option, allowing operations on the RDDs to run as fast as possible.</p>
</li><li>
<p>If not, try using <code>MEMORY_ONLY_SER</code> and <a href="http://spark.apache.org/docs/latest/tuning.html" rel="nofollow">
selecting a fast serialization library</a> tomake the objects much more space-efficient, but still reasonably fast to access.</p>
</li><li>
<p>Don’t spill to disk unless the functions that computed your datasets are expensive, or they filtera large amount of the data. Otherwise, recomputing a partition may be as fast as reading it fromdisk.</p>
</li><li>
<p>Use the replicated storage levels if you want fast fault recovery (e.g. if using Spark to serverequests from a web application).
<em>All</em> the storage levels provide full fault tolerance byrecomputing lost data, but the replicated ones let you continue running tasks on the RDD withoutwaiting to recompute a lost partition.</p>
</li><li>
<p>In environments with high amounts of memory or multiple applications, the experimental
<code>OFF_HEAP</code>mode has several advantages:</p>
<ul><li>It allows multiple executors to share the same pool of memory in Tachyon.</li><li>It significantly reduces garbage collection costs.</li><li>Cached data is not lost if individual executors crash.</li></ul></li></ul><h3 id="removing-data">Removing Data</h3>
<p>Spark automatically monitors cache usage on each node and drops out old data partitions in aleast-recently-used (LRU) fashion. If you would like to manually remove an RDD instead of waiting forit to fall out of the cache, use the
<code>RDD.unpersist()</code> method.</p>
<h1 id="shared-variables">Shared Variables</h1>
<p>Normally, when a function passed to a Spark operation (such as <code>map</code> or
<code>reduce</code>) is executed on aremote cluster node, it works on separate copies of all the variables used in the function. Thesevariables are copied to each machine, and no updates to the variables on the remote machine arepropagated back to the driver
 program. Supporting general, read-write shared variables across taskswould be inefficient. However, Spark does provide two limited types of
<em>shared variables</em> for twocommon usage patterns: broadcast variables and accumulators.</p>
<h2 id="broadcast-variables">Broadcast Variables</h2>
<p>Broadcast variables allow the programmer to keep a read-only variable cached on each machine ratherthan shipping a copy of it with tasks. They can be used, for example, to give every node a copy of alarge input dataset in an efficient manner. Spark also
 attempts to distribute broadcast variablesusing efficient broadcast algorithms to reduce communication cost.</p>
<p>Spark actions are executed through a set of stages, separated by distributed “shuffle” operations.Spark automatically broadcasts the common data needed by tasks within each stage. The databroadcasted this way is cached in serialized form and deserialized
 before running each task. Thismeans that explicitly creating broadcast variables is only useful when tasks across multiple stagesneed the same data or when caching the data in deserialized form is important.</p>
<p>Broadcast variables are created from a variable <code>v</code> by calling <code>
SparkContext.broadcast(v)</code>. Thebroadcast variable is a wrapper around <code>
v</code>, and its value can be accessed by calling the <code>value</code>method. The code below shows this:</p>
<ul class="nav nav-tabs"><li class="active"><a class="tab_scala" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_scala_9" rel="nofollow"><strong>Scala</strong></a></li><li><a class="tab_java" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_java_9" rel="nofollow"><strong>Java</strong></a></li><li><a class="tab_python" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_python_9" rel="nofollow"><strong>Python</strong></a></li></ul><div class="codetabs tab-content">
<div id="tab_scala_9" class="tab-pane active">
<div class="highlight">
<pre><code class="language-scala"><span class="n">scala</span><span class="o">&gt;</span> <span class="k">val</span> <span class="n">broadcastVar</span> <span class="k">=</span> <span class="n">sc</span><span class="o">.</span><span class="n">broadcast</span><span class="o">(</span><span class="nc">Array</span><span class="o">(</span><span class="mi">1</span><span class="o">,</span> <span class="mi">2</span><span class="o">,</span> <span class="mi">3</span><span class="o">))</span>
<span class="n">broadcastVar</span><span class="k">:</span> <span class="kt">org.apache.spark.broadcast.Broadcast</span><span class="o">[</span><span class="kt">Array</span><span class="o">[</span><span class="kt">Int</span><span class="o">]]</span> <span class="k">=</span> <span class="nc">Broadcast</span><span class="o">(</span><span class="mi">0</span><span class="o">)</span>

<span class="n">scala</span><span class="o">&gt;</span> <span class="n">broadcastVar</span><span class="o">.</span><span class="n">value</span>
<span class="n">res0</span><span class="k">:</span> <span class="kt">Array</span><span class="o">[</span><span class="kt">Int</span><span class="o">]</span> <span class="k">=</span> <span class="nc">Array</span><span class="o">(</span><span class="mi">1</span><span class="o">,</span> <span class="mi">2</span><span class="o">,</span> <span class="mi">3</span><span class="o">)</span></code></pre>
</div>
</div>
</div>
<p>After the broadcast variable is created, it should be used instead of the value
<code>v</code> in any functionsrun on the cluster so that <code>v</code> is not shipped to the nodes more than once. In addition, the object<code>v</code> should not be modified after it is broadcast in order to ensure that all nodes get the samevalue of the
 broadcast variable (e.g. if the variable is shipped to a new node later).</p>
<h2 id="accumulators-a-nameaccumlinka">Accumulators <a name="AccumLink">
</a></h2>
<p>Accumulators are variables that are only “added” to through an associative operation and cantherefore be efficiently supported in parallel. They can be used to implement counters (as inMapReduce) or sums. Spark natively supports accumulators of numeric types,
 and programmerscan add support for new types. If accumulators are created with a name, they will bedisplayed in Spark’s UI. This can be useful for understanding the progress of running stages (NOTE: this is not yet supported in Python).</p>
<p>An accumulator is created from an initial value <code>v</code> by calling <code>
SparkContext.accumulator(v)</code>. Tasksrunning on the cluster can then add to it using the
<code>add</code> method or the <code>+=</code> operator (in Scala and Python).However, they cannot read its value.Only the driver program can read the accumulator’s value, using its
<code>value</code> method.</p>
<p>The code below shows an accumulator being used to add up the elements of an array:</p>
<ul class="nav nav-tabs"><li class="active"><a class="tab_scala" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_scala_10" rel="nofollow"><strong>Scala</strong></a></li><li><a class="tab_java" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_java_10" rel="nofollow"><strong>Java</strong></a></li><li><a class="tab_python" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_python_10" rel="nofollow"><strong>Python</strong></a></li></ul><div class="codetabs tab-content">
<div id="tab_scala_10" class="tab-pane active">
<div class="highlight">
<pre><code class="language-scala"><span class="n">scala</span><span class="o">&gt;</span> <span class="k">val</span> <span class="n">accum</span> <span class="k">=</span> <span class="n">sc</span><span class="o">.</span><span class="n">accumulator</span><span class="o">(</span><span class="mi">0</span><span class="o">,</span> <span class="s">"My Accumulator"</span><span class="o">)</span>
<span class="n">accum</span><span class="k">:</span> <span class="kt">spark.Accumulator</span><span class="o">[</span><span class="kt">Int</span><span class="o">]</span> <span class="k">=</span> <span class="mi">0</span>

<span class="n">scala</span><span class="o">&gt;</span> <span class="n">sc</span><span class="o">.</span><span class="n">parallelize</span><span class="o">(</span><span class="nc">Array</span><span class="o">(</span><span class="mi">1</span><span class="o">,</span> <span class="mi">2</span><span class="o">,</span> <span class="mi">3</span><span class="o">,</span> <span class="mi">4</span><span class="o">)).</span><span class="n">foreach</span><span class="o">(</span><span class="n">x</span> <span class="k">=&gt;</span> <span class="n">accum</span> <span class="o">+=</span> <span class="n">x</span><span class="o">)</span>
<span class="o">...</span>
<span class="mi">10</span><span class="o">/</span><span class="mi">09</span><span class="o">/</span><span class="mi">29</span> <span class="mi">18</span><span class="k">:</span><span class="err">41</span><span class="kt">:</span><span class="err">08</span> <span class="kt">INFO</span> <span class="kt">SparkContext:</span> <span class="kt">Tasks</span> <span class="kt">finished</span> <span class="kt">in</span> <span class="err">0</span><span class="kt">.</span><span class="err">317106</span> <span class="kt">s</span>

<span class="n">scala</span><span class="o">&gt;</span> <span class="n">accum</span><span class="o">.</span><span class="n">value</span>
<span class="n">res2</span><span class="k">:</span> <span class="kt">Int</span> <span class="o">=</span> <span class="mi">10</span></code></pre>
</div>
<p>While this code used the built-in support for accumulators of type Int, programmers can alsocreate their own types by subclassing
<a href="http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.AccumulatorParam" rel="nofollow">
AccumulatorParam</a>.The AccumulatorParam interface has two methods: <code>zero</code> for providing a “zero value” for your datatype, and
<code>addInPlace</code> for adding two values together. For example, supposing we had a
<code>Vector</code> classrepresenting mathematical vectors, we could write:</p>
<div class="highlight">
<pre><code class="language-scala"><span class="k">object</span> <span class="nc">VectorAccumulatorParam</span> <span class="k">extends</span> <span class="nc">AccumulatorParam</span><span class="o">[</span><span class="kt">Vector</span><span class="o">]</span> <span class="o">{</span>
  <span class="k">def</span> <span class="n">zero</span><span class="o">(</span><span class="n">initialValue</span><span class="k">:</span> <span class="kt">Vector</span><span class="o">)</span><span class="k">:</span> <span class="kt">Vector</span> <span class="o">=</span> <span class="o">{</span>
    <span class="nc">Vector</span><span class="o">.</span><span class="n">zeros</span><span class="o">(</span><span class="n">initialValue</span><span class="o">.</span><span class="n">size</span><span class="o">)</span>
  <span class="o">}</span>
  <span class="k">def</span> <span class="n">addInPlace</span><span class="o">(</span><span class="n">v1</span><span class="k">:</span> <span class="kt">Vector</span><span class="o">,</span> <span class="n">v2</span><span class="k">:</span> <span class="kt">Vector</span><span class="o">)</span><span class="k">:</span> <span class="kt">Vector</span> <span class="o">=</span> <span class="o">{</span>
    <span class="n">v1</span> <span class="o">+=</span> <span class="n">v2</span>
  <span class="o">}</span>
<span class="o">}</span>

<span class="c1">// Then, create an Accumulator of this type:</span>
<span class="k">val</span> <span class="n">vecAccum</span> <span class="k">=</span> <span class="n">sc</span><span class="o">.</span><span class="n">accumulator</span><span class="o">(</span><span class="k">new</span> <span class="nc">Vector</span><span class="o">(...))(</span><span class="nc">VectorAccumulatorParam</span><span class="o">)</span></code></pre>
</div>
<p>In Scala, Spark also supports the more general <a href="http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.Accumulable" rel="nofollow">
Accumulable</a>interface to accumulate data where the resulting type is not the same as the elements added (e.g. builda list by collecting together elements), and the
<code>SparkContext.accumulableCollection</code> method for accumulatingcommon Scala collection types.</p>
</div>
</div>
<p>For accumulator updates performed inside <strong>actions only</strong>, Spark guarantees that each task’s update to the accumulator will only be applied once, i.e. restarted tasks will not update the value. In transformations, users should be aware of that
 each task’s update may be applied more than once if tasks or job stages are re-executed.</p>
<p>Accumulators do not change the lazy evaluation model of Spark. If they are being updated within an operation on an RDD, their value is only updated once that RDD is computed as part of an action. Consequently, accumulator updates are not guaranteed to be
 executed when made within a lazy transformation like <code>map()</code>. The below code fragment demonstrates this property:</p>
<ul class="nav nav-tabs"><li class="active"><a class="tab_scala" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_scala_11" rel="nofollow"><strong>Scala</strong></a></li><li><a class="tab_java" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_java_11" rel="nofollow"><strong>Java</strong></a></li><li><a class="tab_python" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_python_11" rel="nofollow"><strong>Python</strong></a></li></ul><div class="codetabs tab-content">
<div id="tab_scala_11" class="tab-pane active">
<div class="highlight">
<pre><code class="language-scala"><span class="k">val</span> <span class="n">accum</span> <span class="k">=</span> <span class="n">sc</span><span class="o">.</span><span class="n">accumulator</span><span class="o">(</span><span class="mi">0</span><span class="o">)</span>
<span class="n">data</span><span class="o">.</span><span class="n">map</span> <span class="o">{</span> <span class="n">x</span> <span class="k">=&gt;</span> <span class="n">accum</span> <span class="o">+=</span> <span class="n">x</span><span class="o">;</span> <span class="n">f</span><span class="o">(</span><span class="n">x</span><span class="o">)</span> <span class="o">}</span>
<span class="c1">// Here, accum is still 0 because no actions have caused the `map` to be computed.</span></code></pre>
</div>
</div>
</div>
<h1 id="deploying-to-a-cluster">Deploying to a Cluster</h1>
<p>The <a href="http://spark.apache.org/docs/latest/submitting-applications.html" rel="nofollow">
application submission guide</a> describes how to submit applications to a cluster.In short, once you package your application into a JAR (for Java/Scala) or a set of
<code>.py</code> or <code>.zip</code> files (for Python),the <code>bin/spark-submit</code> script lets you submit it to any supported cluster manager.</p>
<h1 id="launching-spark-jobs-from-java--scala">Launching Spark jobs from Java / Scala</h1>
<p>The <a href="http://spark.apache.org/docs/latest/api/java/index.html?org/apache/spark/launcher/package-summary.html" rel="nofollow">
org.apache.spark.launcher</a>package provides classes for launching Spark jobs as child processes using a simple Java API.</p>
<h1 id="unit-testing">Unit Testing</h1>
<p>Spark is friendly to unit testing with any popular unit test framework.Simply create a
<code>SparkContext</code> in your test with the master URL set to <code>local</code>, run your operations,and then call
<code>SparkContext.stop()</code> to tear it down.Make sure you stop the context within a
<code>finally</code> block or the test framework’s <code>tearDown</code> method,as Spark does not support two contexts running concurrently in the same program.</p>
<h1 id="migrating-from-pre-10-versions-of-spark">Migrating from pre-1.0 Versions of Spark</h1>
<ul class="nav nav-tabs"><li class="active"><a class="tab_scala" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_scala_12" rel="nofollow"><strong>Scala</strong></a></li><li><a class="tab_java" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_java_12" rel="nofollow"><strong>Java</strong></a></li><li><a class="tab_python" href="http://spark.apache.org/docs/latest/programming-guide.html#tab_python_12" rel="nofollow"><strong>Python</strong></a></li></ul><div class="codetabs tab-content">
<div id="tab_scala_12" class="tab-pane active">
<p>Spark 1.0 freezes the API of Spark Core for the 1.X series, in that any API available today that isnot marked “experimental” or “developer API” will be supported in future versions.The only change for Scala users is that the grouping operations, e.g.
<code>groupByKey</code>, <code>cogroup</code> and <code>join</code>,have changed from returning
<code>(Key, Seq[Value])</code> pairs to <code>(Key, Iterable[Value])</code>.</p>
</div>
</div>
<p>Migration guides are also available for <a href="http://spark.apache.org/docs/latest/streaming-programming-guide.html#migration-guide-from-091-or-below-to-1x" rel="nofollow">
Spark Streaming</a>,<a href="http://spark.apache.org/docs/latest/mllib-guide.html#migration-guide" rel="nofollow">MLlib</a> and
<a href="http://spark.apache.org/docs/latest/graphx-programming-guide.html#migrating-from-spark-091" rel="nofollow">
GraphX</a>.</p>
<h1 id="where-to-go-from-here">Where to Go from Here</h1>
<p>You can see some <a href="http://spark.apache.org/examples.html" rel="nofollow">
example Spark programs</a> on the Spark website.In addition, Spark includes several samples in the
<code>examples</code> directory(<a href="https://github.com/apache/spark/tree/master/examples/src/main/scala/org/apache/spark/examples" rel="nofollow">Scala</a>,
<a href="https://github.com/apache/spark/tree/master/examples/src/main/java/org/apache/spark/examples" rel="nofollow">
Java</a>, <a href="https://github.com/apache/spark/tree/master/examples/src/main/python" rel="nofollow">
Python</a>, <a href="https://github.com/apache/spark/tree/master/examples/src/main/r" rel="nofollow">
R</a>).You can run Java and Scala examples by passing the class name to Spark’s <code>
bin/run-example</code> script; for instance:</p>
<pre><code>./bin/run-example SparkPi
</code></pre>
<p>For Python examples, use <code>spark-submit</code> instead:</p>
<pre><code>./bin/spark-submit examples/src/main/python/pi.py
</code></pre>
<p>For R examples, use <code>spark-submit</code> instead:</p>
<pre><code>./bin/spark-submit examples/src/main/r/dataframe.R
</code></pre>
<p>For help on optimizing your programs, the <a href="http://spark.apache.org/docs/latest/configuration.html" rel="nofollow">
configuration</a> and<a href="http://spark.apache.org/docs/latest/tuning.html" rel="nofollow">tuning</a> guides provide information on best practices. They are especially important formaking sure that your data is stored in memory in an efficient format.For
 help on deploying, the <a href="http://spark.apache.org/docs/latest/cluster-overview.html" rel="nofollow">
cluster mode overview</a> describes the components involvedin distributed operation and supported cluster managers.</p>
<p>Finally, full API documentation is available in<a href="http://spark.apache.org/docs/latest/api/scala/#org.apache.spark.package" rel="nofollow">Scala</a>,
<a href="http://spark.apache.org/docs/latest/api/java/" rel="nofollow">Java</a>,
<a href="http://spark.apache.org/docs/latest/api/python/" rel="nofollow">Python</a> and
<a href="http://spark.apache.org/docs/latest/api/R/" rel="nofollow">R</a>.</p>
<p>from: http://spark.apache.org/docs/latest/programming-guide.html<br></p>
            </div>
                </div>