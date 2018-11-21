---
layout:     post
title:      Spark学习（八）---Spark streaming原理
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>这次我们介绍spark streaming,今天主要是原理和相关的操作</p>
<ul>
<li>Spark Streaming概念介绍</li>
<li>Spark Streaming的相关操作</li>
</ul>
<hr>
<h1><a id="1_Spark_Streaming_5"></a>1. Spark Streaming概念</h1>
<h4><a id="11Spark_Streaming_6"></a>1.1什么是Spark Streaming</h4>
<p>Spark Streaming类似于Apache Storm，用于流式数据的处理。根据其官方文档介绍，Spark Streaming有高吞吐量和容错能力强等特点。Spark Streaming支持的数据源有很多，例如：Kafka、Flume、Twitter、ZeroMQ和简单的TCP套接字等等。数据输入后可以用Spark的高度抽象操作如：map、reduce、join、window等进行运算。而结果也能保存在很多地方，如HDFS，数据库等。另外Spark Streaming也能和MLlib（机器学习）以及Graphx完美融合。<br>
<img src="https://img-blog.csdn.net/2018102211553674?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjIyOTA1Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<h4><a id="12_Spark_Streaming_9"></a>1.2 Spark Streaming的特点</h4>
<ol>
<li>易用，可以像编写离线批处理一样去编写流式程序，支持java/scala/python语言。</li>
<li>容错，SparkStreaming在没有额外代码和配置的情况下可以恢复丢失的工作。</li>
<li>易整合到Spark体系，流式处理与批处理和交互式查询相结合。</li>
</ol>
<h4><a id="13__SparkStreamingStorm_14"></a>1.3  SparkStreaming与Storm的对比</h4>
<p><img src="https://img-blog.csdn.net/20181022115834877?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjIyOTA1Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"><br>
<img src="https://img-blog.csdn.net/20181022115903466?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjIyOTA1Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"><br>
<strong>主要的区别如下：</strong></p>
<ul>
<li>
<p>Strom是一个纯实时的流式处理框架，即来一条数据就处理一条数据，这样势必集群内有频繁的网络通讯，吞吐量低。</p>
</li>
<li>
<p>SparkStreaming是微批处理框架，吞吐量高。</p>
</li>
<li>
<p>Strom的事务处理机制要比SparkStreaming的好，SparkStreaming中存在丢数据或者重复计算的问题，Storm中接受或拉取的每条数据可以准确的只处理一次。</p>
</li>
<li>
<p>Strom适合做简单的汇总型计算，SparkStreaming可以做复杂的计算，因为SparkStreaming是基于Dstream来开发的，Dstream可以抽出RDD（即Spark的核心），支持更多的复杂计算。</p>
</li>
<li>
<p>Strom支持动态资源的调整，而SparkStreaming是粗粒度的资源调度（新版本中即使有也是通过kill excutor的形式）。</p>
</li>
</ul>
<h1><a id="2_Spark_Streaming_27"></a>2. Spark Streaming原理</h1>
<p>Spark Streaming 是基于spark的流式批处理引擎，其基本原理是把输入数据以某一时间间隔批量的处理，当批处理间隔缩短到秒级时，便可以用于处理实时数据流。<br>
<img src="https://img-blog.csdn.net/20181022120710536?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjIyOTA1Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<h4><a id="21_Spark_Streaming_31"></a>2.1 Spark Streaming计算流程</h4>
<p>Spark Streaming是将流式计算分解成一系列短小的批处理作业。这里的批处理引擎是Spark Core，也就是把Spark Streaming的输入数据按照batch size（如1秒）分成一段一段的数据（Discretized Stream），每一段数据都转换成Spark中的RDD（Resilient Distributed Dataset），然后将Spark Streaming中对DStream的Transformation操作变为针对Spark中对RDD的Transformation操作，将RDD经过操作变成中间结果保存在内存中。整个流式计算根据业务的需求可以对中间的结果进行缓存或者存储到外部设备。下图显示了Spark Streaming的整个流程。<br>
<strong>下面是SparkStreaming架构图</strong><br>
<img src="https://img-blog.csdn.net/20181022121031777?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjIyOTA1Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<h4><a id="22_Saprk_Streaming_35"></a>2.2 Saprk Streaming的实时性</h4>
<p>对于实时性的讨论，会牵涉到流式处理框架的应用场景。Spark Streaming将流式计算分解成多个Spark Job，对于每一段数据的处理都会经过Spark DAG图分解以及Spark的任务集的调度过程。对于目前版本的Spark Streaming而言，其最小的Batch Size的选取在0.5~2秒钟之间（Storm目前最小的延迟是100ms左右），所以Spark Streaming能够满足除对实时性要求非常高（如高频实时交易）之外的所有流式准实时计算场景。</p>
<h4><a id="23_Spark_Streaming_37"></a>2.3 Spark Streaming的容错性</h4>
<p>对于流式计算来说，容错性至关重要。首先我们要明确一下Spark中RDD的容错机制。每一个RDD都是一个不可变的分布式可重算的数据集，其记录着确定性的操作继承关系（lineage），所以只要输入数据是可容错的，那么任意一个RDD的分区（Partition）出错或不可用，都是可以利用原始输入数据通过转换操作而重新算出的。<br>
<strong>对于Spark Streaming来说，其RDD的传承关系如下图所示：</strong><br>
<img src="https://img-blog.csdn.net/20181022121330206?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjIyOTA1Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"><br>
图中的每一个椭圆形表示一个RDD，椭圆形中的每个圆形代表一个RDD中的一个Partition，图中的每一列的多个RDD表示一个DStream（图中有三个DStream），而每一行最后一个RDD则表示每一个Batch Size所产生的中间结果RDD。我们可以看到图中的每一个RDD都是通过lineage相连接的，由于Spark Streaming输入数据可以来自于磁盘，例如HDFS（多份拷贝）或是来自于网络的数据流（Spark Streaming会将网络输入数据的每一个数据流拷贝两份到其他的机器）都能保证容错性，所以RDD中任意的Partition出错，都可以并行地在其他机器上将缺失的Partition计算出来。这个容错恢复方式比连续计算模型（如Storm）的效率更高。</p>
<h1><a id="3_DStream_43"></a>3. DStream</h1>
<h4><a id="31_DStream_44"></a>3.1 什么是DStream</h4>
<p>Discretized Stream是Spark Streaming的基础抽象，代表持续性的数据流和经过各种Spark算子操作后的结果数据流。在内部实现上，DStream是一系列连续的RDD来表示。每个RDD含有一段时间间隔内的数据，如下图：<img src="https://img-blog.csdn.net/20181022121442147?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjIyOTA1Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"><br>
对数据的操作也是按照RDD为单位来进行的:<br>
<img src="https://img-blog.csdn.net/201810221215060?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjIyOTA1Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"><br>
Spark Streaming使用数据源产生的数据流创建DStream，也可以在已有的DStream上使用一些操作来创建新的DStream。<br>
它的工作流程像下面的图所示一样，接受到实时数据后，给数据分批次，然后传给Spark Engine处理最后生成该批次的结果。<br>
<img src="https://img-blog.csdn.net/20181022121524354?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjIyOTA1Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<h1><a id="4_DStream_51"></a>4. DStream的相关操作</h1>
<p>DStream上的操作与RDD的类似，分为Transformations（转换）和Output Operations（输出）两种，此外转换操作中还有一些比较特殊的操作，如：updateStateByKey()、transform()以及各种Window相关的操作。</p>
<h4><a id="41_Transformations_on_DStreams_53"></a>4.1 Transformations on DStreams</h4>

<table>
<thead>
<tr>
<th>Transformations</th>
<th>Meaning</th>
</tr>
</thead>
<tbody>
<tr>
<td>map(func)</td>
<td>对DStream中的各个元素进行func函数操作，然后返回一个新的DStream</td>
</tr>
<tr>
<td>flatMap(func)</td>
<td>与map方法类似，只不过各个输入项可以被输出为零个或多个输出项</td>
</tr>
<tr>
<td>filter(func)</td>
<td>过滤出所有函数func返回值为true的DStream元素并返回一个新的DStream</td>
</tr>
<tr>
<td>repartition(numPartitions)</td>
<td>增加或减少DStream中的分区数，从而改变DStream的并行度</td>
</tr>
<tr>
<td>union(otherStream)</td>
<td>将源DStream和输入参数为otherDStream的元素合并，并返回一个新的DStream.</td>
</tr>
<tr>
<td>count()</td>
<td>通过对DStream中的各个RDD中的元素进行计数，然后返回只有一个元素的RDD构成的DStream</td>
</tr>
<tr>
<td>reduce(func)</td>
<td>对源DStream中的各个RDD中的元素利用func进行聚合操作，然后返回只有一个元素的RDD构成的新的DStream.</td>
</tr>
<tr>
<td>countByValue()</td>
<td>对于元素类型为K的DStream，返回一个元素为（K,Long）键值对形式的新的DStream，Long对应的值为源DStream中各个RDD的key出现的次数</td>
</tr>
<tr>
<td>reduceByKey(func, [numTasks])</td>
<td>利用func函数对源DStream中的key进行聚合操作，然后返回新的（K，V）对构成的DStream</td>
</tr>
<tr>
<td>join(otherStream, [numTasks])</td>
<td>输入为（K,V)、（K,W）类型的DStream，返回一个新的（K，（V，W））类型的DStream</td>
</tr>
<tr>
<td>cogroup(otherStream, [numTasks])</td>
<td>输入为（K,V)、（K,W）类型的DStream，返回一个新的 (K, Seq[V], Seq[W]) 元组类型的DStream</td>
</tr>
<tr>
<td>transform(func)</td>
<td>通过RDD-to-RDD函数作用于DStream中的各个RDD，可以是任意的RDD操作，从而返回一个新的RDD</td>
</tr>
<tr>
<td>updateStateByKey(func)</td>
<td>根据key的之前状态值和key的新值，对key进行更新，返回一个新状态的DStream</td>
</tr>
</tbody>
</table><ul>
<li>特殊的Transformations</li>
</ul>
<p>（1）UpdateStateByKey Operation<br>
UpdateStateByKey用于记录历史记录，保存上次的状态</p>
<p>（2）Window Operations(开窗函数)<br>
滑动窗口转换操作:<br>
滑动窗口转换操作的计算过程如下图所示，我们可以事先设定一个滑动窗口的长度（也就是窗口的持续时间），并且设定滑动窗口的时间间隔（每隔多长时间执行一次计算），然后，就可以让窗口按照指定时间间隔在源DStream上滑动，每次窗口停放的位置上，都会有一部分DStream被框入窗口内，形成一个小段的DStream，这时，就可以启动对这个小段DStream的计算。<br>
<img src="https://img-blog.csdn.net/20181022121921251?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjIyOTA1Ng==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"><br>
（1）红色的矩形就是一个窗口，窗口框住的是一段时间内的数据流。<br>
（2）这里面每一个time都是时间单元，在官方的例子中，每隔window size是3 time unit, 而且每隔2个单位时间，窗口会slide一次。<br>
所以基于窗口的操作，需要指定2个参数：</p>
<ul>
<li>window length - The duration of the window (3 in the figure)</li>
<li>slide interval - The interval at which the window-based operation is performed (2 in the figure).<br>
a.窗口大小，一段时间内数据的容器。<br>
b.滑动间隔，每隔多久计算一次。</li>
</ul>
<h4><a id="42_Output_Operations_on_DStreams_85"></a>4.2 Output Operations on DStreams</h4>
<p>Output Operations可以将DStream的数据输出到外部的数据库或文件系统，当某个Output Operations被调用时（与RDD的Action相同），spark streaming程序才会开始真正的计算过程。</p>

<table>
<thead>
<tr>
<th>Output Operations</th>
<th>Meaning</th>
</tr>
</thead>
<tbody>
<tr>
<td>print()</td>
<td>打印到控制台</td>
</tr>
<tr>
<td>saveAsTextFiles(prefix, [suffix])</td>
<td>保存流的内容为文本文件，文件名为"prefix-TIME_IN_MS[.suffix]".</td>
</tr>
<tr>
<td>saveAsObjectFiles(prefix, [suffix])</td>
<td>保存流的内容为SequenceFile，文件名为"prefix-TIME_IN_MS[.suffix]".</td>
</tr>
<tr>
<td>saveAsHadoopFiles(prefix, [suffix])</td>
<td>保存流的内容为hadoop文件，文件名为 “prefix-TIME_IN_MS[.suffix]”.</td>
</tr>
<tr>
<td>foreachRDD(func)</td>
<td>对Dstream里面的每个RDD执行func</td>
</tr>
</tbody>
</table><hr>
<h4><a id="spark_Streaming_97"></a>本次介绍结束，下次课程将以实例演示spark Streaming的数据实时处理。</h4>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>