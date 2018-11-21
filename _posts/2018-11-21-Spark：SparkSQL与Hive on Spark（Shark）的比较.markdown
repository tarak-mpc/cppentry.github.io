---
layout:     post
title:      Spark：SparkSQL与Hive on Spark（Shark）的比较
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>简要介绍了SparkSQL与Hive on Spark的区别与联系</p>

<p> </p>

<h3 id="一关于spark"><a name="t0"></a><strong>一、关于Spark</strong></h3>

<p><strong>简介</strong></p>

<p>在Hadoop的整个生态系统中，Spark和MapReduce在同一个层级，即主要解决分布式计算框架的问题。</p>

<p> </p>

<p><strong>架构</strong></p>

<p>Spark的架构如下图所示，主要包含四大组件：Driver、Master、Worker和Executor。</p>

<p><img alt="spark cluster" class="has" height="220" src="http://7xlhna.com1.z0.glb.clouddn.com/spark%20cluster.png" width="400"></p>

<p> </p>

<p><strong>Spark特点</strong></p>

<ul><li>Spark可以部署在YARN上</li>
	<li>Spark原生支持对HDFS文件系统的访问</li>
	<li>使用Scala语言编写</li>
</ul><p> </p>

<p><strong>部署模型</strong></p>

<ol><li>单机模型：主要用来开发测试。特点：Driver、Master、Worker和Executor都运行在同一个JVM进程之中。</li>
	<li>伪集群模型：主要用来开发测试。特点：Master、Worker都运行在同一个JVM进程之中；Master、Worker和Executor都运行于同一台机器，无法跨机器运行；</li>
	<li>独立集群（又叫做原生集群模式）：在集群规模不是非常大的情况下，可用于生产环境。特点：Master、Worker和Executor都运行于独立的JVM进程。</li>
	<li>YARN集群：YARN生态中的ApplicationMaster角色使用Apache开发好的Spark ApplicationMaster代替，每一个YARN生态中的NodeManager角色相当于一个Spark生态中的Worker角色，由NodeManger负责Executor的启动。</li>
	<li>Mesos集群：暂无详细调研。</li>
</ol><p> </p>

<p><strong>测试</strong></p>

<p>经过测试，在宿主系统为CentOS6.5上（3个节点），hadoop2.7.1 + hive1.2.1(pg为元数据库) + sqoop + flume1.6.0 + spark1.5.0可以部署。</p>

<p> </p>

<h3 id="二关于spark-sql"><a name="t1"></a><strong>二、关于Spark SQL</strong></h3>

<p><strong>简介</strong></p>

<p>它主要用于结构化数据处理和对Spark数据执行类SQL的查询。通过Spark SQL，可以针对不同格式的数据执行ETL操作（如JSON，Parquet，数据库）然后完成特定的查询操作。一般来说，Spark每支持一种新的应用开发，都会引入一个新的Context及相应的RDD，对于SQL这一特性来说，引入的就是SQLContext和SchemaRDD。注意：在Spark1.3之后，SchemaRDD已经更名为DataFrame，但它本质就类似一个RDD，因为可以将DataFrame无缝的转换成一个RDD。</p>

<p> </p>

<p><strong>架构</strong></p>

<p>Spark要很好的支持SQL，要完成解析(parser)、优化(optimizer)、执行(execution)三大过程。</p>

<p><img alt="spark cluster" class="has" height="300" src="http://7xlhna.com1.z0.glb.clouddn.com/spark_sql_architecture.png" width="500"></p>

<p><br>
处理顺序大致如下：</p>

<ol><li>SQlParser生成LogicPlan Tree；</li>
	<li>Analyzer和Optimizer将各种Rule作用于LogicalPlan Tree；</li>
	<li>最终优化生成的LogicalPlan生成SparkRDD；</li>
	<li>最后将生成的RDD交由Spark执行；</li>
</ol><p> </p>

<p><strong>Spark SQL的两个组件</strong></p>

<ol><li>SQLContext：Spark SQL提供SQLContext封装Spark中的所有关系型功能。可以用之前的示例中的现有SparkContext创建SQLContext。</li>
	<li>DataFrame：DataFrame是一个分布式的，按照命名列的形式组织的数据集合。<span style="color:#f33b45;">DataFrame基于R语言中的data frame概念，与关系型数据库中的数据库表类似</span>。通过调用将DataFrame的内容作为行RDD返回的rdd方法（RDD of Rows），可以将DataFrame转换成RDD。</li>
</ol><p>         <span style="color:#f33b45;"><strong>可以通过如下数据源创建DataFrame：已有的RDD、结构化数据文件、JSON数据集、Hive表、外部数据库。</strong></span></p>

<p> </p>

<p><strong>使用示例</strong></p>

<p>编写简单的scala程序，从文本文件中加载用户数据并从数据集中创建一个DataFrame对象。然后运行DataFrame函数，执行特定的数据选择查询。</p>

<p>文本文件customers.txt中的内容如下：</p>

<pre class="has">
<code>Tom,12
Mike,13
Tony,34
Lili,8
David,21
Nike,18
Bush,29
Candy,42</code></pre>

<p>编写Scala代码:</p>

<pre class="has">
<code class="language-java">import org.apache.spark._

object Hello {

    // 创建一个表示用户的自定义类
    case class Person(name: String, age: Int)

    def main(args: Array[String]) {

        val conf = new SparkConf().setAppName("SparkSQL Demo")
        val sc = new SparkContext(conf)

        // 首先用已有的Spark Context对象创建SQLContext对象
        val sqlContext = new org.apache.spark.sql.SQLContext(sc)

        // 导入语句，可以隐式地将RDD转化成DataFrame
        import sqlContext.implicits._

        // 用数据集文本文件创建一个Person对象的DataFrame
        val people = sc.textFile("/Users/urey/data/input2.txt").map(_.split(",")).map(p =&gt; Person(p(0), p(1).trim.toInt)).toDF()

        // 将DataFrame注册为一个表
        people.registerTempTable("people")

        // SQL查询
        val teenagers = sqlContext.sql("SELECT name, age FROM people WHERE age &gt;= 13 AND age &lt;= 19")

        // 输出查询结果，按照顺序访问结果行的各个列。
        teenagers.map(t =&gt; "Name: " + t(0)).collect().foreach(println)

        sc.stop()
        }
}</code></pre>

<p>如上所示，Spark SQL提供了十分友好的SQL接口，可以与来自多种不同数据源的数据进行交互，而且所采用的语法也是团队熟知的SQL查询语法。这对于非技术类的项目成员，如数据分析师以及数据库管理员来说，非常实用。</p>

<p> </p>

<p><strong>小结</strong></p>

<p>我们了解到Apache Spark SQL如何用熟知的SQL查询语法提供<span style="color:#f33b45;">与Spark数据交互</span>的SQL接口。Spark SQL是一个功能强大的库，组织中的非技术团队成员，如业务分析师和数据分析师，都可以用Spark SQL执行数据分析。</p>

<p> </p>

<p><strong>三、关于Hive on Spark</strong></p>

<p><strong>背景</strong></p>

<p>Hive on Spark是由Cloudera发起，由Intel、MapR等公司共同参与的开源项目，其目的是把Spark作为Hive的一个计算引擎，将Hive的查询作为Spark的任务提交到Spark集群上进行计算。通过该项目，可以提高Hive查询的性能，同时为已经部署了Hive或者Spark的用户提供了更加灵活的选择，从而进一步提高Hive和Spark的普及率。</p>

<p> </p>

<p><strong>简介</strong></p>

<p>Hive on Spark是从Hive on MapReduce演进而来，Hive的整体解决方案很不错，但是从查询提交到结果返回需要相当长的时间，查询耗时太长，这个主要原因就是由于Hive原生是基于MapReduce的，那么如果我们不生成MapReduce Job，而是生成Spark Job，就可以充分利用Spark的快速执行能力来缩短HiveQL的响应时间。</p>

<p>Hive on Spark现在是Hive组件(从Hive1.1 release之后)的一部分。</p>

<p> </p>

<p><strong>与SparkSQL的区别</strong></p>

<p>SparkSQL和Hive On Spark都是在Spark上实现SQL的解决方案。</p>

<p>Spark早先有Shark项目用来实现SQL层，不过后来推翻重做了，就变成了SparkSQL。</p>

<p>这是Spark官方Databricks的项目，Spark项目本身主推的SQL实现。</p>

<p>Hive On Spark比SparkSQL稍晚。</p>

<p>Hive原本是没有很好支持MapReduce之外的引擎的，而Hive On Tez项目让Hive得以支持和Spark近似的Planning结构（非MapReduce的DAG）。</p>

<p>所以在此基础上，Cloudera主导启动了Hive On Spark。这个项目得到了IBM，Intel和MapR的支持（但是没有Databricks）。</p>

<p> </p>

<p><strong>使用示例</strong></p>

<p>大体与SparkSQL结构类似，只是SQL引擎不同。部分核心代码如下：</p>

<pre class="has">
<code class="language-java">val hiveContext = new HiveContext(sc)

import hiveContext._

hql("CREATE TABLE IF NOT EXIST src(key INT, value STRING)")

hql("LOAD DATA LOCAL PATH '/Users/urey/data/input2.txt' INTO TABLE src")

hql("SELECT key, value FROM src ").collect().foreach(println)</code></pre>

<p> </p>

<p><strong>小结</strong></p>

<p><span style="color:#f33b45;"><strong>结构上Hive On Spark和SparkSQL都是一个翻译层，把一个SQL翻译成分布式可执行的Spark程序。</strong></span>比如一个SQL：</p>

<pre class="has">
<code class="language-sql">SELECT item_type, sum(price)
FROM item
GROUP BY item_type;</code></pre>

<p>上面这个SQL脚本交给Hive或者类似的SQL引擎，它会“告诉”计算引擎做如下两个步骤：</p>

<p>读取item表，抽出item_type,price这两个字段；</p>

<p>对price计算初始的SUM（其实就是每个单独的price作为自己的SUM）</p>

<p>因为GROUP BY说需要根据item_type分组，所以设定shuffle的key为item_type从第一组节点分组后分发给聚合节点，让相同的item_type汇总到同一个聚合节点，然后这些节点把每个组的Partial Sum再加在一起，就得到了最后结果。不管是Hive还是SparkSQL大致上都是做了上面这样的工作。</p>

<p>需要理解的是，Hive和SparkSQL都不负责计算，它们只是告诉Spark，你需要这样算那样算，但是本身并不直接参与计算。</p>

<p> </p>

<p>原文参考：<a href="https://blog.csdn.net/yeruby/article/details/51448188" rel="nofollow">https://blog.csdn.net/yeruby/article/details/51448188</a></p>

<p> </p>

<p> </p>

<h3>扩展：</h3>

<h1>Shark与Spark SQL关系</h1>

<p>在2014年7月1日的Spark Summit上，Databricks宣布终止对Shark的开发，将重点放到Spark SQL上。Databricks表示，Spark SQL将涵盖Shark的所有特性，用户可以从Shark 0.9进行无缝的升级。</p>

<p><img alt="Databricks" class="has" src="http://www.36dsj.com/wp-content/uploads/2014/12/212.png"><br>
本次Databricks推广的Shark相关项目一共有两个，分别是Spark SQL和新的Hive on Spark（HIVE-7292），在介绍这两个项目之前，我们首先关注下被终止的项目Shark。</p>

<p>Shark及项目终止原因</p>

<p>About Shark</p>

<p>Shark发布于3年前，那个时候，Hive可以说是SQL on Hadoop的唯一选择，负责将SQL编译成可扩展的MapReduce作业。鉴于Hive的性能以及与Spark的兼容，Shark项目由此而生。</p>

<p>Shark即Hive on Spark，本质上是通过Hive的HQL解析，把HQL翻译成Spark上的RDD操作，然后通过Hive的metadata获取数据库里的表信息，实际HDFS上的数据和文件，会由Shark获取并放到Spark上运算。</p>

<p>Shark的最大特性就是快和与Hive的完全兼容，且可以在shell模式下使用rdd2sql()这样的API，把HQL得到的结果集，继续在scala环境下运算，支持自己编写简单的机器学习或简单分析处理函数，对HQL结果进一步分析计算。</p>

<p>除去Spark本身的迭代计算，Shark速度快的原因还在于其本身的改造，比如：</p>

<p>partial DAG execution：对join优化，调节并行粒度，因为Spark本身的宽依赖和窄依赖会影响并行计算和速度<br>
基于列的压缩和存储：把HQL表数据按列存，每列是一个array，存在JVM上，避免了JVM GC低效，而压缩和解压相关的技术是Yahoo!提供的。<br>
终止Shark的原因</p>

<p>在会议上，Databricks表示，Shark更多是对Hive的改造，替换了Hive的物理执行引擎，因此会有一个很快的速度。然而，不容忽视的是，Shark继承了大量的Hive代码，因此给优化和维护带来了大量的麻烦。随着性能优化和先进分析整合的进一步加深，基于MapReduce设计的部分无疑成为了整个项目的瓶颈。</p>

<p>因此，为了更好的发展，给用户提供一个更好的体验，Databricks宣布终止Shark项目，从而将更多的精力放到Spark SQL上。</p>

<p>两个相关/替代项目介绍</p>

<p>About Spark SQL</p>

<p>既然不是基于Hive，Spark SQL究竟有什么样的改变，这里我们不妨看向 张包峰的博客。Spark新发布的Spark SQL组件让Spark对SQL有了别样于Shark基于Hive的支持。参考官方手册，具体分三部分：</p>

<p>其一，能在Scala代码里写SQL，支持简单的SQL语法检查，能把RDD指定为Table存储起来。此外支持部分SQL语法的DSL。<br>
其二，支持Parquet文件的读写，且保留Schema。<br>
其三，能在Scala代码里访问Hive元数据，能执行Hive语句，并且把结果取回作为RDD使用。<br>
第一点对SQL的支持主要依赖了Catalyst这个新的查询优化框架（下面会给出一些Catalyst的简介），在把SQL解析成逻辑执行计划之后，利用Catalyst包里的一些类和接口，执行了一些简单的执行计划优化，最后变成RDD的计算。虽然目前的SQL解析器比较简单，执行计划的优化比较通配，还有些参考价值，所以看了下这块代码。目前这个PR在昨天已经merge进了主干，可以在SQL模块里看到这部分实现，还有catalyst模块看到Catalyst的代码。下面会具体介绍Spark SQL模块的实现。</p>

<p>第二点对Parquet的支持不关注，因为我们的应用场景里不会使用Parquet这样的列存储，适用场景不一样。</p>

<p>第三点对Hive的这种结合方式，没有什么核心的进展。与Shark相比，Shark依赖Hive的Metastore，解析器等能把hql执行变成Spark上的计算，而Hive的现在这种结合方式与代码里引入Hive包执行hql没什么本质区别，只是把hive hql的数据与RDD的打通这种交互做得更友好了。</p>

<p>About HIVE-7292</p>

<p>HIVE-7292更像是Spark SQL成为标准SQL on Spark项目的补充，首先它是一个Hive on Spark Project，旨在服务已有Hive投入的机构，这个项目将Spark作为一个替代执行引擎提供给Hive，从而为这些机构提供一个迁往Spark的途径，提供一个更流畅的Hive体验。</p>

<p>参考原文：<a href="https://blog.csdn.net/gdp12315_gu/article/details/50148709" rel="nofollow">https://blog.csdn.net/gdp12315_gu/article/details/50148709</a></p>

<h1>spark SQL和hive到底什么关系</h1>

<p>历史上存在的原理，以前都是使用hive来构建数据仓库，所以存在大量对hive所管理的数据查询的需求。而hive、shark（hive on spark）、sparlSQL都可以进行hive的数据查询。shark是使用了hive的sql语法解析器和优化器，修改了执行器（mapreduce改为spark了），使之物理执行过程是跑在spark上；而sparkSQL是使用了自身的语法解析器、优化器和执行器，同时sparkSQL还扩展了接口，不单单支持hive数据的查询，可以进行多种数据源的数据查询。</p>            </div>
                </div>