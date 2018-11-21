---
layout:     post
title:      Spark Sql orderby排序使用测试
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<ul>
<li>版本信息：Spark 2.2-hadoop-2.6</li>
</ul>
<h2><a id="1Spark_Shell1g_4"></a>1、使用Spark Shell方式测试1g数据</h2>
<p>（1）测试sql：SELECT * FROM rddTable order by age</p>
<p>先打开spark集群， /…/…/sbin/start-all.sh</p>
<p>然后启动spark shell：</p>
<p>./spark-shell --master spark://10.47.85.158:7077 --executor-memory 2g --conf spark.default.parallelism=64</p>
<p>Spark web界面:10.47.85.158:8080</p>
<p>Spark Jon web界面：10.47.85.158:4040</p>
<p>下面是在spark shell中的代码：</p>
<p>val sqlContext=new org.apache.spark.sql.SQLContext(sc)</p>
<p>import sqlContext.implicits._</p>
<p>case class Person(id:Long, name:String, age:Int, department: String, job_number: Long, hire_date:String, on_the_job:Boolean)</p>
<p>val rddpeople=sc.textFile(“hdfs://10.47.85.158:9000/SQLData/100M/SQLData_1G.txt”).map(_.split(",")).map(p=&gt;Person(p(0).trim.toLong, p(1), p(2).trim.toInt, p(3), p(4).trim.toLong, p(5), p(6).trim.toBoolean)).toDF()</p>
<p>rddpeople.createOrReplaceTempView(“rddTable”)</p>
<p>sqlContext.sql(“SELECT * FROM rddTable order by age”).toDF().write.format(“csv”).save(“hdfs://10.47.85.158:9000/nht/spark1g/result1g111/”)</p>
<p>测试时间： 11秒 / 13s / 12s</p>
<p><a href="//sqlContext.sql" rel="nofollow">//sqlContext.sql</a>(“SELECT * FROM rddTable order by age”).toDF().write.save(“hdfs://10.47.85.158:9000/nht/spark1g/result1g/”) //默认使用的是parquet格式，而且使用snappy格式压缩</p>
<p><a href="//sqlContext.sql" rel="nofollow">//sqlContext.sql</a>(“SELECT * FROM rddTable order by age”).show() //默认展示20行数据</p>
<h2><a id="2javasubmit_38"></a>2、使用java程序方式，submit提交任务的形式</h2>
<p>参照博客：<a href="https://blog.csdn.net/qq_21383435/article/details/77428659" rel="nofollow">https://blog.csdn.net/qq_21383435/article/details/77428659</a></p>
<p>然后根据上面的代码修改，然后提交到集群运行（提交命令分多种集群形式，local、standalone、yarn clent和yarn cluster）：</p>
<p>./bin/spark-submit \</p>
<p>–class </p>
<p>–master  \</p>
<p>–deploy-mode  \</p>
<p>–conf = \</p>
<pre><code> ... # other options  
</code></pre>
<p> \</p>
<p>[application-arguments]</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>