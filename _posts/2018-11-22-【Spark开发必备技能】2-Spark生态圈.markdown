---
layout:     post
title:      【Spark开发必备技能】2-Spark生态圈
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h2 id="id-2-Spark生态圈-Spark生态圈架构图">Spark生态圈架构图</h2>

<p>    <img alt="" class="has" height="338" src="https://img-blog.csdn.net/20180802203241268?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xvdmVjaGVuZG9uZ3hpbmc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1178"></p>

<h3 id="id-2-Spark生态圈-1、Spark通用性较强">1、Spark通用性较强</h3>

<p>    Spark生态圈包含了Spark Core、Spark SQL、MLLib、GraphX、Spark Streaming和Spark Structured Streaming等组件，提供离线计算、实时计算、图形化处理和机器学习等能力，能够无缝的集成并提供一站式解决方案。</p>

<ul><li><strong>Spark Core：</strong>包含Spark的基本功能；尤其是定义RDD的API、操作以及这两者上的动作。其他Spark的库都是构建在RDD和Spark Core之上的</li>
	<li><strong>Spark SQL：</strong>提供Hive查询语言（HiveQL）以及SQL查询语言(如Mysql)与Spark进行交互的API。每个数据库表被当做一个RDD，Spark SQL查询被转换为Spark Core操作。</li>
	<li><strong>GraphX：</strong>控制图、并行图操作和计算的一组算法和工具的集合。GraphX扩展了RDD API，包含控制图、创建子图、访问路径上所有顶点的操作</li>
	<li><strong>MLlib：</strong>一个常用机器学习算法库，算法被实现为对RDD的Spark操作。这个库包含可扩展的学习算法，比如分类、回归等需要对大量数据集进行迭代的操作</li>
	<li><strong>Spark Streaming：</strong>对实时数据流进行处理和控制。Spark Streaming允许程序能够像普通RDD一样处理实时数据。</li>
	<li><strong>Structured Streaming：</strong>以结构化的方式操作流式数据，能够像使用Spark SQL处理批处理一样，处理流数据。基于Event-Time，相比于SparkStreaming的Receive-Time更精确。</li>
</ul><h3 id="id-2-Spark生态圈-2、Spark适应性较强">2、Spark适应性较强</h3>

<ul><li>    能够读取HDFS、Cassandra和HBase等离线数据</li>
	<li>    能够读取Kafka、Flume和Kinesis等实时数据</li>
	<li>    能够以Mesos、YARN或Standalone作为资源管理器调度JOB，进行集群资源的合理分配和容错，来完成Spark应用程序的计算</li>
</ul><h3 id="id-2-Spark生态圈-3、Spark与hadoop的区别"><strong>3、Spark与hadoop的区别</strong></h3>

<ul><li>Hadoop有两个核心模块，分布式存储模块HDFS和分布式计算模块Mapreduce</li>
	<li>Spark本身并没有提供分布式存储能力，因此Spark的分析大多依赖于Hadoop的分布式文件系统HDFS</li>
	<li>Hadoop的Mapreduce与Spark都可以进行数据计算，<strong>而相比于Mapreduce，Spark拥有DAG执行引擎，支持在内存中对数据进行迭代计算。官方提供的数据表明，如果数据由磁盘读取，速度是Hadoop MapReduce的10倍以上，如果数据从内存中读取，速度可以高达100多倍。</strong></li>
	<li>两者关系图如下：</li>
	<li><img alt="" class="has" height="222" src="https://img-blog.csdn.net/20180802203253891?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xvdmVjaGVuZG9uZ3hpbmc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="505"></li>
</ul>            </div>
                </div>