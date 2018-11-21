---
layout:     post
title:      Spark RDD API 参考示例（一）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，欢迎转载。					https://blog.csdn.net/u010472512/article/details/72901244				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>本文参考<a href="http://homepage.cs.latrobe.edu.au/zhe/ZhenHeSparkRDDAPIExamples.html" rel="nofollow" target="_blank">Zhen He</a></p>



<h2 id="1aggregate"><strong>1、aggregate</strong></h2>

<p>原型  <br>
def aggregate[U: ClassTag](zeroValue: U)(seqOp: (U, T) =&gt; U, combOp: (U, U) =&gt; U): U</p>

<p>含义 <br>
<strong>aggregate</strong>是一个聚合函数，一个RDD分区后，产生多个Partition，在<strong>aggregate</strong>中需要指定两个处理函数，第一个函数用于对每个分区内部处理，第二个函数用于分区之间的处理。<strong>aggregate</strong> 的初始值作用于前后两个部分， <em>分区是严格按照顺序的，顺序不同<strong>aggregate</strong> 结果就不同。</em></p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-comment">//分区顺序严重aggregate的结果,分区特点，先平均分配，多的依次放到序号大的分区</span>

<span class="hljs-keyword">val</span> z = sc.parallelize(List(<span class="hljs-number">1</span>,<span class="hljs-number">2</span>,<span class="hljs-number">3</span>,<span class="hljs-number">4</span>,<span class="hljs-number">5</span>,<span class="hljs-number">6</span>), <span class="hljs-number">2</span>)
<span class="hljs-comment">//使用parallelize产生2个分区，可以使用mapPartitionWithIndex查看分区</span>
<span class="hljs-comment">//分区结果是(1,2,3)和(4,5,6)</span>
z.aggregate(<span class="hljs-number">0</span>)(math.max,_+_)
res0: Int = <span class="hljs-number">9</span>  

特别注意：初始值 <span class="hljs-number">0</span> 是加在分区的左边
<span class="hljs-comment">//第一个参数0 表示将0放入到每个分区中，然后每个分区进行计算。每个分区之间汇总时，再次将0放入其中计算</span>
<span class="hljs-comment">//math.max表示对每个分区内部取最大值，第一个分区有（1，2，3），再加上0，所以是（0，1，2，3）取最大值</span>
<span class="hljs-comment">//_+_表示每个分区之间进行相加</span>

<span class="hljs-keyword">val</span> x = sc.parallelize(List(<span class="hljs-number">1</span>,<span class="hljs-number">2</span>,<span class="hljs-number">3</span>,<span class="hljs-number">4</span>,<span class="hljs-number">5</span>,<span class="hljs-number">6</span>,<span class="hljs-number">7</span>,<span class="hljs-number">8</span>,<span class="hljs-number">9</span>), <span class="hljs-number">3</span>)
x.aggregate(<span class="hljs-number">5</span>)(math.max,_+_)
res1: Int = <span class="hljs-number">25</span>

<span class="hljs-comment">//分析三个分区为(1,2,3),(4,5,6),(7,8,9)其中aggregate的初始值为5</span>
<span class="hljs-comment">//每次分区计算数据为(5,1,2,3),(5,4,5,6),(5,7,8,9),分别取最大值，得到(5,6,9)</span>
<span class="hljs-comment">//分区之间使用 _+_ 计算时，再次将5，放入其中，所以变成(5,5,6,9)</span>
<span class="hljs-comment">//最后将其相加变成5+5+6+9=25</span>

<span class="hljs-comment">//字符串类型处理</span>
<span class="hljs-keyword">val</span> z = sc.parallelize(List(<span class="hljs-string">"a"</span>,<span class="hljs-string">"b"</span>,<span class="hljs-string">"c"</span>,<span class="hljs-string">"d"</span>,<span class="hljs-string">"e"</span>,<span class="hljs-string">"f"</span>),<span class="hljs-number">2</span>)
z.aggregate(<span class="hljs-string">"x"</span>)(_ + _, _+_)
res2: String = xxdefxabc
<span class="hljs-comment">//第一个分区集合中有("x","a","b","c") 分区内部的聚合结果为"xabc"</span>
<span class="hljs-comment">//由于是并行的聚合，所以"xabc"和"xdef"谁在前面和后面并不知道</span>
<span class="hljs-comment">//分区之间进行聚合时，集合变成了("x","xabc","xdef")</span>

<span class="hljs-comment">//两个高级的示例</span>
<span class="hljs-keyword">val</span> z = sc.parallelize(List(<span class="hljs-string">"12"</span>,<span class="hljs-string">"23"</span>,<span class="hljs-string">"345"</span>,<span class="hljs-string">"4567"</span>),<span class="hljs-number">2</span>)
z.aggregate(<span class="hljs-string">""</span>)((x,y) =&gt; math.max(x.length, y.length).toString, _+_)
res3: String = <span class="hljs-number">42</span>
<span class="hljs-comment">//分为两个区("12","23")和("345","4567")</span>
<span class="hljs-comment">//由于需要使用两个函数，所以这里定义了一个闭包函数，集合中的每两个元素，取出最大的长度。</span>
<span class="hljs-comment">//每个分区进行比较时集合为("","12","34")、("","345","4567")</span>
<span class="hljs-comment">//每个分区聚合之后的结果为("2","4")</span>
<span class="hljs-comment">//分区之间进行聚合时，集合为("","2","4"),再次聚合结构可能是"24"也可能是"42"</span>

<span class="hljs-keyword">val</span> z = sc.parallelize(List(<span class="hljs-string">"12"</span>,<span class="hljs-string">"23"</span>,<span class="hljs-string">"345"</span>,<span class="hljs-string">"4567"</span>),<span class="hljs-number">2</span>)
z.aggregate(<span class="hljs-string">"asd"</span>)((x,y) =&gt; math.min(x.length, y.length).toString, _+_)
res4: String = asd11
<span class="hljs-comment">//分区后的结果为("12","23")  ("345","456")</span>
<span class="hljs-comment">//分区内部应用初始值之后的结果为("asd","12","34")   ("asd","345","456")</span>
<span class="hljs-comment">//每个分区中有三个元素，每个分区内部需要多次聚合，分为("asd","12")和("34")</span>
<span class="hljs-comment">//("asd","12")聚合结果是"2", 再与("34")聚合结果是"1"</span>
<span class="hljs-comment">//所以("asd","12","34") 聚合结果是"1"</span>
<span class="hljs-comment">//同理("asd","345","456") 聚合结果是"1"</span>
<span class="hljs-comment">//两个分区之间再次聚合，将初始值应用到其中，得到"asd11"</span>

<span class="hljs-keyword">val</span> z = sc.parallelize(List(<span class="hljs-string">"12"</span>,<span class="hljs-string">"23"</span>,<span class="hljs-string">"345"</span>,<span class="hljs-string">""</span>),<span class="hljs-number">2</span>)
z.aggregate(<span class="hljs-string">""</span>)((x,y) =&gt; math.min(x.length, y.length).toString,_+_)
res5: String = <span class="hljs-number">10</span>
<span class="hljs-comment">//分区后的结果为("12","23") ("345","")</span>
<span class="hljs-comment">//分区内部应用初始值之后的结果为("","12","23") ("","345","")</span>
<span class="hljs-comment">//有三个元素，需要多次聚合("","12")  ("23")</span>
<span class="hljs-comment">//("","12")聚合结果是"0"  再与("23")聚合，得到的结果是"1"</span>
<span class="hljs-comment">//("","345")聚合结果是"0"  再与("")聚合，得到的结果是"0"</span>
<span class="hljs-comment">//最后两次结果相加，得到的是"10"</span>

特别注意：为了说明分区顺序的重要性，请看下面的例子
<span class="hljs-keyword">val</span> z = sc.parallelize(List(<span class="hljs-string">"12"</span>,<span class="hljs-string">"23"</span>,<span class="hljs-string">""</span>,<span class="hljs-string">"345"</span>),<span class="hljs-number">2</span>)
z.aggregate(<span class="hljs-string">""</span>)((x,y) =&gt; math.min(x.length, y.length).toString,_+_)
res6: String = <span class="hljs-number">11</span>
<span class="hljs-comment">//分区后的结果为("12","23") ("","345")</span>
<span class="hljs-comment">//分区内部应用初始值之后的结果为("","12","23") ("","","345")</span>
<span class="hljs-comment">//有三个元素，需要多次聚合("","12")  ("23")</span>
<span class="hljs-comment">//("","12")聚合结果是"0"  再与("23")聚合，得到的结果是"1"</span>
<span class="hljs-comment">//("","")聚合结果是"0"  再与("345")聚合，得到的结果是"1"</span>
<span class="hljs-comment">//最后两次结果相加，得到的是"11"</span>
</code></pre>

<hr>



<h2 id="2aggregatebykeypair"><strong>2、aggregateByKey[Pair]</strong></h2>

<p>原型  <br>
def aggregateByKey[U](zeroValue: U)(seqOp: (U, V) ⇒ U, combOp: (U, U) ⇒ U)(implicit arg0: ClassTag[U]): RDD[(K, U)]</p>

<p>含义 <br>
<strong>aggregateByKey</strong> 也是一个聚合函数，一个RDD分区后，产生多个Partition，在<strong>aggregateByKey</strong>中需要指定两个处理函数，第一个函数用于对每个分区内部处理，第二个函数用于分区之间的处理。<strong>aggregateByKey</strong> 的初始值只作用于每个分区内部，不影响分区之间聚合，<em>分区是严格按照顺序的，顺序不同<strong>aggregateByKey</strong> 结果就不同。</em></p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> z = sc.parallelize(List( (<span class="hljs-string">"cat"</span>,<span class="hljs-number">2</span>), (<span class="hljs-string">"cat"</span>, <span class="hljs-number">5</span>), (<span class="hljs-string">"pig"</span>, <span class="hljs-number">4</span>),(<span class="hljs-string">"cat"</span>, <span class="hljs-number">12</span>), (<span class="hljs-string">"dog"</span>, <span class="hljs-number">12</span>)), <span class="hljs-number">2</span>)
z.aggregateByKey(<span class="hljs-number">0</span>)(math.max(_, _), _ + _).collect
res1: Array((dog,<span class="hljs-number">12</span>), (pig,<span class="hljs-number">4</span>), (cat,<span class="hljs-number">17</span>))  
<span class="hljs-comment">//分区之后的结果是(("cat",2),("cat",5))   (("pig",4),("cat",12),("dog",12))</span>
<span class="hljs-comment">//应用aggregate的初始值后，第二个分区为: </span>
<span class="hljs-comment">//([("pig",0)，("pig","4")],[("cat",0),("cat",12)],[("dog",0),("dog",12)])</span>
<span class="hljs-comment">//使用math.max函数后，结果为(("pig","4"),("cat",12),("dog",12))</span>
<span class="hljs-comment">//如果使用math.min函数后，结果就为(("pig",0),("cat",0),("dog",0))</span>
<span class="hljs-comment">//使用函数_+_聚合时，不会应用初始值</span>

z.aggregateByKey(<span class="hljs-number">100</span>)(math.max(_, _), _ + _).collect
res2: Array((dog,<span class="hljs-number">100</span>), (pig,<span class="hljs-number">100</span>), (cat,<span class="hljs-number">200</span>))
</code></pre>

<hr>



<h2 id="3cartesian"><strong>3、cartesian</strong></h2>

<p>原型  <br>
def cartesian[U: ClassTag](other: RDD[U]): RDD[(T, U)]</p>

<p>含义 <br>
<strong>cartesian</strong> 笛卡尔积，两个RDD中的元素两两组合</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> x = sc.parallelize(List(<span class="hljs-number">1</span>,<span class="hljs-number">2</span>,<span class="hljs-number">3</span>,<span class="hljs-number">4</span>,<span class="hljs-number">5</span>))
<span class="hljs-keyword">val</span> y = sc.parallelize(List(<span class="hljs-number">6</span>,<span class="hljs-number">7</span>,<span class="hljs-number">8</span>,<span class="hljs-number">9</span>,<span class="hljs-number">10</span>))
x.cartesian(y).collect
<span class="hljs-comment">//结果是两者由小到大的顺序排列</span>
res0: Array[(Int, Int)] = Array((<span class="hljs-number">1</span>,<span class="hljs-number">6</span>), (<span class="hljs-number">1</span>,<span class="hljs-number">7</span>), (<span class="hljs-number">1</span>,<span class="hljs-number">8</span>), (<span class="hljs-number">1</span>,<span class="hljs-number">9</span>), (<span class="hljs-number">1</span>,<span class="hljs-number">10</span>), (<span class="hljs-number">2</span>,<span class="hljs-number">6</span>), (<span class="hljs-number">2</span>,<span class="hljs-number">7</span>), (<span class="hljs-number">2</span>,<span class="hljs-number">8</span>), (<span class="hljs-number">2</span>,<span class="hljs-number">9</span>), (<span class="hljs-number">2</span>,<span class="hljs-number">10</span>), (<span class="hljs-number">3</span>,<span class="hljs-number">6</span>), (<span class="hljs-number">3</span>,<span class="hljs-number">7</span>), (<span class="hljs-number">3</span>,<span class="hljs-number">8</span>), (<span class="hljs-number">3</span>,<span class="hljs-number">9</span>), (<span class="hljs-number">3</span>,<span class="hljs-number">10</span>), (<span class="hljs-number">4</span>,<span class="hljs-number">6</span>), (<span class="hljs-number">5</span>,<span class="hljs-number">6</span>), (<span class="hljs-number">4</span>,<span class="hljs-number">7</span>), (<span class="hljs-number">5</span>,<span class="hljs-number">7</span>), (<span class="hljs-number">4</span>,<span class="hljs-number">8</span>), (<span class="hljs-number">5</span>,<span class="hljs-number">8</span>), (<span class="hljs-number">4</span>,<span class="hljs-number">9</span>), (<span class="hljs-number">4</span>,<span class="hljs-number">10</span>), (<span class="hljs-number">5</span>,<span class="hljs-number">9</span>), (<span class="hljs-number">5</span>,<span class="hljs-number">10</span>))</code></pre>

<hr>



<h2 id="4checkpoint"><strong>4、checkpoint</strong></h2>

<p>原型  <br>
def checkpoint()</p>

<p>含义 <br>
<strong>checkpoint</strong> 检查点机制，假设你在迭代1000次的计算中在第999次失败了，然后你没有checkpoint，你只能重新开始恢复了，如果恰好你在第998次迭代的时候你做了一个checkpoint，那么你只需要恢复第998次产生的rdd,然后再执行2次迭代完成总共1000的迭代，这样效率就很高，比较适用于迭代计算非常复杂的情况，也就是恢复计算代价非常高的情况，适当进行checkpoint会有很大的好处。</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs ">sc.setCheckpointDir(<span class="hljs-string">"hdfs://192.168.10.71:9000/wc"</span>)
<span class="hljs-comment">//检查点目录必须存在</span>
<span class="hljs-keyword">val</span> a = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">5</span>)
a.checkpoint
<span class="hljs-comment">//将其结果检查点更新</span>
a.collect
res1: Long = <span class="hljs-number">5</span></code></pre>

<hr>



<h2 id="5coalesce-repartition"><strong>5、coalesce, repartition</strong></h2>

<p>原型  <br>
def coalesce ( numPartitions : Int , shuffle : Boolean = false ): RDD [T] <br>
def repartition ( numPartitions : Int ): RDD [T]</p>

<p>含义 <br>
<strong>repartition</strong> 表示对已经分区的数据重新分区，是<strong>coalesce</strong> 的一个简单应用 </p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> y = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">10</span>, <span class="hljs-number">5</span>)
y.coalesce(<span class="hljs-number">2</span>, <span class="hljs-keyword">false</span>).collect
res1: Array[Int] = Array(<span class="hljs-number">1</span>, <span class="hljs-number">2</span>, <span class="hljs-number">3</span>, <span class="hljs-number">4</span>, <span class="hljs-number">5</span>, <span class="hljs-number">6</span>, <span class="hljs-number">7</span>, <span class="hljs-number">8</span>, <span class="hljs-number">9</span>, <span class="hljs-number">10</span>)
<span class="hljs-comment">//coalesce分区中false表示直接分区，不对其重新打乱顺序</span>

<span class="hljs-keyword">val</span> z = y.coalesce(<span class="hljs-number">2</span>, <span class="hljs-keyword">true</span>)
<span class="hljs-keyword">val</span> x = y.repartition(<span class="hljs-number">2</span>)
z.collect
res2: Array[Int] = Array(<span class="hljs-number">3</span>, <span class="hljs-number">5</span>, <span class="hljs-number">9</span>, <span class="hljs-number">1</span>, <span class="hljs-number">7</span>, <span class="hljs-number">2</span>, <span class="hljs-number">8</span>, <span class="hljs-number">4</span>, <span class="hljs-number">6</span>, <span class="hljs-number">10</span>)
<span class="hljs-comment">//coalesce分区中false表示重新打乱顺序，再分区，和repartition相等，默认是不打乱顺序的</span>
</code></pre>

<hr>



<h2 id="6cogroup-pair-groupwith-pair"><strong>6、cogroup [Pair], groupWith [Pair]</strong></h2>

<p>原型  <br>
def cogroup[W](other: RDD[(K, W)]): RDD[(K, (Iterable[V], Iterable[W]))] <br>
def groupWith[W](other: RDD[(K, W)]): RDD[(K, (Iterable[V], Iterable[W]))] <br>
含义 <br>
<strong>cogroup</strong> 超级分组，可以将3个key-value类型的RDD进行分组</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-comment">//3个key-value类型的RDD分组聚合</span>
<span class="hljs-keyword">val</span> x = sc.parallelize(List((<span class="hljs-number">1</span>, <span class="hljs-string">"apple"</span>), (<span class="hljs-number">2</span>, <span class="hljs-string">"banana"</span>), (<span class="hljs-number">3</span>, <span class="hljs-string">"orange"</span>), (<span class="hljs-number">4</span>, <span class="hljs-string">"kiwi"</span>)), <span class="hljs-number">2</span>)
<span class="hljs-keyword">val</span> y = sc.parallelize(List((<span class="hljs-number">5</span>, <span class="hljs-string">"computer"</span>), (<span class="hljs-number">1</span>, <span class="hljs-string">"laptop"</span>), (<span class="hljs-number">1</span>, <span class="hljs-string">"desktop"</span>), (<span class="hljs-number">4</span>, <span class="hljs-string">"iPad"</span>)), <span class="hljs-number">2</span>)
<span class="hljs-keyword">val</span> z = sc.parallelize(List((<span class="hljs-number">3</span>,<span class="hljs-string">"iphone"</span>),(<span class="hljs-number">1</span>,<span class="hljs-string">"xiaomi"</span>),(<span class="hljs-number">4</span>,<span class="hljs-string">"huawei"</span>)),<span class="hljs-number">2</span>)
x.cogroup(y,z).collect

res1: Array((<span class="hljs-number">4</span>,(CompactBuffer(kiwi),CompactBuffer(iPad),CompactBuffer(huawei))), (<span class="hljs-number">2</span>,(CompactBuffer(banana),CompactBuffer(),CompactBuffer())), (<span class="hljs-number">1</span>,(CompactBuffer(apple),CompactBuffer(laptop, desktop),CompactBuffer(xiaomi))), (<span class="hljs-number">3</span>,(CompactBuffer(orange),CompactBuffer(),CompactBuffer(iphone))), (<span class="hljs-number">5</span>,(CompactBuffer(),CompactBuffer(computer),CompactBuffer())))

<span class="hljs-comment">//分析：如果x中不存在这个元素，那么就会将其值置为空</span></code></pre>

<hr>



<h2 id="7collect-toarray"><strong>7、collect, toArray</strong></h2>

<p>原型  <br>
def collect[U: ClassTag](f: PartialFunction[T, U]): RDD[U] <br>
def toArray(): Array[T] <br>
含义 <br>
<strong>collect</strong> 将RDD数据转换成Scala中的数组并返回</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs ">
<span class="hljs-keyword">val</span> c = sc.parallelize(List(<span class="hljs-string">"Gnu"</span>, <span class="hljs-string">"Cat"</span>, <span class="hljs-string">"Rat"</span>, <span class="hljs-string">"Dog"</span>, <span class="hljs-string">"Gnu"</span>, <span class="hljs-string">"Rat"</span>), <span class="hljs-number">2</span>)
c.collect
res1: Array[String] = Array(Gnu, Cat, Rat, Dog, Gnu, Rat)
</code></pre>

<hr>



<h2 id="8collectasmap"><strong>8、collectAsMap</strong></h2>

<p>原型  <br>
def collectAsMap(): Map[K, V]</p>

<p>含义 <br>
<strong>collectAsMap</strong> 和collect非常类似，但是，是将数据转换成key-value类型的Map</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs ">
<span class="hljs-keyword">val</span> a = sc.parallelize(List(<span class="hljs-number">1</span>, <span class="hljs-number">2</span>, <span class="hljs-number">1</span>, <span class="hljs-number">3</span>), <span class="hljs-number">1</span>)
<span class="hljs-keyword">val</span> b = a.zip(a)
<span class="hljs-comment">//以List中的数字作为key，出现次数作为value</span>
b.collectAsMap
res1: scala.collection.Map[Int,Int] = Map(<span class="hljs-number">2</span> -&gt; <span class="hljs-number">2</span>, <span class="hljs-number">1</span> -&gt; <span class="hljs-number">1</span>, <span class="hljs-number">3</span> -&gt; <span class="hljs-number">3</span>)
</code></pre>

<hr>



<h2 id="9combinebykeypair"><strong>9、combineByKey[Pair]</strong></h2>

<p>原型  <br>
def combineByKey[C](createCombiner: V =&gt; C, mergeValue: (C, V) =&gt; C, mergeCombiners: (C, C) =&gt; C): RDD[(K, C)]</p>

<p>含义 <br>
<strong>combineByKey</strong> 高效的实现按照key来聚合，通过在每一个分区内部先聚合，再在分区之间聚合。每个分区内部是通过迭代方式来聚合，效率非常高。</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> a = sc.parallelize(List(<span class="hljs-string">"dog"</span>,<span class="hljs-string">"cat"</span>,<span class="hljs-string">"gnu"</span>,<span class="hljs-string">"salmon"</span>,<span class="hljs-string">"rabbit"</span>,<span class="hljs-string">"turkey"</span>,<span class="hljs-string">"wolf"</span>,<span class="hljs-string">"bear"</span>,<span class="hljs-string">"bee"</span>), <span class="hljs-number">3</span>)
<span class="hljs-keyword">val</span> b = sc.parallelize(List(<span class="hljs-number">1</span>,<span class="hljs-number">1</span>,<span class="hljs-number">2</span>,<span class="hljs-number">2</span>,<span class="hljs-number">2</span>,<span class="hljs-number">1</span>,<span class="hljs-number">2</span>,<span class="hljs-number">2</span>,<span class="hljs-number">2</span>), <span class="hljs-number">3</span>)
<span class="hljs-keyword">val</span> c = b.zip(a)
<span class="hljs-comment">//将数据分成三个区，使用数字当作key，字符串作为value</span>

<span class="hljs-keyword">val</span> d = c.combineByKey(List(_), (x:List[String], y:String) =&gt; y :: x, (x:List[String], y:List[String]) =&gt; x ::: y)
<span class="hljs-comment">//combineByKey的三个参数的意思是:</span>
<span class="hljs-comment">//第一个分区为((1,"dog"), (1,"cat"), (2,"gnu"))</span>
<span class="hljs-comment">//List(_) 每个分区中的key，每次取该分区中的一个key</span>
<span class="hljs-comment">//第一次取1，初始时x中的List为空，找到第一个key为1的dog，将dog放入到x中的List，然后再找到cat</span>
<span class="hljs-comment">//第二次取2，初始时x中的List为空，找到第一个key为2的gnu</span>
<span class="hljs-comment">//第一个分区得到了((1,("cat","dog")),(2,("gnu")))</span>
<span class="hljs-comment">//同理，第二个分区得到了((2,("salmon","rabbit")),(1,("turkey")))</span>
<span class="hljs-comment">//同理，第三个分区得到了(2,("wolf","bear"，"bee"))</span>
<span class="hljs-comment">//最后，每个分区之间进行合并，将相同的key的数据合并</span>

d.collect
res1: Array[(Int, List[String])] = Array((<span class="hljs-number">1</span>,List(cat, dog, turkey)), (<span class="hljs-number">2</span>,List(gnu, rabbit, salmon, bee, bear, wolf)))
</code></pre>

<hr>



<h2 id="10context-sparkcontext"><strong>10、context, sparkContext</strong></h2>

<p>原型  <br>
def compute(split: Partition, context: TaskContext): Iterator[T]</p>

<p>含义 <br>
<strong>context</strong> 返回创建RDD时的SparkContext</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> c = sc.parallelize(List(<span class="hljs-string">"Gnu"</span>, <span class="hljs-string">"Cat"</span>, <span class="hljs-string">"Rat"</span>, <span class="hljs-string">"Dog"</span>), <span class="hljs-number">2</span>)
c.context
res1: org.apache.spark.SparkContext = org.apache.spark.SparkContext@<span class="hljs-number">74</span>c08828</code></pre>

<hr>



<h2 id="11count"><strong>11、count</strong></h2>

<p>原型  <br>
def count(): Long</p>

<p>含义 <br>
<strong>count</strong> 返回RDD中存储的元素的个数</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> c = sc.parallelize(List(<span class="hljs-string">"Gnu"</span>, <span class="hljs-string">"Cat"</span>, <span class="hljs-string">"Rat"</span>, <span class="hljs-string">"Dog"</span>), <span class="hljs-number">2</span>)
c.count
res2: Long = <span class="hljs-number">4</span></code></pre>

<hr>



<h2 id="12countapproxdistinct"><strong>12、countApproxDistinct</strong></h2>

<p>原型  <br>
def countApproxDistinct(relativeSD: Double = 0.05): Long</p>

<p>含义 <br>
<strong>countApproxDistinct</strong> 近似统计RDD中不重复的值的个数，可以通过<strong>relativeSD</strong> 来确定统计精度，这种方式适合大规模，分布式的数据快速统计</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> z = sc.parallelize(List(<span class="hljs-number">1</span>,<span class="hljs-number">3</span>,<span class="hljs-number">4</span>,<span class="hljs-number">1</span>,<span class="hljs-number">2</span>,<span class="hljs-number">3</span>))
z.countApproxDistinct(<span class="hljs-number">0.01</span>)
res1: Long = <span class="hljs-number">4</span>
<span class="hljs-comment">//countApproxDistinct统计的是去掉重复的数值</span>

<span class="hljs-keyword">val</span> a = sc.parallelize(<span class="hljs-number">1</span> to <span class="hljs-number">10000</span>, <span class="hljs-number">20</span>)
<span class="hljs-keyword">val</span> b = a++a++a++a++a
b.countApproxDistinct(<span class="hljs-number">0.1</span>)
res2: Long = <span class="hljs-number">8224</span>

b.countApproxDistinct(<span class="hljs-number">0.05</span>)
res3: Long = <span class="hljs-number">9750</span>

b.countApproxDistinct(<span class="hljs-number">0.01</span>)
res4: Long = <span class="hljs-number">9947</span>

b.countApproxDistinct(<span class="hljs-number">0.001</span>)
res5: Long = <span class="hljs-number">10000</span>
</code></pre>

<hr>



<h2 id="13countapproxdistinctbykey-pair"><strong>13、countApproxDistinctByKey [Pair]</strong></h2>

<p>原型  <br>
def countApproxDistinctByKey(relativeSD: Double = 0.05): RDD[(K, Long)]</p>

<p>含义 <br>
<strong>countApproxDistinctByKey</strong> 和 <strong>countApproxDistinct</strong> 类似，但是他是统计元组中的相同的key的个数，相比于其他统计方式，这种统计速度更快</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> a = sc.parallelize(List(<span class="hljs-string">"Gnu"</span>, <span class="hljs-string">"Cat"</span>, <span class="hljs-string">"Rat"</span>, <span class="hljs-string">"Dog"</span>), <span class="hljs-number">2</span>)
<span class="hljs-keyword">val</span> b = sc.parallelize(a.takeSample(<span class="hljs-keyword">true</span>, <span class="hljs-number">100</span>, <span class="hljs-number">0</span>), <span class="hljs-number">5</span>)
<span class="hljs-comment">//产生100个随机的，从a中选取的字符，并分为5个区</span>

<span class="hljs-keyword">val</span> c = sc.parallelize(<span class="hljs-number">1</span> to b.count().toInt, <span class="hljs-number">5</span>)
<span class="hljs-comment">//产生1-100的数组，并分为5个区</span>

<span class="hljs-keyword">val</span> d = b.zip(c)
<span class="hljs-comment">//将其转换为map类型</span>
<span class="hljs-comment">//按照key来进行统计</span>
d.countApproxDistinctByKey(<span class="hljs-number">0.1</span>).collect
res1: Array((Rat,<span class="hljs-number">27</span>), (Cat,<span class="hljs-number">25</span>), (Dog,<span class="hljs-number">29</span>), (Gnu,<span class="hljs-number">24</span>))

d.countApproxDistinctByKey(<span class="hljs-number">0.01</span>).collect
res2:  Array((Rat,<span class="hljs-number">27</span>), (Cat,<span class="hljs-number">24</span>), (Dog,<span class="hljs-number">27</span>), (Gnu,<span class="hljs-number">22</span>))
</code></pre>

<hr>



<h2 id="14countbykey-pair"><strong>14、countByKey [Pair]</strong></h2>

<p>原型  <br>
def countByKey(): Map[K, Long]</p>

<p>含义 <br>
<strong>countByKey</strong> 和 <strong>count</strong> 类似，但是他是用于统计元组中每个相同的key出现的次数，最后返回的是一个元组</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs "><span class="hljs-keyword">val</span> c = sc.parallelize(List((<span class="hljs-number">3</span>, <span class="hljs-string">"Gnu"</span>), (<span class="hljs-number">3</span>, <span class="hljs-string">"Yak"</span>), (<span class="hljs-number">5</span>, <span class="hljs-string">"Mouse"</span>), (<span class="hljs-number">3</span>, <span class="hljs-string">"Dog"</span>)), <span class="hljs-number">2</span>)
c.countByKey
res1: scala.collection.Map[Int,Long] = Map(<span class="hljs-number">3</span> -&gt; <span class="hljs-number">3</span>, <span class="hljs-number">5</span> -&gt; <span class="hljs-number">1</span>)
</code></pre>

<hr>



<h2 id="15countbyvalue-pair"><strong>15、countByValue [Pair]</strong></h2>

<p>原型  <br>
def countByValue(): Map[T, Long]</p>

<p>含义 <br>
<strong>countByValue</strong> 计算RDD中不同值出现的次数，以value作为统计的标准。分区统计时，这个操作会将信息合并到单个reduce中去。</p>

<p>示例</p>



<pre class="prettyprint"><code class="language-scala hljs ">
<span class="hljs-keyword">val</span> b = sc.parallelize(List(<span class="hljs-number">1</span>,<span class="hljs-number">2</span>,<span class="hljs-number">3</span>,<span class="hljs-number">4</span>,<span class="hljs-number">5</span>,<span class="hljs-number">6</span>,<span class="hljs-number">7</span>,<span class="hljs-number">8</span>,<span class="hljs-number">2</span>,<span class="hljs-number">4</span>,<span class="hljs-number">2</span>,<span class="hljs-number">1</span>,<span class="hljs-number">1</span>,<span class="hljs-number">1</span>,<span class="hljs-number">1</span>,<span class="hljs-number">1</span>))
b.countByValue
res1: Map(<span class="hljs-number">5</span> -&gt; <span class="hljs-number">1</span>, <span class="hljs-number">8</span> -&gt; <span class="hljs-number">1</span>, <span class="hljs-number">3</span> -&gt; <span class="hljs-number">1</span>, <span class="hljs-number">6</span> -&gt; <span class="hljs-number">1</span>, <span class="hljs-number">1</span> -&gt; <span class="hljs-number">6</span>, <span class="hljs-number">2</span> -&gt; <span class="hljs-number">3</span>, <span class="hljs-number">4</span> -&gt; <span class="hljs-number">2</span>, <span class="hljs-number">7</span> -&gt; <span class="hljs-number">1</span>)
</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>