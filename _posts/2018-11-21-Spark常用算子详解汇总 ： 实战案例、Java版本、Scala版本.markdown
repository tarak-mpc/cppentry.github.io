---
layout:     post
title:      Spark常用算子详解汇总 ： 实战案例、Java版本、Scala版本
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/leen0304/article/details/78836073				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>官网API地址：</p>

<p>JavaRDD：<a href="http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.api.java.JavaRDD" rel="nofollow">http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.api.java.JavaRDD</a> <br>
JavaPairRDD：<a href="http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.api.java.JavaPairRDD" rel="nofollow">http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.api.java.JavaPairRDD</a></p>

<p>RDD：<a href="http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.rdd.RDD" rel="nofollow">http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.rdd.RDD</a> <br>
PairRDDFunctions：<a href="http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.rdd.PairRDDFunctions" rel="nofollow">http://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.rdd.PairRDDFunctions</a></p>

<hr>

<p><font size="3"><a href="http://blog.csdn.net/leen0304/article/details/78652663" rel="nofollow">Spark算子[01]：foreach，foreachPartition </a> <br>
<font size="3"> <br>
<a href="http://blog.csdn.net/leen0304/article/details/78656269" rel="nofollow"> Spark算子[02]：coalesce，repartition </a> <br>
<font size="3"> <br>
<a href="http://blog.csdn.net/leen0304/article/details/78658113" rel="nofollow"> Spark算子[03]：mapPartitions，mapPartitionsWithIndex 源码实战案例分析  </a> <br>
<font size="3"> <br>
<a href="http://blog.csdn.net/leen0304/article/details/78743715" rel="nofollow">Spark算子[04]：map，flatMap，mapToPair，flatMapToPair </a> <br>
<font size="3"> <br>
<a href="http://blog.csdn.net/leen0304/article/details/78745278" rel="nofollow">  Spark算子[05]：parallelize，makeRDD，textFile  </a> <br>
<font size="3"> <br>
<a href="http://blog.csdn.net/leen0304/article/details/78750257" rel="nofollow"> Spark算子[06]：union，distinct，cartesian，intersection，subtract  </a> <br>
<font size="3"> <br>
<a href="http://blog.csdn.net/leen0304/article/details/78752366" rel="nofollow"> Spark算子[07]：reduce，reduceByKey，count，countByKey  </a> <br>
<font size="3"> <br>
<a href="http://blog.csdn.net/leen0304/article/details/78775458" rel="nofollow">Spark算子[08]：combineByKey详解  </a> <br>
<font size="3"> <br>
<a href="http://blog.csdn.net/leen0304/article/details/78777936" rel="nofollow"> Spark算子[09]：aggregateByKey、aggregate详解  </a> <br>
<font size="3"> <br>
<a href="http://blog.csdn.net/leen0304/article/details/78789564" rel="nofollow"> Spark算子[10]：foldByKey、fold 源码实例详解  </a> <br>
<font size="3"> <br>
<a href="http://blog.csdn.net/leen0304/article/details/78790489" rel="nofollow">  Spark算子[11]：reduce、aggregate、fold 详解  </a> <br>
<font size="3"> <br>
<a href="http://blog.csdn.net/leen0304/article/details/78656269" rel="nofollow"> Spark算子[02]：coalesce，repartition </a> <br>
<font size="3"> <br>
<a href="http://blog.csdn.net/leen0304/article/details/78793313" rel="nofollow">Spark算子[12]：groupByKey、cogroup、join、lookup 源码实例详解  </a> <br>
<font size="3"> <br>
<a href="http://blog.csdn.net/leen0304/article/details/78804194" rel="nofollow"> Spark算子[13]：sortByKey、sortBy、二次排序 源码实例详解  </a> <br>
<font size="3"> <br>
<a href="http://blog.csdn.net/leen0304/article/details/78815021" rel="nofollow">  Spark算子[14]：top、takeOrdered 源码实例详解  </a> <br>
<font size="3"> <br>
<a href="http://blog.csdn.net/leen0304/article/details/78818743" rel="nofollow"> Spark算子[15]：sample、takeSample 源码实例详解  </a> <br>
<font size="3"> <br>
<a href="http://blog.csdn.net/leen0304/article/details/78819231" rel="nofollow">Spark算子[16]：subtractByKey、join、rightOuterJoin、leftOuterJoin 实例详解 </a> <br>
<font size="3"> <br>
<a href="http://blog.csdn.net/leen0304/article/details/78834539" rel="nofollow"> Spark算子[17]：zip、zipPartitions、zipWithIndex、zipWithUniqueId 实例详解 </a> <br>
<font size="3"> <br>
<a href="http://blog.csdn.net/leen0304/article/details/78841301" rel="nofollow"> Spark算子[18]：saveAsTextFile、saveAsObjectFile 源码实例详解  </a> <br>
<font size="3"> <br>
<a href="http://blog.csdn.net/leen0304/article/details/78854530" rel="nofollow"> Spark算子[19]：saveAsHadoopFile、saveAsNewAPIHadoopFile 源码实例详解   </a> <br>
<font size="3"> <br>
<a href="http://blog.csdn.net/leen0304/article/details/78855576" rel="nofollow">Spark算子[20]：saveAsHadoopDataset、saveAsNewAPIHadoopDataset 实例详解  </a></font></font></font></font></font></font></font></font></font></font></font></font></font></font></font></font></font></font></font></font></font></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>