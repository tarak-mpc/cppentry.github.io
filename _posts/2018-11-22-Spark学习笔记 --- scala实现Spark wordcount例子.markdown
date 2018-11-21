---
layout:     post
title:      Spark学习笔记 --- scala实现Spark wordcount例子
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：学习交流为主，未经博主同意禁止转载，禁止用于商用。					https://blog.csdn.net/u012965373/article/details/60782851				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<pre><code class="language-java">package wordcount.WordCount
import org.apache.spark.SparkConf
import org.apache.spark.SparkContext

/**
  * Created by Ryan on 2017/3/7.
  */
object scala {
  def main(args: Array[String]) : Unit = {
    val conf = new SparkConf()
    conf.setAppName("WordCountApplication")
    conf.setMaster("local")
    val sc = new SparkContext(conf)
    val lines = sc.textFile("/usr/softwares/spark/ReadMe.md", 3)
    val words = lines.flatMap{line =&gt; line.split(" ")}
    val pairs = words.map(words =&gt; (words, 1))
    val wordCounts = pairs.reduceByKey(_+_)
    wordCounts.foreach(wordNumberPair =&gt; println(wordNumberPair._1 + " : " + wordNumberPair._2))
    sc.stop()
  }
}
</code></pre><br>            </div>
                </div>