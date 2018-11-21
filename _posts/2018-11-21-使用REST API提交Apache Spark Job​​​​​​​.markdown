---
layout:     post
title:      使用REST API提交Apache Spark Job​​​​​​​
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_29726869/article/details/82626121				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1><span style="color:#212529;"><span style="color:inherit;">使用REST API提交Apache Spark Job</span></span></h1>

<p><span style="color:#212529;">使用Apache spark时，有时需要从群集外部按需触发Spark作业。我们可以通过两种方式在群集中提交Apache spark作业。</span></p>

<ul><li><span style="color:#212529;">Spark从Spark集群中提交</span></li>
</ul><p><span style="color:#212529;">要从spark集群中提交spark作业，我们使用spark-submit。下面是一个示例shell脚本，它提交了Spark作业。大多数参与者都是自我解释的。</span></p>

<pre class="has" style="margin-left:0px;">
<code class="language-highlight">&lt;span style="color:#212529"&gt;&lt;span style="color:#212529"&gt;&lt;code&gt;&lt;span style="color:#93a1a1"&gt;#!/bin/bash&lt;/span&gt;

&lt;span style="color:#22b3eb"&gt;$SPARK_HOME&lt;/span&gt;/bin/spark-submit &lt;span style="color:#cb4b16"&gt;\&lt;/span&gt;
 &lt;span style="color:#22b3eb"&gt;--class&lt;/span&gt; com.nitendragautam.sparkbatchapp.main.Boot &lt;span style="color:#cb4b16"&gt;\&lt;/span&gt;
&lt;span style="color:#22b3eb"&gt;--master&lt;/span&gt; spark://192.168.133.128:7077 &lt;span style="color:#cb4b16"&gt;\&lt;/span&gt;
&lt;span style="color:#22b3eb"&gt;--deploy-mode&lt;/span&gt; cluster &lt;span style="color:#cb4b16"&gt;\&lt;/span&gt;
&lt;span style="color:#22b3eb"&gt;--supervise&lt;/span&gt; &lt;span style="color:#cb4b16"&gt;\&lt;/span&gt;
&lt;span style="color:#22b3eb"&gt;--executor-memory&lt;/span&gt; 4G &lt;span style="color:#cb4b16"&gt;\&lt;/span&gt;
&lt;span style="color:#22b3eb"&gt;--driver-memory&lt;/span&gt; 4G &lt;span style="color:#cb4b16"&gt;\&lt;/span&gt;
&lt;span style="color:#22b3eb"&gt;--total-executor-cores&lt;/span&gt; 2 &lt;span style="color:#cb4b16"&gt;\&lt;/span&gt;
/home/hduser/sparkbatchapp.jar &lt;span style="color:#cb4b16"&gt;\&lt;/span&gt;
/home/hduser/NDSBatchApp/input &lt;span style="color:#cb4b16"&gt;\&lt;/span&gt;
/home/hduser/NDSBatchApp/output/

&lt;/code&gt;&lt;/span&gt;&lt;/span&gt;</code></pre>

<ul><li><span style="color:#212529;">来自Spark集群外部的REST API</span></li>
</ul><p><span style="color:#212529;">在这篇文章中，我将解释如何在REST API的帮助下触发Spark作业。我请在提交Spark Job之前确保Spark Cluster正在运行。</span></p>

<p><span style="color:#212529;"><img alt="Spark Master" class="img-fluid" height="400" src="https://www.nitendragautam.com/assets/images/spark/spark-master.PNG" width="500"></span></p>

<p><span style="color:#212529;">图：Apache Spark Master</span></p>

<h2 id="trigger-spark-batch-job-by-using-shell-script"><span style="color:#212529;"><span style="color:inherit;">使用Shell脚本触发Spark批处理作业</span></span></h2>

<p><span style="color:#212529;">创建一个<code>submit_spark_job.sh</code>以下面的内容命名的Shell脚本。给shell脚本</span></p>

<pre class="has" style="margin-left:0px;">
<code class="language-highlight">&lt;span style="color:#212529"&gt;&lt;span style="color:#212529"&gt;&lt;code&gt;&lt;span style="color:#93a1a1"&gt;#!/bin/bash&lt;/span&gt;


curl &lt;span style="color:#22b3eb"&gt;-X&lt;/span&gt; POST http://192.168.133.128:6066/v1/submissions/create &lt;span style="color:#22b3eb"&gt;--header&lt;/span&gt; &lt;span style="color:#2aa198"&gt;"Content-Type:application/json;charset=UTF-8"&lt;/span&gt; &lt;span style="color:#22b3eb"&gt;--data&lt;/span&gt; &lt;span style="color:#2aa198"&gt;'{
  "appResource": "/home/hduser/sparkbatchapp.jar",
  "sparkProperties": {
    "spark.executor.memory": "4g",
    "spark.master": "spark://192.168.133.128:7077",
    "spark.driver.memory": "4g",
    "spark.driver.cores": "2",
    "spark.eventLog.enabled": "false",
    "spark.app.name": "Spark REST API201804291717022",
    "spark.submit.deployMode": "cluster",
    "spark.jars": "/home/hduser/sparkbatchapp.jar",
    "spark.driver.supervise": "true"
  },
  "clientSparkVersion": "2.0.1",
  "mainClass": "com.nitendragautam.sparkbatchapp.main.Boot",
  "environmentVariables": {
    "SPARK_ENV_LOADED": "1"
  },
  "action": "CreateSubmissionRequest",
  "appArgs": [
    "/home/hduser/NDSBatchApp/input",
    "/home/hduser/NDSBatchApp/output/"
  ]
}'&lt;/span&gt;
&lt;/code&gt;&lt;/span&gt;&lt;/span&gt;</code></pre>

<p><span style="color:#212529;">一旦火花作业成功执行，您将看到具有以下内容的输出。</span></p>

<pre class="has" style="margin-left:0px;">
<code class="language-highlight">&lt;span style="color:#212529"&gt;&lt;span style="color:#212529"&gt;&lt;code&gt;
nitendragautam@Nemo: sh submit_spark_job.sh
&lt;span style="color:#859900"&gt;{&lt;/span&gt;
  &lt;span style="color:#2aa198"&gt;"action"&lt;/span&gt; : &lt;span style="color:#2aa198"&gt;"CreateSubmissionResponse"&lt;/span&gt;,
  &lt;span style="color:#2aa198"&gt;"message"&lt;/span&gt; : &lt;span style="color:#2aa198"&gt;"Driver successfully submitted as driver-20180429125849-0001"&lt;/span&gt;,
  &lt;span style="color:#2aa198"&gt;"serverSparkVersion"&lt;/span&gt; : &lt;span style="color:#2aa198"&gt;"2.0.1"&lt;/span&gt;,
  &lt;span style="color:#2aa198"&gt;"submissionId"&lt;/span&gt; : &lt;span style="color:#2aa198"&gt;"driver-20180429125849-0001"&lt;/span&gt;,
  &lt;span style="color:#2aa198"&gt;"success"&lt;/span&gt; : &lt;span style="color:#b58900"&gt;true&lt;/span&gt;
&lt;span style="color:#859900"&gt;}&lt;/span&gt;

&lt;/code&gt;&lt;/span&gt;&lt;/span&gt;</code></pre>

<h2 id="check-status-of-spark-job-using-rest-api"><span style="color:#212529;"><span style="color:inherit;">使用REST API检查Spark作业的状态</span></span></h2>

<p><span style="color:#212529;">如果要检查Spark作业的状态，可以使用Submission Id和下面的shell脚本。</span></p>

<pre class="has" style="margin-left:0px;">
<code class="language-highlight">&lt;span style="color:#212529"&gt;&lt;span style="color:#212529"&gt;&lt;code&gt; curl http://192.168.133.128:6066/v1/submissions/status/driver-20180429125849-0001
&lt;span style="color:#859900"&gt;{&lt;/span&gt;
  &lt;span style="color:#2aa198"&gt;"action"&lt;/span&gt; : &lt;span style="color:#2aa198"&gt;"SubmissionStatusResponse"&lt;/span&gt;,
  &lt;span style="color:#2aa198"&gt;"driverState"&lt;/span&gt; : &lt;span style="color:#2aa198"&gt;"FINISHED"&lt;/span&gt;,
  &lt;span style="color:#2aa198"&gt;"serverSparkVersion"&lt;/span&gt; : &lt;span style="color:#2aa198"&gt;"2.0.1"&lt;/span&gt;,
  &lt;span style="color:#2aa198"&gt;"submissionId"&lt;/span&gt; : &lt;span style="color:#2aa198"&gt;"driver-20180429125849-0001"&lt;/span&gt;,
  &lt;span style="color:#2aa198"&gt;"success"&lt;/span&gt; : &lt;span style="color:#b58900"&gt;true&lt;/span&gt;,
  &lt;span style="color:#2aa198"&gt;"workerHostPort"&lt;/span&gt; : &lt;span style="color:#2aa198"&gt;"192.168.133.128:38451"&lt;/span&gt;,
  &lt;span style="color:#2aa198"&gt;"workerId"&lt;/span&gt; : &lt;span style="color:#2aa198"&gt;"worker-20180429124356-192.168.133.128-38451"&lt;/span&gt;
&lt;span style="color:#859900"&gt;}&lt;/span&gt;
&lt;/code&gt;&lt;/span&gt;&lt;/span&gt;</code></pre>

<p> </p>            </div>
                </div>