---
layout:     post
title:      Spark SQL（二十）初识Spark SQL
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/Suubyy/article/details/82563964				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<ol>
<li>
<p><strong><code>Spark SQL</code>定义</strong></p>
<ol>
<li><code>Spark SQL</code>是<code>Spark</code>的一个模块，它是用来处理结构化数据的。</li>
<li>它将任务利用<code>SQL</code>的形式转换成<code>RDD</code>的计算。类似于<code>Hive</code>利用<code>SQL</code>转化成了<code>MapReduce</code>计算。</li>
</ol>
</li>
<li>
<p><strong><code>Spark SQL</code>优点</strong></p>
<ol>
<li>它与<code>Spark Core</code>无缝集成，在项目中我们可以与<code>Spark Core</code>配合实现业务逻辑。</li>
<li>它提供了同一的数据源接口</li>
<li>它内嵌了<code>Hive</code>，可以连接外部已经部署好的<code>Hive</code>数据源，实现了<code>Hive</code>的集成</li>
<li>标准化的数据连接方式，可以开启<code>thrift server</code> 为外部提供<code>jdbc</code>、<code>odbc</code>的连接访问。</li>
</ol>
</li>
<li>
<p><strong><code>Spark SQl</code>的数据抽象</strong></p>
<ol>
<li><code>DataFrame（1.3）</code>：<code>Spark</code>对<code>RDD</code>的一种抽象，也是一种分布式、不可变的数据集，也只是在<code>RDD</code>之上加入了<code>Schema</code>信息，为<code>RDD</code>中的每个元素的每一列加上了名称与数据类型，这样<code>Spark SQL</code>就知道了数据的内部结构，比如知道了每列的名称和类型是什么，所以我们可以把<code>DataFrame</code>看做是一张数据库表，这样<code>Spark</code>为<code>DataFrame</code>提供了更加丰富的<code>API</code>，并且提供了类似于<code>SQL</code>的语法的操作。性能要比<code>RDD</code>性能高，因为<code>Spark</code> 为<code>DataFrame</code>提供了优化器。它的缺点就是编译器是不会进行类型安全检查，只有在运行时进行类型安全检查。<code>DataFrame</code>是懒执行的，它的数据是以二进制的方式存储在非堆内存中，这样大大减少了内存的开销。<code>Spark</code>为<code>DataFrame</code>还提供了执行优化器（<strong><code>catalyst</code></strong>），当我们在利用<code>SparkSQL</code>操作数据的时候，<code>Spark</code>会为我们的查询语法进行自动的优化，提高执行效率。<br>
<img src="https://img-blog.csdn.net/20180910214054461?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1N1dWJ5eQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述"></li>
<li><code>DataSet(1.6)</code>：<code>DataSet</code>是<code>Spark</code>最新的数据抽象，它扩展了<code>DataFrame</code>，弥补了<code>DataFrame</code>的类型安全检查的缺陷，继承了<code>DataFrame</code>的执行优化。<code>DataFrame</code>在编译期间是不知道字段的类型的，只有在运行的时候才知道字段的类型，而<code>DataSet</code>是在编译期间就知道了字段的类型。<code>DataSet</code>支持编解码器。DataFrame=DataSet[Row]，可以利用as方法将DataFrame转换成DataSet，DataSet是一直强类型数据集，在编译器就会校验数据类型。</li>
</ol>
</li>
<li>
<p><strong><code>Spark SQL查询方式</code></strong></p>
<ol>
<li>利用<code>API</code></li>
<li>注册临时表，利用<code>SQL</code>查询</li>
</ol>
<pre><code>package com.lyz.sql.dataframe
import org.apache.spark.sql.{DataFrame, SparkSession}

object DataFrameTest {
  def main(args: Array[String]): Unit = {
    val sparkSession: SparkSession = SparkSession.builder().appName("DataFrameTest").master("local[2]").getOrCreate()
    val peopleDF: DataFrame = sparkSession.read.json("C:\\Users\\39402\\Desktop\\people.json")
    //引入隐式转换
    import sparkSession.implicits._
    /**
      * 打印结果为
      * +---+------+
      * |age|  name|
      * +---+------+
      * | 10|zhang3|
      * | 11|   li4|
      * | 12| wang5|
      * | 13| zhao6|
      * +---+------+
      *
      *
      */
    peopleDF.show()

    /**
      * 打印结果为
      *
      * | 11|  li4|
      * | 12|wang5|
      * | 13|zhao6|
      * +---+-----+
      *
      *
      */
    //第一种方式利用Spark API的方式来处理数据
    //$"age"这是一种DSL表达式，这个表达式相当一个变量，可以进行逻辑运算，例如：$"age"+10
    peopleDF.filter($"age" &gt; 10).show()

    /**
      * 打印结果为
      * +---+------+
      * |age|  name|
      * +---+------+
      * | 10|zhang3|
      * | 11|   li4|
      * | 12| wang5|
      * | 13| zhao6|
      * +---+------+
      */
    //第二种方式利用sql方式处理数据
    //转换成临时表，用sql查询
    peopleDF.createOrReplaceTempView("people")
    sparkSession.sql("select * from people").show()
  }
}

</code></pre>
</li>
</ol>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>