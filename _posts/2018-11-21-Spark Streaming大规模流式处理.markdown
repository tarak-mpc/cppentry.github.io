---
layout:     post
title:      Spark Streaming大规模流式处理
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/snail_gesture/article/details/49968617				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>1.1概述： <br>
    Spark Streaming架构概述和原理 <br>
    Spark Streaming案例集锦 <br>
    源码分析与性能优化</p>

<p>1.2   Spark Streaming架构概述和原理 <br>
    What is Spark Streaming? <br>
        是规模的，可伸缩的，实时流处理。 <br>
<img src="https://img-blog.csdn.net/20151121220916416" alt="这里写图片描述" title=""> <br>
 Spark Streaming的数据来源除了上述kafka,flume,HDFS/S3,Kinsesis,Twitter之外，还可以来源TCP sockets网站发来的数据，并且可以使用高级函数例如，map,join,reduce和window，来构建复杂的算法。最后被处理过的数据也可以被保存在hdfs，Databases, Dashboards里面。并且可以用流处理来处理图计算和机器学习。 <br>
<img src="https://img-blog.csdn.net/20151121220943476" alt="这里写图片描述" title=""> <br>
在Spark Streaming内部实现是接收到输入数据之后，以时间为分片对数据进行批次处理。，切分好数据分片之后，Spark Engine对数据进行计算，最后，产生一批又一批的处理后的数据。对于每一批的处理batch是并行处理的。例如，一秒产生一批，如果前一秒的还没处理完，下一秒的将不会被计算，这时候就会产生阻塞。因此这里面的时间设置也是一个优化点。 <br>
<img src="https://img-blog.csdn.net/20151121221023985" alt="这里写图片描述" title=""> <br>
数据是串行输入的，每个batch处理是并行的。</p>

<p><strong>Discretized Streams (DStreams)</strong> <br>
<img src="https://img-blog.csdn.net/20151121221110521" alt="这里写图片描述" title=""> <br>
Discretized Streams 离散流，DStream就是一系列RDD的集合，随着时间的流逝RDD会不断地产生，这些RDD会被DSream管理和计算。流进来的数据会被DStream划分为不同的时间段，每个时间段都会产生很多RDD，每个时间段是有自己ID的，第一个时间区间是[0.1),左闭右开区间的，然后依次类推。 <br>
<img src="https://img-blog.csdn.net/20151121221124700" alt="这里写图片描述" title=""> <br>
每一个时间段进行单词统计，进行flatMap操作，单词统计，然后随着时间流逝依次类推。</p>

<p><img src="https://img-blog.csdn.net/20151121221205676" alt="这里写图片描述" title=""> <br>
<strong>batch处理流程：</strong> <br>
        这是Spark Streaming中的 lineage(血统)关系，处理的单位是每个  RDD， 首先对batch进行split操作，把batch转化成切片产生partition，这里面的并行计算指的是batch切分成分片partition并行计算，里面的切片是平行计算。并行计算中都是batch级别的，将最终的结果以batch保存。随着时间的流逝，每个时间段都是batch同样操作。 <br>
<strong>容错性：</strong> <br>
    图中的每个椭圆是一个RDD，椭圆里面的每个小圆是一个partition，图中的每一列多个RDD， <br>
    表示一个DStream(图中有三个RDD)，而每一行的最后一个RDD表示batch size所产生的中间结果。 <br>
    每个RDD是lineage(血统)关系的，并且Spark Streaming数据来源有多种，可以来自磁盘，如HDFS(多份拷贝)或 <br>
    是来自网络的数据流(Spark Streaming 网络输入的数据拷贝两份到其他机器上)，因此可以保证Spark  Streaming <br>
    很高的容错性。即使某个RDD上的partition出错，可以并行地将其他机器上将出错的partition计算出来。y</p>

<p><strong>Spark Streaming 有3种主要的运行场景：</strong> <br>
    1.     无状态操作： 每次操作都只是计算当前时间切片的内容，例如：每次只计算1秒钟时间切片中产生的数据的RDD。 <br>
    2.    有状态操作：  要不断的把当前和历史的时间切片的RDD累加计算，随着时间的流逝，计算的数据规模会越来越大。(例 <br>
            如：    updateStateByKey) <br>
    3.    window操作：   是针对特定时间段并以特定时间间隔为单位进行的滑动操作，例如：在以1秒为时间切片的情况下，我们要统计最近10分钟内Spark Streaming产生的数据，并且每个2分钟进行一次更新。</p>

<p><strong>1.3     Spark Streaming案例集锦</strong> <br>
 使用Twitter方式收集数据 <br>
<img src="https://img-blog.csdn.net/20151121221242083" alt="这里写图片描述" title=""> <br>
tweets是DStream，对它进行flatMap操作之后生成新的DStream. <br>
<img src="https://img-blog.csdn.net/20151121221304298" alt="这里写图片描述" title=""> <br>
将产生的数据保存到hdfs，上图是把结果保存到磁盘上。</p>

<p><strong>Window Operations</strong> <br>
<img src="https://img-blog.csdn.net/20151121221341432" alt="这里写图片描述" title=""> <br>
上图中每一个红色的方框表示一个窗口，窗口的长度为：3，滑动间隔：窗口操作的 <br>
时间间隔为：2，也就是说，每个2，对过去时间为3进行统计。注意：比如窗口长度为3 <br>
的话，则是左闭右开的，也就是说 window at time 3 是对time1 和time2 进行统计的，不 <br>
包含time3。 <br>
<img src="https://img-blog.csdn.net/20151121221351117" alt="这里写图片描述" title=""> <br>
假设对过去10分钟进行统计计算，Minutes(10),指的是过去10分钟，Seconds(1)每个1s，这个意思就是，每隔1s对过去10分钟的数据进行统计。上面的操作很容易就重读计算，所以继续往下看，对上面操作进行优化。 <br>
<img src="https://img-blog.csdn.net/20151121221424758" alt="这里写图片描述" title=""> <br>
假设我们现在要求出 t 到    t + 4    中所有状态的值。 <br>
滑动窗口的叠加处理上面已经介绍过了，我们现在来看增量处理。现在可以把    t-1    到    t+3 <br>
总和保存下来，然后再与t+4时刻相加，最后减去t-1时刻的就可以了，这个过程中我们只需要保存三个中间结果。 <br>
而(a)图的话，要保存5个中间结果，效率一下就提升了40%。如果数据量很大的话，这样的效果是非常明显的。</p>

<p>具体代码实现： <br>
<img src="https://img-blog.csdn.net/20151121221515727" alt="这里写图片描述" title=""> <br>
综上最后优化后的代码为： <br>
<img src="https://img-blog.csdn.net/20151121221536144" alt="这里写图片描述" title=""></p>

<p><strong>1.3    性能优化</strong> <br>
    <strong>Batch Size 设置的大小.</strong> <br>
 假设：现在有一个batch是10s,但是你处理的时候时间超过了10s,但是每个batch之间必须要保证前一个batch结束下面的batch才可以执行，因此，就会造成阻塞。 <br>
<strong>如果真的阻塞的话怎么办？</strong> <br>
<img src="https://img-blog.csdn.net/20151121221606658" alt="这里写图片描述" title=""> <br>
    <strong>优化内存使用</strong> <br>
数据放入内存，要存两块副本，而我们只操作一个副本，默认数据存入到内存是需要序列化的。我们对数据进行操作的 <br>
时候就需要反序列化，这中间就比较耗CPU。 <br>
    1.    如果内存足够的话，我们可以将序列化给关闭，直接写入内存。 <br>
    2.    如果内存不足的情况下，可以使用Kyro序列化器。 <br>
    3.    设置spark.cleaner.ttl参数：Spark Streaming会将接收到的数据全部保存在内存中，因此很多不用的数据任然会被 <br>
           保存在内存中。因此可以通过设置该参数的时长，来及时清理无用的数据。</p>

<p><strong>总结：</strong> <br>
    Spark Streaming提供了一套高效、可容错的准实时大规模流式处理框架，Spark Streaming能够集成spark批处理和交互查询，通过简单的接口就可以实现复杂算法等等优点，Spark Streaming在未来将会有更大的发展。 期待更多spark好消息。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>