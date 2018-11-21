---
layout:     post
title:      Spark SQL 数据源 API：Spark平台的统一数据接入
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 class="title" style="list-style:none;font-weight:normal;font-size:28px;line-height:36px;font-family:'Microsoft YaHei';color:rgb(51,51,51);">
Spark SQL 数据源 API：Spark平台的统一数据接入</h1>
<div class="summary" style="list-style:none;font-family:Helvetica, Tahoma, Arial, sans-serif;line-height:24px;">
<span style="color:#999999;"><span style="background-color:rgb(255,255,255);">自从</span></span><span style="color:rgb(51,51,51);font-size:14px;background-color:rgb(247,247,247);">Spark 1.0版本的Spark SQL问世以来，它最常见的用途之一就是作为从Spark平台上面获取数据的一个渠道。到了Spark 1.2 版本，我们已经迈出了下一步，让Spark的原生资源和更多的输入源进行整合集成。</span></div>
<div class="con news_content" style="list-style:none;color:rgb(51,51,51);font-family:Helvetica, Tahoma, Arial, sans-serif;font-size:14px;line-height:24px;">
<p style="list-style:none;">
<span>自从Spark 1.0版本的Spark SQL问世以来，它最常见的用途之一就是作为从Spark平台上面获取数据的一个渠道。</span></p>
<p style="list-style:none;">
早期用户比较喜爱Spark SQL提供的从现有Apache Hive表以及流行的Parquet列式存储格式中读取数据的支持。之后，Spark SQL还增加了对其他格式的支持，比如说<a href="https://spark.apache.org/docs/latest/sql-programming-guide.html#json-datasets" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">JSON</a>。<span>到了Spark
 1.2 版本，我们已经迈出了下一步，让Spark的原生资源和更多的输入源进行整合集成。</span><span>这些新的整合将随着纳入新的Spark SQL数据源API而成为可能。</span></p>
<p style="list-style:none;text-align:center;">
<img src="http://img.ptcms.csdn.net/article/201502/13/54dd5970ebfda_middle.jpg" alt="" style="vertical-align:middle;border:none;"></p>
<p style="list-style:none;">
数据源API通过Spark SQL提供了访问结构化数据的可插拔机制。<span>数据源不仅仅有了简便的途径去进行数据转换并加入到Spark 平台。</span><span>由API提供的密集的优化器集合意味着过滤和列修剪在很多情况下都会被运用于数据源。</span><span>这些综合的优化极大地减少了需要处理的数据量，因此能够显著提高Spark的工作效率。</span></p>
<p style="list-style:none;">
<span>使用数据源和通过SQL访问他们一样简单（或者你喜爱的Spark语言）</span></p>
<p style="list-style:none;">
<span></span></p>
<pre style="list-style:none;overflow:hidden;border:1px solid rgb(221,221,221);background:rgb(247,247,247);">CREATE TEMPORARY TABLE episodes
USING com.databricks.spark.avro
OPTIONS (path "episodes.avro")</pre>
<p style="list-style:none;">
</p>
<p style="list-style:none;">
数据源API的另外一个优点就是不管数据的来源如何，用户都能够通过Spark支持的所有语言来操作这些数据 。<span>例如，那些用Scala实现的数据源，pySpark用户不需要其他的库开发者做任何额外的工作就可以使用。</span><span>此外，Spark SQL可以很容易的使用单一接口访问不同数据源的数据。</span></p>
<p style="list-style:none;">
总之，Spark 1.2 提供的这些功能进一步统一了大数据分析的解决方案。</p>
<p style="list-style:none;">
尽管这个API刚开发出来不久，在此之上已经建立了好几个库，其中包括<a href="http://spark-packages.org/package/3" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Apache Avro</a>，<a href="http://spark-packages.org/package/12" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Comma
 Separated Values（csv）</a>，甚至还有<a href="https://github.com/mraad/spark-dbf" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">dBASE Table File Format (dbf)</a>。</p>
<p style="list-style:none;">
现在Spark 1.2 已经正式发布了，我们期望这个版本能够得到快速的发展。<span>我们正在不断的努力，让Spark能够支持HBase，JDBC等。</span></p>
<p style="list-style:none;">
访问<a href="http://spark-packages.org/" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Spark Packages</a> 来获取最新的可用库列表。</p>
<p style="list-style:none;">
对于有兴趣编写他们喜爱格式的库开发者，我们建议你参阅<a href="https://github.com/databricks/spark-avro" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">Apache Avro</a>，学习<a href="https://github.com/apache/spark/tree/master/sql/core/src/test/scala/org/apache/spark/sql/sources" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">示例源码</a>或者观看<a href="http://www.youtube.com/watch?v=GQSNJAzxOr" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">聚会视频</a>。</p>
<p style="list-style:none;">
此外，敬请期待这个API的扩展。<span>在Spark 1.3 版本中，我们希望增加对数据分区，持久化表以及可选用户指定模式的支持。</span></p>
<p style="list-style:none;">
英文原文：<a href="http://databricks.com/blog/2015/01/09/spark-sql-data-sources-api-unified-data-access-for-the-spark-platform.html" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">databricks.com</a>  作者：Michael Armbrust</p>
</div>
            </div>
                </div>