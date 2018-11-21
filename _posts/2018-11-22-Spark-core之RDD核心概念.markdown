---
layout:     post
title:      Spark-core之RDD核心概念
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h2>一、Spark包括什么</h2>

<p><img alt="" class="has" height="306" src="https://img-blog.csdn.net/20180906095042758?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N0dWRlbnRfX3NvZnR3YXJl/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="593"></p>

<p>spark的核心是Spark Core，其中上面的Spark Sql对接的是Hive等结构化查询，Spark Streaming是对接的流式计算，后面的那两个也是主要用在科学任务中，<strong>但是他们的基础都是spark core，而Spark core的核心就是RDD操作</strong>，RDD的操作重要的就是算子，也就是说，掌握了算子基本上就掌握了spark的基础。</p>

<h2>二、RDD</h2>

<h3>1、是什么？</h3>

<p>   简单的一句话就是，为用户封装了底层对数据的复杂抽象和处理，给用户提供了对数据转换，求值等操作。</p>

<p>RDD的全称是，<span style="color:#000000;">Resilient Distributed Dataset，</span><span style="color:#FF0000;">弹性分布式数据集，弹性体现在哪？本人认为，弹性最主要的是体现在RDD能够通过依赖形成DGA，DGA就确定了RDD的一系列操作的集合，就相当于一套模板，而分布式就体现在可以将数据分布在各个分区上，能提高并行度，当然这可以动态调整，那么，在每个分区上执行一系列DGA的操作，如果是每个RDD都是窄依赖，那么就不用做聚合，那么这个速度是非常快的，所以说，RDD的弹性和分布式特点很显而易见。</span></p>

<h3><strong><span>2、做了什么？主要通过算子实现</span></strong></h3>

<p><strong><span>      </span></strong><img alt="" class="has" height="372" src="https://img-blog.csdn.net/20180906101256519?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N0dWRlbnRfX3NvZnR3YXJl/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="472"></p>

<h3><strong>3、特性：</strong></h3>

<p><strong>         不可变（只读），RDD一旦创建，内容是不可以变的，如果想修改内容就会创建一个新的RDD，其实和java中的String很像、</strong></p>

<p>       <strong>  可分区，RDD的数据存储在Executor中，一个Exeutor有多个分区，RDD的分区数可以根据需求动态改变，在Spark中，分区的多少就是并行度是多少。</strong></p>

<p><strong>        RDD的弹性，  表现在存储、分区、计算以及容错，  弹性指的是什么呢？其实是伸缩性和容错性的体现</strong></p>

<p><strong>         依赖，也就是，多个RDD之间可以构成先后顺序，形成DGA图，从而形成一系列操作</strong></p>

<p><strong>         缓存，因为RDD是只读的，所以如果是多个程序使用同一个RDD，则可以将RDD先缓存下来，待日后重用</strong></p>

<p><strong>         检查点，checkpoint，</strong><strong>RDD支持checkpoint将数据保存到持久化的存储中，这样就可以切断之前的血缘关系，因为checkpoint后的RDD不需要知道它的父RDDs了，它可以从checkpoint处拿到数据。</strong></p>

<p><strong>   下面是RDD特性详解</strong></p>

<h3><strong>4、特性之 弹性</strong></h3>

<p>   1)  存储弹性：  自动进行内存和磁盘数据存储的切换</p>

<p style="margin-left:0cm;">           Spark优先把数据放到内存中，如果内存放不下，就会放到磁盘里面，程序进行自动的存储切换</p>

<p>    2) <span style="color:#f33b45;"><strong> 容错弹性：基于血统的高效容错机制---很重要</strong></span></p>

<p style="margin-left:0cm;">    在RDD进行转换和动作的时候，会形成RDD的Lineage依赖链，当某一个RDD失效的时候，可以通过重新计算上游的RDD来重新生成丢失的RDD数据。</p>

<p> <strong>   3）数据分片的高度弹性</strong></p>

<p style="margin-left:0cm;">    可以根据业务的特征，<strong>动态调整数据分片的个数</strong>，提升整体的应用执行效率。</p>

<p>    RDD全称叫做<strong>弹性分布式数据集</strong>(Resilient Distributed Datasets)，它是一种<strong>分布式的内存抽象</strong>，<strong>表示一个只读的记录分区的集合，它只能通过其他RDD转换而创建</strong>，为此，RDD支持丰富的转换操作(如map, join, filter, groupBy等)，通过这种转换操作，新的RDD则包含了如何从其他RDDs衍生所必需的信息，所以说RDDs之间是有依赖关系的。基于RDDs之间的依赖，RDDs会形成一个有向无环图DAG，该<strong>DAG描述了整个流式计算的流程，实际执行的时候，RDD是通过血缘关系(Lineage)一气呵成的，即使出现数据分区丢失，也可以通过血缘关系重建分区</strong>，总结起来，<span style="color:#f33b45;"><strong>基于RDD的流式计算任务可描述为：从稳定的物理存储(如分布式文件系统)中加载记录，记录被传入由一组确定性操作构成的DAG，然后写回稳定存储</strong></span>。另外RDD还可以将数据集缓存到内存中，使得在多个操作之间可以重用数据集，基于这个特点可以很方便地构建迭代型应用(图计算、机器学习等)或者交互式数据分析应用。可以说Spark最初也就是实现RDD的一个分布式系统，后面通过不断发展壮大成为现在较为完善的大数据生态系统，<strong><span style="color:#FF0000;">简单来讲，</span></strong><strong><span style="color:#FF0000;">Spark-RDD</span><span style="color:#FF0000;">的关系类似于</span><span style="color:#FF0000;">Hadoop-MapReduce</span><span style="color:#FF0000;">关系。</span></strong></p>

<p>    4）计算弹性</p>

<p>    <strong>Task如果失败会自动进行特定次数的重试  和MR很像</strong></p>

<p style="margin-left:0cm;">       RDD的计算任务如果运行失败，会自动进行任务的重新计算，默认次数是4次。</p>

<p>   <strong> Stage如果失败会自动进行特定次数的重试</strong></p>

<p style="margin-left:0cm;">      如果<strong>Job的某个Stage阶段计算失败</strong>，框架也会自动进行任务的重新计算，默认次数也是4次。</p>

<p>  <strong> Checkpoint和Persist可主动或被动触发</strong></p>

<p style="margin-left:0cm;">    RDD可以通过Persist持久化将RDD缓存到内存或者磁盘，当再次用到该RDD时直接读取就行。也可以将RDD进行检查点，检查点会将数据存储在HDFS中，该RDD的所有父RDD依赖都会被移除。</p>

<p>   <strong> 数据调度弹性</strong></p>

<p style="margin-left:0cm;">    Spark把这个JOB执行模型抽象为通用的有向无环图DAG，可以将多Stage的任务串联或并行执行，调度引擎自动处理Stage的失败以及Task的失败。</p>

<h3>5、特性之 分区</h3>

<p>      分区是RDD内部并行计算的一个计算单元，RDD的数据集在逻辑上被划分为多个分片，每一个分片称为分区，分区的格式决定了并行计算的粒度，而每个分区的数值计算都是在一个任务中进行的，因此任务的个数，也是由RDD(准确来说是作业最后一个RDD)的分区数决定。    </p>

<p><strong>RDD分区的一个分区原则：尽可能是得分区的个数等于集群核心数目</strong></p>

<p>下面我们仅讨论Spark默认的分区个数，这里分别就<u>parallelize</u>和<u>textFile</u>具体分析其默认的分区数</p>

<p>无论是本地模式、Standalone模式、YARN模式或Mesos模式，我们都可以通过<span style="color:#f33b45;"><strong><u>spark.default.parallelism</u></strong></span>来配置其默认分区个数，若没有设置该值，则根据不同的集群环境确定该值</p>

<ul><li>本地模式：<strong>默认为本地机器的CPU数目，若设置了local[N],则默认为N</strong></li>
	<li>Apache Mesos：默认的分区数为8</li>
	<li>Standalone或YARN：<span style="color:#f33b45;"><strong>默认取集群中所有核心数目的总和</strong></span>，或者2，取二者的较大值</li>
</ul><p>结论：</p>

<ol><li>对于parallelize来说，没有在方法中的指定分区数，则默认为spark.default.parallelism</li>
	<li>对于textFile来说，没有在方法中的指定分区数，则默认为min(defaultParallelism,2)，而defaultParallelism对应的就是spark.default.parallelism。如果是<strong><span style="color:#f33b45;">从hdfs上面读取文件，其分区数为文件分片数(128MB/片)</span></strong></li>
	<li><strong><span style="color:#f33b45;">分区数就一个原则，默认尽可能的使用能用到的CPU等计算资源</span></strong></li>
</ol><p><img alt="" class="has" height="473" src="https://img-blog.csdn.net/20180906103255421?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N0dWRlbnRfX3NvZnR3YXJl/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="732"></p>

<blockquote>
<p><strong>     KV型自定义分区器需要继承 Partitioner </strong><br>
class CustomerPar(par:Int) extends org.apache.spark.Partitioner {<br>
   override def numPartitions:Int = par<br>
   override def getPartition(key: Any):Int = {<br>
      key.toString.toInt%par<br>
   }</p>
</blockquote>

<h3>6、RDD特性之 依赖</h3>

<p style="margin-left:0cm;"> RDDs通过操作算子进行转换，转换得到的新RDD包含了从其他RDDs衍生所必需的信息，RDDs之间维护着这种血缘关系，也称之为依赖。如下图所示，依赖包括两种，<strong>一种是窄依赖</strong>，RDDs之间分区是一一对应的，<strong>另一种是宽依赖</strong>，下游RDD的每个分区与上游RDD(也称之为父RDD)的每个分区都有关，是多对多的关系。<span style="color:#555555;"> </span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="230" src="https://img-blog.csdn.net/20180906103955235?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N0dWRlbnRfX3NvZnR3YXJl/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="400"></p>

<p style="margin-left:0cm;">    通过RDDs之间的这种依赖关系，<span style="color:#f33b45;"><strong>一个任务流可以描述为DAG</strong></span>(有向无环图)，如下图所示，<strong><span style="color:#f33b45;">在实际执行过程中宽依赖对应于Shuffle</span></strong>(图中的reduceByKey和join)，窄依赖中的所有转换操作可以通过类似于管道的方式一气呵成执行(图中map和union可以一起执行)。</p>

<p style="margin-left:0cm;"> </p>

<p><img alt="" class="has" height="300" src="https://img-blog.csdn.net/20180906104001691?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N0dWRlbnRfX3NvZnR3YXJl/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="500"></p>

<h3>7、特性之  缓存</h3>

<p style="margin-left:0cm;">  如果在应用程序中多次使用同一个RDD，可以将该RDD缓存起来，该RDD只有在第一次计算的时候会根据血缘关系得到分区的数据，在后续其他地方用到该RDD的时候，会直接从缓存处取而不用再根据血缘关系计算，这样就加速后期的重用。如下图所示，RDD-1经过一系列的转换后得到RDD-n并保存到hdfs，RDD-1在这一过程中会有个中间结果，如果将其缓存到内存，那么在随后的RDD-1转换到RDD-m这一过程中，就不会计算其之前的RDD-0了。</p>

<p><img alt="" class="has" height="300" src="https://img-blog.csdn.net/20180906104153929?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N0dWRlbnRfX3NvZnR3YXJl/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="500"></p>

<h3>8、特性之 checkpoint</h3>

<p>虽然<strong>RDD的血缘关系天然地可以实现容错</strong>，当RDD的某个分区数据失败或丢失，可以通过血缘关系重建。但是对于长时间迭代型应用来说，随着迭代的进行，RDDs之间的血缘关系会越来越长，一旦在后续迭代过程中出错，则需要通过非常长的血缘关系去重建，势必影响性能。为此<strong><span style="color:#f33b45;">，RDD支持checkpoint将数据保存到持久化的存储中</span></strong>，这样就可以切断之前的血缘关系，<span style="color:#f33b45;"><strong>因为checkpoint后的RDD不需要知道它的父RDDs了，它可以从checkpoint处拿到数据。</strong></span></p>

<p>       <span style="color:#f33b45;"> ★</span>也就说，checkpoint能够将数据进行持久化，而且最关键的，也就是和缓存最大的区别就是，checkpoint可以不用关心之前的RDD的依赖连，用到数据的时候，直接拿数据，而上面介绍的缓存，必须存在这个 依赖连，一般某个executor宕机，那之前的RDD链就找不到，同时在cache中的数据也会请清除，也就是，checkpoint具有更高的容错性。</p>

<p><img alt="" class="has" height="627" src="https://img-blog.csdn.net/20180906105130963?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N0dWRlbnRfX3NvZnR3YXJl/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="891"></p>

<p>怎么设置？</p>

<p>  1、通过sc设定检查点的地址， jar包内有效<br>
      sc.setCheckpointDir("hdfs://master01:9000/checkpoint")<br>
   2、rdd调用checkpoint      rdd.checkpoint<br><strong>持久机制策略</strong></p>

<p><img alt="" class="has" height="594" src="https://img-blog.csdn.net/20180906105301848?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N0dWRlbnRfX3NvZnR3YXJl/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="922"></p>

<p>另外解析：<a href="https://blog.csdn.net/imbetter/article/details/80023338" rel="nofollow">https://blog.csdn.net/imbetter/article/details/80023338</a></p>

<h3>9、rdd的任务切分和Stage</h3>

<p><img alt="" class="has" height="434" src="https://img-blog.csdn.net/20180906105651171?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N0dWRlbnRfX3NvZnR3YXJl/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="689"></p>

<p>          1、一个jar包就是一个应用程序<br>
          2、一个行动操作就会触发一个job<br>
          3、一个job包含多个stage，<strong>这些stage的运行通过有向无环图来运行</strong><br>
          4、一个stage中包含多个task，一个task处理一个分区的数据，所以分区数又叫并行度，</p>

<p>             spark中有两类task，一类是shuffleMapTask，一类是resultTask，详见<a href="https://blog.csdn.net/a1043498776/article/details/54889922" rel="nofollow">https://blog.csdn.net/a1043498776/article/details/54889922</a></p>

<p><span style="color:#f33b45;"><strong>为什么要有stage这个概念呢？ 其实这也是spark的高明之处，spark怎么才能实现MR中的map和reduce，怎么控制呢？通过使用stage</strong></span></p>

<p><img alt="" class="has" height="554" src="https://img-blog.csdn.net/20180906105952454?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N0dWRlbnRfX3NvZnR3YXJl/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="964"></p>

<p><span style="color:#f33b45;"><strong>执行代码从前往后执行，划分stage是从后往前划分，从最后往前扫描代码，当遇到宽依赖时，将宽依赖前面的所有代码划分为一个stage。</strong></span></p>

<h3>总结：</h3>

<p>1、给定一个RDD可以获得什么信息？</p>

<p>        1、分区数以及分区方式；2、由父RDDs衍生而来的相关依赖信息；3、计算每个分区的数据，计算步骤为：1）如果被缓存，则从缓存中取的分区的数据；2）如果被checkpoint，则从checkpoint处恢复数据；3）根据血缘关系计算分区的数据。</p>

<p>2、本文主要是围绕着RDD进行讲述， 主要是讲述了RDD的特性，总的来说，rdd基本特性就是弹性和分区，一个是提供了高容错性的DGA， 一个是提供了rdd的并行度，而缓存和checkpoint是从性能和安全上提升rdd，两者是相结合使用的，既有区别又要共同使用，要记住两个的区别是什么，以及持久化的策略是什么。</p>

<p>其次本文还简述了一个rdd的执行，其中先从spark任务中说明了任务的切分和执行的颗粒度以及顺序，当然，整个任务的执行的最小单元就是rdd，执行的结果无非就是从一个rdd变成新的rdd，然后从一个任务task变成另个task，在从一个stage转变成另一个stage，这才是一个job的结果，</p>

<p>当然，从stage的划分到rdd形成的DGA都离不开依赖，两种依赖的概念要理解。</p>            </div>
                </div>