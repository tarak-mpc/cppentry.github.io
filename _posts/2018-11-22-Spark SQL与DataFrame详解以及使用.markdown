---
layout:     post
title:      Spark SQL与DataFrame详解以及使用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_37142346/article/details/80721177				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<blockquote>
  <p>Spark一站式的解决方案使得大数据技术快速发展，其中，最核心的部分当然也包括Spark SQL，它简单，常用，高效。与Spark Core中类似的，Spark SQL中最核心的部分是DataFrame抽象模型，本篇文章主要介绍Spark SQL的核心内容以及其简单使用。</p>
</blockquote>

<p>转载请标明原文地址：<a href="https://blog.csdn.net/qq_37142346/article/details/80721177" rel="nofollow">原文链接</a></p>

<p>Spark SQL是Spark的一个结构化数据处理模块，提供一个DataFrame编程模型抽象，可以看做是一个分布式SQL查询引擎。Spark SQL主要由Catalyst优化，Spark SQL内核，Hive支持三部分组成。</p>

<p>Catalyst优化处理查询语句的整个过程，包括解析，绑定，优化，物理计划等，主要由关系代数，表达式以及查询优化组成。</p>

<p>Spark SQL内核处理数据的输入输出，从不同的数据源（结构化Parquet文件和JSON文件，Hive表，外部数据库，创建RDD）获取数据，执行查询，并将结果输出成DataFrame。</p>

<p>Hive支持是值对Hive数据的处理，主要包括HiveSQL,MetaStore,SerDes,UDFS等。</p>

<h2 id="spark-sql架构">Spark SQL架构</h2>

<p>Spark SQL对SQL语句的处理和关系型数据库SQL处理类似，将SQL语句解析成一颗数（Tree）,然后通过规则（rule）的模式匹配，对树进行绑定，优化等，然后得到查询结果。</p>

<p>Tree的具体操作是通过TreeNode实现的；Rule是一个抽象类，是通过RuleExecutor完成的，应用与Spark SQL的Analyzer,Optimizer,Spark Planner等组件中，可以简便，模块化地对Tree进行Transform操作。</p>

<p>在整个SQL语句执行的过程中，主要依赖了优化框架Catalyst，把SQL语句解析，绑定，优化等，最终将逻辑计划优化后并且转换为物理执行计划，最后变成DataFrame模型。</p>

<p>Spark SQL的整个架构图如下所示： <br>
<img src="https://img-blog.csdn.net/20180617212351116?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MTQyMzQ2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<ol>
<li>使用SqlParser对SQL语句进行解析，生成Unresolved逻辑计划（没有提取schema信息）。</li>
<li>使用Catalyst分析器，结合数据字典（catalog）进行绑定，生成Analyzed逻辑计划，在此过程中，Schema Catalog则要提取schema信息。</li>
<li>使用Catalyst优化器对Analyzed逻辑计划进行优化，按照优化规则得到Optimized逻辑计划。</li>
<li>接着和Spark Planner交互，使用相应的策略将逻辑计划转换为物理计划，然后调用next函数，生成可执行物理计划。</li>
<li>调用toDF，最后生成DataFrame。</li>
</ol>

<p><strong>Spark SQL有以下几点特征：</strong> <br>
 1.兼容多种数据格式，如上面所说的parquet文件，HIve表，JSON文件等等。 <br>
 2.方便扩展，它的优化器，解析器都可以重新定义。 <br>
 3.性能优化方面：采用了内存列式存储，动态字节码生成等技术，还采用了内存缓存数据。 <br>
 4.支持多种语言操作，包括JAVA,SCALA,PYTHON,R语言等。</p>

<p><strong>Spark SQL性能</strong></p>

<p>1.内存列式存储</p>

<p>Spark SQL内部使用内存列式模式缓存表，仅扫描需要的列，并且自动调整压缩比使内存使用率和GC压力最小化，如果缓存了数据，则下次执行时不需要重复读取数据。</p>

<p>2.动态代码生成和字节码生成技术</p>

<p>对于一个简单的查询，如：</p>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">select</span> a+b <span class="hljs-keyword">from</span> <span class="hljs-keyword">table</span></span></code></pre>

<p>这个查询，在传统的方式中，会将SQL语句生成一个表达树，然后调用虚函数确认Add两边的数据类型，然后再调用虚函数计算装箱最后返回结果，计算时需要多次涉及虚函数的使用，打断了CPU的利用，减缓了执行效率。</p>

<p>因此，使用字节码技术会将表达式使用特定的代码动态 编译，为每一个查询生成自定义字节码，然后运行。最后需要说明的是DataFrame也是惰性的，在遇见Action操作的时候才会真正的去执行。</p>



<h2 id="创建dataframe">创建DataFrame</h2>

<p>创建一个json格式文件，内容如下所示;</p>



<pre class="prettyprint"><code class=" hljs json">{"<span class="hljs-attribute">name</span>":<span class="hljs-value"><span class="hljs-string">"Mirckel"</span></span>}
{"<span class="hljs-attribute">name</span>":<span class="hljs-value"><span class="hljs-string">"Andy"</span></span>,"<span class="hljs-attribute">age</span>":<span class="hljs-value"><span class="hljs-number">30</span></span>}
{"<span class="hljs-attribute">name</span>":<span class="hljs-value"><span class="hljs-string">"Jsutin"</span></span>,"<span class="hljs-attribute">age</span>":<span class="hljs-value"><span class="hljs-number">13</span></span>}</code></pre>

<p>创建以及相关操作如下代码所示：</p>



<pre class="prettyprint"><code class=" hljs go"> val sqlContext=<span class="hljs-built_in">new</span> sql.SQLContext(sc)
    val df=sqlContext.jsonFile(<span class="hljs-string">"file:///F:/spark-2.0.0/SparkApp/src/cn/just/shinelon/txt/SparkSql01.json"</span>)

    <span class="hljs-built_in">println</span>(df.show())      <span class="hljs-comment">//打印表数据</span>

    <span class="hljs-built_in">println</span>(df.printSchema())  <span class="hljs-comment">//以树的形式打印DataFrame的Schema</span>

    <span class="hljs-built_in">println</span>(df.<span class="hljs-keyword">select</span>(df(<span class="hljs-string">"name"</span>),df(<span class="hljs-string">"age"</span><span class="hljs-number">)+1</span>).show())</code></pre>

<p>下面有两种方式来操作RDD数据源，即将现有的RDD转化为DataFrame。</p>

<p>操作的文件内容格式如下：</p>



<pre class="prettyprint"><code class=" hljs ">shinelon,19
mike,20
wangwu,25</code></pre>

<p><strong>1.以反射机制推断RDD模式</strong> <br>
主要分下面三个步骤：</p>

<ol>
<li>必须创建case类，只有case类才能隐式转换为DataFrame。</li>
<li>必须生成DataFrame，进行注册临时表操作。</li>
<li>必须在内存中register成临时表，才能供查询使用。</li>
</ol>



<pre class="prettyprint"><code class=" hljs scala">    <span class="hljs-keyword">val</span> conf=<span class="hljs-keyword">new</span> SparkConf()
      .setMaster(<span class="hljs-string">"local"</span>)
      .setAppName(<span class="hljs-string">"SparkSqlDemo01"</span>)
    <span class="hljs-keyword">val</span> sc=<span class="hljs-keyword">new</span> SparkContext(conf)
    <span class="hljs-keyword">val</span> sqlContext=<span class="hljs-keyword">new</span> sql.SQLContext(sc)

    <span class="hljs-comment">//使用case定义Schema（不能超过22个属性），实现Person接口</span>
    <span class="hljs-comment">//只有case类才能隐式转换为一个DataFrame</span>
    <span class="hljs-class"><span class="hljs-keyword">case</span> <span class="hljs-keyword">class</span> <span class="hljs-title">Person</span><span class="hljs-params">(name:String,age:Int)</span></span>
    <span class="hljs-comment">//使用前缀hdfs://来标识HDFS存储系统的文件</span>
    <span class="hljs-keyword">val</span> people=sc.textFile(<span class="hljs-string">"file:///F:/spark-2.0.0/SparkApp/src/cn/just/shinelon/txt/SparkSql02.txt"</span>)
          .map(_.split(<span class="hljs-string">","</span>)).map(p=&gt;Person(p(<span class="hljs-number">0</span>),p(<span class="hljs-number">1</span>).trim.toInt)).toDF

    <span class="hljs-comment">//DataFrame注册临时表</span>
    people.refisterTempTable(<span class="hljs-string">"person"</span>)
    <span class="hljs-comment">//使用sql运行SQL表达式</span>
    <span class="hljs-keyword">val</span> result=sqlContext.sql(<span class="hljs-string">"SELECT name,age from person WHERE age&gt;=19"</span>)

    println(result.map(t=&gt;<span class="hljs-string">"Name:"</span>+t(<span class="hljs-number">0</span>)).collect())

    println(result.map(t=&gt;<span class="hljs-string">"Name:"</span>+t.getAs[String](<span class="hljs-number">1</span>)).collect())
</code></pre>

<p><strong>2.以编程方式定义RDD模型</strong> <br>
主要分三个步骤：</p>

<ol>
<li>从原始RDD中创建一个Rows的RDD。</li>
<li>创建一个表示为StructType类型的Schema，匹配在第一步创建的RDD的Rows的结构。</li>
<li>通过SQLContext提供的createDataFrame方法，应用Schema到Rows的RDD。</li>
</ol>

<p>代码如下所示：</p>



<pre class="prettyprint"><code class=" hljs scala"> <span class="hljs-keyword">val</span> conf=<span class="hljs-keyword">new</span> SparkConf()
      .setMaster(<span class="hljs-string">"local"</span>)
      .setAppName(<span class="hljs-string">"SparkSqlDemo01"</span>)
    <span class="hljs-keyword">val</span> sc=<span class="hljs-keyword">new</span> SparkContext(conf)
    <span class="hljs-keyword">val</span> sqlContext=<span class="hljs-keyword">new</span> sql.SQLContext(sc)
<span class="hljs-keyword">val</span> sqlContext=<span class="hljs-keyword">new</span> sql.SQLContext(sc)

    <span class="hljs-comment">//使用case定义Schema（不能超过22个属性），实现Person接口</span>
    <span class="hljs-comment">//只有case类才能隐式转换为一个DataFrame</span>
    <span class="hljs-class"><span class="hljs-keyword">case</span> <span class="hljs-keyword">class</span> <span class="hljs-title">Person</span><span class="hljs-params">(name:String,age:Int)</span></span>
    <span class="hljs-comment">//使用前缀hdfs://来标识HDFS存储系统的文件</span>
    <span class="hljs-keyword">val</span> people=sc.textFile(<span class="hljs-string">"file:///F:/spark-2.0.0/SparkApp/src/cn/just/shinelon/txt/SparkSql02.txt"</span>)
          .map(_.split(<span class="hljs-string">","</span>)).map(p=&gt;Person(p(<span class="hljs-number">0</span>),p(<span class="hljs-number">1</span>).trim.toInt)).toDF

    <span class="hljs-comment">//DataFrame注册临时表</span>
    people.refisterTempTable(<span class="hljs-string">"person"</span>)
    <span class="hljs-comment">//使用sql运行SQL表达式</span>
    <span class="hljs-keyword">val</span> result=sqlContext.sql(<span class="hljs-string">"SELECT name,age from person WHERE age&gt;=19"</span>)

    println(result.map(t=&gt;<span class="hljs-string">"Name:"</span>+t(<span class="hljs-number">0</span>)).collect())

    println(result.map(t=&gt;<span class="hljs-string">"Name:"</span>+t.getAs[String](<span class="hljs-number">1</span>)).collect())</code></pre>

<p>至此，就介绍了Spark SQL的基本原理以及初步使用，Spark SQL凭借着内存的快速访问快速在大数据界发展，有人说它取代了HIVE，这种说法过于狭隘，Hive还是应用广泛，只能说代替了Hive的 部分功能，并不能完全代替Hive来使用。谢谢阅读！！！</p>

<hr>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>