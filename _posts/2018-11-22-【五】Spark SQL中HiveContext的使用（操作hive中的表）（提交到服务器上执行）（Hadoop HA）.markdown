---
layout:     post
title:      【五】Spark SQL中HiveContext的使用（操作hive中的表）（提交到服务器上执行）（Hadoop HA）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：转载注明出处					https://blog.csdn.net/jy02268879/article/details/81193155				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>HiveContext在基本的SQLContext上有了一些新的特性，可以用Hive QL写查询，可以读取Hive表中的数据，支持Hive的UDF。</p>

<p>要<strong><span style="color:#f33b45;">把hive/conf/hive-site.xml文件拷贝到spark/conf下</span></strong>。</p>

<p>cd /app/hive/conf</p>

<p>scp hive-site.xml root@node1:/app/spark/spark-2.2.0-bin-2.9.0/conf/</p>

<p>scp hive-site.xml root@node2:/app/spark/spark-2.2.0-bin-2.9.0/conf/</p>

<p>scp hive-site.xml root@node3:/app/spark/spark-2.2.0-bin-2.9.0/conf/</p>

<p>scp hive-site.xml root@node4:/app/spark/spark-2.2.0-bin-2.9.0/conf/</p>

<p>准备hive的emp表数据</p>

<p>cd /app/hive/testData</p>

<p>vi emploaddata.txt</p>

<pre class="has">
<code>1    sid    12    cq
2    zhangsan    13    bj
3    lisi    14    sh</code></pre>

<p>启动zookeeper</p>

<p>启动hadoop</p>

<p>启动hive</p>

<p>hive创建表emp</p>

<p>cd /app/hive/bin</p>

<p>hive</p>

<pre class="has">
<code class="language-sql">CREATE TABLE IF NOT EXISTS emp (
id int, 
name String,
salary String,
destination String)
COMMENT 'Employee details'
ROW FORMAT DELIMITED
FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE;</code></pre>

<p>给emp表添加数据</p>

<pre class="has">
<code>LOAD DATA LOCAL inpath '/app/hive/testData/emploaddata.txt' OVERWRITE INTO TABLE emp;  </code></pre>

<p> </p>

<p><img alt="" class="has" height="187" src="https://img-blog.csdn.net/20180724233053127?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p5MDIyNjg4Nzk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="662"></p>

<p>项目目录</p>

<p><img alt="" class="has" height="391" src="https://img-blog.csdn.net/20180724220141969?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p5MDIyNjg4Nzk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="329"></p>

<p>pom.xml</p>

<pre class="has">
<code class="language-html">&lt;project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd"&gt;
  &lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;
  &lt;groupId&gt;com.sid.com&lt;/groupId&gt;
  &lt;artifactId&gt;sparksqltrain&lt;/artifactId&gt;
  &lt;version&gt;1.0-SNAPSHOT&lt;/version&gt;
  &lt;inceptionYear&gt;2008&lt;/inceptionYear&gt;
  &lt;properties&gt;
    &lt;scala.version&gt;2.11.8&lt;/scala.version&gt;
    &lt;spark.version&gt;2.2.0&lt;/spark.version&gt;
  &lt;/properties&gt;

  &lt;repositories&gt;
    &lt;repository&gt;
      &lt;id&gt;scala-tools.org&lt;/id&gt;
      &lt;name&gt;Scala-Tools Maven2 Repository&lt;/name&gt;
      &lt;url&gt;http://scala-tools.org/repo-releases&lt;/url&gt;
    &lt;/repository&gt;
  &lt;/repositories&gt;

  &lt;pluginRepositories&gt;
    &lt;pluginRepository&gt;
      &lt;id&gt;scala-tools.org&lt;/id&gt;
      &lt;name&gt;Scala-Tools Maven2 Repository&lt;/name&gt;
      &lt;url&gt;http://scala-tools.org/repo-releases&lt;/url&gt;
    &lt;/pluginRepository&gt;
  &lt;/pluginRepositories&gt;

  &lt;dependencies&gt;
    &lt;!-- scala依赖 --&gt;
    &lt;dependency&gt;
      &lt;groupId&gt;org.scala-lang&lt;/groupId&gt;
      &lt;artifactId&gt;scala-library&lt;/artifactId&gt;
      &lt;version&gt;${scala.version}&lt;/version&gt;
    &lt;/dependency&gt;
    &lt;!-- spark依赖 --&gt;
    &lt;dependency&gt;
      &lt;groupId&gt;org.apache.spark&lt;/groupId&gt;
      &lt;artifactId&gt;spark-sql_2.11&lt;/artifactId&gt;
      &lt;version&gt;${spark.version}&lt;/version&gt;
    &lt;/dependency&gt;
    &lt;!-- hivecontext要用这个依赖--&gt;
    &lt;dependency&gt;
      &lt;groupId&gt;org.apache.spark&lt;/groupId&gt;
      &lt;artifactId&gt;spark-hive_2.11&lt;/artifactId&gt;
      &lt;version&gt;${spark.version}&lt;/version&gt;
    &lt;/dependency&gt;


  &lt;/dependencies&gt;

  &lt;build&gt;
    &lt;sourceDirectory&gt;src/main/scala&lt;/sourceDirectory&gt;
    &lt;testSourceDirectory&gt;src/test/scala&lt;/testSourceDirectory&gt;
    &lt;plugins&gt;
      &lt;plugin&gt;
        &lt;groupId&gt;org.scala-tools&lt;/groupId&gt;
        &lt;artifactId&gt;maven-scala-plugin&lt;/artifactId&gt;
        &lt;executions&gt;
          &lt;execution&gt;
            &lt;goals&gt;
              &lt;goal&gt;compile&lt;/goal&gt;
              &lt;goal&gt;testCompile&lt;/goal&gt;
            &lt;/goals&gt;
          &lt;/execution&gt;
        &lt;/executions&gt;
        &lt;configuration&gt;
          &lt;scalaVersion&gt;${scala.version}&lt;/scalaVersion&gt;
          &lt;args&gt;
            &lt;arg&gt;-target:jvm-1.5&lt;/arg&gt;
          &lt;/args&gt;
        &lt;/configuration&gt;
      &lt;/plugin&gt;
      &lt;plugin&gt;
        &lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;
        &lt;artifactId&gt;maven-eclipse-plugin&lt;/artifactId&gt;
        &lt;configuration&gt;
          &lt;downloadSources&gt;true&lt;/downloadSources&gt;
          &lt;buildcommands&gt;
            &lt;buildcommand&gt;ch.epfl.lamp.sdt.core.scalabuilder&lt;/buildcommand&gt;
          &lt;/buildcommands&gt;
          &lt;additionalProjectnatures&gt;
            &lt;projectnature&gt;ch.epfl.lamp.sdt.core.scalanature&lt;/projectnature&gt;
          &lt;/additionalProjectnatures&gt;
          &lt;classpathContainers&gt;
            &lt;classpathContainer&gt;org.eclipse.jdt.launching.JRE_CONTAINER&lt;/classpathContainer&gt;
            &lt;classpathContainer&gt;ch.epfl.lamp.sdt.launching.SCALA_CONTAINER&lt;/classpathContainer&gt;
          &lt;/classpathContainers&gt;
        &lt;/configuration&gt;
      &lt;/plugin&gt;
    &lt;/plugins&gt;
  &lt;/build&gt;
  &lt;reporting&gt;
    &lt;plugins&gt;
      &lt;plugin&gt;
        &lt;groupId&gt;org.scala-tools&lt;/groupId&gt;
        &lt;artifactId&gt;maven-scala-plugin&lt;/artifactId&gt;
        &lt;configuration&gt;
          &lt;scalaVersion&gt;${scala.version}&lt;/scalaVersion&gt;
        &lt;/configuration&gt;
      &lt;/plugin&gt;
    &lt;/plugins&gt;
  &lt;/reporting&gt;
&lt;/project&gt;
</code></pre>

<pre class="has">
<code class="language-html">HiveContext.scala</code></pre>

<pre class="has">
<code class="language-java">package com.sid.com

import org.apache.spark.{SparkConf, SparkContext}
import org.apache.spark.sql.hive.HiveContext

object HiveContext {
  def main(args: Array[String]): Unit = {
    //创建相应的Context
    val sparkConf = new SparkConf()
    sparkConf//.setAppName("SQLContext").setMaster("local[3]")
    val sc = new SparkContext(sparkConf)
    //这个过时了，在spark1.X中这样用，2.X已经不用这个了
    val hiveContext = new HiveContext(sc);

    hiveContext.table("emp").show()


    sc.stop()
  }
}
</code></pre>

<p>打包</p>

<p><img alt="" class="has" height="449" src="https://img-blog.csdn.net/20180724220130484?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p5MDIyNjg4Nzk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="460"></p>

<p><img alt="" class="has" height="300" src="https://img-blog.csdn.net/20180724220445475?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p5MDIyNjg4Nzk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="739"></p>

<p>传到服务器上运行</p>

<p>cd /app/spark/spark-2.2.0-bin-2.9.0/bin</p>

<p>./spark-submit --class com.sid.com.HiveContext --master local[2] --name HiveContext --jars /app/mysql-connector-java-5.1.46.jar /app/spark/test_data/sparksqltrain-1.0-SNAPSHOT.jar</p>

<p>报错</p>

<pre class="has">
<code>Exception in thread "main" java.lang.IllegalArgumentException: java.net.UnknownHostException: hadoopcluster
	at org.apache.hadoop.security.SecurityUtil.buildTokenService(SecurityUtil.java:374)
	at org.apache.hadoop.hdfs.NameNodeProxies.createNonHAProxy(NameNodeProxies.java:310)
	at org.apache.hadoop.hdfs.NameNodeProxies.createProxy(NameNodeProxies.java:176)
	at org.apache.hadoop.hdfs.DFSClient.&lt;init&gt;(DFSClient.java:668)
	at org.apache.hadoop.hdfs.DFSClient.&lt;init&gt;(DFSClient.java:604)
	at org.apache.hadoop.hdfs.DistributedFileSystem.initialize(DistributedFileSystem.java:148)
	at org.apache.hadoop.fs.FileSystem.createFileSystem(FileSystem.java:2598)
	at org.apache.hadoop.fs.FileSystem.access$200(FileSystem.java:91)
	at org.apache.hadoop.fs.FileSystem$Cache.getInternal(FileSystem.java:2632)
	at org.apache.hadoop.fs.FileSystem$Cache.get(FileSystem.java:2614)
	at org.apache.hadoop.fs.FileSystem.get(FileSystem.java:370)
	at org.apache.hadoop.fs.Path.getFileSystem(Path.java:296)
	at org.apache.hadoop.mapred.FileInputFormat.singleThreadedListStatus(FileInputFormat.java:256)
	at org.apache.hadoop.mapred.FileInputFormat.listStatus(FileInputFormat.java:228)
	at org.apache.hadoop.mapred.FileInputFormat.getSplits(FileInputFormat.java:313)
	at org.apache.spark.rdd.HadoopRDD.getPartitions(HadoopRDD.scala:194)
	at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:252)
	at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:250)
	at scala.Option.getOrElse(Option.scala:121)
	at org.apache.spark.rdd.RDD.partitions(RDD.scala:250)
	at org.apache.spark.rdd.MapPartitionsRDD.getPartitions(MapPartitionsRDD.scala:35)
	at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:252)
	at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:250)
	at scala.Option.getOrElse(Option.scala:121)
	at org.apache.spark.rdd.RDD.partitions(RDD.scala:250)
	at org.apache.spark.rdd.MapPartitionsRDD.getPartitions(MapPartitionsRDD.scala:35)
	at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:252)
	at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:250)
	at scala.Option.getOrElse(Option.scala:121)
	at org.apache.spark.rdd.RDD.partitions(RDD.scala:250)
	at org.apache.spark.rdd.MapPartitionsRDD.getPartitions(MapPartitionsRDD.scala:35)
	at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:252)
	at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:250)
	at scala.Option.getOrElse(Option.scala:121)
	at org.apache.spark.rdd.RDD.partitions(RDD.scala:250)
	at org.apache.spark.rdd.MapPartitionsRDD.getPartitions(MapPartitionsRDD.scala:35)
	at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:252)
	at org.apache.spark.rdd.RDD$$anonfun$partitions$2.apply(RDD.scala:250)
	at scala.Option.getOrElse(Option.scala:121)
	at org.apache.spark.rdd.RDD.partitions(RDD.scala:250)
	at org.apache.spark.sql.execution.SparkPlan.executeTake(SparkPlan.scala:314)
	at org.apache.spark.sql.execution.CollectLimitExec.executeCollect(limit.scala:38)
	at org.apache.spark.sql.Dataset.org$apache$spark$sql$Dataset$$collectFromPlan(Dataset.scala:2853)
	at org.apache.spark.sql.Dataset$$anonfun$head$1.apply(Dataset.scala:2153)
	at org.apache.spark.sql.Dataset$$anonfun$head$1.apply(Dataset.scala:2153)
	at org.apache.spark.sql.Dataset$$anonfun$55.apply(Dataset.scala:2837)
	at org.apache.spark.sql.execution.SQLExecution$.withNewExecutionId(SQLExecution.scala:65)
	at org.apache.spark.sql.Dataset.withAction(Dataset.scala:2836)
	at org.apache.spark.sql.Dataset.head(Dataset.scala:2153)
	at org.apache.spark.sql.Dataset.take(Dataset.scala:2366)
	at org.apache.spark.sql.Dataset.showString(Dataset.scala:245)
	at org.apache.spark.sql.Dataset.show(Dataset.scala:644)
	at org.apache.spark.sql.Dataset.show(Dataset.scala:603)
	at org.apache.spark.sql.Dataset.show(Dataset.scala:612)
	at com.sid.com.HiveContext$.main(HiveContext.scala:15)
	at com.sid.com.HiveContext.main(HiveContext.scala)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at org.apache.spark.deploy.SparkSubmit$.org$apache$spark$deploy$SparkSubmit$$runMain(SparkSubmit.scala:755)
	at org.apache.spark.deploy.SparkSubmit$.doRunMain$1(SparkSubmit.scala:180)
	at org.apache.spark.deploy.SparkSubmit$.submit(SparkSubmit.scala:205)
	at org.apache.spark.deploy.SparkSubmit$.main(SparkSubmit.scala:119)
	at org.apache.spark.deploy.SparkSubmit.main(SparkSubmit.scala)
Caused by: java.net.UnknownHostException: hadoopcluster
	... 65 more
</code></pre>

<p>因为我的hadoop的HDFS配置了HA高可用，hadoopcluster是hadoop配置文件hdfs-site.xml中dfs.nameservices的值。</p>

<p><strong><span style="color:#f33b45;">需要把hadoop的配置文件hdfs-site.xml core-site.xml也拷贝到每个spark的conf下</span></strong></p>

<p>cd /app/hadoop/hadoop-2.9.0/etc/hadoop</p>

<p>scp hdfs-site.xml root@node1:/app/spark/spark-2.2.0-bin-2.9.0/conf/</p>

<p>scp hdfs-site.xml root@node2:/app/spark/spark-2.2.0-bin-2.9.0/conf/</p>

<p>scp hdfs-site.xml root@node3:/app/spark/spark-2.2.0-bin-2.9.0/conf/</p>

<p>scp hdfs-site.xml root@node4:/app/spark/spark-2.2.0-bin-2.9.0/conf/</p>

<p>scp core-site.xml root@node1:/app/spark/spark-2.2.0-bin-2.9.0/conf/</p>

<p>scp core-site.xml root@node2:/app/spark/spark-2.2.0-bin-2.9.0/conf/</p>

<p>scp core-site.xml root@node3:/app/spark/spark-2.2.0-bin-2.9.0/conf/</p>

<p>scp core-site.xml root@node4:/app/spark/spark-2.2.0-bin-2.9.0/conf/</p>

<p>cd /app/spark/spark-2.2.0-bin-2.9.0/conf</p>

<p>cp spark-defaults.conf.template spark-defaults.conf</p>

<p>vi spark-defaults.conf</p>

<p>追加一下内容</p>

<pre class="has">
<code>spark.files file:///app/spark/spark-2.2.0-bin-2.9.0/conf/hdfs-site.xml,file:///app/spark/spark-2.2.0-bin-2.9.0/conf/core-site.xml
</code></pre>

<p>重新执行spark作业</p>

<p><img alt="" class="has" height="143" src="https://img-blog.csdn.net/20180725002938508?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p5MDIyNjg4Nzk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="696"></p>            </div>
                </div>