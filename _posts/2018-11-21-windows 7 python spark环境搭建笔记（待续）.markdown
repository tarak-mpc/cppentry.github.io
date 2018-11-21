---
layout:     post
title:      windows 7 python spark环境搭建笔记（待续）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_41672744/article/details/79540142				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>windows 7 python spark环境搭建笔记</p><p>1. 安装Anaconda 3, python3.6, JAVA</p><p>2. 安装spark，解压到d:\spark， hadoop到d:\hadoop</p><p>3.到Anaconda3添加spark环境</p><p>4.到我的电脑，高级，添加环境变量</p><p>系统变量：</p><p>HADOOP_HOME=D:\HADOOP</p><p>SPARK_HOME=D:\SPARK</p><p>PATH=C:\ProgramData\Oracle\Java\javapath;%SystemRoot%\system32;%SystemRoot%;%SystemRoot%\System32\Wbem;%SYSTEMROOT%\System32\WindowsPowerShell\v1.0\;%SPARK_HOME%\bin;%Path%;D:\Spark\bin</p><p>用户变量：</p><p>path=%JAVA_HOME%\bin;%SPARK_HOME%\bin;%SPARK_HOME%\sbin;%HADOOP_HOME%\bin</p><p><br></p><p>测试：</p><p>打开 spyder</p><pre><code class="language-python">from pyspark import SparkConf, SparkContext
conf = SparkConf().setMaster("local[*]").setAppName("First_App")
sc = SparkContext(conf=conf)
data = [1, 2, 3, 4, 5]
distData = sc.parallelize(data)
print(distData)</code></pre><p>结果：</p><pre><code class="language-python">Reloaded modules: pyspark, pyspark.conf, pyspark.context, pyspark.accumulators, pyspark.cloudpickle, pyspark.util, pyspark.serializers, pyspark.broadcast, pyspark.files, pyspark.java_gateway, pyspark.find_spark_home, pyspark.storagelevel, pyspark.rdd, pyspark.join, pyspark.resultiterable, pyspark.statcounter, pyspark.rddsampler, pyspark.shuffle, pyspark.heapq3, pyspark.traceback_utils, pyspark.status, pyspark.profiler, pyspark.taskcontext, pyspark.version, pyspark._globals, pyspark.sql, pyspark.sql.types, pyspark.sql.context, pyspark.sql.session, pyspark.sql.conf, pyspark.sql.dataframe, pyspark.sql.column, pyspark.sql.readwriter, pyspark.sql.utils, pyspark.sql.streaming, pyspark.sql.udf, pyspark.sql.catalog, pyspark.sql.group, pyspark.sql.window
ParallelCollectionRDD[0] at parallelize at PythonRDD.scala:175</code></pre><br><p>初步成功，还有java版本问题，待续</p><p>继续补救：</p><p>1. 添加python环境变量 “C:\Users\...\AppData\Local\Programs\Python\Python36”</p><p>2. 安装setuptools-28.6.0：在目录下执行 cmd--python setup.py install</p><p>3.到pyspark目录下执行 cmd--python.py install</p><p>spyder下 import pyspark 成功</p><p><br></p><br><p><br></p><p><br></p><p><br></p>            </div>
                </div>