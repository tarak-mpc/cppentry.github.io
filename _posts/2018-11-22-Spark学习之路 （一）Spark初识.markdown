---
layout:     post
title:      Spark学习之路 （一）Spark初识
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1><a href="https://www.cnblogs.com/qingyunzong/p/8886338.html" rel="nofollow" id="cb_post_title_url">Spark学习之路 （一）Spark初识</a><a name="_label0"></a></h1>

<h2>一、官网介绍</h2>

<p><a name="_label0_0"></a></p>

<h3>1、什么是Spark</h3>

<p>官网地址：<a href="http://spark.apache.org/" rel="nofollow">http://spark.apache.org/</a></p>

<p><img alt="" class="has" height="387" src="https://images2018.cnblogs.com/blog/1228818/201804/1228818-20180419210341840-1023204495.png" width="833"></p>

<p><strong>Apache Spark™</strong>是用于大规模数据处理的统一分析引擎。</p>

<p>从右侧最后一条新闻看，Spark也用于AI人工智能</p>

<p>spark是一个实现快速通用的集群计算平台。它是由加州大学伯克利分校AMP实验室 开发的通用内存并行计算框架，用来构建大型的、低延迟的数据分析应用程序。它扩展了广泛使用的MapReduce计算</p>

<p>模型。高效的支撑更多计算模式，包括交互式查询和流处理。spark的一个主要特点是能够在内存中进行计算，及时依赖磁盘进行复杂的运算，Spark依然比MapReduce更加高效。</p>

<h3>2、为什么要学Spark</h3>

<p><strong>中间结果输出</strong>：基于MapReduce的计算引擎通常会将中间结果输出到磁盘上，进行存储和容错。出于任务管道承接的，考虑，当一些查询翻译到MapReduce任务时，往往会产生多个Stage，而这些串联的Stage又依赖于底层文件系统（如HDFS）来存储每一个Stage的输出结果。</p>

<p><strong>Spark是MapReduce的替代方案，而且兼容HDFS、Hive，可融入Hadoop的生态系统，以弥补MapReduce的不足。</strong></p>

<p><a href="http://www.cnblogs.com/qingyunzong/p/8886338.html#_labelTop" rel="nofollow">回到顶部</a><a name="_label1"></a></p>

<h2>二、Spark的四大特性</h2>

<p><a name="_label1_0"></a></p>

<h3>1、高效性</h3>

<p>运行速度提高100倍。</p>

<p>Apache Spark使用最先进的DAG调度程序，查询优化程序和物理执行引擎，实现批量和流式数据的高性能。</p>

<p><img alt="" class="has" height="255" src="https://images2018.cnblogs.com/blog/1228818/201804/1228818-20180419210923327-1692975321.png" width="1042"></p>

<p><a name="_label1_1"></a></p>

<h3>2、易用性</h3>

<p>Spark支持Java、Python和Scala的API，还支持超过80种高级算法，使用户可以快速构建不同的应用。而且Spark支持交互式的Python和Scala的shell，可以非常方便地在这些shell中使用Spark集群来验证解决问题的方法。</p>

<p><img alt="" class="has" height="251" src="https://images2018.cnblogs.com/blog/1228818/201804/1228818-20180419211144845-2020753937.png" width="1033"></p>

<p><a name="_label1_2"></a></p>

<h3>3、通用性</h3>

<p>Spark提供了统一的解决方案。Spark可以用于批处理、交互式查询（Spark SQL）、实时流处理（Spark Streaming）、机器学习（Spark MLlib）和图计算（GraphX）。这些不同类型的处理都可以在同一个应用中无缝使用。Spark统一的解决方案非常具有吸引力，毕竟任何公司都想用统一的平台去处理遇到的问题，减少开发和维护的人力成本和部署平台的物力成本。</p>

<p><img alt="" class="has" height="249" src="https://images2018.cnblogs.com/blog/1228818/201804/1228818-20180419211451611-367631364.png" width="1053"></p>

<p><a name="_label1_3"></a></p>

<h3>4、兼容性</h3>

<p>Spark可以非常方便地与其他的开源产品进行融合。比如，Spark可以使用Hadoop的YARN和Apache Mesos作为它的资源管理和调度器，器，并且可以处理所有Hadoop支持的数据，包括HDFS、HBase和Cassandra等。这对于已经部署Hadoop集群的用户特别重要，因为不需要做任何数据迁移就可以使用Spark的强大处理能力。Spark也可以不依赖于第三方的资源管理和调度器，它实现了Standalone作为其内置的资源管理和调度框架，这样进一步降低了Spark的使用门槛，使得所有人都可以非常容易地部署和使用Spark。此外，Spark还提供了在EC2上部署Standalone的Spark集群的工具。</p>

<p><img alt="" class="has" height="397" src="https://images2018.cnblogs.com/blog/1228818/201804/1228818-20180419211657058-248260284.png" width="1044"></p>

<p> </p>

<p><a href="https://mesos.apache.org/" rel="nofollow">Mesos</a>：Spark可以运行在Mesos里面（Mesos 类似于yarn的一个资源调度框架）</p>

<p><a href="http://spark.apache.org/docs/latest/spark-standalone.html" rel="nofollow">standalone</a>：Spark自己可以给自己分配资源（master，worker）</p>

<p><a href="https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html" rel="nofollow">YARN</a>：Spark可以运行在yarn上面</p>

<p> <a href="https://kubernetes.io/" rel="nofollow">Kubernetes</a>：Spark接收 <a href="https://kubernetes.io/" rel="nofollow">Kubernetes</a>的资源调度</p>

<h2>三、Spark的组成</h2>

<p>Spark组成(BDAS)：全称伯克利数据分析栈，通过大规模集成算法、机器、人之间展现大数据应用的一个平台。也是处理大数据、云计算、通信的技术解决方案。</p>

<p>它的主要组件有：</p>

<p><strong>SparkCore</strong>：将分布式数据抽象为弹性分布式数据集（RDD），实现了应用任务调度、RPC、序列化和压缩，并为运行在其上的上层组件提供API。</p>

<p><strong>SparkSQL</strong>：Spark Sql 是Spark来操作结构化数据的程序包，可以让我使用SQL语句的方式来查询数据，Spark支持 多种数据源，包含Hive表，parquest以及JSON等内容。</p>

<p><strong>SparkStreaming</strong>： 是Spark提供的实时数据进行流式计算的组件。</p>

<p><strong>MLlib</strong>：提供常用机器学习算法的实现库。</p>

<p><strong>GraphX</strong>：提供一个分布式图计算框架，能高效进行图计算。</p>

<p><strong>BlinkDB</strong>：用于在海量数据上进行交互式SQL的近似查询引擎。</p>

<p><strong>Tachyon</strong>：以内存为中心高容错的的分布式文件系统。</p>

<h2>四、应用场景</h2>

<p>Yahoo将Spark用在Audience Expansion中的应用，进行点击预测和即席查询等</p>

<p>淘宝技术团队使用了Spark来解决多次迭代的机器学习算法、高计算复杂度的算法等。应用于内容推荐、社区发现等<br>
腾讯大数据精准推荐借助Spark快速迭代的优势，实现了在“数据实时采集、算法实时训练、系统实时预测”的全流程实时并行高维算法，最终成功应用于广点通pCTR投放系统上。<br>
优酷土豆将Spark应用于视频推荐(图计算)、广告业务，主要实现机器学习、图计算等迭代计算。</p>            </div>
                </div>