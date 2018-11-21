---
layout:     post
title:      Spark Examples
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="spark-examples">Spark Examples</h1>

<p>这些例子提供了Spark API的快速概览。Spark API建立在分布数据集的理念上，包含Java或Python对象。从外部数据创建数据集，然后对它应用并行操作。有两种操作类型：<em>transformations</em>-从上一个数据集定义一个新数据集；<em>actions</em>-在集群中开始一个Job。</p>



<h2 id="text-search">Text Search</h2>

<p>在log文件中查询所有error信息：</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> file = spark.textFile(<span class="hljs-string">"hdfs://..."</span>)
<span class="hljs-keyword">val</span> errors = file.filter(line =&gt; line.contains(<span class="hljs-string">"ERROR"</span>))
<span class="hljs-comment">// Count all the errors</span>
errors.count()
<span class="hljs-comment">// Count errors mentioning MySQL</span>
errors.filter(line =&gt; line.contains(<span class="hljs-string">"MySQL"</span>)).count()
<span class="hljs-comment">// Fetch the MySQL errors as an array of strings</span>
errors.filter(line =&gt; line.contains(<span class="hljs-string">"MySQL"</span>)).collect()</code></pre>

<p><em>filter</em>是<em>transformations</em>操作；<em>count</em>，<em>collect</em>是<em>actions</em>操作。</p>



<h2 id="in-memory-text-search">In-Memory Text Search</h2>

<p>Spark可以<em>cache</em>数据集到内存，提升复用的速度。下面的例子，把error信息加载到RAM使用：</p>



<pre class="prettyprint"><code class="language-scala hljs ">errors.cache()</code></pre>

<p>第一个使用<em>errors</em>的action执行后，之后对errors的action都会非常快。</p>



<h2 id="word-count">Word Count</h2>

<p>这个例子，我们使用一些<em>transformations</em> 建立一个(String, Int)对的数据集，调用<em>counts</em>，然后保存到文件。</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> file = spark.textFile(<span class="hljs-string">"hdfs://..."</span>)
<span class="hljs-keyword">val</span> counts = file.flatMap(line =&gt; line.split(<span class="hljs-string">" "</span>))
                 .map(word =&gt; (word, <span class="hljs-number">1</span>))
                 .reduceByKey(_ + _)
counts.saveAsTextFile(<span class="hljs-string">"hdfs://..."</span>) </code></pre>



<h2 id="estimating-pi">Estimating Pi</h2>

<p>Spark也可以应用在计算密集型任务。这段代码通过”throwing darts”在循环中估算π。选择随机点在单位正方形((0,0) to (1,1))中，然后看有多少在单位圆粒。分数部分应该是π / 4，所以我们可以使用这种方式获得估算。</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> count = spark.parallelize(<span class="hljs-number">1</span> to NUM_SAMPLES).map{i =&gt;
  <span class="hljs-keyword">val</span> x = Math.random()
  <span class="hljs-keyword">val</span> y = Math.random()
  <span class="hljs-keyword">if</span> (x*x + y*y &lt; <span class="hljs-number">1</span>) <span class="hljs-number">1</span> <span class="hljs-keyword">else</span> <span class="hljs-number">0</span>
}.reduce(_ + _)
println(<span class="hljs-string">"Pi is roughly "</span> + <span class="hljs-number">4.0</span> * count / NUM_SAMPLES)</code></pre>



<h2 id="logistic-regression逻辑回归">Logistic Regression(逻辑回归)</h2>

<p>这是一个迭代的机器学习算法-探索怎样查找最好的超平面-分隔多维特征空间中2组点。例如，它被应用在分类垃圾邮件。因为这个算法在同一个数据集应用相同的MapReduce操作，更适合缓存输出到内存。</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> points = spark.textFile(...).map(parsePoint).cache()
<span class="hljs-keyword">var</span> w = Vector.random(D) <span class="hljs-comment">// current separating plane</span>
<span class="hljs-keyword">for</span> (i &lt;- <span class="hljs-number">1</span> to ITERATIONS) {
  <span class="hljs-keyword">val</span> gradient = points.map(p =&gt;
    (<span class="hljs-number">1</span> / (<span class="hljs-number">1</span> + exp(-p.y*(w dot p.x))) - <span class="hljs-number">1</span>) * p.y * p.x
  ).reduce(_ + _)
  w -= gradient
}
println(<span class="hljs-string">"Final separating plane: "</span> + w)</code></pre>

<p>注意当前分隔平面，w，每个<em>map</em>调用自动装填到集群中。 <br>
下面图表比较了Spark和Hadoop程序，在100个节点集群中的100GB数据，迭代的平均运行时间。表现了内存缓存的好处： <br>
<img src="http://spark.apache.org/images/logistic-regression.png" alt="这里写图片描述" title=""></p>



<h1 id="additional-examples">Additional Examples</h1>

<p>更多的分布式Spark附加实例：</p>

<ul>
<li><p>Basic Spark: <em><a href="https://github.com/apache/spark/tree/master/examples/src/main/scala/org/apache/spark/examples" rel="nofollow">Scala examples</a></em>, Java examples, Python examples</p></li>
<li><p>Spark Streaming: <em><a href="https://github.com/apache/spark/tree/master/examples/src/main/scala/org/apache/spark/examples/streaming" rel="nofollow">Scala examples</a></em>, Java examples</p></li>
</ul>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>