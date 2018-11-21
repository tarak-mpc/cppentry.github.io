---
layout:     post
title:      Spark的Cluster模式架构图
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：					https://blog.csdn.net/weixin_41076809/article/details/79396529				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>一、在Spark的官网文件中，可以看到Spark的Cluster模式架构图，</p><p>浏览器输入:https://spark.apache.org/docs/latest/cluster-overview.html</p><p><img src="https://img-blog.csdn.net/20180228100257467?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2VpeGluXzQxMDc2ODA5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""></p><p>（1）DriverProgram 就是程序员设计的Spark程序，在Spark中必须定义SparkContext，它是开发Spark应用程序的入口</p><p>（2）SparkContext是通过Cluster Manager管理整个集群，集群中包含多个Worker Node，程序运行时，一个worker启动Driver，其他Worker Node启动Executor负责执行任务</p><p>（3）executor是真正执行作业的地方，一个集群包含多个Executor，每个Executor接受Driver的命令执行task，一个Executor可以执行一到多个Task<br></p>            </div>
                </div>