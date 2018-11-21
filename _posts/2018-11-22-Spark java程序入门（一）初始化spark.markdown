---
layout:     post
title:      Spark java程序入门（一）初始化spark
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/h_e_l_l_o_h_i/article/details/52829340				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>把提交spark独立应用时需要初始化spark，告知spark要运行的应用的名字，运行方式，jar包的主类等等</p>
<p>一、在自己的独立应用中使用spark的方法<br></p>
<p><span></span>      在代码中初始化spark：</p>
<p><span></span>      SparkConf conf = new SparkConf();   //新建SparkConf对象,通过设置键值对的方式配置spark的运行（具体见下方参数设置方法）</p>
<p>      JavaSparkContext sc = new JavaSparkContext(conf);  <span></span>//用于联系spark集群，建立RDD，累加器，广播变量等</p>
<p><span></span>       之后就可以利用SparkContext对象建立RDD，开始RDD编程啦<span></span></p>
<p><br></p>
<p>二、初始化参数的方式有两种：</p>
<p><span></span>       （1）在程序中调用SparkConf对象的方法直接设置，如本地多线程运行wordcount程序，则可用</p>
<p><span></span>                 SparkConf conf  = new SparkConf().setMaster("local[*]").setAppName("wordcount");</p>
<p><span></span>       （2）在用spark-submit提交程序时直接设置也可以</p>
<p><span></span>                  spark-submit  --master：local[*]  --class:Word  wordcount.jar</p>
<p><br></p>
<p><br></p>
            </div>
                </div>