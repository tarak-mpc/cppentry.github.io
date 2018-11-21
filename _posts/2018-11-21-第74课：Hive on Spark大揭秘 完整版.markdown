---
layout:     post
title:      第74课：Hive on Spark大揭秘 完整版
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/someby/article/details/83961533				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h3>内容：</h3>

<p>    1.Hive On Spark内幕<br>
    2.Hive on Spark实战</p>

<h3>一、Hive On Spark内幕</h3>

<p><br>
    1.Hive on spark 是hive 的一个子项目，它是指不通过mapReduce 作为唯一的查询引擎，而是将spark 作为底层的查询引擎。hive on spark 只适用于hive 在可预见的未来，很有可能Hive默认的底层引擎就从MapReduce 切换到Spark 了 。使用于将原来有的Hive 数据仓库以及数据统计分析替换为spark 引擎，作为全公司通用的大数据统计分析引擎。<br>
    2. 将Hive 表作为SparkRDD 来进行操作 <br>
    3. 使用hive 原语：对于一些针对于RDD的操作，比如groupByKey,sortedByKey等不使用Spark的transformation操作和原语。如果那样的话，那么就需要重新实现一套Hive 的原语，而且如果Hive 增加了新功能，那么又要实现新的spark 原语。因此选择将hive 的原语包装为针对于RDD的操作即可。 <br>
    4.新的执行计划生成机制：使用SparkCompiler 将逻辑执行计划，即可Operator Tree ,转换为Task Tree ,提交Spark Task 给 Spark 进行执行。sparkTask 包装了DAG ，DAG 包装为SparkWork，SparkTask 根据SparkWork 表示的DAG 计算。 <br>
    5. SparkContext生命周期：hive on Spark会为每个用户创建自己的会话，比如说执行一次Sql创建一个SparkContext，但是Spark不允许在一个JVM内穿概念多个SparkContext。因此需要在单独的JVM中启动每个会话的Sparkcontext，然后通过RPC与远程JVM中的Spark Context进行通信。 <br>
    6.本地和远程运行模式：Hive on spark 提供两种运行模式，本地和远程。如果将SparkMaster这是为local，比如set.spark.master=local 那么就是本地模式，sparkContext 与客户端运行在一个JVM中。否则如果将sparkMaster设置为master的地址，那么就是远程模式，sparkcontext会在远程JVM中启动，远程模式下每个用户session 都会创建一个sparkClient，sparkClient启动RemoveDriver，RemoveDriver负责创建SparkContext。<br>
    7.Map join Spark Sql默认对join是支持使用BroatCast机制，将小表广播到各个节点上，以进行join，但是问题是这会driver和worker带来很大的内存开销。因为广播的数据要一直报讯在Driver中，所以目前采取的措施是类似于MapReduce的Distribuesd cache机制，即提高HDFS replication factor 的赋值因子，让数据在每一个计算节点上都有一个备份，从而可以在本地进行读取数据。 <br>
    8.cache table：对于某些需要对一张表执行多次操作的场景，hive on spark 内部做了优化，即将要多次操作的表cache 到内存中以便于提升性能。但是这里要注意并不是所有的情况都会自动进行cache 所以说hive on spark 很有很多需要完善的地方。</p>

<h3>二、Hive on Spark实战</h3>

<p>由于版本问题，测试没有成功</p>            </div>
                </div>