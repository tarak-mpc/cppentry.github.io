---
layout:     post
title:      Hive on Spark 与Spark SQL比较
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h4 id="hive-on-spark-与spark-sql比较">Hive on Spark 与Spark SQL比较</h4>

<p>背景</p>

<p>Hive on Spark是由Cloudera发起，由Intel、MapR等公司共同参与的开源项目，其目的是把Spark作为Hive的一个计算引擎，将Hive的查询作为Spark的任务提交到Spark集群上进行计算。通过该项目，可以提高Hive查询的性能，同时为已经部署了Hive或者Spark的用户提供了更加灵活的选择，从而进一步提高Hive和Spark的普及率。</p>

<p>ETL是Extract Transform Load三个英文单词的缩写 中文意思就是抽取、转换、加载。</p>

<p>Hive的两个作用：</p>

<p>数据仓库； <br>
计算引擎；  <br>
模式： <br>
Hive on Spark  <br>
利用Spark做计算引擎，用Hive SQL 对SQL语句进行封装；</p>

<p>Spark  SQL （Spark操作Hive里的数据）  <br>
Hive的作用：</p>

<p>Hive管理的数据本身就带有表结构，比如数据类型，字段名  <br>
 在spark机器学习数据处理过程中，直接读取文件的方式需要从文件转换到dataframe，这个时候最麻烦的就是Schema  <br>
 如果打通hive和spark，我们就可以直接在hive中定义好表结构,在spark中直接使用。非常省事，也不用关心hive表数据是哪种文件格式了，RCFILE，SequenceFile, TextFile等全部兼容</p>

<p>shark是使用了hive的sql语法解析器和优化器，修改了执行器，使之物理执行过程是跑在spark上；而sparkSQL是使用了自身的语法解析器、优化器和执行器，同时sparkSQL还扩展了接口，不单单支持hive数据的查询，可以进行多种数据源的数据查询。 <br>
Shark为了实现Hive兼容，在HQL方面重用了Hive中HQL的解析、逻辑执行计划翻译、执行计划优化等逻辑，可以近似认为仅将物理执行计划从MR作业替换成了Spark作业（辅以内存列式存储等各种和Hive关系不大的优化）； 同时还依赖Hive Metastore和Hive SerDe（用于兼容现有的各种Hive存储格式）。这一策略导致了两个问题， 第一是执行计划优化完全依赖于Hive，不方便添加新的优化策略； 二是因为MR是进程级并行，写代码的时候不是很注意线程安全问题，导致Shark不得不使用另外一套独立维护的打了补丁的Hive源码分支（至于为何相关修改没有合并到Hive主线，我也不太清楚）。 <br>
Spark SQL解决了这两个问题。</p>

<p>第一，Spark SQL在Hive兼容层面仅依赖HQL parser、Hive Metastore和Hive SerDe。也就是说，从HQL被解析成抽象语法树（AST）起，就全部由Spark SQL接管了。执行计划生成和优化都由Catalyst负责。借助Scala的模式匹配等函数式语言特性，利用Catalyst开发执行计划优化策略比Hive要简洁得多。去年Spark summit上Catalyst的作者Michael Armbrust对Catalyst做了一个简要介绍：2013 | Spark Summit。 <br>
第二，相对于Shark，由于进一步削减了对Hive的依赖，Spark SQL不再需要自行维护打了patch的Hive分支。Shark后续将全面采用Spark SQL作为引擎，不仅仅是查询优化方面。 <br>
Spark是新一代的计算引擎，相对于传统的MapReduce。而SQL基本上是每个类似引擎都绕不过必须实现的东西，毕竟数据分析的用户有很大一半并非CS背景，不可能指望他们写程序的。  <br>
SparkSQL和Hive On Spark都是在Spark上实现SQL的解决方案。Spark早先有Shark项目用来实现SQL层，不过后来推翻重做了，就变成了SparkSQL。这是Spark官方Databricks的项目，Spark项目本身主推的SQL实现。</p>

<p>Hive On Spark比SparkSQL稍晚。Hive原本是没有很好支持MapReduce之外的引擎的，而Hive On Tez项目让Hive得以支持和Spark近似的Planning结构（非MapReduce的DAG）。所以在此基础上，Cloudera主导启动了Hive On Spark。这个项目得到了IBM，Intel和MapR的支持（但是没有Databricks）。</p>

<p>作者：Xiaoyu Ma  <br>
 链接：<a href="https://www.zhihu.com/question/27011364/answer/36525479" rel="nofollow">https://www.zhihu.com/question/27011364/answer/36525479</a>  <br>
 来源：知乎  <br>
 著作权归作者所有，转载请联系作者获得授权。 <br>
Spark SQL 兼容了Hive的大部分语法和UDF，但是在处理查询计划的时候，使用了Catalyst框架进行优化，优化成适合Spark编程模型的执行计划 <br>
哪个效果好？</p>

<p>Spark SQL 几乎完全兼容 HIVE SQL 语法，只是 HIVE 特有的一些优化参数及极少用语法 不支持。 Impala SQL 与 HIVE SQL 高度兼容，但不局限于 HIVE 已有的查询 SQL，同时 Impala 还 支持 insert into。</p>

<p>与SparkSQL的区别</p>

<p>SparkSQL和Hive On Spark都是在Spark上实现SQL的解决方案。Spark早先有Shark项目用来实现SQL层，不过后来推翻重做了，就变成了SparkSQL。这是Spark官方Databricks的项目，Spark项目本身主推的SQL实现。Hive On Spark比SparkSQL稍晚。Hive原本是没有很好支持MapReduce之外的引擎的，而Hive On Tez项目让Hive得以支持和Spark近似的Planning结构（非MapReduce的DAG）。所以在此基础上，Cloudera主导启动了Hive On Spark。这个项目得到了IBM，Intel和MapR的支持（但是没有Databricks）。  <br>
Databricks Spark SQL的推出者：</p>

<p>While Spark SQL is becoming the standard for SQL on Spark, we do realize many organizations have existing investments in Hive. Many of these organizations, however, are also eager to migrate to Spark. The Hive community proposed a new initiative to the project that would add Spark as an alternative execution engine to Hive. For these organizations, this effort will provide a clear path for them to migrate the execution to Spark.  <br>
 And for organizations with legacy Hive deployments, Hive on Spark will provide them a clear path to Spark.</p>

<p>Spark SQL 连接Hive是由Spark SQL替代了Hive做SQL转换接口的作用；把Hive作为数据仓库；从转化到AST开始，后面的解析，优化，生成执行计划都由Spark SQL接管。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>