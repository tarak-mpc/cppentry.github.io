---
layout:     post
title:      Spark RDD API 参考示例（二）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，欢迎转载。					https://blog.csdn.net/u010472512/article/details/72910442				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>本文参考<a href="http://homepage.cs.latrobe.edu.au/zhe/ZhenHeSparkRDDAPIExamples.html" rel="nofollow" target="_blank">Zhen He</a></p>



<h2 id="16dependencies"><strong>16、dependencies</strong></h2>

<p>原型  <br>
final def dependencies: Seq[Dependency[_]]</p>

<p>含义 <br>
<strong>dependencies</strong> 返回RDD的依赖，简单来说，就是这个RDD是怎么一步步生成的。通过这种方式可以很快的重新构建这个RDD</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> b = sc.parallelize(List(<span class="hljs-number">1</span>,<span class="hljs-number">2</span>,<span class="hljs-number">3</span>,<span class="hljs-number">4</span>,<span class="hljs-number">5</span>,<span class="hljs-number">6</span>,<span class="hljs-number">7</span>,<span class="hljs-number">8</span>,<span class="hljs-number">2</span>,<span class="hljs-number">4</span>,<span class="hljs-number">2</span>,<span class="hljs-number">1</span>,<span class="hljs-number">1</span>,<span class="hljs-number">1</span>,<span class="hljs-number">1</span>,<span class="hljs-number">1</span>))
b.dependencieses
res0: Seq[org.apache.spark.Dependency[_]] = List()
<span class="hljs-comment">//表示目前b还没有依赖关系</span>

<span class="hljs-keyword">val</span> c=b.map(a=&gt;a)
c.dependencieses
res1: Seq[org.apache.spark.Dependency[_]] = List(org.apache.spark.OneToOneDependency@<span class="hljs-number">73041</span>dc1)
<span class="hljs-comment">//表示目前c存在一个依赖关系</span>

<span class="hljs-keyword">val</span> d = c.cartesian(a)
d.dependencies
res2: List(org.apache.spark.rdd.CartesianRDD$$anon$<span class="hljs-number">1</span>@<span class="hljs-number">2e981</span>e6a, org.apache.spark.rdd.CartesianRDD$$anon$<span class="hljs-number">2</span>@<span class="hljs-number">5911</span>fad1)
<span class="hljs-comment">//这里表示d存在两个依赖,通过这种方式，可以很快的恢复其构建顺序</span>
</code></pre>

<hr>

<h2 id="17distinct"><strong>17、distinct</strong></h2>

<p>原型  <br>
def distinct(): RDD[T] <br>
def distinct(numPartitions: Int): RDD[T]</p>

<p>含义 <br>
<strong>distinct</strong> 返回一个只包含唯一键的新的RDD，简单来说，就是去重复</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-comment">//用于去重复</span>
<span class="hljs-keyword">val</span> c = sc.parallelize(List(<span class="hljs-string">"Gnu"</span>, <span class="hljs-string">"Cat"</span>, <span class="hljs-string">"Rat"</span>, <span class="hljs-string">"Dog"</span>, <span class="hljs-string">"Gnu"</span>, <span class="hljs-string">"Rat"</span>), <span class="hljs-number">2</span>)
<span class="hljs-comment">//没有带分区数，那么分区数保持为2</span>
c.distinct.collect
res1: Array[String] = Array(Dog, Gnu, Cat, Rat)

<span class="hljs-comment">//去重复时，还可以指定所用的分区数量，提高并行效率</span>
<span class="hljs-keyword">val</span> a = sc.parallelize(List(<span class="hljs-number">1</span>,<span class="hljs-number">2</span>,<span class="hljs-number">3</span>,<span class="hljs-number">4</span>,<span class="hljs-number">5</span>,<span class="hljs-number">6</span>,<span class="hljs-number">7</span>,<span class="hljs-number">8</span>,<span class="hljs-number">9</span>,<span class="hljs-number">10</span>))
<span class="hljs-comment">//将取重复的结果分为了三个分区</span>
a.distinct(<span class="hljs-number">3</span>).collect
res2: Array[Int] = Array(<span class="hljs-number">6</span>, <span class="hljs-number">3</span>, <span class="hljs-number">9</span>, <span class="hljs-number">4</span>, <span class="hljs-number">1</span>, <span class="hljs-number">7</span>, <span class="hljs-number">10</span>, <span class="hljs-number">8</span>, <span class="hljs-number">5</span>, <span class="hljs-number">2</span>)</code></pre>

<hr>

<h2 id="18first"><strong>18、first</strong></h2>

<p>原型  <br>
def first(): T</p>

<p>含义 <br>
<strong>first</strong> 查找RDD中第一个元素，可以是单个元素，也可以是一个元组</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-comment">//查找单个元素</span>
<span class="hljs-keyword">val</span> c = sc.parallelize(List(<span class="hljs-string">"Gnu"</span>, <span class="hljs-string">"Cat"</span>, <span class="hljs-string">"Rat"</span>, <span class="hljs-string">"Dog"</span>), <span class="hljs-number">2</span>)
c.first
res1: String = Gnu

<span class="hljs-comment">//查找元组</span>
<span class="hljs-keyword">val</span> c = sc.parallelize(List(<span class="hljs-string">"Gnu"</span>, <span class="hljs-string">"Cat"</span>, <span class="hljs-string">"Rat"</span>, <span class="hljs-string">"Dog"</span>), <span class="hljs-number">2</span>)
<span class="hljs-keyword">val</span> d = sc.parallelize(List(<span class="hljs-number">1</span>,<span class="hljs-number">2</span>,<span class="hljs-number">3</span>,<span class="hljs-number">4</span>), <span class="hljs-number">2</span>)
<span class="hljs-keyword">val</span> e = c.zip(d)
e.first
res2: (String, Int) = (Gnu,<span class="hljs-number">1</span>)
</code></pre>

<hr>

<h2 id="19filter"><strong>19、filter</strong></h2>

<p>原型  <br>
def filter(f: T =&gt; Boolean): RDD[T]</p>

<p>含义 <br>
<strong>filter</strong> 筛选使得filter中的函数为真的值，也就是筛选满足条件的值，并将这些值放入到RDD中</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-comment">//筛选满足模2为0的值</span>
<span class="hljs-keyword">val</span> a = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">10</span>, <span class="hljs-number">3</span>)
<span class="hljs-keyword">val</span> b = a.filter(_ % <span class="hljs-number">2</span> == <span class="hljs-number">0</span>)
b.collect
res1: Array[Int] = Array(<span class="hljs-number">2</span>, <span class="hljs-number">4</span>, <span class="hljs-number">6</span>, <span class="hljs-number">8</span>, <span class="hljs-number">10</span>)

<span class="hljs-comment">//当使用自定义的筛选函数时，这个筛选函数必须要能够处理RDD中所有的元素</span>
<span class="hljs-keyword">val</span> b = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">8</span>)
b.filter(_ &lt; <span class="hljs-number">4</span>).collect
res2: Array[Int] = Array(<span class="hljs-number">1</span>, <span class="hljs-number">2</span>, <span class="hljs-number">3</span>)

<span class="hljs-comment">//如果不能处理所有元素，就会报错</span>
<span class="hljs-keyword">val</span> a = sc.parallelize(List(<span class="hljs-string">"cat"</span>, <span class="hljs-string">"horse"</span>, <span class="hljs-number">4.0</span>, <span class="hljs-number">3.5</span>, <span class="hljs-number">2</span>, <span class="hljs-string">"dog"</span>))
a.filter(_ &lt; <span class="hljs-number">4</span>).collect
&lt;console&gt;:<span class="hljs-number">3</span>: error: value &lt; is not a member of Any
<span class="hljs-comment">//如果需要对不同类型的数据处理，可以自定义混合处理函数，例如使用case语句</span>
</code></pre>

<hr>

<h2 id="20filterbyrange"><strong>20、filterByRange</strong></h2>

<p>原型  <br>
def filterByRange(lower: K, upper: K): RDD[P] <br>
含义 <br>
<strong>filterByRange</strong> 只适合筛选key-value类型的键值对，<strong>filterByRange</strong> 参数中的范围只对key起作用，通过key来筛选。</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> randRDD = sc.parallelize(List( (<span class="hljs-number">2</span>,<span class="hljs-string">"cat"</span>), (<span class="hljs-number">6</span>, <span class="hljs-string">"mouse"</span>),(<span class="hljs-number">7</span>, <span class="hljs-string">"cup"</span>), (<span class="hljs-number">3</span>, <span class="hljs-string">"book"</span>), (<span class="hljs-number">4</span>, <span class="hljs-string">"tv"</span>), (<span class="hljs-number">1</span>, <span class="hljs-string">"screen"</span>), (<span class="hljs-number">5</span>, <span class="hljs-string">"heater"</span>)), <span class="hljs-number">3</span>)
<span class="hljs-comment">//先排序，这样筛选的速度快一些</span>
<span class="hljs-keyword">val</span> sortedRDD = randRDD.sortByKey()
<span class="hljs-comment">//筛选key在1-3范围内的元组</span>
sortedRDD.filterByRange(<span class="hljs-number">1</span>, <span class="hljs-number">3</span>).collect
res1: Array[(Int, String)] = Array((<span class="hljs-number">1</span>,screen), (<span class="hljs-number">2</span>,cat), (<span class="hljs-number">3</span>,book))</code></pre>

<hr>



<h2 id="21flatmap"><strong>21、flatMap</strong></h2>

<p>原型  <br>
def flatMap[U: ClassTag](f: T =&gt; TraversableOnce[U]): RDD[U]</p>

<p>含义 <br>
<strong>flatMap</strong> 和<strong>map</strong> 功能类似，但是他是将每个元素中的数据拆分来处理，而<strong>map</strong> 是将其作为一个整体来处理，也就是<strong>flatMap</strong> 的操作粒度更小。如果一个数据是一行文本，我们要统计单词，就可以使用它指定切分方式，来统计单词个数，这样的功能<strong>Map</strong> 不方便实现</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> z = sc.parallelize(List(<span class="hljs-string">"cat"</span>,<span class="hljs-string">"dog"</span>,<span class="hljs-string">"gnu"</span>),<span class="hljs-number">2</span>)
z.flatMap(i=&gt;i).collect
<span class="hljs-comment">//flatMap每次将元素的值，会进行再次切分</span>
res1: Array[Char] = Array(c, a, t, d, o, g, g, n, u)

<span class="hljs-comment">//map每次将一个元素作为处理单位</span>
z.map(i=&gt;i).collect
res2: Array[String] = Array(cat, dog, gnu)

sc.parallelize(List(<span class="hljs-number">1</span>, <span class="hljs-number">2</span>, <span class="hljs-number">3</span>), <span class="hljs-number">2</span>).flatMap(x =&gt; List(x, x, x)).collect
res3: Array[Int] = Array(<span class="hljs-number">1</span>, <span class="hljs-number">1</span>, <span class="hljs-number">1</span>, <span class="hljs-number">2</span>, <span class="hljs-number">2</span>, <span class="hljs-number">2</span>, <span class="hljs-number">3</span>, <span class="hljs-number">3</span>, <span class="hljs-number">3</span>)
</code></pre>

<hr>

<h2 id="22flatmapvalues"><strong>22、flatMapValues</strong></h2>

<p>原型  <br>
def flatMapValues[U](f: V =&gt; TraversableOnce[U]): RDD[(K, U)]</p>

<p>含义 <br>
<strong>flatMapValues</strong> 和<strong>mapValues</strong> 功能类似，每次以<strong>value</strong> 作为切分单位，切分方式和 <strong>flatMap</strong> 类似，默认以单个字符作为切分单位</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> a = sc.parallelize(List(<span class="hljs-string">"dog"</span>, <span class="hljs-string">"tiger"</span>, <span class="hljs-string">"lion"</span>, <span class="hljs-string">"cat"</span>, <span class="hljs-string">"panther"</span>, <span class="hljs-string">"eagle"</span>), <span class="hljs-number">2</span>)
<span class="hljs-comment">//以数字作为key</span>
<span class="hljs-keyword">val</span> b = a.map(x =&gt; (x.length, x))
b.flatMapValues(<span class="hljs-string">"x"</span> + _ + <span class="hljs-string">"x"</span>).collect

res1: Array[(Int, Char)] = Array((<span class="hljs-number">3</span>,x), (<span class="hljs-number">3</span>,d), (<span class="hljs-number">3</span>,o), (<span class="hljs-number">3</span>,g), (<span class="hljs-number">3</span>,x), (<span class="hljs-number">5</span>,x), (<span class="hljs-number">5</span>,t), (<span class="hljs-number">5</span>,i), (<span class="hljs-number">5</span>,g), (<span class="hljs-number">5</span>,e), (<span class="hljs-number">5</span>,r), (<span class="hljs-number">5</span>,x), (<span class="hljs-number">4</span>,x), (<span class="hljs-number">4</span>,l), (<span class="hljs-number">4</span>,i), (<span class="hljs-number">4</span>,o), (<span class="hljs-number">4</span>,n), (<span class="hljs-number">4</span>,x), (<span class="hljs-number">3</span>,x), (<span class="hljs-number">3</span>,c), (<span class="hljs-number">3</span>,a), (<span class="hljs-number">3</span>,t), (<span class="hljs-number">3</span>,x), (<span class="hljs-number">7</span>,x), (<span class="hljs-number">7</span>,p), (<span class="hljs-number">7</span>,a), (<span class="hljs-number">7</span>,n), (<span class="hljs-number">7</span>,t), (<span class="hljs-number">7</span>,h), (<span class="hljs-number">7</span>,e), (<span class="hljs-number">7</span>,r), (<span class="hljs-number">7</span>,x), (<span class="hljs-number">5</span>,x), (<span class="hljs-number">5</span>,e), (<span class="hljs-number">5</span>,a), (<span class="hljs-number">5</span>,g), (<span class="hljs-number">5</span>,l), (<span class="hljs-number">5</span>,e), (<span class="hljs-number">5</span>,x))</code></pre>

<hr>

<h2 id="23fold-1"><strong>23、fold</strong></h2>

<p>原型  <br>
def fold(zeroValue: T)(op: (T, T) =&gt; T): T</p>

<p>含义 <br>
<strong>fold</strong> 是 <strong>aggregate</strong> 的简化版，他只能传递一个函数，分别作用于每个分区内部以及分区之间 </p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> a = sc.parallelize(List(<span class="hljs-number">1</span>,<span class="hljs-number">2</span>,<span class="hljs-number">3</span>), <span class="hljs-number">3</span>)
a.fold(<span class="hljs-number">1</span>)(_ + _)
res1: Int = <span class="hljs-number">6</span>

<span class="hljs-keyword">val</span> a = sc.parallelize(List(<span class="hljs-string">"c"</span>,<span class="hljs-string">"b"</span>,<span class="hljs-string">"abc"</span>), <span class="hljs-number">3</span>)
a.fold(<span class="hljs-string">"x"</span>)(_ + _)
res2: String = xxcxbxabc
</code></pre>

<hr>

<h2 id="24foldbykey-pair"><strong>24、foldByKey [Pair]</strong></h2>

<p>原型  <br>
def foldByKey(zeroValue: V)(func: (V, V) =&gt; V): RDD[(K, V)]</p>

<p>含义 <br>
<strong>foldByKey</strong> 和 <strong>fold</strong> 类似，他只能传递一个函数，分别作用于每个分区内部以及分区之间 。同时是 <strong>aggregateByKey</strong> 的一个简化版，功能和他类似</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> a = sc.parallelize(List(<span class="hljs-string">"dog"</span>, <span class="hljs-string">"tiger"</span>, <span class="hljs-string">"lion"</span>, <span class="hljs-string">"cat"</span>, <span class="hljs-string">"panther"</span>, <span class="hljs-string">"eagle"</span>), <span class="hljs-number">2</span>)
<span class="hljs-keyword">val</span> b = a.map(x =&gt; (x.length, x))
<span class="hljs-comment">//每次按照key来聚合，分区内和分区之间的聚合函数相同</span>
b.foldByKey(<span class="hljs-string">""</span>)(_ + _).collect
res1: Array[(Int, String)] = Array((<span class="hljs-number">4</span>,lion), (<span class="hljs-number">3</span>,dogcat), (<span class="hljs-number">7</span>,panther), (<span class="hljs-number">5</span>,tigereagle))
</code></pre>

<hr>



<h2 id="25foreach"><strong>25、foreach</strong></h2>

<p>原型  <br>
def foreach(f: T =&gt; Unit)</p>

<p>含义 <br>
<strong>foreach</strong> 对RDD中的每个元素执行一个特定的功能，这个元素可以是元组，也可以是单个元素。也就是对RDD中每一项进行操作</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-comment">//这个目前没有实践，spark-shell中不支持</span>
<span class="hljs-keyword">val</span> c = sc.parallelize(List(<span class="hljs-string">"cat"</span>, <span class="hljs-string">"dog"</span>, <span class="hljs-string">"tiger"</span>, <span class="hljs-string">"lion"</span>, <span class="hljs-string">"gnu"</span>, <span class="hljs-string">"spider"</span>), <span class="hljs-number">3</span>)
c.foreach(x =&gt; println(x + <span class="hljs-string">"s are yummy"</span>))
<span class="hljs-comment">//通常用于下一步操作，可以将其赋值给变量</span>
</code></pre>

<hr>

<h2 id="26foreachpartition"><strong>26、foreachPartition</strong></h2>

<p>原型  <br>
def foreachPartition(f: Iterator[T] =&gt; Unit)</p>

<p>含义 <br>
<strong>foreachPartition</strong> 对RDD中的每个分区作为一个整体，每次操作的是一个分区</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-comment">//这个目前没有实践，spark-shell中不支持</span>
<span class="hljs-keyword">val</span> b = sc.parallelize(List(<span class="hljs-number">1</span>, <span class="hljs-number">2</span>, <span class="hljs-number">3</span>, <span class="hljs-number">4</span>, <span class="hljs-number">5</span>, <span class="hljs-number">6</span>, <span class="hljs-number">7</span>, <span class="hljs-number">8</span>, <span class="hljs-number">9</span>), <span class="hljs-number">3</span>)
<span class="hljs-comment">//x 表示一个分区，每次对一个分区进行操作</span>
b.foreachPartition(x =&gt; println(x.reduce(_ + _)))
<span class="hljs-comment">//通常用于下一步操作，可以将其赋值给变量</span>
</code></pre>

<hr>

<h2 id="27fullouterjoin-pair"><strong>27、fullOuterJoin [Pair]</strong></h2>

<p>原型  <br>
def fullOuterJoin[W](other: RDD[(K, W)], numPartitions: Int): RDD[(K, (Option[V], Option[W]))]</p>

<p>含义 <br>
<strong>fullOuterJoin</strong> 类似于数据库中的外连接，一共有 n×m 行，每个元素都要出现。按照key进行分组。</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> pairRDD1 = sc.parallelize(List( (<span class="hljs-string">"cat"</span>,<span class="hljs-number">2</span>), (<span class="hljs-string">"cat"</span>, <span class="hljs-number">5</span>), (<span class="hljs-string">"book"</span>, <span class="hljs-number">4</span>),(<span class="hljs-string">"gnu"</span>, <span class="hljs-number">12</span>)))
<span class="hljs-keyword">val</span> pairRDD2 = sc.parallelize(List( (<span class="hljs-string">"cat"</span>,<span class="hljs-number">2</span>), (<span class="hljs-string">"gnu"</span>, <span class="hljs-number">5</span>), (<span class="hljs-string">"mouse"</span>, <span class="hljs-number">4</span>),(<span class="hljs-string">"cat"</span>, <span class="hljs-number">12</span>)))
pairRDD1.fullOuterJoin(pairRDD2).collect
<span class="hljs-comment">//pairRDD1中的每个元素都会于 m 个 pairRDD2 个元素连接，形成一个 n×m 行的数据</span>
res0: Array[(String, (Option[Int], Option[Int]))] = Array((gnu,(Some(<span class="hljs-number">12</span>),Some(<span class="hljs-number">5</span>))), (cat,(Some(<span class="hljs-number">2</span>),Some(<span class="hljs-number">12</span>))), (cat,(Some(<span class="hljs-number">2</span>),Some(<span class="hljs-number">2</span>))), (cat,(Some(<span class="hljs-number">5</span>),Some(<span class="hljs-number">12</span>))), (cat,(Some(<span class="hljs-number">5</span>),Some(<span class="hljs-number">2</span>))), (book,(Some(<span class="hljs-number">4</span>),None)), (mouse,(None,Some(<span class="hljs-number">4</span>))))
</code></pre>

<hr>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>