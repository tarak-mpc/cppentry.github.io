---
layout:     post
title:      Spark学习之Spark SQL（8）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为王小雷原创文章，未经博主允许不得转载					https://blog.csdn.net/dream_an/article/details/50542539				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="spark学习之spark-sql8">Spark学习之Spark SQL（8）</h2>



<h3 id="1-spark用来操作结构化和半结构化数据的接口spark-sql">1. Spark用来操作结构化和半结构化数据的接口——Spark SQL、</h3>



<h3 id="2-spark-sql的三大功能">2. Spark SQL的三大功能</h3>

<pre><code>2.1 Spark SQL可以从各种结构化数据（例如JSON、Hive、Parquet等）中读取数据。
2.2 Spark SQL不仅支持在Spark程序内使用SQL语句进行查询，也支持从类似商业智能软件Tableau这样的外部工具中通过标准数据库连接器（JDBC/ODBC）连接Spark SQL进行查询。
2.3 当在Spark程序内使用Spark SQL时，Spark SQL支持SQ与常规的Python/Java/Scala代码高度整合，包括连接RDD与SQL表、公开的自定义SQL函数接口等。
</code></pre>



<h3 id="3-schemardd13版本后为dataframe是存放row对象的rdd每个row对象代表一行记录schemardd还包含记录的结果信息即数据字段">3. SchemaRDD（1.3版本后为DataFrame）是存放Row对象的RDD，每个Row对象代表一行记录。SchemaRDD还包含记录的结果信息（即数据字段）。</h3>



<h3 id="4-连接spark-sql">4. 连接Spark SQL</h3>

<pre><code>带有Hive支持的Spark SQL的Maven索引
</code></pre>



<pre class="prettyprint"><code class=" hljs avrasm">    groupID =org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span>
    artifactID = spark-hive_2<span class="hljs-number">.10</span>
    version = <span class="hljs-number">1.2</span><span class="hljs-number">.0</span></code></pre>



<h3 id="5-在应用使用spark">5. 在应用使用Spark</h3>

<pre><code>5.1 初始化Spark
</code></pre>



<pre class="prettyprint"><code class="language-scala hljs ">        <span class="hljs-comment">//Sacla中SQL的import的声明</span>
        <span class="hljs-keyword">import</span> org.apache.spark.sql.hive.HiveContext
        <span class="hljs-keyword">import</span> org.apache.spark.sql.SQLContext
        <span class="hljs-comment">//Scala中SQL导入隐式转换支持</span>
        <span class="hljs-keyword">val</span> hiveCtx = ...<span class="hljs-comment">//创建HiveContext</span>
        <span class="hljs-keyword">import</span> hiveCtx._<span class="hljs-comment">//导入隐式转换支持</span>
        <span class="hljs-comment">//创建SQL上下文环境</span>
        <span class="hljs-keyword">val</span> sc = <span class="hljs-keyword">new</span> SparkContext(...)
        <span class="hljs-keyword">val</span> hiveCtx = <span class="hljs-keyword">new</span> HiveContext(sc)</code></pre>

<pre><code>5.2 基本的查询示例
</code></pre>

<pre class="prettyprint"><code class="language-scala hljs ">        <span class="hljs-keyword">val</span> input = hiveCtx.jsonFile(inputFile)
        <span class="hljs-comment">//注册输入的SchemaRDD</span>
        input.registerTempTable(<span class="hljs-string">"tweets"</span>)
        <span class="hljs-comment">//依据tetwwtCount（转发计算）宣传推文</span>
        <span class="hljs-keyword">val</span> topTweeter = hiveCtx.sql(<span class="hljs-string">"SELECT text,retweetCount FROM tweets ORDER　BY retweetCount LIMIT 10"</span>)</code></pre>

<h3 id="6-用户自定义函数udf">6. 用户自定义函数(UDF)</h3>

<pre><code>Scala版本的字符串长度UDF
</code></pre>



<pre class="prettyprint"><code class="language-scala hljs ">    registerFunction(<span class="hljs-string">"strLenScala"</span>,(_:string).length)
    <span class="hljs-keyword">val</span> tweetLength = hiveCtx.sql(<span class="hljs-string">"SELECT strLenScala('tweet') FROM tweets LIMIT 10"</span>)</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>