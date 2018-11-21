---
layout:     post
title:      spark官方文档之——Running Spark on YARN YARN上运行SPARK
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><br></p>
<p><span></span>支持YARN上运行spark是在版本0.6.0上添加的，后续版本中完善。</p>
<p><br></p>
<h1>准备</h1>
<p><span></span>在YARN上运行spark需要一个分布式的二进制spark文件，这个文件被编译能够支持YARN（download时会让选择版本，有支持hadoop yarn的版本）。二进制文件可以从spark工程网站上下载。自己编译spark，参考<a href="http://spark.apache.org/docs/latest/building-spark.html" rel="nofollow" style="color:rgb(0,136,204);text-decoration:none;font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:22px;">Building
 Spark</a>（http://spark.apache.org/docs/latest/building-spark.html）。</p>
<p><br></p>
<h1>配置</h1>
<p><span></span>YARN上的sprak的大多数配置和其他部署模式下的配置相同。更多信息参考<a href="http://spark.apache.org/docs/latest/configuration.html" rel="nofollow" style="color:rgb(0,136,204);text-decoration:none;font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:22px;">configuration
 page</a> 。以下配置是YARN上spark特定的（没翻译，用到时再细看）。</p>
<p></p>
<h4 id="spark-properties" style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:20px;color:rgb(51,51,51);font-size:18px;">
Spark Properties</h4>
<table class="table" style="border-collapse:collapse;border-spacing:0px;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:22px;"><tbody><tr><th style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
Property Name</th>
<th style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
Default</th>
<th style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
Meaning</th>
</tr><tr><td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
<code style="font-family:Menlo, 'Lucida Console', monospace;color:rgb(68,68,68);border:none;">spark.yarn.am.memory</code></td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
512m</td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
Amount of memory to use for the YARN Application Master in client mode, in the same format as JVM memory strings (e.g. <code style="font-family:Menlo, 'Lucida Console', monospace;color:rgb(68,68,68);border:none;">512m</code>, <code style="font-family:Menlo, 'Lucida Console', monospace;color:rgb(68,68,68);border:none;">2g</code>).
 In cluster mode, use <code style="font-family:Menlo, 'Lucida Console', monospace;color:rgb(68,68,68);border:none;">spark.driver.memory</code> instead.</td>
</tr><tr><td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
<code style="font-family:Menlo, 'Lucida Console', monospace;color:rgb(68,68,68);border:none;">spark.driver.cores</code></td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
1</td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
Number of cores used by the driver in YARN cluster mode. Since the driver is run in the same JVM as the YARN Application Master in cluster mode, this also controls the cores used by the YARN AM. In client mode, use <code style="font-family:Menlo, 'Lucida Console', monospace;color:rgb(68,68,68);border:none;">spark.yarn.am.cores</code> to
 control the number of cores used by the YARN AM instead.</td>
</tr><tr><td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
<code style="font-family:Menlo, 'Lucida Console', monospace;color:rgb(68,68,68);border:none;">spark.yarn.am.cores</code></td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
1</td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
Number of cores to use for the YARN Application Master in client mode. In cluster mode, use <code style="font-family:Menlo, 'Lucida Console', monospace;color:rgb(68,68,68);border:none;">spark.driver.cores</code> instead.</td>
</tr><tr><td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
<code style="font-family:Menlo, 'Lucida Console', monospace;color:rgb(68,68,68);border:none;">spark.yarn.am.waitTime</code></td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
100s</td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
In yarn-cluster mode, time for the application master to wait for the SparkContext to be initialized. In yarn-client mode, time for the application master to wait for the driver to connect to it.</td>
</tr><tr><td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
<code style="font-family:Menlo, 'Lucida Console', monospace;color:rgb(68,68,68);border:none;">spark.yarn.submit.file.replication</code></td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
The default HDFS replication (usually 3)</td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
HDFS replication level for the files uploaded into HDFS for the application. These include things like the Spark jar, the app jar, and any distributed cache files/archives.</td>
</tr><tr><td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
<code style="font-family:Menlo, 'Lucida Console', monospace;color:rgb(68,68,68);border:none;">spark.yarn.preserve.staging.files</code></td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
false</td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
Set to true to preserve the staged files (Spark jar, app jar, distributed cache files) at the end of the job rather than delete them.</td>
</tr><tr><td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
<code style="font-family:Menlo, 'Lucida Console', monospace;color:rgb(68,68,68);border:none;">spark.yarn.scheduler.heartbeat.interval-ms</code></td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
5000</td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
The interval in ms in which the Spark application master heartbeats into the YARN ResourceManager.</td>
</tr><tr><td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
<code style="font-family:Menlo, 'Lucida Console', monospace;color:rgb(68,68,68);border:none;">spark.yarn.max.executor.failures</code></td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
numExecutors * 2, with minimum of 3</td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
The maximum number of executor failures before failing the application.</td>
</tr><tr><td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
<code style="font-family:Menlo, 'Lucida Console', monospace;color:rgb(68,68,68);border:none;">spark.yarn.historyServer.address</code></td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
(none)</td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
The address of the Spark history server (i.e. host.com:18080). The address should not contain a scheme (http://). Defaults to not being set since the history server is an optional service. This address is given to the YARN ResourceManager when the Spark application
 finishes to link the application from the ResourceManager UI to the Spark history server UI. For this property, YARN properties can be used as variables, and these are substituted by Spark at runtime. For eg, if the Spark history server runs on the same node
 as the YARN ResourceManager, it can be set to `${hadoopconf-yarn.resourcemanager.hostname}:18080`.</td>
</tr><tr><td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
<code style="font-family:Menlo, 'Lucida Console', monospace;color:rgb(68,68,68);border:none;">spark.yarn.dist.archives</code></td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
(none)</td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
Comma separated list of archives to be extracted into the working directory of each executor.</td>
</tr><tr><td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
<code style="font-family:Menlo, 'Lucida Console', monospace;color:rgb(68,68,68);border:none;">spark.yarn.dist.files</code></td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
(none)</td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
Comma-separated list of files to be placed in the working directory of each executor.</td>
</tr><tr><td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
<code style="font-family:Menlo, 'Lucida Console', monospace;color:rgb(68,68,68);border:none;">spark.executor.instances</code></td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
2</td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
The number of executors. Note that this property is incompatible with<code style="font-family:Menlo, 'Lucida Console', monospace;color:rgb(68,68,68);border:none;">spark.dynamicAllocation.enabled</code>.</td>
</tr><tr><td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
<code style="font-family:Menlo, 'Lucida Console', monospace;color:rgb(68,68,68);border:none;">spark.yarn.executor.memoryOverhead</code></td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
executorMemory * 0.10, with minimum of 384</td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
The amount of off heap memory (in megabytes) to be allocated per executor. This is memory that accounts for things like VM overheads, interned strings, other native overheads, etc. This tends to grow with the executor size (typically 6-10%).</td>
</tr><tr><td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
<code style="font-family:Menlo, 'Lucida Console', monospace;color:rgb(68,68,68);border:none;">spark.yarn.driver.memoryOverhead</code></td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
driverMemory * 0.07, with minimum of 384</td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
The amount of off heap memory (in megabytes) to be allocated per driver in cluster mode. This is memory that accounts for things like VM overheads, interned strings, other native overheads, etc. This tends to grow with the container size (typically 6-10%).</td>
</tr><tr><td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
<code style="font-family:Menlo, 'Lucida Console', monospace;color:rgb(68,68,68);border:none;">spark.yarn.am.memoryOverhead</code></td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
AM memory * 0.07, with minimum of 384</td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
Same as <code style="font-family:Menlo, 'Lucida Console', monospace;color:rgb(68,68,68);border:none;">spark.yarn.driver.memoryOverhead</code>, but for the Application Master in client mode.</td>
</tr><tr><td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
<code style="font-family:Menlo, 'Lucida Console', monospace;color:rgb(68,68,68);border:none;">spark.yarn.am.port</code></td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
(random)</td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
Port for the YARN Application Master to listen on. In YARN client mode, this is used to communicate between the Spark driver running on a gateway and the Application Master running on YARN. In YARN cluster mode, this is used for the dynamic executor feature,
 where it handles the kill from the scheduler backend.</td>
</tr><tr><td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
<code style="font-family:Menlo, 'Lucida Console', monospace;color:rgb(68,68,68);border:none;">spark.yarn.queue</code></td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
default</td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
The name of the YARN queue to which the application is submitted.</td>
</tr><tr><td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
<code style="font-family:Menlo, 'Lucida Console', monospace;color:rgb(68,68,68);border:none;">spark.yarn.jar</code></td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
(none)</td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
The location of the Spark jar file, in case overriding the default location is desired. By default, Spark on YARN will use a Spark jar installed locally, but the Spark jar can also be in a world-readable location on HDFS. This allows YARN to cache it on nodes
 so that it doesn't need to be distributed each time an application runs. To point to a jar on HDFS, for example, set this configuration to "hdfs:///some/path".</td>
</tr><tr><td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
<code style="font-family:Menlo, 'Lucida Console', monospace;color:rgb(68,68,68);border:none;">spark.yarn.access.namenodes</code></td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
(none)</td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
A list of secure HDFS namenodes your Spark application is going to access. For example, `spark.yarn.access.namenodes=hdfs://nn1.com:8032,hdfs://nn2.com:8032`. The Spark application must have acess to the namenodes listed and Kerberos must be properly configured
 to be able to access them (either in the same realm or in a trusted realm). Spark acquires security tokens for each of the namenodes so that the Spark application can access those remote HDFS clusters.</td>
</tr><tr><td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
<code style="font-family:Menlo, 'Lucida Console', monospace;color:rgb(68,68,68);border:none;">spark.yarn.appMasterEnv.[EnvironmentVariableName]</code></td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
(none)</td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
Add the environment variable specified by <code style="font-family:Menlo, 'Lucida Console', monospace;color:rgb(68,68,68);border:none;">EnvironmentVariableName</code> to the Application Master process launched on YARN. The user can specify multiple
 of these and to set multiple environment variables. In yarn-cluster mode this controls the environment of the SPARK driver and in yarn-client mode it only controls the environment of the executor launcher.</td>
</tr><tr><td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
<code style="font-family:Menlo, 'Lucida Console', monospace;color:rgb(68,68,68);border:none;">spark.yarn.containerLauncherMaxThreads</code></td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
25</td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
The maximum number of threads to use in the application master for launching executor containers.</td>
</tr><tr><td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
<code style="font-family:Menlo, 'Lucida Console', monospace;color:rgb(68,68,68);border:none;">spark.yarn.am.extraJavaOptions</code></td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
(none)</td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
A string of extra JVM options to pass to the YARN Application Master in client mode. In cluster mode, use spark.driver.extraJavaOptions instead.</td>
</tr><tr><td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
<code style="font-family:Menlo, 'Lucida Console', monospace;color:rgb(68,68,68);border:none;">spark.yarn.am.extraLibraryPath</code></td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
(none)</td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
Set a special library path to use when launching the application master in client mode.</td>
</tr><tr><td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
<code style="font-family:Menlo, 'Lucida Console', monospace;color:rgb(68,68,68);border:none;">spark.yarn.maxAppAttempts</code></td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
yarn.resourcemanager.am.max-attempts in YARN</td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
The maximum number of attempts that will be made to submit the application. It should be no larger than the global number of max attempts in the YARN configuration.</td>
</tr><tr><td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
<code style="font-family:Menlo, 'Lucida Console', monospace;color:rgb(68,68,68);border:none;">spark.yarn.submit.waitAppCompletion</code></td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
true</td>
<td style="line-height:20px;vertical-align:top;border-top-width:1px;border-top-style:solid;border-top-color:rgb(221,221,221);">
In YARN cluster mode, controls whether the client waits to exit until the application completes. If set to true, the client process will stay alive reporting the application's status. Otherwise, the client process will exit after submission.</td>
</tr></tbody></table><h1>YARN上发布spark程序</h1>
<div><span></span>确保HADOOP_CONF_DIR或YARN_CONF_DIR指向包含hadoop集群配置文件的文件夹。这些配置用来写数据到dfs，连接到YARN的resourceManager。文件中的的配置文件会分布到YARN集群上，让应用使用的所有containers使用相同的配置。如果配置指向不受YARN控制管理的java系统属性或环境变量，这些配置也应该在应用的配置（当在client模式运行时的driver，executors和AM）里设置（这句话不是很理解··）。</div>
<div><span></span>YARN上发布spark应用程序又两种方式。在yarn-cluster模式中，集群上YARN控制管理的master进程里运行spark driver，并且client可以初始化应用之后结束掉自己的生命（生命周期）。在yarn-client模式中，driver运行在client进程中，并且应用master只用来从YARN申请资源。</div>
<div><span></span>standalone和mesos模式下，master的地址在“master”参数中指定，而YARN模式，resourceManager的地址是从hadoop配置中得到的，因此，master参数只是简单的“yarn-client”或“yarn-cluster”</div>
<div><span></span>yarn-cluster模式运行spark程序：</div>
<div>
<pre style="font-family:Menlo, 'Lucida Console', monospace;font-size:13px;color:rgb(51,51,51);line-height:20px;background-color:rgb(245,245,245);"><code style="font-family:Menlo, 'Lucida Console', monospace;font-size:12px;color:inherit;background-color:transparent;border:0px;">./bin/spark-submit --class path.to.your.Class --master yarn-cluster [options] &lt;app jar&gt; [app options]</code></pre>
<span></span>例如：</div>
<div>
<pre style="font-family:Menlo, 'Lucida Console', monospace;font-size:13px;color:rgb(51,51,51);line-height:20px;background-color:rgb(245,245,245);"><code style="font-family:Menlo, 'Lucida Console', monospace;font-size:12px;color:inherit;background-color:transparent;border:0px;">$ ./bin/spark-submit --class org.apache.spark.examples.SparkPi \
    --master yarn-cluster \
    --num-executors 3 \
    --driver-memory 4g \
    --executor-memory 2g \
    --executor-cores 1 \
    --queue thequeue \
    lib/spark-examples*.jar \
    10</code></pre>
<span></span>上面命令开始了一个spark client程序，client程序启动应用默认的master。接着sparkPi会作为应用master的一个子线程运行。client会定期检查应用master来更新状态并在终端显示。一旦你的应用运行结束client就会退出。怎样看driver和executor的log参考下面“Debugging your application”章节。</div>
<div><span></span>yarn-client模式运行一个spark应用，像上面那样做，只是把“yarn-cluster”替换成“yarn-client”。运行spark-shell：</div>
<div>
<pre style="font-family:Menlo, 'Lucida Console', monospace;font-size:13px;color:rgb(51,51,51);line-height:20px;background-color:rgb(245,245,245);"><code style="font-family:Menlo, 'Lucida Console', monospace;font-size:12px;color:inherit;background-color:transparent;border:0px;">$ ./bin/spark-shell --master yarn-client</code></pre>
<h1>添加其他JARs包</h1>
</div>
<div><span></span>在yarn-cluster模式中，driver与这个client不同的机器上，所以SparkContext.addJar不会工作，因为对于client来说是本地文件的jar才能工作。为了使SparkContext.addJar在client上可见，在命令中用--jar参数包括它们：</div>
<div>
<pre style="font-family:Menlo, 'Lucida Console', monospace;font-size:13px;color:rgb(51,51,51);line-height:20px;background-color:rgb(245,245,245);"><code style="font-family:Menlo, 'Lucida Console', monospace;font-size:12px;color:inherit;background-color:transparent;border:0px;">$ ./bin/spark-submit --class my.main.Class \
    --master yarn-cluster \
    --jars my-other-jar.jar,my-other-other-jar.jar
    my-main-jar.jar
    app_arg1 app_arg2</code></pre>
<br></div>
<h1>调试你的程序</h1>
<div><span></span>在YARN的术语中，executors和应用master（appMarster）在”containers“内运行。YARN在应用完成之后有两种模式来处理container的logs。如果日志收集功能开启（yarn.log-aggregation-enable配置），container日志会被拷贝到HDFS并在本地机上删除。用”yarn logs“命令可在集群上任何地方查看logs：</div>
<div>
<pre style="font-family:Menlo, 'Lucida Console', monospace;font-size:13px;color:rgb(51,51,51);line-height:20px;background-color:rgb(245,245,245);"><code style="font-family:Menlo, 'Lucida Console', monospace;font-size:12px;color:inherit;background-color:transparent;border:0px;">yarn logs -applicationId &lt;app ID&gt;</code></pre>
<span></span>将会打印出给定应用的所有containers的log文件的内容。你也可以用HDFS shell或者API直接在HDFS中查看container日志文件。可以查看你的YARN配置（yarn.nodemanager.remote-app-log-dir和yarn.nodemanager.remote-app-log-dir-suffix）来找到存放日志的文件夹。</div>
<div><span></span>当log收集功能没有开启，log会本地存留到每个机器的YARN_APP_LOGS_DIR下，根据hadoop版本和安装，通常会配置到/tmp/logs或$HADOOP_HOME/logs/userlogs下。查看日志需要到包含它们的主机下查看这些文件夹。子文件夹根据应用ID和container ID来管理日志文件。</div>
<div><span></span>为了review每个container的运行环境，增大yarn.nodemanager.delete.debug-delay-sec为一个大的值（例如36000），并且然后在节点的yarn.nodemanager.local-dirs上，访问应用缓存，contains就是运行在yarn.nodemanager.local-dirs上。这个文件夹包含运行脚本，jars包，和运行每个container的所有环境变量。此过程在调试路径问题时特别有用（注意上述能力是在集群设置和所有node
 managers重启上需要管理员权限的，因此，不适用于托管集群）。</div>
<div><span></span>应用master或executors使用定制的log4j配置，有两个操作：</div>
<div><span></span>*通过添加到--files列表中，使用spark-submit上传定制的log4j.properties。</div>
<div><span></span>*添加<span style="color:rgb(68,68,68);font-family:Menlo, 'Lucida Console', monospace;line-height:20px;">-Dlog4j.configuration=&lt;location of configuration file&gt; 到<span style="color:rgb(68,68,68);font-family:Menlo, 'Lucida Console', monospace;line-height:20px;">spark.driver.extraJavaOptions（driver）或<span style="color:rgb(68,68,68);font-family:Menlo, 'Lucida Console', monospace;line-height:20px;">spark.executor.extraJavaOptions（executor）。注意如果使用文件，文件：协议应显示提供，并在所有节点文件需要本地存在。</span></span></span></div>
<div><span style="color:rgb(68,68,68);font-family:Menlo, 'Lucida Console', monospace;line-height:20px;"><span style="color:rgb(68,68,68);font-family:Menlo, 'Lucida Console', monospace;line-height:20px;"><span style="color:rgb(68,68,68);font-family:Menlo, 'Lucida Console', monospace;line-height:20px;"><span></span>注意第一个操作，executors和应用master会共享相同的log4j配置，所以相同节点上运行时可能会引发问题（例如，尝试写相同的日志文件）。</span></span></span></div>
<div><span style="color:rgb(68,68,68);font-family:Menlo, 'Lucida Console', monospace;line-height:20px;"><span style="color:rgb(68,68,68);font-family:Menlo, 'Lucida Console', monospace;line-height:20px;"><span style="color:rgb(68,68,68);font-family:Menlo, 'Lucida Console', monospace;line-height:20px;"><span></span>在YARN中如果你需要指向合适的位置放置日志文件，来让<span style="color:rgb(68,68,68);font-family:Menlo, 'Lucida Console', monospace;line-height:20px;">YARN能够正确的显示和收集日志，请在log4j.properties中使用spark.yarn.app.container.log.dir。例如，<span style="color:rgb(68,68,68);font-family:Menlo, 'Lucida Console', monospace;line-height:19px;">log4j.appender.file_appender.File=${spark.yarn.app.container.log.dir}/spark.log。对于流式应用，配置RollingFileAppender并设置日志文件存放位置到YARN的日志文件夹下，会避免大的日志文件导致的磁盘空间溢出，并且可使用YARN的日志工具访问。</span></span></span></span></span></div>
<div><span style="color:rgb(68,68,68);font-family:Menlo, 'Lucida Console', monospace;line-height:20px;"><span style="color:rgb(68,68,68);font-family:Menlo, 'Lucida Console', monospace;line-height:20px;"><span style="color:rgb(68,68,68);font-family:Menlo, 'Lucida Console', monospace;line-height:20px;"><span style="color:rgb(68,68,68);font-family:Menlo, 'Lucida Console', monospace;line-height:20px;"><span style="color:rgb(68,68,68);font-family:Menlo, 'Lucida Console', monospace;line-height:19px;"><br></span></span></span></span></span></div>
<h1><span style="color:rgb(68,68,68);font-family:Menlo, 'Lucida Console', monospace;line-height:20px;"><span style="color:rgb(68,68,68);font-family:Menlo, 'Lucida Console', monospace;line-height:20px;"><span style="color:rgb(68,68,68);font-family:Menlo, 'Lucida Console', monospace;line-height:20px;"><span style="color:rgb(68,68,68);font-family:Menlo, 'Lucida Console', monospace;line-height:20px;"><span style="color:rgb(68,68,68);font-family:Menlo, 'Lucida Console', monospace;line-height:19px;">重要的注意点</span></span></span></span></span></h1>
<div><span style="font-family:Menlo, 'Lucida Console', monospace;color:#444444;"><span style="line-height:19px;">*core请求是否are honored in调度策略，取决于使用哪个scheduler和怎样配置它。</span></span></div>
<div><span style="font-family:Menlo, 'Lucida Console', monospace;color:#444444;"><span style="line-height:19px;">*在yarn-cluster模式，spark executors和spark driver使用的本地文件夹是YARN配置的本地文件夹（hadoop yarn 配置 yarn.nodemanager.local-dirs）。如果用户指定（spark.local.dir），它会被忽略掉。在yarn-client模式，spark
 executors会使用YARN配置的本地文件夹，而spark driver使用spark.locak.dir定义的。这是因为yarn-client模式中spark driver不在YARN集群上运行，只有spark executors运行在集群上。</span></span></div>
<div><span style="font-family:Menlo, 'Lucida Console', monospace;color:#444444;"><span style="line-height:19px;">*--files和--archives操作支持像hadoop那样指定文件名包含#的文件。例如，你可以指定：--files localtest.txt#appSees.txt，这会上传名称为localtest.txt的本地文件到HDFS，····（不太懂）</span></span></div>
<div><span style="font-family:Menlo, 'Lucida Console', monospace;color:#444444;"><span style="line-height:19px;">*--jars操作允许你在yarn-cluster模式下用SparkContext.addJar作用于本地文件。如果你正在使用<span style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:20px;">HDFS,
 HTTP, HTTPS, or FTP文件，这个操作是不需要的。</span></span></span></div>
<p></p>
            </div>
                </div>