---
layout:     post
title:      MongoDB on Spark的参数优化
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_33689414/article/details/83421776				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2><a id="MongoDB_on_Spark_0"></a>MongoDB on Spark的参数优化</h2>
<h3><a id="mongodb_on_spark_2"></a>mongodb on spark的参数：</h3>
<p>mongodb on spark的参数基本分为输入参数和输出参数。</p>
<h4><a id="1_mongodb_on_spark_6"></a>1. mongodb on spark输入的参数：</h4>
<ul>
<li>
<p>spark.mongodb.input.uri：mongo的uri，</p>
<p>如：mongodb://host:port/</p>
<p>或者直接指定数据库和集合：mongodb://host:port/database.collection，相当于使用了uri，database，collection参数</p>
</li>
<li>
<p>spark.mongodb.input.database：mongo的数据库指定</p>
</li>
<li>
<p>spark.mongodb.input.collection：mongo的集合</p>
</li>
<li>
<p><strong>spark.mongodb.input.partitioner</strong>：用于对数据进行分区的分区程序的类名</p>
<p>默认使用：MongoDefaultPartitioner，其他值有：</p>
<ul>
<li>
<p>MongoSamplePartitioner：使用集合的平均文档大小和随机抽样来确定集合的合适分区。</p>
</li>
<li>
<p>MongoShardedPartitioner：根据数据块对集合进行分区。需要对config数据库的读访问权限。</p>
</li>
<li>
<p>MongoSplitVectorPartitioner：使用splitVector独立命令或主数据库上的命令来确定数据库的分区。需要特权才能运行splitVector命令</p>
</li>
<li>
<p>MongoPaginateByCountPartitioner：创建特定数量的分区。需要查询每个分区。</p>
</li>
<li>
<p>MongoPaginateBySizePartitioner：根据数据大小创建分区。需要查询每个分区。</p>
</li>
</ul>
<p>每个Partitioner类都有option参数，具体参阅<a href="https://docs.mongodb.com/spark-connector/current/configuration/#partitioner-conf" rel="nofollow">文档</a></p>
</li>
</ul>
<h4><a id="2_mongodb_on_spark_34"></a>2. mongodb on spark输出的参数</h4>
<ul>
<li>
<p>spark.mongodb.output.uri：和输入参数一样</p>
</li>
<li>
<p>spark.mongodb.output.database：和输入参数一样</p>
</li>
<li>
<p>spark.mongodb.output.collection：和输入参数一样</p>
</li>
<li>
<p>spark.mongodb.output.replaceDocument：保存包含_id字段的数据集时替换整个文档。如果为false，它将仅更新文档中与数据集中的字段匹配的字段。默认true。</p>
</li>
<li>
<p>maxBatchSize：保存数据时批量操作的最大批量大小。默认512M。</p>
</li>
</ul>
<hr>
<h4><a id="3_mongodb_on_spark_48"></a>3. mongodb on spark的参数添加</h4>
<p>mongodb on spark的参数添加有2种方式，可以通过spark的conf添加，也可以通过option添加参数的方式进行添加。</p>
<p>3.1 通过config的方式进行添加</p>
<pre><code>val spark = SparkSession.builder()
      .master("local")
      .appName("MongoSparkConnectorIntro")
      .config("spark.mongodb.input.uri", "mongodb://127.0.0.1/test.myCollection")
      .config("spark.mongodb.output.uri", "mongodb://127.0.0.1/test.myCollection")
      .getOrCreate()
</code></pre>
<p>这里也可以通过config添加其他参数。</p>
<p>3.2 通过option的方式进行添加</p>
<pre><code># 输入参数添加

spark.read.format("com.mongodb.spark.sql.DefaultSource") \
    .option("spark.mongodb.input.partitioner", "MongoPaginateByCountPartitioner") \
    .option("spark.mongodb.input.partitionerOptions.partitionKey", 'service_time') \
    .option("spark.mongodb.input.partitionerOptions.numberOfPartitions", "10") \
    .schema(collect_schema) \
    .load()



# 输出参数添加

df.write.format('com.mongodb.spark.sql.DefaultSource')
    .option("spark.mongodb.output.uri", "mongodb://127.0.0.1/test.myCollection") \
    .mode("append")\
    .save()
</code></pre>
<hr>
<p>参考文档：<br>
<a href="https://docs.mongodb.com/spark-connector/current/configuration/" rel="nofollow">MongoDB on Spark的参数优化</a></p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>