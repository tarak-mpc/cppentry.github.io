---
layout:     post
title:      Spark Streaming（02）——Spark Streaming原理
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2><a id="Spark_Streaming_0"></a>Spark Streaming原理</h2>
<p>Spark Streaming 是基于spark的流式批处理引擎，其基本原理是把输入数据以某一时间间隔批量的处理，当批处理间隔缩短到秒级时，便可以用于处理实时数据流。<br>
<img src="https://img-blog.csdn.net/20181003115714151?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0ZlbmdnbXM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<h2><a id="Spark_Streaming_4"></a>Spark Streaming计算流程</h2>
<p>Spark Streaming是将流式计算分解成一系列短小的批处理作业。<br>
这里的批处理引擎是Spark Core，也就是把Spark Streaming的输入数据按照batch size（如1秒）分成一段一段的数据（Discretized Stream），每一段数据都转换成Spark中的RDD（Resilient Distributed Dataset），然后将Spark Streaming中对DStream的Transformation操作变为针对Spark中对RDD的Transformation操作，将RDD经过操作变成中间结果保存在内存中。<br>
整个流式计算根据业务的需求可以对中间的结果进行缓存或者存储到外部设备。<br>
下图显示了Spark Streaming的整个流程。<br>
<img src="https://img-blog.csdn.net/20181003115820832?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0ZlbmdnbXM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<h2><a id="Spark_Streaming_11"></a>Spark Streaming容错性</h2>
<p>对于流式计算来说，容错性至关重要。<br>
首先我们要明确一下Spark中RDD的容错机制。每一个RDD都是一个不可变的分布式可重算的数据集，其记录着确定性的操作继承关系（lineage），所以只要输入数据是可容错的，那么任意一个RDD的分区（Partition）出错或不可用，都是可以利用原始输入数据通过转换操作而重新算出的。</p>
<p>对于Spark Streaming来说，其RDD的传承关系如下图所示：<br>
<img src="https://img-blog.csdn.net/20181003120217201?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0ZlbmdnbXM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"><br>
图中的每一个椭圆形表示一个RDD，椭圆形中的每个圆形代表一个RDD中的一个Partition分区；<br>
图中每一列的多个RDD表示一个DStream（图中有三个DStream），而每一行最后一个RDD则表示每一个Batch Size所产生的中间结果RDD。<br>
我们可以看到图中的每一个RDD都是通过lineage相连接的，由于Spark Streaming输入数据可以来自于磁盘，例如HDFS或是来自于网络的数据流（Spark Streaming会将网络输入数据的每一个数据流拷贝两份到其他的机器），他们都能保证容错性，所以RDD中任意的Partition出错，都可以并行地在其他机器上将缺失的Partition计算出来。这个容错恢复方式比连续计算模型（如Storm）的效率更高。</p>
<h2><a id="Spark_Streaming_20"></a>Spark Streaming实时性</h2>
<p>Spark Streaming将流式计算分解成多个Spark Job，对于每一段数据的处理都会经过Spark DAG图分解以及Spark的任务集的调度过程。<br>
对于目前的Spark Streaming而言，其最小的Batch Size的选取在0.5~2秒钟之间（Storm目前最小的延迟是100ms左右），所以Spark Streaming能够满足除对实时性要求非常高（如高频实时交易）之外的所有流式准实时计算场景。</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>