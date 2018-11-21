---
layout:     post
title:      Spark 11 Spark SQL 实战：日志分析（四）spark on yarn
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/lihaogn/article/details/82291451				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3 id="1-介绍">1 介绍</h3>

<p>在spark中，支持4种运行模式：</p>

<ul>
<li>local：开发时使用</li>
<li>standalone：spark自带，如果一个集群时standalone的话，就需要在多台机器上同时部署spark环境。</li>
<li>yarn：建议在生产上使用该模式，统一使用yarn进行整个集群作业（MR，spark）的资源调度。</li>
<li>mesos</li>
</ul>

<p>注意：</p>

<ul>
<li>不管使用什么模式，spark应用程序的代码是一模一样的</li>
<li>spark支持可插拔的集群管理模式</li>
<li>对于yarn而言，spark application仅仅是一个客户端而已</li>
</ul>

<p>1）yarn之client模式</p>

<ul>
<li>driver运行在client端（提交spark作业的机器）</li>
<li>client会和请求到的container进行通信来完成作业的调度和执行，client是不能退出的</li>
<li>日志信息会在控制台输出，便于我们测试</li>
<li>application master 负责资源的申请</li>
</ul>

<p>2）yarn之cluster模式</p>

<ul>
<li>driver运行在application master中。</li>
<li>client只要提交完作业之后就可以关掉，因为作业已经在yarn上运行了。</li>
<li>日志在终端是看不见的，因为日志在driver上，只能通过yarn logs -applicationId application_id查看。</li>
<li>application master：资源申请与任务调度。</li>
</ul>



<h3 id="2-程序">2 程序</h3>

<p>1）修改pom.xml文件</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-comment">&lt;!-- 以下依赖不需要打进jar包 添加&lt;scope&gt; --&gt;</span>
<span class="hljs-comment">&lt;!-- scala --&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.scala-lang<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>scala-library<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>${scala.version}<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">scope</span>&gt;</span>provided<span class="hljs-tag">&lt;/<span class="hljs-title">scope</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

<span class="hljs-comment">&lt;!-- sparksql --&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-sql_2.11<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>${spark.version}<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">scope</span>&gt;</span>provided<span class="hljs-tag">&lt;/<span class="hljs-title">scope</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

<span class="hljs-comment">&lt;!-- 使用hiveContext需要的依赖--&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-hive_2.11<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>${spark.version}<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">scope</span>&gt;</span>provided<span class="hljs-tag">&lt;/<span class="hljs-title">scope</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

<span class="hljs-comment">&lt;!-- jdbc 操作依赖 使用thriftserver--&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.spark-project.hive<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>hive-jdbc<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.2.1.spark2<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">scope</span>&gt;</span>provided<span class="hljs-tag">&lt;/<span class="hljs-title">scope</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span></code></pre>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-comment">&lt;!-- 添加plugin，将需要的依赖一起打包--&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">plugin</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>maven-assembly-plugin<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">archive</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">manifest</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">mainClass</span>&gt;</span><span class="hljs-tag">&lt;/<span class="hljs-title">mainClass</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">manifest</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">archive</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">descriptorRefs</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">descriptorRef</span>&gt;</span>jar-with-dependencies<span class="hljs-tag">&lt;/<span class="hljs-title">descriptorRef</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">descriptorRefs</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">plugin</span>&gt;</span></code></pre>

<p>2）SparkStatCleanJobYarn.scala</p>

<pre class="prettyprint"><code class=" hljs avrasm">package log

import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.sql</span>.{SaveMode, SparkSession}

<span class="hljs-comment">/**
  * spark清洗操作运行在yarn上
  */</span>
object SparkStatCleanJobYarn {

  def main(args: Array[String]): Unit = {

    if (args<span class="hljs-preprocessor">.length</span>!=<span class="hljs-number">2</span>) {
      println(<span class="hljs-string">"usage: sparkstatcleanjobyarn &lt;inputpath&gt; &lt;outputpath&gt;"</span>)
      System<span class="hljs-preprocessor">.exit</span>(<span class="hljs-number">1</span>)
    }

    val Array(inputPath,outputPath)=args

    val spark=SparkSession<span class="hljs-preprocessor">.builder</span>()<span class="hljs-preprocessor">.getOrCreate</span>()

    val accessRDD=spark<span class="hljs-preprocessor">.sparkContext</span><span class="hljs-preprocessor">.textFile</span>(inputPath)

    val accessDF = spark<span class="hljs-preprocessor">.createDataFrame</span>(accessRDD<span class="hljs-preprocessor">.map</span>(<span class="hljs-built_in">x</span> =&gt; AccessConvertUtil<span class="hljs-preprocessor">.parseLog</span>(<span class="hljs-built_in">x</span>)),
      AccessConvertUtil<span class="hljs-preprocessor">.struct</span>)

    accessDF<span class="hljs-preprocessor">.coalesce</span>(<span class="hljs-number">1</span>)<span class="hljs-preprocessor">.write</span><span class="hljs-preprocessor">.format</span>(<span class="hljs-string">"parquet"</span>)<span class="hljs-preprocessor">.mode</span>(SaveMode<span class="hljs-preprocessor">.Overwrite</span>)
      <span class="hljs-preprocessor">.partitionBy</span>(<span class="hljs-string">"day"</span>)<span class="hljs-preprocessor">.save</span>(outputPath)

    spark<span class="hljs-preprocessor">.stop</span>()

  }
}
</code></pre>

<p>3）<strong>TopNStatJobYARN.scala</strong>，更改TopNStatJob.scala中的main函数</p>

<pre class="prettyprint"><code class=" hljs python"><span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">main</span><span class="hljs-params">(args: Array[String])</span>:</span> Unit = {

    <span class="hljs-keyword">if</span> (args.length != <span class="hljs-number">2</span>) {
      println(<span class="hljs-string">"usage: topnstatjobyarn &lt;inputpath&gt; &lt;day&gt;"</span>)
      System.exit(<span class="hljs-number">1</span>)
    }

    val Array(inputPath, day) = args

    val spark = SparkSession.builder()
      .config(<span class="hljs-string">"spark.sql.sources.partitionColumnTypeInference.enabled"</span>, <span class="hljs-string">"false"</span>)
      .getOrCreate()

    val accessDF = spark.read.format(<span class="hljs-string">"parquet"</span>).load(inputPath)

    StatDAO.deleteData(day)

    // 最受欢迎的topN课程
    videoAccessTopNStat(spark, accessDF, day)

    // 按地市统计topN课程
    cityAccessTopNStat(spark, accessDF, day)

    // 按流量统计topN课程
    videoTrafficsTopNStat(spark, accessDF, day)

    spark.stop()

  }
</code></pre>

<h3 id="3-提交运行">3 提交运行</h3>

<p>1）项目打包</p>

<pre class="prettyprint"><code class=" hljs vbnet">mvn <span class="hljs-keyword">assembly</span>:<span class="hljs-keyword">assembly</span></code></pre>

<p>2）通过 spark-submit方式提交</p>



<pre class="prettyprint"><code class=" hljs haml">export HADOOP_CONF_DIR=/Users/Mac/app/hadoop-2.6.0-cdh5.7.0/etc/hadoop/
<span class="hljs-comment">
// 提交清洗数据作业</span>
spark-submit \
-<span class="ruby">-<span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">log</span>.<span class="hljs-title">SparkStatCleanJobYarn</span> \</span>
</span>-<span class="ruby">-name <span class="hljs-constant">SparkStatCleanJobYarn</span> \
</span>-<span class="ruby">-master yarn \
</span>-<span class="ruby">-executor-memory <span class="hljs-number">1</span>G \
</span>-<span class="ruby">-num-executors <span class="hljs-number">1</span> \
</span>-<span class="ruby">-files /<span class="hljs-constant">Users</span>/<span class="hljs-constant">Mac</span>/pro-use-file/ipDatabase.csv,<span class="hljs-regexp">/Users/</span><span class="hljs-constant">Mac</span>/pro-use-file/ipRegion.xlsx \
</span><span class="hljs-comment">/Users/Mac/my-lib/sql-1.0-jar-with-dependencies.jar \</span>
hdfs://localhost:8020/access.log hdfs://localhost:8020/spark-clean-out/
<span class="hljs-comment">
// 提交统计作业</span>
spark-submit \
-<span class="ruby">-<span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">log</span>.<span class="hljs-title">TopNStatJobYARN</span> \</span>
</span>-<span class="ruby">-name <span class="hljs-constant">TopNStatJobYARN</span> \
</span>-<span class="ruby">-master yarn \
</span>-<span class="ruby">-deploy-mode client \
</span>-<span class="ruby">-executor-memory <span class="hljs-number">1</span>G \
</span>-<span class="ruby">-num-executors <span class="hljs-number">1</span> \
</span><span class="hljs-comment">/Users/Mac/my-lib/sql-1.0-jar-with-dependencies.jar \</span>
hdfs://localhost:8020/spark-clean-out/ 20161110</code></pre>

<p>3）结果</p>

<ul>
<li>清洗数据作业可以在HDFS上的输出目录中找到输出文件。</li>
<li>统计作业可以到数据库中查看输出数据。</li>
</ul>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>