---
layout:     post
title:      【Hadoop】Hadoop生态圈基本组件介绍
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/NextAction/article/details/78993014				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><img src="https://img-blog.csdn.net/20180107082841496?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTmV4dEFjdGlvbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""></p>
<p><span style="font-size:24px;">1. HDFS</span></p>
<p>HDFS（Hadoop分布式文件系统）源于Google在<span style="color:rgb(79,79,79);text-align:justify;">2003年10月发表的</span>GFS论文，HDFS是GFS的实现。HDFS通过流式数据访问，适合大数据集访问的应用程序。HDFS有一次写入多次读取的机制，数据已块的形式，同时分布在集群的不同物理机器上。</p>
<p><span style="font-size:24px;">2. MapReduce</span></p>
<p>MapReduce（分布式计算框架）源于Google在2004年12月发表的MapReduce论文，Hadoop MapReduce是Google MapReduce的克隆版。MapReduce是一种分布式计算模型，用以进行海量数据的计算，它将计算抽象成Map和Reduce两部分，其中Map对数据集上的独立元素进行指定的操作，生成键-值对形式中间结果。Reduce则对中间结果中相同“键”的所有“值”进行规约，以得到最终结果。</p>
<p><span style="font-size:24px;">3. YARN</span></p>
<p>YARN（分布式资源管理器）是第二代MapReduce，即MRv2，是从第一代MapReduce基础上演变而来的，主要为了解决原始Hadoop扩展性较差，不支持多计算框架而提出的。它的作用包括作业调度和资源管理等。</p>
<p><span style="font-size:24px;">4. Ambari</span></p>
<p>Ambari是一个基于Web的工具，用于配置、管理和监控Hadoop集群。Ambari以图形化的方式 查看MapReduce、Pig、Hive应用程序的运行状况，方便用户进行问题诊断和优化。</p>
<p><span style="font-size:24px;">5. HBase</span></p>
<p>HBase（分布式列存储数据库）源于Google的BigTable论文，发表于2006年11月，HBase是Google BigTable的实现。HBase是一个建立在HDFS上，面向结构化数据的可伸缩、高可靠、高性能、分布式和面向列的动态模式数据库。HBase采用BigTable模型，即增强的稀疏排序映射表（Key/Value），其中，键由行关键字、列关键字和时间戳构成。</p>
<p><span style="font-size:24px;">6. Hive</span></p>
<p>Hive（数据仓库）有Facebook开源，最初用于解决海量结构化的日志数据统计问题。Hive定义了一种类似于SQL的查询语言（HQL），将SQL转化为MapReduce任务在Hadoop上运行，通常用于离线分析。</p>
<p><span style="font-size:24px;">7. ZooKeeper</span></p>
<p>ZooKeeper（分布式协作服务）源自Google的Chubby论文，发表于2006年11月，ZooKeeper是Chubby实现版。ZooKeeper的主要目标是解决分布式环境下的数据管理问题，如统一命名、状态同步、集群管理等。Hadoop的很多组件都依赖Zookeeper，它运行在Hadoop集群上面，用于管理Hadoop操作。</p>
<p><span style="font-size:24px;">8. Pig</span></p>
<p>Pig（ad-hoc脚本）有Yahoo开源，其设计目的是提供一种基于MapReduce的ad-hoc数据分析工具。Pig定义了一种数据流语言——Pig Latin，它是MapReduce复杂编程的抽象。其编译器将Pig Latin翻译成MapReduce程序序列，将脚本转换为MapReduce任务在Hadoop上执行，同行用于离线分析。</p>
<p><span style="font-size:24px;">9. Sqoop</span></p>
<p><span style="color:rgb(79,79,79);text-align:justify;">Sqoop</span>（数据ETL/同步工具）是SQL-to-Hadoop的缩写，主要用于传统数据库和Hadoop之间的数据传输。数据的到导入和导出实质上是MapReduce程序，充分利用了MR的并行化和容错性。Sqoop利用数据库技术描述数据架构，用于咋关系数据库、数据仓库和Hadoop之间转换数据。</p>
<p><span style="font-size:24px;">10. Flume</span></p>
<p>Flume（日志收集工具）是Cloudera开源的日志收集系统，具有分布式、高可靠、高容错、易于定制和扩展的特点。他将数据从产生、传输、处理并最终写入目标的路径的过程抽象为数据流。总的来说，Flume是一个可扩展、适合复杂环境的海量日志收集系统，当然也可以用于收集其他类型数据。</p>
<p><span style="font-size:24px;">11. Mahout</span></p>
<p>Mahout源于2008年，最初是Apache Lucent的子项目，现在发展为Apache的顶级项目。Mahout是一个可扩展的机器学习和数据挖掘库。</p>
<p><span style="font-size:24px;">12. Spark</span></p>
<p>Spark（内存DAG计算模型）是一个处理Hadoop数据的、高速的、通用的计算引擎。Spark提供了一种简单而富有表达能力的编程模式，支持包括ETL、机器学习、数据流处理、图像计算等多种应用。</p>
<p><span style="font-size:24px;">13. Kafka</span></p>
<p>Kafka是Linkedin于2010年12月开源的消息系统，主要用于处理活跃的流式数据。活跃的流式数据在Web网站应用中非常常见，这些数据包括网站的PV（Page View），用户访问了什么内容，搜索了什么内容等。这些数据通常以日志的形式记录下来，然后每隔一段时间进行一次统计。</p>
            </div>
                </div>