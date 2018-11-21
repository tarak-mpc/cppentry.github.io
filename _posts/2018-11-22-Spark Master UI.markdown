---
layout:     post
title:      Spark Master UI
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>There are 4 was to run spark:</p>
<ul><li>local</li><li>Standalone</li><li>YARN</li><li>Mesos</li></ul><p>When Spark is run in local mode (as you're doing on your laptop) a separate Spark Master and separate Spark Workers + Exectuors are not launched.</p>
<p>Instead a single Java Virtual Machine (JVM) is launched with one Executor, whose ID is <code>&lt;driver&gt;</code>. This special Executor runs the Driver (which is the "Spark shell" application in this instance) and this special Executor also
 runs our Scala code. By default, this single Executor will be started with X threads, where X is equal to the # of cores on your machine.</p>
<p>Local mode is used when you want to run Spark locally and not in a distributed cluster. Spark local mode is different than Standalone mode (which is still designed for a cluster setup).</p>
<p>To summarize, in local mode, the Spark shell application (aka the Driver) and the Spark Executor is run within the same JVM. More precisely, the single Executor that is launched is named <code>&lt;driver&gt;</code> and this Executor runs both
 the driver code and the executes our Spark Scala transformations and actions.</p>
<p>So, although there is no Master UI in local mode, if you are curious, here is what a Master UI looks like here is a screenshot:</p>
<p><code> </code> <img src="http://blueplastic.com/databricks/spark-on-a-stick/webUIs-master.jpg" alt=""></p>
<p>https://blueplastic.gitbooks.io/how-to-light-your-spark-on-a-stick/content/spark_web_uis/spark_stages_ui.html<br></p>
            </div>
                </div>