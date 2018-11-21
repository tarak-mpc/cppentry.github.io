---
layout:     post
title:      Spark的architectue
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载，该博客仅用于个人学习，欢迎大家互相交流讨论					https://blog.csdn.net/sbq63683210/article/details/51568220				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="spark的architectue"><strong>Spark的architectue</strong></h2>

<p>本文章主要对Spark的基本架构和重要模块作基本介绍，不会涉及Spark的安装部署以及使用</p>

<p>在分析深入源码之前，再从宏观上分析一下spark的主要架构。</p>

<p><strong>What is Spark</strong> <br>
Spark是UC Berkeley AMP lab所开源的类Hadoop MapReduce 框架，都是基于map reduce算法所实现的<strong>分布式计算框架</strong>，拥有Hadoop MapReduce所具有的 优点；不同于MapReduce的是：<strong>Job中间输出和结果可以保存在内存中</strong>，而不需要读写HDFS，因 此Spark能更好地适用于machine learning等需要迭代的map reduce算法。</p>

<p><strong>Spark Architecture</strong> <br>
图片不是太清楚 <br>
<img src="https://img-blog.csdn.net/20160602162536811" alt="这里写图片描述" title=""></p>

<p><strong>整体上spark分为一下几个子模块</strong></p>

<p>1.deploy(5600行源码）: <br>
deply模块包括Master，Work和Client，参见architecture图的最上 部分。deploy主要负责<strong>启动和调度</strong>用户实现的Spark application并且<strong>分配资源</strong>给用户 application，类似于Hadoop YARN框架。</p>

<p>2.scheduler（6200行源码）:  <br>
scheduler主要负责<strong>调度</strong>用户application内的tasks，根据部署方式的不同，Spark实现了多种不同的scheduler，包括LocalScheduler，ClusterScheduler等 。</p>

<p>3.RDD:  <br>
RDD类似于一个分布式的<strong>数据集</strong>，用户可以根据RDD所提供的api进行数据集的操作，RDD模块是用户交互的主要模块。</p>

<p>4.storage（4700行源码）:  <br>
storage模块主要负责数据集，也就是rdd的存取。根据设定的不同，数据可以保存在内存、磁盘或是两者。Spark与Hadoop MapReduce最大的不同在于<strong>MapReduce 将数据保存在HDFS上，而Spark则由自己的存储系统</strong>。</p>

<hr>

<p>当然还有一些其他的子模块，可以参考上图。</p>

<p>Spark采用了Actor的设计方式，整体架构，包括各子模块的设计上都是采用master-slave模 式，master和slave之间通信的主要协议可以参见上图。</p>

<hr>

<p>以上大致地总结了Spark的architecture，之后会陆续对各子模块作详细的学习。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>