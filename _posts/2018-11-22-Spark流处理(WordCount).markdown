---
layout:     post
title:      Spark流处理(WordCount)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1>Spark流处理(WordCount)</h1>
<h2>1.     Spark环境</h2>
<p>1)        4台CentOs 6.4</p>
<p>2)        Hadoop 2.2.0</p>
<p>3)        Jdk 1.7</p>
<p>4)        Scala 2.10.3</p>
<p>5)        Spark 0.9.0</p>
<h2>2.     前期准备</h2>
<p>由于Spark支持Socket流输入，准备NetCat作为TCP服务器不断对Spark进行word输入。</p>
<h3>2.1  NetCat安装</h3>
<p>1)        yum install -y nc</p>
<h3>2.2  启动监听服务</h3>
<p>1)        nc -l 9999   9999是端口号</p>
<h2>3 启动Spark</h2>
<p>输入：</p>
<p>/usr/local/spark/bin/run-exampleorg.apache.spark.streaming.exampl.NetworkWordCount local[2] 192.168.178.1829999</p>
<p>解释：192.168.178.182是netcat服务器的IP地址，9999是netcat服务器的端口号。</p>
<h2>4 结果</h2>
<h3>4.1 netcat端输入</h3>
<div><img src="https://img-blog.csdn.net/20140320195406062?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvenp6enpxZg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><img src="" alt=""></div>
<h3>4.2 Spark中输出</h3>
<div><img src="https://img-blog.csdn.net/20140320195414843?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvenp6enpxZg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><img src="https://img-blog.csdn.net/20140320195422578?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvenp6enpxZg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></div>
<div><img src="" alt=""><br></div>
<div><img src="" alt=""><br></div>
<p align="center"></p>
<p> </p>
            </div>
                </div>