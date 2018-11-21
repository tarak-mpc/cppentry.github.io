---
layout:     post
title:      Spark 从入门到放弃 之 quick start
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许最好别转载，你非要转我也没办法。 ╮(╯▽╰)╭					https://blog.csdn.net/u011478909/article/details/52267522				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:24px;">使用spark shell进行交互式分析</span></p>
<p><span style="font-size:18px;">上传一个文件到hdfs上的如下目录/user/hdfs/</span><br></p>
<pre><code class="language-plain">-bash-4.1$ hadoop fs -put README.md /user/hdfs/

-bash-4.1$ hadoop fs -ls /user/hdfs
Found 3 items
drwxr-xr-x   - hdfs supergroup          0 2016-08-21 15:34 /user/hdfs/.sparkStaging
drwx------   - hdfs supergroup          0 2016-08-20 13:12 /user/hdfs/.staging
-rw-r--r--   3 hdfs supergroup       3233 2016-08-21 15:36 /user/hdfs/README.md


</code></pre>
<p><span style="font-size:18px;">然后进入spark-shell命令行交互界面</span></p>
<p></p><pre><code class="language-plain">[root@hadoop01 ~]# su - hdfs
-bash-4.1$ spark-shell 
Setting default log level to "WARN".
To adjust logging level use sc.setLogLevel(newLevel).
Welcome to
      ____              __
     / __/__  ___ _____/ /__
    _\ \/ _ \/ _ `/ __/  '_/
   /___/ .__/\_,_/_/ /_/\_\   version 1.5.0-cdh5.5.4
      /_/

Using Scala version 2.10.4 (Java HotSpot(TM) 64-Bit Server VM, Java 1.7.0_79)
Type in expressions to have them evaluated.
Type :help for more information.
16/08/21 15:34:25 WARN MetricsSystem: Using default name DAGScheduler for source because spark.app.id is not set.
Spark context available as sc (master = yarn-client, app id = application_1471668978254_0006).
SQL context available as sqlContext.</code></pre>
<span style="font-size:18px;">计算文件行数</span><br><pre><code class="language-plain">scala&gt; val textFile = sc.textFile("README.md")
textFile: org.apache.spark.rdd.RDD[String] = MapPartitionsRDD[3] at textFile at &lt;console&gt;:21

scala&gt; textFile.count()
res1: Long = 99   </code></pre>
<p><span style="font-size:18px;">输出文件第一行内容</span></p>
<p></p><pre><code class="language-plain">scala&gt; textFile.first()
res2: String = # Highlight.js</code></pre><br><span style="font-size:18px;">计算包含某字符的行数</span>
<p></p><pre><code class="language-plain">scala&gt; textFile.filter(line =&gt; line.contains("##")).count
res3: Long = 5 </code></pre><br><span style="font-size:18px;">接下来我们造一个文件来做词频统计用</span>
<p><span style="font-size:18px;">文件内容如下：</span></p>
<p></p><pre><code class="language-plain">-bash-4.1$ cat test.txt
张三 张四
张三 张五
李三 李三
李四 李四
李四 王二
老王 老王</code></pre>
<p><span style="font-size:18px;">上传文件</span></p>
<p></p><pre><code class="language-plain">-bash-4.1$ hadoop fs -put test.txt /user/hdfs/</code></pre><span style="font-size:18px;">做词频统计</span>
<p></p><pre><code class="language-plain">scala&gt; val wc = sc.textFile("test.txt")
wc: org.apache.spark.rdd.RDD[String] = MapPartitionsRDD[6] at textFile at &lt;console&gt;:21
</code></pre><span style="font-size:18px;">进行map reduce</span><br><pre><code class="language-plain">scala&gt; val result = wc.flatMap(line =&gt; line.split(" ")).map(word =&gt; (word, 1)).reduceByKey((a, b) =&gt; a + b)
result: org.apache.spark.rdd.RDD[(String, Int)] = ShuffledRDD[9] at reduceByKey at &lt;console&gt;:23</code></pre><pre><code class="language-plain">scala&gt; result.collect()
res5: Array[(String, Int)] = Array((张五,1), (老王,2), (张三,2), (张四,1), (王二,1), (李四,3), (李三,2))
</code></pre><br><br><br><br><p></p>
<p><br></p>
            </div>
                </div>