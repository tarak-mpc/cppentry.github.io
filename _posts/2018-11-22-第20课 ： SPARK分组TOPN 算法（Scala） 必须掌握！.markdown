---
layout:     post
title:      第20课 ： SPARK分组TOPN 算法（Scala） 必须掌握！
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：王家林大咖2018年新书《SPARK大数据商业实战三部曲》清华大学出版，清华大学出版社官方旗舰店（天猫）https://qhdx.tmall.com/?spm=a220o.1000855.1997427721.d4918089.4b2a2e5dT6bUsM					https://blog.csdn.net/duan_zhihua/article/details/50815250				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>1、输入文件</p>
<p>Spark,100<br>
Hadoop,62<br>
Flink,77<br>
Kafka,91<br>
Hadoop,93<br>
Spark,78<br>
Hadoop,69<br>
Spark,98<br>
Hadoop,62<br>
Spark,99<br>
Hadoop,61<br>
Spark,70<br>
Hadoop,75<br>
Spark,88<br>
Hadoop,68<br>
Spark,90<br>
Hadoop,61</p>
<p>2、运行结果</p>
<p>Flink:<br>
77<br>
Hadoop:<br>
61<br>
61<br>
62<br>
62<br>
68<br>
Kafka:<br>
91<br>
Spark:<br>
70<br>
78<br>
88<br>
90<br>
98</p>
<p> </p>
<p>3、源代码</p>
<p>package com.dt.spark</p>
<p>import org.apache.spark.SparkConf<br>
import org.apache.spark.SparkContext</p>
<p>object TopNGroup {<br>
    def main(args:Array[String]){<br>
      val conf = new SparkConf() //创建SparkConf对象<br>
      conf.setAppName("Wow,TopNGroup App!") //设置应用程序的名称，在程序运行的监控界面可以看到名称<br>
      conf.setMaster("local") //此时，程序在本地运行，不需要安装Spark集群<br>
       <br>
      val sc = new SparkContext(conf) //创建SparkContext对象，通过传入SparkConf实例来定制Spark运行的具体参数和配置信息<br>
      sc.setLogLevel("WARN") <br>
      val lines = sc.textFile("G://IMFBigDataSpark2016//tesdata//topNGroup1.txt", 1) //读取本地文件并设置为一个Partion<br>
      <br>
       val pairs=lines.map { line =&gt; (line.split(",")(0),line.split(",")(1).toInt) }<br>
       val grouped=pairs.groupByKey<br>
        val groupedTop5=grouped.map(grouped=&gt;<br>
      {<br>
        (grouped._1,grouped._2.toList.sortWith(_&lt;_).take(5))<br>
      })<br>
        val groupedKeySorted=groupedTop5.sortByKey()<br>
        <br>
      groupedKeySorted.collect().foreach(pair=&gt;{<br>
      println(pair._1+":")<br>
      pair._2.foreach { println }<br>
       })<br>
    sc.stop()<br>
        <br>
  }<br>
}</p>
<p> </p>
<p>4、结果截图</p>
<p> </p>
<p> </p>
<p> </p>
<p><img src="https://img-blog.csdn.net/20160306205007098?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p> </p>
            </div>
                </div>