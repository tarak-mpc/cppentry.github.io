---
layout:     post
title:      Spark Core and Cluster Managers（翻译自Learning.Spark.Lightning-Fast.Big.Data.Analysis）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><strong><span style="font-size:24px;">Spark Core</span></strong></p>
<p><span style="font-size:24px;">Spark Core 包含了Spark提供的基本功能，包括任务调度，内存管理，容错，与存储系统交互等。<span style="font-size:24px;">Spark Core</span>也是定义RDD的API的地方，RDD（resilient distributed dataset）是Spark的主要编程抽象。RDD是一个元素的集合，而集合中的元素分布在许多计算节点中，这些元素可以被并行的操作。Spark Core提供了很多API来创建和操作这些集合。</span></p>
<p><br></p>
<p><strong><span style="font-size:24px;">Cluster Managers</span></strong></p>
<p><span style="font-size:24px;">在底层，Spark被设计成能运行在一个节点上，或者数千个节点组成的集群上。在最大化弹性的情况下实现这个目的，Spark能够运行在不同的集群管理器上，包括Hadoop YARN，Apache Mesos和Spark自带的集群管理器——Standalone Scheduler。如果你的Spark安装在一组没有其它集群管理器的机器上，<span style="font-size:24px;">Standalone Scheduler</span>就能提供简单的集群管理功能；如果你已经有了<span style="font-size:24px;">Hadoop YARN或者Mesos集群，Spark允许你的应用运行在这些集群上。第七章深入学习不同的选项和怎样去选择正确的集群管理器。</span><br></span></p>
            </div>
                </div>