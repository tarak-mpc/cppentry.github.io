---
layout:     post
title:      SparkSQL与Hive on Spark的比较
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>简要介绍了SparkSQL与Hive on Spark的区别与联系</p>

<!--more-->

<p><br></p>



<h3 id="一关于spark"><strong>一、关于Spark</strong></h3>



<h5 id="简介"><strong>简介</strong></h5>

<p>在Hadoop的整个生态系统中，Spark和MapReduce在同一个层级，即主要解决分布式计算框架的问题。</p>

<p><br></p>



<h5 id="架构"><strong>架构</strong></h5>

<p>Spark的架构如下图所示，主要包含四大组件：Driver、Master、Worker和Executor。</p>

<p><img src="http://7xlhna.com1.z0.glb.clouddn.com/spark%20cluster.png" width="400" height="220" alt="spark cluster" align="center"></p>

<p><br></p>



<h5 id="spark特点"><strong>Spark特点</strong></h5>

<ul>
<li>Spark可以部署在YARN上</li>
<li>Spark原生支持对HDFS文件系统的访问</li>
<li>使用Scala语言编写</li>
</ul>

<p><br></p>



<h5 id="部署模型"><strong>部署模型</strong></h5>

<ol>
<li>单机模型：主要用来开发测试。特点：Driver、Master、Worker和Executor都运行在同一个JVM进程之中。</li>
<li>伪集群模型：主要用来开发测试。特点：Master、Worker都运行在同一个JVM进程之中；Master、Worker和Executor都运行于同一台机器，无法跨机器运行；</li>
<li>独立集群（又叫做原生集群模式）：在集群规模不是非常大的情况下，可用于生产环境。特点：Master、Worker和Executor都运行于独立的JVM进程。</li>
<li>YARN集群：YARN生态中的ApplicationMaster角色使用Apache开发好的Spark ApplicationMaster代替，每一个YARN生态中的NodeManager角色相当于一个Spark生态中的Worker角色，由NodeManger负责Executor的启动。</li>
<li>Mesos集群：暂无详细调研。</li>
</ol>

<p><br></p>



<h5 id="测试"><strong>测试</strong></h5>

<p>经过测试，在宿主系统为CentOS6.5上（3个节点），hadoop2.7.1 + hive1.2.1(pg为元数据库) + sqoop + flume1.6.0 + spark1.5.0可以部署。</p>

<p><br></p>



<h3 id="二关于spark-sql"><strong>二、关于Spark SQL</strong></h3>



<h5 id="简介-1"><strong>简介</strong></h5>

<p>它主要用于结构化数据处理和对Spark数据执行类SQL的查询。通过Spark SQL，可以针对不同格式的数据执行ETL操作（如JSON，Parquet，数据库）然后完成特定的查询操作。一般来说，Spark每支持一种新的应用开发，都会引入一个新的Context及相应的RDD，对于SQL这一特性来说，引入的就是SQLContext和SchemaRDD。注意：在Spark1.3之后，SchemaRDD已经更名为DataFrame，但它本质就类似一个RDD，因为可以将DataFrame无缝的转换成一个RDD。</p>

<p><br></p>



<h5 id="架构-1"><strong>架构</strong></h5>

<p>Spark要很好的支持SQL，要完成解析(parser)、优化(optimizer)、执行(execution)三大过程。</p>

<p><img src="http://7xlhna.com1.z0.glb.clouddn.com/spark_sql_architecture.png" width="500" height="300" alt="spark cluster" align="center"></p>

<p><br>
处理顺序大致如下：</p>

<ol>
<li>SQlParser生成LogicPlan Tree；</li>
<li>Analyzer和Optimizer将各种Rule作用于LogicalPlan Tree；</li>
<li>最终优化生成的LogicalPlan生成SparkRDD；</li>
<li>最后将生成的RDD交由Spark执行；</li>
</ol>

<p><br></p>



<h5 id="spark-sql的两个组件"><strong>Spark SQL的两个组件</strong></h5>

<ol>
<li>SQLContext：Spark SQL提供SQLContext封装Spark中的所有关系型功能。可以用之前的示例中的现有SparkContext创建SQLContext。</li>
<li>DataFrame：DataFrame是一个分布式的，按照命名列的形式组织的数据集合。DataFrame基于R语言中的data frame概念，与关系型数据库中的数据库表类似。通过调用将DataFrame的内容作为行RDD（RDD of Rows）返回的rdd方法，可以将DataFrame转换成RDD。可以通过如下数据源创建DataFrame：已有的RDD、结构化数据文件、JSON数据集、Hive表、外部数据库。</li>
</ol>

<p><br></p>



<h5 id="使用示例"><strong>使用示例</strong></h5>

<p>编写简单的scala程序，从文本文件中加载用户数据并从数据集中创建一个DataFrame对象。然后运行DataFrame函数，执行特定的数据选择查询。</p>

<p>文本文件customers.txt中的内容如下：</p>



<pre class="prettyprint"><code class=" hljs ">Tom,12
Mike,13
Tony,34
Lili,8
David,21
Nike,18
Bush,29
Candy,42</code></pre>

<p>编写Scala代码:</p>



<pre class="prettyprint"><code class=" hljs scala"><span class="hljs-keyword">import</span> org.apache.spark._

<span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">Hello</span> {</span>

    <span class="hljs-comment">// 创建一个表示用户的自定义类</span>
    <span class="hljs-class"><span class="hljs-keyword">case</span> <span class="hljs-keyword">class</span> <span class="hljs-title">Person</span><span class="hljs-params">(name: String, age: Int)</span></span>

    <span class="hljs-keyword">def</span> main(args: Array[String]) {

        <span class="hljs-keyword">val</span> conf = <span class="hljs-keyword">new</span> SparkConf().setAppName(<span class="hljs-string">"SparkSQL Demo"</span>)
        <span class="hljs-keyword">val</span> sc = <span class="hljs-keyword">new</span> SparkContext(conf)

        <span class="hljs-comment">// 首先用已有的Spark Context对象创建SQLContext对象</span>
        <span class="hljs-keyword">val</span> sqlContext = <span class="hljs-keyword">new</span> org.apache.spark.sql.SQLContext(sc)

        <span class="hljs-comment">// 导入语句，可以隐式地将RDD转化成DataFrame</span>
        <span class="hljs-keyword">import</span> sqlContext.implicits._

        <span class="hljs-comment">// 用数据集文本文件创建一个Person对象的DataFrame</span>
        <span class="hljs-keyword">val</span> people = sc.textFile(<span class="hljs-string">"/Users/urey/data/input2.txt"</span>).map(_.split(<span class="hljs-string">","</span>)).map(p =&gt; Person(p(<span class="hljs-number">0</span>), p(<span class="hljs-number">1</span>).trim.toInt)).toDF()

        <span class="hljs-comment">// 将DataFrame注册为一个表</span>
        people.registerTempTable(<span class="hljs-string">"people"</span>)

        <span class="hljs-comment">// SQL查询</span>
        <span class="hljs-keyword">val</span> teenagers = sqlContext.sql(<span class="hljs-string">"SELECT name, age FROM people WHERE age &gt;= 13 AND age &lt;= 19"</span>)

        <span class="hljs-comment">// 输出查询结果，按照顺序访问结果行的各个列。</span>
        teenagers.map(t =&gt; <span class="hljs-string">"Name: "</span> + t(<span class="hljs-number">0</span>)).collect().foreach(println)

        sc.stop()
        }
}</code></pre>

<p>如上所示，Spark SQL提供了十分友好的SQL接口，可以与来自多种不同数据源的数据进行交互，而且所采用的语法也是团队熟知的SQL查询语法。这对于非技术类的项目成员，如数据分析师以及数据库管理员来说，非常实用。</p>

<p><br></p>



<h5 id="小结"><strong>小结</strong></h5>

<p>我们了解到Apache Spark SQL如何用熟知的SQL查询语法提供与Spark数据交互的SQL接口。Spark SQL是一个功能强大的库，组织中的非技术团队成员，如业务分析师和数据分析师，都可以用Spark SQL执行数据分析。</p>

<p><br></p>



<h4 id="三关于hive-on-spark"><strong>三、关于Hive on Spark</strong></h4>



<h5 id="背景"><strong>背景</strong></h5>

<p>Hive on Spark是由Cloudera发起，由Intel、MapR等公司共同参与的开源项目，其目的是把Spark作为Hive的一个计算引擎，将Hive的查询作为Spark的任务提交到Spark集群上进行计算。通过该项目，可以提高Hive查询的性能，同时为已经部署了Hive或者Spark的用户提供了更加灵活的选择，从而进一步提高Hive和Spark的普及率。</p>

<p><br></p>



<h5 id="简介-2"><strong>简介</strong></h5>

<p>Hive on Spark是从Hive on MapReduce演进而来，Hive的整体解决方案很不错，但是从查询提交到结果返回需要相当长的时间，查询耗时太长，这个主要原因就是由于Hive原生是基于MapReduce的，那么如果我们不生成MapReduce Job，而是生成Spark Job，就可以充分利用Spark的快速执行能力来缩短HiveQL的响应时间。</p>

<p>Hive on Spark现在是Hive组件(从Hive1.1 release之后)的一部分。</p>

<p><br></p>



<h5 id="与sparksql的区别"><strong>与SparkSQL的区别</strong></h5>

<p>SparkSQL和Hive On Spark都是在Spark上实现SQL的解决方案。Spark早先有Shark项目用来实现SQL层，不过后来推翻重做了，就变成了SparkSQL。这是Spark官方Databricks的项目，Spark项目本身主推的SQL实现。Hive On Spark比SparkSQL稍晚。Hive原本是没有很好支持MapReduce之外的引擎的，而Hive On Tez项目让Hive得以支持和Spark近似的Planning结构（非MapReduce的DAG）。所以在此基础上，Cloudera主导启动了Hive On Spark。这个项目得到了IBM，Intel和MapR的支持（但是没有Databricks）。</p>

<p><br></p>



<h5 id="使用示例-1"><strong>使用示例</strong></h5>

<p>大体与SparkSQL结构类似，只是SQL引擎不同。部分核心代码如下：</p>



<pre class="prettyprint"><code class=" hljs sql">val hiveContext = new HiveContext(sc)

import hiveContext._

hql("<span class="hljs-operator"><span class="hljs-keyword">CREATE</span> <span class="hljs-keyword">TABLE</span> <span class="hljs-keyword">IF</span> <span class="hljs-keyword">NOT</span> EXIST src(<span class="hljs-keyword">key</span> <span class="hljs-keyword">INT</span>, <span class="hljs-keyword">value</span> STRING)<span class="hljs-string">")

hql("</span><span class="hljs-keyword">LOAD</span> DATA <span class="hljs-keyword">LOCAL</span> PATH <span class="hljs-string">'/Users/urey/data/input2.txt'</span> <span class="hljs-keyword">INTO</span> <span class="hljs-keyword">TABLE</span> src<span class="hljs-string">")

hql("</span><span class="hljs-keyword">FROM</span> src <span class="hljs-keyword">SELECT</span> <span class="hljs-keyword">key</span>, <span class="hljs-keyword">value</span><span class="hljs-string">").collect().foreach(println)</span></span></code></pre>

<p><br></p>



<h4 id="小结-1"><strong>小结</strong></h4>

<p>结构上Hive On Spark和SparkSQL都是一个翻译层，把一个SQL翻译成分布式可执行的Spark程序。比如一个SQL：</p>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">SELECT</span> item_type, <span class="hljs-aggregate">sum</span>(price)
<span class="hljs-keyword">FROM</span> item
<span class="hljs-keyword">GROUP</span> item_type;</span></code></pre>

<p>上面这个SQL脚本交给Hive或者类似的SQL引擎，它会“告诉”计算引擎做如下两个步骤：读取item表，抽出item_type,price这两个字段；对price计算初始的SUM（其实就是每个单独的price作为自己的SUM）因为GROUP BY说需要根据item_type分组，所以设定shuffle的key为item_type从第一组节点分组后分发给聚合节点，让相同的item_type汇总到同一个聚合节点，然后这些节点把每个组的Partial Sum再加在一起，就得到了最后结果。不管是Hive还是SparkSQL大致上都是做了上面这样的工作。</p>

<p>需要理解的是，Hive和SparkSQL都不负责计算，它们只是告诉Spark，你需要这样算那样算，但是本身并不直接参与计算。</p>

<p><br></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>