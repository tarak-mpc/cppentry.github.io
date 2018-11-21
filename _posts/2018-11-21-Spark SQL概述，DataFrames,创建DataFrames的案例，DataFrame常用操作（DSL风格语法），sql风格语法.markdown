---
layout:     post
title:      Spark SQL概述，DataFrames,创建DataFrames的案例，DataFrame常用操作（DSL风格语法），sql风格语法
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/toto1297488504/article/details/74907124				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="一-spark-sql">一、 Spark SQL</h1>



<h1 id="1-spark-sql概述">1． Spark SQL概述</h1>



<h2 id="11-什么是spark-sql">1.1． 什么是Spark SQL</h2>

<p><img src="https://img-blog.csdn.net/20170709214633486?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdG90b3R1enVvcXVhbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title="">  <br>
Spark SQL是Spark用来处理结构化数据的一个模块，它提供了一个编程抽象叫做DataFrame并且作为分布式SQL查询引擎的作用。 <br>
1.2． 为什么要学习Spark SQL <br>
我们已经学习了Hive，它是将Hive SQL转换成MapReduce然后提交到集群上执行，大大简化了编写MapReduce的程序的复杂性，由于MapReduce这种计算模型执行效率比较慢。所有Spark SQL的应运而生，它是将Spark SQL转换成RDD，然后提交到集群执行，执行效率非常快！ <br>
<strong>1.易整合</strong> <br>
<img src="https://img-blog.csdn.net/20170709214651504?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdG90b3R1enVvcXVhbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title="">  <br>
<strong>2.统一的数据访问方式</strong> <br>
<img src="https://img-blog.csdn.net/20170709214710318?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdG90b3R1enVvcXVhbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title="">  <br>
<strong>3.兼容Hive</strong> <br>
<img src="https://img-blog.csdn.net/20170709214734110?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdG90b3R1enVvcXVhbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title="">  <br>
<strong>4.标准的数据连接</strong> <br>
<img src="https://img-blog.csdn.net/20170709214750363?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdG90b3R1enVvcXVhbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> </p>



<h1 id="2-dataframes">2． DataFrames</h1>



<h2 id="21-什么是dataframes">2.1． 什么是DataFrames</h2>

<p>与RDD类似，DataFrame也是一个分布式数据容器。然而DataFrame更像传统数据库的二维表格，除了数据以外，还记录数据的结构信息，即schema。同时，与Hive类似，DataFrame也支持嵌套数据类型（struct、array和map）。从API易用性的角度上 看，DataFrame API提供的是一套高层的关系操作，比函数式的RDD API要更加友好，门槛更低。由于与R和Pandas的DataFrame类似，Spark DataFrame很好地继承了传统单机数据分析的开发体验。 <br>
<img src="https://img-blog.csdn.net/20170709233141848?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdG90b3R1enVvcXVhbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> </p>



<h2 id="22-创建dataframes">2.2． 创建DataFrames</h2>

<p>连接spark-shell:</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@hadoop1</span> spark-<span class="hljs-number">2.1</span>.<span class="hljs-number">1</span>-bin-hadoop2.<span class="hljs-number">7</span>]<span class="hljs-comment"># bin/spark-shell --master spark://hadoop1:7077,hadoop2:7077</span></code></pre>

<p>在Spark SQL中SQLContext是创建DataFrames和执行SQL的入口，在spark-1.5.2中已经内置了一个sqlContext</p>

<p><strong>1.</strong>在本地创建一个文件，有三列，分别是id、name、age，用空格分隔，然后上传到hdfs上 <br>
hdfs dfs -put person.txt /</p>

<p>person.txt的内容如下：</p>



<pre class="prettyprint"><code class=" hljs ">1 zhangsan 19
2 lisi 20
3 wangwu 28
4 zhaoliu 26
5 tianqi 24
6 chengnong 55
7 zhouxingchi 58
8 mayun 50
9 yangliying 30
10 lilianjie 51
11 zhanghuimei 35
12 lian 53
13 zhangyimou 54</code></pre>

<p><strong>2.</strong>在spark shell执行下面命令，读取数据，将每一行的数据使用列分隔符分割</p>



<pre class="prettyprint"><code class=" hljs avrasm">scala&gt; val lineRDD = sc<span class="hljs-preprocessor">.textFile</span>(<span class="hljs-string">"hdfs://mycluster/person.txt"</span>)<span class="hljs-preprocessor">.map</span>(_<span class="hljs-preprocessor">.split</span>(<span class="hljs-string">" "</span>))
<span class="hljs-label">lineRDD:</span> org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.rdd</span><span class="hljs-preprocessor">.RDD</span>[Array[String]] = MapPartitionsRDD[<span class="hljs-number">2</span>] at map at &lt;console&gt;:<span class="hljs-number">24</span></code></pre>

<p><strong>3.</strong>定义case class（相当于表的schema）</p>



<pre class="prettyprint"><code class=" hljs ruby">scala&gt; <span class="hljs-keyword">case</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">Person</span>(<span class="hljs-title">id</span>:<span class="hljs-title">Int</span>, <span class="hljs-title">name</span>:<span class="hljs-title">String</span>, <span class="hljs-title">age</span>:<span class="hljs-title">Int</span>)</span>
<span class="hljs-keyword">defined</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">Person</span></span></code></pre>

<p><strong>4.</strong>将RDD和case class关联</p>



<pre class="prettyprint"><code class=" hljs avrasm">scala&gt; val personRDD = lineRDD<span class="hljs-preprocessor">.map</span>(<span class="hljs-built_in">x</span> =&gt; Person(<span class="hljs-built_in">x</span>(<span class="hljs-number">0</span>)<span class="hljs-preprocessor">.toInt</span>, <span class="hljs-built_in">x</span>(<span class="hljs-number">1</span>), <span class="hljs-built_in">x</span>(<span class="hljs-number">2</span>)<span class="hljs-preprocessor">.toInt</span>))
<span class="hljs-label">personRDD:</span> org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.rdd</span><span class="hljs-preprocessor">.RDD</span>[Person] = MapPartitionsRDD[<span class="hljs-number">3</span>] at map at &lt;console&gt;:<span class="hljs-number">28</span></code></pre>

<p><strong>5.</strong>将RDD转换成DataFrame</p>



<pre class="prettyprint"><code class=" hljs r">scala&gt; val personDF = personRDD.toDF
personDF: org.apache.spark.sql.DataFrame = [id: int, name: string <span class="hljs-keyword">...</span> <span class="hljs-number">1</span> more field]</code></pre>

<p><strong>6.</strong>对DataFrame进行处理</p>



<pre class="prettyprint"><code class=" hljs smalltalk">scala&gt; personDF.show
+---+-----------+---+
<span class="hljs-localvars">| id|       name|age|</span>
+---+-----------+---+
|  <span class="hljs-number">1</span><span class="hljs-localvars">|   zhangsan|</span> <span class="hljs-number">19</span>|
|  <span class="hljs-number">2</span><span class="hljs-localvars">|       lisi|</span> <span class="hljs-number">20</span>|
|  <span class="hljs-number">3</span><span class="hljs-localvars">|     wangwu|</span> <span class="hljs-number">28</span>|
|  <span class="hljs-number">4</span><span class="hljs-localvars">|    zhaoliu|</span> <span class="hljs-number">26</span>|
|  <span class="hljs-number">5</span><span class="hljs-localvars">|     tianqi|</span> <span class="hljs-number">24</span>|
|  <span class="hljs-number">6</span><span class="hljs-localvars">|  chengnong|</span> <span class="hljs-number">55</span>|
|  <span class="hljs-number">7</span><span class="hljs-localvars">|zhouxingchi|</span> <span class="hljs-number">58</span>|
|  <span class="hljs-number">8</span><span class="hljs-localvars">|      mayun|</span> <span class="hljs-number">50</span>|
|  <span class="hljs-number">9</span><span class="hljs-localvars">| yangliying|</span> <span class="hljs-number">30</span>|
| <span class="hljs-number">10</span><span class="hljs-localvars">|  lilianjie|</span> <span class="hljs-number">51</span>|
| <span class="hljs-number">11</span><span class="hljs-localvars">|zhanghuimei|</span> <span class="hljs-number">35</span>|
| <span class="hljs-number">12</span><span class="hljs-localvars">|       lian|</span> <span class="hljs-number">53</span>|
| <span class="hljs-number">13</span><span class="hljs-localvars">| zhangyimou|</span> <span class="hljs-number">54</span>|
+---+-----------+---+</code></pre>



<h1 id="3dataframe常用操作">3.DataFrame常用操作</h1>



<h2 id="31-dsl风格语法">3.1 DSL风格语法</h2>

<p><strong>1.</strong>查看DataFrame中的内容</p>



<pre class="prettyprint"><code class=" hljs smalltalk">scala&gt; personDF.show
+---+-----------+---+
<span class="hljs-localvars">| id|       name|age|</span>
+---+-----------+---+
|  <span class="hljs-number">1</span><span class="hljs-localvars">|   zhangsan|</span> <span class="hljs-number">19</span>|
|  <span class="hljs-number">2</span><span class="hljs-localvars">|       lisi|</span> <span class="hljs-number">20</span>|
|  <span class="hljs-number">3</span><span class="hljs-localvars">|     wangwu|</span> <span class="hljs-number">28</span>|
|  <span class="hljs-number">4</span><span class="hljs-localvars">|    zhaoliu|</span> <span class="hljs-number">26</span>|
|  <span class="hljs-number">5</span><span class="hljs-localvars">|     tianqi|</span> <span class="hljs-number">24</span>|
|  <span class="hljs-number">6</span><span class="hljs-localvars">|  chengnong|</span> <span class="hljs-number">55</span>|
|  <span class="hljs-number">7</span><span class="hljs-localvars">|zhouxingchi|</span> <span class="hljs-number">58</span>|
|  <span class="hljs-number">8</span><span class="hljs-localvars">|      mayun|</span> <span class="hljs-number">50</span>|
|  <span class="hljs-number">9</span><span class="hljs-localvars">| yangliying|</span> <span class="hljs-number">30</span>|
| <span class="hljs-number">10</span><span class="hljs-localvars">|  lilianjie|</span> <span class="hljs-number">51</span>|
| <span class="hljs-number">11</span><span class="hljs-localvars">|zhanghuimei|</span> <span class="hljs-number">35</span>|
| <span class="hljs-number">12</span><span class="hljs-localvars">|       lian|</span> <span class="hljs-number">53</span>|
| <span class="hljs-number">13</span><span class="hljs-localvars">| zhangyimou|</span> <span class="hljs-number">54</span>|
+---+-----------+---+</code></pre>

<p><strong>2.</strong>查看DataFrame部分列中的内容</p>



<pre class="prettyprint"><code class=" hljs asciidoc"><span class="hljs-header">scala&gt; personDF.select(personDF.col("name")).show
+-----------+</span>
<span class="hljs-header">|       name|
+-----------+</span>
|   zhangsan|
|       lisi|
|     wangwu|
|    zhaoliu|
|     tianqi|
|  chengnong|
|zhouxingchi|
|      mayun|
| yangliying|
|  lilianjie|
|zhanghuimei|
|       lian|
<span class="hljs-header">| zhangyimou|
+-----------+</span></code></pre>



<pre class="prettyprint"><code class=" hljs smalltalk">scala&gt; personDF.select(col(<span class="hljs-comment">"name"</span>),col(<span class="hljs-comment">"age"</span>)).show
+-----------+---+
<span class="hljs-localvars">|       name|age|</span>
+-----------+---+
<span class="hljs-localvars">|   zhangsan|</span> <span class="hljs-number">19</span>|
<span class="hljs-localvars">|       lisi|</span> <span class="hljs-number">20</span>|
<span class="hljs-localvars">|     wangwu|</span> <span class="hljs-number">28</span>|
<span class="hljs-localvars">|    zhaoliu|</span> <span class="hljs-number">26</span>|
<span class="hljs-localvars">|     tianqi|</span> <span class="hljs-number">24</span>|
<span class="hljs-localvars">|  chengnong|</span> <span class="hljs-number">55</span>|
<span class="hljs-localvars">|zhouxingchi|</span> <span class="hljs-number">58</span>|
<span class="hljs-localvars">|      mayun|</span> <span class="hljs-number">50</span>|
<span class="hljs-localvars">| yangliying|</span> <span class="hljs-number">30</span>|
<span class="hljs-localvars">|  lilianjie|</span> <span class="hljs-number">51</span>|
<span class="hljs-localvars">|zhanghuimei|</span> <span class="hljs-number">35</span>|
<span class="hljs-localvars">|       lian|</span> <span class="hljs-number">53</span>|
<span class="hljs-localvars">| zhangyimou|</span> <span class="hljs-number">54</span>|
+-----------+---+</code></pre>



<pre class="prettyprint"><code class=" hljs asciidoc"><span class="hljs-header">scala&gt; personDF.select("name").show
+-----------+</span>
<span class="hljs-header">|       name|
+-----------+</span>
|   zhangsan|
|       lisi|
|     wangwu|
|    zhaoliu|
|     tianqi|
|  chengnong|
|zhouxingchi|
|      mayun|
| yangliying|
|  lilianjie|
|zhanghuimei|
|       lian|
<span class="hljs-header">| zhangyimou|
+-----------+</span></code></pre>

<p><strong>3.</strong>打印DataFrame的Schema信息</p>



<pre class="prettyprint"><code class=" hljs vbnet">scala&gt; personDF.printSchema
root
 |-- id: <span class="hljs-built_in">integer</span> (nullable = <span class="hljs-literal">true</span>)
 |-- name: <span class="hljs-built_in">string</span> (nullable = <span class="hljs-literal">true</span>)
 |-- age: <span class="hljs-built_in">integer</span> (nullable = <span class="hljs-literal">true</span>)</code></pre>

<p><strong>4.</strong> 查询所有的name和age，并将age +1</p>



<pre class="prettyprint"><code class=" hljs smalltalk">scala&gt; personDF.select(col(<span class="hljs-comment">"id"</span>),col(<span class="hljs-comment">"name"</span>),col(<span class="hljs-comment">"age"</span>) + <span class="hljs-number">1</span>).show
+---+-----------+---------+
<span class="hljs-localvars">| id|       name|</span>(age + <span class="hljs-number">1</span>)|
+---+-----------+---------+
|  <span class="hljs-number">1</span><span class="hljs-localvars">|   zhangsan|</span>       <span class="hljs-number">20</span>|
|  <span class="hljs-number">2</span><span class="hljs-localvars">|       lisi|</span>       <span class="hljs-number">21</span>|
|  <span class="hljs-number">3</span><span class="hljs-localvars">|     wangwu|</span>       <span class="hljs-number">29</span>|
|  <span class="hljs-number">4</span><span class="hljs-localvars">|    zhaoliu|</span>       <span class="hljs-number">27</span>|
|  <span class="hljs-number">5</span><span class="hljs-localvars">|     tianqi|</span>       <span class="hljs-number">25</span>|
|  <span class="hljs-number">6</span><span class="hljs-localvars">|  chengnong|</span>       <span class="hljs-number">56</span>|
|  <span class="hljs-number">7</span><span class="hljs-localvars">|zhouxingchi|</span>       <span class="hljs-number">59</span>|
|  <span class="hljs-number">8</span><span class="hljs-localvars">|      mayun|</span>       <span class="hljs-number">51</span>|
|  <span class="hljs-number">9</span><span class="hljs-localvars">| yangliying|</span>       <span class="hljs-number">31</span>|
| <span class="hljs-number">10</span><span class="hljs-localvars">|  lilianjie|</span>       <span class="hljs-number">52</span>|
| <span class="hljs-number">11</span><span class="hljs-localvars">|zhanghuimei|</span>       <span class="hljs-number">36</span>|
| <span class="hljs-number">12</span><span class="hljs-localvars">|       lian|</span>       <span class="hljs-number">54</span>|
| <span class="hljs-number">13</span><span class="hljs-localvars">| zhangyimou|</span>       <span class="hljs-number">55</span>|
+---+-----------+---------+</code></pre>



<pre class="prettyprint"><code class=" hljs smalltalk">scala&gt; personDF.select(personDF(<span class="hljs-comment">"id"</span>),personDF(<span class="hljs-comment">"name"</span>),personDF(<span class="hljs-comment">"age"</span>) + <span class="hljs-number">1</span>).show
+---+-----------+---------+
<span class="hljs-localvars">| id|       name|</span>(age + <span class="hljs-number">1</span>)|
+---+-----------+---------+
|  <span class="hljs-number">1</span><span class="hljs-localvars">|   zhangsan|</span>       <span class="hljs-number">20</span>|
|  <span class="hljs-number">2</span><span class="hljs-localvars">|       lisi|</span>       <span class="hljs-number">21</span>|
|  <span class="hljs-number">3</span><span class="hljs-localvars">|     wangwu|</span>       <span class="hljs-number">29</span>|
|  <span class="hljs-number">4</span><span class="hljs-localvars">|    zhaoliu|</span>       <span class="hljs-number">27</span>|
|  <span class="hljs-number">5</span><span class="hljs-localvars">|     tianqi|</span>       <span class="hljs-number">25</span>|
|  <span class="hljs-number">6</span><span class="hljs-localvars">|  chengnong|</span>       <span class="hljs-number">56</span>|
|  <span class="hljs-number">7</span><span class="hljs-localvars">|zhouxingchi|</span>       <span class="hljs-number">59</span>|
|  <span class="hljs-number">8</span><span class="hljs-localvars">|      mayun|</span>       <span class="hljs-number">51</span>|
|  <span class="hljs-number">9</span><span class="hljs-localvars">| yangliying|</span>       <span class="hljs-number">31</span>|
| <span class="hljs-number">10</span><span class="hljs-localvars">|  lilianjie|</span>       <span class="hljs-number">52</span>|
| <span class="hljs-number">11</span><span class="hljs-localvars">|zhanghuimei|</span>       <span class="hljs-number">36</span>|
| <span class="hljs-number">12</span><span class="hljs-localvars">|       lian|</span>       <span class="hljs-number">54</span>|
| <span class="hljs-number">13</span><span class="hljs-localvars">| zhangyimou|</span>       <span class="hljs-number">55</span>|
+---+-----------+---------+</code></pre>

<p><strong>5.</strong> 过滤age大于等于40的</p>



<pre class="prettyprint"><code class=" hljs avrasm">scala&gt; personDF<span class="hljs-preprocessor">.filter</span>(col(<span class="hljs-string">"age"</span>) &gt;= <span class="hljs-number">40</span>)<span class="hljs-preprocessor">.show</span></code></pre>

<p><img src="https://img-blog.csdn.net/20170710175913395?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdG90b3R1enVvcXVhbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><strong>6.</strong> 按年龄进行分组并统计相同年龄的人数</p>



<pre class="prettyprint"><code class=" hljs asciidoc">scala&gt; personDF.groupBy("age").count.show()
<span class="hljs-code">+---+</span>-----+                                                                     
<span class="hljs-header">|age|count|
+---+-----+</span>
| 53|    1|
| 28|    1|
| 26|    1|
| 20|    1|
| 54|    1|
| 19|    1|
| 35|    1|
| 55|    1|
| 51|    1|
| 50|    1|
| 24|    1|
| 58|    1|
<span class="hljs-header">| 30|    1|
+---+-----+</span></code></pre>



<h2 id="32-sql风格语法">3.2 SQL风格语法：</h2>

<ol>
<li>在使用SQL的语法之前，需要先执行（也就是或先创建一个sqlContext）：</li>
</ol>



<pre class="prettyprint"><code class=" hljs avrasm">scala&gt; val sqlContext = new org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.sql</span><span class="hljs-preprocessor">.SQLContext</span>(sc)
<span class="hljs-label">warning:</span> there was one deprecation warning<span class="hljs-comment">; re-run with -deprecation for details</span>
<span class="hljs-label">sqlContext:</span> org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.sql</span><span class="hljs-preprocessor">.SQLContext</span> = org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.sql</span><span class="hljs-preprocessor">.SQLContext</span><span class="hljs-localvars">@11</span>fb9fc7</code></pre>

<ol>
<li>如果想使用SQL风格的语法，需要将DataFrame注册成表</li>
</ol>



<pre class="prettyprint"><code class=" hljs livecodeserver">scala&gt; personDF.registerTempTable(<span class="hljs-string">"t_person"</span>)
warning: there was <span class="hljs-constant">one</span> deprecation warning; re-run <span class="hljs-operator">with</span> -deprecation <span class="hljs-keyword">for</span> details</code></pre>

<ol>
<li>查询年龄最大的前两名</li>
</ol>



<pre class="prettyprint"><code class=" hljs smalltalk">scala&gt; sqlContext.sql(<span class="hljs-comment">"select * from t_person order by age desc limit 2"</span>).show
+---+-----------+---+                                                           
<span class="hljs-localvars">| id|       name|age|</span>
+---+-----------+---+
|  <span class="hljs-number">7</span><span class="hljs-localvars">|zhouxingchi|</span> <span class="hljs-number">58</span>|
|  <span class="hljs-number">6</span><span class="hljs-localvars">|  chengnong|</span> <span class="hljs-number">55</span>|
+---+-----------+---+</code></pre>

<ol>
<li>显示表的Schema信息</li>
</ol>



<pre class="prettyprint"><code class=" hljs asciidoc"><span class="hljs-header">scala&gt; sqlContext.sql("desc t_person").show
+--------+---------+-------+</span>
<span class="hljs-header">|col_name|data_type|comment|
+--------+---------+-------+</span>
|      id|      int|   null|
|    name|   string|   null|
<span class="hljs-header">|     age|      int|   null|
+--------+---------+-------+</span>


scala&gt;</code></pre>

<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-number">7.</span>保存结果
result<span class="hljs-preprocessor">.save</span>(<span class="hljs-string">"hdfs://hadoop.itcast.cn:9000/sql/res1"</span>)
result<span class="hljs-preprocessor">.save</span>(<span class="hljs-string">"hdfs://hadoop.itcast.cn:9000/sql/res2"</span>, <span class="hljs-string">"json"</span>)

<span class="hljs-preprocessor">#以JSON文件格式覆写HDFS上的JSON文件</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.sql</span><span class="hljs-preprocessor">.SaveMode</span>._
result<span class="hljs-preprocessor">.save</span>(<span class="hljs-string">"hdfs://hadoop.itcast.cn:9000/sql/res2"</span>, <span class="hljs-string">"json"</span> , Overwrite)

<span class="hljs-number">8.</span>重新加载以前的处理结果（可选）
sqlContext<span class="hljs-preprocessor">.load</span>(<span class="hljs-string">"hdfs://hadoop.itcast.cn:9000/sql/res1"</span>)
sqlContext<span class="hljs-preprocessor">.load</span>(<span class="hljs-string">"hdfs://hadoop.itcast.cn:9000/sql/res2"</span>, <span class="hljs-string">"json"</span>)</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>