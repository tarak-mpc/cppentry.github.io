---
layout:     post
title:      Spark RDD API 参考示例（三）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，欢迎转载。					https://blog.csdn.net/u010472512/article/details/72923278				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>本文参考<a href="http://homepage.cs.latrobe.edu.au/zhe/ZhenHeSparkRDDAPIExamples.html" rel="nofollow">Zhen He</a></p>



<h2 id="28getcheckpointfile"><strong>28、getCheckpointFile</strong></h2>

<p>原型  <br>
def getCheckpointFile: Option[String]</p>

<p>含义 <br>
<strong>getCheckpointFile</strong> 返回RDD的<strong>checkpoint</strong> 文件的路径，主要用于对大型计算中恢复到指定的节点</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-comment">//设置CheckPoint的路径，前提是路径一定要存在</span>
sc.setCheckpointDir(<span class="hljs-string">"hdfs://192.168.10.71:9000/wc"</span>)
<span class="hljs-keyword">val</span> a = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">500</span>, <span class="hljs-number">5</span>)
<span class="hljs-keyword">val</span> b = a++a++a++a++a

<span class="hljs-comment">//获取b的历史 checkpoint 文件路径</span>
b.getCheckpointFile
<span class="hljs-comment">//目前没有checkpoint文件</span>
res5: Option[String] = None

<span class="hljs-comment">//设置checkpoint，但不会立马提交，rdd具有延迟的特点</span>
b.checkpoint
b.getCheckpointFile
res10: Option[String] = None

<span class="hljs-comment">//使用action算子时，才会真正提交checkpoint</span>
b.collect

<span class="hljs-comment">//获取上面提交的checkpoint文件路径</span>
b.getCheckpointFile
res15: Option[String] = Some(hdfs:<span class="hljs-comment">//192.168.10.71:9000/wc/e7f2340a-b37b-4d97-8b48-58253e6e4464/rdd-133)</span>
</code></pre>

<hr>



<h2 id="29getstoragelevel"><strong>29、getStorageLevel</strong></h2>

<p>原型  <br>
def getStorageLevel</p>

<p>含义 <br>
<strong>getStorageLevel</strong> 返回RDD当前的存储级别，存储级别一旦确定，就不能再修改了。</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> a = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">100000</span>, <span class="hljs-number">2</span>)
<span class="hljs-comment">//表示目前RDD使用的存储级别是存储在内存中，未序列化，存储1份</span>
a.getStorageLevel
res1: org.apache.spark.storage.StorageLevel = StorageLevel(memory, deserialized, <span class="hljs-number">1</span> replicas)

<span class="hljs-comment">//可以事先指定存储级别</span>
<span class="hljs-keyword">val</span> a = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">100000</span>, <span class="hljs-number">2</span>)
a.persist(org.apache.spark.storage.StorageLevel.DISK_ONLY)
a.getStorageLevel
<span class="hljs-comment">//表示存储在磁盘中，存储1份</span>
res2: org.apache.spark.storage.StorageLevel = StorageLevel(disk, <span class="hljs-number">1</span> replicas)
</code></pre>

<hr>



<h2 id="30glom"><strong>30、glom</strong></h2>

<p>原型  <br>
def glom(): RDD[Array[T]]</p>

<p>含义 <br>
<strong>glom</strong> 将RDD的每一个分区作为一个单独的包装，然后分区之间再包装起来</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> a = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">10</span>, <span class="hljs-number">3</span>)
a.glom.collect
<span class="hljs-comment">//每一个分区作为一个单独的包装，然后分区之间再包装起来</span>
res1:  Array(Array(<span class="hljs-number">1</span>, <span class="hljs-number">2</span>, <span class="hljs-number">3</span>), Array(<span class="hljs-number">4</span>, <span class="hljs-number">5</span>, <span class="hljs-number">6</span>), Array(<span class="hljs-number">7</span>, <span class="hljs-number">8</span>, <span class="hljs-number">9</span>, <span class="hljs-number">10</span>))
</code></pre>

<hr>



<h2 id="31groupby"><strong>31、groupBy</strong></h2>

<p>原型  <br>
def groupBy[K: ClassTag](f: T =&gt; K, numPartitions: Int): RDD[(K, Iterable[T])]</p>

<p>含义 <br>
<strong>groupBy</strong> 将RDD中的数据按照指定的函数和分区数量，来进行分组。</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> a = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">9</span>, <span class="hljs-number">3</span>)
<span class="hljs-comment">//groupBy的第一个参数是一个函数，用于指定分组条件。分类标签由条件返回值给定</span>
<span class="hljs-comment">//这里会根据条件返回 "even" 和 "odd"</span>
a.groupBy(x =&gt; { <span class="hljs-keyword">if</span> (x % <span class="hljs-number">2</span> == <span class="hljs-number">0</span>) <span class="hljs-string">"even"</span> <span class="hljs-keyword">else</span> <span class="hljs-string">"odd"</span> }).collect
res1： Array((even,CompactBuffer(<span class="hljs-number">2</span>, <span class="hljs-number">8</span>, <span class="hljs-number">4</span>, <span class="hljs-number">6</span>)), (odd,CompactBuffer(<span class="hljs-number">5</span>, <span class="hljs-number">1</span>, <span class="hljs-number">3</span>, <span class="hljs-number">7</span>, <span class="hljs-number">9</span>)))

<span class="hljs-comment">//这里的返回标签为 0 ，1 ，2</span>
a.groupBy(x =&gt;(x % <span class="hljs-number">3</span>)).collect
res2:Array((<span class="hljs-number">0</span>,CompactBuffer(<span class="hljs-number">3</span>, <span class="hljs-number">9</span>, <span class="hljs-number">6</span>)), (<span class="hljs-number">1</span>,CompactBuffer(<span class="hljs-number">4</span>, <span class="hljs-number">1</span>, <span class="hljs-number">7</span>)), (<span class="hljs-number">2</span>,CompactBuffer(<span class="hljs-number">2</span>, <span class="hljs-number">8</span>, <span class="hljs-number">5</span>)))

<span class="hljs-comment">//自定义函数进行分组</span>
<span class="hljs-keyword">val</span> a = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">9</span>, <span class="hljs-number">3</span>)
<span class="hljs-keyword">def</span> myfunc(a: Int) : Int =
{
  a % <span class="hljs-number">2</span>
}
<span class="hljs-comment">//groupBy中的第二个参数是指定，分组后将结果存储在几个分区中，默认分区数量和RDD元素分区数量相等</span>
a.groupBy(x =&gt; myfunc(x), <span class="hljs-number">3</span>).collect
res2: Array((<span class="hljs-number">0</span>,ArrayBuffer(<span class="hljs-number">2</span>, <span class="hljs-number">4</span>, <span class="hljs-number">6</span>, <span class="hljs-number">8</span>)), (<span class="hljs-number">1</span>,ArrayBuffer(<span class="hljs-number">1</span>, <span class="hljs-number">3</span>, <span class="hljs-number">5</span>, <span class="hljs-number">7</span>, <span class="hljs-number">9</span>)))
a.groupBy(x =&gt; myfunc(x), <span class="hljs-number">3</span>).partitions.length
res4: Int = <span class="hljs-number">3</span>

<span class="hljs-comment">//指定结果分区数量为1</span>
a.groupBy(myfunc(_), <span class="hljs-number">1</span>).collect
res3: Array((<span class="hljs-number">0</span>,ArrayBuffer(<span class="hljs-number">2</span>, <span class="hljs-number">4</span>, <span class="hljs-number">6</span>, <span class="hljs-number">8</span>)), (<span class="hljs-number">1</span>,ArrayBuffer(<span class="hljs-number">1</span>, <span class="hljs-number">3</span>, <span class="hljs-number">5</span>, <span class="hljs-number">7</span>, <span class="hljs-number">9</span>)))
a.groupBy(myfunc(_), <span class="hljs-number">1</span>).partitions.length
res5: Int = <span class="hljs-number">1</span>
</code></pre>

<hr>



<h2 id="32groupbykey-pair"><strong>32、groupByKey [Pair]</strong></h2>

<p>原型  <br>
def groupByKey(): RDD[(K, Iterable[V])] <br>
def groupByKey(numPartitions: Int): RDD[(K, Iterable[V])] <br>
def groupByKey(partitioner: Partitioner): RDD[(K, Iterable[V])]</p>

<p>含义 <br>
<strong>groupByKey</strong> 和 <strong>groupBy</strong> 非常相似，不提供函数功能，只是按照key来进行分组，相同的key分在一组，相比于<strong>groupBy</strong> 要简单</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> a = sc.parallelize(List(<span class="hljs-string">"dog"</span>, <span class="hljs-string">"tiger"</span>, <span class="hljs-string">"lion"</span>, <span class="hljs-string">"cat"</span>, <span class="hljs-string">"spider"</span>, <span class="hljs-string">"eagle"</span>), <span class="hljs-number">2</span>)
<span class="hljs-comment">//生成一个以单词长度作为key，单词作为value的 元组</span>
<span class="hljs-keyword">val</span> b = a.keyBy(_.length)
<span class="hljs-comment">//groupByKey不提供函数功能，直接按照Key进行分类</span>
b.groupByKey.collect
res1: Array[(Int, Seq[String])] = Array((<span class="hljs-number">4</span>,ArrayBuffer(lion)), (<span class="hljs-number">6</span>,ArrayBuffer(spider)), (<span class="hljs-number">3</span>,ArrayBuffer(dog, cat)), (<span class="hljs-number">5</span>,ArrayBuffer(tiger, eagle)))
</code></pre>

<hr>



<h2 id="33histogram-double"><strong>33、histogram [Double]</strong></h2>

<p>原型  <br>
def histogram(bucketCount: Int): Pair[Array[Double], Array[Long]] <br>
def histogram(buckets: Array[Double], evenBuckets: Boolean = false): Array[Long]</p>

<p>含义 <br>
<strong>histogram</strong> 根据RDD中的数据生成一个随机的直方图，RDD中的数据作为横坐标，系统自动生成一个纵坐标，有两种方式生成横坐标，第一种指定需要几个柱，第二种，给定横坐标个数。</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-comment">//根据给定的柱子数量来确定坐标</span>
<span class="hljs-keyword">val</span> a = sc.parallelize(List(<span class="hljs-number">9.1</span>, <span class="hljs-number">1.0</span>, <span class="hljs-number">1.2</span>, <span class="hljs-number">2.1</span>, <span class="hljs-number">1.3</span>, <span class="hljs-number">5.0</span>, <span class="hljs-number">2.0</span>, <span class="hljs-number">2.1</span>, <span class="hljs-number">7.4</span>, <span class="hljs-number">7.5</span>, <span class="hljs-number">7.6</span>, <span class="hljs-number">8.8</span>, <span class="hljs-number">10.0</span>, <span class="hljs-number">8.9</span>, <span class="hljs-number">5.5</span>), <span class="hljs-number">3</span>)
a.histogram(<span class="hljs-number">6</span>)
<span class="hljs-comment">//表示需要7个横坐标点，生成6个柱</span>
res1: (Array[Double], Array[Long]) = (Array(<span class="hljs-number">1.0</span>, <span class="hljs-number">2.5</span>, <span class="hljs-number">4.0</span>, <span class="hljs-number">5.5</span>, <span class="hljs-number">7.0</span>, <span class="hljs-number">8.5</span>, <span class="hljs-number">10.0</span>),Array(<span class="hljs-number">6</span>, <span class="hljs-number">0</span>, <span class="hljs-number">1</span>, <span class="hljs-number">1</span>, <span class="hljs-number">3</span>, <span class="hljs-number">4</span>))

<span class="hljs-comment">//根据用户指定的横坐标来确定</span>
<span class="hljs-keyword">val</span> a = sc.parallelize(List(<span class="hljs-number">1.1</span>, <span class="hljs-number">1.2</span>, <span class="hljs-number">1.3</span>, <span class="hljs-number">2.0</span>, <span class="hljs-number">2.1</span>, <span class="hljs-number">7.4</span>, <span class="hljs-number">7.5</span>, <span class="hljs-number">7.6</span>, <span class="hljs-number">8.8</span>, <span class="hljs-number">9.0</span>), <span class="hljs-number">3</span>)
a.histogram(Array(<span class="hljs-number">0.0</span>, <span class="hljs-number">3.0</span>, <span class="hljs-number">8.0</span>))
res2: Array[Long] = Array(<span class="hljs-number">5</span>, <span class="hljs-number">3</span>)
</code></pre>

<hr>



<h2 id="34id"><strong>34、id</strong></h2>

<p>原型  <br>
val id: Int</p>

<p>含义 <br>
<strong>id</strong> 获取系统分配给RDD的编号，这个编号可以用于查找指定的的RDD</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> y = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">10</span>, <span class="hljs-number">10</span>)
y.id
res1: Int = <span class="hljs-number">19</span>
</code></pre>

<hr>



<h2 id="35intersection"><strong>35、intersection</strong></h2>

<p>原型  <br>
def intersection(other: RDD[T], numPartitions: Int): RDD[T] <br>
def intersection(other: RDD[T], partitioner: Partitioner)(implicit ord: Ordering[T] = null): RDD[T] <br>
def intersection(other: RDD[T]): RDD[T]</p>

<p>含义 <br>
<strong>intersection</strong> 求两个集合中相同的元素，也就是求二者的交集</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-comment">//普通元素求交集</span>
<span class="hljs-keyword">val</span> x = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">20</span>)
<span class="hljs-keyword">val</span> y = sc.parallelize(<span class="hljs-number">10</span> to <span class="hljs-number">30</span>)
<span class="hljs-keyword">val</span> z = x.intersection(y)
<span class="hljs-comment">//求两个集合的交集</span>
z.collect
res1: Array[Int] = Array(<span class="hljs-number">16</span>, <span class="hljs-number">12</span>, <span class="hljs-number">20</span>, <span class="hljs-number">13</span>, <span class="hljs-number">17</span>, <span class="hljs-number">14</span>, <span class="hljs-number">18</span>, <span class="hljs-number">10</span>, <span class="hljs-number">19</span>, <span class="hljs-number">15</span>, <span class="hljs-number">11</span>)

<span class="hljs-comment">//两个元组求交集</span>
<span class="hljs-keyword">val</span> x = sc.parallelize(List((<span class="hljs-string">"cat"</span>,<span class="hljs-number">2</span>),(<span class="hljs-string">"wolf"</span>,<span class="hljs-number">1</span>),(<span class="hljs-string">"gnu"</span>,<span class="hljs-number">1</span>)))
<span class="hljs-keyword">val</span> y = sc.parallelize(List((<span class="hljs-string">"cat"</span>,<span class="hljs-number">1</span>),(<span class="hljs-string">"wolf"</span>,<span class="hljs-number">1</span>),(<span class="hljs-string">"mouse"</span>,<span class="hljs-number">1</span>)))
<span class="hljs-keyword">val</span> z = x.intersection(y)
z.collect
<span class="hljs-comment">//只有完全相同的元组才算相同元素</span>
res2: Array[(String, Int)] = Array((wolf,<span class="hljs-number">1</span>))</code></pre>

<hr>



<h2 id="36ischeckpointed"><strong>36、isCheckpointed</strong></h2>

<p>原型  <br>
def isCheckpointed: Boolean</p>

<p>含义 <br>
<strong>isCheckpointed</strong> 检测一个RDD是否已经存在检查点</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-comment">//设置检查点</span>
<span class="hljs-keyword">val</span> c = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">10</span>)
sc.setCheckpointDir(<span class="hljs-string">"hdfs://192.168.10.71:9000/wc"</span>)
c.isCheckpointed
res1: Boolean = <span class="hljs-keyword">false</span>

<span class="hljs-comment">//延迟执行，只有执行action算子时，才会执行checkpoint</span>
c.checkpoint
c.isCheckpointed
res2: Boolean = <span class="hljs-keyword">false</span>

<span class="hljs-comment">//执行action算子，生成checkpoint</span>
c.collect
c.isCheckpointed
res3: Boolean = <span class="hljs-keyword">true</span></code></pre>

<hr>



<h2 id="37join-pair"><strong>37、join [Pair]</strong></h2>

<p>原型  <br>
def join[W](other: RDD[(K, W)]): RDD[(K, (V, W))] <br>
def join[W](other: RDD[(K, W)], numPartitions: Int): RDD[(K, (V, W))] <br>
def join[W](other: RDD[(K, W)], partitioner: Partitioner): RDD[(K, (V, W))]</p>

<p>含义 <br>
<strong>join</strong> 用于两个key-value类型的RDD的内连接操作，类似于数据库中的内连接。只有两者的key相同时，才会连接</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> a = sc.parallelize(List(<span class="hljs-string">"dog"</span>, <span class="hljs-string">"salmon"</span>, <span class="hljs-string">"salmon"</span>, <span class="hljs-string">"rat"</span>, <span class="hljs-string">"elephant"</span>), <span class="hljs-number">3</span>)
<span class="hljs-keyword">val</span> b = a.keyBy(_.length)
<span class="hljs-keyword">val</span> c = sc.parallelize(List(<span class="hljs-string">"dog"</span>,<span class="hljs-string">"cat"</span>,<span class="hljs-string">"gnu"</span>,<span class="hljs-string">"salmon"</span>,<span class="hljs-string">"rabbit"</span>,<span class="hljs-string">"turkey"</span>,<span class="hljs-string">"wolf"</span>,<span class="hljs-string">"bear"</span>,<span class="hljs-string">"bee"</span>), <span class="hljs-number">3</span>)
<span class="hljs-comment">//相同的key，就能连接在一起</span>
<span class="hljs-keyword">val</span> d = c.keyBy(_.length)
b.join(d).collect 

res0: Array[(Int, (String, String))] = Array((<span class="hljs-number">6</span>,(salmon,salmon)), (<span class="hljs-number">6</span>,(salmon,rabbit)), (<span class="hljs-number">6</span>,(salmon,turkey)), (<span class="hljs-number">6</span>,(salmon,salmon)), (<span class="hljs-number">6</span>,(salmon,rabbit)), (<span class="hljs-number">6</span>,(salmon,turkey)), (<span class="hljs-number">3</span>,(dog,dog)), (<span class="hljs-number">3</span>,(dog,cat)), (<span class="hljs-number">3</span>,(dog,gnu)), (<span class="hljs-number">3</span>,(dog,bee)), (<span class="hljs-number">3</span>,(rat,dog)), (<span class="hljs-number">3</span>,(rat,cat)), (<span class="hljs-number">3</span>,(rat,gnu)), (<span class="hljs-number">3</span>,(rat,bee)))
</code></pre>

<hr>



<h2 id="38keyby"><strong>38、keyBy</strong></h2>

<p>原型  <br>
def keyBy[K](f: T =&gt; K): RDD[(K, T)]</p>

<p>含义 <br>
<strong>keyBy</strong> 指定一个函数产生特定的数据作为RDD的key，这个函数可以自定义，主要目的是产生一个元组。</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> a = sc.parallelize(List(<span class="hljs-string">"dog"</span>, <span class="hljs-string">"salmon"</span>, <span class="hljs-string">"salmon"</span>, <span class="hljs-string">"rat"</span>, <span class="hljs-string">"elephant"</span>), <span class="hljs-number">3</span>)
<span class="hljs-comment">//指定每个单词的长度作为RDD中元素的Key</span>
<span class="hljs-keyword">val</span> b = a.keyBy(_.length)
b.collect
res1: Array[(Int, String)] = Array((<span class="hljs-number">3</span>,dog), (<span class="hljs-number">6</span>,salmon), (<span class="hljs-number">6</span>,salmon), (<span class="hljs-number">3</span>,rat), (<span class="hljs-number">8</span>,elephant))
</code></pre>

<hr>



<h2 id="39keys-pair"><strong>39、keys [Pair]</strong></h2>

<p>原型  <br>
def keys: RDD[K]</p>

<p>含义 <br>
<strong>keys</strong> 获取RDD中元组的key，这些key可以重复出现</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> a = sc.parallelize(List(<span class="hljs-string">"dog"</span>, <span class="hljs-string">"tiger"</span>, <span class="hljs-string">"lion"</span>, <span class="hljs-string">"cat"</span>, <span class="hljs-string">"panther"</span>, <span class="hljs-string">"eagle"</span>), <span class="hljs-number">2</span>)
<span class="hljs-keyword">val</span> b = a.map(x =&gt; (x.length, x))
b.keys.collect
<span class="hljs-comment">//可以重复出现</span>
res2: Array[Int] = Array(<span class="hljs-number">3</span>, <span class="hljs-number">5</span>, <span class="hljs-number">4</span>, <span class="hljs-number">3</span>, <span class="hljs-number">7</span>, <span class="hljs-number">5</span>)</code></pre>

<hr>



<h2 id="40leftouterjoin-pair"><strong>40、leftOuterJoin [Pair]</strong></h2>

<p>原型  <br>
def leftOuterJoin[W](other: RDD[(K, W)]): RDD[(K, (V, Option[W]))] <br>
def leftOuterJoin[W](other: RDD[(K, W)], numPartitions: Int): RDD[(K, (V, Option[W]))] <br>
def leftOuterJoin[W](other: RDD[(K, W)], partitioner: Partitioner): RDD[(K, (V, Option[W]))]</p>

<p>含义 <br>
<strong>leftOuterJoin</strong> 类似于数据库中的左外连接，以左边作为标准，右边没有的填缺失值，左边没有的右边有，舍弃掉。</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> a = sc.parallelize(List((<span class="hljs-string">"dog"</span>,<span class="hljs-number">2</span>),(<span class="hljs-string">"salmon"</span>,<span class="hljs-number">2</span>),(<span class="hljs-string">"rat"</span>,<span class="hljs-number">1</span>),(<span class="hljs-string">"elephant"</span>,<span class="hljs-number">10</span>)),<span class="hljs-number">3</span>)
<span class="hljs-keyword">val</span> b = sc.parallelize(List((<span class="hljs-string">"dog"</span>,<span class="hljs-number">2</span>),(<span class="hljs-string">"salmon"</span>,<span class="hljs-number">2</span>),(<span class="hljs-string">"rabbit"</span>,<span class="hljs-number">1</span>),(<span class="hljs-string">"cat"</span>,<span class="hljs-number">7</span>)), <span class="hljs-number">3</span>)
a.leftOuterJoin(b).collect

<span class="hljs-comment">//左边有的，在结果集中都有，左边没有的，右边都舍弃掉。以左边作为参考标准</span>
res1:Array((rat,(<span class="hljs-number">1</span>,None)), (salmon,(<span class="hljs-number">2</span>,Some(<span class="hljs-number">2</span>))), (elephant,(<span class="hljs-number">10</span>,None)), (dog,(<span class="hljs-number">2</span>,Some(<span class="hljs-number">2</span>))))</code></pre>

<hr>



<h2 id="41lookup"><strong>41、lookup</strong></h2>

<p>原型  <br>
def lookup(key: K): Seq[V]</p>

<p>含义 <br>
<strong>lookup</strong> 查看指定key的value值，通过全表扫描来实现</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> a = sc.parallelize(List(<span class="hljs-string">"dog"</span>, <span class="hljs-string">"tiger"</span>, <span class="hljs-string">"lion"</span>, <span class="hljs-string">"cat"</span>, <span class="hljs-string">"panther"</span>, <span class="hljs-string">"eagle"</span>), <span class="hljs-number">2</span>)
<span class="hljs-keyword">val</span> b = a.map(x =&gt; (x.length, x))
b.lookup(<span class="hljs-number">5</span>)
<span class="hljs-comment">//通过全表扫描来查找 key=5 的值</span>
res1: Seq[String] = WrappedArray(tiger, eagle)</code></pre>

<hr>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>