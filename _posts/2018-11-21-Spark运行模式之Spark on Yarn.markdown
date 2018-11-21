---
layout:     post
title:      Spark运行模式之Spark on Yarn
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1>Spark的四种运行模式</h1>

<p style="text-indent:50px;">特点：不管使用什么模式，Spark应用程序的代码是一样的，只需要通过参数--master来指定</p>

<ul><li>local</li>
	<li>standalone  是spark自带的，如果一个集群式standalone模式，那么就需要在堕胎机器上同时部署spark环境</li>
	<li>yarn，统一使用YARN进行整个集群作业的资源调度</li>
	<li>mesos</li>
</ul><h1>Spark ON Yarn 概述</h1>

<ul><li> spark支持可插拔的集群管理模式</li>
	<li> 对于yarn而言，spark application仅仅是个客户端而已</li>
</ul><h1>Spark ON Yarn的两种模式对比</h1>

<h2>spark on yarn 之client模式</h2>

<p style="text-indent:50px;">客户端通过driver 在集群中申请到资源后， 还需要将程序发送到各个worker节点，所以driver和集群之间的联系是不能断的。client是不能退出的。</p>

<p style="text-indent:50px;">日志信息会在控制台输出，方便测试</p>

<p style="text-indent:50px;">Yarn的ApplicationMaster只负责资源调度</p>

<h2>spark on yarn 之cluster模式</h2>

<p style="text-indent:50px;">driver是运行在ApplicationMaster中</p>

<p style="text-indent:50px;">clinet只要提交完作业就可以关掉，因为作业已经提交到yarn上</p>

<p style="text-indent:50px;">ApplicationMaster不仅需要调度资源，还需要调度作业，因为driver是运行在ApplicationMaster中</p>

<p style="text-indent:50px;">运行输出日志在终端是看不到的，因为日志是在driver上。只能 通过yarn  logs -application_id **</p>

<h1>测试验证</h1>

<h2>设置HADOOP_CONF_DIR或者是YARN_CONF_DIR</h2>

<p style="text-indent:0;">如果想运行spark在yarn之上，那就必须要设置HADOOP_CONF_DIR或者是YARN_CONF_DIR</p>

<p style="text-indent:0;">官网给出的解释如下：</p>

<pre class="has">
<code> yarn	Connect to a YARN cluster in client or cluster mode depending on the value of --deploy-mode. 
 The cluster location will be found based on the HADOOP_CONF_DIR or YARN_CONF_DIR variable.
 </code></pre>

<pre class="has">
<code>1:export HADOOP_CONF_DIR=/home/hadoop/app/hadoop-2.6.0-cdh5.7.0/etc/hadoop
2:$SPARK_HOME/conf/spark-env.sh中配置上述选项</code></pre>

<h2>启动hadoop服务</h2>

<pre class="has">
<code>[hadoop@hadoop000 hadoop-2.6.0-cdh5.7.0]$ sbin/start-all.sh 
This script is Deprecated. Instead use start-dfs.sh and start-yarn.sh
18/09/29 21:08:29 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
Starting namenodes on [hadoop000]</code></pre>

<h2> 基于Client模式提交Spark任务</h2>

<pre class="has">
<code>./bin/spark-submit \
--class org.apache.spark.examples.SparkPi \
--master yarn \
--executor-memory 1G \
--num-executors 1 \
/home/hadoop/app/spark-2.1.0-bin-2.6.0-cdh5.7.0/examples/jars/spark-examples_2.11-2.1.0.jar \
4</code></pre>

<h2>验证Client模式</h2>

<pre class="has">
<code>18/09/29 21:23:32 INFO scheduler.DAGScheduler: ResultStage 0 (reduce at SparkPi.scala:38) finished in 36.122 s
18/09/29 21:23:33 INFO cluster.YarnScheduler: Removed TaskSet 0.0, whose tasks have all completed, from pool 
18/09/29 21:23:33 INFO scheduler.DAGScheduler: Job 0 finished: reduce at SparkPi.scala:38, took 38.391304 s
Pi is roughly 3.143197857994645</code></pre>

<h2>基于Cluster模式提交任务</h2>

<pre class="has">
<code>./bin/spark-submit \
--class org.apache.spark.examples.SparkPi \
--master yarn-cluster \
--executor-memory 1G \
--num-executors 1 \
/home/hadoop/app/spark-2.1.0-bin-2.6.0-cdh5.7.0/examples/jars/spark-examples_2.11-2.1.0.jar \
4</code></pre>

<p> </p>            </div>
                </div>