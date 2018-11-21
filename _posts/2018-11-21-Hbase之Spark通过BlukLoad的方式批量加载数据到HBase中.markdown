---
layout:     post
title:      Hbase之Spark通过BlukLoad的方式批量加载数据到HBase中
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/Suubyy/article/details/80892023				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<ol>
<li><p><strong>BlukLoad 定义：</strong></p>

<ol><li>它是一种Hbase的批处理方式，可以提高效率，可作为优化的一部分。 在实际开发中，我们可能处理的数据量比较大，利用普通的<code>Put</code>来想<code>Hbase</code>中插入数据会降低程序的运行效率，所以<code>Hbase</code>为我们提供了批处理，向<code>Hbase</code>批量写入数据提高效率，在<code>Hbase</code>交互式命令行中，Hbase也提供了将数据批量插入到<code>Hbase</code>数据库中，命令行的批量插入原理就是先将文件转换成<code>HFile</code>文件，然后再将<code>HFile</code>文件导入到<code>Hbase</code>数据库中，那么利用API的方式的原理跟命令行的原理是相同的，以下就是利用<code>API</code>实现<code>Spark</code>处理后的数据批量导入到<code>Hbase</code>中。</li></ol></li>
<li><p><strong>BlukLoad 优点：</strong></p>

<ol><li>消除对<code>Hbase</code>集群的写操作的压力，因为导入过程中不占用<code>RegionServer</code>的资源</li>
<li>能够快速导入海量数据</li>
<li>节省内存</li></ol></li>
<li><p><strong>BlukLoad 原理：</strong></p>

<ol><li>利用HBase数据按照<code>HFile</code>格式存储在<code>HDFS</code>的原理，</li>
<li>使用<code>Mapreduce</code>直接生成<code>HFile</code>格式文件后，<code>RegionServers</code>再将<code>HFile</code>文件移动到相应的<code>Region</code>目录下</li></ol></li>
<li><p><strong>BlukLoad 两种方式：</strong></p>

<ol><li><p>单列</p>

<pre class="prettyprint"><code class=" hljs avrasm">package <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.lyz</span><span class="hljs-preprocessor">.hbase</span>

import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.fs</span><span class="hljs-preprocessor">.Path</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span>._
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span>.{ConnectionFactory, HBaseAdmin}
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.ImmutableBytesWritable</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.mapreduce</span>._
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Bytes</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span>.{HBaseConfiguration, KeyValue, TableName}
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapreduce</span><span class="hljs-preprocessor">.Job</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.sql</span><span class="hljs-preprocessor">.SparkSession</span>

object HbaseBatch {
  def main(args: Array[String]): Unit = {
    val spark = SparkSession<span class="hljs-preprocessor">.builder</span>()<span class="hljs-preprocessor">.appName</span>(<span class="hljs-string">"DataInput"</span>)<span class="hljs-preprocessor">.master</span>(<span class="hljs-string">"local[2]"</span>)<span class="hljs-preprocessor">.getOrCreate</span>()
    val sc = spark<span class="hljs-preprocessor">.sparkContext</span>

    val tableNameStr = <span class="hljs-string">"user"</span>

    //获取配置文件的属性，为连接Hbase提供参数
    val configuraton = HBaseConfiguration<span class="hljs-preprocessor">.create</span>

    //连接与Hbase建立连接
    val conn = ConnectionFactory<span class="hljs-preprocessor">.createConnection</span>(configuraton)

    //获取Hbase的表的名字
    val tableName = TableName<span class="hljs-preprocessor">.valueOf</span>(tableNameStr)
    //根据表的名字来得到表
    val table = conn<span class="hljs-preprocessor">.getTable</span>(tableName)

    //设定HBase的输入表
    configuraton<span class="hljs-preprocessor">.set</span>(TableInputFormat<span class="hljs-preprocessor">.INPUT</span>_TABLE, tableNameStr)

    //初始化一个Job
    val job = Job<span class="hljs-preprocessor">.getInstance</span>(configuraton)

    //设置输出的Key类型
    job<span class="hljs-preprocessor">.setMapOutputKeyClass</span>(classOf[ImmutableBytesWritable])

    //设置输出值的类型
    job<span class="hljs-preprocessor">.setMapOutputValueClass</span>(classOf[KeyValue])

    val regionLocator = conn<span class="hljs-preprocessor">.getRegionLocator</span>(tableName)
    HFileOutputFormat2<span class="hljs-preprocessor">.configureIncrementalLoad</span>(job, table, regionLocator)

    val array = Array(<span class="hljs-number">1</span> to <span class="hljs-number">10</span>: _*)
    val value = sc<span class="hljs-preprocessor">.parallelize</span>(array)
    val rdd = value<span class="hljs-preprocessor">.map</span>(<span class="hljs-built_in">x</span> =&gt; {
      //KeyValue 是Hbase数据存储的单元
      val kv: KeyValue = new KeyValue(Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-built_in">x</span>), <span class="hljs-string">"info"</span><span class="hljs-preprocessor">.getBytes</span>(), <span class="hljs-string">"c1"</span><span class="hljs-preprocessor">.getBytes</span>(), <span class="hljs-string">"value_xxx"</span><span class="hljs-preprocessor">.getBytes</span>())
      (new ImmutableBytesWritable(Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-built_in">x</span>)), kv)
    })


    //数据转换成HFile，导入到HDFS目录中
    rdd<span class="hljs-preprocessor">.saveAsNewAPIHadoopFile</span>(<span class="hljs-string">"/hbase/data1"</span>, classOf[ImmutableBytesWritable], classOf[KeyValue], classOf[HFileOutputFormat2], configuraton)


    //将HDFS上的HFile导入到Hbase的表中
    val bulkLoader = new LoadIncrementalHFiles(configuraton)
    bulkLoader<span class="hljs-preprocessor">.doBulkLoad</span>(new Path(<span class="hljs-string">"/hbase/data1"</span>), conn<span class="hljs-preprocessor">.getAdmin</span>, table, regionLocator)
  }
}
</code></pre></li>
<li><p>多列，在这里有一个比较重要的隐式转换来增强RDD的功能<code>import org.apache.hadoop.hbase.spark.HBaseRDDFunctions._</code></p>

<pre class="prettyprint"><code class=" hljs scala"><span class="hljs-keyword">package</span> com.lyz.hbase.domian

<span class="hljs-javadoc">/**
  * 类的注释
  *
  * <span class="hljs-javadoctag">@Package</span> com.lyz.hbase.domian
  * <span class="hljs-javadoctag">@ClassName</span> HbaseDomain
  * <span class="hljs-javadoctag">@Description</span> ${TODO}
  * <span class="hljs-javadoctag">@Author</span> liyuzhi
  * <span class="hljs-javadoctag">@Date</span> 2018-07-04 9:35
  */</span>
<span class="hljs-class"><span class="hljs-keyword">case</span> <span class="hljs-keyword">class</span> <span class="hljs-title">HbaseDomain</span><span class="hljs-params">(id: String, name: String, age: Int)</span></span>
</code></pre>

<pre class="prettyprint"><code class=" hljs avrasm">package <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.lyz</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.HbaseService</span>

import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.UUID</span>

import <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.lyz</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.domian</span><span class="hljs-preprocessor">.HbaseDomain</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.avro</span><span class="hljs-preprocessor">.SchemaBuilder</span><span class="hljs-preprocessor">.ArrayBuilder</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Bytes</span>

import scala<span class="hljs-preprocessor">.collection</span><span class="hljs-preprocessor">.mutable</span><span class="hljs-preprocessor">.ListBuffer</span>

object HbaseService {

  def getHbaseDomain: ListBuffer[HbaseDomain] = {

    val hbaseDomainList = new ListBuffer[HbaseDomain]
    for (i &lt;- <span class="hljs-number">1</span> to <span class="hljs-number">10</span>) {
      val uuid = UUID<span class="hljs-preprocessor">.randomUUID</span>()<span class="hljs-preprocessor">.toString</span>
      val hbaseDomain = new HbaseDomain(uuid, <span class="hljs-string">"liyuzhi"</span> + i, i)
      hbaseDomainList<span class="hljs-preprocessor">.append</span>(hbaseDomain)
    }
    hbaseDomainList
  }

  def getHbaseDomainArray: ListBuffer[(Array[Byte], Array[(Array[Byte], Array[Byte], Array[Byte])])] = {
    val domains = getHbaseDomain
    val array = new ListBuffer[(Array[Byte], Array[(Array[Byte], Array[Byte], Array[Byte])])]
    domains<span class="hljs-preprocessor">.foreach</span>(domain =&gt; {
      array<span class="hljs-preprocessor">.append</span>((Bytes<span class="hljs-preprocessor">.toBytes</span>(domain<span class="hljs-preprocessor">.id</span>), Array((Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"info"</span>), Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"name"</span>), Bytes<span class="hljs-preprocessor">.toBytes</span>(domain<span class="hljs-preprocessor">.name</span>)))))
      array<span class="hljs-preprocessor">.append</span>((Bytes<span class="hljs-preprocessor">.toBytes</span>(domain<span class="hljs-preprocessor">.id</span>), Array((Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"info"</span>), Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"age"</span>), Bytes<span class="hljs-preprocessor">.toBytes</span>(domain<span class="hljs-preprocessor">.age</span>)))))
    })
    array
  }
}
</code></pre>

<pre class="prettyprint"><code class=" hljs avrasm">package <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.lyz</span><span class="hljs-preprocessor">.hbase</span>

import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.fs</span><span class="hljs-preprocessor">.Path</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.ConnectionFactory</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.mapreduce</span><span class="hljs-preprocessor">.LoadIncrementalHFiles</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.HBaseRDDFunctions</span>._
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.spark</span>.{HBaseContext, KeyFamilyQualifier}
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Bytes</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span>.{HBaseConfiguration, TableName}
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.SparkContext</span>

object HbaseBatch1 {
  def main(args: Array[String]): Unit = {

    val sc = new SparkContext(<span class="hljs-string">"local"</span>, <span class="hljs-string">"test"</span>)
    //获取配置文件的属性，为连接Hbase提供参数
    val configuraton = HBaseConfiguration<span class="hljs-preprocessor">.create</span>
    val hbaseContext = new HBaseContext(sc, configuraton)

    val tableNameStr = <span class="hljs-string">"user"</span>
    val loadPathStr = <span class="hljs-string">"/hbase/data1"</span>

    //连接与Hbase建立连接
    val conn = ConnectionFactory<span class="hljs-preprocessor">.createConnection</span>(configuraton)
    //获取Hbase表
    val table = conn<span class="hljs-preprocessor">.getTable</span>(TableName<span class="hljs-preprocessor">.valueOf</span>(tableNameStr))
    val rdd = sc<span class="hljs-preprocessor">.parallelize</span>(HbaseService<span class="hljs-preprocessor">.HbaseService</span><span class="hljs-preprocessor">.getHbaseDomainArray</span>)

    rdd<span class="hljs-preprocessor">.hbaseBulkLoad</span>(hbaseContext, TableName<span class="hljs-preprocessor">.valueOf</span>(tableNameStr),
      t =&gt; {
        val rowKey = t._1
        val family: Array[Byte] = t._2(<span class="hljs-number">0</span>)._1
        val qualifier = t._2(<span class="hljs-number">0</span>)._2
        val value = t._2(<span class="hljs-number">0</span>)._3
        val keyFamilyQualifier = new KeyFamilyQualifier(rowKey, family, qualifier)
        Seq((keyFamilyQualifier, value))<span class="hljs-preprocessor">.iterator</span>
      },
      loadPathStr)

    val load = new LoadIncrementalHFiles(configuraton)
    load<span class="hljs-preprocessor">.doBulkLoad</span>(new Path(loadPathStr), conn<span class="hljs-preprocessor">.getAdmin</span>, table, conn<span class="hljs-preprocessor">.getRegionLocator</span>(TableName<span class="hljs-preprocessor">.valueOf</span>(tableNameStr)))
  }
}
</code></pre></li>
<li><p>命令行终端方式</p>

<ol><li><p>（利用importtsv方法迁徙tsv数据，这个缺点是数据只保存在内存中，并没有保存磁盘中）</p>

<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-constant">HADOOP_CLASSPATH</span>=`<span class="hljs-variable">${</span><span class="hljs-constant">HBASE_HOME</span>}/bin/hbase classpath` \
<span class="hljs-variable">${</span><span class="hljs-constant">HADOOP_HOME</span>}/bin/hadoop jar <span class="hljs-variable">${</span><span class="hljs-constant">HBASE_HOME</span>}/lib/hbase-server-<span class="hljs-number">1.2</span>.<span class="hljs-number">0</span>-cdh5.<span class="hljs-number">7.0</span>.jar \
importtsv  -<span class="hljs-constant">Dimporttsv</span>.columns=<span class="hljs-constant">HBASE_ROW_KEY</span>,<span class="hljs-symbol">info:</span>name,<span class="hljs-symbol">info:</span>age,<span class="hljs-symbol">info:</span>address,<span class="hljs-symbol">info:</span>phone \
student <span class="hljs-symbol">hdfs:</span>/<span class="hljs-regexp">/hadoop001:8020/opt</span><span class="hljs-regexp">/testfile/importtsv</span>   </code></pre></li>
<li><p>最常用的方法，而且能够大批量的迁徙数据，必会的</p>

<ol><li><p>先将数据变成Hfile</p>

<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-constant">HADOOP_CLASSPATH</span>=`<span class="hljs-variable">${</span><span class="hljs-constant">HBASE_HOME</span>}/bin/hbase classpath` <span class="hljs-variable">${</span><span class="hljs-constant">HADOOP_HOME</span>}/bin/hadoop jar \
<span class="hljs-variable">${</span><span class="hljs-constant">HBASE_HOME</span>}/lib/hbase-server-<span class="hljs-number">1.2</span>.<span class="hljs-number">0</span>-cdh5.<span class="hljs-number">7.0</span>.jar importtsv  -<span class="hljs-constant">Dimporttsv</span>.columns=<span class="hljs-constant">HBASE_ROW_KEY</span>,<span class="hljs-symbol">info:</span>name,<span class="hljs-symbol">info:</span>age,<span class="hljs-symbol">info:</span>address,<span class="hljs-symbol">info:</span>phone \
-<span class="hljs-constant">Dimporttsv</span>.bulk.output=<span class="hljs-symbol">hdfs:</span>/<span class="hljs-regexp">/hadoop001:8020/opt</span><span class="hljs-regexp">/testfile/hfileoutput</span> \
student2 <span class="hljs-symbol">hdfs:</span>/<span class="hljs-regexp">/hadoop001:8020/opt</span><span class="hljs-regexp">/testfile/importtsv</span></code></pre></li>
<li><p>将Hfile同步到Hbase数据库里</p>

<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-constant">HADOOP_CLASSPATH</span>=`<span class="hljs-variable">${</span><span class="hljs-constant">HBASE_HOME</span>}/bin/hbase classpath` <span class="hljs-variable">${</span><span class="hljs-constant">HADOOP_HOME</span>}/bin/hadoop jar <span class="hljs-variable">${</span><span class="hljs-constant">HBASE_HOME</span>}/lib/hbase-server-<span class="hljs-number">1.2</span>.<span class="hljs-number">0</span>-cdh5.<span class="hljs-number">7.0</span>.jar \
completebulkload <span class="hljs-symbol">hdfs:</span>/<span class="hljs-regexp">/hadoop001:8020/opt</span><span class="hljs-regexp">/testfile/hfileoutput</span> student2</code></pre></li></ol></li></ol></li></ol></li>
<li><p><strong>注意：</strong>当我们在向<code>Hbase</code>迁徙大量数据的时候，在创建表的时候需要创建多个<code>Region</code>，因为默认创建一张表值创建一个<code>Region</code>，那么当我们向表中插入大量数据的时候，由于只有一个<code>Region</code>，而<code>Region</code>是被<code>RegionServer</code>管理的，由此会增加单个服务器的内存压力，有可能宕机。所以我们在创建表的时候应该多创建<code>Region</code>，根据预估<code>rowkey</code>来创建。</p></li>
</ol>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>