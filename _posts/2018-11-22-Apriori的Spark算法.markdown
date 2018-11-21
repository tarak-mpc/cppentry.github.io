---
layout:     post
title:      Apriori的Spark算法
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/gdp12315/article/details/50589930				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p> </p>
<h3>2014届全国高校云计算大赛技能赛</h3>
<p> </p>
<p><strong>K-频繁项集挖掘并行化算法</strong><br>
 环境描述： 本题目需要运行在 Apache Spark 1.0.1Apache Spark 1.0.1Apache Spark 1.0.1 Apache Spark 1.0.1Apache Spark 1.0.1 Apache Spark 1.0.1 Apache Spark 1.0.1Apache Spark 1.0.1Apache Spark 1.0.1Apache Spark 1.0.1 环境下， 使用 Java JavaJava或者 ScalaScala 进行编程开发 。<br>
 题目描述： 在规定的 ChessChessChess 标准数据集上，规定 标准数据集上，规定 K = 8，支持度 ，支持度 support = 85% support = 85% support = 85% support = 85%support = 85%， 进行 1-频繁项集到 K-频繁项集的挖掘。<br>
 数据集： 本题目将 采用 ChessChess 标准 数据集 apriori_data apriori_dataapriori_data apriori_data apriori_data apriori_data，具体下载地址见大赛网 站
<a href="http://cloud.seu.edu.cn" rel="nofollow">http://cloud.seu.edu.cn</a> </p>
<p> 程序设计约束： 程序需要两个输入参数，第一为据集路径二出 文件 夹路径。 1-频繁项集到 K-频繁项集的结果放在 K个文件中，名分别为 个文件中，名分别为 resultresult -1,result1,result1,result1,result -2, …,result,result,result -8(K=8)8(K=8) 8(K=8)8(K=8)，每个 文件的格式为： 文件的格式为：</p>
<p>    </p>
<pre><code class="language-plain">a,b,c:0.85
a,b,d:0.90</code></pre>
<p><br>
    项集和支持度用西文冒号 (:) (:)分割，项集 分割，项集 中如果有多个元素则用西文逗号分割 中如果有多个元素则用西文逗号分割 (,) 。</p>
<p> </p>
<pre><code class="language-plain">import scala.util.control.Breaks._
import scala.collection.mutable.ArrayBuffer
import java.util.BitSet
import org.apache.spark.SparkContext
import org.apache.spark.SparkContext._
import org.apache.spark._


object FrequentItemset {
  def main(args: Array[String]) {
    if (args.length != 2) {
      println("USage:&lt;Datapath&gt; &lt;Output&gt;")
    }
    //initial SparkContext
    val sc = new SparkContext()
    val SUPPORT_NUM = 15278611 //Transactions total is num=17974836, SUPPORT_NUM = num*0.85
    val TRANSACITON_NUM = 17974836.0
    val K = 8


    //All transactions after removing transaction ID, and here we combine the same transactions.
    val transactions = sc.textFile(args(0)).map(line =&gt;
      line.substring(line.indexOf(" ") + 1).trim).map((_, 1)).reduceByKey(_ + _).map(line =&gt; {
      val bitSet = new BitSet()
      val ss = line._1.split(" ")
      for (i &lt;- 0 until ss.length) {
        bitSet.set(ss(i).toInt, true)
      }
      (bitSet, line._2)
    }).cache()


    //To get 1 frequent itemset, here, fi represents frequent itemset
    var fi = transactions.flatMap { line =&gt;
      val tmp = new ArrayBuffer[(String, Int)]
      for (i &lt;- 0 until line._1.size()) {
        if (line._1.get(i)) tmp += ((i.toString, line._2))
      }
      tmp
    }.reduceByKey(_ + _).filter(line1 =&gt; line1._2 &gt;= SUPPORT_NUM).cache()
    val result = fi.map(line =&gt; line._1 + ":" + line._2 / TRANSACITON_NUM)
    result.saveAsTextFile(args(1) + "/result-1")


    for (i &lt;- 2 to K) {
      val candiateFI = getCandiateFI(fi.map(_._1).collect(), i)
      val bccFI = sc.broadcast(candiateFI)
      //To get the final frequent itemset
      fi = transactions.flatMap { line =&gt;
        val tmp = new ArrayBuffer[(String, Int)]()
        //To check if each itemset of candiateFI in transactions
        bccFI.value.foreach { itemset =&gt;
          val itemArray = itemset.split(",")
          var count = 0
          for (item &lt;- itemArray) if (line._1.get(item.toInt)) count += 1
          if (count == itemArray.size) tmp += ((itemset, line._2))
        }
        tmp
      }.reduceByKey(_ + _).filter(_._2 &gt;= SUPPORT_NUM).cache()
      val result = fi.map(line =&gt; line._1 + ":" + line._2 / TRANSACITON_NUM)
      result.saveAsTextFile(args(1) + "/result-" + i)
      bccFI.unpersist()
    }
  }


  //To get the candiate k frequent itemset from k-1 frequent itemset
  def getCandiateFI(f: Array[String], tag: Int) = {
    val separator = ","
    val arrayBuffer = ArrayBuffer[String]()
    for(i &lt;- 0 until f.length;j &lt;- i + 1 until f.length){
      var tmp = ""
      if(2 == tag) tmp = (f(i) + "," + f(j)).split(",").sortWith((a,b) =&gt; a.toInt &lt;= b.toInt).reduce(_+","+_)
      else {
        if (f(i).substring(0, f(i).lastIndexOf(',')).equals(f(j).substring(0, f(j).lastIndexOf(',')))) {
          tmp = (f(i) + f(j).substring(f(j).lastIndexOf(','))).split(",").sortWith((a, b) =&gt; a.toInt &lt;= b.toInt).reduce(_ + "," + _)
        }
      }
      var hasInfrequentSubItem = false //To filter the item which has infrequent subitem
      if (!tmp.equals("")) {
        val arrayTmp = tmp.split(separator)
        breakable {
          for (i &lt;- 0 until arrayTmp.size) {
            var subItem = ""
            for (j &lt;- 0 until arrayTmp.size) {
              if (j != i) subItem += arrayTmp(j) + separator
            }
            //To remove the separator "," in the end of the item
            subItem = subItem.substring(0, subItem.lastIndexOf(separator))
            if (!f.contains(subItem)) {
              hasInfrequentSubItem = true
              break
            }
          }
        } //breakable
      }
      else hasInfrequentSubItem = true
      //If itemset has no sub inftequent itemset, then put it into candiateFI
      if (!hasInfrequentSubItem) arrayBuffer += (tmp)
    } //for
    arrayBuffer.toArray
  }
}</code></pre>
<p><br>
 </p>
            </div>
                </div>