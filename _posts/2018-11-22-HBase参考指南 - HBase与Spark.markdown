---
layout:     post
title:      HBase参考指南 - HBase与Spark
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>Apache Spark是一个运行于内存中的分布式数据处理框架，但很多情况下用来代替MapReduce（应该是特指Hadoop的MapReduce）。</p>

<p>Spark超出了本文档的范围，请在Spark官网参考Spark项目及其子项目。这里只关注Spark与HBase四个主要的交互点：</p>

<h3>Basic Spark</h3>

<p style="text-indent:50px;">在Spark DAG中获取HBase连接的能力</p>

<h3>Spark Streaming</h3>

<p style="text-indent:50px;">在Spark Streaming中获取HBase连接的能力</p>

<h3>Spark Bulk Load</h3>

<p style="text-indent:50px;">通过直接写HBase HFiles文件而具有批量写HBase的能力</p>

<h3>SparkSQL/DataFrames</h3>

<p style="text-indent:50px;">The ability to write SparkSQL that draws on tables that are represented in HBase.</p>

<p>下面的章节会涉及所有这些交互点的例子。</p>

<p> </p>

<h2>103. Basic Spark</h2>

<p>这节会讨论Spark HBase最简单与最底层的交互。所有的其他交互点都基于这里描述到的概念。</p>

<p>Spark与HBase交互的根源在于HBaseContext。HBaseContext包含HBase配置并把它们推到Spark Executors。这赋予我们每个Spark Executor都有一个静态HBase连接（这个Executor上所有的Spark Task都有可以共用这个HBase连接）。</p>

<p>Spark Executors可以与Region Servers在同一个节点，也可以在不同的没有物理依赖的节点。把Spark Executor想像成一个多线程的客户端应用，它允许运行在这台Executors上的Spark Tasks访问这个共享的连接对象。</p>

<p><em>Example 31. HBaseContext Usage Example</em></p>

<pre class="has">
<code>//这个例子示范了在一个RDD中，HBaseContext如何被用来做一个foreachPartition。
val sc = new SparkContext("local", "test")
val config = new HBaseConfiguration()

...

val hbaseContext = new HBaseContext(sc, config)

rdd.hbaseForeachPartition(hbaseContext, (it, conn) =&gt; {
 val bufferedMutator = conn.getBufferedMutator(TableName.valueOf("t1"))
 it.foreach((putRecord) =&gt; {
. val put = new Put(putRecord._1)
. putRecord._2.foreach((putValue) =&gt; put.addColumn(putValue._1, putValue._2, putValue._3))
. bufferedMutator.mutate(put)
 })
 bufferedMutator.flush()
 bufferedMutator.close()
})</code></pre>

<blockquote>
<p>java示例忽略</p>
</blockquote>

<p>Spark与HBase之间的所有功能，都支持scala与java。Spark SQL支持了所有的Spark支持的语言。在剩下的文档中，我们会专注于scala语言。</p>

<p>上面的例子阐明了如何用一个HBase Connectio做一个foreachPartition。一些其他Spark基本功能支持开箱即用：</p>

<p>bulkPut</p>

<p>  用于大量并发HBase Put操作</p>

<p>bulkDelete</p>

<p>  用于大量并发HBase delete操作</p>

<p>bulkGet</p>

<p>  用于大量并发的用来创建一个新RDD的HBase get操作</p>

<p>mapPartitionn</p>

<p>  用一个允许完全访问HBase的连接对象，来执行一个spark Map函数</p>

<p>hBaseRDD</p>

<p>  创建一个RDD来简化一个分布式Scan操作</p>

<p>对于这些功能点的例子，请查看HBase-Spark模块.</p>

<hr><h2>104. Spark Streaming</h2>

<p>Spark Streaming是一个基于Spark的小批次流处理框架。HBase与Spark Streaming有较深的合作，HBase可以为Spark Streaming提供以下服务：</p>

<ul><li>提供一个在飞速运行中获取相关数据或者配置的地方</li>
	<li>提供一个存储计数或者聚合数据的地方，在一定程序上支持了Spark Streaming “只执行一次”的承诺</li>
</ul><p>HBase-Spark模块的交互点， 与HBase和Spark Streaming的交互点是相似的，之前的命令可直接运行于Spark Streming DStream:</p>

<blockquote>
<p>bulkPut, bulkDelete, bulkGet, mapPartition, hBaseRDD</p>
</blockquote>

<p><em>Example 32. <code>bulkPut</code> Example with DStreams</em></p>

<pre class="has">
<code>//bulkPut与DStream的这个例子， 与RDD bulk put特别相似
val sc = new SparkContext("local", "test")
val config = new HBaseConfiguration()

val hbaseContext = new HBaseContext(sc, config)
val ssc = new StreamingContext(sc, Milliseconds(200))

val rdd1 = ...
val rdd2 = ...

val queue = mutable.Queue[RDD[(Array[Byte], Array[(Array[Byte],
    Array[Byte], Array[Byte])])]]()

queue += rdd1
queue += rdd2

val dStream = ssc.queueStream(queue)

dStream.hbaseBulkPut(
  hbaseContext,
  TableName.valueOf(tableName),
  (putRecord) =&gt; {
   val put = new Put(putRecord._1)
   putRecord._2.foreach((putValue) =&gt; put.addColumn(putValue._1, putValue._2, putValue._3))
   put
  })</code></pre>

<p>这个例子中，有三个inputs输入到hbaseBulkPut函数。 hbaseContex携带配置信息，并把我们链接到executors中的HBase Connection。最终这个函数会转化DStream中的每一个记录到对应的HBase Put Object。</p>

<p> </p>

<h2>105. Bulk Load</h2>

<p>Spark有两种批量导入数据到HBase的方法 。一种是基本的批量导入功能， 用于一行有几百万个columns或者你的columns在map端未经consolidated与分区。（There is the basic bulk load functionality that will work for cases where your rows have millions of columns and cases where your columns are not consolidated and partitions before the on the map side of the Spark bulk load process.）</p>

<p>另外一种是轻量级的批量导入，这种方法是为每行小于10K columns的表而设计，它的优势是高吞吐以及在Spark shuffle操作上有较少的负载。</p>

<p>两种实现都或多或少类似于MapReduce的批量加载方式。分区器基于regeion splits划分rowkeys，并将row key顺序地传送到reducers，这样HFiles就可以在reduce阶段直接写出来。</p>

<p>用Spark术语来说， bulk load是实现在一个Spark repartitionAndSortWithinPartitions，接着是Spark <code>foreachPartition。</code></p>

<p>首先看第一种，基本的批量加载功能：</p>

<p><em>Example 33. Bulk Loading Example</em></p>

<pre class="has">
<code>val sc = new SparkContext("local", "test")
val config = new HBaseConfiguration()

val hbaseContext = new HBaseContext(sc, config)

val stagingFolder = ...
val rdd = sc.parallelize(Array(
      (Bytes.toBytes("1"),
        (Bytes.toBytes(columnFamily1), Bytes.toBytes("a"), Bytes.toBytes("foo1"))),
      (Bytes.toBytes("3"),
        (Bytes.toBytes(columnFamily1), Bytes.toBytes("b"), Bytes.toBytes("foo2.b"))), ...

rdd.hbaseBulkLoad(TableName.valueOf(tableName),
  t =&gt; {
   val rowKey = t._1
   val family:Array[Byte] = t._2(0)._1
   val qualifier = t._2(0)._2
   val value = t._2(0)._3

   val keyFamilyQualifier= new KeyFamilyQualifier(rowKey, family, qualifier)

   Seq((keyFamilyQualifier, value)).iterator
  },
  stagingFolder.getPath)

val load = new LoadIncrementalHFiles(config)
load.doBulkLoad(new Path(stagingFolder.getPath),
  conn.getAdmin, table, conn.getRegionLocator(TableName.valueOf(tableName)))</code></pre>

<p>hbaseBulkLoad函数需要三个参数：</p>

<p>1. 想要批量导入数据的表名(table Name)</p>

<p>2. 一个函数：转化记录为key-value的元组对， Key是KeyFamilyQualifer对象, 含有RowKey,CloumnFamily,CloumnQualifier。 shuffle会根据rowkey分区并基于value进行排序。</p>

<p>3. 用于写HFile的临时地址</p>

<p>Spark bulk load命令后面，就是使用HBase的LoadIncrementalHFiles对象将新创建的HFile文件加载到HBase中。</p>

<p> </p>

<p>Spark Bulk Load的其他参数：</p>

<ul><li>HFile的最大size</li>
	<li>从compactions中排除HFile的标志位（A flag to exclude HFiles from compactions）</li>
	<li>列族设置：compression, bloomType, blockSize, and dataBlockEncoding</li>
</ul><p><em>Example 34. Using Additional Parameters</em></p>

<pre class="has">
<code>val sc = new SparkContext("local", "test")
val config = new HBaseConfiguration()

val hbaseContext = new HBaseContext(sc, config)

val stagingFolder = ...
val rdd = sc.parallelize(Array(
      (Bytes.toBytes("1"),
        (Bytes.toBytes(columnFamily1), Bytes.toBytes("a"), Bytes.toBytes("foo1"))),
      (Bytes.toBytes("3"),
        (Bytes.toBytes(columnFamily1), Bytes.toBytes("b"), Bytes.toBytes("foo2.b"))), ...

val familyHBaseWriterOptions = new java.util.HashMap[Array[Byte], FamilyHFileWriteOptions]
val f1Options = new FamilyHFileWriteOptions("GZ", "ROW", 128, "PREFIX")

familyHBaseWriterOptions.put(Bytes.toBytes("columnFamily1"), f1Options)

rdd.hbaseBulkLoad(TableName.valueOf(tableName),
  t =&gt; {
   val rowKey = t._1
   val family:Array[Byte] = t._2(0)._1
   val qualifier = t._2(0)._2
   val value = t._2(0)._3

   val keyFamilyQualifier= new KeyFamilyQualifier(rowKey, family, qualifier)

   Seq((keyFamilyQualifier, value)).iterator
  },
  stagingFolder.getPath,
  familyHBaseWriterOptions,
  compactionExclude = false,
  HConstants.DEFAULT_MAX_FILE_SIZE)

val load = new LoadIncrementalHFiles(config)
load.doBulkLoad(new Path(stagingFolder.getPath),
  conn.getAdmin, table, conn.getRegionLocator(TableName.valueOf(tableName)))</code></pre>

<p> </p>

<p>现在看下如何使用轻量级批量加载实现:</p>

<p><em>Example 35. Using thin record bulk load</em></p>

<pre class="has">
<code>val sc = new SparkContext("local", "test")
val config = new HBaseConfiguration()

val hbaseContext = new HBaseContext(sc, config)

val stagingFolder = ...
val rdd = sc.parallelize(Array(
      ("1",
        (Bytes.toBytes(columnFamily1), Bytes.toBytes("a"), Bytes.toBytes("foo1"))),
      ("3",
        (Bytes.toBytes(columnFamily1), Bytes.toBytes("b"), Bytes.toBytes("foo2.b"))), ...

rdd.hbaseBulkLoadThinRows(hbaseContext,
      TableName.valueOf(tableName),
      t =&gt; {
        val rowKey = t._1

        val familyQualifiersValues = new FamiliesQualifiersValues
        t._2.foreach(f =&gt; {
          val family:Array[Byte] = f._1
          val qualifier = f._2
          val value:Array[Byte] = f._3

          familyQualifiersValues +=(family, qualifier, value)
        })
        (new ByteArrayWrapper(Bytes.toBytes(rowKey)), familyQualifiersValues)
      },
      stagingFolder.getPath,
      new java.util.HashMap[Array[Byte], FamilyHFileWriteOptions],
      compactionExclude = false,
      20)

val load = new LoadIncrementalHFiles(config)
load.doBulkLoad(new Path(stagingFolder.getPath),
  conn.getAdmin, table, conn.getRegionLocator(TableName.valueOf(tableName)))</code></pre>

<p>与第一种方法的不同之在于，这里返回的key-value元组，第一个值是rowkey， 第二个值是FamiliesQualifiersValues对象。</p>

<p> </p>

<h2>106. SparkSQL/DataFrames</h2>

<p>HBase-Spark Connector (在 HBase-Spark 模块)在Spark-1.2.0中被引进， 在简单的HBase KV存储与复杂的关系型SQL查询架起桥梁。让用户能够使用Spark在HBase上执行复杂的数据分析工作。HBase Dataframe是一个标准的Spark Dataframe，可以与任务data source进行集成，比如Hive, Orc，Parquet, JSON等等。HBase-Spark Connector 应用了像 partition pruning, column pruning, predicate pushdown 和 data locality 等关键技术。</p>

<p>如果需要使用HBase-Spark Connector，用户需要定义用于在HBase与Spark tables之间的模式映射的Catalog，预处理数据与构建HBase tables（prepare the data and populate the HBase table），然后加载HBase DataFrame。这样用户就可以使用SQL语句来查询HBase表。</p>

<p>示例：</p>

<pre class="has">
<code class="hljs">def catalog = s"""{
       |"table":{"namespace":"default", "name":"table1"},
       |"rowkey":"key",
       |"columns":{
         |"col0":{"cf":"rowkey", "col":"key", "type":"string"},
         |"col1":{"cf":"cf1", "col":"col1", "type":"boolean"},
         |"col2":{"cf":"cf2", "col":"col2", "type":"double"},
         |"col3":{"cf":"cf3", "col":"col3", "type":"float"},
         |"col4":{"cf":"cf4", "col":"col4", "type":"int"},
         |"col5":{"cf":"cf5", "col":"col5", "type":"bigint"},
         |"col6":{"cf":"cf6", "col":"col6", "type":"smallint"},
         |"col7":{"cf":"cf7", "col":"col7", "type":"string"},
         |"col8":{"cf":"cf8", "col":"col8", "type":"tinyint"}
       |}
     |}""".stripMargin</code></pre>

<p>Catalog有两个关键的部分：一个是Rowkey的定义，另一个是Spark中table column与HBase中Column Family/column qualifier之间的映射。如上面的示例，HBase的table名称是table1，columns对应对col1 - col8；注意rowkey也像Column一样被定义在col0.</p>

<h3>106.2 保存DataFrame</h3>

<pre class="has">
<code class="hljs">case class HBaseRecord(
   col0: String,
   col1: Boolean,
   col2: Double,
   col3: Float,
   col4: Int,       
   col5: Long,
   col6: Short,
   col7: String,
   col8: Byte)

object HBaseRecord
{                                                                                                             
   def apply(i: Int, t: String): HBaseRecord = {
      val s = s"""row${"%03d".format(i)}"""       
      HBaseRecord(s,
      i % 2 == 0,
      i.toDouble,
      i.toFloat,  
      i,
      i.toLong,
      i.toShort,  
      s"String$i: $t",      
      i.toByte)
  }
}

val data = (0 to 255).map { i =&gt;  HBaseRecord(i, "extra")}

sc.parallelize(data).toDF.write.options(
 Map(HBaseTableCatalog.tableCatalog -&gt; catalog, HBaseTableCatalog.newTable -&gt; "5"))
 .format("org.apache.hadoop.hbase.spark ")
 .save()</code></pre>

<p>准备的数据data有256个HBaseRecord对象。sc.parallelize函数分发数据转化为RDD，.toDF返回一个DataFrame.  write方法返回一个DataFrameWriter用来把DataFrame数据写到外存（这里是HBase）. 指定一个schema Catalog，save方法会创建一个有5个regions的表来存储这个DataFrame。</p>

<h3>106.3. 加载DataFrame</h3>

<pre class="has">
<code class="hljs">def withCatalog(cat: String): DataFrame = {
  sqlContext
  .read
  .options(Map(HBaseTableCatalog.tableCatalog-&gt;cat))
  .format("org.apache.hadoop.hbase.spark")
  .load()
}
val df = withCatalog(catalog)</code></pre>

<p>在上面的示例中，sqlContext是SQLContext的变量，是Spark中用来处理结构化数据（rows与columns）的。read返回的DataFrameReader可以把数据读进来并转成DataFrame。 option指定了读取的参数。format指定数据源是hbase，load方法加载数据到DataFrame。</p>

<h3>106.4 语言集成查询（Language Integrated Query）</h3>

<pre class="has">
<code class="hljs">val s = df.filter(($"col0" &lt;= "row050" &amp;&amp; $"col0" &gt; "row040") ||
  $"col0" === "row005" ||
  $"col0" &lt;= "row005")
  .select("col0", "col1", "col4")
s.show</code></pre>

<p>DataFrame可以做多种操作，比如join, sort, select, filter, orderBy等等。 上面的df.filter使用了给定的SQL表达式。 select 选择了col0,col1,col4的集合。</p>

<h3>106.5 SQL 查询</h3>

<pre class="has">
<code class="hljs">df.registerTempTable("table1")
sqlContext.sql("select count(col1) from table1").show</code></pre>

<p>registerTempTable注册了一个df当成临时的table1使用。这个临时表的生命周期绑定在用来创建df的SQLContext.  sqlContext.sql方法允许用户执行SQL查询</p>

<h3>106.6 其他</h3>

<p><em>Example 36. Query with different timestamps</em></p>

<p>略</p>

<p> </p>

<p> </p>            </div>
                </div>