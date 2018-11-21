---
layout:     post
title:      Spark调优之Tuning Spark(Part 1)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u011564172/article/details/73739721				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="概述">概述</h1>

<p>翻译Spark官方调优指南<a href="http://spark.apache.org/docs/latest/tuning.html" rel="nofollow" target="_blank">Tuning Spark</a>。</p>



<h1 id="data-serialization">Data Serialization</h1>

<p><strong>序列化</strong>在分布式程序中扮演着重要角色，序列化较慢或者序列化结果较大均会降低计算速度。Spark在易用性和性能之间做了权衡，提供了两种实现，如下</p>

<ul>
<li><strong>JavaSerializer :</strong> Spark默认的Serializer，基于java.io.ObjectOutputStream、java.io.ObjectInputStream实现，优点是，可以序列化任意实现Serializable接口的class，易用性强；缺点也明显，序列化速度慢，性能差，序列化的结果较大。</li>
<li><strong>KryoSerializer :</strong> <a href="https://github.com/EsotericSoftware/kryo" rel="nofollow" target="_blank">Kryo</a>相对于JavaSerializer性能更好，序列化结果更小，唯一明显的不足，需要注册(register)自定义的class类型，这也是KryoSerializer不是默认Serializer的主要原因。</li>
</ul>

<p>在代码中通过<strong>SparkConf</strong>设置使用KryoSerializer ，如下</p>

<pre> conf.set("spark.serializer", "org.apache.spark.serializer.KryoSerializer")
</pre>

<p>Serializer主要在两个阶段发挥作用：</p>

<ul>
<li>数据溢写磁盘、Shuffle中间结果写入磁盘</li>
<li>Shuffle阶段数据写入内存(Tungsten)</li>
</ul>

<p>上述第一点不仅减少了磁盘空间使用，也减少了后续传输Shuffle中间结果的网络开销，第二点是Tungsten项目优化内存的重点，即在内存中存储序列化的对象，大大降低内存开销。</p>

<p>使用KryoSerializer，需要注册自定义class，模板代码如下</p>

<pre>val conf = new SparkConf().setMaster(...).setAppName(...)
conf.registerKryoClasses(Array(classOf[MyClass1], classOf[MyClass2]))
val sc = new SparkContext(conf)
</pre>

<p>如果使用中遇到<strong>buffer limit exceeded</strong>异常，则需要增大<strong>spark.kryoserializer.buffer.max(默认64M)</strong>参数，更多使用及测试参考<a href="https://github.com/apache/spark/blob/fb5869f2cf94217b3e254e2d0820507dc83a25cc/core/src/test/scala/org/apache/spark/serializer/KryoSerializerSuite.scala" rel="nofollow" target="_blank">KryoSerializerSuite</a>。</p>

<p>最后，如果不注册class，Kryo仍然可以使用，但是性能会变差，因为每个序列化对象都要存储一份完整对象名(full class name)。</p>



<h1 id="memory-tuning">Memory Tuning</h1>

<p>主要从以下三个方面理解内存的调优</p>

<ul>
<li>数据(objects)占用空间的大小</li>
<li>访问数据(objects)的耗时</li>
<li>GC延时</li>
</ul>

<p>Java对象比占用空间比原始数据大，但访问Java对象更快，关于Java对象空间开销参考我的博客<a href="http://blog.csdn.net/u011564172/article/details/71170176" rel="nofollow" target="_blank">Spark 内存管理之Tungsten</a>中<strong>Spark中JVM的不足</strong>小节。Java对象空间开销大主要原因如下</p>

<ul>
<li>Java对象中额外存储header、hash信息</li>
<li>编码带来的开销，如UTF-8编码至少占2 byte</li>
<li>集合类，如List、Map等还存储了length、capacity、load factor等信息</li>
<li>基本数据类型被转成包装类存储在集合中，增加了对象的开销</li>
</ul>

<p>接下来的内容，依次是，内存管理的概述，查看数据占用空间，优化数据结构，存储序列化的数据及GC调优。</p>



<h3 id="memory-management-overview"><strong>Memory Management Overview</strong></h3>

<p>Spark将内存划分为<strong>Execution</strong>、<strong>Storage</strong>、<strong>Other</strong>三部分，Execution存储Shuffle过程中joins、sorts、aggregations的数据，Storage用于cache、broadcast及传输结果给driver。</p>

<p>Spark实现了两个内存管理器<strong>StaticMemoryManager</strong>、<strong>UnifiedMemoryManager</strong>，两者主要区别在于，后者Execution和Storage的界限是不固定的，可以互相借用对方内存，但是，当Storage借用了Execution内存且Execution内存不足时，Execution会要回Storage借用的内存，反之，Execution并不会返还借用的内存。这样处理使得内存的使用更为充分，UnifiedMemoryManager也成为Spark 1.6及以后版本默认的内存管理器。</p>

<p>更多内容及参数设置参考我的博客<a href="http://blog.csdn.net/u011564172/article/details/70183729" rel="nofollow" target="_blank">Spark 内存管理概述</a>，<a href="http://blog.csdn.net/u011564172/article/details/70183740" rel="nofollow" target="_blank">Spark 内存管理之StaticMemoryManager</a>，<a href="http://blog.csdn.net/u011564172/article/details/71170151" rel="nofollow" target="_blank">Spark 内存管理之UnifiedMemoryManager</a>。</p>



<h3 id="determining-memory-consumption"><strong>Determining Memory Consumption</strong></h3>

<p>估算数据的内存占用，能够更好的进行内存调优，最好的也是最简单的方式是，创建RDD并cache(MEMORY_ONLY或MEMORY_ONLY_SER)，在Web UI中Storage页面查看内存使用情况。</p>

<p>对于单个对象，Spark提供了<a href="https://github.com/apache/spark/blob/39e2bad6a866d27c3ca594d15e574a1da3ee84cc/core/src/test/scala/org/apache/spark/util/SizeEstimatorSuite.scala" rel="nofollow" target="_blank">SizeEstimator</a>工具查看其内存占用，可以用来估算broadcast变量内存占用，代码如下</p>

<pre>SizeEstimator.estimate(new java.lang.Boolean(true))
</pre>



<h3 id="tuning-data-structures"><strong>Tuning Data Structures</strong></h3>

<p>避免由于Java对象带来的内存开销，如下</p>

<ul>
<li>避免使用Java、Scala集合类(如List、Map)，使用数组、基本数据类型和<a href="http://fastutil.di.unimi.it/" rel="nofollow" target="_blank">fastutil</a></li>
<li>避免设计或使用带有嵌套和指针的class，如HashMap的设计</li>
<li>能使用number或enumeration类型作为key，不使用String</li>
<li>如果是64位机器并且内存小于32G，使用<strong>-XX:+UseCompressedOops</strong>参数压缩指针，参数可以配置在<a href="http://spark.apache.org/docs/latest/configuration.html#environment-variables" rel="nofollow" target="_blank">spark-env.sh</a></li>
</ul>



<h3 id="serialized-rdd-storage"><strong>Serialized RDD Storage</strong></h3>

<p>cache是Spark一个重要功能，StorageLevel选择MEMORY_ONLY_SER或MEMORY_AND_DISK_SER会降低内存开销，不足是序列化及反序列化带来的CPU开销，推荐使用Kryo，性能好。</p>



<h3 id="garbage-collection-tuning"><strong>Garbage Collection Tuning</strong></h3>

<p>当使用RDD的cache(persist)时，内存使用增加，GC成为关注的重点。Java判断对象是否可用进行清理，因此对象的数量是影响GC的一个因素，使用int[]而不是List&lt; Integer &gt;能显著减少对象数量。</p>

<p>内存管理中Execution和Storage之间的相关作用，也是影响GC的一个关键点，后续会介绍通过参数进行相关调整。</p>



<h4 id="measuring-the-impact-of-gc"><strong><em>Measuring the Impact of GC</em></strong></h4>

<p>GC调优首先要收集GC的相关信息，主要为GC频率和耗时，配置如下</p>

<pre>spark-submit 
--name "My app" 
--master local[*] 
--conf "spark.executor.extraJavaOptions= -verbose:gc -XX:+PrintGCDetails -XX:+PrintGCTimeStamps  -Xloggc:/tmp/executor.gc.out" 
--conf "spark.driver.extraJavaOptions= -verbose:gc -XX:+PrintGCDetails -XX:+PrintGCTimeStamps  -Xloggc:/tmp/driver.gc.out" 
myApp.jar
</pre>

<p>GC日志会写入对应的/tmp/executor.gc.out、/tmp/driver.gc.out文件，通过Spark Web UI观察Executor运行情况，查找运行慢的节点，查看对应节点的GC日志。</p>



<h4 id="advanced-gc-tuning"><strong><em>Advanced GC Tuning</em></strong></h4>

<p>进行GC调优前，先对GC机制作简单介绍。 <br>
<img src="https://img-blog.csdn.net/20170628174632605?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU2NDE3Mg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" title=""> <br>
JVM内存模型将heap划分为如上几部分，各部分比例为Eden:S0:S1=8:1:1，Young:Old=1:2，如下</p>

<table border="1" width="100">
   <tbody><tr>
        <td rowspan="3">Young Gen</td>
        <td>Eden</td>
        <td>8</td>
        <td rowspan='3"'>1</td>
    </tr>
    <tr>
        <td>Survivor0</td>
        <td>1</td>
    </tr>
    <tr>
        <td>Survivor1</td>
        <td>1</td>
    </tr>
    <tr>
        <td>Old Gen</td>
        <td colspan="2"> </td>
        <td>2</td>
    </tr>
</tbody></table>

<p><img src="https://img-blog.csdn.net/20170628174803819?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTU2NDE3Mg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" title=""> <br>
GC过程如上图，分为<strong>Minor GC</strong>和<strong>Major GC</strong>，如下</p>

<ul>
<li><strong>Minor GC</strong>：新生成的对象存放在Eden区，Eden区满了触发Minor GC，存活的对象转移到Survivor中的一个，Eden再次满了，检查Eden和存有对象的Survivor区，存活的对象转义到另一个Survivor区，如此反复上述过程，存活一定次数的对象放入Old区，此外，如果上述过程出现Survivor放不下全部存活对象时，也会放入Old区，最后，大对象会直接放入Old区。</li>
<li><strong>Major GC</strong>：Old区满了触发Major GC，Major GC会”Stop the World”，程序hang住且没有任何日志信息很可能是在执行Major GC，使用CMS垃圾回收器(<strong>-XX:+UseConcMarkSweepGC</strong>)能够降低”Stop the World”导致的延迟，当通过Web UI观察Spark程序GC时间较长时，使用该参数调优。</li>
</ul>

<p>上面介绍了JVM GC，Spark GC调优希望能够做到，Old区只是long-lived objects，Young区是short-lived objects，从而减少Major GC频率，具体方法如下</p>

<ul>
<li>通过Spark Web UI查看Major GC耗时，如果耗时较长，尝试使用CMS垃圾回收器，以及调整Execution内存，或者增大executor内存。</li>
<li>通过我们上面提到的<strong>executor.gc.out</strong>日志，查看Minor GC情况，如果Minor GC太过频繁，考虑增大Eden区，使用 <br>
-Xmn参数，增大为原有的4/3倍是一个经验值。</li>
<li>查看GC日志，如果Old区是接近于满的，尽可能减少cache功能的使用，或者通过<strong>spark.memory.storageFraction</strong>(UnifiedMemoryManager)参数控制storage部分内存大小，当然还可以通过降低Young区增大Old区，但效果并不会理想，根本上还是要增大executor内存。</li>
<li>G1垃圾回收器在某些场景下会显著提升，特别是高版本JVM中，逐渐趋于成熟，是GC调优的一个尝试，使用参数<strong>-XX:+UseG1GC</strong>。</li>
<li>内存使用估算，例如数据源是hdfs的情况，如果block大小是128M，executor同时运行4个task的话，那么内存使用大概是4 * 128M，如果使用了压缩格式，那么考虑解压后的大小。</li>
</ul>

<p>从上面可以看出，GC调优的目的是降低Major GC频率，手段集中在选择合适的垃圾回收器、调整Young和Old区大小、调整Spark内存管理器中各组成的占比、增大内存，其中JVM相关参数通过<strong>spark.executor.extraJavaOption</strong>配置。GC调优的技巧需要通过实践不断积累，环境不同效果可能完全不同，调优之中调是很重要的一步。</p>

<p>参考： <br>
<a href="http://spark.apache.org/docs/latest/tuning.html" rel="nofollow">Tuning Spark</a> <br>
<a href="http://www.cnblogs.com/hnrainll/archive/2013/11/06/3410042.html" rel="nofollow">Java 内存区域和GC机制</a> <br>
<a href="http://www.journaldev.com/2856/java-jvm-memory-model-memory-management-in-java" rel="nofollow">Java (JVM) Memory Model – Memory Management in Java</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>