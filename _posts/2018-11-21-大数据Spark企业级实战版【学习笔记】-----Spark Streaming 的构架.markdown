---
layout:     post
title:      大数据Spark企业级实战版【学习笔记】-----Spark Streaming 的构架
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/henni_719/article/details/77577054				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h4>1.3.2.1 Spark Streaming 的构架</h4>
<p>     计算流程：<strong>Spark Streaming是将流式计算分解成一系列短小的批处理作业</strong>。这里的批处理引擎是Spark，也就是<strong>把Spark Streaming的输入数据按照batch size(如1秒)分成一段一段的数据(DiscretizedStream)，每一段数据转换成Spark中的RDD</strong>，然后将Spark Streaming中<strong>对DStream</strong>的Transformation(转换)操作变为<strong>Spark中对RDD的Transformation(转换)操作</strong>，将RDD经过操作变成中间结果保存在内存中。整个流式计算根据业务的需求可以对中间的结果进行叠加，或者存储到外部设备上。</p>
<p>       Spark Streaming流程图如图1-46所示：</p>
<p>       <img src="https://img-blog.csdn.net/20170825161201635?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaGVubmlfNzE5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>       <strong>容错性：</strong>对于流式计算来说，容错性至关重要。<strong>每一个RDD都是一个不可变的分布式可重算的数据集，其记录着确定性的操作继承关系(linage)</strong>，<strong>只要输入数据是可容错的，那么任意一个RDD的分区(Partition)出错或者不可用，都是可以利用原始输入数据通过转换操作而重新算出的</strong>。</p>
<p>       对于Spark Streaming来说，其RDD的传承关系如图1-47所示：</p>
<p>      <img src="https://img-blog.csdn.net/20170825161222903?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaGVubmlfNzE5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>       图中的每一个椭圆形表示一个RDD，椭圆形中的每个圆形代表一个RDD中的一个Partition(分区)，图中的每一列的多个RDD表示一个DStream(图中有3个DStream)，而每一行最后一个RDD则表示每一个Batch Size所产生的中间结果RDD。</p>
<p>图中的每一个RDD都是通过lineage相连接，由于SparkStreaming输入数据可以来自于磁盘，例如HDFS或是来自于网络的数据流（<strong>Spark Streaming会将网络输入数据的每一个数据流复制两份到其他机器</strong>）都能保证容错性。所以RDD中任意的Partition出错，都可以并行地在其他机器上将缺失的Partition计算出来。这个容错恢复方式比连续计算模型(如Storm)的效率更高。</p>
<p>       <strong>实时性：Spark Streaming将流式计算分解成多个Spark Job，对于每一段数据的处理都会经过SparkDAG图分解，以及Spark的任务集的调度过程</strong>。对于目前版本的SparkStreaming而言，<strong>其最小的Batch Size的选取在0.5~2秒之间</strong>(Stor目前最小的延迟是100ms左右)，所以SparkStreaming能够满足除对实时性要求非常高(如高频实时交易)之外的所有流式实时计算场景。</p>
<p>       <strong>扩展性与吞吐量：</strong>Spark目前在EC2上已经能<strong>够线性扩展到100个节点(每个节点4Core)，可以以数秒的延迟处理6GB/s的数据量(60MB records/s)</strong>，<strong>其吞吐量也比流行的Storm高2~5倍</strong>。图1-48是Berkeley利用WordCount和Grep两个用例所做的测试，在Grep这个测试中，Spark Streaming中的每个节点的吞吐量是670KBrecords/s，而Storm是115KBrecords/s。</p>
<p>       <img src="https://img-blog.csdn.net/20170825161244758?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaGVubmlfNzE5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
            </div>
                </div>