---
layout:     post
title:      Spark操作hive遇到的问题
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:14px;">Spark操作hive报错如下</span></p>
<p><span style="font-size:14px;">Exception in thread "main" java.lang.IllegalArgumentException: Unable to instantiate SparkSession with Hive support because Hive classes are not found.<br>
    at org.apache.spark.sql.SparkSession$Builder.enableHiveSupport(SparkSession.scala:778)<br>
    at scala.hiveDB.getRecords(hiveDB.scala:20)<br>
    at scala.TransData$.main(TransData.scala:12)<br>
    at scala.TransData.main(TransData.scala)</span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;">跟踪SparkSession.scala源码发现如下代码：</span></p>
<p></p>
<pre style="background-color:#c7edcc;color:#000000;font-family:Consolas;font-size:12pt;"><span style="font-size:14px;color:#808080;font-style:italic;">/**
</span><span style="font-size:14px;color:#808080;font-style:italic;"> * Return true if Hive classes can be loaded, otherwise false.
</span><span style="font-size:14px;color:#808080;font-style:italic;"> */
</span><span style="font-size:14px;color:#000080;font-weight:bold;">private</span><span style="font-size:14px;">[spark] <span style="color:#000080;font-weight:bold;">def </span>hiveClassesArePresent: Boolean = {
  <span style="color:#000080;font-weight:bold;">try </span>{
    Utils.<span style="font-style:italic;">classForName</span>(<span style="color:#660e7a;font-style:italic;">HIVE_SESSION_STATE_CLASS_NAME</span>)
    Utils.<span style="font-style:italic;">classForName</span>(<span style="color:#660e7a;font-style:italic;">HIVE_SHARED_STATE_CLASS_NAME</span>)
    Utils.<span style="font-style:italic;">classForName</span>(<span style="color:#008000;font-weight:bold;">"org.apache.hadoop.hive.conf.HiveConf"</span>)
    <span style="color:#000080;font-weight:bold;">true
</span><span style="color:#000080;font-weight:bold;">  </span>} <span style="color:#000080;font-weight:bold;">catch </span>{
    <span style="color:#000080;font-weight:bold;">case </span>_: ClassNotFoundException | _: NoClassDefFoundError =&gt; <span style="color:#000080;font-weight:bold;">false
</span><span style="color:#000080;font-weight:bold;">  </span>}
}</span></pre>
<span style="font-size:14px;">原来是缺少jar包，导致classForName失败，查询所需要的三个类所需要的maven引用并添加到pom中，解决。</span>
<p></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;">---持续更新中，填平后再做总结。<br></span></p>
            </div>
                </div>