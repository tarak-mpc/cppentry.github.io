---
layout:     post
title:      再探spark之一（复习之前）加spark如何实现远程调试代码
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/yewakui2253/article/details/80324577				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>Scala的 练习https://blog.csdn.net/yewakui2253/article/details/80022403</p><p><span style="color:#ff0000;">alt+p可以用来上传东西仅限于secureCRT</span></p><p><span style="color:#ff0000;">然后put F:\Users\Administrator\WorkspacesIDEA\scalaMaven\target\scalaMaven-1.0.jar就可以将东西传到Linux上了</span></p><p>没启动spark的话。</p><p>可以通过 sbin/start-all.sh 来启动spark</p><p><strong>按crtl+c来终止任务，而不是ctrl+z</strong></p><p>spark-shell --master spark://192.168.235.128:7077  <span style="color:#cc0000;">连接到spark上</span><br></p><p> spark-shell --master spark://192.168.235.128:7077 --total-executor-cores 3 <span style="color:#cc0000;">调整任务用核的数量</span></p><p><span style="color:#666666;"> spark-shell --master spark://192.168.235.128:7077 --executor-memory 2g </span><span style="color:#ff0000;">用2g运行内存</span><br></p><p><span style="color:#666666;">spark-submit --master spark://192.168.235.128:7077 --class org.apache.spark.examples.SparkPi /export/servers/spark-2.1.0-bin-hadoop2.7/examples/jars/spark-examples_2.11-2.1.0.jar 100  </span><span style="color:#cc0000;"> 用来运行spark-submit任务</span><br></p><p><span style="color:#cc0000;"><br></span></p><p><span style="color:#cc0000;"><br></span></p><p><span style="color:#cc0000;">远程调试代码如下</span></p><pre><code class="language-java">package com.wxa.spark.one

import org.apache.spark.{SparkConf, SparkContext}

/**
  * Created by root on 2016/5/14.
  * 远程调试的方法
  */
object WordCount {
  def main(args: Array[String]) {
    //非常重要，是通向Spark集群的入口
    val conf = new SparkConf().setAppName("WC").
      setJars(Array("F:\\Users\\Administrator\\WorkspacesIDEA\\scalaMaven\\target\\scalaMaven-1.0.jar"))
        .setMaster("spark://hadoop01:7077")

    val sc = new SparkContext(conf)

    //textFile会产生两个RDD：HadoopRDD  -&gt; MapPartitinsRDD
    sc.textFile(args(0)).cache()
      // 产生一个RDD ：MapPartitinsRDD
      .flatMap(_.split(" "))
      //产生一个RDD MapPartitionsRDD
      .map((_, 1))
      //产生一个RDD ShuffledRDD
      .reduceByKey(_+_)
      //产生一个RDD: mapPartitions
      .saveAsTextFile(args(1))
    sc.stop()
  }
}
</code></pre><br>            </div>
                </div>