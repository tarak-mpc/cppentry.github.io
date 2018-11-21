---
layout:     post
title:      07大数据内存计算spark系列贴-spark SQL
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/codemosi/article/details/41940505				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div>   （原文地址：<a href="http://blog.csdn.net/codemosi/article/category/2777045" rel="nofollow">http://blog.csdn.net/codemosi/article/category/2777045</a>，转载麻烦带上原文地址。hadoop hive hbase mahout storm spark kafka flume,等连载中，做个爱分享的人<img alt="奋斗" src="http://static.blog.csdn.net/xheditor/xheditor_emot/default/struggle.gif">）﻿﻿</div>
<div align="left">版本 spark 1.1.0</div>
<br><div align="left"><span style="font-size:32px;"><span style="color:#FF0000;">spark SQL 简介</span></span></div>
<div align="left">            spark SQL 用来替代shark来，做基于spark内核的，面向结构化数据的的数据仓库。1.10支持 jdbc</div>
<br><div align="left">            spark SQL查询的结构化数据（RDD，parquet，json），可以作为spark 的分布式的数据集（RDD），集成API在Python中，Scala和Java程序中，这意味着spark SQL可以集成其他spark的组件，如mllib+spark SQL提供SQL+机器学习的超复杂程序。SchemaRDD 对象对各种结构化数据源提供了一致的编程API。对程序员来说。<span style="color:#FF0000;">spark SQL ==</span><span style="color:#FF0000;">SchemaRDD</span>
 的API。</div>
<br><div align="left">            spark SQL 可以直接复用 hive 的数据仓库，和任务脚本。</div>
<br><div align="left"><span style="font-size:32px;"><span style="color:#FF0000;">spark SQL 编程API介绍</span></span></div>
<div align="left"><span style="color:#FF0000;">//1 数据源转成</span><span style="color:#FF0000;">SchemaRDD</span></div>
<div align="left">  /**<br>
   * Registers this RDD as a temporary table using the given name.  The lifetime of this temporary<br>
   * table is tied to the [[SQLContext]] that was used to create this SchemaRDD.<br>
   *<br>
   * @group schema<br>
   */<br>
  def <span style="color:#FF0000;">registerTempTable</span>(tableName: String): Unit = {<br>
    sqlContext.registerRDDAsTable(baseSchemaRDD, tableName)<br>
  }</div>
<br><div align="left"><span style="color:#FF0000;">//2 </span><span style="color:#FF0000;">SchemaRDD的sql查询方法</span></div>
<div align="left">/**<br>
   * Executes a SQL query using Spark, returning the result as a SchemaRDD.  The dialect that is<br>
   * used for SQL parsing can be configured with 'spark.sql.dialect'.<br>
   *<br>
   * @group userf<br>
   */<br>
  def <span style="color:#FF0000;">sql</span>(sqlText: String       // <span style="color:#FF0000;">
要执行的SQL</span><span style="color:#FF0000;">语句</span></div>
<div align="left">): SchemaRDD = {<br>
    if (dialect == "sql") {      //默认的sqlcontext 的 dialect 方言默认使用sql 来查询<br>
      new SchemaRDD(this, parseSql(sqlText))<br>
    } else {                          //HIVE 通过改变 方言，可以改为 hiveQL 语法，来解析 语句 <br>
      sys.error(s"Unsupported SQL dialect: $dialect")<br>
    }<br>
  }</div>
<br><div align="left"><span style="color:#FF0000;">//3 spark sql = <strong>SchemaRDD，</strong>SchemaRDD继承RDD，所以sql后的结果集，可以使用RDD的方法来解析</span></div>
<div align="left">class SchemaRDD(<br>
    @transient val sqlContext: SQLContext,<br>
    @transient val baseLogicalPlan: LogicalPlan)<br>
  <span style="color:#FF0000;">extends</span> <strong><span style="color:#FF0000;">RDD</span></strong>[Row](sqlContext.sparkContext, Nil) with SchemaRDDLike {</div>
<br><div align="left"><span style="font-size:32px;">spark SQL的sql官方例子</span></div>
<div align="left"><span style="font-size:24px;">1 spark sql的基础使用流程</span></div>
<br><div align="left">//0 准备spark sql的上下文环境</div>
<div align="left">  val sparkConf = new SparkConf().setAppName("rdc spark sql test")</div>
<div align="left">   val sc = new SparkContext(sparkConf)<br>
    val sqlContext = new SQLContext(sc)<br>
    import sqlContext._</div>
<br><div align="left">//1 准备数据</div>
<div align="left">    val rdd = sc.parallelize((1 to 100).map(i =&gt; Record(i, s"val_$i")))<br>
//2 转成<span style="color:#000000;">SchemaRDD</span> </div>
<div align="left">    rdd.<span style="color:#FF0000;">registerTempTable</span>("records")</div>
<br><div align="left">//3 执行sql查询<br>
    <span style="color:#FF0000;">sql</span>("SELECT * FROM records").collect().foreach(println)</div>
<br><div align="left"><span style="font-size:24px;">2 spark sql 读取hive</span></div>
<div align="left"></div>
//0 准备spark sql 连 hive的上下文环境
<div align="left">val sparkConf = new SparkConf().setAppName("HiveFromSpark")<br>
    val sc = new SparkContext(sparkConf)<br>
    val hiveContext = new HiveContext(sc)<br>
    import hiveContext._</div>
<br><div align="left">//2 spark sql查询hive原有数据<br>
    val rddFromSql = sql("SELECT key, value FROM src WHERE key &lt; 10 ORDER BY key")</div>
<br><div align="left"><span style="font-size:24px;">2 spark sql 1.1.0 中，使用 jdbc来使用spark sql</span></div>
<br><div align="left">和jdbc访问mysql一样。</div>
<div>﻿﻿</div>
            </div>
                </div>