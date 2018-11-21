---
layout:     post
title:      Spark算子：RDD行动Action操作(3)–aggregate、fold、lookup
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：如需转载请留言，谢谢，遇到问题可以留言					https://blog.csdn.net/qq_36764089/article/details/81276819				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1><a href="http://lxw1234.com/archives/2015/07/394.htm" rel="nofollow">Spark算子：RDD行动Action操作(3)–aggregate、fold、lookup</a></h1>

<p> </p>

<h1>关键字：Spark算子、Spark函数、Spark RDD行动Action、aggregate、fold、lookup<br>
aggregate</h1>

<p>def aggregate[U](zeroValue: U)(seqOp: (U, T) ⇒ U, combOp: (U, U) ⇒ U)(implicit arg0: ClassTag[U]): U</p>

<p>aggregate用户聚合RDD中的元素，先使用seqOp将RDD中每个分区中的T类型元素聚合成U类型，再使用combOp将之前每个分区聚合后的U类型聚合成U类型，特别注意seqOp和combOp都会使用zeroValue的值，zeroValue的类型为U。</p>

<p> </p>

<p>  <strong>  var rdd1 = sc.makeRDD(1 to 10,2)</strong><br>
   <strong> rdd1.mapPartitionsWithIndex{<br>
            (partIdx,iter) =&gt; {<br>
              var part_map = scala.collection.mutable.Map[String,List[Int]]()<br>
                while(iter.hasNext){<br>
                  var part_name = "part_" + partIdx;<br>
                  var elem = iter.next()<br>
                  if(part_map.contains(part_name)) {<br>
                    var elems = part_map(part_name)<br>
                    elems ::= elem<br>
                    part_map(part_name) = elems<br>
                  } else {<br>
                    part_map(part_name) = List[Int]{elem}<br>
                  }<br>
                }<br>
                part_map.iterator</strong><br>
               <br>
     <strong>       }<br>
          }.collect</strong><br>
    res16: Array[(String, List[Int])] = Array((part_0,List(5, 4, 3, 2, 1)), (part_1,List(10, 9, 8, 7, 6)))<br>
     </p>

<p>##第一个分区中包含5,4,3,2,1</p>

<p>##第二个分区中包含10,9,8,7,6</p>

<p>    scala&gt; <strong>rdd1.aggregate(1)(<br>
         |           {(x : Int,y : Int) =&gt; x + y},<br>
         |           {(a : Int,b : Int) =&gt; a + b}<br>
         |     )</strong><br>
    res17: Int = 58<br>
     </p>

<p>结果为什么是58，看下面的计算过程：</p>

<p>##先在每个分区中迭代执行 (x : Int,y : Int) =&gt; x + y 并且使用zeroValue的值1</p>

<p>##即：part_0中 zeroValue+5+4+3+2+1 = 1+5+4+3+2+1 = 16</p>

<p>## part_1中 zeroValue+10+9+8+7+6 = 1+10+9+8+7+6 = 41</p>

<p>##再将两个分区的结果合并(a : Int,b : Int) =&gt; a + b ，并且使用zeroValue的值1</p>

<p>##即：zeroValue+part_0+part_1 = 1 + 16 + 41 = 58</p>

<p>再比如：</p>

<p>    <strong>scala&gt; rdd1.aggregate(2)(<br>
         |           {(x : Int,y : Int) =&gt; x + y},<br>
         |           {(a : Int,b : Int) =&gt; a * b}<br>
         |     )</strong><br>
    res18: Int = 1428<br>
     </p>

<p>##这次zeroValue=2</p>

<p>##part_0中 zeroValue+5+4+3+2+1 = 2+5+4+3+2+1 = 17</p>

<p>##part_1中 zeroValue+10+9+8+7+6 = 2+10+9+8+7+6 = 42</p>

<p>##最后：zeroValue*part_0*part_1 = 2 * 17 * 42 = 1428</p>

<p>因此，zeroValue即确定了U的类型，也会对结果产生至关重要的影响，使用时候要特别注意。</p>

<h1> <br>
fold</h1>

<p>def fold(zeroValue: T)(op: (T, T) ⇒ T): T</p>

<p>fold是aggregate的简化，将aggregate中的seqOp和combOp使用同一个函数op。</p>

<p>    scala&gt; <strong>rdd1.fold(1)(<br>
         |       (x,y) =&gt; x + y    <br>
         |     )</strong><br>
    res19: Int = 58<br>
     <br>
    ##结果同上面使用aggregate的第一个例子一样，即：<br>
    scala&gt;<strong> rdd1.aggregate(1)(<br>
         |           {(x,y) =&gt; x + y},<br>
         |           {(a,b) =&gt; a + b}<br>
         |     )</strong><br>
    res20: Int = 58<br>
     </p>

<h1>lookup</h1>

<p>def lookup(key: K): Seq[V]</p>

<p>lookup用于(K,V)类型的RDD,指定K值，返回RDD中该K对应的所有V值。</p>

<p> </p>

<p>    scala&gt;<strong> var rdd1 = sc.makeRDD(Array(("A",0),("A",2),("B",1),("B",2),("C",1)))</strong><br>
    rdd1: org.apache.spark.rdd.RDD[(String, Int)] = ParallelCollectionRDD[0] at makeRDD at :21<br>
     <br>
    scala&gt;<strong> rdd1.lookup("A")</strong><br>
    res0: Seq[Int] = WrappedArray(0, 2)<br>
     <br>
    scala&gt; rdd1.lookup("B")<br>
    res1: Seq[Int] = WrappedArray(1, 2)<br>
    </p>            </div>
                </div>