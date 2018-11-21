---
layout:     post
title:      Spark RDD API 参考示例（四）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，欢迎转载。					https://blog.csdn.net/u010472512/article/details/72935180				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>本文参考<a href="http://homepage.cs.latrobe.edu.au/zhe/ZhenHeSparkRDDAPIExamples.html" rel="nofollow" target="_blank">Zhen He</a></p>



<h2 id="42map"><strong>42、map</strong></h2>

<p>原型  <br>
def map[U: ClassTag](f: T =&gt; U): RDD[U]</p>

<p>含义 <br>
<strong>map</strong> 对RDD中的每一个<strong>item</strong> 应用一个函数，并且返回一个新的RDD</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> a = sc.parallelize(List(<span class="hljs-string">"dog"</span>, <span class="hljs-string">"salmon"</span>, <span class="hljs-string">"salmon"</span>, <span class="hljs-string">"rat"</span>, <span class="hljs-string">"elephant"</span>), <span class="hljs-number">3</span>)
<span class="hljs-comment">//对每一个 item 应用一个函数</span>
<span class="hljs-keyword">val</span> b = a.map(_.length)
<span class="hljs-keyword">val</span> c = a.zip(b)
c.collect
res0: Array[(String, Int)] = Array((dog,<span class="hljs-number">3</span>), (salmon,<span class="hljs-number">6</span>), (salmon,<span class="hljs-number">6</span>), (rat,<span class="hljs-number">3</span>), (elephant,<span class="hljs-number">8</span>))
</code></pre>

<hr>



<h2 id="43mappartitions"><strong>43、mapPartitions</strong></h2>

<p>原型  <br>
def mapPartitions[U: ClassTag](f: Iterator[T] =&gt; Iterator[U], preservesPartitioning: Boolean = false): RDD[U]</p>

<p>含义 <br>
<strong>mapPartitions</strong> 对RDD中每个<strong>Partition</strong> 调用一次，每个<strong>Partition</strong> 中的内容使用一个<strong>Iterator</strong>， 使用迭代器流作为输入，这种迭代流自动转换为RDD类型数据。这种流只会作用于每个分区内部。</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> a = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">9</span>, <span class="hljs-number">3</span>)
<span class="hljs-comment">//RDD分成三个分区(1,2,3)  (4,5,6)  (7,8,9)</span>
<span class="hljs-comment">//当iter取1和2时，可以得到(1,2) (2,3)</span>
<span class="hljs-comment">//当iter取3时</span>
<span class="hljs-comment">//此处采用的是泛型，所以函数名后面要myfunc[T]，如果是具体类型，就不需要</span>
<span class="hljs-keyword">def</span> myfunc[T](iter: Iterator[T]) : Iterator[(T, T)] = {
  <span class="hljs-keyword">var</span> res = List[(T, T)]()
  <span class="hljs-comment">//iter取3</span>
  <span class="hljs-keyword">var</span> pre = iter.next
  <span class="hljs-comment">//这个分区内容已经没有来，所以的流已经结束来。</span>
  <span class="hljs-comment">//所以(3,4)就不存在</span>
  <span class="hljs-keyword">while</span> (iter.hasNext)
  {
    <span class="hljs-keyword">val</span> cur = iter.next;
    res .::= (pre, cur)
    pre = cur;
  }
  res.iterator
}
<span class="hljs-comment">//作用于每一个分区，每个分区的内容以iterator流的格式输入，分区之间互不影响</span>
a.mapPartitions(myfunc).collect
res0: Array[(Int, Int)] = Array((<span class="hljs-number">2</span>,<span class="hljs-number">3</span>), (<span class="hljs-number">1</span>,<span class="hljs-number">2</span>), (<span class="hljs-number">5</span>,<span class="hljs-number">6</span>), (<span class="hljs-number">4</span>,<span class="hljs-number">5</span>), (<span class="hljs-number">8</span>,<span class="hljs-number">9</span>), (<span class="hljs-number">7</span>,<span class="hljs-number">8</span>))

<span class="hljs-comment">//对每个分区进行partition，这是于map的最大的区别</span>
<span class="hljs-keyword">val</span> x = sc.parallelize(List(<span class="hljs-number">1</span>, <span class="hljs-number">2</span>, <span class="hljs-number">3</span>, <span class="hljs-number">4</span>, <span class="hljs-number">5</span>, <span class="hljs-number">6</span>, <span class="hljs-number">7</span>, <span class="hljs-number">8</span>, <span class="hljs-number">9</span>,<span class="hljs-number">10</span>), <span class="hljs-number">3</span>)
<span class="hljs-comment">//此处采用的是具体类型，所以不需要在函数名后面加泛型</span>
<span class="hljs-keyword">def</span> myfunc(iter: Iterator[Int]) : Iterator[Int] = {
  <span class="hljs-keyword">var</span> res = List[Int]()
  <span class="hljs-keyword">while</span> (iter.hasNext) {
    <span class="hljs-keyword">val</span> cur = iter.next;
    res = res ::: List.fill(scala.util.Random.nextInt(<span class="hljs-number">5</span>))(cur)
  }
  res.iterator
}
x.mapPartitions(myfunc).collect
res2: Array(<span class="hljs-number">1</span>, <span class="hljs-number">2</span>, <span class="hljs-number">2</span>, <span class="hljs-number">2</span>, <span class="hljs-number">2</span>, <span class="hljs-number">3</span>, <span class="hljs-number">3</span>, <span class="hljs-number">4</span>, <span class="hljs-number">6</span>, <span class="hljs-number">7</span>, <span class="hljs-number">7</span>, <span class="hljs-number">8</span>, <span class="hljs-number">8</span>, <span class="hljs-number">9</span>, <span class="hljs-number">9</span>, <span class="hljs-number">9</span>, <span class="hljs-number">9</span>, <span class="hljs-number">10</span>, <span class="hljs-number">10</span>, <span class="hljs-number">10</span>)</code></pre>

<hr>



<h2 id="44mappartitionswithindex"><strong>44、mapPartitionsWithIndex</strong></h2>

<p>原型  <br>
def mapPartitionsWithIndex[U: ClassTag](f: (Int, Iterator[T]) =&gt; Iterator[U], preservesPartitioning: Boolean = false): RDD[U]</p>

<p>含义 <br>
<strong>mapPartitionsWithIndex</strong> 和<strong>mapPartitions</strong> 非常类似，但是他处理的数据带有两个部分，第一个是分区号，第二部分是该分区号内部的数据组成的<strong>Iterator</strong> 。 对RDD中每个<strong>Partition</strong> 调用一次，每个<strong>Partition</strong> 中的内容使用一个<strong>Iterator</strong>， 使用迭代器流作为输入，这种迭代流自动转换为RDD类型数据。这种流只会作用于每个分区内部。</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> x = sc.parallelize(List(<span class="hljs-number">1</span>,<span class="hljs-number">2</span>,<span class="hljs-number">3</span>,<span class="hljs-number">4</span>,<span class="hljs-number">5</span>,<span class="hljs-number">6</span>,<span class="hljs-number">7</span>,<span class="hljs-number">8</span>,<span class="hljs-number">9</span>,<span class="hljs-number">10</span>), <span class="hljs-number">3</span>)
<span class="hljs-comment">//mapPartitionsWithIndex处理函数，需要两个参数，第一个参数是分区号，第二个参数是该分区中数据组成的iterator</span>
<span class="hljs-comment">//处理函数的返回结果必须也是一个iterator</span>
<span class="hljs-keyword">def</span> myfunc(index: Int, iter: Iterator[Int]) : Iterator[String] = {
  iter.toList.map(x =&gt; index + <span class="hljs-string">","</span> + x).iterator
}
x.mapPartitionsWithIndex(myfunc).collect()
res1: Array[String] = Array(<span class="hljs-number">0</span>,<span class="hljs-number">1</span>, <span class="hljs-number">0</span>,<span class="hljs-number">2</span>, <span class="hljs-number">0</span>,<span class="hljs-number">3</span>, <span class="hljs-number">1</span>,<span class="hljs-number">4</span>, <span class="hljs-number">1</span>,<span class="hljs-number">5</span>, <span class="hljs-number">1</span>,<span class="hljs-number">6</span>, <span class="hljs-number">2</span>,<span class="hljs-number">7</span>, <span class="hljs-number">2</span>,<span class="hljs-number">8</span>, <span class="hljs-number">2</span>,<span class="hljs-number">9</span>, <span class="hljs-number">2</span>,<span class="hljs-number">10</span>)
</code></pre>

<hr>



<h2 id="45mapvalues-pair"><strong>45、mapValues [Pair]</strong></h2>

<p>原型  <br>
def mapValues[U](f: V =&gt; U): RDD[(K, U)]</p>

<p>含义 <br>
<strong>mapValues</strong> 用于处理key-value类型的RDD，每次处理一个key-value对，<strong>mapValues</strong> 处理的是key-value中的value，处理完value之后，就会返回一个key-value类型的数据，返回到新RDD中去 </p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> a = sc.parallelize(List(<span class="hljs-string">"dog"</span>, <span class="hljs-string">"tiger"</span>, <span class="hljs-string">"lion"</span>, <span class="hljs-string">"cat"</span>, <span class="hljs-string">"panther"</span>, <span class="hljs-string">"eagle"</span>), <span class="hljs-number">2</span>)
<span class="hljs-keyword">val</span> b = a.map(x =&gt; (x.length, x))
<span class="hljs-comment">//每次处理一个key-value中的value，然后附上key就行了</span>
b.mapValues(<span class="hljs-string">"x"</span> + _ + <span class="hljs-string">"x"</span>).collect
res1: Array((<span class="hljs-number">3</span>,xdogx), (<span class="hljs-number">5</span>,xtigerx), (<span class="hljs-number">4</span>,xlionx), (<span class="hljs-number">3</span>,xcatx), (<span class="hljs-number">7</span>,xpantherx), (<span class="hljs-number">5</span>,xeaglex))
</code></pre>

<hr>



<h2 id="46max"><strong>46、max</strong></h2>

<p>原型  <br>
def max()(implicit ord: Ordering[T]): T</p>

<p>含义 <br>
<strong>max</strong> 返回RDD中最大的元素，如果是元组，那么返回key最大的元素</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> y = sc.parallelize(<span class="hljs-number">10</span> to <span class="hljs-number">30</span>)
y.max
res1: Int = <span class="hljs-number">30</span>

<span class="hljs-keyword">val</span> a = sc.parallelize(List((<span class="hljs-number">10</span>, <span class="hljs-string">"dog"</span>), (<span class="hljs-number">3</span>, <span class="hljs-string">"tiger"</span>), (<span class="hljs-number">9</span>, <span class="hljs-string">"lion"</span>), (<span class="hljs-number">18</span>, <span class="hljs-string">"cat"</span>)))
a.max
res2: (Int, String) = (<span class="hljs-number">18</span>,cat)
</code></pre>

<hr>



<h2 id="47mean-double-meanapprox-double"><strong>47、mean [Double], meanApprox [Double]</strong></h2>

<p>原型  <br>
def mean(): Double <br>
def meanApprox(timeout: Long, confidence: Double = 0.95): PartialResult[BoundedDouble]</p>

<p>含义 <br>
<strong>mean</strong> 返回RDD中元素的均值，RDD中元素必须是数字类型的，可以是整数，也可以是浮点数</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-comment">//浮点数使用mean函数</span>
<span class="hljs-keyword">val</span> a = sc.parallelize(List(<span class="hljs-number">5.0</span>, <span class="hljs-number">2.0</span>, <span class="hljs-number">2.1</span>, <span class="hljs-number">7.4</span>, <span class="hljs-number">7.5</span>, <span class="hljs-number">7.1</span>, <span class="hljs-number">8.8</span>, <span class="hljs-number">10.0</span>, <span class="hljs-number">8.9</span>, <span class="hljs-number">5.5</span>), <span class="hljs-number">3</span>)
a.mean
res0: Double = <span class="hljs-number">6.4300000000000015</span>
<span class="hljs-comment">//整数使用mean函数</span>
<span class="hljs-keyword">val</span> b = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">10</span>)
b.mean
res1: Double = <span class="hljs-number">5.5</span>
</code></pre>

<hr>



<h2 id="48min"><strong>48、min</strong></h2>

<p>原型  <br>
def min()(implicit ord: Ordering[T]): T</p>

<p>含义 <br>
<strong>min</strong> 返回RDD中元素的最小值，如果是元组，那么返回key最小的元素</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> y = sc.parallelize(<span class="hljs-number">10</span> to <span class="hljs-number">30</span>)
y.min
res1: Int = <span class="hljs-number">10</span>
<span class="hljs-comment">//元组返回最小的key的元素</span>
<span class="hljs-keyword">val</span> a = sc.parallelize(List((<span class="hljs-number">10</span>, <span class="hljs-string">"dog"</span>), (<span class="hljs-number">3</span>, <span class="hljs-string">"tiger"</span>), (<span class="hljs-number">9</span>, <span class="hljs-string">"lion"</span>), (<span class="hljs-number">8</span>, <span class="hljs-string">"cat"</span>)))
a.min
res2: (Int, String) = (<span class="hljs-number">3</span>,tiger)
</code></pre>

<hr>



<h2 id="49name-setname"><strong>49、name, setName</strong></h2>

<p>原型  <br>
@transient var name: String <br>
def setName(_name: String)</p>

<p>含义 <br>
<strong>name</strong> 返回用户给RDD标记的名称</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> y = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">10</span>, <span class="hljs-number">10</span>)
<span class="hljs-comment">//查询RDD的名称</span>
y.name
res1: String = <span class="hljs-keyword">null</span>
<span class="hljs-comment">//用户设置RDD的名称</span>
y.setName(<span class="hljs-string">"Fancy RDD Name"</span>)
y.name
res2: String = Fancy RDD Name
</code></pre>

<hr>



<h2 id="50partitions"><strong>50、partitions</strong></h2>

<p>原型  <br>
final def partitions: Array[Partition]</p>

<p>含义 <br>
<strong>partitions</strong> 获得RDD的分区信息，并将其放入一个数组返回</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> a = sc.parallelize(List(<span class="hljs-number">5.0</span>, <span class="hljs-number">2.0</span>, <span class="hljs-number">2.1</span>, <span class="hljs-number">7.4</span>, <span class="hljs-number">7.5</span>, <span class="hljs-number">7.1</span>, <span class="hljs-number">8.8</span>, <span class="hljs-number">10.0</span>, <span class="hljs-number">8.9</span>, <span class="hljs-number">5.5</span>), <span class="hljs-number">3</span>)
a.partitions
<span class="hljs-comment">//返回三个分区信息，主要包括分区编号，分区类型</span>
res1: Array(
org.apache.spark.rdd.ParallelCollectionPartition@<span class="hljs-number">29</span>f6, org.apache.spark.rdd.ParallelCollectionPartition@<span class="hljs-number">29</span>f7, org.apache.spark.rdd.ParallelCollectionPartition@<span class="hljs-number">29</span>f8)
</code></pre>

<hr>



<h2 id="51persist-cache"><strong>51、persist, cache</strong></h2>

<p>原型  <br>
def cache(): RDD[T] <br>
def persist(): RDD[T] <br>
def persist(newLevel: StorageLevel): RDD[T]</p>

<p>含义 <br>
<strong>persist, cache</strong> 只是 <strong>persist(StorageLevel.MEMORY_ONLY)</strong> 的缩写，用于调整RDD的存储级别，一旦存储级别修改，就不能再次修改。</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> c = sc.parallelize(List(<span class="hljs-string">"Gnu"</span>, <span class="hljs-string">"Cat"</span>, <span class="hljs-string">"Rat"</span>, <span class="hljs-string">"Dog"</span>, <span class="hljs-string">"Gnu"</span>, <span class="hljs-string">"Rat"</span>), <span class="hljs-number">2</span>)
c.getStorageLevel
<span class="hljs-comment">//表示该RDD还没有设置存储级别，只是存储一份</span>
res0: org.apache.spark.storage.StorageLevel = StorageLevel(<span class="hljs-number">1</span> replicas)
<span class="hljs-comment">//cache 和persist功能一样，默认调整RDD的存储级别为 MEMORY</span>
c.cache
c.getStorageLevel
res2: org.apache.spark.storage.StorageLevel = StorageLevel(memory, deserialized, <span class="hljs-number">1</span> replicas)
</code></pre>

<hr>



<h2 id="52pipe"><strong>52、pipe</strong></h2>

<p>原型  <br>
def pipe(command: String): RDD[String] <br>
def pipe(command: String, env: Map[String, String]): RDD[String]</p>

<p>含义 <br>
<strong>pipe</strong> 对RDD中的每一个分区执行shell命令操作，</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-comment">//对每一个分区执行shell命令，并将结果返回到新的RDD中</span>
<span class="hljs-keyword">val</span> a = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">9</span>, <span class="hljs-number">3</span>)
a.pipe(<span class="hljs-string">"head -n 1"</span>).collect
res1: Array[String] = Array(<span class="hljs-number">1</span>, <span class="hljs-number">4</span>, <span class="hljs-number">7</span>)
</code></pre>

<hr>



<h2 id="53randomsplit"><strong>53、randomSplit</strong></h2>

<p>原型  <br>
def randomSplit(weights: Array[Double], seed: Long = Utils.random.nextLong): Array[RDD[T]]</p>

<p>含义 <br>
<strong>randomSplit</strong> 把RDD中数据，根据用户指定的权重，随机切分成多个小的RDD，小RDD中元素的个数由权重来确定。在一些特殊情况下，我们需要让随机产生的数据相同，就可以初始化相同的种子。</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> y = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">10</span>)
<span class="hljs-comment">//按照6:4的权重来切分数据，指定的初始化种子为 seed = 11L</span>
<span class="hljs-keyword">val</span> splits = y.randomSplit(Array(<span class="hljs-number">0.6</span>, <span class="hljs-number">0.4</span>), seed = <span class="hljs-number">11</span>L)
<span class="hljs-comment">//返回的是一个小型的RDD集合</span>
<span class="hljs-keyword">val</span> training = splits(<span class="hljs-number">0</span>)
<span class="hljs-keyword">val</span> test = splits(<span class="hljs-number">1</span>)
training.collect
res:<span class="hljs-number">1</span> Array[Int] = Array(<span class="hljs-number">1</span>, <span class="hljs-number">4</span>, <span class="hljs-number">5</span>, <span class="hljs-number">6</span>, <span class="hljs-number">8</span>, <span class="hljs-number">10</span>)
test.collect
res2: Array[Int] = Array(<span class="hljs-number">2</span>, <span class="hljs-number">3</span>, <span class="hljs-number">7</span>, <span class="hljs-number">9</span>)

<span class="hljs-comment">//也可以不指定初始化种子，每次按照权重切分的结果不同，可能一个RDD中的数据个数也不同</span>
<span class="hljs-keyword">val</span> y = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">10</span>)
<span class="hljs-keyword">val</span> splits = y.randomSplit(Array(<span class="hljs-number">0.1</span>, <span class="hljs-number">0.3</span>, <span class="hljs-number">0.6</span>))

splits(<span class="hljs-number">2</span>).collect
<span class="hljs-comment">//此处结果是5个，就说明不是严格按照百分比，可能会出现上下波动</span>
res2: Array[Int] = Array(<span class="hljs-number">2</span>, <span class="hljs-number">3</span>, <span class="hljs-number">4</span>, <span class="hljs-number">6</span>, <span class="hljs-number">8</span>)</code></pre>

<hr>



<h2 id="54reduce"><strong>54、reduce</strong></h2>

<p>原型  <br>
def reduce(f: (T, T) =&gt; T): T</p>

<p>含义 <br>
<strong>reduce</strong> 把RDD中任意两个元素分层合并，根据用户指定的函数进行聚合</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> a = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">100</span>, <span class="hljs-number">3</span>)
<span class="hljs-comment">//rdd中任意两个数据，将其不断缩小</span>
a.reduce(_ + _)
res1: Int = <span class="hljs-number">5050</span></code></pre>

<hr>



<h2 id="55reducebykey-pair"><strong>55、reduceByKey [Pair]</strong></h2>

<p>原型  <br>
def reduceByKey(func: (V, V) =&gt; V): RDD[(K, V)] <br>
def reduceByKey(func: (V, V) =&gt; V, numPartitions: Int): RDD[(K, V)]</p>

<p>含义 <br>
<strong>reduceByKey</strong> 先根据相同的 key 进行分组，然后在相同的 key 中进行聚合，聚合形式和 <strong>reduce</strong> 相同</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-comment">//reduceByKey处理的是key-value类型的数据</span>
<span class="hljs-keyword">val</span> a = sc.parallelize(List(<span class="hljs-string">"dog"</span>, <span class="hljs-string">"tiger"</span>, <span class="hljs-string">"lion"</span>, <span class="hljs-string">"cat"</span>, <span class="hljs-string">"panther"</span>, <span class="hljs-string">"eagle"</span>), <span class="hljs-number">2</span>)
<span class="hljs-keyword">val</span> b = a.map(x =&gt; (x.length, x))
<span class="hljs-comment">//先按照key分组，每个组内部进行reduce，然后生成新的RDD</span>
b.reduceByKey(_ + _).collect
res1: Array[(Int, String)] = Array((<span class="hljs-number">4</span>,lion), (<span class="hljs-number">3</span>,dogcat), (<span class="hljs-number">7</span>,panther), (<span class="hljs-number">5</span>,tigereagle))</code></pre>

<hr>



<h2 id="56rightouterjoin-pair"><strong>56、rightOuterJoin [Pair]</strong></h2>

<p>原型  <br>
def rightOuterJoin[W](other: RDD[(K, W)]): RDD[(K, (Option[V], W))] <br>
def rightOuterJoin[W](other: RDD[(K, W)], numPartitions: Int): RDD[(K, (Option[V], W))]</p>

<p>含义 <br>
<strong>rightOuterJoin</strong> 类似于数据中的右外连接，以右边的作为参考，要是左边没有，那么就将其补空。右边没有的，左边有，那么就舍弃。</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-comment">//设置两个key-value集合</span>
<span class="hljs-keyword">val</span> a = sc.parallelize(List(<span class="hljs-string">"dog"</span>, <span class="hljs-string">"salmon"</span>, <span class="hljs-string">"salmon"</span>, <span class="hljs-string">"rat"</span>, <span class="hljs-string">"elephant"</span>), <span class="hljs-number">3</span>)
<span class="hljs-keyword">val</span> b = a.keyBy(_.length)
<span class="hljs-keyword">val</span> c =sc.parallelize(List(<span class="hljs-string">"dog"</span>,<span class="hljs-string">"cat"</span>,<span class="hljs-string">"gnu"</span>,<span class="hljs-string">"salmon"</span>,<span class="hljs-string">"turkey"</span>,<span class="hljs-string">"wolf"</span>,<span class="hljs-string">"bear"</span>), <span class="hljs-number">3</span>)
<span class="hljs-keyword">val</span> d = c.keyBy(_.length)
b.rightOuterJoin(d).collect
<span class="hljs-comment">//如果右边有相同的key，他们会按照多个key来计算</span>
res2: Array[(Int, (Option[String], String))] = Array((<span class="hljs-number">6</span>,(Some(salmon),salmon)), (<span class="hljs-number">6</span>,(Some(salmon),turkey)), (<span class="hljs-number">6</span>,(Some(salmon),salmon)), (<span class="hljs-number">6</span>,(Some(salmon),turkey)), (<span class="hljs-number">3</span>,(Some(dog),gnu)), (<span class="hljs-number">3</span>,(Some(dog),dog)), (<span class="hljs-number">3</span>,(Some(dog),cat)), (<span class="hljs-number">3</span>,(Some(rat),gnu)), (<span class="hljs-number">3</span>,(Some(rat),dog)), (<span class="hljs-number">3</span>,(Some(rat),cat)), (<span class="hljs-number">4</span>,(None,wolf)), (<span class="hljs-number">4</span>,(None,bear)))</code></pre>

<hr>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>