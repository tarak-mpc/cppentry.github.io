---
layout:     post
title:      spark学习系列——2 WordCount
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/FisherWang_CN/article/details/79630148				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>经典的例子</p><p>首先上传文件到hdfs，再启动spark-shell，进行计算</p><pre><code class="language-plain">[bdpos@BJHC-Client-18562 spark]$ hdfs dfs -mkdir /spark/input
[bdpos@BJHC-Client-18562 spark]$ hdfs dfs -put ./README.md /spark/input
[bdpos@BJHC-Client-18562 spark]$ hdfs dfs -ls /spark/input
Found 1 items
-rw-r--r--   2 bdpos supergroup       3818 2018-03-20 19:07 /spark/input/README.md</code></pre><pre><code class="language-java">scala&gt; sc.textFile("/spark/input/README.md").flatMap(line =&gt; line.split(" ")).map(word =&gt; (word,1)).reduceByKey(_+_).sortBy(t=&gt;t._2,false).take(10)
res9: Array[(String, Int)] = Array(("",71), (the,24), (to,17), (Spark,16), (for,12), (and,9), (a,8), (##,8), (run,7), (on,7))</code></pre><br><br><br>            </div>
                </div>