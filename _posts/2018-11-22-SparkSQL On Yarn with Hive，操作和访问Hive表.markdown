---
layout:     post
title:      SparkSQL On Yarn with Hive，操作和访问Hive表
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>转载自：<a href="http://lxw1234.com/archives/2015/08/466.htm" rel="nofollow">http://lxw1234.com/archives/2015/08/466.htm</a> <br>
本文将介绍以yarn-cluster模式运行SparkSQL应用程序，访问和操作Hive中的表，这个和在Yarn上运行普通的Spark应用程序有所不同，重点是需要将Hive的依赖包以及配置文件传递到Driver和Executor上，因为在yarn-cluster模式下，Driver和Executor都是由Yarn和分配的。 <br>
下面的代码完成了以下功能： <br>
1. 在Hive的数据库liuxiaowen中，创建目标表lxw1234； <br>
2. 从已存在的源表lxw_cate_id插入数据到目标表lxw1234； <br>
3. 统计目标表lxw1234的记录数； <br>
4. 统计源表lxw_cate_id的记录数； <br>
5. 打印目标表lxw1234的limit 5记录；</p>

<pre class="prettyprint"><code class=" hljs scala"><span class="hljs-keyword">package</span> com.lxw1234.sparksql;


<span class="hljs-keyword">import</span> java.io.File
<span class="hljs-keyword">import</span> org.apache.spark.{SparkConf, SparkContext}
<span class="hljs-keyword">import</span> org.apache.spark.sql._
<span class="hljs-keyword">import</span> org.apache.spark.sql.hive.HiveContext

<span class="hljs-javadoc">/**
 * lxw的大数据田地 -- lxw1234.com
 */</span>
<span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">SparkSQLHiveOnYarn</span> {</span>

  <span class="hljs-keyword">def</span> main(args: Array[String]) {
    <span class="hljs-keyword">val</span> sparkConf = <span class="hljs-keyword">new</span> SparkConf().setAppName(<span class="hljs-string">"SparkSQLHiveOnYarn"</span>)
    <span class="hljs-keyword">val</span> sc = <span class="hljs-keyword">new</span> SparkContext(sparkConf)

    <span class="hljs-keyword">val</span> hiveContext = <span class="hljs-keyword">new</span> HiveContext(sc)
    <span class="hljs-keyword">import</span> hiveContext.implicits._
    <span class="hljs-keyword">import</span> hiveContext.sql

    <span class="hljs-comment">//在数据库liuxiaowen中创建表 lxw1234</span>
    println(<span class="hljs-string">"create table lxw1234 .. "</span>)
    sql(<span class="hljs-string">"USE liuxiaowen"</span>)
    sql(<span class="hljs-string">"CREATE TABLE IF NOT EXISTS lxw1234 (cate STRING, cate_id INT) STORED AS TEXTFILE"</span>)

    <span class="hljs-comment">//从已存在的源表lxw_cate_id插入数据到目标表lxw1234</span>
    println(<span class="hljs-string">"insert data into table lxw1234 .. "</span>)
    sql(<span class="hljs-string">"INSERT OVERWRITE TABLE lxw1234 select cate,cate_id FROM lxw_cate_id"</span>)

    <span class="hljs-comment">//目标表lxw1234的记录数</span>
    println(<span class="hljs-string">"Result of 'select count(1) from lxw1234': "</span>)
    <span class="hljs-keyword">val</span> count = sql(<span class="hljs-string">"SELECT COUNT(1) FROM lxw1234"</span>).collect().head.getLong(<span class="hljs-number">0</span>)
    println(s<span class="hljs-string">"lxw1234 COUNT(1): $count"</span>)

    <span class="hljs-comment">//源表lxw_cate_id的记录数</span>
    println(<span class="hljs-string">"Result of 'select count(1) from lxw_cate_id': "</span>)
    <span class="hljs-keyword">val</span> count2 = sql(<span class="hljs-string">"SELECT COUNT(1) FROM lxw_cate_id"</span>).collect().head.getLong(<span class="hljs-number">0</span>)
    println(s<span class="hljs-string">"lxw_cate_id COUNT(1): $count2"</span>)

    <span class="hljs-comment">//目标表lxw1234的limit 5记录</span>
    println(<span class="hljs-string">"Result of 'SELECT * from lxw1234 limit 10': "</span>)
    sql(<span class="hljs-string">"SELECT * FROM lxw1234 limit 5"</span>).collect().foreach(println)

    <span class="hljs-comment">//sleep 10分钟，为了从WEB界面上看日志</span>
    Thread.sleep(<span class="hljs-number">600000</span>)
    sc.stop()

  }
}
</code></pre>

<p>将上面的程序打包成sparksql.jar，并上传至Spark和Hadoop所在的客户端机器上。 <br>
运行下面的命令，使用spark-submit将该SparkSQL应用程序提交到Yarn上：</p>



<pre class="prettyprint"><code class=" hljs haml">cd $SPARK_HOME/bin
./spark-submit \
-<span class="ruby">-<span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">com</span>.<span class="hljs-title">lxw1234</span>.<span class="hljs-title">sparksql</span>.<span class="hljs-title">SparkSQLHiveOnYarn</span> \</span>
</span>-<span class="ruby">-master yarn-cluster \
</span>-<span class="ruby">-driver-memory <span class="hljs-number">4</span>G \
</span>-<span class="ruby">-driver-java-options <span class="hljs-string">"-XX:MaxPermSize=1G"</span> \
</span>-<span class="ruby">-verbose \
</span>-<span class="ruby">-files <span class="hljs-variable">$HIVE_HOME</span>/conf/hive-site.xml \
</span>-<span class="ruby">-jars <span class="hljs-variable">$HIVE_HOME</span>/lib/mysql-connector-java-<span class="hljs-number">5.1</span>.<span class="hljs-number">15</span>-bin.jar,<span class="hljs-variable">$SPARK_HOME</span>/lib/datanucleus-api-jdo-<span class="hljs-number">3.2</span>.<span class="hljs-number">6</span>.jar,<span class="hljs-variable">$SPARK_HOME</span>/lib/datanucleus-core-<span class="hljs-number">3.2</span>.<span class="hljs-number">10</span>.jar,<span class="hljs-variable">$SPARK_HOME</span>/lib/datanucleus-rdbms-<span class="hljs-number">3.2</span>.<span class="hljs-number">9</span>.jar,<span class="hljs-variable">$SPARK_HOME</span>/lib/guava-<span class="hljs-number">12.0</span>.<span class="hljs-number">1</span>.jar \
</span><span class="hljs-comment">/tmp/sparksql.jar</span></code></pre>

<p>提交运行之后，在Yarn上可以看到该application: <br>
<img title="" alt="这里写图片描述" src="https://img-blog.csdn.net/20160101210952862"> <br>
点击logs，进入stdout，可以查看程序的标准输出： <br>
<img title="" alt="这里写图片描述" src="https://img-blog.csdn.net/20160101211025275"> <br>
从日志中看到，程序已经成功执行。 <br>
点击ApplicationMaster的WEB URL，进入SparkMaster的WEB界面： <br>
<img title="" alt="这里写图片描述" src="https://img-blog.csdn.net/20160101211102576"> <br>
可以看到，每句SQL是一个Job. <br>
在Hive中查看表lxw1234的数据： <br>
<img title="" alt="这里写图片描述" src="https://img-blog.csdn.net/20160101211116704"> <br>
没问题，和日志中打印出来的一样。</p>

<p>说明一下上面使用spark-submit提交的命令：</p>

<p>–master yarn-cluster  //指定以yarn-cluster模式运行，关于yarn-cluster和yarn-client的区别，在之前的文章中提到过</p>

<p>–driver-memory 4G  //指定Driver使用的内存为4G，</p>

<p>//如果太小的话，会报错：Exception: java.lang.OutOfMemoryError thrown from the UncaughtExceptionHandler in thread “Driver”</p>

<p>–driver-java-options “-XX:MaxPermSize=1G”   //指定Driver程序JVM参数</p>

<p>–files $HIVE_HOME/conf/hive-site.xml    //将Hive的配置文件添加到Driver和Executor的classpath中</p>

<p>–jars $HIVE_HOME/lib/mysql-connector-java-5.1.15-bin.jar,….    //将Hive依赖的jar包添加到Driver和Executor的classpath中</p>

<p>//需要依赖的jar包有：mysql-connector-java-5.1.15-bin.jar、datanucleus-api-jdo-3.2.6.jar、datanucleus-core-3.2.10.jar、datanucleus-rdbms-3.2.9.jar、guava-12.0.1.jar</p>

<p>另外还有一点要注意：由于Driver和Executor需要访问Hive的元数据库，而Driver和Executor被分配到哪台机器上是不固定的，所以需要授权，使集群上所有机器都有操作Hive元数据库的权限。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>