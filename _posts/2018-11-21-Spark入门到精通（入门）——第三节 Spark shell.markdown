---
layout:     post
title:      Spark入门到精通（入门）——第三节 Spark shell
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：所有内容原创，如需转载，请声明					https://blog.csdn.net/lovedopa/article/details/82944822				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p id="main-toc"><strong>目录</strong></p>

<p id="%E5%89%8D%E8%A8%80%EF%BC%9A-toc" style="margin-left:0px;"><a href="#%E5%89%8D%E8%A8%80%EF%BC%9A" rel="nofollow">前言：</a></p>

<p style="margin-left:0px;">本章学习如何和spark使用命令行交互 - spark shell</p>

<hr id="hr-toc"><h1>一  执行spark任务</h1>

<h2>1.1 执行第一个spark程序</h2>

<blockquote>
<p>/opt/module/spark/bin/spark-submit \</p>

<p>--class org.apache.spark.examples.SparkPi \</p>

<p>--master spark://linux102:7077 \</p>

<p>--executor-memory 1G \</p>

<p>--total-executor-cores 2 \</p>

<p>/opt/module/spark/examples/jars/spark-examples_2.11-2.1.1.jar \</p>

<p>100</p>

<p>参数说明：</p>

<p>--master spark://linux102:7077 \ 指定Master的地址</p>

<p>--executor-memory 1G 指定每个executor可用内存为1G</p>

<p>--total-executor-cores 2 指定每个executor使用的cup核数为2个</p>

<p>该算法是利用蒙特·卡罗算法求PI</p>
</blockquote>

<h2>1.2 使用spark - shell</h2>

<h3>1）集群模式</h3>

<blockquote>
<p>/opt/module/spark/bin/spark-shell \<br>
--master spark://linux102:7077 \<br>
--executor-memory 2g \<br>
--total-executor-cores 2</p>
</blockquote>

<p>注意：<br>
如果启动spark shell时没有指定master地址，但是也可以正常启动spark shell和执行spark shell中的程序，其实是启动了spark的local模式，该模式仅在本机启动一个进程，没有与集群建立联系。</p>

<p>Spark Shell中已经默认将SparkContext类初始化为对象sc。用户代码如果需要用到，则直接应用sc即可<br>
 </p>

<p>2)测试</p>

<p>我这里使用单词统计来做1个最简单的测试</p>

<blockquote>
<p>新建hello.txt</p>

<p>hello world<br>
linux linux<br>
hadoop <br>
spark<br>
hello world<br>
linux linux<br>
hadoop <br>
spark<br>
hello world<br>
linux linux<br>
hadoop <br>
spark</p>
</blockquote>

<p>上传到linux集群</p>

<p><img alt="" class="has" height="75" src="https://img-blog.csdn.net/20181006131629744?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xvdmVkb3Bh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="985"></p>

<h3>使用scala编写代码-只是展示</h3>

<p>sc.textFile("hdfs://linux102:9001/hello.txt").flatMap(_.split(" ")).map((_,1)).reduceByKey(_+_).collect</p>

<p>结果展示</p>

<p><img alt="" class="has" height="138" src="https://img-blog.csdn.net/20181006131959952?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xvdmVkb3Bh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1200"></p>

<h3>使用scala编写代码-结果保存到hdfs上</h3>

<p style="margin-left:0cm;"><span style="color:#333333;">sc.textFile("</span>hdfs://linux102:9001/hello.txt<span style="color:#333333;">").flatMap(_.split(" ")).</span></p>

<p style="margin-left:0cm;"><span style="color:#333333;">map((_,1)).reduceByKey(_+_).saveAsTextFile("</span>hdfs://linux102:9001/out<span style="color:#333333;">")</span></p>

<p style="margin-left:0cm;"><span style="color:#333333;">查看结果</span></p>

<p style="margin-left:0cm;"><span style="color:#333333;">1 文件夹</span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="204" src="https://img-blog.csdn.net/20181006132526858?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xvdmVkb3Bh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1200"></p>

<p>2 结果</p>

<p><img alt="" class="has" height="350" src="https://img-blog.csdn.net/20181006132630193?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xvdmVkb3Bh/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1200"></p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>            </div>
                </div>