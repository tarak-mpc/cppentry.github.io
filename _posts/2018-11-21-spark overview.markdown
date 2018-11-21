---
layout:     post
title:      spark overview
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:20px;">
<strong>转自：http://jerryshao.me/architecture/2013/03/29/spark-overview/</strong></p>
<p style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:20px;">
<strong>Overview</strong></p>
<p style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:20px;">
本文章主要对Spark,Spark的基本架构和重要模块作基本介绍，文章不会涉及Spark的安装部署以及使用，对此可以参考<a href="http://spark-project.org/documentation/" rel="nofollow" style="color:rgb(0,136,204);text-decoration:none;">Spark官方文档</a>。</p>
<h1 id="what_is_spark" style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:40px;color:rgb(51,51,51);font-size:38.5px;">
What is Spark</h1>
<p style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:20px;">
<a href="http://spark-project.org/" rel="nofollow" style="color:rgb(0,136,204);text-decoration:none;"><strong>Spark</strong></a>是UC Berkeley AMP lab所开源的类Hadoop MapReduce 框架，都是基于map reduce算法所实现的分布式计算框架，拥有Hadoop MapReduce所具有的 优点；不同于MapReduce的是Job中间输出和结果可以保存在内存中，而不需要读写HDFS，因
 此Spark能更好地适用于machine learning等需要迭代的map reduce算法。</p>
<blockquote style="border-left-width:5px;border-left-style:solid;border-left-color:rgb(238,238,238);color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:20px;">
<p style="font-size:16px;line-height:25px;">Spark is an open source cluster computing system that aims to make data analytics fast — both fast to run and fast to write.</p>
<p style="font-size:16px;line-height:25px;">To run programs faster, Spark provides primitives for in-memory cluster computing: your job can load data into memory and query it repeatedly much quicker than with disk-based systems
 like Hadoop MapReduce.</p>
<p style="font-size:16px;line-height:25px;">To make programming faster, Spark provides clean, concise APIs in Scala, Java and Python. You can also use Spark interactively from the Scala and Python shells to rapidly query big
 datasets.</p>
</blockquote>
<h1 id="spark_architecture" style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:40px;color:rgb(51,51,51);font-size:38.5px;">
Spark Architecture</h1>
<p style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:20px;">
援引<a href="http://weibo.com/jerrylead" rel="nofollow" style="color:rgb(0,136,204);text-decoration:none;">@JerryLead</a>的系统架构图作为Spark整体结构的一个 birdview：</p>
<p style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:20px;">
<img alt="Spark birdview" src="http://jerryshao.me/img/2013-03-29-spark-overview/architecture-birdview.jpg" width="640" style="vertical-align:middle;border:0px;"></p>
<p style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:20px;">
整体上Spark分为以下几个主要的子模块:</p>
<ul style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:20px;"><li><code style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;font-size:12px;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">deploy</code>: <code style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;font-size:12px;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">deply</code>模块包括<code style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;font-size:12px;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">Master</code>，<code style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;font-size:12px;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">Work</code>和<code style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;font-size:12px;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">Client</code>，参见architecture图的最上
 部分。<code style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;font-size:12px;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">deploy</code>主要负责启动和调度用户实现的Spark application并且分配资源给用户
 application，类似于Hadoop YARN框架。</li><li><code style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;font-size:12px;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">scheduler</code>: <code style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;font-size:12px;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">scheduler</code>主要负责调度用户application内的tasks，根据部署方式的不
 同Spark实现了多种不同的<code style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;font-size:12px;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">scheduler</code>，包括<code style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;font-size:12px;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">LocalScheduler</code>，<code style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;font-size:12px;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">ClusterScheduler</code>等
 。</li><li><code style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;font-size:12px;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">rdd</code>: <code style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;font-size:12px;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">rdd</code>类似于一个分布式的数据集，用户可以根据<code style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;font-size:12px;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">rdd</code>所提供的api进行数据集的
 操作，<code style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;font-size:12px;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">rdd</code>模块是用户交互的主要模块。</li><li><code style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;font-size:12px;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">storage</code>: <code style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;font-size:12px;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">storage</code>模块主要负责数据集，也就是<code style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;font-size:12px;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">rdd</code>的存取。根据设定的不同，数
 据可以保存在内存、磁盘或是两者。Spark与Hadoop MapReduce最大的不同在于MapReduce 将数据保存在HDFS上，而Spark则由自己的存储系统。</li></ul><p style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:20px;">
当然还有一些其他的子模块，可以参考上图。</p>
<p style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:20px;">
Spark采用了Actor的设计方式，整体架构，包括各子模块的设计上都是采用master-slave模 式，master和slave之间通信的主要协议可以参见上图。</p>
<h1 id="end" style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:40px;color:rgb(51,51,51);font-size:38.5px;">
End</h1>
<p style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:20px;">
以上大致地介绍了Spark的architecture，之后会陆续对各子模块作详细的介绍</p>
            </div>
                </div>