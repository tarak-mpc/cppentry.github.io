---
layout:     post
title:      Spark RDD API 参考示例（六）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，欢迎转载。					https://blog.csdn.net/u010472512/article/details/72972189				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>本文参考<a href="http://homepage.cs.latrobe.edu.au/zhe/ZhenHeSparkRDDAPIExamples.html" rel="nofollow" target="_blank">Zhen He</a></p>



<h2 id="69take"><strong>69、take</strong></h2>

<p>原型  <br>
def take(num: Int): Array[T]</p>

<p>含义 <br>
<strong>take</strong> 提取RDD中元素的前几个，这几个是没有排序的，不需要排序，但是这个底部代码实现起来非常困难，因为他们分布在不同的分区</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> a = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">10</span>,<span class="hljs-number">2</span>)
a.take(<span class="hljs-number">2</span>)
res1: Array[Int] = Array(<span class="hljs-number">1</span>, <span class="hljs-number">2</span>)

<span class="hljs-keyword">val</span> b = sc.parallelize(List(<span class="hljs-string">"dog"</span>, <span class="hljs-string">"cat"</span>, <span class="hljs-string">"ape"</span>, <span class="hljs-string">"salmon"</span>, <span class="hljs-string">"gnu"</span>), <span class="hljs-number">2</span>)
b.take(<span class="hljs-number">2</span>)
res2: Array[String] = Array(dog, cat)
</code></pre>

<hr>

<h2 id="70takeordered"><strong>70、takeOrdered</strong></h2>

<p>原型  <br>
def takeOrdered(num: Int)(implicit ord: Ordering[T]): Array[T]</p>

<p>含义 <br>
<strong>takeOrdered</strong> 先将RDD中的数据进行排序，然后在取出来指定个数的数据，用户可以自定义排序方法。</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-comment">//先对RDD中的数据排序，然后再取出相应的数据</span>
<span class="hljs-keyword">val</span> b = sc.parallelize(List(<span class="hljs-string">"dog"</span>, <span class="hljs-string">"cat"</span>, <span class="hljs-string">"ape"</span>, <span class="hljs-string">"salmon"</span>, <span class="hljs-string">"gnu"</span>), <span class="hljs-number">2</span>)
b.takeOrdered(<span class="hljs-number">2</span>)
res19: Array[String] = Array(ape, cat)
</code></pre>

<hr>

<h2 id="71takesample"><strong>71、takeSample</strong></h2>

<p>原型  <br>
def takeSample(withReplacement: Boolean, num: Int, seed: Int): Array[T]</p>

<p>含义 <br>
<strong>takeSample</strong> 基本功能是随机返回用户数量的元素。与sample不同的是，(1) takeSample 返回的是确定数量的值，而sample返回的数的个数不确定。(2) takeSample返回的是一个数组，而sample返回的是一个RDD。</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> x = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">100</span>, <span class="hljs-number">3</span>)
<span class="hljs-comment">//表示 可以重复  拿10个数据 初始化随机种子为 1</span>
x.takeSample(<span class="hljs-keyword">true</span>, <span class="hljs-number">10</span>, <span class="hljs-number">1</span>)
res1: Array[Int] = Array(<span class="hljs-number">75</span>, <span class="hljs-number">17</span>, <span class="hljs-number">80</span>, <span class="hljs-number">48</span>, <span class="hljs-number">71</span>, <span class="hljs-number">12</span>, <span class="hljs-number">34</span>, <span class="hljs-number">3</span>, <span class="hljs-number">20</span>, <span class="hljs-number">17</span>)
</code></pre>

<hr>

<h2 id="72todebugstring"><strong>72、toDebugString</strong></h2>

<p>原型  <br>
def toDebugString: String</p>

<p>含义 <br>
<strong>toDebugString</strong> 获取RDD的依赖及其Debug信息</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs ">
<span class="hljs-keyword">val</span> a = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">9</span>, <span class="hljs-number">3</span>)
<span class="hljs-keyword">val</span> b = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">3</span>, <span class="hljs-number">3</span>)
<span class="hljs-keyword">val</span> c = a.subtract(b)
c.toDebugString
<span class="hljs-comment">//返回的详细debug信息</span>
res1: String =
(<span class="hljs-number">3</span>) MapPartitionsRDD[<span class="hljs-number">289</span>] at subtract at &lt;console&gt;:<span class="hljs-number">34</span> []
 |  SubtractedRDD[<span class="hljs-number">288</span>] at subtract at &lt;console&gt;:<span class="hljs-number">34</span> []
 +-(<span class="hljs-number">3</span>) MapPartitionsRDD[<span class="hljs-number">286</span>] at subtract at &lt;console&gt;:<span class="hljs-number">34</span> []
 |  |  ParallelCollectionRDD[<span class="hljs-number">284</span>] at parallelize at &lt;console&gt;:<span class="hljs-number">30</span> []
 +-(<span class="hljs-number">3</span>) MapPartitionsRDD[<span class="hljs-number">287</span>] at subtract at &lt;console&gt;:<span class="hljs-number">34</span> []
    |  ParallelCollectionRDD[<span class="hljs-number">285</span>] at parallelize at &lt;console&gt;:<span class="hljs-number">30</span> []

</code></pre>

<hr>

<h2 id="73top"><strong>73、top</strong></h2>

<p>原型  <br>
ddef top(num: Int)(implicit ord: Ordering[T]): Array[T]</p>

<p>含义 <br>
<strong>top</strong> 获取RDD按照降序排序后，获取前几个 <br>
示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> c = sc.parallelize(Array(<span class="hljs-number">6</span>, <span class="hljs-number">9</span>, <span class="hljs-number">4</span>, <span class="hljs-number">7</span>, <span class="hljs-number">5</span>, <span class="hljs-number">8</span>), <span class="hljs-number">2</span>)
<span class="hljs-comment">//指定获取的数量</span>
c.top(<span class="hljs-number">2</span>)
res1: Array[Int] = Array(<span class="hljs-number">9</span>, <span class="hljs-number">8</span>)</code></pre>

<hr>

<h2 id="74tostring"><strong>74、toString</strong></h2>

<p>原型  <br>
override def toString: String</p>

<p>含义 <br>
<strong>toString</strong> 获取RDD人性化的描述信息 <br>
示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> randRDD = sc.parallelize(List( (<span class="hljs-number">7</span>,<span class="hljs-string">"cat"</span>), (<span class="hljs-number">6</span>, <span class="hljs-string">"mouse"</span>),(<span class="hljs-number">7</span>, <span class="hljs-string">"cup"</span>), (<span class="hljs-number">6</span>, <span class="hljs-string">"book"</span>), (<span class="hljs-number">7</span>, <span class="hljs-string">"tv"</span>), (<span class="hljs-number">6</span>, <span class="hljs-string">"screen"</span>), (<span class="hljs-number">7</span>, <span class="hljs-string">"heater"</span>)))
<span class="hljs-keyword">val</span> sortedRDD = randRDD.sortByKey()
sortedRDD.toString
<span class="hljs-comment">//用可读文本描述来RDD的生成过程</span>
res1: String = ShuffledRDD[<span class="hljs-number">88</span>] at sortByKey at &lt;console&gt;:<span class="hljs-number">23</span></code></pre>

<hr>

<h2 id="75treeaggregate"><strong>75、treeAggregate</strong></h2>

<p>原型  <br>
def treeAggregate[U](zeroValue: U)(seqOp: (U, T) ⇒ U, combOp: (U, U) ⇒ U, depth: Int = 2)(implicit arg0: ClassTag[U]): U</p>

<p>含义 <br>
<strong>treeAggregate</strong> 和<strong>aggregateByKey</strong> 非常类似，不会在每个分区之间再应用初始值。不同的是，他可以指定每次聚合元素的个数，这样能够减少聚合的次数，减少计算，默认聚合的是两个。</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-comment">//使用默认的聚合深度</span>
<span class="hljs-keyword">val</span> z = sc.parallelize(List(<span class="hljs-number">1</span>,<span class="hljs-number">2</span>,<span class="hljs-number">3</span>,<span class="hljs-number">4</span>,<span class="hljs-number">5</span>,<span class="hljs-number">6</span>), <span class="hljs-number">2</span>)
z.treeAggregate(<span class="hljs-number">0</span>)(math.max(_, _), _ + _)
res1: Int = <span class="hljs-number">9</span>

<span class="hljs-comment">//指定聚合深度和初始值，初始值只会在每个分区内部使用，不会在分区之间使用</span>
z.treeAggregate(<span class="hljs-number">5</span>)(math.max(_, _), _ + _，<span class="hljs-number">4</span>)
res2: Int = <span class="hljs-number">11</span>
</code></pre>

<hr>

<h2 id="76treereduce"><strong>76、treeReduce</strong></h2>

<p>原型  <br>
def  treeReduce(f: (T, T) ⇒ T, depth: Int = 2): T</p>

<p>含义 <br>
<strong>treeReduce</strong> 和<strong>reduce</strong> 非常类似。不同的是，他可以指定每次聚合元素的个数，这样能够减少聚合的次数，减少计算，默认聚合的是两个。</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> z = sc.parallelize(List(<span class="hljs-number">1</span>,<span class="hljs-number">2</span>,<span class="hljs-number">3</span>,<span class="hljs-number">4</span>,<span class="hljs-number">5</span>,<span class="hljs-number">6</span>), <span class="hljs-number">2</span>)
z.treeReduce(_+_,<span class="hljs-number">3</span>)
res1: Int = <span class="hljs-number">21</span>
</code></pre>

<hr>

<h2 id="76union"><strong>76、union, ++</strong></h2>

<p>原型  <br>
def ++(other: RDD[T]): RDD[T] <br>
def union(other: RDD[T]): RDD[T]</p>

<p>含义 <br>
<strong>union</strong> 和集合中相加很类似，但是他们之间允许出现重复数据，如果不希望出现重复数据可以使用distinct</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> a = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">3</span>, <span class="hljs-number">1</span>)
<span class="hljs-keyword">val</span> b = sc.parallelize(<span class="hljs-number">2</span> to <span class="hljs-number">7</span>, <span class="hljs-number">1</span>)
(a ++ b).collect
res0: Array[Int] = Array[Int] = Array(<span class="hljs-number">1</span>, <span class="hljs-number">2</span>, <span class="hljs-number">3</span>, <span class="hljs-number">2</span>, <span class="hljs-number">3</span>, <span class="hljs-number">4</span>, <span class="hljs-number">5</span>, <span class="hljs-number">6</span>, <span class="hljs-number">7</span>)
</code></pre>

<hr>

<h2 id="77unpersist"><strong>77、unpersist</strong></h2>

<p>原型  <br>
def unpersist(blocking: Boolean = true): RDD[T]</p>

<p>含义 <br>
<strong>unpersist</strong> 撤销已经实现的RDD的内容，但是产生的RDD编号不会消失，如果再次使用这个编号，就会将消失的数据复原</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-comment">//撤销已经生成的RDD数据</span>
<span class="hljs-keyword">val</span> y = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">10</span>, <span class="hljs-number">10</span>)
<span class="hljs-keyword">val</span> z = (y++y)
z.collect
z.unpersist(<span class="hljs-keyword">true</span>)
</code></pre>

<hr>

<h2 id="78values"><strong>78、values</strong></h2>

<p>原型  <br>
def values: RDD[V]</p>

<p>含义 <br>
<strong>values</strong> 获取RDD元组中每一个key-value元素的value值，和keys对应</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> a = sc.parallelize(List(<span class="hljs-string">"dog"</span>, <span class="hljs-string">"tiger"</span>, <span class="hljs-string">"lion"</span>, <span class="hljs-string">"cat"</span>, <span class="hljs-string">"panther"</span>, <span class="hljs-string">"eagle"</span>), <span class="hljs-number">2</span>)
<span class="hljs-keyword">val</span> b = a.map(x =&gt; (x.length, x))
b.values.collect
res3: Array[String] = Array(dog, tiger, lion, cat, panther, eagle)
</code></pre>

<hr>

<h2 id="79variance-double-samplevariance-double"><strong>79、variance [Double], sampleVariance [Double]</strong></h2>

<p>原型  <br>
def variance(): Double <br>
def sampleVariance(): Double</p>

<p>含义 <br>
<strong>variance</strong> 求RDD中元素的方差，适用于小量数据  divide N <br>
<strong>sampleVariance</strong> 求RDD中元素的方差，适用于大量数据  divide N-1</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-comment">//统计精度不同</span>
<span class="hljs-keyword">val</span> x = sc.parallelize(List(<span class="hljs-number">1.0</span>, <span class="hljs-number">2.0</span>, <span class="hljs-number">3.0</span>, <span class="hljs-number">5.0</span>, <span class="hljs-number">20.0</span>, <span class="hljs-number">19.02</span>, <span class="hljs-number">19.29</span>, <span class="hljs-number">11.09</span>, <span class="hljs-number">21.0</span>), <span class="hljs-number">2</span>)
x.variance
res14: Double = <span class="hljs-number">66.04584444444443</span>

x.sampleVariance
res13: Double = <span class="hljs-number">74.30157499999999</span></code></pre>

<hr>

<h2 id="79zip"><strong>79、zip</strong></h2>

<p>原型  <br>
def zip[U: ClassTag](other: RDD[U]): RDD[(T, U)]</p>

<p>含义 <br>
<strong>zip</strong> 将两个RDD联合起来，形成一个key-value类型的RDD</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> x = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">10</span>)
<span class="hljs-keyword">val</span> y = sc.parallelize(<span class="hljs-number">21</span> to <span class="hljs-number">30</span>)
<span class="hljs-comment">//x和y的长度必须相同，x作为key，y作为value</span>
x.zip(y).collect</code></pre>

<hr>



<h2 id="80zipparititions"><strong>80、zipParititions</strong></h2>

<p>原型  <br>
def zipPartitions[B: ClassTag, V: ClassTag](rdd2: RDD[B])(f: (Iterator[T], Iterator[B]) =&gt; Iterator[V]): RDD[V]</p>

<p>含义 <br>
<strong>zipParititions</strong> 和<strong>zip</strong>的功能类似，但是用户可以自定义实现多个RDD之间进行<strong>zip</strong>，但是用户必须自定义实现函数</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> a = sc.parallelize(<span class="hljs-number">0</span> to <span class="hljs-number">9</span>, <span class="hljs-number">3</span>)
<span class="hljs-keyword">val</span> b = sc.parallelize(<span class="hljs-number">10</span> to <span class="hljs-number">19</span>, <span class="hljs-number">3</span>)
<span class="hljs-keyword">val</span> c = sc.parallelize(<span class="hljs-number">100</span> to <span class="hljs-number">109</span>, <span class="hljs-number">3</span>)
<span class="hljs-keyword">def</span> myfunc(aiter: Iterator[Int], biter: Iterator[Int], citer: Iterator[Int]): Iterator[String] =
{
  <span class="hljs-keyword">var</span> res = List[String]()
  <span class="hljs-keyword">while</span> (aiter.hasNext &amp;&amp; biter.hasNext &amp;&amp; citer.hasNext)
  {
    <span class="hljs-keyword">val</span> x = aiter.next + <span class="hljs-string">" "</span> + biter.next + <span class="hljs-string">" "</span> + citer.next
    res ::= x
  }
  res.iterator
}
a.zipPartitions(b, c)(myfunc).collect
res0: Array[String] = Array(<span class="hljs-number">2</span> <span class="hljs-number">12</span> <span class="hljs-number">102</span>, <span class="hljs-number">1</span> <span class="hljs-number">11</span> <span class="hljs-number">101</span>, <span class="hljs-number">0</span> <span class="hljs-number">10</span> <span class="hljs-number">100</span>, <span class="hljs-number">5</span> <span class="hljs-number">15</span> <span class="hljs-number">105</span>, <span class="hljs-number">4</span> <span class="hljs-number">14</span> <span class="hljs-number">104</span>, <span class="hljs-number">3</span> <span class="hljs-number">13</span> <span class="hljs-number">103</span>, <span class="hljs-number">9</span> <span class="hljs-number">19</span> <span class="hljs-number">109</span>, <span class="hljs-number">8</span> <span class="hljs-number">18</span> <span class="hljs-number">108</span>, <span class="hljs-number">7</span> <span class="hljs-number">17</span> <span class="hljs-number">107</span>, <span class="hljs-number">6</span> <span class="hljs-number">16</span> <span class="hljs-number">106</span>)</code></pre>

<hr>

<h2 id="81zipwithindex"><strong>81、zipWithIndex</strong></h2>

<p>原型  <br>
def zipWithIndex(): RDD[(T, Long)]</p>

<p>含义 <br>
<strong>zipWithIndex</strong> 和<strong>zip</strong>的功能类似，是<strong>zip</strong> 的简化版，他使用前者所在的位置作为其<strong>value</strong>，不需要用户再定义一个RDD</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> z = sc.parallelize(Array(<span class="hljs-string">"A"</span>, <span class="hljs-string">"B"</span>, <span class="hljs-string">"C"</span>, <span class="hljs-string">"D"</span>))
<span class="hljs-keyword">val</span> r = z.zipWithIndex
res110: Array[(String, Long)] = Array((A,<span class="hljs-number">0</span>), (B,<span class="hljs-number">1</span>), (C,<span class="hljs-number">2</span>), (D,<span class="hljs-number">3</span>))

<span class="hljs-comment">//由于RDD中有多个分区，所以不能完全确定其序号大小，存在并行关系</span>
<span class="hljs-keyword">val</span> z = sc.parallelize(<span class="hljs-number">100</span> to <span class="hljs-number">120</span>, <span class="hljs-number">5</span>)
<span class="hljs-keyword">val</span> r = z.zipWithIndex
r.collect
res11: Array[(Int, Long)] = Array((<span class="hljs-number">100</span>,<span class="hljs-number">0</span>), (<span class="hljs-number">101</span>,<span class="hljs-number">1</span>), (<span class="hljs-number">102</span>,<span class="hljs-number">2</span>), (<span class="hljs-number">103</span>,<span class="hljs-number">3</span>), (<span class="hljs-number">104</span>,<span class="hljs-number">4</span>), (<span class="hljs-number">105</span>,<span class="hljs-number">5</span>), (<span class="hljs-number">106</span>,<span class="hljs-number">6</span>), (<span class="hljs-number">107</span>,<span class="hljs-number">7</span>), (<span class="hljs-number">108</span>,<span class="hljs-number">8</span>), (<span class="hljs-number">109</span>,<span class="hljs-number">9</span>), (<span class="hljs-number">110</span>,<span class="hljs-number">10</span>), (<span class="hljs-number">111</span>,<span class="hljs-number">11</span>), (<span class="hljs-number">112</span>,<span class="hljs-number">12</span>), (<span class="hljs-number">113</span>,<span class="hljs-number">13</span>), (<span class="hljs-number">114</span>,<span class="hljs-number">14</span>), (<span class="hljs-number">115</span>,<span class="hljs-number">15</span>), (<span class="hljs-number">116</span>,<span class="hljs-number">16</span>), (<span class="hljs-number">117</span>,<span class="hljs-number">17</span>), (<span class="hljs-number">118</span>,<span class="hljs-number">18</span>), (<span class="hljs-number">119</span>,<span class="hljs-number">19</span>), (<span class="hljs-number">120</span>,<span class="hljs-number">20</span>))</code></pre>

<hr>

<h2 id="82zipwithuniqueid"><strong>82、zipWithUniqueId</strong></h2>

<p>原型  <br>
def zipWithUniqueId(): RDD[(T, Long)]</p>

<p>含义 <br>
<strong>zipWithUniqueId</strong> 和<strong>zip</strong>的功能类似，是<strong>zip</strong> 的简化版，他使用一个系统分配的唯一Id作为其value ，这与序号并不对应，但是这个更加安全，不同分区之间也不会导致相同的value</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> z = sc.parallelize(<span class="hljs-number">100</span> to <span class="hljs-number">120</span>, <span class="hljs-number">5</span>)
<span class="hljs-keyword">val</span> r = z.zipWithUniqueId
r.collect

res1: Array[(Int, Long)] = Array((<span class="hljs-number">100</span>,<span class="hljs-number">0</span>), (<span class="hljs-number">101</span>,<span class="hljs-number">5</span>), (<span class="hljs-number">102</span>,<span class="hljs-number">10</span>), (<span class="hljs-number">103</span>,<span class="hljs-number">15</span>), (<span class="hljs-number">104</span>,<span class="hljs-number">1</span>), (<span class="hljs-number">105</span>,<span class="hljs-number">6</span>), (<span class="hljs-number">106</span>,<span class="hljs-number">11</span>), (<span class="hljs-number">107</span>,<span class="hljs-number">16</span>), (<span class="hljs-number">108</span>,<span class="hljs-number">2</span>), (<span class="hljs-number">109</span>,<span class="hljs-number">7</span>), (<span class="hljs-number">110</span>,<span class="hljs-number">12</span>), (<span class="hljs-number">111</span>,<span class="hljs-number">17</span>), (<span class="hljs-number">112</span>,<span class="hljs-number">3</span>), (<span class="hljs-number">113</span>,<span class="hljs-number">8</span>), (<span class="hljs-number">114</span>,<span class="hljs-number">13</span>), (<span class="hljs-number">115</span>,<span class="hljs-number">18</span>), (<span class="hljs-number">116</span>,<span class="hljs-number">4</span>), (<span class="hljs-number">117</span>,<span class="hljs-number">9</span>), (<span class="hljs-number">118</span>,<span class="hljs-number">14</span>), (<span class="hljs-number">119</span>,<span class="hljs-number">19</span>), (<span class="hljs-number">120</span>,<span class="hljs-number">24</span>))</code></pre>

<hr>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>