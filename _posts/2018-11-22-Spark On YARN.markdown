---
layout:     post
title:      Spark On YARN
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_22027637/article/details/78903960				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2>Spark On YARN概述</h2>
<div>
<ul><li>Spark支持可插拔的集群管理模式                                                                                                                                                                                                                       在Spark中，支持4种运行模式：<br>
           1）Local：开发时使用<br>
           2）Standalone： 是Spark自带的，如果一个集群是Standalone的话，那么就需要在多台机器上同时部署Spark环境<br>
           3）YARN：建议大家在生产上使用该模式，统一使用YARN进行整个集群作业(MR、Spark)的资源调度<br><p>           4）Mesos</p>
<p>不管使用什么模式，Spark应用程序的代码是一模一样的，只需要在提交的时候通过--master参数来指定我们的运行模式即可</p>
</li><li>对于YARN而言，Spark Application仅仅是一个客户端而已（两种流程）</li></ul></div>
<blockquote style="border:none;">
<div>
<div>
<ol><li>Spark on YARN之client模式</li></ol><div><img src="https://img-blog.csdn.net/20160311224304760" alt=""><br></div>
</div>
</div>
</blockquote>
<blockquote style="border:none;">
<blockquote style="border:none;">
<div>
<div>Client<br><span></span>Driver运行在Client端(提交Spark作业的机器)<br><span></span>Client会和请求到的Container进行通信来完成作业的调度和执行，Client是不能退出的<br><span></span>日志信息会在控制台输出：便于我们测试</div>
</div>
<div>2.Spark on YARN之cluster模式</div>
<div><img src="https://img-blog.csdn.net/20160311224211425" alt=""><br></div>
<div>Cluster<br><span></span>Driver运行在ApplicationMaster中<br><span></span>Client只要提交完作业之后就可以关掉，因为作业已经在YARN上运行了<br><span></span>日志是在终端看不到的，因为日志是在Driver上，只能通过yarn logs -applicationIdapplication_id<br></div>
</blockquote>
<p>Spark On YARN的使用</p>
<p></p>
<ul><li>在/conf/spak-env.sh中设置HADOOP_CONF_DIR=/home/hadoop/app/hadoop-2.6.0-cdh5.7.0/etc/hadoop</li><li>spark-shell运行在YARN上测试 ./bin/spark-shell --master yarn</li><li>spark-submit运行在YARN上测试</li></ul><div><pre><code class="language-plain">./bin/spark-submit \
--class org.apache.spark.examples.SparkPi \
--master yarn \
--executor-memory 1G \
--num-executors 1 \
/home/hadoop/app/spark-2.1.0-bin-2.6.0-cdh5.7.0/examples/jars/spark-examples_2.11-2.1.0.jar \
4</code></pre><br><br></div>
<p></p>
</blockquote>
            </div>
                </div>