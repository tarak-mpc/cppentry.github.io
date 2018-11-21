---
layout:     post
title:      spark: scala实现worldcount
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:14px;"><strong>spark: scala实现worldcount</strong></span></p>
<p><strong><br></strong></p>
<p><strong>1. 首先创建hdfs目录</strong></p>
<p>在master节点（OPENFIRE-DEV）上创建hdfs目录，创建步骤如下：<br></p>
<p>//创建hdfs目录，普通目录是读取不到文件<br></p>
<p>hdfs   dfs -mkdir /alidata <br></p>
<p>#将要统计单词的文件拷贝到hdfs目录中<br></p>
<p>hadoop fs -put /usr/local/hadoop/hadoop-2.6.0/README.txt /alidata</p>
<p><br></p>
<p><strong>2. scala程序</strong></p>
<p></p><pre><code class="language-java">package com.liuzx.test

import org.apache.spark.SparkConf
import org.apache.spark.SparkContext

object SparkOnFile {
  def main(args: Array[String]) {
    val sparkConf = new SparkConf().setMaster("spark://OPENFIRE-DEV:7080").setAppName("spark sql hbase test");
    val sc = new SparkContext(sparkConf);

    val textFile = sc.textFile("hdfs://192.168.0.108:9000/alidata/README.txt")
    val counts = textFile.flatMap(line =&gt; line.split(" "))
      .map(word =&gt; (word, 1))
      .reduceByKey(_ + _)
    counts.saveAsTextFile("hdfs://192.168.0.108:9000/alidata/word_result.txt")
  }
}</code></pre>
<p><strong>3.  提交作业</strong></p><pre><code class="language-python">spark-submit --class com.liuzx.test.SparkOnFile --master spark://dev-app-209-211:7080 /usr/local/wonhigh/miu-tag-spark-0.0.1-SNAPSHOT.jar</code></pre><br><strong>4. 查看结果</strong>
<p></p><pre><code class="language-python">[hadoop@OPENFIRE-DEV data]$ hadoop fs -ls /alidata
Found 2 items
-rw-r--r--   2 hadoop supergroup       1366 2016-06-26 04:15 /alidata/README.txt
drwxr-xr-x   - hadoop supergroup          0 2016-06-26 04:28 /alidata/word_result.txt</code></pre><br><pre><code class="language-python">[hadoop@OPENFIRE-DEV data]$ hadoop fs -ls /alidata/word_result.txt
Found 3 items
-rw-r--r--   2 hadoop supergroup          0 2016-06-26 04:28 /alidata/word_result.txt/_SUCCESS
-rw-r--r--   3 hadoop supergroup        681 2016-06-26 04:28 /alidata/word_result.txt/part-00000
-rw-r--r--   3 hadoop supergroup        893 2016-06-26 04:28 /alidata/word_result.txt/part-00001</code></pre><br><pre><code class="language-python">[hadoop@OPENFIRE-DEV data]$ hadoop fs -cat /alidata/word_result.txt/part-00000
(under,1)
(this,3)
(distribution,2)
(Technology,1)
(country,1)
(is,1)
(Jetty,1)
(currently,1)
(permitted.,1)
(check,1)
(have,1)
(Security,1)
(U.S.,1)
(with,1)
(BIS,1)
(This,1)
(mortbay.org.,1)
((ECCN),1)
(using,2)
(security,1)
(Department,1)
(export,1)
(reside,1)
(any,1)
(algorithms.,1)
(from,1)
(re-export,2)
(has,1)
(SSL,1)
(Industry,1)
(Administration,1)
(details,1)
(provides,1)
(http://hadoop.apache.org/core/,1)
(country's,1)
(Unrestricted,1)
(740.13),1)
(policies,1)
(country,,1)
(concerning,1)
(uses,1)
(Apache,1)
(possession,,2)
(information,2)
(our,2)
(as,1)
(,18)
(Bureau,1)
(wiki,,1)
(please,2)
(form,1)
(information.,1)
(ENC,1)
(Export,2)
(included,1)
(asymmetric,1)
[hadoop@OPENFIRE-DEV data]$ </code></pre><br><br><p><br></p>
            </div>
                </div>