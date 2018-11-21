---
layout:     post
title:      【Spark】Spark 简单介绍
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h2 id="spark官方网站">Spark官方网站</h2>

<pre class="has">
<code>http://spark.apache.org</code></pre>

<h2 id="spark通用性解释"><a name="t1"></a>Spark通用性解释</h2>

<p><img alt="Spark通用性" class="has" src="https://img-blog.csdn.net/20170925151046923?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveW91bmdiaXQwMDc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast"></p>

<h2 id="spark的特点"><a name="t2"></a>Spark的特点</h2>

<p>官方网站都有。目前支持Java,Scala, Python，R。推荐使用Java和Scala，spark2中对python的支持不够好。</p>

<h2 id="spark的数据源"><a name="t3"></a>Spark的数据源</h2>

<ul><li>HDFS，</li>
	<li>HBase，</li>
	<li>Cassandra（类似于hbase数据库，国外用的多）</li>
	<li>Hive，</li>
	<li>Tachyon（基于内存的分布式的文件系统, 阿里出的<a href="http://www.alluxio.org/" rel="nofollow">http://www.alluxio.org/</a>，比较重要）</li>
</ul><h2 id="spark的四种部署模式"><a name="t4"></a>Spark的四种部署模式</h2>

<ul><li>hadoop 模式（spark on yarn）。用yarn来管理spark的资源，也是国内用的最多的模式。</li>
	<li>Mesos模式。Mesos一个类似于yarn的资源管理器，国内用的少，国外多。</li>
	<li>Standalone模式。上面两种都不是自己管理资源，使用第三方来管理，而standalone模式是spark自己来管理资源，多内用的比较多</li>
	<li>部署到云端</li>
</ul><p> </p>

<h2 id="验证集群是否好用">验证集群是否好用</h2>

<p>验证spark集群是否可用</p>

<pre class="has">
<code>./bin/spark-submit --class org.apache.spark.examples.SparkPi --master yarn --num-executors 1 --driver-memory 500m --executor-memory 500m --executor-cores 1 lib/spark-examples-1.5.2-hadoop2.5*.jar 10

验证结果如下：
Pi is roughly 3.1411
</code></pre>

<p> </p>

<pre class="has">
<code>
用scala编写完简单的运算后，可以在localhost:4040上查看</code></pre>

<p> </p>

<p>运行spark有两种：</p>

<ol><li>本地运行</li>
</ol><pre class="has">
<code>bin/spark-submit --master local --class org.apache.spark.examples.SparkPi lib/spark-examples-1.5.2-hadoop2.6.0-cdh5.4.4.jar 5</code></pre>

<p>本地运行会直接输出结果</p>

<ol><li>分布式运行</li>
</ol><pre class="has">
<code>bin/spark-submit --master yarn-cluster --class org.apache.spark.examples.SparkPi lib/spark-examples-1.5.2-hadoop2.6.0-cdh5.4.4.jar 5</code></pre>

<p>而分布式运行的结果在</p>

<pre class="has">
<code>cd $HADOOP_HOME/logs/userlogs/</code></pre>

<p>scala 运行例子</p>

<pre class="has">
<code>val lines = sc.textFile("hdfs://master:9000/input0917/qqFriend.txt")
val lineLengths = lines.map(s =&gt; s.length)
val totalLength = lineLengths.reduce((a, b) =&gt; a + b)

输出：
totalLength: Int = 188</code></pre>

<h2 id="rdd"><a name="t6"></a>RDD</h2>

<p><img alt="sparkRDD" class="has" src="https://img-blog.csdn.net/20170925203122040?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveW91bmdiaXQwMDc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast"></p>

<h2 id="spark架构"><a name="t7"></a>Spark架构</h2>

<p><img alt="这里写图片描述" class="has" src="https://img-blog.csdn.net/20170925205008762?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveW91bmdiaXQwMDc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast"></p>

<p> </p>

<h2 id="transformation-和action-原理">transformation 和action 原理</h2>

<p>Spark支持两种RDD操作: transformation and action. <br>
transformation 会针对已有的RDD创建一个新的RDD，而action则主要对RDD进行最后的操作。transformation只是记录了对RDD的操作，并不会触发spark程序的执行，只有当transform之后接着一个action操作，那么所有的transformation才会执行。比如</p>

<pre class="has">
<code>val file=sc.textFile("hdfs://hadoop1:9000/hello.txt").flatMap(line =&gt; line.split("\t"))
回车之后并没有触发spark的执行，因为flatMap等属于transformation操作
等到file.collect()后会看到spark的执行，collect是action操作</code></pre>

<p>具体可以参考编程指南：<a href="http://spark.apache.org/docs/2.1.0/programming-guide.html" rel="nofollow">http://spark.apache.org/docs/2.1.0/programming-guide.html</a></p>

<p>那为什么要有transformation和action呢？Spark可以通过这种lazy属性，来进行底层的spark应用程序的优化，避免过多的中间结果。 </p>

<p><img alt="这里写图片描述" class="has" src="https://img-blog.csdn.net/20170927133726180?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveW91bmdiaXQwMDc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast"></p>

<p> </p>

<h2 id="宽依赖和窄依赖">宽依赖和窄依赖</h2>

<p>窄依赖，父RDD的每个分区只被一个子RDD的分区依赖。 <br>
宽依赖，父RDD的分区被多个子RDD的分区所依赖。 <br><img alt="这里写图片描述" class="has" src="https://img-blog.csdn.net/20171011204035205?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveW91bmdiaXQwMDc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast"></p>

<h2 id="shuffle的原理"><a name="t20"></a>shuffle的原理</h2>

<p><img alt="这里写图片描述" class="has" src="https://img-blog.csdn.net/20171017164108180?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveW91bmdiaXQwMDc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast"></p>

<p>本例中前面三个RDD都不涉及到其他的节点，但是真实环境是需要其他节点的参与的。shuffle阶段需要大量的磁盘IO，序列化与反序列化，网络数据的传输，所以spark很大的性能损耗都在shuffle上，所以有必要进行调优。</p>

<p>转自：<a href="https://blog.csdn.net/youngbit007/article/details/78085427" rel="nofollow">https://blog.csdn.net/youngbit007/article/details/78085427</a></p>

<p> </p>            </div>
                </div>