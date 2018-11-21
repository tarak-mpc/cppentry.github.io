---
layout:     post
title:      spark部署：在YARN上运行Spark
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">相关内容</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><a href="http://www.aboutyun.com/thread-11413-1-1.html" rel="nofollow" style="color:rgb(51,102,153);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">Spark中文手册-编程指南</a><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><a href="http://www.aboutyun.com/thread-11484-1-1.html" rel="nofollow" style="color:rgb(51,102,153);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">Spark之一个快速的例子</a><a href="http://www.aboutyun.com/thread-11502-1-1.html" rel="nofollow" style="color:rgb(51,102,153);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">Spark之基本概念</a><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><a href="http://www.aboutyun.com/thread-11502-1-1.html" rel="nofollow" style="color:rgb(51,102,153);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">Spark之基本概念</a><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><a href="http://www.aboutyun.com/thread-11516-1-1.html" rel="nofollow" style="color:rgb(51,102,153);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">Spark之基本概念（2）</a><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><a href="http://www.aboutyun.com/thread-11535-1-1.html" rel="nofollow" style="color:rgb(51,102,153);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">Spark之基本概念（3）</a><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><a href="http://www.aboutyun.com/thread-11562-1-1.html" rel="nofollow" style="color:rgb(51,102,153);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">Spark-sql由入门到精通</a><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><a href="http://www.aboutyun.com/thread-11575-1-1.html" rel="nofollow" style="color:rgb(51,102,153);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">Spark-sql由入门到精通续</a><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><a href="http://www.aboutyun.com/thread-11589-1-1.html" rel="nofollow" style="color:rgb(51,102,153);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">spark GraphX编程指南（1）</a><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><a href="http://www.aboutyun.com/thread-11601-1-1.html" rel="nofollow" style="color:rgb(51,102,153);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">spark GraphX编程指南（2）</a><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><a href="http://www.aboutyun.com/thread-11615-1-1.html" rel="nofollow" style="color:rgb(51,102,153);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">spark部署：提交应用程序及独立部署模式</a><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-size:18px;"><br></span></span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-size:18px;"><span><span style="font-size:18px;"><br></span></span><br>
配置</span></span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
大部分为Spark on YARN模式提供的配置与其它部署模式提供的配置相同。下面这些是为Spark on YARN模式提供的配置。</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-size:18px;">Spark属性</span></span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><table cellspacing="0" class="t_table" style="border-collapse:collapse;border:1px solid rgb(227,237,245);table-layout:auto;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><tbody><tr><td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
Property Name</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
Default</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
Meaning</td>
</tr><tr><td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
spark.yarn.applicationMaster.waitTries</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
10</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
ApplicationMaster等待Spark master的次数以及<br>
SparkContext初始化尝试的次数</td>
</tr><tr><td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
spark.yarn.submit.file.replication</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
HDFS默认的复制次数（3）</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
上传到HDFS的文件的HDFS复制水平。<br>
这些文件包括Spark jar、app jar以及任何分布式缓存文件/档案</td>
</tr><tr><td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
spark.yarn.preserve.staging.files</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
false</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
设置为true，则在作业结束时保留阶段性文件<br>
（Spark jar、app jar以及任何分布式缓存文件）而不是删除它们</td>
</tr><tr><td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
spark.yarn.scheduler.heartbeat.interval-ms</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
5000</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
Spark application master给YARN ResourceManager<br>
发送心跳的时间间隔（ms）</td>
</tr><tr><td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
spark.yarn.max.executor.failures</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
numExecutors * 2,最小为3</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
失败应用程序之前最大的执行失败数</td>
</tr><tr><td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
spark.yarn.historyServer.address</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
(none)</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
Spark历史服务器（如host.com:18080）的地址。<br>
这个地址不应该包含一个模式（http://）。<br>
默认情况下没有设置值，这是因为该选项是一个可选选项。<br>
当Spark应用程序完成从ResourceManager UI到Spark历史<br>
服务器UI的连接时，这个地址从YARN ResourceManager得到</td>
</tr><tr><td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
spark.yarn.dist.archives</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
(none)</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
提取逗号分隔的档案列表到每个执行器的工作目录</td>
</tr><tr><td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
spark.yarn.dist.files</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
(none)</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
放置逗号分隔的文件列表到每个执行器的工作目录</td>
</tr><tr><td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
spark.yarn.executor.memoryOverhead</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
executorMemory * 0.07,最小384</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
分配给每个执行器的堆内存大小（以MB为单位）。<br>
它是VM开销、interned字符串或者其它本地开销占用的内存。<br>
这往往随着执行器大小而增长。（典型情况下是6%-10%）</td>
</tr><tr><td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
spark.yarn.driver.memoryOverhead</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
driverMemory * 0.07,最小384</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
分配给每个driver的堆内存大小（以MB为单位）。<br>
它是VM开销、interned字符串或者其它本地开销占用的内存。<br>
这往往随着执行器大小而增长。（典型情况下是6%-10%）</td>
</tr><tr><td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
spark.yarn.queue</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
default</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
应用程序被提交到的YARN队列的名称</td>
</tr><tr><td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
spark.yarn.jar</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
(none)</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
Spark jar文件的位置，覆盖默认的位置。默认情况下，<br>
Spark on YARN将会用到本地安装的Spark jar。<br>
但是Spark jar也可以HDFS中的一个公共位置。<br>
这允许YARN缓存它到节点上，而不用在每次运行应用程序时都需要分配。<br>
指向HDFS中的jar包，可以这个参数为"hdfs:///some/path"</td>
</tr><tr><td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
spark.yarn.access.namenodes</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
(none)</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
你的Spark应用程序访问的HDFS namenode列表。例如，spark.yarn.access.namenodes=hdfs://nn1<br>
.com:8032,hdfs://nn2.com:8032，<br>
Spark应用程序必须访问namenode列表，<br>
Kerberos必须正确配置来访问它们。<br>
Spark获得namenode的安全令牌，<br>
这样Spark应用程序就能够访问这些远程的HDFS集群。</td>
</tr><tr><td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
spark.yarn.containerLauncherMaxThreads</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
25</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
为了启动执行者容器，应用程序master用到的最大线程数</td>
</tr><tr><td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
spark.yarn.appMasterEnv.[EnvironmentVariableName]</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
(none)</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
添加通过EnvironmentVariableName指定的环境变量<br>
到Application Master处理YARN上的启动。<br>
用户可以指定多个该设置，从而设置多个环境变量。<br>
在yarn-cluster模式下，这控制Spark driver的环境。<br>
在yarn-client模式下，这仅仅控制执行器启动者的环境。</td>
</tr></tbody></table><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-size:18px;">在YARN上启动Spark</span></span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
确保HADOOP_CONF_DIR或YARN_CONF_DIR指向的目录包含Hadoop集群的（客户端）配置文件。这些配置用于写数据到dfs和连接到YARN ResourceManager。</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
有两种部署模式可以用来在YARN上启动Spark应用程序。在yarn-cluster模式下，Spark driver运行在application master进程中，这个进程被集群中的YARN所管理，客户端会在初始化应用程序 之后关闭。在yarn-client模式下，driver运行在客户端进程中，application master仅仅用来向YARN请求资源。</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
和Spark单独模式以及Mesos模式不同，在这些模式中，master的地址由"master"参数指定，而在YARN模式下，ResourceManager的地址从Hadoop配置得到。因此master参数是简单的yarn-client和yarn-cluster。</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
在yarn-cluster模式下启动Spark应用程序。</div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div class="blockcode" style="overflow:hidden;color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<div id="code_fR6">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
./bin/spark-submit --class path.to.your.Class --master yarn-cluster [options] &lt;app jar&gt; [app options]<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
例子：</div>
<div class="blockcode" style="overflow:hidden;color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<div id="code_n84">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
$ ./bin/spark-submit --class org.apache.spark.examples.SparkPi \<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    --master yarn-cluster \<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    --num-executors 3 \<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    --driver-memory 4g \<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    --executor-memory 2g \<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    --executor-cores 1 \<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    --queue thequeue \<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    lib/spark-examples*.jar \<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    10</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
以上启动了一个YARN客户端程序用来启动默认的 Application Master，然后SparkPi会作为Application Master的子线程运行。客户端会定期的轮询Application Master用于状态更新并将 更新显示在控制台上。一旦你的应用程序运行完毕，客户端就会退出。</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
在yarn-client模式下启动Spark应用程序，运行下面的shell脚本</div>
<div class="blockcode" style="overflow:hidden;color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<div id="code_FWx">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
$ ./bin/spark-shell --master yarn-client</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">添加其它的jar</span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
在yarn-cluster模式下，driver运行在不同的机器上，所以离开了保存在本地客户端的文件，SparkContext.addJar将不会工作。为了使SparkContext.addJar用到保存在客户端的文件， 在启动命令中加上--jars选项。</div>
<div class="blockcode" style="overflow:hidden;color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<div id="code_L75">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
$ ./bin/spark-submit --class my.main.Class \<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    --master yarn-cluster \<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    --jars my-other-jar.jar,my-other-other-jar.jar<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    my-main-jar.jar<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    app_arg1 app_arg2</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">注意事项</span>
<ul style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><li style="list-style:disc;">
在Hadoop 2.2之前，YARN不支持容器核的资源请求。因此，当运行早期的版本时，通过命令行参数指定的核的数量无法传递给YARN。在调度决策中，核请求是否兑现取决于用哪个调度器以及 如何配置调度器。</li><li style="list-style:disc;">
Spark executors使用的本地目录将会是YARN配置（yarn.nodemanager.local-dirs）的本地目录。如果用户指定了spark.local.dir，它将被忽略。</li><li style="list-style:disc;">
--files和--archives选项支持指定带 # 号文件名。例如，你能够指定--files localtest.txt#appSees.txt，它上传你在本地命名为localtest.txt的文件到HDFS，但是将会链接为名称appSees.txt。当你的应用程序运行在YARN上时，你应该使用appSees.txt去引用该文件。</li><li style="list-style:disc;">
如果你在yarn-cluster模式下运行SparkContext.addJar，并且用到了本地文件， --jars选项允许SparkContext.addJar函数能够工作。如果你正在使用 HDFS, HTTP, HTTPS或FTP，你不需要用到该选项</li></ul>            </div>
                </div>