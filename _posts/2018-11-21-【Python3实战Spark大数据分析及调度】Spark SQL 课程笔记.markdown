---
layout:     post
title:      【Python3实战Spark大数据分析及调度】Spark SQL 课程笔记
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p id="main-toc"><strong>目录</strong></p>

<p id="Spark%20SQL%20%E5%89%8D%E4%B8%96%E4%BB%8A%E7%94%9F-toc" style="margin-left:40px;"><a href="#Spark%20SQL%20%E5%89%8D%E4%B8%96%E4%BB%8A%E7%94%9F" rel="nofollow">Spark SQL 前世今生</a></p>

<p id="Spark%20SQL%E6%A6%82%E8%BF%B0-toc" style="margin-left:40px;"><a href="#Spark%20SQL%E6%A6%82%E8%BF%B0" rel="nofollow">Spark SQL概述</a></p>

<p id="Spark%20SQL%E6%9E%B6%E6%9E%84-toc" style="margin-left:40px;"><a href="#Spark%20SQL%E6%9E%B6%E6%9E%84" rel="nofollow">Spark SQL架构</a></p>

<p id="DataFrame%20%26%20Dataset-toc" style="margin-left:40px;"><a href="#DataFrame%20%26%20Dataset" rel="nofollow">DataFrame &amp; Dataset</a></p>

<hr id="hr-toc"><h2 id="Spark%20SQL%20%E5%89%8D%E4%B8%96%E4%BB%8A%E7%94%9F">Spark SQL 前世今生</h2>

<p><img alt="" class="has" src="https://img-blog.csdnimg.cn/20181115152050868.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3poYW5nMTU5NTM3MDk5MTM=,size_16,color_FFFFFF,t_70"></p>

<p>MapReduce：map、reduce都是进程操作，而且中间结果要落地（存入disk），IO开销大</p>

<p><img alt="" class="has" src="https://img-blog.csdnimg.cn/20181115152422449.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3poYW5nMTU5NTM3MDk5MTM=,size_16,color_FFFFFF,t_70"></p>

<p>metadata放在MySql，所以spark SQL也能用，Hive也能用</p>

<p>Hive on Spark和Spark SQL是两个东西</p>

<p> </p>

<h2 id="Spark%20SQL%E6%A6%82%E8%BF%B0">Spark SQL概述</h2>

<ul><li>Spark SQL不仅仅是SQL这么简单的事情，还能做更多的事情</li>
	<li>一个用于处理结构化数据的Spark组件，强调的是“结构化数据”，而非“SQL”</li>
</ul><p><a href="https://spark.apache.org/sql/" rel="nofollow">https://spark.apache.org/sql/</a></p>

<p><img alt="" class="has" height="199" src="https://img-blog.csdnimg.cn/20181115153142473.png" width="727"></p>

<p><img alt="" class="has" src="https://img-blog.csdnimg.cn/20181115153155779.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3poYW5nMTU5NTM3MDk5MTM=,size_16,color_FFFFFF,t_70"></p>

<p><img alt="" class="has" src="https://img-blog.csdnimg.cn/20181115153203357.png"></p>

<p><img alt="" class="has" height="154" src="https://img-blog.csdnimg.cn/20181115153211522.png" width="710"></p>

<h2 id="Spark%20SQL%E6%9E%B6%E6%9E%84">Spark SQL架构</h2>

<p><img alt="" class="has" src="https://img-blog.csdnimg.cn/20181115153750376.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3poYW5nMTU5NTM3MDk5MTM=,size_16,color_FFFFFF,t_70"></p>

<p> </p>

<h2 id="DataFrame%20%26%20Dataset">DataFrame &amp; Dataset</h2>

<p><img alt="" class="has" src="https://img-blog.csdnimg.cn/20181115153943608.png"></p>

<p>A <span style="color:#f33b45;">Dataset</span> is a distributed collection of data. Dataset is a new interface added in<span style="color:#f33b45;"> Spark 1.6</span> that provides the benefits of RDDs (<span style="color:#f33b45;">strong typing, ability to use powerful lambda functions</span>) with the benefits of Spark SQL’s optimized execution engine. A Dataset can b<span>e </span><a href="https://spark.apache.org/docs/latest/sql-getting-started.html#creating-datasets" rel="nofollow"><span>constructed</span></a><span> from JVM objects and then manipulated using functional transformations (<code>map</code></span><span>, </span><span><code>flatMap</code></span><span>,</span><span> </span><span><code>filter</code></span><span>,</span><span> etc.). The Dataset API is available in </span><a href="https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.Dataset" rel="nofollow"><span>Scala</span></a><span> and </span><a href="https://spark.apache.org/docs/latest/api/java/index.html?org/apache/spark/sql/Dataset.html" rel="nofollow"><span>Java</span></a><span>. Python does not have the support for the Dataset API. But due to Python’s dynamic nature, many of the benefits of the Dataset API are already available (i.e. you can access the field of a row by name naturally<code>row.columnName</code>). The case for R is similar.</span></p>

<p><span style="color:#f33b45;">A DataFrame is a <em>Dataset</em> organized into named columns. </span>It is conceptually equivalent to a table in a relational database or a data frame in R/Python, but with richer optimizations under the hood. DataFrames can be constructed from a wide array of <a href="https://spark.apache.org/docs/latest/sql-data-sources.html" rel="nofollow">sources</a> such as: structured data files, tables in Hive, external databases, or existing RDDs. The DataFrame API is available in Scala, Java, <a href="https://spark.apache.org/docs/latest/api/python/pyspark.sql.html#pyspark.sql.DataFrame" rel="nofollow">Python</a>, and <a href="https://spark.apache.org/docs/latest/api/R/index.html" rel="nofollow">R</a>. In Scala and Java, a DataFrame is represented by a Dataset of <code>Row</code>s. In <a href="https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.Dataset" rel="nofollow">the Scala API</a>, <code>DataFrame</code>is simply a type alias of <code>Dataset[Row]</code>. While, in <a href="https://spark.apache.org/docs/latest/api/java/index.html?org/apache/spark/sql/Dataset.html" rel="nofollow">Java API</a>, users need to use <code>Dataset&lt;Row&gt;</code> to represent a <code>DataFrame</code>.</p>            </div>
                </div>