---
layout:     post
title:      Hadoop学习笔记（六）（Spark + Flink + Beam）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/victorzzzz/article/details/83420782				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><span style="color:#f33b45;"><strong>spark:</strong></span>计算框架（速度，易用，通用性）<br>
                <em>   </em><u><em>Mapreduce是进程级别的，Spark是线程级别的</em></u></p>

<p><span style="color:#f33b45;"><strong>Spark生态系统：</strong></span>DBAS（Berkeley Data Analytics Stack）<br>
Mesos，HDFS，Tachyon（基于内存的文件系统），Spark（核心）<br><span style="color:#f33b45;"><strong>自框架：</strong></span>Spark Streaming，GraphX，MLib，SparkSQL<br><span style="color:#f33b45;"><strong>外部交互：</strong></span>Hive，Storm，MPI</p>

<ul><li><strong>spark可用语言：</strong>python，scala，java，R</li>
	<li><strong>spak运行模式：</strong>standalone，Yarn，Mesos，local</li>
</ul><p>-----------------------------------------------------------</p>

<p><span style="color:#f33b45;"><strong>scala安装</strong></span><br>
1）tar<br>
2）vi ~/.bash_profile<br>
    export SCALA_HOME=/home/hadoop/app/scala-2.11.8<br>
    export PATH=$SCALA_HOME/bin:$PATH<br>
3）source<br>
4）scala启动</p>

<p><strong>Spark编译过程</strong>..<br>
Spark 2.1.0，source code 下载<br>
cd bin 目录：spark启动：./spark-shell --master local[2]</p>

<p><span style="color:#f33b45;"><strong>spark实现wc：</strong></span></p>

<blockquote>
<p>val file = sc.textFile("file:///root/hello.txt")//a.collect输出<br>
val a = file.flatMap(line =&gt; line.split(" "))<br>
val b = a.map(word =&gt; (word,1))<br>
//Array((hadoop,1), (welcome,1), (hadoop,1), (hdfs,1), (mapreduce,1), (hadoop,1), (hdfs,1))</p>

<p>val c = b.reduceByKey(_ + _)<br>
//Array((mapreduce,1), (welcome,1), (hadoop,3), (hdfs,2))<br>
sc.textFile("file:///home/hadoop/data/hello.txt").flatMap(line =&gt; line.split(" ")).map(word =&gt; (word,1)).reduceByKey(_ + _).collect</p>
</blockquote>

<p><strong>监控页面：</strong>localhost:4040</p>

<p>-----------------------------------------------------------</p>

<p><strong><span style="color:#f33b45;">Flink安装、运行</span></strong><br><strong>启动：</strong>bin/start.local.sh<br>
./bin/flink run ./examples/batch/WordCount.jar \<br>
--input file:///home/hadoop/data/hello.txt --output file:///home/hadoop/tmp/flink_wc_output</p>

<p><strong>查看：</strong>localhost:8081</p>

<p>-----------------------------------------------------------</p>

<p><span style="color:#f33b45;"><strong>Beam：</strong></span>将批处理（Spark，Flink）和流处理运行在执行引擎上</p>

<p>Beam运行：<br><strong>1）#direct方式运行</strong><br>
mvn compile exec:java -Dexec.mainClass=org.apache.beam.examples.WordCount \<br>
-Dexec.args="--inputFile=/home/hadoop/data/hello.txt --output=counts" \<br>
-Pdirect-runner</p>

<p><strong>2）#spark方式运行</strong><br>
mvn compile exec:java -Dexec.mainClass=org.apache.beam.examples.WordCount \<br>
-Dexec.args="--runner=SparkRunner --inputFile=/home/hadoop/data/hello.txt --output=counts" -Pspark-runner</p>

<p><br><strong>3）#flink方式运行</strong></p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>            </div>
                </div>