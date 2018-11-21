---
layout:     post
title:      Spark征程——Spark基础2
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/paicMis/article/details/55120728				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><strong>核心内容</strong> <br>
<strong>1、spark的概念</strong> <br>
<strong>2、spark与mapreduce的比较</strong> <br>
<strong>3、spark中RDD的相关概念</strong> <br>
<strong>4、spark shell的使用</strong></p>

<p><strong>一、Spark的相关概念</strong>  <br>
Spark的概念：Spark是分布式的、主要基于内存的、特别适合于迭代计算的大数据计算框架。 <br>
接下来从3个方面去理解Spark的概念： </p>

<p><strong>分布式：</strong>所谓分布式就是有很多台机器在运行，每个机器运行任务中的一部分，提高了任务运行的效率。 </p>

<p><strong>主要基于内存：</strong> Spark在计算的过程中会优先将数据放在内存中，如果内存容量不足的话，Spark也会将数据放在磁盘上或者部分数据放在磁盘上进行计算，即Spark不仅可以计算内存中放的下的数据，一定也可以计算内存中放不下的数据，Spark适合各种规模的分布式数据的计算。  <br>
注意：在整个数据的计算过程中，我们肯定是希望数据是在内存中的，我们甚至不希望数据是在本地磁盘上的，当然也不希望数据是通过网络从其他机器上抓取过来的，本质上Spark优先考虑将数据放在内存中， <br>
实际上是对计算机物理资源的最大化利用。所以对于Spark的运行会耗费内存的观点肯定是错误的。</p>

<p>Hadoop每次计算数据的时候都要读写磁盘，而Spark优先考虑基于内存，也就是说每次Spark计算的结果优先考虑放在内存中，下一次计算直接基于上次内存中计算的结果，这是Spark可以高速运行的主要原因之一</p>

<p><strong>迭代式计算：</strong>迭代式计算是Spark真正的精髓所在。  <br>
Hadoop的MapReduce与Spark最大的不同在于迭代模型，Spark在处理完一个阶段之后，处理的结果可以继续在其他节点上进行下一个阶段的计算，还可以有很多的阶段，不仅仅只有map和reduce两个阶段， <br>
由于这种迭代式的特点，导致Spark更加灵活，也更加强大。  <br>
接下来讲一下Spark处理数据的来源与数据输出之后的去向：  <br>
处理数据来源：HDFS、Hbase、Hive、DB  <br>
处理数据输出：HDFS、HBase、Hive、DB，S3，HANA、Cassandra或直接将结果返还给客户端。 </p>

<p>谈一下Spark处理数据的3个优势：  <br>
Spark是对于海量数据的快速通用引擎。它的优势如下：  <br>
<strong>(1) 快</strong>  <br>
Spark运行快的原因一是因为运行过程中将中间结果存入内存，二是因为Spark运行前会将运行过程生成一张DAG图（有向无环图），当处理的源数据在文件中时，比Hadoop快10倍，当处理的源数据在内存中时， <br>
比Hadoop快100倍。  <br>
<strong>(2) 通用</strong>  <br>
可以使用Core/SQL/Streaming/Graphx/MLib/R/StructStreaming(2.0)等进行Spark计算。  <br>
处理的数据通用：可以处理HDFS/Hive/HBase/ES、JSON/JDBC等数据  <br>
Spark运行模式：Spark可以运行在本地模式、集群模式，集群模式时，可以运行在YARN上、Mesos上、Standalone集群上、云端  <br>
<strong>(3) 使用简单</strong>  <br>
可以使用Python、Scala、Java等开发。</p>

<p>Spark和Hadoop的比较 <br>
<img src="https://img-blog.csdn.net/20170214232840108?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcGFpY01pcw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><strong>三、Spark中RDD的相关概念</strong>  <br>
<strong>1、 定义</strong>  <br>
RDD是弹性分布式数据集（Resilient Distributed Dataset）的简称，其实就是分布式元素集合。在Spark中，对数据的所有操作不外乎创建RDD、转化已有的RDD、调用RDD操作进行求值。  <br>
RDD是Spark提出的最重要的核心抽象，Spark整个的编程、任务调度、容错和性能优化等等都是基于RDD之上的。  <br>
RDD是弹性分布式数据集，同理向Spark一样，我们也从3个方面去介绍RDD：  <br>
弹性：RDD的弹性体现在4个方面：  <br>
①：RDD会自动的进行内存和磁盘中数据的存储的切换；  <br>
②：基于Lineage的高效容错（例如如果第n个步骤出错，RDD会从第n-1个步骤恢复，血统容错），提高了错误恢复的步骤，不需要一切从头开始；  <br>
③：Task如果失败会自动进行特定次数的重试（默认4次）；  <br>
④：Stage（计算阶段）如果失败会自动进行特定次数的重试（可以只运行计算失败的阶段），只计算失败的数据分片,默认3次。  <br>
分布式：  <br>
所谓分布式就是RDD本身代表一系列的数据分片，这些分片会被放在Spark集群中的不同机器的计算节点上。然而RDD对于用户来说是透明的，也就是说用户根本就不用关心RDD对应的一些列的数据分片究竟放在哪里，用户只要对RDD进行计算处理就可以了。  <br>
数据集：  <br>
RDD本身代表要处理的数据集，一个RDD其本身在逻辑上抽象的代表了底层的一个文件或文件夹。  <br>
<strong>2、操作类型</strong>  <br>
RDD有两种类型的操作：Transformation操作、Action操作，Transformation操作和Action操作区别在于Spark计算RDD的方式不同。  <br>
1、Transformation操作会由一个RDD生成另一个新的RDD，生成的新的RDD是惰性求值的，只有在Action操作时才会被计算。  <br>
2、Action操作会对RDD计算出一个结果，并把结果返回到驱动器程序中，或者是把结果存储到外部存储系统中。  <br>
3、RDD会做缓存的几个情况：(如果都做缓存和Hadoop就没有什么区别了)  <br>
①、计算步骤特别耗时  <br>
②、计算链条很长  <br>
③、Shuffle之后,Shuffle需要从其他机器上面抓取数据  <br>
④、CheckPoint之前  <br>
最后在补充几句，RDD是通过Spark Context的形式进行创建的，Spark Context是集群的唯一接口，即我们做的一切工作都基于Spark context的；我们的RDD在逻辑上代表了HDFS上面具体的文件，但是实际上RDD底层是不同的分片，这些分片散落在集群中不同的机器上面；Spark中的一个分区的大小默认和一个block的大小的是相同的；NODE_LOCAL是本地磁盘上面，Process_Local是在内存中。</p>

<p><strong>四、Spark中shell的使用</strong>  <br>
先谈一下Spark处理数据的三个步骤：  <br>
<strong>1、读取数据：</strong>  <br>
读取数据一般是从HDFS上读取数，如sc.textfile(‘/user/input’)  <br>
对于Spark Core来说，将数据变为RDD；对于Spark Sql来说，是将数据变为DataFrame；对于Streaming来说，将数据变为DStream。  <br>
<strong>2、处理数据：</strong>  <br>
对于Spark Core来说，调用RDD的一系列方法；对于Spark Sql来说，是调用df的一系列方法；对于Streaming来说，是调用dstream一系列方法。这些方法大部分是高阶函数，使用各种方法来在内存中处理数据。  <br>
3、输出数据：输出数据也大部分是存入硬盘，  <br>
sc.SaveAsTextFile()  <br>
resultDF.write.jdbc()  <br>
resultDStream.foreach(Redis\HBase)。  <br>
接下来我们以WordCount程序为例进行具体说明：</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@SZB</span>-<span class="hljs-constant">L0032014</span> bin]<span class="hljs-comment"># ./spark-shell --master spark://SZB-L0032014:7077</span></code></pre>

<p><img src="https://img-blog.csdn.net/20170214233130645?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcGFpY01pcw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<pre class="prettyprint"><code class=" hljs fsharp"><span class="hljs-keyword">val</span> data = sc.textFile(<span class="hljs-string">"/word/wordcount"</span>)</code></pre>



<pre class="prettyprint"><code class=" hljs avrasm"> val flatted = data<span class="hljs-preprocessor">.flatMap</span>(_<span class="hljs-preprocessor">.split</span>(<span class="hljs-string">" "</span>))</code></pre>



<pre class="prettyprint"><code class=" hljs livecodeserver"> val mapped = flatted.map(<span class="hljs-built_in">word</span> =&gt; (<span class="hljs-built_in">word</span>,<span class="hljs-number">1</span>))</code></pre>



<pre class="prettyprint"><code class=" hljs fix"><span class="hljs-attribute"> val reduced </span>=<span class="hljs-string"> mapped.reduceByKey(_+_)</span></code></pre>



<pre class="prettyprint"><code class=" hljs avrasm"> reduced<span class="hljs-preprocessor">.saveAsTextFile</span>(<span class="hljs-string">"/word/output"</span>)</code></pre>

<p>接下来我们查一下运行结果：</p>



<pre class="prettyprint"><code class=" hljs coffeescript"> [root<span class="hljs-property">@SZB</span>-L0032017 ~]<span class="hljs-comment"># hadoop fs -cat /word/wordcount</span>
hello you
spark hadoop
java scala
java hello
[root<span class="hljs-property">@SZB</span>-L0032017 ~]<span class="hljs-comment"># hadoop fs -lsr /word/output</span>
<span class="hljs-attribute">lsr</span>: <span class="hljs-attribute">DEPRECATED</span>: Please use <span class="hljs-string">'ls -R'</span> instead.
-rw-r--r--   <span class="hljs-number">3</span> root root          <span class="hljs-number">0</span> <span class="hljs-number">2017</span>-<span class="hljs-number">02</span>-<span class="hljs-number">14</span> <span class="hljs-number">20</span>:<span class="hljs-number">02</span> /word/output/_SUCCESS
-rw-r--r--   <span class="hljs-number">3</span> root root         <span class="hljs-number">29</span> <span class="hljs-number">2017</span>-<span class="hljs-number">02</span>-<span class="hljs-number">14</span> <span class="hljs-number">20</span>:<span class="hljs-number">02</span> /word/output/part-<span class="hljs-number">00000</span>
-rw-r--r--   <span class="hljs-number">3</span> root root         <span class="hljs-number">29</span> <span class="hljs-number">2017</span>-<span class="hljs-number">02</span>-<span class="hljs-number">14</span> <span class="hljs-number">20</span>:<span class="hljs-number">02</span> /word/output/part-<span class="hljs-number">00001</span>
You have <span class="hljs-keyword">new</span> mail <span class="hljs-keyword">in</span> /<span class="hljs-reserved">var</span>/spool/mail/root
[root<span class="hljs-property">@SZB</span>-L0032017 ~]<span class="hljs-comment"># hadoop fs -cat /word/output/part-00000</span>
(scala,<span class="hljs-number">1</span>)
(hello,<span class="hljs-number">2</span>)
(java,<span class="hljs-number">2</span>)
[root<span class="hljs-property">@SZB</span>-L0032017 ~]<span class="hljs-comment"># hadoop fs -cat /word/output/part-00001</span>
(spark,<span class="hljs-number">1</span>)
(you,<span class="hljs-number">1</span>)
(hadoop,<span class="hljs-number">1</span>)</code></pre>

<p>从运行结果上来看结果是正确的。  <br>
接下来我们查看一下在程序运行过程中产生的RDD：</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">scala&gt; data.toDebugString
res4: String = 
(<span class="hljs-number">2</span>) MapPartitionsRDD[<span class="hljs-number">1</span>] <span class="hljs-keyword">at</span> textFile <span class="hljs-keyword">at</span> &lt;console&gt;:<span class="hljs-number">27</span> []
 |  /<span class="hljs-built_in">word</span>/wordcount HadoopRDD[<span class="hljs-number">0</span>] <span class="hljs-keyword">at</span> textFile <span class="hljs-keyword">at</span> &lt;console&gt;:<span class="hljs-number">27</span> []

scala&gt; flatted.toDebugString
res5: String = 
(<span class="hljs-number">2</span>) MapPartitionsRDD[<span class="hljs-number">2</span>] <span class="hljs-keyword">at</span> flatMap <span class="hljs-keyword">at</span> &lt;console&gt;:<span class="hljs-number">29</span> []
 |  MapPartitionsRDD[<span class="hljs-number">1</span>] <span class="hljs-keyword">at</span> textFile <span class="hljs-keyword">at</span> &lt;console&gt;:<span class="hljs-number">27</span> []
 |  /<span class="hljs-built_in">word</span>/wordcount HadoopRDD[<span class="hljs-number">0</span>] <span class="hljs-keyword">at</span> textFile <span class="hljs-keyword">at</span> &lt;console&gt;:<span class="hljs-number">27</span> []

scala&gt; mapped.toDebugString
res6: String = 
(<span class="hljs-number">2</span>) MapPartitionsRDD[<span class="hljs-number">3</span>] <span class="hljs-keyword">at</span> map <span class="hljs-keyword">at</span> &lt;console&gt;:<span class="hljs-number">31</span> []
 |  MapPartitionsRDD[<span class="hljs-number">2</span>] <span class="hljs-keyword">at</span> flatMap <span class="hljs-keyword">at</span> &lt;console&gt;:<span class="hljs-number">29</span> []
 |  MapPartitionsRDD[<span class="hljs-number">1</span>] <span class="hljs-keyword">at</span> textFile <span class="hljs-keyword">at</span> &lt;console&gt;:<span class="hljs-number">27</span> []
 |  /<span class="hljs-built_in">word</span>/wordcount HadoopRDD[<span class="hljs-number">0</span>] <span class="hljs-keyword">at</span> textFile <span class="hljs-keyword">at</span> &lt;console&gt;:<span class="hljs-number">27</span> []

scala&gt; reduced.toDebugString
res7: String = 
(<span class="hljs-number">2</span>) ShuffledRDD[<span class="hljs-number">4</span>] <span class="hljs-keyword">at</span> reduceByKey <span class="hljs-keyword">at</span> &lt;console&gt;:<span class="hljs-number">33</span> []
 +-(<span class="hljs-number">2</span>) MapPartitionsRDD[<span class="hljs-number">3</span>] <span class="hljs-keyword">at</span> map <span class="hljs-keyword">at</span> &lt;console&gt;:<span class="hljs-number">31</span> []
    |  MapPartitionsRDD[<span class="hljs-number">2</span>] <span class="hljs-keyword">at</span> flatMap <span class="hljs-keyword">at</span> &lt;console&gt;:<span class="hljs-number">29</span> []
    |  MapPartitionsRDD[<span class="hljs-number">1</span>] <span class="hljs-keyword">at</span> textFile <span class="hljs-keyword">at</span> &lt;console&gt;:<span class="hljs-number">27</span> []
    |  /<span class="hljs-built_in">word</span>/wordcount HadoopRDD[<span class="hljs-number">0</span>] <span class="hljs-keyword">at</span> textFile <span class="hljs-keyword">at</span> &lt;console&gt;:<span class="hljs-number">27</span> </code></pre>

<p>从运行结果可以看出，RDD之间是有依赖关系的。  <br>
之前我们讲到RDD本身代表要处理的数据集，但是实际上RDD底层是不同的分片，这些分片散落在集群中不同的机器上面，但是如何证明这些分片分布在不同的机器之上?  <br>
我们查看一下程序运行记录：  <br>
<img src="https://img-blog.csdn.net/20170214233145034?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcGFpY01pcw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><img src="https://img-blog.csdn.net/20170214233155128?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcGFpY01pcw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
<img src="https://img-blog.csdn.net/20170214233205128?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcGFpY01pcw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>