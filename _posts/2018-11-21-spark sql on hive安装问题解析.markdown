---
layout:     post
title:      spark sql on hive安装问题解析
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span></span><span style="font-size:14px;">安装spark时，默认的spark assembly 不包含hive支持。spark官网上说明“<span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:22.399999618530273px;">Spark SQL also
 supports reading and writing data stored in </span><a href="http://hive.apache.org/" rel="nofollow" style="color:rgb(0,136,204);text-decoration:none;font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:22.399999618530273px;">Apache
 Hive</a><span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:22.399999618530273px;">. However, since Hive has a large number of dependencies, it is not included in the default Spark assembly.</span>” ，要想spark
 sql在hive上运行，需要编辑与自己使用spark版本相同的源码，将依赖包重新打入assembly中，编译后将所需要的包加入到之前spark安装位置。</span></p>
<p><span style="font-size:18px;">1、首先重新编译与使用版本一样的spark源码</span></p>
<p><span style="font-size:14px;">本文hadoop版本为2.3.0-cdh5.1.2，spark版本为1.0.2。</span></p>
<p><span style="font-family:Arial;line-height:26px;"><span style="font-size:14px;">本文是用sbt工具进行编译，也可使用maven编译。</span></span></p>
<p><span style="font-family:Arial;font-size:14px;"><span style="line-height:26px;">编译过程如下：</span></span></p>
<p><span style="font-size:14px;"><span style="font-family:Arial;line-height:26px;">修改spark1.0.2/project/SparkBuild.scala文件，如下：</span><br></span></p>
<p><span style="font-family:Arial;line-height:26px;"><span style="font-size:14px;"></span></span></p><pre><code class="language-java">val DEFAULT_HADOOP_VERSION = "2.3.0-cdh5.1.2"
val DEFAULT_YARN = true
val DEFAULT_HIVE = true</code></pre>
<p>执行命令：sbt/bin/sbt spark1.0.2/assembly</p>
<p>等待编译，时间较长</p>
<p>编译结束后，查看spark-1.0.2/assembly/target/scala-2.10目录下，有新生成的jar包，本文生成的jar包为spark-assembly-1.0.2-hadoop2.3.0-cdh5.1.2.jar</p>
此外，源码中<span style="font-family:Arial;line-height:26px;">spark-1.0.2/lib_managed/jars目录下也含有依赖包。</span>
<p><span style="font-family:Arial;line-height:26px;"><span style="font-size:18px;">2、配置sqark sql on hive依赖包</span></span></p>
<p><span style="font-family:Arial;line-height:26px;"><span style="font-size:14px;">首先执行spark-shell，查看下缺什么包。</span></span></p>
<p><span style="font-size:14px;"><span style="font-family:Arial;line-height:26px;"></span></span></p>
<pre><code class="language-java">&lt;span style="font-size:14px;"&gt;./spark-shell \
  --master yarn-client \
  --driver-class-path $(echo /opt/cloudera/parcels/CDH/lib/hadoop-yarn/*.jar |sed 's/ /:/g'):/opt/cloudera/parcels/CDH-5.1.2-1.cdh5.1.2.p0.3/lib/hadoop-hdfs/hadoop-hdfs-2.3.0-cdh5.1.2.jar&lt;/span&gt;</code></pre><span style="font-size:14px;"><br>
然后执行hql语句</span>
<p></p>
<p><span style="font-size:14px;"><span style="font-family:Arial;line-height:26px;"></span></span></p>
<pre><code class="language-java">&lt;span style="font-size:14px;"&gt;val sqlContext = new org.apache.spark.sql.hive.HiveContext(sc)
&lt;/span&gt;</code></pre><span style="font-size:14px;"><br>
结果出现如下错误</span>
<p></p><pre><code class="language-java">&lt;span style="font-size:14px;"&gt;val sqlContext = new org.apache.spark.sql.hive.HiveContext(sc)
&lt;console&gt;:12: error: object hive is not a member of package org.apache.spark.sql
       val sqlContext = new org.apache.spark.sql.hive.HiveContext(sc)&lt;/span&gt;</code></pre><span style="font-size:14px;">从错误中可以看出是缺少org.apache.spark.sql.hive包。</span>
<p><span style="font-size:14px;">此时将编译的<span style="font-family:Arial;line-height:26px;">spark-assembly-1.0.2-hadoop2.3.0-cdh5.1.2.jar包放入spark/assembly/lib目录下。</span></span></p>
<p><span style="font-family:Arial;line-height:26px;"><span style="font-size:14px;">再次运行</span></span></p>
<p><span style="font-family:Arial;line-height:26px;"><span style="font-size:14px;"></span></span></p><pre><code class="language-java">./spark-shell \
  --master yarn-client \
  --driver-class-path $(echo /opt/cloudera/parcels/CDH/lib/hadoop-yarn/*.jar |sed 's/ /:/g'):/opt/cloudera/parcels/CDH-5.1.2-1.cdh5.1.2.p0.3/lib/hadoop-hdfs/hadoop-hdfs-2.3.0-cdh5.1.2.jar</code></pre>之后执行语句
<p><span style="font-family:Arial;line-height:26px;"><span style="font-size:14px;"></span></span></p><pre><code class="language-java">val sqlContext = new org.apache.spark.sql.hive.HiveContext(sc)</code></pre>出现如下错误：
<p><span style="font-family:Arial;line-height:26px;"></span></p><pre><code class="language-java">&lt;span style="font-size:14px;"&gt;ERROR DDLTask: org.apache.hadoop.hive.ql.metadata.HiveException: java.lang.RuntimeException: Unable to instantiate org.apache.hadoop.hive.metastore.HiveMetaStoreClient
        at org.apache.hadoop.hive.ql.metadata.Hive.getAllDatabases(Hive.java:1074)
        at org.apache.hadoop.hive.ql.exec.DDLTask.showDatabases(DDLTask.java:2198)
        at org.apache.hadoop.hive.ql.exec.DDLTask.execute(DDLTask.java:328)
        at org.apache.hadoop.hive.ql.exec.Task.executeTask(Task.java:151)
        at org.apache.hadoop.hive.ql.exec.TaskRunner.runSequential(TaskRunner.java:65)
        at org.apache.hadoop.hive.ql.Driver.launchTask(Driver.java:1414)
        at org.apache.hadoop.hive.ql.Driver.execute(Driver.java:1192)
        at org.apache.hadoop.hive.ql.Driver.runInternal(Driver.java:1020)
        at org.apache.hadoop.hive.ql.Driver.run(Driver.java:888)
        at org.apache.spark.sql.hive.HiveContext.runHive(HiveContext.scala:189)
        at org.apache.spark.sql.hive.HiveContext.runSqlHive(HiveContext.scala:163)
        at org.apache.spark.sql.hive.execution.NativeCommand.sideEffectResult$lzycompute(NativeCommand.scala:35)
        at org.apache.spark.sql.hive.execution.NativeCommand.sideEffectResult(NativeCommand.scala:35)
        at org.apache.spark.sql.hive.execution.NativeCommand.execute(NativeCommand.scala:38)
        at org.apache.spark.sql.hive.HiveContext$QueryExecution.toRdd$lzycompute(HiveContext.scala:250)
        at org.apache.spark.sql.hive.HiveContext$QueryExecution.toRdd(HiveContext.scala:250)
        at org.apache.spark.sql.SchemaRDDLike$class.$init$(SchemaRDDLike.scala:58)
        at org.apache.spark.sql.SchemaRDD.&lt;init&gt;(SchemaRDD.scala:104)
        at org.apache.spark.sql.hive.HiveContext.hiveql(HiveContext.scala:75)
        at org.apache.spark.sql.hive.HiveContext.hql(HiveContext.scala:78)
        at $line9.$read$$iwC$$iwC$$iwC$$iwC.&lt;init&gt;(&lt;console&gt;:15)
        at $line9.$read$$iwC$$iwC$$iwC.&lt;init&gt;(&lt;console&gt;:20)
        at $line9.$read$$iwC$$iwC.&lt;init&gt;(&lt;console&gt;:22)
        at $line9.$read$$iwC.&lt;init&gt;(&lt;console&gt;:24)
        at $line9.$read.&lt;init&gt;(&lt;console&gt;:26)
        at $line9.$read$.&lt;init&gt;(&lt;console&gt;:30)
        at $line9.$read$.&lt;clinit&gt;(&lt;console&gt;)
        at $line9.$eval$.&lt;init&gt;(&lt;console&gt;:7)
        at $line9.$eval$.&lt;clinit&gt;(&lt;console&gt;)
        at $line9.$eval.$print(&lt;console&gt;)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:39)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:25)
        at java.lang.reflect.Method.invoke(Method.java:597)
        at org.apache.spark.repl.SparkIMain$ReadEvalPrint.call(SparkIMain.scala:788)
        at org.apache.spark.repl.SparkIMain$Request.loadAndRun(SparkIMain.scala:1056)
        at org.apache.spark.repl.SparkIMain.loadAndRunReq$1(SparkIMain.scala:614)
        at org.apache.spark.repl.SparkIMain.interpret(SparkIMain.scala:645)
        at org.apache.spark.repl.SparkIMain.interpret(SparkIMain.scala:609)
        at org.apache.spark.repl.SparkILoop.reallyInterpret$1(SparkILoop.scala:796)
        at org.apache.spark.repl.SparkILoop.interpretStartingWith(SparkILoop.scala:841)
        at org.apache.spark.repl.SparkILoop.command(SparkILoop.scala:753)
        at org.apache.spark.repl.SparkILoop.processLine$1(SparkILoop.scala:601)
        at org.apache.spark.repl.SparkILoop.innerLoop$1(SparkILoop.scala:608)
        at org.apache.spark.repl.SparkILoop.loop(SparkILoop.scala:611)
        at org.apache.spark.repl.SparkILoop$$anonfun$process$1.apply$mcZ$sp(SparkILoop.scala:936)
        at org.apache.spark.repl.SparkILoop$$anonfun$process$1.apply(SparkILoop.scala:884)
        at org.apache.spark.repl.SparkILoop$$anonfun$process$1.apply(SparkILoop.scala:884)
        at scala.tools.nsc.util.ScalaClassLoader$.savingContextLoader(ScalaClassLoader.scala:135)
        at org.apache.spark.repl.SparkILoop.process(SparkILoop.scala:884)
        at org.apache.spark.repl.SparkILoop.process(SparkILoop.scala:982)
        at org.apache.spark.repl.Main$.main(Main.scala:31)
        at org.apache.spark.repl.Main.main(Main.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:39)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:25)
        at java.lang.reflect.Method.invoke(Method.java:597)
        at org.apache.spark.deploy.SparkSubmit$.launch(SparkSubmit.scala:303)
        at org.apache.spark.deploy.SparkSubmit$.main(SparkSubmit.scala:55)
        at org.apache.spark.deploy.SparkSubmit.main(SparkSubmit.scala)
Caused by: java.lang.RuntimeException: Unable to instantiate org.apache.hadoop.hive.metastore.HiveMetaStoreClient
        at org.apache.hadoop.hive.metastore.MetaStoreUtils.newInstance(MetaStoreUtils.java:1212)
        at org.apache.hadoop.hive.metastore.RetryingMetaStoreClient.&lt;init&gt;(RetryingMetaStoreClient.java:62)
        at org.apache.hadoop.hive.metastore.RetryingMetaStoreClient.getProxy(RetryingMetaStoreClient.java:72)
        at org.apache.hadoop.hive.ql.metadata.Hive.createMetaStoreClient(Hive.java:2372)
        at org.apache.hadoop.hive.ql.metadata.Hive.getMSC(Hive.java:2383)
        at org.apache.hadoop.hive.ql.metadata.Hive.getAllDatabases(Hive.java:1072)
        ... 59 more
Caused by: java.lang.reflect.InvocationTargetException
        at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
        at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:39)
        at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:27)
        at java.lang.reflect.Constructor.newInstance(Constructor.java:513)
        at org.apache.hadoop.hive.metastore.MetaStoreUtils.newInstance(MetaStoreUtils.java:1210)
        ... 64 more
Caused by: javax.jdo.JDOFatalUserException: Class org.datanucleus.api.jdo.JDOPersistenceManagerFactory was not found.
NestedThrowables:
java.lang.ClassNotFoundException: org.datanucleus.api.jdo.JDOPersistenceManagerFactory
        at javax.jdo.JDOHelper.invokeGetPersistenceManagerFactoryOnImplementation(JDOHelper.java:1175)
        at javax.jdo.JDOHelper.getPersistenceManagerFactory(JDOHelper.java:808)
        at javax.jdo.JDOHelper.getPersistenceManagerFactory(JDOHelper.java:701)
        at org.apache.hadoop.hive.metastore.ObjectStore.getPMF(ObjectStore.java:275)
        at org.apache.hadoop.hive.metastore.ObjectStore.getPersistenceManager(ObjectStore.java:304)
        at org.apache.hadoop.hive.metastore.ObjectStore.initialize(ObjectStore.java:234)
        at org.apache.hadoop.hive.metastore.ObjectStore.setConf(ObjectStore.java:209)
        at org.apache.hadoop.util.ReflectionUtils.setConf(ReflectionUtils.java:73)
        at org.apache.hadoop.util.ReflectionUtils.newInstance(ReflectionUtils.java:133)
        at org.apache.hadoop.hive.metastore.RetryingRawStore.&lt;init&gt;(RetryingRawStore.java:64)
        at org.apache.hadoop.hive.metastore.RetryingRawStore.getProxy(RetryingRawStore.java:73)
        at org.apache.hadoop.hive.metastore.HiveMetaStore$HMSHandler.newRawStore(HiveMetaStore.java:415)
        at org.apache.hadoop.hive.metastore.HiveMetaStore$HMSHandler.getMS(HiveMetaStore.java:402)
        at org.apache.hadoop.hive.metastore.HiveMetaStore$HMSHandler.createDefaultDB(HiveMetaStore.java:441)
        at org.apache.hadoop.hive.metastore.HiveMetaStore$HMSHandler.init(HiveMetaStore.java:326)
        at org.apache.hadoop.hive.metastore.HiveMetaStore$HMSHandler.&lt;init&gt;(HiveMetaStore.java:286)
        at org.apache.hadoop.hive.metastore.RetryingHMSHandler.&lt;init&gt;(RetryingHMSHandler.java:54)
        at org.apache.hadoop.hive.metastore.RetryingHMSHandler.getProxy(RetryingHMSHandler.java:59)
        at org.apache.hadoop.hive.metastore.HiveMetaStore.newHMSHandler(HiveMetaStore.java:4060)
        at org.apache.hadoop.hive.metastore.HiveMetaStoreClient.&lt;init&gt;(HiveMetaStoreClient.java:121)
        ... 69 more
Caused by: java.lang.ClassNotFoundException: org.datanucleus.api.jdo.JDOPersistenceManagerFactory
        at scala.tools.nsc.interpreter.AbstractFileClassLoader.findClass(AbstractFileClassLoader.scala:83)
        at java.lang.ClassLoader.loadClass(ClassLoader.java:306)
        at java.lang.ClassLoader.loadClass(ClassLoader.java:247)
        at java.lang.Class.forName0(Native Method)
        at java.lang.Class.forName(Class.java:247)
        at javax.jdo.JDOHelper$18.run(JDOHelper.java:2018)
        at javax.jdo.JDOHelper$18.run(JDOHelper.java:2016)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.jdo.JDOHelper.forName(JDOHelper.java:2015)
        at javax.jdo.JDOHelper.invokeGetPersistenceManagerFactoryOnImplementation(JDOHelper.java:1162)&lt;/span&gt;</code></pre>
<p><span style="font-family:Arial;line-height:26px;"><span style="font-size:14px;">从以上错误可以看出缺少datanucleus类jar包，将<span style="font-family:Arial;line-height:26px;">源码中</span><span style="font-family:Arial;line-height:26px;">spark-1.0.2/lib_managed/jars</span>目录下的datanucleus-api-jdo-3.2.1.jar，datanucleus-core-3.2.2.jar，datanucleus-rdbms-3.2.1.jar在执行时使用--jars参数导入。</span></span></p>
<p><span style="font-family:Arial;line-height:26px;"><span style="font-size:14px;">运行</span></span></p>
<p><span style="font-family:Arial;line-height:26px;"></span></p><pre><code class="language-java">&lt;span style="font-size:14px;"&gt;./spark-shell \
  --master yarn-client \
  --driver-class-path $(echo /opt/cloudera/parcels/CDH/lib/hadoop-yarn/*.jar |sed 's/ /:/g'):/opt/cloudera/parcels/CDH-5.1.2-1.cdh5.1.2.p0.3/lib/hadoop-hdfs/hadoop-hdfs-2.3.0-cdh5.1.2.jar \
  --jars /opt/cloudera/parcels/CDH/lib/spark/libs/datanucleus-api-jdo-3.2.1.jar,/opt/cloudera/parcels/CDH/lib/spark/libs/datanucleus-core-3.2.2.jar,/opt/cloudera/parcels/CDH/lib/spark/libs/datanucleus-rdbms-3.2.1.jar &lt;/span&gt;</code></pre>
<p></p>
<p><span style="font-family:Arial;line-height:26px;"><span style="font-size:14px;">之后执行语句</span></span></p>
<p></p><pre><code class="language-java">&lt;span style="font-size:14px;"&gt;val sqlContext = new org.apache.spark.sql.hive.HiveContext(sc)
sqlContext.hql("show databases").collect().foreach(println)&lt;/span&gt;</code></pre><span style="font-size:14px;"><br>
结果显示没有结果，不正确，没有读取到hive-site.xml</span>
<p><span style="font-size:14px;">将hive-site.xml文件放入spark/conf目录下，重新运行</span></p>
<p></p><pre><code class="language-java">&lt;span style="font-size:14px;"&gt;./spark-shell \
  --master yarn-client \
  --driver-class-path $(echo /opt/cloudera/parcels/CDH/lib/hadoop-yarn/*.jar |sed 's/ /:/g'):/opt/cloudera/parcels/CDH-5.1.2-1.cdh5.1.2.p0.3/lib/hadoop-hdfs/hadoop-hdfs-2.3.0-cdh5.1.2.jar \
  --jars /opt/cloudera/parcels/CDH/lib/spark/libs/datanucleus-api-jdo-3.2.1.jar,/opt/cloudera/parcels/CDH/lib/spark/libs/datanucleus-core-3.2.2.jar,/opt/cloudera/parcels/CDH/lib/spark/libs/datanucleus-rdbms-3.2.1.jar &lt;/span&gt;</code></pre>
<p><span style="font-family:Arial;line-height:26px;"><span style="font-size:14px;">之后执行语句</span></span></p>
<p></p>
<pre><code class="language-java">&lt;span style="font-size:14px;"&gt;val sqlContext = new org.apache.spark.sql.hive.HiveContext(sc)
sqlContext.hql("show databases").collect().foreach(println)&lt;/span&gt;</code></pre>
<p></p>
<span style="font-size:14px;">结果显示正常，配置结束。</span>
            </div>
                </div>