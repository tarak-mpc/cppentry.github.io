---
layout:     post
title:      Hadoop与Spark的核心组件对比
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u012050154/article/details/52484270				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1><span style="font-family:'Times New Roman';font-size:18px;">一、Hadoop的核心组件</span></h1>
<p><span style="font-family:'Times New Roman';font-size:18px;"><span style="font-family:'Times New Roman';font-size:18px;">          </span>Hadoop的组件如图所示，但核心组件包括：<u>MapReduce</u>和<u>HDFS</u>。</span></p>
<p style="text-align:center;"><span style="font-family:'Times New Roman';font-size:18px;"><img src="https://img-blog.csdn.net/20160910130959079" alt=""><br></span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;"><span style="font-family:'Times New Roman';font-size:18px;">          </span>1、HDFS的体系结构<br><span style="font-family:'Times New Roman';font-size:18px;">          </span>我们首先介绍HDFS的体系结构，HDFS采用了主从(Master/Slave)结构模型，一个HDFS集群是由一个NameNode和若干个DataNode组成的。其中NameNode作为主服务器，管理文件系统的命名空间和客户端对文件的访问操作;集群中的DataNode管理存储的数据。HDFS允许用户以文件的形式存储数据。从内部来看，文件被分成若干个数据块，而且这若干个数据块存放在一组DataNode上。NameNode执行文件系统的命名空间操作，比如打开、关闭、重命名文件或目录等，它也负责数据块到具体DataNode的映射。DataNode负责处理文件系统客户端的文件读写请求，并在NameNode的统一调度下进行数据块的创建、删除和复制工作。<br></span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;"><span style="font-family:'Times New Roman';font-size:18px;">          NameNode和DataNode都被设计成可以在普通商用计算机上运行。这些计算机通常运行的是GNU/Linux操作系统。HDFS采用Java语言开发，因此任何支持Java的机器都可以部署NameNode和DataNode。一个典型的部署场景是集群中的一台机器运行一个NameNode实例，其他机器分别运行一个DataNode实例。当然，并不排除一台机器运行多个DataNode实例的情况。集群中单一的NameNode的设计则大大简化了系统的架构。NameNode是所有HDFS元数据的管理者，用户数据永远不会经过NameNode。</span><br></span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;"><span style="font-family:'Times New Roman';font-size:18px;"><span style="font-family:'Times New Roman';font-size:18px;">          2、MapReduce</span><br></span></span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;"><span style="font-family:'Times New Roman';font-size:18px;"><span style="font-family:'Times New Roman';font-size:18px;"><span style="font-family:'Times New Roman';font-size:18px;">          接下来介绍MapReduce的体系结构，MapReduce是一种并行编程模式，这种模式使得软件开发者可以轻松地编写出分布式并行程序。在Hadoop的体系结构中，MapReduce是一个简单易用的软件框架，基于它可以将任务分发到由上千台商用机器组成的集群上，并以一种高容错的方式并行处理大量的数据集，实现Hadoop的并行任务处理功能。MapReduce框架是由一个单独运行在主节点上的JobTracker和运行在每个集群从节点上的TaskTracker共同组成的。主节点负责调度构成一个作业的所有任务，这些任务分布在不同的从节点上。主节点监控它们的执行情况，并且重新执行之前失败的任务;从节点仅负责由主节点指派的任务。当一个Job被提交时，JobTracker接收到提交作业和配置信息之后，就会将配置信息等分发给从节点，同时调度任务并监控TaskTracker的执行。</span></span></span></span></p>
<p></p>
<div style="text-align:center;"><img src="https://img-blog.csdn.net/20160909104139652" width="600" height="300" alt="" style="font-family:'Times New Roman';font-size:18px;"></div>
<span style="font-family:'Times New Roman';font-size:18px;"><span style="font-family:'Times New Roman';font-size:18px;"><span style="font-family:'Times New Roman';font-size:18px;"><span style="font-family:'Times New Roman';font-size:18px;">　　从上面的介绍可以看出，HDFS和MapReduce共同组成了Hadoop分布式系统体系结构的核心。HDFS在集群上实现了分布式文件系统，MapReduce在集群上实现了分布式计算和任务处理。HDFS在MapReduce任务处理过程中提供了文件操作和存储等支持，MapReduce在HDFS的基础上实现了任务的分发、跟踪、执行等工作，并收集结果，二者相互作用，完成了Hadoop分布式集群的主要任务。</span><br></span></span></span>
<p></p>
<p><span style="font-family:'Times New Roman';font-size:18px;"><span style="font-family:'Times New Roman';font-size:18px;"><span style="font-family:'Times New Roman';font-size:18px;"><span style="font-family:'Times New Roman';font-size:18px;"><span style="font-family:'Times New Roman';font-size:18px;"> 
         其它组件的介绍参见<a href="http://hadoop.apache.org/" rel="nofollow">Hadoop</a>、<a href="http://www.mamicode.com/info-detail-434461.html" rel="nofollow">Hadoop核心组件</a></span><br></span></span></span></span></p>
<h1><span style="font-family:'Times New Roman';font-size:18px;">二、Spark的核心组件概述</span></h1>
<p><span style="font-family:'Times New Roman';font-size:18px;">          了解Spark先了解一下它的核心组件：<span style="font-family:'Times New Roman';font-size:18px;"><u>Spark SQL</u></span><span style="font-family:'Times New Roman';font-size:18px;">、</span><u>Spark Streaming</u>、<u>Spark
 MLlib</u>和<u>Spark Graphx</u>。上图</span></p>
<p style="text-align:center;"><span style="font-family:'Times New Roman';font-size:18px;"><img src="https://img-blog.csdn.net/20160909095529964" alt=""><br></span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">        1、Spark SQL</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">        自Spark1.0版本以来，它就作为一个从Spark平台获取数据的渠道，早期用户比较喜爱Spark SQL提供的从现有Apache Hive表以及流行和Parquet列式存储格式中读取数据的支持。之后，随着Spark SQL对其他格式支持的增加（如比较流行的数据格式---JSON等），使得数据源更加方便的进行数据格式转换后融合到Spark平台中。并由API提供的密集优化器集合意味着过滤和列修剪在很多情况下都会被运用于数据源，极大的优化、减少了需要处理的数据量，显著提高了Spark的工作效率。更详细理解可以学习<a href="http://spark.apache.org/sql/" rel="nofollow">SparkSQL</a>、借鉴<a href="http://www.tuicool.com/articles/VZRBV3" rel="nofollow">整理对Spark
 SQL的理解</a></span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">      2、Spark Streaming</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">       Spark Streaming基于Spark Core实现了可扩展、高吞吐和容错的实时流处理。支持非常多的数据源（包括Kafka、Flume、HDFS、S3等等），处理的结果可以存储到HDFS、Database等中。它的原理就是将流式计算分解成一系列短小的批处理作业---以Spark作为批处理的引擎，将Spark Streaming的输入数据按照批处理尺寸分成一段一段的数据，再将每一段数据转换成Spark中的RDD，然后将Spark
 Streaming中对DStream的转换操作变为针对Spark中对RDD的转换操作，将RDD经过操作变成中间结果保存在内存中。整个Spark Streaming提供了一套高效、可容错的、实时的、大规模的流式处理框架。更详细理解可以学习<a href="http://spark.apache.org/streaming/" rel="nofollow">SparkStreaming</a>、借鉴<a href="http://www.tuicool.com/articles/iuMryu" rel="nofollow">Spark
 Streaming实时计算框架介绍</a>、<a href="http://www.aboutyun.com/thread-8901-1-1.html" rel="nofollow">Spark Streaming实例分析</a></span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">        3、Spark MLlib</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">        MLlib是Spark对常用的机器学习算法的实现库，同时含有相关的测试和数据生成器，包括分类、回归、聚类、协同过滤、降维及底层基本的优化元素。其中分类和回归的相关算法包括<u>SVM</u>、<u>逻辑回归</u>、<u>线性回归</u>、<u>朴素贝叶斯</u>、<u>决策树</u>等；协同过滤包括<u>交替最小二乘法（ALS）</u>；聚类有<u>KMeans</u>、<u>Streaming版本的KMeans</u>、<u>高斯混合（Gaussian
 mixture）</u>、<u>PIC（Power Iteration Clustering）</u>、<u>LDA（Latent Dirichlet Allocation）</u>等；降维实现了<u>SVD（Singular Value Decomposition）</u>和<u>PCA（Principal Compaonet Analysis）</u>；频繁模式挖掘实现有<u>FP-growth</u>。相信随着时间的推移，机器学习库会越来越完备，<a href="http://spark.apache.org/docs/latest/mllib-guide.html" rel="nofollow">Spark
 MLlib</a></span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">        4、Graphx</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">        Spark对图计算的支持，随着人工智能、机器学习的研究，<span style="font-family:'Times New Roman';font-size:18px;"><a href="http://spark.apache.org/graphx/" rel="nofollow">Graphx</a>也逐渐蓬勃发展。</span></span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;"><br></span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">        Spark整体架构如下图所示：</span></p>
<p style="text-align:center;"><span style="font-family:'Times New Roman';font-size:18px;"><img src="https://img-blog.csdn.net/20160910125100141" alt=""><br></span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">        Driver是用户编写的数据处理逻辑，这个逻辑包含用户创建的SparkContext，SparkContext是用户逻辑与Spark集群主要的交互接口，它会和Cluster Manager交互申请计算资源等；Cluster Manager总体负责集群的资源管理和调度（支持Standalone、Apache Mesos和Hadoop的YARN）；WorkNode则是集群中可以执行计算任务的节点；Excutor是一个WorkNode上为某应用程序启动的一个进程，负责运行任务并且将数据存在内存或者磁盘上；Task是被送到某个Executor上计算单元，每个应用都有独立的Executor计算最终在计算节点的Executor中执行：</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;"><span style="font-family:'Times New Roman';font-size:18px;">        </span>用户程序从提交到计算执行需要经历以下几个阶段：</span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;"><span style="font-family:'Times New Roman';font-size:18px;">        1）用户程序创建SparkContext时，新创建的SparkContext实例会连接到Cluster Manager，Cluster Manager会根据用户提交时设置的CPU和内存信息为本次提交分配计算资源，启动Executor；</span><br></span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;"><span style="font-family:'Times New Roman';font-size:18px;"><span style="font-family:'Times New Roman';font-size:18px;">        2）Driver会将用户程序划分为不同的执行阶段，每个执行阶段由一组完全相同的Task组成，这些Task分别作用于待处理数据的不同分区；在阶段划分完成和Task创建后，Driver会向Executor发送Task；</span><br></span></span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">        3）Executor在接收到Task后，会下载Task运行时依赖，在准备好Task的执行环境后开始执行Task，并将Task的运行状态汇报给Driver；</span><br></p>
<p><span style="font-family:'Times New Roman';font-size:18px;"><span style="font-family:'Times New Roman';font-size:18px;">        4）Driver会根据收到的Task的运行状态来处理不同的状态更新，Task分为两种：一种是Shuffle Map Task，它实现数据的重新洗牌，洗牌的结果保存到Executor所在节点的文件系统中；另外一种是Result Task，它负责生成结果数据；</span><br></span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;"><span style="font-family:'Times New Roman';font-size:18px;"><span style="font-family:'Times New Roman';font-size:18px;">        5）Driver会不断调用Task，将Task发送到Executor执行，在所有的Task都正确执行或超过执行次数的限制仍然没有执行成功时停止；</span><br></span></span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;"><br></span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;"><br></span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;"><br></span></p>
            </div>
                </div>