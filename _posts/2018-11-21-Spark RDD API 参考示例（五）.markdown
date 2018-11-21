---
layout:     post
title:      Spark RDD API 参考示例（五）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，欢迎转载。					https://blog.csdn.net/u010472512/article/details/72967964				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>本文参考<a href="http://homepage.cs.latrobe.edu.au/zhe/ZhenHeSparkRDDAPIExamples.html" rel="nofollow" target="_blank">Zhen He</a></p>



<h2 id="57sample"><strong>57、sample</strong></h2>

<p>原型  <br>
def sample(withReplacement: Boolean, fraction: Double, seed: Int): RDD[T]</p>

<p>含义 <br>
<strong>sample</strong> 随机挑选RDD中的一部分产生新的RDD，withReplacement表示是否允许重复挑选，fraction表示挑选比例，seed表示随机初始化种子</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> a = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">20</span>, <span class="hljs-number">3</span>)
<span class="hljs-comment">//不允许重复挑选，也就是不能有相同的数据</span>
a.sample(<span class="hljs-keyword">false</span>,<span class="hljs-number">0.3</span>,<span class="hljs-number">0</span>).collect
res1: Array[Int] = Array(<span class="hljs-number">3</span>, <span class="hljs-number">10</span>, <span class="hljs-number">13</span>, <span class="hljs-number">15</span>, <span class="hljs-number">16</span>, <span class="hljs-number">19</span>, <span class="hljs-number">20</span>)
<span class="hljs-comment">//可以重复挑选</span>
a.sample(<span class="hljs-keyword">true</span>,<span class="hljs-number">0.3</span>,<span class="hljs-number">1</span>).collect
res2: Array[Int] = Array(<span class="hljs-number">4</span>, <span class="hljs-number">6</span>, <span class="hljs-number">7</span>, <span class="hljs-number">7</span>, <span class="hljs-number">12</span>, <span class="hljs-number">14</span>)
</code></pre>

<hr>

<h2 id="58samplebykey-pair"><strong>58、sampleByKey [Pair]</strong></h2>

<p>原型  <br>
def sampleByKey(withReplacement: Boolean, fractions: Map[K, Double], seed: Long = Utils.random.nextLong): RDD[(K, V)]</p>

<p>含义 <br>
<strong>sampleByKey</strong> 先将每一个key相同的聚合在一起，然后在相同的key之间按照指定的权重筛选，用户需要自定义一个Map来确定每个key筛选的比例。</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> randRDD = sc.parallelize(List( (<span class="hljs-number">7</span>,<span class="hljs-string">"cat"</span>), (<span class="hljs-number">6</span>, <span class="hljs-string">"mouse"</span>),(<span class="hljs-number">7</span>, <span class="hljs-string">"cup"</span>), (<span class="hljs-number">6</span>, <span class="hljs-string">"book"</span>), (<span class="hljs-number">7</span>, <span class="hljs-string">"tv"</span>), (<span class="hljs-number">6</span>, <span class="hljs-string">"screen"</span>), (<span class="hljs-number">7</span>, <span class="hljs-string">"heater"</span>)))
<span class="hljs-comment">//定制自己想要筛选的key，然后放到sampleByKey中去</span>
<span class="hljs-keyword">val</span> sampleMap = List((<span class="hljs-number">7</span>, <span class="hljs-number">0.4</span>), (<span class="hljs-number">6</span>, <span class="hljs-number">0.6</span>)).toMap
randRDD.sampleByKey(<span class="hljs-keyword">false</span>, sampleMap,<span class="hljs-number">42</span>).collect

res1: Array[(Int, String)] = Array((<span class="hljs-number">6</span>,book), (<span class="hljs-number">7</span>,tv), (<span class="hljs-number">7</span>,heater))
</code></pre>

<hr>



<h2 id="59saveasobjectfile"><strong>59、saveAsObjectFile</strong></h2>

<p>原型  <br>
def saveAsObjectFile(path: String)</p>

<p>含义 <br>
<strong>saveAsObjectFile</strong> 把RDD数据保存成二进制格式</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> x = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">10</span>, <span class="hljs-number">3</span>)
<span class="hljs-comment">//保存成二进制文件</span>
x.saveAsObjectFile(<span class="hljs-string">"objFile"</span>)
<span class="hljs-comment">//读取上面保存的而二进制文件，[Int]用于提示对象类型</span>
<span class="hljs-keyword">val</span> y = sc.objectFile[Int](<span class="hljs-string">"objFile"</span>)
y.collect
res1: Array[Int] =  Array[Int] = Array(<span class="hljs-number">1</span>, <span class="hljs-number">2</span>, <span class="hljs-number">3</span>, <span class="hljs-number">4</span>, <span class="hljs-number">5</span>, <span class="hljs-number">6</span>, <span class="hljs-number">7</span>, <span class="hljs-number">8</span>, <span class="hljs-number">9</span>, <span class="hljs-number">10</span>)
</code></pre>

<hr>

<h2 id="60saveassequencefile-seqfile"><strong>60、saveAsSequenceFile [SeqFile]</strong></h2>

<p>原型  <br>
def saveAsSequenceFile(path: String, codec: Option[Class[_ &lt;: CompressionCodec]] = None)</p>

<p>含义 <br>
<strong>saveAsSequenceFile</strong> 把RDD数据保存成Hadoop 序列文件</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-comment">//将这个数据保存到hdfs中去</span>
<span class="hljs-keyword">val</span> v = sc.parallelize(Array((<span class="hljs-string">"owl"</span>,<span class="hljs-number">3</span>), (<span class="hljs-string">"gnu"</span>,<span class="hljs-number">4</span>), (<span class="hljs-string">"dog"</span>,<span class="hljs-number">1</span>), (<span class="hljs-string">"cat"</span>,<span class="hljs-number">2</span>), (<span class="hljs-string">"ant"</span>,<span class="hljs-number">5</span>)), <span class="hljs-number">2</span>)
<span class="hljs-comment">//这个功能非常实用，可以直接操作hdfs文件</span>
v.saveAsSequenceFile(<span class="hljs-string">"hdfs://192.168.10.71:9000/wc2"</span>)
</code></pre>

<hr>

<h2 id="61saveastextfile"><strong>61、saveAsTextFile</strong></h2>

<p>原型  <br>
def saveAsTextFile(path: String) <br>
def saveAsTextFile(path: String, codec: Class[_ &lt;: CompressionCodec])</p>

<p>含义 <br>
<strong>saveAsTextFile</strong> 把RDD数据保存成文本文件</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> a = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">10000</span>, <span class="hljs-number">3</span>)
a.saveAsTextFile(<span class="hljs-string">"mydata_a"</span>)
<span class="hljs-comment">//保存时，还可以定制压缩格式，这样可以节省空间</span>
<span class="hljs-keyword">import</span> org.apache.hadoop.io.compress.GzipCodec
a.saveAsTextFile(<span class="hljs-string">"mydata_b"</span>, classOf[GzipCodec])
</code></pre>

<hr>



<h2 id="62stats-double"><strong>62、stats [Double]</strong></h2>

<p>原型  <br>
def stats(): StatCounter</p>

<p>含义 <br>
<strong>stats</strong> 统计学方法，同时计算RDD中的平均值，方差，标准差，最大值和最小值</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> z = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">10</span>,<span class="hljs-number">3</span>)
z.stats
<span class="hljs-comment">//同时计算统计值，</span>
res1: (count: <span class="hljs-number">10</span>, mean: <span class="hljs-number">5.500000</span>, stdev: <span class="hljs-number">2.872281</span>, max: <span class="hljs-number">10.000000</span>, min: <span class="hljs-number">1.000000</span>)
</code></pre>

<hr>

<h2 id="63sortby"><strong>63、sortBy</strong></h2>

<p>原型  <br>
def sortBy[K](f: (T) ⇒ K, ascending: Boolean = true, numPartitions: Int = this.partitions.size)(implicit ord: Ordering[K], ctag: ClassTag[K]): RDD[T]</p>

<p>含义 <br>
<strong>sortBy</strong> 用于排序，第一个参数用于指定将待排序的RDD元素转换成要排序的key，根据这个指定的key来排序，第二个参数如果为true表示升序，否则为降序</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-comment">//对于普通的单个元素，按照一般的排序</span>
<span class="hljs-keyword">val</span> y = sc.parallelize(Array(<span class="hljs-number">5</span>, <span class="hljs-number">7</span>, <span class="hljs-number">1</span>, <span class="hljs-number">3</span>, <span class="hljs-number">2</span>, <span class="hljs-number">1</span>))
<span class="hljs-comment">//true指定排序方式为升序</span>
y.sortBy(c =&gt; c, <span class="hljs-keyword">true</span>).collect
res1: Array[Int] = Array(<span class="hljs-number">1</span>, <span class="hljs-number">1</span>, <span class="hljs-number">2</span>, <span class="hljs-number">3</span>, <span class="hljs-number">5</span>, <span class="hljs-number">7</span>)

<span class="hljs-comment">//false表示按照降序排序</span>
y.sortBy(c =&gt; c, <span class="hljs-keyword">false</span>).collect
res2: Array[Int] = Array(<span class="hljs-number">7</span>, <span class="hljs-number">5</span>, <span class="hljs-number">3</span>, <span class="hljs-number">2</span>, <span class="hljs-number">1</span>, <span class="hljs-number">1</span>)

<span class="hljs-comment">//对于元组，则需要指定将元组映射到要排序的key上</span>
<span class="hljs-keyword">val</span> z = sc.parallelize(Array((<span class="hljs-string">"H"</span>, <span class="hljs-number">10</span>), (<span class="hljs-string">"A"</span>, <span class="hljs-number">26</span>), (<span class="hljs-string">"Z"</span>, <span class="hljs-number">1</span>), (<span class="hljs-string">"L"</span>, <span class="hljs-number">5</span>)))

<span class="hljs-comment">//此处表示按照每个元组的第一个值排序</span>
z.sortBy(c =&gt; c._1, <span class="hljs-keyword">true</span>).collect
res3: Array[(String, Int)] = Array((A,<span class="hljs-number">26</span>), (H,<span class="hljs-number">10</span>), (L,<span class="hljs-number">5</span>), (Z,<span class="hljs-number">1</span>))

<span class="hljs-comment">//此处表示按照每个元组的第二个值排序</span>
z.sortBy(c =&gt; c._2, <span class="hljs-keyword">true</span>).collect
res4: Array[(String, Int)] = Array((Z,<span class="hljs-number">1</span>), (L,<span class="hljs-number">5</span>), (H,<span class="hljs-number">10</span>), (A,<span class="hljs-number">26</span>))
</code></pre>

<hr>

<h2 id="64sortbykey-ordered"><strong>64、sortByKey [Ordered]</strong></h2>

<p>原型  <br>
def sortByKey(ascending: Boolean = true, numPartitions: Int = self.partitions.size): RDD[P]</p>

<p>含义 <br>
<strong>sortByKey</strong> 是<strong>sortBy</strong> 的一个简单应用，他使用的时候不需要用户指定排序key，而是直接排序。只能对key-value类型使用。</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> a = sc.parallelize(List(<span class="hljs-string">"dog"</span>, <span class="hljs-string">"cat"</span>, <span class="hljs-string">"owl"</span>, <span class="hljs-string">"gnu"</span>, <span class="hljs-string">"ant"</span>), <span class="hljs-number">2</span>)
<span class="hljs-keyword">val</span> b = sc.parallelize(<span class="hljs-number">1</span> to a.count.toInt, <span class="hljs-number">2</span>)
<span class="hljs-keyword">val</span> c = a.zip(b)
c.sortByKey(<span class="hljs-keyword">true</span>).collect
res1: Array[(String, Int)] = Array((ant,<span class="hljs-number">5</span>), (cat,<span class="hljs-number">2</span>), (dog,<span class="hljs-number">1</span>), (gnu,<span class="hljs-number">4</span>), (owl,<span class="hljs-number">3</span>))
</code></pre>

<hr>

<h2 id="65stdev-double-samplestdev-double"><strong>65、stdev [Double], sampleStdev [Double]</strong></h2>

<p>原型  <br>
def stdev(): Double <br>
def sampleStdev(): Double</p>

<p>含义 <br>
<strong>stdev</strong> 调用stats 函数，取得RDD中的标准差，divides by N <br>
<strong>sampleStdev</strong> 调用stats 函数，取得RDD中的标准差，divides by N-1，对于数据量很大的情况下适用</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-comment">//这里可以体现stdev和sampleStdev的区别</span>
<span class="hljs-keyword">val</span> d = sc.parallelize(List(<span class="hljs-number">0.0</span>, <span class="hljs-number">0.0</span>, <span class="hljs-number">1.0</span>), <span class="hljs-number">3</span>)
d.stdev
res14: Double = <span class="hljs-number">0.4714045207910317</span>
d.sampleStdev
res15: Double = <span class="hljs-number">0.5773502691896257</span></code></pre>

<hr>



<h2 id="66subtract"><strong>66、subtract</strong></h2>

<p>原型  <br>
def subtract(other: RDD[T]): RDD[T] <br>
def subtract(other: RDD[T], numPartitions: Int): RDD[T] <br>
def subtract(other: RDD[T], p: Partitioner): RDD[T]</p>

<p>含义 <br>
<strong>subtract</strong> 求两个集合的差，A-B </p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-comment">//和数学上的集合相减类似</span>
<span class="hljs-keyword">val</span> a = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">9</span>, <span class="hljs-number">3</span>)
<span class="hljs-keyword">val</span> b = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">3</span>, <span class="hljs-number">3</span>)
<span class="hljs-keyword">val</span> c = a.subtract(b)
c.collect
res1: Array[Int] = Array(<span class="hljs-number">6</span>, <span class="hljs-number">9</span>, <span class="hljs-number">4</span>, <span class="hljs-number">7</span>, <span class="hljs-number">5</span>, <span class="hljs-number">8</span>)</code></pre>

<hr>

<h2 id="67subtractbykey-pair"><strong>67、subtractByKey [Pair]</strong></h2>

<p>原型  <br>
def subtractByKey[W: ClassTag](other: RDD[(K, W)]): RDD[(K, V)] <br>
def subtractByKey[W: ClassTag](other: RDD[(K, W)], numPartitions: Int): RDD[(K, V)] <br>
def subtractByKey[W: ClassTag](other: RDD[(K, W)], p: Partitioner): RDD[(K, V)]</p>

<p>含义 <br>
<strong>subtractByKey</strong> 两个RDD先按照key分组，二者相减A-B，key相同，都会消除。如果A中没有，B中有，那么就会丢弃</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-comment">//丢弃A中不存在的，key相同的都会消除</span>
<span class="hljs-keyword">val</span> a = sc.parallelize(List(<span class="hljs-string">"dog"</span>, <span class="hljs-string">"tiger"</span>, <span class="hljs-string">"lion"</span>, <span class="hljs-string">"cat"</span>, <span class="hljs-string">"spider"</span>, <span class="hljs-string">"wolf"</span>), <span class="hljs-number">2</span>)
<span class="hljs-keyword">val</span> b = a.keyBy(_.length)
<span class="hljs-keyword">val</span> c = sc.parallelize(List(<span class="hljs-string">"ant"</span>, <span class="hljs-string">"falcon"</span>, <span class="hljs-string">"squid"</span>,<span class="hljs-string">"abcdefg"</span>), <span class="hljs-number">2</span>)
<span class="hljs-keyword">val</span> d = c.keyBy(_.length)
b.subtractByKey(d).collect

res15: Array[(Int, String)] = Array((<span class="hljs-number">4</span>,lion),(<span class="hljs-number">4</span>,wolf))</code></pre>

<hr>

<h2 id="68sum-double-sumapprox-double"><strong>68、sum [Double], sumApprox [Double]</strong></h2>

<p>原型  <br>
def sum(): Double <br>
def sumApprox(timeout: Long, confidence: Double = 0.95): PartialResult[BoundedDouble]</p>

<p>含义 <br>
<strong>sum</strong> 计算RDD中元素的总和，要求输入一个数字类型的RDD</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> x = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">10</span>)
x.sum
res1: Double = <span class="hljs-number">55.0</span></code></pre>

<hr>



<h2 id="68sum-double-sumapprox-double-1"><strong>68、sum [Double], sumApprox [Double]</strong></h2>

<p>原型  <br>
def sum(): Double <br>
def sumApprox(timeout: Long, confidence: Double = 0.95): PartialResult[BoundedDouble]</p>

<p>含义 <br>
<strong>sum</strong> 计算RDD中元素的总和，要求输入一个数字类型的RDD</p>

<p>示例</p>

<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> x = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">10</span>)
x.sum
res1: Double = <span class="hljs-number">55.0</span></code></pre>

<hr>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>