---
layout:     post
title:      Spark算子：RDD行动Action操作(4)–countByKey、foreach、foreachPartition、sortBy
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：如需转载请留言，谢谢，遇到问题可以留言					https://blog.csdn.net/qq_36764089/article/details/81276848				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1><a href="http://lxw1234.com/archives/2015/07/399.htm" rel="nofollow">Spark算子：RDD行动Action操作(4)–countByKey、foreach、foreachPartition、sortBy</a></h1>

<p> </p>

<h1>关键字：Spark算子、Spark函数、Spark RDD行动Action、countByKey、foreach、foreachPartition、sortBy<br>
countByKey</h1>

<p>def countByKey(): Map[K, Long]</p>

<p>countByKey用于统计RDD[K,V]中每个K的数量。</p>

<p>    scala&gt; <strong>var rdd1 = sc.makeRDD(Array(("A",0),("A",2),("B",1),("B",2),("B",3)))</strong><br>
    rdd1: org.apache.spark.rdd.RDD[(String, Int)] = ParallelCollectionRDD[7] at makeRDD at :21<br>
     <br>
    scala&gt;<strong> rdd1.countByKey</strong><br>
    res5: scala.collection.Map[String,Long] = Map(A -&gt; 2, B -&gt; 3)<br>
     </p>

<h1>foreach</h1>

<p>def foreach(f: (T) ⇒ Unit): Unit</p>

<p>foreach用于遍历RDD,将函数f应用于每一个元素。</p>

<p>但要注意，如果对RDD执行foreach，只会在Executor端有效，而并不是Driver端。</p>

<p>比如：rdd.foreach(println)，只会在Executor的stdout中打印出来，Driver端是看不到的。</p>

<p>我在Spark1.4中是这样，不知道是否真如此。</p>

<p> </p>

<p>这时候，使用accumulator共享变量与foreach结合，倒是个不错的选择。</p>

<p>    scala&gt;<strong> var cnt = sc.accumulator(0)</strong><br>
    cnt: org.apache.spark.Accumulator[Int] = 0<br>
     <br>
    scala&gt;<strong> var rdd1 = sc.makeRDD(1 to 10,2)</strong><br>
    rdd1: org.apache.spark.rdd.RDD[Int] = ParallelCollectionRDD[5] at makeRDD at :21<br>
     <br>
    scala&gt; <strong>rdd1.foreach(x =&gt; cnt += x)</strong><br>
     <br>
    scala&gt; <strong>cnt.value</strong><br>
    res51: Int = 55<br>
     <br>
    scala&gt;<strong> rdd1.collect.foreach(println)</strong><br>
    1<br>
    2<br>
    3<br>
    4<br>
    5<br>
    6<br>
    7<br>
    8<br>
    9<br>
    10<br>
     </p>

<h1>foreachPartition</h1>

<p>def foreachPartition(f: (Iterator[T]) ⇒ Unit): Unit</p>

<p>foreachPartition和foreach类似，只不过是对每一个分区使用f。</p>

<p>    scala&gt; <strong>var rdd1 = sc.makeRDD(1 to 10,2)</strong><br>
    rdd1: org.apache.spark.rdd.RDD[Int] = ParallelCollectionRDD[5] at makeRDD at :21<br>
     <br>
    scala&gt; <strong>var allsize = sc.accumulator(0)</strong><br>
    size: org.apache.spark.Accumulator[Int] = 0<br>
     <br>
    scala&gt; <strong>var rdd1 = sc.makeRDD(1 to 10,2)</strong><br>
    rdd1: org.apache.spark.rdd.RDD[Int] = ParallelCollectionRDD[6] at makeRDD at :21<br>
     <br>
    scala&gt;    <strong> rdd1.foreachPartition { x =&gt; {<br>
         |       allsize += x.size<br>
         |     }}</strong><br>
     <br>
    scala&gt; <strong>println(allsize.value)</strong><br>
    10<br>
     </p>

<h1>sortBy</h1>

<p>def sortBy[K](f: (T) ⇒ K, ascending: Boolean = true, numPartitions: Int = this.partitions.length)(implicit ord: Ordering[K], ctag: ClassTag[K]): RDD[T]</p>

<p>sortBy根据给定的排序k函数将RDD中的元素进行排序。</p>

<p>    scala&gt; <strong>var rdd1 = sc.makeRDD(Seq(3,6,7,1,2,0),2)</strong><br>
     <br>
    scala&gt; <strong>rdd1.sortBy(x =&gt; x).collect</strong><br>
    res1: Array[Int] = Array(0, 1, 2, 3, 6, 7) //默认升序<br>
     <br>
    scala&gt; <strong>rdd1.sortBy(x =&gt; x,false).collect</strong><br>
    res2: Array[Int] = Array(7, 6, 3, 2, 1, 0)  //降序<br>
     <br>
    //RDD[K,V]类型<br>
    scala&gt;<strong>var rdd1 = sc.makeRDD(Array(("A",2),("A",1),("B",6),("B",3),("B",7)))</strong><br>
     <br>
    scala&gt; <strong>rdd1.sortBy(x =&gt; x).collect</strong><br>
    res3: Array[(String, Int)] = Array((A,1), (A,2), (B,3), (B,6), (B,7))<br>
     <br>
    //按照V进行降序排序<br>
    scala&gt; <strong>rdd1.sortBy(x =&gt; x._2,false).collect</strong><br>
    res4: Array[(String, Int)] = Array((B,7), (B,6), (B,3), (A,2), (A,1))<br>
    </p>            </div>
                </div>