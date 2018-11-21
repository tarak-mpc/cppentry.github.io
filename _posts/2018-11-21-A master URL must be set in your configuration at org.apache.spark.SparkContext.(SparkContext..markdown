---
layout:     post
title:      A master URL must be set in your configuration at org.apache.spark.SparkContext.(SparkContext.
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/caiandyong/article/details/51736980				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:14px;">在Intellij idea中测试写好的Spark程序,运行时报如下错误:<br></span></p>
<p><span style="font-size:14px;">......</span></p>
<span style="font-size:14px;">Using Spark's default log4j profile: org/apache/spark/log4j-defaults.properties<br>
16/06/21 14:49:08 INFO SparkContext: Running Spark version 1.4.0<br>
16/06/21 14:49:13 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable<br>
16/06/21 14:49:14 <span style="color:#FF0000;">ERROR SparkContext: Error initializing SparkContext.<br>
org.apache.spark.SparkException: A master URL must be set in your configuration<br>
    at org.apache.spark.SparkContext.&lt;init&gt;(SparkContext.scala:368)</span><br>
    at Svm2$.main(Svm2.scala:12)<br>
    at Svm2.main(Svm2.scala)<br>
    at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)<br>
    at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)<br>
    at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)<br>
    at java.lang.reflect.Method.invoke(Method.java:606)<br>
    at com.intellij.rt.execution.application.AppMain.main(AppMain.java:140)<br>
16/06/21 14:49:14 INFO SparkContext: Successfully stopped SparkContext<br>
Exception in thread "main" org.apache.spark.SparkException: A master URL must be set in your configuration<br>
    at org.apache.spark.SparkContext.&lt;init&gt;(SparkContext.scala:368)<br>
    at Svm2$.main(Svm2.scala:12)<br>
    at Svm2.main(Svm2.scala)<br>
    at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)<br>
    at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)<br>
    at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)<br>
    at java.lang.reflect.Method.invoke(Method.java:606)<br>
    at com.intellij.rt.execution.application.AppMain.main(AppMain.java:140)<br>
16/06/21 14:49:14 INFO Utils: Shutdown hook called<br><br>
Process finished with exit code 1<br><br></span>
<p><span style="font-size:14px;">解决方法:</span></p>
<p><span style="font-size:14px;">进入:Run &gt; Edit Configurations... &gt; Application &gt; "<span style="color:#FF0000;">My project name</span>" &gt; Configuraton,</span></p>
<p><span style="font-size:14px;">设置VM options项为<span style="color:#FF0000;">-Dspark.master=local</span>.</span></p>
<p><span style="font-size:14px;">-Dspark.master=local:表示设置我的spark程序以local模式运行.<br></span></p>
<p><br></p>
<p><br></p>
<p><span style="font-size:14px;"><br></span></p>
            </div>
                </div>