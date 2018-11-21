---
layout:     post
title:      WordCount背后的数据流
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:14px;">第一阶段（<span style="font-family:Calibri;">1-3</span><span style="font-family:'宋体';">月）：会从浅入深，基于大量案例实战，深度剖析和讲解</span><span style="font-family:Calibri;">Spark</span><span style="font-family:'宋体';">，并且会包含完全从企业真实复杂业务需求中抽取出的案例实战。课程会涵盖</span><span style="font-family:Calibri;">Scala</span><span style="font-family:'宋体';">编程详解、</span><span style="font-family:Calibri;">Spark</span><span style="font-family:'宋体';">核心编程、</span><span style="font-family:Calibri;">Spark SQL</span><span style="font-family:'宋体';">和</span><span style="font-family:Calibri;">Spark Streaming</span><span style="font-family:'宋体';">、</span><span style="font-family:Calibri;">Spark GraphX</span><span style="font-family:'宋体';">、</span><span style="font-family:Calibri;">SparkR</span><span style="font-family:'宋体';">、</span><span style="font-family:Calibri;">Machine Learning</span><span style="font-family:'宋体';">、</span><span style="font-family:Calibri;">Spark</span><span style="font-family:'宋体';">内核以及源码剖析、性能调优、企业级案例实战等部分</span></span></p>
<p><span style="font-size:14px;">第二阶段（<span style="font-family:Calibri;">Spark</span><span style="font-family:'宋体';">超大规模大数据案例实战）：使用了</span><span style="font-family:Calibri;">Spark</span><span style="font-family:'宋体';">技术生态栈中的</span><span style="font-family:Calibri;">Spark Core</span><span style="font-family:'宋体';">、</span><span style="font-family:Calibri;">Spark SQL</span><span style="font-family:'宋体';">、</span><span style="font-family:Calibri;">Spark Streaming</span><span style="font-family:'宋体';">、</span><span style="font-family:Calibri;">SparkR</span><span style="font-family:'宋体';">、</span><span style="font-family:Calibri;">Machine Learning</span><span style="font-family:'宋体';">，进行离线计算和实时计算业务模块的开发、数据的关联性分析、用户行为模式和特征的训练与应用、用户网络的社区发现、用户影响力、能量传播、标签传播、标签推理、人群划分、年龄段预测、商品交易时序跳转</span></span></p>
<p>本期内容：</p>
<p>1 <span style="font-family:'宋体';">从数据流动视角解密</span><span style="font-family:Calibri;">WordCount</span></p>
<p>2 <span style="font-family:'宋体';">从</span><span style="font-family:Calibri;">RDD</span><span style="font-family:'宋体';">依赖关系的视角解密</span><span style="font-family:Calibri;">WordCount</span></p>
<p>3 DAG<span style="font-family:'宋体';">与</span><span style="font-family:Calibri;">Lineage</span><span style="font-family:'宋体';">的思考</span></p>
<p><span style="font-family:'宋体';"></span></p>
<p><strong>RDD<span style="font-family:'宋体';">数据流：</span></strong></p>
<p>1<span style="font-family:'宋体';">）</span><span style="font-family:'Times New Roman';">RDD</span><span style="font-family:'宋体';">的</span><span style="font-family:'Times New Roman';">5</span><span style="font-family:'宋体';">个接口，一个接口记录了从哪些父</span><span style="font-family:'Times New Roman';">RDD</span><span style="font-family:'宋体';">获取数据，一个接口记录从父</span><span style="font-family:'Times New Roman';">RDD</span><span style="font-family:'宋体';">的哪些分区获取分区数据；一个接口记录当前</span><span style="font-family:'Times New Roman';">RDD</span><span style="font-family:'宋体';">对分区的操作算子；</span></p>
<p>2）RDD的构建 —— 其中之一，从其他RDD转换（操作算子）得到 ；</p>
<p>每个RDD通过记录父RDD、分区数据对应关系 —— 最终形成一个血统图，这些图，记录了数据流和操作算子流。</p>
<p><span style="font-family:'宋体';"><strong>创建RDD：</strong></span></p>
<p><span style="font-family:'宋体';"></span></p>
<p></p>
<p>RDD可以从普通数组创建出来，也可以从文件系统或者HDFS中的文件创建出来。</p>
<p>举例：从普通数组创建RDD，里面包含了1到9这9个数字，它们分别在3个分区中。</p>
<p>scala&gt; val a = sc.parallelize(1 to 9, 3)</p>
<p>   可以从Driver内存中的集合构建，可以从外部存储系统构建，每个转换操作，都会构建出一个新的RDD，这个RDD会记录它的数据来源，和自己的操作，输入--操作——每个RDD都有，对应的，就形成了一个输入——操作——输入——操作.....的流图，也就是每个RDD对应的血统图。 </p>
<p></p>
<p><strong>Spark上下文<br></strong>　　Spark集群的执行单位是Application，任何提交的任务都会产生一个Application。一个Application只会关联上一个Spark上下文，也就是SparkContext。构建SparkContext时可以传入Spark相关配置，也就是SparkConf，它可以用来指定Application的名称，任务需要的CPU核数/内存大小，调优需要的配置等等。</p>
<p>  val conf = new SparkConf()<br>
　　conf.setAppName("WorkCount")<br>
　　val sc = new SparkContext(conf)</p>
<p>这三行语句创建了一个Spark上下文，并且运行时这个Application的名字就叫WordCount。</p>
<p><strong>WordCount背后的数据流：</strong></p>
<p><strong><img src="https://img-blog.csdn.net/20160115002659485?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></strong></p>
<p><span style="font-size:14px;">（1）TextFile<span> </span>
</span></p>
<p></p>
<p><span></span></p><pre><code class="language-java">lines = sc.textFile(file)</code></pre>
<p>从hdfs文件中创建了叫lines的RDD，它的每个元素就对应文件中的每一行，有了RDD我们就可以通过它提供的各种API来完成需要的业务功能。<br></p>
<p><span style="font-size:14px;">（2）map</span></p>
<p>map是对RDD中的每个元素都执行一个指定的函数来产生一个新的RDD。任何原RDD中的元素在新RDD中都有且只有一个元素与之对应。基于HadoopRDD产生的Partition去掉Key。</p>
<span style="font-size:14px;">（</span><span style="font-size:14px;">3）flatmap</span>
<p>  对Partition中的每一行单词进行拆分并合并为一个大集合，与map类似，区别是原RDD中的元素经map处理后只能生成一个元素，而原RDD中的元素经flatmap处理后可生成多个元素来构建新RDD。 </p>
<p>举例：对原RDD中的每个元素x产生y个元素（从1到y，y为元素x的值）</p>
<p></p><pre><code class="language-java">scala&gt; val a = sc.parallelize(1 to 4, 2)
scala&gt; val b = a.flatMap(x =&gt; 1 to x)
scala&gt; b.collect
res12: Array[Int] = Array(1, 1, 2, 1, 2, 3, 1, 2, 3, 4)</code></pre>
<p></p>
<p>Reduce</p>
<p>reduce将RDD中元素两两传递给输入函数，同时产生一个新的值，新产生的值与RDD中下一个元素再被传递给输入函数直到最后只有一个值为止。</p>
<pre><code class="language-java">scala&gt; val c = sc.parallelize(1 to 10)
scala&gt; c.reduce((x, y) =&gt; x + y)
res4: Int = 55</code></pre>
<p><span style="font-size:14px;">（4）ReduceByKey</span></p>
<p>reduceByKey就是对元素为KV对的RDD中Key相同的元素的Value进行reduce，因此，Key相同的多个元素的值被reduce为一个值，然后与原RDD中的Key组成一个新的KV对。</p>
<pre><code class="language-java">scala&gt; val a = sc.parallelize(List((1,2),(3,4),(3,6)))
scala&gt; a.reduceByKey((x,y) =&gt; x + y).collect
res7: Array[(Int, Int)] = Array((1,2), (3,10))</code></pre><img src="https://img-blog.csdn.net/20160115004245860?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><br><span style="font-size:14px;">第一个Stage（父Stage，基于内存进行迭代）</span>
<p><span style="font-size:14px;">包含5个RDD：HadoopRDD、mapParttionsRDD、</span><span style="font-family:'宋体';"><span style="font-size:14px;">mapParttionsRDD、</span><span style="font-family:'宋体';"><span style="font-size:14px;">mapParttionsRDD、<span style="font-family:'宋体';">mapParttionsRDD</span></span></span></span></p>
<p><img src="https://img-blog.csdn.net/20160115004558145?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><span style="font-size:14px;">注：上面ReduceByKey中Partition中(Hello ,4)为一个value值，而非键值对，因为再进行map操作（基于HadoopRDD产生的Partition去掉行的Key），进行了计算操作后，需要加上一个Key，这符合能量守恒，因为在saveAsTextFile中可以看到this.map(x=&gt;(NUllWriteble.get(),text(x.toString))),outputFormat时需要一个键值对的形式。</span></p>
<strong><span style="font-size:14px;">第二个Stage</span></strong>
<p><span style="font-size:14px;">包含shuffledRDD和MapPartitionRDD</span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;"></span></p>
<p>ＤＴ大数据梦工厂</p>
<p>新浪微博：<a href="http://www.weibo.com/ilovepains/" rel="nofollow">www.weibo.com/ilovepains/</a></p>
<p>微信公众号：<span style="font-family:Calibri;">DT_Spark</span></p>
<p>博客：<a href="/ilovepains" rel="nofollow">http://.blog.sina.com.cn/ilovepains</a></p>
<p>TEL:18610086859</p>
<p>Email:18610086859@vip.126.com</p>
<br><p><br></p>
<br>            </div>
                </div>