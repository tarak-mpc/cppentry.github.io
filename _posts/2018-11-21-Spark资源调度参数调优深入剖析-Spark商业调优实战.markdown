---
layout:     post
title:      Spark资源调度参数调优深入剖析-Spark商业调优实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本套技术专栏是作者（秦凯新）平时工作的总结和升华，通过从真实商业环境抽取案例进行总结和分享，并给出商业应用的调优建议和集群环境容量规划等内容，请持续关注本套博客。版权声明：禁止转载，欢迎学习。QQ邮箱地址：1120746959@qq.com，如有任何商业交流，可随时联系。					https://blog.csdn.net/shenshouniu/article/details/83794231				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>本套系列博客从真实商业环境抽取案例进行总结和分享，并给出Spark商业应用实战指导，请持续关注本套博客。版权声明：本套Spark商业应用实战归作者（秦凯新）所有，禁止转载，欢迎学习。</p>
<ul>
<li><a href="https://juejin.im/post/5bded9d9e51d45053b5c73b4" rel="nofollow">秦凯新的技术社区-大数据商业实战系列全集目录  </a></li>
<li><a href="https://juejin.im/post/5be01f0be51d4540ed4e811f" rel="nofollow">Spark商业应用实战-Spark数据倾斜案例测试及调优准则深入剖析 </a></li>
<li><a href="https://juejin.im/post/5be183546fb9a049f745aa2b" rel="nofollow">Spark商业应用实战-Spark资源调度参数调优深入剖析 </a></li>
</ul>
<h2><a id="1_Spark_6"></a>1 Spark内部资源关系</h2>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/6/166e8e9f18add8c0?w=683&amp;h=434&amp;f=png&amp;s=15337" alt=""></p>
<h2><a id="2_Spark_10"></a>2 Spark运行资源优化配置</h2>
<pre><code>    ./bin/spark-submit \  
    --master yarn-cluster \  
    --num-executors 100 \  
    --executor-memory 6G \ 
    --executor-cores 4 \
    --driver-memory 1G \
    --conf spark.default.parallelism=1000 \
    --conf spark.storage.memoryFraction=0.5 \  
    --conf spark.shuffle.memoryFraction=0.3 \
</code></pre>
<h2><a id="3__Spark__21"></a>3  Spark 算子调优建议</h2>
<ul>
<li>
<p>程序开发调优 ：避免创建重复的RDD</p>
<pre><code>  val rdd1 = sc.textFile("hdfs://master01:9000/hello.txt")
  rdd1.map(...)
  val rdd2 = sc.textFile("hdfs://master01:9000/hello.txt")
  rdd2.reduce(...)
</code></pre>
</li>
</ul>
<p>需要对名为“hello.txt”的HDFS文件进行一次map操作，再进行一次reduce操作。<br>
也就是说，需要对一份数据执行两次算子操作。<br>
错误的做法：对于同一份数据执行多次算子操作时，创建多个RDD。<br>
这里执行了两次textFile方法，针对同一个HDFS文件，创建了两个RDD出来<br>
，然后分别对每个RDD都执行了一个算子操作。<br>
这种情况下，Spark需要从HDFS上两次加载hello.txt文件的内容，并创建两个单独的RDD；<br>
第二次加载HDFS文件以及创建RDD的性能开销，很明显是白白浪费掉的。</p>
<hr>
<ul>
<li>
<p>程序开发调优 ：尽可能复用同一个RDD</p>
</li>
<li>
<p>错误的做法：<br>
有一个&lt;long , String&gt;格式的RDD，即rdd1。<br>
接着由于业务需要，对rdd1执行了一个map操作，创建了一个rdd2，<br>
而rdd2中的数据仅仅是rdd1中的value值而已，也就是说，rdd2是rdd1的子集。</p>
<p>JavaPairRDD&lt;long , String&gt; rdd1 = …<br>
JavaRDD rdd2 = rdd1.map(…)</p>
</li>
</ul>
<p>分别对rdd1和rdd2执行了不同的算子操作。</p>
<pre><code>    rdd1.reduceByKey(...)
    rdd2.map(...)
</code></pre>
<ul>
<li>正确的做法：</li>
</ul>
<p>rdd2的数据完全就是rdd1的子集而已，却创建了两个rdd，并对两个rdd都执行了一次算子操作。<br>
此时会因为对rdd1执行map算子来创建rdd2，而多执行一次算子操作，进而增加性能开销。<br>
其实在这种情况下完全可以复用同一个RDD。<br>
我们可以使用rdd1，既做reduceByKey操作，也做map操作。</p>
<pre><code>JavaPairRDD&lt;long , String&gt; rdd1 = ...
rdd1.reduceByKey(...)
rdd1.map(tuple._2...)
</code></pre>
<hr>
<ul>
<li>
<p>程序开发调优 ：对多次使用的RDD进行持久化</p>
<pre><code>  // 正确的做法。
  // cache()方法表示：使用非序列化的方式将RDD中的数据全部尝试持久化到内存中。
  // 此时再对rdd1执行两次算子操作时，只有在第一次执行map算子时，才会将这个rdd1从源头处计算一次。
  // 第二次执行reduce算子时，就会直接从内存中提取数据进行计算，不会重复计算一个rdd。
  
  val rdd1 = sc.textFile("hdfs://192.168.0.1:9000/hello.txt").cache()
  rdd1.map(...)
  rdd1.reduce(...)


  正确的做法：
  // 序列化的方式可以减少持久化的数据对内存/磁盘的占用量，进而避免内存被持久化数据占用过多，
  //从而发生频繁GC。
  
  val rdd1 = sc.textFile("hdfs://192.168.0.1:9000/hello.txt")
  .persist(StorageLevel.MEMORY_AND_DISK_SER)
  rdd1.map(...)
  rdd1.reduce(...)
</code></pre>
</li>
</ul>
<p>通常不建议使用DISK_ONLY和后缀为_2的级别：因为完全基于磁盘文件进行数据的读写，会导致性能急剧降低，已经网络较大开销</p>
<hr>
<ul>
<li>程序开发调优 ：尽量避免使用shuffle类算子</li>
</ul>
<p>如果有可能的话，要尽量避免使用shuffle类算子，最消耗性能的地方就是shuffle过程。</p>
<p>shuffle过程中，各个节点上的相同key都会先写入本地磁盘文件中，然后其他节点需要通过网络传输拉取各个节点上的磁盘文件中的相同key。而且相同key都拉取到同一个节点进行聚合操作时，还有可能会因为一个节点上处理的key过多，导致内存不够存放，进而溢写到磁盘文件中。因此在shuffle过程中，可能会发生大量的磁盘文件读写的IO操作，以及数据的网络传输操作。磁盘IO和网络数据传输也是shuffle性能较差的主要原因。</p>
<pre><code>    尽可能避免使用reduceByKey、join、distinct、repartition等会进行shuffle的算子，尽量使用map类的非shuffle算子。
    // 传统的join操作会导致shuffle操作。
    // 因为两个RDD中，相同的key都需要通过网络拉取到一个节点上，由一个task进行join操作。
       val rdd3 = rdd1.join(rdd2)

    // Broadcast+map的join操作，不会导致shuffle操作。
    // 使用Broadcast将一个数据量较小的RDD作为广播变量。

    // 注意，以上操作，建议仅仅在rdd2的数据量比较少（比如几百M，或者一两G）的情况下使用。
    // 因为每个Executor的内存中，都会驻留一份rdd2的全量数据。
    val rdd2Data = rdd2.collect()
    val rdd2DataBroadcast = sc.broadcast(rdd2Data)
    val rdd3 = rdd1.map(rdd2DataBroadcast...)
</code></pre>
<hr>
<ul>
<li>程序开发调优 ：使用map-side预聚合的shuffle操作</li>
</ul>
<p>如果因为业务需要，一定要使用shuffle操作，无法用map类的算子来替代，那么尽量使用可以map-side预聚合的算子<br>
类似于MapReduce中的本地combiner。map-side预聚合之后，每个节点本地就只会有一条相同的key，因为多条相同的key都被聚合起来了。其他节点在拉取所有节点上的相同key时，就会大大减少需要拉取的数据数量，从而也就减少了磁盘IO以及网络传输开销。</p>
<p>建议使用reduceByKey或者aggregateByKey算子来替代掉groupByKey算子</p>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/6/166e9130f30c44ba?w=428&amp;h=430&amp;f=png&amp;s=178540" alt=""></p>
<hr>
<ul>
<li>程序开发调优 ：使用高性能的算子</li>
<li>使用reduceByKey/aggregateByKey替代groupByKey              :   map-side</li>
<li>使用mapPartitions替代普通map                              :  函数执行频率</li>
<li>使用foreachPartitions替代foreach                          :  函数执行频率</li>
<li>使用filter之后进行coalesce操作                            :  filter后对分区进行压缩</li>
<li>使用repartitionAndSortWithinPartitions替代repartition与sort类操作</li>
</ul>
<p>repartitionAndSortWithinPartitions是Spark官网推荐的一个算子，官方建议，如果需要在repartition重分区之后，还要进行排序，建议直接使用repartitionAndSortWithinPartitions算子</p>
<hr>
<ul>
<li>程序开发调优 ：广播大变量</li>
</ul>
<p>有时在开发过程中，会遇到需要在算子函数中使用外部变量的场景（尤其是大变量，比如100M以上的大集合），那么此时就应该使用Spark的广播（Broadcast）功能来提升性能。<br>
默认情况下，Spark会将该变量复制多个副本，通过网络传输到task中，此时每个task都有一个变量副本。如果变量本身比较大的话（比如100M，甚至1G），那么大量的变量副本在网络中传输的性能开销，以及在各个节点的Executor中占用过多内存导致的频繁GC，都会极大地影响性能。<br>
广播后的变量，会保证每个Executor的内存中，只驻留一份变量副本，而Executor中的task执行时共享该Executor中的那份变量副本。</p>
<hr>
<ul>
<li>程序开发调优 ：使用Kryo优化序列化性能</li>
<li>1、在算子函数中使用到外部变量时，该变量会被序列化后进行网络传输。</li>
<li>2、将自定义的类型作为RDD的泛型类型时（比如JavaRDD，Student是自定义类型），所有自定义类型对象，都会进行序列化。因此这种情况下，也要求自定义的类必须实现Serializable接口。</li>
<li>3、使用可序列化的持久化策略时（比如MEMORY_ONLY_SER），Spark会将RDD中的每个partition都序列化成一个大的字节数组。</li>
<li>
</ul>
<p>Spark默认使用的是Java的序列化机制，你可以使用Kryo作为序列化类库，效率要比<br>
Java的序列化机制要高：</p>
<pre><code>        // 创建SparkConf对象。
        val conf = new SparkConf().setMaster(...).setAppName(...)
        // 设置序列化器为KryoSerializer。
        conf.set("spark.serializer", "org.apache.spark.serializer.KryoSerializer")
        // 注册要序列化的自定义类型。
        conf.registerKryoClasses(Array(classOf[MyClass1], classOf[MyClass2]))
</code></pre>
<hr>
<ul>
<li>程序开发调优 ：优化数据结构</li>
</ul>
<p>Java中，有三种类型比较耗费内存：</p>
<ul>
<li>
<p>1、对象，每个Java对象都有对象头、引用等额外的信息，因此比较占用内存空间。</p>
</li>
<li>
<p>2、字符串，每个字符串内部都有一个字符数组以及长度等额外信息。</p>
</li>
<li>
<p>3、集合类型，比如HashMap、LinkedList等，因为集合类型内部通常会使用一些内部类来封装集合元素，比如Map.Entry</p>
<p>Spark官方建议，在Spark编码实现中，特别是对于算子函数中的代码，尽量不要使用上述三种数据结构，尽量使用字符串替代对象，使用原始类型（比如Int、Long）替代字符串，使用数组替代集合类型，这样尽可能地减少内存占用，从而降低GC频率，提升性能。</p>
</li>
</ul>
<h2><a id="4__167"></a>4 总结</h2>
<p>因为开发程序调优相对成熟，所以在此参考大牛的笔记，加上自己的总结，一气呵成。</p>
<p>秦凯新 于深圳</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>