---
layout:     post
title:      Hadoop，HBase，Storm，Spark到底是什么？
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
Hadoop，HBase，Storm，Spark到底是什么？</h1>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="font-size:18px;"><strong>Hadoop</strong>=HDFS+Hive+Pig+...</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="font-size:18px;"><strong>HDFS</strong>: 存储系统<br><strong>MapReduce</strong>：计算系统<br><strong>Hive</strong>：提供给SQL开发人员（通过HiveQL）的MapReduce，基于Hadoop的数据仓库框架<br><strong>Pig</strong>：基于Hadoop的语言开发的<br><strong>HBase</strong>:NoSQL数据库<br><strong>Flume</strong>：一个收集处理Hadoop数据的框架<br><strong>Oozie</strong>：一个让用户以多种语言（如MapReduce，Pig和Hive）定义一系列作业的工作流处理系统<br><strong>Ambari</strong>：一个基于web的部署/管理/监控Hadoop集群的工具集<br><strong>Avro</strong>：允许编码Hadoop文件的schema的一种数据序列化系统<br><strong>Mahout</strong>：一个数据挖掘库，它包含了最流行的一些数据挖据算法，并且以MapReduce模型来实现他们<br><strong>Sqoop</strong>：一个从非Hadoop数据存储（如关系数据库和数据仓库）进来的移动数据到Hadoop中的连接工具<br><strong>HCatalog</strong>：一个中心化的元数据管理以及Apache Hadoop共享服务，它允许在Hadoop集群中的所有数据的统一视图，并允许不同的工具，包括Pig和Hive，处理任何数据元素</span><span style="font-size:18px;">，而无需知道身体在集群中的数据存储。</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="font-size:18px;"><strong>BigTop</strong>：为了创造一个更正式的程序或框架Hadoop的子项目及相关组件的目标提高Hadoop的平台，作为一个整体的包装和互操作性测试。</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="font-size:18px;"><strong>Apache  Storm</strong>：一个分布式实时计算系统，Storm是一个任务并行连续计算引擎。 Storm本身并不典型在Hadoop集群上运行，它使用Apache ZooKeeper的和自己的主/从工作</span><span style="font-size:18px;">进程，协调拓扑，主机和工作者状态，保证信息的语义。无论如何， Storm必定还是可以从HDFS文件消费或者从文件写入到HDFS。</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="font-size:18px;"><strong>Apache Spark</strong>：一种快速，通用引擎用于大规模数据处理，Spark是一个数据并行通用批量处理引擎。工作流中在一个类似的和怀旧风格的MapReduce中定义，但是，比传统</span><span style="font-size:18px;">Hadoop MapReduce的更能干。Apache Spark有其流API项目，该项目通过短间隔批次允许连续处理。Apache Spark本身并不需要Hadoop操作。但是，它的数据并行模式，需要稳定的数据优化使用共享文件系统。该稳定源的范围可以从S3，NFS或更典型地，HDFS。执行Spark应用程序并不需要Hadoop
 YARN。Spark有自己独立的主/服务器进程。然而，这是共同的运行使用YARN容器Spark的应用程序。此外，Spark还可以在Mesos集群上运</span><span style="font-size:18px;">行。</span></p>
            </div>
                </div>