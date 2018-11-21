---
layout:     post
title:      spark-submit提交kafka测试
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/Rosen_Luo/article/details/47394503				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;line-height:20px;">
spark-submit --jars /home/aus/spark/lib/spark-streaming-kafka_2.10-1.0.0.jar,/home/aus/spark/lib/kafka_2.8.0-0.8.1.jar --master <a href="" rel="nofollow" style="color:rgb(50,108,166);text-decoration:none;">spark://10.21.17.202:7074</a> --class
 KafkaWordCount /home/aus/spark/data/SparkTest.jar</p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;line-height:20px;">
10.21.17.202上测试spark程序：</p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;line-height:20px;">
<img src="https://img-blog.csdn.net/20150810101100556" alt=""><br></p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;line-height:20px;">
接kafka 需要引入两个jar 一个是spark-streaming-kafka_2.10-1.2.0.jar 还有一个是kafka_2.8.0-0.8.1.jar</p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;line-height:20px;">
<span style="color:rgb(255,0,0);">方法一：–jars方式引入 （我用的这个方法成功）</span></p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;line-height:20px;">
<span style="color:rgb(0,0,0);">方法二：（待验证）</span></p>
<div style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;line-height:20px;">
 把如下包放到一个目录下，然后在spark-default.conf下加如：<br>
spark.jars                            /opt/spark-1.3.0/libs/kafka_2.10-0.8.1.1.jar,/opt/spark-1.3.0/libs/spark-streaming-kafka_2.10-1.3.0.jar,/opt/spark-1.3.0/libs/jopt-simple-3.2.jar,/opt/spark-1.3.0/libs/zkclient-0.3.jar,/opt/spark-1.3.0/libs/metrics-annotation-2.2.0.jar,/opt/spark-1.3.0/libs/metrics-core-2.2.0.jar </div>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;line-height:20px;">
<span style="color:rgb(255,0,0);">注意：</span></p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;line-height:20px;">
<span style="color:rgb(255,0,0);">运行时发现java.lang.NoClassDefFoundError: scala/Tuple2$mcLL$sp，原来是scala的版本跟kafka的版本不一致所导致。</span></p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;line-height:20px;">
<span style="color:rgb(255,0,0);">kafka官网对应表说明如下：</span></p>
<p style="color:rgb(51,51,51);font-family:Arial, sans-serif;font-size:14px;line-height:20px;">
<img src="https://img-blog.csdn.net/20150810101145369" alt=""><br></p>
            </div>
                </div>