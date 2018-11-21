---
layout:     post
title:      spark-shell初体验
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2>1、复制文件至HDFS：</h2>
<div>hadoop@Mhadoop:/usr/local/hadoop$ bin/hdfs dfs -mkdir /user<br>
hadoop@Mhadoop:/usr/local/hadoop$ bin/hdfs dfs -mkdir /user/hadoop<br>
hadoop@Mhadoop:/usr/local/hadoop$ bin/hdfs dfs -copyFromLocal /usr/local/spark/spark-1.3.1-bin-hadoop2.4/README.md /user/hadoop/<br><br></div>
<h2>2、运行spark-shell</h2>
<div><img src="https://img-blog.csdn.net/20150502133453877?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWlqaWNoYW5na29uZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<h2>3、读取文件统计spark这个词出现次数</h2>
<div>scala&gt; sc<br>
res0: org.apache.spark.SparkContext = org.apache.spark.SparkContext@472ac3d3<br><br>
scala&gt; val file = sc.textFile("hdfs://Mhadoop:9000/user/hadoop/README.md")</div>
<div>file: org.apache.spark.rdd.RDD[String] = hdfs://Mhadoop:9000/user/hadoop/README.md MapPartitionsRDD[1] at textFile at &lt;console&gt;:21<br></div>
<div><br></div>
<div><span style="font-size:18px;">file变量是一个MapPartitionsRDD；接着过滤spark这个词</span></div>
<div><span style="font-size:18px;"><br></span></div>
<div>scala&gt; val sparks = file.filter(line =&gt; line.contains("spark"))<br>
sparks: org.apache.spark.rdd.RDD[String] = MapPartitionsRDD[2] at filter at &lt;console&gt;:23<br></div>
<div><br></div>
<div><span style="font-size:18px;">统计spark出现次数，结果为11：</span></div>
<div>scala&gt; sparks.count</div>
<div><img src="https://img-blog.csdn.net/20150502134031227?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWlqaWNoYW5na29uZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""></div>
<div><span style="font-size:18px;">另开一个terminal用ubuntu自带的wc命令验证下：</span></div>
<div><span style="font-size:18px;"><br></span></div>
<div>hadoop@Mhadoop:/usr/local/spark/spark-1.3.1-bin-hadoop2.4$ grep spark README.md|wc<br>
     <span style="background-color:rgb(153,255,153);">11 </span>     50     761<br></div>
<h2><br><span style="font-size:18px;">4、执行spark cache看下效率提升</span></h2>
<div>scala&gt; sparks.cache<br>
res3: sparks.type = MapPartitionsRDD[2] at filter at &lt;console&gt;:23</div>
<div><br><span style="font-size:18px;">登录控制台：</span><u>http://192.168.85.10:4040/stages/</u></div>
<div><u><img src="https://img-blog.csdn.net/20150502134747855?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWlqaWNoYW5na29uZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></u></div>
<div><span style="font-size:18px;">可见cache之后，耗时从s变为ms,性能提升明显。</span></div>
            </div>
                </div>