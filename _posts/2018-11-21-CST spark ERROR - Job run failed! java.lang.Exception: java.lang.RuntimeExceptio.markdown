---
layout:     post
title:      CST spark ERROR - Job run failed! java.lang.Exception: java.lang.RuntimeExceptio
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <pre><code class="language-html">11-04-2018 17:22:03 CST spark INFO - Starting job spark at 1523438523394
11-04-2018 17:22:03 CST spark INFO - azkaban.webserver.url property was not set
11-04-2018 17:22:03 CST spark INFO - job JVM args: -Dazkaban.flowid=spark -Dazkaban.execid=110 -Dazkaban.jobid=spark
11-04-2018 17:22:03 CST spark INFO - user.to.proxy property was not set, defaulting to submit user azkaban
11-04-2018 17:22:03 CST spark INFO - Building spark job executor. 
11-04-2018 17:22:03 CST spark INFO - Memory granted for job spark
11-04-2018 17:22:03 CST spark INFO - Not setting up secure proxy info for child process
11-04-2018 17:22:03 CST spark INFO - No classpath specified. Trying to load classes from /root/azkaban/azkaban-exec-server-3.43.0/executions/110
11-04-2018 17:22:03 CST spark INFO - Adding jobtype.classpath: [/opt/cloudera/parcels/CDH/lib/hadoop/etc/hadoop:/opt/cloudera/parcels/CDH/jars/*:/opt/cloudera/parcels/CDH/lib/spark/conf:/opt/cloudera/parcels/CDH/lib/spark/lib/*]
11-04-2018 17:22:03 CST spark INFO - Using system default spark: /opt/cloudera/parcels/CDH/lib/spark and conf: /opt/cloudera/parcels/CDH/lib/spark/conf
11-04-2018 17:22:03 CST spark INFO - Adding jobtype.global.classpath: [/opt/cloudera/parcels/CDH/lib/hadoop/etc/hadoop:/opt/cloudera/parcels/CDH/jars/*]
11-04-2018 17:22:03 CST spark INFO - Final classpath: [azkaban.jar, /root/azkaban/azkaban-exec-server-3.43.0/lib/az-core-3.43.0-8-gf0e72fa.jar, /root/azkaban/azkaban-exec-server-3.43.0/lib/azkaban-common-3.43.0-8-gf0e72fa.jar, /root/azkaban/azkaban-exec-server-3.43.0/extlib/azkaban-jobtype-3.0.0.jar, /root/azkaban/azkaban-exec-server-3.43.0/lib/azkaban-hadoop-security-plugin-3.43.0-8-gf0e72fa.jar, /root/azkaban/azkaban-exec-server-3.43.0/executions/110/_resources_spark, /opt/cloudera/parcels/CDH/lib/hadoop/etc/hadoop:/opt/cloudera/parcels/CDH/jars/*:/opt/cloudera/parcels/CDH/lib/spark/conf:/opt/cloudera/parcels/CDH/lib/spark/lib/*, /opt/cloudera/parcels/CDH/lib/spark/lib/*, /opt/cloudera/parcels/CDH/lib/spark/conf, /opt/cloudera/parcels/CDH/lib/hadoop/etc/hadoop:/opt/cloudera/parcels/CDH/jars/*]
11-04-2018 17:22:03 CST spark INFO - Resolving execution jar/py name: resolvedJarName: /root/azkaban/azkaban-exec-server-3.43.0/executions/110/azkaban.jar
11-04-2018 17:22:03 CST spark INFO - 1 commands to execute.
11-04-2018 17:22:03 CST spark INFO - cwd=/root/azkaban/azkaban-exec-server-3.43.0/executions/110
11-04-2018 17:22:03 CST spark INFO - effective user is: azkaban
11-04-2018 17:22:03 CST spark INFO - Command: java -Dazkaban.flowid=spark -Dazkaban.execid=110 -Dazkaban.jobid=spark -Xms64M -Xmx256M -cp azkaban.jar:/root/azkaban/azkaban-exec-server-3.43.0/lib/az-core-3.43.0-8-gf0e72fa.jar:/root/azkaban/azkaban-exec-server-3.43.0/lib/azkaban-common-3.43.0-8-gf0e72fa.jar:/root/azkaban/azkaban-exec-server-3.43.0/extlib/azkaban-jobtype-3.0.0.jar:/root/azkaban/azkaban-exec-server-3.43.0/lib/azkaban-hadoop-security-plugin-3.43.0-8-gf0e72fa.jar:/root/azkaban/azkaban-exec-server-3.43.0/executions/110/_resources_spark:/opt/cloudera/parcels/CDH/lib/hadoop/etc/hadoop:/opt/cloudera/parcels/CDH/jars/*:/opt/cloudera/parcels/CDH/lib/spark/conf:/opt/cloudera/parcels/CDH/lib/spark/lib/*:/opt/cloudera/parcels/CDH/lib/spark/lib/*:/opt/cloudera/parcels/CDH/lib/spark/conf:/opt/cloudera/parcels/CDH/lib/hadoop/etc/hadoop:/opt/cloudera/parcels/CDH/jars/* azkaban.jobtype.HadoopSecureSparkWrapper --driver-java-options--masterspark://cdh-master:7077--classcom.azkaban.hollysys.sparkTest--queuedefault/root/azkaban/azkaban-exec-server-3.43.0/executions/110/azkaban.jar
11-04-2018 17:22:03 CST spark INFO - Environment variables: {JOB_OUTPUT_PROP_FILE=/root/azkaban/azkaban-exec-server-3.43.0/executions/110/spark_output_8468954967439774988_tmp, JOB_PROP_FILE=/root/azkaban/azkaban-exec-server-3.43.0/executions/110/spark_props_3032483159170958803_tmp, KRB5CCNAME=/tmp/krb5cc__sparkNoHdfs__spark__spark__110__azkaban, JOB_NAME=spark}
11-04-2018 17:22:03 CST spark INFO - Working directory: /root/azkaban/azkaban-exec-server-3.43.0/executions/110
11-04-2018 17:22:04 CST spark INFO - SLF4J: Class path contains multiple SLF4J bindings.
11-04-2018 17:22:04 CST spark INFO - SLF4J: Found binding in [jar:file:/opt/cloudera/parcels/CDH-5.11.0-1.cdh5.11.0.p0.34/jars/slf4j-log4j12-1.7.5.jar!/org/slf4j/impl/StaticLoggerBinder.class]
11-04-2018 17:22:04 CST spark INFO - SLF4J: Found binding in [jar:file:/opt/cloudera/parcels/CDH-5.11.0-1.cdh5.11.0.p0.34/jars/pig-0.12.0-cdh5.11.0.jar!/org/slf4j/impl/StaticLoggerBinder.class]
11-04-2018 17:22:04 CST spark INFO - SLF4J: Found binding in [jar:file:/opt/cloudera/parcels/CDH-5.11.0-1.cdh5.11.0.p0.34/jars/slf4j-simple-1.7.5.jar!/org/slf4j/impl/StaticLoggerBinder.class]
11-04-2018 17:22:04 CST spark INFO - SLF4J: Found binding in [jar:file:/opt/cloudera/parcels/CDH-5.11.0-1.cdh5.11.0.p0.34/jars/avro-tools-1.7.6-cdh5.11.0.jar!/org/slf4j/impl/StaticLoggerBinder.class]
11-04-2018 17:22:04 CST spark INFO - SLF4J: Found binding in [jar:file:/opt/cloudera/parcels/CDH-5.11.0-1.cdh5.11.0.p0.34/lib/spark/lib/slf4j-log4j12-1.7.16.jar!/org/slf4j/impl/StaticLoggerBinder.class]
11-04-2018 17:22:04 CST spark INFO - SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
11-04-2018 17:22:04 CST spark INFO - SLF4J: Actual binding is of type [org.slf4j.impl.Log4jLoggerFactory]
11-04-2018 17:22:04 CST spark INFO - 18/04/11 17:22:04 INFO root: Args before adjusting driver java opts: [--driver-java-options, , --master, spark://cdh-master:7077, --class, com.azkaban.hollysys.sparkTest, --queue, default, /root/azkaban/azkaban-exec-server-3.43.0/executions/110/azkaban.jar]
<span style="color:#ff0000;">11-04-2018 17:22:04 CST spark INFO - Exception in thread "main" java.lang.RuntimeException: Cannot find property [azkaban.link.workflow.url], in Hadoop configuration: [null]</span>
11-04-2018 17:22:04 CST spark INFO - 	at azkaban.jobtype.HadoopJobUtils.javaOptStringFromHadoopConfiguration(HadoopJobUtils.java:586)
11-04-2018 17:22:04 CST spark INFO - 	at azkaban.jobtype.HadoopSecureSparkWrapper.handleDriverJavaOpts(HadoopSecureSparkWrapper.java:168)
11-04-2018 17:22:04 CST spark INFO - 	at azkaban.jobtype.HadoopSecureSparkWrapper.runSpark(HadoopSecureSparkWrapper.java:135)
11-04-2018 17:22:04 CST spark INFO - 	at azkaban.jobtype.HadoopSecureSparkWrapper.main(HadoopSecureSparkWrapper.java:105)
11-04-2018 17:22:04 CST spark INFO - Process completed unsuccessfully in 0 seconds.
11-04-2018 17:22:04 CST spark ERROR - caught error running the job
11-04-2018 17:22:04 CST spark ERROR - Job run failed!
java.lang.Exception: java.lang.RuntimeException: azkaban.jobExecutor.utils.process.ProcessFailureException
	at azkaban.jobtype.HadoopSparkJob.run(HadoopSparkJob.java:285)
	at azkaban.execapp.JobRunner.runJob(JobRunner.java:778)
	at azkaban.execapp.JobRunner.doRun(JobRunner.java:594)
	at azkaban.execapp.JobRunner.run(JobRunner.java:555)
	at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:511)
	at java.util.concurrent.FutureTask.run(FutureTask.java:266)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
	at java.lang.Thread.run(Thread.java:745)
Caused by: java.lang.RuntimeException: azkaban.jobExecutor.utils.process.ProcessFailureException
	at azkaban.jobExecutor.ProcessJob.run(ProcessJob.java:304)
	at azkaban.jobtype.HadoopSparkJob.run(HadoopSparkJob.java:281)
	... 8 more
Caused by: azkaban.jobExecutor.utils.process.ProcessFailureException
	at azkaban.jobExecutor.utils.process.AzkabanProcess.run(AzkabanProcess.java:130)
	at azkaban.jobExecutor.ProcessJob.run(ProcessJob.java:296)
	... 9 more
11-04-2018 17:22:04 CST spark ERROR - java.lang.RuntimeException: azkaban.jobExecutor.utils.process.ProcessFailureException cause: java.lang.RuntimeException: azkaban.jobExecutor.utils.process.ProcessFailureException
11-04-2018 17:22:04 CST spark INFO - Finishing job spark at 1523438524471 with status FAILED</code></pre><br><p>出现上述错误，在azkaban-exec-server-3.43.0/conf目录下修改azkaban.properties文件</p><p>在最后添加</p><pre><code class="language-html"><span style="color:#ff0000;">azkaban.webserver.url=https://192.168.66.194:8666  #web加载页面</span></code></pre>配置文件信息如下<br><pre><code class="language-html">#Azkaban
default.timezone.id=Asia/Shanghai

#Loader for projects
executor.global.properties=/root/azkaban/azkaban-exec-server-3.43.0/conf/global.properties
azkaban.project.dir=/root/azkaban/azkaban-exec-server-3.43.0/projects
azkaban.execution.dir=/root/azkaban/azkaban-exec-server-3.43.0/executions
project.temp.dir=/root/azkaban/azkaban-exec-server-3.43.0/temp

database.type=mysql
mysql.port=3306
mysql.host=192.168.66.170
mysql.database=azkaban
mysql.user=root
mysql.password=sql_9879
mysql.numconnections=100

#  Azkaban Executor settings

executor.maxThreads=50

executor.port=12321

executor.flow.threads=30

# JMX stats

jetty.connector.stats=true

executor.connector.stats=true

# Azkaban JobTypes Plugins
azkaban.jobtype.plugin.dir=/root/azkaban/azkaban-exec-server-3.43.0/plugins/jobtypes

# uncomment to enable inmemory stats for azkaban

#executor.metric.reports=true

#executor.metric.milisecinterval.default=60000

<span style="color:#ff0000;">azkaban.webserver.url=https://192.168.66.194:8666</span>

</code></pre><br>            </div>
                </div>