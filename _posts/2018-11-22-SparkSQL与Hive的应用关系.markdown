---
layout:     post
title:      SparkSQL与Hive的应用关系
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/sinat_29508201/article/details/49620029				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<ol>
<li><p>Spark生态圈 <br>
Spark是基于scala语言产生的，因此有关spark中的各大编程应用也基本跟scala脱不开关系，但是好在spark的平台化做的不错，目前可支持python及R等语言的应用，这些得益于sparkSQL组件的存在，整个spark的结构组件如下如所示： <br>
<img src="http://images0.cnblogs.com/blog/107289/201508/032218309397942.gif" alt="这里写图片描述" title=""> <br>
关于spark的组件功能描述不多说，可以参考博客<a href="http://www.cnblogs.com/shishanyuan/p/4699644.html" rel="nofollow">spark入门系列</a>及 官方资料等，保证有个初步的印象。</p></li>
<li><p>SparkSQL在Hive中的应用 <br>
目前关于sparksql应用的中文文档并不多见，有兴趣的想深入了解的可以<a href="http://spark.apache.org/docs/latest/programming-guide.html" rel="nofollow">参考官方编程指导</a>，里面涵盖了有关scala，java，python及R的语法规则及应用案例，这里简单的就pyspark交互式编程命令说说如何使用hive中的表。</p>

<p>Spark SQL提供了一种抽象的编程工具DataFrame框架，它是分布式sql查询的基本引擎，不论采用何种编程语言最终都是实现对DataFrame的操作。DataFrame的构建数据源可以来自于结构化的数据文件，hive数据表，外部数据库及存在的RDDs数据集。</p>

<p>现在假设Spark环境已经安装好，调用spark安装包下的bin/pyspark脚本命令可启动交互式编程环境，如下图所示： <br>
<img src="https://img-blog.csdn.net/20151103193115275" alt="这里写图片描述" title=""></p>

<p>由于pyspark是基于spark环境的，故首先建立起关于sql的环境，输入命令，其中sc参数是启动后系统自建的sparkcontext环境，不需要用户自己建立，只需导入python中的SQLContent类即可应用，这样可以利用sql实现对DataFrame的查询应用等。</p></li>
</ol>

<blockquote>
  <p>&gt;from pyspark.sql import SQLContext <br>
  &gt;sqlContext = SQLContext(sc)</p>
</blockquote>

<p>虽然目前SQLContext已经向下兼容了HiveContext环境，不过对要使用hive数据仓库数据的用户来说需要使用HiveContext，基本命令跟上面基本类似：</p>

<blockquote>
  <p>&gt;from pyspark.sql import HiveContext <br>
  &gt;sqlContext = HiveContext(sc)</p>
</blockquote>

<p>之后用户可以使用sqlContext.sql(“select * from table”)命令完成对hive数据的查询应用，相比于mapreduce下的查询效率确实快了上十倍了。</p>

<p>下面将列出pyspark环境下使用python API实现的对hive的一系列连贯操作， <br>
第一步，选择数据库</p>

<blockquote>
  <p>sqlContext.sql(“use database”)</p>
</blockquote>

<p>第二步，创建数据表</p>

<blockquote>
  <p>sqlContext.sql(“CREATE TABLE IF NOT EXISTS src (key INT, value STRING)”)</p>
</blockquote>

<p>第三步，导入数据集</p>

<blockquote>
  <p>sqlContext.sql(“LOAD DATA LOCAL INPATH ‘examples/src/main/resources/kv1.txt’ INTO TABLE src”)</p>
</blockquote>

<p>第四步，查询数据集，产生DataFrame</p>

<blockquote>
  <p>results = sqlContext.sql(“FROM src SELECT key, value”)</p>
</blockquote>

<p>若要产生RDDs，则可使用命令results.collect()，之后就可采用针对DataFrame的各种应用变化，当结束应用后，可将读入保存的命令保存到本hdfs环境中</p>

<blockquote>
  <p>df = sqlContext.read.load(“examples/src/main/resources/people.json”, format=”json”) <br>
  df.select(“name”, “age”).write.save(“namesAndAges.parquet”, format=”parquet”)</p>
</blockquote>

<p>PS:若要实现对DataFrame的sql查询，可以采用如下转化命令，之后将people视同hive表进行sql查询。</p>

<blockquote>
  <p>results.registerTempTable(“people”)</p>
</blockquote>

<p>注意：若使用HiveContext读取hive表的数据，然后使用registerTempTable()后的表名只能通过HiveContext.sql查询，若使用sqlContext查询将会报错找不到表名people</p>

<p>另：附上两篇学习pyspark的官方指导翻译篇： <br>
<a href="http://cholerae.com/2015/04/11/-%E7%BF%BB%E8%AF%91-Spark%E7%BC%96%E7%A8%8B%E6%8C%87%E5%8D%97-Python%E7%89%88/" rel="nofollow">Spark编程指南(Python版)</a> <br>
<a href="http://www.fuqingchuan.com/2015/04/280.html" rel="nofollow">spark机器学习库指南[Spark 1.3.1版]</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>